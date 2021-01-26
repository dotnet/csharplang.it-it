---
ms.openlocfilehash: 05de92313261e2d0c74de13df5a3c0eb96b49e1e
ms.sourcegitcommit: 6ab8409a32f1249f9aef618054426acb7bc7b4c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763664"
---
# <a name="function-pointers"></a><span data-ttu-id="09e46-101">Puntatori a funzione</span><span class="sxs-lookup"><span data-stu-id="09e46-101">Function Pointers</span></span>

## <a name="summary"></a><span data-ttu-id="09e46-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="09e46-102">Summary</span></span>

<span data-ttu-id="09e46-103">Questa proposta fornisce costrutti di linguaggio che espongono i codici operativi IL al quale attualmente non è possibile accedere in modo efficiente, o, in C#: `ldftn` e `calli` .</span><span class="sxs-lookup"><span data-stu-id="09e46-103">This proposal provides language constructs that expose IL opcodes that cannot currently be accessed efficiently, or at all, in C# today: `ldftn` and `calli`.</span></span> <span data-ttu-id="09e46-104">Questi codici operativi IL può essere importante nel codice a prestazioni elevate e gli sviluppatori hanno bisogno di un modo efficiente per accedervi.</span><span class="sxs-lookup"><span data-stu-id="09e46-104">These IL opcodes can be important in high performance code and developers need an efficient way to access them.</span></span>

## <a name="motivation"></a><span data-ttu-id="09e46-105">Motivazione</span><span class="sxs-lookup"><span data-stu-id="09e46-105">Motivation</span></span>

<span data-ttu-id="09e46-106">Le motivazioni e lo sfondo per questa funzionalità sono descritti nel seguente problema (come è una potenziale implementazione della funzionalità):</span><span class="sxs-lookup"><span data-stu-id="09e46-106">The motivations and background for this feature are described in the following issue (as is a potential implementation of the feature):</span></span>

https://github.com/dotnet/csharplang/issues/191

<span data-ttu-id="09e46-107">Si tratta di una proposta di progettazione alternativa alle [funzioni intrinseche del compilatore](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)</span><span class="sxs-lookup"><span data-stu-id="09e46-107">This is an alternate design proposal to [compiler intrinsics](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)</span></span>

## <a name="detailed-design"></a><span data-ttu-id="09e46-108">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="09e46-108">Detailed Design</span></span>

### <a name="function-pointers"></a><span data-ttu-id="09e46-109">Puntatori funzione</span><span class="sxs-lookup"><span data-stu-id="09e46-109">Function pointers</span></span>

<span data-ttu-id="09e46-110">Il linguaggio consentirà la dichiarazione di puntatori a funzione usando la `delegate*` sintassi.</span><span class="sxs-lookup"><span data-stu-id="09e46-110">The language will allow for the declaration of function pointers using the `delegate*` syntax.</span></span> <span data-ttu-id="09e46-111">La sintassi completa viene descritta in dettaglio nella sezione successiva, ma è concepita come la sintassi utilizzata dalle dichiarazioni di `Func` `Action` tipo e.</span><span class="sxs-lookup"><span data-stu-id="09e46-111">The full syntax is described in detail in the next section but it is meant to resemble the syntax used by `Func` and `Action` type declarations.</span></span>

``` csharp
unsafe class Example {
    void Example(Action<int> a, delegate*<int, void> f) {
        a(42);
        f(42);
    }
}
```

<span data-ttu-id="09e46-112">Questi tipi sono rappresentati usando il tipo di puntatore a funzione, come descritto in ECMA-335.</span><span class="sxs-lookup"><span data-stu-id="09e46-112">These types are represented using the function pointer type as outlined in ECMA-335.</span></span> <span data-ttu-id="09e46-113">Ciò significa che la chiamata di un oggetto utilizzerà la `delegate*` `calli` posizione in cui la chiamata di un oggetto `delegate` verrà utilizzata `callvirt` nel `Invoke` metodo.</span><span class="sxs-lookup"><span data-stu-id="09e46-113">This means invocation of a `delegate*` will use `calli` where invocation of a `delegate` will use `callvirt` on the `Invoke` method.</span></span>
<span data-ttu-id="09e46-114">Sintatticamente, sebbene la chiamata sia identica per entrambi i costrutti.</span><span class="sxs-lookup"><span data-stu-id="09e46-114">Syntactically though invocation is identical for both constructs.</span></span>

<span data-ttu-id="09e46-115">La definizione ECMA-335 dei puntatori al metodo include la convenzione di chiamata come parte della firma del tipo (sezione 7,1).</span><span class="sxs-lookup"><span data-stu-id="09e46-115">The ECMA-335 definition of method pointers includes the calling convention as part of the type signature (section 7.1).</span></span>
<span data-ttu-id="09e46-116">La convenzione di chiamata predefinita sarà `managed` .</span><span class="sxs-lookup"><span data-stu-id="09e46-116">The default calling convention will be `managed`.</span></span> <span data-ttu-id="09e46-117">È possibile specificare le convenzioni di chiamata non gestite inserendo una `unmanaged` parola chiave dopo la `delegate*` sintassi, che utilizzerà l'impostazione predefinita della piattaforma di Runtime.</span><span class="sxs-lookup"><span data-stu-id="09e46-117">Unmanaged calling conventions can by specified by putting an `unmanaged` keyword afer the `delegate*` syntax, which will use the runtime platform default.</span></span> <span data-ttu-id="09e46-118">È quindi possibile specificare convenzioni non gestite specifiche tra parentesi quadre per la `unmanaged` parola chiave specificando qualsiasi tipo che inizia con `CallConv` nello `System.Runtime.CompilerServices` spazio dei nomi, lasciando il `CallConv` prefisso.</span><span class="sxs-lookup"><span data-stu-id="09e46-118">Specific unmanaged conventions can then be specified in brackets to the `unmanaged` keyword by specifying any type starting with `CallConv` in the `System.Runtime.CompilerServices` namespace, leaving off the `CallConv` prefix.</span></span> <span data-ttu-id="09e46-119">Questi tipi devono provenire dalla libreria principale del programma e il set di combinazioni valide è dipendente dalla piattaforma.</span><span class="sxs-lookup"><span data-stu-id="09e46-119">These types must come from the program's core library, and the set of valid combinations is platform-dependent.</span></span>

``` csharp
//This method has a managed calling convention. This is the same as leaving the managed keyword off.
delegate* managed<int, int>;

// This method will be invoked using whatever the default unmanaged calling convention on the runtime
// platform is. This is platform and architecture dependent and is determined by the CLR at runtime.
delegate* unmanaged<int, int>;

// This method will be invoked using the cdecl calling convention
// Cdecl maps to System.Runtime.CompilerServices.CallConvCdecl
delegate* unmanaged[Cdecl] <int, int>;

// This method will be invoked using the stdcall calling convention, and suppresses GC transition
// Stdcall maps to System.Runtime.CompilerServices.CallConvStdcall
// SuppressGCTransition maps to System.Runtime.CompilerServices.CallConvSuppressGCTransition
delegate* unmanaged[Stdcall, SuppressGCTransition] <int, int>;
```

<span data-ttu-id="09e46-120">Le conversioni tra `delegate*` i tipi vengono eseguite in base alla relativa firma, inclusa la convenzione di chiamata.</span><span class="sxs-lookup"><span data-stu-id="09e46-120">Conversions between `delegate*` types is done based on their signature including the calling convention.</span></span>

``` csharp
unsafe class Example {
    void Conversions() {
        delegate*<int, int, int> p1 = ...;
        delegate* managed<int, int, int> p2 = ...;
        delegate* unmanaged<int, int, int> p3 = ...;

        p1 = p2; // okay p1 and p2 have compatible signatures
        Console.WriteLine(p2 == p1); // True
        p2 = p3; // error: calling conventions are incompatible
    }
}
```

<span data-ttu-id="09e46-121">Un `delegate*` tipo è un tipo di puntatore che significa che ha tutte le funzionalità e le restrizioni di un tipo di puntatore standard:</span><span class="sxs-lookup"><span data-stu-id="09e46-121">A `delegate*` type is a pointer type which means it has all of the capabilities and restrictions of a standard pointer type:</span></span>

- <span data-ttu-id="09e46-122">Valido solo in un `unsafe` contesto.</span><span class="sxs-lookup"><span data-stu-id="09e46-122">Only valid in an `unsafe` context.</span></span>
- <span data-ttu-id="09e46-123">I metodi che contengono un `delegate*` parametro o un tipo restituito possono essere chiamati solo da un `unsafe` contesto.</span><span class="sxs-lookup"><span data-stu-id="09e46-123">Methods which contain a `delegate*` parameter or return type can only be called from an `unsafe` context.</span></span>
- <span data-ttu-id="09e46-124">Non può essere convertito in `object` .</span><span class="sxs-lookup"><span data-stu-id="09e46-124">Cannot be converted to `object`.</span></span>
- <span data-ttu-id="09e46-125">Non può essere usato come argomento generico.</span><span class="sxs-lookup"><span data-stu-id="09e46-125">Cannot be used as a generic argument.</span></span>
- <span data-ttu-id="09e46-126">Può convertire in modo implicito `delegate*` in `void*` .</span><span class="sxs-lookup"><span data-stu-id="09e46-126">Can implicitly convert `delegate*` to `void*`.</span></span>
- <span data-ttu-id="09e46-127">Consente di eseguire la conversione esplicita da `void*` a `delegate*` .</span><span class="sxs-lookup"><span data-stu-id="09e46-127">Can explicitly convert from `void*` to `delegate*`.</span></span>

<span data-ttu-id="09e46-128">Restrizioni:</span><span class="sxs-lookup"><span data-stu-id="09e46-128">Restrictions:</span></span>

- <span data-ttu-id="09e46-129">Gli attributi personalizzati non possono essere applicati a un oggetto o a uno `delegate*` dei relativi elementi.</span><span class="sxs-lookup"><span data-stu-id="09e46-129">Custom attributes cannot be applied to a `delegate*` or any of its elements.</span></span>
- <span data-ttu-id="09e46-130">Un `delegate*` parametro non può essere contrassegnato come `params`</span><span class="sxs-lookup"><span data-stu-id="09e46-130">A `delegate*` parameter cannot be marked as `params`</span></span>
- <span data-ttu-id="09e46-131">Un `delegate*` tipo ha tutte le restrizioni di un tipo di puntatore normale.</span><span class="sxs-lookup"><span data-stu-id="09e46-131">A `delegate*` type has all of the restrictions of a normal pointer type.</span></span>
- <span data-ttu-id="09e46-132">Non è possibile eseguire l'aritmetica del puntatore direttamente sui tipi di puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="09e46-132">Pointer arithmetic cannot be performed directly on function pointer types.</span></span>

