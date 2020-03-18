---
ms.openlocfilehash: 032cb8590a0b6e83f8ab6201e10720f1b254c605
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484531"
---
# <a name="nullable-enhanced-common-type"></a><span data-ttu-id="95d79-101">Tipo comune migliorato Nullable</span><span class="sxs-lookup"><span data-stu-id="95d79-101">Nullable-Enhanced Common Type</span></span>

* <span data-ttu-id="95d79-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="95d79-102">[x] Proposed</span></span>
* <span data-ttu-id="95d79-103">[] Prototipo: nessuno</span><span class="sxs-lookup"><span data-stu-id="95d79-103">[ ] Prototype: None</span></span>
* <span data-ttu-id="95d79-104">[] Implementazione: nessuna</span><span class="sxs-lookup"><span data-stu-id="95d79-104">[ ] Implementation: None</span></span>
* <span data-ttu-id="95d79-105">[] Specifica: vedere di seguito</span><span class="sxs-lookup"><span data-stu-id="95d79-105">[ ] Specification: See below</span></span>

## <a name="summary"></a><span data-ttu-id="95d79-106">Summary</span><span class="sxs-lookup"><span data-stu-id="95d79-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="95d79-107">Si verifica una situazione in cui i risultati dell'algoritmo di tipo comune corrente sono controintuitivi e il programmatore aggiunge un cast ridondante al codice.</span><span class="sxs-lookup"><span data-stu-id="95d79-107">There is a situation in which the current common-type algorithm results are counter-intuitive, and results in the programmer adding what feels like a redundant cast to the code.</span></span> <span data-ttu-id="95d79-108">Con questa modifica, un'espressione come `condition ? 1 : null` comporterebbe un valore di tipo `int?`e un'espressione, ad esempio `condition ? x : 1.0` dove `x` è di tipo `int?` comporterebbe un valore di tipo `double?`.</span><span class="sxs-lookup"><span data-stu-id="95d79-108">With this change, an expression such as `condition ? 1 : null` would result in a value of type `int?`, and an expression such as `condition ? x : 1.0` where `x` is of type `int?` would result in a value of type `double?`.</span></span>

## <a name="motivation"></a><span data-ttu-id="95d79-109">Motivazione</span><span class="sxs-lookup"><span data-stu-id="95d79-109">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="95d79-110">Si tratta di una ragione comune di ciò che si ritiene al programmatore come un codice standard inutile.</span><span class="sxs-lookup"><span data-stu-id="95d79-110">This is a common cause of what feels to the programmer like needless boilerplate code.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="95d79-111">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="95d79-111">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="95d79-112">Viene modificata la specifica per [trovare il tipo comune migliore di un set di espressioni](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) per influenzare le situazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="95d79-112">We modify the specification for [finding the best common type of a set of expressions](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) to affect the following situations:</span></span>

- <span data-ttu-id="95d79-113">Se un'espressione è di un tipo di valore non nullable `T` e l'altro è un valore letterale null, il risultato è di tipo `T?`.</span><span class="sxs-lookup"><span data-stu-id="95d79-113">If one expression is of a non-nullable value type `T` and the other is a null literal, the result is of type `T?`.</span></span>
- <span data-ttu-id="95d79-114">Se un'espressione è di un tipo di valore Nullable `T?` e l'altra è di un tipo di valore `U`ed esiste una conversione implicita da `T` a `U`, il risultato è di tipo `U?`.</span><span class="sxs-lookup"><span data-stu-id="95d79-114">If one expression is of a nullable value type `T?` and the other is of a value type `U`, and there is an implicit conversion from `T` to `U`, then the result is of type `U?`.</span></span>

<span data-ttu-id="95d79-115">Questa operazione dovrebbe influire sugli aspetti seguenti del linguaggio:</span><span class="sxs-lookup"><span data-stu-id="95d79-115">This is expected to affect the following aspects of the language:</span></span>

