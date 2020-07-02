---
ms.openlocfilehash: 6bf0b29d16297ae5201d9a7773fc9cb95a47f9cb
ms.sourcegitcommit: 1bb454804d017a9ca18c5de47737dd1d68ce3eea
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85835143"
---
# <a name="function-pointers"></a><span data-ttu-id="62d43-101">Puntatori a funzione</span><span class="sxs-lookup"><span data-stu-id="62d43-101">Function Pointers</span></span>

## <a name="summary"></a><span data-ttu-id="62d43-102">Summary</span><span class="sxs-lookup"><span data-stu-id="62d43-102">Summary</span></span>

<span data-ttu-id="62d43-103">Questa proposta fornisce costrutti di linguaggio che espongono i codici operativi IL al quale attualmente non è possibile accedere in modo efficiente, o, in C#: `ldftn` e `calli` .</span><span class="sxs-lookup"><span data-stu-id="62d43-103">This proposal provides language constructs that expose IL opcodes that cannot currently be accessed efficiently, or at all, in C# today: `ldftn` and `calli`.</span></span> <span data-ttu-id="62d43-104">Questi codici operativi IL può essere importante nel codice a prestazioni elevate e gli sviluppatori hanno bisogno di un modo efficiente per accedervi.</span><span class="sxs-lookup"><span data-stu-id="62d43-104">These IL opcodes can be important in high performance code and developers need an efficient way to access them.</span></span>

## <a name="motivation"></a><span data-ttu-id="62d43-105">Motivazione</span><span class="sxs-lookup"><span data-stu-id="62d43-105">Motivation</span></span>

<span data-ttu-id="62d43-106">Le motivazioni e lo sfondo per questa funzionalità sono descritti nel seguente problema (come è una potenziale implementazione della funzionalità):</span><span class="sxs-lookup"><span data-stu-id="62d43-106">The motivations and background for this feature are described in the following issue (as is a potential implementation of the feature):</span></span>

https://github.com/dotnet/csharplang/issues/191

<span data-ttu-id="62d43-107">Si tratta di una proposta di progettazione alternativa alle [funzioni intrinseche del compilatore](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)</span><span class="sxs-lookup"><span data-stu-id="62d43-107">This is an alternate design proposal to [compiler intrinsics](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)</span></span>

## <a name="detailed-design"></a><span data-ttu-id="62d43-108">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="62d43-108">Detailed Design</span></span>

### <a name="function-pointers"></a><span data-ttu-id="62d43-109">Puntatori funzione</span><span class="sxs-lookup"><span data-stu-id="62d43-109">Function pointers</span></span>

<span data-ttu-id="62d43-110">Il linguaggio consentirà la dichiarazione di puntatori a funzione usando la `delegate*` sintassi.</span><span class="sxs-lookup"><span data-stu-id="62d43-110">The language will allow for the declaration of function pointers using the `delegate*` syntax.</span></span> <span data-ttu-id="62d43-111">La sintassi completa viene descritta in dettaglio nella sezione successiva, ma è concepita come la sintassi utilizzata dalle dichiarazioni di `Func` `Action` tipo e.</span><span class="sxs-lookup"><span data-stu-id="62d43-111">The full syntax is described in detail in the next section but it is meant to resemble the syntax used by `Func` and `Action` type declarations.</span></span>

``` csharp
unsafe class Example {
    void Example(Action<int> a, delegate*<int, void> f) {
        a(42);
        f(42);
    }
}
```

<span data-ttu-id="62d43-112">Questi tipi sono rappresentati usando il tipo di puntatore a funzione, come descritto in ECMA-335.</span><span class="sxs-lookup"><span data-stu-id="62d43-112">These types are represented using the function pointer type as outlined in ECMA-335.</span></span> <span data-ttu-id="62d43-113">Ciò significa che la chiamata di un oggetto utilizzerà la `delegate*` `calli` posizione in cui la chiamata di un oggetto `delegate` verrà utilizzata `callvirt` nel `Invoke` metodo.</span><span class="sxs-lookup"><span data-stu-id="62d43-113">This means invocation of a `delegate*` will use `calli` where invocation of a `delegate` will use `callvirt` on the `Invoke` method.</span></span>
<span data-ttu-id="62d43-114">Sintatticamente, sebbene la chiamata sia identica per entrambi i costrutti.</span><span class="sxs-lookup"><span data-stu-id="62d43-114">Syntactically though invocation is identical for both constructs.</span></span>

<span data-ttu-id="62d43-115">La definizione ECMA-335 dei puntatori al metodo include la convenzione di chiamata come parte della firma del tipo (sezione 7,1).</span><span class="sxs-lookup"><span data-stu-id="62d43-115">The ECMA-335 definition of method pointers includes the calling convention as part of the type signature (section 7.1).</span></span>
<span data-ttu-id="62d43-116">La convenzione di chiamata predefinita sarà `managed` .</span><span class="sxs-lookup"><span data-stu-id="62d43-116">The default calling convention will be `managed`.</span></span> <span data-ttu-id="62d43-117">È possibile specificare le convenzioni di chiamata non gestite inserendo una `unmanaged` parola chiave dopo la `delegate*` sintassi, che utilizzerà l'impostazione predefinita della piattaforma di Runtime.</span><span class="sxs-lookup"><span data-stu-id="62d43-117">Unmanaged calling conventions can by specified by putting an `unmanaged` keyword afer the `delegate*` syntax, which will use the runtime platform default.</span></span> <span data-ttu-id="62d43-118">È quindi possibile specificare convenzioni non gestite specifiche tra parentesi quadre per la `unmanaged` parola chiave specificando qualsiasi tipo che inizia con `CallConv` nello `System.Runtime.CompilerServices` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="62d43-118">Specific unmanaged conventions can then be specified in brackets to the `unmanaged` keyword by specifying any type starting with `CallConv` in the `System.Runtime.CompilerServices` namespace.</span></span> <span data-ttu-id="62d43-119">Questi tipi devono provenire dalla libreria principale del programma e il set di combinazioni valide è dipendente dalla piattaforma.</span><span class="sxs-lookup"><span data-stu-id="62d43-119">These types must come from the program's core library, and the set of valid combinations is platform-dependent.</span></span>

``` csharp
//This method has a managed calling convention. This is the same as leaving the managed keyword off.
delegate* managed<int, int>;

// This method will be invoked using whatever the default unmanaged calling convention on the runtime
// platform is. This is platform and architecture dependent and is determined by the CLR at runtime.
delegate* unmanaged<int, int>;

// This method will be invoked using the cdecl calling convention
delegate* unmanaged[CallConvCdecl] <int, int>;

// This method will be invoked using the stdcall calling convention, and suppresses GC transition
delegate* unmanaged[CallConvStdCall, CallConvSuppressGCTransition] <int, int>;
```

<span data-ttu-id="62d43-120">Le conversioni tra `delegate*` i tipi vengono eseguite in base alla relativa firma, inclusa la convenzione di chiamata.</span><span class="sxs-lookup"><span data-stu-id="62d43-120">Conversions between `delegate*` types is done based on their signature including the calling convention.</span></span>

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

<span data-ttu-id="62d43-121">Un `delegate*` tipo è un tipo di puntatore che significa che ha tutte le funzionalità e le restrizioni di un tipo di puntatore standard:</span><span class="sxs-lookup"><span data-stu-id="62d43-121">A `delegate*` type is a pointer type which means it has all of the capabilities and restrictions of a standard pointer type:</span></span>

- <span data-ttu-id="62d43-122">Valido solo in un `unsafe` contesto.</span><span class="sxs-lookup"><span data-stu-id="62d43-122">Only valid in an `unsafe` context.</span></span>
- <span data-ttu-id="62d43-123">I metodi che contengono un `delegate*` parametro o un tipo restituito possono essere chiamati solo da un `unsafe` contesto.</span><span class="sxs-lookup"><span data-stu-id="62d43-123">Methods which contain a `delegate*` parameter or return type can only be called from an `unsafe` context.</span></span>
- <span data-ttu-id="62d43-124">Non può essere convertito in `object` .</span><span class="sxs-lookup"><span data-stu-id="62d43-124">Cannot be converted to `object`.</span></span>
- <span data-ttu-id="62d43-125">Non può essere usato come argomento generico.</span><span class="sxs-lookup"><span data-stu-id="62d43-125">Cannot be used as a generic argument.</span></span>
- <span data-ttu-id="62d43-126">Può convertire in modo implicito `delegate*` in `void*` .</span><span class="sxs-lookup"><span data-stu-id="62d43-126">Can implicitly convert `delegate*` to `void*`.</span></span>
- <span data-ttu-id="62d43-127">Consente di eseguire la conversione esplicita da `void*` a `delegate*` .</span><span class="sxs-lookup"><span data-stu-id="62d43-127">Can explicitly convert from `void*` to `delegate*`.</span></span>

