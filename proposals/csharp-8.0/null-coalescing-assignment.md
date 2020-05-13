---
ms.openlocfilehash: 962236dd30365b2e9c33bb1f6ec5328fdf22e90a
ms.sourcegitcommit: c2fe8f1d150ac6ac171072d1c6f9df883adcbb40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83203250"
---
# <a name="null-coalescing-assignment"></a><span data-ttu-id="3306e-101">Assegnazione null coalescing</span><span class="sxs-lookup"><span data-stu-id="3306e-101">null coalescing assignment</span></span>

* <span data-ttu-id="3306e-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="3306e-102">[x] Proposed</span></span>
* <span data-ttu-id="3306e-103">[x] prototipo: completato</span><span class="sxs-lookup"><span data-stu-id="3306e-103">[x] Prototype: Completed</span></span>
* <span data-ttu-id="3306e-104">Implementazione di [x]: completata</span><span class="sxs-lookup"><span data-stu-id="3306e-104">[x] Implementation: Completed</span></span>
* <span data-ttu-id="3306e-105">[x] Specifica: di seguito</span><span class="sxs-lookup"><span data-stu-id="3306e-105">[x] Specification: Below</span></span>

## <a name="summary"></a><span data-ttu-id="3306e-106">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="3306e-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="3306e-107">Semplifica un modello di codifica comune a cui a una variabile viene assegnato un valore se è null.</span><span class="sxs-lookup"><span data-stu-id="3306e-107">Simplifies a common coding pattern where a variable is assigned a value if it is null.</span></span>

<span data-ttu-id="3306e-108">Come parte di questa proposta, verranno rilasciati anche i requisiti di tipo in `??` per consentire a un'espressione il cui tipo è un parametro di tipo non vincolato da usare sul lato sinistro.</span><span class="sxs-lookup"><span data-stu-id="3306e-108">As part of this proposal, we will also loosen the type requirements on `??` to allow an expression whose type is an unconstrained type parameter to be used on the left-hand side.</span></span>

## <a name="motivation"></a><span data-ttu-id="3306e-109">Motivazione</span><span class="sxs-lookup"><span data-stu-id="3306e-109">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="3306e-110">È comune vedere il codice del modulo</span><span class="sxs-lookup"><span data-stu-id="3306e-110">It is common to see code of the form</span></span>

```csharp
if (variable == null)
{
    variable = expression;
}
```

<span data-ttu-id="3306e-111">Questa proposta aggiunge un operatore binario non che esegue l'overload al linguaggio che esegue questa funzione.</span><span class="sxs-lookup"><span data-stu-id="3306e-111">This proposal adds a non-overloadable binary operator to the language that performs this function.</span></span>

<span data-ttu-id="3306e-112">Sono state apportate almeno otto richieste della community separate per questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="3306e-112">There have been at least eight separate community requests for this feature.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="3306e-113">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="3306e-113">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="3306e-114">Viene aggiunta una nuova forma di operatore di assegnazione</span><span class="sxs-lookup"><span data-stu-id="3306e-114">We add a new form of assignment operator</span></span>

``` antlr
assignment_operator
    : '??='
    ;
```