### <a name="function-pointer-syntax"></a><span data-ttu-id="09e46-133">Sintassi del puntatore a funzione</span><span class="sxs-lookup"><span data-stu-id="09e46-133">Function pointer syntax</span></span>

<span data-ttu-id="09e46-134">La sintassi del puntatore a funzione completa è rappresentata dalla grammatica seguente:</span><span class="sxs-lookup"><span data-stu-id="09e46-134">The full function pointer syntax is represented by the following grammar:</span></span>

```antlr
pointer_type
    : ...
    | funcptr_type
    ;

funcptr_type
    : 'delegate' '*' calling_convention_specifier? '<' funcptr_parameter_list funcptr_return_type '>'
    ;

calling_convention_specifier
    : 'managed'
    | 'unmanaged' ('[' unmanaged_calling_convention ']')?
    ;

unmanaged_calling_convention
    : 'Cdecl'
    | 'Stdcall'
    | 'Thiscall'
    | 'Fastcall'
    | identifier (',' identifier)*
    ;

funptr_parameter_list
    : (funcptr_parameter ',')*
    ;

funcptr_parameter
    : funcptr_parameter_modifier? type
    ;

funcptr_return_type
    : funcptr_return_modifier? return_type
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

<span data-ttu-id="09e46-135">Se non `calling_convention_specifier` viene specificato alcun valore, il valore predefinito è `managed` .</span><span class="sxs-lookup"><span data-stu-id="09e46-135">If no `calling_convention_specifier` is provided, the default is `managed`.</span></span> <span data-ttu-id="09e46-136">La codifica dei metadati precisa di `calling_convention_specifier` e di quali `identifier` sono i validi in `unmanaged_calling_convention` viene analizzata nella [rappresentazione dei metadati delle convenzioni di chiamata](#metadata-representation-of-calling-conventions).</span><span class="sxs-lookup"><span data-stu-id="09e46-136">The precise metadata encoding of the `calling_convention_specifier` and what `identifier`s are valid in the `unmanaged_calling_convention` is covered in [Metadata Representation of Calling Conventions](#metadata-representation-of-calling-conventions).</span></span>

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

### <a name="function-pointer-conversions"></a><span data-ttu-id="09e46-137">Conversioni di puntatori a funzione</span><span class="sxs-lookup"><span data-stu-id="09e46-137">Function pointer conversions</span></span>

<span data-ttu-id="09e46-138">In un contesto non sicuro, il set di conversioni implicite disponibili (conversioni implicite) viene esteso in modo da includere le seguenti conversioni implicite del puntatore:</span><span class="sxs-lookup"><span data-stu-id="09e46-138">In an unsafe context, the set of available implicit conversions (Implicit conversions) is extended to include the following implicit pointer conversions:</span></span>
- [<span data-ttu-id="09e46-139">_Conversioni esistenti_</span><span class="sxs-lookup"><span data-stu-id="09e46-139">_Existing conversions_</span></span>](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#pointer-conversions)
- <span data-ttu-id="09e46-140">Dal _\_ tipo funcptr_ `F0` a un _altro \_ tipo funcptr_ `F1` , purché siano soddisfatte tutte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="09e46-140">From _funcptr\_type_ `F0` to another _funcptr\_type_ `F1`, provided all of the following are true:</span></span>
    - <span data-ttu-id="09e46-141">`F0` e `F1` hanno lo stesso numero di parametri e ogni parametro `D0n` in `F0` ha gli stessi `ref` `out` modificatori, o del `in` parametro corrispondente `D1n` in `F1` .</span><span class="sxs-lookup"><span data-stu-id="09e46-141">`F0` and `F1` have the same number of parameters, and each parameter `D0n` in `F0` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter `D1n` in `F1`.</span></span>
    - <span data-ttu-id="09e46-142">Per ogni parametro di valore (un parametro senza `ref` `out` `in` modificatore, o), esiste una conversione di identità, una conversione implicita di un riferimento o una conversione del puntatore implicita dal tipo di parametro in `F0` al tipo di parametro corrispondente in `F1` .</span><span class="sxs-lookup"><span data-stu-id="09e46-142">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `F0` to the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="09e46-143">Per ogni `ref` `out` parametro, o `in` , il tipo di parametro in `F0` corrisponde al tipo di parametro corrispondente in `F1` .</span><span class="sxs-lookup"><span data-stu-id="09e46-143">For each `ref`, `out`, or `in` parameter, the parameter type in `F0` is the same as the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="09e46-144">Se il tipo restituito è per valore (No `ref` o `ref readonly` ), esiste un'identità, un riferimento implicito o una conversione del puntatore implicita dal tipo restituito di `F1` al tipo restituito di `F0` .</span><span class="sxs-lookup"><span data-stu-id="09e46-144">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F1` to the return type of `F0`.</span></span>
    - <span data-ttu-id="09e46-145">Se il tipo restituito è per riferimento ( `ref` o `ref readonly` ), il tipo restituito e i `ref` modificatori di `F1` sono gli stessi del tipo restituito e `ref` dei modificatori di `F0` .</span><span class="sxs-lookup"><span data-stu-id="09e46-145">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F1` are the same as the return type and `ref` modifiers of `F0`.</span></span>
    - <span data-ttu-id="09e46-146">La convenzione di chiamata di `F0` è uguale alla convenzione di chiamata di `F1` .</span><span class="sxs-lookup"><span data-stu-id="09e46-146">The calling convention of `F0` is the same as the calling convention of `F1`.</span></span>

### <a name="allow-address-of-to-target-methods"></a><span data-ttu-id="09e46-147">Consenti indirizzo-di ai metodi di destinazione</span><span class="sxs-lookup"><span data-stu-id="09e46-147">Allow address-of to target methods</span></span>

<span data-ttu-id="09e46-148">I gruppi di metodi saranno ora consentiti come argomenti per un'espressione address-of.</span><span class="sxs-lookup"><span data-stu-id="09e46-148">Method groups will now be allowed as arguments to an address-of expression.</span></span> <span data-ttu-id="09e46-149">Il tipo di tale espressione sarà un `delegate*` che ha la firma equivalente del metodo di destinazione e una convenzione di chiamata gestita:</span><span class="sxs-lookup"><span data-stu-id="09e46-149">The type of such an expression will be a `delegate*` which has the equivalent signature of the target method and a managed calling convention:</span></span>

``` csharp
unsafe class Util {
    public static void Log() { }

