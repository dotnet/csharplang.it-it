---
ms.openlocfilehash: 08c14d9ef2afe30580f456995066c141653ede92
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229649"
---
# <a name="delegates"></a><span data-ttu-id="9f8ef-101">Delegati</span><span class="sxs-lookup"><span data-stu-id="9f8ef-101">Delegates</span></span>

<span data-ttu-id="9f8ef-102">I delegati consentono scenari che gli altri linguaggi, ad esempio C++, la convenzione Pascal e Modula - siano risolti con i puntatori a funzione.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-102">Delegates enable scenarios that other languages—such as C++, Pascal, and Modula -- have addressed with function pointers.</span></span> <span data-ttu-id="9f8ef-103">A differenza dei puntatori a funzione di C++, tuttavia, i delegati sono completamente orientata agli oggetti e a differenza dei puntatori di C++ per le funzioni membro, i delegati incapsulare un'istanza dell'oggetto e un metodo.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-103">Unlike C++ function pointers, however, delegates are fully object oriented, and unlike C++ pointers to member functions, delegates encapsulate both an object instance and a method.</span></span>

<span data-ttu-id="9f8ef-104">Una dichiarazione di delegato definisce una classe che deriva dalla classe `System.Delegate`.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-104">A delegate declaration defines a class that is derived from the class `System.Delegate`.</span></span> <span data-ttu-id="9f8ef-105">Un'istanza del delegato incapsula un elenco di chiamate, ovvero un elenco di uno o più metodi, ognuno dei quali è detta entità richiamabile.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-105">A delegate instance encapsulates an invocation list, which is a list of one or more methods, each of which is referred to as a callable entity.</span></span> <span data-ttu-id="9f8ef-106">Ad esempio metodi, entità richiamabile costituito da un'istanza e un metodo su quell'istanza.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-106">For instance methods, a callable entity consists of an instance and a method on that instance.</span></span> <span data-ttu-id="9f8ef-107">Per i metodi statici, entità richiamabile è costituito semplicemente da un metodo.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-107">For static methods, a callable entity consists of just a method.</span></span> <span data-ttu-id="9f8ef-108">Richiamo di un'istanza del delegato con un set appropriato di argomenti, ognuna delle entità del delegato da richiamare con il set specificato di argomenti.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-108">Invoking a delegate instance with an appropriate set of arguments causes each of the delegate's callable entities to be invoked with the given set of arguments.</span></span>

<span data-ttu-id="9f8ef-109">Una proprietà interessante e utile di un'istanza del delegato è che non conoscono o interessano le classi dei metodi che incapsula; Ciò che conta è che questi metodi devono essere compatibili ([dichiarazioni Delegate](delegates.md#delegate-declarations)) con il tipo del delegato.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-109">An interesting and useful property of a delegate instance is that it does not know or care about the classes of the methods it encapsulates; all that matters is that those methods be compatible ([Delegate declarations](delegates.md#delegate-declarations)) with the delegate's type.</span></span> <span data-ttu-id="9f8ef-110">In questo modo i delegati ideali per la chiamata "anonymous".</span><span class="sxs-lookup"><span data-stu-id="9f8ef-110">This makes delegates perfectly suited for "anonymous" invocation.</span></span>

## <a name="delegate-declarations"></a><span data-ttu-id="9f8ef-111">Dichiarazioni delegate</span><span class="sxs-lookup"><span data-stu-id="9f8ef-111">Delegate declarations</span></span>

