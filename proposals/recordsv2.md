---
ms.openlocfilehash: 5636157ba54e93587847d6f2f7aac2dc675f3112
ms.sourcegitcommit: af27912886f22cda9b98b7769447d85cd9732736
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "79485000"
---

# <a name="records-v2"></a><span data-ttu-id="66f80-101">Record V2</span><span class="sxs-lookup"><span data-stu-id="66f80-101">Records v2</span></span>

<span data-ttu-id="66f80-102">In passato abbiamo pensato ai record come una funzionalità per consentire l'utilizzo dei dati.</span><span class="sxs-lookup"><span data-stu-id="66f80-102">In the past we've thought about records as a feature to enable working with data.</span></span>

<span data-ttu-id="66f80-103">"Lavorare con i dati" è un grande gruppo con diversi facet, quindi può essere interessante esaminare ogni isolamento.</span><span class="sxs-lookup"><span data-stu-id="66f80-103">"Working with data" is a big group with a number of facets, so it may be interesting to look at each in isolation.</span></span> <span data-ttu-id="66f80-104">Iniziamo esaminando un esempio di record oggi e alcuni dei relativi svantaggi.</span><span class="sxs-lookup"><span data-stu-id="66f80-104">Let's start by looking at an example of records today and some of its drawbacks.</span></span>

<span data-ttu-id="66f80-105">Ad esempio, un record semplice verrebbe definito oggi come segue</span><span class="sxs-lookup"><span data-stu-id="66f80-105">For instance, a simple record would be defined today as follows</span></span>

```C#
public class UserInfo
{
    public string Username { get; set; }
    public string Email { get; set; }
    public bool IsAdmin  { get; set; } = false;
}
```

<span data-ttu-id="66f80-106">e l'utilizzo verrebbe letto</span><span class="sxs-lookup"><span data-stu-id="66f80-106">and the usage would read</span></span>

```C#
void M()
{
    var userInfo = new UserInfo() 
    {
        Username = "andy",
        Email = "angocke@microsoft.com",
        IsAdmin = true
    };
}
```

<span data-ttu-id="66f80-107">Questo codice presenta vantaggi significativi:</span><span class="sxs-lookup"><span data-stu-id="66f80-107">There are significant advantages in this code:</span></span>

1. <span data-ttu-id="66f80-108">La definizione è resiliente della versione, le proprietà possono essere aggiunte o spostate facilmente</span><span class="sxs-lookup"><span data-stu-id="66f80-108">The definition is version resilient, properties can easily be added or moved</span></span>
2. <span data-ttu-id="66f80-109">Le proprietà possono essere impostate in qualsiasi ordine e i nomi nell'inizializzazione corrispondono sempre alle funzioni di accesso</span><span class="sxs-lookup"><span data-stu-id="66f80-109">Properties can be set in any order, and the names in the initialization always match the accessors</span></span>
3. <span data-ttu-id="66f80-110">Le proprietà con valori predefiniti possono semplicemente essere ignorate</span><span class="sxs-lookup"><span data-stu-id="66f80-110">Properties with default values can simply be skipped</span></span>

<span data-ttu-id="66f80-111">Il primo difetto è che ora le proprietà devono essere modificabili.</span><span class="sxs-lookup"><span data-stu-id="66f80-111">The first flaw is that the properties must now be mutable.</span></span>

# <a name="mutability"></a><span data-ttu-id="66f80-112">Modificabilità</span><span class="sxs-lookup"><span data-stu-id="66f80-112">Mutability</span></span>

<span data-ttu-id="66f80-113">C# Per fornire un modo per impostare un membro `readonly` negli inizializzatori di oggetto.</span><span class="sxs-lookup"><span data-stu-id="66f80-113">What we'd like is for C# to provide a way to set a `readonly` member in object initializers.</span></span>
<span data-ttu-id="66f80-114">Poiché alcuni tipi potrebbero non essere stati progettati tenendo conto di questa inizializzazione, è anche possibile acconsentire esplicitamente.</span><span class="sxs-lookup"><span data-stu-id="66f80-114">Since some types may not have been designed with this initialization in mind, we'd also like it to be opt-in.</span></span>

