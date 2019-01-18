---
ms.openlocfilehash: 0a09585f4f885647230354c66a2449abb7ef1f44
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229613"
---
# <a name="interfaces"></a><span data-ttu-id="da30a-101">Interfacce</span><span class="sxs-lookup"><span data-stu-id="da30a-101">Interfaces</span></span>

<span data-ttu-id="da30a-102">Un'interfaccia definisce un contratto.</span><span class="sxs-lookup"><span data-stu-id="da30a-102">An interface defines a contract.</span></span> <span data-ttu-id="da30a-103">Una classe o struct che implementa un'interfaccia deve aderire al rispettivo contratto.</span><span class="sxs-lookup"><span data-stu-id="da30a-103">A class or struct that implements an interface must adhere to its contract.</span></span> <span data-ttu-id="da30a-104">Un'interfaccia può ereditare da più interfacce di base e una classe o struct può implementare più interfacce.</span><span class="sxs-lookup"><span data-stu-id="da30a-104">An interface may inherit from multiple base interfaces, and a class or struct may implement multiple interfaces.</span></span>

<span data-ttu-id="da30a-105">Le interfacce possono contenere metodi, proprietà, eventi e indicizzatori.</span><span class="sxs-lookup"><span data-stu-id="da30a-105">Interfaces can contain methods, properties, events, and indexers.</span></span> <span data-ttu-id="da30a-106">L'interfaccia stessa non fornisce le implementazioni per i membri che lo definisce.</span><span class="sxs-lookup"><span data-stu-id="da30a-106">The interface itself does not provide implementations for the members that it defines.</span></span> <span data-ttu-id="da30a-107">L'interfaccia specifica semplicemente i membri che devono essere forniti dalle classi o struct che implementa l'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-107">The interface merely specifies the members that must be supplied by classes or structs that implement the interface.</span></span>

## <a name="interface-declarations"></a><span data-ttu-id="da30a-108">Dichiarazioni di interfaccia</span><span class="sxs-lookup"><span data-stu-id="da30a-108">Interface declarations</span></span>

<span data-ttu-id="da30a-109">Un' *interface_declaration* è un *type_declaration* ([dichiarazioni di tipo](namespaces.md#type-declarations)) che dichiara un nuovo tipo di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-109">An *interface_declaration* is a *type_declaration* ([Type declarations](namespaces.md#type-declarations)) that declares a new interface type.</span></span>

```antlr
interface_declaration
    : attributes? interface_modifier* 'partial'? 'interface'
      identifier variant_type_parameter_list? interface_base?
      type_parameter_constraints_clause* interface_body ';'?
    ;
```

