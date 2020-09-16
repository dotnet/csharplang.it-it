---
ms.openlocfilehash: 904efa11b08027f45243974e5a25c9957daca5a3
ms.sourcegitcommit: fcf884f7f8a2c2c7d925a67c2a7fad22e09b9506
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682284"
---
# <a name="compile-time-enforcement-of-safety-for-ref-like-types"></a><span data-ttu-id="055c6-101">Imposizione del tempo di compilazione per la sicurezza per i tipi simili a Ref</span><span class="sxs-lookup"><span data-stu-id="055c6-101">Compile time enforcement of safety for ref-like types</span></span>

## <a name="introduction"></a><span data-ttu-id="055c6-102">Introduzione</span><span class="sxs-lookup"><span data-stu-id="055c6-102">Introduction</span></span>

<span data-ttu-id="055c6-103">Il motivo principale per le regole di sicurezza aggiuntive quando si gestiscono tipi come `Span<T>` e `ReadOnlySpan<T>` è che tali tipi devono essere limitati allo stack di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="055c6-103">The main reason for the additional safety rules when dealing with types like `Span<T>` and `ReadOnlySpan<T>` is that such types must be confined to the execution stack.</span></span>
 
<span data-ttu-id="055c6-104">Esistono due motivi per cui `Span<T>` e i tipi simili devono essere tipi di solo stack.</span><span class="sxs-lookup"><span data-stu-id="055c6-104">There are two reasons why `Span<T>` and similar types must be a stack-only types.</span></span>

1. <span data-ttu-id="055c6-105">`Span<T>` è semanticamente uno struct che contiene un riferimento e un intervallo `(ref T data, int length)` .</span><span class="sxs-lookup"><span data-stu-id="055c6-105">`Span<T>` is semantically a struct containing a reference and a range - `(ref T data, int length)`.</span></span> <span data-ttu-id="055c6-106">Indipendentemente dall'implementazione effettiva, le Scritture in tale struct non sarebbero atomiche.</span><span class="sxs-lookup"><span data-stu-id="055c6-106">Regardless of actual implementation, writes to such struct would not be atomic.</span></span> <span data-ttu-id="055c6-107">Il "strappo" simultaneo di tale struct potrebbe causare la `length` mancata corrispondenza di `data` , causando accessi fuori intervallo e violazioni di indipendenza dai tipi, che in definitiva potrebbero causare il danneggiamento dell'heap GC nel codice apparentemente "sicuro".</span><span class="sxs-lookup"><span data-stu-id="055c6-107">Concurrent "tearing" of such struct would lead to the possibility of `length` not matching the `data`, causing out-of-range accesses and type-safety violations, which ultimately could result in GC heap corruption in seemingly "safe" code.</span></span>
2. <span data-ttu-id="055c6-108">Alcune implementazioni di `Span<T>` contengono letteralmente un puntatore gestito in uno dei relativi campi.</span><span class="sxs-lookup"><span data-stu-id="055c6-108">Some implementations of `Span<T>` literally contain a managed pointer in one of its fields.</span></span> <span data-ttu-id="055c6-109">I puntatori gestiti non sono supportati come campi di oggetti heap e il codice che gestisce per inserire un puntatore gestito nell'heap GC in genere si arresta in modo anomalo in fase di JIT.</span><span class="sxs-lookup"><span data-stu-id="055c6-109">Managed pointers are not supported as fields of heap objects and code that manages to put a managed pointer on the GC heap typically crashes at JIT time.</span></span>

<span data-ttu-id="055c6-110">Se le istanze di `Span<T>` sono vincolate a esistere solo nello stack di esecuzione, vengono risolti tutti i problemi precedenti.</span><span class="sxs-lookup"><span data-stu-id="055c6-110">All the above problems would be alleviated if instances of `Span<T>` are constrained to exist only on the execution stack.</span></span> 

<span data-ttu-id="055c6-111">Un ulteriore problema si verifica a causa della composizione.</span><span class="sxs-lookup"><span data-stu-id="055c6-111">An additional problem arises due to composition.</span></span> <span data-ttu-id="055c6-112">È in genere consigliabile compilare tipi di dati più complessi che incorporano `Span<T>` istanze di e `ReadOnlySpan<T>` .</span><span class="sxs-lookup"><span data-stu-id="055c6-112">It would be generally desirable to build more complex data types that would embed `Span<T>` and `ReadOnlySpan<T>` instances.</span></span> <span data-ttu-id="055c6-113">Tali tipi compositi dovrebbero essere struct e condividere tutti i rischi e i requisiti di `Span<T>` .</span><span class="sxs-lookup"><span data-stu-id="055c6-113">Such composite types would have to be structs and would share all the hazards and requirements of `Span<T>`.</span></span> <span data-ttu-id="055c6-114">Di conseguenza, le regole di sicurezza descritte di seguito dovrebbero essere visualizzate come applicabile all'intera gamma di **_tipi simili a Ref_**.</span><span class="sxs-lookup"><span data-stu-id="055c6-114">As a result the safety rules described here should be viewed as applicable to the whole range of **_ref-like types_**.</span></span>

