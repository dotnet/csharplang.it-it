---
ms.openlocfilehash: 39fb0aab5e8bb0d422f25fd2e92ab3d8256d3f59
ms.sourcegitcommit: b8f1103eb686c5d82e294837c9386d9b667da292
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "79485112"
---
# <a name="readonly-references"></a><span data-ttu-id="b6e58-101">Riferimenti di sola lettura</span><span class="sxs-lookup"><span data-stu-id="b6e58-101">Readonly references</span></span>

* <span data-ttu-id="b6e58-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="b6e58-102">[x] Proposed</span></span>
* <span data-ttu-id="b6e58-103">Prototipo [x]</span><span class="sxs-lookup"><span data-stu-id="b6e58-103">[x] Prototype</span></span>
* <span data-ttu-id="b6e58-104">Implementazione di [x]: avviata</span><span class="sxs-lookup"><span data-stu-id="b6e58-104">[x] Implementation: Started</span></span>
* <span data-ttu-id="b6e58-105">[] Specifica: non avviata</span><span class="sxs-lookup"><span data-stu-id="b6e58-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="b6e58-106">Summary</span><span class="sxs-lookup"><span data-stu-id="b6e58-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="b6e58-107">La funzionalità "riferimenti di sola lettura" è in realtà un gruppo di funzionalità che sfruttano l'efficienza del passaggio di variabili in base al riferimento, ma senza esporre i dati alle modifiche:</span><span class="sxs-lookup"><span data-stu-id="b6e58-107">The "readonly references" feature is actually a group of features that leverage the efficiency of passing variables by reference, but without exposing the data to modifications:</span></span>  
- <span data-ttu-id="b6e58-108">parametri `in`</span><span class="sxs-lookup"><span data-stu-id="b6e58-108">`in` parameters</span></span>
- <span data-ttu-id="b6e58-109">Valori restituiti `ref readonly`</span><span class="sxs-lookup"><span data-stu-id="b6e58-109">`ref readonly` returns</span></span>
- <span data-ttu-id="b6e58-110">struct di `readonly`</span><span class="sxs-lookup"><span data-stu-id="b6e58-110">`readonly` structs</span></span>
- <span data-ttu-id="b6e58-111">`ref`/metodi di estensione `in`</span><span class="sxs-lookup"><span data-stu-id="b6e58-111">`ref`/`in` extension methods</span></span>
- <span data-ttu-id="b6e58-112">variabili locali `ref readonly`</span><span class="sxs-lookup"><span data-stu-id="b6e58-112">`ref readonly` locals</span></span>
- <span data-ttu-id="b6e58-113">`ref` espressioni condizionali</span><span class="sxs-lookup"><span data-stu-id="b6e58-113">`ref` conditional expressions</span></span>

## <a name="passing-arguments-as-readonly-references"></a><span data-ttu-id="b6e58-114">Passaggio di argomenti come riferimenti di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="b6e58-114">Passing arguments as readonly references.</span></span>

<span data-ttu-id="b6e58-115">Esiste una proposta esistente che tocca questo argomento https://github.com/dotnet/roslyn/issues/115 come un caso speciale di parametri ReadOnly senza passare a molti dettagli.</span><span class="sxs-lookup"><span data-stu-id="b6e58-115">There is an existing proposal that touches this topic https://github.com/dotnet/roslyn/issues/115 as a special case of readonly parameters without going into many details.</span></span>
<span data-ttu-id="b6e58-116">Qui voglio solo confermare che l'idea non è stata molto nuova.</span><span class="sxs-lookup"><span data-stu-id="b6e58-116">Here I just want to acknowledge that the idea by itself is not very new.</span></span>

### <a name="motivation"></a><span data-ttu-id="b6e58-117">Motivazione</span><span class="sxs-lookup"><span data-stu-id="b6e58-117">Motivation</span></span>

