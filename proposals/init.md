---
ms.openlocfilehash: ddcacefebc0580a8121adda4693bfea6eaf16b94
ms.sourcegitcommit: fea3e4f37432254c00c29988c2ed0efebc7af20a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82072300"
---
<a name="init-only-setters"></a><span data-ttu-id="74dc7-101">Solo Setter Init</span><span class="sxs-lookup"><span data-stu-id="74dc7-101">Init Only Setters</span></span>
=====

## <a name="summary"></a><span data-ttu-id="74dc7-102">Summary</span><span class="sxs-lookup"><span data-stu-id="74dc7-102">Summary</span></span>
<span data-ttu-id="74dc7-103">Questa proposta aggiunge il concetto di proprietà solo init a C .</span><span class="sxs-lookup"><span data-stu-id="74dc7-103">This proposal adds the concept of init only properties to C#.</span></span> <span data-ttu-id="74dc7-104">Queste proprietà possono essere impostate nel punto `get` di creazione dell'oggetto, ma diventano effettissuose solo dopo il completamento della creazione dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="74dc7-104">These properties can be set at the point of object creation but become effectively `get` only once object creation has completed.</span></span> <span data-ttu-id="74dc7-105">In questo modo è possibile ottenere un modello non modificabile molto più flessibile in C.</span><span class="sxs-lookup"><span data-stu-id="74dc7-105">This allows for a much more flexible immutable model in C#.</span></span> 

## <a name="motivation"></a><span data-ttu-id="74dc7-106">Motivazione</span><span class="sxs-lookup"><span data-stu-id="74dc7-106">Motivation</span></span>
<span data-ttu-id="74dc7-107">I meccanismi sottostanti per la creazione di dati non modificabili in C , non sono stati modificati dopo 1.0.The underlying mechanisms for building immutable data in C's no changed since 1.0.</span><span class="sxs-lookup"><span data-stu-id="74dc7-107">The underlying mechanisms for building immutable data in C# haven't changed since 1.0.</span></span> <span data-ttu-id="74dc7-108">Essi rimangono:</span><span class="sxs-lookup"><span data-stu-id="74dc7-108">They remain:</span></span>

1. <span data-ttu-id="74dc7-109">Dichiarazione di `readonly`campi come .</span><span class="sxs-lookup"><span data-stu-id="74dc7-109">Declaring fields as `readonly`.</span></span>
1. <span data-ttu-id="74dc7-110">Dichiarazione di proprietà `get` che contengono solo una funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="74dc7-110">Declaring properties that contain only a `get` accessor.</span></span>

<span data-ttu-id="74dc7-111">Questi meccanismi sono efficaci per consentire la costruzione di dati non modificabili, ma lo fanno aggiungendo il costo al codice boilerplate dei tipi e scegliendo tali tipi da funzionalità come gli inizializzatori di oggetto e di raccolta.</span><span class="sxs-lookup"><span data-stu-id="74dc7-111">These mechanisms are effective at allowing the construction of immutable data but they do so by adding cost to the boilerplate code of types and opting such types out of features like object and collection initializers.</span></span> <span data-ttu-id="74dc7-112">Ciò significa che gli sviluppatori devono scegliere tra facilità d'uso e immutabilità.</span><span class="sxs-lookup"><span data-stu-id="74dc7-112">This means developers must choose between ease of use and immutability.</span></span>

<span data-ttu-id="74dc7-113">Un semplice oggetto non `Point` modificabile come richiede il doppio del codice della piastra della caldaia per supportare la costruzione rispetto a dichiarare il tipo.</span><span class="sxs-lookup"><span data-stu-id="74dc7-113">A simple immutable object like `Point` requires twice as much boiler plate code to support construction as it does to declare the type.</span></span> <span data-ttu-id="74dc7-114">Più grande è il tipo più grande è il costo di questa piastra caldaia:</span><span class="sxs-lookup"><span data-stu-id="74dc7-114">The bigger the type the bigger the cost of this boiler plate:</span></span>

```cs
struct Point
{
    public int X { get; }
    public int Y { get; }

    public Point(int X, int Y)
    {
        this.X = x;
        this.Y = y;
    }
}
```

<span data-ttu-id="74dc7-115">La `init` funzione di accesso rende gli oggetti non modificabili più flessibili consentendo al chiamante di mutare i membri durante l'atto di costruzione.</span><span class="sxs-lookup"><span data-stu-id="74dc7-115">The `init` accessor makes immutable objects more flexible by allowing the caller to mutate the members during the act of construction.</span></span> <span data-ttu-id="74dc7-116">Ciò significa che le proprietà non modificabili dell'oggetto possono partecipare agli inizializzatori di oggetto e quindi elimina la necessità di tutti i boilerplate del costruttore nel tipo.</span><span class="sxs-lookup"><span data-stu-id="74dc7-116">That means the object's immutable properties can participate in object initializers and thus removes the need for all constructor boilerplate in the type.</span></span> <span data-ttu-id="74dc7-117">Il `Point` tipo è ora semplicemente:</span><span class="sxs-lookup"><span data-stu-id="74dc7-117">The `Point` type is now simply:</span></span>

```cs
struct Point
{
    public int X { get; init; }
    public int Y { get; init; }
}
```

<span data-ttu-id="74dc7-118">Il consumer può quindi utilizzare gli inizializzatori di oggetto per creare l'oggetto</span><span class="sxs-lookup"><span data-stu-id="74dc7-118">The consumer can then use object initializers to create the object</span></span>

```cs
var p = new Point() { X = 42, Y = 13 };
```

## <a name="detailed-design"></a><span data-ttu-id="74dc7-119">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="74dc7-119">Detailed Design</span></span>

### <a name="init-members"></a><span data-ttu-id="74dc7-120">membri init</span><span class="sxs-lookup"><span data-stu-id="74dc7-120">init members</span></span>
<span data-ttu-id="74dc7-121">Una proprietà init only viene `init` dichiarata utilizzando la `set` funzione di accesso al posto della funzione di accesso:</span><span class="sxs-lookup"><span data-stu-id="74dc7-121">An init only property is declared by using the `init` accessor in place of the `set` accessor:</span></span>

```cs
class Student
{
    public string FirstName { get; init; }
    public string LastName { get; init; }
}
```

<span data-ttu-id="74dc7-122">Una proprietà di `init` istanza contenente una funzione di accesso viene considerata impostabile nelle circostanze seguenti:An instance property containing an accessor is considered settable in the following circumstances:</span><span class="sxs-lookup"><span data-stu-id="74dc7-122">An instance property containing an `init` accessor is considered settable in the following circumstances:</span></span>

