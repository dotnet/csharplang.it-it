---
ms.openlocfilehash: 1785ed77ef6a4a6ab3c9a1eef31a3d255e402f34
ms.sourcegitcommit: 660f5a2e1c5c6e3b181a82d0b2bdb17821ef9b84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100489032"
---
# <a name="unmanaged-type-constraint"></a><span data-ttu-id="c04ba-101">Vincolo di tipo non gestito</span><span class="sxs-lookup"><span data-stu-id="c04ba-101">Unmanaged type constraint</span></span>

## <a name="summary"></a><span data-ttu-id="c04ba-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="c04ba-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="c04ba-103">La funzionalità di vincolo non gestito consente di applicare il linguaggio alla classe di tipi noti come "tipi non gestiti" nelle specifiche del linguaggio C#.  Questa operazione è definita nella sezione 18,2 come tipo che non è un tipo riferimento e non contiene campi di tipo riferimento a qualsiasi livello di annidamento.</span><span class="sxs-lookup"><span data-stu-id="c04ba-103">The unmanaged constraint feature will give language enforcement to the class of types known as "unmanaged types" in the C# language spec.  This is defined in section 18.2 as a type which is not a reference type and doesn't contain reference type fields at any level of nesting.</span></span>  

## <a name="motivation"></a><span data-ttu-id="c04ba-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="c04ba-104">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="c04ba-105">Il motivo principale è quello di semplificare la creazione di codice di interoperabilità di basso livello in C#.</span><span class="sxs-lookup"><span data-stu-id="c04ba-105">The primary motivation is to make it easier to author low level interop code in C#.</span></span> <span data-ttu-id="c04ba-106">I tipi non gestiti sono uno dei principali blocchi predefiniti per il codice di interoperabilità, ma la mancanza di supporto nei generics rende impossibile creare routine riutilizzabili in tutti i tipi non gestiti.</span><span class="sxs-lookup"><span data-stu-id="c04ba-106">Unmanaged types are one of the core building blocks for interop code, yet the lack of support in generics makes it impossible to create re-usable routines across all unmanaged types.</span></span> <span data-ttu-id="c04ba-107">Al contrario, gli sviluppatori devono creare lo stesso codice della piastra calda per ogni tipo non gestito nella libreria:</span><span class="sxs-lookup"><span data-stu-id="c04ba-107">Instead developers are forced to author the same boiler plate code for every unmanaged type in their library:</span></span>

```csharp
int Hash(Point point) { ... } 
int Hash(TimeSpan timeSpan) { ... } 
```

<span data-ttu-id="c04ba-108">Per abilitare questo tipo di scenario, il linguaggio introdurrà un nuovo vincolo: non gestito:</span><span class="sxs-lookup"><span data-stu-id="c04ba-108">To enable this type of scenario the language will be introducing a new constraint: unmanaged:</span></span>

```csharp
void Hash<T>(T value) where T : unmanaged
{
    ...
}
```

<span data-ttu-id="c04ba-109">Questo vincolo può essere soddisfatto solo da tipi che rientrano nella definizione di tipo non gestito nelle specifiche del linguaggio C#. Un altro modo per esaminarlo è che un tipo soddisfa il vincolo non gestito se può essere usato anche come puntatore.</span><span class="sxs-lookup"><span data-stu-id="c04ba-109">This constraint can only be met by types which fit into the unmanaged type definition in the C# language spec. Another way of looking at it is that a type satisfies the unmanaged constraint if it can also be used as a pointer.</span></span> 

```csharp
Hash(new Point()); // Okay 
Hash(42); // Okay
Hash("hello") // Error: Type string does not satisfy the unmanaged constraint
```

<span data-ttu-id="c04ba-110">I parametri di tipo con il vincolo non gestito possono usare tutte le funzionalità disponibili per i tipi non gestiti: puntatori, fissi e così via...</span><span class="sxs-lookup"><span data-stu-id="c04ba-110">Type parameters with the unmanaged constraint can use all the features available to unmanaged types: pointers, fixed, etc ...</span></span> 

```csharp
void Hash<T>(T value) where T : unmanaged
{
    // Okay
    fixed (T* p = &value) 
    { 
        ...
    }
}
```

<span data-ttu-id="c04ba-111">Questo vincolo consente inoltre di ottenere conversioni efficienti tra dati strutturati e flussi di byte.</span><span class="sxs-lookup"><span data-stu-id="c04ba-111">This constraint will also make it possible to have efficient conversions between structured data and streams of bytes.</span></span> <span data-ttu-id="c04ba-112">Si tratta di un'operazione comune negli stack di rete e nei livelli di serializzazione:</span><span class="sxs-lookup"><span data-stu-id="c04ba-112">This is an operation that is common in networking stacks and serialization layers:</span></span>