<span data-ttu-id="b6e58-118">Prima di questa funzionalità C# non disponeva di un modo efficiente per esprimere il desiderio di passare le variabili struct nelle chiamate al metodo per le operazioni di sola lettura senza alcuna modifica.</span><span class="sxs-lookup"><span data-stu-id="b6e58-118">Prior to this feature C# did not have an efficient way of expressing a desire to pass struct variables into method calls for readonly purposes with no intention of modifying.</span></span> <span data-ttu-id="b6e58-119">Il passaggio di un argomento per valore normale implica la copia, che aggiunge costi superflui.</span><span class="sxs-lookup"><span data-stu-id="b6e58-119">Regular by-value argument passing implies copying, which adds unnecessary costs.</span></span>  <span data-ttu-id="b6e58-120">Che consente agli utenti di usare l'argomento per-Ref passando e si basano su Commenti/documentazione per indicare che i dati non devono essere mutati dal chiamato.</span><span class="sxs-lookup"><span data-stu-id="b6e58-120">That drives users to use by-ref argument passing and rely on comments/documentation to indicate that the data is not supposed to be mutated by the callee.</span></span> <span data-ttu-id="b6e58-121">Non è una soluzione ideale per molti motivi.</span><span class="sxs-lookup"><span data-stu-id="b6e58-121">It is not a good solution for many reasons.</span></span>  
<span data-ttu-id="b6e58-122">Gli esempi sono gli operatori matematici di tipo Vector/Matrix nelle librerie grafiche, come [XNA](https://msdn.microsoft.com/library/bb194944.aspx) , che hanno operandi di riferimento esclusivamente a causa di considerazioni sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="b6e58-122">The examples are numerous - vector/matrix math operators in graphics libraries like [XNA](https://msdn.microsoft.com/library/bb194944.aspx) are known to have ref operands purely because of performance considerations.</span></span> <span data-ttu-id="b6e58-123">Nel compilatore Roslyn è presente codice che usa gli struct per evitare allocazioni e quindi li passa per riferimento per evitare di copiare i costi.</span><span class="sxs-lookup"><span data-stu-id="b6e58-123">There is code in Roslyn compiler itself that uses structs to avoid allocations and then passes them by reference to avoid copying costs.</span></span>

### <a name="solution-in-parameters"></a><span data-ttu-id="b6e58-124">Soluzione (parametri`in`)</span><span class="sxs-lookup"><span data-stu-id="b6e58-124">Solution (`in` parameters)</span></span>

<span data-ttu-id="b6e58-125">Analogamente ai parametri `out`, `in` parametri vengono passati come riferimenti gestiti con garanzie aggiuntive da parte del chiamato.</span><span class="sxs-lookup"><span data-stu-id="b6e58-125">Similarly to the `out` parameters, `in` parameters are passed as managed references with additional guarantees from the callee.</span></span>  
<span data-ttu-id="b6e58-126">A differenza dei parametri di `out` che _devono_ essere assegnati dal chiamato prima di qualsiasi altro uso, non è possibile assegnare `in` parametri al chiamato.</span><span class="sxs-lookup"><span data-stu-id="b6e58-126">Unlike `out` parameters which _must_ be assigned by the callee before any other use, `in` parameters cannot be assigned by the callee at all.</span></span>

<span data-ttu-id="b6e58-127">Di conseguenza `in` parametri consentono l'efficacia del passaggio di argomenti indiretti senza esporre argomenti alle mutazioni da parte del chiamato.</span><span class="sxs-lookup"><span data-stu-id="b6e58-127">As a result `in` parameters allow for effectiveness of indirect argument passing without exposing arguments to mutations by the callee.</span></span>

### <a name="declaring-in-parameters"></a><span data-ttu-id="b6e58-128">Dichiarazione di parametri `in`</span><span class="sxs-lookup"><span data-stu-id="b6e58-128">Declaring `in` parameters</span></span>

<span data-ttu-id="b6e58-129">`in` parametri vengono dichiarati utilizzando `in` parola chiave come modificatore nella firma del parametro.</span><span class="sxs-lookup"><span data-stu-id="b6e58-129">`in` parameters are declared by using `in` keyword as a modifier in the parameter signature.</span></span>

<span data-ttu-id="b6e58-130">Per tutti gli scopi, il parametro `in` viene considerato come una variabile di `readonly`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-130">For all purposes the `in` parameter is treated as a `readonly` variable.</span></span> <span data-ttu-id="b6e58-131">La maggior parte delle restrizioni relative all'uso dei parametri `in` all'interno del metodo è identica a quella dei campi `readonly`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-131">Most of the restrictions on the use of `in` parameters inside the method are the same as with `readonly` fields.</span></span>

> <span data-ttu-id="b6e58-132">Un parametro `in` può infatti rappresentare un campo di `readonly`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-132">Indeed an `in` parameter may represent a `readonly` field.</span></span> <span data-ttu-id="b6e58-133">La somiglianza delle restrizioni non è una coincidenza.</span><span class="sxs-lookup"><span data-stu-id="b6e58-133">Similarity of restrictions is not a coincidence.</span></span>

<span data-ttu-id="b6e58-134">Per esempio, i campi di un parametro di `in` con un tipo struct sono ricorsivi in modo ricorsivo come `readonly` variabili.</span><span class="sxs-lookup"><span data-stu-id="b6e58-134">For example fields of an `in` parameter which has a struct type are all recursively classified as `readonly` variables .</span></span>

```csharp
static Vector3 Add (in Vector3 v1, in Vector3 v2)
{
    // not OK!!
    v1 = default(Vector3);

    // not OK!!
    v1.X = 0;

    // not OK!!
    foo(ref v1.X);

    // OK
    return new Vector3(v1.X + v2.X, v1.Y + v2.Y, v1.Z + v2.Z);
}
```

- <span data-ttu-id="b6e58-135">`in` parametri sono consentiti in qualsiasi punto in cui sono consentiti parametri ByVal comuni.</span><span class="sxs-lookup"><span data-stu-id="b6e58-135">`in` parameters are allowed anywhere where ordinary byval parameters are allowed.</span></span> <span data-ttu-id="b6e58-136">Sono inclusi gli indicizzatori, gli operatori (incluse le conversioni), i delegati, le espressioni lambda, le funzioni locali.</span><span class="sxs-lookup"><span data-stu-id="b6e58-136">This includes indexers, operators (including conversions), delegates, lambdas, local functions.</span></span>

> ```csharp
>  (in int x) => x                                                     // lambda expression  
>  TValue this[in TKey index];                                         // indexer
>  public static Vector3 operator +(in Vector3 x, in Vector3 y) => ... // operator
>  ```

- <span data-ttu-id="b6e58-137">`in` non è consentita in combinazione con `out` o con qualsiasi elemento che `out` non è combinato con.</span><span class="sxs-lookup"><span data-stu-id="b6e58-137">`in` is not allowed in combination with `out` or with anything that `out` does not combine with.</span></span>

- <span data-ttu-id="b6e58-138">Non è consentito eseguire l'overload in `ref`/`out`/differenze `in`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-138">It is not permitted to overload on `ref`/`out`/`in` differences.</span></span>

- <span data-ttu-id="b6e58-139">È consentita l'overload delle differenze comuni tra ByVal e `in`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-139">It is permitted to overload on ordinary byval and `in` differences.</span></span>

- <span data-ttu-id="b6e58-140">Ai fini di OHI (overload, nascondiglio, implementazione) `in` si comporta in modo analogo a un parametro di `out`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-140">For the purpose of OHI (Overloading, Hiding, Implementing), `in` behaves similarly to an `out` parameter.</span></span>
<span data-ttu-id="b6e58-141">Si applicano tutte le stesse regole.</span><span class="sxs-lookup"><span data-stu-id="b6e58-141">All the same rules apply.</span></span>
<span data-ttu-id="b6e58-142">Ad esempio, il metodo di override dovrà corrispondere `in` parametri con `in` parametri di un tipo convertibile in identità.</span><span class="sxs-lookup"><span data-stu-id="b6e58-142">For example the overriding method will have to match `in` parameters with `in` parameters of an identity-convertible type.</span></span>

- <span data-ttu-id="b6e58-143">Per le conversioni di gruppi di delegati/lambda/metodi, `in` si comporta in modo analogo a un parametro di `out`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-143">For the purpose of delegate/lambda/method group conversions, `in` behaves similarly to an `out` parameter.</span></span>
<span data-ttu-id="b6e58-144">Le espressioni lambda e i candidati per la conversione dei gruppi di metodi applicabili dovranno corrispondere `in` parametri del delegato di destinazione con `in` parametri di un tipo convertibile in identità.</span><span class="sxs-lookup"><span data-stu-id="b6e58-144">Lambdas and applicable method group conversion candidates will have to match `in` parameters of the target delegate with `in` parameters of an identity-convertible type.</span></span>

- <span data-ttu-id="b6e58-145">Ai fini della varianza generica, i parametri `in` sono non variant.</span><span class="sxs-lookup"><span data-stu-id="b6e58-145">For the purpose of generic variance, `in` parameters are nonvariant.</span></span>

> <span data-ttu-id="b6e58-146">Nota: non sono presenti avvisi nei parametri `in` con tipi di riferimento o primitivi.</span><span class="sxs-lookup"><span data-stu-id="b6e58-146">NOTE: There are no warnings on `in` parameters that have reference or primitives types.</span></span>
<span data-ttu-id="b6e58-147">Potrebbe essere inutile in generale, ma in alcuni casi l'utente deve/vuole passare le primitive come `in`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-147">It may be pointless in general, but in some cases user must/want to pass primitives as `in`.</span></span> <span data-ttu-id="b6e58-148">Esempi: override di un metodo generico come `Method(in T param)` quando `T` è stato sostituito da `int`o quando si hanno metodi come `Volatile.Read(in int location)`</span><span class="sxs-lookup"><span data-stu-id="b6e58-148">Examples - overriding a generic method like `Method(in T param)` when `T` was substituted to be `int`, or when having methods like `Volatile.Read(in int location)`</span></span>
>
> <span data-ttu-id="b6e58-149">È preferibile disporre di un analizzatore in grado di avvisare in caso di uso inefficiente di parametri di `in`, ma le regole per tale analisi sarebbero troppo fuzzy per essere parte di una specifica del linguaggio.</span><span class="sxs-lookup"><span data-stu-id="b6e58-149">It is conceivable to have an analyzer that warns in cases of inefficient use of `in` parameters, but the rules for such analysis would be too fuzzy to be a part of a language specification.</span></span>

### <a name="use-of-in-at-call-sites-in-arguments"></a><span data-ttu-id="b6e58-150">Uso di `in` nei siti di chiamata.</span><span class="sxs-lookup"><span data-stu-id="b6e58-150">Use of `in` at call sites.</span></span> <span data-ttu-id="b6e58-151">(argomenti`in`)</span><span class="sxs-lookup"><span data-stu-id="b6e58-151">(`in` arguments)</span></span>

<span data-ttu-id="b6e58-152">Esistono due modi per passare argomenti a `in` parametri.</span><span class="sxs-lookup"><span data-stu-id="b6e58-152">There are two ways to pass arguments to `in` parameters.</span></span>

#### <a name="in-arguments-can-match-in-parameters"></a><span data-ttu-id="b6e58-153">`in` argomenti possono corrispondere `in` parametri:</span><span class="sxs-lookup"><span data-stu-id="b6e58-153">`in` arguments can match `in` parameters:</span></span>

<span data-ttu-id="b6e58-154">Un argomento con un modificatore `in` nel sito di chiamata può corrispondere `in` parametri.</span><span class="sxs-lookup"><span data-stu-id="b6e58-154">An argument with an `in` modifier at the call site can match `in` parameters.</span></span>

```csharp
int x = 1;

void M1<T>(in T x)
{
  // . . .
}

var x = M1(in x);  // in argument to a method

class D
{
    public string this[in Guid index];
}

D dictionary = . . . ;
var y = dictionary[in Guid.Empty]; // in argument to an indexer
```

- <span data-ttu-id="b6e58-155">`in` argomento deve essere un lvalue _leggibile_ (\*).</span><span class="sxs-lookup"><span data-stu-id="b6e58-155">`in` argument must be a _readable_ LValue(\*).</span></span>
<span data-ttu-id="b6e58-156">Esempio: `M1(in 42)` non è valido</span><span class="sxs-lookup"><span data-stu-id="b6e58-156">Example: `M1(in 42)` is invalid</span></span>

> <span data-ttu-id="b6e58-157">(\*) Il concetto di [lvalue/rvalue](https://en.wikipedia.org/wiki/Value_(computer_science)#lrvalue) varia tra le lingue.</span><span class="sxs-lookup"><span data-stu-id="b6e58-157">(\*) The notion of [LValue/RValue](https://en.wikipedia.org/wiki/Value_(computer_science)#lrvalue) vary between languages.</span></span>  
<span data-ttu-id="b6e58-158">Qui, da LValue, intendo un'espressione che rappresenta una posizione a cui è possibile fare riferimento direttamente.</span><span class="sxs-lookup"><span data-stu-id="b6e58-158">Here, by LValue I mean an expression that represent a location that can be referred to directly.</span></span>
<span data-ttu-id="b6e58-159">E RValue indica un'espressione che produce un risultato temporaneo che non viene mantenuto autonomamente.</span><span class="sxs-lookup"><span data-stu-id="b6e58-159">And RValue means an expression that yields a temporary result which does not persist on its own.</span></span>  

- <span data-ttu-id="b6e58-160">In particolare, è possibile passare `readonly` campi, `in` parametri o altre variabili formalmente `readonly` come argomenti `in`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-160">In particular it is valid to pass `readonly` fields, `in` parameters or other formally `readonly` variables as `in` arguments.</span></span>
<span data-ttu-id="b6e58-161">Esempio: `dictionary[in Guid.Empty]` è valido.</span><span class="sxs-lookup"><span data-stu-id="b6e58-161">Example: `dictionary[in Guid.Empty]` is legal.</span></span> <span data-ttu-id="b6e58-162">`Guid.Empty` è un campo di sola lettura statico.</span><span class="sxs-lookup"><span data-stu-id="b6e58-162">`Guid.Empty` is a static readonly field.</span></span>

- <span data-ttu-id="b6e58-163">`in` argomento deve avere identità del tipo _convertibile_ nel tipo di parametro.</span><span class="sxs-lookup"><span data-stu-id="b6e58-163">`in` argument must have type _identity-convertible_ to the type of the parameter.</span></span>
<span data-ttu-id="b6e58-164">Esempio: `M1<object>(in Guid.Empty)` non è valido.</span><span class="sxs-lookup"><span data-stu-id="b6e58-164">Example: `M1<object>(in Guid.Empty)` is invalid.</span></span> <span data-ttu-id="b6e58-165">`Guid.Empty` non è _convertibile in identità_ in `object`</span><span class="sxs-lookup"><span data-stu-id="b6e58-165">`Guid.Empty` is not _identity-convertible_ to `object`</span></span>

<span data-ttu-id="b6e58-166">La motivazione per le regole precedenti è che `in` argomenti garantiscono l' _aliasing_ della variabile dell'argomento.</span><span class="sxs-lookup"><span data-stu-id="b6e58-166">The motivation for the above rules is that `in` arguments guarantee _aliasing_ of the argument variable.</span></span> <span data-ttu-id="b6e58-167">Il chiamato riceve sempre un riferimento diretto alla stessa posizione rappresentata dall'argomento.</span><span class="sxs-lookup"><span data-stu-id="b6e58-167">The callee always receives a direct reference to the same location as represented by the argument.</span></span>

- <span data-ttu-id="b6e58-168">in rari casi in cui gli argomenti di `in` devono essere distribuiti in modo stack a causa di espressioni `await` utilizzate come operandi della stessa chiamata, il comportamento è identico a quello degli argomenti `out` e `ref`. se la variabile non può essere distribuita in modo trasparente, viene restituito un errore.</span><span class="sxs-lookup"><span data-stu-id="b6e58-168">in rare situations when `in` arguments must be stack-spilled due to `await` expressions used as operands of the same call, the behavior is the same as with `out` and `ref` arguments - if the variable cannot be spilled in referentially-transparent manner, an error is reported.</span></span>

<span data-ttu-id="b6e58-169">Esempi:</span><span class="sxs-lookup"><span data-stu-id="b6e58-169">Examples:</span></span>
1. <span data-ttu-id="b6e58-170">`M1(in staticField, await SomethingAsync())` valido.</span><span class="sxs-lookup"><span data-stu-id="b6e58-170">`M1(in staticField, await SomethingAsync())`  is valid.</span></span>
<span data-ttu-id="b6e58-171">`staticField` è un campo statico a cui è possibile accedere più di una volta senza effetti collaterali osservabili.</span><span class="sxs-lookup"><span data-stu-id="b6e58-171">`staticField` is a static field which can be accessed more than once without observable side effects.</span></span> <span data-ttu-id="b6e58-172">È quindi possibile fornire sia l'ordine degli effetti collaterali che i requisiti di aliasing.</span><span class="sxs-lookup"><span data-stu-id="b6e58-172">Therefore both the order of side effects and aliasing requirements can be provided.</span></span>
2. <span data-ttu-id="b6e58-173">`M1(in RefReturningMethod(), await SomethingAsync())` produrrà un errore.</span><span class="sxs-lookup"><span data-stu-id="b6e58-173">`M1(in RefReturningMethod(), await SomethingAsync())`  will produce an error.</span></span>
<span data-ttu-id="b6e58-174">`RefReturningMethod()` è un `ref` metodo restituito.</span><span class="sxs-lookup"><span data-stu-id="b6e58-174">`RefReturningMethod()` is a `ref` returning method.</span></span> <span data-ttu-id="b6e58-175">Una chiamata al metodo può avere effetti collaterali osservabili, pertanto deve essere valutata prima dell'operando `SomethingAsync()`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-175">A method call may have observable side effects, therefore it must be evaluated before the `SomethingAsync()` operand.</span></span> <span data-ttu-id="b6e58-176">Tuttavia, il risultato della chiamata è un riferimento che non può essere mantenuto nell'`await` punto di sospensione che rende impossibile il requisito di riferimento diretto.</span><span class="sxs-lookup"><span data-stu-id="b6e58-176">However the result of the invocation is a reference that cannot be preserved across the `await` suspension point which make the direct reference requirement impossible.</span></span>

> <span data-ttu-id="b6e58-177">Nota: gli errori di distribuzione dello stack sono considerati limitazioni specifiche dell'implementazione.</span><span class="sxs-lookup"><span data-stu-id="b6e58-177">NOTE: the stack spilling errors are considered to be implementation-specific limitations.</span></span> <span data-ttu-id="b6e58-178">Pertanto non hanno effetto sulla risoluzione dell'overload o sull'inferenza lambda.</span><span class="sxs-lookup"><span data-stu-id="b6e58-178">Therefore they do not have effect on overload resolution or lambda inference.</span></span>

#### <a name="ordinary-byval-arguments-can-match-in-parameters"></a><span data-ttu-id="b6e58-179">Gli argomenti ByVal comuni possono corrispondere `in` parametri:</span><span class="sxs-lookup"><span data-stu-id="b6e58-179">Ordinary byval arguments can match `in` parameters:</span></span>

<span data-ttu-id="b6e58-180">Gli argomenti regolari senza modificatori possono corrispondere a `in` parametri.</span><span class="sxs-lookup"><span data-stu-id="b6e58-180">Regular arguments without modifiers can match `in` parameters.</span></span> <span data-ttu-id="b6e58-181">In tal caso, gli argomenti hanno gli stessi vincoli rilassati di un normale argomento ByVal.</span><span class="sxs-lookup"><span data-stu-id="b6e58-181">In such case the arguments have the same relaxed constraints as an ordinary byval arguments would have.</span></span>

<span data-ttu-id="b6e58-182">La motivazione di questo scenario è che `in` parametri nelle API possono comportare inconvenienti per l'utente quando non è possibile passare gli argomenti come risultati di riferimento diretto, ad esempio valori letterali, calcolati o `await`ed argomenti che hanno tipi più specifici.</span><span class="sxs-lookup"><span data-stu-id="b6e58-182">The motivation for this scenario is that `in` parameters in APIs may result in inconveniences for the user when arguments cannot be passed as a direct reference - ex: literals, computed or `await`-ed results or arguments that happen to have more specific types.</span></span>  
<span data-ttu-id="b6e58-183">Tutti questi casi hanno una soluzione semplice per archiviare il valore dell'argomento in una lingua locale temporanea di tipo appropriato e passare tale oggetto locale come argomento `in`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-183">All these cases have a trivial solution of storing the argument value in a temporary local of appropriate type and passing that local as an `in` argument.</span></span>  
<span data-ttu-id="b6e58-184">Per ridurre la necessità del compilatore di codice standard, è possibile eseguire la stessa trasformazione, se necessario, quando `in` modificatore non è presente nel sito di chiamata.</span><span class="sxs-lookup"><span data-stu-id="b6e58-184">To reduce the need for such boilerplate code compiler can perform the same transformation, if needed, when `in` modifier is not present at the call site.</span></span>  

<span data-ttu-id="b6e58-185">Inoltre, in alcuni casi, ad esempio la chiamata di operatori o i metodi di estensione `in`, non esiste alcun modo sintattico per specificare `in`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-185">In addition, in some cases, such as invocation of operators, or `in` extension methods, there is no syntactical way to specify `in` at all.</span></span> <span data-ttu-id="b6e58-186">Questo solo richiede la specifica del comportamento degli argomenti ByVal comuni quando corrispondono `in` parametri.</span><span class="sxs-lookup"><span data-stu-id="b6e58-186">That alone requires specifying the behavior of ordinary byval arguments when they match `in` parameters.</span></span>

<span data-ttu-id="b6e58-187">In particolare:</span><span class="sxs-lookup"><span data-stu-id="b6e58-187">In particular:</span></span>

- <span data-ttu-id="b6e58-188">il passaggio di RValues è valido.</span><span class="sxs-lookup"><span data-stu-id="b6e58-188">it is valid to pass RValues.</span></span>
<span data-ttu-id="b6e58-189">Un riferimento a un oggetto temporaneo viene passato in questo caso.</span><span class="sxs-lookup"><span data-stu-id="b6e58-189">A reference to a temporary is passed in such case.</span></span>
<span data-ttu-id="b6e58-190">Esempio:</span><span class="sxs-lookup"><span data-stu-id="b6e58-190">Example:</span></span>
```csharp
Print("hello");      // not an error.

void Print<T>(in T x)
{
  //. . .
}
```

- <span data-ttu-id="b6e58-191">sono consentite conversioni implicite.</span><span class="sxs-lookup"><span data-stu-id="b6e58-191">implicit conversions are allowed.</span></span>

> <span data-ttu-id="b6e58-192">Si tratta in realtà di un caso speciale di passaggio di un RValue</span><span class="sxs-lookup"><span data-stu-id="b6e58-192">This is actually a special case of passing an RValue</span></span>  

<span data-ttu-id="b6e58-193">In tal caso, viene passato un riferimento a un valore convertito temporaneo.</span><span class="sxs-lookup"><span data-stu-id="b6e58-193">A reference to a temporary holding converted value is passed in such case.</span></span>
<span data-ttu-id="b6e58-194">Esempio:</span><span class="sxs-lookup"><span data-stu-id="b6e58-194">Example:</span></span>
```csharp
Print<int>(Short.MaxValue)     // not an error.
```

- <span data-ttu-id="b6e58-195">nel caso di un destinatario di un metodo di estensione `in`, anziché `ref` metodi di estensione, sono consentite le _conversioni_ di RValues o implicite.</span><span class="sxs-lookup"><span data-stu-id="b6e58-195">in a case of a receiver of an `in` extension method (as opposed to `ref` extension methods), RValues or implicit _this-argument-conversions_ are allowed.</span></span>
<span data-ttu-id="b6e58-196">In tal caso, viene passato un riferimento a un valore convertito temporaneo.</span><span class="sxs-lookup"><span data-stu-id="b6e58-196">A reference to a temporary holding converted value is passed in such case.</span></span>
<span data-ttu-id="b6e58-197">Esempio:</span><span class="sxs-lookup"><span data-stu-id="b6e58-197">Example:</span></span>
```csharp
public static IEnumerable<T> Concat<T>(in this (IEnumerable<T>, IEnumerable<T>) arg)  => . . .;

("aa", "bb").Concat<char>()    // not an error.
```
<span data-ttu-id="b6e58-198">Altre informazioni su `ref`/`in` metodi di estensione sono fornite più avanti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="b6e58-198">More information on `ref`/`in` extension methods is provided further in this document.</span></span>

- <span data-ttu-id="b6e58-199">la distribuzione di argomenti a causa di `await` operandi potrebbe riportare "per valore", se necessario.</span><span class="sxs-lookup"><span data-stu-id="b6e58-199">argument spilling due to `await` operands could spill "by-value", if necessary.</span></span>
<span data-ttu-id="b6e58-200">Negli scenari in cui non è possibile fornire un riferimento diretto all'argomento a causa del `await` intermedio, viene invece distribuita una copia del valore dell'argomento.</span><span class="sxs-lookup"><span data-stu-id="b6e58-200">In scenarios where providing a direct reference to the argument is not possible due to intervening `await` a copy of the argument's value is spilled instead.</span></span>  
<span data-ttu-id="b6e58-201">Esempio:</span><span class="sxs-lookup"><span data-stu-id="b6e58-201">Example:</span></span>
```csharp
M1(RefReturningMethod(), await SomethingAsync())   // not an error.
```
<span data-ttu-id="b6e58-202">Poiché il risultato di una chiamata di effetto collaterale è un riferimento che non può essere mantenuto tra `await` sospensione, viene invece mantenuto un oggetto temporaneo che contiene il valore effettivo (come in un caso di parametro ByVal comune).</span><span class="sxs-lookup"><span data-stu-id="b6e58-202">Since the result of a side-effecting invocation is a reference that cannot be preserved across `await` suspension, a temporary containing the actual value will be preserved instead (as it would in an ordinary byval parameter case).</span></span>

#### <a name="omitted-optional-arguments"></a><span data-ttu-id="b6e58-203">Argomenti facoltativi omessi</span><span class="sxs-lookup"><span data-stu-id="b6e58-203">Omitted optional arguments</span></span>

<span data-ttu-id="b6e58-204">È consentito che un parametro `in` specifichi un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="b6e58-204">It is permitted for an `in` parameter to specify a default value.</span></span> <span data-ttu-id="b6e58-205">Che rende facoltativo l'argomento corrispondente.</span><span class="sxs-lookup"><span data-stu-id="b6e58-205">That makes the corresponding argument optional.</span></span>

<span data-ttu-id="b6e58-206">Se si omette l'argomento facoltativo nel sito di chiamata, il valore predefinito viene passato tramite un oggetto temporaneo.</span><span class="sxs-lookup"><span data-stu-id="b6e58-206">Omitting optional argument at the call site results in passing the default value via a temporary.</span></span>

```csharp
Print("hello");      // not an error, same as
Print("hello", c: Color.Black);

void Print(string s, in Color c = Color.Black)
{
    // . . .
}
```

### <a name="aliasing-behavior-in-general"></a><span data-ttu-id="b6e58-207">Comportamento degli alias in generale</span><span class="sxs-lookup"><span data-stu-id="b6e58-207">Aliasing behavior in general</span></span>

<span data-ttu-id="b6e58-208">Proprio come `ref` e `out` variabili, `in` le variabili sono riferimenti/alias alle posizioni esistenti.</span><span class="sxs-lookup"><span data-stu-id="b6e58-208">Just like `ref` and `out` variables, `in` variables are references/aliases to existing locations.</span></span>

<span data-ttu-id="b6e58-209">Sebbene il chiamato non sia autorizzato a scrivere in essi, la lettura di un parametro `in` può osservare valori diversi come effetto collaterale di altre valutazioni.</span><span class="sxs-lookup"><span data-stu-id="b6e58-209">While callee is not allowed to write into them, reading an `in` parameter can observe different values as a side effect of other evaluations.</span></span>

<span data-ttu-id="b6e58-210">Esempio:</span><span class="sxs-lookup"><span data-stu-id="b6e58-210">Example:</span></span>

```C#
static Vector3 v = Vector3.UnitY;

static void Main()
{
    Test(v);
}

static void Test(in Vector3 v1)
{
    Debug.Assert(v1 == Vector3.UnitY);
    // changes v1 deterministically (no races required)
    ChangeV();
    Debug.Assert(v1 == Vector3.UnitX);
}

static void ChangeV()
{
    v = Vector3.UnitX;
}
```

### <a name="in-parameters-and-capturing-of-local-variables"></a><span data-ttu-id="b6e58-211">`in` i parametri e l'acquisizione di variabili locali.</span><span class="sxs-lookup"><span data-stu-id="b6e58-211">`in` parameters and capturing of local variables.</span></span>  
<span data-ttu-id="b6e58-212">Ai fini dell'acquisizione lambda/Async `in` parametri si comportano come `out` e `ref` parametri.</span><span class="sxs-lookup"><span data-stu-id="b6e58-212">For the purpose of lambda/async capturing `in` parameters behave the same as `out` and `ref` parameters.</span></span>

- <span data-ttu-id="b6e58-213">Impossibile acquisire `in` parametri in una chiusura</span><span class="sxs-lookup"><span data-stu-id="b6e58-213">`in` parameters cannot be captured in a closure</span></span>
- <span data-ttu-id="b6e58-214">parametri di `in` non consentiti nei metodi iterator</span><span class="sxs-lookup"><span data-stu-id="b6e58-214">`in` parameters are not allowed in iterator methods</span></span>
- <span data-ttu-id="b6e58-215">parametri di `in` non consentiti nei metodi asincroni</span><span class="sxs-lookup"><span data-stu-id="b6e58-215">`in` parameters are not allowed in async methods</span></span>

### <a name="temporary-variables"></a><span data-ttu-id="b6e58-216">Variabili temporanee.</span><span class="sxs-lookup"><span data-stu-id="b6e58-216">Temporary variables.</span></span>  
<span data-ttu-id="b6e58-217">Alcuni usi del passaggio `in` parametro possono richiedere l'uso indiretto di una variabile locale temporanea:</span><span class="sxs-lookup"><span data-stu-id="b6e58-217">Some uses of `in` parameter passing may require indirect use of a temporary local variable:</span></span>  
- <span data-ttu-id="b6e58-218">`in` argomenti vengono sempre passati come alias diretti quando il sito di chiamata USA `in`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-218">`in` arguments are always passed as direct aliases when call-site uses `in`.</span></span> <span data-ttu-id="b6e58-219">Il tipo temporaneo non viene mai usato in questo caso.</span><span class="sxs-lookup"><span data-stu-id="b6e58-219">Temporary is never used in such case.</span></span>
- <span data-ttu-id="b6e58-220">non è necessario che gli argomenti `in` siano alias diretti quando il sito di chiamata non usa `in`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-220">`in` arguments are not required to be direct aliases when call-site does not use `in`.</span></span> <span data-ttu-id="b6e58-221">Quando argument non è un LValue, è possibile usare un oggetto temporaneo.</span><span class="sxs-lookup"><span data-stu-id="b6e58-221">When argument is not an LValue, a temporary may be used.</span></span>
- <span data-ttu-id="b6e58-222">`in` parametro può avere un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="b6e58-222">`in` parameter may have default value.</span></span> <span data-ttu-id="b6e58-223">Quando l'argomento corrispondente viene omesso nel sito di chiamata, il valore predefinito viene passato tramite un oggetto temporaneo.</span><span class="sxs-lookup"><span data-stu-id="b6e58-223">When corresponding argument is omitted at the call site, the default value are passed via a temporary.</span></span>
- <span data-ttu-id="b6e58-224">`in` argomenti possono avere conversioni implicite, incluse quelle che non conservano l'identità.</span><span class="sxs-lookup"><span data-stu-id="b6e58-224">`in` arguments may have implicit conversions, including those that do not preserve identity.</span></span> <span data-ttu-id="b6e58-225">In questi casi viene usato un oggetto temporaneo.</span><span class="sxs-lookup"><span data-stu-id="b6e58-225">A temporary is used in those cases.</span></span>
- <span data-ttu-id="b6e58-226">i ricevitori di chiamate struct ordinarie non possono essere scrivibili lvalue (**case esistente!** ).</span><span class="sxs-lookup"><span data-stu-id="b6e58-226">receivers of ordinary struct calls may not be writeable LValues (**existing case!**).</span></span> <span data-ttu-id="b6e58-227">In questi casi viene usato un oggetto temporaneo.</span><span class="sxs-lookup"><span data-stu-id="b6e58-227">A temporary is used in those cases.</span></span>

<span data-ttu-id="b6e58-228">Il tempo di vita dell'argomento temporaries corrisponde all'ambito più vicino a quello del sito di chiamata.</span><span class="sxs-lookup"><span data-stu-id="b6e58-228">The life time of the argument temporaries matches the closest encompassing scope of the call-site.</span></span>

<span data-ttu-id="b6e58-229">Il tempo di vita formale delle variabili temporanee è semanticamente significativo in scenari che coinvolgono l'analisi di escape delle variabili restituite per riferimento.</span><span class="sxs-lookup"><span data-stu-id="b6e58-229">The formal life time of temporary variables is semantically significant in scenarios involving escape analysis of variables returned by reference.</span></span>

### <a name="metadata-representation-of-in-parameters"></a><span data-ttu-id="b6e58-230">Rappresentazione dei metadati di `in` parametri.</span><span class="sxs-lookup"><span data-stu-id="b6e58-230">Metadata representation of `in` parameters.</span></span>
<span data-ttu-id="b6e58-231">Quando `System.Runtime.CompilerServices.IsReadOnlyAttribute` viene applicato a un parametro ByRef, significa che il parametro è un parametro di `in`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-231">When `System.Runtime.CompilerServices.IsReadOnlyAttribute` is applied to a byref parameter, it means that the parameter is an `in` parameter.</span></span>

<span data-ttu-id="b6e58-232">Inoltre, se il metodo è *astratto* o *virtuale*, la firma di tali parametri (e solo tali parametri) deve avere `modreq[System.Runtime.InteropServices.InAttribute]`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-232">In addition, if the method is *abstract* or *virtual*, then the signature of such parameters (and only such parameters) must have `modreq[System.Runtime.InteropServices.InAttribute]`.</span></span>

<span data-ttu-id="b6e58-233">**Motivazione**: questa operazione viene eseguita per assicurarsi che, in caso di override o implementazione del metodo, i parametri di `in` corrispondano.</span><span class="sxs-lookup"><span data-stu-id="b6e58-233">**Motivation**: this is done to ensure that in a case of method overriding/implementing the `in` parameters match.</span></span>

<span data-ttu-id="b6e58-234">Gli stessi requisiti si applicano ai metodi `Invoke` nei delegati.</span><span class="sxs-lookup"><span data-stu-id="b6e58-234">Same requirements apply to `Invoke` methods in delegates.</span></span>

<span data-ttu-id="b6e58-235">**Motivazione**: ciò consente di garantire che i compilatori esistenti non possano semplicemente ignorare `readonly` durante la creazione o l'assegnazione di delegati.</span><span class="sxs-lookup"><span data-stu-id="b6e58-235">**Motivation**: this is to ensure that existing compilers cannot simply ignore `readonly` when creating or assigning delegates.</span></span>

## <a name="returning-by-readonly-reference"></a><span data-ttu-id="b6e58-236">Restituzione per riferimento di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="b6e58-236">Returning by readonly reference.</span></span>

### <a name="motivation"></a><span data-ttu-id="b6e58-237">Motivazione</span><span class="sxs-lookup"><span data-stu-id="b6e58-237">Motivation</span></span>
<span data-ttu-id="b6e58-238">La motivazione di questa funzionalità secondaria è approssimativamente simmetrica per i motivi della `in` parametri, evitando la copia, ma sul lato di ritorno.</span><span class="sxs-lookup"><span data-stu-id="b6e58-238">The motivation for this sub-feature is roughly symmetrical to the reasons for the `in` parameters - avoiding copying, but on the returning side.</span></span> <span data-ttu-id="b6e58-239">Prima di questa funzionalità, un metodo o un indicizzatore aveva due opzioni: 1) restituito per riferimento ed essere esposto a possibili mutazioni o 2) restituito per valore che comporta la copia.</span><span class="sxs-lookup"><span data-stu-id="b6e58-239">Prior to this feature, a method or an indexer had two options: 1) return by reference and be exposed to possible mutations or 2) return by value which results in copying.</span></span>