    void Use() {
        delegate*<void> ptr1 = &Util.Log;

        // Error: type "delegate*<void>" not compatible with "delegate*<int>";
        delegate*<int> ptr2 = &Util.Log;
   }
}
```

<span data-ttu-id="09e46-150">In un contesto non sicuro, un metodo `M` è compatibile con un tipo di puntatore a funzione `F` se si verificano tutte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="09e46-150">In an unsafe context, a method `M` is compatible with a function pointer type `F` if all of the following are true:</span></span>
- <span data-ttu-id="09e46-151">`M` e `F` hanno lo stesso numero di parametri e ogni parametro in `M` ha gli stessi `ref` `out` modificatori, o del `in` parametro corrispondente in `F` .</span><span class="sxs-lookup"><span data-stu-id="09e46-151">`M` and `F` have the same number of parameters, and each parameter in `M` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter in `F`.</span></span>
- <span data-ttu-id="09e46-152">Per ogni parametro di valore (un parametro senza `ref` `out` `in` modificatore, o), esiste una conversione di identità, una conversione implicita di un riferimento o una conversione del puntatore implicita dal tipo di parametro in `M` al tipo di parametro corrispondente in `F` .</span><span class="sxs-lookup"><span data-stu-id="09e46-152">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `M` to the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="09e46-153">Per ogni `ref` `out` parametro, o `in` , il tipo di parametro in `M` corrisponde al tipo di parametro corrispondente in `F` .</span><span class="sxs-lookup"><span data-stu-id="09e46-153">For each `ref`, `out`, or `in` parameter, the parameter type in `M` is the same as the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="09e46-154">Se il tipo restituito è per valore (No `ref` o `ref readonly` ), esiste un'identità, un riferimento implicito o una conversione del puntatore implicita dal tipo restituito di `F` al tipo restituito di `M` .</span><span class="sxs-lookup"><span data-stu-id="09e46-154">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F` to the return type of `M`.</span></span>
- <span data-ttu-id="09e46-155">Se il tipo restituito è per riferimento ( `ref` o `ref readonly` ), il tipo restituito e i `ref` modificatori di `F` sono gli stessi del tipo restituito e `ref` dei modificatori di `M` .</span><span class="sxs-lookup"><span data-stu-id="09e46-155">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F` are the same as the return type and `ref` modifiers of `M`.</span></span>
- <span data-ttu-id="09e46-156">La convenzione di chiamata di `M` è uguale alla convenzione di chiamata di `F` .</span><span class="sxs-lookup"><span data-stu-id="09e46-156">The calling convention of `M` is the same as the calling convention of `F`.</span></span> <span data-ttu-id="09e46-157">Sono inclusi sia il bit della convenzione di chiamata sia i flag della convenzione di chiamata specificati nell'identificatore non gestito.</span><span class="sxs-lookup"><span data-stu-id="09e46-157">This includes both the calling convention bit, as well as any calling convention flags specified in the unmanaged identifier.</span></span>
- <span data-ttu-id="09e46-158">`M` è un metodo statico.</span><span class="sxs-lookup"><span data-stu-id="09e46-158">`M` is a static method.</span></span>

<span data-ttu-id="09e46-159">In un contesto non sicuro, esiste una conversione implicita da un'espressione address-of la cui destinazione è un gruppo `E` di metodi a un tipo di puntatore a funzione compatibile `F` se `E` contiene almeno un metodo applicabile nel formato normale a un elenco di argomenti costruito usando i tipi di parametro e i modificatori di `F` , come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="09e46-159">In an unsafe context, an implicit conversion exists from an address-of expression whose target is a method group `E` to a compatible function pointer type `F` if `E` contains at least one method that is applicable in its normal form to an argument list constructed by use of the parameter types and modifiers of `F`, as described in the following.</span></span>
- <span data-ttu-id="09e46-160">Viene selezionato un singolo metodo `M` corrispondente alla chiamata di un metodo del form `E(A)` con le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="09e46-160">A single method `M` is selected corresponding to a method invocation of the form `E(A)` with the following modifications:</span></span>
   - <span data-ttu-id="09e46-161">L'elenco `A` degli argomenti è un elenco di espressioni, ciascuna classificata come variabile e con il tipo e il modificatore ( `ref` , `out` o `in` ) dell' _\_ \_ elenco di parametri funcptr_ corrispondente di `F` .</span><span class="sxs-lookup"><span data-stu-id="09e46-161">The arguments list `A` is a list of expressions, each classified as a variable and with the type and modifier (`ref`, `out`, or `in`) of the corresponding _funcptr\_parameter\_list_ of `F`.</span></span>
   - <span data-ttu-id="09e46-162">I metodi candidati sono solo i metodi applicabili nel formato normale, non quelli applicabili nella forma espansa.</span><span class="sxs-lookup"><span data-stu-id="09e46-162">The candidate methods are only those methods that are applicable in their normal form, not those applicable in their expanded form.</span></span>
   - <span data-ttu-id="09e46-163">I metodi candidati sono solo i metodi statici.</span><span class="sxs-lookup"><span data-stu-id="09e46-163">The candidate methods are only those methods that are static.</span></span>
- <span data-ttu-id="09e46-164">Se l'algoritmo di risoluzione dell'overload genera un errore, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="09e46-164">If the algorithm of overload resolution produces an error, then a compile-time error occurs.</span></span> <span data-ttu-id="09e46-165">In caso contrario, l'algoritmo produce un unico metodo migliore `M` con lo stesso numero di parametri di `F` e la conversione viene considerata esistente.</span><span class="sxs-lookup"><span data-stu-id="09e46-165">Otherwise, the algorithm produces a single best method `M` having the same number of parameters as `F` and the conversion is considered to exist.</span></span>
- <span data-ttu-id="09e46-166">Il metodo selezionato `M` deve essere compatibile (come definito in precedenza) con il tipo di puntatore a funzione `F` .</span><span class="sxs-lookup"><span data-stu-id="09e46-166">The selected method `M` must be compatible (as defined above) with the function pointer type `F`.</span></span> <span data-ttu-id="09e46-167">In caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="09e46-167">Otherwise, a compile-time error occurs.</span></span>
- <span data-ttu-id="09e46-168">Il risultato della conversione è un puntatore a funzione di tipo `F` .</span><span class="sxs-lookup"><span data-stu-id="09e46-168">The result of the conversion is a function pointer of type `F`.</span></span>

<span data-ttu-id="09e46-169">Ciò significa che gli sviluppatori possono dipendere dalle regole di risoluzione dell'overload per lavorare insieme all'operatore address-of:</span><span class="sxs-lookup"><span data-stu-id="09e46-169">This means developers can depend on overload resolution rules to work in conjunction with the address-of operator:</span></span>

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

<span data-ttu-id="09e46-170">L'operatore address-of verrà implementato utilizzando l' `ldftn` istruzione.</span><span class="sxs-lookup"><span data-stu-id="09e46-170">The address-of operator will be implemented using the `ldftn` instruction.</span></span>

<span data-ttu-id="09e46-171">Limitazioni di questa funzionalità:</span><span class="sxs-lookup"><span data-stu-id="09e46-171">Restrictions of this feature:</span></span>

- <span data-ttu-id="09e46-172">Si applica solo ai metodi contrassegnati come `static` .</span><span class="sxs-lookup"><span data-stu-id="09e46-172">Only applies to methods marked as `static`.</span></span>
- <span data-ttu-id="09e46-173">Le `static` funzioni non locali non possono essere utilizzate in `&` .</span><span class="sxs-lookup"><span data-stu-id="09e46-173">Non-`static` local functions cannot be used in `&`.</span></span> <span data-ttu-id="09e46-174">I dettagli di implementazione di questi metodi non sono deliberatamente specificati dal linguaggio.</span><span class="sxs-lookup"><span data-stu-id="09e46-174">The implementation details of these methods are deliberately not specified by the language.</span></span> <span data-ttu-id="09e46-175">Ciò include la presenza di un'istanza statica rispetto a quella con cui vengono emesse le firme.</span><span class="sxs-lookup"><span data-stu-id="09e46-175">This includes whether they are static vs. instance or exactly what signature they are emitted with.</span></span>


### <a name="operators-on-function-pointer-types"></a><span data-ttu-id="09e46-176">Operatori sui tipi di puntatore a funzione</span><span class="sxs-lookup"><span data-stu-id="09e46-176">Operators on Function Pointer Types</span></span>

<span data-ttu-id="09e46-177">La sezione in codice unsafe sugli operatori viene modificata come segue:</span><span class="sxs-lookup"><span data-stu-id="09e46-177">The section in unsafe code on operators is modified as such:</span></span>

> <span data-ttu-id="09e46-178">In un contesto non sicuro sono disponibili numerosi costrutti per operare su tutti i _pointer \_ type_s che non sono _funcptr \_ type_s:</span><span class="sxs-lookup"><span data-stu-id="09e46-178">In an unsafe context, several constructs are available for operating on all _pointer\_type_s that are not _funcptr\_type_s:</span></span>
>
> *  <span data-ttu-id="09e46-179">L' `*` operatore può essere utilizzato per eseguire un riferimento indiretto del puntatore ([riferimento indiretto](../../spec/unsafe-code.md#pointer-indirection)).</span><span class="sxs-lookup"><span data-stu-id="09e46-179">The `*` operator may be used to perform pointer indirection ([Pointer indirection](../../spec/unsafe-code.md#pointer-indirection)).</span></span>
> *  <span data-ttu-id="09e46-180">L' `->` operatore può essere usato per accedere a un membro di uno struct tramite un puntatore ([accesso al membro del puntatore](../../spec/unsafe-code.md#pointer-member-access)).</span><span class="sxs-lookup"><span data-stu-id="09e46-180">The `->` operator may be used to access a member of a struct through a pointer ([Pointer member access](../../spec/unsafe-code.md#pointer-member-access)).</span></span>
> *  <span data-ttu-id="09e46-181">L' `[]` operatore può essere utilizzato per indicizzare un puntatore ([accesso all'elemento del puntatore](../../spec/unsafe-code.md#pointer-element-access)).</span><span class="sxs-lookup"><span data-stu-id="09e46-181">The `[]` operator may be used to index a pointer ([Pointer element access](../../spec/unsafe-code.md#pointer-element-access)).</span></span>
> *  <span data-ttu-id="09e46-182">L' `&` operatore può essere utilizzato per ottenere l'indirizzo di una variabile ([l'operatore address-of](../../spec/unsafe-code.md#the-address-of-operator)).</span><span class="sxs-lookup"><span data-stu-id="09e46-182">The `&` operator may be used to obtain the address of a variable ([The address-of operator](../../spec/unsafe-code.md#the-address-of-operator)).</span></span>
> *  <span data-ttu-id="09e46-183">Gli `++` `--` operatori e possono essere utilizzati per incrementare e decrementare i puntatori ([incremento e decremento del puntatore](../../spec/unsafe-code.md#pointer-increment-and-decrement)).</span><span class="sxs-lookup"><span data-stu-id="09e46-183">The `++` and `--` operators may be used to increment and decrement pointers ([Pointer increment and decrement](../../spec/unsafe-code.md#pointer-increment-and-decrement)).</span></span>
> *  <span data-ttu-id="09e46-184">Gli `+` `-` operatori e possono essere utilizzati per eseguire l'aritmetica dei puntatori ([aritmetica del puntatore](../../spec/unsafe-code.md#pointer-arithmetic)).</span><span class="sxs-lookup"><span data-stu-id="09e46-184">The `+` and `-` operators may be used to perform pointer arithmetic ([Pointer arithmetic](../../spec/unsafe-code.md#pointer-arithmetic)).</span></span>
> *  <span data-ttu-id="09e46-185">Gli `==` `!=` operatori,, `<` ,, `>` `<=` e `=>` possono essere usati per confrontare i puntatori ([confronto tra](../../spec/unsafe-code.md#pointer-comparison)puntatori).</span><span class="sxs-lookup"><span data-stu-id="09e46-185">The `==`, `!=`, `<`, `>`, `<=`, and `=>` operators may be used to compare pointers ([Pointer comparison](../../spec/unsafe-code.md#pointer-comparison)).</span></span>
> *  <span data-ttu-id="09e46-186">L' `stackalloc` operatore può essere utilizzato per allocare memoria dallo stack di chiamate ([buffer a dimensione fissa](../../spec/unsafe-code.md#fixed-size-buffers)).</span><span class="sxs-lookup"><span data-stu-id="09e46-186">The `stackalloc` operator may be used to allocate memory from the call stack ([Fixed size buffers](../../spec/unsafe-code.md#fixed-size-buffers)).</span></span>
> *  <span data-ttu-id="09e46-187">L' `fixed` istruzione può essere utilizzata per correggere temporaneamente una variabile in modo che sia possibile ottenere l'indirizzo ([istruzione fixed](../../spec/unsafe-code.md#the-fixed-statement)).</span><span class="sxs-lookup"><span data-stu-id="09e46-187">The `fixed` statement may be used to temporarily fix a variable so its address can be obtained ([The fixed statement](../../spec/unsafe-code.md#the-fixed-statement)).</span></span>
> 
> <span data-ttu-id="09e46-188">In un contesto non sicuro sono disponibili numerosi costrutti per operare su tutti i _funcptr \_ type_s:</span><span class="sxs-lookup"><span data-stu-id="09e46-188">In an unsafe context, several constructs are available for operating on all _funcptr\_type_s:</span></span>
> *  <span data-ttu-id="09e46-189">L' `&` operatore può essere utilizzato per ottenere l'indirizzo dei metodi statici ([Consenti indirizzo-di ai metodi di destinazione](#allow-address-of-to-target-methods))</span><span class="sxs-lookup"><span data-stu-id="09e46-189">The `&` operator may be used to obtain the address of static methods ([Allow address-of to target methods](#allow-address-of-to-target-methods))</span></span>
> *  <span data-ttu-id="09e46-190">Gli `==` `!=` operatori,, `<` ,, `>` `<=` e `=>` possono essere usati per confrontare i puntatori ([confronto tra](../../spec/unsafe-code.md#pointer-comparison)puntatori).</span><span class="sxs-lookup"><span data-stu-id="09e46-190">The `==`, `!=`, `<`, `>`, `<=`, and `=>` operators may be used to compare pointers ([Pointer comparison](../../spec/unsafe-code.md#pointer-comparison)).</span></span>

<span data-ttu-id="09e46-191">Inoltre, si modificano tutte le sezioni in `Pointers in expressions` per impedire tipi di puntatore a funzione, eccetto `Pointer comparison` e `The sizeof operator` .</span><span class="sxs-lookup"><span data-stu-id="09e46-191">Additionally, we modify all the sections in `Pointers in expressions` to forbid function pointer types, except `Pointer comparison` and `The sizeof operator`.</span></span>

### <a name="better-function-member"></a><span data-ttu-id="09e46-192">Membro di funzione migliore</span><span class="sxs-lookup"><span data-stu-id="09e46-192">Better function member</span></span>

<span data-ttu-id="09e46-193">La specifica del membro di funzione migliore verrà modificata in modo da includere la riga seguente:</span><span class="sxs-lookup"><span data-stu-id="09e46-193">The better function member specification will be changed to include the following line:</span></span>

> <span data-ttu-id="09e46-194">Un `delegate*` è più specifico di `void*`</span><span class="sxs-lookup"><span data-stu-id="09e46-194">A `delegate*` is more specific than `void*`</span></span>

<span data-ttu-id="09e46-195">Ciò significa che è possibile eseguire l'overload su `void*` e un oggetto `delegate*` e continuare a usare l'operatore address-of.</span><span class="sxs-lookup"><span data-stu-id="09e46-195">This means that it is possible to overload on `void*` and a `delegate*` and still sensibly use the address-of operator.</span></span>

### <a name="type-inference"></a><span data-ttu-id="09e46-196">Inferenza di tipi</span><span class="sxs-lookup"><span data-stu-id="09e46-196">Type Inference</span></span>

<span data-ttu-id="09e46-197">Nel codice unsafe vengono apportate le modifiche seguenti agli algoritmi di inferenza del tipo:</span><span class="sxs-lookup"><span data-stu-id="09e46-197">In unsafe code, the following changes are made to the type inference algorithms:</span></span>

#### <a name="input-types"></a><span data-ttu-id="09e46-198">Tipi di input</span><span class="sxs-lookup"><span data-stu-id="09e46-198">Input types</span></span>

https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#input-types

<span data-ttu-id="09e46-199">Viene aggiunto quanto segue:</span><span class="sxs-lookup"><span data-stu-id="09e46-199">The following is added:</span></span>

> <span data-ttu-id="09e46-200">Se `E` è un gruppo di metodi address-of e `T` è un tipo di puntatore a funzione, tutti i tipi di parametro di `T` sono tipi di input di `E` con tipo `T` .</span><span class="sxs-lookup"><span data-stu-id="09e46-200">If `E` is an address-of method group and `T` is a function pointer type then all the parameter types of `T` are input types of `E` with type `T`.</span></span>

#### <a name="output-types"></a><span data-ttu-id="09e46-201">Tipi di output</span><span class="sxs-lookup"><span data-stu-id="09e46-201">Output types</span></span>

https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#output-types

<span data-ttu-id="09e46-202">Viene aggiunto quanto segue:</span><span class="sxs-lookup"><span data-stu-id="09e46-202">The following is added:</span></span>

> <span data-ttu-id="09e46-203">Se `E` è un gruppo di metodi address-of e `T` è un tipo di puntatore a funzione, il tipo restituito di `T` è un tipo di output di tipo `E` `T` .</span><span class="sxs-lookup"><span data-stu-id="09e46-203">If `E` is an address-of method group and `T` is a function pointer type then the return type of `T` is an output type of `E` with type `T`.</span></span>

#### <a name="output-type-inferences"></a><span data-ttu-id="09e46-204">Inferenza del tipo di output</span><span class="sxs-lookup"><span data-stu-id="09e46-204">Output type inferences</span></span>

https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#output-type-inferences

<span data-ttu-id="09e46-205">Viene aggiunto il seguente Bullet tra i punti elenco 2 e 3:</span><span class="sxs-lookup"><span data-stu-id="09e46-205">The following bullet is added between bullets 2 and 3:</span></span>

> * <span data-ttu-id="09e46-206">Se `E` è un gruppo di metodi address-of e `T` è un tipo di puntatore a funzione con tipi di parametro `T1...Tk` e tipo restituito e `Tb` la risoluzione dell'overload di `E` con i tipi `T1..Tk` produce un solo metodo con tipo restituito `U` , viene eseguita un' _inferenza con binding inferiore_ da `U` a `Tb` .</span><span class="sxs-lookup"><span data-stu-id="09e46-206">If `E` is an address-of method group and `T` is a function pointer type with parameter types `T1...Tk` and return type `Tb`, and overload resolution of `E` with the types `T1..Tk` yields a single method with return type `U`, then a _lower-bound inference_ is made from `U` to `Tb`.</span></span>

#### <a name="exact-inferences"></a><span data-ttu-id="09e46-207">Inferenza esatta</span><span class="sxs-lookup"><span data-stu-id="09e46-207">Exact inferences</span></span>

https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#exact-inferences

<span data-ttu-id="09e46-208">Il punto elenco secondario seguente viene aggiunto come caso a Bullet 2:</span><span class="sxs-lookup"><span data-stu-id="09e46-208">The following sub-bullet is added as a case to bullet 2:</span></span>

> * <span data-ttu-id="09e46-209">`V` è un tipo di puntatore a funzione `delegate*<V2..Vk, V1>` e `U` è un tipo di puntatore a funzione e `delegate*<U2..Uk, U1>` la convenzione di chiamata di `V` è identica a `U` e il refness di `Vi` è identico a `Ui` .</span><span class="sxs-lookup"><span data-stu-id="09e46-209">`V` is a function pointer type `delegate*<V2..Vk, V1>` and `U` is a function pointer type `delegate*<U2..Uk, U1>`, and the calling convention of `V` is identical to `U`, and the refness of `Vi` is identical to `Ui`.</span></span>

#### <a name="lower-bound-inferences"></a><span data-ttu-id="09e46-210">Inferenza con associazione inferiore</span><span class="sxs-lookup"><span data-stu-id="09e46-210">Lower-bound inferences</span></span>

https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#lower-bound-inferences

<span data-ttu-id="09e46-211">Il caso seguente viene aggiunto al Bullet 3:</span><span class="sxs-lookup"><span data-stu-id="09e46-211">The following case is added to bullet 3:</span></span>

> * <span data-ttu-id="09e46-212">`V` è un tipo di puntatore a funzione `delegate*<V2..Vk, V1>` ed è presente un tipo di puntatore a funzione `delegate*<U2..Uk, U1>` che `U` è identico a e `delegate*<U2..Uk, U1>` la convenzione di chiamata di `V` è identica a `U` e il refness di `Vi` è identico a `Ui` .</span><span class="sxs-lookup"><span data-stu-id="09e46-212">`V` is a function pointer type `delegate*<V2..Vk, V1>` and there is a function pointer type `delegate*<U2..Uk, U1>` such that `U` is identical to `delegate*<U2..Uk, U1>`, and the calling convention of `V` is identical to `U`, and the refness of `Vi` is identical to `Ui`.</span></span>

<span data-ttu-id="09e46-213">Il primo punto elenco di inferenza da `Ui` a `Vi` viene modificato in:</span><span class="sxs-lookup"><span data-stu-id="09e46-213">The first bullet of inference from `Ui` to `Vi` is modified to:</span></span>

> * <span data-ttu-id="09e46-214">Se `U` non è un tipo di puntatore a funzione e `Ui` non è noto come tipo di riferimento o se `U` è un tipo di puntatore a funzione e `Ui` non è noto come tipo di puntatore a funzione o come tipo di riferimento, viene eseguita un' _inferenza esatta_</span><span class="sxs-lookup"><span data-stu-id="09e46-214">If `U` is not a function pointer type and `Ui` is not known to be a reference type, or if `U` is a function pointer type and `Ui` is not known to be a function pointer type or a reference type, then an _exact inference_ is made</span></span>

<span data-ttu-id="09e46-215">Quindi, aggiunto dopo il terzo punto elenco di inferenza da `Ui` a `Vi` :</span><span class="sxs-lookup"><span data-stu-id="09e46-215">Then, added after the 3rd bullet of inference from `Ui` to `Vi`:</span></span>

> * <span data-ttu-id="09e46-216">In caso contrario, se `V` è, `delegate*<V2..Vk, V1>` l'inferenza dipende dal parametro i-th di `delegate*<V2..Vk, V1>` :</span><span class="sxs-lookup"><span data-stu-id="09e46-216">Otherwise, if `V` is `delegate*<V2..Vk, V1>` then inference depends on the i-th parameter of `delegate*<V2..Vk, V1>`:</span></span>
>    * <span data-ttu-id="09e46-217">Se V1:</span><span class="sxs-lookup"><span data-stu-id="09e46-217">If V1:</span></span>
>        * <span data-ttu-id="09e46-218">Se il valore restituito è per valore, viene eseguita un' _inferenza con associazione inferiore_ .</span><span class="sxs-lookup"><span data-stu-id="09e46-218">If the return is by value, then a _lower-bound inference_ is made.</span></span>
>        * <span data-ttu-id="09e46-219">Se il valore restituito è per riferimento, viene eseguita un' _inferenza esatta_ .</span><span class="sxs-lookup"><span data-stu-id="09e46-219">If the return is by reference, then an _exact inference_ is made.</span></span>
>    * <span data-ttu-id="09e46-220">Se V2.. VK</span><span class="sxs-lookup"><span data-stu-id="09e46-220">If V2..Vk:</span></span>
>        * <span data-ttu-id="09e46-221">Se il parametro è per valore, viene eseguita un' _inferenza con limite superiore_ .</span><span class="sxs-lookup"><span data-stu-id="09e46-221">If the parameter is by value, then an _upper-bound inference_ is made.</span></span>
>        * <span data-ttu-id="09e46-222">Se il parametro è per riferimento, viene eseguita un' _inferenza esatta_ .</span><span class="sxs-lookup"><span data-stu-id="09e46-222">If the parameter is by reference, then an _exact inference_ is made.</span></span>

#### <a name="upper-bound-inferences"></a><span data-ttu-id="09e46-223">Inferenze con associazione superiore</span><span class="sxs-lookup"><span data-stu-id="09e46-223">Upper-bound inferences</span></span>

https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#upper-bound-inferences

<span data-ttu-id="09e46-224">Il caso seguente viene aggiunto al Bullet 2:</span><span class="sxs-lookup"><span data-stu-id="09e46-224">The following case is added to bullet 2:</span></span>

> * <span data-ttu-id="09e46-225">`U` è un tipo di puntatore a funzione `delegate*<U2..Uk, U1>` e `V` è un tipo di puntatore a funzione identico a `delegate*<V2..Vk, V1>` e la convenzione di chiamata di `U` è identica a `V` e il refness di `Ui` è identico a `Vi` .</span><span class="sxs-lookup"><span data-stu-id="09e46-225">`U` is a function pointer type `delegate*<U2..Uk, U1>` and `V` is a function pointer type which is identical to `delegate*<V2..Vk, V1>`, and the calling convention of `U` is identical to `V`, and the refness of `Ui` is identical to `Vi`.</span></span>

<span data-ttu-id="09e46-226">Il primo punto elenco di inferenza da `Ui` a `Vi` viene modificato in:</span><span class="sxs-lookup"><span data-stu-id="09e46-226">The first bullet of inference from `Ui` to `Vi` is modified to:</span></span>

> * <span data-ttu-id="09e46-227">Se `U` non è un tipo di puntatore a funzione e `Ui` non è noto come tipo di riferimento o se `U` è un tipo di puntatore a funzione e `Ui` non è noto come tipo di puntatore a funzione o come tipo di riferimento, viene eseguita un' _inferenza esatta_</span><span class="sxs-lookup"><span data-stu-id="09e46-227">If `U` is not a function pointer type and `Ui` is not known to be a reference type, or if `U` is a function pointer type and `Ui` is not known to be a function pointer type or a reference type, then an _exact inference_ is made</span></span>

<span data-ttu-id="09e46-228">Aggiunto quindi dopo il terzo punto elenco di inferenza da `Ui` a `Vi` :</span><span class="sxs-lookup"><span data-stu-id="09e46-228">Then added after the 3rd bullet of inference from `Ui` to `Vi`:</span></span>

> * <span data-ttu-id="09e46-229">In caso contrario, se `U` è, `delegate*<U2..Uk, U1>` l'inferenza dipende dal parametro i-th di `delegate*<U2..Uk, U1>` :</span><span class="sxs-lookup"><span data-stu-id="09e46-229">Otherwise, if `U` is `delegate*<U2..Uk, U1>` then inference depends on the i-th parameter of `delegate*<U2..Uk, U1>`:</span></span>
>    * <span data-ttu-id="09e46-230">Se U1:</span><span class="sxs-lookup"><span data-stu-id="09e46-230">If U1:</span></span>
>        * <span data-ttu-id="09e46-231">Se il valore restituito è per valore, viene eseguita un' _inferenza con limite superiore_ .</span><span class="sxs-lookup"><span data-stu-id="09e46-231">If the return is by value, then an _upper-bound inference_ is made.</span></span>
>        * <span data-ttu-id="09e46-232">Se il valore restituito è per riferimento, viene eseguita un' _inferenza esatta_ .</span><span class="sxs-lookup"><span data-stu-id="09e46-232">If the return is by reference, then an _exact inference_ is made.</span></span>
>    * <span data-ttu-id="09e46-233">Se U2.. Regno Unito</span><span class="sxs-lookup"><span data-stu-id="09e46-233">If U2..Uk:</span></span>
>        * <span data-ttu-id="09e46-234">Se il parametro è per valore, viene eseguita un' _inferenza con associazione inferiore_ .</span><span class="sxs-lookup"><span data-stu-id="09e46-234">If the parameter is by value, then a _lower-bound inference_ is made.</span></span>
>        * <span data-ttu-id="09e46-235">Se il parametro è per riferimento, viene eseguita un' _inferenza esatta_ .</span><span class="sxs-lookup"><span data-stu-id="09e46-235">If the parameter is by reference, then an _exact inference_ is made.</span></span>

## <a name="metadata-representation-of-in-out-and-ref-readonly-parameters-and-return-types"></a><span data-ttu-id="09e46-236">Rappresentazione dei metadati `in` di `out` parametri, e `ref readonly` e tipi restituiti</span><span class="sxs-lookup"><span data-stu-id="09e46-236">Metadata representation of `in`, `out`, and `ref readonly` parameters and return types</span></span>

<span data-ttu-id="09e46-237">Le firme del puntatore a funzione non hanno un percorso di flag di parametro, pertanto è necessario codificare se i parametri e il tipo restituito sono `in` , `out` o `ref readonly` usando modreqs.</span><span class="sxs-lookup"><span data-stu-id="09e46-237">Function pointer signatures have no parameter flags location, so we must encode whether parameters and the return type are `in`, `out`, or `ref readonly` by using modreqs.</span></span>

### `in`

<span data-ttu-id="09e46-238">Riutilizziamo `System.Runtime.InteropServices.InAttribute` , applicato come oggetto `modreq` all'identificatore Ref su un parametro o un tipo restituito, per indicare quanto segue:</span><span class="sxs-lookup"><span data-stu-id="09e46-238">We reuse `System.Runtime.InteropServices.InAttribute`, applied as a `modreq` to the ref specifier on a parameter or return type, to mean the following:</span></span>
* <span data-ttu-id="09e46-239">Se applicato a un identificatore di riferimento del parametro, questo parametro viene trattato come `in` .</span><span class="sxs-lookup"><span data-stu-id="09e46-239">If applied to a parameter ref specifier, this parameter is treated as `in`.</span></span>
* <span data-ttu-id="09e46-240">Se applicato all'identificatore Ref del tipo restituito, il tipo restituito viene considerato come `ref readonly` .</span><span class="sxs-lookup"><span data-stu-id="09e46-240">If applied to the return type ref specifier, the return type is treated as `ref readonly`.</span></span>

### `out`

<span data-ttu-id="09e46-241">Si usa `System.Runtime.InteropServices.OutAttribute` , applicato come oggetto `modreq` all'identificatore Ref su un tipo di parametro, per indicare che il parametro è un `out` parametro.</span><span class="sxs-lookup"><span data-stu-id="09e46-241">We use `System.Runtime.InteropServices.OutAttribute`, applied as a `modreq` to the ref specifier on a parameter type, to mean that the parameter is an `out` parameter.</span></span>

### <a name="errors"></a><span data-ttu-id="09e46-242">Errors</span><span class="sxs-lookup"><span data-stu-id="09e46-242">Errors</span></span>

* <span data-ttu-id="09e46-243">Si tratta di un errore da applicare `OutAttribute` come modreq a un tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="09e46-243">It is an error to apply `OutAttribute` as a modreq to a return type.</span></span>
* <span data-ttu-id="09e46-244">È un errore applicare sia che `InAttribute` `OutAttribute` come modreq a un tipo di parametro.</span><span class="sxs-lookup"><span data-stu-id="09e46-244">It is an error to apply both `InAttribute` and `OutAttribute` as a modreq to a parameter type.</span></span>
* <span data-ttu-id="09e46-245">Se vengono specificati tramite modopt, verranno ignorati.</span><span class="sxs-lookup"><span data-stu-id="09e46-245">If either are specified via modopt, they are ignored.</span></span>

### <a name="metadata-representation-of-calling-conventions"></a><span data-ttu-id="09e46-246">Rappresentazione dei metadati delle convenzioni di chiamata</span><span class="sxs-lookup"><span data-stu-id="09e46-246">Metadata Representation of Calling Conventions</span></span>

<span data-ttu-id="09e46-247">Le convenzioni di chiamata vengono codificate nella firma di un metodo nei metadati mediante una combinazione del `CallKind` flag nella firma e zero o più `modopt` all'inizio della firma.</span><span class="sxs-lookup"><span data-stu-id="09e46-247">Calling conventions are encoded in a method signature in metadata by a combination of the `CallKind` flag in the signature and zero or more `modopt`s at the start of the signature.</span></span> <span data-ttu-id="09e46-248">ECMA-335 dichiara attualmente gli elementi seguenti nel `CallKind` flag:</span><span class="sxs-lookup"><span data-stu-id="09e46-248">ECMA-335 currently declares the following elements in the `CallKind` flag:</span></span>

```antlr
CallKind
   : default
   | unmanaged cdecl
   | unmanaged fastcall
   | unmanaged thiscall
   | unmanaged stdcall
   | varargs
   ;
