---
ms.openlocfilehash: 80ccdb75e2f5a022e367f3c023ea4464195deaaf
ms.sourcegitcommit: 95f5f86ba2e2a23cd4fb37bd9d1ff690c83d1191
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81647134"
---
# <a name="target-typed-conditional-expression"></a><span data-ttu-id="ae203-101">Espressione condizionale tipizzato di destinazioneTarget-Typed Expression</span><span class="sxs-lookup"><span data-stu-id="ae203-101">Target-Typed Conditional Expression</span></span>

<span data-ttu-id="ae203-102">Per un'espressione `c ? e1 : e2`condizionale, quando</span><span class="sxs-lookup"><span data-stu-id="ae203-102">For a conditional expression `c ? e1 : e2`, when</span></span>

1. <span data-ttu-id="ae203-103">non esiste un `e1` tipo `e2`comune per e , o</span><span class="sxs-lookup"><span data-stu-id="ae203-103">there is no common type for `e1` and `e2`, or</span></span>
2. <span data-ttu-id="ae203-104">per cui esiste un tipo comune, ma una delle espressioni `e1` o `e2` non ha alcuna conversione implicita in quel tipo</span><span class="sxs-lookup"><span data-stu-id="ae203-104">for which a common type exists but one of the expressions `e1` or `e2` has no implicit conversion to that type</span></span>

<span data-ttu-id="ae203-105">viene definita una nuova conversione di *espressioni condizionali* che `T` consente una conversione implicita `e1` dall'espressione condizionale a qualsiasi tipo per il quale esiste un'espressione di conversione da a `T` e anche da `e2` a `T`.</span><span class="sxs-lookup"><span data-stu-id="ae203-105">we define a new *conditional expression conversion* that permits an implicit conversion from the conditional expression to any type `T` for which there is a conversion-from-expression from `e1` to `T` and also from `e2` to `T`.</span></span>  <span data-ttu-id="ae203-106">Si tratta di un errore se un'espressione condizionale non ha un tipo comune tra `e1` e `e2` né è soggetta a una *conversione*di espressione condizionale .</span><span class="sxs-lookup"><span data-stu-id="ae203-106">It is an error if a conditional expression neither has a common type between `e1` and `e2` nor is subject to a *conditional expression conversion*.</span></span>

### <a name="open-issues"></a><span data-ttu-id="ae203-107">Open Issues</span><span class="sxs-lookup"><span data-stu-id="ae203-107">Open Issues</span></span>

<span data-ttu-id="ae203-108">Desideriamo estendere questa digitazione di destinazione ai casi in `e1` `e2` cui l'espressione condizionale ha un tipo comune per e ma non vi è alcuna conversione da tale tipo comune al tipo di destinazione.</span><span class="sxs-lookup"><span data-stu-id="ae203-108">We would like to extend this target typing to cases in which the conditional expression has a common type for `e1` and `e2` but there is no conversion from that common type to the target type.</span></span> <span data-ttu-id="ae203-109">Ciò porterebbe la digitazione di destinazione dell'espressione condizionale in allineamento della digitazione di destinazione dell'espressione switch.</span><span class="sxs-lookup"><span data-stu-id="ae203-109">That would bring target typing of the conditional expression into alignment of target typing of the switch expression.</span></span> <span data-ttu-id="ae203-110">Tuttavia siamo preoccupati che sarebbe un cambiamento radicale:</span><span class="sxs-lookup"><span data-stu-id="ae203-110">However we are concerned that would be a breaking change:</span></span>

```csharp
M(b ? 1 : 2); // calls M(long) without this feature; calls M(short) with this feature

void M(short);
void M(long);
```

<span data-ttu-id="ae203-111">È possibile ridurre l'ambito della modifica sostanziale modificando le regole per una *migliore conversione dall'espressione*: la conversione da un'espressione condizionale a T1 è una conversione migliore *dall'espressione*rispetto alla conversione in T2 se la conversione in T1 non è una *conversione* di espressione condizionale e la conversione in T2 è una conversione di espressione condizionale .</span><span class="sxs-lookup"><span data-stu-id="ae203-111">We could reduce the scope of the breaking change by modifying the rules for *better conversion from expression*: the conversion from a conditional expression to T1 is a better conversion from expression than the conversion to T2 if the conversion to T1 is not a *conditional expression conversion* and the conversion to T2 is a *conditional expression conversion*.</span></span>  <span data-ttu-id="ae203-112">Che risolve il cambiamento sostanziale nel `M(long)` programma di cui sopra (chiama con o senza questa funzione).</span><span class="sxs-lookup"><span data-stu-id="ae203-112">That resolves the breaking change in the above program (it calls `M(long)` with or without this feature).</span></span> <span data-ttu-id="ae203-113">Questo approccio ha due piccoli aspetti negativi.</span><span class="sxs-lookup"><span data-stu-id="ae203-113">This approach does have two small downsides.</span></span>  <span data-ttu-id="ae203-114">In primo luogo, non è proprio uguale all'espressione switch:</span><span class="sxs-lookup"><span data-stu-id="ae203-114">First, it is not quite the same as the switch expression:</span></span>

