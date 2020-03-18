---
ms.openlocfilehash: fecd5a6c1e0f6c7a7a7beac0e4e60445281c7846
ms.sourcegitcommit: 1b488e76c2c07aafc377bc5e8a7197252c82f425
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "79485098"
---
# <a name="static-local-functions"></a><span data-ttu-id="06e40-101">Funzioni locali statiche</span><span class="sxs-lookup"><span data-stu-id="06e40-101">Static local functions</span></span>

## <a name="summary"></a><span data-ttu-id="06e40-102">Summary</span><span class="sxs-lookup"><span data-stu-id="06e40-102">Summary</span></span>

<span data-ttu-id="06e40-103">Supportano le funzioni locali che non consentono l'acquisizione dello stato dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="06e40-103">Support local functions that disallow capturing state from the enclosing scope.</span></span>

## <a name="motivation"></a><span data-ttu-id="06e40-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="06e40-104">Motivation</span></span>

<span data-ttu-id="06e40-105">Evitare di acquisire lo stato involontariamente dal contesto di inclusione.</span><span class="sxs-lookup"><span data-stu-id="06e40-105">Avoid unintentionally capturing state from the enclosing context.</span></span>
<span data-ttu-id="06e40-106">Consente di usare le funzioni locali negli scenari in cui è necessario un `static` metodo.</span><span class="sxs-lookup"><span data-stu-id="06e40-106">Allow local functions to be used in scenarios where a `static` method is required.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="06e40-107">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="06e40-107">Detailed design</span></span>

<span data-ttu-id="06e40-108">Una funzione locale dichiarata `static` non è in grado di acquisire lo stato dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="06e40-108">A local function declared `static` cannot capture state from the enclosing scope.</span></span>
<span data-ttu-id="06e40-109">Di conseguenza, variabili locali, parametri e `this` dall'ambito di inclusione non sono disponibili all'interno di una funzione `static` locale.</span><span class="sxs-lookup"><span data-stu-id="06e40-109">As a result, locals, parameters, and `this` from the enclosing scope are not available within a `static` local function.</span></span>

<span data-ttu-id="06e40-110">Una `static` funzione locale non può fare riferimento a membri di istanza da un riferimento implicito o esplicito `this` o `base`.</span><span class="sxs-lookup"><span data-stu-id="06e40-110">A `static` local function cannot reference instance members from an implicit or explicit `this` or `base` reference.</span></span>

<span data-ttu-id="06e40-111">Una funzione locale `static` può fare riferimento a `static` membri dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="06e40-111">A `static` local function may reference `static` members from the enclosing scope.</span></span>

<span data-ttu-id="06e40-112">Una funzione locale `static` può fare riferimento a definizioni `constant` dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="06e40-112">A `static` local function may reference `constant` definitions from the enclosing scope.</span></span>

<span data-ttu-id="06e40-113">`nameof()` in una funzione locale `static` può fare riferimento a variabili locali, parametri o `this` o `base` dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="06e40-113">`nameof()` in a `static` local function may reference locals, parameters, or `this` or `base` from the enclosing scope.</span></span>

<span data-ttu-id="06e40-114">Le regole di accessibilità per i membri `private` nell'ambito di inclusione sono le stesse per `static` e non`static` funzioni locali.</span><span class="sxs-lookup"><span data-stu-id="06e40-114">Accessibility rules for `private` members in the enclosing scope are the same for `static` and non-`static` local functions.</span></span>

<span data-ttu-id="06e40-115">Una `static` definizione di funzione locale viene emessa come metodo di `static` nei metadati, anche se utilizzata solo in un delegato.</span><span class="sxs-lookup"><span data-stu-id="06e40-115">A `static` local function definition is emitted as a `static` method in metadata, even if only used in a delegate.</span></span>

<span data-ttu-id="06e40-116">Una funzione locale non`static` o un'espressione lambda può acquisire lo stato da una funzione `static` locale che lo contiene ma non può acquisire lo stato al di fuori della funzione `static` locale di inclusione.</span><span class="sxs-lookup"><span data-stu-id="06e40-116">A non-`static` local function or lambda can capture state from an enclosing `static` local function but cannot capture state outside the enclosing `static` local function.</span></span>

<span data-ttu-id="06e40-117">Non è possibile richiamare una funzione locale `static` in un albero delle espressioni.</span><span class="sxs-lookup"><span data-stu-id="06e40-117">A `static` local function cannot be invoked in an expression tree.</span></span>

<span data-ttu-id="06e40-118">Una chiamata a una funzione locale viene emessa come `call` anziché `callvirt`, indipendentemente dal fatto che la funzione locale sia `static`ta.</span><span class="sxs-lookup"><span data-stu-id="06e40-118">A call to a local function is emitted as `call` rather than `callvirt`, regardless of whether the local function is `static`.</span></span>

<span data-ttu-id="06e40-119">Risoluzione dell'overload di una chiamata all'interno di una funzione locale non interessata dal fatto che la funzione locale sia `static`.</span><span class="sxs-lookup"><span data-stu-id="06e40-119">Overload resolution of a call within a local function not affected by whether the local function is `static`.</span></span>

<span data-ttu-id="06e40-120">La rimozione del modificatore `static` da una funzione locale in un programma valido non comporta la modifica del significato del programma.</span><span class="sxs-lookup"><span data-stu-id="06e40-120">Removing the `static` modifier from a local function in a valid program does not change the meaning of the program.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="06e40-121">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="06e40-121">Design meetings</span></span>

https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-09-10.md#static-local-functions
