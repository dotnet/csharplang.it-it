---
ms.openlocfilehash: 7ea62713416ef82034963aef06f3cb11703342ed
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484608"
---
# <a name="stackalloc-array-initializers"></a><span data-ttu-id="d1f60-101">Inizializzatori di matrice stackalloc</span><span class="sxs-lookup"><span data-stu-id="d1f60-101">Stackalloc array initializers</span></span>

## <a name="summary"></a><span data-ttu-id="d1f60-102">Summary</span><span class="sxs-lookup"><span data-stu-id="d1f60-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="d1f60-103">Consente di usare la sintassi dell'inizializzatore di matrici con `stackalloc`.</span><span class="sxs-lookup"><span data-stu-id="d1f60-103">Allow array initializer syntax to be used with `stackalloc`.</span></span>

## <a name="motivation"></a><span data-ttu-id="d1f60-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="d1f60-104">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="d1f60-105">Le matrici ordinarie possono avere elementi inizializzati al momento della creazione.</span><span class="sxs-lookup"><span data-stu-id="d1f60-105">Ordinary arrays can have their elements initialized at creation time.</span></span> <span data-ttu-id="d1f60-106">Sembra ragionevole consentirne l'uso in `stackalloc` caso.</span><span class="sxs-lookup"><span data-stu-id="d1f60-106">It seems reasonable to allow that in `stackalloc` case.</span></span>

<span data-ttu-id="d1f60-107">Il motivo per cui questa sintassi non è consentita con `stackalloc` si verifica abbastanza spesso.</span><span class="sxs-lookup"><span data-stu-id="d1f60-107">The question of why such syntax is not allowed with `stackalloc` arises fairly frequently.</span></span>  
<span data-ttu-id="d1f60-108">Vedere, ad esempio, [#1112](https://github.com/dotnet/csharplang/issues/1112)</span><span class="sxs-lookup"><span data-stu-id="d1f60-108">See, for example, [#1112](https://github.com/dotnet/csharplang/issues/1112)</span></span>

## <a name="detailed-design"></a><span data-ttu-id="d1f60-109">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="d1f60-109">Detailed design</span></span>

<span data-ttu-id="d1f60-110">Le matrici ordinarie possono essere create tramite la sintassi seguente:</span><span class="sxs-lookup"><span data-stu-id="d1f60-110">Ordinary arrays can be created through the following syntax:</span></span>

```csharp
new int[3]
new int[3] { 1, 2, 3 }
new int[] { 1, 2, 3 }
new[] { 1, 2, 3 }
```

<span data-ttu-id="d1f60-111">È necessario consentire la creazione di matrici allocate nello stack tramite:</span><span class="sxs-lookup"><span data-stu-id="d1f60-111">We should allow stack allocated arrays be created through:</span></span>  

```csharp
stackalloc int[3]               // currently allowed
stackalloc int[3] { 1, 2, 3 }
stackalloc int[] { 1, 2, 3 }
stackalloc[] { 1, 2, 3 }
```

<span data-ttu-id="d1f60-112">La semantica di tutti i case è approssimativamente identica a quella delle matrici.</span><span class="sxs-lookup"><span data-stu-id="d1f60-112">The semantics of all cases is roughly the same as with arrays.</span></span>  
<span data-ttu-id="d1f60-113">Nell'ultimo caso, ad esempio, il tipo di elemento viene dedotto dall'inizializzatore e deve essere un tipo "non gestito".</span><span class="sxs-lookup"><span data-stu-id="d1f60-113">For example: in the last case the element type is inferred from the initializer and must be an "unmanaged" type.</span></span>

<span data-ttu-id="d1f60-114">Nota: la funzionalità non dipende dalla destinazione `Span<T>`.</span><span class="sxs-lookup"><span data-stu-id="d1f60-114">NOTE: the feature is not dependent on the target being a `Span<T>`.</span></span> <span data-ttu-id="d1f60-115">È altrettanto applicabile in `T*` caso, quindi non sembra ragionevole predicarlo in `Span<T>` caso.</span><span class="sxs-lookup"><span data-stu-id="d1f60-115">It is just as applicable in `T*` case, so it does not seem reasonable to predicate it on `Span<T>` case.</span></span>  

## <a name="translation"></a><span data-ttu-id="d1f60-116">Traduzione</span><span class="sxs-lookup"><span data-stu-id="d1f60-116">Translation</span></span>

<span data-ttu-id="d1f60-117">L'implementazione ingenua può semplicemente inizializzare la matrice subito dopo la creazione tramite una serie di assegnazioni di elementi.</span><span class="sxs-lookup"><span data-stu-id="d1f60-117">The naive implementation could just initialize the array right after creation through a series of element-wise assignments.</span></span>  

<span data-ttu-id="d1f60-118">Analogamente a quanto avviene con le matrici, potrebbe essere possibile e opportuno rilevare i casi in cui tutti o la maggior parte degli elementi sono tipi copiabili e utilizzano tecniche più efficienti copiando lo stato creato in precedenza di tutti gli elementi costanti.</span><span class="sxs-lookup"><span data-stu-id="d1f60-118">Similarly to the case with arrays, it might be possible and desirable to detect cases where all or most of the elements are blittable types and use more efficient techniques by copying over the pre-created state of all the constant elements.</span></span> 

## <a name="drawbacks"></a><span data-ttu-id="d1f60-119">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="d1f60-119">Drawbacks</span></span>
[drawbacks]: #drawbacks

## <a name="alternatives"></a><span data-ttu-id="d1f60-120">Alternativi</span><span class="sxs-lookup"><span data-stu-id="d1f60-120">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="d1f60-121">Si tratta di una funzionalità di praticità.</span><span class="sxs-lookup"><span data-stu-id="d1f60-121">This is a convenience feature.</span></span> <span data-ttu-id="d1f60-122">È possibile semplicemente non eseguire alcuna operazione.</span><span class="sxs-lookup"><span data-stu-id="d1f60-122">It is possible to just do nothing.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="d1f60-123">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="d1f60-123">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

## <a name="design-meetings"></a><span data-ttu-id="d1f60-124">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="d1f60-124">Design meetings</span></span>

<span data-ttu-id="d1f60-125">Nessuno ancora.</span><span class="sxs-lookup"><span data-stu-id="d1f60-125">None yet.</span></span> 
