---
ms.openlocfilehash: 02ebf35fc43fee1183be5d0118e03e02b17f41a9
ms.sourcegitcommit: 38908f71e529669d1e049db6c6cce2cad65c1727
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94498310"
---
# <a name="pattern-match-spanchar-on-a-constant-string"></a><span data-ttu-id="ca4f5-101">Criterio `Span<char>` di corrispondenza in una stringa costante</span><span class="sxs-lookup"><span data-stu-id="ca4f5-101">Pattern match `Span<char>` on a constant string</span></span>

* <span data-ttu-id="ca4f5-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="ca4f5-102">[x] Proposed</span></span>
* <span data-ttu-id="ca4f5-103">[x] prototipo: completato</span><span class="sxs-lookup"><span data-stu-id="ca4f5-103">[x] Prototype: Completed</span></span>
* <span data-ttu-id="ca4f5-104">Implementazione di [x]: in corso</span><span class="sxs-lookup"><span data-stu-id="ca4f5-104">[x] Implementation: In Progress</span></span>
* <span data-ttu-id="ca4f5-105">[] Specifica: non avviata</span><span class="sxs-lookup"><span data-stu-id="ca4f5-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="ca4f5-106">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="ca4f5-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="ca4f5-107">Consentire ai criteri di ricerca `Span<char>` e a `ReadOnlySpan<char>` su una stringa costante.</span><span class="sxs-lookup"><span data-stu-id="ca4f5-107">Permit pattern matching a `Span<char>` and a `ReadOnlySpan<char>` on a constant string.</span></span>

## <a name="motivation"></a><span data-ttu-id="ca4f5-108">Motivazione</span><span class="sxs-lookup"><span data-stu-id="ca4f5-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="ca4f5-109">Per le prestazioni, l'utilizzo di `Span<char>` e `ReadOnlySpan<char>` è preferibile rispetto alla stringa in molti scenari.</span><span class="sxs-lookup"><span data-stu-id="ca4f5-109">For perfomance, usage of `Span<char>` and `ReadOnlySpan<char>` is preferred over string in many scenarios.</span></span> <span data-ttu-id="ca4f5-110">Il Framework ha aggiunto molte nuove API che consentono di usare `ReadOnlySpan<char>` al posto di un `string` .</span><span class="sxs-lookup"><span data-stu-id="ca4f5-110">The framework has added many new APIs to allow you to use `ReadOnlySpan<char>` in place of a `string`.</span></span>

<span data-ttu-id="ca4f5-111">Un'operazione comune sulle stringhe consiste nell'usare un'opzione per verificare se si tratta di un valore specifico e il compilatore ottimizza tale opzione.</span><span class="sxs-lookup"><span data-stu-id="ca4f5-111">A common operation on strings is to use a switch to test if it is a particular value, and the compiler optimizes such a switch.</span></span> <span data-ttu-id="ca4f5-112">Tuttavia, non è attualmente possibile eseguire la stessa operazione in modo `ReadOnlySpan<char>` efficiente, oltre a implementare manualmente l'opzione e l'ottimizzazione.</span><span class="sxs-lookup"><span data-stu-id="ca4f5-112">However there is currently no way to do the same on a `ReadOnlySpan<char>` efficiently, other than implementing the switch and the optimization manually.</span></span>

