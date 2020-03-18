---
ms.openlocfilehash: 2e4a3a32def6900797c151264c984378b09b4988
ms.sourcegitcommit: 5983461e05be62f39c77383cb7857539518cb04f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "79485126"
---

# <a name="discriminated-unions--enum-class"></a><span data-ttu-id="20d91-101">Unioni discriminate/`enum class`</span><span class="sxs-lookup"><span data-stu-id="20d91-101">Discriminated unions / `enum class`</span></span>

<span data-ttu-id="20d91-102">`enum class`es sono un nuovo tipo di dichiarazione di tipo, talvolta definita unioni discriminate, in cui ogni istanza può essere elencata e ogni istanza è non sovrapposta.</span><span class="sxs-lookup"><span data-stu-id="20d91-102">`enum class`es are a new kind of type declaration, sometimes referred to as discriminated unions, where each every possible instance the type is listed, and each instance is non-overlapping.</span></span>

<span data-ttu-id="20d91-103">Un `enum class` viene definito utilizzando la sintassi seguente:</span><span class="sxs-lookup"><span data-stu-id="20d91-103">An `enum class` is defined using the following syntax:</span></span>

```antlr
enum_class
    : 'partial'? 'enum class' identifier type_parameter_list? type_parameter_constraints_clause* 
      '{' enum_class_body '}'
    ;

enum_class_body
    : enum_class_cases?
    | enum_class_cases ','
    ;

enum_class_cases
    : enum_class_case
    | enum_class_case ',' enum_class_cases
    ;

enum_class_case
    : enum_class
    | class_declaration
    | identifier type_parameter_list? '(' formal_parameter_list? ')'
    | identifier
    ;

```

<span data-ttu-id="20d91-104">Sintassi di esempio:</span><span class="sxs-lookup"><span data-stu-id="20d91-104">Sample syntax:</span></span>

```C#
enum class Shape
{
    Rectangle(float Width, float Length),
    Circle(float Radius),
}
```

## <a name="semantics"></a><span data-ttu-id="20d91-105">Semantics</span><span class="sxs-lookup"><span data-stu-id="20d91-105">Semantics</span></span>

<span data-ttu-id="20d91-106">Una definizione `enum class` definisce un tipo radice, ovvero una classe astratta con lo stesso nome della dichiarazione di `enum class` e un set di membri, ognuno dei quali ha un tipo che è un sottotipo del tipo radice.</span><span class="sxs-lookup"><span data-stu-id="20d91-106">An `enum class` definition defines a root type, which is an abstract class of the same name as the `enum class` declaration, and a set of members, each of which has a type which is a subtype of the root type.</span></span> <span data-ttu-id="20d91-107">Se sono presenti più definizioni di `partial enum class`, tutti i membri verranno considerati membri della definizione della classe Enum.</span><span class="sxs-lookup"><span data-stu-id="20d91-107">If there are multiple `partial enum class` definitions, all members will be considered members of the enum class definition.</span></span> <span data-ttu-id="20d91-108">A differenza di una definizione di classe astratta definita dall'utente, il tipo di radice `enum class` è parziale per impostazione predefinita e definito in modo da disporre di un costruttore *privato* senza parametri predefinito.</span><span class="sxs-lookup"><span data-stu-id="20d91-108">Unlike a user-defined abstract class definition, the `enum class` root type is partial by default and defined to have a default *private* parameter-less constructor.</span></span>

<span data-ttu-id="20d91-109">Si noti che, poiché il tipo radice è definito come una classe astratta parziale, è possibile aggiungere anche definizioni parziali del *tipo radice* , in cui sono consentiti moduli di sintassi standard per un corpo della classe.</span><span class="sxs-lookup"><span data-stu-id="20d91-109">Note that, since the root type is defined to be a partial abstract class, partial definitions of the *root type* may also be added, where standard syntax forms for a class body are allowed.</span></span>
<span data-ttu-id="20d91-110">Tuttavia, nessun tipo può ereditare direttamente dal tipo radice in una dichiarazione, eccetto quelli specificati come `enum class` membri.</span><span class="sxs-lookup"><span data-stu-id="20d91-110">However, no types may directly inherit from the root type in any declaration, aside from those specified as `enum class` members.</span></span> <span data-ttu-id="20d91-111">Non è inoltre consentito alcun costruttore definito dall'utente per il tipo radice.</span><span class="sxs-lookup"><span data-stu-id="20d91-111">In addition, no user-defined constructors are permitted for the root type.</span></span>

