---
ms.openlocfilehash: ba36f1c95ee399c90885c351edc48b079c3bf860
ms.sourcegitcommit: 8a5955168e85fec63b404189cbbe8ca79c4fb34c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102470119"
---
# <a name="required-members"></a><span data-ttu-id="194bf-101">Membri obbligatori</span><span class="sxs-lookup"><span data-stu-id="194bf-101">Required Members</span></span>

## <a name="summary"></a><span data-ttu-id="194bf-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="194bf-102">Summary</span></span>

<span data-ttu-id="194bf-103">Questa proposta aggiunge un modo per specificare che una proprietà o un campo deve essere impostato durante l'inizializzazione dell'oggetto, forzando l'autore dell'istanza a fornire un valore iniziale per il membro in un inizializzatore di oggetto nel sito di creazione.</span><span class="sxs-lookup"><span data-stu-id="194bf-103">This proposal adds a way of specifying that a property or field is required to be set during object initialization, forcing the instance creator to provide an initial value for the member in an object initializer at the creation site.</span></span>

## <a name="motivation"></a><span data-ttu-id="194bf-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="194bf-104">Motivation</span></span>

<span data-ttu-id="194bf-105">Le gerarchie di oggetti oggi richiedono una grande quantità di standard per il trasporto di dati in tutti i livelli della gerarchia.</span><span class="sxs-lookup"><span data-stu-id="194bf-105">Object hierarchies today require a lot of boilerplate to carry data across all levels of the hierarchy.</span></span> <span data-ttu-id="194bf-106">Verrà ora esaminata una semplice gerarchia che interessa un `Person` come potrebbe essere definita in C# 8:</span><span class="sxs-lookup"><span data-stu-id="194bf-106">Let's look at a simple hierarchy involving a `Person` as might be defined in C# 8:</span></span>

```cs
class Person
{
    public string FirstName { get; }
    public string MiddleName { get; }
    public string LastName { get; }

    public Person(string firstName, string lastName, string? middleName = null)
    {
        FirstName = firstName;
        LastName = lastName;
        MiddleName = middleName ?? string.Empty;
    }
}

class Student : Person
{
    public int ID { get; }
    public Person(int id, string firstName, string lastName, string? middleName = null)
        : base(firstName, lastName, middleName)
    {
        ID = id;
    }
}
```

<span data-ttu-id="194bf-107">In questo articolo è disponibile un numero elevato di ripetizioni:</span><span class="sxs-lookup"><span data-stu-id="194bf-107">There's lots of repetition going on here:</span></span>

1. <span data-ttu-id="194bf-108">Alla radice della gerarchia, il tipo di ogni proprietà deve essere ripetuto due volte e il nome deve essere ripetuto quattro volte.</span><span class="sxs-lookup"><span data-stu-id="194bf-108">At the root of the hierarchy, the type of each property had to be repeated twice, and the name had to be repeated four times.</span></span>
2. <span data-ttu-id="194bf-109">Al livello derivato, il tipo di ogni proprietà ereditata deve essere ripetuto una volta e il nome deve essere ripetuto due volte.</span><span class="sxs-lookup"><span data-stu-id="194bf-109">At the derived level, the type of each inherited property had to be repeated once, and the name had to be repeated twice.</span></span>

<span data-ttu-id="194bf-110">Si tratta di una gerarchia semplice con 3 proprietà e 1 livello di ereditarietà, ma molti esempi reali di questi tipi di gerarchie passano a molti livelli, accumulando un numero maggiore e maggiore di proprietà da passare durante l'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="194bf-110">This is a simple hierarchy with 3 properties and 1 level of inheritance, but many real-world examples of these types of hierarchies go many levels deeper, accumulating larger and larger numbers of properties to pass along as they do so.</span></span> <span data-ttu-id="194bf-111">Roslyn è uno di questi codebase, ad esempio, nei vari tipi di albero che rendono CSTs e AST.</span><span class="sxs-lookup"><span data-stu-id="194bf-111">Roslyn is one such codebase, for example, in the various tree types that make our CSTs and ASTs.</span></span> <span data-ttu-id="194bf-112">Questo annidamento è abbastanza noioso da consentire ai generatori di codice di generare i costruttori e le definizioni di questi tipi e molti clienti hanno approcci simili al problema.</span><span class="sxs-lookup"><span data-stu-id="194bf-112">This nesting is tedious enough that we have code generators to generate the constructors and definitions of these types, and many customers take similar approaches to the problem.</span></span> <span data-ttu-id="194bf-113">In C# 9 sono stati introdotti i record, che per alcuni scenari può migliorare:</span><span class="sxs-lookup"><span data-stu-id="194bf-113">C# 9 introduces records, which for some scenarios can make this better:</span></span>