### <a name="solution-ref-readonly-returns"></a><span data-ttu-id="b6e58-240">Soluzione (`ref readonly` restituisce)</span><span class="sxs-lookup"><span data-stu-id="b6e58-240">Solution (`ref readonly` returns)</span></span>  
<span data-ttu-id="b6e58-241">La funzionalità consente a un membro di restituire le variabili in base al riferimento senza esporle alle mutazioni.</span><span class="sxs-lookup"><span data-stu-id="b6e58-241">The feature allows a member to return variables by reference without exposing them to mutations.</span></span>

### <a name="declaring-ref-readonly-returning-members"></a><span data-ttu-id="b6e58-242">Dichiarazione di `ref readonly` la restituzione di membri</span><span class="sxs-lookup"><span data-stu-id="b6e58-242">Declaring `ref readonly` returning members</span></span>

<span data-ttu-id="b6e58-243">Una combinazione di modificatori `ref readonly` sulla firma restituita viene utilizzata per indicare che il membro restituisce un riferimento di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="b6e58-243">A combination of modifiers `ref readonly` on the return signature is used to to indicate that the member returns a readonly reference.</span></span>

<span data-ttu-id="b6e58-244">Per tutti gli scopi, un membro `ref readonly` viene considerato come una variabile `readonly`, simile a `readonly` campi e parametri `in`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-244">For all purposes a `ref readonly` member is treated as a `readonly` variable - similar to `readonly` fields and `in` parameters.</span></span>

