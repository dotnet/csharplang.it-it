---
ms.openlocfilehash: d9080202f9413f8beb80db222d47f5fc082ae641
ms.sourcegitcommit: f3170512e7a3193efbcea52ec330648375e36915
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79485504"
---
# <a name="function-pointers"></a><span data-ttu-id="c0f56-101">Puntatori a funzione</span><span class="sxs-lookup"><span data-stu-id="c0f56-101">Function Pointers</span></span>

## <a name="summary"></a><span data-ttu-id="c0f56-102">Summary</span><span class="sxs-lookup"><span data-stu-id="c0f56-102">Summary</span></span>

<span data-ttu-id="c0f56-103">Questa proposta fornisce costrutti di linguaggio che espongono i codici operativi IL al quale attualmente non è possibile accedere in modo C# efficiente o, in oggi, `ldftn` e `calli`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-103">This proposal provides language constructs that expose IL opcodes that cannot currently be accessed efficiently, or at all, in C# today: `ldftn` and `calli`.</span></span> <span data-ttu-id="c0f56-104">Questi codici operativi IL può essere importante nel codice a prestazioni elevate e gli sviluppatori hanno bisogno di un modo efficiente per accedervi.</span><span class="sxs-lookup"><span data-stu-id="c0f56-104">These IL opcodes can be important in high performance code and developers need an efficient way to access them.</span></span>

## <a name="motivation"></a><span data-ttu-id="c0f56-105">Motivazione</span><span class="sxs-lookup"><span data-stu-id="c0f56-105">Motivation</span></span>

<span data-ttu-id="c0f56-106">Le motivazioni e lo sfondo per questa funzionalità sono descritti nel seguente problema (come è una potenziale implementazione della funzionalità):</span><span class="sxs-lookup"><span data-stu-id="c0f56-106">The motivations and background for this feature are described in the following issue (as is a potential implementation of the feature):</span></span>

https://github.com/dotnet/csharplang/issues/191

<span data-ttu-id="c0f56-107">Si tratta di una proposta di progettazione alternativa alle [funzioni intrinseche del compilatore](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)</span><span class="sxs-lookup"><span data-stu-id="c0f56-107">This is an alternate design proposal to [compiler intrinsics](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)</span></span>

## <a name="detailed-design"></a><span data-ttu-id="c0f56-108">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="c0f56-108">Detailed Design</span></span>

### <a name="function-pointers"></a><span data-ttu-id="c0f56-109">Puntatori funzione</span><span class="sxs-lookup"><span data-stu-id="c0f56-109">Function pointers</span></span>

<span data-ttu-id="c0f56-110">Il linguaggio consentirà la dichiarazione di puntatori a funzione usando la sintassi del `delegate*`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-110">The language will allow for the declaration of function pointers using the `delegate*` syntax.</span></span> <span data-ttu-id="c0f56-111">La sintassi completa viene descritta in dettaglio nella sezione successiva, ma è concepita come la sintassi utilizzata da `Func` e `Action` dichiarazioni di tipo.</span><span class="sxs-lookup"><span data-stu-id="c0f56-111">The full syntax is described in detail in the next section but it is meant to resemble the syntax used by `Func` and `Action` type declarations.</span></span>

``` csharp
unsafe class Example {
    void Example(Action<int> a, delegate*<int, void> f) {
        a(42);
        f(42);
    }
}
```

<span data-ttu-id="c0f56-112">Questi tipi sono rappresentati usando il tipo di puntatore a funzione, come descritto in ECMA-335.</span><span class="sxs-lookup"><span data-stu-id="c0f56-112">These types are represented using the function pointer type as outlined in ECMA-335.</span></span> <span data-ttu-id="c0f56-113">Ciò significa che la chiamata di un `delegate*` utilizzerà `calli` in cui la chiamata di un `delegate` utilizzerà `callvirt` sul metodo `Invoke`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-113">This means invocation of a `delegate*` will use `calli` where invocation of a `delegate` will use `callvirt` on the `Invoke` method.</span></span>
<span data-ttu-id="c0f56-114">Sintatticamente, sebbene la chiamata sia identica per entrambi i costrutti.</span><span class="sxs-lookup"><span data-stu-id="c0f56-114">Syntactically though invocation is identical for both constructs.</span></span>

<span data-ttu-id="c0f56-115">La definizione ECMA-335 dei puntatori al metodo include la convenzione di chiamata come parte della firma del tipo (sezione 7,1).</span><span class="sxs-lookup"><span data-stu-id="c0f56-115">The ECMA-335 definition of method pointers includes the calling convention as part of the type signature (section 7.1).</span></span>
<span data-ttu-id="c0f56-116">La convenzione di chiamata predefinita verrà `managed`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-116">The default calling convention will be `managed`.</span></span> <span data-ttu-id="c0f56-117">È possibile specificare moduli alternativi aggiungendo il modificatore appropriato dopo la sintassi del `delegate*`: `managed`, `cdecl`, `stdcall`, `thiscall`o `unmanaged`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-117">Alternate forms can be specified by adding the appropriate modifier after the `delegate*` syntax: `managed`, `cdecl`, `stdcall`, `thiscall`, or `unmanaged`.</span></span> <span data-ttu-id="c0f56-118">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c0f56-118">Example:</span></span>

``` csharp
// This method will be invoked using the cdecl calling convention
delegate* cdecl<int, int>;

// This method will be invoked using the stdcall calling convention
delegate* stdcall<int, int>;
```

<span data-ttu-id="c0f56-119">Le conversioni tra i tipi di `delegate*` vengono eseguite in base alla relativa firma, inclusa la convenzione di chiamata.</span><span class="sxs-lookup"><span data-stu-id="c0f56-119">Conversions between `delegate*` types is done based on their signature including the calling convention.</span></span>

``` csharp
unsafe class Example {
    void Conversions() {
        delegate*<int, int, int> p1 = ...;
        delegate* managed<int, int, int> p2 = ...;
        delegate* cdecl<int, int, int> p3 = ...;

        p1 = p2; // okay p1 and p2 have compatible signatures
        Console.WriteLine(p2 == p1); // True
        p2 = p3; // error: calling conventions are incompatible
    }
}
```

<span data-ttu-id="c0f56-120">Un tipo di `delegate*` è un tipo di puntatore che significa che ha tutte le funzionalità e le restrizioni di un tipo di puntatore standard:</span><span class="sxs-lookup"><span data-stu-id="c0f56-120">A `delegate*` type is a pointer type which means it has all of the capabilities and restrictions of a standard pointer type:</span></span>