<span data-ttu-id="62d43-128">Restrizioni:</span><span class="sxs-lookup"><span data-stu-id="62d43-128">Restrictions:</span></span>

- <span data-ttu-id="62d43-129">Gli attributi personalizzati non possono essere applicati a un oggetto o a uno `delegate*` dei relativi elementi.</span><span class="sxs-lookup"><span data-stu-id="62d43-129">Custom attributes cannot be applied to a `delegate*` or any of its elements.</span></span>
- <span data-ttu-id="62d43-130">Un `delegate*` parametro non può essere contrassegnato come`params`</span><span class="sxs-lookup"><span data-stu-id="62d43-130">A `delegate*` parameter cannot be marked as `params`</span></span>
- <span data-ttu-id="62d43-131">Un `delegate*` tipo ha tutte le restrizioni di un tipo di puntatore normale.</span><span class="sxs-lookup"><span data-stu-id="62d43-131">A `delegate*` type has all of the restrictions of a normal pointer type.</span></span>
- <span data-ttu-id="62d43-132">Non è possibile eseguire l'aritmetica del puntatore direttamente sui tipi di puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="62d43-132">Pointer arithmetic cannot be performed directly on function pointer types.</span></span>

### <a name="function-pointer-syntax"></a><span data-ttu-id="62d43-133">Sintassi del puntatore a funzione</span><span class="sxs-lookup"><span data-stu-id="62d43-133">Function pointer syntax</span></span>

<span data-ttu-id="62d43-134">La sintassi del puntatore a funzione completa è rappresentata dalla grammatica seguente:</span><span class="sxs-lookup"><span data-stu-id="62d43-134">The full function pointer syntax is represented by the following grammar:</span></span>

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

