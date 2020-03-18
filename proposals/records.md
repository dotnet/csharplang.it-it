---
ms.openlocfilehash: 49720d62c496ff904eacacb31ae29ef97a5aefaa
ms.sourcegitcommit: 8152182f0a477cb3082e625b607262cc459a17f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "79484944"
---
# <a name="records"></a>record

* [x] proposto
* [] Prototipo: [completato](https://github.com/PROTOTYPE_OWNER/roslyn/BRANCH_NAME)
* [] Implementazione: [in corso](https://github.com/dotnet/roslyn/BRANCH_NAME)
* [] Specification: specifica bozza racchiusa

## <a name="summary"></a>Summary
[summary]: #summary

I record sono un nuovo modulo di dichiarazione semplificato per C# i tipi di classe e struct che combinano i vantaggi di una serie di funzionalità più semplici. Verranno descritte le nuove funzionalità (parametri del ricevitore del chiamante e *con-Expression*s), verranno fornite la sintassi e la semantica per le dichiarazioni di record, quindi verranno forniti alcuni esempi.


## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Un numero significativo di dichiarazioni di tipo in C# non è altro che di aggregare raccolte di dati tipizzati. Sfortunatamente, la dichiarazione di tali tipi richiede una grande quantità di codice standard. I record forniscono un meccanismo per dichiarare un tipo di *dati* descrivendo i membri dell'aggregazione insieme al codice o alle deviazioni aggiuntive rispetto al normale standard, se presente.

Vedere gli [esempi](#examples)riportati di seguito.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

### <a name="caller-receiver-parameters"></a>parametri del ricevitore del chiamante

Attualmente *, l'argomento predefinito* di un parametro del metodo deve essere 
- *espressione costante*; o
- espressione nel formato `new S()` in cui `S` è un tipo di valore. o
- espressione nel formato `default(S)` dove `S` è un tipo di valore

Questa operazione viene estesa per aggiungere quanto segue
- espressione nel formato `this.Identifier`

Questo nuovo form è denominato *argomento predefinito del destinatario del chiamante*ed è consentito solo se sono soddisfatte tutte le condizioni seguenti.
- Il metodo in cui viene visualizzato è un metodo di istanza. e
- L'espressione `this.Identifier` associa a un membro di istanza del tipo che lo contiene, che deve essere un campo o una proprietà. e
- Il membro a cui viene associato (e la funzione di accesso `get`, se è una proprietà), è almeno accessibile come metodo; e
- Il tipo di `this.Identifier` è convertibile in modo implicito da un'identità o Conversione Nullable nel tipo del parametro (si tratta di un vincolo esistente nell' *argomento default*).

Quando un argomento viene omesso da una chiamata di un membro di funzione per un parametro facoltativo corrispondente con un *argomento predefinito del destinatario del chiamante*, il valore del membro del ricevitore viene passato in modo implicito. 

> **Note sulla progettazione**: il motivo principale per il parametro del destinatario del chiamante è supportare l' *espressione with*. L'idea è che è possibile dichiarare un metodo come questo
> ```cs
> class Point
> {
>     public readonly int X;
>     public readonly int Y;
>     public Point With(int x = this.X, int y = this.Y) => new Point(x, y);
>     // etc
> }
> ```
> e quindi usarlo come segue
> ```cs
>     Point p = new Point(3, 4);
>     p = p.With(x: 1);
> ```
> Per creare un nuovo `Point` come un `Point` esistente, ma con il valore di `X` modificato.
> 
> Si tratta di una domanda aperta indipendentemente dal fatto che la forma sintattica di *with-Expression* valga la pena aggiungere una volta che è disponibile il supporto per i parametri del ricevitore del chiamante, quindi è possibile eseguire questa operazione *anziché* anziché *in aggiunta all'* *espressione with*.

- [] **Problema aperto**: qual è l'ordine in cui viene valutato un *argomento predefinito del destinatario del chiamante* rispetto ad altri argomenti? È necessario indicare che non è specificato?

### <a name="with-expressions"></a>espressioni with

Viene proposto un nuovo form di espressione:

```antlr
primary_expression
    : with_expression
    ;

with_expression
    : primary_expression 'with' '{' with_initializer_list '}'
    ;

with_initializer_list
    : with_initializer
    | with_initializer ',' with_initializer_list
    ;

with_initializer
    : identifier '=' expression
    ;
```

Il token `with` è una nuova parola chiave sensibile al contesto.

Ogni *identificatore* a sinistra di un *with_initializer* deve essere associato a una proprietà o a un campo di istanza accessibile del tipo di *primary_expression* della *with_expression*. Potrebbe non essere presente alcun nome duplicato tra questi identificatori di un determinato *with_expression*.

*With_expression* nel formato

> *e1* `with` `{` *identificatore* = *e2*,... `}`

viene considerato come una chiamata del form

> *e1*`.With(`*identifier2*`:` *e2*,...`)`

Dove, per ogni metodo denominato `With` che è un membro di istanza accessibile di *E1*, si seleziona *identifier2* come nome del primo parametro del metodo che ha un parametro del ricevitore del chiamante che è lo stesso membro del campo di istanza o della proprietà associata all' *identificatore*. Se tale parametro non può essere identificato, il metodo viene eliminato dalla considerazione. Il metodo da richiamare è selezionato tra i candidati rimanenti per risoluzione dell'overload.

> **Note sulla progettazione**: i parametri del ricevitore del chiamante, molti dei vantaggi dell' *espressione with* sono disponibili senza questo formato di sintassi speciale. Viene pertanto considerato se è necessario o meno. Il vantaggio principale è quello di consentire la programmazione in termini di nomi di campi e proprietà, anziché in termini di nomi di parametri. In questo modo è possibile migliorare sia la leggibilità che la qualità degli strumenti (ad esempio, per passare alla definizione dell'identificatore di un *with_expression* passare alla proprietà anziché a un parametro di metodo).

- [] **Problema aperto**: questa descrizione deve essere modificata per supportare i metodi di estensione.

### <a name="pattern-matching"></a>criteri di ricerca

Vedere la [specifica](csharp-8.0/patterns.md#positional-pattern) di criteri di ricerca per una specifica di `Deconstruct` e la relativa relazione con la corrispondenza dei modelli.

> **Note sulla progettazione**: in base al `Deconstruct` generato dal compilatore come specificato in questo documento e alla specifica per la corrispondenza dei modelli, una dichiarazione di record
> ```cs
> public class Point(int X, int Y);
> ```
> supporterà la corrispondenza del criterio posizionale come indicato di seguito
> ```cs
> Point p = new Point(3, 4);
> if (p is Point(3, var y)) { // if X is 3
>     Console.WriteLine(y);   // print Y
> }
> ```

### <a name="record-type-declarations"></a>dichiarazioni di tipi di record

La sintassi per una dichiarazione di `class` o `struct` è estesa per supportare parametri di valore. i parametri diventano proprietà del tipo:

```antlr
class_declaration
    : attributes? class_modifiers? 'partial'? 'class' identifier type_parameter_list?
      record_parameters? record_class_base? type_parameter_constraints_clauses? class_body
    ;

struct_declaration
    : attributes? struct_modifiers? 'partial'? 'struct' identifier type_parameter_list?
      record_parameters? struct_interfaces? type_parameter_constraints_clauses? struct_body
    ;

record_class_base
    : class_type record_base_arguments?
    | interface_type_list
    | class_type record_base_arguments? ',' interface_type_list
    ;

record_base_arguments
    : '(' argument_list? ')'
    ;

record_parameters
    : '(' record_parameter_list? ')'
    ;

record_parameter_list
    : record_parameter
    | record_parameter ',' record_parameter_list
    ;

record_parameter
    : attributes? type identifier record_property_name? default_argument?
    ;

record_property_name
    : ':' identifier
    ;

class_body
    : '{' class_member_declarations? '}'
    | ';'
    ;

struct_body
    : '{' struct_members_declarations? '}'
    | ';'
    ;
```

> **Note sulla progettazione**: poiché i tipi di record sono spesso utili senza la necessità di membri dichiarati in modo esplicito in un corpo di classe, la sintassi della dichiarazione viene modificata in modo da consentire a un corpo di essere semplicemente un punto e virgola.

Una classe (struct) dichiarata con i *parametri di record* viene chiamata *classe di record* (struct di*record*), che è un *tipo di record*.

- [] **Problema aperto**: è necessario includere *primary_constructor_body* nella grammatica, in modo che possa essere visualizzato all'interno di una dichiarazione del tipo di record.
- [] **Problema aperto**: quali sono le regole dei conflitti di nome per i nomi dei parametri? Probabilmente non è consentito entrare in conflitto con un parametro di tipo o un altro *parametro di record*.
- [] **Problema aperto**: è necessario specificare l'ambito dei parametri di record. Dove possono essere usati? Presumibilmente all'interno degli inizializzatori di campo di istanza e *primary_constructor_body* almeno.
- [] **Problema aperto**: la dichiarazione di un tipo di record può essere parziale? In caso affermativo, i parametri devono essere ripetuti per ogni parte?

#### <a name="members-of-a-record-type"></a>Membri di un tipo di record

Oltre ai membri dichiarati nel *corpo della classe*, un tipo di record presenta i membri aggiuntivi seguenti:

##### <a name="primary-constructor"></a>Costruttore primario

Un tipo di record ha un costruttore di `public` la cui firma corrisponde ai parametri del valore della dichiarazione del tipo. Questo metodo è denominato *costruttore primario* per il tipo e determina l'eliminazione del *costruttore predefinito* dichiarato in modo implicito.

In fase di esecuzione il costruttore primario

* Inizializza i campi di supporto generati dal compilatore per le proprietà corrispondenti ai parametri del valore (se queste proprietà sono fornite dal compilatore; [vedere 1.1.2](#1.1.2)); quindi
* esegue gli inizializzatori di campo di istanza visualizzati nel *corpo della classe*; E poi
* richiama un costruttore della classe base:
    * Se sono presenti argomenti nell' *record_base_arguments*, viene richiamato un costruttore di base selezionato dalla risoluzione dell'overload con questi argomenti;
    * In caso contrario, viene richiamato un costruttore di base senza argomenti.
* esegue il corpo di ogni *primary_constructor_body*, se presente, in ordine di origine.

- [] **Problema aperto**: è necessario specificare tale ordine, in particolare tra le unità di compilazione per i parziali.
- [] **Problema aperto**: è necessario specificare che tutti i costruttori dichiarati in modo esplicito devono essere concatenati al costruttore primario.
- [] **Problema aperto**: dovrebbe essere consentito modificare il modificatore di accesso nel costruttore primario?
- [] **Problema aperto**: in uno struct di record è un errore perché non ci siano parametri di record?

##### <a name="primary-constructor-body"></a>Corpo del costruttore primario

```antlr
primary_constructor_body
    : attributes? constructor_modifiers? identifier block
    ;
```

Un *primary_constructor_body* può essere utilizzato solo all'interno di una dichiarazione del tipo di record. L' *identificatore* di un *primary_constructor_body* deve denominare il tipo di record in cui è dichiarato.

Il *primary_constructor_body* non dichiara autonomamente un membro, ma è un modo per consentire al programmatore di fornire attributi per e specificare l'accesso del costruttore primario di un tipo di record. Consente inoltre al programmatore di fornire codice aggiuntivo che verrà eseguito quando viene costruita un'istanza del tipo di record.

- [] **Problema aperto**: si noti che un costruttore predefinito struct ignora questa situazione.
- [] **Problema aperto**: è necessario specificare l'ordine di esecuzione dell'inizializzazione.
- [] **Problema aperto**: se si vuole consentire un *primary_constructor_body* (presumibilmente senza attributi e modificatori) in una dichiarazione di tipo non di record e considerarlo come il codice di un inizializzatore di campo di istanza?

##### <a name="properties"></a>Proprietà

Per ogni parametro di record di una dichiarazione del tipo di record è presente un membro della proprietà `public` corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value. Il nome è l' *identificatore* del *record_property_name*, se presente, o l' *identificatore* del *record_parameter* in caso contrario. Se nessuna proprietà pubblica concreta (ovvero non astratta) con una funzione di accesso `get` e con questo nome e tipo è stata dichiarata o ereditata in modo esplicito, viene prodotta dal compilatore come indicato di seguito:

* Per uno *struct di record* o una *classe di record*`sealed`:
 * Un campo di `readonly` `private` viene prodotto come campo di supporto per una proprietà `readonly`. Il valore viene inizializzato durante la costruzione con il valore del parametro del costruttore primario corrispondente.
 * La funzione di accesso `get` della proprietà viene implementata per restituire il valore del campo sottostante.
 * Ogni funzione di accesso `get` della proprietà virtuale ereditata corrispondente viene sottoposta a override.

> **Note sulla progettazione**: in altre parole, se si estende una classe di base o si implementa un'interfaccia che dichiara una proprietà astratta pubblica con lo stesso nome e tipo di un parametro di record, tale proprietà viene sottoposta a override o implementata.

- [] **Problema aperto**: se è possibile modificare il modificatore di accesso in una proprietà quando viene dichiarata in modo esplicito?
- [] **Problema aperto**: se è possibile sostituire un campo con una proprietà?

##### <a name="object-methods"></a>Metodi di oggetto

Per uno *struct di record* o una *classe `sealed` record*, le implementazioni dei metodi `object.GetHashCode()` e `object.Equals(object)` vengono prodotte dal compilatore a meno che non vengano fornite dall'utente.

- [] **Problema aperto**: è necessario specificare con precisione la relativa implementazione.
- [] **Problema aperto**: è necessario aggiungere anche l'interfaccia `IEquatable<T>` per il tipo di record e specificare che vengono fornite le implementazioni.
- [] **Problema aperto**: è necessario specificare anche l'implementazione di ogni `IEquatable<T>.Equals`.
- [] **Problema aperto**: è necessario specificare esattamente il modo in cui viene risolto il problema di Equals in presenza di ereditarietà dei record: in particolare come vengono generati metodi di uguaglianza in modo che siano simmetrici, transitivi, riflessivi e così via.
- [] **Problema aperto**: è stato proposto di implementare `operator ==` e `operator !=` per i tipi di record.
- [] **Problema aperto**: è necessario generare automaticamente un'implementazione di `object.ToString`?

##### `Deconstruct`

Un tipo di record dispone di un metodo di `public` generato dal compilatore `void Deconstruct` a meno che l'utente non ne venga fornita una con alcuna firma. Ogni parametro è un parametro di `out` con lo stesso nome e lo stesso tipo del parametro corrispondente del tipo di record. L'implementazione fornita dal compilatore di questo metodo deve assegnare ogni parametro di `out` con il valore della proprietà corrispondente.

Vedere [la specifica dei criteri](csharp-8.0/patterns.md#positional-pattern) di ricerca per la semantica di `Deconstruct`.

##### <a name="with-method"></a>Metodo`With`

A meno che non esista un membro dichiarato dall'utente denominato `With` dichiarato, un tipo di record dispone di un metodo fornito dal compilatore denominato `With` il cui tipo restituito è il tipo di record stesso e contenente un parametro di valore corrispondente a ogni *parametro di record* nello stesso ordine in cui questi parametri vengono visualizzati nella dichiarazione del tipo di record. Ogni parametro deve avere un *argomento predefinito del destinatario del chiamante* della proprietà corrispondente.

In una classe `abstract` record, il metodo di `With` fornito dal compilatore è astratto. In uno struct di record o una classe di record sealed, viene `sealed`il metodo di `With` fornito dal compilatore. In caso contrario, il metodo `With` fornito dal compilatore è virtuale e la relativa implementazione deve restituire una nuova istanza prodotta richiamando il costruttore primario con i parametri come argomenti per creare una nuova istanza dai parametri e restituire la nuova istanza.

- [] **Problema aperto**: è necessario specificare anche in quali condizioni è necessario eseguire l'override o implementare metodi di `With` virtuali ereditati o metodi `With` da interfacce implementate.
- [] **Problema aperto**: è necessario indicare cosa accade quando si eredita un metodo di `With` non virtuale.

> **Note sulla progettazione**: poiché i tipi di record sono immutabili per impostazione predefinita, il `With` metodo consente di creare una nuova istanza che corrisponde a un'istanza esistente ma con le proprietà selezionate in base ai nuovi valori. Ad esempio, dato
> ```cs
> public class Point(int X, int Y);
> ```
> è presente un membro fornito dal compilatore
> ```cs
>     public virtual Point With(int X = this.X, int Y = this.Y) => new Point(X, Y);
> ```
> Che Abilita una variabile del tipo di record
> ```cs
> var p = new Point(3, 4);
> ```
> da sostituire con un'istanza con una o più proprietà diverse
> ```cs
>     p = p.With(X: 5);
> ```
> Questo può essere espresso anche usando il *with_expression*:
> ```cs
>     p = p with { X = 5 };
> ```

### <a name="examples"></a>Esempi

#### <a name="compatibility-of-record-types"></a>Compatibilità dei tipi di record

Poiché il programmatore può aggiungere membri a una dichiarazione di tipo record, spesso è possibile modificare il set di elementi record senza influire sui client esistenti. Ad esempio, data una versione iniziale di un tipo di record

```cs
// v1
public class Person(string Name, DateTime DateOfBirth);
```

Un nuovo elemento del tipo di record può essere aggiunto compatibilmente con la revisione successiva del tipo senza influire sulla compatibilità binaria o dell'origine:

```cs
// v2
public class Person(string Name, DateTime DateOfBirth, string HomeTown)
{
    // Note: below operations added to retain binary compatibility with v1
    public Person(string Name, DateTime DateOfBirth) : this(Name, DateOfBirth, string.Empty) {}
    public static void operator is(Person self, out string Name, out DateTime DateOfBirth)
        { Name = self.Name; DateOfBirth = self.DateOfBirth; }
    public Person With(string Name, DateTime DateOfBirth) => new Person(Name, DateOfBirth);
}
```

#### <a name="record-struct-example"></a>esempio di struct di record

Questo struct di record

```cs
public struct Pair(object First, object Second);
```

viene convertito in questo codice

```cs
public struct Pair : IEquatable<Pair>
{
    public object First { get; }
    public object Second { get; }
    public Pair(object First, object Second)
    {
        this.First = First;
        this.Second = Second;
    }
    public bool Equals(Pair other) // for IEquatable<Pair>
    {
        return Equals(First, other.First) && Equals(Second, other.Second);
    }
    public override bool Equals(object other)
    {
        return (other as Pair)?.Equals(this) == true;
    }
    public override int GetHashCode()
    {
        return (First?.GetHashCode()*17 + Second?.GetHashCode()).GetValueOrDefault();
    }
    public Pair With(object First = this.First, object Second = this.Second) => new Pair(First, Second);
    public void Deconstruct(out object First, out object Second)
    {
        First = self.First;
        Second = self.Second;
    }
}
```

- [] **Problema aperto**: se l'implementazione di Equals (pair other) è un membro pubblico di pair?
- [] **Problema aperto**: questa implementazione di `Equals` non è simmetrica in presenza di ereditarietà.
- [] **Problema aperto**: se un record dichiara `operator ==` e `operator !=`?

> **Note sulla progettazione**: poiché un tipo di record può ereditare da un altro tipo di record e questa implementazione di `Equals` non è simmetrica in tal caso, non è corretta. Si propone di implementare l'uguaglianza in questo modo:
> ```cs
>     public bool Equals(Pair other) // for IEquatable<Pair>
>     {
>         return other != null && EqualityContract == other.EqualityContract &&
>             Equals(First, other.First) && Equals(Second, other.Second);
>     }
>     protected virtual Type EqualityContract => typeof(Pair);
> ```
> I record derivati `override EqualityContract`. L'alternativa meno interessante consiste nel limitare l'ereditarietà.

#### <a name="sealed-record-example"></a>esempio di record sealed

Classe di record sealed

```cs
public sealed class Student(string Name, decimal Gpa);
```

viene convertito in questo codice

```cs
public sealed class Student : IEquatable<Student>
{
    public string Name { get; }
    public decimal Gpa { get; }
    public Student(string Name, decimal Gpa)
    {
        this.Name = Name;
        this.Gpa = Gpa;
    }
    public bool Equals(Student other) // for IEquatable<Student>
    {
        return other != null && Equals(Name, other.Name) && Equals(Gpa, other.Gpa);
    }
    public override bool Equals(object other)
    {
        return this.Equals(other as Student);
    }
    public override int GetHashCode()
    {
        return (Name?.GetHashCode()*17 + Gpa?.GetHashCode()).GetValueOrDefault();
    }
    public Student With(string Name = this.Name, decimal Gpa = this.Gpa) => new Student(Name, Gpa);
    public void Deconstruct(out string Name, out decimal Gpa)
    {
        Name = self.Name;
        Gpa = self.Gpa;
    }
}
```

#### <a name="abstract-record-class-example"></a>esempio di classe di record abstract

Questa classe di record abstract

```cs
public abstract class Person(string Name);
```

viene convertito in questo codice

```cs
public abstract class Person : IEquatable<Person>
{
    public string Name { get; }
    public Person(string Name)
    {
        this.Name = Name;
    }
    public bool Equals(Person other)
    {
        return other != null && Equals(Name, other.Name);
    }
    public override bool Equals(object other)
    {
        return Equals(other as Person);
    }
    public override int GetHashCode()
    {
        return (Name?.GetHashCode()).GetValueOrDefault();
    }
    public abstract Person With(string Name = this.Name);
    public void Deconstruct(Person self, out string Name)
    {
        Name = self.Name;
    }
}
```

#### <a name="combining-abstract-and-sealed-records"></a>combinazione di record astratti e sealed

Data la classe di record abstract `Person` sopra, questa classe di record sealed

```cs
public sealed class Student(string Name, decimal Gpa) : Person(Name);
```

viene convertito in questo codice

```cs
public sealed class Student : Person, IEquatable<Student>
{
    public override string Name { get; }
    public decimal Gpa { get; }
    public Student(string Name, decimal Gpa) : base(Name)
    {
        this.Name = Name;
        this.Gpa = Gpa;
    }
    public override bool Equals(Student other) // for IEquatable<Student>
    {
        return Equals(Name, other.Name) && Equals(Gpa, other.Gpa);
    }
    public bool Equals(Person other) // for IEquatable<Person>
    {
        return (other as Student)?.Equals(this) == true;
    }
    public override bool Equals(object other)
    {
        return (other as Student)?.Equals(this) == true;
    }
    public override int GetHashCode()
    {
        return (Name?.GetHashCode()*17 + Gpa.GetHashCode()).GetValueOrDefault();
    }
    public Student With(string Name = this.Name, decimal Gpa = this.Gpa) => new Student(Name, Gpa);
    public override Person With(string Name = this.Name) => new Student(Name, Gpa);
    public void Deconstruct(Student self, out string Name, out decimal Gpa)
    {
        Name = self.Name;
        Gpa = self.Gpa;
    }
}
```

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Come per qualsiasi funzionalità del linguaggio, è necessario stabilire se la complessità aggiuntiva al linguaggio venga ripagata in base a una maggiore chiarezza offerta C# al corpo dei programmi che trarrebbero vantaggio dalla funzionalità.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Abbiamo preso in considerazione l'aggiunta di C# *costruttori primari* in 6. Sebbene occupino la stessa superficie sintattica di questa proposta, abbiamo scoperto che i vantaggi offerti dai record sono stati ridotti.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

Le domande aperte vengono visualizzate nel corpo della proposta.

## <a name="design-meetings"></a>Riunioni di progettazione

TBD