<span data-ttu-id="055c6-115">La [specifica del linguaggio bozza](#draft-language-specification) ha lo scopo di garantire che i valori di un tipo simile a ref vengano eseguiti solo nello stack.</span><span class="sxs-lookup"><span data-stu-id="055c6-115">The [draft language specification](#draft-language-specification) is intended to ensure that values of a ref-like type occur only on the stack.</span></span>

## <a name="generalized-ref-like-types-in-source-code"></a><span data-ttu-id="055c6-116">Tipi generalizzati `ref-like` nel codice sorgente</span><span class="sxs-lookup"><span data-stu-id="055c6-116">Generalized `ref-like` types in source code</span></span>

<span data-ttu-id="055c6-117">`ref-like` gli struct sono contrassegnati in modo esplicito nel codice sorgente usando il `ref` modificatore:</span><span class="sxs-lookup"><span data-stu-id="055c6-117">`ref-like` structs are explicitly marked in the source code using `ref` modifier:</span></span>

```csharp
ref struct TwoSpans<T>
{
    // can have ref-like instance fields
    public Span<T> first;
    public Span<T> second;
} 

// error: arrays of ref-like types are not allowed. 
TwoSpans<T>[] arr = null;

```

<span data-ttu-id="055c6-118">La designazione di uno struct come ref-like consentirà allo struct di avere campi di istanza di tipo ref e renderà disponibili anche tutti i requisiti di tipi simili a Ref applicabili allo struct.</span><span class="sxs-lookup"><span data-stu-id="055c6-118">Designating a struct as ref-like will allow the struct to have ref-like instance fields and will also make all the requirements of ref-like types applicable to the struct.</span></span> 

## <a name="metadata-representation-or-ref-like-structs"></a><span data-ttu-id="055c6-119">Rappresentazione dei metadati o struct di tipo Ref</span><span class="sxs-lookup"><span data-stu-id="055c6-119">Metadata representation or ref-like structs</span></span>

<span data-ttu-id="055c6-120">Gli struct di tipo ref verranno contrassegnati con l'attributo **System. Runtime. CompilerServices. IsRefLikeAttribute** .</span><span class="sxs-lookup"><span data-stu-id="055c6-120">Ref-like structs will be marked with **System.Runtime.CompilerServices.IsRefLikeAttribute** attribute.</span></span>

<span data-ttu-id="055c6-121">L'attributo verrà aggiunto alle librerie di base comuni, ad esempio `mscorlib` .</span><span class="sxs-lookup"><span data-stu-id="055c6-121">The attribute will be added to common base libraries such as `mscorlib`.</span></span> <span data-ttu-id="055c6-122">Se l'attributo non è disponibile, il compilatore genererà uno interno in modo analogo ad altri attributi incorporati su richiesta, ad esempio `IsReadOnlyAttribute` .</span><span class="sxs-lookup"><span data-stu-id="055c6-122">In a case if the attribute is not available, compiler will generate an internal one similarly to other embedded-on-demand attributes such as `IsReadOnlyAttribute`.</span></span>

<span data-ttu-id="055c6-123">Viene eseguita una misura aggiuntiva per impedire l'uso di struct di tipo Ref nei compilatori che non conoscono le regole di sicurezza (sono inclusi i compilatori C# precedenti a quello in cui è implementata questa funzionalità).</span><span class="sxs-lookup"><span data-stu-id="055c6-123">An additional measure will be taken to prevent the use of ref-like structs in compilers not familiar with the safety rules (this includes C# compilers prior to the one in which this feature is implemented).</span></span> 

<span data-ttu-id="055c6-124">Non avendo altre alternative valide che funzionano nei compilatori obsoleti senza manutenzione, `Obsolete` viene aggiunto un attributo con una stringa nota a tutti gli struct simili a Ref.</span><span class="sxs-lookup"><span data-stu-id="055c6-124">Having no other good alternatives that work in old compilers without servicing, an `Obsolete` attribute with a known string will be added to all ref-like structs.</span></span> <span data-ttu-id="055c6-125">I compilatori che sanno come usare i tipi di tipo ref ignoreranno questo particolare formato di `Obsolete` .</span><span class="sxs-lookup"><span data-stu-id="055c6-125">Compilers that know how to use ref-like types will ignore this particular form of `Obsolete`.</span></span>

<span data-ttu-id="055c6-126">Rappresentazione tipica dei metadati:</span><span class="sxs-lookup"><span data-stu-id="055c6-126">A typical metadata representation:</span></span>

```csharp
    [IsRefLike]
    [Obsolete("Types with embedded references are not supported in this version of your compiler.")]
    public struct TwoSpans<T>
    {
       // . . . .
    }
```

<span data-ttu-id="055c6-127">Nota: non è l'obiettivo di fare in modo che qualsiasi uso di tipi simili a Ref nei compilatori precedenti non riesca 100%.</span><span class="sxs-lookup"><span data-stu-id="055c6-127">NOTE: it is not the goal to make it so that any use of ref-like types on old compilers fails 100%.</span></span> <span data-ttu-id="055c6-128">Questo è difficile da raggiungere e non è strettamente necessario.</span><span class="sxs-lookup"><span data-stu-id="055c6-128">That is hard to achieve and is not strictly necessary.</span></span> <span data-ttu-id="055c6-129">Ad esempio, esiste sempre un modo per aggirare l' `Obsolete` uso del codice dinamico o, ad esempio, la creazione di una matrice di tipi simili a Ref tramite reflection.</span><span class="sxs-lookup"><span data-stu-id="055c6-129">For example there would always be a way to get around the `Obsolete` using dynamic code or, for example, creating an array of ref-like types through reflection.</span></span>

<span data-ttu-id="055c6-130">In particolare, se l'utente vuole effettivamente inserire un `Obsolete` `Deprecated` attributo o su un tipo simile a ref, non sarà possibile scegliere un valore diverso da quello predefinito, perché non `Obsolete` è possibile applicare l'attributo più di una volta.</span><span class="sxs-lookup"><span data-stu-id="055c6-130">In particular, if user wants to actually put an `Obsolete` or `Deprecated` attribute on a ref-like type, we will have no choice other than not emitting the predefined one since `Obsolete` attribute cannot be applied more than once..</span></span>  

## <a name="examples"></a><span data-ttu-id="055c6-131">Esempi:</span><span class="sxs-lookup"><span data-stu-id="055c6-131">Examples:</span></span>

```csharp
SpanLikeType M1(ref SpanLikeType x, Span<byte> y)
{
    // this is all valid, unconcerned with stack-referring stuff
    var local = new SpanLikeType(y);
    x = local;
    return x;
}

void Test1(ref SpanLikeType param1, Span<byte> param2)
{
    Span<byte> stackReferring1 = stackalloc byte[10];
    var stackReferring2 = new SpanLikeType(stackReferring1);

    // this is allowed
    stackReferring2 = M1(ref stackReferring2, stackReferring1);

    // this is NOT allowed
    stackReferring2 = M1(ref param1, stackReferring1);

    // this is NOT allowed
    param1 = M1(ref stackReferring2, stackReferring1);

    // this is NOT allowed
    param2 = stackReferring1.Slice(10);

    // this is allowed
    param1 = new SpanLikeType(param2);

    // this is allowed
    stackReferring2 = param1;
}

ref SpanLikeType M2(ref SpanLikeType x)
{
    return ref x;
}

ref SpanLikeType Test2(ref SpanLikeType param1, Span<byte> param2)
{
    Span<byte> stackReferring1 = stackalloc byte[10];
    var stackReferring2 = new SpanLikeType(stackReferring1);

    ref var stackReferring3 = M2(ref stackReferring2);

    // this is allowed
    stackReferring3 = M1(ref stackReferring2, stackReferring1);

    // this is allowed
    M2(ref stackReferring3) = stackReferring2;

    // this is NOT allowed
    M1(ref param1) = stackReferring2;

    // this is NOT allowed
    param1 = stackReferring3;

    // this is NOT allowed
    return ref stackReferring3;

    // this is allowed
    return ref param1;
}

```

----------------

## <a name="draft-language-specification"></a><span data-ttu-id="055c6-132">Specifica del linguaggio bozza</span><span class="sxs-lookup"><span data-stu-id="055c6-132">Draft language specification</span></span>

<span data-ttu-id="055c6-133">Di seguito viene descritto un set di regole di sicurezza per i tipi simili `ref struct` a ref per garantire che i valori di questi tipi vengano eseguiti solo nello stack.</span><span class="sxs-lookup"><span data-stu-id="055c6-133">Below we describe a set of safety rules for ref-like types (`ref struct`s) to ensure that values of these types occur only on the stack.</span></span> <span data-ttu-id="055c6-134">Un set di regole di sicurezza diverso e più semplice è possibile se le variabili locali non possono essere passate per riferimento.</span><span class="sxs-lookup"><span data-stu-id="055c6-134">A different, simpler set of safety rules would be possible if locals cannot be passed by reference.</span></span> <span data-ttu-id="055c6-135">Questa specifica consente inoltre la riassegnazione sicura delle variabili locali Ref.</span><span class="sxs-lookup"><span data-stu-id="055c6-135">This specification would also permit the safe reassignment of ref locals.</span></span>

### <a name="overview"></a><span data-ttu-id="055c6-136">Panoramica</span><span class="sxs-lookup"><span data-stu-id="055c6-136">Overview</span></span>

<span data-ttu-id="055c6-137">Si associa a ogni espressione in fase di compilazione il concetto di ambito a cui l'espressione può eseguire l'escape, ovvero "safe-to-escape".</span><span class="sxs-lookup"><span data-stu-id="055c6-137">We associate with each expression at compile-time the concept of what scope that expression is permitted to escape to, "safe-to-escape".</span></span> <span data-ttu-id="055c6-138">Analogamente, per ogni lvalue si mantiene un concetto dell'ambito a cui è consentito l'escape di un riferimento a esso, ovvero "Ref-safe-to-escape".</span><span class="sxs-lookup"><span data-stu-id="055c6-138">Similarly, for each lvalue we maintain a concept of what scope a reference to it is permitted to escape to, "ref-safe-to-escape".</span></span> <span data-ttu-id="055c6-139">Per un'espressione lvalue specificata, potrebbero essere diversi.</span><span class="sxs-lookup"><span data-stu-id="055c6-139">For a given lvalue expression, these may be different.</span></span>

<span data-ttu-id="055c6-140">Si tratta di una procedura analoga alla "restituzione sicura" della funzionalità variabili locali Ref, ma con granularità più fine.</span><span class="sxs-lookup"><span data-stu-id="055c6-140">These are analogous to the "safe to return" of the ref locals feature, but it is more fine-grained.</span></span> <span data-ttu-id="055c6-141">Dove il "safe-to-return" di un'espressione registra solo se (o meno) può sfuggire al metodo di inclusione nel suo complesso, i record di tipo safe-to-escape per cui è possibile eseguire l'escape, ovvero l'ambito di cui non è possibile evitare l'escape.</span><span class="sxs-lookup"><span data-stu-id="055c6-141">Where the "safe-to-return" of an expression records only whether (or not) it may escape the enclosing method as a whole, the safe-to-escape records which scope it may escape to (which scope it may not escape beyond).</span></span> <span data-ttu-id="055c6-142">Il meccanismo di sicurezza di base viene applicato come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="055c6-142">The basic safety mechanism is enforced as follows.</span></span> <span data-ttu-id="055c6-143">Data un'assegnazione da un'espressione E1 con un ambito sicuro a escape S1, a un'espressione (lvalue) E2 con ambito safe-to-escape S2, si tratta di un errore se S2 è un ambito più ampio rispetto a S1.</span><span class="sxs-lookup"><span data-stu-id="055c6-143">Given an assignment from an expression E1 with a safe-to-escape scope S1, to an (lvalue) expression E2 with safe-to-escape scope S2, it is an error if S2 is a wider scope than S1.</span></span> <span data-ttu-id="055c6-144">In base alla costruzione, i due ambiti S1 e S2 si trovano in una relazione di annidamento, perché un'espressione valida è sempre sicura per essere restituita da un ambito che include l'espressione.</span><span class="sxs-lookup"><span data-stu-id="055c6-144">By construction, the two scopes S1 and S2 are in a nesting relationship, because a legal expression is always safe-to-return from some scope enclosing the expression.</span></span>

<span data-ttu-id="055c6-145">Per il momento in cui è sufficiente, ai fini dell'analisi, per supportare solo due ambiti esterni al metodo e l'ambito di primo livello del metodo.</span><span class="sxs-lookup"><span data-stu-id="055c6-145">For the time being it is sufficient, for the purpose of the analysis, to support just two scopes - external to the method, and top-level scope of the method.</span></span> <span data-ttu-id="055c6-146">Ciò è dovuto al fatto che non è possibile creare valori di tipo Ref con ambiti interni e le variabili locali Ref non supportano la riassegnazione.</span><span class="sxs-lookup"><span data-stu-id="055c6-146">That is because ref-like values with inner scopes cannot be created and ref locals do not support re-assignment.</span></span> <span data-ttu-id="055c6-147">Le regole, tuttavia, possono supportare più di due livelli di ambito.</span><span class="sxs-lookup"><span data-stu-id="055c6-147">The rules, however, can support more than two scope levels.</span></span>

<span data-ttu-id="055c6-148">Di seguito sono riportate le regole precise per il calcolo dello stato di *sicurezza per la restituzione* di un'espressione e le regole relative alla legalità delle espressioni.</span><span class="sxs-lookup"><span data-stu-id="055c6-148">The precise rules for computing the *safe-to-return* status of an expression, and the rules governing the legality of expressions, follow.</span></span>

### <a name="ref-safe-to-escape"></a><span data-ttu-id="055c6-149">Ref-safe-to-escape</span><span class="sxs-lookup"><span data-stu-id="055c6-149">ref-safe-to-escape</span></span>

<span data-ttu-id="055c6-150">*Ref-safe-to-escape* è un ambito, che include un'espressione lvalue, a cui è possibile eseguire l'escape di un riferimento a lvalue in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="055c6-150">The *ref-safe-to-escape* is a scope, enclosing an lvalue expression, to which it is safe for a ref to the lvalue to escape to.</span></span> <span data-ttu-id="055c6-151">Se tale ambito è l'intero metodo, si afferma che un riferimento a lvalue è *sicuro per restituire* dal metodo.</span><span class="sxs-lookup"><span data-stu-id="055c6-151">If that scope is the entire method, we say that a ref to the lvalue is *safe to return* from the method.</span></span>

### <a name="safe-to-escape"></a><span data-ttu-id="055c6-152">da Safe a escape</span><span class="sxs-lookup"><span data-stu-id="055c6-152">safe-to-escape</span></span>

<span data-ttu-id="055c6-153">Il tipo *safe-to-escape* è un ambito, che racchiude un'espressione a cui è sicuro che il valore venga sottoposto a escape.</span><span class="sxs-lookup"><span data-stu-id="055c6-153">The *safe-to-escape* is a scope, enclosing an expression, to which it is safe for the value to escape to.</span></span> <span data-ttu-id="055c6-154">Se tale ambito è l'intero metodo, si afferma che il valore è *Safe per restituire* dal metodo.</span><span class="sxs-lookup"><span data-stu-id="055c6-154">If that scope is the entire method, we say that the value is *safe to return* from the method.</span></span>

<span data-ttu-id="055c6-155">Un'espressione il cui tipo non è un `ref struct` tipo è *safe-to-Return* dall'intero metodo contenitore.</span><span class="sxs-lookup"><span data-stu-id="055c6-155">An expression whose type is not a `ref struct` type is *safe-to-return* from the entire enclosing method.</span></span> <span data-ttu-id="055c6-156">In caso contrario, si fa riferimento alle regole seguenti.</span><span class="sxs-lookup"><span data-stu-id="055c6-156">Otherwise we refer to the rules below.</span></span>

#### <a name="parameters"></a><span data-ttu-id="055c6-157">Parametri</span><span class="sxs-lookup"><span data-stu-id="055c6-157">Parameters</span></span>

<span data-ttu-id="055c6-158">Un lvalue che designa un parametro formale è *ref-safe-to-escape* (per riferimento) come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="055c6-158">An lvalue designating a formal parameter is *ref-safe-to-escape* (by reference) as follows:</span></span>
- <span data-ttu-id="055c6-159">Se il parametro è un `ref` `out` parametro, o `in` , è *ref-safe-to-escape* dall'intero metodo (ad esempio tramite un' `return ref` istruzione); in caso contrario,.</span><span class="sxs-lookup"><span data-stu-id="055c6-159">If the parameter is a `ref`, `out`, or `in` parameter, it is *ref-safe-to-escape* from the entire method (e.g. by a `return ref` statement); otherwise</span></span>
- <span data-ttu-id="055c6-160">Se il parametro è il `this` parametro di un tipo struct, è di tipo *ref-safe-to-escape* nell'ambito di primo livello del metodo (ma non dall'intero metodo); [Esempio](#struct-this-escape) di</span><span class="sxs-lookup"><span data-stu-id="055c6-160">If the parameter is the `this` parameter of a struct type, it is *ref-safe-to-escape* to the top-level scope of the method (but not from the entire method itself); [Sample](#struct-this-escape)</span></span>
- <span data-ttu-id="055c6-161">In caso contrario, il parametro è un parametro di valore ed è *ref-safe-to-escape* per l'ambito di primo livello del metodo, ma non dal metodo stesso.</span><span class="sxs-lookup"><span data-stu-id="055c6-161">Otherwise the parameter is a value parameter, and it is *ref-safe-to-escape* to the top-level scope of the method (but not from the method itself).</span></span>

<span data-ttu-id="055c6-162">Un'espressione che è un rvalue che designa l'uso di un parametro formale è *safe-to-escape* (per valore) dall'intero metodo (ad esempio tramite un' `return` istruzione).</span><span class="sxs-lookup"><span data-stu-id="055c6-162">An expression that is an rvalue designating the use of a formal parameter is *safe-to-escape* (by value) from the entire method (e.g. by a `return` statement).</span></span> <span data-ttu-id="055c6-163">Questo vale anche per il `this` parametro.</span><span class="sxs-lookup"><span data-stu-id="055c6-163">This applies to the `this` parameter as well.</span></span>