- <span data-ttu-id="c0f56-121">Valido solo in un contesto di `unsafe`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-121">Only valid in an `unsafe` context.</span></span>
- <span data-ttu-id="c0f56-122">I metodi che contengono un `delegate*` parametro o un tipo restituito possono essere chiamati solo da un contesto di `unsafe`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-122">Methods which contain a `delegate*` parameter or return type can only be called from an `unsafe` context.</span></span>
- <span data-ttu-id="c0f56-123">Non può essere convertito in `object`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-123">Cannot be converted to `object`.</span></span>
- <span data-ttu-id="c0f56-124">Non può essere usato come argomento generico.</span><span class="sxs-lookup"><span data-stu-id="c0f56-124">Cannot be used as a generic argument.</span></span>
- <span data-ttu-id="c0f56-125">Può convertire in modo implicito `delegate*` in `void*`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-125">Can implicitly convert `delegate*` to `void*`.</span></span>
- <span data-ttu-id="c0f56-126">Consente di convertire in modo esplicito da `void*` a `delegate*`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-126">Can explicitly convert from `void*` to `delegate*`.</span></span>

<span data-ttu-id="c0f56-127">Restrizioni:</span><span class="sxs-lookup"><span data-stu-id="c0f56-127">Restrictions:</span></span>

- <span data-ttu-id="c0f56-128">Gli attributi personalizzati non possono essere applicati a un `delegate*` o a uno dei relativi elementi.</span><span class="sxs-lookup"><span data-stu-id="c0f56-128">Custom attributes cannot be applied to a `delegate*` or any of its elements.</span></span>
- <span data-ttu-id="c0f56-129">Non è possibile contrassegnare un parametro di `delegate*` come `params`</span><span class="sxs-lookup"><span data-stu-id="c0f56-129">A `delegate*` parameter cannot be marked as `params`</span></span>
- <span data-ttu-id="c0f56-130">Un tipo di `delegate*` presenta tutte le restrizioni di un tipo di puntatore normale.</span><span class="sxs-lookup"><span data-stu-id="c0f56-130">A `delegate*` type has all of the restrictions of a normal pointer type.</span></span>

### <a name="function-pointer-syntax"></a><span data-ttu-id="c0f56-131">Sintassi del puntatore a funzione</span><span class="sxs-lookup"><span data-stu-id="c0f56-131">Function pointer syntax</span></span>

<span data-ttu-id="c0f56-132">La sintassi del puntatore a funzione completa è rappresentata dalla grammatica seguente:</span><span class="sxs-lookup"><span data-stu-id="c0f56-132">The full function pointer syntax is represented by the following grammar:</span></span>

```antlr
pointer_type
    : ...
    | funcptr_type
    ;

funcptr_type
    : 'delegate' '*' calling_convention? '<' (funcptr_parameter_modifier? type ',')* funcptr_return_modifier? return_type '>'
    ;

calling_convention
    : 'cdecl'
    | 'managed'
    | 'stdcall'
    | 'thiscall'
    | 'unmanaged'
    ;

funcptr_parameter_modifier
    : 'ref'
    | 'out'
    | 'in'
    ;

funcptr_return_modifier
    : 'ref'
    | 'ref readonly'
    ;
```

<span data-ttu-id="c0f56-133">La convenzione di chiamata `unmanaged` rappresenta la convenzione di chiamata predefinita per il codice nativo nella piattaforma corrente e viene codificata come WINAPI.</span><span class="sxs-lookup"><span data-stu-id="c0f56-133">The `unmanaged` calling convention represents the default calling convention for native code on the current platform, and is encoded as winapi.</span></span>
<span data-ttu-id="c0f56-134">Tutte `calling_convention`s sono parole chiave contestuali quando precedute da una `delegate*`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-134">All `calling_convention`s are contextual keywords when preceded by a `delegate*`.</span></span>

``` csharp
delegate int Func1(string s);
delegate Func1 Func2(Func1 f);

// Function pointer equivalent without calling convention
delegate*<string, int>;
delegate*<delegate*<string, int>, delegate*<string, int>>;

// Function pointer equivalent with calling convention
delegate* managed<string, int>;
delegate*<delegate* managed<string, int>, delegate*<string, int>>;
```

### <a name="function-pointer-conversions"></a><span data-ttu-id="c0f56-135">Conversioni di puntatori a funzione</span><span class="sxs-lookup"><span data-stu-id="c0f56-135">Function pointer conversions</span></span>

