---
ms.openlocfilehash: 3d10cacef98e802333c8cbe65edb93c19c74cabf
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484496"
---
# <a name="null-conditional-await"></a><span data-ttu-id="991e9-101">null-await condizionale</span><span class="sxs-lookup"><span data-stu-id="991e9-101">null-conditional await</span></span>

* <span data-ttu-id="991e9-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="991e9-102">[x] Proposed</span></span>
* <span data-ttu-id="991e9-103">[] Prototipo: nessuno</span><span class="sxs-lookup"><span data-stu-id="991e9-103">[ ] Prototype: None</span></span>
* <span data-ttu-id="991e9-104">[] Implementazione: nessuna</span><span class="sxs-lookup"><span data-stu-id="991e9-104">[ ] Implementation: None</span></span>
* <span data-ttu-id="991e9-105">[] Specifica: avviata, di seguito</span><span class="sxs-lookup"><span data-stu-id="991e9-105">[ ] Specification: Started, below</span></span>

## <a name="summary"></a><span data-ttu-id="991e9-106">Summary</span><span class="sxs-lookup"><span data-stu-id="991e9-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="991e9-107">Supporta un'espressione nel formato `await? e`, che attende `e` se è non null. in caso contrario, restituisce `null`.</span><span class="sxs-lookup"><span data-stu-id="991e9-107">Support an expression of the form `await? e`, which awaits `e` if it is non-null, otherwise it results in `null`.</span></span>

## <a name="motivation"></a><span data-ttu-id="991e9-108">Motivazione</span><span class="sxs-lookup"><span data-stu-id="991e9-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="991e9-109">Si tratta di un modello di codifica comune e questa funzionalità avrebbe una buona sinergia con gli operatori esistenti di propagazione di valori null e con Unione null.</span><span class="sxs-lookup"><span data-stu-id="991e9-109">This is a common coding pattern, and this feature would have nice synergy with the existing null-propagating and null-coalescing operators.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="991e9-110">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="991e9-110">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="991e9-111">Viene aggiunto un nuovo modulo del *await_expression*:</span><span class="sxs-lookup"><span data-stu-id="991e9-111">We add a new form of the *await_expression*:</span></span>

```antlr
await_expression
    : 'await' '?' unary_expression
    ;
```

<span data-ttu-id="991e9-112">L'operatore `await` condizionale null attende il proprio operando solo se l'operando è diverso da null.</span><span class="sxs-lookup"><span data-stu-id="991e9-112">The null-conditional `await` operator awaits its operand only if that operand is non-null.</span></span> <span data-ttu-id="991e9-113">In caso contrario, il risultato dell'applicazione dell'operatore è null.</span><span class="sxs-lookup"><span data-stu-id="991e9-113">Otherwise the result of applying the operator is null.</span></span>

<span data-ttu-id="991e9-114">Il tipo del risultato viene calcolato utilizzando le [regole per l'operatore condizionale null](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#null-conditional-operator).</span><span class="sxs-lookup"><span data-stu-id="991e9-114">The type of the result is computed using the [rules for the null-conditional operator](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#null-conditional-operator).</span></span>

> <span data-ttu-id="991e9-115">**Nota:** Se `e` è di tipo `Task`, `await? e;` non esegue alcuna operazione se `e` è `null`e await `e` se non è `null`.</span><span class="sxs-lookup"><span data-stu-id="991e9-115">**NOTE:** If `e` is of type `Task`, then `await? e;` would do nothing if `e` is `null`, and await `e` if it is not `null`.</span></span>
>
> <span data-ttu-id="991e9-116">Se `e` è di tipo `Task<K>` dove `K` è un tipo di valore, `await? e` produrrebbe un valore di tipo `K?`.</span><span class="sxs-lookup"><span data-stu-id="991e9-116">If `e` is of type `Task<K>` where `K` is a value type, then `await? e` would yield a value of type `K?`.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="991e9-117">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="991e9-117">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="991e9-118">Come per qualsiasi funzionalità del linguaggio, è necessario stabilire se la complessità aggiuntiva al linguaggio venga ripagata in base a una maggiore chiarezza offerta C# al corpo dei programmi che trarrebbero vantaggio dalla funzionalità.</span><span class="sxs-lookup"><span data-stu-id="991e9-118">As with any language feature, we must question whether the additional complexity to the language is repaid in the additional clarity offered to the body of C# programs that would benefit from the feature.</span></span>

## <a name="alternatives"></a><span data-ttu-id="991e9-119">Alternativi</span><span class="sxs-lookup"><span data-stu-id="991e9-119">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="991e9-120">Sebbene richieda codice standard, gli utilizzi di questo operatore possono spesso essere sostituiti da un'espressione simile `(e == null) ? null : await e` o un'istruzione come `if (e != null) await e`.</span><span class="sxs-lookup"><span data-stu-id="991e9-120">Although it requires some boilerplate code, uses of this operator can often be replaced by an expression something like `(e == null) ? null : await e` or a statement like `if (e != null) await e`.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="991e9-121">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="991e9-121">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="991e9-122">[] Richiede la revisione di LDM</span><span class="sxs-lookup"><span data-stu-id="991e9-122">[ ] Requires LDM review</span></span>

## <a name="design-meetings"></a><span data-ttu-id="991e9-123">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="991e9-123">Design meetings</span></span>

<span data-ttu-id="991e9-124">No.</span><span class="sxs-lookup"><span data-stu-id="991e9-124">None.</span></span>
