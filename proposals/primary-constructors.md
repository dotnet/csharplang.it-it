---
ms.openlocfilehash: 77c9ffda3a59cc5f3dcc3ec9848600c6c5e03eed
ms.sourcegitcommit: 27487fa0294f4cdb7e5f2478884149e05314fd8a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "79484972"
---
# <a name="primary-constructors"></a><span data-ttu-id="7fcca-101">Costruttori primari</span><span class="sxs-lookup"><span data-stu-id="7fcca-101">Primary constructors</span></span>

* <span data-ttu-id="7fcca-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="7fcca-102">[x] Proposed</span></span>
* <span data-ttu-id="7fcca-103">[] Prototipo: non avviato</span><span class="sxs-lookup"><span data-stu-id="7fcca-103">[ ] Prototype: Not started</span></span>
* <span data-ttu-id="7fcca-104">[] Implementazione: non avviata</span><span class="sxs-lookup"><span data-stu-id="7fcca-104">[ ] Implementation: Not started</span></span>
* <span data-ttu-id="7fcca-105">[] Specifica: non avviata</span><span class="sxs-lookup"><span data-stu-id="7fcca-105">[ ] Specification: Not started</span></span>

## <a name="summary"></a><span data-ttu-id="7fcca-106">Summary</span><span class="sxs-lookup"><span data-stu-id="7fcca-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="7fcca-107">Le classi possono avere un elenco di parametri e, quando lo fanno, la relativa specifica della classe di base può avere un elenco di argomenti.</span><span class="sxs-lookup"><span data-stu-id="7fcca-107">Classes can have a parameter list, and when they do, their base class specification can have an argument list.</span></span>
<span data-ttu-id="7fcca-108">I parametri del costruttore primario rientrano nell'ambito della dichiarazione di classe e, se vengono acquisiti da un membro di funzione o da una funzione anonima, vengono archiviati come campi privati nella classe.</span><span class="sxs-lookup"><span data-stu-id="7fcca-108">Primary constructor parameters are in scope throughout the class declaration, and if they are captured by a function member or anonymous function, they are stored as private fields in the class.</span></span>

## <a name="motivation"></a><span data-ttu-id="7fcca-109">Motivazione</span><span class="sxs-lookup"><span data-stu-id="7fcca-109">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="7fcca-110">Nel codice di inizializzazione del programma è comune avere molto standard.</span><span class="sxs-lookup"><span data-stu-id="7fcca-110">It is common to have a lot of boilerplate in program initialization code.</span></span> <span data-ttu-id="7fcca-111">Nel caso generale, una determinata parte dei dati `x` è indicata più volte:</span><span class="sxs-lookup"><span data-stu-id="7fcca-111">In the general case, a given piece of data `x` is mentioned many times:</span></span>

- <span data-ttu-id="7fcca-112">Come campo privato `_x`</span><span class="sxs-lookup"><span data-stu-id="7fcca-112">As a private field `_x`</span></span>
- <span data-ttu-id="7fcca-113">Come parametro `x` a un costruttore</span><span class="sxs-lookup"><span data-stu-id="7fcca-113">As a parameter `x` to a constructor</span></span>
- <span data-ttu-id="7fcca-114">In un `_x = x;` di assegnazione del campo dal parametro nel costruttore</span><span class="sxs-lookup"><span data-stu-id="7fcca-114">In an assignment `_x = x;` of the field from the parameter in the constructor</span></span>
- <span data-ttu-id="7fcca-115">Come proprietà `X`</span><span class="sxs-lookup"><span data-stu-id="7fcca-115">As a property `X`</span></span>
- <span data-ttu-id="7fcca-116">Nel setter della proprietà `x = value;`</span><span class="sxs-lookup"><span data-stu-id="7fcca-116">In the property setter `x = value;`</span></span>
- <span data-ttu-id="7fcca-117">Nel getter della proprietà `return x;`</span><span class="sxs-lookup"><span data-stu-id="7fcca-117">In the property getter `return x;`</span></span>

