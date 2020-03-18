---
ms.openlocfilehash: 9ed1aa75d581cecbf754a84d1f523c6334b8c0ac
ms.sourcegitcommit: 5278336b61519956240a6f7d83bcb4322019e032
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79485259"
---
# <a name="async-streams"></a><span data-ttu-id="6d2ca-101">Flussi asincroni</span><span class="sxs-lookup"><span data-stu-id="6d2ca-101">Async Streams</span></span>

* <span data-ttu-id="6d2ca-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="6d2ca-102">[x] Proposed</span></span>
* <span data-ttu-id="6d2ca-103">Prototipo [x]</span><span class="sxs-lookup"><span data-stu-id="6d2ca-103">[x] Prototype</span></span>
* <span data-ttu-id="6d2ca-104">[] Implementazione</span><span class="sxs-lookup"><span data-stu-id="6d2ca-104">[ ] Implementation</span></span>
* <span data-ttu-id="6d2ca-105">[] Specifica</span><span class="sxs-lookup"><span data-stu-id="6d2ca-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="6d2ca-106">Summary</span><span class="sxs-lookup"><span data-stu-id="6d2ca-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="6d2ca-107">C#dispone del supporto per metodi iteratori e metodi asincroni, ma non supporta un metodo che sia un iteratore che un metodo asincrono.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-107">C# has support for iterator methods and async methods, but no support for a method that is both an iterator and an async method.</span></span>  <span data-ttu-id="6d2ca-108">È necessario risolvere il problema consentendo l'uso di `await` in un nuovo modulo di `async` iteratore, uno che restituisce un `IAsyncEnumerable<T>` o `IAsyncEnumerator<T>` anziché un `IEnumerable<T>` o `IEnumerator<T>`, con `IAsyncEnumerable<T>` utilizzabile in un nuovo `await foreach`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-108">We should rectify this by allowing for `await` to be used in a new form of `async` iterator, one that returns an `IAsyncEnumerable<T>` or `IAsyncEnumerator<T>` rather than an `IEnumerable<T>` or `IEnumerator<T>`, with `IAsyncEnumerable<T>` consumable in a new `await foreach`.</span></span>  <span data-ttu-id="6d2ca-109">Per abilitare la pulizia asincrona viene inoltre utilizzata un'interfaccia `IAsyncDisposable`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-109">An `IAsyncDisposable` interface is also used to enable asynchronous cleanup.</span></span>

## <a name="related-discussion"></a><span data-ttu-id="6d2ca-110">Discussione correlata</span><span class="sxs-lookup"><span data-stu-id="6d2ca-110">Related discussion</span></span>
- https://github.com/dotnet/roslyn/issues/261
- https://github.com/dotnet/roslyn/issues/114

## <a name="detailed-design"></a><span data-ttu-id="6d2ca-111">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="6d2ca-111">Detailed design</span></span>
[design]: #detailed-design

## <a name="interfaces"></a><span data-ttu-id="6d2ca-112">Interfacce</span><span class="sxs-lookup"><span data-stu-id="6d2ca-112">Interfaces</span></span>

### <a name="iasyncdisposable"></a><span data-ttu-id="6d2ca-113">IAsyncDisposable</span><span class="sxs-lookup"><span data-stu-id="6d2ca-113">IAsyncDisposable</span></span>

<span data-ttu-id="6d2ca-114">C'è stata una discussione approfondita di `IAsyncDisposable`, ad esempio https://github.com/dotnet/roslyn/issues/114) e se si tratta di una buona idea.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-114">There has been much discussion of `IAsyncDisposable` (e.g. https://github.com/dotnet/roslyn/issues/114) and whether it's a good idea.</span></span>  <span data-ttu-id="6d2ca-115">Tuttavia, si tratta di un concetto necessario per aggiungere supporto per gli iteratori asincroni.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-115">However, it's a required concept to add in support of async iterators.</span></span>  <span data-ttu-id="6d2ca-116">Poiché i blocchi di `finally` possono contenere `await`s e poiché i blocchi di `finally` devono essere eseguiti come parte dell'eliminazione degli iteratori, è necessaria l'eliminazione asincrona.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-116">Since `finally` blocks may contain `await`s, and since `finally` blocks need to be run as part of disposing of iterators, we need async disposal.</span></span>  <span data-ttu-id="6d2ca-117">È anche di solito utile ogni volta che la pulizia delle risorse potrebbe richiedere un certo periodo di tempo, ad esempio la chiusura di file (che richiedono lo scaricamento), la deregistrazione dei callback e la possibilità di individuare il momento in cui la registrazione è stata completata e così via.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-117">It's also just generally useful any time cleaning up of resources might take any period of time, e.g. closing files (requiring flushes), deregistering callbacks and providing a way to know when deregistration has completed, etc.</span></span>

<span data-ttu-id="6d2ca-118">L'interfaccia seguente viene aggiunta alle librerie .NET di base (ad esempio System. private. CoreLib/System. Runtime):</span><span class="sxs-lookup"><span data-stu-id="6d2ca-118">The following interface is added to the core .NET libraries (e.g. System.Private.CoreLib / System.Runtime):</span></span>
```csharp
namespace System
{
    public interface IAsyncDisposable
    {
        ValueTask DisposeAsync();
    }
}
```
<span data-ttu-id="6d2ca-119">Come per `Dispose`, la chiamata di `DisposeAsync` più volte è accettabile e le chiamate successive dopo la prima devono essere considerate come pon, restituendo un'attività completata in modo sincrono (`DisposeAsync` non devono essere thread-safe, tuttavia, e non devono supportare la chiamata simultanea).</span><span class="sxs-lookup"><span data-stu-id="6d2ca-119">As with `Dispose`, invoking `DisposeAsync` multiple times is acceptable, and subsequent invocations after the first should be treated as nops, returning a synchronously completed successful task (`DisposeAsync` need not be thread-safe, though, and need not support concurrent invocation).</span></span>  <span data-ttu-id="6d2ca-120">Inoltre, i tipi possono implementare sia `IDisposable` che `IAsyncDisposable`e, in caso affermativo, è accettabile richiamarli `Dispose` e quindi `DisposeAsync` o viceversa, ma solo il primo deve essere significativo e le chiamate successive di devono essere di tipo NOP.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-120">Further, types may implement both `IDisposable` and `IAsyncDisposable`, and if they do, it's similarly acceptable to invoke `Dispose` and then `DisposeAsync` or vice versa, but only the first should be meaningful and subsequent invocations of either should be a nop.</span></span>  <span data-ttu-id="6d2ca-121">Di conseguenza, se un tipo implementa entrambi, è consigliabile che gli utenti chiamino una sola volta il metodo più pertinente in base al contesto, `Dispose` nei contesti sincroni e `DisposeAsync` in quelli asincroni.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-121">As such, if a type does implement both, consumers are encouraged to call once and only once the more relevant method based on the context, `Dispose` in synchronous contexts and `DisposeAsync` in asynchronous ones.</span></span>

<span data-ttu-id="6d2ca-122">(Sto lasciando una discussione sul modo in cui `IAsyncDisposable` interagisce con `using` a una discussione separata.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-122">(I'm leaving discussion of how `IAsyncDisposable` interacts with `using` to a separate discussion.</span></span>  <span data-ttu-id="6d2ca-123">E la copertura del modo in cui interagisce con `foreach` viene gestita più avanti in questa proposta.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-123">And coverage of how it interacts with `foreach` is handled later in this proposal.)</span></span>

