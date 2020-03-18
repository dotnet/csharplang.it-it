---
ms.openlocfilehash: 405153448d0e3685d6f22725e00d75d9250b3e20
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484797"
---
# <a name="async-main"></a><span data-ttu-id="a836a-101">Principale asincrono</span><span class="sxs-lookup"><span data-stu-id="a836a-101">Async Main</span></span>

* <span data-ttu-id="a836a-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="a836a-102">[x] Proposed</span></span>
* <span data-ttu-id="a836a-103">[] Prototype</span><span class="sxs-lookup"><span data-stu-id="a836a-103">[ ] Prototype</span></span>
* <span data-ttu-id="a836a-104">[] Implementazione</span><span class="sxs-lookup"><span data-stu-id="a836a-104">[ ] Implementation</span></span>
* <span data-ttu-id="a836a-105">[] Specifica</span><span class="sxs-lookup"><span data-stu-id="a836a-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="a836a-106">Summary</span><span class="sxs-lookup"><span data-stu-id="a836a-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="a836a-107">Consente di usare `await` nel metodo Main/EntryPoint di un'applicazione consentendo al EntryPoint di restituire `Task` / `Task<int>` e di essere contrassegnati come `async`.</span><span class="sxs-lookup"><span data-stu-id="a836a-107">Allow `await` to be used in an application's Main / entrypoint method by allowing the entrypoint to return `Task` / `Task<int>` and be marked `async`.</span></span>

## <a name="motivation"></a><span data-ttu-id="a836a-108">Motivazione</span><span class="sxs-lookup"><span data-stu-id="a836a-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="a836a-109">È molto comune durante l'apprendimento C#, quando si scrivono utilità basate su console e quando si scrivono app di test di piccole dimensioni per chiamare e `await` `async` metodi da Main.</span><span class="sxs-lookup"><span data-stu-id="a836a-109">It is very common when learning C#, when writing console-based utilities, and when writing small test apps to want to call and `await` `async` methods from Main.</span></span>  <span data-ttu-id="a836a-110">Attualmente si aggiunge un livello di complessità forzando l'esecuzione di tale `await`in un metodo asincrono separato, che fa sì che gli sviluppatori debbano scrivere uno standard come il seguente solo per iniziare:</span><span class="sxs-lookup"><span data-stu-id="a836a-110">Today we add a level of complexity here by forcing such `await`'ing to be done in a separate async method, which causes developers to need to write boilerplate like the following just to get started:</span></span>

```csharp
public static void Main()
{
    MainAsync().GetAwaiter().GetResult();
}

private static async Task MainAsync()
{
    ... // Main body here
}
```

<span data-ttu-id="a836a-111">È possibile rimuovere la necessità di questo standard e semplificarne l'avvio semplicemente consentendo la `async` principale, in modo che sia possibile utilizzare `await`s.</span><span class="sxs-lookup"><span data-stu-id="a836a-111">We can remove the need for this boilerplate and make it easier to get started simply by allowing Main itself to be `async` such that `await`s can be used in it.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="a836a-112">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="a836a-112">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="a836a-113">Le firme seguenti sono attualmente consentite entryPoints:</span><span class="sxs-lookup"><span data-stu-id="a836a-113">The following signatures are currently allowed entrypoints:</span></span>

```csharp
static void Main()
static void Main(string[])
static int Main()
static int Main(string[])
```

<span data-ttu-id="a836a-114">Si estenderà l'elenco dei entryPoints consentiti in modo da includere:</span><span class="sxs-lookup"><span data-stu-id="a836a-114">We extend the list of allowed entrypoints to include:</span></span>

```csharp
static Task Main()
static Task<int> Main()
static Task Main(string[])
static Task<int> Main(string[])
```

<span data-ttu-id="a836a-115">Per evitare i rischi di compatibilità, queste nuove firme verranno considerate entryPoints valide solo se non sono presenti overload del set precedente.</span><span class="sxs-lookup"><span data-stu-id="a836a-115">To avoid compatibility risks, these new signatures will only be considered as valid entrypoints if no overloads of the previous set are present.</span></span>
<span data-ttu-id="a836a-116">Il linguaggio/compilatore non richiede che il EntryPoint venga contrassegnato come `async`, anche se si prevede che la maggior parte degli usi venga contrassegnata come tale.</span><span class="sxs-lookup"><span data-stu-id="a836a-116">The language / compiler will not require that the entrypoint be marked as `async`, though we expect the vast majority of uses will be marked as such.</span></span>

