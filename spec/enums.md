---
ms.openlocfilehash: 3b142d7dbda8a94a4cf2c973a2e380065dcbf5ee
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703972"
---
# <a name="enums"></a><span data-ttu-id="85099-101">Enumerazioni</span><span class="sxs-lookup"><span data-stu-id="85099-101">Enums</span></span>

<span data-ttu-id="85099-102">Un ***tipo enum*** è un tipo di valore Distinct ([tipi valore](types.md#value-types)) che dichiara un set di costanti denominate.</span><span class="sxs-lookup"><span data-stu-id="85099-102">An ***enum type*** is a distinct value type ([Value types](types.md#value-types)) that declares a set of named constants.</span></span>

<span data-ttu-id="85099-103">Esempio</span><span class="sxs-lookup"><span data-stu-id="85099-103">The example</span></span>

```csharp
enum Color
{
    Red,
    Green,
    Blue
}
```

<span data-ttu-id="85099-104">dichiara un tipo enum denominato `Color` con i membri `Red`, `Green` e `Blue`.</span><span class="sxs-lookup"><span data-stu-id="85099-104">declares an enum type named `Color` with members `Red`, `Green`, and `Blue`.</span></span>

## <a name="enum-declarations"></a><span data-ttu-id="85099-105">Dichiarazioni enum</span><span class="sxs-lookup"><span data-stu-id="85099-105">Enum declarations</span></span>

<span data-ttu-id="85099-106">Una dichiarazione enum dichiara un nuovo tipo enum.</span><span class="sxs-lookup"><span data-stu-id="85099-106">An enum declaration declares a new enum type.</span></span> <span data-ttu-id="85099-107">Una dichiarazione enum inizia con la parola chiave `enum` e definisce il nome, l'accessibilità, il tipo sottostante e i membri dell'enumerazione.</span><span class="sxs-lookup"><span data-stu-id="85099-107">An enum declaration begins with the keyword `enum`, and defines the name, accessibility, underlying type, and members of the enum.</span></span>

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

<span data-ttu-id="85099-108">Ogni tipo di enumerazione ha un tipo integrale corrispondente denominato ***tipo sottostante*** del tipo enum.</span><span class="sxs-lookup"><span data-stu-id="85099-108">Each enum type has a corresponding integral type called the ***underlying type*** of the enum type.</span></span> <span data-ttu-id="85099-109">Questo tipo sottostante deve essere in grado di rappresentare tutti i valori di enumeratore definiti nell'enumerazione.</span><span class="sxs-lookup"><span data-stu-id="85099-109">This underlying type must be able to represent all the enumerator values defined in the enumeration.</span></span> <span data-ttu-id="85099-110">Una dichiarazione enum può dichiarare in modo esplicito un tipo sottostante di `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` o `ulong`.</span><span class="sxs-lookup"><span data-stu-id="85099-110">An enum declaration may explicitly declare an underlying type of `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` or `ulong`.</span></span> <span data-ttu-id="85099-111">Si noti che `char` non può essere usato come tipo sottostante.</span><span class="sxs-lookup"><span data-stu-id="85099-111">Note that `char` cannot be used as an underlying type.</span></span> <span data-ttu-id="85099-112">Una dichiarazione enum che non dichiara in modo esplicito un tipo sottostante ha un tipo sottostante di `int`.</span><span class="sxs-lookup"><span data-stu-id="85099-112">An enum declaration that does not explicitly declare an underlying type has an underlying type of `int`.</span></span>

<span data-ttu-id="85099-113">Esempio</span><span class="sxs-lookup"><span data-stu-id="85099-113">The example</span></span>

```csharp
enum Color: long
{
    Red,
    Green,
    Blue
}
```

<span data-ttu-id="85099-114">dichiara un'enumerazione con un tipo sottostante di `long`.</span><span class="sxs-lookup"><span data-stu-id="85099-114">declares an enum with an underlying type of `long`.</span></span> <span data-ttu-id="85099-115">Uno sviluppatore può scegliere di utilizzare un tipo sottostante di `long`, come nell'esempio, per consentire l'utilizzo di valori compresi nell'intervallo `long` ma non nell'intervallo di `int` oppure per mantenere questa opzione per il futuro.</span><span class="sxs-lookup"><span data-stu-id="85099-115">A developer might choose to use an underlying type of `long`, as in the example, to enable the use of values that are in the range of `long` but not in the range of `int`, or to preserve this option for the future.</span></span>

## <a name="enum-modifiers"></a><span data-ttu-id="85099-116">Modificatori enum</span><span class="sxs-lookup"><span data-stu-id="85099-116">Enum modifiers</span></span>

<span data-ttu-id="85099-117">Un *enum_declaration* può facoltativamente includere una sequenza di modificatori enum:</span><span class="sxs-lookup"><span data-stu-id="85099-117">An *enum_declaration* may optionally include a sequence of enum modifiers:</span></span>

```antlr
enum_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    ;
```

<span data-ttu-id="85099-118">Si tratta di un errore in fase di compilazione perché lo stesso modificatore venga visualizzato più volte in una dichiarazione di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="85099-118">It is a compile-time error for the same modifier to appear multiple times in an enum declaration.</span></span>

<span data-ttu-id="85099-119">I modificatori di una dichiarazione di enumerazione hanno lo stesso significato di quelli di una dichiarazione di classe ([modificatori di classe](classes.md#class-modifiers)).</span><span class="sxs-lookup"><span data-stu-id="85099-119">The modifiers of an enum declaration have the same meaning as those of a class declaration ([Class modifiers](classes.md#class-modifiers)).</span></span> <span data-ttu-id="85099-120">Si noti, tuttavia, che i modificatori `abstract` e `sealed` non sono consentiti in una dichiarazione enum.</span><span class="sxs-lookup"><span data-stu-id="85099-120">Note, however, that the `abstract` and `sealed` modifiers are not permitted in an enum declaration.</span></span> <span data-ttu-id="85099-121">Le enumerazioni non possono essere astratte e non consentire la derivazione.</span><span class="sxs-lookup"><span data-stu-id="85099-121">Enums cannot be abstract and do not permit derivation.</span></span>

## <a name="enum-members"></a><span data-ttu-id="85099-122">Membri enum</span><span class="sxs-lookup"><span data-stu-id="85099-122">Enum members</span></span>

<span data-ttu-id="85099-123">Il corpo di una dichiarazione di tipo enum definisce zero o più membri enum, ovvero le costanti denominate del tipo enum.</span><span class="sxs-lookup"><span data-stu-id="85099-123">The body of an enum type declaration defines zero or more enum members, which are the named constants of the enum type.</span></span> <span data-ttu-id="85099-124">Due membri enum non possono avere lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="85099-124">No two enum members can have the same name.</span></span>

```antlr
enum_member_declarations
    : enum_member_declaration (',' enum_member_declaration)*
    ;

enum_member_declaration
    : attributes? identifier ('=' constant_expression)?
    ;
```

<span data-ttu-id="85099-125">A ogni membro enum è associato un valore costante.</span><span class="sxs-lookup"><span data-stu-id="85099-125">Each enum member has an associated constant value.</span></span> <span data-ttu-id="85099-126">Il tipo di questo valore è il tipo sottostante per l'enumerazione che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="85099-126">The type of this value is the underlying type for the containing enum.</span></span> <span data-ttu-id="85099-127">Il valore costante per ogni membro enum deve essere compreso nell'intervallo del tipo sottostante per l'enumerazione.</span><span class="sxs-lookup"><span data-stu-id="85099-127">The constant value for each enum member must be in the range of the underlying type for the enum.</span></span> <span data-ttu-id="85099-128">Esempio</span><span class="sxs-lookup"><span data-stu-id="85099-128">The example</span></span>

```csharp
enum Color: uint
{
    Red = -1,
    Green = -2,
    Blue = -3
}
```

<span data-ttu-id="85099-129">genera un errore in fase di compilazione perché i valori costanti `-1`, `-2` e `-3` non sono compresi nell'intervallo del tipo integrale sottostante `uint`.</span><span class="sxs-lookup"><span data-stu-id="85099-129">results in a compile-time error because the constant values `-1`, `-2`, and `-3` are not in the range of the underlying integral type `uint`.</span></span>

<span data-ttu-id="85099-130">Più membri enum possono condividere lo stesso valore associato.</span><span class="sxs-lookup"><span data-stu-id="85099-130">Multiple enum members may share the same associated value.</span></span> <span data-ttu-id="85099-131">Esempio</span><span class="sxs-lookup"><span data-stu-id="85099-131">The example</span></span>

```csharp
enum Color 
{
    Red,
    Green,
    Blue,

    Max = Blue
}
```

<span data-ttu-id="85099-132">Mostra un'enumerazione in cui due membri enum, `Blue` e `Max`, hanno lo stesso valore associato.</span><span class="sxs-lookup"><span data-stu-id="85099-132">shows an enum in which two enum members -- `Blue` and `Max` -- have the same associated value.</span></span>

<span data-ttu-id="85099-133">Il valore associato di un membro enum viene assegnato in modo implicito o esplicito.</span><span class="sxs-lookup"><span data-stu-id="85099-133">The associated value of an enum member is assigned either implicitly or explicitly.</span></span> <span data-ttu-id="85099-134">Se la dichiarazione del membro enum dispone di un inizializzatore *constant_expression* , il valore di tale espressione costante, convertito in modo implicito nel tipo sottostante dell'enumerazione, è il valore associato del membro enum.</span><span class="sxs-lookup"><span data-stu-id="85099-134">If the declaration of the enum member has a *constant_expression* initializer, the value of that constant expression, implicitly converted to the underlying type of the enum, is the associated value of the enum member.</span></span> <span data-ttu-id="85099-135">Se la dichiarazione del membro enum non dispone di un inizializzatore, il relativo valore associato viene impostato in modo implicito, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="85099-135">If the declaration of the enum member has no initializer, its associated value is set implicitly, as follows:</span></span>

*  <span data-ttu-id="85099-136">Se il membro enum è il primo membro enum dichiarato nel tipo enum, il valore associato è zero.</span><span class="sxs-lookup"><span data-stu-id="85099-136">If the enum member is the first enum member declared in the enum type, its associated value is zero.</span></span>
*  <span data-ttu-id="85099-137">In caso contrario, il valore associato del membro enum viene ottenuto aumentando di uno il valore associato del membro enum testuale precedente.</span><span class="sxs-lookup"><span data-stu-id="85099-137">Otherwise, the associated value of the enum member is obtained by increasing the associated value of the textually preceding enum member by one.</span></span> <span data-ttu-id="85099-138">Questo valore maggiore deve essere compreso nell'intervallo di valori che possono essere rappresentati dal tipo sottostante; in caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="85099-138">This increased value must be within the range of values that can be represented by the underlying type, otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="85099-139">Esempio</span><span class="sxs-lookup"><span data-stu-id="85099-139">The example</span></span>

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

<span data-ttu-id="85099-140">stampa i nomi dei membri di enumerazione e i relativi valori associati.</span><span class="sxs-lookup"><span data-stu-id="85099-140">prints out the enum member names and their associated values.</span></span> <span data-ttu-id="85099-141">L'output è:</span><span class="sxs-lookup"><span data-stu-id="85099-141">The output is:</span></span>

```console
Red = 0
Green = 10
Blue = 11
```

<span data-ttu-id="85099-142">per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="85099-142">for the following reasons:</span></span>

*  <span data-ttu-id="85099-143">al membro enum `Red` viene assegnato automaticamente il valore zero (poiché non contiene inizializzatore ed è il primo membro enum);</span><span class="sxs-lookup"><span data-stu-id="85099-143">the enum member `Red` is automatically assigned the value zero (since it has no initializer and is the first enum member);</span></span>
*  <span data-ttu-id="85099-144">al membro enum `Green` viene assegnato in modo esplicito il valore `10`;</span><span class="sxs-lookup"><span data-stu-id="85099-144">the enum member `Green` is explicitly given the value `10`;</span></span>
*  <span data-ttu-id="85099-145">al membro enum `Blue` viene assegnato automaticamente il valore maggiore di quello del membro che lo precede in modo testuale.</span><span class="sxs-lookup"><span data-stu-id="85099-145">and the enum member `Blue` is automatically assigned the value one greater than the member that textually precedes it.</span></span>

<span data-ttu-id="85099-146">Il valore associato di un membro enum non può, direttamente o indirettamente, utilizzare il valore del proprio membro enum associato.</span><span class="sxs-lookup"><span data-stu-id="85099-146">The associated value of an enum member may not, directly or indirectly, use the value of its own associated enum member.</span></span> <span data-ttu-id="85099-147">Oltre a questa restrizione della circolare, gli inizializzatori di membri di enumerazione possono fare riferimento ad altri inizializzatori di membri enum, indipendentemente dalla loro posizione testuale.</span><span class="sxs-lookup"><span data-stu-id="85099-147">Other than this circularity restriction, enum member initializers may freely refer to other enum member initializers, regardless of their textual position.</span></span> <span data-ttu-id="85099-148">All'interno di un inizializzatore di membro enum, i valori di altri membri enum vengono sempre considerati come aventi il tipo del relativo tipo sottostante, in modo che i cast non siano necessari quando si fa riferimento ad altri membri enum.</span><span class="sxs-lookup"><span data-stu-id="85099-148">Within an enum member initializer, values of other enum members are always treated as having the type of their underlying type, so that casts are not necessary when referring to other enum members.</span></span>

<span data-ttu-id="85099-149">Esempio</span><span class="sxs-lookup"><span data-stu-id="85099-149">The example</span></span>

```csharp
enum Circular
{
    A = B,
    B
}
```

<span data-ttu-id="85099-150">genera un errore in fase di compilazione perché le dichiarazioni di `A` e `B` sono circolari.</span><span class="sxs-lookup"><span data-stu-id="85099-150">results in a compile-time error because the declarations of `A` and `B` are circular.</span></span> <span data-ttu-id="85099-151">`A` dipende da `B` in modo esplicito e `B` dipende da `A` in modo implicito.</span><span class="sxs-lookup"><span data-stu-id="85099-151">`A` depends on `B` explicitly, and `B` depends on `A` implicitly.</span></span>

<span data-ttu-id="85099-152">I membri enum sono denominati e hanno come ambito un modo esattamente analogo ai campi all'interno delle classi.</span><span class="sxs-lookup"><span data-stu-id="85099-152">Enum members are named and scoped in a manner exactly analogous to fields within classes.</span></span> <span data-ttu-id="85099-153">L'ambito di un membro enum è il corpo del tipo enum che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="85099-153">The scope of an enum member is the body of its containing enum type.</span></span> <span data-ttu-id="85099-154">All'interno di tale ambito, è possibile fare riferimento ai membri enum con il nome semplice.</span><span class="sxs-lookup"><span data-stu-id="85099-154">Within that scope, enum members can be referred to by their simple name.</span></span> <span data-ttu-id="85099-155">Da tutto il codice, il nome di un membro enum deve essere qualificato con il nome del relativo tipo enum.</span><span class="sxs-lookup"><span data-stu-id="85099-155">From all other code, the name of an enum member must be qualified with the name of its enum type.</span></span> <span data-ttu-id="85099-156">I membri enum non hanno alcuna accessibilità dichiarata. un membro enum è accessibile se il tipo enum che lo contiene è accessibile.</span><span class="sxs-lookup"><span data-stu-id="85099-156">Enum members do not have any declared accessibility -- an enum member is accessible if its containing enum type is accessible.</span></span>

## <a name="the-systemenum-type"></a><span data-ttu-id="85099-157">Tipo System. Enum</span><span class="sxs-lookup"><span data-stu-id="85099-157">The System.Enum type</span></span>

<span data-ttu-id="85099-158">Il tipo `System.Enum` è la classe di base astratta di tutti i tipi enum (distinti e diversi dal tipo sottostante del tipo enum) e i membri ereditati da `System.Enum` sono disponibili in qualsiasi tipo enum.</span><span class="sxs-lookup"><span data-stu-id="85099-158">The type `System.Enum` is the abstract base class of all enum types (this is distinct and different from the underlying type of the enum type), and the members inherited from `System.Enum` are available in any enum type.</span></span> <span data-ttu-id="85099-159">Una conversione boxing ([conversioni boxing](types.md#boxing-conversions)) esiste da qualsiasi tipo enum a `System.Enum` ed è presente una conversione unboxing (conversione[unboxing](types.md#unboxing-conversions)) da `System.Enum` a qualsiasi tipo enum.</span><span class="sxs-lookup"><span data-stu-id="85099-159">A boxing conversion ([Boxing conversions](types.md#boxing-conversions)) exists from any enum type to `System.Enum`, and an unboxing conversion ([Unboxing conversions](types.md#unboxing-conversions)) exists from `System.Enum` to any enum type.</span></span>

<span data-ttu-id="85099-160">Si noti che `System.Enum` non è un *enum_type*.</span><span class="sxs-lookup"><span data-stu-id="85099-160">Note that `System.Enum` is not itself an *enum_type*.</span></span> <span data-ttu-id="85099-161">Piuttosto, si tratta di un *class_type* da cui derivano tutti i *enum_type*.</span><span class="sxs-lookup"><span data-stu-id="85099-161">Rather, it is a *class_type* from which all *enum_type*s are derived.</span></span> <span data-ttu-id="85099-162">Il tipo `System.Enum` eredita dal tipo `System.ValueType` ([il tipo System. ValueType](types.md#the-systemvaluetype-type)), che a sua volta eredita dal tipo `object`.</span><span class="sxs-lookup"><span data-stu-id="85099-162">The type `System.Enum` inherits from the type `System.ValueType` ([The System.ValueType type](types.md#the-systemvaluetype-type)), which, in turn, inherits from type `object`.</span></span> <span data-ttu-id="85099-163">In fase di esecuzione, un valore di tipo `System.Enum` può essere `null` o un riferimento a un valore boxed di qualsiasi tipo enum.</span><span class="sxs-lookup"><span data-stu-id="85099-163">At run-time, a value of type `System.Enum` can be `null` or a reference to a boxed value of any enum type.</span></span>

## <a name="enum-values-and-operations"></a><span data-ttu-id="85099-164">Valori e operazioni di enumerazione</span><span class="sxs-lookup"><span data-stu-id="85099-164">Enum values and operations</span></span>

<span data-ttu-id="85099-165">Ogni tipo enum definisce un tipo distinto; per la conversione tra un tipo enum e un tipo integrale o tra due tipi enum è necessaria una conversione esplicita dell'enumerazione ([conversioni esplicite dell'enumerazione](conversions.md#explicit-enumeration-conversions)).</span><span class="sxs-lookup"><span data-stu-id="85099-165">Each enum type defines a distinct type; an explicit enumeration conversion ([Explicit enumeration conversions](conversions.md#explicit-enumeration-conversions)) is required to convert between an enum type and an integral type, or between two enum types.</span></span> <span data-ttu-id="85099-166">Il set di valori che un tipo enum può assumere non è limitato dai relativi membri enum.</span><span class="sxs-lookup"><span data-stu-id="85099-166">The set of values that an enum type can take on is not limited by its enum members.</span></span> <span data-ttu-id="85099-167">In particolare, è possibile eseguire il cast di qualsiasi valore del tipo sottostante di un'enumerazione al tipo enum ed è un valore valido distinto di quel tipo enum.</span><span class="sxs-lookup"><span data-stu-id="85099-167">In particular, any value of the underlying type of an enum can be cast to the enum type, and is a distinct valid value of that enum type.</span></span>

<span data-ttu-id="85099-168">I membri enum hanno il tipo del tipo enum che lo contiene, tranne che all'interno di altri inizializzatori di membri enum: vedere [membri enum](enums.md#enum-members).</span><span class="sxs-lookup"><span data-stu-id="85099-168">Enum members have the type of their containing enum type (except within other enum member initializers: see [Enum members](enums.md#enum-members)).</span></span> <span data-ttu-id="85099-169">Il valore di un membro enum dichiarato nel tipo enum `E` con valore associato `v` è `(E)v`.</span><span class="sxs-lookup"><span data-stu-id="85099-169">The value of an enum member declared in enum type `E` with associated value `v` is `(E)v`.</span></span>

<span data-ttu-id="85099-170">Gli operatori seguenti possono essere utilizzati in valori di tipi enum: `==`, `!=`, `<`, `>`, `<=`, `>=` @ no__t-6 ([operatori di confronto di enumerazione](expressions.md#enumeration-comparison-operators)), Binary `+` @ no__t-9 ([operatore di addizione](expressions.md#addition-operator)), @no__t binaria-11 @ no__ t-12 ([operatore di sottrazione](expressions.md#subtraction-operator)), 4, 5, 6 @ no__t-17 ([operatori logici di enumerazione](expressions.md#enumeration-logical-operators)), 9 @ no__t-20 ([operatore di complemento bit per bit](expressions.md#bitwise-complement-operator)), 2 e 3 @ no__t-24 ([incremento suffisso e operatori di decremento](expressions.md#postfix-increment-and-decrement-operators) e [operatori di incremento e decremento del prefisso](expressions.md#prefix-increment-and-decrement-operators)).</span><span class="sxs-lookup"><span data-stu-id="85099-170">The following operators can be used on values of enum types: `==`, `!=`, `<`, `>`, `<=`, `>=` ([Enumeration comparison operators](expressions.md#enumeration-comparison-operators)), binary `+` ([Addition operator](expressions.md#addition-operator)), binary `-` ([Subtraction operator](expressions.md#subtraction-operator)), `^`, `&`, `|` ([Enumeration logical operators](expressions.md#enumeration-logical-operators)), `~` ([Bitwise complement operator](expressions.md#bitwise-complement-operator)), `++` and `--` ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators) and [Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)).</span></span>

<span data-ttu-id="85099-171">Ogni tipo enum deriva automaticamente dalla classe `System.Enum` (che, a sua volta, deriva da `System.ValueType` e `object`).</span><span class="sxs-lookup"><span data-stu-id="85099-171">Every enum type automatically derives from the class `System.Enum` (which, in turn, derives from `System.ValueType` and `object`).</span></span> <span data-ttu-id="85099-172">Pertanto, i metodi e le proprietà ereditati di questa classe possono essere utilizzati in valori di un tipo enum.</span><span class="sxs-lookup"><span data-stu-id="85099-172">Thus, inherited methods and properties of this class can be used on values of an enum type.</span></span>