<span data-ttu-id="c0f56-136">In un contesto non sicuro, il set di conversioni implicite disponibili (conversioni implicite) viene esteso in modo da includere le seguenti conversioni implicite del puntatore:</span><span class="sxs-lookup"><span data-stu-id="c0f56-136">In an unsafe context, the set of available implicit conversions (Implicit conversions) is extended to include the following implicit pointer conversions:</span></span>
- [<span data-ttu-id="c0f56-137">_Conversioni esistenti_</span><span class="sxs-lookup"><span data-stu-id="c0f56-137">_Existing conversions_</span></span>](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#pointer-conversions)
- <span data-ttu-id="c0f56-138">Da _funcptr\_tipo_ `F0` a un altro _tipo di\_di funcptr_ `F1`, purché siano soddisfatte tutte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="c0f56-138">From _funcptr\_type_ `F0` to another _funcptr\_type_ `F1`, provided all of the following are true:</span></span>
    - <span data-ttu-id="c0f56-139">`F0` e `F1` hanno lo stesso numero di parametri e ogni parametro `D0n` in `F0` ha gli stessi modificatori `ref`, `out`o `in` del parametro corrispondente `D1n` in `F1`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-139">`F0` and `F1` have the same number of parameters, and each parameter `D0n` in `F0` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter `D1n` in `F1`.</span></span>
    - <span data-ttu-id="c0f56-140">Per ogni parametro di valore (un parametro senza `ref`, `out`o modificatore di `in`), esiste una conversione di identità, una conversione di un riferimento implicito o una conversione del puntatore implicita dal tipo di parametro in `F0` al tipo di parametro corrispondente in `F1`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-140">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `F0` to the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="c0f56-141">Per ogni parametro `ref`, `out`o `in`, il tipo di parametro in `F0` corrisponde al tipo di parametro corrispondente in `F1`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-141">For each `ref`, `out`, or `in` parameter, the parameter type in `F0` is the same as the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="c0f56-142">Se il tipo restituito è per valore (nessuna `ref` o `ref readonly`), esiste un'identità, un riferimento implicito o una conversione del puntatore implicita dal tipo restituito di `F1` al tipo restituito di `F0`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-142">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F1` to the return type of `F0`.</span></span>
    - <span data-ttu-id="c0f56-143">Se il tipo restituito è per riferimento (`ref` o `ref readonly`), il tipo restituito e i modificatori di `ref` di `F1` corrispondono al tipo restituito e ai modificatori di `ref` di `F0`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-143">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F1` are the same as the return type and `ref` modifiers of `F0`.</span></span>
    - <span data-ttu-id="c0f56-144">La convenzione di chiamata di `F0` corrisponde alla convenzione di chiamata di `F1`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-144">The calling convention of `F0` is the same as the calling convention of `F1`.</span></span>

### <a name="allow-address-of-to-target-methods"></a><span data-ttu-id="c0f56-145">Consenti indirizzo-di ai metodi di destinazione</span><span class="sxs-lookup"><span data-stu-id="c0f56-145">Allow address-of to target methods</span></span>

<span data-ttu-id="c0f56-146">I gruppi di metodi saranno ora consentiti come argomenti per un'espressione address-of.</span><span class="sxs-lookup"><span data-stu-id="c0f56-146">Method groups will now be allowed as arguments to an address-of expression.</span></span> <span data-ttu-id="c0f56-147">Il tipo di tale espressione sarà un `delegate*` che ha la firma equivalente del metodo di destinazione e una convenzione di chiamata gestita:</span><span class="sxs-lookup"><span data-stu-id="c0f56-147">The type of such an expression will be a `delegate*` which has the equivalent signature of the target method and a managed calling convention:</span></span>

``` csharp
unsafe class Util {
    public static void Log() { }

    void Use() {
        delegate*<void> ptr1 = &Util.Log;

        // Error: type "delegate*<void>" not compatible with "delegate*<int>";
        delegate*<int> ptr2 = &Util.Log;

        // Okay. Conversion to void* is always allowed.
        void* v = &Util.Log;
   }
}
```

<span data-ttu-id="c0f56-148">In un contesto unsafe, un metodo `M` è compatibile con un tipo di puntatore a funzione `F` se si verificano tutte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="c0f56-148">In an unsafe context, a method `M` is compatible with a function pointer type `F` if all of the following are true:</span></span>
- <span data-ttu-id="c0f56-149">`M` e `F` hanno lo stesso numero di parametri e ogni parametro in `D` ha gli stessi modificatori `ref`, `out`o `in` del parametro corrispondente in `F`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-149">`M` and `F` have the same number of parameters, and each parameter in `D` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter in `F`.</span></span>
- <span data-ttu-id="c0f56-150">Per ogni parametro di valore (un parametro senza `ref`, `out`o modificatore di `in`), esiste una conversione di identità, una conversione di un riferimento implicito o una conversione del puntatore implicita dal tipo di parametro in `M` al tipo di parametro corrispondente in `F`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-150">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `M` to the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="c0f56-151">Per ogni parametro `ref`, `out`o `in`, il tipo di parametro in `M` corrisponde al tipo di parametro corrispondente in `F`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-151">For each `ref`, `out`, or `in` parameter, the parameter type in `M` is the same as the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="c0f56-152">Se il tipo restituito è per valore (nessuna `ref` o `ref readonly`), esiste un'identità, un riferimento implicito o una conversione del puntatore implicita dal tipo restituito di `F` al tipo restituito di `M`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-152">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F` to the return type of `M`.</span></span>
- <span data-ttu-id="c0f56-153">Se il tipo restituito è per riferimento (`ref` o `ref readonly`), il tipo restituito e i modificatori di `ref` di `F` corrispondono al tipo restituito e ai modificatori di `ref` di `M`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-153">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F` are the same as the return type and `ref` modifiers of `M`.</span></span>
- <span data-ttu-id="c0f56-154">La convenzione di chiamata di `M` corrisponde alla convenzione di chiamata di `F`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-154">The calling convention of `M` is the same as the calling convention of `F`.</span></span>
- <span data-ttu-id="c0f56-155">`M` è un metodo statico.</span><span class="sxs-lookup"><span data-stu-id="c0f56-155">`M` is a static method.</span></span>

<span data-ttu-id="c0f56-156">In un contesto non sicuro, esiste una conversione implicita da un'espressione address-of la cui destinazione è un gruppo di metodi `E` a un tipo di puntatore a funzione compatibile `F` se `E` contiene almeno un metodo applicabile nel formato normale a un elenco di argomenti costruito usando i tipi di parametro e i modificatori di `F`, come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="c0f56-156">In an unsafe context, an implicit conversion exists from an address-of expression whose target is a method group `E` to a compatible function pointer type `F` if `E` contains at least one method that is applicable in its normal form to an argument list constructed by use of the parameter types and modifiers of `F`, as described in the following.</span></span>
- <span data-ttu-id="c0f56-157">Viene selezionato un solo metodo `M` corrispondente a una chiamata al metodo del form `E(A)` con le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="c0f56-157">A single method `M` is selected corresponding to a method invocation of the form `E(A)` with the following modifications:</span></span>
   - <span data-ttu-id="c0f56-158">L'elenco di argomenti `A` è un elenco di espressioni, ciascuna classificata come variabile e con il tipo e il modificatore (`ref`, `out`o `in`) del _parametro\_formale corrispondente\_elenco_ di `D`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-158">The arguments list `A` is a list of expressions, each classified as a variable and with the type and modifier (`ref`, `out`, or `in`) of the corresponding _formal\_parameter\_list_ of `D`.</span></span>
   - <span data-ttu-id="c0f56-159">I metodi candidati sono solo i metodi che sono solo quelli applicabili nel formato normale, non quelli applicabili nella forma espansa.</span><span class="sxs-lookup"><span data-stu-id="c0f56-159">The candidate methods are only those methods that are only those methods that are applicable in their normal form, not those applicable in their expanded form.</span></span>
- <span data-ttu-id="c0f56-160">Se l'algoritmo delle chiamate di metodo genera un errore, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="c0f56-160">If the algorithm of Method invocations produces an error, then a compile-time error occurs.</span></span> <span data-ttu-id="c0f56-161">In caso contrario, l'algoritmo produce un unico metodo migliore `M` avere lo stesso numero di parametri `F` e la conversione viene considerata esistente.</span><span class="sxs-lookup"><span data-stu-id="c0f56-161">Otherwise, the algorithm produces a single best method `M` having the same number of parameters as `F` and the conversion is considered to exist.</span></span>
- <span data-ttu-id="c0f56-162">Il metodo selezionato `M` deve essere compatibile (come definito in precedenza) con il tipo di puntatore a funzione `F`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-162">The selected method `M` must be compatible (as defined above) with the function pointer type `F`.</span></span> <span data-ttu-id="c0f56-163">In caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="c0f56-163">Otherwise, a compile-time error occurs.</span></span>
- <span data-ttu-id="c0f56-164">Il risultato della conversione è un puntatore a funzione di tipo `F`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-164">The result of the conversion is a function pointer of type `F`.</span></span>

<span data-ttu-id="c0f56-165">Esiste una conversione implicita da un'espressione address-of la cui destinazione è un gruppo di metodi `E` `void*` se è presente un solo metodo statico `M` in `E`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-165">An implicit conversion exists from an address-of expression whose target is a method group `E` to `void*` if there is only one static method `M` in `E`.</span></span>
<span data-ttu-id="c0f56-166">Se è presente un metodo statico, il singolo metodo migliore da `E` è `M`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-166">If there is one static method, then the single best method from `E` is `M`.</span></span>
<span data-ttu-id="c0f56-167">In caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="c0f56-167">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="c0f56-168">Ciò significa che gli sviluppatori possono dipendere dalle regole di risoluzione dell'overload per lavorare insieme all'operatore address-of:</span><span class="sxs-lookup"><span data-stu-id="c0f56-168">This means developers can depend on overload resolution rules to work in conjunction with the address-of operator:</span></span>

``` csharp
unsafe class Util {
    public static void Log() { }
    public static void Log(string p1) { }
    public static void Log(int i) { };

    void Use() {
        delegate*<void> a1 = &Log; // Log()
        delegate*<int, void> a2 = &Log; // Log(int i)

        // Error: ambiguous conversion from method group Log to "void*"
        void* v = &Log;
    }
```

<span data-ttu-id="c0f56-169">L'operatore address-of verrà implementato utilizzando l'istruzione `ldftn`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-169">The address-of operator will be implemented using the `ldftn` instruction.</span></span>

<span data-ttu-id="c0f56-170">Limitazioni di questa funzionalità:</span><span class="sxs-lookup"><span data-stu-id="c0f56-170">Restrictions of this feature:</span></span>

- <span data-ttu-id="c0f56-171">Si applica solo ai metodi contrassegnati come `static`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-171">Only applies to methods marked as `static`.</span></span>
- <span data-ttu-id="c0f56-172">Impossibile utilizzare le funzioni locali non`static` in `&`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-172">Non-`static` local functions cannot be used in `&`.</span></span> <span data-ttu-id="c0f56-173">I dettagli di implementazione di questi metodi non sono deliberatamente specificati dal linguaggio.</span><span class="sxs-lookup"><span data-stu-id="c0f56-173">The implementation details of these methods are deliberately not specified by the language.</span></span> <span data-ttu-id="c0f56-174">Ciò include la presenza di un'istanza statica rispetto a quella con cui vengono emesse le firme.</span><span class="sxs-lookup"><span data-stu-id="c0f56-174">This includes whether they are static vs. instance or exactly what signature they are emitted with.</span></span>

### <a name="better-function-member"></a><span data-ttu-id="c0f56-175">Membro di funzione migliore</span><span class="sxs-lookup"><span data-stu-id="c0f56-175">Better function member</span></span>

<span data-ttu-id="c0f56-176">La specifica del membro di funzione migliore verrà modificata in modo da includere la riga seguente:</span><span class="sxs-lookup"><span data-stu-id="c0f56-176">The better function member specification will be changed to include the following line:</span></span>

> <span data-ttu-id="c0f56-177">Una `delegate*` è più specifica di `void*`</span><span class="sxs-lookup"><span data-stu-id="c0f56-177">A `delegate*` is more specific than `void*`</span></span>

<span data-ttu-id="c0f56-178">Ciò significa che è possibile eseguire l'overload su `void*` e un `delegate*` e comunque utilizzare l'operatore address-of.</span><span class="sxs-lookup"><span data-stu-id="c0f56-178">This means that it is possible to overload on `void*` and a `delegate*` and still sensibly use the address-of operator.</span></span>

## <a name="open-issues"></a><span data-ttu-id="c0f56-179">Problemi aperti</span><span class="sxs-lookup"><span data-stu-id="c0f56-179">Open Issues</span></span>

### <a name="nativecallableattribute"></a><span data-ttu-id="c0f56-180">NativeCallableAttribute</span><span class="sxs-lookup"><span data-stu-id="c0f56-180">NativeCallableAttribute</span></span>

<span data-ttu-id="c0f56-181">Si tratta di un attributo utilizzato da CLR per evitare il prologo gestito a nativo quando si richiama.</span><span class="sxs-lookup"><span data-stu-id="c0f56-181">This is an attribute used by the CLR to avoid the managed to native prologue when invoking.</span></span> <span data-ttu-id="c0f56-182">I metodi contrassegnati da questo attributo sono richiamabili solo da codice nativo, non gestito (non è possibile chiamare metodi, creare un delegato e così via).</span><span class="sxs-lookup"><span data-stu-id="c0f56-182">Methods marked by this attribute are only callable from native code, not managed (can’t call methods, create a delegate, etc …).</span></span> <span data-ttu-id="c0f56-183">L'attributo non è speciale per mscorlib; il runtime considererà qualsiasi attributo con questo nome con la stessa semantica.</span><span class="sxs-lookup"><span data-stu-id="c0f56-183">The attribute is not special to mscorlib; the runtime will treat any attribute with this name with the same semantics.</span></span>

<span data-ttu-id="c0f56-184">È possibile che il runtime e il linguaggio collaborino per supportare completamente questa operazione.</span><span class="sxs-lookup"><span data-stu-id="c0f56-184">It's possible for the runtime and language to work together to fully support this.</span></span> <span data-ttu-id="c0f56-185">Il linguaggio può scegliere di considerare i membri `static` address-of con un attributo `NativeCallable` come `delegate*` con la convenzione di chiamata specificata.</span><span class="sxs-lookup"><span data-stu-id="c0f56-185">The language could choose to treat address-of `static` members with a `NativeCallable` attribute as a `delegate*` with the specified calling convention.</span></span>

``` csharp
unsafe class NativeCallableExample {
    [NativeCallable(CallingConvention.CDecl)]
    static void CloseHandle(IntPtr p) => Marshal.FreeHGlobal(p);

    void Use() {
        delegate*<IntPtr, void> p1 = &CloseHandle; // Error: Invalid calling convention

        delegate* cdecl<IntPtr, void> p2 = &CloseHandle; // Okay
    }
}

```

<span data-ttu-id="c0f56-186">Inoltre, è probabile che il linguaggio desideri:</span><span class="sxs-lookup"><span data-stu-id="c0f56-186">Additionally the language would likely also want to:</span></span>

- <span data-ttu-id="c0f56-187">Contrassegnare qualsiasi chiamata gestita a un metodo contrassegnato con `NativeCallable` come un errore.</span><span class="sxs-lookup"><span data-stu-id="c0f56-187">Flag any managed calls to a method tagged with `NativeCallable` as an error.</span></span> <span data-ttu-id="c0f56-188">Dato che la funzione non può essere richiamata dal codice gestito, il compilatore deve impedire agli sviluppatori di provare tale chiamata.</span><span class="sxs-lookup"><span data-stu-id="c0f56-188">Given the function can't be invoked from managed code the compiler should prevent developers from attempting such an invocation.</span></span>
- <span data-ttu-id="c0f56-189">Impedisci le conversioni dei gruppi di metodi `delegate` quando il metodo è contrassegnato con `NativeCallable`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-189">Prevent method group conversions to `delegate` when the method is tagged with `NativeCallable`.</span></span>

<span data-ttu-id="c0f56-190">Questa operazione non è necessaria per supportare `NativeCallable` tuttavia.</span><span class="sxs-lookup"><span data-stu-id="c0f56-190">This is not necessary to support `NativeCallable` though.</span></span> <span data-ttu-id="c0f56-191">Il compilatore è in grado di supportare l'attributo `NativeCallable` come utilizza la sintassi esistente.</span><span class="sxs-lookup"><span data-stu-id="c0f56-191">The compiler can support the `NativeCallable` attribute as is using the existing syntax.</span></span> <span data-ttu-id="c0f56-192">Il programma deve semplicemente eseguire il cast a `void*` prima di eseguire il cast alla firma `delegate*` corretta.</span><span class="sxs-lookup"><span data-stu-id="c0f56-192">The program would simply need to cast to `void*` before casting to the correct `delegate*` signature.</span></span> <span data-ttu-id="c0f56-193">Questa situazione non è ancora peggiore del supporto.</span><span class="sxs-lookup"><span data-stu-id="c0f56-193">That would be no worse than the support today.</span></span>

``` csharp
void* v = &CloseHandle;
delegate* cdecl<IntPtr, bool> f1 = (delegate* cdecl<IntPtr, bool>)v;
```

### <a name="extensible-set-of-unmanaged-calling-conventions"></a><span data-ttu-id="c0f56-194">Set estensibile di convenzioni di chiamata non gestite</span><span class="sxs-lookup"><span data-stu-id="c0f56-194">Extensible set of unmanaged calling conventions</span></span>

<span data-ttu-id="c0f56-195">Il set di convenzioni di chiamata non gestite supportate dalle codifiche ECMA-335 correnti è obsoleto.</span><span class="sxs-lookup"><span data-stu-id="c0f56-195">The set of unmanaged calling conventions supported by the current ECMA-335 encodings is outdated.</span></span> <span data-ttu-id="c0f56-196">Sono state rilevate richieste per aggiungere il supporto per più convenzioni di chiamata non gestite, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c0f56-196">We have seen requests to add support for more unmanaged calling conventions, for example:</span></span>

- <span data-ttu-id="c0f56-197">https://github.com/dotnet/coreclr/issues/12120 [vectorcall](https://docs.microsoft.com/cpp/cpp/vectorcall)</span><span class="sxs-lookup"><span data-stu-id="c0f56-197">[vectorcall](https://docs.microsoft.com/cpp/cpp/vectorcall) https://github.com/dotnet/coreclr/issues/12120</span></span>
- <span data-ttu-id="c0f56-198">StdCall con questa https://github.com/dotnet/coreclr/pull/23974#issuecomment-482991750 esplicita</span><span class="sxs-lookup"><span data-stu-id="c0f56-198">StdCall with explicit this https://github.com/dotnet/coreclr/pull/23974#issuecomment-482991750</span></span>

<span data-ttu-id="c0f56-199">La progettazione di questa funzionalità consente di estendere il set di convenzioni di chiamata non gestite in base alle esigenze in futuro.</span><span class="sxs-lookup"><span data-stu-id="c0f56-199">The design of this feature should allow extending the set of unmanaged calling conventions as needed in future.</span></span> <span data-ttu-id="c0f56-200">I problemi includono spazio limitato per la codifica delle convenzioni di chiamata (12 valori di 16 vengono presi in `IMAGE_CEE_CS_CALLCONV_MASK`) e il numero di posizioni che devono essere modificate per aggiungere una nuova convenzione di chiamata.</span><span class="sxs-lookup"><span data-stu-id="c0f56-200">The problems include limited space for encoding calling conventions (12 out of 16 values are taken in `IMAGE_CEE_CS_CALLCONV_MASK`) and number of places that need to be touched in order to add a new calling convention.</span></span> <span data-ttu-id="c0f56-201">Una possibile soluzione consiste nell'introdurre una nuova codifica che rappresenti la convenzione di chiamata usando [`System.Runtime.InteropServices.CallingConvention`](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.callingconvention) enum.</span><span class="sxs-lookup"><span data-stu-id="c0f56-201">A potential solution is to introduce a new encoding that represents the calling convention using [`System.Runtime.InteropServices.CallingConvention`](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.callingconvention) enum.</span></span>

<span data-ttu-id="c0f56-202">Per riferimento https://github.com/llvm/llvm-project/blob/master/llvm/include/llvm/IR/CallingConv.h contiene l'elenco delle convenzioni di chiamata supportate da LLVM.</span><span class="sxs-lookup"><span data-stu-id="c0f56-202">For reference, https://github.com/llvm/llvm-project/blob/master/llvm/include/llvm/IR/CallingConv.h has the list of calling conventions supported by LLVM.</span></span> <span data-ttu-id="c0f56-203">Sebbene sia improbabile che .NET debba necessariamente supportare tutti questi elementi, dimostra che lo spazio delle convenzioni di chiamata è molto ricco.</span><span class="sxs-lookup"><span data-stu-id="c0f56-203">While it is unlikely that .NET will ever need to support all of them, it demonstrates that the space of calling conventions is very rich.</span></span>

## <a name="considerations"></a><span data-ttu-id="c0f56-204">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="c0f56-204">Considerations</span></span>

### <a name="allow-instance-methods"></a><span data-ttu-id="c0f56-205">Consenti metodi di istanza</span><span class="sxs-lookup"><span data-stu-id="c0f56-205">Allow instance methods</span></span>

<span data-ttu-id="c0f56-206">È possibile estendere la proposta per supportare i metodi di istanza sfruttando la convenzione di chiamata dell'interfaccia della riga di C# comando `EXPLICITTHIS` (denominata `instance` nel codice).</span><span class="sxs-lookup"><span data-stu-id="c0f56-206">The proposal could be extended to support instance methods by taking advantage of the `EXPLICITTHIS` CLI calling convention (named `instance` in C# code).</span></span> <span data-ttu-id="c0f56-207">Questa forma di puntatori a funzione dell'interfaccia della riga di comando inserisce il parametro `this` come primo parametro esplicito della sintassi del puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="c0f56-207">This form of CLI function pointers puts the `this` parameter as an explicit first parameter of the function pointer syntax.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        delegate* instance<Instance, string> f = &ToString;
        f(this);
    }
}
```

<span data-ttu-id="c0f56-208">Si tratta di un suono che aggiunge alcune complicazioni alla proposta.</span><span class="sxs-lookup"><span data-stu-id="c0f56-208">This is sound but adds some complication to the proposal.</span></span> <span data-ttu-id="c0f56-209">In particolare, poiché i puntatori a funzione che differiscono dalla convenzione di chiamata `instance` e `managed` sarebbero incompatibili anche se entrambi i casi vengono usati per richiamare metodi C# gestiti con la stessa firma.</span><span class="sxs-lookup"><span data-stu-id="c0f56-209">Particularly because function pointers which differed by the calling convention `instance` and `managed` would be incompatible even though both cases are used to invoke managed methods with the same C# signature.</span></span> <span data-ttu-id="c0f56-210">Anche in tutti i casi in cui questo potrebbe essere utile avere un lavoro semplice: usare una funzione locale `static`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-210">Also in every case considered where this would be valuable to have there was a simple work around: use a `static` local function.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        static string toString(Instance i) = i.ToString();
        delgate*<Instance, string> f = &toString;
        f(this);
    }
}
```