```cs
record Person(string FirstName, string LastName, string MiddleName = "");
record Student(int ID, string FirstName, string LastName, string MiddleName = "") : Person(FirstName, LastName, MiddleName);
```

<span data-ttu-id="194bf-114">`record`Elimina la prima origine di duplicazione, ma la seconda origine di duplicazione rimane invariata. Sfortunatamente, si tratta dell'origine della duplicazione che cresce man mano che la gerarchia cresce ed è la parte più dolorosa della duplicazione da correggere dopo aver apportato una modifica alla gerarchia, in quanto è necessaria per l'inseguimento della gerarchia attraverso tutti i relativi percorsi, possibilmente anche tra</span><span class="sxs-lookup"><span data-stu-id="194bf-114">`record`s eliminate the first source of duplication, but the second source of duplication remains unchanged: unfortunately, this is the source of duplication that grows as the hierarchy grows, and is the most painful part of the duplication to fix up after making a change in the hierarchy as it required chasing the hierarchy through all of its locations, possibly even across projects and potentially breaking consumers.</span></span>

<span data-ttu-id="194bf-115">Come soluzione alternativa per evitare questa duplicazione, sono stati rilevati i consumer che abbracciano gli inizializzatori di oggetto come un modo per evitare la scrittura di costruttori.</span><span class="sxs-lookup"><span data-stu-id="194bf-115">As a workaround to avoid this duplication, we have long seen consumers embracing object initializers as a way of avoiding writing constructors.</span></span> <span data-ttu-id="194bf-116">Prima di C# 9, tuttavia, questo aveva due svantaggi principali:</span><span class="sxs-lookup"><span data-stu-id="194bf-116">Prior to C# 9, however, this had 2 major downsides:</span></span>

1. <span data-ttu-id="194bf-117">La gerarchia di oggetti deve essere completamente modificabile, con `set` funzioni di accesso per ogni proprietà.</span><span class="sxs-lookup"><span data-stu-id="194bf-117">The object hierarchy has to be fully mutable, with `set` accessors on every property.</span></span>
2. <span data-ttu-id="194bf-118">Non esiste alcun modo per garantire che ogni istante di un oggetto del grafico imposti ogni membro.</span><span class="sxs-lookup"><span data-stu-id="194bf-118">There is no way to ensure that every instantation of an object from the graph sets every member.</span></span>

<span data-ttu-id="194bf-119">In C# 9 è stato risolto il primo problema, introducendo la `init` funzione di accesso, queste proprietà possono essere impostate per la creazione o l'inizializzazione di un oggetto, ma non successivamente.</span><span class="sxs-lookup"><span data-stu-id="194bf-119">C# 9 again addressed the first issue here, by introducing the `init` accessor: with it, these properties can be set on object creation/initialization, but not subsequently.</span></span> <span data-ttu-id="194bf-120">Tuttavia, abbiamo ancora il secondo problema: le proprietà in C# sono state facoltative a partire da C# 1,0.</span><span class="sxs-lookup"><span data-stu-id="194bf-120">However, we again still have the second issue: properties in C# have been optional since C# 1.0.</span></span> <span data-ttu-id="194bf-121">I tipi di riferimento Nullable, introdotti in C# 8,0, fanno parte di questo problema: se un costruttore non Inizializza una proprietà di tipo riferimento non nullable, viene visualizzato un avviso per l'utente.</span><span class="sxs-lookup"><span data-stu-id="194bf-121">Nullable reference types, introduced in C# 8.0, addressed part of this issue: if a constructor does not initialize a non-nullable reference-type property, then the user is warned about it.</span></span> <span data-ttu-id="194bf-122">Tuttavia, questo non risolve il problema: l'utente non vuole ripetere le parti di tipo grande nel costruttore, ma desidera passare la _richiesta_ di impostare le proprietà per gli utenti.</span><span class="sxs-lookup"><span data-stu-id="194bf-122">However, this doesn't solve the problem: the user here wants to not repeat large parts of their type in the constructor, they want to pass the _requirement_ to set properties on to their consumers.</span></span> <span data-ttu-id="194bf-123">Non fornisce inoltre avvisi relativi a `ID` da `Student` , poiché si tratta di un tipo di valore.</span><span class="sxs-lookup"><span data-stu-id="194bf-123">It also doesn't provide any warnings about `ID` from `Student`, as that is a value type.</span></span> <span data-ttu-id="194bf-124">Questi scenari sono estremamente comuni nel modello di database ORM, ad esempio EF Core, che necessitano di un costruttore pubblico senza parametri, ma che quindi guidano i valori Null delle righe in base al supporto di valori Null delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="194bf-124">These scenarios are extremely common in database model ORMs, such as EF Core, which need to have a public parameterless constructor but then drive nullability of the rows based on the nullability of the properties.</span></span>