<span data-ttu-id="b6e58-245">Ad esempio, i campi di `ref readonly` membro che dispone di un tipo struct sono ricorsivi in modo ricorsivo come `readonly` variabili.</span><span class="sxs-lookup"><span data-stu-id="b6e58-245">For example fields of `ref readonly` member which has a struct type are all recursively classified as `readonly` variables.</span></span> <span data-ttu-id="b6e58-246">-È consentito passarli come argomenti `in`, ma non come argomenti `ref` o `out`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-246">- It is permitted to pass them as `in` arguments, but not as `ref` or `out` arguments.</span></span>

```csharp
ref readonly Guid Method1()
{
}

Method2(in Method1()); // valid. Can pass as `in` argument.

Method3(ref Method1()); // not valid. Cannot pass as `ref` argument
```

- <span data-ttu-id="b6e58-247">gli `ref readonly` restituiti sono consentiti nelle stesse posizioni in cui sono consentiti `ref` returni.</span><span class="sxs-lookup"><span data-stu-id="b6e58-247">`ref readonly` returns are allowed in the same places were `ref` returns are allowed.</span></span>
<span data-ttu-id="b6e58-248">Sono inclusi gli indicizzatori, i delegati, le espressioni lambda, le funzioni locali.</span><span class="sxs-lookup"><span data-stu-id="b6e58-248">This includes indexers, delegates, lambdas, local functions.</span></span>

- <span data-ttu-id="b6e58-249">Non è consentito eseguire l'overload di `ref`/`ref readonly`/differenze.</span><span class="sxs-lookup"><span data-stu-id="b6e58-249">It is not permitted to overload on `ref`/`ref readonly` /  differences.</span></span>

- <span data-ttu-id="b6e58-250">È consentita l'overload sulle differenze comuni di ByVal e `ref readonly` return.</span><span class="sxs-lookup"><span data-stu-id="b6e58-250">It is permitted to overload on ordinary byval and `ref readonly` return differences.</span></span>

- <span data-ttu-id="b6e58-251">Ai fini di OHI (overload, occultamento, implementazione) `ref readonly` è simile ma distinto da `ref`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-251">For the purpose of OHI (Overloading, Hiding, Implementing), `ref readonly` is similar but distinct from `ref`.</span></span>
<span data-ttu-id="b6e58-252">Ad esempio, un metodo che esegue l'override di `ref readonly` uno, deve essere `ref readonly` e avere un tipo convertibile di identità.</span><span class="sxs-lookup"><span data-stu-id="b6e58-252">For example the a method that overrides `ref readonly` one, must itself be `ref readonly` and have identity-convertible type.</span></span>

- <span data-ttu-id="b6e58-253">Ai fini delle conversioni di gruppi delegate/Lambda/Method, `ref readonly` è simile ma è diverso da `ref`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-253">For the purpose of delegate/lambda/method group conversions, `ref readonly` is similar but distinct from `ref`.</span></span>
<span data-ttu-id="b6e58-254">Le espressioni lambda e i candidati per la conversione di gruppi di metodi applicabili devono corrispondere `ref readonly` ritorno del delegato di destinazione con `ref readonly` la restituzione del tipo che è convertibile in identità.</span><span class="sxs-lookup"><span data-stu-id="b6e58-254">Lambdas and applicable method group conversion candidates have to match `ref readonly` return of the target delegate with `ref readonly` return of the type that is identity-convertible.</span></span>

- <span data-ttu-id="b6e58-255">Ai fini della varianza generica, `ref readonly` restituisce un risultato non variant.</span><span class="sxs-lookup"><span data-stu-id="b6e58-255">For the purpose of generic variance, `ref readonly` returns are nonvariant.</span></span>

> <span data-ttu-id="b6e58-256">Nota: non sono presenti avvisi per `ref readonly` restituiti con tipi di riferimento o primitivi.</span><span class="sxs-lookup"><span data-stu-id="b6e58-256">NOTE: There are no warnings on `ref readonly` returns that have reference or primitives types.</span></span>
<span data-ttu-id="b6e58-257">Potrebbe essere inutile in generale, ma in alcuni casi l'utente deve/vuole passare le primitive come `in`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-257">It may be pointless in general, but in some cases user must/want to pass primitives as `in`.</span></span> <span data-ttu-id="b6e58-258">Esempi: override di un metodo generico come `ref readonly T Method()` quando `T` è stato sostituito da `int`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-258">Examples - overriding a generic method like `ref readonly T Method()` when `T` was substituted to be `int`.</span></span>
>
><span data-ttu-id="b6e58-259">È preferibile disporre di un analizzatore in grado di avvisare in caso di uso inefficiente di `ref readonly` restituisce, ma le regole per tale analisi sarebbero troppo fuzzy per essere parte di una specifica del linguaggio.</span><span class="sxs-lookup"><span data-stu-id="b6e58-259">It is conceivable to have an analyzer that warns in cases of inefficient use of `ref readonly` returns, but the rules for such analysis would be too fuzzy to be a part of a language specification.</span></span>

### <a name="returning-from-ref-readonly-members"></a><span data-ttu-id="b6e58-260">Restituzione da membri `ref readonly`</span><span class="sxs-lookup"><span data-stu-id="b6e58-260">Returning from `ref readonly` members</span></span>
<span data-ttu-id="b6e58-261">All'interno del corpo del metodo la sintassi è identica a quella dei normali ritorni Ref.</span><span class="sxs-lookup"><span data-stu-id="b6e58-261">Inside the method body the syntax is the same as with regular ref returns.</span></span> <span data-ttu-id="b6e58-262">Il `readonly` verrà dedotto dal metodo contenitore.</span><span class="sxs-lookup"><span data-stu-id="b6e58-262">The `readonly` will be inferred from the containing method.</span></span>

<span data-ttu-id="b6e58-263">La motivazione è che `return ref readonly <expression>` è troppo lungo e consente solo la mancata corrispondenza della parte `readonly` che comporterebbe sempre errori.</span><span class="sxs-lookup"><span data-stu-id="b6e58-263">The motivation is that `return ref readonly <expression>` is unnecessary long and only allows for mismatches on the `readonly` part that would always result in errors.</span></span>
<span data-ttu-id="b6e58-264">Il `ref` è, tuttavia, necessario per garantire la coerenza con altri scenari in cui un elemento viene passato tramite un alias rigoroso rispetto al valore.</span><span class="sxs-lookup"><span data-stu-id="b6e58-264">The `ref` is, however, required for consistency with other scenarios where something is passed via strict aliasing vs. by value.</span></span>

> <span data-ttu-id="b6e58-265">A differenza del caso con `in` parametri, `ref readonly` restituisce mai un risultato tramite una copia locale.</span><span class="sxs-lookup"><span data-stu-id="b6e58-265">Unlike the case with `in` parameters, `ref readonly` returns never return via a local copy.</span></span> <span data-ttu-id="b6e58-266">Considerato che la copia smetterebbe di esistere immediatamente dopo la restituzione di tale procedura sarebbe inutile e pericolosa.</span><span class="sxs-lookup"><span data-stu-id="b6e58-266">Considering that the copy would cease to exist immediately upon returning such practice would be pointless and dangerous.</span></span> <span data-ttu-id="b6e58-267">Pertanto `ref readonly` restituisce sempre riferimenti diretti.</span><span class="sxs-lookup"><span data-stu-id="b6e58-267">Therefore `ref readonly` returns are always direct references.</span></span>

<span data-ttu-id="b6e58-268">Esempio:</span><span class="sxs-lookup"><span data-stu-id="b6e58-268">Example:</span></span>

```csharp
struct ImmutableArray<T>
{
    private readonly T[] array;

    public ref readonly T ItemRef(int i)
    {
        // returning a readonly reference to an array element
        return ref this.array[i];
    }
}

```