- <span data-ttu-id="95d79-116">[espressione ternaria](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#conditional-operator)</span><span class="sxs-lookup"><span data-stu-id="95d79-116">the [ternary expression](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#conditional-operator)</span></span>
- <span data-ttu-id="95d79-117">[espressione di creazione della matrice](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#array-creation-expressions) tipizzata in modo implicito</span><span class="sxs-lookup"><span data-stu-id="95d79-117">implicitly typed [array creation expression](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#array-creation-expressions)</span></span>
- <span data-ttu-id="95d79-118">deduzione del [tipo restituito di un'espressione lambda](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#inferred-return-type) per l'inferenza del tipo</span><span class="sxs-lookup"><span data-stu-id="95d79-118">inferring the [return type of a lambda](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#inferred-return-type) for type inference</span></span>
- <span data-ttu-id="95d79-119">casi che coinvolgono generics, ad esempio la chiamata di `M<T>(T a, T b)` come `M(1, null)`.</span><span class="sxs-lookup"><span data-stu-id="95d79-119">cases involving generics, such as invoking `M<T>(T a, T b)` as `M(1, null)`.</span></span>

<span data-ttu-id="95d79-120">Più precisamente, si modificano le sezioni seguenti della specifica (inserimenti in grassetto, eliminazioni in barrate):</span><span class="sxs-lookup"><span data-stu-id="95d79-120">More precisely, we change the following sections of the specification (insertions in bold, deletions in strikethrough):</span></span>

> #### <a name="output-type-inferences"></a><span data-ttu-id="95d79-121">Inferenza del tipo di output</span><span class="sxs-lookup"><span data-stu-id="95d79-121">Output type inferences</span></span>
> 
> <span data-ttu-id="95d79-122">Un' *inferenza del tipo di output* viene eseguita *da* un'espressione `E` *a* un tipo `T` nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="95d79-122">An *output type inference* is made *from* an expression `E` *to* a type `T` in the following way:</span></span>
> 
> *  <span data-ttu-id="95d79-123">Se `E` è una funzione anonima con tipo restituito dedotto `U` ([tipo restituito derivato](expressions.md#inferred-return-type)) e `T` è un tipo delegato o un tipo di albero delle espressioni con tipo restituito `Tb`, viene eseguita un' *inferenza ad associazione inferiore* ([inferenza con associazione](expressions.md#lower-bound-inferences)inferiore) *da* `U` *a* `Tb`.</span><span class="sxs-lookup"><span data-stu-id="95d79-123">If `E` is an anonymous function with inferred return type  `U` ([Inferred return type](expressions.md#inferred-return-type)) and `T` is a delegate type or expression tree type with return type `Tb`, then a *lower-bound inference* ([Lower-bound inferences](expressions.md#lower-bound-inferences)) is made *from* `U` *to* `Tb`.</span></span>
> *  <span data-ttu-id="95d79-124">In caso contrario, se `E` è un gruppo di metodi e `T` è un tipo delegato o un tipo di albero delle espressioni con tipi di parametro `T1...Tk` e tipo restituito `Tb`e la risoluzione dell'overload di `E` con i tipi `T1...Tk` genera un solo metodo con tipo restituito `U`, viene eseguita un' *inferenza con associazione inferiore* *da* `U` *a* `Tb`.</span><span class="sxs-lookup"><span data-stu-id="95d79-124">Otherwise, if `E` is a method group and `T` is a delegate type or expression tree type with parameter types `T1...Tk` and return type `Tb`, and overload resolution of `E` with the types `T1...Tk` yields a single method with return type `U`, then a *lower-bound inference* is made *from* `U` *to* `Tb`.</span></span>
> *  <span data-ttu-id="95d79-125">\* \* In caso contrario, se `E` è un'espressione con tipo di valore Nullable `U?`, viene eseguita un' *inferenza con associazione inferiore* *da* `U` *a* `T` e viene aggiunto un *limite null* a `T`.</span><span class="sxs-lookup"><span data-stu-id="95d79-125">\*\*Otherwise, if `E` is an expression with nullable value type `U?`, then a *lower-bound inference* is made *from* `U` *to* `T` and a *null bound* is added to `T`.</span></span> **
> *  <span data-ttu-id="95d79-126">In caso contrario, se `E` è un'espressione con tipo `U`, viene eseguita un' *inferenza con associazione inferiore* *da* `U` *a* `T`.</span><span class="sxs-lookup"><span data-stu-id="95d79-126">Otherwise, if `E` is an expression with type `U`, then a *lower-bound inference* is made *from* `U` *to* `T`.</span></span>
> *  <span data-ttu-id="95d79-127">**In caso contrario, se `E` è un'espressione costante con valore `null`, viene aggiunto un *limite null* a `T`**</span><span class="sxs-lookup"><span data-stu-id="95d79-127">**Otherwise, if `E` is a constant expression with value `null`, then a *null bound* is added to `T`**</span></span> 
> *  <span data-ttu-id="95d79-128">In caso contrario, non vengono eseguite inferenze.</span><span class="sxs-lookup"><span data-stu-id="95d79-128">Otherwise, no inferences are made.</span></span>

> #### <a name="fixing"></a><span data-ttu-id="95d79-129">Fissaggio</span><span class="sxs-lookup"><span data-stu-id="95d79-129">Fixing</span></span>
> 
> <span data-ttu-id="95d79-130">Una variabile di tipo *unfixed* `Xi` con un set di limiti viene *fissata* come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="95d79-130">An *unfixed* type variable `Xi` with a set of bounds is *fixed* as follows:</span></span>
> 
> *  <span data-ttu-id="95d79-131">Il set di *tipi candidati* `Uj` inizia come set di tutti i tipi nel set di limiti per `Xi`.</span><span class="sxs-lookup"><span data-stu-id="95d79-131">The set of *candidate types* `Uj` starts out as the set of all types in the set of bounds for `Xi`.</span></span>
> *  <span data-ttu-id="95d79-132">Si esamina quindi ogni limite per `Xi` a sua volta: per ogni `U` con binding esatto di `Xi` tutti i tipi `Uj` che non sono identici a `U` vengono rimossi dal set candidato.</span><span class="sxs-lookup"><span data-stu-id="95d79-132">We then examine each bound for `Xi` in turn: For each exact bound `U` of `Xi` all types `Uj` which are not identical to `U` are removed from the candidate set.</span></span> <span data-ttu-id="95d79-133">Per ogni `U` con binding inferiore di `Xi` tutti i tipi `Uj` a cui *non* è presente una conversione implicita da `U` vengono rimossi dal set di candidati.</span><span class="sxs-lookup"><span data-stu-id="95d79-133">For each lower bound `U` of `Xi` all types `Uj` to which there is *not* an implicit conversion from `U` are removed from the candidate set.</span></span> <span data-ttu-id="95d79-134">Per ogni `U` limite superiore di `Xi` tutti i tipi `Uj` da cui *non* esiste una conversione implicita a `U` vengono rimossi dal set di candidati.</span><span class="sxs-lookup"><span data-stu-id="95d79-134">For each upper bound `U` of `Xi` all types `Uj` from which there is *not* an implicit conversion to `U` are removed from the candidate set.</span></span>
> *  <span data-ttu-id="95d79-135">Se tra i tipi candidati rimanenti `Uj` è presente un tipo univoco `V` da cui esiste una conversione implicita a tutti gli altri tipi candidati, ~~`Xi` viene fissata `V`.~~</span><span class="sxs-lookup"><span data-stu-id="95d79-135">If among the remaining candidate types `Uj` there is a unique type `V` from which there is an implicit conversion to all the other candidate types, then ~~`Xi` is fixed to `V`.~~</span></span>
>     -  <span data-ttu-id="95d79-136">**Se `V` è un tipo di valore ed è associato un valore *null* per `Xi`, `Xi` viene fissata `V?`**</span><span class="sxs-lookup"><span data-stu-id="95d79-136">**If `V` is a value type and there is a *null bound* for `Xi`, then `Xi` is fixed to `V?`**</span></span>
>     -  <span data-ttu-id="95d79-137">**In caso contrario `Xi` viene corretto `V`**</span><span class="sxs-lookup"><span data-stu-id="95d79-137">**Otherwise   `Xi` is fixed to `V`**</span></span>
> *  <span data-ttu-id="95d79-138">In caso contrario, l'inferenza del tipo non riesce.</span><span class="sxs-lookup"><span data-stu-id="95d79-138">Otherwise, type inference fails.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="95d79-139">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="95d79-139">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="95d79-140">Potrebbero esserci alcune incompatibilità introdotte da questa proposta.</span><span class="sxs-lookup"><span data-stu-id="95d79-140">There may be some incompatibilities introduced by this proposal.</span></span>

## <a name="alternatives"></a><span data-ttu-id="95d79-141">Alternativi</span><span class="sxs-lookup"><span data-stu-id="95d79-141">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="95d79-142">No.</span><span class="sxs-lookup"><span data-stu-id="95d79-142">None.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="95d79-143">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="95d79-143">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="95d79-144">[] Qual è la gravità dell'incompatibilità introdotta da questa proposta, se presente, e come è possibile moderarla?</span><span class="sxs-lookup"><span data-stu-id="95d79-144">[ ] What is the severity of incompatibility introduced by this proposal, if any, and how can it be moderated?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="95d79-145">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="95d79-145">Design meetings</span></span>

<span data-ttu-id="95d79-146">No.</span><span class="sxs-lookup"><span data-stu-id="95d79-146">None.</span></span>
