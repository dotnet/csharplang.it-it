---
ms.openlocfilehash: e0e18660cb3dbb6bfea77dd860ef7aa1388a3ca3
ms.sourcegitcommit: f22293533088b7527fa88d8916711b1d6a9ce5fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802419"
---
# <a name="top-level-statements"></a><span data-ttu-id="1597f-101">Istruzioni di primo livello</span><span class="sxs-lookup"><span data-stu-id="1597f-101">Top-level statements</span></span>

* <span data-ttu-id="1597f-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="1597f-102">[x] Proposed</span></span>
* <span data-ttu-id="1597f-103">[x] prototipo: avviato</span><span class="sxs-lookup"><span data-stu-id="1597f-103">[x] Prototype: Started</span></span>
* <span data-ttu-id="1597f-104">Implementazione di [x]: avviata</span><span class="sxs-lookup"><span data-stu-id="1597f-104">[x] Implementation: Started</span></span>
* <span data-ttu-id="1597f-105">[] Specifica: non avviata</span><span class="sxs-lookup"><span data-stu-id="1597f-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="1597f-106">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="1597f-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="1597f-107">Consente di eseguire una sequenza di *istruzioni* immediatamente prima del *namespace_member_declaration*s di un *compilation_unit* , ad esempio un file di origine.</span><span class="sxs-lookup"><span data-stu-id="1597f-107">Allow a sequence of *statements* to occur right before the *namespace_member_declaration*s of a *compilation_unit* (i.e. source file).</span></span>

<span data-ttu-id="1597f-108">La semantica è che, se è presente una sequenza di *istruzioni* di questo tipo, viene emessa la seguente dichiarazione di tipo, modulo del nome del tipo effettivo e del nome del metodo:</span><span class="sxs-lookup"><span data-stu-id="1597f-108">The semantics are that if such a sequence of *statements* is present, the following type declaration, modulo the actual type name and the method name, would be emitted:</span></span>

``` c#
static class Program
{
    static async Task Main(string[] args)
    {
        // statements
    }
}
```

<span data-ttu-id="1597f-109">Vedere anche https://github.com/dotnet/csharplang/issues/3117.</span><span class="sxs-lookup"><span data-stu-id="1597f-109">See also https://github.com/dotnet/csharplang/issues/3117.</span></span>

## <a name="motivation"></a><span data-ttu-id="1597f-110">Motivazione</span><span class="sxs-lookup"><span data-stu-id="1597f-110">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="1597f-111">Esiste una certa quantità di standard che circonda anche il più semplice dei programmi, a causa della necessità di un metodo esplicito `Main` .</span><span class="sxs-lookup"><span data-stu-id="1597f-111">There's a certain amount of boilerplate surrounding even the simplest of programs, because of the need for an explicit `Main` method.</span></span> <span data-ttu-id="1597f-112">Si tratta di una soluzione di apprendimento della lingua e di chiarezza del programma.</span><span class="sxs-lookup"><span data-stu-id="1597f-112">This seems to get in the way of language learning and program clarity.</span></span> <span data-ttu-id="1597f-113">L'obiettivo principale della funzionalità è quindi quello di consentire i programmi C# senza che siano necessari elementi standard, per gli studenti e la chiarezza del codice.</span><span class="sxs-lookup"><span data-stu-id="1597f-113">The primary goal of the feature therefore is to allow C# programs without unnecessary boilerplate around them, for the sake of learners and the clarity of code.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="1597f-114">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="1597f-114">Detailed design</span></span>
[design]: #detailed-design

### <a name="syntax"></a><span data-ttu-id="1597f-115">Sintassi</span><span class="sxs-lookup"><span data-stu-id="1597f-115">Syntax</span></span>

<span data-ttu-id="1597f-116">L'unica sintassi aggiuntiva consente di eseguire una sequenza di *istruzioni*in un'unità di compilazione, immediatamente prima del *namespace_member_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="1597f-116">The only additional syntax is allowing a sequence of *statement*s in a compilation unit, just before the *namespace_member_declaration*s:</span></span>

``` antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? statement* namespace_member_declaration*
    ;
```

<span data-ttu-id="1597f-117">Solo un *compilation_unit* può avere un' *istruzione*s.</span><span class="sxs-lookup"><span data-stu-id="1597f-117">Only one *compilation_unit* is allowed to have *statement*s.</span></span> 

<span data-ttu-id="1597f-118">Esempio:</span><span class="sxs-lookup"><span data-stu-id="1597f-118">Example:</span></span>

``` c#
if (args.Length == 0
    || !int.TryParse(args[0], out int n)
    || n < 0) return;
Console.WriteLine(Fib(n).curr);

(int curr, int prev) Fib(int i)
{
    if (i == 0) return (1, 0);
    var (curr, prev) = Fib(i - 1);
    return (curr + prev, curr);
}
```

### <a name="semantics"></a><span data-ttu-id="1597f-119">Semantica</span><span class="sxs-lookup"><span data-stu-id="1597f-119">Semantics</span></span>

<span data-ttu-id="1597f-120">Se in qualsiasi unità di compilazione del programma sono presenti istruzioni di primo livello, il significato è come se fossero combinate nel corpo del blocco di un `Main` metodo di una `Program` classe nello spazio dei nomi globale, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="1597f-120">If any top-level statements are present in any compilation unit of the program, the meaning is as if they were combined in the block body of a `Main` method of a `Program` class in the global namespace, as follows:</span></span>

``` c#
static class Program
{
    static async Task Main(string[] args)
    {
        // statements
    }
}
```

<span data-ttu-id="1597f-121">Si noti che i nomi "Program" e "Main" vengono utilizzati solo a scopo illustrativo, i nomi effettivi utilizzati dal compilatore sono dipendenti dall'implementazione, né il tipo, né il metodo a cui è possibile fare riferimento dal codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="1597f-121">Note that the names "Program" and "Main" are used only for illustrations purposes, actual names used by compiler are implementation dependent and neither the type, nor the method can be referenced by name from source code.</span></span>

<span data-ttu-id="1597f-122">Il metodo viene designato come punto di ingresso del programma.</span><span class="sxs-lookup"><span data-stu-id="1597f-122">The method is designated as the entry point of the program.</span></span> <span data-ttu-id="1597f-123">I metodi dichiarati in modo esplicito che per convenzione potrebbero essere considerati come candidati di un punto di ingresso vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="1597f-123">Explicitly declared methods that by convention could be considered as an entry point candidates are ignored.</span></span> <span data-ttu-id="1597f-124">Quando si verifica un avviso, viene visualizzato un avviso.</span><span class="sxs-lookup"><span data-stu-id="1597f-124">A warning is reported when that happens.</span></span> <span data-ttu-id="1597f-125">È un errore specificare l' `-main:<type>` opzione del compilatore quando sono presenti istruzioni di primo livello.</span><span class="sxs-lookup"><span data-stu-id="1597f-125">It is an error to specify `-main:<type>` compiler switch when there are top-level statements.</span></span>

<span data-ttu-id="1597f-126">Il metodo del punto di ingresso ha sempre un parametro formale, ```string[] args``` .</span><span class="sxs-lookup"><span data-stu-id="1597f-126">The entry point method always has one formal parameter, ```string[] args```.</span></span> <span data-ttu-id="1597f-127">L'ambiente di esecuzione crea e passa un ```string[]``` argomento contenente gli argomenti della riga di comando specificati al momento dell'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="1597f-127">The execution environment creates and passes a ```string[]``` argument containing the command-line arguments that were specified when the application was started.</span></span> <span data-ttu-id="1597f-128">L' ```string[]``` argomento non è mai null, ma può avere una lunghezza pari a zero se non è stato specificato alcun argomento della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="1597f-128">The ```string[]``` argument is never null, but it may have a length of zero if no command-line arguments were specified.</span></span> <span data-ttu-id="1597f-129">Il parametro ' args ' si trova nell'ambito all'interno delle istruzioni di primo livello e non è incluso nell'ambito.</span><span class="sxs-lookup"><span data-stu-id="1597f-129">The ‘args’ parameter is in scope  within top-level statements and is not in scope outside of them.</span></span> <span data-ttu-id="1597f-130">Si applicano le regole di conflitto/ombreggiatura dei nomi regolari.</span><span class="sxs-lookup"><span data-stu-id="1597f-130">Regular name conflict/shadowing rules apply.</span></span>