#### <a name="locals"></a><span data-ttu-id="055c6-164">Variabili locali</span><span class="sxs-lookup"><span data-stu-id="055c6-164">Locals</span></span>

<span data-ttu-id="055c6-165">Un lvalue che designa una variabile locale è *ref-safe-to-escape* (per riferimento) come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="055c6-165">An lvalue designating a local variable is *ref-safe-to-escape* (by reference) as follows:</span></span>
- <span data-ttu-id="055c6-166">Se la variabile è una `ref` variabile, il relativo *ref-safe-to-escape* viene tratto da *ref-safe-to-escape* della relativa espressione di inizializzazione; in caso contrario,.</span><span class="sxs-lookup"><span data-stu-id="055c6-166">If the variable is a `ref` variable, then its *ref-safe-to-escape* is taken from the *ref-safe-to-escape* of its initializing expression; otherwise</span></span>
- <span data-ttu-id="055c6-167">La variabile è *ref-safe-to-escape* nell'ambito in cui è stata dichiarata.</span><span class="sxs-lookup"><span data-stu-id="055c6-167">The variable is *ref-safe-to-escape* the scope in which it was declared.</span></span>

<span data-ttu-id="055c6-168">Un'espressione che è un rvalue che designa l'uso di una variabile locale è *safe-to-escape* (per valore) come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="055c6-168">An expression that is an rvalue designating the use of a local variable is *safe-to-escape* (by value) as follows:</span></span>
- <span data-ttu-id="055c6-169">Tuttavia, la regola generale precedente, un oggetto locale il cui tipo non è un `ref struct` tipo è *safe-to-Return* dall'intero metodo contenitore.</span><span class="sxs-lookup"><span data-stu-id="055c6-169">But the general rule above, a local whose type is not a `ref struct` type is *safe-to-return* from the entire enclosing method.</span></span>
- <span data-ttu-id="055c6-170">Se la variabile è una variabile di iterazione di un `foreach` ciclo, l'ambito *safe-to-escape* della variabile è uguale a quello di *safe-to-escape* dell' `foreach` espressione del ciclo.</span><span class="sxs-lookup"><span data-stu-id="055c6-170">If the variable is an iteration variable of a `foreach` loop, then the variable's *safe-to-escape* scope is the same as the *safe-to-escape* of the `foreach` loop's expression.</span></span>
- <span data-ttu-id="055c6-171">Un oggetto locale di `ref struct` tipo e non inizializzato al momento della dichiarazione è *sicuro per essere restituito* dall'intero metodo contenitore.</span><span class="sxs-lookup"><span data-stu-id="055c6-171">A local of `ref struct` type and uninitialized at the point of declaration is *safe-to-return* from the entire enclosing method.</span></span>
- <span data-ttu-id="055c6-172">In caso contrario, il tipo della variabile è un `ref struct` tipo e la dichiarazione della variabile richiede un inizializzatore.</span><span class="sxs-lookup"><span data-stu-id="055c6-172">Otherwise the variable's type is a `ref struct` type, and the variable's declaration requires an initializer.</span></span> <span data-ttu-id="055c6-173">L'ambito di *sicurezza* della variabile è identico *a* quello del relativo inizializzatore.</span><span class="sxs-lookup"><span data-stu-id="055c6-173">The variable's *safe-to-escape* scope is the same as the *safe-to-escape* of its initializer.</span></span>