``` c#
class C
{
    private string _x;
    
    public C(string x)
    {
        _x = x;
    }
    public string X
    {
        get => _x;
        set { if (value == null) throw new NullArgumentException(nameof(X)); _x = value; }
    }
}
```

<span data-ttu-id="7fcca-118">Per le proprietà che non richiedono la convalida o il calcolo, la noia può essere ridotta usando le proprietà automatiche, riducendo così la necessità di dichiarare un campo sottostante esplicito per la proprietà.</span><span class="sxs-lookup"><span data-stu-id="7fcca-118">For properties that don't require validation or computation, the tedium can be reduced using auto-properties, thus cutting out the need to declare an explicit backing field for the property.</span></span> <span data-ttu-id="7fcca-119">Tuttavia, se la proprietà richiede qualsiasi tipo di logica oltre a quella fornita da una proprietà automatica, il risultato è il migliore.</span><span class="sxs-lookup"><span data-stu-id="7fcca-119">But if your property requires any sort of logic beyond what an auto-property provides, the above is the best you an do.</span></span>

<span data-ttu-id="7fcca-120">I costruttori primari riducono invece il sovraccarico inserendo gli argomenti del costruttore direttamente nell'ambito della classe, evitando di nuovo la necessità di dichiarare in modo esplicito un campo sottostante.</span><span class="sxs-lookup"><span data-stu-id="7fcca-120">Primary constructors instead reduce the overhead by putting constructor arguments directly in scope throughout the class, again obviating the need to explicitly declare a backing field.</span></span> <span data-ttu-id="7fcca-121">Quindi, l'esempio precedente diventa:</span><span class="sxs-lookup"><span data-stu-id="7fcca-121">Thus, the above example would become:</span></span>

``` c#
class C(string x)
{
    public string X
    {
        get => x;
        set { if (value == null) throw new NullArgumentException(nameof(X)); x = value; }
    }
}
```

<span data-ttu-id="7fcca-122">In questo esempio, il costruttore primario riduce il numero di entità denominate per `x` da tre a due, evitando il `_x` campo sottostante.</span><span class="sxs-lookup"><span data-stu-id="7fcca-122">In this example, the primary constructor reduces the number of named entities for `x` from three to two, obviating the `_x` backing field.</span></span> <span data-ttu-id="7fcca-123">Rimuove due delle tre dichiarazioni di membro (mantenendo solo la dichiarazione di proprietà) e riduce il numero totale di menzioni di `x`/`_x`/`X` da 8 a cinque.</span><span class="sxs-lookup"><span data-stu-id="7fcca-123">It removes two out of three member declarations (keeping only the property declaration itself), and reduces the total number of mentions of `x`/`_x`/`X` from eight to five.</span></span>


## <a name="detailed-design"></a><span data-ttu-id="7fcca-124">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="7fcca-124">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="7fcca-125">Le classi possono avere un elenco di parametri:</span><span class="sxs-lookup"><span data-stu-id="7fcca-125">Classes can have a parameter list:</span></span>

``` c#
public class C(int i, string s)
{
    ...
}
```

<span data-ttu-id="7fcca-126">L'elenco di parametri comporta la dichiarazione implicita di un costruttore per la classe, con la stessa accessibilità della classe stessa.</span><span class="sxs-lookup"><span data-stu-id="7fcca-126">The parameter list causes a constructor to be implicitly declared for the class, with the same accessibility as the class itself.</span></span>

``` c#
new C(5, "Hello");
```

<span data-ttu-id="7fcca-127">I parametri del costruttore primario rientrano nell'ambito di tutto il corpo della classe.</span><span class="sxs-lookup"><span data-stu-id="7fcca-127">Primary constructor parameters are in scope throughout the class body.</span></span> <span data-ttu-id="7fcca-128">Se vengono acquisiti da un membro di funzione o da una funzione anonima, vengono archiviati come campi privati nella classe.</span><span class="sxs-lookup"><span data-stu-id="7fcca-128">If they are captured by a function member or anonymous function, they become stored as private fields in the class.</span></span> <span data-ttu-id="7fcca-129">Se vengono utilizzati solo durante l'inizializzazione, non verranno archiviati nell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="7fcca-129">If they are only used during initialization they will not be stored in the object.</span></span>

