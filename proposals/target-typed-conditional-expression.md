---
ms.openlocfilehash: cd73a3d7289205f65f5144a98d32da06dfed3efc
ms.sourcegitcommit: e355841daad8c4672fae6a49c98653952d89a9cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83775420"
---
# <a name="target-typed-conditional-expression"></a><span data-ttu-id="19634-101">Espressione condizionale tipizzata di destinazione</span><span class="sxs-lookup"><span data-stu-id="19634-101">Target-Typed Conditional Expression</span></span>

<span data-ttu-id="19634-102">Per un'espressione condizionale `c ? e1 : e2` , quando</span><span class="sxs-lookup"><span data-stu-id="19634-102">For a conditional expression `c ? e1 : e2`, when</span></span>

1. <span data-ttu-id="19634-103">non esiste alcun tipo comune per `e1` e `e2` , o</span><span class="sxs-lookup"><span data-stu-id="19634-103">there is no common type for `e1` and `e2`, or</span></span>
2. <span data-ttu-id="19634-104">per cui esiste un tipo comune, ma una delle espressioni `e1` o `e2` Nessuna conversione implicita a tale tipo</span><span class="sxs-lookup"><span data-stu-id="19634-104">for which a common type exists but one of the expressions `e1` or `e2` has no implicit conversion to that type</span></span>

<span data-ttu-id="19634-105">viene definita una nuova *conversione di espressione condizionale* che consente una conversione implicita dall'espressione condizionale a qualsiasi tipo `T` per il quale esiste una conversione da espressione da `e1` a `T` e anche da `e2` a `T` .</span><span class="sxs-lookup"><span data-stu-id="19634-105">we define a new *conditional expression conversion* that permits an implicit conversion from the conditional expression to any type `T` for which there is a conversion-from-expression from `e1` to `T` and also from `e2` to `T`.</span></span>  <span data-ttu-id="19634-106">Se un'espressione condizionale non dispone di un tipo comune tra `e1` e `e2` né è soggetto a una conversione di *espressione condizionale*, è un errore.</span><span class="sxs-lookup"><span data-stu-id="19634-106">It is an error if a conditional expression neither has a common type between `e1` and `e2` nor is subject to a *conditional expression conversion*.</span></span>

### <a name="open-issues"></a><span data-ttu-id="19634-107">Problemi non risolti</span><span class="sxs-lookup"><span data-stu-id="19634-107">Open Issues</span></span>

<span data-ttu-id="19634-108">Si vuole estendere la tipizzazione della destinazione ai casi in cui l'espressione condizionale ha un tipo comune per `e1` e `e2` ma non esiste alcuna conversione da tale tipo comune al tipo di destinazione.</span><span class="sxs-lookup"><span data-stu-id="19634-108">We would like to extend this target typing to cases in which the conditional expression has a common type for `e1` and `e2` but there is no conversion from that common type to the target type.</span></span> <span data-ttu-id="19634-109">In questo modo, la tipizzazione della destinazione dell'espressione condizionale verrà allineata alla tipizzazione della destinazione dell'espressione switch.</span><span class="sxs-lookup"><span data-stu-id="19634-109">That would bring target typing of the conditional expression into alignment of target typing of the switch expression.</span></span> <span data-ttu-id="19634-110">Tuttavia, si tratta di una modifica sostanziale:</span><span class="sxs-lookup"><span data-stu-id="19634-110">However we are concerned that would be a breaking change:</span></span>

```csharp
M(b ? 1 : 2); // calls M(long) without this feature; calls M(short) with this feature

void M(short);
void M(long);
```

<span data-ttu-id="19634-111">È possibile ridurre l'ambito della modifica sostanziale modificando le regole per una *migliore conversione dall'espressione*: la conversione da un'espressione condizionale a T1 è una conversione migliore dall'espressione rispetto alla conversione in T2 se la conversione a T1 non è una *conversione di espressione condizionale* e la conversione a T2 è una *conversione di espressione condizionale*.</span><span class="sxs-lookup"><span data-stu-id="19634-111">We could reduce the scope of the breaking change by modifying the rules for *better conversion from expression*: the conversion from a conditional expression to T1 is a better conversion from expression than the conversion to T2 if the conversion to T1 is not a *conditional expression conversion* and the conversion to T2 is a *conditional expression conversion*.</span></span>  <span data-ttu-id="19634-112">Che risolve la modifica di rilievo nel programma precedente (chiama `M(long)` con o senza questa funzionalità).</span><span class="sxs-lookup"><span data-stu-id="19634-112">That resolves the breaking change in the above program (it calls `M(long)` with or without this feature).</span></span> <span data-ttu-id="19634-113">Questo approccio presenta due svantaggi limitati.</span><span class="sxs-lookup"><span data-stu-id="19634-113">This approach does have two small downsides.</span></span>  <span data-ttu-id="19634-114">In primo luogo, non è esattamente uguale all'espressione switch:</span><span class="sxs-lookup"><span data-stu-id="19634-114">First, it is not quite the same as the switch expression:</span></span>