### <a name="dont-require-unsafe-at-declaration"></a><span data-ttu-id="c0f56-211">Non richiedere unsafe alla dichiarazione</span><span class="sxs-lookup"><span data-stu-id="c0f56-211">Don't require unsafe at declaration</span></span>

<span data-ttu-id="c0f56-212">Anziché richiedere `unsafe` a ogni utilizzo di un `delegate*`, richiederlo solo nel punto in cui un gruppo di metodi viene convertito in un `delegate*`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-212">Instead of requiring `unsafe` at every use of a `delegate*`, only require it at the point where a method group is converted to a `delegate*`.</span></span> <span data-ttu-id="c0f56-213">Questo è il punto in cui vengono rilevati i problemi di sicurezza di base (sapendo che l'assembly contenitore non può essere scaricato mentre il valore è attivo).</span><span class="sxs-lookup"><span data-stu-id="c0f56-213">This is where the core safety issues come into play (knowing that the containing assembly cannot be unloaded while the value is alive).</span></span> <span data-ttu-id="c0f56-214">La richiesta di `unsafe` nelle altre posizioni può essere considerata eccessiva.</span><span class="sxs-lookup"><span data-stu-id="c0f56-214">Requiring `unsafe` on the other locations can be seen as excessive.</span></span>

<span data-ttu-id="c0f56-215">Questo è il modo in cui la progettazione è stata originariamente progettata.</span><span class="sxs-lookup"><span data-stu-id="c0f56-215">This is how the design was originally intended.</span></span> <span data-ttu-id="c0f56-216">Ma le regole del linguaggio risultavano molto scomode.</span><span class="sxs-lookup"><span data-stu-id="c0f56-216">But the resulting language rules felt very awkward.</span></span> <span data-ttu-id="c0f56-217">Non è possibile nascondere il fatto che si tratta di un valore di puntatore e la visualizzazione continua anche senza la parola chiave `unsafe`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-217">It's impossible to hide the fact that this is a pointer value and it kept peeking through even without the `unsafe` keyword.</span></span> <span data-ttu-id="c0f56-218">La conversione in `object`, ad esempio, non può essere consentita, ma non può essere un membro di un `class`e così via. Il C# progetto prevede di richiedere `unsafe` per tutti gli utilizzi dei puntatori e di conseguenza questa progettazione lo segue.</span><span class="sxs-lookup"><span data-stu-id="c0f56-218">For example the conversion to `object` can't be allowed, it can't be a member of a `class`, etc ... The C# design is to require `unsafe` for all pointer uses and hence this design follows that.</span></span>

