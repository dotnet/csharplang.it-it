---
ms.openlocfilehash: aaf11b188c4622d50e86f757c24eb5cd934f8909
ms.sourcegitcommit: 5b6f535beed62156b5239035e1829b001886b0b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102622485"
---
# <a name="parameterless-struct-constructors"></a><span data-ttu-id="39738-101">Costruttori struct senza parametri</span><span class="sxs-lookup"><span data-stu-id="39738-101">Parameterless struct constructors</span></span>

## <a name="summary"></a><span data-ttu-id="39738-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="39738-102">Summary</span></span>
<span data-ttu-id="39738-103">Supporta costruttori senza parametri e inizializzatori di campo di istanza per i tipi struct.</span><span class="sxs-lookup"><span data-stu-id="39738-103">Support parameterless constructors and instance field initializers for struct types.</span></span>

## <a name="motivation"></a><span data-ttu-id="39738-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="39738-104">Motivation</span></span>
<span data-ttu-id="39738-105">I costruttori espliciti senza parametri offrono un maggiore controllo sulle istanze con costruzione minima del tipo di struct.</span><span class="sxs-lookup"><span data-stu-id="39738-105">Explicit parameterless constructors would give more control over minimally constructed instances of the struct type.</span></span>
<span data-ttu-id="39738-106">Gli inizializzatori di campo di istanza consentono l'inizializzazione semplificata tra più costruttori.</span><span class="sxs-lookup"><span data-stu-id="39738-106">Instance field initializers would allow simplified initialization across multiple constructors.</span></span>
<span data-ttu-id="39738-107">Insieme si chiuderebbe uno spazio ovvio tra `struct` le `class` dichiarazioni e.</span><span class="sxs-lookup"><span data-stu-id="39738-107">Together these would close an obvious gap between `struct` and `class` declarations.</span></span>

<span data-ttu-id="39738-108">Il supporto per gli inizializzatori di campo consente inoltre di inizializzare i campi nelle `record struct` dichiarazioni senza implementare in modo esplicito il costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="39738-108">Support for field initializers would also allow initialization of fields in `record struct` declarations without explicitly implementing the primary constructor.</span></span>
```csharp
record struct Person(string Name)
{
    public object Id { get; init; } = GetNextId();
}
```

<span data-ttu-id="39738-109">Se gli inizializzatori di campo struct sono supportati per i costruttori con parametri, sembra naturale estenderlo anche a costruttori senza parametri.</span><span class="sxs-lookup"><span data-stu-id="39738-109">If struct field initializers are supported for constructors with parameters, it seems natural to extend that to parameterless constructors as well.</span></span>
```csharp
record struct Person()
{
    public string Name { get; init; }
    public object Id { get; init; } = GetNextId();
}
```

## <a name="proposal"></a><span data-ttu-id="39738-110">Proposal</span><span class="sxs-lookup"><span data-stu-id="39738-110">Proposal</span></span>

### <a name="instance-field-initializers"></a><span data-ttu-id="39738-111">Inizializzatori di campo di istanza</span><span class="sxs-lookup"><span data-stu-id="39738-111">Instance field initializers</span></span>
<span data-ttu-id="39738-112">Le dichiarazioni dei campi di istanza per uno struct possono includere inizializzatori.</span><span class="sxs-lookup"><span data-stu-id="39738-112">Instance field declarations for a struct may include initializers.</span></span>

<span data-ttu-id="39738-113">Come per gli [inizializzatori di campo di classe](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-field-initialization):</span><span class="sxs-lookup"><span data-stu-id="39738-113">As with [class field initializers](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-field-initialization):</span></span>
> <span data-ttu-id="39738-114">Un inizializzatore di variabile per un campo di istanza non può fare riferimento all'istanza in fase di creazione.</span><span class="sxs-lookup"><span data-stu-id="39738-114">A variable initializer for an instance field cannot reference the instance being created.</span></span> 

### <a name="constructors"></a><span data-ttu-id="39738-115">Costruttori</span><span class="sxs-lookup"><span data-stu-id="39738-115">Constructors</span></span>
<span data-ttu-id="39738-116">Uno struct può dichiarare un costruttore di istanza senza parametri.</span><span class="sxs-lookup"><span data-stu-id="39738-116">A struct may declare a parameterless instance constructor.</span></span>

