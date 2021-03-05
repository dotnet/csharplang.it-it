---
ms.openlocfilehash: d5eeb2501d4e99136911cb1fa2153ff7057432b3
ms.sourcegitcommit: 547b1d0991d7557cd658937d1d730a1c91ff1571
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102236377"
---
# <a name="parameterless-struct-constructors"></a><span data-ttu-id="e7489-101">Costruttori struct senza parametri</span><span class="sxs-lookup"><span data-stu-id="e7489-101">Parameterless struct constructors</span></span>

## <a name="summary"></a><span data-ttu-id="e7489-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="e7489-102">Summary</span></span>

<span data-ttu-id="e7489-103">Supporta costruttori senza parametri e inizializzatori di campo di istanza per i tipi struct.</span><span class="sxs-lookup"><span data-stu-id="e7489-103">Support parameterless constructors and instance field initializers for struct types.</span></span>

## <a name="proposal"></a><span data-ttu-id="e7489-104">Proposal</span><span class="sxs-lookup"><span data-stu-id="e7489-104">Proposal</span></span>

### <a name="instance-field-initializers"></a><span data-ttu-id="e7489-105">Inizializzatori di campo di istanza</span><span class="sxs-lookup"><span data-stu-id="e7489-105">Instance field initializers</span></span>
<span data-ttu-id="e7489-106">Le dichiarazioni dei campi di istanza per uno struct possono includere inizializzatori.</span><span class="sxs-lookup"><span data-stu-id="e7489-106">Instance field declarations for a struct may include initializers.</span></span>

<span data-ttu-id="e7489-107">Come per gli [inizializzatori di campo di classe](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-field-initialization):</span><span class="sxs-lookup"><span data-stu-id="e7489-107">As with [class field initializers](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-field-initialization):</span></span>
> <span data-ttu-id="e7489-108">Un inizializzatore di variabile per un campo di istanza non può fare riferimento all'istanza in fase di creazione.</span><span class="sxs-lookup"><span data-stu-id="e7489-108">A variable initializer for an instance field cannot reference the instance being created.</span></span> 

### <a name="constructors"></a><span data-ttu-id="e7489-109">Costruttori</span><span class="sxs-lookup"><span data-stu-id="e7489-109">Constructors</span></span>
<span data-ttu-id="e7489-110">Uno struct può dichiarare un costruttore di istanza senza parametri.</span><span class="sxs-lookup"><span data-stu-id="e7489-110">A struct may declare a parameterless instance constructor.</span></span>
<span data-ttu-id="e7489-111">Un costruttore di istanza senza parametri è valido per tutti i tipi di struct, tra cui `struct` ,, `readonly struct` `ref struct` e `record struct` .</span><span class="sxs-lookup"><span data-stu-id="e7489-111">A parameterless instance constructor is valid for all struct kinds including `struct`, `readonly struct`, `ref struct`, and `record struct`.</span></span>

<span data-ttu-id="e7489-112">Se lo struct non dichiara un costruttore di istanza senza parametri e lo struct non ha campi con inizializzatori di variabile, lo struct (vedere [costruttori struct](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors))...</span><span class="sxs-lookup"><span data-stu-id="e7489-112">If the struct does not declare a parameterless instance constructor, and the struct has no fields with variable initializers, the struct (see [struct constructors](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)) ...</span></span>
> <span data-ttu-id="e7489-113">dispone implicitamente di un costruttore di istanza senza parametri che restituisce sempre il valore risultante dall'impostazione di tutti i campi di tipo valore sul valore predefinito e di tutti i campi di tipo riferimento su null.</span><span class="sxs-lookup"><span data-stu-id="e7489-113">implicitly has a parameterless instance constructor which always returns the value that results from setting all value type fields to their default value and all reference type fields to null.</span></span>

