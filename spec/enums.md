# <a name="enums"></a><span data-ttu-id="bf049-101">Enumerazioni</span><span class="sxs-lookup"><span data-stu-id="bf049-101">Enums</span></span>

<span data-ttu-id="bf049-102">Un' ***tipo di enumerazione*** è un tipo valore distinto ([tipi di valore](types.md#value-types)) che dichiara un set di costanti denominate.</span><span class="sxs-lookup"><span data-stu-id="bf049-102">An ***enum type*** is a distinct value type ([Value types](types.md#value-types)) that declares a set of named constants.</span></span>

<span data-ttu-id="bf049-103">L'esempio</span><span class="sxs-lookup"><span data-stu-id="bf049-103">The example</span></span>

```csharp
enum Color
{
    Red,
    Green,
    Blue
}
```

<span data-ttu-id="bf049-104">dichiara un tipo di enumerazione denominato `Color` con i membri `Red`, `Green`, e `Blue`.</span><span class="sxs-lookup"><span data-stu-id="bf049-104">declares an enum type named `Color` with members `Red`, `Green`, and `Blue`.</span></span>

## <a name="enum-declarations"></a><span data-ttu-id="bf049-105">Dichiarazioni di enumerazione</span><span class="sxs-lookup"><span data-stu-id="bf049-105">Enum declarations</span></span>

<span data-ttu-id="bf049-106">Una dichiarazione di enumerazione dichiara un nuovo tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-106">An enum declaration declares a new enum type.</span></span> <span data-ttu-id="bf049-107">Una dichiarazione enum inizia con la parola chiave `enum`e definisce il nome, accessibilità, tipo sottostante e i membri dell'enumerazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-107">An enum declaration begins with the keyword `enum`, and defines the name, accessibility, underlying type, and members of the enum.</span></span>

```antlr
enum_declaration
    : attributes? enum_modifier* 'enum' identifier enum_base? enum_body ';'?
    ;

enum_base
    : ':' integral_type
    ;

enum_body
    : '{' enum_member_declarations? '}'
    | '{' enum_member_declarations ',' '}'
    ;
```

<span data-ttu-id="bf049-108">Ogni tipo di enumerazione ha un tipo integrale corrispondente denominato il ***tipo sottostante*** del tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-108">Each enum type has a corresponding integral type called the ***underlying type*** of the enum type.</span></span> <span data-ttu-id="bf049-109">Questo tipo sottostante deve essere in grado di rappresentare tutti i valori dell'enumeratore definiti nell'enumerazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-109">This underlying type must be able to represent all the enumerator values defined in the enumeration.</span></span> <span data-ttu-id="bf049-110">Una dichiarazione di enumerazione possa dichiarare in modo esplicito un tipo sottostante `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` o `ulong`.</span><span class="sxs-lookup"><span data-stu-id="bf049-110">An enum declaration may explicitly declare an underlying type of `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` or `ulong`.</span></span> <span data-ttu-id="bf049-111">Si noti che `char` non può essere utilizzato come un tipo sottostante.</span><span class="sxs-lookup"><span data-stu-id="bf049-111">Note that `char` cannot be used as an underlying type.</span></span> <span data-ttu-id="bf049-112">Una dichiarazione di enumerazione che non dichiara in modo esplicito un tipo sottostante è un tipo sottostante di `int`.</span><span class="sxs-lookup"><span data-stu-id="bf049-112">An enum declaration that does not explicitly declare an underlying type has an underlying type of `int`.</span></span>

<span data-ttu-id="bf049-113">L'esempio</span><span class="sxs-lookup"><span data-stu-id="bf049-113">The example</span></span>

```csharp
enum Color: long
{
    Red,
    Green,
    Blue
}
```

