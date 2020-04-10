---
ms.openlocfilehash: 9e7059b579060e547e4665ac50518581fe3512e6
ms.sourcegitcommit: 52624f54c0ad99a4d659fe4e2560a472be795c49
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81005704"
---
# <a name="function-pointers"></a><span data-ttu-id="7b74f-101">Puntatori a funzione</span><span class="sxs-lookup"><span data-stu-id="7b74f-101">Function Pointers</span></span>

## <a name="summary"></a><span data-ttu-id="7b74f-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="7b74f-102">Summary</span></span>

<span data-ttu-id="7b74f-103">Questa proposta fornisce costrutti di linguaggio che espongono i codici operativi `ldftn` IL `calli`a cui non è attualmente possibile accedere in modo efficiente o, oggi in C: e .</span><span class="sxs-lookup"><span data-stu-id="7b74f-103">This proposal provides language constructs that expose IL opcodes that cannot currently be accessed efficiently, or at all, in C# today: `ldftn` and `calli`.</span></span> <span data-ttu-id="7b74f-104">Questi codici operativi IL possono essere importanti nel codice ad alte prestazioni e gli sviluppatori hanno bisogno di un modo efficiente per accedervi.</span><span class="sxs-lookup"><span data-stu-id="7b74f-104">These IL opcodes can be important in high performance code and developers need an efficient way to access them.</span></span>

## <a name="motivation"></a><span data-ttu-id="7b74f-105">Motivazione</span><span class="sxs-lookup"><span data-stu-id="7b74f-105">Motivation</span></span>

<span data-ttu-id="7b74f-106">Le motivazioni e le informazioni di base per questa funzionalità sono descritte nel seguente numero (come è una potenziale implementazione della funzionalità):</span><span class="sxs-lookup"><span data-stu-id="7b74f-106">The motivations and background for this feature are described in the following issue (as is a potential implementation of the feature):</span></span>

https://github.com/dotnet/csharplang/issues/191

<span data-ttu-id="7b74f-107">Si tratta di una proposta di progettazione alternativa alle [intrinseche del compilatore](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)</span><span class="sxs-lookup"><span data-stu-id="7b74f-107">This is an alternate design proposal to [compiler intrinsics](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)</span></span>

## <a name="detailed-design"></a><span data-ttu-id="7b74f-108">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="7b74f-108">Detailed Design</span></span>

### <a name="function-pointers"></a><span data-ttu-id="7b74f-109">Puntatori funzione</span><span class="sxs-lookup"><span data-stu-id="7b74f-109">Function pointers</span></span>

<span data-ttu-id="7b74f-110">Il linguaggio consentirà la dichiarazione `delegate*` di puntatori a funzione utilizzando la sintassi.</span><span class="sxs-lookup"><span data-stu-id="7b74f-110">The language will allow for the declaration of function pointers using the `delegate*` syntax.</span></span> <span data-ttu-id="7b74f-111">La sintassi completa è descritta in dettaglio nella sezione `Func` successiva, ma è destinata ad assomigliare alla sintassi utilizzata dalle dichiarazioni e `Action` type.</span><span class="sxs-lookup"><span data-stu-id="7b74f-111">The full syntax is described in detail in the next section but it is meant to resemble the syntax used by `Func` and `Action` type declarations.</span></span>

``` csharp
unsafe class Example {
    void Example(Action<int> a, delegate*<int, void> f) {
        a(42);
        f(42);
    }
}
```

<span data-ttu-id="7b74f-112">Questi tipi vengono rappresentati utilizzando il tipo di puntatore a funzione come descritto in ECMA-335.</span><span class="sxs-lookup"><span data-stu-id="7b74f-112">These types are represented using the function pointer type as outlined in ECMA-335.</span></span> <span data-ttu-id="7b74f-113">Ciò significa che `delegate*` la `calli` chiamata di `delegate` un `callvirt` verrà `Invoke` utilizzato dove la chiamata di un verrà utilizzato sul metodo.</span><span class="sxs-lookup"><span data-stu-id="7b74f-113">This means invocation of a `delegate*` will use `calli` where invocation of a `delegate` will use `callvirt` on the `Invoke` method.</span></span>
<span data-ttu-id="7b74f-114">Sintatticamente, anche se la chiamata è identica per entrambi i costrutti.</span><span class="sxs-lookup"><span data-stu-id="7b74f-114">Syntactically though invocation is identical for both constructs.</span></span>

<span data-ttu-id="7b74f-115">La definizione ECMA-335 dei puntatori al metodo include la convenzione di chiamata come parte della firma del tipo (sezione 7.1).</span><span class="sxs-lookup"><span data-stu-id="7b74f-115">The ECMA-335 definition of method pointers includes the calling convention as part of the type signature (section 7.1).</span></span>
<span data-ttu-id="7b74f-116">La convenzione di `managed`chiamata predefinita sarà .</span><span class="sxs-lookup"><span data-stu-id="7b74f-116">The default calling convention will be `managed`.</span></span> <span data-ttu-id="7b74f-117">È possibile specificare forme alternative aggiungendo `delegate*` il `managed` `cdecl`modificatore `thiscall`appropriato `unmanaged`dopo la sintassi: , , `stdcall`, o .</span><span class="sxs-lookup"><span data-stu-id="7b74f-117">Alternate forms can be specified by adding the appropriate modifier after the `delegate*` syntax: `managed`, `cdecl`, `stdcall`, `thiscall`, or `unmanaged`.</span></span> <span data-ttu-id="7b74f-118">Esempio:</span><span class="sxs-lookup"><span data-stu-id="7b74f-118">Example:</span></span>

``` csharp
// This method will be invoked using the cdecl calling convention
delegate* cdecl<int, int>;

// This method will be invoked using the stdcall calling convention
delegate* stdcall<int, int>;
```

<span data-ttu-id="7b74f-119">Le conversioni tra `delegate*` tipi vengono eseguite in base alla firma, inclusa la convenzione di chiamata.</span><span class="sxs-lookup"><span data-stu-id="7b74f-119">Conversions between `delegate*` types is done based on their signature including the calling convention.</span></span>

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

<span data-ttu-id="7b74f-120">Un `delegate*` tipo è un tipo di puntatore che significa che ha tutte le funzionalità e le restrizioni di un tipo di puntatore standard:A type is a pointer type which means it has all of the capabilities and restrictions of a standard pointer type:</span><span class="sxs-lookup"><span data-stu-id="7b74f-120">A `delegate*` type is a pointer type which means it has all of the capabilities and restrictions of a standard pointer type:</span></span>

- <span data-ttu-id="7b74f-121">Valido solo `unsafe` in un contesto.</span><span class="sxs-lookup"><span data-stu-id="7b74f-121">Only valid in an `unsafe` context.</span></span>
- <span data-ttu-id="7b74f-122">I metodi `delegate*` che contengono un parametro `unsafe` o un tipo restituito possono essere chiamati solo da un contesto.</span><span class="sxs-lookup"><span data-stu-id="7b74f-122">Methods which contain a `delegate*` parameter or return type can only be called from an `unsafe` context.</span></span>
- <span data-ttu-id="7b74f-123">Non può `object`essere convertito in .</span><span class="sxs-lookup"><span data-stu-id="7b74f-123">Cannot be converted to `object`.</span></span>
- <span data-ttu-id="7b74f-124">Non può essere utilizzato come argomento generico.</span><span class="sxs-lookup"><span data-stu-id="7b74f-124">Cannot be used as a generic argument.</span></span>
- <span data-ttu-id="7b74f-125">Può convertire `delegate*` in `void*`modo implicito in .</span><span class="sxs-lookup"><span data-stu-id="7b74f-125">Can implicitly convert `delegate*` to `void*`.</span></span>
- <span data-ttu-id="7b74f-126">Può convertire `void*` in `delegate*`modo esplicito da a .</span><span class="sxs-lookup"><span data-stu-id="7b74f-126">Can explicitly convert from `void*` to `delegate*`.</span></span>

