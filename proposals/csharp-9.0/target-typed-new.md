---
ms.openlocfilehash: f000dda7eeb1c4f17c26f94c326a12a9d0014288
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297484"
---

# <a name="target-typed-new-expressions"></a><span data-ttu-id="ce203-101">Espressioni tipizzate di destinazione `new`</span><span class="sxs-lookup"><span data-stu-id="ce203-101">Target-typed `new` expressions</span></span>

* <span data-ttu-id="ce203-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="ce203-102">[x] Proposed</span></span>
* <span data-ttu-id="ce203-103">[prototipo](https://github.com/alrz/roslyn/tree/features/target-typed-new) [x]</span><span class="sxs-lookup"><span data-stu-id="ce203-103">[x] [Prototype](https://github.com/alrz/roslyn/tree/features/target-typed-new)</span></span>
* <span data-ttu-id="ce203-104">[] Implementazione</span><span class="sxs-lookup"><span data-stu-id="ce203-104">[ ] Implementation</span></span>
* <span data-ttu-id="ce203-105">[] Specifica</span><span class="sxs-lookup"><span data-stu-id="ce203-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="ce203-106">Summary</span><span class="sxs-lookup"><span data-stu-id="ce203-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="ce203-107">Non richiede la specifica del tipo per i costruttori quando il tipo è noto.</span><span class="sxs-lookup"><span data-stu-id="ce203-107">Do not require type specification for constructors when the type is known.</span></span> 

## <a name="motivation"></a><span data-ttu-id="ce203-108">Motivazione</span><span class="sxs-lookup"><span data-stu-id="ce203-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="ce203-109">Consente l'inizializzazione del campo senza duplicare il tipo.</span><span class="sxs-lookup"><span data-stu-id="ce203-109">Allow field initialization without duplicating the type.</span></span>
```cs
Dictionary<string, List<int>> field = new() {
    { "item1", new() { 1, 2, 3 } }
};
```

<span data-ttu-id="ce203-110">Consente di omettere il tipo quando può essere dedotto dall'utilizzo.</span><span class="sxs-lookup"><span data-stu-id="ce203-110">Allow omitting the type when it can be inferred from usage.</span></span>
```cs
XmlReader.Create(reader, new() { IgnoreWhitespace = true });
```

<span data-ttu-id="ce203-111">Creare un'istanza di un oggetto senza sillabare il tipo.</span><span class="sxs-lookup"><span data-stu-id="ce203-111">Instantiate an object without spelling out the type.</span></span>
```cs
private readonly static object s_syncObj = new();
```

## <a name="specification"></a><span data-ttu-id="ce203-112">Specifiche</span><span class="sxs-lookup"><span data-stu-id="ce203-112">Specification</span></span>
[design]: #detailed-design

<span data-ttu-id="ce203-113">Viene accettata una nuova forma sintattica, *target_typed_new* della *object_creation_expression* , in cui il *tipo* è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="ce203-113">A new syntactic form, *target_typed_new* of the *object_creation_expression* is accepted in which the *type* is optional.</span></span>

```antlr
object_creation_expression
    : 'new' type '(' argument_list? ')' object_or_collection_initializer?
    | 'new' type object_or_collection_initializer
    | target_typed_new
    ;
target_typed_new
    : 'new' '(' argument_list? ')' object_or_collection_initializer?
    ;
```

<span data-ttu-id="ce203-114">Un'espressione *target_typed_new* non dispone di un tipo.</span><span class="sxs-lookup"><span data-stu-id="ce203-114">A *target_typed_new* expression does not have a type.</span></span> <span data-ttu-id="ce203-115">Tuttavia, esiste una nuova *conversione di creazione di oggetti* che è una conversione implicita da Expression, esistente da un *target_typed_new* a ogni tipo.</span><span class="sxs-lookup"><span data-stu-id="ce203-115">However, there is a new *object creation conversion* that is an implicit conversion from expression, that exists from a *target_typed_new* to every type.</span></span>

<span data-ttu-id="ce203-116">Dato un tipo di destinazione `T` , il tipo `T0` è `T` sottostante se `T` è un'istanza di `System.Nullable` .</span><span class="sxs-lookup"><span data-stu-id="ce203-116">Given a target type `T`, the type `T0` is `T`'s underlying type if `T` is an instance of `System.Nullable`.</span></span> <span data-ttu-id="ce203-117">In caso contrario `T0` `T` , è.</span><span class="sxs-lookup"><span data-stu-id="ce203-117">Otherwise `T0` is `T`.</span></span> <span data-ttu-id="ce203-118">Il significato di un'espressione *target_typed_new* convertita nel tipo `T` corrisponde al significato di un *object_creation_expression* corrispondente che specifica `T0` come tipo.</span><span class="sxs-lookup"><span data-stu-id="ce203-118">The meaning of a *target_typed_new* expression that is converted to the type `T` is the same as the meaning of a corresponding *object_creation_expression* that specifies `T0` as the type.</span></span>

<span data-ttu-id="ce203-119">Si tratta di un errore in fase di compilazione se una *target_typed_new* viene utilizzata come operando di un operatore unario o binario o se viene utilizzata quando non è soggetta alla *conversione della creazione*di un oggetto.</span><span class="sxs-lookup"><span data-stu-id="ce203-119">It is a compile-time error if a *target_typed_new* is used as an operand of a unary or binary operator, or if it is used where it is not subject to an *object creation conversion*.</span></span>

> <span data-ttu-id="ce203-120">**Problema di apertura:** è necessario consentire i delegati e le tuple come tipo di destinazione?</span><span class="sxs-lookup"><span data-stu-id="ce203-120">**Open Issue:** should we allow delegates and tuples as the target-type?</span></span>

<span data-ttu-id="ce203-121">Le regole sopra elencate includono i delegati (un tipo di riferimento) e le Tuple (un tipo struct).</span><span class="sxs-lookup"><span data-stu-id="ce203-121">The above rules include delegates (a reference type) and tuples (a struct type).</span></span> <span data-ttu-id="ce203-122">Anche se entrambi i tipi sono costruibile, se il tipo è deducebile, è possibile usare una funzione anonima o un valore letterale di tupla.</span><span class="sxs-lookup"><span data-stu-id="ce203-122">Although both types are constructible, if the type is inferable, an anonymous function or a tuple literal can already be used.</span></span>
```cs
(int a, int b) t = new(1, 2); // "new" is redundant
Action a = new(() => {}); // "new" is redundant

(int a, int b) t = new(); // OK; same as (0, 0)
Action a = new(); // no constructor found
```

### <a name="miscellaneous"></a><span data-ttu-id="ce203-123">Varie</span><span class="sxs-lookup"><span data-stu-id="ce203-123">Miscellaneous</span></span>

<span data-ttu-id="ce203-124">Di seguito sono riportate le conseguenze della specifica:</span><span class="sxs-lookup"><span data-stu-id="ce203-124">The following are consequences of the specification:</span></span>

- <span data-ttu-id="ce203-125">`throw new()`consentito (il tipo di destinazione è `System.Exception` )</span><span class="sxs-lookup"><span data-stu-id="ce203-125">`throw new()` is allowed (the target type is `System.Exception`)</span></span>
- <span data-ttu-id="ce203-126">`new`Non sono consentiti tipi di destinazione con gli operatori binari.</span><span class="sxs-lookup"><span data-stu-id="ce203-126">Target-typed `new` is not allowed with binary operators.</span></span>
- <span data-ttu-id="ce203-127">Non è consentito quando non è presente alcun tipo di destinazione: operatori unari, raccolta di un oggetto, in un oggetto, in una `foreach` `using` decostruzione, in un' `await` espressione, come proprietà di tipo anonimo (), in un'istruzione in un `new { Prop = new() }` oggetto, in un'istruzione, in un `lock` `sizeof` `fixed` accesso ai membri ( `new().field` ), in un'operazione inviata dinamicamente ( `someDynamic.Method(new())` ), in una query LINQ, come operando dell' `is` operatore, come operando sinistro dell' `??` operatore...</span><span class="sxs-lookup"><span data-stu-id="ce203-127">It is disallowed when there is no type to target: unary operators, collection of a `foreach`, in a `using`, in a deconstruction, in an `await` expression, as an anonymous type property (`new { Prop = new() }`), in a `lock` statement, in a `sizeof`, in a `fixed` statement, in a member access (`new().field`), in a dynamically dispatched operation (`someDynamic.Method(new())`), in a LINQ query, as the operand of the `is` operator, as the left operand of the `??` operator,  ...</span></span>
- <span data-ttu-id="ce203-128">Non è inoltre consentito come `ref` .</span><span class="sxs-lookup"><span data-stu-id="ce203-128">It is also disallowed as a `ref`.</span></span>
- <span data-ttu-id="ce203-129">I tipi di tipi seguenti non sono consentiti come destinazioni della conversione</span><span class="sxs-lookup"><span data-stu-id="ce203-129">The following kinds of types are not permitted as targets of the conversion</span></span>
  - <span data-ttu-id="ce203-130">**Tipi enum:** `new()` funzionerà (come `new Enum()` funziona per assegnare il valore predefinito), ma `new(1)` non funzionerà perché i tipi enum non dispongono di un costruttore.</span><span class="sxs-lookup"><span data-stu-id="ce203-130">**Enum types:** `new()` will work (as `new Enum()` works to give the default value), but `new(1)` will not work as enum types do not have a constructor.</span></span>
  - <span data-ttu-id="ce203-131">**Tipi di interfaccia:** Questa operazione funziona allo stesso modo dell'espressione di creazione corrispondente per i tipi COM.</span><span class="sxs-lookup"><span data-stu-id="ce203-131">**Interface types:** This would work the same as the corresponding creation expression for COM types.</span></span>
  - <span data-ttu-id="ce203-132">**Tipi di matrice:** le matrici necessitano di una sintassi speciale per fornire la lunghezza.</span><span class="sxs-lookup"><span data-stu-id="ce203-132">**Array types:** arrays need a special syntax to provide the length.</span></span>    
  - <span data-ttu-id="ce203-133">**dinamica:** non è consentita `new dynamic()` , pertanto non è possibile usare `new()` `dynamic` come tipo di destinazione.</span><span class="sxs-lookup"><span data-stu-id="ce203-133">**dynamic:** we don't allow `new dynamic()`, so we don't allow `new()` with `dynamic` as a target type.</span></span>
  - <span data-ttu-id="ce203-134">**Tuple:** Hanno lo stesso significato della creazione di un oggetto usando il tipo sottostante.</span><span class="sxs-lookup"><span data-stu-id="ce203-134">**tuples:** These have the same meaning as an object creation using the underlying type.</span></span>
  - <span data-ttu-id="ce203-135">Sono esclusi anche tutti gli altri tipi non consentiti nella *object_creation_expression* , ad esempio i tipi di puntatore.</span><span class="sxs-lookup"><span data-stu-id="ce203-135">All the other types that are not permitted in the *object_creation_expression* are excluded as well, for instance, pointer types.</span></span>   

## <a name="drawbacks"></a><span data-ttu-id="ce203-136">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="ce203-136">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="ce203-137">Si sono verificati alcuni problemi relativi alla `new` creazione di nuove categorie di modifiche di rilievo da parte di destinazione, ma è già presente con `null` e `default` e non è stato un problema significativo.</span><span class="sxs-lookup"><span data-stu-id="ce203-137">There were some concerns with target-typed `new` creating new categories of breaking changes, but we already have that with `null` and `default`, and that has not been a significant problem.</span></span>

## <a name="alternatives"></a><span data-ttu-id="ce203-138">Alternativi</span><span class="sxs-lookup"><span data-stu-id="ce203-138">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="ce203-139">La maggior parte dei reclami relativi ai tipi troppo lunghi per essere duplicati nell'inizializzazione del campo riguarda gli *argomenti di tipo* , non il tipo stesso, è possibile dedurre solo argomenti di tipo come `new Dictionary(...)` (o simili) e dedurre gli argomenti di tipo localmente dagli argomenti o dall'inizializzatore di raccolta.</span><span class="sxs-lookup"><span data-stu-id="ce203-139">Most of complaints about types being too long to duplicate in field initialization is about *type arguments* not the type itself, we could infer only type arguments like `new Dictionary(...)` (or similar) and infer type arguments locally from arguments or the collection initializer.</span></span>

## <a name="questions"></a><span data-ttu-id="ce203-140">Domande</span><span class="sxs-lookup"><span data-stu-id="ce203-140">Questions</span></span>
[questions]: #questions

- <span data-ttu-id="ce203-141">È consigliabile proibire gli utilizzi negli alberi delle espressioni?</span><span class="sxs-lookup"><span data-stu-id="ce203-141">Should we forbid usages in expression trees?</span></span> <span data-ttu-id="ce203-142">non</span><span class="sxs-lookup"><span data-stu-id="ce203-142">(no)</span></span>
- <span data-ttu-id="ce203-143">Interazione tra la funzionalità e gli `dynamic` argomenti</span><span class="sxs-lookup"><span data-stu-id="ce203-143">How the feature interacts with `dynamic` arguments?</span></span> <span data-ttu-id="ce203-144">(nessun trattamento speciale)</span><span class="sxs-lookup"><span data-stu-id="ce203-144">(no special treatment)</span></span>
- <span data-ttu-id="ce203-145">Funzionamento `new()` di IntelliSense</span><span class="sxs-lookup"><span data-stu-id="ce203-145">How IntelliSense should work with `new()`?</span></span> <span data-ttu-id="ce203-146">(solo quando è presente un solo tipo di destinazione)</span><span class="sxs-lookup"><span data-stu-id="ce203-146">(only when there is a single target-type)</span></span>

## <a name="design-meetings"></a><span data-ttu-id="ce203-147">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="ce203-147">Design meetings</span></span>

- [<span data-ttu-id="ce203-148">LDM-2017-10-18</span><span class="sxs-lookup"><span data-stu-id="ce203-148">LDM-2017-10-18</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-10-18.md#100)
- [<span data-ttu-id="ce203-149">LDM-2018-05-21</span><span class="sxs-lookup"><span data-stu-id="ce203-149">LDM-2018-05-21</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-05-21.md)
- [<span data-ttu-id="ce203-150">LDM-2018-06-25</span><span class="sxs-lookup"><span data-stu-id="ce203-150">LDM-2018-06-25</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-06-25.md)
- [<span data-ttu-id="ce203-151">LDM-2018-08-22</span><span class="sxs-lookup"><span data-stu-id="ce203-151">LDM-2018-08-22</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-08-22.md#target-typed-new)
- [<span data-ttu-id="ce203-152">LDM-2018-10-17</span><span class="sxs-lookup"><span data-stu-id="ce203-152">LDM-2018-10-17</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md)
- [<span data-ttu-id="ce203-153">LDM-2020-03-25</span><span class="sxs-lookup"><span data-stu-id="ce203-153">LDM-2020-03-25</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md)