<span data-ttu-id="194bf-125">Questa proposta cerca di risolvere questi problemi introducendo una nuova funzionalità per C#: membri obbligatori.</span><span class="sxs-lookup"><span data-stu-id="194bf-125">This proposal seeks to address these concerns by introducing a new feature to C#: required members.</span></span> <span data-ttu-id="194bf-126">I membri obbligatori dovranno essere inizializzati dai consumer, anziché dall'autore del tipo, con varie personalizzazioni per consentire la flessibilità per più costruttori e altri scenari.</span><span class="sxs-lookup"><span data-stu-id="194bf-126">Required members will be required to be initialized by consumers, rather than by the type author, with various customizations to allow flexibility for multiple constructors and other scenarios.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="194bf-127">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="194bf-127">Detailed Design</span></span>

<span data-ttu-id="194bf-128">`class``struct` `record` i tipi, e consentono di dichiarare un _\_ \_ elenco di membri obbligatorio_.</span><span class="sxs-lookup"><span data-stu-id="194bf-128">`class`, `struct`, and `record` types gain the ability to declare a _required\_member\_list_.</span></span> <span data-ttu-id="194bf-129">Questo elenco è l'elenco di tutte le proprietà e i campi di un tipo considerati _necessari_ e deve essere inizializzato durante la costruzione e l'inizializzazione di un'istanza del tipo.</span><span class="sxs-lookup"><span data-stu-id="194bf-129">This list is the list of all the properties and fields of a type that are considered _required_, and must be initialized during the construction and initialization of an instance of the type.</span></span> <span data-ttu-id="194bf-130">I tipi ereditano automaticamente questi elenchi dai rispettivi tipi di base, offrendo un'esperienza apparentemente non intuitiva che rimuove codice standard e ripetitivo.</span><span class="sxs-lookup"><span data-stu-id="194bf-130">Types inherit these lists from their base types automatically, providing a seemless experience that removes boilerplate and repetitive code.</span></span>

### <a name="required-modifier"></a><span data-ttu-id="194bf-131">`required` modificatore</span><span class="sxs-lookup"><span data-stu-id="194bf-131">`required` modifier</span></span>

<span data-ttu-id="194bf-132">Si aggiunge `'required'` all'elenco di modificatori nel modificatore di _campo \__ e nel _\_ modificatore della proprietà_.</span><span class="sxs-lookup"><span data-stu-id="194bf-132">We add `'required'` to the list of modifiers in _field\_modifier_ and _property\_modifier_.</span></span> <span data-ttu-id="194bf-133">L' _\_ \_ elenco di membri obbligatorio_ di un tipo è costituito da tutti i membri a cui sono stati `required` applicati.</span><span class="sxs-lookup"><span data-stu-id="194bf-133">The _required\_member\_list_ of a type is composed of all the members that have had `required` applied to them.</span></span> <span data-ttu-id="194bf-134">Di conseguenza, il `Person` tipo di precedenti ora è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="194bf-134">Thus, the `Person` type from earlier now looks like this:</span></span>

```cs
public class Person
{
    // The default constructor requires that FirstName and LastName be set at construction time
    public required string FirstName { get; init; }
    public string MiddleName { get; init; } = "";
    public required string LastName { get; init; }
}
```

<span data-ttu-id="194bf-135">Tutti i costruttori in un tipo che dispone di _un \_ \_ elenco di membri obbligatorio_ annuncia automaticamente un _contratto_ che i consumer del tipo devono inizializzare tutte le proprietà nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="194bf-135">All constructors on a type that has a _required\_member\_list_ automatically advertise a _contract_ that consumers of the type must initialize all of the properties in the list.</span></span> <span data-ttu-id="194bf-136">È un errore per un costruttore annunciare un contratto che richiede un membro che non è almeno accessibile del costruttore.</span><span class="sxs-lookup"><span data-stu-id="194bf-136">It is an error for a constructor to advertise a contract that requires a member that is not at least as accessible as the constructor itself.</span></span> <span data-ttu-id="194bf-137">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="194bf-137">For example:</span></span>

```cs
public class C
{
    public required int Prop { get; protected init; }

    // Advertises that Prop is required. This is fine, because the constructor is just as accessible as the property initer.
    protected C() {}

    // Error: ctor C(object) is more accessible than required property Prop.init.
    public C(object otherArg) {}
}
```

<span data-ttu-id="194bf-138">`required` è valido solo nei `class` `struct` tipi, e `record` .</span><span class="sxs-lookup"><span data-stu-id="194bf-138">`required` is only valid in `class`, `struct`, and `record` types.</span></span> <span data-ttu-id="194bf-139">Non è valido nei `interface` tipi.</span><span class="sxs-lookup"><span data-stu-id="194bf-139">It is not valid in `interface` types.</span></span>

