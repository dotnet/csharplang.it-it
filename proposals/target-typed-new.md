---
ms.openlocfilehash: 07b4afe4a3fcbf10c978f05e642dfd8a47d53ea5
ms.sourcegitcommit: 194a043db72b9244f8db45db326cc82de6cec965
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80217203"
---

# <a name="target-typed-new-expressions"></a><span data-ttu-id="e56d1-101">Espressioni di `new` tipizzate come destinazione</span><span class="sxs-lookup"><span data-stu-id="e56d1-101">Target-typed `new` expressions</span></span>

* <span data-ttu-id="e56d1-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="e56d1-102">[x] Proposed</span></span>
* <span data-ttu-id="e56d1-103">[prototipo](https://github.com/alrz/roslyn/tree/features/target-typed-new) [x]</span><span class="sxs-lookup"><span data-stu-id="e56d1-103">[x] [Prototype](https://github.com/alrz/roslyn/tree/features/target-typed-new)</span></span>
* <span data-ttu-id="e56d1-104">[] Implementazione</span><span class="sxs-lookup"><span data-stu-id="e56d1-104">[ ] Implementation</span></span>
* <span data-ttu-id="e56d1-105">[] Specifica</span><span class="sxs-lookup"><span data-stu-id="e56d1-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="e56d1-106">Summary</span><span class="sxs-lookup"><span data-stu-id="e56d1-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="e56d1-107">Non richiede la specifica del tipo per i costruttori quando il tipo è noto.</span><span class="sxs-lookup"><span data-stu-id="e56d1-107">Do not require type specification for constructors when the type is known.</span></span> 

## <a name="motivation"></a><span data-ttu-id="e56d1-108">Motivazione</span><span class="sxs-lookup"><span data-stu-id="e56d1-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="e56d1-109">Consente l'inizializzazione del campo senza duplicare il tipo.</span><span class="sxs-lookup"><span data-stu-id="e56d1-109">Allow field initialization without duplicating the type.</span></span>
```cs
Dictionary<string, List<int>> field = new() {
    { "item1", new() { 1, 2, 3 } }
};
```

<span data-ttu-id="e56d1-110">Consente di omettere il tipo quando può essere dedotto dall'utilizzo.</span><span class="sxs-lookup"><span data-stu-id="e56d1-110">Allow omitting the type when it can be inferred from usage.</span></span>
```cs
XmlReader.Create(reader, new() { IgnoreWhitespace = true });
```

<span data-ttu-id="e56d1-111">Creare un'istanza di un oggetto senza sillabare il tipo.</span><span class="sxs-lookup"><span data-stu-id="e56d1-111">Instantiate an object without spelling out the type.</span></span>
```cs
private readonly static object s_syncObj = new();
```

## <a name="detailed-design"></a><span data-ttu-id="e56d1-112">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="e56d1-112">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="e56d1-113">La sintassi *object_creation_expression* verrebbe modificata per rendere facoltativo il *tipo* quando sono presenti parentesi.</span><span class="sxs-lookup"><span data-stu-id="e56d1-113">The *object_creation_expression* syntax would be modified to make the *type* optional when parentheses are present.</span></span> <span data-ttu-id="e56d1-114">Questa operazione è necessaria per risolvere l'ambiguità con *anonymous_object_creation_expression*.</span><span class="sxs-lookup"><span data-stu-id="e56d1-114">This is required to address the ambiguity with *anonymous_object_creation_expression*.</span></span>
```antlr
object_creation_expression
    : 'new' type? '(' argument_list? ')' object_or_collection_initializer?
    | 'new' type object_or_collection_initializer
    ;
```

<span data-ttu-id="e56d1-115">Una `new` tipizzata come destinazione è convertibile in qualsiasi tipo.</span><span class="sxs-lookup"><span data-stu-id="e56d1-115">A target-typed `new` is convertible to any type.</span></span> <span data-ttu-id="e56d1-116">Di conseguenza, non contribuisce alla risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="e56d1-116">As a result, it does not contribute to overload resolution.</span></span> <span data-ttu-id="e56d1-117">Questa operazione viene eseguita principalmente per evitare modifiche di rilievo imprevedibili.</span><span class="sxs-lookup"><span data-stu-id="e56d1-117">This is mainly to avoid unpredictable breaking changes.</span></span>

<span data-ttu-id="e56d1-118">L'elenco di argomenti e le espressioni dell'inizializzatore verranno associati dopo che il tipo è stato determinato.</span><span class="sxs-lookup"><span data-stu-id="e56d1-118">The argument list and the initializer expressions will be bound after the type is determined.</span></span>

<span data-ttu-id="e56d1-119">Il tipo dell'espressione verrebbe dedotto dal tipo di destinazione, che dovrebbe essere uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="e56d1-119">The type of the expression would be inferred from the target-type which would be required to be one of the following:</span></span>

- <span data-ttu-id="e56d1-120">**Qualsiasi tipo di struct** (inclusi i tipi di tupla)</span><span class="sxs-lookup"><span data-stu-id="e56d1-120">**Any struct type** (including tuple types)</span></span>
- <span data-ttu-id="e56d1-121">**Qualsiasi tipo di riferimento** (inclusi i tipi delegati)</span><span class="sxs-lookup"><span data-stu-id="e56d1-121">**Any reference type** (including delegate types)</span></span>
- <span data-ttu-id="e56d1-122">**Qualsiasi parametro di tipo** con un costruttore o un vincolo `struct`</span><span class="sxs-lookup"><span data-stu-id="e56d1-122">**Any type parameter** with a constructor or a `struct` constraint</span></span>

<span data-ttu-id="e56d1-123">con le eccezioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="e56d1-123">with the following exceptions:</span></span>

- <span data-ttu-id="e56d1-124">**Tipi enum:** non tutti i tipi enum contengono la costante zero, quindi è consigliabile usare il membro enum esplicito.</span><span class="sxs-lookup"><span data-stu-id="e56d1-124">**Enum types:** not all enum types contain the constant zero, so it should be desirable to use the explicit enum member.</span></span>
- <span data-ttu-id="e56d1-125">**Tipi di interfaccia:** si tratta di una funzionalità di nicchia che deve essere preferibile per citare in modo esplicito il tipo.</span><span class="sxs-lookup"><span data-stu-id="e56d1-125">**Interface types:** this is a niche feature and it should be preferable to explicitly mention the type.</span></span>
- <span data-ttu-id="e56d1-126">**Tipi di matrice:** le matrici necessitano di una sintassi speciale per fornire la lunghezza.</span><span class="sxs-lookup"><span data-stu-id="e56d1-126">**Array types:** arrays need a special syntax to provide the length.</span></span>
- <span data-ttu-id="e56d1-127">**dinamica:** non è consentito `new dynamic()`, quindi non è consentito `new()` con `dynamic` come tipo di destinazione.</span><span class="sxs-lookup"><span data-stu-id="e56d1-127">**dynamic:** we don't allow `new dynamic()`, so we don't allow `new()` with `dynamic` as a target type.</span></span>

<span data-ttu-id="e56d1-128">Sono esclusi anche tutti gli altri tipi non consentiti nella *object_creation_expression* , ad esempio i tipi di puntatore.</span><span class="sxs-lookup"><span data-stu-id="e56d1-128">All the other types that are not permitted in the *object_creation_expression* are excluded as well, for instance, pointer types.</span></span>

<span data-ttu-id="e56d1-129">Quando il tipo di destinazione è un tipo di valore Nullable, il `new` tipizzato di destinazione verrà convertito nel tipo sottostante invece che nel tipo Nullable.</span><span class="sxs-lookup"><span data-stu-id="e56d1-129">When the target type is a nullable value type, the target-typed `new` will be converted to the underlying type instead of the nullable type.</span></span>

> <span data-ttu-id="e56d1-130">**Problema di apertura:** è necessario consentire i delegati e le tuple come tipo di destinazione?</span><span class="sxs-lookup"><span data-stu-id="e56d1-130">**Open Issue:** should we allow delegates and tuples as the target-type?</span></span>

<span data-ttu-id="e56d1-131">Le regole sopra elencate includono i delegati (un tipo di riferimento) e le Tuple (un tipo struct).</span><span class="sxs-lookup"><span data-stu-id="e56d1-131">The above rules include delegates (a reference type) and tuples (a struct type).</span></span> <span data-ttu-id="e56d1-132">Anche se entrambi i tipi sono costruibile, se il tipo è deducebile, è possibile usare una funzione anonima o un valore letterale di tupla.</span><span class="sxs-lookup"><span data-stu-id="e56d1-132">Although both types are constructible, if the type is inferable, an anonymous function or a tuple literal can already be used.</span></span>
```cs
(int a, int b) t = new(1, 2); // "new" is redundant
Action a = new(() => {}); // "new" is redundant

(int a, int b) t = new(); // ruled out by "use of struct default constructor"
Action a = new(); // no constructor found
```

### <a name="miscellaneous"></a><span data-ttu-id="e56d1-133">Varie</span><span class="sxs-lookup"><span data-stu-id="e56d1-133">Miscellaneous</span></span>

<span data-ttu-id="e56d1-134">`throw new()` non è consentito.</span><span class="sxs-lookup"><span data-stu-id="e56d1-134">`throw new()` is disallowed.</span></span>

<span data-ttu-id="e56d1-135">Il `new` tipizzato per la destinazione non è consentito con gli operatori binari.</span><span class="sxs-lookup"><span data-stu-id="e56d1-135">Target-typed `new` is not allowed with binary operators.</span></span>

<span data-ttu-id="e56d1-136">Non è consentito quando non è disponibile alcun tipo di destinazione: operatori unari, raccolta di un `foreach`, in una `using`, in una decostruzione, in un'espressione `await` come proprietà di tipo anonimo (`new { Prop = new() }`), in un'istruzione `lock`, in un `sizeof`, in un'istruzione `fixed`, in un accesso ai membri (`new().field`), in un'operazione inviata dinamicamente (`someDynamic.Method(new())`), in una query LINQ, come operando dell'operatore `is`, come operando sinistro dell'operatore `??` ,  ...</span><span class="sxs-lookup"><span data-stu-id="e56d1-136">It is disallowed when there is no type to target: unary operators, collection of a `foreach`, in a `using`, in a deconstruction, in an `await` expression, as an anonymous type property (`new { Prop = new() }`), in a `lock` statement, in a `sizeof`, in a `fixed` statement, in a member access (`new().field`), in a dynamically dispatched operation (`someDynamic.Method(new())`), in a LINQ query, as the operand of the `is` operator, as the left operand of the `??` operator,  ...</span></span>

<span data-ttu-id="e56d1-137">Non è inoltre consentito come `ref`.</span><span class="sxs-lookup"><span data-stu-id="e56d1-137">It is also disallowed as a `ref`.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="e56d1-138">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="e56d1-138">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="e56d1-139">Si sono verificati alcuni problemi con tipi di destinazione `new` la creazione di nuove categorie di modifiche di rilievo, ma è già presente con `null` e `default`e questo non è stato un problema significativo.</span><span class="sxs-lookup"><span data-stu-id="e56d1-139">There were some concerns with target-typed `new` creating new categories of breaking changes, but we already have that with `null` and `default`, and that has not been a significant problem.</span></span>

## <a name="alternatives"></a><span data-ttu-id="e56d1-140">Alternativi</span><span class="sxs-lookup"><span data-stu-id="e56d1-140">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="e56d1-141">La maggior parte dei reclami relativi ai tipi troppo lunghi per duplicare l'inizializzazione del campo riguarda gli *argomenti di tipo* , non il tipo stesso, è possibile dedurre solo gli argomenti di tipo come `new Dictionary(...)` (o simile) e dedurre gli argomenti di tipo localmente dagli argomenti o dall'inizializzatore di raccolta.</span><span class="sxs-lookup"><span data-stu-id="e56d1-141">Most of complaints about types being too long to duplicate in field initialization is about *type arguments* not the type itself, we could infer only type arguments like `new Dictionary(...)` (or similar) and infer type arguments locally from arguments or the collection initializer.</span></span>

## <a name="questions"></a><span data-ttu-id="e56d1-142">Domande</span><span class="sxs-lookup"><span data-stu-id="e56d1-142">Questions</span></span>
[questions]: #questions

- <span data-ttu-id="e56d1-143">È consigliabile proibire gli utilizzi negli alberi delle espressioni?</span><span class="sxs-lookup"><span data-stu-id="e56d1-143">Should we forbid usages in expression trees?</span></span> <span data-ttu-id="e56d1-144">non</span><span class="sxs-lookup"><span data-stu-id="e56d1-144">(no)</span></span>
- <span data-ttu-id="e56d1-145">Interazione tra la funzionalità e gli argomenti `dynamic`</span><span class="sxs-lookup"><span data-stu-id="e56d1-145">How the feature interacts with `dynamic` arguments?</span></span> <span data-ttu-id="e56d1-146">(nessun trattamento speciale)</span><span class="sxs-lookup"><span data-stu-id="e56d1-146">(no special treatment)</span></span>
- <span data-ttu-id="e56d1-147">Funzionamento di IntelliSense con `new()`</span><span class="sxs-lookup"><span data-stu-id="e56d1-147">How IntelliSense should work with `new()`?</span></span> <span data-ttu-id="e56d1-148">(solo quando è presente un solo tipo di destinazione)</span><span class="sxs-lookup"><span data-stu-id="e56d1-148">(only when there is a single target-type)</span></span>

## <a name="design-meetings"></a><span data-ttu-id="e56d1-149">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="e56d1-149">Design meetings</span></span>

- [<span data-ttu-id="e56d1-150">LDM-2017-10-18</span><span class="sxs-lookup"><span data-stu-id="e56d1-150">LDM-2017-10-18</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-10-18.md#100)
- [<span data-ttu-id="e56d1-151">LDM-2018-05-21</span><span class="sxs-lookup"><span data-stu-id="e56d1-151">LDM-2018-05-21</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-05-21.md)
- [<span data-ttu-id="e56d1-152">LDM-2018-06-25</span><span class="sxs-lookup"><span data-stu-id="e56d1-152">LDM-2018-06-25</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-06-25.md)
- [<span data-ttu-id="e56d1-153">LDM-2018-08-22</span><span class="sxs-lookup"><span data-stu-id="e56d1-153">LDM-2018-08-22</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-08-22.md#target-typed-new)
- [<span data-ttu-id="e56d1-154">LDM-2018-10-17</span><span class="sxs-lookup"><span data-stu-id="e56d1-154">LDM-2018-10-17</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md)
