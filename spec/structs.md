---
ms.openlocfilehash: 6b35df4e00a00a23221dedca6c0424604971a2f5
ms.sourcegitcommit: f921de697a44d3ae827f168a8371d7dc3ca66f2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101641986"
---
# <a name="structs"></a><span data-ttu-id="10dc1-101">Struct</span><span class="sxs-lookup"><span data-stu-id="10dc1-101">Structs</span></span>

<span data-ttu-id="10dc1-102">Gli struct sono simili alle classi in quanto rappresentano strutture dei dati che possono contenere membri dati e membri di funzione.</span><span class="sxs-lookup"><span data-stu-id="10dc1-102">Structs are similar to classes in that they represent data structures that can contain data members and function members.</span></span> <span data-ttu-id="10dc1-103">Tuttavia, a differenza delle classi, gli struct sono tipi valore e non richiedono l'allocazione dell'heap.</span><span class="sxs-lookup"><span data-stu-id="10dc1-103">However, unlike classes, structs are value types and do not require heap allocation.</span></span> <span data-ttu-id="10dc1-104">Una variabile di un tipo struct contiene direttamente i dati dello struct, mentre una variabile di un tipo di classe contiene un riferimento ai dati, il secondo noto come oggetto.</span><span class="sxs-lookup"><span data-stu-id="10dc1-104">A variable of a struct type directly contains the data of the struct, whereas a variable of a class type contains a reference to the data, the latter known as an object.</span></span>

<span data-ttu-id="10dc1-105">I tipi struct sono particolarmente utili per strutture dati di piccole dimensioni che hanno una semantica di valori.</span><span class="sxs-lookup"><span data-stu-id="10dc1-105">Structs are particularly useful for small data structures that have value semantics.</span></span> <span data-ttu-id="10dc1-106">I numeri complessi, i punti di un sistema di coordinate o le coppie chiave-valore di un dizionario sono buoni esempi di struct.</span><span class="sxs-lookup"><span data-stu-id="10dc1-106">Complex numbers, points in a coordinate system, or key-value pairs in a dictionary are all good examples of structs.</span></span> <span data-ttu-id="10dc1-107">La chiave di queste strutture di dati è che hanno pochi membri dati, che non richiedono l'uso dell'ereditarietà o dell'identità referenziale e che possono essere implementati facilmente usando la semantica del valore in cui l'assegnazione copia il valore anziché il riferimento.</span><span class="sxs-lookup"><span data-stu-id="10dc1-107">Key to these data structures is that they have few data members, that they do not require use of inheritance or referential identity, and that they can be conveniently implemented using value semantics where assignment copies the value instead of the reference.</span></span>