```

<span data-ttu-id="09e46-249">Di questi, i puntatori a funzione in C# supporteranno tutti i, tranne `varargs` .</span><span class="sxs-lookup"><span data-stu-id="09e46-249">Of these, function pointers in C# will support all but `varargs`.</span></span>

<span data-ttu-id="09e46-250">Inoltre, il runtime (e infine 335) verrà aggiornato in modo da includere un nuovo `CallKind` nelle nuove piattaforme.</span><span class="sxs-lookup"><span data-stu-id="09e46-250">In addition, the runtime (and eventually 335) will be updated to include a new `CallKind` on new platforms.</span></span> <span data-ttu-id="09e46-251">Attualmente non è presente un nome formale, ma questo documento utilizzerà `unmanaged ext` come segnaposto per il nuovo formato della convenzione di chiamata estendibile.</span><span class="sxs-lookup"><span data-stu-id="09e46-251">This does not have a formal name currently, but this document will use `unmanaged ext` as a placeholder to stand for the new extensible calling convention format.</span></span> <span data-ttu-id="09e46-252">Con No `modopt` s, `unmanaged ext` è la convenzione di chiamata predefinita della piattaforma, `unmanaged` senza le parentesi quadre.</span><span class="sxs-lookup"><span data-stu-id="09e46-252">With no `modopt`s, `unmanaged ext` is the platform default calling convention, `unmanaged` without the square brackets.</span></span>

#### <a name="mapping-the-calling_convention_specifier-to-a-callkind"></a><span data-ttu-id="09e46-253">Mapping `calling_convention_specifier` di a un `CallKind`</span><span class="sxs-lookup"><span data-stu-id="09e46-253">Mapping the `calling_convention_specifier` to a `CallKind`</span></span>

<span data-ttu-id="09e46-254">Un oggetto `calling_convention_specifier` che viene omesso o specificato come `managed` , viene mappato a `default` `CallKind` .</span><span class="sxs-lookup"><span data-stu-id="09e46-254">A `calling_convention_specifier` that is omitted, or specified as `managed`, maps to the `default` `CallKind`.</span></span> <span data-ttu-id="09e46-255">Si tratta del valore predefinito `CallKind` di qualsiasi metodo senza attributi `UnmanagedCallersOnly` .</span><span class="sxs-lookup"><span data-stu-id="09e46-255">This is default `CallKind` of any method not attributed with `UnmanagedCallersOnly`.</span></span>

<span data-ttu-id="09e46-256">C# riconosce 4 identificatori speciali che vengono mappati a specifiche non gestite esistenti `CallKind` da ECMA 335.</span><span class="sxs-lookup"><span data-stu-id="09e46-256">C# recognizes 4 special identifiers that map to specific existing unmanaged `CallKind`s from ECMA 335.</span></span> <span data-ttu-id="09e46-257">Affinché questo mapping venga eseguito, è necessario specificare i relativi identificatori in modo autonomo, senza altri identificatori, e questo requisito viene codificato nelle specifiche per `unmanaged_calling_convention` s.</span><span class="sxs-lookup"><span data-stu-id="09e46-257">In order for this mapping to occur, these identifiers must be specified on their own, with no other identifiers, and this requirement is encoded into the spec for `unmanaged_calling_convention`s.</span></span> <span data-ttu-id="09e46-258">Questi identificatori sono `Cdecl` , `Thiscall` , `Stdcall` e `Fastcall` , che corrispondono rispettivamente a `unmanaged cdecl` , `unmanaged thiscall` , `unmanaged stdcall` e `unmanaged fastcall` .</span><span class="sxs-lookup"><span data-stu-id="09e46-258">These identifiers are `Cdecl`, `Thiscall`, `Stdcall`, and `Fastcall`, which correspond to `unmanaged cdecl`, `unmanaged thiscall`, `unmanaged stdcall`, and `unmanaged fastcall`, respectively.</span></span> <span data-ttu-id="09e46-259">Se viene specificato più di un `identifer` oggetto o se il singolo `identifier` non è costituito dagli identificatori specifici, viene eseguita la ricerca di nomi speciali sull'identificatore con le regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="09e46-259">If more than one `identifer` is specified, or the single `identifier` is not of the specially recognized identifiers, we perform special name lookup on the identifier with the following rules:</span></span>

* <span data-ttu-id="09e46-260">Viene anteposto l'oggetto `identifier` con la stringa `CallConv`</span><span class="sxs-lookup"><span data-stu-id="09e46-260">We prepend the `identifier` with the string `CallConv`</span></span>
* <span data-ttu-id="09e46-261">Si osserveranno solo i tipi definiti nello `System.Runtime.CompilerServices` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="09e46-261">We look only at types defined in the `System.Runtime.CompilerServices` namespace.</span></span>
* <span data-ttu-id="09e46-262">Si osserveranno solo i tipi definiti nella libreria principale dell'applicazione, ovvero la libreria che definisce `System.Object` e non ha dipendenze.</span><span class="sxs-lookup"><span data-stu-id="09e46-262">We look only at types defined in the core library of the application, which is the library that defines `System.Object` and has no dependencies.</span></span>
* <span data-ttu-id="09e46-263">Vengono esaminati solo i tipi pubblici.</span><span class="sxs-lookup"><span data-stu-id="09e46-263">We look only at public types.</span></span>

<span data-ttu-id="09e46-264">Se la ricerca ha esito positivo su tutti gli oggetti `identifier` specificati in un oggetto `unmanaged_calling_convention` , viene codificato `CallKind` come `unmanaged ext` e vengono codificati tutti i tipi risolti nel set di oggetti `modopt` all'inizio della firma del puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="09e46-264">If lookup succeeds on all of the `identifier`s specified in an `unmanaged_calling_convention`, we encode the `CallKind` as `unmanaged ext`, and encode each of the resolved types in the set of `modopt`s at the beginning of the function pointer signature.</span></span> <span data-ttu-id="09e46-265">Si noti che queste regole indicano che gli utenti non possono precedere questi `identifier` con `CallConv` , perché ciò comporta la ricerca `CallConvCallConvVectorCall` .</span><span class="sxs-lookup"><span data-stu-id="09e46-265">As a note, these rules mean that users cannot prefix these `identifier`s with `CallConv`, as that will result in looking up `CallConvCallConvVectorCall`.</span></span>

<span data-ttu-id="09e46-266">Quando si interpretano i metadati, si esamina prima di tutto `CallKind` .</span><span class="sxs-lookup"><span data-stu-id="09e46-266">When interpreting metadata, we first look at the `CallKind`.</span></span> <span data-ttu-id="09e46-267">Se si tratta di un valore diverso da `unmanaged ext` , si ignoreranno tutti i `modopt` nel tipo restituito ai fini della determinazione della convenzione di chiamata e si utilizzerà solo `CallKind` .</span><span class="sxs-lookup"><span data-stu-id="09e46-267">If it is anything other than `unmanaged ext`, we ignore all `modopt`s on the return type for the purposes of determining the calling convention, and use only the `CallKind`.</span></span> <span data-ttu-id="09e46-268">Se `CallKind` è `unmanaged ext` , si esamina il modopts all'inizio del tipo di puntatore a funzione, prendendo l'Unione di tutti i tipi che soddisfano i requisiti seguenti:</span><span class="sxs-lookup"><span data-stu-id="09e46-268">If the `CallKind` is `unmanaged ext`, we look at the modopts at the start of the function pointer type, taking the union of all types that meet the following requirements:</span></span>

* <span data-ttu-id="09e46-269">L'oggetto è definito nella libreria principale, ovvero la libreria che fa riferimento ad altre librerie e non definisce `System.Object` .</span><span class="sxs-lookup"><span data-stu-id="09e46-269">The is defined in the core library, which is the library that references no other libraries and defines `System.Object`.</span></span>
* <span data-ttu-id="09e46-270">Il tipo è definito nello `System.Runtime.CompilerServices` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="09e46-270">The type is defined in the `System.Runtime.CompilerServices` namespace.</span></span>
* <span data-ttu-id="09e46-271">Il tipo inizia con il prefisso `CallConv` .</span><span class="sxs-lookup"><span data-stu-id="09e46-271">The type starts with the prefix `CallConv`.</span></span>
* <span data-ttu-id="09e46-272">Il tipo è public.</span><span class="sxs-lookup"><span data-stu-id="09e46-272">The type is public.</span></span>

 <span data-ttu-id="09e46-273">Rappresentano i tipi che devono essere trovati quando si esegue la ricerca in `identifier` un oggetto `unmanaged_calling_convention` quando si definisce un tipo di puntatore a funzione nell'origine.</span><span class="sxs-lookup"><span data-stu-id="09e46-273">These represent the types that must be found when performing lookup on the `identifier`s in an `unmanaged_calling_convention` when defining a function pointer type in source.</span></span>

<span data-ttu-id="09e46-274">Non è possibile tentare di usare un puntatore a funzione con un valore `CallKind` di `unmanaged ext` se il runtime di destinazione non supporta la funzionalità.</span><span class="sxs-lookup"><span data-stu-id="09e46-274">It is an error to attempt to use a function pointer with a `CallKind` of `unmanaged ext` if the target runtime does not support the feature.</span></span> <span data-ttu-id="09e46-275">Questa operazione verrà determinata cercando la presenza della `System.Runtime.CompilerServices.RuntimeFeature.UnmanagedCallKind` costante.</span><span class="sxs-lookup"><span data-stu-id="09e46-275">This will be determined by looking for the presence of the `System.Runtime.CompilerServices.RuntimeFeature.UnmanagedCallKind` constant.</span></span> <span data-ttu-id="09e46-276">Se questa costante è presente, il runtime viene considerato come supporto della funzionalità.</span><span class="sxs-lookup"><span data-stu-id="09e46-276">If this constant is present, the runtime is considered to support the feature.</span></span>

### `System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute`

<span data-ttu-id="09e46-277">`System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute` attributo utilizzato da CLR per indicare che un metodo deve essere chiamato con una convenzione di chiamata specifica.</span><span class="sxs-lookup"><span data-stu-id="09e46-277">`System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute` is an attribute used by the CLR to indicate that a method should be called with a specific calling convention.</span></span> <span data-ttu-id="09e46-278">Per questo motivo, viene introdotto il supporto seguente per l'utilizzo dell'attributo:</span><span class="sxs-lookup"><span data-stu-id="09e46-278">Because of this, we introduce the following support for working with the attribute:</span></span>

* <span data-ttu-id="09e46-279">È un errore chiamare direttamente un metodo annotato con questo attributo da C#.</span><span class="sxs-lookup"><span data-stu-id="09e46-279">It is an error to directly call a method annotated with this attribute from C#.</span></span> <span data-ttu-id="09e46-280">Gli utenti devono ottenere un puntatore a funzione per il metodo e quindi richiamare il puntatore.</span><span class="sxs-lookup"><span data-stu-id="09e46-280">Users must obtain a function pointer to the method and then invoke that pointer.</span></span>
* <span data-ttu-id="09e46-281">Non è possibile applicare l'attributo a qualsiasi elemento diverso da un metodo statico comune o da una normale funzione locale statica.</span><span class="sxs-lookup"><span data-stu-id="09e46-281">It is an error to apply the attribute to anything other than an ordinary static method or ordinary static local function.</span></span>
<span data-ttu-id="09e46-282">Il compilatore C# contrassegnerà tutti i metodi non statici o statici non ordinari importati dai metadati con questo attributo come non supportati dal linguaggio.</span><span class="sxs-lookup"><span data-stu-id="09e46-282">The C# compiler will mark any non-static or static non-ordinary methods imported from metadata with this attribute as unsupported by the language.</span></span>
* <span data-ttu-id="09e46-283">È un errore per un metodo contrassegnato con l'attributo per avere un parametro o un tipo restituito diverso da `unmanaged_type` .</span><span class="sxs-lookup"><span data-stu-id="09e46-283">It is an error for a method marked with the attribute to have a parameter or return type that is not an `unmanaged_type`.</span></span>
* <span data-ttu-id="09e46-284">Si tratta di un errore per un metodo contrassegnato con l'attributo con parametri di tipo, anche se tali parametri di tipo sono limitati a `unmanaged` .</span><span class="sxs-lookup"><span data-stu-id="09e46-284">It is an error for a method marked with the attribute to have type parameters, even if those type parameters are constrained to `unmanaged`.</span></span>
* <span data-ttu-id="09e46-285">Non è possibile contrassegnare un metodo in un tipo generico con l'attributo.</span><span class="sxs-lookup"><span data-stu-id="09e46-285">It is an error for a method in a generic type to be marked with the attribute.</span></span>
* <span data-ttu-id="09e46-286">È un errore convertire un metodo contrassegnato con l'attributo in un tipo delegato.</span><span class="sxs-lookup"><span data-stu-id="09e46-286">It is an error to convert a method marked with the attribute to a delegate type.</span></span>
* <span data-ttu-id="09e46-287">Non è un errore specificare i tipi per `UnmanagedCallersOnly.CallConvs` che non soddisfano i requisiti per la chiamata di Convention `modopt` s nei metadati.</span><span class="sxs-lookup"><span data-stu-id="09e46-287">It is an error to specify any types for `UnmanagedCallersOnly.CallConvs` that do not meet the requirements for calling convention `modopt`s in metadata.</span></span>

<span data-ttu-id="09e46-288">Quando si determina la convenzione di chiamata di un metodo contrassegnato con un `UnmanagedCallersOnly` attributo valido, il compilatore esegue i controlli seguenti sui tipi specificati nella `CallConvs` proprietà per determinare gli effettivi `CallKind` e `modopt` che devono essere utilizzati per determinare la convenzione di chiamata:</span><span class="sxs-lookup"><span data-stu-id="09e46-288">When determining the calling convention of a method marked with a valid `UnmanagedCallersOnly` attribute, the compiler performs the following checks on the types specified in the `CallConvs` property to determine the effective `CallKind` and `modopt`s that should be used to determine the calling convention:</span></span>

* <span data-ttu-id="09e46-289">Se non viene specificato alcun tipo, `CallKind` viene considerato come `unmanaged ext` , senza alcuna convenzione `modopt` di chiamata all'inizio del tipo di puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="09e46-289">If no types are specified, the `CallKind` is treated as `unmanaged ext`, with no calling convention `modopt`s at the start of the function pointer type.</span></span>
* <span data-ttu-id="09e46-290">Se è stato specificato un tipo e tale tipo è denominato `CallConvCdecl` ,, `CallConvThiscall` `CallConvStdcall` o `CallConvFastcall` , l'oggetto `CallKind` viene trattato come,, `unmanaged cdecl` `unmanaged thiscall` `unmanaged stdcall` o `unmanaged fastcall` , rispettivamente, senza convenzione di chiamata `modopt` all'inizio del tipo di puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="09e46-290">If there is one type specified, and that type is named `CallConvCdecl`, `CallConvThiscall`, `CallConvStdcall`, or `CallConvFastcall`, the `CallKind` is treated as `unmanaged cdecl`, `unmanaged thiscall`, `unmanaged stdcall`, or `unmanaged fastcall`, respectively, with no calling convention `modopt`s at the start of the function pointer type.</span></span>
* <span data-ttu-id="09e46-291">Se vengono specificati più tipi o se il tipo singolo non è denominato uno dei tipi indicati in precedenza, `CallKind` viene considerato come `unmanaged ext` , con l'Unione dei tipi specificati come `modopt` s all'inizio del tipo di puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="09e46-291">If multiple types are specified or the single type is not named one of the specially called out types above, the `CallKind` is treated as `unmanaged ext`, with the union of the types specified treated as `modopt`s at the start of the function pointer type.</span></span>

<span data-ttu-id="09e46-292">Il compilatore esamina quindi questo effetto `CallKind` e la `modopt` raccolta e usa le normali regole di metadati per determinare la convenzione di chiamata finale del tipo di puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="09e46-292">The compiler then looks at this effective `CallKind` and `modopt` collection and uses normal metadata rules to determine the final calling convention of the function pointer type.</span></span>

## <a name="open-questions"></a><span data-ttu-id="09e46-293">Domande aperte</span><span class="sxs-lookup"><span data-stu-id="09e46-293">Open Questions</span></span>

### <a name="detecting-runtime-support-for-unmanaged-ext"></a><span data-ttu-id="09e46-294">Rilevamento del supporto runtime per `unmanaged ext`</span><span class="sxs-lookup"><span data-stu-id="09e46-294">Detecting runtime support for `unmanaged ext`</span></span>

<span data-ttu-id="09e46-295">https://github.com/dotnet/runtime/issues/38135 tiene traccia dell'aggiunta di questo flag.</span><span class="sxs-lookup"><span data-stu-id="09e46-295">https://github.com/dotnet/runtime/issues/38135 tracks adding this flag.</span></span> <span data-ttu-id="09e46-296">A seconda dei commenti e suggerimenti della revisione, si utilizzerà la proprietà specificata nel problema oppure si utilizzerà la presenza di `UnmanagedCallersOnlyAttribute` come flag che determina se i runtime supportano `unmanaged ext` .</span><span class="sxs-lookup"><span data-stu-id="09e46-296">Depending on the feedback from review, we will either use the property specified in the issue, or use the presence of `UnmanagedCallersOnlyAttribute` as the flag that determines whether the runtimes supports `unmanaged ext`.</span></span>

## <a name="considerations"></a><span data-ttu-id="09e46-297">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="09e46-297">Considerations</span></span>

### <a name="allow-instance-methods"></a><span data-ttu-id="09e46-298">Consenti metodi di istanza</span><span class="sxs-lookup"><span data-stu-id="09e46-298">Allow instance methods</span></span>

<span data-ttu-id="09e46-299">È possibile estendere la proposta per supportare i metodi di istanza sfruttando la convenzione di chiamata dell'interfaccia della riga di comando `EXPLICITTHIS` (denominata `instance` nel codice C#).</span><span class="sxs-lookup"><span data-stu-id="09e46-299">The proposal could be extended to support instance methods by taking advantage of the `EXPLICITTHIS` CLI calling convention (named `instance` in C# code).</span></span> <span data-ttu-id="09e46-300">Questa forma di puntatori a funzione dell'interfaccia della riga di comando inserisce il `this` parametro come primo parametro esplicito della sintassi del puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="09e46-300">This form of CLI function pointers puts the `this` parameter as an explicit first parameter of the function pointer syntax.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        delegate* instance<Instance, string> f = &ToString;
        f(this);
    }
}
```

