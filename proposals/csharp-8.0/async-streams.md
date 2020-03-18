---
ms.openlocfilehash: 9ed1aa75d581cecbf754a84d1f523c6334b8c0ac
ms.sourcegitcommit: 5278336b61519956240a6f7d83bcb4322019e032
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79485259"
---
# <a name="async-streams"></a>Flussi asincroni

* [x] proposto
* Prototipo [x]
* [] Implementazione
* [] Specifica

## <a name="summary"></a>Summary
[summary]: #summary

C#dispone del supporto per metodi iteratori e metodi asincroni, ma non supporta un metodo che sia un iteratore che un metodo asincrono.  È necessario risolvere il problema consentendo l'uso di `await` in un nuovo modulo di `async` iteratore, uno che restituisce un `IAsyncEnumerable<T>` o `IAsyncEnumerator<T>` anziché un `IEnumerable<T>` o `IEnumerator<T>`, con `IAsyncEnumerable<T>` utilizzabile in un nuovo `await foreach`.  Per abilitare la pulizia asincrona viene inoltre utilizzata un'interfaccia `IAsyncDisposable`.

## <a name="related-discussion"></a>Discussione correlata
- https://github.com/dotnet/roslyn/issues/261
- https://github.com/dotnet/roslyn/issues/114

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

## <a name="interfaces"></a>Interfacce

### <a name="iasyncdisposable"></a>IAsyncDisposable

C'è stata una discussione approfondita di `IAsyncDisposable`, ad esempio https://github.com/dotnet/roslyn/issues/114) e se si tratta di una buona idea.  Tuttavia, si tratta di un concetto necessario per aggiungere supporto per gli iteratori asincroni.  Poiché i blocchi di `finally` possono contenere `await`s e poiché i blocchi di `finally` devono essere eseguiti come parte dell'eliminazione degli iteratori, è necessaria l'eliminazione asincrona.  È anche di solito utile ogni volta che la pulizia delle risorse potrebbe richiedere un certo periodo di tempo, ad esempio la chiusura di file (che richiedono lo scaricamento), la deregistrazione dei callback e la possibilità di individuare il momento in cui la registrazione è stata completata e così via.

L'interfaccia seguente viene aggiunta alle librerie .NET di base (ad esempio System. private. CoreLib/System. Runtime):
```csharp
namespace System
{
    public interface IAsyncDisposable
    {
        ValueTask DisposeAsync();
    }
}
```
Come per `Dispose`, la chiamata di `DisposeAsync` più volte è accettabile e le chiamate successive dopo la prima devono essere considerate come pon, restituendo un'attività completata in modo sincrono (`DisposeAsync` non devono essere thread-safe, tuttavia, e non devono supportare la chiamata simultanea).  Inoltre, i tipi possono implementare sia `IDisposable` che `IAsyncDisposable`e, in caso affermativo, è accettabile richiamarli `Dispose` e quindi `DisposeAsync` o viceversa, ma solo il primo deve essere significativo e le chiamate successive di devono essere di tipo NOP.  Di conseguenza, se un tipo implementa entrambi, è consigliabile che gli utenti chiamino una sola volta il metodo più pertinente in base al contesto, `Dispose` nei contesti sincroni e `DisposeAsync` in quelli asincroni.