- <span data-ttu-id="74dc7-123">Durante un inizializzatore di oggetto</span><span class="sxs-lookup"><span data-stu-id="74dc7-123">During an object initializer</span></span>
- <span data-ttu-id="74dc7-124">All'interno di un costruttore di `this` istanza del tipo contenitore o derivato,`base`</span><span class="sxs-lookup"><span data-stu-id="74dc7-124">Inside an instance constructor of the containing or derived type, on `this` or `base`</span></span>
- <span data-ttu-id="74dc7-125">All'interno dell'accesso `init` di `this` qualsiasi proprietà, su o`base`</span><span class="sxs-lookup"><span data-stu-id="74dc7-125">Inside the `init` accessor of any property, on `this` or `base`</span></span>

<span data-ttu-id="74dc7-126">I tempi precedenti `init` in cui le funzioni di accesso sono impostabili sono collettivamente indicati in questo documento come la fase di costruzione dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="74dc7-126">The times above in which the `init` accessors are settable are collectively referred to in this document as the construction phase of the object.</span></span>

<span data-ttu-id="74dc7-127">Ciò `Student` significa che la classe può essere utilizzata nei modi seguenti:This means the class can be used in the following ways:</span><span class="sxs-lookup"><span data-stu-id="74dc7-127">This means the `Student` class can be used in the following ways:</span></span>

```cs
var s = new Student()
{
    FirstName = "Jared",
    LastName = "Parosns",
};
s.LastName = "Parsons"; // Error: LastName is not settable
```

<span data-ttu-id="74dc7-128">Le regole `init` relative alle funzioni di accesso sono impostabili tra gerarchie di tipi.</span><span class="sxs-lookup"><span data-stu-id="74dc7-128">The rules around when `init` accessors are settable extend across type hierarchies.</span></span> <span data-ttu-id="74dc7-129">Se il membro è accessibile e l'oggetto è noto per essere in fase di costruzione, il membro è impostabile.</span><span class="sxs-lookup"><span data-stu-id="74dc7-129">If the member is accessible and the object is known to be in the construction phase then the member is settable.</span></span> <span data-ttu-id="74dc7-130">Ciò consente specificamente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="74dc7-130">That specifically allows for the following:</span></span>

```cs
class Base
{
    public bool Value { get; init; }
}

class Derived : Base
{
    Derived()
    {
        // Not allowed with get only properties but allowed with init
        Value = true;
    }
}

class Consumption
{
    void Example()
    {
        var d = new Derived() { Value = true; };
    }
}

```

<span data-ttu-id="74dc7-131">Nel punto `init` in cui viene richiamata una funzione di accesso, l'istanza è nota per essere nella fase di costruzione aperta.</span><span class="sxs-lookup"><span data-stu-id="74dc7-131">At the point a `init` accessor is invoked the instance is known to be in the open construction phase.</span></span> <span data-ttu-id="74dc7-132">Pertanto `init` una funzione di accesso può eseguire le `set` seguenti azioni oltre a ciò che una normale funzione di accesso può fare:</span><span class="sxs-lookup"><span data-stu-id="74dc7-132">Hence an `init` accessor is allowed to take the following actions in addition to what a normal `set` accessor can do:</span></span>

1. <span data-ttu-id="74dc7-133">Chiamare `init` altre funzioni `this` di accesso disponibili tramite o`base`</span><span class="sxs-lookup"><span data-stu-id="74dc7-133">Call other `init` accessors available through `this` or `base`</span></span>
1. <span data-ttu-id="74dc7-134">Assegnare `readonly` campi dichiarati nello stesso tipo</span><span class="sxs-lookup"><span data-stu-id="74dc7-134">Assign `readonly` fields declared on the same type</span></span>

```cs
class Complex
{
    readonly int Field1;
    int Field2;
    int Prop1 { get; init ; }
    int Prop2
    {
        get => 42;
        init
        {
            Field1 = 13; // okay
            Field2 = 13; // okay
            Prop1 = 13; // okay
        }
    }
}
```

<span data-ttu-id="74dc7-135">La possibilità `readonly` di assegnare campi da una `init` funzione di accesso è limitata ai campi dichiarati nello stesso tipo della funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="74dc7-135">The ability to assign `readonly` fields from an `init` accessor is limited to those fields declared on the same type as the accessor.</span></span> <span data-ttu-id="74dc7-136">Non può essere `readonly` utilizzato per assegnare campi in un tipo di base.</span><span class="sxs-lookup"><span data-stu-id="74dc7-136">It cannot be used to assign `readonly` fields in a base type.</span></span> <span data-ttu-id="74dc7-137">Questa regola garantisce che gli autori dei tipi mandino il controllo sul comportamento di mutabilità del tipo.</span><span class="sxs-lookup"><span data-stu-id="74dc7-137">This rule ensures that type authors remain in control over the mutability behavior of their type.</span></span> <span data-ttu-id="74dc7-138">Gli sviluppatori che non `init` desiderano utilizzare non possono essere influenzati da altri tipi che scelgono di farlo:</span><span class="sxs-lookup"><span data-stu-id="74dc7-138">Developers who do not wish to utilize `init` cannot be impacted from other types choosing to do so:</span></span>

```cs
class Base
{
    internal readonly int Field;
    internal int Property
    {
        get => Field;
        init => Field = value; // Okay
    }

    internal int OtherProperty { get; init; }
}

class Derived : Base
{
    internal readonly int DerivedField;
    internal int DerivedProperty
    {
        get => DerivedField;
        init
        {
            DerivedField = 42;  // Okay
            Property = 0;       // Okay
            Field = 13;         // Error Field is readonly
        }
    }

    public Derived()
    {
        Property = 42;  // Okay 
        Field = 13;     // Error Field is readonly
    }
}
```

<span data-ttu-id="74dc7-139">Quando `init` viene utilizzato in una proprietà virtuale, anche `init`tutti gli override devono essere contrassegnati come .</span><span class="sxs-lookup"><span data-stu-id="74dc7-139">When `init` is used in a virtual property then all the overrides must also be marked as `init`.</span></span> <span data-ttu-id="74dc7-140">Allo stesso modo non è `set` possibile `init`eseguire l'override di un semplice con .</span><span class="sxs-lookup"><span data-stu-id="74dc7-140">Likewise it is not possible to override a simple `set` with `init`.</span></span>

```cs
class Base
{
    public virtual int Property { get; init; }
}

class C1 : Base
{
    public override int Property { get; init; }
}

class C2 : Base
{
    // Error: Property must have init to override Base.Property
    public override int Property { get; set; }
}
```

<span data-ttu-id="74dc7-141">Una `interface` dichiarazione può `init` anche partecipare all'inizializzazione dello stile tramite il modello seguente:An declaration can also participate in style initialization via the following pattern:</span><span class="sxs-lookup"><span data-stu-id="74dc7-141">An `interface` declaration can also participate in `init` style initialization via the following pattern:</span></span>

