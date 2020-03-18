---
ms.openlocfilehash: 1457c1eb018e12af30ce5b38be704bf8851d4b25
ms.sourcegitcommit: 7f7fc6e9e195e51b7ff8229aeaa70aa9fbbb63cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "79485014"
---
# <a name="efficient-params-and-string-formatting"></a>Parametri efficienti e formattazione delle stringhe

## <a name="summary"></a>Summary
Questa combinazione di funzionalità aumenterà l'efficienza della formattazione `string` valori e il passaggio di argomenti di stile `params`.

## <a name="motivation"></a>Motivazione
L'overhead di allocazione per la formattazione dei valori `string` può compromettere le prestazioni di molte applicazioni basate su testo: dalla pena di boxe dei tipi di `struct`, dall'allocazione `object[]` per `params` e dalle allocazioni `string` intermedie durante le chiamate `string.Format`. Per garantire l'efficienza di tali applicazioni, spesso è necessario abbandonare le funzionalità di produttività, ad esempio `params` e `string` l'interpolazione e passare a soluzioni codificate a mano non standard. 

Prendere in considerazione MSBuild come esempio. Questa operazione viene scritta usando numerose funzionalità moderne C# da parte degli sviluppatori che sono consapevoli delle prestazioni. Tuttavia, in un esempio di compilazione rappresentativo MSBuild genererà 262MB di allocazione `string` usando un livello di dettaglio minimo. Di tale 1/2 delle allocazioni sono allocazioni di breve durata all'interno `string.Format`. Queste funzionalità consentono di rimuovere gran parte di queste funzionalità sul desktop .NET e di ridurle a quasi zero in .NET Core a causa della disponibilità di `Span<T>`

Il set di funzionalità del linguaggio descritte in questo articolo consentirà alle applicazioni di continuare a usare queste funzionalità, con una varianza minima o nulla per la base di codice dell'applicazione, rimuovendo al tempo stesso l'overhead di allocazione imprevisto nella maggior parte dei casi.

## <a name="detailed-design"></a>Progettazione dettagliata 
Ecco un set di funzionalità che verranno usate qui per ottenere i risultati seguenti:

- Espansione `params` per supportare un set più ampio di tipi di raccolta.
- Consentire agli sviluppatori di personalizzare il modo in cui viene eseguita l'interpolazione `string`. 
- Consentire l'associazione di `string` interpolati a overload `string.Format` più efficienti.

### <a name="extending-params"></a>Estensione di params
Il linguaggio consentirà `params` nella firma di un metodo di disporre dei tipi `Span<T>`, `ReadOnlySpan<T>` e `IEnumerable<T>`. Le stesse regole per la chiamata verranno applicate a questi nuovi tipi che si applicano a `params T[]`:

- Non è possibile eseguire l'overload se l'unica differenza è una parola chiave `params`.
- Può richiamare passando una serie di argomenti convertibili in modo implicito in `T` o in un singolo `Span<T>` / 
`ReadOnlySpan<T>` / argomento `IEnumerable<T>`.
- Deve essere l'ultimo parametro in una firma del metodo.
- E così via... 

Per semplicità, le varianti `Span<T>` e `ReadOnlySpan<T>` verranno indicate come `Span<T>` di seguito. Nei casi in cui il comportamento di `ReadOnlySpan<T>` è diverso, verrà esplicitamente chiamato. 

Il vantaggio offerto dalle varianti `Span<T>` di `params` fornisce al compilatore una grande flessibilità nel modo in cui alloca l'archivio di backup per il valore di `Span<T>`. Con un `params T[]` il compilatore deve allocare una nuova `T[]` per ogni chiamata di un metodo di `params`. Il riutilizzo non è possibile perché deve presupporre che il chiamato abbia archiviato e riutilizzato il parametro. Questo può causare un notevole inefficienza nei metodi con numerose chiamate di `params`.