#### <a name="field-reference"></a><span data-ttu-id="055c6-174">Riferimento ai campi</span><span class="sxs-lookup"><span data-stu-id="055c6-174">Field reference</span></span>

<span data-ttu-id="055c6-175">Un lvalue che designa un riferimento a un campo, `e.F` , è *ref-safe-to-escape* (per riferimento) come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="055c6-175">An lvalue designating a reference to a field, `e.F`, is *ref-safe-to-escape* (by reference) as follows:</span></span>
- <span data-ttu-id="055c6-176">Se `e` è di un tipo riferimento, è *ref-safe-to-escape* dall'intero metodo; in caso contrario,</span><span class="sxs-lookup"><span data-stu-id="055c6-176">If `e` is of a reference type, it is *ref-safe-to-escape* from the entire method; otherwise</span></span>
- <span data-ttu-id="055c6-177">Se `e` è di un tipo di valore, il relativo *ref-safe-to-escape* viene tratto da *ref-safe-to-escape* di `e` .</span><span class="sxs-lookup"><span data-stu-id="055c6-177">If `e` is of a value type, its *ref-safe-to-escape* is taken from the *ref-safe-to-escape* of `e`.</span></span>

<span data-ttu-id="055c6-178">Un rvalue che designa un riferimento a un campo, `e.F` , dispone di un ambito *di tipo safe-to-escape* che corrisponde alla *sicurezza di escape* di `e` .</span><span class="sxs-lookup"><span data-stu-id="055c6-178">An rvalue designating a reference to a field, `e.F`, has a *safe-to-escape* scope that is the same as the *safe-to-escape* of `e`.</span></span>

#### <a name="operators-including-"></a><span data-ttu-id="055c6-179">Operatori che includono `?:`</span><span class="sxs-lookup"><span data-stu-id="055c6-179">Operators including `?:`</span></span>

<span data-ttu-id="055c6-180">L'applicazione di un operatore definito dall'utente viene considerata come una chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="055c6-180">The application of a user-defined operator is treated as a method invocation.</span></span>

<span data-ttu-id="055c6-181">Per un operatore che restituisce un rvalue, ad esempio `e1 + e2` o `c ? e1 : e2` , il valore *safe-to-escape* del risultato è l'ambito più stretto tra i tipi di *sicurezza di escape* degli operandi dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="055c6-181">For an operator that yields an rvalue, such as `e1 + e2` or `c ? e1 : e2`, the *safe-to-escape* of the result is the narrowest scope among the *safe-to-escape* of the operands of the operator.</span></span>  <span data-ttu-id="055c6-182">Di conseguenza, per un operatore unario che restituisce un rvalue, ad esempio `+e` , il *safe-to-escape* del risultato è la sicurezza di *escape* dell'operando.</span><span class="sxs-lookup"><span data-stu-id="055c6-182">As a consequence, for a unary operator that yields an rvalue, such as `+e`, the *safe-to-escape* of the result is the *safe-to-escape* of the operand.</span></span>