```cs
interface IPerson
{
    string Name { get; init; }
}

class Init
{
    void M<T>() where T : IPerson, new()
    {
        var local = new T()
        {
            Name = "Jared"
        };
        local.Name = "Jraed"; // Error
    }
}
```

<span data-ttu-id="74dc7-142">Restrizioni di questa funzione:</span><span class="sxs-lookup"><span data-stu-id="74dc7-142">Restrictions of this feature:</span></span>
- <span data-ttu-id="74dc7-143">La `init` funzione di accesso può essere utilizzata solo sulle proprietà dell'istanzaThe accessor can only be used on instance properties</span><span class="sxs-lookup"><span data-stu-id="74dc7-143">The `init` accessor can only be used on instance properties</span></span>
- <span data-ttu-id="74dc7-144">Una proprietà non `init` può `set` contenere entrambe una funzione di accesso e</span><span class="sxs-lookup"><span data-stu-id="74dc7-144">A property cannot contain both an `init` and `set` accessor</span></span>
- <span data-ttu-id="74dc7-145">Tutti gli override di `init` una proprietà `init`devono avere se la base dispone di .</span><span class="sxs-lookup"><span data-stu-id="74dc7-145">All overrides of a property must have `init` if the base had `init`.</span></span> <span data-ttu-id="74dc7-146">Questa regola si applica anche all'implementazione dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="74dc7-146">This rule also applies to interface implementation.</span></span>

### <a name="metadata-encoding"></a><span data-ttu-id="74dc7-147">Codifica dei metadati</span><span class="sxs-lookup"><span data-stu-id="74dc7-147">Metadata encoding</span></span> 
<span data-ttu-id="74dc7-148">Le `init` funzioni di accesso alle `set` proprietà verranno generate come funzione di `IsExternalInit`accesso standard con il tipo restituito contrassegnato con un modreq di .</span><span class="sxs-lookup"><span data-stu-id="74dc7-148">Property `init` accessors will be emitted as a standard `set` accessor with the return type marked with a modreq of `IsExternalInit`.</span></span> <span data-ttu-id="74dc7-149">Questo è un nuovo tipo che avrà la seguente definizione:</span><span class="sxs-lookup"><span data-stu-id="74dc7-149">This is a new type which will have the following definition:</span></span>

```cs
namespace System.Runtime.CompilerServices
{
    public sealed class IsExternalInit
    {
    }
}
```

<span data-ttu-id="74dc7-150">Il compilatore corrisponderà al tipo in base al nome completo.</span><span class="sxs-lookup"><span data-stu-id="74dc7-150">The compiler will match the type by full name.</span></span> <span data-ttu-id="74dc7-151">Non è necessario che venga visualizzato nella libreria principale.</span><span class="sxs-lookup"><span data-stu-id="74dc7-151">There is no requirement that it appear in the core library.</span></span> <span data-ttu-id="74dc7-152">Se sono presenti più tipi con questo nome, il compilatore legherà break nel seguente ordine:</span><span class="sxs-lookup"><span data-stu-id="74dc7-152">If there are multiple types by this name then the compiler will tie break in the following order:</span></span>

1. <span data-ttu-id="74dc7-153">Quello definito nel progetto in fase di compilazione</span><span class="sxs-lookup"><span data-stu-id="74dc7-153">The one defined in the project being compiled</span></span>
1. <span data-ttu-id="74dc7-154">Quello definito in corelib</span><span class="sxs-lookup"><span data-stu-id="74dc7-154">The one defined in corelib</span></span>

<span data-ttu-id="74dc7-155">Se nessuno di questi esiste, verrà generato un errore di ambiguità del tipo.</span><span class="sxs-lookup"><span data-stu-id="74dc7-155">If neither of these exist then a type ambiguity error will be issued.</span></span>