<span data-ttu-id="20d91-112">Sono disponibili quattro tipi di dichiarazioni di membri `enum class`:</span><span class="sxs-lookup"><span data-stu-id="20d91-112">There are four kinds of `enum class` member declarations:</span></span>

* <span data-ttu-id="20d91-113">Membri di classi semplici</span><span class="sxs-lookup"><span data-stu-id="20d91-113">simple class members</span></span>

* <span data-ttu-id="20d91-114">membri di classe complessi</span><span class="sxs-lookup"><span data-stu-id="20d91-114">complex class members</span></span>

* <span data-ttu-id="20d91-115">membri `enum class`</span><span class="sxs-lookup"><span data-stu-id="20d91-115">`enum class` members</span></span>

* <span data-ttu-id="20d91-116">membri del valore.</span><span class="sxs-lookup"><span data-stu-id="20d91-116">value members.</span></span>

### <a name="simple-class-members"></a><span data-ttu-id="20d91-117">Membri di classi semplici</span><span class="sxs-lookup"><span data-stu-id="20d91-117">Simple class members</span></span>

<span data-ttu-id="20d91-118">Una semplice dichiarazione del membro di classe definisce una nuova classe "record" annidata (intenzionalmente lasciata non definita in questo documento) con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="20d91-118">A simple class member declaration defines a new nested "record" class (intentionally left undefined in this document) with the same name.</span></span> <span data-ttu-id="20d91-119">La classe annidata eredita dal tipo radice.</span><span class="sxs-lookup"><span data-stu-id="20d91-119">The nested class inherits from the root type.</span></span>

<span data-ttu-id="20d91-120">Dato il codice di esempio precedente,</span><span class="sxs-lookup"><span data-stu-id="20d91-120">Given the sample code above,</span></span>

```C#
enum class Shape
{
    Rectangle(float Width, float Length),
    Circle(float Radius)
}
```

<span data-ttu-id="20d91-121">la dichiarazione di `enum class` presenta una semantica equivalente alla seguente dichiarazione</span><span class="sxs-lookup"><span data-stu-id="20d91-121">the `enum class` declaration has semantics equivalent to the following declaration</span></span>

```C#
partial abstract class Shape
{
    public data class Rectangle(float Width, float Length) : Shape,
    public data class Circle(float Radius) : Shape
}
```

### <a name="complex-class-members"></a><span data-ttu-id="20d91-122">Membri di classe complessi</span><span class="sxs-lookup"><span data-stu-id="20d91-122">Complex class members</span></span>

<span data-ttu-id="20d91-123">È anche possibile annidare un'intera dichiarazione di classe al di sotto di una dichiarazione di `enum class`.</span><span class="sxs-lookup"><span data-stu-id="20d91-123">You can also nest an entire class declaration below an `enum class` declaration.</span></span> <span data-ttu-id="20d91-124">Verrà considerato come una classe annidata del tipo radice.</span><span class="sxs-lookup"><span data-stu-id="20d91-124">It will be treated as a nested class of the root type.</span></span> <span data-ttu-id="20d91-125">La sintassi consente qualsiasi dichiarazione di classe, ma è necessaria affinché il membro della classe complessa erediti dalla dichiarazione di `enum class` contenitore diretto.</span><span class="sxs-lookup"><span data-stu-id="20d91-125">The syntax allows any class declaration, but it is required for the complex class member to inherit from the direct containing `enum class` declaration.</span></span> 

### <a name="enum-class-members"></a><span data-ttu-id="20d91-126">membri `enum class`</span><span class="sxs-lookup"><span data-stu-id="20d91-126">`enum class` members</span></span>

