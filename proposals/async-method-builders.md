---
ms.openlocfilehash: c1ff78f64b1529e6b648d5cbe5b80e507642fac8
ms.sourcegitcommit: ec31c2771e390305a430c407e29f72d8299a4c72
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99985908"
---
# <a name="asyncmethodbuilder-override"></a>Override di AsyncMethodBuilder

* [x] proposto
* [] Prototipo: non avviato
* [] Implementazione: non avviata
* [] Specifica: non avviata

## <a name="summary"></a>Riepilogo
[summary]: #summary

Consente l'override per metodo del generatore di metodi asincroni da usare.
Per alcuni metodi asincroni si vuole personalizzare la chiamata di `Builder.Create()` per usare un _tipo di generatore_ diverso e possibilmente passare informazioni aggiuntive sullo stato.

```C#
[AsyncMethodBuilderOverride(typeof(PoolingAsyncValueTaskMethodBuilder<int>))] // new, referring to some custom builder type
static async ValueTask<int> ExampleAsync() { ... }
```

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Attualmente, i generatori di metodi asincroni sono collegati a un tipo specificato usato come tipo restituito di un metodo asincrono.  Ad esempio, qualsiasi metodo dichiarato come `async Task` utilizza `AsyncTaskMethodBuilder` e qualsiasi metodo dichiarato come `async ValueTask<T>` utilizza `AsyncValueTaskMethodBuilder<T>` .  Questo è dovuto all' `[AsyncMethodBuilder(Type)]` attributo del tipo utilizzato come tipo restituito, ad esempio, `ValueTask<T>` è attribuito come `[AsyncMethodBuilder(typeof(AsyncValueTaskMethodBuilder<>))]` . Questo risolve la maggior parte dei casi comuni, ma lascia alcuni buchi rilevanti per gli scenari avanzati.

In .NET 5 è stata fornita una funzionalità sperimentale che fornisce due modalità in cui `AsyncValueTaskMethodBuilder` e `AsyncValueTaskMethodBuilder<T>` operano.  La modalità attiva per impostazione predefinita è identica a quella in cui è stata introdotta la funzionalità: quando la macchina a stati deve essere sollevata nell'heap, viene allocato un oggetto per archiviare lo stato e il metodo asincrono restituisce un oggetto `ValueTask{<T>}` supportato da `Task{<T>}` .  Tuttavia, se viene impostata una variabile di ambiente, tutti i generatori del processo passano a una modalità in cui, invece, le `ValueTask{<T>}` istanze sono supportate dalle implementazioni riutilizzabili in `IValueTaskSource{<T>}` pool.  Ogni metodo asincrono dispone di un proprio pool con un numero massimo fisso di istanze consentite per il pool e, a condizione che non venga mai restituito un numero maggiore di tale numero al pool, in modo che venga ripreso in pool contemporaneamente, i `async ValueTask<{T}>` metodi diventano senza alcun sovraccarico di allocazione GC.

Esistono diversi problemi con questa modalità sperimentale, tuttavia, che è il motivo per cui è disattivata per impostazione predefinita e b) è probabile che venga rimossa in una versione futura, a meno che non siano presenti nuove informazioni molto interessanti ( https://github.com/dotnet/runtime/issues/13633) .
- Introduce una differenza comportamentale per i consumer dell'oggetto restituito `ValueTask{<T>}` se `ValueTask` non viene utilizzato in base alla specifica.  Quando è supportato da `Task` , è possibile eseguire le `ValueTask` operazioni con un `Task` , ad esempio await it più volte, await it simultaneamente, blocca in attesa del completamento e così via.  Tuttavia, quando è supportato da un arbitrario `IValueTaskSource` , queste operazioni non sono consentite e il cambio automatico dal primo al secondo può causare bug.  Con l'opzione a livello di processo e che interessano tutti i `async ValueTask` metodi del processo, indipendentemente dal fatto che vengano controllate o meno, il martello è troppo grande.
- Non è necessariamente una vittoria sulle prestazioni e può rappresentare una regressione in alcune situazioni.  L'implementazione sta commerciando il costo del pool (l'accesso a un pool non è gratuito) con il costo di GC e in varie situazioni il GC può vincere.  Anche in questo caso, l'applicazione del pool a tutti i `async ValueTask` metodi del processo, anziché essere selettiva rispetto a quelli che trarrebbero maggiore vantaggio, è troppo grande.
- Aggiunge alla dimensione IL di un'applicazione tagliata, anche se il flag non è impostato, quindi alla dimensione ASM risultante.  È possibile che sia possibile aggirare i miglioramenti apportati all'implementazione per indicare che per una distribuzione specifica la variabile di ambiente sarà sempre falsa, ma, come attualmente, ogni `async ValueTask` metodo ha visto, ad esempio, un aumento del footprint binario di ~ 2K nelle immagini AOT a causa di questa opzione, e, ancora una volta, che si applica a tutti i `async ValueTask` metodi nell'intera chiusura dell'applicazione.
- Metodi diversi possono trarre vantaggio da diversi livelli di controllo, ad esempio le dimensioni del pool utilizzate a causa della conoscenza del metodo e della relativa modalità di utilizzo, ma la stessa impostazione viene applicata a tutti gli utilizzi del generatore.  Si può immaginare di aggirare il problema, in modo che il codice del generatore usi la reflection in fase di esecuzione per cercare un attributo, ma che aggiunga spese di run-time significative e probabilmente nel percorso di avvio.