```csharp
M(b ? 1 : 2); // calls M(long)
M(b switch { true => 1, false => 2 }); // calls M(short)
```

<span data-ttu-id="19634-115">Si tratta comunque di una modifica sostanziale, ma è meno probabile che il relativo ambito influisca sui programmi reali:</span><span class="sxs-lookup"><span data-stu-id="19634-115">This is still a breaking change, but its scope is less likely to affect real programs:</span></span>

```csharp
M(b ? 1 : 2, 1); // calls M(long, long) without this feature; ambiguous with this feature.

M(short, short);
M(long, long);
```

<span data-ttu-id="19634-116">Questa operazione diventa ambigua perché la conversione a `long` è migliore per il primo argomento, perché non usa la *conversione dell'espressione condizionale*, ma la conversione a `short` è migliore per il secondo argomento (perché `short` è un *obiettivo di conversione migliore* di `long` ).</span><span class="sxs-lookup"><span data-stu-id="19634-116">This becomes ambiguous because the conversion to `long` is better for the first argument (because it does not use the *conditional expression conversion*), but the conversion to `short` is better for the second argument (because `short` is a *better conversion target* than `long`).</span></span> <span data-ttu-id="19634-117">Questa modifica di rilievo sembra meno grave perché non modifica automaticamente il comportamento di un programma esistente.</span><span class="sxs-lookup"><span data-stu-id="19634-117">This breaking change seems less serious because it does not silently change the behavior of an existing program.</span></span>

<span data-ttu-id="19634-118">Se si decide di apportare questa modifica alla proposta, si modificherà</span><span class="sxs-lookup"><span data-stu-id="19634-118">Should we elect to make this change to the proposal, we would change</span></span>

> #### <a name="better-conversion-from-expression"></a><span data-ttu-id="19634-119">Migliore conversione dall'espressione</span><span class="sxs-lookup"><span data-stu-id="19634-119">Better conversion from expression</span></span>
> 
> <span data-ttu-id="19634-120">Una conversione implicita `C1` che converte da un'espressione `E` a un tipo `T1` e una conversione implicita `C2` che converte da un'espressione `E` a un tipo `T2` , `C1` è una ***conversione migliore*** rispetto a `C2` se non `E` corrisponde esattamente a e almeno `T2` una delle seguenti condizioni:</span><span class="sxs-lookup"><span data-stu-id="19634-120">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>
> 
> * <span data-ttu-id="19634-121">`E`corrisponde esattamente `T1` ([espressione corrispondente esattamente](expressions.md#exactly-matching-expression))</span><span class="sxs-lookup"><span data-stu-id="19634-121">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
> * <span data-ttu-id="19634-122">`T1`è una destinazione di conversione migliore rispetto a `T2` ([migliore destinazione di conversione](expressions.md#better-conversion-target))</span><span class="sxs-lookup"><span data-stu-id="19634-122">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target))</span></span>

<span data-ttu-id="19634-123">to</span><span class="sxs-lookup"><span data-stu-id="19634-123">to</span></span>

> #### <a name="better-conversion-from-expression"></a><span data-ttu-id="19634-124">Migliore conversione dall'espressione</span><span class="sxs-lookup"><span data-stu-id="19634-124">Better conversion from expression</span></span>
> 
> <span data-ttu-id="19634-125">Una conversione implicita `C1` che converte da un'espressione `E` a un tipo `T1` e una conversione implicita `C2` che converte da un'espressione `E` a un tipo `T2` , `C1` è una ***conversione migliore*** rispetto a `C2` se non `E` corrisponde esattamente a e almeno `T2` una delle seguenti condizioni:</span><span class="sxs-lookup"><span data-stu-id="19634-125">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>
> 
> * <span data-ttu-id="19634-126">`E`corrisponde esattamente `T1` ([espressione corrispondente esattamente](expressions.md#exactly-matching-expression))</span><span class="sxs-lookup"><span data-stu-id="19634-126">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
> * <span data-ttu-id="19634-127">\*\*`C1`non è una *conversione di espressione condizionale* ed `C2` è una \* conversione di espressione condizionale \* \* \*.</span><span class="sxs-lookup"><span data-stu-id="19634-127">\*\*`C1` is not a *conditional expression conversion* and `C2` is a \*conditional expression conversion\*\*\*.</span></span>
> * <span data-ttu-id="19634-128">`T1`è una destinazione di conversione migliore rispetto a `T2` ([migliore destinazione di conversione](expressions.md#better-conversion-target)) \* \* e `C1` e `C2` sono entrambe *conversioni di espressioni condizionali* o nessuna delle due è una \* conversione di espressione condizionale \* \* \*.</span><span class="sxs-lookup"><span data-stu-id="19634-128">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target)) \*\*and either `C1` and `C2` are both *conditional expression conversions* or neither is a \*conditional expression conversion\*\*\*.</span></span>
