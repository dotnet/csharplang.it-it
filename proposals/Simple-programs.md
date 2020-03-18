---
ms.openlocfilehash: b9697fc1d772ba59ed3b1de339a5a3d4eb24b1bd
ms.sourcegitcommit: 36b028f4d6e88bd7d4a843c6d384d1b63cc73334
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "79485224"
---
# <a name="simple-programs"></a><span data-ttu-id="ce515-101">Programmi semplici</span><span class="sxs-lookup"><span data-stu-id="ce515-101">Simple programs</span></span>

* <span data-ttu-id="ce515-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="ce515-102">[x] Proposed</span></span>
* <span data-ttu-id="ce515-103">[x] prototipo: avviato</span><span class="sxs-lookup"><span data-stu-id="ce515-103">[x] Prototype: Started</span></span>
* <span data-ttu-id="ce515-104">[] Implementazione: non avviata</span><span class="sxs-lookup"><span data-stu-id="ce515-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="ce515-105">[] Specifica: non avviata</span><span class="sxs-lookup"><span data-stu-id="ce515-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="ce515-106">Summary</span><span class="sxs-lookup"><span data-stu-id="ce515-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="ce515-107">Consente di eseguire una sequenza di *istruzioni* immediatamente prima del *namespace_member_declaration*s di un *compilation_unit* , ad esempio un file di origine.</span><span class="sxs-lookup"><span data-stu-id="ce515-107">Allow a sequence of *statements* to occur right before the *namespace_member_declaration*s of a *compilation_unit* (i.e. source file).</span></span>

<span data-ttu-id="ce515-108">La semantica è che, se è presente una sequenza di *istruzioni* di questo tipo, viene emessa la seguente dichiarazione di tipo, modulo del nome del tipo effettivo e del nome del metodo:</span><span class="sxs-lookup"><span data-stu-id="ce515-108">The semantics are that if such a sequence of *statements* is present, the following type declaration, modulo the actual type name and the method name, would be emitted:</span></span>

``` c#
static class Program
{
    static async Task Main()
    {
        // statements
    }
}
```

<span data-ttu-id="ce515-109">Vedere anche https://github.com/dotnet/csharplang/issues/3117.</span><span class="sxs-lookup"><span data-stu-id="ce515-109">See also https://github.com/dotnet/csharplang/issues/3117.</span></span>

## <a name="motivation"></a><span data-ttu-id="ce515-110">Motivazione</span><span class="sxs-lookup"><span data-stu-id="ce515-110">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="ce515-111">Esiste una certa quantità di standard che circonda anche il più semplice dei programmi, a causa della necessità di un metodo esplicito `Main`.</span><span class="sxs-lookup"><span data-stu-id="ce515-111">There's a certain amount of boilerplate surrounding even the simplest of programs, because of the need for an explicit `Main` method.</span></span> <span data-ttu-id="ce515-112">Si tratta di una soluzione di apprendimento della lingua e di chiarezza del programma.</span><span class="sxs-lookup"><span data-stu-id="ce515-112">This seems to get in the way of language learning and program clarity.</span></span> <span data-ttu-id="ce515-113">L'obiettivo principale della funzionalità è quindi consentire C# l'uso di programmi privi di standard superflui per gli studenti e la chiarezza del codice.</span><span class="sxs-lookup"><span data-stu-id="ce515-113">The primary goal of the feature therefore is to allow C# programs without unnecessary boilerplate around them, for the sake of learners and the clarity of code.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="ce515-114">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="ce515-114">Detailed design</span></span>
[design]: #detailed-design

### <a name="syntax"></a><span data-ttu-id="ce515-115">Sintassi</span><span class="sxs-lookup"><span data-stu-id="ce515-115">Syntax</span></span>

<span data-ttu-id="ce515-116">L'unica sintassi aggiuntiva consente di eseguire una sequenza di *istruzioni*in un'unità di compilazione, immediatamente prima del *namespace_member_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="ce515-116">The only additional syntax is allowing a sequence of *statement*s in a compilation unit, just before the *namespace_member_declaration*s:</span></span>

``` antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? statement* namespace_member_declaration*
    ;
```

<span data-ttu-id="ce515-117">In tutti gli oggetti tranne uno *compilation_unit* l' *istruzione*s deve essere tutti dichiarazioni di funzioni locali.</span><span class="sxs-lookup"><span data-stu-id="ce515-117">In all but one *compilation_unit* the *statement*s must all be local function declarations.</span></span> 