<span data-ttu-id="62d43-135">Se non `calling_convention_specifier` viene specificato alcun valore, il valore predefinito è `managed` .</span><span class="sxs-lookup"><span data-stu-id="62d43-135">If no `calling_convention_specifier` is provided, the default is `managed`.</span></span> <span data-ttu-id="62d43-136">La codifica dei metadati precisa di `calling_convention_specifier` e di quali `identifier` sono i validi in `unmanaged_calling_convention` viene analizzata nella [rappresentazione dei metadati delle convenzioni di chiamata](#Metadata-Representation-of-Calling-Conventions).</span><span class="sxs-lookup"><span data-stu-id="62d43-136">The precise metadata encoding of the `calling_convention_specifier` and what `identifier`s are valid in the `unmanaged_calling_convention` is covered in [Metadata Representation of Calling Conventions](#Metadata-Representation-of-Calling-Conventions).</span></span>

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

### <a name="function-pointer-conversions"></a><span data-ttu-id="62d43-137">Conversioni di puntatori a funzione</span><span class="sxs-lookup"><span data-stu-id="62d43-137">Function pointer conversions</span></span>

<span data-ttu-id="62d43-138">In un contesto non sicuro, il set di conversioni implicite disponibili (conversioni implicite) viene esteso in modo da includere le seguenti conversioni implicite del puntatore:</span><span class="sxs-lookup"><span data-stu-id="62d43-138">In an unsafe context, the set of available implicit conversions (Implicit conversions) is extended to include the following implicit pointer conversions:</span></span>
- [<span data-ttu-id="62d43-139">_Conversioni esistenti_</span><span class="sxs-lookup"><span data-stu-id="62d43-139">_Existing conversions_</span></span>](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#pointer-conversions)
- <span data-ttu-id="62d43-140">Dal _ \_ tipo funcptr_ `F0` a un _altro \_ tipo funcptr_ `F1` , purché siano soddisfatte tutte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="62d43-140">From _funcptr\_type_ `F0` to another _funcptr\_type_ `F1`, provided all of the following are true:</span></span>
    - <span data-ttu-id="62d43-141">`F0`e `F1` hanno lo stesso numero di parametri e ogni parametro `D0n` in `F0` ha gli stessi `ref` `out` modificatori, o del `in` parametro corrispondente `D1n` in `F1` .</span><span class="sxs-lookup"><span data-stu-id="62d43-141">`F0` and `F1` have the same number of parameters, and each parameter `D0n` in `F0` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter `D1n` in `F1`.</span></span>
    - <span data-ttu-id="62d43-142">Per ogni parametro di valore (un parametro senza `ref` `out` `in` modificatore, o), esiste una conversione di identità, una conversione implicita di un riferimento o una conversione del puntatore implicita dal tipo di parametro in `F0` al tipo di parametro corrispondente in `F1` .</span><span class="sxs-lookup"><span data-stu-id="62d43-142">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `F0` to the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="62d43-143">Per ogni `ref` `out` parametro, o `in` , il tipo di parametro in `F0` corrisponde al tipo di parametro corrispondente in `F1` .</span><span class="sxs-lookup"><span data-stu-id="62d43-143">For each `ref`, `out`, or `in` parameter, the parameter type in `F0` is the same as the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="62d43-144">Se il tipo restituito è per valore (No `ref` o `ref readonly` ), esiste un'identità, un riferimento implicito o una conversione del puntatore implicita dal tipo restituito di `F1` al tipo restituito di `F0` .</span><span class="sxs-lookup"><span data-stu-id="62d43-144">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F1` to the return type of `F0`.</span></span>
    - <span data-ttu-id="62d43-145">Se il tipo restituito è per riferimento ( `ref` o `ref readonly` ), il tipo restituito e i `ref` modificatori di `F1` sono gli stessi del tipo restituito e `ref` dei modificatori di `F0` .</span><span class="sxs-lookup"><span data-stu-id="62d43-145">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F1` are the same as the return type and `ref` modifiers of `F0`.</span></span>
    - <span data-ttu-id="62d43-146">La convenzione di chiamata di `F0` è uguale alla convenzione di chiamata di `F1` .</span><span class="sxs-lookup"><span data-stu-id="62d43-146">The calling convention of `F0` is the same as the calling convention of `F1`.</span></span>

### <a name="allow-address-of-to-target-methods"></a><span data-ttu-id="62d43-147">Consenti indirizzo-di ai metodi di destinazione</span><span class="sxs-lookup"><span data-stu-id="62d43-147">Allow address-of to target methods</span></span>

<span data-ttu-id="62d43-148">I gruppi di metodi saranno ora consentiti come argomenti per un'espressione address-of.</span><span class="sxs-lookup"><span data-stu-id="62d43-148">Method groups will now be allowed as arguments to an address-of expression.</span></span> <span data-ttu-id="62d43-149">Il tipo di tale espressione sarà un `delegate*` che ha la firma equivalente del metodo di destinazione e una convenzione di chiamata gestita:</span><span class="sxs-lookup"><span data-stu-id="62d43-149">The type of such an expression will be a `delegate*` which has the equivalent signature of the target method and a managed calling convention:</span></span>

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

<span data-ttu-id="62d43-150">In un contesto non sicuro, un metodo `M` è compatibile con un tipo di puntatore a funzione `F` se si verificano tutte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="62d43-150">In an unsafe context, a method `M` is compatible with a function pointer type `F` if all of the following are true:</span></span>
- <span data-ttu-id="62d43-151">`M`e `F` hanno lo stesso numero di parametri e ogni parametro in `M` ha gli stessi `ref` `out` modificatori, o del `in` parametro corrispondente in `F` .</span><span class="sxs-lookup"><span data-stu-id="62d43-151">`M` and `F` have the same number of parameters, and each parameter in `M` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter in `F`.</span></span>
- <span data-ttu-id="62d43-152">Per ogni parametro di valore (un parametro senza `ref` `out` `in` modificatore, o), esiste una conversione di identità, una conversione implicita di un riferimento o una conversione del puntatore implicita dal tipo di parametro in `M` al tipo di parametro corrispondente in `F` .</span><span class="sxs-lookup"><span data-stu-id="62d43-152">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `M` to the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="62d43-153">Per ogni `ref` `out` parametro, o `in` , il tipo di parametro in `M` corrisponde al tipo di parametro corrispondente in `F` .</span><span class="sxs-lookup"><span data-stu-id="62d43-153">For each `ref`, `out`, or `in` parameter, the parameter type in `M` is the same as the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="62d43-154">Se il tipo restituito è per valore (No `ref` o `ref readonly` ), esiste un'identità, un riferimento implicito o una conversione del puntatore implicita dal tipo restituito di `F` al tipo restituito di `M` .</span><span class="sxs-lookup"><span data-stu-id="62d43-154">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F` to the return type of `M`.</span></span>
- <span data-ttu-id="62d43-155">Se il tipo restituito è per riferimento ( `ref` o `ref readonly` ), il tipo restituito e i `ref` modificatori di `F` sono gli stessi del tipo restituito e `ref` dei modificatori di `M` .</span><span class="sxs-lookup"><span data-stu-id="62d43-155">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F` are the same as the return type and `ref` modifiers of `M`.</span></span>
- <span data-ttu-id="62d43-156">La convenzione di chiamata di `M` è uguale alla convenzione di chiamata di `F` .</span><span class="sxs-lookup"><span data-stu-id="62d43-156">The calling convention of `M` is the same as the calling convention of `F`.</span></span> <span data-ttu-id="62d43-157">Sono inclusi sia il bit della convenzione di chiamata sia i flag della convenzione di chiamata specificati nell'identificatore non gestito.</span><span class="sxs-lookup"><span data-stu-id="62d43-157">This includes both the calling convention bit, as well as any calling convention flags specified in the unmanaged identifier.</span></span>
- <span data-ttu-id="62d43-158">`M` è un metodo statico.</span><span class="sxs-lookup"><span data-stu-id="62d43-158">`M` is a static method.</span></span>

<span data-ttu-id="62d43-159">In un contesto non sicuro, esiste una conversione implicita da un'espressione address-of la cui destinazione è un gruppo `E` di metodi a un tipo di puntatore a funzione compatibile `F` se `E` contiene almeno un metodo applicabile nel formato normale a un elenco di argomenti costruito usando i tipi di parametro e i modificatori di `F` , come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="62d43-159">In an unsafe context, an implicit conversion exists from an address-of expression whose target is a method group `E` to a compatible function pointer type `F` if `E` contains at least one method that is applicable in its normal form to an argument list constructed by use of the parameter types and modifiers of `F`, as described in the following.</span></span>
- <span data-ttu-id="62d43-160">Viene selezionato un singolo metodo `M` corrispondente alla chiamata di un metodo del form `E(A)` con le modifiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="62d43-160">A single method `M` is selected corresponding to a method invocation of the form `E(A)` with the following modifications:</span></span>
   - <span data-ttu-id="62d43-161">L'elenco `A` degli argomenti è un elenco di espressioni, ciascuna classificata come variabile e con il tipo e il modificatore ( `ref` , `out` o `in` ) dell' _ \_ \_ elenco di parametri funcptr_ corrispondente di `F` .</span><span class="sxs-lookup"><span data-stu-id="62d43-161">The arguments list `A` is a list of expressions, each classified as a variable and with the type and modifier (`ref`, `out`, or `in`) of the corresponding _funcptr\_parameter\_list_ of `F`.</span></span>
   - <span data-ttu-id="62d43-162">I metodi candidati sono solo i metodi applicabili nel formato normale, non quelli applicabili nella forma espansa.</span><span class="sxs-lookup"><span data-stu-id="62d43-162">The candidate methods are only those methods that are applicable in their normal form, not those applicable in their expanded form.</span></span>
   - <span data-ttu-id="62d43-163">I metodi candidati sono solo i metodi statici.</span><span class="sxs-lookup"><span data-stu-id="62d43-163">The candidate methods are only those methods that are static.</span></span>
- <span data-ttu-id="62d43-164">Se l'algoritmo di risoluzione dell'overload genera un errore, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="62d43-164">If the algorithm of overload resolution produces an error, then a compile-time error occurs.</span></span> <span data-ttu-id="62d43-165">In caso contrario, l'algoritmo produce un unico metodo migliore `M` con lo stesso numero di parametri di `F` e la conversione viene considerata esistente.</span><span class="sxs-lookup"><span data-stu-id="62d43-165">Otherwise, the algorithm produces a single best method `M` having the same number of parameters as `F` and the conversion is considered to exist.</span></span>
- <span data-ttu-id="62d43-166">Il metodo selezionato `M` deve essere compatibile (come definito in precedenza) con il tipo di puntatore a funzione `F` .</span><span class="sxs-lookup"><span data-stu-id="62d43-166">The selected method `M` must be compatible (as defined above) with the function pointer type `F`.</span></span> <span data-ttu-id="62d43-167">In caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="62d43-167">Otherwise, a compile-time error occurs.</span></span>
- <span data-ttu-id="62d43-168">Il risultato della conversione è un puntatore a funzione di tipo `F` .</span><span class="sxs-lookup"><span data-stu-id="62d43-168">The result of the conversion is a function pointer of type `F`.</span></span>

<span data-ttu-id="62d43-169">Ciò significa che gli sviluppatori possono dipendere dalle regole di risoluzione dell'overload per lavorare insieme all'operatore address-of:</span><span class="sxs-lookup"><span data-stu-id="62d43-169">This means developers can depend on overload resolution rules to work in conjunction with the address-of operator:</span></span>

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

<span data-ttu-id="62d43-170">L'operatore address-of verrà implementato utilizzando l' `ldftn` istruzione.</span><span class="sxs-lookup"><span data-stu-id="62d43-170">The address-of operator will be implemented using the `ldftn` instruction.</span></span>

<span data-ttu-id="62d43-171">Limitazioni di questa funzionalità:</span><span class="sxs-lookup"><span data-stu-id="62d43-171">Restrictions of this feature:</span></span>

- <span data-ttu-id="62d43-172">Si applica solo ai metodi contrassegnati come `static` .</span><span class="sxs-lookup"><span data-stu-id="62d43-172">Only applies to methods marked as `static`.</span></span>
- <span data-ttu-id="62d43-173">Le `static` funzioni non locali non possono essere utilizzate in `&` .</span><span class="sxs-lookup"><span data-stu-id="62d43-173">Non-`static` local functions cannot be used in `&`.</span></span> <span data-ttu-id="62d43-174">I dettagli di implementazione di questi metodi non sono deliberatamente specificati dal linguaggio.</span><span class="sxs-lookup"><span data-stu-id="62d43-174">The implementation details of these methods are deliberately not specified by the language.</span></span> <span data-ttu-id="62d43-175">Ciò include la presenza di un'istanza statica rispetto a quella con cui vengono emesse le firme.</span><span class="sxs-lookup"><span data-stu-id="62d43-175">This includes whether they are static vs. instance or exactly what signature they are emitted with.</span></span>


### <a name="operators-on-function-pointer-types"></a><span data-ttu-id="62d43-176">Operatori sui tipi di puntatore a funzione</span><span class="sxs-lookup"><span data-stu-id="62d43-176">Operators on Function Pointer Types</span></span>

<span data-ttu-id="62d43-177">La sezione in codice unsafe sugli operatori viene modificata come segue:</span><span class="sxs-lookup"><span data-stu-id="62d43-177">The section in unsafe code on operators is modified as such:</span></span>

> <span data-ttu-id="62d43-178">In un contesto non sicuro sono disponibili numerosi costrutti per operare su tutti i _pointer \_ type_s che non sono _funcptr \_ type_s:</span><span class="sxs-lookup"><span data-stu-id="62d43-178">In an unsafe context, several constructs are available for operating on all _pointer\_type_s that are not _funcptr\_type_s:</span></span>
>
> *  <span data-ttu-id="62d43-179">L' `*` operatore può essere utilizzato per eseguire un riferimento indiretto del puntatore ([riferimento indiretto](unsafe-code.md#pointer-indirection)).</span><span class="sxs-lookup"><span data-stu-id="62d43-179">The `*` operator may be used to perform pointer indirection ([Pointer indirection](unsafe-code.md#pointer-indirection)).</span></span>
> *  <span data-ttu-id="62d43-180">L' `->` operatore può essere usato per accedere a un membro di uno struct tramite un puntatore ([accesso al membro del puntatore](unsafe-code.md#pointer-member-access)).</span><span class="sxs-lookup"><span data-stu-id="62d43-180">The `->` operator may be used to access a member of a struct through a pointer ([Pointer member access](unsafe-code.md#pointer-member-access)).</span></span>
> *  <span data-ttu-id="62d43-181">L' `[]` operatore può essere utilizzato per indicizzare un puntatore ([accesso all'elemento del puntatore](unsafe-code.md#pointer-element-access)).</span><span class="sxs-lookup"><span data-stu-id="62d43-181">The `[]` operator may be used to index a pointer ([Pointer element access](unsafe-code.md#pointer-element-access)).</span></span>
> *  <span data-ttu-id="62d43-182">L' `&` operatore può essere utilizzato per ottenere l'indirizzo di una variabile ([l'operatore address-of](unsafe-code.md#the-address-of-operator)).</span><span class="sxs-lookup"><span data-stu-id="62d43-182">The `&` operator may be used to obtain the address of a variable ([The address-of operator](unsafe-code.md#the-address-of-operator)).</span></span>
> *  <span data-ttu-id="62d43-183">Gli `++` `--` operatori e possono essere utilizzati per incrementare e decrementare i puntatori ([incremento e decremento del puntatore](unsafe-code.md#pointer-increment-and-decrement)).</span><span class="sxs-lookup"><span data-stu-id="62d43-183">The `++` and `--` operators may be used to increment and decrement pointers ([Pointer increment and decrement](unsafe-code.md#pointer-increment-and-decrement)).</span></span>
> *  <span data-ttu-id="62d43-184">Gli `+` `-` operatori e possono essere utilizzati per eseguire l'aritmetica dei puntatori ([aritmetica del puntatore](unsafe-code.md#pointer-arithmetic)).</span><span class="sxs-lookup"><span data-stu-id="62d43-184">The `+` and `-` operators may be used to perform pointer arithmetic ([Pointer arithmetic](unsafe-code.md#pointer-arithmetic)).</span></span>
> *  <span data-ttu-id="62d43-185">Gli `==` `!=` operatori,, `<` ,, `>` `<=` e `=>` possono essere usati per confrontare i puntatori ([confronto tra](unsafe-code.md#pointer-comparison)puntatori).</span><span class="sxs-lookup"><span data-stu-id="62d43-185">The `==`, `!=`, `<`, `>`, `<=`, and `=>` operators may be used to compare pointers ([Pointer comparison](unsafe-code.md#pointer-comparison)).</span></span>
> *  <span data-ttu-id="62d43-186">L' `stackalloc` operatore può essere utilizzato per allocare memoria dallo stack di chiamate ([buffer a dimensione fissa](unsafe-code.md#fixed-size-buffers)).</span><span class="sxs-lookup"><span data-stu-id="62d43-186">The `stackalloc` operator may be used to allocate memory from the call stack ([Fixed size buffers](unsafe-code.md#fixed-size-buffers)).</span></span>
> *  <span data-ttu-id="62d43-187">L' `fixed` istruzione può essere utilizzata per correggere temporaneamente una variabile in modo che sia possibile ottenere l'indirizzo ([istruzione fixed](unsafe-code.md#the-fixed-statement)).</span><span class="sxs-lookup"><span data-stu-id="62d43-187">The `fixed` statement may be used to temporarily fix a variable so its address can be obtained ([The fixed statement](unsafe-code.md#the-fixed-statement)).</span></span>
> 
> <span data-ttu-id="62d43-188">In un contesto non sicuro sono disponibili numerosi costrutti per operare su tutti i _funcptr \_ type_s:</span><span class="sxs-lookup"><span data-stu-id="62d43-188">In an unsafe context, several constructs are available for operating on all _funcptr\_type_s:</span></span>
> *  <span data-ttu-id="62d43-189">L' `&` operatore può essere utilizzato per ottenere l'indirizzo dei metodi statici ([Consenti indirizzo-di ai metodi di destinazione](function-pointers.md#allow-address-of-to-target-methods))</span><span class="sxs-lookup"><span data-stu-id="62d43-189">The `&` operator may be used to obtain the address of static methods ([Allow address-of to target methods](function-pointers.md#allow-address-of-to-target-methods))</span></span>
> *  <span data-ttu-id="62d43-190">Gli `==` `!=` operatori,, `<` ,, `>` `<=` e `=>` possono essere usati per confrontare i puntatori ([confronto tra](unsafe-code.md#pointer-comparison)puntatori).</span><span class="sxs-lookup"><span data-stu-id="62d43-190">The `==`, `!=`, `<`, `>`, `<=`, and `=>` operators may be used to compare pointers ([Pointer comparison](unsafe-code.md#pointer-comparison)).</span></span>

<span data-ttu-id="62d43-191">Inoltre, si modificano tutte le sezioni in `Pointers in expressions` per impedire tipi di puntatore a funzione, eccetto `Pointer comparison` e `The sizeof operator` .</span><span class="sxs-lookup"><span data-stu-id="62d43-191">Additionally, we modify all the sections in `Pointers in expressions` to forbid function pointer types, except `Pointer comparison` and `The sizeof operator`.</span></span>

### <a name="better-function-member"></a><span data-ttu-id="62d43-192">Membro di funzione migliore</span><span class="sxs-lookup"><span data-stu-id="62d43-192">Better function member</span></span>

<span data-ttu-id="62d43-193">La specifica del membro di funzione migliore verrà modificata in modo da includere la riga seguente:</span><span class="sxs-lookup"><span data-stu-id="62d43-193">The better function member specification will be changed to include the following line:</span></span>

> <span data-ttu-id="62d43-194">Un `delegate*` è più specifico di`void*`</span><span class="sxs-lookup"><span data-stu-id="62d43-194">A `delegate*` is more specific than `void*`</span></span>

<span data-ttu-id="62d43-195">Ciò significa che è possibile eseguire l'overload su `void*` e un oggetto `delegate*` e continuare a usare l'operatore address-of.</span><span class="sxs-lookup"><span data-stu-id="62d43-195">This means that it is possible to overload on `void*` and a `delegate*` and still sensibly use the address-of operator.</span></span>

## <a name="metadata-representation-of-in-out-and-ref-readonly-parameters-and-return-types"></a><span data-ttu-id="62d43-196">Rappresentazione dei metadati `in` di `out` parametri, e `ref readonly` e tipi restituiti</span><span class="sxs-lookup"><span data-stu-id="62d43-196">Metadata representation of `in`, `out`, and `ref readonly` parameters and return types</span></span>

<span data-ttu-id="62d43-197">Le firme del puntatore a funzione non hanno un percorso di flag di parametro, pertanto è necessario codificare se i parametri e il tipo restituito sono `in` , `out` o `ref readonly` usando modreqs.</span><span class="sxs-lookup"><span data-stu-id="62d43-197">Function pointer signatures have no parameter flags location, so we must encode whether parameters and the return type are `in`, `out`, or `ref readonly` by using modreqs.</span></span>

### `in`

<span data-ttu-id="62d43-198">Riutilizziamo `System.Runtime.InteropServices.InAttribute` , applicato come oggetto `modreq` all'identificatore Ref su un parametro o un tipo restituito, per indicare quanto segue:</span><span class="sxs-lookup"><span data-stu-id="62d43-198">We reuse `System.Runtime.InteropServices.InAttribute`, applied as a `modreq` to the ref specifier on a parameter or return type, to mean the following:</span></span>
* <span data-ttu-id="62d43-199">Se applicato a un identificatore di riferimento del parametro, questo parametro viene trattato come `in` .</span><span class="sxs-lookup"><span data-stu-id="62d43-199">If applied to a parameter ref specifier, this parameter is treated as `in`.</span></span>
* <span data-ttu-id="62d43-200">Se applicato all'identificatore Ref del tipo restituito, il tipo restituito viene considerato come `ref readonly` .</span><span class="sxs-lookup"><span data-stu-id="62d43-200">If applied to the return type ref specifier, the return type is treated as `ref readonly`.</span></span>

### `out`

<span data-ttu-id="62d43-201">Si usa `System.Runtime.InteropServices.OutAttribute` , applicato come oggetto `modreq` all'identificatore Ref su un tipo di parametro, per indicare che il parametro è un `out` parametro.</span><span class="sxs-lookup"><span data-stu-id="62d43-201">We use `System.Runtime.InteropServices.OutAttribute`, applied as a `modreq` to the ref specifier on a parameter type, to mean that the parameter is an `out` parameter.</span></span>

### <a name="errors"></a><span data-ttu-id="62d43-202">Errori</span><span class="sxs-lookup"><span data-stu-id="62d43-202">Errors</span></span>

* <span data-ttu-id="62d43-203">Si tratta di un errore da applicare `OutAttribute` come modreq a un tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="62d43-203">It is an error to apply `OutAttribute` as a modreq to a return type.</span></span>
* <span data-ttu-id="62d43-204">È un errore applicare sia che `InAttribute` `OutAttribute` come modreq a un tipo di parametro.</span><span class="sxs-lookup"><span data-stu-id="62d43-204">It is an error to apply both `InAttribute` and `OutAttribute` as a modreq to a parameter type.</span></span>
* <span data-ttu-id="62d43-205">Se vengono specificati tramite modopt, verranno ignorati.</span><span class="sxs-lookup"><span data-stu-id="62d43-205">If either are specified via modopt, they are ignored.</span></span>

### <a name="metadata-representation-of-calling-conventions"></a><span data-ttu-id="62d43-206">Rappresentazione dei metadati delle convenzioni di chiamata</span><span class="sxs-lookup"><span data-stu-id="62d43-206">Metadata Representation of Calling Conventions</span></span>

<span data-ttu-id="62d43-207">Le convenzioni di chiamata vengono codificate nella firma di un metodo nei metadati mediante una combinazione del `CallKind` flag nella firma e zero o più `modopt` all'inizio della firma.</span><span class="sxs-lookup"><span data-stu-id="62d43-207">Calling conventions are encoded in a method signature in metadata by a combination of the `CallKind` flag in the signature and zero or more `modopt`s at the start of the signature.</span></span> <span data-ttu-id="62d43-208">ECMA-335 dichiara attualmente gli elementi seguenti nel `CallKind` flag:</span><span class="sxs-lookup"><span data-stu-id="62d43-208">ECMA-335 currently declares the following elements in the `CallKind` flag:</span></span>

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

<span data-ttu-id="62d43-209">Di questi, i puntatori a funzione in C# supporteranno tutti i, tranne `varargs` .</span><span class="sxs-lookup"><span data-stu-id="62d43-209">Of these, function pointers in C# will support all but `varargs`.</span></span>

<span data-ttu-id="62d43-210">Inoltre, il runtime (e infine 335) verrà aggiornato in modo da includere un nuovo `CallKind` nelle nuove piattaforme.</span><span class="sxs-lookup"><span data-stu-id="62d43-210">In addition, the runtime (and eventually 335) will be updated to include a new `CallKind` on new platforms.</span></span> <span data-ttu-id="62d43-211">Attualmente non è presente un nome formale, ma questo documento utilizzerà `unmanaged ext` come segnaposto per il nuovo formato della convenzione di chiamata estendibile.</span><span class="sxs-lookup"><span data-stu-id="62d43-211">This does not have a formal name currently, but this document will use `unmanaged ext` as a placeholder to stand for the new extensible calling convention format.</span></span> <span data-ttu-id="62d43-212">Con No `modopt` s, `unmanaged ext` è la convenzione di chiamata predefinita della piattaforma, `unmanaged` senza le parentesi quadre.</span><span class="sxs-lookup"><span data-stu-id="62d43-212">With no `modopt`s, `unmanaged ext` is the platform default calling convention, `unmanaged` without the square brackets.</span></span>

#### <a name="mapping-the-calling_convention_specifier-to-a-callkind"></a><span data-ttu-id="62d43-213">Mapping `calling_convention_specifier` di a un`CallKind`</span><span class="sxs-lookup"><span data-stu-id="62d43-213">Mapping the `calling_convention_specifier` to a `CallKind`</span></span>

<span data-ttu-id="62d43-214">Un oggetto `calling_convention_specifier` che viene omesso o specificato come `managed` , viene mappato a `default` `CallKind` .</span><span class="sxs-lookup"><span data-stu-id="62d43-214">A `calling_convention_specifier` that is omitted, or specified as `managed`, maps to the `default` `CallKind`.</span></span> <span data-ttu-id="62d43-215">Si tratta del valore predefinito `CallKind` di qualsiasi metodo senza attributi `UnmanagedCallersOnly` .</span><span class="sxs-lookup"><span data-stu-id="62d43-215">This is default `CallKind` of any method not attributed with `UnmanagedCallersOnly`.</span></span>

<span data-ttu-id="62d43-216">C# riconosce 4 identificatori speciali che vengono mappati a specifiche non gestite esistenti `CallKind` da ECMA 335.</span><span class="sxs-lookup"><span data-stu-id="62d43-216">C# recognizes 4 special identifiers that map to specific existing unmanaged `CallKind`s from ECMA 335.</span></span> <span data-ttu-id="62d43-217">Affinché questo mapping venga eseguito, è necessario specificare i relativi identificatori in modo autonomo, senza altri identificatori, e questo requisito viene codificato nelle specifiche per `unmanaged_calling_convention` s.</span><span class="sxs-lookup"><span data-stu-id="62d43-217">In order for this mapping to occur, these identifiers must be specified on their own, with no other identifiers, and this requirement is encoded into the spec for `unmanaged_calling_convention`s.</span></span> <span data-ttu-id="62d43-218">Questi identificatori sono `Cdecl` , `Thiscall` , `Stdcall` e `Fastcall` , che corrispondono rispettivamente a `unmanaged cdecl` , `unmanaged thiscall` , `unmanaged stdcall` e `unmanaged fastcall` .</span><span class="sxs-lookup"><span data-stu-id="62d43-218">These identifiers are `Cdecl`, `Thiscall`, `Stdcall`, and `Fastcall`, which correspond to `unmanaged cdecl`, `unmanaged thiscall`, `unmanaged stdcall`, and `unmanaged fastcall`, respectively.</span></span> <span data-ttu-id="62d43-219">Se viene specificato più di un `identifer` oggetto o se il singolo `identifier` non è costituito dagli identificatori specifici, viene eseguita la ricerca di nomi speciali sull'identificatore con le regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="62d43-219">If more than one `identifer` is specified, or the single `identifier` is not of the specially recognized identifiers, we perform special name lookup on the identifier with the following rules:</span></span>

* <span data-ttu-id="62d43-220">Viene anteposto l'oggetto `identifier` con la stringa`CallConv`</span><span class="sxs-lookup"><span data-stu-id="62d43-220">We prepend the `identifier` with the string `CallConv`</span></span>
* <span data-ttu-id="62d43-221">Si osserveranno solo i tipi definiti nello `System.Runtime.CompilerServices` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="62d43-221">We look only at types defined in the `System.Runtime.CompilerServices` namespace.</span></span>
* <span data-ttu-id="62d43-222">Si osserveranno solo i tipi definiti nella libreria principale dell'applicazione, ovvero la libreria che definisce `System.Object` e non ha dipendenze.</span><span class="sxs-lookup"><span data-stu-id="62d43-222">We look only at types defined in the core library of the application, which is the library that defines `System.Object` and has no dependencies.</span></span>

<span data-ttu-id="62d43-223">Se la ricerca ha esito positivo su tutti gli oggetti `identifier` specificati in un oggetto `unmanaged_calling_convention` , viene codificato `CallKind` come `unmanaged ext` e vengono codificati tutti i tipi risolti nel set di oggetti `modopt` all'inizio della firma del puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="62d43-223">If lookup succeeds on all of the `identifier`s specified in an `unmanaged_calling_convention`, we encode the `CallKind` as `unmanaged ext`, and encode each of the resolved types in the set of `modopt`s at the beginning of the function pointer signature.</span></span> <span data-ttu-id="62d43-224">Si noti che queste regole indicano che gli utenti non possono precedere questi `identifier` con `CallConv` , perché ciò comporta la ricerca `CallConvCallConvVectorCall` .</span><span class="sxs-lookup"><span data-stu-id="62d43-224">As a note, these rules mean that users cannot prefix these `identifier`s with `CallConv`, as that will result in looking up `CallConvCallConvVectorCall`.</span></span>

<span data-ttu-id="62d43-225">Quando si interpretano i metadati, si esamina prima di tutto `CallKind` .</span><span class="sxs-lookup"><span data-stu-id="62d43-225">When interpreting metadata, we first look at the `CallKind`.</span></span> <span data-ttu-id="62d43-226">Se si tratta di un valore diverso da `unmanaged ext` , si ignoreranno tutti i `modopt` nel tipo restituito ai fini della determinazione della convenzione di chiamata e si utilizzerà solo `CallKind` .</span><span class="sxs-lookup"><span data-stu-id="62d43-226">If it is anything other than `unmanaged ext`, we ignore all `modopt`s on the return type for the purposes of determining the calling convention, and use only the `CallKind`.</span></span> <span data-ttu-id="62d43-227">Se `CallKind` è `unmanaged ext` , si esamina il modopts all'inizio del tipo di puntatore a funzione, prendendo l'Unione di tutti i tipi che soddisfano i requisiti seguenti:</span><span class="sxs-lookup"><span data-stu-id="62d43-227">If the `CallKind` is `unmanaged ext`, we look at the modopts at the start of the function pointer type, taking the union of all types that meet the following requirements:</span></span>

* <span data-ttu-id="62d43-228">L'oggetto è definito nella libreria principale, ovvero la libreria che fa riferimento ad altre librerie e non definisce `System.Object` .</span><span class="sxs-lookup"><span data-stu-id="62d43-228">The is defined in the core library, which is the library that references no other libraries and defines `System.Object`.</span></span>
* <span data-ttu-id="62d43-229">Il tipo è definito nello `System.Runtime.CompilerServices` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="62d43-229">The type is defined in the `System.Runtime.CompilerServices` namespace.</span></span>
* <span data-ttu-id="62d43-230">Il tipo inizia con il prefisso `CallConv` .</span><span class="sxs-lookup"><span data-stu-id="62d43-230">The type starts with the prefix `CallConv`.</span></span>

 <span data-ttu-id="62d43-231">Rappresentano i tipi che devono essere trovati quando si esegue la ricerca in `identifier` un oggetto `unmanaged_calling_convention` quando si definisce un tipo di puntatore a funzione nell'origine.</span><span class="sxs-lookup"><span data-stu-id="62d43-231">These represent the types that must be found when performing lookup on the `identifier`s in an `unmanaged_calling_convention` when defining a function pointer type in source.</span></span>

<span data-ttu-id="62d43-232">Non è possibile tentare di usare un puntatore a funzione con un valore `CallKind` di `unmanaged ext` se il runtime di destinazione non supporta la funzionalità.</span><span class="sxs-lookup"><span data-stu-id="62d43-232">It is an error to attempt to use a function pointer with a `CallKind` of `unmanaged ext` if the target runtime does not support the feature.</span></span> <span data-ttu-id="62d43-233">Questa operazione verrà determinata cercando la presenza della `System.Runtime.CompilerServices.RuntimeFeature.UnmanagedCallKind` costante.</span><span class="sxs-lookup"><span data-stu-id="62d43-233">This will be determined by looking for the presence of the `System.Runtime.CompilerServices.RuntimeFeature.UnmanagedCallKind` constant.</span></span> <span data-ttu-id="62d43-234">Se questa costante è presente, il runtime viene considerato come supporto della funzionalità.</span><span class="sxs-lookup"><span data-stu-id="62d43-234">If this constant is present, the runtime is considered to support the feature.</span></span>

### `System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute`

<span data-ttu-id="62d43-235">`System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute`attributo utilizzato da CLR per indicare che un metodo deve essere chiamato con una convenzione di chiamata specifica.</span><span class="sxs-lookup"><span data-stu-id="62d43-235">`System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute` is an attribute used by the CLR to indicate that a method should be called with a specific calling convention.</span></span> <span data-ttu-id="62d43-236">Per questo motivo, viene introdotto il supporto seguente per l'utilizzo dell'attributo:</span><span class="sxs-lookup"><span data-stu-id="62d43-236">Because of this, we introduce the following support for working with the attribute:</span></span>

* <span data-ttu-id="62d43-237">È un errore chiamare direttamente un metodo annotato con questo attributo da C#.</span><span class="sxs-lookup"><span data-stu-id="62d43-237">It is an error to directly call a method annotated with this attribute from C#.</span></span> <span data-ttu-id="62d43-238">Gli utenti devono ottenere un puntatore a funzione per il metodo e quindi richiamare il puntatore.</span><span class="sxs-lookup"><span data-stu-id="62d43-238">Users must obtain a function pointer to the method and then invoke that pointer.</span></span>
* <span data-ttu-id="62d43-239">È un errore applicare l'attributo a qualsiasi elemento diverso da un metodo statico.</span><span class="sxs-lookup"><span data-stu-id="62d43-239">It is an error to apply the attribute to anything other than a static method.</span></span> <span data-ttu-id="62d43-240">Il compilatore C# contrassegna tutti i metodi non statici importati dai metadati con questo attributo come non supportati dal linguaggio.</span><span class="sxs-lookup"><span data-stu-id="62d43-240">The C# compiler will mark any non-static methods imported from metadata with this attribute as unsupported by the language.</span></span>
* <span data-ttu-id="62d43-241">Non è possibile usare tipi non gestiti come parametri o il tipo restituito di un metodo contrassegnato con l'attributo.</span><span class="sxs-lookup"><span data-stu-id="62d43-241">It is an error to have non-unmanaged types as parameters or the return type of a method marked with the attribute.</span></span>
* <span data-ttu-id="62d43-242">È un errore convertire un metodo contrassegnato con l'attributo in un tipo delegato.</span><span class="sxs-lookup"><span data-stu-id="62d43-242">It is an error to convert a method marked with the attribute to a delegate type.</span></span>
* <span data-ttu-id="62d43-243">Non è un errore specificare i tipi per `UnmanagedCallersOnly.CallConvs` che non soddisfano i requisiti per la chiamata di Convention `modopt` s nei metadati.</span><span class="sxs-lookup"><span data-stu-id="62d43-243">It is an error to specify any types for `UnmanagedCallersOnly.CallConvs` that do not meet the requirements for calling convention `modopt`s in metadata.</span></span>

<span data-ttu-id="62d43-244">Quando si determina la convenzione di chiamata di un metodo contrassegnato con un `UnmanagedCallersOnly` attributo valido, il compilatore esegue i controlli seguenti sui tipi specificati nella `CallConvs` proprietà per determinare gli effettivi `CallKind` e `modopt` che devono essere utilizzati per determinare la convenzione di chiamata:</span><span class="sxs-lookup"><span data-stu-id="62d43-244">When determining the calling convention of a method marked with a valid `UnmanagedCallersOnly` attribute, the compiler performs the following checks on the types specified in the `CallConvs` property to determine the effective `CallKind` and `modopt`s that should be used to determine the calling convention:</span></span>

* <span data-ttu-id="62d43-245">Se non viene specificato alcun tipo, `CallKind` viene considerato come `unmanaged ext` , senza alcuna convenzione `modopt` di chiamata all'inizio del tipo di puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="62d43-245">If no types are specified, the `CallKind` is treated as `unmanaged ext`, with no calling convention `modopt`s at the start of the function pointer type.</span></span>
* <span data-ttu-id="62d43-246">Se è stato specificato un tipo e tale tipo è denominato `CallConvCdecl` ,, `CallConvThiscall` `CallConvStdcall` o `CallConvFastcall` , l'oggetto `CallKind` viene trattato come,, `unmanaged cdecl` `unmanaged thiscall` `unmanaged stdcall` o `unmanaged fastcall` , rispettivamente, senza convenzione di chiamata `modopt` all'inizio del tipo di puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="62d43-246">If there is one type specified, and that type is named `CallConvCdecl`, `CallConvThiscall`, `CallConvStdcall`, or `CallConvFastcall`, the `CallKind` is treated as `unmanaged cdecl`, `unmanaged thiscall`, `unmanaged stdcall`, or `unmanaged fastcall`, respectively, with no calling convention `modopt`s at the start of the function pointer type.</span></span>
* <span data-ttu-id="62d43-247">Se vengono specificati più tipi o se il tipo singolo non è denominato uno dei tipi indicati in precedenza, `CallKind` viene considerato come `unmanaged ext` , con l'Unione dei tipi specificati come `modopt` s all'inizio del tipo di puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="62d43-247">If multiple types are specified or the single type is not named one of the specially called out types above, the `CallKind` is treated as `unmanaged ext`, with the union of the types specified treated as `modopt`s at the start of the function pointer type.</span></span>

<span data-ttu-id="62d43-248">Il compilatore esamina quindi questo effetto `CallKind` e la `modopt` raccolta e usa le normali regole di metadati per determinare la convenzione di chiamata finale del tipo di puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="62d43-248">The compiler then looks at this effective `CallKind` and `modopt` collection and uses normal metadata rules to determine the final calling convention of the function pointer type.</span></span>

## <a name="open-questions"></a><span data-ttu-id="62d43-249">Domande aperte</span><span class="sxs-lookup"><span data-stu-id="62d43-249">Open Questions</span></span>

### <a name="detecting-runtime-support-for-unmanaged-ext"></a><span data-ttu-id="62d43-250">Rilevamento del supporto runtime per`unmanaged ext`</span><span class="sxs-lookup"><span data-stu-id="62d43-250">Detecting runtime support for `unmanaged ext`</span></span>

<span data-ttu-id="62d43-251">https://github.com/dotnet/runtime/issues/38135tiene traccia dell'aggiunta di questo flag.</span><span class="sxs-lookup"><span data-stu-id="62d43-251">https://github.com/dotnet/runtime/issues/38135 tracks adding this flag.</span></span> <span data-ttu-id="62d43-252">A seconda dei commenti e suggerimenti della revisione, si utilizzerà la proprietà specificata nel problema oppure si utilizzerà la presenza di `UnmanagedCallersOnlyAttribute` come flag che determina se i runtime supportano `unmanaged ext` .</span><span class="sxs-lookup"><span data-stu-id="62d43-252">Depending on the feedback from review, we will either use the property specified in the issue, or use the presence of `UnmanagedCallersOnlyAttribute` as the flag that determines whether the runtimes supports `unmanaged ext`.</span></span>

## <a name="considerations"></a><span data-ttu-id="62d43-253">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="62d43-253">Considerations</span></span>

### <a name="allow-instance-methods"></a><span data-ttu-id="62d43-254">Consenti metodi di istanza</span><span class="sxs-lookup"><span data-stu-id="62d43-254">Allow instance methods</span></span>

<span data-ttu-id="62d43-255">È possibile estendere la proposta per supportare i metodi di istanza sfruttando la convenzione di chiamata dell'interfaccia della riga di comando `EXPLICITTHIS` (denominata `instance` nel codice C#).</span><span class="sxs-lookup"><span data-stu-id="62d43-255">The proposal could be extended to support instance methods by taking advantage of the `EXPLICITTHIS` CLI calling convention (named `instance` in C# code).</span></span> <span data-ttu-id="62d43-256">Questa forma di puntatori a funzione dell'interfaccia della riga di comando inserisce il `this` parametro come primo parametro esplicito della sintassi del puntatore a funzione.</span><span class="sxs-lookup"><span data-stu-id="62d43-256">This form of CLI function pointers puts the `this` parameter as an explicit first parameter of the function pointer syntax.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        delegate* instance<Instance, string> f = &ToString;
        f(this);
    }
}
```

<span data-ttu-id="62d43-257">Si tratta di un suono che aggiunge alcune complicazioni alla proposta.</span><span class="sxs-lookup"><span data-stu-id="62d43-257">This is sound but adds some complication to the proposal.</span></span> <span data-ttu-id="62d43-258">In particolare perché i puntatori a funzione che differiscono dalla convenzione di chiamata `instance` e `managed` sarebbero incompatibili anche se entrambi i casi vengono usati per richiamare i metodi gestiti con la stessa firma C#.</span><span class="sxs-lookup"><span data-stu-id="62d43-258">Particularly because function pointers which differed by the calling convention `instance` and `managed` would be incompatible even though both cases are used to invoke managed methods with the same C# signature.</span></span> <span data-ttu-id="62d43-259">Inoltre, in ogni caso, si considera utile la presenza di una semplice soluzione: usare una `static` funzione locale.</span><span class="sxs-lookup"><span data-stu-id="62d43-259">Also in every case considered where this would be valuable to have there was a simple work around: use a `static` local function.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        static string toString(Instance i) = i.ToString();
        delgate*<Instance, string> f = &toString;
        f(this);
    }
}
```

### <a name="dont-require-unsafe-at-declaration"></a><span data-ttu-id="62d43-260">Non richiedere unsafe alla dichiarazione</span><span class="sxs-lookup"><span data-stu-id="62d43-260">Don't require unsafe at declaration</span></span>

<span data-ttu-id="62d43-261">Anziché richiedere `unsafe` a ogni utilizzo di un `delegate*` , richiederlo solo nel punto in cui un gruppo di metodi viene convertito in un oggetto `delegate*` .</span><span class="sxs-lookup"><span data-stu-id="62d43-261">Instead of requiring `unsafe` at every use of a `delegate*`, only require it at the point where a method group is converted to a `delegate*`.</span></span> <span data-ttu-id="62d43-262">Questo è il punto in cui vengono rilevati i problemi di sicurezza di base (sapendo che l'assembly contenitore non può essere scaricato mentre il valore è attivo).</span><span class="sxs-lookup"><span data-stu-id="62d43-262">This is where the core safety issues come into play (knowing that the containing assembly cannot be unloaded while the value is alive).</span></span> <span data-ttu-id="62d43-263">La richiesta `unsafe` di altre posizioni può essere considerata eccessiva.</span><span class="sxs-lookup"><span data-stu-id="62d43-263">Requiring `unsafe` on the other locations can be seen as excessive.</span></span>

<span data-ttu-id="62d43-264">Questo è il modo in cui la progettazione è stata originariamente progettata.</span><span class="sxs-lookup"><span data-stu-id="62d43-264">This is how the design was originally intended.</span></span> <span data-ttu-id="62d43-265">Ma le regole del linguaggio risultavano molto scomode.</span><span class="sxs-lookup"><span data-stu-id="62d43-265">But the resulting language rules felt very awkward.</span></span> <span data-ttu-id="62d43-266">Non è possibile nascondere il fatto che si tratta di un valore di puntatore che continua a leggere anche senza la `unsafe` parola chiave.</span><span class="sxs-lookup"><span data-stu-id="62d43-266">It's impossible to hide the fact that this is a pointer value and it kept peeking through even without the `unsafe` keyword.</span></span> <span data-ttu-id="62d43-267">Ad esempio, la conversione a `object` non può essere consentita, non può essere un membro di `class` , e così via. Il progetto C# è necessario `unsafe` per tutti gli utilizzi del puntatore e pertanto questa progettazione lo segue.</span><span class="sxs-lookup"><span data-stu-id="62d43-267">For example the conversion to `object` can't be allowed, it can't be a member of a `class`, etc ... The C# design is to require `unsafe` for all pointer uses and hence this design follows that.</span></span>

<span data-ttu-id="62d43-268">Gli sviluppatori saranno comunque in grado di presentare un wrapper _sicuro_ sopra i `delegate*` valori nello stesso modo in cui fanno oggi i normali tipi di puntatore.</span><span class="sxs-lookup"><span data-stu-id="62d43-268">Developers will still be capable of presenting a _safe_ wrapper on top of `delegate*` values the same way that they do for normal pointer types today.</span></span> <span data-ttu-id="62d43-269">Tenere in considerazione:</span><span class="sxs-lookup"><span data-stu-id="62d43-269">Consider:</span></span>

``` csharp
unsafe struct Action {
    delegate*<void> _ptr;

