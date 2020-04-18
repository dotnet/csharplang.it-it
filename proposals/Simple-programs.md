---
ms.openlocfilehash: 0e2b4b70e0555145f54be2cc57da9f50f5a43548
ms.sourcegitcommit: 1ecebc412f073f3959395f33b70666c8ede88f3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81612103"
---
# <a name="simple-programs"></a><span data-ttu-id="f0e96-101">Programmi semplici</span><span class="sxs-lookup"><span data-stu-id="f0e96-101">Simple programs</span></span>

* <span data-ttu-id="f0e96-102">[x] Proposto</span><span class="sxs-lookup"><span data-stu-id="f0e96-102">[x] Proposed</span></span>
* <span data-ttu-id="f0e96-103">[x] Prototipo: Avviato</span><span class="sxs-lookup"><span data-stu-id="f0e96-103">[x] Prototype: Started</span></span>
* <span data-ttu-id="f0e96-104">[x] Implementazione: Avviata</span><span class="sxs-lookup"><span data-stu-id="f0e96-104">[x] Implementation: Started</span></span>
* <span data-ttu-id="f0e96-105">[ ] Specifica: Non avviato</span><span class="sxs-lookup"><span data-stu-id="f0e96-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="f0e96-106">Summary</span><span class="sxs-lookup"><span data-stu-id="f0e96-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="f0e96-107">Consentire la verifica di una sequenza di *istruzioni* subito prima delle *namespace_member_declaration*di un *compilation_unit* (ad esempio un file di origine).</span><span class="sxs-lookup"><span data-stu-id="f0e96-107">Allow a sequence of *statements* to occur right before the *namespace_member_declaration*s of a *compilation_unit* (i.e. source file).</span></span>

<span data-ttu-id="f0e96-108">La semantica è che se tale sequenza di *istruzioni* è presente, la seguente dichiarazione di tipo, modulo il nome effettivo del tipo e il nome del metodo, verrebbe generato:</span><span class="sxs-lookup"><span data-stu-id="f0e96-108">The semantics are that if such a sequence of *statements* is present, the following type declaration, modulo the actual type name and the method name, would be emitted:</span></span>

``` c#
static class Program
{
    static async Task Main()
    {
        // statements
    }
}
```

<span data-ttu-id="f0e96-109">Vedere anche https://github.com/dotnet/csharplang/issues/3117.</span><span class="sxs-lookup"><span data-stu-id="f0e96-109">See also https://github.com/dotnet/csharplang/issues/3117.</span></span>

## <a name="motivation"></a><span data-ttu-id="f0e96-110">Motivazione</span><span class="sxs-lookup"><span data-stu-id="f0e96-110">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="f0e96-111">C'è una certa quantità di boilerplate che circonda anche il `Main` più semplice dei programmi, a causa della necessità di un metodo esplicito.</span><span class="sxs-lookup"><span data-stu-id="f0e96-111">There's a certain amount of boilerplate surrounding even the simplest of programs, because of the need for an explicit `Main` method.</span></span> <span data-ttu-id="f0e96-112">Questo sembra intralciare l'apprendimento delle lingue e la chiarezza del programma.</span><span class="sxs-lookup"><span data-stu-id="f0e96-112">This seems to get in the way of language learning and program clarity.</span></span> <span data-ttu-id="f0e96-113">L'obiettivo principale della funzionalità è quindi quello di consentire i programmi c'è senza inutili boilerplate intorno a loro, per il bene degli studenti e la chiarezza del codice.</span><span class="sxs-lookup"><span data-stu-id="f0e96-113">The primary goal of the feature therefore is to allow C# programs without unnecessary boilerplate around them, for the sake of learners and the clarity of code.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="f0e96-114">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="f0e96-114">Detailed design</span></span>
[design]: #detailed-design

### <a name="syntax"></a><span data-ttu-id="f0e96-115">Sintassi</span><span class="sxs-lookup"><span data-stu-id="f0e96-115">Syntax</span></span>

<span data-ttu-id="f0e96-116">L'unica sintassi aggiuntiva è consentire una sequenza di *istruzioni*s in un'unità di compilazione, appena prima del *namespace_member_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="f0e96-116">The only additional syntax is allowing a sequence of *statement*s in a compilation unit, just before the *namespace_member_declaration*s:</span></span>

``` antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? statement* namespace_member_declaration*
    ;
```

<span data-ttu-id="f0e96-117">Solo un *compilation_unit* può avere *l'istruzione*s.</span><span class="sxs-lookup"><span data-stu-id="f0e96-117">Only one *compilation_unit* is allowed to have *statement*s.</span></span> 

<span data-ttu-id="f0e96-118">Esempio:</span><span class="sxs-lookup"><span data-stu-id="f0e96-118">Example:</span></span>

``` c#
if (System.Environment.CommandLine.Length == 0
    || !int.TryParse(System.Environment.CommandLine, out int n)
    || n < 0) return;
Console.WriteLine(Fib(n).curr);

(int curr, int prev) Fib(int i)
{
    if (i == 0) return (1, 0);
    var (curr, prev) = Fib(i - 1);
    return (curr + prev, curr);
}
```