<span data-ttu-id="c0f56-219">Gli sviluppatori saranno comunque in grado di presentare un wrapper _sicuro_ oltre ai valori `delegate*` nello stesso modo in cui fanno oggi i normali tipi di puntatore.</span><span class="sxs-lookup"><span data-stu-id="c0f56-219">Developers will still be capable of presenting a _safe_ wrapper on top of `delegate*` values the same way that they do for normal pointer types today.</span></span> <span data-ttu-id="c0f56-220">Prendere in considerazione:</span><span class="sxs-lookup"><span data-stu-id="c0f56-220">Consider:</span></span>

``` csharp
unsafe struct Action {
    delegate*<void> _ptr;

    Action(delegate*<void> ptr) => _ptr = ptr;
    public void Invoke() => _ptr();
}
```

### <a name="using-delegates"></a><span data-ttu-id="c0f56-221">Uso di delegati</span><span class="sxs-lookup"><span data-stu-id="c0f56-221">Using delegates</span></span>

<span data-ttu-id="c0f56-222">Invece di usare un nuovo elemento della sintassi, `delegate*`, è sufficiente usare i tipi di `delegate` esistenti con una `*` che segue il tipo:</span><span class="sxs-lookup"><span data-stu-id="c0f56-222">Instead of using a new syntax element, `delegate*`, simply use existing `delegate` types with a `*` following the type:</span></span>

