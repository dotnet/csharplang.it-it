---
ms.openlocfilehash: 0a6b3a4aa86bac8e7855ec2f4c50ac022941000e
ms.sourcegitcommit: cc68af0b2a6e2ef5780eeb43935600b5927ee720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86157207"
---
# <a name="static-anonymous-functions"></a><span data-ttu-id="dd3dc-101">Funzioni anonime statiche</span><span class="sxs-lookup"><span data-stu-id="dd3dc-101">Static anonymous functions</span></span>

## <a name="summary"></a><span data-ttu-id="dd3dc-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="dd3dc-102">Summary</span></span>

<span data-ttu-id="dd3dc-103">Consente un modificatore ' static ' per le espressioni lambda e i metodi anonimi, che non consente l'acquisizione di variabili locali o lo stato dell'istanza dagli ambiti contenitore.</span><span class="sxs-lookup"><span data-stu-id="dd3dc-103">Allow a 'static' modifier on lambdas and anonymous methods, which disallows capture of locals or instance state from containing scopes.</span></span>

## <a name="motivation"></a><span data-ttu-id="dd3dc-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="dd3dc-104">Motivation</span></span>

<span data-ttu-id="dd3dc-105">Evitare l'acquisizione accidentale dello stato dal contesto di inclusione, che può causare un periodo di conservazione imprevisto di oggetti acquisiti o allocazioni aggiuntive impreviste.</span><span class="sxs-lookup"><span data-stu-id="dd3dc-105">Avoid unintentionally capturing state from the enclosing context, which can result in unexpected retention of captured objects or unexpected additional allocations.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="dd3dc-106">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="dd3dc-106">Detailed design</span></span>

<span data-ttu-id="dd3dc-107">Un metodo lambda o anonimo può avere un `static` modificatore.</span><span class="sxs-lookup"><span data-stu-id="dd3dc-107">A lambda or anonymous method may have a `static` modifier.</span></span> <span data-ttu-id="dd3dc-108">Il `static` modificatore indica che l'espressione lambda o il metodo anonimo è una *funzione statica anonima*.</span><span class="sxs-lookup"><span data-stu-id="dd3dc-108">The `static` modifier indicates that the lambda or anonymous method is a *static anonymous function*.</span></span>

<span data-ttu-id="dd3dc-109">Una *funzione anonima statica* non può acquisire lo stato dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="dd3dc-109">A *static anonymous function* cannot capture state from the enclosing scope.</span></span>
<span data-ttu-id="dd3dc-110">Di conseguenza, variabili locali, parametri e `this` dall'ambito di inclusione non sono disponibili all'interno di una *funzione anonima statica*.</span><span class="sxs-lookup"><span data-stu-id="dd3dc-110">As a result, locals, parameters, and `this` from the enclosing scope are not available within a *static anonymous function*.</span></span>

<span data-ttu-id="dd3dc-111">Una *funzione anonima statica* non può fare riferimento a membri di istanza da un riferimento implicito o esplicito `this` o `base` .</span><span class="sxs-lookup"><span data-stu-id="dd3dc-111">A *static anonymous function* cannot reference instance members from an implicit or explicit `this` or `base` reference.</span></span>

<span data-ttu-id="dd3dc-112">Una *funzione statica anonima* può fare riferimento a `static` membri dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="dd3dc-112">A *static anonymous function* may reference `static` members from the enclosing scope.</span></span>

<span data-ttu-id="dd3dc-113">Una *funzione statica anonima* può fare riferimento a `constant` definizioni dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="dd3dc-113">A *static anonymous function* may reference `constant` definitions from the enclosing scope.</span></span>

<span data-ttu-id="dd3dc-114">`nameof()`in una *funzione statica anonima* può fare riferimento a variabili locali, parametri o `this` o `base` dall'ambito di inclusione.</span><span class="sxs-lookup"><span data-stu-id="dd3dc-114">`nameof()` in a *static anonymous function* may reference locals, parameters, or `this` or `base` from the enclosing scope.</span></span>

<span data-ttu-id="dd3dc-115">Le regole di accessibilità per `private` i membri nell'ambito di inclusione sono le stesse per le `static` funzioni e non `static` anonime.</span><span class="sxs-lookup"><span data-stu-id="dd3dc-115">Accessibility rules for `private` members in the enclosing scope are the same for `static` and non-`static` anonymous functions.</span></span>

<span data-ttu-id="dd3dc-116">Non viene garantita alcuna garanzia in merito al fatto che una definizione di *funzione statica anonima* venga emessa come `static` Metodo nei metadati.</span><span class="sxs-lookup"><span data-stu-id="dd3dc-116">No guarantee is made as to whether a *static anonymous function* definition is emitted as a `static` method in metadata.</span></span> <span data-ttu-id="dd3dc-117">Questa operazione viene lasciata all'implementazione del compilatore per ottimizzare.</span><span class="sxs-lookup"><span data-stu-id="dd3dc-117">This is left up to the compiler implementation to optimize.</span></span>

<span data-ttu-id="dd3dc-118">Una funzione non `static` locale o una funzione anonima può acquisire lo stato da una *funzione statica anonima* di inclusione, ma non può acquisire lo stato all'esterno della *funzione anonima*di inclusione statica.</span><span class="sxs-lookup"><span data-stu-id="dd3dc-118">A non-`static` local function or anonymous function can capture state from an enclosing *static anonymous function* but cannot capture state outside the enclosing *static anonymous function*.</span></span>

<span data-ttu-id="dd3dc-119">La rimozione del `static` modificatore da una funzione anonima in un programma valido non comporta la modifica del significato del programma.</span><span class="sxs-lookup"><span data-stu-id="dd3dc-119">Removing the `static` modifier from an anonymous function in a valid program does not change the meaning of the program.</span></span>