- <span data-ttu-id="b6e58-269">Un argomento di `return ref` deve essere un LValue (**regola esistente**)</span><span class="sxs-lookup"><span data-stu-id="b6e58-269">An argument of `return ref` must be an LValue (**existing rule**)</span></span>
- <span data-ttu-id="b6e58-270">Un argomento di `return ref` deve essere "safe to return" (**regola esistente**)</span><span class="sxs-lookup"><span data-stu-id="b6e58-270">An argument of `return ref` must be "safe to return" (**existing rule**)</span></span>
- <span data-ttu-id="b6e58-271">In un membro `ref readonly` _non è necessario che_ un argomento di `return ref` sia scrivibile.</span><span class="sxs-lookup"><span data-stu-id="b6e58-271">In a `ref readonly` member an argument of `return ref` is _not required to be writeable_ .</span></span>
<span data-ttu-id="b6e58-272">Ad esempio, un membro può restituire Ref, un campo ReadOnly o uno dei relativi parametri `in`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-272">For example such member can ref-return a readonly field or one of its `in` parameters.</span></span>

### <a name="safe-to-return-rules"></a><span data-ttu-id="b6e58-273">Regole sicure per la restituzione.</span><span class="sxs-lookup"><span data-stu-id="b6e58-273">Safe to Return rules.</span></span>
<span data-ttu-id="b6e58-274">Le normali regole sicure per la restituzione dei riferimenti verranno applicate anche ai riferimenti di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="b6e58-274">Normal safe to return rules for references will apply to readonly references as well.</span></span>

<span data-ttu-id="b6e58-275">Si noti che è possibile ottenere un `ref readonly` da un normale `ref` locale/parametro/restituito, ma non viceversa.</span><span class="sxs-lookup"><span data-stu-id="b6e58-275">Note that a `ref readonly` can be obtained from a regular `ref` local/parameter/return, but not the other way around.</span></span> <span data-ttu-id="b6e58-276">In caso contrario, la sicurezza del `ref readonly` restituisce viene dedotta allo stesso modo del normale `ref` restituisce.</span><span class="sxs-lookup"><span data-stu-id="b6e58-276">Otherwise the safety of `ref readonly` returns is inferred the same way as for regular `ref` returns.</span></span>

<span data-ttu-id="b6e58-277">Considerando che RValues può essere passato come parametro di `in` e restituito come `ref readonly` è necessaria una sola regola: **RValues non è sicuro da restituire per riferimento**.</span><span class="sxs-lookup"><span data-stu-id="b6e58-277">Considering that RValues can be passed as `in` parameter and returned as `ref readonly` we need one more rule - **RValues are not safe-to-return by reference**.</span></span>

> <span data-ttu-id="b6e58-278">Si consideri la situazione in cui un RValue viene passato a un parametro di `in` tramite una copia e quindi restituito sotto forma di `ref readonly`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-278">Consider the situation when an RValue is passed to an `in` parameter via a copy and then returned back in a form of a `ref readonly`.</span></span> <span data-ttu-id="b6e58-279">Nel contesto del chiamante il risultato di tale chiamata è un riferimento ai dati locali e, di conseguenza, non è sicuro restituire.</span><span class="sxs-lookup"><span data-stu-id="b6e58-279">In the context of the caller the result of such invocation is a reference to local data and as such is unsafe to return.</span></span>
> <span data-ttu-id="b6e58-280">Una volta che RValues non è sicuro, la regola esistente `#6` gestisce già questo caso.</span><span class="sxs-lookup"><span data-stu-id="b6e58-280">Once RValues are not safe to return, the existing rule `#6` already handles this case.</span></span>

<span data-ttu-id="b6e58-281">Esempio:</span><span class="sxs-lookup"><span data-stu-id="b6e58-281">Example:</span></span>
```csharp
ref readonly Vector3 Test1()
{
    // can pass an RValue as "in" (via a temp copy)
    // but the result is not safe to return
    // because the RValue argument was not safe to return by reference
    return ref Test2(default(Vector3));
}

ref readonly Vector3 Test2(in Vector3 r)
{
    // this is ok, r is returnable
    return ref r;
}
```

<span data-ttu-id="b6e58-282">Regole `safe to return` aggiornate:</span><span class="sxs-lookup"><span data-stu-id="b6e58-282">Updated `safe to return` rules:</span></span>

1.  <span data-ttu-id="b6e58-283">**i riferimenti alle variabili nell'heap possono essere restituiti in modo sicuro**</span><span class="sxs-lookup"><span data-stu-id="b6e58-283">**refs to variables on the heap are safe to return**</span></span>
2.  <span data-ttu-id="b6e58-284">**i parametri ref/in sono sicuri per restituire**
`in` i parametri possono essere restituiti solo come ReadOnly.</span><span class="sxs-lookup"><span data-stu-id="b6e58-284">**ref/in parameters are safe to return**
`in` parameters naturally can only be returned as readonly.</span></span>
3.  <span data-ttu-id="b6e58-285">i **parametri out possono essere restituiti in modo sicuro** , ma è necessario assegnarli definitivamente, come già nel caso attuale</span><span class="sxs-lookup"><span data-stu-id="b6e58-285">**out parameters are safe to return** (but must be definitely assigned, as is already the case today)</span></span>
4.  <span data-ttu-id="b6e58-286">**i campi struct dell'istanza possono essere restituiti in modo sicuro finché il ricevitore è sicuro per la restituzione**</span><span class="sxs-lookup"><span data-stu-id="b6e58-286">**instance struct fields are safe to return as long as the receiver is safe to return**</span></span>
5.  <span data-ttu-id="b6e58-287">**' This ' non è sicuro per restituire i membri struct**</span><span class="sxs-lookup"><span data-stu-id="b6e58-287">**'this' is not safe to return from struct members**</span></span>
6.  <span data-ttu-id="b6e58-288">**un riferimento, restituito da un altro metodo, può essere restituito in modo sicuro se tutti i refs/outs passati al metodo come parametri formali erano sicuri da restituire.** 
*in particolare è irrilevante se il ricevitore è sicuro da restituire, indipendentemente dal fatto che il ricevitore sia uno struct, una classe o tipizzato come parametro di tipo generico.*</span><span class="sxs-lookup"><span data-stu-id="b6e58-288">**a ref, returned from another method is safe to return if all refs/outs passed to that method as formal parameters were safe to return.**
*Specifically it is irrelevant if receiver is safe to return, regardless whether receiver is a struct, class or typed as a generic type parameter.*</span></span>
7.  <span data-ttu-id="b6e58-289">**RValues non è sicuro restituire per riferimento.** 
in *particolare RValues sono sicure da passare come parametri in.*</span><span class="sxs-lookup"><span data-stu-id="b6e58-289">**RValues are not safe to return by reference.**
*Specifically RValues are safe to pass as in parameters.*</span></span>

> <span data-ttu-id="b6e58-290">Nota: esistono regole aggiuntive relative alla sicurezza dei ritorni che entrano in gioco quando sono interessati tipi di riferimento e riassegnazioni Ref.</span><span class="sxs-lookup"><span data-stu-id="b6e58-290">NOTE: There are additional rules regarding safety of returns that come into play when ref-like types and ref-reassignments are involved.</span></span>
> <span data-ttu-id="b6e58-291">Le regole si applicano ugualmente ai membri `ref` e `ref readonly` e pertanto non sono indicate in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="b6e58-291">The rules equally apply to `ref` and `ref readonly` members and therefore are not mentioned here.</span></span>

### <a name="aliasing-behavior"></a><span data-ttu-id="b6e58-292">Comportamento degli alias.</span><span class="sxs-lookup"><span data-stu-id="b6e58-292">Aliasing behavior.</span></span>
<span data-ttu-id="b6e58-293">`ref readonly` membri forniscono lo stesso comportamento di alias dei membri `ref` ordinari (ad eccezione di ReadOnly).</span><span class="sxs-lookup"><span data-stu-id="b6e58-293">`ref readonly` members provide the same aliasing behavior as ordinary `ref` members (except for being readonly).</span></span>
<span data-ttu-id="b6e58-294">Per questo motivo, per l'acquisizione di espressioni lambda, async, iteratori, distribuzione dello stack e così via... si applicano le stesse restrizioni.</span><span class="sxs-lookup"><span data-stu-id="b6e58-294">Therefore for the purpose of capturing in lambdas, async, iterators, stack spilling etc... the same restrictions apply.</span></span> <span data-ttu-id="b6e58-295">cioè.</span><span class="sxs-lookup"><span data-stu-id="b6e58-295">- I.E.</span></span> <span data-ttu-id="b6e58-296">a causa dell'impossibilità di acquisire i riferimenti effettivi e la natura degli effetti collaterali della valutazione dei membri, tali scenari non sono consentiti.</span><span class="sxs-lookup"><span data-stu-id="b6e58-296">due to inability to capture the actual references and due to side-effecting nature of member evaluation such scenarios are disallowed.</span></span>

> <span data-ttu-id="b6e58-297">È consentita e necessaria per eseguire una copia quando `ref readonly` return è un destinatario di metodi struct normali che accettano `this` come riferimento scrivibile normale.</span><span class="sxs-lookup"><span data-stu-id="b6e58-297">It is permitted and required to make a copy when `ref readonly` return is a receiver of regular struct methods, which take `this` as an ordinary writeable reference.</span></span> <span data-ttu-id="b6e58-298">Storicamente, in tutti i casi in cui tali chiamate vengono applicate alla variabile ReadOnly, viene eseguita una copia locale.</span><span class="sxs-lookup"><span data-stu-id="b6e58-298">Historically in all cases where such invocations are applied to readonly variable a local copy is made.</span></span>

### <a name="metadata-representation"></a><span data-ttu-id="b6e58-299">Rappresentazione dei metadati.</span><span class="sxs-lookup"><span data-stu-id="b6e58-299">Metadata representation.</span></span>
<span data-ttu-id="b6e58-300">Quando `System.Runtime.CompilerServices.IsReadOnlyAttribute` viene applicato alla restituzione di un metodo ByRef returning, significa che il metodo restituisce un riferimento di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="b6e58-300">When `System.Runtime.CompilerServices.IsReadOnlyAttribute` is applied to the return of a byref returning method, it means that the method returns a readonly reference.</span></span>

<span data-ttu-id="b6e58-301">Inoltre, la firma del risultato di tali metodi (e solo quei metodi) deve avere `modreq[System.Runtime.CompilerServices.IsReadOnlyAttribute]`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-301">In addition, the result signature of such methods (and only those methods) must have `modreq[System.Runtime.CompilerServices.IsReadOnlyAttribute]`.</span></span>

<span data-ttu-id="b6e58-302">**Motivazione**: per garantire che i compilatori esistenti non possano semplicemente ignorare `readonly` durante la chiamata di metodi con `ref readonly` restituisce</span><span class="sxs-lookup"><span data-stu-id="b6e58-302">**Motivation**: this is to ensure that existing compilers cannot simply ignore `readonly` when invoking methods with `ref readonly` returns</span></span>

## <a name="readonly-structs"></a><span data-ttu-id="b6e58-303">Struct di sola lettura</span><span class="sxs-lookup"><span data-stu-id="b6e58-303">Readonly structs</span></span>
<span data-ttu-id="b6e58-304">In breve, funzionalità che rende `this` parametro di tutti i membri di istanza di uno struct, ad eccezione dei costruttori, di un parametro di `in`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-304">In short - a feature that makes `this` parameter of all instance members of a struct, except for constructors, an `in` parameter.</span></span>

### <a name="motivation"></a><span data-ttu-id="b6e58-305">Motivazione</span><span class="sxs-lookup"><span data-stu-id="b6e58-305">Motivation</span></span>
<span data-ttu-id="b6e58-306">Il compilatore deve presupporre che qualsiasi chiamata al metodo su un'istanza struct possa modificare l'istanza.</span><span class="sxs-lookup"><span data-stu-id="b6e58-306">Compiler must assume that any method call on a struct instance may modify the instance.</span></span> <span data-ttu-id="b6e58-307">In realtà, un riferimento scrivibile viene passato al metodo come `this` parametro e Abilita completamente questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="b6e58-307">Indeed a writeable reference is passed to the method as `this` parameter and fully enables this behavior.</span></span> <span data-ttu-id="b6e58-308">Per consentire le chiamate in `readonly` variabili, le chiamate vengono applicate alle copie temporanee.</span><span class="sxs-lookup"><span data-stu-id="b6e58-308">To allow such invocations on `readonly` variables, the invocations are applied to temp copies.</span></span> <span data-ttu-id="b6e58-309">Questo potrebbe essere poco intuitivo e talvolta impone agli utenti di abbandonare `readonly` per motivi di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="b6e58-309">That could be unintuitive and sometimes forces people to abandon `readonly` for performance reasons.</span></span>  
<span data-ttu-id="b6e58-310">Esempio: https://codeblog.jonskeet.uk/2014/07/16/micro-optimization-the-surprising-inefficiency-of-readonly-fields/</span><span class="sxs-lookup"><span data-stu-id="b6e58-310">Example: https://codeblog.jonskeet.uk/2014/07/16/micro-optimization-the-surprising-inefficiency-of-readonly-fields/</span></span>

