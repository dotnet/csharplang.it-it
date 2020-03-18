---
ms.openlocfilehash: a8822137c85f449444ed675c6f2912315c041691
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484475"
---
# <a name="static-delegates"></a><span data-ttu-id="c8c53-101">Delegati statici</span><span class="sxs-lookup"><span data-stu-id="c8c53-101">Static Delegates</span></span>

* <span data-ttu-id="c8c53-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="c8c53-102">[x] Proposed</span></span>
* <span data-ttu-id="c8c53-103">[] Prototipo: non avviato</span><span class="sxs-lookup"><span data-stu-id="c8c53-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="c8c53-104">[] Implementazione: non avviata</span><span class="sxs-lookup"><span data-stu-id="c8c53-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="c8c53-105">[] Specifica: non avviata</span><span class="sxs-lookup"><span data-stu-id="c8c53-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="c8c53-106">Summary</span><span class="sxs-lookup"><span data-stu-id="c8c53-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="c8c53-107">Fornire al C# linguaggio una funzionalità di callback leggero e per utilizzo generico.</span><span class="sxs-lookup"><span data-stu-id="c8c53-107">Provide a general-purpose, lightweight callback capability to the C# language.</span></span>

## <a name="motivation"></a><span data-ttu-id="c8c53-108">Motivazione</span><span class="sxs-lookup"><span data-stu-id="c8c53-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="c8c53-109">Attualmente, gli utenti hanno la possibilità di creare callback usando il tipo di `System.Delegate`.</span><span class="sxs-lookup"><span data-stu-id="c8c53-109">Today, users have the ability to create callbacks using the `System.Delegate` type.</span></span> <span data-ttu-id="c8c53-110">Tuttavia, questi sono piuttosto pesanti, ad esempio richiedere un'allocazione dell'heap e gestire sempre il concatenamento dei callback.</span><span class="sxs-lookup"><span data-stu-id="c8c53-110">However, these are fairly heavyweight (such as requiring a heap allocation and always having handling for chaining callbacks together).</span></span>

<span data-ttu-id="c8c53-111">Inoltre, `System.Delegate` non fornisce la migliore interoperabilità con i puntatori a funzioni non gestite, ovvero perché non è copiabile e richiede il marshalling ogni volta che supera il limite gestito/non gestito.</span><span class="sxs-lookup"><span data-stu-id="c8c53-111">Additionally, `System.Delegate` does not provide the best interop with unmanaged function pointers, namely due being non-blittable and requiring marshalling anytime it crosses the managed/unmanaged boundary.</span></span>

<span data-ttu-id="c8c53-112">Con alcune modifiche minime, è possibile fornire un nuovo tipo di delegato che è semplice, generico e interoperabilità con il codice nativo.</span><span class="sxs-lookup"><span data-stu-id="c8c53-112">With a few minor tweaks, we could provide a new type of delegate that is lightweight, general-purpose, and interops well with native code.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="c8c53-113">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="c8c53-113">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="c8c53-114">Uno dichiara un delegato statico tramite il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="c8c53-114">One would declare a static delegate via the following:</span></span>

```C#
static delegate int Func()
```

<span data-ttu-id="c8c53-115">È anche possibile attribuire la dichiarazione con un valore simile a `System.Runtime.InteropServices.UnmanagedFunctionPointer` in modo che sia possibile controllare la convenzione di chiamata, il marshalling della stringa e il comportamento dell'ultimo errore impostato.</span><span class="sxs-lookup"><span data-stu-id="c8c53-115">One could additionally attribute the declaration with something similar to `System.Runtime.InteropServices.UnmanagedFunctionPointer` so that the calling convention, string marshalling, and set last error behavior can be controlled.</span></span> <span data-ttu-id="c8c53-116">Nota: l'uso di `System.Runtime.InteropServices.UnmanagedFunctionPointer` stesso non funzionerà perché è utilizzabile solo nei delegati.</span><span class="sxs-lookup"><span data-stu-id="c8c53-116">NOTE: Using `System.Runtime.InteropServices.UnmanagedFunctionPointer` itself will not work, as it is only usable on Delegates.</span></span>

<span data-ttu-id="c8c53-117">La dichiarazione verrebbe convertita in una rappresentazione interna da parte del compilatore, simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="c8c53-117">The declaration would get translated into an internal representation by the compiler that is similar to the following</span></span>

```C#
struct <Func>e__StaticDelegate
{
    IntPtr pFunction;

    static int WellKnownCompilerName();
}
```