### <a name="init-clauses"></a><span data-ttu-id="194bf-140">`init` Clausole</span><span class="sxs-lookup"><span data-stu-id="194bf-140">`init` Clauses</span></span>

<span data-ttu-id="194bf-141">Un costruttore può rimuovere un membro obbligatorio dal relativo contratto aggiungendo una `init` clausola che specifica il nome del membro da rimuovere.</span><span class="sxs-lookup"><span data-stu-id="194bf-141">A constructor can remove a required member from its contract by adding an `init` clause that specifies the name of the member to remove.</span></span> <span data-ttu-id="194bf-142">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="194bf-142">For example:</span></span>

```cs
public class C
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    // Advertises that just Prop1 is required.
    public C() : init(Prop2)
    {
        Prop2 = 2;
        Console.WriteLine($"Prop2 is {Prop2}")
    }
}
```

<span data-ttu-id="194bf-143">Una clausola INIT può inoltre fornire il valore di inizializzazione per la proprietà inline.</span><span class="sxs-lookup"><span data-stu-id="194bf-143">An init clause can also provide the initialization value for the property inline.</span></span> <span data-ttu-id="194bf-144">Queste assegnazioni vengono eseguite prima che venga eseguito il corpo del costruttore, dopo la chiamata di base se ne esiste una:</span><span class="sxs-lookup"><span data-stu-id="194bf-144">These assignments are run before the body of the constructor is executed, after the base call if one exists:</span></span>

```cs
public class C
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    // Sets Prop2 to 2 before the constructor body is run
    public C() : init(Prop2 = 2)
    {
        Console.WriteLine($"Prop2 is {Prop1}")
    }
}
```

<span data-ttu-id="194bf-145">Una clausola INIT può rimuovere tutti i requisiti usando l' `init required` abbreviazione:</span><span class="sxs-lookup"><span data-stu-id="194bf-145">An init clause can remove all requirements by using the `init required` shorthand:</span></span>

```cs
public class C
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    // Advertises that there are no requirements
    public C() : init required
    {
        Prop1 = 1;
        Prop2 = 2;
    }
}
```

<span data-ttu-id="194bf-146">_\_ \_ elenco di membri obbligatorio_ catena in una gerarchia di tipi.</span><span class="sxs-lookup"><span data-stu-id="194bf-146">_required\_member\_lists_ chain across a type hierarchy.</span></span> <span data-ttu-id="194bf-147">Il _contratto_ di un costruttore non include solo i membri obbligatori del tipo corrente, ma anche i membri obbligatori dal tipo di base e dalle interfacce che implementa.</span><span class="sxs-lookup"><span data-stu-id="194bf-147">A constructor's _contract_ not only includes the required members from the current type, but also the required members from the base type and any interfaces that it implements.</span></span> <span data-ttu-id="194bf-148">Se il costruttore derivato chiama un costruttore di base che rimuove alcuni di questi membri dall'elenco, il costruttore derivato rimuove anche tali membri dall'elenco.</span><span class="sxs-lookup"><span data-stu-id="194bf-148">If derived constructor calls a base constructor that removes some of those members from the list, the derived constructor also removes those members from the list.</span></span>

```cs
public class Base
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    public Base() : init(Prop1 = 1) {}
}

public class Derived
{
    public required int Prop3 { get; set; }
    public required int Prop4 { get; set; }

    // Only advertises that Prop2 and Prop3 are required, because `base()` removed Prop1 from the list, and the init clause removes Prop4
    public Derived : base() init(Prop4 = 4) { }
}
```

### <a name="initialization-requirement"></a><span data-ttu-id="194bf-149">Requisito di inizializzazione</span><span class="sxs-lookup"><span data-stu-id="194bf-149">Initialization Requirement</span></span>

<span data-ttu-id="194bf-150">I membri specificati in una clausola INIT devono essere assegnati definitivamente alla fine del corpo del costruttore.</span><span class="sxs-lookup"><span data-stu-id="194bf-150">Members specified in an init clause must be definitely assigned at the end of the constructor body.</span></span> <span data-ttu-id="194bf-151">In caso contrario, viene generato un errore.</span><span class="sxs-lookup"><span data-stu-id="194bf-151">If they are not, an error is produced.</span></span> <span data-ttu-id="194bf-152">Per supportare una logica di inizializzazione più complessa, questo errore può essere eliminato utilizzando l' `!` operatore:</span><span class="sxs-lookup"><span data-stu-id="194bf-152">To support more complicated initialization logic, this error can be suppressed using the `!` operator:</span></span>

```cs
public class C
{
    public required int Prop { get; set; }

    // Error: Prop is not definitely assigned at the end of the constructor body
    public C(int param) : init(Prop)
    {
        Initialize(param);
    }

    // No error: Prop! suppresses it
    public C() : init(Prop!)
        => Initialize(1);

    public void Initialize(int param) => Prop = param;
}
```