<span data-ttu-id="bf049-114">dichiara un'enumerazione con un tipo sottostante di `long`.</span><span class="sxs-lookup"><span data-stu-id="bf049-114">declares an enum with an underlying type of `long`.</span></span> <span data-ttu-id="bf049-115">Uno sviluppatore può scegliere di usare un tipo sottostante di `long`, come illustrato nell'esempio, per consentire l'uso di valori che sono nell'intervallo `long` ma non compresa nell'intervallo di `int`, o per mantenere questa opzione per il futuro.</span><span class="sxs-lookup"><span data-stu-id="bf049-115">A developer might choose to use an underlying type of `long`, as in the example, to enable the use of values that are in the range of `long` but not in the range of `int`, or to preserve this option for the future.</span></span>

## <a name="enum-modifiers"></a><span data-ttu-id="bf049-116">Modificatori enum</span><span class="sxs-lookup"><span data-stu-id="bf049-116">Enum modifiers</span></span>

<span data-ttu-id="bf049-117">Un' *enum_declaration* può includere una sequenza dei modificatori enum:</span><span class="sxs-lookup"><span data-stu-id="bf049-117">An *enum_declaration* may optionally include a sequence of enum modifiers:</span></span>

```antlr
enum_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    ;
```

<span data-ttu-id="bf049-118">È un errore in fase di compilazione per lo stesso modificatore venga visualizzato più volte in una dichiarazione di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-118">It is a compile-time error for the same modifier to appear multiple times in an enum declaration.</span></span>

