# <a name="attributes"></a><span data-ttu-id="59804-101">Attributi</span><span class="sxs-lookup"><span data-stu-id="59804-101">Attributes</span></span>

<span data-ttu-id="59804-102">Gran parte del linguaggio c# consente al programmatore di specificare informazioni dichiarative sulle entità definite nel programma.</span><span class="sxs-lookup"><span data-stu-id="59804-102">Much of the C# language enables the programmer to specify declarative information about the entities defined in the program.</span></span> <span data-ttu-id="59804-103">Ad esempio, specificare l'accessibilità di un metodo in una classe decorandolo con il *method_modifier*s `public`, `protected`, `internal`, e `private`.</span><span class="sxs-lookup"><span data-stu-id="59804-103">For example, the accessibility of a method in a class is specified by decorating it with the *method_modifier*s `public`, `protected`, `internal`, and `private`.</span></span>

<span data-ttu-id="59804-104">C# consente ai programmatori di creare nuovi tipi di informazioni dichiarative, dette ***attributi***.</span><span class="sxs-lookup"><span data-stu-id="59804-104">C# enables programmers to invent new kinds of declarative information, called ***attributes***.</span></span> <span data-ttu-id="59804-105">I programmatori possono quindi allegare gli attributi a diverse entità di programma e recuperare informazioni sugli attributi in un ambiente di runtime.</span><span class="sxs-lookup"><span data-stu-id="59804-105">Programmers can then attach attributes to various program entities, and retrieve attribute information in a run-time environment.</span></span> <span data-ttu-id="59804-106">Ad esempio, un framework è possibile definire un `HelpAttribute` attributo che può essere inserito su determinati elementi di programma (ad esempio classi e metodi) per fornire un mapping tra gli elementi di programma e la relativa documentazione.</span><span class="sxs-lookup"><span data-stu-id="59804-106">For instance, a framework might define a `HelpAttribute` attribute that can be placed on certain program elements (such as classes and methods) to provide a mapping from those program elements to their documentation.</span></span>