<span data-ttu-id="055c6-183">Per un operatore che restituisce un lvalue, ad esempio `c ? ref e1 : ref e2`</span><span class="sxs-lookup"><span data-stu-id="055c6-183">For an operator that yields an lvalue, such as `c ? ref e1 : ref e2`</span></span>
- <span data-ttu-id="055c6-184">*ref-safe-to-escape* del risultato è l'ambito più restrittivo tra *ref-safe-to-escape* degli operandi dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="055c6-184">the *ref-safe-to-escape* of the result is the narrowest scope among the *ref-safe-to-escape* of the operands of the operator.</span></span>
- <span data-ttu-id="055c6-185">il *safe-to-escape* degli operandi deve essere d'accordo e questo è il *safe-to-escape* del lvalue risultante.</span><span class="sxs-lookup"><span data-stu-id="055c6-185">the *safe-to-escape* of the operands must agree, and that is the *safe-to-escape* of the resulting lvalue.</span></span>

#### <a name="method-invocation"></a><span data-ttu-id="055c6-186">Chiamata al metodo</span><span class="sxs-lookup"><span data-stu-id="055c6-186">Method invocation</span></span>

<span data-ttu-id="055c6-187">Un lvalue risultante da una chiamata al metodo che restituisce un riferimento `e1.M(e2, ...)` è *ref-safe-to-escape* il più piccolo degli ambiti seguenti:</span><span class="sxs-lookup"><span data-stu-id="055c6-187">An lvalue resulting from a ref-returning method invocation `e1.M(e2, ...)` is *ref-safe-to-escape* the smallest of the following scopes:</span></span>
- <span data-ttu-id="055c6-188">Intero metodo contenitore</span><span class="sxs-lookup"><span data-stu-id="055c6-188">The entire enclosing method</span></span>
- <span data-ttu-id="055c6-189">*ref-safe-to-escape* di tutte le `ref` `out` espressioni di argomento e (escluso il ricevitore)</span><span class="sxs-lookup"><span data-stu-id="055c6-189">the *ref-safe-to-escape* of all `ref` and `out` argument expressions (excluding the receiver)</span></span>
- <span data-ttu-id="055c6-190">Per ogni `in` parametro del metodo, se esiste un'espressione corrispondente che è un lvalue, il relativo *ref-safe-to-escape*, in caso contrario l'ambito di inclusione più vicino</span><span class="sxs-lookup"><span data-stu-id="055c6-190">For each `in` parameter of the method, if there is a corresponding expression that is an lvalue, its *ref-safe-to-escape*, otherwise the nearest enclosing scope</span></span>
- <span data-ttu-id="055c6-191">il carattere di *escape sicuro per* tutte le espressioni di argomento (incluso il ricevitore)</span><span class="sxs-lookup"><span data-stu-id="055c6-191">the *safe-to-escape* of all argument expressions (including the receiver)</span></span>

> <span data-ttu-id="055c6-192">Nota: l'ultimo punto elenco è necessario per gestire il codice, ad esempio</span><span class="sxs-lookup"><span data-stu-id="055c6-192">Note: the last bullet is necessary to handle code such as</span></span>
> ```csharp
> var sp = new Span(...)
> return ref sp[0];
> ```
> <span data-ttu-id="055c6-193">oppure</span><span class="sxs-lookup"><span data-stu-id="055c6-193">or</span></span>
> ```csharp
> return ref M(sp, 0);
> ```

<span data-ttu-id="055c6-194">Un rvalue risultante da una chiamata al metodo `e1.M(e2, ...)` è di tipo *safe-to-escape* dal più piccolo degli ambiti seguenti:</span><span class="sxs-lookup"><span data-stu-id="055c6-194">An rvalue resulting from a method invocation `e1.M(e2, ...)` is *safe-to-escape* from the smallest of the following scopes:</span></span>
- <span data-ttu-id="055c6-195">Intero metodo contenitore</span><span class="sxs-lookup"><span data-stu-id="055c6-195">The entire enclosing method</span></span>
- <span data-ttu-id="055c6-196">il carattere di *escape sicuro per* tutte le espressioni di argomento (incluso il ricevitore)</span><span class="sxs-lookup"><span data-stu-id="055c6-196">the *safe-to-escape* of all argument expressions (including the receiver)</span></span>

#### <a name="an-rvalue"></a><span data-ttu-id="055c6-197">Rvalue</span><span class="sxs-lookup"><span data-stu-id="055c6-197">An Rvalue</span></span>
<span data-ttu-id="055c6-198">Un rvalue è *ref-safe-to-escape* dall'ambito di inclusione più vicino.</span><span class="sxs-lookup"><span data-stu-id="055c6-198">An rvalue is *ref-safe-to-escape* from the nearest enclosing scope.</span></span> <span data-ttu-id="055c6-199">Questo problema si verifica ad esempio in una chiamata, ad esempio `M(ref d.Length)` dove `d` è di tipo `dynamic` .</span><span class="sxs-lookup"><span data-stu-id="055c6-199">This occurs for example in an invocation such as `M(ref d.Length)` where `d` is of type `dynamic`.</span></span> <span data-ttu-id="055c6-200">È inoltre coerente con (e probabilmente sussume) la gestione degli argomenti corrispondenti ai `in` parametri.</span><span class="sxs-lookup"><span data-stu-id="055c6-200">It is also consistent with (and perhaps subsumes) our handling of arguments corresponding to `in` parameters.</span></span>

#### <a name="property-invocations"></a><span data-ttu-id="055c6-201">Chiamate di proprietà</span><span class="sxs-lookup"><span data-stu-id="055c6-201">Property invocations</span></span>

<span data-ttu-id="055c6-202">Chiamata di una proprietà ( `get` o `set` ) considerata come chiamata di metodo del metodo sottostante dalle regole precedenti.</span><span class="sxs-lookup"><span data-stu-id="055c6-202">A property invocation (either `get` or `set`) it treated as a method invocation of the underlying method by the above rules.</span></span>

#### `stackalloc`

