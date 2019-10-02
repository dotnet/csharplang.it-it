---
ms.openlocfilehash: d162d4b6a489032dcdfca9094a39d88fd03d4013
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71704090"
---
# <a name="delegates"></a><span data-ttu-id="16033-101">Delegati</span><span class="sxs-lookup"><span data-stu-id="16033-101">Delegates</span></span>

<span data-ttu-id="16033-102">I delegati consentono scenari in cui altri C++linguaggi, ad esempio, Pascal e modula, sono stati risolti con i puntatori a funzione.</span><span class="sxs-lookup"><span data-stu-id="16033-102">Delegates enable scenarios that other languages—such as C++, Pascal, and Modula -- have addressed with function pointers.</span></span> <span data-ttu-id="16033-103">A differenza C++ dei puntatori a funzione, tuttavia, i delegati sono completamente orientati agli oggetti e, a differenza C++ dei puntatori alle funzioni membro, i delegati incapsulano sia un'istanza dell'oggetto che un metodo.</span><span class="sxs-lookup"><span data-stu-id="16033-103">Unlike C++ function pointers, however, delegates are fully object oriented, and unlike C++ pointers to member functions, delegates encapsulate both an object instance and a method.</span></span>

<span data-ttu-id="16033-104">Una dichiarazione di delegato definisce una classe derivata dalla classe `System.Delegate`.</span><span class="sxs-lookup"><span data-stu-id="16033-104">A delegate declaration defines a class that is derived from the class `System.Delegate`.</span></span> <span data-ttu-id="16033-105">Un'istanza del delegato incapsula un elenco chiamate, ovvero un elenco di uno o più metodi, ognuno dei quali viene definito entità richiamabile.</span><span class="sxs-lookup"><span data-stu-id="16033-105">A delegate instance encapsulates an invocation list, which is a list of one or more methods, each of which is referred to as a callable entity.</span></span> <span data-ttu-id="16033-106">Per i metodi di istanza, un'entità chiamabile è costituita da un'istanza e da un metodo su tale istanza.</span><span class="sxs-lookup"><span data-stu-id="16033-106">For instance methods, a callable entity consists of an instance and a method on that instance.</span></span> <span data-ttu-id="16033-107">Per i metodi statici, un'entità chiamabile è costituita da un solo metodo.</span><span class="sxs-lookup"><span data-stu-id="16033-107">For static methods, a callable entity consists of just a method.</span></span> <span data-ttu-id="16033-108">La chiamata di un'istanza del delegato con un set di argomenti appropriato comporta la chiamata di ogni entità chiamabile del delegato con il set di argomenti specificato.</span><span class="sxs-lookup"><span data-stu-id="16033-108">Invoking a delegate instance with an appropriate set of arguments causes each of the delegate's callable entities to be invoked with the given set of arguments.</span></span>

<span data-ttu-id="16033-109">Una proprietà interessante e utile di un'istanza del delegato è che non è in grado di conoscere o preoccuparsi delle classi dei metodi incapsulati. ciò che conta è che questi metodi siano compatibili ([dichiarazioni delegate](delegates.md#delegate-declarations)) con il tipo del delegato.</span><span class="sxs-lookup"><span data-stu-id="16033-109">An interesting and useful property of a delegate instance is that it does not know or care about the classes of the methods it encapsulates; all that matters is that those methods be compatible ([Delegate declarations](delegates.md#delegate-declarations)) with the delegate's type.</span></span> <span data-ttu-id="16033-110">Questa operazione rende i delegati perfettamente adatti alla chiamata "anonima".</span><span class="sxs-lookup"><span data-stu-id="16033-110">This makes delegates perfectly suited for "anonymous" invocation.</span></span>

## <a name="delegate-declarations"></a><span data-ttu-id="16033-111">Dichiarazioni di delegati</span><span class="sxs-lookup"><span data-stu-id="16033-111">Delegate declarations</span></span>