```csharp
Span<byte> Convert<T>(ref T value) where T : unmanaged 
{
    ...
}
```

<span data-ttu-id="c04ba-113">Tali routine sono vantaggiose perché sono dimostrate sicure in fase di compilazione e di allocazione gratuita.</span><span class="sxs-lookup"><span data-stu-id="c04ba-113">Such routines are advantageous because they are provably safe at compile time and allocation free.</span></span>  <span data-ttu-id="c04ba-114">Attualmente gli autori di interoperabilità non possono eseguire questa operazione (anche se si trova a un livello in cui Perf è fondamentale).</span><span class="sxs-lookup"><span data-stu-id="c04ba-114">Interop authors today can not do this (even though it's at a layer where perf is critical).</span></span>  <span data-ttu-id="c04ba-115">È invece necessario affidarsi alle routine di allocazione con controlli di runtime costosi per verificare che i valori siano correttamente non gestiti.</span><span class="sxs-lookup"><span data-stu-id="c04ba-115">Instead they need to rely on allocating routines that have expensive runtime checks to verify values are correctly unmanaged.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="c04ba-116">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="c04ba-116">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="c04ba-117">Il linguaggio introdurrà un nuovo vincolo denominato `unmanaged` .</span><span class="sxs-lookup"><span data-stu-id="c04ba-117">The language will introduce a new constraint named `unmanaged`.</span></span> <span data-ttu-id="c04ba-118">Per soddisfare questo vincolo, un tipo deve essere uno struct e tutti i campi del tipo devono rientrare in una delle categorie seguenti:</span><span class="sxs-lookup"><span data-stu-id="c04ba-118">In order to satisfy this constraint a type must be a struct and all the fields of the type must fall into one of the following categories:</span></span>

- <span data-ttu-id="c04ba-119">Hanno il tipo,,,,,,,, `sbyte` `byte` `short` `ushort` `int` `uint` `long` `ulong` `char` , `float` , `double` , `decimal` , `bool` `IntPtr` o `UIntPtr` .</span><span class="sxs-lookup"><span data-stu-id="c04ba-119">Have the type `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `IntPtr` or `UIntPtr`.</span></span>
- <span data-ttu-id="c04ba-120">Essere di qualsiasi `enum` tipo.</span><span class="sxs-lookup"><span data-stu-id="c04ba-120">Be any `enum` type.</span></span>
- <span data-ttu-id="c04ba-121">È un tipo di puntatore.</span><span class="sxs-lookup"><span data-stu-id="c04ba-121">Be a pointer type.</span></span>
- <span data-ttu-id="c04ba-122">Essere uno struct definito dall'utente che soddisfi il `unmanaged` vincolo.</span><span class="sxs-lookup"><span data-stu-id="c04ba-122">Be a user defined struct that satisfies the `unmanaged` constraint.</span></span>

<span data-ttu-id="c04ba-123">I campi di istanza generati dal compilatore, ad esempio le proprietà implementate automaticamente, devono soddisfare anche tali vincoli.</span><span class="sxs-lookup"><span data-stu-id="c04ba-123">Compiler generated instance fields, such as those backing auto-implemented properties, must also meet these constraints.</span></span> 

<span data-ttu-id="c04ba-124">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c04ba-124">For example:</span></span>

```csharp
// Unmanaged type
struct Point 
{ 
    int X;
    int Y {get; set;}
}

// Not an unmanaged type
struct Student 
{ 
    string FirstName;
    string LastName;
}
``` 

<span data-ttu-id="c04ba-125">Il `unmanaged` vincolo non può essere combinato `struct` con `class` o `new()` .</span><span class="sxs-lookup"><span data-stu-id="c04ba-125">The `unmanaged` constraint cannot be combined with `struct`, `class` or `new()`.</span></span> <span data-ttu-id="c04ba-126">Questa restrizione deriva dal fatto che `unmanaged` implica di `struct` conseguenza gli altri vincoli non hanno senso.</span><span class="sxs-lookup"><span data-stu-id="c04ba-126">This restriction derives from the fact that `unmanaged` implies `struct` hence the other constraints do not make sense.</span></span>

<span data-ttu-id="c04ba-127">Il `unmanaged` vincolo non viene applicato da CLR, solo dal linguaggio.</span><span class="sxs-lookup"><span data-stu-id="c04ba-127">The `unmanaged` constraint is not enforced by CLR, only by the language.</span></span> <span data-ttu-id="c04ba-128">Per impedire l'uso scorretto da altri linguaggi, i metodi che presentano questo vincolo saranno protetti da un mod-req. Questo impedirà ad altri linguaggi di usare argomenti di tipo che non sono tipi non gestiti.</span><span class="sxs-lookup"><span data-stu-id="c04ba-128">To prevent mis-use by other languages, methods which have this constraint will be protected by a mod-req. This will prevent other languages from using type arguments which are not unmanaged types.</span></span>

<span data-ttu-id="c04ba-129">Il token `unmanaged` nel vincolo non è una parola chiave, né una parola chiave contestuale.</span><span class="sxs-lookup"><span data-stu-id="c04ba-129">The token `unmanaged` in the constraint is not a keyword, nor a contextual keyword.</span></span> <span data-ttu-id="c04ba-130">È invece come `var` nel caso in cui venga valutato in tale posizione e:</span><span class="sxs-lookup"><span data-stu-id="c04ba-130">Instead it is like `var` in that it is evaluated at that location and will either:</span></span>

- <span data-ttu-id="c04ba-131">Eseguire l'associazione a un tipo definito dall'utente o a cui si fa riferimento `unmanaged` : questa operazione verrà trattata come qualsiasi altro vincolo di tipo denominato gestito.</span><span class="sxs-lookup"><span data-stu-id="c04ba-131">Bind to user defined or referenced type named `unmanaged`: This will be treated just as any other named type constraint is treated.</span></span> 
- <span data-ttu-id="c04ba-132">Associa a nessun tipo: verrà interpretato come `unmanaged` vincolo.</span><span class="sxs-lookup"><span data-stu-id="c04ba-132">Bind to no type: This will be interpreted as the `unmanaged` constraint.</span></span>

<span data-ttu-id="c04ba-133">Se è presente un tipo denominato `unmanaged` ed è disponibile senza qualifica nel contesto corrente, non sarà possibile usare il `unmanaged` vincolo.</span><span class="sxs-lookup"><span data-stu-id="c04ba-133">In the case there is a type named `unmanaged` and it is available without qualification in the current context, then there will be no way to use the `unmanaged` constraint.</span></span> <span data-ttu-id="c04ba-134">Questa operazione è parallela alle regole che racchiudono la funzionalità `var` e i tipi definiti dall'utente con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="c04ba-134">This parallels the rules surrounding the feature `var` and user defined types of the same name.</span></span> 

## <a name="drawbacks"></a><span data-ttu-id="c04ba-135">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="c04ba-135">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="c04ba-136">Lo svantaggio principale di questa funzionalità è che offre un numero ridotto di sviluppatori: in genere autori o Framework di librerie di basso livello.</span><span class="sxs-lookup"><span data-stu-id="c04ba-136">The primary drawback of this feature is that it serves a small number of developers: typically low level library authors or frameworks.</span></span>  <span data-ttu-id="c04ba-137">Quindi, il tempo dedicato al linguaggio è molto prezioso per un numero ridotto di sviluppatori.</span><span class="sxs-lookup"><span data-stu-id="c04ba-137">Hence it's spending precious language time for a small number of developers.</span></span> 

<span data-ttu-id="c04ba-138">Tuttavia, questi Framework sono spesso la base per la maggior parte delle applicazioni .NET.</span><span class="sxs-lookup"><span data-stu-id="c04ba-138">Yet these frameworks are often the basis for the majority of .NET applications out there.</span></span>  <span data-ttu-id="c04ba-139">Di conseguenza, le prestazioni e la correttezza di questo livello possono avere un effetto Ripple sull'ecosistema .NET.</span><span class="sxs-lookup"><span data-stu-id="c04ba-139">Hence performance / correctness wins at this level can have a ripple effect on the .NET ecosystem.</span></span>  <span data-ttu-id="c04ba-140">In questo modo è opportuno considerare la funzionalità anche con i destinatari limitati.</span><span class="sxs-lookup"><span data-stu-id="c04ba-140">This makes the feature worth considering even with the limited audience.</span></span>

## <a name="alternatives"></a><span data-ttu-id="c04ba-141">Alternativi</span><span class="sxs-lookup"><span data-stu-id="c04ba-141">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="c04ba-142">Ci sono due alternative da considerare:</span><span class="sxs-lookup"><span data-stu-id="c04ba-142">There are a couple of alternatives to consider:</span></span>

- <span data-ttu-id="c04ba-143">Status quo: la funzionalità non è giustificata per i propri meriti e gli sviluppatori continuano a usare il comportamento implicito di consenso esplicito.</span><span class="sxs-lookup"><span data-stu-id="c04ba-143">The status quo:  The feature is not justified on its own merits and developers continue to use the implicit opt in behavior.</span></span>

## <a name="questions"></a><span data-ttu-id="c04ba-144">Domande</span><span class="sxs-lookup"><span data-stu-id="c04ba-144">Questions</span></span>
[quesions]: #questions

### <a name="metadata-representation"></a><span data-ttu-id="c04ba-145">Rappresentazione dei metadati</span><span class="sxs-lookup"><span data-stu-id="c04ba-145">Metadata Representation</span></span>

<span data-ttu-id="c04ba-146">Il linguaggio F # codifica il vincolo nel file di firma, il che significa che C# non può riutilizzare la relativa rappresentazione.</span><span class="sxs-lookup"><span data-stu-id="c04ba-146">The F# language encodes the constraint in the signature file which means C# cannot re-use their representation.</span></span> <span data-ttu-id="c04ba-147">Per questo vincolo sarà necessario scegliere un nuovo attributo.</span><span class="sxs-lookup"><span data-stu-id="c04ba-147">A new attribute will need to be chosen for this constraint.</span></span> <span data-ttu-id="c04ba-148">Un metodo con questo vincolo, inoltre, deve essere protetto da un mod-req.</span><span class="sxs-lookup"><span data-stu-id="c04ba-148">Additionally a method which has this constraint must be protected by a mod-req.</span></span>

### <a name="blittable-vs-unmanaged"></a><span data-ttu-id="c04ba-149">Copiabile rispetto a non gestita</span><span class="sxs-lookup"><span data-stu-id="c04ba-149">Blittable vs. Unmanaged</span></span>
<span data-ttu-id="c04ba-150">Il linguaggio F # presenta una [funzionalità molto simile](https://docs.microsoft.com/dotnet/articles/fsharp/language-reference/generics/constraints) che usa la parola chiave unmanaged.</span><span class="sxs-lookup"><span data-stu-id="c04ba-150">The F# language has a very [similar feature](https://docs.microsoft.com/dotnet/articles/fsharp/language-reference/generics/constraints) which uses the keyword unmanaged.</span></span> <span data-ttu-id="c04ba-151">Il nome copiabile deriva dall'utilizzo di Midori.</span><span class="sxs-lookup"><span data-stu-id="c04ba-151">The blittable name comes from the use in Midori.</span></span>  <span data-ttu-id="c04ba-152">È possibile che si desideri predisporre la precedenza e utilizzare invece non gestito.</span><span class="sxs-lookup"><span data-stu-id="c04ba-152">May want to look to precedence here and use unmanaged instead.</span></span> 

<span data-ttu-id="c04ba-153">**Risoluzione** dei problemi Il linguaggio decide di usare non gestito</span><span class="sxs-lookup"><span data-stu-id="c04ba-153">**Resolution** The language decide to use unmanaged</span></span> 

### <a name="verifier"></a><span data-ttu-id="c04ba-154">Verifier</span><span class="sxs-lookup"><span data-stu-id="c04ba-154">Verifier</span></span>

<span data-ttu-id="c04ba-155">Il Verifier/runtime deve essere aggiornato per comprendere l'utilizzo di puntatori a parametri di tipo generico?</span><span class="sxs-lookup"><span data-stu-id="c04ba-155">Does the verifier / runtime need to be updated to understand the use of pointers to generic type parameters?</span></span>  <span data-ttu-id="c04ba-156">Oppure può funzionare semplicemente senza modifiche?</span><span class="sxs-lookup"><span data-stu-id="c04ba-156">Or can it simply work as is without changes?</span></span>

<span data-ttu-id="c04ba-157">**Risoluzione** dei problemi Non sono necessarie modifiche.</span><span class="sxs-lookup"><span data-stu-id="c04ba-157">**Resolution** No changes needed.</span></span> <span data-ttu-id="c04ba-158">Tutti i tipi di puntatore sono semplicemente non verificabili.</span><span class="sxs-lookup"><span data-stu-id="c04ba-158">All pointer types are simply unverifiable.</span></span> 

## <a name="design-meetings"></a><span data-ttu-id="c04ba-159">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="c04ba-159">Design meetings</span></span>

<span data-ttu-id="c04ba-160">n/d</span><span class="sxs-lookup"><span data-stu-id="c04ba-160">n/a</span></span>