``` csharp
Func<object, object, bool>* ptr = &object.ReferenceEquals;
```

<span data-ttu-id="c0f56-223">La gestione della convenzione di chiamata può essere eseguita annotando i tipi di `delegate` con un attributo che specifica un valore `CallingConvention`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-223">Handling calling convention can be done by annotating the `delegate` types with an attribute that specifies a `CallingConvention` value.</span></span> <span data-ttu-id="c0f56-224">La mancanza di un attributo significa la convenzione di chiamata gestita.</span><span class="sxs-lookup"><span data-stu-id="c0f56-224">The lack of an attribute would signify the managed calling convention.</span></span>

<span data-ttu-id="c0f56-225">La codifica in IL è problematica.</span><span class="sxs-lookup"><span data-stu-id="c0f56-225">Encoding this in IL is problematic.</span></span> <span data-ttu-id="c0f56-226">Il valore sottostante deve essere rappresentato come puntatore, ma deve anche:</span><span class="sxs-lookup"><span data-stu-id="c0f56-226">The underlying value needs to be represented as a pointer yet it also must:</span></span>

1. <span data-ttu-id="c0f56-227">Hanno un tipo univoco per consentire gli overload con tipi di puntatore a funzione diversi.</span><span class="sxs-lookup"><span data-stu-id="c0f56-227">Have a unique type to allow for overloads with different function pointer types.</span></span>
1. <span data-ttu-id="c0f56-228">Essere equivalente a scopi OHI tra i limiti di assembly.</span><span class="sxs-lookup"><span data-stu-id="c0f56-228">Be equivalent for OHI purposes across assembly boundaries.</span></span>

