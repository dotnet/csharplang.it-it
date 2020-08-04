---
ms.openlocfilehash: 0642f28e921ea224a157394226d43df2f3f81a10
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297514"
---
<a name="init-only-setters"></a><span data-ttu-id="a92b4-101">Setter solo init</span><span class="sxs-lookup"><span data-stu-id="a92b4-101">Init Only Setters</span></span>
=====

## <a name="summary"></a><span data-ttu-id="a92b4-102">Summary</span><span class="sxs-lookup"><span data-stu-id="a92b4-102">Summary</span></span>
<span data-ttu-id="a92b4-103">Questa proposta aggiunge il concetto di proprietà e indicizzatori solo init a C#.</span><span class="sxs-lookup"><span data-stu-id="a92b4-103">This proposal adds the concept of init only properties and indexers to C#.</span></span> <span data-ttu-id="a92b4-104">Queste proprietà e indicizzatori possono essere impostati al momento della creazione dell'oggetto, ma diventano efficaci `get` solo dopo il completamento della creazione dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="a92b4-104">These properties and indexers can be set at the point of object creation but become effectively `get` only once object creation has completed.</span></span>
<span data-ttu-id="a92b4-105">In questo modo è possibile disporre di un modello non modificabile molto più flessibile in C#.</span><span class="sxs-lookup"><span data-stu-id="a92b4-105">This allows for a much more flexible immutable model in C#.</span></span> 

## <a name="motivation"></a><span data-ttu-id="a92b4-106">Motivazione</span><span class="sxs-lookup"><span data-stu-id="a92b4-106">Motivation</span></span>
<span data-ttu-id="a92b4-107">I meccanismi sottostanti per la compilazione di dati non modificabili in C# non sono stati modificati dopo 1,0.</span><span class="sxs-lookup"><span data-stu-id="a92b4-107">The underlying mechanisms for building immutable data in C# haven't changed since 1.0.</span></span> <span data-ttu-id="a92b4-108">Rimangono:</span><span class="sxs-lookup"><span data-stu-id="a92b4-108">They remain:</span></span>

1. <span data-ttu-id="a92b4-109">Dichiarazione di campi come `readonly` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-109">Declaring fields as `readonly`.</span></span>
1. <span data-ttu-id="a92b4-110">Dichiarazione delle proprietà che contengono solo una `get` funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="a92b4-110">Declaring properties that contain only a `get` accessor.</span></span>

<span data-ttu-id="a92b4-111">Questi meccanismi sono efficaci per consentire la costruzione di dati non modificabili, ma lo fanno aggiungendo costi al codice standard dei tipi e scegliendo tali tipi di funzionalità come gli inizializzatori di oggetto e di raccolta.</span><span class="sxs-lookup"><span data-stu-id="a92b4-111">These mechanisms are effective at allowing the construction of immutable data but they do so by adding cost to the boilerplate code of types and opting such types out of features like object and collection initializers.</span></span> <span data-ttu-id="a92b4-112">Ciò significa che gli sviluppatori devono scegliere tra semplicità d'uso e immutabilità.</span><span class="sxs-lookup"><span data-stu-id="a92b4-112">This means developers must choose between ease of use and immutability.</span></span>

<span data-ttu-id="a92b4-113">Un oggetto non modificabile semplice `Point` , ad esempio, richiede il doppio del codice della piastra calda per supportare la costruzione, così come per dichiarare il tipo.</span><span class="sxs-lookup"><span data-stu-id="a92b4-113">A simple immutable object like `Point` requires twice as much boiler plate code to support construction as it does to declare the type.</span></span> <span data-ttu-id="a92b4-114">Maggiore è il tipo, maggiore è il costo di questa piastra caldatura:</span><span class="sxs-lookup"><span data-stu-id="a92b4-114">The bigger the type the bigger the cost of this boiler plate:</span></span>

```cs
struct Point
{
    public int X { get; }
    public int Y { get; }

    public Point(int x, int y)
    {
        this.X = x;
        this.Y = y;
    }
}
```

<span data-ttu-id="a92b4-115">La `init` funzione di accesso rende più flessibili gli oggetti non modificabili consentendo al chiamante di mutare i membri durante l'esecuzione dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="a92b4-115">The `init` accessor makes immutable objects more flexible by allowing the caller to mutate the members during the act of construction.</span></span> <span data-ttu-id="a92b4-116">Ciò significa che le proprietà non modificabili dell'oggetto possono partecipare agli inizializzatori di oggetto e pertanto elimina la necessità di tutti i tipi di standard del costruttore nel tipo.</span><span class="sxs-lookup"><span data-stu-id="a92b4-116">That means the object's immutable properties can participate in object initializers and thus removes the need for all constructor boilerplate in the type.</span></span> <span data-ttu-id="a92b4-117">Il `Point` tipo è ora semplice:</span><span class="sxs-lookup"><span data-stu-id="a92b4-117">The `Point` type is now simply:</span></span>

```cs
struct Point
{
    public int X { get; init; }
    public int Y { get; init; }
}
```

<span data-ttu-id="a92b4-118">Il consumer può quindi usare gli inizializzatori di oggetto per creare l'oggetto</span><span class="sxs-lookup"><span data-stu-id="a92b4-118">The consumer can then use object initializers to create the object</span></span>

```cs
var p = new Point() { X = 42, Y = 13 };
```

## <a name="detailed-design"></a><span data-ttu-id="a92b4-119">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="a92b4-119">Detailed Design</span></span>

### <a name="init-accessors"></a><span data-ttu-id="a92b4-120">funzioni di accesso init</span><span class="sxs-lookup"><span data-stu-id="a92b4-120">init accessors</span></span>
<span data-ttu-id="a92b4-121">Una proprietà (o un indicizzatore) solo init viene dichiarata utilizzando la `init` funzione di accesso al posto della `set` funzione di accesso:</span><span class="sxs-lookup"><span data-stu-id="a92b4-121">An init only property (or indexer) is declared by using the `init` accessor in place of the `set` accessor:</span></span>

```cs
class Student
{
    public string FirstName { get; init; }
    public string LastName { get; init; }
}
```

<span data-ttu-id="a92b4-122">Una proprietà dell'istanza contenente una `init` funzione di accesso viene considerata impostabile nelle circostanze seguenti, tranne quando in una funzione o in una lambda locale:</span><span class="sxs-lookup"><span data-stu-id="a92b4-122">An instance property containing an `init` accessor is considered settable in the following circumstances, except when in a local function or lambda:</span></span>