Oltre a tutti questi problemi con il pool esistente, è anche possibile impedire agli sviluppatori di scrivere i propri generatori personalizzati per i tipi di cui non sono proprietari.  Se, ad esempio, uno sviluppatore desidera implementare il proprio supporto per la creazione di pool, deve presentare anche un nuovo tipo simile a un'attività, anziché semplicemente poter utilizzare `{Value}Task{<T>}` , perché l'attributo che specifica il generatore è solo specificabile nella dichiarazione di tipo del tipo restituito.

È necessario un modo per consentire a un singolo metodo asincrono di acconsentire esplicitamente a un generatore specifico.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

#### <a name="p0-asyncmethodbuilderoverrideattribute-applied-on-async-methods"></a>P0: AsyncMethodBuilderOverrideAttribute applicato ai metodi asincroni

In `dotnet/runtime` aggiungere `AsyncMethodBuilderOverrideAttribute` :
```csharp
namespace System.Runtime.CompilerServices
{
    /// <summary>
    /// Indicates the type of the async method builder that should be used by a language compiler to
    /// build the attributed method.
    /// </summary>
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Struct | AttributeTargets.Interface | AttributeTargets.Method | AttributeTargets.Module, Inherited = false, AllowMultiple = false)]
    public sealed class AsyncMethodBuilderOverrideAttribute : Attribute
    {
        /// <summary>Initializes the <see cref="AsyncMethodBuilderOverrideAttribute"/>.</summary>
        /// <param name="builderType">The <see cref="Type"/> of the associated builder.</param>
        public AsyncMethodBuilderOverrideAttribute(Type builderType) => BuilderType = builderType;

        // for scoped application (use property for targetReturnType? have compiler check that it's provided)
        public AsyncMethodBuilderOverrideAttribute(Type builderType, Type targetReturnType) => ...

        /// <summary>Gets the <see cref="Type"/> of the associated builder.</summary>
        public Type BuilderType { get; }
    }
}
```

Nel compilatore C# preferiscono l'attributo nel metodo quando si determina il generatore da usare rispetto a quello definito nel tipo.  Ad esempio, oggi se un metodo viene definito come:
```C#
public async ValueTask<T> ExampleAsync() { ... }
```
il compilatore genererà codice analogo al seguente:
```C#
[AsyncStateMachine(typeof(<ExampleAsync>d__29))]
[CompilerGenerated]
static ValueTask<int> ExampleAsync()
{
    <ExampleAsync>d__29 stateMachine;
    stateMachine.<>t__builder = AsyncValueTaskMethodBuilder<int>.Create();
    stateMachine.<>1__state = -1;
    stateMachine.<>t__builder.Start(ref stateMachine);
    return stateMachine.<>t__builder.Task;
}
```
Con questa modifica, se lo sviluppatore ha scritto:
```C#
[AsyncMethodBuilderOverride(typeof(PoolingAsyncValueTaskMethodBuilder<int>))] // new, referring to some custom builder type
static async ValueTask<int> ExampleAsync() { ... }
```
verrebbe invece compilato in:
```C#
[AsyncStateMachine(typeof(<ExampleAsync>d__29))]
[CompilerGenerated]
[AsyncMethodBuilder(typeof(PoolingAsyncValueTaskMethodBuilder<int>))] // retained but not necessary anymore
static ValueTask<int> ExampleAsync()
{
    <ExampleAsync>d__29 stateMachine;
    stateMachine.<>t__builder = PoolingAsyncValueTaskMethodBuilder<int>.Create(); // <>t__builder now a different type
    stateMachine.<>1__state = -1;
    stateMachine.<>t__builder.Start(ref stateMachine);
    return stateMachine.<>t__builder.Task;
}
```

