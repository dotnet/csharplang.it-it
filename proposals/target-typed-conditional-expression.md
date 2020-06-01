---
ms.openlocfilehash: bfdd44c81a40c49b26ac15a69d2327f795bd88e6
ms.sourcegitcommit: 85263bfff8512e3e6fa4da7dc75e892937076de8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84239040"
---
# <a name="target-typed-conditional-expression"></a><span data-ttu-id="ee625-101">Espressione condizionale tipizzata di destinazione</span><span class="sxs-lookup"><span data-stu-id="ee625-101">Target-Typed Conditional Expression</span></span>

## <a name="conditional-expression-conversion"></a><span data-ttu-id="ee625-102">Conversione di espressioni condizionali</span><span class="sxs-lookup"><span data-stu-id="ee625-102">Conditional Expression Conversion</span></span>

<span data-ttu-id="ee625-103">Per un'espressione condizionale `c ? e1 : e2` , quando</span><span class="sxs-lookup"><span data-stu-id="ee625-103">For a conditional expression `c ? e1 : e2`, when</span></span>

1. <span data-ttu-id="ee625-104">non esiste alcun tipo comune per `e1` e `e2` , o</span><span class="sxs-lookup"><span data-stu-id="ee625-104">there is no common type for `e1` and `e2`, or</span></span>
2. <span data-ttu-id="ee625-105">per cui esiste un tipo comune, ma una delle espressioni `e1` o `e2` Nessuna conversione implicita a tale tipo</span><span class="sxs-lookup"><span data-stu-id="ee625-105">for which a common type exists but one of the expressions `e1` or `e2` has no implicit conversion to that type</span></span>

<span data-ttu-id="ee625-106">viene definita una nuova conversione implicita di *espressioni condizionali* che consente una conversione implicita dall'espressione condizionale a qualsiasi tipo `T` per il quale è presente una conversione da espressione da `e1` a `T` e anche da `e2` a `T` .</span><span class="sxs-lookup"><span data-stu-id="ee625-106">we define a new implicit *conditional expression conversion* that permits an implicit conversion from the conditional expression to any type `T` for which there is a conversion-from-expression from `e1` to `T` and also from `e2` to `T`.</span></span>  <span data-ttu-id="ee625-107">Se un'espressione condizionale non dispone di un tipo comune tra `e1` e `e2` né è soggetto a una conversione di *espressione condizionale*, è un errore.</span><span class="sxs-lookup"><span data-stu-id="ee625-107">It is an error if a conditional expression neither has a common type between `e1` and `e2` nor is subject to a *conditional expression conversion*.</span></span>

## <a name="better-conversion-from-expression"></a><span data-ttu-id="ee625-108">Migliore conversione dall'espressione</span><span class="sxs-lookup"><span data-stu-id="ee625-108">Better Conversion from Expression</span></span>

<span data-ttu-id="ee625-109">Modifiche apportate</span><span class="sxs-lookup"><span data-stu-id="ee625-109">We change</span></span>

