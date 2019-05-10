---
ms.openlocfilehash: 0a09585f4f885647230354c66a2449abb7ef1f44
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "64488769"
---
# <a name="interfaces"></a>Interfacce

Un'interfaccia definisce un contratto. Una classe o struct che implementa un'interfaccia deve aderire al rispettivo contratto. Un'interfaccia può ereditare da più interfacce di base e una classe o struct può implementare più interfacce.

Le interfacce possono contenere metodi, proprietà, eventi e indicizzatori. L'interfaccia stessa non fornisce le implementazioni per i membri che lo definisce. L'interfaccia specifica semplicemente i membri che devono essere forniti dalle classi o struct che implementa l'interfaccia.

## <a name="interface-declarations"></a>Dichiarazioni di interfaccia

Un' *interface_declaration* è un *type_declaration* ([dichiarazioni di tipo](namespaces.md#type-declarations)) che dichiara un nuovo tipo di interfaccia.

```antlr
interface_declaration
    : attributes? interface_modifier* 'partial'? 'interface'
      identifier variant_type_parameter_list? interface_base?
      type_parameter_constraints_clause* interface_body ';'?
    ;
```

Un' *interface_declaration* costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), seguito da un set facoltativo di *interface_modifier*s ([modificatori dell'interfaccia](interfaces.md#interface-modifiers)), seguito da un oggetto facoltativo `partial` modificatore, seguita dalla parola chiave `interface` e una *identificatore* che denomina l'interfaccia, seguita da un oggetto facoltativo *variant_type_parameter_list* specification ([elenchi di parametri di tipo variante](interfaces.md#variant-type-parameter-lists)), seguito da un oggetto facoltativo *interface_base* Specifica ([interfacce di Base](interfaces.md#base-interfaces)), seguito da un oggetto facoltativo *type_parameter_constraints_clause*specifica s ([digitare i vincoli del parametro](classes.md#type-parameter-constraints)) , seguita da un *interface_body* ([interfaccia corpo](interfaces.md#interface-body)), seguito facoltativamente da un punto e virgola.

### <a name="interface-modifiers"></a>Modificatori di interfaccia

Un' *interface_declaration* può includere una sequenza dei modificatori di interfaccia:

```antlr
interface_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | interface_modifier_unsafe
    ;
```

È un errore in fase di compilazione per più volte lo stesso modificatore in una dichiarazione di interfaccia.

Il `new` modificatore è consentito solo in interfacce definite all'interno di una classe. Specifica che l'interfaccia nasconde un membro ereditato con lo stesso nome, come descritto in [il nuovo modificatore](classes.md#the-new-modifier).

Il `public`, `protected`, `internal`, e `private` modificatori determinano l'accessibilità dell'interfaccia. A seconda del contesto in cui si verifica la dichiarazione dell'interfaccia, solo alcuni di questi modificatori è ammessa ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)).

### <a name="partial-modifier"></a>Modificatore parziale

Il `partial` modificatore indica che questo *interface_declaration* è una dichiarazione di tipo parziale. Più dichiarazioni di interfaccia parziale con lo stesso nome all'interno di una dichiarazione dello spazio dei nomi o tipo di inclusione combinano al form una dichiarazione di interfaccia, in base alle regole specificate [i tipi parziali](classes.md#partial-types).

### <a name="variant-type-parameter-lists"></a>Elenchi di parametri di tipo Variant

Gli elenchi di parametri di tipo Variant possono verificarsi solo sui tipi di interfaccia e delegato. La differenza rispetto a ordinario *type_parameter_list*s è facoltativa *variance_annotation* su ogni parametro di tipo.

```antlr
variant_type_parameter_list
    : '<' variant_type_parameters '>'
    ;

variant_type_parameters
    : attributes? variance_annotation? type_parameter
    | variant_type_parameters ',' attributes? variance_annotation? type_parameter
    ;

variance_annotation
    : 'in'
    | 'out'
    ;
```

Se l'annotazione della varianza `out`, il parametro di tipo è detto ***covariante***. Se l'annotazione della varianza `in`, il parametro di tipo è detto ***controvariante***. Se è presente alcuna annotazione della varianza, il parametro di tipo viene detto ***invariante***.

Nell'esempio
```csharp
interface C<out X, in Y, Z> 
{
  X M(Y y);
  Z P { get; set; }
}
```
`X` è covariante, `Y` è controvariante e `Z` è invariante.

#### <a name="variance-safety"></a>Protezione della varianza

L'occorrenza delle annotazioni di varianza nell'elenco dei parametri di tipo di un tipo consente di limitare le posizioni in cui possono verificarsi i tipi all'interno della dichiarazione di tipo.

Un tipo `T` viene ***output unsafe*** se una delle seguenti condizioni:

*  `T` è un parametro di tipo controvariante
*  `T` è un tipo di matrice con un tipo di elemento di output non sicuri
*  `T` è un tipo interfaccia o delegato `S<A1,...,Ak>` costruito da un tipo generico `S<X1,...,Xk>` where per almeno uno `Ai` contiene uno dei seguenti:
   * `Xi` è covariante o invarianti e `Ai` è output unsafe.
   * `Xi` è controvariante o invariante e `Ai` input-safe.
   
Un tipo `T` viene ***input unsafe*** se una delle seguenti condizioni:

*  `T` è un parametro di tipo covariante
*  `T` è un tipo di matrice con un tipo di elemento di tipo unsafe input
*  `T` è un tipo interfaccia o delegato `S<A1,...,Ak>` costruito da un tipo generico `S<X1,...,Xk>` where per almeno uno `Ai` contiene uno dei seguenti:
   * `Xi` è covariante o invarianti e `Ai` è di tipo unsafe input.
   * `Xi` è controvariante o invariante e `Ai` è output unsafe.

Intuitivamente, un tipo di output-unsafe non è consentito in una posizione di output e un tipo unsafe-input non è consentito in una posizione di input.

È un tipo ***output-safe*** se non è unsafe, output e ***indipendente input*** se non è tipo unsafe input.

#### <a name="variance-conversion"></a>Conversione della varianza

Lo scopo di annotazioni della varianza è fornire più flessibili (ma ancora indipendente dai tipi) conversioni in tipi di interfaccia e delegato. A questo fine le definizioni di implicito ([conversioni implicite](conversions.md#implicit-conversions)) e le conversioni esplicite ([conversioni esplicite](conversions.md#explicit-conversions)) apportare Usa la nozione di convertibilità della varianza, che viene definita come segue:

Un tipo `T<A1,...,An>` convertibile la varianza è a un tipo `T<B1,...,Bn>` se `T` è un'interfaccia o un tipo delegato dichiarato con i parametri di tipo variant `T<X1,...,Xn>`e per ogni parametro di tipo variant `Xi` uno dei seguenti contiene:

*  `Xi` è una covariante e non esiste una conversione implicita di identità o di riferimento da `Ai` a `Bi`
*  `Xi` è controvariante e un riferimento implicito o non esiste conversione di identità da `Bi` a `Ai`
*  `Xi` è invariante e un'identità non esiste conversione da `Ai` a `Bi`

### <a name="base-interfaces"></a>Interfacce di base

Un'interfaccia può ereditare da zero o più tipi di interfaccia, che vengono chiamati i ***interfacce di base esplicite*** dell'interfaccia. Quando un'interfaccia presenta uno o più interfacce di base esplicitare, nella dichiarazione di tale interfaccia, l'identificatore di interfaccia è seguito da due punti e virgola da virgole dei tipi di interfaccia di base.

```antlr
interface_base
    : ':' interface_type_list
    ;
```

Per un tipo di interfaccia costruita, interfacce di base esplicite costituite prendendo le dichiarazioni di interfaccia di base esplicite nella dichiarazione del tipo generico e sostituzione, per ogni *type_parameter* nell'interfaccia di base dichiarazione corrispondente *type_argument* del tipo costruito.

Le interfacce di base esplicitare di un'interfaccia devono essere accessibile almeno quanto l'interfaccia stessa ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)). Ad esempio, si tratta di un errore in fase di compilazione per specificare una `private` o `internal` dell'interfaccia nel *interface_base* di un `public` interfaccia.

Tratta di un errore in fase di compilazione per un'interfaccia ereditare direttamente o indirettamente da se stessa.

Il ***interfacce di base*** di un'interfaccia sono interfacce di base esplicite e le interfacce di base. In altre parole, il set di interfacce di base è la chiusura transitiva completata di interfacce di base esplicite, le interfacce di base esplicite e così via. Un'interfaccia eredita tutti i membri delle relative interfacce di base. Nell'esempio
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

interface IListBox: IControl
{
    void SetItems(string[] items);
}

interface IComboBox: ITextBox, IListBox {}
```
le interfacce di base di `IComboBox` vengono `IControl`, `ITextBox`, e `IListBox`.

In altre parole, il `IComboBox` precedente interfaccia eredita membri `SetText` e `SetItems` nonché `Paint`.

Ogni interfaccia di base di un'interfaccia deve essere indipendente output ([safety varianza](interfaces.md#variance-safety)). Una classe o struct che implementa un'interfaccia in modo implicito anche implementa tutte le interfacce di base.

### <a name="interface-body"></a>Corpo di interfaccia

Il *interface_body* di un'interfaccia definisce i membri dell'interfaccia.

```antlr
interface_body
    : '{' interface_member_declaration* '}'
    ;
```

## <a name="interface-members"></a>Membri di interfaccia

I membri di un'interfaccia sono i membri ereditati da interfacce di base e i membri dichiarati dall'interfaccia stessa.

```antlr
interface_member_declaration
    : interface_method_declaration
    | interface_property_declaration
    | interface_event_declaration
    | interface_indexer_declaration
    ;
```

Una dichiarazione di interfaccia può dichiarare zero o più membri. I membri di un'interfaccia devono essere metodi, proprietà, eventi o gli indicizzatori. Un'interfaccia non può contenere costanti, campi, operatori, costruttori di istanze, distruttori o tipi, né è un'interfaccia contengono membri statici di alcun tipo.

Tutti i membri di interfaccia dispongono in modo implicito accesso pubblico. È un errore in fase di compilazione per le dichiarazioni di membro di interfaccia includere tutti i modificatori. In particolare, i membri di interfacce non possono essere dichiarati con i modificatori `abstract`, `public`, `protected`, `internal`, `private`, `virtual`, `override`, o `static`.

L'esempio
```csharp
public delegate void StringListEvent(IStringList sender);

public interface IStringList
{
    void Add(string s);
    int Count { get; }
    event StringListEvent Changed;
    string this[int index] { get; set; }
}
```
dichiara un'interfaccia che contiene tutti i possibili tipi di membri: Un metodo, una proprietà, un evento e un indicizzatore.

Un' *interface_declaration* crea un nuovo spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)) e il *interface_member_declaration*s contenuti immediatamente il *interface_declaration* introducono nuovi membri in tale spazio di dichiarazione. Le regole seguenti riguardano *interface_member_declaration*s:

*  Il nome di un metodo deve essere diverso dai nomi di tutte le proprietà e gli eventi dichiarati nell'interfaccia stessa. Inoltre, la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) di un metodo deve essere diversa dalle firme di tutti gli altri metodi dichiarati nell'interfaccia stessa, e due i metodi dichiarati nell'interfaccia stessa potrebbero non avere firme che differiscono unicamente per `ref` e `out`.
*  Il nome di una proprietà o evento deve essere diverso dai nomi di tutti gli altri membri dichiarati nell'interfaccia stessa.
*  È necessario che la firma di un indicizzatore sia diversa dalle firme di tutti gli altri indicizzatori dichiarati nella stessa interfaccia.

I membri ereditati di un'interfaccia sono specificamente non fanno parte dello spazio di dichiarazione dell'interfaccia. Di conseguenza, un'interfaccia può dichiarare un membro con lo stesso nome o firma di un membro ereditato. In questo caso, viene definito il membro di interfaccia derivata per nascondere il membro di interfaccia di base. Se si nasconde un membro ereditato non è considerato un errore, ma comporta il compilatore emette un avviso. Per eliminare l'avviso, è necessario includere la dichiarazione del membro di interfaccia derivata una `new` modificatore per indicare che il membro derivato deve nascondere il membro di base. Questo argomento viene descritto dettagliatamente nella [nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance).

Se un `new` modificatore viene inclusa in una dichiarazione che non nasconde un membro ereditato, viene generato un avviso in tal senso. Questo avviso viene eliminato tramite la rimozione di `new` modificatore.

Si noti che i membri nella classe `object` non lo sono, in senso stretto, membri di un'interfaccia ([membri di interfaccia](interfaces.md#interface-members)). Tuttavia, i membri nella classe `object` sono disponibili tramite ricerca di membri in qualsiasi tipo di interfaccia ([ricerca di membri](expressions.md#member-lookup)).

### <a name="interface-methods"></a>Metodi di interfaccia

I metodi di interfaccia vengono dichiarati usando *interface_method_declaration*s:

```antlr
interface_method_declaration
    : attributes? 'new'? return_type identifier type_parameter_list
      '(' formal_parameter_list? ')' type_parameter_constraints_clause* ';'
    ;
```

Il *attributi*, *return_type*, *identificatore*, e *formal_parameter_list* hanno lo stesso di una dichiarazione di metodo di interfaccia vale a dire come quelli di una dichiarazione di metodo in una classe ([metodi](classes.md#methods)). Una dichiarazione di metodo di interfaccia non è consentita specificare un corpo del metodo e la dichiarazione di conseguenza termina sempre con un punto e virgola.

Ogni tipo di parametro formali di un metodo di interfaccia deve essere indipendente input ([safety varianza](interfaces.md#variance-safety)), e il tipo restituito deve essere `void` o output-safe. Inoltre, ogni vincolo di tipo classe, il vincolo di tipo di interfaccia e il vincolo di parametro di tipo per qualsiasi parametro di tipo del metodo deve essere indipendente input.

Queste regole assicurano che eventuali covariante o controvariante utilizzo dell'interfaccia rimane indipendente dai tipi. Ad esempio,
```csharp
interface I<out T> { void M<U>() where U : T; }
```
non è valido perché l'utilizzo delle `T` come un vincolo del parametro di tipo in `U` non input-safe.

Precedenti questa restrizione non è possibile violare la sicurezza di tipo nel modo seguente:
```csharp
class B {}
class D : B{}
class E : B {}
class C : I<D> { public void M<U>() {...} }
...
I<B> b = new C();
b.M<E>();
```
Si tratta in realtà una chiamata a `C.M<E>`. Ma tale chiamata richiede che `E` derivano da `D`, pertanto l'indipendenza dai tipi venga violata qui.

### <a name="interface-properties"></a>Proprietà dell'interfaccia

Le proprietà dell'interfaccia vengono dichiarate usando *interface_property_declaration*s:

```antlr
interface_property_declaration
    : attributes? 'new'? type identifier '{' interface_accessors '}'
    ;

interface_accessors
    : attributes? 'get' ';'
    | attributes? 'set' ';'
    | attributes? 'get' ';' attributes? 'set' ';'
    | attributes? 'set' ';' attributes? 'get' ';'
    ;
```

Il *attributi*, *tipo*, e *identificatore* di una dichiarazione di proprietà di interfaccia abbia lo stesso significato di quelli di una dichiarazione di proprietà in una classe ([ Proprietà](classes.md#properties)).

Funzioni di accesso di una dichiarazione di proprietà dell'interfaccia corrispondono alle funzioni di accesso di una dichiarazione di proprietà di classe ([funzioni di accesso](classes.md#accessors)), ad eccezione del fatto che il corpo della funzione di accesso deve essere sempre un punto e virgola. Di conseguenza, le funzioni di accesso indica semplicemente se la proprietà è di lettura / scrittura, sola lettura o in sola lettura.

Il tipo di una proprietà dell'interfaccia deve essere output-safe se è presente una funzione di accesso get e deve essere l'input indipendente se è presente una funzione di accesso set.

### <a name="interface-events"></a>Eventi di interfaccia

Gli eventi di interfaccia vengono dichiarati usando *interface_event_declaration*s:

```antlr
interface_event_declaration
    : attributes? 'new'? 'event' type identifier ';'
    ;
```

Il *attributi*, *tipo*, e *identificatore* di una dichiarazione di evento di interfaccia abbia lo stesso significato di quelli di una dichiarazione di evento in una classe ([eventi ](classes.md#events)).

Il tipo di un evento di interfaccia deve essere di input-safe.

### <a name="interface-indexers"></a>Indicizzatori nelle interfacce

Indicizzatori nelle interfacce vengono dichiarate utilizzando *interface_indexer_declaration*s:

```antlr
interface_indexer_declaration
    : attributes? 'new'? type 'this' '[' formal_parameter_list ']' '{' interface_accessors '}'
    ;
```

Il *attributi*, *tipo*, e *formal_parameter_list* di una dichiarazione di interfaccia dell'indicizzatore abbia lo stesso significato di quelli di una dichiarazione di indicizzatore in una classe ([ Gli indicizzatori](classes.md#indexers)).

Le funzioni di accesso di una dichiarazione di indicizzatore interfaccia corrispondono alle funzioni di accesso di una dichiarazione di indicizzatore di classe ([indicizzatori](classes.md#indexers)), ad eccezione del fatto che il corpo della funzione di accesso deve essere sempre un punto e virgola. Di conseguenza, le funzioni di accesso indica semplicemente se l'indicizzatore è in lettura / scrittura, sola lettura o in sola lettura.

Tutti i tipi di parametri formali di un indicizzatore di interfaccia devono essere di input-safe. Inoltre, qualsiasi `out` o `ref` i tipi di parametro formale devono anche essere output-safe. Si noti che anche `out` parametri devono essere di input-safe, a causa di una limitazione della piattaforma sottostante di esecuzione.

Il tipo di un indicizzatore di interfaccia deve essere output-safe se è presente una funzione di accesso get e deve essere l'input indipendente se è presente una funzione di accesso set.

### <a name="interface-member-access"></a>Accesso ai membri di interfaccia

I membri di interfaccia sono accessibili tramite l'accesso ai membri ([accesso al membro](expressions.md#member-access)) e l'accesso dell'indicizzatore ([accesso all'indicizzatore](expressions.md#indexer-access)) espressioni nel formato `I.M` e `I[A]`, dove `I` è un tipo di interfaccia, `M` è un metodo, proprietà o evento di quel tipo di interfaccia, e `A` è un elenco di argomenti dell'indicizzatore.

Per le interfacce che sono strettamente a ereditarietà singola (ogni interfaccia nella catena di ereditarietà ha esattamente zero o a un'interfaccia di base diretta), gli effetti della ricerca di membri ([ricerca di membri](expressions.md#member-lookup)), chiamata al metodo ([ Le chiamate ai metodi](expressions.md#method-invocations)) e l'accesso dell'indicizzatore ([accesso all'indicizzatore](expressions.md#indexer-access)) le regole sono esattamente identico a quello di classi e struct: Più derivati Nascondi membri meno derivata i membri con lo stesso nome o firma. Tuttavia, per le interfacce di ereditarietà multipla, le ambiguità possono verificarsi quando due o più interfacce di base correlate dichiarano membri con lo stesso nome o firma. Questa sezione illustra alcuni esempi di situazioni di questo tipo. In tutti i casi, è possibile utilizzare cast espliciti per risolvere le ambiguità.

Nell'esempio
```csharp
interface IList
{
    int Count { get; set; }
}

interface ICounter
{
    void Count(int i);
}

interface IListCounter: IList, ICounter {}

class C
{
    void Test(IListCounter x) {
        x.Count(1);                  // Error
        x.Count = 1;                 // Error
        ((IList)x).Count = 1;        // Ok, invokes IList.Count.set
        ((ICounter)x).Count(1);      // Ok, invokes ICounter.Count
    }
}
```
le prime due istruzioni causano errori di compilazione perché la ricerca di membri ([ricerca di membri](expressions.md#member-lookup)) della `Count` in `IListCounter` è ambiguo. Come illustrato nell'esempio, eseguendo il cast viene risolta l'ambiguità `x` nel tipo di interfaccia di base appropriato. Tali cast non hanno costi alcuna fase di esecuzione, ovvero sono costituite semplicemente visualizzazione dell'istanza come un tipo meno derivato in fase di compilazione.

Nell'esempio
```csharp
interface IInteger
{
    void Add(int i);
}

interface IDouble
{
    void Add(double d);
}

interface INumber: IInteger, IDouble {}

class C
{
    void Test(INumber n) {
        n.Add(1);                // Invokes IInteger.Add
        n.Add(1.0);              // Only IDouble.Add is applicable
        ((IInteger)n).Add(1);    // Only IInteger.Add is a candidate
        ((IDouble)n).Add(1);     // Only IDouble.Add is a candidate
    }
}
```
la chiamata `n.Add(1)` seleziona `IInteger.Add` applicando le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution). Analogamente alla chiamata `n.Add(1.0)` seleziona `IDouble.Add`. Quando si inseriscono cast espliciti, non vi è solo un candidato (metodo) e pertanto non esistono ambiguità.

Nell'esempio
```csharp
interface IBase
{
    void F(int i);
}

interface ILeft: IBase
{
    new void F(int i);
}

interface IRight: IBase
{
    void G();
}

interface IDerived: ILeft, IRight {}

class A
{
    void Test(IDerived d) {
        d.F(1);                 // Invokes ILeft.F
        ((IBase)d).F(1);        // Invokes IBase.F
        ((ILeft)d).F(1);        // Invokes ILeft.F
        ((IRight)d).F(1);       // Invokes IBase.F
    }
}
```
il `IBase.F` membro viene nascosto per il `ILeft.F` membro. La chiamata `d.F(1)` consente di selezionare `ILeft.F`, anche se `IBase.F` sembra non essere nascosto nel percorso di accesso che assista `IRight`.

La regola intuitiva per nascondere elementi nelle interfacce di ereditarietà multipla è semplicemente la seguente: Se un membro viene nascosto in qualsiasi percorso di accesso, è nascosto in tutti i percorsi di accesso. Poiché il percorso di accesso da `IDerived` per `ILeft` a `IBase` nasconde `IBase.F`, il membro viene nascosto anche nel percorso di accesso da `IDerived` per `IRight` a `IBase`.

## <a name="fully-qualified-interface-member-names"></a>Nomi dei membri di interfaccia completo

Un membro di interfaccia viene a volte fa riferimento al relativo ***nome completo***. Il nome completo di un membro di interfaccia costituita dal nome dell'interfaccia in cui il membro è dichiarato, seguito da un punto, seguito dal nome del membro. Il nome completo di un membro fa riferimento all'interfaccia in cui viene dichiarato il membro. Ad esempio, date le dichiarazioni
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}
```
il nome completo del `Paint` viene `IControl.Paint` e specificare il nome completo del `SetText` è `ITextBox.SetText`.

Nell'esempio precedente, non è possibile fare riferimento a `Paint` come `ITextBox.Paint`.

Quando un'interfaccia fa parte di uno spazio dei nomi, il nome completo di un membro di interfaccia include lo spazio dei nomi. Esempio:
```csharp
namespace System
{
    public interface ICloneable
    {
        object Clone();
    }
}
```

Questo caso, il nome completo del `Clone` metodo `System.ICloneable.Clone`.

## <a name="interface-implementations"></a>Implementazioni di interfacce

Le interfacce possono essere implementate da classi e struct. Per indicare che una classe o struct direttamente implementa un'interfaccia, l'identificatore di interfaccia è incluso nell'elenco delle classi base della classe o struct. Ad esempio:
```csharp
interface ICloneable
{
    object Clone();
}

interface IComparable
{
    int CompareTo(object other);
}

class ListEntry: ICloneable, IComparable
{
    public object Clone() {...}
    public int CompareTo(object other) {...}
}
```

Una classe o struct che implementa direttamente anche direttamente un'interfaccia implementa tutte le interfacce di base in modo implicito. Questo vale anche se la classe o struct non vengono elencati in modo esplicito tutte le interfacce di base nell'elenco delle classi base. Ad esempio:
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

class TextBox: ITextBox
{
    public void Paint() {...}
    public void SetText(string text) {...}
}
```

In questo caso, classe `TextBox` implementa sia `IControl` e `ITextBox`.

Quando una classe `C` direttamente implementa un'interfaccia, tutte le classi derivate da C anche implementarla l'interfaccia in modo implicito. Le interfacce di base specificate in una dichiarazione di classe possono essere tipi di interfaccia costruita ([i tipi costruiti](types.md#constructed-types)). Un'interfaccia di base non può essere un parametro di tipo di per sé, ma può comportare i parametri di tipo che rientrano nell'ambito. Il codice seguente viene illustrato come una classe può implementare ed estendere i tipi costruiti:
```csharp
class C<U,V> {}

interface I1<V> {}

class D: C<string,int>, I1<string> {}

class E<T>: C<int,T>, I1<T> {}
```

Le interfacce di base di una dichiarazione di classe generica devono soddisfare la regola di univocità descritto nella [univocità delle interfacce implementate](interfaces.md#uniqueness-of-implemented-interfaces).

### <a name="explicit-interface-member-implementations"></a>Implementazioni di membri di interfaccia esplicita

Ai fini dell'implementazione di interfacce, una classe o struct possono dichiarare ***implementazioni esplicite dell'interfaccia membro***. Un'implementazione di membro di interfaccia esplicita è una dichiarazione di metodo, proprietà, eventi o dell'indicizzatore che fa riferimento a un nome di membro completo dell'interfaccia. Esempio:
```csharp
interface IList<T>
{
    T[] GetElements();
}

interface IDictionary<K,V>
{
    V this[K key];
    void Add(K key, V value);
}

class List<T>: IList<T>, IDictionary<int,T>
{
    T[] IList<T>.GetElements() {...}
    T IDictionary<int,T>.this[int index] {...}
    void IDictionary<int,T>.Add(int index, T value) {...}
}
```

Di seguito `IDictionary<int,T>.this` e `IDictionary<int,T>.Add` è interfacce esplicite le implementazioni di membri.

In alcuni casi, il nome di un membro di interfaccia potrebbe non essere appropriato per la classe di implementazione, in cui il membro può essere implementato tramite l'implementazione di membro di interfaccia esplicita. Probabilmente è necessario implementare una classe che implementa un'astrazione di file, ad esempio, un `Close` funzione membro che ha l'effetto di rilascio della risorsa di file e implementare le `Dispose` metodo il `IDisposable` usando un'interfaccia esplicita dell'interfaccia implementazione di un membro:
```csharp
interface IDisposable
{
    void Dispose();
}

class MyFile: IDisposable
{
    void IDisposable.Dispose() {
        Close();
    }

    public void Close() {
        // Do what's necessary to close the file
        System.GC.SuppressFinalize(this);
    }
}
```

Non è possibile accedere a un'implementazione di membro di interfaccia esplicita tramite il nome completo in una chiamata al metodo, l'accesso alle proprietà o l'accesso dell'indicizzatore. Un'implementazione di membro di interfaccia esplicita è accessibile solo tramite un'istanza dell'interfaccia e in tal caso fa semplicemente il nome del membro.

Tratta di un errore in fase di compilazione per un'implementazione di membro di interfaccia esplicita includere modificatori di accesso, ed è un errore in fase di compilazione per includere i modificatori `abstract`, `virtual`, `override`, o `static`.

Implementazioni esplicite dell'interfaccia membro hanno caratteristiche di accessibilità diversa rispetto ad altri membri. Poiché tali implementazioni mai sono accessibili tramite il relativo nome completo in una chiamata al metodo o un accesso a proprietà, sono in un certo senso privato. Tuttavia, dal momento che sia possibile accedervi tramite un'istanza dell'interfaccia, sono in un certo senso anche pubblico.

Implementazioni esplicite dell'interfaccia membro hanno due scopi principali:

*  Poiché tali implementazioni non sono accessibili tramite le istanze di classe o struct, consentono di implementazioni di interfaccia da escludere dall'interfaccia pubblica di una classe o struct. Ciò è particolarmente utile quando una classe o struct implementa un'interfaccia interna di alcun interesse a un consumer di quella classe o struct.
*  Tali implementazioni consentono di risoluzione dell'ambiguità dei membri di interfaccia con la stessa firma. Senza tali implementazioni sarebbe impossibile ritestare per una classe o struct avere diverse implementazioni di membri con la stessa firma dell'interfaccia e il tipo restituito, come lo sarebbe impossibile per una classe o struct qualsiasi implementazione in tutti i membri di interfaccia con la stessa firma ma con tipi restituiti diversi.

Per un'implementazione esplicita dell'interfaccia membro sia valida, la classe o struct deve denominare un'interfaccia nel relativo elenco di classi base che include il membro il cui nome completo del tipo e i tipi di parametro corrispondono esattamente a quelli dei membri di interfaccia esplicita implementazione. Di conseguenza, nella classe seguente
```csharp
class Shape: ICloneable
{
    object ICloneable.Clone() {...}
    int IComparable.CompareTo(object other) {...}    // invalid
}
```
la dichiarazione di `IComparable.CompareTo` genera un errore in fase di compilazione in quanto `IComparable` non è elencato nell'elenco delle classi base di `Shape` e non è un'interfaccia di base di `ICloneable`. Allo stesso modo, nelle dichiarazioni
```csharp
class Shape: ICloneable
{
    object ICloneable.Clone() {...}
}

class Ellipse: Shape
{
    object ICloneable.Clone() {...}    // invalid
}
```
la dichiarazione di `ICloneable.Clone` nelle `Ellipse` genera un errore in fase di compilazione in quanto `ICloneable` non è elencato in modo esplicito nell'elenco delle classi base di `Ellipse`.

Il nome completo di un membro di interfaccia deve fare riferimento l'interfaccia in cui è stato dichiarato il membro. Pertanto, nelle dichiarazioni
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

class TextBox: ITextBox
{
    void IControl.Paint() {...}
    void ITextBox.SetText(string text) {...}
}
```
l'implementazione del membro di interfaccia esplicita `Paint` deve essere scritto come `IControl.Paint`.

### <a name="uniqueness-of-implemented-interfaces"></a>Univocità delle interfacce implementate

Le interfacce implementate da una dichiarazione di tipo generico devono rimanere univoche per tutti i possibili tipi costruiti. Senza questa regola, sarebbe Impossibile determinare il metodo corretto da chiamare per determinati tipi costruiti. Si supponga, ad esempio, una dichiarazione di classe generica fosse consentito deve essere scritto come indicato di seguito:
```csharp
interface I<T>
{
    void F();
}

class X<U,V>: I<U>, I<V>                    // Error: I<U> and I<V> conflict
{
    void I<U>.F() {...}
    void I<V>.F() {...}
}
```

Sono stati ciò è consentito, sarebbe impossibile ritestare determinare il codice da eseguire nel caso seguente:
```csharp
I<int> x = new X<int,int>();
x.F();
```

Per determinare se l'elenco di interfaccia di una dichiarazione di tipo generico è valido, vengono eseguiti i passaggi seguenti:

*  Let `L` l'elenco delle interfacce specificate direttamente in una classe generica, una struct o una dichiarazione di interfaccia `C`.
*  Aggiungere al `L` interfacce delle interfacce già in uno qualsiasi di base `L`.
*  Rimuovere i duplicati da `L`.
*  Se qualsiasi possibile tipo creato da costruito `C` verrebbe, dopo che gli argomenti di tipo vengono sostituiti nella `L`, le due interfacce in `L` siano identici, quindi la dichiarazione di `C` non è valido. Le dichiarazioni di vincolo non vengono considerate durante la determinazione di tutti i possibili tipi costruiti.

Nella dichiarazione della classe `X` precedente, l'elenco delle interfacce `L` costituito `I<U>` e `I<V>`. La dichiarazione non è valida perché qualsiasi tipo con costruito `U` e `V` dello stesso tipo comporterebbe queste due interfacce identiche tipi.

È possibile che le interfacce specificate a diversi livelli di ereditarietà per unificare:
```csharp
interface I<T>
{
    void F();
}

class Base<U>: I<U>
{
    void I<U>.F() {...}
}

class Derived<U,V>: Base<U>, I<V>    // Ok
{
    void I<V>.F() {...}
}
```

Questo codice è valido anche se `Derived<U,V>` implementa sia `I<U>` e `I<V>`. Il codice
```csharp
I<int> x = new Derived<int,int>();
x.F();
```
richiama il metodo nel `Derived`, poiché `Derived<int,int>` in modo efficace la reimplementazione `I<int>` ([prevista nuova implementazione dell'interfaccia](interfaces.md#interface-re-implementation)).

### <a name="implementation-of-generic-methods"></a>Implementazione di metodi generici

Quando un metodo generico in modo implicito implementa un metodo di interfaccia, i vincoli indicati per ogni parametro di tipo di metodo deve essere equivalente in entrambe le dichiarazioni (dopo qualsiasi tipo di interfaccia parametri vengono sostituiti con gli argomenti di tipo appropriato), dove (metodo) i parametri di tipo sono identificati da posizioni ordinali, da sinistra a destra.

Quando un metodo generico implementa in modo esplicito un metodo di interfaccia, tuttavia, vincoli non consentiti per il metodo di implementazione. Al contrario, i vincoli vengono ereditati dal metodo di interfaccia

```csharp
interface I<A,B,C>
{
    void F<T>(T t) where T: A;
    void G<T>(T t) where T: B;
    void H<T>(T t) where T: C;
}

class C: I<object,C,string>
{
    public void F<T>(T t) {...}                    // Ok
    public void G<T>(T t) where T: C {...}         // Ok
    public void H<T>(T t) where T: string {...}    // Error
}
```

Il metodo `C.F<T>` implementa in modo implicito `I<object,C,string>.F<T>`. In questo caso `C.F<T>` non viene richiesto (né consentito) per specificare il vincolo `T:object` poiché `object` è un vincolo su tutti i parametri di tipo implicito. Il metodo `C.G<T>` implementa in modo implicito `I<object,C,string>.G<T>` perché i vincoli corrispondano a quelle nell'interfaccia, dopo che i parametri di tipo di interfaccia vengono sostituiti con gli argomenti di tipo corrispondente. Il vincolo per il metodo `C.H<T>` è un errore perché i tipi sealed (`string` in questo caso) non possono essere usati come vincoli. L'omissione del vincolo sarà ugualmente un errore poiché sono necessari i vincoli di implementazioni del metodo dell'interfaccia implicita in modo che corrispondano. Pertanto, non è possibile implementare in modo implicito `I<object,C,string>.H<T>`. Questo metodo di interfaccia può essere implementato solo tramite un'implementazione di membro di interfaccia esplicita:
```csharp
class C: I<object,C,string>
{
    ...

    public void H<U>(U u) where U: class {...}

    void I<object,C,string>.H<T>(T t) {
        string s = t;    // Ok
        H<T>(t);
    }
}
```

In questo esempio, l'implementazione del membro di interfaccia esplicita richiama un metodo pubblico con vincoli meno rigorosi. Si noti che l'assegnazione da `t` al `s` è valido poiché `T` eredita un vincolo di `T:string`, anche se questo vincolo non può essere espresso nel codice sorgente.

### <a name="interface-mapping"></a>Mapping di interfaccia

Una classe o struct deve fornire le implementazioni di tutti i membri delle interfacce che sono elencati nell'elenco delle classi base della classe o struct. Il processo di individuazione delle implementazioni dei membri di interfaccia in una classe o struct implementazione è detta ***mapping dell'interfaccia***.

Mapping di interfaccia per una classe o struct `C` individua un'implementazione per ogni membro di ogni interfaccia specificata nell'elenco delle classi base di `C`. L'implementazione di un membro di interfaccia specifica `I.M`, dove `I` è l'interfaccia in cui il membro `M` viene dichiarato, viene determinato analizzando ogni classe o struct `S`, che inizia con `C` e ripetendo l'operazione per ogni classe di base successiva di `C`, fino a quando non trova una corrispondenza:

*  Se `S` contiene una dichiarazione di un'implementazione esplicita dell'interfaccia membro corrisponde `I` e `M`, quindi questo membro è l'implementazione di `I.M`.
*  In caso contrario, se `S` contiene una dichiarazione di un membro pubblico non statico che corrisponde al `M`, quindi questo membro è l'implementazione di `I.M`. Se più corrispondenze di un membro, non viene specificato quale membro è l'implementazione di `I.M`. Questa situazione può verificarsi solo se `S` è un tipo costruito in cui i due membri dichiarati nel tipo generico hanno firme diverse, ma gli argomenti di tipo apportare le relative firme identiche.

Si verifica un errore in fase di compilazione se non è possibile individuare per tutti i membri di tutte le interfacce specificate nell'elenco delle classi base di implementazioni `C`. Si noti che i membri di un'interfaccia includono i membri vengono ereditati da interfacce di base.

Ai fini del mapping dell'interfaccia, un membro della classe `A` corrisponde a un membro di interfaccia `B` quando:

*  `A` e `B` sono i metodi e il nome, tipo, e di elenchi di parametri formali `A` e `B` sono identici.
*  `A` e `B` sono le proprietà, il nome e tipo di `A` e `B` sono identiche, e `A` sono le stesse come `B` (`A` può avere funzioni di accesso aggiuntivi se non è un'interfaccia esplicita implementazione del membro).
*  `A` e `B` sono eventi e il nome e il tipo di `A` e `B` sono identici.
*  `A` e `B` sono gli indicizzatori, il tipo e gli elenchi di parametri formali della `A` e `B` sono identiche, e `A` sono le stesse come `B` (`A` può avere funzioni di accesso aggiuntivi se non è un implementazione del membro dell'interfaccia esplicita).

Le implicazioni principali dell'algoritmo di mapping di interfaccia sono:

*  Quando si determina il membro di classe o struct che implementa un membro di interfaccia, tali implementazioni hanno la precedenza rispetto agli altri membri nella stessa classe o struct.
*  Membri non pubblici e statici non vengono considerati nel mapping di interfaccia.

Nell'esempio
```csharp
interface ICloneable
{
    object Clone();
}

class C: ICloneable
{
    object ICloneable.Clone() {...}
    public object Clone() {...}
}
```
il `ICloneable.Clone` appartenente `C` diventa l'implementazione di `Clone` in `ICloneable` perché tali implementazioni hanno la precedenza rispetto agli altri membri.

Se una classe o struct implementa due o più interfacce che contengono un membro con il nome stesso, tipo e i tipi di parametro, è possibile eseguire il mapping di ognuno di tali membri di interfaccia in un singolo membro di classe o struct. Esempio:
```csharp
interface IControl
{
    void Paint();
}

interface IForm
{
    void Paint();
}

class Page: IControl, IForm
{
    public void Paint() {...}
}
```

In questo caso, il `Paint` di entrambi i metodi `IControl` e `IForm` vengono mappati i `Paint` metodo nella `Page`. È ovviamente anche possibile disporre di implementazioni di membro di interfaccia esplicita separata per i due metodi.

Se una classe o struct implementa un'interfaccia che contiene i membri nascosti, alcuni membri devono essere implementati necessariamente tramite implementazioni esplicite dell'interfaccia membro. Esempio:
```csharp
interface IBase
{
    int P { get; }
}

interface IDerived: IBase
{
    new int P();
}
```

Un'implementazione di questa interfaccia richiede almeno una implementazione esplicita dell'interfaccia membro e potrebbe assumere uno dei formati seguenti
```csharp
class C: IDerived
{
    int IBase.P { get {...} }
    int IDerived.P() {...}
}

class C: IDerived
{
    public int P { get {...} }
    int IDerived.P() {...}
}

class C: IDerived
{
    int IBase.P { get {...} }
    public int P() {...}
}
```

Quando una classe implementa più interfacce che hanno la stessa interfaccia di base, può esistere solo un'implementazione dell'interfaccia di base. Nell'esempio
```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

interface IListBox: IControl
{
    void SetItems(string[] items);
}

class ComboBox: IControl, ITextBox, IListBox
{
    void IControl.Paint() {...}
    void ITextBox.SetText(string text) {...}
    void IListBox.SetItems(string[] items) {...}
}
```
non è possibile disporre di implementazioni separate per la `IControl` denominato nell'elenco delle classi base, il `IControl` ereditata da `ITextBox`e il `IControl` ereditata da `IListBox`. In effetti, non è prevista un'identità distinta per queste interfacce. Piuttosto, le implementazioni di `ITextBox` e `IListBox` condividono la stessa implementazione di `IControl`, e `ComboBox` viene considerata solo per implementare tre interfacce `IControl`, `ITextBox`, e `IListBox`.

I membri di una classe base partecipano al mapping dell'interfaccia. Nell'esempio
```csharp
interface Interface1
{
    void F();
}

class Class1
{
    public void F() {}
    public void G() {}
}

class Class2: Class1, Interface1
{
    new public void G() {}
}
```
il metodo `F` nelle `Class1` viene utilizzata nella `Class2`dell'implementazione di `Interface1`.

### <a name="interface-implementation-inheritance"></a>Per ereditarietà dell'implementazione dell'interfaccia

Una classe eredita tutte le implementazioni di interfaccia fornite dalle classi base.

Senza in modo esplicito ***implementare nuovamente*** un'interfaccia, una classe derivata non è possibile in alcun modo modificare i mapping di interfaccia eredita dalle relative classi base. Ad esempio, nelle dichiarazioni
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    public void Paint() {...}
}

class TextBox: Control
{
    new public void Paint() {...}
}
```
il `Paint` metodo nella `TextBox` nasconde il `Paint` metodo nella `Control`, ma non modifica il mapping dei `Control.Paint` nello `IControl.Paint`e le chiamate a `Paint` tramite la classe verranno istanze e le istanze dell'interfaccia i seguenti effetti
```csharp
Control c = new Control();
TextBox t = new TextBox();
IControl ic = c;
IControl it = t;
c.Paint();            // invokes Control.Paint();
t.Paint();            // invokes TextBox.Paint();
ic.Paint();           // invokes Control.Paint();
it.Paint();           // invokes Control.Paint();
```

Tuttavia, quando un metodo di interfaccia viene mappato a un metodo virtuale in una classe, è possibile per le classi derivate eseguire l'override del metodo virtuale e modificare l'implementazione dell'interfaccia. Ad esempio, riscrivere le dichiarazioni sopra alla
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    public virtual void Paint() {...}
}

class TextBox: Control
{
    public override void Paint() {...}
}
```
gli effetti seguenti verranno ora esaminati
```csharp
Control c = new Control();
TextBox t = new TextBox();
IControl ic = c;
IControl it = t;
c.Paint();            // invokes Control.Paint();
t.Paint();            // invokes TextBox.Paint();
ic.Paint();           // invokes Control.Paint();
it.Paint();           // invokes TextBox.Paint();
```

Poiché le implementazioni di membri di interfaccia esplicita non possono essere dichiarate come virtuale, non è possibile eseguire l'override di un'implementazione di membro di interfaccia esplicita. Tuttavia, è perfettamente valido per un'implementazione di membro di interfaccia esplicita chiamare un altro metodo e che è possibile dichiarare virtuale per consentire altro metodo dalle classi derivate per eseguirne l'override. Esempio:
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    void IControl.Paint() { PaintControl(); }
    protected virtual void PaintControl() {...}
}

class TextBox: Control
{
    protected override void PaintControl() {...}
}
```

In questo caso, le classi derivate da `Control` possibile specializzare l'implementazione di `IControl.Paint` eseguendo l'override di `PaintControl` (metodo).

### <a name="interface-re-implementation"></a>Prevista nuova implementazione dell'interfaccia

È la possibilità di una classe che eredita un'implementazione dell'interfaccia ***reimplementare*** l'interfaccia includendolo nell'elenco delle classi base.

Una nuova implementazione di un'interfaccia segue esattamente la stessa interfaccia le regole di mapping come implementazione iniziale di un'interfaccia. Di conseguenza, il mapping di interfaccia ereditati non ha effetto alcun modo sul mapping dell'interfaccia stabilita per la nuova implementazione dell'interfaccia. Ad esempio, nelle dichiarazioni
```csharp
interface IControl
{
    void Paint();
}

class Control: IControl
{
    void IControl.Paint() {...}
}

class MyControl: Control, IControl
{
    public void Paint() {}
}
```
il fatto che `Control` viene eseguito il mapping `IControl.Paint` nello `Control.IControl.Paint` non ha alcun effetto reimplementazione in `MyControl`, che esegue il mapping `IControl.Paint` nello `MyControl.Paint`.

Ereditate le dichiarazioni dei membri pubblici e membri dell'interfaccia esplicita ereditati dichiarazioni di partecipano al processo di mapping per le interfacce implementate nuovamente. Esempio:
```csharp
interface IMethods
{
    void F();
    void G();
    void H();
    void I();
}

class Base: IMethods
{
    void IMethods.F() {}
    void IMethods.G() {}
    public void H() {}
    public void I() {}
}

class Derived: Base, IMethods
{
    public void F() {}
    void IMethods.H() {}
}
```

Questo caso, l'implementazione di `IMethods` nelle `Derived` esegue il mapping dei metodi di interfaccia su `Derived.F`, `Base.IMethods.G`, `Derived.IMethods.H`, e `Base.I`.

Quando una classe implementa un'interfaccia, in modo implicito anche implementa tutte le interfacce di base di tale interfaccia. Analogamente, una nuova implementazione di un'interfaccia in modo implicito è anche una nuova implementazione di tutte le interfacce di base. Esempio:
```csharp
interface IBase
{
    void F();
}

interface IDerived: IBase
{
    void G();
}

class C: IDerived
{
    void IBase.F() {...}
    void IDerived.G() {...}
}

class D: C, IDerived
{
    public void F() {...}
    public void G() {...}
}
```

Questo caso, la nuova implementazione di `IDerived` implementa anche nuovamente `IBase`, il mapping `IBase.F` nello `D.F`.

### <a name="abstract-classes-and-interfaces"></a>Interfacce e classi astratte

Ad esempio una classe non astratta, una classe astratta deve fornire implementazioni di tutti i membri delle interfacce che sono elencati nell'elenco delle classi base della classe. Tuttavia, una classe astratta può mappare i metodi di interfaccia su metodi astratti. Esempio:
```csharp
interface IMethods
{
    void F();
    void G();
}

abstract class C: IMethods
{
    public abstract void F();
    public abstract void G();
}
```

Questo caso, l'implementazione di `IMethods` viene eseguito il mapping `F` e `G` su metodi astratti, che deve essere sottoposto a override nelle classi astratte che derivano da `C`.

Si noti che le implementazioni di membri di interfaccia esplicita non possono essere astratte, ma tali implementazioni sono naturalmente la possibilità di chiamare i metodi astratti. Esempio:
```csharp
interface IMethods
{
    void F();
    void G();
}

abstract class C: IMethods
{
    void IMethods.F() { FF(); }
    void IMethods.G() { GG(); }
    protected abstract void FF();
    protected abstract void GG();
}
```

Questo caso, le classi astratte che derivano da `C` potrebbe essere necessario eseguire l'override `FF` e `GG`, fornendo in tal modo l'implementazione effettiva del `IMethods`.
