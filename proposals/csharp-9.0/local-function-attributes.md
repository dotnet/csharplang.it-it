---
ms.openlocfilehash: e48717f85f781acfb69234c139d06688cd828fcc
ms.sourcegitcommit: a88d56e3131d7a94c65e637c276379541a3cd491
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87434449"
---
# <a name="attributes-on-local-functions"></a><span data-ttu-id="b65bd-101">Attributi nelle funzioni locali</span><span class="sxs-lookup"><span data-stu-id="b65bd-101">Attributes on local functions</span></span>

## <a name="attributes"></a><span data-ttu-id="b65bd-102">Attributi</span><span class="sxs-lookup"><span data-stu-id="b65bd-102">Attributes</span></span>

<span data-ttu-id="b65bd-103">Le dichiarazioni di funzione locali ora possono avere [attributi](../../spec/attributes.md).</span><span class="sxs-lookup"><span data-stu-id="b65bd-103">Local function declarations are now permitted to have [attributes](../../spec/attributes.md).</span></span> <span data-ttu-id="b65bd-104">I parametri e i parametri di tipo nelle funzioni locali possono avere anche attributi.</span><span class="sxs-lookup"><span data-stu-id="b65bd-104">Parameters and type parameters on local functions are also allowed to have attributes.</span></span>

<span data-ttu-id="b65bd-105">Gli attributi con un significato specificato quando vengono applicati a un metodo, i relativi parametri o i parametri di tipo avranno lo stesso significato quando vengono applicati rispettivamente a una funzione locale, ai relativi parametri o ai parametri di tipo.</span><span class="sxs-lookup"><span data-stu-id="b65bd-105">Attributes with a specified meaning when applied to a method, its parameters, or its type parameters will have the same meaning when applied to a local function, its parameters, or its type parameters, respectively.</span></span>

<span data-ttu-id="b65bd-106">Una funzione locale può essere resa condizionale nello stesso senso di un [metodo condizionale](../../spec/attributes.md#the-conditional-attribute) mediante la decorazione di un oggetto `[ConditionalAttribute]` .</span><span class="sxs-lookup"><span data-stu-id="b65bd-106">A local function can be made conditional in the same sense as a [conditional method](../../spec/attributes.md#the-conditional-attribute) by decorating it with a `[ConditionalAttribute]`.</span></span> <span data-ttu-id="b65bd-107">Una funzione locale condizionale deve essere anche `static` .</span><span class="sxs-lookup"><span data-stu-id="b65bd-107">A conditional local function must also be `static`.</span></span> <span data-ttu-id="b65bd-108">Tutte le restrizioni sui metodi condizionali si applicano anche alle funzioni locali condizionali, tra cui il tipo restituito deve essere `void` .</span><span class="sxs-lookup"><span data-stu-id="b65bd-108">All restrictions on conditional methods also apply to conditional local functions, including that the return type must be `void`.</span></span>

## <a name="extern"></a><span data-ttu-id="b65bd-109">Extern</span><span class="sxs-lookup"><span data-stu-id="b65bd-109">Extern</span></span>

<span data-ttu-id="b65bd-110">Il `extern` modificatore è ora consentito nelle funzioni locali.</span><span class="sxs-lookup"><span data-stu-id="b65bd-110">The `extern` modifier is now permitted on local functions.</span></span> <span data-ttu-id="b65bd-111">In questo modo la funzione locale è esterna allo stesso senso di un [metodo esterno](../../spec/classes.md#external-methods).</span><span class="sxs-lookup"><span data-stu-id="b65bd-111">This makes the local function external in the same sense as an [external method](../../spec/classes.md#external-methods).</span></span>

<span data-ttu-id="b65bd-112">Analogamente a un metodo esterno, il *corpo della funzione locale* di una funzione locale esterna deve essere un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="b65bd-112">Similarly to an external method, the *local-function-body* of an external local function must be a semicolon.</span></span> <span data-ttu-id="b65bd-113">Un *corpo della funzione locale* con punto e virgola è consentito solo in una funzione locale esterna.</span><span class="sxs-lookup"><span data-stu-id="b65bd-113">A semicolon *local-function-body* is only permitted on an external local function.</span></span> 

<span data-ttu-id="b65bd-114">Una funzione locale esterna deve essere anche `static` .</span><span class="sxs-lookup"><span data-stu-id="b65bd-114">An external local function must also be `static`.</span></span>

## <a name="syntax"></a><span data-ttu-id="b65bd-115">Sintassi</span><span class="sxs-lookup"><span data-stu-id="b65bd-115">Syntax</span></span>

<span data-ttu-id="b65bd-116">La [grammatica delle funzioni locali](../csharp-7.0/local-functions.md#syntax-grammar) viene modificata come segue:</span><span class="sxs-lookup"><span data-stu-id="b65bd-116">The [local functions grammar](../csharp-7.0/local-functions.md#syntax-grammar) is modified as follows:</span></span>
```
local-function-header
    : attributes? local-function-modifiers? return-type identifier type-parameter-list?
        ( formal-parameter-list? ) type-parameter-constraints-clauses
    ;

local-function-modifiers
    : (async | unsafe | static | extern)*
    ;

local-function-body
    : block
    | arrow-expression-body
    | ';'
    ;
```
