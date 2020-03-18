---
ms.openlocfilehash: 1457c1eb018e12af30ce5b38be704bf8851d4b25
ms.sourcegitcommit: 7f7fc6e9e195e51b7ff8229aeaa70aa9fbbb63cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "79485014"
---
# <a name="efficient-params-and-string-formatting"></a><span data-ttu-id="51da3-101">Parametri efficienti e formattazione delle stringhe</span><span class="sxs-lookup"><span data-stu-id="51da3-101">Efficient Params and String Formatting</span></span>

## <a name="summary"></a><span data-ttu-id="51da3-102">Summary</span><span class="sxs-lookup"><span data-stu-id="51da3-102">Summary</span></span>
<span data-ttu-id="51da3-103">Questa combinazione di funzionalità aumenterà l'efficienza della formattazione `string` valori e il passaggio di argomenti di stile `params`.</span><span class="sxs-lookup"><span data-stu-id="51da3-103">This combination of features will increase the efficiency of formatting `string` values and passing of `params` style arguments.</span></span>

## <a name="motivation"></a><span data-ttu-id="51da3-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="51da3-104">Motivation</span></span>
<span data-ttu-id="51da3-105">L'overhead di allocazione per la formattazione dei valori `string` può compromettere le prestazioni di molte applicazioni basate su testo: dalla pena di boxe dei tipi di `struct`, dall'allocazione `object[]` per `params` e dalle allocazioni `string` intermedie durante le chiamate `string.Format`.</span><span class="sxs-lookup"><span data-stu-id="51da3-105">The allocation overhead of formatting `string` values can dominate the performance of many text based applications: from the boxing penalty of `struct` types, the `object[]` allocation for `params` and the intermediate `string` allocations during `string.Format` calls.</span></span> <span data-ttu-id="51da3-106">Per garantire l'efficienza di tali applicazioni, spesso è necessario abbandonare le funzionalità di produttività, ad esempio `params` e `string` l'interpolazione e passare a soluzioni codificate a mano non standard.</span><span class="sxs-lookup"><span data-stu-id="51da3-106">In order to maintain efficiency such applications often need to abandon productivity features such as `params` and `string` interpolation and move to non-standard, hand coded solutions.</span></span> 

<span data-ttu-id="51da3-107">Prendere in considerazione MSBuild come esempio.</span><span class="sxs-lookup"><span data-stu-id="51da3-107">Consider MSBuild as an example.</span></span> <span data-ttu-id="51da3-108">Questa operazione viene scritta usando numerose funzionalità moderne C# da parte degli sviluppatori che sono consapevoli delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="51da3-108">This is written using a lot of modern C# features by developers who are conscious of performance.</span></span> <span data-ttu-id="51da3-109">Tuttavia, in un esempio di compilazione rappresentativo MSBuild genererà 262MB di allocazione `string` usando un livello di dettaglio minimo.</span><span class="sxs-lookup"><span data-stu-id="51da3-109">Yet in one representative build sample MSBuild will generate 262MB of `string` allocation using minimal verbosity.</span></span> <span data-ttu-id="51da3-110">Di tale 1/2 delle allocazioni sono allocazioni di breve durata all'interno `string.Format`.</span><span class="sxs-lookup"><span data-stu-id="51da3-110">Of that 1/2 of the allocations are short lived allocations inside `string.Format`.</span></span> <span data-ttu-id="51da3-111">Queste funzionalità consentono di rimuovere gran parte di queste funzionalità sul desktop .NET e di ridurle a quasi zero in .NET Core a causa della disponibilità di `Span<T>`</span><span class="sxs-lookup"><span data-stu-id="51da3-111">These features would remove much of that on .NET Desktop and get it down to nearly zero on .NET Core due to the availability of `Span<T>`</span></span>

<span data-ttu-id="51da3-112">Il set di funzionalità del linguaggio descritte in questo articolo consentirà alle applicazioni di continuare a usare queste funzionalità, con una varianza minima o nulla per la base di codice dell'applicazione, rimuovendo al tempo stesso l'overhead di allocazione imprevisto nella maggior parte dei casi.</span><span class="sxs-lookup"><span data-stu-id="51da3-112">The set of language features described here will enable applications to continue using these features, with very little or no churn to their application code base, while removing the unintended allocation overhead in the majority of cases.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="51da3-113">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="51da3-113">Detailed Design</span></span> 
<span data-ttu-id="51da3-114">Ecco un set di funzionalità che verranno usate qui per ottenere i risultati seguenti:</span><span class="sxs-lookup"><span data-stu-id="51da3-114">There are a set of features that will be used here to achieve these results:</span></span>

- <span data-ttu-id="51da3-115">Espansione `params` per supportare un set più ampio di tipi di raccolta.</span><span class="sxs-lookup"><span data-stu-id="51da3-115">Expanding `params` to support a broader set of collection types.</span></span>
- <span data-ttu-id="51da3-116">Consentire agli sviluppatori di personalizzare il modo in cui viene eseguita l'interpolazione `string`.</span><span class="sxs-lookup"><span data-stu-id="51da3-116">Allowing for developers to customize how `string` interpolation is achieved.</span></span> 
- <span data-ttu-id="51da3-117">Consentire l'associazione di `string` interpolati a overload `string.Format` più efficienti.</span><span class="sxs-lookup"><span data-stu-id="51da3-117">Allowing for interpolated `string` to bind to more efficient `string.Format` overloads.</span></span>