<span data-ttu-id="16033-112">Un *delegate_declaration* è un *type_declaration* ([dichiarazioni di tipo](namespaces.md#type-declarations)) che dichiara un nuovo tipo delegato.</span><span class="sxs-lookup"><span data-stu-id="16033-112">A *delegate_declaration* is a *type_declaration* ([Type declarations](namespaces.md#type-declarations)) that declares a new delegate type.</span></span>

```antlr
delegate_declaration
    : attributes? delegate_modifier* 'delegate' return_type
      identifier variant_type_parameter_list?
      '(' formal_parameter_list? ')' type_parameter_constraints_clause* ';'
    ;

delegate_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | delegate_modifier_unsafe
    ;
```

<span data-ttu-id="16033-113">Si tratta di un errore in fase di compilazione perché lo stesso modificatore venga visualizzato più volte in una dichiarazione di delegato.</span><span class="sxs-lookup"><span data-stu-id="16033-113">It is a compile-time error for the same modifier to appear multiple times in a delegate declaration.</span></span>

<span data-ttu-id="16033-114">Il modificatore `new` è consentito solo su delegati dichiarati all'interno di un altro tipo, nel qual caso specifica che tale delegato nasconde un membro ereditato con lo stesso nome, come descritto nel [nuovo modificatore](classes.md#the-new-modifier).</span><span class="sxs-lookup"><span data-stu-id="16033-114">The `new` modifier is only permitted on delegates declared within another type, in which case it specifies that such a delegate hides an inherited member by the same name, as described in [The new modifier](classes.md#the-new-modifier).</span></span>

<span data-ttu-id="16033-115">I modificatori `public`, `protected`, `internal` e `private` controllano l'accessibilità del tipo delegato.</span><span class="sxs-lookup"><span data-stu-id="16033-115">The `public`, `protected`, `internal`, and `private` modifiers control the accessibility of the delegate type.</span></span> <span data-ttu-id="16033-116">A seconda del contesto in cui si verifica la dichiarazione del delegato, alcuni di questi modificatori potrebbero non essere consentiti ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)).</span><span class="sxs-lookup"><span data-stu-id="16033-116">Depending on the context in which the delegate declaration occurs, some of these modifiers may not be permitted ([Declared accessibility](basic-concepts.md#declared-accessibility)).</span></span>

<span data-ttu-id="16033-117">Il nome del tipo del delegato è *Identifier*.</span><span class="sxs-lookup"><span data-stu-id="16033-117">The delegate's type name is *identifier*.</span></span>

<span data-ttu-id="16033-118">Il *formal_parameter_list* facoltativo specifica i parametri del delegato e *return_type* indica il tipo restituito del delegato.</span><span class="sxs-lookup"><span data-stu-id="16033-118">The optional *formal_parameter_list* specifies the parameters of the delegate, and *return_type* indicates the return type of the delegate.</span></span>

<span data-ttu-id="16033-119">Il *variant_type_parameter_list* facoltativo ([elenchi di parametri di tipo Variant](interfaces.md#variant-type-parameter-lists)) specifica i parametri di tipo per il delegato stesso.</span><span class="sxs-lookup"><span data-stu-id="16033-119">The optional *variant_type_parameter_list* ([Variant type parameter lists](interfaces.md#variant-type-parameter-lists)) specifies the type parameters to the delegate itself.</span></span>

<span data-ttu-id="16033-120">Il tipo restituito di un tipo di delegato deve essere `void` o indipendente dall'output ([sicurezza dalla varianza](interfaces.md#variance-safety)).</span><span class="sxs-lookup"><span data-stu-id="16033-120">The return type of a delegate type must be either `void`, or output-safe ([Variance safety](interfaces.md#variance-safety)).</span></span>

<span data-ttu-id="16033-121">Tutti i tipi di parametro formali di un tipo delegato devono essere indipendenti dall'input.</span><span class="sxs-lookup"><span data-stu-id="16033-121">All the formal parameter types of a delegate type must be input-safe.</span></span> <span data-ttu-id="16033-122">Inoltre, tutti i tipi di parametro `out` o `ref` devono essere anche indipendenti dall'output.</span><span class="sxs-lookup"><span data-stu-id="16033-122">Additionally, any `out` or `ref` parameter types must also be output-safe.</span></span> <span data-ttu-id="16033-123">Si noti che è necessario che anche i parametri `out` siano indipendenti dall'input, a causa di una limitazione della piattaforma di esecuzione sottostante.</span><span class="sxs-lookup"><span data-stu-id="16033-123">Note that even `out` parameters are required to be input-safe, due to a limitation of the underlying execution platform.</span></span>

<span data-ttu-id="16033-124">I tipi di C# delegati in sono equivalenti al nome e non sono strutturalmente equivalenti.</span><span class="sxs-lookup"><span data-stu-id="16033-124">Delegate types in C# are name equivalent, not structurally equivalent.</span></span> <span data-ttu-id="16033-125">In particolare, due tipi delegati diversi che hanno gli stessi elenchi di parametri e il tipo restituito sono considerati tipi delegati diversi.</span><span class="sxs-lookup"><span data-stu-id="16033-125">Specifically, two different delegate types that have the same parameter lists and return type are considered different delegate types.</span></span> <span data-ttu-id="16033-126">Tuttavia, le istanze di due tipi delegati distinti ma strutturalmente equivalenti possono essere confrontate come uguali ([operatori di uguaglianza dei delegati](expressions.md#delegate-equality-operators)).</span><span class="sxs-lookup"><span data-stu-id="16033-126">However, instances of two distinct but structurally equivalent delegate types may compare as equal ([Delegate equality operators](expressions.md#delegate-equality-operators)).</span></span>

<span data-ttu-id="16033-127">Esempio:</span><span class="sxs-lookup"><span data-stu-id="16033-127">For example:</span></span>

```csharp
delegate int D1(int i, double d);

class A
{
    public static int M1(int a, double b) {...}
}

class B
{
    delegate int D2(int c, double d);
    public static int M1(int f, double g) {...}
    public static void M2(int k, double l) {...}
    public static int M3(int g) {...}
    public static void M4(int g) {...}
}
```

<span data-ttu-id="16033-128">I metodi `A.M1` e `B.M1` sono compatibili con i tipi delegati `D1` e `D2`, perché hanno lo stesso tipo restituito e l'elenco di parametri. Tuttavia, questi tipi delegati sono due tipi diversi, quindi non sono intercambiabili.</span><span class="sxs-lookup"><span data-stu-id="16033-128">The methods `A.M1` and `B.M1` are compatible with both the delegate types `D1` and `D2` , since they have the same return type and parameter list; however, these delegate types are two different types, so they are not interchangeable.</span></span> <span data-ttu-id="16033-129">I metodi `B.M2`, `B.M3` e `B.M4` sono incompatibili con i tipi delegati `D1` e `D2`, poiché presentano tipi restituiti o elenchi di parametri diversi.</span><span class="sxs-lookup"><span data-stu-id="16033-129">The methods `B.M2`, `B.M3`, and `B.M4` are incompatible with the delegate types `D1` and `D2`, since they have different return types or parameter lists.</span></span>

<span data-ttu-id="16033-130">Analogamente ad altre dichiarazioni di tipo generico, è necessario assegnare argomenti di tipo per creare un tipo delegato costruito.</span><span class="sxs-lookup"><span data-stu-id="16033-130">Like other generic type declarations, type arguments must be given to create a constructed delegate type.</span></span> <span data-ttu-id="16033-131">I tipi di parametro e il tipo restituito di un tipo delegato costruito vengono creati sostituendo, per ogni parametro di tipo nella dichiarazione del delegato, l'argomento di tipo corrispondente del tipo delegato costruito.</span><span class="sxs-lookup"><span data-stu-id="16033-131">The parameter types and return type of a constructed delegate type are created by substituting, for each type parameter in the delegate declaration, the corresponding type argument of the constructed delegate type.</span></span> <span data-ttu-id="16033-132">Il tipo restituito e i tipi di parametro risultanti vengono utilizzati per determinare quali metodi sono compatibili con un tipo delegato costruito.</span><span class="sxs-lookup"><span data-stu-id="16033-132">The resulting return type and parameter types are used in determining what methods are compatible with a constructed delegate type.</span></span> <span data-ttu-id="16033-133">Esempio:</span><span class="sxs-lookup"><span data-stu-id="16033-133">For example:</span></span>

```csharp
delegate bool Predicate<T>(T value);

class X
{
    static bool F(int i) {...}
    static bool G(string s) {...}
}
```

<span data-ttu-id="16033-134">Il metodo `X.F` è compatibile con il tipo delegato `Predicate<int>` e il metodo `X.G` è compatibile con il tipo di delegato `Predicate<string>`.</span><span class="sxs-lookup"><span data-stu-id="16033-134">The method `X.F` is compatible with the delegate type `Predicate<int>` and the method `X.G` is compatible with the delegate type `Predicate<string>` .</span></span>

<span data-ttu-id="16033-135">L'unico modo per dichiarare un tipo delegato è tramite un *delegate_declaration*.</span><span class="sxs-lookup"><span data-stu-id="16033-135">The only way to declare a delegate type is via a *delegate_declaration*.</span></span> <span data-ttu-id="16033-136">Un tipo delegato è un tipo di classe derivato da `System.Delegate`.</span><span class="sxs-lookup"><span data-stu-id="16033-136">A delegate type is a class type that is derived from `System.Delegate`.</span></span> <span data-ttu-id="16033-137">I tipi delegati sono implicitamente `sealed`, pertanto non è consentito derivare alcun tipo da un tipo delegato.</span><span class="sxs-lookup"><span data-stu-id="16033-137">Delegate types are implicitly `sealed`, so it is not permissible to derive any type from a delegate type.</span></span> <span data-ttu-id="16033-138">Non è inoltre consentito derivare un tipo di classe non delegata da `System.Delegate`.</span><span class="sxs-lookup"><span data-stu-id="16033-138">It is also not permissible to derive a non-delegate class type from `System.Delegate`.</span></span> <span data-ttu-id="16033-139">Si noti che `System.Delegate` non è un tipo delegato. si tratta di un tipo di classe da cui vengono derivati tutti i tipi delegati.</span><span class="sxs-lookup"><span data-stu-id="16033-139">Note that `System.Delegate` is not itself a delegate type; it is a class type from which all delegate types are derived.</span></span>

<span data-ttu-id="16033-140">C#fornisce una sintassi speciale per la creazione e la creazione di istanze di delegati.</span><span class="sxs-lookup"><span data-stu-id="16033-140">C# provides special syntax for delegate instantiation and invocation.</span></span> <span data-ttu-id="16033-141">Fatta eccezione per la creazione di istanze, qualsiasi operazione che può essere applicata a una classe o a un'istanza di classe può essere applicata rispettivamente a una classe o a un'istanza di delegato.</span><span class="sxs-lookup"><span data-stu-id="16033-141">Except for instantiation, any operation that can be applied to a class or class instance can also be applied to a delegate class or instance, respectively.</span></span> <span data-ttu-id="16033-142">In particolare, è possibile accedere ai membri del tipo `System.Delegate` tramite la consueta sintassi di accesso ai membri.</span><span class="sxs-lookup"><span data-stu-id="16033-142">In particular, it is possible to access members of the `System.Delegate` type via the usual member access syntax.</span></span>

<span data-ttu-id="16033-143">Il set di metodi incapsulati da un'istanza di delegato è denominato elenco chiamate.</span><span class="sxs-lookup"><span data-stu-id="16033-143">The set of methods encapsulated by a delegate instance is called an invocation list.</span></span> <span data-ttu-id="16033-144">Quando viene creata un'istanza del delegato (la compatibilità con i[delegati](delegates.md#delegate-compatibility)) da un singolo metodo, il metodo viene incapsulato e il relativo elenco chiamate contiene una sola voce.</span><span class="sxs-lookup"><span data-stu-id="16033-144">When a delegate instance is created ([Delegate compatibility](delegates.md#delegate-compatibility)) from a single method, it encapsulates that method, and its invocation list contains only one entry.</span></span> <span data-ttu-id="16033-145">Tuttavia, quando vengono combinate due istanze delegate non null, i rispettivi elenchi chiamate vengono concatenati nell'operando sinistro dell'ordine, quindi nell'operando destro, per formare un nuovo elenco chiamate, che contiene due o più voci.</span><span class="sxs-lookup"><span data-stu-id="16033-145">However, when two non-null delegate instances are combined, their invocation lists are concatenated -- in the order left operand then right operand -- to form a new invocation list, which contains two or more entries.</span></span>

<span data-ttu-id="16033-146">I delegati vengono combinati usando l'operatore binario `+` ([operatore di addizione](expressions.md#addition-operator)) e gli operatori `+=` ([assegnazione composta](expressions.md#compound-assignment)).</span><span class="sxs-lookup"><span data-stu-id="16033-146">Delegates are combined using the binary `+` ([Addition operator](expressions.md#addition-operator)) and `+=` operators ([Compound assignment](expressions.md#compound-assignment)).</span></span> <span data-ttu-id="16033-147">Un delegato può essere rimosso da una combinazione di delegati, usando il binario `-` ([operatore di sottrazione](expressions.md#subtraction-operator)) e gli operatori `-=` ([assegnazione composta](expressions.md#compound-assignment)).</span><span class="sxs-lookup"><span data-stu-id="16033-147">A delegate can be removed from a combination of delegates, using the binary `-` ([Subtraction operator](expressions.md#subtraction-operator)) and `-=` operators ([Compound assignment](expressions.md#compound-assignment)).</span></span> <span data-ttu-id="16033-148">I delegati possono essere confrontati per verificarne[l'uguaglianza.](expressions.md#delegate-equality-operators)</span><span class="sxs-lookup"><span data-stu-id="16033-148">Delegates can be compared for equality ([Delegate equality operators](expressions.md#delegate-equality-operators)).</span></span>

<span data-ttu-id="16033-149">Nell'esempio seguente viene illustrata la creazione di un'istanza di un numero di delegati e gli elenchi chiamate corrispondenti:</span><span class="sxs-lookup"><span data-stu-id="16033-149">The following example shows the instantiation of a number of delegates, and their corresponding invocation lists:</span></span>

```csharp
delegate void D(int x);

class C
{
    public static void M1(int i) {...}
    public static void M2(int i) {...}

}

class Test
{
    static void Main() {
        D cd1 = new D(C.M1);      // M1
        D cd2 = new D(C.M2);      // M2
        D cd3 = cd1 + cd2;        // M1 + M2
        D cd4 = cd3 + cd1;        // M1 + M2 + M1
        D cd5 = cd4 + cd3;        // M1 + M2 + M1 + M1 + M2
    }

}
```

<span data-ttu-id="16033-150">Quando vengono create istanze di `cd1` e `cd2`, ognuna incapsula un metodo.</span><span class="sxs-lookup"><span data-stu-id="16033-150">When `cd1` and `cd2` are instantiated, they each encapsulate one method.</span></span> <span data-ttu-id="16033-151">Quando viene creata un'istanza di `cd3`, dispone di un elenco chiamate di due metodi, `M1` e `M2`, in questo ordine.</span><span class="sxs-lookup"><span data-stu-id="16033-151">When `cd3` is instantiated, it has an invocation list of two methods, `M1` and `M2`, in that order.</span></span> <span data-ttu-id="16033-152">l'elenco chiamate di `cd4` contiene `M1`, `M2` e `M1`, in questo ordine.</span><span class="sxs-lookup"><span data-stu-id="16033-152">`cd4`'s invocation list contains `M1`, `M2`, and `M1`, in that order.</span></span> <span data-ttu-id="16033-153">Infine, l'elenco chiamate di `cd5` contiene `M1`, `M2`, `M1`, `M1` e `M2`, in questo ordine.</span><span class="sxs-lookup"><span data-stu-id="16033-153">Finally, `cd5`'s invocation list contains `M1`, `M2`, `M1`, `M1`, and `M2`, in that order.</span></span> <span data-ttu-id="16033-154">Per altri esempi di combinazione e rimozione di delegati, vedere [chiamata al delegato](delegates.md#delegate-invocation).</span><span class="sxs-lookup"><span data-stu-id="16033-154">For more examples of combining (as well as removing) delegates, see [Delegate invocation](delegates.md#delegate-invocation).</span></span>

## <a name="delegate-compatibility"></a><span data-ttu-id="16033-155">Compatibilità con i delegati</span><span class="sxs-lookup"><span data-stu-id="16033-155">Delegate compatibility</span></span>

<span data-ttu-id="16033-156">Un metodo o un delegato `M` è ***compatibile*** con un tipo delegato `D` Se si verificano tutte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="16033-156">A method or delegate `M` is ***compatible*** with a delegate type `D` if all of the following are true:</span></span>

*  <span data-ttu-id="16033-157">`D` e `M` hanno lo stesso numero di parametri e ogni parametro in `D` ha gli stessi modificatori `ref` o `out` come parametro corrispondente in `M`.</span><span class="sxs-lookup"><span data-stu-id="16033-157">`D` and `M` have the same number of parameters, and each parameter in `D` has the same `ref` or `out` modifiers as the corresponding parameter in `M`.</span></span>
*  <span data-ttu-id="16033-158">Per ogni parametro di valore (un parametro senza modificatore `ref` o `out`), esiste una conversione di identità ([conversione di identità](conversions.md#identity-conversion)) o conversione di un riferimento implicito ([conversioni di riferimenti impliciti](conversions.md#implicit-reference-conversions)) dal tipo di parametro in `D` al tipo di parametro corrispondente in `M`.</span><span class="sxs-lookup"><span data-stu-id="16033-158">For each value parameter (a parameter with no `ref` or `out` modifier), an identity conversion ([Identity conversion](conversions.md#identity-conversion)) or implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) exists from the parameter type in `D` to the corresponding parameter type in `M`.</span></span>
*  <span data-ttu-id="16033-159">Per ogni parametro `ref` o `out`, il tipo di parametro in `D` corrisponde al tipo di parametro in `M`.</span><span class="sxs-lookup"><span data-stu-id="16033-159">For each `ref` or `out` parameter, the parameter type in `D` is the same as the parameter type in `M`.</span></span>
*  <span data-ttu-id="16033-160">Una conversione di identità o di riferimento implicita esiste dal tipo restituito di `M` al tipo restituito di `D`.</span><span class="sxs-lookup"><span data-stu-id="16033-160">An identity or implicit reference conversion exists from the return type of `M` to the return type of `D`.</span></span>

## <a name="delegate-instantiation"></a><span data-ttu-id="16033-161">Creazione di istanze di delegati</span><span class="sxs-lookup"><span data-stu-id="16033-161">Delegate instantiation</span></span>

<span data-ttu-id="16033-162">Un'istanza di un delegato viene creata da un *delegate_creation_expression* ([espressioni di creazione di delegati](expressions.md#delegate-creation-expressions)) o una conversione in un tipo delegato.</span><span class="sxs-lookup"><span data-stu-id="16033-162">An instance of a delegate is created by a *delegate_creation_expression* ([Delegate creation expressions](expressions.md#delegate-creation-expressions)) or a conversion to a delegate type.</span></span> <span data-ttu-id="16033-163">L'istanza del delegato appena creata fa quindi riferimento a uno dei seguenti valori:</span><span class="sxs-lookup"><span data-stu-id="16033-163">The newly created delegate instance then refers to either:</span></span>

*  <span data-ttu-id="16033-164">Metodo statico a cui si fa riferimento in *delegate_creation_expression*o</span><span class="sxs-lookup"><span data-stu-id="16033-164">The static method referenced in the *delegate_creation_expression*, or</span></span>
*  <span data-ttu-id="16033-165">L'oggetto di destinazione (che non può essere `null`) e il metodo di istanza a cui si fa riferimento in *delegate_creation_expression*o</span><span class="sxs-lookup"><span data-stu-id="16033-165">The target object (which cannot be `null`) and instance method referenced in the *delegate_creation_expression*, or</span></span>
*  <span data-ttu-id="16033-166">Un altro delegato.</span><span class="sxs-lookup"><span data-stu-id="16033-166">Another delegate.</span></span>

<span data-ttu-id="16033-167">Esempio:</span><span class="sxs-lookup"><span data-stu-id="16033-167">For example:</span></span>

```csharp
delegate void D(int x);

class C
{
    public static void M1(int i) {...}
    public void M2(int i) {...}
}

class Test
{
    static void Main() { 
        D cd1 = new D(C.M1);        // static method
        C t = new C();
        D cd2 = new D(t.M2);        // instance method
        D cd3 = new D(cd2);        // another delegate
    }
}
```

<span data-ttu-id="16033-168">Una volta creata un'istanza, le istanze delegate fanno sempre riferimento allo stesso oggetto e metodo di destinazione.</span><span class="sxs-lookup"><span data-stu-id="16033-168">Once instantiated, delegate instances always refer to the same target object and method.</span></span> <span data-ttu-id="16033-169">Tenere presente che quando due delegati vengono combinati o uno viene rimosso da un altro, un nuovo delegato restituisce il proprio elenco chiamate; gli elenchi chiamate dei delegati combinati o rimossi restano invariati.</span><span class="sxs-lookup"><span data-stu-id="16033-169">Remember, when two delegates are combined, or one is removed from another, a new delegate results with its own invocation list; the invocation lists of the delegates combined or removed remain unchanged.</span></span>

## <a name="delegate-invocation"></a><span data-ttu-id="16033-170">Chiamata al delegato</span><span class="sxs-lookup"><span data-stu-id="16033-170">Delegate invocation</span></span>

<span data-ttu-id="16033-171">C#fornisce una sintassi speciale per richiamare un delegato.</span><span class="sxs-lookup"><span data-stu-id="16033-171">C# provides special syntax for invoking a delegate.</span></span> <span data-ttu-id="16033-172">Quando un'istanza di delegato non null il cui elenco chiamate contiene una voce viene richiamata, richiama il metodo con gli stessi argomenti che è stato fornito e restituisce lo stesso valore del metodo a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="16033-172">When a non-null delegate instance whose invocation list contains one entry is invoked, it invokes the one method with the same arguments it was given, and returns the same value as the referred to method.</span></span> <span data-ttu-id="16033-173">Per informazioni dettagliate sulla chiamata al delegato, vedere [chiamate di delegati](expressions.md#delegate-invocations) . Se si verifica un'eccezione durante la chiamata di un delegato di questo tipo e tale eccezione non viene rilevata all'interno del metodo richiamato, la ricerca di una clausola catch di eccezione continua nel metodo che ha chiamato il delegato, come se il metodo avesse chiamato direttamente il metodo Metodo a cui fa riferimento il delegato.</span><span class="sxs-lookup"><span data-stu-id="16033-173">(See [Delegate invocations](expressions.md#delegate-invocations) for detailed information on delegate invocation.) If an exception occurs during the invocation of such a delegate, and that exception is not caught within the method that was invoked, the search for an exception catch clause continues in the method that called the delegate, as if that method had directly called the method to which that delegate referred.</span></span>

<span data-ttu-id="16033-174">La chiamata di un'istanza del delegato il cui elenco chiamate contiene più voci continua richiamando ognuno dei metodi nell'elenco chiamate, in modo sincrono, nell'ordine.</span><span class="sxs-lookup"><span data-stu-id="16033-174">Invocation of a delegate instance whose invocation list contains multiple entries proceeds by invoking each of the methods in the invocation list, synchronously, in order.</span></span> <span data-ttu-id="16033-175">A ogni metodo chiamato viene passato lo stesso set di argomenti fornito all'istanza del delegato.</span><span class="sxs-lookup"><span data-stu-id="16033-175">Each method so called is passed the same set of arguments as was given to the delegate instance.</span></span> <span data-ttu-id="16033-176">Se tale chiamata al delegato include parametri di riferimento ([parametri di riferimento](classes.md#reference-parameters)), ogni chiamata al metodo viene eseguita con un riferimento alla stessa variabile. le modifiche apportate a tale variabile da un metodo nell'elenco chiamate saranno visibili ai metodi successivi nell'elenco chiamate.</span><span class="sxs-lookup"><span data-stu-id="16033-176">If such a delegate invocation includes reference parameters ([Reference parameters](classes.md#reference-parameters)), each method invocation will occur with a reference to the same variable; changes to that variable by one method in the invocation list will be visible to methods further down the invocation list.</span></span> <span data-ttu-id="16033-177">Se la chiamata al delegato include parametri di output o un valore restituito, il relativo valore finale proverrà dalla chiamata dell'ultimo delegato nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="16033-177">If the delegate invocation includes output parameters or a return value, their final value will come from the invocation of the last delegate in the list.</span></span>

<span data-ttu-id="16033-178">Se si verifica un'eccezione durante l'elaborazione della chiamata di un delegato di questo tipo e tale eccezione non viene rilevata all'interno del metodo richiamato, la ricerca di una clausola catch di eccezione continua nel metodo che ha chiamato il delegato ed eventuali metodi successivi l'elenco chiamate non viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="16033-178">If an exception occurs during processing of the invocation of such a delegate, and that exception is not caught within the method that was invoked, the search for an exception catch clause continues in the method that called the delegate, and any methods further down the invocation list are not invoked.</span></span>

<span data-ttu-id="16033-179">Se si tenta di richiamare un'istanza del delegato il cui valore è null, viene generata un'eccezione di tipo `System.NullReferenceException`.</span><span class="sxs-lookup"><span data-stu-id="16033-179">Attempting to invoke a delegate instance whose value is null results in an exception of type `System.NullReferenceException`.</span></span>

<span data-ttu-id="16033-180">Nell'esempio seguente viene illustrato come creare un'istanza, combinare, rimuovere e richiamare delegati:</span><span class="sxs-lookup"><span data-stu-id="16033-180">The following example shows how to instantiate, combine, remove, and invoke delegates:</span></span>

```csharp
using System;

delegate void D(int x);

class C
{
    public static void M1(int i) {
        Console.WriteLine("C.M1: " + i);
    }

    public static void M2(int i) {
        Console.WriteLine("C.M2: " + i);
    }

    public void M3(int i) {
        Console.WriteLine("C.M3: " + i);
    }
}

class Test
{
    static void Main() { 
        D cd1 = new D(C.M1);
        cd1(-1);                // call M1

        D cd2 = new D(C.M2);
        cd2(-2);                // call M2

        D cd3 = cd1 + cd2;
        cd3(10);                // call M1 then M2

        cd3 += cd1;
        cd3(20);                // call M1, M2, then M1

        C c = new C();
        D cd4 = new D(c.M3);
        cd3 += cd4;
        cd3(30);                // call M1, M2, M1, then M3

        cd3 -= cd1;             // remove last M1
        cd3(40);                // call M1, M2, then M3

        cd3 -= cd4;
        cd3(50);                // call M1 then M2

        cd3 -= cd2;
        cd3(60);                // call M1

        cd3 -= cd2;             // impossible removal is benign
        cd3(60);                // call M1

        cd3 -= cd1;             // invocation list is empty so cd3 is null

        cd3(70);                // System.NullReferenceException thrown

        cd3 -= cd1;             // impossible removal is benign
    }
}
```

<span data-ttu-id="16033-181">Come illustrato nell'istruzione `cd3 += cd1;`, un delegato può essere presente più volte in un elenco chiamate.</span><span class="sxs-lookup"><span data-stu-id="16033-181">As shown in the statement `cd3 += cd1;`, a delegate can be present in an invocation list multiple times.</span></span> <span data-ttu-id="16033-182">In questo caso, viene semplicemente richiamato una volta per ogni occorrenza.</span><span class="sxs-lookup"><span data-stu-id="16033-182">In this case, it is simply invoked once per occurrence.</span></span> <span data-ttu-id="16033-183">In un elenco chiamate come questo, quando il delegato viene rimosso, l'ultima occorrenza nell'elenco chiamate è quella effettivamente rimossa.</span><span class="sxs-lookup"><span data-stu-id="16033-183">In an invocation list such as this, when that delegate is removed, the last occurrence in the invocation list is the one actually removed.</span></span>

<span data-ttu-id="16033-184">Immediatamente prima dell'esecuzione dell'istruzione finale, `cd3 -= cd1;`, il delegato `cd3` fa riferimento a un elenco chiamate vuoto.</span><span class="sxs-lookup"><span data-stu-id="16033-184">Immediately prior to the execution of the final statement, `cd3 -= cd1;`, the delegate `cd3` refers to an empty invocation list.</span></span> <span data-ttu-id="16033-185">Il tentativo di rimuovere un delegato da un elenco vuoto (o di rimuovere un delegato non esistente da un elenco non vuoto) non è un errore.</span><span class="sxs-lookup"><span data-stu-id="16033-185">Attempting to remove a delegate from an empty list (or to remove a non-existent delegate from a non-empty list) is not an error.</span></span>

<span data-ttu-id="16033-186">L'output prodotto è:</span><span class="sxs-lookup"><span data-stu-id="16033-186">The output produced is:</span></span>

```console
C.M1: -1
C.M2: -2
C.M1: 10
C.M2: 10
C.M1: 20
C.M2: 20
C.M1: 20
C.M1: 30
C.M2: 30
C.M1: 30
C.M3: 30
C.M1: 40
C.M2: 40
C.M3: 40
C.M1: 50
C.M2: 50
C.M1: 60
C.M1: 60
```