<span data-ttu-id="1597f-131">Le operazioni asincrone sono consentite nelle istruzioni di primo livello fino al grado in cui sono consentite nelle istruzioni all'interno di un normale metodo del punto di ingresso asincrono.</span><span class="sxs-lookup"><span data-stu-id="1597f-131">Async operations are allowed in top-level statements to the degree they are allowed in statements within a regular async entry point method.</span></span> <span data-ttu-id="1597f-132">Tuttavia, non sono necessari, se `await` le espressioni e altre operazioni asincrone vengono omesse, non viene generato alcun avviso.</span><span class="sxs-lookup"><span data-stu-id="1597f-132">However, they are not required, if `await` expressions and other async operations are omitted, no warning is produced.</span></span>

<span data-ttu-id="1597f-133">La firma del metodo del punto di ingresso generato viene determinata in base alle operazioni utilizzate dalle istruzioni di primo livello, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="1597f-133">The signature of the generated entry point method is determined based on operations used by the top level statements as follows:</span></span>
| <span data-ttu-id="1597f-134">**Async-operations\Return-with-expression**</span><span class="sxs-lookup"><span data-stu-id="1597f-134">**Async-operations\Return-with-expression**</span></span> | <span data-ttu-id="1597f-135">**Presente**</span><span class="sxs-lookup"><span data-stu-id="1597f-135">**Present**</span></span> | <span data-ttu-id="1597f-136">**Assente**</span><span class="sxs-lookup"><span data-stu-id="1597f-136">**Absent**</span></span> |
|----------------------------------------|-------------|-------------|
| <span data-ttu-id="1597f-137">**Presente**</span><span class="sxs-lookup"><span data-stu-id="1597f-137">**Present**</span></span> | ```static Task<int> Main(string[] args)```| ```static Task Main(string[] args)``` |
| <span data-ttu-id="1597f-138">**Assente**</span><span class="sxs-lookup"><span data-stu-id="1597f-138">**Absent**</span></span>  | ```static int Main(string[] args)``` | ```static void Main(string[] args)``` |

<span data-ttu-id="1597f-139">Nell'esempio precedente viene restituita la `$Main` dichiarazione di metodo seguente:</span><span class="sxs-lookup"><span data-stu-id="1597f-139">The example above would yield the following `$Main` method declaration:</span></span>

``` c#
static class $Program
{
    static void $Main(string[] args)
    {
        if (args.Length == 0
            || !int.TryParse(args[0], out int n)
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

<span data-ttu-id="1597f-140">Allo stesso tempo, un esempio simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="1597f-140">At the same time an example like this:</span></span>
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
```

<span data-ttu-id="1597f-141">produrrebbe:</span><span class="sxs-lookup"><span data-stu-id="1597f-141">would  yield:</span></span>
``` c#
static class $Program
{
    static async Task $Main(string[] args)
    {
        await System.Threading.Tasks.Task.Delay(1000);
        System.Console.WriteLine("Hi!");
    }
}
```