<span data-ttu-id="6d2ca-124">Alternative considerate:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-124">Alternatives considered:</span></span>
- <span data-ttu-id="6d2ca-125">_`DisposeAsync` accettando una `CancellationToken`_ : in teoria, è sensato che qualsiasi asincrono possa essere annullata, l'eliminazione riguarda la pulizia, la chiusura, le risorse free'ing e così via, che in genere non è un elemento che deve essere annullato. la pulizia è ancora importante per il lavoro annullato.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-125">_`DisposeAsync` accepting a `CancellationToken`_: while in theory it makes sense that anything async can be canceled, disposal is about cleanup, closing things out, free'ing resources, etc., which is generally not something that should be canceled; cleanup is still important for work that's canceled.</span></span>  <span data-ttu-id="6d2ca-126">Lo stesso `CancellationToken` che ha causato l'annullamento del lavoro effettivo è in genere lo stesso token passato a `DisposeAsync`, rendendo `DisposeAsync` inutile perché l'annullamento del lavoro provocherebbe un NOP per `DisposeAsync`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-126">The same `CancellationToken` that caused the actual work to be canceled would typically be the same token passed to `DisposeAsync`, making `DisposeAsync` worthless because cancellation of the work would cause `DisposeAsync` to be a nop.</span></span>  <span data-ttu-id="6d2ca-127">Se un utente vuole evitare che venga bloccato in attesa di eliminazione, può evitare di aspettare il `ValueTask`risultante o attenderlo solo per un determinato periodo di tempo.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-127">If someone wants to avoid being blocked waiting for disposal, they can avoid waiting on the resulting `ValueTask`, or wait on it only for some period of time.</span></span>
- <span data-ttu-id="6d2ca-128">_`DisposeAsync` la restituzione di un `Task`_ : ora che non esiste una `ValueTask` generica e che può essere costruita da un `IValueTaskSource`, restituendo `ValueTask` da `DisposeAsync` è possibile riutilizzare un oggetto esistente come promessa che rappresenta il completamento asincrono finale di `DisposeAsync`, salvando un'allocazione `Task` nel caso in cui `DisposeAsync` venga completata in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-128">_`DisposeAsync` returning a `Task`_: Now that a non-generic `ValueTask` exists and can be constructed from an `IValueTaskSource`, returning `ValueTask` from `DisposeAsync` allows an existing object to be reused as the promise representing the eventual async completion of `DisposeAsync`, saving a `Task` allocation in the case where `DisposeAsync` completes asynchronously.</span></span>
- <span data-ttu-id="6d2ca-129">_Configurazione di `DisposeAsync` con un `bool continueOnCapturedContext` (`ConfigureAwait`)_ : Sebbene ci siano problemi correlati al modo in cui tale concetto viene esposto a `using`, `foreach`e altri costrutti di linguaggio che lo utilizzano, dal punto di vista dell'interfaccia non esegue alcuna `await`"e non è necessario configurare nulla... i consumer del `ValueTask` possono utilizzarlo tuttavia.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-129">_Configuring `DisposeAsync` with a `bool continueOnCapturedContext` (`ConfigureAwait`)_: While there may be issues related to how such a concept is exposed to `using`, `foreach`, and other language constructs that consume this, from an interface perspective it's not actually doing any `await`'ing and there's nothing to configure... consumers of the `ValueTask` can consume it however they wish.</span></span>
- <span data-ttu-id="6d2ca-130">_`IAsyncDisposable` ereditare `IDisposable`_ : poiché è necessario usare solo uno o l'altro, non ha senso forzare i tipi a implementare entrambi.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-130">_`IAsyncDisposable` inheriting `IDisposable`_:  Since only one or the other should be used, it doesn't make sense to force types to implement both.</span></span>
- <span data-ttu-id="6d2ca-131">_`IDisposableAsync` al posto di `IAsyncDisposable`_ : è stato seguito il nome che gli elementi o i tipi sono "Async something" mentre le operazioni sono "done Async", quindi i tipi hanno "Async" come prefisso e i metodi hanno "Async" come suffisso.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-131">_`IDisposableAsync` instead of `IAsyncDisposable`_: We've been following the naming that things/types are an "async something" whereas operations are "done async", so types have "Async" as a prefix and methods have "Async" as a suffix.</span></span>

### <a name="iasyncenumerable--iasyncenumerator"></a><span data-ttu-id="6d2ca-132">IAsyncEnumerable / IAsyncEnumerator</span><span class="sxs-lookup"><span data-stu-id="6d2ca-132">IAsyncEnumerable / IAsyncEnumerator</span></span>

<span data-ttu-id="6d2ca-133">Vengono aggiunte due interfacce alle librerie .NET di base:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-133">Two interfaces are added to the core .NET libraries:</span></span>

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

<span data-ttu-id="6d2ca-134">Il consumo tipico (senza funzionalità aggiuntive del linguaggio) avrà un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-134">Typical consumption (without additional language features) would look like:</span></span>

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