<span data-ttu-id="ca4f5-113">Per incoraggiare l'adozione di `ReadOnlySpan<char>` , è possibile utilizzare criteri di ricerca, in una costante, in modo da consentirne l' `ReadOnlySpan<char>` `string` utilizzo anche in un'opzione.</span><span class="sxs-lookup"><span data-stu-id="ca4f5-113">In order to encourage adoption of `ReadOnlySpan<char>` we allow pattern matching a `ReadOnlySpan<char>`, on a constant `string`, thus also allowing it to be used in a switch.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="ca4f5-114">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="ca4f5-114">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="ca4f5-115">La [specifica](../csharp-7.0/pattern-matching.md#constant-pattern) per i modelli costanti viene modificata come segue (l'aggiunta proposta viene visualizzata in grassetto):</span><span class="sxs-lookup"><span data-stu-id="ca4f5-115">We alter the [spec](../csharp-7.0/pattern-matching.md#constant-pattern) for constant patterns as follows (the proposed addition is shown in bold):</span></span>

> <span data-ttu-id="ca4f5-116">Un criterio costante verifica il valore di un'espressione rispetto a un valore costante.</span><span class="sxs-lookup"><span data-stu-id="ca4f5-116">A constant pattern tests the value of an expression against a constant value.</span></span> <span data-ttu-id="ca4f5-117">La costante può essere qualsiasi espressione costante, ad esempio un valore letterale, il nome di una variabile dichiarata `const` , una costante di enumerazione o un' `typeof` espressione e così via.</span><span class="sxs-lookup"><span data-stu-id="ca4f5-117">The constant may be any constant expression, such as a literal, the name of a declared `const` variable, or an enumeration constant, or a `typeof` expression etc.</span></span>
>
> <span data-ttu-id="ca4f5-118">Se e *e* *c* sono di tipo integrale, il modello viene considerato corrispondente se il risultato dell'espressione `e == c` è `true` .</span><span class="sxs-lookup"><span data-stu-id="ca4f5-118">If both *e* and *c* are of integral types, the pattern is considered matched if the result of the expression `e == c` is `true`.</span></span>
>
> <span data-ttu-id="ca4f5-119">**Se *e* è di tipo `System.Span<char>` o `System.ReadOnlySpan<char>` e *c* è una stringa costante e *c* non ha un valore costante di `null` , il modello viene considerato corrispondente se `System.MemoryExtensions.SequenceEqual<char>(e, System.MemoryExtensions.AsSpan(c))` restituisce `true` .**</span><span class="sxs-lookup"><span data-stu-id="ca4f5-119">**If *e* is of type `System.Span<char>` or `System.ReadOnlySpan<char>`, and *c* is a constant string, and *c* does not have a constant value of `null`, then the pattern is considered matching if `System.MemoryExtensions.SequenceEqual<char>(e, System.MemoryExtensions.AsSpan(c))` returns `true`.**</span></span>
> 
> <span data-ttu-id="ca4f5-120">In caso contrario, il modello viene considerato corrispondente se `object.Equals(e, c)` restituisce `true` .</span><span class="sxs-lookup"><span data-stu-id="ca4f5-120">Otherwise the pattern is considered matching if `object.Equals(e, c)` returns `true`.</span></span> <span data-ttu-id="ca4f5-121">In questo caso si tratta di un errore in fase di compilazione se il tipo statico di *e* non è *compatibile* con il tipo della costante.</span><span class="sxs-lookup"><span data-stu-id="ca4f5-121">In this case it is a compile-time error if the static type of *e* is not *pattern compatible* with the type of the constant.</span></span>

<span data-ttu-id="ca4f5-122">`System.Span<T>` e `System.ReadOnlySpan<T>` vengono confrontati in base al nome, devono essere `ref struct` s e possono essere definiti all'esterno di corlib.</span><span class="sxs-lookup"><span data-stu-id="ca4f5-122">`System.Span<T>` and `System.ReadOnlySpan<T>` are matched by name, must be `ref struct`s, and can be defined outside corlib.</span></span> <span data-ttu-id="ca4f5-123">`System.MemoryExtensions` corrisponde al nome e può essere definito all'esterno di corlib.</span><span class="sxs-lookup"><span data-stu-id="ca4f5-123">`System.MemoryExtensions` is matched by name and can be defined outside corlib.</span></span> <span data-ttu-id="ca4f5-124">La firma di `System.MemoryExtensions.SequenceEqual` deve corrispondere `public static bool SequenceEqual<T>(System.Span<T>, System.ReadOnlySpan<T>)` rispettivamente a e `public static bool SequenceEqual<T>(System.ReadOnlySpan<T>, System.ReadOnlySpan<T>)` la firma di `System.MemoryExtensions.AsSpan` deve corrispondere a `public static System.ReadOnlySpan<char> AsSpan(string)` .</span><span class="sxs-lookup"><span data-stu-id="ca4f5-124">The signature of `System.MemoryExtensions.SequenceEqual` must match `public static bool SequenceEqual<T>(System.Span<T>, System.ReadOnlySpan<T>)` and `public static bool SequenceEqual<T>(System.ReadOnlySpan<T>, System.ReadOnlySpan<T>)` respectively, and the signature of `System.MemoryExtensions.AsSpan` must match `public static System.ReadOnlySpan<char> AsSpan(string)`.</span></span> <span data-ttu-id="ca4f5-125">I metodi con parametri facoltativi sono esclusi dalla considerazione.</span><span class="sxs-lookup"><span data-stu-id="ca4f5-125">Methods with optional parameters are excluded from consideration.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="ca4f5-126">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="ca4f5-126">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="ca4f5-127">nessuno</span><span class="sxs-lookup"><span data-stu-id="ca4f5-127">None</span></span>

## <a name="alternatives"></a><span data-ttu-id="ca4f5-128">Alternativi</span><span class="sxs-lookup"><span data-stu-id="ca4f5-128">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="ca4f5-129">nessuno</span><span class="sxs-lookup"><span data-stu-id="ca4f5-129">None</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="ca4f5-130">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="ca4f5-130">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="ca4f5-131">nessuno</span><span class="sxs-lookup"><span data-stu-id="ca4f5-131">None</span></span>

## <a name="design-meetings"></a><span data-ttu-id="ca4f5-132">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="ca4f5-132">Design meetings</span></span>

https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-10-07.md#readonlyspanchar-patterns