(Sto lasciando una discussione sul modo in cui `IAsyncDisposable` interagisce con `using` a una discussione separata.  E la copertura del modo in cui interagisce con `foreach` viene gestita più avanti in questa proposta.

Alternative considerate:
- _`DisposeAsync` accettando una `CancellationToken`_ : in teoria, è sensato che qualsiasi asincrono possa essere annullata, l'eliminazione riguarda la pulizia, la chiusura, le risorse free'ing e così via, che in genere non è un elemento che deve essere annullato. la pulizia è ancora importante per il lavoro annullato.  Lo stesso `CancellationToken` che ha causato l'annullamento del lavoro effettivo è in genere lo stesso token passato a `DisposeAsync`, rendendo `DisposeAsync` inutile perché l'annullamento del lavoro provocherebbe un NOP per `DisposeAsync`.  Se un utente vuole evitare che venga bloccato in attesa di eliminazione, può evitare di aspettare il `ValueTask`risultante o attenderlo solo per un determinato periodo di tempo.
- _`DisposeAsync` la restituzione di un `Task`_ : ora che non esiste una `ValueTask` generica e che può essere costruita da un `IValueTaskSource`, restituendo `ValueTask` da `DisposeAsync` è possibile riutilizzare un oggetto esistente come promessa che rappresenta il completamento asincrono finale di `DisposeAsync`, salvando un'allocazione `Task` nel caso in cui `DisposeAsync` venga completata in modo asincrono.
- _Configurazione di `DisposeAsync` con un `bool continueOnCapturedContext` (`ConfigureAwait`)_ : Sebbene ci siano problemi correlati al modo in cui tale concetto viene esposto a `using`, `foreach`e altri costrutti di linguaggio che lo utilizzano, dal punto di vista dell'interfaccia non esegue alcuna `await`"e non è necessario configurare nulla... i consumer del `ValueTask` possono utilizzarlo tuttavia.
- _`IAsyncDisposable` ereditare `IDisposable`_ : poiché è necessario usare solo uno o l'altro, non ha senso forzare i tipi a implementare entrambi.
- _`IDisposableAsync` al posto di `IAsyncDisposable`_ : è stato seguito il nome che gli elementi o i tipi sono "Async something" mentre le operazioni sono "done Async", quindi i tipi hanno "Async" come prefisso e i metodi hanno "Async" come suffisso.

### <a name="iasyncenumerable--iasyncenumerator"></a>IAsyncEnumerable / IAsyncEnumerator

Vengono aggiunte due interfacce alle librerie .NET di base:

```csharp
namespace System.Collections.Generic
{
    public interface IAsyncEnumerable<out T>
    {
        IAsyncEnumerator<T> GetAsyncEnumerator(CancellationToken cancellationToken = default);
    }

    public interface IAsyncEnumerator<out T> : IAsyncDisposable
    {
        ValueTask<bool> MoveNextAsync();
        T Current { get; }
    }
}
```

Il consumo tipico (senza funzionalità aggiuntive del linguaggio) avrà un aspetto simile al seguente:

```csharp
IAsyncEnumerator<T> enumerator = enumerable.GetAsyncEnumerator();
try
{
    while (await enumerator.MoveNextAsync())
    {
        Use(enumerator.Current);
    }
}
finally { await enumerator.DisposeAsync(); }
```

Opzioni scartate considerate:
- _`Task<bool> MoveNextAsync(); T current { get; }`_ : l'utilizzo di `Task<bool>` supporta l'utilizzo di un oggetto attività memorizzato nella cache per rappresentare chiamate sincrone e `MoveNextAsync` di esito positivo, ma è comunque necessaria un'allocazione per il completamento asincrono.  Restituendo `ValueTask<bool>`, l'oggetto enumeratore viene abilitato a se stesso per implementare `IValueTaskSource<bool>` ed essere utilizzato come supporto per il `ValueTask<bool>` restituito da `MoveNextAsync`, che a sua volta consente un sovraccarico significativamente ridotto.
- _`ValueTask<(bool, T)> MoveNextAsync();`_ : non solo è più difficile da utilizzare, ma significa che `T` non può più essere covariante.
- _`ValueTask<T?> TryMoveNextAsync();`_ : non covariante.
- _`Task<T?> TryMoveNextAsync();`_ : non covariante, allocazioni a ogni chiamata e così via.
- _`ITask<T?> TryMoveNextAsync();`_ : non covariante, allocazioni a ogni chiamata e così via.
- _`ITask<(bool,T)> TryMoveNextAsync();`_ : non covariante, allocazioni a ogni chiamata e così via.
- _`Task<bool> TryMoveNextAsync(out T result);`_ : è necessario impostare il risultato del `out` quando l'operazione viene restituita in modo sincrono, non quando l'attività viene completata in modo asincrono in futuro, quindi non sarà possibile comunicare il risultato.
- _`IAsyncEnumerator<T>` non implementare `IAsyncDisposable`_ : è possibile scegliere di separarli.  Tuttavia, in questo modo si complicano alcune altre aree della proposta, perché il codice deve essere in grado di gestire la possibilità che un enumeratore non fornisca l'eliminazione, rendendo difficile la scrittura di helper basati su modelli.  Inoltre, per gli enumeratori sarà necessario disporre di un'eliminazione (ad esempio, qualsiasi C# iteratore asincrono con un blocco finally, la maggior parte delle operazioni di enumerazione dei dati da una connessione di rete e così via) e, se non è possibile, è semplice implementare il metodo esclusivamente come `public ValueTask DisposeAsync() => default(ValueTask);` con un overhead aggiuntivo minimo.
- _ `IAsyncEnumerator<T> GetAsyncEnumerator()`: nessun parametro del token di annullamento.

#### <a name="viable-alternative"></a>Alternativa valida:

```csharp
namespace System.Collections.Generic
{
    public interface IAsyncEnumerable<out T>
    {
        IAsyncEnumerator<T> GetAsyncEnumerator();
    }

    public interface IAsyncEnumerator<out T> : IAsyncDisposable
    {
        ValueTask<bool> WaitForNextAsync();
        T TryGetNext(out bool success);
    }
}
```

`TryGetNext` viene usato in un ciclo interno per utilizzare elementi con una singola chiamata di interfaccia, purché siano disponibili in modo sincrono.  Quando l'elemento successivo non può essere recuperato in modo sincrono, restituisce false e ogni volta che restituisce false, un chiamante deve successivamente richiamare `WaitForNextAsync` per attendere che l'elemento successivo sia disponibile o per determinare che non sarà mai presente un altro elemento. Il consumo tipico (senza funzionalità aggiuntive del linguaggio) avrà un aspetto simile al seguente:

```csharp
IAsyncEnumerator<T> enumerator = enumerable.GetAsyncEnumerator();
try
{
    while (await enumerator.WaitForNextAsync())
    {
        while (true)
        {
            int item = enumerator.TryGetNext(out bool success);
            if (!success) break;
            Use(item);
        }
    }
}
finally { await enumerator.DisposeAsync(); }
```

Il vantaggio è costituito da due riduzioni, una secondaria e una principale:
- _Minor: consente a un enumeratore di supportare più consumer_. Potrebbero esserci scenari in cui è utile che un enumeratore supporti più consumer simultanei.  Non è possibile ottenere questo risultato quando `MoveNextAsync` e `Current` sono separati in modo che un'implementazione non possa renderne l'utilizzo atomico.  Questo approccio, invece, fornisce un solo metodo `TryGetNext` che supporta il push dell'enumeratore in avanti e il recupero dell'elemento successivo, in modo che l'enumeratore possa abilitare l'atomicità se lo si desidera.  Tuttavia, è probabile che questi scenari possano essere abilitati anche fornendo a ogni consumer il proprio enumeratore da un enumerabile condiviso.  Inoltre, non è necessario imporre che ogni enumeratore supporti l'utilizzo simultaneo, in quanto in questo modo si aggiungono sovraccarichi non banali alla maggior parte dei casi che non lo richiedono, il che significa che un consumer dell'interfaccia non è in genere in grado di basarsi su questo metodo.
- _Principale: prestazioni_. L'approccio `MoveNextAsync`/`Current` richiede due chiamate di interfaccia per operazione, mentre il caso migliore per `WaitForNextAsync`/`TryGetNext` è che la maggior parte delle iterazioni viene completata in modo sincrono, consentendo un ciclo interno stretto con `TryGetNext`, in modo da avere una sola chiamata di interfaccia per ogni operazione.  Questo può avere un effetto misurabile nelle situazioni in cui le chiamate di interfaccia dominano il calcolo.

Tuttavia, esistono svantaggi non banali, tra cui una maggiore complessità quando questi vengono utilizzati manualmente e una maggiore probabilità di introdurre bug quando vengono utilizzati.  Mentre i vantaggi in merito alle prestazioni vengono visualizzati in microbenchmark, non ci riteniamo che saranno interessati dalla maggior parte dell'utilizzo reale.  Se si scopre che sono, è possibile introdurre un secondo set di interfacce in modo leggero.

Opzioni scartate considerate:
- `ValueTask<bool> WaitForNextAsync(); bool TryGetNext(out T result);`: i parametri di `out` non possono essere covarianti.  È anche presente un piccolo effetto (un problema con il modello try in generale) che probabilmente comporta una barriera di scrittura di runtime per i risultati del tipo di riferimento.

#### <a name="cancellation"></a>Annullamento

Esistono diversi approcci possibili per supportare l'annullamento:
1. `IAsyncEnumerable<T>`/`IAsyncEnumerator<T>` sono indipendenti dall'annullamento: `CancellationToken` non viene visualizzato in un punto qualsiasi.  L'annullamento viene ottenuto tramite la cottura logica del `CancellationToken` nell'enumerabile e/o nell'enumeratore nel modo appropriato, ad esempio quando si chiama un iteratore, passando l'`CancellationToken` come argomento al metodo iteratore e utilizzandolo nel corpo dell'iteratore, come avviene con qualsiasi altro parametro.
2. `IAsyncEnumerator<T>.GetAsyncEnumerator(CancellationToken)`: si passa una `CancellationToken` a `GetAsyncEnumerator`e le successive operazioni di `MoveNextAsync` lo rispettano, ma è possibile.
3. `IAsyncEnumerator<T>.MoveNextAsync(CancellationToken)`: si passa una `CancellationToken` a ogni singola chiamata di `MoveNextAsync`.
4. 1 & & 2: incorporare `CancellationToken`s nell'enumeratore/enumeratore e passare `CancellationToken`s in `GetAsyncEnumerator`.
5. 1 & & 3: incorporare `CancellationToken`s nell'enumeratore/enumeratore e passare `CancellationToken`s in `MoveNextAsync`.

Dal punto di vista puramente teorico, (5) è il più affidabile, in quanto (a) `MoveNextAsync` accettare una `CancellationToken` consente il controllo più granulare sugli elementi annullati e (b) `CancellationToken` è solo qualsiasi altro tipo che può essere passato come argomento negli iteratori, incorporati in tipi arbitrari e così via.

Tuttavia, esistono diversi problemi con questo approccio:
- In che modo un `CancellationToken` passato a `GetAsyncEnumerator` impostarlo nel corpo dell'iteratore?  È possibile esporre una nuova parola chiave `iterator` a cui è possibile fare il punto per accedere al `CancellationToken` passato al `GetEnumerator`. Tuttavia, a) si tratta di una grande quantità di macchinari aggiuntivi, b) che si tratta di un cittadino di prima classe e c) il 99% caso sembrerebbe lo stesso codice che chiama un iteratore e chiama `GetAsyncEnumerator` su di esso, nel qual caso può semplicemente passare la `CancellationToken` come argomento nel metodo.
- In che modo un `CancellationToken` passato a `MoveNextAsync` accedere al corpo del metodo?  Questo è ancora peggio, come se venisse esposto da un `iterator` oggetto locale, il suo valore potrebbe cambiare tra gli await, il che significa che il codice registrato con il token deve annullare la registrazione prima di await e quindi ripetere la registrazione dopo; è anche potenzialmente costoso dover eseguire la registrazione e l'annullamento della registrazione in ogni chiamata di `MoveNextAsync`, indipendentemente dal fatto che sia implementato dal compilatore in un iteratore o da uno sviluppatore manualmente.
- In che modo uno sviluppatore Annulla un ciclo `foreach`?  Se questa operazione viene eseguita assegnando a un `CancellationToken` a un enumeratore/enumeratore, un oggetto) è necessario supportare `foreach`' ing over enumeratori, che li eleva a essere cittadini di prima classe e ora è necessario iniziare a pensare a un ecosistema costruito intorno agli enumeratori (ad esempio metodi LINQ) o b) è necessario incorporare il `CancellationToken` nell'enumerabile in modo da disporre di un `WithCancellation` metodo di estensione disattivato `IAsyncEnumerable<T>` che archivia il token fornito e quindi passarlo all'`GetAsyncEnumerator` enumerabile di cui è stato eseguito il wrapped quando il `GetAsyncEnumerator` nello struct viene richiamato (ignorando tale token).  In alternativa, è possibile usare solo il `CancellationToken` nel corpo del ciclo foreach.
- Se/quando sono supportati i genericità delle query, in che modo il `CancellationToken` fornito per `GetEnumerator` o `MoveNextAsync` essere passato a ogni clausola?  Il modo più semplice è semplicemente la clausola per la relativa acquisizione. a quel punto, qualsiasi token viene passato a `GetAsyncEnumerator`/`MoveNextAsync` viene ignorato.

È stata consigliata una versione precedente di questo documento (1), ma è stato passato a (4).

I due problemi principali con (1):
- i produttori di Enumerable annullabili devono implementare alcuni standard e possono sfruttare il supporto del compilatore solo per gli iteratori asincroni per implementare un metodo `IAsyncEnumerator<T> GetAsyncEnumerator(CancellationToken)`.
- è probabile che molti produttori debbano semplicemente aggiungere un parametro di `CancellationToken` alla propria firma enumerabile asincrona, in modo da impedire ai consumer di passare il token di annullamento desiderato quando viene assegnato un tipo di `IAsyncEnumerable`.

Esistono due scenari principali di utilizzo:
1. `await foreach (var i in GetData(token)) ...` in cui il consumer chiama il metodo async-iterator,
2. `await foreach (var i in givenIAsyncEnumerable.WithCancellation(token)) ...` in cui il consumer gestisce un'istanza di `IAsyncEnumerable` specificata.

Si ritiene che un compromesso ragionevole per supportare entrambi gli scenari in modo che sia utile per i producer e i consumer di flussi asincroni consiste nell'usare un parametro con annotazioni specifico nel metodo asincrono-iteratore. A questo scopo, viene usato l'attributo `[EnumeratorCancellation]`. L'inserimento di questo attributo su un parametro indica al compilatore che se un token viene passato al metodo `GetAsyncEnumerator`, è necessario utilizzare tale token al posto del valore passato originariamente per il parametro.

Considerare `IAsyncEnumerable<int> GetData([EnumeratorCancellation] CancellationToken token = default)`. L'implementatore di questo metodo può semplicemente utilizzare il parametro nel corpo del metodo. Il consumer può usare i modelli di consumo indicati in precedenza:
1. Se si usa `GetData(token)`, il token viene salvato in Async-Enumerable e verrà usato nell'iterazione.
2. Se si usa `givenIAsyncEnumerable.WithCancellation(token)`, il token passato a `GetAsyncEnumerator` sostituirà tutti i token salvati in Async-Enumerable.

## <a name="foreach"></a>foreach

`foreach` sarà aumentata per supportare `IAsyncEnumerable<T>` oltre al supporto esistente per `IEnumerable<T>`.  Inoltre, supporterà l'equivalente di `IAsyncEnumerable<T>` come modello se i membri pertinenti sono esposti pubblicamente, eseguendo il fallback all'utilizzo diretto dell'interfaccia in caso contrario, per abilitare le estensioni basate su struct che evitano l'allocazione e l'utilizzo di awaitable alternativi come tipo restituito di `MoveNextAsync` e `DisposeAsync`.

### <a name="syntax"></a>Sintassi

Utilizzando la sintassi:

```csharp
foreach (var i in enumerable)
```

C#continuerà a considerare `enumerable` come un enumerabile sincrono, in modo che anche se espone le API appropriate per gli Enumerable asincroni (esponendo il modello o implementando l'interfaccia), considererà solo le API sincrone.

Per forzare la `foreach` al invece di considerare solo le API asincrone, `await` viene inserito come indicato di seguito:

```csharp
await foreach (var i in enumerable)
```

Non viene fornita alcuna sintassi che supporti l'uso delle API Async o Sync. lo sviluppatore deve scegliere in base alla sintassi utilizzata.

Opzioni scartate considerate:
- _`foreach (var i in await enumerable)`_ : questa è già una sintassi valida e la modifica del suo significato è una modifica di rilievo.  Ciò significa `await` il `enumerable`, ottenere un elemento iterable in modo sincrono e quindi eseguire l'iterazione in modo sincrono.
- _`foreach (var i await in enumerable)`, `foreach (var await i in enumerable)`, `foreach (await var i in enumerable)`_ : questi tutti indicano che è in attesa dell'elemento successivo, ma sono presenti altre attese in foreach, in particolare se l'enumerabile è un `IAsyncDisposable`, si `await`l'eliminazione asincrona.  Questo await è come ambito di foreach anziché per ogni singolo elemento, quindi la parola chiave `await` merita di essere a livello di `foreach`.  Inoltre, il fatto che sia associato al `foreach` ci consente di descrivere il `foreach` con un termine diverso, ad esempio "await foreach".  Tuttavia, ancora più importante, è importante considerare `foreach` sintassi allo stesso tempo della sintassi `using`, in modo che rimangano coerenti tra loro e `using (await ...)` sia già una sintassi valida.
- _`foreach await (var i in enumerable)`_

Tenere comunque presente quanto segue:
- `foreach` attualmente non supporta l'iterazione in un enumeratore.  Si prevede che sia più comune avere `IAsyncEnumerator<T>`e che quindi si sta tentando di supportare `await foreach` con `IAsyncEnumerable<T>` e `IAsyncEnumerator<T>`.  Tuttavia, una volta aggiunto tale supporto, viene introdotta la questione se `IAsyncEnumerator<T>` è un cittadino di prima classe e se è necessario disporre di overload di combinatori che operano su enumeratori in aggiunta a enumeratori?    Si vuole incoraggiare i metodi per restituire gli enumeratori anziché gli enumerabili? È necessario continuare a illustrare questo problema.  Se si decide di non supportarlo, potrebbe essere necessario introdurre un metodo di estensione `public static IAsyncEnumerable<T> AsEnumerable<T>(this IAsyncEnumerator<T> enumerator);` che consentirebbe a un enumeratore di continuare a `foreach`.  Se si decide di supportarla, è necessario decidere se la `await foreach` è responsabile della chiamata `DisposeAsync` sull'enumeratore e la risposta è probabilmente "No, il controllo sull'eliminazione deve essere gestito da chiunque abbia chiamato `GetEnumerator`".

### <a name="pattern-based-compilation"></a>Compilazione basata su modelli

Il compilatore eseguirà l'associazione alle API basate su modelli, se esistenti, preferendole tramite l'interfaccia (il modello può essere soddisfatto con i metodi di istanza o i metodi di estensione).  I requisiti per il modello sono:
- L'enumerabile deve esporre un `GetAsyncEnumerator` metodo che può essere chiamato senza argomenti e che restituisce un enumeratore che soddisfa il modello pertinente.
- L'enumeratore deve esporre un `MoveNextAsync` metodo che può essere chiamato senza argomenti e che restituisce un elemento che può essere `await`ed e il cui `GetResult()` restituisce un `bool`.
- L'enumeratore deve inoltre esporre `Current` proprietà il cui Getter restituisce un `T` che rappresenta il tipo di dati enumerati.
- L'enumeratore può facoltativamente esporre un `DisposeAsync` metodo che può essere richiamato senza argomenti e che restituisce un elemento che può essere `await`ed e il cui `GetResult()` restituisce `void`.

Questo codice:

```csharp
var enumerable = ...;
await foreach (T item in enumerable)
{
   ...
}
```

viene convertito in equivalente a:

```csharp
var enumerable = ...;
var enumerator = enumerable.GetAsyncEnumerator();
try
{
    while (await enumerator.MoveNextAsync())
    {
       T item = enumerator.Current;
       ...
    }
}
finally
{
    await enumerator.DisposeAsync(); // omitted, along with the try/finally, if the enumerator doesn't expose DisposeAsync
}
```

Se il tipo iterato non espone il modello corretto, verranno usate le interfacce.

### <a name="configureawait"></a>ConfigureAwait

Questa compilazione basata su modelli consentirà l'uso di `ConfigureAwait` in tutti gli await, tramite un metodo di estensione `ConfigureAwait`:

```csharp
await foreach (T item in enumerable.ConfigureAwait(false))
{
   ...
}
```

Questa operazione sarà basata sui tipi che si aggiungeranno anche a .NET, probabilmente a System. Threading. Tasks. Extensions. dll:

```csharp
// Approximate implementation, omitting arg validation and the like
namespace System.Threading.Tasks
{
    public static class AsyncEnumerableExtensions
    {
        public static ConfiguredAsyncEnumerable<T> ConfigureAwait<T>(this IAsyncEnumerable<T> enumerable, bool continueOnCapturedContext) =>
            new ConfiguredAsyncEnumerable<T>(enumerable, continueOnCapturedContext);

        public struct ConfiguredAsyncEnumerable<T>
        {
            private readonly IAsyncEnumerable<T> _enumerable;
            private readonly bool _continueOnCapturedContext;

            internal ConfiguredAsyncEnumerable(IAsyncEnumerable<T> enumerable, bool continueOnCapturedContext)
            {
                _enumerable = enumerable;
                _continueOnCapturedContext = continueOnCapturedContext;
            }

            public ConfiguredAsyncEnumerator<T> GetAsyncEnumerator() =>
                new ConfiguredAsyncEnumerator<T>(_enumerable.GetAsyncEnumerator(), _continueOnCapturedContext);

            public struct Enumerator
            {
                private readonly IAsyncEnumerator<T> _enumerator;
                private readonly bool _continueOnCapturedContext;

                internal Enumerator(IAsyncEnumerator<T> enumerator, bool continueOnCapturedContext)
                {
                    _enumerator = enumerator;
                    _continueOnCapturedContext = continueOnCapturedContext;
                }

                public ConfiguredValueTaskAwaitable<bool> MoveNextAsync() =>
                    _enumerator.MoveNextAsync().ConfigureAwait(_continueOnCapturedContext);

                public T Current => _enumerator.Current;

                public ConfiguredValueTaskAwaitable DisposeAsync() =>
                    _enumerator.DisposeAsync().ConfigureAwait(_continueOnCapturedContext);
            }
        }
    }
}
```

Si noti che questo approccio non consentirà l'uso di `ConfigureAwait` con Enumerable basati su pattern, tuttavia, anche in questo caso, il `ConfigureAwait` viene esposto solo come estensione `Task`/`Task<T>`/`ValueTask`/e non può essere applicato a elementi awaitable arbitrari, perché è opportuno solo quando viene applicato alle attività (controlla un comportamento implementato nel supporto di continuazione dell'attività) e pertanto non ha senso quando si usa un modello in cui le attività awaitable potrebbero non essere attività.`ValueTask<T>`  Tutti gli utenti che restituiscono elementi awaitable possono fornire un comportamento personalizzato in scenari avanzati.