<span data-ttu-id="6d2ca-135">Opzioni scartate considerate:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-135">Discarded options considered:</span></span>
- <span data-ttu-id="6d2ca-136">_`Task<bool> MoveNextAsync(); T current { get; }`_ : l'utilizzo di `Task<bool>` supporta l'utilizzo di un oggetto attività memorizzato nella cache per rappresentare chiamate sincrone e `MoveNextAsync` di esito positivo, ma è comunque necessaria un'allocazione per il completamento asincrono.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-136">_`Task<bool> MoveNextAsync(); T current { get; }`_: Using `Task<bool>` would support using a cached task object to represent synchronous, successful `MoveNextAsync` calls, but an allocation would still be required for asynchronous completion.</span></span>  <span data-ttu-id="6d2ca-137">Restituendo `ValueTask<bool>`, l'oggetto enumeratore viene abilitato a se stesso per implementare `IValueTaskSource<bool>` ed essere utilizzato come supporto per il `ValueTask<bool>` restituito da `MoveNextAsync`, che a sua volta consente un sovraccarico significativamente ridotto.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-137">By returning `ValueTask<bool>`, we enable the enumerator object to itself implement `IValueTaskSource<bool>` and be used as the backing for the `ValueTask<bool>` returned from `MoveNextAsync`, which in turn allows for significantly reduced overheads.</span></span>
- <span data-ttu-id="6d2ca-138">_`ValueTask<(bool, T)> MoveNextAsync();`_ : non solo è più difficile da utilizzare, ma significa che `T` non può più essere covariante.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-138">_`ValueTask<(bool, T)> MoveNextAsync();`_: It's not only harder to consume, but it means that `T` can no longer be covariant.</span></span>
- <span data-ttu-id="6d2ca-139">_`ValueTask<T?> TryMoveNextAsync();`_ : non covariante.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-139">_`ValueTask<T?> TryMoveNextAsync();`_: Not covariant.</span></span>
- <span data-ttu-id="6d2ca-140">_`Task<T?> TryMoveNextAsync();`_ : non covariante, allocazioni a ogni chiamata e così via.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-140">_`Task<T?> TryMoveNextAsync();`_: Not covariant, allocations on every call, etc.</span></span>
- <span data-ttu-id="6d2ca-141">_`ITask<T?> TryMoveNextAsync();`_ : non covariante, allocazioni a ogni chiamata e così via.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-141">_`ITask<T?> TryMoveNextAsync();`_: Not covariant, allocations on every call, etc.</span></span>
- <span data-ttu-id="6d2ca-142">_`ITask<(bool,T)> TryMoveNextAsync();`_ : non covariante, allocazioni a ogni chiamata e così via.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-142">_`ITask<(bool,T)> TryMoveNextAsync();`_: Not covariant, allocations on every call, etc.</span></span>
- <span data-ttu-id="6d2ca-143">_`Task<bool> TryMoveNextAsync(out T result);`_ : è necessario impostare il risultato del `out` quando l'operazione viene restituita in modo sincrono, non quando l'attività viene completata in modo asincrono in futuro, quindi non sarà possibile comunicare il risultato.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-143">_`Task<bool> TryMoveNextAsync(out T result);`_: The `out` result would need to be set when the operation returns synchronously, not when it asynchronously completes the task potentially sometime long in the future, at which point there'd be no way to communicate the result.</span></span>
- <span data-ttu-id="6d2ca-144">_`IAsyncEnumerator<T>` non implementare `IAsyncDisposable`_ : è possibile scegliere di separarli.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-144">_`IAsyncEnumerator<T>` not implementing `IAsyncDisposable`_: We could choose to separate these.</span></span>  <span data-ttu-id="6d2ca-145">Tuttavia, in questo modo si complicano alcune altre aree della proposta, perché il codice deve essere in grado di gestire la possibilità che un enumeratore non fornisca l'eliminazione, rendendo difficile la scrittura di helper basati su modelli.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-145">However, doing so complicates certain other areas of the proposal, as code must then be able to deal with the possibility that an enumerator doesn't provide disposal, which makes it difficult to write pattern-based helpers.</span></span>  <span data-ttu-id="6d2ca-146">Inoltre, per gli enumeratori sarà necessario disporre di un'eliminazione (ad esempio, qualsiasi C# iteratore asincrono con un blocco finally, la maggior parte delle operazioni di enumerazione dei dati da una connessione di rete e così via) e, se non è possibile, è semplice implementare il metodo esclusivamente come `public ValueTask DisposeAsync() => default(ValueTask);` con un overhead aggiuntivo minimo.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-146">Further, it will be common for enumerators to have a need for disposal (e.g. any C# async iterator that has a finally block, most things enumerating data from a network connection, etc.), and if one doesn't, it is simple to implement the method purely as `public ValueTask DisposeAsync() => default(ValueTask);` with minimal additional overhead.</span></span>
- <span data-ttu-id="6d2ca-147">_ `IAsyncEnumerator<T> GetAsyncEnumerator()`: nessun parametro del token di annullamento.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-147">_ `IAsyncEnumerator<T> GetAsyncEnumerator()`: No cancellation token parameter.</span></span>

#### <a name="viable-alternative"></a><span data-ttu-id="6d2ca-148">Alternativa valida:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-148">Viable alternative:</span></span>

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

<span data-ttu-id="6d2ca-149">`TryGetNext` viene usato in un ciclo interno per utilizzare elementi con una singola chiamata di interfaccia, purché siano disponibili in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-149">`TryGetNext` is used in an inner loop to consume items with a single interface call as long as they're available synchronously.</span></span>  <span data-ttu-id="6d2ca-150">Quando l'elemento successivo non può essere recuperato in modo sincrono, restituisce false e ogni volta che restituisce false, un chiamante deve successivamente richiamare `WaitForNextAsync` per attendere che l'elemento successivo sia disponibile o per determinare che non sarà mai presente un altro elemento.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-150">When the next item can't be retrieved synchronously, it returns false, and any time it returns false, a caller must subsequently invoke `WaitForNextAsync` to either wait for the next item to be available or to determine that there will never be another item.</span></span> <span data-ttu-id="6d2ca-151">Il consumo tipico (senza funzionalità aggiuntive del linguaggio) avrà un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-151">Typical consumption (without additional language features) would look like:</span></span>

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

<span data-ttu-id="6d2ca-152">Il vantaggio è costituito da due riduzioni, una secondaria e una principale:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-152">The advantage of this is two-fold, one minor and one major:</span></span>
- <span data-ttu-id="6d2ca-153">_Minor: consente a un enumeratore di supportare più consumer_.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-153">_Minor: Allows for an enumerator to support multiple consumers_.</span></span> <span data-ttu-id="6d2ca-154">Potrebbero esserci scenari in cui è utile che un enumeratore supporti più consumer simultanei.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-154">There may be scenarios where it's valuable for an enumerator to support multiple concurrent consumers.</span></span>  <span data-ttu-id="6d2ca-155">Non è possibile ottenere questo risultato quando `MoveNextAsync` e `Current` sono separati in modo che un'implementazione non possa renderne l'utilizzo atomico.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-155">That can't be achieved when `MoveNextAsync` and `Current` are separate such that an implementation can't make their usage atomic.</span></span>  <span data-ttu-id="6d2ca-156">Questo approccio, invece, fornisce un solo metodo `TryGetNext` che supporta il push dell'enumeratore in avanti e il recupero dell'elemento successivo, in modo che l'enumeratore possa abilitare l'atomicità se lo si desidera.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-156">In contrast, this approach provides a single method `TryGetNext` that supports pushing the enumerator forward and getting the next item, so the enumerator can enable atomicity if desired.</span></span>  <span data-ttu-id="6d2ca-157">Tuttavia, è probabile che questi scenari possano essere abilitati anche fornendo a ogni consumer il proprio enumeratore da un enumerabile condiviso.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-157">However, it's likely that such scenarios could also be enabled by giving each consumer its own enumerator from a shared enumerable.</span></span>  <span data-ttu-id="6d2ca-158">Inoltre, non è necessario imporre che ogni enumeratore supporti l'utilizzo simultaneo, in quanto in questo modo si aggiungono sovraccarichi non banali alla maggior parte dei casi che non lo richiedono, il che significa che un consumer dell'interfaccia non è in genere in grado di basarsi su questo metodo.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-158">Further, we don't want to enforce that every enumerator support concurrent usage, as that would add non-trivial overheads to the majority case that doesn't require it, which means a consumer of the interface generally couldn't rely on this any way.</span></span>
- <span data-ttu-id="6d2ca-159">_Principale: prestazioni_.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-159">_Major: Performance_.</span></span> <span data-ttu-id="6d2ca-160">L'approccio `MoveNextAsync`/`Current` richiede due chiamate di interfaccia per operazione, mentre il caso migliore per `WaitForNextAsync`/`TryGetNext` è che la maggior parte delle iterazioni viene completata in modo sincrono, consentendo un ciclo interno stretto con `TryGetNext`, in modo da avere una sola chiamata di interfaccia per ogni operazione.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-160">The `MoveNextAsync`/`Current` approach requires two interface calls per operation, whereas the best case for `WaitForNextAsync`/`TryGetNext` is that most iterations complete synchronously, enabling a tight inner loop with `TryGetNext`, such that we only have one interface call per operation.</span></span>  <span data-ttu-id="6d2ca-161">Questo può avere un effetto misurabile nelle situazioni in cui le chiamate di interfaccia dominano il calcolo.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-161">This can have a measurable impact in situations where the interface calls dominate the computation.</span></span>

<span data-ttu-id="6d2ca-162">Tuttavia, esistono svantaggi non banali, tra cui una maggiore complessità quando questi vengono utilizzati manualmente e una maggiore probabilità di introdurre bug quando vengono utilizzati.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-162">However, there are non-trivial downsides, including significantly increased complexity when consuming these manually, and an increased chance of introducing bugs when using them.</span></span>  <span data-ttu-id="6d2ca-163">Mentre i vantaggi in merito alle prestazioni vengono visualizzati in microbenchmark, non ci riteniamo che saranno interessati dalla maggior parte dell'utilizzo reale.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-163">And while the performance benefits show up in microbenchmarks, we don't believe they'll be impactful in the vast majority of real usage.</span></span>  <span data-ttu-id="6d2ca-164">Se si scopre che sono, è possibile introdurre un secondo set di interfacce in modo leggero.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-164">If it turns out they are, we can introduce a second set of interfaces in a light-up fashion.</span></span>

<span data-ttu-id="6d2ca-165">Opzioni scartate considerate:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-165">Discarded options considered:</span></span>
- <span data-ttu-id="6d2ca-166">`ValueTask<bool> WaitForNextAsync(); bool TryGetNext(out T result);`: i parametri di `out` non possono essere covarianti.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-166">`ValueTask<bool> WaitForNextAsync(); bool TryGetNext(out T result);`: `out` parameters can't be covariant.</span></span>  <span data-ttu-id="6d2ca-167">È anche presente un piccolo effetto (un problema con il modello try in generale) che probabilmente comporta una barriera di scrittura di runtime per i risultati del tipo di riferimento.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-167">There's also a small impact here (an issue with the try pattern in general) that this likely incurs a runtime write barrier for reference type results.</span></span>

#### <a name="cancellation"></a><span data-ttu-id="6d2ca-168">Annullamento</span><span class="sxs-lookup"><span data-stu-id="6d2ca-168">Cancellation</span></span>

<span data-ttu-id="6d2ca-169">Esistono diversi approcci possibili per supportare l'annullamento:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-169">There are several possible approaches to supporting cancellation:</span></span>
1. <span data-ttu-id="6d2ca-170">`IAsyncEnumerable<T>`/`IAsyncEnumerator<T>` sono indipendenti dall'annullamento: `CancellationToken` non viene visualizzato in un punto qualsiasi.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-170">`IAsyncEnumerable<T>`/`IAsyncEnumerator<T>` are cancellation-agnostic: `CancellationToken` doesn't appear anywhere.</span></span>  <span data-ttu-id="6d2ca-171">L'annullamento viene ottenuto tramite la cottura logica del `CancellationToken` nell'enumerabile e/o nell'enumeratore nel modo appropriato, ad esempio quando si chiama un iteratore, passando l'`CancellationToken` come argomento al metodo iteratore e utilizzandolo nel corpo dell'iteratore, come avviene con qualsiasi altro parametro.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-171">Cancellation is achieved by logically baking the `CancellationToken` into the enumerable and/or enumerator in whatever manner is appropriate, e.g. when calling an iterator, passing the `CancellationToken` as an argument to the iterator method and using it in the body of the iterator, as is done with any other parameter.</span></span>
2. <span data-ttu-id="6d2ca-172">`IAsyncEnumerator<T>.GetAsyncEnumerator(CancellationToken)`: si passa una `CancellationToken` a `GetAsyncEnumerator`e le successive operazioni di `MoveNextAsync` lo rispettano, ma è possibile.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-172">`IAsyncEnumerator<T>.GetAsyncEnumerator(CancellationToken)`: You pass a `CancellationToken` to `GetAsyncEnumerator`, and subsequent `MoveNextAsync` operations respect it however it can.</span></span>
3. <span data-ttu-id="6d2ca-173">`IAsyncEnumerator<T>.MoveNextAsync(CancellationToken)`: si passa una `CancellationToken` a ogni singola chiamata di `MoveNextAsync`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-173">`IAsyncEnumerator<T>.MoveNextAsync(CancellationToken)`: You pass a `CancellationToken` to each individual `MoveNextAsync` call.</span></span>
4. <span data-ttu-id="6d2ca-174">1 & & 2: incorporare `CancellationToken`s nell'enumeratore/enumeratore e passare `CancellationToken`s in `GetAsyncEnumerator`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-174">1 && 2: You both embed `CancellationToken`s into your enumerable/enumerator and pass `CancellationToken`s into `GetAsyncEnumerator`.</span></span>
5. <span data-ttu-id="6d2ca-175">1 & & 3: incorporare `CancellationToken`s nell'enumeratore/enumeratore e passare `CancellationToken`s in `MoveNextAsync`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-175">1 && 3: You both embed `CancellationToken`s into your enumerable/enumerator and pass `CancellationToken`s into `MoveNextAsync`.</span></span>

<span data-ttu-id="6d2ca-176">Dal punto di vista puramente teorico, (5) è il più affidabile, in quanto (a) `MoveNextAsync` accettare una `CancellationToken` consente il controllo più granulare sugli elementi annullati e (b) `CancellationToken` è solo qualsiasi altro tipo che può essere passato come argomento negli iteratori, incorporati in tipi arbitrari e così via.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-176">From a purely theoretical perspective, (5) is the most robust, in that (a) `MoveNextAsync` accepting a `CancellationToken` enables the most fine-grained control over what's canceled, and (b) `CancellationToken` is just any other type that can passed as an argument into iterators, embedded in arbitrary types, etc.</span></span>

<span data-ttu-id="6d2ca-177">Tuttavia, esistono diversi problemi con questo approccio:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-177">However, there are multiple problems with that approach:</span></span>
- <span data-ttu-id="6d2ca-178">In che modo un `CancellationToken` passato a `GetAsyncEnumerator` impostarlo nel corpo dell'iteratore?</span><span class="sxs-lookup"><span data-stu-id="6d2ca-178">How does a `CancellationToken` passed to `GetAsyncEnumerator` make it into the body of the iterator?</span></span>  <span data-ttu-id="6d2ca-179">È possibile esporre una nuova parola chiave `iterator` a cui è possibile fare il punto per accedere al `CancellationToken` passato al `GetEnumerator`. Tuttavia, a) si tratta di una grande quantità di macchinari aggiuntivi, b) che si tratta di un cittadino di prima classe e c) il 99% caso sembrerebbe lo stesso codice che chiama un iteratore e chiama `GetAsyncEnumerator` su di esso, nel qual caso può semplicemente passare la `CancellationToken` come argomento nel metodo.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-179">We could expose a new `iterator` keyword that you could dot off of to get access to the `CancellationToken` passed to `GetEnumerator`, but a) that's a lot of additional machinery, b) we're making it a very first-class citizen, and c) the 99% case would seem to be the same code both calling an iterator and calling `GetAsyncEnumerator` on it, in which case it can just pass the `CancellationToken` as an argument into the method.</span></span>
- <span data-ttu-id="6d2ca-180">In che modo un `CancellationToken` passato a `MoveNextAsync` accedere al corpo del metodo?</span><span class="sxs-lookup"><span data-stu-id="6d2ca-180">How does a `CancellationToken` passed to `MoveNextAsync` get into the body of the method?</span></span>  <span data-ttu-id="6d2ca-181">Questo è ancora peggio, come se venisse esposto da un `iterator` oggetto locale, il suo valore potrebbe cambiare tra gli await, il che significa che il codice registrato con il token deve annullare la registrazione prima di await e quindi ripetere la registrazione dopo; è anche potenzialmente costoso dover eseguire la registrazione e l'annullamento della registrazione in ogni chiamata di `MoveNextAsync`, indipendentemente dal fatto che sia implementato dal compilatore in un iteratore o da uno sviluppatore manualmente.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-181">This is even worse, as if it's exposed off of an `iterator` local object, its value could change across awaits, which means any code that registered with the token would need to unregister from it prior to awaits and then re-register after; it's also potentially quite expensive to need to do such registering and unregistering in every `MoveNextAsync` call, regardless of whether implemented by the compiler in an iterator or by a developer manually.</span></span>
- <span data-ttu-id="6d2ca-182">In che modo uno sviluppatore Annulla un ciclo `foreach`?</span><span class="sxs-lookup"><span data-stu-id="6d2ca-182">How does a developer cancel a `foreach` loop?</span></span>  <span data-ttu-id="6d2ca-183">Se questa operazione viene eseguita assegnando a un `CancellationToken` a un enumeratore/enumeratore, un oggetto) è necessario supportare `foreach`' ing over enumeratori, che li eleva a essere cittadini di prima classe e ora è necessario iniziare a pensare a un ecosistema costruito intorno agli enumeratori (ad esempio metodi LINQ) o b) è necessario incorporare il `CancellationToken` nell'enumerabile in modo da disporre di un `WithCancellation` metodo di estensione disattivato `IAsyncEnumerable<T>` che archivia il token fornito e quindi passarlo all'`GetAsyncEnumerator` enumerabile di cui è stato eseguito il wrapped quando il `GetAsyncEnumerator` nello struct viene richiamato (ignorando tale token).</span><span class="sxs-lookup"><span data-stu-id="6d2ca-183">If it's done by giving a `CancellationToken` to an enumerable/enumerator, then either a) we need to support `foreach`'ing over enumerators, which raises them to being first-class citizens, and now you need to start thinking about an ecosystem built up around enumerators (e.g. LINQ methods) or b) we need to embed the `CancellationToken` in the enumerable anyway by having some `WithCancellation` extension method off of `IAsyncEnumerable<T>` that would store the provided token and then pass it into  the wrapped enumerable's `GetAsyncEnumerator` when the `GetAsyncEnumerator` on the returned struct is invoked (ignoring that token).</span></span>  <span data-ttu-id="6d2ca-184">In alternativa, è possibile usare solo il `CancellationToken` nel corpo del ciclo foreach.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-184">Or, you can just use the `CancellationToken` you have in the body of the foreach.</span></span>
- <span data-ttu-id="6d2ca-185">Se/quando sono supportati i genericità delle query, in che modo il `CancellationToken` fornito per `GetEnumerator` o `MoveNextAsync` essere passato a ogni clausola?</span><span class="sxs-lookup"><span data-stu-id="6d2ca-185">If/when query comprehensions are supported, how would the `CancellationToken` supplied to `GetEnumerator` or `MoveNextAsync` be passed into each clause?</span></span>  <span data-ttu-id="6d2ca-186">Il modo più semplice è semplicemente la clausola per la relativa acquisizione. a quel punto, qualsiasi token viene passato a `GetAsyncEnumerator`/`MoveNextAsync` viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-186">The easiest way would simply be for the clause to capture it, at which point whatever token is passed to `GetAsyncEnumerator`/`MoveNextAsync` is ignored.</span></span>