```csharp
M(b ? 1 : 2); // calls M(long)
M(b switch { true => 1, false => 2 }); // calls M(short)
```

<span data-ttu-id="ae203-115">Questo è ancora un cambiamento sostanziale, ma il suo campo di applicazione è meno probabile che influenzano i programmi reali:</span><span class="sxs-lookup"><span data-stu-id="ae203-115">This is still a breaking change, but its scope is less likely to affect real programs:</span></span>

```csharp
M(b ? 1 : 2, 1); // calls M(long, long) without this feature; ambiguous with this feature.

M(short, short);
M(long, long);
```

<span data-ttu-id="ae203-116">Questo diventa ambiguo `long` perché la conversione in è migliore per il primo argomento `short` (perché non utilizza la `short` *conversione dell'espressione condizionale*), ma la conversione in è migliore per il secondo argomento (perché è una destinazione di *conversione migliore* di `long`).</span><span class="sxs-lookup"><span data-stu-id="ae203-116">This becomes ambiguous because the conversion to `long` is better for the first argument (because it does not use the *conditional expression conversion*), but the conversion to `short` is better for the second argument (because `short` is a *better conversion target* than `long`).</span></span> <span data-ttu-id="ae203-117">Questa modifica di interruzione sembra meno grave perché non modifica silenziosamente il comportamento di un programma esistente.</span><span class="sxs-lookup"><span data-stu-id="ae203-117">This breaking change seems less serious because it does not silently change the behavior of an existing program.</span></span>

<span data-ttu-id="ae203-118">Se scegliamo di apportare questa modifica alla proposta, cambieremmo</span><span class="sxs-lookup"><span data-stu-id="ae203-118">Should we elect to make this change to the proposal, we would change</span></span>

> #### <a name="better-conversion-from-expression"></a><span data-ttu-id="ae203-119">Migliore conversione dall'espressione</span><span class="sxs-lookup"><span data-stu-id="ae203-119">Better conversion from expression</span></span>
> 
> <span data-ttu-id="ae203-120">Data una `C1` conversione implicita `E` che esegue `T1`la conversione `C2` da un'espressione `E` a `T2`un `C1` tipo e una conversione implicita che esegue la conversione da un'espressione a un tipo , è una ***conversione migliore*** rispetto a `C2` if `E` non corrisponde `T2` esattamente e almeno uno degli elementi seguenti è valido:</span><span class="sxs-lookup"><span data-stu-id="ae203-120">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>
> 
> * <span data-ttu-id="ae203-121">`E`corrisponde `T1` esattamente a ([Esattamente corrispondenti a Expression](expressions.md#exactly-matching-expression))</span><span class="sxs-lookup"><span data-stu-id="ae203-121">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
> * <span data-ttu-id="ae203-122">`T1`è un obiettivo `T2` di conversione migliore di ([Migliore obiettivo](expressions.md#better-conversion-target)di conversione )</span><span class="sxs-lookup"><span data-stu-id="ae203-122">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target))</span></span>

<span data-ttu-id="ae203-123">to</span><span class="sxs-lookup"><span data-stu-id="ae203-123">to</span></span>

> #### <a name="better-conversion-from-expression"></a><span data-ttu-id="ae203-124">Migliore conversione dall'espressione</span><span class="sxs-lookup"><span data-stu-id="ae203-124">Better conversion from expression</span></span>
> 
> <span data-ttu-id="ae203-125">Data una `C1` conversione implicita `E` che esegue `T1`la conversione `C2` da un'espressione `E` a `T2`un `C1` tipo e una conversione implicita che esegue la conversione da un'espressione a un tipo , è una ***conversione migliore*** rispetto a `C2` if `E` non corrisponde `T2` esattamente e almeno uno degli elementi seguenti è valido:</span><span class="sxs-lookup"><span data-stu-id="ae203-125">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>
> 
> * <span data-ttu-id="ae203-126">`E`corrisponde `T1` esattamente a ([Esattamente corrispondenti a Expression](expressions.md#exactly-matching-expression))</span><span class="sxs-lookup"><span data-stu-id="ae203-126">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
> * <span data-ttu-id="ae203-127">`T1`è una destinazione `T2` di conversione migliore rispetto `C1` a ( Migliore destinazione di[conversione](expressions.md#better-conversion-target)) e non è una *conversione* di espressione condizionale o `C2` è una conversione di espressione condizionale.</span><span class="sxs-lookup"><span data-stu-id="ae203-127">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target)) \*\*and either `C1` is not a *conditional expression conversion* or `C2` is a \*conditional expression conversion\*\*\*.</span></span>