<span data-ttu-id="7b74f-127">Restrizioni:</span><span class="sxs-lookup"><span data-stu-id="7b74f-127">Restrictions:</span></span>

- <span data-ttu-id="7b74f-128">Gli attributi personalizzati non `delegate*` possono essere applicati a uno o a i relativi elementi.</span><span class="sxs-lookup"><span data-stu-id="7b74f-128">Custom attributes cannot be applied to a `delegate*` or any of its elements.</span></span>
- <span data-ttu-id="7b74f-129">Un `delegate*` parametro non può essere contrassegnato come`params`</span><span class="sxs-lookup"><span data-stu-id="7b74f-129">A `delegate*` parameter cannot be marked as `params`</span></span>
- <span data-ttu-id="7b74f-130">Un `delegate*` tipo ha tutte le restrizioni di un tipo di puntatore normale.</span><span class="sxs-lookup"><span data-stu-id="7b74f-130">A `delegate*` type has all of the restrictions of a normal pointer type.</span></span>

### <a name="function-pointer-syntax"></a><span data-ttu-id="7b74f-131">Sintassi del puntatore a funzione</span><span class="sxs-lookup"><span data-stu-id="7b74f-131">Function pointer syntax</span></span>

<span data-ttu-id="7b74f-132">La sintassi completa del puntatore a funzione è rappresentata dalla grammatica seguente:</span><span class="sxs-lookup"><span data-stu-id="7b74f-132">The full function pointer syntax is represented by the following grammar:</span></span>

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

<span data-ttu-id="7b74f-133">La `unmanaged` convenzione di chiamata rappresenta la convenzione di chiamata predefinita per il codice nativo nella piattaforma corrente e viene codificata come winapi.</span><span class="sxs-lookup"><span data-stu-id="7b74f-133">The `unmanaged` calling convention represents the default calling convention for native code on the current platform, and is encoded as winapi.</span></span>
<span data-ttu-id="7b74f-134">Tutte `calling_convention`le s sono parole chiave `delegate*`contestuali se precedute da un oggetto .</span><span class="sxs-lookup"><span data-stu-id="7b74f-134">All `calling_convention`s are contextual keywords when preceded by a `delegate*`.</span></span>

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

### <a name="function-pointer-conversions"></a><span data-ttu-id="7b74f-135">Conversioni di puntatori a funzione</span><span class="sxs-lookup"><span data-stu-id="7b74f-135">Function pointer conversions</span></span>