Se è possibile trovare un modo per supportare una soluzione di `ConfigureAwait` a livello di ambito o di assembly, questa operazione non è necessaria.

## <a name="async-iterators"></a>Iteratori asincroni

Il linguaggio/compilatore supporterà la produzione di `IAsyncEnumerable<T>`s e `IAsyncEnumerator<T>`s oltre a utilizzarli. Attualmente il linguaggio supporta la scrittura di un iteratore come:

```csharp
static IEnumerable<int> MyIterator()
{
    try
    {
        for (int i = 0; i < 100; i++)
        {
            Thread.Sleep(1000);
            yield return i;
        }
    }
    finally
    {
        Thread.Sleep(200);
        Console.WriteLine("finally");
    }
}
```

non è tuttavia possibile usare `await` nel corpo di questi iteratori.  Il supporto viene aggiunto.

### <a name="syntax"></a>Sintassi

Il supporto del linguaggio esistente per gli iteratori deduce la natura dell'iteratore del metodo a seconda che contenga una o più `yield`.  Lo stesso valore sarà vero per gli iteratori asincroni.  Tali iteratori asincroni saranno delimitati e differenziati dagli iteratori sincroni tramite l'aggiunta di `async` alla firma e devono avere anche `IAsyncEnumerable<T>` o `IAsyncEnumerator<T>` come tipo restituito.  Ad esempio, l'esempio precedente potrebbe essere scritto come iteratore asincrono, come indicato di seguito:

```csharp
static async IAsyncEnumerable<int> MyIterator()
{
    try
    {
        for (int i = 0; i < 100; i++)
        {
            await Task.Delay(1000);
            yield return i;
        }
    }
    finally
    {
        await Task.Delay(200);
        Console.WriteLine("finally");
    }
}
```

Alternative considerate:
- _Non usare `async` nella firma_: l'uso di `async` è probabilmente tecnicamente richiesto dal compilatore, perché lo usa per determinare se `await` è valido in tale contesto.  Tuttavia, anche se non è obbligatorio, abbiamo stabilito che `await` può essere usato solo nei metodi contrassegnati come `async`e sembra importante mantenerne la coerenza.
- L' _Abilitazione di compilatori personalizzati per `IAsyncEnumerable<T>`_ : si tratta di un elemento che è possibile esaminare per il futuro, ma il meccanismo è complesso e non è supportato per le controparti sincrone.
- _La presenza di una parola chiave `iterator` nella firma: gli_iteratori asincroni utilizzeranno `async iterator` nella firma e `yield` potevano essere utilizzati solo nei metodi `async` che includevano `iterator`; `iterator` viene quindi reso facoltativo negli iteratori sincroni.  A seconda della prospettiva, questo ha il vantaggio di renderlo molto chiaro dalla firma del metodo, indipendentemente dal fatto che `yield` sia consentito e dal fatto che il metodo restituisca istanze di tipo `IAsyncEnumerable<T>` invece che la produzione del compilatore a seconda che il codice usi o meno `yield`.  Ma è diverso dagli iteratori sincroni, che non possono essere eseguiti per richiederne uno.  Inoltre, alcuni sviluppatori non amano la sintassi aggiuntiva.  Se la progettazione è stata rilasciata da zero, è probabile che questa operazione sia necessaria, ma a questo punto è molto più utile mantenere gli iteratori asincroni vicini agli iteratori di sincronizzazione.

