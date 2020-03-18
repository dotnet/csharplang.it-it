---
ms.openlocfilehash: b51f27b2f58fd19851c80beb9cedcbd32b80b165
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484587"
---
# <a name="fixed-sized-buffers"></a><span data-ttu-id="7aa7c-101">Buffer a dimensione fissa</span><span class="sxs-lookup"><span data-stu-id="7aa7c-101">Fixed Sized Buffers</span></span>

* <span data-ttu-id="7aa7c-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="7aa7c-102">[x] Proposed</span></span>
* <span data-ttu-id="7aa7c-103">[] Prototipo: non avviato</span><span class="sxs-lookup"><span data-stu-id="7aa7c-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="7aa7c-104">[] Implementazione: non avviata</span><span class="sxs-lookup"><span data-stu-id="7aa7c-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="7aa7c-105">[] Specifica: non avviata</span><span class="sxs-lookup"><span data-stu-id="7aa7c-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="7aa7c-106">Summary</span><span class="sxs-lookup"><span data-stu-id="7aa7c-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="7aa7c-107">Fornire un meccanismo generico e sicuro per la C# dichiarazione di buffer a dimensione fissa al linguaggio.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-107">Provide a general-purpose and safe mechanism for declaring fixed sized buffers to the C# language.</span></span>

## <a name="motivation"></a><span data-ttu-id="7aa7c-108">Motivazione</span><span class="sxs-lookup"><span data-stu-id="7aa7c-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="7aa7c-109">Attualmente, gli utenti hanno la possibilità di creare buffer a dimensione fissa in un contesto non sicuro.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-109">Today, users have the ability to create fixed-sized buffers in an unsafe-context.</span></span> <span data-ttu-id="7aa7c-110">Tuttavia, è necessario che l'utente gestisca i puntatori, esegua manualmente i controlli dei limiti e supporti solo un set limitato di tipi (`bool`, `byte`, `char`, `short`, `int`, `long`, `sbyte`, `ushort`, `uint`, `ulong`, `float`e `double`).</span><span class="sxs-lookup"><span data-stu-id="7aa7c-110">However, this requires the user to deal with pointers, manually perform bounds checks, and only supports a limited set of types (`bool`, `byte`, `char`, `short`, `int`, `long`, `sbyte`, `ushort`, `uint`, `ulong`, `float`, and `double`).</span></span>

<span data-ttu-id="7aa7c-111">Il problema più comune è che i buffer a dimensione fissa non possono essere indicizzati in codice sicuro.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-111">The most common complaint is that fixed-size buffers cannot be indexed in safe code.</span></span> <span data-ttu-id="7aa7c-112">L'impossibilità di usare più tipi è il secondo.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-112">Inability to use more types is the second.</span></span>

<span data-ttu-id="7aa7c-113">Con alcune modifiche minime, è possibile fornire buffer a dimensione fissa per utilizzo generico che supportano qualsiasi tipo, può essere usato in un contesto sicuro e avere eseguito il controllo dei limiti automatici.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-113">With a few minor tweaks, we could provide general-purpose fixed-sized buffers which support any type, can be used in a safe context, and have automatic bounds checking performed.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="7aa7c-114">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="7aa7c-114">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="7aa7c-115">Si dichiarerebbe un buffer a dimensione fissa sicuro tramite il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="7aa7c-115">One would declare a safe fixed-sized buffer via the following:</span></span>

```csharp
public fixed DXGI_RGB GammaCurve[1025];
```

<span data-ttu-id="7aa7c-116">La dichiarazione verrebbe convertita in una rappresentazione interna da parte del compilatore, simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="7aa7c-116">The declaration would get translated into an internal representation by the compiler that is similar to the following</span></span>

```csharp
[FixedBuffer(typeof(DXGI_RGB), 1024)]
public ConsoleApp1.<Buffer>e__FixedBuffer_1024<DXGI_RGB> GammaCurve;

// Pack = 0 is the default packing and should result in indexable layout.
[CompilerGenerated, UnsafeValueType, StructLayout(LayoutKind.Sequential, Pack = 0)]
struct <Buffer>e__FixedBuffer_1024<T>
{
    private T _e0;
    private T _e1;
    // _e2 ... _e1023
    private T _e1024;

    public ref T this[int index] => ref (uint)index <= 1024u ?
                                         ref RefAdd<T>(ref _e0, index):
                                         throw new IndexOutOfRange();
}
```

<span data-ttu-id="7aa7c-117">Poiché tali buffer a dimensione fissa non richiedono più l'uso di `fixed`, è opportuno consentire qualsiasi tipo di elemento.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-117">Since such fixed-sized buffers no longer require use of `fixed`, it makes sense to allow any element type.</span></span>  