<span data-ttu-id="7b74f-136">In un contesto unsafe, il set di conversioni implicite disponibili (conversioni implicite) viene esteso per includere le seguenti conversioni implicite del puntatore:</span><span class="sxs-lookup"><span data-stu-id="7b74f-136">In an unsafe context, the set of available implicit conversions (Implicit conversions) is extended to include the following implicit pointer conversions:</span></span>
- [<span data-ttu-id="7b74f-137">_Conversioni esistenti_</span><span class="sxs-lookup"><span data-stu-id="7b74f-137">_Existing conversions_</span></span>](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#pointer-conversions)
- <span data-ttu-id="7b74f-138">Dal `F0` tipo _funcptr\__ a un altro tipo `F1` _funcptr\__ , purché tutte le seguenti condizioni siano vere:</span><span class="sxs-lookup"><span data-stu-id="7b74f-138">From _funcptr\_type_ `F0` to another _funcptr\_type_ `F1`, provided all of the following are true:</span></span>
    - <span data-ttu-id="7b74f-139">`F0`e `F1` hanno lo stesso numero di `D0n` `F0` parametri e `ref` `out`ogni `in` parametro in ha `D1n` `F1`gli stessi modificatori , , o del parametro corrispondente in .</span><span class="sxs-lookup"><span data-stu-id="7b74f-139">`F0` and `F1` have the same number of parameters, and each parameter `D0n` in `F0` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter `D1n` in `F1`.</span></span>
    - <span data-ttu-id="7b74f-140">Per ogni parametro di `ref`valore `out`(parametro senza modificatore , o o `in` ), esiste una `F0` conversione di identità, una conversione implicita dei riferimenti o una conversione implicita del puntatore dal tipo di parametro in al tipo di parametro corrispondente in `F1`.</span><span class="sxs-lookup"><span data-stu-id="7b74f-140">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `F0` to the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="7b74f-141">Per `ref`ogni `out`parametro , o `in` `F0` , il tipo di parametro in è lo stesso del tipo di parametro corrispondente in `F1`.</span><span class="sxs-lookup"><span data-stu-id="7b74f-141">For each `ref`, `out`, or `in` parameter, the parameter type in `F0` is the same as the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="7b74f-142">Se il tipo restituito è `ref` `ref readonly`in base al valore (no o ), esiste `F1` un'identità, `F0`un riferimento implicito o una conversione implicita del puntatore dal tipo restituito di al tipo restituito di .</span><span class="sxs-lookup"><span data-stu-id="7b74f-142">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F1` to the return type of `F0`.</span></span>
    - <span data-ttu-id="7b74f-143">Se il tipo restituito`ref` è `ref readonly`per riferimento `ref` ( o `F1` ), il tipo restituito `ref` e `F0`i modificatori di sono uguali al tipo restituito e ai modificatori di .</span><span class="sxs-lookup"><span data-stu-id="7b74f-143">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F1` are the same as the return type and `ref` modifiers of `F0`.</span></span>
    - <span data-ttu-id="7b74f-144">La convenzione `F0` di chiamata di è `F1`uguale alla convenzione di chiamata di .</span><span class="sxs-lookup"><span data-stu-id="7b74f-144">The calling convention of `F0` is the same as the calling convention of `F1`.</span></span>

### <a name="allow-address-of-to-target-methods"></a><span data-ttu-id="7b74f-145">Consentire i metodi address-of a target</span><span class="sxs-lookup"><span data-stu-id="7b74f-145">Allow address-of to target methods</span></span>

<span data-ttu-id="7b74f-146">I gruppi di metodi saranno ora consentiti come argomenti a un'espressione address-of.</span><span class="sxs-lookup"><span data-stu-id="7b74f-146">Method groups will now be allowed as arguments to an address-of expression.</span></span> <span data-ttu-id="7b74f-147">Il tipo di tale espressione `delegate*` sarà un che ha la firma equivalente del metodo di destinazione e una convenzione di chiamata gestita:The type of such an expression will be a which has the equivalent signature of the target method and a managed calling convention:</span><span class="sxs-lookup"><span data-stu-id="7b74f-147">The type of such an expression will be a `delegate*` which has the equivalent signature of the target method and a managed calling convention:</span></span>

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

<span data-ttu-id="7b74f-148">In un contesto unsafe, un metodo `M` è `F` compatibile con un tipo di puntatore a funzione se tutte le seguenti sono vere:</span><span class="sxs-lookup"><span data-stu-id="7b74f-148">In an unsafe context, a method `M` is compatible with a function pointer type `F` if all of the following are true:</span></span>
- <span data-ttu-id="7b74f-149">`M`e `F` hanno lo stesso numero di `D` parametri e `ref` `out`ogni `in` parametro in ha `F`gli stessi modificatori , , o del parametro corrispondente in .</span><span class="sxs-lookup"><span data-stu-id="7b74f-149">`M` and `F` have the same number of parameters, and each parameter in `D` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter in `F`.</span></span>
- <span data-ttu-id="7b74f-150">Per ogni parametro di `ref`valore `out`(parametro senza modificatore , o o `in` ), esiste una `M` conversione di identità, una conversione implicita dei riferimenti o una conversione implicita del puntatore dal tipo di parametro in al tipo di parametro corrispondente in `F`.</span><span class="sxs-lookup"><span data-stu-id="7b74f-150">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `M` to the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="7b74f-151">Per `ref`ogni `out`parametro , o `in` `M` , il tipo di parametro in è lo stesso del tipo di parametro corrispondente in `F`.</span><span class="sxs-lookup"><span data-stu-id="7b74f-151">For each `ref`, `out`, or `in` parameter, the parameter type in `M` is the same as the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="7b74f-152">Se il tipo restituito è `ref` `ref readonly`in base al valore (no o ), esiste `F` un'identità, `M`un riferimento implicito o una conversione implicita del puntatore dal tipo restituito di al tipo restituito di .</span><span class="sxs-lookup"><span data-stu-id="7b74f-152">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F` to the return type of `M`.</span></span>
- <span data-ttu-id="7b74f-153">Se il tipo restituito`ref` è `ref readonly`per riferimento `ref` ( o `F` ), il tipo restituito `ref` e `M`i modificatori di sono uguali al tipo restituito e ai modificatori di .</span><span class="sxs-lookup"><span data-stu-id="7b74f-153">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F` are the same as the return type and `ref` modifiers of `M`.</span></span>
- <span data-ttu-id="7b74f-154">La convenzione `M` di chiamata di è `F`uguale alla convenzione di chiamata di .</span><span class="sxs-lookup"><span data-stu-id="7b74f-154">The calling convention of `M` is the same as the calling convention of `F`.</span></span>
- <span data-ttu-id="7b74f-155">`M` è un metodo statico.</span><span class="sxs-lookup"><span data-stu-id="7b74f-155">`M` is a static method.</span></span>

<span data-ttu-id="7b74f-156">In un contesto unsafe esiste una conversione implicita da `E` un'espressione address-of `F` `E` la cui destinazione è un gruppo di metodi a un tipo di puntatore `F`a funzione compatibile se contiene almeno un metodo applicabile nella sua forma normale a un elenco di argomenti costruito utilizzando i tipi di parametro e i modificatori di , come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="7b74f-156">In an unsafe context, an implicit conversion exists from an address-of expression whose target is a method group `E` to a compatible function pointer type `F` if `E` contains at least one method that is applicable in its normal form to an argument list constructed by use of the parameter types and modifiers of `F`, as described in the following.</span></span>
- <span data-ttu-id="7b74f-157">Viene selezionato `M` un singolo metodo corrispondente a `E(A)` una chiamata al metodo del form con le seguenti modifiche:</span><span class="sxs-lookup"><span data-stu-id="7b74f-157">A single method `M` is selected corresponding to a method invocation of the form `E(A)` with the following modifications:</span></span>
   - <span data-ttu-id="7b74f-158">L'elenco `A` di argomenti è un elenco di espressioni, ognuna`ref` `out`classificata `in`come variabile e con il tipo e il modificatore ( , , o ) dell'elenco di _\_\_parametri formali_ corrispondente di `D`.</span><span class="sxs-lookup"><span data-stu-id="7b74f-158">The arguments list `A` is a list of expressions, each classified as a variable and with the type and modifier (`ref`, `out`, or `in`) of the corresponding _formal\_parameter\_list_ of `D`.</span></span>
   - <span data-ttu-id="7b74f-159">I metodi candidati sono solo i metodi applicabili nella loro forma normale, non quelli applicabili nella loro forma espansa.</span><span class="sxs-lookup"><span data-stu-id="7b74f-159">The candidate methods are only those methods that are applicable in their normal form, not those applicable in their expanded form.</span></span>
   - <span data-ttu-id="7b74f-160">I metodi candidati sono solo quei metodi che sono statici.</span><span class="sxs-lookup"><span data-stu-id="7b74f-160">The candidate methods are only those methods that are static.</span></span>
- <span data-ttu-id="7b74f-161">Se l'algoritmo delle chiamate al metodo genera un errore, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="7b74f-161">If the algorithm of Method invocations produces an error, then a compile-time error occurs.</span></span> <span data-ttu-id="7b74f-162">In caso contrario, l'algoritmo produce un singolo metodo `M` migliore con lo stesso numero di parametri `F` e la conversione viene considerata esistente.</span><span class="sxs-lookup"><span data-stu-id="7b74f-162">Otherwise, the algorithm produces a single best method `M` having the same number of parameters as `F` and the conversion is considered to exist.</span></span>
- <span data-ttu-id="7b74f-163">Il metodo `M` selezionato deve essere compatibile (come definito `F`in precedenza) con il tipo di puntatore a funzione .</span><span class="sxs-lookup"><span data-stu-id="7b74f-163">The selected method `M` must be compatible (as defined above) with the function pointer type `F`.</span></span> <span data-ttu-id="7b74f-164">In caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="7b74f-164">Otherwise, a compile-time error occurs.</span></span>
- <span data-ttu-id="7b74f-165">Il risultato della conversione è `F`un puntatore a funzione di tipo .</span><span class="sxs-lookup"><span data-stu-id="7b74f-165">The result of the conversion is a function pointer of type `F`.</span></span>

<span data-ttu-id="7b74f-166">Esiste una conversione implicita da un'espressione `E` address-of la cui destinazione è un gruppo di `void*` metodi se è presente un solo metodo `M` statico in `E`.</span><span class="sxs-lookup"><span data-stu-id="7b74f-166">An implicit conversion exists from an address-of expression whose target is a method group `E` to `void*` if there is only one static method `M` in `E`.</span></span>
<span data-ttu-id="7b74f-167">Se è presente un metodo statico, `E` il `M`singolo metodo migliore da è .</span><span class="sxs-lookup"><span data-stu-id="7b74f-167">If there is one static method, then the single best method from `E` is `M`.</span></span>
<span data-ttu-id="7b74f-168">In caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="7b74f-168">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="7b74f-169">Ciò significa che gli sviluppatori possono dipendere dalle regole di risoluzione dell'overload per lavorare insieme all'operatore address-of:This means developers can depend on overload resolution rules to work in conjunction with the address-of operator:</span><span class="sxs-lookup"><span data-stu-id="7b74f-169">This means developers can depend on overload resolution rules to work in conjunction with the address-of operator:</span></span>

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

<span data-ttu-id="7b74f-170">L'operatore address-of verrà `ldftn` implementato mediante l'istruzione .</span><span class="sxs-lookup"><span data-stu-id="7b74f-170">The address-of operator will be implemented using the `ldftn` instruction.</span></span>

<span data-ttu-id="7b74f-171">Restrizioni di questa funzione:</span><span class="sxs-lookup"><span data-stu-id="7b74f-171">Restrictions of this feature:</span></span>

- <span data-ttu-id="7b74f-172">Si applica solo `static`ai metodi contrassegnati come .</span><span class="sxs-lookup"><span data-stu-id="7b74f-172">Only applies to methods marked as `static`.</span></span>
- <span data-ttu-id="7b74f-173">Le`static` funzioni non locali `&`non possono essere utilizzate in .</span><span class="sxs-lookup"><span data-stu-id="7b74f-173">Non-`static` local functions cannot be used in `&`.</span></span> <span data-ttu-id="7b74f-174">I dettagli di implementazione di questi metodi non sono deliberatamente specificati dal linguaggio.</span><span class="sxs-lookup"><span data-stu-id="7b74f-174">The implementation details of these methods are deliberately not specified by the language.</span></span> <span data-ttu-id="7b74f-175">Ciò include se sono statici rispetto all'istanza o esattamente la firma con cui vengono generate.</span><span class="sxs-lookup"><span data-stu-id="7b74f-175">This includes whether they are static vs. instance or exactly what signature they are emitted with.</span></span>


### <a name="operators-on-function-pointer-types"></a><span data-ttu-id="7b74f-176">Operatori sui tipi di puntatore a funzioneOperators on Function Pointer Types</span><span class="sxs-lookup"><span data-stu-id="7b74f-176">Operators on Function Pointer Types</span></span>

<span data-ttu-id="7b74f-177">La sezione nel codice unsafe sugli operatori viene modificata come tale:The section in unsafe code on operators is modified as such:</span><span class="sxs-lookup"><span data-stu-id="7b74f-177">The section in unsafe code on operators is modified as such:</span></span>

> <span data-ttu-id="7b74f-178">In un contesto unsafe, sono disponibili diversi\_costrutti per\_l'utilizzo di tutti i type_s _pointer che non _funcptr type_s:</span><span class="sxs-lookup"><span data-stu-id="7b74f-178">In an unsafe context, several constructs are available for operating on all _pointer\_type_s that are not _funcptr\_type_s:</span></span>
>
> *  <span data-ttu-id="7b74f-179">L'operatore `*` può essere utilizzato per eseguire il riferimento indiretto del puntatore ([riferimento indiretto del puntatore](unsafe-code.md#pointer-indirection)).</span><span class="sxs-lookup"><span data-stu-id="7b74f-179">The `*` operator may be used to perform pointer indirection ([Pointer indirection](unsafe-code.md#pointer-indirection)).</span></span>
> *  <span data-ttu-id="7b74f-180">L'operatore `->` può essere utilizzato per accedere a un membro di una struttura tramite un puntatore ([Accesso ai membri del puntatore](unsafe-code.md#pointer-member-access)).</span><span class="sxs-lookup"><span data-stu-id="7b74f-180">The `->` operator may be used to access a member of a struct through a pointer ([Pointer member access](unsafe-code.md#pointer-member-access)).</span></span>
> *  <span data-ttu-id="7b74f-181">L'operatore `[]` può essere utilizzato per indicizzare un puntatore ([Accesso all'elemento Puntatore](unsafe-code.md#pointer-element-access)).</span><span class="sxs-lookup"><span data-stu-id="7b74f-181">The `[]` operator may be used to index a pointer ([Pointer element access](unsafe-code.md#pointer-element-access)).</span></span>
> *  <span data-ttu-id="7b74f-182">L'operatore `&` può essere utilizzato per ottenere l'indirizzo di una variabile ([L'operatore address-of](unsafe-code.md#the-address-of-operator)).</span><span class="sxs-lookup"><span data-stu-id="7b74f-182">The `&` operator may be used to obtain the address of a variable ([The address-of operator](unsafe-code.md#the-address-of-operator)).</span></span>
> *  <span data-ttu-id="7b74f-183">Gli `++` `--` operatori e possono essere utilizzati per incrementare e diminuire i puntatori ([Incremento e decremento del puntatore](unsafe-code.md#pointer-increment-and-decrement)).</span><span class="sxs-lookup"><span data-stu-id="7b74f-183">The `++` and `--` operators may be used to increment and decrement pointers ([Pointer increment and decrement](unsafe-code.md#pointer-increment-and-decrement)).</span></span>
> *  <span data-ttu-id="7b74f-184">Gli `+` `-` operatori e possono essere utilizzati per eseguire l'aritmetica dei puntatori ([Pointer arithmetic](unsafe-code.md#pointer-arithmetic)).</span><span class="sxs-lookup"><span data-stu-id="7b74f-184">The `+` and `-` operators may be used to perform pointer arithmetic ([Pointer arithmetic](unsafe-code.md#pointer-arithmetic)).</span></span>
> *  <span data-ttu-id="7b74f-185">Gli `==` `!=`operatori `<` `>`, `<=`, `=>` , , e possono essere utilizzati per confrontare i puntatori ([Confronto di puntatori](unsafe-code.md#pointer-comparison)).</span><span class="sxs-lookup"><span data-stu-id="7b74f-185">The `==`, `!=`, `<`, `>`, `<=`, and `=>` operators may be used to compare pointers ([Pointer comparison](unsafe-code.md#pointer-comparison)).</span></span>
> *  <span data-ttu-id="7b74f-186">L'operatore `stackalloc` può essere utilizzato per allocare memoria dallo stack di chiamate ( buffer a[dimensione fissa](unsafe-code.md#fixed-size-buffers)).</span><span class="sxs-lookup"><span data-stu-id="7b74f-186">The `stackalloc` operator may be used to allocate memory from the call stack ([Fixed size buffers](unsafe-code.md#fixed-size-buffers)).</span></span>
> *  <span data-ttu-id="7b74f-187">L'istruzione `fixed` può essere utilizzata per correggere temporaneamente una variabile in modo che sia possibile ottenere il relativo indirizzo ([L'istruzione fixed](unsafe-code.md#the-fixed-statement)).</span><span class="sxs-lookup"><span data-stu-id="7b74f-187">The `fixed` statement may be used to temporarily fix a variable so its address can be obtained ([The fixed statement](unsafe-code.md#the-fixed-statement)).</span></span>
> 
> <span data-ttu-id="7b74f-188">In un contesto unsafe, sono disponibili diversi\_costrutti per l'utilizzo su tutti i _funcptr type_s:</span><span class="sxs-lookup"><span data-stu-id="7b74f-188">In an unsafe context, several constructs are available for operating on all _funcptr\_type_s:</span></span>
> *  <span data-ttu-id="7b74f-189">L'operatore `&` può essere utilizzato per ottenere l'indirizzo dei metodi statici ([Consenti address-of a target](function-pointers.md#allow-address-of-to-target-methods)methods )</span><span class="sxs-lookup"><span data-stu-id="7b74f-189">The `&` operator may be used to obtain the address of static methods ([Allow address-of to target methods](function-pointers.md#allow-address-of-to-target-methods))</span></span>
> *  <span data-ttu-id="7b74f-190">Gli `==` `!=`operatori `<` `>`, `<=`, `=>` , , e possono essere utilizzati per confrontare i puntatori ([Confronto di puntatori](unsafe-code.md#pointer-comparison)).</span><span class="sxs-lookup"><span data-stu-id="7b74f-190">The `==`, `!=`, `<`, `>`, `<=`, and `=>` operators may be used to compare pointers ([Pointer comparison](unsafe-code.md#pointer-comparison)).</span></span>

<span data-ttu-id="7b74f-191">Inoltre, vengono modificate tutte `Pointers in expressions` le sezioni in per `Pointer comparison` proibire i tipi di puntatori a funzione, ad eccezione di e `The sizeof operator`.</span><span class="sxs-lookup"><span data-stu-id="7b74f-191">Additionally, we modify all the sections in `Pointers in expressions` to forbid function pointer types, except `Pointer comparison` and `The sizeof operator`.</span></span>

### <a name="better-function-member"></a><span data-ttu-id="7b74f-192">Membro funzione migliore</span><span class="sxs-lookup"><span data-stu-id="7b74f-192">Better function member</span></span>

<span data-ttu-id="7b74f-193">La specifica del membro funzione migliore verrà modificata per includere la riga seguente:The better function member specification will be changed to include the following line:</span><span class="sxs-lookup"><span data-stu-id="7b74f-193">The better function member specification will be changed to include the following line:</span></span>

> <span data-ttu-id="7b74f-194">A `delegate*` è più specifico di`void*`</span><span class="sxs-lookup"><span data-stu-id="7b74f-194">A `delegate*` is more specific than `void*`</span></span>

<span data-ttu-id="7b74f-195">Ciò significa che è `void*` possibile `delegate*` sovraccaricare e un e ancora in modo ragionevole utilizzare l'operatore address-of.</span><span class="sxs-lookup"><span data-stu-id="7b74f-195">This means that it is possible to overload on `void*` and a `delegate*` and still sensibly use the address-of operator.</span></span>

## <a name="open-issues"></a><span data-ttu-id="7b74f-196">Open Issues</span><span class="sxs-lookup"><span data-stu-id="7b74f-196">Open Issues</span></span>

### <a name="nativecallableattribute"></a><span data-ttu-id="7b74f-197">NativeCallableAttribute</span><span class="sxs-lookup"><span data-stu-id="7b74f-197">NativeCallableAttribute</span></span>

<span data-ttu-id="7b74f-198">Si tratta di un attributo utilizzato da CLR per evitare il prologo gestito a nativo durante la chiamata.</span><span class="sxs-lookup"><span data-stu-id="7b74f-198">This is an attribute used by the CLR to avoid the managed to native prologue when invoking.</span></span> <span data-ttu-id="7b74f-199">I metodi contrassegnati da questo attributo sono chiamabili solo dal codice nativo, non gestiti (non è possibile chiamare metodi, creare un delegato e così via).</span><span class="sxs-lookup"><span data-stu-id="7b74f-199">Methods marked by this attribute are only callable from native code, not managed (can’t call methods, create a delegate, etc …).</span></span> <span data-ttu-id="7b74f-200">L'attributo non è speciale per mscorlib; il runtime tratterà qualsiasi attributo con questo nome con la stessa semantica.</span><span class="sxs-lookup"><span data-stu-id="7b74f-200">The attribute is not special to mscorlib; the runtime will treat any attribute with this name with the same semantics.</span></span>

<span data-ttu-id="7b74f-201">È possibile che il runtime e il linguaggio collaborino per supportare completamente questa operazione.</span><span class="sxs-lookup"><span data-stu-id="7b74f-201">It's possible for the runtime and language to work together to fully support this.</span></span> <span data-ttu-id="7b74f-202">La lingua può scegliere di `static` trattare `NativeCallable` i `delegate*` membri address-of con un attributo come un con la convenzione di chiamata specificata.</span><span class="sxs-lookup"><span data-stu-id="7b74f-202">The language could choose to treat address-of `static` members with a `NativeCallable` attribute as a `delegate*` with the specified calling convention.</span></span>

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

<span data-ttu-id="7b74f-203">Inoltre la lingua sarebbe probabilmente anche voglia di:</span><span class="sxs-lookup"><span data-stu-id="7b74f-203">Additionally the language would likely also want to:</span></span>

- <span data-ttu-id="7b74f-204">Contrassegnare tutte le chiamate gestite a un metodo contrassegnato con `NativeCallable` come errore.</span><span class="sxs-lookup"><span data-stu-id="7b74f-204">Flag any managed calls to a method tagged with `NativeCallable` as an error.</span></span> <span data-ttu-id="7b74f-205">Dato che la funzione non può essere richiamata dal codice gestito, il compilatore deve impedire agli sviluppatori di tentare una chiamata di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="7b74f-205">Given the function can't be invoked from managed code the compiler should prevent developers from attempting such an invocation.</span></span>
- <span data-ttu-id="7b74f-206">Impedire le conversioni `delegate` del gruppo di `NativeCallable`metodi quando il metodo è contrassegnato con .</span><span class="sxs-lookup"><span data-stu-id="7b74f-206">Prevent method group conversions to `delegate` when the method is tagged with `NativeCallable`.</span></span>

<span data-ttu-id="7b74f-207">Questo non è `NativeCallable` necessario sostenere però.</span><span class="sxs-lookup"><span data-stu-id="7b74f-207">This is not necessary to support `NativeCallable` though.</span></span> <span data-ttu-id="7b74f-208">Il compilatore `NativeCallable` può supportare l'attributo come sta utilizzando la sintassi esistente.</span><span class="sxs-lookup"><span data-stu-id="7b74f-208">The compiler can support the `NativeCallable` attribute as is using the existing syntax.</span></span> <span data-ttu-id="7b74f-209">Il programma avrebbe semplicemente `void*` bisogno di eseguire `delegate*` il cast a prima del cast alla firma corretta.</span><span class="sxs-lookup"><span data-stu-id="7b74f-209">The program would simply need to cast to `void*` before casting to the correct `delegate*` signature.</span></span> <span data-ttu-id="7b74f-210">Non sarebbe peggio del sostegno odierno.</span><span class="sxs-lookup"><span data-stu-id="7b74f-210">That would be no worse than the support today.</span></span>

``` csharp
void* v = &CloseHandle;
delegate* cdecl<IntPtr, bool> f1 = (delegate* cdecl<IntPtr, bool>)v;
```

### <a name="extensible-set-of-unmanaged-calling-conventions"></a><span data-ttu-id="7b74f-211">Set estendibile di convenzioni di chiamata non gestiteExtensible set of unmanaged calling conventions</span><span class="sxs-lookup"><span data-stu-id="7b74f-211">Extensible set of unmanaged calling conventions</span></span>

<span data-ttu-id="7b74f-212">Il set di convenzioni di chiamata non gestite supportate dalle codifiche ECMA-335 correnti è obsoleto.</span><span class="sxs-lookup"><span data-stu-id="7b74f-212">The set of unmanaged calling conventions supported by the current ECMA-335 encodings is outdated.</span></span> <span data-ttu-id="7b74f-213">Sono state riscontrate richieste di aggiunta del supporto per più convenzioni di chiamata non gestite, ad esempio:We have seen requests to add support for more unmanaged calling conventions, for example:</span><span class="sxs-lookup"><span data-stu-id="7b74f-213">We have seen requests to add support for more unmanaged calling conventions, for example:</span></span>

- <span data-ttu-id="7b74f-214">[vectorcall (chiamata vettoriale)](https://docs.microsoft.com/cpp/cpp/vectorcall)https://github.com/dotnet/coreclr/issues/12120</span><span class="sxs-lookup"><span data-stu-id="7b74f-214">[vectorcall](https://docs.microsoft.com/cpp/cpp/vectorcall) https://github.com/dotnet/coreclr/issues/12120</span></span>
- <span data-ttu-id="7b74f-215">StdCall con esplicito questohttps://github.com/dotnet/coreclr/pull/23974#issuecomment-482991750</span><span class="sxs-lookup"><span data-stu-id="7b74f-215">StdCall with explicit this https://github.com/dotnet/coreclr/pull/23974#issuecomment-482991750</span></span>

<span data-ttu-id="7b74f-216">La progettazione di questa funzionalità deve consentire l'estensione del set di convenzioni di chiamata non gestite in base alle esigenze future.</span><span class="sxs-lookup"><span data-stu-id="7b74f-216">The design of this feature should allow extending the set of unmanaged calling conventions as needed in future.</span></span> <span data-ttu-id="7b74f-217">I problemi includono spazio limitato per la codifica delle convenzioni `IMAGE_CEE_CS_CALLCONV_MASK`di chiamata (12 su 16 valori vengono presi in ) e il numero di posizioni che devono essere toccate per aggiungere una nuova convenzione di chiamata.</span><span class="sxs-lookup"><span data-stu-id="7b74f-217">The problems include limited space for encoding calling conventions (12 out of 16 values are taken in `IMAGE_CEE_CS_CALLCONV_MASK`) and number of places that need to be touched in order to add a new calling convention.</span></span> <span data-ttu-id="7b74f-218">Una potenziale soluzione consiste nell'introdurre una [`System.Runtime.InteropServices.CallingConvention`](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.callingconvention) nuova codifica che rappresenta la convenzione di chiamata utilizzando enum.</span><span class="sxs-lookup"><span data-stu-id="7b74f-218">A potential solution is to introduce a new encoding that represents the calling convention using [`System.Runtime.InteropServices.CallingConvention`](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.callingconvention) enum.</span></span>

<span data-ttu-id="7b74f-219">Per riferimento, https://github.com/llvm/llvm-project/blob/master/llvm/include/llvm/IR/CallingConv.h include l'elenco delle convenzioni di chiamata supportate da LLVM.</span><span class="sxs-lookup"><span data-stu-id="7b74f-219">For reference, https://github.com/llvm/llvm-project/blob/master/llvm/include/llvm/IR/CallingConv.h has the list of calling conventions supported by LLVM.</span></span> <span data-ttu-id="7b74f-220">Sebbene sia improbabile che .NET debba mai supportarli tutti, dimostra che lo spazio delle convenzioni di chiamata è molto ricco.</span><span class="sxs-lookup"><span data-stu-id="7b74f-220">While it is unlikely that .NET will ever need to support all of them, it demonstrates that the space of calling conventions is very rich.</span></span>

## <a name="considerations"></a><span data-ttu-id="7b74f-221">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="7b74f-221">Considerations</span></span>

### <a name="allow-instance-methods"></a><span data-ttu-id="7b74f-222">Consentire metodi di istanza</span><span class="sxs-lookup"><span data-stu-id="7b74f-222">Allow instance methods</span></span>

<span data-ttu-id="7b74f-223">La proposta potrebbe essere estesa per supportare `EXPLICITTHIS` i metodi `instance` di istanza sfruttando la convenzione di chiamata CLI (denominato nel codice C .</span><span class="sxs-lookup"><span data-stu-id="7b74f-223">The proposal could be extended to support instance methods by taking advantage of the `EXPLICITTHIS` CLI calling convention (named `instance` in C# code).</span></span> <span data-ttu-id="7b74f-224">Questa forma di puntatori `this` a funzione CLI inserisce il parametro come primo parametro esplicito della sintassi del puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="7b74f-224">This form of CLI function pointers puts the `this` parameter as an explicit first parameter of the function pointer syntax.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        delegate* instance<Instance, string> f = &ToString;
        f(this);
    }
}
```

<span data-ttu-id="7b74f-225">Questo è valido, ma aggiunge qualche complicazione alla proposta.</span><span class="sxs-lookup"><span data-stu-id="7b74f-225">This is sound but adds some complication to the proposal.</span></span> <span data-ttu-id="7b74f-226">In particolare perché i puntatori a `instance` `managed` funzione che differivano dalla convenzione di chiamata e sarebbero incompatibili anche se entrambi i casi vengono utilizzati per richiamare i metodi gestiti con la stessa firma di C.</span><span class="sxs-lookup"><span data-stu-id="7b74f-226">Particularly because function pointers which differed by the calling convention `instance` and `managed` would be incompatible even though both cases are used to invoke managed methods with the same C# signature.</span></span> <span data-ttu-id="7b74f-227">Anche in ogni caso considerato dove questo sarebbe utile avere c'era un semplice lavoro intorno: utilizzare una `static` funzione locale.</span><span class="sxs-lookup"><span data-stu-id="7b74f-227">Also in every case considered where this would be valuable to have there was a simple work around: use a `static` local function.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        static string toString(Instance i) = i.ToString();
        delgate*<Instance, string> f = &toString;
        f(this);
    }
}
```

### <a name="dont-require-unsafe-at-declaration"></a><span data-ttu-id="7b74f-228">Non richiedere unsafe alla dichiarazione</span><span class="sxs-lookup"><span data-stu-id="7b74f-228">Don't require unsafe at declaration</span></span>

<span data-ttu-id="7b74f-229">Anziché `unsafe` richiedere a ogni `delegate*`utilizzo di un oggetto , è necessario `delegate*`solo nel punto in cui un gruppo di metodi viene convertito in un oggetto .</span><span class="sxs-lookup"><span data-stu-id="7b74f-229">Instead of requiring `unsafe` at every use of a `delegate*`, only require it at the point where a method group is converted to a `delegate*`.</span></span> <span data-ttu-id="7b74f-230">È qui che entrano in gioco i problemi di sicurezza di base (sapendo che l'assieme che lo contiene non può essere scaricato mentre il valore è attivo).</span><span class="sxs-lookup"><span data-stu-id="7b74f-230">This is where the core safety issues come into play (knowing that the containing assembly cannot be unloaded while the value is alive).</span></span> <span data-ttu-id="7b74f-231">Richiedere `unsafe` in altre posizioni può essere visto come eccessivo.</span><span class="sxs-lookup"><span data-stu-id="7b74f-231">Requiring `unsafe` on the other locations can be seen as excessive.</span></span>

<span data-ttu-id="7b74f-232">Questo è il modo in cui il design è stato originariamente progettato.</span><span class="sxs-lookup"><span data-stu-id="7b74f-232">This is how the design was originally intended.</span></span> <span data-ttu-id="7b74f-233">Ma le regole linguistiche risultanti sembravano molto scomode.</span><span class="sxs-lookup"><span data-stu-id="7b74f-233">But the resulting language rules felt very awkward.</span></span> <span data-ttu-id="7b74f-234">È impossibile nascondere il fatto che si tratta di un valore `unsafe` del puntatore e ha continuato a sbirciare anche senza la parola chiave.</span><span class="sxs-lookup"><span data-stu-id="7b74f-234">It's impossible to hide the fact that this is a pointer value and it kept peeking through even without the `unsafe` keyword.</span></span> <span data-ttu-id="7b74f-235">Ad esempio la `object` conversione in non può essere consentita, `class`non può essere un membro di un, ecc ... La progettazione di `unsafe` C , è necessario per tutti gli utilizzi del puntatore e quindi questa progettazione segue che.</span><span class="sxs-lookup"><span data-stu-id="7b74f-235">For example the conversion to `object` can't be allowed, it can't be a member of a `class`, etc ... The C# design is to require `unsafe` for all pointer uses and hence this design follows that.</span></span>

<span data-ttu-id="7b74f-236">Gli sviluppatori saranno comunque _safe_ in grado `delegate*` di presentare un wrapper sicuro sopra i valori nello stesso modo in cui avvengono per i tipi di puntatore normali oggi.</span><span class="sxs-lookup"><span data-stu-id="7b74f-236">Developers will still be capable of presenting a _safe_ wrapper on top of `delegate*` values the same way that they do for normal pointer types today.</span></span> <span data-ttu-id="7b74f-237">Prendere in considerazione:</span><span class="sxs-lookup"><span data-stu-id="7b74f-237">Consider:</span></span>

``` csharp
unsafe struct Action {
    delegate*<void> _ptr;

