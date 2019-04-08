---
ms.openlocfilehash: 72d17175dfb8ef284dce6cf7e5837420fa06f16a
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229607"
---
# <a name="structs"></a>Struct

Gli struct sono simili alle classi che rappresentano le strutture di dati che possono contenere membri dati e i membri di funzione. Tuttavia, a differenza delle classi, struct sono tipi valore e non richiedono l'allocazione di heap. Una variabile di un tipo struct contiene direttamente i dati della struttura, mentre una variabile di un tipo di classe contiene un riferimento ai dati, nota come un oggetto.

I tipi struct sono particolarmente utili per strutture dati di piccole dimensioni che hanno una semantica di valori. I numeri complessi, i punti di un sistema di coordinate o le coppie chiave-valore di un dizionario sono buoni esempi di struct. Chiave da queste strutture di dati è che dispongono di alcuni membri di dati, che non richiedono l'uso dell'ereditarietà o identità referenziale e che possono essere facilmente implementati usando la semantica dei valori in cui vengono copiati il valore anziché il riferimento all'assegnazione.

Come descritto in [i tipi semplici](types.md#simple-types), i tipi semplici forniti da C#, ad esempio `int`, `double`, e `bool`, sono in effetti tutti i tipi struct. Analogamente a quanto questi tipi predefiniti struct, è anche possibile usare l'overload degli operatori per implementare nuovi tipi di "base" nel linguaggio C# e struct. Alla fine di questo capitolo vengono forniti due esempi di tali tipi ([esempi di Struct](structs.md#struct-examples)).

## <a name="struct-declarations"></a>Dichiarazioni struct

Oggetto *struct_declaration* è un *type_declaration* ([dichiarazioni di tipo](namespaces.md#type-declarations)) che dichiara un nuovo struct:

```antlr
struct_declaration
    : attributes? struct_modifier* 'partial'? 'struct' identifier type_parameter_list?
      struct_interfaces? type_parameter_constraints_clause* struct_body ';'?
    ;
```

Oggetto *struct_declaration* costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), seguito da un set facoltativo di *struct_modifier*s ([modificatori Struct](structs.md#struct-modifiers)), seguito da un oggetto facoltativo `partial` modificatore, seguita dalla parola chiave `struct` e un *identificatore* che denomina lo struct, seguito da un facoltativo *type_parameter_list* specification ([parametri di tipo](classes.md#type-parameters)), seguito da un oggetto facoltativo *struct_interfaces* specifica ([Modificatore parziale](structs.md#partial-modifier))), seguito da un oggetto facoltativo *type_parameter_constraints_clause*specifica s ([digitare i vincoli del parametro](classes.md#type-parameter-constraints)), seguito da un *struct_body* ([Struct corpo](structs.md#struct-body)), seguito facoltativamente da un punto e virgola.

### <a name="struct-modifiers"></a>Modificatori di struct

Oggetto *struct_declaration* può includere una sequenza dei modificatori struct:

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

È un errore in fase di compilazione per più volte lo stesso modificatore in una dichiarazione di struct.

I modificatori di una dichiarazione di struct hanno lo stesso significato di quelli di una dichiarazione di classe ([le dichiarazioni di classe](classes.md#class-declarations)).

### <a name="partial-modifier"></a>Modificatore parziale

Il `partial` modificatore indica che questo *struct_declaration* è una dichiarazione di tipo parziale. Più dichiarazioni di struct parziale con lo stesso nome all'interno di una dichiarazione dello spazio dei nomi o tipo di inclusione combinano per formare una dichiarazione di struct, in base alle regole specificate [i tipi parziali](classes.md#partial-types).

### <a name="struct-interfaces"></a>Interfacce di struct

Una dichiarazione di struct può includere un *struct_interfaces* specifica, in cui viene detto case della struttura viene implementata direttamente i tipi di interfaccia specificato.

```antlr
struct_interfaces
    : ':' interface_type_list
    ;
```

Le implementazioni di interfaccia sono descritte dettagliatamente nella [implementazioni di interfacce](interfaces.md#interface-implementations).

### <a name="struct-body"></a>Corpo di struct

Il *struct_body* di uno struct definisce i membri dello struct.

```antlr
struct_body
    : '{' struct_member_declaration* '}'
    ;
```

## <a name="struct-members"></a>Membri struct

I membri di uno struct è costituito da membri introdotti dalla relativa *struct_member_declaration*s e i membri ereditati dal tipo `System.ValueType`.

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

Ad eccezione delle differenze indicate nella [differenze di classi e struct](structs.md#class-and-struct-differences), le descrizioni dei membri della classe forniti [membri della classe](classes.md#class-members) attraverso [iteratori](classes.md#iterators) si applicano a uno struct anche i membri.

## <a name="class-and-struct-differences"></a>Differenze tra classi e struct

Gli struct sono diversi dalle classi in diversi modi importanti:

*  Gli struct sono tipi valore ([semantica dei valori](structs.md#value-semantics)).
*  Tutti i tipi struct ereditano in modo implicito dalla classe `System.ValueType` ([ereditarietà](structs.md#inheritance)).
*  Assegnazione a una variabile di un tipo struct consente di creare una copia del valore da assegnare ([assegnazione](structs.md#assignment)).
*  Il valore predefinito di uno struct è il valore generato impostando tutti i campi di tipo di valore al relativo valore predefinito e riferimento a tutti i campi di tipo a `null` ([valori predefiniti](structs.md#default-values)).
*  Operazioni di conversione boxing e unboxing vengono utilizzate per eseguire la conversione tra un tipo struct e `object` ([conversioni Boxing e unboxing](structs.md#boxing-and-unboxing)).
*  Il significato dei `this` è diversa per gli struct ([questo accesso](expressions.md#this-access)).
*  Le dichiarazioni di campo di istanza per uno struct non possono includere gli inizializzatori di variabili ([inizializzatori dei campi](structs.md#field-initializers)).
*  Uno struct non è consentito dichiarare un costruttore di istanza senza parametri ([costruttori](structs.md#constructors)).
*  Uno struct non è consentito dichiarare un distruttore ([distruttori](structs.md#destructors)).

### <a name="value-semantics"></a>Semantica dei valori

Gli struct sono tipi valore ([i tipi di valore](types.md#value-types)) e le seconde esiste la semantica dei valori. Le classi, d'altra parte, sono tipi di riferimento ([fanno riferimento ai tipi](types.md#reference-types)) e le seconde esiste una semantica di riferimento.

Una variabile di un tipo struct contiene direttamente i dati della struttura, mentre una variabile di un tipo di classe contiene un riferimento ai dati, nota come un oggetto. Quando uno struct `B` contiene un campo di istanza di tipo `A` e `A` è un tipo struct, tratta di un errore in fase di compilazione per `A` dipendere `B` o un tipo costruito da `B`. Uno struct `X` ***dipende direttamente*** uno struct `Y` se `X` contiene un campo di istanza di tipo `Y`. Data questa definizione, il set completo di struct da cui dipende un tipo struct è la chiusura transitiva del ***dipende direttamente*** relazione.  Esempio:
```csharp
struct Node
{
    int data;
    Node next; // error, Node directly depends on itself
}
```
è un errore perché `Node` contiene un campo di istanza dello stesso tipo.  Un altro esempio
```csharp
struct A { B b; }

struct B { C c; }

struct C { A a; }
```
è un errore perché ognuno dei tipi `A`, `B`, e `C` dipendenza reciproca.

Con le classi, è possibile che due variabili possono fare riferimento allo stesso oggetto e pertanto le operazioni su una variabile influiscano sull'oggetto a cui fa riferimento l'altra. Con i tipi struct, ogni variabile dispone di una copia dei dati (tranne nel caso delle `ref` e `out` delle variabili dei parametri), e non è possibile per le operazioni su una variabile influiscano su altra. Inoltre, poiché gli struct non sono tipi riferimento, non è possibile che i valori di un tipo struct può essere `null`.

Considerando la dichiarazione
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
Nel frammento di codice
```csharp
Point a = new Point(10, 10);
Point b = a;
a.x = 100;
System.Console.WriteLine(b.x);
```
Restituisce il valore `10`. L'assegnazione delle `a` al `b` crea una copia del valore, e `b` in questo modo è interessato dall'assegnazione a `a.x`. Aveva `Point` invece è stato dichiarato come una classe, l'output sarebbe `100` perché `a` e `b` fa riferimento allo stesso oggetto.

### <a name="inheritance"></a>Ereditarietà

Tutti i tipi struct ereditano in modo implicito dalla classe `System.ValueType`che, a sua volta eredita dalla classe `object`. Una dichiarazione di struct può specificare un elenco di interfacce implementate, ma non è possibile che una dichiarazione di struct specificare una classe di base.

I tipi struct non sono mai astratti e sono sempre implicitamente sealed. Il `abstract` e `sealed` pertanto i modificatori non sono consentiti in una dichiarazione di struct.

Poiché l'ereditarietà non è supportata per gli struct, l'accessibilità dichiarata di un membro di struct non può essere `protected` o `protected internal`.

I membri di funzione in uno struct non possono essere `abstract` oppure `virtual`e il `override` modificatore è consentito solo per eseguire l'override di metodi ereditati da `System.ValueType`.

### <a name="assignment"></a>Assegnazione

Assegnazione a una variabile di un tipo struct consente di creare una copia del valore da assegnare. Questo comportamento è diverso dall'assegnazione a una variabile di un tipo di classe, che consente di copiare il riferimento, ma non l'oggetto identificato dal riferimento.

Analogamente a un'assegnazione, quando uno struct viene passato come parametro di valore o restituito come risultato di un membro di funzione, viene creata una copia dello struct. Uno struct può essere passato per riferimento a un membro di funzione utilizzando un `ref` o `out` parametro.

Quando una proprietà o un indicizzatore di uno struct è la destinazione di un'assegnazione, l'espressione dell'istanza associata all'accesso alla proprietà o l'indicizzatore deve essere classificata come una variabile. Se l'espressione dell'istanza è classificato come un valore, si verifica un errore in fase di compilazione. Come descritto in dettaglio nelle [assegnamento semplice](expressions.md#simple-assignment).

### <a name="default-values"></a>Valori predefiniti

Come descritto in [i valori predefiniti](variables.md#default-values), diversi tipi di variabili vengono inizializzati automaticamente sul valore predefinito al momento della creazione. Per le variabili dei tipi di classe e altri tipi di riferimento, questo valore predefinito è `null`. Tuttavia, poiché gli struct sono tipi di valore non possono essere `null`, il valore predefinito di uno struct è il valore generato impostando tutti i campi di tipo di valore al relativo valore predefinito e riferimento a tutti i campi di tipo per `null`.

Che fa riferimento al `Point` uno struct dichiarato in precedenza, l'esempio
```csharp
Point[] a = new Point[100];
```
Inizializza ogni `Point` nella matrice in cui il valore generato impostando il `x` e `y` campi su zero.

Il valore predefinito di uno struct corrisponde al valore restituito dal costruttore predefinito dello struct ([costruttori predefiniti](types.md#default-constructors)). A differenza di una classe, uno struct non è consentito dichiarare un costruttore di istanza senza parametri. Al contrario, ogni struttura contiene implicitamente un costruttore di istanza senza parametri che restituisce sempre il valore risultante dall'impostazione di tutti i campi di tipo di valore al relativo valore predefinito e riferimento a tutti i campi di tipo per `null`.

Struct devono essere progettati per prendere in considerazione lo stato di inizializzazione predefinito uno stato valido. Nell'esempio
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
il costruttore di istanza definiti dall'utente consente di proteggere solo in cui si è chiamato in modo esplicito i valori null. Nei casi in cui un `KeyValuePair` variabile è soggetta a inizializzazione valore predefinito, il `key` e `value` campi saranno null e lo struct deve essere preparato a gestire questo stato.

### <a name="boxing-and-unboxing"></a>Boxing e unboxing

Un valore di un tipo di classe può essere convertito nel tipo `object` o a un tipo di interfaccia implementata dalla classe semplicemente trattando il riferimento come un altro tipo in fase di compilazione. Analogamente, un valore di tipo `object` o un valore di un tipo di interfaccia può essere convertito nuovamente in un tipo di classe senza dover modificare il riferimento (ma ovviamente un controllo del tipo in fase di esecuzione è necessario in questo caso).

Poiché gli struct non sono tipi riferimento, queste operazioni vengono implementate in modo diverso per i tipi struct. Quando un valore di un tipo struct viene convertito nel tipo `object` o a un tipo di interfaccia implementata da struct, viene eseguita un'operazione di conversione boxing. Analogamente, quando un valore di tipo `object` o un valore di un tipo di interfaccia viene riconvertito in un tipo struct, viene eseguita un'operazione unboxing. La differenza principale con le stesse operazioni in tipi di classi è che la conversione boxing e unboxing copia il valore della struttura all'interno o all'esterno dell'istanza sottoposto a conversione boxing. In seguito un'operazione di conversione boxing o unboxing, di conseguenza, le modifiche apportate allo struct unboxed non vengono riflesse nello struct boxed.

Quando un tipo struct esegue l'override di un metodo virtuale ereditato da `System.Object` (ad esempio `Equals`, `GetHashCode`, o `ToString`), la chiamata al metodo virtuale tramite un'istanza del tipo di struttura non provoca conversione boxing si verifichi. Questo vale anche quando la struttura viene usata come parametro di tipo e ciò avviene tramite un'istanza del tipo di parametro di tipo. Ad esempio:
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
```
1
2
3
```

Sebbene sia lo stile non valido per `ToString` per avere effetti collaterali, nell'esempio viene illustrato che si è verificata alcuna conversione boxing per le tre chiamate di `x.ToString()`.

Analogamente, la conversione boxing mai in modo implicito quando l'accesso a un membro in un parametro di tipo vincolato. Ad esempio, si supponga che un'interfaccia `ICounter` contiene un metodo `Increment` che può essere usato per modificare un valore. Se `ICounter` viene usato come vincolo, l'implementazione del `Increment` viene chiamato con un riferimento alla variabile che `Increment` è stato chiamato su, mai una copia boxed.

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

La prima chiamata a `Increment` modifica il valore nella variabile `x`. Ciò non equivale alla seconda chiamata a `Increment`, che modifica il valore in una copia boxed di `x`. Di conseguenza, l'output del programma è:
```
0
1
1
```

Per altre informazioni dettagliate sulle conversioni boxing e unboxing, vedere [conversioni Boxing e unboxing](types.md#boxing-and-unboxing).

### <a name="meaning-of-this"></a>Significato di questo oggetto

All'interno di un costruttore di istanza o un membro di funzione di istanza di una classe, `this` viene classificata come un valore. Di conseguenza, mentre `this` può essere utilizzato per fare riferimento all'istanza per la quale è stato richiamato il membro di funzione, non è possibile assegnare a `this` in un membro di funzione di una classe.

All'interno di un costruttore di istanze di uno struct, `this` corrisponde a un `out` parametro del tipo struct e all'interno di un membro di funzione di istanza di uno struct `this` corrisponde a un `ref` parametro del tipo struct. In entrambi i casi `this` viene classificato come una variabile, ed è possibile modificare l'intero struct per cui il membro di funzione è stato richiamato assegnando ai `this` oppure passando come una `ref` o `out` parametro.

### <a name="field-initializers"></a>Inizializzatori di campo

Come descritto in [i valori predefiniti](structs.md#default-values), il valore predefinito di uno struct è costituito il valore risultante dall'impostazione di tutti i campi di tipo di valore al relativo valore predefinito e riferimento a tutti i campi di tipo per `null`. Per questo motivo, uno struct non supporta le dichiarazioni di campo di istanza per includere gli inizializzatori di variabili. Questa restrizione si applica solo ai campi di istanza. Per includere gli inizializzatori di variabili sono consentiti campi statici di un tipo struct.

L'esempio
```csharp
struct Point
{
    public int x = 1;  // Error, initializer not permitted
    public int y = 1;  // Error, initializer not permitted
}
```
è in errore perché le dichiarazioni di campo di istanza includono gli inizializzatori di variabili.

### <a name="constructors"></a>Costruttori

A differenza di una classe, uno struct non è consentito dichiarare un costruttore di istanza senza parametri. Al contrario, ogni struttura contiene implicitamente un costruttore di istanza senza parametri che restituisce sempre il valore risultante dall'impostazione di tutti i campi di tipo valore sul relativo valore predefinito e riferimento a tutti i campi di tipo null ([costruttoripredefiniti](types.md#default-constructors)). Uno struct possa dichiarare costruttori di istanze con parametri. Esempio:
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

Considerando la dichiarazione precedente, le istruzioni
```csharp
Point p1 = new Point();
Point p2 = new Point(0, 0);
```
che consentono di creare un `Point` con `x` e `y` inizializzato su zero.

Un costruttore di istanza struct non è consentito per includere un inizializzatore del costruttore del form `base(...)`.

Se il costruttore di istanza struct non specifica un inizializzatore del costruttore, il `this` variabile corrisponde a un `out` parametro del tipo struct ed è analogo a un `out` parametro, `this` deve essere assegnato definitivamente ( [Assegnazione certa](variables.md#definite-assignment)) in ogni posizione in cui viene restituito il costruttore. Se il costruttore di istanza struct specifica un inizializzatore del costruttore, il `this` variabile corrisponde a un `ref` parametro del tipo struct ed è analogo a un `ref` parametro, `this` viene considerato assegnata in modo voce nel corpo del costruttore. Prendere in considerazione l'implementazione di costruttore di istanza seguente:
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

Nessuna funzione membro di istanza (tra cui le funzioni di accesso set per le proprietà `X` e `Y`) può essere chiamata fino a quando non sono stati assegnati sicuramente tutti i campi dello struct in fase di costruzione. L'unica eccezione riguarda le proprietà implementate automaticamente ([proprietà implementate automaticamente](classes.md#automatically-implemented-properties)). Le regole di assegnazione certa ([espressioni di assegnazione semplice](variables.md#simple-assignment-expressions)) in modo specifico esentare assegnazione a una proprietà automatica di un tipo struct all'interno di un costruttore di istanze di quel tipo di struct: tale assegnazione viene considerata una certa assegnazione del campo nascosto sottostante della proprietà automatica. Di conseguenza, è consentita la seguente:

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

Uno struct non è consentito dichiarare un distruttore.

### <a name="static-constructors"></a>Costruttori statici

I costruttori statici per le strutture seguono la maggior parte delle stesse regole per le classi. L'esecuzione di un costruttore statico per un tipo struct viene attivato dal primo degli eventi seguenti che si verificano all'interno di un dominio dell'applicazione:

*  Un membro statico del tipo struct viene fatto riferimento.
*  Viene chiamato un costruttore dichiarato in modo esplicito del tipo struct.

La creazione di valori predefiniti ([i valori predefiniti](structs.md#default-values)) dello struct tipi non attiva il costruttore statico. (Un esempio di questo è il valore iniziale di elementi in una matrice).

## <a name="struct-examples"></a>Esempi di struct

Di seguito viene illustrato due esempi significativi dell'uso di `struct` tipi per creare tipi utilizzabili in modo analogo a quelli predefiniti del linguaggio, ma con una semantica modificata.

### <a name="database-integer-type"></a>Tipo integer del database

Il `DBInt` struct seguente implementa un tipo integer che può rappresentare il set completo di valori del `int` tipo, più uno stato aggiuntivo che indica un valore sconosciuto. Un tipo con queste caratteristiche viene comunemente utilizzato nei database.

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

### <a name="database-boolean-type"></a>Tipo booleano di database

Il `DBBool` struct seguente implementa un tipo di logico a tre valori. I valori possibili di questo tipo sono `DBBool.True`, `DBBool.False`, e `DBBool.Null`, dove il `Null` membro indica un valore sconosciuto. Tali tipi di logici a tre valori vengono comunemente usati nel database.

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
