---
ms.openlocfilehash: 6dd1dde67597b2125de9a1aa2fab9144128d533f
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71704033"
---
# <a name="structs"></a>Struct

Gli struct sono simili alle classi in quanto rappresentano strutture dei dati che possono contenere membri dati e membri di funzione. Tuttavia, a differenza delle classi, gli struct sono tipi valore e non richiedono l'allocazione dell'heap. Una variabile di un tipo struct contiene direttamente i dati dello struct, mentre una variabile di un tipo di classe contiene un riferimento ai dati, il secondo noto come oggetto.

I tipi struct sono particolarmente utili per strutture dati di piccole dimensioni che hanno una semantica di valori. I numeri complessi, i punti di un sistema di coordinate o le coppie chiave-valore di un dizionario sono buoni esempi di struct. La chiave di queste strutture di dati è che hanno pochi membri dati, che non richiedono l'uso dell'ereditarietà o dell'identità referenziale e che possono essere implementati facilmente usando la semantica del valore in cui l'assegnazione copia il valore anziché il riferimento.

Come descritto in [tipi semplici](types.md#simple-types), i tipi semplici forniti da C#, ad esempio `int`, `double` e `bool`, sono in realtà tutti i tipi struct. Proprio come questi tipi predefiniti sono struct, è anche possibile usare gli struct e l'overload degli C# operatori per implementare nuovi tipi "primitivi" nel linguaggio. Alla fine di questo capitolo sono riportati due esempi di tali tipi ([esempi di struct](structs.md#struct-examples)).

## <a name="struct-declarations"></a>Dichiarazioni struct

Un *struct_declaration* è un *type_declaration* ([dichiarazioni di tipo](namespaces.md#type-declarations)) che dichiara un nuovo struct:

```antlr
struct_declaration
    : attributes? struct_modifier* 'partial'? 'struct' identifier type_parameter_list?
      struct_interfaces? type_parameter_constraints_clause* struct_body ';'?
    ;
```

Un *struct_declaration* è costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), seguito da un set facoltativo di *struct_modifier*s ([modificatori struct](structs.md#struct-modifiers)), seguito da un modificatore `partial` facoltativo, seguito dal parametro parola chiave `struct` e un *identificatore* che denomina lo struct, seguito da una specifica *type_parameter_list* facoltativa ([parametri di tipo](classes.md#type-parameters)), seguita da una specifica *struct_interfaces* facoltativa ([parziale modificatore](structs.md#partial-modifier)), seguito da una specifica *type_parameter_constraints_clause*s facoltativa ([vincoli del parametro di tipo](classes.md#type-parameter-constraints)), seguita da un *struct_body* ([corpo della struct](structs.md#struct-body)), seguito facoltativamente da un punto e virgola.

### <a name="struct-modifiers"></a>Modificatori struct

Un *struct_declaration* può facoltativamente includere una sequenza di modificatori struct:

```antlr
struct_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | struct_modifier_unsafe
    ;
```

Si tratta di un errore in fase di compilazione perché lo stesso modificatore venga visualizzato più volte in una dichiarazione struct.

I modificatori di una dichiarazione di struct hanno lo stesso significato di quelli di una dichiarazione di classe ([dichiarazioni di classe](classes.md#class-declarations)).

### <a name="partial-modifier"></a>Modificatore parziale

Il modificatore `partial` indica che questo *struct_declaration* è una dichiarazione di tipo parziale. Più dichiarazioni di struct parziali con lo stesso nome in uno spazio dei nomi di inclusione o in una dichiarazione di tipo combinano per formare una dichiarazione struct, seguendo le regole specificate in [tipi parziali](classes.md#partial-types).

### <a name="struct-interfaces"></a>Interfacce struct

Una dichiarazione struct può includere una specifica *struct_interfaces* , nel qual caso lo struct viene definito per implementare direttamente i tipi di interfaccia specificati.

```antlr
struct_interfaces
    : ':' interface_type_list
    ;
```

Le implementazioni dell'interfaccia sono illustrate ulteriormente nelle [implementazioni dell'interfaccia](interfaces.md#interface-implementations).

### <a name="struct-body"></a>Corpo dello struct

Il *struct_body* di uno struct definisce i membri della struttura.

```antlr
struct_body
    : '{' struct_member_declaration* '}'
    ;
```

## <a name="struct-members"></a>Membri struct

I membri di uno struct sono costituiti dai membri introdotti dai relativi *struct_member_declaration*e dai membri ereditati dal tipo `System.ValueType`.

```antlr
struct_member_declaration
    : constant_declaration
    | field_declaration
    | method_declaration
    | property_declaration
    | event_declaration
    | indexer_declaration
    | operator_declaration
    | constructor_declaration
    | static_constructor_declaration
    | type_declaration
    | struct_member_declaration_unsafe
    ;
```

Ad eccezione delle differenze indicate nelle [differenze tra le classi e gli struct](structs.md#class-and-struct-differences), le descrizioni dei membri della classe forniti nei membri della [classe](classes.md#class-members) tramite gli [iteratori](classes.md#iterators) si applicano anche ai membri struct.

## <a name="class-and-struct-differences"></a>Differenze tra classi e struct

Gli struct differiscono dalle classi in diversi modi importanti:

*  Gli struct sono tipi di valore ([semantica del valore](structs.md#value-semantics)).
*  Tutti i tipi struct ereditano in modo implicito dalla classe `System.ValueType` ([ereditarietà](structs.md#inheritance)).
*  L'assegnazione a una variabile di un tipo struct crea una copia del valore assegnato ([assegnazione](structs.md#assignment)).
*  Il valore predefinito di uno struct è il valore prodotto impostando tutti i campi del tipo di valore sul valore predefinito e tutti i campi di tipo riferimento per `null` ([valori predefiniti](structs.md#default-values)).
*  Le operazioni di conversione boxing e unboxing vengono usate per eseguire la conversione tra un tipo di struct e `object` ([conversione boxing e](structs.md#boxing-and-unboxing)unboxing).
*  Il significato di `this` è diverso per gli struct ([questo accesso](expressions.md#this-access)).
*  Le dichiarazioni dei campi di istanza per uno struct non possono includere inizializzatori di variabile ([inizializzatori di campo](structs.md#field-initializers)).
*  Uno struct non è autorizzato a dichiarare un costruttore di istanza senza parametri ([costruttori](structs.md#constructors)).
*  Uno struct non è autorizzato a dichiarare[un distruttore (](structs.md#destructors)distruttori).

### <a name="value-semantics"></a>Semantica del valore

Gli struct sono tipi di valore ([tipi di valore](types.md#value-types)) e hanno una semantica del valore. Le classi, d'altra parte, sono tipi di riferimento ([tipi di riferimento](types.md#reference-types)) e hanno una semantica di riferimento.

Una variabile di un tipo struct contiene direttamente i dati dello struct, mentre una variabile di un tipo di classe contiene un riferimento ai dati, il secondo noto come oggetto. Quando uno struct `B` contiene un campo di istanza di tipo `A` e `A` è un tipo struct, si tratta di un errore in fase di compilazione perché `A` dipenda da `B` o da un tipo costruito da `B`. Uno struct `X` ***dipende direttamente*** da uno struct `Y` se `X` contiene un campo di istanza di tipo `Y`. Data questa definizione, il set completo di struct su cui dipende uno struct è la chiusura transitiva di che ***dipende direttamente dalla*** relazione.  Esempio:
```csharp
struct Node
{
    int data;
    Node next; // error, Node directly depends on itself
}
```
è un errore perché `Node` contiene un campo di istanza di un proprio tipo.  Un altro esempio
```csharp
struct A { B b; }

struct B { C c; }

struct C { A a; }
```
è un errore perché ognuno dei tipi `A`, `B` e `C` dipendono tra loro.

Con le classi, è possibile che due variabili facciano riferimento allo stesso oggetto ed è quindi possibile che le operazioni su una variabile influiscano sull'oggetto a cui fa riferimento l'altra variabile. Con gli struct, le variabili hanno ciascuna una propria copia dei dati (tranne nel caso delle variabili di parametro `ref` e `out`) e non è possibile che le operazioni su uno influiscano sull'altro. Inoltre, poiché gli struct non sono tipi di riferimento, non è possibile che i valori di un tipo struct siano `null`.

Data la dichiarazione
```csharp
struct Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
frammento di codice
```csharp
Point a = new Point(10, 10);
Point b = a;
a.x = 100;
System.Console.WriteLine(b.x);
```
Restituisce il valore `10`. L'assegnazione di `a` a `b` crea una copia del valore e `b` non è quindi interessata dall'assegnazione a `a.x`. Con `Point` invece è stato dichiarato come una classe, l'output sarà `100` perché `a` e `b` fanno riferimento allo stesso oggetto.

### <a name="inheritance"></a>Ereditarietà

Tutti i tipi struct ereditano in modo implicito dalla classe `System.ValueType`, che a sua volta eredita dalla classe `object`. Una dichiarazione struct può specificare un elenco di interfacce implementate, ma non è possibile che una dichiarazione struct specifichi una classe base.

I tipi struct non sono mai astratti e sono sempre implicitamente sealed. I modificatori `abstract` e `sealed` non sono pertanto consentiti in una dichiarazione struct.

Poiché l'ereditarietà non è supportata per gli struct, l'accessibilità dichiarata di un membro struct non può essere `protected` o `protected internal`.

I membri di funzione in uno struct non possono essere `abstract` o `virtual` e il modificatore `override` è consentito solo per eseguire l'override dei metodi ereditati da `System.ValueType`.

### <a name="assignment"></a>Assegnazione

L'assegnazione a una variabile di un tipo struct crea una copia del valore da assegnare. Questo comportamento è diverso dall'assegnazione a una variabile di un tipo di classe, che copia il riferimento ma non l'oggetto identificato dal riferimento.

Analogamente a un'assegnazione, quando una struttura viene passata come parametro di valore o restituita come risultato di un membro di funzione, viene creata una copia dello struct. Uno struct può essere passato per riferimento a un membro di funzione usando un parametro `ref` o `out`.

Quando una proprietà o un indicizzatore di uno struct è la destinazione di un'assegnazione, l'espressione dell'istanza associata alla proprietà o all'indicizzatore deve essere classificata come variabile. Se l'espressione dell'istanza è classificata come valore, si verifica un errore in fase di compilazione. Questa operazione viene descritta più dettagliatamente nell' [assegnazione semplice](expressions.md#simple-assignment).

### <a name="default-values"></a>Valori predefiniti

Come descritto in [valori predefiniti](variables.md#default-values), i diversi tipi di variabili vengono inizializzati automaticamente sul valore predefinito quando vengono creati. Per le variabili dei tipi di classe e altri tipi di riferimento, questo valore predefinito è `null`. Tuttavia, poiché gli struct sono tipi di valore che non possono essere `null`, il valore predefinito di uno struct è il valore prodotto impostando tutti i campi di tipo valore sul valore predefinito e tutti i campi di tipo riferimento su `null`.

Facendo riferimento allo struct `Point` dichiarato in precedenza, l'esempio
```csharp
Point[] a = new Point[100];
```
Inizializza ogni `Point` nella matrice sul valore prodotto impostando i campi `x` e `y` su zero.

Il valore predefinito di uno struct corrisponde al valore restituito dal costruttore predefinito dello struct ([costruttori predefiniti](types.md#default-constructors)). A differenza di una classe, uno struct non è autorizzato a dichiarare un costruttore di istanza senza parametri. Al contrario, ogni struct dispone implicitamente di un costruttore di istanza senza parametri che restituisce sempre il valore risultante dall'impostazione di tutti i campi di tipo valore sul valore predefinito e di tutti i campi di tipo riferimento su `null`.

Gli struct devono essere progettati per considerare lo stato di inizializzazione predefinito uno stato valido. Nell'esempio
```csharp
using System;

struct KeyValuePair
{
    string key;
    string value;

    public KeyValuePair(string key, string value) {
        if (key == null || value == null) throw new ArgumentException();
        this.key = key;
        this.value = value;
    }
}
```
il costruttore di istanze definito dall'utente protegge da valori null solo se viene chiamato in modo esplicito. Nei casi in cui una variabile `KeyValuePair` è soggetta all'inizializzazione predefinita dei valori, i campi `key` e `value` saranno null e la struttura deve essere preparata a gestire questo stato.

### <a name="boxing-and-unboxing"></a>Boxing e unboxing

Un valore di un tipo di classe può essere convertito nel tipo `object` o in un tipo di interfaccia implementato dalla classe semplicemente trattando il riferimento come un altro tipo in fase di compilazione. Analogamente, un valore di tipo `object` o un valore di un tipo di interfaccia può essere convertito di nuovo in un tipo di classe senza modificare il riferimento (ma, in questo caso, è necessario un controllo del tipo in fase di esecuzione).

Poiché gli struct non sono tipi di riferimento, queste operazioni vengono implementate in modo diverso per i tipi struct. Quando un valore di un tipo struct viene convertito nel tipo `object` o in un tipo di interfaccia implementato dallo struct, viene eseguita un'operazione di conversione boxing. Analogamente, quando un valore di tipo `object` o un valore di un tipo di interfaccia viene riconvertito in un tipo struct, viene eseguita un'operazione di conversione unboxing. Una differenza fondamentale tra le stesse operazioni sui tipi di classe è che la conversione boxing e unboxing copia il valore dello struct all'interno o all'esterno dell'istanza boxed. Quindi, dopo un'operazione di conversione boxing o unboxing, le modifiche apportate allo struct unboxed non vengono riflesse nello struct boxed.

Quando un tipo struct esegue l'override di un metodo virtuale ereditato da `System.Object` (ad esempio `Equals`, `GetHashCode` o `ToString`), la chiamata del metodo virtuale tramite un'istanza del tipo struct non provoca la conversione boxing. Questo vale anche quando lo struct viene usato come parametro di tipo e la chiamata viene eseguita tramite un'istanza del tipo di parametro di tipo. Esempio:
```csharp
using System;

struct Counter
{
    int value;

    public override string ToString() {
        value++;
        return value.ToString();
    }
}

class Program
{
    static void Test<T>() where T: new() {
        T x = new T();
        Console.WriteLine(x.ToString());
        Console.WriteLine(x.ToString());
        Console.WriteLine(x.ToString());
    }

    static void Main() {
        Test<Counter>();
    }
}
```

L'output del programma è:
```console
1
2
3
```

Sebbene non sia uno stile valido per `ToString` per avere effetti collaterali, nell'esempio viene dimostrato che non si è verificata alcuna conversione boxing per le tre chiamate di `x.ToString()`.

Analogamente, la conversione boxing non viene mai eseguita in modo implicito quando si accede a un membro in un parametro di tipo vincolato. Si supponga, ad esempio, che un'interfaccia `ICounter` contenga un metodo `Increment` che può essere utilizzato per modificare un valore. Se `ICounter` viene usato come vincolo, l'implementazione del metodo `Increment` viene chiamata con un riferimento alla variabile su cui è stato chiamato `Increment`, mai una copia boxed.

```csharp
using System;

interface ICounter
{
    void Increment();
}

struct Counter: ICounter
{
    int value;

    public override string ToString() {
        return value.ToString();
    }

    void ICounter.Increment() {
        value++;
    }
}

class Program
{
    static void Test<T>() where T: ICounter, new() {
        T x = new T();
        Console.WriteLine(x);
        x.Increment();                    // Modify x
        Console.WriteLine(x);
        ((ICounter)x).Increment();        // Modify boxed copy of x
        Console.WriteLine(x);
    }

    static void Main() {
        Test<Counter>();
    }
}
```

La prima chiamata a `Increment` modifica il valore nella variabile `x`. Questa operazione non equivale alla seconda chiamata a `Increment`, che modifica il valore in una copia boxed di `x`. Quindi, l'output del programma è:
```console
0
1
1
```

Per ulteriori informazioni su conversione boxing e unboxing, vedere [conversione boxing e](types.md#boxing-and-unboxing)unboxing.

### <a name="meaning-of-this"></a>Significato di questo

All'interno di un costruttore di istanza o di una funzione di istanza di una classe, `this` è classificato come valore. Pertanto, mentre `this` può essere utilizzato per fare riferimento all'istanza per la quale è stato richiamato il membro della funzione, non è possibile assegnare a `this` in un membro di funzione di una classe.

All'interno di un costruttore di istanza di uno struct, `this` corrisponde a un parametro `out` del tipo struct e all'interno di un membro della funzione di istanza di uno struct, `this` corrisponde a un parametro `ref` del tipo struct. In entrambi i casi, `this` è classificato come variabile ed è possibile modificare l'intero struct per cui il membro della funzione è stato richiamato assegnando a `this` o passando come parametro `ref` o `out`.

### <a name="field-initializers"></a>Inizializzatori di campo

Come descritto in [valori predefiniti](structs.md#default-values), il valore predefinito di uno struct è costituito dal valore risultante dall'impostazione di tutti i campi di tipo valore sul valore predefinito e di tutti i campi di tipo riferimento su `null`. Per questo motivo, uno struct non consente alle dichiarazioni dei campi di istanza di includere inizializzatori di variabili. Questa restrizione si applica solo ai campi di istanza. I campi statici di uno struct possono includere inizializzatori di variabile.

Esempio
```csharp
struct Point
{
    public int x = 1;  // Error, initializer not permitted
    public int y = 1;  // Error, initializer not permitted
}
```
è in errore perché le dichiarazioni dei campi di istanza includono inizializzatori di variabile.

### <a name="constructors"></a>Costruttori

A differenza di una classe, uno struct non è autorizzato a dichiarare un costruttore di istanza senza parametri. Al contrario, ogni struct dispone implicitamente di un costruttore di istanza senza parametri che restituisce sempre il valore risultante dall'impostazione di tutti i campi di tipo valore sul valore predefinito e di tutti i campi di tipo riferimento su null ([costruttori predefiniti](types.md#default-constructors)). Uno struct può dichiarare i costruttori di istanza con parametri. Esempio:
```csharp
struct Point
{
    int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

Data la dichiarazione precedente, le istruzioni
```csharp
Point p1 = new Point();
Point p2 = new Point(0, 0);
```
entrambi creano un `Point` con `x` e `y` inizializzato su zero.

Un costruttore di istanze struct non è autorizzato a includere un inizializzatore di costruttore nel formato `base(...)`.

Se il costruttore di istanze struct non specifica un inizializzatore di costruttore, la variabile `this` corrisponde a un parametro `out` del tipo struct e simile a un parametro `out`, `this` deve essere assegnato definitivamente ([assegnazione definita ](variables.md#definite-assignment)) in ogni posizione in cui viene restituito il costruttore. Se il costruttore di istanze struct specifica un inizializzatore di costruttore, la variabile `this` corrisponde a un parametro `ref` del tipo struct e simile a un parametro `ref`, `this` viene considerato assegnato in modo sicuro alla voce al corpo del costruttore. . Si consideri l'implementazione del costruttore di istanze seguente:
```csharp
struct Point
{
    int x, y;

    public int X {
        set { x = value; }
    }

    public int Y {
        set { y = value; }
    }

    public Point(int x, int y) {
        X = x;        // error, this is not yet definitely assigned
        Y = y;        // error, this is not yet definitely assigned
    }
}
```

Non è possibile chiamare alcuna funzione membro di istanza (incluse le funzioni di accesso set per le proprietà `X` e `Y`) finché non vengono assegnati definitivamente tutti i campi della struct da costruire. L'unica eccezione riguarda le proprietà implementate automaticamente ([proprietà implementate automaticamente](classes.md#automatically-implemented-properties)). Le regole di assegnazione definite ([espressioni di assegnazione semplice](variables.md#simple-assignment-expressions)) estendono specificamente l'assegnazione a una proprietà automatica di un tipo struct all'interno di un costruttore di istanza del tipo struct: tale assegnazione viene considerata un'assegnazione definita dell'oggetto nascosto campo sottostante della proprietà automatica. Di conseguenza, sono consentiti gli elementi seguenti:

```csharp
struct Point
{
    public int X { get; set; }
    public int Y { get; set; }

    public Point(int x, int y) {
        X = x;      // allowed, definitely assigns backing field
        Y = y;      // allowed, definitely assigns backing field
    }
```

### <a name="destructors"></a>Distruttori

Uno struct non è autorizzato a dichiarare un distruttore.

### <a name="static-constructors"></a>Costruttori statici

I costruttori statici per gli struct seguono la maggior parte delle stesse regole delle classi. L'esecuzione di un costruttore statico per un tipo struct viene attivata dal primo degli eventi seguenti che si verificano all'interno di un dominio dell'applicazione:

*  Viene fatto riferimento A un membro statico del tipo struct.
*  Viene chiamato un costruttore dichiarato in modo esplicito del tipo struct.

La creazione di valori predefiniti ([valori predefiniti](structs.md#default-values)) di tipi struct non attiva il costruttore statico. Un esempio è il valore iniziale degli elementi in una matrice.

## <a name="struct-examples"></a>Esempi di struct

Di seguito vengono illustrati due esempi significativi di utilizzo di tipi `struct` per creare tipi che possono essere utilizzati in modo analogo ai tipi predefiniti del linguaggio, ma con la semantica modificata.

### <a name="database-integer-type"></a>Tipo Integer database

Lo struct `DBInt` seguente implementa un tipo integer che può rappresentare il set completo di valori del tipo `int`, più uno stato aggiuntivo che indica un valore sconosciuto. Un tipo con queste caratteristiche viene comunemente usato nei database.

```csharp
using System;

public struct DBInt
{
    // The Null member represents an unknown DBInt value.

    public static readonly DBInt Null = new DBInt();

    // When the defined field is true, this DBInt represents a known value
    // which is stored in the value field. When the defined field is false,
    // this DBInt represents an unknown value, and the value field is 0.

    int value;
    bool defined;

    // Private instance constructor. Creates a DBInt with a known value.

    DBInt(int value) {
        this.value = value;
        this.defined = true;
    }

    // The IsNull property is true if this DBInt represents an unknown value.

    public bool IsNull { get { return !defined; } }

    // The Value property is the known value of this DBInt, or 0 if this
    // DBInt represents an unknown value.

    public int Value { get { return value; } }

    // Implicit conversion from int to DBInt.

    public static implicit operator DBInt(int x) {
        return new DBInt(x);
    }

    // Explicit conversion from DBInt to int. Throws an exception if the
    // given DBInt represents an unknown value.

    public static explicit operator int(DBInt x) {
        if (!x.defined) throw new InvalidOperationException();
        return x.value;
    }

    public static DBInt operator +(DBInt x) {
        return x;
    }

    public static DBInt operator -(DBInt x) {
        return x.defined ? -x.value : Null;
    }

    public static DBInt operator +(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value + y.value: Null;
    }

    public static DBInt operator -(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value - y.value: Null;
    }

    public static DBInt operator *(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value * y.value: Null;
    }

    public static DBInt operator /(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value / y.value: Null;
    }

    public static DBInt operator %(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value % y.value: Null;
    }

    public static DBBool operator ==(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value == y.value: DBBool.Null;
    }

    public static DBBool operator !=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value != y.value: DBBool.Null;
    }

    public static DBBool operator >(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value > y.value: DBBool.Null;
    }

    public static DBBool operator <(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value < y.value: DBBool.Null;
    }

    public static DBBool operator >=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value >= y.value: DBBool.Null;
    }

    public static DBBool operator <=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value <= y.value: DBBool.Null;
    }

    public override bool Equals(object obj) {
        if (!(obj is DBInt)) return false;
        DBInt x = (DBInt)obj;
        return value == x.value && defined == x.defined;
    }

    public override int GetHashCode() {
        return value;
    }

    public override string ToString() {
        return defined? value.ToString(): "DBInt.Null";
    }
}
```

### <a name="database-boolean-type"></a>Tipo di database booleano

Lo struct `DBBool` seguente implementa un tipo logico a tre valori. I valori possibili di questo tipo sono `DBBool.True`, `DBBool.False` e `DBBool.Null`, dove il membro `Null` indica un valore sconosciuto. Tali tipi logici a tre valori sono comunemente utilizzati nei database di.

```csharp
using System;

public struct DBBool
{
    // The three possible DBBool values.

    public static readonly DBBool Null = new DBBool(0);
    public static readonly DBBool False = new DBBool(-1);
    public static readonly DBBool True = new DBBool(1);

    // Private field that stores -1, 0, 1 for False, Null, True.

    sbyte value;

    // Private instance constructor. The value parameter must be -1, 0, or 1.

    DBBool(int value) {
        this.value = (sbyte)value;
    }

    // Properties to examine the value of a DBBool. Return true if this
    // DBBool has the given value, false otherwise.

    public bool IsNull { get { return value == 0; } }

    public bool IsFalse { get { return value < 0; } }

    public bool IsTrue { get { return value > 0; } }

    // Implicit conversion from bool to DBBool. Maps true to DBBool.True and
    // false to DBBool.False.

    public static implicit operator DBBool(bool x) {
        return x? True: False;
    }

    // Explicit conversion from DBBool to bool. Throws an exception if the
    // given DBBool is Null, otherwise returns true or false.

    public static explicit operator bool(DBBool x) {
        if (x.value == 0) throw new InvalidOperationException();
        return x.value > 0;
    }

    // Equality operator. Returns Null if either operand is Null, otherwise
    // returns True or False.

    public static DBBool operator ==(DBBool x, DBBool y) {
        if (x.value == 0 || y.value == 0) return Null;
        return x.value == y.value? True: False;
    }

    // Inequality operator. Returns Null if either operand is Null, otherwise
    // returns True or False.

    public static DBBool operator !=(DBBool x, DBBool y) {
        if (x.value == 0 || y.value == 0) return Null;
        return x.value != y.value? True: False;
    }

    // Logical negation operator. Returns True if the operand is False, Null
    // if the operand is Null, or False if the operand is True.

    public static DBBool operator !(DBBool x) {
        return new DBBool(-x.value);
    }

    // Logical AND operator. Returns False if either operand is False,
    // otherwise Null if either operand is Null, otherwise True.

    public static DBBool operator &(DBBool x, DBBool y) {
        return new DBBool(x.value < y.value? x.value: y.value);
    }

    // Logical OR operator. Returns True if either operand is True, otherwise
    // Null if either operand is Null, otherwise False.

    public static DBBool operator |(DBBool x, DBBool y) {
        return new DBBool(x.value > y.value? x.value: y.value);
    }

    // Definitely true operator. Returns true if the operand is True, false
    // otherwise.

    public static bool operator true(DBBool x) {
        return x.value > 0;
    }

    // Definitely false operator. Returns true if the operand is False, false
    // otherwise.

    public static bool operator false(DBBool x) {
        return x.value < 0;
    }

    public override bool Equals(object obj) {
        if (!(obj is DBBool)) return false;
        return value == ((DBBool)obj).value;
    }

    public override int GetHashCode() {
        return value;
    }

    public override string ToString() {
        if (value > 0) return "DBBool.True";
        if (value < 0) return "DBBool.False";
        return "DBBool.Null";
    }
}
```