<span data-ttu-id="6d2ca-187">È stata consigliata una versione precedente di questo documento (1), ma è stato passato a (4).</span><span class="sxs-lookup"><span data-stu-id="6d2ca-187">An earlier version of this document recommended (1), but we since switched to (4).</span></span>

<span data-ttu-id="6d2ca-188">I due problemi principali con (1):</span><span class="sxs-lookup"><span data-stu-id="6d2ca-188">The two main problems with (1):</span></span>
- <span data-ttu-id="6d2ca-189">i produttori di Enumerable annullabili devono implementare alcuni standard e possono sfruttare il supporto del compilatore solo per gli iteratori asincroni per implementare un metodo `IAsyncEnumerator<T> GetAsyncEnumerator(CancellationToken)`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-189">producers of cancellable enumerables have to implement some boilerplate, and can only leverage the compiler's support for async-iterators to implement a `IAsyncEnumerator<T> GetAsyncEnumerator(CancellationToken)` method.</span></span>
- <span data-ttu-id="6d2ca-190">è probabile che molti produttori debbano semplicemente aggiungere un parametro di `CancellationToken` alla propria firma enumerabile asincrona, in modo da impedire ai consumer di passare il token di annullamento desiderato quando viene assegnato un tipo di `IAsyncEnumerable`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-190">it is likely that many producers would be tempted to just add a `CancellationToken` parameter to their async-enumerable signature instead, which will prevent consumers from passing the cancellation token they want when they are given an `IAsyncEnumerable` type.</span></span>