### <a name="semantics"></a><span data-ttu-id="f0e96-119">Semantics</span><span class="sxs-lookup"><span data-stu-id="f0e96-119">Semantics</span></span>

<span data-ttu-id="f0e96-120">Se sono presenti istruzioni di primo livello in qualsiasi unità di compilazione del programma, il `Main` significato `Program` è come se fossero combinate nel corpo del blocco di un metodo di una classe nello spazio dei nomi globale, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="f0e96-120">If any top-level statements are present in any compilation unit of the program, the meaning is as if they were combined in the block body of a `Main` method of a `Program` class in the global namespace, as follows:</span></span>

``` c#
static class Program
{
    static async Task Main()
    {
        // statements
    }
}
```

<span data-ttu-id="f0e96-121">Si noti che i nomi "Programma" e "Main" vengono utilizzati solo a scopo illustrativo, i nomi effettivi utilizzati dal compilatore dipendono dall'implementazione e né è possibile fare riferimento al tipo né al metodo per nome dal codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="f0e96-121">Note that the names "Program" and "Main" are used only for illustrations purposes, actual names used by compiler are implementation dependent and neither the type, nor the method can be referenced by name from source code.</span></span>

<span data-ttu-id="f0e96-122">Il metodo viene designato come punto di ingresso del programma.</span><span class="sxs-lookup"><span data-stu-id="f0e96-122">The method is designated as the entry point of the program.</span></span> <span data-ttu-id="f0e96-123">I metodi dichiarati in modo esplicito che per convenzione potrebbero essere considerati come candidati di un punto di ingresso vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="f0e96-123">Explicitly declared methods that by convention could be considered as an entry point candidates are ignored.</span></span> <span data-ttu-id="f0e96-124">Un avviso viene segnalato quando ciò si verifica.</span><span class="sxs-lookup"><span data-stu-id="f0e96-124">A warning is reported when that happens.</span></span> <span data-ttu-id="f0e96-125">È un errore `-main:<type>` specificare l'opzione del compilatore quando sono presenti istruzioni di primo livello.</span><span class="sxs-lookup"><span data-stu-id="f0e96-125">It is an error to specify `-main:<type>` compiler switch when there are top-level statements.</span></span>

<span data-ttu-id="f0e96-126">Le operazioni asincrone sono consentite nelle istruzioni di primo livello al grado in cui sono consentite nelle istruzioni all'interno di un normale metodo del punto di ingresso asincrono.</span><span class="sxs-lookup"><span data-stu-id="f0e96-126">Async operations are allowed in top-level statements to the degree they are allowed in statements within a regular async entry point method.</span></span> <span data-ttu-id="f0e96-127">Tuttavia, non sono `await` necessari, se le espressioni e altre operazioni asincrone vengono omesse, non viene generato alcun avviso.</span><span class="sxs-lookup"><span data-stu-id="f0e96-127">However, they are not required, if `await` expressions and other async operations are omitted, no warning is produced.</span></span>

<span data-ttu-id="f0e96-128">La firma del metodo del punto di ingresso generato viene determinata in base alle operazioni utilizzate dalle istruzioni di primo livello come segue:</span><span class="sxs-lookup"><span data-stu-id="f0e96-128">The signature of the generated entry point method is determined based on operations used by the top level statements as follows:</span></span>
<span data-ttu-id="f0e96-129">**Async-operations - Return-with-expression (Operazioni asincrone- Return-with-expression)**</span><span class="sxs-lookup"><span data-stu-id="f0e96-129">**Async-operations\Return-with-expression**</span></span> | <span data-ttu-id="f0e96-130">**Presente**</span><span class="sxs-lookup"><span data-stu-id="f0e96-130">**Present**</span></span> | <span data-ttu-id="f0e96-131">**Assente**</span><span class="sxs-lookup"><span data-stu-id="f0e96-131">**Absent**</span></span>
----------------------------------------| -------------|-------------
<span data-ttu-id="f0e96-132">**Presente**</span><span class="sxs-lookup"><span data-stu-id="f0e96-132">**Present**</span></span> | ```static Task<int> Main()```| ```static Task Main()```
<span data-ttu-id="f0e96-133">**Assente**</span><span class="sxs-lookup"><span data-stu-id="f0e96-133">**Absent**</span></span>  | ```static int Main()``` | ```static void Main()```

<span data-ttu-id="f0e96-134">L'esempio precedente produrrebbe la seguente dichiarazione di metodo:The example above would yield the following `$Main` method declaration:</span><span class="sxs-lookup"><span data-stu-id="f0e96-134">The example above would yield the following `$Main` method declaration:</span></span>

``` c#
static class $Program
{
    static void $Main()
    {
        if (System.Environment.CommandLine.Length == 0
            || !int.TryParse(System.Environment.CommandLine, out int n)
            || n < 0) return;
        Console.WriteLine(Fib(n).curr);
        
        (int curr, int prev) Fib(int i)
        {
            if (i == 0) return (1, 0);
            var (curr, prev) = Fib(i - 1);
            return (curr + prev, curr);
        }
    }
}
```