### <a name="extending-params"></a><span data-ttu-id="51da3-118">Estensione di params</span><span class="sxs-lookup"><span data-stu-id="51da3-118">Extending params</span></span>
<span data-ttu-id="51da3-119">Il linguaggio consentirà `params` nella firma di un metodo di disporre dei tipi `Span<T>`, `ReadOnlySpan<T>` e `IEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="51da3-119">The language will allow for `params` in a method signature to have the types `Span<T>`, `ReadOnlySpan<T>` and `IEnumerable<T>`.</span></span> <span data-ttu-id="51da3-120">Le stesse regole per la chiamata verranno applicate a questi nuovi tipi che si applicano a `params T[]`:</span><span class="sxs-lookup"><span data-stu-id="51da3-120">The same rules for invocation will apply to these new types that apply to `params T[]`:</span></span>

- <span data-ttu-id="51da3-121">Non è possibile eseguire l'overload se l'unica differenza è una parola chiave `params`.</span><span class="sxs-lookup"><span data-stu-id="51da3-121">Can't overload where the only difference is a `params` keyword.</span></span>
- <span data-ttu-id="51da3-122">Può richiamare passando una serie di argomenti convertibili in modo implicito in `T` o in un singolo `Span<T>` / 
`ReadOnlySpan<T>` / argomento `IEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="51da3-122">Can invoke by passing a series of arguments that are implicitly convertible to `T` or a single `Span<T>` / 
`ReadOnlySpan<T>` / `IEnumerable<T>` argument.</span></span>
- <span data-ttu-id="51da3-123">Deve essere l'ultimo parametro in una firma del metodo.</span><span class="sxs-lookup"><span data-stu-id="51da3-123">Must be the last parameter in a method signature.</span></span>
- <span data-ttu-id="51da3-124">E così via...</span><span class="sxs-lookup"><span data-stu-id="51da3-124">Etc ...</span></span> 

<span data-ttu-id="51da3-125">Per semplicità, le varianti `Span<T>` e `ReadOnlySpan<T>` verranno indicate come `Span<T>` di seguito.</span><span class="sxs-lookup"><span data-stu-id="51da3-125">The `Span<T>` and `ReadOnlySpan<T>` variants will be referred to as `Span<T>` below for simplicity.</span></span> <span data-ttu-id="51da3-126">Nei casi in cui il comportamento di `ReadOnlySpan<T>` è diverso, verrà esplicitamente chiamato.</span><span class="sxs-lookup"><span data-stu-id="51da3-126">In cases where the behavior of `ReadOnlySpan<T>` differs it will be explicitly called out.</span></span> 

<span data-ttu-id="51da3-127">Il vantaggio offerto dalle varianti `Span<T>` di `params` fornisce al compilatore una grande flessibilità nel modo in cui alloca l'archivio di backup per il valore di `Span<T>`.</span><span class="sxs-lookup"><span data-stu-id="51da3-127">The advantage the `Span<T>` variants of `params` provides is it gives the compiler great flexibility in how it allocates the backing storage for the `Span<T>` value.</span></span> <span data-ttu-id="51da3-128">Con un `params T[]` il compilatore deve allocare una nuova `T[]` per ogni chiamata di un metodo di `params`.</span><span class="sxs-lookup"><span data-stu-id="51da3-128">With a `params T[]` the compiler must allocate a new `T[]` for every invocation of a `params` method.</span></span> <span data-ttu-id="51da3-129">Il riutilizzo non è possibile perché deve presupporre che il chiamato abbia archiviato e riutilizzato il parametro.</span><span class="sxs-lookup"><span data-stu-id="51da3-129">Re-use is not possible because it must assume the callee stored and reused the parameter.</span></span> <span data-ttu-id="51da3-130">Questo può causare un notevole inefficienza nei metodi con numerose chiamate di `params`.</span><span class="sxs-lookup"><span data-stu-id="51da3-130">This can lead to a large inefficiency in methods with lots of `params` invocations.</span></span>

<span data-ttu-id="51da3-131">Date `Span<T>` varianti `ref struct` il chiamato non è in grado di archiviare l'argomento.</span><span class="sxs-lookup"><span data-stu-id="51da3-131">Given `Span<T>` variants are `ref struct` the callee cannot store the argument.</span></span> <span data-ttu-id="51da3-132">Di conseguenza, il compilatore può ottimizzare i siti di chiamata eseguendo azioni come il riutilizzo dell'argomento.</span><span class="sxs-lookup"><span data-stu-id="51da3-132">Hence the compiler can optimize the call sites by taking actions like re-using the argument.</span></span> <span data-ttu-id="51da3-133">Questo può rendere molto efficienti le chiamate ripetute rispetto a `T[]`.</span><span class="sxs-lookup"><span data-stu-id="51da3-133">This can make repeated invocations very efficient as compared to `T[]`.</span></span> <span data-ttu-id="51da3-134">Il linguaggio, tuttavia, non renderà disponibili garanzie specifiche sul modo in cui tali CallSites sono ottimizzate.</span><span class="sxs-lookup"><span data-stu-id="51da3-134">The language though will make no specific guarantees about how such callsites are optimized.</span></span> <span data-ttu-id="51da3-135">Si noti che il compilatore è libero di usare valori diversi da `T[]` quando si richiama un metodo di `params Span<T>`.</span><span class="sxs-lookup"><span data-stu-id="51da3-135">Only note that the compiler is free to use values other than `T[]` when invoking a `params Span<T>` method.</span></span> 

