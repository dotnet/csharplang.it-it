---
ms.openlocfilehash: aaf11b188c4622d50e86f757c24eb5cd934f8909
ms.sourcegitcommit: 5b6f535beed62156b5239035e1829b001886b0b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102622485"
---
# <a name="parameterless-struct-constructors"></a>Costruttori struct senza parametri

## <a name="summary"></a>Riepilogo
Supporta costruttori senza parametri e inizializzatori di campo di istanza per i tipi struct.

## <a name="motivation"></a>Motivazione
I costruttori espliciti senza parametri offrono un maggiore controllo sulle istanze con costruzione minima del tipo di struct.
Gli inizializzatori di campo di istanza consentono l'inizializzazione semplificata tra più costruttori.
Insieme si chiuderebbe uno spazio ovvio tra `struct` le `class` dichiarazioni e.

Il supporto per gli inizializzatori di campo consente inoltre di inizializzare i campi nelle `record struct` dichiarazioni senza implementare in modo esplicito il costruttore primario.
```csharp
record struct Person(string Name)
{
    public object Id { get; init; } = GetNextId();
}
```

Se gli inizializzatori di campo struct sono supportati per i costruttori con parametri, sembra naturale estenderlo anche a costruttori senza parametri.
```csharp
record struct Person()
{
    public string Name { get; init; }
    public object Id { get; init; } = GetNextId();
}
```

## <a name="proposal"></a>Proposal

### <a name="instance-field-initializers"></a>Inizializzatori di campo di istanza
Le dichiarazioni dei campi di istanza per uno struct possono includere inizializzatori.

Come per gli [inizializzatori di campo di classe](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-field-initialization):
> Un inizializzatore di variabile per un campo di istanza non può fare riferimento all'istanza in fase di creazione. 

### <a name="constructors"></a>Costruttori
Uno struct può dichiarare un costruttore di istanza senza parametri.

Un costruttore di istanza senza parametri è valido per tutti i tipi di struct, tra cui `struct` ,, `readonly struct` `ref struct` e `record struct` .

Se lo struct non dichiara un costruttore di istanza senza parametri e lo struct non ha campi con inizializzatori di variabile, lo struct (vedere [costruttori struct](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors))...
> dispone implicitamente di un costruttore di istanza senza parametri che restituisce sempre il valore risultante dall'impostazione di tutti i campi di tipo valore sul valore predefinito e di tutti i campi di tipo riferimento su null.

Se lo struct non dichiara un costruttore di istanza senza parametri e lo struct contiene inizializzatori di campo, `public` viene sintetizzato un costruttore di istanza senza parametri.
Il costruttore senza parametri viene sintetizzato anche se tutti i valori degli inizializzatori sono pari a zero.

### <a name="modifiers"></a>Modificatori
Un costruttore di istanza senza parametri può essere meno accessibile dello struct che lo contiene.
```csharp
public struct NoConstructor { }
public struct PublicConstructor { public PublicConstructor() { } }
public struct InternalConstructor { internal InternalConstructor() { } }
public struct PrivateConstructor { private PrivateConstructor() { } }
```

Lo stesso set di modificatori può essere usato per i costruttori senza parametri come altri costruttori di istanza: `extern` , e `unsafe` .

I costruttori non possono essere `partial` .

### <a name="executing-field-initializers"></a>Esecuzione di inizializzatori di campo
L'esecuzione degli inizializzatori di campo dell'istanza struct corrisponde all'esecuzione degli [inizializzatori di campo della classe](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-variable-initializers):
> Quando un costruttore di istanza non dispone di un inizializzatore di costruttore,... il costruttore esegue in modo implicito le inizializzazioni specificate dal _variable_initializers_ dei campi dell'istanza.... Corrisponde a una sequenza di assegnazioni che vengono eseguite immediatamente alla voce del costruttore.... Gli inizializzatori di variabile vengono eseguiti nell'ordine testuale in cui vengono visualizzati nel... Dichiarazione.

### <a name="definite-assignment"></a>Assegnazione definita
I campi dell'istanza devono essere assegnati in modo sicuro nei costruttori di istanze struct che non dispongono di un `this()` inizializzatore (vedere [costruttori struct](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)).

L'assegnazione definitiva dei campi di istanza è obbligatoria anche nei costruttori espliciti senza parametri.
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

L' _assegnazione definitiva dei campi di istanza struct è obbligatoria nei costruttori senza parametri sintetizzati?_ 
 In _tal caso, se i campi di istanza hanno inizializzatori, tutti i campi dell'istanza devono avere inizializzatori._
```csharp
struct S0
{
    int x = 0;
    object y;
    // ok?
}
```

Se i campi non vengono inizializzati in modo esplicito, il costruttore dovrà azzerare l'istanza prima di eseguire qualsiasi inizializzatore di campo.
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

### <a name="no-base-initializer"></a>Nessun `base()` inizializzatore
Un `base()` inizializzatore non è consentito nei costruttori struct.

Il compilatore non genererà una chiamata al costruttore di base `System.ValueType` da alcun costruttore di istanza struct, inclusi i costruttori senza parametri espliciti e sintetizzati.

### <a name="fields"></a>Campi
Il costruttore senza parametri sintetizzato non chiamerà alcun campo anziché chiamare i costruttori senza parametri per i tipi di campo.

_Se il compilatore segnala un avviso quando i costruttori dei campi vengono ignorati,_
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

### <a name="default-expression"></a>Espressione `default`
`default` Ignora il costruttore senza parametri e genera un'istanza azzerata.

_Se il compilatore segnala un avviso quando un costruttore viene ignorato,_
```csharp
_ = default(NoConstructor);      // ok
_ = default(PublicConstructor);  // ok: constructor ignored
_ = default(PrivateConstructor); // ok: constructor ignored
```