## <a name="linq"></a>LINQ

Sono presenti più di ~ 200 overload di metodi nella classe `System.Linq.Enumerable`, che funzionano tutti in termini di `IEnumerable<T>`; Alcune di queste accettano `IEnumerable<T>`, alcune delle quali producono `IEnumerable<T>`e molte eseguono entrambe.  L'aggiunta del supporto LINQ per `IAsyncEnumerable<T>` potrebbe comportare la duplicazione di tutti questi overload, per un altro ~ 200.  Poiché `IAsyncEnumerator<T>` probabilmente è più comune come entità autonoma nel mondo asincrono rispetto a `IEnumerator<T>` si trova nel mondo sincrono, potrebbero essere necessari altri ~ 200 overload che funzionano con `IAsyncEnumerator<T>`.  Inoltre, un numero elevato di overload occupano i predicati (ad esempio `Where` che accetta una `Func<T, bool>`) e potrebbe essere auspicabile avere Overload basati su `IAsyncEnumerable<T>`che gestiscono predicati sincroni e asincroni (ad esempio `Func<T, ValueTask<bool>>` oltre a `Func<T, bool>`).  Sebbene non sia applicabile a tutti i nuovi overload di ~ 400, un calcolo approssimativo è che sarebbe applicabile a metà, ovvero un altro ~ 200 Overloads, per un totale di ~ 600 nuovi metodi.