<span data-ttu-id="6d2ca-191">Esistono due scenari principali di utilizzo:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-191">There are two main consumption scenarios:</span></span>
1. <span data-ttu-id="6d2ca-192">`await foreach (var i in GetData(token)) ...` in cui il consumer chiama il metodo async-iterator,</span><span class="sxs-lookup"><span data-stu-id="6d2ca-192">`await foreach (var i in GetData(token)) ...` where the consumer calls the async-iterator method,</span></span>
2. <span data-ttu-id="6d2ca-193">`await foreach (var i in givenIAsyncEnumerable.WithCancellation(token)) ...` in cui il consumer gestisce un'istanza di `IAsyncEnumerable` specificata.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-193">`await foreach (var i in givenIAsyncEnumerable.WithCancellation(token)) ...` where the consumer deals with a given `IAsyncEnumerable` instance.</span></span>

<span data-ttu-id="6d2ca-194">Si ritiene che un compromesso ragionevole per supportare entrambi gli scenari in modo che sia utile per i producer e i consumer di flussi asincroni consiste nell'usare un parametro con annotazioni specifico nel metodo asincrono-iteratore.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-194">We find that a reasonable compromise to support both scenarios in a way that is convenient for both producers and consumers of async-streams is to use a specially annotated parameter in the async-iterator method.</span></span> <span data-ttu-id="6d2ca-195">A questo scopo, viene usato l'attributo `[EnumeratorCancellation]`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-195">The `[EnumeratorCancellation]` attribute is used for this purpose.</span></span> <span data-ttu-id="6d2ca-196">L'inserimento di questo attributo su un parametro indica al compilatore che se un token viene passato al metodo `GetAsyncEnumerator`, è necessario utilizzare tale token al posto del valore passato originariamente per il parametro.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-196">Placing this attribute on a parameter tells the compiler that if a token is passed to the `GetAsyncEnumerator` method, that token should be used instead of the value originally passed for the parameter.</span></span>

<span data-ttu-id="6d2ca-197">Considerare `IAsyncEnumerable<int> GetData([EnumeratorCancellation] CancellationToken token = default)`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-197">Consider `IAsyncEnumerable<int> GetData([EnumeratorCancellation] CancellationToken token = default)`.</span></span> <span data-ttu-id="6d2ca-198">L'implementatore di questo metodo può semplicemente utilizzare il parametro nel corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-198">The implementer of this method can simply use the parameter in the method body.</span></span> <span data-ttu-id="6d2ca-199">Il consumer può usare i modelli di consumo indicati in precedenza:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-199">The consumer can use either consumption patterns above:</span></span>
1. <span data-ttu-id="6d2ca-200">Se si usa `GetData(token)`, il token viene salvato in Async-Enumerable e verrà usato nell'iterazione.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-200">if you use `GetData(token)`, then the token is saved into the async-enumerable and will be used in iteration,</span></span>
2. <span data-ttu-id="6d2ca-201">Se si usa `givenIAsyncEnumerable.WithCancellation(token)`, il token passato a `GetAsyncEnumerator` sostituirà tutti i token salvati in Async-Enumerable.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-201">if you use `givenIAsyncEnumerable.WithCancellation(token)`, then the token passed to `GetAsyncEnumerator` will supersede any token saved in the async-enumerable.</span></span>

## <a name="foreach"></a><span data-ttu-id="6d2ca-202">foreach</span><span class="sxs-lookup"><span data-stu-id="6d2ca-202">foreach</span></span>