<span data-ttu-id="194bf-153">`!`È anche possibile applicare l'operatore a per evitare `init required` il controllo di tutte le proprietà obbligatorie per un tipo.</span><span class="sxs-lookup"><span data-stu-id="194bf-153">The `!` operator can also be applied to `init required`, to suppress the checking for all required properties on a type.</span></span>

```cs
public class C
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    // No errors: init required! suppresses
    public C() : init required! {}
}
```

#### <a name="grammar"></a><span data-ttu-id="194bf-154">Grammatica</span><span class="sxs-lookup"><span data-stu-id="194bf-154">Grammar</span></span>

<span data-ttu-id="194bf-155">La grammatica per un `constructor_initializer` viene modificata come segue:</span><span class="sxs-lookup"><span data-stu-id="194bf-155">The grammar for a `constructor_initializer` is modified as follows:</span></span>

```antlr
constructor_initializer
    : ':' constructor_chain
    | ':' init_clause
    | ':' constructor_chain init_clause
    ;

constructor_chain
    : 'base' '(' argument_list? ')'
    | 'this' '(' argument_list? ')'
    ;

init_clause
    : 'init' '(' init_argument_list ')'
    | 'init' 'required' '!'?
    ;

init_argument_list
    : init_argument (',' init_argument)*
    ;

init_argument
    : identifier init_argument_initializer?
    | identifier '!'
    ;

init_argument_initializer
    : '=' expression
    ;
```

### <a name="new-constraint"></a><span data-ttu-id="194bf-156">`new()` vincolo</span><span class="sxs-lookup"><span data-stu-id="194bf-156">`new()` constraint</span></span>

<span data-ttu-id="194bf-157">Un tipo con un costruttore senza parametri che annuncia un _contratto_ non può essere sostituito da un parametro di tipo vincolato a `new()` , perché non esiste alcun modo per la creazione di un'istanza generica per garantire che i requisiti siano soddisfatti.</span><span class="sxs-lookup"><span data-stu-id="194bf-157">A type with a parameterless constructor that advertises a _contract_ is not allowed to be substituted for a type parameter constrained to `new()`, as there is no way for the generic instantiation to ensure that the requirements are satisfied.</span></span>

## <a name="open-questions"></a><span data-ttu-id="194bf-158">Domande aperte</span><span class="sxs-lookup"><span data-stu-id="194bf-158">Open Questions</span></span>

### <a name="syntax-questions"></a><span data-ttu-id="194bf-159">Domande sulla sintassi</span><span class="sxs-lookup"><span data-stu-id="194bf-159">Syntax questions</span></span>

* <span data-ttu-id="194bf-160">Qual è `init` la parola giusta?</span><span class="sxs-lookup"><span data-stu-id="194bf-160">Is `init` the right word?</span></span> <span data-ttu-id="194bf-161">`init` un modificatore di suffisso nel costruttore potrebbe interferire se si vuole riutilizzarlo per le factory e abilitare anche `init` metodi con un modificatore di prefisso.</span><span class="sxs-lookup"><span data-stu-id="194bf-161">`init` as a postfix modifier on the constructor might interfere if we ever want to reuse it for factories and also enable `init` methods with a prefix modifier.</span></span> <span data-ttu-id="194bf-162">Altre possibilità:</span><span class="sxs-lookup"><span data-stu-id="194bf-162">Other possibilities:</span></span>
    * `set`
* <span data-ttu-id="194bf-163">`required`Il modificatore di destra per specificare che tutti i membri vengono inizializzati?</span><span class="sxs-lookup"><span data-stu-id="194bf-163">Is `required` the right modifier for specifying that all members are initialized?</span></span> <span data-ttu-id="194bf-164">Altri suggerimenti:</span><span class="sxs-lookup"><span data-stu-id="194bf-164">Others suggested:</span></span>
    * `default`
    * `all`
    * <span data-ttu-id="194bf-165">Con un!</span><span class="sxs-lookup"><span data-stu-id="194bf-165">With a !</span></span> <span data-ttu-id="194bf-166">per indicare la logica complessa</span><span class="sxs-lookup"><span data-stu-id="194bf-166">to indicate complex logic</span></span>
* <span data-ttu-id="194bf-167">È necessario un separatore tra `base` / `this` e `init` ?</span><span class="sxs-lookup"><span data-stu-id="194bf-167">Should we require a separator between the `base`/`this` and the `init`?</span></span>
    * <span data-ttu-id="194bf-168">`:` separatore</span><span class="sxs-lookup"><span data-stu-id="194bf-168">`:` separator</span></span>
    * <span data-ttu-id="194bf-169">separatore ','</span><span class="sxs-lookup"><span data-stu-id="194bf-169">',' separator</span></span>