<span data-ttu-id="ce515-118">Esempio:</span><span class="sxs-lookup"><span data-stu-id="ce515-118">Example:</span></span>

``` c#
// File 1 - any statements
if (args.Length == 0
    || !int.TryParse(args[0], out int n)
    || n < 0) return;
Console.WriteLine(Fib(n).curr);

// File 2 - only local functions
(int curr, int prev) Fib(int i)
{
    if (i == 0) return (1, 0);
    var (curr, prev) = Fib(i - 1);
    return (curr + prev, curr);
}
```

### <a name="semantics"></a><span data-ttu-id="ce515-119">Semantics</span><span class="sxs-lookup"><span data-stu-id="ce515-119">Semantics</span></span>

<span data-ttu-id="ce515-120">Se in qualsiasi unità di compilazione del programma sono presenti istruzioni di primo livello, il significato è come se fossero combinate nel corpo del blocco di un `Main` metodo di una classe `Program` nello spazio dei nomi globale, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="ce515-120">If any top-level statements are present in any compilation unit of the program, the meaning is as if they were combined in the block body of a `Main` method of a `Program` class in the global namespace, as follows:</span></span>

``` c#
static class Program
{
    static async Task Main()
    {
        // File 1 statements
        // File 2 local functions
        // ...
    }
}
```

<span data-ttu-id="ce515-121">Si noti che i nomi "Program" e "Main" vengono utilizzati solo a scopo illustrativo, i nomi effettivi utilizzati dal compilatore sono dipendenti dall'implementazione, né il tipo, né il metodo a cui è possibile fare riferimento dal codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="ce515-121">Note that the names "Program" and "Main" are used only for illustrations purposes, actual names used by compiler are implementation dependent and neither the type, nor the method can be referenced by name from source code.</span></span>

<span data-ttu-id="ce515-122">Il metodo viene designato come punto di ingresso del programma.</span><span class="sxs-lookup"><span data-stu-id="ce515-122">The method is designated as the entry point of the program.</span></span> <span data-ttu-id="ce515-123">I metodi dichiarati in modo esplicito che per convenzione potrebbero essere considerati come candidati di un punto di ingresso vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="ce515-123">Explicitly declared methods that by convention could be considered as an entry point candidates are ignored.</span></span> <span data-ttu-id="ce515-124">Quando si verifica un avviso, viene visualizzato un avviso.</span><span class="sxs-lookup"><span data-stu-id="ce515-124">A warning is reported when that happens.</span></span> <span data-ttu-id="ce515-125">È un errore specificare `-main:<type>` opzione del compilatore.</span><span class="sxs-lookup"><span data-stu-id="ce515-125">It is an error to specify `-main:<type>` compiler switch.</span></span>

<span data-ttu-id="ce515-126">Se una qualsiasi unità di compilazione dispone di istruzioni diverse dalle dichiarazioni di funzione locali, le istruzioni di tale unità di compilazione vengono eseguite per prime.</span><span class="sxs-lookup"><span data-stu-id="ce515-126">If any one compilation unit has statements other than local function declarations, statements from that compilation unit occur first.</span></span> <span data-ttu-id="ce515-127">In questo modo, è lecito per le funzioni locali in un file fare riferimento a variabili locali in un'altra.</span><span class="sxs-lookup"><span data-stu-id="ce515-127">This causes it to be legal for local functions in one file to reference local variables in another.</span></span> <span data-ttu-id="ce515-128">L'ordine dei contributi di istruzioni (che sarebbero tutte funzioni locali) da altre unità di compilazione non è definito.</span><span class="sxs-lookup"><span data-stu-id="ce515-128">The order of statement contributions (which would all be local functions) from other compilation units is undefined.</span></span>

<span data-ttu-id="ce515-129">Le operazioni asincrone sono consentite nelle istruzioni di primo livello fino al grado in cui sono consentite nelle istruzioni all'interno di un normale metodo del punto di ingresso asincrono.</span><span class="sxs-lookup"><span data-stu-id="ce515-129">Async operations are allowed in top-level statements to the degree they are allowed in statements within a regular async entry point method.</span></span> <span data-ttu-id="ce515-130">Tuttavia, non sono necessari, se `await` espressioni e altre operazioni asincrone vengono omesse, non viene generato alcun avviso.</span><span class="sxs-lookup"><span data-stu-id="ce515-130">However, they are not required, if `await` expressions and other async operations are omitted, no warning is produced.</span></span> <span data-ttu-id="ce515-131">La firma del metodo del punto di ingresso generato è invece equivalente a</span><span class="sxs-lookup"><span data-stu-id="ce515-131">Instead the signature of the generated entry point method is equivalent to</span></span> 
``` c#
    static void Main()
```