    Action(delegate*<void> ptr) => _ptr = ptr;
    public void Invoke() => _ptr();
}
```

### <a name="using-delegates"></a><span data-ttu-id="7b74f-238">Utilizzo dei delegati</span><span class="sxs-lookup"><span data-stu-id="7b74f-238">Using delegates</span></span>

<span data-ttu-id="7b74f-239">Anziché utilizzare un nuovo `delegate*`elemento di `delegate` sintassi, è sufficiente utilizzare i tipi esistenti con il `*` seguente tipo:</span><span class="sxs-lookup"><span data-stu-id="7b74f-239">Instead of using a new syntax element, `delegate*`, simply use existing `delegate` types with a `*` following the type:</span></span>

``` csharp
Func<object, object, bool>* ptr = &object.ReferenceEquals;
```

<span data-ttu-id="7b74f-240">La gestione della convenzione di chiamata `delegate` può essere eseguita `CallingConvention` annotando i tipi con un attributo che specifica un valore.</span><span class="sxs-lookup"><span data-stu-id="7b74f-240">Handling calling convention can be done by annotating the `delegate` types with an attribute that specifies a `CallingConvention` value.</span></span> <span data-ttu-id="7b74f-241">La mancanza di un attributo significherebbe la convenzione di chiamata gestita.</span><span class="sxs-lookup"><span data-stu-id="7b74f-241">The lack of an attribute would signify the managed calling convention.</span></span>

<span data-ttu-id="7b74f-242">Codificare questo in IL è problematico.</span><span class="sxs-lookup"><span data-stu-id="7b74f-242">Encoding this in IL is problematic.</span></span> <span data-ttu-id="7b74f-243">Il valore sottostante deve essere rappresentato come un puntatore ma deve anche:</span><span class="sxs-lookup"><span data-stu-id="7b74f-243">The underlying value needs to be represented as a pointer yet it also must:</span></span>

1. <span data-ttu-id="7b74f-244">Avere un tipo univoco per consentire gli overload con diversi tipi di puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="7b74f-244">Have a unique type to allow for overloads with different function pointer types.</span></span>
1. <span data-ttu-id="7b74f-245">Essere equivalente per scopi OHI oltre i limiti dell'assieme.</span><span class="sxs-lookup"><span data-stu-id="7b74f-245">Be equivalent for OHI purposes across assembly boundaries.</span></span>

<span data-ttu-id="7b74f-246">L'ultimo punto è particolarmente problematico.</span><span class="sxs-lookup"><span data-stu-id="7b74f-246">The last point is particularly problematic.</span></span> <span data-ttu-id="7b74f-247">Ciò significa che `Func<int>*` ogni assembly che utilizza deve `Func<int>*` codificare un tipo equivalente nei metadati anche se è definito in un assembly anche se non controllarlo.</span><span class="sxs-lookup"><span data-stu-id="7b74f-247">This mean that every assembly which uses `Func<int>*` must encode an equivalent type in metadata even though `Func<int>*` is defined in an assembly though don't control.</span></span>
<span data-ttu-id="7b74f-248">Inoltre qualsiasi altro tipo definito con `System.Func<T>` il nome in un assembly che non è mscorlib deve essere diverso da quello definito in mscorlib.</span><span class="sxs-lookup"><span data-stu-id="7b74f-248">Additionally any other type which is defined with the name `System.Func<T>` in an assembly that is not mscorlib must be different than the version defined in mscorlib.</span></span>

<span data-ttu-id="7b74f-249">Un'opzione che è stata esplorata `mod_req(Func<int>) void*`è stata l'emissione di un puntatore come .</span><span class="sxs-lookup"><span data-stu-id="7b74f-249">One option that was explored was emitting such a pointer as `mod_req(Func<int>) void*`.</span></span> <span data-ttu-id="7b74f-250">Questo non funziona anche `mod_req` se come `TypeSpec` un non può associarsi a un e quindi non può indirizzare le istanze generiche.</span><span class="sxs-lookup"><span data-stu-id="7b74f-250">This doesn't work though as a `mod_req` cannot bind to a `TypeSpec` and hence cannot target generic instantiations.</span></span>

### <a name="named-function-pointers"></a><span data-ttu-id="7b74f-251">Puntatori a funzione denominata</span><span class="sxs-lookup"><span data-stu-id="7b74f-251">Named function pointers</span></span>

<span data-ttu-id="7b74f-252">La sintassi del puntatore a funzione può essere complessa, in particolare nei casi complessi come i puntatori a funzione annidati.</span><span class="sxs-lookup"><span data-stu-id="7b74f-252">The function pointer syntax can be cumbersome, particularly in complex cases like nested function pointers.</span></span> <span data-ttu-id="7b74f-253">Anziché fare in modo che gli sviluppatori eseguano la firma ogni `delegate`volta che il linguaggio potrebbe consentire dichiarazioni denominate di puntatori a funzione come avviene con .</span><span class="sxs-lookup"><span data-stu-id="7b74f-253">Rather than have developers type out the signature every time the language could allow for named declarations of function pointers as is done with `delegate`.</span></span>

``` csharp
func* void Action();