<span data-ttu-id="39738-117">Un costruttore di istanza senza parametri è valido per tutti i tipi di struct, tra cui `struct` ,, `readonly struct` `ref struct` e `record struct` .</span><span class="sxs-lookup"><span data-stu-id="39738-117">A parameterless instance constructor is valid for all struct kinds including `struct`, `readonly struct`, `ref struct`, and `record struct`.</span></span>

<span data-ttu-id="39738-118">Se lo struct non dichiara un costruttore di istanza senza parametri e lo struct non ha campi con inizializzatori di variabile, lo struct (vedere [costruttori struct](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors))...</span><span class="sxs-lookup"><span data-stu-id="39738-118">If the struct does not declare a parameterless instance constructor, and the struct has no fields with variable initializers, the struct (see [struct constructors](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)) ...</span></span>
> <span data-ttu-id="39738-119">dispone implicitamente di un costruttore di istanza senza parametri che restituisce sempre il valore risultante dall'impostazione di tutti i campi di tipo valore sul valore predefinito e di tutti i campi di tipo riferimento su null.</span><span class="sxs-lookup"><span data-stu-id="39738-119">implicitly has a parameterless instance constructor which always returns the value that results from setting all value type fields to their default value and all reference type fields to null.</span></span>

<span data-ttu-id="39738-120">Se lo struct non dichiara un costruttore di istanza senza parametri e lo struct contiene inizializzatori di campo, `public` viene sintetizzato un costruttore di istanza senza parametri.</span><span class="sxs-lookup"><span data-stu-id="39738-120">If the struct does not declare a parameterless instance constructor, and the struct has field initializers, a `public` parameterless instance constructor is synthesized.</span></span>
<span data-ttu-id="39738-121">Il costruttore senza parametri viene sintetizzato anche se tutti i valori degli inizializzatori sono pari a zero.</span><span class="sxs-lookup"><span data-stu-id="39738-121">The parameterless constructor is synthesized even if all initializer values are zeros.</span></span>

### <a name="modifiers"></a><span data-ttu-id="39738-122">Modificatori</span><span class="sxs-lookup"><span data-stu-id="39738-122">Modifiers</span></span>
<span data-ttu-id="39738-123">Un costruttore di istanza senza parametri può essere meno accessibile dello struct che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="39738-123">A parameterless instance constructor may be less accessible than the containing struct.</span></span>
```csharp
public struct NoConstructor { }
public struct PublicConstructor { public PublicConstructor() { } }
public struct InternalConstructor { internal InternalConstructor() { } }
public struct PrivateConstructor { private PrivateConstructor() { } }
```

<span data-ttu-id="39738-124">Lo stesso set di modificatori può essere usato per i costruttori senza parametri come altri costruttori di istanza: `extern` , e `unsafe` .</span><span class="sxs-lookup"><span data-stu-id="39738-124">The same set of modifiers can be used for parameterless constructors as other instance constructors: `extern`, and `unsafe`.</span></span>

<span data-ttu-id="39738-125">I costruttori non possono essere `partial` .</span><span class="sxs-lookup"><span data-stu-id="39738-125">Constructors cannot be `partial`.</span></span>

### <a name="executing-field-initializers"></a><span data-ttu-id="39738-126">Esecuzione di inizializzatori di campo</span><span class="sxs-lookup"><span data-stu-id="39738-126">Executing field initializers</span></span>
<span data-ttu-id="39738-127">L'esecuzione degli inizializzatori di campo dell'istanza struct corrisponde all'esecuzione degli [inizializzatori di campo della classe](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-variable-initializers):</span><span class="sxs-lookup"><span data-stu-id="39738-127">Execution of struct instance field initializers matches execution of [class field initializers](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-variable-initializers):</span></span>
> <span data-ttu-id="39738-128">Quando un costruttore di istanza non dispone di un inizializzatore di costruttore,... il costruttore esegue in modo implicito le inizializzazioni specificate dal _variable_initializers_ dei campi dell'istanza....</span><span class="sxs-lookup"><span data-stu-id="39738-128">When an instance constructor has no constructor initializer, ... that constructor implicitly performs the initializations specified by the _variable_initializers_ of the instance fields ... .</span></span> <span data-ttu-id="39738-129">Corrisponde a una sequenza di assegnazioni che vengono eseguite immediatamente alla voce del costruttore....</span><span class="sxs-lookup"><span data-stu-id="39738-129">This corresponds to a sequence of assignments that are executed immediately upon entry to the constructor ... .</span></span> <span data-ttu-id="39738-130">Gli inizializzatori di variabile vengono eseguiti nell'ordine testuale in cui vengono visualizzati nel... Dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="39738-130">The variable initializers are executed in the textual order in which they appear in the ... declaration.</span></span>