<span data-ttu-id="6d2ca-203">`foreach` sarà aumentata per supportare `IAsyncEnumerable<T>` oltre al supporto esistente per `IEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-203">`foreach` will be augmented to support `IAsyncEnumerable<T>` in addition to its existing support for `IEnumerable<T>`.</span></span>  <span data-ttu-id="6d2ca-204">Inoltre, supporterà l'equivalente di `IAsyncEnumerable<T>` come modello se i membri pertinenti sono esposti pubblicamente, eseguendo il fallback all'utilizzo diretto dell'interfaccia in caso contrario, per abilitare le estensioni basate su struct che evitano l'allocazione e l'utilizzo di awaitable alternativi come tipo restituito di `MoveNextAsync` e `DisposeAsync`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-204">And it will support the equivalent of `IAsyncEnumerable<T>` as a pattern if the relevant members are exposed publicly, falling back to using the interface directly if not, in order to enable struct-based extensions that avoid allocating as well as using alternative awaitables as the return type of `MoveNextAsync` and `DisposeAsync`.</span></span>

### <a name="syntax"></a><span data-ttu-id="6d2ca-205">Sintassi</span><span class="sxs-lookup"><span data-stu-id="6d2ca-205">Syntax</span></span>

<span data-ttu-id="6d2ca-206">Utilizzando la sintassi:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-206">Using the syntax:</span></span>

```csharp
foreach (var i in enumerable)
```

<span data-ttu-id="6d2ca-207">C#continuerà a considerare `enumerable` come un enumerabile sincrono, in modo che anche se espone le API appropriate per gli Enumerable asincroni (esponendo il modello o implementando l'interfaccia), considererà solo le API sincrone.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-207">C# will continue to treat `enumerable` as a synchronous enumerable, such that even if it exposes the relevant APIs for async enumerables (exposing the pattern or implementing the interface), it will only consider the synchronous APIs.</span></span>

<span data-ttu-id="6d2ca-208">Per forzare la `foreach` al invece di considerare solo le API asincrone, `await` viene inserito come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-208">To force `foreach` to instead only consider the asynchronous APIs, `await` is inserted as follows:</span></span>

```csharp
await foreach (var i in enumerable)
```

<span data-ttu-id="6d2ca-209">Non viene fornita alcuna sintassi che supporti l'uso delle API Async o Sync. lo sviluppatore deve scegliere in base alla sintassi utilizzata.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-209">No syntax would be provided that would support using either the async or the sync APIs; the developer must choose based on the syntax used.</span></span>

<span data-ttu-id="6d2ca-210">Opzioni scartate considerate:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-210">Discarded options considered:</span></span>
- <span data-ttu-id="6d2ca-211">_`foreach (var i in await enumerable)`_ : questa è già una sintassi valida e la modifica del suo significato è una modifica di rilievo.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-211">_`foreach (var i in await enumerable)`_: This is already valid syntax, and changing its meaning would be a breaking change.</span></span>  <span data-ttu-id="6d2ca-212">Ciò significa `await` il `enumerable`, ottenere un elemento iterable in modo sincrono e quindi eseguire l'iterazione in modo sincrono.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-212">This means to `await` the `enumerable`, get back something synchronously iterable from it, and then synchronously iterate through that.</span></span>
- <span data-ttu-id="6d2ca-213">_`foreach (var i await in enumerable)`, `foreach (var await i in enumerable)`, `foreach (await var i in enumerable)`_ : questi tutti indicano che è in attesa dell'elemento successivo, ma sono presenti altre attese in foreach, in particolare se l'enumerabile è un `IAsyncDisposable`, si `await`l'eliminazione asincrona.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-213">_`foreach (var i await in enumerable)`, `foreach (var await i in enumerable)`, `foreach (await var i in enumerable)`_: These all suggest that we're awaiting the next item, but there are other awaits involved in foreach, in particular if the enumerable is an `IAsyncDisposable`, we will be `await`'ing its async disposal.</span></span>  <span data-ttu-id="6d2ca-214">Questo await è come ambito di foreach anziché per ogni singolo elemento, quindi la parola chiave `await` merita di essere a livello di `foreach`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-214">That await is as the scope of the foreach rather than for each individual element, and thus the `await` keyword deserves to be at the `foreach` level.</span></span>  <span data-ttu-id="6d2ca-215">Inoltre, il fatto che sia associato al `foreach` ci consente di descrivere il `foreach` con un termine diverso, ad esempio "await foreach".</span><span class="sxs-lookup"><span data-stu-id="6d2ca-215">Further, having it associated with the `foreach` gives us a way to describe the `foreach` with a different term, e.g. a "await foreach".</span></span>  <span data-ttu-id="6d2ca-216">Tuttavia, ancora più importante, è importante considerare `foreach` sintassi allo stesso tempo della sintassi `using`, in modo che rimangano coerenti tra loro e `using (await ...)` sia già una sintassi valida.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-216">But more importantly, there's value in considering `foreach` syntax at the same time as `using` syntax, so that they remain consistent with each other, and `using (await ...)` is already valid syntax.</span></span>
- _`foreach await (var i in enumerable)`_

<span data-ttu-id="6d2ca-217">Tenere comunque presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-217">Still to consider:</span></span>
- <span data-ttu-id="6d2ca-218">`foreach` attualmente non supporta l'iterazione in un enumeratore.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-218">`foreach` today does not support iterating through an enumerator.</span></span>  <span data-ttu-id="6d2ca-219">Si prevede che sia più comune avere `IAsyncEnumerator<T>`e che quindi si sta tentando di supportare `await foreach` con `IAsyncEnumerable<T>` e `IAsyncEnumerator<T>`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-219">We expect it will be more common to have `IAsyncEnumerator<T>`s handed around, and thus it's tempting to support `await foreach` with both `IAsyncEnumerable<T>` and `IAsyncEnumerator<T>`.</span></span>  <span data-ttu-id="6d2ca-220">Tuttavia, una volta aggiunto tale supporto, viene introdotta la questione se `IAsyncEnumerator<T>` è un cittadino di prima classe e se è necessario disporre di overload di combinatori che operano su enumeratori in aggiunta a enumeratori?</span><span class="sxs-lookup"><span data-stu-id="6d2ca-220">But once we add such support, it introduces the question of whether `IAsyncEnumerator<T>` is a first-class citizen, and whether we need to have overloads of combinators that operate on enumerators in addition to enumerables?</span></span>    <span data-ttu-id="6d2ca-221">Si vuole incoraggiare i metodi per restituire gli enumeratori anziché gli enumerabili?</span><span class="sxs-lookup"><span data-stu-id="6d2ca-221">Do we want to encourage methods to return enumerators rather than enumerables?</span></span> <span data-ttu-id="6d2ca-222">È necessario continuare a illustrare questo problema.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-222">We should continue to discuss this.</span></span>  <span data-ttu-id="6d2ca-223">Se si decide di non supportarlo, potrebbe essere necessario introdurre un metodo di estensione `public static IAsyncEnumerable<T> AsEnumerable<T>(this IAsyncEnumerator<T> enumerator);` che consentirebbe a un enumeratore di continuare a `foreach`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-223">If we decide we don't want to support it, we might want to introduce an extension method `public static IAsyncEnumerable<T> AsEnumerable<T>(this IAsyncEnumerator<T> enumerator);` that would allow an enumerator to still be `foreach`'d.</span></span>  <span data-ttu-id="6d2ca-224">Se si decide di supportarla, è necessario decidere se la `await foreach` è responsabile della chiamata `DisposeAsync` sull'enumeratore e la risposta è probabilmente "No, il controllo sull'eliminazione deve essere gestito da chiunque abbia chiamato `GetEnumerator`".</span><span class="sxs-lookup"><span data-stu-id="6d2ca-224">If we decide we do want to support it, we'll need to also decide on whether the `await foreach` would be responsible for calling `DisposeAsync` on the enumerator, and the answer is likely "no, control over disposal should be handled by whoever called `GetEnumerator`."</span></span>

### <a name="pattern-based-compilation"></a><span data-ttu-id="6d2ca-225">Compilazione basata su modelli</span><span class="sxs-lookup"><span data-stu-id="6d2ca-225">Pattern-based Compilation</span></span>

<span data-ttu-id="6d2ca-226">Il compilatore eseguirà l'associazione alle API basate su modelli, se esistenti, preferendole tramite l'interfaccia (il modello può essere soddisfatto con i metodi di istanza o i metodi di estensione).</span><span class="sxs-lookup"><span data-stu-id="6d2ca-226">The compiler will bind to the pattern-based APIs if they exist, preferring those over using the interface (the pattern may be satisfied with instance methods or extension methods).</span></span>  <span data-ttu-id="6d2ca-227">I requisiti per il modello sono:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-227">The requirements for the pattern are:</span></span>
- <span data-ttu-id="6d2ca-228">L'enumerabile deve esporre un `GetAsyncEnumerator` metodo che può essere chiamato senza argomenti e che restituisce un enumeratore che soddisfa il modello pertinente.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-228">The enumerable must expose a `GetAsyncEnumerator` method that may be called with no arguments and that returns an enumerator that meets the relevant pattern.</span></span>
- <span data-ttu-id="6d2ca-229">L'enumeratore deve esporre un `MoveNextAsync` metodo che può essere chiamato senza argomenti e che restituisce un elemento che può essere `await`ed e il cui `GetResult()` restituisce un `bool`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-229">The enumerator must expose a `MoveNextAsync` method that may be called with no arguments and that returns something which may be `await`ed and whose `GetResult()` returns a `bool`.</span></span>
- <span data-ttu-id="6d2ca-230">L'enumeratore deve inoltre esporre `Current` proprietà il cui Getter restituisce un `T` che rappresenta il tipo di dati enumerati.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-230">The enumerator must also expose `Current` property whose getter returns a `T` representing the kind of data being enumerated.</span></span>
- <span data-ttu-id="6d2ca-231">L'enumeratore può facoltativamente esporre un `DisposeAsync` metodo che può essere richiamato senza argomenti e che restituisce un elemento che può essere `await`ed e il cui `GetResult()` restituisce `void`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-231">The enumerator may optionally expose a `DisposeAsync` method that may be invoked with no arguments and that returns something that can be `await`ed and whose `GetResult()` returns `void`.</span></span>

<span data-ttu-id="6d2ca-232">Questo codice:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-232">This code:</span></span>

```csharp
var enumerable = ...;
await foreach (T item in enumerable)
{
   ...
}
```

<span data-ttu-id="6d2ca-233">viene convertito in equivalente a:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-233">is translated to the equivalent of:</span></span>

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

<span data-ttu-id="6d2ca-234">Se il tipo iterato non espone il modello corretto, verranno usate le interfacce.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-234">If the iterated type doesn't expose the right pattern, the interfaces will be used.</span></span>

### <a name="configureawait"></a><span data-ttu-id="6d2ca-235">ConfigureAwait</span><span class="sxs-lookup"><span data-stu-id="6d2ca-235">ConfigureAwait</span></span>

<span data-ttu-id="6d2ca-236">Questa compilazione basata su modelli consentirà l'uso di `ConfigureAwait` in tutti gli await, tramite un metodo di estensione `ConfigureAwait`:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-236">This pattern-based compilation will allow `ConfigureAwait` to be used on all of the awaits, via a `ConfigureAwait` extension method:</span></span>

```csharp
await foreach (T item in enumerable.ConfigureAwait(false))
{
   ...
}
```

<span data-ttu-id="6d2ca-237">Questa operazione sarà basata sui tipi che si aggiungeranno anche a .NET, probabilmente a System. Threading. Tasks. Extensions. dll:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-237">This will be based on types we'll add to .NET as well, likely to System.Threading.Tasks.Extensions.dll:</span></span>

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

<span data-ttu-id="6d2ca-238">Si noti che questo approccio non consentirà l'uso di `ConfigureAwait` con Enumerable basati su pattern, tuttavia, anche in questo caso, il `ConfigureAwait` viene esposto solo come estensione `Task`/`Task<T>`/`ValueTask`/e non può essere applicato a elementi awaitable arbitrari, perché è opportuno solo quando viene applicato alle attività (controlla un comportamento implementato nel supporto di continuazione dell'attività) e pertanto non ha senso quando si usa un modello in cui le attività awaitable potrebbero non essere attività.`ValueTask<T>`</span><span class="sxs-lookup"><span data-stu-id="6d2ca-238">Note that this approach will not enable `ConfigureAwait` to be used with pattern-based enumerables, but then again it's already the case that the `ConfigureAwait` is only exposed as an extension on `Task`/`Task<T>`/`ValueTask`/`ValueTask<T>` and can't be applied to arbitrary awaitable things, as it only makes sense when applied to Tasks (it controls a behavior implemented in Task's continuation support), and thus doesn't make sense when using a pattern where the awaitable things may not be tasks.</span></span>  <span data-ttu-id="6d2ca-239">Tutti gli utenti che restituiscono elementi awaitable possono fornire un comportamento personalizzato in scenari avanzati.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-239">Anyone returning awaitable things can provide their own custom behavior in such advanced scenarios.</span></span>

<span data-ttu-id="6d2ca-240">Se è possibile trovare un modo per supportare una soluzione di `ConfigureAwait` a livello di ambito o di assembly, questa operazione non è necessaria.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-240">(If we can come up with some way to support a scope- or assembly-level `ConfigureAwait` solution, then this won't be necessary.)</span></span>

## <a name="async-iterators"></a><span data-ttu-id="6d2ca-241">Iteratori asincroni</span><span class="sxs-lookup"><span data-stu-id="6d2ca-241">Async Iterators</span></span>

<span data-ttu-id="6d2ca-242">Il linguaggio/compilatore supporterà la produzione di `IAsyncEnumerable<T>`s e `IAsyncEnumerator<T>`s oltre a utilizzarli.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-242">The language / compiler will support producing `IAsyncEnumerable<T>`s and `IAsyncEnumerator<T>`s in addition to consuming them.</span></span> <span data-ttu-id="6d2ca-243">Attualmente il linguaggio supporta la scrittura di un iteratore come:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-243">Today the language supports writing an iterator like:</span></span>

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

<span data-ttu-id="6d2ca-244">non è tuttavia possibile usare `await` nel corpo di questi iteratori.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-244">but `await` can't be used in the body of these iterators.</span></span>  <span data-ttu-id="6d2ca-245">Il supporto viene aggiunto.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-245">We will add that support.</span></span>

### <a name="syntax"></a><span data-ttu-id="6d2ca-246">Sintassi</span><span class="sxs-lookup"><span data-stu-id="6d2ca-246">Syntax</span></span>

<span data-ttu-id="6d2ca-247">Il supporto del linguaggio esistente per gli iteratori deduce la natura dell'iteratore del metodo a seconda che contenga una o più `yield`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-247">The existing language support for iterators infers the iterator nature of the method based on whether it contains any `yield`s.</span></span>  <span data-ttu-id="6d2ca-248">Lo stesso valore sarà vero per gli iteratori asincroni.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-248">The same will be true for async iterators.</span></span>  <span data-ttu-id="6d2ca-249">Tali iteratori asincroni saranno delimitati e differenziati dagli iteratori sincroni tramite l'aggiunta di `async` alla firma e devono avere anche `IAsyncEnumerable<T>` o `IAsyncEnumerator<T>` come tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-249">Such async iterators will be demarcated and differentiated from synchronous iterators via adding `async` to the signature, and must then also have either `IAsyncEnumerable<T>` or `IAsyncEnumerator<T>` as its return type.</span></span>  <span data-ttu-id="6d2ca-250">Ad esempio, l'esempio precedente potrebbe essere scritto come iteratore asincrono, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-250">For example, the above example could be written as an async iterator as follows:</span></span>

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

<span data-ttu-id="6d2ca-251">Alternative considerate:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-251">Alternatives considered:</span></span>
- <span data-ttu-id="6d2ca-252">_Non usare `async` nella firma_: l'uso di `async` è probabilmente tecnicamente richiesto dal compilatore, perché lo usa per determinare se `await` è valido in tale contesto.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-252">_Not using `async` in the signature_: Using `async` is likely technically required by the compiler, as it uses it to determine whether `await` is valid in that context.</span></span>  <span data-ttu-id="6d2ca-253">Tuttavia, anche se non è obbligatorio, abbiamo stabilito che `await` può essere usato solo nei metodi contrassegnati come `async`e sembra importante mantenerne la coerenza.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-253">But even if it's not required, we've established that `await` may only be used in methods marked as `async`, and it seems important to keep the consistency.</span></span>
- <span data-ttu-id="6d2ca-254">L' _Abilitazione di compilatori personalizzati per `IAsyncEnumerable<T>`_ : si tratta di un elemento che è possibile esaminare per il futuro, ma il meccanismo è complesso e non è supportato per le controparti sincrone.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-254">_Enabling custom builders for `IAsyncEnumerable<T>`_:  That's something we could look at for the future, but the machinery is complicated and we don't support that for the synchronous counterparts.</span></span>
- <span data-ttu-id="6d2ca-255">_La presenza di una parola chiave `iterator` nella firma: gli_iteratori asincroni utilizzeranno `async iterator` nella firma e `yield` potevano essere utilizzati solo nei metodi `async` che includevano `iterator`; `iterator` viene quindi reso facoltativo negli iteratori sincroni.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-255">_Having an `iterator` keyword in the signature_: Async iterators would use `async iterator` in the signature, and `yield` could only be used in `async` methods that included `iterator`; `iterator` would then be made optional on synchronous iterators.</span></span>  <span data-ttu-id="6d2ca-256">A seconda della prospettiva, questo ha il vantaggio di renderlo molto chiaro dalla firma del metodo, indipendentemente dal fatto che `yield` sia consentito e dal fatto che il metodo restituisca istanze di tipo `IAsyncEnumerable<T>` invece che la produzione del compilatore a seconda che il codice usi o meno `yield`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-256">Depending on your perspective, this has the benefit of making it very clear by the signature of the method whether `yield` is allowed and whether the method is actually meant to return instances of type `IAsyncEnumerable<T>` rather than the compiler manufacturing one based on whether the code uses `yield` or not.</span></span>  <span data-ttu-id="6d2ca-257">Ma è diverso dagli iteratori sincroni, che non possono essere eseguiti per richiederne uno.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-257">But it is different from synchronous iterators, which don't and can't be made to require one.</span></span>  <span data-ttu-id="6d2ca-258">Inoltre, alcuni sviluppatori non amano la sintassi aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-258">Plus some developers don't like the extra syntax.</span></span>  <span data-ttu-id="6d2ca-259">Se la progettazione è stata rilasciata da zero, è probabile che questa operazione sia necessaria, ma a questo punto è molto più utile mantenere gli iteratori asincroni vicini agli iteratori di sincronizzazione.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-259">If we were designing it from scratch, we'd probably make this required, but at this point there's much more value in keeping async iterators close to sync iterators.</span></span>

## <a name="linq"></a><span data-ttu-id="6d2ca-260">LINQ</span><span class="sxs-lookup"><span data-stu-id="6d2ca-260">LINQ</span></span>

<span data-ttu-id="6d2ca-261">Sono presenti più di ~ 200 overload di metodi nella classe `System.Linq.Enumerable`, che funzionano tutti in termini di `IEnumerable<T>`; Alcune di queste accettano `IEnumerable<T>`, alcune delle quali producono `IEnumerable<T>`e molte eseguono entrambe.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-261">There are over ~200 overloads of methods on the `System.Linq.Enumerable` class, all of which work in terms of `IEnumerable<T>`; some of these accept `IEnumerable<T>`, some of them produce `IEnumerable<T>`, and many do both.</span></span>  <span data-ttu-id="6d2ca-262">L'aggiunta del supporto LINQ per `IAsyncEnumerable<T>` potrebbe comportare la duplicazione di tutti questi overload, per un altro ~ 200.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-262">Adding LINQ support for `IAsyncEnumerable<T>` would likely entail duplicating all of these overloads for it, for another ~200.</span></span>  <span data-ttu-id="6d2ca-263">Poiché `IAsyncEnumerator<T>` probabilmente è più comune come entità autonoma nel mondo asincrono rispetto a `IEnumerator<T>` si trova nel mondo sincrono, potrebbero essere necessari altri ~ 200 overload che funzionano con `IAsyncEnumerator<T>`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-263">And since `IAsyncEnumerator<T>` is likely to be more common as a standalone entity in the asynchronous world than `IEnumerator<T>` is in the synchronous world, we could potentially need another ~200 overloads that work with `IAsyncEnumerator<T>`.</span></span>  <span data-ttu-id="6d2ca-264">Inoltre, un numero elevato di overload occupano i predicati (ad esempio `Where` che accetta una `Func<T, bool>`) e potrebbe essere auspicabile avere Overload basati su `IAsyncEnumerable<T>`che gestiscono predicati sincroni e asincroni (ad esempio `Func<T, ValueTask<bool>>` oltre a `Func<T, bool>`).</span><span class="sxs-lookup"><span data-stu-id="6d2ca-264">Plus, a large number of the overloads deal with predicates (e.g. `Where` that takes a `Func<T, bool>`), and it may be desirable to have `IAsyncEnumerable<T>`-based overloads that deal with both synchronous and asynchronous predicates (e.g. `Func<T, ValueTask<bool>>` in addition to `Func<T, bool>`).</span></span>  <span data-ttu-id="6d2ca-265">Sebbene non sia applicabile a tutti i nuovi overload di ~ 400, un calcolo approssimativo è che sarebbe applicabile a metà, ovvero un altro ~ 200 Overloads, per un totale di ~ 600 nuovi metodi.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-265">While this isn't applicable to all of the now ~400 new overloads, a rough calculation is that it'd be applicable to half, which means another ~200 overloads, for a total of ~600 new methods.</span></span>

<span data-ttu-id="6d2ca-266">Si tratta di un numero sbalorditivo di API, con la possibilità di ancora di più quando vengono prese in considerazione librerie di estensione come Interactive Extensions (IX).</span><span class="sxs-lookup"><span data-stu-id="6d2ca-266">That is a staggering number of APIs, with the potential for even more when extension libraries like Interactive Extensions (Ix) are considered.</span></span>  <span data-ttu-id="6d2ca-267">Ma IX dispone già di un'implementazione di molti di questi e non sembra essere un ottimo motivo per duplicare il lavoro; è invece consigliabile aiutare la community a migliorare IX e consigliarla quando gli sviluppatori vogliono usare LINQ con `IAsyncEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-267">But Ix already has an implementation of many of these, and there doesn't seem to be a great reason to duplicate that work; we should instead help the community improve Ix and recommend it for when developers want to use LINQ with `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="6d2ca-268">Esiste anche il problema della sintassi di comprensione delle query.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-268">There is also the issue of query comprehension syntax.</span></span>  <span data-ttu-id="6d2ca-269">La natura basata su modelli dei tipi di genericità delle query consente loro di "semplicemente lavorare" con alcuni operatori, ad esempio se IX fornisce i metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-269">The pattern-based nature of query comprehensions would allow them to "just work" with some operators, e.g. if Ix provides the following methods:</span></span>