<span data-ttu-id="b6e58-311">Dopo l'aggiunta del supporto per `in` parametri e `ref readonly` restituisce il problema della copia difensiva peggiorerà perché le variabili di sola lettura diventeranno più comuni.</span><span class="sxs-lookup"><span data-stu-id="b6e58-311">After adding support for `in` parameters and `ref readonly` returns the problem of defensive copying will get worse since readonly variables will become more common.</span></span>

### <a name="solution"></a><span data-ttu-id="b6e58-312">Soluzione</span><span class="sxs-lookup"><span data-stu-id="b6e58-312">Solution</span></span>
<span data-ttu-id="b6e58-313">Consenti `readonly` modificatore per le dichiarazioni di struct che comporterebbe il `this` trattato come `in` parametro in tutti i metodi di istanza struct ad eccezione dei costruttori.</span><span class="sxs-lookup"><span data-stu-id="b6e58-313">Allow `readonly` modifier on struct declarations which would result in `this` being treated as `in` parameter on all struct instance methods except for constructors.</span></span>

```csharp
static void Test(in Vector3 v1)
{
    // no need to make a copy of v1 since Vector3 is a readonly struct
    System.Console.WriteLine(v1.ToString());
}

readonly struct Vector3
{
    . . .

    public override string ToString()
    {
        // not OK!!  `this` is an `in` parameter
        foo(ref this.X);

        // OK
        return $"X: {X}, Y: {Y}, Z: {Z}";
    }
}
```

### <a name="restrictions-on-members-of-readonly-struct"></a><span data-ttu-id="b6e58-314">Restrizioni sui membri dello struct di sola lettura</span><span class="sxs-lookup"><span data-stu-id="b6e58-314">Restrictions on members of readonly struct</span></span>
- <span data-ttu-id="b6e58-315">I campi di istanza di uno struct di sola lettura devono essere di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="b6e58-315">Instance fields of a readonly struct must be readonly.</span></span>  
<span data-ttu-id="b6e58-316">**Motivazione:** è possibile scrivere solo all'esterno, ma non tramite i membri.</span><span class="sxs-lookup"><span data-stu-id="b6e58-316">**Motivation:** can only be written to externally, but not through members.</span></span>
- <span data-ttu-id="b6e58-317">Le proprietà autoproprietà dell'istanza di uno struct di sola lettura devono essere solo Get.</span><span class="sxs-lookup"><span data-stu-id="b6e58-317">Instance autoproperties of a readonly struct must be get-only.</span></span>  
<span data-ttu-id="b6e58-318">**Motivazione:** conseguenza della restrizione sui campi di istanza.</span><span class="sxs-lookup"><span data-stu-id="b6e58-318">**Motivation:** consequence of restriction on instance fields.</span></span>
- <span data-ttu-id="b6e58-319">Lo struct di sola lettura non può dichiarare eventi simili a campi.</span><span class="sxs-lookup"><span data-stu-id="b6e58-319">Readonly struct may not declare field-like events.</span></span>  
<span data-ttu-id="b6e58-320">**Motivazione:** conseguenza della restrizione sui campi di istanza.</span><span class="sxs-lookup"><span data-stu-id="b6e58-320">**Motivation:** consequence of restriction on instance fields.</span></span>

### <a name="metadata-representation"></a><span data-ttu-id="b6e58-321">Rappresentazione dei metadati.</span><span class="sxs-lookup"><span data-stu-id="b6e58-321">Metadata representation.</span></span>
<span data-ttu-id="b6e58-322">Quando `System.Runtime.CompilerServices.IsReadOnlyAttribute` viene applicato a un tipo di valore, significa che il tipo è un `readonly struct`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-322">When `System.Runtime.CompilerServices.IsReadOnlyAttribute` is applied to a value type, it means that the type is a `readonly struct`.</span></span>

<span data-ttu-id="b6e58-323">In particolare:</span><span class="sxs-lookup"><span data-stu-id="b6e58-323">In particular:</span></span>
-  <span data-ttu-id="b6e58-324">L'identità del tipo di `IsReadOnlyAttribute` non è importante.</span><span class="sxs-lookup"><span data-stu-id="b6e58-324">The identity of the `IsReadOnlyAttribute` type is unimportant.</span></span> <span data-ttu-id="b6e58-325">Infatti, se necessario, può essere incorporato dal compilatore nell'assembly contenitore.</span><span class="sxs-lookup"><span data-stu-id="b6e58-325">In fact it can be embedded by the compiler in the containing assembly if needed.</span></span>

## <a name="refin-extension-methods"></a><span data-ttu-id="b6e58-326">`ref`/metodi di estensione `in`</span><span class="sxs-lookup"><span data-stu-id="b6e58-326">`ref`/`in` extension methods</span></span>
<span data-ttu-id="b6e58-327">Esiste effettivamente una proposta esistente (https://github.com/dotnet/roslyn/issues/165) e la richiesta pull del prototipo corrispondente (https://github.com/dotnet/roslyn/pull/15650).</span><span class="sxs-lookup"><span data-stu-id="b6e58-327">There is actually an existing proposal (https://github.com/dotnet/roslyn/issues/165) and corresponding prototype PR (https://github.com/dotnet/roslyn/pull/15650).</span></span>
<span data-ttu-id="b6e58-328">Voglio solo confermare che questa idea non è completamente nuova.</span><span class="sxs-lookup"><span data-stu-id="b6e58-328">I just want to acknowledge that this idea is not entirely new.</span></span> <span data-ttu-id="b6e58-329">Tuttavia, è pertinente in questo caso poiché `ref readonly` rimuovono in modo elegante il problema più contenzioso su tali metodi, cosa fare con i ricevitori RValue.</span><span class="sxs-lookup"><span data-stu-id="b6e58-329">It is, however, relevant here since `ref readonly` elegantly removes the most contentious issue about such methods - what to do with RValue receivers.</span></span>

<span data-ttu-id="b6e58-330">L'idea generale è consentire ai metodi di estensione di prendere il `this` parametro per riferimento, purché il tipo sia noto come tipo di struct.</span><span class="sxs-lookup"><span data-stu-id="b6e58-330">The general idea is allowing extension methods to take the `this` parameter by reference, as long as the type is known to be a struct type.</span></span>

```csharp
public static void Extension(ref this Guid self)
{
    // do something
}
```

<span data-ttu-id="b6e58-331">I motivi per la scrittura di tali metodi di estensione sono principalmente:</span><span class="sxs-lookup"><span data-stu-id="b6e58-331">The reasons for writing such extension methods are primarily:</span></span>  
1.  <span data-ttu-id="b6e58-332">Evitare la copia quando Receiver è uno struct di grandi dimensioni</span><span class="sxs-lookup"><span data-stu-id="b6e58-332">Avoid copying when receiver is a large struct</span></span>
2.  <span data-ttu-id="b6e58-333">Consenti la mutazione di metodi di estensione su struct</span><span class="sxs-lookup"><span data-stu-id="b6e58-333">Allow mutating extension methods on structs</span></span>

<span data-ttu-id="b6e58-334">I motivi per cui non si vuole consentire questa impostazione sulle classi</span><span class="sxs-lookup"><span data-stu-id="b6e58-334">The reasons why we do not want to allow this on classes</span></span>  
1.  <span data-ttu-id="b6e58-335">Lo scopo è molto limitato.</span><span class="sxs-lookup"><span data-stu-id="b6e58-335">It would be of very limited purpose.</span></span>
2.  <span data-ttu-id="b6e58-336">In questo modo si interrompe un'invarianza a lungo termine che una chiamata al metodo non può trasformare il ricevitore non`null` per diventare `null` dopo la chiamata.</span><span class="sxs-lookup"><span data-stu-id="b6e58-336">It would break long standing invariant that a method call cannot turn non-`null` receiver to become `null` after invocation.</span></span>
> <span data-ttu-id="b6e58-337">Attualmente, una variabile non`null` non può diventare `null` a meno che non venga assegnata o passata in _modo esplicito_ da `ref` o `out`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-337">In fact, currently a non-`null` variable cannot become `null` unless _explicitly_ assigned or passed by `ref` or `out`.</span></span>
> <span data-ttu-id="b6e58-338">Che facilita significativamente la leggibilità o altre forme di analisi "può essere un valore null".</span><span class="sxs-lookup"><span data-stu-id="b6e58-338">That greatly aids readability or other forms of "can this be a null here" analysis.</span></span>
3.  <span data-ttu-id="b6e58-339">Sarebbe difficile riconciliare con la semantica "Evaluate once" degli accessi condizionali null.</span><span class="sxs-lookup"><span data-stu-id="b6e58-339">It would be hard to reconcile with "evaluate once" semantics of null-conditional accesses.</span></span>
<span data-ttu-id="b6e58-340">Esempio: `obj.stringField?.RefExtension(...)`-è necessario acquisire una copia di `stringField` per rendere il controllo null significativo, ma le assegnazioni a `this` all'interno di RefExtension non verranno riflesse nel campo.</span><span class="sxs-lookup"><span data-stu-id="b6e58-340">Example: `obj.stringField?.RefExtension(...)` - need to capture a copy of `stringField` to make the null check meaningful, but then assignments to `this` inside RefExtension would not be reflected back to the field.</span></span>

<span data-ttu-id="b6e58-341">La possibilità di dichiarare metodi di estensione su **struct** che accettano il primo argomento per riferimento è una richiesta di lunga durata.</span><span class="sxs-lookup"><span data-stu-id="b6e58-341">An ability to declare extension methods on **structs** that take the first argument by reference was a long-standing request.</span></span> <span data-ttu-id="b6e58-342">Una delle considerazioni di blocco è stata "cosa accade se il ricevitore non è un LValue?".</span><span class="sxs-lookup"><span data-stu-id="b6e58-342">One of the blocking consideration was "what happens if receiver is not an LValue?".</span></span>

- <span data-ttu-id="b6e58-343">C'è un precedente che qualsiasi metodo di estensione può essere chiamato anche come metodo statico (a volte è l'unico modo per risolvere l'ambiguità).</span><span class="sxs-lookup"><span data-stu-id="b6e58-343">There is a precedent that any extension method could also be called as a static method (sometimes it is the only way to resolve ambiguity).</span></span> <span data-ttu-id="b6e58-344">Si impone che i ricevitori RValue non siano consentiti.</span><span class="sxs-lookup"><span data-stu-id="b6e58-344">It would dictate that RValue receivers should be disallowed.</span></span>
- <span data-ttu-id="b6e58-345">D'altra parte, è necessario eseguire una chiamata in una copia in situazioni simili quando sono interessati i metodi di istanza struct.</span><span class="sxs-lookup"><span data-stu-id="b6e58-345">On the other hand there is a practice of making invocation on a copy in similar situations when struct instance methods are involved.</span></span>

<span data-ttu-id="b6e58-346">Il motivo per cui esiste la "copia implicita" è dato dal fatto che la maggior parte dei metodi struct non modifica effettivamente lo struct e non è in grado di indicare.</span><span class="sxs-lookup"><span data-stu-id="b6e58-346">The reason why the "implicit copying" exists is because the majority of struct methods do not actually modify the struct while not being able to indicate that.</span></span> <span data-ttu-id="b6e58-347">Quindi, la soluzione più pratica consisteva nel creare solo la chiamata a una copia, ma questa procedura è nota per danneggiare le prestazioni e causare bug.</span><span class="sxs-lookup"><span data-stu-id="b6e58-347">Therefore the most practical solution was to just make the invocation on a copy, but this practice is known for harming performance and causing bugs.</span></span>

<span data-ttu-id="b6e58-348">A questo punto, con la disponibilità dei parametri di `in`, è possibile che un'estensione segnali lo scopo.</span><span class="sxs-lookup"><span data-stu-id="b6e58-348">Now, with availability of `in` parameters, it is possible for an extension to signal the intent.</span></span> <span data-ttu-id="b6e58-349">Di conseguenza, l'enigma può essere risolto richiedendo la chiamata delle estensioni `ref` con ricevitori scrivibile mentre le estensioni `in` consentono la copia implicita, se necessario.</span><span class="sxs-lookup"><span data-stu-id="b6e58-349">Therefore the conundrum can be resolved by requiring `ref` extensions to be called with writeable receivers while `in` extensions permit implicit copying if necessary.</span></span>

```csharp
// this can be called on either RValue or an LValue
public static void Reader(in this Guid self)
{
    // do something nonmutating.
    WriteLine(self == default(Guid));
}

// this can be called only on an LValue
public static void Mutator(ref this Guid self)
{
    // can mutate self
    self = new Guid();
}
```

### <a name="in-extensions-and-generics"></a><span data-ttu-id="b6e58-350">`in` estensioni e generics.</span><span class="sxs-lookup"><span data-stu-id="b6e58-350">`in` extensions and generics.</span></span>
<span data-ttu-id="b6e58-351">Lo scopo di `ref` metodi di estensione consiste nel modificare direttamente il ricevitore o richiamando membri mutanti.</span><span class="sxs-lookup"><span data-stu-id="b6e58-351">The purpose of `ref` extension methods is to mutate the receiver directly or by invoking mutating members.</span></span> <span data-ttu-id="b6e58-352">Sono pertanto consentite `ref this T` estensioni purché `T` sia vincolato a essere uno struct.</span><span class="sxs-lookup"><span data-stu-id="b6e58-352">Therefore `ref this T` extensions are allowed as long as `T` is constrained to be a struct.</span></span>

<span data-ttu-id="b6e58-353">D'altra parte `in` metodi di estensione esistono in modo specifico per ridurre la copia implicita.</span><span class="sxs-lookup"><span data-stu-id="b6e58-353">On the other hand `in` extension methods exist specifically to reduce implicit copying.</span></span> <span data-ttu-id="b6e58-354">Tuttavia, qualsiasi uso di un parametro di `in T` deve essere eseguito tramite un membro di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="b6e58-354">However any use of an `in T` parameter will have to be done through an interface member.</span></span> <span data-ttu-id="b6e58-355">Poiché tutti i membri di interfaccia sono considerati mutanti, qualsiasi utilizzo di questo tipo richiederebbe una copia.</span><span class="sxs-lookup"><span data-stu-id="b6e58-355">Since all interface members are considered mutating, any such use would require a copy.</span></span> <span data-ttu-id="b6e58-356">-Anziché ridurre la copia, l'effetto sarebbe l'opposto.</span><span class="sxs-lookup"><span data-stu-id="b6e58-356">- Instead of reducing copying, the effect would be the opposite.</span></span> <span data-ttu-id="b6e58-357">Pertanto `in this T` non è consentito quando `T` è un parametro di tipo generico indipendentemente dai vincoli.</span><span class="sxs-lookup"><span data-stu-id="b6e58-357">Therefore `in this T` is not allowed when `T` is a generic type parameter regardless of constraints.</span></span>

### <a name="valid-kinds-of-extension-methods-recap"></a><span data-ttu-id="b6e58-358">Tipi validi di metodi di estensione (riepilogo):</span><span class="sxs-lookup"><span data-stu-id="b6e58-358">Valid kinds of extension methods (recap):</span></span>
<span data-ttu-id="b6e58-359">Sono ora consentite le seguenti forme di `this` dichiarazione in un metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="b6e58-359">The following forms of `this` declaration in an extension method are now allowed:</span></span>
1) <span data-ttu-id="b6e58-360">estensione ByVal `this T arg`-Regular.</span><span class="sxs-lookup"><span data-stu-id="b6e58-360">`this T arg` - regular byval extension.</span></span> <span data-ttu-id="b6e58-361">(**caso esistente**)</span><span class="sxs-lookup"><span data-stu-id="b6e58-361">(**existing case**)</span></span>
- <span data-ttu-id="b6e58-362">T può essere qualsiasi tipo, inclusi i tipi di riferimento o parametri di tipo.</span><span class="sxs-lookup"><span data-stu-id="b6e58-362">T can be any type, including reference types or type parameters.</span></span>
<span data-ttu-id="b6e58-363">L'istanza sarà la stessa variabile dopo la chiamata.</span><span class="sxs-lookup"><span data-stu-id="b6e58-363">Instance will be the same variable after the call.</span></span>
<span data-ttu-id="b6e58-364">Consente le conversioni implicite di questo tipo di _conversione di argomenti_ .</span><span class="sxs-lookup"><span data-stu-id="b6e58-364">Allows implicit conversions of _this-argument-conversion_ kind.</span></span>
<span data-ttu-id="b6e58-365">Può essere chiamato su RValues.</span><span class="sxs-lookup"><span data-stu-id="b6e58-365">Can be called on RValues.</span></span>