### <a name="definite-assignment"></a><span data-ttu-id="39738-131">Assegnazione definita</span><span class="sxs-lookup"><span data-stu-id="39738-131">Definite assignment</span></span>
<span data-ttu-id="39738-132">I campi dell'istanza devono essere assegnati in modo sicuro nei costruttori di istanze struct che non dispongono di un `this()` inizializzatore (vedere [costruttori struct](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)).</span><span class="sxs-lookup"><span data-stu-id="39738-132">Instance fields must be definitely assigned in struct instance constructors that do not have a `this()` initializer (see [struct constructors](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)).</span></span>

<span data-ttu-id="39738-133">L'assegnazione definitiva dei campi di istanza è obbligatoria anche nei costruttori espliciti senza parametri.</span><span class="sxs-lookup"><span data-stu-id="39738-133">Definite assignment of instance fields is required within explicit parameterless constructors as well.</span></span>
```csharp
struct S1
{
    int x = 1;
    object y;
    S() { } // error: field 'y' must be assigned
}

struct S2
{
    int x = 2;
    object y;
    S() : this(null) { }        // ok
    S(object y) { this.y = y; } // ok
}
```

<span data-ttu-id="39738-134">L' _assegnazione definitiva dei campi di istanza struct è obbligatoria nei costruttori senza parametri sintetizzati?_ 
 In _tal caso, se i campi di istanza hanno inizializzatori, tutti i campi dell'istanza devono avere inizializzatori._</span><span class="sxs-lookup"><span data-stu-id="39738-134">_Should definite assignment of struct instance fields be required within synthesized parameterless constructors?_
_If so, then if any instance fields have initializers, all instance fields must have initializers._</span></span>
```csharp
struct S0
{
    int x = 0;
    object y;
    // ok?
}
```

<span data-ttu-id="39738-135">Se i campi non vengono inizializzati in modo esplicito, il costruttore dovrà azzerare l'istanza prima di eseguire qualsiasi inizializzatore di campo.</span><span class="sxs-lookup"><span data-stu-id="39738-135">If fields are not explicitly initialized, the constructor will need to zero the instance before executing any field initializers.</span></span>
```
.class S0 extends System.ValueType
{
    .field int32 x
    .field object y
    .method public instance void .ctor()
    {
        ldarg.0
        initobj S0
        ldarg.0
        ldc.i4.0
        stfld int32 S0::x
        ret
    }
}
```

### <a name="no-base-initializer"></a><span data-ttu-id="39738-136">Nessun `base()` inizializzatore</span><span class="sxs-lookup"><span data-stu-id="39738-136">No `base()` initializer</span></span>
<span data-ttu-id="39738-137">Un `base()` inizializzatore non è consentito nei costruttori struct.</span><span class="sxs-lookup"><span data-stu-id="39738-137">A `base()` initializer is disallowed in struct constructors.</span></span>

<span data-ttu-id="39738-138">Il compilatore non genererà una chiamata al costruttore di base `System.ValueType` da alcun costruttore di istanza struct, inclusi i costruttori senza parametri espliciti e sintetizzati.</span><span class="sxs-lookup"><span data-stu-id="39738-138">The compiler will not emit a call to the base `System.ValueType` constructor from any struct instance constructors including explicit and synthesized parameterless constructors.</span></span>

### <a name="fields"></a><span data-ttu-id="39738-139">Campi</span><span class="sxs-lookup"><span data-stu-id="39738-139">Fields</span></span>
<span data-ttu-id="39738-140">Il costruttore senza parametri sintetizzato non chiamerà alcun campo anziché chiamare i costruttori senza parametri per i tipi di campo.</span><span class="sxs-lookup"><span data-stu-id="39738-140">The synthesized parameterless constructor will zero fields rather than calling any parameterless constructors for the field types.</span></span>