* <span data-ttu-id="194bf-170">`required`Il modificatore è corretto?</span><span class="sxs-lookup"><span data-stu-id="194bf-170">Is `required` the right modifier?</span></span> <span data-ttu-id="194bf-171">Altre alternative suggerite:</span><span class="sxs-lookup"><span data-stu-id="194bf-171">Other alternatives that have been suggested:</span></span>
    * `req`
    * `require`
    * `mustinit`
    * `must`
    * `explicit`

### <a name="init-clause-restrictions"></a><span data-ttu-id="194bf-172">Limitazioni della clausola INIT</span><span class="sxs-lookup"><span data-stu-id="194bf-172">Init clause restrictions</span></span>

<span data-ttu-id="194bf-173">È necessario consentire l'accesso a `this` nella clausola INIT?</span><span class="sxs-lookup"><span data-stu-id="194bf-173">Should we allow access to `this` in the init clause?</span></span> <span data-ttu-id="194bf-174">Se si vuole che l'assegnazione in `init` sia una sintassi abbreviata per l'assegnazione del membro nel costruttore stesso, sembra che sia necessario.</span><span class="sxs-lookup"><span data-stu-id="194bf-174">If we want the assignment in `init` to be a shorthand for assigning the member in the constructor itself, it seems like we should.</span></span>

<span data-ttu-id="194bf-175">Inoltre, crea un nuovo ambito, `base()` ad esempio, o condivide lo stesso ambito del corpo del metodo?</span><span class="sxs-lookup"><span data-stu-id="194bf-175">Additionally, does it create a new scope, like `base()` does, or does it share the same scope as the method body?</span></span> <span data-ttu-id="194bf-176">Questa operazione è particolarmente importante per elementi come le funzioni locali, a cui la clausola INIT può accedere o per lo shadowing del nome, se un'espressione init introduce una variabile tramite il `out` parametro.</span><span class="sxs-lookup"><span data-stu-id="194bf-176">This is particularly important for things like local functions, which the init clause may want to access, or for name shadowing, if an init expression introduces a variable via `out` parameter.</span></span>

### <a name="base-requirement-chaining-representation-in-metadata"></a><span data-ttu-id="194bf-177">Rappresentazione concatenamento requisito base nei metadati</span><span class="sxs-lookup"><span data-stu-id="194bf-177">Base requirement chaining representation in metadata</span></span>

<span data-ttu-id="194bf-178">Per un'implementazione ideale della rappresentazione dei metadati, ogni costruttore contrassegna il costruttore di base che chiama in un certo modo. in questo modo, se i tipi base e derivati si trovano in assembly diversi, un aggiornamento della versione nell'assembly di base verrebbe riflesso accuratamente nell'utilizzo del tipo derivato senza dover aggiornare l'assembly derivato.</span><span class="sxs-lookup"><span data-stu-id="194bf-178">An ideal implementation of the metadata representation would have each constructor mark the base constructor that they call in some fashion, which would ensure that, if the base and derived types are in different assemblies, a version update in the base assembly would be accurately reflected in usage of the derived type without the derived assembly having to upgrade.</span></span> <span data-ttu-id="194bf-179">Tuttavia, non è possibile codificare un token di metodo in una firma, quindi è necessario trovare un'altra strategia di codifica.</span><span class="sxs-lookup"><span data-stu-id="194bf-179">However, we don't have a way to encode a method token into a signature, so we'd have to find some other encoding strategy.</span></span> <span data-ttu-id="194bf-180">Questa strategia sarà intrinsecamente fragile a diversi scenari potenziali, pertanto potrebbe essere più pragmatico ripetere semplicemente tutti i membri rimossi nell'elenco dei membri rimossi del costruttore derivato.</span><span class="sxs-lookup"><span data-stu-id="194bf-180">This strategy will be inherently fragile to a number of potential scenarios, so it may be more pragmatic to simply repeat any removed members in the removed member list of the derived constructor.</span></span>

### <a name="warning-vs-error"></a><span data-ttu-id="194bf-181">Errore di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="194bf-181">Warning vs Error</span></span>

