---
ms.openlocfilehash: af7af574814dc04ee3ece0396b7ae5f86b3ec8eb
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "64488906"
---
# <a name="classes"></a><span data-ttu-id="5bc16-101">Classi</span><span class="sxs-lookup"><span data-stu-id="5bc16-101">Classes</span></span>

<span data-ttu-id="5bc16-102">Una classe è una struttura di dati che può contenere membri dati costanti e i campi, membri funzione (metodi, proprietà, eventi, indicizzatori, operatori, costruttori di istanze, distruttori e i costruttori statici) e i tipi annidati.</span><span class="sxs-lookup"><span data-stu-id="5bc16-102">A class is a data structure that may contain data members (constants and fields), function members (methods, properties, events, indexers, operators, instance constructors, destructors and static constructors), and nested types.</span></span> <span data-ttu-id="5bc16-103">I tipi classe supportano l'ereditarietà, un meccanismo in base al quale una classe derivata può estendere e specializzare una classe di base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-103">Class types support inheritance, a mechanism whereby a derived class can extend and specialize a base class.</span></span>

## <a name="class-declarations"></a><span data-ttu-id="5bc16-104">Dichiarazioni di classe</span><span class="sxs-lookup"><span data-stu-id="5bc16-104">Class declarations</span></span>

<span data-ttu-id="5bc16-105">Oggetto *class_declaration* è un *type_declaration* ([dichiarazioni di tipo](namespaces.md#type-declarations)) che dichiara una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-105">A *class_declaration* is a *type_declaration* ([Type declarations](namespaces.md#type-declarations)) that declares a new class.</span></span>

```antlr
class_declaration
    : attributes? class_modifier* 'partial'? 'class' identifier type_parameter_list?
      class_base? type_parameter_constraints_clause* class_body ';'?
    ;
```

<span data-ttu-id="5bc16-106">Oggetto *class_declaration* costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), seguito da un set facoltativo di *class_modifier*s ([classe di modificatori](classes.md#class-modifiers)), seguito da un oggetto facoltativo `partial` modificatore, seguita dalla parola chiave `class` e un *identificatore* che denomina la classe, seguita da un facoltativo *type_parameter_list* ([parametri di tipo](classes.md#type-parameters)), seguito da un oggetto facoltativo *class_base* specifica ([della base Class Specification](classes.md#class-base-specification)), seguito da un set facoltativo di *type_parameter_constraints_clause*s ([digitare i vincoli del parametro](classes.md#type-parameter-constraints)), seguito da un *class_body*  ([Classe corpo](classes.md#class-body)), seguito facoltativamente da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-106">A *class_declaration* consists of an optional set of *attributes* ([Attributes](attributes.md)), followed by an optional set of *class_modifier*s ([Class modifiers](classes.md#class-modifiers)), followed by an optional `partial` modifier, followed by the keyword `class` and an *identifier* that names the class, followed by an optional *type_parameter_list* ([Type parameters](classes.md#type-parameters)), followed by an optional *class_base* specification ([Class base specification](classes.md#class-base-specification)) , followed by an optional set of *type_parameter_constraints_clause*s ([Type parameter constraints](classes.md#type-parameter-constraints)), followed by a *class_body* ([Class body](classes.md#class-body)), optionally followed by a semicolon.</span></span>

<span data-ttu-id="5bc16-107">Una dichiarazione di classe non può fornire *type_parameter_constraints_clause*s a meno che non fornisce anche una *type_parameter_list*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-107">A class declaration cannot supply *type_parameter_constraints_clause*s unless it also supplies a *type_parameter_list*.</span></span>

<span data-ttu-id="5bc16-108">Una dichiarazione di classe che fornisce una *type_parameter_list* è un ***dichiarazione di classe generica***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-108">A class declaration that supplies a *type_parameter_list* is a ***generic class declaration***.</span></span> <span data-ttu-id="5bc16-109">Inoltre, qualsiasi classe annidata all'interno di una dichiarazione di classe generica o una dichiarazione di struct generico è essa stessa una dichiarazione di classe generica, poiché i parametri di tipo per il tipo di contenitore devono essere forniti per creare un tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-109">Additionally, any class nested inside a generic class declaration or a generic struct declaration is itself a generic class declaration, since type parameters for the containing type must be supplied to create a constructed type.</span></span>

### <a name="class-modifiers"></a><span data-ttu-id="5bc16-110">Modificatori della classe</span><span class="sxs-lookup"><span data-stu-id="5bc16-110">Class modifiers</span></span>

<span data-ttu-id="5bc16-111">Oggetto *class_declaration* può includere una sequenza dei modificatori della classe:</span><span class="sxs-lookup"><span data-stu-id="5bc16-111">A *class_declaration* may optionally include a sequence of class modifiers:</span></span>

```antlr
class_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'abstract'
    | 'sealed'
    | 'static'
    | class_modifier_unsafe
    ;
```

<span data-ttu-id="5bc16-112">È un errore in fase di compilazione per più volte lo stesso modificatore in una dichiarazione di classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-112">It is a compile-time error for the same modifier to appear multiple times in a class declaration.</span></span>

<span data-ttu-id="5bc16-113">Il `new` modificatore è consentito nelle classi annidate.</span><span class="sxs-lookup"><span data-stu-id="5bc16-113">The `new` modifier is permitted on nested classes.</span></span> <span data-ttu-id="5bc16-114">Specifica che la classe nasconde un membro ereditato con lo stesso nome, come descritto in [il nuovo modificatore](classes.md#the-new-modifier).</span><span class="sxs-lookup"><span data-stu-id="5bc16-114">It specifies that the class hides an inherited member by the same name, as described in [The new modifier](classes.md#the-new-modifier).</span></span> <span data-ttu-id="5bc16-115">Tratta di un errore in fase di compilazione per il `new` modificatore venga visualizzato in una dichiarazione di classe che non sia una dichiarazione di classe annidata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-115">It is a compile-time error for the `new` modifier to appear on a class declaration that is not a nested class declaration.</span></span>

<span data-ttu-id="5bc16-116">Il `public`, `protected`, `internal`, e `private` modificatori determinano l'accessibilità della classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-116">The `public`, `protected`, `internal`, and `private` modifiers control the accessibility of the class.</span></span> <span data-ttu-id="5bc16-117">A seconda del contesto in cui si verifica la dichiarazione di classe, alcuni di questi modificatori potrebbe non essere consentito ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-117">Depending on the context in which the class declaration occurs, some of these modifiers may not be permitted ([Declared accessibility](basic-concepts.md#declared-accessibility)).</span></span>

<span data-ttu-id="5bc16-118">Il `abstract`, `sealed` e `static` modificatori sono descritti nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-118">The `abstract`, `sealed` and `static` modifiers are discussed in the following sections.</span></span>

#### <a name="abstract-classes"></a><span data-ttu-id="5bc16-119">Classi astratte</span><span class="sxs-lookup"><span data-stu-id="5bc16-119">Abstract classes</span></span>

<span data-ttu-id="5bc16-120">Il `abstract` modificatore viene usato per indicare che una classe è incompleta e che si dovrà essere utilizzato solo come classe di base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-120">The `abstract` modifier is used to indicate that a class is incomplete and that it is intended to be used only as a base class.</span></span> <span data-ttu-id="5bc16-121">Una classe astratta è diverso da una classe non astratta nei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-121">An abstract class differs from a non-abstract class in the following ways:</span></span>

*  <span data-ttu-id="5bc16-122">Una classe astratta non può essere creata un'istanza direttamente ed è un errore in fase di compilazione per utilizzare il `new` operatore su una classe astratta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-122">An abstract class cannot be instantiated directly, and it is a compile-time error to use the `new` operator on an abstract class.</span></span> <span data-ttu-id="5bc16-123">Sebbene sia possibile disporre di variabili e valori i cui tipi in fase di compilazione sono astratte, tali variabili e valori necessariamente sarà `null` o contenere riferimenti a istanze di classi non astratte derivate dai tipi astratti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-123">While it is possible to have variables and values whose compile-time types are abstract, such variables and values will necessarily either be `null` or contain references to instances of non-abstract classes derived from the abstract types.</span></span>
*  <span data-ttu-id="5bc16-124">Una classe astratta è consentita (ma non obbligatorio) per contenere membri astratti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-124">An abstract class is permitted (but not required) to contain abstract members.</span></span>
*  <span data-ttu-id="5bc16-125">Una classe astratta non può essere sealed.</span><span class="sxs-lookup"><span data-stu-id="5bc16-125">An abstract class cannot be sealed.</span></span>

<span data-ttu-id="5bc16-126">Quando una classe non astratta è derivata da una classe astratta, la classe non astratta deve includere implementazioni effettive di tutti i membri astratti ereditati, eseguendo l'override di tali membri astratti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-126">When a non-abstract class is derived from an abstract class, the non-abstract class must include actual implementations of all inherited abstract members, thereby overriding those abstract members.</span></span> <span data-ttu-id="5bc16-127">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-127">In the example</span></span>
```csharp
abstract class A
{
    public abstract void F();
}

abstract class B: A
{
    public void G() {}
}

class C: B
{
    public override void F() {
        // actual implementation of F
    }
}
```
<span data-ttu-id="5bc16-128">la classe astratta `A` introduce un metodo astratto `F`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-128">the abstract class `A` introduces an abstract method `F`.</span></span> <span data-ttu-id="5bc16-129">Classe `B` introduce un ulteriore metodo `G`, ma poiché non fornisce un'implementazione di `F`, `B` deve inoltre essere dichiarata astratta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-129">Class `B` introduces an additional method `G`, but since it doesn't provide an implementation of `F`, `B` must also be declared abstract.</span></span> <span data-ttu-id="5bc16-130">Classe `C` esegue l'override `F` e fornisce un'implementazione effettiva.</span><span class="sxs-lookup"><span data-stu-id="5bc16-130">Class `C` overrides `F` and provides an actual implementation.</span></span> <span data-ttu-id="5bc16-131">Poiché non esistono membri astratti nelle `C`, `C` è consentita (ma non obbligatorio) per essere non astratta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-131">Since there are no abstract members in `C`, `C` is permitted (but not required) to be non-abstract.</span></span>

#### <a name="sealed-classes"></a><span data-ttu-id="5bc16-132">Classi sealed</span><span class="sxs-lookup"><span data-stu-id="5bc16-132">Sealed classes</span></span>

<span data-ttu-id="5bc16-133">Il `sealed` modificatore viene usato per impedire la derivazione da una classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-133">The `sealed` modifier is used to prevent derivation from a class.</span></span> <span data-ttu-id="5bc16-134">Se una classe sealed non è specificata come classe di base di un'altra classe, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-134">A compile-time error occurs if a sealed class is specified as the base class of another class.</span></span>

<span data-ttu-id="5bc16-135">Una classe sealed non può essere anche una classe astratta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-135">A sealed class cannot also be an abstract class.</span></span>

<span data-ttu-id="5bc16-136">Il `sealed` modificatore viene usato principalmente per evitare derivazioni indesiderate, ma consente anche di alcune ottimizzazioni in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-136">The `sealed` modifier is primarily used to prevent unintended derivation, but it also enables certain run-time optimizations.</span></span> <span data-ttu-id="5bc16-137">In particolare, poiché è noto che una classe sealed mai in tutte le classi derivate, è possibile trasformare le chiamate dei membri di funzione virtuale in istanze di classe sealed in chiamate al non virtuale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-137">In particular, because a sealed class is known to never have any derived classes, it is possible to transform virtual function member invocations on sealed class instances into non-virtual invocations.</span></span>

#### <a name="static-classes"></a><span data-ttu-id="5bc16-138">Classi statiche</span><span class="sxs-lookup"><span data-stu-id="5bc16-138">Static classes</span></span>

<span data-ttu-id="5bc16-139">Il `static` modificatore viene usato per contrassegnare la classe viene dichiarata come un ***classe statica***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-139">The `static` modifier is used to mark the class being declared as a ***static class***.</span></span> <span data-ttu-id="5bc16-140">Una classe statica non è possibile creare un'istanza, non può essere utilizzata come un tipo e può contenere solo membri statici.</span><span class="sxs-lookup"><span data-stu-id="5bc16-140">A static class cannot be instantiated, cannot be used as a type and can contain only static members.</span></span> <span data-ttu-id="5bc16-141">Solo una classe statica può contenere dichiarazioni di metodi di estensione ([metodi di estensione](classes.md#extension-methods)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-141">Only a static class can contain declarations of extension methods ([Extension methods](classes.md#extension-methods)).</span></span>

<span data-ttu-id="5bc16-142">Una dichiarazione di classe statica è soggetto alle restrizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-142">A static class declaration is subject to the following restrictions:</span></span>

*  <span data-ttu-id="5bc16-143">Una classe statica non può includere un `sealed` o `abstract` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-143">A static class may not include a `sealed` or `abstract` modifier.</span></span> <span data-ttu-id="5bc16-144">Si noti tuttavia che poiché non può essere creata un'istanza o derivata da una classe statica, si comporta come se fosse sealed e astratta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-144">Note, however, that since a static class cannot be instantiated or derived from, it behaves as if it was both sealed and abstract.</span></span>
*  <span data-ttu-id="5bc16-145">Una classe statica non può includere un *class_base* specification ([classe base specifica](classes.md#class-base-specification)) e non è possibile specificare in modo esplicito una classe di base o un elenco di interfacce implementate.</span><span class="sxs-lookup"><span data-stu-id="5bc16-145">A static class may not include a *class_base* specification ([Class base specification](classes.md#class-base-specification)) and cannot explicitly specify a base class or a list of implemented interfaces.</span></span> <span data-ttu-id="5bc16-146">Una classe statica eredita in modo implicito dal tipo `object`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-146">A static class implicitly inherits from type `object`.</span></span>
*  <span data-ttu-id="5bc16-147">Una classe statica può contenere solo membri statici ([i membri statici e di istanza](classes.md#static-and-instance-members)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-147">A static class can only contain static members ([Static and instance members](classes.md#static-and-instance-members)).</span></span> <span data-ttu-id="5bc16-148">Si noti che le costanti e i tipi annidati sono classificati come membri statici.</span><span class="sxs-lookup"><span data-stu-id="5bc16-148">Note that constants and nested types are classified as static members.</span></span>
*  <span data-ttu-id="5bc16-149">Una classe statica non può avere membri con `protected` o `protected internal` l'accessibilità dichiarata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-149">A static class cannot have members with `protected` or `protected internal` declared accessibility.</span></span>

<span data-ttu-id="5bc16-150">Tratta di un errore in fase di compilazione violare una di queste restrizioni.</span><span class="sxs-lookup"><span data-stu-id="5bc16-150">It is a compile-time error to violate any of these restrictions.</span></span>

<span data-ttu-id="5bc16-151">Una classe statica non ha alcun costruttore di istanza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-151">A static class has no instance constructors.</span></span> <span data-ttu-id="5bc16-152">Non è possibile dichiarare un costruttore di istanza in una classe statica e nessun costruttore di istanza predefinito ([costruttori predefiniti](classes.md#default-constructors)) viene fornito per una classe statica.</span><span class="sxs-lookup"><span data-stu-id="5bc16-152">It is not possible to declare an instance constructor in a static class, and no default instance constructor ([Default constructors](classes.md#default-constructors)) is provided for a static class.</span></span>

<span data-ttu-id="5bc16-153">I membri di una classe statica non sono automaticamente statici e le dichiarazioni dei membri in modo esplicito devono includere un `static` modificatore (ad eccezione di costanti e i tipi annidati).</span><span class="sxs-lookup"><span data-stu-id="5bc16-153">The members of a static class are not automatically static, and the member declarations must explicitly include a `static` modifier (except for constants and nested types).</span></span> <span data-ttu-id="5bc16-154">Quando una classe è annidata all'interno di una classe esterna statica, la classe annidata non è una classe statica a meno che non includa in modo esplicito un `static` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-154">When a class is nested within a static outer class, the nested class is not a static class unless it explicitly includes a `static` modifier.</span></span>

<span data-ttu-id="5bc16-155">__Fa riferimento ai tipi di classe statica__</span><span class="sxs-lookup"><span data-stu-id="5bc16-155">__Referencing static class types__</span></span>

<span data-ttu-id="5bc16-156">Oggetto *namespace_or_type_name* ([Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)) è consentito fare riferimento a una classe statica se</span><span class="sxs-lookup"><span data-stu-id="5bc16-156">A *namespace_or_type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)) is permitted to reference a static class if</span></span>

*  <span data-ttu-id="5bc16-157">Il *namespace_or_type_name* è la `T` in un *namespace_or_type_name* del form `T.I`, o</span><span class="sxs-lookup"><span data-stu-id="5bc16-157">The *namespace_or_type_name* is the `T` in a *namespace_or_type_name* of the form `T.I`, or</span></span>
*  <span data-ttu-id="5bc16-158">Il *namespace_or_type_name* è la `T` in un *typeof_expression* ([elenchi di argomenti](expressions.md#argument-lists)1) nel formato `typeof(T)`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-158">The *namespace_or_type_name* is the `T` in a *typeof_expression* ([Argument lists](expressions.md#argument-lists)1) of the form `typeof(T)`.</span></span>

<span data-ttu-id="5bc16-159">Oggetto *primary_expression* ([membri funzione](expressions.md#function-members)) è consentito fare riferimento a una classe statica se</span><span class="sxs-lookup"><span data-stu-id="5bc16-159">A *primary_expression* ([Function members](expressions.md#function-members)) is permitted to reference a static class if</span></span>

*  <span data-ttu-id="5bc16-160">Il *primary_expression* è la `E` in un *member_access* ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) nel formato `E.I`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-160">The *primary_expression* is the `E` in a *member_access* ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) of the form `E.I`.</span></span>

<span data-ttu-id="5bc16-161">In qualsiasi altro contesto è un errore in fase di compilazione per fare riferimento a una classe statica.</span><span class="sxs-lookup"><span data-stu-id="5bc16-161">In any other context it is a compile-time error to reference a static class.</span></span> <span data-ttu-id="5bc16-162">Ad esempio, si tratta di un errore per una classe statica da utilizzare come classe di base, un tipo costitutivo ([i tipi annidati](classes.md#nested-types)) di un membro, un argomento tipo generico o un vincolo del parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-162">For example, it is an error for a static class to be used as a base class, a constituent type ([Nested types](classes.md#nested-types)) of a member, a generic type argument, or a type parameter constraint.</span></span> <span data-ttu-id="5bc16-163">Analogamente, una classe statica non può essere utilizzata in un tipo di matrice, un tipo di puntatore, un `new` espressione, un'espressione cast, un' `is` espressione, un `as` espressione, un `sizeof` espressione o un'espressione con valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-163">Likewise, a static class cannot be used in an array type, a pointer type, a `new` expression, a cast expression, an `is` expression, an `as` expression, a `sizeof` expression, or a default value expression.</span></span>

### <a name="partial-modifier"></a><span data-ttu-id="5bc16-164">Modificatore parziale</span><span class="sxs-lookup"><span data-stu-id="5bc16-164">Partial modifier</span></span>

<span data-ttu-id="5bc16-165">Il `partial` modificatore viene usato per indicare che il *class_declaration* è una dichiarazione di tipo parziale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-165">The `partial` modifier is used to indicate that this *class_declaration* is a partial type declaration.</span></span> <span data-ttu-id="5bc16-166">Più dichiarazioni di tipo parziale con lo stesso nome all'interno di una dichiarazione dello spazio dei nomi o tipo di inclusione combinano alla dichiarazione di un tipo di modulo, in base alle regole specificate [i tipi parziali](classes.md#partial-types).</span><span class="sxs-lookup"><span data-stu-id="5bc16-166">Multiple partial type declarations with the same name within an enclosing namespace or type declaration combine to form one type declaration, following the rules specified in [Partial types](classes.md#partial-types).</span></span>

<span data-ttu-id="5bc16-167">Con la dichiarazione di una classe distribuita su segmenti separati di testo del programma può essere utile se questi segmenti vengono prodotti o gestiti in contesti diversi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-167">Having the declaration of a class distributed over separate segments of program text can be useful if these segments are produced or maintained in different contexts.</span></span> <span data-ttu-id="5bc16-168">Ad esempio, una parte di una dichiarazione di classe possa essere generate, da un computer mentre l'altra viene creata manualmente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-168">For instance, one part of a class declaration may be machine generated, whereas the other is manually authored.</span></span> <span data-ttu-id="5bc16-169">La separazione testuale di due impedisce gli aggiornamenti da uno entri in conflitto con gli aggiornamenti da altro.</span><span class="sxs-lookup"><span data-stu-id="5bc16-169">Textual separation of the two prevents updates by one from conflicting with updates by the other.</span></span>

### <a name="type-parameters"></a><span data-ttu-id="5bc16-170">Parametri di tipo</span><span class="sxs-lookup"><span data-stu-id="5bc16-170">Type parameters</span></span>

<span data-ttu-id="5bc16-171">Un parametro di tipo è un identificatore semplice che denota un segnaposto per un argomento di tipo fornito per creare un tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-171">A type parameter is a simple identifier that denotes a placeholder for a type argument supplied to create a constructed type.</span></span> <span data-ttu-id="5bc16-172">Un parametro di tipo è un segnaposto formale per un tipo che verrà fornito in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-172">A type parameter is a formal placeholder for a type that will be supplied later.</span></span> <span data-ttu-id="5bc16-173">Al contrario, un argomento di tipo ([gli argomenti di tipo](types.md#type-arguments)) è il tipo effettivo che verrà sostituito con il parametro di tipo quando viene creato un tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-173">By contrast, a type argument ([Type arguments](types.md#type-arguments)) is the actual type that is substituted for the type parameter when a constructed type is created.</span></span>

```antlr
type_parameter_list
    : '<' type_parameters '>'
    ;

type_parameters
    : attributes? type_parameter
    | type_parameters ',' attributes? type_parameter
    ;

type_parameter
    : identifier
    ;
```

<span data-ttu-id="5bc16-174">Ogni parametro di tipo in una dichiarazione di classe definisce un nome nello spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)) di tale classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-174">Each type parameter in a class declaration defines a name in the declaration space ([Declarations](basic-concepts.md#declarations)) of that class.</span></span> <span data-ttu-id="5bc16-175">Di conseguenza, non può avere lo stesso nome di un altro parametro di tipo o un membro dichiarato in tale classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-175">Thus, it cannot have the same name as another type parameter or a member declared in that class.</span></span> <span data-ttu-id="5bc16-176">Un parametro di tipo non può avere lo stesso nome del tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-176">A type parameter cannot have the same name as the type itself.</span></span>

### <a name="class-base-specification"></a><span data-ttu-id="5bc16-177">Specifica della classe base</span><span class="sxs-lookup"><span data-stu-id="5bc16-177">Class base specification</span></span>

<span data-ttu-id="5bc16-178">Una dichiarazione di classe può includere un *class_base* specifica, che definisce la classe base diretta della classe e le interfacce ([interfacce](interfaces.md)) direttamente implementata dalla classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-178">A class declaration may include a *class_base* specification, which defines the direct base class of the class and the interfaces ([Interfaces](interfaces.md)) directly implemented by the class.</span></span>

```antlr
class_base
    : ':' class_type
    | ':' interface_type_list
    | ':' class_type ',' interface_type_list
    ;

interface_type_list
    : interface_type (',' interface_type)*
    ;
```

<span data-ttu-id="5bc16-179">La classe di base specificata in una dichiarazione di classe può essere un tipo di classe costruita ([i tipi costruiti](types.md#constructed-types)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-179">The base class specified in a class declaration can be a constructed class type ([Constructed types](types.md#constructed-types)).</span></span> <span data-ttu-id="5bc16-180">Una classe di base non può essere un parametro di tipo di per sé, ma può comportare i parametri di tipo che rientrano nell'ambito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-180">A base class cannot be a type parameter on its own, though it can involve the type parameters that are in scope.</span></span>

```csharp
class Extend<V>: V {}            // Error, type parameter used as base class
```

#### <a name="base-classes"></a><span data-ttu-id="5bc16-181">Classi di base</span><span class="sxs-lookup"><span data-stu-id="5bc16-181">Base classes</span></span>

<span data-ttu-id="5bc16-182">Quando un *class_type* viene incluso nel *class_base*, specifica la classe di base diretta della classe viene dichiarata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-182">When a *class_type* is included in the *class_base*, it specifies the direct base class of the class being declared.</span></span> <span data-ttu-id="5bc16-183">Se una dichiarazione di classe non ha alcun *class_base*, o se il *class_base* Elenca solo i tipi di interfaccia, si presuppone che la classe base diretta da `object`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-183">If a class declaration has no *class_base*, or if the *class_base* lists only interface types, the direct base class is assumed to be `object`.</span></span> <span data-ttu-id="5bc16-184">Una classe eredita i membri dalla classe di base dirette, come descritto in [ereditarietà](classes.md#inheritance).</span><span class="sxs-lookup"><span data-stu-id="5bc16-184">A class inherits members from its direct base class, as described in [Inheritance](classes.md#inheritance).</span></span>

<span data-ttu-id="5bc16-185">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-185">In the example</span></span>
```csharp
class A {}

class B: A {}
```
<span data-ttu-id="5bc16-186">classe `A` viene definita la classe base diretta `B`, e `B` viene definito come derivata da `A`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-186">class `A` is said to be the direct base class of `B`, and `B` is said to be derived from `A`.</span></span> <span data-ttu-id="5bc16-187">Poiché `A` viene non esplicitamente specificata una classe base diretta, la relativa classe base diretta è implicitamente `object`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-187">Since `A` does not explicitly specify a direct base class, its direct base class is implicitly `object`.</span></span>

<span data-ttu-id="5bc16-188">Per un tipo di classe costruita, se una classe di base viene specificata nella dichiarazione di classe generica, la classe di base del tipo costruito viene ottenuta tramite la sostituzione, per ognuno *type_parameter* nella dichiarazione di classe di base, il corrispondente *type_argument* del tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-188">For a constructed class type, if a base class is specified in the generic class declaration, the base class of the constructed type is obtained by substituting, for each *type_parameter* in the base class declaration, the corresponding *type_argument* of the constructed type.</span></span> <span data-ttu-id="5bc16-189">Date le dichiarazioni di classe generica</span><span class="sxs-lookup"><span data-stu-id="5bc16-189">Given the generic class declarations</span></span>
```csharp
class B<U,V> {...}

class G<T>: B<string,T[]> {...}
```
<span data-ttu-id="5bc16-190">la classe di base del tipo costruito `G<int>` sarebbe `B<string,int[]>`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-190">the base class of the constructed type `G<int>` would be `B<string,int[]>`.</span></span>

<span data-ttu-id="5bc16-191">La classe di base diretta di un tipo di classe deve essere accessibile almeno quanto il tipo di classe ([domini accessibilità](basic-concepts.md#accessibility-domains)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-191">The direct base class of a class type must be at least as accessible as the class type itself ([Accessibility domains](basic-concepts.md#accessibility-domains)).</span></span> <span data-ttu-id="5bc16-192">Ad esempio, si tratta di un errore in fase di compilazione per un `public` classe derivandola da un' `private` o `internal` classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-192">For example, it is a compile-time error for a `public` class to derive from a `private` or `internal` class.</span></span>

<span data-ttu-id="5bc16-193">La classe di base diretta di un tipo di classe non deve essere uno qualsiasi dei tipi seguenti: `System.Array`, `System.Delegate`, `System.MulticastDelegate`, `System.Enum`, o `System.ValueType`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-193">The direct base class of a class type must not be any of the following types: `System.Array`, `System.Delegate`, `System.MulticastDelegate`, `System.Enum`, or `System.ValueType`.</span></span> <span data-ttu-id="5bc16-194">Inoltre, non è possibile usare una dichiarazione di classe generica `System.Attribute` come classe base diretta o indiretta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-194">Furthermore, a generic class declaration cannot use `System.Attribute` as a direct or indirect base class.</span></span>

<span data-ttu-id="5bc16-195">Il tentativo di determinare il significato di specifica della classe base diretta `A` di una classe `B`, la classe base diretta `B` temporaneamente si presuppone che sia `object`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-195">While determining the meaning of the direct base class specification `A` of a class `B`, the direct base class of `B` is temporarily assumed to be `object`.</span></span> <span data-ttu-id="5bc16-196">In modo intuitivo questo modo si garantisce che il significato di una specifica classe di base non è in modo ricorsivo dipendere da se stessa.</span><span class="sxs-lookup"><span data-stu-id="5bc16-196">Intuitively this ensures that the meaning of a base class specification cannot recursively depend on itself.</span></span> <span data-ttu-id="5bc16-197">Esempio:</span><span class="sxs-lookup"><span data-stu-id="5bc16-197">The example:</span></span>
```csharp
class A<T> {
   public class B {}
}

class C : A<C.B> {}
```
<span data-ttu-id="5bc16-198">Genera un errore perché nella specifica di classe di base `A<C.B>` la classe base diretta `C` viene considerata `object`e pertanto (in base alle regole [Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)) `C` non viene considerata come contiene il membro `B`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-198">is in error since in the base class specification `A<C.B>` the direct base class of `C` is considered to be `object`, and hence (by the rules of [Namespace and type names](basic-concepts.md#namespace-and-type-names))  `C` is not considered to have a member `B`.</span></span>

<span data-ttu-id="5bc16-199">Le classi di base di un tipo classe sono la classe base diretta e le relative classi base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-199">The base classes of a class type are the direct base class and its base classes.</span></span> <span data-ttu-id="5bc16-200">In altre parole, il set di classi di base è la chiusura transitiva di relazione la classe di base diretta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-200">In other words, the set of base classes is the transitive closure of the direct base class relationship.</span></span> <span data-ttu-id="5bc16-201">Che fa riferimento all'esempio precedente, le classi di base `B` vengono `A` e `object`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-201">Referring to the example above, the base classes of `B` are `A` and `object`.</span></span> <span data-ttu-id="5bc16-202">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-202">In the example</span></span>
```csharp
class A {...}

class B<T>: A {...}

class C<T>: B<IComparable<T>> {...}

class D<T>: C<T[]> {...}
```
<span data-ttu-id="5bc16-203">le classi di base `D<int>` vengono `C<int[]>`, `B<IComparable<int[]>>`, `A`, e `object`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-203">the base classes of `D<int>` are `C<int[]>`, `B<IComparable<int[]>>`, `A`, and `object`.</span></span>

<span data-ttu-id="5bc16-204">Ad eccezione di classe `object`, ogni tipo di classe ha esattamente una classe di base diretta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-204">Except for class `object`, every class type has exactly one direct base class.</span></span> <span data-ttu-id="5bc16-205">Il `object` classe non dispone di alcuna classe di base diretta ed è la principale classe base di tutte le altre classi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-205">The `object` class has no direct base class and is the ultimate base class of all other classes.</span></span>

<span data-ttu-id="5bc16-206">Quando una classe `B` deriva da una classe `A`, tratta di un errore in fase di compilazione per `A` dipendere `B`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-206">When a class `B` derives from a class `A`, it is a compile-time error for `A` to depend on `B`.</span></span> <span data-ttu-id="5bc16-207">Una classe ***dipende direttamente*** relativa classe base diretta (se presente) e ***dipende direttamente*** la classe all'interno del quale area è nidificata immediatamente (se presente).</span><span class="sxs-lookup"><span data-stu-id="5bc16-207">A class ***directly depends on*** its direct base class (if any) and ***directly depends on*** the class within which it is immediately nested (if any).</span></span> <span data-ttu-id="5bc16-208">Data questa definizione, il set completo di classi da cui dipende una classe è la chiusura riflessiva e transitiva il ***dipende direttamente*** relazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-208">Given this definition, the complete set of classes upon which a class depends is the reflexive and transitive closure of the ***directly depends on*** relationship.</span></span>

<span data-ttu-id="5bc16-209">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-209">The example</span></span>
```csharp
class A: A {}
```
<span data-ttu-id="5bc16-210">è errato perché la classe dipende da se stessa.</span><span class="sxs-lookup"><span data-stu-id="5bc16-210">is erroneous because the class depends on itself.</span></span> <span data-ttu-id="5bc16-211">Analogamente, l'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-211">Likewise, the example</span></span>
```csharp
class A: B {}
class B: C {}
class C: A {}
```
<span data-ttu-id="5bc16-212">è in errore perché le classi in modo circolare dipendono da se stessi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-212">is in error because the classes circularly depend on themselves.</span></span> <span data-ttu-id="5bc16-213">Infine, l'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-213">Finally, the example</span></span>
```csharp
class A: B.C {}

class B: A
{
    public class C {}
}
```
<span data-ttu-id="5bc16-214">Genera un errore in fase di compilazione in quanto `A` dipende `B.C` (la classe base diretta), che dipende `B` (la relativa classe che lo contiene), che in modo circolare dipende `A`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-214">results in a compile-time error because `A` depends on `B.C` (its direct base class), which depends on `B` (its immediately enclosing class), which circularly depends on `A`.</span></span>

<span data-ttu-id="5bc16-215">Si noti che una classe non dipendono dalle classi che sono annidate all'interno di esso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-215">Note that a class does not depend on the classes that are nested within it.</span></span> <span data-ttu-id="5bc16-216">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-216">In the example</span></span>
```csharp
class A
{
    class B: A {}
}
```
<span data-ttu-id="5bc16-217">`B` dipende `A` (perché `A` è la relativa classe base diretta sia relativa classe che lo contiene), ma `A` non dipende `B` (poiché `B` è una classe di base né una classe che contiene `A` ).</span><span class="sxs-lookup"><span data-stu-id="5bc16-217">`B` depends on `A` (because `A` is both its direct base class and its immediately enclosing class), but `A` does not depend on `B` (since `B` is neither a base class nor an enclosing class of `A`).</span></span> <span data-ttu-id="5bc16-218">Di conseguenza, l'esempio è valido.</span><span class="sxs-lookup"><span data-stu-id="5bc16-218">Thus, the example is valid.</span></span>

<span data-ttu-id="5bc16-219">Non è possibile derivare da una `sealed` classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-219">It is not possible to derive from a `sealed` class.</span></span> <span data-ttu-id="5bc16-220">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-220">In the example</span></span>
```csharp
sealed class A {}

class B: A {}            // Error, cannot derive from a sealed class
```
<span data-ttu-id="5bc16-221">classe `B` è in errore perché tenta di derivare il `sealed` classe `A`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-221">class `B` is in error because it attempts to derive from the `sealed` class `A`.</span></span>

#### <a name="interface-implementations"></a><span data-ttu-id="5bc16-222">Implementazioni di interfacce</span><span class="sxs-lookup"><span data-stu-id="5bc16-222">Interface implementations</span></span>

<span data-ttu-id="5bc16-223">Oggetto *class_base* specifica può includere un elenco dei tipi di interfaccia, in cui i casi la classe sono detta implementare direttamente i tipi di interfaccia specificato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-223">A *class_base* specification may include a list of interface types, in which case the class is said to directly implement the given interface types.</span></span> <span data-ttu-id="5bc16-224">Le implementazioni di interfaccia sono descritte dettagliatamente nella [implementazioni di interfacce](interfaces.md#interface-implementations).</span><span class="sxs-lookup"><span data-stu-id="5bc16-224">Interface implementations are discussed further in [Interface implementations](interfaces.md#interface-implementations).</span></span>

### <a name="type-parameter-constraints"></a><span data-ttu-id="5bc16-225">Vincoli del parametro di tipo</span><span class="sxs-lookup"><span data-stu-id="5bc16-225">Type parameter constraints</span></span>

<span data-ttu-id="5bc16-226">Le dichiarazioni di metodo e il tipo generiche possono facoltativamente specificare i vincoli del parametro includendo *type_parameter_constraints_clause*s.</span><span class="sxs-lookup"><span data-stu-id="5bc16-226">Generic type and method declarations can optionally specify type parameter constraints by including *type_parameter_constraints_clause*s.</span></span>

```antlr
type_parameter_constraints_clause
    : 'where' type_parameter ':' type_parameter_constraints
    ;

type_parameter_constraints
    : primary_constraint
    | secondary_constraints
    | constructor_constraint
    | primary_constraint ',' secondary_constraints
    | primary_constraint ',' constructor_constraint
    | secondary_constraints ',' constructor_constraint
    | primary_constraint ',' secondary_constraints ',' constructor_constraint
    ;

primary_constraint
    : class_type
    | 'class'
    | 'struct'
    ;

secondary_constraints
    : interface_type
    | type_parameter
    | secondary_constraints ',' interface_type
    | secondary_constraints ',' type_parameter
    ;

constructor_constraint
    : 'new' '(' ')'
    ;
```

<span data-ttu-id="5bc16-227">Ciascuna *type_parameter_constraints_clause* costituito il token `where`, seguito dal nome di un parametro di tipo, seguito da due punti e l'elenco di vincoli per quel parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-227">Each *type_parameter_constraints_clause* consists of the token `where`, followed by the name of a type parameter, followed by a colon and the list of constraints for that type parameter.</span></span> <span data-ttu-id="5bc16-228">Può esistere al massimo uno `where` clausola per ogni parametro di tipo e il `where` clausole possono essere elencate in qualsiasi ordine.</span><span class="sxs-lookup"><span data-stu-id="5bc16-228">There can be at most one `where` clause for each type parameter, and the `where` clauses can be listed in any order.</span></span> <span data-ttu-id="5bc16-229">Ad esempio la `get` e `set` token in una funzione di accesso, il `where` token non è una parola chiave.</span><span class="sxs-lookup"><span data-stu-id="5bc16-229">Like the `get` and `set` tokens in a property accessor, the `where` token is not a keyword.</span></span>

<span data-ttu-id="5bc16-230">L'elenco dei vincoli indicati un `where` clausola può includere uno dei componenti seguenti, nell'ordine indicato: il vincolo di costruttore, un singolo vincolo primario e uno o più vincoli secondari `new()`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-230">The list of constraints given in a `where` clause can include any of the following components, in this order: a single primary constraint, one or more secondary constraints, and the constructor constraint, `new()`.</span></span>

<span data-ttu-id="5bc16-231">Un vincolo primario può essere un tipo di classe o la ***vincolo di tipo di riferimento*** `class` o il ***valore vincolo di tipo*** `struct`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-231">A primary constraint can be a class type or the ***reference type constraint*** `class` or the ***value type constraint*** `struct`.</span></span> <span data-ttu-id="5bc16-232">Un vincolo secondario può essere un' *type_parameter* oppure *interface_type*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-232">A secondary constraint can be a *type_parameter* or *interface_type*.</span></span>

<span data-ttu-id="5bc16-233">Il vincolo di tipo riferimento specifica che un argomento di tipo utilizzato per il parametro type deve essere un tipo di riferimento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-233">The reference type constraint specifies that a type argument used for the type parameter must be a reference type.</span></span> <span data-ttu-id="5bc16-234">Tutti i tipi di classe, tipi interfaccia, tipi delegati, i tipi di matrice e i parametri di tipo noti per essere un tipo riferimento (come definita di seguito) soddisfano questo vincolo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-234">All class types, interface types, delegate types, array types, and type parameters known to be a reference type (as defined below) satisfy this constraint.</span></span>

<span data-ttu-id="5bc16-235">Il vincolo di tipo value specifica che un argomento di tipo utilizzato per il parametro type deve essere un tipo di valore non nullable.</span><span class="sxs-lookup"><span data-stu-id="5bc16-235">The value type constraint specifies that a type argument used for the type parameter must be a non-nullable value type.</span></span> <span data-ttu-id="5bc16-236">Tutti i tipi struct non nullable, tipi enum e parametri di tipo con il vincolo di tipo valore soddisfano il vincolo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-236">All non-nullable struct types, enum types, and type parameters having the value type constraint satisfy this constraint.</span></span> <span data-ttu-id="5bc16-237">Si noti che sebbene classificato come un tipo di valore, un tipo che ammette valori null ([tipi Nullable](types.md#nullable-types)) non soddisfa il vincolo di tipo valore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-237">Note that although classified as a value type, a nullable type ([Nullable types](types.md#nullable-types)) does not satisfy the value type constraint.</span></span> <span data-ttu-id="5bc16-238">Un parametro di tipo con il vincolo di tipo valore non può avere anche il *constructor_constraint*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-238">A type parameter having the value type constraint cannot also have the *constructor_constraint*.</span></span>

<span data-ttu-id="5bc16-239">Tipi di puntatore non sono mai consentiti sia gli argomenti di tipo e non vengono considerati per soddisfare entrambi i tipo o valore vincoli di tipo riferimento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-239">Pointer types are never allowed to be type arguments and are not considered to satisfy either the reference type or value type constraints.</span></span>

<span data-ttu-id="5bc16-240">Se un vincolo è un tipo di classe, un tipo di interfaccia o un parametro di tipo, tale tipo specifica un minima "base del tipo" che deve supportare ogni argomento di tipo utilizzato per tale parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-240">If a constraint is a class type, an interface type, or a type parameter, that type specifies a minimal "base type" that every type argument used for that type parameter must support.</span></span> <span data-ttu-id="5bc16-241">Ogni volta che viene usato un tipo costruito o metodo generico, argomento di tipo viene confrontato con i vincoli sul parametro di tipo in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-241">Whenever a constructed type or generic method is used, the type argument is checked against the constraints on the type parameter at compile-time.</span></span> <span data-ttu-id="5bc16-242">L'argomento tipo fornito deve soddisfare le condizioni descritte nel [che soddisfano i vincoli](types.md#satisfying-constraints).</span><span class="sxs-lookup"><span data-stu-id="5bc16-242">The type argument supplied must satisfy the conditions described in [Satisfying constraints](types.md#satisfying-constraints).</span></span>

<span data-ttu-id="5bc16-243">Oggetto *class_type* vincolo deve soddisfare le regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-243">A *class_type* constraint must satisfy the following rules:</span></span>

*  <span data-ttu-id="5bc16-244">Il tipo deve essere un tipo di classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-244">The type must be a class type.</span></span>
*  <span data-ttu-id="5bc16-245">Il tipo non deve essere `sealed`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-245">The type must not be `sealed`.</span></span>
*  <span data-ttu-id="5bc16-246">Il tipo non deve essere uno dei tipi seguenti: `System.Array`, `System.Delegate`, `System.Enum`, o `System.ValueType`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-246">The type must not be one of the following types: `System.Array`, `System.Delegate`, `System.Enum`, or `System.ValueType`.</span></span>
*  <span data-ttu-id="5bc16-247">Il tipo non deve essere `object`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-247">The type must not be `object`.</span></span> <span data-ttu-id="5bc16-248">Poiché tutti i tipi derivano dal `object`, tale vincolo non avrà effetto se si fosse consentito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-248">Because all types derive from `object`, such a constraint would have no effect if it were permitted.</span></span>
*  <span data-ttu-id="5bc16-249">Al massimo uno dei vincoli per un parametro di tipo specificato può essere un tipo di classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-249">At most one constraint for a given type parameter can be a class type.</span></span>

<span data-ttu-id="5bc16-250">Un tipo specificato come un *interface_type* vincolo deve soddisfare le regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-250">A type specified as an *interface_type* constraint must satisfy the following rules:</span></span>

*  <span data-ttu-id="5bc16-251">Il tipo deve essere un tipo di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="5bc16-251">The type must be an interface type.</span></span>
*  <span data-ttu-id="5bc16-252">Un tipo non deve essere specificato più volte un determinato `where` clausola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-252">A type must not be specified more than once in a given `where` clause.</span></span>

<span data-ttu-id="5bc16-253">In entrambi i casi, il vincolo può includere uno dei parametri di tipo del tipo associato o dichiarazione del metodo come parte di un tipo costruito e può comportare il tipo dichiarato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-253">In either case, the constraint can involve any of the type parameters of the associated type or method declaration as part of a constructed type, and can involve the type being declared.</span></span>

<span data-ttu-id="5bc16-254">Qualsiasi tipo di classe o interfaccia specificato come un vincolo del parametro di tipo deve essere accessibile almeno quanto ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)) come il tipo o metodo generico viene dichiarato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-254">Any class or interface type specified as a type parameter constraint must be at least as accessible ([Accessibility constraints](basic-concepts.md#accessibility-constraints)) as the generic type or method being declared.</span></span>

<span data-ttu-id="5bc16-255">Un tipo specificato come un *type_parameter* vincolo deve soddisfare le regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-255">A type specified as a *type_parameter* constraint must satisfy the following rules:</span></span>

*  <span data-ttu-id="5bc16-256">Il tipo deve essere un parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-256">The type must be a type parameter.</span></span>
*  <span data-ttu-id="5bc16-257">Un tipo non deve essere specificato più volte un determinato `where` clausola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-257">A type must not be specified more than once in a given `where` clause.</span></span>

<span data-ttu-id="5bc16-258">Inoltre non deve esistere alcun ciclo nel grafico delle dipendenze dei parametri di tipo, in cui dipendenza è una relazione transitiva definita da:</span><span class="sxs-lookup"><span data-stu-id="5bc16-258">In addition there must be no cycles in the dependency graph of type parameters, where dependency is a transitive relation defined by:</span></span>

*  <span data-ttu-id="5bc16-259">Se un parametro di tipo `T` viene usato come vincolo per il parametro di tipo `S` quindi `S` ***dipende*** `T`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-259">If a type parameter `T` is used as a constraint for type parameter `S` then `S` ***depends on*** `T`.</span></span>
*  <span data-ttu-id="5bc16-260">Se un parametro di tipo `S` dipende da un parametro di tipo `T` e `T` dipende da un parametro di tipo `U` quindi `S` ***dipende*** `U`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-260">If a type parameter `S` depends on a type parameter `T` and `T` depends on a type parameter `U` then `S` ***depends on*** `U`.</span></span>

<span data-ttu-id="5bc16-261">Data questa relazione, è un errore in fase di compilazione per un parametro di tipo a dipendere da se stessa (direttamente o indirettamente).</span><span class="sxs-lookup"><span data-stu-id="5bc16-261">Given this relation, it is a compile-time error for a type parameter to depend on itself (directly or indirectly).</span></span>

<span data-ttu-id="5bc16-262">Tutti i vincoli devono essere coerenti tra i parametri di tipo dipendente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-262">Any constraints must be consistent among dependent type parameters.</span></span> <span data-ttu-id="5bc16-263">Se il parametro di tipo `S` dipende dal parametro di tipo `T` quindi:</span><span class="sxs-lookup"><span data-stu-id="5bc16-263">If type parameter `S` depends on type parameter `T` then:</span></span>

*  <span data-ttu-id="5bc16-264">`T` non deve contenere il vincolo di tipo valore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-264">`T` must not have the value type constraint.</span></span> <span data-ttu-id="5bc16-265">In caso contrario, `T` in modo efficace è sealed in modo `S` sarei stato obbligato a essere dello stesso tipo `T`, eliminando la necessità di due parametri di tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-265">Otherwise, `T` is effectively sealed so `S` would be forced to be the same type as `T`, eliminating the need for two type parameters.</span></span>
*  <span data-ttu-id="5bc16-266">Se `S` ha il vincolo di tipo valore, quindi `T` non deve avere una *class_type* vincolo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-266">If `S` has the value type constraint then `T` must not have a *class_type* constraint.</span></span>
*  <span data-ttu-id="5bc16-267">Se `S` ha un *class_type* vincolo `A` e `T` dispone di un *class_type* vincolo `B` , è necessario eseguire una conversione di identità implicite o conversione da di riferimento `A` al `B` o una conversione implicita del riferimento da `B` a `A`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-267">If `S` has a *class_type* constraint `A` and `T` has a *class_type* constraint `B` then there must be an identity conversion or implicit reference conversion from `A` to `B` or an implicit reference conversion from `B` to `A`.</span></span>
*  <span data-ttu-id="5bc16-268">Se `S` dipende anche dal parametro di tipo `U` e `U` dispone di una *class_type* vincolo `A` e `T` ha una *class_type* vincolo `B` , è necessario eseguire una conversione di identità o una conversione implicita del riferimento da `A` al `B` o una conversione implicita del riferimento da `B` a `A`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-268">If `S` also depends on type parameter `U` and `U` has a *class_type* constraint `A` and `T` has a *class_type* constraint `B` then there must be an identity conversion or implicit reference conversion from `A` to `B` or an implicit reference conversion from `B` to `A`.</span></span>

<span data-ttu-id="5bc16-269">È valido per `S` affinché il vincolo di tipo valore e `T` affinché il vincolo di tipo riferimento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-269">It is valid for `S` to have the value type constraint and `T` to have the reference type constraint.</span></span> <span data-ttu-id="5bc16-270">In modo efficace Ciò limita `T` ai tipi `System.Object`, `System.ValueType`, `System.Enum`e qualsiasi tipo di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="5bc16-270">Effectively this limits `T` to the types `System.Object`, `System.ValueType`, `System.Enum`, and any interface type.</span></span>

<span data-ttu-id="5bc16-271">Se il `where` clausola per un parametro di tipo include un vincolo di costruttore (che ha il formato `new()`), è possibile utilizzare il `new` operatore per creare istanze del tipo ([leespressionidicreazionedell'oggetto](expressions.md#object-creation-expressions)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-271">If the `where` clause for a type parameter includes a constructor constraint (which has the form `new()`), it is possible to use the `new` operator to create instances of the type ([Object creation expressions](expressions.md#object-creation-expressions)).</span></span> <span data-ttu-id="5bc16-272">Qualsiasi tipo di argomento usato per un parametro di tipo con un vincolo di costruttore deve avere un costruttore pubblico senza parametri (questo costruttore esiste in modo implicito per qualsiasi tipo di valore) o deve essere un parametro di tipo con il vincolo di tipo valore o un vincolo constructor (vedere [Vincoli di parametro di tipo](classes.md#type-parameter-constraints) per informazioni dettagliate).</span><span class="sxs-lookup"><span data-stu-id="5bc16-272">Any type argument used for a type parameter with a constructor constraint must have a public parameterless constructor (this constructor implicitly exists for any value type) or be a type parameter having the value type constraint or constructor constraint (see [Type parameter constraints](classes.md#type-parameter-constraints) for details).</span></span>

<span data-ttu-id="5bc16-273">Di seguito è riportati esempi di vincoli:</span><span class="sxs-lookup"><span data-stu-id="5bc16-273">The following are examples of constraints:</span></span>
```csharp
interface IPrintable
{
    void Print();
}

interface IComparable<T>
{
    int CompareTo(T value);
}

interface IKeyProvider<T>
{
    T GetKey();
}

class Printer<T> where T: IPrintable {...}

class SortedList<T> where T: IComparable<T> {...}

class Dictionary<K,V>
    where K: IComparable<K>
    where V: IPrintable, IKeyProvider<K>, new()
{
    ...
}
```

<span data-ttu-id="5bc16-274">L'esempio seguente è in errore perché causa una circolarità nel grafico delle dipendenze dei parametri di tipo:</span><span class="sxs-lookup"><span data-stu-id="5bc16-274">The following example is in error because it causes a circularity in the dependency graph of the type parameters:</span></span>
```csharp
class Circular<S,T>
    where S: T
    where T: S                // Error, circularity in dependency graph
{
    ...
}
```

<span data-ttu-id="5bc16-275">Gli esempi seguenti illustrano altre situazioni non è validi:</span><span class="sxs-lookup"><span data-stu-id="5bc16-275">The following examples illustrate additional invalid situations:</span></span>
```csharp
class Sealed<S,T>
    where S: T
    where T: struct        // Error, T is sealed
{
    ...
}

class A {...}

class B {...}

class Incompat<S,T>
    where S: A, T
    where T: B                // Error, incompatible class-type constraints
{
    ...
}

class StructWithClass<S,T,U>
    where S: struct, T
    where T: U
    where U: A                // Error, A incompatible with struct
{
    ...
}
```

<span data-ttu-id="5bc16-276">Il ***classe base effettiva*** di un parametro di tipo `T` viene definito come segue:</span><span class="sxs-lookup"><span data-stu-id="5bc16-276">The ***effective base class*** of a type parameter `T` is defined as follows:</span></span>

*  <span data-ttu-id="5bc16-277">Se `T` senza vincoli primaria o vincoli del parametro di tipo, la relativa classe base effettiva è `object`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-277">If `T` has no primary constraints or type parameter constraints, its effective base class is `object`.</span></span>
*  <span data-ttu-id="5bc16-278">Se `T` contiene il vincolo di tipo value, la relativa classe base effettiva è `System.ValueType`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-278">If `T` has the value type constraint, its effective base class is `System.ValueType`.</span></span>
*  <span data-ttu-id="5bc16-279">Se `T` ha un *class_type* vincolo `C` ma nessun *type_parameter* vincoli, la relativa classe base effettiva è `C`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-279">If `T` has a *class_type* constraint `C` but no *type_parameter* constraints, its effective base class is `C`.</span></span>
*  <span data-ttu-id="5bc16-280">Se `T` non ha alcun *class_type* vincolo, ma ha uno o più *type_parameter* vincoli, la relativa classe base effettiva è di tipo maggiormente ([conversione è elevato gli operatori](conversions.md#lifted-conversion-operators)) nel set di classi di base effettive della relativa *type_parameter* vincoli.</span><span class="sxs-lookup"><span data-stu-id="5bc16-280">If `T` has no *class_type* constraint but has one or more *type_parameter* constraints, its effective base class is the most encompassed type ([Lifted conversion operators](conversions.md#lifted-conversion-operators)) in the set of effective base classes of its *type_parameter* constraints.</span></span> <span data-ttu-id="5bc16-281">Le regole di uniformità verificare l'esistenza di questo tipo maggiormente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-281">The consistency rules ensure that such a most encompassed type exists.</span></span>
*  <span data-ttu-id="5bc16-282">Se `T` abbia sia un' *class_type* vincolo e uno o più *type_parameter* vincoli, la relativa classe base effettiva è di tipo maggiormente ([conversione è elevato gli operatori](conversions.md#lifted-conversion-operators)) nel set costituito dal *class_type* vincolo del `T` e le classi di base effettive della relativo *type_parameter* vincoli.</span><span class="sxs-lookup"><span data-stu-id="5bc16-282">If `T` has both a *class_type* constraint and one or more *type_parameter* constraints, its effective base class is the most encompassed type ([Lifted conversion operators](conversions.md#lifted-conversion-operators)) in the set consisting of the *class_type* constraint of `T` and the effective base classes of its *type_parameter* constraints.</span></span> <span data-ttu-id="5bc16-283">Le regole di uniformità verificare l'esistenza di questo tipo maggiormente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-283">The consistency rules ensure that such a most encompassed type exists.</span></span>
*  <span data-ttu-id="5bc16-284">Se `T` ha il vincolo di tipo riferimento, ma non *class_type* vincoli, la relativa classe base effettiva è `object`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-284">If `T` has the reference type constraint but no *class_type* constraints, its effective base class is `object`.</span></span>

<span data-ttu-id="5bc16-285">Ai fini di queste regole, se T ha un vincolo `V` che rappresenta un *value_type*, usare invece il tipo di base più specifico della `V` vale a dire una *class_type*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-285">For the purpose of these rules, if T has a constraint `V` that is a *value_type*, use instead the most specific base type of `V` that is a *class_type*.</span></span> <span data-ttu-id="5bc16-286">Questo non può mai verificarsi in un vincolo specificato in modo esplicito, ma può verificarsi quando i vincoli di un metodo generico in modo implicito vengono ereditati da una dichiarazione di metodo esegue l'override o un'implementazione esplicita di un metodo di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="5bc16-286">This can never happen in an explicitly given constraint, but may occur when the constraints of a generic method are implicitly inherited by an overriding method declaration or an explicit implementation of an interface method.</span></span>

<span data-ttu-id="5bc16-287">Queste regole assicurano che la classe base effettiva è sempre un *class_type*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-287">These rules ensure that the effective base class is always a *class_type*.</span></span>

<span data-ttu-id="5bc16-288">Il ***set di interfacce efficaci*** di un parametro di tipo `T` viene definito come segue:</span><span class="sxs-lookup"><span data-stu-id="5bc16-288">The ***effective interface set*** of a type parameter `T` is defined as follows:</span></span>

*  <span data-ttu-id="5bc16-289">Se `T` non ha alcun *secondary_constraints*, il set di interfacce efficaci è vuoto.</span><span class="sxs-lookup"><span data-stu-id="5bc16-289">If `T` has no *secondary_constraints*, its effective interface set is empty.</span></span>
*  <span data-ttu-id="5bc16-290">Se `T` ha *interface_type* vincoli ma nessuna *type_parameter* vincoli, il set di interfacce efficaci è un set di *interface_type* vincoli.</span><span class="sxs-lookup"><span data-stu-id="5bc16-290">If `T` has *interface_type* constraints but no *type_parameter* constraints, its effective interface set is its set of *interface_type* constraints.</span></span>
*  <span data-ttu-id="5bc16-291">Se `T` non ha alcun *interface_type* vincoli, ma ha *type_parameter* vincoli, il relativo set di interfaccia efficace è l'unione dei set di interfacce effettive del relativo *type_ parametro* vincoli.</span><span class="sxs-lookup"><span data-stu-id="5bc16-291">If `T` has no *interface_type* constraints but has *type_parameter* constraints, its effective interface set is the union of the effective interface sets of its *type_parameter* constraints.</span></span>
*  <span data-ttu-id="5bc16-292">Se `T` dispone di entrambe *interface_type* vincoli e *type_parameter* vincoli, il relativo set di interfaccia efficace è l'unione di set di *interface_type* i vincoli e i set di interfacce effettive del relativo *type_parameter* vincoli.</span><span class="sxs-lookup"><span data-stu-id="5bc16-292">If `T` has both *interface_type* constraints and *type_parameter* constraints, its effective interface set is the union of its set of *interface_type* constraints and the effective interface sets of its *type_parameter* constraints.</span></span>

<span data-ttu-id="5bc16-293">È un parametro di tipo ***noto per essere un tipo riferimento*** se ha il vincolo di tipo riferimento o la relativa classe base effettiva non è `object` o `System.ValueType`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-293">A type parameter is ***known to be a reference type*** if it has the reference type constraint or its effective base class is not `object` or `System.ValueType`.</span></span>

<span data-ttu-id="5bc16-294">I valori di un tipo di parametro di tipo vincolato sono utilizzabile per accedere ai membri di istanza in cui è inclusa per i vincoli.</span><span class="sxs-lookup"><span data-stu-id="5bc16-294">Values of a constrained type parameter type can be used to access the instance members implied by the constraints.</span></span> <span data-ttu-id="5bc16-295">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-295">In the example</span></span>
```csharp
interface IPrintable
{
    void Print();
}

class Printer<T> where T: IPrintable
{
    void PrintOne(T x) {
        x.Print();
    }
}
```
<span data-ttu-id="5bc16-296">i metodi della `IPrintable` può essere richiamato direttamente sul `x` poiché `T` è vincolato a sempre implementare `IPrintable`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-296">the methods of `IPrintable` can be invoked directly on `x` because `T` is constrained to always implement `IPrintable`.</span></span>

### <a name="class-body"></a><span data-ttu-id="5bc16-297">Corpo della classe</span><span class="sxs-lookup"><span data-stu-id="5bc16-297">Class body</span></span>

<span data-ttu-id="5bc16-298">Il *class_body* di una classe definisce i membri di tale classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-298">The *class_body* of a class defines the members of that class.</span></span>

```antlr
class_body
    : '{' class_member_declaration* '}'
    ;
```

## <a name="partial-types"></a><span data-ttu-id="5bc16-299">Tipi parziali</span><span class="sxs-lookup"><span data-stu-id="5bc16-299">Partial types</span></span>

<span data-ttu-id="5bc16-300">Una dichiarazione del tipo può essere suddivisa in più ***dichiarazioni di tipi parziali***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-300">A type declaration can be split across multiple ***partial type declarations***.</span></span> <span data-ttu-id="5bc16-301">La dichiarazione del tipo viene costruita dalle relative parti seguendo le regole in questa sezione nuovamente viene considerato come una singola dichiarazione fino alla fine dell'elaborazione in fase di compilazione e fase di esecuzione del programma.</span><span class="sxs-lookup"><span data-stu-id="5bc16-301">The type declaration is constructed from its parts by following the rules in this section, whereupon it is treated as a single declaration during the remainder of the compile-time and run-time processing of the program.</span></span>

<span data-ttu-id="5bc16-302">Oggetto *class_declaration*, *struct_declaration* oppure *interface_declaration* rappresenta una dichiarazione di tipo parziale, se include un `partial` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-302">A *class_declaration*, *struct_declaration* or *interface_declaration* represents a partial type declaration if it includes a `partial` modifier.</span></span> <span data-ttu-id="5bc16-303">`partial` non è una parola chiave e funge solo da un modificatore se viene visualizzato immediatamente prima che una delle parole chiave `class`, `struct` oppure `interface` in una dichiarazione del tipo, o prima del tipo `void` in una dichiarazione di metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-303">`partial` is not a keyword, and only acts as a modifier if it appears immediately before one of the keywords `class`, `struct` or `interface` in a type declaration, or before the type `void` in a method declaration.</span></span> <span data-ttu-id="5bc16-304">In altri contesti può essere utilizzato come identificatore normale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-304">In other contexts it can be used as a normal identifier.</span></span>

<span data-ttu-id="5bc16-305">Ogni parte di una dichiarazione di tipo parziale deve includere un `partial` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-305">Each part of a partial type declaration must include a `partial` modifier.</span></span> <span data-ttu-id="5bc16-306">Deve avere lo stesso nome e dichiarato nella stesso spazio dei nomi o dichiarazione di tipo come le altre parti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-306">It must have the same name  and be declared in the same namespace or type declaration as the other parts.</span></span> <span data-ttu-id="5bc16-307">Il `partial` modificatore indica che altre parti della dichiarazione del tipo potrebbero esistere in un' posizione, ma l'esistenza di tali parti aggiuntive non è un requisito; è valido per un tipo con una sola dichiarazione includere il `partial` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-307">The `partial` modifier indicates that additional parts of the type declaration may exist elsewhere, but the existence of such additional parts is not a requirement; it is valid for a type with a single declaration to include the `partial` modifier.</span></span>

<span data-ttu-id="5bc16-308">Tutte le parti di un tipo parziale devono essere compilate insieme in modo che le parti possono essere unite in fase di compilazione in una dichiarazione di tipo singolo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-308">All parts of a partial type must be compiled together such that the parts can be merged at compile-time into a single type declaration.</span></span> <span data-ttu-id="5bc16-309">In particolare i tipi parziali non consentono tipi già compilati da estendere.</span><span class="sxs-lookup"><span data-stu-id="5bc16-309">Partial types specifically do not allow already compiled types to be extended.</span></span>

<span data-ttu-id="5bc16-310">I tipi annidati possono essere dichiarati in più parti usando la `partial` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-310">Nested types may be declared in multiple parts by using the `partial` modifier.</span></span> <span data-ttu-id="5bc16-311">In genere, il tipo contenitore è dichiarato mediante `partial` che ogni parte del tipo annidato dichiarato in un'altra parte del tipo contenitore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-311">Typically, the containing type is declared using `partial` as well, and each part of the nested type is declared in a different part of the containing type.</span></span>

<span data-ttu-id="5bc16-312">Il `partial` modificatore non è consentito in dichiarazioni di delegato o enumerazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-312">The `partial` modifier is not permitted on delegate or enum declarations.</span></span>

### <a name="attributes"></a><span data-ttu-id="5bc16-313">Attributi</span><span class="sxs-lookup"><span data-stu-id="5bc16-313">Attributes</span></span>

<span data-ttu-id="5bc16-314">Gli attributi di un tipo parziale sono determinati dalla combinazione, in un ordine non specificato, gli attributi di ognuna delle parti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-314">The attributes of a partial type are determined by combining, in an unspecified order, the attributes of each of the parts.</span></span> <span data-ttu-id="5bc16-315">Se un attributo viene inserito in più parti, equivale a specificare l'attributo più volte sul tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-315">If an attribute is placed on multiple parts, it is equivalent to specifying the attribute multiple times on the type.</span></span> <span data-ttu-id="5bc16-316">Ad esempio, le due parti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-316">For example, the two parts:</span></span>

```csharp
[Attr1, Attr2("hello")]
partial class A {}

[Attr3, Attr2("goodbye")]
partial class A {}
```
<span data-ttu-id="5bc16-317">sono equivalenti a una dichiarazione, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5bc16-317">are equivalent to a declaration such as:</span></span>
```csharp
[Attr1, Attr2("hello"), Attr3, Attr2("goodbye")]
class A {}
```

<span data-ttu-id="5bc16-318">Gli attributi dei parametri di tipo combinano in modo analogo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-318">Attributes on type parameters combine in a similar fashion.</span></span>

### <a name="modifiers"></a><span data-ttu-id="5bc16-319">Modificatori</span><span class="sxs-lookup"><span data-stu-id="5bc16-319">Modifiers</span></span>

<span data-ttu-id="5bc16-320">Quando una dichiarazione di tipo parziale include una specifica di accessibilità (il `public`, `protected`, `internal`, e `private` modificatori) deve essere coerente con tutte le altre parti che includono una specifica di accessibilità.</span><span class="sxs-lookup"><span data-stu-id="5bc16-320">When a partial type declaration includes an accessibility specification (the `public`, `protected`, `internal`, and `private` modifiers) it must agree with all other parts that include an accessibility specification.</span></span> <span data-ttu-id="5bc16-321">Se nessuna parte di un tipo parziale include una specifica di accessibilità, il tipo viene specificato l'accessibilità predefinita appropriata ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-321">If no part of a partial type includes an accessibility specification, the type is given the appropriate default accessibility ([Declared accessibility](basic-concepts.md#declared-accessibility)).</span></span>

<span data-ttu-id="5bc16-322">Se uno o più dichiarazioni parziali di un tipo annidato includono un' `new` modificatore, non viene segnalato alcun messaggio di avviso se il tipo annidato nasconde un membro ereditato ([nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-322">If one or more partial declarations of a nested type include a `new` modifier, no warning is reported if the nested type hides an inherited member ([Hiding through inheritance](basic-concepts.md#hiding-through-inheritance)).</span></span>

<span data-ttu-id="5bc16-323">Se uno o più dichiarazioni parziali di una classe includono un' `abstract` modificatore, la classe verrà considerata astratta ([classi astratte](classes.md#abstract-classes)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-323">If one or more partial declarations of a class include an `abstract` modifier, the class is considered abstract ([Abstract classes](classes.md#abstract-classes)).</span></span> <span data-ttu-id="5bc16-324">In caso contrario, la classe è considerata non astratte.</span><span class="sxs-lookup"><span data-stu-id="5bc16-324">Otherwise, the class is considered non-abstract.</span></span>

<span data-ttu-id="5bc16-325">Se uno o più dichiarazioni parziali di una classe includono un' `sealed` modificatore, la classe verrà considerata sealed ([le classi Sealed](classes.md#sealed-classes)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-325">If one or more partial declarations of a class include a `sealed` modifier, the class is considered sealed ([Sealed classes](classes.md#sealed-classes)).</span></span> <span data-ttu-id="5bc16-326">In caso contrario, viene considerata la classe non sealed.</span><span class="sxs-lookup"><span data-stu-id="5bc16-326">Otherwise, the class is considered unsealed.</span></span>

<span data-ttu-id="5bc16-327">Si noti che una classe non può essere contemporaneamente abstract e sealed.</span><span class="sxs-lookup"><span data-stu-id="5bc16-327">Note that a class cannot be both abstract and sealed.</span></span>

<span data-ttu-id="5bc16-328">Quando la `unsafe` modificatore viene usato in una dichiarazione di tipo parziale, solo tale parte viene considerata un contesto unsafe ([contesti non sicuri](unsafe-code.md#unsafe-contexts)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-328">When the `unsafe` modifier is used on a partial type declaration, only that particular part is considered an unsafe context ([Unsafe contexts](unsafe-code.md#unsafe-contexts)).</span></span>

### <a name="type-parameters-and-constraints"></a><span data-ttu-id="5bc16-329">I parametri di tipo e vincoli</span><span class="sxs-lookup"><span data-stu-id="5bc16-329">Type parameters and constraints</span></span>

<span data-ttu-id="5bc16-330">Se un tipo generico è dichiarato in più parti, ogni parte deve dichiarare i parametri di tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-330">If a generic type is declared in multiple parts, each part must state the type parameters.</span></span> <span data-ttu-id="5bc16-331">Ogni parte deve avere lo stesso numero di parametri di tipo e lo stesso nome per ogni parametro di tipo, in ordine.</span><span class="sxs-lookup"><span data-stu-id="5bc16-331">Each part must have the same number of type parameters, and the same name for each type parameter, in order.</span></span>

<span data-ttu-id="5bc16-332">Quando una dichiarazione parziale di tipo generico include i vincoli (`where` clausole), i vincoli devono essere coerenti con tutte le altre parti che includono vincoli.</span><span class="sxs-lookup"><span data-stu-id="5bc16-332">When a partial generic type declaration includes constraints (`where` clauses), the constraints must agree with all other parts that include constraints.</span></span> <span data-ttu-id="5bc16-333">In particolare, ogni parte che include i vincoli deve avere vincoli per lo stesso set di parametri di tipo e per ogni parametro di tipo i set di principale, secondario e i vincoli del costruttore devono essere equivalente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-333">Specifically, each part that includes constraints must have constraints for the same set of type parameters, and for each type parameter the sets of primary, secondary, and constructor constraints must be equivalent.</span></span> <span data-ttu-id="5bc16-334">Due set di vincoli sono equivalenti se contengono gli stessi membri.</span><span class="sxs-lookup"><span data-stu-id="5bc16-334">Two sets of constraints are equivalent if they contain the same members.</span></span> <span data-ttu-id="5bc16-335">Se nessuna parte di un tipo generico parziale specifica i vincoli del parametro, il tipo di parametri vengono considerati non vincolato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-335">If no part of a partial generic type specifies type parameter constraints, the type parameters are considered unconstrained.</span></span>

<span data-ttu-id="5bc16-336">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-336">The example</span></span>
```csharp
partial class Dictionary<K,V>
    where K: IComparable<K>
    where V: IKeyProvider<K>, IPersistable
{
    ...
}

partial class Dictionary<K,V>
    where V: IPersistable, IKeyProvider<K>
    where K: IComparable<K>
{
    ...
}

partial class Dictionary<K,V>
{
    ...
}
```
<span data-ttu-id="5bc16-337">è corretto perché le parti che includono vincoli (i primi due) in modo efficace specificano stesso set di principale, secondario e i vincoli del costruttore per lo stesso set di parametri di tipo, rispettivamente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-337">is correct because those parts that include constraints (the first two) effectively specify the same set of primary, secondary, and constructor constraints for the same set of type parameters, respectively.</span></span>

### <a name="base-class"></a><span data-ttu-id="5bc16-338">Classe base</span><span class="sxs-lookup"><span data-stu-id="5bc16-338">Base class</span></span>

<span data-ttu-id="5bc16-339">Quando una dichiarazione di classe parziale include una specifica classe di base, deve essere coerente in tutte le altre parti che includono una specifica classe di base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-339">When a partial class declaration includes a base class specification it must agree with all other parts that include a base class specification.</span></span> <span data-ttu-id="5bc16-340">Se nessuna parte di una classe parziale include una specifica classe di base, la classe di base diventa `System.Object` ([classi di Base](classes.md#base-classes)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-340">If no part of a partial class includes a base class specification, the base class becomes `System.Object` ([Base classes](classes.md#base-classes)).</span></span>

### <a name="base-interfaces"></a><span data-ttu-id="5bc16-341">Interfacce di base</span><span class="sxs-lookup"><span data-stu-id="5bc16-341">Base interfaces</span></span>

<span data-ttu-id="5bc16-342">Il set di interfacce di base per un tipo dichiarato in più parti è l'unione delle interfacce di base specificato in ogni parte.</span><span class="sxs-lookup"><span data-stu-id="5bc16-342">The set of base interfaces for a type declared in multiple parts is the union of the base interfaces specified on each part.</span></span> <span data-ttu-id="5bc16-343">Una determinata interfaccia di base potrebbe essere denominata solo una volta in ogni parte, ma è consentito per più parti denominare le stesse interfacce di base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-343">A particular base interface may only be named once on each part, but it is permitted for multiple parts to name the same base interface(s).</span></span> <span data-ttu-id="5bc16-344">Deve essere presente solo un'implementazione dei membri di qualsiasi interfaccia di base specificato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-344">There must only be one implementation of the members of any given base interface.</span></span>

<span data-ttu-id="5bc16-345">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-345">In the example</span></span>
```csharp
partial class C: IA, IB {...}

partial class C: IC {...}

partial class C: IA, IB {...}
```
<span data-ttu-id="5bc16-346">il set di interfacce di base per la classe `C` viene `IA`, `IB`, e `IC`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-346">the set of base interfaces for class `C` is `IA`, `IB`, and `IC`.</span></span>

<span data-ttu-id="5bc16-347">In genere, ogni parte fornisce un'implementazione delle interfacce dichiarate su tale parte; Tuttavia, questo non è un requisito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-347">Typically, each part provides an implementation of the interface(s) declared on that part; however, this is not a requirement.</span></span> <span data-ttu-id="5bc16-348">Una parte può fornire l'implementazione di un'interfaccia dichiarata in una parte diversa:</span><span class="sxs-lookup"><span data-stu-id="5bc16-348">A part may provide the implementation for an interface declared on a different part:</span></span>
```csharp
partial class X
{
    int IComparable.CompareTo(object o) {...}
}

partial class X: IComparable
{
    ...
}
```

### <a name="members"></a><span data-ttu-id="5bc16-349">Membri</span><span class="sxs-lookup"><span data-stu-id="5bc16-349">Members</span></span>

<span data-ttu-id="5bc16-350">Fatta eccezione per i metodi parziali ([metodi parziali](classes.md#partial-methods)), il set di membri di un tipo dichiarato in più parti è semplicemente l'unione del set di membri dichiarati in ogni parte.</span><span class="sxs-lookup"><span data-stu-id="5bc16-350">With the exception of partial methods ([Partial methods](classes.md#partial-methods)), the set of members of a type declared in multiple parts is simply the union of the set of members declared in each part.</span></span> <span data-ttu-id="5bc16-351">I corpi di tutte le parti della dichiarazione del tipo condividono lo stesso spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)) e l'ambito di ogni membro ([ambiti](basic-concepts.md#scopes)) estende i corpi di tutte le parti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-351">The bodies of all parts of the type declaration share the same declaration space ([Declarations](basic-concepts.md#declarations)), and the scope of each member ([Scopes](basic-concepts.md#scopes)) extends to the bodies of all the parts.</span></span> <span data-ttu-id="5bc16-352">Il dominio di accessibilità di qualsiasi membro include sempre tutte le parti del tipo che lo contiene. un `private` membro dichiarato in una parte è possibile accedere liberamente da un'altra parte.</span><span class="sxs-lookup"><span data-stu-id="5bc16-352">The accessibility domain of any member always includes all the parts of the enclosing type; a `private` member declared in one part is freely accessible from another part.</span></span> <span data-ttu-id="5bc16-353">È un errore in fase di compilazione dichiarano lo stesso membro in più di una parte del tipo, a meno che tale membro è un tipo con il `partial` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-353">It is a compile-time error to declare the same member in more than one part of the type, unless that member is a type with the `partial` modifier.</span></span>

```csharp
partial class A
{
    int x;                     // Error, cannot declare x more than once

    partial class Inner        // Ok, Inner is a partial type
    {
        int y;
    }
}

partial class A
{
    int x;                     // Error, cannot declare x more than once

    partial class Inner        // Ok, Inner is a partial type
    {
        int z;
    }
}
```

<span data-ttu-id="5bc16-354">L'ordinamento dei membri all'interno di un tipo è raramente significativa al codice c#, ma potrebbe essere significativo quando eseguono l'interfacciamento con altri linguaggi e ambienti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-354">The ordering of members within a type is rarely significant to C# code, but may be significant when interfacing with other languages and environments.</span></span> <span data-ttu-id="5bc16-355">In questi casi, è definito l'ordinamento dei membri all'interno di un tipo dichiarato in più parti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-355">In these cases, the ordering of members within a type declared in multiple parts is undefined.</span></span>

### <a name="partial-methods"></a><span data-ttu-id="5bc16-356">Metodi parziali</span><span class="sxs-lookup"><span data-stu-id="5bc16-356">Partial methods</span></span>

<span data-ttu-id="5bc16-357">Metodi parziali possono essere definiti in una parte di una dichiarazione del tipo e implementati in un altro.</span><span class="sxs-lookup"><span data-stu-id="5bc16-357">Partial methods can be defined in one part of a type declaration and implemented in another.</span></span> <span data-ttu-id="5bc16-358">L'implementazione è facoltativa. Se nessuna parte implementa il metodo parziale, la dichiarazione di metodo parziale e le relative chiamate vengono rimossi dalla dichiarazione del tipo risultante dalla combinazione delle parti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-358">The implementation is optional; if no part implements the partial method, the partial method declaration and all calls to it are removed from the type declaration resulting from the combination of the parts.</span></span>

<span data-ttu-id="5bc16-359">I metodi parziali non è possibile definire i modificatori di accesso, ma sono implicitamente `private`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-359">Partial methods cannot define access modifiers, but are implicitly `private`.</span></span> <span data-ttu-id="5bc16-360">Il tipo restituito deve essere `void`, e i relativi parametri non possono avere il `out` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-360">Their return type must be `void`, and their parameters cannot have the `out` modifier.</span></span> <span data-ttu-id="5bc16-361">L'identificatore `partial` viene riconosciuto come parola chiave speciale in una dichiarazione di metodo solo se viene visualizzato subito prima di `void` tipo; in caso contrario, può essere utilizzato come un identificatore normale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-361">The identifier `partial` is recognized as a special keyword in a method declaration only if it appears right before the `void` type; otherwise it can be used as a normal identifier.</span></span> <span data-ttu-id="5bc16-362">Un metodo parziale non può implementare in modo esplicito i metodi di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="5bc16-362">A partial method cannot explicitly implement interface methods.</span></span>

<span data-ttu-id="5bc16-363">Esistono due tipi di dichiarazioni di metodo parziale: Se il corpo della dichiarazione del metodo è un punto e virgola, la dichiarazione viene considerata come un ***definizione di dichiarazione di metodo parziale***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-363">There are two kinds of partial method declarations: If the body of the method declaration is a semicolon, the declaration is said to be a ***defining partial method declaration***.</span></span> <span data-ttu-id="5bc16-364">Se il corpo viene fornito come un *blocco*, la dichiarazione viene definita un' ***dichiarazione di metodo parziale che implementa***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-364">If the body is given as a *block*, the declaration is said to be an ***implementing partial method declaration***.</span></span> <span data-ttu-id="5bc16-365">Tra le parti di una dichiarazione del tipo può essere presente solo una dichiarazione di metodo parziale con una determinata firma che definisce e può essere presente solo una dichiarazione di metodo parziale con una determinata firma di implementazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-365">Across the parts of a type declaration there can be only one defining partial method declaration with a given signature, and there can be only one implementing partial method declaration with a given signature.</span></span> <span data-ttu-id="5bc16-366">Se una dichiarazione di metodo parziale implementazione viene assegnata, una corrispondente definizione di dichiarazione di metodo parziale deve essere presente e devono corrispondere le dichiarazioni come specificato di seguito:</span><span class="sxs-lookup"><span data-stu-id="5bc16-366">If an implementing partial method declaration is given, a corresponding defining partial method declaration must exist, and the declarations must match as specified in the following:</span></span>

* <span data-ttu-id="5bc16-367">Le dichiarazioni devono avere gli stessi modificatori (anche se non necessariamente nello stesso ordine), nome del metodo, numero di parametri di tipo e numero di parametri.</span><span class="sxs-lookup"><span data-stu-id="5bc16-367">The declarations must have the same modifiers (although not necessarily in the same order), method name, number of type parameters and number of parameters.</span></span>
* <span data-ttu-id="5bc16-368">I parametri corrispondenti nelle dichiarazioni devono avere gli stessi modificatori (anche se non necessariamente nello stesso ordine) e gli stessi tipi (ad eccezione delle differenze nei nomi dei parametri di tipo).</span><span class="sxs-lookup"><span data-stu-id="5bc16-368">Corresponding parameters in the declarations must have the same modifiers (although not necessarily in the same order) and the same types (modulo differences in type parameter names).</span></span>
* <span data-ttu-id="5bc16-369">Parametri di tipo corrispondenti nelle dichiarazioni di devono avere gli stessi vincoli (ad eccezione delle differenze nei nomi dei parametri di tipo).</span><span class="sxs-lookup"><span data-stu-id="5bc16-369">Corresponding type parameters in the declarations must have the same constraints (modulo differences in type parameter names).</span></span>

<span data-ttu-id="5bc16-370">Una dichiarazione di metodo parziale implementazione visualizzabili nella stessa parte come la dichiarazione di metodo parziale definizione corrispondente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-370">An implementing partial method declaration can appear in the same part as the corresponding defining partial method declaration.</span></span>

<span data-ttu-id="5bc16-371">Solo un metodo di definizione parziale fa parte della risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="5bc16-371">Only a defining partial method participates in overload resolution.</span></span> <span data-ttu-id="5bc16-372">Di conseguenza, se si specifica una dichiarazione di implementazione, espressioni di chiamata possono essere risolti in chiamate del metodo parziale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-372">Thus, whether or not an implementing declaration is given, invocation expressions may resolve to invocations of the partial method.</span></span> <span data-ttu-id="5bc16-373">Perché restituisce sempre un metodo parziale `void`, tali espressioni di chiamata saranno sempre le istruzioni di espressione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-373">Because a partial method always returns `void`, such invocation expressions will always be expression statements.</span></span> <span data-ttu-id="5bc16-374">Inoltre, poiché un metodo parziale è implicitamente `private`, tali istruzioni verranno sempre eseguita all'interno di una delle parti della dichiarazione del tipo all'interno del quale il metodo parziale viene dichiarato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-374">Furthermore, because a partial method is implicitly `private`, such statements will always occur within one of the parts of the type declaration within which the partial method is declared.</span></span>

<span data-ttu-id="5bc16-375">Se nessuna parte di una dichiarazione di tipo parziale contiene una dichiarazione di implementazione per un determinato metodo parziale, qualsiasi istruzione di espressione richiamarlo semplicemente viene rimosso dalla dichiarazione di tipo combinato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-375">If no part of a partial type declaration contains an implementing declaration for a given partial method, any expression statement invoking it is simply removed from the combined type declaration.</span></span> <span data-ttu-id="5bc16-376">Pertanto, l'espressione di chiamata, incluse tutte le espressioni che lo costituiscono, ha effetto in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-376">Thus the invocation expression, including any constituent expressions, has no effect at run-time.</span></span> <span data-ttu-id="5bc16-377">Il metodo parziale viene anche rimossa e non sarà un membro della dichiarazione del tipo combinato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-377">The partial method itself is also removed and will not be a member of the combined type declaration.</span></span>

<span data-ttu-id="5bc16-378">In presenza di una dichiarazione di implementazione per un determinato metodo parziale, le chiamate dei metodi parziali vengono mantenute.</span><span class="sxs-lookup"><span data-stu-id="5bc16-378">If an implementing declaration exist for a given partial method, the invocations of the partial methods are retained.</span></span> <span data-ttu-id="5bc16-379">Il metodo parziale dà luogo a una dichiarazione di metodo simile alla dichiarazione di metodo parziale implementazione ad eccezione dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-379">The partial method gives rise to a method declaration similar to the implementing partial method declaration except for the following:</span></span>

* <span data-ttu-id="5bc16-380">Il `partial` modificatore non è incluso</span><span class="sxs-lookup"><span data-stu-id="5bc16-380">The `partial` modifier is not included</span></span>
* <span data-ttu-id="5bc16-381">Gli attributi nella dichiarazione del metodo risultanti sono gli attributi combinati di definizione e la dichiarazione di metodo parziale implementazione in ordine non specificato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-381">The attributes in the resulting method declaration are the combined attributes of the defining and the implementing partial method declaration in unspecified order.</span></span> <span data-ttu-id="5bc16-382">Non vengono eliminati i duplicati.</span><span class="sxs-lookup"><span data-stu-id="5bc16-382">Duplicates are not removed.</span></span>
* <span data-ttu-id="5bc16-383">Gli attributi dei parametri della dichiarazione di metodo risultante sono gli attributi dei parametri corrispondenti della definizione e la dichiarazione di metodo parziale implementazione combinati in ordine non specificato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-383">The attributes on the parameters of the resulting method declaration are the combined attributes of the corresponding parameters of the defining and the implementing partial method declaration in unspecified order.</span></span> <span data-ttu-id="5bc16-384">Non vengono eliminati i duplicati.</span><span class="sxs-lookup"><span data-stu-id="5bc16-384">Duplicates are not removed.</span></span>

<span data-ttu-id="5bc16-385">Se una dichiarazione di definizione, ma non una dichiarazione di implementazione non viene specificato per un metodo parziale M, si applicano le restrizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-385">If a defining declaration but not an implementing declaration is given for a partial method M, the following restrictions apply:</span></span>

* <span data-ttu-id="5bc16-386">Tratta di un errore in fase di compilazione per creare un delegato al metodo ([espressioni di creazione delegato](expressions.md#delegate-creation-expressions)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-386">It is a compile-time error to create a delegate to method ([Delegate creation expressions](expressions.md#delegate-creation-expressions)).</span></span>
* <span data-ttu-id="5bc16-387">Tratta di un errore in fase di compilazione per fare riferimento a `M` all'interno di una funzione anonima convertita in un tipo di struttura ad albero di espressione ([valutazione delle conversioni di tipi di albero delle espressioni funzioni anonime](conversions.md#evaluation-of-anonymous-function-conversions-to-expression-tree-types)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-387">It is a compile-time error to refer to `M` inside an anonymous function that is converted to an expression tree type ([Evaluation of anonymous function conversions to expression tree types](conversions.md#evaluation-of-anonymous-function-conversions-to-expression-tree-types)).</span></span>
* <span data-ttu-id="5bc16-388">Che si verificano nell'ambito di una chiamata di espressioni `M` interferisca con lo stato di assegnazione certa ([assegnazione certa](variables.md#definite-assignment)), che può potenzialmente causare errori in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-388">Expressions occurring as part of an invocation of `M` do not affect the definite assignment state ([Definite assignment](variables.md#definite-assignment)), which can potentially lead to compile-time errors.</span></span>
* <span data-ttu-id="5bc16-389">`M` non può essere il punto di ingresso per un'applicazione ([avvio dell'applicazione](basic-concepts.md#application-startup)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-389">`M` cannot be the entry point for an application ([Application Startup](basic-concepts.md#application-startup)).</span></span>

<span data-ttu-id="5bc16-390">I metodi parziali sono utili per consentire a una parte di una dichiarazione di tipo per personalizzare il comportamento di un'altra parte, ad esempio, uno che viene generato da uno strumento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-390">Partial methods are useful for allowing one part of a type declaration to customize the behavior of another part, e.g., one that is generated by a tool.</span></span> <span data-ttu-id="5bc16-391">Si consideri la dichiarazione di classe parziale seguente:</span><span class="sxs-lookup"><span data-stu-id="5bc16-391">Consider the following partial class declaration:</span></span>
```csharp
partial class Customer
{
    string name;

    public string Name {
        get { return name; }
        set {
            OnNameChanging(value);
            name = value;
            OnNameChanged();
        }

    }

    partial void OnNameChanging(string newName);

    partial void OnNameChanged();
}
```

<span data-ttu-id="5bc16-392">Se questa classe viene compilata senza altre parti, le dichiarazioni di metodo parziale definizione e le chiamate verranno rimossi e la dichiarazione di classe combinata risultante sarà equivalente alla seguente:</span><span class="sxs-lookup"><span data-stu-id="5bc16-392">If this class is compiled without any other parts, the defining partial method declarations and their invocations will be removed, and the resulting combined class declaration will be equivalent to the following:</span></span>
```csharp
class Customer
{
    string name;

    public string Name {
        get { return name; }
        set { name = value; }
    }
}
```

<span data-ttu-id="5bc16-393">Si supponga che un'altra parte viene fornita, tuttavia, che fornisce le dichiarazioni di implementazione dei metodi parziali:</span><span class="sxs-lookup"><span data-stu-id="5bc16-393">Assume that another part is given, however, which provides implementing declarations of the partial methods:</span></span>
```csharp
partial class Customer
{
    partial void OnNameChanging(string newName)
    {
        Console.WriteLine("Changing " + name + " to " + newName);
    }

    partial void OnNameChanged()
    {
        Console.WriteLine("Changed to " + name);
    }
}
```

<span data-ttu-id="5bc16-394">Quindi la dichiarazione di classe combinata risultante sarà equivalente alla seguente:</span><span class="sxs-lookup"><span data-stu-id="5bc16-394">Then the resulting combined class declaration will be equivalent to the following:</span></span>
```csharp
class Customer
{
    string name;

    public string Name {
        get { return name; }
        set {
            OnNameChanging(value);
            name = value;
            OnNameChanged();
        }

    }

    void OnNameChanging(string newName)
    {
        Console.WriteLine("Changing " + name + " to " + newName);
    }

    void OnNameChanged()
    {
        Console.WriteLine("Changed to " + name);
    }
}
```

### <a name="name-binding"></a><span data-ttu-id="5bc16-395">Associazione del nome</span><span class="sxs-lookup"><span data-stu-id="5bc16-395">Name binding</span></span>

<span data-ttu-id="5bc16-396">Anche se ogni parte di un tipo extensible deve essere dichiarato all'interno dello stesso spazio dei nomi, le parti vengono in genere scritti all'interno delle dichiarazioni dello spazio dei nomi diversi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-396">Although each part of an extensible type must be declared within the same namespace, the parts are typically written within different namespace declarations.</span></span> <span data-ttu-id="5bc16-397">Di conseguenza, diverse `using` direttive ([direttive Using](namespaces.md#using-directives)) possono essere presenti per ciascuna parte.</span><span class="sxs-lookup"><span data-stu-id="5bc16-397">Thus, different `using` directives ([Using directives](namespaces.md#using-directives)) may be present for each part.</span></span> <span data-ttu-id="5bc16-398">Durante l'interpretazione di nomi semplici ([inferenza del tipo](expressions.md#type-inference)) all'interno di una parte, solo il `using` vengono considerate le direttive di dichiarazioni di spazio dei nomi che contengono tale parte.</span><span class="sxs-lookup"><span data-stu-id="5bc16-398">When interpreting simple names ([Type inference](expressions.md#type-inference)) within one part, only the `using` directives of the namespace declaration(s) enclosing that part are considered.</span></span> <span data-ttu-id="5bc16-399">Questo può comportare lo stesso identificatore avere significati diversi in diverse parti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-399">This may result in the same identifier having different meanings in different parts:</span></span>
```csharp
namespace N
{
    using List = System.Collections.ArrayList;

    partial class A
    {
        List x;                // x has type System.Collections.ArrayList
    }
}

namespace N
{
    using List = Widgets.LinkedList;

    partial class A
    {
        List y;                // y has type Widgets.LinkedList
    }
}
```

## <a name="class-members"></a><span data-ttu-id="5bc16-400">Membri della classe</span><span class="sxs-lookup"><span data-stu-id="5bc16-400">Class members</span></span>

<span data-ttu-id="5bc16-401">I membri di una classe sono costituiti da membri introdotti dalla relativa *class_member_declaration*s e i membri ereditati dalla classe di base diretta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-401">The members of a class consist of the members introduced by its *class_member_declaration*s and the members inherited from the direct base class.</span></span>

```antlr
class_member_declaration
    : constant_declaration
    | field_declaration
    | method_declaration
    | property_declaration
    | event_declaration
    | indexer_declaration
    | operator_declaration
    | constructor_declaration
    | destructor_declaration
    | static_constructor_declaration
    | type_declaration
    ;
```

<span data-ttu-id="5bc16-402">I membri di un tipo di classe sono suddivisi nelle categorie seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-402">The members of a class type are divided into the following categories:</span></span>

*  <span data-ttu-id="5bc16-403">Costanti, che rappresentano i valori costanti associati alla classe ([costanti](classes.md#constants)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-403">Constants, which represent constant values associated with the class ([Constants](classes.md#constants)).</span></span>
*  <span data-ttu-id="5bc16-404">I campi, le variabili della classe ([campi](classes.md#fields)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-404">Fields, which are the variables of the class ([Fields](classes.md#fields)).</span></span>
*  <span data-ttu-id="5bc16-405">Metodi che implementano i calcoli e azioni che possono essere eseguite dalla classe ([metodi](classes.md#methods)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-405">Methods, which implement the computations and actions that can be performed by the class ([Methods](classes.md#methods)).</span></span>
*  <span data-ttu-id="5bc16-406">Proprietà, che definiscono le caratteristiche denominate e le azioni associate alla lettura e scrittura di tali caratteristiche ([proprietà](classes.md#properties)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-406">Properties, which define named characteristics and the actions associated with reading and writing those characteristics ([Properties](classes.md#properties)).</span></span>
*  <span data-ttu-id="5bc16-407">Gli eventi, che definiscono le notifiche che possono essere generate dalla classe ([eventi](classes.md#events)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-407">Events, which define notifications that can be generated by the class ([Events](classes.md#events)).</span></span>
*  <span data-ttu-id="5bc16-408">Gli indicizzatori, che consentono di istanze della classe per cui l'indicizzazione nello stesso modo (sintatticamente) sotto forma di matrici ([indicizzatori](classes.md#indexers)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-408">Indexers, which permit instances of the class to be indexed in the same way (syntactically) as arrays ([Indexers](classes.md#indexers)).</span></span>
*  <span data-ttu-id="5bc16-409">Operatori, ovvero definiscono gli operatori di espressione che possono essere applicati a istanze della classe ([operatori](classes.md#operators)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-409">Operators, which define the expression operators that can be applied to instances of the class ([Operators](classes.md#operators)).</span></span>
*  <span data-ttu-id="5bc16-410">Costruttori di istanza, che implementano le azioni necessarie per inizializzare istanze della classe ([costruttori di istanze](classes.md#instance-constructors))</span><span class="sxs-lookup"><span data-stu-id="5bc16-410">Instance constructors, which implement the actions required to initialize instances of the class ([Instance constructors](classes.md#instance-constructors))</span></span>
*  <span data-ttu-id="5bc16-411">I distruttori, che implementano le azioni da eseguire prima dell'eliminazione permanente delle istanze della classe ([distruttori](classes.md#destructors)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-411">Destructors, which implement the actions to be performed before instances of the class are permanently discarded ([Destructors](classes.md#destructors)).</span></span>
*  <span data-ttu-id="5bc16-412">I costruttori statici, che implementano le azioni necessarie per inizializzare la classe stessa ([costruttori statici](classes.md#static-constructors)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-412">Static constructors, which implement the actions required to initialize the class itself ([Static constructors](classes.md#static-constructors)).</span></span>
*  <span data-ttu-id="5bc16-413">I tipi, che rappresentano i tipi che sono locali rispetto alla classe ([i tipi annidati](classes.md#nested-types)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-413">Types, which represent the types that are local to the class ([Nested types](classes.md#nested-types)).</span></span>

<span data-ttu-id="5bc16-414">I membri che contengono codice eseguibile sono noti collettivamente come le *membri funzione* del tipo di classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-414">Members that can contain executable code are collectively known as the *function members* of the class type.</span></span> <span data-ttu-id="5bc16-415">I membri di un tipo classe sono metodi, proprietà, eventi, indicizzatori, operatori, costruttori di istanze, distruttori e i costruttori statici del tipo classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-415">The function members of a class type are the methods, properties, events, indexers, operators, instance constructors,  destructors, and static constructors of that class type.</span></span>

<span data-ttu-id="5bc16-416">Oggetto *class_declaration* crea un nuovo spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)) e il *class_member_declaration*immediatamente contenuti dal *classe _declaration* introducono nuovi membri in tale spazio di dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-416">A *class_declaration* creates a new declaration space ([Declarations](basic-concepts.md#declarations)), and the *class_member_declaration*s immediately contained by the *class_declaration* introduce new members into this declaration space.</span></span> <span data-ttu-id="5bc16-417">Le regole seguenti riguardano *class_member_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="5bc16-417">The following rules apply to *class_member_declaration*s:</span></span>

*  <span data-ttu-id="5bc16-418">Costruttori di istanze, distruttori e i costruttori statici devono avere lo stesso nome della classe che li contiene.</span><span class="sxs-lookup"><span data-stu-id="5bc16-418">Instance constructors, destructors and static constructors must have the same name as the immediately enclosing class.</span></span> <span data-ttu-id="5bc16-419">Tutti gli altri membri devono avere nomi diversi da quello della classe che li contiene.</span><span class="sxs-lookup"><span data-stu-id="5bc16-419">All other members must have names that differ from the name of the immediately enclosing class.</span></span>
*  <span data-ttu-id="5bc16-420">Il nome di una costante, campo, proprietà, eventi o tipo deve essere diverso dai nomi di tutti gli altri membri dichiarati nella stessa classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-420">The name of a constant, field, property, event, or type must differ from the names of all other members declared in the same class.</span></span>
*  <span data-ttu-id="5bc16-421">Il nome di un metodo deve essere diverso dai nomi di tutti gli altri metodi-non dichiarati nella stessa classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-421">The name of a method must differ from the names of all other non-methods declared in the same class.</span></span> <span data-ttu-id="5bc16-422">Inoltre, la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) di un metodo deve essere diversa dalle firme di tutti gli altri metodi dichiarati nella stessa classe, e due i metodi dichiarati nella stessa classe non possono avere firme che differiscono unicamente dal `ref` e `out`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-422">In addition, the signature ([Signatures and overloading](basic-concepts.md#signatures-and-overloading)) of a method must differ from the signatures of all other methods declared in the same class, and two methods declared in the same class may not have signatures that differ solely by `ref` and `out`.</span></span>
*  <span data-ttu-id="5bc16-423">La firma di un costruttore di istanza deve essere diversa dalle firme di tutti gli altri costruttori di istanze dichiarate nella stessa classe, e due costruttori dichiarati nella stessa classe non possono avere firme che differiscono unicamente per `ref` e `out`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-423">The signature of an instance constructor must differ from the signatures of all other instance constructors declared in the same class, and two constructors declared in the same class may not have signatures that differ solely by `ref` and `out`.</span></span>
*  <span data-ttu-id="5bc16-424">La firma di un indicizzatore sia diversa dalle firme di tutti gli altri indicizzatori dichiarati nella stessa classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-424">The signature of an indexer must differ from the signatures of all other indexers declared in the same class.</span></span>
*  <span data-ttu-id="5bc16-425">La firma di un operatore deve essere diversa dalle firme di tutti gli altri operatori dichiarati nella stessa classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-425">The signature of an operator must differ from the signatures of all other operators declared in the same class.</span></span>

<span data-ttu-id="5bc16-426">I membri ereditati di un tipo di classe ([ereditarietà](classes.md#inheritance)) non fanno parte dello spazio di dichiarazione di una classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-426">The inherited members of a class type ([Inheritance](classes.md#inheritance)) are not part of the declaration space of a class.</span></span> <span data-ttu-id="5bc16-427">Di conseguenza, una classe derivata può dichiarare un membro con lo stesso nome o firma di un membro ereditato (che in effetti nasconde il membro ereditato).</span><span class="sxs-lookup"><span data-stu-id="5bc16-427">Thus, a derived class is allowed to declare a member with the same name or signature as an inherited member (which in effect hides the inherited member).</span></span>

### <a name="the-instance-type"></a><span data-ttu-id="5bc16-428">Il tipo di istanza</span><span class="sxs-lookup"><span data-stu-id="5bc16-428">The instance type</span></span>

<span data-ttu-id="5bc16-429">Ogni dichiarazione di classe dispone di un tipo associato ([associato e non associato tipi](types.md#bound-and-unbound-types)), il ***tipo di istanza***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-429">Each class declaration has an associated bound type ([Bound and unbound types](types.md#bound-and-unbound-types)), the ***instance type***.</span></span> <span data-ttu-id="5bc16-430">Per una dichiarazione di classe generica, il tipo di istanza è formato mediante la creazione di un tipo costruito ([i tipi costruiti](types.md#constructed-types)) dalla dichiarazione del tipo, con ogni del tipo specificato di argomenti in corso il corrispondente parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-430">For a generic class declaration, the instance type is formed by creating a constructed type ([Constructed types](types.md#constructed-types)) from the type declaration, with each of the supplied type arguments being the corresponding type parameter.</span></span> <span data-ttu-id="5bc16-431">Poiché il tipo di istanza Usa i parametri di tipo, utilizzabile solo in cui i parametri di tipo sono nell'ambito. vale a dire, all'interno della dichiarazione di classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-431">Since the instance type uses the type parameters, it can only be used where the type parameters are in scope; that is, inside the class declaration.</span></span> <span data-ttu-id="5bc16-432">Il tipo di istanza è il tipo di `this` per il codice scritto all'interno della dichiarazione di classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-432">The instance type is the type of `this` for code written inside the class declaration.</span></span> <span data-ttu-id="5bc16-433">Per le classi non generiche, il tipo di istanza è semplicemente la classe dichiarata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-433">For non-generic classes, the instance type is simply the declared class.</span></span> <span data-ttu-id="5bc16-434">Di seguito viene illustrato più dichiarazioni di classe insieme ai relativi tipi di istanza:</span><span class="sxs-lookup"><span data-stu-id="5bc16-434">The following shows several class declarations along with their instance types:</span></span> 
```csharp
class A<T>                           // instance type: A<T>
{
    class B {}                       // instance type: A<T>.B
    class C<U> {}                    // instance type: A<T>.C<U>
}

class D {}                           // instance type: D
```

### <a name="members-of-constructed-types"></a><span data-ttu-id="5bc16-435">Membri di tipi costruiti</span><span class="sxs-lookup"><span data-stu-id="5bc16-435">Members of constructed types</span></span>

<span data-ttu-id="5bc16-436">I membri di un tipo costruito non ereditate vengono ottenuti tramite la sostituzione, per ognuno *type_parameter* nella dichiarazione del membro, il corrispondente *type_argument* del tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-436">The non-inherited members of a constructed type are obtained by substituting, for each *type_parameter* in the member declaration, the corresponding *type_argument* of the constructed type.</span></span> <span data-ttu-id="5bc16-437">Il processo di sostituzione si basa sul significato semantico delle dichiarazioni di tipo e non è semplice sostituzione di testo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-437">The substitution process is based on the semantic meaning of type declarations, and is not simply textual substitution.</span></span>

<span data-ttu-id="5bc16-438">Ad esempio, considerando la dichiarazione di classe generica</span><span class="sxs-lookup"><span data-stu-id="5bc16-438">For example, given the generic class declaration</span></span>
```csharp
class Gen<T,U>
{
    public T[,] a;
    public void G(int i, T t, Gen<U,T> gt) {...}
    public U Prop { get {...} set {...} }
    public int H(double d) {...}
}
```
<span data-ttu-id="5bc16-439">il tipo costruito `Gen<int[],IComparable<string>>` include i membri seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-439">the constructed type `Gen<int[],IComparable<string>>` has the following members:</span></span>
```csharp
public int[,][] a;
public void G(int i, int[] t, Gen<IComparable<string>,int[]> gt) {...}
public IComparable<string> Prop { get {...} set {...} }
public int H(double d) {...}
```

<span data-ttu-id="5bc16-440">Il tipo del membro `a` nella dichiarazione di classe generica `Gen` è "matrice bidimensionale di `T`", pertanto il tipo del membro `a` nel tipo costruito precedente è "matrice bidimensionale di matrice unidimensionale di `int`", o `int[,][]`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-440">The type of the member `a` in the generic class declaration `Gen` is "two-dimensional array of `T`", so the type of the member `a` in the constructed type above is "two-dimensional array of one-dimensional array of `int`", or `int[,][]`.</span></span>

<span data-ttu-id="5bc16-441">All'interno di membri di funzione di istanza, il tipo della `this` è il tipo di istanza ([del tipo di istanza](classes.md#the-instance-type)) della dichiarazione che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="5bc16-441">Within instance function members, the type of `this` is the instance type ([The instance type](classes.md#the-instance-type)) of the containing declaration.</span></span>

<span data-ttu-id="5bc16-442">Tutti i membri di una classe generica possono usare i parametri di tipo da qualsiasi classe che lo contiene, direttamente o come parte di un tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-442">All members of a generic class can use type parameters from any enclosing class, either directly or as part of a constructed type.</span></span> <span data-ttu-id="5bc16-443">Quando un particolare tipo costruito chiuso ([aperto e chiuso tipi](types.md#open-and-closed-types)) viene utilizzato in fase di esecuzione, ogni uso di un parametro di tipo viene sostituito con l'argomento di tipo effettivo fornito per il tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-443">When a particular closed constructed type ([Open and closed types](types.md#open-and-closed-types)) is used at run-time, each use of a type parameter is replaced with the actual type argument supplied to the constructed type.</span></span> <span data-ttu-id="5bc16-444">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5bc16-444">For example:</span></span>
```csharp
class C<V>
{
    public V f1;
    public C<V> f2 = null;

    public C(V x) {
        this.f1 = x;
        this.f2 = this;
    }
}

class Application
{
    static void Main() {
        C<int> x1 = new C<int>(1);
        Console.WriteLine(x1.f1);        // Prints 1

        C<double> x2 = new C<double>(3.1415);
        Console.WriteLine(x2.f1);        // Prints 3.1415
    }
}
```

### <a name="inheritance"></a><span data-ttu-id="5bc16-445">Ereditarietà</span><span class="sxs-lookup"><span data-stu-id="5bc16-445">Inheritance</span></span>

<span data-ttu-id="5bc16-446">Una classe ***eredita*** i membri del relativo tipo di classe di base diretta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-446">A class ***inherits*** the members of its direct base class type.</span></span> <span data-ttu-id="5bc16-447">L'ereditarietà prevede che una classe contenga in modo implicito tutti i membri del relativo tipo di classe di base diretta, ad eccezione di costruttori di istanze, distruttori e i costruttori statici della classe di base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-447">Inheritance means that a class implicitly contains all members of its direct base class type, except for the instance constructors, destructors and static constructors of the base class.</span></span> <span data-ttu-id="5bc16-448">Alcuni aspetti importanti di ereditarietà sono:</span><span class="sxs-lookup"><span data-stu-id="5bc16-448">Some important aspects of inheritance are:</span></span>

*  <span data-ttu-id="5bc16-449">L'ereditarietà è transitiva.</span><span class="sxs-lookup"><span data-stu-id="5bc16-449">Inheritance is transitive.</span></span> <span data-ttu-id="5bc16-450">Se `C` deriva dal `B`, e `B` è derivato da `A`, quindi `C` eredita i membri dichiarati nella `B` nonché membri dichiarati nel `A`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-450">If `C` is derived from `B`, and `B` is derived from `A`, then `C` inherits the members declared in `B` as well as the members declared in `A`.</span></span>
*  <span data-ttu-id="5bc16-451">Una classe derivata estende la classe di base diretta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-451">A derived class extends its direct base class.</span></span> <span data-ttu-id="5bc16-452">Una classe derivata può aggiungere nuovi membri a quelli ereditati, ma non può rimuovere la definizione di un membro ereditato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-452">A derived class can add new members to those it inherits, but it cannot remove the definition of an inherited member.</span></span>
*  <span data-ttu-id="5bc16-453">Costruttori di istanze, distruttori e i costruttori statici non vengono ereditati, ma sono tutti gli altri membri, indipendentemente dall'accessibilità dichiarata ([l'accesso ai membri](basic-concepts.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-453">Instance constructors, destructors, and static constructors are not inherited, but all other members are, regardless of their declared accessibility ([Member access](basic-concepts.md#member-access)).</span></span> <span data-ttu-id="5bc16-454">Tuttavia, a seconda della relativa accessibilità dichiarata, i membri ereditati non sia accessibili in una classe derivata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-454">However, depending on their declared accessibility, inherited members might not be accessible in a derived class.</span></span>
*  <span data-ttu-id="5bc16-455">Una classe derivata può ***nascondere*** ([nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance)) con la dichiarazione di nuovi membri con lo stesso nome o firma di membri ereditati.</span><span class="sxs-lookup"><span data-stu-id="5bc16-455">A derived class can ***hide*** ([Hiding through inheritance](basic-concepts.md#hiding-through-inheritance)) inherited members by declaring new members with the same name or signature.</span></span> <span data-ttu-id="5bc16-456">Si noti tuttavia che se si nasconde un membro ereditato non rimuove tale membro, ovvero semplicemente rende tale membro non accessibili direttamente tramite la classe derivata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-456">Note however that hiding an inherited member does not remove that member—it merely makes that member inaccessible directly through the derived class.</span></span>
*  <span data-ttu-id="5bc16-457">Un'istanza di una classe contiene un set di tutti i campi di istanza dichiarata nella classe e le relative classi base e una conversione implicita ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)) esistente da un tipo di classe derivata a uno dei relativi tipi di classe di base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-457">An instance of a class contains a set of all instance fields declared in the class and its base classes, and an implicit conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) exists from a derived class type to any of its base class types.</span></span> <span data-ttu-id="5bc16-458">Di conseguenza, un riferimento a un'istanza di una classe derivata può essere considerato un riferimento a un'istanza di una delle relative classi base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-458">Thus, a reference to an instance of some derived class can be treated as a reference to an instance of any of its base classes.</span></span>
*  <span data-ttu-id="5bc16-459">Una classe può dichiarare gli indicizzatori, proprietà e metodi virtuali e le classi derivate possono eseguire l'override l'implementazione di questi membri di funzione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-459">A class can declare virtual methods, properties, and indexers, and derived classes can override the implementation of these function members.</span></span> <span data-ttu-id="5bc16-460">Ciò consente alle classi può presentare un comportamento polimorfico che impediva le azioni eseguite da una chiamata alla funzione membro varia a seconda del tipo in fase di esecuzione dell'istanza tramite cui viene richiamato il membro di funzione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-460">This enables classes to exhibit polymorphic behavior wherein the actions performed by a function member invocation varies depending on the run-time type of the instance through which that function member is invoked.</span></span>

<span data-ttu-id="5bc16-461">Il membro ereditato di un tipo di classe costruita sono i membri del tipo di classe base immediata ([alle classi Base](classes.md#base-classes)), che si trova sostituendo gli argomenti di tipo del tipo costruito per ogni occorrenza del tipo corrispondente i parametri in di *class_base* specifica.</span><span class="sxs-lookup"><span data-stu-id="5bc16-461">The inherited member of a constructed class type are the members of the immediate base class type ([Base classes](classes.md#base-classes)), which is found by substituting the type arguments of the constructed type for each occurrence of the corresponding type parameters in the *class_base* specification.</span></span> <span data-ttu-id="5bc16-462">Questi membri, a sua volta, vengono trasformati tramite la sostituzione, per ognuno *type_parameter* nella dichiarazione del membro, il corrispondente *type_argument* del *class_base* specifica.</span><span class="sxs-lookup"><span data-stu-id="5bc16-462">These members, in turn, are transformed by substituting, for each *type_parameter* in the member declaration, the corresponding *type_argument* of the *class_base* specification.</span></span>

```csharp
class B<U>
{
    public U F(long index) {...}
}

class D<T>: B<T[]>
{
    public T G(string s) {...}
}
```

<span data-ttu-id="5bc16-463">Nell'esempio precedente, il tipo costruito `D<int>` dispone di un membro non ereditate `public int G(string s)` ottenuta sostituendo l'argomento di tipo `int` per il parametro di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-463">In the above example, the constructed type `D<int>` has a non-inherited member `public int G(string s)` obtained by substituting the type argument `int` for the type parameter `T`.</span></span> <span data-ttu-id="5bc16-464">`D<int>` è anche un membro ereditato dalla dichiarazione di classe `B`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-464">`D<int>` also has an inherited member from the class declaration `B`.</span></span> <span data-ttu-id="5bc16-465">Questo membro ereditato è determinato dal primo determinazione del tipo di classe di base `B<int[]>` dei `D<int>` sostituendo `int` per `T` nella specifica della classe base `B<T[]>`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-465">This inherited member is determined by first determining the base class type `B<int[]>` of `D<int>` by substituting `int` for `T` in the base class specification `B<T[]>`.</span></span> <span data-ttu-id="5bc16-466">Quindi, come un argomento di tipo `B`, `int[]` viene sostituito `U` nelle `public U F(long index)`, restituendo il membro ereditato `public int[] F(long index)`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-466">Then, as a type argument to `B`, `int[]` is substituted for `U` in `public U F(long index)`, yielding the inherited member `public int[] F(long index)`.</span></span>

### <a name="the-new-modifier"></a><span data-ttu-id="5bc16-467">Il modificatore new</span><span class="sxs-lookup"><span data-stu-id="5bc16-467">The new modifier</span></span>

<span data-ttu-id="5bc16-468">Oggetto *class_member_declaration* è consentito dichiarare un membro con lo stesso nome o firma di un membro ereditato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-468">A *class_member_declaration* is permitted to declare a member with the same name or signature as an inherited member.</span></span> <span data-ttu-id="5bc16-469">In questo caso, il membro della classe derivata viene detto ***nascondere*** il membro della classe base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-469">When this occurs, the derived class member is said to ***hide*** the base class member.</span></span> <span data-ttu-id="5bc16-470">Se si nasconde un membro ereditato non è considerato un errore, ma comporta il compilatore emette un avviso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-470">Hiding an inherited member is not considered an error, but it does cause the compiler to issue a warning.</span></span> <span data-ttu-id="5bc16-471">Per eliminare l'avviso, la dichiarazione del membro della classe derivata può includere un `new` modificatore per indicare che il membro derivato deve nascondere il membro di base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-471">To suppress the warning, the declaration of the derived class member can include a `new` modifier to indicate that the derived member is intended to hide the base member.</span></span> <span data-ttu-id="5bc16-472">Questo argomento viene descritto dettagliatamente nella [nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance).</span><span class="sxs-lookup"><span data-stu-id="5bc16-472">This topic is discussed further in [Hiding through inheritance](basic-concepts.md#hiding-through-inheritance).</span></span>

<span data-ttu-id="5bc16-473">Se un `new` modificatore viene inclusa in una dichiarazione che non nasconde un membro ereditato, viene generato un avviso in tal senso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-473">If a `new` modifier is included in a declaration that doesn't hide an inherited member, a warning to that effect is issued.</span></span> <span data-ttu-id="5bc16-474">Questo avviso viene eliminato tramite la rimozione di `new` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-474">This warning is suppressed by removing the `new` modifier.</span></span>

### <a name="access-modifiers"></a><span data-ttu-id="5bc16-475">Modificatori di accesso</span><span class="sxs-lookup"><span data-stu-id="5bc16-475">Access modifiers</span></span>

<span data-ttu-id="5bc16-476">Oggetto *class_member_declaration* può avere uno dei cinque tipi possibili di accessibilità dichiarata ([l'accessibilità dichiarata](basic-concepts.md#declared-accessibility)): `public`, `protected internal`, `protected`, `internal` , o `private`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-476">A *class_member_declaration* can have any one of the five possible kinds of declared accessibility ([Declared accessibility](basic-concepts.md#declared-accessibility)): `public`, `protected internal`, `protected`, `internal`, or `private`.</span></span> <span data-ttu-id="5bc16-477">Fatta eccezione per il `protected internal` combinazione, tratta di un errore in fase di compilazione per specificare più di un modificatore di accesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-477">Except for the `protected internal` combination, it is a compile-time error to specify more than one access modifier.</span></span> <span data-ttu-id="5bc16-478">Quando un *class_member_declaration* non include tutti i modificatori di accesso, `private` presuppone.</span><span class="sxs-lookup"><span data-stu-id="5bc16-478">When a *class_member_declaration* does not include any access modifiers, `private` is assumed.</span></span>

### <a name="constituent-types"></a><span data-ttu-id="5bc16-479">Tipi che costituiscono</span><span class="sxs-lookup"><span data-stu-id="5bc16-479">Constituent types</span></span>

<span data-ttu-id="5bc16-480">I tipi utilizzati nella dichiarazione di membro sono denominati tipi che costituiscono di tale membro.</span><span class="sxs-lookup"><span data-stu-id="5bc16-480">Types that are used in the declaration of a member are called the constituent types of that member.</span></span> <span data-ttu-id="5bc16-481">Possibili tipi che lo costituiscono sono il tipo di una costante, campo, proprietà, evento o l'indicizzatore, il tipo restituito di un metodo o un operatore e i tipi di parametro di un metodo, indicizzatore, operatore o costruttore di istanza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-481">Possible constituent types are the type of a constant, field, property, event, or indexer, the return type of a method or operator, and the parameter types of a method, indexer, operator, or instance constructor.</span></span> <span data-ttu-id="5bc16-482">I tipi che costituiscono dei membri devono essere accessibili almeno quanto il membro stesso ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-482">The constituent types of a member must be at least as accessible as that member itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

### <a name="static-and-instance-members"></a><span data-ttu-id="5bc16-483">Membri statici e di istanza</span><span class="sxs-lookup"><span data-stu-id="5bc16-483">Static and instance members</span></span>

<span data-ttu-id="5bc16-484">I membri di una classe sono entrambi ***i membri statici*** oppure ***membri di istanza***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-484">Members of a class are either ***static members*** or ***instance members***.</span></span> <span data-ttu-id="5bc16-485">In generale, è utile pensare a membri statici come appartenenti a tipi di classi e membri di istanza come appartenenti agli oggetti (istanze di tipi di classe).</span><span class="sxs-lookup"><span data-stu-id="5bc16-485">Generally speaking, it is useful to think of static members as belonging to class types and instance members as belonging to objects (instances of class types).</span></span>

<span data-ttu-id="5bc16-486">Quando una dichiarazione di campo, metodo, proprietà, evento o l'operatore costruttore include un `static` modificatore, dichiara un membro statico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-486">When a field, method, property, event, operator, or constructor declaration includes a `static` modifier, it declares a static member.</span></span> <span data-ttu-id="5bc16-487">Inoltre, una dichiarazione di costante o un tipo dichiara in modo implicito un membro statico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-487">In addition, a constant or type declaration implicitly declares a static member.</span></span> <span data-ttu-id="5bc16-488">I membri statici hanno le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-488">Static members have the following characteristics:</span></span>

*  <span data-ttu-id="5bc16-489">Quando un membro statico `M` viene fatto riferimento in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, `E` deve indicare un tipo contenente `M`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-489">When a static member `M` is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, `E` must denote a type containing `M`.</span></span> <span data-ttu-id="5bc16-490">Tratta di un errore in fase di compilazione per `E` per indicare un'istanza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-490">It is a compile-time error for `E` to denote an instance.</span></span>
*  <span data-ttu-id="5bc16-491">Un campo statico identifica esattamente una posizione di archiviazione deve essere condiviso da tutte le istanze di un tipo specifico della classe chiuso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-491">A static field identifies exactly one storage location to be shared by all instances of a given closed class type.</span></span> <span data-ttu-id="5bc16-492">Indipendentemente dal numero di istanze di un tipo specifico della classe chiusi venga creato, è sempre un'unica copia di un campo statico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-492">No matter how many instances of a given closed class type are created, there is only ever one copy of a static field.</span></span>
*  <span data-ttu-id="5bc16-493">Un membro di funzione statica (metodo, proprietà, eventi, operatore o costruttore) non agisce su un'istanza specifica ed è un errore in fase di compilazione per fare riferimento a `this` in un membro di funzione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-493">A static function member (method, property, event, operator, or constructor) does not operate on a specific instance, and it is a compile-time error to refer to `this` in such a function member.</span></span>

<span data-ttu-id="5bc16-494">Quando una dichiarazione di campo, metodo, proprietà, eventi, indicizzatore, costruttore o distruttore non include un `static` modificatore, dichiara un membro di istanza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-494">When a field, method, property, event, indexer, constructor, or destructor declaration does not include a `static` modifier, it declares an instance member.</span></span> <span data-ttu-id="5bc16-495">(Un membro di istanza viene talvolta chiamato un membro non statica.) I membri di istanza hanno le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-495">(An instance member is sometimes called a non-static member.) Instance members have the following characteristics:</span></span>

*  <span data-ttu-id="5bc16-496">Quando un membro di istanza `M` viene fatto riferimento in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, `E` devono indicare un'istanza di un tipo contenente `M`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-496">When an instance member `M` is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, `E` must denote an instance of a type containing `M`.</span></span> <span data-ttu-id="5bc16-497">Tratta di un errore in fase di associazione per `E` per indicare un tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-497">It is a binding-time error for `E` to denote a type.</span></span>
*  <span data-ttu-id="5bc16-498">Ogni istanza di una classe contiene un set separato di tutti i campi di istanza della classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-498">Every instance of a class contains a separate set of all instance fields of the class.</span></span>
*  <span data-ttu-id="5bc16-499">Un membro di funzione di istanza (metodo, proprietà, indicizzatore, costruttore di istanza o un distruttore) opera su una determinata istanza della classe e questa istanza è accessibile come `this` ([questo accesso](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-499">An instance function member (method, property, indexer, instance constructor, or destructor) operates on a given instance of the class, and this instance can be accessed as `this` ([This access](expressions.md#this-access)).</span></span>

<span data-ttu-id="5bc16-500">L'esempio seguente illustra le regole per l'accesso a statiche e membri di istanza:</span><span class="sxs-lookup"><span data-stu-id="5bc16-500">The following example illustrates the rules for accessing static and instance members:</span></span>
```csharp
class Test
{
    int x;
    static int y;

    void F() {
        x = 1;            // Ok, same as this.x = 1
        y = 1;            // Ok, same as Test.y = 1
    }

    static void G() {
        x = 1;            // Error, cannot access this.x
        y = 1;            // Ok, same as Test.y = 1
    }

    static void Main() {
        Test t = new Test();
        t.x = 1;          // Ok
        t.y = 1;          // Error, cannot access static member through instance
        Test.x = 1;       // Error, cannot access instance member through type
        Test.y = 1;       // Ok
    }
}
```

<span data-ttu-id="5bc16-501">Il `F` metodo mostra che in un membro di funzione di istanza, una *simple_name* ([nomi semplici](expressions.md#simple-names)) può essere utilizzato per accedere sia i membri di istanza e membri statici.</span><span class="sxs-lookup"><span data-stu-id="5bc16-501">The `F` method shows that in an instance function member, a *simple_name* ([Simple names](expressions.md#simple-names)) can be used to access both instance members and static members.</span></span> <span data-ttu-id="5bc16-502">Il `G` metodo mostra che in un membro di funzione statica, è un errore in fase di compilazione per accedere a un membro di istanza tramite un *simple_name*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-502">The `G` method shows that in a static function member, it is a compile-time error to access an instance member through a *simple_name*.</span></span> <span data-ttu-id="5bc16-503">Il `Main` metodo illustra che in un *member_access* ([accesso ai membri](expressions.md#member-access)), i membri di istanza devono essere accessibili tramite istanze e i membri statici è possibile accedervi tramite i tipi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-503">The `Main` method shows that in a *member_access* ([Member access](expressions.md#member-access)), instance members must be accessed through instances, and static members must be accessed through types.</span></span>

### <a name="nested-types"></a><span data-ttu-id="5bc16-504">Tipi annidati</span><span class="sxs-lookup"><span data-stu-id="5bc16-504">Nested types</span></span>

<span data-ttu-id="5bc16-505">Un tipo dichiarato all'interno di una dichiarazione di classe o struct viene chiamato un ***tipo annidato***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-505">A type declared within a class or struct declaration is called a ***nested type***.</span></span> <span data-ttu-id="5bc16-506">Un tipo che viene dichiarato all'interno di un'unità di compilazione o dello spazio dei nomi viene chiamato un ***tipo non annidato***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-506">A type that is declared within a compilation unit or namespace is called a ***non-nested type***.</span></span>

<span data-ttu-id="5bc16-507">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-507">In the example</span></span>
```csharp
using System;

class A
{
    class B
    {
        static void F() {
            Console.WriteLine("A.B.F");
        }
    }
}
```
<span data-ttu-id="5bc16-508">classe `B` è un tipo annidato perché è dichiarato all'interno di classi `A`e la classe `A` è un tipo non annidato perché è dichiarato all'interno di un'unità di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-508">class `B` is a nested type because it is declared within class `A`, and class `A` is a non-nested type because it is declared within a compilation unit.</span></span>

#### <a name="fully-qualified-name"></a><span data-ttu-id="5bc16-509">Nome completo</span><span class="sxs-lookup"><span data-stu-id="5bc16-509">Fully qualified name</span></span>

<span data-ttu-id="5bc16-510">Specificare il nome completo ([i nomi completi](basic-concepts.md#fully-qualified-names)) per un tipo annidato è `S.N` in cui `S` è il nome completo del tipo nel quale tipo `N` viene dichiarato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-510">The fully qualified name ([Fully qualified names](basic-concepts.md#fully-qualified-names)) for a nested type is `S.N` where `S` is the fully qualified name of the type in which type `N` is declared.</span></span>

#### <a name="declared-accessibility"></a><span data-ttu-id="5bc16-511">Accessibilità dichiarata</span><span class="sxs-lookup"><span data-stu-id="5bc16-511">Declared accessibility</span></span>

<span data-ttu-id="5bc16-512">Tipi annidati non possono avere `public` oppure `internal` accessibilità dichiarata e avere `internal` l'accessibilità dichiarata per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="5bc16-512">Non-nested types can have `public` or `internal` declared accessibility and have `internal` declared accessibility by default.</span></span> <span data-ttu-id="5bc16-513">I tipi annidati sono disponibili queste forme di accessibilità dichiarata, oltre a uno o più ulteriori forme di accessibilità dichiarata, a seconda che il tipo che lo contiene sia una classe o struct:</span><span class="sxs-lookup"><span data-stu-id="5bc16-513">Nested types can have these forms of declared accessibility too, plus one or more additional forms of declared accessibility, depending on whether the containing type is a class or struct:</span></span>

*  <span data-ttu-id="5bc16-514">Un tipo annidato che viene dichiarato in una classe può avere uno dei cinque forme di accessibilità dichiarata (`public`, `protected internal`, `protected`, `internal`, o `private`) e, analogamente agli altri membri di classe, per impostazione predefinita `private` dichiarato accessibilità.</span><span class="sxs-lookup"><span data-stu-id="5bc16-514">A nested type that is declared in a class can have any of five forms of declared accessibility (`public`, `protected internal`, `protected`, `internal`, or `private`) and, like other class members, defaults to `private` declared accessibility.</span></span>
*  <span data-ttu-id="5bc16-515">Un tipo annidato che viene dichiarato in uno struct può avere uno dei tre formati seguenti di accessibilità dichiarata (`public`, `internal`, o `private`) e, analogamente agli altri membri struct, per impostazione predefinita `private` l'accessibilità dichiarata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-515">A nested type that is declared in a struct can have any of three forms of declared accessibility (`public`, `internal`, or `private`) and, like other struct members, defaults to `private` declared accessibility.</span></span>

<span data-ttu-id="5bc16-516">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-516">The example</span></span>
```csharp
public class List
{
    // Private data structure
    private class Node
    { 
        public object Data;
        public Node Next;

        public Node(object data, Node next) {
            this.Data = data;
            this.Next = next;
        }
    }

    private Node first = null;
    private Node last = null;

    // Public interface
    public void AddToFront(object o) {...}
    public void AddToBack(object o) {...}
    public object RemoveFromFront() {...}
    public object RemoveFromBack() {...}
    public int Count { get {...} }
}
```
<span data-ttu-id="5bc16-517">dichiara una classe annidata private `Node`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-517">declares a private nested class `Node`.</span></span>

#### <a name="hiding"></a><span data-ttu-id="5bc16-518">Disattivazione della visualizzazione</span><span class="sxs-lookup"><span data-stu-id="5bc16-518">Hiding</span></span>

<span data-ttu-id="5bc16-519">Può nascondere un tipo annidato ([nascondere il nome](basic-concepts.md#name-hiding)) un membro di base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-519">A nested type may hide ([Name hiding](basic-concepts.md#name-hiding)) a base member.</span></span> <span data-ttu-id="5bc16-520">Il `new` modificatore è consentito nelle dichiarazioni di tipo annidato in modo da nascondere può essere espressi in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-520">The `new` modifier is permitted on nested type declarations so that hiding can be expressed explicitly.</span></span> <span data-ttu-id="5bc16-521">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-521">The example</span></span>
```csharp
using System;

class Base
{
    public static void M() {
        Console.WriteLine("Base.M");
    }
}

class Derived: Base 
{
    new public class M 
    {
        public static void F() {
            Console.WriteLine("Derived.M.F");
        }
    }
}

class Test 
{
    static void Main() {
        Derived.M.F();
    }
}
```
<span data-ttu-id="5bc16-522">viene illustrata una classe annidata `M` che consente di nascondere il metodo `M` definito nella `Base`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-522">shows a nested class `M` that hides the method `M` defined in `Base`.</span></span>

#### <a name="this-access"></a><span data-ttu-id="5bc16-523">Questo tipo di accesso</span><span class="sxs-lookup"><span data-stu-id="5bc16-523">this access</span></span>

<span data-ttu-id="5bc16-524">Un tipo annidato e relativo tipo contenitore non è una relazione speciale riguarda *this_access* ([questo accesso](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-524">A nested type and its containing type do not have a special relationship with regard to *this_access* ([This access](expressions.md#this-access)).</span></span> <span data-ttu-id="5bc16-525">In particolare, `this` all'interno di un tipo annidato non può essere usato per fare riferimento ai membri di istanza del tipo contenitore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-525">Specifically, `this` within a nested type cannot be used to refer to instance members of the containing type.</span></span> <span data-ttu-id="5bc16-526">In casi in cui un tipo annidato deve accedere ai membri di istanza del tipo contenitore, è possibile fornire l'accesso, fornendo il `this` per l'istanza del tipo contenitore come argomento del costruttore per il tipo annidato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-526">In cases where a nested type needs access to the instance members of its containing type, access can be provided by providing the `this` for the instance of the containing type as a constructor argument for the nested type.</span></span> <span data-ttu-id="5bc16-527">Nell'esempio seguente</span><span class="sxs-lookup"><span data-stu-id="5bc16-527">The following example</span></span>
```csharp
using System;

class C
{
    int i = 123;

    public void F() {
        Nested n = new Nested(this);
        n.G();
    }

    public class Nested
    {
        C this_c;

        public Nested(C c) {
            this_c = c;
        }

        public void G() {
            Console.WriteLine(this_c.i);
        }
    }
}

class Test
{
    static void Main() {
        C c = new C();
        c.F();
    }
}
```
<span data-ttu-id="5bc16-528">Questa tecnica è illustrata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-528">shows this technique.</span></span> <span data-ttu-id="5bc16-529">Un'istanza di `C` crea un'istanza di `Nested` e passa il proprio `this` al `Nested`del costruttore per fornire gli accessi successivi a `C`di membri di istanza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-529">An instance of `C` creates an instance of `Nested` and passes its own `this` to `Nested`'s constructor in order to provide subsequent access to `C`'s instance members.</span></span>

#### <a name="access-to-private-and-protected-members-of-the-containing-type"></a><span data-ttu-id="5bc16-530">Accesso ai membri privati e protetti del tipo contenitore</span><span class="sxs-lookup"><span data-stu-id="5bc16-530">Access to private and protected members of the containing type</span></span>

<span data-ttu-id="5bc16-531">Un tipo annidato può accedere a tutti i membri che sono accessibili al tipo contenitore, inclusi i membri del tipo contenitore aventi `private` e `protected` l'accessibilità dichiarata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-531">A nested type has access to all of the members that are accessible to its containing type, including members of the containing type that have `private` and `protected` declared accessibility.</span></span> <span data-ttu-id="5bc16-532">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-532">The example</span></span>
```csharp
using System;

class C 
{
    private static void F() {
        Console.WriteLine("C.F");
    }

    public class Nested 
    {
        public static void G() {
            F();
        }
    }
}

class Test 
{
    static void Main() {
        C.Nested.G();
    }
}
```
<span data-ttu-id="5bc16-533">viene illustrata una classe `C` che contiene una classe annidata `Nested`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-533">shows a class `C` that contains a nested class `Nested`.</span></span> <span data-ttu-id="5bc16-534">All'interno `Nested`, il metodo `G` chiama il metodo statico `F` definita nella `C`, e `F` privata l'accessibilità dichiarata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-534">Within `Nested`, the method `G` calls the static method `F` defined in `C`, and `F` has private declared accessibility.</span></span>

<span data-ttu-id="5bc16-535">Un tipo annidato può inoltre accedere ai membri protetti definiti in un tipo di base del tipo contenitore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-535">A nested type also may access protected members defined in a base type of its containing type.</span></span> <span data-ttu-id="5bc16-536">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-536">In the example</span></span>
```csharp
using System;

class Base 
{
    protected void F() {
        Console.WriteLine("Base.F");
    }
}

class Derived: Base 
{
    public class Nested 
    {
        public void G() {
            Derived d = new Derived();
            d.F();        // ok
        }
    }
}

class Test 
{
    static void Main() {
        Derived.Nested n = new Derived.Nested();
        n.G();
    }
}
```
<span data-ttu-id="5bc16-537">la classe annidata `Derived.Nested` accede il metodo protetto `F` definiti nel `Derived`della classe base, `Base`, da chiamata tramite un'istanza di `Derived`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-537">the nested class `Derived.Nested` accesses the protected method `F` defined in `Derived`'s base class, `Base`, by calling through an instance of `Derived`.</span></span>

#### <a name="nested-types-in-generic-classes"></a><span data-ttu-id="5bc16-538">Tipi annidati in classi generiche</span><span class="sxs-lookup"><span data-stu-id="5bc16-538">Nested types in generic classes</span></span>

<span data-ttu-id="5bc16-539">Una dichiarazione di classe generica può contenere dichiarazioni di tipo annidato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-539">A generic class declaration can contain nested type declarations.</span></span> <span data-ttu-id="5bc16-540">I parametri di tipo della classe contenitore sono utilizzabile all'interno di tipi annidati.</span><span class="sxs-lookup"><span data-stu-id="5bc16-540">The type parameters of the enclosing class can be used within the nested types.</span></span> <span data-ttu-id="5bc16-541">Una dichiarazione di tipo annidato può contenere parametri di tipo aggiuntivi che si applicano solo al tipo annidato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-541">A nested type declaration can contain additional type parameters that apply only to the nested type.</span></span>

<span data-ttu-id="5bc16-542">Ogni dichiarazione di tipo contenuti all'interno di una dichiarazione di classe generica in modo implicito è una dichiarazione di tipo generico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-542">Every type declaration contained within a generic class declaration is implicitly a generic type declaration.</span></span> <span data-ttu-id="5bc16-543">Quando si scrive un riferimento a un tipo annidato all'interno di un tipo generico, è possibile che il tipo costruito che lo contiene, inclusi i relativi argomenti di tipo, deve chiamarsi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-543">When writing a reference to a type nested within a generic type, the containing constructed type, including its type arguments, must be named.</span></span> <span data-ttu-id="5bc16-544">Tuttavia, all'interno della classe outer, il tipo annidato può essere utilizzato senza qualifica; il tipo di istanza della classe esterna può essere utilizzato in modo implicito quando si crea il tipo annidato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-544">However, from within the outer class, the nested type can be used without qualification; the instance type of the outer class can be implicitly used when constructing the nested type.</span></span> <span data-ttu-id="5bc16-545">L'esempio seguente illustra tre modi diversi corretti per fare riferimento a un tipo costruito creato da `Inner`; le prime due sono equivalenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-545">The following example shows three different correct ways to refer to a constructed type created from `Inner`; the first two are equivalent:</span></span>
```csharp
class Outer<T>
{
    class Inner<U>
    {
        public static void F(T t, U u) {...}
    }

    static void F(T t) {
        Outer<T>.Inner<string>.F(t, "abc");      // These two statements have
        Inner<string>.F(t, "abc");               // the same effect

        Outer<int>.Inner<string>.F(3, "abc");    // This type is different

        Outer.Inner<string>.F(t, "abc");         // Error, Outer needs type arg
    }
}
```

<span data-ttu-id="5bc16-546">Anche se è bello lo stile di programmazione un parametro di tipo in un tipo annidato può nascondere un membro o parametro di tipo dichiarato nel tipo esterno:</span><span class="sxs-lookup"><span data-stu-id="5bc16-546">Although it is bad programming style, a type parameter in a nested type can hide a member or type parameter declared in the outer type:</span></span>
```csharp
class Outer<T>
{
    class Inner<T>        // Valid, hides Outer's T
    {
        public T t;       // Refers to Inner's T
    }
}
```

### <a name="reserved-member-names"></a><span data-ttu-id="5bc16-547">Nomi dei membri riservato</span><span class="sxs-lookup"><span data-stu-id="5bc16-547">Reserved member names</span></span>

<span data-ttu-id="5bc16-548">Per semplificare il codice c# in fase di esecuzione implementazione sottostante, per ogni dichiarazione di membro di origine che è una proprietà, un evento o un indicizzatore, l'implementazione deve riservare due firme del metodo in base al tipo di dichiarazione del membro, il nome e il relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-548">To facilitate the underlying C# run-time implementation, for each source member declaration that is a property, event, or indexer, the implementation must reserve two method signatures based on the kind of the member declaration, its name, and its type.</span></span> <span data-ttu-id="5bc16-549">È un errore in fase di compilazione per un programma dichiarare un membro la cui firma corrisponda a uno di questi riservato firme, anche se l'implementazione sottostante in fase di esecuzione non usa tali prenotazioni.</span><span class="sxs-lookup"><span data-stu-id="5bc16-549">It is a compile-time error for a program to declare a member whose signature matches one of these reserved signatures, even if the underlying run-time implementation does not make use of these reservations.</span></span>

<span data-ttu-id="5bc16-550">Le dichiarazioni non introducono i nomi riservati, pertanto non partecipano in ricerca di membri.</span><span class="sxs-lookup"><span data-stu-id="5bc16-550">The reserved names do not introduce declarations, thus they do not participate in member lookup.</span></span> <span data-ttu-id="5bc16-551">Tuttavia, una dichiarazione di metodo riservato associate le firme di partecipare all'ereditarietà ([ereditarietà](classes.md#inheritance)) e possono essere nascosti con il `new` modificatore ([il nuovo modificatore](classes.md#the-new-modifier)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-551">However, a declaration's associated reserved method signatures do participate in inheritance ([Inheritance](classes.md#inheritance)), and can be hidden with the `new` modifier ([The new modifier](classes.md#the-new-modifier)).</span></span>

<span data-ttu-id="5bc16-552">La prenotazione di questi nomi ha tre scopi:</span><span class="sxs-lookup"><span data-stu-id="5bc16-552">The reservation of these names serves three purposes:</span></span>

*  <span data-ttu-id="5bc16-553">Per consentire l'implementazione sottostante da utilizzare un identificatore standard come un nome di metodo per ottenere o impostare l'accesso alla funzionalità del linguaggio c#.</span><span class="sxs-lookup"><span data-stu-id="5bc16-553">To allow the underlying implementation to use an ordinary identifier as a method name for get or set access to the C# language feature.</span></span>
*  <span data-ttu-id="5bc16-554">Consentire ad altri linguaggi interoperare utilizzando un identificatore standard come un nome di metodo per ottenere o impostare l'accesso alla funzionalità del linguaggio c#.</span><span class="sxs-lookup"><span data-stu-id="5bc16-554">To allow other languages to interoperate using an ordinary identifier as a method name for get or set access to the C# language feature.</span></span>
*  <span data-ttu-id="5bc16-555">Al fine di garantire che l'origine è accettata da un compilatore conforme è accettato da un'altra, rendendo le specifiche del membro riservato a nomi coerenti in tutte le implementazioni in c#.</span><span class="sxs-lookup"><span data-stu-id="5bc16-555">To help ensure that the source accepted by one conforming compiler is accepted by another, by making the specifics of reserved member names consistent across all C# implementations.</span></span>

<span data-ttu-id="5bc16-556">La dichiarazione di un distruttore ([distruttori](classes.md#destructors)) anche fa sì che una firma per la prenotazione ([i nomi dei membri sono riservati per i distruttori](classes.md#member-names-reserved-for-destructors)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-556">The declaration of a destructor ([Destructors](classes.md#destructors)) also causes a signature to be reserved ([Member names reserved for destructors](classes.md#member-names-reserved-for-destructors)).</span></span>

#### <a name="member-names-reserved-for-properties"></a><span data-ttu-id="5bc16-557">Nomi dei membri riservati per le proprietà</span><span class="sxs-lookup"><span data-stu-id="5bc16-557">Member names reserved for properties</span></span>

<span data-ttu-id="5bc16-558">Per una proprietà `P` ([delle proprietà](classes.md#properties)) del tipo `T`, le firme seguenti sono riservate:</span><span class="sxs-lookup"><span data-stu-id="5bc16-558">For a property `P` ([Properties](classes.md#properties)) of type `T`, the following signatures are reserved:</span></span>

```csharp
T get_P();
void set_P(T value);
```

<span data-ttu-id="5bc16-559">Entrambe le firme sono riservate, anche se la proprietà è di sola lettura o in sola lettura.</span><span class="sxs-lookup"><span data-stu-id="5bc16-559">Both signatures are reserved, even if the property is read-only or write-only.</span></span>

<span data-ttu-id="5bc16-560">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-560">In the example</span></span>
```csharp
using System;

class A
{
    public int P {
        get { return 123; }
    }
}

class B: A
{
    new public int get_P() {
        return 456;
    }

    new public void set_P(int value) {
    }
}

class Test
{
    static void Main() {
        B b = new B();
        A a = b;
        Console.WriteLine(a.P);
        Console.WriteLine(b.P);
        Console.WriteLine(b.get_P());
    }
}
```
<span data-ttu-id="5bc16-561">una classe `A` definisce una proprietà di sola lettura `P`, riservando in questo modo le firme per `get_P` e `set_P` metodi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-561">a class `A` defines a read-only property `P`, thus reserving signatures for `get_P` and `set_P` methods.</span></span> <span data-ttu-id="5bc16-562">Una classe `B` deriva da `A` e nasconde entrambe le firme riservate.</span><span class="sxs-lookup"><span data-stu-id="5bc16-562">A class `B` derives from `A` and hides both of these reserved signatures.</span></span> <span data-ttu-id="5bc16-563">L'esempio produce l'output:</span><span class="sxs-lookup"><span data-stu-id="5bc16-563">The example produces the output:</span></span>
```
123
123
456
```

#### <a name="member-names-reserved-for-events"></a><span data-ttu-id="5bc16-564">Nomi dei membri riservati per gli eventi</span><span class="sxs-lookup"><span data-stu-id="5bc16-564">Member names reserved for events</span></span>

<span data-ttu-id="5bc16-565">Per un evento `E` ([eventi](classes.md#events)) del tipo di delegato `T`, le firme seguenti sono riservate:</span><span class="sxs-lookup"><span data-stu-id="5bc16-565">For an event `E` ([Events](classes.md#events)) of delegate type `T`, the following signatures are reserved:</span></span>
```csharp
void add_E(T handler);
void remove_E(T handler);
```

#### <a name="member-names-reserved-for-indexers"></a><span data-ttu-id="5bc16-566">Nomi dei membri riservati per gli indicizzatori</span><span class="sxs-lookup"><span data-stu-id="5bc16-566">Member names reserved for indexers</span></span>

<span data-ttu-id="5bc16-567">Per un indicizzatore ([indicizzatori](classes.md#indexers)) di tipo `T` con l'elenco di parametri `L`, le firme seguenti sono riservate:</span><span class="sxs-lookup"><span data-stu-id="5bc16-567">For an indexer ([Indexers](classes.md#indexers)) of type `T` with parameter-list `L`, the following signatures are reserved:</span></span>
```csharp
T get_Item(L);
void set_Item(L, T value);
```

<span data-ttu-id="5bc16-568">Entrambe le firme sono riservate, anche se l'indicizzatore è in sola lettura o in sola lettura.</span><span class="sxs-lookup"><span data-stu-id="5bc16-568">Both signatures are reserved, even if the indexer is read-only or write-only.</span></span>

<span data-ttu-id="5bc16-569">Inoltre il nome del membro `Item` è riservato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-569">Furthermore the member name `Item` is reserved.</span></span>

#### <a name="member-names-reserved-for-destructors"></a><span data-ttu-id="5bc16-570">Nomi dei membri riservati per i distruttori</span><span class="sxs-lookup"><span data-stu-id="5bc16-570">Member names reserved for destructors</span></span>

<span data-ttu-id="5bc16-571">Per una classe che contiene un distruttore ([distruttori](classes.md#destructors)), viene riservata la firma seguente:</span><span class="sxs-lookup"><span data-stu-id="5bc16-571">For a class containing a destructor ([Destructors](classes.md#destructors)), the following signature is reserved:</span></span>
```csharp
void Finalize();
```

## <a name="constants"></a><span data-ttu-id="5bc16-572">Costanti</span><span class="sxs-lookup"><span data-stu-id="5bc16-572">Constants</span></span>

<span data-ttu-id="5bc16-573">Oggetto ***costante*** è un membro di classe che rappresenta un valore costante: un valore che può essere calcolato in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-573">A ***constant*** is a class member that represents a constant value: a value that can be computed at compile-time.</span></span> <span data-ttu-id="5bc16-574">Oggetto *constant_declaration* introduce una o più costanti di un determinato tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-574">A *constant_declaration* introduces one or more constants of a given type.</span></span>

```antlr
constant_declaration
    : attributes? constant_modifier* 'const' type constant_declarators ';'
    ;

constant_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    ;

constant_declarators
    : constant_declarator (',' constant_declarator)*
    ;

constant_declarator
    : identifier '=' constant_expression
    ;
```

<span data-ttu-id="5bc16-575">Oggetto *constant_declaration* può includere un set di *attributi* ([attributi](attributes.md)), un `new` modificatore ([il nuovo modificatore](classes.md#the-new-modifier)), e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-575">A *constant_declaration* may include a set of *attributes* ([Attributes](attributes.md)), a `new` modifier ([The new modifier](classes.md#the-new-modifier)), and a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)).</span></span> <span data-ttu-id="5bc16-576">Gli attributi e modificatori si applicano a tutti i membri dichiarati per il *constant_declaration*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-576">The attributes and modifiers apply to all of the members declared by the *constant_declaration*.</span></span> <span data-ttu-id="5bc16-577">Anche se le costanti sono considerate membri statici, un *constant_declaration* non richiede né consente a un `static` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-577">Even though constants are considered static members, a *constant_declaration* neither requires nor allows a `static` modifier.</span></span> <span data-ttu-id="5bc16-578">È un errore per più volte lo stesso modificatore in una dichiarazione di costante.</span><span class="sxs-lookup"><span data-stu-id="5bc16-578">It is an error for the same modifier to appear multiple times in a constant declaration.</span></span>

<span data-ttu-id="5bc16-579">Il *tipo* di un *constant_declaration* specifica il tipo di membri introdotti dalla dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-579">The *type* of a *constant_declaration* specifies the type of the members introduced by the declaration.</span></span> <span data-ttu-id="5bc16-580">Il tipo è seguito da un elenco delle *constant_declarator*s, ognuno dei quali presenta un nuovo membro.</span><span class="sxs-lookup"><span data-stu-id="5bc16-580">The type is followed by a list of *constant_declarator*s, each of which introduces a new member.</span></span> <span data-ttu-id="5bc16-581">Oggetto *constant_declarator* costituito da un *identificatore* che denomina il membro, seguito da un "`=`" token, seguito da un *constant_expression* ([ Espressioni costanti](expressions.md#constant-expressions)) che specifica il valore del membro.</span><span class="sxs-lookup"><span data-stu-id="5bc16-581">A *constant_declarator* consists of an *identifier* that names the member, followed by an "`=`" token, followed by a *constant_expression* ([Constant expressions](expressions.md#constant-expressions)) that gives the value of the member.</span></span>

<span data-ttu-id="5bc16-582">Il *tipo* specificato in una costante deve essere dichiarazione `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `string`, un *enum_type*, o un oggetto *reference_type*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-582">The *type* specified in a constant declaration must be `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `string`, an *enum_type*, or a *reference_type*.</span></span> <span data-ttu-id="5bc16-583">Ciascuna *constant_expression* deve restituire un valore del tipo di destinazione o di un tipo che può essere convertito nel tipo di destinazione tramite una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-583">Each *constant_expression* must yield a value of the target type or of a type that can be converted to the target type by an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)).</span></span>

<span data-ttu-id="5bc16-584">Il *tipo* di una costante deve essere accessibile almeno quanto la costante ([vincoli accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-584">The *type* of a constant must be at least as accessible as the constant itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="5bc16-585">Viene ottenuto il valore di una costante in un'espressione che usa un' *simple_name* ([nomi semplici](expressions.md#simple-names)) o un *member_access* ([l'accesso ai membri](expressions.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-585">The value of a constant is obtained in an expression using a *simple_name* ([Simple names](expressions.md#simple-names)) or a *member_access* ([Member access](expressions.md#member-access)).</span></span>

<span data-ttu-id="5bc16-586">Una costante è possibile sia inclusa una *constant_expression*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-586">A constant can itself participate in a *constant_expression*.</span></span> <span data-ttu-id="5bc16-587">Di conseguenza, è possibile utilizzare una costante in qualsiasi costrutto che richiede un *constant_expression*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-587">Thus, a constant may be used in any construct that requires a *constant_expression*.</span></span> <span data-ttu-id="5bc16-588">Esempi di tali costrutti `case` etichette `goto case` istruzioni, `enum` le dichiarazioni dei membri, attributi e le altre dichiarazioni costante.</span><span class="sxs-lookup"><span data-stu-id="5bc16-588">Examples of such constructs include `case` labels, `goto case` statements, `enum` member declarations, attributes, and other constant declarations.</span></span>

<span data-ttu-id="5bc16-589">Come descritto in [espressioni costanti](expressions.md#constant-expressions), un *constant_expression* è un'espressione che può essere valutata interamente in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-589">As described in [Constant expressions](expressions.md#constant-expressions), a *constant_expression* is an expression that can be fully evaluated at compile-time.</span></span> <span data-ttu-id="5bc16-590">Poiché l'unico modo per creare un valore non null di un *reference_type* diverso da `string` consiste nell'applicare il `new` operatore e, poiché il `new` operatore non è consentito un *constant_ espressione*, l'unico valore possibile per le costanti dei *reference_type*s diverso `string` è `null`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-590">Since the only way to create a non-null value of a *reference_type* other than `string` is to apply the `new` operator, and since the `new` operator is not permitted in a *constant_expression*, the only possible value for constants of *reference_type*s other than `string` is `null`.</span></span>

<span data-ttu-id="5bc16-591">Quando si desidera usare un nome simbolico per un valore costante, ma quando il tipo di tale valore non è consentito in una dichiarazione di costante o quando il valore non può essere calcolato in fase di compilazione da un *constant_expression*, un `readonly` campo ( [Campi di sola lettura](classes.md#readonly-fields)) potrebbe invece essere utilizzato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-591">When a symbolic name for a constant value is desired, but when the type of that value is not permitted in a constant declaration, or when the value cannot be computed at compile-time by a *constant_expression*, a `readonly` field ([Readonly fields](classes.md#readonly-fields)) may be used instead.</span></span>

<span data-ttu-id="5bc16-592">Una dichiarazione di costante che dichiara più costanti equivale a più dichiarazioni di singole costanti con gli stessi attributi, i modificatori e tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-592">A constant declaration that declares multiple constants is equivalent to multiple declarations of single constants with the same attributes, modifiers, and type.</span></span> <span data-ttu-id="5bc16-593">Esempio:</span><span class="sxs-lookup"><span data-stu-id="5bc16-593">For example</span></span>
```csharp
class A
{
    public const double X = 1.0, Y = 2.0, Z = 3.0;
}
```
<span data-ttu-id="5bc16-594">equivale a</span><span class="sxs-lookup"><span data-stu-id="5bc16-594">is equivalent to</span></span>
```csharp
class A
{
    public const double X = 1.0;
    public const double Y = 2.0;
    public const double Z = 3.0;
}
```

<span data-ttu-id="5bc16-595">Le costanti possono dipendere da altri costanti all'interno del programma stesso, purché le dipendenze non sono di natura circolare.</span><span class="sxs-lookup"><span data-stu-id="5bc16-595">Constants are permitted to depend on other constants within the same program as long as the dependencies are not of a circular nature.</span></span> <span data-ttu-id="5bc16-596">Il compilatore vengono disposti automaticamente valutare le dichiarazioni costante nell'ordine appropriato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-596">The compiler automatically arranges to evaluate the constant declarations in the appropriate order.</span></span> <span data-ttu-id="5bc16-597">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-597">In the example</span></span>
```csharp
class A
{
    public const int X = B.Z + 1;
    public const int Y = 10;
}

class B
{
    public const int Z = A.Y + 1;
}
```
<span data-ttu-id="5bc16-598">il compilatore valuta innanzitutto `A.Y`, quindi valuta `B.Z`e infine restituisce `A.X`, restituendo i valori `10`, `11`, e `12`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-598">the compiler first evaluates `A.Y`, then evaluates `B.Z`, and finally evaluates `A.X`, producing the values `10`, `11`, and `12`.</span></span> <span data-ttu-id="5bc16-599">Le dichiarazioni di costante possono dipendere da costanti da un altro programma, ma queste dipendenze sono possibili solo in una sola direzione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-599">Constant declarations may depend on constants from other programs, but such dependencies are only possible in one direction.</span></span> <span data-ttu-id="5bc16-600">Che fa riferimento all'esempio precedente, se `A` e `B` sono stati dichiarati in programmi distinti, sarebbe possibile `A.X` dipendere `B.Z`, ma `B.Z` potrebbe quindi non simultaneamente dipendono da `A.Y`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-600">Referring to the example above, if `A` and `B` were declared in separate programs, it would be possible for `A.X` to depend on `B.Z`, but `B.Z` could then not simultaneously depend on `A.Y`.</span></span>

## <a name="fields"></a><span data-ttu-id="5bc16-601">Campi</span><span class="sxs-lookup"><span data-stu-id="5bc16-601">Fields</span></span>

<span data-ttu-id="5bc16-602">Oggetto ***campo*** è un membro che rappresenta una variabile associata a un oggetto o una classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-602">A ***field*** is a member that represents a variable associated with an object or class.</span></span> <span data-ttu-id="5bc16-603">Oggetto *field_declaration* introduce uno o più campi di un determinato tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-603">A *field_declaration* introduces one or more fields of a given type.</span></span>

```antlr
field_declaration
    : attributes? field_modifier* type variable_declarators ';'
    ;

field_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'readonly'
    | 'volatile'
    | field_modifier_unsafe
    ;

variable_declarators
    : variable_declarator (',' variable_declarator)*
    ;

variable_declarator
    : identifier ('=' variable_initializer)?
    ;

variable_initializer
    : expression
    | array_initializer
    ;
```

<span data-ttu-id="5bc16-604">Oggetto *field_declaration* può includere un set di *attributi* ([attributi](attributes.md)), un `new` modificatore ([il nuovo modificatore](classes.md#the-new-modifier)), un oggetto combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)) e una `static` modificatore ([campi statici e di istanza](classes.md#static-and-instance-fields)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-604">A *field_declaration* may include a set of *attributes* ([Attributes](attributes.md)), a `new` modifier ([The new modifier](classes.md#the-new-modifier)), a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), and a `static` modifier ([Static and instance fields](classes.md#static-and-instance-fields)).</span></span> <span data-ttu-id="5bc16-605">Inoltre, un *field_declaration* può includere una `readonly` modificatore ([campi di sola lettura](classes.md#readonly-fields)) o un `volatile` modificatore ([campi volatili](classes.md#volatile-fields)) ma non entrambi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-605">In addition, a *field_declaration* may include a `readonly` modifier ([Readonly fields](classes.md#readonly-fields)) or a `volatile` modifier ([Volatile fields](classes.md#volatile-fields)) but not both.</span></span> <span data-ttu-id="5bc16-606">Gli attributi e modificatori si applicano a tutti i membri dichiarati per il *field_declaration*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-606">The attributes and modifiers apply to all of the members declared by the *field_declaration*.</span></span> <span data-ttu-id="5bc16-607">È un errore per lo stesso modificatore venga visualizzato più volte in una dichiarazione di campo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-607">It is an error for the same modifier to appear multiple times in a field declaration.</span></span>

<span data-ttu-id="5bc16-608">Il *tipo* di un *field_declaration* specifica il tipo di membri introdotti dalla dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-608">The *type* of a *field_declaration* specifies the type of the members introduced by the declaration.</span></span> <span data-ttu-id="5bc16-609">Il tipo è seguito da un elenco delle *variable_declarator*s, ognuno dei quali presenta un nuovo membro.</span><span class="sxs-lookup"><span data-stu-id="5bc16-609">The type is followed by a list of *variable_declarator*s, each of which introduces a new member.</span></span> <span data-ttu-id="5bc16-610">Oggetto *variable_declarator* è costituito da un *identifier* che denomina tale membro, seguito facoltativamente da un "`=`" token e una *variable_initializer* ([ Gli inizializzatori di variabili](classes.md#variable-initializers)) che specifica il valore iniziale di tale membro.</span><span class="sxs-lookup"><span data-stu-id="5bc16-610">A *variable_declarator* consists of an *identifier* that names that member, optionally followed by an "`=`" token and a *variable_initializer* ([Variable initializers](classes.md#variable-initializers)) that gives the initial value of that member.</span></span>

<span data-ttu-id="5bc16-611">Il *tipo* di un campo deve essere accessibile almeno quanto il campo stesso ([vincoli accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-611">The *type* of a field must be at least as accessible as the field itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="5bc16-612">Viene ottenuto il valore di un campo in un'espressione che usa un' *simple_name* ([nomi semplici](expressions.md#simple-names)) o un *member_access* ([l'accesso ai membri](expressions.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-612">The value of a field is obtained in an expression using a *simple_name* ([Simple names](expressions.md#simple-names)) or a *member_access* ([Member access](expressions.md#member-access)).</span></span> <span data-ttu-id="5bc16-613">Il valore di un campo di sola lettura non viene modificato utilizzando un *assegnazione* ([gli operatori di assegnazione](expressions.md#assignment-operators)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-613">The value of a non-readonly field is modified using an *assignment* ([Assignment operators](expressions.md#assignment-operators)).</span></span> <span data-ttu-id="5bc16-614">Il valore di un campo di sola lettura non può essere ottenere e modificare l'utilizzo di incremento e decremento (operatori) ([forma suffissa di incremento e decremento (operatori)](expressions.md#postfix-increment-and-decrement-operators)) e incremento prefisso e decremento (operatori) ([prefisso gli operatori di incremento e decremento](expressions.md#prefix-increment-and-decrement-operators)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-614">The value of a non-readonly field can be both obtained and modified using postfix increment and decrement operators ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators)) and prefix increment and decrement operators ([Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)).</span></span>

<span data-ttu-id="5bc16-615">Una dichiarazione di campo che dichiara più campi è equivalente alla più dichiarazioni di singoli campi con gli stessi attributi, i modificatori e tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-615">A field declaration that declares multiple fields is equivalent to multiple declarations of single fields with the same attributes, modifiers, and type.</span></span> <span data-ttu-id="5bc16-616">Esempio:</span><span class="sxs-lookup"><span data-stu-id="5bc16-616">For example</span></span>
```csharp
class A
{
    public static int X = 1, Y, Z = 100;
}
```
<span data-ttu-id="5bc16-617">equivale a</span><span class="sxs-lookup"><span data-stu-id="5bc16-617">is equivalent to</span></span>
```csharp
class A
{
    public static int X = 1;
    public static int Y;
    public static int Z = 100;
}
```

### <a name="static-and-instance-fields"></a><span data-ttu-id="5bc16-618">Campi statici e di istanza</span><span class="sxs-lookup"><span data-stu-id="5bc16-618">Static and instance fields</span></span>

<span data-ttu-id="5bc16-619">Quando una dichiarazione di campo include un' `static` modificatore, i campi introdotti dalla dichiarazione sono ***i campi statici***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-619">When a field declaration includes a `static` modifier, the fields introduced by the declaration are ***static fields***.</span></span> <span data-ttu-id="5bc16-620">Se non si specifica `static` modificatore è presente, i campi introdotti dalla dichiarazione sono ***campi di istanza***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-620">When no `static` modifier is present, the fields introduced by the declaration are ***instance fields***.</span></span> <span data-ttu-id="5bc16-621">I campi statici e i campi di istanza sono due dei diversi tipi di variabili ([variabili](variables.md)) supportati da c#, e in alcuni casi vengono definiti per ***variabili statiche*** e ***le variabili di istanze*** , rispettivamente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-621">Static fields and instance fields are two of the several kinds of variables ([Variables](variables.md)) supported by C#, and at times they are referred to as ***static variables*** and ***instance variables***, respectively.</span></span>

<span data-ttu-id="5bc16-622">Un campo statico non fa parte di un'istanza specifica; In alternativa, è condiviso tra tutte le istanze di un tipo chiuso ([aperto e chiuso tipi](types.md#open-and-closed-types)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-622">A static field is not part of a specific instance; instead, it is shared amongst all instances of a closed type ([Open and closed types](types.md#open-and-closed-types)).</span></span> <span data-ttu-id="5bc16-623">Indipendentemente dal numero di istanze di un tipo di classe chiusi venga creato, è sempre un'unica copia di un campo statico per il dominio di applicazione associata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-623">No matter how many instances of a closed class type are created, there is only ever one copy of a static field for the associated application domain.</span></span>

<span data-ttu-id="5bc16-624">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5bc16-624">For example:</span></span>
```csharp
class C<V>
{
    static int count = 0;

    public C() {
        count++;
    }

    public static int Count {
        get { return count; }
    }
}

class Application
{
    static void Main() {
        C<int> x1 = new C<int>();
        Console.WriteLine(C<int>.Count);        // Prints 1

        C<double> x2 = new C<double>();
        Console.WriteLine(C<int>.Count);        // Prints 1

        C<int> x3 = new C<int>();
        Console.WriteLine(C<int>.Count);        // Prints 2
    }
}
```

<span data-ttu-id="5bc16-625">Un campo di istanza appartiene a un'istanza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-625">An instance field belongs to an instance.</span></span> <span data-ttu-id="5bc16-626">In particolare, ogni istanza di una classe contiene un set separato di tutti i campi di istanza di tale classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-626">Specifically, every instance of a class contains a separate set of all the instance fields of that class.</span></span>

<span data-ttu-id="5bc16-627">Quando si fa riferimento a un campo un *member_access* ([l'accesso ai membri](expressions.md#member-access)) nel formato `E.M`se `M` è un campo statico, `E` deve indicare un tipo contenente `M` e se `M` è un campo di istanza, È necessario che un'istanza di un tipo contenente `M`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-627">When a field is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, if `M` is a static field, `E` must denote a type containing `M`, and if `M` is an instance field, E must denote an instance of a type containing `M`.</span></span>

<span data-ttu-id="5bc16-628">Le differenze tra statica e i membri di istanza sono descritti dettagliatamente nella [i membri statici e di istanza](classes.md#static-and-instance-members).</span><span class="sxs-lookup"><span data-stu-id="5bc16-628">The differences between static and instance members are discussed further in [Static and instance members](classes.md#static-and-instance-members).</span></span>

### <a name="readonly-fields"></a><span data-ttu-id="5bc16-629">Campi di sola lettura</span><span class="sxs-lookup"><span data-stu-id="5bc16-629">Readonly fields</span></span>

<span data-ttu-id="5bc16-630">Quando un *field_declaration* include un `readonly` modificatore, i campi introdotti dalla dichiarazione sono ***campi di sola lettura***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-630">When a *field_declaration* includes a `readonly` modifier, the fields introduced by the declaration are ***readonly fields***.</span></span> <span data-ttu-id="5bc16-631">Le assegnazioni dirette a campi di sola lettura possono verificarsi solo come parte di tale dichiarazione o in un costruttore di istanza o un costruttore statico nella stessa classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-631">Direct assignments to readonly fields can only occur as part of that declaration or in an instance constructor or static constructor in the same class.</span></span> <span data-ttu-id="5bc16-632">(Un campo di sola lettura può essere assegnato a più volte in questi contesti.) In particolare, le assegnazioni dirette a un `readonly` campo sono consentiti solo nei contesti seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-632">(A readonly field can be assigned to multiple times in these contexts.) Specifically, direct assignments to a `readonly` field are permitted only in the following contexts:</span></span>

*  <span data-ttu-id="5bc16-633">Nel *variable_declarator* che introduce il campo (includendo un *variable_initializer* nella dichiarazione).</span><span class="sxs-lookup"><span data-stu-id="5bc16-633">In the *variable_declarator* that introduces the field (by including a *variable_initializer* in the declaration).</span></span>
*  <span data-ttu-id="5bc16-634">Per un campo di istanza, i costruttori di istanza della classe che contiene la dichiarazione di campo. per un campo statico, nel costruttore statico della classe che contiene la dichiarazione del campo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-634">For an instance field, in the instance constructors of the class that contains the field declaration; for a static field, in the static constructor of the class that contains the field declaration.</span></span> <span data-ttu-id="5bc16-635">Questi sono gli unici contesti in cui è possibile passare un `readonly` campo come un `out` o `ref` parametro.</span><span class="sxs-lookup"><span data-stu-id="5bc16-635">These are also the only contexts in which it is valid to pass a `readonly` field as an `out` or `ref` parameter.</span></span>

<span data-ttu-id="5bc16-636">Tentativo di assegnare a un `readonly` campo oppure passarlo come un' `out` o `ref` parametro in qualsiasi altro contesto è un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-636">Attempting to assign to a `readonly` field or pass it as an `out` or `ref` parameter in any other context is a compile-time error.</span></span>

#### <a name="using-static-readonly-fields-for-constants"></a><span data-ttu-id="5bc16-637">Uso di campi di sola lettura statico per le costanti</span><span class="sxs-lookup"><span data-stu-id="5bc16-637">Using static readonly fields for constants</span></span>

<span data-ttu-id="5bc16-638">Oggetto `static readonly` campo è utile quando si desidera ottenere un nome simbolico per un valore costante, ma quando il tipo di valore non è consentito un `const` dichiarazione, o quando il valore non può essere calcolato in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-638">A `static readonly` field is useful when a symbolic name for a constant value is desired, but when the type of the value is not permitted in a `const` declaration, or when the value cannot be computed at compile-time.</span></span> <span data-ttu-id="5bc16-639">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-639">In the example</span></span>
```csharp
public class Color
{
    public static readonly Color Black = new Color(0, 0, 0);
    public static readonly Color White = new Color(255, 255, 255);
    public static readonly Color Red = new Color(255, 0, 0);
    public static readonly Color Green = new Color(0, 255, 0);
    public static readonly Color Blue = new Color(0, 0, 255);

    private byte red, green, blue;

    public Color(byte r, byte g, byte b) {
        red = r;
        green = g;
        blue = b;
    }
}
```
<span data-ttu-id="5bc16-640">il `Black`, `White`, `Red`, `Green`, e `Blue` membri non possono essere dichiarate come `const` membri perché i relativi valori non possono essere calcolati in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-640">the `Black`, `White`, `Red`, `Green`, and `Blue` members cannot be declared as `const` members because their values cannot be computed at compile-time.</span></span> <span data-ttu-id="5bc16-641">Tuttavia, dichiararli `static readonly` dispone invece di ottenere lo stesso risultato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-641">However, declaring them `static readonly` instead has much the same effect.</span></span>

#### <a name="versioning-of-constants-and-static-readonly-fields"></a><span data-ttu-id="5bc16-642">Controllo delle versioni di costanti e i campi di sola lettura statico</span><span class="sxs-lookup"><span data-stu-id="5bc16-642">Versioning of constants and static readonly fields</span></span>

<span data-ttu-id="5bc16-643">Le costanti e i campi di sola lettura hanno una semantica di versioni binario diverso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-643">Constants and readonly fields have different binary versioning semantics.</span></span> <span data-ttu-id="5bc16-644">Quando un'espressione fa riferimento a una costante, il valore della costante viene ottenuto in fase di compilazione, ma quando un'espressione fa riferimento a un campo di sola lettura, fino alla fase di esecuzione non viene ottenuto il valore del campo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-644">When an expression references a constant, the value of the constant is obtained at compile-time, but when an expression references a readonly field, the value of the field is not obtained until run-time.</span></span> <span data-ttu-id="5bc16-645">Si consideri un'applicazione costituita da due programmi distinti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-645">Consider an application that consists of two separate programs:</span></span>
```csharp
using System;

namespace Program1
{
    public class Utils
    {
        public static readonly int X = 1;
    }
}

namespace Program2
{
    class Test
    {
        static void Main() {
            Console.WriteLine(Program1.Utils.X);
        }
    }
}
```

<span data-ttu-id="5bc16-646">Il `Program1` e `Program2` gli spazi dei nomi indicano due programmi che vengono compilati separatamente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-646">The `Program1` and `Program2` namespaces denote two programs that are compiled separately.</span></span> <span data-ttu-id="5bc16-647">In quanto `Program1.Utils.X` viene dichiarato come un campo statico di sola lettura, il valore restituito dal `Console.WriteLine` istruzione non è noto in fase di compilazione, ma viene ottenuta in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-647">Because `Program1.Utils.X` is declared as a static readonly field, the value output by the `Console.WriteLine` statement is not known at compile-time, but rather is obtained at run-time.</span></span> <span data-ttu-id="5bc16-648">Di conseguenza, se il valore di `X` viene modificato e `Program1` viene ricompilata, il `Console.WriteLine` istruzione restituirà il nuovo valore anche se `Program2` non viene ricompilato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-648">Thus, if the value of `X` is changed and `Program1` is recompiled, the `Console.WriteLine` statement will output the new value even if `Program2` isn't recompiled.</span></span> <span data-ttu-id="5bc16-649">Tuttavia, presentano `X` una costante, il valore di stato `X` sarebbe stato ottenuto al momento `Program2` è stato compilato e verrebbe influenzato dalle modifiche apportate in `Program1` fino a quando non `Program2` viene ricompilato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-649">However, had `X` been a constant, the value of `X` would have been obtained at the time `Program2` was compiled, and would remain unaffected by changes in `Program1` until `Program2` is recompiled.</span></span>

### <a name="volatile-fields"></a><span data-ttu-id="5bc16-650">Campi volatili</span><span class="sxs-lookup"><span data-stu-id="5bc16-650">Volatile fields</span></span>

<span data-ttu-id="5bc16-651">Quando un *field_declaration* include un `volatile` sono campi introdotte dalla dichiarazione di tale modificatore ***campi volatili***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-651">When a *field_declaration* includes a `volatile` modifier, the fields introduced by that declaration are ***volatile fields***.</span></span>

<span data-ttu-id="5bc16-652">Per i campi non volatile, tecniche di ottimizzazione di riordinare le istruzioni possono portare a risultati imprevisti e imprevedibili nei programmi multithread che accedono ai campi senza la sincronizzazione, ad esempio quello fornito dal *lock_statement*  ([Istruzione lock](statements.md#the-lock-statement)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-652">For non-volatile fields, optimization techniques that reorder instructions can lead to unexpected and unpredictable results in multi-threaded programs that access fields without synchronization such as that provided by the *lock_statement* ([The lock statement](statements.md#the-lock-statement)).</span></span> <span data-ttu-id="5bc16-653">Queste ottimizzazioni possono essere eseguite dal compilatore, dal sistema di runtime o dall'hardware.</span><span class="sxs-lookup"><span data-stu-id="5bc16-653">These optimizations can be performed by the compiler, by the run-time system, or by hardware.</span></span> <span data-ttu-id="5bc16-654">Per i campi volatili, le ottimizzazioni di riordino sono limitate:</span><span class="sxs-lookup"><span data-stu-id="5bc16-654">For volatile fields, such reordering optimizations are restricted:</span></span>

*  <span data-ttu-id="5bc16-655">La lettura di un campo volatile viene definita un' ***lettura volatile***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-655">A read of a volatile field is called a ***volatile read***.</span></span> <span data-ttu-id="5bc16-656">Dispone di un'operazione di lettura volatile "acquisire semantica"; vale a dire, è garantito a verificarsi prima di tutti i riferimenti alla memoria che si verificano dopo di essa nella sequenza di istruzioni.</span><span class="sxs-lookup"><span data-stu-id="5bc16-656">A volatile read has "acquire semantics"; that is, it is guaranteed to occur prior to any references to memory that occur after it in the instruction sequence.</span></span>
*  <span data-ttu-id="5bc16-657">La scrittura di un campo volatile viene definita un' ***scrittura di tipo volatile***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-657">A write of a volatile field is called a ***volatile write***.</span></span> <span data-ttu-id="5bc16-658">Scrittura volatile è "la semantica di rilascio"; vale a dire, è garantito a verificarsi dopo qualsiasi riferimento alla memoria prima le istruzioni di scrittura nella sequenza di istruzioni.</span><span class="sxs-lookup"><span data-stu-id="5bc16-658">A volatile write has "release semantics"; that is, it is guaranteed to happen after any memory references prior to the write instruction in the instruction sequence.</span></span>

<span data-ttu-id="5bc16-659">Queste limitazioni garantiscono che tutti i thread considereranno scritture di tipo volatile eseguite da altri thread nell'ordine in cui sono stati eseguiti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-659">These restrictions ensure that all threads will observe volatile writes performed by any other thread in the order in which they were performed.</span></span> <span data-ttu-id="5bc16-660">Un'implementazione conforme, non è necessario fornire un singolo ordinamento totale di scritture volatili visualizzate da tutti i thread di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-660">A conforming implementation is not required to provide a single total ordering of volatile writes as seen from all threads of execution.</span></span> <span data-ttu-id="5bc16-661">Il tipo di un campo volatile deve essere uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-661">The type of a volatile field must be one of the following:</span></span>

*  <span data-ttu-id="5bc16-662">A *reference_type*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-662">A *reference_type*.</span></span>
*  <span data-ttu-id="5bc16-663">Il tipo `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `char`, `float`, `bool`, `System.IntPtr`, o` System.UIntPtr`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-663">The type `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `char`, `float`, `bool`, `System.IntPtr`, or` System.UIntPtr`.</span></span>
*  <span data-ttu-id="5bc16-664">Un' *enum_type* specificando un tipo di base di enumerazione del `byte`, `sbyte`, `short`, `ushort`, `int`, o `uint`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-664">An *enum_type* having an enum base type of `byte`, `sbyte`, `short`, `ushort`, `int`, or `uint`.</span></span>

<span data-ttu-id="5bc16-665">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-665">The example</span></span>
```csharp
using System;
using System.Threading;

class Test
{
    public static int result;   
    public static volatile bool finished;

    static void Thread2() {
        result = 143;    
        finished = true; 
    }

    static void Main() {
        finished = false;

        // Run Thread2() in a new thread
        new Thread(new ThreadStart(Thread2)).Start();

        // Wait for Thread2 to signal that it has a result by setting
        // finished to true.
        for (;;) {
            if (finished) {
                Console.WriteLine("result = {0}", result);
                return;
            }
        }
    }
}
```
<span data-ttu-id="5bc16-666">produce l'output:</span><span class="sxs-lookup"><span data-stu-id="5bc16-666">produces the output:</span></span>
```
result = 143
```

<span data-ttu-id="5bc16-667">In questo esempio, il metodo `Main` avvia un nuovo thread che esegue il metodo `Thread2`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-667">In this example, the method `Main` starts a new thread that runs the method `Thread2`.</span></span> <span data-ttu-id="5bc16-668">Questo metodo archivia un valore in un campo volatile non chiamato `result`, quindi lo archivia `true` nel campo volatile `finished`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-668">This method stores a value into a non-volatile field called `result`, then stores `true` in the volatile field `finished`.</span></span> <span data-ttu-id="5bc16-669">Il thread principale attende che il campo `finished` sia impostato su `true`, quindi legge il campo `result`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-669">The main thread waits for the field `finished` to be set to `true`, then reads the field `result`.</span></span> <span data-ttu-id="5bc16-670">Poiché `finished` è stata dichiarata `volatile`, il thread principale deve leggere il valore `143` dal campo `result`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-670">Since `finished` has been declared `volatile`, the main thread must read the value `143` from the field `result`.</span></span> <span data-ttu-id="5bc16-671">Se il campo `finished` non fosse stato dichiarato `volatile`, sarà consentito per l'archivio di `result` deve essere visibile per il thread principale dopo l'archiviazione `finished`e pertanto per il thread principale leggere il valore `0` dal campo `result`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-671">If the field `finished` had not been declared `volatile`, then it would be permissible for the store to `result` to be visible to the main thread after the store to `finished`, and hence for the main thread to read the value `0` from the field `result`.</span></span> <span data-ttu-id="5bc16-672">La dichiarazione `finished` come un `volatile` campo impedisce qualsiasi tali incoerenze.</span><span class="sxs-lookup"><span data-stu-id="5bc16-672">Declaring `finished` as a `volatile` field prevents any such inconsistency.</span></span>

### <a name="field-initialization"></a><span data-ttu-id="5bc16-673">Inizializzazione del campo</span><span class="sxs-lookup"><span data-stu-id="5bc16-673">Field initialization</span></span>

<span data-ttu-id="5bc16-674">Il valore iniziale di un campo, che si tratti di un campo statico o un campo di istanza, è il valore predefinito ([i valori predefiniti](variables.md#default-values)) del tipo del campo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-674">The initial value of a field, whether it be a static field or an instance field, is the default value ([Default values](variables.md#default-values)) of the field's type.</span></span> <span data-ttu-id="5bc16-675">Non è possibile osservare il valore di un campo prima che si è verificato l'inizializzazione predefinita e un campo non è pertanto mai "inizializzato".</span><span class="sxs-lookup"><span data-stu-id="5bc16-675">It is not possible to observe the value of a field before this default initialization has occurred, and a field is thus never "uninitialized".</span></span> <span data-ttu-id="5bc16-676">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-676">The example</span></span>
```csharp
using System;

class Test
{
    static bool b;
    int i;

    static void Main() {
        Test t = new Test();
        Console.WriteLine("b = {0}, i = {1}", b, t.i);
    }
}
```
<span data-ttu-id="5bc16-677">produce l'output</span><span class="sxs-lookup"><span data-stu-id="5bc16-677">produces the output</span></span>
```
b = False, i = 0
```
<span data-ttu-id="5bc16-678">in quanto `b` e `i` vengono automaticamente inizializzate entrambe su valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-678">because `b` and `i` are both automatically initialized to default values.</span></span>

### <a name="variable-initializers"></a><span data-ttu-id="5bc16-679">Inizializzatori di variabili</span><span class="sxs-lookup"><span data-stu-id="5bc16-679">Variable initializers</span></span>

<span data-ttu-id="5bc16-680">Le dichiarazioni di campo possono includere *variable_initializer*s.</span><span class="sxs-lookup"><span data-stu-id="5bc16-680">Field declarations may include *variable_initializer*s.</span></span> <span data-ttu-id="5bc16-681">Per i campi statici, gli inizializzatori di variabili corrispondono alle istruzioni di assegnazione eseguite durante l'inizializzazione delle classi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-681">For static fields, variable initializers correspond to assignment statements that are executed during class initialization.</span></span> <span data-ttu-id="5bc16-682">Ad esempio campi, gli inizializzatori di variabili corrispondono alle istruzioni di assegnazione che vengono eseguite quando viene creata un'istanza della classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-682">For instance fields, variable initializers correspond to assignment statements that are executed when an instance of the class is created.</span></span>

<span data-ttu-id="5bc16-683">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-683">The example</span></span>
```csharp
using System;

class Test
{
    static double x = Math.Sqrt(2.0);
    int i = 100;
    string s = "Hello";

    static void Main() {
        Test a = new Test();
        Console.WriteLine("x = {0}, i = {1}, s = {2}", x, a.i, a.s);
    }
}
```
<span data-ttu-id="5bc16-684">produce l'output</span><span class="sxs-lookup"><span data-stu-id="5bc16-684">produces the output</span></span>
```
x = 1.4142135623731, i = 100, s = Hello
```
<span data-ttu-id="5bc16-685">perché un'assegnazione a `x` si verifica quando vengono eseguiti gli inizializzatori di campo statico e le assegnazioni ai `i` e `s` quando vengono eseguiti gli inizializzatori di campo di istanza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-685">because an assignment to `x` occurs when static field initializers execute and assignments to `i` and `s` occur when the instance field initializers execute.</span></span>

<span data-ttu-id="5bc16-686">Inizializzazione del valore predefinito descritto nella [inizializzazione del campo](classes.md#field-initialization) si verifica per tutti i campi, inclusi i campi con inizializzatori.</span><span class="sxs-lookup"><span data-stu-id="5bc16-686">The default value initialization described in [Field initialization](classes.md#field-initialization) occurs for all fields, including fields that have variable initializers.</span></span> <span data-ttu-id="5bc16-687">Di conseguenza, quando una classe viene inizializzata, tutti i campi statici nella classe vengono inizializzati sui valori predefiniti e quindi gli inizializzatori di campo statico vengono eseguiti in ordine testuale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-687">Thus, when a class is initialized, all static fields in that class are first initialized to their default values, and then the static field initializers are executed in textual order.</span></span> <span data-ttu-id="5bc16-688">Analogamente, quando viene creata un'istanza di una classe, tutti i campi di istanza in quell'istanza vengono innanzitutto inizializzati sui valori predefiniti e quindi gli inizializzatori di campo di istanza vengono eseguiti in ordine testuale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-688">Likewise, when an instance of a class is created, all instance fields in that instance are first initialized to their default values, and then the instance field initializers are executed in textual order.</span></span>

<span data-ttu-id="5bc16-689">È possibile che i campi statici con gli inizializzatori di variabili da osservare nel relativo stato di valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-689">It is possible for static fields with variable initializers to be observed in their default value state.</span></span> <span data-ttu-id="5bc16-690">Tuttavia, ciò è fortemente sconsigliato come una questione di stile.</span><span class="sxs-lookup"><span data-stu-id="5bc16-690">However, this is strongly discouraged as a matter of style.</span></span> <span data-ttu-id="5bc16-691">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-691">The example</span></span>
```csharp
using System;

class Test
{
    static int a = b + 1;
    static int b = a + 1;

    static void Main() {
        Console.WriteLine("a = {0}, b = {1}", a, b);
    }
}
```
<span data-ttu-id="5bc16-692">Mostra questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-692">exhibits this behavior.</span></span> <span data-ttu-id="5bc16-693">Nonostante le definizioni circolari di una e b, il programma è valido.</span><span class="sxs-lookup"><span data-stu-id="5bc16-693">Despite the circular definitions of a and b, the program is valid.</span></span> <span data-ttu-id="5bc16-694">Il risultato è l'output</span><span class="sxs-lookup"><span data-stu-id="5bc16-694">It results in the output</span></span>
```
a = 1, b = 2
```
<span data-ttu-id="5bc16-695">Poiché i campi statici `a` e `b` vengono inizializzati `0` (il valore predefinito per `int`) prima dell'esecuzione dei relativi inizializzatori.</span><span class="sxs-lookup"><span data-stu-id="5bc16-695">because the static fields `a` and `b` are initialized to `0` (the default value for `int`) before their initializers are executed.</span></span> <span data-ttu-id="5bc16-696">Quando l'inizializzatore per `a` viene eseguito, il valore di `b` è uguale a zero e pertanto `a` viene inizializzata su `1`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-696">When the initializer for `a` runs, the value of `b` is zero, and so `a` is initialized to `1`.</span></span> <span data-ttu-id="5bc16-697">Quando l'inizializzatore per `b` viene eseguito, il valore di `a` già `1`e pertanto `b` viene inizializzato su `2`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-697">When the initializer for `b` runs, the value of `a` is already `1`, and so `b` is initialized to `2`.</span></span>

#### <a name="static-field-initialization"></a><span data-ttu-id="5bc16-698">Inizializzazione del campo statico</span><span class="sxs-lookup"><span data-stu-id="5bc16-698">Static field initialization</span></span>

<span data-ttu-id="5bc16-699">Inizializzatori di campo statico di una classe corrispondono a una sequenza di assegnazioni che vengono eseguite nell'ordine testuale in cui appaiono nella dichiarazione di classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-699">The static field variable initializers of a class correspond to a sequence of assignments that are executed in the textual order in which they appear in the class declaration.</span></span> <span data-ttu-id="5bc16-700">Se un costruttore statico ([costruttori statici](classes.md#static-constructors)) è presente nella classe, gli inizializzatori di campo statico viene eseguito immediatamente prima dell'esecuzione di tale costruttore statico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-700">If a static constructor ([Static constructors](classes.md#static-constructors)) exists in the class, execution of the static field initializers occurs immediately prior to executing that static constructor.</span></span> <span data-ttu-id="5bc16-701">In caso contrario, gli inizializzatori di campo statico vengono eseguiti in un momento dipende dall'implementazione del primo utilizzo di un campo statico di tale classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-701">Otherwise, the static field initializers are executed at an implementation-dependent time prior to the first use of a static field of that class.</span></span> <span data-ttu-id="5bc16-702">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-702">The example</span></span>
```csharp
using System;

class Test 
{ 
    static void Main() {
        Console.WriteLine("{0} {1}", B.Y, A.X);
    }

    public static int F(string s) {
        Console.WriteLine(s);
        return 1;
    }
}

class A
{
    public static int X = Test.F("Init A");
}

class B
{
    public static int Y = Test.F("Init B");
}
```
<span data-ttu-id="5bc16-703">potrebbe produrre l'output:</span><span class="sxs-lookup"><span data-stu-id="5bc16-703">might produce either the output:</span></span>
```
Init A
Init B
1 1
```
<span data-ttu-id="5bc16-704">In alternativa l'output:</span><span class="sxs-lookup"><span data-stu-id="5bc16-704">or the output:</span></span>
```
Init B
Init A
1 1
```
<span data-ttu-id="5bc16-705">Poiché l'esecuzione di `X`dell'inizializzatore e `Y`dell'inizializzatore può verificarsi in qualsiasi ordine; se restano validi solo prima i riferimenti ai campi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-705">because the execution of `X`'s initializer and `Y`'s initializer could occur in either order; they are only constrained to occur before the references to those fields.</span></span> <span data-ttu-id="5bc16-706">Tuttavia, nell'esempio:</span><span class="sxs-lookup"><span data-stu-id="5bc16-706">However, in the example:</span></span>
```csharp
using System;

class Test
{
    static void Main() {
        Console.WriteLine("{0} {1}", B.Y, A.X);
    }

    public static int F(string s) {
        Console.WriteLine(s);
        return 1;
    }
}

class A
{
    static A() {}

    public static int X = Test.F("Init A");
}

class B
{
    static B() {}

    public static int Y = Test.F("Init B");
}
```
<span data-ttu-id="5bc16-707">l'output deve essere:</span><span class="sxs-lookup"><span data-stu-id="5bc16-707">the output must be:</span></span>
```
Init B
Init A
1 1
```
<span data-ttu-id="5bc16-708">Poiché le regole relative all'esecuzione dei costruttori statici (come definito in [costruttori statici](classes.md#static-constructors)) che forniscono `B`del costruttore statico (e pertanto `B`degli inizializzatori di campo statico) deve essere eseguito prima `A`del costruttore statico e gli inizializzatori di campo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-708">because the rules for when static constructors execute (as defined in [Static constructors](classes.md#static-constructors)) provide that `B`'s static constructor (and hence `B`'s static field initializers) must run before `A`'s static constructor and field initializers.</span></span>

#### <a name="instance-field-initialization"></a><span data-ttu-id="5bc16-709">Inizializzazione di campi di istanza</span><span class="sxs-lookup"><span data-stu-id="5bc16-709">Instance field initialization</span></span>

<span data-ttu-id="5bc16-710">Gli inizializzatori variabile di campo di istanza di una classe corrispondono a una sequenza di assegnazioni che vengono eseguiti immediatamente dopo l'inserimento di uno dei costruttori di istanza di ([inizializzatori del costruttore](classes.md#constructor-initializers)) di tale classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-710">The instance field variable initializers of a class correspond to a sequence of assignments that are executed immediately upon entry to any one of the instance constructors ([Constructor initializers](classes.md#constructor-initializers)) of that class.</span></span> <span data-ttu-id="5bc16-711">Inizializzatori vengono eseguiti nell'ordine testuale in cui appaiono nella dichiarazione di classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-711">The variable initializers are executed in the textual order in which they appear in the class declaration.</span></span> <span data-ttu-id="5bc16-712">Il processo di creazione e inizializzazione dell'istanza classe è descritto più dettagliatamente in [costruttori di istanze](classes.md#instance-constructors).</span><span class="sxs-lookup"><span data-stu-id="5bc16-712">The class instance creation and initialization process is described further in [Instance constructors](classes.md#instance-constructors).</span></span>

<span data-ttu-id="5bc16-713">Un inizializzatore di variabile per un campo di istanza non può fare riferimento all'istanza creata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-713">A variable initializer for an instance field cannot reference the instance being created.</span></span> <span data-ttu-id="5bc16-714">Di conseguenza, è un errore in fase di compilazione per fare riferimento a `this` in un inizializzatore di variabile, perché è un errore in fase di compilazione per un inizializzatore di variabile fare riferimento a un membro di istanza tramite un *simple_name*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-714">Thus, it is a compile-time error to reference `this` in a variable initializer, as it is a compile-time error for a variable initializer to reference any instance member through a *simple_name*.</span></span> <span data-ttu-id="5bc16-715">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-715">In the example</span></span>
```csharp
class A
{
    int x = 1;
    int y = x + 1;        // Error, reference to instance member of this
}
```
<span data-ttu-id="5bc16-716">l'inizializzatore di variabile `y` comporta un errore in fase di compilazione perché fa riferimento a un membro dell'istanza da creare.</span><span class="sxs-lookup"><span data-stu-id="5bc16-716">the variable initializer for `y` results in a compile-time error because it references a member of the instance being created.</span></span>

## <a name="methods"></a><span data-ttu-id="5bc16-717">Metodi</span><span class="sxs-lookup"><span data-stu-id="5bc16-717">Methods</span></span>

<span data-ttu-id="5bc16-718">Un ***metodo*** è un membro che implementa un calcolo o un'azione che può essere eseguita da un oggetto o una classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-718">A ***method*** is a member that implements a computation or action that can be performed by an object or class.</span></span> <span data-ttu-id="5bc16-719">I metodi vengono dichiarati usando *method_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="5bc16-719">Methods are declared using *method_declaration*s:</span></span>

```antlr
method_declaration
    : method_header method_body
    ;

method_header
    : attributes? method_modifier* 'partial'? return_type member_name type_parameter_list?
      '(' formal_parameter_list? ')' type_parameter_constraints_clause*
    ;

method_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | 'async'
    | method_modifier_unsafe
    ;

return_type
    : type
    | 'void'
    ;

member_name
    : identifier
    | interface_type '.' identifier
    ;

method_body
    : block
    | '=>' expression ';'
    | ';'
    ;
```

<span data-ttu-id="5bc16-720">Oggetto *method_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso ](classes.md#access-modifiers)), il `new` ([il nuovo modificatore](classes.md#the-new-modifier)), `static` ([metodi statici e di istanza](classes.md#static-and-instance-methods)), `virtual` ([metodi virtuali](classes.md#virtual-methods)), `override` ([Metodi di override](classes.md#override-methods)), `sealed` ([Sealed i metodi](classes.md#sealed-methods)), `abstract` ([metodi astratti](classes.md#abstract-methods)), e `extern` ([Metodi esterni](classes.md#external-methods)) modificatori.</span><span class="sxs-lookup"><span data-stu-id="5bc16-720">A *method_declaration* may include a set of *attributes* ([Attributes](attributes.md)) and a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), the `new` ([The new modifier](classes.md#the-new-modifier)),  `static` ([Static and instance methods](classes.md#static-and-instance-methods)), `virtual` ([Virtual methods](classes.md#virtual-methods)), `override` ([Override methods](classes.md#override-methods)), `sealed` ([Sealed methods](classes.md#sealed-methods)), `abstract` ([Abstract methods](classes.md#abstract-methods)), and `extern` ([External methods](classes.md#external-methods)) modifiers.</span></span>

<span data-ttu-id="5bc16-721">Una dichiarazione contiene una combinazione valida dei modificatori se tutti gli elementi seguenti sono vere:</span><span class="sxs-lookup"><span data-stu-id="5bc16-721">A declaration has a valid combination of modifiers if all of the following are true:</span></span>

*  <span data-ttu-id="5bc16-722">La dichiarazione include una combinazione valida dei modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-722">The declaration includes a valid combination of access modifiers ([Access modifiers](classes.md#access-modifiers)).</span></span>
*  <span data-ttu-id="5bc16-723">La dichiarazione non include più volte lo stesso modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-723">The declaration does not include the same modifier multiple times.</span></span>
*  <span data-ttu-id="5bc16-724">La dichiarazione include al massimo uno dei modificatori seguenti: `static`, `virtual`, e `override`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-724">The declaration includes at most one of the following modifiers: `static`, `virtual`, and `override`.</span></span>
*  <span data-ttu-id="5bc16-725">La dichiarazione include al massimo uno dei modificatori seguenti: `new` e `override`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-725">The declaration includes at most one of the following modifiers: `new` and `override`.</span></span>
*  <span data-ttu-id="5bc16-726">Se la dichiarazione include il `abstract` modificatore, quindi la dichiarazione di non includere uno qualsiasi dei modificatori seguenti: `static`, `virtual`, `sealed` o `extern`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-726">If the declaration includes the `abstract` modifier, then the declaration does not include any of the following modifiers: `static`, `virtual`, `sealed` or `extern`.</span></span>
*  <span data-ttu-id="5bc16-727">Se la dichiarazione include il `private` modificatore, quindi la dichiarazione di non includere uno qualsiasi dei modificatori seguenti: `virtual`, `override`, o `abstract`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-727">If the declaration includes the `private` modifier, then the declaration does not include any of the following modifiers: `virtual`, `override`, or `abstract`.</span></span>
*  <span data-ttu-id="5bc16-728">Se la dichiarazione include il `sealed` modificatore, quindi la dichiarazione include anche il `override` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-728">If the declaration includes the `sealed` modifier, then the declaration also includes the `override` modifier.</span></span>
*  <span data-ttu-id="5bc16-729">Se la dichiarazione include il `partial` modificatore, quindi, non include uno dei modificatori seguenti: `new`, `public`, `protected`, `internal`, `private`, `virtual`, `sealed`, `override` , `abstract`, o `extern`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-729">If the declaration includes the `partial` modifier, then it does not include any of the following modifiers: `new`, `public`, `protected`, `internal`, `private`, `virtual`, `sealed`, `override`, `abstract`, or `extern`.</span></span>

<span data-ttu-id="5bc16-730">Un metodo che contiene il `async` modificatore è una funzione asincrona e segue le regole descritte in [le funzioni asincrone](classes.md#async-functions).</span><span class="sxs-lookup"><span data-stu-id="5bc16-730">A method that has the `async` modifier is an async function and follows the rules described in [Async functions](classes.md#async-functions).</span></span>

<span data-ttu-id="5bc16-731">Il *return_type* di un metodo dichiarazione specifica il tipo di valore calcolato e restituito dal metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-731">The *return_type* of a method declaration specifies the type of the value computed and returned by the method.</span></span> <span data-ttu-id="5bc16-732">Il *return_type* è `void` se il metodo non restituisce un valore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-732">The *return_type* is `void` if the method does not return a value.</span></span> <span data-ttu-id="5bc16-733">Se la dichiarazione include il `partial` modificatore, il tipo restituito deve essere `void`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-733">If the declaration includes the `partial` modifier, then the return type must be `void`.</span></span>

<span data-ttu-id="5bc16-734">Il *member_name* specifica il nome del metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-734">The *member_name* specifies the name of the method.</span></span> <span data-ttu-id="5bc16-735">A meno che il metodo è un'implementazione di membro di interfaccia esplicita ([tali implementazioni](interfaces.md#explicit-interface-member-implementations)), il *member_name* è semplicemente un' *identificatore*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-735">Unless the method is an explicit interface member implementation ([Explicit interface member implementations](interfaces.md#explicit-interface-member-implementations)), the *member_name* is simply an *identifier*.</span></span> <span data-ttu-id="5bc16-736">Per un'implementazione esplicita dell'interfaccia membro, il *member_name* costituito da un *interface_type* seguita da un "`.`" e una *identificatore*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-736">For an explicit interface member implementation, the *member_name* consists of an *interface_type* followed by a "`.`" and an *identifier*.</span></span>

<span data-ttu-id="5bc16-737">L'opzione facoltativa *type_parameter_list* specifica i parametri di tipo del metodo ([parametri di tipo](classes.md#type-parameters)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-737">The optional *type_parameter_list* specifies the type parameters of the method ([Type parameters](classes.md#type-parameters)).</span></span> <span data-ttu-id="5bc16-738">Se un *type_parameter_list* viene specificato il metodo è un ***metodo generico***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-738">If a *type_parameter_list* is specified the method is a ***generic method***.</span></span> <span data-ttu-id="5bc16-739">Se il metodo ha un `extern` modificatore, un' *type_parameter_list* non può essere specificato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-739">If the method has an `extern` modifier, a *type_parameter_list* cannot be specified.</span></span>

<span data-ttu-id="5bc16-740">L'opzione facoltativa *formal_parameter_list* specifica i parametri del metodo ([i parametri del metodo](classes.md#method-parameters)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-740">The optional *formal_parameter_list* specifies the parameters of the method ([Method parameters](classes.md#method-parameters)).</span></span>

<span data-ttu-id="5bc16-741">L'opzione facoltativa *type_parameter_constraints_clause*specificano i vincoli sui parametri di tipo individuale ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)) e può essere specificato solo se un *type_parameter_ elenco* viene indicato anche, e il metodo non ha un `override` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-741">The optional *type_parameter_constraints_clause*s specify constraints on individual type parameters ([Type parameter constraints](classes.md#type-parameter-constraints)) and may only be specified if a *type_parameter_list* is also supplied, and the method does not have an `override` modifier.</span></span>

<span data-ttu-id="5bc16-742">Il *return_type* e ognuno dei tipi a cui fa riferimento il *formal_parameter_list* di un metodo deve essere accessibile almeno quanto il metodo di stesso ([vincoli accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-742">The *return_type* and each of the types referenced in the *formal_parameter_list* of a method must be at least as accessible as the method itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="5bc16-743">Il *method_body* entrambi un punto e virgola, una ***corpo dell'istruzione*** o un ***corpo dell'espressione***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-743">The *method_body* is either a semicolon, a ***statement body*** or an ***expression body***.</span></span> <span data-ttu-id="5bc16-744">È costituito da un corpo dell'istruzione di un *blocco*, che consente di specificare le istruzioni da eseguire quando viene richiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-744">A statement body consists of a *block*, which specifies the statements to execute when the method is invoked.</span></span> <span data-ttu-id="5bc16-745">Costituito da un corpo di espressione `=>` seguita da un' *espressione* e un punto e virgola e indica una singola espressione da eseguire quando viene richiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-745">An expression body consists of `=>` followed by an *expression* and a semicolon, and denotes a single expression to perform when the method is invoked.</span></span> 

<span data-ttu-id="5bc16-746">Per la `abstract` e `extern` metodi, il *method_body* costituito semplicemente da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-746">For `abstract` and `extern` methods, the *method_body* consists simply of a semicolon.</span></span> <span data-ttu-id="5bc16-747">Per la `partial` metodi di *method_body* può essere costituita da un punto e virgola, un corpo del blocco o un corpo di espressione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-747">For `partial` methods the *method_body* may consist of either a semicolon, a block body or an expression body.</span></span> <span data-ttu-id="5bc16-748">Per tutti gli altri metodi, il *method_body* è un corpo del blocco o un corpo di espressione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-748">For all other methods, the *method_body* is either a block body or an expression body.</span></span>

<span data-ttu-id="5bc16-749">Se il *method_body* costituito da un punto e virgola, quindi la dichiarazione non può includere il `async` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-749">If the *method_body* consists of a semicolon, then the declaration may not include the `async` modifier.</span></span>

<span data-ttu-id="5bc16-750">Il nome, l'elenco di parametri di tipo e l'elenco di parametri formali di un metodo di definire la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) del metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-750">The name, the type parameter list and the formal parameter list of a method define the signature ([Signatures and overloading](basic-concepts.md#signatures-and-overloading)) of the method.</span></span> <span data-ttu-id="5bc16-751">In particolare, la firma di un metodo costituita dal relativo nome, il numero di parametri di tipo e il numero, i modificatori e i tipi di parametri formali.</span><span class="sxs-lookup"><span data-stu-id="5bc16-751">Specifically, the signature of a method consists of its name, the number of type parameters and the number, modifiers, and types of its formal parameters.</span></span> <span data-ttu-id="5bc16-752">A tale scopo, viene identificato qualsiasi parametro di tipo del metodo che si verifica nel tipo di un parametro formale non in base al nome, ma in base alla posizione ordinale nell'elenco di argomenti tipo del metodo. Il tipo restituito non fa parte della firma del metodo, né è i nomi dei parametri di tipo o i parametri formali.</span><span class="sxs-lookup"><span data-stu-id="5bc16-752">For these purposes, any type parameter of the method that occurs in the type of a formal parameter is identified not by its name, but by its ordinal position in the type argument list of the method.The return type is not part of a method's signature, nor are the names of the type parameters or the formal parameters.</span></span>

<span data-ttu-id="5bc16-753">Il nome di un metodo deve essere diverso dai nomi di tutti gli altri metodi-non dichiarati nella stessa classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-753">The name of a method must differ from the names of all other non-methods declared in the same class.</span></span> <span data-ttu-id="5bc16-754">Inoltre, la firma di un metodo deve essere diversa dalle firme di tutti gli altri metodi dichiarati nella stessa classe, e due metodi dichiarati nella stessa classe potrebbero non avere firme che differiscono dal `ref` e `out`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-754">In addition, the signature of a method must differ from the signatures of all other methods declared in the same class, and two methods declared in the same class may not have signatures that differ solely by `ref` and `out`.</span></span>

<span data-ttu-id="5bc16-755">Il metodo *type_parameter*sono nell'ambito in tutta il *method_declaration*e può essere utilizzato per formare tipi in tale ambito nel *return_type*, *method_body*, e *type_parameter_constraints_clause*s ma non nella *attributi*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-755">The method's *type_parameter*s are in scope throughout the *method_declaration*, and can be used to form types throughout that scope in *return_type*, *method_body*, and *type_parameter_constraints_clause*s but not in *attributes*.</span></span>

<span data-ttu-id="5bc16-756">Tutti i parametri formali e parametri di tipo devono avere nomi diversi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-756">All formal parameters and type parameters must have different names.</span></span>

### <a name="method-parameters"></a><span data-ttu-id="5bc16-757">Parametri del metodo</span><span class="sxs-lookup"><span data-stu-id="5bc16-757">Method parameters</span></span>

<span data-ttu-id="5bc16-758">I parametri di un metodo, se presenti, vengono dichiarati per il metodo *formal_parameter_list*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-758">The parameters of a method, if any, are declared by the method's *formal_parameter_list*.</span></span>

```antlr
formal_parameter_list
    : fixed_parameters
    | fixed_parameters ',' parameter_array
    | parameter_array
    ;

fixed_parameters
    : fixed_parameter (',' fixed_parameter)*
    ;

fixed_parameter
    : attributes? parameter_modifier? type identifier default_argument?
    ;

default_argument
    : '=' expression
    ;

parameter_modifier
    : 'ref'
    | 'out'
    | 'this'
    ;

parameter_array
    : attributes? 'params' array_type identifier
    ;
```

<span data-ttu-id="5bc16-759">Elenco di parametri formali è costituito da uno o più parametri delimitato da virgole che può essere solo l'ultimo un *parameter_array*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-759">The formal parameter list consists of one or more comma-separated parameters of which only the last may be a *parameter_array*.</span></span>

<span data-ttu-id="5bc16-760">Oggetto *fixed_parameter* costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), facoltativo `ref`, `out` o `this` modificatore, un *tipo*, un *identifier* facoltativo *default_argument*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-760">A *fixed_parameter* consists of an optional set of *attributes* ([Attributes](attributes.md)), an optional `ref`, `out` or `this` modifier, a *type*, an *identifier* and an optional *default_argument*.</span></span> <span data-ttu-id="5bc16-761">Ciascuna *fixed_parameter* dichiara un parametro del tipo specificato con il nome specificato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-761">Each *fixed_parameter* declares a parameter of the given type with the given name.</span></span> <span data-ttu-id="5bc16-762">Il `this` modificatore definisce il metodo come metodo di estensione ed è consentito solo nel primo parametro di un metodo statico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-762">The `this` modifier designates the method as an extension method and is only allowed on the first parameter of a static method.</span></span> <span data-ttu-id="5bc16-763">I metodi di estensione sono descritti nella [metodi di estensione](classes.md#extension-methods).</span><span class="sxs-lookup"><span data-stu-id="5bc16-763">Extension methods are further described in [Extension methods](classes.md#extension-methods).</span></span>

<span data-ttu-id="5bc16-764">Oggetto *fixed_parameter* con un *default_argument* è noto come un' ***parametro facoltativo***, mentre un *fixed_parameter* senza un *default_argument* è un ***parametro obbligatorio***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-764">A *fixed_parameter* with a *default_argument* is known as an ***optional parameter***, whereas a *fixed_parameter* without a *default_argument* is a ***required parameter***.</span></span> <span data-ttu-id="5bc16-765">Un parametro obbligatorio non può essere visualizzato dopo un parametro facoltativo in una *formal_parameter_list*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-765">A required parameter may not appear after an optional parameter in a *formal_parameter_list*.</span></span>

<span data-ttu-id="5bc16-766">Oggetto `ref` oppure `out` parametro non può avere un *default_argument*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-766">A `ref` or `out` parameter cannot have a *default_argument*.</span></span> <span data-ttu-id="5bc16-767">Il *espressione* in un *default_argument* deve essere uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-767">The *expression* in a *default_argument* must be one of the following:</span></span>

*  <span data-ttu-id="5bc16-768">a *constant_expression*</span><span class="sxs-lookup"><span data-stu-id="5bc16-768">a *constant_expression*</span></span>
*  <span data-ttu-id="5bc16-769">un'espressione nel formato `new S()` in cui `S` è un tipo valore</span><span class="sxs-lookup"><span data-stu-id="5bc16-769">an expression of the form `new S()` where `S` is a value type</span></span>
*  <span data-ttu-id="5bc16-770">un'espressione nel formato `default(S)` in cui `S` è un tipo valore</span><span class="sxs-lookup"><span data-stu-id="5bc16-770">an expression of the form `default(S)` where `S` is a value type</span></span>

<span data-ttu-id="5bc16-771">Il *espressione* deve essere convertibile in modo implicito da un'identità o una conversione che ammette valori null per il tipo del parametro.</span><span class="sxs-lookup"><span data-stu-id="5bc16-771">The *expression* must be implicitly convertible by an identity or nullable conversion to the type of the parameter.</span></span>

<span data-ttu-id="5bc16-772">Se si utilizzano parametri facoltativi in una dichiarazione di metodo parziale implementazione ([metodi parziali](classes.md#partial-methods)), un'implementazione di membro di interfaccia esplicita ([implementazioni esplicite dell'interfaccia membro](interfaces.md#explicit-interface-member-implementations)) o in un dichiarazione di indicizzatore parametro singolo ([indicizzatori](classes.md#indexers)) il compilatore deve generare un avviso, poiché questi membri non possono mai essere richiamati in modo che gli argomenti di essere omessa.</span><span class="sxs-lookup"><span data-stu-id="5bc16-772">If optional parameters occur in an implementing partial method declaration ([Partial methods](classes.md#partial-methods)) , an explicit interface member implementation ([Explicit interface member implementations](interfaces.md#explicit-interface-member-implementations)) or in a single-parameter indexer declaration ([Indexers](classes.md#indexers)) the compiler should give a warning, since these members can never be invoked in a way that permits arguments to be omitted.</span></span>

<span data-ttu-id="5bc16-773">Oggetto *parameter_array* costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), un `params` modificatore, un' *array_type*, e un *identificatore*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-773">A *parameter_array* consists of an optional set of *attributes* ([Attributes](attributes.md)), a `params` modifier, an *array_type*, and an *identifier*.</span></span> <span data-ttu-id="5bc16-774">Una matrice di parametri dichiara un singolo parametro del tipo di matrice specificato con il nome specificato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-774">A parameter array declares a single parameter of the given array type with the given name.</span></span> <span data-ttu-id="5bc16-775">Il *array_type* di un parametro di matrice deve essere un tipo di matrici unidimensionali ([tipi matrice](arrays.md#array-types)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-775">The *array_type* of a parameter array must be a single-dimensional array type ([Array types](arrays.md#array-types)).</span></span> <span data-ttu-id="5bc16-776">In una chiamata al metodo, una matrice di parametri consente a un singolo argomento per specificare il tipo matrice specificata o consente zero o più argomenti del tipo di elemento di matrice per specificare.</span><span class="sxs-lookup"><span data-stu-id="5bc16-776">In a method invocation, a parameter array permits either a single argument of the given array type to be specified, or it permits zero or more arguments of the array element type to be specified.</span></span> <span data-ttu-id="5bc16-777">Matrici di parametri sono consultare [matrici di parametri](classes.md#parameter-arrays).</span><span class="sxs-lookup"><span data-stu-id="5bc16-777">Parameter arrays are described further in [Parameter arrays](classes.md#parameter-arrays).</span></span>

<span data-ttu-id="5bc16-778">Oggetto *parameter_array* possono verificarsi dopo un parametro facoltativo, ma non può avere un valore predefinito, ovvero l'omissione di argomenti per un *parameter_array* invece comporterà la creazione di una matrice vuota.</span><span class="sxs-lookup"><span data-stu-id="5bc16-778">A *parameter_array* may occur after an optional parameter, but cannot have a default value -- the omission of arguments for a *parameter_array* would instead result in the creation of an empty array.</span></span>

<span data-ttu-id="5bc16-779">L'esempio seguente illustra i diversi tipi di parametri:</span><span class="sxs-lookup"><span data-stu-id="5bc16-779">The following example illustrates different kinds of parameters:</span></span>
```csharp
public void M(
    ref int      i,
    decimal      d,
    bool         b = false,
    bool?        n = false,
    string       s = "Hello",
    object       o = null,
    T            t = default(T),
    params int[] a
) { }
```

<span data-ttu-id="5bc16-780">Nel *formal_parameter_list* per `M`, `i` è un parametro di riferimento obbligatorio `d` è un parametro obbligatorio, `b`, `s`, `o` e `t` valore facoltativo di parametri e `a` è una matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="5bc16-780">In the *formal_parameter_list* for `M`, `i` is a required ref parameter, `d` is a required value parameter, `b`, `s`, `o` and `t` are optional value parameters and `a` is a parameter array.</span></span>

<span data-ttu-id="5bc16-781">Una dichiarazione di metodo consente di creare uno spazio di dichiarazione separata per i parametri, parametri di tipo e le variabili locali.</span><span class="sxs-lookup"><span data-stu-id="5bc16-781">A method declaration creates a separate declaration space for parameters, type parameters and local variables.</span></span> <span data-ttu-id="5bc16-782">I nomi vengono introdotti in questo spazio di dichiarazione per l'elenco di parametri di tipo e l'elenco di parametri formali del metodo e le dichiarazioni di variabili locali nel *blocco* del metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-782">Names are introduced into this declaration space by the type parameter list and the formal parameter list of the method and by local variable declarations in the *block* of the method.</span></span> <span data-ttu-id="5bc16-783">È un errore per due membri di uno spazio di dichiarazione di metodo che lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="5bc16-783">It is an error for two members of a method declaration space to have the same name.</span></span> <span data-ttu-id="5bc16-784">È un errore per lo spazio di dichiarazione di metodo e lo spazio di dichiarazione di variabile locale di uno spazio di dichiarazione annidati per contenere gli elementi con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="5bc16-784">It is an error for the method declaration space and the local variable declaration space of a nested declaration space to contain elements with the same name.</span></span>

<span data-ttu-id="5bc16-785">Una chiamata al metodo ([chiamate al metodo](expressions.md#method-invocations)) Crea una copia, specifica della chiamata, dei parametri formali e le variabili locali del metodo, l'elenco di argomenti della chiamata assegna i valori o riferimenti a variabili per il appena creati parametri formali.</span><span class="sxs-lookup"><span data-stu-id="5bc16-785">A method invocation ([Method invocations](expressions.md#method-invocations)) creates a copy, specific to that invocation, of the formal parameters and local variables of the method, and the argument list of the invocation assigns values or variable references to the newly created formal parameters.</span></span> <span data-ttu-id="5bc16-786">All'interno di *blocco* di un metodo, i parametri formali possono fare riferimento i relativi identificatori nella *simple_name* espressioni ([nomi semplici](expressions.md#simple-names)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-786">Within the *block* of a method, formal parameters can be referenced by their identifiers in *simple_name* expressions ([Simple names](expressions.md#simple-names)).</span></span>

<span data-ttu-id="5bc16-787">Esistono quattro tipi di parametri formali:</span><span class="sxs-lookup"><span data-stu-id="5bc16-787">There are four kinds of formal parameters:</span></span>

*  <span data-ttu-id="5bc16-788">Parametri di valore, che vengono dichiarati senza tutti i modificatori.</span><span class="sxs-lookup"><span data-stu-id="5bc16-788">Value parameters, which are declared without any modifiers.</span></span>
*  <span data-ttu-id="5bc16-789">Fare riferimento ai parametri, che vengono dichiarati con la `ref` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-789">Reference parameters, which are declared with the `ref` modifier.</span></span>
*  <span data-ttu-id="5bc16-790">I parametri di output, che vengono dichiarati con la `out` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-790">Output parameters, which are declared with the `out` modifier.</span></span>
*  <span data-ttu-id="5bc16-791">Matrici di parametri, che vengono dichiarate con la `params` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-791">Parameter arrays, which are declared with the `params` modifier.</span></span>

<span data-ttu-id="5bc16-792">Come descritto in [firme e overload](basic-concepts.md#signatures-and-overloading), il `ref` e `out` modificatori fanno parte della firma del metodo, ma il `params` modificatore non.</span><span class="sxs-lookup"><span data-stu-id="5bc16-792">As described in [Signatures and overloading](basic-concepts.md#signatures-and-overloading), the `ref` and `out` modifiers are part of a method's signature, but the `params` modifier is not.</span></span>

#### <a name="value-parameters"></a><span data-ttu-id="5bc16-793">Parametri di valore</span><span class="sxs-lookup"><span data-stu-id="5bc16-793">Value parameters</span></span>

<span data-ttu-id="5bc16-794">Un parametro dichiarato con nessun modificatore è un parametro di valore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-794">A parameter declared with no modifiers is a value parameter.</span></span> <span data-ttu-id="5bc16-795">Un parametro di valore corrisponde a una variabile locale che ottiene il valore iniziale dall'argomento corrispondente fornito nella chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-795">A value parameter corresponds to a local variable that gets its initial value from the corresponding argument supplied in the method invocation.</span></span>

<span data-ttu-id="5bc16-796">Quando un parametro formale è un parametro di valore, l'argomento corrispondente in una chiamata al metodo deve essere un'espressione che è convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) al tipo di parametro formale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-796">When a formal parameter is a value parameter, the corresponding argument in a method invocation must be an expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the formal parameter type.</span></span>

<span data-ttu-id="5bc16-797">Un metodo è consentito assegnare nuovi valori per un parametro di valore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-797">A method is permitted to assign new values to a value parameter.</span></span> <span data-ttu-id="5bc16-798">Tali assegnazioni riguardano solo il percorso di archiviazione locale rappresentato dal parametro value — non hanno alcun effetto sull'argomento effettivo specificato nella chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-798">Such assignments only affect the local storage location represented by the value parameter—they have no effect on the actual argument given in the method invocation.</span></span>

#### <a name="reference-parameters"></a><span data-ttu-id="5bc16-799">Parametri per riferimento</span><span class="sxs-lookup"><span data-stu-id="5bc16-799">Reference parameters</span></span>

<span data-ttu-id="5bc16-800">Un parametro dichiarato con un `ref` modificatore è un parametro di riferimento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-800">A parameter declared with a `ref` modifier is a reference parameter.</span></span> <span data-ttu-id="5bc16-801">A differenza di un parametro di valore, un parametro di riferimento non crea un nuovo percorso di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-801">Unlike a value parameter, a reference parameter does not create a new storage location.</span></span> <span data-ttu-id="5bc16-802">Al contrario, un parametro di riferimento rappresenta la stessa posizione di archiviazione della variabile specificata come argomento nella chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-802">Instead, a reference parameter represents the same storage location as the variable given as the argument in the method invocation.</span></span>

<span data-ttu-id="5bc16-803">Quando un parametro formale è un parametro per riferimento, l'argomento corrispondente in una chiamata al metodo deve essere costituito la parola chiave `ref` seguita da un *variable_reference* ([regole Precise per la determinazione assegnazione certa](variables.md#precise-rules-for-determining-definite-assignment)) dello stesso tipo del parametro formale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-803">When a formal parameter is a reference parameter, the corresponding argument in a method invocation must consist of the keyword `ref` followed by a *variable_reference* ([Precise rules for determining definite assignment](variables.md#precise-rules-for-determining-definite-assignment)) of the same type as the formal parameter.</span></span> <span data-ttu-id="5bc16-804">Una variabile deve essere assegnata prima che può essere passata come parametro di riferimento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-804">A variable must be definitely assigned before it can be passed as a reference parameter.</span></span>

<span data-ttu-id="5bc16-805">All'interno di un metodo, un parametro di riferimento viene sempre considerato assegnato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-805">Within a method, a reference parameter is always considered definitely assigned.</span></span>

<span data-ttu-id="5bc16-806">Un metodo dichiarato come iteratore ([iteratori](classes.md#iterators)) non possono avere parametri di riferimento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-806">A method declared as an iterator ([Iterators](classes.md#iterators)) cannot have reference parameters.</span></span>

<span data-ttu-id="5bc16-807">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-807">The example</span></span>
```csharp
using System;

class Test
{
    static void Swap(ref int x, ref int y) {
        int temp = x;
        x = y;
        y = temp;
    }

    static void Main() {
        int i = 1, j = 2;
        Swap(ref i, ref j);
        Console.WriteLine("i = {0}, j = {1}", i, j);
    }
}
```
<span data-ttu-id="5bc16-808">produce l'output</span><span class="sxs-lookup"><span data-stu-id="5bc16-808">produces the output</span></span>
```
i = 2, j = 1
```

<span data-ttu-id="5bc16-809">Per la chiamata `Swap` nelle `Main`, `x` rappresenta `i` e `y` rappresenta `j`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-809">For the invocation of `Swap` in `Main`, `x` represents `i` and `y` represents `j`.</span></span> <span data-ttu-id="5bc16-810">Di conseguenza, la chiamata ha l'effetto di lo scambio dei valori della `i` e `j`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-810">Thus, the invocation has the effect of swapping the values of `i` and `j`.</span></span>

<span data-ttu-id="5bc16-811">In un metodo che accetta i parametri di riferimento è possibile che più nomi rappresentare la posizione di archiviazione stesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-811">In a method that takes reference parameters it is possible for multiple names to represent the same storage location.</span></span> <span data-ttu-id="5bc16-812">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-812">In the example</span></span>
```csharp
class A
{
    string s;

    void F(ref string a, ref string b) {
        s = "One";
        a = "Two";
        b = "Three";
    }

    void G() {
        F(ref s, ref s);
    }
}
```
<span data-ttu-id="5bc16-813">la chiamata di `F` nelle `G` passa un riferimento al `s` per entrambi `a` e `b`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-813">the invocation of `F` in `G` passes a reference to `s` for both `a` and `b`.</span></span> <span data-ttu-id="5bc16-814">Pertanto, per chiamata, i nomi `s`, `a`, e `b` fanno tutte riferimento alla stessa posizione di archiviazione e le assegnazioni di tre tutti modificano il campo di istanza `s`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-814">Thus, for that invocation, the names `s`, `a`, and `b` all refer to the same storage location, and the three assignments all modify the instance field `s`.</span></span>

#### <a name="output-parameters"></a><span data-ttu-id="5bc16-815">Parametri di output</span><span class="sxs-lookup"><span data-stu-id="5bc16-815">Output parameters</span></span>

<span data-ttu-id="5bc16-816">Un parametro dichiarato con un `out` modificatore è un parametro di output.</span><span class="sxs-lookup"><span data-stu-id="5bc16-816">A parameter declared with an `out` modifier is an output parameter.</span></span> <span data-ttu-id="5bc16-817">Simile a un parametro di riferimento, un parametro di output non crea un nuovo percorso di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-817">Similar to a reference parameter, an output parameter does not create a new storage location.</span></span> <span data-ttu-id="5bc16-818">Al contrario, un parametro di output rappresenta la stessa posizione di archiviazione della variabile specificata come argomento nella chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-818">Instead, an output parameter represents the same storage location as the variable given as the argument in the method invocation.</span></span>

<span data-ttu-id="5bc16-819">Quando un parametro formale è un parametro di output, l'argomento corrispondente in una chiamata al metodo deve essere costituito la parola chiave `out` seguita da un *variable_reference* ([regole Precise per la determinazione assegnazione certa](variables.md#precise-rules-for-determining-definite-assignment)) dello stesso tipo del parametro formale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-819">When a formal parameter is an output parameter, the corresponding argument in a method invocation must consist of the keyword `out` followed by a *variable_reference* ([Precise rules for determining definite assignment](variables.md#precise-rules-for-determining-definite-assignment)) of the same type as the formal parameter.</span></span> <span data-ttu-id="5bc16-820">Una variabile necessario non essere assegnata può essere passata come parametro di output, ma in seguito una chiamata in cui una variabile è stata passata come parametro di output, la variabile viene considerata assegnata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-820">A variable need not be definitely assigned before it can be passed as an output parameter, but following an invocation where a variable was passed as an output parameter, the variable is considered definitely assigned.</span></span>

<span data-ttu-id="5bc16-821">All'interno di un metodo, esattamente come una variabile locale variabile, un parametro di output è inizialmente considerato non assegnati e deve essere assegnata prima che venga utilizzato il relativo valore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-821">Within a method, just like a local variable, an output parameter is initially considered unassigned and must be definitely assigned before its value is used.</span></span>

<span data-ttu-id="5bc16-822">Ogni parametro di output di un metodo deve essere assegnata prima che il metodo restituisce.</span><span class="sxs-lookup"><span data-stu-id="5bc16-822">Every output parameter of a method must be definitely assigned before the method returns.</span></span>

<span data-ttu-id="5bc16-823">Un metodo dichiarato come un metodo parziale ([metodi parziali](classes.md#partial-methods)) o un iteratore ([iteratori](classes.md#iterators)) non può avere parametri di output.</span><span class="sxs-lookup"><span data-stu-id="5bc16-823">A method declared as a partial method ([Partial methods](classes.md#partial-methods)) or an iterator ([Iterators](classes.md#iterators)) cannot have output parameters.</span></span>

<span data-ttu-id="5bc16-824">I parametri di output vengono in genere usati nei metodi che restituiscono più valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-824">Output parameters are typically used in methods that produce multiple return values.</span></span> <span data-ttu-id="5bc16-825">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5bc16-825">For example:</span></span>
```csharp
using System;

class Test
{
    static void SplitPath(string path, out string dir, out string name) {
        int i = path.Length;
        while (i > 0) {
            char ch = path[i - 1];
            if (ch == '\\' || ch == '/' || ch == ':') break;
            i--;
        }
        dir = path.Substring(0, i);
        name = path.Substring(i);
    }

    static void Main() {
        string dir, name;
        SplitPath("c:\\Windows\\System\\hello.txt", out dir, out name);
        Console.WriteLine(dir);
        Console.WriteLine(name);
    }
}
```

<span data-ttu-id="5bc16-826">L'esempio produce l'output:</span><span class="sxs-lookup"><span data-stu-id="5bc16-826">The example produces the output:</span></span>
```
c:\Windows\System\
hello.txt
```

<span data-ttu-id="5bc16-827">Si noti che il `dir` e `name` le variabili possono essere non assegnate prima che vengano passati a `SplitPath`, e che sono considerati assegnate dopo la chiamata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-827">Note that the `dir` and `name` variables can be unassigned before they are passed to `SplitPath`, and that they are considered definitely assigned following the call.</span></span>

#### <a name="parameter-arrays"></a><span data-ttu-id="5bc16-828">Matrici di parametri</span><span class="sxs-lookup"><span data-stu-id="5bc16-828">Parameter arrays</span></span>

<span data-ttu-id="5bc16-829">Un parametro dichiarato con un `params` modificatore è una matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="5bc16-829">A parameter declared with a `params` modifier is a parameter array.</span></span> <span data-ttu-id="5bc16-830">Se un elenco di parametri formali include una matrice di parametri, deve essere l'ultimo parametro nell'elenco e deve essere di un tipo di matrice unidimensionale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-830">If a formal parameter list includes a parameter array, it must be the last parameter in the list and it must be of a single-dimensional array type.</span></span> <span data-ttu-id="5bc16-831">Ad esempio, i tipi `string[]` e `string[][]` può essere usato come tipo di una matrice di parametri, ma il tipo `string[,]` non può.</span><span class="sxs-lookup"><span data-stu-id="5bc16-831">For example, the types `string[]` and `string[][]` can be used as the type of a parameter array, but the type `string[,]` can not.</span></span> <span data-ttu-id="5bc16-832">Non è possibile combinare le `params` modificatore con i modificatori `ref` e `out`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-832">It is not possible to combine the `params` modifier with the modifiers `ref` and `out`.</span></span>

<span data-ttu-id="5bc16-833">Una matrice di parametri gli argomenti di essere specificato in uno dei due modi in una chiamata al metodo:</span><span class="sxs-lookup"><span data-stu-id="5bc16-833">A parameter array permits arguments to be specified in one of two ways in a method invocation:</span></span>

*  <span data-ttu-id="5bc16-834">L'argomento specificato per una matrice di parametri può essere una singola espressione che è convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) per il tipo di matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="5bc16-834">The argument given for a parameter array can be a single expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the parameter array type.</span></span> <span data-ttu-id="5bc16-835">In questo caso, la matrice di parametri funziona esattamente come un parametro di valore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-835">In this case, the parameter array acts precisely like a value parameter.</span></span>
*  <span data-ttu-id="5bc16-836">In alternativa, la chiamata è possibile specificare zero o più argomenti per la matrice di parametri, dove ogni argomento è un'espressione che è convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) al tipo di elemento della matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="5bc16-836">Alternatively, the invocation can specify zero or more arguments for the parameter array, where each argument is an expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the element type of the parameter array.</span></span> <span data-ttu-id="5bc16-837">In questo caso, la chiamata crea un'istanza del tipo di parametro matrice con una lunghezza corrispondente al numero di argomenti, inizializza gli elementi dell'istanza di matrice con i valori dell'argomento specificato e Usa l'istanza di matrice appena creata come l'effettivo argomento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-837">In this case, the invocation creates an instance of the parameter array type with a length corresponding to the number of arguments, initializes the elements of the array instance with the given argument values, and uses the newly created array instance as the actual argument.</span></span>

<span data-ttu-id="5bc16-838">Fatta eccezione per consentire un numero variabile di argomenti in una chiamata, una matrice di parametri equivale esattamente a un parametro di valore ([parametri del valore](classes.md#value-parameters)) dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-838">Except for allowing a variable number of arguments in an invocation, a parameter array is precisely equivalent to a value parameter ([Value parameters](classes.md#value-parameters)) of the same type.</span></span>

<span data-ttu-id="5bc16-839">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-839">The example</span></span>
```csharp
using System;

class Test
{
    static void F(params int[] args) {
        Console.Write("Array contains {0} elements:", args.Length);
        foreach (int i in args) 
            Console.Write(" {0}", i);
        Console.WriteLine();
    }

    static void Main() {
        int[] arr = {1, 2, 3};
        F(arr);
        F(10, 20, 30, 40);
        F();
    }
}
```
<span data-ttu-id="5bc16-840">produce l'output</span><span class="sxs-lookup"><span data-stu-id="5bc16-840">produces the output</span></span>
```
Array contains 3 elements: 1 2 3
Array contains 4 elements: 10 20 30 40
Array contains 0 elements:
```

<span data-ttu-id="5bc16-841">La prima chiamata della `F` passa semplicemente la matrice `a` come parametro di valore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-841">The first invocation of `F` simply passes the array `a` as a value parameter.</span></span> <span data-ttu-id="5bc16-842">La seconda chiamata di `F` crea automaticamente un elemento di quattro `int[]` con i valori di elemento specificato e passa tale istanza come parametro di valore della matrice.</span><span class="sxs-lookup"><span data-stu-id="5bc16-842">The second invocation of `F` automatically creates a four-element `int[]` with the given element values and passes that array instance as a value parameter.</span></span> <span data-ttu-id="5bc16-843">Analogamente, la chiamata del terzo `F` crea un elemento di zero `int[]` e passa quell'istanza come un parametro di valore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-843">Likewise, the third invocation of `F` creates a zero-element `int[]` and passes that instance as a value parameter.</span></span> <span data-ttu-id="5bc16-844">Le chiamate al secondo e terzi sono esattamente equivalenti a:</span><span class="sxs-lookup"><span data-stu-id="5bc16-844">The second and third invocations are precisely equivalent to writing:</span></span>
```csharp
F(new int[] {10, 20, 30, 40});
F(new int[] {});
```

<span data-ttu-id="5bc16-845">Quando si esegue la risoluzione dell'overload, un metodo con una matrice di parametri potrebbe essere applicabile in forma normale o in forma espansa ([membro di funzione applicabile](expressions.md#applicable-function-member)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-845">When performing overload resolution, a method with a parameter array may be applicable either in its normal form or in its expanded form ([Applicable function member](expressions.md#applicable-function-member)).</span></span> <span data-ttu-id="5bc16-846">La forma espansa di un metodo è disponibile solo se la forma standard per il metodo non è applicabile e solo se un metodo con la stessa firma in forma espansa applicabile non è già dichiarato nello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-846">The expanded form of a method is available only if the normal form of the method is not applicable and only if an applicable method with the same signature as the expanded form is not already declared in the same type.</span></span>

<span data-ttu-id="5bc16-847">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-847">The example</span></span>
```csharp
using System;

class Test
{
    static void F(params object[] a) {
        Console.WriteLine("F(object[])");
    }

    static void F() {
        Console.WriteLine("F()");
    }

    static void F(object a0, object a1) {
        Console.WriteLine("F(object,object)");
    }

    static void Main() {
        F();
        F(1);
        F(1, 2);
        F(1, 2, 3);
        F(1, 2, 3, 4);
    }
}
```
<span data-ttu-id="5bc16-848">produce l'output</span><span class="sxs-lookup"><span data-stu-id="5bc16-848">produces the output</span></span>
```
F();
F(object[]);
F(object,object);
F(object[]);
F(object[]);
```

<span data-ttu-id="5bc16-849">Nell'esempio, due dei formati possibili espansi del metodo con una matrice di parametri sono già incluse nella classe come i metodi regolari.</span><span class="sxs-lookup"><span data-stu-id="5bc16-849">In the example, two of the possible expanded forms of the method with a parameter array are already included in the class as regular methods.</span></span> <span data-ttu-id="5bc16-850">Queste forme espanse pertanto non vengono considerati quando si esegue la risoluzione dell'overload e le chiamate al primo e il terzo metodo selezionare quindi i metodi regolari.</span><span class="sxs-lookup"><span data-stu-id="5bc16-850">These expanded forms are therefore not considered when performing overload resolution, and the first and third method invocations thus select the regular methods.</span></span> <span data-ttu-id="5bc16-851">Quando una classe dichiara un metodo con una matrice di parametri, non è insolito per includere anche alcune forme espanse come i metodi regolari.</span><span class="sxs-lookup"><span data-stu-id="5bc16-851">When a class declares a method with a parameter array, it is not uncommon to also include some of the expanded forms as regular methods.</span></span> <span data-ttu-id="5bc16-852">In questo modo è possibile evitare l'allocazione di una matrice viene richiamato l'istanza che si verifica quando una forma espansa di un metodo con una matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="5bc16-852">By doing so it is possible to avoid the allocation of an array instance that occurs when an expanded form of a method with a parameter array is invoked.</span></span>

<span data-ttu-id="5bc16-853">Quando il tipo di una matrice di parametri corrisponde `object[]`, si verifica una potenziale ambiguità tra la forma del metodo normale e la forma espansa per un singolo `object` parametro.</span><span class="sxs-lookup"><span data-stu-id="5bc16-853">When the type of a parameter array is `object[]`, a potential ambiguity arises between the normal form of the method and the expended form for a single `object` parameter.</span></span> <span data-ttu-id="5bc16-854">Il motivo per l'ambiguità è che un' `object[]` è a sua volta convertibile in modo implicito nel tipo `object`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-854">The reason for the ambiguity is that an `object[]` is itself implicitly convertible to type `object`.</span></span> <span data-ttu-id="5bc16-855">L'ambiguità non determina alcun problema, tuttavia, poiché può essere risolto inserendo un cast, se necessario.</span><span class="sxs-lookup"><span data-stu-id="5bc16-855">The ambiguity presents no problem, however, since it can be resolved by inserting a cast if needed.</span></span>

<span data-ttu-id="5bc16-856">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-856">The example</span></span>
```csharp
using System;

class Test
{
    static void F(params object[] args) {
        foreach (object o in args) {
            Console.Write(o.GetType().FullName);
            Console.Write(" ");
        }
        Console.WriteLine();
    }

    static void Main() {
        object[] a = {1, "Hello", 123.456};
        object o = a;
        F(a);
        F((object)a);
        F(o);
        F((object[])o);
    }
}
```
<span data-ttu-id="5bc16-857">produce l'output</span><span class="sxs-lookup"><span data-stu-id="5bc16-857">produces the output</span></span>
```
System.Int32 System.String System.Double
System.Object[]
System.Object[]
System.Int32 System.String System.Double
```

<span data-ttu-id="5bc16-858">In chiamate e il cognome dei `F`, la forma standard `F` è applicabile poiché esiste una conversione implicita dal tipo di argomento al tipo di parametro (entrambe sono di tipo `object[]`).</span><span class="sxs-lookup"><span data-stu-id="5bc16-858">In the first and last invocations of `F`, the normal form of `F` is applicable because an implicit conversion exists from the argument type to the parameter type (both are of type `object[]`).</span></span> <span data-ttu-id="5bc16-859">Di conseguenza, la risoluzione dell'overload seleziona la forma standard `F`, e l'argomento viene passato come parametro di valore regolare.</span><span class="sxs-lookup"><span data-stu-id="5bc16-859">Thus, overload resolution selects the normal form of `F`, and the argument is passed as a regular value parameter.</span></span> <span data-ttu-id="5bc16-860">Nelle chiamate al secondo e terzi, la forma standard `F` non applicabile perché non esiste alcuna conversione implicita dal tipo di argomento al tipo di parametro (tipo `object` non può essere convertito in modo implicito nel tipo `object[]`).</span><span class="sxs-lookup"><span data-stu-id="5bc16-860">In the second and third invocations, the normal form of `F` is not applicable because no implicit conversion exists from the argument type to the parameter type (type `object` cannot be implicitly converted to type `object[]`).</span></span> <span data-ttu-id="5bc16-861">Tuttavia, la forma espansa di `F` è applicabile, quindi è selezionata per la risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="5bc16-861">However, the expanded form of `F` is applicable, so it is selected by overload resolution.</span></span> <span data-ttu-id="5bc16-862">Di conseguenza, un solo elemento `object[]` viene creato per la chiamata, il singolo elemento della matrice viene inizializzato con il valore dell'argomento specificato (che a sua volta è un riferimento a un `object[]`).</span><span class="sxs-lookup"><span data-stu-id="5bc16-862">As a result, a one-element `object[]` is created by the invocation, and the single element of the array is initialized with the given argument value (which itself is a reference to an `object[]`).</span></span>

### <a name="static-and-instance-methods"></a><span data-ttu-id="5bc16-863">Metodi statici e di istanza</span><span class="sxs-lookup"><span data-stu-id="5bc16-863">Static and instance methods</span></span>

<span data-ttu-id="5bc16-864">Quando una dichiarazione di metodo include un `static` modificatore, questo metodo viene definito come un metodo statico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-864">When a method declaration includes a `static` modifier, that method is said to be a static method.</span></span> <span data-ttu-id="5bc16-865">Se non si specifica `static` modificatore è presente, il metodo viene definito un metodo di istanza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-865">When no `static` modifier is present, the method is said to be an instance method.</span></span>

<span data-ttu-id="5bc16-866">Un metodo statico non funziona in un'istanza specifica ed è un errore in fase di compilazione per fare riferimento a `this` in un metodo statico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-866">A static method does not operate on a specific instance, and it is a compile-time error to refer to `this` in a static method.</span></span>

<span data-ttu-id="5bc16-867">Un metodo di istanza agisce su una determinata istanza di una classe e tale istanza è accessibile come `this` ([questo accesso](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-867">An instance method operates on a given instance of a class, and that instance can be accessed as `this` ([This access](expressions.md#this-access)).</span></span>

<span data-ttu-id="5bc16-868">Quando si fa riferimento a un metodo un *member_access* ([l'accesso ai membri](expressions.md#member-access)) nel formato `E.M`se `M` è un metodo statico, `E` deve indicare un tipo contenente `M`e se `M` è un metodo di istanza `E` devono indicare un'istanza di un tipo contenente `M`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-868">When a method is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, if `M` is a static method, `E` must denote a type containing `M`, and if `M` is an instance method, `E` must denote an instance of a type containing `M`.</span></span>

<span data-ttu-id="5bc16-869">Le differenze tra statica e i membri di istanza sono descritti dettagliatamente nella [i membri statici e di istanza](classes.md#static-and-instance-members).</span><span class="sxs-lookup"><span data-stu-id="5bc16-869">The differences between static and instance members are discussed further in [Static and instance members](classes.md#static-and-instance-members).</span></span>

### <a name="virtual-methods"></a><span data-ttu-id="5bc16-870">Metodi virtuali</span><span class="sxs-lookup"><span data-stu-id="5bc16-870">Virtual methods</span></span>

<span data-ttu-id="5bc16-871">Quando una dichiarazione di metodo di istanza include un `virtual` modificatore, questo metodo viene definito un metodo virtuale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-871">When an instance method declaration includes a `virtual` modifier, that method is said to be a virtual method.</span></span> <span data-ttu-id="5bc16-872">Se non si specifica `virtual` modificatore è presente, il metodo viene definito un metodo non virtuale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-872">When no `virtual` modifier is present, the method is said to be a non-virtual method.</span></span>

<span data-ttu-id="5bc16-873">L'implementazione di un metodo non virtuale è invariante: L'implementazione è la stessa se il metodo viene richiamato in un'istanza della classe in cui viene dichiarato o un'istanza di una classe derivata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-873">The implementation of a non-virtual method is invariant: The implementation is the same whether the method is invoked on an instance of the class in which it is declared or an instance of a derived class.</span></span> <span data-ttu-id="5bc16-874">Al contrario, l'implementazione di un metodo virtuale può essere sostituito da classi derivate.</span><span class="sxs-lookup"><span data-stu-id="5bc16-874">In contrast, the implementation of a virtual method can be superseded by derived classes.</span></span> <span data-ttu-id="5bc16-875">Il processo di sostituzione dell'implementazione di un metodo virtuale ereditato è noto come ***eseguendo l'override*** tale metodo ([metodi di Override](classes.md#override-methods)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-875">The process of superseding the implementation of an inherited virtual method is known as ***overriding*** that method ([Override methods](classes.md#override-methods)).</span></span>

<span data-ttu-id="5bc16-876">In una chiamata al metodo virtuale, il ***tipo di runtime*** dell'istanza per il quale si verifica la chiamata sul posto determina l'implementazione effettiva del metodo da richiamare.</span><span class="sxs-lookup"><span data-stu-id="5bc16-876">In a virtual method invocation, the ***run-time type*** of the instance for which that invocation takes place determines the actual method implementation to invoke.</span></span> <span data-ttu-id="5bc16-877">In una chiamata al metodo non virtuale, il ***in fase di compilazione*** dell'istanza è il fattore determinante.</span><span class="sxs-lookup"><span data-stu-id="5bc16-877">In a non-virtual method invocation, the ***compile-time type*** of the instance is the determining factor.</span></span> <span data-ttu-id="5bc16-878">Più precisamente, quando un metodo denominato `N` viene richiamato con un elenco di argomenti `A` in un'istanza con un tipo in fase di compilazione `C` e un tipo in fase di esecuzione `R` (dove `R` sia `C` o una classe derivata da `C`), la chiamata viene elaborata come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="5bc16-878">In precise terms, when a method named `N` is invoked with an argument list `A` on an instance with a compile-time type `C` and a run-time type `R` (where `R` is either `C` or a class derived from `C`), the invocation is processed as follows:</span></span>

*  <span data-ttu-id="5bc16-879">In primo luogo, la risoluzione dell'overload viene applicata a `C`, `N`, e `A`per selezionare un metodo specifico `M` dal set di metodi dichiarati ed ereditato da `C`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-879">First, overload resolution is applied to `C`, `N`, and `A`, to select a specific method `M` from the set of methods declared in and inherited by `C`.</span></span> <span data-ttu-id="5bc16-880">Come descritto nella [chiamate al metodo](expressions.md#method-invocations).</span><span class="sxs-lookup"><span data-stu-id="5bc16-880">This is described in [Method invocations](expressions.md#method-invocations).</span></span>
*  <span data-ttu-id="5bc16-881">Quindi, se `M` è un metodo non virtuale, `M` viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-881">Then, if `M` is a non-virtual method, `M` is invoked.</span></span>
*  <span data-ttu-id="5bc16-882">In caso contrario, `M` è un metodo virtuale e l'implementazione più derivata di `M` rispetto a `R` viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-882">Otherwise, `M` is a virtual method, and the most derived implementation of `M` with respect to `R` is invoked.</span></span>

<span data-ttu-id="5bc16-883">Per ogni metodo virtuale dichiarato o ereditato da una classe, esiste un ***implementazione più derivata*** del metodo rispetto a quella classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-883">For every virtual method declared in or inherited by a class, there exists a ***most derived implementation*** of the method with respect to that class.</span></span> <span data-ttu-id="5bc16-884">L'implementazione di un metodo virtuale più derivato `M` rispetto a una classe `R` viene determinata come segue:</span><span class="sxs-lookup"><span data-stu-id="5bc16-884">The most derived implementation of a virtual method `M` with respect to a class `R` is determined as follows:</span></span>

*  <span data-ttu-id="5bc16-885">Se `R` contiene la presentazione `virtual` deklarace `M`, quindi questo è l'implementazione più derivata di `M`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-885">If `R` contains the introducing `virtual` declaration of `M`, then this is the most derived implementation of `M`.</span></span>
*  <span data-ttu-id="5bc16-886">In caso contrario, se `R` contiene un `override` dei `M`, quindi questo è l'implementazione più derivata di `M`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-886">Otherwise, if `R` contains an `override` of `M`, then this is the most derived implementation of `M`.</span></span>
*  <span data-ttu-id="5bc16-887">In caso contrario, più di implementazione della classe derivata `M` rispetto al `R` è quello utilizzato per l'implementazione più derivata del `M` rispetto alla classe base diretta di `R`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-887">Otherwise, the most derived implementation of `M` with respect to `R` is the same as the most derived implementation of `M` with respect to the direct base class of `R`.</span></span>

<span data-ttu-id="5bc16-888">Nell'esempio seguente vengono illustrate le differenze tra i metodi virtuali e non virtuale:</span><span class="sxs-lookup"><span data-stu-id="5bc16-888">The following example illustrates the differences between virtual and non-virtual methods:</span></span>
```csharp
using System;

class A
{
    public void F() { Console.WriteLine("A.F"); }

    public virtual void G() { Console.WriteLine("A.G"); }
}

class B: A
{
    new public void F() { Console.WriteLine("B.F"); }

    public override void G() { Console.WriteLine("B.G"); }
}

class Test
{
    static void Main() {
        B b = new B();
        A a = b;
        a.F();
        b.F();
        a.G();
        b.G();
    }
}
```

<span data-ttu-id="5bc16-889">Nell'esempio riportato `A` introduce un metodo non virtuale `F` e un metodo virtuale `G`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-889">In the example, `A` introduces a non-virtual method `F` and a virtual method `G`.</span></span> <span data-ttu-id="5bc16-890">La classe `B` introduce un nuovo metodo non virtuale `F`, quindi nascondendo ereditato `F`ed esegue l'override del metodo ereditato `G`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-890">The class `B` introduces a new non-virtual method `F`, thus hiding the inherited `F`, and also overrides the inherited method `G`.</span></span> <span data-ttu-id="5bc16-891">L'esempio produce l'output:</span><span class="sxs-lookup"><span data-stu-id="5bc16-891">The example produces the output:</span></span>
```
A.F
B.F
B.G
B.G
```

<span data-ttu-id="5bc16-892">Si noti che l'istruzione `a.G()` richiama `B.G`, non `A.G`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-892">Notice that the statement `a.G()` invokes `B.G`, not `A.G`.</span></span> <span data-ttu-id="5bc16-893">Infatti, il runtime di tipo dell'istanza (che è `B`), non il tipo di fase di compilazione dell'istanza (ovvero `A`), determina l'implementazione effettiva del metodo da richiamare.</span><span class="sxs-lookup"><span data-stu-id="5bc16-893">This is because the run-time type of the instance (which is `B`), not the compile-time type of the instance (which is `A`), determines the actual method implementation to invoke.</span></span>

<span data-ttu-id="5bc16-894">Poiché i metodi sono consentiti per nascondere i metodi ereditati, è possibile che una classe contiene diversi metodi virtuali con la stessa firma.</span><span class="sxs-lookup"><span data-stu-id="5bc16-894">Because methods are allowed to hide inherited methods, it is possible for a class to contain several virtual methods with the same signature.</span></span> <span data-ttu-id="5bc16-895">Ciò non costituire un problema di ambiguità, poiché tutto tranne il metodo più derivato vengono nascosti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-895">This does not present an ambiguity problem, since all but the most derived method are hidden.</span></span> <span data-ttu-id="5bc16-896">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-896">In the example</span></span>
```csharp
using System;

class A
{
    public virtual void F() { Console.WriteLine("A.F"); }
}

class B: A
{
    public override void F() { Console.WriteLine("B.F"); }
}

class C: B
{
    new public virtual void F() { Console.WriteLine("C.F"); }
}

class D: C
{
    public override void F() { Console.WriteLine("D.F"); }
}

class Test
{
    static void Main() {
        D d = new D();
        A a = d;
        B b = d;
        C c = d;
        a.F();
        b.F();
        c.F();
        d.F();
    }
}
```
<span data-ttu-id="5bc16-897">il `C` e `D` classi contengono due metodi virtuali con la stessa firma: Dovuti a quella `A` e quella introdotti da `C`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-897">the `C` and `D` classes contain two virtual methods with the same signature: The one introduced by `A` and the one introduced by `C`.</span></span> <span data-ttu-id="5bc16-898">Il metodo dovuto `C` nasconde il metodo ereditato da `A`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-898">The method introduced by `C` hides the method inherited from `A`.</span></span> <span data-ttu-id="5bc16-899">Di conseguenza, la dichiarazione di override nel `D` esegue l'override del metodo introdotto da `C`, e non è possibile che `D` per eseguire l'override del metodo introdotto da `A`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-899">Thus, the override declaration in `D` overrides the method introduced by `C`, and it is not possible for `D` to override the method introduced by `A`.</span></span> <span data-ttu-id="5bc16-900">L'esempio produce l'output:</span><span class="sxs-lookup"><span data-stu-id="5bc16-900">The example produces the output:</span></span>
```
B.F
B.F
D.F
D.F
```

<span data-ttu-id="5bc16-901">Si noti che è possibile richiamare il metodo virtuale nascosto tramite l'accesso a un'istanza di `D` attraverso un minore derivato tipo in cui il metodo non sia nascosto.</span><span class="sxs-lookup"><span data-stu-id="5bc16-901">Note that it is possible to invoke the hidden virtual method by accessing an instance of `D` through a less derived type in which the method is not hidden.</span></span>

### <a name="override-methods"></a><span data-ttu-id="5bc16-902">Eseguire l'override di metodi</span><span class="sxs-lookup"><span data-stu-id="5bc16-902">Override methods</span></span>

<span data-ttu-id="5bc16-903">Quando una dichiarazione di metodo di istanza include un' `override` modificatore, il metodo viene definito un ***eseguire l'override di metodo***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-903">When an instance method declaration includes an `override` modifier, the method is said to be an ***override method***.</span></span> <span data-ttu-id="5bc16-904">Un metodo di override esegue l'override di un metodo virtuale ereditato con la stessa firma.</span><span class="sxs-lookup"><span data-stu-id="5bc16-904">An override method overrides an inherited virtual method with the same signature.</span></span> <span data-ttu-id="5bc16-905">Mentre una dichiarazione di metodo virtuale introduce un nuovo metodo, una dichiarazione di metodo di override specializza un metodo virtuale ereditato esistente specificando una nuova implementazione del metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-905">Whereas a virtual method declaration introduces a new method, an override method declaration specializes an existing inherited virtual method by providing a new implementation of that method.</span></span>

<span data-ttu-id="5bc16-906">Il metodo sottoposto a override da un' `override` dichiarazione è noto come il ***sottoposto a override di metodo di base***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-906">The method overridden by an `override` declaration is known as the ***overridden base method***.</span></span> <span data-ttu-id="5bc16-907">Per un metodo di override `M` dichiarata in una classe `C`, il metodo di base sottoposto a override è determinato esaminando ogni tipo di classe di base di `C`, che inizia con il tipo di classe di base diretta di `C` e continuando con ogni successivo tipo di classe di base diretta, fino a quando non in un tipo di classe base specificata almeno uno è un metodo accessibile che trova ha la stessa firma `M` dopo la sostituzione degli argomenti di tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-907">For an override method `M` declared in a class `C`, the overridden base method is determined by examining each base class type of `C`, starting with the direct base class type of `C` and continuing with each successive direct base class type, until in a given base class type at least one accessible method is located which has the same signature as `M` after substitution of type arguments.</span></span> <span data-ttu-id="5bc16-908">Ai fini di individuazione del metodo di base sottoposto a override, un metodo viene considerato accessibile se è `public`, se si tratta `protected`, se si tratta `protected internal`, o se è `internal` e dichiarata nello stesso programma come `C`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-908">For the purposes of locating the overridden base method, a method is considered accessible if it is `public`, if it is `protected`, if it is `protected internal`, or if it is `internal` and declared in the same program as `C`.</span></span>

<span data-ttu-id="5bc16-909">A meno che non tutti gli elementi seguenti siano vere per una dichiarazione di override, si verifica un errore in fase di compilazione:</span><span class="sxs-lookup"><span data-stu-id="5bc16-909">A compile-time error occurs unless all of the following are true for an override declaration:</span></span>

*  <span data-ttu-id="5bc16-910">Un metodo di base sottoposto a override può essere archiviato come descritto in precedenza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-910">An overridden base method can be located as described above.</span></span>
*  <span data-ttu-id="5bc16-911">È esattamente uno di questi metodi base sottoposto a override.</span><span class="sxs-lookup"><span data-stu-id="5bc16-911">There is exactly one such overridden base method.</span></span> <span data-ttu-id="5bc16-912">Questa restrizione ha effetto solo se il tipo di classe di base è un tipo costruito in cui la sostituzione degli argomenti di tipo rende la firma dei due metodi lo stesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-912">This restriction has effect only if the base class type is a constructed type where the substitution of type arguments makes the signature of two methods the same.</span></span>
*  <span data-ttu-id="5bc16-913">Il metodo di base sottoposto a override è virtuale, astratto o eseguire l'override di metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-913">The overridden base method is a virtual, abstract, or override method.</span></span> <span data-ttu-id="5bc16-914">In altre parole, il metodo di base sottoposto a override non può essere statico o non virtuale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-914">In other words, the overridden base method cannot be static or non-virtual.</span></span>
*  <span data-ttu-id="5bc16-915">Il metodo di base sottoposto a override non è un metodo sealed.</span><span class="sxs-lookup"><span data-stu-id="5bc16-915">The overridden base method is not a sealed method.</span></span>
*  <span data-ttu-id="5bc16-916">Il metodo di override e il metodo di base sottoposto a override hanno lo stesso tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-916">The override method and the overridden base method have the same return type.</span></span>
*  <span data-ttu-id="5bc16-917">La dichiarazione di override e il metodo di base sottoposto a override hanno la stessa accessibilità dichiarate.</span><span class="sxs-lookup"><span data-stu-id="5bc16-917">The override declaration and the overridden base method have the same declared accessibility.</span></span> <span data-ttu-id="5bc16-918">In altre parole, una dichiarazione di override non è possibile modificare l'accessibilità del metodo virtuale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-918">In other words, an override declaration cannot change the accessibility of the virtual method.</span></span> <span data-ttu-id="5bc16-919">Tuttavia, se il metodo di base sottoposto a override protected internal e viene dichiarata in un assembly diverso rispetto a dichiarata l'assembly contenente il metodo di override quindi il metodo di override accessibilità deve essere protetto.</span><span class="sxs-lookup"><span data-stu-id="5bc16-919">However, if the overridden base method is protected internal and it is declared in a different assembly than the assembly containing the override method then the override method's declared accessibility must be protected.</span></span>
*  <span data-ttu-id="5bc16-920">La dichiarazione di override non specifica parametri di tipo-clausole di vincoli.</span><span class="sxs-lookup"><span data-stu-id="5bc16-920">The override declaration does not specify type-parameter-constraints-clauses.</span></span> <span data-ttu-id="5bc16-921">In alternativa i vincoli vengono ereditati dal metodo base sottoposto a override.</span><span class="sxs-lookup"><span data-stu-id="5bc16-921">Instead the constraints are inherited from the overridden base method.</span></span> <span data-ttu-id="5bc16-922">Si noti che i vincoli che sono parametri di tipo nel metodo sottoposto a override possono essere sostituiti dagli argomenti di tipo nel vincolo ereditato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-922">Note that constraints that are type parameters in the overridden method may be replaced by type arguments in the inherited constraint.</span></span> <span data-ttu-id="5bc16-923">Questo può causare i vincoli che non sono validi quando specificato in modo esplicito, ad esempio i tipi di valore o tipi sealed.</span><span class="sxs-lookup"><span data-stu-id="5bc16-923">This can lead to constraints that are not legal when explicitly specified, such as value types or sealed types.</span></span>

<span data-ttu-id="5bc16-924">Nell'esempio seguente viene illustrato come le regole di override funzionano per le classi generiche:</span><span class="sxs-lookup"><span data-stu-id="5bc16-924">The following example demonstrates how the overriding rules work for generic classes:</span></span>
```csharp
abstract class C<T>
{
    public virtual T F() {...}
    public virtual C<T> G() {...}
    public virtual void H(C<T> x) {...}
}

class D: C<string>
{
    public override string F() {...}            // Ok
    public override C<string> G() {...}         // Ok
    public override void H(C<T> x) {...}        // Error, should be C<string>
}

class E<T,U>: C<U>
{
    public override U F() {...}                 // Ok
    public override C<U> G() {...}              // Ok
    public override void H(C<T> x) {...}        // Error, should be C<U>
}
```

<span data-ttu-id="5bc16-925">Una dichiarazione di sostituzione può accedere il metodo di base sottoposto a override utilizzando un *base_access* ([Base accesso](expressions.md#base-access)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-925">An override declaration can access the overridden base method using a *base_access* ([Base access](expressions.md#base-access)).</span></span> <span data-ttu-id="5bc16-926">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-926">In the example</span></span>
```csharp
class A
{
    int x;

    public virtual void PrintFields() {
        Console.WriteLine("x = {0}", x);
    }
}

class B: A
{
    int y;

    public override void PrintFields() {
        base.PrintFields();
        Console.WriteLine("y = {0}", y);
    }
}
```
<span data-ttu-id="5bc16-927">il `base.PrintFields()` chiamata `B` richiama le `PrintFields` metodo dichiarato come `A`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-927">the `base.PrintFields()` invocation in `B` invokes the `PrintFields` method declared in `A`.</span></span> <span data-ttu-id="5bc16-928">Oggetto *base_access* disabilita il meccanismo di chiamata virtuale e gestisce semplicemente il metodo di base come un metodo non virtuale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-928">A *base_access* disables the virtual invocation mechanism and simply treats the base method as a non-virtual method.</span></span> <span data-ttu-id="5bc16-929">Avevano la chiamata in `B` stato scritti `((A)this).PrintFields()`, avviene in modo ricorsivo richiamare il `PrintFields` metodo è dichiarato `B`, non con quello dichiarato nel `A`, poiché `PrintFields` è virtuale e il tipo di runtime di `((A)this)` è `B`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-929">Had the invocation in `B` been written `((A)this).PrintFields()`, it would recursively invoke the `PrintFields` method declared in `B`, not the one declared in `A`, since `PrintFields` is virtual and the run-time type of `((A)this)` is `B`.</span></span>

<span data-ttu-id="5bc16-930">Solo includendo un `override` can modificatore un metodo esegue l'override di un altro metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-930">Only by including an `override` modifier can a method override another method.</span></span> <span data-ttu-id="5bc16-931">In tutti gli altri casi, un metodo con la stessa firma di un metodo ereditato nasconde semplicemente il metodo ereditato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-931">In all other cases, a method with the same signature as an inherited method simply hides the inherited method.</span></span> <span data-ttu-id="5bc16-932">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-932">In the example</span></span>
```csharp
class A
{
    public virtual void F() {}
}

class B: A
{
    public virtual void F() {}        // Warning, hiding inherited F()
}
```
<span data-ttu-id="5bc16-933">il `F` metodo nella `B` non include un' `override` modificatore e pertanto non esegue l'override di `F` metodo nella `A`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-933">the `F` method in `B` does not include an `override` modifier and therefore does not override the `F` method in `A`.</span></span> <span data-ttu-id="5bc16-934">Piuttosto, il `F` metodo nella `B` nasconde il metodo nella `A`, e viene segnalato un avviso perché la dichiarazione non include un `new` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-934">Rather, the `F` method in `B` hides the method in `A`, and a warning is reported because the declaration does not include a `new` modifier.</span></span>

<span data-ttu-id="5bc16-935">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-935">In the example</span></span>
```csharp
class A
{
    public virtual void F() {}
}

class B: A
{
    new private void F() {}        // Hides A.F within body of B
}

class C: B
{
    public override void F() {}    // Ok, overrides A.F
}
```
<span data-ttu-id="5bc16-936">il `F` nel metodo `B` nasconde virtuale `F` metodo ereditato dal `A`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-936">the `F` method in `B` hides the virtual `F` method inherited from `A`.</span></span> <span data-ttu-id="5bc16-937">Poiché il nuovo `F` nelle `B` con accesso privato, del relativo ambito include solo il corpo della classe di `B` e non si estende a `C`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-937">Since the new `F` in `B` has private access, its scope only includes the class body of `B` and does not extend to `C`.</span></span> <span data-ttu-id="5bc16-938">Di conseguenza, la dichiarazione di `F` nel `C` è la possibilità di eseguire l'override di `F` ereditato da `A`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-938">Therefore, the declaration of `F` in `C` is permitted to override the `F` inherited from `A`.</span></span>

### <a name="sealed-methods"></a><span data-ttu-id="5bc16-939">Metodi sealed</span><span class="sxs-lookup"><span data-stu-id="5bc16-939">Sealed methods</span></span>

<span data-ttu-id="5bc16-940">Quando una dichiarazione di metodo di istanza include un `sealed` modificatore, questo metodo viene definito da una ***sealed metodo***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-940">When an instance method declaration includes a `sealed` modifier, that method is said to be a ***sealed method***.</span></span> <span data-ttu-id="5bc16-941">Se una dichiarazione di metodo di istanza include la `sealed` modificatore, deve includere anche il `override` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-941">If an instance method declaration includes the  `sealed` modifier, it must also include the `override` modifier.</span></span> <span data-ttu-id="5bc16-942">Usare il `sealed` modificatore impedisce che una classe derivata ulteriormente l'override del metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-942">Use of the `sealed` modifier prevents a derived class from further overriding the method.</span></span>

<span data-ttu-id="5bc16-943">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-943">In the example</span></span>
```csharp
using System;

class A
{
    public virtual void F() {
        Console.WriteLine("A.F");
    }

    public virtual void G() {
        Console.WriteLine("A.G");
    }
}

class B: A
{
    sealed override public void F() {
        Console.WriteLine("B.F");
    } 

    override public void G() {
        Console.WriteLine("B.G");
    } 
}

class C: B
{
    override public void G() {
        Console.WriteLine("C.G");
    } 
}
```
<span data-ttu-id="5bc16-944">la classe `B` fornisce due metodi di override: un' `F` metodo che presenta le `sealed` modificatore e un `G` metodo che non sono presenti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-944">the class `B` provides two override methods: an `F` method that has the `sealed` modifier and a `G` method that does not.</span></span> <span data-ttu-id="5bc16-945">`B`utilizzo del sealed `modifier` impedisce `C` sovrascrivano ulteriormente `F`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-945">`B`'s use of the sealed `modifier` prevents `C` from further overriding `F`.</span></span>

### <a name="abstract-methods"></a><span data-ttu-id="5bc16-946">Metodi astratti</span><span class="sxs-lookup"><span data-stu-id="5bc16-946">Abstract methods</span></span>

<span data-ttu-id="5bc16-947">Quando una dichiarazione di metodo di istanza include un' `abstract` modificatore, questo metodo viene definito da un' ***metodo astratto***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-947">When an instance method declaration includes an `abstract` modifier, that method is said to be an ***abstract method***.</span></span> <span data-ttu-id="5bc16-948">Anche se un metodo astratto è implicitamente anche un metodo virtuale, non può includere il modificatore `virtual`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-948">Although an abstract method is implicitly also a virtual method, it cannot have the modifier `virtual`.</span></span>

<span data-ttu-id="5bc16-949">Una dichiarazione di metodo astratto introduce un nuovo metodo virtuale ma non fornisce un'implementazione del metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-949">An abstract method declaration introduces a new virtual method but does not provide an implementation of that method.</span></span> <span data-ttu-id="5bc16-950">Invece, le classi derivate non astratte sono necessarie per fornire la propria implementazione eseguendo l'override di tale metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-950">Instead, non-abstract derived classes are required to provide their own implementation by overriding that method.</span></span> <span data-ttu-id="5bc16-951">Poiché un metodo astratto non fornisce alcuna implementazione effettiva, la *method_body* di un metodo astratto costituito semplicemente da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-951">Because an abstract method provides no actual implementation, the *method_body* of an abstract method simply consists of a semicolon.</span></span>

<span data-ttu-id="5bc16-952">Dichiarazioni di metodi astratti sono consentite solo nelle classi astratte ([classi astratte](classes.md#abstract-classes)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-952">Abstract method declarations are only permitted in abstract classes ([Abstract classes](classes.md#abstract-classes)).</span></span>

<span data-ttu-id="5bc16-953">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-953">In the example</span></span>
```csharp
public abstract class Shape
{
    public abstract void Paint(Graphics g, Rectangle r);
}

public class Ellipse: Shape
{
    public override void Paint(Graphics g, Rectangle r) {
        g.DrawEllipse(r);
    }
}

public class Box: Shape
{
    public override void Paint(Graphics g, Rectangle r) {
        g.DrawRect(r);
    }
}
```
<span data-ttu-id="5bc16-954">il `Shape` classe definisce il concetto astratto di un oggetto forma geometrica può disegnare se stessa.</span><span class="sxs-lookup"><span data-stu-id="5bc16-954">the `Shape` class defines the abstract notion of a geometrical shape object that can paint itself.</span></span> <span data-ttu-id="5bc16-955">Il `Paint` metodo è astratto perché non vi è alcuna implementazione predefiniti significativi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-955">The `Paint` method is abstract because there is no meaningful default implementation.</span></span> <span data-ttu-id="5bc16-956">Il `Ellipse` e `Box` sono classi concrete `Shape` implementazioni.</span><span class="sxs-lookup"><span data-stu-id="5bc16-956">The `Ellipse` and `Box` classes are concrete `Shape` implementations.</span></span> <span data-ttu-id="5bc16-957">Poiché queste classi sono astratte, viene richiesto di eseguire l'override di `Paint` metodo e fornire un'implementazione effettiva.</span><span class="sxs-lookup"><span data-stu-id="5bc16-957">Because these classes are non-abstract, they are required to override the `Paint` method and provide an actual implementation.</span></span>

<span data-ttu-id="5bc16-958">Tratta di un errore in fase di compilazione per un *base_access* ([Base accesso](expressions.md#base-access)) per fare riferimento a un metodo astratto.</span><span class="sxs-lookup"><span data-stu-id="5bc16-958">It is a compile-time error for a *base_access* ([Base access](expressions.md#base-access)) to reference an abstract method.</span></span> <span data-ttu-id="5bc16-959">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-959">In the example</span></span>
```csharp
abstract class A
{
    public abstract void F();
}

class B: A
{
    public override void F() {
        base.F();                        // Error, base.F is abstract
    }
}
```
<span data-ttu-id="5bc16-960">viene segnalato un errore in fase di compilazione per il `base.F()` chiamata perché fa riferimento a un metodo astratto.</span><span class="sxs-lookup"><span data-stu-id="5bc16-960">a compile-time error is reported for the `base.F()` invocation because it references an abstract method.</span></span>

<span data-ttu-id="5bc16-961">Una dichiarazione di metodo astratto è consentita eseguire l'override di un metodo virtuale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-961">An abstract method declaration is permitted to override a virtual method.</span></span> <span data-ttu-id="5bc16-962">Questo consente a una classe astratta forzare la nuova implementazione del metodo nelle classi derivate e rende l'implementazione originale del metodo non disponibile.</span><span class="sxs-lookup"><span data-stu-id="5bc16-962">This allows an abstract class to force re-implementation of the method in derived classes, and makes the original implementation of the method unavailable.</span></span> <span data-ttu-id="5bc16-963">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-963">In the example</span></span>
```csharp
using System;

class A
{
    public virtual void F() {
        Console.WriteLine("A.F");
    }
}

abstract class B: A
{
    public abstract override void F();
}

class C: B
{
    public override void F() {
        Console.WriteLine("C.F");
    }
}
```
<span data-ttu-id="5bc16-964">classe `A` dichiara un metodo virtuale, una classe `B` esegue l'override di questo metodo con un metodo astratto, classe `C` esegue l'override del metodo astratto per fornire la propria implementazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-964">class `A` declares a virtual method, class `B` overrides this method with an abstract method, and class `C` overrides the abstract method to provide its own implementation.</span></span>

### <a name="external-methods"></a><span data-ttu-id="5bc16-965">Metodi esterni</span><span class="sxs-lookup"><span data-stu-id="5bc16-965">External methods</span></span>

<span data-ttu-id="5bc16-966">Quando una dichiarazione di metodo include un' `extern` modificatore, questo metodo viene definito da un' ***metodo esterno***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-966">When a method declaration includes an `extern` modifier, that method is said to be an ***external method***.</span></span> <span data-ttu-id="5bc16-967">Metodi esterni vengono implementati all'esterno, in genere tramite un linguaggio diverso da c#.</span><span class="sxs-lookup"><span data-stu-id="5bc16-967">External methods are implemented externally, typically using a language other than C#.</span></span> <span data-ttu-id="5bc16-968">Poiché una dichiarazione di metodo esterno non fornisce alcuna implementazione effettiva, la *method_body* di un metodo esterno costituita semplicemente da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-968">Because an external method declaration provides no actual implementation, the *method_body* of an external method simply consists of a semicolon.</span></span> <span data-ttu-id="5bc16-969">Un metodo esterno non può essere generico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-969">An external method may not be generic.</span></span>

<span data-ttu-id="5bc16-970">Il `extern` modificatore viene in genere usato in combinazione con un `DllImport` attributo ([l'interoperabilità con i componenti di Win32 e COM](attributes.md#interoperation-with-com-and-win32-components)), che consente metodi esterni deve essere implementata da DLL (Dynamic Link Library).</span><span class="sxs-lookup"><span data-stu-id="5bc16-970">The `extern` modifier is typically used in conjunction with a `DllImport` attribute ([Interoperation with COM and Win32 components](attributes.md#interoperation-with-com-and-win32-components)), allowing external methods to be implemented by DLLs (Dynamic Link Libraries).</span></span> <span data-ttu-id="5bc16-971">L'ambiente di esecuzione potrebbe supportare altri meccanismi in base al quale possono essere fornite le implementazioni di metodi esterni.</span><span class="sxs-lookup"><span data-stu-id="5bc16-971">The execution environment may support other mechanisms whereby implementations of external methods can be provided.</span></span>

<span data-ttu-id="5bc16-972">Quando un metodo esterno include un' `DllImport` attributo, la dichiarazione del metodo deve anche includere un `static` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-972">When an external method includes a `DllImport` attribute, the method declaration must also include a `static` modifier.</span></span> <span data-ttu-id="5bc16-973">In questo esempio viene illustrato l'utilizzo dei `extern` modificatore e `DllImport` attributo:</span><span class="sxs-lookup"><span data-stu-id="5bc16-973">This example demonstrates the use of the `extern` modifier and the `DllImport` attribute:</span></span>
```csharp
using System.Text;
using System.Security.Permissions;
using System.Runtime.InteropServices;

class Path
{
    [DllImport("kernel32", SetLastError=true)]
    static extern bool CreateDirectory(string name, SecurityAttribute sa);

    [DllImport("kernel32", SetLastError=true)]
    static extern bool RemoveDirectory(string name);

    [DllImport("kernel32", SetLastError=true)]
    static extern int GetCurrentDirectory(int bufSize, StringBuilder buf);

    [DllImport("kernel32", SetLastError=true)]
    static extern bool SetCurrentDirectory(string name);
}
```

### <a name="partial-methods-recap"></a><span data-ttu-id="5bc16-974">Metodi parziali (riepilogo)</span><span class="sxs-lookup"><span data-stu-id="5bc16-974">Partial methods (recap)</span></span>

<span data-ttu-id="5bc16-975">Quando una dichiarazione di metodo include un `partial` modificatore, questo metodo viene definito da una ***metodo parziale***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-975">When a method declaration includes a `partial` modifier, that method is said to be a ***partial method***.</span></span> <span data-ttu-id="5bc16-976">I metodi parziali possono essere dichiarati solo come membri di tipi parziali ([i tipi parziali](classes.md#partial-types)) e sono soggetti a un numero di restrizioni.</span><span class="sxs-lookup"><span data-stu-id="5bc16-976">Partial methods can only be declared as members of partial types ([Partial types](classes.md#partial-types)), and are subject to a number of restrictions.</span></span> <span data-ttu-id="5bc16-977">I metodi parziali sono descritte dettagliatamente nella [metodi parziali](classes.md#partial-methods).</span><span class="sxs-lookup"><span data-stu-id="5bc16-977">Partial methods are further described in [Partial methods](classes.md#partial-methods).</span></span>

### <a name="extension-methods"></a><span data-ttu-id="5bc16-978">Metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="5bc16-978">Extension methods</span></span>

<span data-ttu-id="5bc16-979">Se il primo parametro di un metodo include il `this` modificatore, questo metodo viene definito da un' ***metodo di estensione***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-979">When the first parameter of a method includes the `this` modifier, that method is said to be an ***extension method***.</span></span> <span data-ttu-id="5bc16-980">I metodi di estensione possono essere dichiarati solo in classi statiche non generica, non annidata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-980">Extension methods can only be declared in non-generic, non-nested static classes.</span></span> <span data-ttu-id="5bc16-981">Il primo parametro di un metodo di estensione non può avere nessun modificatore diverso da `this`, e il tipo di parametro non può essere un tipo di puntatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-981">The first parameter of an extension method can have no modifiers other than `this`, and the parameter type cannot be a pointer type.</span></span>

<span data-ttu-id="5bc16-982">Di seguito è riportato un esempio di una classe statica che dichiara due metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="5bc16-982">The following is an example of a static class that declares two extension methods:</span></span>
```csharp
public static class Extensions
{
    public static int ToInt32(this string s) {
        return Int32.Parse(s);
    }

    public static T[] Slice<T>(this T[] source, int index, int count) {
        if (index < 0 || count < 0 || source.Length - index < count)
            throw new ArgumentException();
        T[] result = new T[count];
        Array.Copy(source, index, result, 0, count);
        return result;
    }
}
```

<span data-ttu-id="5bc16-983">Un metodo di estensione è un normale metodo statico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-983">An extension method is a regular static method.</span></span> <span data-ttu-id="5bc16-984">Inoltre, in cui la classe statica nell'ambito, un metodo di estensione può essere richiamato usando la sintassi di chiamata al metodo di istanza ([chiamate al metodo di estensione](expressions.md#extension-method-invocations)), usando l'espressione del ricevitore come primo argomento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-984">In addition, where its enclosing static class is in scope, an extension method can be invoked using instance method invocation syntax ([Extension method invocations](expressions.md#extension-method-invocations)), using the receiver expression as the first argument.</span></span>

<span data-ttu-id="5bc16-985">Il programma seguente usa i metodi di estensione dichiarati in precedenza:</span><span class="sxs-lookup"><span data-stu-id="5bc16-985">The following program uses the extension methods declared above:</span></span>
```csharp
static class Program
{
    static void Main() {
        string[] strings = { "1", "22", "333", "4444" };
        foreach (string s in strings.Slice(1, 2)) {
            Console.WriteLine(s.ToInt32());
        }
    }
}
```

<span data-ttu-id="5bc16-986">Il `Slice` metodo è disponibile nel `string[]`e il `ToInt32` metodo è disponibile nel `string`, poiché essi sono stati dichiarati come metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-986">The `Slice` method is available on the `string[]`, and the `ToInt32` method is available on `string`, because they have been declared as extension methods.</span></span> <span data-ttu-id="5bc16-987">Il significato del programma è quello utilizzato per le chiamate al metodo statico normale seguente, usando:</span><span class="sxs-lookup"><span data-stu-id="5bc16-987">The meaning of the program is the same as the following, using ordinary static method calls:</span></span>
```csharp
static class Program
{
    static void Main() {
        string[] strings = { "1", "22", "333", "4444" };
        foreach (string s in Extensions.Slice(strings, 1, 2)) {
            Console.WriteLine(Extensions.ToInt32(s));
        }
    }
}
```

### <a name="method-body"></a><span data-ttu-id="5bc16-988">Corpo del metodo</span><span class="sxs-lookup"><span data-stu-id="5bc16-988">Method body</span></span>

<span data-ttu-id="5bc16-989">Il *method_body* di un metodo dichiarazione costituito da un corpo del blocco, un corpo dell'espressione o un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-989">The *method_body* of a method declaration consists of either a block body, an expression body or a semicolon.</span></span>

<span data-ttu-id="5bc16-990">Il ***come tipo di risultato*** di un metodo viene `void` se il tipo restituito `void`, o se il metodo è asincrono e il tipo restituito è `System.Threading.Tasks.Task`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-990">The ***result type*** of a method is `void` if the return type is `void`, or if the method is async and the return type is `System.Threading.Tasks.Task`.</span></span> <span data-ttu-id="5bc16-991">In caso contrario, il tipo di risultato di un metodo non asincrone è il tipo restituito e il tipo di risultato di un metodo asincrono con tipo restituito `System.Threading.Tasks.Task<T>` è `T`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-991">Otherwise, the result type of a non-async method is its return type, and the result type of an async method with return type `System.Threading.Tasks.Task<T>` is `T`.</span></span>

<span data-ttu-id="5bc16-992">Quando un metodo ha un `void` risultato di tipo e un corpo del blocco, `return` istruzioni ([l'istruzione return](statements.md#the-return-statement)) nel blocco non sono consentite per specificare un'espressione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-992">When a method has a `void` result type and a block body, `return` statements ([The return statement](statements.md#the-return-statement)) in the block are not permitted to specify an expression.</span></span> <span data-ttu-id="5bc16-993">Se viene completata l'esecuzione del blocco di un metodo void (vale a dire, flussi di controllo oltre la fine del corpo del metodo), che restituisce semplicemente metodo al relativo chiamante corrente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-993">If execution of the block of a void method completes normally (that is, control flows off the end of the method body), that method simply returns to its current caller.</span></span>
    
<span data-ttu-id="5bc16-994">Quando un metodo ha un `void` risultato e un corpo di espressione, l'espressione `E` deve essere un *statement_expression*, e il corpo è perfettamente equivalente al corpo di un blocco del form `{ E; }`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-994">When a method has a `void` result and an expression body, the expression `E` must be a *statement_expression*, and the body is exactly equivalent to a block body of the form `{ E; }`.</span></span>
    
<span data-ttu-id="5bc16-995">Quando un metodo ha un tipo di risultato non void e un blocco del corpo, ognuna `return` istruzione nel blocco deve specificare un'espressione che è implicitamente convertibile nel tipo di risultato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-995">When a method has a non-void result type and a block body, each `return` statement in the block must specify an expression that is implicitly convertible to the result type.</span></span> <span data-ttu-id="5bc16-996">L'endpoint di un corpo del blocco di un metodo che restituiscono un valore non deve essere raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="5bc16-996">The endpoint of a block body of a value-returning method must not be reachable.</span></span> <span data-ttu-id="5bc16-997">In altre parole, un metodo che restituiscono un valore con un corpo del blocco, controllo non è consentito propagare oltre la fine del corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-997">In other words, in a value-returning method with a block body, control is not permitted to flow off the end of the method body.</span></span>
    
<span data-ttu-id="5bc16-998">Quando un metodo ha un tipo di risultato non void e un corpo di espressione, l'espressione deve essere convertibile in modo implicito al tipo di risultato e il corpo è perfettamente equivalente al corpo di un blocco del form `{ return E; }`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-998">When a method has a non-void result type and an expression body, the expression must be implicitly convertible to the result type, and the body is exactly equivalent to a block body of the form `{ return E; }`.</span></span>
    
<span data-ttu-id="5bc16-999">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-999">In the example</span></span>
```csharp
class A
{
    public int F() {}            // Error, return value required

    public int G() {
        return 1;
    }

    public int H(bool b) {
        if (b) {
            return 1;
        }
        else {
            return 0;
        }
    }

    public int I(bool b) => b ? 1 : 0;
}
```
<span data-ttu-id="5bc16-1000">il valore di restituzione `F` metodo comporta un errore in fase di compilazione perché il controllo può superare la fine del corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1000">the value-returning `F` method results in a compile-time error because control can flow off the end of the method body.</span></span> <span data-ttu-id="5bc16-1001">Il `G` e `H` metodi siano corretti perché tutti i possibili percorsi di esecuzione terminano con un'istruzione return che specifica un valore restituito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1001">The `G` and `H` methods are correct because all possible execution paths end in a return statement that specifies a return value.</span></span> <span data-ttu-id="5bc16-1002">Il `I` metodo sia corretto, perché il relativo corpo è equivalente a un blocco di istruzioni con solo una singola istruzione return in esso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1002">The `I` method is correct, because its body is equivalent to a statement block with just a single return statement in it.</span></span>

### <a name="method-overloading"></a><span data-ttu-id="5bc16-1003">Overload di un metodo</span><span class="sxs-lookup"><span data-stu-id="5bc16-1003">Method overloading</span></span>

<span data-ttu-id="5bc16-1004">Le regole di risoluzione dell'overload di metodo sono descritti [inferenza del tipo](expressions.md#type-inference).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1004">The method overload resolution rules are described in [Type inference](expressions.md#type-inference).</span></span>

## <a name="properties"></a><span data-ttu-id="5bc16-1005">Proprietà</span><span class="sxs-lookup"><span data-stu-id="5bc16-1005">Properties</span></span>

<span data-ttu-id="5bc16-1006">Oggetto ***proprietà*** è un membro che fornisce l'accesso a una caratteristica di un oggetto o una classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1006">A ***property*** is a member that provides access to a characteristic of an object or a class.</span></span> <span data-ttu-id="5bc16-1007">Esempi di proprietà includono la lunghezza di una stringa, le dimensioni di un tipo di carattere, la didascalia di una finestra, il nome di un cliente e così via.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1007">Examples of properties include the length of a string, the size of a font, the caption of a window, the name of a customer, and so on.</span></span> <span data-ttu-id="5bc16-1008">Le proprietà sono un'estensione naturale dei campi, entrambi sono membri denominati con tipi associati e la sintassi per l'accesso a proprietà e campi è la stessa.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1008">Properties are a natural extension of fields—both are named members with associated types, and the syntax for accessing fields and properties is the same.</span></span> <span data-ttu-id="5bc16-1009">A differenza dei campi, tuttavia, le proprietà non denotano posizioni di memoria,</span><span class="sxs-lookup"><span data-stu-id="5bc16-1009">However, unlike fields, properties do not denote storage locations.</span></span> <span data-ttu-id="5bc16-1010">ma hanno ***funzioni di accesso*** che specificano le istruzioni da eseguire nel momento in cui ne vengono letti o scritti i valori.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1010">Instead, properties have ***accessors*** that specify the statements to be executed when their values are read or written.</span></span> <span data-ttu-id="5bc16-1011">Proprietà quindi di fornire un meccanismo per l'associazione di azioni con la lettura e scrittura di attributi di un oggetto. Inoltre, permettono di tali attributi deve essere calcolata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1011">Properties thus provide a mechanism for associating actions with the reading and writing of an object's attributes; furthermore, they permit such attributes to be computed.</span></span>

<span data-ttu-id="5bc16-1012">Le proprietà vengono dichiarate utilizzando *property_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1012">Properties are declared using *property_declaration*s:</span></span>

```antlr
property_declaration
    : attributes? property_modifier* type member_name property_body
    ;

property_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | property_modifier_unsafe
    ;

property_body
    : '{' accessor_declarations '}' property_initializer?
    | '=>' expression ';'
    ;

property_initializer
    : '=' variable_initializer ';'
    ;
```

<span data-ttu-id="5bc16-1013">Oggetto *property_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso ](classes.md#access-modifiers)), il `new` ([il nuovo modificatore](classes.md#the-new-modifier)), `static` ([metodi statici e di istanza](classes.md#static-and-instance-methods)), `virtual` ([metodi virtuali](classes.md#virtual-methods)), `override` ([Metodi di override](classes.md#override-methods)), `sealed` ([Sealed i metodi](classes.md#sealed-methods)), `abstract` ([metodi astratti](classes.md#abstract-methods)), e `extern` ([Metodi esterni](classes.md#external-methods)) modificatori.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1013">A *property_declaration* may include a set of *attributes* ([Attributes](attributes.md)) and a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), the `new` ([The new modifier](classes.md#the-new-modifier)),  `static` ([Static and instance methods](classes.md#static-and-instance-methods)), `virtual` ([Virtual methods](classes.md#virtual-methods)), `override` ([Override methods](classes.md#override-methods)), `sealed` ([Sealed methods](classes.md#sealed-methods)), `abstract` ([Abstract methods](classes.md#abstract-methods)), and `extern` ([External methods](classes.md#external-methods)) modifiers.</span></span>

<span data-ttu-id="5bc16-1014">Le dichiarazioni di proprietà sono soggetti alle stesse regole come le dichiarazioni di metodo ([metodi](classes.md#methods)) in relazione le combinazioni valide di modificatori.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1014">Property declarations are subject to the same rules as method declarations ([Methods](classes.md#methods)) with regard to valid combinations of modifiers.</span></span>

<span data-ttu-id="5bc16-1015">Il *tipo* di una proprietà dichiarazione specifica il tipo della proprietà introdotte dalla dichiarazione e il *member_name* specifica il nome della proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1015">The *type* of a property declaration specifies the type of the property introduced by the declaration, and the *member_name* specifies the name of the property.</span></span> <span data-ttu-id="5bc16-1016">A meno che la proprietà è un'implementazione esplicita dell'interfaccia membro, il *member_name* è semplicemente un' *identificatore*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1016">Unless the property is an explicit interface member implementation, the *member_name* is simply an *identifier*.</span></span> <span data-ttu-id="5bc16-1017">Implementazione di un membro di interfaccia esplicita ([tali implementazioni](interfaces.md#explicit-interface-member-implementations)), il *member_name* è costituito da un *interface_type* seguita da un " `.`"e un' *identificatore*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1017">For an explicit interface member implementation ([Explicit interface member implementations](interfaces.md#explicit-interface-member-implementations)), the *member_name* consists of an *interface_type* followed by a "`.`" and an *identifier*.</span></span>

<span data-ttu-id="5bc16-1018">Il *tipo* di una proprietà devono essere accessibili almeno quanto la stessa proprietà ([vincoli accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1018">The *type* of a property must be at least as accessible as the property itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="5bc16-1019">Oggetto *property_body* possono essere costituiti da un ***corpo della funzione di accesso*** o un ***corpo dell'espressione***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1019">A *property_body* may either consist of an ***accessor body*** or an ***expression body***.</span></span> <span data-ttu-id="5bc16-1020">In un corpo di funzione di accesso *accessor_declarations*, che deve essere racchiuso tra parentesi "`{`"e"`}`" token, dichiarare le funzioni di accesso ([funzioni di accesso](classes.md#accessors)) della proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1020">In an accessor body,  *accessor_declarations*, which must be enclosed in "`{`" and "`}`" tokens, declare the accessors ([Accessors](classes.md#accessors)) of the property.</span></span> <span data-ttu-id="5bc16-1021">Le funzioni di accesso specificare le istruzioni eseguibili associate alla lettura e scrittura della proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1021">The accessors specify the executable statements associated with reading and writing the property.</span></span>

<span data-ttu-id="5bc16-1022">Un corpo di espressione costituita `=>` seguita da un' *espressione* `E` e un punto e virgola è perfettamente equivalente al corpo dell'istruzione `{ get { return E; } }`e può pertanto essere usato solo per specificare solo Get proprietà in cui il risultato del metodo Get è dato da un'unica espressione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1022">An expression body consisting of `=>` followed by an *expression* `E` and a semicolon is exactly equivalent to the statement body `{ get { return E; } }`, and can therefore only be used to specify getter-only properties where the result of the getter is given by a single expression.</span></span>

<span data-ttu-id="5bc16-1023">Oggetto *property_initializer* può essere specificato solo per una proprietà implementata automaticamente ([proprietà implementate automaticamente](classes.md#automatically-implemented-properties)) e fa sì che l'inizializzazione del campo sottostante di tali le proprietà con il valore specificato per il *espressione*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1023">A *property_initializer* may only be given for an automatically implemented property ([Automatically implemented properties](classes.md#automatically-implemented-properties)), and causes the initialization of the underlying field of such properties with the value given by the *expression*.</span></span>

<span data-ttu-id="5bc16-1024">Anche se la sintassi per l'accesso a una proprietà è uguale a quello per un campo, una proprietà non è classificata come una variabile.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1024">Even though the syntax for accessing a property is the same as that for a field, a property is not classified as a variable.</span></span> <span data-ttu-id="5bc16-1025">Non è pertanto possibile passare una proprietà come un `ref` o `out` argomento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1025">Thus, it is not possible to pass a property as a `ref` or `out` argument.</span></span>

<span data-ttu-id="5bc16-1026">Quando una dichiarazione di proprietà include un' `extern` modificatore, la proprietà viene definita un ***proprietà esterna***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1026">When a property declaration includes an `extern` modifier, the property is said to be an ***external property***.</span></span> <span data-ttu-id="5bc16-1027">Poiché una dichiarazione di proprietà esterna non fornisce alcuna implementazione effettiva, ognuno dei relativi *accessor_declarations* è costituito da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1027">Because an external property declaration provides no actual implementation, each of its *accessor_declarations* consists of a semicolon.</span></span>

### <a name="static-and-instance-properties"></a><span data-ttu-id="5bc16-1028">Proprietà statiche e istanza</span><span class="sxs-lookup"><span data-stu-id="5bc16-1028">Static and instance properties</span></span>

<span data-ttu-id="5bc16-1029">Quando una dichiarazione di proprietà contiene un `static` modificatore, la proprietà viene definita un ***proprietà statica***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1029">When a property declaration includes a `static` modifier, the property is said to be a ***static property***.</span></span> <span data-ttu-id="5bc16-1030">Se non si specifica `static` modificatore è presente, la proprietà viene definita un ***proprietà di istanza***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1030">When no `static` modifier is present, the property is said to be an ***instance property***.</span></span>

<span data-ttu-id="5bc16-1031">Una proprietà statica non è associata a un'istanza specifica e tratta di un errore in fase di compilazione per fare riferimento a `this` nelle funzioni di accesso di una proprietà statica.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1031">A static property is not associated with a specific instance, and it is a compile-time error to refer to `this` in the accessors of a static property.</span></span>

<span data-ttu-id="5bc16-1032">Una proprietà dell'istanza è associata a una determinata istanza di una classe, e tale istanza è accessibile come `this` ([questo accesso](expressions.md#this-access)) nelle funzioni di accesso di tale proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1032">An instance property is associated with a given instance of a class, and that instance can be accessed as `this` ([This access](expressions.md#this-access)) in the accessors of that property.</span></span>

<span data-ttu-id="5bc16-1033">Quando si fa riferimento una proprietà un *member_access* ([l'accesso ai membri](expressions.md#member-access)) nel formato `E.M`se `M` è una proprietà statica, `E` deve indicare un tipo contenente `M`e se `M` è una proprietà dell'istanza, È necessario che un'istanza di un tipo contenente `M`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1033">When a property is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, if `M` is a static property, `E` must denote a type containing `M`, and if `M` is an instance property, E must denote an instance of a type containing `M`.</span></span>

<span data-ttu-id="5bc16-1034">Le differenze tra statica e i membri di istanza sono descritti dettagliatamente nella [i membri statici e di istanza](classes.md#static-and-instance-members).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1034">The differences between static and instance members are discussed further in [Static and instance members](classes.md#static-and-instance-members).</span></span>

### <a name="accessors"></a><span data-ttu-id="5bc16-1035">Funzioni di accesso</span><span class="sxs-lookup"><span data-stu-id="5bc16-1035">Accessors</span></span>

<span data-ttu-id="5bc16-1036">Il *accessor_declarations* di una proprietà specificano le istruzioni eseguibili associate alla lettura e scrittura della proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1036">The *accessor_declarations* of a property specify the executable statements associated with reading and writing that property.</span></span>

```antlr
accessor_declarations
    : get_accessor_declaration set_accessor_declaration?
    | set_accessor_declaration get_accessor_declaration?
    ;

get_accessor_declaration
    : attributes? accessor_modifier? 'get' accessor_body
    ;

set_accessor_declaration
    : attributes? accessor_modifier? 'set' accessor_body
    ;

accessor_modifier
    : 'protected'
    | 'internal'
    | 'private'
    | 'protected' 'internal'
    | 'internal' 'protected'
    ;

accessor_body
    : block
    | ';'
    ;
```

<span data-ttu-id="5bc16-1037">Le dichiarazioni di funzione di accesso è costituito un *get_accessor_declaration*, un *set_accessor_declaration*, o entrambi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1037">The accessor declarations consist of a *get_accessor_declaration*, a *set_accessor_declaration*, or both.</span></span> <span data-ttu-id="5bc16-1038">Ogni dichiarazione della funzione di accesso è costituita da token `get` oppure `set` seguita da un oggetto facoltativo *accessor_modifier* e un *accessor_body*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1038">Each accessor declaration consists of the token `get` or `set` followed by an optional *accessor_modifier* and an *accessor_body*.</span></span>

<span data-ttu-id="5bc16-1039">L'uso di *accessor_modifier*s è soggetto alle restrizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1039">The use of *accessor_modifier*s is governed by the following restrictions:</span></span>

*  <span data-ttu-id="5bc16-1040">Un' *accessor_modifier* non può essere utilizzato in un'interfaccia o in un'implementazione di membro di interfaccia esplicita.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1040">An *accessor_modifier* may not be used in an interface or in an explicit interface member implementation.</span></span>
*  <span data-ttu-id="5bc16-1041">Per una proprietà o indicizzatore che non ha alcun `override` modificatore, un' *accessor_modifier* è consentito solo se la proprietà o l'indicizzatore ha entrambe un `get` e `set` della funzione di accesso e quindi è consentita solo per uno di questi funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1041">For a property or indexer that has no `override` modifier, an *accessor_modifier* is permitted only if the property or indexer has both a `get` and `set` accessor, and then is permitted only on one of those accessors.</span></span>
*  <span data-ttu-id="5bc16-1042">Per una proprietà o indicizzatore che include un' `override` modificatore, deve corrispondere a una funzione di accesso di *accessor_modifier*, se presente, della funzione di accesso da sottoporre a override.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1042">For a property or indexer that includes an `override` modifier, an accessor must match the *accessor_modifier*, if any, of the accessor being overridden.</span></span>
*  <span data-ttu-id="5bc16-1043">Il *accessor_modifier* deve dichiarare un'accessibilità strettamente più restrittiva rispetto l'accessibilità dichiarata di proprietà o nell'indicizzatore stesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1043">The *accessor_modifier* must declare an accessibility that is strictly more restrictive than the declared accessibility of the property or indexer itself.</span></span> <span data-ttu-id="5bc16-1044">Per essere precisi:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1044">To be precise:</span></span>
   * <span data-ttu-id="5bc16-1045">Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata del `public`, il *accessor_modifier* può essere `protected internal`, `internal`, `protected`, o `private`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1045">If the property or indexer has a declared accessibility of `public`, the *accessor_modifier* may be either `protected internal`, `internal`, `protected`, or `private`.</span></span>
   * <span data-ttu-id="5bc16-1046">Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata del `protected internal`, il *accessor_modifier* può essere `internal`, `protected`, o `private`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1046">If the property or indexer has a declared accessibility of `protected internal`, the *accessor_modifier* may be either `internal`, `protected`, or `private`.</span></span>
   * <span data-ttu-id="5bc16-1047">Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata del `internal` oppure `protected`, il *accessor_modifier* deve essere `private`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1047">If the property or indexer has a declared accessibility of `internal` or `protected`, the *accessor_modifier* must be `private`.</span></span>
   * <span data-ttu-id="5bc16-1048">Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata del `private`, nessun *accessor_modifier* può essere utilizzato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1048">If the property or indexer has a declared accessibility of `private`, no *accessor_modifier* may be used.</span></span>

<span data-ttu-id="5bc16-1049">Per la `abstract` e `extern` delle proprietà, il *accessor_body* per ogni funzione di accesso specificato è semplicemente un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1049">For `abstract` and `extern` properties, the *accessor_body* for each accessor specified is simply a semicolon.</span></span> <span data-ttu-id="5bc16-1050">Una proprietà non astratto e non extern può avere ognuna *accessor_body* sia un punto e virgola, nel qual caso è un ***proprietà implementata automaticamente*** ([proprietà implementate automaticamente ](classes.md#automatically-implemented-properties)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1050">A non-abstract, non-extern property may have each *accessor_body* be a semicolon, in which case it is an ***automatically implemented property*** ([Automatically implemented properties](classes.md#automatically-implemented-properties)).</span></span> <span data-ttu-id="5bc16-1051">Una proprietà implementata automaticamente deve avere almeno una funzione di accesso get.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1051">An automatically implemented property must have at least a get accessor.</span></span> <span data-ttu-id="5bc16-1052">Per le funzioni di accesso di qualsiasi altra proprietà astratte e non extern, la *accessor_body* è un *blocco* che consente di specificare le istruzioni da eseguire quando viene richiamata la funzione di accesso corrispondente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1052">For the accessors of any other non-abstract, non-extern property, the *accessor_body* is a *block* which specifies the statements to be executed when the corresponding accessor is invoked.</span></span>

<span data-ttu-id="5bc16-1053">Oggetto `get` della funzione di accesso corrispondente a un metodo senza parametri con un valore restituito del tipo di proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1053">A `get` accessor corresponds to a parameterless method with a return value of the property type.</span></span> <span data-ttu-id="5bc16-1054">Fatta eccezione per quanto la destinazione di un'assegnazione, quando viene fatto riferimento a una proprietà in un'espressione, il `get` funzione di accesso della proprietà viene richiamata per calcolare il valore della proprietà ([i valori delle espressioni](expressions.md#values-of-expressions)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1054">Except as the target of an assignment, when a property is referenced in an expression, the `get` accessor of the property is invoked to compute the value of the property ([Values of expressions](expressions.md#values-of-expressions)).</span></span> <span data-ttu-id="5bc16-1055">Il corpo di una `get` funzione di accesso devono essere conformi alle regole per la restituzione di valore metodi descritti nella [corpo del metodo](classes.md#method-body).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1055">The body of a `get` accessor must conform to the rules for value-returning methods described in [Method body](classes.md#method-body).</span></span> <span data-ttu-id="5bc16-1056">In particolare, tutti i `return` istruzioni nel corpo di un `get` della funzione di accesso è necessario specificare un'espressione che è implicitamente convertibile nel tipo di proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1056">In particular, all `return` statements in the body of a `get` accessor must specify an expression that is implicitly convertible to the property type.</span></span> <span data-ttu-id="5bc16-1057">Inoltre, l'endpoint di un `get` della funzione di accesso non deve essere raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1057">Furthermore, the endpoint of a `get` accessor must not be reachable.</span></span>

<span data-ttu-id="5bc16-1058">Oggetto `set` funzione di accesso corrisponde a un metodo con un singolo parametro di valore del tipo di proprietà e un `void` tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1058">A `set` accessor corresponds to a method with a single value parameter of the property type and a `void` return type.</span></span> <span data-ttu-id="5bc16-1059">Il parametro implicito di un `set` funzione di accesso è sempre denominato `value`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1059">The implicit parameter of a `set` accessor is always named `value`.</span></span> <span data-ttu-id="5bc16-1060">Quando una proprietà viene fatto riferimento come destinazione di un'assegnazione ([gli operatori di assegnazione](expressions.md#assignment-operators)), o come operando di `++` oppure `--` ([forma suffissa di incremento e decremento (operatori)](expressions.md#postfix-increment-and-decrement-operators), [ Incremento prefisso e decremento (operatori)](expressions.md#prefix-increment-and-decrement-operators)), il `set` funzione di accesso viene richiamato con un argomento (il cui valore è quello di destra dell'assegnazione o l'operando del `++` o `--` operator) che fornisce il nuovo valore ([assegnamento semplice](expressions.md#simple-assignment)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1060">When a property is referenced as the target of an assignment ([Assignment operators](expressions.md#assignment-operators)), or as the operand of `++` or `--` ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators), [Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)), the `set` accessor is invoked with an argument (whose value is that of the right-hand side of the assignment or the operand of the `++` or `--` operator) that provides the new value ([Simple assignment](expressions.md#simple-assignment)).</span></span> <span data-ttu-id="5bc16-1061">Il corpo di una `set` funzione di accesso devono essere conformi alle regole per `void` metodi descritti in [corpo del metodo](classes.md#method-body).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1061">The body of a `set` accessor must conform to the rules for `void` methods described in [Method body](classes.md#method-body).</span></span> <span data-ttu-id="5bc16-1062">In particolare, `return` istruzioni di `set` corpo della funzione di accesso non è consentito specificare un'espressione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1062">In particular, `return` statements in the `set` accessor body are not permitted to specify an expression.</span></span> <span data-ttu-id="5bc16-1063">Poiché un `set` funzione di accesso in modo implicito include un parametro denominato `value`, tratta di un errore in fase di compilazione per una dichiarazione di costante o variabile locale in un `set` della funzione di accesso quel nome.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1063">Since a `set` accessor implicitly has a parameter named `value`, it is a compile-time error for a local variable or constant declaration in a `set` accessor to have that name.</span></span>

<span data-ttu-id="5bc16-1064">Basato sulla presenza o assenza del `get` e `set` funzioni di accesso, una proprietà viene classificato come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1064">Based on the presence or absence of the `get` and `set` accessors, a property is classified as follows:</span></span>

*  <span data-ttu-id="5bc16-1065">Una proprietà che include sia un `get` funzione di accesso e un `set` funzione di accesso viene definito un ***lettura / scrittura*** proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1065">A property that includes both a `get` accessor and a `set` accessor is said to be a ***read-write*** property.</span></span>
*  <span data-ttu-id="5bc16-1066">Una proprietà che è presente solo una `get` funzione di accesso viene definito un ***sola lettura*** proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1066">A property that has only a `get` accessor is said to be a ***read-only*** property.</span></span> <span data-ttu-id="5bc16-1067">È un errore in fase di compilazione per una proprietà di sola lettura sia la destinazione di un'assegnazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1067">It is a compile-time error for a read-only property to be the target of an assignment.</span></span>
*  <span data-ttu-id="5bc16-1068">Una proprietà che è presente solo una `set` funzione di accesso viene definito un ***sola scrittura*** proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1068">A property that has only a `set` accessor is said to be a ***write-only*** property.</span></span> <span data-ttu-id="5bc16-1069">Ad eccezione del fatto come destinazione di un'assegnazione, è un errore in fase di compilazione per fare riferimento a una proprietà di sola scrittura in un'espressione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1069">Except as the target of an assignment, it is a compile-time error to reference a write-only property in an expression.</span></span>

<span data-ttu-id="5bc16-1070">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1070">In the example</span></span>
```csharp
public class Button: Control
{
    private string caption;

    public string Caption {
        get {
            return caption;
        }
        set {
            if (caption != value) {
                caption = value;
                Repaint();
            }
        }
    }

    public override void Paint(Graphics g, Rectangle r) {
        // Painting code goes here
    }
}
```
<span data-ttu-id="5bc16-1071">il `Button` controllo dichiara un pubblico `Caption` proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1071">the `Button` control declares a public `Caption` property.</span></span> <span data-ttu-id="5bc16-1072">Il `get` funzione di accesso del `Caption` proprietà restituisce la stringa archiviata in privato `caption` campo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1072">The `get` accessor of the `Caption` property returns the string stored in the private `caption` field.</span></span> <span data-ttu-id="5bc16-1073">Il `set` controlla se il nuovo valore è diverso da quello corrente e, in caso affermativo, archivia il nuovo valore di funzione di accesso e ridisegna il controllo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1073">The `set` accessor checks if the new value is different from the current value, and if so, it stores the new value and repaints the control.</span></span> <span data-ttu-id="5bc16-1074">Proprietà spesso seguono il modello illustrato in precedenza: Il `get` funzione di accesso restituisce un valore archiviato in un campo privato e il `set` della funzione di accesso viene modificato quel campo privato e quindi esegue le azioni aggiuntive necessarie per aggiornare completamente lo stato dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1074">Properties often follow the pattern shown above: The `get` accessor simply returns a value stored in a private field, and the `set` accessor modifies that private field and then performs any additional actions required to fully update the state of the object.</span></span>

<span data-ttu-id="5bc16-1075">Dato il `Button` classe precedente, di seguito è riportato un esempio d'uso del `Caption` proprietà:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1075">Given the `Button` class above, the following is an example of use of the `Caption` property:</span></span>
```csharp
Button okButton = new Button();
okButton.Caption = "OK";            // Invokes set accessor
string s = okButton.Caption;        // Invokes get accessor
```

<span data-ttu-id="5bc16-1076">In questo caso, il `set` funzione di accesso viene richiamato tramite l'assegnazione di un valore alla proprietà e il `get` della funzione di accesso viene richiamato da fare riferimento alla proprietà in un'espressione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1076">Here, the `set` accessor is invoked by assigning a value to the property, and the `get` accessor is invoked by referencing the property in an expression.</span></span>

<span data-ttu-id="5bc16-1077">Il `get` e `set` funzioni di accesso di una proprietà non sono membri distinti e non è possibile dichiarare le funzioni di accesso di una proprietà separatamente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1077">The `get` and `set` accessors of a property are not distinct members, and it is not possible to declare the accessors of a property separately.</span></span> <span data-ttu-id="5bc16-1078">Di conseguenza, non possibile per le due funzioni di accesso di una proprietà di lettura / scrittura avere un'accessibilità diversa.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1078">As such, it is not possible for the two accessors of a read-write property to have different accessibility.</span></span> <span data-ttu-id="5bc16-1079">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1079">The example</span></span>
```csharp
class A
{
    private string name;

    public string Name {                // Error, duplicate member name
        get { return name; }
    }

    public string Name {                // Error, duplicate member name
        set { name = value; }
    }
}
```
<span data-ttu-id="5bc16-1080">non dichiarare una singola proprietà di lettura / scrittura.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1080">does not declare a single read-write property.</span></span> <span data-ttu-id="5bc16-1081">Piuttosto, dichiara due proprietà con lo stesso nome, una sola lettura e una sola scrittura.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1081">Rather, it declares two properties with the same name, one read-only and one write-only.</span></span> <span data-ttu-id="5bc16-1082">Poiché i due membri dichiarati nella stessa classe non possono avere lo stesso nome, viene generato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1082">Since two members declared in the same class cannot have the same name, the example causes a compile-time error to occur.</span></span>

<span data-ttu-id="5bc16-1083">Quando una classe derivata dichiara una proprietà con lo stesso nome di una proprietà ereditata, la classe derivata nasconde la proprietà ereditata rispetto alla lettura e scrittura.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1083">When a derived class declares a property by the same name as an inherited property, the derived property hides the inherited property with respect to both reading and writing.</span></span> <span data-ttu-id="5bc16-1084">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1084">In the example</span></span>
```csharp
class A
{
    public int P {
        set {...}
    }
}

class B: A
{
    new public int P {
        get {...}
    }
}
```
<span data-ttu-id="5bc16-1085">il `P` proprietà nel `B` nasconde le `P` proprietà in `A` rispetto alla lettura e scrittura.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1085">the `P` property in `B` hides the `P` property in `A` with respect to both reading and writing.</span></span> <span data-ttu-id="5bc16-1086">Pertanto, nelle istruzioni</span><span class="sxs-lookup"><span data-stu-id="5bc16-1086">Thus, in the statements</span></span>
```csharp
B b = new B();
b.P = 1;          // Error, B.P is read-only
((A)b).P = 1;     // Ok, reference to A.P
```
<span data-ttu-id="5bc16-1087">l'assegnazione al `b.P` provoca un errore in fase di compilazione essere segnalato, dopo di sola lettura `P` proprietà nel `B` nasconde la sola scrittura `P` proprietà `A`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1087">the assignment to `b.P` causes a compile-time error to be reported, since the read-only `P` property in `B` hides the write-only `P` property in `A`.</span></span> <span data-ttu-id="5bc16-1088">Si noti, tuttavia, un cast può essere utilizzato per accedere di nascosto `P` proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1088">Note, however, that a cast can be used to access the hidden `P` property.</span></span>

<span data-ttu-id="5bc16-1089">A differenza dei campi pubblici, le proprietà forniscono una separazione tra lo stato interno di un oggetto e l'interfaccia pubblica.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1089">Unlike public fields, properties provide a separation between an object's internal state and its public interface.</span></span> <span data-ttu-id="5bc16-1090">Si consideri l'esempio:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1090">Consider the example:</span></span>
```csharp
class Label
{
    private int x, y;
    private string caption;

    public Label(int x, int y, string caption) {
        this.x = x;
        this.y = y;
        this.caption = caption;
    }

    public int X {
        get { return x; }
    }

    public int Y {
        get { return y; }
    }

    public Point Location {
        get { return new Point(x, y); }
    }

    public string Caption {
        get { return caption; }
    }
}
```

<span data-ttu-id="5bc16-1091">In questo caso, il `Label` classe utilizza due `int` campi `x` e `y`, per archiviare il percorso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1091">Here, the `Label` class uses two `int` fields, `x` and `y`, to store its location.</span></span> <span data-ttu-id="5bc16-1092">Il percorso è pubblicamente esposto sia come un `X` e una `Y` proprietà e come una `Location` vlastnosti typu `Point`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1092">The location is publicly exposed both as an `X` and a `Y` property and as a `Location` property of type `Point`.</span></span> <span data-ttu-id="5bc16-1093">Se, in una versione futura di `Label`, diventa più pratico archiviare il percorso come un `Point` internamente, la modifica può essere eseguita senza influire sull'interfaccia pubblica della classe:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1093">If, in a future version of `Label`, it becomes more convenient to store the location as a `Point` internally, the change can be made without affecting the public interface of the class:</span></span>
```csharp
class Label
{
    private Point location;
    private string caption;

    public Label(int x, int y, string caption) {
        this.location = new Point(x, y);
        this.caption = caption;
    }

    public int X {
        get { return location.x; }
    }

    public int Y {
        get { return location.y; }
    }

    public Point Location {
        get { return location; }
    }

    public string Caption {
        get { return caption; }
    }
}
```

<span data-ttu-id="5bc16-1094">Aveva `x` e `y` invece stata `public readonly` campi, sarebbe stato impossibile da apportare questa modifica per il `Label` classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1094">Had `x` and `y` instead been `public readonly` fields, it would have been impossible to make such a change to the `Label` class.</span></span>

<span data-ttu-id="5bc16-1095">Esposizione di stato tramite le proprietà non è necessariamente meno efficiente dell'esposizione diretta dei campi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1095">Exposing state through properties is not necessarily any less efficient than exposing fields directly.</span></span> <span data-ttu-id="5bc16-1096">In particolare, quando una proprietà non è virtuale e contiene solo una piccola quantità di codice, l'ambiente di esecuzione potrà sostituire le chiamate a funzioni di accesso con il codice effettivo delle funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1096">In particular, when a property is non-virtual and contains only a small amount of code, the execution environment may replace calls to accessors with the actual code of the accessors.</span></span> <span data-ttu-id="5bc16-1097">Questo processo è noto come ***inlining***, e lo rende più efficiente l'accesso al campo alle proprietà, ma mantiene la maggiore flessibilità delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1097">This process is known as ***inlining***, and it makes property access as efficient as field access, yet preserves the increased flexibility of properties.</span></span>

<span data-ttu-id="5bc16-1098">Dopo il richiamo di un `get` funzione di accesso è concettualmente equivalente alla lettura del valore di un campo, viene considerato non valido dello stile di programmazione `get` funzioni di accesso per avere effetti collaterali osservabili.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1098">Since invoking a `get` accessor is conceptually equivalent to reading the value of a field, it is considered bad programming style for `get` accessors to have observable side-effects.</span></span> <span data-ttu-id="5bc16-1099">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1099">In the example</span></span>
```csharp
class Counter
{
    private int next;

    public int Next {
        get { return next++; }
    }
}
```
<span data-ttu-id="5bc16-1100">Il valore della `Next` proprietà dipende dal numero di volte in cui la proprietà in precedenza è stato eseguito l'accesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1100">the value of the `Next` property depends on the number of times the property has previously been accessed.</span></span> <span data-ttu-id="5bc16-1101">Di conseguenza, l'accesso alla proprietà produce un effetto collaterale considerevole e la proprietà deve essere implementata come metodo invece.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1101">Thus, accessing the property produces an observable side-effect, and the property should be implemented as a method instead.</span></span>

<span data-ttu-id="5bc16-1102">La convenzione "Nessun effetto collaterale" per `get` funzioni di accesso non significa che `get` funzioni di accesso dovrebbe sempre essere scritto limita a restituire i valori archiviati nei campi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1102">The "no side-effects" convention for `get` accessors doesn't mean that `get` accessors should always be written to simply return values stored in fields.</span></span> <span data-ttu-id="5bc16-1103">In effetti, `get` funzioni di accesso spesso calcolare il valore di una proprietà per l'accesso a più campi o metodi di richiamo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1103">Indeed, `get` accessors often compute the value of a property by accessing multiple fields or invoking methods.</span></span> <span data-ttu-id="5bc16-1104">Tuttavia, progettata correttamente `get` della funzione di accesso non esegue alcuna azione che determinano modifiche dell'osservabile nello stato dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1104">However, a properly designed `get` accessor performs no actions that cause observable changes in the state of the object.</span></span>

<span data-ttu-id="5bc16-1105">Proprietà possono essere usate ritardi l'inizializzazione di una risorsa fino al momento in cui che si trova il primo riferimento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1105">Properties can be used to delay initialization of a resource until the moment it is first referenced.</span></span> <span data-ttu-id="5bc16-1106">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1106">For example:</span></span>
```csharp
using System.IO;

public class Console
{
    private static TextReader reader;
    private static TextWriter writer;
    private static TextWriter error;

    public static TextReader In {
        get {
            if (reader == null) {
                reader = new StreamReader(Console.OpenStandardInput());
            }
            return reader;
        }
    }

    public static TextWriter Out {
        get {
            if (writer == null) {
                writer = new StreamWriter(Console.OpenStandardOutput());
            }
            return writer;
        }
    }

    public static TextWriter Error {
        get {
            if (error == null) {
                error = new StreamWriter(Console.OpenStandardError());
            }
            return error;
        }
    }
}
```

<span data-ttu-id="5bc16-1107">Il `Console` classe contiene tre proprietà, `In`, `Out`, e `Error`, che rappresentano l'input standard, output e dispositivi con errori, rispettivamente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1107">The `Console` class contains three properties, `In`, `Out`, and `Error`, that represent the standard input, output, and error devices, respectively.</span></span> <span data-ttu-id="5bc16-1108">Tramite l'esposizione di questi membri come proprietà, il `Console` classe può ritardare l'inizializzazione fino a quando non vengono effettivamente usati.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1108">By exposing these members as properties, the `Console` class can delay their initialization until they are actually used.</span></span> <span data-ttu-id="5bc16-1109">Ad esempio, al primo riferimento di `Out` proprietà, come in</span><span class="sxs-lookup"><span data-stu-id="5bc16-1109">For example, upon first referencing the `Out` property, as in</span></span>
```csharp
Console.Out.WriteLine("hello, world");
```
<span data-ttu-id="5bc16-1110">sottostante `TextWriter` per il dispositivo di output viene creato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1110">the underlying `TextWriter` for the output device is created.</span></span> <span data-ttu-id="5bc16-1111">Tuttavia, se l'applicazione non fa riferimento al `In` e `Error` proprietà, quindi nessun oggetto viene create per tali dispositivi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1111">But if the application makes no reference to the `In` and `Error` properties, then no objects are created for those devices.</span></span>

### <a name="automatically-implemented-properties"></a><span data-ttu-id="5bc16-1112">Proprietà implementate automaticamente</span><span class="sxs-lookup"><span data-stu-id="5bc16-1112">Automatically implemented properties</span></span>

<span data-ttu-id="5bc16-1113">Una proprietà implementata automaticamente (o ***proprietà automatiche*** breve), è una proprietà non extern non astratta con corpi di funzione di accesso solo da punti e virgola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1113">An automatically implemented property (or ***auto-property*** for short), is a non-abstract non-extern property with semicolon-only accessor bodies.</span></span> <span data-ttu-id="5bc16-1114">Proprietà automatiche devono avere una funzione di accesso get e, facoltativamente, possono avere una funzione di accesso set.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1114">Auto-properties must have a get accessor and can optionally have a set accessor.</span></span>

<span data-ttu-id="5bc16-1115">Quando una proprietà viene specificata come una proprietà implementata automaticamente, un campo nascosto sottostante è automaticamente disponibile per la proprietà e le funzioni di accesso vengono implementati per leggere e scrivere in tale campo sottostante.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1115">When a property is specified as an automatically implemented property, a hidden backing field is automatically available for the property, and the accessors are implemented to read from and write to that backing field.</span></span> <span data-ttu-id="5bc16-1116">Se la proprietà automatica non dispone di alcuna funzione di accesso set, il campo sottostante viene considerato `readonly` ([campi di sola lettura](classes.md#readonly-fields)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1116">If the auto-property has no set accessor, the backing field is considered `readonly` ([Readonly fields](classes.md#readonly-fields)).</span></span> <span data-ttu-id="5bc16-1117">Proprio come un `readonly` campo, una proprietà solo Get automatica anche assegnabili a nel corpo di un costruttore della classe contenitrice.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1117">Just like a `readonly` field, a getter-only auto-property can also be assigned to in the body of a constructor of the enclosing class.</span></span> <span data-ttu-id="5bc16-1118">Tale assegnazione assegna direttamente per il campo sottostante di sola lettura della proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1118">Such an assignment assigns directly to the readonly backing field of the property.</span></span>

<span data-ttu-id="5bc16-1119">Una proprietà automatica può facoltativamente possedere un *property_initializer*, che viene applicato direttamente al campo sottostante come una *variable_initializer* ([inizializzatori](classes.md#variable-initializers)) .</span><span class="sxs-lookup"><span data-stu-id="5bc16-1119">An auto-property may optionally have a *property_initializer*, which is applied directly to the backing field as a *variable_initializer* ([Variable initializers](classes.md#variable-initializers)).</span></span>

<span data-ttu-id="5bc16-1120">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1120">The following example:</span></span>
```csharp
public class Point {
    public int X { get; set; } = 0;
    public int Y { get; set; } = 0;
}
```
<span data-ttu-id="5bc16-1121">è equivalente alla dichiarazione seguente:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1121">is equivalent to the following declaration:</span></span>
```csharp
public class Point {
    private int __x = 0;
    private int __y = 0;
    public int X { get { return __x; } set { __x = value; } }
    public int Y { get { return __y; } set { __y = value; } }
}
```

<span data-ttu-id="5bc16-1122">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1122">The following example:</span></span>
```csharp
public class ReadOnlyPoint
{
    public int X { get; }
    public int Y { get; }
    public ReadOnlyPoint(int x, int y) { X = x; Y = y; }
}
```
<span data-ttu-id="5bc16-1123">è equivalente alla dichiarazione seguente:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1123">is equivalent to the following declaration:</span></span>
```csharp
public class ReadOnlyPoint
{
    private readonly int __x;
    private readonly int __y;
    public int X { get { return __x; } }
    public int Y { get { return __y; } }
    public ReadOnlyPoint(int x, int y) { __x = x; __y = y; }
}
```

<span data-ttu-id="5bc16-1124">Si noti che le assegnazioni di campo di sola lettura sono validi, perché si verificano all'interno del costruttore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1124">Notice that the assignments to the readonly field are legal, because they occur within the constructor.</span></span>


### <a name="accessibility"></a><span data-ttu-id="5bc16-1125">Accessibilità</span><span class="sxs-lookup"><span data-stu-id="5bc16-1125">Accessibility</span></span>

<span data-ttu-id="5bc16-1126">Se una funzione di accesso è un *accessor_modifier*, il dominio di accessibilità ([domini accessibilità](basic-concepts.md#accessibility-domains)) della funzione di accesso viene determinato utilizzando l'accessibilità dichiarata del *accessor_modifier* .</span><span class="sxs-lookup"><span data-stu-id="5bc16-1126">If an accessor has an *accessor_modifier*, the accessibility domain ([Accessibility domains](basic-concepts.md#accessibility-domains)) of the accessor is determined using the declared accessibility of the *accessor_modifier*.</span></span> <span data-ttu-id="5bc16-1127">Se una funzione di accesso non dispone di un *accessor_modifier*, il dominio di accessibilità della funzione di accesso è determinato dall'accessibilità dichiarata della proprietà o indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1127">If an accessor does not have an *accessor_modifier*, the accessibility domain of the accessor is determined from the declared accessibility of the property or indexer.</span></span>

<span data-ttu-id="5bc16-1128">La presenza di un' *accessor_modifier* non influisce sulla ricerca di membri ([operatori](expressions.md#operators)) o la risoluzione dell'overload ([risoluzione dell'Overload](expressions.md#overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1128">The presence of an *accessor_modifier* never affects member lookup ([Operators](expressions.md#operators)) or overload resolution ([Overload resolution](expressions.md#overload-resolution)).</span></span> <span data-ttu-id="5bc16-1129">I modificatori nella proprietà o indicizzatore sempre determinano quali proprietà o l'indicizzatore è associato, indipendentemente dal contesto dell'accesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1129">The modifiers on the property or indexer always determine which property or indexer is bound to, regardless of the context of the access.</span></span>

<span data-ttu-id="5bc16-1130">Dopo aver selezionato una proprietà specifica o un indicizzatore, i domini di accessibilità delle funzioni di accesso specifiche coinvolte vengono usati per determinare se tale uso è valido:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1130">Once a particular property or indexer has been selected, the accessibility domains of the specific accessors involved are used to determine if that usage is valid:</span></span>

*  <span data-ttu-id="5bc16-1131">Se l'utilizzo è sotto forma di valore ([valori di espressioni](expressions.md#values-of-expressions)), il `get` della funzione di accesso devono esistere ed essere accessibile.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1131">If the usage is as a value ([Values of expressions](expressions.md#values-of-expressions)), the `get` accessor must exist and be accessible.</span></span>
*  <span data-ttu-id="5bc16-1132">Se l'utilizzo è come destinazione di un'assegnazione semplice ([assegnamento semplice](expressions.md#simple-assignment)), il `set` della funzione di accesso devono esistere ed essere accessibile.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1132">If the usage is as the target of a simple assignment ([Simple assignment](expressions.md#simple-assignment)), the `set` accessor must exist and be accessible.</span></span>
*  <span data-ttu-id="5bc16-1133">Se l'utilizzo è come destinazione di assegnazione composta ([assegnazione composta](expressions.md#compound-assignment)), o come destinazione della `++` oppure `--` operatori ([membri funzione](expressions.md#function-members),9, [ Espressioni di chiamata](expressions.md#invocation-expressions)), sia il `get` le funzioni di accesso e la `set` della funzione di accesso devono esistere ed essere accessibile.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1133">If the usage is as the target of compound assignment ([Compound assignment](expressions.md#compound-assignment)), or as the target of the `++` or `--` operators ([Function members](expressions.md#function-members).9, [Invocation expressions](expressions.md#invocation-expressions)), both the `get` accessors and the `set` accessor must exist and be accessible.</span></span>

<span data-ttu-id="5bc16-1134">Nell'esempio seguente, la proprietà `A.Text` è nascosto dalla proprietà `B.Text`, anche nei contesti in cui solo il `set` viene chiamata della funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1134">In the following example, the property `A.Text` is hidden by the property `B.Text`, even in contexts where only the `set` accessor is called.</span></span> <span data-ttu-id="5bc16-1135">Al contrario, la proprietà `B.Count` non è possibile accedere alla classe `M`, quindi la proprietà accessibile `A.Count` viene invece usato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1135">In contrast, the property `B.Count` is not accessible to class `M`, so the accessible property `A.Count` is used instead.</span></span>

```csharp
class A
{
    public string Text {
        get { return "hello"; }
        set { }
    }

    public int Count {
        get { return 5; }
        set { }
    }
}

class B: A
{
    private string text = "goodbye"; 
    private int count = 0;

    new public string Text {
        get { return text; }
        protected set { text = value; }
    }

    new protected int Count { 
        get { return count; }
        set { count = value; }
    }
}

class M
{
    static void Main() {
        B b = new B();
        b.Count = 12;             // Calls A.Count set accessor
        int i = b.Count;          // Calls A.Count get accessor
        b.Text = "howdy";         // Error, B.Text set accessor not accessible
        string s = b.Text;        // Calls B.Text get accessor
    }
}
```

<span data-ttu-id="5bc16-1136">Una funzione di accesso utilizzato per implementare un'interfaccia non abbia un *accessor_modifier*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1136">An accessor that is used to implement an interface may not have an *accessor_modifier*.</span></span> <span data-ttu-id="5bc16-1137">Se solo una funzione di accesso viene usato per implementare un'interfaccia, l'altra funzione può essere dichiarata con un *accessor_modifier*:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1137">If only one accessor is used to implement an interface, the other accessor may be declared with an *accessor_modifier*:</span></span>
```csharp
public interface I
{
    string Prop { get; }
}

public class C: I
{
    public Prop {
        get { return "April"; }       // Must not have a modifier here
        internal set {...}            // Ok, because I.Prop has no set accessor
    }
}
```

### <a name="virtual-sealed-override-and-abstract-property-accessors"></a><span data-ttu-id="5bc16-1138">Virtuale, sealed, sostituzione e le funzioni di accesso proprietà astratta</span><span class="sxs-lookup"><span data-stu-id="5bc16-1138">Virtual, sealed, override, and abstract property accessors</span></span>

<span data-ttu-id="5bc16-1139">Oggetto `virtual` dichiarazione di proprietà indica che le funzioni di accesso della proprietà sono virtuali.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1139">A `virtual` property declaration specifies that the accessors of the property are virtual.</span></span> <span data-ttu-id="5bc16-1140">Il `virtual` modificatore viene applicato a entrambe le funzioni di accesso di una proprietà di lettura / scrittura, ovvero non è possibile solo una funzione di accesso per una proprietà di lettura / scrittura sia virtuale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1140">The `virtual` modifier applies to both accessors of a read-write property—it is not possible for only one accessor of a read-write property to be virtual.</span></span>

<span data-ttu-id="5bc16-1141">Un `abstract` dichiarazione di proprietà specifica che le funzioni di accesso della proprietà sono virtuali, ma non fornisce un'implementazione effettiva delle funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1141">An `abstract` property declaration specifies that the accessors of the property are virtual, but does not provide an actual implementation of the accessors.</span></span> <span data-ttu-id="5bc16-1142">Invece, le classi derivate non astratte sono necessarie per fornire la propria implementazione per le funzioni di accesso eseguendo l'override della proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1142">Instead, non-abstract derived classes are required to provide their own implementation for the accessors by overriding the property.</span></span> <span data-ttu-id="5bc16-1143">Poiché una funzione di accesso per una dichiarazione di proprietà astratta non fornisce alcuna implementazione effettiva, relativi *accessor_body* costituita semplicemente da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1143">Because an accessor for an abstract property declaration provides no actual implementation, its *accessor_body* simply consists of a semicolon.</span></span>

<span data-ttu-id="5bc16-1144">Una dichiarazione di proprietà che include sia la `abstract` e `override` modificatori specifica che la proprietà è astratta ed esegue l'override di una proprietà di base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1144">A property declaration that includes both the `abstract` and `override` modifiers specifies that the property is abstract and overrides a base property.</span></span> <span data-ttu-id="5bc16-1145">Le funzioni di accesso di tale proprietà anche sono astratte.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1145">The accessors of such a property are also abstract.</span></span>

<span data-ttu-id="5bc16-1146">Le dichiarazioni di proprietà astratti sono consentite solo nelle classi astratte ([classi astratte](classes.md#abstract-classes)). Le funzioni di accesso di una proprietà virtuale ereditata può essere sottoposto a override in una classe derivata includendo una dichiarazione di proprietà che specifica un `override` direttiva.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1146">Abstract property declarations are only permitted in abstract classes ([Abstract classes](classes.md#abstract-classes)).The accessors of an inherited virtual property can be overridden in a derived class by including a property declaration that specifies an `override` directive.</span></span> <span data-ttu-id="5bc16-1147">Questo è noto come un ***eseguendo l'override di dichiarazione di proprietà***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1147">This is known as an ***overriding property declaration***.</span></span> <span data-ttu-id="5bc16-1148">Dichiarazione di proprietà di override non dichiara una nuova proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1148">An overriding property declaration does not declare a new property.</span></span> <span data-ttu-id="5bc16-1149">Al contrario, semplicemente specializzata le implementazioni delle funzioni di accesso di una proprietà virtuale esistente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1149">Instead, it simply specializes the implementations of the accessors of an existing virtual property.</span></span>

<span data-ttu-id="5bc16-1150">Dichiarazione di proprietà di override deve specificare i modificatori di accessibilità stesso esatto, tipo e nome della proprietà ereditata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1150">An overriding property declaration must specify the exact same accessibility modifiers, type, and name as the inherited property.</span></span> <span data-ttu-id="5bc16-1151">Se la proprietà ereditata ha una sola funzione di accesso (ad esempio, se la proprietà ereditata è di sola lettura o in sola lettura), la proprietà di override deve includere solo tale funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1151">If the inherited property has only a single accessor (i.e., if the inherited property is read-only or write-only), the overriding property must include only that accessor.</span></span> <span data-ttu-id="5bc16-1152">Se la proprietà ereditata include entrambe le funzioni di accesso (ad esempio, se la proprietà ereditata è lettura / scrittura), la proprietà di override può includere una sola funzione di accesso o entrambe le funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1152">If the inherited property includes both accessors (i.e., if the inherited property is read-write), the overriding property can include either a single accessor or both accessors.</span></span>

<span data-ttu-id="5bc16-1153">Può includere una dichiarazione di proprietà esegue l'override di `sealed` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1153">An overriding property declaration may include the `sealed` modifier.</span></span> <span data-ttu-id="5bc16-1154">Questo modificatore evita una classe derivata da ulteriormente l'override della proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1154">Use of this modifier prevents a derived class from further overriding the property.</span></span> <span data-ttu-id="5bc16-1155">Le funzioni di accesso di una proprietà sealed inoltre sono sealed.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1155">The accessors of a sealed property are also sealed.</span></span>

<span data-ttu-id="5bc16-1156">Ad eccezione delle differenze nella dichiarazione e la chiamata di sintassi, override virtuale, sealed e astratte delle funzioni di accesso si comportano esattamente come virtual, sealed, override e metodi astratti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1156">Except for differences in declaration and invocation syntax, virtual, sealed, override, and abstract accessors behave exactly like virtual, sealed, override and abstract methods.</span></span> <span data-ttu-id="5bc16-1157">In particolare, le regole descritte [metodi virtuali](classes.md#virtual-methods), [metodi di Override](classes.md#override-methods), [Sealed i metodi](classes.md#sealed-methods), e [metodi astratti](classes.md#abstract-methods) applicare come se le funzioni di accesso sono stati i metodi di un form corrispondente:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1157">Specifically, the rules described in [Virtual methods](classes.md#virtual-methods), [Override methods](classes.md#override-methods), [Sealed methods](classes.md#sealed-methods), and [Abstract methods](classes.md#abstract-methods) apply as if accessors were methods of a corresponding form:</span></span>

*  <span data-ttu-id="5bc16-1158">Oggetto `get` della funzione di accesso corrispondente a un metodo senza parametri con un valore restituito del tipo di proprietà e gli stessi modificatori di proprietà che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1158">A `get` accessor corresponds to a parameterless method with a return value of the property type and the same modifiers as the containing property.</span></span>
*  <span data-ttu-id="5bc16-1159">Oggetto `set` funzione di accesso corrisponde a un metodo con un singolo parametro di valore del tipo di proprietà, un `void` il tipo restituito e gli stessi modificatori di proprietà che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1159">A `set` accessor corresponds to a method with a single value parameter of the property type, a `void` return type, and the same modifiers as the containing property.</span></span>

<span data-ttu-id="5bc16-1160">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1160">In the example</span></span>
```csharp
abstract class A
{
    int y;

    public virtual int X {
        get { return 0; }
    }

    public virtual int Y {
        get { return y; }
        set { y = value; }
    }

    public abstract int Z { get; set; }
}
```
<span data-ttu-id="5bc16-1161">`X` è una proprietà di sola lettura virtuale, `Y` è una proprietà di lettura / scrittura virtuale, e `Z` è una proprietà di lettura / scrittura astratta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1161">`X` is a virtual read-only property, `Y` is a virtual read-write property, and `Z` is an abstract read-write property.</span></span> <span data-ttu-id="5bc16-1162">In quanto `Z` è astratto, la classe contenitore `A` deve inoltre essere dichiarata astratta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1162">Because `Z` is abstract, the containing class `A` must also be declared abstract.</span></span>

<span data-ttu-id="5bc16-1163">Una classe che deriva da `A` è illustrato di seguito:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1163">A class that derives from `A` is show below:</span></span>
```csharp
class B: A
{
    int z;

    public override int X {
        get { return base.X + 1; }
    }

    public override int Y {
        set { base.Y = value < 0? 0: value; }
    }

    public override int Z {
        get { return z; }
        set { z = value; }
    }
}
```

<span data-ttu-id="5bc16-1164">Questo caso, le dichiarazioni delle `X`, `Y`, e `Z` sta eseguendo l'override di dichiarazioni di proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1164">Here, the declarations of `X`, `Y`, and `Z` are overriding property declarations.</span></span> <span data-ttu-id="5bc16-1165">Ogni dichiarazione di proprietà corrisponde esattamente i modificatori di accessibilità, tipo e nome della proprietà ereditata corrispondente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1165">Each property declaration exactly matches the accessibility modifiers, type, and name of the corresponding inherited property.</span></span> <span data-ttu-id="5bc16-1166">Il `get` funzione di accesso di `X` e il `set` funzione di accesso di `Y` usano il `base` parola chiave per accedere alle funzioni di accesso ereditate.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1166">The `get` accessor of `X` and the `set` accessor of `Y` use the `base` keyword to access the inherited accessors.</span></span> <span data-ttu-id="5bc16-1167">La dichiarazione di `Z` esegue l'override di entrambe le funzioni di accesso astratte, pertanto, non esistono membri funzione astratta in sospeso nel `B`, e `B` è la possibilità di essere una classe non astratta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1167">The declaration of `Z` overrides both abstract accessors—thus, there are no outstanding abstract function members in `B`, and `B` is permitted to be a non-abstract class.</span></span>

<span data-ttu-id="5bc16-1168">Quando una proprietà viene dichiarata come un `override`, qualsiasi funzione di accesso sottoposta a override deve essere accessibile al codice di override.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1168">When a property is declared as an `override`, any overridden accessors must be accessible to the overriding code.</span></span> <span data-ttu-id="5bc16-1169">Inoltre, l'accessibilità dichiarata di entrambe le proprietà o nell'indicizzatore stesso e delle funzioni di accesso, deve corrispondere a quello del membro sottoposto a override e le funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1169">In addition, the declared accessibility of both the property or indexer itself, and of the accessors, must match that of the overridden member and accessors.</span></span> <span data-ttu-id="5bc16-1170">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1170">For example:</span></span>
```csharp
public class B
{
    public virtual int P {
        protected set {...}
        get {...}
    }
}

public class D: B
{
    public override int P {
        protected set {...}            // Must specify protected here
        get {...}                      // Must not have a modifier here
    }
}
```

## <a name="events"></a><span data-ttu-id="5bc16-1171">Eventi</span><span class="sxs-lookup"><span data-stu-id="5bc16-1171">Events</span></span>

<span data-ttu-id="5bc16-1172">Un' ***evento*** è un membro che consente a un oggetto o una classe per fornire le notifiche.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1172">An ***event*** is a member that enables an object or class to provide notifications.</span></span> <span data-ttu-id="5bc16-1173">I client è possono collegare il codice eseguibile per gli eventi fornendo ***gestori eventi***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1173">Clients can attach executable code for events by supplying ***event handlers***.</span></span>

<span data-ttu-id="5bc16-1174">Gli eventi vengono dichiarati usando *event_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1174">Events are declared using *event_declaration*s:</span></span>

```antlr
event_declaration
    : attributes? event_modifier* 'event' type variable_declarators ';'
    | attributes? event_modifier* 'event' type member_name '{' event_accessor_declarations '}'
    ;

event_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | event_modifier_unsafe
    ;

event_accessor_declarations
    : add_accessor_declaration remove_accessor_declaration
    | remove_accessor_declaration add_accessor_declaration
    ;

add_accessor_declaration
    : attributes? 'add' block
    ;

remove_accessor_declaration
    : attributes? 'remove' block
    ;
```

<span data-ttu-id="5bc16-1175">Un' *event_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso ](classes.md#access-modifiers)), il `new` ([il nuovo modificatore](classes.md#the-new-modifier)), `static` ([metodi statici e di istanza](classes.md#static-and-instance-methods)), `virtual` ([metodi virtuali](classes.md#virtual-methods)), `override` ([Metodi di override](classes.md#override-methods)), `sealed` ([Sealed i metodi](classes.md#sealed-methods)), `abstract` ([metodi astratti](classes.md#abstract-methods)), e `extern` ([Metodi esterni](classes.md#external-methods)) modificatori.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1175">An *event_declaration* may include a set of *attributes* ([Attributes](attributes.md)) and a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), the `new` ([The new modifier](classes.md#the-new-modifier)),  `static` ([Static and instance methods](classes.md#static-and-instance-methods)), `virtual` ([Virtual methods](classes.md#virtual-methods)), `override` ([Override methods](classes.md#override-methods)), `sealed` ([Sealed methods](classes.md#sealed-methods)), `abstract` ([Abstract methods](classes.md#abstract-methods)), and `extern` ([External methods](classes.md#external-methods)) modifiers.</span></span>

<span data-ttu-id="5bc16-1176">Le dichiarazioni di eventi sono soggetti alle stesse regole come le dichiarazioni di metodo ([metodi](classes.md#methods)) in relazione le combinazioni valide di modificatori.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1176">Event declarations are subject to the same rules as method declarations ([Methods](classes.md#methods)) with regard to valid combinations of modifiers.</span></span>

<span data-ttu-id="5bc16-1177">Il *tipo* di un evento dichiarazione deve essere un *delegate_type* ([fanno riferimento ai tipi](types.md#reference-types)) e che *delegate_type* deve essere almeno come l'evento accessibile ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1177">The *type* of an event declaration must be a *delegate_type* ([Reference types](types.md#reference-types)), and that *delegate_type* must be at least as accessible as the event itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="5bc16-1178">Può includere una dichiarazione di evento *event_accessor_declarations*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1178">An event declaration may include *event_accessor_declarations*.</span></span> <span data-ttu-id="5bc16-1179">Tuttavia, in caso contrario, per non-extern, gli eventi non astratta, il compilatore fornisce automaticamente ([di eventi campo](classes.md#field-like-events)); per gli eventi extern, le funzioni di accesso vengono fornite esternamente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1179">However, if it does not, for non-extern, non-abstract events, the compiler supplies them automatically ([Field-like events](classes.md#field-like-events)); for extern events, the accessors are provided externally.</span></span>

<span data-ttu-id="5bc16-1180">Una dichiarazione di evento che omette *event_accessor_declarations* definisce uno o più eventi, ovvero uno per ogni le *variable_declarator*s.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1180">An event declaration that omits *event_accessor_declarations* defines one or more events—one for each of the *variable_declarator*s.</span></span> <span data-ttu-id="5bc16-1181">Gli attributi e modificatori si applicano a tutti i membri dichiarati da ad un' *event_declaration*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1181">The attributes and modifiers apply to all of the members declared by such an *event_declaration*.</span></span>

<span data-ttu-id="5bc16-1182">Tratta di un errore in fase di compilazione per un *event_declaration* includono entrambe le `abstract` modificatore e delimitato da parentesi graffa *event_accessor_declarations*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1182">It is a compile-time error for an *event_declaration* to include both the `abstract` modifier and brace-delimited *event_accessor_declarations*.</span></span>

<span data-ttu-id="5bc16-1183">Quando una dichiarazione di evento include un' `extern` modificatore, l'evento viene definito un ***evento esterno***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1183">When an event declaration includes an `extern` modifier, the event is said to be an ***external event***.</span></span> <span data-ttu-id="5bc16-1184">Poiché una dichiarazione di evento esterno non fornisce alcuna implementazione effettiva, è consentito includere entrambi i `extern` modificatore e *event_accessor_declarations*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1184">Because an external event declaration provides no actual implementation, it is an error for it to include both the `extern` modifier and *event_accessor_declarations*.</span></span>

<span data-ttu-id="5bc16-1185">È un errore in fase di compilazione per un *variable_declarator* di una dichiarazione di evento con un `abstract` oppure `external` modificatore per includere una *variable_initializer*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1185">It is a compile-time error for a *variable_declarator* of an event declaration with an `abstract` or `external` modifier to include a *variable_initializer*.</span></span>

<span data-ttu-id="5bc16-1186">Un evento può essere utilizzato come operando di sinistra del `+=` e `-=` operatori ([assegnazione evento](expressions.md#event-assignment)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1186">An event can be used as the left-hand operand of the `+=` and `-=` operators ([Event assignment](expressions.md#event-assignment)).</span></span> <span data-ttu-id="5bc16-1187">Questi operatori vengono utilizzati, rispettivamente, per collegare gestori di eventi a o per rimuovere i gestori di eventi da un evento, e i modificatori di accesso dell'evento di controllano i contesti in cui tali operazioni sono consentite.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1187">These operators are used, respectively, to attach event handlers to or to remove event handlers from an event, and the access modifiers of the event control the contexts in which such operations are permitted.</span></span>

<span data-ttu-id="5bc16-1188">Poiché `+=` e `-=` sono le uniche operazioni consentite su un evento all'esterno del tipo che dichiara l'evento, il codice esterno possono aggiungere e rimuovere gestori per un evento, ma non possono in alcun altro modo ottenere o modificare l'elenco sottostante dell'evento gestori.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1188">Since `+=` and `-=` are the only operations that are permitted on an event outside the type that declares the event, external code can add and remove handlers for an event, but cannot in any other way obtain or modify the underlying list of event handlers.</span></span>

<span data-ttu-id="5bc16-1189">In un'operazione del form `x += y` oppure `x -= y`, quando `x` è un evento e il riferimento viene eseguita all'esterno del tipo che contiene la dichiarazione del `x`, il risultato dell'operazione ha tipo `void` (eliminando la necessità il tipo della `x`, con il valore di `x` dopo l'assegnazione).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1189">In an operation of the form `x += y` or `x -= y`, when `x` is an event and the reference takes place outside the type that contains the declaration of `x`, the result of the operation has type `void` (as opposed to having the type of `x`, with the value of `x` after the assignment).</span></span> <span data-ttu-id="5bc16-1190">Questa regola impedisce indirettamente esaminando il delegato sottostante di un evento di codice esterno.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1190">This rule prohibits external code from indirectly examining the underlying delegate of an event.</span></span>

<span data-ttu-id="5bc16-1191">Nell'esempio seguente mostra come i gestori eventi sono collegati a istanze del `Button` classe:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1191">The following example shows how event handlers are attached to instances of the `Button` class:</span></span>
```csharp
public delegate void EventHandler(object sender, EventArgs e);

public class Button: Control
{
    public event EventHandler Click;
}

public class LoginDialog: Form
{
    Button OkButton;
    Button CancelButton;

    public LoginDialog() {
        OkButton = new Button(...);
        OkButton.Click += new EventHandler(OkButtonClick);
        CancelButton = new Button(...);
        CancelButton.Click += new EventHandler(CancelButtonClick);
    }

    void OkButtonClick(object sender, EventArgs e) {
        // Handle OkButton.Click event
    }

    void CancelButtonClick(object sender, EventArgs e) {
        // Handle CancelButton.Click event
    }
}
```

<span data-ttu-id="5bc16-1192">In questo caso, il `LoginDialog` costruttore di istanze vengono create due `Button` istanze e i gestori eventi per il `Click` gli eventi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1192">Here, the `LoginDialog` instance constructor creates two `Button` instances and attaches event handlers to the `Click` events.</span></span>

### <a name="field-like-events"></a><span data-ttu-id="5bc16-1193">Campo di eventi</span><span class="sxs-lookup"><span data-stu-id="5bc16-1193">Field-like events</span></span>

<span data-ttu-id="5bc16-1194">All'interno del testo di programma della classe o struct che contiene la dichiarazione di un evento, alcuni eventi possono essere usati come i campi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1194">Within the program text of the class or struct that contains the declaration of an event, certain events can be used like fields.</span></span> <span data-ttu-id="5bc16-1195">Per essere utilizzato in questo modo, un evento non deve essere `abstract` oppure `extern`e non deve includere in modo esplicito *event_accessor_declarations*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1195">To be used in this way, an event must not be `abstract` or `extern`, and must not explicitly include *event_accessor_declarations*.</span></span> <span data-ttu-id="5bc16-1196">Tale evento può essere utilizzato in qualsiasi contesto che consente a un campo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1196">Such an event can be used in any context that permits a field.</span></span> <span data-ttu-id="5bc16-1197">Il campo contiene un delegato ([delegati](delegates.md)) che fa riferimento all'elenco di gestori di eventi che sono stati aggiunti all'evento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1197">The field contains a delegate ([Delegates](delegates.md)) which refers to the list of event handlers that have been added to the event.</span></span> <span data-ttu-id="5bc16-1198">Se nessun gestore di eventi sono stati aggiunti, il campo contiene `null`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1198">If no event handlers have been added, the field contains `null`.</span></span>

<span data-ttu-id="5bc16-1199">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1199">In the example</span></span>
```csharp
public delegate void EventHandler(object sender, EventArgs e);

public class Button: Control
{
    public event EventHandler Click;

    protected void OnClick(EventArgs e) {
        if (Click != null) Click(this, e);
    }

    public void Reset() {
        Click = null;
    }
}
```
<span data-ttu-id="5bc16-1200">`Click` viene utilizzato come un campo all'interno di `Button` classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1200">`Click` is used as a field within the `Button` class.</span></span> <span data-ttu-id="5bc16-1201">Come illustrato nell'esempio, il campo può essere esaminato, modificato e utilizzato nelle espressioni di chiamata al delegato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1201">As the example demonstrates, the field can be examined, modified, and used in delegate invocation expressions.</span></span> <span data-ttu-id="5bc16-1202">Il `OnClick` metodo nella `Button` classe "genera" il `Click` evento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1202">The `OnClick` method in the `Button` class "raises" the `Click` event.</span></span> <span data-ttu-id="5bc16-1203">Generare un evento equivale a richiamare il delegato rappresentato dall'evento. Non sono quindi necessari speciali costrutti di linguaggio per generare eventi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1203">The notion of raising an event is precisely equivalent to invoking the delegate represented by the event—thus, there are no special language constructs for raising events.</span></span> <span data-ttu-id="5bc16-1204">Si noti che la chiamata di delegato è preceduta da un controllo, per garantire che il delegato è diverso da null.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1204">Note that the delegate invocation is preceded by a check that ensures the delegate is non-null.</span></span>

<span data-ttu-id="5bc16-1205">All'esterno della dichiarazione del `Button` (classe), il `Click` membro può essere utilizzato solo sul lato sinistro delle `+=` e `-=` operatori, come in</span><span class="sxs-lookup"><span data-stu-id="5bc16-1205">Outside the declaration of the `Button` class, the `Click` member can only be used on the left-hand side of the `+=` and `-=` operators, as in</span></span>
```csharp
b.Click += new EventHandler(...);
```
<span data-ttu-id="5bc16-1206">Aggiunge un delegato all'elenco chiamate del `Click` evento, e</span><span class="sxs-lookup"><span data-stu-id="5bc16-1206">which appends a delegate to the invocation list of the `Click` event, and</span></span>
```csharp
b.Click -= new EventHandler(...);
```
<span data-ttu-id="5bc16-1207">Rimuove un delegato dall'elenco chiamate del `Click` evento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1207">which removes a delegate from the invocation list of the `Click` event.</span></span>

<span data-ttu-id="5bc16-1208">Durante la compilazione di un evento simile a campo, il compilatore può automaticamente crea archiviazione per contenere il delegato e crea le funzioni di accesso per l'evento che aggiungono o rimuovono gestori eventi per il campo di delegato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1208">When compiling a field-like event, the compiler automatically creates storage to hold the delegate, and creates accessors for the event that add or remove event handlers to the delegate field.</span></span> <span data-ttu-id="5bc16-1209">Le operazioni di aggiunta e rimozione sono thread-safe e può (ma non si deve) essere eseguite mentre viene mantenuto il blocco ([istruzione lock](statements.md#the-lock-statement)) l'oggetto contenitore per un evento di istanza, o l'oggetto di tipo ([anonima espressioni per la creazione dell'oggetto](expressions.md#anonymous-object-creation-expressions)) per un evento statico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1209">The addition and removal operations are thread safe, and may (but are not required to) be done while holding the lock ([The lock statement](statements.md#the-lock-statement)) on the containing object for an instance event, or the type object ([Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions)) for a static event.</span></span>

<span data-ttu-id="5bc16-1210">Di conseguenza, una dichiarazione di evento del form:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1210">Thus, an instance event declaration of the form:</span></span>
```csharp
class X
{
    public event D Ev;
}
```
<span data-ttu-id="5bc16-1211">verrà compilato su un valore equivalente a:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1211">will be compiled to something equivalent to:</span></span>
```csharp
class X
{
    private D __Ev;  // field to hold the delegate

    public event D Ev {
        add {
            /* add the delegate in a thread safe way */
        }

        remove {
            /* remove the delegate in a thread safe way */
        }
    }
}
```
<span data-ttu-id="5bc16-1212">All'interno della classe `X`, fa riferimento alle `Ev` sul lato sinistro delle `+=` e `-=` operatori causano l'aggiunta e rimuovere le funzioni di accesso da richiamare.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1212">Within the class `X`, references to `Ev` on the left-hand side of the `+=` and `-=` operators cause the add and remove accessors to be invoked.</span></span> <span data-ttu-id="5bc16-1213">Tutti gli altri riferimenti a `Ev` vengono compilati per fare riferimento a campo nascosto `__Ev` invece ([l'accesso ai membri](expressions.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1213">All other references to `Ev` are compiled to reference the hidden field `__Ev` instead ([Member access](expressions.md#member-access)).</span></span> <span data-ttu-id="5bc16-1214">Il nome "`__Ev`" è arbitrario; il campo nascosto potrebbe avere qualsiasi nome o senza nome affatto.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1214">The name "`__Ev`" is arbitrary; the hidden field could have any name or no name at all.</span></span>

### <a name="event-accessors"></a><span data-ttu-id="5bc16-1215">Funzioni di accesso agli eventi</span><span class="sxs-lookup"><span data-stu-id="5bc16-1215">Event accessors</span></span>

<span data-ttu-id="5bc16-1216">Le dichiarazioni di eventi in genere omettono *event_accessor_declarations*, come nel `Button` esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1216">Event declarations typically omit *event_accessor_declarations*, as in the `Button` example above.</span></span> <span data-ttu-id="5bc16-1217">Una situazione che richiede ad esempio, quando comporta pertanto il caso in cui il costo di archiviazione di un campo per ogni evento non è accettabile.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1217">One situation for doing so involves the case in which the storage cost of one field per event is not acceptable.</span></span> <span data-ttu-id="5bc16-1218">In questi casi, una classe può comprendere *event_accessor_declarations* e usare un meccanismo privato per archiviare l'elenco dei gestori di eventi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1218">In such cases, a class can include *event_accessor_declarations* and use a private mechanism for storing the list of event handlers.</span></span>

<span data-ttu-id="5bc16-1219">Il *event_accessor_declarations* di un evento specificano le istruzioni eseguibili associate all'aggiunta e rimozione di gestori di eventi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1219">The *event_accessor_declarations* of an event specify the executable statements associated with adding and removing event handlers.</span></span>

<span data-ttu-id="5bc16-1220">Le dichiarazioni di funzione di accesso è costituito un *add_accessor_declaration* e una *remove_accessor_declaration*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1220">The accessor declarations consist of an *add_accessor_declaration* and a *remove_accessor_declaration*.</span></span> <span data-ttu-id="5bc16-1221">Ogni dichiarazione della funzione di accesso è costituita da token `add` oppure `remove` seguita da un *blocco*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1221">Each accessor declaration consists of the token `add` or `remove` followed by a *block*.</span></span> <span data-ttu-id="5bc16-1222">Il *blocco* associata a un *add_accessor_declaration* specifica le istruzioni da eseguire quando viene aggiunto un gestore eventi e il *blocco* associato con un *remove_accessor_declaration* specifica le istruzioni da eseguire quando un gestore eventi viene rimosso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1222">The *block* associated with an *add_accessor_declaration* specifies the statements to execute when an event handler is added, and the *block* associated with a *remove_accessor_declaration* specifies the statements to execute when an event handler is removed.</span></span>

<span data-ttu-id="5bc16-1223">Ciascuna *add_accessor_declaration* e *remove_accessor_declaration* corrisponde a un metodo con un singolo parametro di valore del tipo di evento e un `void` tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1223">Each *add_accessor_declaration* and *remove_accessor_declaration* corresponds to a method with a single value parameter of the event type and a `void` return type.</span></span> <span data-ttu-id="5bc16-1224">Il parametro implicito di una funzione di accesso di evento è denominato `value`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1224">The implicit parameter of an event accessor is named `value`.</span></span> <span data-ttu-id="5bc16-1225">Quando un evento viene utilizzato in un'assegnazione di eventi, viene utilizzata la funzione di accesso eventi appropriato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1225">When an event is used in an event assignment, the appropriate event accessor is used.</span></span> <span data-ttu-id="5bc16-1226">In particolare, se è l'operatore di assegnazione `+=` viene utilizzata la funzione di accesso add e se l'operatore di assegnazione è `-=` viene utilizzata la funzione di accesso remove.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1226">Specifically, if the assignment operator is `+=` then the add accessor is used, and if the assignment operator is `-=` then the remove accessor is used.</span></span> <span data-ttu-id="5bc16-1227">In entrambi i casi, l'operando destro dell'operatore di assegnazione viene utilizzato come argomento alla funzione di accesso eventi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1227">In either case, the right-hand operand of the assignment operator is used as the argument to the event accessor.</span></span> <span data-ttu-id="5bc16-1228">Il blocco di un *add_accessor_declaration* o una *remove_accessor_declaration* devono essere conformi alle regole per `void` metodi descritti in [corpo del metodo](classes.md#method-body).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1228">The block of an *add_accessor_declaration* or a *remove_accessor_declaration* must conform to the rules for `void` methods described in [Method body](classes.md#method-body).</span></span> <span data-ttu-id="5bc16-1229">In particolare, `return` le istruzioni in questo blocco non è consentite specificare un'espressione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1229">In particular, `return` statements in such a block are not permitted to specify an expression.</span></span>

<span data-ttu-id="5bc16-1230">Poiché una funzione di accesso eventi in modo implicito include un parametro denominato `value`, è un errore in fase di compilazione per una costante o variabile locale dichiarata in una funzione di accesso eventi quel nome.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1230">Since an event accessor implicitly has a parameter named `value`, it is a compile-time error for a local variable or constant declared in an event accessor to have that name.</span></span>

<span data-ttu-id="5bc16-1231">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1231">In the example</span></span>
```csharp
class Control: Component
{
    // Unique keys for events
    static readonly object mouseDownEventKey = new object();
    static readonly object mouseUpEventKey = new object();

    // Return event handler associated with key
    protected Delegate GetEventHandler(object key) {...}

    // Add event handler associated with key
    protected void AddEventHandler(object key, Delegate handler) {...}

    // Remove event handler associated with key
    protected void RemoveEventHandler(object key, Delegate handler) {...}

    // MouseDown event
    public event MouseEventHandler MouseDown {
        add { AddEventHandler(mouseDownEventKey, value); }
        remove { RemoveEventHandler(mouseDownEventKey, value); }
    }

    // MouseUp event
    public event MouseEventHandler MouseUp {
        add { AddEventHandler(mouseUpEventKey, value); }
        remove { RemoveEventHandler(mouseUpEventKey, value); }
    }

    // Invoke the MouseUp event
    protected void OnMouseUp(MouseEventArgs args) {
        MouseEventHandler handler; 
        handler = (MouseEventHandler)GetEventHandler(mouseUpEventKey);
        if (handler != null)
            handler(this, args);
    }
}
```
<span data-ttu-id="5bc16-1232">il `Control` classe implementa un meccanismo di archiviazione interno per gli eventi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1232">the `Control` class implements an internal storage mechanism for events.</span></span> <span data-ttu-id="5bc16-1233">Il `AddEventHandler` metodo associa il valore di un delegato con una chiave, il `GetEventHandler` metodo viene restituito il delegato attualmente associato a una chiave e il `RemoveEventHandler` metodo rimuove un delegato come gestore eventi per l'evento specificato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1233">The `AddEventHandler` method associates a delegate value with a key, the `GetEventHandler` method returns the delegate currently associated with a key, and the `RemoveEventHandler` method removes a delegate as an event handler for the specified event.</span></span> <span data-ttu-id="5bc16-1234">È probabile che il meccanismo di archiviazione sottostante è progettato in modo che non sono previsti costi per l'associazione di un `null` delegare valore con una chiave, così da eventi non gestiti alcuna archiviazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1234">Presumably, the underlying storage mechanism is designed such that there is no cost for associating a `null` delegate value with a key, and thus unhandled events consume no storage.</span></span>

### <a name="static-and-instance-events"></a><span data-ttu-id="5bc16-1235">Eventi statici e di istanza</span><span class="sxs-lookup"><span data-stu-id="5bc16-1235">Static and instance events</span></span>

<span data-ttu-id="5bc16-1236">Quando una dichiarazione di evento include un `static` modificatore, l'evento viene definito un ***evento statico***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1236">When an event declaration includes a `static` modifier, the event is said to be a ***static event***.</span></span> <span data-ttu-id="5bc16-1237">Se non si specifica `static` modificatore è presente, l'evento viene definito un ***evento istanza***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1237">When no `static` modifier is present, the event is said to be an ***instance event***.</span></span>

<span data-ttu-id="5bc16-1238">Un evento statico non è associato a un'istanza specifica e tratta di un errore in fase di compilazione per fare riferimento a `this` in funzioni di accesso di un evento statico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1238">A static event is not associated with a specific instance, and it is a compile-time error to refer to `this` in the accessors of a static event.</span></span>

<span data-ttu-id="5bc16-1239">Un evento di istanza è associato a una determinata istanza di una classe e questa istanza è accessibile come `this` ([questo accesso](expressions.md#this-access)) nelle funzioni di accesso di tale evento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1239">An instance event is associated with a given instance of a class, and this instance can be accessed as `this` ([This access](expressions.md#this-access)) in the accessors of that event.</span></span>

<span data-ttu-id="5bc16-1240">Quando si fa riferimento a un evento un *member_access* ([l'accesso ai membri](expressions.md#member-access)) nel formato `E.M`se `M` è un evento statico, `E` deve indicare un tipo contenente `M`e se `M` è un evento di istanza, È necessario che un'istanza di un tipo contenente `M`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1240">When an event is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, if `M` is a static event, `E` must denote a type containing `M`, and if `M` is an instance event, E must denote an instance of a type containing `M`.</span></span>

<span data-ttu-id="5bc16-1241">Le differenze tra statica e i membri di istanza sono descritti dettagliatamente nella [i membri statici e di istanza](classes.md#static-and-instance-members).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1241">The differences between static and instance members are discussed further in [Static and instance members](classes.md#static-and-instance-members).</span></span>

### <a name="virtual-sealed-override-and-abstract-event-accessors"></a><span data-ttu-id="5bc16-1242">Virtuale, sealed, override e funzioni di accesso eventi astratti</span><span class="sxs-lookup"><span data-stu-id="5bc16-1242">Virtual, sealed, override, and abstract event accessors</span></span>

<span data-ttu-id="5bc16-1243">Oggetto `virtual` dichiarazione di evento indica che le funzioni di accesso dell'evento in questione sono virtuali.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1243">A `virtual` event declaration specifies that the accessors of that event are virtual.</span></span> <span data-ttu-id="5bc16-1244">Il `virtual` modificatore viene applicato a entrambe le funzioni di accesso di un evento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1244">The `virtual` modifier applies to both accessors of an event.</span></span>

<span data-ttu-id="5bc16-1245">Un `abstract` dichiarazione di evento indica che le funzioni di accesso dell'evento sono virtuali, ma non fornisce un'implementazione effettiva delle funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1245">An `abstract` event declaration specifies that the accessors of the event are virtual, but does not provide an actual implementation of the accessors.</span></span> <span data-ttu-id="5bc16-1246">Invece, le classi derivate non astratte sono necessarie per fornire la propria implementazione per le funzioni di accesso eseguendo l'override dell'evento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1246">Instead, non-abstract derived classes are required to provide their own implementation for the accessors by overriding the event.</span></span> <span data-ttu-id="5bc16-1247">Poiché una dichiarazione di evento astratto non fornisce alcuna implementazione effettiva, non può fornire delimitato da parentesi graffa *event_accessor_declarations*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1247">Because an abstract event declaration provides no actual implementation, it cannot provide brace-delimited *event_accessor_declarations*.</span></span>

<span data-ttu-id="5bc16-1248">Una dichiarazione di evento che include sia la `abstract` e `override` modificatori specifica che l'evento è di tipo abstract ed esegue l'override di un evento di base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1248">An event declaration that includes both the `abstract` and `override` modifiers specifies that the event is abstract and overrides a base event.</span></span> <span data-ttu-id="5bc16-1249">Le funzioni di accesso di tale evento inoltre sono astratte.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1249">The accessors of such an event are also abstract.</span></span>

<span data-ttu-id="5bc16-1250">Le dichiarazioni di eventi astratto sono consentite solo nelle classi astratte ([classi astratte](classes.md#abstract-classes)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1250">Abstract event declarations are only permitted in abstract classes ([Abstract classes](classes.md#abstract-classes)).</span></span>

<span data-ttu-id="5bc16-1251">Le funzioni di accesso di un evento virtuale ereditato può essere sottoposto a override in una classe derivata includendo una dichiarazione di evento che specifica un `override` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1251">The accessors of an inherited virtual event can be overridden in a derived class by including an event declaration that specifies an `override` modifier.</span></span> <span data-ttu-id="5bc16-1252">Questo è noto come un ***eseguendo l'override di dichiarazione di evento***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1252">This is known as an ***overriding event declaration***.</span></span> <span data-ttu-id="5bc16-1253">Una dichiarazione di evento override non dichiara un nuovo evento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1253">An overriding event declaration does not declare a new event.</span></span> <span data-ttu-id="5bc16-1254">Al contrario, semplicemente specializzata le implementazioni delle funzioni di accesso di un evento virtuale esistente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1254">Instead, it simply specializes the implementations of the accessors of an existing virtual event.</span></span>

<span data-ttu-id="5bc16-1255">Esegue l'override di una dichiarazione di evento è necessario specificare i modificatori di accessibilità stesso esatto, tipo e nome dell'evento sottoposto a override.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1255">An overriding event declaration must specify the exact same accessibility modifiers, type, and name as the overridden event.</span></span>

<span data-ttu-id="5bc16-1256">Può includere una dichiarazione di evento esegue l'override di `sealed` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1256">An overriding event declaration may include the `sealed` modifier.</span></span> <span data-ttu-id="5bc16-1257">Questo modificatore evita una classe derivata da ulteriormente override dell'evento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1257">Use of this modifier prevents a derived class from further overriding the event.</span></span> <span data-ttu-id="5bc16-1258">Le funzioni di accesso di un evento sealed inoltre sono sealed.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1258">The accessors of a sealed event are also sealed.</span></span>

<span data-ttu-id="5bc16-1259">Si tratta di un errore in fase di compilazione per una dichiarazione di eventi esegue l'override includere un `new` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1259">It is a compile-time error for an overriding event declaration to include a `new` modifier.</span></span>

<span data-ttu-id="5bc16-1260">Ad eccezione delle differenze nella dichiarazione e la chiamata di sintassi, override virtuale, sealed e astratte delle funzioni di accesso si comportano esattamente come virtual, sealed, override e metodi astratti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1260">Except for differences in declaration and invocation syntax, virtual, sealed, override, and abstract accessors behave exactly like virtual, sealed, override and abstract methods.</span></span> <span data-ttu-id="5bc16-1261">In particolare, le regole descritte [metodi virtuali](classes.md#virtual-methods), [metodi di Override](classes.md#override-methods), [Sealed i metodi](classes.md#sealed-methods), e [metodi astratti](classes.md#abstract-methods) applicare come se le funzioni di accesso sono stati i metodi di un modulo corrispondente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1261">Specifically, the rules described in [Virtual methods](classes.md#virtual-methods), [Override methods](classes.md#override-methods), [Sealed methods](classes.md#sealed-methods), and [Abstract methods](classes.md#abstract-methods) apply as if accessors were methods of a corresponding form.</span></span> <span data-ttu-id="5bc16-1262">Ogni funzione di accesso corrisponde a un metodo con un singolo parametro di valore del tipo di evento, un `void` il tipo restituito e gli stessi modificatori che l'evento che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1262">Each accessor corresponds to a method with a single value parameter of the event type, a `void` return type, and the same modifiers as the containing event.</span></span>

## <a name="indexers"></a><span data-ttu-id="5bc16-1263">Indicizzatori</span><span class="sxs-lookup"><span data-stu-id="5bc16-1263">Indexers</span></span>

<span data-ttu-id="5bc16-1264">Un' ***indicizzatore*** è un membro che consente a un oggetto da indicizzare esattamente come una matrice.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1264">An ***indexer*** is a member that enables an object to be indexed in the same way as an array.</span></span> <span data-ttu-id="5bc16-1265">Gli indicizzatori vengano dichiarati usando *indexer_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1265">Indexers are declared using *indexer_declaration*s:</span></span>

```antlr
indexer_declaration
    : attributes? indexer_modifier* indexer_declarator indexer_body
    ;

indexer_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | indexer_modifier_unsafe
    ;

indexer_declarator
    : type 'this' '[' formal_parameter_list ']'
    | type interface_type '.' 'this' '[' formal_parameter_list ']'
    ;

indexer_body
    : '{' accessor_declarations '}' 
    | '=>' expression ';'
    ;
```

<span data-ttu-id="5bc16-1266">Un' *indexer_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso ](classes.md#access-modifiers)), il `new` ([il nuovo modificatore](classes.md#the-new-modifier)), `virtual` ([metodi virtuali](classes.md#virtual-methods)), `override` ([l'Override di metodi](classes.md#override-methods) ), `sealed` ([Sealed i metodi](classes.md#sealed-methods)), `abstract` ([metodi astratti](classes.md#abstract-methods)), e `extern` ([metodi esterni](classes.md#external-methods)) modificatori.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1266">An *indexer_declaration* may include a set of *attributes* ([Attributes](attributes.md)) and a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), the `new` ([The new modifier](classes.md#the-new-modifier)), `virtual` ([Virtual methods](classes.md#virtual-methods)), `override` ([Override methods](classes.md#override-methods)), `sealed` ([Sealed methods](classes.md#sealed-methods)), `abstract` ([Abstract methods](classes.md#abstract-methods)), and `extern` ([External methods](classes.md#external-methods)) modifiers.</span></span>

<span data-ttu-id="5bc16-1267">Le dichiarazioni dell'indicizzatore sono soggetti alle stesse regole come le dichiarazioni di metodo ([metodi](classes.md#methods)) in relazione le combinazioni valide di modificatori, con l'unica eccezione è che il modificatore static non è consentito in una dichiarazione di indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1267">Indexer declarations are subject to the same rules as method declarations ([Methods](classes.md#methods)) with regard to valid combinations of modifiers, with the one exception being that the static modifier is not permitted on an indexer declaration.</span></span>

<span data-ttu-id="5bc16-1268">I modificatori `virtual`, `override`, e `abstract` si escludono a vicenda, ad eccezione in un caso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1268">The modifiers `virtual`, `override`, and `abstract` are mutually exclusive except in one case.</span></span> <span data-ttu-id="5bc16-1269">Il `abstract` e `override` modificatori possono essere utilizzati insieme, in modo che un indicizzatore astratto può eseguire l'override di un quello virtuale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1269">The `abstract` and `override` modifiers may be used together so that an abstract indexer can override a virtual one.</span></span>

<span data-ttu-id="5bc16-1270">Il *tipo* di un indicizzatore dichiarazione specifica il tipo di elemento dell'indicizzatore introdotta dalla dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1270">The *type* of an indexer declaration specifies the element type of the indexer introduced by the declaration.</span></span> <span data-ttu-id="5bc16-1271">A meno che l'indicizzatore è un'implementazione di membro di interfaccia esplicita, il *tipo* è seguito dalla parola chiave `this`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1271">Unless the indexer is an explicit interface member implementation, the *type* is followed by the keyword `this`.</span></span> <span data-ttu-id="5bc16-1272">Per un'implementazione di membro di interfaccia esplicita, il *tipo* seguito da un *interface_type*, un "`.`" e la parola chiave `this`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1272">For an explicit interface member implementation, the *type* is followed by an *interface_type*, a "`.`", and the keyword `this`.</span></span> <span data-ttu-id="5bc16-1273">A differenza di altri membri, gli indicizzatori non hanno nomi definiti dall'utente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1273">Unlike other members, indexers do not have user-defined names.</span></span>

<span data-ttu-id="5bc16-1274">Il *formal_parameter_list* specifica i parametri dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1274">The *formal_parameter_list* specifies the parameters of the indexer.</span></span> <span data-ttu-id="5bc16-1275">Elenco di parametri formali di un indicizzatore corrisponde a quello di un metodo ([parametri del metodo](classes.md#method-parameters)), ad eccezione del fatto che deve essere specificato almeno un parametro e che le `ref` e `out` modificatori di parametro non sono consentiti .</span><span class="sxs-lookup"><span data-stu-id="5bc16-1275">The formal parameter list of an indexer corresponds to that of a method ([Method parameters](classes.md#method-parameters)), except that at least one parameter must be specified, and that the `ref` and `out` parameter modifiers are not permitted.</span></span>

<span data-ttu-id="5bc16-1276">Il *tipo* di un indicizzatore e ognuno dei tipi a cui fa riferimento il *formal_parameter_list* devono essere accessibili almeno quanto l'indicizzatore ([vincoli accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1276">The *type* of an indexer and each of the types referenced in the *formal_parameter_list* must be at least as accessible as the indexer itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="5bc16-1277">Un' *indexer_body* possono essere costituiti da un ***corpo della funzione di accesso*** o un ***corpo dell'espressione***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1277">An *indexer_body* may either consist of an ***accessor body*** or an ***expression body***.</span></span> <span data-ttu-id="5bc16-1278">In un corpo di funzione di accesso *accessor_declarations*, che deve essere racchiuso tra parentesi "`{`"e"`}`" token, dichiarare le funzioni di accesso ([funzioni di accesso](classes.md#accessors)) della proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1278">In an accessor body, *accessor_declarations*, which must be enclosed in "`{`" and "`}`" tokens, declare the accessors ([Accessors](classes.md#accessors)) of the property.</span></span> <span data-ttu-id="5bc16-1279">Le funzioni di accesso specificare le istruzioni eseguibili associate alla lettura e scrittura della proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1279">The accessors specify the executable statements associated with reading and writing the property.</span></span>

<span data-ttu-id="5bc16-1280">Un corpo di espressione costituita da "`=>`" seguita da un'espressione `E` e un punto e virgola è perfettamente equivalente al corpo dell'istruzione `{ get { return E; } }`e può pertanto essere usato solo per specificare gli indicizzatori solo Get dove è il risultato del metodo Get assegnato da un'unica espressione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1280">An expression body consisting of "`=>`" followed by an expression `E` and a semicolon is exactly equivalent to the statement body `{ get { return E; } }`, and can therefore only be used to specify getter-only indexers where the result of the getter is given by a single expression.</span></span>

<span data-ttu-id="5bc16-1281">Anche se la sintassi per l'accesso a un elemento dell'indicizzatore è uguale a quello per un elemento della matrice, un elemento dell'indicizzatore non è classificato come una variabile.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1281">Even though the syntax for accessing an indexer element is the same as that for an array element, an indexer element is not classified as a variable.</span></span> <span data-ttu-id="5bc16-1282">Non è pertanto possibile passare un elemento dell'indicizzatore come un `ref` o `out` argomento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1282">Thus, it is not possible to pass an indexer element as a `ref` or `out` argument.</span></span>

<span data-ttu-id="5bc16-1283">Elenco di parametri formali di un indicizzatore definisce la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1283">The formal parameter list of an indexer defines the signature ([Signatures and overloading](basic-concepts.md#signatures-and-overloading)) of the indexer.</span></span> <span data-ttu-id="5bc16-1284">In particolare, la firma di un indicizzatore è costituito da numero e tipi di parametri formali.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1284">Specifically, the signature of an indexer consists of the number and types of its formal parameters.</span></span> <span data-ttu-id="5bc16-1285">Il tipo di elemento e i nomi dei parametri formali non fanno parte della firma di un indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1285">The element type and names of the formal parameters are not part of an indexer's signature.</span></span>

<span data-ttu-id="5bc16-1286">La firma di un indicizzatore sia diversa dalle firme di tutti gli altri indicizzatori dichiarati nella stessa classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1286">The signature of an indexer must differ from the signatures of all other indexers declared in the same class.</span></span>

<span data-ttu-id="5bc16-1287">Gli indicizzatori e proprietà sono concettualmente molto simile, ma differiscono nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1287">Indexers and properties are very similar in concept, but differ in the following ways:</span></span>

*  <span data-ttu-id="5bc16-1288">Una proprietà è identificata dal relativo nome, mentre un indicizzatore è identificato in base alla firma.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1288">A property is identified by its name, whereas an indexer is identified by its signature.</span></span>
*  <span data-ttu-id="5bc16-1289">Accesso a una proprietà tramite un *simple_name* ([nomi semplici](expressions.md#simple-names)) o un *member_access* ([l'accesso ai membri](expressions.md#member-access)), mentre un indicizzatore elemento avviene tramite un *element_access* ([accesso all'indicizzatore](expressions.md#indexer-access)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1289">A property is accessed through a *simple_name* ([Simple names](expressions.md#simple-names)) or a *member_access* ([Member access](expressions.md#member-access)), whereas an indexer element is accessed through an *element_access* ([Indexer access](expressions.md#indexer-access)).</span></span>
*  <span data-ttu-id="5bc16-1290">Una proprietà può essere un `static` membro, mentre un indicizzatore è sempre un membro di istanza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1290">A property can be a `static` member, whereas an indexer is always an instance member.</span></span>
*  <span data-ttu-id="5bc16-1291">Oggetto `get` funzione di accesso di una proprietà corrispondente a un metodo senza parametri, mentre un `get` della funzione di accesso di un indicizzatore corrispondente a un metodo con lo stesso elenco di parametri formali dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1291">A `get` accessor of a property corresponds to a method with no parameters, whereas a `get` accessor of an indexer corresponds to a method with the same formal parameter list as the indexer.</span></span>
*  <span data-ttu-id="5bc16-1292">Oggetto `set` funzione di accesso di una proprietà corrispondente a un metodo con un singolo parametro denominato `value`, mentre un `set` della funzione di accesso di un indicizzatore corrispondente a un metodo con lo stesso elenco parametri formali dell'indicizzatore, oltre a un parametro aggiuntivo denominato `value`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1292">A `set` accessor of a property corresponds to a method with a single parameter named `value`, whereas a `set` accessor of an indexer corresponds to a method with the same formal parameter list as the indexer, plus an additional parameter named `value`.</span></span>
*  <span data-ttu-id="5bc16-1293">È un errore in fase di compilazione per una funzione di accesso dell'indicizzatore dichiarare una variabile locale con lo stesso nome come parametro di indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1293">It is a compile-time error for an indexer accessor to declare a local variable with the same name as an indexer parameter.</span></span>
*  <span data-ttu-id="5bc16-1294">In una dichiarazione di proprietà di override, si accede alla proprietà ereditata utilizzando la sintassi `base.P`, dove `P` è il nome della proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1294">In an overriding property declaration, the inherited property is accessed using the syntax `base.P`, where `P` is the property name.</span></span> <span data-ttu-id="5bc16-1295">In una dichiarazione di indicizzatore override, l'indicizzatore ereditato è possibile accedere tramite la sintassi `base[E]`, dove `E` è un elenco delimitato da virgole di espressioni.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1295">In an overriding indexer declaration, the inherited indexer is accessed using the syntax `base[E]`, where `E` is a comma separated list of expressions.</span></span>
*  <span data-ttu-id="5bc16-1296">Non è previsto di un indicizzatore"implementato automaticamente".</span><span class="sxs-lookup"><span data-stu-id="5bc16-1296">There is no concept of an "automatically implemented indexer".</span></span> <span data-ttu-id="5bc16-1297">È possibile avere un indicizzatore non astratta, non esterno con funzioni di accesso di un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1297">It is an error to have a non-abstract, non-external indexer with semicolon accessors.</span></span>

<span data-ttu-id="5bc16-1298">A parte queste differenze, tutte le regole definite [funzioni di accesso](classes.md#accessors) e [proprietà implementate automaticamente](classes.md#automatically-implemented-properties) si applicano anche alle anche per quanto riguarda le funzioni di accesso di proprietà.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1298">Aside from these differences, all rules defined in [Accessors](classes.md#accessors) and [Automatically implemented properties](classes.md#automatically-implemented-properties) apply to indexer accessors as well as to property accessors.</span></span>

<span data-ttu-id="5bc16-1299">Quando una dichiarazione di indicizzatore include un' `extern` modificatore, l'indicizzatore viene definito un ***indicizzatore esterno***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1299">When an indexer declaration includes an `extern` modifier, the indexer is said to be an ***external indexer***.</span></span> <span data-ttu-id="5bc16-1300">Poiché una dichiarazione di indicizzatore esterni non fornisce alcuna implementazione effettiva, ognuno dei relativi *accessor_declarations* è costituito da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1300">Because an external indexer declaration provides no actual implementation, each of its *accessor_declarations* consists of a semicolon.</span></span>

<span data-ttu-id="5bc16-1301">Nell'esempio seguente viene dichiarato un `BitArray` classe che implementa un indicizzatore per l'accesso ai singoli bit nella matrice di bit.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1301">The example below declares a `BitArray` class that implements an indexer for accessing the individual bits in the bit array.</span></span>
```csharp
using System;

class BitArray
{
    int[] bits;
    int length;

    public BitArray(int length) {
        if (length < 0) throw new ArgumentException();
        bits = new int[((length - 1) >> 5) + 1];
        this.length = length;
    }

    public int Length {
        get { return length; }
    }

    public bool this[int index] {
        get {
            if (index < 0 || index >= length) {
                throw new IndexOutOfRangeException();
            }
            return (bits[index >> 5] & 1 << index) != 0;
        }
        set {
            if (index < 0 || index >= length) {
                throw new IndexOutOfRangeException();
            }
            if (value) {
                bits[index >> 5] |= 1 << index;
            }
            else {
                bits[index >> 5] &= ~(1 << index);
            }
        }
    }
}
```

<span data-ttu-id="5bc16-1302">Un'istanza del `BitArray` classe utilizza sostanzialmente meno memoria rispetto a un oggetto corrispondente `bool[]` (poiché ogni valore della prima occupa un solo bit invece di quest'ultimo di un byte), ma consente le stesse operazioni come un `bool[]`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1302">An instance of the `BitArray` class consumes substantially less memory than a corresponding `bool[]` (since each value of the former occupies only one bit instead of the latter's one byte), but it permits the same operations as a `bool[]`.</span></span>

<span data-ttu-id="5bc16-1303">Quanto segue `CountPrimes` classe Usa un `BitArray` e l'algoritmo "crivello" per calcolare il numero di numeri primi compreso tra 1 e un determinato valore massimo:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1303">The following `CountPrimes` class uses a `BitArray` and the classical "sieve" algorithm to compute the number of primes between 1 and a given maximum:</span></span>
```csharp
class CountPrimes
{
    static int Count(int max) {
        BitArray flags = new BitArray(max + 1);
        int count = 1;
        for (int i = 2; i <= max; i++) {
            if (!flags[i]) {
                for (int j = i * 2; j <= max; j += i) flags[j] = true;
                count++;
            }
        }
        return count;
    }

    static void Main(string[] args) {
        int max = int.Parse(args[0]);
        int count = Count(max);
        Console.WriteLine("Found {0} primes between 1 and {1}", count, max);
    }
}
```

<span data-ttu-id="5bc16-1304">Si noti che la sintassi per l'accesso agli elementi del `BitArray` è esattamente uguali a quelle per un `bool[]`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1304">Note that the syntax for accessing elements of the `BitArray` is precisely the same as for a `bool[]`.</span></span>

<span data-ttu-id="5bc16-1305">Nell'esempio seguente viene illustrata una classe di griglia di 26 \* 10 con un indicizzatore con due parametri.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1305">The following example shows a 26 \* 10 grid class that has an indexer with two parameters.</span></span> <span data-ttu-id="5bc16-1306">Il primo parametro deve essere una lettera maiuscola o minuscola nell'intervallo A-Z e il secondo deve essere un numero intero compreso nell'intervallo da 0 a 9.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1306">The first parameter is required to be an upper- or lowercase letter in the range A-Z, and the second is required to be an integer in the range 0-9.</span></span>

```csharp
using System;

class Grid
{
    const int NumRows = 26;
    const int NumCols = 10;

    int[,] cells = new int[NumRows, NumCols];

    public int this[char c, int col] {
        get {
            c = Char.ToUpper(c);
            if (c < 'A' || c > 'Z') {
                throw new ArgumentException();
            }
            if (col < 0 || col >= NumCols) {
                throw new IndexOutOfRangeException();
            }
            return cells[c - 'A', col];
        }

        set {
            c = Char.ToUpper(c);
            if (c < 'A' || c > 'Z') {
                throw new ArgumentException();
            }
            if (col < 0 || col >= NumCols) {
                throw new IndexOutOfRangeException();
            }
            cells[c - 'A', col] = value;
        }
    }
}
```

### <a name="indexer-overloading"></a><span data-ttu-id="5bc16-1307">L'overload di indicizzatore</span><span class="sxs-lookup"><span data-stu-id="5bc16-1307">Indexer overloading</span></span>

<span data-ttu-id="5bc16-1308">Le regole di risoluzione dell'overload di indicizzatore sono descritte nel [inferenza del tipo](expressions.md#type-inference).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1308">The indexer overload resolution rules are described in [Type inference](expressions.md#type-inference).</span></span>

## <a name="operators"></a><span data-ttu-id="5bc16-1309">Operatori</span><span class="sxs-lookup"><span data-stu-id="5bc16-1309">Operators</span></span>

<span data-ttu-id="5bc16-1310">Un' ***operatore*** è un membro che definisce il significato di un operatore di espressione che può essere applicato a istanze della classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1310">An ***operator*** is a member that defines the meaning of an expression operator that can be applied to instances of the class.</span></span> <span data-ttu-id="5bc16-1311">Gli operatori vengono dichiarati usando *operator_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1311">Operators are declared using *operator_declaration*s:</span></span>

```antlr
operator_declaration
    : attributes? operator_modifier+ operator_declarator operator_body
    ;

operator_modifier
    : 'public'
    | 'static'
    | 'extern'
    | operator_modifier_unsafe
    ;

operator_declarator
    : unary_operator_declarator
    | binary_operator_declarator
    | conversion_operator_declarator
    ;

unary_operator_declarator
    : type 'operator' overloadable_unary_operator '(' type identifier ')'
    ;

overloadable_unary_operator
    : '+' | '-' | '!' | '~' | '++' | '--' | 'true' | 'false'
    ;

binary_operator_declarator
    : type 'operator' overloadable_binary_operator '(' type identifier ',' type identifier ')'
    ;

overloadable_binary_operator
    : '+'   | '-'   | '*'   | '/'   | '%'   | '&'   | '|'   | '^'   | '<<'
    | right_shift | '=='  | '!='  | '>'   | '<'   | '>='  | '<='
    ;

conversion_operator_declarator
    : 'implicit' 'operator' type '(' type identifier ')'
    | 'explicit' 'operator' type '(' type identifier ')'
    ;

operator_body
    : block
    | '=>' expression ';'
    | ';'
    ;
```

<span data-ttu-id="5bc16-1312">Esistono tre categorie di operatori di overload: Gli operatori unari ([operatori unari](classes.md#unary-operators)), gli operatori binari ([operatori binari](classes.md#binary-operators)) e gli operatori di conversione ([gli operatori di conversione](classes.md#conversion-operators)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1312">There are three categories of overloadable operators: Unary operators ([Unary operators](classes.md#unary-operators)), binary operators ([Binary operators](classes.md#binary-operators)), and conversion operators ([Conversion operators](classes.md#conversion-operators)).</span></span>

<span data-ttu-id="5bc16-1313">Il *operator_body* entrambi un punto e virgola, una ***corpo dell'istruzione*** o un ***corpo dell'espressione***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1313">The *operator_body* is either a semicolon, a ***statement body*** or an ***expression body***.</span></span> <span data-ttu-id="5bc16-1314">È costituito da un corpo dell'istruzione di un *blocco*, che consente di specificare le istruzioni da eseguire quando viene richiamato l'operatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1314">A statement body consists of a *block*, which specifies the statements to execute when the operator is invoked.</span></span> <span data-ttu-id="5bc16-1315">Il *block* devono essere conformi alle regole per la restituzione di valore metodi descritti in [corpo del metodo](classes.md#method-body).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1315">The *block* must conform to the rules for value-returning methods described in [Method body](classes.md#method-body).</span></span> <span data-ttu-id="5bc16-1316">Costituito da un corpo di espressione `=>` seguita da un'espressione e un punto e virgola e indica una singola espressione da eseguire quando viene richiamato l'operatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1316">An expression body consists of `=>` followed by an expression and a semicolon, and denotes a single expression to perform when the operator is invoked.</span></span>

<span data-ttu-id="5bc16-1317">Per la `extern` operatori, il *operator_body* costituito semplicemente da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1317">For `extern` operators, the *operator_body* consists simply of a semicolon.</span></span> <span data-ttu-id="5bc16-1318">Per tutti gli altri operatori, il *operator_body* è un corpo del blocco o un corpo di espressione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1318">For all other operators, the *operator_body* is either a block body or an expression body.</span></span>

<span data-ttu-id="5bc16-1319">Le regole seguenti si applicano a tutte le dichiarazioni di operatore:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1319">The following rules apply to all operator declarations:</span></span>

*  <span data-ttu-id="5bc16-1320">Una dichiarazione dell'operatore deve includere sia una `public` e un `static` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1320">An operator declaration must include both a `public` and a `static` modifier.</span></span>
*  <span data-ttu-id="5bc16-1321">I parametri di un operatore devono essere parametri di valore ([parametri del valore](variables.md#value-parameters)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1321">The parameter(s) of an operator must be value parameters ([Value parameters](variables.md#value-parameters)).</span></span> <span data-ttu-id="5bc16-1322">Tratta di un errore in fase di compilazione per una dichiarazione dell'operatore specificare `ref` o `out` parametri.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1322">It is a compile-time error for an operator declaration to specify `ref` or `out` parameters.</span></span>
*  <span data-ttu-id="5bc16-1323">La firma di un operatore ([operatori unari](classes.md#unary-operators), [operatori binari](classes.md#binary-operators), [gli operatori di conversione](classes.md#conversion-operators)) deve essere diversa dalle firme di tutti gli altri operatori dichiarati nel classe stessa.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1323">The signature of an operator ([Unary operators](classes.md#unary-operators), [Binary operators](classes.md#binary-operators), [Conversion operators](classes.md#conversion-operators)) must differ from the signatures of all other operators declared in the same class.</span></span>
*  <span data-ttu-id="5bc16-1324">Tutti i tipi di cui viene fatto riferimento nella dichiarazione di un operatore devono essere accessibili almeno quanto l'operatore stesso ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1324">All types referenced in an operator declaration must be at least as accessible as the operator itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>
*  <span data-ttu-id="5bc16-1325">È un errore per lo stesso modificatore venga visualizzato più volte nella dichiarazione di un operatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1325">It is an error for the same modifier to appear multiple times in an operator declaration.</span></span>

<span data-ttu-id="5bc16-1326">Ogni categoria di operatori impone restrizioni aggiuntive, come descritto nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1326">Each operator category imposes additional restrictions, as described in the following sections.</span></span>

<span data-ttu-id="5bc16-1327">Analogamente agli altri membri, gli operatori dichiarati in una classe base vengono ereditati dalle classi derivate.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1327">Like other members, operators declared in a base class are inherited by derived classes.</span></span> <span data-ttu-id="5bc16-1328">Poiché le dichiarazioni di operatore richiedono sempre la classe o struct in cui l'operatore viene dichiarato a far parte della firma dell'operatore, non è possibile che un operatore dichiarato in una classe derivata nascondere un operatore dichiarato in una classe di base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1328">Because operator declarations always require the class or struct in which the operator is declared to participate in the signature of the operator, it is not possible for an operator declared in a derived class to hide an operator declared in a base class.</span></span> <span data-ttu-id="5bc16-1329">Di conseguenza, il `new` modificatore non è necessaria e quindi mai consentito, in una dichiarazione dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1329">Thus, the `new` modifier is never required, and therefore never permitted, in an operator declaration.</span></span>

<span data-ttu-id="5bc16-1330">Altre informazioni sugli operatori unari e binari sono reperibili nelle [operatori](expressions.md#operators).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1330">Additional information on unary and binary operators can be found in [Operators](expressions.md#operators).</span></span>

<span data-ttu-id="5bc16-1331">Ulteriori informazioni sugli operatori di conversione sono reperibile nel [conversioni definite dall'utente](conversions.md#user-defined-conversions).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1331">Additional information on conversion operators can be found in [User-defined conversions](conversions.md#user-defined-conversions).</span></span>

### <a name="unary-operators"></a><span data-ttu-id="5bc16-1332">Operatori unari</span><span class="sxs-lookup"><span data-stu-id="5bc16-1332">Unary operators</span></span>

<span data-ttu-id="5bc16-1333">Le regole seguenti si applicano alle dichiarazioni di operatore unario, in cui `T` denota il tipo di istanza della classe o struct che contiene la dichiarazione dell'operatore:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1333">The following rules apply to unary operator declarations, where `T` denotes the instance type of the class or struct that contains the operator declaration:</span></span>

*  <span data-ttu-id="5bc16-1334">Unario `+`, `-`, `!`, o `~` operatore deve accettare un solo parametro di tipo `T` o `T?` e può restituire qualsiasi tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1334">A unary `+`, `-`, `!`, or `~` operator must take a single parameter of type `T` or `T?` and can return any type.</span></span>
*  <span data-ttu-id="5bc16-1335">Unario `++` oppure `--` operatore deve accettare un solo parametro di tipo `T` o `T?` e deve restituire un tipo stesso o un tipo derivato da esso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1335">A unary `++` or `--` operator must take a single parameter of type `T` or `T?` and must return that same type or a type derived from it.</span></span>
*  <span data-ttu-id="5bc16-1336">Unario `true` oppure `false` operatore deve accettare un solo parametro di tipo `T` oppure `T?` e deve restituire un tipo `bool`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1336">A unary `true` or `false` operator must take a single parameter of type `T` or `T?` and must return type `bool`.</span></span>

<span data-ttu-id="5bc16-1337">La firma di un operatore unario è costituita da token operatore (`+`, `-`, `!`, `~`, `++`, `--`, `true`, o `false`) e il tipo di parametro formale singolo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1337">The signature of a unary operator consists of the operator token (`+`, `-`, `!`, `~`, `++`, `--`, `true`, or `false`) and the type of the single formal parameter.</span></span> <span data-ttu-id="5bc16-1338">Il tipo restituito non fa parte della firma dell'operatore unario, né è il nome del parametro formale.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1338">The return type is not part of a unary operator's signature, nor is the name of the formal parameter.</span></span>

<span data-ttu-id="5bc16-1339">Il `true` e `false` necessario che gli operatori unari.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1339">The `true` and `false` unary operators require pair-wise declaration.</span></span> <span data-ttu-id="5bc16-1340">Se una classe che dichiara uno di questi operatori senza dichiarare anche l'altro, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1340">A compile-time error occurs if a class declares one of these operators without also declaring the other.</span></span> <span data-ttu-id="5bc16-1341">Il `true` e `false` operatori sono descritti dettagliatamente nella [definiti dall'utente di operatori logici condizionali](expressions.md#user-defined-conditional-logical-operators) e [espressioni booleane](expressions.md#boolean-expressions).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1341">The `true` and `false` operators are described further in [User-defined conditional logical operators](expressions.md#user-defined-conditional-logical-operators) and [Boolean expressions](expressions.md#boolean-expressions).</span></span>

<span data-ttu-id="5bc16-1342">L'esempio seguente illustra l'implementazione e l'utilizzo successivo di `operator ++` per una classe di vettori integer:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1342">The following example shows an implementation and subsequent usage of `operator ++` for an integer vector class:</span></span>
```csharp
public class IntVector
{
    public IntVector(int length) {...}

    public int Length {...}                 // read-only property

    public int this[int index] {...}        // read-write indexer

    public static IntVector operator ++(IntVector iv) {
        IntVector temp = new IntVector(iv.Length);
        for (int i = 0; i < iv.Length; i++)
            temp[i] = iv[i] + 1;
        return temp;
    }
}

class Test
{
    static void Main() {
        IntVector iv1 = new IntVector(4);    // vector of 4 x 0
        IntVector iv2;

        iv2 = iv1++;    // iv2 contains 4 x 0, iv1 contains 4 x 1
        iv2 = ++iv1;    // iv2 contains 4 x 2, iv1 contains 4 x 2
    }
}
```

<span data-ttu-id="5bc16-1343">Si noti come il metodo dell'operatore restituisce il valore ottenuto aggiungendo 1 all'operando, esattamente come l'incremento e decremento (operatori) ([forma suffissa di incremento e decremento (operatori)](expressions.md#postfix-increment-and-decrement-operators)) e di incremento prefisso e decremento operatori ([incremento prefisso e decremento (operatori)](expressions.md#prefix-increment-and-decrement-operators)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1343">Note how the operator method returns the value produced by adding 1 to the operand, just like the  postfix increment and decrement operators ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators)), and the prefix increment and decrement operators ([Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)).</span></span> <span data-ttu-id="5bc16-1344">A differenza di c++, questo metodo necessario non modificare il valore del relativo operando direttamente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1344">Unlike in C++, this method need not modify the value of its operand directly.</span></span> <span data-ttu-id="5bc16-1345">In effetti, la modifica del valore dell'operando violerebbe la semantica standard di operatore di incremento suffisso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1345">In fact, modifying the operand value would violate the standard semantics of the postfix increment operator.</span></span>

### <a name="binary-operators"></a><span data-ttu-id="5bc16-1346">Operatori binari</span><span class="sxs-lookup"><span data-stu-id="5bc16-1346">Binary operators</span></span>

<span data-ttu-id="5bc16-1347">Le regole seguenti si applicano alle dichiarazioni di operatore binario, in cui `T` denota il tipo di istanza della classe o struct che contiene la dichiarazione dell'operatore:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1347">The following rules apply to binary operator declarations, where `T` denotes the instance type of the class or struct that contains the operator declaration:</span></span>

*  <span data-ttu-id="5bc16-1348">Un operatore di spostamento non binario deve accettare due parametri, almeno uno dei quali è necessario digitare `T` o `T?`e può restituire qualsiasi tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1348">A binary non-shift operator must take two parameters, at least one of which must have type `T` or `T?`, and can return any type.</span></span>
*  <span data-ttu-id="5bc16-1349">Un file binario `<<` oppure `>>` operatore deve accettare due parametri, il primo dei quali deve avere tipo `T` oppure `T?` e il secondo dei quali deve avere tipo `int` o `int?`e può restituire qualsiasi tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1349">A binary `<<` or `>>` operator must take two parameters, the first of which must have type `T` or `T?` and the second of which must have type `int` or `int?`, and can return any type.</span></span>

<span data-ttu-id="5bc16-1350">La firma di un operatore binario è costituita da token operatore (`+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `<<`, `>>`, `==`, `!=`, `>`, `<`, `>=`, o `<=`) e i tipi dei due parametri formali.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1350">The signature of a binary operator consists of the operator token (`+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `<<`, `>>`, `==`, `!=`, `>`, `<`, `>=`, or `<=`) and the types of the two formal parameters.</span></span> <span data-ttu-id="5bc16-1351">Il tipo restituito e i nomi dei parametri formali non fanno parte della firma di un operatore binario.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1351">The return type and the names of the formal parameters are not part of a binary operator's signature.</span></span>

<span data-ttu-id="5bc16-1352">Alcuni operatori binari devono essere dichiarati in coppia.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1352">Certain binary operators require pair-wise declaration.</span></span> <span data-ttu-id="5bc16-1353">Per ogni dichiarazione degli operatori di una coppia, deve essere una dichiarazione dell'operatore della coppia corrispondente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1353">For every declaration of either operator of a pair, there must be a matching declaration of the other operator of the pair.</span></span> <span data-ttu-id="5bc16-1354">Due dichiarazioni di operatore corrispondenti quando hanno lo stesso tipo restituito e lo stesso tipo per ogni parametro.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1354">Two operator declarations match when they have the same return type and the same type for each parameter.</span></span> <span data-ttu-id="5bc16-1355">Gli operatori seguenti è necessario che:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1355">The following operators require pair-wise declaration:</span></span>

*  <span data-ttu-id="5bc16-1356">`operator ==` e `operator !=`</span><span class="sxs-lookup"><span data-stu-id="5bc16-1356">`operator ==` and `operator !=`</span></span>
*  <span data-ttu-id="5bc16-1357">`operator >` e `operator <`</span><span class="sxs-lookup"><span data-stu-id="5bc16-1357">`operator >` and `operator <`</span></span>
*  <span data-ttu-id="5bc16-1358">`operator >=` e `operator <=`</span><span class="sxs-lookup"><span data-stu-id="5bc16-1358">`operator >=` and `operator <=`</span></span>

### <a name="conversion-operators"></a><span data-ttu-id="5bc16-1359">Operatori di conversione</span><span class="sxs-lookup"><span data-stu-id="5bc16-1359">Conversion operators</span></span>

<span data-ttu-id="5bc16-1360">Una dichiarazione di operatore di conversione introduce un ***conversione definita dall'utente*** ([conversioni definite dall'utente](conversions.md#user-defined-conversions)) che integra le conversioni implicite ed esplicite predefinite.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1360">A conversion operator declaration introduces a ***user-defined conversion*** ([User-defined conversions](conversions.md#user-defined-conversions)) which augments the pre-defined implicit and explicit conversions.</span></span>

<span data-ttu-id="5bc16-1361">Una dichiarazione di operatore di conversione che include il `implicit` (parola chiave) introduce una conversione implicita definito dall'utente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1361">A conversion operator declaration that includes the `implicit` keyword introduces a user-defined implicit conversion.</span></span> <span data-ttu-id="5bc16-1362">Le conversioni implicite possono verificarsi in diverse situazioni, incluse le chiamate di funzione membro, espressioni cast e le assegnazioni.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1362">Implicit conversions can occur in a variety of situations, including function member invocations, cast expressions, and assignments.</span></span> <span data-ttu-id="5bc16-1363">Questo è descritto più dettagliatamente in [conversioni implicite](conversions.md#implicit-conversions).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1363">This is described further in [Implicit conversions](conversions.md#implicit-conversions).</span></span>

<span data-ttu-id="5bc16-1364">Una dichiarazione di operatore di conversione che include il `explicit` (parola chiave) introduce una conversione esplicita definita dall'utente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1364">A conversion operator declaration that includes the `explicit` keyword introduces a user-defined explicit conversion.</span></span> <span data-ttu-id="5bc16-1365">Le conversioni esplicite possono verificarsi nelle espressioni cast e ulteriori informazioni, vedere [conversioni esplicite](conversions.md#explicit-conversions).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1365">Explicit conversions can occur in cast expressions, and are described further in [Explicit conversions](conversions.md#explicit-conversions).</span></span>

<span data-ttu-id="5bc16-1366">Un operatore di conversione converte da un tipo di origine, indicato dal tipo di parametro dell'operatore di conversione, di un tipo di destinazione indicato dal tipo restituito dell'operatore di conversione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1366">A conversion operator converts from a source type, indicated by the parameter type of the conversion operator, to a target type, indicated by the return type of the conversion operator.</span></span>

<span data-ttu-id="5bc16-1367">Per un tipo di origine specificato `S` e tipo di destinazione `T`, se `S` oppure `T` sono i tipi nullable, consentire `S0` e `T0` fanno riferimento ai tipi sottostanti, in caso contrario `S0` e `T0` sono uguale a `S` e `T` rispettivamente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1367">For a given source type `S` and target type `T`, if `S` or `T` are nullable types, let `S0` and `T0` refer to their underlying types, otherwise `S0` and `T0` are equal to `S` and `T` respectively.</span></span> <span data-ttu-id="5bc16-1368">Una classe o struct è possibile dichiarare una conversione da un tipo di origine `S` a un tipo di destinazione `T` solo se tutte le operazioni seguenti sono vere:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1368">A class or struct is permitted to declare a conversion from a source type `S` to a target type `T` only if all of the following are true:</span></span>

*  <span data-ttu-id="5bc16-1369">`S0` e `T0` sono tipi diversi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1369">`S0` and `T0` are different types.</span></span>
*  <span data-ttu-id="5bc16-1370">Sia `S0` o `T0` è il tipo di classe o struct in cui viene eseguita la dichiarazione di operatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1370">Either `S0` or `T0` is the class or struct type in which the operator declaration takes place.</span></span>
*  <span data-ttu-id="5bc16-1371">Né `S0` né `T0` è un *interface_type*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1371">Neither `S0` nor `T0` is an *interface_type*.</span></span>
*  <span data-ttu-id="5bc16-1372">Escludendo le conversioni definite dall'utente, che non esiste una conversione da `S` al `T` o da `T` a `S`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1372">Excluding user-defined conversions, a conversion does not exist from `S` to `T` or from `T` to `S`.</span></span>

<span data-ttu-id="5bc16-1373">Ai fini di queste regole, qualsiasi tipo di parametri associati `S` o `T` sono considerati tipi univoci non hanno alcuna relazione di ereditarietà con gli altri tipi e i vincoli per il tipo di tali parametri vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1373">For the purposes of these rules, any type parameters associated with `S` or `T` are considered to be unique types that have no inheritance relationship with other types, and any constraints on those type parameters are ignored.</span></span>

<span data-ttu-id="5bc16-1374">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1374">In the example</span></span>
```csharp
class C<T> {...}

class D<T>: C<T>
{
    public static implicit operator C<int>(D<T> value) {...}      // Ok
    public static implicit operator C<string>(D<T> value) {...}   // Ok
    public static implicit operator C<T>(D<T> value) {...}        // Error
}
```
<span data-ttu-id="5bc16-1375">il primo due operatore le dichiarazioni sono consentite perché, per quanto riguarda [indicizzatori](classes.md#indexers).3, `T` e `int` e `string` rispettivamente sono considerati tipi univoci con alcuna relazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1375">the first two operator declarations are permitted because, for the purposes of [Indexers](classes.md#indexers).3, `T` and `int` and `string` respectively are considered unique types with no relationship.</span></span> <span data-ttu-id="5bc16-1376">Tuttavia, il terzo operatore è un errore in quanto `C<T>` è la classe di base di `D<T>`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1376">However, the third operator is an error because `C<T>` is the base class of `D<T>`.</span></span>

<span data-ttu-id="5bc16-1377">In base alla regola secondo che ne consegue che è necessario convertire un operatore di conversione da o verso il tipo di classe o struct in cui l'operatore viene dichiarato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1377">From the second rule it follows that a conversion operator must convert either to or from the class or struct type in which the operator is declared.</span></span> <span data-ttu-id="5bc16-1378">Ad esempio, è possibile per un tipo di classe o struct `C` per definire una conversione da `C` per `int` e dal `int` a `C`, ma non da `int` per `bool`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1378">For example, it is possible for a class or struct type `C` to define a conversion from `C` to `int` and from `int` to `C`, but not from `int` to `bool`.</span></span>

<span data-ttu-id="5bc16-1379">Non è possibile ridefinire direttamente una conversione predefinita.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1379">It is not possible to directly redefine a pre-defined conversion.</span></span> <span data-ttu-id="5bc16-1380">Di conseguenza, gli operatori di conversione non sono consentiti per conversione da o verso `object` poiché le conversioni implicite ed esplicite già presenti tra `object` e tutti gli altri tipi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1380">Thus, conversion operators are not allowed to convert from or to `object` because implicit and explicit conversions already exist between `object` and all other types.</span></span> <span data-ttu-id="5bc16-1381">Analogamente, l'origine né i tipi di destinazione di una conversione possono essere un tipo di base di altra parte, poiché una conversione sarebbe già esistente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1381">Likewise, neither the source nor the target types of a conversion can be a base type of the other, since a conversion would then already exist.</span></span>

<span data-ttu-id="5bc16-1382">Tuttavia, è possibile dichiarare gli operatori nei tipi generici che, per gli argomenti di tipo specifico, specificano le conversioni per cui esistono già come le conversioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1382">However, it is possible to declare operators on generic types that, for particular type arguments, specify conversions that already exist as pre-defined conversions.</span></span> <span data-ttu-id="5bc16-1383">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1383">In the example</span></span>
```csharp
struct Convertible<T>
{
    public static implicit operator Convertible<T>(T value) {...}
    public static explicit operator T(Convertible<T> value) {...}
}
```
<span data-ttu-id="5bc16-1384">Quando digita `object` viene specificato come argomento di tipo per `T`, il secondo operatore dichiara una conversione che esiste già (implicita e pertanto anche esplicita, non esiste conversione da qualsiasi tipo al tipo `object`).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1384">when type `object` is specified as a type argument for `T`, the second operator declares a conversion that already exists (an implicit, and therefore also an explicit, conversion exists from any type to type `object`).</span></span>

<span data-ttu-id="5bc16-1385">Nei casi in cui è presente una conversione predefinita tra due tipi, le conversioni definite dall'utente tra tali tipi vengono ignorate.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1385">In cases where a pre-defined conversion exists between two types, any user-defined conversions between those types are ignored.</span></span> <span data-ttu-id="5bc16-1386">In particolare:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1386">Specifically:</span></span>

*  <span data-ttu-id="5bc16-1387">Se una conversione implicita predefinita ([conversioni implicite](conversions.md#implicit-conversions)) esistente dal tipo `S` per digitare `T`, tutte le conversioni definite dall'utente (implicite o esplicite) dal `S` a `T` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1387">If a pre-defined implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from type `S` to type `T`, all user-defined conversions (implicit or explicit) from `S` to `T` are ignored.</span></span>
*  <span data-ttu-id="5bc16-1388">Se la conversione esplicita predefinita ([conversioni esplicite](conversions.md#explicit-conversions)) esistente dal tipo `S` per digitare `T`, tutte le conversioni esplicite definite dall'utente da `S` a `T` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1388">If a pre-defined explicit conversion ([Explicit conversions](conversions.md#explicit-conversions)) exists from type `S` to type `T`, any user-defined explicit conversions from `S` to `T` are ignored.</span></span> <span data-ttu-id="5bc16-1389">Inoltre:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1389">Furthermore:</span></span>

<span data-ttu-id="5bc16-1390">Se `T` è un tipo di interfaccia, le conversioni implicite definite dall'utente dal `S` a `T` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1390">If `T` is an interface type, user-defined implicit conversions from `S` to `T` are ignored.</span></span>

<span data-ttu-id="5bc16-1391">In caso contrario, definita dall'utente conversioni implicite da' `S` a `T` vengono prese in considerazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1391">Otherwise, user-defined implicit conversions from `S` to `T` are still considered.</span></span>

<span data-ttu-id="5bc16-1392">Per tutti i tipi, ma `object`, gli operatori dichiarato dal `Convertible<T>` tipo precedente non sono in conflitto con le conversioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1392">For all types but `object`, the operators declared by the `Convertible<T>` type above do not conflict with pre-defined conversions.</span></span> <span data-ttu-id="5bc16-1393">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1393">For example:</span></span>
```csharp
void F(int i, Convertible<int> n) {
    i = n;                          // Error
    i = (int)n;                     // User-defined explicit conversion
    n = i;                          // User-defined implicit conversion
    n = (Convertible<int>)i;        // User-defined implicit conversion
}
```

<span data-ttu-id="5bc16-1394">Tuttavia, per il tipo `object`, le conversioni predefinite nascondono le conversioni definite dall'utente in tutti i casi tranne il:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1394">However, for type `object`, pre-defined conversions hide the user-defined conversions in all cases but one:</span></span>

```csharp
void F(object o, Convertible<object> n) {
    o = n;                         // Pre-defined boxing conversion
    o = (object)n;                 // Pre-defined boxing conversion
    n = o;                         // User-defined implicit conversion
    n = (Convertible<object>)o;    // Pre-defined unboxing conversion
}
```

<span data-ttu-id="5bc16-1395">Conversioni definite dall'utente non sono consentite per conversione da o verso *interface_type*s.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1395">User-defined conversions are not allowed to convert from or to *interface_type*s.</span></span> <span data-ttu-id="5bc16-1396">In particolare, questa restrizione garantisce che venga eseguita alcuna trasformazione definita dall'utente durante la conversione in un *interface_type*e che una conversione verso un *interface_type* ha esito positivo solo se l'oggetto convertito implementa effettivamente l'oggetto specificato *interface_type*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1396">In particular, this restriction ensures that no user-defined transformations occur when converting to an *interface_type*, and that a conversion to an *interface_type* succeeds only if the object being converted actually implements the specified *interface_type*.</span></span>

<span data-ttu-id="5bc16-1397">La firma di un operatore di conversione è costituita dal tipo di origine e il tipo di destinazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1397">The signature of a conversion operator consists of the source type and the target type.</span></span> <span data-ttu-id="5bc16-1398">Si noti che si tratta dell'unica forma di membro per cui il tipo restituito fa parte della firma. Il `implicit` o `explicit` classificazione di un operatore di conversione non fa parte della firma dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1398">(Note that this is the only form of member for which the return type participates in the signature.) The `implicit` or `explicit` classification of a conversion operator is not part of the operator's signature.</span></span> <span data-ttu-id="5bc16-1399">Di conseguenza, una classe o struct non possono dichiarare entrambi un `implicit` e un `explicit` operatore di conversione con gli stessi tipi di origine e di destinazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1399">Thus, a class or struct cannot declare both an `implicit` and an `explicit` conversion operator with the same source and target types.</span></span>

<span data-ttu-id="5bc16-1400">In generale, le conversioni implicite definite dall'utente devono essere progettate per non generare eccezioni e perdere informazioni.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1400">In general, user-defined implicit conversions should be designed to never throw exceptions and never lose information.</span></span> <span data-ttu-id="5bc16-1401">Se una conversione definita dall'utente può dar luogo a eccezioni (ad esempio, perché l'argomento di origine non è compreso nell'intervallo) o perdita di informazioni (ad esempio, ignorando i bit più significativi) e quindi che la conversione deve essere definita come una conversione esplicita.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1401">If a user-defined conversion can give rise to exceptions (for example, because the source argument is out of range) or loss of information (such as discarding high-order bits), then that conversion should be defined as an explicit conversion.</span></span>

<span data-ttu-id="5bc16-1402">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1402">In the example</span></span>
```csharp
using System;

public struct Digit
{
    byte value;

    public Digit(byte value) {
        if (value < 0 || value > 9) throw new ArgumentException();
        this.value = value;
    }

    public static implicit operator byte(Digit d) {
        return d.value;
    }

    public static explicit operator Digit(byte b) {
        return new Digit(b);
    }
}
```
<span data-ttu-id="5bc16-1403">la conversione da `Digit` al `byte` è implicito perché mai genera eccezioni o perde informazioni, ma la conversione da `byte` al `Digit` esplicita poiché `Digit` possono rappresentare solo un sottoinsieme dei possibili i valori di un `byte`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1403">the conversion from `Digit` to `byte` is implicit because it never throws exceptions or loses information, but the conversion from `byte` to `Digit` is explicit since `Digit` can only represent a subset of the possible values of a `byte`.</span></span>

## <a name="instance-constructors"></a><span data-ttu-id="5bc16-1404">Costruttori di istanza</span><span class="sxs-lookup"><span data-stu-id="5bc16-1404">Instance constructors</span></span>

<span data-ttu-id="5bc16-1405">Un ***costruttore di istanza*** è un membro che implementa le azioni necessarie per inizializzare un'istanza di una classe,</span><span class="sxs-lookup"><span data-stu-id="5bc16-1405">An ***instance constructor*** is a member that implements the actions required to initialize an instance of a class.</span></span> <span data-ttu-id="5bc16-1406">Costruttori di istanza vengono dichiarati utilizzando *constructor_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1406">Instance constructors are declared using *constructor_declaration*s:</span></span>

```antlr
constructor_declaration
    : attributes? constructor_modifier* constructor_declarator constructor_body
    ;

constructor_modifier
    : 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'extern'
    | constructor_modifier_unsafe
    ;

constructor_declarator
    : identifier '(' formal_parameter_list? ')' constructor_initializer?
    ;

constructor_initializer
    : ':' 'base' '(' argument_list? ')'
    | ':' 'this' '(' argument_list? ')'
    ;

constructor_body
    : block
    | ';'
    ;
```

<span data-ttu-id="5bc16-1407">Oggetto *constructor_declaration* può includere un set di *attributi* ([attributi](attributes.md)), una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso ](classes.md#access-modifiers)) e un' `extern` ([metodi esterni](classes.md#external-methods)) modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1407">A *constructor_declaration* may include a set of *attributes* ([Attributes](attributes.md)), a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), and an `extern` ([External methods](classes.md#external-methods)) modifier.</span></span> <span data-ttu-id="5bc16-1408">Una dichiarazione di costruttore non è consentita includere più volte lo stesso modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1408">A constructor declaration is not permitted to include the same modifier multiple times.</span></span>

<span data-ttu-id="5bc16-1409">Il *identifier* di un *constructor_declarator* deve denominare la classe in cui viene dichiarato il costruttore di istanza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1409">The *identifier* of a *constructor_declarator* must name the class in which the instance constructor is declared.</span></span> <span data-ttu-id="5bc16-1410">Se viene specificato un altro nome, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1410">If any other name is specified, a compile-time error occurs.</span></span>

<span data-ttu-id="5bc16-1411">L'opzione facoltativa *formal_parameter_list* di un'istanza del costruttore è soggetto alle stesse regole di *formal_parameter_list* di un metodo ([metodi](classes.md#methods)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1411">The optional *formal_parameter_list* of an instance constructor is subject to the same rules as the *formal_parameter_list* of a method ([Methods](classes.md#methods)).</span></span> <span data-ttu-id="5bc16-1412">Elenco di parametri formali definisce la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) di un costruttore di istanza e determina in base al quale il processo di risoluzione dell'overload ([inferenza del tipo](expressions.md#type-inference)) consente di selezionare un particolare costruttore di istanza in una chiamata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1412">The formal parameter list defines the signature ([Signatures and overloading](basic-concepts.md#signatures-and-overloading)) of an instance constructor and governs the process whereby overload resolution ([Type inference](expressions.md#type-inference)) selects a particular instance constructor in an invocation.</span></span>

<span data-ttu-id="5bc16-1413">Ognuno dei tipi a cui fa riferimento il *formal_parameter_list* di un'istanza del costruttore deve essere accessibile almeno quanto il costruttore stesso ([vincoli accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1413">Each of the types referenced in the *formal_parameter_list* of an instance constructor must be at least as accessible as the constructor itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="5bc16-1414">L'opzione facoltativa *constructor_initializer* specifica un altro costruttore di istanza per richiamare prima di eseguire le istruzioni fornite nella *constructor_body* di questo costruttore di istanza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1414">The optional *constructor_initializer* specifies another instance constructor to invoke before executing the statements given in the *constructor_body* of this instance constructor.</span></span> <span data-ttu-id="5bc16-1415">Questo è descritto più dettagliatamente in [inizializzatori del costruttore](classes.md#constructor-initializers).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1415">This is described further in [Constructor initializers](classes.md#constructor-initializers).</span></span>

<span data-ttu-id="5bc16-1416">Quando una dichiarazione di costruttore include un `extern` modificatore, il costruttore viene definito un ***costruttore esterno***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1416">When a constructor declaration includes an `extern` modifier, the constructor is said to be an ***external constructor***.</span></span> <span data-ttu-id="5bc16-1417">Poiché una dichiarazione di costruttore esterni non fornisce alcuna implementazione effettiva, relativi *constructor_body* è costituito da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1417">Because an external constructor declaration provides no actual implementation, its *constructor_body* consists of a semicolon.</span></span> <span data-ttu-id="5bc16-1418">Per tutti gli altri costruttori, il *constructor_body* costituito da un *blocco* che consente di specificare le istruzioni per inizializzare una nuova istanza della classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1418">For all other constructors, the *constructor_body* consists of a *block* which specifies the statements to initialize a new instance of the class.</span></span> <span data-ttu-id="5bc16-1419">Questo corrisponde esattamente al *block* di un metodo di istanza con un `void` tipo restituito ([corpo del metodo](classes.md#method-body)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1419">This corresponds exactly to the *block* of an instance method with a `void` return type ([Method body](classes.md#method-body)).</span></span>

<span data-ttu-id="5bc16-1420">Costruttori di istanza non vengono ereditati.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1420">Instance constructors are not inherited.</span></span> <span data-ttu-id="5bc16-1421">Di conseguenza, una classe non dispone di alcun costruttore di istanza diversi da quelli effettivamente dichiarati nella classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1421">Thus, a class has no instance constructors other than those actually declared in the class.</span></span> <span data-ttu-id="5bc16-1422">Se una classe non contiene alcuna dichiarazione di costruttore di istanza, viene fornito automaticamente un costruttore di istanza predefinito ([costruttori predefiniti](classes.md#default-constructors)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1422">If a class contains no instance constructor declarations, a default instance constructor is automatically provided ([Default constructors](classes.md#default-constructors)).</span></span>

<span data-ttu-id="5bc16-1423">Costruttori di istanza vengono richiamati dal *object_creation_expression*s ([espressioni per la creazione dell'oggetto](expressions.md#object-creation-expressions)) e tramite *constructor_initializer*s.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1423">Instance constructors are invoked by *object_creation_expression*s ([Object creation expressions](expressions.md#object-creation-expressions)) and through *constructor_initializer*s.</span></span>

### <a name="constructor-initializers"></a><span data-ttu-id="5bc16-1424">Inizializzatori del costruttore</span><span class="sxs-lookup"><span data-stu-id="5bc16-1424">Constructor initializers</span></span>

<span data-ttu-id="5bc16-1425">Tutti i costruttori di istanza (ad eccezione di quelle per la classe `object`) includono in modo implicito una chiamata di un altro costruttore di istanza immediatamente prima di *constructor_body*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1425">All instance constructors (except those for class `object`) implicitly include an invocation of another instance constructor immediately before the *constructor_body*.</span></span> <span data-ttu-id="5bc16-1426">Il costruttore da richiamare in modo implicito è determinato dal *constructor_initializer*:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1426">The constructor to implicitly invoke is determined by the *constructor_initializer*:</span></span>

*  <span data-ttu-id="5bc16-1427">Un inizializzatore del costruttore di istanza nel formato `base(argument_list)` o `base()` provoca un costruttore di istanza dalla classe di base diretta da richiamare.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1427">An instance constructor initializer of the form `base(argument_list)` or `base()` causes an instance constructor from the direct base class to be invoked.</span></span> <span data-ttu-id="5bc16-1428">Tale costruttore viene selezionato mediante *argument_list* se presente e le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1428">That constructor is selected using *argument_list* if present and the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="5bc16-1429">Il set di costruttori di istanza è costituito da tutti i costruttori di istanza accessibile contenuti nella classe di base diretta o il costruttore predefinito ([costruttori predefiniti](classes.md#default-constructors)), se viene dichiarato alcun costruttore di istanza di classe di base diretta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1429">The set of candidate instance constructors consists of all accessible instance constructors contained in the direct base class, or the default constructor ([Default constructors](classes.md#default-constructors)), if no instance constructors are declared in the direct base class.</span></span> <span data-ttu-id="5bc16-1430">Se questo set è vuoto o se non è possibile identificare un costruttore di istanza migliore, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1430">If this set is empty, or if a single best instance constructor cannot be identified, a compile-time error occurs.</span></span>
*  <span data-ttu-id="5bc16-1431">Un inizializzatore del costruttore di istanza nel formato `this(argument-list)` o `this()` provoca un costruttore di istanza della classe stessa da richiamare.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1431">An instance constructor initializer of the form `this(argument-list)` or `this()` causes an instance constructor from the class itself to be invoked.</span></span> <span data-ttu-id="5bc16-1432">Il costruttore viene selezionato mediante *argument_list* se presente e le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1432">The constructor is selected using *argument_list* if present and the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="5bc16-1433">Il set di costruttori di istanza è costituito da tutti i costruttori di istanza accessibile dichiarati nella classe stessa.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1433">The set of candidate instance constructors consists of all accessible instance constructors declared in the class itself.</span></span> <span data-ttu-id="5bc16-1434">Se questo set è vuoto o se non è possibile identificare un costruttore di istanza migliore, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1434">If this set is empty, or if a single best instance constructor cannot be identified, a compile-time error occurs.</span></span> <span data-ttu-id="5bc16-1435">Se una dichiarazione di costruttore di istanza include un inizializzatore del costruttore che richiama il costruttore stesso, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1435">If an instance constructor declaration includes a constructor initializer that invokes the constructor itself, a compile-time error occurs.</span></span>

<span data-ttu-id="5bc16-1436">Se un costruttore di istanza non include alcun inizializzatore di costruttore, un inizializzatore del costruttore del form `base()` viene fornito in modo implicito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1436">If an instance constructor has no constructor initializer, a constructor initializer of the form `base()` is implicitly provided.</span></span> <span data-ttu-id="5bc16-1437">Di conseguenza, una dichiarazione di costruttore di istanza nel formato</span><span class="sxs-lookup"><span data-stu-id="5bc16-1437">Thus, an instance constructor declaration of the form</span></span>
```csharp
C(...) {...}
```
<span data-ttu-id="5bc16-1438">è perfettamente equivalente alla</span><span class="sxs-lookup"><span data-stu-id="5bc16-1438">is exactly equivalent to</span></span>
```csharp
C(...): base() {...}
```

<span data-ttu-id="5bc16-1439">L'ambito dei parametri, dati per il *formal_parameter_list* di un costruttore di istanza dichiarazione include l'inizializzatore del costruttore di tale dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1439">The scope of the parameters given by the *formal_parameter_list* of an instance constructor declaration includes the constructor initializer of that declaration.</span></span> <span data-ttu-id="5bc16-1440">Di conseguenza, un inizializzatore del costruttore è consentito accedere ai parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1440">Thus, a constructor initializer is permitted to access the parameters of the constructor.</span></span> <span data-ttu-id="5bc16-1441">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1441">For example:</span></span>
```csharp
class A
{
    public A(int x, int y) {}
}

class B: A
{
    public B(int x, int y): base(x + y, x - y) {}
}
```

<span data-ttu-id="5bc16-1442">Un inizializzatore di costruttore di istanza non è possibile accedere all'istanza da creare.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1442">An instance constructor initializer cannot access the instance being created.</span></span> <span data-ttu-id="5bc16-1443">È pertanto un errore in fase di compilazione per fare riferimento a `this` in un'espressione dell'inizializzatore del costruttore dell'argomento come perché è un errore in fase di compilazione per un'espressione dell'argomento fare riferimento a un membro di istanza tramite un *simple_name*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1443">Therefore it is a compile-time error to reference `this` in an argument expression of the constructor initializer, as is it a compile-time error for an argument expression to reference any instance member through a *simple_name*.</span></span>

### <a name="instance-variable-initializers"></a><span data-ttu-id="5bc16-1444">Inizializzatori di variabili di istanza</span><span class="sxs-lookup"><span data-stu-id="5bc16-1444">Instance variable initializers</span></span>

<span data-ttu-id="5bc16-1445">Quando un costruttore di istanza non include alcun inizializzatore di costruttore o è in un inizializzatore del costruttore del form `base(...)`, tale costruttore esegue in modo implicito le inizializzazioni specificate per il *variable_initializer*s di i campi di istanza dichiarato nella relativa classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1445">When an instance constructor has no constructor initializer, or it has a constructor initializer of the form `base(...)`, that constructor implicitly performs the initializations specified by the *variable_initializer*s of the instance fields declared in its class.</span></span> <span data-ttu-id="5bc16-1446">Ciò corrisponde a una sequenza di assegnazioni che vengono eseguite immediatamente al momento dell'accesso al costruttore e prima della chiamata del costruttore diretto della classe base implicita.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1446">This corresponds to a sequence of assignments that are executed immediately upon entry to the constructor and before the implicit invocation of the direct base class constructor.</span></span> <span data-ttu-id="5bc16-1447">Inizializzatori vengono eseguiti nell'ordine testuale in cui appaiono nella dichiarazione di classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1447">The variable initializers are executed in the textual order in which they appear in the class declaration.</span></span>

### <a name="constructor-execution"></a><span data-ttu-id="5bc16-1448">Esecuzione del costruttore</span><span class="sxs-lookup"><span data-stu-id="5bc16-1448">Constructor execution</span></span>

<span data-ttu-id="5bc16-1449">Gli inizializzatori di variabili vengono trasformati in istruzioni di assegnazione e queste istruzioni di assegnazione vengono eseguite prima della chiamata del costruttore di istanza della classe base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1449">Variable initializers are transformed into assignment statements, and these assignment statements are executed before the invocation of the base class instance constructor.</span></span> <span data-ttu-id="5bc16-1450">Questo ordinamento garantisce che tutti i campi di istanza vengono inizializzati dai relativi inizializzatori prima dell'esecuzione di qualsiasi istruzione che ha accesso a tale istanza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1450">This ordering ensures that all instance fields are initialized by their variable initializers before any statements that have access to that instance are executed.</span></span>

<span data-ttu-id="5bc16-1451">Dato l'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1451">Given the example</span></span>
```csharp
using System;

class A
{
    public A() {
        PrintFields();
    }

    public virtual void PrintFields() {}
}

class B: A
{
    int x = 1;
    int y;

    public B() {
        y = -1;
    }

    public override void PrintFields() {
        Console.WriteLine("x = {0}, y = {1}", x, y);
    }
}
```
<span data-ttu-id="5bc16-1452">Quando `new B()` viene usato per creare un'istanza di `B`, viene prodotto l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1452">when `new B()` is used to create an instance of `B`, the following output is produced:</span></span>
```
x = 1, y = 0
```

<span data-ttu-id="5bc16-1453">Il valore di `x` è 1, perché l'inizializzatore di variabile viene eseguita prima che venga richiamato il costruttore della classe base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1453">The value of `x` is 1 because the variable initializer is executed before the base class instance constructor is invoked.</span></span> <span data-ttu-id="5bc16-1454">Tuttavia, il valore di `y` è 0 (il valore predefinito di un `int`) perché l'assegnazione al `y` non viene eseguita fino al termine il costruttore di classe di base.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1454">However, the value of `y` is 0 (the default value of an `int`) because the assignment to `y` is not executed until after the base class constructor returns.</span></span>

<span data-ttu-id="5bc16-1455">È utile considerare gli inizializzatori di variabili di istanza e inizializzatori del costruttore come le istruzioni che vengono inserite automaticamente prima la *constructor_body*.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1455">It is useful to think of instance variable initializers and constructor initializers as statements that are automatically inserted before the *constructor_body*.</span></span> <span data-ttu-id="5bc16-1456">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1456">The example</span></span>
```csharp
using System;
using System.Collections;

class A
{
    int x = 1, y = -1, count;

    public A() {
        count = 0;
    }

    public A(int n) {
        count = n;
    }
}

class B: A
{
    double sqrt2 = Math.Sqrt(2.0);
    ArrayList items = new ArrayList(100);
    int max;

    public B(): this(100) {
        items.Add("default");
    }

    public B(int n): base(n - 1) {
        max = n;
    }
}
```
<span data-ttu-id="5bc16-1457">contiene gli inizializzatori di variabili diversi; contiene inoltre inizializzatori del costruttore di entrambi i formati (`base` e `this`).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1457">contains several variable initializers; it also contains constructor initializers of both forms (`base` and `this`).</span></span> <span data-ttu-id="5bc16-1458">Nell'esempio corrisponde al codice riportato di seguito, dove ogni commento indica un'istruzione inserita automaticamente (la sintassi utilizzata per le chiamate al costruttore automaticamente inserito non è valido, ma serve semplicemente a illustrare il meccanismo).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1458">The example corresponds to the code shown below, where each comment indicates an automatically inserted statement (the syntax used for the automatically inserted constructor invocations isn't valid, but merely serves to illustrate the mechanism).</span></span>

```csharp
using System.Collections;

class A
{
    int x, y, count;

    public A() {
        x = 1;                       // Variable initializer
        y = -1;                      // Variable initializer
        object();                    // Invoke object() constructor
        count = 0;
    }

    public A(int n) {
        x = 1;                       // Variable initializer
        y = -1;                      // Variable initializer
        object();                    // Invoke object() constructor
        count = n;
    }
}

class B: A
{
    double sqrt2;
    ArrayList items;
    int max;

    public B(): this(100) {
        B(100);                      // Invoke B(int) constructor
        items.Add("default");
    }

    public B(int n): base(n - 1) {
        sqrt2 = Math.Sqrt(2.0);      // Variable initializer
        items = new ArrayList(100);  // Variable initializer
        A(n - 1);                    // Invoke A(int) constructor
        max = n;
    }
}
```

### <a name="default-constructors"></a><span data-ttu-id="5bc16-1459">Costruttori predefiniti</span><span class="sxs-lookup"><span data-stu-id="5bc16-1459">Default constructors</span></span>

<span data-ttu-id="5bc16-1460">Se una classe non contiene alcuna dichiarazione di costruttore di istanza, viene fornito automaticamente un costruttore di istanza predefinito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1460">If a class contains no instance constructor declarations, a default instance constructor is automatically provided.</span></span> <span data-ttu-id="5bc16-1461">Tale costruttore predefinito richiama semplicemente il costruttore senza parametri della classe di base diretta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1461">That default constructor simply invokes the parameterless constructor of the direct base class.</span></span> <span data-ttu-id="5bc16-1462">Se la classe è astratta è protetta l'accessibilità dichiarata per il costruttore predefinito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1462">If the class is abstract then the declared accessibility for the default constructor is protected.</span></span> <span data-ttu-id="5bc16-1463">In caso contrario, l'accessibilità dichiarata per il costruttore predefinito è pubblico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1463">Otherwise, the declared accessibility for the default constructor is public.</span></span> <span data-ttu-id="5bc16-1464">Di conseguenza, il costruttore predefinito è sempre del form</span><span class="sxs-lookup"><span data-stu-id="5bc16-1464">Thus, the default constructor is always of the form</span></span>

```csharp
protected C(): base() {}
```
<span data-ttu-id="5bc16-1465">oppure</span><span class="sxs-lookup"><span data-stu-id="5bc16-1465">or</span></span>
```csharp
public C(): base() {}
```
<span data-ttu-id="5bc16-1466">in cui `C` è il nome della classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1466">where `C` is the name of the class.</span></span> <span data-ttu-id="5bc16-1467">Se la risoluzione dell'overload non riesce a determinare un candidato migliore univoco per l'inizializzatore del costruttore di classe di base verrà generato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1467">If overload resolution is unable to determine a unique best candidate for the base class constructor initializer then a compile-time error occurs.</span></span>

<span data-ttu-id="5bc16-1468">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1468">In the example</span></span>
```csharp
class Message
{
    object sender;
    string text;
}
```
<span data-ttu-id="5bc16-1469">un costruttore predefinito viene fornito perché la classe non contiene alcuna dichiarazione di costruttore di istanza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1469">a default constructor is provided because the class contains no instance constructor declarations.</span></span> <span data-ttu-id="5bc16-1470">Di conseguenza, l'esempio è esattamente equivalente a</span><span class="sxs-lookup"><span data-stu-id="5bc16-1470">Thus, the example is precisely equivalent to</span></span>
```csharp
class Message
{
    object sender;
    string text;

    public Message(): base() {}
}
```

### <a name="private-constructors"></a><span data-ttu-id="5bc16-1471">Costruttori privati</span><span class="sxs-lookup"><span data-stu-id="5bc16-1471">Private constructors</span></span>

<span data-ttu-id="5bc16-1472">Quando una classe `T` dichiara solo costruttori di istanza privata, non è possibile che per le classi all'esterno del testo di programma del `T` da cui derivare `T` o a creare direttamente istanze di `T`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1472">When a class `T` declares only private instance constructors, it is not possible for classes outside the program text of `T` to derive from `T` or to directly create instances of `T`.</span></span> <span data-ttu-id="5bc16-1473">Di conseguenza, se una classe contiene solo membri statici e non può essere creata un'istanza, l'aggiunta di un costruttore di istanza privata vuota impedirà la creazione di istanze.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1473">Thus, if a class contains only static members and isn't intended to be instantiated, adding an empty private instance constructor will prevent instantiation.</span></span> <span data-ttu-id="5bc16-1474">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1474">For example:</span></span>
```csharp
public class Trig
{
    private Trig() {}        // Prevent instantiation

    public const double PI = 3.14159265358979323846;

    public static double Sin(double x) {...}
    public static double Cos(double x) {...}
    public static double Tan(double x) {...}
}
```

<span data-ttu-id="5bc16-1475">Il `Trig` classe gruppi costanti e metodi correlati, ma non è possibile creare un'istanza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1475">The `Trig` class groups related methods and constants, but is not intended to be instantiated.</span></span> <span data-ttu-id="5bc16-1476">Di conseguenza dichiara un costruttore di istanza singola di privato vuoto.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1476">Therefore it declares a single empty private instance constructor.</span></span> <span data-ttu-id="5bc16-1477">Costruttore almeno un'istanza deve essere dichiarato per eliminare la generazione automatica di un costruttore predefinito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1477">At least one instance constructor must be declared to suppress the automatic generation of a default constructor.</span></span>

### <a name="optional-instance-constructor-parameters"></a><span data-ttu-id="5bc16-1478">Parametri del costruttore istanza facoltativa</span><span class="sxs-lookup"><span data-stu-id="5bc16-1478">Optional instance constructor parameters</span></span>

<span data-ttu-id="5bc16-1479">Il `this(...)` un inizializzatore di costruttore viene comunemente utilizzato in combinazione con l'overload per implementare i parametri di costruttore di istanza facoltativo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1479">The `this(...)` form of constructor initializer is commonly used in conjunction with overloading to implement optional instance constructor parameters.</span></span> <span data-ttu-id="5bc16-1480">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1480">In the example</span></span>
```csharp
class Text
{
    public Text(): this(0, 0, null) {}

    public Text(int x, int y): this(x, y, null) {}

    public Text(int x, int y, string s) {
        // Actual constructor implementation
    }
}
```
<span data-ttu-id="5bc16-1481">i costruttori di due istanza prima forniscono semplicemente i valori predefiniti per gli argomenti mancanti.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1481">the first two instance constructors merely provide the default values for the missing arguments.</span></span> <span data-ttu-id="5bc16-1482">Entrambi usano un `this(...)` inizializzatore del costruttore da richiamare il terzo costruttore di istanza, che effettivamente esegue le operazioni di inizializzazione dell'istanza nuova.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1482">Both use a `this(...)` constructor initializer to invoke the third instance constructor, which actually does the work of initializing the new instance.</span></span> <span data-ttu-id="5bc16-1483">L'effetto è quello di parametri del costruttore facoltativo:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1483">The effect is that of optional constructor parameters:</span></span>
```csharp
Text t1 = new Text();                    // Same as Text(0, 0, null)
Text t2 = new Text(5, 10);               // Same as Text(5, 10, null)
Text t3 = new Text(5, 20, "Hello");
```

## <a name="static-constructors"></a><span data-ttu-id="5bc16-1484">Costruttori statici</span><span class="sxs-lookup"><span data-stu-id="5bc16-1484">Static constructors</span></span>

<span data-ttu-id="5bc16-1485">Oggetto ***costruttore statico*** è un membro che implementa le azioni necessarie per inizializzare un tipo di classe chiuso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1485">A ***static constructor*** is a member that implements the actions required to initialize a closed class type.</span></span> <span data-ttu-id="5bc16-1486">I costruttori statici vengono dichiarati usando *static_constructor_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1486">Static constructors are declared using *static_constructor_declaration*s:</span></span>

```antlr
static_constructor_declaration
    : attributes? static_constructor_modifiers identifier '(' ')' static_constructor_body
    ;

static_constructor_modifiers
    : 'extern'? 'static'
    | 'static' 'extern'?
    | static_constructor_modifiers_unsafe
    ;

static_constructor_body
    : block
    | ';'
    ;
```

<span data-ttu-id="5bc16-1487">Oggetto *static_constructor_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e un `extern` modificatore ([metodi esterni](classes.md#external-methods)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1487">A *static_constructor_declaration* may include a set of *attributes* ([Attributes](attributes.md)) and an `extern` modifier ([External methods](classes.md#external-methods)).</span></span>

<span data-ttu-id="5bc16-1488">Il *identifier* di un *static_constructor_declaration* deve denominare la classe in cui viene dichiarato il costruttore statico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1488">The *identifier* of a *static_constructor_declaration* must name the class in which the static constructor is declared.</span></span> <span data-ttu-id="5bc16-1489">Se viene specificato un altro nome, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1489">If any other name is specified, a compile-time error occurs.</span></span>

<span data-ttu-id="5bc16-1490">Quando una dichiarazione di costruttore statico include un' `extern` modificatore, il costruttore statico viene definito un ***costruttore statico esterno***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1490">When a static constructor declaration includes an `extern` modifier, the static constructor is said to be an ***external static constructor***.</span></span> <span data-ttu-id="5bc16-1491">Poiché una dichiarazione di costruttore statico esterni non fornisce alcuna implementazione effettiva, relativi *static_constructor_body* è costituito da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1491">Because an external static constructor declaration provides no actual implementation, its *static_constructor_body* consists of a semicolon.</span></span> <span data-ttu-id="5bc16-1492">Per tutte le altre dichiarazioni di costruttore statico, il *static_constructor_body* costituito da un *blocco* che consente di specificare le istruzioni da eseguire per inizializzare la classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1492">For all other static constructor declarations, the *static_constructor_body* consists of a *block* which specifies the statements to execute in order to initialize the class.</span></span> <span data-ttu-id="5bc16-1493">Questo corrisponde esattamente al *method_body* di un metodo statico con un `void` tipo di valore restituito ([corpo del metodo](classes.md#method-body)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1493">This corresponds exactly to the *method_body* of a static method with a `void` return type ([Method body](classes.md#method-body)).</span></span>

<span data-ttu-id="5bc16-1494">I costruttori statici non vengono ereditati e non possono essere chiamati direttamente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1494">Static constructors are not inherited, and cannot be called directly.</span></span>

<span data-ttu-id="5bc16-1495">Il costruttore statico per un tipo di classe chiuso esegue al massimo una volta in un dominio applicazione specificato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1495">The static constructor for a closed class type executes at most once in a given application domain.</span></span> <span data-ttu-id="5bc16-1496">L'esecuzione di un costruttore statico viene attivato dal primo degli eventi seguenti che si verificano all'interno di un dominio dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1496">The execution of a static constructor is triggered by the first of the following events to occur within an application domain:</span></span>

*  <span data-ttu-id="5bc16-1497">Viene creata un'istanza del tipo di classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1497">An instance of the class type is created.</span></span>
*  <span data-ttu-id="5bc16-1498">Qualsiasi membro statico del tipo di classe viene fatto riferimento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1498">Any of the static members of the class type are referenced.</span></span>

<span data-ttu-id="5bc16-1499">Se una classe contiene il `Main` metodo ([avvio dell'applicazione](basic-concepts.md#application-startup)) in cui inizia l'esecuzione, il costruttore statico per tale classe viene eseguito prima il `Main` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1499">If a class contains the `Main` method ([Application Startup](basic-concepts.md#application-startup)) in which execution begins, the static constructor for that class executes before the `Main` method is called.</span></span>

<span data-ttu-id="5bc16-1500">Per inizializzare un nuovo tipo di classe chiuso, prima di tutto un nuovo set di campi statici ([campi statici e di istanza](classes.md#static-and-instance-fields)) per quel particolare tipo di chiusura viene creato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1500">To initialize a new closed class type, first a new set of static fields ([Static and instance fields](classes.md#static-and-instance-fields)) for that particular closed type is created.</span></span> <span data-ttu-id="5bc16-1501">Ogni campo statico viene inizializzato sul valore predefinito ([i valori predefiniti](variables.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1501">Each of the static fields is initialized to its default value ([Default values](variables.md#default-values)).</span></span> <span data-ttu-id="5bc16-1502">Successivamente, gli inizializzatori di campo statico ([inizializzazione del campo statico](classes.md#static-field-initialization)) vengono eseguiti per i campi statici.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1502">Next, the static field initializers ([Static field initialization](classes.md#static-field-initialization)) are executed for those static fields.</span></span> <span data-ttu-id="5bc16-1503">Infine, viene eseguito il costruttore statico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1503">Finally, the static constructor is executed.</span></span>

<span data-ttu-id="5bc16-1504">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1504">The example</span></span>
```csharp
using System;

class Test
{
    static void Main() {
        A.F();
        B.F();
    }
}

class A
{
    static A() {
        Console.WriteLine("Init A");
    }
    public static void F() {
        Console.WriteLine("A.F");
    }
}

class B
{
    static B() {
        Console.WriteLine("Init B");
    }
    public static void F() {
        Console.WriteLine("B.F");
    }
}
```
<span data-ttu-id="5bc16-1505">è necessario produrre l'output:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1505">must produce the output:</span></span>
```
Init A
A.F
Init B
B.F
```
<span data-ttu-id="5bc16-1506">Poiché l'esecuzione di `A`del costruttore statico viene attivato dalla chiamata a `A.F`e l'esecuzione di `B`del costruttore statico viene attivato dalla chiamata al metodo `B.F`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1506">because the execution of `A`'s static constructor is triggered by the call to `A.F`, and the execution of `B`'s static constructor is triggered by the call to `B.F`.</span></span>

<span data-ttu-id="5bc16-1507">È possibile creare dipendenze circolari che consentono i campi statici con gli inizializzatori di variabili da osservare nel relativo stato di valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1507">It is possible to construct circular dependencies that allow static fields with variable initializers to be observed in their default value state.</span></span>

<span data-ttu-id="5bc16-1508">L'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1508">The example</span></span>
```csharp
using System;

class A
{
    public static int X;

    static A() {
        X = B.Y + 1;
    }
}

class B
{
    public static int Y = A.X + 1;

    static B() {}

    static void Main() {
        Console.WriteLine("X = {0}, Y = {1}", A.X, B.Y);
    }
}
```
<span data-ttu-id="5bc16-1509">produce l'output</span><span class="sxs-lookup"><span data-stu-id="5bc16-1509">produces the output</span></span>
```
X = 1, Y = 2
```

<span data-ttu-id="5bc16-1510">Per eseguire la `Main` metodo, il sistema esegue innanzitutto l'inizializzatore per `B.Y`, prima di classe `B`del costruttore statico.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1510">To execute the `Main` method, the system first runs the initializer for `B.Y`, prior to class `B`'s static constructor.</span></span> <span data-ttu-id="5bc16-1511">`Y`dell'inizializzatore `A`del costruttore statico per essere eseguito perché il valore di `A.X` viene fatto riferimento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1511">`Y`'s initializer causes `A`'s static constructor to be run because the value of `A.X` is referenced.</span></span> <span data-ttu-id="5bc16-1512">Costruttore statico della `A` procede a sua volta per calcolare il valore di `X`e sulle operazioni di recupero in questo caso il valore predefinito di `Y`, che è uguale a zero.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1512">The static constructor of `A` in turn proceeds to compute the value of `X`, and in doing so fetches the default value of `Y`, which is zero.</span></span> <span data-ttu-id="5bc16-1513">`A.X` In questo modo viene inizializzato su 1.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1513">`A.X` is thus initialized to 1.</span></span> <span data-ttu-id="5bc16-1514">Il processo di esecuzione `A`del costruttore statico e gli inizializzatori di campo statico, quindi viene completato, tornando al calcolo del valore iniziale di `Y`, il cui risultato diventa 2.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1514">The process of running `A`'s static field initializers and static constructor then completes, returning to the calculation of the initial value of `Y`, the result of which becomes 2.</span></span>

<span data-ttu-id="5bc16-1515">Poiché il costruttore statico viene eseguito una sola volta per ogni tipo classe costruito chiuso, è un modo pratico per applicare controlli di runtime nel parametro di tipo che non può essere controllato in fase di compilazione tramite i vincoli ([parametro di tipo i vincoli](classes.md#type-parameter-constraints)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1515">Because the static constructor is executed exactly once for each closed constructed class type, it is a convenient place to enforce run-time checks on the type parameter that cannot be checked at compile-time via constraints ([Type parameter constraints](classes.md#type-parameter-constraints)).</span></span> <span data-ttu-id="5bc16-1516">Ad esempio, il tipo seguente usa un costruttore statico per imporre che l'argomento tipo è un'enumerazione:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1516">For example, the following type uses a static constructor to enforce that the type argument is an enum:</span></span>
```csharp
class Gen<T> where T: struct
{
    static Gen() {
        if (!typeof(T).IsEnum) {
            throw new ArgumentException("T must be an enum");
        }
    }
}
```

## <a name="destructors"></a><span data-ttu-id="5bc16-1517">Distruttori</span><span class="sxs-lookup"><span data-stu-id="5bc16-1517">Destructors</span></span>

<span data-ttu-id="5bc16-1518">Oggetto ***distruttore*** è un membro che implementa le azioni necessarie per distruggere un'istanza di una classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1518">A ***destructor*** is a member that implements the actions required to destruct an instance of a class.</span></span> <span data-ttu-id="5bc16-1519">Un distruttore viene dichiarato con un *destructor_declaration*:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1519">A destructor is declared using a *destructor_declaration*:</span></span>

```antlr
destructor_declaration
    : attributes? 'extern'? '~' identifier '(' ')' destructor_body
    | destructor_declaration_unsafe
    ;

destructor_body
    : block
    | ';'
    ;
```

<span data-ttu-id="5bc16-1520">Oggetto *destructor_declaration* può includere un set di *attributi* ([attributi](attributes.md)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1520">A *destructor_declaration* may include a set of *attributes* ([Attributes](attributes.md)).</span></span>

<span data-ttu-id="5bc16-1521">Il *identifier* di un *destructor_declaration* deve denominare la classe in cui il distruttore viene dichiarato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1521">The *identifier* of a *destructor_declaration* must name the class in which the destructor is declared.</span></span> <span data-ttu-id="5bc16-1522">Se viene specificato un altro nome, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1522">If any other name is specified, a compile-time error occurs.</span></span>

<span data-ttu-id="5bc16-1523">Quando una dichiarazione di un distruttore include un' `extern` modificatore, il distruttore viene definito un' ***distruttore esterno***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1523">When a destructor declaration includes an `extern` modifier, the destructor is said to be an ***external destructor***.</span></span> <span data-ttu-id="5bc16-1524">Poiché una dichiarazione esterna distruttore non fornisce alcuna implementazione effettiva, relativi *destructor_body* è costituito da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1524">Because an external destructor declaration provides no actual implementation, its *destructor_body* consists of a semicolon.</span></span> <span data-ttu-id="5bc16-1525">Per tutti gli altri distruttori, il *destructor_body* costituito da un *blocco* che consente di specificare le istruzioni da eseguire per distruggere un'istanza della classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1525">For all other destructors, the *destructor_body* consists of a *block* which specifies the statements to execute in order to destruct an instance of the class.</span></span> <span data-ttu-id="5bc16-1526">Oggetto *destructor_body* corrispondono esattamente al *method_body* di un metodo di istanza con un `void` tipo restituito ([corpo del metodo](classes.md#method-body)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1526">A *destructor_body* corresponds exactly to the *method_body* of an instance method with a `void` return type ([Method body](classes.md#method-body)).</span></span>

<span data-ttu-id="5bc16-1527">I distruttori non vengono ereditati.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1527">Destructors are not inherited.</span></span> <span data-ttu-id="5bc16-1528">Di conseguenza, una classe non dispone di alcun distruttore diverso da quello che può essere dichiarato nella classe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1528">Thus, a class has no destructors other than the one which may be declared in that class.</span></span>

<span data-ttu-id="5bc16-1529">Poiché un distruttore deve essere priva di parametri, non può essere sottoposto a overload, in modo che una classe può avere al massimo un distruttore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1529">Since a destructor is required to have no parameters, it cannot be overloaded, so a class can have, at most, one destructor.</span></span>

<span data-ttu-id="5bc16-1530">I distruttori vengono chiamati automaticamente e non possono essere richiamati in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1530">Destructors are invoked automatically, and cannot be invoked explicitly.</span></span> <span data-ttu-id="5bc16-1531">Un'istanza diventa idonea per l'eliminazione quando non è più possibile per il codice utilizzare tale istanza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1531">An instance becomes eligible for destruction when it is no longer possible for any code to use that instance.</span></span> <span data-ttu-id="5bc16-1532">L'esecuzione del distruttore per l'istanza può verificarsi in qualsiasi momento dopo l'istanza diventa idoneo per l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1532">Execution of the destructor for the instance may occur at any time after the instance becomes eligible for destruction.</span></span> <span data-ttu-id="5bc16-1533">Quando viene eliminata un'istanza, vengono chiamati i distruttori nella catena di ereditarietà dell'istanza, in ordine, dalla maggior parte derivata alla meno derivata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1533">When an instance is destructed, the destructors in that instance's inheritance chain are called, in order, from most derived to least derived.</span></span> <span data-ttu-id="5bc16-1534">Un distruttore può essere eseguito su qualsiasi thread.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1534">A destructor may be executed on any thread.</span></span> <span data-ttu-id="5bc16-1535">Per ulteriori informazioni sulle regole che determinano quando e come viene eseguito un distruttore, vedere [gestione automatica della memoria](basic-concepts.md#automatic-memory-management).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1535">For further discussion of the rules that govern when and how a destructor is executed, see [Automatic memory management](basic-concepts.md#automatic-memory-management).</span></span>

<span data-ttu-id="5bc16-1536">L'output dell'esempio</span><span class="sxs-lookup"><span data-stu-id="5bc16-1536">The output of the example</span></span>
```csharp
using System;

class A
{
    ~A() {
        Console.WriteLine("A's destructor");
    }
}

class B: A
{
    ~B() {
        Console.WriteLine("B's destructor");
    }
}

class Test
{
   static void Main() {
        B b = new B();
        b = null;
        GC.Collect();
        GC.WaitForPendingFinalizers();
   }
}
```
<span data-ttu-id="5bc16-1537">is</span><span class="sxs-lookup"><span data-stu-id="5bc16-1537">is</span></span>
```
B's destructor
A's destructor
```
<span data-ttu-id="5bc16-1538">Poiché i distruttori in una catena di ereditarietà vengono chiamati in ordine, dalla maggior parte derivata alla meno derivata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1538">since destructors in an inheritance chain are called in order, from most derived to least derived.</span></span>

<span data-ttu-id="5bc16-1539">Eseguendo l'override del metodo virtuale vengono implementati i distruttori `Finalize` su `System.Object`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1539">Destructors are implemented by overriding the virtual method `Finalize` on `System.Object`.</span></span> <span data-ttu-id="5bc16-1540">I programmi c# non sono autorizzati a eseguire l'override di questo metodo oppure chiamare (o le sostituzioni di esso) direttamente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1540">C# programs are not permitted to override this method or call it (or overrides of it) directly.</span></span> <span data-ttu-id="5bc16-1541">Ad esempio, il programma</span><span class="sxs-lookup"><span data-stu-id="5bc16-1541">For instance, the program</span></span>
```csharp
class A 
{
    override protected void Finalize() {}    // error

    public void F() {
        this.Finalize();                     // error
    }
}
```
<span data-ttu-id="5bc16-1542">contiene due errori.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1542">contains two errors.</span></span>

<span data-ttu-id="5bc16-1543">Il compilatore si comporta come se questo metodo ed esegue l'override, non esistono affatto.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1543">The compiler behaves as if this method, and overrides of it, do not exist at all.</span></span> <span data-ttu-id="5bc16-1544">Di conseguenza, questo programma:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1544">Thus, this program:</span></span>
```csharp
class A 
{
    void Finalize() {}                            // permitted
}
```
<span data-ttu-id="5bc16-1545">è valido, e il metodo illustrato nasconde `System.Object`del `Finalize` (metodo).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1545">is valid, and the method shown hides `System.Object`'s `Finalize` method.</span></span>

<span data-ttu-id="5bc16-1546">Per una descrizione del comportamento quando viene generata un'eccezione da un distruttore, vedere [gestione delle eccezioni](exceptions.md#how-exceptions-are-handled).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1546">For a discussion of the behavior when an exception is thrown from a destructor, see [How exceptions are handled](exceptions.md#how-exceptions-are-handled).</span></span>

## <a name="iterators"></a><span data-ttu-id="5bc16-1547">Iterators</span><span class="sxs-lookup"><span data-stu-id="5bc16-1547">Iterators</span></span>

<span data-ttu-id="5bc16-1548">Un membro di funzione ([membri funzione](expressions.md#function-members)) implementata usando un blocco iteratore ([blocchi](statements.md#blocks)) viene chiamato un ***iteratore***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1548">A function member ([Function members](expressions.md#function-members)) implemented using an iterator block ([Blocks](statements.md#blocks)) is called an ***iterator***.</span></span>

<span data-ttu-id="5bc16-1549">Un blocco iteratore può essere usato come il corpo di un membro di funzione, purché il tipo restituito del membro di funzione corrispondente è una delle interfacce enumeratore ([enumeratore (interfacce)](classes.md#enumerator-interfaces)) o una delle interfacce enumerabili ([Interfacce enumerabili](classes.md#enumerable-interfaces)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1549">An iterator block may be used as the body of a function member as long as the return type of the corresponding function member is one of the enumerator interfaces ([Enumerator interfaces](classes.md#enumerator-interfaces)) or one of the enumerable interfaces ([Enumerable interfaces](classes.md#enumerable-interfaces)).</span></span> <span data-ttu-id="5bc16-1550">Può verificarsi come un *method_body*, *operator_body* oppure *accessor_body*, mentre gli eventi, costruttori di istanze, i costruttori statici e i distruttori non possono essere implementate come iteratori.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1550">It can occur as a *method_body*, *operator_body* or *accessor_body*, whereas events, instance constructors, static constructors and destructors cannot be implemented as iterators.</span></span>

<span data-ttu-id="5bc16-1551">Quando un membro di funzione viene implementato usando un blocco iteratore, è un errore in fase di compilazione per l'elenco di parametri formali del membro funzione per specificare eventuali `ref` o `out` parametri.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1551">When a function member is implemented using an iterator block, it is a compile-time error for the formal parameter list of the function member to specify any `ref` or `out` parameters.</span></span>

### <a name="enumerator-interfaces"></a><span data-ttu-id="5bc16-1552">Enumeratore (interfacce)</span><span class="sxs-lookup"><span data-stu-id="5bc16-1552">Enumerator interfaces</span></span>

<span data-ttu-id="5bc16-1553">Il ***interfacce dell'enumeratore*** rappresentano l'interfaccia non generica `System.Collections.IEnumerator` e tutte le istanze dell'interfaccia generica `System.Collections.Generic.IEnumerator<T>`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1553">The ***enumerator interfaces*** are the non-generic interface `System.Collections.IEnumerator` and all instantiations of the generic interface `System.Collections.Generic.IEnumerator<T>`.</span></span> <span data-ttu-id="5bc16-1554">Per ragioni di brevità, in questo capitolo queste interfacce vengono fatto riferimento come `IEnumerator` e `IEnumerator<T>`, rispettivamente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1554">For the sake of brevity, in this chapter these interfaces are referenced as `IEnumerator` and `IEnumerator<T>`, respectively.</span></span>

### <a name="enumerable-interfaces"></a><span data-ttu-id="5bc16-1555">Interfacce enumerabili</span><span class="sxs-lookup"><span data-stu-id="5bc16-1555">Enumerable interfaces</span></span>

<span data-ttu-id="5bc16-1556">Il ***interfacce enumerabili*** rappresentano l'interfaccia non generica `System.Collections.IEnumerable` e tutte le istanze dell'interfaccia generica `System.Collections.Generic.IEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1556">The ***enumerable interfaces*** are the non-generic interface `System.Collections.IEnumerable` and all instantiations of the generic interface `System.Collections.Generic.IEnumerable<T>`.</span></span> <span data-ttu-id="5bc16-1557">Per ragioni di brevità, in questo capitolo queste interfacce vengono fatto riferimento come `IEnumerable` e `IEnumerable<T>`, rispettivamente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1557">For the sake of brevity, in this chapter these interfaces are referenced as `IEnumerable` and `IEnumerable<T>`, respectively.</span></span>

### <a name="yield-type"></a><span data-ttu-id="5bc16-1558">Tipo yield</span><span class="sxs-lookup"><span data-stu-id="5bc16-1558">Yield type</span></span>

<span data-ttu-id="5bc16-1559">Un iteratore produce una sequenza di valori, tutti dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1559">An iterator produces a sequence of values, all of the same type.</span></span> <span data-ttu-id="5bc16-1560">Questo tipo è definito il ***yield tipo*** dell'iteratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1560">This type is called the ***yield type*** of the iterator.</span></span>

*  <span data-ttu-id="5bc16-1561">Il tipo di rendimento di un iteratore che restituisce `IEnumerator` oppure `IEnumerable` è `object`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1561">The yield type of an iterator that returns `IEnumerator` or `IEnumerable` is `object`.</span></span>
*  <span data-ttu-id="5bc16-1562">Il tipo di rendimento di un iteratore che restituisce `IEnumerator<T>` oppure `IEnumerable<T>` è `T`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1562">The yield type of an iterator that returns `IEnumerator<T>` or `IEnumerable<T>` is `T`.</span></span>

### <a name="enumerator-objects"></a><span data-ttu-id="5bc16-1563">Oggetti dell'enumeratore</span><span class="sxs-lookup"><span data-stu-id="5bc16-1563">Enumerator objects</span></span>

<span data-ttu-id="5bc16-1564">Quando un membro di funzione che restituisce un enumeratore tipo di interfaccia viene implementato usando un blocco iteratore, richiamare il membro di funzione non eseguire immediatamente il codice nel blocco iteratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1564">When a function member returning an enumerator interface type is implemented using an iterator block, invoking the function member does not immediately execute the code in the iterator block.</span></span> <span data-ttu-id="5bc16-1565">Al contrario, un ***oggetto enumerator*** viene creata e restituita.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1565">Instead, an ***enumerator object*** is created and returned.</span></span> <span data-ttu-id="5bc16-1566">Questo oggetto incapsula il codice nel blocco iteratore specificato e l'esecuzione del codice nel blocco iteratore si verifica quando l'oggetto enumeratore `MoveNext` metodo viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1566">This object encapsulates the code specified in the iterator block, and execution of the code in the iterator block occurs when the enumerator object's `MoveNext` method is invoked.</span></span> <span data-ttu-id="5bc16-1567">Oggetto enumeratore ha le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1567">An enumerator object has the following characteristics:</span></span>

*  <span data-ttu-id="5bc16-1568">Implementa `IEnumerator` e `IEnumerator<T>`, dove `T` è il tipo di rendimento dell'iteratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1568">It implements `IEnumerator` and `IEnumerator<T>`, where `T` is the yield type of the iterator.</span></span>
*  <span data-ttu-id="5bc16-1569">Implementa `System.IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1569">It implements `System.IDisposable`.</span></span>
*  <span data-ttu-id="5bc16-1570">Viene inizializzato con una copia dei valori di argomento (se presente) e valore dell'istanza passato per il membro di funzione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1570">It is initialized with a copy of the argument values (if any) and instance value passed to the function member.</span></span>
*  <span data-ttu-id="5bc16-1571">Ha quattro diversi stati: ***prima***, ***in esecuzione***, ***sospeso***, e ***dopo***e si trova inizialmente nel ***prima***  dello stato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1571">It has four potential states, ***before***, ***running***, ***suspended***, and ***after***, and is initially in the ***before*** state.</span></span>

<span data-ttu-id="5bc16-1572">Oggetto enumeratore è in genere un'istanza di una classe enumerator generato dal compilatore che incapsula il codice nel blocco iteratore e implementa le interfacce dell'enumeratore, ma sono possibili altri metodi di implementazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1572">An enumerator object is typically an instance of a compiler-generated enumerator class that encapsulates the code in the iterator block and implements the enumerator interfaces, but other methods of implementation are possible.</span></span> <span data-ttu-id="5bc16-1573">Se una classe enumerator viene generata dal compilatore, tale classe verrà nidificata, direttamente o indirettamente, nella classe che contiene il membro di funzione, avrà l'accessibilità privata e avrà un nome riservato per l'utilizzo del compilatore ([identificatori ](lexical-structure.md#identifiers)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1573">If an enumerator class is generated by the compiler, that class will be nested, directly or indirectly, in the class containing the function member, it will have private accessibility, and it will have a name reserved for compiler use ([Identifiers](lexical-structure.md#identifiers)).</span></span>

<span data-ttu-id="5bc16-1574">Oggetto enumeratore può implementare più interfacce rispetto a quelle specificate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1574">An enumerator object may implement more interfaces than those specified above.</span></span>

<span data-ttu-id="5bc16-1575">Le sezioni seguenti descrivono il comportamento esatto della `MoveNext`, `Current`, e `Dispose` membri del `IEnumerable` e `IEnumerable<T>` implementazioni fornite da un oggetto enumeratore dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1575">The following sections describe the exact behavior of the `MoveNext`, `Current`, and `Dispose` members of the `IEnumerable` and `IEnumerable<T>` interface implementations provided by an enumerator object.</span></span>

<span data-ttu-id="5bc16-1576">Si noti che non supportano oggetti dell'enumeratore di `IEnumerator.Reset` (metodo).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1576">Note that enumerator objects do not support the `IEnumerator.Reset` method.</span></span> <span data-ttu-id="5bc16-1577">Richiamando questo metodo un `System.NotSupportedException` generata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1577">Invoking this method causes a `System.NotSupportedException` to be thrown.</span></span>

#### <a name="the-movenext-method"></a><span data-ttu-id="5bc16-1578">Il metodo MoveNext</span><span class="sxs-lookup"><span data-stu-id="5bc16-1578">The MoveNext method</span></span>

<span data-ttu-id="5bc16-1579">Il `MoveNext` metodo dell'oggetto enumeratore incapsula il codice di un blocco iteratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1579">The `MoveNext` method of an enumerator object encapsulates the code of an iterator block.</span></span> <span data-ttu-id="5bc16-1580">Richiamare il `MoveNext` metodo esegue codice nel blocco iteratore e imposta il `Current` proprietà dell'oggetto enumeratore come appropriato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1580">Invoking the `MoveNext` method executes code in the iterator block and sets the `Current` property of the enumerator object as appropriate.</span></span> <span data-ttu-id="5bc16-1581">L'azione specifica eseguita dal `MoveNext` dipende dallo stato dell'oggetto enumeratore quando `MoveNext` viene richiamato:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1581">The precise action performed by `MoveNext` depends on the state of the enumerator object when `MoveNext` is invoked:</span></span>

*  <span data-ttu-id="5bc16-1582">Se lo stato dell'oggetto enumeratore ***prima***, la chiamata `MoveNext`:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1582">If the state of the enumerator object is ***before***, invoking `MoveNext`:</span></span>
   * <span data-ttu-id="5bc16-1583">Imposta lo stato su ***in esecuzione***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1583">Changes the state to ***running***.</span></span>
   * <span data-ttu-id="5bc16-1584">Inizializza i parametri (tra cui `this`) del blocco di iteratore per i valori degli argomenti e il valore di istanza salvato quando l'oggetto enumeratore è stato inizializzato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1584">Initializes the parameters (including `this`) of the iterator block to the argument values and instance value saved when the enumerator object was initialized.</span></span>
   * <span data-ttu-id="5bc16-1585">Esegue il blocco iteratore dall'inizio fino a quando non viene interrotta l'esecuzione (come descritto di seguito).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1585">Executes the iterator block from the beginning until execution is interrupted (as described below).</span></span>
*  <span data-ttu-id="5bc16-1586">Se lo stato dell'oggetto enumeratore ***in esecuzione***, il risultato ottenuto richiamando `MoveNext` non è specificato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1586">If the state of the enumerator object is ***running***, the result of invoking `MoveNext` is unspecified.</span></span>
*  <span data-ttu-id="5bc16-1587">Se lo stato dell'oggetto enumeratore ***sospesa***, la chiamata `MoveNext`:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1587">If the state of the enumerator object is ***suspended***, invoking `MoveNext`:</span></span>
   * <span data-ttu-id="5bc16-1588">Imposta lo stato su ***in esecuzione***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1588">Changes the state to ***running***.</span></span>
   * <span data-ttu-id="5bc16-1589">Ripristina i valori di tutte le variabili e parametri locali (incluso questo) per i valori salvati durante l'ultima sospensione dell'esecuzione del blocco iteratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1589">Restores the values of all local variables and parameters (including this) to the values saved when execution of the iterator block was last suspended.</span></span> <span data-ttu-id="5bc16-1590">Si noti che il contenuto di tutti gli oggetti cui fa riferimento a queste variabili hanno modificato dalla chiamata precedente a MoveNext.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1590">Note that the contents of any objects referenced by these variables may have changed since the previous call to MoveNext.</span></span>
   * <span data-ttu-id="5bc16-1591">Riprende l'esecuzione del blocco iteratore subito dopo il `yield return` istruzione che ha causato la sospensione dell'esecuzione e continua fino a quando l'esecuzione viene interrotta (come descritto di seguito).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1591">Resumes execution of the iterator block immediately following the `yield return` statement that caused the suspension of execution and continues until execution is interrupted (as described below).</span></span>
*  <span data-ttu-id="5bc16-1592">Se lo stato dell'oggetto enumeratore ***dopo aver***, la chiamata `MoveNext` restituisce `false`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1592">If the state of the enumerator object is ***after***, invoking `MoveNext` returns `false`.</span></span>


<span data-ttu-id="5bc16-1593">Quando `MoveNext` esegue il blocco di iteratore, l'esecuzione può essere interrotta in quattro modi: Da un `yield return` istruzione, da un `yield break` istruzione, rilevando la fine del blocco iteratore e da un'eccezione viene generata un'eccezione e propagato fuori dal blocco di iteratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1593">When `MoveNext` executes the iterator block, execution can be interrupted in four ways: By a `yield return` statement, by a `yield break` statement, by encountering the end of the iterator block, and by an exception being thrown and propagated out of the iterator block.</span></span>

*  <span data-ttu-id="5bc16-1594">Quando un `yield return` viene rilevata un'istruzione ([l'istruzione yield](statements.md#the-yield-statement)):</span><span class="sxs-lookup"><span data-stu-id="5bc16-1594">When a `yield return` statement is encountered ([The yield statement](statements.md#the-yield-statement)):</span></span>
   * <span data-ttu-id="5bc16-1595">L'espressione specificata nell'istruzione viene valutata in modo implicito convertito nel tipo di yield e assegnato al `Current` proprietà dell'oggetto enumeratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1595">The expression given in the statement is evaluated, implicitly converted to the yield type, and assigned to the `Current` property of the enumerator object.</span></span>
   * <span data-ttu-id="5bc16-1596">L'esecuzione del corpo dell'iteratore viene sospesa.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1596">Execution of the iterator body is suspended.</span></span> <span data-ttu-id="5bc16-1597">I valori di tutti i parametri e variabili locali (incluso `this`) vengono salvate, nonché la posizione di questo `yield return` istruzione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1597">The values of all local variables and parameters (including `this`) are saved, as is the location of this `yield return` statement.</span></span> <span data-ttu-id="5bc16-1598">Se il `yield return` istruzione è all'interno di uno o più `try` blocca, associato `finally` blocchi non vengono eseguiti in questo momento.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1598">If the `yield return` statement is within one or more `try` blocks, the associated `finally` blocks are not executed at this time.</span></span>
   * <span data-ttu-id="5bc16-1599">Lo stato dell'oggetto enumeratore è impostato su ***sospeso***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1599">The state of the enumerator object is changed to ***suspended***.</span></span>
   * <span data-ttu-id="5bc16-1600">Il `MoveNext` restituzione del metodo `true` al relativo chiamante, che indica che l'iterazione viene spostato il valore successivo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1600">The `MoveNext` method returns `true` to its caller, indicating that the iteration successfully advanced to the next value.</span></span>
*  <span data-ttu-id="5bc16-1601">Quando un `yield break` viene rilevata un'istruzione ([l'istruzione yield](statements.md#the-yield-statement)):</span><span class="sxs-lookup"><span data-stu-id="5bc16-1601">When a `yield break` statement is encountered ([The yield statement](statements.md#the-yield-statement)):</span></span>
   * <span data-ttu-id="5bc16-1602">Se il `yield break` istruzione è all'interno di uno o più `try` blocca, associato `finally` vengono eseguiti blocchi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1602">If the `yield break` statement is within one or more `try` blocks, the associated `finally` blocks are executed.</span></span>
   * <span data-ttu-id="5bc16-1603">Lo stato dell'oggetto enumeratore è impostato su ***dopo aver***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1603">The state of the enumerator object is changed to ***after***.</span></span>
   * <span data-ttu-id="5bc16-1604">Il `MoveNext` restituzione del metodo `false` al relativo chiamante, che indica che l'iterazione è stata completata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1604">The `MoveNext` method returns `false` to its caller, indicating that the iteration is complete.</span></span>
*  <span data-ttu-id="5bc16-1605">Quando viene rilevata la fine del corpo dell'iteratore:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1605">When the end of the iterator body is encountered:</span></span>
   * <span data-ttu-id="5bc16-1606">Lo stato dell'oggetto enumeratore è impostato su ***dopo aver***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1606">The state of the enumerator object is changed to ***after***.</span></span>
   * <span data-ttu-id="5bc16-1607">Il `MoveNext` restituzione del metodo `false` al relativo chiamante, che indica che l'iterazione è stata completata.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1607">The `MoveNext` method returns `false` to its caller, indicating that the iteration is complete.</span></span>
*  <span data-ttu-id="5bc16-1608">Quando un'eccezione viene generata un'eccezione e propagata fuori dal blocco di iteratori:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1608">When an exception is thrown and propagated out of the iterator block:</span></span>
   * <span data-ttu-id="5bc16-1609">Appropriato `finally` blocchi nel corpo dell'iteratore vengono eseguiti mediante la propagazione dell'eccezione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1609">Appropriate `finally` blocks in the iterator body will have been executed by the exception propagation.</span></span>
   * <span data-ttu-id="5bc16-1610">Lo stato dell'oggetto enumeratore è impostato su ***dopo aver***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1610">The state of the enumerator object is changed to ***after***.</span></span>
   * <span data-ttu-id="5bc16-1611">Continua la propagazione dell'eccezione al chiamante del `MoveNext` (metodo).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1611">The exception propagation continues to the caller of the `MoveNext` method.</span></span>

#### <a name="the-current-property"></a><span data-ttu-id="5bc16-1612">La proprietà corrente</span><span class="sxs-lookup"><span data-stu-id="5bc16-1612">The Current property</span></span>

<span data-ttu-id="5bc16-1613">Oggetto enumeratore `Current` proprietà dipende dal `yield return` istruzioni nel blocco iteratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1613">An enumerator object's `Current` property is affected by `yield return` statements in the iterator block.</span></span>

<span data-ttu-id="5bc16-1614">Quando un oggetto enumeratore è nel ***sospesa*** lo stato, il valore di `Current` corrisponde al valore impostato dalla chiamata precedente a `MoveNext`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1614">When an enumerator object is in the ***suspended*** state, the value of `Current` is the value set by the previous call to `MoveNext`.</span></span> <span data-ttu-id="5bc16-1615">Quando un oggetto enumeratore è nel ***prima***, ***che esegue***, o ***dopo*** indicato, il risultato dell'accesso `Current` non è specificato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1615">When an enumerator object is in the ***before***, ***running***, or ***after*** states, the result of accessing `Current` is unspecified.</span></span>

<span data-ttu-id="5bc16-1616">Per un iteratore con una parola chiave yield tipo diverso da `object`, il risultato dell'accesso `Current` tramite l'oggetto enumeratore `IEnumerable` implementazione corrisponde all'accesso `Current` tramite l'oggetto enumeratore `IEnumerator<T>` implementazione e il cast del risultato a `object`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1616">For an iterator with a yield type other than `object`, the result of accessing `Current` through the enumerator object's `IEnumerable` implementation corresponds to accessing `Current` through the enumerator object's `IEnumerator<T>` implementation and casting the result to `object`.</span></span>

#### <a name="the-dispose-method"></a><span data-ttu-id="5bc16-1617">Il metodo Dispose</span><span class="sxs-lookup"><span data-stu-id="5bc16-1617">The Dispose method</span></span>

<span data-ttu-id="5bc16-1618">Il `Dispose` metodo viene utilizzato per pulire l'iterazione portando l'oggetto enumeratore il ***dopo*** dello stato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1618">The `Dispose` method is used to clean up the iteration by bringing the enumerator object to the ***after*** state.</span></span>

*  <span data-ttu-id="5bc16-1619">Se lo stato dell'oggetto enumeratore ***prima***, la chiamata `Dispose` imposta lo stato su ***dopo***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1619">If the state of the enumerator object is ***before***, invoking `Dispose` changes the state to ***after***.</span></span>
*  <span data-ttu-id="5bc16-1620">Se lo stato dell'oggetto enumeratore ***in esecuzione***, il risultato ottenuto richiamando `Dispose` non è specificato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1620">If the state of the enumerator object is ***running***, the result of invoking `Dispose` is unspecified.</span></span>
*  <span data-ttu-id="5bc16-1621">Se lo stato dell'oggetto enumeratore ***sospesa***, la chiamata `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1621">If the state of the enumerator object is ***suspended***, invoking `Dispose`:</span></span>
   * <span data-ttu-id="5bc16-1622">Imposta lo stato su ***in esecuzione***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1622">Changes the state to ***running***.</span></span>
   * <span data-ttu-id="5bc16-1623">Esegue qualsiasi infine blocchi come se l'ultima esecuzione `yield return` istruzione sono stati un `yield break` istruzione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1623">Executes any finally blocks as if the last executed `yield return` statement were a `yield break` statement.</span></span> <span data-ttu-id="5bc16-1624">Se questo genera un'eccezione generata e propagato fuori dal corpo dell'iteratore, lo stato dell'oggetto enumeratore è impostato su ***dopo aver*** e l'eccezione viene propagata al chiamante del `Dispose` (metodo).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1624">If this causes an exception to be thrown and propagated out of the iterator body, the state of the enumerator object is set to ***after*** and the exception is propagated to the caller of the `Dispose` method.</span></span>
   * <span data-ttu-id="5bc16-1625">Imposta lo stato su ***dopo aver***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1625">Changes the state to ***after***.</span></span>
*  <span data-ttu-id="5bc16-1626">Se lo stato dell'oggetto enumeratore ***dopo aver***, la chiamata `Dispose` non ha alcun effetto.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1626">If the state of the enumerator object is ***after***, invoking `Dispose` has no affect.</span></span>

### <a name="enumerable-objects"></a><span data-ttu-id="5bc16-1627">Oggetti enumerabili</span><span class="sxs-lookup"><span data-stu-id="5bc16-1627">Enumerable objects</span></span>

<span data-ttu-id="5bc16-1628">Quando un membro di funzione che restituisce un tipo enumerabile interfaccia viene implementato usando un blocco iteratore, richiamare il membro di funzione non eseguire immediatamente il codice nel blocco iteratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1628">When a function member returning an enumerable interface type is implemented using an iterator block, invoking the function member does not immediately execute the code in the iterator block.</span></span> <span data-ttu-id="5bc16-1629">Al contrario, un ***oggetto enumerabile*** viene creata e restituita.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1629">Instead, an ***enumerable object*** is created and returned.</span></span> <span data-ttu-id="5bc16-1630">L'oggetto enumerabile `GetEnumerator` metodo viene restituito un oggetto enumeratore che incapsula il codice specificato nel blocco iteratore e si verifica l'esecuzione del codice nel blocco iteratore quando l'oggetto enumeratore `MoveNext` metodo viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1630">The enumerable object's `GetEnumerator` method returns an enumerator object that encapsulates the code specified in the iterator block, and execution of the code in the iterator block occurs when the enumerator object's `MoveNext` method is invoked.</span></span> <span data-ttu-id="5bc16-1631">Oggetto enumerabile presenta le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1631">An enumerable object has the following characteristics:</span></span>

*  <span data-ttu-id="5bc16-1632">Implementa `IEnumerable` e `IEnumerable<T>`, dove `T` è il tipo di rendimento dell'iteratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1632">It implements `IEnumerable` and `IEnumerable<T>`, where `T` is the yield type of the iterator.</span></span>
*  <span data-ttu-id="5bc16-1633">Viene inizializzato con una copia dei valori di argomento (se presente) e valore dell'istanza passato per il membro di funzione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1633">It is initialized with a copy of the argument values (if any) and instance value passed to the function member.</span></span>

<span data-ttu-id="5bc16-1634">Oggetto enumerabile in genere è un'istanza della classe enumerable generato dal compilatore che incapsula il codice nel blocco iteratore e implementa le interfacce enumerabili, ma sono possibili altri metodi di implementazione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1634">An enumerable object is typically an instance of a compiler-generated enumerable class that encapsulates the code in the iterator block and implements the enumerable interfaces, but other methods of implementation are possible.</span></span> <span data-ttu-id="5bc16-1635">Se una classe enumerable viene generata dal compilatore, tale classe verrà nidificata, direttamente o indirettamente, nella classe che contiene il membro di funzione, avrà l'accessibilità privata e avrà un nome riservato per l'utilizzo del compilatore ([identificatori ](lexical-structure.md#identifiers)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1635">If an enumerable class is generated by the compiler, that class will be nested, directly or indirectly, in the class containing the function member, it will have private accessibility, and it will have a name reserved for compiler use ([Identifiers](lexical-structure.md#identifiers)).</span></span>

<span data-ttu-id="5bc16-1636">Oggetto enumerabile può implementare più interfacce rispetto a quelle specificate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1636">An enumerable object may implement more interfaces than those specified above.</span></span> <span data-ttu-id="5bc16-1637">In particolare, un oggetto enumerabile può anche implementare `IEnumerator` e `IEnumerator<T>`, in modo da poter essere utilizzato come un oggetto enumerabile sia un enumeratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1637">In particular, an enumerable object may also implement `IEnumerator` and `IEnumerator<T>`, enabling it to serve as both an enumerable and an enumerator.</span></span> <span data-ttu-id="5bc16-1638">In tale tipo di implementazione, la prima volta che un oggetto enumerabile `GetEnumerator` metodo viene richiamato, viene restituito l'oggetto enumerabile stesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1638">In that type of implementation, the first time an enumerable object's `GetEnumerator` method is invoked, the enumerable object itself is returned.</span></span> <span data-ttu-id="5bc16-1639">Le chiamate successive degli oggetti enumerabili `GetEnumerator`, se presente, restituisce una copia dell'oggetto enumerabile.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1639">Subsequent invocations of the enumerable object's `GetEnumerator`, if any, return a copy of the enumerable object.</span></span> <span data-ttu-id="5bc16-1640">Di conseguenza, ogni oggetto restituito enumeratore ha il proprio stato e le modifiche in un enumeratore non influiranno un'altra.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1640">Thus, each returned enumerator has its own state and changes in one enumerator will not affect another.</span></span>

#### <a name="the-getenumerator-method"></a><span data-ttu-id="5bc16-1641">Metodo GetEnumerator</span><span class="sxs-lookup"><span data-stu-id="5bc16-1641">The GetEnumerator method</span></span>

<span data-ttu-id="5bc16-1642">Oggetto enumerabile fornisce un'implementazione del `GetEnumerator` metodi del `IEnumerable` e `IEnumerable<T>` interfacce.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1642">An enumerable object provides an implementation of the `GetEnumerator` methods of the `IEnumerable` and `IEnumerable<T>` interfaces.</span></span> <span data-ttu-id="5bc16-1643">I due `GetEnumerator` metodi condividono un'implementazione comune che consente di acquisire e restituisce un oggetto enumeratore disponibili.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1643">The two `GetEnumerator` methods share a common implementation that acquires and returns an available enumerator object.</span></span> <span data-ttu-id="5bc16-1644">Oggetto enumeratore viene inizializzata con i valori degli argomenti e l'istanza valore salvato quando l'oggetto enumerabile è stato inizializzato, ma in caso contrario, il resto funziona come descritto in [oggetti dell'enumeratore](classes.md#enumerator-objects).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1644">The enumerator object is initialized with the argument values and instance value saved when the enumerable object was initialized, but otherwise the enumerator object functions as described in [Enumerator objects](classes.md#enumerator-objects).</span></span>

### <a name="implementation-example"></a><span data-ttu-id="5bc16-1645">Esempio di implementazione</span><span class="sxs-lookup"><span data-stu-id="5bc16-1645">Implementation example</span></span>

<span data-ttu-id="5bc16-1646">Questa sezione illustra una possibile implementazione degli iteratori in termini di costrutti c# standard.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1646">This section describes a possible implementation of iterators in terms of standard C# constructs.</span></span> <span data-ttu-id="5bc16-1647">L'implementazione descritta di seguito si basa sugli stessi principi usati dal compilatore Microsoft C#, ma è in alcun modo un'implementazione obbligatoria o i possibili solo una.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1647">The implementation described here is based on the same principles used by the Microsoft C# compiler, but it is by no means a mandated implementation or the only one possible.</span></span>

<span data-ttu-id="5bc16-1648">Quanto segue `Stack<T>` classe implementa il `GetEnumerator` metodo mediante un iteratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1648">The following `Stack<T>` class implements its `GetEnumerator` method using an iterator.</span></span> <span data-ttu-id="5bc16-1649">L'iteratore enumera gli elementi dello stack dall'alto verso il basso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1649">The iterator enumerates the elements of the stack in top to bottom order.</span></span>

```csharp
using System;
using System.Collections;
using System.Collections.Generic;

class Stack<T>: IEnumerable<T>
{
    T[] items;
    int count;

    public void Push(T item) {
        if (items == null) {
            items = new T[4];
        }
        else if (items.Length == count) {
            T[] newItems = new T[count * 2];
            Array.Copy(items, 0, newItems, 0, count);
            items = newItems;
        }
        items[count++] = item;
    }

    public T Pop() {
        T result = items[--count];
        items[count] = default(T);
        return result;
    }

    public IEnumerator<T> GetEnumerator() {
        for (int i = count - 1; i >= 0; --i) yield return items[i];
    }
}
```

<span data-ttu-id="5bc16-1650">Il `GetEnumerator` metodo può essere convertito in un'istanza di una classe enumerator generato dal compilatore che incapsula il codice nel blocco iteratore, come illustrato nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1650">The `GetEnumerator` method can be translated into an instantiation of a compiler-generated enumerator class that encapsulates the code in the iterator block, as shown in the following.</span></span>

```csharp
class Stack<T>: IEnumerable<T>
{
    ...

    public IEnumerator<T> GetEnumerator() {
        return new __Enumerator1(this);
    }

    class __Enumerator1: IEnumerator<T>, IEnumerator
    {
        int __state;
        T __current;
        Stack<T> __this;
        int i;

        public __Enumerator1(Stack<T> __this) {
            this.__this = __this;
        }

        public T Current {
            get { return __current; }
        }

        object IEnumerator.Current {
            get { return __current; }
        }

        public bool MoveNext() {
            switch (__state) {
                case 1: goto __state1;
                case 2: goto __state2;
            }
            i = __this.count - 1;
        __loop:
            if (i < 0) goto __state2;
            __current = __this.items[i];
            __state = 1;
            return true;
        __state1:
            --i;
            goto __loop;
        __state2:
            __state = 2;
            return false;
        }

        public void Dispose() {
            __state = 2;
        }

        void IEnumerator.Reset() {
            throw new NotSupportedException();
        }
    }
}
```

<span data-ttu-id="5bc16-1651">Nella conversione precedente, il codice nel blocco iteratore viene trasformato in una macchina a stati e inserito nel `MoveNext` metodo della classe dell'enumeratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1651">In the preceding translation, the code in the iterator block is turned into a state machine and placed in the `MoveNext` method of the enumerator class.</span></span> <span data-ttu-id="5bc16-1652">Inoltre, la variabile locale `i` viene trasformato in un campo dell'oggetto enumeratore per continuare a esistere tra le chiamate di `MoveNext`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1652">Furthermore, the local variable `i` is turned into a field in the enumerator object so it can continue to exist across invocations of `MoveNext`.</span></span>

<span data-ttu-id="5bc16-1653">L'esempio seguente visualizza una semplice tabella moltiplicazione dei numeri interi da 1 a 10.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1653">The following example prints a simple multiplication table of the integers 1 through 10.</span></span> <span data-ttu-id="5bc16-1654">Il `FromTo` metodo nell'esempio restituisce un oggetto enumerabile e viene implementata utilizzando un iteratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1654">The `FromTo` method in the example returns an enumerable object and is implemented using an iterator.</span></span>

```csharp
using System;
using System.Collections.Generic;

class Test
{
    static IEnumerable<int> FromTo(int from, int to) {
        while (from <= to) yield return from++;
    }

    static void Main() {
        IEnumerable<int> e = FromTo(1, 10);
        foreach (int x in e) {
            foreach (int y in e) {
                Console.Write("{0,3} ", x * y);
            }
            Console.WriteLine();
        }
    }
}
```

<span data-ttu-id="5bc16-1655">Il `FromTo` metodo può essere convertito in un'istanza della classe enumerable generato dal compilatore che incapsula il codice nel blocco iteratore, come illustrato nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1655">The `FromTo` method can be translated into an instantiation of a compiler-generated enumerable class that encapsulates the code in the iterator block, as shown in the following.</span></span>

```csharp
using System;
using System.Threading;
using System.Collections;
using System.Collections.Generic;

class Test
{
    ...

    static IEnumerable<int> FromTo(int from, int to) {
        return new __Enumerable1(from, to);
    }

    class __Enumerable1:
        IEnumerable<int>, IEnumerable,
        IEnumerator<int>, IEnumerator
    {
        int __state;
        int __current;
        int __from;
        int from;
        int to;
        int i;

        public __Enumerable1(int __from, int to) {
            this.__from = __from;
            this.to = to;
        }

        public IEnumerator<int> GetEnumerator() {
            __Enumerable1 result = this;
            if (Interlocked.CompareExchange(ref __state, 1, 0) != 0) {
                result = new __Enumerable1(__from, to);
                result.__state = 1;
            }
            result.from = result.__from;
            return result;
        }

        IEnumerator IEnumerable.GetEnumerator() {
            return (IEnumerator)GetEnumerator();
        }

        public int Current {
            get { return __current; }
        }

        object IEnumerator.Current {
            get { return __current; }
        }

        public bool MoveNext() {
            switch (__state) {
            case 1:
                if (from > to) goto case 2;
                __current = from++;
                __state = 1;
                return true;
            case 2:
                __state = 2;
                return false;
            default:
                throw new InvalidOperationException();
            }
        }

        public void Dispose() {
            __state = 2;
        }

        void IEnumerator.Reset() {
            throw new NotSupportedException();
        }
    }
}
```

<span data-ttu-id="5bc16-1656">La classe enumerable implementa le interfacce enumerabili sia le interfacce enumeratore, in modo da poter essere utilizzato come un oggetto enumerabile sia un enumeratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1656">The enumerable class implements both the enumerable interfaces and the enumerator interfaces, enabling it to serve as both an enumerable and an enumerator.</span></span> <span data-ttu-id="5bc16-1657">La prima volta il `GetEnumerator` metodo viene richiamato, viene restituito l'oggetto enumerabile stesso.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1657">The first time the `GetEnumerator` method is invoked, the enumerable object itself is returned.</span></span> <span data-ttu-id="5bc16-1658">Le chiamate successive degli oggetti enumerabili `GetEnumerator`, se presente, restituisce una copia dell'oggetto enumerabile.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1658">Subsequent invocations of the enumerable object's `GetEnumerator`, if any, return a copy of the enumerable object.</span></span> <span data-ttu-id="5bc16-1659">Di conseguenza, ogni oggetto restituito enumeratore ha il proprio stato e le modifiche in un enumeratore non influiranno un'altra.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1659">Thus, each returned enumerator has its own state and changes in one enumerator will not affect another.</span></span> <span data-ttu-id="5bc16-1660">Il `Interlocked.CompareExchange` metodo viene utilizzato per garantire operazioni thread-safe.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1660">The `Interlocked.CompareExchange` method is used to ensure thread-safe operation.</span></span>

<span data-ttu-id="5bc16-1661">Il `from` e `to` parametri vengono convertiti in campi della classe enumerable.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1661">The `from` and `to` parameters are turned into fields in the enumerable class.</span></span> <span data-ttu-id="5bc16-1662">In quanto `from` viene modificato nel blocco iteratore, un'ulteriore `__from` campo è stato introdotto per contenere il valore iniziale assegnato `from` in ogni enumeratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1662">Because `from` is modified in the iterator block, an additional `__from` field is introduced to hold the initial value given to `from` in each enumerator.</span></span>

<span data-ttu-id="5bc16-1663">Il `MoveNext` metodo genera un' `InvalidOperationException` se viene chiamato quando `__state` è `0`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1663">The `MoveNext` method throws an `InvalidOperationException` if it is called when `__state` is `0`.</span></span> <span data-ttu-id="5bc16-1664">Ciò consente di evitare l'utilizzo dell'oggetto enumerabile come un oggetto enumeratore senza prima chiamare `GetEnumerator`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1664">This protects against use of the enumerable object as an enumerator object without first calling `GetEnumerator`.</span></span>

<span data-ttu-id="5bc16-1665">Nell'esempio seguente viene illustrata una classe di albero semplice.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1665">The following example shows a simple tree class.</span></span> <span data-ttu-id="5bc16-1666">Il `Tree<T>` classe implementa il `GetEnumerator` metodo mediante un iteratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1666">The `Tree<T>` class implements its `GetEnumerator` method using an iterator.</span></span> <span data-ttu-id="5bc16-1667">L'iteratore enumera gli elementi dell'albero in ordine di infissi.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1667">The iterator enumerates the elements of the tree in infix order.</span></span>

```csharp
using System;
using System.Collections.Generic;

class Tree<T>: IEnumerable<T>
{
    T value;
    Tree<T> left;
    Tree<T> right;

    public Tree(T value, Tree<T> left, Tree<T> right) {
        this.value = value;
        this.left = left;
        this.right = right;
    }

    public IEnumerator<T> GetEnumerator() {
        if (left != null) foreach (T x in left) yield x;
        yield value;
        if (right != null) foreach (T x in right) yield x;
    }
}

class Program
{
    static Tree<T> MakeTree<T>(T[] items, int left, int right) {
        if (left > right) return null;
        int i = (left + right) / 2;
        return new Tree<T>(items[i], 
            MakeTree(items, left, i - 1),
            MakeTree(items, i + 1, right));
    }

    static Tree<T> MakeTree<T>(params T[] items) {
        return MakeTree(items, 0, items.Length - 1);
    }

    // The output of the program is:
    // 1 2 3 4 5 6 7 8 9
    // Mon Tue Wed Thu Fri Sat Sun

    static void Main() {
        Tree<int> ints = MakeTree(1, 2, 3, 4, 5, 6, 7, 8, 9);
        foreach (int i in ints) Console.Write("{0} ", i);
        Console.WriteLine();

        Tree<string> strings = MakeTree(
            "Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun");
        foreach (string s in strings) Console.Write("{0} ", s);
        Console.WriteLine();
    }
}
```

<span data-ttu-id="5bc16-1668">Il `GetEnumerator` metodo può essere convertito in un'istanza di una classe enumerator generato dal compilatore che incapsula il codice nel blocco iteratore, come illustrato nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1668">The `GetEnumerator` method can be translated into an instantiation of a compiler-generated enumerator class that encapsulates the code in the iterator block, as shown in the following.</span></span>

```csharp
class Tree<T>: IEnumerable<T>
{
    ...

    public IEnumerator<T> GetEnumerator() {
        return new __Enumerator1(this);
    }

    class __Enumerator1 : IEnumerator<T>, IEnumerator
    {
        Node<T> __this;
        IEnumerator<T> __left, __right;
        int __state;
        T __current;

        public __Enumerator1(Node<T> __this) {
            this.__this = __this;
        }

        public T Current {
            get { return __current; }
        }

        object IEnumerator.Current {
            get { return __current; }
        }

        public bool MoveNext() {
            try {
                switch (__state) {

                case 0:
                    __state = -1;
                    if (__this.left == null) goto __yield_value;
                    __left = __this.left.GetEnumerator();
                    goto case 1;

                case 1:
                    __state = -2;
                    if (!__left.MoveNext()) goto __left_dispose;
                    __current = __left.Current;
                    __state = 1;
                    return true;

                __left_dispose:
                    __state = -1;
                    __left.Dispose();

                __yield_value:
                    __current = __this.value;
                    __state = 2;
                    return true;

                case 2:
                    __state = -1;
                    if (__this.right == null) goto __end;
                    __right = __this.right.GetEnumerator();
                    goto case 3;

                case 3:
                    __state = -3;
                    if (!__right.MoveNext()) goto __right_dispose;
                    __current = __right.Current;
                    __state = 3;
                    return true;

                __right_dispose:
                    __state = -1;
                    __right.Dispose();

                __end:
                    __state = 4;
                    break;

                }
            }
            finally {
                if (__state < 0) Dispose();
            }
            return false;
        }

        public void Dispose() {
            try {
                switch (__state) {

                case 1:
                case -2:
                    __left.Dispose();
                    break;

                case 3:
                case -3:
                    __right.Dispose();
                    break;

                }
            }
            finally {
                __state = 4;
            }
        }

        void IEnumerator.Reset() {
            throw new NotSupportedException();
        }
    }
}
```

<span data-ttu-id="5bc16-1669">I temporanei generati dal compilatore usati nel `foreach` istruzioni vengono eseguito il lift nel `__left` e `__right` dei campi dell'oggetto enumeratore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1669">The compiler generated temporaries used in the `foreach` statements are lifted into the `__left` and `__right` fields of the enumerator object.</span></span> <span data-ttu-id="5bc16-1670">Il `__state` campo dell'oggetto enumeratore con attenzione viene aggiornato in modo che i valori corretti `Dispose()` metodo verrà chiamato correttamente se viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1670">The `__state` field of the enumerator object is carefully updated so that the correct `Dispose()` method will be called correctly if an exception is thrown.</span></span> <span data-ttu-id="5bc16-1671">Si noti che non è possibile scrivere il codice tradotto con semplice `foreach` istruzioni.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1671">Note that it is not possible to write the translated code with simple `foreach` statements.</span></span>

## <a name="async-functions"></a><span data-ttu-id="5bc16-1672">Funzioni asincrone</span><span class="sxs-lookup"><span data-stu-id="5bc16-1672">Async functions</span></span>

<span data-ttu-id="5bc16-1673">Un metodo ([metodi](classes.md#methods)) o una funzione anonima ([espressioni di funzioni anonime](expressions.md#anonymous-function-expressions)) con i `async` modificatore viene chiamato un ***funzione async***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1673">A method ([Methods](classes.md#methods)) or anonymous function ([Anonymous function expressions](expressions.md#anonymous-function-expressions)) with the `async` modifier is called an ***async function***.</span></span> <span data-ttu-id="5bc16-1674">In generale, il termine ***async*** viene usato per descrivere qualsiasi tipo di funzione che ha il `async` modificatore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1674">In general, the term ***async*** is used to describe any kind of function that has the `async` modifier.</span></span>

<span data-ttu-id="5bc16-1675">È un errore in fase di compilazione per l'elenco di parametri formali di una funzione asincrona per specificare eventuali `ref` o `out` parametri.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1675">It is a compile-time error for the formal parameter list of an async function to specify any `ref` or `out` parameters.</span></span>

<span data-ttu-id="5bc16-1676">Il *return_type* di un asincrono metodo deve essere `void` o una ***tipo di attività***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1676">The *return_type* of an async method must be either `void` or a ***task type***.</span></span> <span data-ttu-id="5bc16-1677">I tipi di attività sono `System.Threading.Tasks.Task` e i tipi costruiti da `System.Threading.Tasks.Task<T>`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1677">The task types are `System.Threading.Tasks.Task` and types constructed from `System.Threading.Tasks.Task<T>`.</span></span> <span data-ttu-id="5bc16-1678">Per ragioni di brevità, in questo capitolo questi tipi viene fatto riferimento come `Task` e `Task<T>`, rispettivamente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1678">For the sake of brevity, in this chapter these types are referenced as `Task` and `Task<T>`, respectively.</span></span> <span data-ttu-id="5bc16-1679">Un metodo asincrono che restituisce un tipo di attività è detto restituzione di task.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1679">An async method returning a task type is said to be task-returning.</span></span>

<span data-ttu-id="5bc16-1680">La definizione esatta dei tipi di attività non è definito dall'implementazione, ma dal punto di vista del linguaggio è un tipo di attività in uno stato incompleto, ha avuto esito positivo o faulted.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1680">The exact definition of the task types is implementation defined, but from the language's point of view a task type is in one of the states incomplete, succeeded or faulted.</span></span> <span data-ttu-id="5bc16-1681">Un'attività con errori registra un'eccezione pertinente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1681">A faulted task records a pertinent exception.</span></span> <span data-ttu-id="5bc16-1682">Una completato `Task<T>` registra un risultato di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1682">A succeeded `Task<T>` records a result of type `T`.</span></span> <span data-ttu-id="5bc16-1683">I tipi di attività sono awaitable e quindi possono essere gli operandi di espressioni await ([espressioni Await](expressions.md#await-expressions)).</span><span class="sxs-lookup"><span data-stu-id="5bc16-1683">Task types are awaitable, and can therefore be the operands of await expressions ([Await expressions](expressions.md#await-expressions)).</span></span>

<span data-ttu-id="5bc16-1684">Una chiamata di funzione asincrona ha la possibilità di sospendere la valutazione di espressioni await ([espressioni Await](expressions.md#await-expressions)) nel relativo corpo.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1684">An async function invocation has the ability to suspend evaluation by means of await expressions ([Await expressions](expressions.md#await-expressions)) in its body.</span></span> <span data-ttu-id="5bc16-1685">Valutazione in un secondo momento possa essere ripresi nel punto di sospensione per mezzo dell'espressione await una ***delegato ripresa***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1685">Evaluation may later be resumed at the point of the suspending await expression by means of a ***resumption delegate***.</span></span> <span data-ttu-id="5bc16-1686">Il delegato di ripresa è di tipo `System.Action`, e quando viene richiamato, valutazione della chiamata di funzione async verrà ripreso dall'espressione await in cui è stata interrotta.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1686">The resumption delegate is of type `System.Action`, and when it is invoked, evaluation of the async function invocation will resume from the await expression where it left off.</span></span> <span data-ttu-id="5bc16-1687">Il ***chiamante corrente*** di una funzione async chiamata è in caso contrario, il chiamante originale se la chiamata alla funzione non è mai stata sospesa o il più recente chiamante del delegato di ripresa.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1687">The ***current caller*** of an async function invocation is the original caller if the function invocation has never been suspended, or the most recent caller of the resumption delegate otherwise.</span></span>

### <a name="evaluation-of-a-task-returning-async-function"></a><span data-ttu-id="5bc16-1688">Valutazione di una funzione async restituzione di task</span><span class="sxs-lookup"><span data-stu-id="5bc16-1688">Evaluation of a task-returning async function</span></span>

<span data-ttu-id="5bc16-1689">Chiamata di una funzione asincrona che restituisce attività fa sì che un'istanza del tipo di attività restituita da generare.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1689">Invocation of a task-returning async function causes an instance of the returned task type to be generated.</span></span> <span data-ttu-id="5bc16-1690">Questa operazione viene definita la ***Restituisci task*** della funzione async.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1690">This is called the ***return task*** of the async function.</span></span> <span data-ttu-id="5bc16-1691">L'attività è inizialmente in uno stato incompleto.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1691">The task is initially in an incomplete state.</span></span>

<span data-ttu-id="5bc16-1692">Il corpo della funzione async viene quindi valutato fino a quando non è sospeso (dal raggiungimento di un'espressione await) o viene terminato, in corrispondenza del quale punto di controllo viene restituito al chiamante, contestualmente all'attività restituito.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1692">The async function body is then evaluated until it is either suspended (by reaching an await expression) or terminates, at which point control is returned to the caller, along with the return task.</span></span>

<span data-ttu-id="5bc16-1693">Quando termina, il corpo della funzione async viene restituito viene spostato lo stato incompleto:</span><span class="sxs-lookup"><span data-stu-id="5bc16-1693">When the body of the async function terminates, the return task is moved out of the incomplete state:</span></span>

*  <span data-ttu-id="5bc16-1694">Se il corpo della funzione termina come risultato di raggiungere un'istruzione return o alla fine del corpo, qualsiasi valore del risultato viene registrata nell'attività restituito, che viene inserito in un stato completato.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1694">If the function body terminates as the result of reaching a return statement or the end of the body, any result value is recorded in the return task, which is put into a succeeded state.</span></span>
*  <span data-ttu-id="5bc16-1695">Se il corpo della funzione termina come risultato di un'eccezione non rilevata ([l'istruzione throw](statements.md#the-throw-statement)) l'eccezione viene registrata nell'attività restituito che viene inserito in uno stato di errore.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1695">If the function body terminates as the result of an uncaught exception ([The throw statement](statements.md#the-throw-statement)) the exception is recorded in the return task which is put into a faulted state.</span></span>

### <a name="evaluation-of-a-void-returning-async-function"></a><span data-ttu-id="5bc16-1696">Valutazione di una funzione asincrona che restituisce void</span><span class="sxs-lookup"><span data-stu-id="5bc16-1696">Evaluation of a void-returning async function</span></span>

<span data-ttu-id="5bc16-1697">Se il tipo restituito della funzione async è `void`, valutazione è diverso da quanto sopra nel modo seguente: Poiché non viene restituita alcuna attività, la funzione comunica invece completamento ed eccezioni nel thread corrente ***contesto di sincronizzazione***.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1697">If the return type of the async function is `void`, evaluation differs from the above in the following way: Because no task is returned, the function instead communicates completion and exceptions to the current thread's ***synchronization context***.</span></span> <span data-ttu-id="5bc16-1698">La definizione esatta del contesto di sincronizzazione è dipendente dall'implementazione, ma è una rappresentazione in forma di "where" è in esecuzione il thread corrente.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1698">The exact definition of synchronization context is implementation-dependent, but is a representation of "where" the current thread is running.</span></span> <span data-ttu-id="5bc16-1699">Il contesto di sincronizzazione viene notificato quando la valutazione di una funzione asincrona che restituisce void avvia, viene completata correttamente o genera un'eccezione non rilevata eccezione.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1699">The synchronization context is notified when evaluation of a void-returning async function commences, completes successfully, or causes an uncaught exception to be thrown.</span></span>

<span data-ttu-id="5bc16-1700">In questo modo il contesto di tenere traccia di quante funzioni asincrone che restituiscono void sono in esecuzione e decidere come propagare le eccezioni provenienti da esse.</span><span class="sxs-lookup"><span data-stu-id="5bc16-1700">This allows the context to keep track of how many void-returning async functions are running under it, and to decide how to propagate exceptions coming out of them.</span></span>
