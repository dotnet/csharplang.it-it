---
ms.openlocfilehash: 2070cf3b3269585055791adc3427cbd134df444d
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484657"
---
# <a name="pattern-based-fixed-statement"></a><span data-ttu-id="0d236-101">Istruzione `fixed` basata su criteri</span><span class="sxs-lookup"><span data-stu-id="0d236-101">Pattern-based `fixed` statement</span></span>

## <a name="summary"></a><span data-ttu-id="0d236-102">Summary</span><span class="sxs-lookup"><span data-stu-id="0d236-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="0d236-103">Introdurre un modello che consenta ai tipi di partecipare alle istruzioni `fixed`.</span><span class="sxs-lookup"><span data-stu-id="0d236-103">Introduce a pattern that would allow types to participate in `fixed` statements.</span></span> 

## <a name="motivation"></a><span data-ttu-id="0d236-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="0d236-104">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="0d236-105">Il linguaggio fornisce un meccanismo per bloccare i dati gestiti e ottenere un puntatore nativo al buffer sottostante.</span><span class="sxs-lookup"><span data-stu-id="0d236-105">The language provides a mechanism for pinning managed data and obtain a native pointer to the underlying buffer.</span></span>

```csharp
fixed(byte* ptr = byteArray)
{
   // ptr is a native pointer to the first element of the array
   // byteArray is protected from being moved/collected by the GC for the duration of this block 
}

```

<span data-ttu-id="0d236-106">Il set di tipi che possono partecipare a `fixed` è hardcoded e limitato a matrici e `System.String`.</span><span class="sxs-lookup"><span data-stu-id="0d236-106">The set of types that can participate in `fixed` is hardcoded and limited to arrays and `System.String`.</span></span> <span data-ttu-id="0d236-107">Il hardcoded di tipi "speciali" non viene ridimensionato quando vengono introdotte nuove primitive, ad esempio `ImmutableArray<T>`, `Span<T>``Utf8String`.</span><span class="sxs-lookup"><span data-stu-id="0d236-107">Hardcoding "special" types does not scale when new primitives such as `ImmutableArray<T>`, `Span<T>`, `Utf8String` are introduced.</span></span> 

<span data-ttu-id="0d236-108">Inoltre, la soluzione corrente per `System.String` si basa su un'API piuttosto rigida.</span><span class="sxs-lookup"><span data-stu-id="0d236-108">In addition, the current solution for `System.String` relies on a fairly rigid API.</span></span> <span data-ttu-id="0d236-109">La forma dell'API implica che `System.String` è un oggetto contiguo che incorpora i dati codificati UTF16 a un offset fisso dall'intestazione dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="0d236-109">The shape of the API implies that `System.String` is a contiguous object that embeds UTF16 encoded data at a fixed offset from the object header.</span></span> <span data-ttu-id="0d236-110">Questo approccio è stato trovato problematico in diverse proposte che potrebbero richiedere modifiche al layout sottostante.</span><span class="sxs-lookup"><span data-stu-id="0d236-110">Such approach has been found problematic in several proposals that could require changes to the underlying layout.</span></span> <span data-ttu-id="0d236-111">Sarebbe opportuno poter passare a un elemento più flessibile che separa `System.String` oggetto dalla relativa rappresentazione interna allo scopo di interoperabilità non gestita.</span><span class="sxs-lookup"><span data-stu-id="0d236-111">It would be desirable to be able to switch to something more flexible that decouples `System.String` object from its internal representation for the purpose of unmanaged interop.</span></span> 

## <a name="detailed-design"></a><span data-ttu-id="0d236-112">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="0d236-112">Detailed design</span></span>
[design]: #detailed-design