- <span data-ttu-id="a92b4-123">Durante un inizializzatore di oggetto</span><span class="sxs-lookup"><span data-stu-id="a92b4-123">During an object initializer</span></span>
- <span data-ttu-id="a92b4-124">Durante un `with` inizializzatore di espressione</span><span class="sxs-lookup"><span data-stu-id="a92b4-124">During a `with` expression initializer</span></span>
- <span data-ttu-id="a92b4-125">All'interno di un costruttore di istanza del tipo che lo contiene o derivato, su `this` o`base`</span><span class="sxs-lookup"><span data-stu-id="a92b4-125">Inside an instance constructor of the containing or derived type, on `this` or `base`</span></span>
- <span data-ttu-id="a92b4-126">All'interno della `init` funzione di accesso di qualsiasi proprietà, in `this` o`base`</span><span class="sxs-lookup"><span data-stu-id="a92b4-126">Inside the `init` accessor of any property, on `this` or `base`</span></span>
- <span data-ttu-id="a92b4-127">Utilizzi degli attributi interni con i parametri denominati</span><span class="sxs-lookup"><span data-stu-id="a92b4-127">Inside attribute usages with named parameters</span></span>

<span data-ttu-id="a92b4-128">Gli orari in cui `init` è possibile impostare le funzioni di accesso sono definiti collettivamente in questo documento come fase di costruzione dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="a92b4-128">The times above in which the `init` accessors are settable are collectively referred to in this document as the construction phase of the object.</span></span>

<span data-ttu-id="a92b4-129">Ciò significa che la `Student` classe può essere utilizzata nei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="a92b4-129">This means the `Student` class can be used in the following ways:</span></span>

```cs
var s = new Student()
{
    FirstName = "Jared",
    LastName = "Parosns",
};
s.LastName = "Parsons"; // Error: LastName is not settable
```

<span data-ttu-id="a92b4-130">Le regole relative alle `init` funzioni di accesso che è possibile impostare si estendono in più gerarchie di tipi.</span><span class="sxs-lookup"><span data-stu-id="a92b4-130">The rules around when `init` accessors are settable extend across type hierarchies.</span></span> <span data-ttu-id="a92b4-131">Se il membro è accessibile e l'oggetto è noto come fase di costruzione, il membro è impostabile.</span><span class="sxs-lookup"><span data-stu-id="a92b4-131">If the member is accessible and the object is known to be in the construction phase then the member is settable.</span></span> <span data-ttu-id="a92b4-132">Ciò consente in modo specifico quanto segue:</span><span class="sxs-lookup"><span data-stu-id="a92b4-132">That specifically allows for the following:</span></span>

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

<span data-ttu-id="a92b4-133">Nel punto in `init` cui viene richiamata una funzione di accesso, l'istanza è nota come fase di costruzione aperta.</span><span class="sxs-lookup"><span data-stu-id="a92b4-133">At the point a `init` accessor is invoked the instance is known to be in the open construction phase.</span></span> <span data-ttu-id="a92b4-134">Di conseguenza `init` , una funzione di accesso può eseguire le azioni seguenti oltre a ciò che può essere eseguita da una `set` funzione di accesso normale:</span><span class="sxs-lookup"><span data-stu-id="a92b4-134">Hence an `init` accessor is allowed to take the following actions in addition to what a normal `set` accessor can do:</span></span>

1. <span data-ttu-id="a92b4-135">Chiama altre `init` funzioni di accesso disponibili tramite `this` o`base`</span><span class="sxs-lookup"><span data-stu-id="a92b4-135">Call other `init` accessors available through `this` or `base`</span></span>
1. <span data-ttu-id="a92b4-136">Assegnare `readonly` campi dichiarati sullo stesso tipo tramite`this`</span><span class="sxs-lookup"><span data-stu-id="a92b4-136">Assign `readonly` fields declared on the same type through `this`</span></span>

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

<span data-ttu-id="a92b4-137">La possibilità di assegnare `readonly` campi da una `init` funzione di accesso è limitata ai campi dichiarati sullo stesso tipo della funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="a92b4-137">The ability to assign `readonly` fields from an `init` accessor is limited to those fields declared on the same type as the accessor.</span></span> <span data-ttu-id="a92b4-138">Non può essere usato per assegnare `readonly` campi in un tipo di base.</span><span class="sxs-lookup"><span data-stu-id="a92b4-138">It cannot be used to assign `readonly` fields in a base type.</span></span> <span data-ttu-id="a92b4-139">Questa regola garantisce che gli autori dei tipi rimangano in grado di controllare il comportamento di modificabilità del tipo.</span><span class="sxs-lookup"><span data-stu-id="a92b4-139">This rule ensures that type authors remain in control over the mutability behavior of their type.</span></span> <span data-ttu-id="a92b4-140">Gli sviluppatori che non desiderano utilizzare `init` non possono essere interessati da altri tipi che scelgono di eseguire questa operazione:</span><span class="sxs-lookup"><span data-stu-id="a92b4-140">Developers who do not wish to utilize `init` cannot be impacted from other types choosing to do so:</span></span>

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

<span data-ttu-id="a92b4-141">Quando `init` si usa in una proprietà virtuale, anche tutte le sostituzioni devono essere contrassegnate come `init` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-141">When `init` is used in a virtual property then all the overrides must also be marked as `init`.</span></span> <span data-ttu-id="a92b4-142">Analogamente, non è possibile eseguire l'override di una semplice `set` con `init` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-142">Likewise it is not possible to override a simple `set` with `init`.</span></span>

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

<span data-ttu-id="a92b4-143">Una `interface` dichiarazione può anche partecipare all' `init` inizializzazione dello stile tramite il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="a92b4-143">An `interface` declaration can also participate in `init` style initialization via the following pattern:</span></span>

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