unsafe class NamedExample {
    void M(Action a) {
        a();
    }
}
```

<span data-ttu-id="7b74f-254">Parte del problema qui è la primitiva CLI sottostante non ha nomi, quindi questo sarebbe puramente un'invenzione di C , e richiedono un po ' di lavoro di metadati per abilitare.</span><span class="sxs-lookup"><span data-stu-id="7b74f-254">Part of the problem here is the underlying CLI primitive doesn't have names hence this would be purely a C# invention and require a bit of metadata work to enable.</span></span> <span data-ttu-id="7b74f-255">Questo è fattibile, ma è un'importante questione di lavoro.</span><span class="sxs-lookup"><span data-stu-id="7b74f-255">That is doable but is a significant about of work.</span></span> <span data-ttu-id="7b74f-256">In sostanza, è necessario che il linguaggio C'è un complemento alla tabella def del tipo esclusivamente per questi nomi.</span><span class="sxs-lookup"><span data-stu-id="7b74f-256">It essentially requires C# to have a companion to the type def table purely for these names.</span></span>

<span data-ttu-id="7b74f-257">Anche quando sono stati esaminati gli argomenti per i puntatori a funzione denominati abbiamo scoperto che potevano applicarsi altrettanto bene a una serie di altri scenari.</span><span class="sxs-lookup"><span data-stu-id="7b74f-257">Also when the arguments for named function pointers were examined we found they could apply equally well to a number of other scenarios.</span></span> <span data-ttu-id="7b74f-258">Ad esempio, sarebbe altrettanto conveniente dichiarare le tuple denominate per ridurre la necessità di digitare la firma completa in tutti i casi.</span><span class="sxs-lookup"><span data-stu-id="7b74f-258">For example it would be just as convenient to declare named tuples to reduce the need to type out the full signature in all cases.</span></span>

``` csharp
(int x, int y) Point;

