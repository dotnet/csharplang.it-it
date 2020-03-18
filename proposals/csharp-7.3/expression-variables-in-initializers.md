---
ms.openlocfilehash: a78567594d39fc4e204e12c4f2f247b8d6995c38
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484923"
---
# <a name="expression-variables-in-initializers"></a><span data-ttu-id="bf784-101">Variabili di espressione negli inizializzatori</span><span class="sxs-lookup"><span data-stu-id="bf784-101">Expression variables in initializers</span></span>

## <a name="summary"></a><span data-ttu-id="bf784-102">Summary</span><span class="sxs-lookup"><span data-stu-id="bf784-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="bf784-103">Si estendono le funzionalità introdotte C# in 7 per consentire espressioni contenenti variabili di espressione (dichiarazioni di variabili out e modelli di dichiarazione) in inizializzatori di campo, inizializzatori di proprietà, inizializzatori ctor e clausole di query.</span><span class="sxs-lookup"><span data-stu-id="bf784-103">We extend the features introduced in C# 7 to permit expressions containing expression variables (out variable declarations and declaration patterns) in field initializers, property initializers, ctor-initializers, and query clauses.</span></span>

## <a name="motivation"></a><span data-ttu-id="bf784-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="bf784-104">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="bf784-105">Questa operazione completa un paio di bordi approssimativi rimasti nella C# lingua a causa della mancanza di tempo.</span><span class="sxs-lookup"><span data-stu-id="bf784-105">This completes a couple of the rough edges left in the C# language due to lack of time.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="bf784-106">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="bf784-106">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="bf784-107">Viene rimossa la restrizione che impedisce la dichiarazione di variabili di espressione (dichiarazioni di variabili out e modelli di dichiarazione) in un inizializzatore ctor.</span><span class="sxs-lookup"><span data-stu-id="bf784-107">We remove the restriction preventing the declaration of expression variables (out variable declarations and declaration patterns) in a ctor-initializer.</span></span> <span data-ttu-id="bf784-108">Una variabile dichiarata è nell'ambito di tutto il corpo del costruttore.</span><span class="sxs-lookup"><span data-stu-id="bf784-108">Such a declared variable is in scope throughout the body of the constructor.</span></span>

<span data-ttu-id="bf784-109">Viene rimossa la restrizione che impedisce la dichiarazione di variabili di espressione (dichiarazioni di variabili out e modelli di dichiarazione) in un inizializzatore di campo o proprietà.</span><span class="sxs-lookup"><span data-stu-id="bf784-109">We remove the restriction preventing the declaration of expression variables (out variable declarations and declaration patterns) in a field or property initializer.</span></span> <span data-ttu-id="bf784-110">Tale variabile dichiarata rientra nell'ambito dell'espressione di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="bf784-110">Such a declared variable is in scope throughout the initializing expression.</span></span>

<span data-ttu-id="bf784-111">Viene rimossa la restrizione che impedisce la dichiarazione di variabili di espressione (dichiarazioni di variabili out e modelli di dichiarazione) in una clausola dell'espressione di query che viene convertita nel corpo di un'espressione lambda.</span><span class="sxs-lookup"><span data-stu-id="bf784-111">We remove the restriction preventing the declaration of expression variables (out variable declarations and declaration patterns) in a query expression clause that is translated into the body of a lambda.</span></span> <span data-ttu-id="bf784-112">Tale variabile dichiarata rientra nell'ambito di tale espressione della clausola di query.</span><span class="sxs-lookup"><span data-stu-id="bf784-112">Such a declared variable is in scope throughout that expression of the query clause.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="bf784-113">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="bf784-113">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="bf784-114">No.</span><span class="sxs-lookup"><span data-stu-id="bf784-114">None.</span></span>

## <a name="alternatives"></a><span data-ttu-id="bf784-115">Alternativi</span><span class="sxs-lookup"><span data-stu-id="bf784-115">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="bf784-116">L'ambito appropriato per le variabili di espressione dichiarate in questi contesti non è ovvio e merita ulteriore discussione LDM.</span><span class="sxs-lookup"><span data-stu-id="bf784-116">The appropriate scope for expression variables declared in these contexts is not obvious, and deserves further LDM discussion.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="bf784-117">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="bf784-117">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="bf784-118">[] Qual è l'ambito appropriato per queste variabili?</span><span class="sxs-lookup"><span data-stu-id="bf784-118">[ ] What is the appropriate scope for these variables?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="bf784-119">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="bf784-119">Design meetings</span></span>

<span data-ttu-id="bf784-120">No.</span><span class="sxs-lookup"><span data-stu-id="bf784-120">None.</span></span>