<span data-ttu-id="f0e96-135">Allo stesso tempo, un esempio come questo:</span><span class="sxs-lookup"><span data-stu-id="f0e96-135">At the same time an example like this:</span></span>
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
```

<span data-ttu-id="f0e96-136">produrrebbe:</span><span class="sxs-lookup"><span data-stu-id="f0e96-136">would  yield:</span></span>
``` c#
static class $Program
{
    static async Task $Main()
    {
        await System.Threading.Tasks.Task.Delay(1000);
        System.Console.WriteLine("Hi!");
    }
}
```

<span data-ttu-id="f0e96-137">Un esempio come questo:</span><span class="sxs-lookup"><span data-stu-id="f0e96-137">An example like this:</span></span>
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
return 0;
```

<span data-ttu-id="f0e96-138">produrrebbe:</span><span class="sxs-lookup"><span data-stu-id="f0e96-138">would  yield:</span></span>
``` c#
static class $Program
{
    static async Task<int> $Main()
    {
        await System.Threading.Tasks.Task.Delay(1000);
        System.Console.WriteLine("Hi!");
        return 0;
    }
}
```

<span data-ttu-id="f0e96-139">E un esempio come questo:</span><span class="sxs-lookup"><span data-stu-id="f0e96-139">And an example like this:</span></span>
``` c#
System.Console.WriteLine("Hi!");
return 2;
```

<span data-ttu-id="f0e96-140">produrrebbe:</span><span class="sxs-lookup"><span data-stu-id="f0e96-140">would  yield:</span></span>
``` c#
static class $Program
{
    static int $Main()
    {
        System.Console.WriteLine("Hi!");
        return 2;
    }
}
```

### <a name="scope-of-top-level-local-variables-and-local-functions"></a><span data-ttu-id="f0e96-141">Ambito delle variabili locali di primo livello e delle funzioni locali</span><span class="sxs-lookup"><span data-stu-id="f0e96-141">Scope of top-level local variables and local functions</span></span>

<span data-ttu-id="f0e96-142">Anche se le funzioni e le variabili locali di primo livello sono "incapsulate" nel metodo del punto di ingresso generato, devono comunque essere nell'ambito in tutto il programma in ogni unità di compilazione.</span><span class="sxs-lookup"><span data-stu-id="f0e96-142">Even though top-level local variables and functions are "wrapped" into the generated entry point method, they should still be in scope throughout the program in every compilation unit.</span></span>
<span data-ttu-id="f0e96-143">Ai fini della valutazione del nome semplice, una volta raggiunto lo spazio dei nomi globale:</span><span class="sxs-lookup"><span data-stu-id="f0e96-143">For the purpose of simple-name evaluation, once the global namespace is reached:</span></span>
- <span data-ttu-id="f0e96-144">In primo luogo, viene effettuato un tentativo di valutare il nome all'interno del metodo del punto di ingresso generato e solo se il tentativo non riesce</span><span class="sxs-lookup"><span data-stu-id="f0e96-144">First, an attempt is made to evaluate the name within the generated entry point method and only if this attempt fails</span></span> 
- <span data-ttu-id="f0e96-145">Viene eseguita la valutazione "regolare" all'interno della dichiarazione dello spazio dei nomi globale.</span><span class="sxs-lookup"><span data-stu-id="f0e96-145">The "regular" evaluation within the global namespace declaration is performed.</span></span> 

<span data-ttu-id="f0e96-146">Ciò potrebbe portare allo shadowing dei nomi degli spazi dei nomi e dei tipi dichiarati all'interno dello spazio dei nomi globale, nonché allo shadowing dei nomi importati.</span><span class="sxs-lookup"><span data-stu-id="f0e96-146">This could lead to name shadowing of namespaces and types declared within the global namespace as well as to shadowing of imported names.</span></span>

<span data-ttu-id="f0e96-147">Se la valutazione del nome semplice si verifica al di fuori delle istruzioni di primo livello e la valutazione produce una variabile locale di primo livello o una funzione, ciò dovrebbe causare un errore.</span><span class="sxs-lookup"><span data-stu-id="f0e96-147">If the simple name evaluation occurs outside of the top-level statements and the evaluation yields a top-level local variable or function, that should lead to an error.</span></span>

<span data-ttu-id="f0e96-148">In questo modo proteggiamo la nostra futura capacità di affrontare https://github.com/dotnet/csharplang/issues/3117)meglio le "funzioni di primo livello" (scenario 2 in , e siamo in grado di dare diagnostica utile agli utenti che erroneamente credono che siano supportati.</span><span class="sxs-lookup"><span data-stu-id="f0e96-148">In this way we protect our future ability to better address "Top-level functions" (scenario 2 in https://github.com/dotnet/csharplang/issues/3117), and are able to give useful diagnostics to users who mistakenly believe them to be supported.</span></span>

