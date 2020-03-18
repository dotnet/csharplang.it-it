---
ms.openlocfilehash: 4e2a536bab00859b003e8d967cb1927a99a9fa21
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484538"
---

# <a name="target-typed-new-expressions"></a><span data-ttu-id="a3505-101">Espressioni di `new` tipizzate come destinazione</span><span class="sxs-lookup"><span data-stu-id="a3505-101">Target-typed `new` expressions</span></span>

* <span data-ttu-id="a3505-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="a3505-102">[x] Proposed</span></span>
* <span data-ttu-id="a3505-103">[prototipo](https://github.com/alrz/roslyn/tree/features/target-typed-new) [x]</span><span class="sxs-lookup"><span data-stu-id="a3505-103">[x] [Prototype](https://github.com/alrz/roslyn/tree/features/target-typed-new)</span></span>
* <span data-ttu-id="a3505-104">[] Implementazione</span><span class="sxs-lookup"><span data-stu-id="a3505-104">[ ] Implementation</span></span>
* <span data-ttu-id="a3505-105">[] Specifica</span><span class="sxs-lookup"><span data-stu-id="a3505-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="a3505-106">Summary</span><span class="sxs-lookup"><span data-stu-id="a3505-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="a3505-107">Non richiede la specifica del tipo per i costruttori quando il tipo è noto.</span><span class="sxs-lookup"><span data-stu-id="a3505-107">Do not require type specification for constructors when the type is known.</span></span> 

## <a name="motivation"></a><span data-ttu-id="a3505-108">Motivazione</span><span class="sxs-lookup"><span data-stu-id="a3505-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="a3505-109">Consente l'inizializzazione del campo senza duplicare il tipo.</span><span class="sxs-lookup"><span data-stu-id="a3505-109">Allow field initialization without duplicating the type.</span></span>
```cs
Dictionary<string, List<int>> field = new() {
    { "item1", new() { 1, 2, 3 } }
};
```
<span data-ttu-id="a3505-110">Consente di omettere il tipo quando può essere dedotto dall'utilizzo.</span><span class="sxs-lookup"><span data-stu-id="a3505-110">Allow omitting the type when it can be inferred from usage.</span></span>
```cs
XmlReader.Create(reader, new() { IgnoreWhitespace = true });
```
<span data-ttu-id="a3505-111">Creare un'istanza di un oggetto senza sillabare il tipo.</span><span class="sxs-lookup"><span data-stu-id="a3505-111">Instantiate an object without spelling out the type.</span></span>
```cs
private readonly static object s_syncObj = new();
```
## <a name="detailed-design"></a><span data-ttu-id="a3505-112">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="a3505-112">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="a3505-113">La sintassi *object_creation_expression* verrebbe modificata per rendere facoltativo il *tipo* quando sono presenti parentesi.</span><span class="sxs-lookup"><span data-stu-id="a3505-113">The *object_creation_expression* syntax would be modified to make the *type* optional when parentheses are present.</span></span> <span data-ttu-id="a3505-114">Questa operazione è necessaria per risolvere l'ambiguità con *anonymous_object_creation_expression*.</span><span class="sxs-lookup"><span data-stu-id="a3505-114">This is required to address the ambiguity with *anonymous_object_creation_expression*.</span></span>
```antlr
object_creation_expression
    : 'new' type? '(' argument_list? ')' object_or_collection_initializer?
    | 'new' type object_or_collection_initializer
    ;
```
<span data-ttu-id="a3505-115">Una `new` tipizzata come destinazione è convertibile in qualsiasi tipo.</span><span class="sxs-lookup"><span data-stu-id="a3505-115">A target-typed `new` is convertible to any type.</span></span> <span data-ttu-id="a3505-116">Di conseguenza, non contribuisce alla risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="a3505-116">As a result, it does not contribute to overload resolution.</span></span> <span data-ttu-id="a3505-117">Questa operazione viene eseguita principalmente per evitare modifiche di rilievo imprevedibili.</span><span class="sxs-lookup"><span data-stu-id="a3505-117">This is mainly to avoid unpredictable breaking changes.</span></span>

<span data-ttu-id="a3505-118">L'elenco di argomenti e le espressioni dell'inizializzatore verranno associati dopo che il tipo è stato determinato.</span><span class="sxs-lookup"><span data-stu-id="a3505-118">The argument list and the initializer expressions will be bound after the type is determined.</span></span>

<span data-ttu-id="a3505-119">Il tipo dell'espressione verrebbe dedotto dal tipo di destinazione, che dovrebbe essere uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="a3505-119">The type of the expression would be inferred from the target-type which would be required to be one of the following:</span></span>

- <span data-ttu-id="a3505-120">**Qualsiasi tipo di struct**</span><span class="sxs-lookup"><span data-stu-id="a3505-120">**Any struct type**</span></span>
- <span data-ttu-id="a3505-121">**Qualsiasi tipo di riferimento**</span><span class="sxs-lookup"><span data-stu-id="a3505-121">**Any reference type**</span></span>
- <span data-ttu-id="a3505-122">**Qualsiasi parametro di tipo** con un costruttore o un vincolo `struct`</span><span class="sxs-lookup"><span data-stu-id="a3505-122">**Any type parameter** with a constructor or a `struct` constraint</span></span>

<span data-ttu-id="a3505-123">con le eccezioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="a3505-123">with the following exceptions:</span></span>

- <span data-ttu-id="a3505-124">**Tipi enum:** non tutti i tipi enum contengono la costante zero, quindi è consigliabile usare il membro enum esplicito.</span><span class="sxs-lookup"><span data-stu-id="a3505-124">**Enum types:** not all enum types contain the constant zero, so it should be desirable to use the explicit enum member.</span></span>
- <span data-ttu-id="a3505-125">**Tipi di interfaccia:** si tratta di una funzionalità di nicchia che deve essere preferibile per citare in modo esplicito il tipo.</span><span class="sxs-lookup"><span data-stu-id="a3505-125">**Interface types:** this is a niche feature and it should be preferable to explicitly mention the type.</span></span>
- <span data-ttu-id="a3505-126">**Tipi di matrice:** le matrici necessitano di una sintassi speciale per fornire la lunghezza.</span><span class="sxs-lookup"><span data-stu-id="a3505-126">**Array types:** arrays need a special syntax to provide the length.</span></span>
- <span data-ttu-id="a3505-127">**Costruttore predefinito struct**: questa regola esclude tutti i tipi primitivi e la maggior parte dei tipi di valore.</span><span class="sxs-lookup"><span data-stu-id="a3505-127">**Struct default constructor**: this rules out all primitive types and most value types.</span></span> <span data-ttu-id="a3505-128">Se si desidera utilizzare il valore predefinito di tali tipi, è possibile scrivere `default`.</span><span class="sxs-lookup"><span data-stu-id="a3505-128">If you wanted to use the default value of such types you could write `default` instead.</span></span>

<span data-ttu-id="a3505-129">Sono esclusi anche tutti gli altri tipi non consentiti nella *object_creation_expression* , ad esempio i tipi di puntatore.</span><span class="sxs-lookup"><span data-stu-id="a3505-129">All the other types that are not permitted in the *object_creation_expression* are excluded as well, for instance, pointer types.</span></span>

> <span data-ttu-id="a3505-130">**Problema di apertura:** è necessario consentire i delegati e le tuple come tipo di destinazione?</span><span class="sxs-lookup"><span data-stu-id="a3505-130">**Open Issue:** should we allow delegates and tuples as the target-type?</span></span>

<span data-ttu-id="a3505-131">Le regole sopra elencate includono i delegati (un tipo di riferimento) e le Tuple (un tipo struct).</span><span class="sxs-lookup"><span data-stu-id="a3505-131">The above rules include delegates (a reference type) and tuples (a struct type).</span></span> <span data-ttu-id="a3505-132">Anche se entrambi i tipi sono costruibile, se il tipo è deducebile, è possibile usare una funzione anonima o un valore letterale di tupla.</span><span class="sxs-lookup"><span data-stu-id="a3505-132">Although both types are constructible, if the type is inferable, an anonymous function or a tuple literal can already be used.</span></span>
```cs
(int a, int b) t = new(1, 2); // "new" is redundant
Action a = new(() => {}); // "new" is redundant

(int a, int b) t = new(); // ruled out by "use of struct default constructor"
Action a = new(); // no constructor found

var x = new() == (1, 2); // ruled out by "use of struct default constructor"
var x = new(1, 2) == (1, 2) // "new" is redundant
```


> <span data-ttu-id="a3505-133">**Problema di apertura:** è necessario consentire `throw new()` con `Exception` come tipo di destinazione?</span><span class="sxs-lookup"><span data-stu-id="a3505-133">**Open Issue:** should we allow `throw new()` with `Exception` as the target-type?</span></span>

<span data-ttu-id="a3505-134">Attualmente `throw null`, ma non `throw default` (anche se avrebbe lo stesso effetto).</span><span class="sxs-lookup"><span data-stu-id="a3505-134">We have `throw null` today, but not `throw default` (though it would have the same effect).</span></span> <span data-ttu-id="a3505-135">D'altra parte, `throw new()` potrebbe essere effettivamente utile come abbreviazione per `throw new Exception(...)`.</span><span class="sxs-lookup"><span data-stu-id="a3505-135">On the other hand, `throw new()` could be actually useful as a shorthand for `throw new Exception(...)`.</span></span> <span data-ttu-id="a3505-136">Si noti che è già consentito dalla specifica corrente.</span><span class="sxs-lookup"><span data-stu-id="a3505-136">Note that it is already allowed by the current specification.</span></span> <span data-ttu-id="a3505-137">`Exception` è un tipo di riferimento e la specifica dell'istruzione throw indica che l'espressione viene convertita in `Exception`.</span><span class="sxs-lookup"><span data-stu-id="a3505-137">`Exception` is a reference type, and the specification for the throw statement says that the expression is converted to `Exception`.</span></span>

> <span data-ttu-id="a3505-138">**Problema aperto:** è possibile consentire l'utilizzo di una `new` tipizzata come destinazione con operatori aritmetici e di confronto definiti dall'utente?</span><span class="sxs-lookup"><span data-stu-id="a3505-138">**Open Issue:** should we allow usages of a target-typed `new` with user-defined comparison and arithmetic operators?</span></span>

<span data-ttu-id="a3505-139">Per il confronto, `default` supporta solo gli operatori di uguaglianza (definiti dall'utente e incorporati).</span><span class="sxs-lookup"><span data-stu-id="a3505-139">For comparison, `default` only supports equality (user-defined and built-in) operators.</span></span> <span data-ttu-id="a3505-140">Potrebbe essere utile supportare anche altri operatori per `new()`?</span><span class="sxs-lookup"><span data-stu-id="a3505-140">Would it make sense to support other operators for `new()` as well?</span></span>

## <a name="drawbacks"></a><span data-ttu-id="a3505-141">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="a3505-141">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="a3505-142">No.</span><span class="sxs-lookup"><span data-stu-id="a3505-142">None.</span></span>

## <a name="alternatives"></a><span data-ttu-id="a3505-143">Alternativi</span><span class="sxs-lookup"><span data-stu-id="a3505-143">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="a3505-144">La maggior parte dei reclami relativi ai tipi troppo lunghi per duplicare l'inizializzazione del campo riguarda gli *argomenti di tipo* , non il tipo stesso, è possibile dedurre solo gli argomenti di tipo come `new Dictionary(...)` (o simile) e dedurre gli argomenti di tipo localmente dagli argomenti o dall'inizializzatore di raccolta.</span><span class="sxs-lookup"><span data-stu-id="a3505-144">Most of complaints about types being too long to duplicate in field initialization is about *type arguments* not the type itself, we could infer only type arguments like `new Dictionary(...)` (or similar) and infer type arguments locally from arguments or the collection initializer.</span></span>

## <a name="questions"></a><span data-ttu-id="a3505-145">Domande</span><span class="sxs-lookup"><span data-stu-id="a3505-145">Questions</span></span>
[questions]: #questions

- <span data-ttu-id="a3505-146">È consigliabile proibire gli utilizzi negli alberi delle espressioni?</span><span class="sxs-lookup"><span data-stu-id="a3505-146">Should we forbid usages in expression trees?</span></span> <span data-ttu-id="a3505-147">non</span><span class="sxs-lookup"><span data-stu-id="a3505-147">(no)</span></span>
- <span data-ttu-id="a3505-148">Interazione tra la funzionalità e gli argomenti `dynamic`</span><span class="sxs-lookup"><span data-stu-id="a3505-148">How the feature interacts with `dynamic` arguments?</span></span> <span data-ttu-id="a3505-149">(nessun trattamento speciale)</span><span class="sxs-lookup"><span data-stu-id="a3505-149">(no special treatment)</span></span>
- <span data-ttu-id="a3505-150">Funzionamento di IntelliSense con `new()`</span><span class="sxs-lookup"><span data-stu-id="a3505-150">How IntelliSense should work with `new()`?</span></span> <span data-ttu-id="a3505-151">(solo quando è presente un solo tipo di destinazione)</span><span class="sxs-lookup"><span data-stu-id="a3505-151">(only when there is a single target-type)</span></span>
## <a name="design-meetings"></a><span data-ttu-id="a3505-152">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="a3505-152">Design meetings</span></span>

- [<span data-ttu-id="a3505-153">LDM-2017-10-18</span><span class="sxs-lookup"><span data-stu-id="a3505-153">LDM-2017-10-18</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-10-18.md#100)
