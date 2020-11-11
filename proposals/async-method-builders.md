---
ms.openlocfilehash: 26e9407830d125c4c68efc162943fcc4f960301e
ms.sourcegitcommit: 1fccd6246b9806faee3c8265036fb936ee4a0337
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507677"
---
# <a name="per-method-asyncmethodbuilders"></a>Per-Method AsyncMethodBuilders

* [x] proposto
* [] Prototipo: non avviato
* [] Implementazione: non avviata
* [] Specifica: non avviata

## <a name="summary"></a>Riepilogo
[summary]: #summary

Estendere il generatore di metodi asincroni esistente per supportare l'attribuzione per metodo oltre al supporto per il tipo per restituito esistente.

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

#### <a name="p0-asyncmethodbuilderattribute-applicable-to-methods"></a>P0: AsyncMethodBuilderAttribute applicabile ai metodi

In DotNet/Runtime modificare `AsyncMethodBuilderAttribute` AttributeUsage da:
```C#
AttributeTargets.Class | AttributeTargets.Struct | AttributeTargets.Interface | AttributeTargets.Delegate | AttributeTargets.Enum
```
per includere anche il metodo:
```C#
AttributeTargets.Class | AttributeTargets.Struct | AttributeTargets.Interface | AttributeTargets.Delegate | AttributeTargets.Enum | AttributeTargets.Method
```
in modo che possa essere applicato anche ai metodi.  In alternativa, introdurre un nuovo attributo specifico dei metodi, se questo è ritenuto migliore per qualche motivo.

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
[AsyncMethodBuilder(typeof(PoolingAsyncValueTaskMethodBuilder<int>))] // new, referring to some custom builder type
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
- Chiunque scriva il proprio generatore che può essere applicato a metodi asincroni che restituiscono `Task{T}` e `ValueTask{<T>}`
- Come "chiunque", il runtime per fornire il supporto del generatore sperimentale come nuovi tipi di generatore pubblici che possono essere scelti in base al metodo; il supporto esistente verrebbe rimosso dai generatori esistenti.  I metodi, inclusi quelli che si occupano delle librerie principali, possono quindi essere attribuiti a caso per caso per usare il supporto del pool, senza alcun effetto su altri metodi non attribuiti.

e con modifiche minime alla superficie di attacco o funzioni nel compilatore.

#### <a name="p1-asyncmethodbuilderattribute-arguments-forward-to-create"></a>P1: AsyncMethodBuilderAttribute argomenti in futuro da creare

All'attributo viene assegnato un costruttore aggiuntivo:
```C#
public AsyncMethodBuilderAttribute(Type builderType, params object[] createArguments);
```
Se vengono specificati argomenti di questo tipo, il compilatore si aspetta che il generatore disponga di un `Create` metodo che può essere associato a tali argomenti, ad esempio se uno sviluppatore USA:
```C#
[AsyncMethodBuilder(typeof(PoolingAsyncValueTaskMethodBuilder<>), 16)]
```
il compilatore consentirebbe la compilazione a causa `PoolingAsyncValueTaskMethodBuilder<>` dell'esposizione dell' `Create` Overload seguente:
```
public static PoolingAsyncValueTaskMethodBuilder<T> Create(int poolCapacity);
```
e utilizzerebbe tale `Create` Overload invece di un senza parametri `Create` che altrimenti sarebbe previsto e utilizzerebbe, ad esempio:
```C#
[AsyncMethodBuilder(typeof(PoolingAsyncValueTaskMethodBuilder<>), 16)]
static async ValueTask<int> ExampleAsync() { ... }
```
verrà compilato in:
```C#
[AsyncStateMachine(typeof(<ExampleAsync>d__29))]
[CompilerGenerated]
[AsyncMethodBuilder(typeof(PoolingAsyncValueTaskMethodBuilder<>), 16)]
static ValueTask<int> ExampleAsync()
{
    <ExampleAsync>d__29 stateMachine;
    stateMachine.<>t__builder = PoolingAsyncValueTaskMethodBuilder<int>.Create(16); // attr arguments passed to Create
    stateMachine.<>1__state = -1;
    stateMachine.<>t__builder.Start(ref stateMachine);
    return stateMachine.<>t__builder.Task;
}
```
Tale supporto potrebbe consentire ai generatori personalizzati di essere parametrizzati per ogni sito di chiamata, senza richiedere al generatore di eseguire una reflection complessa e costosa.

#### <a name="p2-enable-at-the-module-and-type-level-as-well"></a>P2: abilitare anche a livello di modulo (e digitare?)

Uno sviluppatore che desidera utilizzare un generatore personalizzato specifico per tutti i relativi metodi può eseguire questa operazione inserendo l'attributo pertinente per ogni metodo.  Tuttavia, è anche possibile consentire l'attribuzione a livello di modulo o di tipo, nel qual caso ogni metodo pertinente all'interno di tale ambito si comporterebbe come se fosse annotato direttamente, ad esempio
```C#
[module: PoolingAsyncValueTaskMethodBuilder]
[module: PoolingAsyncValueTaskMethodBuilder<>]

class MyClass
{
    public async ValueTask Method1Async() { ... } // would use PoolingAsyncValueTaskMethodBuilder
    public async ValueTask<int> Method2Async() { ... } // would use PoolingAsyncValueTaskMethodBuilder<int>
    public async ValueTask<string> Method3Async() { ... } // would use PoolingAsyncValueTaskMethodBuilder<string>
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