    Action(delegate*<void> ptr) => _ptr = ptr;
    public void Invoke() => _ptr();
}
```

### <a name="using-delegates"></a><span data-ttu-id="62d43-270">Uso di delegati</span><span class="sxs-lookup"><span data-stu-id="62d43-270">Using delegates</span></span>

<span data-ttu-id="62d43-271">Invece di usare un nuovo elemento della sintassi, `delegate*` , è sufficiente usare `delegate` i tipi esistenti con `*` il tipo seguente:</span><span class="sxs-lookup"><span data-stu-id="62d43-271">Instead of using a new syntax element, `delegate*`, simply use existing `delegate` types with a `*` following the type:</span></span>

``` csharp
Func<object, object, bool>* ptr = &object.ReferenceEquals;
```

<span data-ttu-id="62d43-272">La gestione della convenzione di chiamata può essere eseguita annotando i `delegate` tipi con un attributo che specifica un `CallingConvention` valore.</span><span class="sxs-lookup"><span data-stu-id="62d43-272">Handling calling convention can be done by annotating the `delegate` types with an attribute that specifies a `CallingConvention` value.</span></span> <span data-ttu-id="62d43-273">La mancanza di un attributo significa la convenzione di chiamata gestita.</span><span class="sxs-lookup"><span data-stu-id="62d43-273">The lack of an attribute would signify the managed calling convention.</span></span>

<span data-ttu-id="62d43-274">La codifica in IL è problematica.</span><span class="sxs-lookup"><span data-stu-id="62d43-274">Encoding this in IL is problematic.</span></span> <span data-ttu-id="62d43-275">Il valore sottostante deve essere rappresentato come puntatore, ma deve anche:</span><span class="sxs-lookup"><span data-stu-id="62d43-275">The underlying value needs to be represented as a pointer yet it also must:</span></span>

1. <span data-ttu-id="62d43-276">Hanno un tipo univoco per consentire gli overload con tipi di puntatore a funzione diversi.</span><span class="sxs-lookup"><span data-stu-id="62d43-276">Have a unique type to allow for overloads with different function pointer types.</span></span>
1. <span data-ttu-id="62d43-277">Essere equivalente a scopi OHI tra i limiti di assembly.</span><span class="sxs-lookup"><span data-stu-id="62d43-277">Be equivalent for OHI purposes across assembly boundaries.</span></span>

<span data-ttu-id="62d43-278">L'ultimo punto è particolarmente problematico.</span><span class="sxs-lookup"><span data-stu-id="62d43-278">The last point is particularly problematic.</span></span> <span data-ttu-id="62d43-279">Ciò significa che ogni assembly utilizzato da `Func<int>*` deve codificare un tipo equivalente nei metadati anche se `Func<int>*` è definito in un assembly, sebbene non controlli.</span><span class="sxs-lookup"><span data-stu-id="62d43-279">This mean that every assembly which uses `Func<int>*` must encode an equivalent type in metadata even though `Func<int>*` is defined in an assembly though don't control.</span></span>
<span data-ttu-id="62d43-280">Inoltre, tutti gli altri tipi definiti con il nome `System.Func<T>` in un assembly che non è mscorlib devono essere diversi dalla versione definita in mscorlib.</span><span class="sxs-lookup"><span data-stu-id="62d43-280">Additionally any other type which is defined with the name `System.Func<T>` in an assembly that is not mscorlib must be different than the version defined in mscorlib.</span></span>

<span data-ttu-id="62d43-281">Una delle opzioni Esplorate è stata la creazione di un puntatore di questo tipo come `mod_req(Func<int>) void*` .</span><span class="sxs-lookup"><span data-stu-id="62d43-281">One option that was explored was emitting such a pointer as `mod_req(Func<int>) void*`.</span></span> <span data-ttu-id="62d43-282">Questa operazione non funziona anche se `mod_req` non è possibile eseguire l'associazione a un oggetto `TypeSpec` e pertanto non è possibile fare riferimento a istanze generiche.</span><span class="sxs-lookup"><span data-stu-id="62d43-282">This doesn't work though as a `mod_req` cannot bind to a `TypeSpec` and hence cannot target generic instantiations.</span></span>

### <a name="named-function-pointers"></a><span data-ttu-id="62d43-283">Puntatori a funzioni denominate</span><span class="sxs-lookup"><span data-stu-id="62d43-283">Named function pointers</span></span>

<span data-ttu-id="62d43-284">La sintassi del puntatore a funzione può essere complessa, soprattutto in casi complessi come i puntatori a funzioni annidate.</span><span class="sxs-lookup"><span data-stu-id="62d43-284">The function pointer syntax can be cumbersome, particularly in complex cases like nested function pointers.</span></span> <span data-ttu-id="62d43-285">Anziché fare in modo che gli sviluppatori digitino la firma ogni volta che la lingua potrebbe consentire le dichiarazioni denominate dei puntatori a funzione, come avviene con `delegate` .</span><span class="sxs-lookup"><span data-stu-id="62d43-285">Rather than have developers type out the signature every time the language could allow for named declarations of function pointers as is done with `delegate`.</span></span>

``` csharp
func* void Action();