<span data-ttu-id="09e46-301">Si tratta di un suono che aggiunge alcune complicazioni alla proposta.</span><span class="sxs-lookup"><span data-stu-id="09e46-301">This is sound but adds some complication to the proposal.</span></span> <span data-ttu-id="09e46-302">In particolare perché i puntatori a funzione che differiscono dalla convenzione di chiamata `instance` e `managed` sarebbero incompatibili anche se entrambi i casi vengono usati per richiamare i metodi gestiti con la stessa firma C#.</span><span class="sxs-lookup"><span data-stu-id="09e46-302">Particularly because function pointers which differed by the calling convention `instance` and `managed` would be incompatible even though both cases are used to invoke managed methods with the same C# signature.</span></span> <span data-ttu-id="09e46-303">Inoltre, in ogni caso, si considera utile la presenza di una semplice soluzione: usare una `static` funzione locale.</span><span class="sxs-lookup"><span data-stu-id="09e46-303">Also in every case considered where this would be valuable to have there was a simple work around: use a `static` local function.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        static string toString(Instance i) => i.ToString();
        delegate*<Instance, string> f = &toString;
        f(this);
    }
}
```

### <a name="dont-require-unsafe-at-declaration"></a><span data-ttu-id="09e46-304">Non richiedere unsafe alla dichiarazione</span><span class="sxs-lookup"><span data-stu-id="09e46-304">Don't require unsafe at declaration</span></span>

<span data-ttu-id="09e46-305">Anziché richiedere `unsafe` a ogni utilizzo di un `delegate*` , richiederlo solo nel punto in cui un gruppo di metodi viene convertito in un oggetto `delegate*` .</span><span class="sxs-lookup"><span data-stu-id="09e46-305">Instead of requiring `unsafe` at every use of a `delegate*`, only require it at the point where a method group is converted to a `delegate*`.</span></span> <span data-ttu-id="09e46-306">Questo è il punto in cui vengono rilevati i problemi di sicurezza di base (sapendo che l'assembly contenitore non può essere scaricato mentre il valore è attivo).</span><span class="sxs-lookup"><span data-stu-id="09e46-306">This is where the core safety issues come into play (knowing that the containing assembly cannot be unloaded while the value is alive).</span></span> <span data-ttu-id="09e46-307">La richiesta `unsafe` di altre posizioni può essere considerata eccessiva.</span><span class="sxs-lookup"><span data-stu-id="09e46-307">Requiring `unsafe` on the other locations can be seen as excessive.</span></span>

<span data-ttu-id="09e46-308">Questo è il modo in cui la progettazione è stata originariamente progettata.</span><span class="sxs-lookup"><span data-stu-id="09e46-308">This is how the design was originally intended.</span></span> <span data-ttu-id="09e46-309">Ma le regole del linguaggio risultavano molto scomode.</span><span class="sxs-lookup"><span data-stu-id="09e46-309">But the resulting language rules felt very awkward.</span></span> <span data-ttu-id="09e46-310">Non è possibile nascondere il fatto che si tratta di un valore di puntatore che continua a leggere anche senza la `unsafe` parola chiave.</span><span class="sxs-lookup"><span data-stu-id="09e46-310">It's impossible to hide the fact that this is a pointer value and it kept peeking through even without the `unsafe` keyword.</span></span> <span data-ttu-id="09e46-311">Ad esempio, la conversione a `object` non può essere consentita, non può essere un membro di `class` , e così via. Il progetto C# è necessario `unsafe` per tutti gli utilizzi del puntatore e pertanto questa progettazione lo segue.</span><span class="sxs-lookup"><span data-stu-id="09e46-311">For example the conversion to `object` can't be allowed, it can't be a member of a `class`, etc ... The C# design is to require `unsafe` for all pointer uses and hence this design follows that.</span></span>

<span data-ttu-id="09e46-312">Gli sviluppatori saranno comunque in grado di presentare un wrapper _sicuro_ sopra i `delegate*` valori nello stesso modo in cui fanno oggi i normali tipi di puntatore.</span><span class="sxs-lookup"><span data-stu-id="09e46-312">Developers will still be capable of presenting a _safe_ wrapper on top of `delegate*` values the same way that they do for normal pointer types today.</span></span> <span data-ttu-id="09e46-313">Tenere in considerazione:</span><span class="sxs-lookup"><span data-stu-id="09e46-313">Consider:</span></span>

``` csharp
unsafe struct Action {
    delegate*<void> _ptr;