class NamedTupleExample {
    void M(Point p) {
        Console.WriteLine(p.x);
    }
}
```

<span data-ttu-id="7b74f-259">Dopo la discussione abbiamo deciso `delegate*` di non consentire la dichiarazione denominata dei tipi.</span><span class="sxs-lookup"><span data-stu-id="7b74f-259">After discussion we decided to not allow named declaration of `delegate*` types.</span></span> <span data-ttu-id="7b74f-260">Se troviamo che c'è una notevole necessità per questo in base al feedback sull'utilizzo dei clienti, allora esamineremo una soluzione di denominazione che funziona per i puntatori a funzione, tuple, generics, ecc ... Questo è probabile che sia simile in `typedef` forma ad altri suggerimenti come il pieno supporto nella lingua.</span><span class="sxs-lookup"><span data-stu-id="7b74f-260">If we find there is significant need for this based on customer usage feedback then we will investigate a naming solution that works for function pointers, tuples, generics, etc ... This is likely to be similar in form to other suggestions like full `typedef` support in the language.</span></span>

## <a name="future-considerations"></a><span data-ttu-id="7b74f-261">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="7b74f-261">Future Considerations</span></span>

### <a name="static-local-functions"></a><span data-ttu-id="7b74f-262">funzioni locali statiche</span><span class="sxs-lookup"><span data-stu-id="7b74f-262">static local functions</span></span>

<span data-ttu-id="7b74f-263">Questo si riferisce alla `static` [proposta](https://github.com/dotnet/csharplang/issues/1565) di consentire il modificatore sulle funzioni locali.</span><span class="sxs-lookup"><span data-stu-id="7b74f-263">This refers to [the proposal](https://github.com/dotnet/csharplang/issues/1565) to allow the `static` modifier on local functions.</span></span> <span data-ttu-id="7b74f-264">Tale funzione sarebbe garantita per essere `static` emessa come e con la firma esatta specificata nel codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="7b74f-264">Such a function would be guaranteed to be emitted as `static` and with the exact signature specified in source code.</span></span> <span data-ttu-id="7b74f-265">Tale funzione dovrebbe essere un `&` argomento valido in quanto non contiene nessuno dei problemi che le funzioni locali hanno oggi</span><span class="sxs-lookup"><span data-stu-id="7b74f-265">Such a function should be a valid argument to `&` as it contains none of the problems local functions have today</span></span>

### <a name="static-delegates"></a><span data-ttu-id="7b74f-266">delegati statici</span><span class="sxs-lookup"><span data-stu-id="7b74f-266">static delegates</span></span>

<span data-ttu-id="7b74f-267">Ciò [the proposal](https://github.com/dotnet/csharplang/issues/302) si riferisce alla proposta `delegate` di consentire la `static` dichiarazione di tipi che possono riferirsi solo ai membri.</span><span class="sxs-lookup"><span data-stu-id="7b74f-267">This refers to [the proposal](https://github.com/dotnet/csharplang/issues/302) to allow for the declaration of `delegate` types which can only refer to `static` members.</span></span> <span data-ttu-id="7b74f-268">Il vantaggio è `delegate` che tali istanze possono essere senza allocazione e migliori negli scenari sensibili alle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="7b74f-268">The advantage being that such `delegate` instances can be allocation free and better in performance sensitive scenarios.</span></span>

<span data-ttu-id="7b74f-269">Se viene implementata la `static delegate` funzionalità del puntatore a funzione, la proposta verrà probabilmente chiusa. Il vantaggio proposto di tale funzionalità è la natura priva di allocazione.</span><span class="sxs-lookup"><span data-stu-id="7b74f-269">If the function pointer feature is implemented the `static delegate` proposal will likely be closed out. The proposed advantage of that feature is the allocation free nature.</span></span> <span data-ttu-id="7b74f-270">Tuttavia recenti indagini hanno trovato che non è possibile ottenere a causa di scarico dell'assemblaggio.</span><span class="sxs-lookup"><span data-stu-id="7b74f-270">However recent investigations have found that is not possible to achieve due to assembly unloading.</span></span> <span data-ttu-id="7b74f-271">Deve essere presente una `static delegate` maniglia forte dal al metodo a cui fa riferimento per evitare che l'assembly venga scaricato da sotto di esso.</span><span class="sxs-lookup"><span data-stu-id="7b74f-271">There must be a strong handle from the `static delegate` to the method it refers to in order to keep the assembly from being unloaded out from under it.</span></span>

<span data-ttu-id="7b74f-272">Per mantenere `static delegate` ogni istanza sarebbe necessario allocare un nuovo handle che sia in contrasto con gli obiettivi della proposta.</span><span class="sxs-lookup"><span data-stu-id="7b74f-272">To maintain every `static delegate` instance would be required to allocate a new handle which runs counter to the goals of the proposal.</span></span> <span data-ttu-id="7b74f-273">C'erano alcuni disegni in cui l'allocazione potrebbe essere ammortizzata a una singola allocazione per sito di chiamata, ma che era un po 'complesso e non sembrava vale la pena il trade off.</span><span class="sxs-lookup"><span data-stu-id="7b74f-273">There were some designs where the allocation could be amortized to a single allocation per call-site but that was a bit complex and didn't seem worth the trade off.</span></span>

<span data-ttu-id="7b74f-274">Ciò significa che gli sviluppatori devono essenzialmente decidere tra i seguenti compromessi:</span><span class="sxs-lookup"><span data-stu-id="7b74f-274">That means developers essentially have to decide between the following trade offs:</span></span>

1. <span data-ttu-id="7b74f-275">Sicurezza di fronte allo scarico dell'assemblaggio: ciò richiede allocazioni e quindi `delegate` è già un'opzione sufficiente.</span><span class="sxs-lookup"><span data-stu-id="7b74f-275">Safety in the face of assembly unloading: this requires allocations and hence `delegate` is already a sufficient option.</span></span>
1. <span data-ttu-id="7b74f-276">Nessuna sicurezza di fronte allo `delegate*`scarico dell'assemblaggio: utilizzare un .</span><span class="sxs-lookup"><span data-stu-id="7b74f-276">No safety in face of assembly unloading: use a `delegate*`.</span></span> <span data-ttu-id="7b74f-277">Questo può essere `struct` eseguito il `unsafe` wrapping in un per consentire l'utilizzo all'esterno di un contesto nel resto del codice.</span><span class="sxs-lookup"><span data-stu-id="7b74f-277">This can be wrapped in a `struct` to allow usage outside an `unsafe` context in the rest of the code.</span></span>