<span data-ttu-id="20d91-127">`enum classes` possono essere annidati l'uno con l'altro, ad esempio</span><span class="sxs-lookup"><span data-stu-id="20d91-127">`enum classes` can be nested under each other, e.g.</span></span>

```C#
enum class Expr
{
    enum class Binary
    {
        Addition(Expr left, Expr right),
        Multiplication(Expr left, Expr right)
    }
}
```

<span data-ttu-id="20d91-128">Questo è quasi identico alla semantica di un `enum class`di primo livello, ad eccezione del fatto che la classe di enumerazione annidata definisce un tipo radice annidato e tutto il contenuto sotto la classe enum nidificata è un sottotipo del tipo radice annidato, anziché il tipo radice di primo livello.</span><span class="sxs-lookup"><span data-stu-id="20d91-128">This is almost identical to the semantics of a top-level `enum class`, except that the nested enum class defines a nested root type, and everything below the nested enum class is a subtype of the nested root type, instead of the top-level root type.</span></span>

```C#
partial abstract class Expr
{
    partial abstract class Binary : Expr
    {
        public data class Addition(Expr left, Expr right) : Binary,
        public data class Multiplication(Expr left, Expr right) : Binary
    }
}
```

### <a name="value-members"></a><span data-ttu-id="20d91-129">Membri valore</span><span class="sxs-lookup"><span data-stu-id="20d91-129">Value members</span></span>

<span data-ttu-id="20d91-130">`enum classes` possono inoltre contenere membri di valore.</span><span class="sxs-lookup"><span data-stu-id="20d91-130">`enum classes` can also contain value members.</span></span> <span data-ttu-id="20d91-131">I membri del valore definiscono le proprietà statiche pubbliche solo Get sul tipo radice che restituiscono anche il tipo radice, ad esempio</span><span class="sxs-lookup"><span data-stu-id="20d91-131">Value members define public get-only static properties on the root type that also return the root type, e.g.</span></span>

```C#
enum class Color
{
    Red,
    Green
}
```

<span data-ttu-id="20d91-132">ha proprietà equivalenti a</span><span class="sxs-lookup"><span data-stu-id="20d91-132">has properties equivalent to</span></span>

```C#
partial abstract class Color
{
    public static Color Red => ...;
    public static Color Green => ...;
}
```

<span data-ttu-id="20d91-133">La semantica completa è considerata un dettaglio di implementazione, ma è garantita la restituzione di un'istanza univoca per ogni proprietà e la stessa istanza verrà restituita nelle chiamate ripetute.</span><span class="sxs-lookup"><span data-stu-id="20d91-133">The complete semantics are considered an implementation detail, but it is guaranteed that one unique instance will be returned for each property, and the same instance will be returned on repeated invocations.</span></span>


### <a name="switch-expression-and-patterns"></a><span data-ttu-id="20d91-134">Cambia espressione e modelli</span><span class="sxs-lookup"><span data-stu-id="20d91-134">Switch expression and patterns</span></span>

<span data-ttu-id="20d91-135">Ci sono alcune modifiche proposte per la corrispondenza dei modelli e l'espressione switch per gestire `enum classes`.</span><span class="sxs-lookup"><span data-stu-id="20d91-135">There are some proposed adjustments to pattern matching and the switch expression to handle `enum classes`.</span></span> <span data-ttu-id="20d91-136">Le espressioni switch possono già corrispondere ai tipi tramite il modello di variabile, ma attualmente per i tipi di riferimento, nessun set di bracci di cambio nell'espressione switch viene considerato completo, tranne che per la corrispondenza con il tipo statico dell'argomento o un sottotipo.</span><span class="sxs-lookup"><span data-stu-id="20d91-136">Switch expressions can already match types through the variable pattern, but for currently for reference types, no set of switch arms in the switch expression are considered complete, except for matching against the static type of the argument, or a subtype.</span></span>