<span data-ttu-id="51da3-136">Uno di questi potenziali implementazioni è il seguente.</span><span class="sxs-lookup"><span data-stu-id="51da3-136">One such potential implementation is the following.</span></span> <span data-ttu-id="51da3-137">Si consideri tutte `params` chiamata nel corpo di un metodo.</span><span class="sxs-lookup"><span data-stu-id="51da3-137">Consider all `params` invocation in a method body.</span></span> <span data-ttu-id="51da3-138">Il compilatore può allocare una matrice con una dimensione uguale alla chiamata di `params` più grande e usarla per tutte le chiamate creando istanze di `Span<T>` opportunamente dimensionate sulla matrice.</span><span class="sxs-lookup"><span data-stu-id="51da3-138">The compiler could allocate an array which has a size equal to the largest `params` invocation and use that for all of the invocations by creating appropriately sized `Span<T>` instances over the array.</span></span> <span data-ttu-id="51da3-139">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="51da3-139">For example:</span></span>

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

<span data-ttu-id="51da3-140">Il compilatore può scegliere di emettere il corpo di `Go` come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="51da3-140">The compiler could choose to emit the body of `Go` as follows:</span></span>

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

<span data-ttu-id="51da3-141">Questo può ridurre significativamente il numero di matrici allocate in un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="51da3-141">This can significantly reduce the number of arrays allocated in an application.</span></span> <span data-ttu-id="51da3-142">Le allocazioni possono essere ulteriormente ridotte se il runtime fornisce utilità per l'allocazione più intelligente dello stack di matrici.</span><span class="sxs-lookup"><span data-stu-id="51da3-142">Allocations can be even further reduced if the runtime provides utilities for smarter stack allocation of arrays.</span></span>

<span data-ttu-id="51da3-143">Questa ottimizzazione non può essere sempre applicata.</span><span class="sxs-lookup"><span data-stu-id="51da3-143">This optimization cannot always be applied though.</span></span> <span data-ttu-id="51da3-144">Anche se il chiamato non è in grado di acquisire l'argomento `params` può comunque essere acquisito nel chiamante quando è presente un `ref` o un parametro `out / ref` che è a sua volta un tipo `ref struct`.</span><span class="sxs-lookup"><span data-stu-id="51da3-144">Even though the callee cannot capture the `params` argument it can still be captured in the caller when there is a `ref` or a `out / ref` parameter that is itself a `ref struct` type.</span></span> 

``` csharp
static class SneakyCapture {
    static ref int M(params Span<T> span) => ref span[0];

    static void Oops() {
        // This now holds onto the memory backing the Span<T> 
        ref int r = ref M(42);
    }
}
```

<span data-ttu-id="51da3-145">Questi casi sono comunque rilevabili in modo statico.</span><span class="sxs-lookup"><span data-stu-id="51da3-145">These cases are statically detectable though.</span></span> <span data-ttu-id="51da3-146">Questo problema si verifica potenzialmente ogni volta che viene restituito un `ref` o un `ref struct` parametro passato da `out` o `ref`.</span><span class="sxs-lookup"><span data-stu-id="51da3-146">It potentially occurs whenever there is a `ref` return or a `ref struct` parameter passed by `out` or `ref`.</span></span> <span data-ttu-id="51da3-147">In tal caso, il compilatore deve allocare una `T[]` aggiornata per ogni chiamata.</span><span class="sxs-lookup"><span data-stu-id="51da3-147">In such a case the compiler must allocate a fresh `T[]` for every invocation.</span></span> 

<span data-ttu-id="51da3-148">Alla fine di questo documento sono illustrate diverse altre strategie di ottimizzazione potenziali.</span><span class="sxs-lookup"><span data-stu-id="51da3-148">Several other potential optimization strategies are discussed at the end of this document.</span></span>

<span data-ttu-id="51da3-149">Il `IEnumerable<T>` Variant è semplicemente un overload di praticità.</span><span class="sxs-lookup"><span data-stu-id="51da3-149">The `IEnumerable<T>` variant is a merely a convenience overload.</span></span> <span data-ttu-id="51da3-150">È utile in scenari con frequenti utilizzi di `IEnumerable<T>`, ma con un numero elevato di `params` utilizzo.</span><span class="sxs-lookup"><span data-stu-id="51da3-150">It's useful in scenarios which have frequent uses of `IEnumerable<T>` but also have lots of `params` usage.</span></span> <span data-ttu-id="51da3-151">Quando viene richiamato in `T` argomento, lo spazio di archiviazione di backup verrà allocato come `T[]` proprio come `params T[]` viene eseguito oggi.</span><span class="sxs-lookup"><span data-stu-id="51da3-151">When invoked in `T` argument form the backing storage will be allocated as a `T[]` just as `params T[]` is done today.</span></span>

### <a name="params-overload-resolution-changes"></a><span data-ttu-id="51da3-152">modifiche della risoluzione dell'overload params</span><span class="sxs-lookup"><span data-stu-id="51da3-152">params overload resolution changes</span></span>
<span data-ttu-id="51da3-153">Questa proposta significa che la lingua dispone ora di quattro varianti di `params`, dove prima ne aveva una.</span><span class="sxs-lookup"><span data-stu-id="51da3-153">This proposal means the language now has four variants of `params` where before it had one.</span></span> <span data-ttu-id="51da3-154">È ragionevole per i metodi definire overload di metodi che differiscono solo per il tipo di una dichiarazione di `params`.</span><span class="sxs-lookup"><span data-stu-id="51da3-154">It is sensible for methods to define overloads of methods that differ only on the type of a `params` declarations.</span></span> 