<span data-ttu-id="10dc1-108">Come descritto in [tipi semplici](types.md#simple-types), i tipi semplici forniti da C#, ad esempio `int` , `double` e `bool` , sono in realtà tutti i tipi struct.</span><span class="sxs-lookup"><span data-stu-id="10dc1-108">As described in [Simple types](types.md#simple-types), the simple types provided by C#, such as `int`, `double`, and `bool`, are in fact all struct types.</span></span> <span data-ttu-id="10dc1-109">Proprio come questi tipi predefiniti sono struct, è anche possibile usare gli struct e l'overload degli operatori per implementare nuovi tipi "primitivi" nel linguaggio C#.</span><span class="sxs-lookup"><span data-stu-id="10dc1-109">Just as these predefined types are structs, it is also possible to use structs and operator overloading to implement new "primitive" types in the C# language.</span></span> <span data-ttu-id="10dc1-110">Alla fine di questo capitolo sono riportati due esempi di tali tipi ([esempi di struct](structs.md#struct-examples)).</span><span class="sxs-lookup"><span data-stu-id="10dc1-110">Two examples of such types are given at the end of this chapter ([Struct examples](structs.md#struct-examples)).</span></span>

## <a name="struct-declarations"></a><span data-ttu-id="10dc1-111">Dichiarazioni struct</span><span class="sxs-lookup"><span data-stu-id="10dc1-111">Struct declarations</span></span>

<span data-ttu-id="10dc1-112">Un *struct_declaration* è un *type_declaration* ([dichiarazioni di tipo](namespaces.md#type-declarations)) che dichiara un nuovo struct:</span><span class="sxs-lookup"><span data-stu-id="10dc1-112">A *struct_declaration* is a *type_declaration* ([Type declarations](namespaces.md#type-declarations)) that declares a new struct:</span></span>

```antlr
struct_declaration
    : attributes? struct_modifier* 'partial'? 'struct' identifier type_parameter_list?
      struct_interfaces? type_parameter_constraints_clause* struct_body ';'?
    ;
```

<span data-ttu-id="10dc1-113">Un *struct_declaration* è costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), seguito da un set facoltativo di *struct_modifier* s ([modificatori struct](structs.md#struct-modifiers)), seguito da un `partial` modificatore facoltativo, seguito dalla parola chiave `struct` e da un *identificatore* che denomina lo struct, seguito da una specifica di *type_parameter_list* facoltativa ([parametri di tipo](classes.md#type-parameters)), seguiti da una specifica di *struct_interfaces* facoltativa ([modificatore parziale](structs.md#partial-modifier)), seguiti da una specifica *type_parameter_constraints_clause* s facoltativa (vincoli di [parametro di tipo](classes.md#type-parameter-constraints)), seguiti da un *struct_body* ([corpo della struct](structs.md#struct-body)), seguiti facoltativ</span><span class="sxs-lookup"><span data-stu-id="10dc1-113">A *struct_declaration* consists of an optional set of *attributes* ([Attributes](attributes.md)), followed by an optional set of *struct_modifier* s ([Struct modifiers](structs.md#struct-modifiers)), followed by an optional `partial` modifier, followed by the keyword `struct` and an *identifier* that names the struct, followed by an optional *type_parameter_list* specification ([Type parameters](classes.md#type-parameters)), followed by an optional *struct_interfaces* specification ([Partial modifier](structs.md#partial-modifier)) ), followed by an optional *type_parameter_constraints_clause* s specification ([Type parameter constraints](classes.md#type-parameter-constraints)), followed by a *struct_body* ([Struct body](structs.md#struct-body)), optionally followed by a semicolon.</span></span>

### <a name="struct-modifiers"></a><span data-ttu-id="10dc1-114">Modificatori struct</span><span class="sxs-lookup"><span data-stu-id="10dc1-114">Struct modifiers</span></span>

<span data-ttu-id="10dc1-115">Un *struct_declaration* può includere facoltativamente una sequenza di modificatori struct:</span><span class="sxs-lookup"><span data-stu-id="10dc1-115">A *struct_declaration* may optionally include a sequence of struct modifiers:</span></span>

```antlr
struct_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | struct_modifier_unsafe
    ;
```

<span data-ttu-id="10dc1-116">Si tratta di un errore in fase di compilazione perché lo stesso modificatore venga visualizzato più volte in una dichiarazione struct.</span><span class="sxs-lookup"><span data-stu-id="10dc1-116">It is a compile-time error for the same modifier to appear multiple times in a struct declaration.</span></span>

<span data-ttu-id="10dc1-117">I modificatori di una dichiarazione di struct hanno lo stesso significato di quelli di una dichiarazione di classe ([dichiarazioni di classe](classes.md#class-declarations)).</span><span class="sxs-lookup"><span data-stu-id="10dc1-117">The modifiers of a struct declaration have the same meaning as those of a class declaration ([Class declarations](classes.md#class-declarations)).</span></span>

### <a name="partial-modifier"></a><span data-ttu-id="10dc1-118">Modificatore parziale</span><span class="sxs-lookup"><span data-stu-id="10dc1-118">Partial modifier</span></span>

<span data-ttu-id="10dc1-119">Il `partial` modificatore indica che questo *struct_declaration* è una dichiarazione di tipo parziale.</span><span class="sxs-lookup"><span data-stu-id="10dc1-119">The `partial` modifier indicates that this *struct_declaration* is a partial type declaration.</span></span> <span data-ttu-id="10dc1-120">Più dichiarazioni di struct parziali con lo stesso nome in uno spazio dei nomi di inclusione o in una dichiarazione di tipo combinano per formare una dichiarazione struct, seguendo le regole specificate in [tipi parziali](classes.md#partial-types).</span><span class="sxs-lookup"><span data-stu-id="10dc1-120">Multiple partial struct declarations with the same name within an enclosing namespace or type declaration combine to form one struct declaration, following the rules specified in [Partial types](classes.md#partial-types).</span></span>

### <a name="struct-interfaces"></a><span data-ttu-id="10dc1-121">Interfacce struct</span><span class="sxs-lookup"><span data-stu-id="10dc1-121">Struct interfaces</span></span>

<span data-ttu-id="10dc1-122">Una dichiarazione struct può includere una specifica *struct_interfaces* , nel qual caso lo struct viene definito per implementare direttamente i tipi di interfaccia specificati.</span><span class="sxs-lookup"><span data-stu-id="10dc1-122">A struct declaration may include a *struct_interfaces* specification, in which case the struct is said to directly implement the given interface types.</span></span>

```antlr
struct_interfaces
    : ':' interface_type_list
    ;
```

<span data-ttu-id="10dc1-123">Le implementazioni dell'interfaccia sono illustrate ulteriormente nelle [implementazioni dell'interfaccia](interfaces.md#interface-implementations).</span><span class="sxs-lookup"><span data-stu-id="10dc1-123">Interface implementations are discussed further in [Interface implementations](interfaces.md#interface-implementations).</span></span>

### <a name="struct-body"></a><span data-ttu-id="10dc1-124">Corpo dello struct</span><span class="sxs-lookup"><span data-stu-id="10dc1-124">Struct body</span></span>

<span data-ttu-id="10dc1-125">Il *struct_body* di uno struct definisce i membri della struttura.</span><span class="sxs-lookup"><span data-stu-id="10dc1-125">The *struct_body* of a struct defines the members of the struct.</span></span>

```antlr
struct_body
    : '{' struct_member_declaration* '}'
    ;
```

## <a name="struct-members"></a><span data-ttu-id="10dc1-126">Membri struct</span><span class="sxs-lookup"><span data-stu-id="10dc1-126">Struct members</span></span>

<span data-ttu-id="10dc1-127">I membri di uno struct sono costituiti dai membri introdotti dal *struct_member_declaration* s e dai membri ereditati dal tipo `System.ValueType` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-127">The members of a struct consist of the members introduced by its *struct_member_declaration* s and the members inherited from the type `System.ValueType`.</span></span>

```antlr
struct_member_declaration
    : constant_declaration
    | field_declaration
    | method_declaration
    | property_declaration
    | event_declaration
    | indexer_declaration
    | operator_declaration
    | constructor_declaration
    | static_constructor_declaration
    | type_declaration
    | struct_member_declaration_unsafe
    ;
```

<span data-ttu-id="10dc1-128">Ad eccezione delle differenze indicate nelle [differenze tra le classi e gli struct](structs.md#class-and-struct-differences), le descrizioni dei membri della classe forniti nei membri della [classe](classes.md#class-members) tramite [funzioni asincrone](classes.md#async-functions) si applicano anche ai membri struct.</span><span class="sxs-lookup"><span data-stu-id="10dc1-128">Except for the differences noted in [Class and struct differences](structs.md#class-and-struct-differences), the descriptions of class members provided in [Class members](classes.md#class-members) through [Async functions](classes.md#async-functions) apply to struct members as well.</span></span>

## <a name="class-and-struct-differences"></a><span data-ttu-id="10dc1-129">Differenze di classi e struct</span><span class="sxs-lookup"><span data-stu-id="10dc1-129">Class and struct differences</span></span>

<span data-ttu-id="10dc1-130">Gli struct differiscono dalle classi in diversi modi importanti:</span><span class="sxs-lookup"><span data-stu-id="10dc1-130">Structs differ from classes in several important ways:</span></span>

*  <span data-ttu-id="10dc1-131">Gli struct sono tipi di valore ([semantica del valore](structs.md#value-semantics)).</span><span class="sxs-lookup"><span data-stu-id="10dc1-131">Structs are value types ([Value semantics](structs.md#value-semantics)).</span></span>
*  <span data-ttu-id="10dc1-132">Tutti i tipi struct ereditano in modo implicito dalla classe `System.ValueType` ([ereditarietà](structs.md#inheritance)).</span><span class="sxs-lookup"><span data-stu-id="10dc1-132">All struct types implicitly inherit from the class `System.ValueType` ([Inheritance](structs.md#inheritance)).</span></span>
*  <span data-ttu-id="10dc1-133">L'assegnazione a una variabile di un tipo struct crea una copia del valore assegnato ([assegnazione](structs.md#assignment)).</span><span class="sxs-lookup"><span data-stu-id="10dc1-133">Assignment to a variable of a struct type creates a copy of the value being assigned ([Assignment](structs.md#assignment)).</span></span>
*  <span data-ttu-id="10dc1-134">Il valore predefinito di uno struct è il valore prodotto impostando tutti i campi di tipo valore sul valore predefinito e tutti i campi di tipo riferimento su `null` ([valori predefiniti](structs.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="10dc1-134">The default value of a struct is the value produced by setting all value type fields to their default value and all reference type fields to `null` ([Default values](structs.md#default-values)).</span></span>
*  <span data-ttu-id="10dc1-135">Le operazioni di conversione boxing e unboxing vengono usate per eseguire la conversione tra un tipo di struct e `object` ([conversione boxing e](structs.md#boxing-and-unboxing)unboxing).</span><span class="sxs-lookup"><span data-stu-id="10dc1-135">Boxing and unboxing operations are used to convert between a struct type and `object` ([Boxing and unboxing](structs.md#boxing-and-unboxing)).</span></span>
*  <span data-ttu-id="10dc1-136">Il significato di `this` è diverso per gli struct ([questo accesso](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="10dc1-136">The meaning of `this` is different for structs ([This access](expressions.md#this-access)).</span></span>
*  <span data-ttu-id="10dc1-137">Le dichiarazioni dei campi di istanza per uno struct non possono includere inizializzatori di variabile ([inizializzatori di campo](structs.md#field-initializers)).</span><span class="sxs-lookup"><span data-stu-id="10dc1-137">Instance field declarations for a struct are not permitted to include variable initializers ([Field initializers](structs.md#field-initializers)).</span></span>
*  <span data-ttu-id="10dc1-138">Uno struct non è autorizzato a dichiarare un costruttore di istanza senza parametri ([costruttori](structs.md#constructors)).</span><span class="sxs-lookup"><span data-stu-id="10dc1-138">A struct is not permitted to declare a parameterless instance constructor ([Constructors](structs.md#constructors)).</span></span>
*  <span data-ttu-id="10dc1-139">Uno struct non è autorizzato a dichiarare[un distruttore (](structs.md#destructors)distruttori).</span><span class="sxs-lookup"><span data-stu-id="10dc1-139">A struct is not permitted to declare a destructor ([Destructors](structs.md#destructors)).</span></span>

### <a name="value-semantics"></a><span data-ttu-id="10dc1-140">Semantica del valore</span><span class="sxs-lookup"><span data-stu-id="10dc1-140">Value semantics</span></span>

<span data-ttu-id="10dc1-141">Gli struct sono tipi di valore ([tipi di valore](types.md#value-types)) e hanno una semantica del valore.</span><span class="sxs-lookup"><span data-stu-id="10dc1-141">Structs are value types ([Value types](types.md#value-types)) and are said to have value semantics.</span></span> <span data-ttu-id="10dc1-142">Le classi, d'altra parte, sono tipi di riferimento ([tipi di riferimento](types.md#reference-types)) e hanno una semantica di riferimento.</span><span class="sxs-lookup"><span data-stu-id="10dc1-142">Classes, on the other hand, are reference types ([Reference types](types.md#reference-types)) and are said to have reference semantics.</span></span>

<span data-ttu-id="10dc1-143">Una variabile di un tipo struct contiene direttamente i dati dello struct, mentre una variabile di un tipo di classe contiene un riferimento ai dati, il secondo noto come oggetto.</span><span class="sxs-lookup"><span data-stu-id="10dc1-143">A variable of a struct type directly contains the data of the struct, whereas a variable of a class type contains a reference to the data, the latter known as an object.</span></span> <span data-ttu-id="10dc1-144">Quando uno struct `B` contiene un campo di istanza di tipo `A` e `A` è un tipo struct, si tratta di un errore in fase di compilazione per `A` da dipendere da `B` o da un tipo costruito da `B` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-144">When a struct `B` contains an instance field of type `A` and `A` is a struct type, it is a compile-time error for `A` to depend on `B` or a type constructed from `B`.</span></span> <span data-ttu-id="10dc1-145">Uno struct `X`  \* **dipende direttamente da** _ uno struct `Y` se `X` contiene un campo di istanza di tipo `Y` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-145">A struct `X` \***directly depends on** _ a struct `Y` if `X` contains an instance field of type `Y`.</span></span> <span data-ttu-id="10dc1-146">Data questa definizione, il set completo di struct su cui dipende uno struct è la chiusura transitiva di _ *_direttamente dipende_* da \* Relationship.</span><span class="sxs-lookup"><span data-stu-id="10dc1-146">Given this definition, the complete set of structs upon which a struct depends is the transitive closure of the _ *_directly depends on_*\* relationship.</span></span>  <span data-ttu-id="10dc1-147">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="10dc1-147">For example</span></span>
```csharp
struct Node
{
    int data;
    Node next; // error, Node directly depends on itself
}
```
<span data-ttu-id="10dc1-148">è un errore perché `Node` contiene un campo di istanza di un proprio tipo.</span><span class="sxs-lookup"><span data-stu-id="10dc1-148">is an error because `Node` contains an instance field of its own type.</span></span>  <span data-ttu-id="10dc1-149">Un altro esempio</span><span class="sxs-lookup"><span data-stu-id="10dc1-149">Another example</span></span>
```csharp
struct A { B b; }

struct B { C c; }

struct C { A a; }
```
<span data-ttu-id="10dc1-150">è un errore perché ognuno dei tipi `A` , `B` e dipende l' `C` uno dall'altro.</span><span class="sxs-lookup"><span data-stu-id="10dc1-150">is an error because each of the types `A`, `B`, and `C` depend on each other.</span></span>

<span data-ttu-id="10dc1-151">Con le classi, è possibile che due variabili facciano riferimento allo stesso oggetto ed è quindi possibile che le operazioni su una variabile influiscano sull'oggetto a cui fa riferimento l'altra variabile.</span><span class="sxs-lookup"><span data-stu-id="10dc1-151">With classes, it is possible for two variables to reference the same object, and thus possible for operations on one variable to affect the object referenced by the other variable.</span></span> <span data-ttu-id="10dc1-152">Con gli struct, le variabili hanno ciascuna una propria copia dei dati (tranne nel caso delle variabili di `ref` `out` parametro e) e non è possibile che le operazioni su uno influiscano sull'altro.</span><span class="sxs-lookup"><span data-stu-id="10dc1-152">With structs, the variables each have their own copy of the data (except in the case of `ref` and `out` parameter variables), and it is not possible for operations on one to affect the other.</span></span> <span data-ttu-id="10dc1-153">Inoltre, poiché gli struct non sono tipi di riferimento, non è possibile che i valori di un tipo struct siano `null` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-153">Furthermore, because structs are not reference types, it is not possible for values of a struct type to be `null`.</span></span>

<span data-ttu-id="10dc1-154">Data la dichiarazione</span><span class="sxs-lookup"><span data-stu-id="10dc1-154">Given the declaration</span></span>
```csharp
struct Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
<span data-ttu-id="10dc1-155">frammento di codice</span><span class="sxs-lookup"><span data-stu-id="10dc1-155">the code fragment</span></span>
```csharp
Point a = new Point(10, 10);
Point b = a;
a.x = 100;
System.Console.WriteLine(b.x);
```
<span data-ttu-id="10dc1-156">Restituisce il valore `10` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-156">outputs the value `10`.</span></span> <span data-ttu-id="10dc1-157">L'assegnazione di `a` per `b` Crea una copia del valore e pertanto non `b` è interessata dall'assegnazione a `a.x` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-157">The assignment of `a` to `b` creates a copy of the value, and `b` is thus unaffected by the assignment to `a.x`.</span></span> <span data-ttu-id="10dc1-158">Era `Point` stato invece dichiarato come una classe, l'output è dovuto al fatto `100` `a` che e `b` fanno riferimento allo stesso oggetto.</span><span class="sxs-lookup"><span data-stu-id="10dc1-158">Had `Point` instead been declared as a class, the output would be `100` because `a` and `b` would reference the same object.</span></span>

### <a name="inheritance"></a><span data-ttu-id="10dc1-159">Ereditarietà</span><span class="sxs-lookup"><span data-stu-id="10dc1-159">Inheritance</span></span>

<span data-ttu-id="10dc1-160">Tutti i tipi struct ereditano in modo implicito dalla classe `System.ValueType` , che a sua volta eredita dalla classe `object` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-160">All struct types implicitly inherit from the class `System.ValueType`, which, in turn, inherits from class `object`.</span></span> <span data-ttu-id="10dc1-161">Una dichiarazione struct può specificare un elenco di interfacce implementate, ma non è possibile che una dichiarazione struct specifichi una classe base.</span><span class="sxs-lookup"><span data-stu-id="10dc1-161">A struct declaration may specify a list of implemented interfaces, but it is not possible for a struct declaration to specify a base class.</span></span>

<span data-ttu-id="10dc1-162">I tipi struct non sono mai astratti e sono sempre implicitamente sealed.</span><span class="sxs-lookup"><span data-stu-id="10dc1-162">Struct types are never abstract and are always implicitly sealed.</span></span> <span data-ttu-id="10dc1-163">I `abstract` `sealed` modificatori e non sono pertanto consentiti in una dichiarazione struct.</span><span class="sxs-lookup"><span data-stu-id="10dc1-163">The `abstract` and `sealed` modifiers are therefore not permitted in a struct declaration.</span></span>

<span data-ttu-id="10dc1-164">Poiché l'ereditarietà non è supportata per gli struct, l'accessibilità dichiarata di un membro struct non può essere `protected` o `protected internal` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-164">Since inheritance isn't supported for structs, the declared accessibility of a struct member cannot be `protected` or `protected internal`.</span></span>

<span data-ttu-id="10dc1-165">I membri di funzione in uno struct non possono essere `abstract` o `virtual` e il `override` modificatore è consentito solo per eseguire l'override dei metodi ereditati da `System.ValueType` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-165">Function members in a struct cannot be `abstract` or `virtual`, and the `override` modifier is allowed only to override methods inherited from `System.ValueType`.</span></span>

### <a name="assignment"></a><span data-ttu-id="10dc1-166">Assegnazione</span><span class="sxs-lookup"><span data-stu-id="10dc1-166">Assignment</span></span>

<span data-ttu-id="10dc1-167">L'assegnazione a una variabile di un tipo struct crea una copia del valore da assegnare.</span><span class="sxs-lookup"><span data-stu-id="10dc1-167">Assignment to a variable of a struct type creates a copy of the value being assigned.</span></span> <span data-ttu-id="10dc1-168">Questo comportamento è diverso dall'assegnazione a una variabile di un tipo di classe, che copia il riferimento ma non l'oggetto identificato dal riferimento.</span><span class="sxs-lookup"><span data-stu-id="10dc1-168">This differs from assignment to a variable of a class type, which copies the reference but not the object identified by the reference.</span></span>

<span data-ttu-id="10dc1-169">Analogamente a un'assegnazione, quando una struttura viene passata come parametro di valore o restituita come risultato di un membro di funzione, viene creata una copia dello struct.</span><span class="sxs-lookup"><span data-stu-id="10dc1-169">Similar to an assignment, when a struct is passed as a value parameter or returned as the result of a function member, a copy of the struct is created.</span></span> <span data-ttu-id="10dc1-170">Uno struct può essere passato per riferimento a un membro di funzione usando `ref` un `out` parametro o.</span><span class="sxs-lookup"><span data-stu-id="10dc1-170">A struct may be passed by reference to a function member using a `ref` or `out` parameter.</span></span>

<span data-ttu-id="10dc1-171">Quando una proprietà o un indicizzatore di uno struct è la destinazione di un'assegnazione, l'espressione dell'istanza associata alla proprietà o all'indicizzatore deve essere classificata come variabile.</span><span class="sxs-lookup"><span data-stu-id="10dc1-171">When a property or indexer of a struct is the target of an assignment, the instance expression associated with the property or indexer access must be classified as a variable.</span></span> <span data-ttu-id="10dc1-172">Se l'espressione dell'istanza è classificata come valore, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="10dc1-172">If the instance expression is classified as a value, a compile-time error occurs.</span></span> <span data-ttu-id="10dc1-173">Questa operazione viene descritta più dettagliatamente nell' [assegnazione semplice](expressions.md#simple-assignment).</span><span class="sxs-lookup"><span data-stu-id="10dc1-173">This is described in further detail in [Simple assignment](expressions.md#simple-assignment).</span></span>

### <a name="default-values"></a><span data-ttu-id="10dc1-174">Valori predefiniti</span><span class="sxs-lookup"><span data-stu-id="10dc1-174">Default values</span></span>

<span data-ttu-id="10dc1-175">Come descritto in [valori predefiniti](variables.md#default-values), i diversi tipi di variabili vengono inizializzati automaticamente sul valore predefinito quando vengono creati.</span><span class="sxs-lookup"><span data-stu-id="10dc1-175">As described in [Default values](variables.md#default-values), several kinds of variables are automatically initialized to their default value when they are created.</span></span> <span data-ttu-id="10dc1-176">Per le variabili dei tipi di classe e altri tipi di riferimento, questo valore predefinito è `null` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-176">For variables of class types and other reference types, this default value is `null`.</span></span> <span data-ttu-id="10dc1-177">Tuttavia, poiché gli struct sono tipi di valore che non possono essere `null` , il valore predefinito di uno struct è il valore prodotto impostando tutti i campi di tipo valore sul valore predefinito e tutti i campi di tipo riferimento su `null` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-177">However, since structs are value types that cannot be `null`, the default value of a struct is the value produced by setting all value type fields to their default value and all reference type fields to `null`.</span></span>

<span data-ttu-id="10dc1-178">Facendo riferimento allo `Point` struct dichiarato in precedenza, l'esempio</span><span class="sxs-lookup"><span data-stu-id="10dc1-178">Referring to the `Point` struct declared above, the example</span></span>
```csharp
Point[] a = new Point[100];
```
<span data-ttu-id="10dc1-179">Inizializza ogni oggetto `Point` nella matrice sul valore prodotto impostando i `x` campi e `y` su zero.</span><span class="sxs-lookup"><span data-stu-id="10dc1-179">initializes each `Point` in the array to the value produced by setting the `x` and `y` fields to zero.</span></span>

<span data-ttu-id="10dc1-180">Il valore predefinito di uno struct corrisponde al valore restituito dal costruttore predefinito dello struct ([costruttori predefiniti](types.md#default-constructors)).</span><span class="sxs-lookup"><span data-stu-id="10dc1-180">The default value of a struct corresponds to the value returned by the default constructor of the struct ([Default constructors](types.md#default-constructors)).</span></span> <span data-ttu-id="10dc1-181">A differenza di una classe, uno struct non è autorizzato a dichiarare un costruttore di istanza senza parametri.</span><span class="sxs-lookup"><span data-stu-id="10dc1-181">Unlike a class, a struct is not permitted to declare a parameterless instance constructor.</span></span> <span data-ttu-id="10dc1-182">Al contrario, ogni struct dispone implicitamente di un costruttore di istanza senza parametri che restituisce sempre il valore risultante dall'impostazione di tutti i campi di tipo valore sul valore predefinito e di tutti i campi di tipo riferimento su `null` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-182">Instead, every struct implicitly has a parameterless instance constructor which always returns the value that results from setting all value type fields to their default value and all reference type fields to `null`.</span></span>

<span data-ttu-id="10dc1-183">Gli struct devono essere progettati per considerare lo stato di inizializzazione predefinito uno stato valido.</span><span class="sxs-lookup"><span data-stu-id="10dc1-183">Structs should be designed to consider the default initialization state a valid state.</span></span> <span data-ttu-id="10dc1-184">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="10dc1-184">In the example</span></span>
```csharp
using System;

struct KeyValuePair
{
    string key;
    string value;

    public KeyValuePair(string key, string value) {
        if (key == null || value == null) throw new ArgumentException();
        this.key = key;
        this.value = value;
    }
}
```
<span data-ttu-id="10dc1-185">il costruttore di istanze definito dall'utente protegge da valori null solo se viene chiamato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="10dc1-185">the user-defined instance constructor protects against null values only where it is explicitly called.</span></span> <span data-ttu-id="10dc1-186">Nei casi in cui una `KeyValuePair` variabile è soggetta all'inizializzazione predefinita dei valori, i `key` `value` campi e saranno null e la struttura deve essere preparata per la gestione di questo stato.</span><span class="sxs-lookup"><span data-stu-id="10dc1-186">In cases where a `KeyValuePair` variable is subject to default value initialization, the `key` and `value` fields will be null, and the struct must be prepared to handle this state.</span></span>

### <a name="boxing-and-unboxing"></a><span data-ttu-id="10dc1-187">Boxing e unboxing</span><span class="sxs-lookup"><span data-stu-id="10dc1-187">Boxing and unboxing</span></span>

<span data-ttu-id="10dc1-188">Un valore di un tipo di classe può essere convertito nel tipo `object` o in un tipo di interfaccia implementato dalla classe semplicemente trattando il riferimento come un altro tipo in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="10dc1-188">A value of a class type can be converted to type `object` or to an interface type that is implemented by the class simply by treating the reference as another type at compile-time.</span></span> <span data-ttu-id="10dc1-189">Analogamente, un valore di tipo `object` o un valore di un tipo di interfaccia può essere convertito di nuovo in un tipo di classe senza modificare il riferimento (ma, in questo caso, è necessario un controllo del tipo in fase di esecuzione).</span><span class="sxs-lookup"><span data-stu-id="10dc1-189">Likewise, a value of type `object` or a value of an interface type can be converted back to a class type without changing the reference (but of course a run-time type check is required in this case).</span></span>

<span data-ttu-id="10dc1-190">Poiché gli struct non sono tipi di riferimento, queste operazioni vengono implementate in modo diverso per i tipi struct.</span><span class="sxs-lookup"><span data-stu-id="10dc1-190">Since structs are not reference types, these operations are implemented differently for struct types.</span></span> <span data-ttu-id="10dc1-191">Quando un valore di un tipo struct viene convertito nel tipo `object` o in un tipo di interfaccia implementato dallo struct, viene eseguita un'operazione di conversione boxing.</span><span class="sxs-lookup"><span data-stu-id="10dc1-191">When a value of a struct type is converted to type `object` or to an interface type that is implemented by the struct, a boxing operation takes place.</span></span> <span data-ttu-id="10dc1-192">Analogamente, quando un valore di tipo `object` o un valore di un tipo di interfaccia viene riconvertito in un tipo struct, viene eseguita un'operazione di conversione unboxing.</span><span class="sxs-lookup"><span data-stu-id="10dc1-192">Likewise, when a value of type `object` or a value of an interface type is converted back to a struct type, an unboxing operation takes place.</span></span> <span data-ttu-id="10dc1-193">Una differenza fondamentale tra le stesse operazioni sui tipi di classe è che la conversione boxing e unboxing copia il valore dello struct all'interno o all'esterno dell'istanza boxed.</span><span class="sxs-lookup"><span data-stu-id="10dc1-193">A key difference from the same operations on class types is that boxing and unboxing copies the struct value either into or out of the boxed instance.</span></span> <span data-ttu-id="10dc1-194">Quindi, dopo un'operazione di conversione boxing o unboxing, le modifiche apportate allo struct unboxed non vengono riflesse nello struct boxed.</span><span class="sxs-lookup"><span data-stu-id="10dc1-194">Thus, following a boxing or unboxing operation, changes made to the unboxed struct are not reflected in the boxed struct.</span></span>

<span data-ttu-id="10dc1-195">Quando un tipo struct esegue l'override di un metodo virtuale ereditato da `System.Object` (ad esempio `Equals` , `GetHashCode` o `ToString` ), la chiamata del metodo virtuale tramite un'istanza del tipo struct non provoca la conversione boxing.</span><span class="sxs-lookup"><span data-stu-id="10dc1-195">When a struct type overrides a virtual method inherited from `System.Object` (such as `Equals`, `GetHashCode`, or `ToString`), invocation of the virtual method through an instance of the struct type does not cause boxing to occur.</span></span> <span data-ttu-id="10dc1-196">Questo vale anche quando lo struct viene usato come parametro di tipo e la chiamata viene eseguita tramite un'istanza del tipo di parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="10dc1-196">This is true even when the struct is used as a type parameter and the invocation occurs through an instance of the type parameter type.</span></span> <span data-ttu-id="10dc1-197">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="10dc1-197">For example:</span></span>
```csharp
using System;

struct Counter
{
    int value;

    public override string ToString() {
        value++;
        return value.ToString();
    }
}

class Program
{
    static void Test<T>() where T: new() {
        T x = new T();
        Console.WriteLine(x.ToString());
        Console.WriteLine(x.ToString());
        Console.WriteLine(x.ToString());
    }

    static void Main() {
        Test<Counter>();
    }
}
```

<span data-ttu-id="10dc1-198">L'output del programma è:</span><span class="sxs-lookup"><span data-stu-id="10dc1-198">The output of the program is:</span></span>
```console
1
2
3
```

<span data-ttu-id="10dc1-199">Sebbene non sia uno stile valido per `ToString` avere effetti collaterali, nell'esempio viene dimostrato che non si è verificata alcuna conversione boxing per le tre chiamate di `x.ToString()` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-199">Although it is bad style for `ToString` to have side effects, the example demonstrates that no boxing occurred for the three invocations of `x.ToString()`.</span></span>

<span data-ttu-id="10dc1-200">Analogamente, la conversione boxing non viene mai eseguita in modo implicito quando si accede a un membro in un parametro di tipo vincolato.</span><span class="sxs-lookup"><span data-stu-id="10dc1-200">Similarly, boxing never implicitly occurs when accessing a member on a constrained type parameter.</span></span> <span data-ttu-id="10dc1-201">Si supponga, ad esempio, `ICounter` che un'interfaccia contenga un metodo `Increment` che può essere utilizzato per modificare un valore.</span><span class="sxs-lookup"><span data-stu-id="10dc1-201">For example, suppose an interface `ICounter` contains a method `Increment` which can be used to modify a value.</span></span> <span data-ttu-id="10dc1-202">Se `ICounter` viene usato come vincolo, l'implementazione del `Increment` metodo viene chiamata con un riferimento alla variabile che `Increment` è stata chiamata su, mai una copia boxed.</span><span class="sxs-lookup"><span data-stu-id="10dc1-202">If `ICounter` is used as a constraint, the implementation of the `Increment` method is called with a reference to the variable that `Increment` was called on, never a boxed copy.</span></span>

```csharp
using System;

interface ICounter
{
    void Increment();
}

struct Counter: ICounter
{
    int value;

    public override string ToString() {
        return value.ToString();
    }

    void ICounter.Increment() {
        value++;
    }
}

class Program
{
    static void Test<T>() where T: ICounter, new() {
        T x = new T();
        Console.WriteLine(x);
        x.Increment();                    // Modify x
        Console.WriteLine(x);
        ((ICounter)x).Increment();        // Modify boxed copy of x
        Console.WriteLine(x);
    }

    static void Main() {
        Test<Counter>();
    }
}
```

<span data-ttu-id="10dc1-203">La prima chiamata a `Increment` modifica il valore nella variabile `x` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-203">The first call to `Increment` modifies the value in the variable `x`.</span></span> <span data-ttu-id="10dc1-204">Questa operazione non equivale alla seconda chiamata a `Increment` , che modifica il valore in una copia boxed di `x` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-204">This is not equivalent to the second call to `Increment`, which modifies the value in a boxed copy of `x`.</span></span> <span data-ttu-id="10dc1-205">Quindi, l'output del programma è:</span><span class="sxs-lookup"><span data-stu-id="10dc1-205">Thus, the output of the program is:</span></span>
```console
0
1
1
```

<span data-ttu-id="10dc1-206">Per ulteriori informazioni su conversione boxing e unboxing, vedere [conversione boxing e](types.md#boxing-and-unboxing)unboxing.</span><span class="sxs-lookup"><span data-stu-id="10dc1-206">For further details on boxing and unboxing, see [Boxing and unboxing](types.md#boxing-and-unboxing).</span></span>

### <a name="meaning-of-this"></a><span data-ttu-id="10dc1-207">Significato di questo</span><span class="sxs-lookup"><span data-stu-id="10dc1-207">Meaning of this</span></span>

<span data-ttu-id="10dc1-208">All'interno di un costruttore di istanza o di un membro della funzione di istanza di una classe, `this` è classificato come valore.</span><span class="sxs-lookup"><span data-stu-id="10dc1-208">Within an instance constructor or instance function member of a class, `this` is classified as a value.</span></span> <span data-ttu-id="10dc1-209">Pertanto, while `this` può essere utilizzato per fare riferimento all'istanza per la quale è stato richiamato il membro della funzione, non è possibile assegnare a `this` in un membro di funzione di una classe.</span><span class="sxs-lookup"><span data-stu-id="10dc1-209">Thus, while `this` can be used to refer to the instance for which the function member was invoked, it is not possible to assign to `this` in a function member of a class.</span></span>

<span data-ttu-id="10dc1-210">All'interno di un costruttore di istanza di uno struct, `this` corrisponde a un `out` parametro del tipo struct e all'interno di un membro della funzione di istanza di uno struct `this` corrisponde a un `ref` parametro del tipo struct.</span><span class="sxs-lookup"><span data-stu-id="10dc1-210">Within an instance constructor of a struct, `this` corresponds to an `out` parameter of the struct type, and within an instance function member of a struct, `this` corresponds to a `ref` parameter of the struct type.</span></span> <span data-ttu-id="10dc1-211">In entrambi i casi, `this` è classificato come una variabile ed è possibile modificare l'intero struct per cui il membro della funzione è stato richiamato assegnando a `this` o passando come `ref` `out` parametro o.</span><span class="sxs-lookup"><span data-stu-id="10dc1-211">In both cases, `this` is classified as a variable, and it is possible to modify the entire struct for which the function member was invoked by assigning to `this` or by passing this as a `ref` or `out` parameter.</span></span>

### <a name="field-initializers"></a><span data-ttu-id="10dc1-212">Inizializzatori di campo</span><span class="sxs-lookup"><span data-stu-id="10dc1-212">Field initializers</span></span>

<span data-ttu-id="10dc1-213">Come descritto in [valori predefiniti](structs.md#default-values), il valore predefinito di uno struct è costituito dal valore risultante dall'impostazione di tutti i campi di tipo valore sul valore predefinito e di tutti i campi di tipo riferimento su `null` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-213">As described in [Default values](structs.md#default-values), the default value of a struct consists of the value that results from setting all value type fields to their default value and all reference type fields to `null`.</span></span> <span data-ttu-id="10dc1-214">Per questo motivo, uno struct non consente alle dichiarazioni dei campi di istanza di includere inizializzatori di variabili.</span><span class="sxs-lookup"><span data-stu-id="10dc1-214">For this reason, a struct does not permit instance field declarations to include variable initializers.</span></span> <span data-ttu-id="10dc1-215">Questa restrizione si applica solo ai campi di istanza.</span><span class="sxs-lookup"><span data-stu-id="10dc1-215">This restriction applies only to instance fields.</span></span> <span data-ttu-id="10dc1-216">I campi statici di uno struct possono includere inizializzatori di variabile.</span><span class="sxs-lookup"><span data-stu-id="10dc1-216">Static fields of a struct are permitted to include variable initializers.</span></span>

<span data-ttu-id="10dc1-217">L'esempio:</span><span class="sxs-lookup"><span data-stu-id="10dc1-217">The example</span></span>
```csharp
struct Point
{
    public int x = 1;  // Error, initializer not permitted
    public int y = 1;  // Error, initializer not permitted
}
```
<span data-ttu-id="10dc1-218">è in errore perché le dichiarazioni dei campi di istanza includono inizializzatori di variabile.</span><span class="sxs-lookup"><span data-stu-id="10dc1-218">is in error because the instance field declarations include variable initializers.</span></span>

### <a name="constructors"></a><span data-ttu-id="10dc1-219">Costruttori</span><span class="sxs-lookup"><span data-stu-id="10dc1-219">Constructors</span></span>

<span data-ttu-id="10dc1-220">A differenza di una classe, uno struct non è autorizzato a dichiarare un costruttore di istanza senza parametri.</span><span class="sxs-lookup"><span data-stu-id="10dc1-220">Unlike a class, a struct is not permitted to declare a parameterless instance constructor.</span></span> <span data-ttu-id="10dc1-221">Al contrario, ogni struct dispone implicitamente di un costruttore di istanza senza parametri che restituisce sempre il valore risultante dall'impostazione di tutti i campi di tipo valore sul valore predefinito e di tutti i campi di tipo riferimento su null ([costruttori predefiniti](types.md#default-constructors)).</span><span class="sxs-lookup"><span data-stu-id="10dc1-221">Instead, every struct implicitly has a parameterless instance constructor which always returns the value that results from setting all value type fields to their default value and all reference type fields to null ([Default constructors](types.md#default-constructors)).</span></span> <span data-ttu-id="10dc1-222">Uno struct può dichiarare i costruttori di istanza con parametri.</span><span class="sxs-lookup"><span data-stu-id="10dc1-222">A struct can declare instance constructors having parameters.</span></span> <span data-ttu-id="10dc1-223">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="10dc1-223">For example</span></span>
```csharp
struct Point
{
    int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

<span data-ttu-id="10dc1-224">Data la dichiarazione precedente, le istruzioni</span><span class="sxs-lookup"><span data-stu-id="10dc1-224">Given the above declaration, the statements</span></span>
```csharp
Point p1 = new Point();
Point p2 = new Point(0, 0);
```
<span data-ttu-id="10dc1-225">entrambi creano un oggetto `Point` con `x` e `y` inizializzato su zero.</span><span class="sxs-lookup"><span data-stu-id="10dc1-225">both create a `Point` with `x` and `y` initialized to zero.</span></span>

<span data-ttu-id="10dc1-226">Un costruttore di istanze struct non è autorizzato a includere un inizializzatore del costruttore del modulo `base(...)` .</span><span class="sxs-lookup"><span data-stu-id="10dc1-226">A struct instance constructor is not permitted to include a constructor initializer of the form `base(...)`.</span></span>

<span data-ttu-id="10dc1-227">Se il costruttore di istanze struct non specifica un inizializzatore di costruttore, la `this` variabile corrispondente a un `out` parametro del tipo struct e simile a un `out` parametro `this` deve essere assegnata definitivamente ([assegnazione definita](variables.md#definite-assignment)) in ogni posizione in cui viene restituito il costruttore.</span><span class="sxs-lookup"><span data-stu-id="10dc1-227">If the struct instance constructor doesn't specify a constructor initializer, the `this` variable corresponds to an `out` parameter of the struct type, and similar to an `out` parameter, `this` must be definitely assigned ([Definite assignment](variables.md#definite-assignment)) at every location where the constructor returns.</span></span> <span data-ttu-id="10dc1-228">Se il costruttore di istanze struct specifica un inizializzatore di costruttore, la `this` variabile corrisponde a un `ref` parametro del tipo struct e analogamente a un `ref` parametro `this` viene considerato definitivamente assegnato alla voce al corpo del costruttore.</span><span class="sxs-lookup"><span data-stu-id="10dc1-228">If the struct instance constructor specifies a constructor initializer, the `this` variable corresponds to a `ref` parameter of the struct type, and similar to a `ref` parameter, `this` is considered definitely assigned on entry to the constructor body.</span></span> <span data-ttu-id="10dc1-229">Si consideri l'implementazione del costruttore di istanze seguente:</span><span class="sxs-lookup"><span data-stu-id="10dc1-229">Consider the instance constructor implementation below:</span></span>
```csharp
struct Point
{
    int x, y;

    public int X {
        set { x = value; }
    }

    public int Y {
        set { y = value; }
    }

    public Point(int x, int y) {
        X = x;        // error, this is not yet definitely assigned
        Y = y;        // error, this is not yet definitely assigned
    }
}
```

<span data-ttu-id="10dc1-230">Non è possibile chiamare alcuna funzione membro di istanza (incluse le funzioni di accesso set per le proprietà `X` e `Y` ) finché tutti i campi dello struct costruito non sono stati assegnati definitivamente.</span><span class="sxs-lookup"><span data-stu-id="10dc1-230">No instance member function (including the set accessors for the properties `X` and `Y`) can be called until all fields of the struct being constructed have been definitely assigned.</span></span> <span data-ttu-id="10dc1-231">L'unica eccezione riguarda le proprietà implementate automaticamente ([proprietà implementate automaticamente](classes.md#automatically-implemented-properties)).</span><span class="sxs-lookup"><span data-stu-id="10dc1-231">The only exception involves automatically implemented properties ([Automatically implemented properties](classes.md#automatically-implemented-properties)).</span></span> <span data-ttu-id="10dc1-232">Le regole di assegnazione definite ([espressioni di assegnazione semplice](variables.md#simple-assignment-expressions)) estendono in modo specifico l'assegnazione a una proprietà automatica di un tipo struct all'interno di un costruttore di istanza del tipo struct. tale assegnazione viene considerata un'assegnazione definita del campo sottostante nascosto della proprietà automatica.</span><span class="sxs-lookup"><span data-stu-id="10dc1-232">The definite assignment rules ([Simple assignment expressions](variables.md#simple-assignment-expressions)) specifically exempt assignment to an auto-property of a struct type within an instance constructor of that struct type: such an assignment is considered a definite assignment of the hidden backing field of the auto-property.</span></span> <span data-ttu-id="10dc1-233">Di conseguenza, sono consentiti gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="10dc1-233">Thus, the following is allowed:</span></span>

```csharp
struct Point
{
    public int X { get; set; }
    public int Y { get; set; }

    public Point(int x, int y) {
        X = x;      // allowed, definitely assigns backing field
        Y = y;      // allowed, definitely assigns backing field
    }
```

### <a name="destructors"></a><span data-ttu-id="10dc1-234">Distruttori</span><span class="sxs-lookup"><span data-stu-id="10dc1-234">Destructors</span></span>

<span data-ttu-id="10dc1-235">Uno struct non è autorizzato a dichiarare un distruttore.</span><span class="sxs-lookup"><span data-stu-id="10dc1-235">A struct is not permitted to declare a destructor.</span></span>

### <a name="static-constructors"></a><span data-ttu-id="10dc1-236">Costruttori statici</span><span class="sxs-lookup"><span data-stu-id="10dc1-236">Static constructors</span></span>

<span data-ttu-id="10dc1-237">I costruttori statici per gli struct seguono la maggior parte delle stesse regole delle classi.</span><span class="sxs-lookup"><span data-stu-id="10dc1-237">Static constructors for structs follow most of the same rules as for classes.</span></span> <span data-ttu-id="10dc1-238">L'esecuzione di un costruttore statico per un tipo struct viene attivata dal primo degli eventi seguenti che si verificano all'interno di un dominio dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="10dc1-238">The execution of a static constructor for a struct type is triggered by the first of the following events to occur within an application domain:</span></span>

*  <span data-ttu-id="10dc1-239">Viene fatto riferimento A un membro statico del tipo struct.</span><span class="sxs-lookup"><span data-stu-id="10dc1-239">A static member of the struct type is referenced.</span></span>
*  <span data-ttu-id="10dc1-240">Viene chiamato un costruttore dichiarato in modo esplicito del tipo struct.</span><span class="sxs-lookup"><span data-stu-id="10dc1-240">An explicitly declared constructor of the struct type is called.</span></span>

<span data-ttu-id="10dc1-241">La creazione di valori predefiniti ([valori predefiniti](structs.md#default-values)) di tipi struct non attiva il costruttore statico.</span><span class="sxs-lookup"><span data-stu-id="10dc1-241">The creation of default values ([Default values](structs.md#default-values)) of struct types does not trigger the static constructor.</span></span> <span data-ttu-id="10dc1-242">Un esempio è il valore iniziale degli elementi in una matrice.</span><span class="sxs-lookup"><span data-stu-id="10dc1-242">(An example of this is the initial value of elements in an array.)</span></span>

## <a name="struct-examples"></a><span data-ttu-id="10dc1-243">Esempi di struct</span><span class="sxs-lookup"><span data-stu-id="10dc1-243">Struct examples</span></span>

<span data-ttu-id="10dc1-244">Di seguito vengono illustrati due esempi significativi di utilizzo dei `struct` tipi per creare tipi che possono essere utilizzati in modo analogo ai tipi predefiniti del linguaggio, ma con la semantica modificata.</span><span class="sxs-lookup"><span data-stu-id="10dc1-244">The following shows two significant examples of using `struct` types to create types that can be used similarly to the predefined types of the language, but with modified semantics.</span></span>

### <a name="database-integer-type"></a><span data-ttu-id="10dc1-245">Tipo Integer database</span><span class="sxs-lookup"><span data-stu-id="10dc1-245">Database integer type</span></span>

<span data-ttu-id="10dc1-246">Lo `DBInt` struct seguente implementa un tipo integer che può rappresentare il set completo di valori del `int` tipo, più uno stato aggiuntivo che indica un valore sconosciuto.</span><span class="sxs-lookup"><span data-stu-id="10dc1-246">The `DBInt` struct below implements an integer type that can represent the complete set of values of the `int` type, plus an additional state that indicates an unknown value.</span></span> <span data-ttu-id="10dc1-247">Un tipo con queste caratteristiche viene comunemente usato nei database.</span><span class="sxs-lookup"><span data-stu-id="10dc1-247">A type with these characteristics is commonly used in databases.</span></span>

```csharp
using System;

public struct DBInt
{
    // The Null member represents an unknown DBInt value.

    public static readonly DBInt Null = new DBInt();

    // When the defined field is true, this DBInt represents a known value
    // which is stored in the value field. When the defined field is false,
    // this DBInt represents an unknown value, and the value field is 0.

    int value;
    bool defined;

    // Private instance constructor. Creates a DBInt with a known value.

    DBInt(int value) {
        this.value = value;
        this.defined = true;
    }

    // The IsNull property is true if this DBInt represents an unknown value.

    public bool IsNull { get { return !defined; } }

    // The Value property is the known value of this DBInt, or 0 if this
    // DBInt represents an unknown value.

    public int Value { get { return value; } }

    // Implicit conversion from int to DBInt.

    public static implicit operator DBInt(int x) {
        return new DBInt(x);
    }

    // Explicit conversion from DBInt to int. Throws an exception if the
    // given DBInt represents an unknown value.

    public static explicit operator int(DBInt x) {
        if (!x.defined) throw new InvalidOperationException();
        return x.value;
    }

    public static DBInt operator +(DBInt x) {
        return x;
    }

    public static DBInt operator -(DBInt x) {
        return x.defined ? -x.value : Null;
    }

    public static DBInt operator +(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value + y.value: Null;
    }

    public static DBInt operator -(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value - y.value: Null;
    }

    public static DBInt operator *(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value * y.value: Null;
    }

    public static DBInt operator /(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value / y.value: Null;
    }

    public static DBInt operator %(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value % y.value: Null;
    }

    public static DBBool operator ==(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value == y.value: DBBool.Null;
    }

    public static DBBool operator !=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value != y.value: DBBool.Null;
    }

    public static DBBool operator >(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value > y.value: DBBool.Null;
    }

    public static DBBool operator <(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value < y.value: DBBool.Null;
    }

    public static DBBool operator >=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value >= y.value: DBBool.Null;
    }

    public static DBBool operator <=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value <= y.value: DBBool.Null;
    }

    public override bool Equals(object obj) {
        if (!(obj is DBInt)) return false;
        DBInt x = (DBInt)obj;
        return value == x.value && defined == x.defined;
    }

    public override int GetHashCode() {
        return value;
    }

    public override string ToString() {
        return defined? value.ToString(): "DBInt.Null";
    }
}
```

### <a name="database-boolean-type"></a><span data-ttu-id="10dc1-248">Tipo di database booleano</span><span class="sxs-lookup"><span data-stu-id="10dc1-248">Database boolean type</span></span>

<span data-ttu-id="10dc1-249">Lo `DBBool` struct seguente implementa un tipo logico a tre valori.</span><span class="sxs-lookup"><span data-stu-id="10dc1-249">The `DBBool` struct below implements a three-valued logical type.</span></span> <span data-ttu-id="10dc1-250">I valori possibili di questo tipo sono `DBBool.True` , `DBBool.False` e `DBBool.Null` , dove il `Null` membro indica un valore sconosciuto.</span><span class="sxs-lookup"><span data-stu-id="10dc1-250">The possible values of this type are `DBBool.True`, `DBBool.False`, and `DBBool.Null`, where the `Null` member indicates an unknown value.</span></span> <span data-ttu-id="10dc1-251">Tali tipi logici a tre valori sono comunemente utilizzati nei database di.</span><span class="sxs-lookup"><span data-stu-id="10dc1-251">Such three-valued logical types are commonly used in databases.</span></span>

```csharp
using System;

public struct DBBool
{
    // The three possible DBBool values.

    public static readonly DBBool Null = new DBBool(0);
    public static readonly DBBool False = new DBBool(-1);
    public static readonly DBBool True = new DBBool(1);

    // Private field that stores -1, 0, 1 for False, Null, True.

    sbyte value;

    // Private instance constructor. The value parameter must be -1, 0, or 1.

    DBBool(int value) {
        this.value = (sbyte)value;
    }

    // Properties to examine the value of a DBBool. Return true if this
    // DBBool has the given value, false otherwise.

    public bool IsNull { get { return value == 0; } }

    public bool IsFalse { get { return value < 0; } }

    public bool IsTrue { get { return value > 0; } }

    // Implicit conversion from bool to DBBool. Maps true to DBBool.True and
    // false to DBBool.False.

    public static implicit operator DBBool(bool x) {
        return x? True: False;
    }

    // Explicit conversion from DBBool to bool. Throws an exception if the
    // given DBBool is Null, otherwise returns true or false.

    public static explicit operator bool(DBBool x) {
        if (x.value == 0) throw new InvalidOperationException();
        return x.value > 0;
    }

    // Equality operator. Returns Null if either operand is Null, otherwise
    // returns True or False.

    public static DBBool operator ==(DBBool x, DBBool y) {
        if (x.value == 0 || y.value == 0) return Null;
        return x.value == y.value? True: False;
    }

    // Inequality operator. Returns Null if either operand is Null, otherwise
    // returns True or False.

    public static DBBool operator !=(DBBool x, DBBool y) {
        if (x.value == 0 || y.value == 0) return Null;
        return x.value != y.value? True: False;
    }

    // Logical negation operator. Returns True if the operand is False, Null
    // if the operand is Null, or False if the operand is True.

    public static DBBool operator !(DBBool x) {
        return new DBBool(-x.value);
    }

    // Logical AND operator. Returns False if either operand is False,
    // otherwise Null if either operand is Null, otherwise True.

    public static DBBool operator &(DBBool x, DBBool y) {
        return new DBBool(x.value < y.value? x.value: y.value);
    }

    // Logical OR operator. Returns True if either operand is True, otherwise
    // Null if either operand is Null, otherwise False.

    public static DBBool operator |(DBBool x, DBBool y) {
        return new DBBool(x.value > y.value? x.value: y.value);
    }

    // Definitely true operator. Returns true if the operand is True, false
    // otherwise.

    public static bool operator true(DBBool x) {
        return x.value > 0;
    }

    // Definitely false operator. Returns true if the operand is False, false
    // otherwise.

    public static bool operator false(DBBool x) {
        return x.value < 0;
    }

    public override bool Equals(object obj) {
        if (!(obj is DBBool)) return false;
        return value == ((DBBool)obj).value;
    }

    public override int GetHashCode() {
        return value;
    }

    public override string ToString() {
        if (value > 0) return "DBBool.True";
        if (value < 0) return "DBBool.False";
        return "DBBool.Null";
    }
}
```
