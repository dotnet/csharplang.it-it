---
ms.openlocfilehash: 1852356b830e29c3537a4de559fef32fd2c2f8b6
ms.sourcegitcommit: f7952cdddf85316a4beec493a0ecc14fcb3241c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "79484986"
---
# <a name="target-typed-default-literal"></a><span data-ttu-id="e5397-101">Valore letterale "default" tipizzato come destinazione</span><span class="sxs-lookup"><span data-stu-id="e5397-101">Target-typed "default" literal</span></span>

* <span data-ttu-id="e5397-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="e5397-102">[x] Proposed</span></span>
* <span data-ttu-id="e5397-103">Prototipo [x]</span><span class="sxs-lookup"><span data-stu-id="e5397-103">[x] Prototype</span></span>
* <span data-ttu-id="e5397-104">Implementazione di [x]</span><span class="sxs-lookup"><span data-stu-id="e5397-104">[x] Implementation</span></span>
* <span data-ttu-id="e5397-105">[] Specifica</span><span class="sxs-lookup"><span data-stu-id="e5397-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="e5397-106">Summary</span><span class="sxs-lookup"><span data-stu-id="e5397-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="e5397-107">La funzionalità `default` tipizzata come destinazione è una variante più breve dell'operatore `default(T)`, che consente di omettere il tipo.</span><span class="sxs-lookup"><span data-stu-id="e5397-107">The target-typed `default` feature is a shorter form variation of the `default(T)` operator, which allows the type to be omitted.</span></span> <span data-ttu-id="e5397-108">Il tipo viene dedotto da target-typing.</span><span class="sxs-lookup"><span data-stu-id="e5397-108">Its type is inferred by target-typing instead.</span></span> <span data-ttu-id="e5397-109">A parte questo, si comporta come `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="e5397-109">Aside from that, it behaves like `default(T)`.</span></span>

## <a name="motivation"></a><span data-ttu-id="e5397-110">Motivazione</span><span class="sxs-lookup"><span data-stu-id="e5397-110">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="e5397-111">La motivazione principale è evitare di digitare informazioni ridondanti.</span><span class="sxs-lookup"><span data-stu-id="e5397-111">The main motivation is to avoid typing redundant information.</span></span>

<span data-ttu-id="e5397-112">Ad esempio, quando si richiama `void Method(ImmutableArray<SomeType> array)`, il valore letterale *predefinito* consente `M(default)` al posto di `M(default(ImmutableArray<SomeType>))`.</span><span class="sxs-lookup"><span data-stu-id="e5397-112">For instance, when invoking `void Method(ImmutableArray<SomeType> array)`, the *default* literal allows `M(default)` in place of `M(default(ImmutableArray<SomeType>))`.</span></span>

<span data-ttu-id="e5397-113">Questa operazione è applicabile in diversi scenari, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e5397-113">This is applicable in a number of scenarios, such as:</span></span>

- <span data-ttu-id="e5397-114">Dichiarazione di variabili locali (`ImmutableArray<SomeType> x = default;`)</span><span class="sxs-lookup"><span data-stu-id="e5397-114">declaring locals (`ImmutableArray<SomeType> x = default;`)</span></span>
- <span data-ttu-id="e5397-115">operazioni ternarie (`var x = flag ? default : ImmutableArray<SomeType>.Empty;`)</span><span class="sxs-lookup"><span data-stu-id="e5397-115">ternary operations (`var x = flag ? default : ImmutableArray<SomeType>.Empty;`)</span></span>
- <span data-ttu-id="e5397-116">restituzione di metodi e espressioni lambda (`return default;`)</span><span class="sxs-lookup"><span data-stu-id="e5397-116">returning in methods and lambdas (`return default;`)</span></span>
- <span data-ttu-id="e5397-117">Dichiarazione dei valori predefiniti per i parametri facoltativi (`void Method(ImmutableArray<SomeType> arrayOpt = default)`)</span><span class="sxs-lookup"><span data-stu-id="e5397-117">declaring default values for optional parameters (`void Method(ImmutableArray<SomeType> arrayOpt = default)`)</span></span>
- <span data-ttu-id="e5397-118">inclusione di valori predefiniti nelle espressioni di creazione di matrici (`var x = new[] { default, ImmutableArray.Create(y) };`)</span><span class="sxs-lookup"><span data-stu-id="e5397-118">including default values in array creation expressions (`var x = new[] { default, ImmutableArray.Create(y) };`)</span></span>


## <a name="detailed-design"></a><span data-ttu-id="e5397-119">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="e5397-119">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="e5397-120">Viene introdotta una nuova espressione, il valore letterale *predefinito* .</span><span class="sxs-lookup"><span data-stu-id="e5397-120">A new expression is introduced, the *default* literal.</span></span> <span data-ttu-id="e5397-121">Un'espressione con questa classificazione può essere convertita in modo implicito in qualsiasi tipo, mediante una *conversione di valore letterale predefinito*.</span><span class="sxs-lookup"><span data-stu-id="e5397-121">An expression with this classification can be implicitly converted to any type, by a *default literal conversion*.</span></span> 