> #### <a name="better-conversion-from-expression"></a><span data-ttu-id="ee625-110">Migliore conversione dall'espressione</span><span class="sxs-lookup"><span data-stu-id="ee625-110">Better conversion from expression</span></span>
> 
> <span data-ttu-id="ee625-111">Una conversione implicita `C1` che converte da un'espressione `E` a un tipo `T1` e una conversione implicita `C2` che converte da un'espressione `E` a un tipo `T2` , `C1` è una ***conversione migliore*** rispetto a `C2` se non `E` corrisponde esattamente a e almeno `T2` una delle seguenti condizioni:</span><span class="sxs-lookup"><span data-stu-id="ee625-111">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>
> 
> * <span data-ttu-id="ee625-112">`E`corrisponde esattamente `T1` ([espressione corrispondente esattamente](expressions.md#exactly-matching-expression))</span><span class="sxs-lookup"><span data-stu-id="ee625-112">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
> * <span data-ttu-id="ee625-113">`T1`è una destinazione di conversione migliore rispetto a `T2` ([migliore destinazione di conversione](expressions.md#better-conversion-target))</span><span class="sxs-lookup"><span data-stu-id="ee625-113">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target))</span></span>

<span data-ttu-id="ee625-114">to</span><span class="sxs-lookup"><span data-stu-id="ee625-114">to</span></span>

> #### <a name="better-conversion-from-expression"></a><span data-ttu-id="ee625-115">Migliore conversione dall'espressione</span><span class="sxs-lookup"><span data-stu-id="ee625-115">Better conversion from expression</span></span>
> 
> <span data-ttu-id="ee625-116">Una conversione implicita `C1` che converte da un'espressione `E` a un tipo `T1` e una conversione implicita `C2` che converte da un'espressione `E` a un tipo `T2` , `C1` è una ***conversione migliore*** rispetto a `C2` se non `E` corrisponde esattamente a e almeno `T2` una delle seguenti condizioni:</span><span class="sxs-lookup"><span data-stu-id="ee625-116">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>
> 
> * <span data-ttu-id="ee625-117">`E`corrisponde esattamente `T1` ([espressione corrispondente esattamente](expressions.md#exactly-matching-expression))</span><span class="sxs-lookup"><span data-stu-id="ee625-117">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
> * <span data-ttu-id="ee625-118">\*\*`C1`non è una *conversione di espressione condizionale* ed `C2` è una \* conversione di espressione condizionale \* \* \*.</span><span class="sxs-lookup"><span data-stu-id="ee625-118">\*\*`C1` is not a *conditional expression conversion* and `C2` is a \*conditional expression conversion\*\*\*.</span></span>
> * <span data-ttu-id="ee625-119">`T1`è una destinazione di conversione migliore rispetto a `T2` ([migliore destinazione di conversione](expressions.md#better-conversion-target)) \* \* e `C1` e `C2` sono entrambe *conversioni di espressioni condizionali* o nessuna delle due è una \* conversione di espressione condizionale \* \* \*.</span><span class="sxs-lookup"><span data-stu-id="ee625-119">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target)) \*\*and either `C1` and `C2` are both *conditional expression conversions* or neither is a \*conditional expression conversion\*\*\*.</span></span>

## <a name="cast-expression"></a><span data-ttu-id="ee625-120">Espressione cast</span><span class="sxs-lookup"><span data-stu-id="ee625-120">Cast Expression</span></span>

<span data-ttu-id="ee625-121">La specifica del linguaggio C# corrente indica</span><span class="sxs-lookup"><span data-stu-id="ee625-121">The current C# language specification says</span></span>

> <span data-ttu-id="ee625-122">Un *cast_expression* del form `(T)E` , dove `T` è un *tipo* e `E` è un *unary_expression*, esegue una conversione esplicita ([conversioni esplicite](conversions.md#explicit-conversions)) del valore di `E` nel tipo `T` .</span><span class="sxs-lookup"><span data-stu-id="ee625-122">A *cast_expression* of the form `(T)E`, where `T` is a *type* and `E` is a *unary_expression*, performs an explicit conversion ([Explicit conversions](conversions.md#explicit-conversions)) of the value of `E` to type `T`.</span></span>

<span data-ttu-id="ee625-123">In presenza della conversione dell' *espressione condizionale* può essere presente più di una possibile conversione da `E` a `T` .</span><span class="sxs-lookup"><span data-stu-id="ee625-123">In the presence of the *conditional expression conversion* there may be more than one possible conversion from `E` to `T`.</span></span> <span data-ttu-id="ee625-124">Con l'aggiunta della *conversione di espressioni condizionali*, si preferisce qualsiasi altra conversione a una *conversione di espressione*condizionale e si usa la *conversione dell'espressione condizionale* solo come ultima risorsa.</span><span class="sxs-lookup"><span data-stu-id="ee625-124">With the addition of *conditional expression conversion*, we prefer any other conversion to a *conditional expression conversion*, and use the *conditional expression conversion* only as a last resort.</span></span>

## <a name="design-notes"></a><span data-ttu-id="ee625-125">Note di progettazione</span><span class="sxs-lookup"><span data-stu-id="ee625-125">Design Notes</span></span>

<span data-ttu-id="ee625-126">Il motivo per cui la modifica alla *conversione migliore da Expression* è la gestione di un caso simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="ee625-126">The reason for the change to *Better conversion from expression* is to handle a case such as this:</span></span>

```csharp
M(b ? 1 : 2);

void M(short);
void M(long);
```

<span data-ttu-id="ee625-127">Questo approccio presenta due svantaggi limitati.</span><span class="sxs-lookup"><span data-stu-id="ee625-127">This approach does have two small downsides.</span></span>  <span data-ttu-id="ee625-128">In primo luogo, non è esattamente uguale all'espressione switch:</span><span class="sxs-lookup"><span data-stu-id="ee625-128">First, it is not quite the same as the switch expression:</span></span>

```csharp
M(b ? 1 : 2); // calls M(long)
M(b switch { true => 1, false => 2 }); // calls M(short)
```

<span data-ttu-id="ee625-129">Si tratta comunque di una modifica sostanziale, ma è meno probabile che il relativo ambito influisca sui programmi reali:</span><span class="sxs-lookup"><span data-stu-id="ee625-129">This is still a breaking change, but its scope is less likely to affect real programs:</span></span>

```csharp
M(b ? 1 : 2, 1); // calls M(long, long) without this feature; ambiguous with this feature.

M(short, short);
M(long, long);
```

<span data-ttu-id="ee625-130">Questa operazione diventa ambigua perché la conversione a `long` è migliore per il primo argomento, perché non usa la *conversione dell'espressione condizionale*, ma la conversione a `short` è migliore per il secondo argomento (perché `short` è un *obiettivo di conversione migliore* di `long` ).</span><span class="sxs-lookup"><span data-stu-id="ee625-130">This becomes ambiguous because the conversion to `long` is better for the first argument (because it does not use the *conditional expression conversion*), but the conversion to `short` is better for the second argument (because `short` is a *better conversion target* than `long`).</span></span> <span data-ttu-id="ee625-131">Questa modifica di rilievo sembra meno grave perché non modifica automaticamente il comportamento di un programma esistente.</span><span class="sxs-lookup"><span data-stu-id="ee625-131">This breaking change seems less serious because it does not silently change the behavior of an existing program.</span></span>

<span data-ttu-id="ee625-132">Il motivo per le note sull'espressione cast è quello di gestire un caso simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="ee625-132">The reason for the notes on the cast expression is to handle a case such as this:</span></span>

```csharp
_ = (short)(b ? 1 : 2);
```

<span data-ttu-id="ee625-133">Questo programma utilizza attualmente la conversione esplicita da `int` a `short` e si desidera mantenere il significato della lingua corrente del programma.</span><span class="sxs-lookup"><span data-stu-id="ee625-133">This program currently uses the explicit conversion from `int` to `short`, and we want to preserve the current language meaning of this program.</span></span>  <span data-ttu-id="ee625-134">La modifica potrebbe non essere osservabile in fase di esecuzione, ma con il programma seguente la modifica sarebbe osservabile:</span><span class="sxs-lookup"><span data-stu-id="ee625-134">The change would be unobservable at runtime, but with the following program the change would be observable:</span></span>

```csharp
_ = (A)(b ? c : d);
```

<span data-ttu-id="ee625-135">dove `c` è di tipo `C` , `d` è di tipo `D` ed esiste una conversione implicita definita dall'utente da a e una conversione implicita definita dall'utente da a `C` `D` `D` `A` e una conversione implicita definita dall'utente da `C` a `A` .</span><span class="sxs-lookup"><span data-stu-id="ee625-135">where `c` is of type `C`, `d` is of type `D`, and there is an implicit user-defined conversion from `C` to `D`, and an implicit user-defined conversion from `D` to `A`, and an implicit user-defined conversion from `C` to `A`.</span></span> <span data-ttu-id="ee625-136">Se questo codice viene compilato prima di C# 9,0, quando `b` è true viene convertito da `c` a `D` then a `A` .</span><span class="sxs-lookup"><span data-stu-id="ee625-136">If this code is compiled before C# 9.0, when `b` is true we convert from `c` to `D` then to `A`.</span></span> <span data-ttu-id="ee625-137">Se si usa la *conversione dell'espressione condizionale*, quando `b` è true viene convertito da `c` a `A` direttamente, che esegue una sequenza diversa di codice utente.</span><span class="sxs-lookup"><span data-stu-id="ee625-137">If we use the *conditional expression conversion*, then when `b` is true we convert from `c` to `A` directly, which executes a different sequence of user code.</span></span> <span data-ttu-id="ee625-138">Quindi consideriamo la *conversione dell'espressione condizionale* come ultima risorsa in un cast, per mantenere il comportamento esistente.</span><span class="sxs-lookup"><span data-stu-id="ee625-138">Therefore we treat the *conditional expression conversion* as a last resort in a cast, to preserve existing behavior.</span></span>
