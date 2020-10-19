---
ms.openlocfilehash: 165de098ddbd753416e01c4ff8e44750d9b7f66c
ms.sourcegitcommit: a0b59a6768813152b4b5b6df3637041a64aba2ee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92185115"
---
# <a name="name-shadowing-in-nested-functions"></a><span data-ttu-id="09b9b-101">Shadowing del nome in funzioni annidate</span><span class="sxs-lookup"><span data-stu-id="09b9b-101">Name shadowing in nested functions</span></span>

## <a name="summary"></a><span data-ttu-id="09b9b-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="09b9b-102">Summary</span></span>

<span data-ttu-id="09b9b-103">Consentire i nomi delle variabili nelle espressioni lambda e nelle funzioni locali per riutilizzare (e ombreggiare) i nomi del metodo o della funzione contenitore.</span><span class="sxs-lookup"><span data-stu-id="09b9b-103">Permit variable names in lambdas and local functions to reuse (and shadow) names from the enclosing method or function.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="09b9b-104">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="09b9b-104">Detailed design</span></span>

<span data-ttu-id="09b9b-105">Con `-langversion:8` , i nomi delle variabili locali, le funzioni locali, i parametri, i parametri di tipo e le variabili di intervallo in un'espressione lambda o in una funzione locale possono riutilizzare i nomi di variabili locali, funzioni locali, parametri, parametri di tipo e variabili di intervallo da un metodo o una funzione di inclusione.</span><span class="sxs-lookup"><span data-stu-id="09b9b-105">With `-langversion:8`, names of locals, local functions, parameters, type parameters, and range variables within a lambda or local function can reuse names of locals, local functions, parameters, type parameters, and range variables from an enclosing method or function.</span></span> <span data-ttu-id="09b9b-106">Il nome nella funzione annidata nasconde il simbolo con lo stesso nome della funzione contenitore all'interno della funzione nidificata.</span><span class="sxs-lookup"><span data-stu-id="09b9b-106">The name in the nested function hides the symbol of the same name from the enclosing function within the nested function.</span></span>

<span data-ttu-id="09b9b-107">Lo shadowing è supportato per `static` le `static` funzioni lambda e non locali.</span><span class="sxs-lookup"><span data-stu-id="09b9b-107">Shadowing is supported for `static` and non-`static` local functions and lambdas.</span></span>

<span data-ttu-id="09b9b-108">Non è possibile modificare il comportamento utilizzando `-langversion:7.3` o versioni precedenti: i nomi nelle funzioni annidate che i nomi Shadow del metodo o della funzione contenitore vengono segnalati come errori in tali casi.</span><span class="sxs-lookup"><span data-stu-id="09b9b-108">There is no change in behavior using `-langversion:7.3` or earlier: names in nested functions that shadow names from the enclosing method or function are reported as errors in those cases.</span></span>

<span data-ttu-id="09b9b-109">Tutte le ombreggiature consentite in precedenza sono ancora supportate con `-langversion:8` .</span><span class="sxs-lookup"><span data-stu-id="09b9b-109">Any shadowing previously permitted is still supported with `-langversion:8`.</span></span> <span data-ttu-id="09b9b-110">Ad esempio, i nomi delle variabili possono nascondere il tipo e i nomi dei membri; i nomi delle variabili e possono includere il metodo di inclusione o i nomi delle funzioni locali.</span><span class="sxs-lookup"><span data-stu-id="09b9b-110">For instance: variable names may shadow type and member names; and variable names may shadow enclosing method or local function names.</span></span>

<span data-ttu-id="09b9b-111">Lo shadowing di un nome dichiarato in un ambito di inclusione nella stessa lambda o nella stessa funzione locale viene comunque segnalato come un errore.</span><span class="sxs-lookup"><span data-stu-id="09b9b-111">Shadowing a name declared in an enclosing scope in the same lambda or local function is still reported as an error.</span></span>

<span data-ttu-id="09b9b-112">Viene segnalato un avviso per un parametro di tipo in una funzione locale che nasconde un parametro di tipo nel tipo, nel metodo o nella funzione di inclusione.</span><span class="sxs-lookup"><span data-stu-id="09b9b-112">A warning is reported for a type parameter in a local function that shadows a type parameter in the enclosing type, method, or function.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="09b9b-113">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="09b9b-113">Design meetings</span></span>

- https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-09-10.md#static-local-functions
- https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-01-16.md#shadowing-in-nested-functions
