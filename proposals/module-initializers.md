---
ms.openlocfilehash: e345bb5d9a4f998f80c13a255cec808bd2c9299f
ms.sourcegitcommit: aa2d828c824c5d81c5aa20ba4d3c6880633088ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85279069"
---
# <a name="module-initializers"></a><span data-ttu-id="29297-101">Inizializzatori di modulo</span><span class="sxs-lookup"><span data-stu-id="29297-101">Module Initializers</span></span>

* <span data-ttu-id="29297-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="29297-102">[x] Proposed</span></span>
* <span data-ttu-id="29297-103">[] Prototipo: [in corso](https://github.com/jnm2/roslyn/tree/module_initializer)</span><span class="sxs-lookup"><span data-stu-id="29297-103">[ ] Prototype: [In Progress](https://github.com/jnm2/roslyn/tree/module_initializer)</span></span>
* <span data-ttu-id="29297-104">[] Implementazione: [in corso](https://github.com/dotnet/roslyn/tree/features/module-initializers)</span><span class="sxs-lookup"><span data-stu-id="29297-104">[ ] Implementation: [In Progress](https://github.com/dotnet/roslyn/tree/features/module-initializers)</span></span>
* <span data-ttu-id="29297-105">[] Specifica: [non avviata]()</span><span class="sxs-lookup"><span data-stu-id="29297-105">[ ] Specification: [Not Started]()</span></span>

## <a name="summary"></a><span data-ttu-id="29297-106">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="29297-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="29297-107">Sebbene la piattaforma .NET disponga di una [funzionalità](https://github.com/dotnet/runtime/blob/master/docs/design/specs/Ecma-335-Augments.md#module-initializer) che supporta direttamente la scrittura del codice di inizializzazione per l'assembly (tecnicamente, il modulo), non viene esposta in C#.</span><span class="sxs-lookup"><span data-stu-id="29297-107">Although the .NET platform has a [feature](https://github.com/dotnet/runtime/blob/master/docs/design/specs/Ecma-335-Augments.md#module-initializer) that directly supports writing initialization code for the assembly (technically, the module), it is not exposed in C#.</span></span>  <span data-ttu-id="29297-108">Si tratta di uno scenario di nicchia piuttosto semplice, ma dopo l'esecuzione le soluzioni sembrano essere piuttosto dolorose.</span><span class="sxs-lookup"><span data-stu-id="29297-108">This is a rather niche scenario, but once you run into it the solutions appear to be pretty painful.</span></span>  <span data-ttu-id="29297-109">Ci sono rapporti di [un numero di clienti](https://www.google.com/search?q=.net+module+constructor+c%23&oq=.net+module+constructor) (all'interno e all'esterno di Microsoft) che faticano a riscontrare il problema e non ci sono dubbi più casi non documentati.</span><span class="sxs-lookup"><span data-stu-id="29297-109">There are reports of [a number of customers](https://www.google.com/search?q=.net+module+constructor+c%23&oq=.net+module+constructor) (inside and outside Microsoft) struggling with the problem, and there are no doubt more undocumented cases.</span></span>

## <a name="motivation"></a><span data-ttu-id="29297-110">Motivazione</span><span class="sxs-lookup"><span data-stu-id="29297-110">Motivation</span></span>
[motivation]: #motivation

- <span data-ttu-id="29297-111">Consentire alle librerie di eseguire l'inizializzazione unica, una volta caricata, con un sovraccarico minimo e senza che l'utente debba chiamare esplicitamente niente</span><span class="sxs-lookup"><span data-stu-id="29297-111">Enable libraries to do eager, one-time initialization when loaded, with minimal overhead and without the user needing to explicitly call anything</span></span>
- <span data-ttu-id="29297-112">Un particolare punto problematico degli `static` approcci dei costruttori correnti è che il runtime deve eseguire verifiche aggiuntive sull'utilizzo di un tipo con un costruttore statico, per decidere se il costruttore statico deve essere eseguito o meno.</span><span class="sxs-lookup"><span data-stu-id="29297-112">One particular pain point of current `static` constructor approaches is that the runtime must do additional checks on usage of a type with a static constructor, in order to decide whether the static constructor needs to be run or not.</span></span> <span data-ttu-id="29297-113">Questo aggiunge un sovraccarico misurabile.</span><span class="sxs-lookup"><span data-stu-id="29297-113">This adds measurable overhead.</span></span>
- <span data-ttu-id="29297-114">Consentire ai generatori di origine di eseguire una logica di inizializzazione globale senza che l'utente debba chiamare esplicitamente un elemento</span><span class="sxs-lookup"><span data-stu-id="29297-114">Enable source generators to run some global initialization logic without the user needing to explicitly call anything</span></span>

## <a name="detailed-design"></a><span data-ttu-id="29297-115">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="29297-115">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="29297-116">Un metodo può essere designato come inizializzatore di modulo, decorato con un `[ModuleInitializer]` attributo.</span><span class="sxs-lookup"><span data-stu-id="29297-116">A method can be designated as a module initializer by decorating it with a `[ModuleInitializer]` attribute.</span></span>

```cs
using System;
namespace System.Runtime.CompilerServices
{
    [AttributeUsage(AttributeTargets.Method, AllowMultiple = false)]
    public sealed class ModuleInitializerAttribute : Attribute { }
}
```

<span data-ttu-id="29297-117">L'attributo può essere utilizzato come segue:</span><span class="sxs-lookup"><span data-stu-id="29297-117">The attribute can be used like this:</span></span>

```cs
using System.Runtime.CompilerServices;
class C
{
    [ModuleInitializer]
    internal static void M1()
    {
        // ...
    }
}
```

<span data-ttu-id="29297-118">Alcuni requisiti vengono applicati al metodo di destinazione con questo attributo:</span><span class="sxs-lookup"><span data-stu-id="29297-118">Some requirements are imposed on the method targeted with this attribute:</span></span>
1. <span data-ttu-id="29297-119">Il metodo deve essere `static` .</span><span class="sxs-lookup"><span data-stu-id="29297-119">The method must be `static`.</span></span>
1. <span data-ttu-id="29297-120">Il metodo deve essere senza parametri.</span><span class="sxs-lookup"><span data-stu-id="29297-120">The method must be parameterless.</span></span>
1. <span data-ttu-id="29297-121">Il metodo deve restituire `void`.</span><span class="sxs-lookup"><span data-stu-id="29297-121">The method must return `void`.</span></span>
1. <span data-ttu-id="29297-122">Il metodo non deve essere generico o essere incluso in un tipo generico.</span><span class="sxs-lookup"><span data-stu-id="29297-122">The method must not be generic or be contained in a generic type.</span></span>
1. <span data-ttu-id="29297-123">Il metodo deve essere accessibile dal modulo contenitore.</span><span class="sxs-lookup"><span data-stu-id="29297-123">The method must be accessible from the containing module.</span></span>
    - <span data-ttu-id="29297-124">Ciò significa che l'accessibilità effettiva del metodo deve essere `internal` o `public` .</span><span class="sxs-lookup"><span data-stu-id="29297-124">This means the method's effective accessibility must be `internal` or `public`.</span></span>
    - <span data-ttu-id="29297-125">Questo significa anche che il metodo non può essere una funzione locale.</span><span class="sxs-lookup"><span data-stu-id="29297-125">This also means the method cannot be a local function.</span></span>
    
<span data-ttu-id="29297-126">Quando uno o più metodi validi con questo attributo vengono trovati in una compilazione, il compilatore emetterà un inizializzatore di modulo che chiama ognuno dei metodi con attributi.</span><span class="sxs-lookup"><span data-stu-id="29297-126">When one or more valid methods with this attribute are found in a compilation, the compiler will emit a module initializer which calls each of the attributed methods.</span></span> <span data-ttu-id="29297-127">Le chiamate verranno emesse in un ordine riservato, ma deterministico.</span><span class="sxs-lookup"><span data-stu-id="29297-127">The calls will be emitted in a reserved, but deterministic order.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="29297-128">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="29297-128">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="29297-129">Perché *non* eseguire questa operazione?</span><span class="sxs-lookup"><span data-stu-id="29297-129">Why should we *not* do this?</span></span>

- <span data-ttu-id="29297-130">Forse gli strumenti di terze parti esistenti per gli inizializzatori di modulo "inserimento" sono sufficienti per gli utenti che hanno richiesto questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="29297-130">Perhaps the existing third-party tooling for "injecting" module initializers is sufficient for users who have been asking for this feature.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="29297-131">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="29297-131">Design meetings</span></span>

### <a name="april-8th-2020"></a>[<span data-ttu-id="29297-132">8 aprile 2020</span><span class="sxs-lookup"><span data-stu-id="29297-132">April 8th, 2020</span></span>](/meetings/2020/LDM-2020-04-08.md#module-initializers)