<span data-ttu-id="20d91-137">Le espressioni switch verrebbero modificate in modo tale che, se il tipo radice di un `enum class` è il tipo statico dell'argomento all'espressione switch ed è presente un set di criteri che corrispondono a tutti i membri dell'enumerazione, l'opzione sarà considerata esaustiva.</span><span class="sxs-lookup"><span data-stu-id="20d91-137">Switch expressions would be changed such that, if the root type of an `enum class` is the static type of the argument to the switch expression, and there is a set of patterns matching all members of the enum, then the switch will be considered exhaustive.</span></span>

<span data-ttu-id="20d91-138">Poiché i membri del valore non sono costanti e non definiscono nuovi tipi statici, attualmente non è possibile trovare una corrispondenza per il criterio.</span><span class="sxs-lookup"><span data-stu-id="20d91-138">Since value members are not constants and do not define new static types, they currently cannot be matched by pattern.</span></span> <span data-ttu-id="20d91-139">Per ottenere questo risultato, viene aggiunto un nuovo modello che usa la sintassi del criterio costante per consentire la corrispondenza con i membri del valore `enum class`.</span><span class="sxs-lookup"><span data-stu-id="20d91-139">To make this possible, a new pattern using the constant pattern syntax will be added to allow match against `enum class` value members.</span></span> <span data-ttu-id="20d91-140">La corrispondenza viene definita in modo da avere esito positivo solo se l'argomento del criterio di ricerca e il valore restituito dal membro del valore `enum class` sono uguali al riferimento, anche se l'implementazione non è necessaria per eseguire questo controllo.</span><span class="sxs-lookup"><span data-stu-id="20d91-140">The match is defined to succeed if and only if the argument to the pattern match and the value returned by the `enum class` value member would be reference equal, although the implementation is not required to perform this check.</span></span>


## <a name="open-questions"></a><span data-ttu-id="20d91-141">Domande aperte</span><span class="sxs-lookup"><span data-stu-id="20d91-141">Open questions</span></span>

- <span data-ttu-id="20d91-142">[] Quali sono le informazioni sull'algoritmo di tipo comune sui membri `enum class`?</span><span class="sxs-lookup"><span data-stu-id="20d91-142">[ ] What does the common type algorithm say about `enum class` members?</span></span> <span data-ttu-id="20d91-143">Questo codice è valido?</span><span class="sxs-lookup"><span data-stu-id="20d91-143">Is this valid code?</span></span>
    * `var x = b ? new Shape.Rectangle(...) : new Shape.Circle(...)`

- <span data-ttu-id="20d91-144">[] L'aggiunta di un nuovo modello solo per i membri del valore sembra una mano intensa.</span><span class="sxs-lookup"><span data-stu-id="20d91-144">[ ] Adding a new pattern just for value members seems heavy handed.</span></span> <span data-ttu-id="20d91-145">Esiste una costruzione di versione più generale sensata?</span><span class="sxs-lookup"><span data-stu-id="20d91-145">Is there a more general version construction that makes sense?</span></span>
    - <span data-ttu-id="20d91-146">[] Anche i membri del valore non eseguono il mapping a una costruzione della classe annidata parallela a causa di questo</span><span class="sxs-lookup"><span data-stu-id="20d91-146">[ ] Value members also do not map well to a parallel nested class construction because of this</span></span>

- <span data-ttu-id="20d91-147">[] Sta passando a un argomento con un tipo di `enum class` statico che garantisce un tempo costante?</span><span class="sxs-lookup"><span data-stu-id="20d91-147">[ ] Is switching against an argument with an `enum class` static type guaranteed to be constant-time?</span></span>

- <span data-ttu-id="20d91-148">[] Dovrebbe essere possibile fare in modo che `enum class`es non venga considerato completo nell'espressione switch?</span><span class="sxs-lookup"><span data-stu-id="20d91-148">[ ] Should there be a way to make `enum class`es not be considered complete in the switch expression?</span></span> <span data-ttu-id="20d91-149">Prefisso con `virtual`?</span><span class="sxs-lookup"><span data-stu-id="20d91-149">Prefix with `virtual`?</span></span>

- <span data-ttu-id="20d91-150">[] Quali modificatori devono essere consentiti su `enum class`?</span><span class="sxs-lookup"><span data-stu-id="20d91-150">[ ] What modifiers should be permitted on `enum class`?</span></span>