<span data-ttu-id="39738-141">_Se il compilatore segnala un avviso quando i costruttori dei campi vengono ignorati,_</span><span class="sxs-lookup"><span data-stu-id="39738-141">_Should the compiler report a warning when constructors for fields are ignored?_</span></span>
```csharp
struct S0
{
    public S0() { }
}

struct S1
{
    S0 F; // S0::.ctor() ignored
}

struct S<T> where T : struct
{
    T F; // constructor ignored
}
```

### <a name="default-expression"></a><span data-ttu-id="39738-142">Espressione `default`</span><span class="sxs-lookup"><span data-stu-id="39738-142">`default` expression</span></span>
<span data-ttu-id="39738-143">`default` Ignora il costruttore senza parametri e genera un'istanza azzerata.</span><span class="sxs-lookup"><span data-stu-id="39738-143">`default` ignores the parameterless constructor and generates a zeroed instance.</span></span>

<span data-ttu-id="39738-144">_Se il compilatore segnala un avviso quando un costruttore viene ignorato,_</span><span class="sxs-lookup"><span data-stu-id="39738-144">_Should the compiler report a warning when a constructor is ignored?_</span></span>
```csharp
_ = default(NoConstructor);      // ok
_ = default(PublicConstructor);  // ok: constructor ignored
_ = default(PrivateConstructor); // ok: constructor ignored
```

### <a name="object-creation"></a><span data-ttu-id="39738-145">Creazione di oggetti</span><span class="sxs-lookup"><span data-stu-id="39738-145">Object creation</span></span>
<span data-ttu-id="39738-146">Le espressioni di creazione di oggetti richiedono che il costruttore senza parametri sia accessibile se definito.</span><span class="sxs-lookup"><span data-stu-id="39738-146">Object creation expressions require the parameterless constructor to be accessible if defined.</span></span>
<span data-ttu-id="39738-147">Il costruttore senza parametri viene richiamato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="39738-147">The parameterless constructor is invoked explicitly.</span></span>

<span data-ttu-id="39738-148">Si _tratta di una modifica di rilievo se il tipo struct con costruttore senza parametri è da un assembly esistente._ 
 _Se il compilatore segnala un avviso anziché un errore `new()` se il costruttore è inaccessibile ed emette `initobj` , per la compatibilità?_</span><span class="sxs-lookup"><span data-stu-id="39738-148">_This is a breaking change if the struct type with parameterless constructor is from an existing assembly._
_Should the compiler report a warning rather than an error for `new()` if the constructor is inaccessible, and emit `initobj`, for compatability?_</span></span>
```csharp
_ = new NoConstructor();      // ok: initobj NoConstructor
_ = new PublicConstructor();  // ok: call PublicConstructor::.ctor()
_ = new PrivateConstructor(); // error: 'PrivateConstructor..ctor()' is inaccessible
```

### <a name="uninitialized-values"></a><span data-ttu-id="39738-149">Valori non inizializzati</span><span class="sxs-lookup"><span data-stu-id="39738-149">Uninitialized values</span></span>
<span data-ttu-id="39738-150">Un campo locale o di un tipo struct non inizializzato in modo esplicito viene azzerato.</span><span class="sxs-lookup"><span data-stu-id="39738-150">A local or field of a struct type that is not explicitly initialized is zeroed.</span></span>
<span data-ttu-id="39738-151">Il compilatore segnala un errore di assegnazione definito per uno struct non inizializzato che non è vuoto.</span><span class="sxs-lookup"><span data-stu-id="39738-151">The compiler reports a definite assignment error for an uninitialized struct that is not empty.</span></span> 
```csharp
NoConstructor s1;
PublicConstructor s2;
s1.ToString(); // error: use of unassigned local (unless type is empty)
s2.ToString(); // error: use of unassigned local (unless type is empty)
```

### <a name="array-allocation"></a><span data-ttu-id="39738-152">Allocazione di matrici</span><span class="sxs-lookup"><span data-stu-id="39738-152">Array allocation</span></span>
<span data-ttu-id="39738-153">L'allocazione di matrici ignora qualsiasi costruttore senza parametri e genera elementi azzerati.</span><span class="sxs-lookup"><span data-stu-id="39738-153">Array allocation ignores any parameterless constructor and generates zeroed elements.</span></span>