<span data-ttu-id="9f8ef-112">Oggetto *delegate_declaration* è un *type_declaration* ([dichiarazioni di tipo](namespaces.md#type-declarations)) che dichiara un nuovo tipo di delegato.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-112">A *delegate_declaration* is a *type_declaration* ([Type declarations](namespaces.md#type-declarations)) that declares a new delegate type.</span></span>

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

<span data-ttu-id="9f8ef-113">È un errore in fase di compilazione per lo stesso modificatore venga visualizzato più volte in una dichiarazione di delegato.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-113">It is a compile-time error for the same modifier to appear multiple times in a delegate declaration.</span></span>

<span data-ttu-id="9f8ef-114">Il `new` modificatore è consentito solo su delegati dichiarati all'interno di un altro tipo, nel qual caso specifica che un delegato nasconde un membro ereditato con lo stesso nome, come descritto in [il nuovo modificatore](classes.md#the-new-modifier).</span><span class="sxs-lookup"><span data-stu-id="9f8ef-114">The `new` modifier is only permitted on delegates declared within another type, in which case it specifies that such a delegate hides an inherited member by the same name, as described in [The new modifier](classes.md#the-new-modifier).</span></span>

<span data-ttu-id="9f8ef-115">Il `public`, `protected`, `internal`, e `private` modificatori determinano l'accessibilità del tipo delegato.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-115">The `public`, `protected`, `internal`, and `private` modifiers control the accessibility of the delegate type.</span></span> <span data-ttu-id="9f8ef-116">A seconda del contesto in cui si verifica nella dichiarazione del delegato, alcuni di questi modificatori potrebbe non essere consentito ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)).</span><span class="sxs-lookup"><span data-stu-id="9f8ef-116">Depending on the context in which the delegate declaration occurs, some of these modifiers may not be permitted ([Declared accessibility](basic-concepts.md#declared-accessibility)).</span></span>

<span data-ttu-id="9f8ef-117">Nome del tipo del delegato è *identificatore*.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-117">The delegate's type name is *identifier*.</span></span>

<span data-ttu-id="9f8ef-118">L'opzione facoltativa *formal_parameter_list* specifica i parametri del delegato, e *return_type* indica il tipo restituito del delegato.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-118">The optional *formal_parameter_list* specifies the parameters of the delegate, and *return_type* indicates the return type of the delegate.</span></span>

<span data-ttu-id="9f8ef-119">L'opzione facoltativa *variant_type_parameter_list* ([elenchi di parametri di tipo variante](interfaces.md#variant-type-parameter-lists)) specifica i parametri di tipo per il delegato.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-119">The optional *variant_type_parameter_list* ([Variant type parameter lists](interfaces.md#variant-type-parameter-lists)) specifies the type parameters to the delegate itself.</span></span>

<span data-ttu-id="9f8ef-120">Deve essere il tipo restituito di un tipo delegato `void`, o output-safe ([safety varianza](interfaces.md#variance-safety)).</span><span class="sxs-lookup"><span data-stu-id="9f8ef-120">The return type of a delegate type must be either `void`, or output-safe ([Variance safety](interfaces.md#variance-safety)).</span></span>

<span data-ttu-id="9f8ef-121">Tutti i tipi di parametri formali di un tipo delegato devono essere di input-safe.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-121">All the formal parameter types of a delegate type must be input-safe.</span></span> <span data-ttu-id="9f8ef-122">Inoltre, qualsiasi `out` o `ref` i tipi di parametro devono essere anche output-safe.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-122">Additionally, any `out` or `ref` parameter types must also be output-safe.</span></span> <span data-ttu-id="9f8ef-123">Si noti che anche `out` parametri devono essere di input-safe, a causa di una limitazione della piattaforma sottostante di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-123">Note that even `out` parameters are required to be input-safe, due to a limitation of the underlying execution platform.</span></span>

<span data-ttu-id="9f8ef-124">I tipi di delegati in c# sono il nome equivalenti, non è strutturalmente equivalente.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-124">Delegate types in C# are name equivalent, not structurally equivalent.</span></span> <span data-ttu-id="9f8ef-125">In particolare, due tipi di delegati diversi che hanno lo stesso parametro vengono elencate e restituire tipo sono considerati tipi delegati diversi.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-125">Specifically, two different delegate types that have the same parameter lists and return type are considered different delegate types.</span></span> <span data-ttu-id="9f8ef-126">Tuttavia, le istanze di due tipi di delegati strutturalmente equivalenti, ma distinti possono risultare uguali ([delegare gli operatori di uguaglianza](expressions.md#delegate-equality-operators)).</span><span class="sxs-lookup"><span data-stu-id="9f8ef-126">However, instances of two distinct but structurally equivalent delegate types may compare as equal ([Delegate equality operators](expressions.md#delegate-equality-operators)).</span></span>

<span data-ttu-id="9f8ef-127">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9f8ef-127">For example:</span></span>

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

<span data-ttu-id="9f8ef-128">I metodi `A.M1` e `B.M1 `compatibili con entrambi i tipi di delegati `D1` e `D2` , dal momento che hanno lo stesso restituisce il tipo e l'elenco parametri, tuttavia, questi tipi di delegati sono due tipi diversi, in modo che non siano intercambiabili.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-128">The methods `A.M1` and `B.M1 `are compatible with both the delegate types `D1` and `D2` , since they have the same return type and parameter list; however, these delegate types are two different types, so they are not interchangeable.</span></span> <span data-ttu-id="9f8ef-129">I metodi `B.M2`, `B.M3`, e `B.M4` non sono compatibili con i tipi delegato `D1` e `D2`, dal momento che presentano tipi restituiti diversi o elenchi di parametri.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-129">The methods `B.M2`, `B.M3`, and `B.M4` are incompatible with the delegate types `D1` and `D2`, since they have different return types or parameter lists.</span></span>

<span data-ttu-id="9f8ef-130">Come altre dichiarazioni di tipo generico, gli argomenti di tipo devono essere concessa a creare un tipo delegato costruito.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-130">Like other generic type declarations, type arguments must be given to create a constructed delegate type.</span></span> <span data-ttu-id="9f8ef-131">I tipi di parametro e tipo restituito di un tipo costruito delegato vengono creati tramite la sostituzione, per ogni parametro di tipo nella dichiarazione del delegato, l'argomento di tipo corrispondente del tipo delegato costruito.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-131">The parameter types and return type of a constructed delegate type are created by substituting, for each type parameter in the delegate declaration, the corresponding type argument of the constructed delegate type.</span></span> <span data-ttu-id="9f8ef-132">Il tipo di risultato restituito e i tipi di parametro vengono usati per determinare quali metodi sono compatibili con un tipo delegate costruiti.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-132">The resulting return type and parameter types are used in determining what methods are compatible with a constructed delegate type.</span></span> <span data-ttu-id="9f8ef-133">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9f8ef-133">For example:</span></span>

```csharp
delegate bool Predicate<T>(T value);

class X
{
    static bool F(int i) {...}
    static bool G(string s) {...}
}
```

<span data-ttu-id="9f8ef-134">Il metodo `X.F` è compatibile con il tipo di delegato `Predicate<int>` e il metodo `X.G` compatibile con il tipo di delegato `Predicate<string>` .</span><span class="sxs-lookup"><span data-stu-id="9f8ef-134">The method `X.F` is compatible with the delegate type `Predicate<int>` and the method `X.G` is compatible with the delegate type `Predicate<string>` .</span></span>

<span data-ttu-id="9f8ef-135">L'unico modo per dichiarare un tipo delegato è tramite un *delegate_declaration*.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-135">The only way to declare a delegate type is via a *delegate_declaration*.</span></span> <span data-ttu-id="9f8ef-136">Un tipo delegato è un tipo di classe che deriva da `System.Delegate`.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-136">A delegate type is a class type that is derived from `System.Delegate`.</span></span> <span data-ttu-id="9f8ef-137">I tipi delegati sono implicitamente `sealed`, pertanto non è possibile derivare un tipo da un tipo delegato.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-137">Delegate types are implicitly `sealed`, so it is not permissible to derive any type from a delegate type.</span></span> <span data-ttu-id="9f8ef-138">Non è inoltre possibile derivare un tipo di classe non delegato da `System.Delegate`.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-138">It is also not permissible to derive a non-delegate class type from `System.Delegate`.</span></span> <span data-ttu-id="9f8ef-139">Si noti che `System.Delegate` è non stesso tipo di un delegato, ovvero un tipo di classe da cui derivano tutti i tipi di delegato.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-139">Note that `System.Delegate` is not itself a delegate type; it is a class type from which all delegate types are derived.</span></span>

<span data-ttu-id="9f8ef-140">C# offre una sintassi speciale per delegato la creazione di istanze e la chiamata.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-140">C# provides special syntax for delegate instantiation and invocation.</span></span> <span data-ttu-id="9f8ef-141">Ad eccezione di creazione dell'istanza, qualsiasi operazione che può essere applicato a una classe o istanza della classe anche è applicabile a una classe delegata o un'istanza, rispettivamente.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-141">Except for instantiation, any operation that can be applied to a class or class instance can also be applied to a delegate class or instance, respectively.</span></span> <span data-ttu-id="9f8ef-142">In particolare, è possibile accedere ai membri del `System.Delegate` tipo tramite la sintassi di accesso membro normale.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-142">In particular, it is possible to access members of the `System.Delegate` type via the usual member access syntax.</span></span>

<span data-ttu-id="9f8ef-143">Il set di metodi incapsulate da un'istanza del delegato viene chiamato un elenco di chiamate.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-143">The set of methods encapsulated by a delegate instance is called an invocation list.</span></span> <span data-ttu-id="9f8ef-144">Quando viene creata un'istanza di delegato ([compatibilità dei delegati](delegates.md#delegate-compatibility)) da un singolo metodo, incapsula tale metodo e il relativo elenco chiamate contiene solo una voce.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-144">When a delegate instance is created ([Delegate compatibility](delegates.md#delegate-compatibility)) from a single method, it encapsulates that method, and its invocation list contains only one entry.</span></span> <span data-ttu-id="9f8ef-145">Tuttavia, quando vengono combinate due istanze dei delegati non null, nei relativi elenchi chiamate vengono concatenati, nell'ordine operando di sinistra quello destro, in modo da formare un nuovo elenco di chiamate, che contiene due o più voci.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-145">However, when two non-null delegate instances are combined, their invocation lists are concatenated -- in the order left operand then right operand -- to form a new invocation list, which contains two or more entries.</span></span>

<span data-ttu-id="9f8ef-146">Si combinano delegati usando il file binario `+` ([operatore di addizione](expressions.md#addition-operator)) e `+=` operatori ([assegnazione composta](expressions.md#compound-assignment)).</span><span class="sxs-lookup"><span data-stu-id="9f8ef-146">Delegates are combined using the binary `+` ([Addition operator](expressions.md#addition-operator)) and `+=` operators ([Compound assignment](expressions.md#compound-assignment)).</span></span> <span data-ttu-id="9f8ef-147">Un delegato può essere rimosso da una combinazione di delegati, utilizzando il file binario `-` ([operatore di sottrazione](expressions.md#subtraction-operator)) e `-=` operatori ([assegnazione composta](expressions.md#compound-assignment)).</span><span class="sxs-lookup"><span data-stu-id="9f8ef-147">A delegate can be removed from a combination of delegates, using the binary `-` ([Subtraction operator](expressions.md#subtraction-operator)) and `-=` operators ([Compound assignment](expressions.md#compound-assignment)).</span></span> <span data-ttu-id="9f8ef-148">I delegati possono essere confrontati per verificarne l'uguaglianza ([delegare gli operatori di uguaglianza](expressions.md#delegate-equality-operators)).</span><span class="sxs-lookup"><span data-stu-id="9f8ef-148">Delegates can be compared for equality ([Delegate equality operators](expressions.md#delegate-equality-operators)).</span></span>

<span data-ttu-id="9f8ef-149">L'esempio seguente illustra la creazione di istanze di un numero di delegati e relativo elenco chiamate:</span><span class="sxs-lookup"><span data-stu-id="9f8ef-149">The following example shows the instantiation of a number of delegates, and their corresponding invocation lists:</span></span>

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

<span data-ttu-id="9f8ef-150">Quando `cd1` e `cd2` vengono create istanze, ognuna di esse incapsula un metodo.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-150">When `cd1` and `cd2` are instantiated, they each encapsulate one method.</span></span> <span data-ttu-id="9f8ef-151">Quando `cd3` viene creata un'istanza, presenta un elenco di chiamate dei due metodi `M1` e `M2`, in questo ordine.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-151">When `cd3` is instantiated, it has an invocation list of two methods, `M1` and `M2`, in that order.</span></span> <span data-ttu-id="9f8ef-152">`cd4`dell'elenco chiamate contiene `M1`, `M2`, e `M1`, in questo ordine.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-152">`cd4`'s invocation list contains `M1`, `M2`, and `M1`, in that order.</span></span> <span data-ttu-id="9f8ef-153">Infine `cd5`dell'elenco chiamate contiene `M1`, `M2`, `M1`, `M1`, e `M2`, in questo ordine.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-153">Finally, `cd5`'s invocation list contains `M1`, `M2`, `M1`, `M1`, and `M2`, in that order.</span></span> <span data-ttu-id="9f8ef-154">Per altri esempi di combinazione di delegati (e di rimozione), vedere [chiamata a un delegato](delegates.md#delegate-invocation).</span><span class="sxs-lookup"><span data-stu-id="9f8ef-154">For more examples of combining (as well as removing) delegates, see [Delegate invocation](delegates.md#delegate-invocation).</span></span>

## <a name="delegate-compatibility"></a><span data-ttu-id="9f8ef-155">Compatibilità dei delegati</span><span class="sxs-lookup"><span data-stu-id="9f8ef-155">Delegate compatibility</span></span>

<span data-ttu-id="9f8ef-156">Un metodo o delegato `M` viene ***compatibili*** con un tipo delegato `D` se tutte le operazioni seguenti sono vere:</span><span class="sxs-lookup"><span data-stu-id="9f8ef-156">A method or delegate `M` is ***compatible*** with a delegate type `D` if all of the following are true:</span></span>

*  <span data-ttu-id="9f8ef-157">`D` e `M` hanno lo stesso numero di parametri e ogni parametro nel `D` ha lo stesso `ref` oppure `out` modificatori del parametro corrispondente nel `M`.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-157">`D` and `M` have the same number of parameters, and each parameter in `D` has the same `ref` or `out` modifiers as the corresponding parameter in `M`.</span></span>
*  <span data-ttu-id="9f8ef-158">Per ogni parametro di valore (un parametro senza alcun `ref` oppure `out` modificatore), una conversione di identità ([conversione di identità](conversions.md#identity-conversion)) o la conversione implicita del riferimento ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)) dal tipo di parametro in cui è presente `D` al tipo di parametro corrispondente nel `M`.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-158">For each value parameter (a parameter with no `ref` or `out` modifier), an identity conversion ([Identity conversion](conversions.md#identity-conversion)) or implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) exists from the parameter type in `D` to the corresponding parameter type in `M`.</span></span>
*  <span data-ttu-id="9f8ef-159">Per ognuno `ref` oppure `out` parametro, digitare il parametro `D` corrisponde al tipo di parametro in `M`.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-159">For each `ref` or `out` parameter, the parameter type in `D` is the same as the parameter type in `M`.</span></span>
*  <span data-ttu-id="9f8ef-160">Esiste un identità o una conversione implicita del riferimento dal tipo restituito di `M` per il tipo restituito di `D`.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-160">An identity or implicit reference conversion exists from the return type of `M` to the return type of `D`.</span></span>

## <a name="delegate-instantiation"></a><span data-ttu-id="9f8ef-161">Istanze di delegati</span><span class="sxs-lookup"><span data-stu-id="9f8ef-161">Delegate instantiation</span></span>

<span data-ttu-id="9f8ef-162">Viene creata un'istanza di un delegato da un *delegate_creation_expression* ([delegare le espressioni di creazione](expressions.md#delegate-creation-expressions)) o una conversione in un tipo delegato.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-162">An instance of a delegate is created by a *delegate_creation_expression* ([Delegate creation expressions](expressions.md#delegate-creation-expressions)) or a conversion to a delegate type.</span></span> <span data-ttu-id="9f8ef-163">L'istanza del delegato appena creato fa quindi riferimento a:</span><span class="sxs-lookup"><span data-stu-id="9f8ef-163">The newly created delegate instance then refers to either:</span></span>

*  <span data-ttu-id="9f8ef-164">Il metodo statico a cui fa riferimento il *delegate_creation_expression*, o</span><span class="sxs-lookup"><span data-stu-id="9f8ef-164">The static method referenced in the *delegate_creation_expression*, or</span></span>
*  <span data-ttu-id="9f8ef-165">L'oggetto di destinazione (non può essere presente `null`) e l'istanza di metodo a cui fa riferimento il *delegate_creation_expression*, o</span><span class="sxs-lookup"><span data-stu-id="9f8ef-165">The target object (which cannot be `null`) and instance method referenced in the *delegate_creation_expression*, or</span></span>
*  <span data-ttu-id="9f8ef-166">Un altro delegato.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-166">Another delegate.</span></span>

<span data-ttu-id="9f8ef-167">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9f8ef-167">For example:</span></span>

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

<span data-ttu-id="9f8ef-168">Una volta creata un'istanza, le istanze dei delegati sempre fare riferimento al stesso oggetto di destinazione e metodo.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-168">Once instantiated, delegate instances always refer to the same target object and method.</span></span> <span data-ttu-id="9f8ef-169">Tenere presente che, quando si combinano due delegati o uno viene rimosso da un altro, un nuovo delegato ha come risultato con il proprio elenco di chiamate. gli elenchi chiamate dei delegati combinati o rimosso rimangono invariati.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-169">Remember, when two delegates are combined, or one is removed from another, a new delegate results with its own invocation list; the invocation lists of the delegates combined or removed remain unchanged.</span></span>

## <a name="delegate-invocation"></a><span data-ttu-id="9f8ef-170">Chiamata del delegato</span><span class="sxs-lookup"><span data-stu-id="9f8ef-170">Delegate invocation</span></span>

<span data-ttu-id="9f8ef-171">C# offre una sintassi speciale per richiamare un delegato.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-171">C# provides special syntax for invoking a delegate.</span></span> <span data-ttu-id="9f8ef-172">Quando viene richiamata un'istanza del delegato non null il cui elenco chiamate contiene una voce, richiama il metodo con gli stessi argomenti è stato specificato e restituisce lo stesso valore di cui si fa riferimento al metodo.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-172">When a non-null delegate instance whose invocation list contains one entry is invoked, it invokes the one method with the same arguments it was given, and returns the same value as the referred to method.</span></span> <span data-ttu-id="9f8ef-173">(Vedere [delegare le chiamate](expressions.md#delegate-invocations) per informazioni dettagliate sulla chiamata del delegato.) Se si verifica un'eccezione durante la chiamata di tale delegato e tale eccezione non rilevata all'interno del metodo richiamato, la ricerca di una clausola catch delle eccezioni continua nel metodo che ha chiamato il delegato, come se tale metodo avesse chiamato direttamente il metodo a cui il delegato è noto.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-173">(See [Delegate invocations](expressions.md#delegate-invocations) for detailed information on delegate invocation.) If an exception occurs during the invocation of such a delegate, and that exception is not caught within the method that was invoked, the search for an exception catch clause continues in the method that called the delegate, as if that method had directly called the method to which that delegate referred.</span></span>

<span data-ttu-id="9f8ef-174">Chiamata di un'istanza del delegato il cui elenco chiamate contiene più voci procede da richiamare in modo sincrono, ciascuno dei metodi nell'elenco chiamate, in ordine.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-174">Invocation of a delegate instance whose invocation list contains multiple entries proceeds by invoking each of the methods in the invocation list, synchronously, in order.</span></span> <span data-ttu-id="9f8ef-175">Ogni metodo chiamato in tal modo viene passato lo stesso set di argomenti è stato assegnato all'istanza del delegato.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-175">Each method so called is passed the same set of arguments as was given to the delegate instance.</span></span> <span data-ttu-id="9f8ef-176">Se tale chiamata di delegato include i parametri per riferimento ([facciano riferimento ai parametri](classes.md#reference-parameters)), si verificherà ogni chiamata al metodo con un riferimento alla stessa variabile; le modifiche apportate alla variabile da un metodo nell'elenco chiamate verranno visibile nei metodi successivi verso il basso l'elenco chiamate.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-176">If such a delegate invocation includes reference parameters ([Reference parameters](classes.md#reference-parameters)), each method invocation will occur with a reference to the same variable; changes to that variable by one method in the invocation list will be visible to methods further down the invocation list.</span></span> <span data-ttu-id="9f8ef-177">Se la chiamata di delegato include parametri di output o valore restituito, il valore finale dipenderà dalla chiamata del delegato ultimo nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-177">If the delegate invocation includes output parameters or a return value, their final value will come from the invocation of the last delegate in the list.</span></span>

<span data-ttu-id="9f8ef-178">Se si verifica un'eccezione durante l'elaborazione della chiamata di tale delegato e tale eccezione non rilevata all'interno del metodo che è stato richiamato, continua la ricerca di una clausola catch delle eccezioni nel metodo che ha chiamato il delegato e i metodi più avanti elenco di chiamate non vengono richiamati.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-178">If an exception occurs during processing of the invocation of such a delegate, and that exception is not caught within the method that was invoked, the search for an exception catch clause continues in the method that called the delegate, and any methods further down the invocation list are not invoked.</span></span>

<span data-ttu-id="9f8ef-179">Tentativo di richiamare un'istanza di delegato il cui valore è null genera un'eccezione di tipo `System.NullReferenceException`.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-179">Attempting to invoke a delegate instance whose value is null results in an exception of type `System.NullReferenceException`.</span></span>

<span data-ttu-id="9f8ef-180">Nell'esempio seguente viene illustrato come creare un'istanza, combinare, rimuovere e richiamare delegati:</span><span class="sxs-lookup"><span data-stu-id="9f8ef-180">The following example shows how to instantiate, combine, remove, and invoke delegates:</span></span>

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

<span data-ttu-id="9f8ef-181">Come illustrato nell'istruzione `cd3 += cd1;`, un delegato può essere presente in un elenco di chiamate più volte.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-181">As shown in the statement `cd3 += cd1;`, a delegate can be present in an invocation list multiple times.</span></span> <span data-ttu-id="9f8ef-182">In questo caso, verrà richiamato solo una volta per ogni occorrenza.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-182">In this case, it is simply invoked once per occurrence.</span></span> <span data-ttu-id="9f8ef-183">In un elenco di chiamate come questo, quando viene rimosso il delegato, l'ultima occorrenza nell'elenco chiamate è quello effettivamente rimosso.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-183">In an invocation list such as this, when that delegate is removed, the last occurrence in the invocation list is the one actually removed.</span></span>

<span data-ttu-id="9f8ef-184">Immediatamente prima dell'esecuzione dell'istruzione finale `cd3 -= cd1;`, il delegato `cd3` fa riferimento a un elenco di chiamate vuote.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-184">Immediately prior to the execution of the final statement, `cd3 -= cd1;`, the delegate `cd3` refers to an empty invocation list.</span></span> <span data-ttu-id="9f8ef-185">Il tentativo di rimuovere un delegato da un elenco vuoto (o per rimuovere un delegato non esistente da un elenco non vuoto) non è un errore.</span><span class="sxs-lookup"><span data-stu-id="9f8ef-185">Attempting to remove a delegate from an empty list (or to remove a non-existent delegate from a non-empty list) is not an error.</span></span>

<span data-ttu-id="9f8ef-186">L'output generato è:</span><span class="sxs-lookup"><span data-stu-id="9f8ef-186">The output produced is:</span></span>

```
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