<span data-ttu-id="e5397-122">L'inferenza del tipo per il valore letterale *predefinito* funziona come per il valore letterale *null* , con la differenza che è consentito qualsiasi tipo (non solo i tipi di riferimento).</span><span class="sxs-lookup"><span data-stu-id="e5397-122">The inference of the type for the *default* literal works the same as that for the *null* literal, except that any type is allowed (not just reference types).</span></span>

<span data-ttu-id="e5397-123">Questa conversione genera il valore predefinito del tipo dedotto.</span><span class="sxs-lookup"><span data-stu-id="e5397-123">This conversion produces the default value of the inferred type.</span></span>

<span data-ttu-id="e5397-124">Il valore letterale *predefinito* può avere un valore costante, a seconda del tipo derivato.</span><span class="sxs-lookup"><span data-stu-id="e5397-124">The *default* literal may have a constant value, depending on the inferred type.</span></span> <span data-ttu-id="e5397-125">`const int x = default;` è quindi valido, ma `const int? y = default;` non lo è.</span><span class="sxs-lookup"><span data-stu-id="e5397-125">So `const int x = default;` is legal, but `const int? y = default;` is not.</span></span>

<span data-ttu-id="e5397-126">Il valore letterale *predefinito* può essere l'operando degli operatori di uguaglianza, purché l'altro operando abbia un tipo.</span><span class="sxs-lookup"><span data-stu-id="e5397-126">The *default* literal can be the operand of equality operators, as long as the other operand has a type.</span></span> <span data-ttu-id="e5397-127">Quindi `default == x` e `x == default` sono espressioni valide, ma `default == default` non è valido.</span><span class="sxs-lookup"><span data-stu-id="e5397-127">So `default == x` and `x == default` are valid expressions, but `default == default` is illegal.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="e5397-128">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="e5397-128">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="e5397-129">Uno svantaggio minore è che il valore letterale *predefinito* può essere utilizzato al posto di un valore letterale *null* nella maggior parte dei contesti.</span><span class="sxs-lookup"><span data-stu-id="e5397-129">A minor drawback is that *default* literal can be used in place of *null* literal in most contexts.</span></span> <span data-ttu-id="e5397-130">Due eccezioni sono `throw null;` e `null == null`, che sono consentite per il valore letterale *null* , ma non per il valore letterale *predefinito* .</span><span class="sxs-lookup"><span data-stu-id="e5397-130">Two of the exceptions are `throw null;` and `null == null`, which are allowed for the *null* literal, but not the *default* literal.</span></span>

## <a name="alternatives"></a><span data-ttu-id="e5397-131">Alternativi</span><span class="sxs-lookup"><span data-stu-id="e5397-131">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="e5397-132">Ci sono due alternative da considerare:</span><span class="sxs-lookup"><span data-stu-id="e5397-132">There are a couple of alternatives to consider:</span></span>

- <span data-ttu-id="e5397-133">Status quo: la funzionalità non è giustificata per i propri meriti e gli sviluppatori continuano a usare l'operatore predefinito con un tipo esplicito.</span><span class="sxs-lookup"><span data-stu-id="e5397-133">The status quo:  The feature is not justified on its own merits and developers continue to use the default operator with an explicit type.</span></span>
- <span data-ttu-id="e5397-134">Estensione del valore letterale null: si tratta dell'approccio VB con `Nothing`.</span><span class="sxs-lookup"><span data-stu-id="e5397-134">Extending the null literal: This is the VB approach with `Nothing`.</span></span> <span data-ttu-id="e5397-135">Potremmo consentire `int x = null;`.</span><span class="sxs-lookup"><span data-stu-id="e5397-135">We could allow `int x = null;`.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="e5397-136">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="e5397-136">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="e5397-137">[x] deve essere consentito per *impostazione predefinita* come operando di *è* o *come* operatori?</span><span class="sxs-lookup"><span data-stu-id="e5397-137">[x] Should *default* be allowed as the operand of the *is* or *as* operators?</span></span> <span data-ttu-id="e5397-138">Risposta: non consentire `default is T`, consentire `x is default`, consentire `default as RefType` (con avviso always-null)</span><span class="sxs-lookup"><span data-stu-id="e5397-138">Answer:  disallow `default is T`, allow `x is default`, allow `default as RefType` (with always-null warning)</span></span>

## <a name="design-meetings"></a><span data-ttu-id="e5397-139">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="e5397-139">Design meetings</span></span>

- [<span data-ttu-id="e5397-140">LDM 3/7/2017</span><span class="sxs-lookup"><span data-stu-id="e5397-140">LDM 3/7/2017</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-07.md)
- [<span data-ttu-id="e5397-141">LDM 3/28/2017</span><span class="sxs-lookup"><span data-stu-id="e5397-141">LDM 3/28/2017</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-28.md)
- [<span data-ttu-id="e5397-142">LDM 5/31/2017</span><span class="sxs-lookup"><span data-stu-id="e5397-142">LDM 5/31/2017</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-31.md#default-in-operators)