- <span data-ttu-id="b6e58-366">`in this T self` - estensione di `in`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-366">`in this T self` - `in` extension.</span></span>
<span data-ttu-id="b6e58-367">T deve essere un tipo struct effettivo.</span><span class="sxs-lookup"><span data-stu-id="b6e58-367">T must be an actual struct type.</span></span>
<span data-ttu-id="b6e58-368">L'istanza sarà la stessa variabile dopo la chiamata.</span><span class="sxs-lookup"><span data-stu-id="b6e58-368">Instance will be the same variable after the call.</span></span>
<span data-ttu-id="b6e58-369">Consente le conversioni implicite di questo tipo di _conversione di argomenti_ .</span><span class="sxs-lookup"><span data-stu-id="b6e58-369">Allows implicit conversions of _this-argument-conversion_ kind.</span></span>
<span data-ttu-id="b6e58-370">Può essere chiamato su RValues (può essere richiamato su Temp se necessario).</span><span class="sxs-lookup"><span data-stu-id="b6e58-370">Can be called on RValues (may be invoked on a temp if needed).</span></span>

- <span data-ttu-id="b6e58-371">`ref this T self` - estensione di `ref`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-371">`ref this T self` - `ref` extension.</span></span>
<span data-ttu-id="b6e58-372">T deve essere un tipo struct o un parametro di tipo generico vincolato per essere uno struct.</span><span class="sxs-lookup"><span data-stu-id="b6e58-372">T must be a struct type or a generic type parameter constrained to be a struct.</span></span>
<span data-ttu-id="b6e58-373">L'istanza può essere scritta dalla chiamata.</span><span class="sxs-lookup"><span data-stu-id="b6e58-373">Instance may be written to by the invocation.</span></span>
<span data-ttu-id="b6e58-374">Consente solo le conversioni di identità.</span><span class="sxs-lookup"><span data-stu-id="b6e58-374">Allows only identity conversions.</span></span>
<span data-ttu-id="b6e58-375">Deve essere chiamato su LValue scrivibile.</span><span class="sxs-lookup"><span data-stu-id="b6e58-375">Must be called on writeable LValue.</span></span> <span data-ttu-id="b6e58-376">(mai richiamato tramite una temperatura temporanea).</span><span class="sxs-lookup"><span data-stu-id="b6e58-376">(never invoked via a temp).</span></span>

## <a name="readonly-ref-locals"></a><span data-ttu-id="b6e58-377">Variabili locali Ref di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="b6e58-377">Readonly ref locals.</span></span>

### <a name="motivation"></a><span data-ttu-id="b6e58-378">Motivazione.</span><span class="sxs-lookup"><span data-stu-id="b6e58-378">Motivation.</span></span>
<span data-ttu-id="b6e58-379">Una volta introdotti `ref readonly` membri, è stato chiaro che è necessario associarli al tipo di locale appropriato.</span><span class="sxs-lookup"><span data-stu-id="b6e58-379">Once `ref readonly` members were introduced, it was clear from the use that they need to be paired with appropriate kind of local.</span></span> <span data-ttu-id="b6e58-380">La valutazione di un membro può produrre o osservare gli effetti collaterali, pertanto se il risultato deve essere usato più di una volta, deve essere archiviato.</span><span class="sxs-lookup"><span data-stu-id="b6e58-380">Evaluation of a member may produce or observe side effects, therefore if the result must be used more than once, it needs to be stored.</span></span> <span data-ttu-id="b6e58-381">Le impostazioni locali di `ref` comuni non sono utili perché non è possibile assegnarle un riferimento `readonly`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-381">Ordinary `ref` locals do not help here since they cannot be assigned a `readonly` reference.</span></span>   

### <a name="solution"></a><span data-ttu-id="b6e58-382">Soluzione.</span><span class="sxs-lookup"><span data-stu-id="b6e58-382">Solution.</span></span>
<span data-ttu-id="b6e58-383">Consente la dichiarazione di `ref readonly` variabili locali.</span><span class="sxs-lookup"><span data-stu-id="b6e58-383">Allow declaring `ref readonly` locals.</span></span> <span data-ttu-id="b6e58-384">Si tratta di un nuovo tipo di variabili locali `ref` non scrivibile.</span><span class="sxs-lookup"><span data-stu-id="b6e58-384">This is a new kind of `ref` locals that is not writeable.</span></span> <span data-ttu-id="b6e58-385">Di conseguenza `ref readonly` variabili locali possono accettare riferimenti a variabili di sola lettura senza esporre tali variabili alle Scritture.</span><span class="sxs-lookup"><span data-stu-id="b6e58-385">As a result `ref readonly` locals can accept references to readonly variables without exposing these variables to writes.</span></span>

### <a name="declaring-and-using-ref-readonly-locals"></a><span data-ttu-id="b6e58-386">Dichiarazione e utilizzo di `ref readonly` variabili locali.</span><span class="sxs-lookup"><span data-stu-id="b6e58-386">Declaring and using `ref readonly` locals.</span></span>