unsafe class NamedExample {
    void M(Action a) {
        a();
    }
}
```

<span data-ttu-id="62d43-286">Una parte del problema è che la primitiva dell'interfaccia della riga di comando sottostante non ha nomi, pertanto si tratterebbe puramente di un'invenzione in C# e richiederebbe un po' di lavoro di metadati da abilitare.</span><span class="sxs-lookup"><span data-stu-id="62d43-286">Part of the problem here is the underlying CLI primitive doesn't have names hence this would be purely a C# invention and require a bit of metadata work to enable.</span></span> <span data-ttu-id="62d43-287">Questa operazione è fattibile, ma è una questione significativa di lavoro.</span><span class="sxs-lookup"><span data-stu-id="62d43-287">That is doable but is a significant about of work.</span></span> <span data-ttu-id="62d43-288">In sostanza, è necessario che C# disponga di un complementare alla tabella def del tipo esclusivamente per questi nomi.</span><span class="sxs-lookup"><span data-stu-id="62d43-288">It essentially requires C# to have a companion to the type def table purely for these names.</span></span>

<span data-ttu-id="62d43-289">Inoltre, quando sono stati esaminati gli argomenti per i puntatori a funzione denominati, è possibile che si applichino ugualmente a diversi altri scenari.</span><span class="sxs-lookup"><span data-stu-id="62d43-289">Also when the arguments for named function pointers were examined we found they could apply equally well to a number of other scenarios.</span></span> <span data-ttu-id="62d43-290">Ad esempio, sarebbe altrettanto semplice dichiarare le tuple denominate per ridurre la necessità di digitare la firma completa in tutti i casi.</span><span class="sxs-lookup"><span data-stu-id="62d43-290">For example it would be just as convenient to declare named tuples to reduce the need to type out the full signature in all cases.</span></span>

``` csharp
(int x, int y) Point;