<span data-ttu-id="66f80-115">La soluzione proposta è un nuovo modificatore, `initonly`, che può essere applicato a proprietà e campi:</span><span class="sxs-lookup"><span data-stu-id="66f80-115">The proposed solution is a new modifier, `initonly`, that can be applied to properties and fields:</span></span>

```C#
public class UserInfo
{
    public initonly string Username { get; }
    public initonly string Email { get; }
    public initonly bool IsAdmin { get; } = false;
}
```

<span data-ttu-id="66f80-116">Il codegen è sorprendentemente semplice: è sufficiente impostare il campo ReadOnly.</span><span class="sxs-lookup"><span data-stu-id="66f80-116">The codegen for this is surprisingly straight forward: we just set the readonly field.</span></span>
<span data-ttu-id="66f80-117">In particolare, le proprietà ridotte avranno un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="66f80-117">Specifically, the lowered properties would look like:</span></span>

```C#
public class UserInfo
{
    private readonly string <Backing>_username;
    public string get_Username() => <Backing>_username;
    [return: modreq(initonly)]
    public void set_Username(string value) { <Backing>_username = value; }
    ...
}
```

<span data-ttu-id="66f80-118">CLR considera non verificabili i campi di sola lettura, ma non quelli non sicuri.</span><span class="sxs-lookup"><span data-stu-id="66f80-118">The CLR considers setting readonly fields to be unverifiable, but not unsafe.</span></span> <span data-ttu-id="66f80-119">Per supportare un verificatore più avanzato, viene proposta la regola seguente: un campo di sola lettura può essere modificato solo all'interno di `initonly` metodi o su un nuovo oggetto che si trova nello stack CLR e non è stato pubblicato tramite una chiamata al metodo o all'archivio.</span><span class="sxs-lookup"><span data-stu-id="66f80-119">To support a more advanced verifier, the following rule is proposed: a readonly field can be modified only inside `initonly` methods, or on a new object that is on the CLR stack and has not been published via a store or method call.</span></span>

<span data-ttu-id="66f80-120">Questo dovrebbe risolvere molti dei problemi di mutabilità nell'esempio `UserInfo`, senza richiedere strategie di generazione complesse o fragili.</span><span class="sxs-lookup"><span data-stu-id="66f80-120">This should solve many of the problems with mutability in the `UserInfo` example, while not requiring complicated or brittle emit strategies.</span></span> <span data-ttu-id="66f80-121">Tuttavia, l'immutabilità presenta un nuovo problema: la semplice creazione di un oggetto con modifiche.</span><span class="sxs-lookup"><span data-stu-id="66f80-121">However, immutability does present a new problem: easily constructing an object with changes.</span></span>

# <a name="with-ing"></a><span data-ttu-id="66f80-122">Con-Ing</span><span class="sxs-lookup"><span data-stu-id="66f80-122">With-ing</span></span>