<span data-ttu-id="e7489-114">Se lo struct non dichiara un costruttore di istanza senza parametri e lo struct contiene inizializzatori di campo, `public` viene sintetizzato un costruttore di istanza senza parametri.</span><span class="sxs-lookup"><span data-stu-id="e7489-114">If the struct does not declare a parameterless instance constructor, and the struct has field initializers, a `public` parameterless instance constructor is synthesized.</span></span>
<span data-ttu-id="e7489-115">Il costruttore senza parametri viene sintetizzato anche se tutti i valori degli inizializzatori sono pari a zero.</span><span class="sxs-lookup"><span data-stu-id="e7489-115">The parameterless constructor is synthesized even if all initializer values are zeros.</span></span>

### <a name="modifiers"></a><span data-ttu-id="e7489-116">Modificatori</span><span class="sxs-lookup"><span data-stu-id="e7489-116">Modifiers</span></span>
<span data-ttu-id="e7489-117">Un costruttore di istanza senza parametri può essere meno accessibile dello struct che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="e7489-117">A parameterless instance constructor may be less accessible than the containing struct.</span></span>
<span data-ttu-id="e7489-118">Ciò ha conseguenze per determinati scenari descritti in seguito.</span><span class="sxs-lookup"><span data-stu-id="e7489-118">This has consequences for certain scenarios detailed later.</span></span>

<span data-ttu-id="e7489-119">Lo stesso set di modificatori può essere usato per i costruttori senza parametri come altri costruttori: `static` , `extern` e `unsafe` .</span><span class="sxs-lookup"><span data-stu-id="e7489-119">The same set of modifiers can be used for parameterless constructors as other constructors: `static`, `extern`, and `unsafe`.</span></span>

### <a name="executing-field-initializers"></a><span data-ttu-id="e7489-120">Esecuzione di inizializzatori di campo</span><span class="sxs-lookup"><span data-stu-id="e7489-120">Executing field initializers</span></span>
<span data-ttu-id="e7489-121">L'esecuzione degli inizializzatori di campo dell'istanza struct corrisponde all'esecuzione degli [inizializzatori di campo della classe](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-variable-initializers):</span><span class="sxs-lookup"><span data-stu-id="e7489-121">Execution of struct instance field initializers matches execution of [class field initializers](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-variable-initializers):</span></span>
> <span data-ttu-id="e7489-122">Quando un costruttore di istanza non dispone di un inizializzatore di costruttore,... il costruttore esegue in modo implicito le inizializzazioni specificate dal _variable_initializers_ dei campi dell'istanza....</span><span class="sxs-lookup"><span data-stu-id="e7489-122">When an instance constructor has no constructor initializer, ... that constructor implicitly performs the initializations specified by the _variable_initializers_ of the instance fields ... .</span></span> <span data-ttu-id="e7489-123">Corrisponde a una sequenza di assegnazioni che vengono eseguite immediatamente alla voce al costruttore e prima della chiamata implicita del costruttore della classe base diretta.</span><span class="sxs-lookup"><span data-stu-id="e7489-123">This corresponds to a sequence of assignments that are executed immediately upon entry to the constructor and before the implicit invocation of the direct base class constructor.</span></span> <span data-ttu-id="e7489-124">Gli inizializzatori di variabile vengono eseguiti nell'ordine testuale in cui vengono visualizzati nel... Dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="e7489-124">The variable initializers are executed in the textual order in which they appear in the ... declaration.</span></span>

### <a name="definite-assignment"></a><span data-ttu-id="e7489-125">Assegnazione definita</span><span class="sxs-lookup"><span data-stu-id="e7489-125">Definite assignment</span></span>
<span data-ttu-id="e7489-126">I campi dell'istanza devono essere assegnati in modo sicuro nei costruttori di istanze struct che non dispongono di un `this()` inizializzatore (vedere [costruttori struct](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)).</span><span class="sxs-lookup"><span data-stu-id="e7489-126">Instance fields must be definitely assigned in struct instance constructors that do not have a `this()` initializer (see [struct constructors](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)).</span></span>