``` c#
public class C(int i, string s)
{
    int[] a = new int[i]; // i not captured
    public int S => s;    // s captured
}
```

<span data-ttu-id="7fcca-130">Se una classe con un costruttore primario ha una specifica della classe di base, può avere un elenco di argomenti.</span><span class="sxs-lookup"><span data-stu-id="7fcca-130">If a class with a primary constructor has a base class specification, that one can have an argument list.</span></span> <span data-ttu-id="7fcca-131">Funge da elenco di argomenti per un inizializzatore di `base(...)` del costruttore dichiarato in modo implicito.</span><span class="sxs-lookup"><span data-stu-id="7fcca-131">This serves as the argument list to a `base(...)` initializer of the implicitly declared constructor.</span></span> <span data-ttu-id="7fcca-132">Se non viene fornito alcun elenco di argomenti, viene utilizzato un oggetto vuoto.</span><span class="sxs-lookup"><span data-stu-id="7fcca-132">If no argument list is provided, an empty one is assumed.</span></span>

``` c#
public class C(int i, string s) : B(s)
{
    ...
}
```
<span data-ttu-id="7fcca-133">La classe può avere anche costruttori definiti in modo esplicito, ma tutti devono usare un inizializzatore `this(...)`.</span><span class="sxs-lookup"><span data-stu-id="7fcca-133">The class can have explicitly defined constructors as well, but those all have to use a `this(...)` initializer.</span></span> <span data-ttu-id="7fcca-134">In questo modo si garantisce che il costruttore primario venga sempre chiamato quando viene costruita una nuova istanza.</span><span class="sxs-lookup"><span data-stu-id="7fcca-134">This ensures that the primary constructor is always called when a new instance is constructed.</span></span>

<span data-ttu-id="7fcca-135">Tutti gli inizializzatori nel corpo della classe diventeranno assegnazioni nel costruttore generato.</span><span class="sxs-lookup"><span data-stu-id="7fcca-135">All initializers in the class body will become assignments in the generated constructor.</span></span> <span data-ttu-id="7fcca-136">Ciò significa che, a differenza di altre classi, gli inizializzatori vengono eseguiti *dopo* che è stato richiamato il costruttore di base, non prima.</span><span class="sxs-lookup"><span data-stu-id="7fcca-136">This means that, unlike other classes, initializers will run *after* the base constructor has been invoked, not before.</span></span> <span data-ttu-id="7fcca-137">Inoltre, la classe generata conterrà le assegnazioni per inizializzare tutti i campi privati generati per archiviare i parametri del costruttore primario acquisiti dai membri.</span><span class="sxs-lookup"><span data-stu-id="7fcca-137">In addition, the generated class will contain assignments to initialize any private fields that were generated to store primary constructor parameters that were captured by members.</span></span> <span data-ttu-id="7fcca-138">Questi membri vengono riscritti per utilizzare il campo privato anziché il parametro in modo analogo alle chiusure per le espressioni lambda.</span><span class="sxs-lookup"><span data-stu-id="7fcca-138">Those members are rewritten to use the private field instead of the parameter in a manner similar to closures for lambda expressions.</span></span> <span data-ttu-id="7fcca-139">I campi primari generati vengono inizializzati per primi, quindi le assegnazioni generate dall'inizializzatore vengono eseguite nell'ordine di visualizzazione nella classe.</span><span class="sxs-lookup"><span data-stu-id="7fcca-139">The generated primary fields are initialized first, and then the initializer-generated assignments are executed in the order of appearance in the class.</span></span>

<span data-ttu-id="7fcca-140">Per l'esempio precedente, l'effetto della dichiarazione di classe è come se fosse riscritto nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="7fcca-140">For the above example, the effect of the class declaration is as if rewritten like this:</span></span>