<span data-ttu-id="da30a-110">Un' *interface_declaration* costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), seguito da un set facoltativo di *interface_modifier*s ([modificatori dell'interfaccia](interfaces.md#interface-modifiers)), seguito da un oggetto facoltativo `partial` modificatore, seguita dalla parola chiave `interface` e una *identificatore* che denomina l'interfaccia, seguita da un oggetto facoltativo *variant_type_parameter_list* specification ([elenchi di parametri di tipo variante](interfaces.md#variant-type-parameter-lists)), seguito da un oggetto facoltativo *interface_base* Specifica ([interfacce di Base](interfaces.md#base-interfaces)), seguito da un oggetto facoltativo *type_parameter_constraints_clause*specifica s ([digitare i vincoli del parametro](classes.md#type-parameter-constraints)) , seguita da un *interface_body* ([interfaccia corpo](interfaces.md#interface-body)), seguito facoltativamente da un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="da30a-110">An *interface_declaration* consists of an optional set of *attributes* ([Attributes](attributes.md)), followed by an optional set of *interface_modifier*s ([Interface modifiers](interfaces.md#interface-modifiers)), followed by an optional `partial` modifier, followed by the keyword `interface` and an *identifier* that names the interface, followed by an optional *variant_type_parameter_list* specification ([Variant type parameter lists](interfaces.md#variant-type-parameter-lists)), followed by an optional *interface_base* specification ([Base interfaces](interfaces.md#base-interfaces)), followed by an optional *type_parameter_constraints_clause*s specification ([Type parameter constraints](classes.md#type-parameter-constraints)), followed by an *interface_body* ([Interface body](interfaces.md#interface-body)), optionally followed by a semicolon.</span></span>

### <a name="interface-modifiers"></a><span data-ttu-id="da30a-111">Modificatori di interfaccia</span><span class="sxs-lookup"><span data-stu-id="da30a-111">Interface modifiers</span></span>

<span data-ttu-id="da30a-112">Un' *interface_declaration* può includere una sequenza dei modificatori di interfaccia:</span><span class="sxs-lookup"><span data-stu-id="da30a-112">An *interface_declaration* may optionally include a sequence of interface modifiers:</span></span>

```antlr
interface_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | interface_modifier_unsafe
    ;
```

<span data-ttu-id="da30a-113">È un errore in fase di compilazione per più volte lo stesso modificatore in una dichiarazione di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-113">It is a compile-time error for the same modifier to appear multiple times in an interface declaration.</span></span>

<span data-ttu-id="da30a-114">Il `new` modificatore è consentito solo in interfacce definite all'interno di una classe.</span><span class="sxs-lookup"><span data-stu-id="da30a-114">The `new` modifier is only permitted on interfaces defined within a class.</span></span> <span data-ttu-id="da30a-115">Specifica che l'interfaccia nasconde un membro ereditato con lo stesso nome, come descritto in [il nuovo modificatore](classes.md#the-new-modifier).</span><span class="sxs-lookup"><span data-stu-id="da30a-115">It specifies that the interface hides an inherited member by the same name, as described in [The new modifier](classes.md#the-new-modifier).</span></span>

<span data-ttu-id="da30a-116">Il `public`, `protected`, `internal`, e `private` modificatori determinano l'accessibilità dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-116">The `public`, `protected`, `internal`, and `private` modifiers control the accessibility of the interface.</span></span> <span data-ttu-id="da30a-117">A seconda del contesto in cui si verifica la dichiarazione dell'interfaccia, solo alcuni di questi modificatori è ammessa ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)).</span><span class="sxs-lookup"><span data-stu-id="da30a-117">Depending on the context in which the interface declaration occurs, only some of these modifiers may be permitted ([Declared accessibility](basic-concepts.md#declared-accessibility)).</span></span>

### <a name="partial-modifier"></a><span data-ttu-id="da30a-118">Modificatore parziale</span><span class="sxs-lookup"><span data-stu-id="da30a-118">Partial modifier</span></span>

<span data-ttu-id="da30a-119">Il `partial` modificatore indica che questo *interface_declaration* è una dichiarazione di tipo parziale.</span><span class="sxs-lookup"><span data-stu-id="da30a-119">The `partial` modifier indicates that this *interface_declaration* is a partial type declaration.</span></span> <span data-ttu-id="da30a-120">Più dichiarazioni di interfaccia parziale con lo stesso nome all'interno di una dichiarazione dello spazio dei nomi o tipo di inclusione combinano al form una dichiarazione di interfaccia, in base alle regole specificate [i tipi parziali](classes.md#partial-types).</span><span class="sxs-lookup"><span data-stu-id="da30a-120">Multiple partial interface declarations with the same name within an enclosing namespace or type declaration combine to form one interface declaration, following the rules specified in [Partial types](classes.md#partial-types).</span></span>

### <a name="variant-type-parameter-lists"></a><span data-ttu-id="da30a-121">Elenchi di parametri di tipo Variant</span><span class="sxs-lookup"><span data-stu-id="da30a-121">Variant type parameter lists</span></span>

<span data-ttu-id="da30a-122">Gli elenchi di parametri di tipo Variant possono verificarsi solo sui tipi di interfaccia e delegato.</span><span class="sxs-lookup"><span data-stu-id="da30a-122">Variant type parameter lists can only occur on interface and delegate types.</span></span> <span data-ttu-id="da30a-123">La differenza rispetto a ordinario *type_parameter_list*s è facoltativa *variance_annotation* su ogni parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="da30a-123">The difference from ordinary *type_parameter_list*s is the optional *variance_annotation* on each type parameter.</span></span>

```antlr
variant_type_parameter_list
    : '<' variant_type_parameters '>'
    ;

variant_type_parameters
    : attributes? variance_annotation? type_parameter
    | variant_type_parameters ',' attributes? variance_annotation? type_parameter
    ;

variance_annotation
    : 'in'
    | 'out'
    ;
```

<span data-ttu-id="da30a-124">Se l'annotazione della varianza `out`, il parametro di tipo è detto ***covariante***.</span><span class="sxs-lookup"><span data-stu-id="da30a-124">If the variance annotation is `out`, the type parameter is said to be ***covariant***.</span></span> <span data-ttu-id="da30a-125">Se l'annotazione della varianza `in`, il parametro di tipo è detto ***controvariante***.</span><span class="sxs-lookup"><span data-stu-id="da30a-125">If the variance annotation is `in`, the type parameter is said to be ***contravariant***.</span></span> <span data-ttu-id="da30a-126">Se è presente alcuna annotazione della varianza, il parametro di tipo viene detto ***invariante***.</span><span class="sxs-lookup"><span data-stu-id="da30a-126">If there is no variance annotation, the type parameter is said to be ***invariant***.</span></span>

<span data-ttu-id="da30a-127">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="da30a-127">In the example</span></span>
```csharp
interface C<out X, in Y, Z> 
{
  X M(Y y);
  Z P { get; set; }
}
```
<span data-ttu-id="da30a-128">`X` è covariante, `Y` è controvariante e `Z` è invariante.</span><span class="sxs-lookup"><span data-stu-id="da30a-128">`X` is covariant, `Y` is contravariant and `Z` is invariant.</span></span>

#### <a name="variance-safety"></a><span data-ttu-id="da30a-129">Protezione della varianza</span><span class="sxs-lookup"><span data-stu-id="da30a-129">Variance safety</span></span>

<span data-ttu-id="da30a-130">L'occorrenza delle annotazioni di varianza nell'elenco dei parametri di tipo di un tipo consente di limitare le posizioni in cui possono verificarsi i tipi all'interno della dichiarazione di tipo.</span><span class="sxs-lookup"><span data-stu-id="da30a-130">The occurrence of variance annotations in the type parameter list of a type restricts the places where types can occur within the type declaration.</span></span>

<span data-ttu-id="da30a-131">Un tipo `T` viene ***output unsafe*** se una delle seguenti condizioni:</span><span class="sxs-lookup"><span data-stu-id="da30a-131">A type `T` is ***output-unsafe*** if one of the following holds:</span></span>

*  <span data-ttu-id="da30a-132">`T` è un parametro di tipo controvariante</span><span class="sxs-lookup"><span data-stu-id="da30a-132">`T` is a contravariant type parameter</span></span>
*  <span data-ttu-id="da30a-133">`T` è un tipo di matrice con un tipo di elemento di output non sicuri</span><span class="sxs-lookup"><span data-stu-id="da30a-133">`T` is an array type with an output-unsafe element type</span></span>
*  <span data-ttu-id="da30a-134">`T` è un tipo interfaccia o delegato `S<A1,...,Ak>` costruito da un tipo generico `S<X1,...,Xk>` where per almeno uno `Ai` contiene uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="da30a-134">`T` is an interface or delegate type `S<A1,...,Ak>` constructed from a generic type `S<X1,...,Xk>` where for at least one `Ai` one of the following holds:</span></span>
   * <span data-ttu-id="da30a-135">`Xi` è covariante o invarianti e `Ai` è output unsafe.</span><span class="sxs-lookup"><span data-stu-id="da30a-135">`Xi` is covariant or invariant and `Ai` is output-unsafe.</span></span>
   * <span data-ttu-id="da30a-136">`Xi` è controvariante o invariante e `Ai` input-safe.</span><span class="sxs-lookup"><span data-stu-id="da30a-136">`Xi` is contravariant or invariant and `Ai` is input-safe.</span></span>
   
<span data-ttu-id="da30a-137">Un tipo `T` viene ***input unsafe*** se una delle seguenti condizioni:</span><span class="sxs-lookup"><span data-stu-id="da30a-137">A type `T` is ***input-unsafe*** if one of the following holds:</span></span>

*  <span data-ttu-id="da30a-138">`T` è un parametro di tipo covariante</span><span class="sxs-lookup"><span data-stu-id="da30a-138">`T` is a covariant type parameter</span></span>
*  <span data-ttu-id="da30a-139">`T` è un tipo di matrice con un tipo di elemento di tipo unsafe input</span><span class="sxs-lookup"><span data-stu-id="da30a-139">`T` is an array type with an input-unsafe element type</span></span>
*  <span data-ttu-id="da30a-140">`T` è un tipo interfaccia o delegato `S<A1,...,Ak>` costruito da un tipo generico `S<X1,...,Xk>` where per almeno uno `Ai` contiene uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="da30a-140">`T` is an interface or delegate type `S<A1,...,Ak>` constructed from a generic type `S<X1,...,Xk>` where for at least one `Ai` one of the following holds:</span></span>
   * <span data-ttu-id="da30a-141">`Xi` è covariante o invarianti e `Ai` è di tipo unsafe input.</span><span class="sxs-lookup"><span data-stu-id="da30a-141">`Xi` is covariant or invariant and `Ai` is input-unsafe.</span></span>
   * <span data-ttu-id="da30a-142">`Xi` è controvariante o invariante e `Ai` è output unsafe.</span><span class="sxs-lookup"><span data-stu-id="da30a-142">`Xi` is contravariant or invariant and `Ai` is output-unsafe.</span></span>

<span data-ttu-id="da30a-143">Intuitivamente, un tipo di output-unsafe non è consentito in una posizione di output e un tipo unsafe-input non è consentito in una posizione di input.</span><span class="sxs-lookup"><span data-stu-id="da30a-143">Intuitively, an output-unsafe type is prohibited in an output position, and an input-unsafe type is prohibited in an input position.</span></span>

<span data-ttu-id="da30a-144">È un tipo ***output-safe*** se non è unsafe, output e ***indipendente input*** se non è tipo unsafe input.</span><span class="sxs-lookup"><span data-stu-id="da30a-144">A type is ***output-safe*** if it is not output-unsafe, and ***input-safe*** if it is not input-unsafe.</span></span>

#### <a name="variance-conversion"></a><span data-ttu-id="da30a-145">Conversione della varianza</span><span class="sxs-lookup"><span data-stu-id="da30a-145">Variance conversion</span></span>

<span data-ttu-id="da30a-146">Lo scopo di annotazioni della varianza è fornire più flessibili (ma ancora indipendente dai tipi) conversioni in tipi di interfaccia e delegato.</span><span class="sxs-lookup"><span data-stu-id="da30a-146">The purpose of variance annotations is to provide for more lenient (but still type safe) conversions to interface and delegate types.</span></span> <span data-ttu-id="da30a-147">A questo fine le definizioni di implicito ([conversioni implicite](conversions.md#implicit-conversions)) e le conversioni esplicite ([conversioni esplicite](conversions.md#explicit-conversions)) apportare Usa la nozione di convertibilità della varianza, che viene definita come segue:</span><span class="sxs-lookup"><span data-stu-id="da30a-147">To this end the definitions of implicit ([Implicit conversions](conversions.md#implicit-conversions)) and explicit conversions ([Explicit conversions](conversions.md#explicit-conversions)) make use of the notion of variance-convertibility, which is defined as follows:</span></span>

<span data-ttu-id="da30a-148">Un tipo `T<A1,...,An>` convertibile la varianza è a un tipo `T<B1,...,Bn>` se `T` è un'interfaccia o un tipo delegato dichiarato con i parametri di tipo variant `T<X1,...,Xn>`e per ogni parametro di tipo variant `Xi` uno dei seguenti contiene:</span><span class="sxs-lookup"><span data-stu-id="da30a-148">A type `T<A1,...,An>` is variance-convertible to a type `T<B1,...,Bn>` if `T` is either an interface or a delegate type declared with the variant type parameters `T<X1,...,Xn>`, and for each variant type parameter `Xi` one of the following holds:</span></span>

*  <span data-ttu-id="da30a-149">`Xi` è una covariante e non esiste una conversione implicita di identità o di riferimento da `Ai` a `Bi`</span><span class="sxs-lookup"><span data-stu-id="da30a-149">`Xi` is covariant and an implicit reference or identity conversion exists from `Ai` to `Bi`</span></span>
*  <span data-ttu-id="da30a-150">`Xi` è controvariante e un riferimento implicito o non esiste conversione di identità da `Bi` a `Ai`</span><span class="sxs-lookup"><span data-stu-id="da30a-150">`Xi` is contravariant and an implicit reference or identity conversion exists from `Bi` to `Ai`</span></span>
*  <span data-ttu-id="da30a-151">`Xi` è invariante e un'identità non esiste conversione da `Ai` a `Bi`</span><span class="sxs-lookup"><span data-stu-id="da30a-151">`Xi` is invariant and an identity conversion exists from `Ai` to `Bi`</span></span>

### <a name="base-interfaces"></a><span data-ttu-id="da30a-152">Interfacce di base</span><span class="sxs-lookup"><span data-stu-id="da30a-152">Base interfaces</span></span>

<span data-ttu-id="da30a-153">Un'interfaccia può ereditare da zero o più tipi di interfaccia, che vengono chiamati i ***interfacce di base esplicite*** dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-153">An interface can inherit from zero or more interface types, which are called the ***explicit base interfaces*** of the interface.</span></span> <span data-ttu-id="da30a-154">Quando un'interfaccia presenta uno o più interfacce di base esplicitare, nella dichiarazione di tale interfaccia, l'identificatore di interfaccia è seguito da due punti e virgola da virgole dei tipi di interfaccia di base.</span><span class="sxs-lookup"><span data-stu-id="da30a-154">When an interface has one or more explicit base interfaces, then in the declaration of that interface, the interface identifier is followed by a colon and a comma separated list of base interface types.</span></span>

```antlr
interface_base
    : ':' interface_type_list
    ;
```

<span data-ttu-id="da30a-155">Per un tipo di interfaccia costruita, interfacce di base esplicite costituite prendendo le dichiarazioni di interfaccia di base esplicite nella dichiarazione del tipo generico e sostituzione, per ogni *type_parameter* nell'interfaccia di base dichiarazione corrispondente *type_argument* del tipo costruito.</span><span class="sxs-lookup"><span data-stu-id="da30a-155">For a constructed interface type, the explicit base interfaces are formed by taking the explicit base interface declarations on the generic type declaration, and substituting, for each *type_parameter* in the base interface declaration, the corresponding *type_argument* of the constructed type.</span></span>

<span data-ttu-id="da30a-156">Le interfacce di base esplicitare di un'interfaccia devono essere accessibile almeno quanto l'interfaccia stessa ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).</span><span class="sxs-lookup"><span data-stu-id="da30a-156">The explicit base interfaces of an interface must be at least as accessible as the interface itself ([Accessibility constraints](basic-concepts.md#accessibility-constraints)).</span></span> <span data-ttu-id="da30a-157">Ad esempio, si tratta di un errore in fase di compilazione per specificare una `private` o `internal` dell'interfaccia nel *interface_base* di un `public` interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-157">For example, it is a compile-time error to specify a `private` or `internal` interface in the *interface_base* of a `public` interface.</span></span>

<span data-ttu-id="da30a-158">Tratta di un errore in fase di compilazione per un'interfaccia ereditare direttamente o indirettamente da se stessa.</span><span class="sxs-lookup"><span data-stu-id="da30a-158">It is a compile-time error for an interface to directly or indirectly inherit from itself.</span></span>

<span data-ttu-id="da30a-159">Il ***interfacce di base*** di un'interfaccia sono interfacce di base esplicite e le interfacce di base.</span><span class="sxs-lookup"><span data-stu-id="da30a-159">The ***base interfaces*** of an interface are the explicit base interfaces and their base interfaces.</span></span> <span data-ttu-id="da30a-160">In altre parole, il set di interfacce di base è la chiusura transitiva completata di interfacce di base esplicite, le interfacce di base esplicite e così via.</span><span class="sxs-lookup"><span data-stu-id="da30a-160">In other words, the set of base interfaces is the complete transitive closure of the explicit base interfaces, their explicit base interfaces, and so on.</span></span> <span data-ttu-id="da30a-161">Un'interfaccia eredita tutti i membri delle relative interfacce di base.</span><span class="sxs-lookup"><span data-stu-id="da30a-161">An interface inherits all members of its base interfaces.</span></span> <span data-ttu-id="da30a-162">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="da30a-162">In the example</span></span>
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

interface IListBox: IControl
{
    void SetItems(string[] items);
}

interface IComboBox: ITextBox, IListBox {}
```
<span data-ttu-id="da30a-163">le interfacce di base di `IComboBox` vengono `IControl`, `ITextBox`, e `IListBox`.</span><span class="sxs-lookup"><span data-stu-id="da30a-163">the base interfaces of `IComboBox` are `IControl`, `ITextBox`, and `IListBox`.</span></span>

<span data-ttu-id="da30a-164">In altre parole, il `IComboBox` precedente interfaccia eredita membri `SetText` e `SetItems` nonché `Paint`.</span><span class="sxs-lookup"><span data-stu-id="da30a-164">In other words, the `IComboBox` interface above inherits members `SetText` and `SetItems` as well as `Paint`.</span></span>

<span data-ttu-id="da30a-165">Ogni interfaccia di base di un'interfaccia deve essere indipendente output ([safety varianza](interfaces.md#variance-safety)).</span><span class="sxs-lookup"><span data-stu-id="da30a-165">Every base interface of an interface must be output-safe ([Variance safety](interfaces.md#variance-safety)).</span></span> <span data-ttu-id="da30a-166">Una classe o struct che implementa un'interfaccia in modo implicito anche implementa tutte le interfacce di base.</span><span class="sxs-lookup"><span data-stu-id="da30a-166">A class or struct that implements an interface also implicitly implements all of the interface's base interfaces.</span></span>

### <a name="interface-body"></a><span data-ttu-id="da30a-167">Corpo di interfaccia</span><span class="sxs-lookup"><span data-stu-id="da30a-167">Interface body</span></span>

<span data-ttu-id="da30a-168">Il *interface_body* di un'interfaccia definisce i membri dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-168">The *interface_body* of an interface defines the members of the interface.</span></span>

```antlr
interface_body
    : '{' interface_member_declaration* '}'
    ;
```

## <a name="interface-members"></a><span data-ttu-id="da30a-169">Membri di interfaccia</span><span class="sxs-lookup"><span data-stu-id="da30a-169">Interface members</span></span>

<span data-ttu-id="da30a-170">I membri di un'interfaccia sono i membri ereditati da interfacce di base e i membri dichiarati dall'interfaccia stessa.</span><span class="sxs-lookup"><span data-stu-id="da30a-170">The members of an interface are the members inherited from the base interfaces and the members declared by the interface itself.</span></span>

```antlr
interface_member_declaration
    : interface_method_declaration
    | interface_property_declaration
    | interface_event_declaration
    | interface_indexer_declaration
    ;
```

<span data-ttu-id="da30a-171">Una dichiarazione di interfaccia può dichiarare zero o più membri.</span><span class="sxs-lookup"><span data-stu-id="da30a-171">An interface declaration may declare zero or more members.</span></span> <span data-ttu-id="da30a-172">I membri di un'interfaccia devono essere metodi, proprietà, eventi o gli indicizzatori.</span><span class="sxs-lookup"><span data-stu-id="da30a-172">The members of an interface must be methods, properties, events, or indexers.</span></span> <span data-ttu-id="da30a-173">Un'interfaccia non può contenere costanti, campi, operatori, costruttori di istanze, distruttori o tipi, né è un'interfaccia contengono membri statici di alcun tipo.</span><span class="sxs-lookup"><span data-stu-id="da30a-173">An interface cannot contain constants, fields, operators, instance constructors, destructors, or types, nor can an interface contain static members of any kind.</span></span>

<span data-ttu-id="da30a-174">Tutti i membri di interfaccia dispongono in modo implicito accesso pubblico.</span><span class="sxs-lookup"><span data-stu-id="da30a-174">All interface members implicitly have public access.</span></span> <span data-ttu-id="da30a-175">È un errore in fase di compilazione per le dichiarazioni di membro di interfaccia includere tutti i modificatori.</span><span class="sxs-lookup"><span data-stu-id="da30a-175">It is a compile-time error for interface member declarations to include any modifiers.</span></span> <span data-ttu-id="da30a-176">In particolare, i membri di interfacce non possono essere dichiarati con i modificatori `abstract`, `public`, `protected`, `internal`, `private`, `virtual`, `override`, o `static`.</span><span class="sxs-lookup"><span data-stu-id="da30a-176">In particular, interfaces members cannot be declared with the modifiers `abstract`, `public`, `protected`, `internal`, `private`, `virtual`, `override`, or `static`.</span></span>

<span data-ttu-id="da30a-177">L'esempio</span><span class="sxs-lookup"><span data-stu-id="da30a-177">The example</span></span>
```csharp
public delegate void StringListEvent(IStringList sender);

public interface IStringList
{
    void Add(string s);
    int Count { get; }
    event StringListEvent Changed;
    string this[int index] { get; set; }
}
```
<span data-ttu-id="da30a-178">dichiara un'interfaccia che contiene tutti i possibili tipi di membri: Un metodo, una proprietà, un evento e un indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="da30a-178">declares an interface that contains one each of the possible kinds of members: A method, a property, an event, and an indexer.</span></span>

<span data-ttu-id="da30a-179">Un' *interface_declaration* crea un nuovo spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)) e il *interface_member_declaration*s contenuti immediatamente il *interface_declaration* introducono nuovi membri in tale spazio di dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="da30a-179">An *interface_declaration* creates a new declaration space ([Declarations](basic-concepts.md#declarations)), and the *interface_member_declaration*s immediately contained by the *interface_declaration* introduce new members into this declaration space.</span></span> <span data-ttu-id="da30a-180">Le regole seguenti riguardano *interface_member_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="da30a-180">The following rules apply to *interface_member_declaration*s:</span></span>

*  <span data-ttu-id="da30a-181">Il nome di un metodo deve essere diverso dai nomi di tutte le proprietà e gli eventi dichiarati nell'interfaccia stessa.</span><span class="sxs-lookup"><span data-stu-id="da30a-181">The name of a method must differ from the names of all properties and events declared in the same interface.</span></span> <span data-ttu-id="da30a-182">Inoltre, la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) di un metodo deve essere diversa dalle firme di tutti gli altri metodi dichiarati nell'interfaccia stessa, e due i metodi dichiarati nell'interfaccia stessa potrebbero non avere firme che differiscono unicamente per `ref` e `out`.</span><span class="sxs-lookup"><span data-stu-id="da30a-182">In addition, the signature ([Signatures and overloading](basic-concepts.md#signatures-and-overloading)) of a method must differ from the signatures of all other methods declared in the same interface, and two methods declared in the same interface may not have signatures that differ solely by `ref` and `out`.</span></span>
*  <span data-ttu-id="da30a-183">Il nome di una proprietà o evento deve essere diverso dai nomi di tutti gli altri membri dichiarati nell'interfaccia stessa.</span><span class="sxs-lookup"><span data-stu-id="da30a-183">The name of a property or event must differ from the names of all other members declared in the same interface.</span></span>
*  <span data-ttu-id="da30a-184">È necessario che la firma di un indicizzatore sia diversa dalle firme di tutti gli altri indicizzatori dichiarati nella stessa interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-184">The signature of an indexer must differ from the signatures of all other indexers declared in the same interface.</span></span>

<span data-ttu-id="da30a-185">I membri ereditati di un'interfaccia sono specificamente non fanno parte dello spazio di dichiarazione dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-185">The inherited members of an interface are specifically not part of the declaration space of the interface.</span></span> <span data-ttu-id="da30a-186">Di conseguenza, un'interfaccia può dichiarare un membro con lo stesso nome o firma di un membro ereditato.</span><span class="sxs-lookup"><span data-stu-id="da30a-186">Thus, an interface is allowed to declare a member with the same name or signature as an inherited member.</span></span> <span data-ttu-id="da30a-187">In questo caso, viene definito il membro di interfaccia derivata per nascondere il membro di interfaccia di base.</span><span class="sxs-lookup"><span data-stu-id="da30a-187">When this occurs, the derived interface member is said to hide the base interface member.</span></span> <span data-ttu-id="da30a-188">Se si nasconde un membro ereditato non è considerato un errore, ma comporta il compilatore emette un avviso.</span><span class="sxs-lookup"><span data-stu-id="da30a-188">Hiding an inherited member is not considered an error, but it does cause the compiler to issue a warning.</span></span> <span data-ttu-id="da30a-189">Per eliminare l'avviso, è necessario includere la dichiarazione del membro di interfaccia derivata una `new` modificatore per indicare che il membro derivato deve nascondere il membro di base.</span><span class="sxs-lookup"><span data-stu-id="da30a-189">To suppress the warning, the declaration of the derived interface member must include a `new` modifier to indicate that the derived member is intended to hide the base member.</span></span> <span data-ttu-id="da30a-190">Questo argomento viene descritto dettagliatamente nella [nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance).</span><span class="sxs-lookup"><span data-stu-id="da30a-190">This topic is discussed further in [Hiding through inheritance](basic-concepts.md#hiding-through-inheritance).</span></span>

<span data-ttu-id="da30a-191">Se un `new` modificatore viene inclusa in una dichiarazione che non nasconde un membro ereditato, viene generato un avviso in tal senso.</span><span class="sxs-lookup"><span data-stu-id="da30a-191">If a `new` modifier is included in a declaration that doesn't hide an inherited member, a warning is issued to that effect.</span></span> <span data-ttu-id="da30a-192">Questo avviso viene eliminato tramite la rimozione di `new` modificatore.</span><span class="sxs-lookup"><span data-stu-id="da30a-192">This warning is suppressed by removing the `new` modifier.</span></span>

<span data-ttu-id="da30a-193">Si noti che i membri nella classe `object` non lo sono, in senso stretto, membri di un'interfaccia ([membri di interfaccia](interfaces.md#interface-members)).</span><span class="sxs-lookup"><span data-stu-id="da30a-193">Note that the members in class `object` are not, strictly speaking, members of any interface ([Interface members](interfaces.md#interface-members)).</span></span> <span data-ttu-id="da30a-194">Tuttavia, i membri nella classe `object` sono disponibili tramite ricerca di membri in qualsiasi tipo di interfaccia ([ricerca di membri](expressions.md#member-lookup)).</span><span class="sxs-lookup"><span data-stu-id="da30a-194">However, the members in class `object` are available via member lookup in any interface type ([Member lookup](expressions.md#member-lookup)).</span></span>

### <a name="interface-methods"></a><span data-ttu-id="da30a-195">Metodi di interfaccia</span><span class="sxs-lookup"><span data-stu-id="da30a-195">Interface methods</span></span>

<span data-ttu-id="da30a-196">I metodi di interfaccia vengono dichiarati usando *interface_method_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="da30a-196">Interface methods are declared using *interface_method_declaration*s:</span></span>

```antlr
interface_method_declaration
    : attributes? 'new'? return_type identifier type_parameter_list
      '(' formal_parameter_list? ')' type_parameter_constraints_clause* ';'
    ;
```

<span data-ttu-id="da30a-197">Il *attributi*, *return_type*, *identificatore*, e *formal_parameter_list* hanno lo stesso di una dichiarazione di metodo di interfaccia vale a dire come quelli di una dichiarazione di metodo in una classe ([metodi](classes.md#methods)).</span><span class="sxs-lookup"><span data-stu-id="da30a-197">The *attributes*, *return_type*, *identifier*, and *formal_parameter_list* of an interface method declaration have the same meaning as those of a method declaration in a class ([Methods](classes.md#methods)).</span></span> <span data-ttu-id="da30a-198">Una dichiarazione di metodo di interfaccia non è consentita specificare un corpo del metodo e la dichiarazione di conseguenza termina sempre con un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="da30a-198">An interface method declaration is not permitted to specify a method body, and the declaration therefore always ends with a semicolon.</span></span>

<span data-ttu-id="da30a-199">Ogni tipo di parametro formali di un metodo di interfaccia deve essere indipendente input ([safety varianza](interfaces.md#variance-safety)), e il tipo restituito deve essere `void` o output-safe.</span><span class="sxs-lookup"><span data-stu-id="da30a-199">Each formal parameter type of an interface method must be input-safe ([Variance safety](interfaces.md#variance-safety)), and the return type must be either `void` or output-safe.</span></span> <span data-ttu-id="da30a-200">Inoltre, ogni vincolo di tipo classe, il vincolo di tipo di interfaccia e il vincolo di parametro di tipo per qualsiasi parametro di tipo del metodo deve essere indipendente input.</span><span class="sxs-lookup"><span data-stu-id="da30a-200">Furthermore, each class type constraint, interface type constraint and type parameter constraint on any type parameter of the method must be input-safe.</span></span>

<span data-ttu-id="da30a-201">Queste regole assicurano che eventuali covariante o controvariante utilizzo dell'interfaccia rimane indipendente dai tipi.</span><span class="sxs-lookup"><span data-stu-id="da30a-201">These rules ensure that any covariant or contravariant usage of the interface remains type-safe.</span></span> <span data-ttu-id="da30a-202">Ad esempio,</span><span class="sxs-lookup"><span data-stu-id="da30a-202">For example,</span></span>
```csharp
interface I<out T> { void M<U>() where U : T; }
```
<span data-ttu-id="da30a-203">non è valido perché l'utilizzo delle `T` come un vincolo del parametro di tipo in `U` non input-safe.</span><span class="sxs-lookup"><span data-stu-id="da30a-203">is illegal because the usage of `T` as a type parameter constraint on `U` is not input-safe.</span></span>

<span data-ttu-id="da30a-204">Precedenti questa restrizione non è possibile violare la sicurezza di tipo nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="da30a-204">Were this restriction not in place it would be possible to violate type safety in the following manner:</span></span>
```csharp
class B {}
class D : B{}
class E : B {}
class C : I<D> { public void M<U>() {...} }
...
I<B> b = new C();
b.M<E>();
```
<span data-ttu-id="da30a-205">Si tratta in realtà una chiamata a `C.M<E>`.</span><span class="sxs-lookup"><span data-stu-id="da30a-205">This is actually a call to `C.M<E>`.</span></span> <span data-ttu-id="da30a-206">Ma tale chiamata richiede che `E` derivano da `D`, pertanto l'indipendenza dai tipi venga violata qui.</span><span class="sxs-lookup"><span data-stu-id="da30a-206">But that call requires that `E` derive from `D`, so type safety would be violated here.</span></span>

### <a name="interface-properties"></a><span data-ttu-id="da30a-207">Proprietà dell'interfaccia</span><span class="sxs-lookup"><span data-stu-id="da30a-207">Interface properties</span></span>

<span data-ttu-id="da30a-208">Le proprietà dell'interfaccia vengono dichiarate usando *interface_property_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="da30a-208">Interface properties are declared using *interface_property_declaration*s:</span></span>

```antlr
interface_property_declaration
    : attributes? 'new'? type identifier '{' interface_accessors '}'
    ;

interface_accessors
    : attributes? 'get' ';'
    | attributes? 'set' ';'
    | attributes? 'get' ';' attributes? 'set' ';'
    | attributes? 'set' ';' attributes? 'get' ';'
    ;
```

<span data-ttu-id="da30a-209">Il *attributi*, *tipo*, e *identificatore* di una dichiarazione di proprietà di interfaccia abbia lo stesso significato di quelli di una dichiarazione di proprietà in una classe ([ Proprietà](classes.md#properties)).</span><span class="sxs-lookup"><span data-stu-id="da30a-209">The *attributes*, *type*, and *identifier* of an interface property declaration have the same meaning as those of a property declaration in a class ([Properties](classes.md#properties)).</span></span>

<span data-ttu-id="da30a-210">Funzioni di accesso di una dichiarazione di proprietà dell'interfaccia corrispondono alle funzioni di accesso di una dichiarazione di proprietà di classe ([funzioni di accesso](classes.md#accessors)), ad eccezione del fatto che il corpo della funzione di accesso deve essere sempre un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="da30a-210">The accessors of an interface property declaration correspond to the accessors of a class property declaration ([Accessors](classes.md#accessors)), except that the accessor body must always be a semicolon.</span></span> <span data-ttu-id="da30a-211">Di conseguenza, le funzioni di accesso indica semplicemente se la proprietà è di lettura / scrittura, sola lettura o in sola lettura.</span><span class="sxs-lookup"><span data-stu-id="da30a-211">Thus, the accessors simply indicate whether the property is read-write, read-only, or write-only.</span></span>

<span data-ttu-id="da30a-212">Il tipo di una proprietà dell'interfaccia deve essere output-safe se è presente una funzione di accesso get e deve essere l'input indipendente se è presente una funzione di accesso set.</span><span class="sxs-lookup"><span data-stu-id="da30a-212">The type of an interface property must be output-safe if there is a get accessor, and must be input-safe if there is a set accessor.</span></span>

### <a name="interface-events"></a><span data-ttu-id="da30a-213">Eventi di interfaccia</span><span class="sxs-lookup"><span data-stu-id="da30a-213">Interface events</span></span>

<span data-ttu-id="da30a-214">Gli eventi di interfaccia vengono dichiarati usando *interface_event_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="da30a-214">Interface events are declared using *interface_event_declaration*s:</span></span>

```antlr
interface_event_declaration
    : attributes? 'new'? 'event' type identifier ';'
    ;
```

<span data-ttu-id="da30a-215">Il *attributi*, *tipo*, e *identificatore* di una dichiarazione di evento di interfaccia abbia lo stesso significato di quelli di una dichiarazione di evento in una classe ([eventi ](classes.md#events)).</span><span class="sxs-lookup"><span data-stu-id="da30a-215">The *attributes*, *type*, and *identifier* of an interface event declaration have the same meaning as those of an event declaration in a class ([Events](classes.md#events)).</span></span>

<span data-ttu-id="da30a-216">Il tipo di un evento di interfaccia deve essere di input-safe.</span><span class="sxs-lookup"><span data-stu-id="da30a-216">The type of an interface event must be input-safe.</span></span>

### <a name="interface-indexers"></a><span data-ttu-id="da30a-217">Indicizzatori nelle interfacce</span><span class="sxs-lookup"><span data-stu-id="da30a-217">Interface indexers</span></span>

<span data-ttu-id="da30a-218">Indicizzatori nelle interfacce vengono dichiarate utilizzando *interface_indexer_declaration*s:</span><span class="sxs-lookup"><span data-stu-id="da30a-218">Interface indexers are declared using *interface_indexer_declaration*s:</span></span>

```antlr
interface_indexer_declaration
    : attributes? 'new'? type 'this' '[' formal_parameter_list ']' '{' interface_accessors '}'
    ;
```

<span data-ttu-id="da30a-219">Il *attributi*, *tipo*, e *formal_parameter_list* di una dichiarazione di interfaccia dell'indicizzatore abbia lo stesso significato di quelli di una dichiarazione di indicizzatore in una classe ([ Gli indicizzatori](classes.md#indexers)).</span><span class="sxs-lookup"><span data-stu-id="da30a-219">The *attributes*, *type*, and *formal_parameter_list* of an interface indexer declaration have the same meaning as those of an indexer declaration in a class ([Indexers](classes.md#indexers)).</span></span>

<span data-ttu-id="da30a-220">Le funzioni di accesso di una dichiarazione di indicizzatore interfaccia corrispondono alle funzioni di accesso di una dichiarazione di indicizzatore di classe ([indicizzatori](classes.md#indexers)), ad eccezione del fatto che il corpo della funzione di accesso deve essere sempre un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="da30a-220">The accessors of an interface indexer declaration correspond to the accessors of a class indexer declaration ([Indexers](classes.md#indexers)), except that the accessor body must always be a semicolon.</span></span> <span data-ttu-id="da30a-221">Di conseguenza, le funzioni di accesso indica semplicemente se l'indicizzatore è in lettura / scrittura, sola lettura o in sola lettura.</span><span class="sxs-lookup"><span data-stu-id="da30a-221">Thus, the accessors simply indicate whether the indexer is read-write, read-only, or write-only.</span></span>

<span data-ttu-id="da30a-222">Tutti i tipi di parametri formali di un indicizzatore di interfaccia devono essere di input-safe.</span><span class="sxs-lookup"><span data-stu-id="da30a-222">All the formal parameter types of an interface indexer must be input-safe .</span></span> <span data-ttu-id="da30a-223">Inoltre, qualsiasi `out` o `ref` i tipi di parametro formale devono anche essere output-safe.</span><span class="sxs-lookup"><span data-stu-id="da30a-223">In addition, any `out` or `ref` formal parameter types must also be output-safe.</span></span> <span data-ttu-id="da30a-224">Si noti che anche `out` parametri devono essere di input-safe, a causa di una limitazione della piattaforma sottostante di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="da30a-224">Note that even `out` parameters are required to be input-safe, due to a limitation of the underlying execution platform.</span></span>

<span data-ttu-id="da30a-225">Il tipo di un indicizzatore di interfaccia deve essere output-safe se è presente una funzione di accesso get e deve essere l'input indipendente se è presente una funzione di accesso set.</span><span class="sxs-lookup"><span data-stu-id="da30a-225">The type of an interface indexer must be output-safe if there is a get accessor, and must be input-safe if there is a set accessor.</span></span>

### <a name="interface-member-access"></a><span data-ttu-id="da30a-226">Accesso ai membri di interfaccia</span><span class="sxs-lookup"><span data-stu-id="da30a-226">Interface member access</span></span>

<span data-ttu-id="da30a-227">I membri di interfaccia sono accessibili tramite l'accesso ai membri ([accesso al membro](expressions.md#member-access)) e l'accesso dell'indicizzatore ([accesso all'indicizzatore](expressions.md#indexer-access)) espressioni nel formato `I.M` e `I[A]`, dove `I` è un tipo di interfaccia, `M` è un metodo, proprietà o evento di quel tipo di interfaccia, e `A` è un elenco di argomenti dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="da30a-227">Interface members are accessed through member access ([Member access](expressions.md#member-access)) and indexer access ([Indexer access](expressions.md#indexer-access)) expressions of the form `I.M` and `I[A]`, where `I` is an interface type, `M` is a method, property, or event of that interface type, and `A` is an indexer argument list.</span></span>

<span data-ttu-id="da30a-228">Per le interfacce che sono strettamente a ereditarietà singola (ogni interfaccia nella catena di ereditarietà ha esattamente zero o a un'interfaccia di base diretta), gli effetti della ricerca di membri ([ricerca di membri](expressions.md#member-lookup)), chiamata al metodo ([ Le chiamate ai metodi](expressions.md#method-invocations)) e l'accesso dell'indicizzatore ([accesso all'indicizzatore](expressions.md#indexer-access)) le regole sono esattamente identico a quello di classi e struct: Più derivati Nascondi membri meno derivata i membri con lo stesso nome o firma.</span><span class="sxs-lookup"><span data-stu-id="da30a-228">For interfaces that are strictly single-inheritance (each interface in the inheritance chain has exactly zero or one direct base interface), the effects of the member lookup ([Member lookup](expressions.md#member-lookup)), method invocation ([Method invocations](expressions.md#method-invocations)), and indexer access ([Indexer access](expressions.md#indexer-access)) rules are exactly the same as for classes and structs: More derived members hide less derived members with the same name or signature.</span></span> <span data-ttu-id="da30a-229">Tuttavia, per le interfacce di ereditarietà multipla, le ambiguità possono verificarsi quando due o più interfacce di base correlate dichiarano membri con lo stesso nome o firma.</span><span class="sxs-lookup"><span data-stu-id="da30a-229">However, for multiple-inheritance interfaces, ambiguities can occur when two or more unrelated base interfaces declare members with the same name or signature.</span></span> <span data-ttu-id="da30a-230">Questa sezione illustra alcuni esempi di situazioni di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="da30a-230">This section shows several examples of such situations.</span></span> <span data-ttu-id="da30a-231">In tutti i casi, è possibile utilizzare cast espliciti per risolvere le ambiguità.</span><span class="sxs-lookup"><span data-stu-id="da30a-231">In all cases, explicit casts can be used to resolve the ambiguities.</span></span>

<span data-ttu-id="da30a-232">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="da30a-232">In the example</span></span>
```csharp
interface IList
{
    int Count { get; set; }
}

interface ICounter
{
    void Count(int i);
}

interface IListCounter: IList, ICounter {}

class C
{
    void Test(IListCounter x) {
        x.Count(1);                  // Error
        x.Count = 1;                 // Error
        ((IList)x).Count = 1;        // Ok, invokes IList.Count.set
        ((ICounter)x).Count(1);      // Ok, invokes ICounter.Count
    }
}
```
<span data-ttu-id="da30a-233">le prime due istruzioni causano errori di compilazione perché la ricerca di membri ([ricerca di membri](expressions.md#member-lookup)) della `Count` in `IListCounter` è ambiguo.</span><span class="sxs-lookup"><span data-stu-id="da30a-233">the first two statements cause compile-time errors because the member lookup ([Member lookup](expressions.md#member-lookup)) of `Count` in `IListCounter` is ambiguous.</span></span> <span data-ttu-id="da30a-234">Come illustrato nell'esempio, eseguendo il cast viene risolta l'ambiguità `x` nel tipo di interfaccia di base appropriato.</span><span class="sxs-lookup"><span data-stu-id="da30a-234">As illustrated by the example, the ambiguity is resolved by casting `x` to the appropriate base interface type.</span></span> <span data-ttu-id="da30a-235">Tali cast non hanno costi alcuna fase di esecuzione, ovvero sono costituite semplicemente visualizzazione dell'istanza come un tipo meno derivato in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="da30a-235">Such casts have no run-time costs—they merely consist of viewing the instance as a less derived type at compile-time.</span></span>

<span data-ttu-id="da30a-236">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="da30a-236">In the example</span></span>
```csharp
interface IInteger
{
    void Add(int i);
}

interface IDouble
{
    void Add(double d);
}

interface INumber: IInteger, IDouble {}

class C
{
    void Test(INumber n) {
        n.Add(1);                // Invokes IInteger.Add
        n.Add(1.0);              // Only IDouble.Add is applicable
        ((IInteger)n).Add(1);    // Only IInteger.Add is a candidate
        ((IDouble)n).Add(1);     // Only IDouble.Add is a candidate
    }
}
```
<span data-ttu-id="da30a-237">la chiamata `n.Add(1)` seleziona `IInteger.Add` applicando le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="da30a-237">the invocation `n.Add(1)` selects `IInteger.Add` by applying the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="da30a-238">Analogamente alla chiamata `n.Add(1.0)` seleziona `IDouble.Add`.</span><span class="sxs-lookup"><span data-stu-id="da30a-238">Similarly the invocation `n.Add(1.0)` selects `IDouble.Add`.</span></span> <span data-ttu-id="da30a-239">Quando si inseriscono cast espliciti, non vi è solo un candidato (metodo) e pertanto non esistono ambiguità.</span><span class="sxs-lookup"><span data-stu-id="da30a-239">When explicit casts are inserted, there is only one candidate method, and thus no ambiguity.</span></span>

<span data-ttu-id="da30a-240">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="da30a-240">In the example</span></span>
```csharp
interface IBase
{
    void F(int i);
}

interface ILeft: IBase
{
    new void F(int i);
}

interface IRight: IBase
{
    void G();
}

interface IDerived: ILeft, IRight {}

class A
{
    void Test(IDerived d) {
        d.F(1);                 // Invokes ILeft.F
        ((IBase)d).F(1);        // Invokes IBase.F
        ((ILeft)d).F(1);        // Invokes ILeft.F
        ((IRight)d).F(1);       // Invokes IBase.F
    }
}
```
<span data-ttu-id="da30a-241">il `IBase.F` membro viene nascosto per il `ILeft.F` membro.</span><span class="sxs-lookup"><span data-stu-id="da30a-241">the `IBase.F` member is hidden by the `ILeft.F` member.</span></span> <span data-ttu-id="da30a-242">La chiamata `d.F(1)` consente di selezionare `ILeft.F`, anche se `IBase.F` sembra non essere nascosto nel percorso di accesso che assista `IRight`.</span><span class="sxs-lookup"><span data-stu-id="da30a-242">The invocation `d.F(1)` thus selects `ILeft.F`, even though `IBase.F` appears to not be hidden in the access path that leads through `IRight`.</span></span>

<span data-ttu-id="da30a-243">La regola intuitiva per nascondere elementi nelle interfacce di ereditarietà multipla è semplicemente la seguente: Se un membro viene nascosto in qualsiasi percorso di accesso, è nascosto in tutti i percorsi di accesso.</span><span class="sxs-lookup"><span data-stu-id="da30a-243">The intuitive rule for hiding in multiple-inheritance interfaces is simply this: If a member is hidden in any access path, it is hidden in all access paths.</span></span> <span data-ttu-id="da30a-244">Poiché il percorso di accesso da `IDerived` per `ILeft` a `IBase` nasconde `IBase.F`, il membro viene nascosto anche nel percorso di accesso da `IDerived` per `IRight` a `IBase`.</span><span class="sxs-lookup"><span data-stu-id="da30a-244">Because the access path from `IDerived` to `ILeft` to `IBase` hides `IBase.F`, the member is also hidden in the access path from `IDerived` to `IRight` to `IBase`.</span></span>

## <a name="fully-qualified-interface-member-names"></a><span data-ttu-id="da30a-245">Nomi dei membri di interfaccia completo</span><span class="sxs-lookup"><span data-stu-id="da30a-245">Fully qualified interface member names</span></span>

<span data-ttu-id="da30a-246">Un membro di interfaccia viene a volte fa riferimento al relativo ***nome completo***.</span><span class="sxs-lookup"><span data-stu-id="da30a-246">An interface member is sometimes referred to by its ***fully qualified name***.</span></span> <span data-ttu-id="da30a-247">Il nome completo di un membro di interfaccia costituita dal nome dell'interfaccia in cui il membro è dichiarato, seguito da un punto, seguito dal nome del membro.</span><span class="sxs-lookup"><span data-stu-id="da30a-247">The fully qualified name of an interface member consists of the name of the interface in which the member is declared, followed by a dot, followed by the name of the member.</span></span> <span data-ttu-id="da30a-248">Il nome completo di un membro fa riferimento all'interfaccia in cui viene dichiarato il membro.</span><span class="sxs-lookup"><span data-stu-id="da30a-248">The fully qualified name of a member references the interface in which the member is declared.</span></span> <span data-ttu-id="da30a-249">Ad esempio, date le dichiarazioni</span><span class="sxs-lookup"><span data-stu-id="da30a-249">For example, given the declarations</span></span>
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}
```
<span data-ttu-id="da30a-250">il nome completo del `Paint` viene `IControl.Paint` e specificare il nome completo del `SetText` è `ITextBox.SetText`.</span><span class="sxs-lookup"><span data-stu-id="da30a-250">the fully qualified name of `Paint` is `IControl.Paint` and the fully qualified name of `SetText` is `ITextBox.SetText`.</span></span>

<span data-ttu-id="da30a-251">Nell'esempio precedente, non è possibile fare riferimento a `Paint` come `ITextBox.Paint`.</span><span class="sxs-lookup"><span data-stu-id="da30a-251">In the example above, it is not possible to refer to `Paint` as `ITextBox.Paint`.</span></span>

<span data-ttu-id="da30a-252">Quando un'interfaccia fa parte di uno spazio dei nomi, il nome completo di un membro di interfaccia include lo spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="da30a-252">When an interface is part of a namespace, the fully qualified name of an interface member includes the namespace name.</span></span> <span data-ttu-id="da30a-253">Esempio:</span><span class="sxs-lookup"><span data-stu-id="da30a-253">For example</span></span>
```csharp
namespace System
{
    public interface ICloneable
    {
        object Clone();
    }
}
```

<span data-ttu-id="da30a-254">Questo caso, il nome completo del `Clone` metodo `System.ICloneable.Clone`.</span><span class="sxs-lookup"><span data-stu-id="da30a-254">Here, the fully qualified name of the `Clone` method is `System.ICloneable.Clone`.</span></span>

## <a name="interface-implementations"></a><span data-ttu-id="da30a-255">Implementazioni di interfacce</span><span class="sxs-lookup"><span data-stu-id="da30a-255">Interface implementations</span></span>

<span data-ttu-id="da30a-256">Le interfacce possono essere implementate da classi e struct.</span><span class="sxs-lookup"><span data-stu-id="da30a-256">Interfaces may be implemented by classes and structs.</span></span> <span data-ttu-id="da30a-257">Per indicare che una classe o struct direttamente implementa un'interfaccia, l'identificatore di interfaccia è incluso nell'elenco delle classi base della classe o struct.</span><span class="sxs-lookup"><span data-stu-id="da30a-257">To indicate that a class or struct directly implements an interface, the interface identifier is included in the base class list of the class or struct.</span></span> <span data-ttu-id="da30a-258">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="da30a-258">For example:</span></span>
```csharp
interface ICloneable
{
    object Clone();
}

interface IComparable
{
    int CompareTo(object other);
}

class ListEntry: ICloneable, IComparable
{
    public object Clone() {...}
    public int CompareTo(object other) {...}
}
```

<span data-ttu-id="da30a-259">Una classe o struct che implementa direttamente anche direttamente un'interfaccia implementa tutte le interfacce di base in modo implicito.</span><span class="sxs-lookup"><span data-stu-id="da30a-259">A class or struct that directly implements an interface also directly implements all of the interface's base interfaces implicitly.</span></span> <span data-ttu-id="da30a-260">Questo vale anche se la classe o struct non vengono elencati in modo esplicito tutte le interfacce di base nell'elenco delle classi base.</span><span class="sxs-lookup"><span data-stu-id="da30a-260">This is true even if the class or struct doesn't explicitly list all base interfaces in the base class list.</span></span> <span data-ttu-id="da30a-261">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="da30a-261">For example:</span></span>
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

class TextBox: ITextBox
{
    public void Paint() {...}
    public void SetText(string text) {...}
}
```

<span data-ttu-id="da30a-262">In questo caso, classe `TextBox` implementa sia `IControl` e `ITextBox`.</span><span class="sxs-lookup"><span data-stu-id="da30a-262">Here, class `TextBox` implements both `IControl` and `ITextBox`.</span></span>

<span data-ttu-id="da30a-263">Quando una classe `C` direttamente implementa un'interfaccia, tutte le classi derivate da C anche implementarla l'interfaccia in modo implicito.</span><span class="sxs-lookup"><span data-stu-id="da30a-263">When a class `C` directly implements an interface, all classes derived from C also implement the interface implicitly.</span></span> <span data-ttu-id="da30a-264">Le interfacce di base specificate in una dichiarazione di classe possono essere tipi di interfaccia costruita ([i tipi costruiti](types.md#constructed-types)).</span><span class="sxs-lookup"><span data-stu-id="da30a-264">The base interfaces specified in a class declaration can be constructed interface types ([Constructed types](types.md#constructed-types)).</span></span> <span data-ttu-id="da30a-265">Un'interfaccia di base non può essere un parametro di tipo di per sé, ma può comportare i parametri di tipo che rientrano nell'ambito.</span><span class="sxs-lookup"><span data-stu-id="da30a-265">A base interface cannot be a type parameter on its own, though it can involve the type parameters that are in scope.</span></span> <span data-ttu-id="da30a-266">Il codice seguente viene illustrato come una classe può implementare ed estendere i tipi costruiti:</span><span class="sxs-lookup"><span data-stu-id="da30a-266">The following code illustrates how a class can implement and extend constructed types:</span></span>
```csharp
class C<U,V> {}

interface I1<V> {}

class D: C<string,int>, I1<string> {}

class E<T>: C<int,T>, I1<T> {}
```

<span data-ttu-id="da30a-267">Le interfacce di base di una dichiarazione di classe generica devono soddisfare la regola di univocità descritto nella [univocità delle interfacce implementate](interfaces.md#uniqueness-of-implemented-interfaces).</span><span class="sxs-lookup"><span data-stu-id="da30a-267">The base interfaces of a generic class declaration must satisfy the uniqueness rule described in [Uniqueness of implemented interfaces](interfaces.md#uniqueness-of-implemented-interfaces).</span></span>

### <a name="explicit-interface-member-implementations"></a><span data-ttu-id="da30a-268">Implementazioni di membri di interfaccia esplicita</span><span class="sxs-lookup"><span data-stu-id="da30a-268">Explicit interface member implementations</span></span>

<span data-ttu-id="da30a-269">Ai fini dell'implementazione di interfacce, una classe o struct possono dichiarare ***implementazioni esplicite dell'interfaccia membro***.</span><span class="sxs-lookup"><span data-stu-id="da30a-269">For purposes of implementing interfaces, a class or struct may declare ***explicit interface member implementations***.</span></span> <span data-ttu-id="da30a-270">Un'implementazione di membro di interfaccia esplicita è una dichiarazione di metodo, proprietà, eventi o dell'indicizzatore che fa riferimento a un nome di membro completo dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-270">An explicit interface member implementation is a method, property, event, or indexer declaration that references a fully qualified interface member name.</span></span> <span data-ttu-id="da30a-271">Esempio:</span><span class="sxs-lookup"><span data-stu-id="da30a-271">For example</span></span>
```csharp
interface IList<T>
{
    T[] GetElements();
}

interface IDictionary<K,V>
{
    V this[K key];
    void Add(K key, V value);
}

class List<T>: IList<T>, IDictionary<int,T>
{
    T[] IList<T>.GetElements() {...}
    T IDictionary<int,T>.this[int index] {...}
    void IDictionary<int,T>.Add(int index, T value) {...}
}
```

<span data-ttu-id="da30a-272">Di seguito `IDictionary<int,T>.this` e `IDictionary<int,T>.Add` è interfacce esplicite le implementazioni di membri.</span><span class="sxs-lookup"><span data-stu-id="da30a-272">Here `IDictionary<int,T>.this` and `IDictionary<int,T>.Add` are explicit interface member implementations.</span></span>

<span data-ttu-id="da30a-273">In alcuni casi, il nome di un membro di interfaccia potrebbe non essere appropriato per la classe di implementazione, in cui il membro può essere implementato tramite l'implementazione di membro di interfaccia esplicita.</span><span class="sxs-lookup"><span data-stu-id="da30a-273">In some cases, the name of an interface member may not be appropriate for the implementing class, in which case the interface member may be implemented using explicit interface member implementation.</span></span> <span data-ttu-id="da30a-274">Probabilmente è necessario implementare una classe che implementa un'astrazione di file, ad esempio, un `Close` funzione membro che ha l'effetto di rilascio della risorsa di file e implementare le `Dispose` metodo il `IDisposable` usando un'interfaccia esplicita dell'interfaccia implementazione di un membro:</span><span class="sxs-lookup"><span data-stu-id="da30a-274">A class implementing a file abstraction, for example, would likely implement a `Close` member function that has the effect of releasing the file resource, and implement the `Dispose` method of the `IDisposable` interface using explicit interface member implementation:</span></span>
```csharp
interface IDisposable
{
    void Dispose();
}

class MyFile: IDisposable
{
    void IDisposable.Dispose() {
        Close();
    }

    public void Close() {
        // Do what's necessary to close the file
        System.GC.SuppressFinalize(this);
    }
}
```

<span data-ttu-id="da30a-275">Non è possibile accedere a un'implementazione di membro di interfaccia esplicita tramite il nome completo in una chiamata al metodo, l'accesso alle proprietà o l'accesso dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="da30a-275">It is not possible to access an explicit interface member implementation through its fully qualified name in a method invocation, property access, or indexer access.</span></span> <span data-ttu-id="da30a-276">Un'implementazione di membro di interfaccia esplicita è accessibile solo tramite un'istanza dell'interfaccia e in tal caso fa semplicemente il nome del membro.</span><span class="sxs-lookup"><span data-stu-id="da30a-276">An explicit interface member implementation can only be accessed through an interface instance, and is in that case referenced simply by its member name.</span></span>

<span data-ttu-id="da30a-277">Tratta di un errore in fase di compilazione per un'implementazione di membro di interfaccia esplicita includere modificatori di accesso, ed è un errore in fase di compilazione per includere i modificatori `abstract`, `virtual`, `override`, o `static`.</span><span class="sxs-lookup"><span data-stu-id="da30a-277">It is a compile-time error for an explicit interface member implementation to include access modifiers, and it is a compile-time error to include the modifiers `abstract`, `virtual`, `override`, or `static`.</span></span>

<span data-ttu-id="da30a-278">Implementazioni esplicite dell'interfaccia membro hanno caratteristiche di accessibilità diversa rispetto ad altri membri.</span><span class="sxs-lookup"><span data-stu-id="da30a-278">Explicit interface member implementations have different accessibility characteristics than other members.</span></span> <span data-ttu-id="da30a-279">Poiché tali implementazioni mai sono accessibili tramite il relativo nome completo in una chiamata al metodo o un accesso a proprietà, sono in un certo senso privato.</span><span class="sxs-lookup"><span data-stu-id="da30a-279">Because explicit interface member implementations are never accessible through their fully qualified name in a method invocation or a property access, they are in a sense private.</span></span> <span data-ttu-id="da30a-280">Tuttavia, dal momento che sia possibile accedervi tramite un'istanza dell'interfaccia, sono in un certo senso anche pubblico.</span><span class="sxs-lookup"><span data-stu-id="da30a-280">However, since they can be accessed through an interface instance, they are in a sense also public.</span></span>

<span data-ttu-id="da30a-281">Implementazioni esplicite dell'interfaccia membro hanno due scopi principali:</span><span class="sxs-lookup"><span data-stu-id="da30a-281">Explicit interface member implementations serve two primary purposes:</span></span>

*  <span data-ttu-id="da30a-282">Poiché tali implementazioni non sono accessibili tramite le istanze di classe o struct, consentono di implementazioni di interfaccia da escludere dall'interfaccia pubblica di una classe o struct.</span><span class="sxs-lookup"><span data-stu-id="da30a-282">Because explicit interface member implementations are not accessible through class or struct instances, they allow interface implementations to be excluded from the public interface of a class or struct.</span></span> <span data-ttu-id="da30a-283">Ciò è particolarmente utile quando una classe o struct implementa un'interfaccia interna di alcun interesse a un consumer di quella classe o struct.</span><span class="sxs-lookup"><span data-stu-id="da30a-283">This is particularly useful when a class or struct implements an internal interface that is of no interest to a consumer of that class or struct.</span></span>
*  <span data-ttu-id="da30a-284">Tali implementazioni consentono di risoluzione dell'ambiguità dei membri di interfaccia con la stessa firma.</span><span class="sxs-lookup"><span data-stu-id="da30a-284">Explicit interface member implementations allow disambiguation of interface members with the same signature.</span></span> <span data-ttu-id="da30a-285">Senza tali implementazioni sarebbe impossibile ritestare per una classe o struct avere diverse implementazioni di membri con la stessa firma dell'interfaccia e il tipo restituito, come lo sarebbe impossibile per una classe o struct qualsiasi implementazione in tutti i membri di interfaccia con la stessa firma ma con tipi restituiti diversi.</span><span class="sxs-lookup"><span data-stu-id="da30a-285">Without explicit interface member implementations it would be impossible for a class or struct to have different implementations of interface members with the same signature and return type, as would it be impossible for a class or struct to have any implementation at all of interface members with the same signature but with different return types.</span></span>

<span data-ttu-id="da30a-286">Per un'implementazione esplicita dell'interfaccia membro sia valida, la classe o struct deve denominare un'interfaccia nel relativo elenco di classi base che include il membro il cui nome completo del tipo e i tipi di parametro corrispondono esattamente a quelli dei membri di interfaccia esplicita implementazione.</span><span class="sxs-lookup"><span data-stu-id="da30a-286">For an explicit interface member implementation to be valid, the class or struct must name an interface in its base class list that contains a member whose fully qualified name, type, and parameter types exactly match those of the explicit interface member implementation.</span></span> <span data-ttu-id="da30a-287">Di conseguenza, nella classe seguente</span><span class="sxs-lookup"><span data-stu-id="da30a-287">Thus, in the following class</span></span>
```csharp
class Shape: ICloneable
{
    object ICloneable.Clone() {...}
    int IComparable.CompareTo(object other) {...}    // invalid
}
```
<span data-ttu-id="da30a-288">la dichiarazione di `IComparable.CompareTo` genera un errore in fase di compilazione in quanto `IComparable` non è elencato nell'elenco delle classi base di `Shape` e non è un'interfaccia di base di `ICloneable`.</span><span class="sxs-lookup"><span data-stu-id="da30a-288">the declaration of `IComparable.CompareTo` results in a compile-time error because `IComparable` is not listed in the base class list of `Shape` and is not a base interface of `ICloneable`.</span></span> <span data-ttu-id="da30a-289">Allo stesso modo, nelle dichiarazioni</span><span class="sxs-lookup"><span data-stu-id="da30a-289">Likewise, in the declarations</span></span>
```csharp
class Shape: ICloneable
{
    object ICloneable.Clone() {...}
}

class Ellipse: Shape
{
    object ICloneable.Clone() {...}    // invalid
}
```
<span data-ttu-id="da30a-290">la dichiarazione di `ICloneable.Clone` nelle `Ellipse` genera un errore in fase di compilazione in quanto `ICloneable` non è elencato in modo esplicito nell'elenco delle classi base di `Ellipse`.</span><span class="sxs-lookup"><span data-stu-id="da30a-290">the declaration of `ICloneable.Clone` in `Ellipse` results in a compile-time error because `ICloneable` is not explicitly listed in the base class list of `Ellipse`.</span></span>

<span data-ttu-id="da30a-291">Il nome completo di un membro di interfaccia deve fare riferimento l'interfaccia in cui è stato dichiarato il membro.</span><span class="sxs-lookup"><span data-stu-id="da30a-291">The fully qualified name of an interface member must reference the interface in which the member was declared.</span></span> <span data-ttu-id="da30a-292">Pertanto, nelle dichiarazioni</span><span class="sxs-lookup"><span data-stu-id="da30a-292">Thus, in the declarations</span></span>
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

class TextBox: ITextBox
{
    void IControl.Paint() {...}
    void ITextBox.SetText(string text) {...}
}
```
<span data-ttu-id="da30a-293">l'implementazione del membro di interfaccia esplicita `Paint` deve essere scritto come `IControl.Paint`.</span><span class="sxs-lookup"><span data-stu-id="da30a-293">the explicit interface member implementation of `Paint` must be written as `IControl.Paint`.</span></span>

### <a name="uniqueness-of-implemented-interfaces"></a><span data-ttu-id="da30a-294">Univocità delle interfacce implementate</span><span class="sxs-lookup"><span data-stu-id="da30a-294">Uniqueness of implemented interfaces</span></span>

<span data-ttu-id="da30a-295">Le interfacce implementate da una dichiarazione di tipo generico devono rimanere univoche per tutti i possibili tipi costruiti.</span><span class="sxs-lookup"><span data-stu-id="da30a-295">The interfaces implemented by a generic type declaration must remain unique for all possible constructed types.</span></span> <span data-ttu-id="da30a-296">Senza questa regola, sarebbe Impossibile determinare il metodo corretto da chiamare per determinati tipi costruiti.</span><span class="sxs-lookup"><span data-stu-id="da30a-296">Without this rule, it would be impossible to determine the correct method to call for certain constructed types.</span></span> <span data-ttu-id="da30a-297">Si supponga, ad esempio, una dichiarazione di classe generica fosse consentito deve essere scritto come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="da30a-297">For example, suppose a generic class declaration were permitted to be written as follows:</span></span>
```csharp
interface I<T>
{
    void F();
}

class X<U,V>: I<U>, I<V>                    // Error: I<U> and I<V> conflict
{
    void I<U>.F() {...}
    void I<V>.F() {...}
}
```

<span data-ttu-id="da30a-298">Sono stati ciò è consentito, sarebbe impossibile ritestare determinare il codice da eseguire nel caso seguente:</span><span class="sxs-lookup"><span data-stu-id="da30a-298">Were this permitted, it would be impossible to determine which code to execute in the following case:</span></span>
```csharp
I<int> x = new X<int,int>();
x.F();
```

<span data-ttu-id="da30a-299">Per determinare se l'elenco di interfaccia di una dichiarazione di tipo generico è valido, vengono eseguiti i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="da30a-299">To determine if the interface list of a generic type declaration is valid, the following steps are performed:</span></span>

*  <span data-ttu-id="da30a-300">Let `L` l'elenco delle interfacce specificate direttamente in una classe generica, una struct o una dichiarazione di interfaccia `C`.</span><span class="sxs-lookup"><span data-stu-id="da30a-300">Let `L` be the list of interfaces directly specified in a generic class, struct, or interface declaration `C`.</span></span>
*  <span data-ttu-id="da30a-301">Aggiungere al `L` interfacce delle interfacce già in uno qualsiasi di base `L`.</span><span class="sxs-lookup"><span data-stu-id="da30a-301">Add to `L` any base interfaces of the interfaces already in `L`.</span></span>
*  <span data-ttu-id="da30a-302">Rimuovere i duplicati da `L`.</span><span class="sxs-lookup"><span data-stu-id="da30a-302">Remove any duplicates from `L`.</span></span>
*  <span data-ttu-id="da30a-303">Se qualsiasi possibile tipo creato da costruito `C` verrebbe, dopo che gli argomenti di tipo vengono sostituiti nella `L`, le due interfacce in `L` siano identici, quindi la dichiarazione di `C` non è valido.</span><span class="sxs-lookup"><span data-stu-id="da30a-303">If any possible constructed type created from `C` would, after type arguments are substituted into `L`, cause two interfaces in `L` to be identical, then the declaration of `C` is invalid.</span></span> <span data-ttu-id="da30a-304">Le dichiarazioni di vincolo non vengono considerate durante la determinazione di tutti i possibili tipi costruiti.</span><span class="sxs-lookup"><span data-stu-id="da30a-304">Constraint declarations are not considered when determining all possible constructed types.</span></span>

<span data-ttu-id="da30a-305">Nella dichiarazione della classe `X` precedente, l'elenco delle interfacce `L` costituito `I<U>` e `I<V>`.</span><span class="sxs-lookup"><span data-stu-id="da30a-305">In the class declaration `X` above, the interface list `L` consists of `I<U>` and `I<V>`.</span></span> <span data-ttu-id="da30a-306">La dichiarazione non è valida perché qualsiasi tipo con costruito `U` e `V` dello stesso tipo comporterebbe queste due interfacce identiche tipi.</span><span class="sxs-lookup"><span data-stu-id="da30a-306">The declaration is invalid because any constructed type with `U` and `V` being the same type would cause these two interfaces to be identical types.</span></span>

<span data-ttu-id="da30a-307">È possibile che le interfacce specificate a diversi livelli di ereditarietà per unificare:</span><span class="sxs-lookup"><span data-stu-id="da30a-307">It is possible for interfaces specified at different inheritance levels to unify:</span></span>
```csharp
interface I<T>
{
    void F();
}

class Base<U>: I<U>
{
    void I<U>.F() {...}
}

class Derived<U,V>: Base<U>, I<V>    // Ok
{
    void I<V>.F() {...}
}
```

<span data-ttu-id="da30a-308">Questo codice è valido anche se `Derived<U,V>` implementa sia `I<U>` e `I<V>`.</span><span class="sxs-lookup"><span data-stu-id="da30a-308">This code is valid even though `Derived<U,V>` implements both `I<U>` and `I<V>`.</span></span> <span data-ttu-id="da30a-309">Il codice</span><span class="sxs-lookup"><span data-stu-id="da30a-309">The code</span></span>
```csharp
I<int> x = new Derived<int,int>();
x.F();
```
<span data-ttu-id="da30a-310">richiama il metodo nel `Derived`, poiché `Derived<int,int>` in modo efficace la reimplementazione `I<int>` ([prevista nuova implementazione dell'interfaccia](interfaces.md#interface-re-implementation)).</span><span class="sxs-lookup"><span data-stu-id="da30a-310">invokes the method in `Derived`, since `Derived<int,int>` effectively re-implements `I<int>` ([Interface re-implementation](interfaces.md#interface-re-implementation)).</span></span>

### <a name="implementation-of-generic-methods"></a><span data-ttu-id="da30a-311">Implementazione di metodi generici</span><span class="sxs-lookup"><span data-stu-id="da30a-311">Implementation of generic methods</span></span>

<span data-ttu-id="da30a-312">Quando un metodo generico in modo implicito implementa un metodo di interfaccia, i vincoli indicati per ogni parametro di tipo di metodo deve essere equivalente in entrambe le dichiarazioni (dopo qualsiasi tipo di interfaccia parametri vengono sostituiti con gli argomenti di tipo appropriato), dove (metodo) i parametri di tipo sono identificati da posizioni ordinali, da sinistra a destra.</span><span class="sxs-lookup"><span data-stu-id="da30a-312">When a generic method implicitly implements an interface method, the constraints given for each method type parameter must be equivalent in both declarations (after any interface type parameters are replaced with the appropriate type arguments), where method type parameters are identified by ordinal positions, left to right.</span></span>

<span data-ttu-id="da30a-313">Quando un metodo generico implementa in modo esplicito un metodo di interfaccia, tuttavia, vincoli non consentiti per il metodo di implementazione.</span><span class="sxs-lookup"><span data-stu-id="da30a-313">When a generic method explicitly implements an interface method, however, no constraints are allowed on the implementing method.</span></span> <span data-ttu-id="da30a-314">Al contrario, i vincoli vengono ereditati dal metodo di interfaccia</span><span class="sxs-lookup"><span data-stu-id="da30a-314">Instead, the constraints are inherited from the interface method</span></span>

```csharp
interface I<A,B,C>
{
    void F<T>(T t) where T: A;
    void G<T>(T t) where T: B;
    void H<T>(T t) where T: C;
}

class C: I<object,C,string>
{
    public void F<T>(T t) {...}                    // Ok
    public void G<T>(T t) where T: C {...}         // Ok
    public void H<T>(T t) where T: string {...}    // Error
}
```

<span data-ttu-id="da30a-315">Il metodo `C.F<T>` implementa in modo implicito `I<object,C,string>.F<T>`.</span><span class="sxs-lookup"><span data-stu-id="da30a-315">The method `C.F<T>` implicitly implements `I<object,C,string>.F<T>`.</span></span> <span data-ttu-id="da30a-316">In questo caso `C.F<T>` non viene richiesto (né consentito) per specificare il vincolo `T:object` poiché `object` è un vincolo su tutti i parametri di tipo implicito.</span><span class="sxs-lookup"><span data-stu-id="da30a-316">In this case, `C.F<T>` is not required (nor permitted) to specify the constraint `T:object` since `object` is an implicit constraint on all type parameters.</span></span> <span data-ttu-id="da30a-317">Il metodo `C.G<T>` implementa in modo implicito `I<object,C,string>.G<T>` perché i vincoli corrispondano a quelle nell'interfaccia, dopo che i parametri di tipo di interfaccia vengono sostituiti con gli argomenti di tipo corrispondente.</span><span class="sxs-lookup"><span data-stu-id="da30a-317">The method `C.G<T>` implicitly implements `I<object,C,string>.G<T>` because the constraints match those in the interface, after the interface type parameters are replaced with the corresponding type arguments.</span></span> <span data-ttu-id="da30a-318">Il vincolo per il metodo `C.H<T>` è un errore perché i tipi sealed (`string` in questo caso) non possono essere usati come vincoli.</span><span class="sxs-lookup"><span data-stu-id="da30a-318">The constraint for method `C.H<T>` is an error because sealed types (`string` in this case) cannot be used as constraints.</span></span> <span data-ttu-id="da30a-319">L'omissione del vincolo sarà ugualmente un errore poiché sono necessari i vincoli di implementazioni del metodo dell'interfaccia implicita in modo che corrispondano.</span><span class="sxs-lookup"><span data-stu-id="da30a-319">Omitting the constraint would also be an error since constraints of implicit interface method implementations are required to match.</span></span> <span data-ttu-id="da30a-320">Pertanto, non è possibile implementare in modo implicito `I<object,C,string>.H<T>`.</span><span class="sxs-lookup"><span data-stu-id="da30a-320">Thus, it is impossible to implicitly implement `I<object,C,string>.H<T>`.</span></span> <span data-ttu-id="da30a-321">Questo metodo di interfaccia può essere implementato solo tramite un'implementazione di membro di interfaccia esplicita:</span><span class="sxs-lookup"><span data-stu-id="da30a-321">This interface method can only be implemented using an explicit interface member implementation:</span></span>
```csharp
class C: I<object,C,string>
{
    ...

    public void H<U>(U u) where U: class {...}

    void I<object,C,string>.H<T>(T t) {
        string s = t;    // Ok
        H<T>(t);
    }
}
```

<span data-ttu-id="da30a-322">In questo esempio, l'implementazione del membro di interfaccia esplicita richiama un metodo pubblico con vincoli meno rigorosi.</span><span class="sxs-lookup"><span data-stu-id="da30a-322">In this example, the explicit interface member implementation invokes a public method having strictly weaker constraints.</span></span> <span data-ttu-id="da30a-323">Si noti che l'assegnazione da `t` al `s` è valido poiché `T` eredita un vincolo di `T:string`, anche se questo vincolo non può essere espresso nel codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="da30a-323">Note that the assignment from `t` to `s` is valid since `T` inherits a constraint of `T:string`, even though this constraint is not expressible in source code.</span></span>

### <a name="interface-mapping"></a><span data-ttu-id="da30a-324">Mapping di interfaccia</span><span class="sxs-lookup"><span data-stu-id="da30a-324">Interface mapping</span></span>

<span data-ttu-id="da30a-325">Una classe o struct deve fornire le implementazioni di tutti i membri delle interfacce che sono elencati nell'elenco delle classi base della classe o struct.</span><span class="sxs-lookup"><span data-stu-id="da30a-325">A class or struct must provide implementations of all members of the interfaces that are listed in the base class list of the class or struct.</span></span> <span data-ttu-id="da30a-326">Il processo di individuazione delle implementazioni dei membri di interfaccia in una classe o struct implementazione è detta ***mapping dell'interfaccia***.</span><span class="sxs-lookup"><span data-stu-id="da30a-326">The process of locating implementations of interface members in an implementing class or struct is known as ***interface mapping***.</span></span>

<span data-ttu-id="da30a-327">Mapping di interfaccia per una classe o struct `C` individua un'implementazione per ogni membro di ogni interfaccia specificata nell'elenco delle classi base di `C`.</span><span class="sxs-lookup"><span data-stu-id="da30a-327">Interface mapping for a class or struct `C` locates an implementation for each member of each interface specified in the base class list of `C`.</span></span> <span data-ttu-id="da30a-328">L'implementazione di un membro di interfaccia specifica `I.M`, dove `I` è l'interfaccia in cui il membro `M` viene dichiarato, viene determinato analizzando ogni classe o struct `S`, che inizia con `C` e ripetendo l'operazione per ogni classe di base successiva di `C`, fino a quando non trova una corrispondenza:</span><span class="sxs-lookup"><span data-stu-id="da30a-328">The implementation of a particular interface member `I.M`, where `I` is the interface in which the member `M` is declared, is determined by examining each class or struct `S`, starting with `C` and repeating for each successive base class of `C`, until a match is located:</span></span>

*  <span data-ttu-id="da30a-329">Se `S` contiene una dichiarazione di un'implementazione esplicita dell'interfaccia membro corrisponde `I` e `M`, quindi questo membro è l'implementazione di `I.M`.</span><span class="sxs-lookup"><span data-stu-id="da30a-329">If `S` contains a declaration of an explicit interface member implementation that matches `I` and `M`, then this member is the implementation of `I.M`.</span></span>
*  <span data-ttu-id="da30a-330">In caso contrario, se `S` contiene una dichiarazione di un membro pubblico non statico che corrisponde al `M`, quindi questo membro è l'implementazione di `I.M`.</span><span class="sxs-lookup"><span data-stu-id="da30a-330">Otherwise, if `S` contains a declaration of a non-static public member that matches `M`, then this member is the implementation of `I.M`.</span></span> <span data-ttu-id="da30a-331">Se più corrispondenze di un membro, non viene specificato quale membro è l'implementazione di `I.M`.</span><span class="sxs-lookup"><span data-stu-id="da30a-331">If more than one member matches, it is unspecified which member is the implementation of `I.M`.</span></span> <span data-ttu-id="da30a-332">Questa situazione può verificarsi solo se `S` è un tipo costruito in cui i due membri dichiarati nel tipo generico hanno firme diverse, ma gli argomenti di tipo apportare le relative firme identiche.</span><span class="sxs-lookup"><span data-stu-id="da30a-332">This situation can only occur if `S` is a constructed type where the two members as declared in the generic type have different signatures, but the type arguments make their signatures identical.</span></span>

<span data-ttu-id="da30a-333">Si verifica un errore in fase di compilazione se non è possibile individuare per tutti i membri di tutte le interfacce specificate nell'elenco delle classi base di implementazioni `C`.</span><span class="sxs-lookup"><span data-stu-id="da30a-333">A compile-time error occurs if implementations cannot be located for all members of all interfaces specified in the base class list of `C`.</span></span> <span data-ttu-id="da30a-334">Si noti che i membri di un'interfaccia includono i membri vengono ereditati da interfacce di base.</span><span class="sxs-lookup"><span data-stu-id="da30a-334">Note that the members of an interface include those members that are inherited from base interfaces.</span></span>

<span data-ttu-id="da30a-335">Ai fini del mapping dell'interfaccia, un membro della classe `A` corrisponde a un membro di interfaccia `B` quando:</span><span class="sxs-lookup"><span data-stu-id="da30a-335">For purposes of interface mapping, a class member `A` matches an interface member `B` when:</span></span>

*  <span data-ttu-id="da30a-336">`A` e `B` sono i metodi e il nome, tipo, e di elenchi di parametri formali `A` e `B` sono identici.</span><span class="sxs-lookup"><span data-stu-id="da30a-336">`A` and `B` are methods, and the name, type, and formal parameter lists of `A` and `B` are identical.</span></span>
*  <span data-ttu-id="da30a-337">`A` e `B` sono le proprietà, il nome e tipo di `A` e `B` sono identiche, e `A` sono le stesse come `B` (`A` può avere funzioni di accesso aggiuntivi se non è un'interfaccia esplicita implementazione del membro).</span><span class="sxs-lookup"><span data-stu-id="da30a-337">`A` and `B` are properties, the name and type of `A` and `B` are identical, and `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation).</span></span>
*  <span data-ttu-id="da30a-338">`A` e `B` sono eventi e il nome e il tipo di `A` e `B` sono identici.</span><span class="sxs-lookup"><span data-stu-id="da30a-338">`A` and `B` are events, and the name and type of `A` and `B` are identical.</span></span>
*  <span data-ttu-id="da30a-339">`A` e `B` sono gli indicizzatori, il tipo e gli elenchi di parametri formali della `A` e `B` sono identiche, e `A` sono le stesse come `B` (`A` può avere funzioni di accesso aggiuntivi se non è un implementazione del membro dell'interfaccia esplicita).</span><span class="sxs-lookup"><span data-stu-id="da30a-339">`A` and `B` are indexers, the type and formal parameter lists of `A` and `B` are identical, and `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation).</span></span>

<span data-ttu-id="da30a-340">Le implicazioni principali dell'algoritmo di mapping di interfaccia sono:</span><span class="sxs-lookup"><span data-stu-id="da30a-340">Notable implications of the interface mapping algorithm are:</span></span>

*  <span data-ttu-id="da30a-341">Quando si determina il membro di classe o struct che implementa un membro di interfaccia, tali implementazioni hanno la precedenza rispetto agli altri membri nella stessa classe o struct.</span><span class="sxs-lookup"><span data-stu-id="da30a-341">Explicit interface member implementations take precedence over other members in the same class or struct when determining the class or struct member that implements an interface member.</span></span>
*  <span data-ttu-id="da30a-342">Membri non pubblici e statici non vengono considerati nel mapping di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-342">Neither non-public nor static members participate in interface mapping.</span></span>

<span data-ttu-id="da30a-343">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="da30a-343">In the example</span></span>
```csharp
interface ICloneable
{
    object Clone();
}

class C: ICloneable
{
    object ICloneable.Clone() {...}
    public object Clone() {...}
}
```
<span data-ttu-id="da30a-344">il `ICloneable.Clone` appartenente `C` diventa l'implementazione di `Clone` in `ICloneable` perché tali implementazioni hanno la precedenza rispetto agli altri membri.</span><span class="sxs-lookup"><span data-stu-id="da30a-344">the `ICloneable.Clone` member of `C` becomes the implementation of `Clone` in `ICloneable` because explicit interface member implementations take precedence over other members.</span></span>

<span data-ttu-id="da30a-345">Se una classe o struct implementa due o più interfacce che contengono un membro con il nome stesso, tipo e i tipi di parametro, è possibile eseguire il mapping di ognuno di tali membri di interfaccia in un singolo membro di classe o struct.</span><span class="sxs-lookup"><span data-stu-id="da30a-345">If a class or struct implements two or more interfaces containing a member with the same name, type, and parameter types, it is possible to map each of those interface members onto a single class or struct member.</span></span> <span data-ttu-id="da30a-346">Esempio:</span><span class="sxs-lookup"><span data-stu-id="da30a-346">For example</span></span>
```csharp
interface IControl
{
    void Paint();
}

interface IForm
{
    void Paint();
}

class Page: IControl, IForm
{
    public void Paint() {...}
}
```

<span data-ttu-id="da30a-347">In questo caso, il `Paint` di entrambi i metodi `IControl` e `IForm` vengono mappati i `Paint` metodo nella `Page`.</span><span class="sxs-lookup"><span data-stu-id="da30a-347">Here, the `Paint` methods of both `IControl` and `IForm` are mapped onto the `Paint` method in `Page`.</span></span> <span data-ttu-id="da30a-348">È ovviamente anche possibile disporre di implementazioni di membro di interfaccia esplicita separata per i due metodi.</span><span class="sxs-lookup"><span data-stu-id="da30a-348">It is of course also possible to have separate explicit interface member implementations for the two methods.</span></span>

<span data-ttu-id="da30a-349">Se una classe o struct implementa un'interfaccia che contiene i membri nascosti, alcuni membri devono essere implementati necessariamente tramite implementazioni esplicite dell'interfaccia membro.</span><span class="sxs-lookup"><span data-stu-id="da30a-349">If a class or struct implements an interface that contains hidden members, then some members must necessarily be implemented through explicit interface member implementations.</span></span> <span data-ttu-id="da30a-350">Esempio:</span><span class="sxs-lookup"><span data-stu-id="da30a-350">For example</span></span>
```csharp
interface IBase
{
    int P { get; }
}

interface IDerived: IBase
{
    new int P();
}
```

<span data-ttu-id="da30a-351">Un'implementazione di questa interfaccia richiede almeno una implementazione esplicita dell'interfaccia membro e potrebbe assumere uno dei formati seguenti</span><span class="sxs-lookup"><span data-stu-id="da30a-351">An implementation of this interface would require at least one explicit interface member implementation, and would take one of the following forms</span></span>
```csharp
class C: IDerived
{
    int IBase.P { get {...} }
    int IDerived.P() {...}
}

class C: IDerived
{
    public int P { get {...} }
    int IDerived.P() {...}
}

class C: IDerived
{
    int IBase.P { get {...} }
    public int P() {...}
}
```

<span data-ttu-id="da30a-352">Quando una classe implementa più interfacce che hanno la stessa interfaccia di base, può esistere solo un'implementazione dell'interfaccia di base.</span><span class="sxs-lookup"><span data-stu-id="da30a-352">When a class implements multiple interfaces that have the same base interface, there can be only one implementation of the base interface.</span></span> <span data-ttu-id="da30a-353">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="da30a-353">In the example</span></span>
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

interface IListBox: IControl
{
    void SetItems(string[] items);
}

class ComboBox: IControl, ITextBox, IListBox
{
    void IControl.Paint() {...}
    void ITextBox.SetText(string text) {...}
    void IListBox.SetItems(string[] items) {...}
}
```
<span data-ttu-id="da30a-354">non è possibile disporre di implementazioni separate per la `IControl` denominato nell'elenco delle classi base, il `IControl` ereditata da `ITextBox`e il `IControl` ereditata da `IListBox`.</span><span class="sxs-lookup"><span data-stu-id="da30a-354">it is not possible to have separate implementations for the `IControl` named in the base class list, the `IControl` inherited by `ITextBox`, and the `IControl` inherited by `IListBox`.</span></span> <span data-ttu-id="da30a-355">In effetti, non è prevista un'identità distinta per queste interfacce.</span><span class="sxs-lookup"><span data-stu-id="da30a-355">Indeed, there is no notion of a separate identity for these interfaces.</span></span> <span data-ttu-id="da30a-356">Piuttosto, le implementazioni di `ITextBox` e `IListBox` condividono la stessa implementazione di `IControl`, e `ComboBox` viene considerata solo per implementare tre interfacce `IControl`, `ITextBox`, e `IListBox`.</span><span class="sxs-lookup"><span data-stu-id="da30a-356">Rather, the implementations of `ITextBox` and `IListBox` share the same implementation of `IControl`, and `ComboBox` is simply considered to implement three interfaces, `IControl`, `ITextBox`, and `IListBox`.</span></span>

<span data-ttu-id="da30a-357">I membri di una classe base partecipano al mapping dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-357">The members of a base class participate in interface mapping.</span></span> <span data-ttu-id="da30a-358">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="da30a-358">In the example</span></span>
```csharp
interface Interface1
{
    void F();
}

class Class1
{
    public void F() {}
    public void G() {}
}

class Class2: Class1, Interface1
{
    new public void G() {}
}
```
<span data-ttu-id="da30a-359">il metodo `F` nelle `Class1` viene utilizzata nella `Class2`dell'implementazione di `Interface1`.</span><span class="sxs-lookup"><span data-stu-id="da30a-359">the method `F` in `Class1` is used in `Class2`'s implementation of `Interface1`.</span></span>

### <a name="interface-implementation-inheritance"></a><span data-ttu-id="da30a-360">Per ereditarietà dell'implementazione dell'interfaccia</span><span class="sxs-lookup"><span data-stu-id="da30a-360">Interface implementation inheritance</span></span>

<span data-ttu-id="da30a-361">Una classe eredita tutte le implementazioni di interfaccia fornite dalle classi base.</span><span class="sxs-lookup"><span data-stu-id="da30a-361">A class inherits all interface implementations provided by its base classes.</span></span>

<span data-ttu-id="da30a-362">Senza in modo esplicito ***implementare nuovamente*** un'interfaccia, una classe derivata non è possibile in alcun modo modificare i mapping di interfaccia eredita dalle relative classi base.</span><span class="sxs-lookup"><span data-stu-id="da30a-362">Without explicitly ***re-implementing*** an interface, a derived class cannot in any way alter the interface mappings it inherits from its base classes.</span></span> <span data-ttu-id="da30a-363">Ad esempio, nelle dichiarazioni</span><span class="sxs-lookup"><span data-stu-id="da30a-363">For example, in the declarations</span></span>
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    public void Paint() {...}
}

class TextBox: Control
{
    new public void Paint() {...}
}
```
<span data-ttu-id="da30a-364">il `Paint` metodo nella `TextBox` nasconde il `Paint` metodo nella `Control`, ma non modifica il mapping dei `Control.Paint` nello `IControl.Paint`e le chiamate a `Paint` tramite la classe verranno istanze e le istanze dell'interfaccia i seguenti effetti</span><span class="sxs-lookup"><span data-stu-id="da30a-364">the `Paint` method in `TextBox` hides the `Paint` method in `Control`, but it does not alter the mapping of `Control.Paint` onto `IControl.Paint`, and calls to `Paint` through class instances and interface instances will have the following effects</span></span>
```csharp
Control c = new Control();
TextBox t = new TextBox();
IControl ic = c;
IControl it = t;
c.Paint();            // invokes Control.Paint();
t.Paint();            // invokes TextBox.Paint();
ic.Paint();           // invokes Control.Paint();
it.Paint();           // invokes Control.Paint();
```

<span data-ttu-id="da30a-365">Tuttavia, quando un metodo di interfaccia viene mappato a un metodo virtuale in una classe, è possibile per le classi derivate eseguire l'override del metodo virtuale e modificare l'implementazione dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-365">However, when an interface method is mapped onto a virtual method in a class, it is possible for derived classes to override the virtual method and alter the implementation of the interface.</span></span> <span data-ttu-id="da30a-366">Ad esempio, riscrivere le dichiarazioni sopra alla</span><span class="sxs-lookup"><span data-stu-id="da30a-366">For example, rewriting the declarations above to</span></span>
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    public virtual void Paint() {...}
}

class TextBox: Control
{
    public override void Paint() {...}
}
```
<span data-ttu-id="da30a-367">gli effetti seguenti verranno ora esaminati</span><span class="sxs-lookup"><span data-stu-id="da30a-367">the following effects will now be observed</span></span>
```csharp
Control c = new Control();
TextBox t = new TextBox();
IControl ic = c;
IControl it = t;
c.Paint();            // invokes Control.Paint();
t.Paint();            // invokes TextBox.Paint();
ic.Paint();           // invokes Control.Paint();
it.Paint();           // invokes TextBox.Paint();
```

<span data-ttu-id="da30a-368">Poiché le implementazioni di membri di interfaccia esplicita non possono essere dichiarate come virtuale, non è possibile eseguire l'override di un'implementazione di membro di interfaccia esplicita.</span><span class="sxs-lookup"><span data-stu-id="da30a-368">Since explicit interface member implementations cannot be declared virtual, it is not possible to override an explicit interface member implementation.</span></span> <span data-ttu-id="da30a-369">Tuttavia, è perfettamente valido per un'implementazione di membro di interfaccia esplicita chiamare un altro metodo e che è possibile dichiarare virtuale per consentire altro metodo dalle classi derivate per eseguirne l'override.</span><span class="sxs-lookup"><span data-stu-id="da30a-369">However, it is perfectly valid for an explicit interface member implementation to call another method, and that other method can be declared virtual to allow derived classes to override it.</span></span> <span data-ttu-id="da30a-370">Esempio:</span><span class="sxs-lookup"><span data-stu-id="da30a-370">For example</span></span>
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    void IControl.Paint() { PaintControl(); }
    protected virtual void PaintControl() {...}
}

class TextBox: Control
{
    protected override void PaintControl() {...}
}
```

<span data-ttu-id="da30a-371">In questo caso, le classi derivate da `Control` possibile specializzare l'implementazione di `IControl.Paint` eseguendo l'override di `PaintControl` (metodo).</span><span class="sxs-lookup"><span data-stu-id="da30a-371">Here, classes derived from `Control` can specialize the implementation of `IControl.Paint` by overriding the `PaintControl` method.</span></span>

### <a name="interface-re-implementation"></a><span data-ttu-id="da30a-372">Prevista nuova implementazione dell'interfaccia</span><span class="sxs-lookup"><span data-stu-id="da30a-372">Interface re-implementation</span></span>

<span data-ttu-id="da30a-373">È la possibilità di una classe che eredita un'implementazione dell'interfaccia ***reimplementare*** l'interfaccia includendolo nell'elenco delle classi base.</span><span class="sxs-lookup"><span data-stu-id="da30a-373">A class that inherits an interface implementation is permitted to ***re-implement*** the interface by including it in the base class list.</span></span>

<span data-ttu-id="da30a-374">Una nuova implementazione di un'interfaccia segue esattamente la stessa interfaccia le regole di mapping come implementazione iniziale di un'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-374">A re-implementation of an interface follows exactly the same interface mapping rules as an initial implementation of an interface.</span></span> <span data-ttu-id="da30a-375">Di conseguenza, il mapping di interfaccia ereditati non ha effetto alcun modo sul mapping dell'interfaccia stabilita per la nuova implementazione dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-375">Thus, the inherited interface mapping has no effect whatsoever on the interface mapping established for the re-implementation of the interface.</span></span> <span data-ttu-id="da30a-376">Ad esempio, nelle dichiarazioni</span><span class="sxs-lookup"><span data-stu-id="da30a-376">For example, in the declarations</span></span>
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    void IControl.Paint() {...}
}

class MyControl: Control, IControl
{
    public void Paint() {}
}
```
<span data-ttu-id="da30a-377">il fatto che `Control` viene eseguito il mapping `IControl.Paint` nello `Control.IControl.Paint` non ha alcun effetto reimplementazione in `MyControl`, che esegue il mapping `IControl.Paint` nello `MyControl.Paint`.</span><span class="sxs-lookup"><span data-stu-id="da30a-377">the fact that `Control` maps `IControl.Paint` onto `Control.IControl.Paint` doesn't affect the re-implementation in `MyControl`, which maps `IControl.Paint` onto `MyControl.Paint`.</span></span>

<span data-ttu-id="da30a-378">Ereditate le dichiarazioni dei membri pubblici e membri dell'interfaccia esplicita ereditati dichiarazioni di partecipano al processo di mapping per le interfacce implementate nuovamente.</span><span class="sxs-lookup"><span data-stu-id="da30a-378">Inherited public member declarations and inherited explicit interface member declarations participate in the interface mapping process for re-implemented interfaces.</span></span> <span data-ttu-id="da30a-379">Esempio:</span><span class="sxs-lookup"><span data-stu-id="da30a-379">For example</span></span>
```csharp
interface IMethods
{
    void F();
    void G();
    void H();
    void I();
}

class Base: IMethods
{
    void IMethods.F() {}
    void IMethods.G() {}
    public void H() {}
    public void I() {}
}

class Derived: Base, IMethods
{
    public void F() {}
    void IMethods.H() {}
}
```

<span data-ttu-id="da30a-380">Questo caso, l'implementazione di `IMethods` nelle `Derived` esegue il mapping dei metodi di interfaccia su `Derived.F`, `Base.IMethods.G`, `Derived.IMethods.H`, e `Base.I`.</span><span class="sxs-lookup"><span data-stu-id="da30a-380">Here, the implementation of `IMethods` in `Derived` maps the interface methods onto `Derived.F`, `Base.IMethods.G`, `Derived.IMethods.H`, and `Base.I`.</span></span>

<span data-ttu-id="da30a-381">Quando una classe implementa un'interfaccia, in modo implicito anche implementa tutte le interfacce di base di tale interfaccia.</span><span class="sxs-lookup"><span data-stu-id="da30a-381">When a class implements an interface, it implicitly also implements all of that interface's base interfaces.</span></span> <span data-ttu-id="da30a-382">Analogamente, una nuova implementazione di un'interfaccia in modo implicito è anche una nuova implementazione di tutte le interfacce di base.</span><span class="sxs-lookup"><span data-stu-id="da30a-382">Likewise, a re-implementation of an interface is also implicitly a re-implementation of all of the interface's base interfaces.</span></span> <span data-ttu-id="da30a-383">Esempio:</span><span class="sxs-lookup"><span data-stu-id="da30a-383">For example</span></span>
```csharp
interface IBase
{
    void F();
}

interface IDerived: IBase
{
    void G();
}

class C: IDerived
{
    void IBase.F() {...}
    void IDerived.G() {...}
}

class D: C, IDerived
{
    public void F() {...}
    public void G() {...}
}
```

<span data-ttu-id="da30a-384">Questo caso, la nuova implementazione di `IDerived` implementa anche nuovamente `IBase`, il mapping `IBase.F` nello `D.F`.</span><span class="sxs-lookup"><span data-stu-id="da30a-384">Here, the re-implementation of `IDerived` also re-implements `IBase`, mapping `IBase.F` onto `D.F`.</span></span>

### <a name="abstract-classes-and-interfaces"></a><span data-ttu-id="da30a-385">Interfacce e classi astratte</span><span class="sxs-lookup"><span data-stu-id="da30a-385">Abstract classes and interfaces</span></span>

<span data-ttu-id="da30a-386">Ad esempio una classe non astratta, una classe astratta deve fornire implementazioni di tutti i membri delle interfacce che sono elencati nell'elenco delle classi base della classe.</span><span class="sxs-lookup"><span data-stu-id="da30a-386">Like a non-abstract class, an abstract class must provide implementations of all members of the interfaces that are listed in the base class list of the class.</span></span> <span data-ttu-id="da30a-387">Tuttavia, una classe astratta può mappare i metodi di interfaccia su metodi astratti.</span><span class="sxs-lookup"><span data-stu-id="da30a-387">However, an abstract class is permitted to map interface methods onto abstract methods.</span></span> <span data-ttu-id="da30a-388">Esempio:</span><span class="sxs-lookup"><span data-stu-id="da30a-388">For example</span></span>
```csharp
interface IMethods
{
    void F();
    void G();
}

abstract class C: IMethods
{
    public abstract void F();
    public abstract void G();
}
```

<span data-ttu-id="da30a-389">Questo caso, l'implementazione di `IMethods` viene eseguito il mapping `F` e `G` su metodi astratti, che deve essere sottoposto a override nelle classi astratte che derivano da `C`.</span><span class="sxs-lookup"><span data-stu-id="da30a-389">Here, the implementation of `IMethods` maps `F` and `G` onto abstract methods, which must be overridden in non-abstract classes that derive from `C`.</span></span>

<span data-ttu-id="da30a-390">Si noti che le implementazioni di membri di interfaccia esplicita non possono essere astratte, ma tali implementazioni sono naturalmente la possibilità di chiamare i metodi astratti.</span><span class="sxs-lookup"><span data-stu-id="da30a-390">Note that explicit interface member implementations cannot be abstract, but explicit interface member implementations are of course permitted to call abstract methods.</span></span> <span data-ttu-id="da30a-391">Esempio:</span><span class="sxs-lookup"><span data-stu-id="da30a-391">For example</span></span>
```csharp
interface IMethods
{
    void F();
    void G();
}

abstract class C: IMethods
{
    void IMethods.F() { FF(); }
    void IMethods.G() { GG(); }
    protected abstract void FF();
    protected abstract void GG();
}
```

<span data-ttu-id="da30a-392">Questo caso, le classi astratte che derivano da `C` potrebbe essere necessario eseguire l'override `FF` e `GG`, fornendo in tal modo l'implementazione effettiva del `IMethods`.</span><span class="sxs-lookup"><span data-stu-id="da30a-392">Here, non-abstract classes that derive from `C` would be required to override `FF` and `GG`, thus providing the actual implementation of `IMethods`.</span></span>