<span data-ttu-id="74dc7-156">Il design `IsExternalInit` per è più coperto in [questo numero](https://github.com/dotnet/runtime/issues/34978)</span><span class="sxs-lookup"><span data-stu-id="74dc7-156">The design for `IsExternalInit` is futher covered in [this issue](https://github.com/dotnet/runtime/issues/34978)</span></span>

## <a name="questions"></a><span data-ttu-id="74dc7-157">Domande</span><span class="sxs-lookup"><span data-stu-id="74dc7-157">Questions</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="74dc7-158">Modifiche di rilievo</span><span class="sxs-lookup"><span data-stu-id="74dc7-158">Breaking changes</span></span>
<span data-ttu-id="74dc7-159">Uno dei principali punti cardine di come questa funzione è codificata scenderà alla seguente domanda:</span><span class="sxs-lookup"><span data-stu-id="74dc7-159">One of the main pivot points in how this feature is encoded will come down to the following question:</span></span> 

> <span data-ttu-id="74dc7-160">Si tratta di una `init` modifica `set`di rottura binaria per sostituire con ?</span><span class="sxs-lookup"><span data-stu-id="74dc7-160">Is it a binary breaking change to replace `init` with `set`?</span></span>

<span data-ttu-id="74dc7-161">La `init` sostituzione `set` con e rendendo così una proprietà completamente scrivibile non è mai una modifica di rilievo di origine in una proprietà non virtuale.</span><span class="sxs-lookup"><span data-stu-id="74dc7-161">Replacing `init` with `set` and thus making a property fully writable is never a source breaking change on a non-virtual property.</span></span> <span data-ttu-id="74dc7-162">Espande semplicemente il set di scenari in cui è possibile scrivere la proprietà.</span><span class="sxs-lookup"><span data-stu-id="74dc7-162">It simply expands the set of scenarios where the property can be written.</span></span> <span data-ttu-id="74dc7-163">L'unico comportamento in questione è se questo rimane o meno un cambiamento di rottura binaria.</span><span class="sxs-lookup"><span data-stu-id="74dc7-163">The only behavior in question is whether or not this remains a binary breaking change.</span></span>

<span data-ttu-id="74dc7-164">Se vogliamo fare il `init` `set` cambiamento di una fonte e binario modifica compatibile allora ci costringerà la nostra mano sul modreq vs.</span><span class="sxs-lookup"><span data-stu-id="74dc7-164">If we want to make the change of `init` to `set` a source and binary compatible change then it will force our hand on the modreq vs. attributes decision below because it will rule out modreqs as a soulution.</span></span> <span data-ttu-id="74dc7-165">Se d'altra parte questo è visto come un non-interessante allora questo renderà il modreq vs. decisione attributo meno impatto.</span><span class="sxs-lookup"><span data-stu-id="74dc7-165">If on the other hand this is seen as a non-interesting then this will make the modreq vs. attribute decision less impactful.</span></span>

<span data-ttu-id="74dc7-166">**Risoluzione** Questo scenario non è considerato convincente da LDM.</span><span class="sxs-lookup"><span data-stu-id="74dc7-166">**Resolution** This scenario is not seen as compelling by LDM.</span></span>

### <a name="modreqs-vs-attributes"></a><span data-ttu-id="74dc7-167">Modreqs e attributi</span><span class="sxs-lookup"><span data-stu-id="74dc7-167">Modreqs vs. attributes</span></span>
<span data-ttu-id="74dc7-168">La strategia `init` di generazione per le funzioni di accesso alle proprietà deve scegliere tra l'utilizzo di attributi o modreq quando si emette durante i metadati.</span><span class="sxs-lookup"><span data-stu-id="74dc7-168">The emit strategy for `init` property accessors must choose between using attributes or modreqs when emitting during metadata.</span></span> <span data-ttu-id="74dc7-169">Questi hanno diversi compromessi che devono essere considerati.</span><span class="sxs-lookup"><span data-stu-id="74dc7-169">These have different trade offs that need to be considered.</span></span>

<span data-ttu-id="74dc7-170">L'aggiunta di una funzione di accesso set di proprietà con una dichiarazione modreq significa che i compilatori compatibili con CLI ignoreranno la funzione di accesso a meno che non comprenda il modreq.</span><span class="sxs-lookup"><span data-stu-id="74dc7-170">Annotating a property set accessor with a modreq declaration means CLI compliant compilers will ignore the accessor unless it understands the modreq.</span></span> <span data-ttu-id="74dc7-171">Ciò significa che `init` solo i compilatori consapevoli di leggerà il membro.</span><span class="sxs-lookup"><span data-stu-id="74dc7-171">That means only compilers aware of `init` will read the member.</span></span> <span data-ttu-id="74dc7-172">I compilatori `init` che non `set` sono a conoscenza ignoreranno il membro e pertanto non considereranno accidentalmente la proprietà come lettura/scrittura.</span><span class="sxs-lookup"><span data-stu-id="74dc7-172">Compilers unaware of `init` will ignore the `set` member and hence will not accidentally treat the property as read / write.</span></span> 

<span data-ttu-id="74dc7-173">Lo svantaggio di modreq diventa `init` una parte `set` della firma binaria della funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="74dc7-173">The downside of modreq is `init` becomes a part of the binary signature of the `set` accessor.</span></span> <span data-ttu-id="74dc7-174">L'aggiunta `init` o la rimozione interromperà la compatibilità binaria dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="74dc7-174">Adding or removing `init` will break binary compatbility of the application.</span></span>

<span data-ttu-id="74dc7-175">L'utilizzo di `set` attributi per annotare la funzione di accesso significa che solo i compilatori che comprendono l'attributo sapranno limitare l'accesso ad esso.</span><span class="sxs-lookup"><span data-stu-id="74dc7-175">Using attributes to annotate the `set` accessor means that only compilers which understand the attribute will know to limit access to it.</span></span> <span data-ttu-id="74dc7-176">Un compilatore `init` inconsapevole verrà visualizzato come una semplice proprietà di lettura/scrittura e consentirà l'accesso.</span><span class="sxs-lookup"><span data-stu-id="74dc7-176">A compiler unaware of `init` will see it as a simple read / write property and allow access.</span></span>

<span data-ttu-id="74dc7-177">Questo sarebbe apparentemente significato questa decisione è una scelta tra la sicurezza supplementare a scapito della compatibilità binaria.</span><span class="sxs-lookup"><span data-stu-id="74dc7-177">This would seemingly mean this decision is a choice between extra safety at the expense of binary compatibility.</span></span> <span data-ttu-id="74dc7-178">Scavare un po 'la sicurezza in più non è esattamente quello che sembra.</span><span class="sxs-lookup"><span data-stu-id="74dc7-178">Digging in a bit the extra safety is not exactly what it seems.</span></span> <span data-ttu-id="74dc7-179">Non proteggerà dalle seguenti circostanze:</span><span class="sxs-lookup"><span data-stu-id="74dc7-179">It will not protect against the following circumstances:</span></span>

1. <span data-ttu-id="74dc7-180">Riflessione `public` sui membri</span><span class="sxs-lookup"><span data-stu-id="74dc7-180">Reflection over `public` members</span></span>
1. <span data-ttu-id="74dc7-181">L'uso di`dynamic`</span><span class="sxs-lookup"><span data-stu-id="74dc7-181">The use of `dynamic`</span></span> 
1. <span data-ttu-id="74dc7-182">Compilatori che non riconoscono modreqs</span><span class="sxs-lookup"><span data-stu-id="74dc7-182">Compilers that don't recognize modreqs</span></span>

<span data-ttu-id="74dc7-183">Va anche considerato che, una volta completate le regole `init` di verifica IL per .NET 5, sarà una di queste regole.</span><span class="sxs-lookup"><span data-stu-id="74dc7-183">It should also be considered that, when we complete the IL verification rules for .NET 5, `init` will be one of those rules.</span></span> <span data-ttu-id="74dc7-184">Ciò significa che l'applicazione aggiuntiva sarà ottenuta dalla semplice verifica dei compilatori che emettono IL verificabile.</span><span class="sxs-lookup"><span data-stu-id="74dc7-184">That means extra enforcement will be gained from simply verifying compilers emitting verifiable IL.</span></span>

<span data-ttu-id="74dc7-185">I linguaggi principali per .NET (C , F e VB) `init` verranno tutti aggiornati per riconoscere queste funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="74dc7-185">The primary languages for .NET (C#, F# and VB) will all be updated to recognize these `init` accessors.</span></span> <span data-ttu-id="74dc7-186">Quindi l'unico scenario realistico qui è `init` quando un compilatore c '9 emette proprietà e sono visti da un vecchio set di strumenti come C , VB 15, ecc ... Il linguaggio C. 8.</span><span class="sxs-lookup"><span data-stu-id="74dc7-186">Hence the only realistic scenario here is when a C# 9 compiler emits `init` properties and they are seen by an older toolset such as C# 8, VB 15, etc ... C# 8.</span></span> <span data-ttu-id="74dc7-187">Questo è il compromesso da considerare e pesare contro la compatibilità binaria.</span><span class="sxs-lookup"><span data-stu-id="74dc7-187">That is the trade off to consider and weigh against binary compatibility.</span></span>

<span data-ttu-id="74dc7-188">**Nota:** Questa discussione si applica principalmente ai membri, non ai campi.</span><span class="sxs-lookup"><span data-stu-id="74dc7-188">**Note** This discussion primarily applies to members only, not to fields.</span></span> <span data-ttu-id="74dc7-189">Mentre `init` i campi sono stati rifiutati da LDM sono ancora interessanti da considerare per la discussione modreq vs attributo.</span><span class="sxs-lookup"><span data-stu-id="74dc7-189">While `init` fields were rejected by LDM they are still interesting to consider for the modreq vs. attribute discussion.</span></span> <span data-ttu-id="74dc7-190">La `init` caratteristica per i campi è `readonly`un rilassamento della restrizione esistente di .</span><span class="sxs-lookup"><span data-stu-id="74dc7-190">The `init` feature for fields is a relaxation of the existing restriction of `readonly`.</span></span> <span data-ttu-id="74dc7-191">Ciò significa che se `readonly` emettiamo i campi come : un attributo non vi è `readonly`alcun rischio di precedenti compilatori uso improprio del campo perché sarebbero già riconoscere .</span><span class="sxs-lookup"><span data-stu-id="74dc7-191">That means if we emit the fields as `readonly` + an attribute there is no risk of older compilers mis-using the field because they would already recognize `readonly`.</span></span> <span data-ttu-id="74dc7-192">Quindi l'utilizzo di un modreq qui non aggiunge alcuna protezione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="74dc7-192">Hence using a modreq here doesn't add any extra protection.</span></span>

<span data-ttu-id="74dc7-193">**Risoluzione** La funzione utilizzerà un modreq `init` per codificare il setter della proprietà.</span><span class="sxs-lookup"><span data-stu-id="74dc7-193">**Resolution** The feature will use a modreq to encode the property `init` setter.</span></span> <span data-ttu-id="74dc7-194">I fattori convincente erano (in nessun ordine particolare):</span><span class="sxs-lookup"><span data-stu-id="74dc7-194">The compelling factors were (in no particular order):</span></span>

* <span data-ttu-id="74dc7-195">Desiderio di scoraggiare i `init` compilatori meno recenti dalla violazione della semantica</span><span class="sxs-lookup"><span data-stu-id="74dc7-195">Desire to discourage older compilers from violating `init` semantics</span></span>
* <span data-ttu-id="74dc7-196">Desiderio di effettuare `init` l'aggiunta o la rimozione in una `virtual` dichiarazione o `interface` sia una modifica di interruzione di origine e binaria.</span><span class="sxs-lookup"><span data-stu-id="74dc7-196">Desire to make adding or removing `init` in a `virtual` declaration or `interface` both a source and binary breaking change.</span></span>

<span data-ttu-id="74dc7-197">Dato non c'era anche `init` alcun supporto significativo per la rimozione di essere una modifica binaria compatibile ha fatto la scelta di utilizzare modreq dritto in avanti.</span><span class="sxs-lookup"><span data-stu-id="74dc7-197">Given there was also no significant support for removing `init` to be a binary compatible change it made the choice of using modreq straight forward.</span></span>

### <a name="init-vs-initonly"></a><span data-ttu-id="74dc7-198">init vs.</span><span class="sxs-lookup"><span data-stu-id="74dc7-198">init vs. initonly</span></span>
<span data-ttu-id="74dc7-199">Ci sono state tre forme di sintassi che hanno ottenuto una considerazione significativa durante la nostra riunione LDM:</span><span class="sxs-lookup"><span data-stu-id="74dc7-199">There were three syntax forms which got significant consideration during our LDM meeting:</span></span>

```cs
// 1. Use init 
int Option1 { get; init; }
// 2. Use init set
int Option2 { get; init set; }
// 3. Use initonly
int Option3 { get; initonly; }
```

<span data-ttu-id="74dc7-200">**Risoluzione** Non c'era nessuna sintassi che era schiacciante favorito in LDM.</span><span class="sxs-lookup"><span data-stu-id="74dc7-200">**Resolution** There was no syntax which was overwhelmingly favored in LDM.</span></span>

<span data-ttu-id="74dc7-201">Un punto che ha ottenuto un'attenzione significativa è `init` stato come la scelta della sintassi avrebbe influito sulla nostra capacità di fare i membri come una caratteristica generale in futuro.</span><span class="sxs-lookup"><span data-stu-id="74dc7-201">One point which got significant attention was how the choice of syntax would impact our ability to do `init` members as a general feature in the future.</span></span>
<span data-ttu-id="74dc7-202">Scegliere l'opzione 1 significherebbe che sarebbe difficile definire `init` `get` una proprietà che abbia un metodo di stile in futuro.</span><span class="sxs-lookup"><span data-stu-id="74dc7-202">Choosing option 1 would mean that it would be difficult to define a property which had a `init` style `get` method in the future.</span></span> <span data-ttu-id="74dc7-203">Alla fine si è deciso che se `init` abbiamo deciso di `init` andare avanti con i membri generali in futuro `init set`potremmo consentire di essere un modificatore nella lista delle funzioni di accesso di proprietà, nonché una breve mano per .</span><span class="sxs-lookup"><span data-stu-id="74dc7-203">Eventually it was decided that if we decided to go forward with general `init` members in future we could allow `init` to be a modifier in the property accessor list as well as a short hand for `init set`.</span></span> <span data-ttu-id="74dc7-204">Essenzialmente le due dichiarazioni seguenti sarebbero identiche.</span><span class="sxs-lookup"><span data-stu-id="74dc7-204">Essentially the following two declarations would be identical.</span></span>

```cs
int Property1 { get; init; }
int Property1 { get; init set; }
```

<span data-ttu-id="74dc7-205">È stata presa la `init` decisione di procedere con come funzione di accesso autonoma nell'elenco delle finestre di accesso alle proprietà.</span><span class="sxs-lookup"><span data-stu-id="74dc7-205">The decision was made to move forward with `init` as a standalone accessor in the property accessor list.</span></span>

### <a name="warn-on-failed-init"></a><span data-ttu-id="74dc7-206">Avvisa in caso di mancato init</span><span class="sxs-lookup"><span data-stu-id="74dc7-206">Warn on failed init</span></span>
<span data-ttu-id="74dc7-207">Si consideri lo scenario seguente.</span><span class="sxs-lookup"><span data-stu-id="74dc7-207">Consider the following scenario.</span></span> <span data-ttu-id="74dc7-208">Un tipo dichiara `init` un unico membro che non è impostato nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="74dc7-208">A type declares an `init` only member which is not set in the constructor.</span></span> <span data-ttu-id="74dc7-209">Il codice che costruisce l'oggetto deve ricevere un avviso se non è stato possibile inizializzare il valore?</span><span class="sxs-lookup"><span data-stu-id="74dc7-209">Should the code which constructs the object get a warning if they failed to initialize the value?</span></span>

<span data-ttu-id="74dc7-210">A quel punto è chiaro che il campo non sarà mai impostato e quindi `private` ha molte somiglianze con l'avviso intorno a non riuscire a inizializzare i dati.</span><span class="sxs-lookup"><span data-stu-id="74dc7-210">At that point it is clear the field will never be set and hence has a lot of similarities with the warning around failing to initialize `private` data.</span></span> <span data-ttu-id="74dc7-211">Quindi un avvertimento sarebbe apparentemente avere qualche valore qui?</span><span class="sxs-lookup"><span data-stu-id="74dc7-211">Hence a warning would seemingly have some value here?</span></span>

<span data-ttu-id="74dc7-212">Ci sono aspetti negativi significativi di questo avviso però:</span><span class="sxs-lookup"><span data-stu-id="74dc7-212">There are significant downsides to this warning though:</span></span>
1. <span data-ttu-id="74dc7-213">Si complica la storia di `readonly` `init`compatibilità di passare a .</span><span class="sxs-lookup"><span data-stu-id="74dc7-213">It complicates the compatibility story of changing `readonly` to `init`.</span></span> 
1. <span data-ttu-id="74dc7-214">Richiede di portare metadati aggiuntivi per indicare i membri che devono essere inizializzati dal chiamante.</span><span class="sxs-lookup"><span data-stu-id="74dc7-214">It requires carrying additional metadata around to denote the members which are required to be initialized by the caller.</span></span>

<span data-ttu-id="74dc7-215">Inoltre, se crediamo che ci sia valore qui nello scenario generale di costringere i creatori di oggetti per essere avvertiti / error'd su campi specifici, allora questo probabilmente ha senso come una caratteristica generale.</span><span class="sxs-lookup"><span data-stu-id="74dc7-215">Further if we believe there is value here in the overall scenario of forcing object creators to be warned / error'd about specific fields then this likely makes sense as a general feature.</span></span> <span data-ttu-id="74dc7-216">Non vi è alcun motivo `init` per cui dovrebbe essere limitato solo ai membri.</span><span class="sxs-lookup"><span data-stu-id="74dc7-216">There is no reason it should be limited to just `init` members.</span></span>

<span data-ttu-id="74dc7-217">**Risoluzione** Non ci sarà alcun `init` avviso sul consumo di campi e proprietà.</span><span class="sxs-lookup"><span data-stu-id="74dc7-217">**Resolution** There will be no warning on consumption of `init` fields and properties.</span></span>

<span data-ttu-id="74dc7-218">LDM vuole avere una discussione più ampia sull'idea di campi e proprietà obbligatori.</span><span class="sxs-lookup"><span data-stu-id="74dc7-218">LDM wants to have a broader discussion on the idea of required fields and properties.</span></span> <span data-ttu-id="74dc7-219">Questo potrebbe indurci a tornare e `init` riconsiderare la nostra posizione sui membri e la convalida.</span><span class="sxs-lookup"><span data-stu-id="74dc7-219">That may cause us to come back and reconsider our position on `init` members and validation.</span></span>

## <a name="allow-init-as-a-field-modifier"></a><span data-ttu-id="74dc7-220">Consenti init come modificatore di campo</span><span class="sxs-lookup"><span data-stu-id="74dc7-220">Allow init as a field modifier</span></span>
<span data-ttu-id="74dc7-221">Allo stesso `init` modo può servire come una funzione di accesso di proprietà potrebbe anche `init` servire come una designazione sui campi per dare loro comportamenti simili come proprietà.</span><span class="sxs-lookup"><span data-stu-id="74dc7-221">In the same way `init` can serve as a property accessor it could also serve as a designation on fields to give them similar behaviors as `init` properties.</span></span>
<span data-ttu-id="74dc7-222">Ciò consentirebbe l'assegnazione del campo prima del completamento della costruzione da parte del tipo, dei tipi derivati o degli inizializzatori di oggetto.</span><span class="sxs-lookup"><span data-stu-id="74dc7-222">That would allow for the field to be assigned before construction was complete by the type, derived types, or object initializers.</span></span>

```cs
class Student
{
    public init string FirstName;
    public init string LastName;
}

var s = new Student()
{
    FirstName = "Jarde",
    LastName = "Parsons",
}

s.FirstName = "Jared"; // Error FirstName is readonly
```

<span data-ttu-id="74dc7-223">Nei metadati questi campi verrebbero `readonly` contrassegnati allo stesso modo dei campi, `init` ma con un attributo aggiuntivo o modreq per indicare che sono campi di stile.</span><span class="sxs-lookup"><span data-stu-id="74dc7-223">In metadata these fields would be marked in the same way as `readonly` fields but with an additional attribute or modreq to indicate they are `init` style fields.</span></span> 

<span data-ttu-id="74dc7-224">**Risoluzione** LDM è d'accordo che questa proposta è valida, ma nel complesso lo scenario si sentiva disgiunto dalle proprietà.</span><span class="sxs-lookup"><span data-stu-id="74dc7-224">**Resolution** LDM agrees this proposal is sound but overall the scenario felt disjoint from properties.</span></span> <span data-ttu-id="74dc7-225">La decisione è stata `init` quella di procedere solo con le proprietà per ora.</span><span class="sxs-lookup"><span data-stu-id="74dc7-225">The decision was to proceed only with `init` properties for now.</span></span> <span data-ttu-id="74dc7-226">Questo ha un livello adeguato `init` di flessibilità `readonly` in quanto una proprietà può mutare un campo sul tipo dichiarante della proprietà.</span><span class="sxs-lookup"><span data-stu-id="74dc7-226">This has a suitable level of flexibility as an `init` property can mutate a `readonly` field on the declaring type of the property.</span></span> <span data-ttu-id="74dc7-227">Questo verrà riconsiderato se vi è un feedback significativo dei clienti che giustifica lo scenario.</span><span class="sxs-lookup"><span data-stu-id="74dc7-227">This will be reconsidered if there is significant customer feedback that justifies the scenario.</span></span>

### <a name="allow-init-as-a-type-modifier"></a><span data-ttu-id="74dc7-228">Allow init come modificatore di tipo</span><span class="sxs-lookup"><span data-stu-id="74dc7-228">Allow init as a type modifier</span></span>
<span data-ttu-id="74dc7-229">Allo stesso modo `readonly` in cui il `struct` modificatore può `readonly`essere `init` applicato a a per `struct` `class` dichiarare automaticamente tutti `init`i campi come , l'unico modificatore può essere dichiarato su o per contrassegnare automaticamente tutti i campi come .</span><span class="sxs-lookup"><span data-stu-id="74dc7-229">In the same way the `readonly` modifier can be applied to a `struct` to automatically declare all fields as `readonly`, the `init` only modifier can be declared on a `struct` or `class` to automatically mark all fields as `init`.</span></span>
<span data-ttu-id="74dc7-230">Ciò significa che le due dichiarazioni di tipo seguenti sono equivalenti:This means the following two type declarations are equivalent:</span><span class="sxs-lookup"><span data-stu-id="74dc7-230">This means the following two type declarations are equivalent:</span></span>

```cs
struct Point
{
    public init int X;
    public init int Y;
}

// vs. 

init struct Point
{
    public int X;
    public int Y;
}
```

<span data-ttu-id="74dc7-231">**Risoluzione** Questa funzione è troppo *carina* `readonly struct` qui e contrasta con la funzione su cui si basa.</span><span class="sxs-lookup"><span data-stu-id="74dc7-231">**Resolution** This feature is too *cute* here and conflicts with the `readonly struct` feature on which it is based.</span></span> <span data-ttu-id="74dc7-232">La `readonly struct` funzione è semplice `readonly` in quanto si applica a tutti i membri: campi, metodi, ecc ... La `init struct` funzionalità si applicherebbe solo alle proprietà.</span><span class="sxs-lookup"><span data-stu-id="74dc7-232">The `readonly struct` feature is simple in that it applies `readonly` to all members: fields, methods, etc ... The `init struct` feature would only apply to properties.</span></span> <span data-ttu-id="74dc7-233">Questo in realtà finisce per rendere confuso per gli utenti.</span><span class="sxs-lookup"><span data-stu-id="74dc7-233">This actually ends up making it confusing for users.</span></span> 

<span data-ttu-id="74dc7-234">Dato `init` che è valido solo su alcuni aspetti di un tipo abbiamo respinto l'idea di averlo come modificatore di tipo.</span><span class="sxs-lookup"><span data-stu-id="74dc7-234">Given that `init` is only valid on certain aspects of a type we rejected the idea of having it as a type modifier.</span></span>

## <a name="considerations"></a><span data-ttu-id="74dc7-235">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="74dc7-235">Considerations</span></span>

### <a name="compatibility"></a><span data-ttu-id="74dc7-236">Compatibilità</span><span class="sxs-lookup"><span data-stu-id="74dc7-236">Compatibility</span></span>
<span data-ttu-id="74dc7-237">La `init` funzione è progettata per `get` essere compatibile con le sole proprietà esistenti.</span><span class="sxs-lookup"><span data-stu-id="74dc7-237">The `init` feature is designed to be compatible with existing `get` only properties.</span></span> <span data-ttu-id="74dc7-238">In particolare è destinato ad essere un cambiamento `get` completamente additivo per una proprietà che è solo oggi, ma desidera più flexbile oggetto semantica di creazione.</span><span class="sxs-lookup"><span data-stu-id="74dc7-238">Specifically it is meant to be a completely additive change for a property which is `get` only today but desires more flexbile object creation semantics.</span></span>

<span data-ttu-id="74dc7-239">Si consideri ad esempio il tipo seguente:For example consider the following type:</span><span class="sxs-lookup"><span data-stu-id="74dc7-239">For example consider the following type:</span></span>

```cs
class Name
{
    public string First { get; }
    public string Last { get; }

    public Name(string first, string last)
    {
        First = first;
        Last = last;
    }
}
```

<span data-ttu-id="74dc7-240">L'aggiunta a queste proprietà è una modifica unificatore:Adding `init` to these properties is a un-breaking change:</span><span class="sxs-lookup"><span data-stu-id="74dc7-240">Adding `init` to these properties is a non-breaking change:</span></span>

```cs
class Name
{
    public string First { get; init; }
    public string Last { get; init; }

    public Name(string first, string last)
    {
        First = first;
        Last = last;
    }
}
```

### <a name="il-verification"></a><span data-ttu-id="74dc7-241">Verifica IL</span><span class="sxs-lookup"><span data-stu-id="74dc7-241">IL verification</span></span>
<span data-ttu-id="74dc7-242">Quando .NET Core decide di implementare nuovamente la verifica IL, `init` le regole dovranno essere modificate per tenere conto dei membri.</span><span class="sxs-lookup"><span data-stu-id="74dc7-242">When .NET Core decides to re-implement IL verification the rules will need to be adjusted to account for `init` members.</span></span> <span data-ttu-id="74dc7-243">Questo dovrà essere incluso nelle modifiche delle regole per `readonly` non-mutating acess ai dati.</span><span class="sxs-lookup"><span data-stu-id="74dc7-243">This will need to be included in the rule changes for non-mutating acess to `readonly` data.</span></span>

<span data-ttu-id="74dc7-244">Le regole di verifica IL dovranno essere suddivise in due parti:</span><span class="sxs-lookup"><span data-stu-id="74dc7-244">The IL verification rules will need to be broken into two parts:</span></span> 

1. <span data-ttu-id="74dc7-245">Consentire `init` ai `readonly` membri di impostare un campo.</span><span class="sxs-lookup"><span data-stu-id="74dc7-245">Allowing `init` members to set a `readonly` field.</span></span>
1. <span data-ttu-id="74dc7-246">Determinare quando `init` un membro può essere legalmente chiamato.</span><span class="sxs-lookup"><span data-stu-id="74dc7-246">Determining when an `init` member can be legally called.</span></span>

<span data-ttu-id="74dc7-247">Il primo è un semplice adeguamento alle regole esistenti.</span><span class="sxs-lookup"><span data-stu-id="74dc7-247">The first is a simple adjustment to the existing rules.</span></span> <span data-ttu-id="74dc7-248">Il verificatore IL può essere `init` insegnato a riconoscere i membri `readonly` e da lì `this` deve solo considerare un campo da impostare in tale membro.</span><span class="sxs-lookup"><span data-stu-id="74dc7-248">The IL verifier can be taught to recognize `init` members and from there it just needs to consider a `readonly` field to be settable on `this` in such a member.</span></span>

<span data-ttu-id="74dc7-249">La seconda regola è più complessa.</span><span class="sxs-lookup"><span data-stu-id="74dc7-249">The second rule is more complicated.</span></span> <span data-ttu-id="74dc7-250">Nel semplice caso degli inizializzatori di oggetto, la regola è semplice.</span><span class="sxs-lookup"><span data-stu-id="74dc7-250">In the simple case of object initializers the rule is straight forward.</span></span> <span data-ttu-id="74dc7-251">Deve essere legale `init` chiamare i membri `new` quando il risultato di un'espressione è ancora nello stack.</span><span class="sxs-lookup"><span data-stu-id="74dc7-251">It should be legal to call `init` members when the result of a `new` expression is still on the stack.</span></span> <span data-ttu-id="74dc7-252">Questo fino a quando il valore non è stato archiviato in un locale, elemento di `init` matrice o campo o passato come argomento a un altro metodo sarà comunque valido chiamare i membri.</span><span class="sxs-lookup"><span data-stu-id="74dc7-252">That is until the value has been stored in a local, array element or field or passed as an argument to another method it will still be legal to call `init` members.</span></span> <span data-ttu-id="74dc7-253">In questo modo, una `new` volta pubblicato il risultato dell'espressione in un identificatore denominato (diverso `this`da ), non sarà più valido chiamare `init` i membri.</span><span class="sxs-lookup"><span data-stu-id="74dc7-253">This ensures that once the result of the `new` expression is published to a named identifier (other than `this`) then it will no longer be legal to call `init` members.</span></span> 

<span data-ttu-id="74dc7-254">Il caso più complicato `init` però è quando `await`si combinano membri, inizializzatori di oggetto e .</span><span class="sxs-lookup"><span data-stu-id="74dc7-254">The more complicated case though is when we mix `init` members, object initializers and `await`.</span></span> <span data-ttu-id="74dc7-255">Ciò può causare l'oggetto appena creato per essere temporaneamente issato in una macchina a stati e quindi messo in un campo.</span><span class="sxs-lookup"><span data-stu-id="74dc7-255">That can cause the newly created object to be temporarily hoisted into a state machine and hence put into a field.</span></span>

```cs
var student = new Student() 
{
    Name = await SomeMethod()
};
```

<span data-ttu-id="74dc7-256">Qui il `new Student()` risultato di sarà issato in una `Name` macchina a stati come un campo prima che si verifichi il set di si verifica.</span><span class="sxs-lookup"><span data-stu-id="74dc7-256">Here the result of `new Student()` will be hoised into a state machine as a field before the set of `Name` occurs.</span></span> <span data-ttu-id="74dc7-257">Il compilatore dovrà contrassegnare tali campi issati in modo che il verificatore IL comprenda che non `init`sono accessibili all'utente e pertanto non violi la semantica prevista di .</span><span class="sxs-lookup"><span data-stu-id="74dc7-257">The compiler will need to mark such hoisted fields in a way that the IL verifier understands they're not user accessible and hence doesn't violate the intended semantics of `init`.</span></span>

### <a name="init-members"></a><span data-ttu-id="74dc7-258">membri init</span><span class="sxs-lookup"><span data-stu-id="74dc7-258">init members</span></span>
<span data-ttu-id="74dc7-259">Il `init` modificatore può essere esteso per essere applicato a tutti i membri di istanza.</span><span class="sxs-lookup"><span data-stu-id="74dc7-259">The `init` modifier could be extended to apply to all instance members.</span></span> <span data-ttu-id="74dc7-260">Ciò generalizzerebbe `init` il concetto di durante la costruzione dell'oggetto e consentirebbe `init` ai tipi di dichiarare metodi helper che potrebbero partecipare al processo di costruzione per inizializzare campi e proprietà.</span><span class="sxs-lookup"><span data-stu-id="74dc7-260">This would generalize the concept of `init` during object construction and allow types to declare helper methods that could partipate in the construction process to initialize `init` fields and properties.</span></span>

<span data-ttu-id="74dc7-261">Tali membri avrebbero tutte le richieste `init` che una funzione di accesso fa in questo disegno.</span><span class="sxs-lookup"><span data-stu-id="74dc7-261">Such members would have all the restricions that an `init` accessor does in this design.</span></span> <span data-ttu-id="74dc7-262">La necessità è discutibile però e questo può essere aggiunto in modo sicuro in una versione futura della lingua in modo compatibile.</span><span class="sxs-lookup"><span data-stu-id="74dc7-262">The need is questionable though and this can be safely added in a future version of the language in a compatible manner.</span></span>

### <a name="generate-three-accessors"></a><span data-ttu-id="74dc7-263">Generare tre funzioni di accessoGenerate three accessors</span><span class="sxs-lookup"><span data-stu-id="74dc7-263">Generate three accessors</span></span>
<span data-ttu-id="74dc7-264">Una potenziale `init` implementazione `init` delle proprietà `set`consiste nel rendere completamente separati da .</span><span class="sxs-lookup"><span data-stu-id="74dc7-264">One potential implementation of `init` properties is to make `init` completely separate from `set`.</span></span> <span data-ttu-id="74dc7-265">Ciò significa che una proprietà può potenzialmente `set` `init`avere tre diverse funzioni di accesso: `get`, e .</span><span class="sxs-lookup"><span data-stu-id="74dc7-265">That means that a property can potentially have three different accessors: `get`, `set` and `init`.</span></span>

<span data-ttu-id="74dc7-266">Questo ha il potenziale vantaggio di consentire l'uso di modreq per applicare la correttezza pur mantenendo la compatibilità binaria.</span><span class="sxs-lookup"><span data-stu-id="74dc7-266">This has the potential advantage of allowing the use of modreq to enforce correctness while maintaining binary compatibility.</span></span> <span data-ttu-id="74dc7-267">L'attuazione sarebbe approssimativamente la seguente:</span><span class="sxs-lookup"><span data-stu-id="74dc7-267">The implementation would roughly be the following:</span></span>

1. <span data-ttu-id="74dc7-268">Una `init` funzione di accesso viene sempre `set`generata se è presente un oggetto .</span><span class="sxs-lookup"><span data-stu-id="74dc7-268">An `init` accessor is always emitted if there is a `set`.</span></span> <span data-ttu-id="74dc7-269">Quando non è definito dallo sviluppatore `set`è semplicemente un riferimento a .</span><span class="sxs-lookup"><span data-stu-id="74dc7-269">When not defined by the developer it is simply a reference to `set`.</span></span> 
1. <span data-ttu-id="74dc7-270">Il set di una proprietà in un `init` inizializzatore di `set` oggetto verrà sempre utilizzato se presente, ma eseguire il rollback a se manca.</span><span class="sxs-lookup"><span data-stu-id="74dc7-270">The set of a property in an object initializer will always use `init` if present but fall back to `set` if it's missing.</span></span>

<span data-ttu-id="74dc7-271">Ciò significa che uno `init` sviluppatore può sempre eliminare in modo sicuro da una proprietà.</span><span class="sxs-lookup"><span data-stu-id="74dc7-271">This means that a developer can always safely delete `init` from a property.</span></span> 

<span data-ttu-id="74dc7-272">Lo svantaggio di questo disegno è `init` che è utile solo `set`se viene **sempre** emesso quando c'è un .</span><span class="sxs-lookup"><span data-stu-id="74dc7-272">The downside of this design is that is only useful if `init` is **always** emitted when there is a `set`.</span></span> <span data-ttu-id="74dc7-273">La lingua non può `init` sapere se è stato cancellato in passato, deve presumere che fosse e quindi il `init` deve sempre essere emesso.</span><span class="sxs-lookup"><span data-stu-id="74dc7-273">The language can't know if `init` was deleted in the past, it has to assume it was and hence the `init` must always be emitted.</span></span> <span data-ttu-id="74dc7-274">Ciò causerebbe un'espansione significativa dei metadati e semplicemente non vale il costo della compatibilità qui.</span><span class="sxs-lookup"><span data-stu-id="74dc7-274">That would cause a significant metadata expansion and is simply not worth the cost of the compatibility here.</span></span>