Si tratta di un numero sbalorditivo di API, con la possibilità di ancora di più quando vengono prese in considerazione librerie di estensione come Interactive Extensions (IX).  Ma IX dispone già di un'implementazione di molti di questi e non sembra essere un ottimo motivo per duplicare il lavoro; è invece consigliabile aiutare la community a migliorare IX e consigliarla quando gli sviluppatori vogliono usare LINQ con `IAsyncEnumerable<T>`.

Esiste anche il problema della sintassi di comprensione delle query.  La natura basata su modelli dei tipi di genericità delle query consente loro di "semplicemente lavorare" con alcuni operatori, ad esempio se IX fornisce i metodi seguenti:

```csharp
public static IAsyncEnumerable<TResult> Select<TSource, TResult>(this IAsyncEnumerable<TSource> source, Func<TSource, TResult> func);
public static IAsyncEnumerable<T> Where(this IAsyncEnumerable<T> source, Func<T, bool> func);
```

il C# codice verrà quindi "solo funzionante":

```csharp
IAsyncEnumerable<int> enumerable = ...;
IAsyncEnumerable<int> result = from item in enumerable
                               where item % 2 == 0
                               select item * 2;
```

Tuttavia, non esiste alcuna sintassi di comprensione delle query che supporti l'utilizzo di `await` nelle clausole, pertanto se si aggiunge IX, ad esempio:

```csharp
public static IAsyncEnumerable<TResult> Select<TSource, TResult>(this IAsyncEnumerable<TSource> source, Func<TSource, ValueTask<TResult>> func);
```

quindi, si tratta di un "solo lavoro":

```csharp
IAsyncEnumerable<string> result = from url in urls
                                  where item % 2 == 0
                                  select SomeAsyncMethod(item);

async ValueTask<int> SomeAsyncMethod(int item)
{
    await Task.Yield();
    return item * 2;
}
```

Tuttavia, non esiste alcun modo per scriverlo con il `await` inline nella clausola `select`.  Come sforzo separato, è possibile esaminare l'aggiunta di espressioni `async { ... }` al linguaggio, a questo punto è possibile consentirne l'uso nella comprensione delle query. in alternativa, è possibile scrivere quanto segue:

```csharp
IAsyncEnumerable<int> result = from item in enumerable
                               where item % 2 == 0
                               select async
                               {
                                   await Task.Yield();
                                   return item * 2;
                               };
```

o per abilitare l'utilizzo di `await` direttamente nelle espressioni, ad esempio supportando `async from`.  Tuttavia, è improbabile che una progettazione influisca sul resto del set di funzionalità un modo o sull'altro e che non si tratta di un aspetto particolarmente importante per investire in questo momento, quindi la proposta è di non eseguire alcuna operazione aggiuntiva in questo momento.

## <a name="integration-with-other-asynchronous-frameworks"></a>Integrazione con altri Framework asincroni

L'integrazione con `IObservable<T>` e altri Framework asincroni (ad esempio, i flussi reattivi) viene eseguita a livello di libreria anziché a livello di linguaggio.  Ad esempio, tutti i dati di un `IAsyncEnumerator<T>` possono essere pubblicati in un `IObserver<T>` semplicemente `await foreach`"ING over the enumerator and `OnNext`" ing the data to the Observer, quindi è possibile un metodo di estensione `AsObservable<T>`.  L'utilizzo di un `IObservable<T>` in un `await foreach` richiede il buffering dei dati (nel caso in cui venga eseguito il push di un altro elemento mentre l'elemento precedente è ancora in fase di elaborazione), ma è possibile implementare facilmente un adattatore push pull per consentire il pull di un `IObservable<T>` con una `IAsyncEnumerator<T>`.  Via.  RX/IX fornisce già prototipi di tali implementazioni e librerie come https://github.com/dotnet/corefx/tree/master/src/System.Threading.Channels forniscono vari tipi di strutture di dati di buffering.  In questa fase non è necessario che il linguaggio sia occupato.