``` c#
public class C : B
{
    public C(int i, string s) : base(s)
    {
        __s = s;        // store parameter s for captured use
        a = new int[i]; // initialize a
    }
    int __s; // generated field for capture of s
    
    int[] a;
    public int S => __s; // s replaced with captured __s
}
```

<span data-ttu-id="7fcca-141">L'acquisizione presenta restrizioni simili all'acquisizione di variabili locali da parte delle espressioni lambda.</span><span class="sxs-lookup"><span data-stu-id="7fcca-141">The capture has similar restrictions to the capture of local variables by lambda expressions.</span></span> <span data-ttu-id="7fcca-142">Ad esempio, i parametri `ref` e `out` sono consentiti nei costruttori primari, ma non è possibile acquisire i corpi dei membri.</span><span class="sxs-lookup"><span data-stu-id="7fcca-142">For instance, `ref` and `out` parameters are allowed in primary constructors, but cannot be captured my member bodies.</span></span>


## <a name="drawbacks"></a><span data-ttu-id="7fcca-143">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="7fcca-143">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="7fcca-144">In ordine di importanza approssimativa.</span><span class="sxs-lookup"><span data-stu-id="7fcca-144">In rough order of significance.</span></span>

* <span data-ttu-id="7fcca-145">La proposta usa la sintassi che è stata proposta anche per i record posizionali.</span><span class="sxs-lookup"><span data-stu-id="7fcca-145">The proposal uses syntax that has also been proposed for positional records.</span></span> <span data-ttu-id="7fcca-146">Se si desiderano entrambe le funzionalità, è necessario disporre di alcune sistemazioni.</span><span class="sxs-lookup"><span data-stu-id="7fcca-146">If we desire both features, some accommodation is required.</span></span> <span data-ttu-id="7fcca-147">ad esempio</span><span class="sxs-lookup"><span data-stu-id="7fcca-147">E.g.</span></span> <span data-ttu-id="7fcca-148">è stato proposto un modificatore `data` nei record.</span><span class="sxs-lookup"><span data-stu-id="7fcca-148">a `data` modifier on records has been proposed.</span></span>
* <span data-ttu-id="7fcca-149">Le dimensioni di allocazione degli oggetti costruiti sono meno ovvie, perché il compilatore determina se allocare un campo per un parametro del costruttore primario basato sul testo completo della classe.</span><span class="sxs-lookup"><span data-stu-id="7fcca-149">The allocation size of constructed objects is less obvious, as the compiler determines whether to allocate a field for a primary constructor parameter based on the full text of the class.</span></span> <span data-ttu-id="7fcca-150">Questo rischio è simile all'acquisizione implicita delle variabili da parte delle espressioni lambda.</span><span class="sxs-lookup"><span data-stu-id="7fcca-150">This risk is similar to the implicit capture of variables by lambda expressions.</span></span>
* <span data-ttu-id="7fcca-151">Una tentazione comune (o un modello accidentale) potrebbe essere l'acquisizione del parametro "Same" a più livelli di ereditarietà mentre viene passata alla catena di costruttori invece di attribuire in modo esplicito un campo protetto nella classe di base, causando allocazioni duplicate. per gli stessi dati negli oggetti.</span><span class="sxs-lookup"><span data-stu-id="7fcca-151">A common temptation (or accidental pattern) might be to capture the "same" parameter at multiple levels of inheritance as it is passed up the constructor chain instead of explicitly allotting it a protected field at the base class, leading to duplicated allocations for the same data in objects.</span></span> <span data-ttu-id="7fcca-152">Questo è molto simile al rischio attuale di eseguire l'override delle proprietà automatiche con proprietà automatiche.</span><span class="sxs-lookup"><span data-stu-id="7fcca-152">This is very similar to today's risk of overriding auto-properties with auto-properties.</span></span> 
* <span data-ttu-id="7fcca-153">Come suggerito in precedenza, non esiste alcuna posizione per la logica aggiuntiva che in genere può essere espressa nei corpi del costruttore.</span><span class="sxs-lookup"><span data-stu-id="7fcca-153">As proposed above, there is no place for additional logic that might usually expressed in constructor bodies.</span></span> <span data-ttu-id="7fcca-154">L'estensione "Primary Constructor Bodies" sotto si rivolge a questo.</span><span class="sxs-lookup"><span data-stu-id="7fcca-154">The "Primary constructor bodies" extension below addresses that.</span></span>
* <span data-ttu-id="7fcca-155">Come proposto, la semantica dell'ordine di esecuzione è leggermente diversa rispetto a quella dei costruttori normali.</span><span class="sxs-lookup"><span data-stu-id="7fcca-155">As proposed, execution order semantics are subtly different than with ordinary constructors.</span></span> <span data-ttu-id="7fcca-156">Questa situazione potrebbe essere ovvia, ma al costo di alcune proposte di estensione, in particolare "corpi del costruttore primario".</span><span class="sxs-lookup"><span data-stu-id="7fcca-156">This could probably be remedied, but at the cost of some of the extension proposals (notably "Primary constructor bodies").</span></span>
* <span data-ttu-id="7fcca-157">La proposta funziona solo quando è possibile designare un singolo costruttore come primario.</span><span class="sxs-lookup"><span data-stu-id="7fcca-157">The proposal only works when a single constructor can be designated primary.</span></span>
* <span data-ttu-id="7fcca-158">Non è possibile avere un'accessibilità separata della classe e del costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="7fcca-158">There is no way to have separate accessibility of the class and the primary constructor.</span></span> <span data-ttu-id="7fcca-159">Ad esempio, nelle situazioni in cui i costruttori pubblici delegano tutti a un costruttore privato "Build-it-all" che sarebbe necessario.</span><span class="sxs-lookup"><span data-stu-id="7fcca-159">For instance, in situations where public constructors all delegate to one private "build-it-all" constructor that would be needed.</span></span> <span data-ttu-id="7fcca-160">Se necessario, è possibile proporre la sintassi per tale versione in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="7fcca-160">If necessary, syntax could be proposed for that later.</span></span>


