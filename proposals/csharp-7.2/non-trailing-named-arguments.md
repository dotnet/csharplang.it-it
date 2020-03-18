---
ms.openlocfilehash: ac2b233eb703b5eea3bd2dfdbeeadd7494b0c695
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484671"
---
# <a name="non-trailing-named-arguments"></a><span data-ttu-id="236e1-101">Argomenti denominati non finali</span><span class="sxs-lookup"><span data-stu-id="236e1-101">Non-trailing named arguments</span></span>

## <a name="summary"></a><span data-ttu-id="236e1-102">Summary</span><span class="sxs-lookup"><span data-stu-id="236e1-102">Summary</span></span>
[summary]: #summary
<span data-ttu-id="236e1-103">Consente di utilizzare gli argomenti denominati nella posizione non finale, purché vengano utilizzati nella posizione corretta.</span><span class="sxs-lookup"><span data-stu-id="236e1-103">Allow named arguments to be used in non-trailing position, as long as they are used in their correct position.</span></span> <span data-ttu-id="236e1-104">Ad esempio: `DoSomething(isEmployed:true, name, age);`.</span><span class="sxs-lookup"><span data-stu-id="236e1-104">For example: `DoSomething(isEmployed:true, name, age);`.</span></span>

## <a name="motivation"></a><span data-ttu-id="236e1-105">Motivazione</span><span class="sxs-lookup"><span data-stu-id="236e1-105">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="236e1-106">La motivazione principale è evitare di digitare informazioni ridondanti.</span><span class="sxs-lookup"><span data-stu-id="236e1-106">The main motivation is to avoid typing redundant information.</span></span> <span data-ttu-id="236e1-107">È comune denominare un argomento che è un valore letterale (ad esempio `null`, `true`) allo scopo di chiarire il codice, anziché passare gli argomenti non ordinati.</span><span class="sxs-lookup"><span data-stu-id="236e1-107">It is common to name an argument that is a literal (such as `null`, `true`) for the purpose of clarifying the code, rather than of passing arguments out-of-order.</span></span>
<span data-ttu-id="236e1-108">Attualmente non consentito (`CS1738`), a meno che non vengano denominati anche tutti gli argomenti seguenti.</span><span class="sxs-lookup"><span data-stu-id="236e1-108">That is currently disallowed (`CS1738`) unless all the following arguments are also named.</span></span>

```csharp
DoSomething(isEmployed:true, name, age); // currently disallowed, even though all arguments are in position
// CS1738 "Named argument specifications must appear after all fixed arguments have been specified"
```

<span data-ttu-id="236e1-109">Altri esempi:</span><span class="sxs-lookup"><span data-stu-id="236e1-109">Some additional examples:</span></span>
```csharp
public void DoSomething(bool isEmployed, string personName, int personAge) { ... }

DoSomething(isEmployed:true, name, age); // currently CS1738, but would become legal
DoSomething(true, personName:name, age); // currently CS1738, but would become legal
DoSomething(name, isEmployed:true, age); // remains illegal
DoSomething(name, age, isEmployed:true); // remains illegal
DoSomething(true, personAge:age, personName:name); // already legal
```

<span data-ttu-id="236e1-110">Funziona anche con params:</span><span class="sxs-lookup"><span data-stu-id="236e1-110">This would also work with params:</span></span>
```csharp
public class Task
{
    public static Task When(TaskStatus all, TaskStatus any, params Task[] tasks);
}
Task.When(all: TaskStatus.RanToCompletion, any: TaskStatus.Faulted, task1, task2)
```

## <a name="detailed-design"></a><span data-ttu-id="236e1-111">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="236e1-111">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="236e1-112">In § 7.5.1 (elenchi di argomenti), la specifica attualmente indica:</span><span class="sxs-lookup"><span data-stu-id="236e1-112">In §7.5.1 (Argument lists), the spec currently says:</span></span>
> <span data-ttu-id="236e1-113">Un *argomento con* un *nome di argomento* viene definito argomento __denominato__, mentre un *argomento senza un* nome di *argomento* è un __argomento posizionale__.</span><span class="sxs-lookup"><span data-stu-id="236e1-113">An *argument* with an *argument-name* is referred to as a __named argument__, whereas an *argument* without an *argument-name* is a __positional argument__.</span></span> <span data-ttu-id="236e1-114">Per visualizzare un argomento posizionale dopo un argomento denominato in un *elenco di argomenti*, è un errore.</span><span class="sxs-lookup"><span data-stu-id="236e1-114">It is an error for a positional argument to appear after a named argument in an *argument-list*.</span></span>

<span data-ttu-id="236e1-115">La proposta consiste nel rimuovere questo errore e aggiornare le regole per trovare il parametro corrispondente per un argomento (§ 7.5.1.1):</span><span class="sxs-lookup"><span data-stu-id="236e1-115">The proposal is to remove this error and update the rules for finding the corresponding parameter for an argument (§7.5.1.1):</span></span>

<span data-ttu-id="236e1-116">Argomenti nell'elenco di argomenti di costruttori di istanza, metodi, indicizzatori e delegati:</span><span class="sxs-lookup"><span data-stu-id="236e1-116">Arguments in the argument-list of instance constructors, methods, indexers and delegates:</span></span>
- <span data-ttu-id="236e1-117">[regole esistenti]</span><span class="sxs-lookup"><span data-stu-id="236e1-117">[existing rules]</span></span>
- <span data-ttu-id="236e1-118">Un argomento senza nome corrisponde a nessun parametro se è successivo a un argomento denominato out-of-position o a un argomento denominato params.</span><span class="sxs-lookup"><span data-stu-id="236e1-118">An unnamed argument corresponds to no parameter when it is after an out-of-position named argument or a named params argument.</span></span>