<span data-ttu-id="194bf-182">Non impostare un membro necessario come un avviso o un errore?</span><span class="sxs-lookup"><span data-stu-id="194bf-182">Should not setting a required member be a warning or an error?</span></span> <span data-ttu-id="194bf-183">È certamente possibile ingannare il sistema, tramite `Activator.CreateInstance(typeof(C))` o similare, il che significa che potrebbe non essere possibile garantire completamente che tutte le proprietà siano sempre impostate.</span><span class="sxs-lookup"><span data-stu-id="194bf-183">It is certainly possible to trick the system, via `Activator.CreateInstance(typeof(C))` or similar, which means we may not be able to fully guarantee all properties are always set.</span></span> <span data-ttu-id="194bf-184">Viene anche consentita l'eliminazione della diagnostica nel sito del costruttore tramite `!` , che in genere non consente errori.</span><span class="sxs-lookup"><span data-stu-id="194bf-184">We also allow suppression of the diagnostics at the constructor-site by using the `!`, which we generally do not allow for errors.</span></span> <span data-ttu-id="194bf-185">Tuttavia, la funzionalità è analoga ai campi di sola lettura o alle proprietà init, in quanto si è riscontrato un errore hardware se gli utenti tentano di impostare tale membro dopo l'inizializzazione, ma possono essere elusi tramite reflection.</span><span class="sxs-lookup"><span data-stu-id="194bf-185">However, the feature is similar to readonly fields or init properties, in that we hard error if users attempt to set such a member after initialization, but they can be circumvented by reflection.</span></span>

### <a name="silly-diagnostics"></a><span data-ttu-id="194bf-186">Diagnostica "sciocco"</span><span class="sxs-lookup"><span data-stu-id="194bf-186">"Silly" diagnostics</span></span>

<span data-ttu-id="194bf-187">Dato questo codice:</span><span class="sxs-lookup"><span data-stu-id="194bf-187">Given this code:</span></span>

```cs
class C
{
    public required object? O;
    public C() : init(O = null) {}
}
```

<span data-ttu-id="194bf-188">Dovrebbe emettere un tipo di diagnostica `O` contrassegnato come obbligatorio, ma mai richiesto in un contratto?</span><span class="sxs-lookup"><span data-stu-id="194bf-188">Should issue some kind of diagnostic that `O` is marked required, but never required in a contract?</span></span> <span data-ttu-id="194bf-189">Gli sviluppatori possono trovare le proprietà contrassegnate come necessarie per essere utili come rete di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="194bf-189">Developers might find marking properties as required to be useful as a safety net.</span></span>

## <a name="discussed-questions"></a><span data-ttu-id="194bf-190">Domande discusse</span><span class="sxs-lookup"><span data-stu-id="194bf-190">Discussed Questions</span></span>

### <a name="level-of-enforcement-for-init-clauses"></a><span data-ttu-id="194bf-191">Livello di imposizione per le `init` clausole</span><span class="sxs-lookup"><span data-stu-id="194bf-191">Level of enforcement for `init` clauses</span></span>

<span data-ttu-id="194bf-192">Si impone rigorosamente che i membri specificati in una `init` clausola senza inizializzatore debbano inizializzare tutti i membri?</span><span class="sxs-lookup"><span data-stu-id="194bf-192">Do we strictly enforce that members specified in a `init` clause without an initializer must initialize all members?</span></span> <span data-ttu-id="194bf-193">È probabile che l'operazione venga eseguita. in caso contrario, viene creato un semplice guasto.</span><span class="sxs-lookup"><span data-stu-id="194bf-193">It seems likely that we do, otherwise we create an easy pit-of-failure.</span></span> <span data-ttu-id="194bf-194">Tuttavia, viene anche eseguito il rischio di introdurre nuovamente gli stessi problemi risolti con `MemberNotNull` in C# 9.</span><span class="sxs-lookup"><span data-stu-id="194bf-194">However, we also run the risk of reintroducing the same problems we solved with `MemberNotNull` in C# 9.</span></span> <span data-ttu-id="194bf-195">Se si vuole applicare questa impostazione, è probabile che un metodo helper indichi un metodo per indicare che è necessario impostare un membro.</span><span class="sxs-lookup"><span data-stu-id="194bf-195">If we want to strictly enforce this, we will likely need a way for a helper method to indicate that it sets a member.</span></span> <span data-ttu-id="194bf-196">Di seguito sono riportate alcune possibili sintassi illustrate in questo argomento:</span><span class="sxs-lookup"><span data-stu-id="194bf-196">Some possible syntaxes we've discussed for this:</span></span>