<span data-ttu-id="e7489-127">L'assegnazione definitiva dei campi di istanza è obbligatoria anche nei costruttori espliciti senza parametri.</span><span class="sxs-lookup"><span data-stu-id="e7489-127">Definite assignment of instance fields is required within explicit parameterless constructors as well.</span></span>
<span data-ttu-id="e7489-128">L'assegnazione definitiva dei campi di istanza _non è obbligatoria_ nei costruttori senza parametri sintetizzati.</span><span class="sxs-lookup"><span data-stu-id="e7489-128">Definite assignment of instance fields _is not required_ within synthesized parameterless constructors.</span></span>

<span data-ttu-id="e7489-129">_La regola di assegnazione definitiva deve essere applicata in modo coerente? In altre condizioni, è necessario che tutti i campi di istanza di uno struct vengano assegnati in modo esplicito anche quando viene sintetizzato il costruttore senza parametri oppure se si elimina il requisito esistente che tutti i campi sono assegnati in modo esplicito nei costruttori di struct?_</span><span class="sxs-lookup"><span data-stu-id="e7489-129">_Should the definite assignment rule be applied consistently? That is, should we require that all instance fields of a struct are explicitly assigned even when the parameterless constructor is synthesized, or should we drop the existing requirement that all fields are explicitly assigned in struct constructors?_</span></span>
```csharp
struct S0
{
    object x = null;
    object y;
    // ok
}

struct S1
{
    object x = null;
    object y;
    S() { } // error: field 'y' must be assigned
}

struct S2
{
    object x = null;
    object y;
    S() : this(null) { }        // ok
    S(object y) { this.y = y; } // ok
}
```

<span data-ttu-id="e7489-130">_Il costruttore senza parametri sintetizzato potrebbe dover emettere `ldarg.0 initobj S` prima di qualsiasi inizializzatore di campo. Descrivere le specifiche. Come viene gestito in Visual Basic?_</span><span class="sxs-lookup"><span data-stu-id="e7489-130">_The synthesized parameterless constructor may need to emit `ldarg.0 initobj S` before any field initializers. Describe the specifics. How is this handled in Visual Basic?_</span></span>

### <a name="no-base-initializer"></a><span data-ttu-id="e7489-131">Nessun `base()` inizializzatore</span><span class="sxs-lookup"><span data-stu-id="e7489-131">No `base()` initializer</span></span>
<span data-ttu-id="e7489-132">Un `base()` inizializzatore non è consentito nei costruttori struct.</span><span class="sxs-lookup"><span data-stu-id="e7489-132">A `base()` initializer is disallowed in struct constructors.</span></span>

<span data-ttu-id="e7489-133">Il compilatore non genererà una chiamata al costruttore di base `System.ValueType` da alcun costruttore di istanza struct, inclusi i costruttori senza parametri espliciti e sintetizzati.</span><span class="sxs-lookup"><span data-stu-id="e7489-133">The compiler will not emit a call to the base `System.ValueType` constructor from any struct instance constructors including explicit and synthesized parameterless constructors.</span></span>

### <a name="constructor-use"></a><span data-ttu-id="e7489-134">Uso del costruttore</span><span class="sxs-lookup"><span data-stu-id="e7489-134">Constructor use</span></span>

<span data-ttu-id="e7489-135">Il costruttore senza parametri può essere meno accessibile del tipo di valore che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="e7489-135">The parameterless constructor may be less accessible than the containing value type.</span></span>
```csharp
public struct NoConstructor { }
public struct PublicConstructor { public PublicConstructor() { } }
public struct InternalConstructor { internal InternalConstructor() { } }
public struct PrivateConstructor { private PrivateConstructor() { } }
```

<span data-ttu-id="e7489-136">`default` Ignora il costruttore senza parametri e genera un'istanza azzerata.</span><span class="sxs-lookup"><span data-stu-id="e7489-136">`default` ignores the parameterless constructor and generates a zeroed instance.</span></span>
```csharp
_ = default(NoConstructor);      // ok
_ = default(PublicConstructor);  // ok: constructor ignored
_ = default(PrivateConstructor); // ok: constructor ignored
```

