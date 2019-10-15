---
ms.openlocfilehash: e0def754174ab8646f9b849abe86d2c375c958b6
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703974"
---
# <a name="classes"></a><span data-ttu-id="467ae-101">Classi</span><span class="sxs-lookup"><span data-stu-id="467ae-101">Classes</span></span>

<span data-ttu-id="467ae-102">Una classe è una struttura di dati che può contenere membri dati (costanti e campi), membri di funzione (metodi, proprietà, eventi, indicizzatori, operatori, costruttori di istanze, distruttori e costruttori statici) e tipi annidati.</span><span class="sxs-lookup"><span data-stu-id="467ae-102">A class is a data structure that may contain data members (constants and fields), function members (methods, properties, events, indexers, operators, instance constructors, destructors and static constructors), and nested types.</span></span> <span data-ttu-id="467ae-103">I tipi di classe supportano l'ereditarietà, un meccanismo in base al quale una classe derivata può estendere ed specializzare una classe base.</span><span class="sxs-lookup"><span data-stu-id="467ae-103">Class types support inheritance, a mechanism whereby a derived class can extend and specialize a base class.</span></span>

## <a name="class-declarations"></a><span data-ttu-id="467ae-104">Dichiarazioni di classe</span><span class="sxs-lookup"><span data-stu-id="467ae-104">Class declarations</span></span>

<span data-ttu-id="467ae-105">Un *class_declaration* è un *type_declaration* ([dichiarazioni di tipo](namespaces.md#type-declarations)) che dichiara una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-105">A *class_declaration* is a *type_declaration* ([Type declarations](namespaces.md#type-declarations)) that declares a new class.</span></span>

```antlr
class_declaration
    : attributes? class_modifier* 'partial'? 'class' identifier type_parameter_list?
      class_base? type_parameter_constraints_clause* class_body ';'?
    ;
```

<span data-ttu-id="467ae-106">Un *class_declaration* è costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), seguito da un set facoltativo di *class_modifier*s ([modificatori di classe](classes.md#class-modifiers)), seguito da un modificatore `partial` facoltativo, seguito dalla parola chiave `class` e un *identificatore* che denomina la classe, seguito da un *type_parameter_list* facoltativo ([parametri di tipo](classes.md#type-parameters)), seguito da una specifica *class_base* facoltativa (classe di[base della classe](classes.md#class-base-specification)), seguita da set facoltativo di *type_parameter_constraints_clause*s ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)), seguito da un *class_body* ([corpo della classe](classes.md#class-body)), facoltativamente seguito da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="467ae-106">A *class_declaration* consists of an optional set of *attributes* ([Attributes](attributes.md)), followed by an optional set of *class_modifier*s ([Class modifiers](classes.md#class-modifiers)), followed by an optional `partial` modifier, followed by the keyword `class` and an *identifier* that names the class, followed by an optional *type_parameter_list* ([Type parameters](classes.md#type-parameters)), followed by an optional *class_base* specification ([Class base specification](classes.md#class-base-specification)) , followed by an optional set of *type_parameter_constraints_clause*s ([Type parameter constraints](classes.md#type-parameter-constraints)), followed by a *class_body* ([Class body](classes.md#class-body)), optionally followed by a semicolon.</span></span>

<span data-ttu-id="467ae-107">Una dichiarazione di classe non può fornire *type_parameter_constraints_clause*s a meno che non fornisca anche un *type_parameter_list*.</span><span class="sxs-lookup"><span data-stu-id="467ae-107">A class declaration cannot supply *type_parameter_constraints_clause*s unless it also supplies a *type_parameter_list*.</span></span>

<span data-ttu-id="467ae-108">Una dichiarazione di classe che fornisce un *type_parameter_list* è una ***dichiarazione di classe generica***.</span><span class="sxs-lookup"><span data-stu-id="467ae-108">A class declaration that supplies a *type_parameter_list* is a ***generic class declaration***.</span></span> <span data-ttu-id="467ae-109">Inoltre, qualsiasi classe annidata all'interno di una dichiarazione di classe generica o di una dichiarazione di struct generica è a sua volta una dichiarazione di classe generica, poiché è necessario fornire i parametri di tipo per il tipo contenitore per creare un tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="467ae-109">Additionally, any class nested inside a generic class declaration or a generic struct declaration is itself a generic class declaration, since type parameters for the containing type must be supplied to create a constructed type.</span></span>

### <a name="class-modifiers"></a><span data-ttu-id="467ae-110">Modificatori di classe</span><span class="sxs-lookup"><span data-stu-id="467ae-110">Class modifiers</span></span>

<span data-ttu-id="467ae-111">Un *class_declaration* può facoltativamente includere una sequenza di modificatori di classe:</span><span class="sxs-lookup"><span data-stu-id="467ae-111">A *class_declaration* may optionally include a sequence of class modifiers:</span></span>

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

<span data-ttu-id="467ae-112">Si tratta di un errore in fase di compilazione perché lo stesso modificatore venga visualizzato più volte in una dichiarazione di classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-112">It is a compile-time error for the same modifier to appear multiple times in a class declaration.</span></span>

<span data-ttu-id="467ae-113">Il `new` modificatore è consentito nelle classi annidate.</span><span class="sxs-lookup"><span data-stu-id="467ae-113">The `new` modifier is permitted on nested classes.</span></span> <span data-ttu-id="467ae-114">Specifica che la classe nasconde un membro ereditato con lo stesso nome, come descritto nel [nuovo modificatore](classes.md#the-new-modifier).</span><span class="sxs-lookup"><span data-stu-id="467ae-114">It specifies that the class hides an inherited member by the same name, as described in [The new modifier](classes.md#the-new-modifier).</span></span> <span data-ttu-id="467ae-115">Si tratta di un errore in fase di compilazione `new` perché il modificatore venga visualizzato in una dichiarazione di classe che non è una dichiarazione di classe annidata.</span><span class="sxs-lookup"><span data-stu-id="467ae-115">It is a compile-time error for the `new` modifier to appear on a class declaration that is not a nested class declaration.</span></span>

<span data-ttu-id="467ae-116">I `public`modificatori `internal`, `protected`, e`private` controllano l'accessibilità della classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-116">The `public`, `protected`, `internal`, and `private` modifiers control the accessibility of the class.</span></span> <span data-ttu-id="467ae-117">A seconda del contesto in cui si verifica la dichiarazione di classe, alcuni modificatori potrebbero non essere consentiti ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)).</span><span class="sxs-lookup"><span data-stu-id="467ae-117">Depending on the context in which the class declaration occurs, some of these modifiers may not be permitted ([Declared accessibility](basic-concepts.md#declared-accessibility)).</span></span>

<span data-ttu-id="467ae-118">I `abstract`modificatori `static` , `sealed` e sono descritti nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="467ae-118">The `abstract`, `sealed` and `static` modifiers are discussed in the following sections.</span></span>

#### <a name="abstract-classes"></a><span data-ttu-id="467ae-119">Classi astratte</span><span class="sxs-lookup"><span data-stu-id="467ae-119">Abstract classes</span></span>

<span data-ttu-id="467ae-120">Il `abstract` modificatore viene usato per indicare che una classe è incompleta e che deve essere usata solo come classe di base.</span><span class="sxs-lookup"><span data-stu-id="467ae-120">The `abstract` modifier is used to indicate that a class is incomplete and that it is intended to be used only as a base class.</span></span> <span data-ttu-id="467ae-121">Una classe astratta differisce da una classe non astratta nei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-121">An abstract class differs from a non-abstract class in the following ways:</span></span>

*  <span data-ttu-id="467ae-122">Non è possibile creare direttamente un'istanza di una classe astratta e si tratta di un errore in fase di `new` compilazione per utilizzare l'operatore in una classe astratta.</span><span class="sxs-lookup"><span data-stu-id="467ae-122">An abstract class cannot be instantiated directly, and it is a compile-time error to use the `new` operator on an abstract class.</span></span> <span data-ttu-id="467ae-123">Sebbene sia possibile avere variabili e valori i cui tipi in fase di compilazione sono astratti, tali variabili e valori dovranno necessariamente essere `null` o contenere riferimenti a istanze di classi non astratte derivate dai tipi astratti.</span><span class="sxs-lookup"><span data-stu-id="467ae-123">While it is possible to have variables and values whose compile-time types are abstract, such variables and values will necessarily either be `null` or contain references to instances of non-abstract classes derived from the abstract types.</span></span>
*  <span data-ttu-id="467ae-124">Una classe astratta è consentita (ma non obbligatoria) per contenere membri astratti.</span><span class="sxs-lookup"><span data-stu-id="467ae-124">An abstract class is permitted (but not required) to contain abstract members.</span></span>
*  <span data-ttu-id="467ae-125">Una classe astratta non può essere sealed.</span><span class="sxs-lookup"><span data-stu-id="467ae-125">An abstract class cannot be sealed.</span></span>

<span data-ttu-id="467ae-126">Quando una classe non astratta viene derivata da una classe astratta, la classe non astratta deve includere implementazioni effettive di tutti i membri astratti ereditati, ignorando in tal modo i membri astratti.</span><span class="sxs-lookup"><span data-stu-id="467ae-126">When a non-abstract class is derived from an abstract class, the non-abstract class must include actual implementations of all inherited abstract members, thereby overriding those abstract members.</span></span> <span data-ttu-id="467ae-127">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-127">In the example</span></span>
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
<span data-ttu-id="467ae-128">la classe `A` astratta introduce un metodo `F`astratto.</span><span class="sxs-lookup"><span data-stu-id="467ae-128">the abstract class `A` introduces an abstract method `F`.</span></span> <span data-ttu-id="467ae-129">La `B` classe introduce un metodo `G`aggiuntivo, ma poiché non fornisce un'implementazione di `F`, `B` deve essere dichiarato anche abstract.</span><span class="sxs-lookup"><span data-stu-id="467ae-129">Class `B` introduces an additional method `G`, but since it doesn't provide an implementation of `F`, `B` must also be declared abstract.</span></span> <span data-ttu-id="467ae-130">La `C` classe `F` esegue l'override di e fornisce un'implementazione effettiva.</span><span class="sxs-lookup"><span data-stu-id="467ae-130">Class `C` overrides `F` and provides an actual implementation.</span></span> <span data-ttu-id="467ae-131">Poiché non sono presenti membri astratti `C`in `C` , è consentito (ma non obbligatorio) non essere astratto.</span><span class="sxs-lookup"><span data-stu-id="467ae-131">Since there are no abstract members in `C`, `C` is permitted (but not required) to be non-abstract.</span></span>

#### <a name="sealed-classes"></a><span data-ttu-id="467ae-132">Classi sealed</span><span class="sxs-lookup"><span data-stu-id="467ae-132">Sealed classes</span></span>

<span data-ttu-id="467ae-133">Il `sealed` modificatore viene usato per impedire la derivazione da una classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-133">The `sealed` modifier is used to prevent derivation from a class.</span></span> <span data-ttu-id="467ae-134">Si verifica un errore in fase di compilazione se viene specificata una classe sealed come classe base di un'altra classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-134">A compile-time error occurs if a sealed class is specified as the base class of another class.</span></span>

<span data-ttu-id="467ae-135">Una classe sealed non può essere anche una classe astratta.</span><span class="sxs-lookup"><span data-stu-id="467ae-135">A sealed class cannot also be an abstract class.</span></span>

<span data-ttu-id="467ae-136">Il `sealed` modificatore viene utilizzato principalmente per evitare derivazioni indesiderate, ma consente anche determinate ottimizzazioni in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="467ae-136">The `sealed` modifier is primarily used to prevent unintended derivation, but it also enables certain run-time optimizations.</span></span> <span data-ttu-id="467ae-137">In particolare, poiché una classe sealed non dispone mai di classi derivate, è possibile trasformare le chiamate del membro della funzione virtuale in istanze di classe sealed in chiamate non virtuali.</span><span class="sxs-lookup"><span data-stu-id="467ae-137">In particular, because a sealed class is known to never have any derived classes, it is possible to transform virtual function member invocations on sealed class instances into non-virtual invocations.</span></span>

#### <a name="static-classes"></a><span data-ttu-id="467ae-138">Classi statiche</span><span class="sxs-lookup"><span data-stu-id="467ae-138">Static classes</span></span>

<span data-ttu-id="467ae-139">Il `static` modificatore viene usato per contrassegnare la classe dichiarata come ***classe statica***.</span><span class="sxs-lookup"><span data-stu-id="467ae-139">The `static` modifier is used to mark the class being declared as a ***static class***.</span></span> <span data-ttu-id="467ae-140">Non è possibile creare un'istanza di una classe statica. non può essere usata come tipo e può contenere solo membri statici.</span><span class="sxs-lookup"><span data-stu-id="467ae-140">A static class cannot be instantiated, cannot be used as a type and can contain only static members.</span></span> <span data-ttu-id="467ae-141">Solo una classe statica può contenere dichiarazioni di metodi di estensione ([metodi di estensione](classes.md#extension-methods)).</span><span class="sxs-lookup"><span data-stu-id="467ae-141">Only a static class can contain declarations of extension methods ([Extension methods](classes.md#extension-methods)).</span></span>

<span data-ttu-id="467ae-142">Una dichiarazione di classe statica è soggetta alle restrizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-142">A static class declaration is subject to the following restrictions:</span></span>

*  <span data-ttu-id="467ae-143">Una classe statica non può includere un `sealed` modificatore o `abstract` .</span><span class="sxs-lookup"><span data-stu-id="467ae-143">A static class may not include a `sealed` or `abstract` modifier.</span></span> <span data-ttu-id="467ae-144">Si noti, tuttavia, che poiché una classe statica non può essere creata o derivata da, si comporta come se fosse sealed e abstract.</span><span class="sxs-lookup"><span data-stu-id="467ae-144">Note, however, that since a static class cannot be instantiated or derived from, it behaves as if it was both sealed and abstract.</span></span>
*  <span data-ttu-id="467ae-145">Una classe statica non può includere una specifica *class_base* ([specifica di base della classe](classes.md#class-base-specification)) e non può specificare in modo esplicito una classe base o un elenco di interfacce implementate.</span><span class="sxs-lookup"><span data-stu-id="467ae-145">A static class may not include a *class_base* specification ([Class base specification](classes.md#class-base-specification)) and cannot explicitly specify a base class or a list of implemented interfaces.</span></span> <span data-ttu-id="467ae-146">Una classe statica eredita in modo implicito `object`dal tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-146">A static class implicitly inherits from type `object`.</span></span>
*  <span data-ttu-id="467ae-147">Una classe statica può contenere solo membri statici ([membri statici e di istanza](classes.md#static-and-instance-members)).</span><span class="sxs-lookup"><span data-stu-id="467ae-147">A static class can only contain static members ([Static and instance members](classes.md#static-and-instance-members)).</span></span> <span data-ttu-id="467ae-148">Si noti che le costanti e i tipi annidati sono classificati come membri statici.</span><span class="sxs-lookup"><span data-stu-id="467ae-148">Note that constants and nested types are classified as static members.</span></span>
*  <span data-ttu-id="467ae-149">Una classe statica non può avere membri `protected` con `protected internal` l'accessibilità o dichiarata.</span><span class="sxs-lookup"><span data-stu-id="467ae-149">A static class cannot have members with `protected` or `protected internal` declared accessibility.</span></span>

<span data-ttu-id="467ae-150">Si tratta di un errore in fase di compilazione per violare una qualsiasi di queste restrizioni.</span><span class="sxs-lookup"><span data-stu-id="467ae-150">It is a compile-time error to violate any of these restrictions.</span></span>

<span data-ttu-id="467ae-151">Una classe statica non ha costruttori di istanza.</span><span class="sxs-lookup"><span data-stu-id="467ae-151">A static class has no instance constructors.</span></span> <span data-ttu-id="467ae-152">Non è possibile dichiarare un costruttore di istanza in una classe statica e non viene fornito alcun costruttore di istanza predefinito ([costruttori predefiniti](classes.md#default-constructors)) per una classe statica.</span><span class="sxs-lookup"><span data-stu-id="467ae-152">It is not possible to declare an instance constructor in a static class, and no default instance constructor ([Default constructors](classes.md#default-constructors)) is provided for a static class.</span></span>

<span data-ttu-id="467ae-153">I membri di una classe statica non sono automaticamente statici e le dichiarazioni dei membri devono includere in modo esplicito `static` un modificatore (ad eccezione delle costanti e dei tipi annidati).</span><span class="sxs-lookup"><span data-stu-id="467ae-153">The members of a static class are not automatically static, and the member declarations must explicitly include a `static` modifier (except for constants and nested types).</span></span> <span data-ttu-id="467ae-154">Quando una classe è annidata all'interno di una classe esterna statica, la classe annidata non è una classe statica a meno che `static` non includa in modo esplicito un modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-154">When a class is nested within a static outer class, the nested class is not a static class unless it explicitly includes a `static` modifier.</span></span>

<span data-ttu-id="467ae-155">__Riferimento a tipi di classe statici__</span><span class="sxs-lookup"><span data-stu-id="467ae-155">__Referencing static class types__</span></span>

<span data-ttu-id="467ae-156">Un *namespace_or_type_name* (nomi[di spazio dei nomi e di tipi](basic-concepts.md#namespace-and-type-names)) può fare riferimento a una classe statica se</span><span class="sxs-lookup"><span data-stu-id="467ae-156">A *namespace_or_type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)) is permitted to reference a static class if</span></span>

*  <span data-ttu-id="467ae-157">*Namespace_or_type_name* è il `T` in un *namespace_or_type_name* nel formato `T.I`, oppure</span><span class="sxs-lookup"><span data-stu-id="467ae-157">The *namespace_or_type_name* is the `T` in a *namespace_or_type_name* of the form `T.I`, or</span></span>
*  <span data-ttu-id="467ae-158">*Namespace_or_type_name* è il `T` in un *typeof_expression* ([elenco di argomenti](expressions.md#argument-lists)1) nel formato `typeof(T)`.</span><span class="sxs-lookup"><span data-stu-id="467ae-158">The *namespace_or_type_name* is the `T` in a *typeof_expression* ([Argument lists](expressions.md#argument-lists)1) of the form `typeof(T)`.</span></span>

<span data-ttu-id="467ae-159">Un *primary_expression* ([membri di funzione](expressions.md#function-members)) può fare riferimento a una classe statica se</span><span class="sxs-lookup"><span data-stu-id="467ae-159">A *primary_expression* ([Function members](expressions.md#function-members)) is permitted to reference a static class if</span></span>

*  <span data-ttu-id="467ae-160">*Primary_expression* è il `E` in un *member_access* ([controllo in fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) nel formato `E.I`.</span><span class="sxs-lookup"><span data-stu-id="467ae-160">The *primary_expression* is the `E` in a *member_access* ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) of the form `E.I`.</span></span>

<span data-ttu-id="467ae-161">In qualsiasi altro contesto, si tratta di un errore in fase di compilazione per fare riferimento a una classe statica.</span><span class="sxs-lookup"><span data-stu-id="467ae-161">In any other context it is a compile-time error to reference a static class.</span></span> <span data-ttu-id="467ae-162">Ad esempio, è un errore per una classe statica da usare come classe base, un tipo costituente ([tipi annidati](classes.md#nested-types)) di un membro, un argomento di tipo generico o un vincolo di parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-162">For example, it is an error for a static class to be used as a base class, a constituent type ([Nested types](classes.md#nested-types)) of a member, a generic type argument, or a type parameter constraint.</span></span> <span data-ttu-id="467ae-163">Analogamente, una classe statica non può essere usata in un tipo di matrice, un tipo `new` di puntatore, un'espressione, un' `is` espressione cast, `as` un'espressione, `sizeof` un'espressione, un'espressione o un'espressione con valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="467ae-163">Likewise, a static class cannot be used in an array type, a pointer type, a `new` expression, a cast expression, an `is` expression, an `as` expression, a `sizeof` expression, or a default value expression.</span></span>

### <a name="partial-modifier"></a><span data-ttu-id="467ae-164">Modificatore parziale</span><span class="sxs-lookup"><span data-stu-id="467ae-164">Partial modifier</span></span>

<span data-ttu-id="467ae-165">Il modificatore `partial` viene usato per indicare che questo *class_declaration* è una dichiarazione di tipo parziale.</span><span class="sxs-lookup"><span data-stu-id="467ae-165">The `partial` modifier is used to indicate that this *class_declaration* is a partial type declaration.</span></span> <span data-ttu-id="467ae-166">Più dichiarazioni di tipo parziale con lo stesso nome in uno spazio dei nomi di inclusione o in una dichiarazione di tipo combinano per formare una dichiarazione di tipo, seguendo le regole specificate nei [tipi parziali](classes.md#partial-types).</span><span class="sxs-lookup"><span data-stu-id="467ae-166">Multiple partial type declarations with the same name within an enclosing namespace or type declaration combine to form one type declaration, following the rules specified in [Partial types](classes.md#partial-types).</span></span>

<span data-ttu-id="467ae-167">La dichiarazione di una classe distribuita su segmenti separati del testo del programma può essere utile se questi segmenti vengono prodotti o conservati in contesti diversi.</span><span class="sxs-lookup"><span data-stu-id="467ae-167">Having the declaration of a class distributed over separate segments of program text can be useful if these segments are produced or maintained in different contexts.</span></span> <span data-ttu-id="467ae-168">Ad esempio, una parte di una dichiarazione di classe può essere generata dal computer, mentre l'altra viene creata manualmente.</span><span class="sxs-lookup"><span data-stu-id="467ae-168">For instance, one part of a class declaration may be machine generated, whereas the other is manually authored.</span></span> <span data-ttu-id="467ae-169">La separazione testuale dei due impedisce l'aggiornamento da parte di uno degli aggiornamenti in conflitto con gli aggiornamenti.</span><span class="sxs-lookup"><span data-stu-id="467ae-169">Textual separation of the two prevents updates by one from conflicting with updates by the other.</span></span>

### <a name="type-parameters"></a><span data-ttu-id="467ae-170">Parametri di tipo</span><span class="sxs-lookup"><span data-stu-id="467ae-170">Type parameters</span></span>

<span data-ttu-id="467ae-171">Un parametro di tipo è un identificatore semplice che denota un segnaposto per un argomento di tipo fornito per creare un tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="467ae-171">A type parameter is a simple identifier that denotes a placeholder for a type argument supplied to create a constructed type.</span></span> <span data-ttu-id="467ae-172">Un parametro di tipo è un segnaposto formale per un tipo che verrà fornito in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="467ae-172">A type parameter is a formal placeholder for a type that will be supplied later.</span></span> <span data-ttu-id="467ae-173">Al contrario, un argomento di tipo ([argomenti di tipo](types.md#type-arguments)) è il tipo effettivo che sostituisce il parametro di tipo quando viene creato un tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="467ae-173">By contrast, a type argument ([Type arguments](types.md#type-arguments)) is the actual type that is substituted for the type parameter when a constructed type is created.</span></span>

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

<span data-ttu-id="467ae-174">Ogni parametro di tipo in una dichiarazione di classe definisce un nome nello spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)) di tale classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-174">Each type parameter in a class declaration defines a name in the declaration space ([Declarations](basic-concepts.md#declarations)) of that class.</span></span> <span data-ttu-id="467ae-175">Quindi, non può avere lo stesso nome di un altro parametro di tipo o di un membro dichiarato in tale classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-175">Thus, it cannot have the same name as another type parameter or a member declared in that class.</span></span> <span data-ttu-id="467ae-176">Un parametro di tipo non può avere lo stesso nome del tipo stesso.</span><span class="sxs-lookup"><span data-stu-id="467ae-176">A type parameter cannot have the same name as the type itself.</span></span>

### <a name="class-base-specification"></a><span data-ttu-id="467ae-177">Specifica di base della classe</span><span class="sxs-lookup"><span data-stu-id="467ae-177">Class base specification</span></span>

<span data-ttu-id="467ae-178">Una dichiarazione di classe può includere una specifica *class_base* , che definisce la classe di base diretta della classe e le interfacce ([interfacce](interfaces.md)) direttamente implementate dalla classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-178">A class declaration may include a *class_base* specification, which defines the direct base class of the class and the interfaces ([Interfaces](interfaces.md)) directly implemented by the class.</span></span>

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

<span data-ttu-id="467ae-179">La classe base specificata in una dichiarazione di classe può essere un tipo di classe costruito ([tipi costruiti](types.md#constructed-types)).</span><span class="sxs-lookup"><span data-stu-id="467ae-179">The base class specified in a class declaration can be a constructed class type ([Constructed types](types.md#constructed-types)).</span></span> <span data-ttu-id="467ae-180">Una classe di base non può essere un parametro di tipo autonomamente, anche se può coinvolgere i parametri di tipo inclusi nell'ambito.</span><span class="sxs-lookup"><span data-stu-id="467ae-180">A base class cannot be a type parameter on its own, though it can involve the type parameters that are in scope.</span></span>

```csharp
class Extend<V>: V {}            // Error, type parameter used as base class
```

#### <a name="base-classes"></a><span data-ttu-id="467ae-181">Classi di base</span><span class="sxs-lookup"><span data-stu-id="467ae-181">Base classes</span></span>

<span data-ttu-id="467ae-182">Quando un *class_type* è incluso in *class_base*, specifica la classe di base diretta della classe dichiarata.</span><span class="sxs-lookup"><span data-stu-id="467ae-182">When a *class_type* is included in the *class_base*, it specifies the direct base class of the class being declared.</span></span> <span data-ttu-id="467ae-183">Se una dichiarazione di classe non dispone di *class_base*o se *class_base* elenca solo i tipi di interfaccia, si presuppone che la classe di base diretta sia `object`.</span><span class="sxs-lookup"><span data-stu-id="467ae-183">If a class declaration has no *class_base*, or if the *class_base* lists only interface types, the direct base class is assumed to be `object`.</span></span> <span data-ttu-id="467ae-184">Una classe eredita i membri dalla relativa classe di base diretta, come descritto in [ereditarietà](classes.md#inheritance).</span><span class="sxs-lookup"><span data-stu-id="467ae-184">A class inherits members from its direct base class, as described in [Inheritance](classes.md#inheritance).</span></span>

<span data-ttu-id="467ae-185">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-185">In the example</span></span>
```csharp
class A {}

class B: A {}
```
<span data-ttu-id="467ae-186">la `A` classe è detta classe di base diretta di `B`e `B` viene definita derivata da `A`.</span><span class="sxs-lookup"><span data-stu-id="467ae-186">class `A` is said to be the direct base class of `B`, and `B` is said to be derived from `A`.</span></span> <span data-ttu-id="467ae-187">Poiché `A` in non viene specificata in modo esplicito una classe base diretta, la relativa classe di `object`base diretta è implicita.</span><span class="sxs-lookup"><span data-stu-id="467ae-187">Since `A` does not explicitly specify a direct base class, its direct base class is implicitly `object`.</span></span>

<span data-ttu-id="467ae-188">Per un tipo di classe costruito, se nella dichiarazione di classe generica viene specificata una classe base, la classe base del tipo costruito viene ottenuta sostituendo, per ogni *type_parameter* nella dichiarazione della classe di base, il *type_argument corrispondente* del tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="467ae-188">For a constructed class type, if a base class is specified in the generic class declaration, the base class of the constructed type is obtained by substituting, for each *type_parameter* in the base class declaration, the corresponding *type_argument* of the constructed type.</span></span> <span data-ttu-id="467ae-189">Date le dichiarazioni di classi generiche</span><span class="sxs-lookup"><span data-stu-id="467ae-189">Given the generic class declarations</span></span>
```csharp
class B<U,V> {...}

class G<T>: B<string,T[]> {...}
```
<span data-ttu-id="467ae-190">la classe base del tipo `G<int>` costruito sarà. `B<string,int[]>`</span><span class="sxs-lookup"><span data-stu-id="467ae-190">the base class of the constructed type `G<int>` would be `B<string,int[]>`.</span></span>

<span data-ttu-id="467ae-191">La classe di base diretta di un tipo di classe deve essere accessibile almeno quanto il tipo di classe ([domini di accessibilità](basic-concepts.md#accessibility-domains)).</span><span class="sxs-lookup"><span data-stu-id="467ae-191">The direct base class of a class type must be at least as accessible as the class type itself ([Accessibility domains](basic-concepts.md#accessibility-domains)).</span></span> <span data-ttu-id="467ae-192">Ad esempio, si tratta di un errore in fase di compilazione `public` per la derivazione di `private` una `internal` classe da una classe o.</span><span class="sxs-lookup"><span data-stu-id="467ae-192">For example, it is a compile-time error for a `public` class to derive from a `private` or `internal` class.</span></span>

<span data-ttu-id="467ae-193">La classe di base diretta di un tipo di classe non può essere uno dei tipi seguenti `System.Array`: `System.Delegate` `System.MulticastDelegate`,, `System.Enum`, o `System.ValueType`.</span><span class="sxs-lookup"><span data-stu-id="467ae-193">The direct base class of a class type must not be any of the following types: `System.Array`, `System.Delegate`, `System.MulticastDelegate`, `System.Enum`, or `System.ValueType`.</span></span> <span data-ttu-id="467ae-194">Inoltre, una dichiarazione di classe generica `System.Attribute` non può essere utilizzata come classe base diretta o indiretta.</span><span class="sxs-lookup"><span data-stu-id="467ae-194">Furthermore, a generic class declaration cannot use `System.Attribute` as a direct or indirect base class.</span></span>

<span data-ttu-id="467ae-195">Quando si determina il `A` significato della specifica della classe di base diretta di una classe `B`, la classe di `B` base diretta di è temporaneamente `object`considerata.</span><span class="sxs-lookup"><span data-stu-id="467ae-195">While determining the meaning of the direct base class specification `A` of a class `B`, the direct base class of `B` is temporarily assumed to be `object`.</span></span> <span data-ttu-id="467ae-196">In modo intuitivo, questo garantisce che il significato di una specifica della classe base non possa dipendere in modo ricorsivo da se stesso.</span><span class="sxs-lookup"><span data-stu-id="467ae-196">Intuitively this ensures that the meaning of a base class specification cannot recursively depend on itself.</span></span> <span data-ttu-id="467ae-197">Esempio:</span><span class="sxs-lookup"><span data-stu-id="467ae-197">The example:</span></span>
```csharp
class A<T> {
   public class B {}
}

class C : A<C.B> {}
```
<span data-ttu-id="467ae-198">è in errore poiché nella specifica `A<C.B>` della classe di base la classe di base diretta di `C` viene considerata `object`e, di conseguenza, (dalle regole dei [nomi di spazio dei nomi e di tipo](basic-concepts.md#namespace-and-type-names)) `C` non è considerato un membro `B`.</span><span class="sxs-lookup"><span data-stu-id="467ae-198">is in error since in the base class specification `A<C.B>` the direct base class of `C` is considered to be `object`, and hence (by the rules of [Namespace and type names](basic-concepts.md#namespace-and-type-names))  `C` is not considered to have a member `B`.</span></span>

<span data-ttu-id="467ae-199">Le classi base di un tipo di classe sono la classe base diretta e le relative classi base.</span><span class="sxs-lookup"><span data-stu-id="467ae-199">The base classes of a class type are the direct base class and its base classes.</span></span> <span data-ttu-id="467ae-200">In altre parole, il set di classi di base è la chiusura transitiva della relazione della classe di base diretta.</span><span class="sxs-lookup"><span data-stu-id="467ae-200">In other words, the set of base classes is the transitive closure of the direct base class relationship.</span></span> <span data-ttu-id="467ae-201">Facendo riferimento all'esempio precedente, le classi di base di `B` sono `A` e `object`.</span><span class="sxs-lookup"><span data-stu-id="467ae-201">Referring to the example above, the base classes of `B` are `A` and `object`.</span></span> <span data-ttu-id="467ae-202">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-202">In the example</span></span>
```csharp
class A {...}

class B<T>: A {...}

class C<T>: B<IComparable<T>> {...}

class D<T>: C<T[]> {...}
```
<span data-ttu-id="467ae-203">le classi di base `D<int>` di `C<int[]>`sono `B<IComparable<int[]>>`, `A`, e `object`.</span><span class="sxs-lookup"><span data-stu-id="467ae-203">the base classes of `D<int>` are `C<int[]>`, `B<IComparable<int[]>>`, `A`, and `object`.</span></span>

<span data-ttu-id="467ae-204">Ad eccezione della `object`classe, ogni tipo di classe dispone esattamente di una classe di base diretta.</span><span class="sxs-lookup"><span data-stu-id="467ae-204">Except for class `object`, every class type has exactly one direct base class.</span></span> <span data-ttu-id="467ae-205">La `object` classe non dispone di una classe di base diretta ed è la classe base finale di tutte le altre classi.</span><span class="sxs-lookup"><span data-stu-id="467ae-205">The `object` class has no direct base class and is the ultimate base class of all other classes.</span></span>

<span data-ttu-id="467ae-206">Quando una classe `B` deriva da una classe `A`, si tratta di un `B`errore in fase `A` di compilazione da cui dipende.</span><span class="sxs-lookup"><span data-stu-id="467ae-206">When a class `B` derives from a class `A`, it is a compile-time error for `A` to depend on `B`.</span></span> <span data-ttu-id="467ae-207">Una classe ***dipende direttamente*** dalla relativa classe di base diretta, se presente, e ***dipende direttamente*** dalla classe in cui è immediatamente annidata (se presente).</span><span class="sxs-lookup"><span data-stu-id="467ae-207">A class ***directly depends on*** its direct base class (if any) and ***directly depends on*** the class within which it is immediately nested (if any).</span></span> <span data-ttu-id="467ae-208">Data questa definizione, il set completo di classi da cui dipende una classe è la chiusura riflessiva e transitiva del ***dipendente direttamente dalla*** relazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-208">Given this definition, the complete set of classes upon which a class depends is the reflexive and transitive closure of the ***directly depends on*** relationship.</span></span>

<span data-ttu-id="467ae-209">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-209">The example</span></span>
```csharp
class A: A {}
```
<span data-ttu-id="467ae-210">è errato perché la classe dipende da se stessa.</span><span class="sxs-lookup"><span data-stu-id="467ae-210">is erroneous because the class depends on itself.</span></span> <span data-ttu-id="467ae-211">Analogamente, l'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-211">Likewise, the example</span></span>
```csharp
class A: B {}
class B: C {}
class C: A {}
```
<span data-ttu-id="467ae-212">è in errore perché le classi dipendono da se stesse.</span><span class="sxs-lookup"><span data-stu-id="467ae-212">is in error because the classes circularly depend on themselves.</span></span> <span data-ttu-id="467ae-213">Infine, l'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-213">Finally, the example</span></span>
```csharp
class A: B.C {}

class B: A
{
    public class C {}
}
```
<span data-ttu-id="467ae-214">genera un errore in fase di compilazione perché `A` dipende `B.C` da (la relativa classe di base diretta) `B` , che dipende da (la classe che la contiene immediatamente `A`), che dipende in modo circolare da.</span><span class="sxs-lookup"><span data-stu-id="467ae-214">results in a compile-time error because `A` depends on `B.C` (its direct base class), which depends on `B` (its immediately enclosing class), which circularly depends on `A`.</span></span>

<span data-ttu-id="467ae-215">Si noti che una classe non dipende dalle classi annidate al suo interno.</span><span class="sxs-lookup"><span data-stu-id="467ae-215">Note that a class does not depend on the classes that are nested within it.</span></span> <span data-ttu-id="467ae-216">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-216">In the example</span></span>
```csharp
class A
{
    class B: A {}
}
```
<span data-ttu-id="467ae-217">`B``A` `B` `B` dipende da `A` (perché è la classe di base diretta e la classe che la contiene immediatamente), ma non dipende da (poiché non è una classe di base o una classe di inclusione di `A` `A`).</span><span class="sxs-lookup"><span data-stu-id="467ae-217">`B` depends on `A` (because `A` is both its direct base class and its immediately enclosing class), but `A` does not depend on `B` (since `B` is neither a base class nor an enclosing class of `A`).</span></span> <span data-ttu-id="467ae-218">L'esempio è quindi valido.</span><span class="sxs-lookup"><span data-stu-id="467ae-218">Thus, the example is valid.</span></span>

<span data-ttu-id="467ae-219">Non è possibile derivare da una `sealed` classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-219">It is not possible to derive from a `sealed` class.</span></span> <span data-ttu-id="467ae-220">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-220">In the example</span></span>
```csharp
sealed class A {}

class B: A {}            // Error, cannot derive from a sealed class
```
<span data-ttu-id="467ae-221">`A`la `B` classeèinerroreperchétentadiderivaredallaclasse.`sealed`</span><span class="sxs-lookup"><span data-stu-id="467ae-221">class `B` is in error because it attempts to derive from the `sealed` class `A`.</span></span>

#### <a name="interface-implementations"></a><span data-ttu-id="467ae-222">Implementazioni di interfacce</span><span class="sxs-lookup"><span data-stu-id="467ae-222">Interface implementations</span></span>

<span data-ttu-id="467ae-223">Una specifica *class_base* può includere un elenco di tipi di interfaccia, nel qual caso viene definito che la classe implementi direttamente i tipi di interfaccia specificati.</span><span class="sxs-lookup"><span data-stu-id="467ae-223">A *class_base* specification may include a list of interface types, in which case the class is said to directly implement the given interface types.</span></span> <span data-ttu-id="467ae-224">Le implementazioni dell'interfaccia sono illustrate ulteriormente nelle [implementazioni dell'interfaccia](interfaces.md#interface-implementations).</span><span class="sxs-lookup"><span data-stu-id="467ae-224">Interface implementations are discussed further in [Interface implementations](interfaces.md#interface-implementations).</span></span>

### <a name="type-parameter-constraints"></a><span data-ttu-id="467ae-225">Vincoli di parametro di tipo</span><span class="sxs-lookup"><span data-stu-id="467ae-225">Type parameter constraints</span></span>

<span data-ttu-id="467ae-226">Le dichiarazioni di tipi e metodi generici possono facoltativamente specificare vincoli di parametro di tipo includendo *type_parameter_constraints_clause*s.</span><span class="sxs-lookup"><span data-stu-id="467ae-226">Generic type and method declarations can optionally specify type parameter constraints by including *type_parameter_constraints_clause*s.</span></span>

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

<span data-ttu-id="467ae-227">Ogni *type_parameter_constraints_clause* è costituito dal token `where`, seguito dal nome di un parametro di tipo, seguito da due punti e dall'elenco di vincoli per quel parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-227">Each *type_parameter_constraints_clause* consists of the token `where`, followed by the name of a type parameter, followed by a colon and the list of constraints for that type parameter.</span></span> <span data-ttu-id="467ae-228">Per ogni parametro di tipo può `where` essere presente al massimo una clausola e le `where` clausole possono essere elencate in qualsiasi ordine.</span><span class="sxs-lookup"><span data-stu-id="467ae-228">There can be at most one `where` clause for each type parameter, and the `where` clauses can be listed in any order.</span></span> <span data-ttu-id="467ae-229">Analogamente `get` ai `set` token e in una funzione di accesso alla `where` proprietà, il token non è una parola chiave.</span><span class="sxs-lookup"><span data-stu-id="467ae-229">Like the `get` and `set` tokens in a property accessor, the `where` token is not a keyword.</span></span>

<span data-ttu-id="467ae-230">L'elenco di vincoli specificati in una `where` clausola può includere uno dei seguenti componenti, in questo ordine: un singolo vincolo primario, uno o più vincoli secondari e il vincolo del costruttore,. `new()`</span><span class="sxs-lookup"><span data-stu-id="467ae-230">The list of constraints given in a `where` clause can include any of the following components, in this order: a single primary constraint, one or more secondary constraints, and the constructor constraint, `new()`.</span></span>

<span data-ttu-id="467ae-231">Un vincolo primario può essere un tipo di classe o il vincolo del ***tipo*** `class` di riferimento o il ***vincolo*** `struct`del tipo di valore.</span><span class="sxs-lookup"><span data-stu-id="467ae-231">A primary constraint can be a class type or the ***reference type constraint*** `class` or the ***value type constraint*** `struct`.</span></span> <span data-ttu-id="467ae-232">Un vincolo secondario può essere *type_parameter* o *INTERFACE_TYPE*.</span><span class="sxs-lookup"><span data-stu-id="467ae-232">A secondary constraint can be a *type_parameter* or *interface_type*.</span></span>

<span data-ttu-id="467ae-233">Il vincolo del tipo di riferimento specifica che un argomento di tipo utilizzato per il parametro di tipo deve essere un tipo riferimento.</span><span class="sxs-lookup"><span data-stu-id="467ae-233">The reference type constraint specifies that a type argument used for the type parameter must be a reference type.</span></span> <span data-ttu-id="467ae-234">Tutti i tipi di classe, i tipi di interfaccia, i tipi di delegati, i tipi di matrici e i parametri di tipo noti come un tipo di riferimento, come definito di seguito, soddisfano questo vincolo.</span><span class="sxs-lookup"><span data-stu-id="467ae-234">All class types, interface types, delegate types, array types, and type parameters known to be a reference type (as defined below) satisfy this constraint.</span></span>

<span data-ttu-id="467ae-235">Il vincolo di tipo valore specifica che un argomento di tipo utilizzato per il parametro di tipo deve essere un tipo valore non nullable.</span><span class="sxs-lookup"><span data-stu-id="467ae-235">The value type constraint specifies that a type argument used for the type parameter must be a non-nullable value type.</span></span> <span data-ttu-id="467ae-236">Tutti i tipi struct non nullable, i tipi enum e i parametri di tipo con il vincolo di tipo valore soddisfano questo vincolo.</span><span class="sxs-lookup"><span data-stu-id="467ae-236">All non-nullable struct types, enum types, and type parameters having the value type constraint satisfy this constraint.</span></span> <span data-ttu-id="467ae-237">Si noti che anche se classificato come tipo valore, un tipo Nullable ([tipi nullable](types.md#nullable-types)) non soddisfa il vincolo di tipo valore.</span><span class="sxs-lookup"><span data-stu-id="467ae-237">Note that although classified as a value type, a nullable type ([Nullable types](types.md#nullable-types)) does not satisfy the value type constraint.</span></span> <span data-ttu-id="467ae-238">Un parametro di tipo con il vincolo di tipo valore non può avere anche *constructor_constraint*.</span><span class="sxs-lookup"><span data-stu-id="467ae-238">A type parameter having the value type constraint cannot also have the *constructor_constraint*.</span></span>

<span data-ttu-id="467ae-239">I tipi di puntatore non sono mai consentiti come argomenti di tipo e non sono considerati conformi al tipo di riferimento o ai vincoli del tipo di valore.</span><span class="sxs-lookup"><span data-stu-id="467ae-239">Pointer types are never allowed to be type arguments and are not considered to satisfy either the reference type or value type constraints.</span></span>

<span data-ttu-id="467ae-240">Se un vincolo è un tipo di classe, un tipo di interfaccia o un parametro di tipo, tale tipo specifica un "tipo di base" minimo che ogni argomento di tipo usato per quel parametro di tipo deve supportare.</span><span class="sxs-lookup"><span data-stu-id="467ae-240">If a constraint is a class type, an interface type, or a type parameter, that type specifies a minimal "base type" that every type argument used for that type parameter must support.</span></span> <span data-ttu-id="467ae-241">Ogni volta che viene usato un tipo costruito o un metodo generico, l'argomento di tipo viene confrontato con i vincoli sul parametro di tipo in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-241">Whenever a constructed type or generic method is used, the type argument is checked against the constraints on the type parameter at compile-time.</span></span> <span data-ttu-id="467ae-242">L'argomento di tipo fornito deve soddisfare le condizioni descritte nei [vincoli soddisfacenti](types.md#satisfying-constraints).</span><span class="sxs-lookup"><span data-stu-id="467ae-242">The type argument supplied must satisfy the conditions described in [Satisfying constraints](types.md#satisfying-constraints).</span></span>

<span data-ttu-id="467ae-243">Un vincolo *class_type* deve soddisfare le regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-243">A *class_type* constraint must satisfy the following rules:</span></span>

*  <span data-ttu-id="467ae-244">Il tipo deve essere un tipo di classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-244">The type must be a class type.</span></span>
*  <span data-ttu-id="467ae-245">Il tipo non deve essere `sealed`.</span><span class="sxs-lookup"><span data-stu-id="467ae-245">The type must not be `sealed`.</span></span>
*  <span data-ttu-id="467ae-246">Il tipo non deve essere uno dei tipi seguenti `System.Array`:, `System.Delegate`, `System.Enum`o `System.ValueType`.</span><span class="sxs-lookup"><span data-stu-id="467ae-246">The type must not be one of the following types: `System.Array`, `System.Delegate`, `System.Enum`, or `System.ValueType`.</span></span>
*  <span data-ttu-id="467ae-247">Il tipo non deve essere `object`.</span><span class="sxs-lookup"><span data-stu-id="467ae-247">The type must not be `object`.</span></span> <span data-ttu-id="467ae-248">Poiché tutti i tipi derivano da, un vincolo di `object`questo tipo non avrà alcun effetto se fosse consentito.</span><span class="sxs-lookup"><span data-stu-id="467ae-248">Because all types derive from `object`, such a constraint would have no effect if it were permitted.</span></span>
*  <span data-ttu-id="467ae-249">Al massimo un vincolo per un determinato parametro di tipo può essere un tipo classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-249">At most one constraint for a given type parameter can be a class type.</span></span>

<span data-ttu-id="467ae-250">Un tipo specificato come vincolo *INTERFACE_TYPE* deve soddisfare le regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-250">A type specified as an *interface_type* constraint must satisfy the following rules:</span></span>

*  <span data-ttu-id="467ae-251">Il tipo deve essere un tipo di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="467ae-251">The type must be an interface type.</span></span>
*  <span data-ttu-id="467ae-252">Un tipo non deve essere specificato più di una volta in una `where` clausola specificata.</span><span class="sxs-lookup"><span data-stu-id="467ae-252">A type must not be specified more than once in a given `where` clause.</span></span>

<span data-ttu-id="467ae-253">In entrambi i casi, il vincolo può coinvolgere qualsiasi parametro di tipo della dichiarazione del tipo o del metodo associato come parte di un tipo costruito e può coinvolgere il tipo dichiarato.</span><span class="sxs-lookup"><span data-stu-id="467ae-253">In either case, the constraint can involve any of the type parameters of the associated type or method declaration as part of a constructed type, and can involve the type being declared.</span></span>

<span data-ttu-id="467ae-254">Qualsiasi classe o tipo di interfaccia specificato come vincolo di parametro di tipo deve essere almeno accessibile ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)) del tipo o del metodo generico dichiarato.</span><span class="sxs-lookup"><span data-stu-id="467ae-254">Any class or interface type specified as a type parameter constraint must be at least as accessible ([Accessibility constraints](basic-concepts.md#accessibility-constraints)) as the generic type or method being declared.</span></span>

<span data-ttu-id="467ae-255">Un tipo specificato come vincolo *type_parameter* deve soddisfare le regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-255">A type specified as a *type_parameter* constraint must satisfy the following rules:</span></span>

*  <span data-ttu-id="467ae-256">Il tipo deve essere un parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-256">The type must be a type parameter.</span></span>
*  <span data-ttu-id="467ae-257">Un tipo non deve essere specificato più di una volta in una `where` clausola specificata.</span><span class="sxs-lookup"><span data-stu-id="467ae-257">A type must not be specified more than once in a given `where` clause.</span></span>

<span data-ttu-id="467ae-258">Inoltre, non deve essere presente alcun ciclo nel grafico delle dipendenze di parametri di tipo, in cui la dipendenza è una relazione transitiva definita da:</span><span class="sxs-lookup"><span data-stu-id="467ae-258">In addition there must be no cycles in the dependency graph of type parameters, where dependency is a transitive relation defined by:</span></span>

*  <span data-ttu-id="467ae-259">Se `T` un parametro di tipo viene usato come vincolo per il `S` parametro `S` di tipo, ***dipende*** `T`da.</span><span class="sxs-lookup"><span data-stu-id="467ae-259">If a type parameter `T` is used as a constraint for type parameter `S` then `S` ***depends on*** `T`.</span></span>
*  <span data-ttu-id="467ae-260">Se `S` un parametro di tipo dipende da un parametro `T` di tipo `T` e dipende da un parametro `U` `S` di tipo, ***dipende*** `U`da.</span><span class="sxs-lookup"><span data-stu-id="467ae-260">If a type parameter `S` depends on a type parameter `T` and `T` depends on a type parameter `U` then `S` ***depends on*** `U`.</span></span>

<span data-ttu-id="467ae-261">Data questa relazione, si tratta di un errore in fase di compilazione affinché un parametro di tipo dipenda da solo (direttamente o indirettamente).</span><span class="sxs-lookup"><span data-stu-id="467ae-261">Given this relation, it is a compile-time error for a type parameter to depend on itself (directly or indirectly).</span></span>

<span data-ttu-id="467ae-262">Tutti i vincoli devono essere coerenti tra i parametri di tipo dipendente.</span><span class="sxs-lookup"><span data-stu-id="467ae-262">Any constraints must be consistent among dependent type parameters.</span></span> <span data-ttu-id="467ae-263">Se il parametro `S` di tipo dipende dal parametro `T` di tipo:</span><span class="sxs-lookup"><span data-stu-id="467ae-263">If type parameter `S` depends on type parameter `T` then:</span></span>

*  <span data-ttu-id="467ae-264">`T`non deve avere il vincolo di tipo valore.</span><span class="sxs-lookup"><span data-stu-id="467ae-264">`T` must not have the value type constraint.</span></span> <span data-ttu-id="467ae-265">In caso `T` contrario, è effettivamente `S` sealed, quindi verrebbe forzato a `T`essere lo stesso tipo di, eliminando la necessità di due parametri di tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-265">Otherwise, `T` is effectively sealed so `S` would be forced to be the same type as `T`, eliminating the need for two type parameters.</span></span>
*  <span data-ttu-id="467ae-266">Se `S` ha il vincolo di tipo valore, `T` non deve avere un vincolo *class_type* .</span><span class="sxs-lookup"><span data-stu-id="467ae-266">If `S` has the value type constraint then `T` must not have a *class_type* constraint.</span></span>
*  <span data-ttu-id="467ae-267">Se `S` ha un vincolo *class_type* `A` e `T` ha un vincolo *class_type* `B`, è necessario che sia presente una conversione di identità o una conversione di un riferimento implicito da `A` a `B` o una conversione di riferimento implicita da da `B` a `A`.</span><span class="sxs-lookup"><span data-stu-id="467ae-267">If `S` has a *class_type* constraint `A` and `T` has a *class_type* constraint `B` then there must be an identity conversion or implicit reference conversion from `A` to `B` or an implicit reference conversion from `B` to `A`.</span></span>
*  <span data-ttu-id="467ae-268">Se `S` dipende anche dal parametro di tipo `U` e `U` ha un vincolo *class_type* `A` e `T` ha un vincolo *class_type* `B`, è necessario che sia presente una conversione di identità o un riferimento implicito da `A` per `B` o una conversione di un riferimento implicito da 0 a 1.</span><span class="sxs-lookup"><span data-stu-id="467ae-268">If `S` also depends on type parameter `U` and `U` has a *class_type* constraint `A` and `T` has a *class_type* constraint `B` then there must be an identity conversion or implicit reference conversion from `A` to `B` or an implicit reference conversion from `B` to `A`.</span></span>

<span data-ttu-id="467ae-269">Il vincolo di tipo `S` di valore e `T` il vincolo di tipo di riferimento sono validi per l'oggetto.</span><span class="sxs-lookup"><span data-stu-id="467ae-269">It is valid for `S` to have the value type constraint and `T` to have the reference type constraint.</span></span> <span data-ttu-id="467ae-270">Questo limite `T` è effettivamente limitato ai `System.Object`tipi `System.ValueType`, `System.Enum`, e a qualsiasi tipo di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="467ae-270">Effectively this limits `T` to the types `System.Object`, `System.ValueType`, `System.Enum`, and any interface type.</span></span>

<span data-ttu-id="467ae-271">Se la `where` clausola per un parametro di tipo include un vincolo del costruttore, che ha `new()`il formato, è possibile usare l' `new` operatore per creare istanze del tipo (espressioni di[creazione di oggetti](expressions.md#object-creation-expressions)).</span><span class="sxs-lookup"><span data-stu-id="467ae-271">If the `where` clause for a type parameter includes a constructor constraint (which has the form `new()`), it is possible to use the `new` operator to create instances of the type ([Object creation expressions](expressions.md#object-creation-expressions)).</span></span> <span data-ttu-id="467ae-272">Qualsiasi argomento di tipo usato per un parametro di tipo con un vincolo del costruttore deve avere un costruttore pubblico senza parametri (questo costruttore esiste in modo implicito per qualsiasi tipo di valore) o essere un parametro di tipo con il vincolo del tipo di valore o del costruttore (vedere [Vincoli del parametro di tipo](classes.md#type-parameter-constraints) per i dettagli).</span><span class="sxs-lookup"><span data-stu-id="467ae-272">Any type argument used for a type parameter with a constructor constraint must have a public parameterless constructor (this constructor implicitly exists for any value type) or be a type parameter having the value type constraint or constructor constraint (see [Type parameter constraints](classes.md#type-parameter-constraints) for details).</span></span>

<span data-ttu-id="467ae-273">Di seguito sono riportati alcuni esempi di vincoli:</span><span class="sxs-lookup"><span data-stu-id="467ae-273">The following are examples of constraints:</span></span>
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

<span data-ttu-id="467ae-274">L'esempio seguente è in errore perché causa una circolare nel grafico delle dipendenze dei parametri di tipo:</span><span class="sxs-lookup"><span data-stu-id="467ae-274">The following example is in error because it causes a circularity in the dependency graph of the type parameters:</span></span>
```csharp
class Circular<S,T>
    where S: T
    where T: S                // Error, circularity in dependency graph
{
    ...
}
```

<span data-ttu-id="467ae-275">Gli esempi seguenti illustrano situazioni non valide aggiuntive:</span><span class="sxs-lookup"><span data-stu-id="467ae-275">The following examples illustrate additional invalid situations:</span></span>
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

<span data-ttu-id="467ae-276">La ***classe base effettiva*** di un parametro `T` di tipo è definita nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="467ae-276">The ***effective base class*** of a type parameter `T` is defined as follows:</span></span>

*  <span data-ttu-id="467ae-277">Se `T` non ha vincoli primari o vincoli di parametro di tipo, la relativa classe `object`di base effettiva è.</span><span class="sxs-lookup"><span data-stu-id="467ae-277">If `T` has no primary constraints or type parameter constraints, its effective base class is `object`.</span></span>
*  <span data-ttu-id="467ae-278">Se `T` ha il vincolo di tipo valore, la relativa classe di `System.ValueType`base effettiva è.</span><span class="sxs-lookup"><span data-stu-id="467ae-278">If `T` has the value type constraint, its effective base class is `System.ValueType`.</span></span>
*  <span data-ttu-id="467ae-279">Se `T` ha un vincolo *class_type* `C` ma nessun vincolo *type_parameter* , la relativa classe di base effettiva sarà `C`.</span><span class="sxs-lookup"><span data-stu-id="467ae-279">If `T` has a *class_type* constraint `C` but no *type_parameter* constraints, its effective base class is `C`.</span></span>
*  <span data-ttu-id="467ae-280">Se `T` non ha un vincolo *class_type* ma ha uno o più vincoli *type_parameter* , la relativa classe di base effettiva è il tipo più incluso ([operatori di conversione rimossi](conversions.md#lifted-conversion-operators)) nel set di classi di base valide del relativo *Type_* vincoli di parametro.</span><span class="sxs-lookup"><span data-stu-id="467ae-280">If `T` has no *class_type* constraint but has one or more *type_parameter* constraints, its effective base class is the most encompassed type ([Lifted conversion operators](conversions.md#lifted-conversion-operators)) in the set of effective base classes of its *type_parameter* constraints.</span></span> <span data-ttu-id="467ae-281">Le regole di coerenza assicurano che esista un tipo più incluso.</span><span class="sxs-lookup"><span data-stu-id="467ae-281">The consistency rules ensure that such a most encompassed type exists.</span></span>
*  <span data-ttu-id="467ae-282">Se `T` dispone sia di un vincolo *class_type* che di uno o più vincoli *type_parameter* , la relativa classe di base effettiva è il tipo più incluso ([operatori di conversione rimossi](conversions.md#lifted-conversion-operators)) nel set costituito da *class_type* vincolo di `T` e delle classi di base valide dei relativi vincoli *type_parameter* .</span><span class="sxs-lookup"><span data-stu-id="467ae-282">If `T` has both a *class_type* constraint and one or more *type_parameter* constraints, its effective base class is the most encompassed type ([Lifted conversion operators](conversions.md#lifted-conversion-operators)) in the set consisting of the *class_type* constraint of `T` and the effective base classes of its *type_parameter* constraints.</span></span> <span data-ttu-id="467ae-283">Le regole di coerenza assicurano che esista un tipo più incluso.</span><span class="sxs-lookup"><span data-stu-id="467ae-283">The consistency rules ensure that such a most encompassed type exists.</span></span>
*  <span data-ttu-id="467ae-284">Se `T` ha il vincolo del tipo di riferimento ma nessun vincolo *class_type* , la relativa classe di base effettiva sarà `object`.</span><span class="sxs-lookup"><span data-stu-id="467ae-284">If `T` has the reference type constraint but no *class_type* constraints, its effective base class is `object`.</span></span>

<span data-ttu-id="467ae-285">Ai fini di queste regole, se T ha un vincolo `V` che è un *value_type*, usare invece il tipo di base più specifico di `V` che è un *class_type*.</span><span class="sxs-lookup"><span data-stu-id="467ae-285">For the purpose of these rules, if T has a constraint `V` that is a *value_type*, use instead the most specific base type of `V` that is a *class_type*.</span></span> <span data-ttu-id="467ae-286">Questo problema non può mai verificarsi in un vincolo specificato in modo esplicito, ma può verificarsi quando i vincoli di un metodo generico vengono ereditati in modo implicito da una dichiarazione di metodo che esegue l'override o da un'implementazione esplicita di un metodo di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="467ae-286">This can never happen in an explicitly given constraint, but may occur when the constraints of a generic method are implicitly inherited by an overriding method declaration or an explicit implementation of an interface method.</span></span>

<span data-ttu-id="467ae-287">Queste regole assicurano che la classe di base effettiva sia sempre un *class_type*.</span><span class="sxs-lookup"><span data-stu-id="467ae-287">These rules ensure that the effective base class is always a *class_type*.</span></span>

<span data-ttu-id="467ae-288">Il ***set di interfacce effettivo*** di un parametro `T` di tipo viene definito nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="467ae-288">The ***effective interface set*** of a type parameter `T` is defined as follows:</span></span>

*  <span data-ttu-id="467ae-289">Se `T` non ha *secondary_constraints*, il set di interfacce effettivo è vuoto.</span><span class="sxs-lookup"><span data-stu-id="467ae-289">If `T` has no *secondary_constraints*, its effective interface set is empty.</span></span>
*  <span data-ttu-id="467ae-290">Se `T` ha vincoli *INTERFACE_TYPE* ma nessun vincolo *type_parameter* , il set di interfacce effettivo è il set di vincoli *INTERFACE_TYPE* .</span><span class="sxs-lookup"><span data-stu-id="467ae-290">If `T` has *interface_type* constraints but no *type_parameter* constraints, its effective interface set is its set of *interface_type* constraints.</span></span>
*  <span data-ttu-id="467ae-291">Se `T` non ha vincoli *INTERFACE_TYPE* ma presenta vincoli *type_parameter* , il relativo set di interfacce effettivo è l'Unione dei set di interfaccia effettivi dei vincoli *type_parameter* .</span><span class="sxs-lookup"><span data-stu-id="467ae-291">If `T` has no *interface_type* constraints but has *type_parameter* constraints, its effective interface set is the union of the effective interface sets of its *type_parameter* constraints.</span></span>
*  <span data-ttu-id="467ae-292">Se `T` ha sia vincoli *INTERFACE_TYPE* che vincoli *type_parameter* , il set di interfacce effettivo è l'Unione del set di vincoli *INTERFACE_TYPE* e dei set di interfacce effettivi del relativo *type_parameter* vincoli.</span><span class="sxs-lookup"><span data-stu-id="467ae-292">If `T` has both *interface_type* constraints and *type_parameter* constraints, its effective interface set is the union of its set of *interface_type* constraints and the effective interface sets of its *type_parameter* constraints.</span></span>

<span data-ttu-id="467ae-293">Un parametro di tipo è ***noto come tipo di riferimento*** se ha il vincolo di tipo riferimento o la classe di base effettiva non `object` è `System.ValueType`o.</span><span class="sxs-lookup"><span data-stu-id="467ae-293">A type parameter is ***known to be a reference type*** if it has the reference type constraint or its effective base class is not `object` or `System.ValueType`.</span></span>

<span data-ttu-id="467ae-294">I valori di un tipo di parametro di tipo vincolato possono essere utilizzati per accedere ai membri di istanza impliciti nei vincoli.</span><span class="sxs-lookup"><span data-stu-id="467ae-294">Values of a constrained type parameter type can be used to access the instance members implied by the constraints.</span></span> <span data-ttu-id="467ae-295">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-295">In the example</span></span>
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
<span data-ttu-id="467ae-296">i metodi di `IPrintable` possono essere richiamati `x` direttamente `T` in perché è vincolato a implementare `IPrintable`sempre.</span><span class="sxs-lookup"><span data-stu-id="467ae-296">the methods of `IPrintable` can be invoked directly on `x` because `T` is constrained to always implement `IPrintable`.</span></span>

### <a name="class-body"></a><span data-ttu-id="467ae-297">Corpo della classe</span><span class="sxs-lookup"><span data-stu-id="467ae-297">Class body</span></span>

<span data-ttu-id="467ae-298">Il *class_body* di una classe definisce i membri di tale classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-298">The *class_body* of a class defines the members of that class.</span></span>

```antlr
class_body
    : '{' class_member_declaration* '}'
    ;
```

## <a name="partial-types"></a><span data-ttu-id="467ae-299">Tipi parziali</span><span class="sxs-lookup"><span data-stu-id="467ae-299">Partial types</span></span>

<span data-ttu-id="467ae-300">Una dichiarazione di tipo può essere divisa tra più ***dichiarazioni di tipo parziale***.</span><span class="sxs-lookup"><span data-stu-id="467ae-300">A type declaration can be split across multiple ***partial type declarations***.</span></span> <span data-ttu-id="467ae-301">La dichiarazione di tipo viene costruita dalle sue parti seguendo le regole in questa sezione, in quanto viene considerata come una singola dichiarazione durante il resto dell'elaborazione del programma in fase di compilazione e in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="467ae-301">The type declaration is constructed from its parts by following the rules in this section, whereupon it is treated as a single declaration during the remainder of the compile-time and run-time processing of the program.</span></span>

<span data-ttu-id="467ae-302">*Class_declaration*, *struct_declaration* o *interface_declaration* rappresenta una dichiarazione di tipo parziale se include un modificatore `partial`.</span><span class="sxs-lookup"><span data-stu-id="467ae-302">A *class_declaration*, *struct_declaration* or *interface_declaration* represents a partial type declaration if it includes a `partial` modifier.</span></span> <span data-ttu-id="467ae-303">`partial`non è una parola chiave e agisce solo come modificatore se viene visualizzato immediatamente prima di una delle parole `class`chiave `struct` o `interface` in una dichiarazione di tipo o prima del tipo `void` in una dichiarazione di metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-303">`partial` is not a keyword, and only acts as a modifier if it appears immediately before one of the keywords `class`, `struct` or `interface` in a type declaration, or before the type `void` in a method declaration.</span></span> <span data-ttu-id="467ae-304">In altri contesti può essere usato come identificatore normale.</span><span class="sxs-lookup"><span data-stu-id="467ae-304">In other contexts it can be used as a normal identifier.</span></span>

<span data-ttu-id="467ae-305">Ogni parte di una dichiarazione di tipo parziale deve includere `partial` un modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-305">Each part of a partial type declaration must include a `partial` modifier.</span></span> <span data-ttu-id="467ae-306">Deve avere lo stesso nome ed essere dichiarato nello stesso spazio dei nomi o nella stessa dichiarazione di tipo delle altre parti.</span><span class="sxs-lookup"><span data-stu-id="467ae-306">It must have the same name  and be declared in the same namespace or type declaration as the other parts.</span></span> <span data-ttu-id="467ae-307">Il `partial` modificatore indica che altre parti della dichiarazione di tipo possono esistere altrove, ma l'esistenza di tali parti aggiuntive non è un requisito; è valido per un tipo con una sola dichiarazione per includere il `partial` modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-307">The `partial` modifier indicates that additional parts of the type declaration may exist elsewhere, but the existence of such additional parts is not a requirement; it is valid for a type with a single declaration to include the `partial` modifier.</span></span>

<span data-ttu-id="467ae-308">Tutte le parti di un tipo parziale devono essere compilate insieme in modo che sia possibile unire le parti in fase di compilazione in una singola dichiarazione di tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-308">All parts of a partial type must be compiled together such that the parts can be merged at compile-time into a single type declaration.</span></span> <span data-ttu-id="467ae-309">I tipi parziali in particolare non consentono l'estensione di tipi già compilati.</span><span class="sxs-lookup"><span data-stu-id="467ae-309">Partial types specifically do not allow already compiled types to be extended.</span></span>

<span data-ttu-id="467ae-310">I tipi annidati possono essere dichiarati in più `partial` parti tramite il modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-310">Nested types may be declared in multiple parts by using the `partial` modifier.</span></span> <span data-ttu-id="467ae-311">In genere, il tipo contenitore viene dichiarato `partial` utilizzando anche e ogni parte del tipo annidato viene dichiarata in una parte diversa del tipo che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="467ae-311">Typically, the containing type is declared using `partial` as well, and each part of the nested type is declared in a different part of the containing type.</span></span>

<span data-ttu-id="467ae-312">Il `partial` modificatore non è consentito nelle dichiarazioni di delegato o enum.</span><span class="sxs-lookup"><span data-stu-id="467ae-312">The `partial` modifier is not permitted on delegate or enum declarations.</span></span>

### <a name="attributes"></a><span data-ttu-id="467ae-313">Attributi</span><span class="sxs-lookup"><span data-stu-id="467ae-313">Attributes</span></span>

<span data-ttu-id="467ae-314">Gli attributi di un tipo parziale vengono determinati combinando, in un ordine non specificato, gli attributi di ogni parte.</span><span class="sxs-lookup"><span data-stu-id="467ae-314">The attributes of a partial type are determined by combining, in an unspecified order, the attributes of each of the parts.</span></span> <span data-ttu-id="467ae-315">Se un attributo viene inserito su più parti, equivale a specificare l'attributo più volte nel tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-315">If an attribute is placed on multiple parts, it is equivalent to specifying the attribute multiple times on the type.</span></span> <span data-ttu-id="467ae-316">Ad esempio, le due parti:</span><span class="sxs-lookup"><span data-stu-id="467ae-316">For example, the two parts:</span></span>

```csharp
[Attr1, Attr2("hello")]
partial class A {}

[Attr3, Attr2("goodbye")]
partial class A {}
```
<span data-ttu-id="467ae-317">sono equivalenti a una dichiarazione come:</span><span class="sxs-lookup"><span data-stu-id="467ae-317">are equivalent to a declaration such as:</span></span>
```csharp
[Attr1, Attr2("hello"), Attr3, Attr2("goodbye")]
class A {}
```

<span data-ttu-id="467ae-318">Gli attributi dei parametri di tipo vengono combinati in modo simile.</span><span class="sxs-lookup"><span data-stu-id="467ae-318">Attributes on type parameters combine in a similar fashion.</span></span>

### <a name="modifiers"></a><span data-ttu-id="467ae-319">Modificatori</span><span class="sxs-lookup"><span data-stu-id="467ae-319">Modifiers</span></span>

<span data-ttu-id="467ae-320">Quando una dichiarazione di tipo parziale include una specifica di accessibilità `protected`, `internal`ovvero i `private` `public`modificatori,, e, deve essere conforme a tutte le altre parti che includono una specifica di accessibilità.</span><span class="sxs-lookup"><span data-stu-id="467ae-320">When a partial type declaration includes an accessibility specification (the `public`, `protected`, `internal`, and `private` modifiers) it must agree with all other parts that include an accessibility specification.</span></span> <span data-ttu-id="467ae-321">Se nessuna parte di un tipo parziale include una specifica di accessibilità, al tipo viene assegnata l'accessibilità predefinita appropriata ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)).</span><span class="sxs-lookup"><span data-stu-id="467ae-321">If no part of a partial type includes an accessibility specification, the type is given the appropriate default accessibility ([Declared accessibility](basic-concepts.md#declared-accessibility)).</span></span>

<span data-ttu-id="467ae-322">Se una o più dichiarazioni parziali di un tipo annidato includono `new` un modificatore, non viene segnalato alcun avviso se il tipo annidato nasconde un membro ereditato ([nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance)).</span><span class="sxs-lookup"><span data-stu-id="467ae-322">If one or more partial declarations of a nested type include a `new` modifier, no warning is reported if the nested type hides an inherited member ([Hiding through inheritance](basic-concepts.md#hiding-through-inheritance)).</span></span>

<span data-ttu-id="467ae-323">Se una o più dichiarazioni parziali di una classe includono un `abstract` modificatore, la classe viene considerata astratta ([classi astratte](classes.md#abstract-classes)).</span><span class="sxs-lookup"><span data-stu-id="467ae-323">If one or more partial declarations of a class include an `abstract` modifier, the class is considered abstract ([Abstract classes](classes.md#abstract-classes)).</span></span> <span data-ttu-id="467ae-324">In caso contrario, la classe viene considerata non astratta.</span><span class="sxs-lookup"><span data-stu-id="467ae-324">Otherwise, the class is considered non-abstract.</span></span>

<span data-ttu-id="467ae-325">Se una o più dichiarazioni parziali di una classe includono un `sealed` modificatore, la classe viene considerata sealed ([classi sealed](classes.md#sealed-classes)).</span><span class="sxs-lookup"><span data-stu-id="467ae-325">If one or more partial declarations of a class include a `sealed` modifier, the class is considered sealed ([Sealed classes](classes.md#sealed-classes)).</span></span> <span data-ttu-id="467ae-326">In caso contrario, la classe viene considerata non sealed.</span><span class="sxs-lookup"><span data-stu-id="467ae-326">Otherwise, the class is considered unsealed.</span></span>

<span data-ttu-id="467ae-327">Si noti che una classe non può essere sia astratta che sealed.</span><span class="sxs-lookup"><span data-stu-id="467ae-327">Note that a class cannot be both abstract and sealed.</span></span>

<span data-ttu-id="467ae-328">Quando il `unsafe` modificatore viene utilizzato in una dichiarazione di tipo parziale, solo quella particolare parte viene considerata un contesto non sicuro ([contesti non sicuri](unsafe-code.md#unsafe-contexts)).</span><span class="sxs-lookup"><span data-stu-id="467ae-328">When the `unsafe` modifier is used on a partial type declaration, only that particular part is considered an unsafe context ([Unsafe contexts](unsafe-code.md#unsafe-contexts)).</span></span>

### <a name="type-parameters-and-constraints"></a><span data-ttu-id="467ae-329">Parametri di tipo e vincoli</span><span class="sxs-lookup"><span data-stu-id="467ae-329">Type parameters and constraints</span></span>

<span data-ttu-id="467ae-330">Se un tipo generico è dichiarato in più parti, ogni parte deve indicare i parametri di tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-330">If a generic type is declared in multiple parts, each part must state the type parameters.</span></span> <span data-ttu-id="467ae-331">Ogni parte deve avere lo stesso numero di parametri di tipo e lo stesso nome per ogni parametro di tipo, nell'ordine.</span><span class="sxs-lookup"><span data-stu-id="467ae-331">Each part must have the same number of type parameters, and the same name for each type parameter, in order.</span></span>

<span data-ttu-id="467ae-332">Quando una dichiarazione di tipo generico parziale include vincoli`where` (clausole), i vincoli devono essere concordati con tutte le altre parti che includono vincoli.</span><span class="sxs-lookup"><span data-stu-id="467ae-332">When a partial generic type declaration includes constraints (`where` clauses), the constraints must agree with all other parts that include constraints.</span></span> <span data-ttu-id="467ae-333">In particolare, ogni parte che include vincoli deve contenere vincoli per lo stesso set di parametri di tipo e per ogni parametro di tipo i set di vincoli primari, secondari e del costruttore devono essere equivalenti.</span><span class="sxs-lookup"><span data-stu-id="467ae-333">Specifically, each part that includes constraints must have constraints for the same set of type parameters, and for each type parameter the sets of primary, secondary, and constructor constraints must be equivalent.</span></span> <span data-ttu-id="467ae-334">Due set di vincoli sono equivalenti se contengono gli stessi membri.</span><span class="sxs-lookup"><span data-stu-id="467ae-334">Two sets of constraints are equivalent if they contain the same members.</span></span> <span data-ttu-id="467ae-335">Se nessuna parte di un tipo generico parziale specifica vincoli di parametro di tipo, i parametri di tipo vengono considerati non vincolati.</span><span class="sxs-lookup"><span data-stu-id="467ae-335">If no part of a partial generic type specifies type parameter constraints, the type parameters are considered unconstrained.</span></span>

<span data-ttu-id="467ae-336">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-336">The example</span></span>
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
<span data-ttu-id="467ae-337">è corretto perché le parti che includono vincoli (le prime due) specificano in modo efficace lo stesso set di vincoli PRIMARY, Secondary e Constructor per lo stesso set di parametri di tipo, rispettivamente.</span><span class="sxs-lookup"><span data-stu-id="467ae-337">is correct because those parts that include constraints (the first two) effectively specify the same set of primary, secondary, and constructor constraints for the same set of type parameters, respectively.</span></span>

### <a name="base-class"></a><span data-ttu-id="467ae-338">Classe base</span><span class="sxs-lookup"><span data-stu-id="467ae-338">Base class</span></span>

<span data-ttu-id="467ae-339">Quando una dichiarazione di classe parziale include una specifica della classe di base, deve essere conforme a tutte le altre parti che includono una specifica della classe base.</span><span class="sxs-lookup"><span data-stu-id="467ae-339">When a partial class declaration includes a base class specification it must agree with all other parts that include a base class specification.</span></span> <span data-ttu-id="467ae-340">Se nessuna parte di una classe parziale include una specifica della classe base, la classe di `System.Object` base diventa ([classi base](classes.md#base-classes)).</span><span class="sxs-lookup"><span data-stu-id="467ae-340">If no part of a partial class includes a base class specification, the base class becomes `System.Object` ([Base classes](classes.md#base-classes)).</span></span>

### <a name="base-interfaces"></a><span data-ttu-id="467ae-341">Interfacce di base</span><span class="sxs-lookup"><span data-stu-id="467ae-341">Base interfaces</span></span>

<span data-ttu-id="467ae-342">Il set di interfacce di base per un tipo dichiarato in più parti è l'Unione delle interfacce di base specificate in ogni parte.</span><span class="sxs-lookup"><span data-stu-id="467ae-342">The set of base interfaces for a type declared in multiple parts is the union of the base interfaces specified on each part.</span></span> <span data-ttu-id="467ae-343">Una particolare interfaccia di base può essere denominata una sola volta in ogni parte, ma è consentito per più parti denominare le stesse interfacce di base.</span><span class="sxs-lookup"><span data-stu-id="467ae-343">A particular base interface may only be named once on each part, but it is permitted for multiple parts to name the same base interface(s).</span></span> <span data-ttu-id="467ae-344">Deve essere presente solo un'implementazione dei membri di qualsiasi interfaccia di base specificata.</span><span class="sxs-lookup"><span data-stu-id="467ae-344">There must only be one implementation of the members of any given base interface.</span></span>

<span data-ttu-id="467ae-345">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-345">In the example</span></span>
```csharp
partial class C: IA, IB {...}

partial class C: IC {...}

partial class C: IA, IB {...}
```
<span data-ttu-id="467ae-346">il set di interfacce di base per `C` la `IA`classe `IB`è, `IC`e.</span><span class="sxs-lookup"><span data-stu-id="467ae-346">the set of base interfaces for class `C` is `IA`, `IB`, and `IC`.</span></span>

<span data-ttu-id="467ae-347">In genere, ogni parte fornisce un'implementazione delle interfacce dichiarate su tale parte; Tuttavia, questo non è un requisito.</span><span class="sxs-lookup"><span data-stu-id="467ae-347">Typically, each part provides an implementation of the interface(s) declared on that part; however, this is not a requirement.</span></span> <span data-ttu-id="467ae-348">Una parte può fornire l'implementazione per un'interfaccia dichiarata in una parte diversa:</span><span class="sxs-lookup"><span data-stu-id="467ae-348">A part may provide the implementation for an interface declared on a different part:</span></span>
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

### <a name="members"></a><span data-ttu-id="467ae-349">Members</span><span class="sxs-lookup"><span data-stu-id="467ae-349">Members</span></span>

<span data-ttu-id="467ae-350">Fatta eccezione per i metodi parziali ([metodi parziali](classes.md#partial-methods)), il set di membri di un tipo dichiarato in più parti è semplicemente l'Unione del set di membri dichiarato in ogni parte.</span><span class="sxs-lookup"><span data-stu-id="467ae-350">With the exception of partial methods ([Partial methods](classes.md#partial-methods)), the set of members of a type declared in multiple parts is simply the union of the set of members declared in each part.</span></span> <span data-ttu-id="467ae-351">I corpi di tutte le parti della dichiarazione di tipo condividono lo stesso spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)) e l'ambito di ogni membro ([ambiti](basic-concepts.md#scopes)) si estende ai corpi di tutte le parti.</span><span class="sxs-lookup"><span data-stu-id="467ae-351">The bodies of all parts of the type declaration share the same declaration space ([Declarations](basic-concepts.md#declarations)), and the scope of each member ([Scopes](basic-concepts.md#scopes)) extends to the bodies of all the parts.</span></span> <span data-ttu-id="467ae-352">Il dominio di accessibilità di qualsiasi membro include sempre tutte le parti del tipo di inclusione. un `private` membro dichiarato in una parte è accessibile liberamente da un'altra parte.</span><span class="sxs-lookup"><span data-stu-id="467ae-352">The accessibility domain of any member always includes all the parts of the enclosing type; a `private` member declared in one part is freely accessible from another part.</span></span> <span data-ttu-id="467ae-353">Si tratta di un errore in fase di compilazione per dichiarare lo stesso membro in più di una parte del tipo, a meno che tale membro non sia un `partial` tipo con il modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-353">It is a compile-time error to declare the same member in more than one part of the type, unless that member is a type with the `partial` modifier.</span></span>

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

<span data-ttu-id="467ae-354">L'ordinamento dei membri all'interno di un tipo è raramente C# significativo per il codice, ma può essere significativo in caso di interazione con altri linguaggi e ambienti.</span><span class="sxs-lookup"><span data-stu-id="467ae-354">The ordering of members within a type is rarely significant to C# code, but may be significant when interfacing with other languages and environments.</span></span> <span data-ttu-id="467ae-355">In questi casi, l'ordinamento dei membri all'interno di un tipo dichiarato in più parti non è definito.</span><span class="sxs-lookup"><span data-stu-id="467ae-355">In these cases, the ordering of members within a type declared in multiple parts is undefined.</span></span>

### <a name="partial-methods"></a><span data-ttu-id="467ae-356">Metodi parziali</span><span class="sxs-lookup"><span data-stu-id="467ae-356">Partial methods</span></span>

<span data-ttu-id="467ae-357">I metodi parziali possono essere definiti in una parte di una dichiarazione di tipo e implementati in un altro.</span><span class="sxs-lookup"><span data-stu-id="467ae-357">Partial methods can be defined in one part of a type declaration and implemented in another.</span></span> <span data-ttu-id="467ae-358">L'implementazione è facoltativa. Se nessuna parte implementa il metodo parziale, la dichiarazione del metodo parziale e tutte le chiamate a tale metodo vengono rimosse dalla dichiarazione del tipo risultante dalla combinazione delle parti.</span><span class="sxs-lookup"><span data-stu-id="467ae-358">The implementation is optional; if no part implements the partial method, the partial method declaration and all calls to it are removed from the type declaration resulting from the combination of the parts.</span></span>

<span data-ttu-id="467ae-359">I metodi parziali non possono definire modificatori di accesso, `private`ma sono implicitamente.</span><span class="sxs-lookup"><span data-stu-id="467ae-359">Partial methods cannot define access modifiers, but are implicitly `private`.</span></span> <span data-ttu-id="467ae-360">Il tipo restituito deve essere `void`e i relativi parametri non possono avere `out` il modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-360">Their return type must be `void`, and their parameters cannot have the `out` modifier.</span></span> <span data-ttu-id="467ae-361">L'identificatore `partial` viene riconosciuto come una parola chiave speciale in una dichiarazione di metodo solo se viene visualizzato immediatamente `void` prima del tipo; in caso contrario, può essere usato come identificatore normale.</span><span class="sxs-lookup"><span data-stu-id="467ae-361">The identifier `partial` is recognized as a special keyword in a method declaration only if it appears right before the `void` type; otherwise it can be used as a normal identifier.</span></span> <span data-ttu-id="467ae-362">Un metodo parziale non può implementare in modo esplicito i metodi di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="467ae-362">A partial method cannot explicitly implement interface methods.</span></span>

<span data-ttu-id="467ae-363">Esistono due tipi di dichiarazioni di metodo parziale: Se il corpo della dichiarazione di metodo è un punto e virgola, la dichiarazione viene ***definita come dichiarazione di metodo parziale***che lo definisce.</span><span class="sxs-lookup"><span data-stu-id="467ae-363">There are two kinds of partial method declarations: If the body of the method declaration is a semicolon, the declaration is said to be a ***defining partial method declaration***.</span></span> <span data-ttu-id="467ae-364">Se il corpo viene specificato come *blocco*, la dichiarazione viene definita come ***dichiarazione di metodo parziale di implementazione***.</span><span class="sxs-lookup"><span data-stu-id="467ae-364">If the body is given as a *block*, the declaration is said to be an ***implementing partial method declaration***.</span></span> <span data-ttu-id="467ae-365">Nelle parti di una dichiarazione di tipo può essere presente una sola dichiarazione di metodo parziale di definizione con una determinata firma. può essere presente una sola dichiarazione di implementazione del metodo parziale con una firma specificata.</span><span class="sxs-lookup"><span data-stu-id="467ae-365">Across the parts of a type declaration there can be only one defining partial method declaration with a given signature, and there can be only one implementing partial method declaration with a given signature.</span></span> <span data-ttu-id="467ae-366">Se viene fornita una dichiarazione di metodo parziale di implementazione, deve esistere una dichiarazione di metodo parziale di definizione corrispondente e le dichiarazioni devono corrispondere come specificato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="467ae-366">If an implementing partial method declaration is given, a corresponding defining partial method declaration must exist, and the declarations must match as specified in the following:</span></span>

* <span data-ttu-id="467ae-367">Le dichiarazioni devono avere gli stessi modificatori (anche se non necessariamente nello stesso ordine), il nome del metodo, il numero di parametri di tipo e il numero di parametri.</span><span class="sxs-lookup"><span data-stu-id="467ae-367">The declarations must have the same modifiers (although not necessarily in the same order), method name, number of type parameters and number of parameters.</span></span>
* <span data-ttu-id="467ae-368">I parametri corrispondenti nelle dichiarazioni devono avere gli stessi modificatori (anche se non necessariamente nello stesso ordine) e gli stessi tipi (differenze di modulo nei nomi dei parametri di tipo).</span><span class="sxs-lookup"><span data-stu-id="467ae-368">Corresponding parameters in the declarations must have the same modifiers (although not necessarily in the same order) and the same types (modulo differences in type parameter names).</span></span>
* <span data-ttu-id="467ae-369">I parametri di tipo corrispondenti nelle dichiarazioni devono avere gli stessi vincoli (differenze di modulo nei nomi dei parametri di tipo).</span><span class="sxs-lookup"><span data-stu-id="467ae-369">Corresponding type parameters in the declarations must have the same constraints (modulo differences in type parameter names).</span></span>

<span data-ttu-id="467ae-370">Una dichiarazione di metodo parziale di implementazione può comparire nella stessa parte della dichiarazione di metodo parziale di definizione corrispondente.</span><span class="sxs-lookup"><span data-stu-id="467ae-370">An implementing partial method declaration can appear in the same part as the corresponding defining partial method declaration.</span></span>

<span data-ttu-id="467ae-371">Solo un metodo parziale di definizione fa parte della risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="467ae-371">Only a defining partial method participates in overload resolution.</span></span> <span data-ttu-id="467ae-372">Di conseguenza, indipendentemente dal fatto che venga fornita una dichiarazione di implementazione, le espressioni di chiamata potrebbero risolversi in chiamate del metodo parziale.</span><span class="sxs-lookup"><span data-stu-id="467ae-372">Thus, whether or not an implementing declaration is given, invocation expressions may resolve to invocations of the partial method.</span></span> <span data-ttu-id="467ae-373">Poiché un metodo parziale restituisce `void`sempre, le espressioni di chiamata saranno sempre istruzioni di espressione.</span><span class="sxs-lookup"><span data-stu-id="467ae-373">Because a partial method always returns `void`, such invocation expressions will always be expression statements.</span></span> <span data-ttu-id="467ae-374">Inoltre, poiché un metodo parziale è implicitamente `private`, tali istruzioni si verificheranno sempre all'interno di una delle parti della dichiarazione di tipo all'interno della quale viene dichiarato il metodo parziale.</span><span class="sxs-lookup"><span data-stu-id="467ae-374">Furthermore, because a partial method is implicitly `private`, such statements will always occur within one of the parts of the type declaration within which the partial method is declared.</span></span>

<span data-ttu-id="467ae-375">Se nessuna parte di una dichiarazione di tipo parziale contiene una dichiarazione di implementazione per un determinato metodo parziale, qualsiasi istruzione di espressione che la richiama viene semplicemente rimossa dalla dichiarazione di tipo combinato.</span><span class="sxs-lookup"><span data-stu-id="467ae-375">If no part of a partial type declaration contains an implementing declaration for a given partial method, any expression statement invoking it is simply removed from the combined type declaration.</span></span> <span data-ttu-id="467ae-376">Pertanto, l'espressione di chiamata, incluse le espressioni costitutive, non ha alcun effetto in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="467ae-376">Thus the invocation expression, including any constituent expressions, has no effect at run-time.</span></span> <span data-ttu-id="467ae-377">Anche il metodo parziale viene rimosso e non sarà un membro della dichiarazione di tipo combinato.</span><span class="sxs-lookup"><span data-stu-id="467ae-377">The partial method itself is also removed and will not be a member of the combined type declaration.</span></span>

<span data-ttu-id="467ae-378">Se esiste una dichiarazione di implementazione per un determinato metodo parziale, le chiamate dei metodi parziali vengono mantenute.</span><span class="sxs-lookup"><span data-stu-id="467ae-378">If an implementing declaration exist for a given partial method, the invocations of the partial methods are retained.</span></span> <span data-ttu-id="467ae-379">Il metodo parziale genera una dichiarazione di metodo simile alla dichiarazione di metodo parziale di implementazione, ad eccezione di quanto segue:</span><span class="sxs-lookup"><span data-stu-id="467ae-379">The partial method gives rise to a method declaration similar to the implementing partial method declaration except for the following:</span></span>

* <span data-ttu-id="467ae-380">Il `partial` modificatore non è incluso</span><span class="sxs-lookup"><span data-stu-id="467ae-380">The `partial` modifier is not included</span></span>
* <span data-ttu-id="467ae-381">Gli attributi nella dichiarazione di metodo risultante sono gli attributi combinati della definizione e della dichiarazione di metodo parziale di implementazione in ordine non specificato.</span><span class="sxs-lookup"><span data-stu-id="467ae-381">The attributes in the resulting method declaration are the combined attributes of the defining and the implementing partial method declaration in unspecified order.</span></span> <span data-ttu-id="467ae-382">I duplicati non vengono rimossi.</span><span class="sxs-lookup"><span data-stu-id="467ae-382">Duplicates are not removed.</span></span>
* <span data-ttu-id="467ae-383">Gli attributi sui parametri della dichiarazione di metodo risultante sono gli attributi combinati dei parametri corrispondenti della definizione e la dichiarazione di metodo parziale di implementazione in ordine non specificato.</span><span class="sxs-lookup"><span data-stu-id="467ae-383">The attributes on the parameters of the resulting method declaration are the combined attributes of the corresponding parameters of the defining and the implementing partial method declaration in unspecified order.</span></span> <span data-ttu-id="467ae-384">I duplicati non vengono rimossi.</span><span class="sxs-lookup"><span data-stu-id="467ae-384">Duplicates are not removed.</span></span>

<span data-ttu-id="467ae-385">Se viene fornita una dichiarazione di definizione ma non una dichiarazione di implementazione per un metodo parziale M, si applicano le restrizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-385">If a defining declaration but not an implementing declaration is given for a partial method M, the following restrictions apply:</span></span>

* <span data-ttu-id="467ae-386">Si tratta di un errore in fase di compilazione per creare un delegato al metodo ([espressioni di creazione di delegati](expressions.md#delegate-creation-expressions)).</span><span class="sxs-lookup"><span data-stu-id="467ae-386">It is a compile-time error to create a delegate to method ([Delegate creation expressions](expressions.md#delegate-creation-expressions)).</span></span>
* <span data-ttu-id="467ae-387">Si tratta di un errore in fase di compilazione per `M` fare riferimento all'interno di una funzione anonima convertita in un tipo di albero delle espressioni ([valutazione delle conversioni di funzioni anonime nei tipi di albero delle espressioni](conversions.md#evaluation-of-anonymous-function-conversions-to-expression-tree-types)).</span><span class="sxs-lookup"><span data-stu-id="467ae-387">It is a compile-time error to refer to `M` inside an anonymous function that is converted to an expression tree type ([Evaluation of anonymous function conversions to expression tree types](conversions.md#evaluation-of-anonymous-function-conversions-to-expression-tree-types)).</span></span>
* <span data-ttu-id="467ae-388">Le espressioni che si verificano come parte di una `M` chiamata di non influiscono sullo stato di assegnazione definito ([assegnazione definita](variables.md#definite-assignment)), che può causare errori in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-388">Expressions occurring as part of an invocation of `M` do not affect the definite assignment state ([Definite assignment](variables.md#definite-assignment)), which can potentially lead to compile-time errors.</span></span>
* <span data-ttu-id="467ae-389">`M`non può essere il punto di ingresso di un'applicazione ([avvio dell'applicazione](basic-concepts.md#application-startup)).</span><span class="sxs-lookup"><span data-stu-id="467ae-389">`M` cannot be the entry point for an application ([Application Startup](basic-concepts.md#application-startup)).</span></span>

<span data-ttu-id="467ae-390">I metodi parziali sono utili per consentire a una parte di una dichiarazione di tipo di personalizzare il comportamento di un'altra parte, ad esempio, uno generato da uno strumento.</span><span class="sxs-lookup"><span data-stu-id="467ae-390">Partial methods are useful for allowing one part of a type declaration to customize the behavior of another part, e.g., one that is generated by a tool.</span></span> <span data-ttu-id="467ae-391">Si consideri la seguente dichiarazione di classe parziale:</span><span class="sxs-lookup"><span data-stu-id="467ae-391">Consider the following partial class declaration:</span></span>
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

<span data-ttu-id="467ae-392">Se questa classe viene compilata senza altre parti, le dichiarazioni di metodo parziale di definizione e le relative chiamate verranno rimosse e la dichiarazione della classe combinata risultante sarà equivalente alla seguente:</span><span class="sxs-lookup"><span data-stu-id="467ae-392">If this class is compiled without any other parts, the defining partial method declarations and their invocations will be removed, and the resulting combined class declaration will be equivalent to the following:</span></span>
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

<span data-ttu-id="467ae-393">Si supponga che, tuttavia, venga fornita un'altra parte, che fornisce dichiarazioni di implementazione dei metodi parziali:</span><span class="sxs-lookup"><span data-stu-id="467ae-393">Assume that another part is given, however, which provides implementing declarations of the partial methods:</span></span>
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

<span data-ttu-id="467ae-394">La dichiarazione di classe combinata risultante sarà equivalente alla seguente:</span><span class="sxs-lookup"><span data-stu-id="467ae-394">Then the resulting combined class declaration will be equivalent to the following:</span></span>
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

### <a name="name-binding"></a><span data-ttu-id="467ae-395">Associazione di nomi</span><span class="sxs-lookup"><span data-stu-id="467ae-395">Name binding</span></span>

<span data-ttu-id="467ae-396">Sebbene ogni parte di un tipo estensibile debba essere dichiarata all'interno dello stesso spazio dei nomi, le parti vengono in genere scritte in diverse dichiarazioni dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="467ae-396">Although each part of an extensible type must be declared within the same namespace, the parts are typically written within different namespace declarations.</span></span> <span data-ttu-id="467ae-397">È pertanto possibile `using` che siano presenti direttive diverse ([direttive using](namespaces.md#using-directives)) per ogni parte.</span><span class="sxs-lookup"><span data-stu-id="467ae-397">Thus, different `using` directives ([Using directives](namespaces.md#using-directives)) may be present for each part.</span></span> <span data-ttu-id="467ae-398">Quando si interpretano nomi semplici ([inferenza del tipo](expressions.md#type-inference)) in una parte `using` , vengono considerate solo le direttive delle dichiarazioni dello spazio dei nomi che racchiudono tale parte.</span><span class="sxs-lookup"><span data-stu-id="467ae-398">When interpreting simple names ([Type inference](expressions.md#type-inference)) within one part, only the `using` directives of the namespace declaration(s) enclosing that part are considered.</span></span> <span data-ttu-id="467ae-399">Ciò può comportare che lo stesso identificatore abbia significati diversi in parti diverse:</span><span class="sxs-lookup"><span data-stu-id="467ae-399">This may result in the same identifier having different meanings in different parts:</span></span>
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

## <a name="class-members"></a><span data-ttu-id="467ae-400">Membri della classe</span><span class="sxs-lookup"><span data-stu-id="467ae-400">Class members</span></span>

<span data-ttu-id="467ae-401">I membri di una classe sono costituiti dai membri introdotti dai relativi *class_member_declaration*e dai membri ereditati dalla classe di base diretta.</span><span class="sxs-lookup"><span data-stu-id="467ae-401">The members of a class consist of the members introduced by its *class_member_declaration*s and the members inherited from the direct base class.</span></span>

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

<span data-ttu-id="467ae-402">I membri di un tipo di classe sono divisi nelle categorie seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-402">The members of a class type are divided into the following categories:</span></span>

*  <span data-ttu-id="467ae-403">Costanti, che rappresentano i valori costanti associati alla classe ([costanti](classes.md#constants)).</span><span class="sxs-lookup"><span data-stu-id="467ae-403">Constants, which represent constant values associated with the class ([Constants](classes.md#constants)).</span></span>
*  <span data-ttu-id="467ae-404">Campi, ovvero le variabili della classe ([campi](classes.md#fields)).</span><span class="sxs-lookup"><span data-stu-id="467ae-404">Fields, which are the variables of the class ([Fields](classes.md#fields)).</span></span>
*  <span data-ttu-id="467ae-405">Metodi, che implementano i calcoli e le azioni che possono essere eseguiti dalla classe ([Metodi](classes.md#methods)).</span><span class="sxs-lookup"><span data-stu-id="467ae-405">Methods, which implement the computations and actions that can be performed by the class ([Methods](classes.md#methods)).</span></span>
*  <span data-ttu-id="467ae-406">Proprietà che definiscono le caratteristiche denominate e le azioni associate alla lettura e alla scrittura di tali caratteristiche ([Proprietà](classes.md#properties)).</span><span class="sxs-lookup"><span data-stu-id="467ae-406">Properties, which define named characteristics and the actions associated with reading and writing those characteristics ([Properties](classes.md#properties)).</span></span>
*  <span data-ttu-id="467ae-407">Eventi, che definiscono le notifiche che possono essere generate dalla classe ([eventi](classes.md#events)).</span><span class="sxs-lookup"><span data-stu-id="467ae-407">Events, which define notifications that can be generated by the class ([Events](classes.md#events)).</span></span>
*  <span data-ttu-id="467ae-408">Indicizzatori, che consentono di indicizzare le istanze della classe nello stesso modo (sintatticamente) come matrici ([indicizzatori](classes.md#indexers)).</span><span class="sxs-lookup"><span data-stu-id="467ae-408">Indexers, which permit instances of the class to be indexed in the same way (syntactically) as arrays ([Indexers](classes.md#indexers)).</span></span>
*  <span data-ttu-id="467ae-409">Operatori, che definiscono gli operatori di espressione che possono essere applicati alle istanze della classe ([operatori](classes.md#operators)).</span><span class="sxs-lookup"><span data-stu-id="467ae-409">Operators, which define the expression operators that can be applied to instances of the class ([Operators](classes.md#operators)).</span></span>
*  <span data-ttu-id="467ae-410">Costruttori di istanze, che implementano le azioni necessarie per inizializzare istanze della classe ([costruttori di istanza](classes.md#instance-constructors))</span><span class="sxs-lookup"><span data-stu-id="467ae-410">Instance constructors, which implement the actions required to initialize instances of the class ([Instance constructors](classes.md#instance-constructors))</span></span>
*  <span data-ttu-id="467ae-411">Distruttori, che implementano le azioni da eseguire prima che le istanze della classe vengano eliminate definitivamente ([distruttori](classes.md#destructors)).</span><span class="sxs-lookup"><span data-stu-id="467ae-411">Destructors, which implement the actions to be performed before instances of the class are permanently discarded ([Destructors](classes.md#destructors)).</span></span>
*  <span data-ttu-id="467ae-412">Costruttori statici, che implementano le azioni necessarie per inizializzare la classe stessa ([costruttori statici](classes.md#static-constructors)).</span><span class="sxs-lookup"><span data-stu-id="467ae-412">Static constructors, which implement the actions required to initialize the class itself ([Static constructors](classes.md#static-constructors)).</span></span>
*  <span data-ttu-id="467ae-413">Tipi, che rappresentano i tipi locali della classe ([tipi annidati](classes.md#nested-types)).</span><span class="sxs-lookup"><span data-stu-id="467ae-413">Types, which represent the types that are local to the class ([Nested types](classes.md#nested-types)).</span></span>

<span data-ttu-id="467ae-414">I membri che possono contenere codice eseguibile sono collettivamente noti come *membri di funzione* del tipo di classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-414">Members that can contain executable code are collectively known as the *function members* of the class type.</span></span> <span data-ttu-id="467ae-415">I membri della funzione di un tipo di classe sono i metodi, le proprietà, gli eventi, gli indicizzatori, gli operatori, i costruttori di istanza, i distruttori e i costruttori statici del tipo di classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-415">The function members of a class type are the methods, properties, events, indexers, operators, instance constructors,  destructors, and static constructors of that class type.</span></span>

<span data-ttu-id="467ae-416">Un *class_declaration* crea un nuovo spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)) e il *class_member_declaration*immediatamente contenuto da *class_declaration* introduce nuovi membri in questo spazio di dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-416">A *class_declaration* creates a new declaration space ([Declarations](basic-concepts.md#declarations)), and the *class_member_declaration*s immediately contained by the *class_declaration* introduce new members into this declaration space.</span></span> <span data-ttu-id="467ae-417">Le regole seguenti si applicano a *class_member_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="467ae-417">The following rules apply to *class_member_declaration*s:</span></span>

*  <span data-ttu-id="467ae-418">I costruttori di istanza, i distruttori e i costruttori statici devono avere lo stesso nome della classe che lo contiene immediatamente.</span><span class="sxs-lookup"><span data-stu-id="467ae-418">Instance constructors, destructors and static constructors must have the same name as the immediately enclosing class.</span></span> <span data-ttu-id="467ae-419">Tutti gli altri membri devono avere nomi diversi dal nome della classe che lo contiene immediatamente.</span><span class="sxs-lookup"><span data-stu-id="467ae-419">All other members must have names that differ from the name of the immediately enclosing class.</span></span>
*  <span data-ttu-id="467ae-420">Il nome di una costante, un campo, una proprietà, un evento o un tipo deve essere diverso dai nomi di tutti gli altri membri dichiarati nella stessa classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-420">The name of a constant, field, property, event, or type must differ from the names of all other members declared in the same class.</span></span>
*  <span data-ttu-id="467ae-421">Il nome di un metodo deve essere diverso da quello dei nomi di tutti gli altri metodi non dichiarati nella stessa classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-421">The name of a method must differ from the names of all other non-methods declared in the same class.</span></span> <span data-ttu-id="467ae-422">Inoltre, la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) di un metodo deve essere diversa dalle firme di tutti gli altri metodi dichiarati nella stessa classe e due metodi dichiarati nella stessa classe potrebbero non avere firme che differiscono esclusivamente da `ref` e `out`.</span><span class="sxs-lookup"><span data-stu-id="467ae-422">In addition, the signature ([Signatures and overloading](basic-concepts.md#signatures-and-overloading)) of a method must differ from the signatures of all other methods declared in the same class, and two methods declared in the same class may not have signatures that differ solely by `ref` and `out`.</span></span>
*  <span data-ttu-id="467ae-423">La firma di un costruttore di istanza deve essere diversa dalle firme di tutti gli altri costruttori di istanza dichiarati nella stessa classe e due costruttori dichiarati nella stessa classe potrebbero non avere firme che differiscono `out`solo per `ref` e.</span><span class="sxs-lookup"><span data-stu-id="467ae-423">The signature of an instance constructor must differ from the signatures of all other instance constructors declared in the same class, and two constructors declared in the same class may not have signatures that differ solely by `ref` and `out`.</span></span>
*  <span data-ttu-id="467ae-424">La firma di un indicizzatore deve essere diversa dalle firme di tutti gli altri indicizzatori dichiarati nella stessa classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-424">The signature of an indexer must differ from the signatures of all other indexers declared in the same class.</span></span>
*  <span data-ttu-id="467ae-425">La firma di un operatore deve essere diversa dalle firme di tutti gli altri operatori dichiarati nella stessa classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-425">The signature of an operator must differ from the signatures of all other operators declared in the same class.</span></span>

<span data-ttu-id="467ae-426">I membri ereditati di un tipo di classe ([ereditarietà](classes.md#inheritance)) non fanno parte dello spazio della dichiarazione di una classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-426">The inherited members of a class type ([Inheritance](classes.md#inheritance)) are not part of the declaration space of a class.</span></span> <span data-ttu-id="467ae-427">Pertanto, una classe derivata può dichiarare un membro con lo stesso nome o firma di un membro ereditato, che in effetti nasconde il membro ereditato.</span><span class="sxs-lookup"><span data-stu-id="467ae-427">Thus, a derived class is allowed to declare a member with the same name or signature as an inherited member (which in effect hides the inherited member).</span></span>

### <a name="the-instance-type"></a><span data-ttu-id="467ae-428">Tipo di istanza</span><span class="sxs-lookup"><span data-stu-id="467ae-428">The instance type</span></span>

<span data-ttu-id="467ae-429">A ogni dichiarazione di classe è associato un tipo associato ([tipi associati e non associati](types.md#bound-and-unbound-types)), il ***tipo di istanza***.</span><span class="sxs-lookup"><span data-stu-id="467ae-429">Each class declaration has an associated bound type ([Bound and unbound types](types.md#bound-and-unbound-types)), the ***instance type***.</span></span> <span data-ttu-id="467ae-430">Per una dichiarazione di classe generica, il tipo di istanza viene creato creando un tipo costruito ([tipi costruiti](types.md#constructed-types)) dalla dichiarazione del tipo, con ciascuno degli argomenti di tipo forniti che corrispondono al parametro di tipo corrispondente.</span><span class="sxs-lookup"><span data-stu-id="467ae-430">For a generic class declaration, the instance type is formed by creating a constructed type ([Constructed types](types.md#constructed-types)) from the type declaration, with each of the supplied type arguments being the corresponding type parameter.</span></span> <span data-ttu-id="467ae-431">Poiché il tipo di istanza utilizza i parametri di tipo, può essere utilizzato solo se i parametri di tipo sono inclusi nell'ambito. ovvero, all'interno della dichiarazione di classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-431">Since the instance type uses the type parameters, it can only be used where the type parameters are in scope; that is, inside the class declaration.</span></span> <span data-ttu-id="467ae-432">Il tipo di istanza è il tipo `this` di per il codice scritto all'interno della dichiarazione di classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-432">The instance type is the type of `this` for code written inside the class declaration.</span></span> <span data-ttu-id="467ae-433">Per le classi non generiche, il tipo di istanza è semplicemente la classe dichiarata.</span><span class="sxs-lookup"><span data-stu-id="467ae-433">For non-generic classes, the instance type is simply the declared class.</span></span> <span data-ttu-id="467ae-434">Di seguito vengono illustrate diverse dichiarazioni di classe insieme ai relativi tipi di istanza:</span><span class="sxs-lookup"><span data-stu-id="467ae-434">The following shows several class declarations along with their instance types:</span></span> 
```csharp
class A<T>                           // instance type: A<T>
{
    class B {}                       // instance type: A<T>.B
    class C<U> {}                    // instance type: A<T>.C<U>
}

class D {}                           // instance type: D
```

### <a name="members-of-constructed-types"></a><span data-ttu-id="467ae-435">Membri di tipi costruiti</span><span class="sxs-lookup"><span data-stu-id="467ae-435">Members of constructed types</span></span>

<span data-ttu-id="467ae-436">I membri non ereditati di un tipo costruito vengono ottenuti sostituendo, per ogni *type_parameter* nella dichiarazione del membro, il *type_argument* corrispondente del tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="467ae-436">The non-inherited members of a constructed type are obtained by substituting, for each *type_parameter* in the member declaration, the corresponding *type_argument* of the constructed type.</span></span> <span data-ttu-id="467ae-437">Il processo di sostituzione è basato sul significato semantico delle dichiarazioni di tipo e non è semplicemente una sostituzione testuale.</span><span class="sxs-lookup"><span data-stu-id="467ae-437">The substitution process is based on the semantic meaning of type declarations, and is not simply textual substitution.</span></span>

<span data-ttu-id="467ae-438">Ad esempio, data la dichiarazione di classe generica</span><span class="sxs-lookup"><span data-stu-id="467ae-438">For example, given the generic class declaration</span></span>
```csharp
class Gen<T,U>
{
    public T[,] a;
    public void G(int i, T t, Gen<U,T> gt) {...}
    public U Prop { get {...} set {...} }
    public int H(double d) {...}
}
```
<span data-ttu-id="467ae-439">il tipo `Gen<int[],IComparable<string>>` costruito ha i membri seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-439">the constructed type `Gen<int[],IComparable<string>>` has the following members:</span></span>
```csharp
public int[,][] a;
public void G(int i, int[] t, Gen<IComparable<string>,int[]> gt) {...}
public IComparable<string> Prop { get {...} set {...} }
public int H(double d) {...}
```

<span data-ttu-id="467ae-440">Il `a` tipo del membro nella Dichiarazione `Gen` di classe generica è "matrice bidimensionale di `T`", pertanto il tipo del membro `a` nel tipo costruito sopra è "matrice bidimensionale di una matrice unidimensionale di `int`", o `int[,][]`.</span><span class="sxs-lookup"><span data-stu-id="467ae-440">The type of the member `a` in the generic class declaration `Gen` is "two-dimensional array of `T`", so the type of the member `a` in the constructed type above is "two-dimensional array of one-dimensional array of `int`", or `int[,][]`.</span></span>

<span data-ttu-id="467ae-441">All'interno dei membri della funzione di istanza `this` , il tipo di è il tipo di istanza ([il tipo di istanza](classes.md#the-instance-type)) della dichiarazione che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="467ae-441">Within instance function members, the type of `this` is the instance type ([The instance type](classes.md#the-instance-type)) of the containing declaration.</span></span>

<span data-ttu-id="467ae-442">Tutti i membri di una classe generica possono usare parametri di tipo di qualsiasi classe contenitore, direttamente o come parte di un tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="467ae-442">All members of a generic class can use type parameters from any enclosing class, either directly or as part of a constructed type.</span></span> <span data-ttu-id="467ae-443">Quando un particolare tipo costruito chiuso ([tipi aperti e chiusi](types.md#open-and-closed-types)) viene utilizzato in fase di esecuzione, ogni utilizzo di un parametro di tipo viene sostituito con l'argomento di tipo effettivo fornito al tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="467ae-443">When a particular closed constructed type ([Open and closed types](types.md#open-and-closed-types)) is used at run-time, each use of a type parameter is replaced with the actual type argument supplied to the constructed type.</span></span> <span data-ttu-id="467ae-444">Esempio:</span><span class="sxs-lookup"><span data-stu-id="467ae-444">For example:</span></span>
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

### <a name="inheritance"></a><span data-ttu-id="467ae-445">Ereditarietà</span><span class="sxs-lookup"><span data-stu-id="467ae-445">Inheritance</span></span>

<span data-ttu-id="467ae-446">Una classe ***eredita*** i membri del tipo di classe di base diretta.</span><span class="sxs-lookup"><span data-stu-id="467ae-446">A class ***inherits*** the members of its direct base class type.</span></span> <span data-ttu-id="467ae-447">L'ereditarietà indica che una classe contiene in modo implicito tutti i membri del relativo tipo di classe di base diretta, ad eccezione dei costruttori di istanza, dei distruttori e dei costruttori statici della classe di base.</span><span class="sxs-lookup"><span data-stu-id="467ae-447">Inheritance means that a class implicitly contains all members of its direct base class type, except for the instance constructors, destructors and static constructors of the base class.</span></span> <span data-ttu-id="467ae-448">Alcuni aspetti importanti dell'ereditarietà sono:</span><span class="sxs-lookup"><span data-stu-id="467ae-448">Some important aspects of inheritance are:</span></span>

*  <span data-ttu-id="467ae-449">L'ereditarietà è transitiva.</span><span class="sxs-lookup"><span data-stu-id="467ae-449">Inheritance is transitive.</span></span> <span data-ttu-id="467ae-450">Se `C` è derivato da `B`ed `B` è derivato da `A`, `C` eredita i membri dichiarati in `B` , nonché i membri dichiarati `A`in.</span><span class="sxs-lookup"><span data-stu-id="467ae-450">If `C` is derived from `B`, and `B` is derived from `A`, then `C` inherits the members declared in `B` as well as the members declared in `A`.</span></span>
*  <span data-ttu-id="467ae-451">Una classe derivata estende la relativa classe di base diretta.</span><span class="sxs-lookup"><span data-stu-id="467ae-451">A derived class extends its direct base class.</span></span> <span data-ttu-id="467ae-452">Una classe derivata può aggiungere nuovi membri a quelli ereditati, ma non può rimuovere la definizione di un membro ereditato.</span><span class="sxs-lookup"><span data-stu-id="467ae-452">A derived class can add new members to those it inherits, but it cannot remove the definition of an inherited member.</span></span>
*  <span data-ttu-id="467ae-453">I costruttori di istanza, i distruttori e i costruttori statici non vengono ereditati, ma tutti gli altri membri sono, indipendentemente dall'accessibilità dichiarata ([accesso ai membri](basic-concepts.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="467ae-453">Instance constructors, destructors, and static constructors are not inherited, but all other members are, regardless of their declared accessibility ([Member access](basic-concepts.md#member-access)).</span></span> <span data-ttu-id="467ae-454">Tuttavia, a seconda dell'accessibilità dichiarata, i membri ereditati potrebbero non essere accessibili in una classe derivata.</span><span class="sxs-lookup"><span data-stu-id="467ae-454">However, depending on their declared accessibility, inherited members might not be accessible in a derived class.</span></span>
*  <span data-ttu-id="467ae-455">Una classe derivata può ***nascondere*** ([nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance)) i membri ereditati dichiarando i nuovi membri con lo stesso nome o la stessa firma.</span><span class="sxs-lookup"><span data-stu-id="467ae-455">A derived class can ***hide*** ([Hiding through inheritance](basic-concepts.md#hiding-through-inheritance)) inherited members by declaring new members with the same name or signature.</span></span> <span data-ttu-id="467ae-456">Si noti tuttavia che nascondere un membro ereditato non rimuove quel membro, ma rende semplicemente inaccessibile tale membro direttamente tramite la classe derivata.</span><span class="sxs-lookup"><span data-stu-id="467ae-456">Note however that hiding an inherited member does not remove that member—it merely makes that member inaccessible directly through the derived class.</span></span>
*  <span data-ttu-id="467ae-457">Un'istanza di una classe contiene un set di tutti i campi di istanza dichiarati nella classe e le relative classi di base e una conversione implicita ([conversioni di riferimenti implicite](conversions.md#implicit-reference-conversions)) esiste da un tipo di classe derivata a uno dei relativi tipi di classe di base.</span><span class="sxs-lookup"><span data-stu-id="467ae-457">An instance of a class contains a set of all instance fields declared in the class and its base classes, and an implicit conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) exists from a derived class type to any of its base class types.</span></span> <span data-ttu-id="467ae-458">Pertanto, un riferimento a un'istanza di una classe derivata può essere considerato come un riferimento a un'istanza di una delle relative classi di base.</span><span class="sxs-lookup"><span data-stu-id="467ae-458">Thus, a reference to an instance of some derived class can be treated as a reference to an instance of any of its base classes.</span></span>
*  <span data-ttu-id="467ae-459">Una classe può dichiarare metodi, proprietà e indicizzatori virtuali e le classi derivate possono eseguire l'override dell'implementazione di questi membri di funzione.</span><span class="sxs-lookup"><span data-stu-id="467ae-459">A class can declare virtual methods, properties, and indexers, and derived classes can override the implementation of these function members.</span></span> <span data-ttu-id="467ae-460">Ciò consente alle classi di presentare un comportamento polimorfico in cui le azioni eseguite dalla chiamata di un membro di funzione variano a seconda del tipo di runtime dell'istanza tramite cui viene richiamato il membro della funzione.</span><span class="sxs-lookup"><span data-stu-id="467ae-460">This enables classes to exhibit polymorphic behavior wherein the actions performed by a function member invocation varies depending on the run-time type of the instance through which that function member is invoked.</span></span>

<span data-ttu-id="467ae-461">Il membro ereditato di un tipo di classe costruito è costituito dai membri del tipo di classe di base immediato ([classi base](classes.md#base-classes)), che viene trovato sostituendo gli argomenti di tipo del tipo costruito per ogni occorrenza dei parametri di tipo corrispondenti nell'oggetto  *specifica class_base* .</span><span class="sxs-lookup"><span data-stu-id="467ae-461">The inherited member of a constructed class type are the members of the immediate base class type ([Base classes](classes.md#base-classes)), which is found by substituting the type arguments of the constructed type for each occurrence of the corresponding type parameters in the *class_base* specification.</span></span> <span data-ttu-id="467ae-462">Questi membri, a loro volta, vengono trasformati sostituendo, per ogni *type_parameter* nella dichiarazione del membro, il *type_argument* corrispondente della specifica *class_base* .</span><span class="sxs-lookup"><span data-stu-id="467ae-462">These members, in turn, are transformed by substituting, for each *type_parameter* in the member declaration, the corresponding *type_argument* of the *class_base* specification.</span></span>

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

<span data-ttu-id="467ae-463">Nell'esempio precedente, il `D<int>` tipo costruito ha un membro `public int G(string s)` non ereditato ottenuto sostituendo l'argomento `int` di tipo per il parametro `T`di tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-463">In the above example, the constructed type `D<int>` has a non-inherited member `public int G(string s)` obtained by substituting the type argument `int` for the type parameter `T`.</span></span> <span data-ttu-id="467ae-464">`D<int>`dispone anche di un membro ereditato dalla Dichiarazione `B`di classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-464">`D<int>` also has an inherited member from the class declaration `B`.</span></span> <span data-ttu-id="467ae-465">Questo membro ereditato è determinato dalla prima determinazione `B<int[]>` del tipo `D<int>` di classe di base di sostituendo `T` `int` per nella specifica `B<T[]>`della classe base.</span><span class="sxs-lookup"><span data-stu-id="467ae-465">This inherited member is determined by first determining the base class type `B<int[]>` of `D<int>` by substituting `int` for `T` in the base class specification `B<T[]>`.</span></span> <span data-ttu-id="467ae-466">Quindi, come argomento di tipo per `B`, `int[]` viene sostituito `U` con in `public U F(long index)`, cedendo il membro `public int[] F(long index)`ereditato.</span><span class="sxs-lookup"><span data-stu-id="467ae-466">Then, as a type argument to `B`, `int[]` is substituted for `U` in `public U F(long index)`, yielding the inherited member `public int[] F(long index)`.</span></span>

### <a name="the-new-modifier"></a><span data-ttu-id="467ae-467">Nuovo modificatore</span><span class="sxs-lookup"><span data-stu-id="467ae-467">The new modifier</span></span>

<span data-ttu-id="467ae-468">Un *class_member_declaration* è autorizzato a dichiarare un membro con lo stesso nome o firma di un membro ereditato.</span><span class="sxs-lookup"><span data-stu-id="467ae-468">A *class_member_declaration* is permitted to declare a member with the same name or signature as an inherited member.</span></span> <span data-ttu-id="467ae-469">Quando si verifica questo problema, viene detto che il membro della classe derivata ***nasconde*** il membro della classe base.</span><span class="sxs-lookup"><span data-stu-id="467ae-469">When this occurs, the derived class member is said to ***hide*** the base class member.</span></span> <span data-ttu-id="467ae-470">Nascondere un membro ereditato non viene considerato un errore, ma il compilatore genera un avviso.</span><span class="sxs-lookup"><span data-stu-id="467ae-470">Hiding an inherited member is not considered an error, but it does cause the compiler to issue a warning.</span></span> <span data-ttu-id="467ae-471">Per non visualizzare l'avviso, la dichiarazione del membro della classe derivata può includere `new` un modificatore per indicare che il membro derivato deve nascondere il membro di base.</span><span class="sxs-lookup"><span data-stu-id="467ae-471">To suppress the warning, the declaration of the derived class member can include a `new` modifier to indicate that the derived member is intended to hide the base member.</span></span> <span data-ttu-id="467ae-472">Questo argomento viene trattato ulteriormente in modo [nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance).</span><span class="sxs-lookup"><span data-stu-id="467ae-472">This topic is discussed further in [Hiding through inheritance](basic-concepts.md#hiding-through-inheritance).</span></span>

<span data-ttu-id="467ae-473">Se un `new` modificatore è incluso in una dichiarazione che non nasconde un membro ereditato, viene emesso un avviso a tale effetto.</span><span class="sxs-lookup"><span data-stu-id="467ae-473">If a `new` modifier is included in a declaration that doesn't hide an inherited member, a warning to that effect is issued.</span></span> <span data-ttu-id="467ae-474">Questo avviso viene eliminato rimuovendo il `new` modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-474">This warning is suppressed by removing the `new` modifier.</span></span>

### <a name="access-modifiers"></a><span data-ttu-id="467ae-475">Modificatori di accesso</span><span class="sxs-lookup"><span data-stu-id="467ae-475">Access modifiers</span></span>

<span data-ttu-id="467ae-476">Un *class_member_declaration* può avere uno dei cinque tipi possibili di accessibilità dichiarata ([accessibilità](basic-concepts.md#declared-accessibility)dichiarata): `public`, `protected internal`, `protected`, `internal` o `private`.</span><span class="sxs-lookup"><span data-stu-id="467ae-476">A *class_member_declaration* can have any one of the five possible kinds of declared accessibility ([Declared accessibility](basic-concepts.md#declared-accessibility)): `public`, `protected internal`, `protected`, `internal`, or `private`.</span></span> <span data-ttu-id="467ae-477">Ad eccezione della `protected internal` combinazione, si tratta di un errore in fase di compilazione per specificare più di un modificatore di accesso.</span><span class="sxs-lookup"><span data-stu-id="467ae-477">Except for the `protected internal` combination, it is a compile-time error to specify more than one access modifier.</span></span> <span data-ttu-id="467ae-478">Quando un *class_member_declaration* non include modificatori di accesso, si presuppone `private`.</span><span class="sxs-lookup"><span data-stu-id="467ae-478">When a *class_member_declaration* does not include any access modifiers, `private` is assumed.</span></span>

### <a name="constituent-types"></a><span data-ttu-id="467ae-479">Tipi costitutivi</span><span class="sxs-lookup"><span data-stu-id="467ae-479">Constituent types</span></span>

<span data-ttu-id="467ae-480">I tipi utilizzati nella dichiarazione di un membro sono denominati tipi costitutivi di tale membro.</span><span class="sxs-lookup"><span data-stu-id="467ae-480">Types that are used in the declaration of a member are called the constituent types of that member.</span></span> <span data-ttu-id="467ae-481">I tipi costitutivi possibili sono il tipo di una costante, un campo, una proprietà, un evento o un indicizzatore, il tipo restituito di un metodo o un operatore e i tipi di parametro di un metodo, un indicizzatore, un operatore o un costruttore di istanza.</span><span class="sxs-lookup"><span data-stu-id="467ae-481">Possible constituent types are the type of a constant, field, property, event, or indexer, the return type of a method or operator, and the parameter types of a method, indexer, operator, or instance constructor.</span></span> <span data-ttu-id="467ae-482">I tipi costitutivi di un membro devono essere accessibili almeno quanto il membro stesso ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="467ae-482">The constituent types of a member must be at least as accessible as that member itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

### <a name="static-and-instance-members"></a><span data-ttu-id="467ae-483">Membri statici e di istanza</span><span class="sxs-lookup"><span data-stu-id="467ae-483">Static and instance members</span></span>

<span data-ttu-id="467ae-484">I membri di una classe sono ***membri statici*** o ***membri di istanza***.</span><span class="sxs-lookup"><span data-stu-id="467ae-484">Members of a class are either ***static members*** or ***instance members***.</span></span> <span data-ttu-id="467ae-485">In generale, è utile considerare i membri statici come appartenenti ai tipi di classe e ai membri di istanza come appartenenti a oggetti (istanze di tipi di classe).</span><span class="sxs-lookup"><span data-stu-id="467ae-485">Generally speaking, it is useful to think of static members as belonging to class types and instance members as belonging to objects (instances of class types).</span></span>

<span data-ttu-id="467ae-486">Quando un campo, un metodo, una proprietà, un evento, un operatore o una `static` dichiarazione di costruttore include un modificatore, dichiara un membro statico.</span><span class="sxs-lookup"><span data-stu-id="467ae-486">When a field, method, property, event, operator, or constructor declaration includes a `static` modifier, it declares a static member.</span></span> <span data-ttu-id="467ae-487">Inoltre, una costante o una dichiarazione di tipo dichiara in modo implicito un membro statico.</span><span class="sxs-lookup"><span data-stu-id="467ae-487">In addition, a constant or type declaration implicitly declares a static member.</span></span> <span data-ttu-id="467ae-488">I membri statici presentano le seguenti caratteristiche:</span><span class="sxs-lookup"><span data-stu-id="467ae-488">Static members have the following characteristics:</span></span>

*  <span data-ttu-id="467ae-489">Quando si fa riferimento a un membro statico `M` in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, `E` deve indicare un tipo contenente `M`.</span><span class="sxs-lookup"><span data-stu-id="467ae-489">When a static member `M` is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, `E` must denote a type containing `M`.</span></span> <span data-ttu-id="467ae-490">Si tratta di un errore in fase di `E` compilazione per la denotazione di un'istanza di.</span><span class="sxs-lookup"><span data-stu-id="467ae-490">It is a compile-time error for `E` to denote an instance.</span></span>
*  <span data-ttu-id="467ae-491">Un campo statico identifica esattamente un percorso di archiviazione da condividere con tutte le istanze di un determinato tipo di classe closed.</span><span class="sxs-lookup"><span data-stu-id="467ae-491">A static field identifies exactly one storage location to be shared by all instances of a given closed class type.</span></span> <span data-ttu-id="467ae-492">Indipendentemente dal numero di istanze di un determinato tipo di classe chiusa, esiste una sola copia di un campo statico.</span><span class="sxs-lookup"><span data-stu-id="467ae-492">No matter how many instances of a given closed class type are created, there is only ever one copy of a static field.</span></span>
*  <span data-ttu-id="467ae-493">Un membro di funzione statica (metodo, proprietà, evento, operatore o costruttore) non funziona in un'istanza specifica e si tratta di un errore in fase di compilazione a cui fare `this` riferimento in un membro di funzione di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-493">A static function member (method, property, event, operator, or constructor) does not operate on a specific instance, and it is a compile-time error to refer to `this` in such a function member.</span></span>

<span data-ttu-id="467ae-494">Quando un campo, un metodo, una proprietà, un evento, un indicizzatore, un costruttore o una dichiarazione di distruttore non include un `static` modificatore, dichiara un membro di istanza.</span><span class="sxs-lookup"><span data-stu-id="467ae-494">When a field, method, property, event, indexer, constructor, or destructor declaration does not include a `static` modifier, it declares an instance member.</span></span> <span data-ttu-id="467ae-495">Un membro di istanza viene talvolta denominato membro non statico. I membri di istanza hanno le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-495">(An instance member is sometimes called a non-static member.) Instance members have the following characteristics:</span></span>

*  <span data-ttu-id="467ae-496">Quando si fa riferimento a un membro di istanza `M` in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, `E` deve indicare un'istanza di un tipo contenente `M`.</span><span class="sxs-lookup"><span data-stu-id="467ae-496">When an instance member `M` is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, `E` must denote an instance of a type containing `M`.</span></span> <span data-ttu-id="467ae-497">Si tratta di un errore in fase di `E` binding per per indicare un tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-497">It is a binding-time error for `E` to denote a type.</span></span>
*  <span data-ttu-id="467ae-498">Ogni istanza di una classe contiene un set separato di tutti i campi di istanza della classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-498">Every instance of a class contains a separate set of all instance fields of the class.</span></span>
*  <span data-ttu-id="467ae-499">Un membro della funzione di istanza (metodo, proprietà, indicizzatore, costruttore di istanza o distruttore) opera su un'istanza specificata della classe e questa istanza è accessibile come `this` ([questo accesso](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="467ae-499">An instance function member (method, property, indexer, instance constructor, or destructor) operates on a given instance of the class, and this instance can be accessed as `this` ([This access](expressions.md#this-access)).</span></span>

<span data-ttu-id="467ae-500">Nell'esempio seguente vengono illustrate le regole per l'accesso ai membri statici e di istanza:</span><span class="sxs-lookup"><span data-stu-id="467ae-500">The following example illustrates the rules for accessing static and instance members:</span></span>
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

<span data-ttu-id="467ae-501">Il metodo `F` Mostra che in un membro della funzione di istanza, un *simple_name* ([nomi semplici](expressions.md#simple-names)) può essere usato per accedere ai membri di istanza e ai membri statici.</span><span class="sxs-lookup"><span data-stu-id="467ae-501">The `F` method shows that in an instance function member, a *simple_name* ([Simple names](expressions.md#simple-names)) can be used to access both instance members and static members.</span></span> <span data-ttu-id="467ae-502">Il metodo `G` Mostra che in un membro della funzione statica, si tratta di un errore in fase di compilazione per accedere a un membro di istanza tramite un *simple_name*.</span><span class="sxs-lookup"><span data-stu-id="467ae-502">The `G` method shows that in a static function member, it is a compile-time error to access an instance member through a *simple_name*.</span></span> <span data-ttu-id="467ae-503">Il metodo `Main` Mostra che in un *member_access* ([accesso ai membri](expressions.md#member-access)), i membri dell'istanza devono essere accessibili tramite le istanze e i membri statici devono essere accessibili tramite i tipi.</span><span class="sxs-lookup"><span data-stu-id="467ae-503">The `Main` method shows that in a *member_access* ([Member access](expressions.md#member-access)), instance members must be accessed through instances, and static members must be accessed through types.</span></span>

### <a name="nested-types"></a><span data-ttu-id="467ae-504">Tipi annidati</span><span class="sxs-lookup"><span data-stu-id="467ae-504">Nested types</span></span>

<span data-ttu-id="467ae-505">Un tipo dichiarato all'interno di una dichiarazione di classe o struct è denominato ***tipo annidato***.</span><span class="sxs-lookup"><span data-stu-id="467ae-505">A type declared within a class or struct declaration is called a ***nested type***.</span></span> <span data-ttu-id="467ae-506">Un tipo dichiarato all'interno di un'unità di compilazione o di uno spazio dei nomi viene chiamato ***tipo non annidato***.</span><span class="sxs-lookup"><span data-stu-id="467ae-506">A type that is declared within a compilation unit or namespace is called a ***non-nested type***.</span></span>

<span data-ttu-id="467ae-507">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-507">In the example</span></span>
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
<span data-ttu-id="467ae-508">la `B` classe è un tipo annidato perché viene dichiarata all'interno `A` della classe `A`e la classe è un tipo non annidato perché viene dichiarata all'interno di un'unità di compilazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-508">class `B` is a nested type because it is declared within class `A`, and class `A` is a non-nested type because it is declared within a compilation unit.</span></span>

#### <a name="fully-qualified-name"></a><span data-ttu-id="467ae-509">Nome completo</span><span class="sxs-lookup"><span data-stu-id="467ae-509">Fully qualified name</span></span>

<span data-ttu-id="467ae-510">Il nome completo ([nomi](basic-concepts.md#fully-qualified-names)completi) per un tipo annidato è `S.N` dove `S` è il nome completo del tipo in cui è dichiarato il tipo `N` .</span><span class="sxs-lookup"><span data-stu-id="467ae-510">The fully qualified name ([Fully qualified names](basic-concepts.md#fully-qualified-names)) for a nested type is `S.N` where `S` is the fully qualified name of the type in which type `N` is declared.</span></span>

#### <a name="declared-accessibility"></a><span data-ttu-id="467ae-511">Accessibilità dichiarata</span><span class="sxs-lookup"><span data-stu-id="467ae-511">Declared accessibility</span></span>

<span data-ttu-id="467ae-512">I tipi non annidati possono `public` avere `internal` o dichiarati `internal` accessibilità e hanno dichiarato l'accessibilità per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="467ae-512">Non-nested types can have `public` or `internal` declared accessibility and have `internal` declared accessibility by default.</span></span> <span data-ttu-id="467ae-513">I tipi annidati possono avere anche queste forme di accessibilità dichiarata, oltre a una o più forme aggiuntive di accessibilità dichiarate, a seconda che il tipo che lo contiene sia una classe o uno struct:</span><span class="sxs-lookup"><span data-stu-id="467ae-513">Nested types can have these forms of declared accessibility too, plus one or more additional forms of declared accessibility, depending on whether the containing type is a class or struct:</span></span>

*  <span data-ttu-id="467ae-514">Un tipo annidato dichiarato in una classe può avere una qualsiasi delle cinque forme di accessibilità dichiarata `protected`( `internal``public`, `protected internal`, `private`, o) e, come altri membri della classe, `private` il valore predefinito è dichiarata. accessibilità.</span><span class="sxs-lookup"><span data-stu-id="467ae-514">A nested type that is declared in a class can have any of five forms of declared accessibility (`public`, `protected internal`, `protected`, `internal`, or `private`) and, like other class members, defaults to `private` declared accessibility.</span></span>
*  <span data-ttu-id="467ae-515">Un tipo annidato dichiarato in uno struct può avere tre forme di accessibilità dichiarata (`public`, `internal`o `private`) e, come altri membri struct, per `private` impostazione predefinita è l'accessibilità dichiarata.</span><span class="sxs-lookup"><span data-stu-id="467ae-515">A nested type that is declared in a struct can have any of three forms of declared accessibility (`public`, `internal`, or `private`) and, like other struct members, defaults to `private` declared accessibility.</span></span>

<span data-ttu-id="467ae-516">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-516">The example</span></span>
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
<span data-ttu-id="467ae-517">dichiara una classe `Node`annidata privata.</span><span class="sxs-lookup"><span data-stu-id="467ae-517">declares a private nested class `Node`.</span></span>

#### <a name="hiding"></a><span data-ttu-id="467ae-518">Nascondere</span><span class="sxs-lookup"><span data-stu-id="467ae-518">Hiding</span></span>

<span data-ttu-id="467ae-519">Un tipo annidato può nascondere[, ovvero nascondere, un](basic-concepts.md#name-hiding)membro di base.</span><span class="sxs-lookup"><span data-stu-id="467ae-519">A nested type may hide ([Name hiding](basic-concepts.md#name-hiding)) a base member.</span></span> <span data-ttu-id="467ae-520">Il `new` modificatore è consentito nelle dichiarazioni di tipo annidato in modo che il nascondiglio possa essere espresso in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="467ae-520">The `new` modifier is permitted on nested type declarations so that hiding can be expressed explicitly.</span></span> <span data-ttu-id="467ae-521">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-521">The example</span></span>
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
<span data-ttu-id="467ae-522">Mostra una classe `M` annidata che nasconde il metodo `M` definito in `Base`.</span><span class="sxs-lookup"><span data-stu-id="467ae-522">shows a nested class `M` that hides the method `M` defined in `Base`.</span></span>

#### <a name="this-access"></a><span data-ttu-id="467ae-523">Questo accesso</span><span class="sxs-lookup"><span data-stu-id="467ae-523">this access</span></span>

<span data-ttu-id="467ae-524">Un tipo annidato e il relativo tipo che lo contiene non hanno una relazione speciale per quanto riguarda *THIS_ACCESS* ([questo accesso](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="467ae-524">A nested type and its containing type do not have a special relationship with regard to *this_access* ([This access](expressions.md#this-access)).</span></span> <span data-ttu-id="467ae-525">In particolare `this` , all'interno di un tipo annidato non può essere usato per fare riferimento ai membri di istanza del tipo che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="467ae-525">Specifically, `this` within a nested type cannot be used to refer to instance members of the containing type.</span></span> <span data-ttu-id="467ae-526">Nei casi in cui un tipo annidato deve accedere ai membri dell'istanza del tipo che lo contiene, è possibile fornire l' `this` accesso specificando l'oggetto per l'istanza del tipo che lo contiene come argomento del costruttore per il tipo annidato.</span><span class="sxs-lookup"><span data-stu-id="467ae-526">In cases where a nested type needs access to the instance members of its containing type, access can be provided by providing the `this` for the instance of the containing type as a constructor argument for the nested type.</span></span> <span data-ttu-id="467ae-527">Nell'esempio seguente</span><span class="sxs-lookup"><span data-stu-id="467ae-527">The following example</span></span>
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
<span data-ttu-id="467ae-528">Mostra questa tecnica.</span><span class="sxs-lookup"><span data-stu-id="467ae-528">shows this technique.</span></span> <span data-ttu-id="467ae-529">Un'istanza di `C` crea un'istanza di `Nested` e passa il relativo `this` costruttore `Nested`a per fornire un accesso successivo ai `C`membri dell'istanza di.</span><span class="sxs-lookup"><span data-stu-id="467ae-529">An instance of `C` creates an instance of `Nested` and passes its own `this` to `Nested`'s constructor in order to provide subsequent access to `C`'s instance members.</span></span>

#### <a name="access-to-private-and-protected-members-of-the-containing-type"></a><span data-ttu-id="467ae-530">Accesso a membri privati e protetti del tipo che lo contiene</span><span class="sxs-lookup"><span data-stu-id="467ae-530">Access to private and protected members of the containing type</span></span>

<span data-ttu-id="467ae-531">Un tipo annidato può accedere a tutti i membri accessibili al tipo che lo contiene, inclusi i membri del tipo che `private` lo contiene e `protected` l'accessibilità dichiarata.</span><span class="sxs-lookup"><span data-stu-id="467ae-531">A nested type has access to all of the members that are accessible to its containing type, including members of the containing type that have `private` and `protected` declared accessibility.</span></span> <span data-ttu-id="467ae-532">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-532">The example</span></span>
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
<span data-ttu-id="467ae-533">Mostra una classe `C` che contiene una classe `Nested`annidata.</span><span class="sxs-lookup"><span data-stu-id="467ae-533">shows a class `C` that contains a nested class `Nested`.</span></span> <span data-ttu-id="467ae-534">All' `Nested`interno di, `G` il metodo chiama il `F` metodo statico `C`definito in `F` e ha un'accessibilità dichiarata privata.</span><span class="sxs-lookup"><span data-stu-id="467ae-534">Within `Nested`, the method `G` calls the static method `F` defined in `C`, and `F` has private declared accessibility.</span></span>

<span data-ttu-id="467ae-535">Un tipo annidato può accedere anche a membri protetti definiti in un tipo di base del tipo che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="467ae-535">A nested type also may access protected members defined in a base type of its containing type.</span></span> <span data-ttu-id="467ae-536">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-536">In the example</span></span>
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
<span data-ttu-id="467ae-537">la `Derived.Nested` classe annidata accede al metodo `F` protetto definito nella `Derived`classe di base, `Base`, chiamando tramite un'istanza di `Derived`.</span><span class="sxs-lookup"><span data-stu-id="467ae-537">the nested class `Derived.Nested` accesses the protected method `F` defined in `Derived`'s base class, `Base`, by calling through an instance of `Derived`.</span></span>

#### <a name="nested-types-in-generic-classes"></a><span data-ttu-id="467ae-538">Tipi annidati in classi generiche</span><span class="sxs-lookup"><span data-stu-id="467ae-538">Nested types in generic classes</span></span>

<span data-ttu-id="467ae-539">Una dichiarazione di classe generica può contenere dichiarazioni di tipo annidate.</span><span class="sxs-lookup"><span data-stu-id="467ae-539">A generic class declaration can contain nested type declarations.</span></span> <span data-ttu-id="467ae-540">I parametri di tipo della classe contenitore possono essere utilizzati all'interno dei tipi annidati.</span><span class="sxs-lookup"><span data-stu-id="467ae-540">The type parameters of the enclosing class can be used within the nested types.</span></span> <span data-ttu-id="467ae-541">Una dichiarazione di tipo annidato può contenere parametri di tipo aggiuntivi che si applicano solo al tipo annidato.</span><span class="sxs-lookup"><span data-stu-id="467ae-541">A nested type declaration can contain additional type parameters that apply only to the nested type.</span></span>

<span data-ttu-id="467ae-542">Ogni dichiarazione di tipo contenuta in una dichiarazione di classe generica è implicitamente una dichiarazione di tipo generico.</span><span class="sxs-lookup"><span data-stu-id="467ae-542">Every type declaration contained within a generic class declaration is implicitly a generic type declaration.</span></span> <span data-ttu-id="467ae-543">Quando si scrive un riferimento a un tipo annidato all'interno di un tipo generico, il tipo costruito che lo contiene, inclusi i relativi argomenti di tipo, deve essere denominato.</span><span class="sxs-lookup"><span data-stu-id="467ae-543">When writing a reference to a type nested within a generic type, the containing constructed type, including its type arguments, must be named.</span></span> <span data-ttu-id="467ae-544">Tuttavia, dall'interno della classe esterna, il tipo annidato può essere usato senza qualificazione; il tipo di istanza della classe esterna può essere utilizzato in modo implicito quando si costruisce il tipo annidato.</span><span class="sxs-lookup"><span data-stu-id="467ae-544">However, from within the outer class, the nested type can be used without qualification; the instance type of the outer class can be implicitly used when constructing the nested type.</span></span> <span data-ttu-id="467ae-545">Nell'esempio seguente vengono illustrate tre diversi modi corretti per fare riferimento a un tipo `Inner`costruito creato da. i primi due sono equivalenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-545">The following example shows three different correct ways to refer to a constructed type created from `Inner`; the first two are equivalent:</span></span>
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

<span data-ttu-id="467ae-546">Sebbene non sia uno stile di programmazione valido, un parametro di tipo in un tipo annidato può nascondere un membro o un parametro di tipo dichiarato nel tipo esterno:</span><span class="sxs-lookup"><span data-stu-id="467ae-546">Although it is bad programming style, a type parameter in a nested type can hide a member or type parameter declared in the outer type:</span></span>
```csharp
class Outer<T>
{
    class Inner<T>        // Valid, hides Outer's T
    {
        public T t;       // Refers to Inner's T
    }
}
```

### <a name="reserved-member-names"></a><span data-ttu-id="467ae-547">Nomi di membri riservati</span><span class="sxs-lookup"><span data-stu-id="467ae-547">Reserved member names</span></span>

<span data-ttu-id="467ae-548">Per semplificare l'implementazione C# della fase di esecuzione sottostante, per ogni dichiarazione del membro di origine che è una proprietà, un evento o un indicizzatore, l'implementazione deve riservare due firme del metodo in base al tipo di dichiarazione del membro, al nome e al tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-548">To facilitate the underlying C# run-time implementation, for each source member declaration that is a property, event, or indexer, the implementation must reserve two method signatures based on the kind of the member declaration, its name, and its type.</span></span> <span data-ttu-id="467ae-549">Si tratta di un errore in fase di compilazione per un programma che dichiara un membro la cui firma corrisponde a una di queste firme riservate, anche se l'implementazione della fase di esecuzione sottostante non utilizza tali prenotazioni.</span><span class="sxs-lookup"><span data-stu-id="467ae-549">It is a compile-time error for a program to declare a member whose signature matches one of these reserved signatures, even if the underlying run-time implementation does not make use of these reservations.</span></span>

<span data-ttu-id="467ae-550">I nomi riservati non introducono dichiarazioni, quindi non partecipano alla ricerca di membri.</span><span class="sxs-lookup"><span data-stu-id="467ae-550">The reserved names do not introduce declarations, thus they do not participate in member lookup.</span></span> <span data-ttu-id="467ae-551">Tuttavia, le firme del metodo riservato associate a una dichiarazione partecipano all'ereditarietà ([ereditarietà](classes.md#inheritance)) e possono essere nascoste con `new` il modificatore ([il nuovo modificatore](classes.md#the-new-modifier)).</span><span class="sxs-lookup"><span data-stu-id="467ae-551">However, a declaration's associated reserved method signatures do participate in inheritance ([Inheritance](classes.md#inheritance)), and can be hidden with the `new` modifier ([The new modifier](classes.md#the-new-modifier)).</span></span>

<span data-ttu-id="467ae-552">La prenotazione di questi nomi svolge tre scopi:</span><span class="sxs-lookup"><span data-stu-id="467ae-552">The reservation of these names serves three purposes:</span></span>

*  <span data-ttu-id="467ae-553">Per consentire all'implementazione sottostante di usare un identificatore comune come nome di metodo per ottenere o impostare l'accesso alla C# funzionalità del linguaggio.</span><span class="sxs-lookup"><span data-stu-id="467ae-553">To allow the underlying implementation to use an ordinary identifier as a method name for get or set access to the C# language feature.</span></span>
*  <span data-ttu-id="467ae-554">Per consentire ad altre lingue di interagire con un identificatore ordinario come nome di metodo per ottenere o impostare l'accesso C# alla funzionalità del linguaggio.</span><span class="sxs-lookup"><span data-stu-id="467ae-554">To allow other languages to interoperate using an ordinary identifier as a method name for get or set access to the C# language feature.</span></span>
*  <span data-ttu-id="467ae-555">Per garantire che l'origine accettata da un compilatore conforme venga accettata da un'altra, rendendo le specifiche dei nomi di membro riservati coerenti in tutte C# le implementazioni.</span><span class="sxs-lookup"><span data-stu-id="467ae-555">To help ensure that the source accepted by one conforming compiler is accepted by another, by making the specifics of reserved member names consistent across all C# implementations.</span></span>

<span data-ttu-id="467ae-556">La dichiarazione di un distruttore ([distruttori](classes.md#destructors)) causa anche la riservatezza di una firma ([nomi dei membri riservati ai distruttori](classes.md#member-names-reserved-for-destructors)).</span><span class="sxs-lookup"><span data-stu-id="467ae-556">The declaration of a destructor ([Destructors](classes.md#destructors)) also causes a signature to be reserved ([Member names reserved for destructors](classes.md#member-names-reserved-for-destructors)).</span></span>

#### <a name="member-names-reserved-for-properties"></a><span data-ttu-id="467ae-557">Nomi dei membri riservati per le proprietà</span><span class="sxs-lookup"><span data-stu-id="467ae-557">Member names reserved for properties</span></span>

<span data-ttu-id="467ae-558">Per una proprietà `P` ([proprietà](classes.md#properties)) di tipo `T`, sono riservate le firme seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-558">For a property `P` ([Properties](classes.md#properties)) of type `T`, the following signatures are reserved:</span></span>

```csharp
T get_P();
void set_P(T value);
```

<span data-ttu-id="467ae-559">Entrambe le firme sono riservate, anche se la proprietà è di sola lettura o di sola scrittura.</span><span class="sxs-lookup"><span data-stu-id="467ae-559">Both signatures are reserved, even if the property is read-only or write-only.</span></span>

<span data-ttu-id="467ae-560">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-560">In the example</span></span>
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
<span data-ttu-id="467ae-561">una classe `A` definisce una proprietà `P`di sola lettura, riservando in tal modo le `get_P` firme per i metodi e `set_P` .</span><span class="sxs-lookup"><span data-stu-id="467ae-561">a class `A` defines a read-only property `P`, thus reserving signatures for `get_P` and `set_P` methods.</span></span> <span data-ttu-id="467ae-562">Una classe `B` deriva da `A` e nasconde entrambe le firme riservate.</span><span class="sxs-lookup"><span data-stu-id="467ae-562">A class `B` derives from `A` and hides both of these reserved signatures.</span></span> <span data-ttu-id="467ae-563">L'esempio produce l'output:</span><span class="sxs-lookup"><span data-stu-id="467ae-563">The example produces the output:</span></span>
```console
123
123
456
```

#### <a name="member-names-reserved-for-events"></a><span data-ttu-id="467ae-564">Nomi dei membri riservati per gli eventi</span><span class="sxs-lookup"><span data-stu-id="467ae-564">Member names reserved for events</span></span>

<span data-ttu-id="467ae-565">Per un evento `E` ([Events](classes.md#events)) del tipo `T`delegato, vengono riservate le firme seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-565">For an event `E` ([Events](classes.md#events)) of delegate type `T`, the following signatures are reserved:</span></span>
```csharp
void add_E(T handler);
void remove_E(T handler);
```

#### <a name="member-names-reserved-for-indexers"></a><span data-ttu-id="467ae-566">Nomi dei membri riservati per gli indicizzatori</span><span class="sxs-lookup"><span data-stu-id="467ae-566">Member names reserved for indexers</span></span>

<span data-ttu-id="467ae-567">Per un indicizzatore ([indicizzatori](classes.md#indexers)) di tipo `T` con parameter-list `L`, sono riservate le firme seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-567">For an indexer ([Indexers](classes.md#indexers)) of type `T` with parameter-list `L`, the following signatures are reserved:</span></span>
```csharp
T get_Item(L);
void set_Item(L, T value);
```

<span data-ttu-id="467ae-568">Entrambe le firme sono riservate, anche se l'indicizzatore è di sola lettura o di sola scrittura.</span><span class="sxs-lookup"><span data-stu-id="467ae-568">Both signatures are reserved, even if the indexer is read-only or write-only.</span></span>

<span data-ttu-id="467ae-569">Il nome `Item` del membro è inoltre riservato.</span><span class="sxs-lookup"><span data-stu-id="467ae-569">Furthermore the member name `Item` is reserved.</span></span>

#### <a name="member-names-reserved-for-destructors"></a><span data-ttu-id="467ae-570">Nomi dei membri riservati per i distruttori</span><span class="sxs-lookup"><span data-stu-id="467ae-570">Member names reserved for destructors</span></span>

<span data-ttu-id="467ae-571">Per una classe che contiene un distruttore ([distruttori](classes.md#destructors)), è riservata la firma seguente:</span><span class="sxs-lookup"><span data-stu-id="467ae-571">For a class containing a destructor ([Destructors](classes.md#destructors)), the following signature is reserved:</span></span>
```csharp
void Finalize();
```

## <a name="constants"></a><span data-ttu-id="467ae-572">Costanti</span><span class="sxs-lookup"><span data-stu-id="467ae-572">Constants</span></span>

<span data-ttu-id="467ae-573">Una ***costante*** è un membro di classe che rappresenta un valore costante, ovvero un valore che può essere calcolato in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-573">A ***constant*** is a class member that represents a constant value: a value that can be computed at compile-time.</span></span> <span data-ttu-id="467ae-574">Un *constant_declaration* introduce una o più costanti di un tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="467ae-574">A *constant_declaration* introduces one or more constants of a given type.</span></span>

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

<span data-ttu-id="467ae-575">Un *constant_declaration* può includere un set di *attributi* ([attributi](attributes.md)), un modificatore `new` ([il nuovo modificatore](classes.md#the-new-modifier)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)).</span><span class="sxs-lookup"><span data-stu-id="467ae-575">A *constant_declaration* may include a set of *attributes* ([Attributes](attributes.md)), a `new` modifier ([The new modifier](classes.md#the-new-modifier)), and a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)).</span></span> <span data-ttu-id="467ae-576">Gli attributi e i modificatori si applicano a tutti i membri dichiarati da *constant_declaration*.</span><span class="sxs-lookup"><span data-stu-id="467ae-576">The attributes and modifiers apply to all of the members declared by the *constant_declaration*.</span></span> <span data-ttu-id="467ae-577">Sebbene le costanti siano considerate membri statici, un *constant_declaration* non richiede né consente un modificatore `static`.</span><span class="sxs-lookup"><span data-stu-id="467ae-577">Even though constants are considered static members, a *constant_declaration* neither requires nor allows a `static` modifier.</span></span> <span data-ttu-id="467ae-578">È un errore che lo stesso modificatore venga visualizzato più volte in una dichiarazione di costante.</span><span class="sxs-lookup"><span data-stu-id="467ae-578">It is an error for the same modifier to appear multiple times in a constant declaration.</span></span>

<span data-ttu-id="467ae-579">Il *tipo* di *constant_declaration* specifica il tipo di membri introdotti dalla dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-579">The *type* of a *constant_declaration* specifies the type of the members introduced by the declaration.</span></span> <span data-ttu-id="467ae-580">Il tipo è seguito da un elenco di *constant_declarator*, ognuno dei quali introduce un nuovo membro.</span><span class="sxs-lookup"><span data-stu-id="467ae-580">The type is followed by a list of *constant_declarator*s, each of which introduces a new member.</span></span> <span data-ttu-id="467ae-581">Un *constant_declarator* è costituito da un *identificatore* che assegna un nome al membro, seguito da un token "`=`", seguito da un *constant_expression* ([espressioni costanti](expressions.md#constant-expressions)) che fornisce il valore del membro.</span><span class="sxs-lookup"><span data-stu-id="467ae-581">A *constant_declarator* consists of an *identifier* that names the member, followed by an "`=`" token, followed by a *constant_expression* ([Constant expressions](expressions.md#constant-expressions)) that gives the value of the member.</span></span>

<span data-ttu-id="467ae-582">Il *tipo* specificato in una dichiarazione di costante deve essere `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, 0, 1, 2, 3, 4, *enum_type*o *reference_ digitare*.</span><span class="sxs-lookup"><span data-stu-id="467ae-582">The *type* specified in a constant declaration must be `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `string`, an *enum_type*, or a *reference_type*.</span></span> <span data-ttu-id="467ae-583">Ogni *constant_expression* deve restituire un valore del tipo di destinazione o di un tipo che può essere convertito nel tipo di destinazione mediante una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)).</span><span class="sxs-lookup"><span data-stu-id="467ae-583">Each *constant_expression* must yield a value of the target type or of a type that can be converted to the target type by an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)).</span></span>

<span data-ttu-id="467ae-584">Il *tipo* di una costante deve essere accessibile almeno quanto la costante ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="467ae-584">The *type* of a constant must be at least as accessible as the constant itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="467ae-585">Il valore di una costante viene ottenuto in un'espressione usando un *simple_name* ([nomi semplici](expressions.md#simple-names)) o un *member_access* ([accesso ai membri](expressions.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="467ae-585">The value of a constant is obtained in an expression using a *simple_name* ([Simple names](expressions.md#simple-names)) or a *member_access* ([Member access](expressions.md#member-access)).</span></span>

<span data-ttu-id="467ae-586">Una costante può partecipare a un *constant_expression*.</span><span class="sxs-lookup"><span data-stu-id="467ae-586">A constant can itself participate in a *constant_expression*.</span></span> <span data-ttu-id="467ae-587">Pertanto, una costante può essere usata in qualsiasi costrutto che richiede un *constant_expression*.</span><span class="sxs-lookup"><span data-stu-id="467ae-587">Thus, a constant may be used in any construct that requires a *constant_expression*.</span></span> <span data-ttu-id="467ae-588">Esempi di tali costrutti includono `case` etichette, `goto case` istruzioni, `enum` dichiarazioni di membri, attributi e altre dichiarazioni di costanti.</span><span class="sxs-lookup"><span data-stu-id="467ae-588">Examples of such constructs include `case` labels, `goto case` statements, `enum` member declarations, attributes, and other constant declarations.</span></span>

<span data-ttu-id="467ae-589">Come descritto in [espressioni costanti](expressions.md#constant-expressions), un *constant_expression* è un'espressione che può essere valutata interamente in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-589">As described in [Constant expressions](expressions.md#constant-expressions), a *constant_expression* is an expression that can be fully evaluated at compile-time.</span></span> <span data-ttu-id="467ae-590">Poiché l'unico modo per creare un valore non null di un *reference_type* diverso da `string` consiste nell'applicare l'operatore `new` e poiché l'operatore `new` non è consentito in un *constant_expression*, l'unico valore possibile per le costanti di  *il reference_type*s diverso da `string` è `null`.</span><span class="sxs-lookup"><span data-stu-id="467ae-590">Since the only way to create a non-null value of a *reference_type* other than `string` is to apply the `new` operator, and since the `new` operator is not permitted in a *constant_expression*, the only possible value for constants of *reference_type*s other than `string` is `null`.</span></span>

<span data-ttu-id="467ae-591">Quando si desidera un nome simbolico per un valore costante, ma quando il tipo di tale valore non è consentito in una dichiarazione di costante oppure quando il valore non può essere calcolato in fase di compilazione da un *constant_expression*, è possibile che un campo `readonly` (campi di sola[lettura](classes.md#readonly-fields)) in alternativa, usare.</span><span class="sxs-lookup"><span data-stu-id="467ae-591">When a symbolic name for a constant value is desired, but when the type of that value is not permitted in a constant declaration, or when the value cannot be computed at compile-time by a *constant_expression*, a `readonly` field ([Readonly fields](classes.md#readonly-fields)) may be used instead.</span></span>

<span data-ttu-id="467ae-592">Una dichiarazione di costante che dichiara più costanti è equivalente a più dichiarazioni di costanti singole con gli stessi attributi, modificatori e tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-592">A constant declaration that declares multiple constants is equivalent to multiple declarations of single constants with the same attributes, modifiers, and type.</span></span> <span data-ttu-id="467ae-593">Esempio:</span><span class="sxs-lookup"><span data-stu-id="467ae-593">For example</span></span>
```csharp
class A
{
    public const double X = 1.0, Y = 2.0, Z = 3.0;
}
```
<span data-ttu-id="467ae-594">equivale a</span><span class="sxs-lookup"><span data-stu-id="467ae-594">is equivalent to</span></span>
```csharp
class A
{
    public const double X = 1.0;
    public const double Y = 2.0;
    public const double Z = 3.0;
}
```

<span data-ttu-id="467ae-595">È consentito che le costanti dipendano da altre costanti all'interno dello stesso programma purché le dipendenze non siano di natura circolare.</span><span class="sxs-lookup"><span data-stu-id="467ae-595">Constants are permitted to depend on other constants within the same program as long as the dependencies are not of a circular nature.</span></span> <span data-ttu-id="467ae-596">Il compilatore dispone automaticamente di una valutazione delle dichiarazioni di costanti nell'ordine appropriato.</span><span class="sxs-lookup"><span data-stu-id="467ae-596">The compiler automatically arranges to evaluate the constant declarations in the appropriate order.</span></span> <span data-ttu-id="467ae-597">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-597">In the example</span></span>
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
<span data-ttu-id="467ae-598">il `A.Y`compilatore valuta prima di tutto, quindi `B.Z`valuta e infine valuta `A.X`, producendo i valori `10`, `11`e `12`.</span><span class="sxs-lookup"><span data-stu-id="467ae-598">the compiler first evaluates `A.Y`, then evaluates `B.Z`, and finally evaluates `A.X`, producing the values `10`, `11`, and `12`.</span></span> <span data-ttu-id="467ae-599">Le dichiarazioni di costanti possono dipendere da costanti di altri programmi, ma tali dipendenze sono possibili solo in una direzione.</span><span class="sxs-lookup"><span data-stu-id="467ae-599">Constant declarations may depend on constants from other programs, but such dependencies are only possible in one direction.</span></span> <span data-ttu-id="467ae-600">Facendo riferimento all'esempio precedente, se `A` e `B` sono stati dichiarati in programmi distinti, `A.X` è `B.Z`possibile che dipenda da, ma `B.Z` potrebbe non dipendere `A.Y`simultaneamente da.</span><span class="sxs-lookup"><span data-stu-id="467ae-600">Referring to the example above, if `A` and `B` were declared in separate programs, it would be possible for `A.X` to depend on `B.Z`, but `B.Z` could then not simultaneously depend on `A.Y`.</span></span>

## <a name="fields"></a><span data-ttu-id="467ae-601">Campi</span><span class="sxs-lookup"><span data-stu-id="467ae-601">Fields</span></span>

<span data-ttu-id="467ae-602">Un ***campo*** è un membro che rappresenta una variabile associata a un oggetto o a una classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-602">A ***field*** is a member that represents a variable associated with an object or class.</span></span> <span data-ttu-id="467ae-603">Un *field_declaration* introduce uno o più campi di un tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="467ae-603">A *field_declaration* introduces one or more fields of a given type.</span></span>

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

<span data-ttu-id="467ae-604">Un *field_declaration* può includere un set di *attributi* ([attributi](attributes.md)), un modificatore `new` ([il nuovo modificatore](classes.md#the-new-modifier)), una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)) e un modificatore `static` ([ Campi statici e di istanza](classes.md#static-and-instance-fields).</span><span class="sxs-lookup"><span data-stu-id="467ae-604">A *field_declaration* may include a set of *attributes* ([Attributes](attributes.md)), a `new` modifier ([The new modifier](classes.md#the-new-modifier)), a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), and a `static` modifier ([Static and instance fields](classes.md#static-and-instance-fields)).</span></span> <span data-ttu-id="467ae-605">Inoltre, un *field_declaration* può includere un modificatore `readonly` ([campi](classes.md#readonly-fields)di sola lettura) o un modificatore `volatile` ([campi volatili](classes.md#volatile-fields)), ma non entrambi.</span><span class="sxs-lookup"><span data-stu-id="467ae-605">In addition, a *field_declaration* may include a `readonly` modifier ([Readonly fields](classes.md#readonly-fields)) or a `volatile` modifier ([Volatile fields](classes.md#volatile-fields)) but not both.</span></span> <span data-ttu-id="467ae-606">Gli attributi e i modificatori si applicano a tutti i membri dichiarati da *field_declaration*.</span><span class="sxs-lookup"><span data-stu-id="467ae-606">The attributes and modifiers apply to all of the members declared by the *field_declaration*.</span></span> <span data-ttu-id="467ae-607">È un errore che lo stesso modificatore venga visualizzato più volte in una dichiarazione di campo.</span><span class="sxs-lookup"><span data-stu-id="467ae-607">It is an error for the same modifier to appear multiple times in a field declaration.</span></span>

<span data-ttu-id="467ae-608">Il *tipo* di *field_declaration* specifica il tipo di membri introdotti dalla dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-608">The *type* of a *field_declaration* specifies the type of the members introduced by the declaration.</span></span> <span data-ttu-id="467ae-609">Il tipo è seguito da un elenco di *variable_declarator*, ognuno dei quali introduce un nuovo membro.</span><span class="sxs-lookup"><span data-stu-id="467ae-609">The type is followed by a list of *variable_declarator*s, each of which introduces a new member.</span></span> <span data-ttu-id="467ae-610">Un *variable_declarator* è costituito da un *identificatore* che assegna un nome a tale membro, seguito facoltativamente da un token "`=`" e da un *variable_initializer* ([inizializzatori di variabile](classes.md#variable-initializers)) che fornisce il valore iniziale di tale membro.</span><span class="sxs-lookup"><span data-stu-id="467ae-610">A *variable_declarator* consists of an *identifier* that names that member, optionally followed by an "`=`" token and a *variable_initializer* ([Variable initializers](classes.md#variable-initializers)) that gives the initial value of that member.</span></span>

<span data-ttu-id="467ae-611">Il *tipo* di un campo deve essere accessibile almeno quanto il campo stesso (vincoli di[accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="467ae-611">The *type* of a field must be at least as accessible as the field itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="467ae-612">Il valore di un campo viene ottenuto in un'espressione usando un *simple_name* ([nomi semplici](expressions.md#simple-names)) o un *member_access* ([accesso ai membri](expressions.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="467ae-612">The value of a field is obtained in an expression using a *simple_name* ([Simple names](expressions.md#simple-names)) or a *member_access* ([Member access](expressions.md#member-access)).</span></span> <span data-ttu-id="467ae-613">Il valore di un campo non di sola lettura viene modificato utilizzando un' *assegnazione* ([operatori di assegnazione](expressions.md#assignment-operators)).</span><span class="sxs-lookup"><span data-stu-id="467ae-613">The value of a non-readonly field is modified using an *assignment* ([Assignment operators](expressions.md#assignment-operators)).</span></span> <span data-ttu-id="467ae-614">Il valore di un campo non di sola lettura può essere ottenuto e modificato usando gli operatori di incremento e decremento suffisso ([operatori di incremento e decremento suffisso](expressions.md#postfix-increment-and-decrement-operators)) e gli operatori di incremento e decremento del prefisso ([incremento e decremento prefisso) operatori](expressions.md#prefix-increment-and-decrement-operators)).</span><span class="sxs-lookup"><span data-stu-id="467ae-614">The value of a non-readonly field can be both obtained and modified using postfix increment and decrement operators ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators)) and prefix increment and decrement operators ([Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)).</span></span>

<span data-ttu-id="467ae-615">Una dichiarazione di campo che dichiara più campi equivale a più dichiarazioni di singoli campi con gli stessi attributi, modificatori e tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-615">A field declaration that declares multiple fields is equivalent to multiple declarations of single fields with the same attributes, modifiers, and type.</span></span> <span data-ttu-id="467ae-616">Esempio:</span><span class="sxs-lookup"><span data-stu-id="467ae-616">For example</span></span>
```csharp
class A
{
    public static int X = 1, Y, Z = 100;
}
```
<span data-ttu-id="467ae-617">equivale a</span><span class="sxs-lookup"><span data-stu-id="467ae-617">is equivalent to</span></span>
```csharp
class A
{
    public static int X = 1;
    public static int Y;
    public static int Z = 100;
}
```

### <a name="static-and-instance-fields"></a><span data-ttu-id="467ae-618">Campi statici e di istanza</span><span class="sxs-lookup"><span data-stu-id="467ae-618">Static and instance fields</span></span>

<span data-ttu-id="467ae-619">Quando una dichiarazione di campo include `static` un modificatore, i campi introdotti dalla dichiarazione sono ***campi statici***.</span><span class="sxs-lookup"><span data-stu-id="467ae-619">When a field declaration includes a `static` modifier, the fields introduced by the declaration are ***static fields***.</span></span> <span data-ttu-id="467ae-620">Quando non `static` è presente alcun modificatore, i campi introdotti dalla dichiarazione sono ***campi di istanza***.</span><span class="sxs-lookup"><span data-stu-id="467ae-620">When no `static` modifier is present, the fields introduced by the declaration are ***instance fields***.</span></span> <span data-ttu-id="467ae-621">I campi statici e di istanza sono due dei diversi tipi di variabili ([variabili](variables.md)) supportati da C#e, a volte, sono definiti rispettivamente come ***variabili statiche*** e ***variabili di istanza***.</span><span class="sxs-lookup"><span data-stu-id="467ae-621">Static fields and instance fields are two of the several kinds of variables ([Variables](variables.md)) supported by C#, and at times they are referred to as ***static variables*** and ***instance variables***, respectively.</span></span>

<span data-ttu-id="467ae-622">Un campo statico non fa parte di un'istanza specifica; viene invece condiviso tra tutte le istanze di un tipo chiuso ([tipi aperti e chiusi](types.md#open-and-closed-types)).</span><span class="sxs-lookup"><span data-stu-id="467ae-622">A static field is not part of a specific instance; instead, it is shared amongst all instances of a closed type ([Open and closed types](types.md#open-and-closed-types)).</span></span> <span data-ttu-id="467ae-623">Indipendentemente dal numero di istanze di un tipo di classe chiuso, esiste una sola copia di un campo statico per il dominio dell'applicazione associato.</span><span class="sxs-lookup"><span data-stu-id="467ae-623">No matter how many instances of a closed class type are created, there is only ever one copy of a static field for the associated application domain.</span></span>

<span data-ttu-id="467ae-624">Esempio:</span><span class="sxs-lookup"><span data-stu-id="467ae-624">For example:</span></span>
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

<span data-ttu-id="467ae-625">Un campo di istanza appartiene a un'istanza di.</span><span class="sxs-lookup"><span data-stu-id="467ae-625">An instance field belongs to an instance.</span></span> <span data-ttu-id="467ae-626">In particolare, ogni istanza di una classe contiene un set separato di tutti i campi di istanza di tale classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-626">Specifically, every instance of a class contains a separate set of all the instance fields of that class.</span></span>

<span data-ttu-id="467ae-627">Quando si fa riferimento a un campo in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, se `M` è un campo statico, `E` deve indicare un tipo che contiene `M` e se `M` è un campo di istanza, e deve indicare un'istanza di un tipo che contiene `M`.</span><span class="sxs-lookup"><span data-stu-id="467ae-627">When a field is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, if `M` is a static field, `E` must denote a type containing `M`, and if `M` is an instance field, E must denote an instance of a type containing `M`.</span></span>

<span data-ttu-id="467ae-628">Le differenze tra i membri statici e i membri di istanza sono illustrate ulteriormente nei [membri statici e di istanza](classes.md#static-and-instance-members).</span><span class="sxs-lookup"><span data-stu-id="467ae-628">The differences between static and instance members are discussed further in [Static and instance members](classes.md#static-and-instance-members).</span></span>

### <a name="readonly-fields"></a><span data-ttu-id="467ae-629">Campi di sola lettura</span><span class="sxs-lookup"><span data-stu-id="467ae-629">Readonly fields</span></span>

<span data-ttu-id="467ae-630">Quando un *field_declaration* include un modificatore `readonly`, i campi introdotti dalla dichiarazione sono campi di sola ***lettura***.</span><span class="sxs-lookup"><span data-stu-id="467ae-630">When a *field_declaration* includes a `readonly` modifier, the fields introduced by the declaration are ***readonly fields***.</span></span> <span data-ttu-id="467ae-631">Le assegnazioni dirette ai campi di sola lettura possono essere eseguite solo come parte della dichiarazione o in un costruttore di istanza o in un costruttore statico nella stessa classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-631">Direct assignments to readonly fields can only occur as part of that declaration or in an instance constructor or static constructor in the same class.</span></span> <span data-ttu-id="467ae-632">Un campo di sola lettura può essere assegnato più volte in questi contesti. In particolare, le assegnazioni dirette `readonly` a un campo sono consentite solo nei contesti seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-632">(A readonly field can be assigned to multiple times in these contexts.) Specifically, direct assignments to a `readonly` field are permitted only in the following contexts:</span></span>

*  <span data-ttu-id="467ae-633">In *variable_declarator* che introduce il campo (includendo un *variable_initializer* nella dichiarazione).</span><span class="sxs-lookup"><span data-stu-id="467ae-633">In the *variable_declarator* that introduces the field (by including a *variable_initializer* in the declaration).</span></span>
*  <span data-ttu-id="467ae-634">Per un campo di istanza, nei costruttori di istanza della classe che contiene la dichiarazione di campo; per un campo statico, nel costruttore statico della classe che contiene la dichiarazione di campo.</span><span class="sxs-lookup"><span data-stu-id="467ae-634">For an instance field, in the instance constructors of the class that contains the field declaration; for a static field, in the static constructor of the class that contains the field declaration.</span></span> <span data-ttu-id="467ae-635">Questi sono anche gli unici contesti in cui è possibile passare un `readonly` campo `out` come parametro o `ref` .</span><span class="sxs-lookup"><span data-stu-id="467ae-635">These are also the only contexts in which it is valid to pass a `readonly` field as an `out` or `ref` parameter.</span></span>

<span data-ttu-id="467ae-636">Il tentativo di assegnare a un `readonly` campo o passarlo `out` come parametro o `ref` in qualsiasi altro contesto è un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-636">Attempting to assign to a `readonly` field or pass it as an `out` or `ref` parameter in any other context is a compile-time error.</span></span>

#### <a name="using-static-readonly-fields-for-constants"></a><span data-ttu-id="467ae-637">Utilizzo di campi di sola lettura statici per le costanti</span><span class="sxs-lookup"><span data-stu-id="467ae-637">Using static readonly fields for constants</span></span>

<span data-ttu-id="467ae-638">Un `static readonly` campo è utile quando si desidera un nome simbolico per un valore costante, ma quando il tipo del valore non è consentito in una `const` dichiarazione o quando il valore non può essere calcolato in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-638">A `static readonly` field is useful when a symbolic name for a constant value is desired, but when the type of the value is not permitted in a `const` declaration, or when the value cannot be computed at compile-time.</span></span> <span data-ttu-id="467ae-639">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-639">In the example</span></span>
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
<span data-ttu-id="467ae-640">i `Black`membri `White`,, `Red`, `const` e non`Blue` possono essere dichiarati come membri perché i relativi valori non possono essere calcolati in fase di compilazione. `Green`</span><span class="sxs-lookup"><span data-stu-id="467ae-640">the `Black`, `White`, `Red`, `Green`, and `Blue` members cannot be declared as `const` members because their values cannot be computed at compile-time.</span></span> <span data-ttu-id="467ae-641">Tuttavia, dichiarando `static readonly` invece ha lo stesso effetto.</span><span class="sxs-lookup"><span data-stu-id="467ae-641">However, declaring them `static readonly` instead has much the same effect.</span></span>

#### <a name="versioning-of-constants-and-static-readonly-fields"></a><span data-ttu-id="467ae-642">Controllo delle versioni di costanti e campi di sola lettura statici</span><span class="sxs-lookup"><span data-stu-id="467ae-642">Versioning of constants and static readonly fields</span></span>

<span data-ttu-id="467ae-643">Le costanti e i campi di sola lettura hanno una semantica di controllo delle versioni binaria diversa.</span><span class="sxs-lookup"><span data-stu-id="467ae-643">Constants and readonly fields have different binary versioning semantics.</span></span> <span data-ttu-id="467ae-644">Quando un'espressione fa riferimento a una costante, il valore della costante viene ottenuto in fase di compilazione, ma quando un'espressione fa riferimento a un campo di sola lettura, il valore del campo non viene ottenuto fino alla fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="467ae-644">When an expression references a constant, the value of the constant is obtained at compile-time, but when an expression references a readonly field, the value of the field is not obtained until run-time.</span></span> <span data-ttu-id="467ae-645">Si consideri un'applicazione costituita da due programmi distinti:</span><span class="sxs-lookup"><span data-stu-id="467ae-645">Consider an application that consists of two separate programs:</span></span>
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

<span data-ttu-id="467ae-646">Gli `Program1` spazi `Program2` dei nomi e indicano due programmi compilati separatamente.</span><span class="sxs-lookup"><span data-stu-id="467ae-646">The `Program1` and `Program2` namespaces denote two programs that are compiled separately.</span></span> <span data-ttu-id="467ae-647">Poiché `Program1.Utils.X` viene dichiarato come un campo statico `Console.WriteLine` di sola lettura, il valore restituito dall'istruzione non è noto in fase di compilazione, ma viene invece ottenuto in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="467ae-647">Because `Program1.Utils.X` is declared as a static readonly field, the value output by the `Console.WriteLine` statement is not known at compile-time, but rather is obtained at run-time.</span></span> <span data-ttu-id="467ae-648">Pertanto, se il valore di `X` viene modificato e `Program1` viene ricompilato, l' `Console.WriteLine` istruzione restituirà il nuovo valore anche se `Program2` non viene ricompilato.</span><span class="sxs-lookup"><span data-stu-id="467ae-648">Thus, if the value of `X` is changed and `Program1` is recompiled, the `Console.WriteLine` statement will output the new value even if `Program2` isn't recompiled.</span></span> <span data-ttu-id="467ae-649">Tuttavia, era `X` una costante, il valore di `X` sarebbe stato ottenuto al momento `Program2` della compilazione e rimarrebbe inalterato dalle modifiche apportate in `Program1` fino `Program2` a quando non viene ricompilato.</span><span class="sxs-lookup"><span data-stu-id="467ae-649">However, had `X` been a constant, the value of `X` would have been obtained at the time `Program2` was compiled, and would remain unaffected by changes in `Program1` until `Program2` is recompiled.</span></span>

### <a name="volatile-fields"></a><span data-ttu-id="467ae-650">Campi volatili</span><span class="sxs-lookup"><span data-stu-id="467ae-650">Volatile fields</span></span>

<span data-ttu-id="467ae-651">Quando un *field_declaration* include un modificatore `volatile`, i campi introdotti dalla dichiarazione sono ***campi volatili***.</span><span class="sxs-lookup"><span data-stu-id="467ae-651">When a *field_declaration* includes a `volatile` modifier, the fields introduced by that declaration are ***volatile fields***.</span></span>

<span data-ttu-id="467ae-652">Per i campi non volatili, le tecniche di ottimizzazione che riordinano le istruzioni possono generare risultati imprevisti e imprevedibili nei programmi multithread che accedono a campi senza sincronizzazione, ad esempio quello fornito da *lock_statement* ([il istruzione lock](statements.md#the-lock-statement)).</span><span class="sxs-lookup"><span data-stu-id="467ae-652">For non-volatile fields, optimization techniques that reorder instructions can lead to unexpected and unpredictable results in multi-threaded programs that access fields without synchronization such as that provided by the *lock_statement* ([The lock statement](statements.md#the-lock-statement)).</span></span> <span data-ttu-id="467ae-653">Queste ottimizzazioni possono essere eseguite dal compilatore, dal sistema della fase di esecuzione o dall'hardware.</span><span class="sxs-lookup"><span data-stu-id="467ae-653">These optimizations can be performed by the compiler, by the run-time system, or by hardware.</span></span> <span data-ttu-id="467ae-654">Per i campi volatili, tali ottimizzazioni di riordino sono limitate:</span><span class="sxs-lookup"><span data-stu-id="467ae-654">For volatile fields, such reordering optimizations are restricted:</span></span>

*  <span data-ttu-id="467ae-655">Una lettura di un campo volatile è detta ***lettura volatile***.</span><span class="sxs-lookup"><span data-stu-id="467ae-655">A read of a volatile field is called a ***volatile read***.</span></span> <span data-ttu-id="467ae-656">Una lettura volatile ha una "semantica di acquisizione"; ovvero, è garantita prima di tutti i riferimenti alla memoria che si verificano dopo la sequenza di istruzioni.</span><span class="sxs-lookup"><span data-stu-id="467ae-656">A volatile read has "acquire semantics"; that is, it is guaranteed to occur prior to any references to memory that occur after it in the instruction sequence.</span></span>
*  <span data-ttu-id="467ae-657">Una scrittura di un campo volatile è detta ***scrittura volatile***.</span><span class="sxs-lookup"><span data-stu-id="467ae-657">A write of a volatile field is called a ***volatile write***.</span></span> <span data-ttu-id="467ae-658">Una scrittura volatile presenta una "semantica di rilascio"; ovvero, è garantito che avvenga dopo i riferimenti alla memoria prima dell'istruzione di scrittura nella sequenza di istruzioni.</span><span class="sxs-lookup"><span data-stu-id="467ae-658">A volatile write has "release semantics"; that is, it is guaranteed to happen after any memory references prior to the write instruction in the instruction sequence.</span></span>

<span data-ttu-id="467ae-659">Queste limitazioni garantiscono che tutti i thread considereranno scritture di tipo volatile eseguite da altri thread nell'ordine in cui sono stati eseguiti.</span><span class="sxs-lookup"><span data-stu-id="467ae-659">These restrictions ensure that all threads will observe volatile writes performed by any other thread in the order in which they were performed.</span></span> <span data-ttu-id="467ae-660">Non è necessaria un'implementazione conforme per fornire un singolo ordinamento totale delle scritture volatili, come visto da tutti i thread di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="467ae-660">A conforming implementation is not required to provide a single total ordering of volatile writes as seen from all threads of execution.</span></span> <span data-ttu-id="467ae-661">Il tipo di un campo volatile deve essere uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-661">The type of a volatile field must be one of the following:</span></span>

*  <span data-ttu-id="467ae-662">*Reference_type*.</span><span class="sxs-lookup"><span data-stu-id="467ae-662">A *reference_type*.</span></span>
*  <span data-ttu-id="467ae-663">`byte`Tipo ,`char` ,,`System.UIntPtr`,,,,,, o. `sbyte` `short` `ushort` `int` `uint` `float` `bool` `System.IntPtr`</span><span class="sxs-lookup"><span data-stu-id="467ae-663">The type `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `char`, `float`, `bool`, `System.IntPtr`, or `System.UIntPtr`.</span></span>
*  <span data-ttu-id="467ae-664">*Enum_type* con tipo di base enum `byte`, `sbyte`, `short`, `ushort`, `int` o `uint`.</span><span class="sxs-lookup"><span data-stu-id="467ae-664">An *enum_type* having an enum base type of `byte`, `sbyte`, `short`, `ushort`, `int`, or `uint`.</span></span>

<span data-ttu-id="467ae-665">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-665">The example</span></span>
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
<span data-ttu-id="467ae-666">produce l'output:</span><span class="sxs-lookup"><span data-stu-id="467ae-666">produces the output:</span></span>
```console
result = 143
```

<span data-ttu-id="467ae-667">In questo esempio, il metodo `Main` avvia un nuovo thread che esegue il metodo `Thread2`.</span><span class="sxs-lookup"><span data-stu-id="467ae-667">In this example, the method `Main` starts a new thread that runs the method `Thread2`.</span></span> <span data-ttu-id="467ae-668">Questo metodo archivia un valore in un campo non volatile denominato `result`, quindi Archivia `true` nel campo `finished`volatile.</span><span class="sxs-lookup"><span data-stu-id="467ae-668">This method stores a value into a non-volatile field called `result`, then stores `true` in the volatile field `finished`.</span></span> <span data-ttu-id="467ae-669">Il thread principale attende che il campo `finished` sia impostato su `true`, quindi legge il campo `result`.</span><span class="sxs-lookup"><span data-stu-id="467ae-669">The main thread waits for the field `finished` to be set to `true`, then reads the field `result`.</span></span> <span data-ttu-id="467ae-670">Poiché `finished` è stato dichiarato `volatile`, il thread principale deve leggere il valore `143` dal campo `result`.</span><span class="sxs-lookup"><span data-stu-id="467ae-670">Since `finished` has been declared `volatile`, the main thread must read the value `143` from the field `result`.</span></span> <span data-ttu-id="467ae-671">Se il campo `finished` non è stato dichiarato `volatile`, è possibile che l'archivio `result` sia visibile al thread principale dopo l'archivio in `finished`e quindi affinché il thread principale legga il valore `0` dal campo `result`.</span><span class="sxs-lookup"><span data-stu-id="467ae-671">If the field `finished` had not been declared `volatile`, then it would be permissible for the store to `result` to be visible to the main thread after the store to `finished`, and hence for the main thread to read the value `0` from the field `result`.</span></span> <span data-ttu-id="467ae-672">`finished` La`volatile` dichiarazione come campo impedisce tale incoerenza.</span><span class="sxs-lookup"><span data-stu-id="467ae-672">Declaring `finished` as a `volatile` field prevents any such inconsistency.</span></span>

### <a name="field-initialization"></a><span data-ttu-id="467ae-673">Inizializzazione del campo</span><span class="sxs-lookup"><span data-stu-id="467ae-673">Field initialization</span></span>

<span data-ttu-id="467ae-674">Il valore iniziale di un campo, sia che si tratti di un campo statico o di un campo di istanza, è il valore predefinito ([valori predefiniti](variables.md#default-values)) del tipo del campo.</span><span class="sxs-lookup"><span data-stu-id="467ae-674">The initial value of a field, whether it be a static field or an instance field, is the default value ([Default values](variables.md#default-values)) of the field's type.</span></span> <span data-ttu-id="467ae-675">Non è possibile osservare il valore di un campo prima che questa inizializzazione predefinita venga eseguita e un campo non viene mai "inizializzato".</span><span class="sxs-lookup"><span data-stu-id="467ae-675">It is not possible to observe the value of a field before this default initialization has occurred, and a field is thus never "uninitialized".</span></span> <span data-ttu-id="467ae-676">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-676">The example</span></span>
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
<span data-ttu-id="467ae-677">produce l'output</span><span class="sxs-lookup"><span data-stu-id="467ae-677">produces the output</span></span>
```console
b = False, i = 0
```
<span data-ttu-id="467ae-678">Poiché `b` e`i` vengono inizializzati automaticamente sui valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="467ae-678">because `b` and `i` are both automatically initialized to default values.</span></span>

### <a name="variable-initializers"></a><span data-ttu-id="467ae-679">Inizializzatori di variabile</span><span class="sxs-lookup"><span data-stu-id="467ae-679">Variable initializers</span></span>

<span data-ttu-id="467ae-680">Le dichiarazioni di campo possono includere *variable_initializer*s.</span><span class="sxs-lookup"><span data-stu-id="467ae-680">Field declarations may include *variable_initializer*s.</span></span> <span data-ttu-id="467ae-681">Per i campi statici, gli inizializzatori di variabile corrispondono a istruzioni di assegnazione eseguite durante l'inizializzazione della classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-681">For static fields, variable initializers correspond to assignment statements that are executed during class initialization.</span></span> <span data-ttu-id="467ae-682">Per i campi di istanza, gli inizializzatori di variabile corrispondono a istruzioni di assegnazione eseguite quando viene creata un'istanza della classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-682">For instance fields, variable initializers correspond to assignment statements that are executed when an instance of the class is created.</span></span>

<span data-ttu-id="467ae-683">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-683">The example</span></span>
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
<span data-ttu-id="467ae-684">produce l'output</span><span class="sxs-lookup"><span data-stu-id="467ae-684">produces the output</span></span>
```console
x = 1.4142135623731, i = 100, s = Hello
```
<span data-ttu-id="467ae-685">Poiché un'assegnazione `x` si verifica quando gli inizializzatori di campo statici eseguono e assegnazioni `s` a e si verificano quando vengono eseguiti gli inizializzatori di `i` campo dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="467ae-685">because an assignment to `x` occurs when static field initializers execute and assignments to `i` and `s` occur when the instance field initializers execute.</span></span>

<span data-ttu-id="467ae-686">L'inizializzazione del valore predefinito descritta nell' [inizializzazione del campo](classes.md#field-initialization) si verifica per tutti i campi, inclusi i campi con inizializzatori di variabile.</span><span class="sxs-lookup"><span data-stu-id="467ae-686">The default value initialization described in [Field initialization](classes.md#field-initialization) occurs for all fields, including fields that have variable initializers.</span></span> <span data-ttu-id="467ae-687">Pertanto, quando una classe viene inizializzata, tutti i campi statici della classe vengono prima inizializzati sui valori predefiniti e quindi gli inizializzatori di campo statici vengono eseguiti in ordine testuale.</span><span class="sxs-lookup"><span data-stu-id="467ae-687">Thus, when a class is initialized, all static fields in that class are first initialized to their default values, and then the static field initializers are executed in textual order.</span></span> <span data-ttu-id="467ae-688">Allo stesso modo, quando viene creata un'istanza di una classe, tutti i campi di istanza in tale istanza vengono innanzitutto inizializzati sui rispettivi valori predefiniti, quindi gli inizializzatori di campo dell'istanza vengono eseguiti in ordine testuale.</span><span class="sxs-lookup"><span data-stu-id="467ae-688">Likewise, when an instance of a class is created, all instance fields in that instance are first initialized to their default values, and then the instance field initializers are executed in textual order.</span></span>

<span data-ttu-id="467ae-689">È possibile che i campi statici con inizializzatori di variabile siano osservati nello stato del valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="467ae-689">It is possible for static fields with variable initializers to be observed in their default value state.</span></span> <span data-ttu-id="467ae-690">Tuttavia, questo è fortemente sconsigliato come una cosa di stile.</span><span class="sxs-lookup"><span data-stu-id="467ae-690">However, this is strongly discouraged as a matter of style.</span></span> <span data-ttu-id="467ae-691">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-691">The example</span></span>
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
<span data-ttu-id="467ae-692">presenta questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="467ae-692">exhibits this behavior.</span></span> <span data-ttu-id="467ae-693">Nonostante le definizioni circolari di a e b, il programma è valido.</span><span class="sxs-lookup"><span data-stu-id="467ae-693">Despite the circular definitions of a and b, the program is valid.</span></span> <span data-ttu-id="467ae-694">Produce l'output</span><span class="sxs-lookup"><span data-stu-id="467ae-694">It results in the output</span></span>
```console
a = 1, b = 2
```
<span data-ttu-id="467ae-695">Poiché i campi `a` statici e `b` vengono inizializzati `0` su (il valore predefinito `int`per) prima dell'esecuzione degli inizializzatori.</span><span class="sxs-lookup"><span data-stu-id="467ae-695">because the static fields `a` and `b` are initialized to `0` (the default value for `int`) before their initializers are executed.</span></span> <span data-ttu-id="467ae-696">Quando l'inizializzatore `a` per viene eseguito, il `b` valore di è zero, `a` quindi viene inizializzato su `1`.</span><span class="sxs-lookup"><span data-stu-id="467ae-696">When the initializer for `a` runs, the value of `b` is zero, and so `a` is initialized to `1`.</span></span> <span data-ttu-id="467ae-697">Quando l'inizializzatore `b` per viene eseguito, il `a` valore di `1`è già, `b` quindi viene inizializzato su `2`.</span><span class="sxs-lookup"><span data-stu-id="467ae-697">When the initializer for `b` runs, the value of `a` is already `1`, and so `b` is initialized to `2`.</span></span>

#### <a name="static-field-initialization"></a><span data-ttu-id="467ae-698">Inizializzazione campo statico</span><span class="sxs-lookup"><span data-stu-id="467ae-698">Static field initialization</span></span>

<span data-ttu-id="467ae-699">Gli inizializzatori di variabile di campo statici di una classe corrispondono a una sequenza di assegnazioni che vengono eseguite nell'ordine testuale in cui vengono visualizzate nella dichiarazione di classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-699">The static field variable initializers of a class correspond to a sequence of assignments that are executed in the textual order in which they appear in the class declaration.</span></span> <span data-ttu-id="467ae-700">Se nella classe è presente un costruttore statico ([costruttori statici](classes.md#static-constructors)), l'esecuzione degli inizializzatori di campo statici viene eseguita immediatamente prima dell'esecuzione del costruttore statico.</span><span class="sxs-lookup"><span data-stu-id="467ae-700">If a static constructor ([Static constructors](classes.md#static-constructors)) exists in the class, execution of the static field initializers occurs immediately prior to executing that static constructor.</span></span> <span data-ttu-id="467ae-701">In caso contrario, gli inizializzatori di campo statici vengono eseguiti in un tempo dipendente dall'implementazione prima del primo utilizzo di un campo statico di tale classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-701">Otherwise, the static field initializers are executed at an implementation-dependent time prior to the first use of a static field of that class.</span></span> <span data-ttu-id="467ae-702">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-702">The example</span></span>
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
<span data-ttu-id="467ae-703">potrebbe produrre l'output:</span><span class="sxs-lookup"><span data-stu-id="467ae-703">might produce either the output:</span></span>
```console
Init A
Init B
1 1
```
<span data-ttu-id="467ae-704">o l'output:</span><span class="sxs-lookup"><span data-stu-id="467ae-704">or the output:</span></span>
```console
Init B
Init A
1 1
```
<span data-ttu-id="467ae-705">dato che l'esecuzione `X`dell'inizializzatore `Y`e dell'inizializzatore dell'oggetto può essere eseguito in entrambi i casi, è necessario che si verifichino solo prima dei riferimenti a tali campi.</span><span class="sxs-lookup"><span data-stu-id="467ae-705">because the execution of `X`'s initializer and `Y`'s initializer could occur in either order; they are only constrained to occur before the references to those fields.</span></span> <span data-ttu-id="467ae-706">Tuttavia, nell'esempio:</span><span class="sxs-lookup"><span data-stu-id="467ae-706">However, in the example:</span></span>
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
<span data-ttu-id="467ae-707">l'output deve essere:</span><span class="sxs-lookup"><span data-stu-id="467ae-707">the output must be:</span></span>
```console
Init B
Init A
1 1
```
<span data-ttu-id="467ae-708">Poiché le regole per l'esecuzione dei costruttori statici (come definito nei [costruttori statici](classes.md#static-constructors)) forniscono il costruttore `B`statico (e di conseguenza `B`gli inizializzatori di campo statici) che devono essere `A`eseguiti prima di static inizializzatori di costruttori e campi.</span><span class="sxs-lookup"><span data-stu-id="467ae-708">because the rules for when static constructors execute (as defined in [Static constructors](classes.md#static-constructors)) provide that `B`'s static constructor (and hence `B`'s static field initializers) must run before `A`'s static constructor and field initializers.</span></span>

#### <a name="instance-field-initialization"></a><span data-ttu-id="467ae-709">Inizializzazione campo istanza</span><span class="sxs-lookup"><span data-stu-id="467ae-709">Instance field initialization</span></span>

<span data-ttu-id="467ae-710">Gli inizializzatori di variabile di campo dell'istanza di una classe corrispondono a una sequenza di assegnazioni che vengono eseguite immediatamente dopo l'immissione in uno dei costruttori di istanza ([inizializzatori di costruttori](classes.md#constructor-initializers)) di tale classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-710">The instance field variable initializers of a class correspond to a sequence of assignments that are executed immediately upon entry to any one of the instance constructors ([Constructor initializers](classes.md#constructor-initializers)) of that class.</span></span> <span data-ttu-id="467ae-711">Gli inizializzatori di variabile vengono eseguiti nell'ordine testuale in cui vengono visualizzati nella dichiarazione della classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-711">The variable initializers are executed in the textual order in which they appear in the class declaration.</span></span> <span data-ttu-id="467ae-712">La creazione e il processo di inizializzazione dell'istanza della classe vengono descritti ulteriormente nei [costruttori di istanza](classes.md#instance-constructors).</span><span class="sxs-lookup"><span data-stu-id="467ae-712">The class instance creation and initialization process is described further in [Instance constructors](classes.md#instance-constructors).</span></span>

<span data-ttu-id="467ae-713">Un inizializzatore di variabile per un campo di istanza non può fare riferimento all'istanza in fase di creazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-713">A variable initializer for an instance field cannot reference the instance being created.</span></span> <span data-ttu-id="467ae-714">Pertanto, si tratta di un errore in fase di compilazione per fare riferimento a `this` in un inizializzatore di variabile, poiché si tratta di un errore in fase di compilazione per un inizializzatore di variabile che fa riferimento a qualsiasi membro di istanza tramite un *simple_name*.</span><span class="sxs-lookup"><span data-stu-id="467ae-714">Thus, it is a compile-time error to reference `this` in a variable initializer, as it is a compile-time error for a variable initializer to reference any instance member through a *simple_name*.</span></span> <span data-ttu-id="467ae-715">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-715">In the example</span></span>
```csharp
class A
{
    int x = 1;
    int y = x + 1;        // Error, reference to instance member of this
}
```
<span data-ttu-id="467ae-716">l'inizializzatore di `y` variabile per restituisce un errore in fase di compilazione perché fa riferimento a un membro dell'istanza creata.</span><span class="sxs-lookup"><span data-stu-id="467ae-716">the variable initializer for `y` results in a compile-time error because it references a member of the instance being created.</span></span>

## <a name="methods"></a><span data-ttu-id="467ae-717">Metodi</span><span class="sxs-lookup"><span data-stu-id="467ae-717">Methods</span></span>

<span data-ttu-id="467ae-718">Un ***metodo*** è un membro che implementa un calcolo o un'azione che può essere eseguita da un oggetto o una classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-718">A ***method*** is a member that implements a computation or action that can be performed by an object or class.</span></span> <span data-ttu-id="467ae-719">I metodi vengono dichiarati usando *method_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="467ae-719">Methods are declared using *method_declaration*s:</span></span>

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

<span data-ttu-id="467ae-720">Un *method_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)), il `new` ([il modificatore new](classes.md#the-new-modifier)), `static` ([static e instance Metodi](classes.md#static-and-instance-methods)), `virtual` ([metodi virtuali](classes.md#virtual-methods)), 0 ([metodi di override](classes.md#override-methods)), 2[(Metodi sealed](classes.md#sealed-methods)), 4 ([metodi astratti](classes.md#abstract-methods)) e i modificatori 6 ([metodi esterni](classes.md#external-methods)).</span><span class="sxs-lookup"><span data-stu-id="467ae-720">A *method_declaration* may include a set of *attributes* ([Attributes](attributes.md)) and a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), the `new` ([The new modifier](classes.md#the-new-modifier)),  `static` ([Static and instance methods](classes.md#static-and-instance-methods)), `virtual` ([Virtual methods](classes.md#virtual-methods)), `override` ([Override methods](classes.md#override-methods)), `sealed` ([Sealed methods](classes.md#sealed-methods)), `abstract` ([Abstract methods](classes.md#abstract-methods)), and `extern` ([External methods](classes.md#external-methods)) modifiers.</span></span>

<span data-ttu-id="467ae-721">Una dichiarazione ha una combinazione valida di modificatori se si verificano tutte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-721">A declaration has a valid combination of modifiers if all of the following are true:</span></span>

*  <span data-ttu-id="467ae-722">La dichiarazione include una combinazione valida di modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)).</span><span class="sxs-lookup"><span data-stu-id="467ae-722">The declaration includes a valid combination of access modifiers ([Access modifiers](classes.md#access-modifiers)).</span></span>
*  <span data-ttu-id="467ae-723">La dichiarazione non include lo stesso modificatore più volte.</span><span class="sxs-lookup"><span data-stu-id="467ae-723">The declaration does not include the same modifier multiple times.</span></span>
*  <span data-ttu-id="467ae-724">La dichiarazione include al massimo uno dei modificatori seguenti: `static`, `virtual`e `override`.</span><span class="sxs-lookup"><span data-stu-id="467ae-724">The declaration includes at most one of the following modifiers: `static`, `virtual`, and `override`.</span></span>
*  <span data-ttu-id="467ae-725">La dichiarazione include al massimo uno dei modificatori seguenti: `new` e. `override`</span><span class="sxs-lookup"><span data-stu-id="467ae-725">The declaration includes at most one of the following modifiers: `new` and `override`.</span></span>
*  <span data-ttu-id="467ae-726">Se la dichiarazione include il `abstract` modificatore, la dichiarazione non include i modificatori seguenti: `static`, `virtual` `sealed` o `extern`.</span><span class="sxs-lookup"><span data-stu-id="467ae-726">If the declaration includes the `abstract` modifier, then the declaration does not include any of the following modifiers: `static`, `virtual`, `sealed` or `extern`.</span></span>
*  <span data-ttu-id="467ae-727">Se la dichiarazione include il `private` modificatore, la dichiarazione non include i modificatori seguenti: `virtual`, `override`o `abstract`.</span><span class="sxs-lookup"><span data-stu-id="467ae-727">If the declaration includes the `private` modifier, then the declaration does not include any of the following modifiers: `virtual`, `override`, or `abstract`.</span></span>
*  <span data-ttu-id="467ae-728">Se la dichiarazione include il `sealed` modificatore, la dichiarazione include anche il `override` modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-728">If the declaration includes the `sealed` modifier, then the declaration also includes the `override` modifier.</span></span>
*  <span data-ttu-id="467ae-729">Se la dichiarazione include il `partial` modificatore, non include i modificatori seguenti: `new`, `public`, `protected`, `internal`, `private`, `virtual`,, `override` `sealed` , `abstract`o .`extern`</span><span class="sxs-lookup"><span data-stu-id="467ae-729">If the declaration includes the `partial` modifier, then it does not include any of the following modifiers: `new`, `public`, `protected`, `internal`, `private`, `virtual`, `sealed`, `override`, `abstract`, or `extern`.</span></span>

<span data-ttu-id="467ae-730">Un metodo con il `async` modificatore è una funzione asincrona e segue le regole descritte in [funzioni asincrone](classes.md#async-functions).</span><span class="sxs-lookup"><span data-stu-id="467ae-730">A method that has the `async` modifier is an async function and follows the rules described in [Async functions](classes.md#async-functions).</span></span>

<span data-ttu-id="467ae-731">Il *return_type* di una dichiarazione di metodo specifica il tipo di valore calcolato e restituito dal metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-731">The *return_type* of a method declaration specifies the type of the value computed and returned by the method.</span></span> <span data-ttu-id="467ae-732">*Return_type* è `void` se il metodo non restituisce un valore.</span><span class="sxs-lookup"><span data-stu-id="467ae-732">The *return_type* is `void` if the method does not return a value.</span></span> <span data-ttu-id="467ae-733">Se la dichiarazione include il `partial` modificatore, il tipo restituito deve essere `void`.</span><span class="sxs-lookup"><span data-stu-id="467ae-733">If the declaration includes the `partial` modifier, then the return type must be `void`.</span></span>

<span data-ttu-id="467ae-734">*MEMBER_NAME* specifica il nome del metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-734">The *member_name* specifies the name of the method.</span></span> <span data-ttu-id="467ae-735">A meno che il metodo non sia un'implementazione esplicita di un membro di interfaccia ([implementazioni esplicite di membri di interfaccia](interfaces.md#explicit-interface-member-implementations)), *MEMBER_NAME* è semplicemente un *identificatore*.</span><span class="sxs-lookup"><span data-stu-id="467ae-735">Unless the method is an explicit interface member implementation ([Explicit interface member implementations](interfaces.md#explicit-interface-member-implementations)), the *member_name* is simply an *identifier*.</span></span> <span data-ttu-id="467ae-736">Per un'implementazione esplicita di un membro di interfaccia, *MEMBER_NAME* è costituito da un *INTERFACE_TYPE* seguito da un "`.`" e da un *identificatore*.</span><span class="sxs-lookup"><span data-stu-id="467ae-736">For an explicit interface member implementation, the *member_name* consists of an *interface_type* followed by a "`.`" and an *identifier*.</span></span>

<span data-ttu-id="467ae-737">Il *type_parameter_list* facoltativo specifica i parametri di tipo del metodo ([parametri di tipo](classes.md#type-parameters)).</span><span class="sxs-lookup"><span data-stu-id="467ae-737">The optional *type_parameter_list* specifies the type parameters of the method ([Type parameters](classes.md#type-parameters)).</span></span> <span data-ttu-id="467ae-738">Se viene specificato un *type_parameter_list* , il metodo è un ***metodo generico***.</span><span class="sxs-lookup"><span data-stu-id="467ae-738">If a *type_parameter_list* is specified the method is a ***generic method***.</span></span> <span data-ttu-id="467ae-739">Se il metodo ha un modificatore `extern`, non è possibile specificare un *type_parameter_list* .</span><span class="sxs-lookup"><span data-stu-id="467ae-739">If the method has an `extern` modifier, a *type_parameter_list* cannot be specified.</span></span>

<span data-ttu-id="467ae-740">Il *formal_parameter_list* facoltativo specifica i parametri del metodo ([parametri del metodo](classes.md#method-parameters)).</span><span class="sxs-lookup"><span data-stu-id="467ae-740">The optional *formal_parameter_list* specifies the parameters of the method ([Method parameters](classes.md#method-parameters)).</span></span>

<span data-ttu-id="467ae-741">I *type_parameter_constraints_clause*facoltativi specificano i vincoli sui singoli parametri di tipo ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)) e possono essere specificati solo se viene fornito anche un *type_parameter_list* e il metodo non ha un modificatore `override`.</span><span class="sxs-lookup"><span data-stu-id="467ae-741">The optional *type_parameter_constraints_clause*s specify constraints on individual type parameters ([Type parameter constraints](classes.md#type-parameter-constraints)) and may only be specified if a *type_parameter_list* is also supplied, and the method does not have an `override` modifier.</span></span>

<span data-ttu-id="467ae-742">Il *return_type* e ognuno dei tipi a cui si fa riferimento nel *formal_parameter_list* di un metodo deve essere accessibile almeno quanto il metodo stesso ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="467ae-742">The *return_type* and each of the types referenced in the *formal_parameter_list* of a method must be at least as accessible as the method itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="467ae-743">*Method_body* è un punto e virgola, un ***corpo dell'istruzione*** o un corpo dell' ***espressione***.</span><span class="sxs-lookup"><span data-stu-id="467ae-743">The *method_body* is either a semicolon, a ***statement body*** or an ***expression body***.</span></span> <span data-ttu-id="467ae-744">Il corpo di un'istruzione è costituito da un *blocco*, che specifica le istruzioni da eseguire quando viene richiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-744">A statement body consists of a *block*, which specifies the statements to execute when the method is invoked.</span></span> <span data-ttu-id="467ae-745">Il corpo di `=>` un'espressione è costituito da seguito da un' *espressione* e da un punto e virgola e indica una singola espressione da eseguire quando viene richiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-745">An expression body consists of `=>` followed by an *expression* and a semicolon, and denotes a single expression to perform when the method is invoked.</span></span> 

<span data-ttu-id="467ae-746">Per i metodi `abstract` e `extern`, il *method_body* è costituito semplicemente da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="467ae-746">For `abstract` and `extern` methods, the *method_body* consists simply of a semicolon.</span></span> <span data-ttu-id="467ae-747">Per i metodi `partial` è possibile che *method_body* sia costituito da un punto e virgola, da un corpo del blocco o da un corpo dell'espressione.</span><span class="sxs-lookup"><span data-stu-id="467ae-747">For `partial` methods the *method_body* may consist of either a semicolon, a block body or an expression body.</span></span> <span data-ttu-id="467ae-748">Per tutti gli altri metodi, *method_body* è un corpo del blocco o un corpo dell'espressione.</span><span class="sxs-lookup"><span data-stu-id="467ae-748">For all other methods, the *method_body* is either a block body or an expression body.</span></span>

<span data-ttu-id="467ae-749">Se il *method_body* è costituito da un punto e virgola, la dichiarazione non può includere il modificatore `async`.</span><span class="sxs-lookup"><span data-stu-id="467ae-749">If the *method_body* consists of a semicolon, then the declaration may not include the `async` modifier.</span></span>

<span data-ttu-id="467ae-750">Il nome, l'elenco dei parametri di tipo e l'elenco di parametri formali di un metodo definiscono la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) del metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-750">The name, the type parameter list and the formal parameter list of a method define the signature ([Signatures and overloading](basic-concepts.md#signatures-and-overloading)) of the method.</span></span> <span data-ttu-id="467ae-751">In particolare, la firma di un metodo è costituita dal nome, dal numero di parametri di tipo e dal numero, dai modificatori e dai tipi dei parametri formali.</span><span class="sxs-lookup"><span data-stu-id="467ae-751">Specifically, the signature of a method consists of its name, the number of type parameters and the number, modifiers, and types of its formal parameters.</span></span> <span data-ttu-id="467ae-752">Per questi scopi, qualsiasi parametro di tipo del metodo che si verifica nel tipo di un parametro formale viene identificato senza il relativo nome, ma in base alla posizione ordinale nell'elenco di argomenti di tipo del metodo. Il tipo restituito non fa parte della firma di un metodo, né i nomi dei parametri di tipo o i parametri formali.</span><span class="sxs-lookup"><span data-stu-id="467ae-752">For these purposes, any type parameter of the method that occurs in the type of a formal parameter is identified not by its name, but by its ordinal position in the type argument list of the method.The return type is not part of a method's signature, nor are the names of the type parameters or the formal parameters.</span></span>

<span data-ttu-id="467ae-753">Il nome di un metodo deve essere diverso da quello dei nomi di tutti gli altri metodi non dichiarati nella stessa classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-753">The name of a method must differ from the names of all other non-methods declared in the same class.</span></span> <span data-ttu-id="467ae-754">Inoltre, la firma di un metodo deve essere diversa dalle firme di tutti gli altri metodi dichiarati nella stessa classe e due metodi dichiarati nella stessa classe potrebbero non avere firme che differiscono `ref` solo `out`per e.</span><span class="sxs-lookup"><span data-stu-id="467ae-754">In addition, the signature of a method must differ from the signatures of all other methods declared in the same class, and two methods declared in the same class may not have signatures that differ solely by `ref` and `out`.</span></span>

<span data-ttu-id="467ae-755">Gli *type_parameter*del metodo si trovano nell'ambito di *method_declaration*e possono essere usati per formare i tipi in tutto l'ambito in *return_type*, *method_body*e *type_parameter_constraints_clause*, ma non negli *attributi*.</span><span class="sxs-lookup"><span data-stu-id="467ae-755">The method's *type_parameter*s are in scope throughout the *method_declaration*, and can be used to form types throughout that scope in *return_type*, *method_body*, and *type_parameter_constraints_clause*s but not in *attributes*.</span></span>

<span data-ttu-id="467ae-756">Tutti i parametri formali e i parametri di tipo devono avere nomi diversi.</span><span class="sxs-lookup"><span data-stu-id="467ae-756">All formal parameters and type parameters must have different names.</span></span>

### <a name="method-parameters"></a><span data-ttu-id="467ae-757">Parametri del metodo</span><span class="sxs-lookup"><span data-stu-id="467ae-757">Method parameters</span></span>

<span data-ttu-id="467ae-758">I parametri di un metodo, se presenti, sono dichiarati dal *formal_parameter_list*del metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-758">The parameters of a method, if any, are declared by the method's *formal_parameter_list*.</span></span>

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

<span data-ttu-id="467ae-759">L'elenco di parametri formali è costituito da uno o più parametri delimitati da virgole di cui solo l'ultimo può essere un *parameter_array*.</span><span class="sxs-lookup"><span data-stu-id="467ae-759">The formal parameter list consists of one or more comma-separated parameters of which only the last may be a *parameter_array*.</span></span>

<span data-ttu-id="467ae-760">Un *fixed_parameter* è costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), un modificatore `ref`, `out` o `this` facoltativo, un *tipo*, un *identificatore* e un *default_argument*facoltativo.</span><span class="sxs-lookup"><span data-stu-id="467ae-760">A *fixed_parameter* consists of an optional set of *attributes* ([Attributes](attributes.md)), an optional `ref`, `out` or `this` modifier, a *type*, an *identifier* and an optional *default_argument*.</span></span> <span data-ttu-id="467ae-761">Ogni *fixed_parameter* dichiara un parametro del tipo specificato con il nome specificato.</span><span class="sxs-lookup"><span data-stu-id="467ae-761">Each *fixed_parameter* declares a parameter of the given type with the given name.</span></span> <span data-ttu-id="467ae-762">Il `this` modificatore designa il metodo come metodo di estensione ed è consentito solo sul primo parametro di un metodo statico.</span><span class="sxs-lookup"><span data-stu-id="467ae-762">The `this` modifier designates the method as an extension method and is only allowed on the first parameter of a static method.</span></span> <span data-ttu-id="467ae-763">I metodi di estensione sono descritti ulteriormente nei [metodi di estensione](classes.md#extension-methods).</span><span class="sxs-lookup"><span data-stu-id="467ae-763">Extension methods are further described in [Extension methods](classes.md#extension-methods).</span></span>

<span data-ttu-id="467ae-764">Un *fixed_parameter* con *default_argument* è noto come ***parametro facoltativo***, mentre un *fixed_parameter* senza *default_argument* è un ***parametro obbligatorio***.</span><span class="sxs-lookup"><span data-stu-id="467ae-764">A *fixed_parameter* with a *default_argument* is known as an ***optional parameter***, whereas a *fixed_parameter* without a *default_argument* is a ***required parameter***.</span></span> <span data-ttu-id="467ae-765">Un parametro obbligatorio non può essere visualizzato dopo un parametro facoltativo in un *formal_parameter_list*.</span><span class="sxs-lookup"><span data-stu-id="467ae-765">A required parameter may not appear after an optional parameter in a *formal_parameter_list*.</span></span>

<span data-ttu-id="467ae-766">Un parametro `ref` o `out` non può avere un *default_argument*.</span><span class="sxs-lookup"><span data-stu-id="467ae-766">A `ref` or `out` parameter cannot have a *default_argument*.</span></span> <span data-ttu-id="467ae-767">L' *espressione* in una *default_argument* deve essere una delle seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-767">The *expression* in a *default_argument* must be one of the following:</span></span>

*  <span data-ttu-id="467ae-768">a *constant_expression*</span><span class="sxs-lookup"><span data-stu-id="467ae-768">a *constant_expression*</span></span>
*  <span data-ttu-id="467ae-769">espressione nel formato `new S()` in cui `S` è un tipo di valore</span><span class="sxs-lookup"><span data-stu-id="467ae-769">an expression of the form `new S()` where `S` is a value type</span></span>
*  <span data-ttu-id="467ae-770">espressione nel formato `default(S)` in cui `S` è un tipo di valore</span><span class="sxs-lookup"><span data-stu-id="467ae-770">an expression of the form `default(S)` where `S` is a value type</span></span>

<span data-ttu-id="467ae-771">L' *espressione* deve essere convertibile in modo implicito da un'identità o Conversione Nullable nel tipo di parametro.</span><span class="sxs-lookup"><span data-stu-id="467ae-771">The *expression* must be implicitly convertible by an identity or nullable conversion to the type of the parameter.</span></span>

<span data-ttu-id="467ae-772">Se i parametri facoltativi si verificano in una dichiarazione di metodo parziale di implementazione ([metodi parziali](classes.md#partial-methods)), un'implementazione esplicita di un membro di interfaccia ([implementazioni esplicite di membri di interfaccia](interfaces.md#explicit-interface-member-implementations)) o in una dichiarazione dell'indicizzatore a parametro singolo ([ Indicizzatori](classes.md#indexers)) il compilatore deve restituire un avviso, poiché questi membri non possono mai essere richiamati in modo che consenta l'omissione degli argomenti.</span><span class="sxs-lookup"><span data-stu-id="467ae-772">If optional parameters occur in an implementing partial method declaration ([Partial methods](classes.md#partial-methods)) , an explicit interface member implementation ([Explicit interface member implementations](interfaces.md#explicit-interface-member-implementations)) or in a single-parameter indexer declaration ([Indexers](classes.md#indexers)) the compiler should give a warning, since these members can never be invoked in a way that permits arguments to be omitted.</span></span>

<span data-ttu-id="467ae-773">Un *parameter_array* è costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), da un modificatore `params`, da un *array_type*e da un *identificatore*.</span><span class="sxs-lookup"><span data-stu-id="467ae-773">A *parameter_array* consists of an optional set of *attributes* ([Attributes](attributes.md)), a `params` modifier, an *array_type*, and an *identifier*.</span></span> <span data-ttu-id="467ae-774">Una matrice di parametri dichiara un solo parametro del tipo di matrice specificato con il nome specificato.</span><span class="sxs-lookup"><span data-stu-id="467ae-774">A parameter array declares a single parameter of the given array type with the given name.</span></span> <span data-ttu-id="467ae-775">Il *array_type* di una matrice di parametri deve essere un tipo di matrice unidimensionale ([tipi di matrice](arrays.md#array-types)).</span><span class="sxs-lookup"><span data-stu-id="467ae-775">The *array_type* of a parameter array must be a single-dimensional array type ([Array types](arrays.md#array-types)).</span></span> <span data-ttu-id="467ae-776">In una chiamata al metodo, una matrice di parametri consente di specificare un solo argomento del tipo di matrice specificato oppure di specificare zero o più argomenti del tipo di elemento della matrice.</span><span class="sxs-lookup"><span data-stu-id="467ae-776">In a method invocation, a parameter array permits either a single argument of the given array type to be specified, or it permits zero or more arguments of the array element type to be specified.</span></span> <span data-ttu-id="467ae-777">Le matrici di parametri sono descritte ulteriormente in [matrici di parametri](classes.md#parameter-arrays).</span><span class="sxs-lookup"><span data-stu-id="467ae-777">Parameter arrays are described further in [Parameter arrays](classes.md#parameter-arrays).</span></span>

<span data-ttu-id="467ae-778">Un *parameter_array* può verificarsi dopo un parametro facoltativo, ma non può avere un valore predefinito. l'omissione degli argomenti per un *parameter_array* comporterebbe invece la creazione di una matrice vuota.</span><span class="sxs-lookup"><span data-stu-id="467ae-778">A *parameter_array* may occur after an optional parameter, but cannot have a default value -- the omission of arguments for a *parameter_array* would instead result in the creation of an empty array.</span></span>

<span data-ttu-id="467ae-779">Nell'esempio seguente vengono illustrati diversi tipi di parametri:</span><span class="sxs-lookup"><span data-stu-id="467ae-779">The following example illustrates different kinds of parameters:</span></span>
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

<span data-ttu-id="467ae-780">In *formal_parameter_list* per `M` `i` è un parametro ref obbligatorio, `d` è un parametro di valore obbligatorio, `b`, `s`, `o` e `t` sono parametri di valore facoltativi e `a` è una matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="467ae-780">In the *formal_parameter_list* for `M`, `i` is a required ref parameter, `d` is a required value parameter, `b`, `s`, `o` and `t` are optional value parameters and `a` is a parameter array.</span></span>

<span data-ttu-id="467ae-781">Una dichiarazione di metodo crea uno spazio di dichiarazione separato per parametri, parametri di tipo e variabili locali.</span><span class="sxs-lookup"><span data-stu-id="467ae-781">A method declaration creates a separate declaration space for parameters, type parameters and local variables.</span></span> <span data-ttu-id="467ae-782">I nomi vengono introdotti in questo spazio di dichiarazione dall'elenco di parametri di tipo e dall'elenco di parametri formali del metodo e dalle dichiarazioni di variabili locali nel *blocco* del metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-782">Names are introduced into this declaration space by the type parameter list and the formal parameter list of the method and by local variable declarations in the *block* of the method.</span></span> <span data-ttu-id="467ae-783">Per due membri di uno spazio di dichiarazione di metodo è necessario avere lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="467ae-783">It is an error for two members of a method declaration space to have the same name.</span></span> <span data-ttu-id="467ae-784">È un errore per lo spazio di dichiarazione del metodo e lo spazio di dichiarazione della variabile locale di uno spazio di dichiarazione annidato per contenere elementi con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="467ae-784">It is an error for the method declaration space and the local variable declaration space of a nested declaration space to contain elements with the same name.</span></span>

<span data-ttu-id="467ae-785">Una chiamata a un metodo ([chiamate al metodo](expressions.md#method-invocations)) crea una copia, specifica della chiamata, dei parametri formali e delle variabili locali del metodo e l'elenco di argomenti della chiamata assegna valori o riferimenti a variabili al nuovo formale creato parametri.</span><span class="sxs-lookup"><span data-stu-id="467ae-785">A method invocation ([Method invocations](expressions.md#method-invocations)) creates a copy, specific to that invocation, of the formal parameters and local variables of the method, and the argument list of the invocation assigns values or variable references to the newly created formal parameters.</span></span> <span data-ttu-id="467ae-786">All'interno del *blocco* di un metodo è possibile fare riferimento ai parametri formali in base ai relativi identificatori nelle espressioni *Simple_name* ([nomi semplici](expressions.md#simple-names)).</span><span class="sxs-lookup"><span data-stu-id="467ae-786">Within the *block* of a method, formal parameters can be referenced by their identifiers in *simple_name* expressions ([Simple names](expressions.md#simple-names)).</span></span>

<span data-ttu-id="467ae-787">Sono disponibili quattro tipi di parametri formali:</span><span class="sxs-lookup"><span data-stu-id="467ae-787">There are four kinds of formal parameters:</span></span>

*  <span data-ttu-id="467ae-788">Parametri del valore, che vengono dichiarati senza modificatori.</span><span class="sxs-lookup"><span data-stu-id="467ae-788">Value parameters, which are declared without any modifiers.</span></span>
*  <span data-ttu-id="467ae-789">Parametri di riferimento, dichiarati con `ref` il modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-789">Reference parameters, which are declared with the `ref` modifier.</span></span>
*  <span data-ttu-id="467ae-790">Parametri di output, dichiarati con `out` il modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-790">Output parameters, which are declared with the `out` modifier.</span></span>
*  <span data-ttu-id="467ae-791">Matrici di parametri, dichiarate con il `params` modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-791">Parameter arrays, which are declared with the `params` modifier.</span></span>

<span data-ttu-id="467ae-792">Come descritto in [firme e overload](basic-concepts.md#signatures-and-overloading), i `ref` modificatori e `out` fanno parte della firma di un metodo, ma il modificatore `params` non lo è.</span><span class="sxs-lookup"><span data-stu-id="467ae-792">As described in [Signatures and overloading](basic-concepts.md#signatures-and-overloading), the `ref` and `out` modifiers are part of a method's signature, but the `params` modifier is not.</span></span>

#### <a name="value-parameters"></a><span data-ttu-id="467ae-793">Parametri valore</span><span class="sxs-lookup"><span data-stu-id="467ae-793">Value parameters</span></span>

<span data-ttu-id="467ae-794">Un parametro dichiarato senza modificatori è un parametro di valore.</span><span class="sxs-lookup"><span data-stu-id="467ae-794">A parameter declared with no modifiers is a value parameter.</span></span> <span data-ttu-id="467ae-795">Un parametro di valore corrisponde a una variabile locale che ottiene il valore iniziale dall'argomento corrispondente fornito nella chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-795">A value parameter corresponds to a local variable that gets its initial value from the corresponding argument supplied in the method invocation.</span></span>

<span data-ttu-id="467ae-796">Quando un parametro formale è un parametro di valore, l'argomento corrispondente in una chiamata al metodo deve essere un'espressione convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) nel tipo di parametro formale.</span><span class="sxs-lookup"><span data-stu-id="467ae-796">When a formal parameter is a value parameter, the corresponding argument in a method invocation must be an expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the formal parameter type.</span></span>

<span data-ttu-id="467ae-797">Un metodo è autorizzato a assegnare nuovi valori a un parametro di valore.</span><span class="sxs-lookup"><span data-stu-id="467ae-797">A method is permitted to assign new values to a value parameter.</span></span> <span data-ttu-id="467ae-798">Tali assegnazioni influiscono solo sul percorso di archiviazione locale rappresentato dal parametro value, ma non hanno alcun effetto sull'argomento effettivo specificato nella chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-798">Such assignments only affect the local storage location represented by the value parameter—they have no effect on the actual argument given in the method invocation.</span></span>

#### <a name="reference-parameters"></a><span data-ttu-id="467ae-799">Parametri di riferimento</span><span class="sxs-lookup"><span data-stu-id="467ae-799">Reference parameters</span></span>

<span data-ttu-id="467ae-800">Un parametro dichiarato con un `ref` modificatore è un parametro di riferimento.</span><span class="sxs-lookup"><span data-stu-id="467ae-800">A parameter declared with a `ref` modifier is a reference parameter.</span></span> <span data-ttu-id="467ae-801">A differenza di un parametro di valore, un parametro di riferimento non crea un nuovo percorso di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-801">Unlike a value parameter, a reference parameter does not create a new storage location.</span></span> <span data-ttu-id="467ae-802">Un parametro Reference rappresenta invece lo stesso percorso di archiviazione della variabile specificata come argomento nella chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-802">Instead, a reference parameter represents the same storage location as the variable given as the argument in the method invocation.</span></span>

<span data-ttu-id="467ae-803">Quando un parametro formale è un parametro di riferimento, l'argomento corrispondente in una chiamata al metodo deve essere costituito dalla parola chiave `ref` seguita da un *variable_reference* ([regole precise per determinare l'assegnazione definita](variables.md#precise-rules-for-determining-definite-assignment)) dello stesso tipo di il parametro formale.</span><span class="sxs-lookup"><span data-stu-id="467ae-803">When a formal parameter is a reference parameter, the corresponding argument in a method invocation must consist of the keyword `ref` followed by a *variable_reference* ([Precise rules for determining definite assignment](variables.md#precise-rules-for-determining-definite-assignment)) of the same type as the formal parameter.</span></span> <span data-ttu-id="467ae-804">Una variabile deve essere assegnata definitivamente prima di poter essere passata come parametro di riferimento.</span><span class="sxs-lookup"><span data-stu-id="467ae-804">A variable must be definitely assigned before it can be passed as a reference parameter.</span></span>

<span data-ttu-id="467ae-805">All'interno di un metodo, un parametro di riferimento viene sempre considerato definitivamente assegnato.</span><span class="sxs-lookup"><span data-stu-id="467ae-805">Within a method, a reference parameter is always considered definitely assigned.</span></span>

<span data-ttu-id="467ae-806">Un metodo dichiarato come iteratore ([iteratori](classes.md#iterators)) non può avere parametri di riferimento.</span><span class="sxs-lookup"><span data-stu-id="467ae-806">A method declared as an iterator ([Iterators](classes.md#iterators)) cannot have reference parameters.</span></span>

<span data-ttu-id="467ae-807">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-807">The example</span></span>
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
<span data-ttu-id="467ae-808">produce l'output</span><span class="sxs-lookup"><span data-stu-id="467ae-808">produces the output</span></span>
```console
i = 2, j = 1
```

<span data-ttu-id="467ae-809">Per la chiamata di `Swap` in `Main`, `x` rappresenta `i` e `y` rappresenta. `j`</span><span class="sxs-lookup"><span data-stu-id="467ae-809">For the invocation of `Swap` in `Main`, `x` represents `i` and `y` represents `j`.</span></span> <span data-ttu-id="467ae-810">Quindi, la chiamata ha l'effetto di scambiare i valori di `i` e. `j`</span><span class="sxs-lookup"><span data-stu-id="467ae-810">Thus, the invocation has the effect of swapping the values of `i` and `j`.</span></span>

<span data-ttu-id="467ae-811">In un metodo che accetta parametri di riferimento è possibile che più nomi rappresentino lo stesso percorso di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-811">In a method that takes reference parameters it is possible for multiple names to represent the same storage location.</span></span> <span data-ttu-id="467ae-812">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-812">In the example</span></span>
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
<span data-ttu-id="467ae-813">la `F` chiamata di `b`in `G` `s` passa`a` un riferimento a per e.</span><span class="sxs-lookup"><span data-stu-id="467ae-813">the invocation of `F` in `G` passes a reference to `s` for both `a` and `b`.</span></span> <span data-ttu-id="467ae-814">Pertanto, per tale chiamata, i `s`nomi, `a`e `b` fanno riferimento allo stesso percorso di archiviazione e le tre assegnazioni modificano il campo `s`dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="467ae-814">Thus, for that invocation, the names `s`, `a`, and `b` all refer to the same storage location, and the three assignments all modify the instance field `s`.</span></span>

#### <a name="output-parameters"></a><span data-ttu-id="467ae-815">Parametri di output</span><span class="sxs-lookup"><span data-stu-id="467ae-815">Output parameters</span></span>

<span data-ttu-id="467ae-816">Un parametro dichiarato con un `out` modificatore è un parametro di output.</span><span class="sxs-lookup"><span data-stu-id="467ae-816">A parameter declared with an `out` modifier is an output parameter.</span></span> <span data-ttu-id="467ae-817">Analogamente a un parametro di riferimento, un parametro di output non crea un nuovo percorso di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-817">Similar to a reference parameter, an output parameter does not create a new storage location.</span></span> <span data-ttu-id="467ae-818">Un parametro di output rappresenta invece lo stesso percorso di archiviazione della variabile specificata come argomento nella chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-818">Instead, an output parameter represents the same storage location as the variable given as the argument in the method invocation.</span></span>

<span data-ttu-id="467ae-819">Quando un parametro formale è un parametro di output, l'argomento corrispondente in una chiamata al metodo deve essere costituito dalla parola chiave `out` seguita da un *variable_reference* ([regole precise per determinare l'assegnazione definita](variables.md#precise-rules-for-determining-definite-assignment)) dello stesso tipo di parametro formale.</span><span class="sxs-lookup"><span data-stu-id="467ae-819">When a formal parameter is an output parameter, the corresponding argument in a method invocation must consist of the keyword `out` followed by a *variable_reference* ([Precise rules for determining definite assignment](variables.md#precise-rules-for-determining-definite-assignment)) of the same type as the formal parameter.</span></span> <span data-ttu-id="467ae-820">Una variabile non deve essere definitivamente assegnata prima di poter essere passata come parametro di output, ma dopo una chiamata in cui una variabile è stata passata come parametro di output, la variabile viene considerata assegnata definitivamente.</span><span class="sxs-lookup"><span data-stu-id="467ae-820">A variable need not be definitely assigned before it can be passed as an output parameter, but following an invocation where a variable was passed as an output parameter, the variable is considered definitely assigned.</span></span>

<span data-ttu-id="467ae-821">All'interno di un metodo, come una variabile locale, un parametro di output viene inizialmente considerato non assegnato e deve essere assegnato definitivamente prima di utilizzare il relativo valore.</span><span class="sxs-lookup"><span data-stu-id="467ae-821">Within a method, just like a local variable, an output parameter is initially considered unassigned and must be definitely assigned before its value is used.</span></span>

<span data-ttu-id="467ae-822">Ogni parametro di output di un metodo deve essere assegnato definitivamente prima che il metodo venga restituito.</span><span class="sxs-lookup"><span data-stu-id="467ae-822">Every output parameter of a method must be definitely assigned before the method returns.</span></span>

<span data-ttu-id="467ae-823">Un metodo dichiarato come metodo parziale ([metodi parziali](classes.md#partial-methods)) o un iteratore ([iteratori](classes.md#iterators)) non può avere parametri di output.</span><span class="sxs-lookup"><span data-stu-id="467ae-823">A method declared as a partial method ([Partial methods](classes.md#partial-methods)) or an iterator ([Iterators](classes.md#iterators)) cannot have output parameters.</span></span>

<span data-ttu-id="467ae-824">I parametri di output vengono in genere usati nei metodi che producono più valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="467ae-824">Output parameters are typically used in methods that produce multiple return values.</span></span> <span data-ttu-id="467ae-825">Esempio:</span><span class="sxs-lookup"><span data-stu-id="467ae-825">For example:</span></span>
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

<span data-ttu-id="467ae-826">L'esempio produce l'output:</span><span class="sxs-lookup"><span data-stu-id="467ae-826">The example produces the output:</span></span>
```console
c:\Windows\System\
hello.txt
```

<span data-ttu-id="467ae-827">Si noti che `dir` le `name` variabili e possono essere non assegnate prima che vengano passate `SplitPath`a e che siano considerate definitivamente assegnate dopo la chiamata.</span><span class="sxs-lookup"><span data-stu-id="467ae-827">Note that the `dir` and `name` variables can be unassigned before they are passed to `SplitPath`, and that they are considered definitely assigned following the call.</span></span>

#### <a name="parameter-arrays"></a><span data-ttu-id="467ae-828">Matrici di parametri</span><span class="sxs-lookup"><span data-stu-id="467ae-828">Parameter arrays</span></span>

<span data-ttu-id="467ae-829">Un parametro dichiarato con un `params` modificatore è una matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="467ae-829">A parameter declared with a `params` modifier is a parameter array.</span></span> <span data-ttu-id="467ae-830">Se un elenco di parametri formali include una matrice di parametri, deve essere l'ultimo parametro nell'elenco e deve essere di un tipo di matrice unidimensionale.</span><span class="sxs-lookup"><span data-stu-id="467ae-830">If a formal parameter list includes a parameter array, it must be the last parameter in the list and it must be of a single-dimensional array type.</span></span> <span data-ttu-id="467ae-831">Ad esempio, i tipi `string[]` e `string[][]` possono essere usati come tipo di una matrice di parametri, ma il tipo `string[,]` non può essere.</span><span class="sxs-lookup"><span data-stu-id="467ae-831">For example, the types `string[]` and `string[][]` can be used as the type of a parameter array, but the type `string[,]` can not.</span></span> <span data-ttu-id="467ae-832">Non è possibile combinare il `params` modificatore con i `ref` modificatori e `out`.</span><span class="sxs-lookup"><span data-stu-id="467ae-832">It is not possible to combine the `params` modifier with the modifiers `ref` and `out`.</span></span>

<span data-ttu-id="467ae-833">Una matrice di parametri consente di specificare gli argomenti in uno dei due modi seguenti in una chiamata al metodo:</span><span class="sxs-lookup"><span data-stu-id="467ae-833">A parameter array permits arguments to be specified in one of two ways in a method invocation:</span></span>

*  <span data-ttu-id="467ae-834">L'argomento specificato per una matrice di parametri può essere una singola espressione convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) nel tipo di matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="467ae-834">The argument given for a parameter array can be a single expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the parameter array type.</span></span> <span data-ttu-id="467ae-835">In questo caso, la matrice di parametri funge esattamente da parametro di valore.</span><span class="sxs-lookup"><span data-stu-id="467ae-835">In this case, the parameter array acts precisely like a value parameter.</span></span>
*  <span data-ttu-id="467ae-836">In alternativa, la chiamata può specificare zero o più argomenti per la matrice di parametri, dove ogni argomento è un'espressione che è implicitamente convertibile ([conversioni implicite](conversions.md#implicit-conversions)) nel tipo di elemento della matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="467ae-836">Alternatively, the invocation can specify zero or more arguments for the parameter array, where each argument is an expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the element type of the parameter array.</span></span> <span data-ttu-id="467ae-837">In questo caso, la chiamata crea un'istanza del tipo di matrice di parametri con una lunghezza corrispondente al numero di argomenti, inizializza gli elementi dell'istanza di matrice con i valori di argomento specificati e usa l'istanza di matrice appena creata come valore effettivo argomento.</span><span class="sxs-lookup"><span data-stu-id="467ae-837">In this case, the invocation creates an instance of the parameter array type with a length corresponding to the number of arguments, initializes the elements of the array instance with the given argument values, and uses the newly created array instance as the actual argument.</span></span>

<span data-ttu-id="467ae-838">Eccetto per consentire un numero variabile di argomenti in una chiamata, una matrice di parametri è esattamente equivalente a un parametro valore ([parametri valore](classes.md#value-parameters)) dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-838">Except for allowing a variable number of arguments in an invocation, a parameter array is precisely equivalent to a value parameter ([Value parameters](classes.md#value-parameters)) of the same type.</span></span>

<span data-ttu-id="467ae-839">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-839">The example</span></span>
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
<span data-ttu-id="467ae-840">produce l'output</span><span class="sxs-lookup"><span data-stu-id="467ae-840">produces the output</span></span>
```console
Array contains 3 elements: 1 2 3
Array contains 4 elements: 10 20 30 40
Array contains 0 elements:
```

<span data-ttu-id="467ae-841">La prima chiamata di passa `F` semplicemente la matrice `a` come parametro di valore.</span><span class="sxs-lookup"><span data-stu-id="467ae-841">The first invocation of `F` simply passes the array `a` as a value parameter.</span></span> <span data-ttu-id="467ae-842">La seconda chiamata di `F` crea automaticamente un elemento a quattro elementi `int[]` con i valori dell'elemento specificati e passa l'istanza di matrice come parametro di valore.</span><span class="sxs-lookup"><span data-stu-id="467ae-842">The second invocation of `F` automatically creates a four-element `int[]` with the given element values and passes that array instance as a value parameter.</span></span> <span data-ttu-id="467ae-843">Analogamente, la terza chiamata di `F` crea un elemento `int[]` zero e passa l'istanza come parametro valore.</span><span class="sxs-lookup"><span data-stu-id="467ae-843">Likewise, the third invocation of `F` creates a zero-element `int[]` and passes that instance as a value parameter.</span></span> <span data-ttu-id="467ae-844">La seconda e la terza chiamata sono esattamente equivalenti alla scrittura:</span><span class="sxs-lookup"><span data-stu-id="467ae-844">The second and third invocations are precisely equivalent to writing:</span></span>
```csharp
F(new int[] {10, 20, 30, 40});
F(new int[] {});
```

<span data-ttu-id="467ae-845">Quando si esegue la risoluzione dell'overload, un metodo con una matrice di parametri può essere applicabile nel formato normale o nella forma espansa ([membro della funzione applicabile](expressions.md#applicable-function-member)).</span><span class="sxs-lookup"><span data-stu-id="467ae-845">When performing overload resolution, a method with a parameter array may be applicable either in its normal form or in its expanded form ([Applicable function member](expressions.md#applicable-function-member)).</span></span> <span data-ttu-id="467ae-846">Il form espanso di un metodo è disponibile solo se il formato normale del metodo non è applicabile e solo se un metodo applicabile con la stessa firma del form espanso non è già dichiarato nello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-846">The expanded form of a method is available only if the normal form of the method is not applicable and only if an applicable method with the same signature as the expanded form is not already declared in the same type.</span></span>

<span data-ttu-id="467ae-847">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-847">The example</span></span>
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
<span data-ttu-id="467ae-848">produce l'output</span><span class="sxs-lookup"><span data-stu-id="467ae-848">produces the output</span></span>
```console
F();
F(object[]);
F(object,object);
F(object[]);
F(object[]);
```

<span data-ttu-id="467ae-849">Nell'esempio, due delle possibili forme espanse del metodo con una matrice di parametri sono già incluse nella classe come metodi normali.</span><span class="sxs-lookup"><span data-stu-id="467ae-849">In the example, two of the possible expanded forms of the method with a parameter array are already included in the class as regular methods.</span></span> <span data-ttu-id="467ae-850">Tali forme espanse pertanto non vengono considerate quando si esegue la risoluzione dell'overload e la prima e la terza chiamata al metodo selezionano quindi i metodi normali.</span><span class="sxs-lookup"><span data-stu-id="467ae-850">These expanded forms are therefore not considered when performing overload resolution, and the first and third method invocations thus select the regular methods.</span></span> <span data-ttu-id="467ae-851">Quando una classe dichiara un metodo con una matrice di parametri, non è insolito includere anche alcuni dei form espansi come metodi normali.</span><span class="sxs-lookup"><span data-stu-id="467ae-851">When a class declares a method with a parameter array, it is not uncommon to also include some of the expanded forms as regular methods.</span></span> <span data-ttu-id="467ae-852">In questo modo è possibile evitare l'allocazione di un'istanza di matrice che si verifica quando viene richiamata una forma espansa di un metodo con una matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="467ae-852">By doing so it is possible to avoid the allocation of an array instance that occurs when an expanded form of a method with a parameter array is invoked.</span></span>

<span data-ttu-id="467ae-853">Quando il tipo di una matrice di parametri `object[]`è, una potenziale ambiguità si verifica tra il formato normale del metodo e il formato speso per un singolo `object` parametro.</span><span class="sxs-lookup"><span data-stu-id="467ae-853">When the type of a parameter array is `object[]`, a potential ambiguity arises between the normal form of the method and the expended form for a single `object` parameter.</span></span> <span data-ttu-id="467ae-854">Il motivo dell'ambiguità è che un oggetto `object[]` è implicitamente convertibile nel tipo `object`.</span><span class="sxs-lookup"><span data-stu-id="467ae-854">The reason for the ambiguity is that an `object[]` is itself implicitly convertible to type `object`.</span></span> <span data-ttu-id="467ae-855">Tuttavia, l'ambiguità non presenta alcun problema poiché può essere risolta inserendo un cast se necessario.</span><span class="sxs-lookup"><span data-stu-id="467ae-855">The ambiguity presents no problem, however, since it can be resolved by inserting a cast if needed.</span></span>

<span data-ttu-id="467ae-856">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-856">The example</span></span>
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
<span data-ttu-id="467ae-857">produce l'output</span><span class="sxs-lookup"><span data-stu-id="467ae-857">produces the output</span></span>
```console
System.Int32 System.String System.Double
System.Object[]
System.Object[]
System.Int32 System.String System.Double
```

<span data-ttu-id="467ae-858">Nella prima e nell'ultima chiamata di `F`, il formato normale di `F` è applicabile perché esiste una conversione implicita dal tipo di argomento al tipo di parametro (entrambi sono di tipo `object[]`).</span><span class="sxs-lookup"><span data-stu-id="467ae-858">In the first and last invocations of `F`, the normal form of `F` is applicable because an implicit conversion exists from the argument type to the parameter type (both are of type `object[]`).</span></span> <span data-ttu-id="467ae-859">Pertanto, la risoluzione dell'overload seleziona il formato `F`normale di e l'argomento viene passato come parametro di valore normale.</span><span class="sxs-lookup"><span data-stu-id="467ae-859">Thus, overload resolution selects the normal form of `F`, and the argument is passed as a regular value parameter.</span></span> <span data-ttu-id="467ae-860">Nella seconda e nella terza chiamata, il formato normale di `F` non è applicabile perché non esiste alcuna conversione implicita dal tipo di argomento al tipo di parametro (il tipo `object` non può essere convertito in modo `object[]`implicito nel tipo).</span><span class="sxs-lookup"><span data-stu-id="467ae-860">In the second and third invocations, the normal form of `F` is not applicable because no implicit conversion exists from the argument type to the parameter type (type `object` cannot be implicitly converted to type `object[]`).</span></span> <span data-ttu-id="467ae-861">Tuttavia, il form espanso di `F` è applicabile, quindi è selezionato dalla risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="467ae-861">However, the expanded form of `F` is applicable, so it is selected by overload resolution.</span></span> <span data-ttu-id="467ae-862">Di conseguenza, viene creato un solo elemento `object[]` dalla chiamata e il singolo elemento della matrice viene inizializzato con il valore dell'argomento specificato (che a sua volta è un riferimento a un oggetto `object[]`).</span><span class="sxs-lookup"><span data-stu-id="467ae-862">As a result, a one-element `object[]` is created by the invocation, and the single element of the array is initialized with the given argument value (which itself is a reference to an `object[]`).</span></span>

### <a name="static-and-instance-methods"></a><span data-ttu-id="467ae-863">Metodi statici e di istanza</span><span class="sxs-lookup"><span data-stu-id="467ae-863">Static and instance methods</span></span>

<span data-ttu-id="467ae-864">Quando una dichiarazione di metodo include `static` un modificatore, tale metodo viene definito come metodo statico.</span><span class="sxs-lookup"><span data-stu-id="467ae-864">When a method declaration includes a `static` modifier, that method is said to be a static method.</span></span> <span data-ttu-id="467ae-865">Quando non `static` è presente alcun modificatore, viene definito un metodo di istanza.</span><span class="sxs-lookup"><span data-stu-id="467ae-865">When no `static` modifier is present, the method is said to be an instance method.</span></span>

<span data-ttu-id="467ae-866">Un metodo statico non agisce su un'istanza specifica e si tratta di un errore in fase di compilazione a cui fare `this` riferimento in un metodo statico.</span><span class="sxs-lookup"><span data-stu-id="467ae-866">A static method does not operate on a specific instance, and it is a compile-time error to refer to `this` in a static method.</span></span>

<span data-ttu-id="467ae-867">Un metodo di istanza opera su una determinata istanza di una classe e a tale istanza è possibile accedere `this` come ([questo accesso](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="467ae-867">An instance method operates on a given instance of a class, and that instance can be accessed as `this` ([This access](expressions.md#this-access)).</span></span>

<span data-ttu-id="467ae-868">Quando si fa riferimento a un metodo in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, se `M` è un metodo statico, `E` deve indicare un tipo contenente `M` e se `M` è un metodo di istanza, `E` deve indicare un'istanza di un tipo contenente `M`.</span><span class="sxs-lookup"><span data-stu-id="467ae-868">When a method is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, if `M` is a static method, `E` must denote a type containing `M`, and if `M` is an instance method, `E` must denote an instance of a type containing `M`.</span></span>

<span data-ttu-id="467ae-869">Le differenze tra i membri statici e i membri di istanza sono illustrate ulteriormente nei [membri statici e di istanza](classes.md#static-and-instance-members).</span><span class="sxs-lookup"><span data-stu-id="467ae-869">The differences between static and instance members are discussed further in [Static and instance members](classes.md#static-and-instance-members).</span></span>

### <a name="virtual-methods"></a><span data-ttu-id="467ae-870">Metodi virtuali</span><span class="sxs-lookup"><span data-stu-id="467ae-870">Virtual methods</span></span>

<span data-ttu-id="467ae-871">Quando una dichiarazione di metodo di istanza `virtual` include un modificatore, tale metodo viene definito come metodo virtuale.</span><span class="sxs-lookup"><span data-stu-id="467ae-871">When an instance method declaration includes a `virtual` modifier, that method is said to be a virtual method.</span></span> <span data-ttu-id="467ae-872">Quando non `virtual` è presente alcun modificatore, il metodo viene definito come metodo non virtuale.</span><span class="sxs-lookup"><span data-stu-id="467ae-872">When no `virtual` modifier is present, the method is said to be a non-virtual method.</span></span>

<span data-ttu-id="467ae-873">L'implementazione di un metodo non virtuale è invariante: L'implementazione è la stessa se il metodo viene richiamato su un'istanza della classe in cui è dichiarata o un'istanza di una classe derivata.</span><span class="sxs-lookup"><span data-stu-id="467ae-873">The implementation of a non-virtual method is invariant: The implementation is the same whether the method is invoked on an instance of the class in which it is declared or an instance of a derived class.</span></span> <span data-ttu-id="467ae-874">Al contrario, l'implementazione di un metodo virtuale può essere sostituita dalle classi derivate.</span><span class="sxs-lookup"><span data-stu-id="467ae-874">In contrast, the implementation of a virtual method can be superseded by derived classes.</span></span> <span data-ttu-id="467ae-875">Il processo di sostituzione dell'implementazione di un metodo virtuale ereditato è noto come override ***di tale*** metodo ([metodi di override](classes.md#override-methods)).</span><span class="sxs-lookup"><span data-stu-id="467ae-875">The process of superseding the implementation of an inherited virtual method is known as ***overriding*** that method ([Override methods](classes.md#override-methods)).</span></span>

<span data-ttu-id="467ae-876">In una chiamata a un metodo virtuale, il ***tipo di runtime*** dell'istanza per cui viene eseguita la chiamata determina l'implementazione del metodo effettivo da richiamare.</span><span class="sxs-lookup"><span data-stu-id="467ae-876">In a virtual method invocation, the ***run-time type*** of the instance for which that invocation takes place determines the actual method implementation to invoke.</span></span> <span data-ttu-id="467ae-877">In una chiamata a un metodo non virtuale, il ***tipo in fase di compilazione*** dell'istanza è il fattore determinante.</span><span class="sxs-lookup"><span data-stu-id="467ae-877">In a non-virtual method invocation, the ***compile-time type*** of the instance is the determining factor.</span></span> <span data-ttu-id="467ae-878">In termini precisi, quando un metodo `N` denominato viene richiamato con un `A` elenco di argomenti in un'istanza con un tipo `C` in fase di compilazione e un `R` tipo in `R` fase di `C` esecuzione (dove è o una classe derivata da `C`), la chiamata viene elaborata come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="467ae-878">In precise terms, when a method named `N` is invoked with an argument list `A` on an instance with a compile-time type `C` and a run-time type `R` (where `R` is either `C` or a class derived from `C`), the invocation is processed as follows:</span></span>

*  <span data-ttu-id="467ae-879">Per prima cosa, la risoluzione dell' `C`overload `N`viene applicata `A`a, e per selezionare un `M` metodo specifico dal set di metodi dichiarato in e ereditato `C`da.</span><span class="sxs-lookup"><span data-stu-id="467ae-879">First, overload resolution is applied to `C`, `N`, and `A`, to select a specific method `M` from the set of methods declared in and inherited by `C`.</span></span> <span data-ttu-id="467ae-880">Questa operazione è descritta in [chiamate al metodo](expressions.md#method-invocations).</span><span class="sxs-lookup"><span data-stu-id="467ae-880">This is described in [Method invocations](expressions.md#method-invocations).</span></span>
*  <span data-ttu-id="467ae-881">Quindi, se `M` è un metodo non virtuale, `M` viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="467ae-881">Then, if `M` is a non-virtual method, `M` is invoked.</span></span>
*  <span data-ttu-id="467ae-882">In caso `M` contrario, è un metodo virtuale e viene richiamata l' `M` implementazione più derivata `R` di rispetto a.</span><span class="sxs-lookup"><span data-stu-id="467ae-882">Otherwise, `M` is a virtual method, and the most derived implementation of `M` with respect to `R` is invoked.</span></span>

<span data-ttu-id="467ae-883">Per ogni metodo virtuale dichiarato in o ereditato da una classe, esiste un' ***implementazione più derivata*** del metodo rispetto a tale classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-883">For every virtual method declared in or inherited by a class, there exists a ***most derived implementation*** of the method with respect to that class.</span></span> <span data-ttu-id="467ae-884">L'implementazione più derivata di un metodo `M` virtuale rispetto a una classe `R` viene determinata nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="467ae-884">The most derived implementation of a virtual method `M` with respect to a class `R` is determined as follows:</span></span>

*  <span data-ttu-id="467ae-885">Se `R` contiene la Dichiarazione `virtual` di introduzione `M`di, si tratta dell'implementazione più derivata di `M`.</span><span class="sxs-lookup"><span data-stu-id="467ae-885">If `R` contains the introducing `virtual` declaration of `M`, then this is the most derived implementation of `M`.</span></span>
*  <span data-ttu-id="467ae-886">In caso contrario `R` , se `override` contiene `M`un valore di, si tratta dell'implementazione più `M`derivata di.</span><span class="sxs-lookup"><span data-stu-id="467ae-886">Otherwise, if `R` contains an `override` of `M`, then this is the most derived implementation of `M`.</span></span>
*  <span data-ttu-id="467ae-887">In caso contrario, l'implementazione più `M` derivata di rispetto `R` a corrisponde all'implementazione più derivata di `M` rispetto alla classe di base diretta di `R`.</span><span class="sxs-lookup"><span data-stu-id="467ae-887">Otherwise, the most derived implementation of `M` with respect to `R` is the same as the most derived implementation of `M` with respect to the direct base class of `R`.</span></span>

<span data-ttu-id="467ae-888">Nell'esempio seguente vengono illustrate le differenze tra i metodi virtuali e non virtuali:</span><span class="sxs-lookup"><span data-stu-id="467ae-888">The following example illustrates the differences between virtual and non-virtual methods:</span></span>
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

<span data-ttu-id="467ae-889">Nell'esempio, `A` introduce un metodo `F` non virtuale e un metodo `G`virtuale.</span><span class="sxs-lookup"><span data-stu-id="467ae-889">In the example, `A` introduces a non-virtual method `F` and a virtual method `G`.</span></span> <span data-ttu-id="467ae-890">La classe `B` introduce un nuovo metodo `F`non virtuale, nascondendo quindi l'oggetto `F`ereditato ed esegue l'override del metodo `G`ereditato.</span><span class="sxs-lookup"><span data-stu-id="467ae-890">The class `B` introduces a new non-virtual method `F`, thus hiding the inherited `F`, and also overrides the inherited method `G`.</span></span> <span data-ttu-id="467ae-891">L'esempio produce l'output:</span><span class="sxs-lookup"><span data-stu-id="467ae-891">The example produces the output:</span></span>
```console
A.F
B.F
B.G
B.G
```

<span data-ttu-id="467ae-892">Si noti che l' `a.G()` istruzione `B.G`richiama, non `A.G`.</span><span class="sxs-lookup"><span data-stu-id="467ae-892">Notice that the statement `a.G()` invokes `B.G`, not `A.G`.</span></span> <span data-ttu-id="467ae-893">Questo è dovuto al fatto che il tipo in fase di esecuzione dell'istanza `B`(ovvero), non il tipo in fase di compilazione dell'istanza ( `A`ovvero), determina l'implementazione del metodo effettivo da richiamare.</span><span class="sxs-lookup"><span data-stu-id="467ae-893">This is because the run-time type of the instance (which is `B`), not the compile-time type of the instance (which is `A`), determines the actual method implementation to invoke.</span></span>

<span data-ttu-id="467ae-894">Poiché i metodi possono nascondere i metodi ereditati, è possibile che una classe contenga diversi metodi virtuali con la stessa firma.</span><span class="sxs-lookup"><span data-stu-id="467ae-894">Because methods are allowed to hide inherited methods, it is possible for a class to contain several virtual methods with the same signature.</span></span> <span data-ttu-id="467ae-895">Questo non presenta un problema di ambiguità, dal momento che tutto tranne il metodo più derivato è nascosto.</span><span class="sxs-lookup"><span data-stu-id="467ae-895">This does not present an ambiguity problem, since all but the most derived method are hidden.</span></span> <span data-ttu-id="467ae-896">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-896">In the example</span></span>
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
<span data-ttu-id="467ae-897">le `C` classi `D` e contengono due metodi virtuali con la stessa firma: Quello introdotto da `A` e quello introdotto da `C`.</span><span class="sxs-lookup"><span data-stu-id="467ae-897">the `C` and `D` classes contain two virtual methods with the same signature: The one introduced by `A` and the one introduced by `C`.</span></span> <span data-ttu-id="467ae-898">Il metodo introdotto da `C` nasconde il metodo ereditato da. `A`</span><span class="sxs-lookup"><span data-stu-id="467ae-898">The method introduced by `C` hides the method inherited from `A`.</span></span> <span data-ttu-id="467ae-899">Pertanto, la dichiarazione di override `D` in esegue l'override del `C`metodo introdotto da e non è possibile `D` eseguire l'override del metodo introdotto `A`da.</span><span class="sxs-lookup"><span data-stu-id="467ae-899">Thus, the override declaration in `D` overrides the method introduced by `C`, and it is not possible for `D` to override the method introduced by `A`.</span></span> <span data-ttu-id="467ae-900">L'esempio produce l'output:</span><span class="sxs-lookup"><span data-stu-id="467ae-900">The example produces the output:</span></span>
```console
B.F
B.F
D.F
D.F
```

<span data-ttu-id="467ae-901">Si noti che è possibile richiamare il metodo virtuale nascosto accedendo a un'istanza di `D` tramite un tipo meno derivato in cui il metodo non è nascosto.</span><span class="sxs-lookup"><span data-stu-id="467ae-901">Note that it is possible to invoke the hidden virtual method by accessing an instance of `D` through a less derived type in which the method is not hidden.</span></span>

### <a name="override-methods"></a><span data-ttu-id="467ae-902">Metodi di override</span><span class="sxs-lookup"><span data-stu-id="467ae-902">Override methods</span></span>

<span data-ttu-id="467ae-903">Quando una dichiarazione di metodo di istanza `override` include un modificatore, il metodo viene definito ***metodo di override***.</span><span class="sxs-lookup"><span data-stu-id="467ae-903">When an instance method declaration includes an `override` modifier, the method is said to be an ***override method***.</span></span> <span data-ttu-id="467ae-904">Un metodo di override esegue l'override di un metodo virtuale ereditato con la stessa firma.</span><span class="sxs-lookup"><span data-stu-id="467ae-904">An override method overrides an inherited virtual method with the same signature.</span></span> <span data-ttu-id="467ae-905">Mentre una dichiarazione di metodo virtuale introduce un nuovo metodo, una dichiarazione di metodo di override specializza un metodo virtuale ereditato esistente specificando una nuova implementazione del metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-905">Whereas a virtual method declaration introduces a new method, an override method declaration specializes an existing inherited virtual method by providing a new implementation of that method.</span></span>

<span data-ttu-id="467ae-906">Il metodo sottoposto a override `override` da una dichiarazione è noto come ***metodo di base sottoposto a override***.</span><span class="sxs-lookup"><span data-stu-id="467ae-906">The method overridden by an `override` declaration is known as the ***overridden base method***.</span></span> <span data-ttu-id="467ae-907">Per `M` un metodo di override dichiarato in una `C`classe, il metodo di base sottoposto a override viene determinato esaminando ogni tipo `C`di classe di base di, a partire dal `C` tipo di classe di base diretta di e continuando con ogni operazione successiva tipo di classe di base diretta, finché in un determinato tipo di classe di base viene individuato almeno un metodo accessibile con la `M` stessa firma di dopo la sostituzione degli argomenti di tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-907">For an override method `M` declared in a class `C`, the overridden base method is determined by examining each base class type of `C`, starting with the direct base class type of `C` and continuing with each successive direct base class type, until in a given base class type at least one accessible method is located which has the same signature as `M` after substitution of type arguments.</span></span> <span data-ttu-id="467ae-908">Al fine di individuare il metodo di base sottoposto a override, un metodo viene considerato accessibile se `public` `protected` `protected internal`è, se è, se è o se `internal` è e dichiarato nello stesso programma di `C`.</span><span class="sxs-lookup"><span data-stu-id="467ae-908">For the purposes of locating the overridden base method, a method is considered accessible if it is `public`, if it is `protected`, if it is `protected internal`, or if it is `internal` and declared in the same program as `C`.</span></span>

<span data-ttu-id="467ae-909">Si verifica un errore in fase di compilazione a meno che non siano soddisfatte tutte le condizioni seguenti per una dichiarazione di override:</span><span class="sxs-lookup"><span data-stu-id="467ae-909">A compile-time error occurs unless all of the following are true for an override declaration:</span></span>

*  <span data-ttu-id="467ae-910">È possibile trovare un metodo di base sottoposto a override come descritto in precedenza.</span><span class="sxs-lookup"><span data-stu-id="467ae-910">An overridden base method can be located as described above.</span></span>
*  <span data-ttu-id="467ae-911">Esiste esattamente un metodo di base sottoposto a override.</span><span class="sxs-lookup"><span data-stu-id="467ae-911">There is exactly one such overridden base method.</span></span> <span data-ttu-id="467ae-912">Questa restrizione ha effetto solo se il tipo di classe base è un tipo costruito in cui la sostituzione di argomenti di tipo rende la firma di due metodi uguali.</span><span class="sxs-lookup"><span data-stu-id="467ae-912">This restriction has effect only if the base class type is a constructed type where the substitution of type arguments makes the signature of two methods the same.</span></span>
*  <span data-ttu-id="467ae-913">Il metodo di base sottoposto a override è un metodo virtuale, astratto o di override.</span><span class="sxs-lookup"><span data-stu-id="467ae-913">The overridden base method is a virtual, abstract, or override method.</span></span> <span data-ttu-id="467ae-914">In altre parole, il metodo di base sottoposto a override non può essere statico o non virtuale.</span><span class="sxs-lookup"><span data-stu-id="467ae-914">In other words, the overridden base method cannot be static or non-virtual.</span></span>
*  <span data-ttu-id="467ae-915">Il metodo di base sottoposto a override non è un metodo sealed.</span><span class="sxs-lookup"><span data-stu-id="467ae-915">The overridden base method is not a sealed method.</span></span>
*  <span data-ttu-id="467ae-916">Il metodo di override e il metodo di base sottoposto a override hanno lo stesso tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="467ae-916">The override method and the overridden base method have the same return type.</span></span>
*  <span data-ttu-id="467ae-917">La dichiarazione di override e il metodo di base sottoposto a override hanno la stessa accessibilità dichiarata.</span><span class="sxs-lookup"><span data-stu-id="467ae-917">The override declaration and the overridden base method have the same declared accessibility.</span></span> <span data-ttu-id="467ae-918">In altre parole, una dichiarazione di override non può modificare l'accessibilità del metodo virtuale.</span><span class="sxs-lookup"><span data-stu-id="467ae-918">In other words, an override declaration cannot change the accessibility of the virtual method.</span></span> <span data-ttu-id="467ae-919">Tuttavia, se il metodo di base sottoposto a override è protetto come interno e viene dichiarato in un assembly diverso rispetto all'assembly che contiene il metodo di override, l'accessibilità dichiarata del metodo di override deve essere protetta.</span><span class="sxs-lookup"><span data-stu-id="467ae-919">However, if the overridden base method is protected internal and it is declared in a different assembly than the assembly containing the override method then the override method's declared accessibility must be protected.</span></span>
*  <span data-ttu-id="467ae-920">La dichiarazione di override non specifica il parametro di tipo-Constraints-clausole.</span><span class="sxs-lookup"><span data-stu-id="467ae-920">The override declaration does not specify type-parameter-constraints-clauses.</span></span> <span data-ttu-id="467ae-921">I vincoli vengono invece ereditati dal metodo di base sottoposto a override.</span><span class="sxs-lookup"><span data-stu-id="467ae-921">Instead the constraints are inherited from the overridden base method.</span></span> <span data-ttu-id="467ae-922">Si noti che i vincoli che sono parametri di tipo nel metodo sottoposto a override possono essere sostituiti da argomenti di tipo nel vincolo ereditato.</span><span class="sxs-lookup"><span data-stu-id="467ae-922">Note that constraints that are type parameters in the overridden method may be replaced by type arguments in the inherited constraint.</span></span> <span data-ttu-id="467ae-923">Questo può causare vincoli che non sono validi quando specificati in modo esplicito, ad esempio tipi di valore o tipi sealed.</span><span class="sxs-lookup"><span data-stu-id="467ae-923">This can lead to constraints that are not legal when explicitly specified, such as value types or sealed types.</span></span>

<span data-ttu-id="467ae-924">Nell'esempio seguente viene illustrato il funzionamento delle regole di override per le classi generiche:</span><span class="sxs-lookup"><span data-stu-id="467ae-924">The following example demonstrates how the overriding rules work for generic classes:</span></span>
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

<span data-ttu-id="467ae-925">Una dichiarazione di override può accedere al metodo di base sottoposto a override utilizzando un *base_access* ([accesso di base](expressions.md#base-access)).</span><span class="sxs-lookup"><span data-stu-id="467ae-925">An override declaration can access the overridden base method using a *base_access* ([Base access](expressions.md#base-access)).</span></span> <span data-ttu-id="467ae-926">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-926">In the example</span></span>
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
<span data-ttu-id="467ae-927">la `base.PrintFields()` chiamata in `B` richiama il `PrintFields` metodo dichiarato in `A`.</span><span class="sxs-lookup"><span data-stu-id="467ae-927">the `base.PrintFields()` invocation in `B` invokes the `PrintFields` method declared in `A`.</span></span> <span data-ttu-id="467ae-928">Un *base_access* Disabilita il meccanismo di chiamata virtuale e considera semplicemente il metodo di base come metodo non virtuale.</span><span class="sxs-lookup"><span data-stu-id="467ae-928">A *base_access* disables the virtual invocation mechanism and simply treats the base method as a non-virtual method.</span></span> <span data-ttu-id="467ae-929">Se la `B` chiamata è stata scritta `((A)this).PrintFields()`, richiamerebbe in modo ricorsivo `PrintFields` il metodo dichiarato `B`in, non quello dichiarato in `A`, poiché `PrintFields` è virtuale e il tipo in fase di esecuzione di `((A)this)` è .`B`</span><span class="sxs-lookup"><span data-stu-id="467ae-929">Had the invocation in `B` been written `((A)this).PrintFields()`, it would recursively invoke the `PrintFields` method declared in `B`, not the one declared in `A`, since `PrintFields` is virtual and the run-time type of `((A)this)` is `B`.</span></span>

<span data-ttu-id="467ae-930">Solo se si include `override` un modificatore, un metodo esegue l'override di un altro metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-930">Only by including an `override` modifier can a method override another method.</span></span> <span data-ttu-id="467ae-931">In tutti gli altri casi, un metodo con la stessa firma di un metodo ereditato nasconde semplicemente il metodo ereditato.</span><span class="sxs-lookup"><span data-stu-id="467ae-931">In all other cases, a method with the same signature as an inherited method simply hides the inherited method.</span></span> <span data-ttu-id="467ae-932">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-932">In the example</span></span>
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
<span data-ttu-id="467ae-933">il `F` metodo in `B` non include un `override` modificatore e pertanto non esegue l'override `F` del metodo `A`in.</span><span class="sxs-lookup"><span data-stu-id="467ae-933">the `F` method in `B` does not include an `override` modifier and therefore does not override the `F` method in `A`.</span></span> <span data-ttu-id="467ae-934">Il `F` metodo in `B` nasconde invece il metodo in `A`e viene segnalato un avviso perché la dichiarazione non include un `new` modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-934">Rather, the `F` method in `B` hides the method in `A`, and a warning is reported because the declaration does not include a `new` modifier.</span></span>

<span data-ttu-id="467ae-935">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-935">In the example</span></span>
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
<span data-ttu-id="467ae-936">il `F` metodo in `B` nasconde il metodo virtuale `F` ereditato da `A`.</span><span class="sxs-lookup"><span data-stu-id="467ae-936">the `F` method in `B` hides the virtual `F` method inherited from `A`.</span></span> <span data-ttu-id="467ae-937">Poiché il nuovo `F` in `B` ha accesso privato, l'ambito include solo il corpo della `B` classe e non si estende a `C`.</span><span class="sxs-lookup"><span data-stu-id="467ae-937">Since the new `F` in `B` has private access, its scope only includes the class body of `B` and does not extend to `C`.</span></span> <span data-ttu-id="467ae-938">Pertanto, la dichiarazione di `F` in `C` è consentita per `F` eseguire l' `A`override dell'oggetto ereditato da.</span><span class="sxs-lookup"><span data-stu-id="467ae-938">Therefore, the declaration of `F` in `C` is permitted to override the `F` inherited from `A`.</span></span>

### <a name="sealed-methods"></a><span data-ttu-id="467ae-939">Metodi sealed</span><span class="sxs-lookup"><span data-stu-id="467ae-939">Sealed methods</span></span>

<span data-ttu-id="467ae-940">Quando una dichiarazione di metodo di istanza `sealed` include un modificatore, tale metodo viene definito come ***metodo sealed***.</span><span class="sxs-lookup"><span data-stu-id="467ae-940">When an instance method declaration includes a `sealed` modifier, that method is said to be a ***sealed method***.</span></span> <span data-ttu-id="467ae-941">Se una dichiarazione di metodo di istanza `sealed` include il modificatore, deve includere `override` anche il modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-941">If an instance method declaration includes the  `sealed` modifier, it must also include the `override` modifier.</span></span> <span data-ttu-id="467ae-942">L' `sealed` utilizzo del modificatore impedisce a una classe derivata di eseguire l'override del metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-942">Use of the `sealed` modifier prevents a derived class from further overriding the method.</span></span>

<span data-ttu-id="467ae-943">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-943">In the example</span></span>
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
<span data-ttu-id="467ae-944">la classe `B` fornisce due metodi di override: `F` un metodo con il `sealed` modificatore e `G` un metodo che non lo è.</span><span class="sxs-lookup"><span data-stu-id="467ae-944">the class `B` provides two override methods: an `F` method that has the `sealed` modifier and a `G` method that does not.</span></span> <span data-ttu-id="467ae-945">`B`l'uso del sealed `modifier` impedisce `C` di eseguire ulteriori override `F`.</span><span class="sxs-lookup"><span data-stu-id="467ae-945">`B`'s use of the sealed `modifier` prevents `C` from further overriding `F`.</span></span>

### <a name="abstract-methods"></a><span data-ttu-id="467ae-946">Metodi astratti</span><span class="sxs-lookup"><span data-stu-id="467ae-946">Abstract methods</span></span>

<span data-ttu-id="467ae-947">Quando una dichiarazione di metodo di istanza `abstract` include un modificatore, tale metodo viene definito ***metodo astratto***.</span><span class="sxs-lookup"><span data-stu-id="467ae-947">When an instance method declaration includes an `abstract` modifier, that method is said to be an ***abstract method***.</span></span> <span data-ttu-id="467ae-948">Anche se un metodo astratto è implicitamente un metodo virtuale, non può avere il modificatore `virtual`.</span><span class="sxs-lookup"><span data-stu-id="467ae-948">Although an abstract method is implicitly also a virtual method, it cannot have the modifier `virtual`.</span></span>

<span data-ttu-id="467ae-949">Una dichiarazione di metodo astratto introduce un nuovo metodo virtuale ma non fornisce un'implementazione di tale metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-949">An abstract method declaration introduces a new virtual method but does not provide an implementation of that method.</span></span> <span data-ttu-id="467ae-950">Al contrario, le classi derivate non astratte sono necessarie per fornire la propria implementazione eseguendo l'override del metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-950">Instead, non-abstract derived classes are required to provide their own implementation by overriding that method.</span></span> <span data-ttu-id="467ae-951">Poiché un metodo astratto non fornisce alcuna implementazione effettiva, il *method_body* di un metodo astratto è costituito semplicemente da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="467ae-951">Because an abstract method provides no actual implementation, the *method_body* of an abstract method simply consists of a semicolon.</span></span>

<span data-ttu-id="467ae-952">Le dichiarazioni di metodo abstract sono consentite solo nelle classi astratte ([classi astratte](classes.md#abstract-classes)).</span><span class="sxs-lookup"><span data-stu-id="467ae-952">Abstract method declarations are only permitted in abstract classes ([Abstract classes](classes.md#abstract-classes)).</span></span>

<span data-ttu-id="467ae-953">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-953">In the example</span></span>
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
<span data-ttu-id="467ae-954">la `Shape` classe definisce la nozione astratta di un oggetto Shape geometrico che può disegnare se stesso.</span><span class="sxs-lookup"><span data-stu-id="467ae-954">the `Shape` class defines the abstract notion of a geometrical shape object that can paint itself.</span></span> <span data-ttu-id="467ae-955">Il `Paint` metodo è astratto perché non esiste un'implementazione predefinita significativa.</span><span class="sxs-lookup"><span data-stu-id="467ae-955">The `Paint` method is abstract because there is no meaningful default implementation.</span></span> <span data-ttu-id="467ae-956">Le `Ellipse` classi `Box` e sono implementazioni concrete `Shape` .</span><span class="sxs-lookup"><span data-stu-id="467ae-956">The `Ellipse` and `Box` classes are concrete `Shape` implementations.</span></span> <span data-ttu-id="467ae-957">Poiché queste classi sono non astratte, è necessario eseguire l'override del `Paint` metodo e fornire un'implementazione effettiva.</span><span class="sxs-lookup"><span data-stu-id="467ae-957">Because these classes are non-abstract, they are required to override the `Paint` method and provide an actual implementation.</span></span>

<span data-ttu-id="467ae-958">Si tratta di un errore in fase di compilazione per un *base_access* ([accesso di base](expressions.md#base-access)) per fare riferimento a un metodo astratto.</span><span class="sxs-lookup"><span data-stu-id="467ae-958">It is a compile-time error for a *base_access* ([Base access](expressions.md#base-access)) to reference an abstract method.</span></span> <span data-ttu-id="467ae-959">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-959">In the example</span></span>
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
<span data-ttu-id="467ae-960">viene segnalato un errore in fase di compilazione per `base.F()` la chiamata, perché fa riferimento a un metodo astratto.</span><span class="sxs-lookup"><span data-stu-id="467ae-960">a compile-time error is reported for the `base.F()` invocation because it references an abstract method.</span></span>

<span data-ttu-id="467ae-961">Una dichiarazione di metodo astratto è consentita per eseguire l'override di un metodo virtuale.</span><span class="sxs-lookup"><span data-stu-id="467ae-961">An abstract method declaration is permitted to override a virtual method.</span></span> <span data-ttu-id="467ae-962">Ciò consente a una classe astratta di forzare la riimplementazione del metodo nelle classi derivate e rende non disponibile l'implementazione originale del metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-962">This allows an abstract class to force re-implementation of the method in derived classes, and makes the original implementation of the method unavailable.</span></span> <span data-ttu-id="467ae-963">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-963">In the example</span></span>
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
<span data-ttu-id="467ae-964">la `A` classe dichiara un metodo virtuale, la `B` classe esegue l'override di questo metodo con un metodo `C` astratto e la classe esegue l'override del metodo astratto per fornire la propria implementazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-964">class `A` declares a virtual method, class `B` overrides this method with an abstract method, and class `C` overrides the abstract method to provide its own implementation.</span></span>

### <a name="external-methods"></a><span data-ttu-id="467ae-965">Metodi esterni</span><span class="sxs-lookup"><span data-stu-id="467ae-965">External methods</span></span>

<span data-ttu-id="467ae-966">Quando una dichiarazione di metodo include `extern` un modificatore, viene definito un metodo ***esterno***.</span><span class="sxs-lookup"><span data-stu-id="467ae-966">When a method declaration includes an `extern` modifier, that method is said to be an ***external method***.</span></span> <span data-ttu-id="467ae-967">I metodi esterni vengono implementati esternamente, in genere utilizzando un C#linguaggio diverso da.</span><span class="sxs-lookup"><span data-stu-id="467ae-967">External methods are implemented externally, typically using a language other than C#.</span></span> <span data-ttu-id="467ae-968">Poiché una dichiarazione di metodo esterno non fornisce alcuna implementazione effettiva, il *method_body* di un metodo esterno è semplicemente costituito da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="467ae-968">Because an external method declaration provides no actual implementation, the *method_body* of an external method simply consists of a semicolon.</span></span> <span data-ttu-id="467ae-969">Un metodo esterno potrebbe non essere generico.</span><span class="sxs-lookup"><span data-stu-id="467ae-969">An external method may not be generic.</span></span>

<span data-ttu-id="467ae-970">Il `extern` modificatore viene in genere usato in combinazione `DllImport` con un attributo ([interoperabilità con componenti com e Win32](attributes.md#interoperation-with-com-and-win32-components)), consentendo l'implementazione di metodi esterni tramite dll (librerie a collegamento dinamico).</span><span class="sxs-lookup"><span data-stu-id="467ae-970">The `extern` modifier is typically used in conjunction with a `DllImport` attribute ([Interoperation with COM and Win32 components](attributes.md#interoperation-with-com-and-win32-components)), allowing external methods to be implemented by DLLs (Dynamic Link Libraries).</span></span> <span data-ttu-id="467ae-971">L'ambiente di esecuzione può supportare altri meccanismi in base ai quali è possibile fornire implementazioni di metodi esterni.</span><span class="sxs-lookup"><span data-stu-id="467ae-971">The execution environment may support other mechanisms whereby implementations of external methods can be provided.</span></span>

<span data-ttu-id="467ae-972">Quando un metodo esterno include un `DllImport` attributo, la dichiarazione del metodo deve includere anche `static` un modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-972">When an external method includes a `DllImport` attribute, the method declaration must also include a `static` modifier.</span></span> <span data-ttu-id="467ae-973">Questo esempio illustra l'uso del `extern` modificatore e dell' `DllImport` attributo:</span><span class="sxs-lookup"><span data-stu-id="467ae-973">This example demonstrates the use of the `extern` modifier and the `DllImport` attribute:</span></span>
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

### <a name="partial-methods-recap"></a><span data-ttu-id="467ae-974">Metodi parziali (riepilogo)</span><span class="sxs-lookup"><span data-stu-id="467ae-974">Partial methods (recap)</span></span>

<span data-ttu-id="467ae-975">Quando una dichiarazione di metodo include `partial` un modificatore, tale metodo viene definito come ***metodo parziale***.</span><span class="sxs-lookup"><span data-stu-id="467ae-975">When a method declaration includes a `partial` modifier, that method is said to be a ***partial method***.</span></span> <span data-ttu-id="467ae-976">I metodi parziali possono essere dichiarati solo come membri di tipi parziali ([tipi parziali](classes.md#partial-types)) e sono soggetti a una serie di restrizioni.</span><span class="sxs-lookup"><span data-stu-id="467ae-976">Partial methods can only be declared as members of partial types ([Partial types](classes.md#partial-types)), and are subject to a number of restrictions.</span></span> <span data-ttu-id="467ae-977">I metodi parziali sono descritti ulteriormente in [metodi parziali](classes.md#partial-methods).</span><span class="sxs-lookup"><span data-stu-id="467ae-977">Partial methods are further described in [Partial methods](classes.md#partial-methods).</span></span>

### <a name="extension-methods"></a><span data-ttu-id="467ae-978">Metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="467ae-978">Extension methods</span></span>

<span data-ttu-id="467ae-979">Quando il primo parametro di un metodo include il `this` modificatore, tale metodo viene definito come ***metodo di estensione***.</span><span class="sxs-lookup"><span data-stu-id="467ae-979">When the first parameter of a method includes the `this` modifier, that method is said to be an ***extension method***.</span></span> <span data-ttu-id="467ae-980">I metodi di estensione possono essere dichiarati solo in classi statiche non generiche non nidificate.</span><span class="sxs-lookup"><span data-stu-id="467ae-980">Extension methods can only be declared in non-generic, non-nested static classes.</span></span> <span data-ttu-id="467ae-981">Il primo parametro di un metodo di estensione non può avere modificatori diversi `this`da e il tipo di parametro non può essere un tipo di puntatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-981">The first parameter of an extension method can have no modifiers other than `this`, and the parameter type cannot be a pointer type.</span></span>

<span data-ttu-id="467ae-982">Di seguito è riportato un esempio di una classe statica che dichiara due metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="467ae-982">The following is an example of a static class that declares two extension methods:</span></span>
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

<span data-ttu-id="467ae-983">Un metodo di estensione è un metodo statico normale.</span><span class="sxs-lookup"><span data-stu-id="467ae-983">An extension method is a regular static method.</span></span> <span data-ttu-id="467ae-984">Inoltre, quando la classe statica che lo contiene è nell'ambito, un metodo di estensione può essere richiamato usando la sintassi di chiamata del metodo di istanza ([chiamate al metodo di estensione](expressions.md#extension-method-invocations)), usando l'espressione Receiver come primo argomento.</span><span class="sxs-lookup"><span data-stu-id="467ae-984">In addition, where its enclosing static class is in scope, an extension method can be invoked using instance method invocation syntax ([Extension method invocations](expressions.md#extension-method-invocations)), using the receiver expression as the first argument.</span></span>

<span data-ttu-id="467ae-985">Il programma seguente usa i metodi di estensione dichiarati in precedenza:</span><span class="sxs-lookup"><span data-stu-id="467ae-985">The following program uses the extension methods declared above:</span></span>
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

<span data-ttu-id="467ae-986">Il `Slice` metodo è disponibile `string[]`in e il `ToInt32` metodo è disponibile in `string`, perché sono stati dichiarati come metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="467ae-986">The `Slice` method is available on the `string[]`, and the `ToInt32` method is available on `string`, because they have been declared as extension methods.</span></span> <span data-ttu-id="467ae-987">Il significato del programma è identico a quello riportato di seguito, usando le normali chiamate al metodo statico:</span><span class="sxs-lookup"><span data-stu-id="467ae-987">The meaning of the program is the same as the following, using ordinary static method calls:</span></span>
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

### <a name="method-body"></a><span data-ttu-id="467ae-988">Corpo del metodo</span><span class="sxs-lookup"><span data-stu-id="467ae-988">Method body</span></span>

<span data-ttu-id="467ae-989">Il *method_body* di una dichiarazione di metodo è costituito da un corpo del blocco, da un corpo dell'espressione o da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="467ae-989">The *method_body* of a method declaration consists of either a block body, an expression body or a semicolon.</span></span>

<span data-ttu-id="467ae-990">Il ***tipo di risultato*** di un metodo `void` è se il tipo restituito `void`è o se il metodo è asincrono e il tipo restituito è `System.Threading.Tasks.Task`.</span><span class="sxs-lookup"><span data-stu-id="467ae-990">The ***result type*** of a method is `void` if the return type is `void`, or if the method is async and the return type is `System.Threading.Tasks.Task`.</span></span> <span data-ttu-id="467ae-991">In caso contrario, il tipo di risultato di un metodo non asincrono è il tipo restituito e il tipo di risultato di un metodo asincrono con `System.Threading.Tasks.Task<T>` tipo `T`restituito è.</span><span class="sxs-lookup"><span data-stu-id="467ae-991">Otherwise, the result type of a non-async method is its return type, and the result type of an async method with return type `System.Threading.Tasks.Task<T>` is `T`.</span></span>

<span data-ttu-id="467ae-992">Quando un metodo ha un `void` tipo di risultato e un corpo del `return` blocco,[le istruzioni (istruzione return](statements.md#the-return-statement)) nel blocco non sono autorizzate a specificare un'espressione.</span><span class="sxs-lookup"><span data-stu-id="467ae-992">When a method has a `void` result type and a block body, `return` statements ([The return statement](statements.md#the-return-statement)) in the block are not permitted to specify an expression.</span></span> <span data-ttu-id="467ae-993">Se l'esecuzione del blocco di un metodo void viene completata normalmente (ovvero, il controllo esce dalla fine del corpo del metodo), il metodo restituisce semplicemente al chiamante corrente.</span><span class="sxs-lookup"><span data-stu-id="467ae-993">If execution of the block of a void method completes normally (that is, control flows off the end of the method body), that method simply returns to its current caller.</span></span>
    
<span data-ttu-id="467ae-994">Quando un metodo ha un risultato `void` e un corpo dell'espressione, l'espressione `E` deve essere un *statement_expression*e il corpo è esattamente equivalente a un corpo del blocco nel formato `{ E; }`.</span><span class="sxs-lookup"><span data-stu-id="467ae-994">When a method has a `void` result and an expression body, the expression `E` must be a *statement_expression*, and the body is exactly equivalent to a block body of the form `{ E; }`.</span></span>
    
<span data-ttu-id="467ae-995">Quando un metodo ha un tipo di risultato non void e un corpo del blocco, `return` ogni istruzione nel blocco deve specificare un'espressione convertibile in modo implicito nel tipo di risultato.</span><span class="sxs-lookup"><span data-stu-id="467ae-995">When a method has a non-void result type and a block body, each `return` statement in the block must specify an expression that is implicitly convertible to the result type.</span></span> <span data-ttu-id="467ae-996">L'endpoint di un corpo di blocco di un metodo di restituzione di valori non deve essere raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="467ae-996">The endpoint of a block body of a value-returning method must not be reachable.</span></span> <span data-ttu-id="467ae-997">In altre parole, in un metodo di restituzione di valori con un corpo del blocco, il controllo non può propagarsi alla fine del corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-997">In other words, in a value-returning method with a block body, control is not permitted to flow off the end of the method body.</span></span>
    
<span data-ttu-id="467ae-998">Quando un metodo ha un tipo di risultato non void e un corpo dell'espressione, l'espressione deve essere convertibile in modo implicito nel tipo di risultato e il corpo è esattamente equivalente a un corpo del `{ return E; }`blocco del form.</span><span class="sxs-lookup"><span data-stu-id="467ae-998">When a method has a non-void result type and an expression body, the expression must be implicitly convertible to the result type, and the body is exactly equivalent to a block body of the form `{ return E; }`.</span></span>
    
<span data-ttu-id="467ae-999">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-999">In the example</span></span>
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
<span data-ttu-id="467ae-1000">il metodo che restituisce `F` il valore genera un errore in fase di compilazione perché il controllo può propagarsi alla fine del corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1000">the value-returning `F` method results in a compile-time error because control can flow off the end of the method body.</span></span> <span data-ttu-id="467ae-1001">I `G` metodi `H` e sono corretti perché tutti i possibili percorsi di esecuzione terminano in un'istruzione return che specifica un valore restituito.</span><span class="sxs-lookup"><span data-stu-id="467ae-1001">The `G` and `H` methods are correct because all possible execution paths end in a return statement that specifies a return value.</span></span> <span data-ttu-id="467ae-1002">Il `I` metodo è corretto, perché il corpo è equivalente a un blocco di istruzioni con una sola istruzione return.</span><span class="sxs-lookup"><span data-stu-id="467ae-1002">The `I` method is correct, because its body is equivalent to a statement block with just a single return statement in it.</span></span>

### <a name="method-overloading"></a><span data-ttu-id="467ae-1003">Overload di un metodo</span><span class="sxs-lookup"><span data-stu-id="467ae-1003">Method overloading</span></span>

<span data-ttu-id="467ae-1004">Le regole di risoluzione dell'overload del metodo sono descritte in [inferenza del tipo](expressions.md#type-inference).</span><span class="sxs-lookup"><span data-stu-id="467ae-1004">The method overload resolution rules are described in [Type inference](expressions.md#type-inference).</span></span>

## <a name="properties"></a><span data-ttu-id="467ae-1005">Proprietà</span><span class="sxs-lookup"><span data-stu-id="467ae-1005">Properties</span></span>

<span data-ttu-id="467ae-1006">Una ***Proprietà*** è un membro che fornisce l'accesso a una caratteristica di un oggetto o di una classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1006">A ***property*** is a member that provides access to a characteristic of an object or a class.</span></span> <span data-ttu-id="467ae-1007">Esempi di proprietà includono la lunghezza di una stringa, la dimensione di un tipo di carattere, la didascalia di una finestra, il nome di un cliente e così via.</span><span class="sxs-lookup"><span data-stu-id="467ae-1007">Examples of properties include the length of a string, the size of a font, the caption of a window, the name of a customer, and so on.</span></span> <span data-ttu-id="467ae-1008">Le proprietà sono un'estensione naturale dei campi, entrambi sono membri denominati con tipi associati e la sintassi per l'accesso a campi e proprietà è la stessa.</span><span class="sxs-lookup"><span data-stu-id="467ae-1008">Properties are a natural extension of fields—both are named members with associated types, and the syntax for accessing fields and properties is the same.</span></span> <span data-ttu-id="467ae-1009">A differenza dei campi, tuttavia, le proprietà non denotano posizioni di memoria,</span><span class="sxs-lookup"><span data-stu-id="467ae-1009">However, unlike fields, properties do not denote storage locations.</span></span> <span data-ttu-id="467ae-1010">ma hanno ***funzioni di accesso*** che specificano le istruzioni da eseguire nel momento in cui ne vengono letti o scritti i valori.</span><span class="sxs-lookup"><span data-stu-id="467ae-1010">Instead, properties have ***accessors*** that specify the statements to be executed when their values are read or written.</span></span> <span data-ttu-id="467ae-1011">Le proprietà forniscono pertanto un meccanismo per associare le azioni alla lettura e alla scrittura degli attributi di un oggetto. consentono inoltre di calcolare tali attributi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1011">Properties thus provide a mechanism for associating actions with the reading and writing of an object's attributes; furthermore, they permit such attributes to be computed.</span></span>

<span data-ttu-id="467ae-1012">Le proprietà vengono dichiarate usando *property_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="467ae-1012">Properties are declared using *property_declaration*s:</span></span>

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

<span data-ttu-id="467ae-1013">Un *property_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)), il `new` ([il modificatore new](classes.md#the-new-modifier)), `static` ([static e instance Metodi](classes.md#static-and-instance-methods)), `virtual` ([metodi virtuali](classes.md#virtual-methods)), 0 ([metodi di override](classes.md#override-methods)), 2[(Metodi sealed](classes.md#sealed-methods)), 4 ([metodi astratti](classes.md#abstract-methods)) e i modificatori 6 ([metodi esterni](classes.md#external-methods)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1013">A *property_declaration* may include a set of *attributes* ([Attributes](attributes.md)) and a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), the `new` ([The new modifier](classes.md#the-new-modifier)),  `static` ([Static and instance methods](classes.md#static-and-instance-methods)), `virtual` ([Virtual methods](classes.md#virtual-methods)), `override` ([Override methods](classes.md#override-methods)), `sealed` ([Sealed methods](classes.md#sealed-methods)), `abstract` ([Abstract methods](classes.md#abstract-methods)), and `extern` ([External methods](classes.md#external-methods)) modifiers.</span></span>

<span data-ttu-id="467ae-1014">Le dichiarazioni di proprietà sono soggette alle stesse regole delle dichiarazioni di metodo ([Metodi](classes.md#methods)) per quanto concerne combinazioni valide di modificatori.</span><span class="sxs-lookup"><span data-stu-id="467ae-1014">Property declarations are subject to the same rules as method declarations ([Methods](classes.md#methods)) with regard to valid combinations of modifiers.</span></span>

<span data-ttu-id="467ae-1015">Il *tipo* di una dichiarazione di proprietà specifica il tipo della proprietà introdotta dalla dichiarazione e *MEMBER_NAME* specifica il nome della proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1015">The *type* of a property declaration specifies the type of the property introduced by the declaration, and the *member_name* specifies the name of the property.</span></span> <span data-ttu-id="467ae-1016">A meno che la proprietà non sia un'implementazione esplicita di un membro di interfaccia, *MEMBER_NAME* è semplicemente un *identificatore*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1016">Unless the property is an explicit interface member implementation, the *member_name* is simply an *identifier*.</span></span> <span data-ttu-id="467ae-1017">Per un'implementazione esplicita di un membro di interfaccia ([implementazioni esplicite di membri di interfaccia](interfaces.md#explicit-interface-member-implementations)), *MEMBER_NAME* è costituito da un *interface_type* seguito da un "`.`" e da un *identificatore*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1017">For an explicit interface member implementation ([Explicit interface member implementations](interfaces.md#explicit-interface-member-implementations)), the *member_name* consists of an *interface_type* followed by a "`.`" and an *identifier*.</span></span>

<span data-ttu-id="467ae-1018">Il *tipo* di una proprietà deve essere accessibile almeno quanto la proprietà stessa (vincoli di[accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1018">The *type* of a property must be at least as accessible as the property itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="467ae-1019">Un *property_body* può essere costituito da un ***corpo della funzione di accesso*** o da un corpo di ***espressione***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1019">A *property_body* may either consist of an ***accessor body*** or an ***expression body***.</span></span> <span data-ttu-id="467ae-1020">In un corpo della funzione di accesso, *accessor_declarations*, che deve essere racchiuso tra token "`{`" e "`}`", dichiarare le funzioni di accesso ([funzioni di accesso](classes.md#accessors)) della proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1020">In an accessor body,  *accessor_declarations*, which must be enclosed in "`{`" and "`}`" tokens, declare the accessors ([Accessors](classes.md#accessors)) of the property.</span></span> <span data-ttu-id="467ae-1021">Le funzioni di accesso specificano le istruzioni eseguibili associate alla lettura e alla scrittura della proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1021">The accessors specify the executable statements associated with reading and writing the property.</span></span>

<span data-ttu-id="467ae-1022">Il `=>` corpo di un'espressione costituito da seguito da un' *espressione* `E` e da un punto e virgola è `{ get { return E; } }`esattamente equivalente al corpo dell'istruzione e pertanto può essere utilizzato solo per specificare le proprietà del solo metodo Get in cui il risultato di il getter viene fornito da un'unica espressione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1022">An expression body consisting of `=>` followed by an *expression* `E` and a semicolon is exactly equivalent to the statement body `{ get { return E; } }`, and can therefore only be used to specify getter-only properties where the result of the getter is given by a single expression.</span></span>

<span data-ttu-id="467ae-1023">Un *property_initializer* può essere specificato solo per una proprietà implementata automaticamente ([proprietà implementate automaticamente](classes.md#automatically-implemented-properties)) e causa l'inizializzazione del campo sottostante di tali proprietà con il valore fornito dall' *espressione* .</span><span class="sxs-lookup"><span data-stu-id="467ae-1023">A *property_initializer* may only be given for an automatically implemented property ([Automatically implemented properties](classes.md#automatically-implemented-properties)), and causes the initialization of the underlying field of such properties with the value given by the *expression*.</span></span>

<span data-ttu-id="467ae-1024">Anche se la sintassi per l'accesso a una proprietà è identica a quella di un campo, una proprietà non viene classificata come variabile.</span><span class="sxs-lookup"><span data-stu-id="467ae-1024">Even though the syntax for accessing a property is the same as that for a field, a property is not classified as a variable.</span></span> <span data-ttu-id="467ae-1025">Non è quindi possibile passare una proprietà come `ref` argomento o. `out`</span><span class="sxs-lookup"><span data-stu-id="467ae-1025">Thus, it is not possible to pass a property as a `ref` or `out` argument.</span></span>

<span data-ttu-id="467ae-1026">Quando una dichiarazione di proprietà include `extern` un modificatore, la proprietà viene definita come ***proprietà esterna***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1026">When a property declaration includes an `extern` modifier, the property is said to be an ***external property***.</span></span> <span data-ttu-id="467ae-1027">Poiché una dichiarazione di proprietà esterna non fornisce alcuna implementazione effettiva, ogni *accessor_declarations* è costituito da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="467ae-1027">Because an external property declaration provides no actual implementation, each of its *accessor_declarations* consists of a semicolon.</span></span>

### <a name="static-and-instance-properties"></a><span data-ttu-id="467ae-1028">Proprietà statiche e di istanza</span><span class="sxs-lookup"><span data-stu-id="467ae-1028">Static and instance properties</span></span>

<span data-ttu-id="467ae-1029">Quando una dichiarazione di proprietà include `static` un modificatore, la proprietà viene definita come ***proprietà statica***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1029">When a property declaration includes a `static` modifier, the property is said to be a ***static property***.</span></span> <span data-ttu-id="467ae-1030">Quando non `static` è presente alcun modificatore, la proprietà viene definita come una ***proprietà dell'istanza***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1030">When no `static` modifier is present, the property is said to be an ***instance property***.</span></span>

<span data-ttu-id="467ae-1031">Una proprietà statica non è associata a un'istanza specifica e si tratta di un errore `this` in fase di compilazione a cui fare riferimento nelle funzioni di accesso di una proprietà statica.</span><span class="sxs-lookup"><span data-stu-id="467ae-1031">A static property is not associated with a specific instance, and it is a compile-time error to refer to `this` in the accessors of a static property.</span></span>

<span data-ttu-id="467ae-1032">Una proprietà dell'istanza è associata a una determinata istanza di una classe e a tale istanza è possibile accedere `this` come ([questo accesso](expressions.md#this-access)) nelle funzioni di accesso di tale proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1032">An instance property is associated with a given instance of a class, and that instance can be accessed as `this` ([This access](expressions.md#this-access)) in the accessors of that property.</span></span>

<span data-ttu-id="467ae-1033">Quando si fa riferimento a una proprietà in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, se `M` è una proprietà statica, `E` deve indicare un tipo che contiene `M` e se `M` è una proprietà dell'istanza, e deve indicare un'istanza di un tipo contenente `M`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1033">When a property is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, if `M` is a static property, `E` must denote a type containing `M`, and if `M` is an instance property, E must denote an instance of a type containing `M`.</span></span>

<span data-ttu-id="467ae-1034">Le differenze tra i membri statici e i membri di istanza sono illustrate ulteriormente nei [membri statici e di istanza](classes.md#static-and-instance-members).</span><span class="sxs-lookup"><span data-stu-id="467ae-1034">The differences between static and instance members are discussed further in [Static and instance members](classes.md#static-and-instance-members).</span></span>

### <a name="accessors"></a><span data-ttu-id="467ae-1035">Funzioni</span><span class="sxs-lookup"><span data-stu-id="467ae-1035">Accessors</span></span>

<span data-ttu-id="467ae-1036">*Accessor_declarations* di una proprietà specificano le istruzioni eseguibili associate alla lettura e alla scrittura della proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1036">The *accessor_declarations* of a property specify the executable statements associated with reading and writing that property.</span></span>

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

<span data-ttu-id="467ae-1037">Le dichiarazioni delle funzioni di accesso sono costituite da un *get_accessor_declaration*, un *set_accessor_declaration*o entrambi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1037">The accessor declarations consist of a *get_accessor_declaration*, a *set_accessor_declaration*, or both.</span></span> <span data-ttu-id="467ae-1038">Ogni dichiarazione di funzione di accesso è costituita dal token `get` o `set` seguito da un *accessor_modifier* facoltativo e da un *accessor_body*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1038">Each accessor declaration consists of the token `get` or `set` followed by an optional *accessor_modifier* and an *accessor_body*.</span></span>

<span data-ttu-id="467ae-1039">L'uso di *accessor_modifier*s è regolato dalle restrizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-1039">The use of *accessor_modifier*s is governed by the following restrictions:</span></span>

*  <span data-ttu-id="467ae-1040">Non è possibile usare *accessor_modifier* in un'interfaccia o in un'implementazione esplicita di un membro di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="467ae-1040">An *accessor_modifier* may not be used in an interface or in an explicit interface member implementation.</span></span>
*  <span data-ttu-id="467ae-1041">Per una proprietà o un indicizzatore senza modificatore `override`, un *accessor_modifier* è consentito solo se la proprietà o l'indicizzatore ha entrambe le funzioni di accesso `get` e `set`, quindi è consentito solo in una di queste funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1041">For a property or indexer that has no `override` modifier, an *accessor_modifier* is permitted only if the property or indexer has both a `get` and `set` accessor, and then is permitted only on one of those accessors.</span></span>
*  <span data-ttu-id="467ae-1042">Per una proprietà o un indicizzatore che include un modificatore `override`, una funzione di accesso deve corrispondere al *accessor_modifier*, se presente, della funzione di accesso sottoposta a override.</span><span class="sxs-lookup"><span data-stu-id="467ae-1042">For a property or indexer that includes an `override` modifier, an accessor must match the *accessor_modifier*, if any, of the accessor being overridden.</span></span>
*  <span data-ttu-id="467ae-1043">*Accessor_modifier* deve dichiarare un'accessibilità strettamente più restrittiva dell'accessibilità dichiarata della proprietà o dell'indicizzatore stesso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1043">The *accessor_modifier* must declare an accessibility that is strictly more restrictive than the declared accessibility of the property or indexer itself.</span></span> <span data-ttu-id="467ae-1044">Per essere precisi:</span><span class="sxs-lookup"><span data-stu-id="467ae-1044">To be precise:</span></span>
   * <span data-ttu-id="467ae-1045">Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata di `public`, il valore di *accessor_modifier* può essere `protected internal`, `internal`, `protected` o `private`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1045">If the property or indexer has a declared accessibility of `public`, the *accessor_modifier* may be either `protected internal`, `internal`, `protected`, or `private`.</span></span>
   * <span data-ttu-id="467ae-1046">Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata di `protected internal`, il valore di *accessor_modifier* può essere `internal`, `protected` o `private`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1046">If the property or indexer has a declared accessibility of `protected internal`, the *accessor_modifier* may be either `internal`, `protected`, or `private`.</span></span>
   * <span data-ttu-id="467ae-1047">Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata di `internal` o `protected`, il *accessor_modifier* deve essere `private`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1047">If the property or indexer has a declared accessibility of `internal` or `protected`, the *accessor_modifier* must be `private`.</span></span>
   * <span data-ttu-id="467ae-1048">Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata di `private`, non è possibile usare *accessor_modifier* .</span><span class="sxs-lookup"><span data-stu-id="467ae-1048">If the property or indexer has a declared accessibility of `private`, no *accessor_modifier* may be used.</span></span>

<span data-ttu-id="467ae-1049">Per le proprietà `abstract` e `extern`, il *accessor_body* per ogni funzione di accesso specificata è semplicemente un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="467ae-1049">For `abstract` and `extern` properties, the *accessor_body* for each accessor specified is simply a semicolon.</span></span> <span data-ttu-id="467ae-1050">Una proprietà non astratta e non extern può includere ogni *accessor_body* un punto e virgola, nel qual caso si tratta di una ***proprietà implementata automaticamente*** ([proprietà implementate automaticamente](classes.md#automatically-implemented-properties)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1050">A non-abstract, non-extern property may have each *accessor_body* be a semicolon, in which case it is an ***automatically implemented property*** ([Automatically implemented properties](classes.md#automatically-implemented-properties)).</span></span> <span data-ttu-id="467ae-1051">Una proprietà implementata automaticamente deve avere almeno una funzione di accesso get.</span><span class="sxs-lookup"><span data-stu-id="467ae-1051">An automatically implemented property must have at least a get accessor.</span></span> <span data-ttu-id="467ae-1052">Per le funzioni di accesso di qualsiasi altra proprietà non astratta e non extern, *accessor_body* è un *blocco* che specifica le istruzioni da eseguire quando viene richiamata la funzione di accesso corrispondente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1052">For the accessors of any other non-abstract, non-extern property, the *accessor_body* is a *block* which specifies the statements to be executed when the corresponding accessor is invoked.</span></span>

<span data-ttu-id="467ae-1053">Una `get` funzione di accesso corrisponde a un metodo senza parametri con un valore restituito del tipo di proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1053">A `get` accessor corresponds to a parameterless method with a return value of the property type.</span></span> <span data-ttu-id="467ae-1054">Fatta eccezione per la destinazione di un'assegnazione, quando si fa riferimento a una proprietà in un'espressione `get` , viene richiamata la funzione di accesso della proprietà per calcolare il valore della proprietà ([valori di espressioni](expressions.md#values-of-expressions)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1054">Except as the target of an assignment, when a property is referenced in an expression, the `get` accessor of the property is invoked to compute the value of the property ([Values of expressions](expressions.md#values-of-expressions)).</span></span> <span data-ttu-id="467ae-1055">Il corpo di una `get` funzione di accesso deve essere conforme alle regole per i metodi di restituzione di valori descritti nel [corpo del metodo](classes.md#method-body).</span><span class="sxs-lookup"><span data-stu-id="467ae-1055">The body of a `get` accessor must conform to the rules for value-returning methods described in [Method body](classes.md#method-body).</span></span> <span data-ttu-id="467ae-1056">In particolare, tutte `return` le istruzioni nel corpo di una `get` funzione di accesso devono specificare un'espressione convertibile in modo implicito nel tipo della proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1056">In particular, all `return` statements in the body of a `get` accessor must specify an expression that is implicitly convertible to the property type.</span></span> <span data-ttu-id="467ae-1057">Inoltre, l'endpoint di una `get` funzione di accesso non deve essere raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="467ae-1057">Furthermore, the endpoint of a `get` accessor must not be reachable.</span></span>

<span data-ttu-id="467ae-1058">Una `set` funzione di accesso corrisponde a un metodo con un singolo parametro di valore del tipo di `void` proprietà e un tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="467ae-1058">A `set` accessor corresponds to a method with a single value parameter of the property type and a `void` return type.</span></span> <span data-ttu-id="467ae-1059">Il parametro implicito di `set` una funzione di accesso `value`è sempre denominato.</span><span class="sxs-lookup"><span data-stu-id="467ae-1059">The implicit parameter of a `set` accessor is always named `value`.</span></span> <span data-ttu-id="467ae-1060">Quando si fa riferimento a una proprietà come destinazione di un'assegnazione ([operatori di assegnazione](expressions.md#assignment-operators)) o come operando di `++` o `--` ([operatori di incremento e decremento suffisso](expressions.md#postfix-increment-and-decrement-operators), [operatori di incremento e decremento del prefisso](expressions.md#prefix-increment-and-decrement-operators)), il la funzione di accesso viene richiamata con un argomento (il cui valore è quello del lato destro dell'assegnazione o l'operando `++` dell' `--` operatore OR) che fornisce il nuovo valore ([assegnazione semplice](expressions.md#simple-assignment)). `set`</span><span class="sxs-lookup"><span data-stu-id="467ae-1060">When a property is referenced as the target of an assignment ([Assignment operators](expressions.md#assignment-operators)), or as the operand of `++` or `--` ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators), [Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)), the `set` accessor is invoked with an argument (whose value is that of the right-hand side of the assignment or the operand of the `++` or `--` operator) that provides the new value ([Simple assignment](expressions.md#simple-assignment)).</span></span> <span data-ttu-id="467ae-1061">Il corpo di una `set` funzione di accesso deve essere conforme `void` alle regole per i metodi descritti nel [corpo del metodo](classes.md#method-body).</span><span class="sxs-lookup"><span data-stu-id="467ae-1061">The body of a `set` accessor must conform to the rules for `void` methods described in [Method body](classes.md#method-body).</span></span> <span data-ttu-id="467ae-1062">In particolare, `return` le istruzioni nel `set` corpo della funzione di accesso non sono autorizzate a specificare un'espressione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1062">In particular, `return` statements in the `set` accessor body are not permitted to specify an expression.</span></span> <span data-ttu-id="467ae-1063">Poiché una `set` funzione di accesso include implicitamente un `value`parametro denominato, si tratta di un errore in fase di compilazione per una variabile locale o `set` una dichiarazione di costante in una funzione di accesso con tale nome.</span><span class="sxs-lookup"><span data-stu-id="467ae-1063">Since a `set` accessor implicitly has a parameter named `value`, it is a compile-time error for a local variable or constant declaration in a `set` accessor to have that name.</span></span>

<span data-ttu-id="467ae-1064">In base alla presenza o all'assenza delle `get` funzioni `set` di accesso e, una proprietà viene classificata come segue:</span><span class="sxs-lookup"><span data-stu-id="467ae-1064">Based on the presence or absence of the `get` and `set` accessors, a property is classified as follows:</span></span>

*  <span data-ttu-id="467ae-1065">Una proprietà che include sia `get` una funzione di accesso `set` sia una funzione di accesso è detta proprietà di ***lettura/scrittura*** .</span><span class="sxs-lookup"><span data-stu-id="467ae-1065">A property that includes both a `get` accessor and a `set` accessor is said to be a ***read-write*** property.</span></span>
*  <span data-ttu-id="467ae-1066">Una proprietà che ha solo una `get` funzione di accesso è detta proprietà di ***sola lettura*** .</span><span class="sxs-lookup"><span data-stu-id="467ae-1066">A property that has only a `get` accessor is said to be a ***read-only*** property.</span></span> <span data-ttu-id="467ae-1067">Si tratta di un errore in fase di compilazione perché una proprietà di sola lettura sia la destinazione di un'assegnazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1067">It is a compile-time error for a read-only property to be the target of an assignment.</span></span>
*  <span data-ttu-id="467ae-1068">Una proprietà che ha solo una `set` funzione di accesso è detta proprietà di ***sola scrittura*** .</span><span class="sxs-lookup"><span data-stu-id="467ae-1068">A property that has only a `set` accessor is said to be a ***write-only*** property.</span></span> <span data-ttu-id="467ae-1069">Fatta eccezione per la destinazione di un'assegnazione, si tratta di un errore in fase di compilazione per fare riferimento a una proprietà di sola scrittura in un'espressione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1069">Except as the target of an assignment, it is a compile-time error to reference a write-only property in an expression.</span></span>

<span data-ttu-id="467ae-1070">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1070">In the example</span></span>
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
<span data-ttu-id="467ae-1071">il `Button` controllo dichiara una proprietà pubblica `Caption` .</span><span class="sxs-lookup"><span data-stu-id="467ae-1071">the `Button` control declares a public `Caption` property.</span></span> <span data-ttu-id="467ae-1072">La `get` `caption` funzione`Caption` di accesso della proprietà restituisce la stringa archiviata nel campo privato.</span><span class="sxs-lookup"><span data-stu-id="467ae-1072">The `get` accessor of the `Caption` property returns the string stored in the private `caption` field.</span></span> <span data-ttu-id="467ae-1073">La `set` funzione di accesso controlla se il nuovo valore è diverso dal valore corrente e, in tal caso, archivia il nuovo valore e ridisegna il controllo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1073">The `set` accessor checks if the new value is different from the current value, and if so, it stores the new value and repaints the control.</span></span> <span data-ttu-id="467ae-1074">Le proprietà seguono spesso il modello illustrato in precedenza: La `get` funzione di accesso restituisce semplicemente un valore archiviato in un campo privato e `set` la funzione di accesso modifica tale campo privato e quindi esegue le azioni aggiuntive necessarie per aggiornare completamente lo stato dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="467ae-1074">Properties often follow the pattern shown above: The `get` accessor simply returns a value stored in a private field, and the `set` accessor modifies that private field and then performs any additional actions required to fully update the state of the object.</span></span>

<span data-ttu-id="467ae-1075">Data la `Button` classe precedente, di seguito è riportato un esempio di utilizzo `Caption` della proprietà:</span><span class="sxs-lookup"><span data-stu-id="467ae-1075">Given the `Button` class above, the following is an example of use of the `Caption` property:</span></span>
```csharp
Button okButton = new Button();
okButton.Caption = "OK";            // Invokes set accessor
string s = okButton.Caption;        // Invokes get accessor
```

<span data-ttu-id="467ae-1076">In questo caso `set` , la funzione di accesso viene richiamata assegnando un valore alla proprietà `get` e la funzione di accesso viene richiamata facendo riferimento alla proprietà in un'espressione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1076">Here, the `set` accessor is invoked by assigning a value to the property, and the `get` accessor is invoked by referencing the property in an expression.</span></span>

<span data-ttu-id="467ae-1077">Le `get` funzioni `set` di accesso e di una proprietà non sono membri distinti e non è possibile dichiarare separatamente le funzioni di accesso di una proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1077">The `get` and `set` accessors of a property are not distinct members, and it is not possible to declare the accessors of a property separately.</span></span> <span data-ttu-id="467ae-1078">Di conseguenza, non è possibile per le due funzioni di accesso di una proprietà di lettura/scrittura avere un'accessibilità diversa.</span><span class="sxs-lookup"><span data-stu-id="467ae-1078">As such, it is not possible for the two accessors of a read-write property to have different accessibility.</span></span> <span data-ttu-id="467ae-1079">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1079">The example</span></span>
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
<span data-ttu-id="467ae-1080">non dichiara una singola proprietà di lettura/scrittura.</span><span class="sxs-lookup"><span data-stu-id="467ae-1080">does not declare a single read-write property.</span></span> <span data-ttu-id="467ae-1081">Dichiara invece due proprietà con lo stesso nome, una sola lettura e una sola scrittura.</span><span class="sxs-lookup"><span data-stu-id="467ae-1081">Rather, it declares two properties with the same name, one read-only and one write-only.</span></span> <span data-ttu-id="467ae-1082">Poiché due membri dichiarati nella stessa classe non possono avere lo stesso nome, nell'esempio viene generato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1082">Since two members declared in the same class cannot have the same name, the example causes a compile-time error to occur.</span></span>

<span data-ttu-id="467ae-1083">Quando una classe derivata dichiara una proprietà con lo stesso nome di una proprietà ereditata, la proprietà derivata nasconde la proprietà ereditata in relazione alla lettura e alla scrittura.</span><span class="sxs-lookup"><span data-stu-id="467ae-1083">When a derived class declares a property by the same name as an inherited property, the derived property hides the inherited property with respect to both reading and writing.</span></span> <span data-ttu-id="467ae-1084">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1084">In the example</span></span>
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
<span data-ttu-id="467ae-1085">la `P` proprietà in `B` nasconde la `P` proprietà in `A` rispetto alla lettura e alla scrittura.</span><span class="sxs-lookup"><span data-stu-id="467ae-1085">the `P` property in `B` hides the `P` property in `A` with respect to both reading and writing.</span></span> <span data-ttu-id="467ae-1086">Quindi, nelle istruzioni</span><span class="sxs-lookup"><span data-stu-id="467ae-1086">Thus, in the statements</span></span>
```csharp
B b = new B();
b.P = 1;          // Error, B.P is read-only
((A)b).P = 1;     // Ok, reference to A.P
```
<span data-ttu-id="467ae-1087">l'assegnazione a `b.P` causa la segnalazione di un errore in fase di compilazione, poiché la `P` proprietà di sola lettura `B` in nasconde la proprietà di sola `P` scrittura in `A`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1087">the assignment to `b.P` causes a compile-time error to be reported, since the read-only `P` property in `B` hides the write-only `P` property in `A`.</span></span> <span data-ttu-id="467ae-1088">Si noti, tuttavia, che è possibile usare un cast per accedere alla `P` proprietà Hidden.</span><span class="sxs-lookup"><span data-stu-id="467ae-1088">Note, however, that a cast can be used to access the hidden `P` property.</span></span>

<span data-ttu-id="467ae-1089">A differenza dei campi pubblici, le proprietà forniscono una separazione tra lo stato interno di un oggetto e la relativa interfaccia pubblica.</span><span class="sxs-lookup"><span data-stu-id="467ae-1089">Unlike public fields, properties provide a separation between an object's internal state and its public interface.</span></span> <span data-ttu-id="467ae-1090">Si consideri l'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="467ae-1090">Consider the example:</span></span>
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

<span data-ttu-id="467ae-1091">Qui, la `Label` classe usa due `int` campi, `x` e `y`, per archiviare la relativa posizione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1091">Here, the `Label` class uses two `int` fields, `x` and `y`, to store its location.</span></span> <span data-ttu-id="467ae-1092">Il percorso è esposto pubblicamente sia come `X` `Y` proprietà che come proprietà e come `Location` proprietà di tipo `Point`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1092">The location is publicly exposed both as an `X` and a `Y` property and as a `Location` property of type `Point`.</span></span> <span data-ttu-id="467ae-1093">Se, in una versione futura di `Label`, diventa più comodo archiviare la posizione `Point` come internamente, è possibile apportare la modifica senza influire sull'interfaccia pubblica della classe:</span><span class="sxs-lookup"><span data-stu-id="467ae-1093">If, in a future version of `Label`, it becomes more convenient to store the location as a `Point` internally, the change can be made without affecting the public interface of the class:</span></span>
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

<span data-ttu-id="467ae-1094">Aveva `x` invece campi,`Label` sarebbe stato impossibile apportare tale modifica alla classe. `public readonly` `y`</span><span class="sxs-lookup"><span data-stu-id="467ae-1094">Had `x` and `y` instead been `public readonly` fields, it would have been impossible to make such a change to the `Label` class.</span></span>

<span data-ttu-id="467ae-1095">L'esposizione dello stato attraverso le proprietà non è necessariamente meno efficiente rispetto all'esposizione diretta dei campi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1095">Exposing state through properties is not necessarily any less efficient than exposing fields directly.</span></span> <span data-ttu-id="467ae-1096">In particolare, quando una proprietà non è virtuale e contiene solo una piccola quantità di codice, l'ambiente di esecuzione può sostituire le chiamate alle funzioni di accesso con il codice effettivo delle funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1096">In particular, when a property is non-virtual and contains only a small amount of code, the execution environment may replace calls to accessors with the actual code of the accessors.</span></span> <span data-ttu-id="467ae-1097">Questo processo è noto come ***incorporamento***e rende l'accesso alle proprietà efficiente come l'accesso al campo, ma conserva la maggiore flessibilità delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1097">This process is known as ***inlining***, and it makes property access as efficient as field access, yet preserves the increased flexibility of properties.</span></span>

<span data-ttu-id="467ae-1098">Poiché la chiamata di `get` una funzione di accesso è concettualmente equivalente alla lettura del valore di un campo, viene considerato uno stile `get` di programmazione non valido per le funzioni di accesso che hanno effetti collaterali osservabili.</span><span class="sxs-lookup"><span data-stu-id="467ae-1098">Since invoking a `get` accessor is conceptually equivalent to reading the value of a field, it is considered bad programming style for `get` accessors to have observable side-effects.</span></span> <span data-ttu-id="467ae-1099">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1099">In the example</span></span>
```csharp
class Counter
{
    private int next;

    public int Next {
        get { return next++; }
    }
}
```
<span data-ttu-id="467ae-1100">il valore della `Next` proprietà dipende dal numero di volte in cui è stato eseguito l'accesso alla proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1100">the value of the `Next` property depends on the number of times the property has previously been accessed.</span></span> <span data-ttu-id="467ae-1101">Pertanto, l'accesso alla proprietà produce un effetto collaterale osservabile e la proprietà deve essere implementata come metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1101">Thus, accessing the property produces an observable side-effect, and the property should be implemented as a method instead.</span></span>

<span data-ttu-id="467ae-1102">La convenzione "nessun effetto collaterale" per `get` le funzioni di accesso non `get` significa che le funzioni di accesso devono essere sempre scritte in modo da restituire semplicemente i valori archiviati nei campi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1102">The "no side-effects" convention for `get` accessors doesn't mean that `get` accessors should always be written to simply return values stored in fields.</span></span> <span data-ttu-id="467ae-1103">`get` Le funzioni di accesso spesso calcolano il valore di una proprietà tramite l'accesso a più campi o la chiamata di metodi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1103">Indeed, `get` accessors often compute the value of a property by accessing multiple fields or invoking methods.</span></span> <span data-ttu-id="467ae-1104">Tuttavia, una funzione di `get` accesso progettata correttamente non esegue alcuna azione che causi modifiche osservabili nello stato dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="467ae-1104">However, a properly designed `get` accessor performs no actions that cause observable changes in the state of the object.</span></span>

<span data-ttu-id="467ae-1105">Le proprietà possono essere usate per ritardare l'inizializzazione di una risorsa fino al momento in cui viene fatto riferimento per la prima volta.</span><span class="sxs-lookup"><span data-stu-id="467ae-1105">Properties can be used to delay initialization of a resource until the moment it is first referenced.</span></span> <span data-ttu-id="467ae-1106">Esempio:</span><span class="sxs-lookup"><span data-stu-id="467ae-1106">For example:</span></span>
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

<span data-ttu-id="467ae-1107">La `Console` classe contiene tre proprietà `Out`, `In`, e `Error`, che rappresentano rispettivamente i dispositivi di input, di output e di errore standard.</span><span class="sxs-lookup"><span data-stu-id="467ae-1107">The `Console` class contains three properties, `In`, `Out`, and `Error`, that represent the standard input, output, and error devices, respectively.</span></span> <span data-ttu-id="467ae-1108">Esponendo tali membri come proprietà, è `Console` possibile che la classe ritardi l'inizializzazione fino a quando non vengono effettivamente utilizzati.</span><span class="sxs-lookup"><span data-stu-id="467ae-1108">By exposing these members as properties, the `Console` class can delay their initialization until they are actually used.</span></span> <span data-ttu-id="467ae-1109">Ad esempio, al primo riferimento alla `Out` proprietà, come in</span><span class="sxs-lookup"><span data-stu-id="467ae-1109">For example, upon first referencing the `Out` property, as in</span></span>
```csharp
Console.Out.WriteLine("hello, world");
```
<span data-ttu-id="467ae-1110">viene creato `TextWriter` il sottostante per il dispositivo di output.</span><span class="sxs-lookup"><span data-stu-id="467ae-1110">the underlying `TextWriter` for the output device is created.</span></span> <span data-ttu-id="467ae-1111">Tuttavia, se l'applicazione non fa riferimento alle `In` proprietà `Error` e, non viene creato alcun oggetto per tali dispositivi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1111">But if the application makes no reference to the `In` and `Error` properties, then no objects are created for those devices.</span></span>

### <a name="automatically-implemented-properties"></a><span data-ttu-id="467ae-1112">Proprietà implementate automaticamente</span><span class="sxs-lookup"><span data-stu-id="467ae-1112">Automatically implemented properties</span></span>

<span data-ttu-id="467ae-1113">Una proprietà implementata automaticamente (o una ***proprietà automatica*** per Short), è una proprietà non-extern non astratta con corpi delle funzioni di accesso solo punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="467ae-1113">An automatically implemented property (or ***auto-property*** for short), is a non-abstract non-extern property with semicolon-only accessor bodies.</span></span> <span data-ttu-id="467ae-1114">Le proprietà automatiche devono avere una funzione di accesso get e possono facoltativamente avere una funzione di accesso set.</span><span class="sxs-lookup"><span data-stu-id="467ae-1114">Auto-properties must have a get accessor and can optionally have a set accessor.</span></span>

<span data-ttu-id="467ae-1115">Quando una proprietà viene specificata come proprietà implementata automaticamente, un campo sottostante nascosto è automaticamente disponibile per la proprietà e le funzioni di accesso sono implementate per la lettura e la scrittura in quel campo sottostante.</span><span class="sxs-lookup"><span data-stu-id="467ae-1115">When a property is specified as an automatically implemented property, a hidden backing field is automatically available for the property, and the accessors are implemented to read from and write to that backing field.</span></span> <span data-ttu-id="467ae-1116">Se la proprietà auto non dispone di una funzione di accesso set, il campo sottostante `readonly` viene considerato (campi di sola[lettura](classes.md#readonly-fields)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1116">If the auto-property has no set accessor, the backing field is considered `readonly` ([Readonly fields](classes.md#readonly-fields)).</span></span> <span data-ttu-id="467ae-1117">Analogamente a `readonly` un campo, è anche possibile assegnare una proprietà auto solo Get a nel corpo di un costruttore della classe contenitore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1117">Just like a `readonly` field, a getter-only auto-property can also be assigned to in the body of a constructor of the enclosing class.</span></span> <span data-ttu-id="467ae-1118">Tale assegnazione viene assegnata direttamente al campo di supporto ReadOnly della proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1118">Such an assignment assigns directly to the readonly backing field of the property.</span></span>

<span data-ttu-id="467ae-1119">Una proprietà automatica può facoltativamente avere un *property_initializer*, che viene applicato direttamente al campo sottostante come *variable_initializer* ([inizializzatori di variabile](classes.md#variable-initializers)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1119">An auto-property may optionally have a *property_initializer*, which is applied directly to the backing field as a *variable_initializer* ([Variable initializers](classes.md#variable-initializers)).</span></span>

<span data-ttu-id="467ae-1120">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="467ae-1120">The following example:</span></span>
```csharp
public class Point {
    public int X { get; set; } = 0;
    public int Y { get; set; } = 0;
}
```
<span data-ttu-id="467ae-1121">equivale alla dichiarazione seguente:</span><span class="sxs-lookup"><span data-stu-id="467ae-1121">is equivalent to the following declaration:</span></span>
```csharp
public class Point {
    private int __x = 0;
    private int __y = 0;
    public int X { get { return __x; } set { __x = value; } }
    public int Y { get { return __y; } set { __y = value; } }
}
```

<span data-ttu-id="467ae-1122">L'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="467ae-1122">The following example:</span></span>
```csharp
public class ReadOnlyPoint
{
    public int X { get; }
    public int Y { get; }
    public ReadOnlyPoint(int x, int y) { X = x; Y = y; }
}
```
<span data-ttu-id="467ae-1123">equivale alla dichiarazione seguente:</span><span class="sxs-lookup"><span data-stu-id="467ae-1123">is equivalent to the following declaration:</span></span>
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

<span data-ttu-id="467ae-1124">Si noti che le assegnazioni al campo ReadOnly sono valide, perché si verificano all'interno del costruttore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1124">Notice that the assignments to the readonly field are legal, because they occur within the constructor.</span></span>


### <a name="accessibility"></a><span data-ttu-id="467ae-1125">Accessibilità</span><span class="sxs-lookup"><span data-stu-id="467ae-1125">Accessibility</span></span>

<span data-ttu-id="467ae-1126">Se una funzione di accesso dispone di un *accessor_modifier*, il dominio di accessibilità ([domini di accessibilità](basic-concepts.md#accessibility-domains)) della funzione di accesso viene determinato mediante l'accessibilità dichiarata di *accessor_modifier*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1126">If an accessor has an *accessor_modifier*, the accessibility domain ([Accessibility domains](basic-concepts.md#accessibility-domains)) of the accessor is determined using the declared accessibility of the *accessor_modifier*.</span></span> <span data-ttu-id="467ae-1127">Se una funzione di accesso non dispone di un *accessor_modifier*, il dominio di accessibilità della funzione di accesso è determinato dall'accessibilità dichiarata della proprietà o dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1127">If an accessor does not have an *accessor_modifier*, the accessibility domain of the accessor is determined from the declared accessibility of the property or indexer.</span></span>

<span data-ttu-id="467ae-1128">La presenza di un *accessor_modifier* non influiscono mai sulla ricerca dei membri ([operatori](expressions.md#operators)) o sulla risoluzione dell'overload ([risoluzione dell'overload](expressions.md#overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1128">The presence of an *accessor_modifier* never affects member lookup ([Operators](expressions.md#operators)) or overload resolution ([Overload resolution](expressions.md#overload-resolution)).</span></span> <span data-ttu-id="467ae-1129">I modificatori della proprietà o dell'indicizzatore determinano sempre la proprietà o l'indicizzatore a cui è associato, indipendentemente dal contesto dell'accesso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1129">The modifiers on the property or indexer always determine which property or indexer is bound to, regardless of the context of the access.</span></span>

<span data-ttu-id="467ae-1130">Una volta selezionata una proprietà o un indicizzatore specifico, vengono utilizzati i domini di accessibilità delle funzioni di accesso specifiche per determinare se l'utilizzo è valido:</span><span class="sxs-lookup"><span data-stu-id="467ae-1130">Once a particular property or indexer has been selected, the accessibility domains of the specific accessors involved are used to determine if that usage is valid:</span></span>

*  <span data-ttu-id="467ae-1131">Se l'utilizzo è come valore ([valori di espressioni](expressions.md#values-of-expressions)), la `get` funzione di accesso deve esistere ed essere accessibile.</span><span class="sxs-lookup"><span data-stu-id="467ae-1131">If the usage is as a value ([Values of expressions](expressions.md#values-of-expressions)), the `get` accessor must exist and be accessible.</span></span>
*  <span data-ttu-id="467ae-1132">Se l'utilizzo è come destinazione di un'assegnazione semplice ([assegnazione semplice](expressions.md#simple-assignment)), la `set` funzione di accesso deve esistere ed essere accessibile.</span><span class="sxs-lookup"><span data-stu-id="467ae-1132">If the usage is as the target of a simple assignment ([Simple assignment](expressions.md#simple-assignment)), the `set` accessor must exist and be accessible.</span></span>
*  <span data-ttu-id="467ae-1133">Se l'utilizzo è come destinazione dell'assegnazione composta ([assegnazione composta](expressions.md#compound-assignment)) o come destinazione degli operatori `++` or `--` (,9[membri di funzione](expressions.md#function-members), espressioni di [chiamata](expressions.md#invocation-expressions)), entrambe le `get` funzioni di accesso e la `set` funzione di accesso deve esistere ed essere accessibile.</span><span class="sxs-lookup"><span data-stu-id="467ae-1133">If the usage is as the target of compound assignment ([Compound assignment](expressions.md#compound-assignment)), or as the target of the `++` or `--` operators ([Function members](expressions.md#function-members).9, [Invocation expressions](expressions.md#invocation-expressions)), both the `get` accessors and the `set` accessor must exist and be accessible.</span></span>

<span data-ttu-id="467ae-1134">Nell'esempio seguente la proprietà `A.Text` è nascosta dalla proprietà `B.Text`, anche in contesti in cui viene chiamata solo la `set` funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1134">In the following example, the property `A.Text` is hidden by the property `B.Text`, even in contexts where only the `set` accessor is called.</span></span> <span data-ttu-id="467ae-1135">Al contrario, la proprietà `B.Count` non è accessibile alla classe `M`, pertanto viene invece utilizzata `A.Count` la proprietà accessibile.</span><span class="sxs-lookup"><span data-stu-id="467ae-1135">In contrast, the property `B.Count` is not accessible to class `M`, so the accessible property `A.Count` is used instead.</span></span>

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

<span data-ttu-id="467ae-1136">Una funzione di accesso usata per implementare un'interfaccia potrebbe non avere un *accessor_modifier*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1136">An accessor that is used to implement an interface may not have an *accessor_modifier*.</span></span> <span data-ttu-id="467ae-1137">Se per implementare un'interfaccia viene usata una sola funzione di accesso, l'altra funzione di accesso può essere dichiarata con un *accessor_modifier*:</span><span class="sxs-lookup"><span data-stu-id="467ae-1137">If only one accessor is used to implement an interface, the other accessor may be declared with an *accessor_modifier*:</span></span>
```csharp
public interface I
{
    string Prop { get; }
}

public class C: I
{
    public string Prop {
        get { return "April"; }       // Must not have a modifier here
        internal set {...}            // Ok, because I.Prop has no set accessor
    }
}
```

### <a name="virtual-sealed-override-and-abstract-property-accessors"></a><span data-ttu-id="467ae-1138">Funzioni di accesso alle proprietà virtuali, sealed, override e astratte</span><span class="sxs-lookup"><span data-stu-id="467ae-1138">Virtual, sealed, override, and abstract property accessors</span></span>

<span data-ttu-id="467ae-1139">Una `virtual` dichiarazione di proprietà specifica che le funzioni di accesso della proprietà sono virtuali.</span><span class="sxs-lookup"><span data-stu-id="467ae-1139">A `virtual` property declaration specifies that the accessors of the property are virtual.</span></span> <span data-ttu-id="467ae-1140">Il `virtual` modificatore si applica a entrambe le funzioni di accesso di una proprietà di lettura/scrittura. non è possibile che una sola funzione di accesso di una proprietà di lettura/scrittura sia virtuale.</span><span class="sxs-lookup"><span data-stu-id="467ae-1140">The `virtual` modifier applies to both accessors of a read-write property—it is not possible for only one accessor of a read-write property to be virtual.</span></span>

<span data-ttu-id="467ae-1141">Una `abstract` dichiarazione di proprietà specifica che le funzioni di accesso della proprietà sono virtuali, ma non fornisce un'implementazione effettiva delle funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1141">An `abstract` property declaration specifies that the accessors of the property are virtual, but does not provide an actual implementation of the accessors.</span></span> <span data-ttu-id="467ae-1142">Al contrario, le classi derivate non astratte sono necessarie per fornire la propria implementazione per le funzioni di accesso eseguendo l'override della proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1142">Instead, non-abstract derived classes are required to provide their own implementation for the accessors by overriding the property.</span></span> <span data-ttu-id="467ae-1143">Poiché una funzione di accesso per una dichiarazione di proprietà astratta non fornisce alcuna implementazione effettiva, il relativo *accessor_body* è semplicemente costituito da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="467ae-1143">Because an accessor for an abstract property declaration provides no actual implementation, its *accessor_body* simply consists of a semicolon.</span></span>

<span data-ttu-id="467ae-1144">Una dichiarazione di proprietà che include entrambi `abstract` i `override` modificatori e specifica che la proprietà è astratta ed esegue l'override di una proprietà di base.</span><span class="sxs-lookup"><span data-stu-id="467ae-1144">A property declaration that includes both the `abstract` and `override` modifiers specifies that the property is abstract and overrides a base property.</span></span> <span data-ttu-id="467ae-1145">Anche le funzioni di accesso di tale proprietà sono astratte.</span><span class="sxs-lookup"><span data-stu-id="467ae-1145">The accessors of such a property are also abstract.</span></span>

<span data-ttu-id="467ae-1146">Le dichiarazioni di proprietà astratte sono consentite solo nelle classi astratte ([classi astratte](classes.md#abstract-classes)). È possibile eseguire l'override delle funzioni di accesso di una proprietà virtuale ereditata in una classe derivata includendo una dichiarazione di `override` proprietà che specifica una direttiva.</span><span class="sxs-lookup"><span data-stu-id="467ae-1146">Abstract property declarations are only permitted in abstract classes ([Abstract classes](classes.md#abstract-classes)).The accessors of an inherited virtual property can be overridden in a derived class by including a property declaration that specifies an `override` directive.</span></span> <span data-ttu-id="467ae-1147">Questa operazione è nota come ***dichiarazione di proprietà che esegue l'override***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1147">This is known as an ***overriding property declaration***.</span></span> <span data-ttu-id="467ae-1148">Una dichiarazione di proprietà che esegue l'override non dichiara una nuova proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1148">An overriding property declaration does not declare a new property.</span></span> <span data-ttu-id="467ae-1149">Ma semplicemente specializza le implementazioni delle funzioni di accesso di una proprietà virtuale esistente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1149">Instead, it simply specializes the implementations of the accessors of an existing virtual property.</span></span>

<span data-ttu-id="467ae-1150">Una dichiarazione di proprietà che esegue l'override deve specificare esattamente gli stessi modificatori di accessibilità, tipo e nome della proprietà ereditata.</span><span class="sxs-lookup"><span data-stu-id="467ae-1150">An overriding property declaration must specify the exact same accessibility modifiers, type, and name as the inherited property.</span></span> <span data-ttu-id="467ae-1151">Se la proprietà ereditata ha solo una singola funzione di accesso (ad esempio, se la proprietà ereditata è di sola lettura o di sola scrittura), la proprietà che esegue l'override deve includere solo tale funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1151">If the inherited property has only a single accessor (i.e., if the inherited property is read-only or write-only), the overriding property must include only that accessor.</span></span> <span data-ttu-id="467ae-1152">Se la proprietà ereditata include entrambe le funzioni di accesso (ad esempio, se la proprietà ereditata è di lettura/scrittura), la proprietà che esegue l'override può includere una singola funzione di accesso o entrambe le funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1152">If the inherited property includes both accessors (i.e., if the inherited property is read-write), the overriding property can include either a single accessor or both accessors.</span></span>

<span data-ttu-id="467ae-1153">Una dichiarazione di proprietà che esegue l'override `sealed` può includere il modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1153">An overriding property declaration may include the `sealed` modifier.</span></span> <span data-ttu-id="467ae-1154">L'uso di questo modificatore impedisce a una classe derivata di eseguire ulteriormente l'override della proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1154">Use of this modifier prevents a derived class from further overriding the property.</span></span> <span data-ttu-id="467ae-1155">Anche le funzioni di accesso di una proprietà sealed sono sealed.</span><span class="sxs-lookup"><span data-stu-id="467ae-1155">The accessors of a sealed property are also sealed.</span></span>

<span data-ttu-id="467ae-1156">Ad eccezione delle differenze nella sintassi della dichiarazione e della chiamata, le funzioni di accesso Virtual, sealed, override e abstract si comportano esattamente come i metodi virtual, sealed, override e abstract.</span><span class="sxs-lookup"><span data-stu-id="467ae-1156">Except for differences in declaration and invocation syntax, virtual, sealed, override, and abstract accessors behave exactly like virtual, sealed, override and abstract methods.</span></span> <span data-ttu-id="467ae-1157">In particolare, le regole descritte in [metodi virtuali](classes.md#virtual-methods), [metodi di override](classes.md#override-methods), [Metodi sealed](classes.md#sealed-methods)e [metodi astratti](classes.md#abstract-methods) si applicano come se le funzioni di accesso fossero metodi di un form corrispondente:</span><span class="sxs-lookup"><span data-stu-id="467ae-1157">Specifically, the rules described in [Virtual methods](classes.md#virtual-methods), [Override methods](classes.md#override-methods), [Sealed methods](classes.md#sealed-methods), and [Abstract methods](classes.md#abstract-methods) apply as if accessors were methods of a corresponding form:</span></span>

*  <span data-ttu-id="467ae-1158">Una `get` funzione di accesso corrisponde a un metodo senza parametri con un valore restituito del tipo di proprietà e gli stessi modificatori della proprietà che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="467ae-1158">A `get` accessor corresponds to a parameterless method with a return value of the property type and the same modifiers as the containing property.</span></span>
*  <span data-ttu-id="467ae-1159">Una `set` funzione di accesso corrisponde a un metodo con un singolo parametro di valore del tipo di `void` proprietà, un tipo restituito e gli stessi modificatori della proprietà che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="467ae-1159">A `set` accessor corresponds to a method with a single value parameter of the property type, a `void` return type, and the same modifiers as the containing property.</span></span>

<span data-ttu-id="467ae-1160">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1160">In the example</span></span>
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
<span data-ttu-id="467ae-1161">`X`è una proprietà di sola lettura virtuale, `Y` è una proprietà di lettura/scrittura virtuale ed `Z` è una proprietà astratta di lettura/scrittura.</span><span class="sxs-lookup"><span data-stu-id="467ae-1161">`X` is a virtual read-only property, `Y` is a virtual read-write property, and `Z` is an abstract read-write property.</span></span> <span data-ttu-id="467ae-1162">Poiché `Z` è astratto, la classe `A` che lo contiene deve anche essere dichiarata astratta.</span><span class="sxs-lookup"><span data-stu-id="467ae-1162">Because `Z` is abstract, the containing class `A` must also be declared abstract.</span></span>

<span data-ttu-id="467ae-1163">Una classe che deriva da viene `A` mostrata di seguito:</span><span class="sxs-lookup"><span data-stu-id="467ae-1163">A class that derives from `A` is show below:</span></span>
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

<span data-ttu-id="467ae-1164">Qui, le dichiarazioni di, `X` `Y`e `Z` eseguono l'override delle dichiarazioni di proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1164">Here, the declarations of `X`, `Y`, and `Z` are overriding property declarations.</span></span> <span data-ttu-id="467ae-1165">Ogni dichiarazione di proprietà corrisponde esattamente ai modificatori di accessibilità, al tipo e al nome della proprietà ereditata corrispondente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1165">Each property declaration exactly matches the accessibility modifiers, type, and name of the corresponding inherited property.</span></span> <span data-ttu-id="467ae-1166">La `get` funzione di `X` accesso di `set` e la `Y` funzione di `base` accesso di usano la parola chiave per accedere alle funzioni di accesso ereditate.</span><span class="sxs-lookup"><span data-stu-id="467ae-1166">The `get` accessor of `X` and the `set` accessor of `Y` use the `base` keyword to access the inherited accessors.</span></span> <span data-ttu-id="467ae-1167">La dichiarazione di `Z` esegue l'override di entrambe le funzioni di accesso astratte, pertanto non esistono membri `B`di funzioni `B` astratte in attesa in e può essere una classe non astratta.</span><span class="sxs-lookup"><span data-stu-id="467ae-1167">The declaration of `Z` overrides both abstract accessors—thus, there are no outstanding abstract function members in `B`, and `B` is permitted to be a non-abstract class.</span></span>

<span data-ttu-id="467ae-1168">Quando una proprietà viene dichiarata `override`come, tutte le funzioni di accesso sottoposte a override devono essere accessibili al codice che esegue l'override.</span><span class="sxs-lookup"><span data-stu-id="467ae-1168">When a property is declared as an `override`, any overridden accessors must be accessible to the overriding code.</span></span> <span data-ttu-id="467ae-1169">Inoltre, l'accessibilità dichiarata sia della proprietà che dell'indicizzatore e delle funzioni di accesso devono corrispondere a quelle del membro e delle funzioni di accesso sottoposti a override.</span><span class="sxs-lookup"><span data-stu-id="467ae-1169">In addition, the declared accessibility of both the property or indexer itself, and of the accessors, must match that of the overridden member and accessors.</span></span> <span data-ttu-id="467ae-1170">Esempio:</span><span class="sxs-lookup"><span data-stu-id="467ae-1170">For example:</span></span>
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

## <a name="events"></a><span data-ttu-id="467ae-1171">Events</span><span class="sxs-lookup"><span data-stu-id="467ae-1171">Events</span></span>

<span data-ttu-id="467ae-1172">Un ***evento*** è un membro che consente a un oggetto o a una classe di fornire notifiche.</span><span class="sxs-lookup"><span data-stu-id="467ae-1172">An ***event*** is a member that enables an object or class to provide notifications.</span></span> <span data-ttu-id="467ae-1173">I client possono alleghino il codice eseguibile per gli eventi fornendo i ***gestori eventi***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1173">Clients can attach executable code for events by supplying ***event handlers***.</span></span>

<span data-ttu-id="467ae-1174">Gli eventi vengono dichiarati usando *event_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="467ae-1174">Events are declared using *event_declaration*s:</span></span>

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

<span data-ttu-id="467ae-1175">Un *event_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)), il `new` ([il modificatore new](classes.md#the-new-modifier)), `static` ([static e instance Metodi](classes.md#static-and-instance-methods)), `virtual` ([metodi virtuali](classes.md#virtual-methods)), 0 ([metodi di override](classes.md#override-methods)), 2[(Metodi sealed](classes.md#sealed-methods)), 4 ([metodi astratti](classes.md#abstract-methods)) e i modificatori 6 ([metodi esterni](classes.md#external-methods)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1175">An *event_declaration* may include a set of *attributes* ([Attributes](attributes.md)) and a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), the `new` ([The new modifier](classes.md#the-new-modifier)),  `static` ([Static and instance methods](classes.md#static-and-instance-methods)), `virtual` ([Virtual methods](classes.md#virtual-methods)), `override` ([Override methods](classes.md#override-methods)), `sealed` ([Sealed methods](classes.md#sealed-methods)), `abstract` ([Abstract methods](classes.md#abstract-methods)), and `extern` ([External methods](classes.md#external-methods)) modifiers.</span></span>

<span data-ttu-id="467ae-1176">Le dichiarazioni di evento sono soggette alle stesse regole delle dichiarazioni di metodo ([Metodi](classes.md#methods)) per quanto concerne combinazioni valide di modificatori.</span><span class="sxs-lookup"><span data-stu-id="467ae-1176">Event declarations are subject to the same rules as method declarations ([Methods](classes.md#methods)) with regard to valid combinations of modifiers.</span></span>

<span data-ttu-id="467ae-1177">Il *tipo* di una dichiarazione di evento deve essere un *delegate_type* ([tipi di riferimento](types.md#reference-types)) e *delegate_type* deve essere accessibile almeno quanto l'evento stesso (vincoli di[accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1177">The *type* of an event declaration must be a *delegate_type* ([Reference types](types.md#reference-types)), and that *delegate_type* must be at least as accessible as the event itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="467ae-1178">Una dichiarazione di evento può includere *event_accessor_declarations*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1178">An event declaration may include *event_accessor_declarations*.</span></span> <span data-ttu-id="467ae-1179">Tuttavia, in caso contrario, per gli eventi non esterni, non astratti, il compilatore li fornisce automaticamente ([eventi simili ai campi](classes.md#field-like-events)); per gli eventi extern, le funzioni di accesso vengono fornite esternamente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1179">However, if it does not, for non-extern, non-abstract events, the compiler supplies them automatically ([Field-like events](classes.md#field-like-events)); for extern events, the accessors are provided externally.</span></span>

<span data-ttu-id="467ae-1180">Una dichiarazione di evento che omette *event_accessor_declarations* definisce uno o più eventi, uno per ogni *variable_declarator*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1180">An event declaration that omits *event_accessor_declarations* defines one or more events—one for each of the *variable_declarator*s.</span></span> <span data-ttu-id="467ae-1181">Gli attributi e i modificatori si applicano a tutti i membri dichiarati da un *event_declaration*di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1181">The attributes and modifiers apply to all of the members declared by such an *event_declaration*.</span></span>

<span data-ttu-id="467ae-1182">Si tratta di un errore in fase di compilazione per un *event_declaration* che include sia il modificatore `abstract` sia il *event_accessor_declarations*delimitato da parentesi graffe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1182">It is a compile-time error for an *event_declaration* to include both the `abstract` modifier and brace-delimited *event_accessor_declarations*.</span></span>

<span data-ttu-id="467ae-1183">Quando una dichiarazione di evento include `extern` un modificatore, l'evento viene definito ***evento esterno***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1183">When an event declaration includes an `extern` modifier, the event is said to be an ***external event***.</span></span> <span data-ttu-id="467ae-1184">Poiché una dichiarazione di evento esterno non fornisce alcuna implementazione effettiva, è un errore per includere sia il modificatore `extern` che *event_accessor_declarations*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1184">Because an external event declaration provides no actual implementation, it is an error for it to include both the `extern` modifier and *event_accessor_declarations*.</span></span>

<span data-ttu-id="467ae-1185">Si tratta di un errore in fase di compilazione per un *variable_declarator* di una dichiarazione di evento con un modificatore `abstract` o `external` per includere un *variable_initializer*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1185">It is a compile-time error for a *variable_declarator* of an event declaration with an `abstract` or `external` modifier to include a *variable_initializer*.</span></span>

<span data-ttu-id="467ae-1186">Un evento può essere usato come operando sinistro degli operatori `+=` e `-=` ([Assegnazione di eventi](expressions.md#event-assignment)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1186">An event can be used as the left-hand operand of the `+=` and `-=` operators ([Event assignment](expressions.md#event-assignment)).</span></span> <span data-ttu-id="467ae-1187">Questi operatori vengono utilizzati rispettivamente per il collegamento di gestori eventi a o per rimuovere i gestori eventi da un evento e i modificatori di accesso dell'evento controllano i contesti in cui sono consentite tali operazioni.</span><span class="sxs-lookup"><span data-stu-id="467ae-1187">These operators are used, respectively, to attach event handlers to or to remove event handlers from an event, and the access modifiers of the event control the contexts in which such operations are permitted.</span></span>

<span data-ttu-id="467ae-1188">Poiché `+=` e`-=` sono le uniche operazioni consentite in un evento all'esterno del tipo che dichiara l'evento, il codice esterno può aggiungere e rimuovere gestori per un evento, ma non può in alcun altro modo ottenere o modificare l'elenco sottostante di eventi gestori.</span><span class="sxs-lookup"><span data-stu-id="467ae-1188">Since `+=` and `-=` are the only operations that are permitted on an event outside the type that declares the event, external code can add and remove handlers for an event, but cannot in any other way obtain or modify the underlying list of event handlers.</span></span>

<span data-ttu-id="467ae-1189">In un'operazione del form `x += y` o `x -= y`, quando `x` è un evento e il riferimento si verifica all'esterno del tipo che contiene la dichiarazione di `x`, il risultato dell'operazione è di tipo `void` (invece di tipo di `x`, con il valore di `x` dopo l'assegnazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1189">In an operation of the form `x += y` or `x -= y`, when `x` is an event and the reference takes place outside the type that contains the declaration of `x`, the result of the operation has type `void` (as opposed to having the type of `x`, with the value of `x` after the assignment).</span></span> <span data-ttu-id="467ae-1190">Questa regola impedisce al codice esterno di esaminare indirettamente il delegato sottostante di un evento.</span><span class="sxs-lookup"><span data-stu-id="467ae-1190">This rule prohibits external code from indirectly examining the underlying delegate of an event.</span></span>

<span data-ttu-id="467ae-1191">Nell'esempio seguente viene illustrato il modo in cui i gestori eventi sono collegati `Button` alle istanze della classe:</span><span class="sxs-lookup"><span data-stu-id="467ae-1191">The following example shows how event handlers are attached to instances of the `Button` class:</span></span>
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

<span data-ttu-id="467ae-1192">In questo caso `LoginDialog` , il costruttore di `Button` istanze crea due istanze e connette i `Click` gestori eventi agli eventi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1192">Here, the `LoginDialog` instance constructor creates two `Button` instances and attaches event handlers to the `Click` events.</span></span>

### <a name="field-like-events"></a><span data-ttu-id="467ae-1193">Eventi simili a campi</span><span class="sxs-lookup"><span data-stu-id="467ae-1193">Field-like events</span></span>

<span data-ttu-id="467ae-1194">All'interno del testo del programma della classe o dello struct che contiene la dichiarazione di un evento, è possibile usare alcuni eventi come i campi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1194">Within the program text of the class or struct that contains the declaration of an event, certain events can be used like fields.</span></span> <span data-ttu-id="467ae-1195">Per poter essere usati in questo modo, un evento non deve essere `abstract` o `extern` e non deve includere in modo esplicito *event_accessor_declarations*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1195">To be used in this way, an event must not be `abstract` or `extern`, and must not explicitly include *event_accessor_declarations*.</span></span> <span data-ttu-id="467ae-1196">Un evento di questo tipo può essere utilizzato in qualsiasi contesto che consenta un campo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1196">Such an event can be used in any context that permits a field.</span></span> <span data-ttu-id="467ae-1197">Il campo contiene un delegato ([delegati](delegates.md)) che fa riferimento all'elenco di gestori di eventi che sono stati aggiunti all'evento.</span><span class="sxs-lookup"><span data-stu-id="467ae-1197">The field contains a delegate ([Delegates](delegates.md)) which refers to the list of event handlers that have been added to the event.</span></span> <span data-ttu-id="467ae-1198">Se non è stato aggiunto alcun gestore eventi, il campo contiene `null`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1198">If no event handlers have been added, the field contains `null`.</span></span>

<span data-ttu-id="467ae-1199">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1199">In the example</span></span>
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
<span data-ttu-id="467ae-1200">`Click`viene usato come campo all'interno della `Button` classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1200">`Click` is used as a field within the `Button` class.</span></span> <span data-ttu-id="467ae-1201">Come illustrato nell'esempio, il campo può essere esaminato, modificato e utilizzato nelle espressioni di chiamata dei delegati.</span><span class="sxs-lookup"><span data-stu-id="467ae-1201">As the example demonstrates, the field can be examined, modified, and used in delegate invocation expressions.</span></span> <span data-ttu-id="467ae-1202">Il `OnClick` metodo `Click` nella classe "genera" l'evento. `Button`</span><span class="sxs-lookup"><span data-stu-id="467ae-1202">The `OnClick` method in the `Button` class "raises" the `Click` event.</span></span> <span data-ttu-id="467ae-1203">Generare un evento equivale a richiamare il delegato rappresentato dall'evento. Non sono quindi necessari speciali costrutti di linguaggio per generare eventi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1203">The notion of raising an event is precisely equivalent to invoking the delegate represented by the event—thus, there are no special language constructs for raising events.</span></span> <span data-ttu-id="467ae-1204">Si noti che la chiamata al delegato è preceduta da un controllo che garantisce che il delegato sia diverso da null.</span><span class="sxs-lookup"><span data-stu-id="467ae-1204">Note that the delegate invocation is preceded by a check that ensures the delegate is non-null.</span></span>

<span data-ttu-id="467ae-1205">Al di fuori della Dichiarazione `Button` della classe, `Click` il membro può essere usato solo sul lato sinistro degli `+=` operatori e `-=` , come in</span><span class="sxs-lookup"><span data-stu-id="467ae-1205">Outside the declaration of the `Button` class, the `Click` member can only be used on the left-hand side of the `+=` and `-=` operators, as in</span></span>
```csharp
b.Click += new EventHandler(...);
```
<span data-ttu-id="467ae-1206">che aggiunge un delegato all'elenco chiamate dell' `Click` evento e</span><span class="sxs-lookup"><span data-stu-id="467ae-1206">which appends a delegate to the invocation list of the `Click` event, and</span></span>
```csharp
b.Click -= new EventHandler(...);
```
<span data-ttu-id="467ae-1207">che rimuove un delegato dall'elenco chiamate dell' `Click` evento.</span><span class="sxs-lookup"><span data-stu-id="467ae-1207">which removes a delegate from the invocation list of the `Click` event.</span></span>

<span data-ttu-id="467ae-1208">Quando si compila un evento di tipo campo, il compilatore crea automaticamente lo spazio di archiviazione per conservare il delegato e crea funzioni di accesso per l'evento che aggiungono o rimuovono gestori eventi nel campo delegato.</span><span class="sxs-lookup"><span data-stu-id="467ae-1208">When compiling a field-like event, the compiler automatically creates storage to hold the delegate, and creates accessors for the event that add or remove event handlers to the delegate field.</span></span> <span data-ttu-id="467ae-1209">Le operazioni di aggiunta e rimozione sono thread-safe e può (ma non è necessario) essere eseguite tenendo premuto il blocco ([istruzione lock](statements.md#the-lock-statement)) nell'oggetto contenitore per un evento di istanza o l'oggetto tipo (espressioni di[creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions)) per un evento statico.</span><span class="sxs-lookup"><span data-stu-id="467ae-1209">The addition and removal operations are thread safe, and may (but are not required to) be done while holding the lock ([The lock statement](statements.md#the-lock-statement)) on the containing object for an instance event, or the type object ([Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions)) for a static event.</span></span>

<span data-ttu-id="467ae-1210">Pertanto, una dichiarazione di evento di istanza nel formato seguente:</span><span class="sxs-lookup"><span data-stu-id="467ae-1210">Thus, an instance event declaration of the form:</span></span>
```csharp
class X
{
    public event D Ev;
}
```
<span data-ttu-id="467ae-1211">verrà compilato in un valore equivalente a:</span><span class="sxs-lookup"><span data-stu-id="467ae-1211">will be compiled to something equivalent to:</span></span>
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
<span data-ttu-id="467ae-1212">All'interno della `X`classe, i `Ev` riferimenti a sul lato sinistro degli `+=` operatori e `-=` provocano la chiamata delle funzioni di accesso Add e Remove.</span><span class="sxs-lookup"><span data-stu-id="467ae-1212">Within the class `X`, references to `Ev` on the left-hand side of the `+=` and `-=` operators cause the add and remove accessors to be invoked.</span></span> <span data-ttu-id="467ae-1213">Tutti gli altri riferimenti `Ev` a vengono compilati in modo `__Ev` da fare riferimento al campo nascosto ([accesso ai membri](expressions.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1213">All other references to `Ev` are compiled to reference the hidden field `__Ev` instead ([Member access](expressions.md#member-access)).</span></span> <span data-ttu-id="467ae-1214">Il nome "`__Ev`" è arbitrario. il campo nascosto potrebbe avere qualsiasi nome o nessun nome.</span><span class="sxs-lookup"><span data-stu-id="467ae-1214">The name "`__Ev`" is arbitrary; the hidden field could have any name or no name at all.</span></span>

### <a name="event-accessors"></a><span data-ttu-id="467ae-1215">Funzioni di accesso agli eventi</span><span class="sxs-lookup"><span data-stu-id="467ae-1215">Event accessors</span></span>

<span data-ttu-id="467ae-1216">Le dichiarazioni di evento in genere omettono *event_accessor_declarations*, come nell'esempio `Button` precedente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1216">Event declarations typically omit *event_accessor_declarations*, as in the `Button` example above.</span></span> <span data-ttu-id="467ae-1217">Una situazione in cui è necessario eseguire questa operazione riguarda il caso in cui il costo di archiviazione di un campo per ogni evento non è accettabile.</span><span class="sxs-lookup"><span data-stu-id="467ae-1217">One situation for doing so involves the case in which the storage cost of one field per event is not acceptable.</span></span> <span data-ttu-id="467ae-1218">In questi casi, una classe può includere *event_accessor_declarations* e usare un meccanismo privato per archiviare l'elenco dei gestori eventi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1218">In such cases, a class can include *event_accessor_declarations* and use a private mechanism for storing the list of event handlers.</span></span>

<span data-ttu-id="467ae-1219">*Event_accessor_declarations* di un evento specificano le istruzioni eseguibili associate all'aggiunta e alla rimozione di gestori eventi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1219">The *event_accessor_declarations* of an event specify the executable statements associated with adding and removing event handlers.</span></span>

<span data-ttu-id="467ae-1220">Le dichiarazioni delle funzioni di accesso sono costituite da un *add_accessor_declaration* e da un *remove_accessor_declaration*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1220">The accessor declarations consist of an *add_accessor_declaration* and a *remove_accessor_declaration*.</span></span> <span data-ttu-id="467ae-1221">Ogni dichiarazione di funzione di accesso è `add` costituita dal token o `remove` seguito da un *blocco*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1221">Each accessor declaration consists of the token `add` or `remove` followed by a *block*.</span></span> <span data-ttu-id="467ae-1222">Il *blocco* associato a un *add_accessor_declaration* specifica le istruzioni da eseguire quando viene aggiunto un gestore eventi e il *blocco* associato a un *remove_accessor_declaration* specifica le istruzioni da eseguire Quando viene rimosso un gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1222">The *block* associated with an *add_accessor_declaration* specifies the statements to execute when an event handler is added, and the *block* associated with a *remove_accessor_declaration* specifies the statements to execute when an event handler is removed.</span></span>

<span data-ttu-id="467ae-1223">Ogni *add_accessor_declaration* e *remove_accessor_declaration* corrisponde a un metodo con un singolo parametro di valore del tipo di evento e un tipo restituito `void`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1223">Each *add_accessor_declaration* and *remove_accessor_declaration* corresponds to a method with a single value parameter of the event type and a `void` return type.</span></span> <span data-ttu-id="467ae-1224">Il parametro implicito di una funzione di accesso `value`eventi è denominato.</span><span class="sxs-lookup"><span data-stu-id="467ae-1224">The implicit parameter of an event accessor is named `value`.</span></span> <span data-ttu-id="467ae-1225">Quando un evento viene utilizzato in un'assegnazione di evento, viene utilizzata la funzione di accesso eventi appropriata.</span><span class="sxs-lookup"><span data-stu-id="467ae-1225">When an event is used in an event assignment, the appropriate event accessor is used.</span></span> <span data-ttu-id="467ae-1226">In particolare, se l'operatore `+=` di assegnazione è, viene usata la funzione di accesso Add e se l'operatore di assegnazione è `-=` , viene usata la funzione di accesso remove.</span><span class="sxs-lookup"><span data-stu-id="467ae-1226">Specifically, if the assignment operator is `+=` then the add accessor is used, and if the assignment operator is `-=` then the remove accessor is used.</span></span> <span data-ttu-id="467ae-1227">In entrambi i casi, l'operando destro dell'operatore di assegnazione viene usato come argomento della funzione di accesso dell'evento.</span><span class="sxs-lookup"><span data-stu-id="467ae-1227">In either case, the right-hand operand of the assignment operator is used as the argument to the event accessor.</span></span> <span data-ttu-id="467ae-1228">Il blocco di un *add_accessor_declaration* o *remove_accessor_declaration* deve essere conforme alle regole per i metodi `void` descritti nel [corpo del metodo](classes.md#method-body).</span><span class="sxs-lookup"><span data-stu-id="467ae-1228">The block of an *add_accessor_declaration* or a *remove_accessor_declaration* must conform to the rules for `void` methods described in [Method body](classes.md#method-body).</span></span> <span data-ttu-id="467ae-1229">In particolare, `return` le istruzioni in un blocco di questo tipo non sono autorizzate a specificare un'espressione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1229">In particular, `return` statements in such a block are not permitted to specify an expression.</span></span>

<span data-ttu-id="467ae-1230">Poiché una funzione di accesso agli eventi include implicitamente `value`un parametro denominato, si tratta di un errore in fase di compilazione per una variabile locale o una costante dichiarata in una funzione di accesso a un evento con tale nome.</span><span class="sxs-lookup"><span data-stu-id="467ae-1230">Since an event accessor implicitly has a parameter named `value`, it is a compile-time error for a local variable or constant declared in an event accessor to have that name.</span></span>

<span data-ttu-id="467ae-1231">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1231">In the example</span></span>
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
<span data-ttu-id="467ae-1232">la `Control` classe implementa un meccanismo di archiviazione interno per gli eventi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1232">the `Control` class implements an internal storage mechanism for events.</span></span> <span data-ttu-id="467ae-1233">Il `AddEventHandler` metodo associa un valore delegato a una chiave, il `GetEventHandler` metodo restituisce il delegato attualmente associato a una chiave e il `RemoveEventHandler` metodo rimuove un delegato come gestore eventi per l'evento specificato.</span><span class="sxs-lookup"><span data-stu-id="467ae-1233">The `AddEventHandler` method associates a delegate value with a key, the `GetEventHandler` method returns the delegate currently associated with a key, and the `RemoveEventHandler` method removes a delegate as an event handler for the specified event.</span></span> <span data-ttu-id="467ae-1234">Presumibilmente, il meccanismo di archiviazione sottostante è progettato in modo che non vi sia alcun costo per l' `null` associazione di un valore delegato a una chiave, quindi gli eventi non gestiti non utilizzano alcuna risorsa di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1234">Presumably, the underlying storage mechanism is designed such that there is no cost for associating a `null` delegate value with a key, and thus unhandled events consume no storage.</span></span>

### <a name="static-and-instance-events"></a><span data-ttu-id="467ae-1235">Eventi statici e di istanza</span><span class="sxs-lookup"><span data-stu-id="467ae-1235">Static and instance events</span></span>

<span data-ttu-id="467ae-1236">Quando una dichiarazione di evento include `static` un modificatore, l'evento viene definito ***evento statico***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1236">When an event declaration includes a `static` modifier, the event is said to be a ***static event***.</span></span> <span data-ttu-id="467ae-1237">Quando non `static` è presente alcun modificatore, l'evento viene definito ***evento dell'istanza***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1237">When no `static` modifier is present, the event is said to be an ***instance event***.</span></span>

<span data-ttu-id="467ae-1238">Un evento statico non è associato a un'istanza specifica e si tratta di un errore `this` in fase di compilazione a cui fare riferimento nelle funzioni di accesso di un evento statico.</span><span class="sxs-lookup"><span data-stu-id="467ae-1238">A static event is not associated with a specific instance, and it is a compile-time error to refer to `this` in the accessors of a static event.</span></span>

<span data-ttu-id="467ae-1239">Un evento di istanza è associato a una determinata istanza di una classe e a questa istanza è possibile accedere `this` come ([questo accesso](expressions.md#this-access)) nelle funzioni di accesso dell'evento.</span><span class="sxs-lookup"><span data-stu-id="467ae-1239">An instance event is associated with a given instance of a class, and this instance can be accessed as `this` ([This access](expressions.md#this-access)) in the accessors of that event.</span></span>

<span data-ttu-id="467ae-1240">Quando si fa riferimento a un evento in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, se `M` è un evento statico, `E` deve indicare un tipo che contiene `M` e se `M` è un evento di istanza, e deve indicare un'istanza di un tipo che contiene `M`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1240">When an event is referenced in a *member_access* ([Member access](expressions.md#member-access)) of the form `E.M`, if `M` is a static event, `E` must denote a type containing `M`, and if `M` is an instance event, E must denote an instance of a type containing `M`.</span></span>

<span data-ttu-id="467ae-1241">Le differenze tra i membri statici e i membri di istanza sono illustrate ulteriormente nei [membri statici e di istanza](classes.md#static-and-instance-members).</span><span class="sxs-lookup"><span data-stu-id="467ae-1241">The differences between static and instance members are discussed further in [Static and instance members](classes.md#static-and-instance-members).</span></span>

### <a name="virtual-sealed-override-and-abstract-event-accessors"></a><span data-ttu-id="467ae-1242">Funzioni di accesso a eventi virtuali, sealed, override e astratti</span><span class="sxs-lookup"><span data-stu-id="467ae-1242">Virtual, sealed, override, and abstract event accessors</span></span>

<span data-ttu-id="467ae-1243">Una `virtual` dichiarazione di evento specifica che le funzioni di accesso di tale evento sono virtuali.</span><span class="sxs-lookup"><span data-stu-id="467ae-1243">A `virtual` event declaration specifies that the accessors of that event are virtual.</span></span> <span data-ttu-id="467ae-1244">Il `virtual` modificatore si applica a entrambe le funzioni di accesso di un evento.</span><span class="sxs-lookup"><span data-stu-id="467ae-1244">The `virtual` modifier applies to both accessors of an event.</span></span>

<span data-ttu-id="467ae-1245">Una `abstract` dichiarazione di evento specifica che le funzioni di accesso dell'evento sono virtuali, ma non fornisce un'implementazione effettiva delle funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1245">An `abstract` event declaration specifies that the accessors of the event are virtual, but does not provide an actual implementation of the accessors.</span></span> <span data-ttu-id="467ae-1246">Al contrario, le classi derivate non astratte sono necessarie per fornire la propria implementazione per le funzioni di accesso eseguendo l'override dell'evento.</span><span class="sxs-lookup"><span data-stu-id="467ae-1246">Instead, non-abstract derived classes are required to provide their own implementation for the accessors by overriding the event.</span></span> <span data-ttu-id="467ae-1247">Poiché una dichiarazione di evento astratto non fornisce alcuna implementazione effettiva, non può fornire *event_accessor_declarations*delimitati da parentesi graffe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1247">Because an abstract event declaration provides no actual implementation, it cannot provide brace-delimited *event_accessor_declarations*.</span></span>

<span data-ttu-id="467ae-1248">Una dichiarazione di evento che include entrambi `abstract` i `override` modificatori e specifica che l'evento è astratto ed esegue l'override di un evento di base.</span><span class="sxs-lookup"><span data-stu-id="467ae-1248">An event declaration that includes both the `abstract` and `override` modifiers specifies that the event is abstract and overrides a base event.</span></span> <span data-ttu-id="467ae-1249">Anche le funzioni di accesso di tale evento sono astratte.</span><span class="sxs-lookup"><span data-stu-id="467ae-1249">The accessors of such an event are also abstract.</span></span>

<span data-ttu-id="467ae-1250">Le dichiarazioni di eventi astratti sono consentite solo nelle classi astratte ([classi astratte](classes.md#abstract-classes)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1250">Abstract event declarations are only permitted in abstract classes ([Abstract classes](classes.md#abstract-classes)).</span></span>

<span data-ttu-id="467ae-1251">Le funzioni di accesso di un evento virtuale ereditato possono essere sottoposte a override in una classe derivata includendo una Dichiarazione `override` di evento che specifica un modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1251">The accessors of an inherited virtual event can be overridden in a derived class by including an event declaration that specifies an `override` modifier.</span></span> <span data-ttu-id="467ae-1252">Questa operazione è nota come ***dichiarazione di evento di override***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1252">This is known as an ***overriding event declaration***.</span></span> <span data-ttu-id="467ae-1253">Una dichiarazione di evento che esegue l'override non dichiara un nuovo evento.</span><span class="sxs-lookup"><span data-stu-id="467ae-1253">An overriding event declaration does not declare a new event.</span></span> <span data-ttu-id="467ae-1254">Ma semplicemente specializza le implementazioni delle funzioni di accesso di un evento virtuale esistente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1254">Instead, it simply specializes the implementations of the accessors of an existing virtual event.</span></span>

<span data-ttu-id="467ae-1255">Una dichiarazione di evento di override deve specificare esattamente gli stessi modificatori di accessibilità, tipo e nome dell'evento sottoposto a override.</span><span class="sxs-lookup"><span data-stu-id="467ae-1255">An overriding event declaration must specify the exact same accessibility modifiers, type, and name as the overridden event.</span></span>

<span data-ttu-id="467ae-1256">Una dichiarazione di evento di override può includere `sealed` il modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1256">An overriding event declaration may include the `sealed` modifier.</span></span> <span data-ttu-id="467ae-1257">L'uso di questo modificatore impedisce a una classe derivata di eseguire ulteriormente l'override dell'evento.</span><span class="sxs-lookup"><span data-stu-id="467ae-1257">Use of this modifier prevents a derived class from further overriding the event.</span></span> <span data-ttu-id="467ae-1258">Anche le funzioni di accesso di un evento Sealed sono sealed.</span><span class="sxs-lookup"><span data-stu-id="467ae-1258">The accessors of a sealed event are also sealed.</span></span>

<span data-ttu-id="467ae-1259">Si tratta di un errore in fase di compilazione per la dichiarazione di un evento che `new` esegue l'override per includere un modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1259">It is a compile-time error for an overriding event declaration to include a `new` modifier.</span></span>

<span data-ttu-id="467ae-1260">Ad eccezione delle differenze nella sintassi della dichiarazione e della chiamata, le funzioni di accesso Virtual, sealed, override e abstract si comportano esattamente come i metodi virtual, sealed, override e abstract.</span><span class="sxs-lookup"><span data-stu-id="467ae-1260">Except for differences in declaration and invocation syntax, virtual, sealed, override, and abstract accessors behave exactly like virtual, sealed, override and abstract methods.</span></span> <span data-ttu-id="467ae-1261">In particolare, le regole descritte in [metodi virtuali](classes.md#virtual-methods), [metodi di override](classes.md#override-methods), [Metodi sealed](classes.md#sealed-methods)e [metodi astratti](classes.md#abstract-methods) si applicano come se le funzioni di accesso fossero metodi di un form corrispondente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1261">Specifically, the rules described in [Virtual methods](classes.md#virtual-methods), [Override methods](classes.md#override-methods), [Sealed methods](classes.md#sealed-methods), and [Abstract methods](classes.md#abstract-methods) apply as if accessors were methods of a corresponding form.</span></span> <span data-ttu-id="467ae-1262">Ogni funzione di accesso corrisponde a un metodo con un singolo parametro di valore del tipo di `void` evento, un tipo restituito e gli stessi modificatori dell'evento che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="467ae-1262">Each accessor corresponds to a method with a single value parameter of the event type, a `void` return type, and the same modifiers as the containing event.</span></span>

## <a name="indexers"></a><span data-ttu-id="467ae-1263">Indicizzatori</span><span class="sxs-lookup"><span data-stu-id="467ae-1263">Indexers</span></span>

<span data-ttu-id="467ae-1264">Un ***indicizzatore*** è un membro che consente l'indicizzazione di un oggetto nello stesso modo di una matrice.</span><span class="sxs-lookup"><span data-stu-id="467ae-1264">An ***indexer*** is a member that enables an object to be indexed in the same way as an array.</span></span> <span data-ttu-id="467ae-1265">Gli indicizzatori vengono dichiarati usando *indexer_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="467ae-1265">Indexers are declared using *indexer_declaration*s:</span></span>

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

<span data-ttu-id="467ae-1266">Un *indexer_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)), il `new` ([il modificatore new](classes.md#the-new-modifier)), `virtual` ([metodi virtuali ](classes.md#virtual-methods)), `override` ([metodi di override](classes.md#override-methods)), 0[(Metodi sealed](classes.md#sealed-methods)), 2 ([metodi astratti](classes.md#abstract-methods)) e i modificatori 4 ([metodi esterni](classes.md#external-methods)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1266">An *indexer_declaration* may include a set of *attributes* ([Attributes](attributes.md)) and a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), the `new` ([The new modifier](classes.md#the-new-modifier)), `virtual` ([Virtual methods](classes.md#virtual-methods)), `override` ([Override methods](classes.md#override-methods)), `sealed` ([Sealed methods](classes.md#sealed-methods)), `abstract` ([Abstract methods](classes.md#abstract-methods)), and `extern` ([External methods](classes.md#external-methods)) modifiers.</span></span>

<span data-ttu-id="467ae-1267">Le dichiarazioni dell'indicizzatore sono soggette alle stesse regole delle dichiarazioni di metodo ([Metodi](classes.md#methods)) per quanto riguarda le combinazioni valide di modificatori, con la sola eccezione che il modificatore static non è consentito in una dichiarazione dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1267">Indexer declarations are subject to the same rules as method declarations ([Methods](classes.md#methods)) with regard to valid combinations of modifiers, with the one exception being that the static modifier is not permitted on an indexer declaration.</span></span>

<span data-ttu-id="467ae-1268">I modificatori `virtual`, `override`e `abstract` si escludono a vicenda, tranne che in un caso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1268">The modifiers `virtual`, `override`, and `abstract` are mutually exclusive except in one case.</span></span> <span data-ttu-id="467ae-1269">I `abstract` modificatori e `override` possono essere usati insieme in modo che un indicizzatore astratto possa eseguire l'override di uno virtuale.</span><span class="sxs-lookup"><span data-stu-id="467ae-1269">The `abstract` and `override` modifiers may be used together so that an abstract indexer can override a virtual one.</span></span>

<span data-ttu-id="467ae-1270">Il *tipo* di una dichiarazione di indicizzatore specifica il tipo di elemento dell'indicizzatore introdotto dalla dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1270">The *type* of an indexer declaration specifies the element type of the indexer introduced by the declaration.</span></span> <span data-ttu-id="467ae-1271">A meno che l'indicizzatore non sia un'implementazione esplicita di un membro di interfaccia, il `this`tipo è seguito dalla parola chiave.</span><span class="sxs-lookup"><span data-stu-id="467ae-1271">Unless the indexer is an explicit interface member implementation, the *type* is followed by the keyword `this`.</span></span> <span data-ttu-id="467ae-1272">Per un'implementazione esplicita di un membro di interfaccia, il *tipo* è seguito da un *INTERFACE_TYPE*, un "`.`" e la parola chiave `this`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1272">For an explicit interface member implementation, the *type* is followed by an *interface_type*, a "`.`", and the keyword `this`.</span></span> <span data-ttu-id="467ae-1273">A differenza di altri membri, gli indicizzatori non hanno nomi definiti dall'utente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1273">Unlike other members, indexers do not have user-defined names.</span></span>

<span data-ttu-id="467ae-1274">*Formal_parameter_list* specifica i parametri dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1274">The *formal_parameter_list* specifies the parameters of the indexer.</span></span> <span data-ttu-id="467ae-1275">L'elenco di parametri formali di un indicizzatore corrisponde a quello di un metodo ([parametri del metodo](classes.md#method-parameters)), ad eccezione del fatto che è necessario specificare almeno un `ref` parametro `out` e che i modificatori di parametro e non sono consentiti.</span><span class="sxs-lookup"><span data-stu-id="467ae-1275">The formal parameter list of an indexer corresponds to that of a method ([Method parameters](classes.md#method-parameters)), except that at least one parameter must be specified, and that the `ref` and `out` parameter modifiers are not permitted.</span></span>

<span data-ttu-id="467ae-1276">Il *tipo* di un indicizzatore e ciascuno dei tipi a cui si fa riferimento in *formal_parameter_list* deve essere accessibile almeno quanto l'indicizzatore stesso ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1276">The *type* of an indexer and each of the types referenced in the *formal_parameter_list* must be at least as accessible as the indexer itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="467ae-1277">Un *indexer_body* può essere costituito da un ***corpo della funzione di accesso*** o da un corpo di ***espressione***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1277">An *indexer_body* may either consist of an ***accessor body*** or an ***expression body***.</span></span> <span data-ttu-id="467ae-1278">In un corpo della funzione di accesso, *accessor_declarations*, che deve essere racchiuso tra token "`{`" e "`}`", dichiarare le funzioni di accesso ([funzioni di accesso](classes.md#accessors)) della proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1278">In an accessor body, *accessor_declarations*, which must be enclosed in "`{`" and "`}`" tokens, declare the accessors ([Accessors](classes.md#accessors)) of the property.</span></span> <span data-ttu-id="467ae-1279">Le funzioni di accesso specificano le istruzioni eseguibili associate alla lettura e alla scrittura della proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1279">The accessors specify the executable statements associated with reading and writing the property.</span></span>

<span data-ttu-id="467ae-1280">Il corpo di un'espressione costituito`=>`da "" seguito da `E` un'espressione e da un punto e virgola è `{ get { return E; } }`esattamente equivalente al corpo dell'istruzione e pertanto può essere utilizzato solo per specificare gli indicizzatori solo Get in cui il risultato del Getter è fornito da un'unica espressione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1280">An expression body consisting of "`=>`" followed by an expression `E` and a semicolon is exactly equivalent to the statement body `{ get { return E; } }`, and can therefore only be used to specify getter-only indexers where the result of the getter is given by a single expression.</span></span>

<span data-ttu-id="467ae-1281">Anche se la sintassi per l'accesso a un indicizzatore è identica a quella di un elemento di matrice, un elemento indicizzatore non viene classificato come variabile.</span><span class="sxs-lookup"><span data-stu-id="467ae-1281">Even though the syntax for accessing an indexer element is the same as that for an array element, an indexer element is not classified as a variable.</span></span> <span data-ttu-id="467ae-1282">Non è quindi possibile passare un elemento indicizzatore come `ref` argomento o. `out`</span><span class="sxs-lookup"><span data-stu-id="467ae-1282">Thus, it is not possible to pass an indexer element as a `ref` or `out` argument.</span></span>

<span data-ttu-id="467ae-1283">L'elenco di parametri formali di un indicizzatore definisce la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1283">The formal parameter list of an indexer defines the signature ([Signatures and overloading](basic-concepts.md#signatures-and-overloading)) of the indexer.</span></span> <span data-ttu-id="467ae-1284">In particolare, la firma di un indicizzatore è costituita dal numero e dai tipi dei parametri formali.</span><span class="sxs-lookup"><span data-stu-id="467ae-1284">Specifically, the signature of an indexer consists of the number and types of its formal parameters.</span></span> <span data-ttu-id="467ae-1285">Il tipo di elemento e i nomi dei parametri formali non fanno parte della firma di un indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1285">The element type and names of the formal parameters are not part of an indexer's signature.</span></span>

<span data-ttu-id="467ae-1286">La firma di un indicizzatore deve essere diversa dalle firme di tutti gli altri indicizzatori dichiarati nella stessa classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1286">The signature of an indexer must differ from the signatures of all other indexers declared in the same class.</span></span>

<span data-ttu-id="467ae-1287">Gli indicizzatori e le proprietà sono molto simili, ma si differenziano nei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-1287">Indexers and properties are very similar in concept, but differ in the following ways:</span></span>

*  <span data-ttu-id="467ae-1288">Una proprietà viene identificata in base al nome, mentre un indicizzatore viene identificato dalla firma.</span><span class="sxs-lookup"><span data-stu-id="467ae-1288">A property is identified by its name, whereas an indexer is identified by its signature.</span></span>
*  <span data-ttu-id="467ae-1289">È possibile accedere a una proprietà tramite un *simple_name* ([nomi semplici](expressions.md#simple-names)) o un *member_access* ([accesso ai membri](expressions.md#member-access)), mentre un elemento indicizzatore è accessibile tramite un *element_access* ([accesso all'indicizzatore](expressions.md#indexer-access)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1289">A property is accessed through a *simple_name* ([Simple names](expressions.md#simple-names)) or a *member_access* ([Member access](expressions.md#member-access)), whereas an indexer element is accessed through an *element_access* ([Indexer access](expressions.md#indexer-access)).</span></span>
*  <span data-ttu-id="467ae-1290">Una proprietà può essere un `static` membro, mentre un indicizzatore è sempre un membro di istanza.</span><span class="sxs-lookup"><span data-stu-id="467ae-1290">A property can be a `static` member, whereas an indexer is always an instance member.</span></span>
*  <span data-ttu-id="467ae-1291">Una `get` funzione di accesso di una proprietà corrisponde a un metodo senza parametri, mentre `get` una funzione di accesso di un indicizzatore corrisponde a un metodo con lo stesso elenco di parametri formali dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1291">A `get` accessor of a property corresponds to a method with no parameters, whereas a `get` accessor of an indexer corresponds to a method with the same formal parameter list as the indexer.</span></span>
*  <span data-ttu-id="467ae-1292">Una `set` funzione di accesso di una proprietà corrisponde a un metodo con un solo `value`parametro denominato, `set` mentre una funzione di accesso di un indicizzatore corrisponde a un metodo con lo stesso elenco di parametri formali dell'indicizzatore, oltre a un parametro aggiuntivo. nome `value`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1292">A `set` accessor of a property corresponds to a method with a single parameter named `value`, whereas a `set` accessor of an indexer corresponds to a method with the same formal parameter list as the indexer, plus an additional parameter named `value`.</span></span>
*  <span data-ttu-id="467ae-1293">Si tratta di un errore in fase di compilazione che consente a una funzione di accesso dell'indicizzatore di dichiarare una variabile locale con lo stesso nome di un parametro dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1293">It is a compile-time error for an indexer accessor to declare a local variable with the same name as an indexer parameter.</span></span>
*  <span data-ttu-id="467ae-1294">In una dichiarazione di proprietà che esegue l'override, viene eseguito l'accesso alla `base.P`proprietà ereditata utilizzando la sintassi, dove `P` è il nome della proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1294">In an overriding property declaration, the inherited property is accessed using the syntax `base.P`, where `P` is the property name.</span></span> <span data-ttu-id="467ae-1295">In una dichiarazione di indicizzatore che esegue l'override, viene eseguito l'accesso all'indicizzatore ereditato `E` usando la sintassi `base[E]`, dove è un elenco di espressioni separate da virgole.</span><span class="sxs-lookup"><span data-stu-id="467ae-1295">In an overriding indexer declaration, the inherited indexer is accessed using the syntax `base[E]`, where `E` is a comma separated list of expressions.</span></span>
*  <span data-ttu-id="467ae-1296">Non esiste alcun concetto di "indicizzatore implementato automaticamente".</span><span class="sxs-lookup"><span data-stu-id="467ae-1296">There is no concept of an "automatically implemented indexer".</span></span> <span data-ttu-id="467ae-1297">È un errore avere un indicizzatore non astratto e non esterno con funzioni di accesso punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="467ae-1297">It is an error to have a non-abstract, non-external indexer with semicolon accessors.</span></span>

<span data-ttu-id="467ae-1298">A parte queste differenze, tutte le regole definite nelle [funzioni di accesso](classes.md#accessors) e le [proprietà implementate automaticamente](classes.md#automatically-implemented-properties) si applicano alle funzioni di accesso dell'indicizzatore, oltre che alle funzioni di accesso alle proprietà.</span><span class="sxs-lookup"><span data-stu-id="467ae-1298">Aside from these differences, all rules defined in [Accessors](classes.md#accessors) and [Automatically implemented properties](classes.md#automatically-implemented-properties) apply to indexer accessors as well as to property accessors.</span></span>

<span data-ttu-id="467ae-1299">Quando una dichiarazione di indicizzatore include `extern` un modificatore, l'indicizzatore viene definito ***indicizzatore esterno***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1299">When an indexer declaration includes an `extern` modifier, the indexer is said to be an ***external indexer***.</span></span> <span data-ttu-id="467ae-1300">Poiché una dichiarazione di indicizzatore esterno non fornisce alcuna implementazione effettiva, ogni *accessor_declarations* è costituito da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="467ae-1300">Because an external indexer declaration provides no actual implementation, each of its *accessor_declarations* consists of a semicolon.</span></span>

<span data-ttu-id="467ae-1301">Nell'esempio seguente viene dichiarata una `BitArray` classe che implementa un indicizzatore per accedere ai singoli bit nella matrice di bit.</span><span class="sxs-lookup"><span data-stu-id="467ae-1301">The example below declares a `BitArray` class that implements an indexer for accessing the individual bits in the bit array.</span></span>
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

<span data-ttu-id="467ae-1302">Un'istanza della `BitArray` classe utilizza sostanzialmente meno memoria rispetto a un oggetto corrispondente `bool[]` (poiché ogni valore del primo occupa un solo bit anziché un solo byte di quest'ultimo), ma consente le stesse operazioni di un oggetto `bool[]`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1302">An instance of the `BitArray` class consumes substantially less memory than a corresponding `bool[]` (since each value of the former occupies only one bit instead of the latter's one byte), but it permits the same operations as a `bool[]`.</span></span>

<span data-ttu-id="467ae-1303">La classe `CountPrimes` seguente usa un `BitArray` e l'algoritmo classico "sieve" per calcolare il numero di numeri primi tra 1 e un valore massimo specificato:</span><span class="sxs-lookup"><span data-stu-id="467ae-1303">The following `CountPrimes` class uses a `BitArray` and the classical "sieve" algorithm to compute the number of primes between 1 and a given maximum:</span></span>
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

<span data-ttu-id="467ae-1304">Si noti che la sintassi per l'accesso agli elementi `BitArray` di è esattamente identica a quella di `bool[]`un oggetto.</span><span class="sxs-lookup"><span data-stu-id="467ae-1304">Note that the syntax for accessing elements of the `BitArray` is precisely the same as for a `bool[]`.</span></span>

<span data-ttu-id="467ae-1305">Nell'esempio seguente viene illustrata una classe della griglia di 26 \* 10 con un indicizzatore con due parametri.</span><span class="sxs-lookup"><span data-stu-id="467ae-1305">The following example shows a 26 \* 10 grid class that has an indexer with two parameters.</span></span> <span data-ttu-id="467ae-1306">Il primo parametro deve essere una lettera maiuscola o minuscola nell'intervallo A-Z e il secondo deve essere un numero intero compreso nell'intervallo 0-9.</span><span class="sxs-lookup"><span data-stu-id="467ae-1306">The first parameter is required to be an upper- or lowercase letter in the range A-Z, and the second is required to be an integer in the range 0-9.</span></span>

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

### <a name="indexer-overloading"></a><span data-ttu-id="467ae-1307">Overload dell'indicizzatore</span><span class="sxs-lookup"><span data-stu-id="467ae-1307">Indexer overloading</span></span>

<span data-ttu-id="467ae-1308">Le regole di risoluzione dell'overload dell'indicizzatore sono descritte in [inferenza del tipo](expressions.md#type-inference).</span><span class="sxs-lookup"><span data-stu-id="467ae-1308">The indexer overload resolution rules are described in [Type inference](expressions.md#type-inference).</span></span>

## <a name="operators"></a><span data-ttu-id="467ae-1309">Operatori</span><span class="sxs-lookup"><span data-stu-id="467ae-1309">Operators</span></span>

<span data-ttu-id="467ae-1310">Un ***operatore*** è un membro che definisce il significato di un operatore Expression che può essere applicato alle istanze della classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1310">An ***operator*** is a member that defines the meaning of an expression operator that can be applied to instances of the class.</span></span> <span data-ttu-id="467ae-1311">Gli operatori vengono dichiarati usando *operator_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="467ae-1311">Operators are declared using *operator_declaration*s:</span></span>

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

<span data-ttu-id="467ae-1312">Sono disponibili tre categorie di operatori che eseguono l'overload: Operatori unari ([operatori unari](classes.md#unary-operators)), operatori binari ([operatori binari](classes.md#binary-operators)) e operatori di conversione ([operatori di conversione](classes.md#conversion-operators)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1312">There are three categories of overloadable operators: Unary operators ([Unary operators](classes.md#unary-operators)), binary operators ([Binary operators](classes.md#binary-operators)), and conversion operators ([Conversion operators](classes.md#conversion-operators)).</span></span>

<span data-ttu-id="467ae-1313">*Operator_body* è un punto e virgola, un ***corpo dell'istruzione*** o un corpo dell' ***espressione***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1313">The *operator_body* is either a semicolon, a ***statement body*** or an ***expression body***.</span></span> <span data-ttu-id="467ae-1314">Il corpo di un'istruzione è costituito da un *blocco*, che specifica le istruzioni da eseguire quando viene richiamato l'operatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1314">A statement body consists of a *block*, which specifies the statements to execute when the operator is invoked.</span></span> <span data-ttu-id="467ae-1315">Il *blocco* deve essere conforme alle regole per i metodi che restituiscono valori descritti nel [corpo del metodo](classes.md#method-body).</span><span class="sxs-lookup"><span data-stu-id="467ae-1315">The *block* must conform to the rules for value-returning methods described in [Method body](classes.md#method-body).</span></span> <span data-ttu-id="467ae-1316">Il corpo di `=>` un'espressione è costituito da seguito da un'espressione e da un punto e virgola e indica una singola espressione da eseguire quando viene richiamato l'operatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1316">An expression body consists of `=>` followed by an expression and a semicolon, and denotes a single expression to perform when the operator is invoked.</span></span>

<span data-ttu-id="467ae-1317">Per gli operatori `extern`, il *operator_body* è costituito semplicemente da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="467ae-1317">For `extern` operators, the *operator_body* consists simply of a semicolon.</span></span> <span data-ttu-id="467ae-1318">Per tutti gli altri operatori, *operator_body* è un corpo del blocco o un corpo dell'espressione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1318">For all other operators, the *operator_body* is either a block body or an expression body.</span></span>

<span data-ttu-id="467ae-1319">Le regole seguenti si applicano a tutte le dichiarazioni di operatore:</span><span class="sxs-lookup"><span data-stu-id="467ae-1319">The following rules apply to all operator declarations:</span></span>

*  <span data-ttu-id="467ae-1320">Una dichiarazione di operatore deve includere sia `public` un modificatore che un `static` modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1320">An operator declaration must include both a `public` and a `static` modifier.</span></span>
*  <span data-ttu-id="467ae-1321">Il parametro o i parametri di un operatore devono essere parametri valore ([parametri valore](variables.md#value-parameters)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1321">The parameter(s) of an operator must be value parameters ([Value parameters](variables.md#value-parameters)).</span></span> <span data-ttu-id="467ae-1322">Si tratta di un errore in fase di compilazione per una dichiarazione di `ref` operatore `out` per specificare i parametri o.</span><span class="sxs-lookup"><span data-stu-id="467ae-1322">It is a compile-time error for an operator declaration to specify `ref` or `out` parameters.</span></span>
*  <span data-ttu-id="467ae-1323">La firma di un operatore ([operatori unari](classes.md#unary-operators), operatori [binari](classes.md#binary-operators), [operatori di conversione](classes.md#conversion-operators)) deve differire dalle firme di tutti gli altri operatori dichiarati nella stessa classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1323">The signature of an operator ([Unary operators](classes.md#unary-operators), [Binary operators](classes.md#binary-operators), [Conversion operators](classes.md#conversion-operators)) must differ from the signatures of all other operators declared in the same class.</span></span>
*  <span data-ttu-id="467ae-1324">Tutti i tipi a cui viene fatto riferimento in una dichiarazione di operatore devono essere accessibili almeno quanto l'operatore stesso ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1324">All types referenced in an operator declaration must be at least as accessible as the operator itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>
*  <span data-ttu-id="467ae-1325">È un errore che lo stesso modificatore venga visualizzato più volte in una dichiarazione di operatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1325">It is an error for the same modifier to appear multiple times in an operator declaration.</span></span>

<span data-ttu-id="467ae-1326">Ogni categoria di operatori impone restrizioni aggiuntive, come descritto nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="467ae-1326">Each operator category imposes additional restrictions, as described in the following sections.</span></span>

<span data-ttu-id="467ae-1327">Come gli altri membri, gli operatori dichiarati in una classe di base vengono ereditati dalle classi derivate.</span><span class="sxs-lookup"><span data-stu-id="467ae-1327">Like other members, operators declared in a base class are inherited by derived classes.</span></span> <span data-ttu-id="467ae-1328">Poiché le dichiarazioni di operatore richiedono sempre la classe o lo struct in cui l'operatore viene dichiarato per partecipare alla firma dell'operatore, non è possibile che un operatore dichiarato in una classe derivata nasconda un operatore dichiarato in una classe di base.</span><span class="sxs-lookup"><span data-stu-id="467ae-1328">Because operator declarations always require the class or struct in which the operator is declared to participate in the signature of the operator, it is not possible for an operator declared in a derived class to hide an operator declared in a base class.</span></span> <span data-ttu-id="467ae-1329">Pertanto, il `new` modificatore non è mai necessario e quindi mai consentito in una dichiarazione di operatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1329">Thus, the `new` modifier is never required, and therefore never permitted, in an operator declaration.</span></span>

<span data-ttu-id="467ae-1330">Ulteriori informazioni sugli operatori unari e binari sono reperibili negli [operatori](expressions.md#operators).</span><span class="sxs-lookup"><span data-stu-id="467ae-1330">Additional information on unary and binary operators can be found in [Operators](expressions.md#operators).</span></span>

<span data-ttu-id="467ae-1331">Altre informazioni sugli operatori di conversione sono disponibili nelle [conversioni definite dall'utente](conversions.md#user-defined-conversions).</span><span class="sxs-lookup"><span data-stu-id="467ae-1331">Additional information on conversion operators can be found in [User-defined conversions](conversions.md#user-defined-conversions).</span></span>

### <a name="unary-operators"></a><span data-ttu-id="467ae-1332">Operatori unari</span><span class="sxs-lookup"><span data-stu-id="467ae-1332">Unary operators</span></span>

<span data-ttu-id="467ae-1333">Le regole seguenti si applicano alle dichiarazioni dell'operatore unario, dove `T` indica il tipo di istanza della classe o dello struct che contiene la dichiarazione dell'operatore:</span><span class="sxs-lookup"><span data-stu-id="467ae-1333">The following rules apply to unary operator declarations, where `T` denotes the instance type of the class or struct that contains the operator declaration:</span></span>

*  <span data-ttu-id="467ae-1334">Un operatore `+` `-`unario `~` `T` ,, o deve prendere un solo parametro di tipo o `T?` e può restituire qualsiasi tipo. `!`</span><span class="sxs-lookup"><span data-stu-id="467ae-1334">A unary `+`, `-`, `!`, or `~` operator must take a single parameter of type `T` or `T?` and can return any type.</span></span>
*  <span data-ttu-id="467ae-1335">Un operatore `++` unario o `--` deve prendere un solo parametro di `T` tipo `T?` o e deve restituire lo stesso tipo o un tipo derivato da esso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1335">A unary `++` or `--` operator must take a single parameter of type `T` or `T?` and must return that same type or a type derived from it.</span></span>
*  <span data-ttu-id="467ae-1336">Un operatore `true` unario o `false` deve prendere un solo parametro di `T` tipo `T?` o e deve restituire `bool`il tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1336">A unary `true` or `false` operator must take a single parameter of type `T` or `T?` and must return type `bool`.</span></span>

<span data-ttu-id="467ae-1337">La firma di un operatore unario è costituita dal token`+`dell' `-`operatore `!`(, `++`, `--`, `true` `~`,, `false`, o) e dal tipo del singolo parametro formale.</span><span class="sxs-lookup"><span data-stu-id="467ae-1337">The signature of a unary operator consists of the operator token (`+`, `-`, `!`, `~`, `++`, `--`, `true`, or `false`) and the type of the single formal parameter.</span></span> <span data-ttu-id="467ae-1338">Il tipo restituito non fa parte della firma di un operatore unario, né è il nome del parametro formale.</span><span class="sxs-lookup"><span data-stu-id="467ae-1338">The return type is not part of a unary operator's signature, nor is the name of the formal parameter.</span></span>

<span data-ttu-id="467ae-1339">Gli `true` operatori `false` unari e richiedono la dichiarazione di coppie.</span><span class="sxs-lookup"><span data-stu-id="467ae-1339">The `true` and `false` unary operators require pair-wise declaration.</span></span> <span data-ttu-id="467ae-1340">Si verifica un errore in fase di compilazione se una classe dichiara uno di questi operatori senza dichiarare anche l'altro.</span><span class="sxs-lookup"><span data-stu-id="467ae-1340">A compile-time error occurs if a class declares one of these operators without also declaring the other.</span></span> <span data-ttu-id="467ae-1341">Gli `true` operatori `false` e sono descritti ulteriormente negli [operatori logici condizionali definiti dall'utente](expressions.md#user-defined-conditional-logical-operators) e nelle [espressioni booleane](expressions.md#boolean-expressions).</span><span class="sxs-lookup"><span data-stu-id="467ae-1341">The `true` and `false` operators are described further in [User-defined conditional logical operators](expressions.md#user-defined-conditional-logical-operators) and [Boolean expressions](expressions.md#boolean-expressions).</span></span>

<span data-ttu-id="467ae-1342">Nell'esempio seguente viene illustrata un'implementazione e l' `operator ++` utilizzo successivo di per una classe Vector di tipo Integer:</span><span class="sxs-lookup"><span data-stu-id="467ae-1342">The following example shows an implementation and subsequent usage of `operator ++` for an integer vector class:</span></span>
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

<span data-ttu-id="467ae-1343">Si noti il modo in cui il metodo operator restituisce il valore prodotto aggiungendo 1 all'operando, esattamente come gli operatori di incremento e decremento suffisso ([operatori di incremento e decremento suffisso](expressions.md#postfix-increment-and-decrement-operators)) e gli operatori di incremento e decremento del prefisso ([prefisso operatori di incremento e decremento](expressions.md#prefix-increment-and-decrement-operators)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1343">Note how the operator method returns the value produced by adding 1 to the operand, just like the  postfix increment and decrement operators ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators)), and the prefix increment and decrement operators ([Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)).</span></span> <span data-ttu-id="467ae-1344">Diversamente da C++, questo metodo non deve modificare direttamente il valore del relativo operando.</span><span class="sxs-lookup"><span data-stu-id="467ae-1344">Unlike in C++, this method need not modify the value of its operand directly.</span></span> <span data-ttu-id="467ae-1345">Infatti, la modifica del valore dell'operando violerebbe la semantica standard dell'operatore di incremento suffisso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1345">In fact, modifying the operand value would violate the standard semantics of the postfix increment operator.</span></span>

### <a name="binary-operators"></a><span data-ttu-id="467ae-1346">Operatori binari</span><span class="sxs-lookup"><span data-stu-id="467ae-1346">Binary operators</span></span>

<span data-ttu-id="467ae-1347">Le regole seguenti si applicano alle dichiarazioni di operatori binari `T` , dove indica il tipo di istanza della classe o dello struct che contiene la dichiarazione dell'operatore:</span><span class="sxs-lookup"><span data-stu-id="467ae-1347">The following rules apply to binary operator declarations, where `T` denotes the instance type of the class or struct that contains the operator declaration:</span></span>

*  <span data-ttu-id="467ae-1348">Un operatore non di spostamento binario deve prendere due parametri, almeno uno dei quali deve avere il tipo `T` o `T?`e può restituire qualsiasi tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1348">A binary non-shift operator must take two parameters, at least one of which must have type `T` or `T?`, and can return any type.</span></span>
*  <span data-ttu-id="467ae-1349">Un operatore `<<` o `>>` binario deve assumere due parametri, il primo dei quali deve avere il `T` tipo `T?` o e il secondo di cui deve avere `int` il `int?`tipo o e può restituire qualsiasi tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1349">A binary `<<` or `>>` operator must take two parameters, the first of which must have type `T` or `T?` and the second of which must have type `int` or `int?`, and can return any type.</span></span>

<span data-ttu-id="467ae-1350">La firma di un operatore binario è costituita dal token dell'`+`operatore `-`( `*`, `/`, `%`, `&`, `|`, `^`, `<<`, `>>`,,, `==`, ,,`>`, o`>=`) eitipideidueparametriformali`<=`. `!=` `<`</span><span class="sxs-lookup"><span data-stu-id="467ae-1350">The signature of a binary operator consists of the operator token (`+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `<<`, `>>`, `==`, `!=`, `>`, `<`, `>=`, or `<=`) and the types of the two formal parameters.</span></span> <span data-ttu-id="467ae-1351">Il tipo restituito e i nomi dei parametri formali non fanno parte della firma di un operatore binario.</span><span class="sxs-lookup"><span data-stu-id="467ae-1351">The return type and the names of the formal parameters are not part of a binary operator's signature.</span></span>

<span data-ttu-id="467ae-1352">Per alcuni operatori binari è richiesta la dichiarazione di coppie.</span><span class="sxs-lookup"><span data-stu-id="467ae-1352">Certain binary operators require pair-wise declaration.</span></span> <span data-ttu-id="467ae-1353">Per ogni dichiarazione di uno degli operatori di una coppia, è necessario che sia presente una dichiarazione corrispondente dell'altro operatore della coppia.</span><span class="sxs-lookup"><span data-stu-id="467ae-1353">For every declaration of either operator of a pair, there must be a matching declaration of the other operator of the pair.</span></span> <span data-ttu-id="467ae-1354">Due dichiarazioni di operatore corrispondono quando hanno lo stesso tipo restituito e lo stesso tipo per ogni parametro.</span><span class="sxs-lookup"><span data-stu-id="467ae-1354">Two operator declarations match when they have the same return type and the same type for each parameter.</span></span> <span data-ttu-id="467ae-1355">Per gli operatori seguenti è necessaria la dichiarazione Pair-Wise:</span><span class="sxs-lookup"><span data-stu-id="467ae-1355">The following operators require pair-wise declaration:</span></span>

*  <span data-ttu-id="467ae-1356">`operator ==` e `operator !=`</span><span class="sxs-lookup"><span data-stu-id="467ae-1356">`operator ==` and `operator !=`</span></span>
*  <span data-ttu-id="467ae-1357">`operator >` e `operator <`</span><span class="sxs-lookup"><span data-stu-id="467ae-1357">`operator >` and `operator <`</span></span>
*  <span data-ttu-id="467ae-1358">`operator >=` e `operator <=`</span><span class="sxs-lookup"><span data-stu-id="467ae-1358">`operator >=` and `operator <=`</span></span>

### <a name="conversion-operators"></a><span data-ttu-id="467ae-1359">Operatori di conversione</span><span class="sxs-lookup"><span data-stu-id="467ae-1359">Conversion operators</span></span>

<span data-ttu-id="467ae-1360">Una dichiarazione dell'operatore di conversione introduce una ***conversione definita dall'utente*** ([conversioni definite dall'utente](conversions.md#user-defined-conversions)) che aumenta le conversioni implicite ed esplicite predefinite.</span><span class="sxs-lookup"><span data-stu-id="467ae-1360">A conversion operator declaration introduces a ***user-defined conversion*** ([User-defined conversions](conversions.md#user-defined-conversions)) which augments the pre-defined implicit and explicit conversions.</span></span>

<span data-ttu-id="467ae-1361">Una dichiarazione dell'operatore di conversione che `implicit` include la parola chiave introduce una conversione implicita definita dall'utente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1361">A conversion operator declaration that includes the `implicit` keyword introduces a user-defined implicit conversion.</span></span> <span data-ttu-id="467ae-1362">Le conversioni implicite possono verificarsi in varie situazioni, ad esempio chiamate di membri di funzioni, espressioni cast e assegnazioni.</span><span class="sxs-lookup"><span data-stu-id="467ae-1362">Implicit conversions can occur in a variety of situations, including function member invocations, cast expressions, and assignments.</span></span> <span data-ttu-id="467ae-1363">Questa operazione viene descritta ulteriormente nelle [conversioni implicite](conversions.md#implicit-conversions).</span><span class="sxs-lookup"><span data-stu-id="467ae-1363">This is described further in [Implicit conversions](conversions.md#implicit-conversions).</span></span>

<span data-ttu-id="467ae-1364">Una dichiarazione dell'operatore di conversione che `explicit` include la parola chiave introduce una conversione esplicita definita dall'utente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1364">A conversion operator declaration that includes the `explicit` keyword introduces a user-defined explicit conversion.</span></span> <span data-ttu-id="467ae-1365">Le conversioni esplicite possono verificarsi nelle espressioni cast e sono descritte ulteriormente nelle [conversioni esplicite](conversions.md#explicit-conversions).</span><span class="sxs-lookup"><span data-stu-id="467ae-1365">Explicit conversions can occur in cast expressions, and are described further in [Explicit conversions](conversions.md#explicit-conversions).</span></span>

<span data-ttu-id="467ae-1366">Un operatore di conversione converte da un tipo di origine, indicato dal tipo di parametro dell'operatore di conversione, a un tipo di destinazione, indicato dal tipo restituito dell'operatore di conversione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1366">A conversion operator converts from a source type, indicated by the parameter type of the conversion operator, to a target type, indicated by the return type of the conversion operator.</span></span>

<span data-ttu-id="467ae-1367">Per un `S` tipo di origine e un tipo `T`di destinazione specificati `T` , se `S` o sono tipi `S0` Nullable `T0` , consentire e fare riferimento ai relativi `S0` tipi `T0` sottostanti; in caso contrario, e sono uguale a `S` e `T` rispettivamente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1367">For a given source type `S` and target type `T`, if `S` or `T` are nullable types, let `S0` and `T0` refer to their underlying types, otherwise `S0` and `T0` are equal to `S` and `T` respectively.</span></span> <span data-ttu-id="467ae-1368">Una classe o uno struct è autorizzato a dichiarare una conversione da un tipo `S` di origine a un `T` tipo di destinazione solo se si verificano tutte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-1368">A class or struct is permitted to declare a conversion from a source type `S` to a target type `T` only if all of the following are true:</span></span>

*  <span data-ttu-id="467ae-1369">`S0`e `T0` sono tipi diversi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1369">`S0` and `T0` are different types.</span></span>
*  <span data-ttu-id="467ae-1370">`S0` O`T0` è il tipo di classe o struct in cui si verifica la dichiarazione di operatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1370">Either `S0` or `T0` is the class or struct type in which the operator declaration takes place.</span></span>
*  <span data-ttu-id="467ae-1371">Né `S0` né `T0` è un *INTERFACE_TYPE*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1371">Neither `S0` nor `T0` is an *interface_type*.</span></span>
*  <span data-ttu-id="467ae-1372">Escludendo le conversioni definite dall'utente, non esiste una conversione da `S` a `T` o da `T` a `S`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1372">Excluding user-defined conversions, a conversion does not exist from `S` to `T` or from `T` to `S`.</span></span>

<span data-ttu-id="467ae-1373">Ai fini di queste regole, qualsiasi parametro di tipo associato a `S` o `T` viene considerato tipi univoci che non hanno una relazione di ereditarietà con altri tipi e tutti i vincoli sui parametri di tipo vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="467ae-1373">For the purposes of these rules, any type parameters associated with `S` or `T` are considered to be unique types that have no inheritance relationship with other types, and any constraints on those type parameters are ignored.</span></span>

<span data-ttu-id="467ae-1374">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1374">In the example</span></span>
```csharp
class C<T> {...}

class D<T>: C<T>
{
    public static implicit operator C<int>(D<T> value) {...}      // Ok
    public static implicit operator C<string>(D<T> value) {...}   // Ok
    public static implicit operator C<T>(D<T> value) {...}        // Error
}
```
<span data-ttu-id="467ae-1375">le prime due dichiarazioni di operatore sono consentite perché, a scopo di [indicizzatori](classes.md#indexers),3 `T` , `int` e `string` e rispettivamente sono considerati tipi univoci senza relazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1375">the first two operator declarations are permitted because, for the purposes of [Indexers](classes.md#indexers).3, `T` and `int` and `string` respectively are considered unique types with no relationship.</span></span> <span data-ttu-id="467ae-1376">Tuttavia, il terzo operatore è un errore perché `C<T>` è la classe di base `D<T>`di.</span><span class="sxs-lookup"><span data-stu-id="467ae-1376">However, the third operator is an error because `C<T>` is the base class of `D<T>`.</span></span>

<span data-ttu-id="467ae-1377">Dalla seconda regola segue che un operatore di conversione deve eseguire la conversione da o verso il tipo di classe o struct in cui viene dichiarato l'operatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1377">From the second rule it follows that a conversion operator must convert either to or from the class or struct type in which the operator is declared.</span></span> <span data-ttu-id="467ae-1378">È possibile, ad esempio, che una classe o un tipo `C` struct definisca una `C` conversione `int` da a `int` e `C`da a, ma `int` non `bool`da a.</span><span class="sxs-lookup"><span data-stu-id="467ae-1378">For example, it is possible for a class or struct type `C` to define a conversion from `C` to `int` and from `int` to `C`, but not from `int` to `bool`.</span></span>

<span data-ttu-id="467ae-1379">Non è possibile ridefinire direttamente una conversione predefinita.</span><span class="sxs-lookup"><span data-stu-id="467ae-1379">It is not possible to directly redefine a pre-defined conversion.</span></span> <span data-ttu-id="467ae-1380">Pertanto, gli operatori di conversione non sono autorizzati a eseguire la `object` conversione da o a perché le conversioni implicite `object` ed esplicite esistono già tra e tutti gli altri tipi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1380">Thus, conversion operators are not allowed to convert from or to `object` because implicit and explicit conversions already exist between `object` and all other types.</span></span> <span data-ttu-id="467ae-1381">Analogamente, né l'origine né i tipi di destinazione di una conversione possono essere un tipo di base dell'altro, dal momento che una conversione sarebbe già esistente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1381">Likewise, neither the source nor the target types of a conversion can be a base type of the other, since a conversion would then already exist.</span></span>

<span data-ttu-id="467ae-1382">Tuttavia, è possibile dichiarare gli operatori sui tipi generici che, per determinati argomenti di tipo, specificano le conversioni già esistenti come conversioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="467ae-1382">However, it is possible to declare operators on generic types that, for particular type arguments, specify conversions that already exist as pre-defined conversions.</span></span> <span data-ttu-id="467ae-1383">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1383">In the example</span></span>
```csharp
struct Convertible<T>
{
    public static implicit operator Convertible<T>(T value) {...}
    public static explicit operator T(Convertible<T> value) {...}
}
```
<span data-ttu-id="467ae-1384">Quando il `object` tipo viene specificato come argomento di tipo `T`per, il secondo operatore dichiara una conversione già esistente (la conversione implicita e, di conseguenza, una conversione esplicita esiste da qualsiasi tipo `object`al tipo).</span><span class="sxs-lookup"><span data-stu-id="467ae-1384">when type `object` is specified as a type argument for `T`, the second operator declares a conversion that already exists (an implicit, and therefore also an explicit, conversion exists from any type to type `object`).</span></span>

<span data-ttu-id="467ae-1385">Nei casi in cui esiste una conversione predefinita tra due tipi, le conversioni definite dall'utente tra tali tipi vengono ignorate.</span><span class="sxs-lookup"><span data-stu-id="467ae-1385">In cases where a pre-defined conversion exists between two types, any user-defined conversions between those types are ignored.</span></span> <span data-ttu-id="467ae-1386">In particolare:</span><span class="sxs-lookup"><span data-stu-id="467ae-1386">Specifically:</span></span>

*  <span data-ttu-id="467ae-1387">Se esiste una conversione implicita predefinita ([conversioni implicite](conversions.md#implicit-conversions)) dal tipo `S` al tipo `T`, tutte le conversioni definite dall'utente (implicite o esplicite) `S` da `T` a vengono ignorate.</span><span class="sxs-lookup"><span data-stu-id="467ae-1387">If a pre-defined implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from type `S` to type `T`, all user-defined conversions (implicit or explicit) from `S` to `T` are ignored.</span></span>
*  <span data-ttu-id="467ae-1388">Se una conversione esplicita predefinita ([conversioni esplicite](conversions.md#explicit-conversions)) `S` esiste dal tipo al tipo `T`, le conversioni esplicite definite dall'utente da `S` a `T` vengono ignorate.</span><span class="sxs-lookup"><span data-stu-id="467ae-1388">If a pre-defined explicit conversion ([Explicit conversions](conversions.md#explicit-conversions)) exists from type `S` to type `T`, any user-defined explicit conversions from `S` to `T` are ignored.</span></span> <span data-ttu-id="467ae-1389">Inoltre</span><span class="sxs-lookup"><span data-stu-id="467ae-1389">Furthermore:</span></span>

<span data-ttu-id="467ae-1390">Se `T` è un tipo di interfaccia, le conversioni implicite definite dall' `S` utente `T` da a vengono ignorate.</span><span class="sxs-lookup"><span data-stu-id="467ae-1390">If `T` is an interface type, user-defined implicit conversions from `S` to `T` are ignored.</span></span>

<span data-ttu-id="467ae-1391">In caso contrario, le conversioni implicite definite `S` dall' `T` utente da a vengono ancora prese in considerazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1391">Otherwise, user-defined implicit conversions from `S` to `T` are still considered.</span></span>

<span data-ttu-id="467ae-1392">Per tutti i tipi `object`, ma gli operatori dichiarati `Convertible<T>` dal tipo precedente non sono in conflitto con le conversioni predefinite.</span><span class="sxs-lookup"><span data-stu-id="467ae-1392">For all types but `object`, the operators declared by the `Convertible<T>` type above do not conflict with pre-defined conversions.</span></span> <span data-ttu-id="467ae-1393">Esempio:</span><span class="sxs-lookup"><span data-stu-id="467ae-1393">For example:</span></span>
```csharp
void F(int i, Convertible<int> n) {
    i = n;                          // Error
    i = (int)n;                     // User-defined explicit conversion
    n = i;                          // User-defined implicit conversion
    n = (Convertible<int>)i;        // User-defined implicit conversion
}
```

<span data-ttu-id="467ae-1394">Per il tipo `object`, tuttavia, le conversioni predefinite nascondono le conversioni definite dall'utente in tutti i casi, ma uno:</span><span class="sxs-lookup"><span data-stu-id="467ae-1394">However, for type `object`, pre-defined conversions hide the user-defined conversions in all cases but one:</span></span>

```csharp
void F(object o, Convertible<object> n) {
    o = n;                         // Pre-defined boxing conversion
    o = (object)n;                 // Pre-defined boxing conversion
    n = o;                         // User-defined implicit conversion
    n = (Convertible<object>)o;    // Pre-defined unboxing conversion
}
```

<span data-ttu-id="467ae-1395">Per le conversioni definite dall'utente non è consentito eseguire la conversione da o a *INTERFACE_TYPE*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1395">User-defined conversions are not allowed to convert from or to *interface_type*s.</span></span> <span data-ttu-id="467ae-1396">In particolare, questa restrizione garantisce che non si verifichino trasformazioni definite dall'utente durante la conversione in un *INTERFACE_TYPE*e che una conversione in un *INTERFACE_TYPE* abbia esito positivo solo se l'oggetto in fase di conversione implementa il *INTERFACE_TYPE*specificato.</span><span class="sxs-lookup"><span data-stu-id="467ae-1396">In particular, this restriction ensures that no user-defined transformations occur when converting to an *interface_type*, and that a conversion to an *interface_type* succeeds only if the object being converted actually implements the specified *interface_type*.</span></span>

<span data-ttu-id="467ae-1397">La firma di un operatore di conversione è costituita dal tipo di origine e dal tipo di destinazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1397">The signature of a conversion operator consists of the source type and the target type.</span></span> <span data-ttu-id="467ae-1398">Si noti che questa è l'unica forma di membro per cui il tipo restituito fa parte della firma. La `implicit` classificazione `explicit` o di un operatore di conversione non fa parte della firma dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1398">(Note that this is the only form of member for which the return type participates in the signature.) The `implicit` or `explicit` classification of a conversion operator is not part of the operator's signature.</span></span> <span data-ttu-id="467ae-1399">Pertanto, una classe o uno struct non può dichiarare `implicit` sia un `explicit` operatore di conversione che con gli stessi tipi di origine e di destinazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1399">Thus, a class or struct cannot declare both an `implicit` and an `explicit` conversion operator with the same source and target types.</span></span>

<span data-ttu-id="467ae-1400">In generale, le conversioni implicite definite dall'utente devono essere progettate in modo da non generare mai eccezioni e non perdere mai le informazioni.</span><span class="sxs-lookup"><span data-stu-id="467ae-1400">In general, user-defined implicit conversions should be designed to never throw exceptions and never lose information.</span></span> <span data-ttu-id="467ae-1401">Se una conversione definita dall'utente può generare eccezioni (ad esempio, poiché l'argomento di origine non è compreso nell'intervallo) o la perdita di informazioni, ad esempio la rimozione di bit più significativi, tale conversione deve essere definita come conversione esplicita.</span><span class="sxs-lookup"><span data-stu-id="467ae-1401">If a user-defined conversion can give rise to exceptions (for example, because the source argument is out of range) or loss of information (such as discarding high-order bits), then that conversion should be defined as an explicit conversion.</span></span>

<span data-ttu-id="467ae-1402">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1402">In the example</span></span>
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
<span data-ttu-id="467ae-1403">la conversione da `Digit` a `byte` è implicita, in quanto non genera mai eccezioni o perde informazioni, ma `byte` la `Digit` conversione da a `Digit` è esplicita poiché può rappresentare solo un subset della possibile valori di un `byte`oggetto.</span><span class="sxs-lookup"><span data-stu-id="467ae-1403">the conversion from `Digit` to `byte` is implicit because it never throws exceptions or loses information, but the conversion from `byte` to `Digit` is explicit since `Digit` can only represent a subset of the possible values of a `byte`.</span></span>

## <a name="instance-constructors"></a><span data-ttu-id="467ae-1404">Costruttori di istanze</span><span class="sxs-lookup"><span data-stu-id="467ae-1404">Instance constructors</span></span>

<span data-ttu-id="467ae-1405">Un ***costruttore di istanza*** è un membro che implementa le azioni necessarie per inizializzare un'istanza di una classe,</span><span class="sxs-lookup"><span data-stu-id="467ae-1405">An ***instance constructor*** is a member that implements the actions required to initialize an instance of a class.</span></span> <span data-ttu-id="467ae-1406">I costruttori di istanza vengono dichiarati utilizzando *constructor_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="467ae-1406">Instance constructors are declared using *constructor_declaration*s:</span></span>

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

<span data-ttu-id="467ae-1407">Un *constructor_declaration* può includere un set di *attributi* ([attributi](attributes.md)), una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)) e un modificatore `extern` ([metodi esterni](classes.md#external-methods)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1407">A *constructor_declaration* may include a set of *attributes* ([Attributes](attributes.md)), a valid combination of the four access modifiers ([Access modifiers](classes.md#access-modifiers)), and an `extern` ([External methods](classes.md#external-methods)) modifier.</span></span> <span data-ttu-id="467ae-1408">Una dichiarazione del costruttore non può includere lo stesso modificatore più volte.</span><span class="sxs-lookup"><span data-stu-id="467ae-1408">A constructor declaration is not permitted to include the same modifier multiple times.</span></span>

<span data-ttu-id="467ae-1409">L' *identificatore* di un *constructor_declarator* deve denominare la classe in cui è dichiarato il costruttore di istanza.</span><span class="sxs-lookup"><span data-stu-id="467ae-1409">The *identifier* of a *constructor_declarator* must name the class in which the instance constructor is declared.</span></span> <span data-ttu-id="467ae-1410">Se viene specificato un altro nome, si verificherà un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1410">If any other name is specified, a compile-time error occurs.</span></span>

<span data-ttu-id="467ae-1411">Il *formal_parameter_list* facoltativo di un costruttore di istanza è soggetto alle stesse regole di *formal_parameter_list* di un metodo ([Metodi](classes.md#methods)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1411">The optional *formal_parameter_list* of an instance constructor is subject to the same rules as the *formal_parameter_list* of a method ([Methods](classes.md#methods)).</span></span> <span data-ttu-id="467ae-1412">L'elenco di parametri formali definisce la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) di un costruttore di istanza e regola il processo in base al quale la risoluzione dell'overload ([inferenza del tipo](expressions.md#type-inference)) seleziona un particolare costruttore di istanza in una chiamata.</span><span class="sxs-lookup"><span data-stu-id="467ae-1412">The formal parameter list defines the signature ([Signatures and overloading](basic-concepts.md#signatures-and-overloading)) of an instance constructor and governs the process whereby overload resolution ([Type inference](expressions.md#type-inference)) selects a particular instance constructor in an invocation.</span></span>

<span data-ttu-id="467ae-1413">Ognuno dei tipi a cui viene fatto riferimento in *formal_parameter_list* di un costruttore di istanza deve essere accessibile almeno quanto il costruttore stesso ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1413">Each of the types referenced in the *formal_parameter_list* of an instance constructor must be at least as accessible as the constructor itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span>

<span data-ttu-id="467ae-1414">Il *constructor_initializer* facoltativo specifica un altro costruttore di istanza da richiamare prima di eseguire le istruzioni fornite in *constructor_body* del costruttore di istanza.</span><span class="sxs-lookup"><span data-stu-id="467ae-1414">The optional *constructor_initializer* specifies another instance constructor to invoke before executing the statements given in the *constructor_body* of this instance constructor.</span></span> <span data-ttu-id="467ae-1415">Questa operazione viene descritta ulteriormente negli [inizializzatori del costruttore](classes.md#constructor-initializers).</span><span class="sxs-lookup"><span data-stu-id="467ae-1415">This is described further in [Constructor initializers](classes.md#constructor-initializers).</span></span>

<span data-ttu-id="467ae-1416">Quando una dichiarazione di costruttore include `extern` un modificatore, il costruttore viene definito ***Costruttore esterno***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1416">When a constructor declaration includes an `extern` modifier, the constructor is said to be an ***external constructor***.</span></span> <span data-ttu-id="467ae-1417">Poiché una dichiarazione del costruttore esterno non fornisce alcuna implementazione effettiva, la relativa *constructor_body* è costituita da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="467ae-1417">Because an external constructor declaration provides no actual implementation, its *constructor_body* consists of a semicolon.</span></span> <span data-ttu-id="467ae-1418">Per tutti gli altri costruttori, *constructor_body* è costituito da un *blocco* che specifica le istruzioni per inizializzare una nuova istanza della classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1418">For all other constructors, the *constructor_body* consists of a *block* which specifies the statements to initialize a new instance of the class.</span></span> <span data-ttu-id="467ae-1419">Corrisponde esattamente al *blocco* di un metodo di istanza con un `void` tipo restituito (corpo del[Metodo](classes.md#method-body)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1419">This corresponds exactly to the *block* of an instance method with a `void` return type ([Method body](classes.md#method-body)).</span></span>

<span data-ttu-id="467ae-1420">I costruttori di istanza non vengono ereditati.</span><span class="sxs-lookup"><span data-stu-id="467ae-1420">Instance constructors are not inherited.</span></span> <span data-ttu-id="467ae-1421">Pertanto, una classe non dispone di costruttori di istanza diversi da quelli effettivamente dichiarati nella classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1421">Thus, a class has no instance constructors other than those actually declared in the class.</span></span> <span data-ttu-id="467ae-1422">Se una classe non contiene dichiarazioni di costruttori di istanza, viene automaticamente fornito un costruttore di istanza predefinito ([costruttori predefiniti](classes.md#default-constructors)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1422">If a class contains no instance constructor declarations, a default instance constructor is automatically provided ([Default constructors](classes.md#default-constructors)).</span></span>

<span data-ttu-id="467ae-1423">I costruttori di istanza vengono richiamati da *object_creation_expression*s ([espressioni di creazione di oggetti](expressions.md#object-creation-expressions)) e tramite *constructor_initializer*s.</span><span class="sxs-lookup"><span data-stu-id="467ae-1423">Instance constructors are invoked by *object_creation_expression*s ([Object creation expressions](expressions.md#object-creation-expressions)) and through *constructor_initializer*s.</span></span>

### <a name="constructor-initializers"></a><span data-ttu-id="467ae-1424">Inizializzatori del costruttore</span><span class="sxs-lookup"><span data-stu-id="467ae-1424">Constructor initializers</span></span>

<span data-ttu-id="467ae-1425">Tutti i costruttori di istanza (ad eccezione di quelli per la classe `object`) includono implicitamente una chiamata a un altro costruttore di istanza immediatamente prima di *constructor_body*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1425">All instance constructors (except those for class `object`) implicitly include an invocation of another instance constructor immediately before the *constructor_body*.</span></span> <span data-ttu-id="467ae-1426">Il costruttore da richiamare in modo implicito è determinato da *constructor_initializer*:</span><span class="sxs-lookup"><span data-stu-id="467ae-1426">The constructor to implicitly invoke is determined by the *constructor_initializer*:</span></span>

*  <span data-ttu-id="467ae-1427">Un inizializzatore del costruttore di istanza `base(argument_list)` del `base()` form o causa la richiamata di un costruttore di istanza dalla classe base diretta.</span><span class="sxs-lookup"><span data-stu-id="467ae-1427">An instance constructor initializer of the form `base(argument_list)` or `base()` causes an instance constructor from the direct base class to be invoked.</span></span> <span data-ttu-id="467ae-1428">Il costruttore viene selezionato utilizzando *argument_list* se presente e le regole di risoluzione dell'overload della [risoluzione dell'overload](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="467ae-1428">That constructor is selected using *argument_list* if present and the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="467ae-1429">Il set di costruttori di istanza candidati è costituito da tutti i costruttori di istanza accessibili contenuti nella classe di base diretta o dal costruttore predefinito ([costruttori predefiniti](classes.md#default-constructors)), se non sono stati dichiarati costruttori di istanza nella classe di base diretta.</span><span class="sxs-lookup"><span data-stu-id="467ae-1429">The set of candidate instance constructors consists of all accessible instance constructors contained in the direct base class, or the default constructor ([Default constructors](classes.md#default-constructors)), if no instance constructors are declared in the direct base class.</span></span> <span data-ttu-id="467ae-1430">Se il set è vuoto o se non è possibile identificare un singolo costruttore di istanze migliore, si verificherà un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1430">If this set is empty, or if a single best instance constructor cannot be identified, a compile-time error occurs.</span></span>
*  <span data-ttu-id="467ae-1431">Un inizializzatore del costruttore di istanza `this(argument-list)` del `this()` form o causa la richiamata di un costruttore di istanza dalla classe stessa.</span><span class="sxs-lookup"><span data-stu-id="467ae-1431">An instance constructor initializer of the form `this(argument-list)` or `this()` causes an instance constructor from the class itself to be invoked.</span></span> <span data-ttu-id="467ae-1432">Il costruttore viene selezionato utilizzando *argument_list* se presente e le regole di risoluzione dell'overload della [risoluzione dell'overload](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="467ae-1432">The constructor is selected using *argument_list* if present and the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="467ae-1433">Il set di costruttori di istanza candidati è costituito da tutti i costruttori di istanza accessibili dichiarati nella classe stessa.</span><span class="sxs-lookup"><span data-stu-id="467ae-1433">The set of candidate instance constructors consists of all accessible instance constructors declared in the class itself.</span></span> <span data-ttu-id="467ae-1434">Se il set è vuoto o se non è possibile identificare un singolo costruttore di istanze migliore, si verificherà un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1434">If this set is empty, or if a single best instance constructor cannot be identified, a compile-time error occurs.</span></span> <span data-ttu-id="467ae-1435">Se una dichiarazione di un costruttore di istanza include un inizializzatore del costruttore che richiama il costruttore stesso, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1435">If an instance constructor declaration includes a constructor initializer that invokes the constructor itself, a compile-time error occurs.</span></span>

<span data-ttu-id="467ae-1436">Se un costruttore di istanza non dispone di un inizializzatore di costruttore, viene `base()` fornito implicitamente un inizializzatore del costruttore del modulo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1436">If an instance constructor has no constructor initializer, a constructor initializer of the form `base()` is implicitly provided.</span></span> <span data-ttu-id="467ae-1437">Quindi, una dichiarazione del costruttore di istanza del form</span><span class="sxs-lookup"><span data-stu-id="467ae-1437">Thus, an instance constructor declaration of the form</span></span>
```csharp
C(...) {...}
```
<span data-ttu-id="467ae-1438">è esattamente equivalente a</span><span class="sxs-lookup"><span data-stu-id="467ae-1438">is exactly equivalent to</span></span>
```csharp
C(...): base() {...}
```

<span data-ttu-id="467ae-1439">L'ambito dei parametri forniti dal *formal_parameter_list* di una dichiarazione del costruttore di istanza include l'inizializzatore del costruttore della dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1439">The scope of the parameters given by the *formal_parameter_list* of an instance constructor declaration includes the constructor initializer of that declaration.</span></span> <span data-ttu-id="467ae-1440">Un inizializzatore di costruttore è quindi autorizzato ad accedere ai parametri del costruttore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1440">Thus, a constructor initializer is permitted to access the parameters of the constructor.</span></span> <span data-ttu-id="467ae-1441">Esempio:</span><span class="sxs-lookup"><span data-stu-id="467ae-1441">For example:</span></span>
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

<span data-ttu-id="467ae-1442">Un inizializzatore di costruttore di istanza non può accedere all'istanza da creare.</span><span class="sxs-lookup"><span data-stu-id="467ae-1442">An instance constructor initializer cannot access the instance being created.</span></span> <span data-ttu-id="467ae-1443">Di conseguenza, si tratta di un errore in fase di compilazione per fare riferimento a `this` in un'espressione di argomento dell'inizializzatore del costruttore, in quanto si tratta di un errore in fase di compilazione per un'espressione di argomento che fa riferimento a qualsiasi membro di istanza tramite un *simple_name*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1443">Therefore it is a compile-time error to reference `this` in an argument expression of the constructor initializer, as is it a compile-time error for an argument expression to reference any instance member through a *simple_name*.</span></span>

### <a name="instance-variable-initializers"></a><span data-ttu-id="467ae-1444">Inizializzatori di variabile di istanza</span><span class="sxs-lookup"><span data-stu-id="467ae-1444">Instance variable initializers</span></span>

<span data-ttu-id="467ae-1445">Quando un costruttore di istanza non dispone di un inizializzatore di costruttore o un inizializzatore del costruttore nel formato `base(...)`, il costruttore esegue in modo implicito le inizializzazioni specificate da *variable_initializer*dei campi di istanza dichiarati in classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1445">When an instance constructor has no constructor initializer, or it has a constructor initializer of the form `base(...)`, that constructor implicitly performs the initializations specified by the *variable_initializer*s of the instance fields declared in its class.</span></span> <span data-ttu-id="467ae-1446">Corrisponde a una sequenza di assegnazioni che vengono eseguite immediatamente alla voce al costruttore e prima della chiamata implicita del costruttore della classe base diretta.</span><span class="sxs-lookup"><span data-stu-id="467ae-1446">This corresponds to a sequence of assignments that are executed immediately upon entry to the constructor and before the implicit invocation of the direct base class constructor.</span></span> <span data-ttu-id="467ae-1447">Gli inizializzatori di variabile vengono eseguiti nell'ordine testuale in cui vengono visualizzati nella dichiarazione della classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1447">The variable initializers are executed in the textual order in which they appear in the class declaration.</span></span>

### <a name="constructor-execution"></a><span data-ttu-id="467ae-1448">Esecuzione del costruttore</span><span class="sxs-lookup"><span data-stu-id="467ae-1448">Constructor execution</span></span>

<span data-ttu-id="467ae-1449">Gli inizializzatori di variabile vengono trasformati in istruzioni di assegnazione e queste istruzioni di assegnazione vengono eseguite prima della chiamata del costruttore di istanze della classe di base.</span><span class="sxs-lookup"><span data-stu-id="467ae-1449">Variable initializers are transformed into assignment statements, and these assignment statements are executed before the invocation of the base class instance constructor.</span></span> <span data-ttu-id="467ae-1450">Questo ordinamento garantisce che tutti i campi di istanza vengano inizializzati dai rispettivi inizializzatori di variabile prima che vengano eseguite istruzioni con accesso a tale istanza.</span><span class="sxs-lookup"><span data-stu-id="467ae-1450">This ordering ensures that all instance fields are initialized by their variable initializers before any statements that have access to that instance are executed.</span></span>

<span data-ttu-id="467ae-1451">Dato l'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1451">Given the example</span></span>
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
<span data-ttu-id="467ae-1452">Quando `new B()` viene utilizzato per creare un'istanza di `B`, viene prodotto l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="467ae-1452">when `new B()` is used to create an instance of `B`, the following output is produced:</span></span>
```console
x = 1, y = 0
```

<span data-ttu-id="467ae-1453">Il valore di `x` è 1 perché l'inizializzatore di variabile viene eseguito prima che venga richiamato il costruttore di istanza della classe base.</span><span class="sxs-lookup"><span data-stu-id="467ae-1453">The value of `x` is 1 because the variable initializer is executed before the base class instance constructor is invoked.</span></span> <span data-ttu-id="467ae-1454">Tuttavia, il valore di `y` è 0 (il valore predefinito di un `int`) perché l'assegnazione a `y` non viene eseguita finché il costruttore della classe base non restituisce.</span><span class="sxs-lookup"><span data-stu-id="467ae-1454">However, the value of `y` is 0 (the default value of an `int`) because the assignment to `y` is not executed until after the base class constructor returns.</span></span>

<span data-ttu-id="467ae-1455">È utile considerare gli inizializzatori di variabile di istanza e gli inizializzatori di costruttore come istruzioni inserite automaticamente prima di *constructor_body*.</span><span class="sxs-lookup"><span data-stu-id="467ae-1455">It is useful to think of instance variable initializers and constructor initializers as statements that are automatically inserted before the *constructor_body*.</span></span> <span data-ttu-id="467ae-1456">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1456">The example</span></span>
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
<span data-ttu-id="467ae-1457">contiene diversi inizializzatori di variabili. contiene anche inizializzatori di costruttori di entrambi i form`base` ( `this`e).</span><span class="sxs-lookup"><span data-stu-id="467ae-1457">contains several variable initializers; it also contains constructor initializers of both forms (`base` and `this`).</span></span> <span data-ttu-id="467ae-1458">L'esempio corrisponde al codice riportato di seguito, in cui ogni commento indica un'istruzione inserita automaticamente (la sintassi utilizzata per le chiamate al costruttore inserite automaticamente non è valida, ma serve semplicemente per illustrare il meccanismo).</span><span class="sxs-lookup"><span data-stu-id="467ae-1458">The example corresponds to the code shown below, where each comment indicates an automatically inserted statement (the syntax used for the automatically inserted constructor invocations isn't valid, but merely serves to illustrate the mechanism).</span></span>

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

### <a name="default-constructors"></a><span data-ttu-id="467ae-1459">Costruttori predefiniti</span><span class="sxs-lookup"><span data-stu-id="467ae-1459">Default constructors</span></span>

<span data-ttu-id="467ae-1460">Se una classe non contiene dichiarazioni di costruttori di istanza, viene automaticamente fornito un costruttore di istanza predefinito.</span><span class="sxs-lookup"><span data-stu-id="467ae-1460">If a class contains no instance constructor declarations, a default instance constructor is automatically provided.</span></span> <span data-ttu-id="467ae-1461">Il costruttore predefinito richiama semplicemente il costruttore senza parametri della classe base diretta.</span><span class="sxs-lookup"><span data-stu-id="467ae-1461">That default constructor simply invokes the parameterless constructor of the direct base class.</span></span> <span data-ttu-id="467ae-1462">Se la classe è astratta, l'accessibilità dichiarata per il costruttore predefinito è protetta.</span><span class="sxs-lookup"><span data-stu-id="467ae-1462">If the class is abstract then the declared accessibility for the default constructor is protected.</span></span> <span data-ttu-id="467ae-1463">In caso contrario, l'accessibilità dichiarata per il costruttore predefinito è public.</span><span class="sxs-lookup"><span data-stu-id="467ae-1463">Otherwise, the declared accessibility for the default constructor is public.</span></span> <span data-ttu-id="467ae-1464">Pertanto, il costruttore predefinito è sempre nel formato</span><span class="sxs-lookup"><span data-stu-id="467ae-1464">Thus, the default constructor is always of the form</span></span>

```csharp
protected C(): base() {}
```
<span data-ttu-id="467ae-1465">oppure</span><span class="sxs-lookup"><span data-stu-id="467ae-1465">or</span></span>
```csharp
public C(): base() {}
```
<span data-ttu-id="467ae-1466">dove `C` è il nome della classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1466">where `C` is the name of the class.</span></span> <span data-ttu-id="467ae-1467">Se la risoluzione dell'overload non è in grado di determinare un candidato univoco per l'inizializzatore del costruttore della classe base, si verificherà un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1467">If overload resolution is unable to determine a unique best candidate for the base class constructor initializer then a compile-time error occurs.</span></span>

<span data-ttu-id="467ae-1468">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1468">In the example</span></span>
```csharp
class Message
{
    object sender;
    string text;
}
```
<span data-ttu-id="467ae-1469">viene fornito un costruttore predefinito perché la classe non contiene dichiarazioni di costruttori di istanza.</span><span class="sxs-lookup"><span data-stu-id="467ae-1469">a default constructor is provided because the class contains no instance constructor declarations.</span></span> <span data-ttu-id="467ae-1470">Di conseguenza, l'esempio è esattamente equivalente a</span><span class="sxs-lookup"><span data-stu-id="467ae-1470">Thus, the example is precisely equivalent to</span></span>
```csharp
class Message
{
    object sender;
    string text;

    public Message(): base() {}
}
```

### <a name="private-constructors"></a><span data-ttu-id="467ae-1471">Costruttori privati</span><span class="sxs-lookup"><span data-stu-id="467ae-1471">Private constructors</span></span>

<span data-ttu-id="467ae-1472">Quando una classe `T` dichiara solo costruttori di istanza privati, non è possibile che le classi esterne al testo di programma di `T` derivano da `T` o per creare direttamente istanze di `T`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1472">When a class `T` declares only private instance constructors, it is not possible for classes outside the program text of `T` to derive from `T` or to directly create instances of `T`.</span></span> <span data-ttu-id="467ae-1473">Se pertanto una classe contiene solo membri statici e non è destinata alla creazione di un'istanza, l'aggiunta di un costruttore di istanza privata vuota impedirà la creazione di istanze.</span><span class="sxs-lookup"><span data-stu-id="467ae-1473">Thus, if a class contains only static members and isn't intended to be instantiated, adding an empty private instance constructor will prevent instantiation.</span></span> <span data-ttu-id="467ae-1474">Esempio:</span><span class="sxs-lookup"><span data-stu-id="467ae-1474">For example:</span></span>
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

<span data-ttu-id="467ae-1475">La `Trig` classe raggruppa i metodi e le costanti correlati, ma non deve essere creata un'istanza.</span><span class="sxs-lookup"><span data-stu-id="467ae-1475">The `Trig` class groups related methods and constants, but is not intended to be instantiated.</span></span> <span data-ttu-id="467ae-1476">Dichiara pertanto un singolo costruttore di istanza privata vuota.</span><span class="sxs-lookup"><span data-stu-id="467ae-1476">Therefore it declares a single empty private instance constructor.</span></span> <span data-ttu-id="467ae-1477">Per evitare la generazione automatica di un costruttore predefinito, è necessario dichiarare almeno un costruttore di istanze.</span><span class="sxs-lookup"><span data-stu-id="467ae-1477">At least one instance constructor must be declared to suppress the automatic generation of a default constructor.</span></span>

### <a name="optional-instance-constructor-parameters"></a><span data-ttu-id="467ae-1478">Parametri facoltativi del costruttore di istanza</span><span class="sxs-lookup"><span data-stu-id="467ae-1478">Optional instance constructor parameters</span></span>

<span data-ttu-id="467ae-1479">Il `this(...)` form dell'inizializzatore del costruttore viene comunemente usato in combinazione con l'overload per implementare parametri del costruttore di istanza facoltativi.</span><span class="sxs-lookup"><span data-stu-id="467ae-1479">The `this(...)` form of constructor initializer is commonly used in conjunction with overloading to implement optional instance constructor parameters.</span></span> <span data-ttu-id="467ae-1480">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1480">In the example</span></span>
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
<span data-ttu-id="467ae-1481">i primi due costruttori di istanza forniscono semplicemente i valori predefiniti per gli argomenti mancanti.</span><span class="sxs-lookup"><span data-stu-id="467ae-1481">the first two instance constructors merely provide the default values for the missing arguments.</span></span> <span data-ttu-id="467ae-1482">Entrambi usano un `this(...)` inizializzatore di costruttore per richiamare il terzo costruttore di istanza, che in realtà esegue l'inizializzazione della nuova istanza.</span><span class="sxs-lookup"><span data-stu-id="467ae-1482">Both use a `this(...)` constructor initializer to invoke the third instance constructor, which actually does the work of initializing the new instance.</span></span> <span data-ttu-id="467ae-1483">L'effetto è che i parametri facoltativi del costruttore:</span><span class="sxs-lookup"><span data-stu-id="467ae-1483">The effect is that of optional constructor parameters:</span></span>
```csharp
Text t1 = new Text();                    // Same as Text(0, 0, null)
Text t2 = new Text(5, 10);               // Same as Text(5, 10, null)
Text t3 = new Text(5, 20, "Hello");
```

## <a name="static-constructors"></a><span data-ttu-id="467ae-1484">Costruttori statici</span><span class="sxs-lookup"><span data-stu-id="467ae-1484">Static constructors</span></span>

<span data-ttu-id="467ae-1485">Un ***costruttore statico*** è un membro che implementa le azioni necessarie per inizializzare un tipo di classe closed.</span><span class="sxs-lookup"><span data-stu-id="467ae-1485">A ***static constructor*** is a member that implements the actions required to initialize a closed class type.</span></span> <span data-ttu-id="467ae-1486">I costruttori statici vengono dichiarati usando *static_constructor_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="467ae-1486">Static constructors are declared using *static_constructor_declaration*s:</span></span>

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

<span data-ttu-id="467ae-1487">Un *static_constructor_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e un modificatore `extern` ([metodi esterni](classes.md#external-methods)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1487">A *static_constructor_declaration* may include a set of *attributes* ([Attributes](attributes.md)) and an `extern` modifier ([External methods](classes.md#external-methods)).</span></span>

<span data-ttu-id="467ae-1488">L' *identificatore* di un *static_constructor_declaration* deve denominare la classe in cui è dichiarato il costruttore statico.</span><span class="sxs-lookup"><span data-stu-id="467ae-1488">The *identifier* of a *static_constructor_declaration* must name the class in which the static constructor is declared.</span></span> <span data-ttu-id="467ae-1489">Se viene specificato un altro nome, si verificherà un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1489">If any other name is specified, a compile-time error occurs.</span></span>

<span data-ttu-id="467ae-1490">Quando una dichiarazione di costruttore statico include `extern` un modificatore, il costruttore statico viene definito ***costruttore statico esterno***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1490">When a static constructor declaration includes an `extern` modifier, the static constructor is said to be an ***external static constructor***.</span></span> <span data-ttu-id="467ae-1491">Poiché la dichiarazione di un costruttore statico esterno non fornisce alcuna implementazione effettiva, la relativa *static_constructor_body* è costituita da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="467ae-1491">Because an external static constructor declaration provides no actual implementation, its *static_constructor_body* consists of a semicolon.</span></span> <span data-ttu-id="467ae-1492">Per tutte le altre dichiarazioni di costruttori statici, *static_constructor_body* è costituito da un *blocco* che specifica le istruzioni da eseguire per inizializzare la classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1492">For all other static constructor declarations, the *static_constructor_body* consists of a *block* which specifies the statements to execute in order to initialize the class.</span></span> <span data-ttu-id="467ae-1493">Corrisponde esattamente al *method_body* di un metodo statico con un tipo restituito `void` (corpo del[Metodo](classes.md#method-body)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1493">This corresponds exactly to the *method_body* of a static method with a `void` return type ([Method body](classes.md#method-body)).</span></span>

<span data-ttu-id="467ae-1494">I costruttori statici non vengono ereditati e non possono essere chiamati direttamente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1494">Static constructors are not inherited, and cannot be called directly.</span></span>

<span data-ttu-id="467ae-1495">Il costruttore statico per un tipo di classe Closed viene eseguito al massimo una volta in un determinato dominio applicazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1495">The static constructor for a closed class type executes at most once in a given application domain.</span></span> <span data-ttu-id="467ae-1496">L'esecuzione di un costruttore statico viene attivata dal primo degli eventi seguenti che si verificano all'interno di un dominio dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="467ae-1496">The execution of a static constructor is triggered by the first of the following events to occur within an application domain:</span></span>

*  <span data-ttu-id="467ae-1497">Viene creata un'istanza del tipo di classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1497">An instance of the class type is created.</span></span>
*  <span data-ttu-id="467ae-1498">Viene fatto riferimento a qualsiasi membro statico del tipo di classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1498">Any of the static members of the class type are referenced.</span></span>

<span data-ttu-id="467ae-1499">Se una classe contiene il `Main` metodo ([avvio dell'applicazione](basic-concepts.md#application-startup)) in cui inizia l'esecuzione, il costruttore statico per tale classe viene eseguito `Main` prima che venga chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1499">If a class contains the `Main` method ([Application Startup](basic-concepts.md#application-startup)) in which execution begins, the static constructor for that class executes before the `Main` method is called.</span></span>

<span data-ttu-id="467ae-1500">Per inizializzare un nuovo tipo di classe chiusa, viene creato innanzitutto un nuovo set di campi statici ([campi statici e di istanza](classes.md#static-and-instance-fields)) per quel particolare tipo chiuso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1500">To initialize a new closed class type, first a new set of static fields ([Static and instance fields](classes.md#static-and-instance-fields)) for that particular closed type is created.</span></span> <span data-ttu-id="467ae-1501">Ogni campo statico viene inizializzato sul valore predefinito ([valori predefiniti](variables.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1501">Each of the static fields is initialized to its default value ([Default values](variables.md#default-values)).</span></span> <span data-ttu-id="467ae-1502">Successivamente, vengono eseguiti gli inizializzatori di campo statici ([inizializzazione del campo statico](classes.md#static-field-initialization)) per i campi statici.</span><span class="sxs-lookup"><span data-stu-id="467ae-1502">Next, the static field initializers ([Static field initialization](classes.md#static-field-initialization)) are executed for those static fields.</span></span> <span data-ttu-id="467ae-1503">Infine, viene eseguito il costruttore statico.</span><span class="sxs-lookup"><span data-stu-id="467ae-1503">Finally, the static constructor is executed.</span></span>

<span data-ttu-id="467ae-1504">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1504">The example</span></span>
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
<span data-ttu-id="467ae-1505">deve produrre l'output:</span><span class="sxs-lookup"><span data-stu-id="467ae-1505">must produce the output:</span></span>
```console
Init A
A.F
Init B
B.F
```
<span data-ttu-id="467ae-1506">Poiché l'esecuzione del `A`costruttore statico di viene attivata dalla chiamata a `A.F`e l'esecuzione del `B`costruttore statico di viene attivata dalla chiamata a `B.F`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1506">because the execution of `A`'s static constructor is triggered by the call to `A.F`, and the execution of `B`'s static constructor is triggered by the call to `B.F`.</span></span>

<span data-ttu-id="467ae-1507">È possibile creare dipendenze circolari che consentono di osservare i campi statici con inizializzatori di variabili nello stato del valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="467ae-1507">It is possible to construct circular dependencies that allow static fields with variable initializers to be observed in their default value state.</span></span>

<span data-ttu-id="467ae-1508">Esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1508">The example</span></span>
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
<span data-ttu-id="467ae-1509">produce l'output</span><span class="sxs-lookup"><span data-stu-id="467ae-1509">produces the output</span></span>
```console
X = 1, Y = 2
```

<span data-ttu-id="467ae-1510">Per eseguire il `Main` metodo, il sistema esegue prima l'inizializzatore `B.Y`per, prima del `B`costruttore statico della classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1510">To execute the `Main` method, the system first runs the initializer for `B.Y`, prior to class `B`'s static constructor.</span></span> <span data-ttu-id="467ae-1511">`Y`l'inizializzatore `A`di ' s causa l'esecuzione del costruttore statico perché `A.X` viene fatto riferimento al valore di.</span><span class="sxs-lookup"><span data-stu-id="467ae-1511">`Y`'s initializer causes `A`'s static constructor to be run because the value of `A.X` is referenced.</span></span> <span data-ttu-id="467ae-1512">Il costruttore statico di `A` a sua volta continua a calcolare il valore di `X`e, in questo modo, recupera il valore `Y`predefinito, che è zero.</span><span class="sxs-lookup"><span data-stu-id="467ae-1512">The static constructor of `A` in turn proceeds to compute the value of `X`, and in doing so fetches the default value of `Y`, which is zero.</span></span> <span data-ttu-id="467ae-1513">`A.X`viene quindi inizializzato su 1.</span><span class="sxs-lookup"><span data-stu-id="467ae-1513">`A.X` is thus initialized to 1.</span></span> <span data-ttu-id="467ae-1514">Il processo di esecuzione `A`degli inizializzatori di campo statici e del costruttore statico viene quindi completato, restituendo al calcolo del valore iniziale `Y`di, il risultato del quale diventa 2.</span><span class="sxs-lookup"><span data-stu-id="467ae-1514">The process of running `A`'s static field initializers and static constructor then completes, returning to the calculation of the initial value of `Y`, the result of which becomes 2.</span></span>

<span data-ttu-id="467ae-1515">Poiché il costruttore statico viene eseguito una sola volta per ogni tipo di classe costruito chiuso, è consigliabile applicare controlli di runtime sul parametro di tipo che non possono essere controllati in fase di compilazione tramite vincoli (vincoli del[parametro di tipo](classes.md#type-parameter-constraints)) .</span><span class="sxs-lookup"><span data-stu-id="467ae-1515">Because the static constructor is executed exactly once for each closed constructed class type, it is a convenient place to enforce run-time checks on the type parameter that cannot be checked at compile-time via constraints ([Type parameter constraints](classes.md#type-parameter-constraints)).</span></span> <span data-ttu-id="467ae-1516">Ad esempio, il tipo seguente usa un costruttore statico per applicare che l'argomento di tipo è un'enumerazione:</span><span class="sxs-lookup"><span data-stu-id="467ae-1516">For example, the following type uses a static constructor to enforce that the type argument is an enum:</span></span>
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

## <a name="destructors"></a><span data-ttu-id="467ae-1517">Distruttori</span><span class="sxs-lookup"><span data-stu-id="467ae-1517">Destructors</span></span>

<span data-ttu-id="467ae-1518">Un ***distruttore*** è un membro che implementa le azioni necessarie per distruggere un'istanza di una classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1518">A ***destructor*** is a member that implements the actions required to destruct an instance of a class.</span></span> <span data-ttu-id="467ae-1519">Un distruttore viene dichiarato usando un *destructor_declaration*:</span><span class="sxs-lookup"><span data-stu-id="467ae-1519">A destructor is declared using a *destructor_declaration*:</span></span>

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

<span data-ttu-id="467ae-1520">Un *destructor_declaration* può includere un set di *attributi* ([attributi](attributes.md)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1520">A *destructor_declaration* may include a set of *attributes* ([Attributes](attributes.md)).</span></span>

<span data-ttu-id="467ae-1521">L' *identificatore* di un *destructor_declaration* deve denominare la classe in cui è dichiarato il distruttore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1521">The *identifier* of a *destructor_declaration* must name the class in which the destructor is declared.</span></span> <span data-ttu-id="467ae-1522">Se viene specificato un altro nome, si verificherà un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1522">If any other name is specified, a compile-time error occurs.</span></span>

<span data-ttu-id="467ae-1523">Quando una dichiarazione di distruttore include un `extern` modificatore, il distruttore viene definito un ***distruttore esterno***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1523">When a destructor declaration includes an `extern` modifier, the destructor is said to be an ***external destructor***.</span></span> <span data-ttu-id="467ae-1524">Poiché una dichiarazione di distruttore esterna non fornisce alcuna implementazione effettiva, la relativa *destructor_body* è costituita da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="467ae-1524">Because an external destructor declaration provides no actual implementation, its *destructor_body* consists of a semicolon.</span></span> <span data-ttu-id="467ae-1525">Per tutti gli altri distruttori, *destructor_body* è costituito da un *blocco* che specifica le istruzioni da eseguire per distruggere un'istanza della classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1525">For all other destructors, the *destructor_body* consists of a *block* which specifies the statements to execute in order to destruct an instance of the class.</span></span> <span data-ttu-id="467ae-1526">Un *destructor_body* corrisponde esattamente al *method_body* di un metodo di istanza con un tipo restituito `void` ([corpo del metodo](classes.md#method-body)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1526">A *destructor_body* corresponds exactly to the *method_body* of an instance method with a `void` return type ([Method body](classes.md#method-body)).</span></span>

<span data-ttu-id="467ae-1527">I distruttori non vengono ereditati.</span><span class="sxs-lookup"><span data-stu-id="467ae-1527">Destructors are not inherited.</span></span> <span data-ttu-id="467ae-1528">Pertanto, una classe non dispone di distruttori diversi da quello che può essere dichiarato in tale classe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1528">Thus, a class has no destructors other than the one which may be declared in that class.</span></span>

<span data-ttu-id="467ae-1529">Poiché è necessario che un distruttore non abbia parametri, non può essere sottoposto a overload, quindi una classe può avere al massimo un distruttore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1529">Since a destructor is required to have no parameters, it cannot be overloaded, so a class can have, at most, one destructor.</span></span>

<span data-ttu-id="467ae-1530">I distruttori vengono richiamati automaticamente e non possono essere richiamati in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="467ae-1530">Destructors are invoked automatically, and cannot be invoked explicitly.</span></span> <span data-ttu-id="467ae-1531">Un'istanza diventa idonea per la distruzione quando non è più possibile utilizzare tale istanza per il codice.</span><span class="sxs-lookup"><span data-stu-id="467ae-1531">An instance becomes eligible for destruction when it is no longer possible for any code to use that instance.</span></span> <span data-ttu-id="467ae-1532">L'esecuzione del distruttore per l'istanza può verificarsi in qualsiasi momento dopo che l'istanza diventa idonea per la distruzione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1532">Execution of the destructor for the instance may occur at any time after the instance becomes eligible for destruction.</span></span> <span data-ttu-id="467ae-1533">Quando un'istanza viene distrutta, i distruttori nella catena di ereditarietà di tale istanza vengono chiamati, in ordine, dal più derivato al meno derivato.</span><span class="sxs-lookup"><span data-stu-id="467ae-1533">When an instance is destructed, the destructors in that instance's inheritance chain are called, in order, from most derived to least derived.</span></span> <span data-ttu-id="467ae-1534">Un distruttore può essere eseguito su qualsiasi thread.</span><span class="sxs-lookup"><span data-stu-id="467ae-1534">A destructor may be executed on any thread.</span></span> <span data-ttu-id="467ae-1535">Per ulteriori informazioni sulle regole che determinano quando e come viene eseguito un distruttore, vedere [gestione automatica della memoria](basic-concepts.md#automatic-memory-management).</span><span class="sxs-lookup"><span data-stu-id="467ae-1535">For further discussion of the rules that govern when and how a destructor is executed, see [Automatic memory management](basic-concepts.md#automatic-memory-management).</span></span>

<span data-ttu-id="467ae-1536">Output dell'esempio</span><span class="sxs-lookup"><span data-stu-id="467ae-1536">The output of the example</span></span>
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
<span data-ttu-id="467ae-1537">is</span><span class="sxs-lookup"><span data-stu-id="467ae-1537">is</span></span>
```
B's destructor
A's destructor
```
<span data-ttu-id="467ae-1538">Poiché i distruttori in una catena di ereditarietà vengono chiamati in ordine, dal più derivato al meno derivato.</span><span class="sxs-lookup"><span data-stu-id="467ae-1538">since destructors in an inheritance chain are called in order, from most derived to least derived.</span></span>

<span data-ttu-id="467ae-1539">I distruttori vengono implementati eseguendo l'override del metodo `Finalize` virtuale `System.Object`in.</span><span class="sxs-lookup"><span data-stu-id="467ae-1539">Destructors are implemented by overriding the virtual method `Finalize` on `System.Object`.</span></span> <span data-ttu-id="467ae-1540">C#ai programmi non è consentito eseguire l'override di questo metodo o di chiamarlo direttamente o di eseguirne l'override.</span><span class="sxs-lookup"><span data-stu-id="467ae-1540">C# programs are not permitted to override this method or call it (or overrides of it) directly.</span></span> <span data-ttu-id="467ae-1541">Ad esempio, il programma</span><span class="sxs-lookup"><span data-stu-id="467ae-1541">For instance, the program</span></span>
```csharp
class A 
{
    override protected void Finalize() {}    // error

    public void F() {
        this.Finalize();                     // error
    }
}
```
<span data-ttu-id="467ae-1542">contiene due errori.</span><span class="sxs-lookup"><span data-stu-id="467ae-1542">contains two errors.</span></span>

<span data-ttu-id="467ae-1543">Il compilatore si comporta come se questo metodo e le sue sostituzioni non esistessero affatto.</span><span class="sxs-lookup"><span data-stu-id="467ae-1543">The compiler behaves as if this method, and overrides of it, do not exist at all.</span></span> <span data-ttu-id="467ae-1544">Quindi, questo programma:</span><span class="sxs-lookup"><span data-stu-id="467ae-1544">Thus, this program:</span></span>
```csharp
class A 
{
    void Finalize() {}                            // permitted
}
```
<span data-ttu-id="467ae-1545">è valido e il metodo Mostra il metodo `System.Object`Hides `Finalize` .</span><span class="sxs-lookup"><span data-stu-id="467ae-1545">is valid, and the method shown hides `System.Object`'s `Finalize` method.</span></span>

<span data-ttu-id="467ae-1546">Per una descrizione del comportamento quando viene generata un'eccezione da un distruttore, vedere [come vengono gestite le eccezioni](exceptions.md#how-exceptions-are-handled).</span><span class="sxs-lookup"><span data-stu-id="467ae-1546">For a discussion of the behavior when an exception is thrown from a destructor, see [How exceptions are handled](exceptions.md#how-exceptions-are-handled).</span></span>

## <a name="iterators"></a><span data-ttu-id="467ae-1547">Iterators</span><span class="sxs-lookup"><span data-stu-id="467ae-1547">Iterators</span></span>

<span data-ttu-id="467ae-1548">Un membro di funzione ([membri di funzione](expressions.md#function-members)) implementato utilizzando un blocco iteratore ([blocchi](statements.md#blocks)) viene definito ***iteratore***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1548">A function member ([Function members](expressions.md#function-members)) implemented using an iterator block ([Blocks](statements.md#blocks)) is called an ***iterator***.</span></span>

<span data-ttu-id="467ae-1549">Un blocco iteratore può essere usato come corpo di un membro di funzione purché il tipo restituito del membro di funzione corrispondente sia una delle interfacce di enumeratore ([interfacce di enumeratore](classes.md#enumerator-interfaces)) o una delle interfacce enumerabili[(interfacce enumerabili](classes.md#enumerable-interfaces)) .</span><span class="sxs-lookup"><span data-stu-id="467ae-1549">An iterator block may be used as the body of a function member as long as the return type of the corresponding function member is one of the enumerator interfaces ([Enumerator interfaces](classes.md#enumerator-interfaces)) or one of the enumerable interfaces ([Enumerable interfaces](classes.md#enumerable-interfaces)).</span></span> <span data-ttu-id="467ae-1550">Può verificarsi come *method_body*, *operator_body* o *accessor_body*, mentre gli eventi, i costruttori di istanza, i costruttori statici e i distruttori non possono essere implementati come iteratori.</span><span class="sxs-lookup"><span data-stu-id="467ae-1550">It can occur as a *method_body*, *operator_body* or *accessor_body*, whereas events, instance constructors, static constructors and destructors cannot be implemented as iterators.</span></span>

<span data-ttu-id="467ae-1551">Quando un membro di funzione viene implementato usando un blocco iteratore, si tratta di un errore in fase di compilazione per l'elenco di parametri formali del `ref` membro `out` della funzione per specificare i parametri o.</span><span class="sxs-lookup"><span data-stu-id="467ae-1551">When a function member is implemented using an iterator block, it is a compile-time error for the formal parameter list of the function member to specify any `ref` or `out` parameters.</span></span>

### <a name="enumerator-interfaces"></a><span data-ttu-id="467ae-1552">Interfacce enumeratore</span><span class="sxs-lookup"><span data-stu-id="467ae-1552">Enumerator interfaces</span></span>

<span data-ttu-id="467ae-1553">Le ***interfacce dell'enumeratore*** sono l'interfaccia `System.Collections.IEnumerator` non generica e tutte le creazioni di istanze `System.Collections.Generic.IEnumerator<T>`dell'interfaccia generica.</span><span class="sxs-lookup"><span data-stu-id="467ae-1553">The ***enumerator interfaces*** are the non-generic interface `System.Collections.IEnumerator` and all instantiations of the generic interface `System.Collections.Generic.IEnumerator<T>`.</span></span> <span data-ttu-id="467ae-1554">Per motivi di brevità, in questo capitolo viene fatto riferimento a queste interfacce `IEnumerator` rispettivamente `IEnumerator<T>`come e.</span><span class="sxs-lookup"><span data-stu-id="467ae-1554">For the sake of brevity, in this chapter these interfaces are referenced as `IEnumerator` and `IEnumerator<T>`, respectively.</span></span>

### <a name="enumerable-interfaces"></a><span data-ttu-id="467ae-1555">Interfacce enumerabili</span><span class="sxs-lookup"><span data-stu-id="467ae-1555">Enumerable interfaces</span></span>

<span data-ttu-id="467ae-1556">Le ***interfacce enumerabili*** sono l'interfaccia `System.Collections.IEnumerable` non generica e tutte le creazioni di istanze dell' `System.Collections.Generic.IEnumerable<T>`interfaccia generica.</span><span class="sxs-lookup"><span data-stu-id="467ae-1556">The ***enumerable interfaces*** are the non-generic interface `System.Collections.IEnumerable` and all instantiations of the generic interface `System.Collections.Generic.IEnumerable<T>`.</span></span> <span data-ttu-id="467ae-1557">Per motivi di brevità, in questo capitolo viene fatto riferimento a queste interfacce `IEnumerable` rispettivamente `IEnumerable<T>`come e.</span><span class="sxs-lookup"><span data-stu-id="467ae-1557">For the sake of brevity, in this chapter these interfaces are referenced as `IEnumerable` and `IEnumerable<T>`, respectively.</span></span>

### <a name="yield-type"></a><span data-ttu-id="467ae-1558">Tipo yield</span><span class="sxs-lookup"><span data-stu-id="467ae-1558">Yield type</span></span>

<span data-ttu-id="467ae-1559">Un iteratore produce una sequenza di valori, tutti dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1559">An iterator produces a sequence of values, all of the same type.</span></span> <span data-ttu-id="467ae-1560">Questo tipo è denominato ***tipo yield*** dell'iteratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1560">This type is called the ***yield type*** of the iterator.</span></span>

*  <span data-ttu-id="467ae-1561">Tipo di Yield di un iteratore che `IEnumerator` restituisce `IEnumerable` o `object`è.</span><span class="sxs-lookup"><span data-stu-id="467ae-1561">The yield type of an iterator that returns `IEnumerator` or `IEnumerable` is `object`.</span></span>
*  <span data-ttu-id="467ae-1562">Tipo di Yield di un iteratore che `IEnumerator<T>` restituisce `IEnumerable<T>` o `T`è.</span><span class="sxs-lookup"><span data-stu-id="467ae-1562">The yield type of an iterator that returns `IEnumerator<T>` or `IEnumerable<T>` is `T`.</span></span>

### <a name="enumerator-objects"></a><span data-ttu-id="467ae-1563">Oggetti enumeratore</span><span class="sxs-lookup"><span data-stu-id="467ae-1563">Enumerator objects</span></span>

<span data-ttu-id="467ae-1564">Quando un membro di funzione che restituisce un tipo di interfaccia di enumerazione viene implementato utilizzando un blocco iteratore, la chiamata al membro della funzione non esegue immediatamente il codice nel blocco iteratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1564">When a function member returning an enumerator interface type is implemented using an iterator block, invoking the function member does not immediately execute the code in the iterator block.</span></span> <span data-ttu-id="467ae-1565">Viene invece creato e restituito un ***oggetto enumeratore*** .</span><span class="sxs-lookup"><span data-stu-id="467ae-1565">Instead, an ***enumerator object*** is created and returned.</span></span> <span data-ttu-id="467ae-1566">Questo oggetto incapsula il codice specificato nel blocco iteratore e l'esecuzione del codice nel blocco iteratore si verifica quando viene richiamato il `MoveNext` metodo dell'oggetto enumeratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1566">This object encapsulates the code specified in the iterator block, and execution of the code in the iterator block occurs when the enumerator object's `MoveNext` method is invoked.</span></span> <span data-ttu-id="467ae-1567">Un oggetto enumeratore presenta le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-1567">An enumerator object has the following characteristics:</span></span>

*  <span data-ttu-id="467ae-1568">Implementa `IEnumerator` e `IEnumerator<T>`, dove`T` è il tipo di risultato dell'iteratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1568">It implements `IEnumerator` and `IEnumerator<T>`, where `T` is the yield type of the iterator.</span></span>
*  <span data-ttu-id="467ae-1569">Implementa `System.IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1569">It implements `System.IDisposable`.</span></span>
*  <span data-ttu-id="467ae-1570">Viene inizializzata con una copia dei valori degli argomenti (se presenti) e del valore dell'istanza passati al membro della funzione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1570">It is initialized with a copy of the argument values (if any) and instance value passed to the function member.</span></span>
*  <span data-ttu-id="467ae-1571">Ha quattro stati potenziali, ***prima***, ***in esecuzione***, ***sospesi***e ***dopo***, e si trova inizialmente nello stato ***before*** .</span><span class="sxs-lookup"><span data-stu-id="467ae-1571">It has four potential states, ***before***, ***running***, ***suspended***, and ***after***, and is initially in the ***before*** state.</span></span>

<span data-ttu-id="467ae-1572">Un oggetto enumeratore è in genere un'istanza di una classe di enumeratori generata dal compilatore che incapsula il codice nel blocco iteratore e implementa le interfacce dell'enumeratore, ma sono possibili altri metodi di implementazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1572">An enumerator object is typically an instance of a compiler-generated enumerator class that encapsulates the code in the iterator block and implements the enumerator interfaces, but other methods of implementation are possible.</span></span> <span data-ttu-id="467ae-1573">Se una classe di enumeratori viene generata dal compilatore, tale classe sarà annidata, direttamente o indirettamente, nella classe contenente il membro della funzione, avrà l'accessibilità privata e avrà un nome riservato per l'uso del compilatore ([identificatori](lexical-structure.md#identifiers)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1573">If an enumerator class is generated by the compiler, that class will be nested, directly or indirectly, in the class containing the function member, it will have private accessibility, and it will have a name reserved for compiler use ([Identifiers](lexical-structure.md#identifiers)).</span></span>

<span data-ttu-id="467ae-1574">Un oggetto enumeratore può implementare più interfacce rispetto a quelle specificate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="467ae-1574">An enumerator object may implement more interfaces than those specified above.</span></span>

<span data-ttu-id="467ae-1575">Nelle sezioni seguenti viene descritto il comportamento esatto dei `MoveNext`membri `Current`, e `Dispose` delle `IEnumerable` implementazioni dell' `IEnumerable<T>` interfaccia e fornite da un oggetto enumeratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1575">The following sections describe the exact behavior of the `MoveNext`, `Current`, and `Dispose` members of the `IEnumerable` and `IEnumerable<T>` interface implementations provided by an enumerator object.</span></span>

<span data-ttu-id="467ae-1576">Si noti che gli oggetti enumeratore non `IEnumerator.Reset` supportano il metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1576">Note that enumerator objects do not support the `IEnumerator.Reset` method.</span></span> <span data-ttu-id="467ae-1577">Quando si richiama questo metodo, `System.NotSupportedException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1577">Invoking this method causes a `System.NotSupportedException` to be thrown.</span></span>

#### <a name="the-movenext-method"></a><span data-ttu-id="467ae-1578">Metodo MoveNext</span><span class="sxs-lookup"><span data-stu-id="467ae-1578">The MoveNext method</span></span>

<span data-ttu-id="467ae-1579">Il `MoveNext` metodo di un oggetto enumeratore incapsula il codice di un blocco iteratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1579">The `MoveNext` method of an enumerator object encapsulates the code of an iterator block.</span></span> <span data-ttu-id="467ae-1580">La chiamata del `MoveNext` metodo esegue il codice nel blocco iteratore e imposta la `Current` proprietà dell'oggetto enumeratore nel modo appropriato.</span><span class="sxs-lookup"><span data-stu-id="467ae-1580">Invoking the `MoveNext` method executes code in the iterator block and sets the `Current` property of the enumerator object as appropriate.</span></span> <span data-ttu-id="467ae-1581">L'azione esatta eseguita da `MoveNext` dipende dallo stato dell'oggetto enumeratore quando `MoveNext` viene richiamato:</span><span class="sxs-lookup"><span data-stu-id="467ae-1581">The precise action performed by `MoveNext` depends on the state of the enumerator object when `MoveNext` is invoked:</span></span>

*  <span data-ttu-id="467ae-1582">Se lo stato dell'oggetto enumeratore è ***precedente***, richiamare `MoveNext`:</span><span class="sxs-lookup"><span data-stu-id="467ae-1582">If the state of the enumerator object is ***before***, invoking `MoveNext`:</span></span>
   * <span data-ttu-id="467ae-1583">Imposta lo stato su ***in esecuzione***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1583">Changes the state to ***running***.</span></span>
   * <span data-ttu-id="467ae-1584">Inizializza i parametri (incluso `this`) del blocco iteratore sui valori degli argomenti e sul valore dell'istanza salvati quando l'oggetto enumeratore è stato inizializzato.</span><span class="sxs-lookup"><span data-stu-id="467ae-1584">Initializes the parameters (including `this`) of the iterator block to the argument values and instance value saved when the enumerator object was initialized.</span></span>
   * <span data-ttu-id="467ae-1585">Esegue il blocco iteratore dall'inizio fino a quando l'esecuzione non viene interrotta (come descritto di seguito).</span><span class="sxs-lookup"><span data-stu-id="467ae-1585">Executes the iterator block from the beginning until execution is interrupted (as described below).</span></span>
*  <span data-ttu-id="467ae-1586">Se lo stato dell'oggetto enumeratore è ***in esecuzione***, il risultato della chiamata `MoveNext` non è specificato.</span><span class="sxs-lookup"><span data-stu-id="467ae-1586">If the state of the enumerator object is ***running***, the result of invoking `MoveNext` is unspecified.</span></span>
*  <span data-ttu-id="467ae-1587">Se lo stato dell'oggetto enumeratore è ***sospeso***, richiamando `MoveNext`:</span><span class="sxs-lookup"><span data-stu-id="467ae-1587">If the state of the enumerator object is ***suspended***, invoking `MoveNext`:</span></span>
   * <span data-ttu-id="467ae-1588">Imposta lo stato su ***in esecuzione***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1588">Changes the state to ***running***.</span></span>
   * <span data-ttu-id="467ae-1589">Ripristina i valori di tutte le variabili e i parametri locali (incluso) ai valori salvati durante l'ultima sospensione dell'esecuzione del blocco iteratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1589">Restores the values of all local variables and parameters (including this) to the values saved when execution of the iterator block was last suspended.</span></span> <span data-ttu-id="467ae-1590">Si noti che il contenuto degli oggetti a cui fanno riferimento queste variabili potrebbe essere stato modificato rispetto alla precedente chiamata a MoveNext.</span><span class="sxs-lookup"><span data-stu-id="467ae-1590">Note that the contents of any objects referenced by these variables may have changed since the previous call to MoveNext.</span></span>
   * <span data-ttu-id="467ae-1591">Riprende l'esecuzione del blocco iteratore immediatamente dopo l' `yield return` istruzione che ha causato la sospensione dell'esecuzione e continua fino a quando l'esecuzione non viene interrotta (come descritto di seguito).</span><span class="sxs-lookup"><span data-stu-id="467ae-1591">Resumes execution of the iterator block immediately following the `yield return` statement that caused the suspension of execution and continues until execution is interrupted (as described below).</span></span>
*  <span data-ttu-id="467ae-1592">Se lo stato dell'oggetto enumeratore è ***dopo***, la chiamata `MoveNext` di `false`restituisce.</span><span class="sxs-lookup"><span data-stu-id="467ae-1592">If the state of the enumerator object is ***after***, invoking `MoveNext` returns `false`.</span></span>


<span data-ttu-id="467ae-1593">Quando `MoveNext` esegue il blocco iteratore, l'esecuzione può essere interrotta in quattro modi: Da un'istruzione, da un' `yield break` istruzione, che rileva la fine del blocco iteratore e da un'eccezione generata e propagata all'esterno del blocco iteratore. `yield return`</span><span class="sxs-lookup"><span data-stu-id="467ae-1593">When `MoveNext` executes the iterator block, execution can be interrupted in four ways: By a `yield return` statement, by a `yield break` statement, by encountering the end of the iterator block, and by an exception being thrown and propagated out of the iterator block.</span></span>

*  <span data-ttu-id="467ae-1594">Quando viene `yield return` rilevata un'istruzione ([istruzione yield](statements.md#the-yield-statement)):</span><span class="sxs-lookup"><span data-stu-id="467ae-1594">When a `yield return` statement is encountered ([The yield statement](statements.md#the-yield-statement)):</span></span>
   * <span data-ttu-id="467ae-1595">L'espressione specificata nell'istruzione viene valutata, convertita in modo implicito nel tipo yield e assegnata `Current` alla proprietà dell'oggetto enumeratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1595">The expression given in the statement is evaluated, implicitly converted to the yield type, and assigned to the `Current` property of the enumerator object.</span></span>
   * <span data-ttu-id="467ae-1596">L'esecuzione del corpo dell'iteratore è sospesa.</span><span class="sxs-lookup"><span data-stu-id="467ae-1596">Execution of the iterator body is suspended.</span></span> <span data-ttu-id="467ae-1597">I valori di tutte le variabili e i parametri locali `this`(incluso) vengono salvati, così come il percorso `yield return` di questa istruzione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1597">The values of all local variables and parameters (including `this`) are saved, as is the location of this `yield return` statement.</span></span> <span data-ttu-id="467ae-1598">Se l' `yield return` istruzione si trova all'interno di `try` uno o più blocchi `finally` , i blocchi associati non vengono eseguiti in questo momento.</span><span class="sxs-lookup"><span data-stu-id="467ae-1598">If the `yield return` statement is within one or more `try` blocks, the associated `finally` blocks are not executed at this time.</span></span>
   * <span data-ttu-id="467ae-1599">Lo stato dell'oggetto enumeratore viene modificato in ***sospeso***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1599">The state of the enumerator object is changed to ***suspended***.</span></span>
   * <span data-ttu-id="467ae-1600">Il `MoveNext` metodo restituisce `true` al chiamante, a indicare che l'iterazione è stata avanzata correttamente in base al valore successivo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1600">The `MoveNext` method returns `true` to its caller, indicating that the iteration successfully advanced to the next value.</span></span>
*  <span data-ttu-id="467ae-1601">Quando viene `yield break` rilevata un'istruzione ([istruzione yield](statements.md#the-yield-statement)):</span><span class="sxs-lookup"><span data-stu-id="467ae-1601">When a `yield break` statement is encountered ([The yield statement](statements.md#the-yield-statement)):</span></span>
   * <span data-ttu-id="467ae-1602">Se l' `yield break` istruzione si trova all'interno di `try` uno o più blocchi `finally` , vengono eseguiti i blocchi associati.</span><span class="sxs-lookup"><span data-stu-id="467ae-1602">If the `yield break` statement is within one or more `try` blocks, the associated `finally` blocks are executed.</span></span>
   * <span data-ttu-id="467ae-1603">Lo stato dell'oggetto enumeratore viene modificato in ***dopo***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1603">The state of the enumerator object is changed to ***after***.</span></span>
   * <span data-ttu-id="467ae-1604">Il `MoveNext` metodo restituisce `false` al chiamante, a indicare che l'iterazione è completa.</span><span class="sxs-lookup"><span data-stu-id="467ae-1604">The `MoveNext` method returns `false` to its caller, indicating that the iteration is complete.</span></span>
*  <span data-ttu-id="467ae-1605">Quando viene rilevata la fine del corpo dell'iteratore:</span><span class="sxs-lookup"><span data-stu-id="467ae-1605">When the end of the iterator body is encountered:</span></span>
   * <span data-ttu-id="467ae-1606">Lo stato dell'oggetto enumeratore viene modificato in ***dopo***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1606">The state of the enumerator object is changed to ***after***.</span></span>
   * <span data-ttu-id="467ae-1607">Il `MoveNext` metodo restituisce `false` al chiamante, a indicare che l'iterazione è completa.</span><span class="sxs-lookup"><span data-stu-id="467ae-1607">The `MoveNext` method returns `false` to its caller, indicating that the iteration is complete.</span></span>
*  <span data-ttu-id="467ae-1608">Quando viene generata un'eccezione e viene propagata all'esterno del blocco iteratore:</span><span class="sxs-lookup"><span data-stu-id="467ae-1608">When an exception is thrown and propagated out of the iterator block:</span></span>
   * <span data-ttu-id="467ae-1609">I `finally` blocchi appropriati nel corpo dell'iteratore sono stati eseguiti dalla propagazione delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="467ae-1609">Appropriate `finally` blocks in the iterator body will have been executed by the exception propagation.</span></span>
   * <span data-ttu-id="467ae-1610">Lo stato dell'oggetto enumeratore viene modificato in ***dopo***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1610">The state of the enumerator object is changed to ***after***.</span></span>
   * <span data-ttu-id="467ae-1611">La propagazione delle eccezioni continua al chiamante del `MoveNext` metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1611">The exception propagation continues to the caller of the `MoveNext` method.</span></span>

#### <a name="the-current-property"></a><span data-ttu-id="467ae-1612">Proprietà corrente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1612">The Current property</span></span>

<span data-ttu-id="467ae-1613">La `Current` proprietà di un oggetto enumeratore è `yield return` interessata dalle istruzioni nel blocco iteratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1613">An enumerator object's `Current` property is affected by `yield return` statements in the iterator block.</span></span>

<span data-ttu-id="467ae-1614">Quando un oggetto enumeratore si trova nello stato ***suspended*** , il `Current` valore di è il valore impostato dalla chiamata precedente `MoveNext`a.</span><span class="sxs-lookup"><span data-stu-id="467ae-1614">When an enumerator object is in the ***suspended*** state, the value of `Current` is the value set by the previous call to `MoveNext`.</span></span> <span data-ttu-id="467ae-1615">Quando un oggetto enumeratore si trova negli Stati ***before***, ***Running***o ***after*** , `Current` il risultato dell'accesso a non è specificato.</span><span class="sxs-lookup"><span data-stu-id="467ae-1615">When an enumerator object is in the ***before***, ***running***, or ***after*** states, the result of accessing `Current` is unspecified.</span></span>

<span data-ttu-id="467ae-1616">Per un iteratore con un tipo yield diverso `object`da, il risultato dell' `Current` accesso `IEnumerable` tramite l'implementazione dell'oggetto enumeratore corrisponde `IEnumerator<T>` all'accesso `Current` tramite l'oggetto dell'enumeratore implementazione ed esecuzione del cast del `object`risultato a.</span><span class="sxs-lookup"><span data-stu-id="467ae-1616">For an iterator with a yield type other than `object`, the result of accessing `Current` through the enumerator object's `IEnumerable` implementation corresponds to accessing `Current` through the enumerator object's `IEnumerator<T>` implementation and casting the result to `object`.</span></span>

#### <a name="the-dispose-method"></a><span data-ttu-id="467ae-1617">Metodo Dispose</span><span class="sxs-lookup"><span data-stu-id="467ae-1617">The Dispose method</span></span>

<span data-ttu-id="467ae-1618">Il `Dispose` metodo viene usato per pulire l'iterazione portando l'oggetto enumeratore allo stato ***after*** .</span><span class="sxs-lookup"><span data-stu-id="467ae-1618">The `Dispose` method is used to clean up the iteration by bringing the enumerator object to the ***after*** state.</span></span>

*  <span data-ttu-id="467ae-1619">Se lo stato dell'oggetto enumeratore è ***precedente***, la chiamata `Dispose` di cambia lo stato in ***dopo***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1619">If the state of the enumerator object is ***before***, invoking `Dispose` changes the state to ***after***.</span></span>
*  <span data-ttu-id="467ae-1620">Se lo stato dell'oggetto enumeratore è ***in esecuzione***, il risultato della chiamata `Dispose` non è specificato.</span><span class="sxs-lookup"><span data-stu-id="467ae-1620">If the state of the enumerator object is ***running***, the result of invoking `Dispose` is unspecified.</span></span>
*  <span data-ttu-id="467ae-1621">Se lo stato dell'oggetto enumeratore è ***sospeso***, richiamando `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="467ae-1621">If the state of the enumerator object is ***suspended***, invoking `Dispose`:</span></span>
   * <span data-ttu-id="467ae-1622">Imposta lo stato su ***in esecuzione***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1622">Changes the state to ***running***.</span></span>
   * <span data-ttu-id="467ae-1623">Esegue qualsiasi blocco finally come se l'ultima istruzione `yield return` eseguita fosse un' `yield break` istruzione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1623">Executes any finally blocks as if the last executed `yield return` statement were a `yield break` statement.</span></span> <span data-ttu-id="467ae-1624">Se in questo modo viene generata un'eccezione e viene propagata all'esterno del corpo dell'iteratore, lo stato dell'oggetto enumeratore viene impostato su ***after*** e l'eccezione viene propagata `Dispose` al chiamante del metodo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1624">If this causes an exception to be thrown and propagated out of the iterator body, the state of the enumerator object is set to ***after*** and the exception is propagated to the caller of the `Dispose` method.</span></span>
   * <span data-ttu-id="467ae-1625">Imposta lo stato su ***after***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1625">Changes the state to ***after***.</span></span>
*  <span data-ttu-id="467ae-1626">Se lo stato dell'oggetto enumeratore è ***after***, la chiamata `Dispose` a non ha alcun effetto.</span><span class="sxs-lookup"><span data-stu-id="467ae-1626">If the state of the enumerator object is ***after***, invoking `Dispose` has no affect.</span></span>

### <a name="enumerable-objects"></a><span data-ttu-id="467ae-1627">Oggetti enumerabili</span><span class="sxs-lookup"><span data-stu-id="467ae-1627">Enumerable objects</span></span>

<span data-ttu-id="467ae-1628">Quando un membro di funzione che restituisce un tipo di interfaccia enumerabile viene implementato utilizzando un blocco iteratore, la chiamata al membro della funzione non esegue immediatamente il codice nel blocco iteratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1628">When a function member returning an enumerable interface type is implemented using an iterator block, invoking the function member does not immediately execute the code in the iterator block.</span></span> <span data-ttu-id="467ae-1629">Viene invece creato e restituito un ***oggetto enumerabile*** .</span><span class="sxs-lookup"><span data-stu-id="467ae-1629">Instead, an ***enumerable object*** is created and returned.</span></span> <span data-ttu-id="467ae-1630">Il `GetEnumerator` metodo dell'oggetto enumerabile restituisce un oggetto enumeratore che incapsula il codice specificato nel blocco iteratore e l'esecuzione del codice nel blocco iteratore si verifica quando viene richiamato `MoveNext` il metodo dell'oggetto enumeratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1630">The enumerable object's `GetEnumerator` method returns an enumerator object that encapsulates the code specified in the iterator block, and execution of the code in the iterator block occurs when the enumerator object's `MoveNext` method is invoked.</span></span> <span data-ttu-id="467ae-1631">Un oggetto enumerabile presenta le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="467ae-1631">An enumerable object has the following characteristics:</span></span>

*  <span data-ttu-id="467ae-1632">Implementa `IEnumerable` e `IEnumerable<T>`, dove`T` è il tipo di risultato dell'iteratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1632">It implements `IEnumerable` and `IEnumerable<T>`, where `T` is the yield type of the iterator.</span></span>
*  <span data-ttu-id="467ae-1633">Viene inizializzata con una copia dei valori degli argomenti (se presenti) e del valore dell'istanza passati al membro della funzione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1633">It is initialized with a copy of the argument values (if any) and instance value passed to the function member.</span></span>

<span data-ttu-id="467ae-1634">Un oggetto enumerabile è in genere un'istanza di una classe enumerabile generata dal compilatore che incapsula il codice nel blocco iteratore e implementa le interfacce enumerabili, ma sono possibili altri metodi di implementazione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1634">An enumerable object is typically an instance of a compiler-generated enumerable class that encapsulates the code in the iterator block and implements the enumerable interfaces, but other methods of implementation are possible.</span></span> <span data-ttu-id="467ae-1635">Se una classe enumerabile viene generata dal compilatore, tale classe sarà nidificata, direttamente o indirettamente, nella classe contenente il membro della funzione, avrà accessibilità privata e avrà un nome riservato per l'uso del compilatore ([identificatori](lexical-structure.md#identifiers)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1635">If an enumerable class is generated by the compiler, that class will be nested, directly or indirectly, in the class containing the function member, it will have private accessibility, and it will have a name reserved for compiler use ([Identifiers](lexical-structure.md#identifiers)).</span></span>

<span data-ttu-id="467ae-1636">Un oggetto enumerabile può implementare più interfacce rispetto a quelle specificate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="467ae-1636">An enumerable object may implement more interfaces than those specified above.</span></span> <span data-ttu-id="467ae-1637">In particolare, un oggetto enumerabile può implementare `IEnumerator` anche `IEnumerator<T>`e, consentendo di fungere da enumeratore e enumeratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1637">In particular, an enumerable object may also implement `IEnumerator` and `IEnumerator<T>`, enabling it to serve as both an enumerable and an enumerator.</span></span> <span data-ttu-id="467ae-1638">In tale tipo di implementazione, la prima volta che viene richiamato `GetEnumerator` il metodo di un oggetto enumerabile, viene restituito l'oggetto enumerabile stesso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1638">In that type of implementation, the first time an enumerable object's `GetEnumerator` method is invoked, the enumerable object itself is returned.</span></span> <span data-ttu-id="467ae-1639">Le chiamate successive dell'oggetto `GetEnumerator`enumerabile, se presente, restituiscono una copia dell'oggetto enumerabile.</span><span class="sxs-lookup"><span data-stu-id="467ae-1639">Subsequent invocations of the enumerable object's `GetEnumerator`, if any, return a copy of the enumerable object.</span></span> <span data-ttu-id="467ae-1640">Pertanto, ogni enumeratore restituito ha il proprio stato e le modifiche apportate in un enumeratore non avranno effetto su un'altra.</span><span class="sxs-lookup"><span data-stu-id="467ae-1640">Thus, each returned enumerator has its own state and changes in one enumerator will not affect another.</span></span>

#### <a name="the-getenumerator-method"></a><span data-ttu-id="467ae-1641">Metodo GetEnumerator</span><span class="sxs-lookup"><span data-stu-id="467ae-1641">The GetEnumerator method</span></span>

<span data-ttu-id="467ae-1642">Un oggetto enumerabile fornisce un'implementazione dei `GetEnumerator` metodi `IEnumerable` delle interfacce e `IEnumerable<T>` .</span><span class="sxs-lookup"><span data-stu-id="467ae-1642">An enumerable object provides an implementation of the `GetEnumerator` methods of the `IEnumerable` and `IEnumerable<T>` interfaces.</span></span> <span data-ttu-id="467ae-1643">I due `GetEnumerator` metodi condividono un'implementazione comune che acquisisce e restituisce un oggetto enumeratore disponibile.</span><span class="sxs-lookup"><span data-stu-id="467ae-1643">The two `GetEnumerator` methods share a common implementation that acquires and returns an available enumerator object.</span></span> <span data-ttu-id="467ae-1644">L'oggetto enumeratore viene inizializzato con i valori dell'argomento e il valore dell'istanza salvati quando l'oggetto enumerabile è stato inizializzato. in caso contrario, l'oggetto enumeratore funziona come descritto in [oggetti enumeratore](classes.md#enumerator-objects).</span><span class="sxs-lookup"><span data-stu-id="467ae-1644">The enumerator object is initialized with the argument values and instance value saved when the enumerable object was initialized, but otherwise the enumerator object functions as described in [Enumerator objects](classes.md#enumerator-objects).</span></span>

### <a name="implementation-example"></a><span data-ttu-id="467ae-1645">Esempio di implementazione</span><span class="sxs-lookup"><span data-stu-id="467ae-1645">Implementation example</span></span>

<span data-ttu-id="467ae-1646">In questa sezione viene descritta una possibile implementazione di iteratori in termini C# di costrutti standard.</span><span class="sxs-lookup"><span data-stu-id="467ae-1646">This section describes a possible implementation of iterators in terms of standard C# constructs.</span></span> <span data-ttu-id="467ae-1647">L'implementazione descritta di seguito si basa sugli stessi principi usati dal compilatore Microsoft C# , ma non è un'implementazione obbligatoria o l'unico possibile.</span><span class="sxs-lookup"><span data-stu-id="467ae-1647">The implementation described here is based on the same principles used by the Microsoft C# compiler, but it is by no means a mandated implementation or the only one possible.</span></span>

<span data-ttu-id="467ae-1648">La classe `Stack<T>` seguente `GetEnumerator` implementa il metodo usando un iteratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1648">The following `Stack<T>` class implements its `GetEnumerator` method using an iterator.</span></span> <span data-ttu-id="467ae-1649">L'iteratore enumera gli elementi dello stack nell'ordine dall'alto verso il basso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1649">The iterator enumerates the elements of the stack in top to bottom order.</span></span>

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

<span data-ttu-id="467ae-1650">Il `GetEnumerator` metodo può essere convertito in un'istanza di una classe di enumeratori generata dal compilatore che incapsula il codice nel blocco iteratore, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="467ae-1650">The `GetEnumerator` method can be translated into an instantiation of a compiler-generated enumerator class that encapsulates the code in the iterator block, as shown in the following.</span></span>

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

<span data-ttu-id="467ae-1651">Nella conversione precedente, il codice nel blocco iteratore viene trasformato in una macchina a Stati e inserito nel `MoveNext` metodo della classe Enumerator.</span><span class="sxs-lookup"><span data-stu-id="467ae-1651">In the preceding translation, the code in the iterator block is turned into a state machine and placed in the `MoveNext` method of the enumerator class.</span></span> <span data-ttu-id="467ae-1652">Inoltre, la variabile `i` locale viene trasformata in un campo nell'oggetto enumeratore, in modo che possa continuare a esistere tra le chiamate di. `MoveNext`</span><span class="sxs-lookup"><span data-stu-id="467ae-1652">Furthermore, the local variable `i` is turned into a field in the enumerator object so it can continue to exist across invocations of `MoveNext`.</span></span>

<span data-ttu-id="467ae-1653">Nell'esempio seguente viene stampata una semplice tabella di moltiplicazione dei numeri interi da 1 a 10.</span><span class="sxs-lookup"><span data-stu-id="467ae-1653">The following example prints a simple multiplication table of the integers 1 through 10.</span></span> <span data-ttu-id="467ae-1654">Il `FromTo` metodo nell'esempio restituisce un oggetto enumerabile ed è implementato utilizzando un iteratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1654">The `FromTo` method in the example returns an enumerable object and is implemented using an iterator.</span></span>

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

<span data-ttu-id="467ae-1655">Il `FromTo` metodo può essere convertito in un'istanza di una classe Enumerable generata dal compilatore che incapsula il codice nel blocco iteratore, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="467ae-1655">The `FromTo` method can be translated into an instantiation of a compiler-generated enumerable class that encapsulates the code in the iterator block, as shown in the following.</span></span>

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

<span data-ttu-id="467ae-1656">La classe Enumerable implementa sia le interfacce enumerabili che le interfacce dell'enumeratore, consentendo di fungere da enumeratore e enumeratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1656">The enumerable class implements both the enumerable interfaces and the enumerator interfaces, enabling it to serve as both an enumerable and an enumerator.</span></span> <span data-ttu-id="467ae-1657">La prima volta che `GetEnumerator` il metodo viene richiamato, viene restituito l'oggetto enumerabile stesso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1657">The first time the `GetEnumerator` method is invoked, the enumerable object itself is returned.</span></span> <span data-ttu-id="467ae-1658">Le chiamate successive dell'oggetto `GetEnumerator`enumerabile, se presente, restituiscono una copia dell'oggetto enumerabile.</span><span class="sxs-lookup"><span data-stu-id="467ae-1658">Subsequent invocations of the enumerable object's `GetEnumerator`, if any, return a copy of the enumerable object.</span></span> <span data-ttu-id="467ae-1659">Pertanto, ogni enumeratore restituito ha il proprio stato e le modifiche apportate in un enumeratore non avranno effetto su un'altra.</span><span class="sxs-lookup"><span data-stu-id="467ae-1659">Thus, each returned enumerator has its own state and changes in one enumerator will not affect another.</span></span> <span data-ttu-id="467ae-1660">Il `Interlocked.CompareExchange` metodo viene usato per garantire l'operazione thread-safe.</span><span class="sxs-lookup"><span data-stu-id="467ae-1660">The `Interlocked.CompareExchange` method is used to ensure thread-safe operation.</span></span>

<span data-ttu-id="467ae-1661">I `from` parametri `to` e vengono convertiti in campi nella classe Enumerable.</span><span class="sxs-lookup"><span data-stu-id="467ae-1661">The `from` and `to` parameters are turned into fields in the enumerable class.</span></span> <span data-ttu-id="467ae-1662">Poiché `from` viene modificato nel blocco iteratore, viene introdotto `__from` un campo aggiuntivo per conservare il valore iniziale assegnato a `from` in ogni enumeratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1662">Because `from` is modified in the iterator block, an additional `__from` field is introduced to hold the initial value given to `from` in each enumerator.</span></span>

<span data-ttu-id="467ae-1663">Il `MoveNext` metodo genera un' `InvalidOperationException` eccezione se viene chiamato quando `__state` è `0`.</span><span class="sxs-lookup"><span data-stu-id="467ae-1663">The `MoveNext` method throws an `InvalidOperationException` if it is called when `__state` is `0`.</span></span> <span data-ttu-id="467ae-1664">In questo modo si impedisce l'utilizzo dell'oggetto enumerabile come oggetto enumeratore `GetEnumerator`senza prima chiamare.</span><span class="sxs-lookup"><span data-stu-id="467ae-1664">This protects against use of the enumerable object as an enumerator object without first calling `GetEnumerator`.</span></span>

<span data-ttu-id="467ae-1665">Nell'esempio seguente viene illustrata una semplice classe Tree.</span><span class="sxs-lookup"><span data-stu-id="467ae-1665">The following example shows a simple tree class.</span></span> <span data-ttu-id="467ae-1666">La `Tree<T>` classe`GetEnumerator` implementa il metodo usando un iteratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1666">The `Tree<T>` class implements its `GetEnumerator` method using an iterator.</span></span> <span data-ttu-id="467ae-1667">L'iteratore enumera gli elementi della struttura ad albero in ordine infisso.</span><span class="sxs-lookup"><span data-stu-id="467ae-1667">The iterator enumerates the elements of the tree in infix order.</span></span>

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

<span data-ttu-id="467ae-1668">Il `GetEnumerator` metodo può essere convertito in un'istanza di una classe di enumeratori generata dal compilatore che incapsula il codice nel blocco iteratore, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="467ae-1668">The `GetEnumerator` method can be translated into an instantiation of a compiler-generated enumerator class that encapsulates the code in the iterator block, as shown in the following.</span></span>

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

<span data-ttu-id="467ae-1669">I temporaries generati dal compilatore usati nelle `foreach` istruzioni vengono rimossi `__left` nei campi e `__right` dell'oggetto enumeratore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1669">The compiler generated temporaries used in the `foreach` statements are lifted into the `__left` and `__right` fields of the enumerator object.</span></span> <span data-ttu-id="467ae-1670">Il `__state` campo dell'oggetto enumeratore viene aggiornato con attenzione, in modo `Dispose()` che il metodo corretto venga chiamato correttamente se viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1670">The `__state` field of the enumerator object is carefully updated so that the correct `Dispose()` method will be called correctly if an exception is thrown.</span></span> <span data-ttu-id="467ae-1671">Si noti che non è possibile scrivere il codice tradotto con istruzioni `foreach` semplici.</span><span class="sxs-lookup"><span data-stu-id="467ae-1671">Note that it is not possible to write the translated code with simple `foreach` statements.</span></span>

## <a name="async-functions"></a><span data-ttu-id="467ae-1672">Funzioni asincrone</span><span class="sxs-lookup"><span data-stu-id="467ae-1672">Async functions</span></span>

<span data-ttu-id="467ae-1673">Un metodo ([Methods](classes.md#methods)) o una funzione anonima ([espressioni di funzione anonima](expressions.md#anonymous-function-expressions)) con il `async` modificatore è denominato ***funzione asincrona***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1673">A method ([Methods](classes.md#methods)) or anonymous function ([Anonymous function expressions](expressions.md#anonymous-function-expressions)) with the `async` modifier is called an ***async function***.</span></span> <span data-ttu-id="467ae-1674">In generale, il termine ***Async*** viene usato per descrivere qualsiasi tipo di funzione con il `async` modificatore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1674">In general, the term ***async*** is used to describe any kind of function that has the `async` modifier.</span></span>

<span data-ttu-id="467ae-1675">Si tratta di un errore in fase di compilazione per l'elenco di parametri formali di una funzione `ref` asincrona `out` per specificare i parametri o.</span><span class="sxs-lookup"><span data-stu-id="467ae-1675">It is a compile-time error for the formal parameter list of an async function to specify any `ref` or `out` parameters.</span></span>

<span data-ttu-id="467ae-1676">Il *return_type* di un metodo asincrono deve essere `void` o un ***tipo di attività***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1676">The *return_type* of an async method must be either `void` or a ***task type***.</span></span> <span data-ttu-id="467ae-1677">I tipi di attività `System.Threading.Tasks.Task` sono e i tipi `System.Threading.Tasks.Task<T>`costruiti da.</span><span class="sxs-lookup"><span data-stu-id="467ae-1677">The task types are `System.Threading.Tasks.Task` and types constructed from `System.Threading.Tasks.Task<T>`.</span></span> <span data-ttu-id="467ae-1678">Per motivi di brevità, in questo capitolo viene fatto riferimento a questi tipi `Task` rispettivamente `Task<T>`come e.</span><span class="sxs-lookup"><span data-stu-id="467ae-1678">For the sake of brevity, in this chapter these types are referenced as `Task` and `Task<T>`, respectively.</span></span> <span data-ttu-id="467ae-1679">Un metodo asincrono che restituisce un tipo di attività viene detto restituzione di attività.</span><span class="sxs-lookup"><span data-stu-id="467ae-1679">An async method returning a task type is said to be task-returning.</span></span>

<span data-ttu-id="467ae-1680">La definizione esatta dei tipi di attività è definita dall'implementazione, ma dal punto di vista del linguaggio un tipo di attività è in uno degli Stati incompleti, Succeeded o Faulted.</span><span class="sxs-lookup"><span data-stu-id="467ae-1680">The exact definition of the task types is implementation defined, but from the language's point of view a task type is in one of the states incomplete, succeeded or faulted.</span></span> <span data-ttu-id="467ae-1681">Un'attività con errori registra un'eccezione pertinente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1681">A faulted task records a pertinent exception.</span></span> <span data-ttu-id="467ae-1682">Un oggetto `Task<T>` è riuscito a registrare un `T`risultato di tipo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1682">A succeeded `Task<T>` records a result of type `T`.</span></span> <span data-ttu-id="467ae-1683">I tipi di attività sono awaitable e possono quindi essere gli operandi di espressioni await ([espressioni await](expressions.md#await-expressions)).</span><span class="sxs-lookup"><span data-stu-id="467ae-1683">Task types are awaitable, and can therefore be the operands of await expressions ([Await expressions](expressions.md#await-expressions)).</span></span>

<span data-ttu-id="467ae-1684">Una chiamata di funzione asincrona ha la possibilità di sospendere la valutazione per mezzo delle espressioni await ([await](expressions.md#await-expressions)) nel corpo.</span><span class="sxs-lookup"><span data-stu-id="467ae-1684">An async function invocation has the ability to suspend evaluation by means of await expressions ([Await expressions](expressions.md#await-expressions)) in its body.</span></span> <span data-ttu-id="467ae-1685">La valutazione può essere ripresa in un secondo momento nel punto in cui viene sospesa l'espressione await per mezzo di un ***delegato di ripresa***.</span><span class="sxs-lookup"><span data-stu-id="467ae-1685">Evaluation may later be resumed at the point of the suspending await expression by means of a ***resumption delegate***.</span></span> <span data-ttu-id="467ae-1686">Il delegato di ripresa è di `System.Action`tipo e, quando viene richiamato, la valutazione della chiamata della funzione asincrona riprenderà dall'espressione await da dove era stata interrotta.</span><span class="sxs-lookup"><span data-stu-id="467ae-1686">The resumption delegate is of type `System.Action`, and when it is invoked, evaluation of the async function invocation will resume from the await expression where it left off.</span></span> <span data-ttu-id="467ae-1687">Il chiamante ***corrente*** di una chiamata di funzione asincrona è il chiamante originale se la chiamata di funzione non è mai stata sospesa o il chiamante più recente del delegato di ripresa in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="467ae-1687">The ***current caller*** of an async function invocation is the original caller if the function invocation has never been suspended, or the most recent caller of the resumption delegate otherwise.</span></span>

### <a name="evaluation-of-a-task-returning-async-function"></a><span data-ttu-id="467ae-1688">Valutazione di una funzione asincrona che restituisce un'attività</span><span class="sxs-lookup"><span data-stu-id="467ae-1688">Evaluation of a task-returning async function</span></span>

<span data-ttu-id="467ae-1689">La chiamata di una funzione asincrona che restituisce un'attività causa la generazione di un'istanza del tipo di attività restituito.</span><span class="sxs-lookup"><span data-stu-id="467ae-1689">Invocation of a task-returning async function causes an instance of the returned task type to be generated.</span></span> <span data-ttu-id="467ae-1690">Questa operazione viene definita ***attività return*** della funzione asincrona.</span><span class="sxs-lookup"><span data-stu-id="467ae-1690">This is called the ***return task*** of the async function.</span></span> <span data-ttu-id="467ae-1691">L'attività inizialmente si trova in uno stato incompleto.</span><span class="sxs-lookup"><span data-stu-id="467ae-1691">The task is initially in an incomplete state.</span></span>

<span data-ttu-id="467ae-1692">Il corpo della funzione asincrona viene quindi valutato fino a quando non viene sospeso (raggiungendo un'espressione await) o termina, in cui il controllo del punto viene restituito al chiamante, insieme all'attività return.</span><span class="sxs-lookup"><span data-stu-id="467ae-1692">The async function body is then evaluated until it is either suspended (by reaching an await expression) or terminates, at which point control is returned to the caller, along with the return task.</span></span>

<span data-ttu-id="467ae-1693">Quando il corpo della funzione asincrona termina, l'attività return viene spostata allo stato incompleto:</span><span class="sxs-lookup"><span data-stu-id="467ae-1693">When the body of the async function terminates, the return task is moved out of the incomplete state:</span></span>

*  <span data-ttu-id="467ae-1694">Se il corpo della funzione termina in seguito al raggiungimento di un'istruzione return o alla fine del corpo, qualsiasi valore di risultato viene registrato nell'attività return, che viene inserita in uno stato succeeded.</span><span class="sxs-lookup"><span data-stu-id="467ae-1694">If the function body terminates as the result of reaching a return statement or the end of the body, any result value is recorded in the return task, which is put into a succeeded state.</span></span>
*  <span data-ttu-id="467ae-1695">Se il corpo della funzione termina come risultato di un'eccezione non rilevata ([istruzione throw](statements.md#the-throw-statement)), l'eccezione viene registrata nell'attività return, che viene inserita in uno stato di errore.</span><span class="sxs-lookup"><span data-stu-id="467ae-1695">If the function body terminates as the result of an uncaught exception ([The throw statement](statements.md#the-throw-statement)) the exception is recorded in the return task which is put into a faulted state.</span></span>

### <a name="evaluation-of-a-void-returning-async-function"></a><span data-ttu-id="467ae-1696">Valutazione di una funzione asincrona che restituisce void</span><span class="sxs-lookup"><span data-stu-id="467ae-1696">Evaluation of a void-returning async function</span></span>

<span data-ttu-id="467ae-1697">Se il tipo restituito della funzione asincrona è `void`, la valutazione differisce dal precedente nel modo seguente: Poiché non viene restituita alcuna attività, la funzione comunica invece il completamento e le eccezioni al ***contesto di sincronizzazione***del thread corrente.</span><span class="sxs-lookup"><span data-stu-id="467ae-1697">If the return type of the async function is `void`, evaluation differs from the above in the following way: Because no task is returned, the function instead communicates completion and exceptions to the current thread's ***synchronization context***.</span></span> <span data-ttu-id="467ae-1698">La definizione esatta del contesto di sincronizzazione è dipendente dall'implementazione, ma è una rappresentazione "Where" del thread corrente in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="467ae-1698">The exact definition of synchronization context is implementation-dependent, but is a representation of "where" the current thread is running.</span></span> <span data-ttu-id="467ae-1699">Il contesto di sincronizzazione riceve una notifica quando inizia la valutazione di una funzione asincrona che restituisce void, viene completata correttamente o causa la generazione di un'eccezione non rilevata.</span><span class="sxs-lookup"><span data-stu-id="467ae-1699">The synchronization context is notified when evaluation of a void-returning async function commences, completes successfully, or causes an uncaught exception to be thrown.</span></span>

<span data-ttu-id="467ae-1700">Ciò consente al contesto di tenere traccia del numero di funzioni asincrone che restituiscono void e di decidere come propagare le eccezioni in uscita.</span><span class="sxs-lookup"><span data-stu-id="467ae-1700">This allows the context to keep track of how many void-returning async functions are running under it, and to decide how to propagate exceptions coming out of them.</span></span>