<span data-ttu-id="51da3-155">Si tenga presente che `StringBuilder.AppendFormat` aggiungerebbe certamente un overload `params ReadOnlySpan<object>` oltre al `params object[]`.</span><span class="sxs-lookup"><span data-stu-id="51da3-155">Consider that `StringBuilder.AppendFormat` would certainly add a `params ReadOnlySpan<object>` overload in addition to the `params object[]`.</span></span> <span data-ttu-id="51da3-156">Ciò consente di migliorare notevolmente le prestazioni riducendo le allocazioni di raccolta senza richiedere alcuna modifica al codice chiamante.</span><span class="sxs-lookup"><span data-stu-id="51da3-156">This would allow it to substantially improve performance by reducing collection allocations without requiring any changes to the calling code.</span></span> 

<span data-ttu-id="51da3-157">Per semplificare questa operazione, il linguaggio introdurrà la regola di suddivisione dell'overload della risoluzione dell'overload seguente.</span><span class="sxs-lookup"><span data-stu-id="51da3-157">To facilitate this the language will introduce the following overload resolution tie breaking rule.</span></span> <span data-ttu-id="51da3-158">Quando i metodi candidati differiscono solo per il parametro `params`, i candidati saranno preferiti nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="51da3-158">When the candidate methods differ only by the `params` parameter then the candidates will be preferred in the following order:</span></span>

1. `ReadOnlySpan<T>`
1. `Span<T>`
1. `T[]`
1. `IEnumerable<T>`

<span data-ttu-id="51da3-159">Questo ordine è il più economico per il caso generale.</span><span class="sxs-lookup"><span data-stu-id="51da3-159">This order is the most to the least efficient for the general case.</span></span>