## <a name="alternatives"></a><span data-ttu-id="7fcca-161">Alternativi</span><span class="sxs-lookup"><span data-stu-id="7fcca-161">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="7fcca-162">I record posizionali completi possono essere un'alternativa oppure possono coesistere con i costruttori primari, a seconda delle specifiche.</span><span class="sxs-lookup"><span data-stu-id="7fcca-162">Full-on positional records may be an alternative, or may coexist with primary constructors, depending on the specifics.</span></span> <span data-ttu-id="7fcca-163">Consentono una *maggiore* abbreviazione in un *minor* numero di scenari.</span><span class="sxs-lookup"><span data-stu-id="7fcca-163">They would allow for *more* abbreviation in a *smaller* number of scenarios.</span></span> <span data-ttu-id="7fcca-164">Quindi, entrambi sono potenzialmente utili, ma la loro presenza potrebbe essere eccessiva, a meno che non possano essere perfettamente integrati tra loro.</span><span class="sxs-lookup"><span data-stu-id="7fcca-164">So both are potentially useful, but having both may be overkill, unless they can be somewhat neatly integrated with each other.</span></span>


## <a name="possible-extensions"></a><span data-ttu-id="7fcca-165">Estensioni possibili</span><span class="sxs-lookup"><span data-stu-id="7fcca-165">Possible extensions</span></span>
[extensions]: #possible-extensions

<span data-ttu-id="7fcca-166">Si tratta di varianti o aggiunte alla proposta di base che possono essere considerate insieme a essa o in una fase successiva, se ritenute utili.</span><span class="sxs-lookup"><span data-stu-id="7fcca-166">These are variations or additions to the core proposal that may be considered in conjunction with it, or at a later stage if deemed useful.</span></span>