<span data-ttu-id="c0f56-229">L'ultimo punto è particolarmente problematico.</span><span class="sxs-lookup"><span data-stu-id="c0f56-229">The last point is particularly problematic.</span></span> <span data-ttu-id="c0f56-230">Ciò significa che ogni assembly che usa `Func<int>*` deve codificare un tipo equivalente nei metadati anche se `Func<int>*` è definito in un assembly, sebbene non controlli.</span><span class="sxs-lookup"><span data-stu-id="c0f56-230">This mean that every assembly which uses `Func<int>*` must encode an equivalent type in metadata even though `Func<int>*` is defined in an assembly though don't control.</span></span>
<span data-ttu-id="c0f56-231">Inoltre, tutti gli altri tipi definiti con il nome `System.Func<T>` in un assembly che non è mscorlib devono essere diversi dalla versione definita in mscorlib.</span><span class="sxs-lookup"><span data-stu-id="c0f56-231">Additionally any other type which is defined with the name `System.Func<T>` in an assembly that is not mscorlib must be different than the version defined in mscorlib.</span></span>

<span data-ttu-id="c0f56-232">Un'opzione esplorata è stata la creazione di un puntatore di questo tipo come `mod_req(Func<int>) void*`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-232">One option that was explored was emitting such a pointer as `mod_req(Func<int>) void*`.</span></span> <span data-ttu-id="c0f56-233">Questa operazione non funziona anche se un `mod_req` non può essere associato a una `TypeSpec` e pertanto non può fare riferimento a creazioni di istanze generiche.</span><span class="sxs-lookup"><span data-stu-id="c0f56-233">This doesn't work though as a `mod_req` cannot bind to a `TypeSpec` and hence cannot target generic instantiations.</span></span>

### <a name="named-function-pointers"></a><span data-ttu-id="c0f56-234">Puntatori a funzioni denominate</span><span class="sxs-lookup"><span data-stu-id="c0f56-234">Named function pointers</span></span>

<span data-ttu-id="c0f56-235">La sintassi del puntatore a funzione può essere complessa, soprattutto in casi complessi come i puntatori a funzioni annidate.</span><span class="sxs-lookup"><span data-stu-id="c0f56-235">The function pointer syntax can be cumbersome, particularly in complex cases like nested function pointers.</span></span> <span data-ttu-id="c0f56-236">Anziché fare in modo che gli sviluppatori digitino la firma ogni volta che la lingua potrebbe consentire le dichiarazioni denominate dei puntatori a funzione, come avviene con `delegate`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-236">Rather than have developers type out the signature every time the language could allow for named declarations of function pointers as is done with `delegate`.</span></span>

``` csharp
func* void Action();

unsafe class NamedExample {
    void M(Action a) {
        a();
    }
}
```

<span data-ttu-id="c0f56-237">Parte del problema è che la primitiva dell'interfaccia della riga di comando sottostante non ha nomi, C# pertanto si tratterebbe puramente di un'invenzione e richiederebbe un po' di lavoro di metadati da abilitare.</span><span class="sxs-lookup"><span data-stu-id="c0f56-237">Part of the problem here is the underlying CLI primitive doesn't have names hence this would be purely a C# invention and require a bit of metadata work to enable.</span></span> <span data-ttu-id="c0f56-238">Questa operazione è fattibile, ma è una questione significativa di lavoro.</span><span class="sxs-lookup"><span data-stu-id="c0f56-238">That is doable but is a significant about of work.</span></span> <span data-ttu-id="c0f56-239">In sostanza C# , è necessario disporre di un complementare alla tabella def del tipo esclusivamente per questi nomi.</span><span class="sxs-lookup"><span data-stu-id="c0f56-239">It essentially requires C# to have a companion to the type def table purely for these names.</span></span>

<span data-ttu-id="c0f56-240">Inoltre, quando sono stati esaminati gli argomenti per i puntatori a funzione denominati, è possibile che si applichino ugualmente a diversi altri scenari.</span><span class="sxs-lookup"><span data-stu-id="c0f56-240">Also when the arguments for named function pointers were examined we found they could apply equally well to a number of other scenarios.</span></span> <span data-ttu-id="c0f56-241">Ad esempio, sarebbe altrettanto semplice dichiarare le tuple denominate per ridurre la necessità di digitare la firma completa in tutti i casi.</span><span class="sxs-lookup"><span data-stu-id="c0f56-241">For example it would be just as convenient to declare named tuples to reduce the need to type out the full signature in all cases.</span></span>