```csharp
public static IAsyncEnumerable<TResult> Select<TSource, TResult>(this IAsyncEnumerable<TSource> source, Func<TSource, TResult> func);
public static IAsyncEnumerable<T> Where(this IAsyncEnumerable<T> source, Func<T, bool> func);
```

<span data-ttu-id="6d2ca-270">il C# codice verrà quindi "solo funzionante":</span><span class="sxs-lookup"><span data-stu-id="6d2ca-270">then this C# code will "just work":</span></span>

```csharp
IAsyncEnumerable<int> enumerable = ...;
IAsyncEnumerable<int> result = from item in enumerable
                               where item % 2 == 0
                               select item * 2;
```

<span data-ttu-id="6d2ca-271">Tuttavia, non esiste alcuna sintassi di comprensione delle query che supporti l'utilizzo di `await` nelle clausole, pertanto se si aggiunge IX, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-271">However, there is no query comprehension syntax that supports using `await` in the clauses, so if Ix added, for example:</span></span>

```csharp
public static IAsyncEnumerable<TResult> Select<TSource, TResult>(this IAsyncEnumerable<TSource> source, Func<TSource, ValueTask<TResult>> func);
```

<span data-ttu-id="6d2ca-272">quindi, si tratta di un "solo lavoro":</span><span class="sxs-lookup"><span data-stu-id="6d2ca-272">then this would "just work":</span></span>

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