## <a name="pattern"></a><span data-ttu-id="0d236-113">*Modello*</span><span class="sxs-lookup"><span data-stu-id="0d236-113">*Pattern*</span></span> ##
<span data-ttu-id="0d236-114">Una soluzione valida "Fixed" basata su modelli deve:</span><span class="sxs-lookup"><span data-stu-id="0d236-114">A viable pattern-based “fixed” need to:</span></span>
-   <span data-ttu-id="0d236-115">Fornire i riferimenti gestiti per aggiungere l'istanza e per inizializzare il puntatore (preferibilmente si tratta dello stesso riferimento)</span><span class="sxs-lookup"><span data-stu-id="0d236-115">Provide the managed references to pin the instance and to initialize the pointer (preferably this is the same reference)</span></span>
-   <span data-ttu-id="0d236-116">Comunica in modo univoco il tipo dell'elemento non gestito (ad esempio "char" per "String")</span><span class="sxs-lookup"><span data-stu-id="0d236-116">Convey unambiguously the type of the unmanaged element   (i.e. “char” for “string”)</span></span>
-   <span data-ttu-id="0d236-117">Prescrivere il comportamento nel caso "Empty" quando non vi sono elementi a cui fare riferimento.</span><span class="sxs-lookup"><span data-stu-id="0d236-117">Prescribe the behavior in "empty" case when there is nothing to refer to.</span></span> 
-   <span data-ttu-id="0d236-118">Non deve effettuare il push degli autori di API nelle decisioni di progettazione che danneggiano l'uso del tipo al di fuori del `fixed`.</span><span class="sxs-lookup"><span data-stu-id="0d236-118">Should not push API authors toward design decisions that hurt the use of the type outside of `fixed`.</span></span>

<span data-ttu-id="0d236-119">Ritengo che l'esempio precedente possa essere soddisfatto riconoscendo un membro con nome Ref returning speciale: `ref [readonly] T GetPinnableReference()`.</span><span class="sxs-lookup"><span data-stu-id="0d236-119">I think the above could be satisfied by recognizing a specially named ref-returning member: `ref [readonly] T GetPinnableReference()`.</span></span>

<span data-ttu-id="0d236-120">Per poter essere utilizzato dall'istruzione `fixed` è necessario che siano soddisfatte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="0d236-120">In order to be used by the `fixed` statement the following conditions must be met:</span></span>

1. <span data-ttu-id="0d236-121">Per un tipo è specificato un solo membro.</span><span class="sxs-lookup"><span data-stu-id="0d236-121">There is only one such member provided for a type.</span></span>
1. <span data-ttu-id="0d236-122">Restituisce per `ref` o `ref readonly`.</span><span class="sxs-lookup"><span data-stu-id="0d236-122">Returns by `ref` or `ref readonly`.</span></span> <span data-ttu-id="0d236-123">(`readonly` è consentito, in modo che gli autori di tipi non modificabili/ReadOnly possano implementare il modello senza aggiungere un'API scrivibile che può essere usata in codice sicuro)</span><span class="sxs-lookup"><span data-stu-id="0d236-123">(`readonly` is permitted so that authors of immutable/readonly types could implement the pattern without adding writeable API that could be used in safe code)</span></span>
1. <span data-ttu-id="0d236-124">T è un tipo non gestito.</span><span class="sxs-lookup"><span data-stu-id="0d236-124">T is an unmanaged type.</span></span>
<span data-ttu-id="0d236-125">Poiché `T*` diventa il tipo di puntatore.</span><span class="sxs-lookup"><span data-stu-id="0d236-125">(since `T*` becomes the pointer type.</span></span> <span data-ttu-id="0d236-126">La restrizione verrà espansa naturalmente se/quando la nozione di "non gestito" viene espansa)</span><span class="sxs-lookup"><span data-stu-id="0d236-126">The restriction will naturally expand if/when the notion of "unmanaged" is expanded)</span></span>
1. <span data-ttu-id="0d236-127">Restituisce `nullptr` gestiti quando non sono presenti dati da aggiungere, probabilmente il modo più economico per comunicarne il vuoto.</span><span class="sxs-lookup"><span data-stu-id="0d236-127">Returns managed `nullptr` when there is no data to pin – probably the cheapest way to convey emptiness.</span></span>
<span data-ttu-id="0d236-128">Si noti che la stringa "" restituisce un riferimento a' \ 0' poiché le stringhe hanno terminazione null.</span><span class="sxs-lookup"><span data-stu-id="0d236-128">(note that “” string returns a ref to '\0' since strings are null-terminated)</span></span>