<span data-ttu-id="a92b4-144">Limitazioni di questa funzionalità:</span><span class="sxs-lookup"><span data-stu-id="a92b4-144">Restrictions of this feature:</span></span>
- <span data-ttu-id="a92b4-145">La `init` funzione di accesso può essere utilizzata solo nelle proprietà dell'istanza</span><span class="sxs-lookup"><span data-stu-id="a92b4-145">The `init` accessor can only be used on instance properties</span></span>
- <span data-ttu-id="a92b4-146">Una proprietà non può contenere sia `init` una `set` funzione di accesso che</span><span class="sxs-lookup"><span data-stu-id="a92b4-146">A property cannot contain both an `init` and `set` accessor</span></span>
- <span data-ttu-id="a92b4-147">Tutte le sostituzioni di una proprietà devono avere `init` se la base era `init` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-147">All overrides of a property must have `init` if the base had `init`.</span></span> <span data-ttu-id="a92b4-148">Questa regola si applica anche all'implementazione dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="a92b4-148">This rule also applies to interface implementation.</span></span>

### <a name="metadata-encoding"></a><span data-ttu-id="a92b4-149">Codifica dei metadati</span><span class="sxs-lookup"><span data-stu-id="a92b4-149">Metadata encoding</span></span> 
<span data-ttu-id="a92b4-150">`init`Le funzioni di accesso alle proprietà verranno emesse come `set` funzione di accesso standard con il tipo restituito contrassegnato con un modreq di `IsExternalInit` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-150">Property `init` accessors will be emitted as a standard `set` accessor with the return type marked with a modreq of `IsExternalInit`.</span></span> <span data-ttu-id="a92b4-151">Si tratta di un nuovo tipo che avrà la definizione seguente:</span><span class="sxs-lookup"><span data-stu-id="a92b4-151">This is a new type which will have the following definition:</span></span>

```cs
namespace System.Runtime.CompilerServices
{
    public sealed class IsExternalInit
    {
    }
}
```

<span data-ttu-id="a92b4-152">Il compilatore corrisponderà al tipo in base al nome completo.</span><span class="sxs-lookup"><span data-stu-id="a92b4-152">The compiler will match the type by full name.</span></span> <span data-ttu-id="a92b4-153">Non è necessario che venga visualizzato nella libreria principale.</span><span class="sxs-lookup"><span data-stu-id="a92b4-153">There is no requirement that it appear in the core library.</span></span> <span data-ttu-id="a92b4-154">Se sono presenti più tipi con questo nome, il compilatore verrà vincolato nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="a92b4-154">If there are multiple types by this name then the compiler will tie break in the following order:</span></span>

1. <span data-ttu-id="a92b4-155">Quello definito nel progetto da compilare</span><span class="sxs-lookup"><span data-stu-id="a92b4-155">The one defined in the project being compiled</span></span>
1. <span data-ttu-id="a92b4-156">Quello definito in corelib</span><span class="sxs-lookup"><span data-stu-id="a92b4-156">The one defined in corelib</span></span>

<span data-ttu-id="a92b4-157">Se nessuna di queste esiste, verrà generato un errore di ambiguità del tipo.</span><span class="sxs-lookup"><span data-stu-id="a92b4-157">If neither of these exist then a type ambiguity error will be issued.</span></span>