<span data-ttu-id="59804-107">Gli attributi sono definiti tramite la dichiarazione di classi di attributi ([classi Attribute](attributes.md#attribute-classes)), che possono avere parametri posizionali e denominati ([posizionale e i parametri denominati](attributes.md#positional-and-named-parameters)).</span><span class="sxs-lookup"><span data-stu-id="59804-107">Attributes are defined through the declaration of attribute classes ([Attribute classes](attributes.md#attribute-classes)), which may have positional and named parameters ([Positional and named parameters](attributes.md#positional-and-named-parameters)).</span></span> <span data-ttu-id="59804-108">Gli attributi sono associati all'entità in un programma c# con specifiche per gli attributi ([specifica degli attributi](attributes.md#attribute-specification)) e possono essere recuperati in fase di esecuzione come istanze di attributi ([istanze di attributo](attributes.md#attribute-instances)).</span><span class="sxs-lookup"><span data-stu-id="59804-108">Attributes are attached to entities in a C# program using attribute specifications ([Attribute specification](attributes.md#attribute-specification)), and can be retrieved at run-time as attribute instances ([Attribute instances](attributes.md#attribute-instances)).</span></span>

## <a name="attribute-classes"></a><span data-ttu-id="59804-109">Classi di attributi</span><span class="sxs-lookup"><span data-stu-id="59804-109">Attribute classes</span></span>

<span data-ttu-id="59804-110">Una classe che deriva dalla classe astratta `System.Attribute`, direttamente o indirettamente, è un ***classe attribute***.</span><span class="sxs-lookup"><span data-stu-id="59804-110">A class that derives from the abstract class `System.Attribute`, whether directly or indirectly, is an ***attribute class***.</span></span> <span data-ttu-id="59804-111">La dichiarazione di un attributo definisce un nuovo tipo di ***attributo*** che può essere inserito in una dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="59804-111">The declaration of an attribute class defines a new kind of ***attribute*** that can be placed on a declaration.</span></span> <span data-ttu-id="59804-112">Per convenzione, le classi di attributi sono denominate con un suffisso di `Attribute`.</span><span class="sxs-lookup"><span data-stu-id="59804-112">By convention, attribute classes are named with a suffix of `Attribute`.</span></span> <span data-ttu-id="59804-113">Usi di un attributo possono includere oppure omettere questo suffisso.</span><span class="sxs-lookup"><span data-stu-id="59804-113">Uses of an attribute may either include or omit this suffix.</span></span>

### <a name="attribute-usage"></a><span data-ttu-id="59804-114">Utilizzo dell'attributo</span><span class="sxs-lookup"><span data-stu-id="59804-114">Attribute usage</span></span>

<span data-ttu-id="59804-115">L'attributo `AttributeUsage` ([attributo AttributeUsage The](attributes.md#the-attributeusage-attribute)) viene utilizzato per descrivere l'utilizzo di una classe attribute.</span><span class="sxs-lookup"><span data-stu-id="59804-115">The attribute `AttributeUsage` ([The AttributeUsage attribute](attributes.md#the-attributeusage-attribute)) is used to describe how an attribute class can be used.</span></span>

<span data-ttu-id="59804-116">`AttributeUsage` ha un parametro posizionale ([posizionale e i parametri denominati](attributes.md#positional-and-named-parameters)) che consente di specificare i tipi di dichiarazioni in cui può essere utilizzata una classe attribute.</span><span class="sxs-lookup"><span data-stu-id="59804-116">`AttributeUsage` has a positional parameter ([Positional and named parameters](attributes.md#positional-and-named-parameters)) that enables an attribute class to specify the kinds of declarations on which it can be used.</span></span> <span data-ttu-id="59804-117">L'esempio</span><span class="sxs-lookup"><span data-stu-id="59804-117">The example</span></span>

```csharp
using System;

[AttributeUsage(AttributeTargets.Class | AttributeTargets.Interface)]
public class SimpleAttribute: Attribute 
{
    ...
}
```

<span data-ttu-id="59804-118">definisce una classe attribute denominata `SimpleAttribute` che può essere inserito nel *class_declaration*s e *interface_declaration*solo.</span><span class="sxs-lookup"><span data-stu-id="59804-118">defines an attribute class named `SimpleAttribute` that can be placed on *class_declaration*s and *interface_declaration*s only.</span></span> <span data-ttu-id="59804-119">L'esempio</span><span class="sxs-lookup"><span data-stu-id="59804-119">The example</span></span>

```csharp
[Simple] class Class1 {...}

[Simple] interface Interface1 {...}
```

<span data-ttu-id="59804-120">vengono illustrati diversi utilizzi del `Simple` attributo.</span><span class="sxs-lookup"><span data-stu-id="59804-120">shows several uses of the `Simple` attribute.</span></span> <span data-ttu-id="59804-121">Anche se questo attributo viene definito con il nome `SimpleAttribute`, quando questo attributo viene utilizzato, il `Attribute` suffisso può essere omesso, causando il nome breve `Simple`.</span><span class="sxs-lookup"><span data-stu-id="59804-121">Although this attribute is defined with the name `SimpleAttribute`, when this attribute is used, the `Attribute` suffix may be omitted, resulting in the short name `Simple`.</span></span> <span data-ttu-id="59804-122">Di conseguenza, l'esempio precedente è semanticamente equivalente alla seguente:</span><span class="sxs-lookup"><span data-stu-id="59804-122">Thus, the example above is semantically equivalent to the following:</span></span>

```csharp
[SimpleAttribute] class Class1 {...}

[SimpleAttribute] interface Interface1 {...}
```

<span data-ttu-id="59804-123">`AttributeUsage` ha un parametro denominato ([posizionale e i parametri denominati](attributes.md#positional-and-named-parameters)) denominato `AllowMultiple`, che indica se l'attributo può essere specificato più volte per una determinata entità.</span><span class="sxs-lookup"><span data-stu-id="59804-123">`AttributeUsage` has a named parameter ([Positional and named parameters](attributes.md#positional-and-named-parameters)) called `AllowMultiple`, which indicates whether the attribute can be specified more than once for a given entity.</span></span> <span data-ttu-id="59804-124">Se `AllowMultiple` per un attributo di classe è true e quindi tale classe di attributo è un ***classi di attributo multiuso***e può essere specificato più volte su un'entità.</span><span class="sxs-lookup"><span data-stu-id="59804-124">If `AllowMultiple` for an attribute class is true, then that attribute class is a ***multi-use attribute class***, and can be specified more than once on an entity.</span></span> <span data-ttu-id="59804-125">Se `AllowMultiple` per un attributo di classe è false o non viene specificato, quindi tale classe di attributo è un ***monouso classe attribute***e può essere specificato più di una volta in un'entità.</span><span class="sxs-lookup"><span data-stu-id="59804-125">If `AllowMultiple` for an attribute class is false or it is unspecified, then that attribute class is a ***single-use attribute class***, and can be specified at most once on an entity.</span></span>

<span data-ttu-id="59804-126">L'esempio</span><span class="sxs-lookup"><span data-stu-id="59804-126">The example</span></span>

```csharp
using System;

[AttributeUsage(AttributeTargets.Class, AllowMultiple = true)]
public class AuthorAttribute: Attribute
{
    private string name;

    public AuthorAttribute(string name) {
        this.name = name;
    }

    public string Name {
        get { return name; }
    }
}
```

<span data-ttu-id="59804-127">definisce una classe di attributo multiuso denominata `AuthorAttribute`.</span><span class="sxs-lookup"><span data-stu-id="59804-127">defines a multi-use attribute class named `AuthorAttribute`.</span></span> <span data-ttu-id="59804-128">L'esempio</span><span class="sxs-lookup"><span data-stu-id="59804-128">The example</span></span>

```csharp
[Author("Brian Kernighan"), Author("Dennis Ritchie")] 
class Class1
{
    ...
}
```

<span data-ttu-id="59804-129">viene illustrata una dichiarazione di classe con due utilizzi del `Author` attributo.</span><span class="sxs-lookup"><span data-stu-id="59804-129">shows a class declaration with two uses of the `Author` attribute.</span></span>

<span data-ttu-id="59804-130">`AttributeUsage` include un altro parametro denominato `Inherited`, che indica se l'attributo, quando viene specificato in una classe base, inoltre viene ereditato dalle classi che derivano da tale classe.</span><span class="sxs-lookup"><span data-stu-id="59804-130">`AttributeUsage` has another named parameter called `Inherited`, which indicates whether the attribute, when specified on a base class, is also inherited by classes that derive from that base class.</span></span> <span data-ttu-id="59804-131">Se `Inherited` per un attributo di classe è true, l'attributo in questione viene ereditata.</span><span class="sxs-lookup"><span data-stu-id="59804-131">If `Inherited` for an attribute class is true, then that attribute is inherited.</span></span> <span data-ttu-id="59804-132">Se `Inherited` per un attributo di classe è false, l'attributo non è ereditato.</span><span class="sxs-lookup"><span data-stu-id="59804-132">If `Inherited` for an attribute class is false then that attribute is not inherited.</span></span> <span data-ttu-id="59804-133">Se non viene specificato, il valore predefinito è true.</span><span class="sxs-lookup"><span data-stu-id="59804-133">If it is unspecified, its default value is true.</span></span>

<span data-ttu-id="59804-134">Una classe attribute `X` non ha un `AttributeUsage` attributo collegato, come in</span><span class="sxs-lookup"><span data-stu-id="59804-134">An attribute class `X` not having an `AttributeUsage` attribute attached to it, as in</span></span>

```csharp
using System;

class X: Attribute {...}
```

<span data-ttu-id="59804-135">È equivalente alla seguente:</span><span class="sxs-lookup"><span data-stu-id="59804-135">is equivalent to the following:</span></span>

```csharp
using System;

[AttributeUsage(
    AttributeTargets.All,
    AllowMultiple = false,
    Inherited = true)
]
class X: Attribute {...}
```

### <a name="positional-and-named-parameters"></a><span data-ttu-id="59804-136">Parametri posizionali e denominati</span><span class="sxs-lookup"><span data-stu-id="59804-136">Positional and named parameters</span></span>

<span data-ttu-id="59804-137">Le classi di attributo possono avere ***parametri posizionali*** e ***parametri denominati***.</span><span class="sxs-lookup"><span data-stu-id="59804-137">Attribute classes can have ***positional parameters*** and ***named parameters***.</span></span> <span data-ttu-id="59804-138">Ogni costruttore di istanza pubblici per una classe attribute definisce una sequenza valida di parametri posizionali per classe di attributi.</span><span class="sxs-lookup"><span data-stu-id="59804-138">Each public instance constructor for an attribute class defines a valid sequence of positional parameters for that attribute class.</span></span> <span data-ttu-id="59804-139">Ogni campo di lettura / scrittura pubblica non statico e la proprietà di una classe attribute definisce un parametro denominato per la classe di attributo.</span><span class="sxs-lookup"><span data-stu-id="59804-139">Each non-static public read-write field and property for an attribute class defines a named parameter for the attribute class.</span></span>

<span data-ttu-id="59804-140">L'esempio</span><span class="sxs-lookup"><span data-stu-id="59804-140">The example</span></span>

```csharp
using System;

[AttributeUsage(AttributeTargets.Class)]
public class HelpAttribute: Attribute
{
    public HelpAttribute(string url) {        // Positional parameter
        ...
    }

    public string Topic {                     // Named parameter
        get {...}
        set {...}
    }

    public string Url {
        get {...}
    }
}
```

<span data-ttu-id="59804-141">definisce una classe attribute denominata `HelpAttribute` che dispone di un parametro posizionale `url`e un parametro denominato, `Topic`.</span><span class="sxs-lookup"><span data-stu-id="59804-141">defines an attribute class named `HelpAttribute` that has one positional parameter, `url`, and one named parameter, `Topic`.</span></span> <span data-ttu-id="59804-142">Anche se è non statico e pubblico, la proprietà `Url` non definisce un parametro denominato, perché non è in lettura / scrittura.</span><span class="sxs-lookup"><span data-stu-id="59804-142">Although it is non-static and public, the property `Url` does not define a named parameter, since it is not read-write.</span></span>

<span data-ttu-id="59804-143">Questa classe di attributi può essere usata come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="59804-143">This attribute class might be used as follows:</span></span>

```csharp
[Help("http://www.mycompany.com/.../Class1.htm")]
class Class1
{
    ...
}

[Help("http://www.mycompany.com/.../Misc.htm", Topic = "Class2")]
class Class2
{
    ...
}
```

### <a name="attribute-parameter-types"></a><span data-ttu-id="59804-144">Tipi di parametro di attributo</span><span class="sxs-lookup"><span data-stu-id="59804-144">Attribute parameter types</span></span>

<span data-ttu-id="59804-145">I tipi di parametri posizionali e denominati per una classe di attributo sono limitati per il ***tipi di parametro di attributo***, che sono:</span><span class="sxs-lookup"><span data-stu-id="59804-145">The types of positional and named parameters for an attribute class are limited to the ***attribute parameter types***, which are:</span></span>

*  <span data-ttu-id="59804-146">Uno dei tipi seguenti: `bool`, `byte`, `char`, `double`, `float`, `int`, `long`, `sbyte`, `short`, `string`, `uint`, `ulong`, `ushort`.</span><span class="sxs-lookup"><span data-stu-id="59804-146">One of the following types: `bool`, `byte`, `char`, `double`, `float`, `int`, `long`, `sbyte`, `short`, `string`, `uint`, `ulong`, `ushort`.</span></span>
*  <span data-ttu-id="59804-147">Tipo `object`.</span><span class="sxs-lookup"><span data-stu-id="59804-147">The type `object`.</span></span>
*  <span data-ttu-id="59804-148">Tipo `System.Type`.</span><span class="sxs-lookup"><span data-stu-id="59804-148">The type `System.Type`.</span></span>
*  <span data-ttu-id="59804-149">Un tipo enum, purché con accessibilità pubblica e i tipi in cui è annidato (se presente) avere accessibilità pubblica ([specifica degli attributi](attributes.md#attribute-specification)).</span><span class="sxs-lookup"><span data-stu-id="59804-149">An enum type, provided it has public accessibility and the types in which it is nested (if any) also have public accessibility ([Attribute specification](attributes.md#attribute-specification)).</span></span>
*  <span data-ttu-id="59804-150">Matrici unidimensionali dei tipi precedenti.</span><span class="sxs-lookup"><span data-stu-id="59804-150">Single-dimensional arrays of the above types.</span></span>
*  <span data-ttu-id="59804-151">Un argomento del costruttore o un campo pubblico che non dispone di uno di questi tipi, non può essere utilizzato come un parametro posizionale o denominato in una specifica di attributo.</span><span class="sxs-lookup"><span data-stu-id="59804-151">A constructor argument or public field which does not have one of these types, cannot be used as a positional or named parameter in an attribute specification.</span></span>

## <a name="attribute-specification"></a><span data-ttu-id="59804-152">Specifica di attributo</span><span class="sxs-lookup"><span data-stu-id="59804-152">Attribute specification</span></span>

<span data-ttu-id="59804-153">***Specifica degli attributi*** è l'applicazione di un attributo definito in precedenza a una dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="59804-153">***Attribute specification*** is the application of a previously defined attribute to a declaration.</span></span> <span data-ttu-id="59804-154">Un attributo è una parte di informazioni dichiarative aggiuntive che sono specificate per una dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="59804-154">An attribute is a piece of additional declarative information that is specified for a declaration.</span></span> <span data-ttu-id="59804-155">Attributi possono essere specificati in ambito globale (per specificare attributi per l'assembly o un modulo che lo contiene) e per *type_declaration*s ([dichiarazioni di tipo](namespaces.md#type-declarations)), *class_member_declaration* s ([digitare i vincoli del parametro](classes.md#type-parameter-constraints)), *interface_member_declaration*s ([membri di interfaccia](interfaces.md#interface-members)), *struct_member _declaration*s ([i membri Struct](structs.md#struct-members)), *enum_member_declaration*s ([membri Enum](enums.md#enum-members)), *accessor_declarations*  ([Le funzioni di accesso](classes.md#accessors)), *event_accessor_declarations* ([di eventi campo](classes.md#field-like-events)), e *formal_parameter_list*s ([i parametri del metodo](classes.md#method-parameters)).</span><span class="sxs-lookup"><span data-stu-id="59804-155">Attributes can be specified at global scope (to specify attributes on the containing assembly or module) and for *type_declaration*s ([Type declarations](namespaces.md#type-declarations)), *class_member_declaration*s ([Type parameter constraints](classes.md#type-parameter-constraints)), *interface_member_declaration*s ([Interface members](interfaces.md#interface-members)), *struct_member_declaration*s ([Struct members](structs.md#struct-members)), *enum_member_declaration*s ([Enum members](enums.md#enum-members)), *accessor_declarations* ([Accessors](classes.md#accessors)), *event_accessor_declarations* ([Field-like events](classes.md#field-like-events)), and *formal_parameter_list*s ([Method parameters](classes.md#method-parameters)).</span></span>

<span data-ttu-id="59804-156">Gli attributi sono specificati in ***sezioni attributo***.</span><span class="sxs-lookup"><span data-stu-id="59804-156">Attributes are specified in ***attribute sections***.</span></span> <span data-ttu-id="59804-157">Una sezione di attributo è costituito da una coppia di parentesi quadre che racchiudono un elenco delimitato da virgole di uno o più attributi.</span><span class="sxs-lookup"><span data-stu-id="59804-157">An attribute section consists of a pair of square brackets, which surround a comma-separated list of one or more attributes.</span></span> <span data-ttu-id="59804-158">L'ordine in cui gli attributi sono specificati in tale elenco, e l'ordine in cui sezioni collegata alla stessa entità di programma sono disposti, non è significativo.</span><span class="sxs-lookup"><span data-stu-id="59804-158">The order in which attributes are specified in such a list, and the order in which sections attached to the same program entity are arranged, is not significant.</span></span> <span data-ttu-id="59804-159">Ad esempio, le specifiche per gli attributi `[A][B]`, `[B][A]`, `[A,B]`, e `[B,A]` sono equivalenti.</span><span class="sxs-lookup"><span data-stu-id="59804-159">For instance, the attribute specifications `[A][B]`, `[B][A]`, `[A,B]`, and `[B,A]` are equivalent.</span></span>

```antlr
global_attributes
    : global_attribute_section+
    ;

global_attribute_section
    : '[' global_attribute_target_specifier attribute_list ']'
    | '[' global_attribute_target_specifier attribute_list ',' ']'
    ;

global_attribute_target_specifier
    : global_attribute_target ':'
    ;

global_attribute_target
    : 'assembly'
    | 'module'
    ;

attributes
    : attribute_section+
    ;

attribute_section
    : '[' attribute_target_specifier? attribute_list ']'
    | '[' attribute_target_specifier? attribute_list ',' ']'
    ;

attribute_target_specifier
    : attribute_target ':'
    ;

attribute_target
    : 'field'
    | 'event'
    | 'method'
    | 'param'
    | 'property'
    | 'return'
    | 'type'
    ;

attribute_list
    : attribute (',' attribute)*
    ;

attribute
    : attribute_name attribute_arguments?
    ;

attribute_name
    : type_name
    ;

attribute_arguments
    : '(' positional_argument_list? ')'
    | '(' positional_argument_list ',' named_argument_list ')'
    | '(' named_argument_list ')'
    ;

positional_argument_list
    : positional_argument (',' positional_argument)*
    ;

positional_argument
    : attribute_argument_expression
    ;

named_argument_list
    : named_argument (','  named_argument)*
    ;

named_argument
    : identifier '=' attribute_argument_expression
    ;

attribute_argument_expression
    : expression
    ;
```

<span data-ttu-id="59804-160">Un attributo è costituito da un *attribute_name* e un elenco facoltativo di argomenti posizionali e denominati.</span><span class="sxs-lookup"><span data-stu-id="59804-160">An attribute consists of an *attribute_name* and an optional list of positional and named arguments.</span></span> <span data-ttu-id="59804-161">Gli argomenti posizionali, se presente, precedono gli argomenti denominati.</span><span class="sxs-lookup"><span data-stu-id="59804-161">The positional arguments (if any) precede the named arguments.</span></span> <span data-ttu-id="59804-162">Un argomento posizionale è costituito da un *attribute_argument_expression*; un argomento denominato è costituito da un nome, seguito da un segno di uguale, seguito da un *attribute_argument_expression*che, insieme , sono limitati dalle stesse regole di assegnazione semplice.</span><span class="sxs-lookup"><span data-stu-id="59804-162">A positional argument consists of an *attribute_argument_expression*; a named argument consists of a name, followed by an equal sign, followed by an *attribute_argument_expression*, which, together, are constrained by the same rules as simple assignment.</span></span> <span data-ttu-id="59804-163">L'ordine degli argomenti denominati non è significativo.</span><span class="sxs-lookup"><span data-stu-id="59804-163">The order of named arguments is not significant.</span></span>

<span data-ttu-id="59804-164">Il *attribute_name* identifica una classe attribute.</span><span class="sxs-lookup"><span data-stu-id="59804-164">The *attribute_name* identifies an attribute class.</span></span> <span data-ttu-id="59804-165">Se la forma di *attribute_name* viene *type_name* quindi questo nome deve fare riferimento a una classe attribute.</span><span class="sxs-lookup"><span data-stu-id="59804-165">If the form of *attribute_name* is *type_name* then this name must refer to an attribute class.</span></span> <span data-ttu-id="59804-166">In caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="59804-166">Otherwise, a compile-time error occurs.</span></span> <span data-ttu-id="59804-167">L'esempio</span><span class="sxs-lookup"><span data-stu-id="59804-167">The example</span></span>

```csharp
class Class1 {}

[Class1] class Class2 {}    // Error
```

<span data-ttu-id="59804-168">comporta un errore in fase di compilazione perché tenta di usare `Class1` come attributo quando viene chiamato `Class1` non è una classe attribute.</span><span class="sxs-lookup"><span data-stu-id="59804-168">results in a compile-time error because it attempts to use `Class1` as an attribute class when `Class1` is not an attribute class.</span></span>

<span data-ttu-id="59804-169">Determinati contesti consentono la specifica di un attributo più di una destinazione.</span><span class="sxs-lookup"><span data-stu-id="59804-169">Certain contexts permit the specification of an attribute on more than one target.</span></span> <span data-ttu-id="59804-170">Un programma può specificare in modo esplicito la destinazione, includendo un' *attribute_target_specifier*.</span><span class="sxs-lookup"><span data-stu-id="59804-170">A program can explicitly specify the target by including an *attribute_target_specifier*.</span></span> <span data-ttu-id="59804-171">Quando viene inserito un attributo a livello globale, un *global_attribute_target_specifier* è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="59804-171">When an attribute is placed at the global level, a *global_attribute_target_specifier* is required.</span></span> <span data-ttu-id="59804-172">In tutti gli altri percorsi, viene applicata un'impostazione predefinita adeguata, ma un' *attribute_target_specifier* consente dunque confermare o sostituire l'impostazione predefinita in determinati casi ambigui (o a confermano semplicemente il valore predefinito in casi non ambiguo).</span><span class="sxs-lookup"><span data-stu-id="59804-172">In all other locations, a reasonable default is applied, but an *attribute_target_specifier* can be used to affirm or override the default in certain ambiguous cases (or to just affirm the default in non-ambiguous cases).</span></span> <span data-ttu-id="59804-173">In questo modo, in genere *attribute_target_specifier*s può essere omesso tranne a livello globale.</span><span class="sxs-lookup"><span data-stu-id="59804-173">Thus, typically, *attribute_target_specifier*s can be omitted except at the global level.</span></span> <span data-ttu-id="59804-174">I contesti potenzialmente ambigui vengano risolto come segue:</span><span class="sxs-lookup"><span data-stu-id="59804-174">The potentially ambiguous contexts are resolved as follows:</span></span>

*  <span data-ttu-id="59804-175">Un attributo specificato in ambito globale può essere applicato all'assembly di destinazione o il modulo di destinazione.</span><span class="sxs-lookup"><span data-stu-id="59804-175">An attribute specified at global scope can apply either to the target assembly or the target module.</span></span> <span data-ttu-id="59804-176">Esiste alcun valore predefinito per questo contesto, pertanto, un' *attribute_target_specifier* è sempre obbligatorio in questo contesto.</span><span class="sxs-lookup"><span data-stu-id="59804-176">No default exists for this context, so an *attribute_target_specifier* is always required in this context.</span></span> <span data-ttu-id="59804-177">La presenza del `assembly` *attribute_target_specifier* indica che l'attributo si applica alla destinazione assembly; la presenza del `module` *attribute_target_specifier* indica che l'attributo si applica al modulo di destinazione.</span><span class="sxs-lookup"><span data-stu-id="59804-177">The presence of the `assembly` *attribute_target_specifier* indicates that the attribute applies to the target assembly; the presence of the `module` *attribute_target_specifier* indicates that the attribute applies to the target module.</span></span>
*  <span data-ttu-id="59804-178">Un attributo specificato in una dichiarazione di delegato è possibile applicare al delegato dichiarato o al relativo valore restituito.</span><span class="sxs-lookup"><span data-stu-id="59804-178">An attribute specified on a delegate declaration can apply either to the delegate being declared or to its return value.</span></span> <span data-ttu-id="59804-179">In assenza di un *attribute_target_specifier*, l'attributo viene applicato al delegato.</span><span class="sxs-lookup"><span data-stu-id="59804-179">In the absence of an *attribute_target_specifier*, the attribute applies to the delegate.</span></span> <span data-ttu-id="59804-180">La presenza del `type` *attribute_target_specifier* indica che l'attributo si applica al delegato; la presenza del `return` *attribute_target_specifier* indica che l'attributo viene applicato al valore restituito.</span><span class="sxs-lookup"><span data-stu-id="59804-180">The presence of the `type` *attribute_target_specifier* indicates that the attribute applies to the delegate; the presence of the `return` *attribute_target_specifier* indicates that the attribute applies to the return value.</span></span>
*  <span data-ttu-id="59804-181">Può applicare un attributo specificato in una dichiarazione di metodo al metodo da dichiarare o al relativo valore restituito.</span><span class="sxs-lookup"><span data-stu-id="59804-181">An attribute specified on a method declaration can apply either to the method being declared or to its return value.</span></span> <span data-ttu-id="59804-182">In assenza di un *attribute_target_specifier*, l'attributo viene applicato al metodo.</span><span class="sxs-lookup"><span data-stu-id="59804-182">In the absence of an *attribute_target_specifier*, the attribute applies to the method.</span></span> <span data-ttu-id="59804-183">La presenza del `method` *attribute_target_specifier* indica che l'attributo si applica al metodo; la presenza del `return` *attribute_target_specifier* indica che l'attributo viene applicato al valore restituito.</span><span class="sxs-lookup"><span data-stu-id="59804-183">The presence of the `method` *attribute_target_specifier* indicates that the attribute applies to the method; the presence of the `return` *attribute_target_specifier* indicates that the attribute applies to the return value.</span></span>
*  <span data-ttu-id="59804-184">Un attributo specificato in una dichiarazione dell'operatore è possibile applicare l'operatore viene dichiarato o al relativo valore restituito.</span><span class="sxs-lookup"><span data-stu-id="59804-184">An attribute specified on an operator declaration can apply either to the operator being declared or to its return value.</span></span> <span data-ttu-id="59804-185">In assenza di un *attribute_target_specifier*, l'attributo viene applicato all'operatore.</span><span class="sxs-lookup"><span data-stu-id="59804-185">In the absence of an *attribute_target_specifier*, the attribute applies to the operator.</span></span> <span data-ttu-id="59804-186">La presenza del `method` *attribute_target_specifier* indica che l'attributo viene applicato all'operatore; la presenza del `return` *attribute_target_specifier* indica che l'attributo viene applicato al valore restituito.</span><span class="sxs-lookup"><span data-stu-id="59804-186">The presence of the `method` *attribute_target_specifier* indicates that the attribute applies to the operator; the presence of the `return` *attribute_target_specifier* indicates that the attribute applies to the return value.</span></span>
*  <span data-ttu-id="59804-187">Un attributo specificato in una dichiarazione di evento che omette queste funzioni possa applicare all'evento dichiarato, al campo associato (se l'evento non è astratta) o per il componente associato e rimuovere i metodi.</span><span class="sxs-lookup"><span data-stu-id="59804-187">An attribute specified on an event declaration that omits event accessors can apply to the event being declared, to the associated field (if the event is not abstract), or to the associated add and remove methods.</span></span> <span data-ttu-id="59804-188">In assenza di un *attribute_target_specifier*, l'attributo viene applicato all'evento.</span><span class="sxs-lookup"><span data-stu-id="59804-188">In the absence of an *attribute_target_specifier*, the attribute applies to the event.</span></span> <span data-ttu-id="59804-189">La presenza del `event` *attribute_target_specifier* indica che l'attributo viene applicato all'evento; la presenza del `field` *attribute_target_specifier* indica che l'attributo viene applicato al campo. la presenza di e il `method` *attribute_target_specifier* indica che l'attributo si applica ai metodi.</span><span class="sxs-lookup"><span data-stu-id="59804-189">The presence of the `event` *attribute_target_specifier* indicates that the attribute applies to the event; the presence of the `field` *attribute_target_specifier* indicates that the attribute applies to the field; and the presence of the `method` *attribute_target_specifier* indicates that the attribute applies to the methods.</span></span>
*  <span data-ttu-id="59804-190">Un attributo specificato in una dichiarazione di funzione di accesso get per una dichiarazione di proprietà o l'indicizzatore è possibile applicare per il metodo associato o al relativo valore restituito.</span><span class="sxs-lookup"><span data-stu-id="59804-190">An attribute specified on a get accessor declaration for a property or indexer declaration can apply either to the associated method or to its return value.</span></span> <span data-ttu-id="59804-191">In assenza di un *attribute_target_specifier*, l'attributo viene applicato al metodo.</span><span class="sxs-lookup"><span data-stu-id="59804-191">In the absence of an *attribute_target_specifier*, the attribute applies to the method.</span></span> <span data-ttu-id="59804-192">La presenza del `method` *attribute_target_specifier* indica che l'attributo si applica al metodo; la presenza del `return` *attribute_target_specifier* indica che l'attributo viene applicato al valore restituito.</span><span class="sxs-lookup"><span data-stu-id="59804-192">The presence of the `method` *attribute_target_specifier* indicates that the attribute applies to the method; the presence of the `return` *attribute_target_specifier* indicates that the attribute applies to the return value.</span></span>
*  <span data-ttu-id="59804-193">Un attributo specificato in una funzione di accesso set per una dichiarazione di proprietà o un indicizzatore può essere applicato al metodo associato o al solo parametro implicito.</span><span class="sxs-lookup"><span data-stu-id="59804-193">An attribute specified on a set accessor for a property or indexer declaration can apply either to the associated method or to its lone implicit parameter.</span></span> <span data-ttu-id="59804-194">In assenza di un *attribute_target_specifier*, l'attributo viene applicato al metodo.</span><span class="sxs-lookup"><span data-stu-id="59804-194">In the absence of an *attribute_target_specifier*, the attribute applies to the method.</span></span> <span data-ttu-id="59804-195">La presenza del `method` *attribute_target_specifier* indica che l'attributo si applica al metodo; la presenza del `param` *attribute_target_specifier* indica che l'attributo viene applicato al parametro. la presenza del `return` *attribute_target_specifier* indica che l'attributo si applica al valore restituito.</span><span class="sxs-lookup"><span data-stu-id="59804-195">The presence of the `method` *attribute_target_specifier* indicates that the attribute applies to the method; the presence of the `param` *attribute_target_specifier* indicates that the attribute applies to the parameter; the presence of the `return` *attribute_target_specifier* indicates that the attribute applies to the return value.</span></span>
*  <span data-ttu-id="59804-196">Un attributo specificato in una dichiarazione di funzione di accesso add o remove per una dichiarazione di evento può essere applicato al metodo associato o al relativo singolo parametro.</span><span class="sxs-lookup"><span data-stu-id="59804-196">An attribute specified on an add or remove accessor declaration for an event declaration can apply either to the associated method or to its lone parameter.</span></span> <span data-ttu-id="59804-197">In assenza di un *attribute_target_specifier*, l'attributo viene applicato al metodo.</span><span class="sxs-lookup"><span data-stu-id="59804-197">In the absence of an *attribute_target_specifier*, the attribute applies to the method.</span></span> <span data-ttu-id="59804-198">La presenza del `method` *attribute_target_specifier* indica che l'attributo si applica al metodo; la presenza del `param` *attribute_target_specifier* indica che l'attributo viene applicato al parametro. la presenza del `return` *attribute_target_specifier* indica che l'attributo si applica al valore restituito.</span><span class="sxs-lookup"><span data-stu-id="59804-198">The presence of the `method` *attribute_target_specifier* indicates that the attribute applies to the method; the presence of the `param` *attribute_target_specifier* indicates that the attribute applies to the parameter; the presence of the `return` *attribute_target_specifier* indicates that the attribute applies to the return value.</span></span>

<span data-ttu-id="59804-199">In altri contesti, inclusione di un' *attribute_target_specifier* è consentito, ma non necessari.</span><span class="sxs-lookup"><span data-stu-id="59804-199">In other contexts, inclusion of an *attribute_target_specifier* is permitted but unnecessary.</span></span> <span data-ttu-id="59804-200">Ad esempio, una dichiarazione di classe può includere o omettere l'identificatore di `type`:</span><span class="sxs-lookup"><span data-stu-id="59804-200">For instance, a class declaration may either include or omit the specifier `type`:</span></span>

```csharp
[type: Author("Brian Kernighan")]
class Class1 {}

[Author("Dennis Ritchie")]
class Class2 {}
```

<span data-ttu-id="59804-201">È possibile specificare un valore non valido *attribute_target_specifier*.</span><span class="sxs-lookup"><span data-stu-id="59804-201">It is an error to specify an invalid *attribute_target_specifier*.</span></span> <span data-ttu-id="59804-202">Ad esempio, l'identificatore di `param` non può essere utilizzato in una dichiarazione di classe:</span><span class="sxs-lookup"><span data-stu-id="59804-202">For instance, the specifier `param` cannot be used on a class declaration:</span></span>

```csharp
[param: Author("Brian Kernighan")]        // Error
class Class1 {}
```

<span data-ttu-id="59804-203">Per convenzione, le classi di attributi sono denominate con un suffisso di `Attribute`.</span><span class="sxs-lookup"><span data-stu-id="59804-203">By convention, attribute classes are named with a suffix of `Attribute`.</span></span> <span data-ttu-id="59804-204">Un' *attribute_name* del form *type_name* potrebbe includere oppure omettere questo suffisso.</span><span class="sxs-lookup"><span data-stu-id="59804-204">An *attribute_name* of the form *type_name* may either include or omit this suffix.</span></span> <span data-ttu-id="59804-205">Se una classe attribute viene trovata con e senza il suffisso, è presente un'ambiguità e verrà generato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="59804-205">If an attribute class is found both with and without this suffix, an ambiguity is present, and a compile-time error results.</span></span> <span data-ttu-id="59804-206">Se il *attribute_name* sia stato digitato in modo che il più a destra *identificatore* è un identificatore verbatim ([identificatori](lexical-structure.md#identifiers)), quindi viene confrontato solo con un attributo senza un suffisso, consentendo in questo tipo un'ambiguità da risolvere.</span><span class="sxs-lookup"><span data-stu-id="59804-206">If the *attribute_name* is spelled such that its right-most *identifier* is a verbatim identifier ([Identifiers](lexical-structure.md#identifiers)), then only an attribute without a suffix is matched, thus enabling such an ambiguity to be resolved.</span></span> <span data-ttu-id="59804-207">L'esempio</span><span class="sxs-lookup"><span data-stu-id="59804-207">The example</span></span>

```csharp
using System;

[AttributeUsage(AttributeTargets.All)]
public class X: Attribute
{}

[AttributeUsage(AttributeTargets.All)]
public class XAttribute: Attribute
{}

[X]                     // Error: ambiguity
class Class1 {}

[XAttribute]            // Refers to XAttribute
class Class2 {}

[@X]                    // Refers to X
class Class3 {}

[@XAttribute]           // Refers to XAttribute
class Class4 {}
```

<span data-ttu-id="59804-208">Mostra due classi attribute denominate `X` e `XAttribute`.</span><span class="sxs-lookup"><span data-stu-id="59804-208">shows two attribute classes named `X` and `XAttribute`.</span></span> <span data-ttu-id="59804-209">L'attributo `[X]` , è ambiguo poiché potrebbe fare riferimento a una delle due `X` o `XAttribute`.</span><span class="sxs-lookup"><span data-stu-id="59804-209">The attribute `[X]` is ambiguous, since it could refer to either `X` or `XAttribute`.</span></span> <span data-ttu-id="59804-210">Utilizzando un identificatore verbatim consente all'obiettivo esatta di essere specificati in questi rari casi.</span><span class="sxs-lookup"><span data-stu-id="59804-210">Using a verbatim identifier allows the exact intent to be specified in such rare cases.</span></span> <span data-ttu-id="59804-211">L'attributo `[XAttribute]` non è ambiguo (anche se sarebbe se si è verificato una classe attribute denominata `XAttributeAttribute`!).</span><span class="sxs-lookup"><span data-stu-id="59804-211">The attribute `[XAttribute]` is not ambiguous (although it would be if there was an attribute class named `XAttributeAttribute`!).</span></span> <span data-ttu-id="59804-212">Se la dichiarazione della classe `X` viene rimosso, quindi entrambi gli attributi fanno riferimento alla classe attribute denominata `XAttribute`, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="59804-212">If the declaration for class `X` is removed, then both attributes refer to the attribute class named `XAttribute`, as follows:</span></span>

```csharp
using System;

[AttributeUsage(AttributeTargets.All)]
public class XAttribute: Attribute
{}

[X]                     // Refers to XAttribute
class Class1 {}

[XAttribute]            // Refers to XAttribute
class Class2 {}

[@X]                    // Error: no attribute named "X"
class Class3 {}
```

<span data-ttu-id="59804-213">È un errore in fase di compilazione da usare più volte una classe di attributo monouso per la stessa entità.</span><span class="sxs-lookup"><span data-stu-id="59804-213">It is a compile-time error to use a single-use attribute class more than once on the same entity.</span></span> <span data-ttu-id="59804-214">L'esempio</span><span class="sxs-lookup"><span data-stu-id="59804-214">The example</span></span>

```csharp
using System;

[AttributeUsage(AttributeTargets.Class)]
public class HelpStringAttribute: Attribute
{
    string value;

    public HelpStringAttribute(string value) {
        this.value = value;
    }

    public string Value {
        get {...}
    }
}

[HelpString("Description of Class1")]
[HelpString("Another description of Class1")]
public class Class1 {}
```

<span data-ttu-id="59804-215">comporta un errore in fase di compilazione perché tenta di usare `HelpString`, ovvero una classe di attributo monouso, più volte nella dichiarazione di `Class1`.</span><span class="sxs-lookup"><span data-stu-id="59804-215">results in a compile-time error because it attempts to use `HelpString`, which is a single-use attribute class, more than once on the declaration of `Class1`.</span></span>

<span data-ttu-id="59804-216">Un'espressione `E` è un *attribute_argument_expression* se vengono soddisfatte tutte le istruzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="59804-216">An expression `E` is an *attribute_argument_expression* if all of the following statements are true:</span></span>

*  <span data-ttu-id="59804-217">Il tipo della `E` è un tipo di parametro di attributo ([tipi di parametro di attributo](attributes.md#attribute-parameter-types)).</span><span class="sxs-lookup"><span data-stu-id="59804-217">The type of `E` is an attribute parameter type ([Attribute parameter types](attributes.md#attribute-parameter-types)).</span></span>
*  <span data-ttu-id="59804-218">In fase di compilazione, il valore di `E` può essere risolto in uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="59804-218">At compile-time, the value of `E` can be resolved to one of the following:</span></span>
   * <span data-ttu-id="59804-219">Un valore costante.</span><span class="sxs-lookup"><span data-stu-id="59804-219">A constant value.</span></span>
   * <span data-ttu-id="59804-220">Oggetto `System.Type`.</span><span class="sxs-lookup"><span data-stu-id="59804-220">A `System.Type` object.</span></span>
   * <span data-ttu-id="59804-221">Una matrice unidimensionale di *attribute_argument_expression*s.</span><span class="sxs-lookup"><span data-stu-id="59804-221">A one-dimensional array of *attribute_argument_expression*s.</span></span>

<span data-ttu-id="59804-222">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="59804-222">For example:</span></span>

```csharp
using System;

[AttributeUsage(AttributeTargets.Class)]
public class TestAttribute: Attribute
{
    public int P1 {
        get {...}
        set {...}
    }

    public Type P2 {
        get {...}
        set {...}
    }

    public object P3 {
        get {...}
        set {...}
    }
}

[Test(P1 = 1234, P3 = new int[] {1, 3, 5}, P2 = typeof(float))]
class MyClass {}
```

<span data-ttu-id="59804-223">Oggetto *typeof_expression* ([l'operatore typeof](expressions.md#the-typeof-operator)) utilizzata come un'espressione di argomento di attributo può fare riferimento a un tipo non generico, un tipo costruito chiuso o un tipo generico non associato, ma non a un tipo Open.</span><span class="sxs-lookup"><span data-stu-id="59804-223">A *typeof_expression* ([The typeof operator](expressions.md#the-typeof-operator)) used as an attribute argument expression can reference a non-generic type, a closed constructed type, or an unbound generic type, but it cannot reference an open type.</span></span> <span data-ttu-id="59804-224">Questo modo si garantisce che l'espressione possa essere risolto in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="59804-224">This is to ensure that the expression can be resolved at compile-time.</span></span>

```csharp
class A: Attribute
{
    public A(Type t) {...}
}

class G<T>
{
    [A(typeof(T))] T t;                  // Error, open type in attribute
}

class X
{
    [A(typeof(List<int>))] int x;        // Ok, closed constructed type
    [A(typeof(List<>))] int y;           // Ok, unbound generic type
}
```

## <a name="attribute-instances"></a><span data-ttu-id="59804-225">Istanze di attributo</span><span class="sxs-lookup"><span data-stu-id="59804-225">Attribute instances</span></span>

<span data-ttu-id="59804-226">Un' ***istanza dell'attributo*** è un'istanza che rappresenta un attributo in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="59804-226">An ***attribute instance*** is an instance that represents an attribute at run-time.</span></span> <span data-ttu-id="59804-227">Un attributo viene definito con una classe attribute, gli argomenti posizionali e gli argomenti denominato.</span><span class="sxs-lookup"><span data-stu-id="59804-227">An attribute is defined with an attribute class, positional arguments, and named arguments.</span></span> <span data-ttu-id="59804-228">Un'istanza di attributo è un'istanza della classe di attributo che viene inizializzata con gli argomenti posizionali e denominati.</span><span class="sxs-lookup"><span data-stu-id="59804-228">An attribute instance is an instance of the attribute class that is initialized with the positional and named arguments.</span></span>

<span data-ttu-id="59804-229">Il recupero di un'istanza dell'attributo comporta l'elaborazione in fase di compilazione sia in fase di esecuzione, come descritto nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="59804-229">Retrieval of an attribute instance involves both compile-time and run-time processing, as described in the following sections.</span></span>

### <a name="compilation-of-an-attribute"></a><span data-ttu-id="59804-230">Compilazione di un attributo</span><span class="sxs-lookup"><span data-stu-id="59804-230">Compilation of an attribute</span></span>

<span data-ttu-id="59804-231">La compilazione di un' *attributo* con una classe attribute `T`, *positional_argument_list* `P` e *named_argument_list* `N`, costituita dai passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="59804-231">The compilation of an *attribute* with attribute class `T`, *positional_argument_list* `P` and *named_argument_list* `N`, consists of the following steps:</span></span>

*  <span data-ttu-id="59804-232">Seguire i passaggi di elaborazione in fase di compilazione per la compilazione di un' *object_creation_expression* del form `new T(P)`.</span><span class="sxs-lookup"><span data-stu-id="59804-232">Follow the compile-time processing steps for compiling an *object_creation_expression* of the form `new T(P)`.</span></span> <span data-ttu-id="59804-233">Questi passaggi, generano un errore in fase di compilazione o determinare un costruttore di istanze `C` su `T` che può essere richiamato in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="59804-233">These steps either result in a compile-time error, or determine an instance constructor `C` on `T` that can be invoked at run-time.</span></span>
*  <span data-ttu-id="59804-234">Se `C` non dispone di accessibilità pubblica, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="59804-234">If `C` does not have public accessibility, then a compile-time error occurs.</span></span>
*  <span data-ttu-id="59804-235">Per ognuno *named_argument* `Arg` in `N`:</span><span class="sxs-lookup"><span data-stu-id="59804-235">For each *named_argument* `Arg` in `N`:</span></span>
   * <span data-ttu-id="59804-236">Let `Name` essere il *identificatore* del *named_argument* `Arg`.</span><span class="sxs-lookup"><span data-stu-id="59804-236">Let `Name` be the *identifier* of the *named_argument* `Arg`.</span></span>
   * <span data-ttu-id="59804-237">`Name` è necessario identificare un campo pubblico in lettura / scrittura non statico o una proprietà sul `T`.</span><span class="sxs-lookup"><span data-stu-id="59804-237">`Name` must identify a non-static read-write public field or property on `T`.</span></span> <span data-ttu-id="59804-238">Se `T` ha non tale campo o proprietà, quindi si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="59804-238">If `T` has no such field or property, then a compile-time error occurs.</span></span>
*  <span data-ttu-id="59804-239">Mantenere le informazioni seguenti per la creazione di istanze di runtime dell'attributo: la classe attribute `T`, il costruttore di istanze `C` sul `T`, il *positional_argument_list* `P` e il *named_argument_list* `N`.</span><span class="sxs-lookup"><span data-stu-id="59804-239">Keep the following information for run-time instantiation of the attribute: the attribute class `T`, the instance constructor `C` on `T`, the *positional_argument_list* `P` and the *named_argument_list* `N`.</span></span>

### <a name="run-time-retrieval-of-an-attribute-instance"></a><span data-ttu-id="59804-240">Recupero in fase di esecuzione di un'istanza di attributo</span><span class="sxs-lookup"><span data-stu-id="59804-240">Run-time retrieval of an attribute instance</span></span>

<span data-ttu-id="59804-241">Compilazione di un' *attributo* produce una classe attribute `T`, un costruttore di istanze `C` sul `T`, un *positional_argument_list* `P`e un *named_argument_list* `N`.</span><span class="sxs-lookup"><span data-stu-id="59804-241">Compilation of an *attribute* yields an attribute class `T`, an instance constructor `C` on `T`, a *positional_argument_list* `P`, and a *named_argument_list* `N`.</span></span> <span data-ttu-id="59804-242">Se viene fornito queste informazioni, un'istanza dell'attributo può essere recuperata in fase di esecuzione usando la procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="59804-242">Given this information, an attribute instance can be retrieved at run-time using the following steps:</span></span>

*  <span data-ttu-id="59804-243">Seguire i passaggi di elaborazione in fase di esecuzione per l'esecuzione di un' *object_creation_expression* del form `new T(P)`, usando il costruttore di istanza `C` determinata in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="59804-243">Follow the run-time processing steps for executing an *object_creation_expression* of the form `new T(P)`, using the instance constructor `C` as determined at compile-time.</span></span> <span data-ttu-id="59804-244">Questi passaggi, generano un'eccezione o produrre un'istanza `O` di `T`.</span><span class="sxs-lookup"><span data-stu-id="59804-244">These steps either result in an exception, or produce an instance `O` of `T`.</span></span>
*  <span data-ttu-id="59804-245">Per ognuno *named_argument* `Arg` in `N`, nell'ordine:</span><span class="sxs-lookup"><span data-stu-id="59804-245">For each *named_argument* `Arg` in `N`, in order:</span></span>
   * <span data-ttu-id="59804-246">Let `Name` essere il *identificatore* del *named_argument* `Arg`.</span><span class="sxs-lookup"><span data-stu-id="59804-246">Let `Name` be the *identifier* of the *named_argument* `Arg`.</span></span> <span data-ttu-id="59804-247">Se `Name` non identifica un campo di lettura / scrittura pubblica non statico o una proprietà su `O`, viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="59804-247">If `Name` does not identify a non-static public read-write field or property on `O`, then an exception is thrown.</span></span>
   * <span data-ttu-id="59804-248">Let `Value` essere il risultato della valutazione di *attribute_argument_expression* di `Arg`.</span><span class="sxs-lookup"><span data-stu-id="59804-248">Let `Value` be the result of evaluating the *attribute_argument_expression* of `Arg`.</span></span>
   * <span data-ttu-id="59804-249">Se `Name` identifica un campo sul `O`, quindi impostare questo campo su `Value`.</span><span class="sxs-lookup"><span data-stu-id="59804-249">If `Name` identifies a field on `O`, then set this field to `Value`.</span></span>
   * <span data-ttu-id="59804-250">In caso contrario, `Name` identifica una proprietà su `O`.</span><span class="sxs-lookup"><span data-stu-id="59804-250">Otherwise, `Name` identifies a property on `O`.</span></span> <span data-ttu-id="59804-251">Impostare questa proprietà su `Value`.</span><span class="sxs-lookup"><span data-stu-id="59804-251">Set this property to `Value`.</span></span>
   * <span data-ttu-id="59804-252">Il risultato viene `O`, un'istanza della classe attribute `T` che è stato inizializzato con il *positional_argument_list* `P` e la *named_argument_list* `N`.</span><span class="sxs-lookup"><span data-stu-id="59804-252">The result is `O`, an instance of the attribute class `T` that has been initialized with the *positional_argument_list* `P` and the *named_argument_list* `N`.</span></span>

## <a name="reserved-attributes"></a><span data-ttu-id="59804-253">Attributi riservati</span><span class="sxs-lookup"><span data-stu-id="59804-253">Reserved attributes</span></span>

<span data-ttu-id="59804-254">Un numero ridotto di attributi influisce sulla lingua in qualche modo.</span><span class="sxs-lookup"><span data-stu-id="59804-254">A small number of attributes affect the language in some way.</span></span> <span data-ttu-id="59804-255">Questi attributi includono:</span><span class="sxs-lookup"><span data-stu-id="59804-255">These attributes include:</span></span>

*  <span data-ttu-id="59804-256">`System.AttributeUsageAttribute` ([Attributo AttributeUsage the](attributes.md#the-attributeusage-attribute)), che viene utilizzato per descrivere i modi in cui è possibile usare una classe attribute.</span><span class="sxs-lookup"><span data-stu-id="59804-256">`System.AttributeUsageAttribute` ([The AttributeUsage attribute](attributes.md#the-attributeusage-attribute)), which is used to describe the ways in which an attribute class can be used.</span></span>
*  <span data-ttu-id="59804-257">`System.Diagnostics.ConditionalAttribute` ([Attributo Conditional](attributes.md#the-conditional-attribute)), che viene usato per definire metodi condizionali.</span><span class="sxs-lookup"><span data-stu-id="59804-257">`System.Diagnostics.ConditionalAttribute` ([The Conditional attribute](attributes.md#the-conditional-attribute)), which is used to define conditional methods.</span></span>
*  <span data-ttu-id="59804-258">`System.ObsoleteAttribute` ([Attributo the Obsolete](attributes.md#the-obsolete-attribute)), che viene usato per contrassegnare un membro come obsoleto.</span><span class="sxs-lookup"><span data-stu-id="59804-258">`System.ObsoleteAttribute` ([The Obsolete attribute](attributes.md#the-obsolete-attribute)), which is used to mark a member as obsolete.</span></span>
*  <span data-ttu-id="59804-259">`System.Runtime.CompilerServices.CallerLineNumberAttribute`, `System.Runtime.CompilerServices.CallerFilePathAttribute` e `System.Runtime.CompilerServices.CallerMemberNameAttribute` ([attributi informativi sul chiamante](attributes.md#caller-info-attributes)), che sono usati per fornire informazioni sul contesto del chiama ai parametri facoltativi.</span><span class="sxs-lookup"><span data-stu-id="59804-259">`System.Runtime.CompilerServices.CallerLineNumberAttribute`, `System.Runtime.CompilerServices.CallerFilePathAttribute` and `System.Runtime.CompilerServices.CallerMemberNameAttribute` ([Caller info attributes](attributes.md#caller-info-attributes)), which are used to supply information about the calling context to optional parameters.</span></span>

### <a name="the-attributeusage-attribute"></a><span data-ttu-id="59804-260">L'attributo AttributeUsage</span><span class="sxs-lookup"><span data-stu-id="59804-260">The AttributeUsage attribute</span></span>

<span data-ttu-id="59804-261">L'attributo `AttributeUsage` viene usato per descrivere il modo in cui è possibile usare la classe attribute.</span><span class="sxs-lookup"><span data-stu-id="59804-261">The attribute `AttributeUsage` is used to describe the manner in which the attribute class can be used.</span></span>

<span data-ttu-id="59804-262">Una classe decorata con il `AttributeUsage` attributo deve derivare da `System.Attribute`, direttamente o indirettamente.</span><span class="sxs-lookup"><span data-stu-id="59804-262">A class that is decorated with the `AttributeUsage` attribute must derive from `System.Attribute`, either directly or indirectly.</span></span> <span data-ttu-id="59804-263">In caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="59804-263">Otherwise, a compile-time error occurs.</span></span>

```csharp
namespace System
{
    [AttributeUsage(AttributeTargets.Class)]
    public class AttributeUsageAttribute: Attribute
    {
        public AttributeUsageAttribute(AttributeTargets validOn) {...}
        public virtual bool AllowMultiple { get {...} set {...} }
        public virtual bool Inherited { get {...} set {...} }
        public virtual AttributeTargets ValidOn { get {...} }
    }

    public enum AttributeTargets
    {
        Assembly     = 0x0001,
        Module       = 0x0002,
        Class        = 0x0004,
        Struct       = 0x0008,
        Enum         = 0x0010,
        Constructor  = 0x0020,
        Method       = 0x0040,
        Property     = 0x0080,
        Field        = 0x0100,
        Event        = 0x0200,
        Interface    = 0x0400,
        Parameter    = 0x0800,
        Delegate     = 0x1000,
        ReturnValue  = 0x2000,

        All = Assembly | Module | Class | Struct | Enum | Constructor | 
            Method | Property | Field | Event | Interface | Parameter | 
            Delegate | ReturnValue
    }
}
```

### <a name="the-conditional-attribute"></a><span data-ttu-id="59804-264">L'attributo Conditional</span><span class="sxs-lookup"><span data-stu-id="59804-264">The Conditional attribute</span></span>

<span data-ttu-id="59804-265">L'attributo `Conditional` consente la definizione delle ***metodi condizionali*** e ***classi di attributi condizionali***.</span><span class="sxs-lookup"><span data-stu-id="59804-265">The attribute `Conditional` enables the definition of ***conditional methods*** and ***conditional attribute classes***.</span></span>

```csharp
namespace System.Diagnostics
{
    [AttributeUsage(AttributeTargets.Method | AttributeTargets.Class, AllowMultiple = true)]
    public class ConditionalAttribute: Attribute
    {
        public ConditionalAttribute(string conditionString) {...}
        public string ConditionString { get {...} }
    }
}
```

#### <a name="conditional-methods"></a><span data-ttu-id="59804-266">Metodi condizionali</span><span class="sxs-lookup"><span data-stu-id="59804-266">Conditional methods</span></span>

<span data-ttu-id="59804-267">Un metodo decorata con il `Conditional` attributo è un metodo condizionale.</span><span class="sxs-lookup"><span data-stu-id="59804-267">A method decorated with the `Conditional` attribute is a conditional method.</span></span> <span data-ttu-id="59804-268">Il `Conditional` attributo indica una condizione dal test di un simbolo di compilazione condizionale.</span><span class="sxs-lookup"><span data-stu-id="59804-268">The `Conditional` attribute indicates a condition by testing a conditional compilation symbol.</span></span> <span data-ttu-id="59804-269">Chiamate a un metodo condizionale sono inclusi o omessi a seconda del fatto che questo simbolo viene definito al momento della chiamata.</span><span class="sxs-lookup"><span data-stu-id="59804-269">Calls to a conditional method are either included or omitted depending on whether this symbol is defined at the point of the call.</span></span> <span data-ttu-id="59804-270">Se il simbolo è definito, la chiamata viene inclusa; in caso contrario, viene omessa (inclusi evaluation del ricevitore e i parametri della chiamata).</span><span class="sxs-lookup"><span data-stu-id="59804-270">If the symbol is defined, the call is included; otherwise, the call (including evaluation of the receiver and parameters of the call) is omitted.</span></span>

<span data-ttu-id="59804-271">Un metodo condizionale è soggetto alle restrizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="59804-271">A conditional method is subject to the following restrictions:</span></span>

*  <span data-ttu-id="59804-272">Il metodo condizionale deve essere un metodo in un *class_declaration* oppure *struct_declaration*.</span><span class="sxs-lookup"><span data-stu-id="59804-272">The conditional method must be a method in a *class_declaration* or *struct_declaration*.</span></span> <span data-ttu-id="59804-273">Si verifica un errore in fase di compilazione se il `Conditional` attributo è specificato in un metodo in una dichiarazione di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="59804-273">A compile-time error occurs if the `Conditional` attribute is specified on a method in an interface declaration.</span></span>
*  <span data-ttu-id="59804-274">Il metodo condizionale deve avere un tipo restituito `void`.</span><span class="sxs-lookup"><span data-stu-id="59804-274">The conditional method must have a return type of `void`.</span></span>
*  <span data-ttu-id="59804-275">Il metodo condizionale non deve essere contrassegnato con il `override` modificatore.</span><span class="sxs-lookup"><span data-stu-id="59804-275">The conditional method must not be marked with the `override` modifier.</span></span> <span data-ttu-id="59804-276">Un metodo condizionale può essere contrassegnato con il `virtual` modificatore, tuttavia.</span><span class="sxs-lookup"><span data-stu-id="59804-276">A conditional method may be marked with the `virtual` modifier, however.</span></span> <span data-ttu-id="59804-277">Esegue l'override di questo metodo sono implicitamente condizionale e non deve essere contrassegnata in modo esplicito con una `Conditional` attributo.</span><span class="sxs-lookup"><span data-stu-id="59804-277">Overrides of such a method are implicitly conditional, and must not be explicitly marked with a `Conditional` attribute.</span></span>
*  <span data-ttu-id="59804-278">Il metodo condizionale non deve essere un'implementazione di un metodo di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="59804-278">The conditional method must not be an implementation of an interface method.</span></span> <span data-ttu-id="59804-279">In caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="59804-279">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="59804-280">Inoltre, in cui si verifica un errore in fase di compilazione se un metodo condizionale viene usato in un *delegate_creation_expression*.</span><span class="sxs-lookup"><span data-stu-id="59804-280">In addition, a compile-time error occurs if a conditional method is used in a *delegate_creation_expression*.</span></span> <span data-ttu-id="59804-281">L'esempio</span><span class="sxs-lookup"><span data-stu-id="59804-281">The example</span></span>

```csharp
#define DEBUG

using System;
using System.Diagnostics;

class Class1 
{
    [Conditional("DEBUG")]
    public static void M() {
        Console.WriteLine("Executed Class1.M");
    }
}

class Class2
{
    public static void Test() {
        Class1.M();
    }
}
```

<span data-ttu-id="59804-282">dichiara `Class1.M` come un metodo condizionale.</span><span class="sxs-lookup"><span data-stu-id="59804-282">declares `Class1.M` as a conditional method.</span></span> <span data-ttu-id="59804-283">`Class2`del `Test` chiama questo metodo.</span><span class="sxs-lookup"><span data-stu-id="59804-283">`Class2`'s `Test` method calls this method.</span></span> <span data-ttu-id="59804-284">Dopo il simbolo di compilazione condizionale `DEBUG` è definito, se `Class2.Test` viene chiamato, verrà chiamato `M`.</span><span class="sxs-lookup"><span data-stu-id="59804-284">Since the conditional compilation symbol `DEBUG` is defined, if `Class2.Test` is called, it will call `M`.</span></span> <span data-ttu-id="59804-285">Se il simbolo `DEBUG` non fosse stato definito, quindi `Class2.Test` non chiamasse `Class1.M`.</span><span class="sxs-lookup"><span data-stu-id="59804-285">If the symbol `DEBUG` had not been defined, then `Class2.Test` would not call `Class1.M`.</span></span>

<span data-ttu-id="59804-286">È importante notare che l'inclusione o esclusione di una chiamata a un metodo condizionale è controllata dai simboli di compilazione condizionale al momento della chiamata.</span><span class="sxs-lookup"><span data-stu-id="59804-286">It is important to note that the inclusion or exclusion of a call to a conditional method is controlled by the conditional compilation symbols at the point of the call.</span></span> <span data-ttu-id="59804-287">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="59804-287">In the example</span></span>

<span data-ttu-id="59804-288">File `class1.cs`:</span><span class="sxs-lookup"><span data-stu-id="59804-288">File `class1.cs`:</span></span>

```csharp
using System.Diagnostics;

class Class1 
{
    [Conditional("DEBUG")]
    public static void F() {
        Console.WriteLine("Executed Class1.F");
    }
}
```

<span data-ttu-id="59804-289">File `class2.cs`:</span><span class="sxs-lookup"><span data-stu-id="59804-289">File `class2.cs`:</span></span>

```csharp
#define DEBUG

class Class2
{
    public static void G() {
        Class1.F();                // F is called
    }
}
```

<span data-ttu-id="59804-290">File `class3.cs`:</span><span class="sxs-lookup"><span data-stu-id="59804-290">File `class3.cs`:</span></span>

```csharp
#undef DEBUG

class Class3
{
    public static void H() {
        Class1.F();                // F is not called
    }
}
```

<span data-ttu-id="59804-291">le classi `Class2` e `Class3` ognuno dei quali contiene chiamate al metodo condizionale `Class1.F`, ovvero condizionale basata o meno `DEBUG` è definito.</span><span class="sxs-lookup"><span data-stu-id="59804-291">the classes `Class2` and `Class3` each contain calls to the conditional method `Class1.F`, which is conditional based on whether or not `DEBUG` is defined.</span></span> <span data-ttu-id="59804-292">Poiché questo simbolo è definito nel contesto di `Class2` ma non `Class3`, la chiamata a `F` nelle `Class2` è incluso, mentre la chiamata a `F` in `Class3` viene omesso.</span><span class="sxs-lookup"><span data-stu-id="59804-292">Since this symbol is defined in the context of `Class2` but not `Class3`, the call to `F` in `Class2` is included, while the call to `F` in `Class3` is omitted.</span></span>

<span data-ttu-id="59804-293">L'uso di metodi condizionali in una catena di ereditarietà può generare confusione.</span><span class="sxs-lookup"><span data-stu-id="59804-293">The use of conditional methods in an inheritance chain can be confusing.</span></span> <span data-ttu-id="59804-294">Le chiamate effettuate a un metodo condizionale attraverso `base`, nel formato `base.M`, sono soggetti alle regole di chiamata di metodo condizionale normale.</span><span class="sxs-lookup"><span data-stu-id="59804-294">Calls made to a conditional method through `base`, of the form `base.M`, are subject to the normal conditional method call rules.</span></span> <span data-ttu-id="59804-295">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="59804-295">In the example</span></span>

<span data-ttu-id="59804-296">File `class1.cs`:</span><span class="sxs-lookup"><span data-stu-id="59804-296">File `class1.cs`:</span></span>

```csharp
using System;
using System.Diagnostics;

class Class1 
{
    [Conditional("DEBUG")]
    public virtual void M() {
        Console.WriteLine("Class1.M executed");
    }
}
```

<span data-ttu-id="59804-297">File `class2.cs`:</span><span class="sxs-lookup"><span data-stu-id="59804-297">File `class2.cs`:</span></span>

```csharp
using System;

class Class2: Class1
{
    public override void M() {
        Console.WriteLine("Class2.M executed");
        base.M();                        // base.M is not called!
    }
}
```

<span data-ttu-id="59804-298">File `class3.cs`:</span><span class="sxs-lookup"><span data-stu-id="59804-298">File `class3.cs`:</span></span>

```csharp
#define DEBUG

using System;

class Class3
{
    public static void Test() {
        Class2 c = new Class2();
        c.M();                            // M is called
    }
}
```

<span data-ttu-id="59804-299">`Class2` include una chiamata al `M` definite nella relativa classe base.</span><span class="sxs-lookup"><span data-stu-id="59804-299">`Class2` includes a call to the `M` defined in its base class.</span></span> <span data-ttu-id="59804-300">Questa chiamata viene omesso in quanto il metodo di base è condizionale in base alla presenza del simbolo `DEBUG`, che non è definito.</span><span class="sxs-lookup"><span data-stu-id="59804-300">This call is omitted because the base method is conditional based on the presence of the symbol `DEBUG`, which is undefined.</span></span> <span data-ttu-id="59804-301">Di conseguenza, il metodo scrive nella console "`Class2.M executed`" solo.</span><span class="sxs-lookup"><span data-stu-id="59804-301">Thus, the method writes to the console "`Class2.M executed`" only.</span></span> <span data-ttu-id="59804-302">L'utilizzo di razionale *pp_declaration*s può evitare tali problemi.</span><span class="sxs-lookup"><span data-stu-id="59804-302">Judicious use of *pp_declaration*s can eliminate such problems.</span></span>

#### <a name="conditional-attribute-classes"></a><span data-ttu-id="59804-303">Classi di attributo Conditional</span><span class="sxs-lookup"><span data-stu-id="59804-303">Conditional attribute classes</span></span>

<span data-ttu-id="59804-304">Una classe attribute ([classi Attribute](attributes.md#attribute-classes)) decorato con uno o più `Conditional` attributi è un ***classe attribute condizionale***.</span><span class="sxs-lookup"><span data-stu-id="59804-304">An attribute class ([Attribute classes](attributes.md#attribute-classes)) decorated with one or more `Conditional` attributes is a ***conditional attribute class***.</span></span> <span data-ttu-id="59804-305">Una classe attribute condizionale è pertanto associata con i simboli di compilazione condizionale dichiarati nel relativo `Conditional` attributi.</span><span class="sxs-lookup"><span data-stu-id="59804-305">A conditional attribute class is thus associated with the conditional compilation symbols declared in its `Conditional` attributes.</span></span> <span data-ttu-id="59804-306">In questo esempio:</span><span class="sxs-lookup"><span data-stu-id="59804-306">This example:</span></span>

```csharp
using System;
using System.Diagnostics;
[Conditional("ALPHA")]
[Conditional("BETA")]
public class TestAttribute : Attribute {}
```

<span data-ttu-id="59804-307">dichiara `TestAttribute` come classe di attributi condizionali associata con i simboli le compilazioni condizionali `ALPHA` e `BETA`.</span><span class="sxs-lookup"><span data-stu-id="59804-307">declares `TestAttribute` as a conditional attribute class associated with the conditional compilations symbols `ALPHA` and `BETA`.</span></span>

<span data-ttu-id="59804-308">Specifiche dell'attributo ([specifica degli attributi](attributes.md#attribute-specification)) di un attributo conditional sono inclusi se uno o più dei relativi simboli di compilazione condizionale associato è stato definito, in caso contrario, l'attributo verrà omessa.</span><span class="sxs-lookup"><span data-stu-id="59804-308">Attribute specifications ([Attribute specification](attributes.md#attribute-specification)) of a conditional attribute are included if one or more of its associated conditional compilation symbols is defined at the point of specification, otherwise the attribute specification is omitted.</span></span>

<span data-ttu-id="59804-309">È importante notare che l'inclusione o esclusione di una specifica di attributo di una classe di attributi condizionali è controllata dai simboli al momento della specifica di compilazione condizionale.</span><span class="sxs-lookup"><span data-stu-id="59804-309">It is important to note that the inclusion or exclusion of an attribute specification of a conditional attribute class is controlled by the conditional compilation symbols at the point of the specification.</span></span> <span data-ttu-id="59804-310">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="59804-310">In the example</span></span>

<span data-ttu-id="59804-311">File `test.cs`:</span><span class="sxs-lookup"><span data-stu-id="59804-311">File `test.cs`:</span></span>

```csharp
using System;
using System.Diagnostics;

[Conditional("DEBUG")]

public class TestAttribute : Attribute {}
```

<span data-ttu-id="59804-312">File `class1.cs`:</span><span class="sxs-lookup"><span data-stu-id="59804-312">File `class1.cs`:</span></span>

```csharp
#define DEBUG

[Test]                // TestAttribute is specified

class Class1 {}
```

<span data-ttu-id="59804-313">File `class2.cs`:</span><span class="sxs-lookup"><span data-stu-id="59804-313">File `class2.cs`:</span></span>

```csharp
#undef DEBUG

[Test]                 // TestAttribute is not specified

class Class2 {}
```

<span data-ttu-id="59804-314">le classi `Class1` e `Class2` sono entrambe decorate con l'attributo `Test`, ovvero condizionale basata o meno `DEBUG` è definito.</span><span class="sxs-lookup"><span data-stu-id="59804-314">the classes `Class1` and `Class2` are each decorated with attribute `Test`, which is conditional based on whether or not `DEBUG` is defined.</span></span> <span data-ttu-id="59804-315">Poiché questo simbolo è definito nel contesto di `Class1` ma non `Class2`, la specifica del `Test` attributo `Class1` è incluso, durante la specifica del `Test` dell'attributo su `Class2` viene omesso.</span><span class="sxs-lookup"><span data-stu-id="59804-315">Since this symbol is defined in the context of `Class1` but not `Class2`, the specification of the `Test` attribute on `Class1` is included, while the specification of the `Test` attribute on `Class2` is omitted.</span></span>

### <a name="the-obsolete-attribute"></a><span data-ttu-id="59804-316">Attributo Obsolete</span><span class="sxs-lookup"><span data-stu-id="59804-316">The Obsolete attribute</span></span>

<span data-ttu-id="59804-317">L'attributo `Obsolete` viene usato per contrassegnare tipi e membri dei tipi che non devono più essere utilizzati.</span><span class="sxs-lookup"><span data-stu-id="59804-317">The attribute `Obsolete` is used to mark types and members of types that should no longer be used.</span></span>

```csharp
namespace System
{
    [AttributeUsage(
        AttributeTargets.Class | 
        AttributeTargets.Struct |
        AttributeTargets.Enum | 
        AttributeTargets.Interface | 
        AttributeTargets.Delegate |
        AttributeTargets.Method | 
        AttributeTargets.Constructor |
        AttributeTargets.Property | 
        AttributeTargets.Field |
        AttributeTargets.Event,
        Inherited = false)
    ]
    public class ObsoleteAttribute: Attribute
    {
        public ObsoleteAttribute() {...}
        public ObsoleteAttribute(string message) {...}
        public ObsoleteAttribute(string message, bool error) {...}
        public string Message { get {...} }
        public bool IsError { get {...} }
    }
}
```

<span data-ttu-id="59804-318">Se un programma Usa un tipo o membro che è dotato di `Obsolete` attributo, il compilatore genera un avviso o un errore.</span><span class="sxs-lookup"><span data-stu-id="59804-318">If a program uses a type or member that is decorated with the `Obsolete` attribute, the compiler issues a warning or an error.</span></span> <span data-ttu-id="59804-319">In particolare, il compilatore genera un avviso se viene fornito alcun parametro di errore o se il parametro di errore viene fornito e ha il valore `false`.</span><span class="sxs-lookup"><span data-stu-id="59804-319">Specifically, the compiler issues a warning if no error parameter is provided, or if the error parameter is provided and has the value `false`.</span></span> <span data-ttu-id="59804-320">Il compilatore genera un errore se il parametro di errore specificato e ha il valore `true`.</span><span class="sxs-lookup"><span data-stu-id="59804-320">The compiler issues an error if the error parameter is specified and has the value `true`.</span></span>

<span data-ttu-id="59804-321">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="59804-321">In the example</span></span>

```csharp
[Obsolete("This class is obsolete; use class B instead")]
class A
{
    public void F() {}
}

class B
{
    public void F() {}
}

class Test
{
    static void Main() {
        A a = new A();         // Warning
        a.F();
    }
}
```

<span data-ttu-id="59804-322">la classe `A` decorata con il `Obsolete` attributo.</span><span class="sxs-lookup"><span data-stu-id="59804-322">the class `A` is decorated with the `Obsolete` attribute.</span></span> <span data-ttu-id="59804-323">Ogni uso di `A` in `Main` genera un avviso indicante che il messaggio specificato, "questa classe è obsoleta; Utilizzare la classe B."</span><span class="sxs-lookup"><span data-stu-id="59804-323">Each use of `A` in `Main` results in a warning that includes the specified message, "This class is obsolete; use class B instead."</span></span>

### <a name="caller-info-attributes"></a><span data-ttu-id="59804-324">Attributi informativi sul chiamante</span><span class="sxs-lookup"><span data-stu-id="59804-324">Caller info attributes</span></span>

<span data-ttu-id="59804-325">Per scopi, ad esempio la registrazione e creazione di report, è talvolta utile per un membro di funzione ottenere determinate informazioni in fase di compilazione relativa al codice chiama.</span><span class="sxs-lookup"><span data-stu-id="59804-325">For purposes such as logging and reporting, it is sometimes useful for a function member to obtain certain compile-time information about the calling code.</span></span> <span data-ttu-id="59804-326">Di attributi informativi sul chiamante consentono di passare tali informazioni in modo trasparente.</span><span class="sxs-lookup"><span data-stu-id="59804-326">The caller info attributes provide a way to pass such information transparently.</span></span>

<span data-ttu-id="59804-327">Quando un parametro facoltativo è annotato con uno degli attributi di informazioni sul chiamante, omettendo l'argomento corrispondente in una chiamata non necessariamente causano il valore del parametro predefinito deve essere sostituito.</span><span class="sxs-lookup"><span data-stu-id="59804-327">When an optional parameter is annotated with one of the caller info attributes, omitting the corresponding argument in a call does not necessarily cause the default parameter value to be substituted.</span></span> <span data-ttu-id="59804-328">In alternativa, se le informazioni specificate sul contesto del chiama sono disponibile, verranno passate tali informazioni come il valore dell'argomento.</span><span class="sxs-lookup"><span data-stu-id="59804-328">Instead, if the specified information about the calling context is available, that information will be passed as the argument value.</span></span>

<span data-ttu-id="59804-329">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="59804-329">For example:</span></span>

```csharp
using System.Runtime.CompilerServices

...

public void Log(
    [CallerLineNumber] int line = -1,
    [CallerFilePath]   string path = null,
    [CallerMemberName] string name = null
)
{
    Console.WriteLine((line < 0) ? "No line" : "Line "+ line);
    Console.WriteLine((path == null) ? "No file path" : path);
    Console.WriteLine((name == null) ? "No member name" : name);
}
```

<span data-ttu-id="59804-330">Una chiamata a `Log()` senza argomenti, stamperebbe il percorso di file e numero di riga della chiamata, oltre al nome del membro all'interno del quale si è verificata la chiamata.</span><span class="sxs-lookup"><span data-stu-id="59804-330">A call to `Log()` with no arguments would print the line number and file path of the call, as well as the name of the member within which the call occurred.</span></span>

<span data-ttu-id="59804-331">Attributi informativi sul chiamante possono verificarsi in qualsiasi origine, i parametri facoltativi incluse nelle dichiarazioni di delegato.</span><span class="sxs-lookup"><span data-stu-id="59804-331">Caller info attributes can occur on optional parameters anywhere, including in delegate declarations.</span></span> <span data-ttu-id="59804-332">Tuttavia, di attributi informativi sul chiamante specifico presentano restrizioni sui tipi di parametri che possono attributo, in modo che sarà sempre presente una conversione implicita da un valore sostitutivo per il tipo di parametro.</span><span class="sxs-lookup"><span data-stu-id="59804-332">However, the specific caller info attributes have restrictions on the types of the parameters they can attribute, so that there will always be an implicit conversion from a substituted value to the parameter type.</span></span>

<span data-ttu-id="59804-333">È un errore per avere lo stessa attributi informativi sul chiamante in un parametro di definizione e implementazione della parte di una dichiarazione di metodo parziale.</span><span class="sxs-lookup"><span data-stu-id="59804-333">It is an error to have the same caller info attribute on a parameter of both the defining and implementing part of a partial method declaration.</span></span> <span data-ttu-id="59804-334">Vengono applicati solo attributi informativi sul chiamante nella parte definizione, mentre attributi informativi sul chiamante che si verificano solo in parte l'implementazione vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="59804-334">Only caller info attributes in the defining part are applied, whereas caller info attributes occurring only in the implementing part are ignored.</span></span>

<span data-ttu-id="59804-335">Informazioni sul chiamante non interessa la risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="59804-335">Caller information does not affect overload resolution.</span></span> <span data-ttu-id="59804-336">Come i parametri facoltativi con attributi ancora vengono omessi dal codice sorgente del chiamante, la risoluzione dell'overload ignora tali parametri nello stesso modo ignora altri omessi parametri facoltativi ([risoluzione dell'Overload](expressions.md#overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="59804-336">As the attributed optional parameters are still omitted from the source code of the caller, overload resolution ignores those parameters in the same way it ignores other omitted optional parameters ([Overload resolution](expressions.md#overload-resolution)).</span></span>

<span data-ttu-id="59804-337">Informazioni sul chiamante viene sostituiti solo quando una funzione viene richiamata in modo esplicito nel codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="59804-337">Caller information is only substituted when a function is explicitly invoked in source code.</span></span> <span data-ttu-id="59804-338">Le chiamate implicite, ad esempio le chiamate di costruttore implicito padre non è un percorso di origine e non consente di sostituire le informazioni sul chiamante.</span><span class="sxs-lookup"><span data-stu-id="59804-338">Implicit invocations such as implicit parent constructor calls do not have a source location and will not substitute caller information.</span></span> <span data-ttu-id="59804-339">Inoltre, le chiamate che sono associate in modo dinamico non sostituirà con informazioni sul chiamante.</span><span class="sxs-lookup"><span data-stu-id="59804-339">Also, calls that are dynamically bound will not substitute caller information.</span></span> <span data-ttu-id="59804-340">Quando un informazioni sul chiamante con attributi di parametro viene omesso in questi casi, viene invece utilizzato il valore predefinito specificato del parametro.</span><span class="sxs-lookup"><span data-stu-id="59804-340">When a caller info attributed parameter is omitted in such cases, the specified default value of the parameter is used instead.</span></span>

<span data-ttu-id="59804-341">Un'eccezione riguarda le espressioni di query.</span><span class="sxs-lookup"><span data-stu-id="59804-341">One exception is query-expressions.</span></span> <span data-ttu-id="59804-342">Questi indirizzi sono considerati espansioni sintattiche e se le chiamate impegnate nell'espansione per omettere i parametri facoltativi con attributi informativi sul chiamante, informazioni sul chiamante verrà sostituiti.</span><span class="sxs-lookup"><span data-stu-id="59804-342">These are considered syntactic expansions, and if the calls they expand to omit optional parameters with caller info attributes, caller information will be substituted.</span></span> <span data-ttu-id="59804-343">Il percorso utilizzato è il percorso della clausola che la chiamata è stata generata dalla query.</span><span class="sxs-lookup"><span data-stu-id="59804-343">The location used is the location of the query clause which the call was generated from.</span></span>

<span data-ttu-id="59804-344">Se in un determinato parametro è specificato più di un attributo di informazioni sul chiamante, sono preferibili nell'ordine seguente: `CallerLineNumber`, `CallerFilePath`, `CallerMemberName`.</span><span class="sxs-lookup"><span data-stu-id="59804-344">If more than one caller info attribute is specified on a given parameter, they are preferred in the following order: `CallerLineNumber`, `CallerFilePath`, `CallerMemberName`.</span></span>

#### <a name="the-callerlinenumber-attribute"></a><span data-ttu-id="59804-345">L'attributo CallerLineNumber</span><span class="sxs-lookup"><span data-stu-id="59804-345">The CallerLineNumber attribute</span></span>

<span data-ttu-id="59804-346">Il `System.Runtime.CompilerServices.CallerLineNumberAttribute` JE povolen parametri facoltativi quando viene eseguita una conversione implicita standard ([conversioni implicite](conversions.md#standard-implicit-conversions)) dal valore di costante `int.MaxValue` per il tipo del parametro.</span><span class="sxs-lookup"><span data-stu-id="59804-346">The `System.Runtime.CompilerServices.CallerLineNumberAttribute` is allowed on optional parameters when there is a standard implicit conversion ([Standard implicit conversions](conversions.md#standard-implicit-conversions)) from the constant value `int.MaxValue` to the parameter's type.</span></span> <span data-ttu-id="59804-347">Ciò garantisce che qualsiasi numero di riga non negativo fino a tale valore può essere passato senza errori.</span><span class="sxs-lookup"><span data-stu-id="59804-347">This ensures that any non-negative line number up to that value can be passed without error.</span></span>

<span data-ttu-id="59804-348">Se una chiamata alla funzione da una posizione nel codice sorgente omette un parametro facoltativo e il `CallerLineNumberAttribute`, un valore letterale numerico che rappresenta il numero di riga dell'indirizzo che viene usato come argomento alla chiamata anziché il valore del parametro predefinito.</span><span class="sxs-lookup"><span data-stu-id="59804-348">If a function invocation from a location in source code omits an optional parameter with the `CallerLineNumberAttribute`, then a numeric literal representing that location's line number is used as an argument to the invocation instead of the default parameter value.</span></span>

<span data-ttu-id="59804-349">Se la chiamata si estende su più righe, la riga scelta è dipendente dall'implementazione.</span><span class="sxs-lookup"><span data-stu-id="59804-349">If the invocation spans multiple lines, the line chosen is implementation-dependent.</span></span>

<span data-ttu-id="59804-350">Si noti che il numero di riga può essere influenzato `#line` direttive ([direttive di riga](lexical-structure.md#line-directives)).</span><span class="sxs-lookup"><span data-stu-id="59804-350">Note that the line number may be affected by `#line` directives ([Line directives](lexical-structure.md#line-directives)).</span></span>

#### <a name="the-callerfilepath-attribute"></a><span data-ttu-id="59804-351">L'attributo CallerFilePath</span><span class="sxs-lookup"><span data-stu-id="59804-351">The CallerFilePath attribute</span></span>

<span data-ttu-id="59804-352">Il `System.Runtime.CompilerServices.CallerFilePathAttribute` JE povolen parametri facoltativi quando viene eseguita una conversione implicita standard ([conversioni implicite](conversions.md#standard-implicit-conversions)) da `string` per il tipo del parametro.</span><span class="sxs-lookup"><span data-stu-id="59804-352">The `System.Runtime.CompilerServices.CallerFilePathAttribute` is allowed on optional parameters when there is a standard implicit conversion ([Standard implicit conversions](conversions.md#standard-implicit-conversions)) from `string` to the parameter's type.</span></span>

<span data-ttu-id="59804-353">Se una chiamata alla funzione da una posizione nel codice sorgente omette un parametro facoltativo e il `CallerFilePathAttribute`, un valore letterale stringa che rappresenta tale percorso del file viene usato come argomento alla chiamata anziché il valore del parametro predefinito.</span><span class="sxs-lookup"><span data-stu-id="59804-353">If a function invocation from a location in source code omits an optional parameter with the `CallerFilePathAttribute`, then a string literal representing that location's file path is used as an argument to the invocation instead of the default parameter value.</span></span>

<span data-ttu-id="59804-354">Il formato del percorso del file è dipendente dall'implementazione.</span><span class="sxs-lookup"><span data-stu-id="59804-354">The format of the file path is implementation-dependent.</span></span>

<span data-ttu-id="59804-355">Si noti che il percorso del file potrebbe essere interessato dalle `#line` direttive ([direttive di riga](lexical-structure.md#line-directives)).</span><span class="sxs-lookup"><span data-stu-id="59804-355">Note that the file path may be affected by `#line` directives ([Line directives](lexical-structure.md#line-directives)).</span></span>

#### <a name="the-callermembername-attribute"></a><span data-ttu-id="59804-356">L'attributo CallerMemberName</span><span class="sxs-lookup"><span data-stu-id="59804-356">The CallerMemberName attribute</span></span>

<span data-ttu-id="59804-357">Il `System.Runtime.CompilerServices.CallerMemberNameAttribute` JE povolen parametri facoltativi quando viene eseguita una conversione implicita standard ([conversioni implicite](conversions.md#standard-implicit-conversions)) da `string` per il tipo del parametro.</span><span class="sxs-lookup"><span data-stu-id="59804-357">The `System.Runtime.CompilerServices.CallerMemberNameAttribute` is allowed on optional parameters when there is a standard implicit conversion ([Standard implicit conversions](conversions.md#standard-implicit-conversions)) from `string` to the parameter's type.</span></span>

<span data-ttu-id="59804-358">Se una chiamata alla funzione da una posizione all'interno del corpo di un membro di funzione o all'interno di un attributo applicato al membro di funzione stesso o il tipo restituito, i parametri o parametri di tipo nel codice sorgente omette un parametro facoltativo e il `CallerMemberNameAttribute`, un valore letterale stringa che rappresenta il nome di tale membro viene usato come argomento alla chiamata anziché il valore del parametro predefinito.</span><span class="sxs-lookup"><span data-stu-id="59804-358">If a function invocation from a location within the body of a function member or within an attribute applied to the function member itself or its return type, parameters or type parameters in source code omits an optional parameter with the `CallerMemberNameAttribute`, then a string literal representing the name of that member is used as an argument to the invocation instead of the default parameter value.</span></span>

<span data-ttu-id="59804-359">Per le chiamate che si verificano all'interno di metodi generici, viene usato solo il nome del metodo stesso, senza elenco parametri di tipo.</span><span class="sxs-lookup"><span data-stu-id="59804-359">For invocations that occur within generic methods, only the method name itself is used, without the type parameter list.</span></span>

<span data-ttu-id="59804-360">Per le chiamate che si verificano all'interno di implementazioni esplicite dell'interfaccia membro, viene usato solo il nome del metodo stesso, senza la qualificazione di interfaccia precedente.</span><span class="sxs-lookup"><span data-stu-id="59804-360">For invocations that occur within explicit interface member implementations, only the method name itself is used, without the preceding interface qualification.</span></span>

<span data-ttu-id="59804-361">Per le chiamate che si verificano all'interno di funzioni di accesso a proprietà o un evento, il nome del membro utilizzato è quello della proprietà o evento stesso.</span><span class="sxs-lookup"><span data-stu-id="59804-361">For invocations that occur within property or event accessors, the member name used is that of the property or event itself.</span></span>

<span data-ttu-id="59804-362">Per le chiamate che si verificano all'interno di funzioni di accesso dell'indicizzatore, il nome del membro utilizzato è fornito da un `IndexerNameAttribute` ([attributo IndexerName](attributes.md#the-indexername-attribute)) il membro di un indicizzatore, se presente, o il nome predefinito `Item` in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="59804-362">For invocations that occur within indexer accessors, the member name used is that supplied by an `IndexerNameAttribute` ([The IndexerName attribute](attributes.md#the-indexername-attribute)) on the indexer member, if present, or the default name `Item` otherwise.</span></span>

<span data-ttu-id="59804-363">Per le chiamate che si verificano all'interno delle dichiarazioni dei costruttori di istanze, i costruttori statici, distruttori e operatori membro nome usato è dipendente dall'implementazione.</span><span class="sxs-lookup"><span data-stu-id="59804-363">For invocations that occur within declarations of instance constructors, static constructors, destructors and operators the member name used is implementation-dependent.</span></span>

## <a name="attributes-for-interoperation"></a><span data-ttu-id="59804-364">Attributi per l'interoperabilità</span><span class="sxs-lookup"><span data-stu-id="59804-364">Attributes for Interoperation</span></span>

<span data-ttu-id="59804-365">Nota: In questa sezione è applicabile solo per l'implementazione .NET di Microsoft del linguaggio c#.</span><span class="sxs-lookup"><span data-stu-id="59804-365">Note: This section is applicable only to the Microsoft .NET implementation of C#.</span></span>

### <a name="interoperation-with-com-and-win32-components"></a><span data-ttu-id="59804-366">Interazione con componenti COM e Win32</span><span class="sxs-lookup"><span data-stu-id="59804-366">Interoperation with COM and Win32 components</span></span>

<span data-ttu-id="59804-367">Il runtime di .NET offre un numero elevato di attributi che consentono ai programmi c# di interoperare con componenti scritti con DLL Win32 e COM.</span><span class="sxs-lookup"><span data-stu-id="59804-367">The .NET run-time provides a large number of attributes that enable C# programs to interoperate with components written using COM and Win32 DLLs.</span></span> <span data-ttu-id="59804-368">Ad esempio, il `DllImport` attributo può essere utilizzato in un `static extern` metodo per indicare che l'implementazione del metodo deve essere trovato in una DLL Win32.</span><span class="sxs-lookup"><span data-stu-id="59804-368">For example, the `DllImport` attribute can be used on a `static extern` method to indicate that the implementation of the method is to be found in a Win32 DLL.</span></span> <span data-ttu-id="59804-369">Questi attributi si trovano nel `System.Runtime.InteropServices` dello spazio dei nomi e la documentazione dettagliata per questi attributi sono disponibili nella documentazione di runtime di .NET.</span><span class="sxs-lookup"><span data-stu-id="59804-369">These attributes are found in the `System.Runtime.InteropServices` namespace, and detailed documentation for these attributes is found in the .NET runtime documentation.</span></span>

### <a name="interoperation-with-other-net-languages"></a><span data-ttu-id="59804-370">Interoperabilità con altri linguaggi .NET</span><span class="sxs-lookup"><span data-stu-id="59804-370">Interoperation with other .NET languages</span></span>

#### <a name="the-indexername-attribute"></a><span data-ttu-id="59804-371">L'attributo IndexerName</span><span class="sxs-lookup"><span data-stu-id="59804-371">The IndexerName attribute</span></span>

<span data-ttu-id="59804-372">Gli indicizzatori vengono implementati in .NET usando le proprietà indicizzate e hanno un nome nei metadati di .NET.</span><span class="sxs-lookup"><span data-stu-id="59804-372">Indexers are implemented in .NET using indexed properties, and have a name in the .NET metadata.</span></span> <span data-ttu-id="59804-373">Se nessun `IndexerName` attributo è presente per un indicizzatore e quindi dal nome `Item` viene usato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="59804-373">If no `IndexerName` attribute is present for an indexer, then the name `Item` is used by default.</span></span> <span data-ttu-id="59804-374">Il `IndexerName` attributo consente agli sviluppatori di eseguire l'override di questa impostazione predefinita e specificare un nome diverso.</span><span class="sxs-lookup"><span data-stu-id="59804-374">The `IndexerName` attribute enables a developer to override this default and specify a different name.</span></span>

```csharp
namespace System.Runtime.CompilerServices.CSharp
{
    [AttributeUsage(AttributeTargets.Property)]
    public class IndexerNameAttribute: Attribute
    {
        public IndexerNameAttribute(string indexerName) {...}
        public string Value { get {...} } 
    }
}
```