<span data-ttu-id="0d236-129">In alternativa, per la `#3` è possibile consentire il risultato in casi vuoti non definiti o specifici dell'implementazione.</span><span class="sxs-lookup"><span data-stu-id="0d236-129">Alternatively for the `#3` we can allow the result in empty cases be undefined or implementation-specific.</span></span> <span data-ttu-id="0d236-130">Questo, tuttavia, può rendere l'API più pericolosa e soggetta a abusi e oneri di compatibilità indesiderati.</span><span class="sxs-lookup"><span data-stu-id="0d236-130">That, however, may make the API more dangerous and prone to abuse and unintended compatibility burdens.</span></span> 

## <a name="translation"></a><span data-ttu-id="0d236-131">*Traduzione*</span><span class="sxs-lookup"><span data-stu-id="0d236-131">*Translation*</span></span> ##

```csharp
fixed(byte* ptr = thing)
{ 
    // <BODY>
}
```

<span data-ttu-id="0d236-132">diventa lo pseudocodice seguente (non tutti esprimibile in C#)</span><span class="sxs-lookup"><span data-stu-id="0d236-132">becomes the following pseudocode (not all expressible in C#)</span></span>

```csharp
byte* ptr;
// specially decorated "pinned" IL local slot, not visible to user code.
pinned ref byte _pinned;

try
{
    // NOTE: null check is omitted for value types 
    // NOTE: `thing` is evaluated only once (temporary is introduced if necessary) 
    if (thing != null)
    {
        // obtain and "pin" the reference
        _pinned = ref thing.GetPinnableReference();

        // unsafe cast in IL
        ptr = (byte*)_pinned;
    }
    else
    {
        ptr = default(byte*);
    }

    // <BODY> 
}
finally   // finally can be omitted when not observable
{
    // "unpin" the object
    _pinned = nullptr;
}
```

## <a name="drawbacks"></a><span data-ttu-id="0d236-133">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="0d236-133">Drawbacks</span></span>
[drawbacks]: #drawbacks

- <span data-ttu-id="0d236-134">GetPinnableReference è progettato per essere usato solo in `fixed`, ma nulla ne impedisce l'uso nel codice sicuro, quindi l'implementatore deve tenere presente questo aspetto.</span><span class="sxs-lookup"><span data-stu-id="0d236-134">GetPinnableReference is intended to be used only in `fixed`, but nothing prevents its use in safe code, so implementor must keep that in mind.</span></span>

## <a name="alternatives"></a><span data-ttu-id="0d236-135">Alternativi</span><span class="sxs-lookup"><span data-stu-id="0d236-135">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="0d236-136">Gli utenti possono introdurre GetPinnableReference o membri simili e usarli come</span><span class="sxs-lookup"><span data-stu-id="0d236-136">Users can introduce GetPinnableReference or similar member and use it as</span></span>
 
```csharp
fixed(byte* ptr = thing.GetPinnableReference())
{ 
    // <BODY>
}
```

<span data-ttu-id="0d236-137">Non esiste alcuna soluzione per `System.String` se si desidera una soluzione alternativa.</span><span class="sxs-lookup"><span data-stu-id="0d236-137">There is no solution for `System.String` if alternative solution is desired.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="0d236-138">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="0d236-138">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="0d236-139">[] Comportamento nello stato "vuoto".</span><span class="sxs-lookup"><span data-stu-id="0d236-139">[ ] Behavior in "empty" state.</span></span><span data-ttu-id="0d236-140"> - `nullptr` o `undefined`?</span><span class="sxs-lookup"><span data-stu-id="0d236-140"> - `nullptr` or `undefined` ?</span></span> 
- <span data-ttu-id="0d236-141">[] Se i metodi di estensione devono essere considerati?</span><span class="sxs-lookup"><span data-stu-id="0d236-141">[ ] Should the extension methods be considered ?</span></span> 
- <span data-ttu-id="0d236-142">[] Se viene rilevato un modello in `System.String`, in caso di superamento?</span><span class="sxs-lookup"><span data-stu-id="0d236-142">[ ] If a pattern is detected on `System.String`, should it win over ?</span></span> 

## <a name="design-meetings"></a><span data-ttu-id="0d236-143">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="0d236-143">Design meetings</span></span>

<span data-ttu-id="0d236-144">Nessuno ancora.</span><span class="sxs-lookup"><span data-stu-id="0d236-144">None yet.</span></span> 