### <a name="primary-constructor-bodies"></a><span data-ttu-id="7fcca-167">Corpi del costruttore primario</span><span class="sxs-lookup"><span data-stu-id="7fcca-167">Primary constructor bodies</span></span>

<span data-ttu-id="7fcca-168">I costruttori stessi spesso contengono la logica di convalida del parametro o altro codice di inizializzazione non banale che non può essere espresso come inizializzatori.</span><span class="sxs-lookup"><span data-stu-id="7fcca-168">Constructors themselves often contain parameter validation logic or other nontrivial initialization code that cannot be expressed as initializers.</span></span>

<span data-ttu-id="7fcca-169">I costruttori primari possono essere estesi per consentire la visualizzazione dei blocchi di istruzioni direttamente nel corpo della classe.</span><span class="sxs-lookup"><span data-stu-id="7fcca-169">Primary constructors could be extended to allow statement blocks to appear directly in the class body.</span></span> <span data-ttu-id="7fcca-170">Tali istruzioni verranno inserite nel costruttore generato nel punto in cui compaiono tra l'inizializzazione delle assegnazioni e verrebbero quindi eseguite intervallate dagli inizializzatori.</span><span class="sxs-lookup"><span data-stu-id="7fcca-170">Those statements would be inserted in the generated constructor at the point where they appear between initializing assignments, and would thus be executed interspersed with initializers.</span></span> <span data-ttu-id="7fcca-171">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7fcca-171">For instance:</span></span>

``` c#
public class C(int i, string s) : B(s)
{
    {
        if (i < 0) throw new ArgumentOutOfRangeException(nameof(i));
    }
    int[] a = new int[i];
    public int S => s;
}
```

### <a name="initializer-fields-and-initializer-functions"></a><span data-ttu-id="7fcca-172">Campi inizializzatori e funzioni inizializzatore</span><span class="sxs-lookup"><span data-stu-id="7fcca-172">Initializer fields and initializer functions</span></span>

<span data-ttu-id="7fcca-173">In una classe con un costruttore primario è possibile prendere in considerazione le dichiarazioni di campo e di metodo senza modificatori di accessibilità come variabili locali e funzioni locali:</span><span class="sxs-lookup"><span data-stu-id="7fcca-173">In a class with a primary constructor we could consider field and method declarations without accessibility modifiers to be more like local variables and local functions:</span></span>

* <span data-ttu-id="7fcca-174">Analogamente ai parametri del costruttore primario, i "campi dell'inizializzatore" vengono acquisiti in un campo privato effettivo se sono stati usati nei membri della funzione.</span><span class="sxs-lookup"><span data-stu-id="7fcca-174">Just like primary constructor parameters the "initializer fields" would only be captured into an actual private field if they were used in function members.</span></span>
* <span data-ttu-id="7fcca-175">Le "funzioni inizializzatore" verrebbero considerate solo per acquisire i parametri del costruttore primario e i campi di inizializzazione se fossero usati in altri membri di funzione.</span><span class="sxs-lookup"><span data-stu-id="7fcca-175">The "initializer functions" would only be considered to capture primary constructor parameters and initializer fields if they were themselves used in other function members.</span></span> <span data-ttu-id="7fcca-176">Se non vengono acquisiti, potrebbero essere generati in modo più ottimale, come le funzioni locali.</span><span class="sxs-lookup"><span data-stu-id="7fcca-176">If not captured, they could be generated in a more optimal fashion, like local functions.</span></span>
* <span data-ttu-id="7fcca-177">Analogamente ai parametri del costruttore primario, non sono disponibili tramite l'accesso ai membri, ma solo come nome semplice.</span><span class="sxs-lookup"><span data-stu-id="7fcca-177">Just like primary constructor parameters they would not be available via member access, but only as a simple name.</span></span>

<span data-ttu-id="7fcca-178">Questa operazione può essere utilizzata per variabili temporanee e funzioni di supporto rilevanti solo per l'inizializzazione:</span><span class="sxs-lookup"><span data-stu-id="7fcca-178">This could be used for temporary variables and helper functions that are only relevant to initialization:</span></span>