<span data-ttu-id="c8c53-118">Ovvero, è rappresentato internamente da uno struct che ha un singolo membro di tipo `IntPtr` (tale struct è copiabile e non comporta allocazioni heap):</span><span class="sxs-lookup"><span data-stu-id="c8c53-118">That is to say, it is internally represented by a struct that has a single member of type `IntPtr` (such a struct is blittable and does not incur any heap allocations):</span></span>
* <span data-ttu-id="c8c53-119">Il membro contiene l'indirizzo della funzione che deve essere il callback.</span><span class="sxs-lookup"><span data-stu-id="c8c53-119">The member contains the address of the function that is to be the callback.</span></span>
* <span data-ttu-id="c8c53-120">Il tipo dichiara un metodo che corrisponde alla firma del metodo del callback.</span><span class="sxs-lookup"><span data-stu-id="c8c53-120">The type declares a method matching the method signature of the callback.</span></span>
* <span data-ttu-id="c8c53-121">Il nome dello struct non deve essere user-costruibile (come per le altre strutture di supporto generate internamente).</span><span class="sxs-lookup"><span data-stu-id="c8c53-121">The name of the struct should not be user-constructible (as we do with other internally generated backing structures).</span></span>
 * <span data-ttu-id="c8c53-122">Ad esempio: i buffer a dimensione fissa generano uno struct con un nome nel formato `<name>e__FixedBuffer` (`<` e `>` fanno parte dell'identificatore e rendono l'identificatore non costruibile in C#, ma ancora utilizzabile in il).</span><span class="sxs-lookup"><span data-stu-id="c8c53-122">For example: fixed size buffers generate a struct with a name in the format of `<name>e__FixedBuffer` (`<` and `>` are part of the identifier and make the identifier not constructible in C#, but still useable in IL).</span></span>
* <span data-ttu-id="c8c53-123">Il nome della dichiarazione del metodo deve essere un nome noto usato in tutti i tipi delegati statici (in questo modo il compilatore può conoscere il nome da cercare quando si determina la firma).</span><span class="sxs-lookup"><span data-stu-id="c8c53-123">The name of the method declaration should be a well known name used across all static delegate types (this allows the compiler to know the name to look for when determining the signature).</span></span>

<span data-ttu-id="c8c53-124">Il valore del delegato statico può essere associato solo a un metodo statico che corrisponde alla firma del callback.</span><span class="sxs-lookup"><span data-stu-id="c8c53-124">The value of the static delegate can only be bound to a static method that matches the signature of the callback.</span></span>

<span data-ttu-id="c8c53-125">Il concatenamento di callback non è supportato.</span><span class="sxs-lookup"><span data-stu-id="c8c53-125">Chaining callbacks together is not supported.</span></span>

<span data-ttu-id="c8c53-126">La chiamata del callback verrebbe implementata dall'istruzione `calli`.</span><span class="sxs-lookup"><span data-stu-id="c8c53-126">Invocation of the callback would be implemented by the `calli` instruction.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="c8c53-127">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="c8c53-127">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="c8c53-128">I delegati statici non funzionano con le API esistenti che usano i delegati regolari (è necessario eseguire il wrapping del delegato statico definito in un delegato normale della stessa firma).</span><span class="sxs-lookup"><span data-stu-id="c8c53-128">Static Delegates would not work with existing APIs that use regular delegates (one would need to wrap said static delegate in a regular delegate of the same signature).</span></span>
* <span data-ttu-id="c8c53-129">Dato che `System.Delegate` viene rappresentata internamente come un set di campi `object` e `IntPtr` (http://source.dot.net/#System.Private.CoreLib/src/System/Delegate.cs), è possibile consentire la conversione implicita di un delegato statico in un `System.Delegate` con una firma del metodo corrispondente.</span><span class="sxs-lookup"><span data-stu-id="c8c53-129">Given that `System.Delegate` is represented internally as a set of `object` and `IntPtr` fields (http://source.dot.net/#System.Private.CoreLib/src/System/Delegate.cs), it would be possible to allow implicit conversion of a static delegate to a `System.Delegate` that has a matching method signature.</span></span> <span data-ttu-id="c8c53-130">È anche possibile fornire una conversione esplicita nella direzione opposta, purché il `System.Delegate` sia conforme a tutti i requisiti di un delegato statico.</span><span class="sxs-lookup"><span data-stu-id="c8c53-130">It would also be possible to provide an explicit conversion in the opposite direction, provided the `System.Delegate` conformed to all the requirements of being a static delegate.</span></span>

<span data-ttu-id="c8c53-131">È necessario lavoro aggiuntivo per rendere il delegato statico prontamente utilizzabile nel Framework di base.</span><span class="sxs-lookup"><span data-stu-id="c8c53-131">Additional work would be needed to make Static Delegate readily usable in the core framework.</span></span>

## <a name="alternatives"></a><span data-ttu-id="c8c53-132">Alternativi</span><span class="sxs-lookup"><span data-stu-id="c8c53-132">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="c8c53-133">TBD</span><span class="sxs-lookup"><span data-stu-id="c8c53-133">TBD</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="c8c53-134">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="c8c53-134">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="c8c53-135">Quali parti della progettazione sono ancora da definire?</span><span class="sxs-lookup"><span data-stu-id="c8c53-135">What parts of the design are still TBD?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="c8c53-136">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="c8c53-136">Design meetings</span></span>

<span data-ttu-id="c8c53-137">Collegamento alle note sulla progettazione che interessano questa proposta e descrivono in una frase per ogni modifica apportata.</span><span class="sxs-lookup"><span data-stu-id="c8c53-137">Link to design notes that affect this proposal, and describe in one sentence for each what changes they led to.</span></span>


