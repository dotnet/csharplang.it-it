---
ms.openlocfilehash: 7522bee6ac14d205aaf2a8491c13a321d2c18d62
ms.sourcegitcommit: c30039481ee8a75c3b3e4ddd369fdf8f84f8945b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598581"
---
# <a name="module-initializers"></a><span data-ttu-id="b5a16-101">Inizializzatori di modulo</span><span class="sxs-lookup"><span data-stu-id="b5a16-101">Module Initializers</span></span>

* <span data-ttu-id="b5a16-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="b5a16-102">[x] Proposed</span></span>
* <span data-ttu-id="b5a16-103">[] Prototipo: [in corso](https://github.com/jnm2/roslyn/tree/module_initializer)</span><span class="sxs-lookup"><span data-stu-id="b5a16-103">[ ] Prototype: [In Progress](https://github.com/jnm2/roslyn/tree/module_initializer)</span></span>
* <span data-ttu-id="b5a16-104">[] Implementazione: [in corso](https://github.com/dotnet/roslyn/tree/features/module-initializers)</span><span class="sxs-lookup"><span data-stu-id="b5a16-104">[ ] Implementation: [In Progress](https://github.com/dotnet/roslyn/tree/features/module-initializers)</span></span>
* <span data-ttu-id="b5a16-105">[] Specifica: [non avviata]()</span><span class="sxs-lookup"><span data-stu-id="b5a16-105">[ ] Specification: [Not Started]()</span></span>

## <a name="summary"></a><span data-ttu-id="b5a16-106">Summary</span><span class="sxs-lookup"><span data-stu-id="b5a16-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="b5a16-107">Sebbene la piattaforma .NET disponga di una funzionalità che supporta direttamente la scrittura del codice di inizializzazione per l'assembly (tecnicamente, il modulo), non viene esposta in C#.</span><span class="sxs-lookup"><span data-stu-id="b5a16-107">Although the .NET platform has a feature that directly supports writing initialization code for the assembly (technically, the module), it is not exposed in C#.</span></span>  <span data-ttu-id="b5a16-108">Si tratta di uno scenario di nicchia piuttosto semplice, ma dopo l'esecuzione le soluzioni sembrano essere piuttosto dolorose.</span><span class="sxs-lookup"><span data-stu-id="b5a16-108">This is a rather niche scenario, but once you run into it the solutions appear to be pretty painful.</span></span>  <span data-ttu-id="b5a16-109">Ci sono rapporti di [un numero di clienti](https://www.google.com/search?q=.net+module+constructor+c%23&oq=.net+module+constructor) (all'interno e all'esterno di Microsoft) che faticano a riscontrare il problema e non ci sono dubbi più casi non documentati.</span><span class="sxs-lookup"><span data-stu-id="b5a16-109">There are reports of [a number of customers](https://www.google.com/search?q=.net+module+constructor+c%23&oq=.net+module+constructor) (inside and outside Microsoft) struggling with the problem, and there are no doubt more undocumented cases.</span></span>

## <a name="motivation"></a><span data-ttu-id="b5a16-110">Motivazione</span><span class="sxs-lookup"><span data-stu-id="b5a16-110">Motivation</span></span>
[motivation]: #motivation

- <span data-ttu-id="b5a16-111">Consentire alle librerie di eseguire l'inizializzazione unica, una volta caricata, con un sovraccarico minimo e senza che l'utente debba chiamare esplicitamente niente</span><span class="sxs-lookup"><span data-stu-id="b5a16-111">Enable libraries to do eager, one-time initialization when loaded, with minimal overhead and without the user needing to explicitly call anything</span></span>
- <span data-ttu-id="b5a16-112">Un particolare punto problematico degli `static` approcci dei costruttori correnti è che il runtime deve eseguire verifiche aggiuntive sull'utilizzo di un tipo con un costruttore statico, per decidere se il costruttore statico deve essere eseguito o meno.</span><span class="sxs-lookup"><span data-stu-id="b5a16-112">One particular pain point of current `static` constructor approaches is that the runtime must do additional checks on usage of a type with a static constructor, in order to decide whether the static constructor needs to be run or not.</span></span> <span data-ttu-id="b5a16-113">Questo aggiunge un sovraccarico misurabile.</span><span class="sxs-lookup"><span data-stu-id="b5a16-113">This adds measurable overhead.</span></span>
- <span data-ttu-id="b5a16-114">Consentire ai generatori di origine di eseguire una logica di inizializzazione globale senza che l'utente debba chiamare esplicitamente un elemento</span><span class="sxs-lookup"><span data-stu-id="b5a16-114">Enable source generators to run some global initialization logic without the user needing to explicitly call anything</span></span>

## <a name="detailed-design"></a><span data-ttu-id="b5a16-115">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="b5a16-115">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="b5a16-116">Il compilatore C# riconoscerà l'attributo seguente:</span><span class="sxs-lookup"><span data-stu-id="b5a16-116">The C# compiler will recognize the following attribute:</span></span>

``` c#
namespace System.Runtime.CompilerServices
{
    // Note: an Obsolete attribute is not needed here,
    // because only C# 9 compilers will have access to attributes added in .NET 5.
    // LangVersion checks will still be necessary.
    [AttributeUsage(AttributeTargets.Module, AllowMultiple = false)]
    public class ModuleInitializerAttribute : Attribute
    {
        public ModuleInitializerAttribute(Type type) { }
    }
}
```

<span data-ttu-id="b5a16-117">Che verrà usata come questa</span><span class="sxs-lookup"><span data-stu-id="b5a16-117">You would use it like this</span></span>

``` c#
using System.Runtime.CompilerServices;

[module: ModuleInitializer(typeof(MyModuleInitializer))]

internal static class MyModuleInitializer
{
    static MyModuleInitializer()
    {
        // put your module initializer here
    }
}
```

<span data-ttu-id="b5a16-118">e il compilatore C# creerebbe quindi un costruttore di modulo che causa l'attivazione del costruttore statico del tipo identificato:</span><span class="sxs-lookup"><span data-stu-id="b5a16-118">and the C# compiler would then emit a module constructor that causes the static constructor of the identified type to be triggered:</span></span>

``` c#
void .cctor()
{
    // synthesize and call a dummy method with an unspeakable name,
    // which will cause the runtime to call the static constructor
    MyModuleInitializer.<TriggerClassConstructor>();
}
```

<span data-ttu-id="b5a16-119">Si noti che il sovraccarico di verifica se il costruttore statico è stato eseguito in un tipo di`MyModuleInitializer` inizializzatore di modulo (in questo esempio) può essere mantenuto come minimo facendo riferimento al `[module: ModuleInitializer(typeof(MyModuleInitializer))]` tipo solo nell'attributo.</span><span class="sxs-lookup"><span data-stu-id="b5a16-119">Note that the overhead of checking whether the static constructor has run on a module initializer type (`MyModuleInitializer` in this example) can be kept to a minimum by referencing the type only in the `[module: ModuleInitializer(typeof(MyModuleInitializer))]` attribute.</span></span>

<span data-ttu-id="b5a16-120">Questa proposta USA `AttributeUsage(AllowMultiple = false)` per impedire all'utente di dichiarare più di una classe dell'inizializzatore senza aggiungere alcuna regola di linguaggio speciale per eseguire tale operazione.</span><span class="sxs-lookup"><span data-stu-id="b5a16-120">This proposal uses `AttributeUsage(AllowMultiple = false)` to disallow the user from declaring more than one initializer class without adding any special language rules to accomplish that.</span></span> <span data-ttu-id="b5a16-121">È molto leggero perché usa la sintassi e le regole semantiche esistenti per gli attributi.</span><span class="sxs-lookup"><span data-stu-id="b5a16-121">It is very lightweight in that it uses existing syntax and semantic rules for attributes.</span></span> <span data-ttu-id="b5a16-122">D'altra parte, può risultare più difficile per gli utenti tenere presente che il costruttore statico che sta esaminando è l'inizializzatore del modulo, perché l'attributo può essere distante dalla classe che contiene effettivamente l'inizializzatore del modulo e richiede anche di decidere cosa accade se l'attributo viene usato per fare riferimento a un tipo da un altro assembly.</span><span class="sxs-lookup"><span data-stu-id="b5a16-122">On the other hand, it may make it more difficult for users to realize that the static constructor they are looking at is the module initializer, because the attribute may be far away from the class that actually contains the module initializer, and it also requires us to decide what happens if the attribute is used to reference a type from another assembly.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="b5a16-123">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="b5a16-123">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

#### <a name="should-we-permit-multiple-types-to-be-decorated-with-moduleinitializerattribute-in-a-compilation-if-so-in-what-order-should-the-static-constructors-be-invoked"></a><span data-ttu-id="b5a16-124">È possibile consentire la decorazione di più tipi con ModuleInitializerAttribute in una compilazione?</span><span class="sxs-lookup"><span data-stu-id="b5a16-124">Should we permit multiple types to be decorated with ModuleInitializerAttribute in a compilation?</span></span> <span data-ttu-id="b5a16-125">In tal caso, nell'ordine in cui devono essere richiamati i costruttori statici?</span><span class="sxs-lookup"><span data-stu-id="b5a16-125">If so, in what order should the static constructors be invoked?</span></span>

<span data-ttu-id="b5a16-126">Un'opzione: eseguire la stessa operazione per gli inizializzatori statici nelle classi parziali.</span><span class="sxs-lookup"><span data-stu-id="b5a16-126">One option: do the same thing we do for static initializers in partial classes.</span></span> <span data-ttu-id="b5a16-127">Ordinarle in base all'ordine del file e alla posizione di origine.</span><span class="sxs-lookup"><span data-stu-id="b5a16-127">Order them based on file order+source position.</span></span>

#### <a name="should-we-permit-using-a-type-from-another-assembly-as-the-module-initializer"></a><span data-ttu-id="b5a16-128">È consigliabile consentire l'uso di un tipo da un altro assembly come inizializzatore di modulo?</span><span class="sxs-lookup"><span data-stu-id="b5a16-128">Should we permit using a type from another assembly as the module initializer?</span></span>

<span data-ttu-id="b5a16-129">e.g.`[module: ModuleInitializerAttribute(typeof(InitializerFromOtherAssembly))]`</span><span class="sxs-lookup"><span data-stu-id="b5a16-129">e.g.  `[module: ModuleInitializerAttribute(typeof(InitializerFromOtherAssembly))]`</span></span>

<span data-ttu-id="b5a16-130">Questo potrebbe rendere difficile richiamare il costruttore della classe semplicemente chiamando un metodo fittizio, perché non sarà possibile sintetizzare tale metodo sul tipo.</span><span class="sxs-lookup"><span data-stu-id="b5a16-130">This could make it difficult to invoke the class constructor simply by calling a dummy method, because we won't be able to synthesize such a method on the type.</span></span> <span data-ttu-id="b5a16-131">È possibile considerare la possibilità di `System.Runtime.CompilerServices.RuntimeHelpers.RunClassConstructor()` eseguire il fallback in questo caso, ma introduce una dipendenza dallo stack di Reflection.</span><span class="sxs-lookup"><span data-stu-id="b5a16-131">We could consider falling back to `System.Runtime.CompilerServices.RuntimeHelpers.RunClassConstructor()` in this case, but it introduces a dependency on the reflection stack.</span></span>

<span data-ttu-id="b5a16-132">Questo caso d'uso implica che la classe dell'inizializzatore non sia l'inizializzatore di modulo per l'assembly in cui è dichiarato.</span><span class="sxs-lookup"><span data-stu-id="b5a16-132">This use case implies that the initializer class is not the module initializer for the assembly it is declared in.</span></span> <span data-ttu-id="b5a16-133">Si tratta di un caso di utilizzo che deve invece essere gestito esponendo un metodo che il consumer esterno deve chiamare dall'inizializzatore del modulo.</span><span class="sxs-lookup"><span data-stu-id="b5a16-133">It feels like this use case should instead by handled by exposing a method that the external consumer should call from their module initializer.</span></span>

```cs
// Assembly 1
public class MyLibInit
{
    public static void Init() { }
}

// Assembly 2
using System.Runtime.CompilerServices;

[module: ModuleInitializer(typeof(MyInit))]

class MyInit
{
    static
    {
        MyLibInit.Init();
    }
}
```

## <a name="drawbacks"></a><span data-ttu-id="b5a16-134">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="b5a16-134">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="b5a16-135">Perché *non* eseguire questa operazione?</span><span class="sxs-lookup"><span data-stu-id="b5a16-135">Why should we *not* do this?</span></span>

- <span data-ttu-id="b5a16-136">Forse gli strumenti di terze parti esistenti per gli inizializzatori di modulo "inserimento" sono sufficienti per gli utenti che hanno richiesto questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="b5a16-136">Perhaps the existing third-party tooling for "injecting" module initializers is sufficient for users who have been asking for this feature.</span></span>

## <a name="alternatives"></a><span data-ttu-id="b5a16-137">Alternativi</span><span class="sxs-lookup"><span data-stu-id="b5a16-137">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="b5a16-138">Quali altre progettazioni sono state considerate?</span><span class="sxs-lookup"><span data-stu-id="b5a16-138">What other designs have been considered?</span></span> <span data-ttu-id="b5a16-139">Qual è l'effetto di questa operazione?</span><span class="sxs-lookup"><span data-stu-id="b5a16-139">What is the impact of not doing this?</span></span>

<span data-ttu-id="b5a16-140">Esistono diversi modi possibili per esporre questa funzionalità nel linguaggio:</span><span class="sxs-lookup"><span data-stu-id="b5a16-140">There are a number of possible ways of exposing this feature in the language:</span></span>

### <a name="1-special-global-method-declaration"></a><span data-ttu-id="b5a16-141">1. dichiarazione speciale di metodo globale</span><span class="sxs-lookup"><span data-stu-id="b5a16-141">1. Special global method declaration</span></span>

<span data-ttu-id="b5a16-142">Viene fornito un inizializzatore di modulo scrivendo un tipo speciale di metodo nell'ambito globale:</span><span class="sxs-lookup"><span data-stu-id="b5a16-142">A module initializer would be provided by writing a special kind of method in the global scope:</span></span>

```csharp
internal void operator init() ...
```

<span data-ttu-id="b5a16-143">In questo modo il nuovo linguaggio crea la propria sintassi.</span><span class="sxs-lookup"><span data-stu-id="b5a16-143">This gives the new language construct its own syntax.</span></span> <span data-ttu-id="b5a16-144">Tuttavia, date le rare e le nicchie dello scenario, questo approccio è probabilmente troppo pesante.</span><span class="sxs-lookup"><span data-stu-id="b5a16-144">However, given how rare and niche the scenario is, this is probably far too heavyweight an approach.</span></span>

### <a name="2-attribute-on-the-type-to-be-initialized"></a><span data-ttu-id="b5a16-145">2. attributo sul tipo da inizializzare</span><span class="sxs-lookup"><span data-stu-id="b5a16-145">2. Attribute on the type to be initialized</span></span>

<span data-ttu-id="b5a16-146">Anziché un attributo a livello di modulo, probabilmente l'attributo verrebbe inserito sul tipo da inizializzare</span><span class="sxs-lookup"><span data-stu-id="b5a16-146">Instead of a module-level attribute, perhaps the attribute would be placed on the type to be initialized</span></span>

```csharp
[ModuleInitializer]
class ToInitialize
{
    static ToInitialize() ...
}
```

<span data-ttu-id="b5a16-147">Con questo approccio, è necessario rifiutare un programma che contiene più di un'applicazione di questo attributo o fornire un criterio per definire l'ordine nel caso in cui venga usato più volte.</span><span class="sxs-lookup"><span data-stu-id="b5a16-147">With this approach, we would either need to reject a program that contains more than one application of this attribute, or provide some policy to define the ordering in case it is used multiple times.</span></span> <span data-ttu-id="b5a16-148">In entrambi i casi, è più complesso rispetto alla proposta originale precedente.</span><span class="sxs-lookup"><span data-stu-id="b5a16-148">Either way, it is more complex than the original proposal above.</span></span>

#### <a name="3-attribute-on-the-static-constructor-to-be-initialized"></a><span data-ttu-id="b5a16-149">3. attributo sul costruttore statico da inizializzare</span><span class="sxs-lookup"><span data-stu-id="b5a16-149">3. Attribute on the static constructor to be initialized</span></span>

<span data-ttu-id="b5a16-150">Invece di un attributo a livello di modulo, probabilmente l'attributo viene inserito nel costruttore statico da inizializzare.</span><span class="sxs-lookup"><span data-stu-id="b5a16-150">Instead of a module-level attribute, perhaps the attribute would be placed on the static constructor to be initialized</span></span>

```csharp
class ToInitialize
{
    [ModuleInitializer]
    static ToInitialize() ...
}
```

<span data-ttu-id="b5a16-151">Con questo approccio, è necessario rifiutare un programma che contiene più di un'applicazione di questo attributo o fornire un criterio per definire l'ordine nel caso in cui venga usato più volte.</span><span class="sxs-lookup"><span data-stu-id="b5a16-151">With this approach, we would either need to reject a program that contains more than one application of this attribute, or provide some policy to define the ordering in case it is used multiple times.</span></span> <span data-ttu-id="b5a16-152">In entrambi i casi, è più complesso rispetto alla proposta originale precedente.</span><span class="sxs-lookup"><span data-stu-id="b5a16-152">Either way, it is more complex than the original proposal above.</span></span>

#### <a name="4-attribute-on-a-static-method-to-be-called"></a><span data-ttu-id="b5a16-153">4. attributo su un metodo statico da chiamare</span><span class="sxs-lookup"><span data-stu-id="b5a16-153">4. Attribute on a static method to be called</span></span>

<span data-ttu-id="b5a16-154">Invece di un attributo a livello di modulo, probabilmente l'attributo viene inserito nel metodo da chiamare per eseguire l'inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="b5a16-154">Instead of a module-level attribute, perhaps the attribute would be placed on the method to be called to perform the initialization</span></span>

```csharp
class Any
{
    [ModuleInitializer]
    static void Initializer() ...
}
```

<span data-ttu-id="b5a16-155">Come nell'approccio precedente, è necessario rifiutare un programma che contiene più di un'applicazione di questo attributo o fornire alcuni criteri per definire l'ordine nel caso in cui venga usato più volte.</span><span class="sxs-lookup"><span data-stu-id="b5a16-155">As in the previous approach, we would either need to reject a program that contains more than one application of this attribute, or provide some policy to define the ordering in case it is used multiple times.</span></span> <span data-ttu-id="b5a16-156">In entrambi i casi, è più complesso della proposta originale.</span><span class="sxs-lookup"><span data-stu-id="b5a16-156">Either way, it is more complex than the original proposal.</span></span> <span data-ttu-id="b5a16-157">Potrebbe anche essere necessario rifiutare qualsiasi metodo con questo attributo se presenta parametri o un tipo non`void` restituito.</span><span class="sxs-lookup"><span data-stu-id="b5a16-157">We also would probably need to reject any method with this attribute if it has parameters or a non-`void` return type.</span></span>

#### <a name="5-do-nothing"></a><span data-ttu-id="b5a16-158">5. non eseguire alcuna operazione</span><span class="sxs-lookup"><span data-stu-id="b5a16-158">5. Do nothing</span></span>
<span data-ttu-id="b5a16-159">Se questa funzionalità non viene implementata, effettuare le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="b5a16-159">If we do not implement this feature, then:</span></span>
- <span data-ttu-id="b5a16-160">Gli utenti che necessitano degli inizializzatori di moduli continuano a basarsi sugli strumenti di terze parti per inserirli negli assembly.</span><span class="sxs-lookup"><span data-stu-id="b5a16-160">Users who really need module initializers continue to rely on third-party tooling to inject them into their assemblies.</span></span>
- <span data-ttu-id="b5a16-161">I generatori di origine dovranno basarsi su costruttori statici che aggiungono sovraccarico o sui metodi init () che gli utenti devono chiamare in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="b5a16-161">Source generators will have to rely on static constructors which add overhead or on Init() methods that users must explicitly call.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="b5a16-162">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="b5a16-162">Design meetings</span></span>

### <a name="april-8th-2020"></a>[<span data-ttu-id="b5a16-163">8 aprile 2020</span><span class="sxs-lookup"><span data-stu-id="b5a16-163">April 8th, 2020</span></span>](/meetings/2020/LDM-2020-04-08.md#module-initializers)
<span data-ttu-id="b5a16-164">È possibile lasciare che qualsiasi metodo statico sia un inizializzatore di modulo (opzione 4 nella proposta precedente) e contrassegnare il metodo usando un attributo noto.</span><span class="sxs-lookup"><span data-stu-id="b5a16-164">Let's let any static method be a module initializer (option 4 in the above proposal), and mark that method using a well-known attribute.</span></span> <span data-ttu-id="b5a16-165">Verranno inoltre consentiti più metodi di inizializzazione del modulo e ognuno verrà chiamato in un ordine riservato, ma deterministico.</span><span class="sxs-lookup"><span data-stu-id="b5a16-165">We'll also allow multiple module initializer methods, and they will each be called in a reserved, but deterministic order.</span></span>