``` c#
public class C(string s)
{
    int size = s.Length;             // not captured
    int[] Create() => new int[size]; // not captured
    int[] a = Create();
    ...
}
```

<span data-ttu-id="7fcca-179">Questo può essere troppo sottile, soprattutto perché l'assenza di modificatori di accessibilità altrove significa semplicemente `private`.</span><span class="sxs-lookup"><span data-stu-id="7fcca-179">This may be too subtle, especially since the absence of accessibility modifiers elsewhere simply means `private`.</span></span> 

### <a name="initializer-statements"></a><span data-ttu-id="7fcca-180">Istruzioni inizializzatore</span><span class="sxs-lookup"><span data-stu-id="7fcca-180">Initializer statements</span></span>

<span data-ttu-id="7fcca-181">Una combinazione radicale delle estensioni precedenti consiste nel consentire semplicemente le istruzioni direttamente nel corpo della classe.</span><span class="sxs-lookup"><span data-stu-id="7fcca-181">A radical combination of the above to extensions would be to simply allow statements directly in the class body.</span></span> <span data-ttu-id="7fcca-182">Tali istruzioni sono esattamente come i corpi dei costruttori intervallati proposte in precedenza, ad eccezione del fatto che non devono essere racchiusi in `{ }`.</span><span class="sxs-lookup"><span data-stu-id="7fcca-182">Such statements are exactly as the interspersed constructor bodies proposed above, except they don't need to be enclosed in `{ }`.</span></span> <span data-ttu-id="7fcca-183">Per poter essere sufficientemente utile, le variabili locali e le funzioni di supporto devono essere esprimibile anche al livello superiore della classe, nel modo in cui vengono esplorate nell'estensione "campi dell'inizializzatore e funzioni di inizializzazione" sopra.</span><span class="sxs-lookup"><span data-stu-id="7fcca-183">For this to be sufficiently useful, "local" variables and helper functions would need to also be expressible at the top level of the class, in the manner explored in the extension "Initializer fields and initializer functions" above.</span></span>


### <a name="member-access"></a><span data-ttu-id="7fcca-184">Accesso ai membri</span><span class="sxs-lookup"><span data-stu-id="7fcca-184">Member access</span></span>

<span data-ttu-id="7fcca-185">La proposta principale considera i parametri del costruttore primario come parametri che possono essere definiti solo come nomi semplici.</span><span class="sxs-lookup"><span data-stu-id="7fcca-185">The core proposal treats primary constructor parameters as parameters that can only be referred as simple names.</span></span> <span data-ttu-id="7fcca-186">In alternativa, è possibile farvi riferimento come se fossero campi privati, ad esempio con un accesso ai membri, *anche* se talvolta non vengono generati come campi.</span><span class="sxs-lookup"><span data-stu-id="7fcca-186">An alternative is to allow them to be referenced as if they were private fields, i.e. with a member access, *even* if they are sometimes not generated as fields.</span></span> <span data-ttu-id="7fcca-187">In questo modo è possibile fare riferimento a tali `this.x` quando vengono nascoste dalle variabili locali e a cui si accede da un'istanza diversa come `other.x`.</span><span class="sxs-lookup"><span data-stu-id="7fcca-187">This would allow them to be referenced as `this.x` when shadowed by local variables, and accessed from a different instance as `other.x`.</span></span>

<span data-ttu-id="7fcca-188">Se applicato all'estensione "campi inizializzatori e funzioni inizializzatore", questo ridurrebbe anche il grado di differenza rispetto ai membri privati normali.</span><span class="sxs-lookup"><span data-stu-id="7fcca-188">If applied to the "initializer fields and initializer functions" extension this would also reduce the degree to which those were different from ordinary private members.</span></span> <span data-ttu-id="7fcca-189">L'unica differenza è che il compilatore è libero di Elide dall'oggetto se usato solo durante l'inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="7fcca-189">The only difference would then be that the compiler is free to elide them from the object if only used during initialization.</span></span>