<span data-ttu-id="39738-154">_Il compilatore avvisa che il costruttore senza parametri viene ignorato? Come evitare un avviso di questo tipo?_</span><span class="sxs-lookup"><span data-stu-id="39738-154">_Should the compiler warn that the parameterless constructor is ignored? How would such a warning be avoided?_</span></span>
```csharp
_ = new NoConstructor[1];      // ok
_ = new PublicConstructor[1];  // ok: constructor ignored
_ = new PrivateConstructor[1]; // ok: constructor ignored
```

### <a name="parameter-default-values"></a><span data-ttu-id="39738-155">Valori predefiniti dei parametri</span><span class="sxs-lookup"><span data-stu-id="39738-155">Parameter default values</span></span>
<span data-ttu-id="39738-156">Non è possibile usare costruttori senza parametri come valori predefiniti dei parametri.</span><span class="sxs-lookup"><span data-stu-id="39738-156">Parameterless constructors cannot be used as parameter default values.</span></span>

<span data-ttu-id="39738-157">Si _tratta di una modifica di rilievo se il tipo struct con costruttore senza parametri è da un assembly esistente._ 
 _Se il compilatore segnala un avviso anziché un errore `new()` se il costruttore è inaccessibile ed emette `default` , per la compatibilità?_</span><span class="sxs-lookup"><span data-stu-id="39738-157">_This is a breaking change if the struct type with parameterless constructor is from an existing assembly._
_Should the compiler report a warning rather than an error for `new()` if the constructor is inaccessible, and emit `default`, for compatability?_</span></span>
```csharp
static void F1(NoConstructor s1 = new()) { }     // ok
static void F2(PublicConstructor s1 = new()) { } // error: default value must be constant
```

### <a name="constraints"></a><span data-ttu-id="39738-158">Vincoli</span><span class="sxs-lookup"><span data-stu-id="39738-158">Constraints</span></span>
<span data-ttu-id="39738-159">Il `new()` vincolo del parametro di tipo richiede che il costruttore senza parametri sia definito (vedere la pagina relativa ai `public` [vincoli soddisfacenti](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)).</span><span class="sxs-lookup"><span data-stu-id="39738-159">The `new()` type parameter constraint requires the parameterless constructor to be `public` if defined (see [satisfying constraints](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)).</span></span>
```csharp
static T CreateNew<T>() where T : new() => new T();

_ = CreateNew<NoConstructor>();       // ok
_ = CreateNew<PublicConstructor>();   // ok
_ = CreateNew<InternalConstructor>(); // error: 'InternalConstructor..ctor()' is not public
```
<span data-ttu-id="39738-160">_Se il compilatore segnala un avviso anziché un errore quando si sostituisce uno struct con un costruttore non pubblico per un parametro di tipo con un `new()` vincolo, per la compatibilità e per evitare di presumere che sia effettivamente stata creata un'istanza del tipo._</span><span class="sxs-lookup"><span data-stu-id="39738-160">_Should the compiler report a warning rather than an error when substituting a struct with a non-public constructor for a type parameter with a `new()` constraint, for compatability and to avoid assuming the type is actually instantiated?_</span></span>

<span data-ttu-id="39738-161">`new T()` viene generato come una chiamata a `System.Activator.CreateInstance<T>()` e il compilatore presuppone che l'implementazione di `CreateInstance<T>()` richiami il `public` costruttore senza parametri se definito.</span><span class="sxs-lookup"><span data-stu-id="39738-161">`new T()` is emitted as a call to `System.Activator.CreateInstance<T>()`, and the compiler assumes the implementation of `CreateInstance<T>()` invokes the `public` parameterless constructor if defined.</span></span>

<span data-ttu-id="39738-162">_Con .NET Framework, `Activator.CreateInstance<T>()` richiama il costruttore senza parametri se il vincolo è `where T : new()` ma sembra ignorare il costruttore senza parametri se il vincolo è `where T : struct` ._</span><span class="sxs-lookup"><span data-stu-id="39738-162">_With .NET Framework, `Activator.CreateInstance<T>()` invokes the parameterless constructor if the constraint is `where T : new()` but appears to ignore the parameterless constructor if the constraint is `where T : struct`._</span></span>