<span data-ttu-id="a836a-117">Quando uno di questi viene identificato come EntryPoint, il compilatore sintetizza un Metodo EntryPoint effettivo che chiama uno di questi metodi codificati:</span><span class="sxs-lookup"><span data-stu-id="a836a-117">When one of these is identified as the entrypoint, the compiler will synthesize an actual entrypoint method that calls one of these coded methods:</span></span>
- <span data-ttu-id="a836a-118">```static Task Main()``` comporterà il compilatore che emette l'equivalente di ```private static void $GeneratedMain() => Main().GetAwaiter().GetResult();```</span><span class="sxs-lookup"><span data-stu-id="a836a-118">```static Task Main()``` will result in the compiler emitting the equivalent of ```private static void $GeneratedMain() => Main().GetAwaiter().GetResult();```</span></span>
- <span data-ttu-id="a836a-119">```static Task Main(string[])``` comporterà il compilatore che emette l'equivalente di ```private static void $GeneratedMain(string[] args) => Main(args).GetAwaiter().GetResult();```</span><span class="sxs-lookup"><span data-stu-id="a836a-119">```static Task Main(string[])``` will result in the compiler emitting the equivalent of ```private static void $GeneratedMain(string[] args) => Main(args).GetAwaiter().GetResult();```</span></span>
- <span data-ttu-id="a836a-120">```static Task<int> Main()``` comporterà il compilatore che emette l'equivalente di ```private static int $GeneratedMain() => Main().GetAwaiter().GetResult();```</span><span class="sxs-lookup"><span data-stu-id="a836a-120">```static Task<int> Main()``` will result in the compiler emitting the equivalent of ```private static int $GeneratedMain() => Main().GetAwaiter().GetResult();```</span></span>
- <span data-ttu-id="a836a-121">```static Task<int> Main(string[])``` comporterà il compilatore che emette l'equivalente di ```private static int $GeneratedMain(string[] args) => Main(args).GetAwaiter().GetResult();```</span><span class="sxs-lookup"><span data-stu-id="a836a-121">```static Task<int> Main(string[])``` will result in the compiler emitting the equivalent of ```private static int $GeneratedMain(string[] args) => Main(args).GetAwaiter().GetResult();```</span></span>

<span data-ttu-id="a836a-122">Esempio di utilizzo:</span><span class="sxs-lookup"><span data-stu-id="a836a-122">Example usage:</span></span>

```csharp
using System;
using System.Net.Http;

class Test
{
    static async Task Main(string[] args) =>
        Console.WriteLine(await new HttpClient().GetStringAsync(args[0]));
}
```

## <a name="drawbacks"></a><span data-ttu-id="a836a-123">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="a836a-123">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="a836a-124">Lo svantaggio principale è semplicemente l'ulteriore complessità del supporto di firme EntryPoint aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="a836a-124">The main drawback is simply the additional complexity of supporting additional entrypoint signatures.</span></span>

## <a name="alternatives"></a><span data-ttu-id="a836a-125">Alternativi</span><span class="sxs-lookup"><span data-stu-id="a836a-125">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="a836a-126">Altre varianti considerate:</span><span class="sxs-lookup"><span data-stu-id="a836a-126">Other variants considered:</span></span>

<span data-ttu-id="a836a-127">Consentendo `async void`.</span><span class="sxs-lookup"><span data-stu-id="a836a-127">Allowing `async void`.</span></span>  <span data-ttu-id="a836a-128">È necessario mantenerla identica per il codice che lo chiama direttamente, il che renderebbe difficile per un punto di ingresso generato la chiamata (nessuna attività restituita).</span><span class="sxs-lookup"><span data-stu-id="a836a-128">We need to keep the semantics the same for code calling it directly, which would then make it difficult for a generated entrypoint to call it (no Task returned).</span></span>  <span data-ttu-id="a836a-129">È possibile risolvere questo problema generando altri due metodi, ad esempio</span><span class="sxs-lookup"><span data-stu-id="a836a-129">We could solve this by generating two other methods, e.g.</span></span>

```csharp
public static async void Main()
{
   ... // await code
}
```

<span data-ttu-id="a836a-130">diventa</span><span class="sxs-lookup"><span data-stu-id="a836a-130">becomes</span></span>

```csharp
public static async void Main() => await $MainTask();

private static void $EntrypointMain() => Main().GetAwaiter().GetResult();

private static async Task $MainTask()
{
    ... // await code
}
```

<span data-ttu-id="a836a-131">Ci sono inoltre problemi a incoraggiare l'utilizzo di `async void`.</span><span class="sxs-lookup"><span data-stu-id="a836a-131">There are also concerns around encouraging usage of `async void`.</span></span>

<span data-ttu-id="a836a-132">Usare "MainAsync" invece di "Main" come nome.</span><span class="sxs-lookup"><span data-stu-id="a836a-132">Using "MainAsync" instead of "Main" as the name.</span></span>  <span data-ttu-id="a836a-133">Sebbene sia consigliabile usare il suffisso asincrono per i metodi che restituiscono attività, si tratta principalmente della funzionalità della libreria, che non è la principale, e il supporto di nomi di EntryPoint aggiuntivi oltre a "Main" non vale la pena.</span><span class="sxs-lookup"><span data-stu-id="a836a-133">While the async suffix is recommended for Task-returning methods, that's primarily about library functionality, which Main is not, and supporting additional entrypoint names beyond "Main" is not worth it.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="a836a-134">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="a836a-134">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="a836a-135">n/d</span><span class="sxs-lookup"><span data-stu-id="a836a-135">n/a</span></span>

## <a name="design-meetings"></a><span data-ttu-id="a836a-136">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="a836a-136">Design meetings</span></span>

<span data-ttu-id="a836a-137">n/d</span><span class="sxs-lookup"><span data-stu-id="a836a-137">n/a</span></span>