``` csharp
(int x, int y) Point;

class NamedTupleExample {
    void M(Point p) {
        Console.WriteLine(p.x);
    }
}
```

<span data-ttu-id="c0f56-242">Dopo la discussione si è deciso di non consentire la dichiarazione denominata dei tipi di `delegate*`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-242">After discussion we decided to not allow named declaration of `delegate*` types.</span></span> <span data-ttu-id="c0f56-243">Se viene rilevata una necessità significativa in base ai commenti e suggerimenti sull'utilizzo dei clienti, viene esaminata una soluzione di denominazione che funziona per i puntatori a funzione, le tuple, i generics e così via. È probabile che questo aspetto sia simile ad altri suggerimenti, come il supporto completo `typedef` nel linguaggio.</span><span class="sxs-lookup"><span data-stu-id="c0f56-243">If we find there is significant need for this based on customer usage feedback then we will investigate a naming solution that works for function pointers, tuples, generics, etc ... This is likely to be similar in form to other suggestions like full `typedef` support in the language.</span></span>

## <a name="future-considerations"></a><span data-ttu-id="c0f56-244">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="c0f56-244">Future Considerations</span></span>

### <a name="static-local-functions"></a><span data-ttu-id="c0f56-245">funzioni locali statiche</span><span class="sxs-lookup"><span data-stu-id="c0f56-245">static local functions</span></span>

<span data-ttu-id="c0f56-246">Si fa riferimento alla [proposta](https://github.com/dotnet/csharplang/issues/1565) per consentire il modificatore `static` sulle funzioni locali.</span><span class="sxs-lookup"><span data-stu-id="c0f56-246">This refers to [the proposal](https://github.com/dotnet/csharplang/issues/1565) to allow the `static` modifier on local functions.</span></span> <span data-ttu-id="c0f56-247">Una funzione di questo tipo verrebbe generata come `static` e con la firma esatta specificata nel codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="c0f56-247">Such a function would be guaranteed to be emitted as `static` and with the exact signature specified in source code.</span></span> <span data-ttu-id="c0f56-248">Una funzione di questo tipo deve essere un argomento valido per `&` perché non contiene alcun problema attualmente presente nelle funzioni locali</span><span class="sxs-lookup"><span data-stu-id="c0f56-248">Such a function should be a valid argument to `&` as it contains none of the problems local functions have today</span></span>

### <a name="static-delegates"></a><span data-ttu-id="c0f56-249">delegati statici</span><span class="sxs-lookup"><span data-stu-id="c0f56-249">static delegates</span></span>

<span data-ttu-id="c0f56-250">Si fa riferimento alla [proposta](https://github.com/dotnet/csharplang/issues/302) per consentire la dichiarazione di tipi di `delegate` che possono fare riferimento solo ai membri `static`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-250">This refers to [the proposal](https://github.com/dotnet/csharplang/issues/302) to allow for the declaration of `delegate` types which can only refer to `static` members.</span></span> <span data-ttu-id="c0f56-251">Il vantaggio è che tali istanze di `delegate` possono essere allocazione gratuite e migliori negli scenari sensibili alle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="c0f56-251">The advantage being that such `delegate` instances can be allocation free and better in performance sensitive scenarios.</span></span>

<span data-ttu-id="c0f56-252">Se la funzionalità del puntatore a funzione è implementata, è probabile che la proposta di `static delegate` venga chiusa. Il vantaggio proposto da questa funzionalità è la natura di allocazione.</span><span class="sxs-lookup"><span data-stu-id="c0f56-252">If the function pointer feature is implemented the `static delegate` proposal will likely be closed out. The proposed advantage of that feature is the allocation free nature.</span></span> <span data-ttu-id="c0f56-253">Sono state rilevate tuttavia ricerche recenti che non è possibile ottenere a causa dello scaricamento di assembly.</span><span class="sxs-lookup"><span data-stu-id="c0f56-253">However recent investigations have found that is not possible to achieve due to assembly unloading.</span></span> <span data-ttu-id="c0f56-254">Il `static delegate` al metodo a cui si riferisce deve essere un handle sicuro per evitare che l'assembly venga scaricato dall'interno.</span><span class="sxs-lookup"><span data-stu-id="c0f56-254">There must be a strong handle from the `static delegate` to the method it refers to in order to keep the assembly from being unloaded out from under it.</span></span>

<span data-ttu-id="c0f56-255">Per mantenere ogni `static delegate` istanza è necessario allocare un nuovo handle che esegue il contatore agli obiettivi della proposta.</span><span class="sxs-lookup"><span data-stu-id="c0f56-255">To maintain every `static delegate` instance would be required to allocate a new handle which runs counter to the goals of the proposal.</span></span> <span data-ttu-id="c0f56-256">Sono stati creati alcuni progetti in cui l'allocazione può essere ammortizzata in una singola allocazione per ogni chiamata, ma si tratta di un po' complesso che non sembra essere il compromesso.</span><span class="sxs-lookup"><span data-stu-id="c0f56-256">There were some designs where the allocation could be amortized to a single allocation per call-site but that was a bit complex and didn't seem worth the trade off.</span></span>

<span data-ttu-id="c0f56-257">Ciò significa che gli sviluppatori devono essenzialmente decidere tra i compromessi seguenti:</span><span class="sxs-lookup"><span data-stu-id="c0f56-257">That means developers essentially have to decide between the following trade offs:</span></span>

1. <span data-ttu-id="c0f56-258">Sicurezza in caso di scaricamento di assembly: è necessario allocazioni e quindi `delegate` è già un'opzione sufficiente.</span><span class="sxs-lookup"><span data-stu-id="c0f56-258">Safety in the face of assembly unloading: this requires allocations and hence `delegate` is already a sufficient option.</span></span>
1. <span data-ttu-id="c0f56-259">Nessuna protezione in caso di scaricamento di assembly: usare un `delegate*`.</span><span class="sxs-lookup"><span data-stu-id="c0f56-259">No safety in face of assembly unloading: use a `delegate*`.</span></span> <span data-ttu-id="c0f56-260">Questo può essere incluso in un `struct` per consentire l'utilizzo all'esterno di un contesto di `unsafe` nel resto del codice.</span><span class="sxs-lookup"><span data-stu-id="c0f56-260">This can be wrapped in a `struct` to allow usage outside an `unsafe` context in the rest of the code.</span></span>