<span data-ttu-id="3306e-115">Che segue le [regole semantiche esistenti per gli operatori di assegnazione composti](../../spec/expressions.md#compound-assignment), ad eccezione del fatto che Elide l'assegnazione se il lato sinistro non è null.</span><span class="sxs-lookup"><span data-stu-id="3306e-115">Which follows the [existing semantic rules for compound assignment operators](../../spec/expressions.md#compound-assignment), except that we elide the assignment if the left-hand side is non-null.</span></span> <span data-ttu-id="3306e-116">Di seguito sono riportate le regole per questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="3306e-116">The rules for this feature are as follows.</span></span>

<span data-ttu-id="3306e-117">Dato `a ??= b` , dove `A` è il tipo di `a` , `B` è il tipo di `b` e `A0` è il tipo sottostante di `A` se `A` è un tipo di valore Nullable:</span><span class="sxs-lookup"><span data-stu-id="3306e-117">Given `a ??= b`, where `A` is the type of `a`, `B` is the type of `b`, and `A0` is the underlying type of `A` if `A` is a nullable value type:</span></span>

1. <span data-ttu-id="3306e-118">Se non `A` esiste o è un tipo di valore non nullable, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="3306e-118">If `A` does not exist or is a non-nullable value type, a compile-time error occurs.</span></span>
2. <span data-ttu-id="3306e-119">Se `B` non è implicitamente convertibile in `A` o `A0` (se `A0` esiste), si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="3306e-119">If `B` is not implicitly convertible to `A` or `A0` (if `A0` exists), a compile-time error occurs.</span></span>
3. <span data-ttu-id="3306e-120">Se `A0` esiste ed `B` è implicitamente convertibile in `A0` e `B` non è dinamico, il tipo di `a ??= b` è `A0` .</span><span class="sxs-lookup"><span data-stu-id="3306e-120">If `A0` exists and `B` is implicitly convertible to `A0`, and `B` is not dynamic, then the type of `a ??= b` is `A0`.</span></span> <span data-ttu-id="3306e-121">`a ??= b`viene valutato in fase di esecuzione come:</span><span class="sxs-lookup"><span data-stu-id="3306e-121">`a ??= b` is evaluated at runtime as:</span></span>
   ```C#
   var tmp = a.GetValueOrDefault();
   if (!a.HasValue) { tmp = b; a = tmp; }
   tmp
   ```
   <span data-ttu-id="3306e-122">Con la differenza che `a` viene valutato una sola volta.</span><span class="sxs-lookup"><span data-stu-id="3306e-122">Except that `a` is only evaluated once.</span></span>
4. <span data-ttu-id="3306e-123">In caso contrario, il tipo di `a ??= b` è `A` .</span><span class="sxs-lookup"><span data-stu-id="3306e-123">Otherwise, the type of `a ??= b` is `A`.</span></span> <span data-ttu-id="3306e-124">`a ??= b`viene valutato in fase di esecuzione come `a ?? (a = b)` , ad eccezione del fatto che `a` viene valutato una sola volta.</span><span class="sxs-lookup"><span data-stu-id="3306e-124">`a ??= b` is evaluated at runtime as `a ?? (a = b)`, except that `a` is only evaluated once.</span></span>


<span data-ttu-id="3306e-125">Per il rilassamento dei requisiti di tipo di `??` , viene aggiornata la specifica in cui viene attualmente indicato, dato `a ?? b` , dove `A` è il tipo di `a` :</span><span class="sxs-lookup"><span data-stu-id="3306e-125">For the relaxation of the type requirements of `??`, we update the spec where it currently states that, given `a ?? b`, where `A` is the type of `a`:</span></span>

> 1. <span data-ttu-id="3306e-126">Se esiste e non è un tipo nullable o un tipo riferimento, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="3306e-126">If A exists and is not a nullable type or a reference type, a compile-time error occurs.</span></span>

<span data-ttu-id="3306e-127">Questo requisito è stato rilassato per:</span><span class="sxs-lookup"><span data-stu-id="3306e-127">We relax this requirement to:</span></span>

1. <span data-ttu-id="3306e-128">Se esiste e è un tipo di valore non nullable, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="3306e-128">If A exists and is a non-nullable value type, a compile-time error occurs.</span></span>

<span data-ttu-id="3306e-129">Ciò consente all'operatore di Unione null di funzionare sui parametri di tipo non vincolati, poiché il parametro di tipo non vincolato esiste, non è un tipo nullable e non è un tipo di riferimento.</span><span class="sxs-lookup"><span data-stu-id="3306e-129">This allows the null coalescing operator to work on unconstrained type parameters, as the unconstrained type parameter T exists, is not a nullable type, and is not a reference type.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="3306e-130">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="3306e-130">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="3306e-131">Come per qualsiasi funzionalità del linguaggio, è necessario stabilire se la complessità aggiuntiva al linguaggio viene ripagata in base a una maggiore chiarezza offerta al corpo dei programmi C# che trarrebbe vantaggio dalla funzionalità.</span><span class="sxs-lookup"><span data-stu-id="3306e-131">As with any language feature, we must question whether the additional complexity to the language is repaid in the additional clarity offered to the body of C# programs that would benefit from the feature.</span></span>

## <a name="alternatives"></a><span data-ttu-id="3306e-132">Alternativi</span><span class="sxs-lookup"><span data-stu-id="3306e-132">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="3306e-133">Il programmatore può scrivere `(x = x ?? y)` , `if (x == null) x = y;` o `x ?? (x = y)` manualmente.</span><span class="sxs-lookup"><span data-stu-id="3306e-133">The programmer can write `(x = x ?? y)`, `if (x == null) x = y;`, or `x ?? (x = y)` by hand.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="3306e-134">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="3306e-134">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="3306e-135">[] Richiede la revisione di LDM</span><span class="sxs-lookup"><span data-stu-id="3306e-135">[ ] Requires LDM review</span></span>
- <span data-ttu-id="3306e-136">[] Sono supportati anche `&&=` `||=` gli operatori e?</span><span class="sxs-lookup"><span data-stu-id="3306e-136">[ ] Should we also support `&&=` and `||=` operators?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="3306e-137">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="3306e-137">Design meetings</span></span>

<span data-ttu-id="3306e-138">Nessuno.</span><span class="sxs-lookup"><span data-stu-id="3306e-138">None.</span></span>