<span data-ttu-id="e7489-137">Le espressioni di creazione di oggetti richiedono che il costruttore senza parametri sia accessibile se definito.</span><span class="sxs-lookup"><span data-stu-id="e7489-137">Object creation expressions require the parameterless constructor to be accessible if defined.</span></span>
<span data-ttu-id="e7489-138">Il costruttore senza parametri viene richiamato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="e7489-138">The parameterless constructor is invoked explicitly.</span></span>
<span data-ttu-id="e7489-139">_Si tratta di una modifica di rilievo se il tipo struct con costruttore senza parametri è da un assembly esistente._</span><span class="sxs-lookup"><span data-stu-id="e7489-139">_This is a breaking change if the struct type with parameterless constructor is from an existing assembly._</span></span>
```csharp
_ = new NoConstructor();       // ok: initobj NoConstructor
_ = new InternalConstructor(); // ok: call InternalConstructor::.ctor()
_ = new PrivateConstructor();  // error: 'PrivateConstructor..ctor()' is inaccessible
```

<span data-ttu-id="e7489-140">Un campo locale o di un tipo struct non inizializzato in modo esplicito viene azzerato.</span><span class="sxs-lookup"><span data-stu-id="e7489-140">A local or field of a struct type that is not explicitly initialized is zeroed.</span></span>
<span data-ttu-id="e7489-141">Il compilatore segnala un errore di assegnazione definito per uno struct non inizializzato che non è vuoto.</span><span class="sxs-lookup"><span data-stu-id="e7489-141">The compiler reports a definite assignment error for an uninitialized struct that is not empty.</span></span> 
```csharp
NoConstructor s1;
PublicConstructor s2;
s1.ToString(); // error: use of unassigned local (unless type is empty)
s2.ToString(); // error: use of unassigned local (unless type is empty)
```

<span data-ttu-id="e7489-142">L'allocazione di matrici ignora qualsiasi costruttore senza parametri e genera elementi azzerati.</span><span class="sxs-lookup"><span data-stu-id="e7489-142">Array allocation ignores any parameterless constructor and generates zeroed elements.</span></span>
<span data-ttu-id="e7489-143">_Il compilatore avvisa che il costruttore senza parametri viene ignorato? Come evitare un avviso di questo tipo?_</span><span class="sxs-lookup"><span data-stu-id="e7489-143">_Should the compiler warn that the parameterless constructor is ignored? How would such a warning be avoided?_</span></span>
```csharp
_ = new NoConstructor[1];      // ok
_ = new PublicConstructor[1];  // ok: constructor ignored
_ = new PrivateConstructor[1]; // ok: constructor ignored
```

<span data-ttu-id="e7489-144">Non è possibile usare costruttori senza parametri come valori predefiniti dei parametri.</span><span class="sxs-lookup"><span data-stu-id="e7489-144">Parameterless constructors cannot be used as parameter default values.</span></span>
<span data-ttu-id="e7489-145">_Si tratta di una modifica di rilievo se il tipo struct con costruttore senza parametri è da un assembly esistente._</span><span class="sxs-lookup"><span data-stu-id="e7489-145">_This is a breaking change if the struct type with parameterless constructor is from an existing assembly._</span></span>
```csharp
static void F1(NoConstructor s1 = new()) { }     // ok
static void F2(PublicConstructor s1 = new()) { } // error: default value must be constant
```

### <a name="constraints"></a><span data-ttu-id="e7489-146">Vincoli</span><span class="sxs-lookup"><span data-stu-id="e7489-146">Constraints</span></span>
<span data-ttu-id="e7489-147">Il `new()` vincolo del parametro di tipo richiede che il costruttore senza parametri sia definito (vedere la pagina relativa ai `public` [vincoli soddisfacenti](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)).</span><span class="sxs-lookup"><span data-stu-id="e7489-147">The `new()` type parameter constraint requires the parameterless constructor to be `public` if defined (see [satisfying constraints](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)).</span></span>
```csharp
static T CreateNew<T>() where T : new() => new T();

_ = CreateNew<NoConstructor>();       // ok
_ = CreateNew<PublicConstructor>();   // ok
_ = CreateNew<InternalConstructor>(); // error: 'InternalConstructor..ctor()' is not public
_ = CreateNew<PrivateConstructor>();  // error: 'PrivateConstructor..ctor()' is not public
```