<span data-ttu-id="b6e58-387">La sintassi di tali variabili locali utilizza i modificatori `ref readonly` nel sito di dichiarazione (nell'ordine specifico).</span><span class="sxs-lookup"><span data-stu-id="b6e58-387">The syntax of such locals uses `ref readonly` modifiers at declaration site (in that specific order).</span></span> <span data-ttu-id="b6e58-388">Analogamente alle variabili locali di `ref`, `ref readonly` variabili locali devono essere inizializzate come ref alla dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="b6e58-388">Similarly to ordinary `ref` locals, `ref readonly` locals must be ref-initialized at declaration.</span></span> <span data-ttu-id="b6e58-389">Diversamente dalle normali variabili locali `ref`, `ref readonly` variabili locali possono fare riferimento a `readonly` lvalue, ad esempio `in` parametri, `readonly` campi, `ref readonly` metodi.</span><span class="sxs-lookup"><span data-stu-id="b6e58-389">Unlike regular `ref` locals, `ref readonly` locals can refer to `readonly` LValues like `in` parameters, `readonly` fields, `ref readonly` methods.</span></span>

<span data-ttu-id="b6e58-390">Per tutti gli scopi, un `ref readonly` local viene considerato come una variabile di `readonly`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-390">For all purposes a `ref readonly` local is treated as a `readonly` variable.</span></span> <span data-ttu-id="b6e58-391">La maggior parte delle restrizioni relative all'utilizzo è identica a quella di `readonly` campi o parametri di `in`.</span><span class="sxs-lookup"><span data-stu-id="b6e58-391">Most of the restrictions on the use are the same as with `readonly` fields or `in` parameters.</span></span>

<span data-ttu-id="b6e58-392">Per esempio, i campi di un parametro di `in` con un tipo struct sono ricorsivi in modo ricorsivo come `readonly` variabili.</span><span class="sxs-lookup"><span data-stu-id="b6e58-392">For example fields of an `in` parameter which has a struct type are all recursively classified as `readonly` variables .</span></span>   

```csharp
static readonly ref Vector3 M1() => . . .

static readonly ref Vector3 M1_Trace()
{
    // OK
    ref readonly var r1 = ref M1();

    // Not valid. Need an LValue
    ref readonly Vector3 r2 = ref default(Vector3);

    // Not valid. r1 is readonly.
    Mutate(ref r1);

    // OK.
    Print(in r1);

    // OK.
    return ref r1;
}
```

### <a name="restrictions-on-use-of-ref-readonly-locals"></a><span data-ttu-id="b6e58-393">Restrizioni sull'uso di variabili locali `ref readonly`</span><span class="sxs-lookup"><span data-stu-id="b6e58-393">Restrictions on use of `ref readonly` locals</span></span>
<span data-ttu-id="b6e58-394">Ad eccezione della natura `readonly`, `ref readonly` le variabili locali comportano come normali `ref` variabili locali e sono soggette esattamente alle stesse restrizioni.</span><span class="sxs-lookup"><span data-stu-id="b6e58-394">Except for their `readonly` nature, `ref readonly` locals behave like ordinary `ref` locals and are subject to exactly same restrictions.</span></span>  
<span data-ttu-id="b6e58-395">Ad esempio, le restrizioni relative all'acquisizione nelle chiusure, dichiarando in `async` metodi o l'analisi `safe-to-return` si applicano ugualmente alle `ref readonly` variabili locali.</span><span class="sxs-lookup"><span data-stu-id="b6e58-395">For example restrictions related to capturing in closures, declaring in `async` methods or the `safe-to-return` analysis equally applies to `ref readonly` locals.</span></span>

## <a name="ternary-ref-expressions-aka-conditional-lvalues"></a><span data-ttu-id="b6e58-396">Espressioni `ref` ternarie.</span><span class="sxs-lookup"><span data-stu-id="b6e58-396">Ternary `ref` expressions.</span></span> <span data-ttu-id="b6e58-397">(noto anche come "lvalue condizionale")</span><span class="sxs-lookup"><span data-stu-id="b6e58-397">(aka "Conditional LValues")</span></span>

### <a name="motivation"></a><span data-ttu-id="b6e58-398">Motivazione</span><span class="sxs-lookup"><span data-stu-id="b6e58-398">Motivation</span></span>
<span data-ttu-id="b6e58-399">L'uso di `ref` e `ref readonly` variabili locali hanno esposto la necessità di inizializzare tali variabili locali con una o un'altra variabile di destinazione in base a una condizione.</span><span class="sxs-lookup"><span data-stu-id="b6e58-399">Use of `ref` and `ref readonly` locals exposed a need to ref-initialize such locals with one or another target variable based on a condition.</span></span>

<span data-ttu-id="b6e58-400">Una soluzione alternativa tipica consiste nell'introdurre un metodo come:</span><span class="sxs-lookup"><span data-stu-id="b6e58-400">A typical workaround is to introduce a method like:</span></span>

```csharp
ref T Choice(bool condition, ref T consequence, ref T alternative)
{
    if (condition)
    {
         return ref consequence;
    }
    else
    {
         return ref alternative;
    }
}
```

<span data-ttu-id="b6e58-401">Si noti che `Choice` non è una sostituzione esatta di una terna poiché _tutti_ gli argomenti devono essere valutati nel sito di chiamata, il che ha provocato un comportamento non intuitivo e bug.</span><span class="sxs-lookup"><span data-stu-id="b6e58-401">Note that `Choice` is not an exact replacement of a ternary since _all_ arguments must be evaluated at the call site, which was leading to unintuitive behavior and bugs.</span></span>

<span data-ttu-id="b6e58-402">Il codice seguente non funzionerà come previsto:</span><span class="sxs-lookup"><span data-stu-id="b6e58-402">The following will not work as expected:</span></span>

```csharp
    // will crash with NRE because 'arr[0]' will be executed unconditionally
    ref var r = ref Choice(arr != null, ref arr[0], ref otherArr[0]);
```

### <a name="solution"></a><span data-ttu-id="b6e58-403">Soluzione</span><span class="sxs-lookup"><span data-stu-id="b6e58-403">Solution</span></span>
<span data-ttu-id="b6e58-404">Consente un tipo speciale di espressione condizionale che restituisce un riferimento a uno degli argomenti LValue in base a una condizione.</span><span class="sxs-lookup"><span data-stu-id="b6e58-404">Allow special kind of conditional expression that evaluates to a reference to one of LValue argument based on a condition.</span></span>

### <a name="using-ref-ternary-expression"></a><span data-ttu-id="b6e58-405">Utilizzando `ref` espressione ternaria.</span><span class="sxs-lookup"><span data-stu-id="b6e58-405">Using `ref` ternary expression.</span></span>

<span data-ttu-id="b6e58-406">La sintassi per la versione `ref` di un'espressione condizionale è `<condition> ? ref <consequence> : ref <alternative>;`</span><span class="sxs-lookup"><span data-stu-id="b6e58-406">The syntax for the `ref` flavor of a conditional expression is `<condition> ? ref <consequence> : ref <alternative>;`</span></span>

<span data-ttu-id="b6e58-407">Analogamente all'espressione condizionale ordinaria, solo `<consequence>` o `<alternative>` viene valutato a seconda del risultato dell'espressione della condizione booleana.</span><span class="sxs-lookup"><span data-stu-id="b6e58-407">Just like with the ordinary conditional expression only `<consequence>` or `<alternative>` is evaluated depending on result of the boolean condition expression.</span></span>

<span data-ttu-id="b6e58-408">A differenza della normale espressione condizionale, `ref` espressione condizionale:</span><span class="sxs-lookup"><span data-stu-id="b6e58-408">Unlike ordinary conditional expression, `ref` conditional expression:</span></span>
- <span data-ttu-id="b6e58-409">richiede che `<consequence>` e `<alternative>` siano lvalue.</span><span class="sxs-lookup"><span data-stu-id="b6e58-409">requires that `<consequence>` and `<alternative>` are LValues.</span></span>
- <span data-ttu-id="b6e58-410">`ref` espressione condizionale è LValue e</span><span class="sxs-lookup"><span data-stu-id="b6e58-410">`ref` conditional expression itself is an LValue and</span></span>
- <span data-ttu-id="b6e58-411">`ref` espressione condizionale è scrivibile se sia `<consequence>` che `<alternative>` sono scrivibile lvalue</span><span class="sxs-lookup"><span data-stu-id="b6e58-411">`ref` conditional expression is writeable if both `<consequence>` and `<alternative>` are writeable LValues</span></span>

<span data-ttu-id="b6e58-412">Esempi:</span><span class="sxs-lookup"><span data-stu-id="b6e58-412">Examples:</span></span>  
<span data-ttu-id="b6e58-413">`ref` ternaria è un LValue e, di conseguenza, può essere passato/assegnato/restituito per riferimento;</span><span class="sxs-lookup"><span data-stu-id="b6e58-413">`ref` ternary is an LValue and as such it can be passed/assigned/returned by reference;</span></span>
```csharp
     // pass by reference
     foo(ref (arr != null ? ref arr[0]: ref otherArr[0]));

     // return by reference
     return ref (arr != null ? ref arr[0]: ref otherArr[0]);
```

<span data-ttu-id="b6e58-414">Essendo un LValue, può anche essere assegnato a.</span><span class="sxs-lookup"><span data-stu-id="b6e58-414">Being an LValue, it can also be assigned to.</span></span>
```csharp
     // assign to
     (arr != null ? ref arr[0]: ref otherArr[0]) = 1;

     // error. readOnlyField is readonly and thus conditional expression is readonly
     (arr != null ? ref arr[0]: ref obj.readOnlyField) = 1;
```

<span data-ttu-id="b6e58-415">Può essere usato come ricevitore di una chiamata al metodo e ignorare la copia, se necessario.</span><span class="sxs-lookup"><span data-stu-id="b6e58-415">Can be used as a receiver of a method call and skip copying if necessary.</span></span>
```csharp
     // no copies
     (arr != null ? ref arr[0]: ref otherArr[0]).StructMethod();

     // invoked on a copy.
     // The receiver is `readonly` because readOnlyField is readonly.
     (arr != null ? ref arr[0]: ref obj.readOnlyField).StructMethod();

     // no copies. `ReadonlyStructMethod` is a method on a `readonly` struct
     // and can be invoked directly on a readonly receiver
     (arr != null ? ref arr[0]: ref obj.readOnlyField).ReadonlyStructMethod();
```

<span data-ttu-id="b6e58-416">`ref` ternaria può essere usato anche in un contesto normale (non Ref).</span><span class="sxs-lookup"><span data-stu-id="b6e58-416">`ref` ternary can be used in a regular (not ref) context as well.</span></span>
```csharp
     // only an example
     // a regular ternary could work here just the same
     int x = (arr != null ? ref arr[0]: ref otherArr[0]);
```

### <a name="drawbacks"></a><span data-ttu-id="b6e58-417">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="b6e58-417">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="b6e58-418">È possibile visualizzare due argomenti principali rispetto al supporto migliorato per i riferimenti e i riferimenti di sola lettura:</span><span class="sxs-lookup"><span data-stu-id="b6e58-418">I can see two major arguments against enhanced support for references and readonly references:</span></span>

1) <span data-ttu-id="b6e58-419">I problemi risolti qui sono molto vecchi.</span><span class="sxs-lookup"><span data-stu-id="b6e58-419">The problems that are solved here are very old.</span></span> <span data-ttu-id="b6e58-420">Perché non è possibile risolverli rapidamente, soprattutto perché non è utile per il codice esistente?</span><span class="sxs-lookup"><span data-stu-id="b6e58-420">Why suddenly solve them now, especially since it would not help existing code?</span></span>

<span data-ttu-id="b6e58-421">Come è stato C# trovato e .NET usato nei nuovi domini, alcuni problemi diventano più evidenti.</span><span class="sxs-lookup"><span data-stu-id="b6e58-421">As we find C# and .Net used in new domains, some problems become more prominent.</span></span>  
<span data-ttu-id="b6e58-422">Come esempi di ambienti più critici rispetto alla media dei sovraccarichi di calcolo, è possibile elencare</span><span class="sxs-lookup"><span data-stu-id="b6e58-422">As examples of environments that are more critical than average about computation overheads, I can list</span></span>

* <span data-ttu-id="b6e58-423">scenari di cloud/Data Center in cui il calcolo viene fatturato e la velocità di risposta è un vantaggio competitivo.</span><span class="sxs-lookup"><span data-stu-id="b6e58-423">cloud/datacenter scenarios where computation is billed for and responsiveness is a competitive advantage.</span></span>
* <span data-ttu-id="b6e58-424">Giochi/VR/AR con requisiti di tempo reale per le latenze</span><span class="sxs-lookup"><span data-stu-id="b6e58-424">Games/VR/AR with soft-realtime requirements on latencies</span></span>     

<span data-ttu-id="b6e58-425">Questa funzionalità non sacrifica i punti di forza esistenti, ad esempio la indipendenza dai tipi, consentendo al tempo stesso di ridurre i sovraccarichi in alcuni scenari comuni.</span><span class="sxs-lookup"><span data-stu-id="b6e58-425">This feature does not sacrifice any of the existing strengths such as type-safety, while allowing to lower overheads in some common scenarios.</span></span>

2) <span data-ttu-id="b6e58-426">È ragionevolmente possibile garantire che il chiamato venga riprodotto dalle regole quando si opta per i contratti `readonly`?</span><span class="sxs-lookup"><span data-stu-id="b6e58-426">Can we reasonably guarantee that the callee will play by the rules when it opts into `readonly` contracts?</span></span>

<span data-ttu-id="b6e58-427">Quando si usa `out`si ha un trust simile.</span><span class="sxs-lookup"><span data-stu-id="b6e58-427">We have similar trust when using `out`.</span></span> <span data-ttu-id="b6e58-428">Un'implementazione non corretta di `out` può causare un comportamento non specificato, ma in realtà si verifica raramente.</span><span class="sxs-lookup"><span data-stu-id="b6e58-428">Incorrect implementation of `out` can cause unspecified behavior, but in reality it rarely happens.</span></span>  

<span data-ttu-id="b6e58-429">L'esecuzione delle regole di verifica formale che hanno familiarità con `ref readonly` potrebbe ridurre ulteriormente il problema di attendibilità.</span><span class="sxs-lookup"><span data-stu-id="b6e58-429">Making the formal verification rules familiar with `ref readonly` would further mitigate the trust issue.</span></span>

### <a name="alternatives"></a><span data-ttu-id="b6e58-430">Alternativi</span><span class="sxs-lookup"><span data-stu-id="b6e58-430">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="b6e58-431">Il principale progetto in competizione è effettivamente "non fare nulla".</span><span class="sxs-lookup"><span data-stu-id="b6e58-431">The main competing design is really "do nothing".</span></span>

### <a name="unresolved-questions"></a><span data-ttu-id="b6e58-432">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="b6e58-432">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

### <a name="design-meetings"></a><span data-ttu-id="b6e58-433">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="b6e58-433">Design meetings</span></span>

<span data-ttu-id="b6e58-434">https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-02-22.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-01.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-08-28.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-09-25.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-09-27.md</span><span class="sxs-lookup"><span data-stu-id="b6e58-434">https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-02-22.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-01.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-08-28.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-09-25.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-09-27.md</span></span>