class NamedTupleExample {
    void M(Point p) {
        Console.WriteLine(p.x);
    }
}
```

<span data-ttu-id="62d43-291">Dopo la discussione si è deciso di non consentire la dichiarazione denominata dei `delegate*` tipi.</span><span class="sxs-lookup"><span data-stu-id="62d43-291">After discussion we decided to not allow named declaration of `delegate*` types.</span></span> <span data-ttu-id="62d43-292">Se viene rilevata una necessità significativa in base ai commenti e suggerimenti sull'utilizzo dei clienti, viene esaminata una soluzione di denominazione che funziona per i puntatori a funzione, le tuple, i generics e così via. È probabile che questo aspetto sia simile a quello di altri suggerimenti `typedef` , come il supporto completo nel linguaggio.</span><span class="sxs-lookup"><span data-stu-id="62d43-292">If we find there is significant need for this based on customer usage feedback then we will investigate a naming solution that works for function pointers, tuples, generics, etc ... This is likely to be similar in form to other suggestions like full `typedef` support in the language.</span></span>

## <a name="future-considerations"></a><span data-ttu-id="62d43-293">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="62d43-293">Future Considerations</span></span>

### <a name="static-delegates"></a><span data-ttu-id="62d43-294">delegati statici</span><span class="sxs-lookup"><span data-stu-id="62d43-294">static delegates</span></span>

<span data-ttu-id="62d43-295">Si fa riferimento alla [proposta](https://github.com/dotnet/csharplang/issues/302) per consentire la dichiarazione di `delegate` tipi che possono fare riferimento solo ai `static` membri.</span><span class="sxs-lookup"><span data-stu-id="62d43-295">This refers to [the proposal](https://github.com/dotnet/csharplang/issues/302) to allow for the declaration of `delegate` types which can only refer to `static` members.</span></span> <span data-ttu-id="62d43-296">Il vantaggio è che tali `delegate` istanze possono essere allocazione gratuite e migliori negli scenari sensibili alle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="62d43-296">The advantage being that such `delegate` instances can be allocation free and better in performance sensitive scenarios.</span></span>

<span data-ttu-id="62d43-297">Se la funzionalità del puntatore a funzione è implementata `static delegate` , la proposta verrà probabilmente chiusa. Il vantaggio proposto da questa funzionalità è la natura di allocazione.</span><span class="sxs-lookup"><span data-stu-id="62d43-297">If the function pointer feature is implemented the `static delegate` proposal will likely be closed out. The proposed advantage of that feature is the allocation free nature.</span></span> <span data-ttu-id="62d43-298">Sono state rilevate tuttavia ricerche recenti che non è possibile ottenere a causa dello scaricamento di assembly.</span><span class="sxs-lookup"><span data-stu-id="62d43-298">However recent investigations have found that is not possible to achieve due to assembly unloading.</span></span> <span data-ttu-id="62d43-299">È necessario che sia presente un handle sicuro da `static delegate` al metodo a cui si riferisce per evitare che l'assembly venga scaricato dal sottoinsieme.</span><span class="sxs-lookup"><span data-stu-id="62d43-299">There must be a strong handle from the `static delegate` to the method it refers to in order to keep the assembly from being unloaded out from under it.</span></span>

<span data-ttu-id="62d43-300">Per mantenere ogni `static delegate` istanza è necessario allocare un nuovo handle che esegue il contatore agli obiettivi della proposta.</span><span class="sxs-lookup"><span data-stu-id="62d43-300">To maintain every `static delegate` instance would be required to allocate a new handle which runs counter to the goals of the proposal.</span></span> <span data-ttu-id="62d43-301">Sono stati creati alcuni progetti in cui l'allocazione può essere ammortizzata in una singola allocazione per ogni chiamata, ma si tratta di un po' complesso che non sembra essere il compromesso.</span><span class="sxs-lookup"><span data-stu-id="62d43-301">There were some designs where the allocation could be amortized to a single allocation per call-site but that was a bit complex and didn't seem worth the trade off.</span></span>

<span data-ttu-id="62d43-302">Ciò significa che gli sviluppatori devono essenzialmente decidere tra i compromessi seguenti:</span><span class="sxs-lookup"><span data-stu-id="62d43-302">That means developers essentially have to decide between the following trade offs:</span></span>

1. <span data-ttu-id="62d43-303">Sicurezza in caso di scaricamento di assembly: è necessario allocazioni e pertanto `delegate` è già un'opzione sufficiente.</span><span class="sxs-lookup"><span data-stu-id="62d43-303">Safety in the face of assembly unloading: this requires allocations and hence `delegate` is already a sufficient option.</span></span>
1. <span data-ttu-id="62d43-304">Nessun aspetto di sicurezza in caso di scaricamento dell'assembly: usare un `delegate*` .</span><span class="sxs-lookup"><span data-stu-id="62d43-304">No safety in face of assembly unloading: use a `delegate*`.</span></span> <span data-ttu-id="62d43-305">Questo può essere incluso in un oggetto `struct` per consentire l'utilizzo all'esterno `unsafe` di un contesto nel resto del codice.</span><span class="sxs-lookup"><span data-stu-id="62d43-305">This can be wrapped in a `struct` to allow usage outside an `unsafe` context in the rest of the code.</span></span>