* <span data-ttu-id="194bf-197">`init`Metodi allow.</span><span class="sxs-lookup"><span data-stu-id="194bf-197">Allow `init` methods.</span></span> <span data-ttu-id="194bf-198">Questi metodi possono essere chiamati solo da un costruttore o da un altro `init` metodo e possono accedere `this` come se fosse nel costruttore (IE, set `readonly` e `init` Fields/Properties).</span><span class="sxs-lookup"><span data-stu-id="194bf-198">These methods are only allowed to be called from a constructor or from another `init` method, and can access `this` as if it's in the constructor (ie, set `readonly` and `init` fields/properties).</span></span> <span data-ttu-id="194bf-199">Questo può essere combinato con `init` clausole su tali metodi.</span><span class="sxs-lookup"><span data-stu-id="194bf-199">This can be combined with `init` clauses on such methods.</span></span> <span data-ttu-id="194bf-200">Una `init` clausola viene considerata soddisfatta se il membro nella clausola viene assegnato definitivamente nel corpo del metodo o del costruttore.</span><span class="sxs-lookup"><span data-stu-id="194bf-200">A `init` clause would be considered satisfied if the member in the clause is definitely assigned in the body of the method/constructor.</span></span> <span data-ttu-id="194bf-201">La chiamata a un metodo con una `init` clausola che include un membro viene conteggiata come assegnazione a tale membro.</span><span class="sxs-lookup"><span data-stu-id="194bf-201">Calling a method with a `init` clause that includes a member counts as assigning to that member.</span></span>
<span data-ttu-id="194bf-202">Se si decide che si tratta di una route che si vuole perseguire, ora o in futuro, è probabile che non si debba usare `init` come parola chiave per la clausola init in un costruttore, perché questo potrebbe creare confusione.</span><span class="sxs-lookup"><span data-stu-id="194bf-202">If we do decided that this is a route we want to pursue, now or in the future, it seems likely that we should not use `init` as the keyword for the init clause on a constructor, as that would be confusing.</span></span>
* <span data-ttu-id="194bf-203">Consente all' `!` operatore di disattivare in modo esplicito l'avviso o l'errore.</span><span class="sxs-lookup"><span data-stu-id="194bf-203">Allow the `!` operator to suppress the warning/error explicitly.</span></span> <span data-ttu-id="194bf-204">Se l'inizializzazione di un membro viene eseguita in modo complesso, ad esempio in un metodo condiviso, l'utente può aggiungere un oggetto `!` alla clausola INIT per indicare che il compilatore non deve verificare la presenza di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="194bf-204">If initializing a member in a complicated way (such as in a shared method), the user can add a `!` to the init clause to indicate the compiler should not check for initialization.</span></span>

<span data-ttu-id="194bf-205">Dopo la discussione, si vuole l'idea dell' `!` operatore.</span><span class="sxs-lookup"><span data-stu-id="194bf-205">After discussion we like the idea of the `!` operator.</span></span> <span data-ttu-id="194bf-206">Consente all'utente di essere intenzionale su scenari più complessi, ma anche di non creare un foro di progettazione di grandi dimensioni intorno ai metodi init e annotare ogni metodo come impostare i membri X o Y. `!` è stato scelto perché è già stato usato per la disattivazione degli avvisi nullable e viene usato per indicare al compilatore "sono più intelligente di te" in un'altra posizione è un'estensione naturale del formato di sintassi.</span><span class="sxs-lookup"><span data-stu-id="194bf-206">It allows the user to be intentional about more complicated scenarios while also not creating a large design hole around init methods and annotating every method as setting members X or Y. `!` was chosen because we already use it for suppressing nullable warnings, and using it to tell the compiler "I'm smarter than you" in another place is a natural extension of the syntax form.</span></span>

### <a name="required-interface-members"></a><span data-ttu-id="194bf-207">Membri di interfaccia necessari</span><span class="sxs-lookup"><span data-stu-id="194bf-207">Required interface members</span></span>

<span data-ttu-id="194bf-208">Questa proposta non consente alle interfacce di contrassegnare i membri come richiesto.</span><span class="sxs-lookup"><span data-stu-id="194bf-208">This proposal does not allow interfaces to mark members as required.</span></span> <span data-ttu-id="194bf-209">In questo modo è possibile evitare di dover individuare scenari complessi `new()` e vincoli di interfaccia nei generics in questo momento ed è direttamente correlato alle fabbriche e alla costruzione generica.</span><span class="sxs-lookup"><span data-stu-id="194bf-209">This protects us from having to figure out complex scenarios around `new()` and interface constraints in generics right now, and is directly related to both factories and generic construction.</span></span> <span data-ttu-id="194bf-210">Per assicurarsi di avere spazio di progettazione in quest'area, non è possibile usare le `required` interfacce e impedire ai tipi _con \_ \_ elenchi di membri richiesti_ di sostituire i parametri di tipo vincolati a `new()` .</span><span class="sxs-lookup"><span data-stu-id="194bf-210">In order to ensure that we have design space in this area, we forbid `required` in interfaces, and forbid types with _required\_member\_lists_ from being substituted for type parameters constrained to `new()`.</span></span> <span data-ttu-id="194bf-211">Quando si vuole esaminare in modo più ampio gli scenari di costruzione generici con le factory, è possibile rivedere questo problema.</span><span class="sxs-lookup"><span data-stu-id="194bf-211">When we want to take a broader look at generic construction scenarios with factories, we can revisit this issue.</span></span>
