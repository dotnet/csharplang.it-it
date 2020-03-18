---
ms.openlocfilehash: a4b0fbbc600eaf1e705ad8e6bd9fcecb44100761
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484440"
---
# <a name="local-functions"></a><span data-ttu-id="c676e-101">Funzioni locali</span><span class="sxs-lookup"><span data-stu-id="c676e-101">Local functions</span></span>

<span data-ttu-id="c676e-102">Si estende C# per supportare la dichiarazione di funzioni nell'ambito del blocco.</span><span class="sxs-lookup"><span data-stu-id="c676e-102">We extend C# to support the declaration of functions in block scope.</span></span> <span data-ttu-id="c676e-103">Le funzioni locali possono usare (acquisire) variabili dall'ambito che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="c676e-103">Local functions may use (capture) variables from the enclosing scope.</span></span>

<span data-ttu-id="c676e-104">Il compilatore usa l'analisi di flusso per rilevare le variabili usate da una funzione locale prima di assegnarle un valore.</span><span class="sxs-lookup"><span data-stu-id="c676e-104">The compiler uses flow analysis to detect which variables a local function uses before assigning it a value.</span></span> <span data-ttu-id="c676e-105">Per ogni chiamata della funzione è necessario assegnare definitivamente tali variabili.</span><span class="sxs-lookup"><span data-stu-id="c676e-105">Every call of the function requires such variables to be definitely assigned.</span></span> <span data-ttu-id="c676e-106">Analogamente, il compilatore determina quali variabili vengono assegnate definitivamente al ritorno.</span><span class="sxs-lookup"><span data-stu-id="c676e-106">Similarly the compiler determines which variables are definitely assigned on return.</span></span> <span data-ttu-id="c676e-107">Tali variabili vengono considerate assegnate definitivamente dopo che la funzione locale è stata richiamata.</span><span class="sxs-lookup"><span data-stu-id="c676e-107">Such variables are considered definitely assigned after the local function is invoked.</span></span>

<span data-ttu-id="c676e-108">Le funzioni locali possono essere chiamate da un punto lessicale prima della relativa definizione.</span><span class="sxs-lookup"><span data-stu-id="c676e-108">Local functions may be called from a lexical point before its definition.</span></span> <span data-ttu-id="c676e-109">Le istruzioni di dichiarazione di funzione locale non generano un avviso quando non sono raggiungibili.</span><span class="sxs-lookup"><span data-stu-id="c676e-109">Local function declaration statements do not cause a warning when they are not reachable.</span></span>

<span data-ttu-id="c676e-110">TODO: _scrivere specifiche_</span><span class="sxs-lookup"><span data-stu-id="c676e-110">TODO: _WRITE SPEC_</span></span>

## <a name="syntax-grammar"></a><span data-ttu-id="c676e-111">Grammatica della sintassi</span><span class="sxs-lookup"><span data-stu-id="c676e-111">Syntax grammar</span></span>

<span data-ttu-id="c676e-112">Questa grammatica viene rappresentata come diff dalla grammatica delle specifiche corrente.</span><span class="sxs-lookup"><span data-stu-id="c676e-112">This grammar is represented as a diff from the current spec grammar.</span></span>

```diff
declaration-statement
    : local-variable-declaration ';'
    | local-constant-declaration ';'
+   | local-function-declaration
    ;

+local-function-declaration
+   : local-function-header local-function-body
+   ;

+local-function-header
+   : local-function-modifiers? return-type identifier type-parameter-list?
+       ( formal-parameter-list? ) type-parameter-constraints-clauses
+   ;

+local-function-modifiers
+   : (async | unsafe)
+   ;

+local-function-body
+   : block
+   | arrow-expression-body
+   ;
```

<span data-ttu-id="c676e-113">Le funzioni locali possono usare variabili definite nell'ambito di inclusione.</span><span class="sxs-lookup"><span data-stu-id="c676e-113">Local functions may use variables defined in the enclosing scope.</span></span> <span data-ttu-id="c676e-114">Per l'implementazione corrente è necessario che ogni variabile letta all'interno di una funzione locale venga assegnata in modo definitivo, come se venisse eseguita la funzione locale in corrispondenza del punto di definizione.</span><span class="sxs-lookup"><span data-stu-id="c676e-114">The current implementation requires that every variable read inside a local function be definitely assigned, as if executing the local function at its point of definition.</span></span> <span data-ttu-id="c676e-115">Inoltre, la definizione della funzione locale deve essere stata "eseguita" in qualsiasi punto di utilizzo.</span><span class="sxs-lookup"><span data-stu-id="c676e-115">Also, the local function definition must have been "executed" at any use point.</span></span>

<span data-ttu-id="c676e-116">Dopo aver sperimentato questo aspetto (ad esempio, non è possibile definire due funzioni locali ricorsivamente ricorsive), è stato modificato il modo in cui si vuole che l'assegnazione definita funzioni.</span><span class="sxs-lookup"><span data-stu-id="c676e-116">After experimenting with that a bit (for example, it is not possible to define two mutually recursive local functions), we've since revised how we want the definite assignment to work.</span></span> <span data-ttu-id="c676e-117">La revisione (non ancora implementata) consiste nel fare in modo che tutte le variabili locali lette in una funzione locale siano assegnate definitivamente a ogni chiamata della funzione locale.</span><span class="sxs-lookup"><span data-stu-id="c676e-117">The revision (not yet implemented) is that all local variables read in a local function must be definitely assigned at each invocation of the local function.</span></span> <span data-ttu-id="c676e-118">Si tratta di un'operazione più complessa rispetto al suono e c'è un gruppo di lavoro rimanente per renderlo funzionante.</span><span class="sxs-lookup"><span data-stu-id="c676e-118">That's actually more subtle than it sounds, and there is a bunch of work remaining to make it work.</span></span> <span data-ttu-id="c676e-119">Al termine, sarà possibile spostare le funzioni locali alla fine del blocco di inclusione.</span><span class="sxs-lookup"><span data-stu-id="c676e-119">Once it is done you'll be able to move your local functions to the end of its enclosing block.</span></span>

<span data-ttu-id="c676e-120">Le nuove regole di assegnazione definite sono incompatibili con l'inferenza del tipo restituito di una funzione locale, pertanto è probabile che sia in corso la rimozione del supporto per dedurre il tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="c676e-120">The new definite assignment rules are incompatible with inferring the return type of a local function, so we'll likely be removing support for inferring the return type.</span></span>

<span data-ttu-id="c676e-121">A meno che non si converta una funzione locale in un delegato, l'acquisizione viene eseguita in frame che sono tipi valore.</span><span class="sxs-lookup"><span data-stu-id="c676e-121">Unless you convert a local function to a delegate, capturing is done into frames that are value types.</span></span> <span data-ttu-id="c676e-122">Ciò significa che non si ottiene alcuna pressione GC dall'uso di funzioni locali con l'acquisizione.</span><span class="sxs-lookup"><span data-stu-id="c676e-122">That means you don't get any GC pressure from using local functions with capturing.</span></span>

### <a name="reachability"></a><span data-ttu-id="c676e-123">Raggiungibilità</span><span class="sxs-lookup"><span data-stu-id="c676e-123">Reachability</span></span>

<span data-ttu-id="c676e-124">Aggiungiamo alla specifica</span><span class="sxs-lookup"><span data-stu-id="c676e-124">We add to the spec</span></span>

> <span data-ttu-id="c676e-125">Il corpo di un'espressione lambda con corpo di istruzione o di una funzione locale è considerato raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="c676e-125">The body of a statement-bodied lambda expression or local function is considered reachable.</span></span>