<span data-ttu-id="236e1-119">In particolare, questa operazione impedisce la chiamata di `void M(bool a = true, bool b = true, bool c = true, );` con `M(c: false, valueB);`.</span><span class="sxs-lookup"><span data-stu-id="236e1-119">In particular, this prevents invoking `void M(bool a = true, bool b = true, bool c = true, );` with `M(c: false, valueB);`.</span></span> <span data-ttu-id="236e1-120">Il primo argomento viene utilizzato fuori posizione (l'argomento viene utilizzato nella prima posizione, ma il parametro denominato "c" si trova nella terza posizione), pertanto è necessario denominare gli argomenti seguenti.</span><span class="sxs-lookup"><span data-stu-id="236e1-120">The first argument is used out-of-position (the argument is used in first position, but the parameter named "c" is in third position), so the following arguments should be named.</span></span>

<span data-ttu-id="236e1-121">In altre parole, gli argomenti denominati non finali sono consentiti solo quando il nome e la posizione determinano la ricerca dello stesso parametro corrispondente.</span><span class="sxs-lookup"><span data-stu-id="236e1-121">In other words, non-trailing named arguments are only allowed when the name and the position result in finding the same corresponding parameter.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="236e1-122">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="236e1-122">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="236e1-123">Questa proposta aggrava le sottigliezze esistenti con argomenti denominati nella risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="236e1-123">This proposal exacerbates existing subtleties with named arguments in overload resolution.</span></span> <span data-ttu-id="236e1-124">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="236e1-124">For instance:</span></span>

```csharp
void M(int x, int y) { }
void M<T>(T y, int x) { }

void M2()
{
    M(3, 4);
    M(y: 3, x: 4); // Invokes M(int, int)
    M(y: 3, 4); // Invokes M<T>(T, int)
}
```

<span data-ttu-id="236e1-125">È possibile ottenere questa situazione oggi cambiando i parametri:</span><span class="sxs-lookup"><span data-stu-id="236e1-125">You could get this situation today by swapping the parameters:</span></span>

```csharp
void M(int y, int x) { }
void M<T>(int x, T y) { }

void M2()
{
    M(3, 4);
    M(x: 3, y: 4); // Invokes M(int, int)
    M(3, y: 4); // Invokes M<T>(int, T)
}
```

<span data-ttu-id="236e1-126">Analogamente, se si dispone di due metodi `void M(int a, int b)` e `void M(int x, string y)`, il `M(x: 1, 2)` di chiamata errato produrrà una diagnostica basata sul secondo overload ("Impossibile convertire da' int ' a' String '").</span><span class="sxs-lookup"><span data-stu-id="236e1-126">Similarly, if you have two methods `void M(int a, int b)` and `void M(int x, string y)`, the mistaken invocation `M(x: 1, 2)` will produce a diagnostic based on the second overload ("cannot convert from 'int' to 'string'").</span></span> <span data-ttu-id="236e1-127">Questo problema esiste già quando l'argomento denominato viene utilizzato in una posizione finale.</span><span class="sxs-lookup"><span data-stu-id="236e1-127">This problem already exists when the named argument is used in a trailing position.</span></span>

## <a name="alternatives"></a><span data-ttu-id="236e1-128">Alternativi</span><span class="sxs-lookup"><span data-stu-id="236e1-128">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="236e1-129">Ci sono due alternative da considerare:</span><span class="sxs-lookup"><span data-stu-id="236e1-129">There are a couple of alternatives to consider:</span></span>

- <span data-ttu-id="236e1-130">Status quo</span><span class="sxs-lookup"><span data-stu-id="236e1-130">The status quo</span></span>
- <span data-ttu-id="236e1-131">Fornire assistenza IDE per inserire tutti i nomi degli argomenti finali quando si digita un nome specifico al centro.</span><span class="sxs-lookup"><span data-stu-id="236e1-131">Providing IDE assistance to fill-in all the names of trailing arguments when you type specific a name in the middle.</span></span>

<span data-ttu-id="236e1-132">Entrambe le persone soffrono di un livello di dettaglio maggiore, perché introducono più argomenti denominati anche se è necessario solo un nome di un valore letterale all'inizio dell'elenco di argomenti.</span><span class="sxs-lookup"><span data-stu-id="236e1-132">Both of those suffer from more verbosity, as they introduce multiple named arguments even if you just need one name of a literal at the beginning of the argument list.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="236e1-133">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="236e1-133">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

## <a name="design-meetings"></a><span data-ttu-id="236e1-134">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="236e1-134">Design meetings</span></span>
[ldm]: #ldm
<span data-ttu-id="236e1-135">La funzionalità è stata brevemente discussa in LDM il 16 maggio 2017, con l'approvazione in principio (OK per passare alla proposta/prototipo).</span><span class="sxs-lookup"><span data-stu-id="236e1-135">The feature was briefly discussed in LDM on May 16th 2017, with approval in principle (ok to move to proposal/prototype).</span></span> <span data-ttu-id="236e1-136">È stato descritto anche brevemente il 28 giugno 2017.</span><span class="sxs-lookup"><span data-stu-id="236e1-136">It was also briefly discussed on June 28th 2017.</span></span>

<span data-ttu-id="236e1-137">Si riferisce alla discussione iniziale https://github.com/dotnet/csharplang/issues/518 si riferisce al problema sostenuto https://github.com/dotnet/csharplang/issues/570</span><span class="sxs-lookup"><span data-stu-id="236e1-137">Relates to initial discussion https://github.com/dotnet/csharplang/issues/518 Relates to championed issue https://github.com/dotnet/csharplang/issues/570</span></span>
