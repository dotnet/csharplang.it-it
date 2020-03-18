---
ms.openlocfilehash: 4f66c0f60d05ed6509a1d0843318a71d1b36c351
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484706"
---
# <a name="pattern-matching-with-generics"></a><span data-ttu-id="1230b-101">criteri di ricerca con generics</span><span class="sxs-lookup"><span data-stu-id="1230b-101">pattern-matching with generics</span></span>

* <span data-ttu-id="1230b-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="1230b-102">[x] Proposed</span></span>
* <span data-ttu-id="1230b-103">[] Prototipo:</span><span class="sxs-lookup"><span data-stu-id="1230b-103">[ ] Prototype:</span></span>
* <span data-ttu-id="1230b-104">[] Implementazione:</span><span class="sxs-lookup"><span data-stu-id="1230b-104">[ ] Implementation:</span></span>
* <span data-ttu-id="1230b-105">[] Specifica:</span><span class="sxs-lookup"><span data-stu-id="1230b-105">[ ] Specification:</span></span>

## <a name="summary"></a><span data-ttu-id="1230b-106">Summary</span><span class="sxs-lookup"><span data-stu-id="1230b-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="1230b-107">La specifica per l' [operatore C# As esistente](../../spec/expressions.md#the-as-operator) consente di non eseguire alcuna conversione tra il tipo dell'operando e il tipo specificato quando uno dei due è un tipo aperto.</span><span class="sxs-lookup"><span data-stu-id="1230b-107">The specification for the [existing C# as operator](../../spec/expressions.md#the-as-operator) permits there to be no conversion between the type of the operand and the specified type when either is an open type.</span></span> <span data-ttu-id="1230b-108">Tuttavia, in C# 7 il modello di `Type identifier` richiede la conversione tra il tipo di input e il tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="1230b-108">However, in C# 7 the `Type identifier` pattern requires there be a conversion between the type of the input and the given type.</span></span>

<span data-ttu-id="1230b-109">Si consiglia di ridurre questa situazione e di modificare `expression is Type identifier`, oltre a essere consentite nelle condizioni in cui è C# consentito in 7, per consentire anche quando `expression as Type` sarebbe consentito.</span><span class="sxs-lookup"><span data-stu-id="1230b-109">We propose to relax this and change `expression is Type identifier`, in addition to being permitted in the conditions when it is permitted in C# 7, to also be permitted when `expression as Type` would be allowed.</span></span> <span data-ttu-id="1230b-110">In particolare, i nuovi casi sono casi in cui il tipo dell'espressione o il tipo specificato è un tipo aperto.</span><span class="sxs-lookup"><span data-stu-id="1230b-110">Specifically, the new cases are cases where the type of the expression or the specified type is an open type.</span></span> 

## <a name="motivation"></a><span data-ttu-id="1230b-111">Motivazione</span><span class="sxs-lookup"><span data-stu-id="1230b-111">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="1230b-112">I casi in cui la corrispondenza dei modelli dovrebbe "ovviamente" essere consentita, attualmente non è possibile compilare.</span><span class="sxs-lookup"><span data-stu-id="1230b-112">Cases where pattern-matching should "obviously" be permitted currently fail to compile.</span></span> <span data-ttu-id="1230b-113">Vedere, ad esempio, https://github.com/dotnet/roslyn/issues/16195.</span><span class="sxs-lookup"><span data-stu-id="1230b-113">See, for example, https://github.com/dotnet/roslyn/issues/16195.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="1230b-114">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="1230b-114">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="1230b-115">Si modifica il paragrafo nella specifica dei criteri di ricerca (l'aggiunta proposta viene visualizzata in grassetto):</span><span class="sxs-lookup"><span data-stu-id="1230b-115">We change the paragraph in the pattern-matching specification (the proposed addition is shown in bold):</span></span>

> <span data-ttu-id="1230b-116">Alcune combinazioni di tipo statico del lato sinistro e del tipo specificato vengono considerate incompatibili e generano un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="1230b-116">Certain combinations of static type of the left-hand-side and the given type are considered incompatible and result in compile-time error.</span></span> <span data-ttu-id="1230b-117">Un valore di tipo statico `E` viene definito *modello compatibile* con il tipo `T` se esiste una conversione di identità, una conversione di un riferimento implicito, una conversione boxing, una conversione di riferimento esplicita o una conversione unboxing da `E` a `T`**oppure se `E` o `T` è un tipo aperto**.</span><span class="sxs-lookup"><span data-stu-id="1230b-117">A value of static type `E` is said to be *pattern compatible* with the type `T` if there exists an identity conversion, an implicit reference conversion, a boxing conversion, an explicit reference conversion, or an unboxing conversion from `E` to `T`**, or if either `E` or `T` is an open type**.</span></span> <span data-ttu-id="1230b-118">Si tratta di un errore in fase di compilazione se un'espressione di tipo `E` non è compatibile con il tipo in un modello di tipo con cui corrisponde.</span><span class="sxs-lookup"><span data-stu-id="1230b-118">It is a compile-time error if an expression of type `E` is not pattern compatible with the type in a type pattern that it is matched with.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="1230b-119">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="1230b-119">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="1230b-120">No.</span><span class="sxs-lookup"><span data-stu-id="1230b-120">None.</span></span>

## <a name="alternatives"></a><span data-ttu-id="1230b-121">Alternativi</span><span class="sxs-lookup"><span data-stu-id="1230b-121">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="1230b-122">No.</span><span class="sxs-lookup"><span data-stu-id="1230b-122">None.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="1230b-123">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="1230b-123">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="1230b-124">No.</span><span class="sxs-lookup"><span data-stu-id="1230b-124">None.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="1230b-125">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="1230b-125">Design meetings</span></span>

<span data-ttu-id="1230b-126">LDM ha preso in considerazione questa domanda e si è ritenuta una modifica a livello di correzione di bug.</span><span class="sxs-lookup"><span data-stu-id="1230b-126">LDM considered this question and felt it was a bug-fix level change.</span></span> <span data-ttu-id="1230b-127">Si tratta di una funzionalità di linguaggio distinta, perché la modifica dopo il rilascio della lingua introduce un'incompatibilità con le versioni successive.</span><span class="sxs-lookup"><span data-stu-id="1230b-127">We are treating it as a separate language feature because just making the change after the language has been released would introduce a forward incompatibility.</span></span> <span data-ttu-id="1230b-128">Per usare la modifica proposta è necessario che il programmatore specifichi la versione del linguaggio 7,1.</span><span class="sxs-lookup"><span data-stu-id="1230b-128">Using the proposed change requires that the programmer specify language version 7.1.</span></span>