Solo le piccole aggiunte consentono:
- Chiunque scriva il proprio generatore che può essere applicato a metodi asincroni che restituiscono `Task<T>` e `ValueTask<T>`
- Come "chiunque", il runtime per fornire il supporto del generatore sperimentale come nuovi tipi di generatore pubblici che possono essere scelti in base al metodo; il supporto esistente verrebbe rimosso dai generatori esistenti.  I metodi, inclusi quelli che si occupano delle librerie principali, possono quindi essere attribuiti a caso per caso per usare il supporto del pool, senza alcun effetto su altri metodi non attribuiti.

e con modifiche minime alla superficie di attacco o funzioni nel compilatore.


Si noti che è necessario il codice generato per consentire la restituzione di un tipo diverso dal `Create` Metodo:
```
AsyncPooledBuilder _builder = AsyncPooledBuilderWithSize4.Create();
```

#### <a name="p1-passing-state-to-the-builder-instantiation"></a>P1: passaggio dello stato alla creazione dell'istanza del generatore

In alcuni scenari può essere utile passare alcune informazioni al generatore.  Questo può essere statico (ad esempio, le costanti che configurano la dimensione del pool da usare) o dinamico (ad esempio, riferimento alla cache o singleton da usare).

Sono state rilevate alcune opzioni (documentate di seguito per il record), ma si consiglia di non eseguire alcuna operazione (opzione #0) finché non si determina che il supporto dello stato dinamico potrebbe essere utile.

##### <a name="option-0-no-extra-state"></a>Opzione 0: nessun stato aggiuntivo

È possibile approssimare il passaggio di informazioni statiche eseguendo il wrapping dei tipi di generatore.
Ad esempio, è possibile creare un tipo di generatore personalizzato che hardcoded una determinata configurazione:

```C#
public struct AsyncPooledBuilderWithSize4
{
    AsyncPooledBuilder Create() => AsyncPooledBuilder.Create(4);
}
```

##### <a name="option-1-use-constants-in-attribute-as-arguments-for-create"></a>Opzione 1: usare le costanti nell'attributo come argomenti per `Create`

`AsyncMethodBuilderOverrideAttribute`Avrebbe accettato alcune informazioni aggiuntive:
```C#
    public AsyncMethodBuilderOverrideAttribute(Type builderType, params object[] args)
```

Argomenti raccolti nell'attributo:
```C#
[AsyncMethodBuilderOverride(typeof(AsyncPooledBuilder), 4)]
```
viene usato nella chiamata del `Create` Metodo:
```C#
AsyncPooledBuilder.Create(4);
```

##### <a name="option-2-use-arguments-of-the-async-method"></a>Opzione 2: usare gli argomenti del metodo asincrono

Oltre a `AsyncMethodBuilderOverrideAttribute` un attributo per contrassegnare uno dei parametri del metodo asincrono:
```C#
[AsyncMethodBuilderOverride(typeof(AsyncPooledBuilder))]
async ValueTask MyMethodAsync(/* regular arguments */, [FOR_BUILDER] int i)
```

Questo comporterebbe il passaggio del valore per il parametro alla `Create` chiamata:
```C#
BuilderType.Create(i);
```

Nella maggior parte dei casi, l'utente potrebbe scrivere un wrapper per ottenere la firma desiderata:
```C#
public ValueTask MyMethodWrapperAsync(/* regular parameters */)
{
    return MyMethodAsync(/* pass values from regular parameters through */, 4); // static or dynamic value for the builder
}
```

Uno può anche passare i delegati memorizzati nella cache in questo modo:
```C#
.ctor()
{
    s_func_take = () => get_item();
    s_action_put = t => free_item(t);
}

public ValueTask MyMethodWrapperAsync(/* regular parameters */)
{
   return MyMethodAsync(/* pass values from regular parameters through */, (s_func_take, s_action_put));
}
```

Questo approccio è simile al `EnumeratorCancellationAttribute` funzionamento di. Il parametro aggiuntivo, tuttavia, non è utile per il codice utente, quindi la firma e la macchina a Stati sono inquinate.
Questo approccio si sovrappone con l'opzione #1, quindi è probabile che non si desideri supportare entrambi.

##### <a name="option-3-pass-a-lambda-that-instantiates-builders"></a>Opzione 3: passare un'espressione lambda che crea un'istanza di generatori

Come sostituzione per `AsyncMethodBuilderOverrideAttribute` (o possibilmente in aggiunta), avremmo un attributo per contrassegnare uno dei parametri del metodo asincrono:
```C#
async ValueTask MyMethodAsync(/* regular parameters */, [FOR_BUILDER] Func<AsyncPooledBuilder> lambda)
```
Il parametro deve avere un tipo delegato senza parametri e restituire un tipo di generatore.

Il compilatore potrebbe generare:
```C#
...
static ValueTask<int> MyMethodAsync(/* regular parameters */, [FOR_BUILDER] Func<AsyncPooledBuilder> lambda)
{
    <MyMethodAsync>d__29 stateMachine;
    stateMachine.<>t__builder = lambda();
    ...
}
```

È ancora presente il problema dell'inquinamento della firma del metodo e della macchina a Stati.

Nel caso in cui si desideri un generatore contenente due delegati memorizzati nella cache

```C#
.ctor()
{
    s_func_take = () => get_item();
    s_action_put = t => free_item(t);
    s_func = () => Builder.Create(take: s_func_take, put: s_action_put);
}

public ValueTask MyMethodWrapperAsync(...)
{
    return MyMethodAsync(..., s_func);
}
```

#### <a name="p2-enable-at-the-module-and-type-level-as-well"></a>P2: abilitare anche a livello di modulo (e digitare?)

Uno sviluppatore che desidera utilizzare un generatore personalizzato specifico per tutti i relativi metodi può eseguire questa operazione inserendo l'attributo pertinente per ogni metodo.  Tuttavia, è anche possibile consentire l'attribuzione a livello di modulo o di tipo, nel qual caso ogni metodo pertinente all'interno di tale ambito si comporterebbe come se fosse annotato direttamente, ad esempio
```C#
[module: AsyncMethodBuilderOverride(typeof(PoolingAsyncValueTaskMethodBuilder), typeof(ValueTask)]
[module: AsyncMethodBuilderOverride(typeof(PoolingAsyncValueTaskMethodBuilder<>), typeof(ValueTask<>)]

class MyClass
{
    public async ValueTask Method1Async() { ... } // would use PoolingAsyncValueTaskMethodBuilder
    public async ValueTask<int> Method2Async() { ... } // would use PoolingAsyncValueTaskMethodBuilder<int>
    public async ValueTask<string> Method3Async() { ... } // would use PoolingAsyncValueTaskMethodBuilder<string>
}
```

A tale proposito, aggiungere i membri seguenti all'attributo:
```C#
namespace System.Runtime.CompilerServices
{
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Struct | AttributeTargets.Interface | AttributeTargets.Method | AttributeTargets.Module, Inherited = false, AllowMultiple = false)]
    public sealed class AsyncMethodBuilderOverrideAttribute : Attribute
    {
        ...
        // for scoped application (use property for targetReturnType? have compiler check that it's provided)
        public AsyncMethodBuilderOverrideAttribute(Type builderType, Type targetReturnType) => ...

        public Type TargetReturnType { get; }
    }
}
```

Questa operazione non solo renderebbe più semplice l'inserimento dell'attributo su ogni metodo, ma renderebbe più semplice l'utilizzo di compilazioni diverse che usavano diversi generatori.  Una compilazione ottimizzata per la velocità effettiva potrebbe includere, ad esempio, un file con estensione cs che specifica un generatore di pool in un attributo a livello di modulo, mentre una compilazione ottimizzata per le dimensioni potrebbe includere un file con estensione cs che specifica un generatore minimalista che sceglie di usare più allocazione/conversione boxing anziché numerose ottimizzazioni della velocità effettiva e della specializzazione del codice.


## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

* La sintassi per l'applicazione di tale attributo a un metodo è Verbose.  L'effetto di questa operazione viene ridotto se uno sviluppatore può applicarlo a più metodi, ad esempio a livello di tipo o di modulo.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

- Implementare un tipo diverso per l'attività e esporre la differenza ai consumer.  `ValueTask` è stata resa estendibile tramite l' `IValueTaskSource` interfaccia per evitare tale necessità.
- Risolvere solo la parte del problema relativa al pool di ValueTask, abilitando l'esperimento come implementazione on-by-default-only.  Questo non risolve altri aspetti, ad esempio la configurazione del pool, o l'abilitazione di un altro utente a fornire il proprio generatore.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

1. **Attributo.** È necessario riutilizzare `[AsyncMethodBuilder(typeof(...))]` o introdurre ancora un altro attributo?
2. **Sostituire o creare anche.** Tutti gli esempi di questa proposta sono relativi alla sostituzione di un generatore di tipo attività compilabile.  L'ambito della funzionalità dovrebbe essere sufficiente? In alternativa, è possibile usare questo attributo in un metodo con un tipo restituito che non dispone già di un generatore (ad esempio, un'interfaccia comune)?  Che può influisca sulla risoluzione dell'overload.
3. **Virtuals/Interfaces.** Qual è il comportamento se l'attributo viene specificato in un metodo di interfaccia?  Credo che debba essere un NOP o un avviso o un errore del compilatore, ma non dovrebbe influisca sulle implementazioni dell'interfaccia.  Esiste una domanda simile per i metodi di base sottoposti a override e di nuovo non credo che l'attributo nel metodo di base debba influisca sul comportamento di un'implementazione di override. Si noti che l'attributo corrente ha ereditato = false sul relativo AttributeUsage.
4. **Precedenza.** Se si vuole eseguire l'annotazione a livello di modulo o di tipo, è necessario decidere quale sia l'attribuzione vincente nel caso in cui siano state applicate più, ad esempio una sul metodo, una sul modulo contenitore.  È anche necessario determinare se questa operazione richiederebbe l'uso di un attributo diverso (vedere (1) sopra), ad esempio quale sarebbe il comportamento se un tipo simile a un'attività era nello stesso ambito?  In alternativa, se un'attività compilabile, ad esempio, aveva metodi asincroni su di essa, verrebbe influenzato dall'attributo applicato al tipo simile a un'attività per specificare il generatore predefinito?
5. **Generatori privati**. Il compilatore deve supportare i generatori di metodi asincroni non pubblici? Questa non è una specifica di oggi, ma sperimentale sono supportate solo quelle pubbliche.  Questo ha senso quando l'attributo viene applicato a un tipo per controllare quale generatore viene usato con quel tipo, perché chiunque scriva un metodo asincrono con quel tipo come tipo restituito dovrebbe avere accesso al generatore.  Tuttavia, con questa nuova funzionalità, quando l'attributo viene applicato a un metodo, influisca solo sull'implementazione di tale metodo e pertanto può fare ragionevolmente riferimento a un generatore non pubblico.  È probabile che si desideri supportare gli autori di librerie con quelli non pubblici che vogliono usare.
6. **Stato passthrough per consentire un pool più efficiente**.  Si consideri un tipo come SslStream o WebSocket.  Che espongono operazioni asincrone di lettura/scrittura e consentono la lettura e la scrittura simultanee, ma al massimo un'operazione di lettura alla volta e al massimo 1 operazione di scrittura alla volta.  Questa soluzione è ideale per il pool, perché ogni istanza di SslStream o WebSocket richiede al massimo un oggetto in pool per le letture e un oggetto in pool per le Scritture.  Inoltre, un pool centralizzato è eccessivo: piuttosto che pagare i costi di un pool centrale che deve essere gestito e di accesso sincronizzato, ogni SslStream/WebSocket può semplicemente gestire un campo per archiviare il singleton per il lettore e un singleton per il writer, eliminando tutti i conflitti per il pool ed eliminando tutte le operazioni di gestione associate ai limiti del pool.  Il problema è la modalità di connessione di un campo arbitrario nell'istanza di con il meccanismo di pooling utilizzato dal generatore.  Potremmo almeno renderlo possibile se avessimo passato tutti gli argomenti al metodo asincrono in una firma corrispondente nel metodo create del generatore (o forse un metodo Bind separato, o qualche cosa), ma il generatore doveva essere specializzato per quel tipo specifico, conoscendo i campi.  Il metodo Create potrebbe potenzialmente accettare un delegato Rent e un delegato return e il metodo asincrono potrebbe essere appositamente creato per accettare tali argomenti (insieme a uno stato dell'oggetto da passare).  La soluzione ideale è quella di creare un'ottima soluzione, in quanto renderebbe il meccanismo significativamente più potente e prezioso.

## <a name="design-meetings"></a>Riunioni di progettazione

Collegamento alle note sulla progettazione che interessano questa proposta e descrivono in una frase per ogni modifica apportata.
