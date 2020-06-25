---
ms.openlocfilehash: efe927e6d3abdde5a350eadc9208949710bb4a39
ms.sourcegitcommit: b901db48deffcbe2ec7cc08ec6cb376d1f7faecb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85345202"
---
# <a name="static-lambdas"></a><span data-ttu-id="58252-101">Espressioni lambda statiche</span><span class="sxs-lookup"><span data-stu-id="58252-101">Static lambdas</span></span>

## <a name="summary"></a><span data-ttu-id="58252-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="58252-102">Summary</span></span>

<span data-ttu-id="58252-103">Supportano le espressioni lambda che non consentono l'acquisizione dello stato dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="58252-103">Support lambdas that disallow capturing state from the enclosing scope.</span></span>

## <a name="motivation"></a><span data-ttu-id="58252-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="58252-104">Motivation</span></span>

<span data-ttu-id="58252-105">Evitare di acquisire lo stato involontariamente dal contesto di inclusione.</span><span class="sxs-lookup"><span data-stu-id="58252-105">Avoid unintentionally capturing state from the enclosing context.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="58252-106">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="58252-106">Detailed design</span></span>

<span data-ttu-id="58252-107">Le espressioni lambda con `static` non possono acquisire lo stato dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="58252-107">A lambdas with `static` cannot capture state from the enclosing scope.</span></span>
<span data-ttu-id="58252-108">Di conseguenza, variabili locali, parametri e `this` dall'ambito di inclusione non sono disponibili all'interno di un' `static` espressione lambda.</span><span class="sxs-lookup"><span data-stu-id="58252-108">As a result, locals, parameters, and `this` from the enclosing scope are not available within a `static` lambda.</span></span>

<span data-ttu-id="58252-109">Un' `static` espressione lambda non può fare riferimento a membri di istanza da un riferimento implicito o esplicito `this` o `base` .</span><span class="sxs-lookup"><span data-stu-id="58252-109">A `static` lambda cannot reference instance members from an implicit or explicit `this` or `base` reference.</span></span>

<span data-ttu-id="58252-110">Un' `static` espressione lambda può fare riferimento `static` ai membri dell'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="58252-110">A `static` lambda may reference `static` members from the enclosing scope.</span></span>

<span data-ttu-id="58252-111">Un' `static` espressione lambda può fare riferimento a `constant` definizioni dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="58252-111">A `static` lambda may reference `constant` definitions from the enclosing scope.</span></span>

<span data-ttu-id="58252-112">`nameof()`in un' `static` espressione lambda può fare riferimento a variabili locali, parametri o `this` o `base` dall'ambito di inclusione.</span><span class="sxs-lookup"><span data-stu-id="58252-112">`nameof()` in a `static` lambda may reference locals, parameters, or `this` or `base` from the enclosing scope.</span></span>

<span data-ttu-id="58252-113">Le regole di accessibilità per `private` i membri nell'ambito di inclusione sono le stesse per `static` e non per le `static` espressioni lambda.</span><span class="sxs-lookup"><span data-stu-id="58252-113">Accessibility rules for `private` members in the enclosing scope are the same for `static` and non-`static` lambdas.</span></span>

<span data-ttu-id="58252-114">Non viene garantita alcuna garanzia in merito al fatto che una `static` definizione lambda venga emessa come `static` Metodo nei metadati.</span><span class="sxs-lookup"><span data-stu-id="58252-114">No guarantee is made as to whether a `static` lambda definition is emitted as a `static` method in metadata.</span></span> <span data-ttu-id="58252-115">Questa operazione viene lasciata all'implementazione del compilatore per ottimizzare.</span><span class="sxs-lookup"><span data-stu-id="58252-115">This is left up to the compiler implementation to optimize.</span></span>

<span data-ttu-id="58252-116">Una `static` funzione o un'espressione lambda non locale può acquisire lo stato da un' `static` espressione lambda di inclusione ma non può acquisire lo stato al di fuori dell'espressione lambda di inclusione `static` .</span><span class="sxs-lookup"><span data-stu-id="58252-116">A non-`static` local function or lambda can capture state from an enclosing `static` lambda but cannot capture state outside the enclosing `static` lambda.</span></span>

<span data-ttu-id="58252-117">La rimozione del `static` modificatore da un'espressione lambda in un programma valido non comporta la modifica del significato del programma.</span><span class="sxs-lookup"><span data-stu-id="58252-117">Removing the `static` modifier from a lambda in a valid program does not change the meaning of the program.</span></span>