<span data-ttu-id="66f80-123">Quando si esegue la programmazione con l'immutabilità, le modifiche apportate a un oggetto vengono eseguite costruendo una copia con le modifiche anziché apportare le modifiche direttamente nell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="66f80-123">When programming with immutability, making changes to an object is done by constructing a copy with changes instead of making the changes directly on the object.</span></span> <span data-ttu-id="66f80-124">Sfortunatamente, non esiste un modo pratico per eseguire questa operazione C#in, nemmeno con i record in stile corrente.</span><span class="sxs-lookup"><span data-stu-id="66f80-124">Unfortunately, there's no convenient way to do this in C#, even with current-style records.</span></span> <span data-ttu-id="66f80-125">In precedenza è stato proposto che venisse fornito un tipo di metodo "with" generato automaticamente per i record che implementano tale funzionalità.</span><span class="sxs-lookup"><span data-stu-id="66f80-125">It's been previously proposed that some kind of autogenerated "With" method be provided for records that implements that functionality.</span></span> <span data-ttu-id="66f80-126">Se si dispone di un meccanismo di questo tipo, è importante che funzioni con i membri del `initonly`.</span><span class="sxs-lookup"><span data-stu-id="66f80-126">If we have such a mechanism, it's important that it work with `initonly` members.</span></span> <span data-ttu-id="66f80-127">A tale scopo, viene proposto di aggiungere un'espressione `with`, analoga a un inizializzatore di oggetto.</span><span class="sxs-lookup"><span data-stu-id="66f80-127">To achieve this, it's proposed that we add a `with` expression, analogous to an object initializer.</span></span> <span data-ttu-id="66f80-128">L'utilizzo di esempio sarà il seguente:</span><span class="sxs-lookup"><span data-stu-id="66f80-128">Sample usage would be as follows:</span></span>

```C#
var userInfo = new UserInfo() 
{
    Username = "andy",
    Email = "angocke@microsoft.com",
    IsAdmin = true
};
var newUserName = userInfo with { Username = "angocke" };
```

<span data-ttu-id="66f80-129">L'oggetto `newUserName` risultante sarà una copia di `userInfo`, con `Username` impostato su "angocke".</span><span class="sxs-lookup"><span data-stu-id="66f80-129">The resulting `newUserName` object would be a copy of `userInfo`, with `Username` set to "angocke".</span></span>
<span data-ttu-id="66f80-130">Il valore di codegen sull'espressione `with` sarebbe anche simile all'inizializzatore di oggetto: viene costruito un nuovo oggetto, quindi il set di `Username` `initonly` verrebbe chiamato nel corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="66f80-130">The codegen on the `with` expression would also be similar to the object initializer: a new object is constructed, and then the `initonly` `Username` setter would be called in the method body.</span></span>

<span data-ttu-id="66f80-131">Naturalmente, la differenza è che il nuovo oggetto costruito non è una semplice creazione di un nuovo oggetto, è un duplicato dell'oggetto originale.</span><span class="sxs-lookup"><span data-stu-id="66f80-131">Of course, the difference here is that the new object being constructed is not a simple new object creation, it is a duplicate of the original object.</span></span> <span data-ttu-id="66f80-132">Per fornire questa funzionalità, è necessario che l'oggetto fornisca un "with Constructor" che fornisce un oggetto duplicato.</span><span class="sxs-lookup"><span data-stu-id="66f80-132">To provide this functionality, we require that the object provide a "With constructor" that provides a duplicate object.</span></span> <span data-ttu-id="66f80-133">Un costruttore di `With` di esempio avrà un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="66f80-133">A sample `With` constructor would look like:</span></span>

```C#
class UserInfo
{
    ...
    [WithConstructor] // placeholder syntax, up for debate
    public UserInfo With()
    {
        return new UserInfo() { Username = this.Username, Email = this.Email, IsAdmin = this.IsAdmin };
    }
}
```

<span data-ttu-id="66f80-134">In particolare, l'espressione `with` imposta `initonly` membri, proprio come l'inizializzatore di oggetto, pertanto per supportare la verifica è necessario assicurarsi che l'oggetto non possa essere stato pubblicato prima che i membri del `initonly` siano impostati.</span><span class="sxs-lookup"><span data-stu-id="66f80-134">Notably, the `with` expression will set `initonly` members, just like the object initializer, so to support verification we must ensure that the object cannot have been published before the `initonly` members are set.</span></span> <span data-ttu-id="66f80-135">Per applicare questa operazione, l'attributo `WithConstructor` (o la sintassi equivalente) imporrà una nuova regola per il metodo: tutte le istruzioni return devono contenere direttamente un'espressione di creazione di oggetti, possibilmente con un inizializzatore di oggetto.</span><span class="sxs-lookup"><span data-stu-id="66f80-135">To enforce this, the `WithConstructor` attribute (or equivalent syntax) will enforce a new rule for the method: all return statements must directly contain an object creation expression, possibly with an object initializer.</span></span>