### <a name="variant"></a><span data-ttu-id="51da3-160">Variant</span><span class="sxs-lookup"><span data-stu-id="51da3-160">Variant</span></span>
<span data-ttu-id="51da3-161">CoreFX è la creazione di prototipi di un nuovo tipo gestito denominato [Variant](https://github.com/dotnet/corefxlab/pull/2595).</span><span class="sxs-lookup"><span data-stu-id="51da3-161">CoreFX is prototyping a new managed type named [Variant](https://github.com/dotnet/corefxlab/pull/2595).</span></span> <span data-ttu-id="51da3-162">Questo tipo deve essere usato nelle API che prevedono valori eterogenei, ma non desiderano che il sovraccarico venga immesso utilizzando `object` come parametro.</span><span class="sxs-lookup"><span data-stu-id="51da3-162">This type is meant to be used in APIs which expect heterogeneous values but don't want the overhead brought on by using `object` as the parameter.</span></span> <span data-ttu-id="51da3-163">Il tipo di `Variant` fornisce l'archiviazione universale, ma evita l'allocazione Boxing per i tipi usati più di frequente.</span><span class="sxs-lookup"><span data-stu-id="51da3-163">The `Variant` type provides universal storage but avoids the boxing allocation for the most commonly used types.</span></span> <span data-ttu-id="51da3-164">L'uso di questo tipo in API come `string.Format` può eliminare l'overhead di Boxing nella maggior parte dei casi.</span><span class="sxs-lookup"><span data-stu-id="51da3-164">Using this type in APIs like `string.Format` can eliminate the boxing overhead in the majority of cases.</span></span>

<span data-ttu-id="51da3-165">Questo tipo non è necessariamente speciale per il linguaggio.</span><span class="sxs-lookup"><span data-stu-id="51da3-165">This type itself is not necessarily special to the language.</span></span> <span data-ttu-id="51da3-166">Viene introdotta in questo documento separatamente, sebbene diventi un dettaglio di implementazione di altre parti della proposta.</span><span class="sxs-lookup"><span data-stu-id="51da3-166">It is being introduced in this document separately though as it becomes an implementation detail of other parts of the proposal.</span></span> 

### <a name="efficient-interpolated-strings"></a><span data-ttu-id="51da3-167">Stringhe interpolate efficienti</span><span class="sxs-lookup"><span data-stu-id="51da3-167">Efficient interpolated strings</span></span>
<span data-ttu-id="51da3-168">Le stringhe interpolate sono una funzionalità molto diffusa ma C#inefficiente in.</span><span class="sxs-lookup"><span data-stu-id="51da3-168">Interpolated strings are a popular yet inefficient feature in C#.</span></span> <span data-ttu-id="51da3-169">La sintassi più comune, usando una `string` interpolata come `string`, si traduce in una chiamata di `string.Format(string, params object[])`.</span><span class="sxs-lookup"><span data-stu-id="51da3-169">The most common syntax, using an interpolated `string` as a `string`, translates into a `string.Format(string, params object[])` call.</span></span> <span data-ttu-id="51da3-170">Questo comporterà allocazioni Boxing per tutti i tipi di valore, allocazioni di `string` intermedie poiché l'implementazione USA in gran parte `object.ToString` per la formattazione e le allocazioni di matrici quando il numero di argomenti supera la quantità di parametri negli overload "Fast" di `string.Format`.</span><span class="sxs-lookup"><span data-stu-id="51da3-170">That will incur boxing allocations for all value types, intermediate `string` allocations as the implementation largely uses `object.ToString` for formatting as well as array allocations once the number of arguments exceeds the amount of parameters on the "fast" overloads of `string.Format`.</span></span> 

<span data-ttu-id="51da3-171">Il linguaggio cambierà la riduzione dell'interpolazione per prendere in considerazione gli overload alternativi di `string.Format`.</span><span class="sxs-lookup"><span data-stu-id="51da3-171">The language will change its interpolation lowering to consider alternate overloads of `string.Format`.</span></span> <span data-ttu-id="51da3-172">Prenderà in considerazione tutte le forme di `string.Format(string, params)` e sceglierà l'overload "Best" che soddisfa i tipi di argomento.</span><span class="sxs-lookup"><span data-stu-id="51da3-172">It will consider all forms of `string.Format(string, params)` and pick the "best" overload which satisfies the argument types.</span></span>
<span data-ttu-id="51da3-173">L'overload "migliore" `params` sarà determinato dalle regole descritte in precedenza.</span><span class="sxs-lookup"><span data-stu-id="51da3-173">The "best" `params` overload will be determined by the rules discussed above.</span></span> <span data-ttu-id="51da3-174">Ciò significa che `string` interpolate ora possono essere associate a overload molto efficienti come `string.Format(string format, params ReadOnlySpan<Variant> args)`.</span><span class="sxs-lookup"><span data-stu-id="51da3-174">This means interpolated `string` can now bind to very efficient overloads like `string.Format(string format, params ReadOnlySpan<Variant> args)`.</span></span> <span data-ttu-id="51da3-175">In molti casi, tutte le allocazioni intermedie vengono rimosse.</span><span class="sxs-lookup"><span data-stu-id="51da3-175">In many cases this will remove all intermediate allocations.</span></span>

### <a name="customizable-interpolated-strings"></a><span data-ttu-id="51da3-176">Stringhe interpolate personalizzabili</span><span class="sxs-lookup"><span data-stu-id="51da3-176">Customizable interpolated strings</span></span>
<span data-ttu-id="51da3-177">Gli sviluppatori sono in grado di personalizzare il comportamento delle stringhe interpolate con `FormattableString`.</span><span class="sxs-lookup"><span data-stu-id="51da3-177">Developers are able to customize the behavior of interpolated strings with `FormattableString`.</span></span> <span data-ttu-id="51da3-178">Contiene i dati che vengono inseriti in una stringa interpolata: il formato `string` e gli argomenti come matrice.</span><span class="sxs-lookup"><span data-stu-id="51da3-178">This contains the data which goes into an interpolated string: the format `string` and the arguments as an array.</span></span> <span data-ttu-id="51da3-179">Questa operazione, tuttavia, ha ancora l'allocazione Boxing e la matrice di argomenti, nonché l'allocazione per `FormattableString` (si tratta di un `abstract class`).</span><span class="sxs-lookup"><span data-stu-id="51da3-179">This though still has the boxing and argument array allocation as well as the allocation for `FormattableString` (it's an `abstract class`).</span></span> <span data-ttu-id="51da3-180">Di conseguenza, non è possibile utilizzare le applicazioni che sono molto complesse nell'allocazione `string` formattazione.</span><span class="sxs-lookup"><span data-stu-id="51da3-180">Hence it's of little use to applications which are allocation heavy in `string` formatting.</span></span>

<span data-ttu-id="51da3-181">Per rendere efficiente la formattazione delle stringhe interpolate, il linguaggio rileverà un nuovo tipo: `System.ValueFormattableString`.</span><span class="sxs-lookup"><span data-stu-id="51da3-181">To make interpolated string formatting efficient the language will recognize a new type: `System.ValueFormattableString`.</span></span> <span data-ttu-id="51da3-182">Tutte le stringhe interpolate avranno una conversione del tipo di destinazione in questo tipo.</span><span class="sxs-lookup"><span data-stu-id="51da3-182">All interpolated strings will have a target type conversion to this type.</span></span> <span data-ttu-id="51da3-183">Questa operazione verrà implementata traducendo la stringa interpolata nella chiamata `ValueFormattableString.Create` esattamente come avviene per `FormattableString.Create` oggi.</span><span class="sxs-lookup"><span data-stu-id="51da3-183">This will be implemented by translating the interpolated string into the call `ValueFormattableString.Create` exactly as is done for `FormattableString.Create` today.</span></span> <span data-ttu-id="51da3-184">Il linguaggio supporterà tutte le opzioni `params` descritte in questo documento quando si cerca il metodo di `ValueFormattableString.Create` più appropriato.</span><span class="sxs-lookup"><span data-stu-id="51da3-184">The language will support all `params` options described in this document when looking for the most suitable `ValueFormattableString.Create` method.</span></span> 

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

<span data-ttu-id="51da3-185">Le regole di risoluzione dell'overload verranno modificate in modo da preferire `ValueFormattableString` rispetto `string` quando l'argomento è una stringa interpolata.</span><span class="sxs-lookup"><span data-stu-id="51da3-185">Overload resolution rules will be changed to prefer `ValueFormattableString` over `string` when the argument is an interpolated string.</span></span> <span data-ttu-id="51da3-186">Ciò significa che sarà utile avere overload che differiscono solo per `string` e `ValueFormattableString`.</span><span class="sxs-lookup"><span data-stu-id="51da3-186">This means it will be valuable to have overloads which differ only on `string` and `ValueFormattableString`.</span></span> <span data-ttu-id="51da3-187">Un sovraccarico di questo tipo oggi con `FormattableString` non è utile perché il compilatore preferisce sempre la versione `string` (a meno che lo sviluppatore non usi un cast esplicito).</span><span class="sxs-lookup"><span data-stu-id="51da3-187">Such an overload today with `FormattableString` is not valuable as the compiler will always prefer the `string` version (unless the developer uses an explicit cast).</span></span> 

## <a name="open-issues"></a><span data-ttu-id="51da3-188">Problemi aperti</span><span class="sxs-lookup"><span data-stu-id="51da3-188">Open Issues</span></span>

### <a name="valueformattablestring-breaking-change"></a><span data-ttu-id="51da3-189">Modifica di rilievo ValueFormattableString</span><span class="sxs-lookup"><span data-stu-id="51da3-189">ValueFormattableString breaking change</span></span>
<span data-ttu-id="51da3-190">La modifica da preferire `ValueFormattableString` durante la risoluzione dell'overload su `string` è una modifica sostanziale.</span><span class="sxs-lookup"><span data-stu-id="51da3-190">The change to prefer `ValueFormattableString` during overload resolution over `string` is a breaking change.</span></span> <span data-ttu-id="51da3-191">Uno sviluppatore può definire un tipo denominato `ValueFormattableString` oggi e utilizzarlo negli overload del metodo con `string`.</span><span class="sxs-lookup"><span data-stu-id="51da3-191">It is possible for a developer to have defined a type called `ValueFormattableString` today and use it in method overloads with `string`.</span></span> <span data-ttu-id="51da3-192">Questa modifica proposta comporta il prelievo da parte del compilatore di un overload diverso dopo l'implementazione di questo set di funzionalità.</span><span class="sxs-lookup"><span data-stu-id="51da3-192">This proposed change would cause the compiler to pick a different overload once this set of features was implemented.</span></span> 

<span data-ttu-id="51da3-193">La possibilità di questo sembra ragionevolmente bassa.</span><span class="sxs-lookup"><span data-stu-id="51da3-193">The possibility of this seems reasonably low.</span></span> <span data-ttu-id="51da3-194">Per il tipo è necessario il nome completo `System.ValueFormattableString` ed è necessario disporre di `static` metodi denominati `Create`.</span><span class="sxs-lookup"><span data-stu-id="51da3-194">The type would need the full name `System.ValueFormattableString` and it would need to have `static` methods named `Create`.</span></span> <span data-ttu-id="51da3-195">Dato che gli sviluppatori sono fortemente sconsigliati di definire qualsiasi tipo nello spazio dei nomi `System` questa rottura sembra una compromissione ragionevole.</span><span class="sxs-lookup"><span data-stu-id="51da3-195">Given that developers are strongly discouraged from defining any type in the `System` namespace this break seems like a reasonable compromise.</span></span>

### <a name="expanding-to-more-types"></a><span data-ttu-id="51da3-196">Espansione a più tipi</span><span class="sxs-lookup"><span data-stu-id="51da3-196">Expanding to more types</span></span>
<span data-ttu-id="51da3-197">Dato che ci troviamo nell'area, dobbiamo prendere in considerazione l'aggiunta di `IList<T>`, `ICollection<T>` e `IReadOnlyList<T>` al set di raccolte per cui `params` è supportato.</span><span class="sxs-lookup"><span data-stu-id="51da3-197">Given we're in the area we should consider adding `IList<T>`, `ICollection<T>` and `IReadOnlyList<T>` to the set of collections for which `params` is supported.</span></span> <span data-ttu-id="51da3-198">In termini di implementazione, il costo di una piccola quantità rispetto alle altre operazioni qui.</span><span class="sxs-lookup"><span data-stu-id="51da3-198">In terms of implementation it will cost a small amount over the other work here.</span></span>

<span data-ttu-id="51da3-199">LDM deve decidere se la complicazione per la lingua è comunque valsa la pena.</span><span class="sxs-lookup"><span data-stu-id="51da3-199">LDM needs to decide if the complication to the language is worth it though.</span></span> <span data-ttu-id="51da3-200">L'aggiunta di `IEnumerable<T>` rimuove un punto di attrito molto specifico.</span><span class="sxs-lookup"><span data-stu-id="51da3-200">The addition of `IEnumerable<T>` removes a very specific friction point.</span></span> <span data-ttu-id="51da3-201">Manca questa `params` soluzione molti clienti hanno dovuto allocare `T[]` da un `IEnumerable<T>` quando si chiama un metodo `params`.</span><span class="sxs-lookup"><span data-stu-id="51da3-201">Lacking this `params` solution many customers were forced to allocate `T[]` from an `IEnumerable<T>` when calling a `params` method.</span></span> <span data-ttu-id="51da3-202">L'aggiunta di `IEnumerable<T>` corregge tuttavia questo problema.</span><span class="sxs-lookup"><span data-stu-id="51da3-202">The addition of `IEnumerable<T>` fixes this though.</span></span> <span data-ttu-id="51da3-203">Non esiste un punto di attrito specifico che le altre interfacce correggeranno qui.</span><span class="sxs-lookup"><span data-stu-id="51da3-203">There is no specific friction point that the other interfaces fix here.</span></span> 

## <a name="considerations"></a><span data-ttu-id="51da3-204">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="51da3-204">Considerations</span></span>

### <a name="variant2-and-variant3"></a><span data-ttu-id="51da3-205">Variant2 e Variant3</span><span class="sxs-lookup"><span data-stu-id="51da3-205">Variant2 and Variant3</span></span>
<span data-ttu-id="51da3-206">Il team di CoreFX dispone anche di un set di tipi di archiviazione non allocato per un massimo di tre `Variant` argomenti.</span><span class="sxs-lookup"><span data-stu-id="51da3-206">The CoreFX team also has a non-allocating set of storage types for up to three `Variant` arguments.</span></span> <span data-ttu-id="51da3-207">Si tratta di una singola `Variant`, `Variant2` e `Variant3`.</span><span class="sxs-lookup"><span data-stu-id="51da3-207">These are a single `Variant`, `Variant2` and `Variant3`.</span></span> <span data-ttu-id="51da3-208">Tutti dispongono di una coppia di metodi per ottenere un'allocazione priva di `Span<Variant>`: `CreateSpan` e `KeepAlive`.</span><span class="sxs-lookup"><span data-stu-id="51da3-208">All have a pair of methods for getting an allocation free `Span<Variant>` off of them: `CreateSpan` and `KeepAlive`.</span></span> <span data-ttu-id="51da3-209">Ciò significa che per un `params Span<Variant>` di un massimo di tre argomenti, il sito di chiamata può essere interamente allocazione gratuita.</span><span class="sxs-lookup"><span data-stu-id="51da3-209">This means for a `params Span<Variant>` of up to three arguments the call site can be entirely allocation free.</span></span>

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

<span data-ttu-id="51da3-210">Il metodo `Go` può essere ridotto a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="51da3-210">The `Go` method can be lowered to the following:</span></span>

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

<span data-ttu-id="51da3-211">Questa operazione richiede un lavoro molto limitato sulla proposta di riutilizzare `T[]` tra `params Span<T>` chiamate.</span><span class="sxs-lookup"><span data-stu-id="51da3-211">This requires very little work on top of the proposal to re-use `T[]` between `params Span<T>` calls.</span></span> <span data-ttu-id="51da3-212">Il compilatore è già in grado di gestire un oggetto temporaneo per chiamata ed eseguire la pulizia del lavoro dopo (anche se in un caso si sta semplicemente contrassegnando una temperatura interna come disponibile).</span><span class="sxs-lookup"><span data-stu-id="51da3-212">The compiler already needs to manage a temporary per call and do clean up work after (even if in one case it's just marking an internal temp as free).</span></span> 

<span data-ttu-id="51da3-213">Nota: la funzione `KeepAlive` è necessaria solo sul desktop.</span><span class="sxs-lookup"><span data-stu-id="51da3-213">Note: the `KeepAlive` function is only necessary on desktop.</span></span> <span data-ttu-id="51da3-214">In .NET Core il metodo non sarà disponibile e pertanto il compilatore non emetterà una chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="51da3-214">On .NET Core the method will not be available and hence the compiler won't emit a call to it.</span></span>

### <a name="clr-stack-allocation-helpers"></a><span data-ttu-id="51da3-215">Helper di allocazione dello stack CLR</span><span class="sxs-lookup"><span data-stu-id="51da3-215">CLR stack allocation helpers</span></span>
<span data-ttu-id="51da3-216">CLR fornisce solo [localloc](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.localloc?redirectedfrom=MSDN&view=netframework-4.7.2) per l'allocazione dello stack della memoria contigua.</span><span class="sxs-lookup"><span data-stu-id="51da3-216">The CLR only provides only [localloc](https://docs.microsoft.com/en-us/dotnet/api/system.reflection.emit.opcodes.localloc?redirectedfrom=MSDN&view=netframework-4.7.2) for stack allocation of contiguous memory.</span></span> <span data-ttu-id="51da3-217">Questa istruzione è limitata perché funziona solo per i tipi di `unmanaged`.</span><span class="sxs-lookup"><span data-stu-id="51da3-217">This instruction is limited in that it only works for `unmanaged` types.</span></span> <span data-ttu-id="51da3-218">Ciò significa che non può essere usato come soluzione universale per allocare in modo efficiente l'archiviazione di backup per `params 
 Span<T>`.</span><span class="sxs-lookup"><span data-stu-id="51da3-218">This means it can't be used as a universal solution for efficiently allocating the backing storage for `params 
 Span<T>`.</span></span> 

<span data-ttu-id="51da3-219">Questa limitazione non è una limitazione fondamentale, ma piuttosto un artefatto della cronologia.</span><span class="sxs-lookup"><span data-stu-id="51da3-219">This limitation is not some fundamental restriction though but instead more an artifact of history.</span></span> <span data-ttu-id="51da3-220">CLR può scegliere di aggiungere nuovi codici op/intrinseci che forniscono l'allocazione dello stack universale.</span><span class="sxs-lookup"><span data-stu-id="51da3-220">The CLR could choose to add new op codes / intrinsics which provide universal stack allocation.</span></span> <span data-ttu-id="51da3-221">Questi possono quindi essere usati per allocare l'archiviazione di backup per la maggior parte delle chiamate `params Span<T>`.</span><span class="sxs-lookup"><span data-stu-id="51da3-221">These could then be used to allocate the backing storage for most `params Span<T>` calls.</span></span>

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

<span data-ttu-id="51da3-222">Il metodo `Go` può essere ridotto a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="51da3-222">The `Go` method can be lowered to the following:</span></span>

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

<span data-ttu-id="51da3-223">Sebbene questo approccio sia molto efficiente, comporta un uso aggiuntivo dello stack.</span><span class="sxs-lookup"><span data-stu-id="51da3-223">While this approach is very heap efficient it does cause extra stack usage.</span></span> <span data-ttu-id="51da3-224">In un algoritmo che dispone di un deep stack e di molti `params` utilizzo è possibile che venga generata una `StackOverflowException` in cui una semplice allocazione `T[]` avrà esito positivo.</span><span class="sxs-lookup"><span data-stu-id="51da3-224">In an algorithm which has a deep stack and lots of `params` usage it's possible this could cause a `StackOverflowException` to be generated where a simple `T[]` allocation would succeed.</span></span> 

<span data-ttu-id="51da3-225">Sfortunatamente C# non è configurato per il tipo di analisi tra i metodi in cui è possibile determinare se la chiamata deve usare l'allocazione di stack o heap di `params`.</span><span class="sxs-lookup"><span data-stu-id="51da3-225">Unfortunately C# is not set up for the type of inter-method analysis where it could make an educated determination of whether or not call should use stack or heap allocation of `params`.</span></span> <span data-ttu-id="51da3-226">Può considerare solo ogni chiamata.</span><span class="sxs-lookup"><span data-stu-id="51da3-226">It can only really consider each call on its own.</span></span>

<span data-ttu-id="51da3-227">CLR è la configurazione migliore per eseguire questo tipo di determinazione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="51da3-227">The CLR is best setup for making this type of determination at runtime.</span></span> <span data-ttu-id="51da3-228">Di conseguenza, è probabile che il Runtime fornisca due metodi per l'allocazione dello stack universale:</span><span class="sxs-lookup"><span data-stu-id="51da3-228">Hence we'd likely have the runtime provide two methods for universal stack allocation:</span></span>

1. <span data-ttu-id="51da3-229">`Span<T> StackAlloc<T>(int length)`: presenta gli stessi comportamenti e limitazioni di `localloc` ad eccezione del fatto che può funzionare in qualsiasi `T`di tipo.</span><span class="sxs-lookup"><span data-stu-id="51da3-229">`Span<T> StackAlloc<T>(int length)`: this has the same behaviors and limitations of `localloc` except it can work on any type `T`.</span></span> 
1. <span data-ttu-id="51da3-230">`Span<T> MaybeStackAlloc<T>(int length)`: questo runtime può scegliere di implementare questa operazione eseguendo un'allocazione dello stack o dell'heap.</span><span class="sxs-lookup"><span data-stu-id="51da3-230">`Span<T> MaybeStackAlloc<T>(int length)`: this runtime can choose to implement this by doing a stack or heap allocation.</span></span> <span data-ttu-id="51da3-231">Il runtime può quindi usare il contesto di esecuzione in cui viene chiamato per determinare la modalità di allocazione del `Span<T>`.</span><span class="sxs-lookup"><span data-stu-id="51da3-231">The runtime can then use the execution context in which it's called to determine how the `Span<T>` is allocated.</span></span> <span data-ttu-id="51da3-232">Il chiamante tuttavia lo considera sempre come se fosse allocato nello stack.</span><span class="sxs-lookup"><span data-stu-id="51da3-232">The caller though will always treat it as if it were stack allocated.</span></span>

<span data-ttu-id="51da3-233">Per i casi molto semplici, ad esempio uno a due argomenti C# , il compilatore potrebbe sempre usare `StackAlloc<T>` Variant.</span><span class="sxs-lookup"><span data-stu-id="51da3-233">For very simple cases, like one to two arguments, the C# compiler could always use `StackAlloc<T>` variant.</span></span> <span data-ttu-id="51da3-234">Questo è improbabile che contribuisca significativamente all'esaurimento dello stack nella maggior parte dei casi.</span><span class="sxs-lookup"><span data-stu-id="51da3-234">This is unlikely to significantly contribute to stack exhaustion in most cases.</span></span> <span data-ttu-id="51da3-235">Per altri casi, il compilatore può scegliere di usare `MaybeStackAlloc<T>` e lasciare che il runtime effettui la chiamata.</span><span class="sxs-lookup"><span data-stu-id="51da3-235">For other cases the compiler could choose to use `MaybeStackAlloc<T>` instead and let the runtime make the call.</span></span>

<span data-ttu-id="51da3-236">Il modo in cui si sceglie probabilmente richiederà un'analisi più approfondita e l'analisi delle app reali.</span><span class="sxs-lookup"><span data-stu-id="51da3-236">How we choose will likely require a deeper investigation and examination of real world apps.</span></span> <span data-ttu-id="51da3-237">Tuttavia, se queste nuove funzioni intrinseche sono disponibili, questo tipo di flessibilità verrà fornito.</span><span class="sxs-lookup"><span data-stu-id="51da3-237">But if these new intrinsics are available then it will give us this type of flexibility.</span></span>

### <a name="why-not-varargs"></a><span data-ttu-id="51da3-238">Perché non varargs?</span><span class="sxs-lookup"><span data-stu-id="51da3-238">Why not varargs?</span></span> 
<span data-ttu-id="51da3-239">La funzionalità [varargs](https://docs.microsoft.com/en-us/cpp/windows/variable-argument-lists-dot-dot-dot-cpp-cli?view=vs-2017) esistente è stata considerata come una possibile soluzione.</span><span class="sxs-lookup"><span data-stu-id="51da3-239">The existing [varargs](https://docs.microsoft.com/en-us/cpp/windows/variable-argument-lists-dot-dot-dot-cpp-cli?view=vs-2017) feature was considered here as a possible solution.</span></span> <span data-ttu-id="51da3-240">Questa funzionalità è tuttavia destinata principalmente C++agli scenari/CLI e presenta buchi noti per altri scenari.</span><span class="sxs-lookup"><span data-stu-id="51da3-240">This feature though is meant primarily for C++/CLI scenarios and has known holes for other scenarios.</span></span> <span data-ttu-id="51da3-241">Il porting di questo a UNIX comporta inoltre un costo significativo.</span><span class="sxs-lookup"><span data-stu-id="51da3-241">Additionally there is significant cost in porting this to Unix.</span></span> <span data-ttu-id="51da3-242">Quindi non è stata considerata una soluzione valida.</span><span class="sxs-lookup"><span data-stu-id="51da3-242">Hence it wasn't seen as a viable solution.</span></span>

## <a name="related-issues"></a><span data-ttu-id="51da3-243">Problemi correlati</span><span class="sxs-lookup"><span data-stu-id="51da3-243">Related Issues</span></span>
<span data-ttu-id="51da3-244">Questa specifica è correlata ai problemi seguenti:</span><span class="sxs-lookup"><span data-stu-id="51da3-244">This spec is related to the following issues:</span></span> 

- https://github.com/dotnet/csharplang/issues/1757
- https://github.com/dotnet/csharplang/issues/179
- https://github.com/dotnet/corefxlab/pull/2595