    Action(delegate*<void> ptr) => _ptr = ptr;
    public void Invoke() => _ptr();
}
```

### <a name="using-delegates"></a><span data-ttu-id="09e46-314">Uso di delegati</span><span class="sxs-lookup"><span data-stu-id="09e46-314">Using delegates</span></span>

<span data-ttu-id="09e46-315">Invece di usare un nuovo elemento della sintassi, `delegate*` , è sufficiente usare `delegate` i tipi esistenti con `*` il tipo seguente:</span><span class="sxs-lookup"><span data-stu-id="09e46-315">Instead of using a new syntax element, `delegate*`, simply use existing `delegate` types with a `*` following the type:</span></span>

``` csharp
Func<object, object, bool>* ptr = &object.ReferenceEquals;
```

<span data-ttu-id="09e46-316">La gestione della convenzione di chiamata può essere eseguita annotando i `delegate` tipi con un attributo che specifica un `CallingConvention` valore.</span><span class="sxs-lookup"><span data-stu-id="09e46-316">Handling calling convention can be done by annotating the `delegate` types with an attribute that specifies a `CallingConvention` value.</span></span> <span data-ttu-id="09e46-317">La mancanza di un attributo significa la convenzione di chiamata gestita.</span><span class="sxs-lookup"><span data-stu-id="09e46-317">The lack of an attribute would signify the managed calling convention.</span></span>

<span data-ttu-id="09e46-318">La codifica in IL è problematica.</span><span class="sxs-lookup"><span data-stu-id="09e46-318">Encoding this in IL is problematic.</span></span> <span data-ttu-id="09e46-319">Il valore sottostante deve essere rappresentato come puntatore, ma deve anche:</span><span class="sxs-lookup"><span data-stu-id="09e46-319">The underlying value needs to be represented as a pointer yet it also must:</span></span>

1. <span data-ttu-id="09e46-320">Hanno un tipo univoco per consentire gli overload con tipi di puntatore a funzione diversi.</span><span class="sxs-lookup"><span data-stu-id="09e46-320">Have a unique type to allow for overloads with different function pointer types.</span></span>
1. <span data-ttu-id="09e46-321">Essere equivalente a scopi OHI tra i limiti di assembly.</span><span class="sxs-lookup"><span data-stu-id="09e46-321">Be equivalent for OHI purposes across assembly boundaries.</span></span>

<span data-ttu-id="09e46-322">L'ultimo punto è particolarmente problematico.</span><span class="sxs-lookup"><span data-stu-id="09e46-322">The last point is particularly problematic.</span></span> <span data-ttu-id="09e46-323">Ciò significa che ogni assembly utilizzato da `Func<int>*` deve codificare un tipo equivalente nei metadati anche se `Func<int>*` è definito in un assembly, sebbene non controlli.</span><span class="sxs-lookup"><span data-stu-id="09e46-323">This mean that every assembly which uses `Func<int>*` must encode an equivalent type in metadata even though `Func<int>*` is defined in an assembly though don't control.</span></span>
<span data-ttu-id="09e46-324">Inoltre, tutti gli altri tipi definiti con il nome `System.Func<T>` in un assembly che non è mscorlib devono essere diversi dalla versione definita in mscorlib.</span><span class="sxs-lookup"><span data-stu-id="09e46-324">Additionally any other type which is defined with the name `System.Func<T>` in an assembly that is not mscorlib must be different than the version defined in mscorlib.</span></span>

<span data-ttu-id="09e46-325">Una delle opzioni Esplorate è stata la creazione di un puntatore di questo tipo come `mod_req(Func<int>) void*` .</span><span class="sxs-lookup"><span data-stu-id="09e46-325">One option that was explored was emitting such a pointer as `mod_req(Func<int>) void*`.</span></span> <span data-ttu-id="09e46-326">Questa operazione non funziona anche se `mod_req` non è possibile eseguire l'associazione a un oggetto `TypeSpec` e pertanto non è possibile fare riferimento a istanze generiche.</span><span class="sxs-lookup"><span data-stu-id="09e46-326">This doesn't work though as a `mod_req` cannot bind to a `TypeSpec` and hence cannot target generic instantiations.</span></span>

### <a name="named-function-pointers"></a><span data-ttu-id="09e46-327">Puntatori a funzioni denominate</span><span class="sxs-lookup"><span data-stu-id="09e46-327">Named function pointers</span></span>

<span data-ttu-id="09e46-328">La sintassi del puntatore a funzione può essere complessa, soprattutto in casi complessi come i puntatori a funzioni annidate.</span><span class="sxs-lookup"><span data-stu-id="09e46-328">The function pointer syntax can be cumbersome, particularly in complex cases like nested function pointers.</span></span> <span data-ttu-id="09e46-329">Anziché fare in modo che gli sviluppatori digitino la firma ogni volta che la lingua potrebbe consentire le dichiarazioni denominate dei puntatori a funzione, come avviene con `delegate` .</span><span class="sxs-lookup"><span data-stu-id="09e46-329">Rather than have developers type out the signature every time the language could allow for named declarations of function pointers as is done with `delegate`.</span></span>

``` csharp
func* void Action();