<span data-ttu-id="a92b4-158">Il progetto per `IsExternalInit` è più trattato in [questo problema](https://github.com/dotnet/runtime/issues/34978)</span><span class="sxs-lookup"><span data-stu-id="a92b4-158">The design for `IsExternalInit` is futher covered in [this issue](https://github.com/dotnet/runtime/issues/34978)</span></span>

## <a name="questions"></a><span data-ttu-id="a92b4-159">Domande</span><span class="sxs-lookup"><span data-stu-id="a92b4-159">Questions</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="a92b4-160">Modifiche di rilievo</span><span class="sxs-lookup"><span data-stu-id="a92b4-160">Breaking changes</span></span>
<span data-ttu-id="a92b4-161">Uno dei principali punti cardine nel modo in cui questa funzionalità viene codificata dipende dalla domanda seguente:</span><span class="sxs-lookup"><span data-stu-id="a92b4-161">One of the main pivot points in how this feature is encoded will come down to the following question:</span></span> 

> <span data-ttu-id="a92b4-162">Si tratta di una modifica di rilievo binaria da sostituire `init` con `set` ?</span><span class="sxs-lookup"><span data-stu-id="a92b4-162">Is it a binary breaking change to replace `init` with `set`?</span></span>

<span data-ttu-id="a92b4-163">La sostituzione `init` di con `set` e rendendo una proprietà completamente scrivibile non è mai una modifica di rilievo di origine in una proprietà non virtuale.</span><span class="sxs-lookup"><span data-stu-id="a92b4-163">Replacing `init` with `set` and thus making a property fully writable is never a source breaking change on a non-virtual property.</span></span> <span data-ttu-id="a92b4-164">Espande semplicemente il set di scenari in cui è possibile scrivere la proprietà.</span><span class="sxs-lookup"><span data-stu-id="a92b4-164">It simply expands the set of scenarios where the property can be written.</span></span> <span data-ttu-id="a92b4-165">L'unico comportamento in questione è il fatto che rimanga una modifica di rilievo binaria.</span><span class="sxs-lookup"><span data-stu-id="a92b4-165">The only behavior in question is whether or not this remains a binary breaking change.</span></span>

<span data-ttu-id="a92b4-166">Se si vuole apportare la modifica di `init` a `set` una modifica di origine e di compatibilità binaria, l'utente forza la decisione sulla modreq rispetto alla decisione degli attributi riportata di seguito perché esclude modreqs come Soulution.</span><span class="sxs-lookup"><span data-stu-id="a92b4-166">If we want to make the change of `init` to `set` a source and binary compatible change then it will force our hand on the modreq vs. attributes decision below because it will rule out modreqs as a soulution.</span></span> <span data-ttu-id="a92b4-167">Se invece si tratta di un aspetto non interessante, questa operazione renderà la decisione di confronto tra l'modreq e l'attributo meno incisiva.</span><span class="sxs-lookup"><span data-stu-id="a92b4-167">If on the other hand this is seen as a non-interesting then this will make the modreq vs. attribute decision less impactful.</span></span>

<span data-ttu-id="a92b4-168">**Risoluzione** dei problemi Questo scenario non è considerato interessante da LDM.</span><span class="sxs-lookup"><span data-stu-id="a92b4-168">**Resolution** This scenario is not seen as compelling by LDM.</span></span>

### <a name="modreqs-vs-attributes"></a><span data-ttu-id="a92b4-169">Confronto tra Modreqs e attributi</span><span class="sxs-lookup"><span data-stu-id="a92b4-169">Modreqs vs. attributes</span></span>
<span data-ttu-id="a92b4-170">La strategia Emit per le `init` funzioni di accesso alle proprietà deve scegliere se usare gli attributi o modreqs durante la creazione durante i metadati.</span><span class="sxs-lookup"><span data-stu-id="a92b4-170">The emit strategy for `init` property accessors must choose between using attributes or modreqs when emitting during metadata.</span></span> <span data-ttu-id="a92b4-171">Si tratta di compromessi diversi che devono essere presi in considerazione.</span><span class="sxs-lookup"><span data-stu-id="a92b4-171">These have different trade offs that need to be considered.</span></span>

<span data-ttu-id="a92b4-172">Annotando una funzione di accesso set di proprietà con una dichiarazione modreq significa che i compilatori conformi CLI ignoreranno la funzione di accesso a meno che non riconosca modreq</span><span class="sxs-lookup"><span data-stu-id="a92b4-172">Annotating a property set accessor with a modreq declaration means CLI compliant compilers will ignore the accessor unless it understands the modreq.</span></span> <span data-ttu-id="a92b4-173">Ciò significa che solo i compilatori a conoscenza di `init` leggeranno il membro.</span><span class="sxs-lookup"><span data-stu-id="a92b4-173">That means only compilers aware of `init` will read the member.</span></span> <span data-ttu-id="a92b4-174">I compilatori che `init` non conoscono ignoreranno la `set` funzione di accesso e pertanto non considereranno accidentalmente la proprietà come di lettura/scrittura.</span><span class="sxs-lookup"><span data-stu-id="a92b4-174">Compilers unaware of `init` will ignore the `set` accessor and hence will not accidentally treat the property as read / write.</span></span> 

<span data-ttu-id="a92b4-175">Lo svantaggio di modreq è `init` costituito da una parte della firma binaria della `set` funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="a92b4-175">The downside of modreq is `init` becomes a part of the binary signature of the `set` accessor.</span></span> <span data-ttu-id="a92b4-176">L'aggiunta o `init` la rimozione comporterà l'eliminazione di compatbility binari dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="a92b4-176">Adding or removing `init` will break binary compatbility of the application.</span></span>

<span data-ttu-id="a92b4-177">L'uso di attributi per annotare la `set` funzione di accesso significa che solo i compilatori che comprendono l'attributo sapranno limitare l'accesso.</span><span class="sxs-lookup"><span data-stu-id="a92b4-177">Using attributes to annotate the `set` accessor means that only compilers which understand the attribute will know to limit access to it.</span></span> <span data-ttu-id="a92b4-178">Un compilatore ignaro di lo vedrà `init` come una semplice proprietà di lettura/scrittura e consentirà l'accesso.</span><span class="sxs-lookup"><span data-stu-id="a92b4-178">A compiler unaware of `init` will see it as a simple read / write property and allow access.</span></span>

<span data-ttu-id="a92b4-179">Questa decisione potrebbe essere la scelta tra una maggiore sicurezza a scapito della compatibilità binaria.</span><span class="sxs-lookup"><span data-stu-id="a92b4-179">This would seemingly mean this decision is a choice between extra safety at the expense of binary compatibility.</span></span> <span data-ttu-id="a92b4-180">L'approfondimento della protezione aggiuntiva non è esattamente quello che sembra.</span><span class="sxs-lookup"><span data-stu-id="a92b4-180">Digging in a bit the extra safety is not exactly what it seems.</span></span> <span data-ttu-id="a92b4-181">Non verrà protetta dalle seguenti circostanze:</span><span class="sxs-lookup"><span data-stu-id="a92b4-181">It will not protect against the following circumstances:</span></span>

1. <span data-ttu-id="a92b4-182">Reflection sui `public` membri</span><span class="sxs-lookup"><span data-stu-id="a92b4-182">Reflection over `public` members</span></span>
1. <span data-ttu-id="a92b4-183">Utilizzo di`dynamic`</span><span class="sxs-lookup"><span data-stu-id="a92b4-183">The use of `dynamic`</span></span> 
1. <span data-ttu-id="a92b4-184">Compilatori che non riconoscono modreqs</span><span class="sxs-lookup"><span data-stu-id="a92b4-184">Compilers that don't recognize modreqs</span></span>

<span data-ttu-id="a92b4-185">Si deve anche considerare che, quando si completano le regole di verifica IL per .NET 5, `init` sarà una di queste regole.</span><span class="sxs-lookup"><span data-stu-id="a92b4-185">It should also be considered that, when we complete the IL verification rules for .NET 5, `init` will be one of those rules.</span></span> <span data-ttu-id="a92b4-186">Ciò significa che l'imposizione aggiuntiva sarà ottenuta semplicemente verificando i compilatori che emettono IL verificabile.</span><span class="sxs-lookup"><span data-stu-id="a92b4-186">That means extra enforcement will be gained from simply verifying compilers emitting verifiable IL.</span></span>

<span data-ttu-id="a92b4-187">Le lingue primarie per .NET (C#, F # e VB) verranno tutte aggiornate per riconoscere tali `init` funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="a92b4-187">The primary languages for .NET (C#, F# and VB) will all be updated to recognize these `init` accessors.</span></span> <span data-ttu-id="a92b4-188">Di conseguenza, l'unico scenario realistico è quando un compilatore C# 9 emette `init` proprietà e vengono visualizzate da un set di strumenti precedente, ad esempio C# 8, VB 15 e così via. C# 8.</span><span class="sxs-lookup"><span data-stu-id="a92b4-188">Hence the only realistic scenario here is when a C# 9 compiler emits `init` properties and they are seen by an older toolset such as C# 8, VB 15, etc ... C# 8.</span></span> <span data-ttu-id="a92b4-189">Questo è il compromesso da considerare e da valutare la compatibilità binaria.</span><span class="sxs-lookup"><span data-stu-id="a92b4-189">That is the trade off to consider and weigh against binary compatibility.</span></span>

<span data-ttu-id="a92b4-190">**Nota** Questa discussione si applica principalmente solo ai membri e non ai campi.</span><span class="sxs-lookup"><span data-stu-id="a92b4-190">**Note** This discussion primarily applies to members only, not to fields.</span></span> <span data-ttu-id="a92b4-191">Mentre `init` i campi sono stati rifiutati da LDM, sono comunque interessanti da considerare per la discussione di modreq rispetto all'attributo.</span><span class="sxs-lookup"><span data-stu-id="a92b4-191">While `init` fields were rejected by LDM they are still interesting to consider for the modreq vs. attribute discussion.</span></span> <span data-ttu-id="a92b4-192">La `init` funzionalità per i campi è un rilassamento della restrizione esistente di `readonly` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-192">The `init` feature for fields is a relaxation of the existing restriction of `readonly`.</span></span> <span data-ttu-id="a92b4-193">Ciò significa che se i campi vengono creati come `readonly` + un attributo, non esiste alcun rischio che i compilatori meno recenti utilizzino il campo in quanto riconoscono già `readonly` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-193">That means if we emit the fields as `readonly` + an attribute there is no risk of older compilers mis-using the field because they would already recognize `readonly`.</span></span> <span data-ttu-id="a92b4-194">Quindi, l'uso di un modreq qui non aggiunge alcuna protezione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="a92b4-194">Hence using a modreq here doesn't add any extra protection.</span></span>

<span data-ttu-id="a92b4-195">**Risoluzione** dei problemi La funzionalità userà un modreq per codificare il setter della proprietà `init` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-195">**Resolution** The feature will use a modreq to encode the property `init` setter.</span></span> <span data-ttu-id="a92b4-196">I fattori interessanti sono stati (in nessun ordine particolare):</span><span class="sxs-lookup"><span data-stu-id="a92b4-196">The compelling factors were (in no particular order):</span></span>

* <span data-ttu-id="a92b4-197">Desiderio di scoraggiare i compilatori meno recenti dalla violazione della `init` semantica</span><span class="sxs-lookup"><span data-stu-id="a92b4-197">Desire to discourage older compilers from violating `init` semantics</span></span>
* <span data-ttu-id="a92b4-198">Desiderio di aggiungere o rimuovere `init` in una `virtual` dichiarazione o `interface` una modifica di rilievo di origine e binaria.</span><span class="sxs-lookup"><span data-stu-id="a92b4-198">Desire to make adding or removing `init` in a `virtual` declaration or `interface` both a source and binary breaking change.</span></span>

<span data-ttu-id="a92b4-199">Dato che non esisteva un supporto significativo per la rimozione `init` di una modifica compatibile con binario, era possibile scegliere di usare modreq in modo semplice.</span><span class="sxs-lookup"><span data-stu-id="a92b4-199">Given there was also no significant support for removing `init` to be a binary compatible change it made the choice of using modreq straight forward.</span></span>

### <a name="init-vs-initonly"></a><span data-ttu-id="a92b4-200">init rispetto a initonly</span><span class="sxs-lookup"><span data-stu-id="a92b4-200">init vs. initonly</span></span>
<span data-ttu-id="a92b4-201">Sono stati rilevati tre forme di sintassi che hanno preso in considerazione in modo significativo durante la riunione LDM:</span><span class="sxs-lookup"><span data-stu-id="a92b4-201">There were three syntax forms which got significant consideration during our LDM meeting:</span></span>

```cs
// 1. Use init 
int Option1 { get; init; }
// 2. Use init set
int Option2 { get; init set; }
// 3. Use initonly
int Option3 { get; initonly; }
```

<span data-ttu-id="a92b4-202">**Risoluzione** dei problemi Non c'era una sintassi estremamente favorita in LDM.</span><span class="sxs-lookup"><span data-stu-id="a92b4-202">**Resolution** There was no syntax which was overwhelmingly favored in LDM.</span></span>

<span data-ttu-id="a92b4-203">Un punto che ha avuto un notevole interesse è stato il modo in cui la scelta della sintassi avrà un impatto sulla possibilità di eseguire i `init` membri come funzionalità generale in futuro.</span><span class="sxs-lookup"><span data-stu-id="a92b4-203">One point which got significant attention was how the choice of syntax would impact our ability to do `init` members as a general feature in the future.</span></span>
<span data-ttu-id="a92b4-204">Se si sceglie l'opzione 1, sarebbe difficile definire una proprietà con un `init` `get` metodo di stile in futuro.</span><span class="sxs-lookup"><span data-stu-id="a92b4-204">Choosing option 1 would mean that it would be difficult to define a property which had a `init` style `get` method in the future.</span></span> <span data-ttu-id="a92b4-205">Infine, è stato deciso che se avessimo deciso di proseguire con `init` i membri generali in futuro, avremmo potuto `init` essere un modificatore nell'elenco delle funzioni di accesso alle proprietà, così come un breve termine per `init set` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-205">Eventually it was decided that if we decided to go forward with general `init` members in future we could allow `init` to be a modifier in the property accessor list as well as a short hand for `init set`.</span></span> <span data-ttu-id="a92b4-206">Essenzialmente le due dichiarazioni seguenti sarebbero identiche.</span><span class="sxs-lookup"><span data-stu-id="a92b4-206">Essentially the following two declarations would be identical.</span></span>

```cs
int Property1 { get; init; }
int Property1 { get; init set; }
```

<span data-ttu-id="a92b4-207">È stata presa la decisione di procedere con `init` una funzione di accesso autonoma nell'elenco delle funzioni di accesso alle proprietà.</span><span class="sxs-lookup"><span data-stu-id="a92b4-207">The decision was made to move forward with `init` as a standalone accessor in the property accessor list.</span></span>

### <a name="warn-on-failed-init"></a><span data-ttu-id="a92b4-208">Avvisa in caso di inizializzazione non riuscita</span><span class="sxs-lookup"><span data-stu-id="a92b4-208">Warn on failed init</span></span>
<span data-ttu-id="a92b4-209">Si consideri lo scenario seguente.</span><span class="sxs-lookup"><span data-stu-id="a92b4-209">Consider the following scenario.</span></span> <span data-ttu-id="a92b4-210">Un tipo dichiara un `init` solo membro che non è impostato nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="a92b4-210">A type declares an `init` only member which is not set in the constructor.</span></span> <span data-ttu-id="a92b4-211">Se il codice che costruisce l'oggetto riceve un avviso se non è stato possibile inizializzare il valore?</span><span class="sxs-lookup"><span data-stu-id="a92b4-211">Should the code which constructs the object get a warning if they failed to initialize the value?</span></span>

<span data-ttu-id="a92b4-212">A questo punto è chiaro che il campo non verrà mai impostato e pertanto presenta numerose analogie con l'avviso che non riesce a inizializzare i `private` dati.</span><span class="sxs-lookup"><span data-stu-id="a92b4-212">At that point it is clear the field will never be set and hence has a lot of similarities with the warning around failing to initialize `private` data.</span></span> <span data-ttu-id="a92b4-213">Quindi, un avviso potrebbe avere un valore apparentemente simile a questo?</span><span class="sxs-lookup"><span data-stu-id="a92b4-213">Hence a warning would seemingly have some value here?</span></span>

<span data-ttu-id="a92b4-214">Questo avviso tuttavia presenta svantaggi significativi:</span><span class="sxs-lookup"><span data-stu-id="a92b4-214">There are significant downsides to this warning though:</span></span>
1. <span data-ttu-id="a92b4-215">Complica la storia di compatibilità della modifica `readonly` a `init` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-215">It complicates the compatibility story of changing `readonly` to `init`.</span></span> 
1. <span data-ttu-id="a92b4-216">Per indicare i membri che devono essere inizializzati dal chiamante, è necessario portare metadati aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="a92b4-216">It requires carrying additional metadata around to denote the members which are required to be initialized by the caller.</span></span>

<span data-ttu-id="a92b4-217">Inoltre, se si ritiene che sia presente un valore nello scenario generale di forzare l'avviso o l'errore degli autori di oggetti in base a campi specifici, è probabile che questa funzionalità abbia un aspetto significativo.</span><span class="sxs-lookup"><span data-stu-id="a92b4-217">Further if we believe there is value here in the overall scenario of forcing object creators to be warned / error'd about specific fields then this likely makes sense as a general feature.</span></span> <span data-ttu-id="a92b4-218">Non esiste alcun motivo per cui deve essere limitato solo ai `init` membri.</span><span class="sxs-lookup"><span data-stu-id="a92b4-218">There is no reason it should be limited to just `init` members.</span></span>

<span data-ttu-id="a92b4-219">**Risoluzione** dei problemi Non verrà visualizzato alcun avviso relativo all'utilizzo di `init` campi e proprietà.</span><span class="sxs-lookup"><span data-stu-id="a92b4-219">**Resolution** There will be no warning on consumption of `init` fields and properties.</span></span>

<span data-ttu-id="a92b4-220">LDM desidera una discussione più ampia sull'idea dei campi e delle proprietà obbligatori.</span><span class="sxs-lookup"><span data-stu-id="a92b4-220">LDM wants to have a broader discussion on the idea of required fields and properties.</span></span> <span data-ttu-id="a92b4-221">Questo potrebbe causare la restituzione e la rivalutazione della posizione dei `init` membri e della convalida.</span><span class="sxs-lookup"><span data-stu-id="a92b4-221">That may cause us to come back and reconsider our position on `init` members and validation.</span></span>

## <a name="allow-init-as-a-field-modifier"></a><span data-ttu-id="a92b4-222">Consenti init come modificatore di campo</span><span class="sxs-lookup"><span data-stu-id="a92b4-222">Allow init as a field modifier</span></span>
<span data-ttu-id="a92b4-223">Allo stesso modo può fungere `init` da funzione di accesso alla proprietà, può anche fungere da designazione sui campi per fornire comportamenti simili come `init` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="a92b4-223">In the same way `init` can serve as a property accessor it could also serve as a designation on fields to give them similar behaviors as `init` properties.</span></span>
<span data-ttu-id="a92b4-224">Ciò consente di assegnare il campo prima che la costruzione sia stata completata da tipo, tipi derivati o inizializzatori di oggetto.</span><span class="sxs-lookup"><span data-stu-id="a92b4-224">That would allow for the field to be assigned before construction was complete by the type, derived types, or object initializers.</span></span>

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

<span data-ttu-id="a92b4-225">Nei metadati questi campi verrebbero contrassegnati nello stesso modo dei campi, `readonly` ma con un attributo o modreq aggiuntivo per indicare che si tratta di `init` campi di stile.</span><span class="sxs-lookup"><span data-stu-id="a92b4-225">In metadata these fields would be marked in the same way as `readonly` fields but with an additional attribute or modreq to indicate they are `init` style fields.</span></span> 

<span data-ttu-id="a92b4-226">**Risoluzione** dei problemi LDM accetta questa proposta è un suono, ma in generale lo scenario si è risentito disgiunto dalle proprietà.</span><span class="sxs-lookup"><span data-stu-id="a92b4-226">**Resolution** LDM agrees this proposal is sound but overall the scenario felt disjoint from properties.</span></span> <span data-ttu-id="a92b4-227">La decisione consisteva nel procedere solo con le `init` proprietà per il momento.</span><span class="sxs-lookup"><span data-stu-id="a92b4-227">The decision was to proceed only with `init` properties for now.</span></span> <span data-ttu-id="a92b4-228">Si tratta di un livello di flessibilità adatto perché una `init` proprietà può mutare un `readonly` campo nel tipo dichiarante della proprietà.</span><span class="sxs-lookup"><span data-stu-id="a92b4-228">This has a suitable level of flexibility as an `init` property can mutate a `readonly` field on the declaring type of the property.</span></span> <span data-ttu-id="a92b4-229">Questo verrà ripreso in considerazione se è presente un feedback significativo per i clienti che giustifica lo scenario.</span><span class="sxs-lookup"><span data-stu-id="a92b4-229">This will be reconsidered if there is significant customer feedback that justifies the scenario.</span></span>

### <a name="allow-init-as-a-type-modifier"></a><span data-ttu-id="a92b4-230">Consenti init come modificatore di tipo</span><span class="sxs-lookup"><span data-stu-id="a92b4-230">Allow init as a type modifier</span></span>
<span data-ttu-id="a92b4-231">Allo stesso modo, il `readonly` modificatore può essere applicato a un `struct` per dichiarare automaticamente tutti i campi come `readonly` , l' `init` unico modificatore può essere dichiarato in un `struct` o `class` per contrassegnare automaticamente tutti i campi come `init` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-231">In the same way the `readonly` modifier can be applied to a `struct` to automatically declare all fields as `readonly`, the `init` only modifier can be declared on a `struct` or `class` to automatically mark all fields as `init`.</span></span>
<span data-ttu-id="a92b4-232">Ciò significa che le due dichiarazioni di tipo seguenti sono equivalenti:</span><span class="sxs-lookup"><span data-stu-id="a92b4-232">This means the following two type declarations are equivalent:</span></span>

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

<span data-ttu-id="a92b4-233">**Risoluzione** dei problemi Questa funzionalità è troppo *carina* ed è in conflitto con la `readonly struct` funzionalità su cui è basata.</span><span class="sxs-lookup"><span data-stu-id="a92b4-233">**Resolution** This feature is too *cute* here and conflicts with the `readonly struct` feature on which it is based.</span></span> <span data-ttu-id="a92b4-234">La `readonly struct` funzionalità è semplice in quanto si applica `readonly` a tutti i membri: campi, metodi e così via. La `init struct` funzionalità si applica solo alle proprietà.</span><span class="sxs-lookup"><span data-stu-id="a92b4-234">The `readonly struct` feature is simple in that it applies `readonly` to all members: fields, methods, etc ... The `init struct` feature would only apply to properties.</span></span> <span data-ttu-id="a92b4-235">Questa operazione sta effettivamente creando confusione per gli utenti.</span><span class="sxs-lookup"><span data-stu-id="a92b4-235">This actually ends up making it confusing for users.</span></span> 

<span data-ttu-id="a92b4-236">Dato che `init` è valido solo per determinati aspetti di un tipo, si è rifiutato il concetto di come modificatore di tipo.</span><span class="sxs-lookup"><span data-stu-id="a92b4-236">Given that `init` is only valid on certain aspects of a type we rejected the idea of having it as a type modifier.</span></span>

## <a name="considerations"></a><span data-ttu-id="a92b4-237">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="a92b4-237">Considerations</span></span>

### <a name="compatibility"></a><span data-ttu-id="a92b4-238">Compatibilità</span><span class="sxs-lookup"><span data-stu-id="a92b4-238">Compatibility</span></span>
<span data-ttu-id="a92b4-239">La `init` funzionalità è progettata per essere compatibile con le `get` proprietà esistenti.</span><span class="sxs-lookup"><span data-stu-id="a92b4-239">The `init` feature is designed to be compatible with existing `get` only properties.</span></span> <span data-ttu-id="a92b4-240">In particolare, è concepita come una modifica completamente additiva per una proprietà che è `get` solo oggi ma che desidera una semantica di creazione di oggetti Flexbile.</span><span class="sxs-lookup"><span data-stu-id="a92b4-240">Specifically it is meant to be a completely additive change for a property which is `get` only today but desires more flexbile object creation semantics.</span></span>

<span data-ttu-id="a92b4-241">Si consideri ad esempio il tipo seguente:</span><span class="sxs-lookup"><span data-stu-id="a92b4-241">For example consider the following type:</span></span>

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

<span data-ttu-id="a92b4-242">`init`L'aggiunta a queste proprietà è una modifica senza interruzioni:</span><span class="sxs-lookup"><span data-stu-id="a92b4-242">Adding `init` to these properties is a non-breaking change:</span></span>

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

### <a name="il-verification"></a><span data-ttu-id="a92b4-243">Verifica IL</span><span class="sxs-lookup"><span data-stu-id="a92b4-243">IL verification</span></span>
<span data-ttu-id="a92b4-244">Quando .NET Core decide di implementare nuovamente la verifica il, le regole dovranno essere modificate per tenere conto dei `init` membri.</span><span class="sxs-lookup"><span data-stu-id="a92b4-244">When .NET Core decides to re-implement IL verification the rules will need to be adjusted to account for `init` members.</span></span> <span data-ttu-id="a92b4-245">Questa operazione deve essere inclusa nelle modifiche regolate per le voci ACE non in mutazione ai `readonly` dati.</span><span class="sxs-lookup"><span data-stu-id="a92b4-245">This will need to be included in the rule changes for non-mutating acess to `readonly` data.</span></span>

<span data-ttu-id="a92b4-246">Le regole di verifica il devono essere suddivise in due parti:</span><span class="sxs-lookup"><span data-stu-id="a92b4-246">The IL verification rules will need to be broken into two parts:</span></span> 

1. <span data-ttu-id="a92b4-247">Consentire `init` ai membri di impostare un `readonly` campo.</span><span class="sxs-lookup"><span data-stu-id="a92b4-247">Allowing `init` members to set a `readonly` field.</span></span>
1. <span data-ttu-id="a92b4-248">Determinazione del momento in cui un `init` membro può essere chiamato legalmente.</span><span class="sxs-lookup"><span data-stu-id="a92b4-248">Determining when an `init` member can be legally called.</span></span>

<span data-ttu-id="a92b4-249">Il primo è una semplice regolazione delle regole esistenti.</span><span class="sxs-lookup"><span data-stu-id="a92b4-249">The first is a simple adjustment to the existing rules.</span></span> <span data-ttu-id="a92b4-250">Il verificatore di IL può essere insegnato a riconoscere `init` i membri e da qui è sufficiente prendere `readonly` in considerazione un campo da impostare `this` in un membro di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="a92b4-250">The IL verifier can be taught to recognize `init` members and from there it just needs to consider a `readonly` field to be settable on `this` in such a member.</span></span>

<span data-ttu-id="a92b4-251">La seconda regola è più complessa.</span><span class="sxs-lookup"><span data-stu-id="a92b4-251">The second rule is more complicated.</span></span> <span data-ttu-id="a92b4-252">Nel caso semplice degli inizializzatori di oggetto la regola è diretta.</span><span class="sxs-lookup"><span data-stu-id="a92b4-252">In the simple case of object initializers the rule is straight forward.</span></span> <span data-ttu-id="a92b4-253">È consigliabile chiamare `init` i membri quando il risultato di un' `new` espressione è ancora nello stack.</span><span class="sxs-lookup"><span data-stu-id="a92b4-253">It should be legal to call `init` members when the result of a `new` expression is still on the stack.</span></span> <span data-ttu-id="a92b4-254">Fino a quando il valore non è stato archiviato in un campo o elemento di matrice locale o passato come argomento a un altro metodo, sarà ancora possibile chiamare `init` i membri.</span><span class="sxs-lookup"><span data-stu-id="a92b4-254">That is until the value has been stored in a local, array element or field or passed as an argument to another method it will still be legal to call `init` members.</span></span> <span data-ttu-id="a92b4-255">In questo modo si garantisce che, una volta che il risultato dell' `new` espressione è stato pubblicato in un identificatore denominato (diverso da `this` ), non sarà più consentito chiamare `init` i membri.</span><span class="sxs-lookup"><span data-stu-id="a92b4-255">This ensures that once the result of the `new` expression is published to a named identifier (other than `this`) then it will no longer be legal to call `init` members.</span></span> 

<span data-ttu-id="a92b4-256">Il caso più complicato consiste tuttavia nel combinare `init` membri, inizializzatori di oggetto e `await` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-256">The more complicated case though is when we mix `init` members, object initializers and `await`.</span></span> <span data-ttu-id="a92b4-257">In questo modo, l'oggetto appena creato verrà temporaneamente sottoposto a sollevamento in una macchina a Stati e quindi inserito in un campo.</span><span class="sxs-lookup"><span data-stu-id="a92b4-257">That can cause the newly created object to be temporarily hoisted into a state machine and hence put into a field.</span></span>

```cs
var student = new Student() 
{
    Name = await SomeMethod()
};
```

<span data-ttu-id="a92b4-258">Qui il risultato di `new Student()` sarà hoised in una macchina a stati come un campo prima del set di `Name` si verifica.</span><span class="sxs-lookup"><span data-stu-id="a92b4-258">Here the result of `new Student()` will be hoised into a state machine as a field before the set of `Name` occurs.</span></span> <span data-ttu-id="a92b4-259">Il compilatore dovrà contrassegnare tali campi in modo che il verificatore il riconosca che non sono accessibili dall'utente e pertanto non viola la semantica desiderata di `init` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-259">The compiler will need to mark such hoisted fields in a way that the IL verifier understands they're not user accessible and hence doesn't violate the intended semantics of `init`.</span></span>

### <a name="init-members"></a><span data-ttu-id="a92b4-260">membri init</span><span class="sxs-lookup"><span data-stu-id="a92b4-260">init members</span></span>
<span data-ttu-id="a92b4-261">Il `init` modificatore può essere esteso per essere applicato a tutti i membri di istanza.</span><span class="sxs-lookup"><span data-stu-id="a92b4-261">The `init` modifier could be extended to apply to all instance members.</span></span> <span data-ttu-id="a92b4-262">In questo modo si generalizza il concetto di `init` durante la costruzione dell'oggetto e i tipi consentono di dichiarare metodi helper che potrebbero partecipare nel processo di costruzione per inizializzare `init` campi e proprietà.</span><span class="sxs-lookup"><span data-stu-id="a92b4-262">This would generalize the concept of `init` during object construction and allow types to declare helper methods that could partipate in the construction process to initialize `init` fields and properties.</span></span>

<span data-ttu-id="a92b4-263">Tali membri avranno tutti i restricions di una `init` funzione di accesso in questa progettazione.</span><span class="sxs-lookup"><span data-stu-id="a92b4-263">Such members would have all the restricions that an `init` accessor does in this design.</span></span> <span data-ttu-id="a92b4-264">La necessità è comunque discutibile e questo può essere aggiunto in modo sicuro in una versione futura del linguaggio in modo compatibile.</span><span class="sxs-lookup"><span data-stu-id="a92b4-264">The need is questionable though and this can be safely added in a future version of the language in a compatible manner.</span></span>

### <a name="generate-three-accessors"></a><span data-ttu-id="a92b4-265">Genera tre funzioni di accesso</span><span class="sxs-lookup"><span data-stu-id="a92b4-265">Generate three accessors</span></span>
<span data-ttu-id="a92b4-266">Una delle possibili implementazioni delle `init` proprietà è quella di eseguire `init` completamente la separazione da `set` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-266">One potential implementation of `init` properties is to make `init` completely separate from `set`.</span></span> <span data-ttu-id="a92b4-267">Ciò significa che una proprietà può avere potenzialmente tre funzioni di accesso diverse: `get` , `set` e `init` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-267">That means that a property can potentially have three different accessors: `get`, `set` and `init`.</span></span>

<span data-ttu-id="a92b4-268">Questo offre il vantaggio potenziale di consentire l'uso di modreq per applicare la correttezza mantenendo la compatibilità binaria.</span><span class="sxs-lookup"><span data-stu-id="a92b4-268">This has the potential advantage of allowing the use of modreq to enforce correctness while maintaining binary compatibility.</span></span> <span data-ttu-id="a92b4-269">L'implementazione sarà approssimativamente la seguente:</span><span class="sxs-lookup"><span data-stu-id="a92b4-269">The implementation would roughly be the following:</span></span>

1. <span data-ttu-id="a92b4-270">Una `init` funzione di accesso viene sempre emessa se è presente un oggetto `set` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-270">An `init` accessor is always emitted if there is a `set`.</span></span> <span data-ttu-id="a92b4-271">Quando non è definito dallo sviluppatore, è semplicemente un riferimento a `set` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-271">When not defined by the developer it is simply a reference to `set`.</span></span> 
1. <span data-ttu-id="a92b4-272">Il set di una proprietà in un inizializzatore di oggetto utilizzerà sempre `init` se presente, ma eseguirà il fallback a `set` se manca.</span><span class="sxs-lookup"><span data-stu-id="a92b4-272">The set of a property in an object initializer will always use `init` if present but fall back to `set` if it's missing.</span></span>

<span data-ttu-id="a92b4-273">Ciò significa che uno sviluppatore può sempre eliminare in modo sicuro `init` da una proprietà.</span><span class="sxs-lookup"><span data-stu-id="a92b4-273">This means that a developer can always safely delete `init` from a property.</span></span> 

<span data-ttu-id="a92b4-274">Lo svantaggio di questa progettazione è che è utile solo se `init` viene **sempre** emesso quando esiste una `set` .</span><span class="sxs-lookup"><span data-stu-id="a92b4-274">The downside of this design is that is only useful if `init` is **always** emitted when there is a `set`.</span></span> <span data-ttu-id="a92b4-275">Il linguaggio non può `init` essere in grado di stabilire se è stato eliminato in passato, deve presupporre che sia stato e pertanto `init` deve essere sempre emesso.</span><span class="sxs-lookup"><span data-stu-id="a92b4-275">The language can't know if `init` was deleted in the past, it has to assume it was and hence the `init` must always be emitted.</span></span> <span data-ttu-id="a92b4-276">Questo potrebbe causare un'espansione significativa dei metadati e non vale la pena per il costo della compatibilità.</span><span class="sxs-lookup"><span data-stu-id="a92b4-276">That would cause a significant metadata expansion and is simply not worth the cost of the compatibility here.</span></span>