<span data-ttu-id="66f80-136">Se il costruttore di `With` richiede la convalida, l'utente può introdurre un costruttore per eseguire tale convalida, ad esempio</span><span class="sxs-lookup"><span data-stu-id="66f80-136">If the `With` constructor requires validation, the user may introduce a constructor to do that validation, e.g.</span></span>

```C#
class UserInfo
{
    ...
    private UserInfo(UserInfo original)
    {
        // validation code
    }
    [WithConstructor]
    public UserInfo With() => new UserInfo(this);
}
```

<span data-ttu-id="66f80-137">L'ultimo elemento di complessità associato a `With` è l'ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="66f80-137">The last piece of complexity associated with `With` is inheritance.</span></span> <span data-ttu-id="66f80-138">Se il record è estensibile, sarà necessario fornire un nuovo `With` per la sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="66f80-138">If your record is extensible, you will need to provide a new `With` for the subclass.</span></span> <span data-ttu-id="66f80-139">Questa operazione può essere eseguita nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="66f80-139">This can be achieved as follows:</span></span>

```C#
class Base
{
    ...
    protected Base(Base original)
    {
        // validation
    }
    [WithConstructor]
    public virtual Base With() => new Base(this);
}
class Derived : Base
{
    ...
    protected Derived(Derived original)
    : base(original)
    {
        // validation
    }
    [WithConstructor]
    public override Derived With() => new Derived(this);
}
```

