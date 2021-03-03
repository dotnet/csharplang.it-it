---
ms.openlocfilehash: 44980f4dadfb80a5ef1fccb09ef6490f80a12c5c
ms.sourcegitcommit: 3f8f57e294e2952af19a5231e6b9c7ff85d0ed56
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101751673"
---
# <a name="improved-definite-assignment-analysis"></a><span data-ttu-id="69cf7-101">Analisi di assegnazione definita migliorata</span><span class="sxs-lookup"><span data-stu-id="69cf7-101">Improved Definite Assignment Analysis</span></span>

## <a name="summary"></a><span data-ttu-id="69cf7-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="69cf7-102">Summary</span></span>
<span data-ttu-id="69cf7-103">L' [analisi di assegnazione definita](../spec/variables.md#definite-assignment) come specificato presenta alcuni gap che hanno causato l'inconveniente degli utenti.</span><span class="sxs-lookup"><span data-stu-id="69cf7-103">[Definite assignment analysis](../spec/variables.md#definite-assignment) as specified has a few gaps which have caused users inconvenience.</span></span> <span data-ttu-id="69cf7-104">In particolare, scenari che coinvolgono il confronto con costanti booleane, accesso condizionale e Unione di valori null.</span><span class="sxs-lookup"><span data-stu-id="69cf7-104">In particular, scenarios involving comparison to boolean constants, conditional-access, and null coalescing.</span></span>

## <a name="related-discussions-and-issues"></a><span data-ttu-id="69cf7-105">Discussioni e problemi correlati</span><span class="sxs-lookup"><span data-stu-id="69cf7-105">Related discussions and issues</span></span>
<span data-ttu-id="69cf7-106">csharplang discussione di questa proposta: https://github.com/dotnet/csharplang/discussions/4240</span><span class="sxs-lookup"><span data-stu-id="69cf7-106">csharplang discussion of this proposal: https://github.com/dotnet/csharplang/discussions/4240</span></span>

<span data-ttu-id="69cf7-107">Probabilmente è possibile trovare una decina di report per gli utenti tramite questa o query simili, ovvero cercare "assegnazione definita" invece di "CS0165" o cercare in csharplang.</span><span class="sxs-lookup"><span data-stu-id="69cf7-107">Probably a dozen or so user reports can be found via this or similar queries (i.e. search for "definite assignment" instead of "CS0165", or search in csharplang).</span></span>
<span data-ttu-id="69cf7-108">https://github.com/dotnet/roslyn/issues?q=is%3Aclosed+is%3Aissue+label%3A%22Resolution-By+Design%22+cs0165</span><span class="sxs-lookup"><span data-stu-id="69cf7-108">https://github.com/dotnet/roslyn/issues?q=is%3Aclosed+is%3Aissue+label%3A%22Resolution-By+Design%22+cs0165</span></span>

<span data-ttu-id="69cf7-109">Sono stati inclusi problemi correlati negli scenari indicati di seguito per dare un'idea dell'effetto relativo di ogni scenario.</span><span class="sxs-lookup"><span data-stu-id="69cf7-109">I have included related issues in the scenarios below to give a sense of the relative impact of each scenario.</span></span>

## <a name="scenarios"></a><span data-ttu-id="69cf7-110">Scenari</span><span class="sxs-lookup"><span data-stu-id="69cf7-110">Scenarios</span></span>

<span data-ttu-id="69cf7-111">Come punto di riferimento, iniziamo con una nota "felice caso" che funziona in un'assegnazione definita e ammette i valori null.</span><span class="sxs-lookup"><span data-stu-id="69cf7-111">As a point of reference, let's start with a well-known "happy case" that does work in definite assignment and in nullable.</span></span>
```cs
#nullable enable

C c = new C();
if (c != null && c.M(out object obj0))
{
    obj0.ToString(); // ok
}

public class C
{
    public bool M(out object obj)
    {
        obj = new object();
        return true;
    }
}
```

### <a name="comparison-to-bool-constant"></a><span data-ttu-id="69cf7-112">Confronto con costante bool</span><span class="sxs-lookup"><span data-stu-id="69cf7-112">Comparison to bool constant</span></span>
- https://github.com/dotnet/csharplang/discussions/801
- https://github.com/dotnet/roslyn/issues/45582
  - <span data-ttu-id="69cf7-113">Collegamenti a altri quattro problemi per cui gli utenti sono interessati.</span><span class="sxs-lookup"><span data-stu-id="69cf7-113">Links to 4 other issues where people were affected by this.</span></span>
```cs
if ((c != null && c.M(out object obj1)) == true)
{
    obj1.ToString(); // undesired error
}

if ((c != null && c.M(out object obj2)) is true)
{
    obj2.ToString(); // undesired error
}
```

### <a name="comparison-between-a-conditional-access-and-a-constant-value"></a><span data-ttu-id="69cf7-114">Confronto tra un accesso condizionale e un valore costante</span><span class="sxs-lookup"><span data-stu-id="69cf7-114">Comparison between a conditional access and a constant value</span></span>

- https://github.com/dotnet/roslyn/issues/33559
- https://github.com/dotnet/csharplang/discussions/4214
- https://github.com/dotnet/csharplang/issues/3659
- https://github.com/dotnet/csharplang/issues/3485
- https://github.com/dotnet/csharplang/issues/3659

<span data-ttu-id="69cf7-115">Questo scenario è probabilmente quello più grande.</span><span class="sxs-lookup"><span data-stu-id="69cf7-115">This scenario is probably the biggest one.</span></span> <span data-ttu-id="69cf7-116">Questa operazione è supportata in Nullable ma non nell'assegnazione definitiva.</span><span class="sxs-lookup"><span data-stu-id="69cf7-116">We do support this in nullable but not in definite assignment.</span></span>

```cs
if (c?.M(out object obj3) == true)
{
    obj3.ToString(); // undesired error
}
```

### <a name="conditional-access-coalesced-to-a-bool-constant"></a><span data-ttu-id="69cf7-117">Accesso condizionale Unito a una costante bool</span><span class="sxs-lookup"><span data-stu-id="69cf7-117">Conditional access coalesced to a bool constant</span></span>

- https://github.com/dotnet/csharplang/discussions/916
- https://github.com/dotnet/csharplang/issues/3365

<span data-ttu-id="69cf7-118">Questo scenario è molto simile a quello precedente.</span><span class="sxs-lookup"><span data-stu-id="69cf7-118">This scenario is very similar to the previous one.</span></span> <span data-ttu-id="69cf7-119">Questa operazione è supportata anche in Nullable ma non nell'assegnazione definita.</span><span class="sxs-lookup"><span data-stu-id="69cf7-119">This is also supported in nullable but not in definite assignment.</span></span>

```cs
if (c?.M(out object obj4) ?? false)
{
    obj4.ToString(); // undesired error
}
```

### <a name="conditional-expressions-where-one-arm-is-a-bool-constant"></a><span data-ttu-id="69cf7-120">Espressioni condizionali in cui un ARM è una costante bool</span><span class="sxs-lookup"><span data-stu-id="69cf7-120">Conditional expressions where one arm is a bool constant</span></span>
- https://github.com/dotnet/roslyn/issues/4272

<span data-ttu-id="69cf7-121">Vale la pena sottolineare che è già presente un comportamento speciale quando l'espressione della condizione è costante (ad esempio `true ? a : b` ).</span><span class="sxs-lookup"><span data-stu-id="69cf7-121">It's worth pointing out that we already have special behavior for when the condition expression is constant (i.e. `true ? a : b`).</span></span> <span data-ttu-id="69cf7-122">Si visita solo in modo non condizionale il ARM indicato dalla condizione costante e si ignora l'altro ARM.</span><span class="sxs-lookup"><span data-stu-id="69cf7-122">We just unconditionally visit the arm indicated by the constant condition and ignore the other arm.</span></span>

<span data-ttu-id="69cf7-123">Si noti inoltre che questo scenario non è stato gestito in Nullable.</span><span class="sxs-lookup"><span data-stu-id="69cf7-123">Also note that we haven't handled this scenario in nullable.</span></span>

```cs
if (c != null ? c.M(out object obj4) : false)
{
    obj4.ToString(); // undesired error
}
```

# <a name="specification"></a><span data-ttu-id="69cf7-124">Specifiche</span><span class="sxs-lookup"><span data-stu-id="69cf7-124">Specification</span></span>

## <a name="-null-conditional-operator-expressions"></a><span data-ttu-id="69cf7-125">?.</span><span class="sxs-lookup"><span data-stu-id="69cf7-125">?.</span></span> <span data-ttu-id="69cf7-126">espressioni (operatore condizionale null)</span><span class="sxs-lookup"><span data-stu-id="69cf7-126">(null-conditional operator) expressions</span></span>
<span data-ttu-id="69cf7-127">Viene introdotta una nuova sezione **?. espressioni (operatore condizionale null)**.</span><span class="sxs-lookup"><span data-stu-id="69cf7-127">We introduce a new section **?. (null-conditional operator) expressions**.</span></span> <span data-ttu-id="69cf7-128">Per il contesto, vedere la specifica dell' [operatore condizionale null](../spec/expressions.md#null-conditional-operator) e [le regole di assegnazione definite](../spec/variables.md#precise-rules-for-determining-definite-assignment) .</span><span class="sxs-lookup"><span data-stu-id="69cf7-128">See the [null-conditional operator](../spec/expressions.md#null-conditional-operator) specification and [definite assignment rules](../spec/variables.md#precise-rules-for-determining-definite-assignment) for context.</span></span>

<span data-ttu-id="69cf7-129">Come per le regole di assegnazione definite sopra indicate, si fa riferimento a una variabile inizialmente non assegnata come *v*.</span><span class="sxs-lookup"><span data-stu-id="69cf7-129">As in the definite assignment rules linked above, we refer to a given initially unassigned variable as *v*.</span></span>

<span data-ttu-id="69cf7-130">Viene introdotto il concetto di "Contains direttamente".</span><span class="sxs-lookup"><span data-stu-id="69cf7-130">We introduce the concept of "directly contains".</span></span> <span data-ttu-id="69cf7-131">Un'espressione *e* viene detto "contiene direttamente" una sottoespressione *e <sub>1</sub>* se si verifica una delle condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="69cf7-131">An expression *E* is said to "directly contain" a subexpression *E<sub>1</sub>* if one of the following conditions holds:</span></span>
- <span data-ttu-id="69cf7-132">*E* è *e <sub>1</sub>*.</span><span class="sxs-lookup"><span data-stu-id="69cf7-132">*E* is *E<sub>1</sub>*.</span></span> <span data-ttu-id="69cf7-133">Ad esempio, `a?.b()` contiene direttamente l'espressione `a?.b()` .</span><span class="sxs-lookup"><span data-stu-id="69cf7-133">For example, `a?.b()` directly contains the expression `a?.b()`.</span></span>
- <span data-ttu-id="69cf7-134">Se *e* è un'espressione tra parentesi `(E2)` e e *<sub>2</sub>* contengono direttamente *e <sub>1</sub>*.</span><span class="sxs-lookup"><span data-stu-id="69cf7-134">If *E* is a parenthesized expression `(E2)`, and *E <sub>2</sub>* directly contains *E<sub>1</sub>*.</span></span>
- <span data-ttu-id="69cf7-135">Se *e* è un'espressione di operatore con indulgenza null `E2!` e e *<sub>2</sub>* contengono direttamente *e <sub>1</sub>*.</span><span class="sxs-lookup"><span data-stu-id="69cf7-135">If *E* is a null-forgiving operator expression `E2!`, and *E <sub>2</sub>* directly contains *E<sub>1</sub>*.</span></span>

<span data-ttu-id="69cf7-136">Per un'espressione *e* del form `primary_expression null_conditional_operations` , lasciare che *e <sub>0</sub>* sia l'espressione ottenuta rimuovendo in modo testuale il leader?</span><span class="sxs-lookup"><span data-stu-id="69cf7-136">For an expression *E* of the form `primary_expression null_conditional_operations`, let *E<sub>0</sub>* be the expression obtained by textually removing the leading ?</span></span> <span data-ttu-id="69cf7-137">da ogni *null_conditional_operations* di *E* che ne hanno uno, come nella specifica collegata sopra.</span><span class="sxs-lookup"><span data-stu-id="69cf7-137">from each of the *null_conditional_operations* of *E* that have one, as in the linked specification above.</span></span>

<span data-ttu-id="69cf7-138">Nelle sezioni successive si fa riferimento a *E <sub>0</sub>* come *controparte non condizionale* per l'espressione condizionale null.</span><span class="sxs-lookup"><span data-stu-id="69cf7-138">In subsequent sections we will refer to *E <sub>0</sub>* as the *non-conditional counterpart* to the null-conditional expression.</span></span> <span data-ttu-id="69cf7-139">Si noti che alcune espressioni nelle sezioni successive sono soggette a regole aggiuntive che si applicano solo quando uno degli operandi contiene direttamente un'espressione condizionale null.</span><span class="sxs-lookup"><span data-stu-id="69cf7-139">Note that some expressions in subsequent sections are subject to additional rules that only apply when one of the operands directly contains a null-conditional expression.</span></span>

- <span data-ttu-id="69cf7-140">Lo stato di assegnazione definito di *v* in qualsiasi punto all'interno di *E* corrisponde allo stato di assegnazione definito nel punto corrispondente all'interno di *E0*.</span><span class="sxs-lookup"><span data-stu-id="69cf7-140">The definite assignment state of *v* at any point within *E* is the same as the definite assignment state at the corresponding point within *E0*.</span></span>
- <span data-ttu-id="69cf7-141">Lo stato di assegnazione definito di *v* dopo *E* corrisponde allo stato di assegnazione definito di *v* dopo *primary_expression*.</span><span class="sxs-lookup"><span data-stu-id="69cf7-141">The definite assignment state of *v* after *E* is the same as the definite assignment state of *v* after *primary_expression*.</span></span>

### <a name="remarks"></a><span data-ttu-id="69cf7-142">Commenti</span><span class="sxs-lookup"><span data-stu-id="69cf7-142">Remarks</span></span>
<span data-ttu-id="69cf7-143">Viene usato il concetto di "Contains" direttamente per consentire di ignorare espressioni "wrapper" relativamente semplici durante l'analisi degli accessi condizionali confrontati con altri valori.</span><span class="sxs-lookup"><span data-stu-id="69cf7-143">We use the concept of "directly contains" to allow us to skip over relatively simple "wrapper" expressions when analyzing conditional accesses that are compared to other values.</span></span> <span data-ttu-id="69cf7-144">Si prevede, ad esempio, che `((a?.b(out x))!) == true` si verifichi lo stesso stato del flusso `a?.b == true` in generale.</span><span class="sxs-lookup"><span data-stu-id="69cf7-144">For example, `((a?.b(out x))!) == true` is expected to result in the same flow state as `a?.b == true` in general.</span></span>

<span data-ttu-id="69cf7-145">Quando si considera la possibilità di assegnare una variabile in un punto specifico all'interno di un'espressione condizionale null, si presuppone che tutte le operazioni precedenti condizionali null all'interno della stessa espressione condizionale null abbiano avuto esito positivo.</span><span class="sxs-lookup"><span data-stu-id="69cf7-145">When we consider whether a variable is assigned at a given point within a null-conditional expression, we simply assume that any preceding null-conditional operations within the same null-conditional expression succeeded.</span></span>

<span data-ttu-id="69cf7-146">Ad esempio, data un'espressione condizionale `a?.b(out x)?.c(x)` , la controparte non condizionale è `a.b(out x).c(x)` .</span><span class="sxs-lookup"><span data-stu-id="69cf7-146">For example, given a conditional expression `a?.b(out x)?.c(x)`, the non-conditional counterpart is `a.b(out x).c(x)`.</span></span> <span data-ttu-id="69cf7-147">Se si vuole ottenere lo stato di assegnazione definito di `x` prima `?.c(x)` , ad esempio, si esegue un'analisi ipotetica di e si `a.b(out x)` Usa lo stato risultante come input per `?.c(x)` .</span><span class="sxs-lookup"><span data-stu-id="69cf7-147">If we want to know the definite assignment state of `x` before `?.c(x)`, for example, then we perform a "hypothetical" analysis of `a.b(out x)` and use the resulting state as an input to `?.c(x)`.</span></span>

## <a name="boolean-constant-expressions"></a><span data-ttu-id="69cf7-148">Espressioni costanti booleane</span><span class="sxs-lookup"><span data-stu-id="69cf7-148">Boolean constant expressions</span></span>
<span data-ttu-id="69cf7-149">Viene introdotta una nuova sezione "espressioni costanti booleane":</span><span class="sxs-lookup"><span data-stu-id="69cf7-149">We introduce a new section "Boolean constant expressions":</span></span>

<span data-ttu-id="69cf7-150">Per un'espressione *expr* in cui *expr* è un'espressione costante con un valore bool:</span><span class="sxs-lookup"><span data-stu-id="69cf7-150">For an expression *expr* where *expr* is a constant expression with a bool value:</span></span>
- <span data-ttu-id="69cf7-151">Lo stato di assegnazione definito di *v* dopo *expr* è determinato da:</span><span class="sxs-lookup"><span data-stu-id="69cf7-151">The definite assignment state of *v* after *expr* is determined by:</span></span>
  - <span data-ttu-id="69cf7-152">Se *expr* è un'espressione costante con valore *true* e lo stato di *v* prima di *expr* è "non assegnato definitivamente", lo stato di *v* dopo *expr* è "definitely unsigned when false".</span><span class="sxs-lookup"><span data-stu-id="69cf7-152">If *expr* is a constant expression with value *true*, and the state of *v* before *expr* is "not definitely assigned", then the state of *v* after *expr* is "definitely assigned when false".</span></span>
  - <span data-ttu-id="69cf7-153">Se *expr* è un'espressione costante con valore *false* e lo stato di *v* prima di *expr* è "non assegnato definitivamente", lo stato di *v* dopo *expr* è "definitely Assigned when true".</span><span class="sxs-lookup"><span data-stu-id="69cf7-153">If *expr* is a constant expression with value *false*, and the state of *v* before *expr* is "not definitely assigned", then the state of *v* after *expr* is "definitely assigned when true".</span></span>

### <a name="remarks"></a><span data-ttu-id="69cf7-154">Commenti</span><span class="sxs-lookup"><span data-stu-id="69cf7-154">Remarks</span></span>

<span data-ttu-id="69cf7-155">Si presuppone che se un'espressione ha un valore costante bool `false` , ad esempio, è Impossibile raggiungere qualsiasi ramo che richiede che l'espressione restituisca `true` .</span><span class="sxs-lookup"><span data-stu-id="69cf7-155">We assume that if an expression has a constant value bool `false`, for example, it's impossible to reach any branch that requires the expression to return `true`.</span></span> <span data-ttu-id="69cf7-156">Pertanto si presuppone che le variabili siano assegnate in modo sicuro in tali rami.</span><span class="sxs-lookup"><span data-stu-id="69cf7-156">Therefore variables are assumed to be definitely assigned in such branches.</span></span> <span data-ttu-id="69cf7-157">Questa operazione si integra perfettamente con le modifiche delle specifiche per le espressioni, ad esempio `??` e `?:` , e consentendo numerosi scenari utili.</span><span class="sxs-lookup"><span data-stu-id="69cf7-157">This ends up combining nicely with the spec changes for expressions like `??` and `?:` and enabling a lot of useful scenarios.</span></span>

<span data-ttu-id="69cf7-158">È anche importante notare che non ci si aspetta mai di trovarsi in uno stato condizionale *prima* di visitare un'espressione costante.</span><span class="sxs-lookup"><span data-stu-id="69cf7-158">It's also worth noting that we never expect to be in a conditional state *before* visiting a constant expression.</span></span> <span data-ttu-id="69cf7-159">Questo è il motivo per cui non si tiene conto di scenari quali "*expr* è un'espressione costante con valore *true* e lo stato di *v* prima di *expr* è" assegnato definitivamente se true ".</span><span class="sxs-lookup"><span data-stu-id="69cf7-159">That's why we do not account for scenarios such as "*expr* is a constant expression with value *true*, and the state of *v* before *expr* is "definitely assigned when true".</span></span>

## <a name="-null-coalescing-expressions-augment"></a><span data-ttu-id="69cf7-160">??</span><span class="sxs-lookup"><span data-stu-id="69cf7-160">??</span></span> <span data-ttu-id="69cf7-161">(espressioni con Unione null) potenziare</span><span class="sxs-lookup"><span data-stu-id="69cf7-161">(null-coalescing expressions) augment</span></span>
<span data-ttu-id="69cf7-162">La sezione è stata ampliata [? espressioni (Unione di valori null)](../spec/variables.md#-null-coalescing-expressions) come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="69cf7-162">We augment the section [?? (null coalescing) expressions](../spec/variables.md#-null-coalescing-expressions) as follows:</span></span>

<span data-ttu-id="69cf7-163">Per un'espressione *expr* nel formato `expr_first ?? expr_second` :</span><span class="sxs-lookup"><span data-stu-id="69cf7-163">For an expression *expr* of the form `expr_first ?? expr_second`:</span></span>
- <span data-ttu-id="69cf7-164">...</span><span class="sxs-lookup"><span data-stu-id="69cf7-164">...</span></span>
- <span data-ttu-id="69cf7-165">Lo stato di assegnazione definito di *v* dopo *expr* è determinato da:</span><span class="sxs-lookup"><span data-stu-id="69cf7-165">The definite assignment state of *v* after *expr* is determined by:</span></span>
  - <span data-ttu-id="69cf7-166">...</span><span class="sxs-lookup"><span data-stu-id="69cf7-166">...</span></span>
  - <span data-ttu-id="69cf7-167">Se *expr_first* contiene direttamente *un'espressione condizionale null e e* *v* viene assegnato definitivamente dopo la controparte non condizionale *e <sub>0</sub>*, lo stato di assegnazione definito di *v* dopo *expr* corrisponde allo stato di assegnazione definito di *v* dopo la *expr_second*.</span><span class="sxs-lookup"><span data-stu-id="69cf7-167">If *expr_first* directly contains a null-conditional expression *E*, and *v* is definitely assigned after the non-conditional counterpart *E <sub>0</sub>*, then the definite assignment state of *v* after *expr* is the same as the definite assignment state of *v* after *expr_second*.</span></span>

### <a name="remarks"></a><span data-ttu-id="69cf7-168">Commenti</span><span class="sxs-lookup"><span data-stu-id="69cf7-168">Remarks</span></span>
<span data-ttu-id="69cf7-169">La regola precedente formalizza che per un'espressione come `a?.M(out x) ?? (x = false)` , `a?.M(out x)` è stato completamente valutato e ha prodotto un valore non null, nel qual caso `x` è stato assegnato oppure `x = false` è stato valutato, nel qual caso `x` è stato assegnato anche.</span><span class="sxs-lookup"><span data-stu-id="69cf7-169">The above rule formalizes that for an expression like `a?.M(out x) ?? (x = false)`, either the `a?.M(out x)` was fully evaluated and produced a non-null value, in which case `x` was assigned, or the `x = false` was evaluated, in which case `x` was also assigned.</span></span> <span data-ttu-id="69cf7-170">Pertanto `x` viene sempre assegnato dopo questa espressione.</span><span class="sxs-lookup"><span data-stu-id="69cf7-170">Therefore `x` is always assigned after this expression.</span></span>

<span data-ttu-id="69cf7-171">Questa operazione gestisce anche lo `dict?.TryGetValue(key, out var value) ?? false` scenario, osservando che la *versione di v* viene assegnata in modo definitivo dopo `dict.TryGetValue(key, out var value)` e *v* è "definitivamente assegnata quando true" dopo `false` e concludendo che *v* deve essere "assegnato definitivamente quando è true".</span><span class="sxs-lookup"><span data-stu-id="69cf7-171">This also handles the `dict?.TryGetValue(key, out var value) ?? false` scenario, by observing that *v* is definitely assigned after `dict.TryGetValue(key, out var value)`, and *v* is "definitely assigned when true" after `false`, and concluding that *v* must be "definitely assigned when true".</span></span>

<span data-ttu-id="69cf7-172">La formulazione più generale consente inoltre di gestire alcuni scenari più insoliti, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="69cf7-172">The more general formulation also allows us to handle some more unusual scenarios, such as:</span></span>
- `if (x?.M(out y) ?? (b && z.M(out y))) y.ToString();`
- `if (x?.M(out y) ?? z?.M(out y) ?? false) y.ToString();`

## <a name="-conditional-expressions"></a><span data-ttu-id="69cf7-173">espressioni?: (condizionale)</span><span class="sxs-lookup"><span data-stu-id="69cf7-173">?: (conditional) expressions</span></span>
<span data-ttu-id="69cf7-174">Sono state aggiunte le [**espressioni (condizionali)**](../spec/variables.md#-conditional-expressions) della sezione come segue:</span><span class="sxs-lookup"><span data-stu-id="69cf7-174">We augment the section [**?: (conditional) expressions**](../spec/variables.md#-conditional-expressions) as follows:</span></span>

<span data-ttu-id="69cf7-175">Per un'espressione *expr* nel formato `expr_cond ? expr_true : expr_false` :</span><span class="sxs-lookup"><span data-stu-id="69cf7-175">For an expression *expr* of the form `expr_cond ? expr_true : expr_false`:</span></span>
- <span data-ttu-id="69cf7-176">...</span><span class="sxs-lookup"><span data-stu-id="69cf7-176">...</span></span>
- <span data-ttu-id="69cf7-177">Lo stato di assegnazione definito di *v* dopo *expr* è determinato da:</span><span class="sxs-lookup"><span data-stu-id="69cf7-177">The definite assignment state of *v* after *expr* is determined by:</span></span>
  - <span data-ttu-id="69cf7-178">...</span><span class="sxs-lookup"><span data-stu-id="69cf7-178">...</span></span>
  - <span data-ttu-id="69cf7-179">Se lo stato di *v* dopo *expr_true* è "assegnato definitivamente quando è true" e lo stato di *v* dopo che *expr_false* è "assegnato definitivamente quando true", lo stato di *v* dopo *expr* è "definitely assegnata quando true".</span><span class="sxs-lookup"><span data-stu-id="69cf7-179">If the state of *v* after *expr_true* is "definitely assigned when true", and the state of *v* after *expr_false* is "definitely assigned when true", then the state of *v* after *expr* is "definitely assigned when true".</span></span>
  - <span data-ttu-id="69cf7-180">Se lo stato di *v* dopo *expr_true* è "assegnato definitivamente quando è false" e lo stato di *v* dopo che *expr_false* è "assegnato definitivamente quando false", lo stato di *v* dopo *expr* è "definitely unsigned when false".</span><span class="sxs-lookup"><span data-stu-id="69cf7-180">If the state of *v* after *expr_true* is "definitely assigned when false", and the state of *v* after *expr_false* is "definitely assigned when false", then the state of *v* after *expr* is "definitely assigned when false".</span></span>

### <a name="remarks"></a><span data-ttu-id="69cf7-181">Commenti</span><span class="sxs-lookup"><span data-stu-id="69cf7-181">Remarks</span></span>

<span data-ttu-id="69cf7-182">In questo modo, quando entrambi i bracci di un'espressione condizionale generano uno stato condizionale, si uniscono gli Stati condizionali corrispondenti e lo si propaga invece di separare lo stato e consentire lo stato finale come non condizionale.</span><span class="sxs-lookup"><span data-stu-id="69cf7-182">This makes it so when both arms of a conditional expression result in a conditional state, we join the corresponding conditional states and propagate it out instead of unsplitting the state and allowing the final state to be non-conditional.</span></span> <span data-ttu-id="69cf7-183">Questo consente scenari come i seguenti:</span><span class="sxs-lookup"><span data-stu-id="69cf7-183">This enables scenarios like the following:</span></span>

```cs
bool b = true;
object x = null;
int y;
if (b ? x != null && Set(out y) : x != null && Set(out y))
{
  y.ToString();
}

bool Set(out int x) { x = 0; return true; }
```

<span data-ttu-id="69cf7-184">Si tratta di uno scenario di nicchia che si compila senza errori nel compilatore nativo, ma è stato suddiviso in Roslyn per poter corrispondere alla specifica al momento.</span><span class="sxs-lookup"><span data-stu-id="69cf7-184">This is an admittedly niche scenario, that compiles without error in the native compiler, but was broken in Roslyn in order to match the specification at the time.</span></span> <span data-ttu-id="69cf7-185">Vedere il [problema interno](http://vstfdevdiv:8080/DevDiv2/DevDiv/_workitems/edit/529603).</span><span class="sxs-lookup"><span data-stu-id="69cf7-185">See [internal issue](http://vstfdevdiv:8080/DevDiv2/DevDiv/_workitems/edit/529603).</span></span>

## <a name="-relational-equality-operator-expressions"></a><span data-ttu-id="69cf7-186">espressioni = =/! = (operatore di uguaglianza relazionale)</span><span class="sxs-lookup"><span data-stu-id="69cf7-186">==/!= (relational equality operator) expressions</span></span>
<span data-ttu-id="69cf7-187">Vengono introdotte una nuova **espressione Section = =/! = (operatore di uguaglianza relazionale)**.</span><span class="sxs-lookup"><span data-stu-id="69cf7-187">We introduce a new section **==/!= (relational equality operator) expressions**.</span></span>

<span data-ttu-id="69cf7-188">Si applicano le [regole generali per le espressioni con espressioni incorporate](../spec/variables.md#general-rules-for-expressions-with-embedded-expressions) , ad eccezione degli scenari descritti di seguito.</span><span class="sxs-lookup"><span data-stu-id="69cf7-188">The [general rules for expressions with embedded expressions](../spec/variables.md#general-rules-for-expressions-with-embedded-expressions) apply, except for the scenarios described below.</span></span>

<span data-ttu-id="69cf7-189">Per un'espressione *expr* del form `expr_first == expr_second` , dove `==` è un [operatore di confronto predefinito](../spec/expressions.md#relational-and-type-testing-operators) o un [operatore lifted](../spec/expressions.md#lifted-operators), lo stato di assegnazione definito di *v* dopo *expr* è determinato da:</span><span class="sxs-lookup"><span data-stu-id="69cf7-189">For an expression *expr* of the form `expr_first == expr_second`, where `==` is a [predefined comparison operator](../spec/expressions.md#relational-and-type-testing-operators) or a [lifted operator](../spec/expressions.md#lifted-operators), the definite assignment state of *v* after *expr* is determined by:</span></span>
  - <span data-ttu-id="69cf7-190">Se *expr_first* contiene direttamente un'espressione condizionale null *e* e *expr_second* è un'espressione costante con valore *null* e lo stato *v* dopo la controparte non condizionale e *<sub>0</sub>* è "assegnato definitivamente", lo stato di *v* dopo *expr* è "definitivamente assegnato quando false".</span><span class="sxs-lookup"><span data-stu-id="69cf7-190">If *expr_first* directly contains a null-conditional expression *E* and *expr_second* is a constant expression with value *null*, and the state of *v* after the non-conditional counterpart *E <sub>0</sub>* is "definitely assigned", then the state of *v* after *expr* is "definitely assigned when false".</span></span>
  - <span data-ttu-id="69cf7-191">Se *expr_first* contiene direttamente un'espressione condizionale null *e* e *expr_second* è un'espressione di un tipo di valore non nullable oppure un'espressione costante con un valore non null e lo stato di *v* dopo la controparte non condizionale e *<sub>0</sub>* è "assegnato definitivamente", lo stato di *v* dopo *expr* è "definitely Assigned when true".</span><span class="sxs-lookup"><span data-stu-id="69cf7-191">If *expr_first* directly contains a null-conditional expression *E* and *expr_second* is an expression of a non-nullable value type, or a constant expression with a non-null value, and the state of *v* after the non-conditional counterpart *E <sub>0</sub>* is "definitely assigned", then the state of *v* after *expr* is "definitely assigned when true".</span></span>
  - <span data-ttu-id="69cf7-192">Se *expr_first* è di tipo *booleano* e *expr_second* è un'espressione costante con valore *true*, lo stato di assegnazione definito dopo *expr* corrisponde allo stato di assegnazione definito dopo *expr_first*.</span><span class="sxs-lookup"><span data-stu-id="69cf7-192">If *expr_first* is of type *boolean*, and *expr_second* is a constant expression with value *true*, then the definite assignment state after *expr* is the same as the definite assignment state after *expr_first*.</span></span>
  - <span data-ttu-id="69cf7-193">Se *expr_first* è di tipo *booleano* e *expr_second* è un'espressione costante con valore *false*, lo stato di assegnazione definito dopo *expr* corrisponde allo stato di assegnazione definito di *v* dopo l'espressione di negazione logica `!expr_first` .</span><span class="sxs-lookup"><span data-stu-id="69cf7-193">If *expr_first* is of type *boolean*, and *expr_second* is a constant expression with value *false*, then the definite assignment state after *expr* is the same as the definite assignment state of *v* after the logical negation expression `!expr_first`.</span></span>

<span data-ttu-id="69cf7-194">Per un'espressione *expr* del form `expr_first != expr_second` , dove `!=` è un [operatore di confronto predefinito](../spec/expressions.md#relational-and-type-testing-operators) o un [operatore lifted](../spec/expressions.md#lifted-operators), lo stato di assegnazione definito di *v* dopo *expr* è determinato da:</span><span class="sxs-lookup"><span data-stu-id="69cf7-194">For an expression *expr* of the form `expr_first != expr_second`, where `!=` is a [predefined comparison operator](../spec/expressions.md#relational-and-type-testing-operators) or a [lifted operator](../spec/expressions.md#lifted-operators), the definite assignment state of *v* after *expr* is determined by:</span></span>
  - <span data-ttu-id="69cf7-195">Se *expr_first* contiene direttamente un'espressione condizionale null *e* e *expr_second* è un'espressione costante con valore *null* e lo stato *v* dopo la controparte non condizionale e *<sub>0</sub>* è "assegnato definitivamente", lo stato di *v* dopo *expr* è "definitely Assigned when true".</span><span class="sxs-lookup"><span data-stu-id="69cf7-195">If *expr_first* directly contains a null-conditional expression *E* and *expr_second* is a constant expression with value *null*, and the state of *v* after the non-conditional counterpart *E <sub>0</sub>* is "definitely assigned", then the state of *v* after *expr* is "definitely assigned when true".</span></span>
  - <span data-ttu-id="69cf7-196">Se *expr_first* contiene direttamente un'espressione condizionale null *e* e *expr_second* è un'espressione di un tipo di valore non nullable oppure un'espressione costante con un valore non null e lo stato di *v* dopo la controparte non condizionale e *<sub>0</sub>* è "assegnato definitivamente", lo stato di *v* dopo *expr* è "definitely unsigned when false".</span><span class="sxs-lookup"><span data-stu-id="69cf7-196">If *expr_first* directly contains a null-conditional expression *E* and *expr_second* is an expression of a non-nullable value type, or a constant expression with a non-null value, and the state of *v* after the non-conditional counterpart *E <sub>0</sub>* is "definitely assigned", then the state of *v* after *expr* is "definitely assigned when false".</span></span>
  - <span data-ttu-id="69cf7-197">Se *expr_first* è di tipo *booleano* e *expr_second* è un'espressione costante con valore *true*, lo stato di assegnazione definito dopo *expr* corrisponde allo stato di assegnazione definito di *v* dopo l'espressione di negazione logica `!expr_first` .</span><span class="sxs-lookup"><span data-stu-id="69cf7-197">If *expr_first* is of type *boolean*, and *expr_second* is a constant expression with value *true*, then the definite assignment state after *expr* is the same as the definite assignment state of *v* after the logical negation expression `!expr_first`.</span></span>
  - <span data-ttu-id="69cf7-198">Se *expr_first* è di tipo *booleano* e *expr_second* è un'espressione costante con valore *false*, lo stato di assegnazione definito dopo *expr* corrisponde allo stato di assegnazione definito dopo *expr_first*.</span><span class="sxs-lookup"><span data-stu-id="69cf7-198">If *expr_first* is of type *boolean*, and *expr_second* is a constant expression with value *false*, then the definite assignment state after *expr* is the same as the definite assignment state after *expr_first*.</span></span>

<span data-ttu-id="69cf7-199">Tutte le regole descritte in questa sezione sono commutative, vale a dire che se una regola viene applicata quando viene valutata nel modulo `expr_second op expr_first` , viene applicata anche nel formato `expr_first op expr_second` .</span><span class="sxs-lookup"><span data-stu-id="69cf7-199">All of the above rules in this section are commutative, meaning that if a rule applies when evaluated in the form `expr_second op expr_first`, it also applies in the form `expr_first op expr_second`.</span></span>

### <a name="remarks"></a><span data-ttu-id="69cf7-200">Commenti</span><span class="sxs-lookup"><span data-stu-id="69cf7-200">Remarks</span></span>
<span data-ttu-id="69cf7-201">L'idea generale espressa da queste regole è la seguente:</span><span class="sxs-lookup"><span data-stu-id="69cf7-201">The general idea expressed by these rules is:</span></span>
- <span data-ttu-id="69cf7-202">Se un accesso condizionale viene confrontato con `null` , si sa che le operazioni si sono verificate definitivamente se il risultato del confronto è `false`</span><span class="sxs-lookup"><span data-stu-id="69cf7-202">if a conditional access is compared to `null`, then we know the operations definitely occurred if the result of the comparison is `false`</span></span>
- <span data-ttu-id="69cf7-203">Se un accesso condizionale viene confrontato con un tipo di valore non nullable o una costante non null, si sa che le operazioni si sono verificate definitivamente se il risultato del confronto è `true` .</span><span class="sxs-lookup"><span data-stu-id="69cf7-203">if a conditional access is compared to a non-nullable value type or a non-null constant, then we know the operations definitely occurred if the result of the comparison is `true`.</span></span>
- <span data-ttu-id="69cf7-204">Poiché non è possibile considerare attendibili gli operatori definiti dall'utente per fornire risposte affidabili in cui è interessata la sicurezza dell'inizializzazione, le nuove regole si applicano solo quando un `==` / `!=` operatore predefinito è in uso.</span><span class="sxs-lookup"><span data-stu-id="69cf7-204">since we can't trust user-defined operators to provide reliable answers where initialization safety is concerned, the new rules only apply when a predefined `==`/`!=` operator is in use.</span></span>

<span data-ttu-id="69cf7-205">È possibile che si desideri perfezionare queste regole in thread tramite lo stato condizionale presente alla fine di un accesso o di una chiamata a un membro.</span><span class="sxs-lookup"><span data-stu-id="69cf7-205">We may eventually want to refine these rules to thread through conditional state which is present at the end of a member access or call.</span></span> <span data-ttu-id="69cf7-206">Questi scenari non si verificano realmente nell'assegnazione definitiva, ma si verificano in modo nullable in presenza di `[NotNullWhen(true)]` e attributi simili.</span><span class="sxs-lookup"><span data-stu-id="69cf7-206">Such scenarios don't really happen in definite assignment, but they do happen in nullable in the presence of `[NotNullWhen(true)]` and similar attributes.</span></span> <span data-ttu-id="69cf7-207">Questa operazione richiede una gestione speciale per le `bool` costanti oltre alla semplice gestione delle `null` costanti/non-null.</span><span class="sxs-lookup"><span data-stu-id="69cf7-207">This would require special handling for `bool` constants in addition to just handling for `null`/non-null constants.</span></span>

<span data-ttu-id="69cf7-208">Di seguito sono riportate alcune conseguenze di queste regole:</span><span class="sxs-lookup"><span data-stu-id="69cf7-208">Some consequences of these rules:</span></span>
- <span data-ttu-id="69cf7-209">`if (a?.b(out var x) == true)) x() else x();` errore nel ramo ' else '</span><span class="sxs-lookup"><span data-stu-id="69cf7-209">`if (a?.b(out var x) == true)) x() else x();` will error in the 'else' branch</span></span>
- <span data-ttu-id="69cf7-210">`if (a?.b(out var x) == 42)) x() else x();` errore nel ramo ' else '</span><span class="sxs-lookup"><span data-stu-id="69cf7-210">`if (a?.b(out var x) == 42)) x() else x();` will error in the 'else' branch</span></span>
- <span data-ttu-id="69cf7-211">`if (a?.b(out var x) == false)) x() else x();` errore nel ramo ' else '</span><span class="sxs-lookup"><span data-stu-id="69cf7-211">`if (a?.b(out var x) == false)) x() else x();` will error in the 'else' branch</span></span>
- <span data-ttu-id="69cf7-212">`if (a?.b(out var x) == null)) x() else x();` si errore nel ramo ' then '</span><span class="sxs-lookup"><span data-stu-id="69cf7-212">`if (a?.b(out var x) == null)) x() else x();` will error in the 'then' branch</span></span>
- <span data-ttu-id="69cf7-213">`if (a?.b(out var x) != true)) x() else x();` si errore nel ramo ' then '</span><span class="sxs-lookup"><span data-stu-id="69cf7-213">`if (a?.b(out var x) != true)) x() else x();` will error in the 'then' branch</span></span>
- <span data-ttu-id="69cf7-214">`if (a?.b(out var x) != 42)) x() else x();` si errore nel ramo ' then '</span><span class="sxs-lookup"><span data-stu-id="69cf7-214">`if (a?.b(out var x) != 42)) x() else x();` will error in the 'then' branch</span></span>
- <span data-ttu-id="69cf7-215">`if (a?.b(out var x) != false)) x() else x();` si errore nel ramo ' then '</span><span class="sxs-lookup"><span data-stu-id="69cf7-215">`if (a?.b(out var x) != false)) x() else x();` will error in the 'then' branch</span></span>
- <span data-ttu-id="69cf7-216">`if (a?.b(out var x) != null)) x() else x();` errore nel ramo ' else '</span><span class="sxs-lookup"><span data-stu-id="69cf7-216">`if (a?.b(out var x) != null)) x() else x();` will error in the 'else' branch</span></span>

## <a name="is-operator-and-is-pattern-expressions"></a><span data-ttu-id="69cf7-217">`is` espressioni di operatore e `is` modello</span><span class="sxs-lookup"><span data-stu-id="69cf7-217">`is` operator and `is` pattern expressions</span></span>
<span data-ttu-id="69cf7-218">Vengono introdotte una nuova sezione **`is` operatore e `is` espressioni di criteri**.</span><span class="sxs-lookup"><span data-stu-id="69cf7-218">We introduce a new section **`is` operator and `is` pattern expressions**.</span></span>

<span data-ttu-id="69cf7-219">Per un'espressione *expr* del form `E is T` , dove *T* è qualsiasi tipo o modello</span><span class="sxs-lookup"><span data-stu-id="69cf7-219">For an expression *expr* of the form `E is T`, where *T* is any type or pattern</span></span>
- <span data-ttu-id="69cf7-220">Lo stato di assegnazione definito di *v* prima di *E* è lo stesso dello stato di assegnazione definito di *v* prima di *expr*.</span><span class="sxs-lookup"><span data-stu-id="69cf7-220">The definite assignment state of *v* before *E* is the same as the definite assignment state of *v* before *expr*.</span></span>
- <span data-ttu-id="69cf7-221">Lo stato di assegnazione definito di *v* dopo *expr* è determinato da:</span><span class="sxs-lookup"><span data-stu-id="69cf7-221">The definite assignment state of *v* after *expr* is determined by:</span></span>
  - <span data-ttu-id="69cf7-222">Se *e* contiene direttamente un'espressione condizionale null e lo stato di *v* dopo la controparte non condizionale *e <sub>0</sub>* è "assegnato definitivamente" ed `T` è di qualsiasi tipo o di un modello che corrisponde solo a un input non null, lo stato di *v* dopo *expr* è "definitivamente assegnato se true".</span><span class="sxs-lookup"><span data-stu-id="69cf7-222">If *E* directly contains a null-conditional expression, and the state of *v* after the non-conditional counterpart *E <sub>0</sub>* is "definitely assigned", and `T` is any type or a pattern that only matches a non-null input, then the state of *v* after *expr* is "definitely assigned when true".</span></span>
  - <span data-ttu-id="69cf7-223">Se *e* contiene direttamente un'espressione condizionale null e lo stato *v* dopo la controparte non condizionale *e <sub>0</sub>* è "assegnato definitivamente" ed `T` è un modello che corrisponde solo a un input null, lo stato di *v* dopo *expr* è "definitely unsigned when false".</span><span class="sxs-lookup"><span data-stu-id="69cf7-223">If *E* directly contains a null-conditional expression, and the state of *v* after the non-conditional counterpart *E <sub>0</sub>* is "definitely assigned", and `T` is a pattern which only matches a null input, then the state of *v* after *expr* is "definitely assigned when false".</span></span>
  - <span data-ttu-id="69cf7-224">Se *e* è di tipo booleano ed `T` è il criterio costante `true` , lo stato di assegnazione definito di *v* dopo *expr* corrisponde allo stato di assegnazione definito di *v* dopo E.</span><span class="sxs-lookup"><span data-stu-id="69cf7-224">If *E* is of type boolean and `T` is the constant pattern `true`, then the definite assignment state of *v* after *expr* is the same as the definite assignment state of *v* after E.</span></span>
  - <span data-ttu-id="69cf7-225">Se *e* è di tipo booleano ed `T` è il criterio costante `false` , lo stato di assegnazione definito di *v* dopo *expr* corrisponde allo stato di assegnazione definito di *v* dopo l'espressione di negazione logica `!expr` .</span><span class="sxs-lookup"><span data-stu-id="69cf7-225">If *E* is of type boolean and `T` is the constant pattern `false`, then the definite assignment state of *v* after *expr* is the same as the definite assignment state of *v* after the logical negation expression `!expr`.</span></span>
  - <span data-ttu-id="69cf7-226">In caso contrario, se lo stato di assegnazione definito di *v* dopo E è "definitivamente assegnato", lo stato di assegnazione definito di *v* dopo *expr* sarà "assegnato definitivamente".</span><span class="sxs-lookup"><span data-stu-id="69cf7-226">Otherwise, if the definite assignment state of *v* after E is "definitely assigned", then the definite assignment state of *v* after *expr* is "definitely assigned".</span></span>

### <a name="remarks"></a><span data-ttu-id="69cf7-227">Commenti</span><span class="sxs-lookup"><span data-stu-id="69cf7-227">Remarks</span></span>

<span data-ttu-id="69cf7-228">Questa sezione è destinata a scenari simili, come nella `==` / `!=` sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="69cf7-228">This section is meant to address similar scenarios as in the `==`/`!=` section above.</span></span>
<span data-ttu-id="69cf7-229">Questa specifica non risolve i modelli ricorsivi, ad esempio `(a?.b(out x), c?.d(out y)) is (object, object)` .</span><span class="sxs-lookup"><span data-stu-id="69cf7-229">This specification does not address recursive patterns, e.g. `(a?.b(out x), c?.d(out y)) is (object, object)`.</span></span> <span data-ttu-id="69cf7-230">Questo supporto può rientrare in un secondo momento, se consentito.</span><span class="sxs-lookup"><span data-stu-id="69cf7-230">Such support may come later if time permits.</span></span>

# <a name="additional-scenarios"></a><span data-ttu-id="69cf7-231">Scenari aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="69cf7-231">Additional scenarios</span></span>

<span data-ttu-id="69cf7-232">Questa specifica non è attualmente indirizzata a scenari che coinvolgono espressioni di cambio di modello e istruzioni switch.</span><span class="sxs-lookup"><span data-stu-id="69cf7-232">This specification doesn't currently address scenarios involving pattern switch expressions and switch statements.</span></span> <span data-ttu-id="69cf7-233">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="69cf7-233">For example:</span></span>

```cs
_ = c?.M(out object obj4) switch
{
    not null => obj4.ToString() // undesired error
};
```

<span data-ttu-id="69cf7-234">Sembra che il supporto per questa operazione potrebbe rientrare in un secondo momento, se consentito.</span><span class="sxs-lookup"><span data-stu-id="69cf7-234">It seems like support for this could come later if time permits.</span></span>

<span data-ttu-id="69cf7-235">Sono state rilevate diverse categorie di bug per i valori null, che richiedono essenzialmente una maggiore complessità dell'analisi dei modelli.</span><span class="sxs-lookup"><span data-stu-id="69cf7-235">There have been several categories of bugs filed for nullable which require we essentially increase the sophistication of pattern analysis.</span></span> <span data-ttu-id="69cf7-236">È probabile che qualsiasi decisione che aumenti l'assegnazione definitiva venga trasferita anche a Nullable.</span><span class="sxs-lookup"><span data-stu-id="69cf7-236">It is likely that any ruling we make which improves definite assignment would also be carried over to nullable.</span></span>

https://github.com/dotnet/roslyn/issues/49353  
https://github.com/dotnet/roslyn/issues/46819  
https://github.com/dotnet/roslyn/issues/44127

## <a name="drawbacks"></a><span data-ttu-id="69cf7-237">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="69cf7-237">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="69cf7-238">Sembra strano che l'analisi sia "raggiungibile" e abbia un riconoscimento speciale degli accessi condizionali, quando lo stato di analisi del flusso dovrebbe essere propagato in alto.</span><span class="sxs-lookup"><span data-stu-id="69cf7-238">It feels odd to have the analysis "reach down" and have special recognition of conditional accesses, when typically flow analysis state is supposed to propagate upward.</span></span> <span data-ttu-id="69cf7-239">Ci occupiamo di come una soluzione come questa possa intersecarsi in modo doloroso con possibili funzionalità del linguaggio future che eseguono controlli null.</span><span class="sxs-lookup"><span data-stu-id="69cf7-239">We are concerned about how a solution like this could intersect painfully with possible future language features that do null checks.</span></span>

## <a name="alternatives"></a><span data-ttu-id="69cf7-240">Alternativi</span><span class="sxs-lookup"><span data-stu-id="69cf7-240">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="69cf7-241">Due alternative a questa proposta:</span><span class="sxs-lookup"><span data-stu-id="69cf7-241">Two alternatives to this proposal:</span></span>
1. <span data-ttu-id="69cf7-242">Introdurre "state when null" e "state when not null" per il linguaggio e il compilatore.</span><span class="sxs-lookup"><span data-stu-id="69cf7-242">Introduce "state when null" and "state when not null" to the language and compiler.</span></span> <span data-ttu-id="69cf7-243">Questa operazione è stata giudicata come un lavoro troppo impegnativo per gli scenari che si sta tentando di risolvere, ma è possibile implementare la proposta precedente e quindi passare a un modello "state when null/not null" in un secondo momento senza causare interruzioni.</span><span class="sxs-lookup"><span data-stu-id="69cf7-243">This has been judged to be too much effort for the scenarios we are trying to solve, but that we could potentially implement the above proposal and then move to a "state when null/not null" model later on without breaking people.</span></span>
2. <span data-ttu-id="69cf7-244">Non eseguire alcuna operazione.</span><span class="sxs-lookup"><span data-stu-id="69cf7-244">Do nothing.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="69cf7-245">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="69cf7-245">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="69cf7-246">Ci sono effetti sulle espressioni switch che è necessario specificare: https://github.com/dotnet/csharplang/discussions/4240#discussioncomment-343395</span><span class="sxs-lookup"><span data-stu-id="69cf7-246">There are impacts on switch expressions that should be specified: https://github.com/dotnet/csharplang/discussions/4240#discussioncomment-343395</span></span>

## <a name="design-meetings"></a><span data-ttu-id="69cf7-247">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="69cf7-247">Design meetings</span></span>

https://github.com/dotnet/csharplang/discussions/4243