<span data-ttu-id="e7489-148">`new T()` viene generato come una chiamata a `System.Activator.CreateInstance<T>()` e il compilatore presuppone che l'implementazione di `CreateInstance<T>()` richiami il `public` costruttore senza parametri se definito.</span><span class="sxs-lookup"><span data-stu-id="e7489-148">`new T()` is emitted as a call to `System.Activator.CreateInstance<T>()`, and the compiler assumes the implementation of `CreateInstance<T>()` invokes the `public` parameterless constructor if defined.</span></span>

<span data-ttu-id="e7489-149">Il controllo dei vincoli del parametro di tipo presenta un divario perché il `new()` vincolo è soddisfatto da un parametro di tipo con un vincolo (vedere la pagina relativa ai `struct` vincoli di [soddisfazione](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)).</span><span class="sxs-lookup"><span data-stu-id="e7489-149">There is a gap in type parameter constraint checking because the `new()` constraint is satisfied by a type parameter with a `struct` constraint (see [satisfying constraints](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)).</span></span>

<span data-ttu-id="e7489-150">Di conseguenza, il compilatore può eseguire le operazioni seguenti, ma `Activator.CreateInstance<InternalConstructor>()` avrà esito negativo in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="e7489-150">As a result, the following will be allowed by the compiler but `Activator.CreateInstance<InternalConstructor>()` will fail at runtime.</span></span>
<span data-ttu-id="e7489-151">Il problema non è stato introdotto da questa proposta, tuttavia, il problema esiste con C# 9 se il tipo struct con costruttore senza parametri inaccessibile è dei metadati.</span><span class="sxs-lookup"><span data-stu-id="e7489-151">The issue is not introduced by this proposal though - the issue exists with C# 9 if the struct type with inaccessible parameterless constructor is from metadata.</span></span>
```csharp
static T CreateNew<T>() where T : new() => new T();
static T CreateStruct<T>() where T : struct => CreateNew<T>();

_ = CreateStruct<InternalConstructor>(); // compiles; 'MissingMethodException' at runtime
```

### <a name="metadata"></a><span data-ttu-id="e7489-152">Metadati</span><span class="sxs-lookup"><span data-stu-id="e7489-152">Metadata</span></span>
<span data-ttu-id="e7489-153">I costruttori di istanze struct senza parametri espliciti e sintetizzati verranno emessi nei metadati.</span><span class="sxs-lookup"><span data-stu-id="e7489-153">Explicit and synthesized parameterless struct instance constructors will be emitted to metadata.</span></span>

<span data-ttu-id="e7489-154">I costruttori di istanze struct senza parametri verranno importati dai metadati.</span><span class="sxs-lookup"><span data-stu-id="e7489-154">Parameterless struct instance constructors will be imported from metadata.</span></span>
<span data-ttu-id="e7489-155">_Si tratta di una modifica sostanziale per gli utenti di assembly esistenti con struct con costruttori senza parametri._</span><span class="sxs-lookup"><span data-stu-id="e7489-155">_This is a breaking change for consumers of existing assemblies with structs with parameterless constructors._</span></span>

<span data-ttu-id="e7489-156">I costruttori di istanze struct senza parametri verranno emessi in assembly ref indipendentemente dall'accessibilità per consentire ai consumer di distinguere tra nessun costruttore senza parametri un costruttore inaccessibile.</span><span class="sxs-lookup"><span data-stu-id="e7489-156">Parameterless struct instance constructors will be emitted to ref assemblies regardless of accessibility to allow consumers to differentiate between no parameterless constructor an inaccessible constructor.</span></span>

## <a name="see-also"></a><span data-ttu-id="e7489-157">Vedi anche</span><span class="sxs-lookup"><span data-stu-id="e7489-157">See also</span></span>

- https://github.com/dotnet/roslyn/issues/1029

## <a name="design-meetings"></a><span data-ttu-id="e7489-158">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="e7489-158">Design meetings</span></span>

- https://github.com/dotnet/csharplang/blob/master/meetings/2021/LDM-2021-01-27.md#field-initializers