<span data-ttu-id="1597f-142">Un esempio simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="1597f-142">An example like this:</span></span>
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
return 0;
```

<span data-ttu-id="1597f-143">produrrebbe:</span><span class="sxs-lookup"><span data-stu-id="1597f-143">would  yield:</span></span>
``` c#
static class $Program
{
    static async Task<int> $Main(string[] args)
    {
        await System.Threading.Tasks.Task.Delay(1000);
        System.Console.WriteLine("Hi!");
        return 0;
    }
}
```

<span data-ttu-id="1597f-144">E un esempio simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="1597f-144">And an example like this:</span></span>
``` c#
System.Console.WriteLine("Hi!");
return 2;
```

<span data-ttu-id="1597f-145">produrrebbe:</span><span class="sxs-lookup"><span data-stu-id="1597f-145">would  yield:</span></span>
``` c#
static class $Program
{
    static int $Main(string[] args)
    {
        System.Console.WriteLine("Hi!");
        return 2;
    }
}
```

### <a name="scope-of-top-level-local-variables-and-local-functions"></a><span data-ttu-id="1597f-146">Ambito delle variabili locali di primo livello e delle funzioni locali</span><span class="sxs-lookup"><span data-stu-id="1597f-146">Scope of top-level local variables and local functions</span></span>

<span data-ttu-id="1597f-147">Anche se le funzioni e le variabili locali di primo livello sono "incapsulate" nel metodo del punto di ingresso generato, devono ancora trovarsi nell'ambito del programma in ogni unità di compilazione.</span><span class="sxs-lookup"><span data-stu-id="1597f-147">Even though top-level local variables and functions are "wrapped" into the generated entry point method, they should still be in scope throughout the program in every compilation unit.</span></span>
<span data-ttu-id="1597f-148">Ai fini della valutazione con nome semplice, una volta raggiunto lo spazio dei nomi globale:</span><span class="sxs-lookup"><span data-stu-id="1597f-148">For the purpose of simple-name evaluation, once the global namespace is reached:</span></span>
- <span data-ttu-id="1597f-149">Viene innanzitutto eseguito un tentativo di valutare il nome all'interno del metodo del punto di ingresso generato e solo se il tentativo ha esito negativo</span><span class="sxs-lookup"><span data-stu-id="1597f-149">First, an attempt is made to evaluate the name within the generated entry point method and only if this attempt fails</span></span> 
- <span data-ttu-id="1597f-150">Viene eseguita la valutazione "regular" all'interno della dichiarazione dello spazio dei nomi globale.</span><span class="sxs-lookup"><span data-stu-id="1597f-150">The "regular" evaluation within the global namespace declaration is performed.</span></span> 

<span data-ttu-id="1597f-151">Questo potrebbe causare l'ombreggiatura dei nomi degli spazi dei nomi e dei tipi dichiarati all'interno dello spazio dei nomi globale, nonché dello shadowing dei nomi importati.</span><span class="sxs-lookup"><span data-stu-id="1597f-151">This could lead to name shadowing of namespaces and types declared within the global namespace as well as to shadowing of imported names.</span></span>

<span data-ttu-id="1597f-152">Se la valutazione del nome semplice viene eseguita al di fuori delle istruzioni di primo livello e la valutazione restituisce una variabile o una funzione locale di primo livello, questo dovrebbe causare un errore.</span><span class="sxs-lookup"><span data-stu-id="1597f-152">If the simple name evaluation occurs outside of the top-level statements and the evaluation yields a top-level local variable or function, that should lead to an error.</span></span>

<span data-ttu-id="1597f-153">In questo modo, proteggiamo la nostra futura capacità di gestire meglio le "funzioni di primo livello" (scenario 2 in https://github.com/dotnet/csharplang/issues/3117) e sono in grado di fornire una diagnostica utile agli utenti che si ritengono erroneamente supportati.</span><span class="sxs-lookup"><span data-stu-id="1597f-153">In this way we protect our future ability to better address "Top-level functions" (scenario 2 in https://github.com/dotnet/csharplang/issues/3117), and are able to give useful diagnostics to users who mistakenly believe them to be supported.</span></span>