unsafe class NamedExample {
    void M(Action a) {
        a();
    }
}
```

<span data-ttu-id="09e46-330">Una parte del problema è che la primitiva dell'interfaccia della riga di comando sottostante non ha nomi, pertanto si tratterebbe puramente di un'invenzione in C# e richiederebbe un po' di lavoro di metadati da abilitare.</span><span class="sxs-lookup"><span data-stu-id="09e46-330">Part of the problem here is the underlying CLI primitive doesn't have names hence this would be purely a C# invention and require a bit of metadata work to enable.</span></span> <span data-ttu-id="09e46-331">Questa operazione è fattibile, ma è una questione significativa di lavoro.</span><span class="sxs-lookup"><span data-stu-id="09e46-331">That is doable but is a significant about of work.</span></span> <span data-ttu-id="09e46-332">In sostanza, è necessario che C# disponga di un complementare alla tabella def del tipo esclusivamente per questi nomi.</span><span class="sxs-lookup"><span data-stu-id="09e46-332">It essentially requires C# to have a companion to the type def table purely for these names.</span></span>

<span data-ttu-id="09e46-333">Inoltre, quando sono stati esaminati gli argomenti per i puntatori a funzione denominati, è possibile che si applichino ugualmente a diversi altri scenari.</span><span class="sxs-lookup"><span data-stu-id="09e46-333">Also when the arguments for named function pointers were examined we found they could apply equally well to a number of other scenarios.</span></span> <span data-ttu-id="09e46-334">Ad esempio, sarebbe altrettanto semplice dichiarare le tuple denominate per ridurre la necessità di digitare la firma completa in tutti i casi.</span><span class="sxs-lookup"><span data-stu-id="09e46-334">For example it would be just as convenient to declare named tuples to reduce the need to type out the full signature in all cases.</span></span>

``` csharp
(int x, int y) Point;