> <span data-ttu-id="7aa7c-118">Nota: `fixed` sarà ancora supportato, ma solo se il tipo di elemento è `blittable`</span><span class="sxs-lookup"><span data-stu-id="7aa7c-118">NOTE: `fixed` will still be supported, but only if the element type is `blittable`</span></span>

## <a name="drawbacks"></a><span data-ttu-id="7aa7c-119">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="7aa7c-119">Drawbacks</span></span>
[drawbacks]: #drawbacks

* <span data-ttu-id="7aa7c-120">È possibile che si verifichino problemi con la compatibilità con le versioni precedenti, ma dato che i buffer a dimensione fissa esistenti funzionano solo con una selezione di tipi primitivi, è possibile che il compilatore continui a funzionare solo se l'utente considera il buffer fisso come puntatore.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-120">There could be some challenges with backwards compatibility, but given that the existing fixed-sized buffers only work with a selection of primitive types, it should be possible for the compiler to continue "just-working" if the user treats the fixed-buffer as a pointer.</span></span>
* <span data-ttu-id="7aa7c-121">Costrutti incompatibili potrebbero dover usare una codifica `v2` leggermente diversa per nascondere i campi del compilatore precedente.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-121">Incompatible constructs may need to use slightly different `v2` encoding to hide the fields from old compiler.</span></span>
* <span data-ttu-id="7aa7c-122">La compressione non è ben definita nella specifica IL per i tipi generici.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-122">Packing is not well defined in IL spec for generic types.</span></span> <span data-ttu-id="7aa7c-123">Mentre l'approccio dovrebbe funzionare, verrà eseguito il bordering in caso di comportamento non documentato.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-123">While the approach should work, we will be bordering on undocumented behavior.</span></span> <span data-ttu-id="7aa7c-124">Dobbiamo renderlo documentato e verificare che altri JIT come mono hanno lo stesso comportamento.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-124">We should make that documented and make sure other JITs like Mono have the same behavior.</span></span>
* <span data-ttu-id="7aa7c-125">Se si specifica un tipo separato per ogni lunghezza (un possibile altro per i campi `readonly`, se supportato), i metadati avranno un effetto sui metadati.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-125">Specifying a separate type for every length (an possibly another for `readonly` fields, if supported) will have impact on metadata.</span></span> <span data-ttu-id="7aa7c-126">Verrà associato dal numero di matrici di dimensioni diverse nell'app specificata.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-126">It will be bound by the number of arrays of different sizes in the given app.</span></span>
* <span data-ttu-id="7aa7c-127">`ref` Math non è formalmente verificabile (poiché non è sicura).</span><span class="sxs-lookup"><span data-stu-id="7aa7c-127">`ref` math is not formally verifiable (since it is unsafe).</span></span> <span data-ttu-id="7aa7c-128">Sarà necessario trovare un modo per aggiornare le regole di verifica per sapere che l'uso è accettabile.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-128">We will need to find a way to update verification rules to know that our use is ok.</span></span>

## <a name="alternatives"></a><span data-ttu-id="7aa7c-129">Alternativi</span><span class="sxs-lookup"><span data-stu-id="7aa7c-129">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="7aa7c-130">Dichiarare manualmente le strutture e usare codice unsafe per costruire gli indicizzatori.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-130">Manually declare your structures and use unsafe code to construct indexers.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="7aa7c-131">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="7aa7c-131">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="7aa7c-132">è necessario consentire la `readonly`?</span><span class="sxs-lookup"><span data-stu-id="7aa7c-132">should we allow `readonly`?</span></span>  <span data-ttu-id="7aa7c-133">(con indicizzatore ReadOnly)</span><span class="sxs-lookup"><span data-stu-id="7aa7c-133">(with readonly indexer)</span></span>
- <span data-ttu-id="7aa7c-134">è necessario consentire gli inizializzatori di matrice?</span><span class="sxs-lookup"><span data-stu-id="7aa7c-134">should we allow array initializers?</span></span>
- <span data-ttu-id="7aa7c-135">è necessario `fixed` parola chiave?</span><span class="sxs-lookup"><span data-stu-id="7aa7c-135">is `fixed` keyword necessary?</span></span>
- <span data-ttu-id="7aa7c-136">`foreach`?</span><span class="sxs-lookup"><span data-stu-id="7aa7c-136">`foreach`?</span></span>
- <span data-ttu-id="7aa7c-137">solo i campi di istanza negli struct?</span><span class="sxs-lookup"><span data-stu-id="7aa7c-137">only instance fields in structs?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="7aa7c-138">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="7aa7c-138">Design meetings</span></span>

<span data-ttu-id="7aa7c-139">Collegamento alle note sulla progettazione che interessano questa proposta e descrivono in una frase per ogni modifica apportata.</span><span class="sxs-lookup"><span data-stu-id="7aa7c-139">Link to design notes that affect this proposal, and describe in one sentence for each what changes they led to.</span></span>