<span data-ttu-id="39738-163">Il controllo dei vincoli del parametro di tipo presenta un divario perché il `new()` vincolo è soddisfatto da un parametro di tipo con un vincolo (vedere la pagina relativa ai `struct` vincoli di [soddisfazione](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)).</span><span class="sxs-lookup"><span data-stu-id="39738-163">There is a gap in type parameter constraint checking because the `new()` constraint is satisfied by a type parameter with a `struct` constraint (see [satisfying constraints](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)).</span></span>

<span data-ttu-id="39738-164">Di conseguenza, il compilatore può eseguire le operazioni seguenti, ma `Activator.CreateInstance<InternalConstructor>()` avrà esito negativo in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="39738-164">As a result, the following will be allowed by the compiler but `Activator.CreateInstance<InternalConstructor>()` will fail at runtime.</span></span>
<span data-ttu-id="39738-165">Il problema non è stato introdotto da questa proposta, tuttavia, il problema esiste con C# 9 se il tipo struct con costruttore senza parametri inaccessibile è dei metadati.</span><span class="sxs-lookup"><span data-stu-id="39738-165">The issue is not introduced by this proposal though - the issue exists with C# 9 if the struct type with inaccessible parameterless constructor is from metadata.</span></span>
```csharp
static T CreateNew<T>() where T : new() => new T();
static T CreateStruct<T>() where T : struct => CreateNew<T>();

_ = CreateStruct<InternalConstructor>(); // compiles; 'MissingMethodException' at runtime
```

### <a name="optional-parameters"></a><span data-ttu-id="39738-166">Parametri facoltativi</span><span class="sxs-lookup"><span data-stu-id="39738-166">Optional parameters</span></span>
<span data-ttu-id="39738-167">I costruttori con parametri facoltativi non sono considerati costruttori senza parametri.</span><span class="sxs-lookup"><span data-stu-id="39738-167">Constructors with optional parameters are not considered parameterless constructors.</span></span> <span data-ttu-id="39738-168">Questo comportamento è invariato rispetto alle versioni precedenti del compilatore.</span><span class="sxs-lookup"><span data-stu-id="39738-168">This behavior is unchanged from earlier compiler versions.</span></span>
```csharp
struct S1 { public S1(string s = "") { } }
struct S2 { public S2(params object[] args) { } }

_ = new S1(); // ok: ignores constructor
_ = new S2(); // ok: ignores constructor
```

### <a name="metadata"></a><span data-ttu-id="39738-169">Metadati</span><span class="sxs-lookup"><span data-stu-id="39738-169">Metadata</span></span>
<span data-ttu-id="39738-170">I costruttori di istanze struct senza parametri espliciti e sintetizzati verranno emessi nei metadati.</span><span class="sxs-lookup"><span data-stu-id="39738-170">Explicit and synthesized parameterless struct instance constructors will be emitted to metadata.</span></span>

<span data-ttu-id="39738-171">I costruttori di istanze struct senza parametri verranno importati dai metadati indipendentemente dall'accessibilità.</span><span class="sxs-lookup"><span data-stu-id="39738-171">Parameterless struct instance constructors will be imported from metadata regardless of accessibility.</span></span>
<span data-ttu-id="39738-172">_Potrebbe trattarsi di una modifica di rilievo per i consumer di assembly esistenti con struct con costruttori senza parametri privati se vengono segnalati errori o avvisi aggiuntivi._</span><span class="sxs-lookup"><span data-stu-id="39738-172">_This might be a breaking change for consumers of existing assemblies with structs with private parameterless constructors if additional errors or warnings are reported._</span></span>

<span data-ttu-id="39738-173">I costruttori di istanze struct senza parametri verranno emessi in assembly ref indipendentemente dall'accessibilità per consentire ai consumer di distinguere tra nessun costruttore senza parametri un costruttore inaccessibile.</span><span class="sxs-lookup"><span data-stu-id="39738-173">Parameterless struct instance constructors will be emitted to ref assemblies regardless of accessibility to allow consumers to differentiate between no parameterless constructor an inaccessible constructor.</span></span>

## <a name="see-also"></a><span data-ttu-id="39738-174">Vedi anche</span><span class="sxs-lookup"><span data-stu-id="39738-174">See also</span></span>

- https://github.com/dotnet/roslyn/issues/1029

## <a name="design-meetings"></a><span data-ttu-id="39738-175">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="39738-175">Design meetings</span></span>

- https://github.com/dotnet/csharplang/blob/master/meetings/2021/LDM-2021-01-27.md#field-initializers