<span data-ttu-id="66f80-140">Si noti un'ulteriore complessità: per eseguire l'override del costruttore `With` con il tipo derivato, il linguaggio dovrà supportare anche tipi restituiti covarianti nelle sostituzioni.</span><span class="sxs-lookup"><span data-stu-id="66f80-140">Note one additional piece of complexity here: in order to override the `With` constructor with the derived type the language will also need to support covariant return types in overrides.</span></span>
<span data-ttu-id="66f80-141">Per [questa funzionalità è già presente una](https://github.com/dotnet/csharplang/blob/725763343ad44a9251b03814e6897d87fe553769/proposals/covariant-returns.md)proposta distinta.</span><span class="sxs-lookup"><span data-stu-id="66f80-141">There is already a separate proposal for this feature [here](https://github.com/dotnet/csharplang/blob/725763343ad44a9251b03814e6897d87fe553769/proposals/covariant-returns.md).</span></span>

<span data-ttu-id="66f80-142">**Svantaggi**</span><span class="sxs-lookup"><span data-stu-id="66f80-142">**Drawbacks**</span></span>

- <span data-ttu-id="66f80-143">L'esecuzione di tutte le istruzioni return in `WithConstructor`s contiene nuove espressioni di oggetto è restrittiva.</span><span class="sxs-lookup"><span data-stu-id="66f80-143">Making all return statements in `WithConstructor`s contain new object expressions is restrictive.</span></span>
  <span data-ttu-id="66f80-144">Questo potrebbe essere mitigato dall'analisi dei flussi che garantisce che il nuovo oggetto non sfugga al metodo</span><span class="sxs-lookup"><span data-stu-id="66f80-144">This could be possibly be mitigated by flow analysis that ensures the new object doesn't escape the method</span></span>
- <span data-ttu-id="66f80-145">Per supportare la varianza nelle sostituzioni tramite trucchi del compilatore sono necessari metodi stub, che aumenteranno quadratico con la profondità dell'ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="66f80-145">Supporting variance in overrides through compiler tricks will require stub methods, which will grow quadratically with the inheritance depth.</span></span> <span data-ttu-id="66f80-146">La necessità di un metodo stub è causata da un requisito di runtime che sostituisce esattamente le firme.</span><span class="sxs-lookup"><span data-stu-id="66f80-146">The need for a stub method is due to a runtime requirement that override signatures match exactly.</span></span> <span data-ttu-id="66f80-147">Se il requisito di runtime è stato allentato, i metodi dello stub non sarebbero necessari.</span><span class="sxs-lookup"><span data-stu-id="66f80-147">If the runtime requirement were loosened, the stub methods would not be required at all.</span></span>
- <span data-ttu-id="66f80-148">Se si usano costruttori concatenati del modulo `Type(Type original)` si riserva effettivamente il costruttore per l'uso del modello.</span><span class="sxs-lookup"><span data-stu-id="66f80-148">Using chained constructors of the form `Type(Type original)` effectively reserves that constructor for the use of the pattern.</span></span> <span data-ttu-id="66f80-149">Poiché i costruttori hanno una semantica univoca e non possono essere rinominati, questa operazione potrebbe essere limitata.</span><span class="sxs-lookup"><span data-stu-id="66f80-149">Since constructors have unique semantics and cannot be re-named this could be limiting.</span></span>


## <a name="wrapping-it-all-up-records"></a><span data-ttu-id="66f80-150">Wrapping: record</span><span class="sxs-lookup"><span data-stu-id="66f80-150">Wrapping it all up: Records</span></span>

<span data-ttu-id="66f80-151">Le funzionalità sopra indicate consentono uno stile di programmazione molto difficile in precedenza.</span><span class="sxs-lookup"><span data-stu-id="66f80-151">The above features enable a style of programming that was very difficult before.</span></span> <span data-ttu-id="66f80-152">Tuttavia, anche con le nuove funzionalità, può essere abbastanza dettagliata e soggetta a errori per annotare tutto.</span><span class="sxs-lookup"><span data-stu-id="66f80-152">But even with the new features it could be quite verbose and error prone to annotate everything yourself.</span></span> <span data-ttu-id="66f80-153">Ci sono anche alcuni elementi, ad esempio Equals e GetHashCode, che possono essere già scritti oggi, ma solo laboriosi.</span><span class="sxs-lookup"><span data-stu-id="66f80-153">There are also a few items, like Equals and GetHashCode, which can already be written today, it's just laborious.</span></span>
<span data-ttu-id="66f80-154">Inoltre, un difetto significativo nell'implementazione dell'uguaglianza rispetto a queste nuove primitive è che l'uguaglianza strutturale è un elemento che deve essere modificato con il tipo di dati man mano che vengono aggiunti nuovi dati, ma durante la gestione manuale è probabile che queste operazioni possano essere sincronizzate.</span><span class="sxs-lookup"><span data-stu-id="66f80-154">Moreover, a significant flaw in implementing equality on top of these new primitives is that structural equality is something that should change with your data type as new data is added, but when handling it manually it is likely that these things can get out of sync.</span></span>

<span data-ttu-id="66f80-155">Pertanto, viene proposto che supporti C# la nuova sintassi per i record, non per fornire nuove funzionalità, ma per impostare i valori predefiniti e generare codice progettato per l'utilizzo nei record.</span><span class="sxs-lookup"><span data-stu-id="66f80-155">Therefore, it is proposed that C# support new syntax for records, not for providing new features, but for setting defaults and generating code designed for use in records.</span></span> <span data-ttu-id="66f80-156">La sintassi di esempio avrà un aspetto simile al seguente</span><span class="sxs-lookup"><span data-stu-id="66f80-156">Example syntax would look like</span></span>

```C#
data class UserInfo
{
    public string Username { get; }
    public string Email { get; }
    public bool IsAdmin { get; } = false;
}
```

<span data-ttu-id="66f80-157">Il codice generato per questa classe considera tutti i campi pubblici e le proprietà automatiche come membri strutturali del record.</span><span class="sxs-lookup"><span data-stu-id="66f80-157">The generated code for this class would regard all public fields and auto-properties as structural members of the record.</span></span> <span data-ttu-id="66f80-158">I membri del record possono essere personalizzati usando un nuovo attributo `RecordMember(bool)` che può essere usato per includere o escludere i membri.</span><span class="sxs-lookup"><span data-stu-id="66f80-158">Record members could be customized using a new `RecordMember(bool)` attribute that could be used to either include or exclude members.</span></span> <span data-ttu-id="66f80-159">I membri dei record vengono `initonly` per impostazione predefinita e l'uguaglianza verrebbe generata automaticamente per la classe in base ai membri del record.</span><span class="sxs-lookup"><span data-stu-id="66f80-159">Record members would be `initonly` by default and equality would be autogenerated for the class based on the record members.</span></span> <span data-ttu-id="66f80-160">In qualsiasi momento, il comportamento di questi membri potrebbe essere personalizzato semplicemente dichiarando tali membri nell'origine.</span><span class="sxs-lookup"><span data-stu-id="66f80-160">At any point the behavior of these members could be customized simply by declaring them in source.</span></span> <span data-ttu-id="66f80-161">L'implementazione scritta dall'utente sostituisce l'implementazione predefinita in tutti i modelli di utilizzo.</span><span class="sxs-lookup"><span data-stu-id="66f80-161">The user-written implementation would replace the default implementation in all pattern usage.</span></span>

<span data-ttu-id="66f80-162">Si noti che l'uguaglianza in caso di ereditarietà è complessa, ma sembra che sia stata risolta adeguatamente nella [proposta di altri record](records.md).</span><span class="sxs-lookup"><span data-stu-id="66f80-162">Note that equality in the face of inheritance is complex, but seems to have been adequately solved in the [other records proposal](records.md).</span></span>

## <a name="primary-constructors"></a><span data-ttu-id="66f80-163">Costruttori primari</span><span class="sxs-lookup"><span data-stu-id="66f80-163">Primary constructors</span></span>

<span data-ttu-id="66f80-164">Nella proposta di record precedente è stata inclusa anche una nuova sintassi per un elenco di parametri nel tipo stesso, ad esempio</span><span class="sxs-lookup"><span data-stu-id="66f80-164">Previous record proposal have also included a new syntax for a parameter list on the type itself, e.g.</span></span>

```C#
class Point(int X, int Y);
```

<span data-ttu-id="66f80-165">Nella nuova progettazione, l'elenco di parametri è una funzionalità ortogonale C# , che può essere integrata in modo pulito con i record.</span><span class="sxs-lookup"><span data-stu-id="66f80-165">In the new design, the parameter list would be an orthogonal C# feature, which could be cleanly integrated with records.</span></span> <span data-ttu-id="66f80-166">Se un costruttore primario è incluso in un record, avrà nuove impostazioni predefinite, proprio come i campi pubblici e le proprietà automatiche: i parametri nel costruttore primario verrebbero usati per generare proprietà del membro record pubblico con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="66f80-166">If a primary constructor is included in a record, it would have new defaults, just like public fields and auto-properties: the parameters in the primary constructor would be used to generate public record-member properties with the same name.</span></span> <span data-ttu-id="66f80-167">Inoltre, è ora possibile usare il costruttore primario per generare automaticamente un oggetto Deconstructor.</span><span class="sxs-lookup"><span data-stu-id="66f80-167">In addition, the primary constructor could now be used to auto-generate a deconstructor.</span></span>

<span data-ttu-id="66f80-168">Ad esempio, il record seguente con un costruttore primario</span><span class="sxs-lookup"><span data-stu-id="66f80-168">For example, the following record with a primary constructor</span></span>

```C#
data class Point(int X, int Y);
```

<span data-ttu-id="66f80-169">equivale a</span><span class="sxs-lookup"><span data-stu-id="66f80-169">would be equivalent to</span></span>

```C#
data class Point
{
    public int X { get; }
    public int Y { get; }

    public Point(int x, int y)
    {
        X = x;
        Y = y;
    }

    public void Deconstruct(out int X, out int Y)
    {
        X = this.X;
        Y = this.Y;
    }
}
```

<span data-ttu-id="66f80-170">e la generazione definitiva del precedente sarebbe</span><span class="sxs-lookup"><span data-stu-id="66f80-170">and the final generation of the above would be</span></span>

```C#
class Point
{
    public initonly int X { get; }
    public initonly int Y { get; }

    public Point(int x, int y)
    {
        X = x;
        Y = y;
    }

    protected Point(Point other)
    : this(other.X, other.Y)
    { }

    [WithConstructor]
    public virtual Point With() => new Point(this);

    public void Deconstruct(out int X, out int Y)
    {
        X = this.X;
        Y = this.Y;
    }

    // Generated equality
}
```

<span data-ttu-id="66f80-171">Si noti che sono state prese in considerazione un'altra informazione per una classe di dati con un costruttore primario: anziché impostare i campi primari all'interno del costruttore protetto generato, viene delegato al costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="66f80-171">Note that we've taken one other piece of information into account for a data class with a primary constructor: instead of setting the primary fields inside the generated protected constructor, we delegate to the primary constructor.</span></span> <span data-ttu-id="66f80-172">Se la classe Point aveva un altro membro record non primario, ad esempio</span><span class="sxs-lookup"><span data-stu-id="66f80-172">If the Point class had another non-primary record member, e.g.</span></span>

```C#
data class Point(int X, int Y)
{
    public int Z { get; }
}
```

<span data-ttu-id="66f80-173">quindi, il costruttore protetto generato verrà modificato come segue:</span><span class="sxs-lookup"><span data-stu-id="66f80-173">then that would change the generated protected constructor as follows:</span></span>

```C#
class Point
{
    // ...
    protected Point(Point other)
    : this(other.X, other.Y)
    {
        Z = other.Z;
    }
    // ...
}
```

<span data-ttu-id="66f80-174">In particolare, questo non risponde alle operazioni da eseguire sull'ereditarietà dei record con i costruttori primari.</span><span class="sxs-lookup"><span data-stu-id="66f80-174">Notably, this doesn't answer what to do about inheritance of records with primary constructors.</span></span> <span data-ttu-id="66f80-175">Ad esempio,</span><span class="sxs-lookup"><span data-stu-id="66f80-175">For instance,</span></span>

```C#
data class A(int X, int Y);
data class B(int X, int Y, int Z) : A;
```

<span data-ttu-id="66f80-176">Anziché risolverli in modo arbitrario, un approccio più esplicito potrebbe richiedere l'inclusione di un elenco di parametri con l'elenco di base, ad esempio</span><span class="sxs-lookup"><span data-stu-id="66f80-176">Rather than resolving in an arbitrary manner, a more explicit approach could require that a parameter list be provided with the base list, e.g.</span></span>

```C#
data class A(int X, int Y);
data class B(int X, int Y, int Z) : A(X, Y);
```

<span data-ttu-id="66f80-177">L'elenco di parametri nell'elenco base verrebbe quindi applicato a una chiamata `base` nel costruttore primario generato:</span><span class="sxs-lookup"><span data-stu-id="66f80-177">The parameter list in the base list would then be applied to a `base` call in the generated primary constructor:</span></span>

```C#
class B
{
    // ..
    public B(int x, int y, int z)
    : base(x, y)
    // ..
}
```

<span data-ttu-id="66f80-178">Per quanto riguarda il modo in cui un costruttore primario può significare all'esterno di un record, questo è ancora aperto per una proposta ulteriore.</span><span class="sxs-lookup"><span data-stu-id="66f80-178">As for what a primary constructor could mean outside of a record, that is still open to further proposal.</span></span>