<span data-ttu-id="bf049-119">I modificatori di una dichiarazione di enumerazione hanno lo stesso significato di quelli di una dichiarazione di classe ([classe modificatori](classes.md#class-modifiers)).</span><span class="sxs-lookup"><span data-stu-id="bf049-119">The modifiers of an enum declaration have the same meaning as those of a class declaration ([Class modifiers](classes.md#class-modifiers)).</span></span> <span data-ttu-id="bf049-120">Si noti tuttavia che il `abstract` e `sealed` modificatori non consentiti in una dichiarazione di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-120">Note, however, that the `abstract` and `sealed` modifiers are not permitted in an enum declaration.</span></span> <span data-ttu-id="bf049-121">Le enumerazioni non possono essere astratte e non consentono la derivazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-121">Enums cannot be abstract and do not permit derivation.</span></span>

## <a name="enum-members"></a><span data-ttu-id="bf049-122">Membri enum</span><span class="sxs-lookup"><span data-stu-id="bf049-122">Enum members</span></span>

<span data-ttu-id="bf049-123">Il corpo di una dichiarazione di tipo enum definisce zero o più membri di enumerazione, che sono costanti denominate del tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-123">The body of an enum type declaration defines zero or more enum members, which are the named constants of the enum type.</span></span> <span data-ttu-id="bf049-124">Nessuna due membri di enumerazione possono avere lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="bf049-124">No two enum members can have the same name.</span></span>

```antlr
enum_member_declarations
    : enum_member_declaration (',' enum_member_declaration)*
    ;

enum_member_declaration
    : attributes? identifier ('=' constant_expression)?
    ;
```

<span data-ttu-id="bf049-125">Ogni membro di enumerazione è associato un valore costante.</span><span class="sxs-lookup"><span data-stu-id="bf049-125">Each enum member has an associated constant value.</span></span> <span data-ttu-id="bf049-126">Il tipo di questo valore è il tipo sottostante per l'enumerazione che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="bf049-126">The type of this value is the underlying type for the containing enum.</span></span> <span data-ttu-id="bf049-127">Il valore costante per ogni membro di enumerazione deve essere compreso nell'intervallo del tipo sottostante per l'enumerazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-127">The constant value for each enum member must be in the range of the underlying type for the enum.</span></span> <span data-ttu-id="bf049-128">L'esempio</span><span class="sxs-lookup"><span data-stu-id="bf049-128">The example</span></span>

```csharp
enum Color: uint
{
    Red = -1,
    Green = -2,
    Blue = -3
}
```

<span data-ttu-id="bf049-129">Genera un errore in fase di compilazione in quanto i valori delle costanti `-1`, `-2`, e `-3` non sono compresi nell'intervallo del tipo integrale sottostante `uint`.</span><span class="sxs-lookup"><span data-stu-id="bf049-129">results in a compile-time error because the constant values `-1`, `-2`, and `-3` are not in the range of the underlying integral type `uint`.</span></span>

<span data-ttu-id="bf049-130">Più membri di enumerazione possono condividere lo stesso valore associato.</span><span class="sxs-lookup"><span data-stu-id="bf049-130">Multiple enum members may share the same associated value.</span></span> <span data-ttu-id="bf049-131">L'esempio</span><span class="sxs-lookup"><span data-stu-id="bf049-131">The example</span></span>

```csharp
enum Color 
{
    Red,
    Green,
    Blue,

    Max = Blue
}
```

<span data-ttu-id="bf049-132">viene illustrata un'enumerazione nel quale due membri enum - `Blue` e `Max` : associato lo stesso valore.</span><span class="sxs-lookup"><span data-stu-id="bf049-132">shows an enum in which two enum members -- `Blue` and `Max` -- have the same associated value.</span></span>

<span data-ttu-id="bf049-133">Il valore associato di un membro di enumerazione è assegnato implicitamente o esplicitamente.</span><span class="sxs-lookup"><span data-stu-id="bf049-133">The associated value of an enum member is assigned either implicitly or explicitly.</span></span> <span data-ttu-id="bf049-134">Se la dichiarazione del membro enum ha un *constant_expression* inizializzatore, il valore dell'espressione costante, convertito in modo implicito al tipo sottostante dell'enumerazione, è il valore del membro enum associato.</span><span class="sxs-lookup"><span data-stu-id="bf049-134">If the declaration of the enum member has a *constant_expression* initializer, the value of that constant expression, implicitly converted to the underlying type of the enum, is the associated value of the enum member.</span></span> <span data-ttu-id="bf049-135">Se la dichiarazione del membro enum non include alcun inizializzatore, il valore associato viene impostato in modo implicito, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="bf049-135">If the declaration of the enum member has no initializer, its associated value is set implicitly, as follows:</span></span>

*  <span data-ttu-id="bf049-136">Se il membro di enumerazione è il primo membro di enumerazione dichiarato nel tipo di enumerazione, il valore associato è zero.</span><span class="sxs-lookup"><span data-stu-id="bf049-136">If the enum member is the first enum member declared in the enum type, its associated value is zero.</span></span>
*  <span data-ttu-id="bf049-137">In caso contrario, il valore del membro enum associato viene ottenuto incrementando il valore associato del membro enum testualmente precedente di uno.</span><span class="sxs-lookup"><span data-stu-id="bf049-137">Otherwise, the associated value of the enum member is obtained by increasing the associated value of the textually preceding enum member by one.</span></span> <span data-ttu-id="bf049-138">Questo aumento del valore deve essere compreso nell'intervallo di valori che può essere rappresentato dal tipo sottostante, in caso contrario, verrà generato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-138">This increased value must be within the range of values that can be represented by the underlying type, otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="bf049-139">L'esempio</span><span class="sxs-lookup"><span data-stu-id="bf049-139">The example</span></span>

```csharp
using System;

enum Color
{
    Red,
    Green = 10,
    Blue
}

class Test
{
    static void Main() {
        Console.WriteLine(StringFromColor(Color.Red));
        Console.WriteLine(StringFromColor(Color.Green));
        Console.WriteLine(StringFromColor(Color.Blue));
    }

    static string StringFromColor(Color c) {
        switch (c) {
            case Color.Red: 
                return String.Format("Red = {0}", (int) c);

            case Color.Green:
                return String.Format("Green = {0}", (int) c);

            case Color.Blue:
                return String.Format("Blue = {0}", (int) c);

            default:
                return "Invalid color";
        }
    }
}
```

<span data-ttu-id="bf049-140">Stampa i nomi dei membri di enumerazione e i relativi valori associati.</span><span class="sxs-lookup"><span data-stu-id="bf049-140">prints out the enum member names and their associated values.</span></span> <span data-ttu-id="bf049-141">L'output è:</span><span class="sxs-lookup"><span data-stu-id="bf049-141">The output is:</span></span>

```
Red = 0
Green = 10
Blue = 11
```

<span data-ttu-id="bf049-142">per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="bf049-142">for the following reasons:</span></span>

*  <span data-ttu-id="bf049-143">il membro di enumerazione `Red` viene automaticamente assegnato il valore zero (perché non include alcun inizializzatore ed è il primo membro di enumerazione).</span><span class="sxs-lookup"><span data-stu-id="bf049-143">the enum member `Red` is automatically assigned the value zero (since it has no initializer and is the first enum member);</span></span>
*  <span data-ttu-id="bf049-144">il membro di enumerazione `Green` viene assegnato in modo esplicito il valore `10`;</span><span class="sxs-lookup"><span data-stu-id="bf049-144">the enum member `Green` is explicitly given the value `10`;</span></span>
*  <span data-ttu-id="bf049-145">e il membro di enumerazione `Blue` viene automaticamente assegnato il valore più grande rispetto al membro testualmente precedente.</span><span class="sxs-lookup"><span data-stu-id="bf049-145">and the enum member `Blue` is automatically assigned the value one greater than the member that textually precedes it.</span></span>

<span data-ttu-id="bf049-146">Il valore associato di un membro enum non potrà, in modo diretto o indiretto, usare il valore di un proprio membro di enumerazione associata.</span><span class="sxs-lookup"><span data-stu-id="bf049-146">The associated value of an enum member may not, directly or indirectly, use the value of its own associated enum member.</span></span> <span data-ttu-id="bf049-147">Questa restrizione circolarità, inizializzatori di membro di enumerazione possono fare riferimento ad altri inizializzatori di membro enum, indipendentemente dalla loro posizione testuale.</span><span class="sxs-lookup"><span data-stu-id="bf049-147">Other than this circularity restriction, enum member initializers may freely refer to other enum member initializers, regardless of their textual position.</span></span> <span data-ttu-id="bf049-148">All'interno di un inizializzatore di membro di enumerazione, i valori degli altri membri di enumerazione vengono sempre trattati come se avessero il tipo del relativo tipo sottostante, in modo che i cast non sono necessari quando si fa riferimento ad altri membri di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-148">Within an enum member initializer, values of other enum members are always treated as having the type of their underlying type, so that casts are not necessary when referring to other enum members.</span></span>

<span data-ttu-id="bf049-149">L'esempio</span><span class="sxs-lookup"><span data-stu-id="bf049-149">The example</span></span>

```csharp
enum Circular
{
    A = B,
    B
}
```

<span data-ttu-id="bf049-150">Genera un errore in fase di compilazione in quanto le dichiarazioni delle `A` e `B` sono circolari.</span><span class="sxs-lookup"><span data-stu-id="bf049-150">results in a compile-time error because the declarations of `A` and `B` are circular.</span></span> <span data-ttu-id="bf049-151">`A` dipende `B` in modo esplicito, e `B` dipende `A` in modo implicito.</span><span class="sxs-lookup"><span data-stu-id="bf049-151">`A` depends on `B` explicitly, and `B` depends on `A` implicitly.</span></span>

<span data-ttu-id="bf049-152">Membri enum sono denominati e con ambiti in modo analogo ai campi all'interno delle classi.</span><span class="sxs-lookup"><span data-stu-id="bf049-152">Enum members are named and scoped in a manner exactly analogous to fields within classes.</span></span> <span data-ttu-id="bf049-153">L'ambito di un membro di enumerazione è il corpo del tipo di enumerazione che li contiene.</span><span class="sxs-lookup"><span data-stu-id="bf049-153">The scope of an enum member is the body of its containing enum type.</span></span> <span data-ttu-id="bf049-154">In tale ambito, i membri di enumerazione possono farvi riferimento.{0}{0}i il relativo nome semplice.</span><span class="sxs-lookup"><span data-stu-id="bf049-154">Within that scope, enum members can be referred to by their simple name.</span></span> <span data-ttu-id="bf049-155">Da tutto l'altro codice, il nome di un membro enum deve essere qualificato con il nome del relativo tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-155">From all other code, the name of an enum member must be qualified with the name of its enum type.</span></span> <span data-ttu-id="bf049-156">Membri enum sono privi di qualsiasi accessibilità dichiarata: un membro di enumerazione è accessibile se il tipo di enumerazione che lo contiene è accessibile.</span><span class="sxs-lookup"><span data-stu-id="bf049-156">Enum members do not have any declared accessibility -- an enum member is accessible if its containing enum type is accessible.</span></span>

## <a name="the-systemenum-type"></a><span data-ttu-id="bf049-157">Il tipo System. Enum</span><span class="sxs-lookup"><span data-stu-id="bf049-157">The System.Enum type</span></span>

<span data-ttu-id="bf049-158">Il tipo `System.Enum` è la classe base astratta di tutti i tipi di enumerazione (questo è diverso dal tipo sottostante del tipo di enumerazione e distinct) e i membri ereditati da `System.Enum` sono disponibili in qualsiasi tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-158">The type `System.Enum` is the abstract base class of all enum types (this is distinct and different from the underlying type of the enum type), and the members inherited from `System.Enum` are available in any enum type.</span></span> <span data-ttu-id="bf049-159">Una conversione boxing ([conversioni Boxing](types.md#boxing-conversions)) esistente da qualsiasi tipo enum `System.Enum`e una conversione unboxing ([conversione Unboxing](types.md#unboxing-conversions)) esistente dal `System.Enum` a qualsiasi tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-159">A boxing conversion ([Boxing conversions](types.md#boxing-conversions)) exists from any enum type to `System.Enum`, and an unboxing conversion ([Unboxing conversions](types.md#unboxing-conversions)) exists from `System.Enum` to any enum type.</span></span>

<span data-ttu-id="bf049-160">Si noti che `System.Enum` non è a sua volta un' *enum_type*.</span><span class="sxs-lookup"><span data-stu-id="bf049-160">Note that `System.Enum` is not itself an *enum_type*.</span></span> <span data-ttu-id="bf049-161">Si tratta piuttosto di una *class_type* da cui tutti gli *enum_type*derivano.</span><span class="sxs-lookup"><span data-stu-id="bf049-161">Rather, it is a *class_type* from which all *enum_type*s are derived.</span></span> <span data-ttu-id="bf049-162">Il tipo `System.Enum` eredita dal tipo `System.ValueType` ([System. ValueType di tipo](types.md#the-systemvaluetype-type)), che, a sua volta eredita dal tipo `object`.</span><span class="sxs-lookup"><span data-stu-id="bf049-162">The type `System.Enum` inherits from the type `System.ValueType` ([The System.ValueType type](types.md#the-systemvaluetype-type)), which, in turn, inherits from type `object`.</span></span> <span data-ttu-id="bf049-163">In fase di esecuzione, un valore di tipo `System.Enum` può essere `null` o un riferimento a un valore boxed di qualsiasi tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-163">At run-time, a value of type `System.Enum` can be `null` or a reference to a boxed value of any enum type.</span></span>

## <a name="enum-values-and-operations"></a><span data-ttu-id="bf049-164">Operazioni e i valori di enumerazione</span><span class="sxs-lookup"><span data-stu-id="bf049-164">Enum values and operations</span></span>

<span data-ttu-id="bf049-165">Ogni tipo di enumerazione definisce un tipo distinto. una conversione esplicita di enumerazione ([conversioni enumerazione esplicita](conversions.md#explicit-enumeration-conversions)) è necessaria per convertire tra un tipo enum e un tipo integrale o tra due tipi di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-165">Each enum type defines a distinct type; an explicit enumeration conversion ([Explicit enumeration conversions](conversions.md#explicit-enumeration-conversions)) is required to convert between an enum type and an integral type, or between two enum types.</span></span> <span data-ttu-id="bf049-166">Il set di valori che può assumere un tipo enum non è limitato dai relativi membri enum.</span><span class="sxs-lookup"><span data-stu-id="bf049-166">The set of values that an enum type can take on is not limited by its enum members.</span></span> <span data-ttu-id="bf049-167">In particolare, qualsiasi valore del tipo sottostante di un'enumerazione può essere convertito nel tipo di enumerazione e rappresenta un valore valido distinto di quel tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="bf049-167">In particular, any value of the underlying type of an enum can be cast to the enum type, and is a distinct valid value of that enum type.</span></span>

<span data-ttu-id="bf049-168">Membri enum sono il tipo del rispettivo tipo enum contenitore (tranne che all'interno di altri inizializzatori del membro enum: vedere [membri Enum](enums.md#enum-members)).</span><span class="sxs-lookup"><span data-stu-id="bf049-168">Enum members have the type of their containing enum type (except within other enum member initializers: see [Enum members](enums.md#enum-members)).</span></span> <span data-ttu-id="bf049-169">Il valore di un membro di enumerazione dichiarato nel tipo di enumerazione `E` con il valore associato `v` è `(E)v`.</span><span class="sxs-lookup"><span data-stu-id="bf049-169">The value of an enum member declared in enum type `E` with associated value `v` is `(E)v`.</span></span>

<span data-ttu-id="bf049-170">Gli operatori seguenti possono essere utilizzati con valori dei tipi enum: `==`, `!=`, `<`, `>`, `<=`, `>=` ([gli operatori di confronto di enumerazione](expressions.md#enumeration-comparison-operators)), binary `+` ([Operatore di addizione](expressions.md#addition-operator)) binari `-` ([operatore di sottrazione](expressions.md#subtraction-operator)), `^`, `&`, `|` ([enumerazione logico gli operatori](expressions.md#enumeration-logical-operators)), `~` ([operatore di complemento bit per bit](expressions.md#bitwise-complement-operator)), `++` e `--` ([forma suffissa di incremento e decremento (operatori)](expressions.md#postfix-increment-and-decrement-operators) e[ Incremento prefisso e decremento (operatori)](expressions.md#prefix-increment-and-decrement-operators)).</span><span class="sxs-lookup"><span data-stu-id="bf049-170">The following operators can be used on values of enum types: `==`, `!=`, `<`, `>`, `<=`, `>=` ([Enumeration comparison operators](expressions.md#enumeration-comparison-operators)), binary `+` ([Addition operator](expressions.md#addition-operator)), binary `-` ([Subtraction operator](expressions.md#subtraction-operator)), `^`, `&`, `|` ([Enumeration logical operators](expressions.md#enumeration-logical-operators)), `~` ([Bitwise complement operator](expressions.md#bitwise-complement-operator)), `++` and `--` ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators) and [Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)).</span></span>

<span data-ttu-id="bf049-171">Ogni tipo di enumerazione automaticamente deriva dalla classe `System.Enum` (che, a sua volta deriva da `System.ValueType` e `object`).</span><span class="sxs-lookup"><span data-stu-id="bf049-171">Every enum type automatically derives from the class `System.Enum` (which, in turn, derives from `System.ValueType` and `object`).</span></span> <span data-ttu-id="bf049-172">Di conseguenza, metodi ereditati e le proprietà di questa classe sono utilizzabile con valori di un tipo enum.</span><span class="sxs-lookup"><span data-stu-id="bf049-172">Thus, inherited methods and properties of this class can be used on values of an enum type.</span></span>
