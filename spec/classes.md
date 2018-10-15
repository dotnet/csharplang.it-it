# <a name="classes"></a>Classi

Una classe è una struttura di dati che può contenere membri dati costanti e i campi, membri funzione (metodi, proprietà, eventi, indicizzatori, operatori, costruttori di istanze, distruttori e i costruttori statici) e i tipi annidati. I tipi classe supportano l'ereditarietà, un meccanismo in base al quale una classe derivata può estendere e specializzare una classe di base.

## <a name="class-declarations"></a>Dichiarazioni di classe

Oggetto *class_declaration* è un *type_declaration* ([dichiarazioni di tipo](namespaces.md#type-declarations)) che dichiara una nuova classe.

```antlr
class_declaration
    : attributes? class_modifier* 'partial'? 'class' identifier type_parameter_list?
      class_base? type_parameter_constraints_clause* class_body ';'?
    ;
```

Oggetto *class_declaration* costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), seguito da un set facoltativo di *class_modifier*s ([classe di modificatori](classes.md#class-modifiers)), seguito da un oggetto facoltativo `partial` modificatore, seguita dalla parola chiave `class` e un *identificatore* che denomina la classe, seguita da un facoltativo *type_parameter_list* ([parametri di tipo](classes.md#type-parameters)), seguito da un oggetto facoltativo *class_base* specifica ([della base Class Specification](classes.md#class-base-specification)), seguito da un set facoltativo di *type_parameter_constraints_clause*s ([digitare i vincoli del parametro](classes.md#type-parameter-constraints)), seguito da un *class_body*  ([Classe corpo](classes.md#class-body)), seguito facoltativamente da un punto e virgola.

Una dichiarazione di classe non può fornire *type_parameter_constraints_clause*s a meno che non fornisce anche una *type_parameter_list*.

Una dichiarazione di classe che fornisce una *type_parameter_list* è un ***dichiarazione di classe generica***. Inoltre, qualsiasi classe annidata all'interno di una dichiarazione di classe generica o una dichiarazione di struct generico è essa stessa una dichiarazione di classe generica, poiché i parametri di tipo per il tipo di contenitore devono essere forniti per creare un tipo costruito.

### <a name="class-modifiers"></a>Modificatori della classe

Oggetto *class_declaration* può includere una sequenza dei modificatori della classe:

```antlr
class_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'abstract'
    | 'sealed'
    | 'static'
    | class_modifier_unsafe
    ;
```

È un errore in fase di compilazione per più volte lo stesso modificatore in una dichiarazione di classe.

Il `new` modificatore è consentito nelle classi annidate. Specifica che la classe nasconde un membro ereditato con lo stesso nome, come descritto in [il nuovo modificatore](classes.md#the-new-modifier). Tratta di un errore in fase di compilazione per il `new` modificatore venga visualizzato in una dichiarazione di classe che non sia una dichiarazione di classe annidata.

Il `public`, `protected`, `internal`, e `private` modificatori determinano l'accessibilità della classe. A seconda del contesto in cui si verifica la dichiarazione di classe, alcuni di questi modificatori potrebbe non essere consentito ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)).

Il `abstract`, `sealed` e `static` modificatori sono descritti nelle sezioni seguenti.

#### <a name="abstract-classes"></a>Classi astratte

Il `abstract` modificatore viene usato per indicare che una classe è incompleta e che si dovrà essere utilizzato solo come classe di base. Una classe astratta è diverso da una classe non astratta nei modi seguenti:

*  Una classe astratta non può essere creata un'istanza direttamente ed è un errore in fase di compilazione per utilizzare il `new` operatore su una classe astratta. Sebbene sia possibile disporre di variabili e valori i cui tipi in fase di compilazione sono astratte, tali variabili e valori necessariamente sarà `null` o contenere riferimenti a istanze di classi non astratte derivate dai tipi astratti.
*  Una classe astratta è consentita (ma non obbligatorio) per contenere membri astratti.
*  Una classe astratta non può essere sealed.

Quando una classe non astratta è derivata da una classe astratta, la classe non astratta deve includere implementazioni effettive di tutti i membri astratti ereditati, eseguendo l'override di tali membri astratti. Nell'esempio
```csharp
abstract class A
{
    public abstract void F();
}

abstract class B: A
{
    public void G() {}
}

class C: B
{
    public override void F() {
        // actual implementation of F
    }
}
```
la classe astratta `A` introduce un metodo astratto `F`. Classe `B` introduce un ulteriore metodo `G`, ma poiché non fornisce un'implementazione di `F`, `B` deve inoltre essere dichiarata astratta. Classe `C` esegue l'override `F` e fornisce un'implementazione effettiva. Poiché non esistono membri astratti nelle `C`, `C` è consentita (ma non obbligatorio) per essere non astratta.

#### <a name="sealed-classes"></a>Classi sealed

Il `sealed` modificatore viene usato per impedire la derivazione da una classe. Se una classe sealed non è specificata come classe di base di un'altra classe, si verifica un errore in fase di compilazione.

Una classe sealed non può essere anche una classe astratta.

Il `sealed` modificatore viene usato principalmente per evitare derivazioni indesiderate, ma consente anche di alcune ottimizzazioni in fase di esecuzione. In particolare, poiché è noto che una classe sealed mai in tutte le classi derivate, è possibile trasformare le chiamate dei membri di funzione virtuale in istanze di classe sealed in chiamate al non virtuale.

#### <a name="static-classes"></a>Classi statiche

Il `static` modificatore viene usato per contrassegnare la classe viene dichiarata come un ***classe statica***. Una classe statica non è possibile creare un'istanza, non può essere utilizzata come un tipo e può contenere solo membri statici. Solo una classe statica può contenere dichiarazioni di metodi di estensione ([metodi di estensione](classes.md#extension-methods)).

Una dichiarazione di classe statica è soggetto alle restrizioni seguenti:

*  Una classe statica non può includere un `sealed` o `abstract` modificatore. Si noti tuttavia che poiché non può essere creata un'istanza o derivata da una classe statica, si comporta come se fosse sealed e astratta.
*  Una classe statica non può includere un *class_base* specification ([classe base specifica](classes.md#class-base-specification)) e non è possibile specificare in modo esplicito una classe di base o un elenco di interfacce implementate. Una classe statica eredita in modo implicito dal tipo `object`.
*  Una classe statica può contenere solo membri statici ([i membri statici e di istanza](classes.md#static-and-instance-members)). Si noti che le costanti e i tipi annidati sono classificati come membri statici.
*  Una classe statica non può avere membri con `protected` o `protected internal` l'accessibilità dichiarata.

Tratta di un errore in fase di compilazione violare una di queste restrizioni.

Una classe statica non ha alcun costruttore di istanza. Non è possibile dichiarare un costruttore di istanza in una classe statica e nessun costruttore di istanza predefinito ([costruttori predefiniti](classes.md#default-constructors)) viene fornito per una classe statica.

I membri di una classe statica non sono automaticamente statici e le dichiarazioni dei membri in modo esplicito devono includere un `static` modificatore (ad eccezione di costanti e i tipi annidati). Quando una classe è annidata all'interno di una classe esterna statica, la classe annidata non è una classe statica a meno che non includa in modo esplicito un `static` modificatore.

__Fa riferimento ai tipi di classe statica__

Oggetto *namespace_or_type_name* ([Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)) è consentito fare riferimento a una classe statica se

*  Il *namespace_or_type_name* è la `T` in un *namespace_or_type_name* del form `T.I`, o
*  Il *namespace_or_type_name* è la `T` in un *typeof_expression* ([elenchi di argomenti](expressions.md#argument-lists)1) nel formato `typeof(T)`.

Oggetto *primary_expression* ([membri funzione](expressions.md#function-members)) è consentito fare riferimento a una classe statica se

*  Il *primary_expression* è la `E` in un *member_access* ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) nel formato `E.I`.

In qualsiasi altro contesto è un errore in fase di compilazione per fare riferimento a una classe statica. Ad esempio, si tratta di un errore per una classe statica da utilizzare come classe di base, un tipo costitutivo ([i tipi annidati](classes.md#nested-types)) di un membro, un argomento tipo generico o un vincolo del parametro di tipo. Analogamente, una classe statica non può essere utilizzata in un tipo di matrice, un tipo di puntatore, un `new` espressione, un'espressione cast, un' `is` espressione, un `as` espressione, un `sizeof` espressione o un'espressione con valore predefinito.

### <a name="partial-modifier"></a>Modificatore parziale

Il `partial` modificatore viene usato per indicare che il *class_declaration* è una dichiarazione di tipo parziale. Più dichiarazioni di tipo parziale con lo stesso nome all'interno di una dichiarazione dello spazio dei nomi o tipo di inclusione combinano alla dichiarazione di un tipo di modulo, in base alle regole specificate [i tipi parziali](classes.md#partial-types).

Con la dichiarazione di una classe distribuita su segmenti separati di testo del programma può essere utile se questi segmenti vengono prodotti o gestiti in contesti diversi. Ad esempio, una parte di una dichiarazione di classe possa essere generate, da un computer mentre l'altra viene creata manualmente. La separazione testuale di due impedisce gli aggiornamenti da uno entri in conflitto con gli aggiornamenti da altro.

### <a name="type-parameters"></a>Parametri di tipo

Un parametro di tipo è un identificatore semplice che denota un segnaposto per un argomento di tipo fornito per creare un tipo costruito. Un parametro di tipo è un segnaposto formale per un tipo che verrà fornito in un secondo momento. Al contrario, un argomento di tipo ([gli argomenti di tipo](types.md#type-arguments)) è il tipo effettivo che verrà sostituito con il parametro di tipo quando viene creato un tipo costruito.

```antlr
type_parameter_list
    : '<' type_parameters '>'
    ;

type_parameters
    : attributes? type_parameter
    | type_parameters ',' attributes? type_parameter
    ;

type_parameter
    : identifier
    ;
```

Ogni parametro di tipo in una dichiarazione di classe definisce un nome nello spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)) di tale classe. Di conseguenza, non può avere lo stesso nome di un altro parametro di tipo o un membro dichiarato in tale classe. Un parametro di tipo non può avere lo stesso nome del tipo.

### <a name="class-base-specification"></a>Specifica della classe base

Una dichiarazione di classe può includere un *class_base* specifica, che definisce la classe base diretta della classe e le interfacce ([interfacce](interfaces.md)) direttamente implementata dalla classe.

```antlr
class_base
    : ':' class_type
    | ':' interface_type_list
    | ':' class_type ',' interface_type_list
    ;

interface_type_list
    : interface_type (',' interface_type)*
    ;
```

La classe di base specificata in una dichiarazione di classe può essere un tipo di classe costruita ([i tipi costruiti](types.md#constructed-types)). Una classe di base non può essere un parametro di tipo di per sé, ma può comportare i parametri di tipo che rientrano nell'ambito.

```csharp
class Extend<V>: V {}            // Error, type parameter used as base class
```

#### <a name="base-classes"></a>Classi di base

Quando un *class_type* viene incluso nel *class_base*, specifica la classe di base diretta della classe viene dichiarata. Se una dichiarazione di classe non ha alcun *class_base*, o se il *class_base* Elenca solo i tipi di interfaccia, si presuppone che la classe base diretta da `object`. Una classe eredita i membri dalla classe di base dirette, come descritto in [ereditarietà](classes.md#inheritance).

Nell'esempio
```csharp
class A {}

class B: A {}
```
classe `A` viene definita la classe base diretta `B`, e `B` viene definito come derivata da `A`. Poiché `A` viene non esplicitamente specificata una classe base diretta, la relativa classe base diretta è implicitamente `object`.

Per un tipo di classe costruita, se una classe di base viene specificata nella dichiarazione di classe generica, la classe di base del tipo costruito viene ottenuta tramite la sostituzione, per ognuno *type_parameter* nella dichiarazione di classe di base, il corrispondente *type_argument* del tipo costruito. Date le dichiarazioni di classe generica
```csharp
class B<U,V> {...}

class G<T>: B<string,T[]> {...}
```
la classe di base del tipo costruito `G<int>` sarebbe `B<string,int[]>`.

La classe di base diretta di un tipo di classe deve essere accessibile almeno quanto il tipo di classe ([domini accessibilità](basic-concepts.md#accessibility-domains)). Ad esempio, si tratta di un errore in fase di compilazione per un `public` classe derivandola da un' `private` o `internal` classe.

La classe di base diretta di un tipo di classe non deve essere uno qualsiasi dei tipi seguenti: `System.Array`, `System.Delegate`, `System.MulticastDelegate`, `System.Enum`, o `System.ValueType`. Inoltre, non è possibile usare una dichiarazione di classe generica `System.Attribute` come classe base diretta o indiretta.

Il tentativo di determinare il significato di specifica della classe base diretta `A` di una classe `B`, la classe base diretta `B` temporaneamente si presuppone che sia `object`. In modo intuitivo questo modo si garantisce che il significato di una specifica classe di base non è in modo ricorsivo dipendere da se stessa. Esempio:
```csharp
class A<T> {
   public class B {}
}

class C : A<C.B> {}
```
Genera un errore perché nella specifica di classe di base `A<C.B>` la classe base diretta `C` viene considerata `object`e pertanto (in base alle regole [Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)) `C` non viene considerata come contiene il membro `B`.

Le classi di base di un tipo classe sono la classe base diretta e le relative classi base. In altre parole, il set di classi di base è la chiusura transitiva di relazione la classe di base diretta. Che fa riferimento all'esempio precedente, le classi di base `B` vengono `A` e `object`. Nell'esempio
```csharp
class A {...}

class B<T>: A {...}

class C<T>: B<IComparable<T>> {...}

class D<T>: C<T[]> {...}
```
le classi di base `D<int>` vengono `C<int[]>`, `B<IComparable<int[]>>`, `A`, e `object`.

Ad eccezione di classe `object`, ogni tipo di classe ha esattamente una classe di base diretta. Il `object` classe non dispone di alcuna classe di base diretta ed è la principale classe base di tutte le altre classi.

Quando una classe `B` deriva da una classe `A`, tratta di un errore in fase di compilazione per `A` dipendere `B`. Una classe ***dipende direttamente*** relativa classe base diretta (se presente) e ***dipende direttamente*** la classe all'interno del quale area è nidificata immediatamente (se presente). Data questa definizione, il set completo di classi da cui dipende una classe è la chiusura riflessiva e transitiva il ***dipende direttamente*** relazione.

L'esempio
```csharp
class A: A {}
```
è errato perché la classe dipende da se stessa. Analogamente, l'esempio
```csharp
class A: B {}
class B: C {}
class C: A {}
```
è in errore perché le classi in modo circolare dipendono da se stessi. Infine, l'esempio
```csharp
class A: B.C {}

class B: A
{
    public class C {}
}
```
Genera un errore in fase di compilazione in quanto `A` dipende `B.C` (la classe base diretta), che dipende `B` (la relativa classe che lo contiene), che in modo circolare dipende `A`.

Si noti che una classe non dipendono dalle classi che sono annidate all'interno di esso. Nell'esempio
```csharp
class A
{
    class B: A {}
}
```
`B` dipende `A` (perché `A` è la relativa classe base diretta sia relativa classe che lo contiene), ma `A` non dipende `B` (poiché `B` è una classe di base né una classe che contiene `A` ). Di conseguenza, l'esempio è valido.

Non è possibile derivare da una `sealed` classe. Nell'esempio
```csharp
sealed class A {}

class B: A {}            // Error, cannot derive from a sealed class
```
classe `B` è in errore perché tenta di derivare il `sealed` classe `A`.

#### <a name="interface-implementations"></a>Implementazioni di interfacce

Oggetto *class_base* specifica può includere un elenco dei tipi di interfaccia, in cui i casi la classe sono detta implementare direttamente i tipi di interfaccia specificato. Le implementazioni di interfaccia sono descritte dettagliatamente nella [implementazioni di interfacce](interfaces.md#interface-implementations).

### <a name="type-parameter-constraints"></a>Vincoli del parametro di tipo

Le dichiarazioni di metodo e il tipo generiche possono facoltativamente specificare i vincoli del parametro includendo *type_parameter_constraints_clause*s.

```antlr
type_parameter_constraints_clause
    : 'where' type_parameter ':' type_parameter_constraints
    ;

type_parameter_constraints
    : primary_constraint
    | secondary_constraints
    | constructor_constraint
    | primary_constraint ',' secondary_constraints
    | primary_constraint ',' constructor_constraint
    | secondary_constraints ',' constructor_constraint
    | primary_constraint ',' secondary_constraints ',' constructor_constraint
    ;

primary_constraint
    : class_type
    | 'class'
    | 'struct'
    ;

secondary_constraints
    : interface_type
    | type_parameter
    | secondary_constraints ',' interface_type
    | secondary_constraints ',' type_parameter
    ;

constructor_constraint
    : 'new' '(' ')'
    ;
```

Ciascuna *type_parameter_constraints_clause* costituito il token `where`, seguito dal nome di un parametro di tipo, seguito da due punti e l'elenco di vincoli per quel parametro di tipo. Può esistere al massimo uno `where` clausola per ogni parametro di tipo e il `where` clausole possono essere elencate in qualsiasi ordine. Ad esempio la `get` e `set` token in una funzione di accesso, il `where` token non è una parola chiave.

L'elenco dei vincoli indicati un `where` clausola può includere uno dei componenti seguenti, nell'ordine indicato: il vincolo di costruttore, un singolo vincolo primario e uno o più vincoli secondari `new()`.

Un vincolo primario può essere un tipo di classe o la ***vincolo di tipo di riferimento*** `class` o il ***valore vincolo di tipo*** `struct`. Un vincolo secondario può essere un' *type_parameter* oppure *interface_type*.

Il vincolo di tipo riferimento specifica che un argomento di tipo utilizzato per il parametro type deve essere un tipo di riferimento. Tutti i tipi di classe, tipi interfaccia, tipi delegati, i tipi di matrice e i parametri di tipo noti per essere un tipo riferimento (come definita di seguito) soddisfano questo vincolo.

Il vincolo di tipo value specifica che un argomento di tipo utilizzato per il parametro type deve essere un tipo di valore non nullable. Tutti i tipi struct non nullable, tipi enum e parametri di tipo con il vincolo di tipo valore soddisfano il vincolo. Si noti che sebbene classificato come un tipo di valore, un tipo che ammette valori null ([tipi Nullable](types.md#nullable-types)) non soddisfa il vincolo di tipo valore. Un parametro di tipo con il vincolo di tipo valore non può avere anche il *constructor_constraint*.

Tipi di puntatore non sono mai consentiti sia gli argomenti di tipo e non vengono considerati per soddisfare entrambi i tipo o valore vincoli di tipo riferimento.

Se un vincolo è un tipo di classe, un tipo di interfaccia o un parametro di tipo, tale tipo specifica un minima "base del tipo" che deve supportare ogni argomento di tipo utilizzato per tale parametro di tipo. Ogni volta che viene usato un tipo costruito o metodo generico, argomento di tipo viene confrontato con i vincoli sul parametro di tipo in fase di compilazione. L'argomento tipo fornito deve soddisfare le condizioni descritte nel [che soddisfano i vincoli](types.md#satisfying-constraints).

Oggetto *class_type* vincolo deve soddisfare le regole seguenti:

*  Il tipo deve essere un tipo di classe.
*  Il tipo non deve essere `sealed`.
*  Il tipo non deve essere uno dei tipi seguenti: `System.Array`, `System.Delegate`, `System.Enum`, o `System.ValueType`.
*  Il tipo non deve essere `object`. Poiché tutti i tipi derivano dal `object`, tale vincolo non avrà effetto se si fosse consentito.
*  Al massimo uno dei vincoli per un parametro di tipo specificato può essere un tipo di classe.

Un tipo specificato come un *interface_type* vincolo deve soddisfare le regole seguenti:

*  Il tipo deve essere un tipo di interfaccia.
*  Un tipo non deve essere specificato più volte un determinato `where` clausola.

In entrambi i casi, il vincolo può includere uno dei parametri di tipo del tipo associato o dichiarazione del metodo come parte di un tipo costruito e può comportare il tipo dichiarato.

Qualsiasi tipo di classe o interfaccia specificato come un vincolo del parametro di tipo deve essere accessibile almeno quanto ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)) come il tipo o metodo generico viene dichiarato.

Un tipo specificato come un *type_parameter* vincolo deve soddisfare le regole seguenti:

*  Il tipo deve essere un parametro di tipo.
*  Un tipo non deve essere specificato più volte un determinato `where` clausola.

Inoltre non deve esistere alcun ciclo nel grafico delle dipendenze dei parametri di tipo, in cui dipendenza è una relazione transitiva definita da:

*  Se un parametro di tipo `T` viene usato come vincolo per il parametro di tipo `S` quindi `S` ***dipende*** `T`.
*  Se un parametro di tipo `S` dipende da un parametro di tipo `T` e `T` dipende da un parametro di tipo `U` quindi `S` ***dipende*** `U`.

Data questa relazione, è un errore in fase di compilazione per un parametro di tipo a dipendere da se stessa (direttamente o indirettamente).

Tutti i vincoli devono essere coerenti tra i parametri di tipo dipendente. Se il parametro di tipo `S` dipende dal parametro di tipo `T` quindi:

*  `T` non deve contenere il vincolo di tipo valore. In caso contrario, `T` in modo efficace è sealed in modo `S` sarei stato obbligato a essere dello stesso tipo `T`, eliminando la necessità di due parametri di tipo.
*  Se `S` ha il vincolo di tipo valore, quindi `T` non deve avere una *class_type* vincolo.
*  Se `S` ha un *class_type* vincolo `A` e `T` dispone di un *class_type* vincolo `B` , è necessario eseguire una conversione di identità implicite o conversione da di riferimento `A` al `B` o una conversione implicita del riferimento da `B` a `A`.
*  Se `S` dipende anche dal parametro di tipo `U` e `U` dispone di una *class_type* vincolo `A` e `T` ha una *class_type* vincolo `B` , è necessario eseguire una conversione di identità o una conversione implicita del riferimento da `A` al `B` o una conversione implicita del riferimento da `B` a `A`.

È valido per `S` affinché il vincolo di tipo valore e `T` affinché il vincolo di tipo riferimento. In modo efficace Ciò limita `T` ai tipi `System.Object`, `System.ValueType`, `System.Enum`e qualsiasi tipo di interfaccia.

Se il `where` clausola per un parametro di tipo include un vincolo di costruttore (che ha il formato `new()`), è possibile utilizzare il `new` operatore per creare istanze del tipo ([leespressionidicreazionedell'oggetto](expressions.md#object-creation-expressions)). Qualsiasi tipo di argomento usato per un parametro di tipo con un vincolo di costruttore deve avere un costruttore pubblico senza parametri (questo costruttore esiste in modo implicito per qualsiasi tipo di valore) o deve essere un parametro di tipo con il vincolo di tipo valore o un vincolo constructor (vedere [Vincoli di parametro di tipo](classes.md#type-parameter-constraints) per informazioni dettagliate).

Di seguito è riportati esempi di vincoli:
```csharp
interface IPrintable
{
    void Print();
}

interface IComparable<T>
{
    int CompareTo(T value);
}

interface IKeyProvider<T>
{
    T GetKey();
}

class Printer<T> where T: IPrintable {...}

class SortedList<T> where T: IComparable<T> {...}

class Dictionary<K,V>
    where K: IComparable<K>
    where V: IPrintable, IKeyProvider<K>, new()
{
    ...
}
```

L'esempio seguente è in errore perché causa una circolarità nel grafico delle dipendenze dei parametri di tipo:
```csharp
class Circular<S,T>
    where S: T
    where T: S                // Error, circularity in dependency graph
{
    ...
}
```

Gli esempi seguenti illustrano altre situazioni non è validi:
```csharp
class Sealed<S,T>
    where S: T
    where T: struct        // Error, T is sealed
{
    ...
}

class A {...}

class B {...}

class Incompat<S,T>
    where S: A, T
    where T: B                // Error, incompatible class-type constraints
{
    ...
}

class StructWithClass<S,T,U>
    where S: struct, T
    where T: U
    where U: A                // Error, A incompatible with struct
{
    ...
}
```

Il ***classe base effettiva*** di un parametro di tipo `T` viene definito come segue:

*  Se `T` senza vincoli primaria o vincoli del parametro di tipo, la relativa classe base effettiva è `object`.
*  Se `T` contiene il vincolo di tipo value, la relativa classe base effettiva è `System.ValueType`.
*  Se `T` ha un *class_type* vincolo `C` ma nessun *type_parameter* vincoli, la relativa classe base effettiva è `C`.
*  Se `T` non ha alcun *class_type* vincolo, ma ha uno o più *type_parameter* vincoli, la relativa classe base effettiva è di tipo maggiormente ([conversione è elevato gli operatori](conversions.md#lifted-conversion-operators)) nel set di classi di base effettive della relativa *type_parameter* vincoli. Le regole di uniformità verificare l'esistenza di questo tipo maggiormente.
*  Se `T` abbia sia un' *class_type* vincolo e uno o più *type_parameter* vincoli, la relativa classe base effettiva è di tipo maggiormente ([conversione è elevato gli operatori](conversions.md#lifted-conversion-operators)) nel set costituito dal *class_type* vincolo del `T` e le classi di base effettive della relativo *type_parameter* vincoli. Le regole di uniformità verificare l'esistenza di questo tipo maggiormente.
*  Se `T` ha il vincolo di tipo riferimento, ma non *class_type* vincoli, la relativa classe base effettiva è `object`.

Ai fini di queste regole, se T ha un vincolo `V` che rappresenta un *value_type*, usare invece il tipo di base più specifico della `V` vale a dire una *class_type*. Questo non può mai verificarsi in un vincolo specificato in modo esplicito, ma può verificarsi quando i vincoli di un metodo generico in modo implicito vengono ereditati da una dichiarazione di metodo esegue l'override o un'implementazione esplicita di un metodo di interfaccia.

Queste regole assicurano che la classe base effettiva è sempre un *class_type*.

Il ***set di interfacce efficaci*** di un parametro di tipo `T` viene definito come segue:

*  Se `T` non ha alcun *secondary_constraints*, il set di interfacce efficaci è vuoto.
*  Se `T` ha *interface_type* vincoli ma nessuna *type_parameter* vincoli, il set di interfacce efficaci è un set di *interface_type* vincoli.
*  Se `T` non ha alcun *interface_type* vincoli, ma ha *type_parameter* vincoli, il relativo set di interfaccia efficace è l'unione dei set di interfacce effettive del relativo *type_ parametro* vincoli.
*  Se `T` dispone di entrambe *interface_type* vincoli e *type_parameter* vincoli, il relativo set di interfaccia efficace è l'unione di set di *interface_type* i vincoli e i set di interfacce effettive del relativo *type_parameter* vincoli.

È un parametro di tipo ***noto per essere un tipo riferimento*** se ha il vincolo di tipo riferimento o la relativa classe base effettiva non è `object` o `System.ValueType`.

I valori di un tipo di parametro di tipo vincolato sono utilizzabile per accedere ai membri di istanza in cui è inclusa per i vincoli. Nell'esempio
```csharp
interface IPrintable
{
    void Print();
}

class Printer<T> where T: IPrintable
{
    void PrintOne(T x) {
        x.Print();
    }
}
```
i metodi della `IPrintable` può essere richiamato direttamente sul `x` poiché `T` è vincolato a sempre implementare `IPrintable`.

### <a name="class-body"></a>Corpo della classe

Il *class_body* di una classe definisce i membri di tale classe.

```antlr
class_body
    : '{' class_member_declaration* '}'
    ;
```

## <a name="partial-types"></a>Tipi parziali

Una dichiarazione del tipo può essere suddivisa in più ***dichiarazioni di tipi parziali***. La dichiarazione del tipo viene costruita dalle relative parti seguendo le regole in questa sezione nuovamente viene considerato come una singola dichiarazione fino alla fine dell'elaborazione in fase di compilazione e fase di esecuzione del programma.

Oggetto *class_declaration*, *struct_declaration* oppure *interface_declaration* rappresenta una dichiarazione di tipo parziale, se include un `partial` modificatore. `partial` non è una parola chiave e funge solo da un modificatore se viene visualizzato immediatamente prima che una delle parole chiave `class`, `struct` oppure `interface` in una dichiarazione del tipo, o prima del tipo `void` in una dichiarazione di metodo. In altri contesti può essere utilizzato come identificatore normale.

Ogni parte di una dichiarazione di tipo parziale deve includere un `partial` modificatore. Deve avere lo stesso nome e dichiarato nella stesso spazio dei nomi o dichiarazione di tipo come le altre parti. Il `partial` modificatore indica che altre parti della dichiarazione del tipo potrebbero esistere in un' posizione, ma l'esistenza di tali parti aggiuntive non è un requisito; è valido per un tipo con una sola dichiarazione includere il `partial` modificatore.

Tutte le parti di un tipo parziale devono essere compilate insieme in modo che le parti possono essere unite in fase di compilazione in una dichiarazione di tipo singolo. In particolare i tipi parziali non consentono tipi già compilati da estendere.

I tipi annidati possono essere dichiarati in più parti usando la `partial` modificatore. In genere, il tipo contenitore è dichiarato mediante `partial` che ogni parte del tipo annidato dichiarato in un'altra parte del tipo contenitore.

Il `partial` modificatore non è consentito in dichiarazioni di delegato o enumerazione.

### <a name="attributes"></a>Attributi

Gli attributi di un tipo parziale sono determinati dalla combinazione, in un ordine non specificato, gli attributi di ognuna delle parti. Se un attributo viene inserito in più parti, equivale a specificare l'attributo più volte sul tipo. Ad esempio, le due parti:

```csharp
[Attr1, Attr2("hello")]
partial class A {}

[Attr3, Attr2("goodbye")]
partial class A {}
```
sono equivalenti a una dichiarazione, ad esempio:
```csharp
[Attr1, Attr2("hello"), Attr3, Attr2("goodbye")]
class A {}
```

Gli attributi dei parametri di tipo combinano in modo analogo.

### <a name="modifiers"></a>Modificatori

Quando una dichiarazione di tipo parziale include una specifica di accessibilità (il `public`, `protected`, `internal`, e `private` modificatori) deve essere coerente con tutte le altre parti che includono una specifica di accessibilità. Se nessuna parte di un tipo parziale include una specifica di accessibilità, il tipo viene specificato l'accessibilità predefinita appropriata ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)).

Se uno o più dichiarazioni parziali di un tipo annidato includono un' `new` modificatore, non viene segnalato alcun messaggio di avviso se il tipo annidato nasconde un membro ereditato ([nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance)).

Se uno o più dichiarazioni parziali di una classe includono un' `abstract` modificatore, la classe verrà considerata astratta ([classi astratte](classes.md#abstract-classes)). In caso contrario, la classe è considerata non astratte.

Se uno o più dichiarazioni parziali di una classe includono un' `sealed` modificatore, la classe verrà considerata sealed ([le classi Sealed](classes.md#sealed-classes)). In caso contrario, viene considerata la classe non sealed.

Si noti che una classe non può essere contemporaneamente abstract e sealed.

Quando la `unsafe` modificatore viene usato in una dichiarazione di tipo parziale, solo tale parte viene considerata un contesto unsafe ([contesti non sicuri](unsafe-code.md#unsafe-contexts)).

### <a name="type-parameters-and-constraints"></a>I parametri di tipo e vincoli

Se un tipo generico è dichiarato in più parti, ogni parte deve dichiarare i parametri di tipo. Ogni parte deve avere lo stesso numero di parametri di tipo e lo stesso nome per ogni parametro di tipo, in ordine.

Quando una dichiarazione parziale di tipo generico include i vincoli (`where` clausole), i vincoli devono essere coerenti con tutte le altre parti che includono vincoli. In particolare, ogni parte che include i vincoli deve avere vincoli per lo stesso set di parametri di tipo e per ogni parametro di tipo i set di principale, secondario e i vincoli del costruttore devono essere equivalente. Due set di vincoli sono equivalenti se contengono gli stessi membri. Se nessuna parte di un tipo generico parziale specifica i vincoli del parametro, il tipo di parametri vengono considerati non vincolato.

L'esempio
```csharp
partial class Dictionary<K,V>
    where K: IComparable<K>
    where V: IKeyProvider<K>, IPersistable
{
    ...
}

partial class Dictionary<K,V>
    where V: IPersistable, IKeyProvider<K>
    where K: IComparable<K>
{
    ...
}

partial class Dictionary<K,V>
{
    ...
}
```
è corretto perché le parti che includono vincoli (i primi due) in modo efficace specificano stesso set di principale, secondario e i vincoli del costruttore per lo stesso set di parametri di tipo, rispettivamente.

### <a name="base-class"></a>Classe base

Quando una dichiarazione di classe parziale include una specifica classe di base, deve essere coerente in tutte le altre parti che includono una specifica classe di base. Se nessuna parte di una classe parziale include una specifica classe di base, la classe di base diventa `System.Object` ([classi di Base](classes.md#base-classes)).

### <a name="base-interfaces"></a>Interfacce di base

Il set di interfacce di base per un tipo dichiarato in più parti è l'unione delle interfacce di base specificato in ogni parte. Una determinata interfaccia di base potrebbe essere denominata solo una volta in ogni parte, ma è consentito per più parti denominare le stesse interfacce di base. Deve essere presente solo un'implementazione dei membri di qualsiasi interfaccia di base specificato.

Nell'esempio
```csharp
partial class C: IA, IB {...}

partial class C: IC {...}

partial class C: IA, IB {...}
```
il set di interfacce di base per la classe `C` viene `IA`, `IB`, e `IC`.

In genere, ogni parte fornisce un'implementazione delle interfacce dichiarate su tale parte; Tuttavia, questo non è un requisito. Una parte può fornire l'implementazione di un'interfaccia dichiarata in una parte diversa:
```csharp
partial class X
{
    int IComparable.CompareTo(object o) {...}
}

partial class X: IComparable
{
    ...
}
```

### <a name="members"></a>Membri

Fatta eccezione per i metodi parziali ([metodi parziali](classes.md#partial-methods)), il set di membri di un tipo dichiarato in più parti è semplicemente l'unione del set di membri dichiarati in ogni parte. I corpi di tutte le parti della dichiarazione del tipo condividono lo stesso spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)) e l'ambito di ogni membro ([ambiti](basic-concepts.md#scopes)) estende i corpi di tutte le parti. Il dominio di accessibilità di qualsiasi membro include sempre tutte le parti del tipo che lo contiene. un `private` membro dichiarato in una parte è possibile accedere liberamente da un'altra parte. È un errore in fase di compilazione dichiarano lo stesso membro in più di una parte del tipo, a meno che tale membro è un tipo con il `partial` modificatore.

```csharp
partial class A
{
    int x;                     // Error, cannot declare x more than once

    partial class Inner        // Ok, Inner is a partial type
    {
        int y;
    }
}

partial class A
{
    int x;                     // Error, cannot declare x more than once

    partial class Inner        // Ok, Inner is a partial type
    {
        int z;
    }
}
```

L'ordinamento dei membri all'interno di un tipo è raramente significativa al codice c#, ma potrebbe essere significativo quando eseguono l'interfacciamento con altri linguaggi e ambienti. In questi casi, è definito l'ordinamento dei membri all'interno di un tipo dichiarato in più parti.

### <a name="partial-methods"></a>Metodi parziali

Metodi parziali possono essere definiti in una parte di una dichiarazione del tipo e implementati in un altro. L'implementazione è facoltativa. Se nessuna parte implementa il metodo parziale, la dichiarazione di metodo parziale e le relative chiamate vengono rimossi dalla dichiarazione del tipo risultante dalla combinazione delle parti.

I metodi parziali non è possibile definire i modificatori di accesso, ma sono implicitamente `private`. Il tipo restituito deve essere `void`, e i relativi parametri non possono avere il `out` modificatore. L'identificatore `partial` viene riconosciuto come parola chiave speciale in una dichiarazione di metodo solo se viene visualizzato subito prima di `void` tipo; in caso contrario, può essere utilizzato come un identificatore normale. Un metodo parziale non può implementare in modo esplicito i metodi di interfaccia.

Esistono due tipi di dichiarazioni di metodo parziale: se il corpo della dichiarazione del metodo è un punto e virgola, la dichiarazione viene considerata come un ***definizione di dichiarazione di metodo parziale***. Se il corpo viene fornito come un *blocco*, la dichiarazione viene definita un' ***dichiarazione di metodo parziale che implementa***. Tra le parti di una dichiarazione del tipo può essere presente solo una dichiarazione di metodo parziale con una determinata firma che definisce e può essere presente solo una dichiarazione di metodo parziale con una determinata firma di implementazione. Se una dichiarazione di metodo parziale implementazione viene assegnata, una corrispondente definizione di dichiarazione di metodo parziale deve essere presente e devono corrispondere le dichiarazioni come specificato di seguito:

* Le dichiarazioni devono avere gli stessi modificatori (anche se non necessariamente nello stesso ordine), nome del metodo, numero di parametri di tipo e numero di parametri.
* I parametri corrispondenti nelle dichiarazioni devono avere gli stessi modificatori (anche se non necessariamente nello stesso ordine) e gli stessi tipi (ad eccezione delle differenze nei nomi dei parametri di tipo).
* Parametri di tipo corrispondenti nelle dichiarazioni di devono avere gli stessi vincoli (ad eccezione delle differenze nei nomi dei parametri di tipo).

Una dichiarazione di metodo parziale implementazione visualizzabili nella stessa parte come la dichiarazione di metodo parziale definizione corrispondente.

Solo un metodo di definizione parziale fa parte della risoluzione dell'overload. Di conseguenza, se si specifica una dichiarazione di implementazione, espressioni di chiamata possono essere risolti in chiamate del metodo parziale. Perché restituisce sempre un metodo parziale `void`, tali espressioni di chiamata saranno sempre le istruzioni di espressione. Inoltre, poiché un metodo parziale è implicitamente `private`, tali istruzioni verranno sempre eseguita all'interno di una delle parti della dichiarazione del tipo all'interno del quale il metodo parziale viene dichiarato.

Se nessuna parte di una dichiarazione di tipo parziale contiene una dichiarazione di implementazione per un determinato metodo parziale, qualsiasi istruzione di espressione richiamarlo semplicemente viene rimosso dalla dichiarazione di tipo combinato. Pertanto, l'espressione di chiamata, incluse tutte le espressioni che lo costituiscono, ha effetto in fase di esecuzione. Il metodo parziale viene anche rimossa e non sarà un membro della dichiarazione del tipo combinato.

In presenza di una dichiarazione di implementazione per un determinato metodo parziale, le chiamate dei metodi parziali vengono mantenute. Il metodo parziale dà luogo a una dichiarazione di metodo simile alla dichiarazione di metodo parziale implementazione ad eccezione dei seguenti:

* Il `partial` modificatore non è incluso
* Gli attributi nella dichiarazione del metodo risultanti sono gli attributi combinati di definizione e la dichiarazione di metodo parziale implementazione in ordine non specificato. Non vengono eliminati i duplicati.
* Gli attributi dei parametri della dichiarazione di metodo risultante sono gli attributi dei parametri corrispondenti della definizione e la dichiarazione di metodo parziale implementazione combinati in ordine non specificato. Non vengono eliminati i duplicati.

Se una dichiarazione di definizione, ma non una dichiarazione di implementazione non viene specificato per un metodo parziale M, si applicano le restrizioni seguenti:

* Tratta di un errore in fase di compilazione per creare un delegato al metodo ([espressioni di creazione delegato](expressions.md#delegate-creation-expressions)).
* Tratta di un errore in fase di compilazione per fare riferimento a `M` all'interno di una funzione anonima convertita in un tipo di struttura ad albero di espressione ([valutazione delle conversioni di tipi di albero delle espressioni funzioni anonime](conversions.md#evaluation-of-anonymous-function-conversions-to-expression-tree-types)).
* Che si verificano nell'ambito di una chiamata di espressioni `M` interferisca con lo stato di assegnazione certa ([assegnazione certa](variables.md#definite-assignment)), che può potenzialmente causare errori in fase di compilazione.
* `M` non può essere il punto di ingresso per un'applicazione ([avvio dell'applicazione](basic-concepts.md#application-startup)).

I metodi parziali sono utili per consentire a una parte di una dichiarazione di tipo per personalizzare il comportamento di un'altra parte, ad esempio, uno che viene generato da uno strumento. Si consideri la dichiarazione di classe parziale seguente:
```csharp
partial class Customer
{
    string name;

    public string Name {
        get { return name; }
        set {
            OnNameChanging(value);
            name = value;
            OnNameChanged();
        }

    }

    partial void OnNameChanging(string newName);

    partial void OnNameChanged();
}
```

Se questa classe viene compilata senza altre parti, le dichiarazioni di metodo parziale definizione e le chiamate verranno rimossi e la dichiarazione di classe combinata risultante sarà equivalente alla seguente:
```csharp
class Customer
{
    string name;

    public string Name {
        get { return name; }
        set { name = value; }
    }
}
```

Si supponga che un'altra parte viene fornita, tuttavia, che fornisce le dichiarazioni di implementazione dei metodi parziali:
```csharp
partial class Customer
{
    partial void OnNameChanging(string newName)
    {
        Console.WriteLine("Changing " + name + " to " + newName);
    }

    partial void OnNameChanged()
    {
        Console.WriteLine("Changed to " + name);
    }
}
```

Quindi la dichiarazione di classe combinata risultante sarà equivalente alla seguente:
```csharp
class Customer
{
    string name;

    public string Name {
        get { return name; }
        set {
            OnNameChanging(value);
            name = value;
            OnNameChanged();
        }

    }

    void OnNameChanging(string newName)
    {
        Console.WriteLine("Changing " + name + " to " + newName);
    }

    void OnNameChanged()
    {
        Console.WriteLine("Changed to " + name);
    }
}
```

### <a name="name-binding"></a>Associazione del nome

Anche se ogni parte di un tipo extensible deve essere dichiarato all'interno dello stesso spazio dei nomi, le parti vengono in genere scritti all'interno delle dichiarazioni dello spazio dei nomi diversi. Di conseguenza, diverse `using` direttive ([direttive Using](namespaces.md#using-directives)) possono essere presenti per ciascuna parte. Durante l'interpretazione di nomi semplici ([inferenza del tipo](expressions.md#type-inference)) all'interno di una parte, solo il `using` vengono considerate le direttive di dichiarazioni di spazio dei nomi che contengono tale parte. Questo può comportare lo stesso identificatore avere significati diversi in diverse parti:
```csharp
namespace N
{
    using List = System.Collections.ArrayList;

    partial class A
    {
        List x;                // x has type System.Collections.ArrayList
    }
}

namespace N
{
    using List = Widgets.LinkedList;

    partial class A
    {
        List y;                // y has type Widgets.LinkedList
    }
}
```

## <a name="class-members"></a>Membri della classe

I membri di una classe sono costituiti da membri introdotti dalla relativa *class_member_declaration*s e i membri ereditati dalla classe di base diretta.

```antlr
class_member_declaration
    : constant_declaration
    | field_declaration
    | method_declaration
    | property_declaration
    | event_declaration
    | indexer_declaration
    | operator_declaration
    | constructor_declaration
    | destructor_declaration
    | static_constructor_declaration
    | type_declaration
    ;
```

I membri di un tipo di classe sono suddivisi nelle categorie seguenti:

*  Costanti, che rappresentano i valori costanti associati alla classe ([costanti](classes.md#constants)).
*  I campi, le variabili della classe ([campi](classes.md#fields)).
*  Metodi che implementano i calcoli e azioni che possono essere eseguite dalla classe ([metodi](classes.md#methods)).
*  Proprietà, che definiscono le caratteristiche denominate e le azioni associate alla lettura e scrittura di tali caratteristiche ([proprietà](classes.md#properties)).
*  Gli eventi, che definiscono le notifiche che possono essere generate dalla classe ([eventi](classes.md#events)).
*  Gli indicizzatori, che consentono di istanze della classe per cui l'indicizzazione nello stesso modo (sintatticamente) sotto forma di matrici ([indicizzatori](classes.md#indexers)).
*  Operatori, ovvero definiscono gli operatori di espressione che possono essere applicati a istanze della classe ([operatori](classes.md#operators)).
*  Costruttori di istanza, che implementano le azioni necessarie per inizializzare istanze della classe ([costruttori di istanze](classes.md#instance-constructors))
*  I distruttori, che implementano le azioni da eseguire prima dell'eliminazione permanente delle istanze della classe ([distruttori](classes.md#destructors)).
*  I costruttori statici, che implementano le azioni necessarie per inizializzare la classe stessa ([costruttori statici](classes.md#static-constructors)).
*  I tipi, che rappresentano i tipi che sono locali rispetto alla classe ([i tipi annidati](classes.md#nested-types)).

I membri che contengono codice eseguibile sono noti collettivamente come le *membri funzione* del tipo di classe. I membri di un tipo classe sono metodi, proprietà, eventi, indicizzatori, operatori, costruttori di istanze, distruttori e i costruttori statici del tipo classe.

Oggetto *class_declaration* crea un nuovo spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)) e il *class_member_declaration*immediatamente contenuti dal *classe _declaration* introducono nuovi membri in tale spazio di dichiarazione. Le regole seguenti riguardano *class_member_declaration*s:

*  Costruttori di istanze, distruttori e i costruttori statici devono avere lo stesso nome della classe che li contiene. Tutti gli altri membri devono avere nomi diversi da quello della classe che li contiene.
*  Il nome di una costante, campo, proprietà, eventi o tipo deve essere diverso dai nomi di tutti gli altri membri dichiarati nella stessa classe.
*  Il nome di un metodo deve essere diverso dai nomi di tutti gli altri metodi-non dichiarati nella stessa classe. Inoltre, la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) di un metodo deve essere diversa dalle firme di tutti gli altri metodi dichiarati nella stessa classe, e due i metodi dichiarati nella stessa classe non possono avere firme che differiscono unicamente dal `ref` e `out`.
*  La firma di un costruttore di istanza deve essere diversa dalle firme di tutti gli altri costruttori di istanze dichiarate nella stessa classe, e due costruttori dichiarati nella stessa classe non possono avere firme che differiscono unicamente per `ref` e `out`.
*  La firma di un indicizzatore sia diversa dalle firme di tutti gli altri indicizzatori dichiarati nella stessa classe.
*  La firma di un operatore deve essere diversa dalle firme di tutti gli altri operatori dichiarati nella stessa classe.

I membri ereditati di un tipo di classe ([ereditarietà](classes.md#inheritance)) non fanno parte dello spazio di dichiarazione di una classe. Di conseguenza, una classe derivata può dichiarare un membro con lo stesso nome o firma di un membro ereditato (che in effetti nasconde il membro ereditato).

### <a name="the-instance-type"></a>Il tipo di istanza

Ogni dichiarazione di classe dispone di un tipo associato ([associato e non associato tipi](types.md#bound-and-unbound-types)), il ***tipo di istanza***. Per una dichiarazione di classe generica, il tipo di istanza è formato mediante la creazione di un tipo costruito ([i tipi costruiti](types.md#constructed-types)) dalla dichiarazione del tipo, con ogni del tipo specificato di argomenti in corso il corrispondente parametro di tipo. Poiché il tipo di istanza Usa i parametri di tipo, utilizzabile solo in cui i parametri di tipo sono nell'ambito. vale a dire, all'interno della dichiarazione di classe. Il tipo di istanza è il tipo di `this` per il codice scritto all'interno della dichiarazione di classe. Per le classi non generiche, il tipo di istanza è semplicemente la classe dichiarata. Di seguito viene illustrato più dichiarazioni di classe insieme ai relativi tipi di istanza: 
```csharp
class A<T>                           // instance type: A<T>
{
    class B {}                       // instance type: A<T>.B
    class C<U> {}                    // instance type: A<T>.C<U>
}

class D {}                           // instance type: D
```

### <a name="members-of-constructed-types"></a>Membri di tipi costruiti

I membri di un tipo costruito non ereditate vengono ottenuti tramite la sostituzione, per ognuno *type_parameter* nella dichiarazione del membro, il corrispondente *type_argument* del tipo costruito. Il processo di sostituzione si basa sul significato semantico delle dichiarazioni di tipo e non è semplice sostituzione di testo.

Ad esempio, considerando la dichiarazione di classe generica
```csharp
class Gen<T,U>
{
    public T[,] a;
    public void G(int i, T t, Gen<U,T> gt) {...}
    public U Prop { get {...} set {...} }
    public int H(double d) {...}
}
```
il tipo costruito `Gen<int[],IComparable<string>>` include i membri seguenti:
```csharp
public int[,][] a;
public void G(int i, int[] t, Gen<IComparable<string>,int[]> gt) {...}
public IComparable<string> Prop { get {...} set {...} }
public int H(double d) {...}
```

Il tipo del membro `a` nella dichiarazione di classe generica `Gen` è "matrice bidimensionale di `T`", pertanto il tipo del membro `a` nel tipo costruito precedente è "matrice bidimensionale di matrice unidimensionale di `int`", o `int[,][]`.

All'interno di membri di funzione di istanza, il tipo della `this` è il tipo di istanza ([del tipo di istanza](classes.md#the-instance-type)) della dichiarazione che lo contiene.

Tutti i membri di una classe generica possono usare i parametri di tipo da qualsiasi classe che lo contiene, direttamente o come parte di un tipo costruito. Quando un particolare tipo costruito chiuso ([aperto e chiuso tipi](types.md#open-and-closed-types)) viene utilizzato in fase di esecuzione, ogni uso di un parametro di tipo viene sostituito con l'argomento di tipo effettivo fornito per il tipo costruito. Ad esempio:
```csharp
class C<V>
{
    public V f1;
    public C<V> f2 = null;

    public C(V x) {
        this.f1 = x;
        this.f2 = this;
    }
}

class Application
{
    static void Main() {
        C<int> x1 = new C<int>(1);
        Console.WriteLine(x1.f1);        // Prints 1

        C<double> x2 = new C<double>(3.1415);
        Console.WriteLine(x2.f1);        // Prints 3.1415
    }
}
```

### <a name="inheritance"></a>Ereditarietà

Una classe ***eredita*** i membri del relativo tipo di classe di base diretta. L'ereditarietà prevede che una classe contenga in modo implicito tutti i membri del relativo tipo di classe di base diretta, ad eccezione di costruttori di istanze, distruttori e i costruttori statici della classe di base. Alcuni aspetti importanti di ereditarietà sono:

*  L'ereditarietà è transitiva. Se `C` deriva dal `B`, e `B` è derivato da `A`, quindi `C` eredita i membri dichiarati nella `B` nonché membri dichiarati nel `A`.
*  Una classe derivata estende la classe di base diretta. Una classe derivata può aggiungere nuovi membri a quelli ereditati, ma non può rimuovere la definizione di un membro ereditato.
*  Costruttori di istanze, distruttori e i costruttori statici non vengono ereditati, ma sono tutti gli altri membri, indipendentemente dall'accessibilità dichiarata ([l'accesso ai membri](basic-concepts.md#member-access)). Tuttavia, a seconda della relativa accessibilità dichiarata, i membri ereditati non sia accessibili in una classe derivata.
*  Una classe derivata può ***nascondere*** ([nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance)) con la dichiarazione di nuovi membri con lo stesso nome o firma di membri ereditati. Si noti tuttavia che se si nasconde un membro ereditato non rimuove tale membro, ovvero semplicemente rende tale membro non accessibili direttamente tramite la classe derivata.
*  Un'istanza di una classe contiene un set di tutti i campi di istanza dichiarata nella classe e le relative classi base e una conversione implicita ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)) esistente da un tipo di classe derivata a uno dei relativi tipi di classe di base. Di conseguenza, un riferimento a un'istanza di una classe derivata può essere considerato un riferimento a un'istanza di una delle relative classi base.
*  Una classe può dichiarare gli indicizzatori, proprietà e metodi virtuali e le classi derivate possono eseguire l'override l'implementazione di questi membri di funzione. Ciò consente alle classi può presentare un comportamento polimorfico che impediva le azioni eseguite da una chiamata alla funzione membro varia a seconda del tipo in fase di esecuzione dell'istanza tramite cui viene richiamato il membro di funzione.

Il membro ereditato di un tipo di classe costruita sono i membri del tipo di classe base immediata ([alle classi Base](classes.md#base-classes)), che si trova sostituendo gli argomenti di tipo del tipo costruito per ogni occorrenza del tipo corrispondente i parametri in di *class_base* specifica. Questi membri, a sua volta, vengono trasformati tramite la sostituzione, per ognuno *type_parameter* nella dichiarazione del membro, il corrispondente *type_argument* del *class_base* specifica.

```csharp
class B<U>
{
    public U F(long index) {...}
}

class D<T>: B<T[]>
{
    public T G(string s) {...}
}
```

Nell'esempio precedente, il tipo costruito `D<int>` dispone di un membro non ereditate `public int G(string s)` ottenuta sostituendo l'argomento di tipo `int` per il parametro di tipo `T`. `D<int>` è anche un membro ereditato dalla dichiarazione di classe `B`. Questo membro ereditato è determinato dal primo determinazione del tipo di classe di base `B<int[]>` dei `D<int>` sostituendo `int` per `T` nella specifica della classe base `B<T[]>`. Quindi, come un argomento di tipo `B`, `int[]` viene sostituito `U` nelle `public U F(long index)`, restituendo il membro ereditato `public int[] F(long index)`.

### <a name="the-new-modifier"></a>Il modificatore new

Oggetto *class_member_declaration* è consentito dichiarare un membro con lo stesso nome o firma di un membro ereditato. In questo caso, il membro della classe derivata viene detto ***nascondere*** il membro della classe base. Se si nasconde un membro ereditato non è considerato un errore, ma comporta il compilatore emette un avviso. Per eliminare l'avviso, la dichiarazione del membro della classe derivata può includere un `new` modificatore per indicare che il membro derivato deve nascondere il membro di base. Questo argomento viene descritto dettagliatamente nella [nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance).

Se un `new` modificatore viene inclusa in una dichiarazione che non nasconde un membro ereditato, viene generato un avviso in tal senso. Questo avviso viene eliminato tramite la rimozione di `new` modificatore.

### <a name="access-modifiers"></a>Modificatori di accesso

Oggetto *class_member_declaration* può avere uno dei cinque tipi possibili di accessibilità dichiarata ([l'accessibilità dichiarata](basic-concepts.md#declared-accessibility)): `public`, `protected internal`, `protected`, `internal` , o `private`. Fatta eccezione per il `protected internal` combinazione, tratta di un errore in fase di compilazione per specificare più di un modificatore di accesso. Quando un *class_member_declaration* non include tutti i modificatori di accesso, `private` presuppone.

### <a name="constituent-types"></a>Tipi che costituiscono

I tipi utilizzati nella dichiarazione di membro sono denominati tipi che costituiscono di tale membro. Possibili tipi che lo costituiscono sono il tipo di una costante, campo, proprietà, evento o l'indicizzatore, il tipo restituito di un metodo o un operatore e i tipi di parametro di un metodo, indicizzatore, operatore o costruttore di istanza. I tipi che costituiscono dei membri devono essere accessibili almeno quanto il membro stesso ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).

### <a name="static-and-instance-members"></a>Membri statici e di istanza

I membri di una classe sono entrambi ***i membri statici*** oppure ***membri di istanza***. In generale, è utile pensare a membri statici come appartenenti a tipi di classi e membri di istanza come appartenenti agli oggetti (istanze di tipi di classe).

Quando una dichiarazione di campo, metodo, proprietà, evento o l'operatore costruttore include un `static` modificatore, dichiara un membro statico. Inoltre, una dichiarazione di costante o un tipo dichiara in modo implicito un membro statico. I membri statici hanno le caratteristiche seguenti:

*  Quando un membro statico `M` viene fatto riferimento in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, `E` deve indicare un tipo contenente `M`. Tratta di un errore in fase di compilazione per `E` per indicare un'istanza.
*  Un campo statico identifica esattamente una posizione di archiviazione deve essere condiviso da tutte le istanze di un tipo specifico della classe chiuso. Indipendentemente dal numero di istanze di un tipo specifico della classe chiusi venga creato, è sempre un'unica copia di un campo statico.
*  Un membro di funzione statica (metodo, proprietà, eventi, operatore o costruttore) non agisce su un'istanza specifica ed è un errore in fase di compilazione per fare riferimento a `this` in un membro di funzione.

Quando una dichiarazione di campo, metodo, proprietà, eventi, indicizzatore, costruttore o distruttore non include un `static` modificatore, dichiara un membro di istanza. (Un membro di istanza viene talvolta chiamato un membro non statica.) I membri di istanza hanno le caratteristiche seguenti:

*  Quando un membro di istanza `M` viene fatto riferimento in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, `E` devono indicare un'istanza di un tipo contenente `M`. Tratta di un errore in fase di associazione per `E` per indicare un tipo.
*  Ogni istanza di una classe contiene un set separato di tutti i campi di istanza della classe.
*  Un membro di funzione di istanza (metodo, proprietà, indicizzatore, costruttore di istanza o un distruttore) opera su una determinata istanza della classe e questa istanza è accessibile come `this` ([questo accesso](expressions.md#this-access)).

L'esempio seguente illustra le regole per l'accesso a statiche e membri di istanza:
```csharp
class Test
{
    int x;
    static int y;

    void F() {
        x = 1;            // Ok, same as this.x = 1
        y = 1;            // Ok, same as Test.y = 1
    }

    static void G() {
        x = 1;            // Error, cannot access this.x
        y = 1;            // Ok, same as Test.y = 1
    }

    static void Main() {
        Test t = new Test();
        t.x = 1;          // Ok
        t.y = 1;          // Error, cannot access static member through instance
        Test.x = 1;       // Error, cannot access instance member through type
        Test.y = 1;       // Ok
    }
}
```

Il `F` metodo mostra che in un membro di funzione di istanza, una *simple_name* ([nomi semplici](expressions.md#simple-names)) può essere utilizzato per accedere sia i membri di istanza e membri statici. Il `G` metodo mostra che in un membro di funzione statica, è un errore in fase di compilazione per accedere a un membro di istanza tramite un *simple_name*. Il `Main` metodo illustra che in un *member_access* ([accesso ai membri](expressions.md#member-access)), i membri di istanza devono essere accessibili tramite istanze e i membri statici è possibile accedervi tramite i tipi.

### <a name="nested-types"></a>Tipi annidati

Un tipo dichiarato all'interno di una dichiarazione di classe o struct viene chiamato un ***tipo annidato***. Un tipo che viene dichiarato all'interno di un'unità di compilazione o dello spazio dei nomi viene chiamato un ***tipo non annidato***.

Nell'esempio
```csharp
using System;

class A
{
    class B
    {
        static void F() {
            Console.WriteLine("A.B.F");
        }
    }
}
```
classe `B` è un tipo annidato perché è dichiarato all'interno di classi `A`e la classe `A` è un tipo non annidato perché è dichiarato all'interno di un'unità di compilazione.

#### <a name="fully-qualified-name"></a>Nome completo

Specificare il nome completo ([i nomi completi](basic-concepts.md#fully-qualified-names)) per un tipo annidato è `S.N` in cui `S` è il nome completo del tipo nel quale tipo `N` viene dichiarato.

#### <a name="declared-accessibility"></a>Accessibilità dichiarata

Tipi annidati non possono avere `public` oppure `internal` accessibilità dichiarata e avere `internal` l'accessibilità dichiarata per impostazione predefinita. I tipi annidati sono disponibili queste forme di accessibilità dichiarata, oltre a uno o più ulteriori forme di accessibilità dichiarata, a seconda che il tipo che lo contiene sia una classe o struct:

*  Un tipo annidato che viene dichiarato in una classe può avere uno dei cinque forme di accessibilità dichiarata (`public`, `protected internal`, `protected`, `internal`, o `private`) e, analogamente agli altri membri di classe, per impostazione predefinita `private` dichiarato accessibilità.
*  Un tipo annidato che viene dichiarato in uno struct può avere uno dei tre formati seguenti di accessibilità dichiarata (`public`, `internal`, o `private`) e, analogamente agli altri membri struct, per impostazione predefinita `private` l'accessibilità dichiarata.

L'esempio
```csharp
public class List
{
    // Private data structure
    private class Node
    { 
        public object Data;
        public Node Next;

        public Node(object data, Node next) {
            this.Data = data;
            this.Next = next;
        }
    }

    private Node first = null;
    private Node last = null;

    // Public interface
    public void AddToFront(object o) {...}
    public void AddToBack(object o) {...}
    public object RemoveFromFront() {...}
    public object RemoveFromBack() {...}
    public int Count { get {...} }
}
```
dichiara una classe annidata private `Node`.

#### <a name="hiding"></a>Disattivazione della visualizzazione

Può nascondere un tipo annidato ([nascondere il nome](basic-concepts.md#name-hiding)) un membro di base. Il `new` modificatore è consentito nelle dichiarazioni di tipo annidato in modo da nascondere può essere espressi in modo esplicito. L'esempio
```csharp
using System;

class Base
{
    public static void M() {
        Console.WriteLine("Base.M");
    }
}

class Derived: Base 
{
    new public class M 
    {
        public static void F() {
            Console.WriteLine("Derived.M.F");
        }
    }
}

class Test 
{
    static void Main() {
        Derived.M.F();
    }
}
```
viene illustrata una classe annidata `M` che consente di nascondere il metodo `M` definito nella `Base`.

#### <a name="this-access"></a>Questo tipo di accesso

Un tipo annidato e relativo tipo contenitore non è una relazione speciale riguarda *this_access* ([questo accesso](expressions.md#this-access)). In particolare, `this` all'interno di un tipo annidato non può essere usato per fare riferimento ai membri di istanza del tipo contenitore. In casi in cui un tipo annidato deve accedere ai membri di istanza del tipo contenitore, è possibile fornire l'accesso, fornendo il `this` per l'istanza del tipo contenitore come argomento del costruttore per il tipo annidato. Nell'esempio seguente
```csharp
using System;

class C
{
    int i = 123;

    public void F() {
        Nested n = new Nested(this);
        n.G();
    }

    public class Nested
    {
        C this_c;

        public Nested(C c) {
            this_c = c;
        }

        public void G() {
            Console.WriteLine(this_c.i);
        }
    }
}

class Test
{
    static void Main() {
        C c = new C();
        c.F();
    }
}
```
Questa tecnica è illustrata. Un'istanza di `C` crea un'istanza di `Nested` e passa il proprio `this` al `Nested`del costruttore per fornire gli accessi successivi a `C`di membri di istanza.

#### <a name="access-to-private-and-protected-members-of-the-containing-type"></a>Accesso ai membri privati e protetti del tipo contenitore

Un tipo annidato può accedere a tutti i membri che sono accessibili al tipo contenitore, inclusi i membri del tipo contenitore aventi `private` e `protected` l'accessibilità dichiarata. L'esempio
```csharp
using System;

class C 
{
    private static void F() {
        Console.WriteLine("C.F");
    }

    public class Nested 
    {
        public static void G() {
            F();
        }
    }
}

class Test 
{
    static void Main() {
        C.Nested.G();
    }
}
```
viene illustrata una classe `C` che contiene una classe annidata `Nested`. All'interno `Nested`, il metodo `G` chiama il metodo statico `F` definita nella `C`, e `F` privata l'accessibilità dichiarata.

Un tipo annidato può inoltre accedere ai membri protetti definiti in un tipo di base del tipo contenitore. Nell'esempio
```csharp
using System;

class Base 
{
    protected void F() {
        Console.WriteLine("Base.F");
    }
}

class Derived: Base 
{
    public class Nested 
    {
        public void G() {
            Derived d = new Derived();
            d.F();        // ok
        }
    }
}

class Test 
{
    static void Main() {
        Derived.Nested n = new Derived.Nested();
        n.G();
    }
}
```
la classe annidata `Derived.Nested` accede il metodo protetto `F` definiti nel `Derived`della classe base, `Base`, da chiamata tramite un'istanza di `Derived`.

#### <a name="nested-types-in-generic-classes"></a>Tipi annidati in classi generiche

Una dichiarazione di classe generica può contenere dichiarazioni di tipo annidato. I parametri di tipo della classe contenitore sono utilizzabile all'interno di tipi annidati. Una dichiarazione di tipo annidato può contenere parametri di tipo aggiuntivi che si applicano solo al tipo annidato.

Ogni dichiarazione di tipo contenuti all'interno di una dichiarazione di classe generica in modo implicito è una dichiarazione di tipo generico. Quando si scrive un riferimento a un tipo annidato all'interno di un tipo generico, è possibile che il tipo costruito che lo contiene, inclusi i relativi argomenti di tipo, deve chiamarsi. Tuttavia, all'interno della classe outer, il tipo annidato può essere utilizzato senza qualifica; il tipo di istanza della classe esterna può essere utilizzato in modo implicito quando si crea il tipo annidato. L'esempio seguente illustra tre modi diversi corretti per fare riferimento a un tipo costruito creato da `Inner`; le prime due sono equivalenti:
```csharp
class Outer<T>
{
    class Inner<U>
    {
        public static void F(T t, U u) {...}
    }

    static void F(T t) {
        Outer<T>.Inner<string>.F(t, "abc");      // These two statements have
        Inner<string>.F(t, "abc");               // the same effect

        Outer<int>.Inner<string>.F(3, "abc");    // This type is different

        Outer.Inner<string>.F(t, "abc");         // Error, Outer needs type arg
    }
}
```

Anche se è bello lo stile di programmazione un parametro di tipo in un tipo annidato può nascondere un membro o parametro di tipo dichiarato nel tipo esterno:
```csharp
class Outer<T>
{
    class Inner<T>        // Valid, hides Outer's T
    {
        public T t;       // Refers to Inner's T
    }
}
```

### <a name="reserved-member-names"></a>Nomi dei membri riservato

Per semplificare il codice c# in fase di esecuzione implementazione sottostante, per ogni dichiarazione di membro di origine che è una proprietà, un evento o un indicizzatore, l'implementazione deve riservare due firme del metodo in base al tipo di dichiarazione del membro, il nome e il relativo tipo. È un errore in fase di compilazione per un programma dichiarare un membro la cui firma corrisponda a uno di questi riservato firme, anche se l'implementazione sottostante in fase di esecuzione non usa tali prenotazioni.

Le dichiarazioni non introducono i nomi riservati, pertanto non partecipano in ricerca di membri. Tuttavia, una dichiarazione di metodo riservato associate le firme di partecipare all'ereditarietà ([ereditarietà](classes.md#inheritance)) e possono essere nascosti con il `new` modificatore ([il nuovo modificatore](classes.md#the-new-modifier)).

La prenotazione di questi nomi ha tre scopi:

*  Per consentire l'implementazione sottostante da utilizzare un identificatore standard come un nome di metodo per ottenere o impostare l'accesso alla funzionalità del linguaggio c#.
*  Consentire ad altri linguaggi interoperare utilizzando un identificatore standard come un nome di metodo per ottenere o impostare l'accesso alla funzionalità del linguaggio c#.
*  Al fine di garantire che l'origine è accettata da un compilatore conforme è accettato da un'altra, rendendo le specifiche del membro riservato a nomi coerenti in tutte le implementazioni in c#.

La dichiarazione di un distruttore ([distruttori](classes.md#destructors)) anche fa sì che una firma per la prenotazione ([i nomi dei membri sono riservati per i distruttori](classes.md#member-names-reserved-for-destructors)).

#### <a name="member-names-reserved-for-properties"></a>Nomi dei membri riservati per le proprietà

Per una proprietà `P` ([delle proprietà](classes.md#properties)) del tipo `T`, le firme seguenti sono riservate:

```csharp
T get_P();
void set_P(T value);
```

Entrambe le firme sono riservate, anche se la proprietà è di sola lettura o in sola lettura.

Nell'esempio
```csharp
using System;

class A
{
    public int P {
        get { return 123; }
    }
}

class B: A
{
    new public int get_P() {
        return 456;
    }

    new public void set_P(int value) {
    }
}

class Test
{
    static void Main() {
        B b = new B();
        A a = b;
        Console.WriteLine(a.P);
        Console.WriteLine(b.P);
        Console.WriteLine(b.get_P());
    }
}
```
una classe `A` definisce una proprietà di sola lettura `P`, riservando in questo modo le firme per `get_P` e `set_P` metodi. Una classe `B` deriva da `A` e nasconde entrambe le firme riservate. L'esempio produce l'output:
```
123
123
456
```

#### <a name="member-names-reserved-for-events"></a>Nomi dei membri riservati per gli eventi

Per un evento `E` ([eventi](classes.md#events)) del tipo di delegato `T`, le firme seguenti sono riservate:
```csharp
void add_E(T handler);
void remove_E(T handler);
```

#### <a name="member-names-reserved-for-indexers"></a>Nomi dei membri riservati per gli indicizzatori

Per un indicizzatore ([indicizzatori](classes.md#indexers)) di tipo `T` con l'elenco di parametri `L`, le firme seguenti sono riservate:
```csharp
T get_Item(L);
void set_Item(L, T value);
```

Entrambe le firme sono riservate, anche se l'indicizzatore è in sola lettura o in sola lettura.

Inoltre il nome del membro `Item` è riservato.

#### <a name="member-names-reserved-for-destructors"></a>Nomi dei membri riservati per i distruttori

Per una classe che contiene un distruttore ([distruttori](classes.md#destructors)), viene riservata la firma seguente:
```csharp
void Finalize();
```

## <a name="constants"></a>Costanti

Oggetto ***costante*** è un membro di classe che rappresenta un valore costante: un valore che può essere calcolato in fase di compilazione. Oggetto *constant_declaration* introduce una o più costanti di un determinato tipo.

```antlr
constant_declaration
    : attributes? constant_modifier* 'const' type constant_declarators ';'
    ;

constant_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    ;

constant_declarators
    : constant_declarator (',' constant_declarator)*
    ;

constant_declarator
    : identifier '=' constant_expression
    ;
```

Oggetto *constant_declaration* può includere un set di *attributi* ([attributi](attributes.md)), un `new` modificatore ([il nuovo modificatore](classes.md#the-new-modifier)), e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)). Gli attributi e modificatori si applicano a tutti i membri dichiarati per il *constant_declaration*. Anche se le costanti sono considerate membri statici, un *constant_declaration* non richiede né consente a un `static` modificatore. È un errore per più volte lo stesso modificatore in una dichiarazione di costante.

Il *tipo* di un *constant_declaration* specifica il tipo di membri introdotti dalla dichiarazione. Il tipo è seguito da un elenco delle *constant_declarator*s, ognuno dei quali presenta un nuovo membro. Oggetto *constant_declarator* costituito da un *identificatore* che denomina il membro, seguito da un "`=`" token, seguito da un *constant_expression* ([ Espressioni costanti](expressions.md#constant-expressions)) che specifica il valore del membro.

Il *tipo* specificato in una costante deve essere dichiarazione `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `string`, un *enum_type*, o un oggetto *reference_type*. Ciascuna *constant_expression* deve restituire un valore del tipo di destinazione o di un tipo che può essere convertito nel tipo di destinazione tramite una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)).

Il *tipo* di una costante deve essere accessibile almeno quanto la costante ([vincoli accessibilità](basic-concepts.md#accessibility-constraints)).

Viene ottenuto il valore di una costante in un'espressione che usa un' *simple_name* ([nomi semplici](expressions.md#simple-names)) o un *member_access* ([l'accesso ai membri](expressions.md#member-access)).

Una costante è possibile sia inclusa una *constant_expression*. Di conseguenza, è possibile utilizzare una costante in qualsiasi costrutto che richiede un *constant_expression*. Esempi di tali costrutti `case` etichette `goto case` istruzioni, `enum` le dichiarazioni dei membri, attributi e le altre dichiarazioni costante.

Come descritto in [espressioni costanti](expressions.md#constant-expressions), un *constant_expression* è un'espressione che può essere valutata interamente in fase di compilazione. Poiché l'unico modo per creare un valore non null di un *reference_type* diverso da `string` consiste nell'applicare il `new` operatore e, poiché il `new` operatore non è consentito un *constant_ espressione*, l'unico valore possibile per le costanti dei *reference_type*s diverso `string` è `null`.

Quando si desidera usare un nome simbolico per un valore costante, ma quando il tipo di tale valore non è consentito in una dichiarazione di costante o quando il valore non può essere calcolato in fase di compilazione da un *constant_expression*, un `readonly` campo ( [Campi di sola lettura](classes.md#readonly-fields)) potrebbe invece essere utilizzato.

Una dichiarazione di costante che dichiara più costanti equivale a più dichiarazioni di singole costanti con gli stessi attributi, i modificatori e tipo. Esempio:
```csharp
class A
{
    public const double X = 1.0, Y = 2.0, Z = 3.0;
}
```
equivale a
```csharp
class A
{
    public const double X = 1.0;
    public const double Y = 2.0;
    public const double Z = 3.0;
}
```

Le costanti possono dipendere da altri costanti all'interno del programma stesso, purché le dipendenze non sono di natura circolare. Il compilatore vengono disposti automaticamente valutare le dichiarazioni costante nell'ordine appropriato. Nell'esempio
```csharp
class A
{
    public const int X = B.Z + 1;
    public const int Y = 10;
}

class B
{
    public const int Z = A.Y + 1;
}
```
il compilatore valuta innanzitutto `A.Y`, quindi valuta `B.Z`e infine restituisce `A.X`, restituendo i valori `10`, `11`, e `12`. Le dichiarazioni di costante possono dipendere da costanti da un altro programma, ma queste dipendenze sono possibili solo in una sola direzione. Che fa riferimento all'esempio precedente, se `A` e `B` sono stati dichiarati in programmi distinti, sarebbe possibile `A.X` dipendere `B.Z`, ma `B.Z` potrebbe quindi non simultaneamente dipendono da `A.Y`.

## <a name="fields"></a>Campi

Oggetto ***campo*** è un membro che rappresenta una variabile associata a un oggetto o una classe. Oggetto *field_declaration* introduce uno o più campi di un determinato tipo.

```antlr
field_declaration
    : attributes? field_modifier* type variable_declarators ';'
    ;

field_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'readonly'
    | 'volatile'
    | field_modifier_unsafe
    ;

variable_declarators
    : variable_declarator (',' variable_declarator)*
    ;

variable_declarator
    : identifier ('=' variable_initializer)?
    ;

variable_initializer
    : expression
    | array_initializer
    ;
```

Oggetto *field_declaration* può includere un set di *attributi* ([attributi](attributes.md)), un `new` modificatore ([il nuovo modificatore](classes.md#the-new-modifier)), un oggetto combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)) e una `static` modificatore ([campi statici e di istanza](classes.md#static-and-instance-fields)). Inoltre, un *field_declaration* può includere una `readonly` modificatore ([campi di sola lettura](classes.md#readonly-fields)) o un `volatile` modificatore ([campi volatili](classes.md#volatile-fields)) ma non entrambi. Gli attributi e modificatori si applicano a tutti i membri dichiarati per il *field_declaration*. È un errore per lo stesso modificatore venga visualizzato più volte in una dichiarazione di campo.

Il *tipo* di un *field_declaration* specifica il tipo di membri introdotti dalla dichiarazione. Il tipo è seguito da un elenco delle *variable_declarator*s, ognuno dei quali presenta un nuovo membro. Oggetto *variable_declarator* è costituito da un *identifier* che denomina tale membro, seguito facoltativamente da un "`=`" token e una *variable_initializer* ([ Gli inizializzatori di variabili](classes.md#variable-initializers)) che specifica il valore iniziale di tale membro.

Il *tipo* di un campo deve essere accessibile almeno quanto il campo stesso ([vincoli accessibilità](basic-concepts.md#accessibility-constraints)).

Viene ottenuto il valore di un campo in un'espressione che usa un' *simple_name* ([nomi semplici](expressions.md#simple-names)) o un *member_access* ([l'accesso ai membri](expressions.md#member-access)). Il valore di un campo di sola lettura non viene modificato utilizzando un *assegnazione* ([gli operatori di assegnazione](expressions.md#assignment-operators)). Il valore di un campo di sola lettura non può essere ottenere e modificare l'utilizzo di incremento e decremento (operatori) ([forma suffissa di incremento e decremento (operatori)](expressions.md#postfix-increment-and-decrement-operators)) e incremento prefisso e decremento (operatori) ([prefisso gli operatori di incremento e decremento](expressions.md#prefix-increment-and-decrement-operators)).

Una dichiarazione di campo che dichiara più campi è equivalente alla più dichiarazioni di singoli campi con gli stessi attributi, i modificatori e tipo. Esempio:
```csharp
class A
{
    public static int X = 1, Y, Z = 100;
}
```
equivale a
```csharp
class A
{
    public static int X = 1;
    public static int Y;
    public static int Z = 100;
}
```

### <a name="static-and-instance-fields"></a>Campi statici e di istanza

Quando una dichiarazione di campo include un' `static` modificatore, i campi introdotti dalla dichiarazione sono ***i campi statici***. Se non si specifica `static` modificatore è presente, i campi introdotti dalla dichiarazione sono ***campi di istanza***. I campi statici e i campi di istanza sono due dei diversi tipi di variabili ([variabili](variables.md)) supportati da c#, e in alcuni casi vengono definiti per ***variabili statiche*** e ***le variabili di istanze*** , rispettivamente.

Un campo statico non fa parte di un'istanza specifica; In alternativa, è condiviso tra tutte le istanze di un tipo chiuso ([aperto e chiuso tipi](types.md#open-and-closed-types)). Indipendentemente dal numero di istanze di un tipo di classe chiusi venga creato, è sempre un'unica copia di un campo statico per il dominio di applicazione associata.

Ad esempio:
```csharp
class C<V>
{
    static int count = 0;

    public C() {
        count++;
    }

    public static int Count {
        get { return count; }
    }
}

class Application
{
    static void Main() {
        C<int> x1 = new C<int>();
        Console.WriteLine(C<int>.Count);        // Prints 1

        C<double> x2 = new C<double>();
        Console.WriteLine(C<int>.Count);        // Prints 1

        C<int> x3 = new C<int>();
        Console.WriteLine(C<int>.Count);        // Prints 2
    }
}
```

Un campo di istanza appartiene a un'istanza. In particolare, ogni istanza di una classe contiene un set separato di tutti i campi di istanza di tale classe.

Quando si fa riferimento a un campo un *member_access* ([l'accesso ai membri](expressions.md#member-access)) nel formato `E.M`se `M` è un campo statico, `E` deve indicare un tipo contenente `M` e se `M` è un campo di istanza, È necessario che un'istanza di un tipo contenente `M`.

Le differenze tra statica e i membri di istanza sono descritti dettagliatamente nella [i membri statici e di istanza](classes.md#static-and-instance-members).

### <a name="readonly-fields"></a>Campi di sola lettura

Quando un *field_declaration* include un `readonly` modificatore, i campi introdotti dalla dichiarazione sono ***campi di sola lettura***. Le assegnazioni dirette a campi di sola lettura possono verificarsi solo come parte di tale dichiarazione o in un costruttore di istanza o un costruttore statico nella stessa classe. (Un campo di sola lettura può essere assegnato a più volte in questi contesti.) In particolare, le assegnazioni dirette a un `readonly` campo sono consentiti solo nei contesti seguenti:

*  Nel *variable_declarator* che introduce il campo (includendo un *variable_initializer* nella dichiarazione).
*  Per un campo di istanza, i costruttori di istanza della classe che contiene la dichiarazione di campo. per un campo statico, nel costruttore statico della classe che contiene la dichiarazione del campo. Questi sono gli unici contesti in cui è possibile passare un `readonly` campo come un `out` o `ref` parametro.

Tentativo di assegnare a un `readonly` campo oppure passarlo come un' `out` o `ref` parametro in qualsiasi altro contesto è un errore in fase di compilazione.

#### <a name="using-static-readonly-fields-for-constants"></a>Uso di campi di sola lettura statico per le costanti

Oggetto `static readonly` campo è utile quando si desidera ottenere un nome simbolico per un valore costante, ma quando il tipo di valore non è consentito un `const` dichiarazione, o quando il valore non può essere calcolato in fase di compilazione. Nell'esempio
```csharp
public class Color
{
    public static readonly Color Black = new Color(0, 0, 0);
    public static readonly Color White = new Color(255, 255, 255);
    public static readonly Color Red = new Color(255, 0, 0);
    public static readonly Color Green = new Color(0, 255, 0);
    public static readonly Color Blue = new Color(0, 0, 255);

    private byte red, green, blue;

    public Color(byte r, byte g, byte b) {
        red = r;
        green = g;
        blue = b;
    }
}
```
il `Black`, `White`, `Red`, `Green`, e `Blue` membri non possono essere dichiarate come `const` membri perché i relativi valori non possono essere calcolati in fase di compilazione. Tuttavia, dichiararli `static readonly` dispone invece di ottenere lo stesso risultato.

#### <a name="versioning-of-constants-and-static-readonly-fields"></a>Controllo delle versioni di costanti e i campi di sola lettura statico

Le costanti e i campi di sola lettura hanno una semantica di versioni binario diverso. Quando un'espressione fa riferimento a una costante, il valore della costante viene ottenuto in fase di compilazione, ma quando un'espressione fa riferimento a un campo di sola lettura, fino alla fase di esecuzione non viene ottenuto il valore del campo. Si consideri un'applicazione costituita da due programmi distinti:
```csharp
using System;

namespace Program1
{
    public class Utils
    {
        public static readonly int X = 1;
    }
}

namespace Program2
{
    class Test
    {
        static void Main() {
            Console.WriteLine(Program1.Utils.X);
        }
    }
}
```

Il `Program1` e `Program2` gli spazi dei nomi indicano due programmi che vengono compilati separatamente. In quanto `Program1.Utils.X` viene dichiarato come un campo statico di sola lettura, il valore restituito dal `Console.WriteLine` istruzione non è noto in fase di compilazione, ma viene ottenuta in fase di esecuzione. Di conseguenza, se il valore di `X` viene modificato e `Program1` viene ricompilata, il `Console.WriteLine` istruzione restituirà il nuovo valore anche se `Program2` non viene ricompilato. Tuttavia, presentano `X` una costante, il valore di stato `X` sarebbe stato ottenuto al momento `Program2` è stato compilato e verrebbe influenzato dalle modifiche apportate in `Program1` fino a quando non `Program2` viene ricompilato.

### <a name="volatile-fields"></a>Campi volatili

Quando un *field_declaration* include un `volatile` sono campi introdotte dalla dichiarazione di tale modificatore ***campi volatili***.

Per i campi non volatile, tecniche di ottimizzazione di riordinare le istruzioni possono portare a risultati imprevisti e imprevedibili nei programmi multithread che accedono ai campi senza la sincronizzazione, ad esempio quello fornito dal *lock_statement*  ([Istruzione lock](statements.md#the-lock-statement)). Queste ottimizzazioni possono essere eseguite dal compilatore, dal sistema di runtime o dall'hardware. Per i campi volatili, le ottimizzazioni di riordino sono limitate:

*  La lettura di un campo volatile viene definita un' ***lettura volatile***. Dispone di un'operazione di lettura volatile "acquisire semantica"; vale a dire, è garantito a verificarsi prima di tutti i riferimenti alla memoria che si verificano dopo di essa nella sequenza di istruzioni.
*  La scrittura di un campo volatile viene definita un' ***scrittura di tipo volatile***. Scrittura volatile è "la semantica di rilascio"; vale a dire, è garantito a verificarsi dopo qualsiasi riferimento alla memoria prima le istruzioni di scrittura nella sequenza di istruzioni.

Queste limitazioni garantiscono che tutti i thread considereranno scritture di tipo volatile eseguite da altri thread nell'ordine in cui sono stati eseguiti. Un'implementazione conforme, non è necessario fornire un singolo ordinamento totale di scritture volatili visualizzate da tutti i thread di esecuzione. Il tipo di un campo volatile deve essere uno dei seguenti:

*  Oggetto *reference_type*.
*  Il tipo `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `char`, `float`, `bool`, `System.IntPtr`, o` System.UIntPtr`.
*  Un' *enum_type* specificando un tipo di base di enumerazione del `byte`, `sbyte`, `short`, `ushort`, `int`, o `uint`.

L'esempio
```csharp
using System;
using System.Threading;

class Test
{
    public static int result;   
    public static volatile bool finished;

    static void Thread2() {
        result = 143;    
        finished = true; 
    }

    static void Main() {
        finished = false;

        // Run Thread2() in a new thread
        new Thread(new ThreadStart(Thread2)).Start();

        // Wait for Thread2 to signal that it has a result by setting
        // finished to true.
        for (;;) {
            if (finished) {
                Console.WriteLine("result = {0}", result);
                return;
            }
        }
    }
}
```
produce l'output:
```
result = 143
```

In questo esempio, il metodo `Main` avvia un nuovo thread che esegue il metodo `Thread2`. Questo metodo archivia un valore in un campo volatile non chiamato `result`, quindi lo archivia `true` nel campo volatile `finished`. Il thread principale attende che il campo `finished` sia impostato su `true`, quindi legge il campo `result`. Poiché `finished` è stata dichiarata `volatile`, il thread principale deve leggere il valore `143` dal campo `result`. Se il campo `finished` non fosse stato dichiarato `volatile`, sarà consentito per l'archivio di `result` deve essere visibile per il thread principale dopo l'archiviazione `finished`e pertanto per il thread principale leggere il valore `0` dal campo `result`. La dichiarazione `finished` come un `volatile` campo impedisce qualsiasi tali incoerenze.

### <a name="field-initialization"></a>Inizializzazione del campo

Il valore iniziale di un campo, che si tratti di un campo statico o un campo di istanza, è il valore predefinito ([i valori predefiniti](variables.md#default-values)) del tipo del campo. Non è possibile osservare il valore di un campo prima che si è verificato l'inizializzazione predefinita e un campo non è pertanto mai "inizializzato". L'esempio
```csharp
using System;

class Test
{
    static bool b;
    int i;

    static void Main() {
        Test t = new Test();
        Console.WriteLine("b = {0}, i = {1}", b, t.i);
    }
}
```
produce l'output
```
b = False, i = 0
```
in quanto `b` e `i` vengono automaticamente inizializzate entrambe su valori predefiniti.

### <a name="variable-initializers"></a>Inizializzatori di variabili

Le dichiarazioni di campo possono includere *variable_initializer*s. Per i campi statici, gli inizializzatori di variabili corrispondono alle istruzioni di assegnazione eseguite durante l'inizializzazione delle classi. Ad esempio campi, gli inizializzatori di variabili corrispondono alle istruzioni di assegnazione che vengono eseguite quando viene creata un'istanza della classe.

L'esempio
```csharp
using System;

class Test
{
    static double x = Math.Sqrt(2.0);
    int i = 100;
    string s = "Hello";

    static void Main() {
        Test a = new Test();
        Console.WriteLine("x = {0}, i = {1}, s = {2}", x, a.i, a.s);
    }
}
```
produce l'output
```
x = 1.4142135623731, i = 100, s = Hello
```
perché un'assegnazione a `x` si verifica quando vengono eseguiti gli inizializzatori di campo statico e le assegnazioni ai `i` e `s` quando vengono eseguiti gli inizializzatori di campo di istanza.

Inizializzazione del valore predefinito descritto nella [inizializzazione del campo](classes.md#field-initialization) si verifica per tutti i campi, inclusi i campi con inizializzatori. Di conseguenza, quando una classe viene inizializzata, tutti i campi statici nella classe vengono inizializzati sui valori predefiniti e quindi gli inizializzatori di campo statico vengono eseguiti in ordine testuale. Analogamente, quando viene creata un'istanza di una classe, tutti i campi di istanza in quell'istanza vengono innanzitutto inizializzati sui valori predefiniti e quindi gli inizializzatori di campo di istanza vengono eseguiti in ordine testuale.

È possibile che i campi statici con gli inizializzatori di variabili da osservare nel relativo stato di valore predefinito. Tuttavia, ciò è fortemente sconsigliato come una questione di stile. L'esempio
```csharp
using System;

class Test
{
    static int a = b + 1;
    static int b = a + 1;

    static void Main() {
        Console.WriteLine("a = {0}, b = {1}", a, b);
    }
}
```
Mostra questo comportamento. Nonostante le definizioni circolari di una e b, il programma è valido. Il risultato è l'output
```
a = 1, b = 2
```
Poiché i campi statici `a` e `b` vengono inizializzati `0` (il valore predefinito per `int`) prima dell'esecuzione dei relativi inizializzatori. Quando l'inizializzatore per `a` viene eseguito, il valore di `b` è uguale a zero e pertanto `a` viene inizializzata su `1`. Quando l'inizializzatore per `b` viene eseguito, il valore di `a` già `1`e pertanto `b` viene inizializzato su `2`.

#### <a name="static-field-initialization"></a>Inizializzazione del campo statico

Inizializzatori di campo statico di una classe corrispondono a una sequenza di assegnazioni che vengono eseguite nell'ordine testuale in cui appaiono nella dichiarazione di classe. Se un costruttore statico ([costruttori statici](classes.md#static-constructors)) è presente nella classe, gli inizializzatori di campo statico viene eseguito immediatamente prima dell'esecuzione di tale costruttore statico. In caso contrario, gli inizializzatori di campo statico vengono eseguiti in un momento dipende dall'implementazione del primo utilizzo di un campo statico di tale classe. L'esempio
```csharp
using System;

class Test 
{ 
    static void Main() {
        Console.WriteLine("{0} {1}", B.Y, A.X);
    }

    public static int F(string s) {
        Console.WriteLine(s);
        return 1;
    }
}

class A
{
    public static int X = Test.F("Init A");
}

class B
{
    public static int Y = Test.F("Init B");
}
```
potrebbe produrre l'output:
```
Init A
Init B
1 1
```
In alternativa l'output:
```
Init B
Init A
1 1
```
Poiché l'esecuzione di `X`dell'inizializzatore e `Y`dell'inizializzatore può verificarsi in qualsiasi ordine; se restano validi solo prima i riferimenti ai campi. Tuttavia, nell'esempio:
```csharp
using System;

class Test
{
    static void Main() {
        Console.WriteLine("{0} {1}", B.Y, A.X);
    }

    public static int F(string s) {
        Console.WriteLine(s);
        return 1;
    }
}

class A
{
    static A() {}

    public static int X = Test.F("Init A");
}

class B
{
    static B() {}

    public static int Y = Test.F("Init B");
}
```
l'output deve essere:
```
Init B
Init A
1 1
```
Poiché le regole relative all'esecuzione dei costruttori statici (come definito in [costruttori statici](classes.md#static-constructors)) che forniscono `B`del costruttore statico (e pertanto `B`degli inizializzatori di campo statico) deve essere eseguito prima `A`del costruttore statico e gli inizializzatori di campo.

#### <a name="instance-field-initialization"></a>Inizializzazione di campi di istanza

Gli inizializzatori variabile di campo di istanza di una classe corrispondono a una sequenza di assegnazioni che vengono eseguiti immediatamente dopo l'inserimento di uno dei costruttori di istanza di ([inizializzatori del costruttore](classes.md#constructor-initializers)) di tale classe. Inizializzatori vengono eseguiti nell'ordine testuale in cui appaiono nella dichiarazione di classe. Il processo di creazione e inizializzazione dell'istanza classe è descritto più dettagliatamente in [costruttori di istanze](classes.md#instance-constructors).

Un inizializzatore di variabile per un campo di istanza non può fare riferimento all'istanza creata. Di conseguenza, è un errore in fase di compilazione per fare riferimento a `this` in un inizializzatore di variabile, perché è un errore in fase di compilazione per un inizializzatore di variabile fare riferimento a un membro di istanza tramite un *simple_name*. Nell'esempio
```csharp
class A
{
    int x = 1;
    int y = x + 1;        // Error, reference to instance member of this
}
```
l'inizializzatore di variabile `y` comporta un errore in fase di compilazione perché fa riferimento a un membro dell'istanza da creare.

## <a name="methods"></a>Metodi

Un ***metodo*** è un membro che implementa un calcolo o un'azione che può essere eseguita da un oggetto o una classe. I metodi vengono dichiarati usando *method_declaration*s:

```antlr
method_declaration
    : method_header method_body
    ;

method_header
    : attributes? method_modifier* 'partial'? return_type member_name type_parameter_list?
      '(' formal_parameter_list? ')' type_parameter_constraints_clause*
    ;

method_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | 'async'
    | method_modifier_unsafe
    ;

return_type
    : type
    | 'void'
    ;

member_name
    : identifier
    | interface_type '.' identifier
    ;

method_body
    : block
    | '=>' expression ';'
    | ';'
    ;
```

Oggetto *method_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso ](classes.md#access-modifiers)), il `new` ([il nuovo modificatore](classes.md#the-new-modifier)), `static` ([metodi statici e di istanza](classes.md#static-and-instance-methods)), `virtual` ([metodi virtuali](classes.md#virtual-methods)), `override` ([Metodi di override](classes.md#override-methods)), `sealed` ([Sealed i metodi](classes.md#sealed-methods)), `abstract` ([metodi astratti](classes.md#abstract-methods)), e `extern` ([Metodi esterni](classes.md#external-methods)) modificatori.

Una dichiarazione contiene una combinazione valida dei modificatori se tutti gli elementi seguenti sono vere:

*  La dichiarazione include una combinazione valida dei modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)).
*  La dichiarazione non include più volte lo stesso modificatore.
*  La dichiarazione include al massimo uno dei modificatori seguenti: `static`, `virtual`, e `override`.
*  La dichiarazione include al massimo uno dei modificatori seguenti: `new` e `override`.
*  Se la dichiarazione include il `abstract` modificatore, quindi la dichiarazione di non includere uno qualsiasi dei modificatori seguenti: `static`, `virtual`, `sealed` o `extern`.
*  Se la dichiarazione include il `private` modificatore, quindi la dichiarazione di non includere uno qualsiasi dei modificatori seguenti: `virtual`, `override`, o `abstract`.
*  Se la dichiarazione include il `sealed` modificatore, quindi la dichiarazione include anche il `override` modificatore.
*  Se la dichiarazione include il `partial` modificatore, quindi, non include uno dei modificatori seguenti: `new`, `public`, `protected`, `internal`, `private`, `virtual`, `sealed`, `override` , `abstract`, o `extern`.

Un metodo che contiene il `async` modificatore è una funzione asincrona e segue le regole descritte in [le funzioni asincrone](classes.md#async-functions).

Il *return_type* di un metodo dichiarazione specifica il tipo di valore calcolato e restituito dal metodo. Il *return_type* è `void` se il metodo non restituisce un valore. Se la dichiarazione include il `partial` modificatore, il tipo restituito deve essere `void`.

Il *member_name* specifica il nome del metodo. A meno che il metodo è un'implementazione di membro di interfaccia esplicita ([tali implementazioni](interfaces.md#explicit-interface-member-implementations)), il *member_name* è semplicemente un' *identificatore*. Per un'implementazione esplicita dell'interfaccia membro, il *member_name* costituito da un *interface_type* seguita da un "`.`" e una *identificatore*.

L'opzione facoltativa *type_parameter_list* specifica i parametri di tipo del metodo ([parametri di tipo](classes.md#type-parameters)). Se un *type_parameter_list* viene specificato il metodo è un ***metodo generico***. Se il metodo ha un `extern` modificatore, un' *type_parameter_list* non può essere specificato.

L'opzione facoltativa *formal_parameter_list* specifica i parametri del metodo ([i parametri del metodo](classes.md#method-parameters)).

L'opzione facoltativa *type_parameter_constraints_clause*specificano i vincoli sui parametri di tipo individuale ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)) e può essere specificato solo se un *type_parameter_ elenco* viene indicato anche, e il metodo non ha un `override` modificatore.

Il *return_type* e ognuno dei tipi a cui fa riferimento il *formal_parameter_list* di un metodo deve essere accessibile almeno quanto il metodo di stesso ([vincoli accessibilità](basic-concepts.md#accessibility-constraints)).

Il *method_body* entrambi un punto e virgola, una ***corpo dell'istruzione*** o un ***corpo dell'espressione***. È costituito da un corpo dell'istruzione di un *blocco*, che consente di specificare le istruzioni da eseguire quando viene richiamato il metodo. Costituito da un corpo di espressione `=>` seguita da un' *espressione* e un punto e virgola e indica una singola espressione da eseguire quando viene richiamato il metodo. 

Per la `abstract` e `extern` metodi, il *method_body* costituito semplicemente da un punto e virgola. Per la `partial` metodi di *method_body* può essere costituita da un punto e virgola, un corpo del blocco o un corpo di espressione. Per tutti gli altri metodi, il *method_body* è un corpo del blocco o un corpo di espressione.

Se il *method_body* costituito da un punto e virgola, quindi la dichiarazione non può includere il `async` modificatore.

Il nome, l'elenco di parametri di tipo e l'elenco di parametri formali di un metodo di definire la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) del metodo. In particolare, la firma di un metodo costituita dal relativo nome, il numero di parametri di tipo e il numero, i modificatori e i tipi di parametri formali. A tale scopo, viene identificato qualsiasi parametro di tipo del metodo che si verifica nel tipo di un parametro formale non in base al nome, ma in base alla posizione ordinale nell'elenco di argomenti tipo del metodo. Il tipo restituito non fa parte della firma del metodo, né è i nomi dei parametri di tipo o i parametri formali.

Il nome di un metodo deve essere diverso dai nomi di tutti gli altri metodi-non dichiarati nella stessa classe. Inoltre, la firma di un metodo deve essere diversa dalle firme di tutti gli altri metodi dichiarati nella stessa classe, e due metodi dichiarati nella stessa classe potrebbero non avere firme che differiscono dal `ref` e `out`.

Il metodo *type_parameter*sono nell'ambito in tutta il *method_declaration*e può essere utilizzato per formare tipi in tale ambito nel *return_type*, *method_body*, e *type_parameter_constraints_clause*s ma non nella *attributi*.

Tutti i parametri formali e parametri di tipo devono avere nomi diversi.

### <a name="method-parameters"></a>Parametri del metodo

I parametri di un metodo, se presenti, vengono dichiarati per il metodo *formal_parameter_list*.

```antlr
formal_parameter_list
    : fixed_parameters
    | fixed_parameters ',' parameter_array
    | parameter_array
    ;

fixed_parameters
    : fixed_parameter (',' fixed_parameter)*
    ;

fixed_parameter
    : attributes? parameter_modifier? type identifier default_argument?
    ;

default_argument
    : '=' expression
    ;

parameter_modifier
    : 'ref'
    | 'out'
    | 'this'
    ;

parameter_array
    : attributes? 'params' array_type identifier
    ;
```

Elenco di parametri formali è costituito da uno o più parametri delimitato da virgole che può essere solo l'ultimo un *parameter_array*.

Oggetto *fixed_parameter* costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), facoltativo `ref`, `out` o `this` modificatore, un *tipo*, un *identifier* facoltativo *default_argument*. Ciascuna *fixed_parameter* dichiara un parametro del tipo specificato con il nome specificato. Il `this` modificatore definisce il metodo come metodo di estensione ed è consentito solo nel primo parametro di un metodo statico. I metodi di estensione sono descritti nella [metodi di estensione](classes.md#extension-methods).

Oggetto *fixed_parameter* con un *default_argument* è noto come un' ***parametro facoltativo***, mentre un *fixed_parameter* senza un *default_argument* è un ***parametro obbligatorio***. Un parametro obbligatorio non può essere visualizzato dopo un parametro facoltativo in una *formal_parameter_list*.

Oggetto `ref` oppure `out` parametro non può avere un *default_argument*. Il *espressione* in un *default_argument* deve essere uno dei seguenti:

*  un *constant_expression*
*  un'espressione nel formato `new S()` in cui `S` è un tipo valore
*  un'espressione nel formato `default(S)` in cui `S` è un tipo valore

Il *espressione* deve essere convertibile in modo implicito da un'identità o una conversione che ammette valori null per il tipo del parametro.

Se si utilizzano parametri facoltativi in una dichiarazione di metodo parziale implementazione ([metodi parziali](classes.md#partial-methods)), un'implementazione di membro di interfaccia esplicita ([implementazioni esplicite dell'interfaccia membro](interfaces.md#explicit-interface-member-implementations)) o in un dichiarazione di indicizzatore parametro singolo ([indicizzatori](classes.md#indexers)) il compilatore deve generare un avviso, poiché questi membri non possono mai essere richiamati in modo che gli argomenti di essere omessa.

Oggetto *parameter_array* costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), un `params` modificatore, un' *array_type*, e un *identificatore*. Una matrice di parametri dichiara un singolo parametro del tipo di matrice specificato con il nome specificato. Il *array_type* di un parametro di matrice deve essere un tipo di matrici unidimensionali ([tipi matrice](arrays.md#array-types)). In una chiamata al metodo, una matrice di parametri consente a un singolo argomento per specificare il tipo matrice specificata o consente zero o più argomenti del tipo di elemento di matrice per specificare. Matrici di parametri sono consultare [matrici di parametri](classes.md#parameter-arrays).

Oggetto *parameter_array* possono verificarsi dopo un parametro facoltativo, ma non può avere un valore predefinito, ovvero l'omissione di argomenti per un *parameter_array* invece comporterà la creazione di una matrice vuota.

L'esempio seguente illustra i diversi tipi di parametri:
```csharp
public void M(
    ref int      i,
    decimal      d,
    bool         b = false,
    bool?        n = false,
    string       s = "Hello",
    object       o = null,
    T            t = default(T),
    params int[] a
) { }
```

Nel *formal_parameter_list* per `M`, `i` è un parametro di riferimento obbligatorio `d` è un parametro obbligatorio, `b`, `s`, `o` e `t` valore facoltativo di parametri e `a` è una matrice di parametri.

Una dichiarazione di metodo consente di creare uno spazio di dichiarazione separata per i parametri, parametri di tipo e le variabili locali. I nomi vengono introdotti in questo spazio di dichiarazione per l'elenco di parametri di tipo e l'elenco di parametri formali del metodo e le dichiarazioni di variabili locali nel *blocco* del metodo. È un errore per due membri di uno spazio di dichiarazione di metodo che lo stesso nome. È un errore per lo spazio di dichiarazione di metodo e lo spazio di dichiarazione di variabile locale di uno spazio di dichiarazione annidati per contenere gli elementi con lo stesso nome.

Una chiamata al metodo ([chiamate al metodo](expressions.md#method-invocations)) Crea una copia, specifica della chiamata, dei parametri formali e le variabili locali del metodo, l'elenco di argomenti della chiamata assegna i valori o riferimenti a variabili per il appena creati parametri formali. All'interno di *blocco* di un metodo, i parametri formali possono fare riferimento i relativi identificatori nella *simple_name* espressioni ([nomi semplici](expressions.md#simple-names)).

Esistono quattro tipi di parametri formali:

*  Parametri di valore, che vengono dichiarati senza tutti i modificatori.
*  Fare riferimento ai parametri, che vengono dichiarati con la `ref` modificatore.
*  I parametri di output, che vengono dichiarati con la `out` modificatore.
*  Matrici di parametri, che vengono dichiarate con la `params` modificatore.

Come descritto in [firme e overload](basic-concepts.md#signatures-and-overloading), il `ref` e `out` modificatori fanno parte della firma del metodo, ma il `params` modificatore non.

#### <a name="value-parameters"></a>Parametri di valore

Un parametro dichiarato con nessun modificatore è un parametro di valore. Un parametro di valore corrisponde a una variabile locale che ottiene il valore iniziale dall'argomento corrispondente fornito nella chiamata al metodo.

Quando un parametro formale è un parametro di valore, l'argomento corrispondente in una chiamata al metodo deve essere un'espressione che è convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) al tipo di parametro formale.

Un metodo è consentito assegnare nuovi valori per un parametro di valore. Tali assegnazioni riguardano solo il percorso di archiviazione locale rappresentato dal parametro value — non hanno alcun effetto sull'argomento effettivo specificato nella chiamata al metodo.

#### <a name="reference-parameters"></a>Parametri per riferimento

Un parametro dichiarato con un `ref` modificatore è un parametro di riferimento. A differenza di un parametro di valore, un parametro di riferimento non crea un nuovo percorso di archiviazione. Al contrario, un parametro di riferimento rappresenta la stessa posizione di archiviazione della variabile specificata come argomento nella chiamata al metodo.

Quando un parametro formale è un parametro per riferimento, l'argomento corrispondente in una chiamata al metodo deve essere costituito la parola chiave `ref` seguita da un *variable_reference* ([regole Precise per la determinazione assegnazione certa](variables.md#precise-rules-for-determining-definite-assignment)) dello stesso tipo del parametro formale. Una variabile deve essere assegnata prima che può essere passata come parametro di riferimento.

All'interno di un metodo, un parametro di riferimento viene sempre considerato assegnato.

Un metodo dichiarato come iteratore ([iteratori](classes.md#iterators)) non possono avere parametri di riferimento.

L'esempio
```csharp
using System;

class Test
{
    static void Swap(ref int x, ref int y) {
        int temp = x;
        x = y;
        y = temp;
    }

    static void Main() {
        int i = 1, j = 2;
        Swap(ref i, ref j);
        Console.WriteLine("i = {0}, j = {1}", i, j);
    }
}
```
produce l'output
```
i = 2, j = 1
```

Per la chiamata `Swap` nelle `Main`, `x` rappresenta `i` e `y` rappresenta `j`. Di conseguenza, la chiamata ha l'effetto di lo scambio dei valori della `i` e `j`.

In un metodo che accetta i parametri di riferimento è possibile che più nomi rappresentare la posizione di archiviazione stesso. Nell'esempio
```csharp
class A
{
    string s;

    void F(ref string a, ref string b) {
        s = "One";
        a = "Two";
        b = "Three";
    }

    void G() {
        F(ref s, ref s);
    }
}
```
la chiamata di `F` nelle `G` passa un riferimento al `s` per entrambi `a` e `b`. Pertanto, per chiamata, i nomi `s`, `a`, e `b` fanno tutte riferimento alla stessa posizione di archiviazione e le assegnazioni di tre tutti modificano il campo di istanza `s`.

#### <a name="output-parameters"></a>Parametri di output

Un parametro dichiarato con un `out` modificatore è un parametro di output. Simile a un parametro di riferimento, un parametro di output non crea un nuovo percorso di archiviazione. Al contrario, un parametro di output rappresenta la stessa posizione di archiviazione della variabile specificata come argomento nella chiamata al metodo.

Quando un parametro formale è un parametro di output, l'argomento corrispondente in una chiamata al metodo deve essere costituito la parola chiave `out` seguita da un *variable_reference* ([regole Precise per la determinazione assegnazione certa](variables.md#precise-rules-for-determining-definite-assignment)) dello stesso tipo del parametro formale. Una variabile necessario non essere assegnata può essere passata come parametro di output, ma in seguito una chiamata in cui una variabile è stata passata come parametro di output, la variabile viene considerata assegnata.

All'interno di un metodo, esattamente come una variabile locale variabile, un parametro di output è inizialmente considerato non assegnati e deve essere assegnata prima che venga utilizzato il relativo valore.

Ogni parametro di output di un metodo deve essere assegnata prima che il metodo restituisce.

Un metodo dichiarato come un metodo parziale ([metodi parziali](classes.md#partial-methods)) o un iteratore ([iteratori](classes.md#iterators)) non può avere parametri di output.

I parametri di output vengono in genere usati nei metodi che restituiscono più valori restituiti. Ad esempio:
```csharp
using System;

class Test
{
    static void SplitPath(string path, out string dir, out string name) {
        int i = path.Length;
        while (i > 0) {
            char ch = path[i - 1];
            if (ch == '\\' || ch == '/' || ch == ':') break;
            i--;
        }
        dir = path.Substring(0, i);
        name = path.Substring(i);
    }

    static void Main() {
        string dir, name;
        SplitPath("c:\\Windows\\System\\hello.txt", out dir, out name);
        Console.WriteLine(dir);
        Console.WriteLine(name);
    }
}
```

L'esempio produce l'output:
```
c:\Windows\System\
hello.txt
```

Si noti che il `dir` e `name` le variabili possono essere non assegnate prima che vengano passati a `SplitPath`, e che sono considerati assegnate dopo la chiamata.

#### <a name="parameter-arrays"></a>Matrici di parametri

Un parametro dichiarato con un `params` modificatore è una matrice di parametri. Se un elenco di parametri formali include una matrice di parametri, deve essere l'ultimo parametro nell'elenco e deve essere di un tipo di matrice unidimensionale. Ad esempio, i tipi `string[]` e `string[][]` può essere usato come tipo di una matrice di parametri, ma il tipo `string[,]` non può. Non è possibile combinare le `params` modificatore con i modificatori `ref` e `out`.

Una matrice di parametri gli argomenti di essere specificato in uno dei due modi in una chiamata al metodo:

*  L'argomento specificato per una matrice di parametri può essere una singola espressione che è convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) per il tipo di matrice di parametri. In questo caso, la matrice di parametri funziona esattamente come un parametro di valore.
*  In alternativa, la chiamata è possibile specificare zero o più argomenti per la matrice di parametri, dove ogni argomento è un'espressione che è convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) al tipo di elemento della matrice di parametri. In questo caso, la chiamata crea un'istanza del tipo di parametro matrice con una lunghezza corrispondente al numero di argomenti, inizializza gli elementi dell'istanza di matrice con i valori dell'argomento specificato e Usa l'istanza di matrice appena creata come l'effettivo argomento.

Fatta eccezione per consentire un numero variabile di argomenti in una chiamata, una matrice di parametri equivale esattamente a un parametro di valore ([parametri del valore](classes.md#value-parameters)) dello stesso tipo.

L'esempio
```csharp
using System;

class Test
{
    static void F(params int[] args) {
        Console.Write("Array contains {0} elements:", args.Length);
        foreach (int i in args) 
            Console.Write(" {0}", i);
        Console.WriteLine();
    }

    static void Main() {
        int[] arr = {1, 2, 3};
        F(arr);
        F(10, 20, 30, 40);
        F();
    }
}
```
produce l'output
```
Array contains 3 elements: 1 2 3
Array contains 4 elements: 10 20 30 40
Array contains 0 elements:
```

La prima chiamata della `F` passa semplicemente la matrice `a` come parametro di valore. La seconda chiamata di `F` crea automaticamente un elemento di quattro `int[]` con i valori di elemento specificato e passa tale istanza come parametro di valore della matrice. Analogamente, la chiamata del terzo `F` crea un elemento di zero `int[]` e passa quell'istanza come un parametro di valore. Le chiamate al secondo e terzi sono esattamente equivalenti a:
```csharp
F(new int[] {10, 20, 30, 40});
F(new int[] {});
```

Quando si esegue la risoluzione dell'overload, un metodo con una matrice di parametri potrebbe essere applicabile in forma normale o in forma espansa ([membro di funzione applicabile](expressions.md#applicable-function-member)). La forma espansa di un metodo è disponibile solo se la forma standard per il metodo non è applicabile e solo se un metodo con la stessa firma in forma espansa applicabile non è già dichiarato nello stesso tipo.

L'esempio
```csharp
using System;

class Test
{
    static void F(params object[] a) {
        Console.WriteLine("F(object[])");
    }

    static void F() {
        Console.WriteLine("F()");
    }

    static void F(object a0, object a1) {
        Console.WriteLine("F(object,object)");
    }

    static void Main() {
        F();
        F(1);
        F(1, 2);
        F(1, 2, 3);
        F(1, 2, 3, 4);
    }
}
```
produce l'output
```
F();
F(object[]);
F(object,object);
F(object[]);
F(object[]);
```

Nell'esempio, due dei formati possibili espansi del metodo con una matrice di parametri sono già incluse nella classe come i metodi regolari. Queste forme espanse pertanto non vengono considerati quando si esegue la risoluzione dell'overload e le chiamate al primo e il terzo metodo selezionare quindi i metodi regolari. Quando una classe dichiara un metodo con una matrice di parametri, non è insolito per includere anche alcune forme espanse come i metodi regolari. In questo modo è possibile evitare l'allocazione di una matrice viene richiamato l'istanza che si verifica quando una forma espansa di un metodo con una matrice di parametri.

Quando il tipo di una matrice di parametri corrisponde `object[]`, si verifica una potenziale ambiguità tra la forma del metodo normale e la forma espansa per un singolo `object` parametro. Il motivo per l'ambiguità è che un' `object[]` è a sua volta convertibile in modo implicito nel tipo `object`. L'ambiguità non determina alcun problema, tuttavia, poiché può essere risolto inserendo un cast, se necessario.

L'esempio
```csharp
using System;

class Test
{
    static void F(params object[] args) {
        foreach (object o in args) {
            Console.Write(o.GetType().FullName);
            Console.Write(" ");
        }
        Console.WriteLine();
    }

    static void Main() {
        object[] a = {1, "Hello", 123.456};
        object o = a;
        F(a);
        F((object)a);
        F(o);
        F((object[])o);
    }
}
```
produce l'output
```
System.Int32 System.String System.Double
System.Object[]
System.Object[]
System.Int32 System.String System.Double
```

In chiamate e il cognome dei `F`, la forma standard `F` è applicabile poiché esiste una conversione implicita dal tipo di argomento al tipo di parametro (entrambe sono di tipo `object[]`). Di conseguenza, la risoluzione dell'overload seleziona la forma standard `F`, e l'argomento viene passato come parametro di valore regolare. Nelle chiamate al secondo e terzi, la forma standard `F` non applicabile perché non esiste alcuna conversione implicita dal tipo di argomento al tipo di parametro (tipo `object` non può essere convertito in modo implicito nel tipo `object[]`). Tuttavia, la forma espansa di `F` è applicabile, quindi è selezionata per la risoluzione dell'overload. Di conseguenza, un solo elemento `object[]` viene creato per la chiamata, il singolo elemento della matrice viene inizializzato con il valore dell'argomento specificato (che a sua volta è un riferimento a un `object[]`).

### <a name="static-and-instance-methods"></a>Metodi statici e di istanza

Quando una dichiarazione di metodo include un `static` modificatore, questo metodo viene definito come un metodo statico. Se non si specifica `static` modificatore è presente, il metodo viene definito un metodo di istanza.

Un metodo statico non funziona in un'istanza specifica ed è un errore in fase di compilazione per fare riferimento a `this` in un metodo statico.

Un metodo di istanza agisce su una determinata istanza di una classe e tale istanza è accessibile come `this` ([questo accesso](expressions.md#this-access)).

Quando si fa riferimento a un metodo un *member_access* ([l'accesso ai membri](expressions.md#member-access)) nel formato `E.M`se `M` è un metodo statico, `E` deve indicare un tipo contenente `M`e se `M` è un metodo di istanza `E` devono indicare un'istanza di un tipo contenente `M`.

Le differenze tra statica e i membri di istanza sono descritti dettagliatamente nella [i membri statici e di istanza](classes.md#static-and-instance-members).

### <a name="virtual-methods"></a>Metodi virtuali

Quando una dichiarazione di metodo di istanza include un `virtual` modificatore, questo metodo viene definito un metodo virtuale. Se non si specifica `virtual` modificatore è presente, il metodo viene definito un metodo non virtuale.

L'implementazione di un metodo non virtuale è invariante: l'implementazione è la stessa se il metodo viene richiamato in un'istanza della classe in cui viene dichiarato o un'istanza di una classe derivata. Al contrario, l'implementazione di un metodo virtuale può essere sostituito da classi derivate. Il processo di sostituzione dell'implementazione di un metodo virtuale ereditato è noto come ***eseguendo l'override*** tale metodo ([metodi di Override](classes.md#override-methods)).

In una chiamata al metodo virtuale, il ***tipo di runtime*** dell'istanza per il quale si verifica la chiamata sul posto determina l'implementazione effettiva del metodo da richiamare. In una chiamata al metodo non virtuale, il ***in fase di compilazione*** dell'istanza è il fattore determinante. Più precisamente, quando un metodo denominato `N` viene richiamato con un elenco di argomenti `A` in un'istanza con un tipo in fase di compilazione `C` e un tipo in fase di esecuzione `R` (dove `R` sia `C` o una classe derivata da `C`), la chiamata viene elaborata come indicato di seguito:

*  In primo luogo, la risoluzione dell'overload viene applicata a `C`, `N`, e `A`per selezionare un metodo specifico `M` dal set di metodi dichiarati ed ereditato da `C`. Come descritto nella [chiamate al metodo](expressions.md#method-invocations).
*  Quindi, se `M` è un metodo non virtuale, `M` viene richiamato.
*  In caso contrario, `M` è un metodo virtuale e l'implementazione più derivata di `M` rispetto a `R` viene richiamato.

Per ogni metodo virtuale dichiarato o ereditato da una classe, esiste un ***implementazione più derivata*** del metodo rispetto a quella classe. L'implementazione di un metodo virtuale più derivato `M` rispetto a una classe `R` viene determinata come segue:

*  Se `R` contiene la presentazione `virtual` deklarace `M`, quindi questo è l'implementazione più derivata di `M`.
*  In caso contrario, se `R` contiene un `override` dei `M`, quindi questo è l'implementazione più derivata di `M`.
*  In caso contrario, più di implementazione della classe derivata `M` rispetto al `R` è quello utilizzato per l'implementazione più derivata del `M` rispetto alla classe base diretta di `R`.

Nell'esempio seguente vengono illustrate le differenze tra i metodi virtuali e non virtuale:
```csharp
using System;

class A
{
    public void F() { Console.WriteLine("A.F"); }

    public virtual void G() { Console.WriteLine("A.G"); }
}

class B: A
{
    new public void F() { Console.WriteLine("B.F"); }

    public override void G() { Console.WriteLine("B.G"); }
}

class Test
{
    static void Main() {
        B b = new B();
        A a = b;
        a.F();
        b.F();
        a.G();
        b.G();
    }
}
```

Nell'esempio riportato `A` introduce un metodo non virtuale `F` e un metodo virtuale `G`. La classe `B` introduce un nuovo metodo non virtuale `F`, quindi nascondendo ereditato `F`ed esegue l'override del metodo ereditato `G`. L'esempio produce l'output:
```
A.F
B.F
B.G
B.G
```

Si noti che l'istruzione `a.G()` richiama `B.G`, non `A.G`. Infatti, il runtime di tipo dell'istanza (che è `B`), non il tipo di fase di compilazione dell'istanza (ovvero `A`), determina l'implementazione effettiva del metodo da richiamare.

Poiché i metodi sono consentiti per nascondere i metodi ereditati, è possibile che una classe contiene diversi metodi virtuali con la stessa firma. Ciò non costituire un problema di ambiguità, poiché tutto tranne il metodo più derivato vengono nascosti. Nell'esempio
```csharp
using System;

class A
{
    public virtual void F() { Console.WriteLine("A.F"); }
}

class B: A
{
    public override void F() { Console.WriteLine("B.F"); }
}

class C: B
{
    new public virtual void F() { Console.WriteLine("C.F"); }
}

class D: C
{
    public override void F() { Console.WriteLine("D.F"); }
}

class Test
{
    static void Main() {
        D d = new D();
        A a = d;
        B b = d;
        C c = d;
        a.F();
        b.F();
        c.F();
        d.F();
    }
}
```
il `C` e `D` classi contengono due metodi virtuali con la stessa firma: dovuti a quella `A` e quello introdotti da `C`. Il metodo dovuto `C` nasconde il metodo ereditato da `A`. Di conseguenza, la dichiarazione di override nel `D` esegue l'override del metodo introdotto da `C`, e non è possibile che `D` per eseguire l'override del metodo introdotto da `A`. L'esempio produce l'output:
```
B.F
B.F
D.F
D.F
```

Si noti che è possibile richiamare il metodo virtuale nascosto tramite l'accesso a un'istanza di `D` attraverso un minore derivato tipo in cui il metodo non sia nascosto.

### <a name="override-methods"></a>Eseguire l'override di metodi

Quando una dichiarazione di metodo di istanza include un' `override` modificatore, il metodo viene definito un ***eseguire l'override di metodo***. Un metodo di override esegue l'override di un metodo virtuale ereditato con la stessa firma. Mentre una dichiarazione di metodo virtuale introduce un nuovo metodo, una dichiarazione di metodo di override specializza un metodo virtuale ereditato esistente specificando una nuova implementazione del metodo.

Il metodo sottoposto a override da un' `override` dichiarazione è noto come il ***sottoposto a override di metodo di base***. Per un metodo di override `M` dichiarata in una classe `C`, il metodo di base sottoposto a override è determinato esaminando ogni tipo di classe di base di `C`, che inizia con il tipo di classe di base diretta di `C` e continuando con ogni successivo tipo di classe di base diretta, fino a quando non in un tipo di classe base specificata almeno uno è un metodo accessibile che trova ha la stessa firma `M` dopo la sostituzione degli argomenti di tipo. Ai fini di individuazione del metodo di base sottoposto a override, un metodo viene considerato accessibile se è `public`, se si tratta `protected`, se si tratta `protected internal`, o se è `internal` e dichiarata nello stesso programma come `C`.

A meno che non tutti gli elementi seguenti siano vere per una dichiarazione di override, si verifica un errore in fase di compilazione:

*  Un metodo di base sottoposto a override può essere archiviato come descritto in precedenza.
*  È esattamente uno di questi metodi base sottoposto a override. Questa restrizione ha effetto solo se il tipo di classe di base è un tipo costruito in cui la sostituzione degli argomenti di tipo rende la firma dei due metodi lo stesso.
*  Il metodo di base sottoposto a override è virtuale, astratto o eseguire l'override di metodo. In altre parole, il metodo di base sottoposto a override non può essere statico o non virtuale.
*  Il metodo di base sottoposto a override non è un metodo sealed.
*  Il metodo di override e il metodo di base sottoposto a override hanno lo stesso tipo restituito.
*  La dichiarazione di override e il metodo di base sottoposto a override hanno la stessa accessibilità dichiarate. In altre parole, una dichiarazione di override non è possibile modificare l'accessibilità del metodo virtuale. Tuttavia, se il metodo di base sottoposto a override protected internal e viene dichiarata in un assembly diverso rispetto a dichiarata l'assembly contenente il metodo di override quindi il metodo di override accessibilità deve essere protetto.
*  La dichiarazione di override non specifica parametri di tipo-clausole di vincoli. In alternativa i vincoli vengono ereditati dal metodo base sottoposto a override. Si noti che i vincoli che sono parametri di tipo nel metodo sottoposto a override possono essere sostituiti dagli argomenti di tipo nel vincolo ereditato. Questo può causare i vincoli che non sono validi quando specificato in modo esplicito, ad esempio i tipi di valore o tipi sealed.

Nell'esempio seguente viene illustrato come le regole di override funzionano per le classi generiche:
```csharp
abstract class C<T>
{
    public virtual T F() {...}
    public virtual C<T> G() {...}
    public virtual void H(C<T> x) {...}
}

class D: C<string>
{
    public override string F() {...}            // Ok
    public override C<string> G() {...}         // Ok
    public override void H(C<T> x) {...}        // Error, should be C<string>
}

class E<T,U>: C<U>
{
    public override U F() {...}                 // Ok
    public override C<U> G() {...}              // Ok
    public override void H(C<T> x) {...}        // Error, should be C<U>
}
```

Una dichiarazione di sostituzione può accedere il metodo di base sottoposto a override utilizzando un *base_access* ([Base accesso](expressions.md#base-access)). Nell'esempio
```csharp
class A
{
    int x;

    public virtual void PrintFields() {
        Console.WriteLine("x = {0}", x);
    }
}

class B: A
{
    int y;

    public override void PrintFields() {
        base.PrintFields();
        Console.WriteLine("y = {0}", y);
    }
}
```
il `base.PrintFields()` chiamata `B` richiama le `PrintFields` metodo dichiarato come `A`. Oggetto *base_access* disabilita il meccanismo di chiamata virtuale e gestisce semplicemente il metodo di base come un metodo non virtuale. Avevano la chiamata in `B` stato scritti `((A)this).PrintFields()`, avviene in modo ricorsivo richiamare il `PrintFields` metodo è dichiarato `B`, non con quello dichiarato nel `A`, poiché `PrintFields` è virtuale e il tipo di runtime di `((A)this)` è `B`.

Solo includendo un `override` can modificatore un metodo esegue l'override di un altro metodo. In tutti gli altri casi, un metodo con la stessa firma di un metodo ereditato nasconde semplicemente il metodo ereditato. Nell'esempio
```csharp
class A
{
    public virtual void F() {}
}

class B: A
{
    public virtual void F() {}        // Warning, hiding inherited F()
}
```
il `F` metodo nella `B` non include un' `override` modificatore e pertanto non esegue l'override di `F` metodo nella `A`. Piuttosto, il `F` metodo nella `B` nasconde il metodo nella `A`, e viene segnalato un avviso perché la dichiarazione non include un `new` modificatore.

Nell'esempio
```csharp
class A
{
    public virtual void F() {}
}

class B: A
{
    new private void F() {}        // Hides A.F within body of B
}

class C: B
{
    public override void F() {}    // Ok, overrides A.F
}
```
il `F` nel metodo `B` nasconde virtuale `F` metodo ereditato dal `A`. Poiché il nuovo `F` nelle `B` con accesso privato, del relativo ambito include solo il corpo della classe di `B` e non si estende a `C`. Di conseguenza, la dichiarazione di `F` nel `C` è la possibilità di eseguire l'override di `F` ereditato da `A`.

### <a name="sealed-methods"></a>Metodi sealed

Quando una dichiarazione di metodo di istanza include un `sealed` modificatore, questo metodo viene definito da una ***sealed metodo***. Se una dichiarazione di metodo di istanza include la `sealed` modificatore, deve includere anche il `override` modificatore. Usare il `sealed` modificatore impedisce che una classe derivata ulteriormente l'override del metodo.

Nell'esempio
```csharp
using System;

class A
{
    public virtual void F() {
        Console.WriteLine("A.F");
    }

    public virtual void G() {
        Console.WriteLine("A.G");
    }
}

class B: A
{
    sealed override public void F() {
        Console.WriteLine("B.F");
    } 

    override public void G() {
        Console.WriteLine("B.G");
    } 
}

class C: B
{
    override public void G() {
        Console.WriteLine("C.G");
    } 
}
```
la classe `B` fornisce due metodi di override: un' `F` metodo che presenta le `sealed` modificatore e un `G` metodo che non sono presenti. `B`utilizzo del sealed `modifier` impedisce `C` sovrascrivano ulteriormente `F`.

### <a name="abstract-methods"></a>Metodi astratti

Quando una dichiarazione di metodo di istanza include un' `abstract` modificatore, questo metodo viene definito da un' ***metodo astratto***. Anche se un metodo astratto è implicitamente anche un metodo virtuale, non può includere il modificatore `virtual`.

Una dichiarazione di metodo astratto introduce un nuovo metodo virtuale ma non fornisce un'implementazione del metodo. Invece, le classi derivate non astratte sono necessarie per fornire la propria implementazione eseguendo l'override di tale metodo. Poiché un metodo astratto non fornisce alcuna implementazione effettiva, la *method_body* di un metodo astratto costituito semplicemente da un punto e virgola.

Dichiarazioni di metodi astratti sono consentite solo nelle classi astratte ([classi astratte](classes.md#abstract-classes)).

Nell'esempio
```csharp
public abstract class Shape
{
    public abstract void Paint(Graphics g, Rectangle r);
}

public class Ellipse: Shape
{
    public override void Paint(Graphics g, Rectangle r) {
        g.DrawEllipse(r);
    }
}

public class Box: Shape
{
    public override void Paint(Graphics g, Rectangle r) {
        g.DrawRect(r);
    }
}
```
il `Shape` classe definisce il concetto astratto di un oggetto forma geometrica può disegnare se stessa. Il `Paint` metodo è astratto perché non vi è alcuna implementazione predefiniti significativi. Il `Ellipse` e `Box` sono classi concrete `Shape` implementazioni. Poiché queste classi sono astratte, viene richiesto di eseguire l'override di `Paint` metodo e fornire un'implementazione effettiva.

Tratta di un errore in fase di compilazione per un *base_access* ([Base accesso](expressions.md#base-access)) per fare riferimento a un metodo astratto. Nell'esempio
```csharp
abstract class A
{
    public abstract void F();
}

class B: A
{
    public override void F() {
        base.F();                        // Error, base.F is abstract
    }
}
```
viene segnalato un errore in fase di compilazione per il `base.F()` chiamata perché fa riferimento a un metodo astratto.

Una dichiarazione di metodo astratto è consentita eseguire l'override di un metodo virtuale. Questo consente a una classe astratta forzare la nuova implementazione del metodo nelle classi derivate e rende l'implementazione originale del metodo non disponibile. Nell'esempio
```csharp
using System;

class A
{
    public virtual void F() {
        Console.WriteLine("A.F");
    }
}

abstract class B: A
{
    public abstract override void F();
}

class C: B
{
    public override void F() {
        Console.WriteLine("C.F");
    }
}
```
classe `A` dichiara un metodo virtuale, una classe `B` esegue l'override di questo metodo con un metodo astratto, classe `C` esegue l'override del metodo astratto per fornire la propria implementazione.

### <a name="external-methods"></a>Metodi esterni

Quando una dichiarazione di metodo include un' `extern` modificatore, questo metodo viene definito da un' ***metodo esterno***. Metodi esterni vengono implementati all'esterno, in genere tramite un linguaggio diverso da c#. Poiché una dichiarazione di metodo esterno non fornisce alcuna implementazione effettiva, la *method_body* di un metodo esterno costituita semplicemente da un punto e virgola. Un metodo esterno non può essere generico.

Il `extern` modificatore viene in genere usato in combinazione con un `DllImport` attributo ([l'interoperabilità con i componenti di Win32 e COM](attributes.md#interoperation-with-com-and-win32-components)), che consente metodi esterni deve essere implementata da DLL (Dynamic Link Library). L'ambiente di esecuzione potrebbe supportare altri meccanismi in base al quale possono essere fornite le implementazioni di metodi esterni.

Quando un metodo esterno include un' `DllImport` attributo, la dichiarazione del metodo deve anche includere un `static` modificatore. In questo esempio viene illustrato l'utilizzo dei `extern` modificatore e `DllImport` attributo:
```csharp
using System.Text;
using System.Security.Permissions;
using System.Runtime.InteropServices;

class Path
{
    [DllImport("kernel32", SetLastError=true)]
    static extern bool CreateDirectory(string name, SecurityAttribute sa);

    [DllImport("kernel32", SetLastError=true)]
    static extern bool RemoveDirectory(string name);

    [DllImport("kernel32", SetLastError=true)]
    static extern int GetCurrentDirectory(int bufSize, StringBuilder buf);

    [DllImport("kernel32", SetLastError=true)]
    static extern bool SetCurrentDirectory(string name);
}
```

### <a name="partial-methods-recap"></a>Metodi parziali (riepilogo)

Quando una dichiarazione di metodo include un `partial` modificatore, questo metodo viene definito da una ***metodo parziale***. I metodi parziali possono essere dichiarati solo come membri di tipi parziali ([i tipi parziali](classes.md#partial-types)) e sono soggetti a un numero di restrizioni. I metodi parziali sono descritte dettagliatamente nella [metodi parziali](classes.md#partial-methods).

### <a name="extension-methods"></a>Metodi di estensione

Se il primo parametro di un metodo include il `this` modificatore, questo metodo viene definito da un' ***metodo di estensione***. I metodi di estensione possono essere dichiarati solo in classi statiche non generica, non annidata. Il primo parametro di un metodo di estensione non può avere nessun modificatore diverso da `this`, e il tipo di parametro non può essere un tipo di puntatore.

Di seguito è riportato un esempio di una classe statica che dichiara due metodi di estensione:
```csharp
public static class Extensions
{
    public static int ToInt32(this string s) {
        return Int32.Parse(s);
    }

    public static T[] Slice<T>(this T[] source, int index, int count) {
        if (index < 0 || count < 0 || source.Length - index < count)
            throw new ArgumentException();
        T[] result = new T[count];
        Array.Copy(source, index, result, 0, count);
        return result;
    }
}
```

Un metodo di estensione è un normale metodo statico. Inoltre, in cui la classe statica nell'ambito, un metodo di estensione può essere richiamato usando la sintassi di chiamata al metodo di istanza ([chiamate al metodo di estensione](expressions.md#extension-method-invocations)), usando l'espressione del ricevitore come primo argomento.

Il programma seguente usa i metodi di estensione dichiarati in precedenza:
```csharp
static class Program
{
    static void Main() {
        string[] strings = { "1", "22", "333", "4444" };
        foreach (string s in strings.Slice(1, 2)) {
            Console.WriteLine(s.ToInt32());
        }
    }
}
```

Il `Slice` metodo è disponibile nel `string[]`e il `ToInt32` metodo è disponibile nel `string`, poiché essi sono stati dichiarati come metodi di estensione. Il significato del programma è quello utilizzato per le chiamate al metodo statico normale seguente, usando:
```csharp
static class Program
{
    static void Main() {
        string[] strings = { "1", "22", "333", "4444" };
        foreach (string s in Extensions.Slice(strings, 1, 2)) {
            Console.WriteLine(Extensions.ToInt32(s));
        }
    }
}
```

### <a name="method-body"></a>Corpo del metodo

Il *method_body* di un metodo dichiarazione costituito da un corpo del blocco, un corpo dell'espressione o un punto e virgola.

Il ***come tipo di risultato*** di un metodo viene `void` se il tipo restituito `void`, o se il metodo è asincrono e il tipo restituito è `System.Threading.Tasks.Task`. In caso contrario, il tipo di risultato di un metodo non asincrone è il tipo restituito e il tipo di risultato di un metodo asincrono con tipo restituito `System.Threading.Tasks.Task<T>` è `T`.

Quando un metodo ha un `void` risultato di tipo e un corpo del blocco, `return` istruzioni ([l'istruzione return](statements.md#the-return-statement)) nel blocco non sono consentite per specificare un'espressione. Se viene completata l'esecuzione del blocco di un metodo void (vale a dire, flussi di controllo oltre la fine del corpo del metodo), che restituisce semplicemente metodo al relativo chiamante corrente.
    
Quando un metodo ha un `void` risultato e un corpo di espressione, l'espressione `E` deve essere un *statement_expression*, e il corpo è perfettamente equivalente al corpo di un blocco del form `{ E; }`.
    
Quando un metodo ha un tipo di risultato non void e un blocco del corpo, ognuna `return` istruzione nel blocco deve specificare un'espressione che è implicitamente convertibile nel tipo di risultato. L'endpoint di un corpo del blocco di un metodo che restituiscono un valore non deve essere raggiungibile. In altre parole, un metodo che restituiscono un valore con un corpo del blocco, controllo non è consentito propagare oltre la fine del corpo del metodo.
    
Quando un metodo ha un tipo di risultato non void e un corpo di espressione, l'espressione deve essere convertibile in modo implicito al tipo di risultato e il corpo è perfettamente equivalente al corpo di un blocco del form `{ return E; }`.
    
Nell'esempio
```csharp
class A
{
    public int F() {}            // Error, return value required

    public int G() {
        return 1;
    }

    public int H(bool b) {
        if (b) {
            return 1;
        }
        else {
            return 0;
        }
    }

    public int I(bool b) => b ? 1 : 0;
}
```
il valore di restituzione `F` metodo comporta un errore in fase di compilazione perché il controllo può superare la fine del corpo del metodo. Il `G` e `H` metodi siano corretti perché tutti i possibili percorsi di esecuzione terminano con un'istruzione return che specifica un valore restituito. Il `I` metodo sia corretto, perché il relativo corpo è equivalente a un blocco di istruzioni con solo una singola istruzione return in esso.

### <a name="method-overloading"></a>Overload di un metodo

Le regole di risoluzione dell'overload di metodo sono descritti [inferenza del tipo](expressions.md#type-inference).

## <a name="properties"></a>Proprietà

Oggetto ***proprietà*** è un membro che fornisce l'accesso a una caratteristica di un oggetto o una classe. Esempi di proprietà includono la lunghezza di una stringa, le dimensioni di un tipo di carattere, la didascalia di una finestra, il nome di un cliente e così via. Le proprietà sono un'estensione naturale dei campi, entrambi sono membri denominati con tipi associati e la sintassi per l'accesso a proprietà e campi è la stessa. A differenza dei campi, tuttavia, le proprietà non denotano posizioni di memoria, ma hanno ***funzioni di accesso*** che specificano le istruzioni da eseguire nel momento in cui ne vengono letti o scritti i valori. Proprietà quindi di fornire un meccanismo per l'associazione di azioni con la lettura e scrittura di attributi di un oggetto. Inoltre, permettono di tali attributi deve essere calcolata.

Le proprietà vengono dichiarate utilizzando *property_declaration*s:

```antlr
property_declaration
    : attributes? property_modifier* type member_name property_body
    ;

property_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | property_modifier_unsafe
    ;

property_body
    : '{' accessor_declarations '}' property_initializer?
    | '=>' expression ';'
    ;

property_initializer
    : '=' variable_initializer ';'
    ;
```

Oggetto *property_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso ](classes.md#access-modifiers)), il `new` ([il nuovo modificatore](classes.md#the-new-modifier)), `static` ([metodi statici e di istanza](classes.md#static-and-instance-methods)), `virtual` ([metodi virtuali](classes.md#virtual-methods)), `override` ([Metodi di override](classes.md#override-methods)), `sealed` ([Sealed i metodi](classes.md#sealed-methods)), `abstract` ([metodi astratti](classes.md#abstract-methods)), e `extern` ([Metodi esterni](classes.md#external-methods)) modificatori.

Le dichiarazioni di proprietà sono soggetti alle stesse regole come le dichiarazioni di metodo ([metodi](classes.md#methods)) in relazione le combinazioni valide di modificatori.

Il *tipo* di una proprietà dichiarazione specifica il tipo della proprietà introdotte dalla dichiarazione e il *member_name* specifica il nome della proprietà. A meno che la proprietà è un'implementazione esplicita dell'interfaccia membro, il *member_name* è semplicemente un' *identificatore*. Implementazione di un membro di interfaccia esplicita ([tali implementazioni](interfaces.md#explicit-interface-member-implementations)), il *member_name* è costituito da un *interface_type* seguita da un " `.`"e un' *identificatore*.

Il *tipo* di una proprietà devono essere accessibili almeno quanto la stessa proprietà ([vincoli accessibilità](basic-concepts.md#accessibility-constraints)).

Oggetto *property_body* possono essere costituiti da un ***corpo della funzione di accesso*** o un ***corpo dell'espressione***. In un corpo di funzione di accesso *accessor_declarations*, che deve essere racchiuso tra parentesi "`{`"e"`}`" token, dichiarare le funzioni di accesso ([funzioni di accesso](classes.md#accessors)) della proprietà. Le funzioni di accesso specificare le istruzioni eseguibili associate alla lettura e scrittura della proprietà.

Un corpo di espressione costituita `=>` seguita da un' *espressione* `E` e un punto e virgola è perfettamente equivalente al corpo dell'istruzione `{ get { return E; } }`e può pertanto essere usato solo per specificare solo Get proprietà in cui il risultato del metodo Get è dato da un'unica espressione.

Oggetto *property_initializer* può essere specificato solo per una proprietà implementata automaticamente ([proprietà implementate automaticamente](classes.md#automatically-implemented-properties)) e fa sì che l'inizializzazione del campo sottostante di tali le proprietà con il valore specificato per il *espressione*.

Anche se la sintassi per l'accesso a una proprietà è uguale a quello per un campo, una proprietà non è classificata come una variabile. Non è pertanto possibile passare una proprietà come un `ref` o `out` argomento.

Quando una dichiarazione di proprietà include un' `extern` modificatore, la proprietà viene definita un ***proprietà esterna***. Poiché una dichiarazione di proprietà esterna non fornisce alcuna implementazione effettiva, ognuno dei relativi *accessor_declarations* è costituito da un punto e virgola.

### <a name="static-and-instance-properties"></a>Proprietà statiche e istanza

Quando una dichiarazione di proprietà contiene un `static` modificatore, la proprietà viene definita un ***proprietà statica***. Se non si specifica `static` modificatore è presente, la proprietà viene definita un ***proprietà di istanza***.

Una proprietà statica non è associata a un'istanza specifica e tratta di un errore in fase di compilazione per fare riferimento a `this` nelle funzioni di accesso di una proprietà statica.

Una proprietà dell'istanza è associata a una determinata istanza di una classe, e tale istanza è accessibile come `this` ([questo accesso](expressions.md#this-access)) nelle funzioni di accesso di tale proprietà.

Quando si fa riferimento una proprietà un *member_access* ([l'accesso ai membri](expressions.md#member-access)) nel formato `E.M`se `M` è una proprietà statica, `E` deve indicare un tipo contenente `M`e se `M` è una proprietà dell'istanza, È necessario che un'istanza di un tipo contenente `M`.

Le differenze tra statica e i membri di istanza sono descritti dettagliatamente nella [i membri statici e di istanza](classes.md#static-and-instance-members).

### <a name="accessors"></a>Funzioni di accesso

Il *accessor_declarations* di una proprietà specificano le istruzioni eseguibili associate alla lettura e scrittura della proprietà.

```antlr
accessor_declarations
    : get_accessor_declaration set_accessor_declaration?
    | set_accessor_declaration get_accessor_declaration?
    ;

get_accessor_declaration
    : attributes? accessor_modifier? 'get' accessor_body
    ;

set_accessor_declaration
    : attributes? accessor_modifier? 'set' accessor_body
    ;

accessor_modifier
    : 'protected'
    | 'internal'
    | 'private'
    | 'protected' 'internal'
    | 'internal' 'protected'
    ;

accessor_body
    : block
    | ';'
    ;
```

Le dichiarazioni di funzione di accesso è costituito un *get_accessor_declaration*, un *set_accessor_declaration*, o entrambi. Ogni dichiarazione della funzione di accesso è costituita da token `get` oppure `set` seguita da un oggetto facoltativo *accessor_modifier* e un *accessor_body*.

L'uso di *accessor_modifier*s è soggetto alle restrizioni seguenti:

*  Un' *accessor_modifier* non può essere utilizzato in un'interfaccia o in un'implementazione di membro di interfaccia esplicita.
*  Per una proprietà o indicizzatore che non ha alcun `override` modificatore, un' *accessor_modifier* è consentito solo se la proprietà o l'indicizzatore ha entrambe un `get` e `set` della funzione di accesso e quindi è consentita solo per uno di questi funzioni di accesso.
*  Per una proprietà o indicizzatore che include un' `override` modificatore, deve corrispondere a una funzione di accesso di *accessor_modifier*, se presente, della funzione di accesso da sottoporre a override.
*  Il *accessor_modifier* deve dichiarare un'accessibilità strettamente più restrittiva rispetto l'accessibilità dichiarata di proprietà o nell'indicizzatore stesso. Per essere precisi:
   * Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata del `public`, il *accessor_modifier* può essere `protected internal`, `internal`, `protected`, o `private`.
   * Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata del `protected internal`, il *accessor_modifier* può essere `internal`, `protected`, o `private`.
   * Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata del `internal` oppure `protected`, il *accessor_modifier* deve essere `private`.
   * Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata del `private`, nessun *accessor_modifier* può essere utilizzato.

Per la `abstract` e `extern` delle proprietà, il *accessor_body* per ogni funzione di accesso specificato è semplicemente un punto e virgola. Una proprietà non astratto e non extern può avere ognuna *accessor_body* sia un punto e virgola, nel qual caso è un ***proprietà implementata automaticamente*** ([proprietà implementate automaticamente ](classes.md#automatically-implemented-properties)). Una proprietà implementata automaticamente deve avere almeno una funzione di accesso get. Per le funzioni di accesso di qualsiasi altra proprietà astratte e non extern, la *accessor_body* è un *blocco* che consente di specificare le istruzioni da eseguire quando viene richiamata la funzione di accesso corrispondente.

Oggetto `get` della funzione di accesso corrispondente a un metodo senza parametri con un valore restituito del tipo di proprietà. Fatta eccezione per quanto la destinazione di un'assegnazione, quando viene fatto riferimento a una proprietà in un'espressione, il `get` funzione di accesso della proprietà viene richiamata per calcolare il valore della proprietà ([i valori delle espressioni](expressions.md#values-of-expressions)). Il corpo di una `get` funzione di accesso devono essere conformi alle regole per la restituzione di valore metodi descritti nella [corpo del metodo](classes.md#method-body). In particolare, tutti i `return` istruzioni nel corpo di un `get` della funzione di accesso è necessario specificare un'espressione che è implicitamente convertibile nel tipo di proprietà. Inoltre, l'endpoint di un `get` della funzione di accesso non deve essere raggiungibile.

Oggetto `set` funzione di accesso corrisponde a un metodo con un singolo parametro di valore del tipo di proprietà e un `void` tipo restituito. Il parametro implicito di un `set` funzione di accesso è sempre denominato `value`. Quando una proprietà viene fatto riferimento come destinazione di un'assegnazione ([gli operatori di assegnazione](expressions.md#assignment-operators)), o come operando di `++` oppure `--` ([forma suffissa di incremento e decremento (operatori)](expressions.md#postfix-increment-and-decrement-operators), [ Incremento prefisso e decremento (operatori)](expressions.md#prefix-increment-and-decrement-operators)), il `set` funzione di accesso viene richiamato con un argomento (il cui valore è quello di destra dell'assegnazione o l'operando del `++` o `--` operator) che fornisce il nuovo valore ([assegnamento semplice](expressions.md#simple-assignment)). Il corpo di una `set` funzione di accesso devono essere conformi alle regole per `void` metodi descritti in [corpo del metodo](classes.md#method-body). In particolare, `return` istruzioni di `set` corpo della funzione di accesso non è consentito specificare un'espressione. Poiché un `set` funzione di accesso in modo implicito include un parametro denominato `value`, tratta di un errore in fase di compilazione per una dichiarazione di costante o variabile locale in un `set` della funzione di accesso quel nome.

Basato sulla presenza o assenza del `get` e `set` funzioni di accesso, una proprietà viene classificato come indicato di seguito:

*  Una proprietà che include sia un `get` funzione di accesso e un `set` funzione di accesso viene definito un ***lettura / scrittura*** proprietà.
*  Una proprietà che è presente solo una `get` funzione di accesso viene definito un ***sola lettura*** proprietà. È un errore in fase di compilazione per una proprietà di sola lettura sia la destinazione di un'assegnazione.
*  Una proprietà che è presente solo una `set` funzione di accesso viene definito un ***sola scrittura*** proprietà. Ad eccezione del fatto come destinazione di un'assegnazione, è un errore in fase di compilazione per fare riferimento a una proprietà di sola scrittura in un'espressione.

Nell'esempio
```csharp
public class Button: Control
{
    private string caption;

    public string Caption {
        get {
            return caption;
        }
        set {
            if (caption != value) {
                caption = value;
                Repaint();
            }
        }
    }

    public override void Paint(Graphics g, Rectangle r) {
        // Painting code goes here
    }
}
```
il `Button` controllo dichiara un pubblico `Caption` proprietà. Il `get` funzione di accesso del `Caption` proprietà restituisce la stringa archiviata in privato `caption` campo. Il `set` controlla se il nuovo valore è diverso da quello corrente e, in caso affermativo, archivia il nuovo valore di funzione di accesso e ridisegna il controllo. Proprietà spesso seguono il modello illustrato in precedenza: il `get` funzione di accesso restituisce un valore archiviato in un campo privato e il `set` della funzione di accesso viene modificato quel campo privato e quindi esegue le azioni aggiuntive necessarie per aggiornare completamente lo stato dell'oggetto.

Dato il `Button` classe precedente, di seguito è riportato un esempio d'uso del `Caption` proprietà:
```csharp
Button okButton = new Button();
okButton.Caption = "OK";            // Invokes set accessor
string s = okButton.Caption;        // Invokes get accessor
```

In questo caso, il `set` funzione di accesso viene richiamato tramite l'assegnazione di un valore alla proprietà e il `get` della funzione di accesso viene richiamato da fare riferimento alla proprietà in un'espressione.

Il `get` e `set` funzioni di accesso di una proprietà non sono membri distinti e non è possibile dichiarare le funzioni di accesso di una proprietà separatamente. Di conseguenza, non possibile per le due funzioni di accesso di una proprietà di lettura / scrittura avere un'accessibilità diversa. L'esempio
```csharp
class A
{
    private string name;

    public string Name {                // Error, duplicate member name
        get { return name; }
    }

    public string Name {                // Error, duplicate member name
        set { name = value; }
    }
}
```
non dichiarare una singola proprietà di lettura / scrittura. Piuttosto, dichiara due proprietà con lo stesso nome, una sola lettura e una sola scrittura. Poiché i due membri dichiarati nella stessa classe non possono avere lo stesso nome, viene generato un errore in fase di compilazione.

Quando una classe derivata dichiara una proprietà con lo stesso nome di una proprietà ereditata, la classe derivata nasconde la proprietà ereditata rispetto alla lettura e scrittura. Nell'esempio
```csharp
class A
{
    public int P {
        set {...}
    }
}

class B: A
{
    new public int P {
        get {...}
    }
}
```
il `P` proprietà nel `B` nasconde le `P` proprietà in `A` rispetto alla lettura e scrittura. Pertanto, nelle istruzioni
```csharp
B b = new B();
b.P = 1;          // Error, B.P is read-only
((A)b).P = 1;     // Ok, reference to A.P
```
l'assegnazione al `b.P` provoca un errore in fase di compilazione essere segnalato, dopo di sola lettura `P` proprietà nel `B` nasconde la sola scrittura `P` proprietà `A`. Si noti, tuttavia, un cast può essere utilizzato per accedere di nascosto `P` proprietà.

A differenza dei campi pubblici, le proprietà forniscono una separazione tra lo stato interno di un oggetto e l'interfaccia pubblica. Si consideri l'esempio:
```csharp
class Label
{
    private int x, y;
    private string caption;

    public Label(int x, int y, string caption) {
        this.x = x;
        this.y = y;
        this.caption = caption;
    }

    public int X {
        get { return x; }
    }

    public int Y {
        get { return y; }
    }

    public Point Location {
        get { return new Point(x, y); }
    }

    public string Caption {
        get { return caption; }
    }
}
```

In questo caso, il `Label` classe utilizza due `int` campi `x` e `y`, per archiviare il percorso. Il percorso è pubblicamente esposto sia come un `X` e una `Y` proprietà e come una `Location` vlastnosti typu `Point`. Se, in una versione futura di `Label`, diventa più pratico archiviare il percorso come un `Point` internamente, la modifica può essere eseguita senza influire sull'interfaccia pubblica della classe:
```csharp
class Label
{
    private Point location;
    private string caption;

    public Label(int x, int y, string caption) {
        this.location = new Point(x, y);
        this.caption = caption;
    }

    public int X {
        get { return location.x; }
    }

    public int Y {
        get { return location.y; }
    }

    public Point Location {
        get { return location; }
    }

    public string Caption {
        get { return caption; }
    }
}
```

Aveva `x` e `y` invece stata `public readonly` campi, sarebbe stato impossibile da apportare questa modifica per il `Label` classe.

Esposizione di stato tramite le proprietà non è necessariamente meno efficiente dell'esposizione diretta dei campi. In particolare, quando una proprietà non è virtuale e contiene solo una piccola quantità di codice, l'ambiente di esecuzione potrà sostituire le chiamate a funzioni di accesso con il codice effettivo delle funzioni di accesso. Questo processo è noto come ***inlining***, e lo rende più efficiente l'accesso al campo alle proprietà, ma mantiene la maggiore flessibilità delle proprietà.

Dopo il richiamo di un `get` funzione di accesso è concettualmente equivalente alla lettura del valore di un campo, viene considerato non valido dello stile di programmazione `get` funzioni di accesso per avere effetti collaterali osservabili. Nell'esempio
```csharp
class Counter
{
    private int next;

    public int Next {
        get { return next++; }
    }
}
```
Il valore della `Next` proprietà dipende dal numero di volte in cui la proprietà in precedenza è stato eseguito l'accesso. Di conseguenza, l'accesso alla proprietà produce un effetto collaterale considerevole e la proprietà deve essere implementata come metodo invece.

La convenzione "Nessun effetto collaterale" per `get` funzioni di accesso non significa che `get` funzioni di accesso dovrebbe sempre essere scritto limita a restituire i valori archiviati nei campi. In effetti, `get` funzioni di accesso spesso calcolare il valore di una proprietà per l'accesso a più campi o metodi di richiamo. Tuttavia, progettata correttamente `get` della funzione di accesso non esegue alcuna azione che determinano modifiche dell'osservabile nello stato dell'oggetto.

Proprietà possono essere usate ritardi l'inizializzazione di una risorsa fino al momento in cui che si trova il primo riferimento. Ad esempio:
```csharp
using System.IO;

public class Console
{
    private static TextReader reader;
    private static TextWriter writer;
    private static TextWriter error;

    public static TextReader In {
        get {
            if (reader == null) {
                reader = new StreamReader(Console.OpenStandardInput());
            }
            return reader;
        }
    }

    public static TextWriter Out {
        get {
            if (writer == null) {
                writer = new StreamWriter(Console.OpenStandardOutput());
            }
            return writer;
        }
    }

    public static TextWriter Error {
        get {
            if (error == null) {
                error = new StreamWriter(Console.OpenStandardError());
            }
            return error;
        }
    }
}
```

Il `Console` classe contiene tre proprietà, `In`, `Out`, e `Error`, che rappresentano l'input standard, output e dispositivi con errori, rispettivamente. Tramite l'esposizione di questi membri come proprietà, il `Console` classe può ritardare l'inizializzazione fino a quando non vengono effettivamente usati. Ad esempio, al primo riferimento di `Out` proprietà, come in
```csharp
Console.Out.WriteLine("hello, world");
```
sottostante `TextWriter` per il dispositivo di output viene creato. Tuttavia, se l'applicazione non fa riferimento al `In` e `Error` proprietà, quindi nessun oggetto viene create per tali dispositivi.

### <a name="automatically-implemented-properties"></a>Proprietà implementate automaticamente

Una proprietà implementata automaticamente (o ***proprietà automatiche*** breve), è una proprietà non extern non astratta con corpi di funzione di accesso solo da punti e virgola. Proprietà automatiche devono avere una funzione di accesso get e, facoltativamente, possono avere una funzione di accesso set.

Quando una proprietà viene specificata come una proprietà implementata automaticamente, un campo nascosto sottostante è automaticamente disponibile per la proprietà e le funzioni di accesso vengono implementati per leggere e scrivere in tale campo sottostante. Se la proprietà automatica non dispone di alcuna funzione di accesso set, il campo sottostante viene considerato `readonly` ([campi di sola lettura](classes.md#readonly-fields)). Proprio come un `readonly` campo, una proprietà solo Get automatica anche assegnabili a nel corpo di un costruttore della classe contenitrice. Tale assegnazione assegna direttamente per il campo sottostante di sola lettura della proprietà.

Una proprietà automatica può facoltativamente possedere un *property_initializer*, che viene applicato direttamente al campo sottostante come una *variable_initializer* ([inizializzatori](classes.md#variable-initializers)) .

Nell'esempio seguente:
```csharp
public class Point {
    public int X { get; set; } = 0;
    public int Y { get; set; } = 0;
}
```
è equivalente alla dichiarazione seguente:
```csharp
public class Point {
    private int __x = 0;
    private int __y = 0;
    public int X { get { return __x; } set { __x = value; } }
    public int Y { get { return __y; } set { __y = value; } }
}
```

Nell'esempio seguente:
```csharp
public class ReadOnlyPoint
{
    public int X { get; }
    public int Y { get; }
    public ReadOnlyPoint(int x, int y) { X = x; Y = y; }
}
```
è equivalente alla dichiarazione seguente:
```csharp
public class ReadOnlyPoint
{
    private readonly int __x;
    private readonly int __y;
    public int X { get { return __x; } }
    public int Y { get { return __y; } }
    public ReadOnlyPoint(int x, int y) { __x = x; __y = y; }
}
```

Si noti che le assegnazioni di campo di sola lettura sono validi, perché si verificano all'interno del costruttore.


### <a name="accessibility"></a>Accessibilità

Se una funzione di accesso è un *accessor_modifier*, il dominio di accessibilità ([domini accessibilità](basic-concepts.md#accessibility-domains)) della funzione di accesso viene determinato utilizzando l'accessibilità dichiarata del *accessor_modifier* . Se una funzione di accesso non dispone di un *accessor_modifier*, il dominio di accessibilità della funzione di accesso è determinato dall'accessibilità dichiarata della proprietà o indicizzatore.

La presenza di un' *accessor_modifier* non influisce sulla ricerca di membri ([operatori](expressions.md#operators)) o la risoluzione dell'overload ([risoluzione dell'Overload](expressions.md#overload-resolution)). I modificatori nella proprietà o indicizzatore sempre determinano quali proprietà o l'indicizzatore è associato, indipendentemente dal contesto dell'accesso.

Dopo aver selezionato una proprietà specifica o un indicizzatore, i domini di accessibilità delle funzioni di accesso specifiche coinvolte vengono usati per determinare se tale uso è valido:

*  Se l'utilizzo è sotto forma di valore ([valori di espressioni](expressions.md#values-of-expressions)), il `get` della funzione di accesso devono esistere ed essere accessibile.
*  Se l'utilizzo è come destinazione di un'assegnazione semplice ([assegnamento semplice](expressions.md#simple-assignment)), il `set` della funzione di accesso devono esistere ed essere accessibile.
*  Se l'utilizzo è come destinazione di assegnazione composta ([assegnazione composta](expressions.md#compound-assignment)), o come destinazione della `++` oppure `--` operatori ([membri funzione](expressions.md#function-members),9, [ Espressioni di chiamata](expressions.md#invocation-expressions)), sia il `get` le funzioni di accesso e la `set` della funzione di accesso devono esistere ed essere accessibile.

Nell'esempio seguente, la proprietà `A.Text` è nascosto dalla proprietà `B.Text`, anche nei contesti in cui solo il `set` viene chiamata della funzione di accesso. Al contrario, la proprietà `B.Count` non è possibile accedere alla classe `M`, quindi la proprietà accessibile `A.Count` viene invece usato.

```csharp
class A
{
    public string Text {
        get { return "hello"; }
        set { }
    }

    public int Count {
        get { return 5; }
        set { }
    }
}

class B: A
{
    private string text = "goodbye"; 
    private int count = 0;

    new public string Text {
        get { return text; }
        protected set { text = value; }
    }

    new protected int Count { 
        get { return count; }
        set { count = value; }
    }
}

class M
{
    static void Main() {
        B b = new B();
        b.Count = 12;             // Calls A.Count set accessor
        int i = b.Count;          // Calls A.Count get accessor
        b.Text = "howdy";         // Error, B.Text set accessor not accessible
        string s = b.Text;        // Calls B.Text get accessor
    }
}
```

Una funzione di accesso utilizzato per implementare un'interfaccia non abbia un *accessor_modifier*. Se solo una funzione di accesso viene usato per implementare un'interfaccia, l'altra funzione può essere dichiarata con un *accessor_modifier*:
```csharp
public interface I
{
    string Prop { get; }
}

public class C: I
{
    public Prop {
        get { return "April"; }       // Must not have a modifier here
        internal set {...}            // Ok, because I.Prop has no set accessor
    }
}
```

### <a name="virtual-sealed-override-and-abstract-property-accessors"></a>Virtuale, sealed, sostituzione e le funzioni di accesso proprietà astratta

Oggetto `virtual` dichiarazione di proprietà indica che le funzioni di accesso della proprietà sono virtuali. Il `virtual` modificatore viene applicato a entrambe le funzioni di accesso di una proprietà di lettura / scrittura, ovvero non è possibile solo una funzione di accesso per una proprietà di lettura / scrittura sia virtuale.

Un `abstract` dichiarazione di proprietà specifica che le funzioni di accesso della proprietà sono virtuali, ma non fornisce un'implementazione effettiva delle funzioni di accesso. Invece, le classi derivate non astratte sono necessarie per fornire la propria implementazione per le funzioni di accesso eseguendo l'override della proprietà. Poiché una funzione di accesso per una dichiarazione di proprietà astratta non fornisce alcuna implementazione effettiva, relativi *accessor_body* costituita semplicemente da un punto e virgola.

Una dichiarazione di proprietà che include sia la `abstract` e `override` modificatori specifica che la proprietà è astratta ed esegue l'override di una proprietà di base. Le funzioni di accesso di tale proprietà anche sono astratte.

Le dichiarazioni di proprietà astratti sono consentite solo nelle classi astratte ([classi astratte](classes.md#abstract-classes)). Le funzioni di accesso di una proprietà virtuale ereditata può essere sottoposto a override in una classe derivata includendo una dichiarazione di proprietà che specifica un `override` direttiva. Questo è noto come un ***eseguendo l'override di dichiarazione di proprietà***. Dichiarazione di proprietà di override non dichiara una nuova proprietà. Al contrario, semplicemente specializzata le implementazioni delle funzioni di accesso di una proprietà virtuale esistente.

Dichiarazione di proprietà di override deve specificare i modificatori di accessibilità stesso esatto, tipo e nome della proprietà ereditata. Se la proprietà ereditata ha una sola funzione di accesso (ad esempio, se la proprietà ereditata è di sola lettura o in sola lettura), la proprietà di override deve includere solo tale funzione di accesso. Se la proprietà ereditata include entrambe le funzioni di accesso (ad esempio, se la proprietà ereditata è lettura / scrittura), la proprietà di override può includere una sola funzione di accesso o entrambe le funzioni di accesso.

Può includere una dichiarazione di proprietà esegue l'override di `sealed` modificatore. Questo modificatore evita una classe derivata da ulteriormente l'override della proprietà. Le funzioni di accesso di una proprietà sealed inoltre sono sealed.

Ad eccezione delle differenze nella dichiarazione e la chiamata di sintassi, override virtuale, sealed e astratte delle funzioni di accesso si comportano esattamente come virtual, sealed, override e metodi astratti. In particolare, le regole descritte [metodi virtuali](classes.md#virtual-methods), [metodi di Override](classes.md#override-methods), [Sealed i metodi](classes.md#sealed-methods), e [metodi astratti](classes.md#abstract-methods) applicare come se le funzioni di accesso sono stati i metodi di un form corrispondente:

*  Oggetto `get` della funzione di accesso corrispondente a un metodo senza parametri con un valore restituito del tipo di proprietà e gli stessi modificatori di proprietà che lo contiene.
*  Oggetto `set` funzione di accesso corrisponde a un metodo con un singolo parametro di valore del tipo di proprietà, un `void` il tipo restituito e gli stessi modificatori di proprietà che lo contiene.

Nell'esempio
```csharp
abstract class A
{
    int y;

    public virtual int X {
        get { return 0; }
    }

    public virtual int Y {
        get { return y; }
        set { y = value; }
    }

    public abstract int Z { get; set; }
}
```
`X` è una proprietà di sola lettura virtuale, `Y` è una proprietà di lettura / scrittura virtuale, e `Z` è una proprietà di lettura / scrittura astratta. In quanto `Z` è astratto, la classe contenitore `A` deve inoltre essere dichiarata astratta.

Una classe che deriva da `A` è illustrato di seguito:
```csharp
class B: A
{
    int z;

    public override int X {
        get { return base.X + 1; }
    }

    public override int Y {
        set { base.Y = value < 0? 0: value; }
    }

    public override int Z {
        get { return z; }
        set { z = value; }
    }
}
```

Questo caso, le dichiarazioni delle `X`, `Y`, e `Z` sta eseguendo l'override di dichiarazioni di proprietà. Ogni dichiarazione di proprietà corrisponde esattamente i modificatori di accessibilità, tipo e nome della proprietà ereditata corrispondente. Il `get` funzione di accesso di `X` e il `set` funzione di accesso di `Y` usano il `base` parola chiave per accedere alle funzioni di accesso ereditate. La dichiarazione di `Z` esegue l'override di entrambe le funzioni di accesso astratte, pertanto, non esistono membri funzione astratta in sospeso nel `B`, e `B` è la possibilità di essere una classe non astratta.

Quando una proprietà viene dichiarata come un `override`, qualsiasi funzione di accesso sottoposta a override deve essere accessibile al codice di override. Inoltre, l'accessibilità dichiarata di entrambe le proprietà o nell'indicizzatore stesso e delle funzioni di accesso, deve corrispondere a quello del membro sottoposto a override e le funzioni di accesso. Ad esempio:
```csharp
public class B
{
    public virtual int P {
        protected set {...}
        get {...}
    }
}

public class D: B
{
    public override int P {
        protected set {...}            // Must specify protected here
        get {...}                      // Must not have a modifier here
    }
}
```

## <a name="events"></a>Eventi

Un' ***evento*** è un membro che consente a un oggetto o una classe per fornire le notifiche. I client è possono collegare il codice eseguibile per gli eventi fornendo ***gestori eventi***.

Gli eventi vengono dichiarati usando *event_declaration*s:

```antlr
event_declaration
    : attributes? event_modifier* 'event' type variable_declarators ';'
    | attributes? event_modifier* 'event' type member_name '{' event_accessor_declarations '}'
    ;

event_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'static'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | event_modifier_unsafe
    ;

event_accessor_declarations
    : add_accessor_declaration remove_accessor_declaration
    | remove_accessor_declaration add_accessor_declaration
    ;

add_accessor_declaration
    : attributes? 'add' block
    ;

remove_accessor_declaration
    : attributes? 'remove' block
    ;
```

Un' *event_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso ](classes.md#access-modifiers)), il `new` ([il nuovo modificatore](classes.md#the-new-modifier)), `static` ([metodi statici e di istanza](classes.md#static-and-instance-methods)), `virtual` ([metodi virtuali](classes.md#virtual-methods)), `override` ([Metodi di override](classes.md#override-methods)), `sealed` ([Sealed i metodi](classes.md#sealed-methods)), `abstract` ([metodi astratti](classes.md#abstract-methods)), e `extern` ([Metodi esterni](classes.md#external-methods)) modificatori.

Le dichiarazioni di eventi sono soggetti alle stesse regole come le dichiarazioni di metodo ([metodi](classes.md#methods)) in relazione le combinazioni valide di modificatori.

Il *tipo* di un evento dichiarazione deve essere un *delegate_type* ([fanno riferimento ai tipi](types.md#reference-types)) e che *delegate_type* deve essere almeno come l'evento accessibile ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).

Può includere una dichiarazione di evento *event_accessor_declarations*. Tuttavia, in caso contrario, per non-extern, gli eventi non astratta, il compilatore fornisce automaticamente ([di eventi campo](classes.md#field-like-events)); per gli eventi extern, le funzioni di accesso vengono fornite esternamente.

Una dichiarazione di evento che omette *event_accessor_declarations* definisce uno o più eventi, ovvero uno per ogni le *variable_declarator*s. Gli attributi e modificatori si applicano a tutti i membri dichiarati da ad un' *event_declaration*.

Tratta di un errore in fase di compilazione per un *event_declaration* includono entrambe le `abstract` modificatore e delimitato da parentesi graffa *event_accessor_declarations*.

Quando una dichiarazione di evento include un' `extern` modificatore, l'evento viene definito un ***evento esterno***. Poiché una dichiarazione di evento esterno non fornisce alcuna implementazione effettiva, è consentito includere entrambi i `extern` modificatore e *event_accessor_declarations*.

È un errore in fase di compilazione per un *variable_declarator* di una dichiarazione di evento con un `abstract` oppure `external` modificatore per includere una *variable_initializer*.

Un evento può essere utilizzato come operando di sinistra del `+=` e `-=` operatori ([assegnazione evento](expressions.md#event-assignment)). Questi operatori vengono utilizzati, rispettivamente, per collegare gestori di eventi a o per rimuovere i gestori di eventi da un evento, e i modificatori di accesso dell'evento di controllano i contesti in cui tali operazioni sono consentite.

Poiché `+=` e `-=` sono le uniche operazioni consentite su un evento all'esterno del tipo che dichiara l'evento, il codice esterno possono aggiungere e rimuovere gestori per un evento, ma non possono in alcun altro modo ottenere o modificare l'elenco sottostante dell'evento gestori.

In un'operazione del form `x += y` oppure `x -= y`, quando `x` è un evento e il riferimento viene eseguita all'esterno del tipo che contiene la dichiarazione del `x`, il risultato dell'operazione ha tipo `void` (eliminando la necessità il tipo della `x`, con il valore di `x` dopo l'assegnazione). Questa regola impedisce indirettamente esaminando il delegato sottostante di un evento di codice esterno.

Nell'esempio seguente mostra come i gestori eventi sono collegati a istanze del `Button` classe:
```csharp
public delegate void EventHandler(object sender, EventArgs e);

public class Button: Control
{
    public event EventHandler Click;
}

public class LoginDialog: Form
{
    Button OkButton;
    Button CancelButton;

    public LoginDialog() {
        OkButton = new Button(...);
        OkButton.Click += new EventHandler(OkButtonClick);
        CancelButton = new Button(...);
        CancelButton.Click += new EventHandler(CancelButtonClick);
    }

    void OkButtonClick(object sender, EventArgs e) {
        // Handle OkButton.Click event
    }

    void CancelButtonClick(object sender, EventArgs e) {
        // Handle CancelButton.Click event
    }
}
```

In questo caso, il `LoginDialog` costruttore di istanze vengono create due `Button` istanze e i gestori eventi per il `Click` gli eventi.

### <a name="field-like-events"></a>Campo di eventi

All'interno del testo di programma della classe o struct che contiene la dichiarazione di un evento, alcuni eventi possono essere usati come i campi. Per essere utilizzato in questo modo, un evento non deve essere `abstract` oppure `extern`e non deve includere in modo esplicito *event_accessor_declarations*. Tale evento può essere utilizzato in qualsiasi contesto che consente a un campo. Il campo contiene un delegato ([delegati](delegates.md)) che fa riferimento all'elenco di gestori di eventi che sono stati aggiunti all'evento. Se nessun gestore di eventi sono stati aggiunti, il campo contiene `null`.

Nell'esempio
```csharp
public delegate void EventHandler(object sender, EventArgs e);

public class Button: Control
{
    public event EventHandler Click;

    protected void OnClick(EventArgs e) {
        if (Click != null) Click(this, e);
    }

    public void Reset() {
        Click = null;
    }
}
```
`Click` viene utilizzato come un campo all'interno di `Button` classe. Come illustrato nell'esempio, il campo può essere esaminato, modificato e utilizzato nelle espressioni di chiamata al delegato. Il `OnClick` metodo nella `Button` classe "genera" il `Click` evento. Generare un evento equivale a richiamare il delegato rappresentato dall'evento. Non sono quindi necessari speciali costrutti di linguaggio per generare eventi. Si noti che la chiamata di delegato è preceduta da un controllo, per garantire che il delegato è diverso da null.

All'esterno della dichiarazione del `Button` (classe), il `Click` membro può essere utilizzato solo sul lato sinistro delle `+=` e `-=` operatori, come in
```csharp
b.Click += new EventHandler(...);
```
Aggiunge un delegato all'elenco chiamate del `Click` evento, e
```csharp
b.Click -= new EventHandler(...);
```
Rimuove un delegato dall'elenco chiamate del `Click` evento.

Durante la compilazione di un evento simile a campo, il compilatore può automaticamente crea archiviazione per contenere il delegato e crea le funzioni di accesso per l'evento che aggiungono o rimuovono gestori eventi per il campo di delegato. Le operazioni di aggiunta e rimozione sono thread-safe e può (ma non si deve) essere eseguite mentre viene mantenuto il blocco ([istruzione lock](statements.md#the-lock-statement)) l'oggetto contenitore per un evento di istanza, o l'oggetto di tipo ([anonima espressioni per la creazione dell'oggetto](expressions.md#anonymous-object-creation-expressions)) per un evento statico.

Di conseguenza, una dichiarazione di evento del form:
```csharp
class X
{
    public event D Ev;
}
```
verrà compilato su un valore equivalente a:
```csharp
class X
{
    private D __Ev;  // field to hold the delegate

    public event D Ev {
        add {
            /* add the delegate in a thread safe way */
        }

        remove {
            /* remove the delegate in a thread safe way */
        }
    }
}
```
All'interno della classe `X`, fa riferimento alle `Ev` sul lato sinistro delle `+=` e `-=` operatori causano l'aggiunta e rimuovere le funzioni di accesso da richiamare. Tutti gli altri riferimenti a `Ev` vengono compilati per fare riferimento a campo nascosto `__Ev` invece ([l'accesso ai membri](expressions.md#member-access)). Il nome "`__Ev`" è arbitrario; il campo nascosto potrebbe avere qualsiasi nome o senza nome affatto.

### <a name="event-accessors"></a>Funzioni di accesso agli eventi

Le dichiarazioni di eventi in genere omettono *event_accessor_declarations*, come nel `Button` esempio precedente. Una situazione che richiede ad esempio, quando comporta pertanto il caso in cui il costo di archiviazione di un campo per ogni evento non è accettabile. In questi casi, una classe può comprendere *event_accessor_declarations* e usare un meccanismo privato per archiviare l'elenco dei gestori di eventi.

Il *event_accessor_declarations* di un evento specificano le istruzioni eseguibili associate all'aggiunta e rimozione di gestori di eventi.

Le dichiarazioni di funzione di accesso è costituito un *add_accessor_declaration* e una *remove_accessor_declaration*. Ogni dichiarazione della funzione di accesso è costituita da token `add` oppure `remove` seguita da un *blocco*. Il *blocco* associata a un *add_accessor_declaration* specifica le istruzioni da eseguire quando viene aggiunto un gestore eventi e il *blocco* associato con un *remove_accessor_declaration* specifica le istruzioni da eseguire quando un gestore eventi viene rimosso.

Ciascuna *add_accessor_declaration* e *remove_accessor_declaration* corrisponde a un metodo con un singolo parametro di valore del tipo di evento e un `void` tipo restituito. Il parametro implicito di una funzione di accesso di evento è denominato `value`. Quando un evento viene utilizzato in un'assegnazione di eventi, viene utilizzata la funzione di accesso eventi appropriato. In particolare, se è l'operatore di assegnazione `+=` viene utilizzata la funzione di accesso add e se l'operatore di assegnazione è `-=` viene utilizzata la funzione di accesso remove. In entrambi i casi, l'operando destro dell'operatore di assegnazione viene utilizzato come argomento alla funzione di accesso eventi. Il blocco di un *add_accessor_declaration* o una *remove_accessor_declaration* devono essere conformi alle regole per `void` metodi descritti in [corpo del metodo](classes.md#method-body). In particolare, `return` le istruzioni in questo blocco non è consentite specificare un'espressione.

Poiché una funzione di accesso eventi in modo implicito include un parametro denominato `value`, è un errore in fase di compilazione per una costante o variabile locale dichiarata in una funzione di accesso eventi quel nome.

Nell'esempio
```csharp
class Control: Component
{
    // Unique keys for events
    static readonly object mouseDownEventKey = new object();
    static readonly object mouseUpEventKey = new object();

    // Return event handler associated with key
    protected Delegate GetEventHandler(object key) {...}

    // Add event handler associated with key
    protected void AddEventHandler(object key, Delegate handler) {...}

    // Remove event handler associated with key
    protected void RemoveEventHandler(object key, Delegate handler) {...}

    // MouseDown event
    public event MouseEventHandler MouseDown {
        add { AddEventHandler(mouseDownEventKey, value); }
        remove { RemoveEventHandler(mouseDownEventKey, value); }
    }

    // MouseUp event
    public event MouseEventHandler MouseUp {
        add { AddEventHandler(mouseUpEventKey, value); }
        remove { RemoveEventHandler(mouseUpEventKey, value); }
    }

    // Invoke the MouseUp event
    protected void OnMouseUp(MouseEventArgs args) {
        MouseEventHandler handler; 
        handler = (MouseEventHandler)GetEventHandler(mouseUpEventKey);
        if (handler != null)
            handler(this, args);
    }
}
```
il `Control` classe implementa un meccanismo di archiviazione interno per gli eventi. Il `AddEventHandler` metodo associa il valore di un delegato con una chiave, il `GetEventHandler` metodo viene restituito il delegato attualmente associato a una chiave e il `RemoveEventHandler` metodo rimuove un delegato come gestore eventi per l'evento specificato. È probabile che il meccanismo di archiviazione sottostante è progettato in modo che non sono previsti costi per l'associazione di un `null` delegare valore con una chiave, così da eventi non gestiti alcuna archiviazione.

### <a name="static-and-instance-events"></a>Eventi statici e di istanza

Quando una dichiarazione di evento include un `static` modificatore, l'evento viene definito un ***evento statico***. Se non si specifica `static` modificatore è presente, l'evento viene definito un ***evento istanza***.

Un evento statico non è associato a un'istanza specifica e tratta di un errore in fase di compilazione per fare riferimento a `this` in funzioni di accesso di un evento statico.

Un evento di istanza è associato a una determinata istanza di una classe e questa istanza è accessibile come `this` ([questo accesso](expressions.md#this-access)) nelle funzioni di accesso di tale evento.

Quando si fa riferimento a un evento un *member_access* ([l'accesso ai membri](expressions.md#member-access)) nel formato `E.M`se `M` è un evento statico, `E` deve indicare un tipo contenente `M`e se `M` è un evento di istanza, È necessario che un'istanza di un tipo contenente `M`.

Le differenze tra statica e i membri di istanza sono descritti dettagliatamente nella [i membri statici e di istanza](classes.md#static-and-instance-members).

### <a name="virtual-sealed-override-and-abstract-event-accessors"></a>Virtuale, sealed, override e funzioni di accesso eventi astratti

Oggetto `virtual` dichiarazione di evento indica che le funzioni di accesso dell'evento in questione sono virtuali. Il `virtual` modificatore viene applicato a entrambe le funzioni di accesso di un evento.

Un `abstract` dichiarazione di evento indica che le funzioni di accesso dell'evento sono virtuali, ma non fornisce un'implementazione effettiva delle funzioni di accesso. Invece, le classi derivate non astratte sono necessarie per fornire la propria implementazione per le funzioni di accesso eseguendo l'override dell'evento. Poiché una dichiarazione di evento astratto non fornisce alcuna implementazione effettiva, non può fornire delimitato da parentesi graffa *event_accessor_declarations*.

Una dichiarazione di evento che include sia la `abstract` e `override` modificatori specifica che l'evento è di tipo abstract ed esegue l'override di un evento di base. Le funzioni di accesso di tale evento inoltre sono astratte.

Le dichiarazioni di eventi astratto sono consentite solo nelle classi astratte ([classi astratte](classes.md#abstract-classes)).

Le funzioni di accesso di un evento virtuale ereditato può essere sottoposto a override in una classe derivata includendo una dichiarazione di evento che specifica un `override` modificatore. Questo è noto come un ***eseguendo l'override di dichiarazione di evento***. Una dichiarazione di evento override non dichiara un nuovo evento. Al contrario, semplicemente specializzata le implementazioni delle funzioni di accesso di un evento virtuale esistente.

Esegue l'override di una dichiarazione di evento è necessario specificare i modificatori di accessibilità stesso esatto, tipo e nome dell'evento sottoposto a override.

Può includere una dichiarazione di evento esegue l'override di `sealed` modificatore. Questo modificatore evita una classe derivata da ulteriormente override dell'evento. Le funzioni di accesso di un evento sealed inoltre sono sealed.

Si tratta di un errore in fase di compilazione per una dichiarazione di eventi esegue l'override includere un `new` modificatore.

Ad eccezione delle differenze nella dichiarazione e la chiamata di sintassi, override virtuale, sealed e astratte delle funzioni di accesso si comportano esattamente come virtual, sealed, override e metodi astratti. In particolare, le regole descritte [metodi virtuali](classes.md#virtual-methods), [metodi di Override](classes.md#override-methods), [Sealed i metodi](classes.md#sealed-methods), e [metodi astratti](classes.md#abstract-methods) applicare come se le funzioni di accesso sono stati i metodi di un modulo corrispondente. Ogni funzione di accesso corrisponde a un metodo con un singolo parametro di valore del tipo di evento, un `void` il tipo restituito e gli stessi modificatori che l'evento che lo contiene.

## <a name="indexers"></a>Indicizzatori

Un' ***indicizzatore*** è un membro che consente a un oggetto da indicizzare esattamente come una matrice. Gli indicizzatori vengano dichiarati usando *indexer_declaration*s:

```antlr
indexer_declaration
    : attributes? indexer_modifier* indexer_declarator indexer_body
    ;

indexer_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'virtual'
    | 'sealed'
    | 'override'
    | 'abstract'
    | 'extern'
    | indexer_modifier_unsafe
    ;

indexer_declarator
    : type 'this' '[' formal_parameter_list ']'
    | type interface_type '.' 'this' '[' formal_parameter_list ']'
    ;

indexer_body
    : '{' accessor_declarations '}' 
    | '=>' expression ';'
    ;
```

Un' *indexer_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso ](classes.md#access-modifiers)), il `new` ([il nuovo modificatore](classes.md#the-new-modifier)), `virtual` ([metodi virtuali](classes.md#virtual-methods)), `override` ([l'Override di metodi](classes.md#override-methods) ), `sealed` ([Sealed i metodi](classes.md#sealed-methods)), `abstract` ([metodi astratti](classes.md#abstract-methods)), e `extern` ([metodi esterni](classes.md#external-methods)) modificatori.

Le dichiarazioni dell'indicizzatore sono soggetti alle stesse regole come le dichiarazioni di metodo ([metodi](classes.md#methods)) in relazione le combinazioni valide di modificatori, con l'unica eccezione è che il modificatore static non è consentito in una dichiarazione di indicizzatore.

I modificatori `virtual`, `override`, e `abstract` si escludono a vicenda, ad eccezione in un caso. Il `abstract` e `override` modificatori possono essere utilizzati insieme, in modo che un indicizzatore astratto può eseguire l'override di un quello virtuale.

Il *tipo* di un indicizzatore dichiarazione specifica il tipo di elemento dell'indicizzatore introdotta dalla dichiarazione. A meno che l'indicizzatore è un'implementazione di membro di interfaccia esplicita, il *tipo* è seguito dalla parola chiave `this`. Per un'implementazione di membro di interfaccia esplicita, il *tipo* seguito da un *interface_type*, un "`.`" e la parola chiave `this`. A differenza di altri membri, gli indicizzatori non hanno nomi definiti dall'utente.

Il *formal_parameter_list* specifica i parametri dell'indicizzatore. Elenco di parametri formali di un indicizzatore corrisponde a quello di un metodo ([parametri del metodo](classes.md#method-parameters)), ad eccezione del fatto che deve essere specificato almeno un parametro e che le `ref` e `out` modificatori di parametro non sono consentiti .

Il *tipo* di un indicizzatore e ognuno dei tipi a cui fa riferimento il *formal_parameter_list* devono essere accessibili almeno quanto l'indicizzatore ([vincoli accessibilità](basic-concepts.md#accessibility-constraints)).

Un' *indexer_body* possono essere costituiti da un ***corpo della funzione di accesso*** o un ***corpo dell'espressione***. In un corpo di funzione di accesso *accessor_declarations*, che deve essere racchiuso tra parentesi "`{`"e"`}`" token, dichiarare le funzioni di accesso ([funzioni di accesso](classes.md#accessors)) della proprietà. Le funzioni di accesso specificare le istruzioni eseguibili associate alla lettura e scrittura della proprietà.

Un corpo di espressione costituita da "`=>`" seguita da un'espressione `E` e un punto e virgola è perfettamente equivalente al corpo dell'istruzione `{ get { return E; } }`e può pertanto essere usato solo per specificare gli indicizzatori solo Get dove è il risultato del metodo Get assegnato da un'unica espressione.

Anche se la sintassi per l'accesso a un elemento dell'indicizzatore è uguale a quello per un elemento della matrice, un elemento dell'indicizzatore non è classificato come una variabile. Non è pertanto possibile passare un elemento dell'indicizzatore come un `ref` o `out` argomento.

Elenco di parametri formali di un indicizzatore definisce la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) dell'indicizzatore. In particolare, la firma di un indicizzatore è costituito da numero e tipi di parametri formali. Il tipo di elemento e i nomi dei parametri formali non fanno parte della firma di un indicizzatore.

La firma di un indicizzatore sia diversa dalle firme di tutti gli altri indicizzatori dichiarati nella stessa classe.

Gli indicizzatori e proprietà sono concettualmente molto simile, ma differiscono nel modo seguente:

*  Una proprietà è identificata dal relativo nome, mentre un indicizzatore è identificato in base alla firma.
*  Accesso a una proprietà tramite un *simple_name* ([nomi semplici](expressions.md#simple-names)) o un *member_access* ([l'accesso ai membri](expressions.md#member-access)), mentre un indicizzatore elemento avviene tramite un *element_access* ([accesso all'indicizzatore](expressions.md#indexer-access)).
*  Una proprietà può essere un `static` membro, mentre un indicizzatore è sempre un membro di istanza.
*  Oggetto `get` funzione di accesso di una proprietà corrispondente a un metodo senza parametri, mentre un `get` della funzione di accesso di un indicizzatore corrispondente a un metodo con lo stesso elenco di parametri formali dell'indicizzatore.
*  Oggetto `set` funzione di accesso di una proprietà corrispondente a un metodo con un singolo parametro denominato `value`, mentre un `set` della funzione di accesso di un indicizzatore corrispondente a un metodo con lo stesso elenco parametri formali dell'indicizzatore, oltre a un parametro aggiuntivo denominato `value`.
*  È un errore in fase di compilazione per una funzione di accesso dell'indicizzatore dichiarare una variabile locale con lo stesso nome come parametro di indicizzatore.
*  In una dichiarazione di proprietà di override, si accede alla proprietà ereditata utilizzando la sintassi `base.P`, dove `P` è il nome della proprietà. In una dichiarazione di indicizzatore override, l'indicizzatore ereditato è possibile accedere tramite la sintassi `base[E]`, dove `E` è un elenco delimitato da virgole di espressioni.
*  Non è previsto di un indicizzatore"implementato automaticamente". È possibile avere un indicizzatore non astratta, non esterno con funzioni di accesso di un punto e virgola.

A parte queste differenze, tutte le regole definite [funzioni di accesso](classes.md#accessors) e [proprietà implementate automaticamente](classes.md#automatically-implemented-properties) si applicano anche alle anche per quanto riguarda le funzioni di accesso di proprietà.

Quando una dichiarazione di indicizzatore include un' `extern` modificatore, l'indicizzatore viene definito un ***indicizzatore esterno***. Poiché una dichiarazione di indicizzatore esterni non fornisce alcuna implementazione effettiva, ognuno dei relativi *accessor_declarations* è costituito da un punto e virgola.

Nell'esempio seguente viene dichiarato un `BitArray` classe che implementa un indicizzatore per l'accesso ai singoli bit nella matrice di bit.
```csharp
using System;

class BitArray
{
    int[] bits;
    int length;

    public BitArray(int length) {
        if (length < 0) throw new ArgumentException();
        bits = new int[((length - 1) >> 5) + 1];
        this.length = length;
    }

    public int Length {
        get { return length; }
    }

    public bool this[int index] {
        get {
            if (index < 0 || index >= length) {
                throw new IndexOutOfRangeException();
            }
            return (bits[index >> 5] & 1 << index) != 0;
        }
        set {
            if (index < 0 || index >= length) {
                throw new IndexOutOfRangeException();
            }
            if (value) {
                bits[index >> 5] |= 1 << index;
            }
            else {
                bits[index >> 5] &= ~(1 << index);
            }
        }
    }
}
```

Un'istanza del `BitArray` classe utilizza sostanzialmente meno memoria rispetto a un oggetto corrispondente `bool[]` (poiché ogni valore della prima occupa un solo bit invece di quest'ultimo di un byte), ma consente le stesse operazioni come un `bool[]`.

Quanto segue `CountPrimes` classe Usa un `BitArray` e l'algoritmo "crivello" per calcolare il numero di numeri primi compreso tra 1 e un determinato valore massimo:
```csharp
class CountPrimes
{
    static int Count(int max) {
        BitArray flags = new BitArray(max + 1);
        int count = 1;
        for (int i = 2; i <= max; i++) {
            if (!flags[i]) {
                for (int j = i * 2; j <= max; j += i) flags[j] = true;
                count++;
            }
        }
        return count;
    }

    static void Main(string[] args) {
        int max = int.Parse(args[0]);
        int count = Count(max);
        Console.WriteLine("Found {0} primes between 1 and {1}", count, max);
    }
}
```

Si noti che la sintassi per l'accesso agli elementi del `BitArray` è esattamente uguali a quelle per un `bool[]`.

Nell'esempio seguente viene illustrata una classe di griglia di 26 * 10 con un indicizzatore con due parametri. Il primo parametro deve essere una lettera maiuscola o minuscola nell'intervallo A-Z e il secondo deve essere un numero intero compreso nell'intervallo da 0 a 9.

```csharp
using System;

class Grid
{
    const int NumRows = 26;
    const int NumCols = 10;

    int[,] cells = new int[NumRows, NumCols];

    public int this[char c, int col] {
        get {
            c = Char.ToUpper(c);
            if (c < 'A' || c > 'Z') {
                throw new ArgumentException();
            }
            if (col < 0 || col >= NumCols) {
                throw new IndexOutOfRangeException();
            }
            return cells[c - 'A', col];
        }

        set {
            c = Char.ToUpper(c);
            if (c < 'A' || c > 'Z') {
                throw new ArgumentException();
            }
            if (col < 0 || col >= NumCols) {
                throw new IndexOutOfRangeException();
            }
            cells[c - 'A', col] = value;
        }
    }
}
```

### <a name="indexer-overloading"></a>L'overload di indicizzatore

Le regole di risoluzione dell'overload di indicizzatore sono descritte nel [inferenza del tipo](expressions.md#type-inference).

## <a name="operators"></a>Operatori

Un' ***operatore*** è un membro che definisce il significato di un operatore di espressione che può essere applicato a istanze della classe. Gli operatori vengono dichiarati usando *operator_declaration*s:

```antlr
operator_declaration
    : attributes? operator_modifier+ operator_declarator operator_body
    ;

operator_modifier
    : 'public'
    | 'static'
    | 'extern'
    | operator_modifier_unsafe
    ;

operator_declarator
    : unary_operator_declarator
    | binary_operator_declarator
    | conversion_operator_declarator
    ;

unary_operator_declarator
    : type 'operator' overloadable_unary_operator '(' type identifier ')'
    ;

overloadable_unary_operator
    : '+' | '-' | '!' | '~' | '++' | '--' | 'true' | 'false'
    ;

binary_operator_declarator
    : type 'operator' overloadable_binary_operator '(' type identifier ',' type identifier ')'
    ;

overloadable_binary_operator
    : '+'   | '-'   | '*'   | '/'   | '%'   | '&'   | '|'   | '^'   | '<<'
    | 'right_shift' | '=='  | '!='  | '>'   | '<'   | '>='  | '<='
    ;

conversion_operator_declarator
    : 'implicit' 'operator' type '(' type identifier ')'
    | 'explicit' 'operator' type '(' type identifier ')'
    ;

operator_body
    : block
    | '=>' expression ';'
    | ';'
    ;
```

Esistono tre categorie di operatori di overload: gli operatori unari ([operatori unari](classes.md#unary-operators)), gli operatori binari ([operatori binari](classes.md#binary-operators)) e gli operatori di conversione ([gli operatori di conversione ](classes.md#conversion-operators)).

Il *operator_body* entrambi un punto e virgola, una ***corpo dell'istruzione*** o un ***corpo dell'espressione***. È costituito da un corpo dell'istruzione di un *blocco*, che consente di specificare le istruzioni da eseguire quando viene richiamato l'operatore. Il *block* devono essere conformi alle regole per la restituzione di valore metodi descritti in [corpo del metodo](classes.md#method-body). Costituito da un corpo di espressione `=>` seguita da un'espressione e un punto e virgola e indica una singola espressione da eseguire quando viene richiamato l'operatore.

Per la `extern` operatori, il *operator_body* costituito semplicemente da un punto e virgola. Per tutti gli altri operatori, il *operator_body* è un corpo del blocco o un corpo di espressione.

Le regole seguenti si applicano a tutte le dichiarazioni di operatore:

*  Una dichiarazione dell'operatore deve includere sia una `public` e un `static` modificatore.
*  I parametri di un operatore devono essere parametri di valore ([parametri del valore](variables.md#value-parameters)). Tratta di un errore in fase di compilazione per una dichiarazione dell'operatore specificare `ref` o `out` parametri.
*  La firma di un operatore ([operatori unari](classes.md#unary-operators), [operatori binari](classes.md#binary-operators), [gli operatori di conversione](classes.md#conversion-operators)) deve essere diversa dalle firme di tutti gli altri operatori dichiarati nel classe stessa.
*  Tutti i tipi di cui viene fatto riferimento nella dichiarazione di un operatore devono essere accessibili almeno quanto l'operatore stesso ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).
*  È un errore per lo stesso modificatore venga visualizzato più volte nella dichiarazione di un operatore.

Ogni categoria di operatori impone restrizioni aggiuntive, come descritto nelle sezioni seguenti.

Analogamente agli altri membri, gli operatori dichiarati in una classe base vengono ereditati dalle classi derivate. Poiché le dichiarazioni di operatore richiedono sempre la classe o struct in cui l'operatore viene dichiarato a far parte della firma dell'operatore, non è possibile che un operatore dichiarato in una classe derivata nascondere un operatore dichiarato in una classe di base. Di conseguenza, il `new` modificatore non è necessaria e quindi mai consentito, in una dichiarazione dell'operatore.

Altre informazioni sugli operatori unari e binari sono reperibili nelle [operatori](expressions.md#operators).

Ulteriori informazioni sugli operatori di conversione sono reperibile nel [conversioni definite dall'utente](conversions.md#user-defined-conversions).

### <a name="unary-operators"></a>Operatori unari

Le regole seguenti si applicano alle dichiarazioni di operatore unario, in cui `T` denota il tipo di istanza della classe o struct che contiene la dichiarazione dell'operatore:

*  Unario `+`, `-`, `!`, o `~` operatore deve accettare un solo parametro di tipo `T` o `T?` e può restituire qualsiasi tipo.
*  Unario `++` oppure `--` operatore deve accettare un solo parametro di tipo `T` o `T?` e deve restituire un tipo stesso o un tipo derivato da esso.
*  Unario `true` oppure `false` operatore deve accettare un solo parametro di tipo `T` oppure `T?` e deve restituire un tipo `bool`.

La firma di un operatore unario è costituita da token operatore (`+`, `-`, `!`, `~`, `++`, `--`, `true`, o `false`) e il tipo di parametro formale singolo. Il tipo restituito non fa parte della firma dell'operatore unario, né è il nome del parametro formale.

Il `true` e `false` necessario che gli operatori unari. Se una classe che dichiara uno di questi operatori senza dichiarare anche l'altro, si verifica un errore in fase di compilazione. Il `true` e `false` operatori sono descritti dettagliatamente nella [definiti dall'utente di operatori logici condizionali](expressions.md#user-defined-conditional-logical-operators) e [espressioni booleane](expressions.md#boolean-expressions).

L'esempio seguente illustra l'implementazione e l'utilizzo successivo di `operator ++` per una classe di vettori integer:
```csharp
public class IntVector
{
    public IntVector(int length) {...}

    public int Length {...}                 // read-only property

    public int this[int index] {...}        // read-write indexer

    public static IntVector operator ++(IntVector iv) {
        IntVector temp = new IntVector(iv.Length);
        for (int i = 0; i < iv.Length; i++)
            temp[i] = iv[i] + 1;
        return temp;
    }
}

class Test
{
    static void Main() {
        IntVector iv1 = new IntVector(4);    // vector of 4 x 0
        IntVector iv2;

        iv2 = iv1++;    // iv2 contains 4 x 0, iv1 contains 4 x 1
        iv2 = ++iv1;    // iv2 contains 4 x 2, iv1 contains 4 x 2
    }
}
```

Si noti come il metodo dell'operatore restituisce il valore ottenuto aggiungendo 1 all'operando, esattamente come l'incremento e decremento (operatori) ([forma suffissa di incremento e decremento (operatori)](expressions.md#postfix-increment-and-decrement-operators)) e di incremento prefisso e decremento operatori ([incremento prefisso e decremento (operatori)](expressions.md#prefix-increment-and-decrement-operators)). A differenza di c++, questo metodo necessario non modificare il valore del relativo operando direttamente. In effetti, la modifica del valore dell'operando violerebbe la semantica standard di operatore di incremento suffisso.

### <a name="binary-operators"></a>Operatori binari

Le regole seguenti si applicano alle dichiarazioni di operatore binario, in cui `T` denota il tipo di istanza della classe o struct che contiene la dichiarazione dell'operatore:

*  Un operatore di spostamento non binario deve accettare due parametri, almeno uno dei quali è necessario digitare `T` o `T?`e può restituire qualsiasi tipo.
*  Un file binario `<<` oppure `>>` operatore deve accettare due parametri, il primo dei quali deve avere tipo `T` oppure `T?` e il secondo dei quali deve avere tipo `int` o `int?`e può restituire qualsiasi tipo.

La firma di un operatore binario è costituita da token operatore (`+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `<<`, `>>`, `==`, `!=`, `>`, `<`, `>=`, o `<=`) e i tipi dei due parametri formali. Il tipo restituito e i nomi dei parametri formali non fanno parte della firma di un operatore binario.

Alcuni operatori binari devono essere dichiarati in coppia. Per ogni dichiarazione degli operatori di una coppia, deve essere una dichiarazione dell'operatore della coppia corrispondente. Due dichiarazioni di operatore corrispondenti quando hanno lo stesso tipo restituito e lo stesso tipo per ogni parametro. Gli operatori seguenti è necessario che:

*  `operator ==` e `operator !=`
*  `operator >` e `operator <`
*  `operator >=` e `operator <=`

### <a name="conversion-operators"></a>Operatori di conversione

Una dichiarazione di operatore di conversione introduce un ***conversione definita dall'utente*** ([conversioni definite dall'utente](conversions.md#user-defined-conversions)) che integra le conversioni implicite ed esplicite predefinite.

Una dichiarazione di operatore di conversione che include il `implicit` (parola chiave) introduce una conversione implicita definito dall'utente. Le conversioni implicite possono verificarsi in diverse situazioni, incluse le chiamate di funzione membro, espressioni cast e le assegnazioni. Questo è descritto più dettagliatamente in [conversioni implicite](conversions.md#implicit-conversions).

Una dichiarazione di operatore di conversione che include il `explicit` (parola chiave) introduce una conversione esplicita definita dall'utente. Le conversioni esplicite possono verificarsi nelle espressioni cast e ulteriori informazioni, vedere [conversioni esplicite](conversions.md#explicit-conversions).

Un operatore di conversione converte da un tipo di origine, indicato dal tipo di parametro dell'operatore di conversione, di un tipo di destinazione indicato dal tipo restituito dell'operatore di conversione.

Per un tipo di origine specificato `S` e tipo di destinazione `T`, se `S` oppure `T` sono i tipi nullable, consentire `S0` e `T0` fanno riferimento ai tipi sottostanti, in caso contrario `S0` e `T0` sono uguale a `S` e `T` rispettivamente. Una classe o struct è possibile dichiarare una conversione da un tipo di origine `S` a un tipo di destinazione `T` solo se tutte le operazioni seguenti sono vere:

*  `S0` e `T0` sono tipi diversi.
*  Sia `S0` o `T0` è il tipo di classe o struct in cui viene eseguita la dichiarazione di operatore.
*  Né `S0` né `T0` è un *interface_type*.
*  Escludendo le conversioni definite dall'utente, che non esiste una conversione da `S` al `T` o da `T` a `S`.

Ai fini di queste regole, qualsiasi tipo di parametri associati `S` o `T` sono considerati tipi univoci non hanno alcuna relazione di ereditarietà con gli altri tipi e i vincoli per il tipo di tali parametri vengono ignorati.

Nell'esempio
```csharp
class C<T> {...}

class D<T>: C<T>
{
    public static implicit operator C<int>(D<T> value) {...}      // Ok
    public static implicit operator C<string>(D<T> value) {...}   // Ok
    public static implicit operator C<T>(D<T> value) {...}        // Error
}
```
il primo due operatore le dichiarazioni sono consentite perché, per quanto riguarda [indicizzatori](classes.md#indexers).3, `T` e `int` e `string` rispettivamente sono considerati tipi univoci con alcuna relazione. Tuttavia, il terzo operatore è un errore in quanto `C<T>` è la classe di base di `D<T>`.

In base alla regola secondo che ne consegue che è necessario convertire un operatore di conversione da o verso il tipo di classe o struct in cui l'operatore viene dichiarato. Ad esempio, è possibile per un tipo di classe o struct `C` per definire una conversione da `C` per `int` e dal `int` a `C`, ma non da `int` per `bool`.

Non è possibile ridefinire direttamente una conversione predefinita. Di conseguenza, gli operatori di conversione non sono consentiti per conversione da o verso `object` poiché le conversioni implicite ed esplicite già presenti tra `object` e tutti gli altri tipi. Analogamente, l'origine né i tipi di destinazione di una conversione possono essere un tipo di base di altra parte, poiché una conversione sarebbe già esistente.

Tuttavia, è possibile dichiarare gli operatori nei tipi generici che, per gli argomenti di tipo specifico, specificano le conversioni per cui esistono già come le conversioni predefinite. Nell'esempio
```csharp
struct Convertible<T>
{
    public static implicit operator Convertible<T>(T value) {...}
    public static explicit operator T(Convertible<T> value) {...}
}
```
Quando digita `object` viene specificato come argomento di tipo per `T`, il secondo operatore dichiara una conversione che esiste già (implicita e pertanto anche esplicita, non esiste conversione da qualsiasi tipo al tipo `object`).

Nei casi in cui è presente una conversione predefinita tra due tipi, le conversioni definite dall'utente tra tali tipi vengono ignorate. In particolare:

*  Se una conversione implicita predefinita ([conversioni implicite](conversions.md#implicit-conversions)) esistente dal tipo `S` per digitare `T`, tutte le conversioni definite dall'utente (implicite o esplicite) dal `S` a `T` vengono ignorati.
*  Se la conversione esplicita predefinita ([conversioni esplicite](conversions.md#explicit-conversions)) esistente dal tipo `S` per digitare `T`, tutte le conversioni esplicite definite dall'utente da `S` a `T` vengono ignorati. Inoltre:

Se `T` è un tipo di interfaccia, le conversioni implicite definite dall'utente dal `S` a `T` vengono ignorati.

In caso contrario, definita dall'utente conversioni implicite da' `S` a `T` vengono prese in considerazione.

Per tutti i tipi, ma `object`, gli operatori dichiarato dal `Convertible<T>` tipo precedente non sono in conflitto con le conversioni predefinite. Ad esempio:
```csharp
void F(int i, Convertible<int> n) {
    i = n;                          // Error
    i = (int)n;                     // User-defined explicit conversion
    n = i;                          // User-defined implicit conversion
    n = (Convertible<int>)i;        // User-defined implicit conversion
}
```

Tuttavia, per il tipo `object`, le conversioni predefinite nascondono le conversioni definite dall'utente in tutti i casi tranne il:

```csharp
void F(object o, Convertible<object> n) {
    o = n;                         // Pre-defined boxing conversion
    o = (object)n;                 // Pre-defined boxing conversion
    n = o;                         // User-defined implicit conversion
    n = (Convertible<object>)o;    // Pre-defined unboxing conversion
}
```

Conversioni definite dall'utente non sono consentite per conversione da o verso *interface_type*s. In particolare, questa restrizione garantisce che venga eseguita alcuna trasformazione definita dall'utente durante la conversione in un *interface_type*e che una conversione verso un *interface_type* ha esito positivo solo se l'oggetto convertito implementa effettivamente l'oggetto specificato *interface_type*.

La firma di un operatore di conversione è costituita dal tipo di origine e il tipo di destinazione. Si noti che si tratta dell'unica forma di membro per cui il tipo restituito fa parte della firma. Il `implicit` o `explicit` classificazione di un operatore di conversione non fa parte della firma dell'operatore. Di conseguenza, una classe o struct non possono dichiarare entrambi un `implicit` e un `explicit` operatore di conversione con gli stessi tipi di origine e di destinazione.

In generale, le conversioni implicite definite dall'utente devono essere progettate per non generare eccezioni e perdere informazioni. Se una conversione definita dall'utente può dar luogo a eccezioni (ad esempio, perché l'argomento di origine non è compreso nell'intervallo) o perdita di informazioni (ad esempio, ignorando i bit più significativi) e quindi che la conversione deve essere definita come una conversione esplicita.

Nell'esempio
```csharp
using System;

public struct Digit
{
    byte value;

    public Digit(byte value) {
        if (value < 0 || value > 9) throw new ArgumentException();
        this.value = value;
    }

    public static implicit operator byte(Digit d) {
        return d.value;
    }

    public static explicit operator Digit(byte b) {
        return new Digit(b);
    }
}
```
la conversione da `Digit` al `byte` è implicito perché mai genera eccezioni o perde informazioni, ma la conversione da `byte` al `Digit` esplicita poiché `Digit` possono rappresentare solo un sottoinsieme dei possibili i valori di un `byte`.

## <a name="instance-constructors"></a>Costruttori di istanza

Un ***costruttore di istanza*** è un membro che implementa le azioni necessarie per inizializzare un'istanza di una classe, Costruttori di istanza vengono dichiarati utilizzando *constructor_declaration*s:

```antlr
constructor_declaration
    : attributes? constructor_modifier* constructor_declarator constructor_body
    ;

constructor_modifier
    : 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'extern'
    | constructor_modifier_unsafe
    ;

constructor_declarator
    : identifier '(' formal_parameter_list? ')' constructor_initializer?
    ;

constructor_initializer
    : ':' 'base' '(' argument_list? ')'
    | ':' 'this' '(' argument_list? ')'
    ;

constructor_body
    : block
    | ';'
    ;
```

Oggetto *constructor_declaration* può includere un set di *attributi* ([attributi](attributes.md)), una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso ](classes.md#access-modifiers)) e un' `extern` ([metodi esterni](classes.md#external-methods)) modificatore. Una dichiarazione di costruttore non è consentita includere più volte lo stesso modificatore.

Il *identifier* di un *constructor_declarator* deve denominare la classe in cui viene dichiarato il costruttore di istanza. Se viene specificato un altro nome, si verifica un errore in fase di compilazione.

L'opzione facoltativa *formal_parameter_list* di un'istanza del costruttore è soggetto alle stesse regole di *formal_parameter_list* di un metodo ([metodi](classes.md#methods)). Elenco di parametri formali definisce la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) di un costruttore di istanza e determina in base al quale il processo di risoluzione dell'overload ([inferenza del tipo](expressions.md#type-inference)) consente di selezionare un particolare costruttore di istanza in una chiamata.

Ognuno dei tipi a cui fa riferimento il *formal_parameter_list* di un'istanza del costruttore deve essere accessibile almeno quanto il costruttore stesso ([vincoli accessibilità](basic-concepts.md#accessibility-constraints)).

L'opzione facoltativa *constructor_initializer* specifica un altro costruttore di istanza per richiamare prima di eseguire le istruzioni fornite nella *constructor_body* di questo costruttore di istanza. Questo è descritto più dettagliatamente in [inizializzatori del costruttore](classes.md#constructor-initializers).

Quando una dichiarazione di costruttore include un `extern` modificatore, il costruttore viene definito un ***costruttore esterno***. Poiché una dichiarazione di costruttore esterni non fornisce alcuna implementazione effettiva, relativi *constructor_body* è costituito da un punto e virgola. Per tutti gli altri costruttori, il *constructor_body* costituito da un *blocco* che consente di specificare le istruzioni per inizializzare una nuova istanza della classe. Questo corrisponde esattamente al *block* di un metodo di istanza con un `void` tipo restituito ([corpo del metodo](classes.md#method-body)).

Costruttori di istanza non vengono ereditati. Di conseguenza, una classe non dispone di alcun costruttore di istanza diversi da quelli effettivamente dichiarati nella classe. Se una classe non contiene alcuna dichiarazione di costruttore di istanza, viene fornito automaticamente un costruttore di istanza predefinito ([costruttori predefiniti](classes.md#default-constructors)).

Costruttori di istanza vengono richiamati dal *object_creation_expression*s ([espressioni per la creazione dell'oggetto](expressions.md#object-creation-expressions)) e tramite *constructor_initializer*s.

### <a name="constructor-initializers"></a>Inizializzatori del costruttore

Tutti i costruttori di istanza (ad eccezione di quelle per la classe `object`) includono in modo implicito una chiamata di un altro costruttore di istanza immediatamente prima di *constructor_body*. Il costruttore da richiamare in modo implicito è determinato dal *constructor_initializer*:

*  Un inizializzatore del costruttore di istanza nel formato `base(argument_list)` o `base()` provoca un costruttore di istanza dalla classe di base diretta da richiamare. Tale costruttore viene selezionato mediante *argument_list* se presente e le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution). Il set di costruttori di istanza è costituito da tutti i costruttori di istanza accessibile contenuti nella classe di base diretta o il costruttore predefinito ([costruttori predefiniti](classes.md#default-constructors)), se viene dichiarato alcun costruttore di istanza di classe di base diretta. Se questo set è vuoto o se non è possibile identificare un costruttore di istanza migliore, si verifica un errore in fase di compilazione.
*  Un inizializzatore del costruttore di istanza nel formato `this(argument-list)` o `this()` provoca un costruttore di istanza della classe stessa da richiamare. Il costruttore viene selezionato mediante *argument_list* se presente e le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution). Il set di costruttori di istanza è costituito da tutti i costruttori di istanza accessibile dichiarati nella classe stessa. Se questo set è vuoto o se non è possibile identificare un costruttore di istanza migliore, si verifica un errore in fase di compilazione. Se una dichiarazione di costruttore di istanza include un inizializzatore del costruttore che richiama il costruttore stesso, si verifica un errore in fase di compilazione.

Se un costruttore di istanza non include alcun inizializzatore di costruttore, un inizializzatore del costruttore del form `base()` viene fornito in modo implicito. Di conseguenza, una dichiarazione di costruttore di istanza nel formato
```csharp
C(...) {...}
```
è perfettamente equivalente alla
```csharp
C(...): base() {...}
```

L'ambito dei parametri, dati per il *formal_parameter_list* di un costruttore di istanza dichiarazione include l'inizializzatore del costruttore di tale dichiarazione. Di conseguenza, un inizializzatore del costruttore è consentito accedere ai parametri del costruttore. Ad esempio:
```csharp
class A
{
    public A(int x, int y) {}
}

class B: A
{
    public B(int x, int y): base(x + y, x - y) {}
}
```

Un inizializzatore di costruttore di istanza non è possibile accedere all'istanza da creare. È pertanto un errore in fase di compilazione per fare riferimento a `this` in un'espressione dell'inizializzatore del costruttore dell'argomento come perché è un errore in fase di compilazione per un'espressione dell'argomento fare riferimento a un membro di istanza tramite un *simple_name*.

### <a name="instance-variable-initializers"></a>Inizializzatori di variabili di istanza

Quando un costruttore di istanza non include alcun inizializzatore di costruttore o è in un inizializzatore del costruttore del form `base(...)`, tale costruttore esegue in modo implicito le inizializzazioni specificate per il *variable_initializer*s di i campi di istanza dichiarato nella relativa classe. Ciò corrisponde a una sequenza di assegnazioni che vengono eseguite immediatamente al momento dell'accesso al costruttore e prima della chiamata del costruttore diretto della classe base implicita. Inizializzatori vengono eseguiti nell'ordine testuale in cui appaiono nella dichiarazione di classe.

### <a name="constructor-execution"></a>Esecuzione del costruttore

Gli inizializzatori di variabili vengono trasformati in istruzioni di assegnazione e queste istruzioni di assegnazione vengono eseguite prima della chiamata del costruttore di istanza della classe base. Questo ordinamento garantisce che tutti i campi di istanza vengono inizializzati dai relativi inizializzatori prima dell'esecuzione di qualsiasi istruzione che ha accesso a tale istanza.

Dato l'esempio
```csharp
using System;

class A
{
    public A() {
        PrintFields();
    }

    public virtual void PrintFields() {}
}

class B: A
{
    int x = 1;
    int y;

    public B() {
        y = -1;
    }

    public override void PrintFields() {
        Console.WriteLine("x = {0}, y = {1}", x, y);
    }
}
```
Quando `new B()` viene usato per creare un'istanza di `B`, viene prodotto l'output seguente:
```
x = 1, y = 0
```

Il valore di `x` è 1, perché l'inizializzatore di variabile viene eseguita prima che venga richiamato il costruttore della classe base. Tuttavia, il valore di `y` è 0 (il valore predefinito di un `int`) perché l'assegnazione al `y` non viene eseguita fino al termine il costruttore di classe di base.

È utile considerare gli inizializzatori di variabili di istanza e inizializzatori del costruttore come le istruzioni che vengono inserite automaticamente prima la *constructor_body*. L'esempio
```csharp
using System;
using System.Collections;

class A
{
    int x = 1, y = -1, count;

    public A() {
        count = 0;
    }

    public A(int n) {
        count = n;
    }
}

class B: A
{
    double sqrt2 = Math.Sqrt(2.0);
    ArrayList items = new ArrayList(100);
    int max;

    public B(): this(100) {
        items.Add("default");
    }

    public B(int n): base(n - 1) {
        max = n;
    }
}
```
contiene gli inizializzatori di variabili diversi; contiene inoltre inizializzatori del costruttore di entrambi i formati (`base` e `this`). Nell'esempio corrisponde al codice riportato di seguito, dove ogni commento indica un'istruzione inserita automaticamente (la sintassi utilizzata per le chiamate al costruttore automaticamente inserito non è valido, ma serve semplicemente a illustrare il meccanismo).

```csharp
using System.Collections;

class A
{
    int x, y, count;

    public A() {
        x = 1;                       // Variable initializer
        y = -1;                      // Variable initializer
        object();                    // Invoke object() constructor
        count = 0;
    }

    public A(int n) {
        x = 1;                       // Variable initializer
        y = -1;                      // Variable initializer
        object();                    // Invoke object() constructor
        count = n;
    }
}

class B: A
{
    double sqrt2;
    ArrayList items;
    int max;

    public B(): this(100) {
        B(100);                      // Invoke B(int) constructor
        items.Add("default");
    }

    public B(int n): base(n - 1) {
        sqrt2 = Math.Sqrt(2.0);      // Variable initializer
        items = new ArrayList(100);  // Variable initializer
        A(n - 1);                    // Invoke A(int) constructor
        max = n;
    }
}
```

### <a name="default-constructors"></a>Costruttori predefiniti

Se una classe non contiene alcuna dichiarazione di costruttore di istanza, viene fornito automaticamente un costruttore di istanza predefinito. Tale costruttore predefinito richiama semplicemente il costruttore senza parametri della classe di base diretta. Se la classe è astratta è protetta l'accessibilità dichiarata per il costruttore predefinito. In caso contrario, l'accessibilità dichiarata per il costruttore predefinito è pubblico. Di conseguenza, il costruttore predefinito è sempre del form

```csharp
protected C(): base() {}
```
oppure
```csharp
public C(): base() {}
```
in cui `C` è il nome della classe. Se la risoluzione dell'overload non riesce a determinare un candidato migliore univoco per l'inizializzatore del costruttore di classe di base verrà generato un errore in fase di compilazione.

Nell'esempio
```csharp
class Message
{
    object sender;
    string text;
}
```
un costruttore predefinito viene fornito perché la classe non contiene alcuna dichiarazione di costruttore di istanza. Di conseguenza, l'esempio è esattamente equivalente a
```csharp
class Message
{
    object sender;
    string text;

    public Message(): base() {}
}
```

### <a name="private-constructors"></a>Costruttori privati

Quando una classe `T` dichiara solo costruttori di istanza privata, non è possibile che per le classi all'esterno del testo di programma del `T` da cui derivare `T` o a creare direttamente istanze di `T`. Di conseguenza, se una classe contiene solo membri statici e non può essere creata un'istanza, l'aggiunta di un costruttore di istanza privata vuota impedirà la creazione di istanze. Ad esempio:
```csharp
public class Trig
{
    private Trig() {}        // Prevent instantiation

    public const double PI = 3.14159265358979323846;

    public static double Sin(double x) {...}
    public static double Cos(double x) {...}
    public static double Tan(double x) {...}
}
```

Il `Trig` classe gruppi costanti e metodi correlati, ma non è possibile creare un'istanza. Di conseguenza dichiara un costruttore di istanza singola di privato vuoto. Costruttore almeno un'istanza deve essere dichiarato per eliminare la generazione automatica di un costruttore predefinito.

### <a name="optional-instance-constructor-parameters"></a>Parametri del costruttore istanza facoltativa

Il `this(...)` un inizializzatore di costruttore viene comunemente utilizzato in combinazione con l'overload per implementare i parametri di costruttore di istanza facoltativo. Nell'esempio
```csharp
class Text
{
    public Text(): this(0, 0, null) {}

    public Text(int x, int y): this(x, y, null) {}

    public Text(int x, int y, string s) {
        // Actual constructor implementation
    }
}
```
i costruttori di due istanza prima forniscono semplicemente i valori predefiniti per gli argomenti mancanti. Entrambi usano un `this(...)` inizializzatore del costruttore da richiamare il terzo costruttore di istanza, che effettivamente esegue le operazioni di inizializzazione dell'istanza nuova. L'effetto è quello di parametri del costruttore facoltativo:
```csharp
Text t1 = new Text();                    // Same as Text(0, 0, null)
Text t2 = new Text(5, 10);               // Same as Text(5, 10, null)
Text t3 = new Text(5, 20, "Hello");
```

## <a name="static-constructors"></a>Costruttori statici

Oggetto ***costruttore statico*** è un membro che implementa le azioni necessarie per inizializzare un tipo di classe chiuso. I costruttori statici vengono dichiarati usando *static_constructor_declaration*s:

```antlr
static_constructor_declaration
    : attributes? static_constructor_modifiers identifier '(' ')' static_constructor_body
    ;

static_constructor_modifiers
    : 'extern'? 'static'
    | 'static' 'extern'?
    | static_constructor_modifiers_unsafe
    ;

static_constructor_body
    : block
    | ';'
    ;
```

Oggetto *static_constructor_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e un `extern` modificatore ([metodi esterni](classes.md#external-methods)).

Il *identifier* di un *static_constructor_declaration* deve denominare la classe in cui viene dichiarato il costruttore statico. Se viene specificato un altro nome, si verifica un errore in fase di compilazione.

Quando una dichiarazione di costruttore statico include un' `extern` modificatore, il costruttore statico viene definito un ***costruttore statico esterno***. Poiché una dichiarazione di costruttore statico esterni non fornisce alcuna implementazione effettiva, relativi *static_constructor_body* è costituito da un punto e virgola. Per tutte le altre dichiarazioni di costruttore statico, il *static_constructor_body* costituito da un *blocco* che consente di specificare le istruzioni da eseguire per inizializzare la classe. Questo corrisponde esattamente al *method_body* di un metodo statico con un `void` tipo di valore restituito ([corpo del metodo](classes.md#method-body)).

I costruttori statici non vengono ereditati e non possono essere chiamati direttamente.

Il costruttore statico per un tipo di classe chiuso esegue al massimo una volta in un dominio applicazione specificato. L'esecuzione di un costruttore statico viene attivato dal primo degli eventi seguenti che si verificano all'interno di un dominio dell'applicazione:

*  Viene creata un'istanza del tipo di classe.
*  Qualsiasi membro statico del tipo di classe viene fatto riferimento.

Se una classe contiene il `Main` metodo ([avvio dell'applicazione](basic-concepts.md#application-startup)) in cui inizia l'esecuzione, il costruttore statico per tale classe viene eseguito prima il `Main` viene chiamato il metodo.

Per inizializzare un nuovo tipo di classe chiuso, prima di tutto un nuovo set di campi statici ([campi statici e di istanza](classes.md#static-and-instance-fields)) per quel particolare tipo di chiusura viene creato. Ogni campo statico viene inizializzato sul valore predefinito ([i valori predefiniti](variables.md#default-values)). Successivamente, gli inizializzatori di campo statico ([inizializzazione del campo statico](classes.md#static-field-initialization)) vengono eseguiti per i campi statici. Infine, viene eseguito il costruttore statico.

L'esempio
```csharp
using System;

class Test
{
    static void Main() {
        A.F();
        B.F();
    }
}

class A
{
    static A() {
        Console.WriteLine("Init A");
    }
    public static void F() {
        Console.WriteLine("A.F");
    }
}

class B
{
    static B() {
        Console.WriteLine("Init B");
    }
    public static void F() {
        Console.WriteLine("B.F");
    }
}
```
è necessario produrre l'output:
```
Init A
A.F
Init B
B.F
```
Poiché l'esecuzione di `A`del costruttore statico viene attivato dalla chiamata a `A.F`e l'esecuzione di `B`del costruttore statico viene attivato dalla chiamata al metodo `B.F`.

È possibile creare dipendenze circolari che consentono i campi statici con gli inizializzatori di variabili da osservare nel relativo stato di valore predefinito.

L'esempio
```csharp
using System;

class A
{
    public static int X;

    static A() {
        X = B.Y + 1;
    }
}

class B
{
    public static int Y = A.X + 1;

    static B() {}

    static void Main() {
        Console.WriteLine("X = {0}, Y = {1}", A.X, B.Y);
    }
}
```
produce l'output
```
X = 1, Y = 2
```

Per eseguire la `Main` metodo, il sistema esegue innanzitutto l'inizializzatore per `B.Y`, prima di classe `B`del costruttore statico. `Y`dell'inizializzatore `A`del costruttore statico per essere eseguito perché il valore di `A.X` viene fatto riferimento. Costruttore statico della `A` procede a sua volta per calcolare il valore di `X`e sulle operazioni di recupero in questo caso il valore predefinito di `Y`, che è uguale a zero. `A.X` In questo modo viene inizializzato su 1. Il processo di esecuzione `A`del costruttore statico e gli inizializzatori di campo statico, quindi viene completato, tornando al calcolo del valore iniziale di `Y`, il cui risultato diventa 2.

Poiché il costruttore statico viene eseguito una sola volta per ogni tipo classe costruito chiuso, è un modo pratico per applicare controlli di runtime nel parametro di tipo che non può essere controllato in fase di compilazione tramite i vincoli ([parametro di tipo i vincoli](classes.md#type-parameter-constraints)). Ad esempio, il tipo seguente usa un costruttore statico per imporre che l'argomento tipo è un'enumerazione:
```csharp
class Gen<T> where T: struct
{
    static Gen() {
        if (!typeof(T).IsEnum) {
            throw new ArgumentException("T must be an enum");
        }
    }
}
```

## <a name="destructors"></a>Distruttori

Oggetto ***distruttore*** è un membro che implementa le azioni necessarie per distruggere un'istanza di una classe. Un distruttore viene dichiarato con un *destructor_declaration*:

```antlr
destructor_declaration
    : attributes? 'extern'? '~' identifier '(' ')' destructor_body
    | destructor_declaration_unsafe
    ;

destructor_body
    : block
    | ';'
    ;
```

Oggetto *destructor_declaration* può includere un set di *attributi* ([attributi](attributes.md)).

Il *identifier* di un *destructor_declaration* deve denominare la classe in cui il distruttore viene dichiarato. Se viene specificato un altro nome, si verifica un errore in fase di compilazione.

Quando una dichiarazione di un distruttore include un' `extern` modificatore, il distruttore viene definito un' ***distruttore esterno***. Poiché una dichiarazione esterna distruttore non fornisce alcuna implementazione effettiva, relativi *destructor_body* è costituito da un punto e virgola. Per tutti gli altri distruttori, il *destructor_body* costituito da un *blocco* che consente di specificare le istruzioni da eseguire per distruggere un'istanza della classe. Oggetto *destructor_body* corrispondono esattamente al *method_body* di un metodo di istanza con un `void` tipo restituito ([corpo del metodo](classes.md#method-body)).

I distruttori non vengono ereditati. Di conseguenza, una classe non dispone di alcun distruttore diverso da quello che può essere dichiarato nella classe.

Poiché un distruttore deve essere priva di parametri, non può essere sottoposto a overload, in modo che una classe può avere al massimo un distruttore.

I distruttori vengono chiamati automaticamente e non possono essere richiamati in modo esplicito. Un'istanza diventa idonea per l'eliminazione quando non è più possibile per il codice utilizzare tale istanza. L'esecuzione del distruttore per l'istanza può verificarsi in qualsiasi momento dopo l'istanza diventa idoneo per l'eliminazione. Quando viene eliminata un'istanza, vengono chiamati i distruttori nella catena di ereditarietà dell'istanza, in ordine, dalla maggior parte derivata alla meno derivata. Un distruttore può essere eseguito su qualsiasi thread. Per ulteriori informazioni sulle regole che determinano quando e come viene eseguito un distruttore, vedere [gestione automatica della memoria](basic-concepts.md#automatic-memory-management).

L'output dell'esempio
```csharp
using System;

class A
{
    ~A() {
        Console.WriteLine("A's destructor");
    }
}

class B: A
{
    ~B() {
        Console.WriteLine("B's destructor");
    }
}

class Test
{
   static void Main() {
        B b = new B();
        b = null;
        GC.Collect();
        GC.WaitForPendingFinalizers();
   }
}
```
is
```
B's destructor
A's destructor
```
Poiché i distruttori in una catena di ereditarietà vengono chiamati in ordine, dalla maggior parte derivata alla meno derivata.

Eseguendo l'override del metodo virtuale vengono implementati i distruttori `Finalize` su `System.Object`. I programmi c# non sono autorizzati a eseguire l'override di questo metodo oppure chiamare (o le sostituzioni di esso) direttamente. Ad esempio, il programma
```csharp
class A 
{
    override protected void Finalize() {}    // error

    public void F() {
        this.Finalize();                     // error
    }
}
```
contiene due errori.

Il compilatore si comporta come se questo metodo ed esegue l'override, non esistono affatto. Di conseguenza, questo programma:
```csharp
class A 
{
    void Finalize() {}                            // permitted
}
```
è valido, e il metodo illustrato nasconde `System.Object`del `Finalize` (metodo).

Per una descrizione del comportamento quando viene generata un'eccezione da un distruttore, vedere [gestione delle eccezioni](exceptions.md#how-exceptions-are-handled).

## <a name="iterators"></a>Iterators

Un membro di funzione ([membri funzione](expressions.md#function-members)) implementata usando un blocco iteratore ([blocchi](statements.md#blocks)) viene chiamato un ***iteratore***.

Un blocco iteratore può essere usato come il corpo di un membro di funzione, purché il tipo restituito del membro di funzione corrispondente è una delle interfacce enumeratore ([enumeratore (interfacce)](classes.md#enumerator-interfaces)) o una delle interfacce enumerabili ([Interfacce enumerabili](classes.md#enumerable-interfaces)). Può verificarsi come un *method_body*, *operator_body* oppure *accessor_body*, mentre gli eventi, costruttori di istanze, i costruttori statici e i distruttori non possono essere implementate come iteratori.

Quando un membro di funzione viene implementato usando un blocco iteratore, è un errore in fase di compilazione per l'elenco di parametri formali del membro funzione per specificare eventuali `ref` o `out` parametri.

### <a name="enumerator-interfaces"></a>Enumeratore (interfacce)

Il ***interfacce dell'enumeratore*** rappresentano l'interfaccia non generica `System.Collections.IEnumerator` e tutte le istanze dell'interfaccia generica `System.Collections.Generic.IEnumerator<T>`. Per ragioni di brevità, in questo capitolo queste interfacce vengono fatto riferimento come `IEnumerator` e `IEnumerator<T>`, rispettivamente.

### <a name="enumerable-interfaces"></a>Interfacce enumerabili

Il ***interfacce enumerabili*** rappresentano l'interfaccia non generica `System.Collections.IEnumerable` e tutte le istanze dell'interfaccia generica `System.Collections.Generic.IEnumerable<T>`. Per ragioni di brevità, in questo capitolo queste interfacce vengono fatto riferimento come `IEnumerable` e `IEnumerable<T>`, rispettivamente.

### <a name="yield-type"></a>Tipo yield

Un iteratore produce una sequenza di valori, tutti dello stesso tipo. Questo tipo è definito il ***yield tipo*** dell'iteratore.

*  Il tipo di rendimento di un iteratore che restituisce `IEnumerator` oppure `IEnumerable` è `object`.
*  Il tipo di rendimento di un iteratore che restituisce `IEnumerator<T>` oppure `IEnumerable<T>` è `T`.

### <a name="enumerator-objects"></a>Oggetti dell'enumeratore

Quando un membro di funzione che restituisce un enumeratore tipo di interfaccia viene implementato usando un blocco iteratore, richiamare il membro di funzione non eseguire immediatamente il codice nel blocco iteratore. Al contrario, un ***oggetto enumerator*** viene creata e restituita. Questo oggetto incapsula il codice nel blocco iteratore specificato e l'esecuzione del codice nel blocco iteratore si verifica quando l'oggetto enumeratore `MoveNext` metodo viene richiamato. Oggetto enumeratore ha le caratteristiche seguenti:

*  Implementa `IEnumerator` e `IEnumerator<T>`, dove `T` è il tipo di rendimento dell'iteratore.
*  Implementa `System.IDisposable`.
*  Viene inizializzato con una copia dei valori di argomento (se presente) e valore dell'istanza passato per il membro di funzione.
*  Ha quattro diversi stati: ***prima***, ***in esecuzione***, ***sospeso***, e ***dopo***e si trova inizialmente nel ***prima***  dello stato.

Oggetto enumeratore è in genere un'istanza di una classe enumerator generato dal compilatore che incapsula il codice nel blocco iteratore e implementa le interfacce dell'enumeratore, ma sono possibili altri metodi di implementazione. Se una classe enumerator viene generata dal compilatore, tale classe verrà nidificata, direttamente o indirettamente, nella classe che contiene il membro di funzione, avrà l'accessibilità privata e avrà un nome riservato per l'utilizzo del compilatore ([identificatori ](lexical-structure.md#identifiers)).

Oggetto enumeratore può implementare più interfacce rispetto a quelle specificate in precedenza.

Le sezioni seguenti descrivono il comportamento esatto della `MoveNext`, `Current`, e `Dispose` membri del `IEnumerable` e `IEnumerable<T>` implementazioni fornite da un oggetto enumeratore dell'interfaccia.

Si noti che non supportano oggetti dell'enumeratore di `IEnumerator.Reset` (metodo). Richiamando questo metodo un `System.NotSupportedException` generata.

#### <a name="the-movenext-method"></a>Il metodo MoveNext

Il `MoveNext` metodo dell'oggetto enumeratore incapsula il codice di un blocco iteratore. Richiamare il `MoveNext` metodo esegue codice nel blocco iteratore e imposta il `Current` proprietà dell'oggetto enumeratore come appropriato. L'azione specifica eseguita dal `MoveNext` dipende dallo stato dell'oggetto enumeratore quando `MoveNext` viene richiamato:

*  Se lo stato dell'oggetto enumeratore ***prima***, la chiamata `MoveNext`:
   * Imposta lo stato su ***in esecuzione***.
   * Inizializza i parametri (tra cui `this`) del blocco di iteratore per i valori degli argomenti e il valore di istanza salvato quando l'oggetto enumeratore è stato inizializzato.
   * Esegue il blocco iteratore dall'inizio fino a quando non viene interrotta l'esecuzione (come descritto di seguito).
*  Se lo stato dell'oggetto enumeratore ***in esecuzione***, il risultato ottenuto richiamando `MoveNext` non è specificato.
*  Se lo stato dell'oggetto enumeratore ***sospesa***, la chiamata `MoveNext`:
   * Imposta lo stato su ***in esecuzione***.
   * Ripristina i valori di tutte le variabili e parametri locali (incluso questo) per i valori salvati durante l'ultima sospensione dell'esecuzione del blocco iteratore. Si noti che il contenuto di tutti gli oggetti cui fa riferimento a queste variabili hanno modificato dalla chiamata precedente a MoveNext.
   * Riprende l'esecuzione del blocco iteratore subito dopo il `yield return` istruzione che ha causato la sospensione dell'esecuzione e continua fino a quando l'esecuzione viene interrotta (come descritto di seguito).
*  Se lo stato dell'oggetto enumeratore ***dopo aver***, la chiamata `MoveNext` restituisce `false`.


Quando `MoveNext` esegue il blocco di iteratore, l'esecuzione può essere interrotta in quattro modi: da un `yield return` istruzione, da un `yield break` istruzione, rilevando la fine del blocco iteratore e da un'eccezione generata e propagata fuori il blocco iteratore.

*  Quando un `yield return` viene rilevata un'istruzione ([l'istruzione yield](statements.md#the-yield-statement)):
   * L'espressione specificata nell'istruzione viene valutata in modo implicito convertito nel tipo di yield e assegnato al `Current` proprietà dell'oggetto enumeratore.
   * L'esecuzione del corpo dell'iteratore viene sospesa. I valori di tutti i parametri e variabili locali (incluso `this`) vengono salvate, nonché la posizione di questo `yield return` istruzione. Se il `yield return` istruzione è all'interno di uno o più `try` blocca, associato `finally` blocchi non vengono eseguiti in questo momento.
   * Lo stato dell'oggetto enumeratore è impostato su ***sospeso***.
   * Il `MoveNext` restituzione del metodo `true` al relativo chiamante, che indica che l'iterazione viene spostato il valore successivo.
*  Quando un `yield break` viene rilevata un'istruzione ([l'istruzione yield](statements.md#the-yield-statement)):
   * Se il `yield break` istruzione è all'interno di uno o più `try` blocca, associato `finally` vengono eseguiti blocchi.
   * Lo stato dell'oggetto enumeratore è impostato su ***dopo aver***.
   * Il `MoveNext` restituzione del metodo `false` al relativo chiamante, che indica che l'iterazione è stata completata.
*  Quando viene rilevata la fine del corpo dell'iteratore:
   * Lo stato dell'oggetto enumeratore è impostato su ***dopo aver***.
   * Il `MoveNext` restituzione del metodo `false` al relativo chiamante, che indica che l'iterazione è stata completata.
*  Quando un'eccezione viene generata un'eccezione e propagata fuori dal blocco di iteratori:
   * Appropriato `finally` blocchi nel corpo dell'iteratore vengono eseguiti mediante la propagazione dell'eccezione.
   * Lo stato dell'oggetto enumeratore è impostato su ***dopo aver***.
   * Continua la propagazione dell'eccezione al chiamante del `MoveNext` (metodo).

#### <a name="the-current-property"></a>La proprietà corrente

Oggetto enumeratore `Current` proprietà dipende dal `yield return` istruzioni nel blocco iteratore.

Quando un oggetto enumeratore è nel ***sospesa*** lo stato, il valore di `Current` corrisponde al valore impostato dalla chiamata precedente a `MoveNext`. Quando un oggetto enumeratore è nel ***prima***, ***che esegue***, o ***dopo*** indicato, il risultato dell'accesso `Current` non è specificato.

Per un iteratore con una parola chiave yield tipo diverso da `object`, il risultato dell'accesso `Current` tramite l'oggetto enumeratore `IEnumerable` implementazione corrisponde all'accesso `Current` tramite l'oggetto enumeratore `IEnumerator<T>` implementazione e il cast del risultato a `object`.

#### <a name="the-dispose-method"></a>Il metodo Dispose

Il `Dispose` metodo viene utilizzato per pulire l'iterazione portando l'oggetto enumeratore il ***dopo*** dello stato.

*  Se lo stato dell'oggetto enumeratore ***prima***, la chiamata `Dispose` imposta lo stato su ***dopo***.
*  Se lo stato dell'oggetto enumeratore ***in esecuzione***, il risultato ottenuto richiamando `Dispose` non è specificato.
*  Se lo stato dell'oggetto enumeratore ***sospesa***, la chiamata `Dispose`:
   * Imposta lo stato su ***in esecuzione***.
   * Esegue qualsiasi infine blocchi come se l'ultima esecuzione `yield return` istruzione sono stati un `yield break` istruzione. Se questo genera un'eccezione generata e propagato fuori dal corpo dell'iteratore, lo stato dell'oggetto enumeratore è impostato su ***dopo aver*** e l'eccezione viene propagata al chiamante del `Dispose` (metodo).
   * Imposta lo stato su ***dopo aver***.
*  Se lo stato dell'oggetto enumeratore ***dopo aver***, la chiamata `Dispose` non ha alcun effetto.

### <a name="enumerable-objects"></a>Oggetti enumerabili

Quando un membro di funzione che restituisce un tipo enumerabile interfaccia viene implementato usando un blocco iteratore, richiamare il membro di funzione non eseguire immediatamente il codice nel blocco iteratore. Al contrario, un ***oggetto enumerabile*** viene creata e restituita. L'oggetto enumerabile `GetEnumerator` metodo viene restituito un oggetto enumeratore che incapsula il codice specificato nel blocco iteratore e si verifica l'esecuzione del codice nel blocco iteratore quando l'oggetto enumeratore `MoveNext` metodo viene richiamato. Oggetto enumerabile presenta le caratteristiche seguenti:

*  Implementa `IEnumerable` e `IEnumerable<T>`, dove `T` è il tipo di rendimento dell'iteratore.
*  Viene inizializzato con una copia dei valori di argomento (se presente) e valore dell'istanza passato per il membro di funzione.

Oggetto enumerabile in genere è un'istanza della classe enumerable generato dal compilatore che incapsula il codice nel blocco iteratore e implementa le interfacce enumerabili, ma sono possibili altri metodi di implementazione. Se una classe enumerable viene generata dal compilatore, tale classe verrà nidificata, direttamente o indirettamente, nella classe che contiene il membro di funzione, avrà l'accessibilità privata e avrà un nome riservato per l'utilizzo del compilatore ([identificatori ](lexical-structure.md#identifiers)).

Oggetto enumerabile può implementare più interfacce rispetto a quelle specificate in precedenza. In particolare, un oggetto enumerabile può anche implementare `IEnumerator` e `IEnumerator<T>`, in modo da poter essere utilizzato come un oggetto enumerabile sia un enumeratore. In tale tipo di implementazione, la prima volta che un oggetto enumerabile `GetEnumerator` metodo viene richiamato, viene restituito l'oggetto enumerabile stesso. Le chiamate successive degli oggetti enumerabili `GetEnumerator`, se presente, restituisce una copia dell'oggetto enumerabile. Di conseguenza, ogni oggetto restituito enumeratore ha il proprio stato e le modifiche in un enumeratore non influiranno un'altra.

#### <a name="the-getenumerator-method"></a>Metodo GetEnumerator

Oggetto enumerabile fornisce un'implementazione del `GetEnumerator` metodi del `IEnumerable` e `IEnumerable<T>` interfacce. I due `GetEnumerator` metodi condividono un'implementazione comune che consente di acquisire e restituisce un oggetto enumeratore disponibili. Oggetto enumeratore viene inizializzata con i valori degli argomenti e l'istanza valore salvato quando l'oggetto enumerabile è stato inizializzato, ma in caso contrario, il resto funziona come descritto in [oggetti dell'enumeratore](classes.md#enumerator-objects).

### <a name="implementation-example"></a>Esempio di implementazione

Questa sezione illustra una possibile implementazione degli iteratori in termini di costrutti c# standard. L'implementazione descritta di seguito si basa sugli stessi principi usati dal compilatore Microsoft C#, ma è in alcun modo un'implementazione obbligatoria o i possibili solo una.

Quanto segue `Stack<T>` classe implementa il `GetEnumerator` metodo mediante un iteratore. L'iteratore enumera gli elementi dello stack dall'alto verso il basso.

```csharp
using System;
using System.Collections;
using System.Collections.Generic;

class Stack<T>: IEnumerable<T>
{
    T[] items;
    int count;

    public void Push(T item) {
        if (items == null) {
            items = new T[4];
        }
        else if (items.Length == count) {
            T[] newItems = new T[count * 2];
            Array.Copy(items, 0, newItems, 0, count);
            items = newItems;
        }
        items[count++] = item;
    }

    public T Pop() {
        T result = items[--count];
        items[count] = default(T);
        return result;
    }

    public IEnumerator<T> GetEnumerator() {
        for (int i = count - 1; i >= 0; --i) yield return items[i];
    }
}
```

Il `GetEnumerator` metodo può essere convertito in un'istanza di una classe enumerator generato dal compilatore che incapsula il codice nel blocco iteratore, come illustrato nell'esempio seguente.

```csharp
class Stack<T>: IEnumerable<T>
{
    ...

    public IEnumerator<T> GetEnumerator() {
        return new __Enumerator1(this);
    }

    class __Enumerator1: IEnumerator<T>, IEnumerator
    {
        int __state;
        T __current;
        Stack<T> __this;
        int i;

        public __Enumerator1(Stack<T> __this) {
            this.__this = __this;
        }

        public T Current {
            get { return __current; }
        }

        object IEnumerator.Current {
            get { return __current; }
        }

        public bool MoveNext() {
            switch (__state) {
                case 1: goto __state1;
                case 2: goto __state2;
            }
            i = __this.count - 1;
        __loop:
            if (i < 0) goto __state2;
            __current = __this.items[i];
            __state = 1;
            return true;
        __state1:
            --i;
            goto __loop;
        __state2:
            __state = 2;
            return false;
        }

        public void Dispose() {
            __state = 2;
        }

        void IEnumerator.Reset() {
            throw new NotSupportedException();
        }
    }
}
```

Nella conversione precedente, il codice nel blocco iteratore viene trasformato in una macchina a stati e inserito nel `MoveNext` metodo della classe dell'enumeratore. Inoltre, la variabile locale `i` viene trasformato in un campo dell'oggetto enumeratore per continuare a esistere tra le chiamate di `MoveNext`.

L'esempio seguente visualizza una semplice tabella moltiplicazione dei numeri interi da 1 a 10. Il `FromTo` metodo nell'esempio restituisce un oggetto enumerabile e viene implementata utilizzando un iteratore.

```csharp
using System;
using System.Collections.Generic;

class Test
{
    static IEnumerable<int> FromTo(int from, int to) {
        while (from <= to) yield return from++;
    }

    static void Main() {
        IEnumerable<int> e = FromTo(1, 10);
        foreach (int x in e) {
            foreach (int y in e) {
                Console.Write("{0,3} ", x * y);
            }
            Console.WriteLine();
        }
    }
}
```

Il `FromTo` metodo può essere convertito in un'istanza della classe enumerable generato dal compilatore che incapsula il codice nel blocco iteratore, come illustrato nell'esempio seguente.

```csharp
using System;
using System.Threading;
using System.Collections;
using System.Collections.Generic;

class Test
{
    ...

    static IEnumerable<int> FromTo(int from, int to) {
        return new __Enumerable1(from, to);
    }

    class __Enumerable1:
        IEnumerable<int>, IEnumerable,
        IEnumerator<int>, IEnumerator
    {
        int __state;
        int __current;
        int __from;
        int from;
        int to;
        int i;

        public __Enumerable1(int __from, int to) {
            this.__from = __from;
            this.to = to;
        }

        public IEnumerator<int> GetEnumerator() {
            __Enumerable1 result = this;
            if (Interlocked.CompareExchange(ref __state, 1, 0) != 0) {
                result = new __Enumerable1(__from, to);
                result.__state = 1;
            }
            result.from = result.__from;
            return result;
        }

        IEnumerator IEnumerable.GetEnumerator() {
            return (IEnumerator)GetEnumerator();
        }

        public int Current {
            get { return __current; }
        }

        object IEnumerator.Current {
            get { return __current; }
        }

        public bool MoveNext() {
            switch (__state) {
            case 1:
                if (from > to) goto case 2;
                __current = from++;
                __state = 1;
                return true;
            case 2:
                __state = 2;
                return false;
            default:
                throw new InvalidOperationException();
            }
        }

        public void Dispose() {
            __state = 2;
        }

        void IEnumerator.Reset() {
            throw new NotSupportedException();
        }
    }
}
```

La classe enumerable implementa le interfacce enumerabili sia le interfacce enumeratore, in modo da poter essere utilizzato come un oggetto enumerabile sia un enumeratore. La prima volta il `GetEnumerator` metodo viene richiamato, viene restituito l'oggetto enumerabile stesso. Le chiamate successive degli oggetti enumerabili `GetEnumerator`, se presente, restituisce una copia dell'oggetto enumerabile. Di conseguenza, ogni oggetto restituito enumeratore ha il proprio stato e le modifiche in un enumeratore non influiranno un'altra. Il `Interlocked.CompareExchange` metodo viene utilizzato per garantire operazioni thread-safe.

Il `from` e `to` parametri vengono convertiti in campi della classe enumerable. In quanto `from` viene modificato nel blocco iteratore, un'ulteriore `__from` campo è stato introdotto per contenere il valore iniziale assegnato `from` in ogni enumeratore.

Il `MoveNext` metodo genera un' `InvalidOperationException` se viene chiamato quando `__state` è `0`. Ciò consente di evitare l'utilizzo dell'oggetto enumerabile come un oggetto enumeratore senza prima chiamare `GetEnumerator`.

Nell'esempio seguente viene illustrata una classe di albero semplice. Il `Tree<T>` classe implementa il `GetEnumerator` metodo mediante un iteratore. L'iteratore enumera gli elementi dell'albero in ordine di infissi.

```csharp
using System;
using System.Collections.Generic;

class Tree<T>: IEnumerable<T>
{
    T value;
    Tree<T> left;
    Tree<T> right;

    public Tree(T value, Tree<T> left, Tree<T> right) {
        this.value = value;
        this.left = left;
        this.right = right;
    }

    public IEnumerator<T> GetEnumerator() {
        if (left != null) foreach (T x in left) yield x;
        yield value;
        if (right != null) foreach (T x in right) yield x;
    }
}

class Program
{
    static Tree<T> MakeTree<T>(T[] items, int left, int right) {
        if (left > right) return null;
        int i = (left + right) / 2;
        return new Tree<T>(items[i], 
            MakeTree(items, left, i - 1),
            MakeTree(items, i + 1, right));
    }

    static Tree<T> MakeTree<T>(params T[] items) {
        return MakeTree(items, 0, items.Length - 1);
    }

    // The output of the program is:
    // 1 2 3 4 5 6 7 8 9
    // Mon Tue Wed Thu Fri Sat Sun

    static void Main() {
        Tree<int> ints = MakeTree(1, 2, 3, 4, 5, 6, 7, 8, 9);
        foreach (int i in ints) Console.Write("{0} ", i);
        Console.WriteLine();

        Tree<string> strings = MakeTree(
            "Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun");
        foreach (string s in strings) Console.Write("{0} ", s);
        Console.WriteLine();
    }
}
```

Il `GetEnumerator` metodo può essere convertito in un'istanza di una classe enumerator generato dal compilatore che incapsula il codice nel blocco iteratore, come illustrato nell'esempio seguente.

```csharp
class Tree<T>: IEnumerable<T>
{
    ...

    public IEnumerator<T> GetEnumerator() {
        return new __Enumerator1(this);
    }

    class __Enumerator1 : IEnumerator<T>, IEnumerator
    {
        Node<T> __this;
        IEnumerator<T> __left, __right;
        int __state;
        T __current;

        public __Enumerator1(Node<T> __this) {
            this.__this = __this;
        }

        public T Current {
            get { return __current; }
        }

        object IEnumerator.Current {
            get { return __current; }
        }

        public bool MoveNext() {
            try {
                switch (__state) {

                case 0:
                    __state = -1;
                    if (__this.left == null) goto __yield_value;
                    __left = __this.left.GetEnumerator();
                    goto case 1;

                case 1:
                    __state = -2;
                    if (!__left.MoveNext()) goto __left_dispose;
                    __current = __left.Current;
                    __state = 1;
                    return true;

                __left_dispose:
                    __state = -1;
                    __left.Dispose();

                __yield_value:
                    __current = __this.value;
                    __state = 2;
                    return true;

                case 2:
                    __state = -1;
                    if (__this.right == null) goto __end;
                    __right = __this.right.GetEnumerator();
                    goto case 3;

                case 3:
                    __state = -3;
                    if (!__right.MoveNext()) goto __right_dispose;
                    __current = __right.Current;
                    __state = 3;
                    return true;

                __right_dispose:
                    __state = -1;
                    __right.Dispose();

                __end:
                    __state = 4;
                    break;

                }
            }
            finally {
                if (__state < 0) Dispose();
            }
            return false;
        }

        public void Dispose() {
            try {
                switch (__state) {

                case 1:
                case -2:
                    __left.Dispose();
                    break;

                case 3:
                case -3:
                    __right.Dispose();
                    break;

                }
            }
            finally {
                __state = 4;
            }
        }

        void IEnumerator.Reset() {
            throw new NotSupportedException();
        }
    }
}
```

I temporanei generati dal compilatore usati nel `foreach` istruzioni vengono eseguito il lift nel `__left` e `__right` dei campi dell'oggetto enumeratore. Il `__state` campo dell'oggetto enumeratore con attenzione viene aggiornato in modo che i valori corretti `Dispose()` metodo verrà chiamato correttamente se viene generata un'eccezione. Si noti che non è possibile scrivere il codice tradotto con semplice `foreach` istruzioni.

## <a name="async-functions"></a>Funzioni asincrone

Un metodo ([metodi](classes.md#methods)) o una funzione anonima ([espressioni di funzioni anonime](expressions.md#anonymous-function-expressions)) con i `async` modificatore viene chiamato un ***funzione async***. In generale, il termine ***async*** viene usato per descrivere qualsiasi tipo di funzione che ha il `async` modificatore.

È un errore in fase di compilazione per l'elenco di parametri formali di una funzione asincrona per specificare eventuali `ref` o `out` parametri.

Il *return_type* di un asincrono metodo deve essere `void` o una ***tipo di attività***. I tipi di attività sono `System.Threading.Tasks.Task` e i tipi costruiti da `System.Threading.Tasks.Task<T>`. Per ragioni di brevità, in questo capitolo questi tipi viene fatto riferimento come `Task` e `Task<T>`, rispettivamente. Un metodo asincrono che restituisce un tipo di attività è detto restituzione di task.

La definizione esatta dei tipi di attività non è definito dall'implementazione, ma dal punto di vista del linguaggio è un tipo di attività in uno stato incompleto, ha avuto esito positivo o faulted. Un'attività con errori registra un'eccezione pertinente. Una completato `Task<T>` registra un risultato di tipo `T`. I tipi di attività sono awaitable e quindi possono essere gli operandi di espressioni await ([espressioni Await](expressions.md#await-expressions)).

Una chiamata di funzione asincrona ha la possibilità di sospendere la valutazione di espressioni await ([espressioni Await](expressions.md#await-expressions)) nel relativo corpo. Valutazione in un secondo momento possa essere ripresi nel punto di sospensione per mezzo dell'espressione await una ***delegato ripresa***. Il delegato di ripresa è di tipo `System.Action`, e quando viene richiamato, valutazione della chiamata di funzione async verrà ripreso dall'espressione await in cui è stata interrotta. Il ***chiamante corrente*** di una funzione async chiamata è in caso contrario, il chiamante originale se la chiamata alla funzione non è mai stata sospesa o il più recente chiamante del delegato di ripresa.

### <a name="evaluation-of-a-task-returning-async-function"></a>Valutazione di una funzione async restituzione di task

Chiamata di una funzione asincrona che restituisce attività fa sì che un'istanza del tipo di attività restituita da generare. Questa operazione viene definita la ***Restituisci task*** della funzione async. L'attività è inizialmente in uno stato incompleto.

Il corpo della funzione async viene quindi valutato fino a quando non è sospeso (dal raggiungimento di un'espressione await) o viene terminato, in corrispondenza del quale punto di controllo viene restituito al chiamante, contestualmente all'attività restituito.

Quando termina, il corpo della funzione async viene restituito viene spostato lo stato incompleto:

*  Se il corpo della funzione termina come risultato di raggiungere un'istruzione return o alla fine del corpo, qualsiasi valore del risultato viene registrata nell'attività restituito, che viene inserito in un stato completato.
*  Se il corpo della funzione termina come risultato di un'eccezione non rilevata ([l'istruzione throw](statements.md#the-throw-statement)) l'eccezione viene registrata nell'attività restituito che viene inserito in uno stato di errore.

### <a name="evaluation-of-a-void-returning-async-function"></a>Valutazione di una funzione asincrona che restituisce void

Se il tipo restituito della funzione async `void`, la valutazione è diverso da quanto sopra nel modo seguente: perché non viene restituita alcuna attività, la funzione comunica invece completamento ed eccezioni nel thread corrente ***sincronizzazione contesto***. La definizione esatta del contesto di sincronizzazione è dipendente dall'implementazione, ma è una rappresentazione in forma di "where" è in esecuzione il thread corrente. Il contesto di sincronizzazione viene notificato quando la valutazione di una funzione asincrona che restituisce void avvia, viene completata correttamente o genera un'eccezione non rilevata eccezione.

In questo modo il contesto di tenere traccia di quante funzioni asincrone che restituiscono void sono in esecuzione e decidere come propagare le eccezioni provenienti da esse.