Date `Span<T>` varianti `ref struct` il chiamato non è in grado di archiviare l'argomento. Di conseguenza, il compilatore può ottimizzare i siti di chiamata eseguendo azioni come il riutilizzo dell'argomento. Questo può rendere molto efficienti le chiamate ripetute rispetto a `T[]`. Il linguaggio, tuttavia, non renderà disponibili garanzie specifiche sul modo in cui tali CallSites sono ottimizzate. Si noti che il compilatore è libero di usare valori diversi da `T[]` quando si richiama un metodo di `params Span<T>`. 

Uno di questi potenziali implementazioni è il seguente. Si consideri tutte `params` chiamata nel corpo di un metodo. Il compilatore può allocare una matrice con una dimensione uguale alla chiamata di `params` più grande e usarla per tutte le chiamate creando istanze di `Span<T>` opportunamente dimensionate sulla matrice. Ad esempio:

``` csharp
static class OneAllocation {
    static void Use(params Span<string> spans) {
        ...
    }

    static void Go() {
        Use("jaredpar");
        Use("hello", "world");
        Use("a", "longer", "set");
    }
}
```

Il compilatore può scegliere di emettere il corpo di `Go` come indicato di seguito:

``` csharp
    static void Go() {
        var args = new string[3];
        args[0] = "jaredpar";
        Use(new Span<string>(args, start: 0, length: 1));

        args[0] = "hello";
        args[1] = "world";
        Use(new Span<string>(args, start: 0, length: 2));

        args[0] = "a";
        args[1] = "longer";
        args[2] = "set";
        Use(new Span<string>(args, start: 0, length: 3));
   }
```

Questo può ridurre significativamente il numero di matrici allocate in un'applicazione. Le allocazioni possono essere ulteriormente ridotte se il runtime fornisce utilità per l'allocazione più intelligente dello stack di matrici.

Questa ottimizzazione non può essere sempre applicata. Anche se il chiamato non è in grado di acquisire l'argomento `params` può comunque essere acquisito nel chiamante quando è presente un `ref` o un parametro `out / ref` che è a sua volta un tipo `ref struct`. 

``` csharp
static class SneakyCapture {
    static ref int M(params Span<T> span) => ref span[0];

    static void Oops() {
        // This now holds onto the memory backing the Span<T> 
        ref int r = ref M(42);
    }
}
```

Questi casi sono comunque rilevabili in modo statico. Questo problema si verifica potenzialmente ogni volta che viene restituito un `ref` o un `ref struct` parametro passato da `out` o `ref`. In tal caso, il compilatore deve allocare una `T[]` aggiornata per ogni chiamata. 

Alla fine di questo documento sono illustrate diverse altre strategie di ottimizzazione potenziali.

Il `IEnumerable<T>` Variant è semplicemente un overload di praticità. È utile in scenari con frequenti utilizzi di `IEnumerable<T>`, ma con un numero elevato di `params` utilizzo. Quando viene richiamato in `T` argomento, lo spazio di archiviazione di backup verrà allocato come `T[]` proprio come `params T[]` viene eseguito oggi.

### <a name="params-overload-resolution-changes"></a>modifiche della risoluzione dell'overload params
Questa proposta significa che la lingua dispone ora di quattro varianti di `params`, dove prima ne aveva una. È ragionevole per i metodi definire overload di metodi che differiscono solo per il tipo di una dichiarazione di `params`. 

Si tenga presente che `StringBuilder.AppendFormat` aggiungerebbe certamente un overload `params ReadOnlySpan<object>` oltre al `params object[]`. Ciò consente di migliorare notevolmente le prestazioni riducendo le allocazioni di raccolta senza richiedere alcuna modifica al codice chiamante. 

Per semplificare questa operazione, il linguaggio introdurrà la regola di suddivisione dell'overload della risoluzione dell'overload seguente. Quando i metodi candidati differiscono solo per il parametro `params`, i candidati saranno preferiti nell'ordine seguente:

1. `ReadOnlySpan<T>`
1. `Span<T>`
1. `T[]`
1. `IEnumerable<T>`

Questo ordine è il più economico per il caso generale.

### <a name="variant"></a>Variant
CoreFX è la creazione di prototipi di un nuovo tipo gestito denominato [Variant](https://github.com/dotnet/corefxlab/pull/2595). Questo tipo deve essere usato nelle API che prevedono valori eterogenei, ma non desiderano che il sovraccarico venga immesso utilizzando `object` come parametro. Il tipo di `Variant` fornisce l'archiviazione universale, ma evita l'allocazione Boxing per i tipi usati più di frequente. L'uso di questo tipo in API come `string.Format` può eliminare l'overhead di Boxing nella maggior parte dei casi.

Questo tipo non è necessariamente speciale per il linguaggio. Viene introdotta in questo documento separatamente, sebbene diventi un dettaglio di implementazione di altre parti della proposta. 

### <a name="efficient-interpolated-strings"></a>Stringhe interpolate efficienti
Le stringhe interpolate sono una funzionalità molto diffusa ma C#inefficiente in. La sintassi più comune, usando una `string` interpolata come `string`, si traduce in una chiamata di `string.Format(string, params object[])`. Questo comporterà allocazioni Boxing per tutti i tipi di valore, allocazioni di `string` intermedie poiché l'implementazione USA in gran parte `object.ToString` per la formattazione e le allocazioni di matrici quando il numero di argomenti supera la quantità di parametri negli overload "Fast" di `string.Format`. 

Il linguaggio cambierà la riduzione dell'interpolazione per prendere in considerazione gli overload alternativi di `string.Format`. Prenderà in considerazione tutte le forme di `string.Format(string, params)` e sceglierà l'overload "Best" che soddisfa i tipi di argomento.
L'overload "migliore" `params` sarà determinato dalle regole descritte in precedenza. Ciò significa che `string` interpolate ora possono essere associate a overload molto efficienti come `string.Format(string format, params ReadOnlySpan<Variant> args)`. In molti casi, tutte le allocazioni intermedie vengono rimosse.

### <a name="customizable-interpolated-strings"></a>Stringhe interpolate personalizzabili
Gli sviluppatori sono in grado di personalizzare il comportamento delle stringhe interpolate con `FormattableString`. Contiene i dati che vengono inseriti in una stringa interpolata: il formato `string` e gli argomenti come matrice. Questa operazione, tuttavia, ha ancora l'allocazione Boxing e la matrice di argomenti, nonché l'allocazione per `FormattableString` (si tratta di un `abstract class`). Di conseguenza, non è possibile utilizzare le applicazioni che sono molto complesse nell'allocazione `string` formattazione.

Per rendere efficiente la formattazione delle stringhe interpolate, il linguaggio rileverà un nuovo tipo: `System.ValueFormattableString`. Tutte le stringhe interpolate avranno una conversione del tipo di destinazione in questo tipo. Questa operazione verrà implementata traducendo la stringa interpolata nella chiamata `ValueFormattableString.Create` esattamente come avviene per `FormattableString.Create` oggi. Il linguaggio supporterà tutte le opzioni `params` descritte in questo documento quando si cerca il metodo di `ValueFormattableString.Create` più appropriato. 

``` csharp
readonly struct ValueFormattableString {
    public static ValueFormattableString Create(Variant v) { ... } 
    public static ValueFormattableString Create(string s) { ... } 
    public static ValueFormattableString Create(string s, params ReadOnlySpan<Variant> collection) { ... } 
}

class ConsoleEx { 
    static void Write(ValueFormattableString f) { ... }
}

class Program { 
    static void Main() { 
        ConsoleEx.Write(42);
        ConsoleEx.Write($"hello {DateTime.UtcNow}");

        // Translates into 
        ConsoleEx.Write(ValueFormattableString.Create((Variant)42));
        ConsoleEx.Write(ValueFormattableString.Create(
            "hello {0}", 
            new Variant(DateTime.UtcNow));
    }
}
```

Le regole di risoluzione dell'overload verranno modificate in modo da preferire `ValueFormattableString` rispetto `string` quando l'argomento è una stringa interpolata. Ciò significa che sarà utile avere overload che differiscono solo per `string` e `ValueFormattableString`. Un sovraccarico di questo tipo oggi con `FormattableString` non è utile perché il compilatore preferisce sempre la versione `string` (a meno che lo sviluppatore non usi un cast esplicito). 

## <a name="open-issues"></a>Problemi aperti

### <a name="valueformattablestring-breaking-change"></a>Modifica di rilievo ValueFormattableString
La modifica da preferire `ValueFormattableString` durante la risoluzione dell'overload su `string` è una modifica sostanziale. Uno sviluppatore può definire un tipo denominato `ValueFormattableString` oggi e utilizzarlo negli overload del metodo con `string`. Questa modifica proposta comporta il prelievo da parte del compilatore di un overload diverso dopo l'implementazione di questo set di funzionalità. 

La possibilità di questo sembra ragionevolmente bassa. Per il tipo è necessario il nome completo `System.ValueFormattableString` ed è necessario disporre di `static` metodi denominati `Create`. Dato che gli sviluppatori sono fortemente sconsigliati di definire qualsiasi tipo nello spazio dei nomi `System` questa rottura sembra una compromissione ragionevole.

### <a name="expanding-to-more-types"></a>Espansione a più tipi
Dato che ci troviamo nell'area, dobbiamo prendere in considerazione l'aggiunta di `IList<T>`, `ICollection<T>` e `IReadOnlyList<T>` al set di raccolte per cui `params` è supportato. In termini di implementazione, il costo di una piccola quantità rispetto alle altre operazioni qui.

LDM deve decidere se la complicazione per la lingua è comunque valsa la pena. L'aggiunta di `IEnumerable<T>` rimuove un punto di attrito molto specifico. Manca questa `params` soluzione molti clienti hanno dovuto allocare `T[]` da un `IEnumerable<T>` quando si chiama un metodo `params`. L'aggiunta di `IEnumerable<T>` corregge tuttavia questo problema. Non esiste un punto di attrito specifico che le altre interfacce correggeranno qui. 

## <a name="considerations"></a>Considerazioni

### <a name="variant2-and-variant3"></a>Variant2 e Variant3
Il team di CoreFX dispone anche di un set di tipi di archiviazione non allocato per un massimo di tre `Variant` argomenti. Si tratta di una singola `Variant`, `Variant2` e `Variant3`. Tutti dispongono di una coppia di metodi per ottenere un'allocazione priva di `Span<Variant>`: `CreateSpan` e `KeepAlive`. Ciò significa che per un `params Span<Variant>` di un massimo di tre argomenti, il sito di chiamata può essere interamente allocazione gratuita.

``` csharp
static class ZeroAllocation {
    static void Use(params Span<Variant> spans) {
        ...
    }

    static void Go() {
        Use("hello", "world");
    }
}
```

Il metodo `Go` può essere ridotto a quanto segue:

``` csharp
static class ZeroAllocation {
    static void Go() {
        Variant2 _v;
        _v.Variant1 = new Variant("hello");
        _v.Variant2 = new Variant("word");
        Use(_v.CreateSpan());
        _v.KeepAlive();
    }
}
```

Questa operazione richiede un lavoro molto limitato sulla proposta di riutilizzare `T[]` tra `params Span<T>` chiamate. Il compilatore è già in grado di gestire un oggetto temporaneo per chiamata ed eseguire la pulizia del lavoro dopo (anche se in un caso si sta semplicemente contrassegnando una temperatura interna come disponibile). 

Nota: la funzione `KeepAlive` è necessaria solo sul desktop. In .NET Core il metodo non sarà disponibile e pertanto il compilatore non emetterà una chiamata al metodo.

### <a name="clr-stack-allocation-helpers"></a>Helper di allocazione dello stack CLR
CLR fornisce solo [localloc](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.localloc?redirectedfrom=MSDN&view=netframework-4.7.2) per l'allocazione dello stack della memoria contigua. Questa istruzione è limitata perché funziona solo per i tipi di `unmanaged`. Ciò significa che non può essere usato come soluzione universale per allocare in modo efficiente l'archiviazione di backup per `params 
 Span<T>`. 

Questa limitazione non è una limitazione fondamentale, ma piuttosto un artefatto della cronologia. CLR può scegliere di aggiungere nuovi codici op/intrinseci che forniscono l'allocazione dello stack universale. Questi possono quindi essere usati per allocare l'archiviazione di backup per la maggior parte delle chiamate `params Span<T>`.

``` csharp
static class BetterAllocation {
    static void Use(params Span<string> spans) {
        ...
    }

    static void Go() {
        Use("hello", "world");
    }
}
```

Il metodo `Go` può essere ridotto a quanto segue:

``` csharp
static class ZeroAllocation {
    static void Go() {
        Span<T> span = RuntimeIntrinsic.StackAlloc<string>(length: 2);
        span[0] = "hello";
        span[1] = "world";
        Use(span);
    }
}
```

Sebbene questo approccio sia molto efficiente, comporta un uso aggiuntivo dello stack. In un algoritmo che dispone di un deep stack e di molti `params` utilizzo è possibile che venga generata una `StackOverflowException` in cui una semplice allocazione `T[]` avrà esito positivo. 

Sfortunatamente C# non è configurato per il tipo di analisi tra i metodi in cui è possibile determinare se la chiamata deve usare l'allocazione di stack o heap di `params`. Può considerare solo ogni chiamata.

CLR è la configurazione migliore per eseguire questo tipo di determinazione in fase di esecuzione. Di conseguenza, è probabile che il Runtime fornisca due metodi per l'allocazione dello stack universale:

1. `Span<T> StackAlloc<T>(int length)`: presenta gli stessi comportamenti e limitazioni di `localloc` ad eccezione del fatto che può funzionare in qualsiasi `T`di tipo. 
1. `Span<T> MaybeStackAlloc<T>(int length)`: questo runtime può scegliere di implementare questa operazione eseguendo un'allocazione dello stack o dell'heap. Il runtime può quindi usare il contesto di esecuzione in cui viene chiamato per determinare la modalità di allocazione del `Span<T>`. Il chiamante tuttavia lo considera sempre come se fosse allocato nello stack.

Per i casi molto semplici, ad esempio uno a due argomenti C# , il compilatore potrebbe sempre usare `StackAlloc<T>` Variant. Questo è improbabile che contribuisca significativamente all'esaurimento dello stack nella maggior parte dei casi. Per altri casi, il compilatore può scegliere di usare `MaybeStackAlloc<T>` e lasciare che il runtime effettui la chiamata.

Il modo in cui si sceglie probabilmente richiederà un'analisi più approfondita e l'analisi delle app reali. Tuttavia, se queste nuove funzioni intrinseche sono disponibili, questo tipo di flessibilità verrà fornito.

### <a name="why-not-varargs"></a>Perché non varargs? 
La funzionalità [varargs](https://docs.microsoft.com/en-us/cpp/windows/variable-argument-lists-dot-dot-dot-cpp-cli?view=vs-2017) esistente è stata considerata come una possibile soluzione. Questa funzionalità è tuttavia destinata principalmente C++agli scenari/CLI e presenta buchi noti per altri scenari. Il porting di questo a UNIX comporta inoltre un costo significativo. Quindi non è stata considerata una soluzione valida.

## <a name="related-issues"></a>Problemi correlati
Questa specifica è correlata ai problemi seguenti: 

- https://github.com/dotnet/csharplang/issues/1757
- https://github.com/dotnet/csharplang/issues/179
- https://github.com/dotnet/corefxlab/pull/2595