### <a name="object-creation"></a>Creazione di oggetti
Le espressioni di creazione di oggetti richiedono che il costruttore senza parametri sia accessibile se definito.
Il costruttore senza parametri viene richiamato in modo esplicito.

Si _tratta di una modifica di rilievo se il tipo struct con costruttore senza parametri è da un assembly esistente._ 
 _Se il compilatore segnala un avviso anziché un errore `new()` se il costruttore è inaccessibile ed emette `initobj` , per la compatibilità?_
```csharp
_ = new NoConstructor();      // ok: initobj NoConstructor
_ = new PublicConstructor();  // ok: call PublicConstructor::.ctor()
_ = new PrivateConstructor(); // error: 'PrivateConstructor..ctor()' is inaccessible
```

### <a name="uninitialized-values"></a>Valori non inizializzati
Un campo locale o di un tipo struct non inizializzato in modo esplicito viene azzerato.
Il compilatore segnala un errore di assegnazione definito per uno struct non inizializzato che non è vuoto. 
```csharp
NoConstructor s1;
PublicConstructor s2;
s1.ToString(); // error: use of unassigned local (unless type is empty)
s2.ToString(); // error: use of unassigned local (unless type is empty)
```

### <a name="array-allocation"></a>Allocazione di matrici
L'allocazione di matrici ignora qualsiasi costruttore senza parametri e genera elementi azzerati.

_Il compilatore avvisa che il costruttore senza parametri viene ignorato? Come evitare un avviso di questo tipo?_
```csharp
_ = new NoConstructor[1];      // ok
_ = new PublicConstructor[1];  // ok: constructor ignored
_ = new PrivateConstructor[1]; // ok: constructor ignored
```

### <a name="parameter-default-values"></a>Valori predefiniti dei parametri
Non è possibile usare costruttori senza parametri come valori predefiniti dei parametri.

Si _tratta di una modifica di rilievo se il tipo struct con costruttore senza parametri è da un assembly esistente._ 
 _Se il compilatore segnala un avviso anziché un errore `new()` se il costruttore è inaccessibile ed emette `default` , per la compatibilità?_
```csharp
static void F1(NoConstructor s1 = new()) { }     // ok
static void F2(PublicConstructor s1 = new()) { } // error: default value must be constant
```

### <a name="constraints"></a>Vincoli
Il `new()` vincolo del parametro di tipo richiede che il costruttore senza parametri sia definito (vedere la pagina relativa ai `public` [vincoli soddisfacenti](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)).
```csharp
static T CreateNew<T>() where T : new() => new T();

_ = CreateNew<NoConstructor>();       // ok
_ = CreateNew<PublicConstructor>();   // ok
_ = CreateNew<InternalConstructor>(); // error: 'InternalConstructor..ctor()' is not public
```
_Se il compilatore segnala un avviso anziché un errore quando si sostituisce uno struct con un costruttore non pubblico per un parametro di tipo con un `new()` vincolo, per la compatibilità e per evitare di presumere che sia effettivamente stata creata un'istanza del tipo._

`new T()` viene generato come una chiamata a `System.Activator.CreateInstance<T>()` e il compilatore presuppone che l'implementazione di `CreateInstance<T>()` richiami il `public` costruttore senza parametri se definito.

_Con .NET Framework, `Activator.CreateInstance<T>()` richiama il costruttore senza parametri se il vincolo è `where T : new()` ma sembra ignorare il costruttore senza parametri se il vincolo è `where T : struct` ._

Il controllo dei vincoli del parametro di tipo presenta un divario perché il `new()` vincolo è soddisfatto da un parametro di tipo con un vincolo (vedere la pagina relativa ai `struct` vincoli di [soddisfazione](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)).

Di conseguenza, il compilatore può eseguire le operazioni seguenti, ma `Activator.CreateInstance<InternalConstructor>()` avrà esito negativo in fase di esecuzione.
Il problema non è stato introdotto da questa proposta, tuttavia, il problema esiste con C# 9 se il tipo struct con costruttore senza parametri inaccessibile è dei metadati.
```csharp
static T CreateNew<T>() where T : new() => new T();
static T CreateStruct<T>() where T : struct => CreateNew<T>();

_ = CreateStruct<InternalConstructor>(); // compiles; 'MissingMethodException' at runtime
```

### <a name="optional-parameters"></a>Parametri facoltativi
I costruttori con parametri facoltativi non sono considerati costruttori senza parametri. Questo comportamento è invariato rispetto alle versioni precedenti del compilatore.
```csharp
struct S1 { public S1(string s = "") { } }
struct S2 { public S2(params object[] args) { } }

_ = new S1(); // ok: ignores constructor
_ = new S2(); // ok: ignores constructor
```

### <a name="metadata"></a>Metadati
I costruttori di istanze struct senza parametri espliciti e sintetizzati verranno emessi nei metadati.

I costruttori di istanze struct senza parametri verranno importati dai metadati indipendentemente dall'accessibilità.
_Potrebbe trattarsi di una modifica di rilievo per i consumer di assembly esistenti con struct con costruttori senza parametri privati se vengono segnalati errori o avvisi aggiuntivi._

I costruttori di istanze struct senza parametri verranno emessi in assembly ref indipendentemente dall'accessibilità per consentire ai consumer di distinguere tra nessun costruttore senza parametri un costruttore inaccessibile.

## <a name="see-also"></a>Vedi anche

- https://github.com/dotnet/roslyn/issues/1029

## <a name="design-meetings"></a>Riunioni di progettazione

- https://github.com/dotnet/csharplang/blob/master/meetings/2021/LDM-2021-01-27.md#field-initializers