class NamedTupleExample {
    void M(Point p) {
        Console.WriteLine(p.x);
    }
}
```

<span data-ttu-id="09e46-335">Dopo la discussione si è deciso di non consentire la dichiarazione denominata dei `delegate*` tipi.</span><span class="sxs-lookup"><span data-stu-id="09e46-335">After discussion we decided to not allow named declaration of `delegate*` types.</span></span> <span data-ttu-id="09e46-336">Se viene rilevata una necessità significativa in base ai commenti e suggerimenti sull'utilizzo dei clienti, viene esaminata una soluzione di denominazione che funziona per i puntatori a funzione, le tuple, i generics e così via. È probabile che questo aspetto sia simile a quello di altri suggerimenti `typedef` , come il supporto completo nel linguaggio.</span><span class="sxs-lookup"><span data-stu-id="09e46-336">If we find there is significant need for this based on customer usage feedback then we will investigate a naming solution that works for function pointers, tuples, generics, etc ... This is likely to be similar in form to other suggestions like full `typedef` support in the language.</span></span>

## <a name="future-considerations"></a><span data-ttu-id="09e46-337">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="09e46-337">Future Considerations</span></span>

### <a name="static-delegates"></a><span data-ttu-id="09e46-338">delegati statici</span><span class="sxs-lookup"><span data-stu-id="09e46-338">static delegates</span></span>

<span data-ttu-id="09e46-339">Si fa riferimento alla [proposta](https://github.com/dotnet/csharplang/issues/302) per consentire la dichiarazione di `delegate` tipi che possono fare riferimento solo ai `static` membri.</span><span class="sxs-lookup"><span data-stu-id="09e46-339">This refers to [the proposal](https://github.com/dotnet/csharplang/issues/302) to allow for the declaration of `delegate` types which can only refer to `static` members.</span></span> <span data-ttu-id="09e46-340">Il vantaggio è che tali `delegate` istanze possono essere allocazione gratuite e migliori negli scenari sensibili alle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="09e46-340">The advantage being that such `delegate` instances can be allocation free and better in performance sensitive scenarios.</span></span>

<span data-ttu-id="09e46-341">Se la funzionalità del puntatore a funzione è implementata `static delegate` , la proposta verrà probabilmente chiusa. Il vantaggio proposto da questa funzionalità è la natura di allocazione.</span><span class="sxs-lookup"><span data-stu-id="09e46-341">If the function pointer feature is implemented the `static delegate` proposal will likely be closed out. The proposed advantage of that feature is the allocation free nature.</span></span> <span data-ttu-id="09e46-342">Sono state rilevate tuttavia ricerche recenti che non è possibile ottenere a causa dello scaricamento di assembly.</span><span class="sxs-lookup"><span data-stu-id="09e46-342">However recent investigations have found that is not possible to achieve due to assembly unloading.</span></span> <span data-ttu-id="09e46-343">È necessario che sia presente un handle sicuro da `static delegate` al metodo a cui si riferisce per evitare che l'assembly venga scaricato dal sottoinsieme.</span><span class="sxs-lookup"><span data-stu-id="09e46-343">There must be a strong handle from the `static delegate` to the method it refers to in order to keep the assembly from being unloaded out from under it.</span></span>

<span data-ttu-id="09e46-344">Per mantenere ogni `static delegate` istanza è necessario allocare un nuovo handle che esegue il contatore agli obiettivi della proposta.</span><span class="sxs-lookup"><span data-stu-id="09e46-344">To maintain every `static delegate` instance would be required to allocate a new handle which runs counter to the goals of the proposal.</span></span> <span data-ttu-id="09e46-345">Sono stati creati alcuni progetti in cui l'allocazione può essere ammortizzata in una singola allocazione per ogni chiamata, ma si tratta di un po' complesso che non sembra essere il compromesso.</span><span class="sxs-lookup"><span data-stu-id="09e46-345">There were some designs where the allocation could be amortized to a single allocation per call-site but that was a bit complex and didn't seem worth the trade off.</span></span>

<span data-ttu-id="09e46-346">Ciò significa che gli sviluppatori devono essenzialmente decidere tra i compromessi seguenti:</span><span class="sxs-lookup"><span data-stu-id="09e46-346">That means developers essentially have to decide between the following trade offs:</span></span>

1. <span data-ttu-id="09e46-347">Sicurezza in caso di scaricamento di assembly: è necessario allocazioni e pertanto `delegate` è già un'opzione sufficiente.</span><span class="sxs-lookup"><span data-stu-id="09e46-347">Safety in the face of assembly unloading: this requires allocations and hence `delegate` is already a sufficient option.</span></span>
1. <span data-ttu-id="09e46-348">Nessun aspetto di sicurezza in caso di scaricamento dell'assembly: usare un `delegate*` .</span><span class="sxs-lookup"><span data-stu-id="09e46-348">No safety in face of assembly unloading: use a `delegate*`.</span></span> <span data-ttu-id="09e46-349">Questo può essere incluso in un oggetto `struct` per consentire l'utilizzo all'esterno `unsafe` di un contesto nel resto del codice.</span><span class="sxs-lookup"><span data-stu-id="09e46-349">This can be wrapped in a `struct` to allow usage outside an `unsafe` context in the rest of the code.</span></span>