<span data-ttu-id="6d2ca-273">Tuttavia, non esiste alcun modo per scriverlo con il `await` inline nella clausola `select`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-273">but there'd be no way to write it with the `await` inline in the `select` clause.</span></span>  <span data-ttu-id="6d2ca-274">Come sforzo separato, è possibile esaminare l'aggiunta di espressioni `async { ... }` al linguaggio, a questo punto è possibile consentirne l'uso nella comprensione delle query. in alternativa, è possibile scrivere quanto segue:</span><span class="sxs-lookup"><span data-stu-id="6d2ca-274">As a separate effort, we could look into adding `async { ... }` expressions to the language, at which point we could allow them to be used in query comprehensions and the above could instead be written as:</span></span>

```csharp
IAsyncEnumerable<int> result = from item in enumerable
                               where item % 2 == 0
                               select async
                               {
                                   await Task.Yield();
                                   return item * 2;
                               };
```

<span data-ttu-id="6d2ca-275">o per abilitare l'utilizzo di `await` direttamente nelle espressioni, ad esempio supportando `async from`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-275">or to enabling `await` to be used directly in expressions, such as by supporting `async from`.</span></span>  <span data-ttu-id="6d2ca-276">Tuttavia, è improbabile che una progettazione influisca sul resto del set di funzionalità un modo o sull'altro e che non si tratta di un aspetto particolarmente importante per investire in questo momento, quindi la proposta è di non eseguire alcuna operazione aggiuntiva in questo momento.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-276">However, it's unlikely a design here would impact the rest of the feature set one way or the other, and this isn't a particularly high-value thing to invest in right now, so the proposal is to do nothing additional here right now.</span></span>

## <a name="integration-with-other-asynchronous-frameworks"></a><span data-ttu-id="6d2ca-277">Integrazione con altri Framework asincroni</span><span class="sxs-lookup"><span data-stu-id="6d2ca-277">Integration with other asynchronous frameworks</span></span>

<span data-ttu-id="6d2ca-278">L'integrazione con `IObservable<T>` e altri Framework asincroni (ad esempio, i flussi reattivi) viene eseguita a livello di libreria anziché a livello di linguaggio.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-278">Integration with `IObservable<T>` and other asynchronous frameworks (e.g. reactive streams) would be done at the library level rather than at the language level.</span></span>  <span data-ttu-id="6d2ca-279">Ad esempio, tutti i dati di un `IAsyncEnumerator<T>` possono essere pubblicati in un `IObserver<T>` semplicemente `await foreach`"ING over the enumerator and `OnNext`" ing the data to the Observer, quindi è possibile un metodo di estensione `AsObservable<T>`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-279">For example, all of the data from an `IAsyncEnumerator<T>` can be published to an `IObserver<T>` simply by `await foreach`'ing over the enumerator and `OnNext`'ing the data to the observer, so an `AsObservable<T>` extension method is possible.</span></span>  <span data-ttu-id="6d2ca-280">L'utilizzo di un `IObservable<T>` in un `await foreach` richiede il buffering dei dati (nel caso in cui venga eseguito il push di un altro elemento mentre l'elemento precedente è ancora in fase di elaborazione), ma è possibile implementare facilmente un adattatore push pull per consentire il pull di un `IObservable<T>` con una `IAsyncEnumerator<T>`.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-280">Consuming an `IObservable<T>` in a `await foreach` requires buffering the data (in case another item is pushed while the previous item is still being processing), but such a push-pull adapter can easily be implemented to enable an `IObservable<T>` to be pulled from with an `IAsyncEnumerator<T>`.</span></span>  <span data-ttu-id="6d2ca-281">Via.  RX/IX fornisce già prototipi di tali implementazioni e librerie come https://github.com/dotnet/corefx/tree/master/src/System.Threading.Channels forniscono vari tipi di strutture di dati di buffering.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-281">Etc.  Rx/Ix already provide prototypes of such implementations, and libraries like https://github.com/dotnet/corefx/tree/master/src/System.Threading.Channels provide various kinds of buffering data structures.</span></span>  <span data-ttu-id="6d2ca-282">In questa fase non è necessario che il linguaggio sia occupato.</span><span class="sxs-lookup"><span data-stu-id="6d2ca-282">The language need not be involved at this stage.</span></span>