<span data-ttu-id="ce515-132">Nell'esempio precedente viene restituita la dichiarazione di metodo `$Main` seguente:</span><span class="sxs-lookup"><span data-stu-id="ce515-132">The example above would yield the following `$Main` method declaration:</span></span>

``` c#
static class $Program
{
    static void $Main()
    {
        // Statements from File 1
        if (args.Length == 0
            || !int.TryParse(args[0], out int n)
            || n < 0) return;
        Console.WriteLine(Fib(n).curr);
        
        // Local functions from File 2
        (int curr, int prev) Fib(int i)
        {
            if (i == 0) return (1, 0);
            var (curr, prev) = Fib(i - 1);
            return (curr + prev, curr);
        }
    }
}
```

<span data-ttu-id="ce515-133">Allo stesso tempo, un esempio simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="ce515-133">At the same time an example like this:</span></span>
``` c#
// File 1
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
```

<span data-ttu-id="ce515-134">produrrebbe:</span><span class="sxs-lookup"><span data-stu-id="ce515-134">would  yield:</span></span>
``` c#
static class $Program
{
    static async Task $Main()
    {
        // Statements from File 1
        await System.Threading.Tasks.Task.Delay(1000);
        System.Console.WriteLine("Hi!");
    }
}
```

### <a name="scope-of-top-level-local-variables-and-local-functions"></a><span data-ttu-id="ce515-135">Ambito delle variabili locali di primo livello e delle funzioni locali</span><span class="sxs-lookup"><span data-stu-id="ce515-135">Scope of top-level local variables and local functions</span></span>

<span data-ttu-id="ce515-136">Anche se le funzioni e le variabili locali di primo livello sono "sottoposte a incapsulamento" nel metodo del punto di ingresso generato, devono essere ancora nell'ambito dell'intero programma.</span><span class="sxs-lookup"><span data-stu-id="ce515-136">Even though top-level local variables and functions are "wrapped" into the generated entry point method, they should still be in scope throughout the program.</span></span>
<span data-ttu-id="ce515-137">Ai fini della valutazione con nome semplice, una volta raggiunto lo spazio dei nomi globale:</span><span class="sxs-lookup"><span data-stu-id="ce515-137">For the purpose of simple-name evaluation, once the global namespace is reached:</span></span>
- <span data-ttu-id="ce515-138">Viene innanzitutto eseguito un tentativo di valutare il nome all'interno del metodo del punto di ingresso generato e solo se il tentativo ha esito negativo</span><span class="sxs-lookup"><span data-stu-id="ce515-138">First, an attempt is made to evaluate the name within the generated entry point method and only if this attempt fails</span></span> 
- <span data-ttu-id="ce515-139">Viene eseguita la valutazione "regular" all'interno della dichiarazione dello spazio dei nomi globale.</span><span class="sxs-lookup"><span data-stu-id="ce515-139">The "regular" evaluation within the global namespace declaration is performed.</span></span> 

<span data-ttu-id="ce515-140">Questo potrebbe causare l'ombreggiatura dei nomi degli spazi dei nomi e dei tipi dichiarati all'interno dello spazio dei nomi globale, nonché dello shadowing dei nomi importati.</span><span class="sxs-lookup"><span data-stu-id="ce515-140">This could lead to name shadowing of namespaces and types declared within the global namespace as well as to shadowing of imported names.</span></span>

<span data-ttu-id="ce515-141">Se la valutazione del nome semplice viene eseguita al di fuori delle istruzioni di primo livello e la valutazione restituisce una variabile o una funzione locale di primo livello, questo dovrebbe causare un errore.</span><span class="sxs-lookup"><span data-stu-id="ce515-141">If the simple name evaluation occurs outside of the top-level statements and the evaluation yields a top-level local variable or function, that should lead to an error.</span></span>

<span data-ttu-id="ce515-142">In questo modo, proteggiamo la nostra futura capacità di gestire meglio le "funzioni di primo livello" (scenario 2 in https://github.com/dotnet/csharplang/issues/3117)e in grado di fornire una diagnostica utile agli utenti che si ritengono erroneamente supportati.</span><span class="sxs-lookup"><span data-stu-id="ce515-142">In this way we protect our future ability to better address "Top-level functions" (scenario 2 in https://github.com/dotnet/csharplang/issues/3117), and are able to give useful diagnostics to users who mistakenly believe them to be supported.</span></span>

