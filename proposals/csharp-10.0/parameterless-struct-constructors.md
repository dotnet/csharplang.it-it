---
ms.openlocfilehash: d5eeb2501d4e99136911cb1fa2153ff7057432b3
ms.sourcegitcommit: 14c7c290e926fcaff7cb8609b282821a473589af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101751679"
---
# <a name="parameterless-struct-constructors"></a>Costruttori struct senza parametri

## <a name="summary"></a>Riepilogo

Supporta costruttori senza parametri e inizializzatori di campo di istanza per i tipi struct.

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
Ciò ha conseguenze per determinati scenari descritti in seguito.

Lo stesso set di modificatori può essere usato per i costruttori senza parametri come altri costruttori: `static` , `extern` e `unsafe` .

### <a name="executing-field-initializers"></a>Esecuzione di inizializzatori di campo
L'esecuzione degli inizializzatori di campo dell'istanza struct corrisponde all'esecuzione degli [inizializzatori di campo della classe](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-variable-initializers):
> Quando un costruttore di istanza non dispone di un inizializzatore di costruttore,... il costruttore esegue in modo implicito le inizializzazioni specificate dal _variable_initializers_ dei campi dell'istanza.... Corrisponde a una sequenza di assegnazioni che vengono eseguite immediatamente alla voce al costruttore e prima della chiamata implicita del costruttore della classe base diretta. Gli inizializzatori di variabile vengono eseguiti nell'ordine testuale in cui vengono visualizzati nel... Dichiarazione.

### <a name="definite-assignment"></a>Assegnazione definita
I campi dell'istanza devono essere assegnati in modo sicuro nei costruttori di istanze struct che non dispongono di un `this()` inizializzatore (vedere [costruttori struct](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)).

L'assegnazione definitiva dei campi di istanza è obbligatoria anche nei costruttori espliciti senza parametri.
L'assegnazione definitiva dei campi di istanza _non è obbligatoria_ nei costruttori senza parametri sintetizzati.

_La regola di assegnazione definitiva deve essere applicata in modo coerente? In altre condizioni, è necessario che tutti i campi di istanza di uno struct vengano assegnati in modo esplicito anche quando viene sintetizzato il costruttore senza parametri oppure se si elimina il requisito esistente che tutti i campi sono assegnati in modo esplicito nei costruttori di struct?_
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

_Il costruttore senza parametri sintetizzato potrebbe dover emettere `ldarg.0 initobj S` prima di qualsiasi inizializzatore di campo. Descrivere le specifiche. Come viene gestito in Visual Basic?_

### <a name="no-base-initializer"></a>Nessun `base()` inizializzatore
Un `base()` inizializzatore non è consentito nei costruttori struct.

Il compilatore non genererà una chiamata al costruttore di base `System.ValueType` da alcun costruttore di istanza struct, inclusi i costruttori senza parametri espliciti e sintetizzati.

### <a name="constructor-use"></a>Uso del costruttore

Il costruttore senza parametri può essere meno accessibile del tipo di valore che lo contiene.
```csharp
public struct NoConstructor { }
public struct PublicConstructor { public PublicConstructor() { } }
public struct InternalConstructor { internal InternalConstructor() { } }
public struct PrivateConstructor { private PrivateConstructor() { } }
```

`default` Ignora il costruttore senza parametri e genera un'istanza azzerata.
```csharp
_ = default(NoConstructor);      // ok
_ = default(PublicConstructor);  // ok: constructor ignored
_ = default(PrivateConstructor); // ok: constructor ignored
```

Le espressioni di creazione di oggetti richiedono che il costruttore senza parametri sia accessibile se definito.
Il costruttore senza parametri viene richiamato in modo esplicito.
_Si tratta di una modifica di rilievo se il tipo struct con costruttore senza parametri è da un assembly esistente._
```csharp
_ = new NoConstructor();       // ok: initobj NoConstructor
_ = new InternalConstructor(); // ok: call InternalConstructor::.ctor()
_ = new PrivateConstructor();  // error: 'PrivateConstructor..ctor()' is inaccessible
```

Un campo locale o di un tipo struct non inizializzato in modo esplicito viene azzerato.
Il compilatore segnala un errore di assegnazione definito per uno struct non inizializzato che non è vuoto. 
```csharp
NoConstructor s1;
PublicConstructor s2;
s1.ToString(); // error: use of unassigned local (unless type is empty)
s2.ToString(); // error: use of unassigned local (unless type is empty)
```

L'allocazione di matrici ignora qualsiasi costruttore senza parametri e genera elementi azzerati.
_Il compilatore avvisa che il costruttore senza parametri viene ignorato? Come evitare un avviso di questo tipo?_
```csharp
_ = new NoConstructor[1];      // ok
_ = new PublicConstructor[1];  // ok: constructor ignored
_ = new PrivateConstructor[1]; // ok: constructor ignored
```

Non è possibile usare costruttori senza parametri come valori predefiniti dei parametri.
_Si tratta di una modifica di rilievo se il tipo struct con costruttore senza parametri è da un assembly esistente._
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
_ = CreateNew<PrivateConstructor>();  // error: 'PrivateConstructor..ctor()' is not public
```

`new T()` viene generato come una chiamata a `System.Activator.CreateInstance<T>()` e il compilatore presuppone che l'implementazione di `CreateInstance<T>()` richiami il `public` costruttore senza parametri se definito.

Il controllo dei vincoli del parametro di tipo presenta un divario perché il `new()` vincolo è soddisfatto da un parametro di tipo con un vincolo (vedere la pagina relativa ai `struct` vincoli di [soddisfazione](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)).

Di conseguenza, il compilatore può eseguire le operazioni seguenti, ma `Activator.CreateInstance<InternalConstructor>()` avrà esito negativo in fase di esecuzione.
Il problema non è stato introdotto da questa proposta, tuttavia, il problema esiste con C# 9 se il tipo struct con costruttore senza parametri inaccessibile è dei metadati.
```csharp
static T CreateNew<T>() where T : new() => new T();
static T CreateStruct<T>() where T : struct => CreateNew<T>();

_ = CreateStruct<InternalConstructor>(); // compiles; 'MissingMethodException' at runtime
```

### <a name="metadata"></a>Metadati
I costruttori di istanze struct senza parametri espliciti e sintetizzati verranno emessi nei metadati.

I costruttori di istanze struct senza parametri verranno importati dai metadati.
_Si tratta di una modifica sostanziale per gli utenti di assembly esistenti con struct con costruttori senza parametri._

I costruttori di istanze struct senza parametri verranno emessi in assembly ref indipendentemente dall'accessibilità per consentire ai consumer di distinguere tra nessun costruttore senza parametri un costruttore inaccessibile.

## <a name="see-also"></a>Vedi anche

- https://github.com/dotnet/roslyn/issues/1029

## <a name="design-meetings"></a>Riunioni di progettazione

- https://github.com/dotnet/csharplang/blob/master/meetings/2021/LDM-2021-01-27.md#field-initializers
