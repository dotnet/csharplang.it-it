---
ms.openlocfilehash: 6f05bbc1365e59d737103b586db9d4a242c6d306
ms.sourcegitcommit: e9afb74cc1abd56db93b4b50bd5e6765e27c1c5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "79485084"
---
# <a name="static-lambdas"></a><span data-ttu-id="4198c-101">Espressioni lambda statiche</span><span class="sxs-lookup"><span data-stu-id="4198c-101">Static lambdas</span></span>

## <a name="summary"></a><span data-ttu-id="4198c-102">Summary</span><span class="sxs-lookup"><span data-stu-id="4198c-102">Summary</span></span>

<span data-ttu-id="4198c-103">Supportano le espressioni lambda che non consentono l'acquisizione dello stato dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="4198c-103">Support lambdas that disallow capturing state from the enclosing scope.</span></span>

## <a name="motivation"></a><span data-ttu-id="4198c-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="4198c-104">Motivation</span></span>

<span data-ttu-id="4198c-105">Evitare di acquisire lo stato involontariamente dal contesto di inclusione.</span><span class="sxs-lookup"><span data-stu-id="4198c-105">Avoid unintentionally capturing state from the enclosing context.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="4198c-106">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="4198c-106">Detailed design</span></span>

<span data-ttu-id="4198c-107">Le espressioni lambda con `static` non possono acquisire lo stato dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="4198c-107">A lambdas with `static` cannot capture state from the enclosing scope.</span></span>
<span data-ttu-id="4198c-108">Di conseguenza, variabili locali, parametri e `this` dall'ambito di inclusione non sono disponibili all'interno di un'espressione lambda `static`.</span><span class="sxs-lookup"><span data-stu-id="4198c-108">As a result, locals, parameters, and `this` from the enclosing scope are not available within a `static` lambda.</span></span>

<span data-ttu-id="4198c-109">Una `static` lambda non può fare riferimento a membri di istanza da un riferimento implicito o esplicito `this` o `base`.</span><span class="sxs-lookup"><span data-stu-id="4198c-109">A `static` lambda cannot reference instance members from an implicit or explicit `this` or `base` reference.</span></span>

<span data-ttu-id="4198c-110">Un'espressione lambda `static` può fare riferimento a `static` membri dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="4198c-110">A `static` lambda may reference `static` members from the enclosing scope.</span></span>

<span data-ttu-id="4198c-111">Un'espressione lambda `static` può fare riferimento a `constant` definizioni dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="4198c-111">A `static` lambda may reference `constant` definitions from the enclosing scope.</span></span>

<span data-ttu-id="4198c-112">`nameof()` in un'espressione lambda `static` può fare riferimento a variabili locali, parametri o `this` o `base` dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="4198c-112">`nameof()` in a `static` lambda may reference locals, parameters, or `this` or `base` from the enclosing scope.</span></span>

<span data-ttu-id="4198c-113">Le regole di accessibilità per i membri `private` nell'ambito di inclusione sono le stesse per le espressioni lambda `static` e non`static`.</span><span class="sxs-lookup"><span data-stu-id="4198c-113">Accessibility rules for `private` members in the enclosing scope are the same for `static` and non-`static` lambdas.</span></span>

<span data-ttu-id="4198c-114">Non viene garantita alcuna garanzia in merito al fatto che una definizione lambda `static` venga emessa come metodo `static` nei metadati.</span><span class="sxs-lookup"><span data-stu-id="4198c-114">No guarantee is made as to whether a `static` lambda definition is emitted as a `static` method in metadata.</span></span> <span data-ttu-id="4198c-115">Questa operazione viene lasciata all'implementazione del compilatore per ottimizzare.</span><span class="sxs-lookup"><span data-stu-id="4198c-115">This is left up to the compiler implementation to optimize.</span></span>

<span data-ttu-id="4198c-116">Una funzione locale non`static` o un'espressione lambda può acquisire lo stato da un `static` lambda di inclusione ma non può acquisire lo stato al di fuori della `static` lambda di inclusione.</span><span class="sxs-lookup"><span data-stu-id="4198c-116">A non-`static` local function or lambda can capture state from an enclosing `static` lambda but cannot capture state outside the enclosing `static` lambda.</span></span>

<span data-ttu-id="4198c-117">In un albero delle espressioni è possibile utilizzare un'espressione lambda `static`.</span><span class="sxs-lookup"><span data-stu-id="4198c-117">A `static` lambda can be used in an expression tree.</span></span>

<span data-ttu-id="4198c-118">La rimozione del modificatore `static` da un'espressione lambda in un programma valido non comporta la modifica del significato del programma.</span><span class="sxs-lookup"><span data-stu-id="4198c-118">Removing the `static` modifier from a lambda in a valid program does not change the meaning of the program.</span></span>