<span data-ttu-id="055c6-203">Un'espressione stackalloc è un rvalue che è *sicuro* da escludere dall'ambito di primo livello del metodo (ma non dall'intero metodo).</span><span class="sxs-lookup"><span data-stu-id="055c6-203">A stackalloc expression is an rvalue that is *safe-to-escape* to the top-level scope of the method (but not from the entire method itself).</span></span>

#### <a name="constructor-invocations"></a><span data-ttu-id="055c6-204">Chiamate al costruttore</span><span class="sxs-lookup"><span data-stu-id="055c6-204">Constructor invocations</span></span>

<span data-ttu-id="055c6-205">Un' `new` espressione che richiama un costruttore rispetta le stesse regole di una chiamata al metodo considerata per restituire il tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="055c6-205">A `new` expression that invokes a constructor obeys the same rules as a method invocation that is considered to return the type being constructed.</span></span>

<span data-ttu-id="055c6-206">Inoltre, *safe-to-escape* non è più ampio del più piccolo tra quelli di tipo *safe-to-escape* di tutti gli argomenti o operandi delle espressioni dell'inizializzatore di oggetto, in modo ricorsivo se è presente l'inizializzatore.</span><span class="sxs-lookup"><span data-stu-id="055c6-206">In addition *safe-to-escape* is no wider than the smallest of the *safe-to-escape* of all arguments/operands of the object initializer expressions, recursively, if initializer is present.</span></span> 

#### <a name="span-constructor"></a><span data-ttu-id="055c6-207">Costruttore Span</span><span class="sxs-lookup"><span data-stu-id="055c6-207">Span constructor</span></span>
<span data-ttu-id="055c6-208">Il linguaggio si basa sul fatto `Span<T>` che non è presente un costruttore nel formato seguente:</span><span class="sxs-lookup"><span data-stu-id="055c6-208">The language relies on `Span<T>` not having a constructor of the following form:</span></span>

```csharp
void Example(ref int x)
{
    // Create a span of length one
    var span = new Span<int>(ref x); 
}
```

<span data-ttu-id="055c6-209">Un costruttore di questo tipo consente `Span<T>` di usare come campi non distinguibili da un `ref` campo.</span><span class="sxs-lookup"><span data-stu-id="055c6-209">Such a constructor makes `Span<T>` which are used as fields indistinguishable from a `ref` field.</span></span> <span data-ttu-id="055c6-210">Le regole di sicurezza descritte in questo documento dipendono dai `ref` campi che non sono un costrutto valido in C# o .NET.</span><span class="sxs-lookup"><span data-stu-id="055c6-210">The safety rules described in this document depend on `ref` fields not being a valid construct in C# or .NET.</span></span>

#### <a name="default-expressions"></a><span data-ttu-id="055c6-211">Espressioni `default`</span><span class="sxs-lookup"><span data-stu-id="055c6-211">`default` expressions</span></span>

<span data-ttu-id="055c6-212">Un' `default` espressione è *sicura per l'escape* dall'intero metodo contenitore.</span><span class="sxs-lookup"><span data-stu-id="055c6-212">A `default` expression is *safe-to-escape* from the entire enclosing method.</span></span>

## <a name="language-constraints"></a><span data-ttu-id="055c6-213">Vincoli della lingua</span><span class="sxs-lookup"><span data-stu-id="055c6-213">Language Constraints</span></span>

<span data-ttu-id="055c6-214">Si vuole garantire che nessuna `ref` variabile locale e nessuna variabile di tipo faccia `ref struct` riferimento alla memoria dello stack o a variabili che non sono più attive.</span><span class="sxs-lookup"><span data-stu-id="055c6-214">We wish to ensure that no `ref` local variable, and no variable of `ref struct` type, refers to stack memory or variables that are no longer alive.</span></span> <span data-ttu-id="055c6-215">Sono pertanto presenti i vincoli di linguaggio seguenti:</span><span class="sxs-lookup"><span data-stu-id="055c6-215">We therefore have the following language constraints:</span></span>

- <span data-ttu-id="055c6-216">Non è possibile rimuovere un parametro ref, né un oggetto locale Ref né un parametro o una variabile locale di un `ref struct` tipo in un'espressione lambda o in una funzione locale.</span><span class="sxs-lookup"><span data-stu-id="055c6-216">Neither a ref parameter, nor a ref local, nor a parameter or local of a `ref struct` type can be lifted into a lambda or local function.</span></span>

- <span data-ttu-id="055c6-217">Né un parametro ref né un parametro di un `ref struct` tipo possono essere un argomento in un metodo iteratore o un `async` metodo.</span><span class="sxs-lookup"><span data-stu-id="055c6-217">Neither a ref parameter nor a parameter of a `ref struct` type may be an argument on an iterator method or an `async` method.</span></span>

- <span data-ttu-id="055c6-218">Né un oggetto locale Ref né un oggetto locale di un `ref struct` tipo possono essere nell'ambito nel punto di un' `yield return` istruzione o di un' `await` espressione.</span><span class="sxs-lookup"><span data-stu-id="055c6-218">Neither a ref local, nor a local of a `ref struct` type may be in scope at the point of a `yield return` statement or an `await` expression.</span></span>

- <span data-ttu-id="055c6-219">Un `ref struct` tipo non può essere utilizzato come argomento di tipo o come tipo di elemento in un tipo di tupla.</span><span class="sxs-lookup"><span data-stu-id="055c6-219">A `ref struct` type may not be used as a type argument, or as an element type in a tuple type.</span></span>

- <span data-ttu-id="055c6-220">Un `ref struct` tipo non può essere il tipo dichiarato di un campo, ad eccezione del fatto che può essere il tipo dichiarato di un campo di istanza di un altro `ref struct` .</span><span class="sxs-lookup"><span data-stu-id="055c6-220">A `ref struct` type may not be the declared type of a field, except that it may be the declared type of an instance field of another `ref struct`.</span></span>

- <span data-ttu-id="055c6-221">Un `ref struct` tipo non può essere il tipo di elemento di una matrice.</span><span class="sxs-lookup"><span data-stu-id="055c6-221">A `ref struct` type may not be the element type of an array.</span></span>

- <span data-ttu-id="055c6-222">Un valore di un `ref struct` tipo non può essere boxed:</span><span class="sxs-lookup"><span data-stu-id="055c6-222">A value of a `ref struct` type may not be boxed:</span></span>
  - <span data-ttu-id="055c6-223">Non esiste alcuna conversione da un `ref struct` tipo al tipo `object` o al tipo `System.ValueType` .</span><span class="sxs-lookup"><span data-stu-id="055c6-223">There is no conversion from a `ref struct` type to the type `object` or the type `System.ValueType`.</span></span>
  - <span data-ttu-id="055c6-224">`ref struct`Non è possibile dichiarare un tipo per implementare alcuna interfaccia</span><span class="sxs-lookup"><span data-stu-id="055c6-224">A `ref struct` type may not be declared to implement any interface</span></span>
  - <span data-ttu-id="055c6-225">Nessun metodo di istanza dichiarato in `object` o in `System.ValueType` , ma non sottoposto a override in un `ref struct` tipo, può essere chiamato con un destinatario di quel `ref struct` tipo.</span><span class="sxs-lookup"><span data-stu-id="055c6-225">No instance method declared in `object` or in `System.ValueType` but not overridden in a `ref struct` type may be called with a receiver of that `ref struct` type.</span></span>
  - <span data-ttu-id="055c6-226">Nessun metodo di istanza di un `ref struct` tipo può essere acquisito dalla conversione del metodo in un tipo delegato.</span><span class="sxs-lookup"><span data-stu-id="055c6-226">No instance method of a `ref struct` type may be captured by method conversion to a delegate type.</span></span>

- <span data-ttu-id="055c6-227">Per una riassegnazione Ref `ref e1 = ref e2` , il *ref-safe-to-escape* di `e2` deve avere un ambito almeno uguale a quello di *ref-safe-to-escape* di `e1` .</span><span class="sxs-lookup"><span data-stu-id="055c6-227">For a ref reassignment `ref e1 = ref e2`, the *ref-safe-to-escape* of `e2` must be at least as wide a scope as the *ref-safe-to-escape* of `e1`.</span></span>

- <span data-ttu-id="055c6-228">Per un'istruzione ref return `return ref e1` , *ref-safe-to-escape* di `e1` deve essere *ref-safe-to-escape* dall'intero metodo.</span><span class="sxs-lookup"><span data-stu-id="055c6-228">For a ref return statement `return ref e1`, the *ref-safe-to-escape* of `e1` must be *ref-safe-to-escape* from the entire method.</span></span> <span data-ttu-id="055c6-229">(TODO: è necessaria anche una regola che `e1` deve essere *di tipo safe-to-escape* dall'intero metodo o è ridondante?)</span><span class="sxs-lookup"><span data-stu-id="055c6-229">(TODO: Do we also need a rule that `e1` must be *safe-to-escape* from the entire method, or is that redundant?)</span></span>

- <span data-ttu-id="055c6-230">Per un'istruzione return `return e1` , l'oggetto *safe-to-escape* di `e1` deve essere *safe-to-escape* dall'intero metodo.</span><span class="sxs-lookup"><span data-stu-id="055c6-230">For a return statement `return e1`, the *safe-to-escape* of `e1` must be *safe-to-escape* from the entire method.</span></span>

- <span data-ttu-id="055c6-231">Per un'assegnazione `e1 = e2` , se il tipo di `e1` è un `ref struct` tipo, il livello di *sicurezza di escape* di deve essere almeno uguale a quello `e2` di un ambito come *safe-to-escape* di `e1` .</span><span class="sxs-lookup"><span data-stu-id="055c6-231">For an assignment `e1 = e2`, if the type of `e1` is a `ref struct` type, then the *safe-to-escape* of `e2` must be at least as wide a scope as the *safe-to-escape* of `e1`.</span></span>

- <span data-ttu-id="055c6-232">Per una chiamata a un metodo se è presente `ref` un `out` argomento o di un `ref struct` tipo (incluso il ricevitore), con la *sicurezza di escape* E1, nessun argomento (incluso il ricevitore) può avere una *sicurezza di escape* più stretta di E1.</span><span class="sxs-lookup"><span data-stu-id="055c6-232">For a method invocation if there is a `ref` or `out` argument of a `ref struct` type (including the receiver), with *safe-to-escape* E1, then no argument (including the receiver) may have a narrower *safe-to-escape* than E1.</span></span> [<span data-ttu-id="055c6-233">Esempio</span><span class="sxs-lookup"><span data-stu-id="055c6-233">Sample</span></span>](#method-arguments-must-match)

- <span data-ttu-id="055c6-234">Una funzione locale o una funzione anonima non può fare riferimento a un parametro o locale di `ref struct` tipo dichiarato in un ambito di inclusione.</span><span class="sxs-lookup"><span data-stu-id="055c6-234">A local function or anonymous function may not refer to a local or parameter of `ref struct` type declared in an enclosing scope.</span></span>

> <span data-ttu-id="055c6-235">***Problema aperto:*** È necessaria una regola che consenta di generare un errore quando è necessario eseguire il versamento di un valore dello stack di un `ref struct` tipo in un'espressione await, ad esempio nel codice</span><span class="sxs-lookup"><span data-stu-id="055c6-235">***Open Issue:*** We need some rule that permits us to produce an error when needing to spill a stack value of a `ref struct` type at an await expression, for example in the code</span></span>
> ```csharp
> Foo(new Span<int>(...), await e2);
> ```

## <a name="explanations"></a><span data-ttu-id="055c6-236">Spiegazioni</span><span class="sxs-lookup"><span data-stu-id="055c6-236">Explanations</span></span>
<span data-ttu-id="055c6-237">Queste spiegazioni ed esempi illustrano il motivo per cui sono presenti molte delle regole di sicurezza precedenti</span><span class="sxs-lookup"><span data-stu-id="055c6-237">These explanations and samples help explain why many of the safety rules above exist</span></span>

### <a name="method-arguments-must-match"></a><span data-ttu-id="055c6-238">Gli argomenti del metodo devono corrispondere</span><span class="sxs-lookup"><span data-stu-id="055c6-238">Method Arguments Must Match</span></span>
<span data-ttu-id="055c6-239">Quando si richiama un metodo in cui è presente `out` un `ref` parametro, che `ref struct` include il ricevitore, è necessario che tutte `ref struct` le richieste abbiano la stessa durata.</span><span class="sxs-lookup"><span data-stu-id="055c6-239">When invoking a method where there is an `out`, `ref` parameter that is a `ref struct` including the receiver then all of the `ref struct` need to have the same lifetime.</span></span> <span data-ttu-id="055c6-240">Questa operazione è necessaria perché C# deve prendere tutte le decisioni relative alla sicurezza della durata in base alle informazioni disponibili nella firma del metodo e la durata dei valori nel sito di chiamata.</span><span class="sxs-lookup"><span data-stu-id="055c6-240">This is necessary because C# must make all of its decisions around lifetime safety based on the information available in the signature of the method and the lifetime of the values at the call site.</span></span> 

<span data-ttu-id="055c6-241">Quando sono presenti `ref` parametri che possono essere `ref struct` sostituiti con il contenuto.</span><span class="sxs-lookup"><span data-stu-id="055c6-241">When there are `ref` parameters that are `ref struct` then there is the possiblity they could swap around their contents.</span></span> <span data-ttu-id="055c6-242">Quindi, nel sito di chiamata è necessario assicurarsi che tutti questi **potenziali** swap siano compatibili.</span><span class="sxs-lookup"><span data-stu-id="055c6-242">Hence at the call site we must ensure all of these **potential** swaps are compatible.</span></span> <span data-ttu-id="055c6-243">Se la lingua non è stata applicata, sarà possibile scrivere codice errato come quello riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="055c6-243">If the language didn't enforce that then it will allow for bad code like the following.</span></span>

```csharp
void M1(ref Span<int> s1)
{
    Span<int> s2 = stackalloc int[1];
    Swap(ref s1, ref s2);
}

void Swap(ref Span<int> x, ref Span<int> y)
{
    // This will effectively assign the stackalloc to the s1 parameter and allow it
    // to escape to the caller of M1
    ref x = ref y; 
}
```

<span data-ttu-id="055c6-244">La restrizione sul ricevitore è necessaria perché mentre nessun contenuto è Ref-safe-to-escape, può archiviare i valori specificati.</span><span class="sxs-lookup"><span data-stu-id="055c6-244">The restriction on the receiver is necessary because while none of its contents are ref-safe-to-escape it can store the provided values.</span></span> <span data-ttu-id="055c6-245">Ciò significa che con durate non corrispondenti è possibile creare un foro di sicurezza dei tipi nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="055c6-245">This means with mismatched lifetimes you could create a type safety hole in the following way:</span></span>

```csharp
ref struct S
{
    public Span<int> Span;

    public void Set(Span<int> span)
    {
        Span = span;
    }
}

void Broken(ref S s)
{
    Span<int> span = stackalloc int[1];

    // The result of a stackalloc is now stored in s.Span and escaped to the caller
    // of Broken
    s.Set(span); 
}
```

### <a name="struct-this-escape"></a><span data-ttu-id="055c6-246">Struttura Escape</span><span class="sxs-lookup"><span data-stu-id="055c6-246">Struct This Escape</span></span>
<span data-ttu-id="055c6-247">Per quanto riguarda le regole di sicurezza, il `this` valore in un membro di istanza viene modellato come parametro per il membro.</span><span class="sxs-lookup"><span data-stu-id="055c6-247">When it comes to span safety rules, the `this` value in an instance member is modeled as a parameter to the member.</span></span> <span data-ttu-id="055c6-248">A questo punto `struct` , il tipo di `this` è in realtà `ref S` Where in a è `class` semplicemente `S` (per i membri di un `class / struct` denominato s).</span><span class="sxs-lookup"><span data-stu-id="055c6-248">Now for a `struct` the type of `this` is actually `ref S` where in a `class` it's simply `S` (for members of a `class / struct` named S).</span></span> 

<span data-ttu-id="055c6-249">`this`Dispone tuttavia di regole di escape diverse da quelle degli altri `ref` parametri.</span><span class="sxs-lookup"><span data-stu-id="055c6-249">Yet `this` has different escaping rules than other `ref` parameters.</span></span> <span data-ttu-id="055c6-250">In particolare, non è Ref-safe-to-escape mentre altri parametri sono:</span><span class="sxs-lookup"><span data-stu-id="055c6-250">Specifically it is not ref-safe-to-escape while other parameters are:</span></span>

```csharp
ref struct S
{ 
    int Field;

    // Illegal because `this` isn't safe to escape as ref
    ref int Get() => ref Field;

    // Legal
    ref int GetParam(ref int p) => ref p;
}
```

<span data-ttu-id="055c6-251">Il motivo di questa restrizione è in realtà sufficiente per la `struct` chiamata del membro.</span><span class="sxs-lookup"><span data-stu-id="055c6-251">The reason for this restriction actually has little to do with `struct` member invocation.</span></span> <span data-ttu-id="055c6-252">Esistono alcune regole che devono essere elaborate in relazione alla chiamata del membro sui membri in `struct` cui il ricevitore è un rvalue.</span><span class="sxs-lookup"><span data-stu-id="055c6-252">There are some rules that need to be worked out with respect to member invocation on `struct` members where the receiver is an rvalue.</span></span> <span data-ttu-id="055c6-253">Ma questo approccio è molto intuitivo.</span><span class="sxs-lookup"><span data-stu-id="055c6-253">But that is very approachable.</span></span> 

<span data-ttu-id="055c6-254">Il motivo di questa restrizione riguarda effettivamente la chiamata all'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="055c6-254">The reason for this restriction is actually about interface invocation.</span></span> <span data-ttu-id="055c6-255">In particolare, si tratta di un valore che indica se l'esempio seguente non deve o non deve essere compilato;</span><span class="sxs-lookup"><span data-stu-id="055c6-255">Specifically it comes down to whether or not the following sample should or should not compile;</span></span>

```csharp
interface I1
{
    ref int Get();
}

ref int Use<T>(T p)
    where T : I1
{
    return ref p.Get();
}
```

<span data-ttu-id="055c6-256">Si consideri il caso `T` in cui viene creata un'istanza di come `struct` .</span><span class="sxs-lookup"><span data-stu-id="055c6-256">Consider the case where `T` is instantiated as a `struct`.</span></span> <span data-ttu-id="055c6-257">Se il `this` parametro è Ref-safe-to-escape, il valore restituito di `p.Get` può puntare allo stack (in particolare può essere un campo all'interno del tipo di cui è stata creata un'istanza di `T` ).</span><span class="sxs-lookup"><span data-stu-id="055c6-257">If the `this` parameter is ref-safe-to-escape then the return of `p.Get` could point to the stack (specifically it could be a field inside of the instantiated type of `T`).</span></span> <span data-ttu-id="055c6-258">Ciò significa che il linguaggio non può consentire la compilazione di questo esempio perché potrebbe restituire un oggetto `ref` a una posizione dello stack.</span><span class="sxs-lookup"><span data-stu-id="055c6-258">That means the language could not allow this sample to compile as it could be returning a `ref` to a stack location.</span></span> <span data-ttu-id="055c6-259">D'altra parte, se `this` non è Ref-safe-to-escape, `p.Get` non può fare riferimento allo stack e pertanto è sicuro restituire.</span><span class="sxs-lookup"><span data-stu-id="055c6-259">On the other hand if `this` is not ref-safe-to-escape then `p.Get` cannot refer to the stack and hence it's safe to return.</span></span> 

<span data-ttu-id="055c6-260">Questo è il motivo per cui l'evasione di `this` in un `struct` è molto importante per le interfacce.</span><span class="sxs-lookup"><span data-stu-id="055c6-260">This is why the escapability of `this` in a `struct` is really all about interfaces.</span></span> <span data-ttu-id="055c6-261">Questa operazione può essere eseguita, ma presenta un compromesso.</span><span class="sxs-lookup"><span data-stu-id="055c6-261">It can absolutely be made to work but it has a trade off.</span></span> <span data-ttu-id="055c6-262">Alla fine, la progettazione si è rivelata a favore di rendere più flessibili le interfacce.</span><span class="sxs-lookup"><span data-stu-id="055c6-262">The design eventually came down in favor of making interfaces more flexible.</span></span> 

<span data-ttu-id="055c6-263">In futuro, tuttavia, è possibile che ci si rilassi.</span><span class="sxs-lookup"><span data-stu-id="055c6-263">There is potential for us to relax this in the future though.</span></span> 

## <a name="future-considerations"></a><span data-ttu-id="055c6-264">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="055c6-264">Future Considerations</span></span>

### <a name="length-one-spant-over-ref-values"></a><span data-ttu-id="055c6-265">Lunghezza di un intervallo \<T> rispetto ai valori Ref</span><span class="sxs-lookup"><span data-stu-id="055c6-265">Length one Span\<T> over ref values</span></span>
<span data-ttu-id="055c6-266">Sebbene non sia attualmente valido, esistono casi in cui la creazione di una lunghezza di un' `Span<T>` istanza su un valore può risultare utile:</span><span class="sxs-lookup"><span data-stu-id="055c6-266">Though not legal today there are cases where creating a length one `Span<T>` instance over a value would be beneficial:</span></span>

```csharp
void RefExample()
{
    int x = ...;

    // Today creating a length one Span<int> requires a stackalloc and a new 
    // local
    Span<int> span1 = stackalloc [] { x };
    Use(span1);
    x = span1[0]; 

    // Simpler to just allow length one span
    var span2 = new Span<int>(ref x);
    Use(span2);
}
```

<span data-ttu-id="055c6-267">Questa funzionalità risulta più interessante se si rilevano le restrizioni sui [buffer a dimensione fissa](https://github.com/dotnet/csharplang/blob/master/proposals/fixed-sized-buffers.md) in quanto consentono di ottenere `Span<T>` una lunghezza ancora maggiore.</span><span class="sxs-lookup"><span data-stu-id="055c6-267">This feature gets more compelling if we lift the restrictions on [fixed sized buffers](https://github.com/dotnet/csharplang/blob/master/proposals/fixed-sized-buffers.md) as it would allow for `Span<T>` instances of even greater length.</span></span> 

<span data-ttu-id="055c6-268">Se è necessario arrestare questo percorso, è possibile che il linguaggio soddisfi questo problema garantendo che tali `Span<T>` istanze fossero solo verso il basso.</span><span class="sxs-lookup"><span data-stu-id="055c6-268">If there is ever a need to go down this path then the language could accommodate this by ensuring such `Span<T>` instances were downward facing only.</span></span> <span data-ttu-id="055c6-269">Si tratta di una condizione di *escape sicura* per l'ambito in cui sono stati creati.</span><span class="sxs-lookup"><span data-stu-id="055c6-269">That is they were only ever *safe-to-escape* to the scope in which they were created.</span></span> <span data-ttu-id="055c6-270">In questo modo si garantisce che la lingua non debba mai considerare un `ref` valore di escape per un metodo tramite un valore `ref struct` restituito o un campo di `ref struct` .</span><span class="sxs-lookup"><span data-stu-id="055c6-270">This ensure the language never had to consider a `ref` value escaping a method via a `ref struct` return or field of `ref struct`.</span></span> <span data-ttu-id="055c6-271">Questo potrebbe anche richiedere ulteriori modifiche per riconoscere questi costruttori come l'acquisizione di un `ref` parametro in questo modo.</span><span class="sxs-lookup"><span data-stu-id="055c6-271">This would likely also require further changes to recognize such constructors as capturing a `ref` parameter in this way though.</span></span>
