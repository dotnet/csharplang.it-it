---
ms.openlocfilehash: e0def754174ab8646f9b849abe86d2c375c958b6
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703974"
---
# <a name="classes"></a>Classi

Una classe è una struttura di dati che può contenere membri dati (costanti e campi), membri di funzione (metodi, proprietà, eventi, indicizzatori, operatori, costruttori di istanze, distruttori e costruttori statici) e tipi annidati. I tipi di classe supportano l'ereditarietà, un meccanismo in base al quale una classe derivata può estendere ed specializzare una classe base.

## <a name="class-declarations"></a>Dichiarazioni di classe

Un *class_declaration* è un *type_declaration* ([dichiarazioni di tipo](namespaces.md#type-declarations)) che dichiara una nuova classe.

```antlr
class_declaration
    : attributes? class_modifier* 'partial'? 'class' identifier type_parameter_list?
      class_base? type_parameter_constraints_clause* class_body ';'?
    ;
```

Un *class_declaration* è costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), seguito da un set facoltativo di *class_modifier*s ([modificatori di classe](classes.md#class-modifiers)), seguito da un modificatore `partial` facoltativo, seguito dalla parola chiave `class` e un *identificatore* che denomina la classe, seguito da un *type_parameter_list* facoltativo ([parametri di tipo](classes.md#type-parameters)), seguito da una specifica *class_base* facoltativa (classe di[base della classe](classes.md#class-base-specification)), seguita da set facoltativo di *type_parameter_constraints_clause*s ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)), seguito da un *class_body* ([corpo della classe](classes.md#class-body)), facoltativamente seguito da un punto e virgola.

Una dichiarazione di classe non può fornire *type_parameter_constraints_clause*s a meno che non fornisca anche un *type_parameter_list*.

Una dichiarazione di classe che fornisce un *type_parameter_list* è una ***dichiarazione di classe generica***. Inoltre, qualsiasi classe annidata all'interno di una dichiarazione di classe generica o di una dichiarazione di struct generica è a sua volta una dichiarazione di classe generica, poiché è necessario fornire i parametri di tipo per il tipo contenitore per creare un tipo costruito.

### <a name="class-modifiers"></a>Modificatori di classe

Un *class_declaration* può facoltativamente includere una sequenza di modificatori di classe:

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

Si tratta di un errore in fase di compilazione perché lo stesso modificatore venga visualizzato più volte in una dichiarazione di classe.

Il `new` modificatore è consentito nelle classi annidate. Specifica che la classe nasconde un membro ereditato con lo stesso nome, come descritto nel [nuovo modificatore](classes.md#the-new-modifier). Si tratta di un errore in fase di compilazione `new` perché il modificatore venga visualizzato in una dichiarazione di classe che non è una dichiarazione di classe annidata.

I `public`modificatori `internal`, `protected`, e`private` controllano l'accessibilità della classe. A seconda del contesto in cui si verifica la dichiarazione di classe, alcuni modificatori potrebbero non essere consentiti ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)).

I `abstract`modificatori `static` , `sealed` e sono descritti nelle sezioni seguenti.

#### <a name="abstract-classes"></a>Classi astratte

Il `abstract` modificatore viene usato per indicare che una classe è incompleta e che deve essere usata solo come classe di base. Una classe astratta differisce da una classe non astratta nei modi seguenti:

*  Non è possibile creare direttamente un'istanza di una classe astratta e si tratta di un errore in fase di `new` compilazione per utilizzare l'operatore in una classe astratta. Sebbene sia possibile avere variabili e valori i cui tipi in fase di compilazione sono astratti, tali variabili e valori dovranno necessariamente essere `null` o contenere riferimenti a istanze di classi non astratte derivate dai tipi astratti.
*  Una classe astratta è consentita (ma non obbligatoria) per contenere membri astratti.
*  Una classe astratta non può essere sealed.

Quando una classe non astratta viene derivata da una classe astratta, la classe non astratta deve includere implementazioni effettive di tutti i membri astratti ereditati, ignorando in tal modo i membri astratti. Nell'esempio
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
la classe `A` astratta introduce un metodo `F`astratto. La `B` classe introduce un metodo `G`aggiuntivo, ma poiché non fornisce un'implementazione di `F`, `B` deve essere dichiarato anche abstract. La `C` classe `F` esegue l'override di e fornisce un'implementazione effettiva. Poiché non sono presenti membri astratti `C`in `C` , è consentito (ma non obbligatorio) non essere astratto.

#### <a name="sealed-classes"></a>Classi sealed

Il `sealed` modificatore viene usato per impedire la derivazione da una classe. Si verifica un errore in fase di compilazione se viene specificata una classe sealed come classe base di un'altra classe.

Una classe sealed non può essere anche una classe astratta.

Il `sealed` modificatore viene utilizzato principalmente per evitare derivazioni indesiderate, ma consente anche determinate ottimizzazioni in fase di esecuzione. In particolare, poiché una classe sealed non dispone mai di classi derivate, è possibile trasformare le chiamate del membro della funzione virtuale in istanze di classe sealed in chiamate non virtuali.

#### <a name="static-classes"></a>Classi statiche

Il `static` modificatore viene usato per contrassegnare la classe dichiarata come ***classe statica***. Non è possibile creare un'istanza di una classe statica. non può essere usata come tipo e può contenere solo membri statici. Solo una classe statica può contenere dichiarazioni di metodi di estensione ([metodi di estensione](classes.md#extension-methods)).

Una dichiarazione di classe statica è soggetta alle restrizioni seguenti:

*  Una classe statica non può includere un `sealed` modificatore o `abstract` . Si noti, tuttavia, che poiché una classe statica non può essere creata o derivata da, si comporta come se fosse sealed e abstract.
*  Una classe statica non può includere una specifica *class_base* ([specifica di base della classe](classes.md#class-base-specification)) e non può specificare in modo esplicito una classe base o un elenco di interfacce implementate. Una classe statica eredita in modo implicito `object`dal tipo.
*  Una classe statica può contenere solo membri statici ([membri statici e di istanza](classes.md#static-and-instance-members)). Si noti che le costanti e i tipi annidati sono classificati come membri statici.
*  Una classe statica non può avere membri `protected` con `protected internal` l'accessibilità o dichiarata.

Si tratta di un errore in fase di compilazione per violare una qualsiasi di queste restrizioni.

Una classe statica non ha costruttori di istanza. Non è possibile dichiarare un costruttore di istanza in una classe statica e non viene fornito alcun costruttore di istanza predefinito ([costruttori predefiniti](classes.md#default-constructors)) per una classe statica.

I membri di una classe statica non sono automaticamente statici e le dichiarazioni dei membri devono includere in modo esplicito `static` un modificatore (ad eccezione delle costanti e dei tipi annidati). Quando una classe è annidata all'interno di una classe esterna statica, la classe annidata non è una classe statica a meno che `static` non includa in modo esplicito un modificatore.

__Riferimento a tipi di classe statici__

Un *namespace_or_type_name* (nomi[di spazio dei nomi e di tipi](basic-concepts.md#namespace-and-type-names)) può fare riferimento a una classe statica se

*  *Namespace_or_type_name* è il `T` in un *namespace_or_type_name* nel formato `T.I`, oppure
*  *Namespace_or_type_name* è il `T` in un *typeof_expression* ([elenco di argomenti](expressions.md#argument-lists)1) nel formato `typeof(T)`.

Un *primary_expression* ([membri di funzione](expressions.md#function-members)) può fare riferimento a una classe statica se

*  *Primary_expression* è il `E` in un *member_access* ([controllo in fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) nel formato `E.I`.

In qualsiasi altro contesto, si tratta di un errore in fase di compilazione per fare riferimento a una classe statica. Ad esempio, è un errore per una classe statica da usare come classe base, un tipo costituente ([tipi annidati](classes.md#nested-types)) di un membro, un argomento di tipo generico o un vincolo di parametro di tipo. Analogamente, una classe statica non può essere usata in un tipo di matrice, un tipo `new` di puntatore, un'espressione, un' `is` espressione cast, `as` un'espressione, `sizeof` un'espressione, un'espressione o un'espressione con valore predefinito.

### <a name="partial-modifier"></a>Modificatore parziale

Il modificatore `partial` viene usato per indicare che questo *class_declaration* è una dichiarazione di tipo parziale. Più dichiarazioni di tipo parziale con lo stesso nome in uno spazio dei nomi di inclusione o in una dichiarazione di tipo combinano per formare una dichiarazione di tipo, seguendo le regole specificate nei [tipi parziali](classes.md#partial-types).

La dichiarazione di una classe distribuita su segmenti separati del testo del programma può essere utile se questi segmenti vengono prodotti o conservati in contesti diversi. Ad esempio, una parte di una dichiarazione di classe può essere generata dal computer, mentre l'altra viene creata manualmente. La separazione testuale dei due impedisce l'aggiornamento da parte di uno degli aggiornamenti in conflitto con gli aggiornamenti.

### <a name="type-parameters"></a>Parametri di tipo

Un parametro di tipo è un identificatore semplice che denota un segnaposto per un argomento di tipo fornito per creare un tipo costruito. Un parametro di tipo è un segnaposto formale per un tipo che verrà fornito in un secondo momento. Al contrario, un argomento di tipo ([argomenti di tipo](types.md#type-arguments)) è il tipo effettivo che sostituisce il parametro di tipo quando viene creato un tipo costruito.

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

Ogni parametro di tipo in una dichiarazione di classe definisce un nome nello spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)) di tale classe. Quindi, non può avere lo stesso nome di un altro parametro di tipo o di un membro dichiarato in tale classe. Un parametro di tipo non può avere lo stesso nome del tipo stesso.

### <a name="class-base-specification"></a>Specifica di base della classe

Una dichiarazione di classe può includere una specifica *class_base* , che definisce la classe di base diretta della classe e le interfacce ([interfacce](interfaces.md)) direttamente implementate dalla classe.

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

La classe base specificata in una dichiarazione di classe può essere un tipo di classe costruito ([tipi costruiti](types.md#constructed-types)). Una classe di base non può essere un parametro di tipo autonomamente, anche se può coinvolgere i parametri di tipo inclusi nell'ambito.

```csharp
class Extend<V>: V {}            // Error, type parameter used as base class
```

#### <a name="base-classes"></a>Classi di base

Quando un *class_type* è incluso in *class_base*, specifica la classe di base diretta della classe dichiarata. Se una dichiarazione di classe non dispone di *class_base*o se *class_base* elenca solo i tipi di interfaccia, si presuppone che la classe di base diretta sia `object`. Una classe eredita i membri dalla relativa classe di base diretta, come descritto in [ereditarietà](classes.md#inheritance).

Nell'esempio
```csharp
class A {}

class B: A {}
```
la `A` classe è detta classe di base diretta di `B`e `B` viene definita derivata da `A`. Poiché `A` in non viene specificata in modo esplicito una classe base diretta, la relativa classe di `object`base diretta è implicita.

Per un tipo di classe costruito, se nella dichiarazione di classe generica viene specificata una classe base, la classe base del tipo costruito viene ottenuta sostituendo, per ogni *type_parameter* nella dichiarazione della classe di base, il *type_argument corrispondente* del tipo costruito. Date le dichiarazioni di classi generiche
```csharp
class B<U,V> {...}

class G<T>: B<string,T[]> {...}
```
la classe base del tipo `G<int>` costruito sarà. `B<string,int[]>`

La classe di base diretta di un tipo di classe deve essere accessibile almeno quanto il tipo di classe ([domini di accessibilità](basic-concepts.md#accessibility-domains)). Ad esempio, si tratta di un errore in fase di compilazione `public` per la derivazione di `private` una `internal` classe da una classe o.

La classe di base diretta di un tipo di classe non può essere uno dei tipi seguenti `System.Array`: `System.Delegate` `System.MulticastDelegate`,, `System.Enum`, o `System.ValueType`. Inoltre, una dichiarazione di classe generica `System.Attribute` non può essere utilizzata come classe base diretta o indiretta.

Quando si determina il `A` significato della specifica della classe di base diretta di una classe `B`, la classe di `B` base diretta di è temporaneamente `object`considerata. In modo intuitivo, questo garantisce che il significato di una specifica della classe base non possa dipendere in modo ricorsivo da se stesso. Esempio:
```csharp
class A<T> {
   public class B {}
}

class C : A<C.B> {}
```
è in errore poiché nella specifica `A<C.B>` della classe di base la classe di base diretta di `C` viene considerata `object`e, di conseguenza, (dalle regole dei [nomi di spazio dei nomi e di tipo](basic-concepts.md#namespace-and-type-names)) `C` non è considerato un membro `B`.

Le classi base di un tipo di classe sono la classe base diretta e le relative classi base. In altre parole, il set di classi di base è la chiusura transitiva della relazione della classe di base diretta. Facendo riferimento all'esempio precedente, le classi di base di `B` sono `A` e `object`. Nell'esempio
```csharp
class A {...}

class B<T>: A {...}

class C<T>: B<IComparable<T>> {...}

class D<T>: C<T[]> {...}
```
le classi di base `D<int>` di `C<int[]>`sono `B<IComparable<int[]>>`, `A`, e `object`.

Ad eccezione della `object`classe, ogni tipo di classe dispone esattamente di una classe di base diretta. La `object` classe non dispone di una classe di base diretta ed è la classe base finale di tutte le altre classi.

Quando una classe `B` deriva da una classe `A`, si tratta di un `B`errore in fase `A` di compilazione da cui dipende. Una classe ***dipende direttamente*** dalla relativa classe di base diretta, se presente, e ***dipende direttamente*** dalla classe in cui è immediatamente annidata (se presente). Data questa definizione, il set completo di classi da cui dipende una classe è la chiusura riflessiva e transitiva del ***dipendente direttamente dalla*** relazione.

Esempio
```csharp
class A: A {}
```
è errato perché la classe dipende da se stessa. Analogamente, l'esempio
```csharp
class A: B {}
class B: C {}
class C: A {}
```
è in errore perché le classi dipendono da se stesse. Infine, l'esempio
```csharp
class A: B.C {}

class B: A
{
    public class C {}
}
```
genera un errore in fase di compilazione perché `A` dipende `B.C` da (la relativa classe di base diretta) `B` , che dipende da (la classe che la contiene immediatamente `A`), che dipende in modo circolare da.

Si noti che una classe non dipende dalle classi annidate al suo interno. Nell'esempio
```csharp
class A
{
    class B: A {}
}
```
`B``A` `B` `B` dipende da `A` (perché è la classe di base diretta e la classe che la contiene immediatamente), ma non dipende da (poiché non è una classe di base o una classe di inclusione di `A` `A`). L'esempio è quindi valido.

Non è possibile derivare da una `sealed` classe. Nell'esempio
```csharp
sealed class A {}

class B: A {}            // Error, cannot derive from a sealed class
```
`A`la `B` classeèinerroreperchétentadiderivaredallaclasse.`sealed`

#### <a name="interface-implementations"></a>Implementazioni di interfacce

Una specifica *class_base* può includere un elenco di tipi di interfaccia, nel qual caso viene definito che la classe implementi direttamente i tipi di interfaccia specificati. Le implementazioni dell'interfaccia sono illustrate ulteriormente nelle [implementazioni dell'interfaccia](interfaces.md#interface-implementations).

### <a name="type-parameter-constraints"></a>Vincoli di parametro di tipo

Le dichiarazioni di tipi e metodi generici possono facoltativamente specificare vincoli di parametro di tipo includendo *type_parameter_constraints_clause*s.

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

Ogni *type_parameter_constraints_clause* è costituito dal token `where`, seguito dal nome di un parametro di tipo, seguito da due punti e dall'elenco di vincoli per quel parametro di tipo. Per ogni parametro di tipo può `where` essere presente al massimo una clausola e le `where` clausole possono essere elencate in qualsiasi ordine. Analogamente `get` ai `set` token e in una funzione di accesso alla `where` proprietà, il token non è una parola chiave.

L'elenco di vincoli specificati in una `where` clausola può includere uno dei seguenti componenti, in questo ordine: un singolo vincolo primario, uno o più vincoli secondari e il vincolo del costruttore,. `new()`

Un vincolo primario può essere un tipo di classe o il vincolo del ***tipo*** `class` di riferimento o il ***vincolo*** `struct`del tipo di valore. Un vincolo secondario può essere *type_parameter* o *INTERFACE_TYPE*.

Il vincolo del tipo di riferimento specifica che un argomento di tipo utilizzato per il parametro di tipo deve essere un tipo riferimento. Tutti i tipi di classe, i tipi di interfaccia, i tipi di delegati, i tipi di matrici e i parametri di tipo noti come un tipo di riferimento, come definito di seguito, soddisfano questo vincolo.

Il vincolo di tipo valore specifica che un argomento di tipo utilizzato per il parametro di tipo deve essere un tipo valore non nullable. Tutti i tipi struct non nullable, i tipi enum e i parametri di tipo con il vincolo di tipo valore soddisfano questo vincolo. Si noti che anche se classificato come tipo valore, un tipo Nullable ([tipi nullable](types.md#nullable-types)) non soddisfa il vincolo di tipo valore. Un parametro di tipo con il vincolo di tipo valore non può avere anche *constructor_constraint*.

I tipi di puntatore non sono mai consentiti come argomenti di tipo e non sono considerati conformi al tipo di riferimento o ai vincoli del tipo di valore.

Se un vincolo è un tipo di classe, un tipo di interfaccia o un parametro di tipo, tale tipo specifica un "tipo di base" minimo che ogni argomento di tipo usato per quel parametro di tipo deve supportare. Ogni volta che viene usato un tipo costruito o un metodo generico, l'argomento di tipo viene confrontato con i vincoli sul parametro di tipo in fase di compilazione. L'argomento di tipo fornito deve soddisfare le condizioni descritte nei [vincoli soddisfacenti](types.md#satisfying-constraints).

Un vincolo *class_type* deve soddisfare le regole seguenti:

*  Il tipo deve essere un tipo di classe.
*  Il tipo non deve essere `sealed`.
*  Il tipo non deve essere uno dei tipi seguenti `System.Array`:, `System.Delegate`, `System.Enum`o `System.ValueType`.
*  Il tipo non deve essere `object`. Poiché tutti i tipi derivano da, un vincolo di `object`questo tipo non avrà alcun effetto se fosse consentito.
*  Al massimo un vincolo per un determinato parametro di tipo può essere un tipo classe.

Un tipo specificato come vincolo *INTERFACE_TYPE* deve soddisfare le regole seguenti:

*  Il tipo deve essere un tipo di interfaccia.
*  Un tipo non deve essere specificato più di una volta in una `where` clausola specificata.

In entrambi i casi, il vincolo può coinvolgere qualsiasi parametro di tipo della dichiarazione del tipo o del metodo associato come parte di un tipo costruito e può coinvolgere il tipo dichiarato.

Qualsiasi classe o tipo di interfaccia specificato come vincolo di parametro di tipo deve essere almeno accessibile ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)) del tipo o del metodo generico dichiarato.

Un tipo specificato come vincolo *type_parameter* deve soddisfare le regole seguenti:

*  Il tipo deve essere un parametro di tipo.
*  Un tipo non deve essere specificato più di una volta in una `where` clausola specificata.

Inoltre, non deve essere presente alcun ciclo nel grafico delle dipendenze di parametri di tipo, in cui la dipendenza è una relazione transitiva definita da:

*  Se `T` un parametro di tipo viene usato come vincolo per il `S` parametro `S` di tipo, ***dipende*** `T`da.
*  Se `S` un parametro di tipo dipende da un parametro `T` di tipo `T` e dipende da un parametro `U` `S` di tipo, ***dipende*** `U`da.

Data questa relazione, si tratta di un errore in fase di compilazione affinché un parametro di tipo dipenda da solo (direttamente o indirettamente).

Tutti i vincoli devono essere coerenti tra i parametri di tipo dipendente. Se il parametro `S` di tipo dipende dal parametro `T` di tipo:

*  `T`non deve avere il vincolo di tipo valore. In caso `T` contrario, è effettivamente `S` sealed, quindi verrebbe forzato a `T`essere lo stesso tipo di, eliminando la necessità di due parametri di tipo.
*  Se `S` ha il vincolo di tipo valore, `T` non deve avere un vincolo *class_type* .
*  Se `S` ha un vincolo *class_type* `A` e `T` ha un vincolo *class_type* `B`, è necessario che sia presente una conversione di identità o una conversione di un riferimento implicito da `A` a `B` o una conversione di riferimento implicita da da `B` a `A`.
*  Se `S` dipende anche dal parametro di tipo `U` e `U` ha un vincolo *class_type* `A` e `T` ha un vincolo *class_type* `B`, è necessario che sia presente una conversione di identità o un riferimento implicito da `A` per `B` o una conversione di un riferimento implicito da 0 a 1.

Il vincolo di tipo `S` di valore e `T` il vincolo di tipo di riferimento sono validi per l'oggetto. Questo limite `T` è effettivamente limitato ai `System.Object`tipi `System.ValueType`, `System.Enum`, e a qualsiasi tipo di interfaccia.

Se la `where` clausola per un parametro di tipo include un vincolo del costruttore, che ha `new()`il formato, è possibile usare l' `new` operatore per creare istanze del tipo (espressioni di[creazione di oggetti](expressions.md#object-creation-expressions)). Qualsiasi argomento di tipo usato per un parametro di tipo con un vincolo del costruttore deve avere un costruttore pubblico senza parametri (questo costruttore esiste in modo implicito per qualsiasi tipo di valore) o essere un parametro di tipo con il vincolo del tipo di valore o del costruttore (vedere [Vincoli del parametro di tipo](classes.md#type-parameter-constraints) per i dettagli).

Di seguito sono riportati alcuni esempi di vincoli:
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

L'esempio seguente è in errore perché causa una circolare nel grafico delle dipendenze dei parametri di tipo:
```csharp
class Circular<S,T>
    where S: T
    where T: S                // Error, circularity in dependency graph
{
    ...
}
```

Gli esempi seguenti illustrano situazioni non valide aggiuntive:
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

La ***classe base effettiva*** di un parametro `T` di tipo è definita nel modo seguente:

*  Se `T` non ha vincoli primari o vincoli di parametro di tipo, la relativa classe `object`di base effettiva è.
*  Se `T` ha il vincolo di tipo valore, la relativa classe di `System.ValueType`base effettiva è.
*  Se `T` ha un vincolo *class_type* `C` ma nessun vincolo *type_parameter* , la relativa classe di base effettiva sarà `C`.
*  Se `T` non ha un vincolo *class_type* ma ha uno o più vincoli *type_parameter* , la relativa classe di base effettiva è il tipo più incluso ([operatori di conversione rimossi](conversions.md#lifted-conversion-operators)) nel set di classi di base valide del relativo *Type_* vincoli di parametro. Le regole di coerenza assicurano che esista un tipo più incluso.
*  Se `T` dispone sia di un vincolo *class_type* che di uno o più vincoli *type_parameter* , la relativa classe di base effettiva è il tipo più incluso ([operatori di conversione rimossi](conversions.md#lifted-conversion-operators)) nel set costituito da *class_type* vincolo di `T` e delle classi di base valide dei relativi vincoli *type_parameter* . Le regole di coerenza assicurano che esista un tipo più incluso.
*  Se `T` ha il vincolo del tipo di riferimento ma nessun vincolo *class_type* , la relativa classe di base effettiva sarà `object`.

Ai fini di queste regole, se T ha un vincolo `V` che è un *value_type*, usare invece il tipo di base più specifico di `V` che è un *class_type*. Questo problema non può mai verificarsi in un vincolo specificato in modo esplicito, ma può verificarsi quando i vincoli di un metodo generico vengono ereditati in modo implicito da una dichiarazione di metodo che esegue l'override o da un'implementazione esplicita di un metodo di interfaccia.

Queste regole assicurano che la classe di base effettiva sia sempre un *class_type*.

Il ***set di interfacce effettivo*** di un parametro `T` di tipo viene definito nel modo seguente:

*  Se `T` non ha *secondary_constraints*, il set di interfacce effettivo è vuoto.
*  Se `T` ha vincoli *INTERFACE_TYPE* ma nessun vincolo *type_parameter* , il set di interfacce effettivo è il set di vincoli *INTERFACE_TYPE* .
*  Se `T` non ha vincoli *INTERFACE_TYPE* ma presenta vincoli *type_parameter* , il relativo set di interfacce effettivo è l'Unione dei set di interfaccia effettivi dei vincoli *type_parameter* .
*  Se `T` ha sia vincoli *INTERFACE_TYPE* che vincoli *type_parameter* , il set di interfacce effettivo è l'Unione del set di vincoli *INTERFACE_TYPE* e dei set di interfacce effettivi del relativo *type_parameter* vincoli.

Un parametro di tipo è ***noto come tipo di riferimento*** se ha il vincolo di tipo riferimento o la classe di base effettiva non `object` è `System.ValueType`o.

I valori di un tipo di parametro di tipo vincolato possono essere utilizzati per accedere ai membri di istanza impliciti nei vincoli. Nell'esempio
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
i metodi di `IPrintable` possono essere richiamati `x` direttamente `T` in perché è vincolato a implementare `IPrintable`sempre.

### <a name="class-body"></a>Corpo della classe

Il *class_body* di una classe definisce i membri di tale classe.

```antlr
class_body
    : '{' class_member_declaration* '}'
    ;
```

## <a name="partial-types"></a>Tipi parziali

Una dichiarazione di tipo può essere divisa tra più ***dichiarazioni di tipo parziale***. La dichiarazione di tipo viene costruita dalle sue parti seguendo le regole in questa sezione, in quanto viene considerata come una singola dichiarazione durante il resto dell'elaborazione del programma in fase di compilazione e in fase di esecuzione.

*Class_declaration*, *struct_declaration* o *interface_declaration* rappresenta una dichiarazione di tipo parziale se include un modificatore `partial`. `partial`non è una parola chiave e agisce solo come modificatore se viene visualizzato immediatamente prima di una delle parole `class`chiave `struct` o `interface` in una dichiarazione di tipo o prima del tipo `void` in una dichiarazione di metodo. In altri contesti può essere usato come identificatore normale.

Ogni parte di una dichiarazione di tipo parziale deve includere `partial` un modificatore. Deve avere lo stesso nome ed essere dichiarato nello stesso spazio dei nomi o nella stessa dichiarazione di tipo delle altre parti. Il `partial` modificatore indica che altre parti della dichiarazione di tipo possono esistere altrove, ma l'esistenza di tali parti aggiuntive non è un requisito; è valido per un tipo con una sola dichiarazione per includere il `partial` modificatore.

Tutte le parti di un tipo parziale devono essere compilate insieme in modo che sia possibile unire le parti in fase di compilazione in una singola dichiarazione di tipo. I tipi parziali in particolare non consentono l'estensione di tipi già compilati.

I tipi annidati possono essere dichiarati in più `partial` parti tramite il modificatore. In genere, il tipo contenitore viene dichiarato `partial` utilizzando anche e ogni parte del tipo annidato viene dichiarata in una parte diversa del tipo che lo contiene.

Il `partial` modificatore non è consentito nelle dichiarazioni di delegato o enum.

### <a name="attributes"></a>Attributi

Gli attributi di un tipo parziale vengono determinati combinando, in un ordine non specificato, gli attributi di ogni parte. Se un attributo viene inserito su più parti, equivale a specificare l'attributo più volte nel tipo. Ad esempio, le due parti:

```csharp
[Attr1, Attr2("hello")]
partial class A {}

[Attr3, Attr2("goodbye")]
partial class A {}
```
sono equivalenti a una dichiarazione come:
```csharp
[Attr1, Attr2("hello"), Attr3, Attr2("goodbye")]
class A {}
```

Gli attributi dei parametri di tipo vengono combinati in modo simile.

### <a name="modifiers"></a>Modificatori

Quando una dichiarazione di tipo parziale include una specifica di accessibilità `protected`, `internal`ovvero i `private` `public`modificatori,, e, deve essere conforme a tutte le altre parti che includono una specifica di accessibilità. Se nessuna parte di un tipo parziale include una specifica di accessibilità, al tipo viene assegnata l'accessibilità predefinita appropriata ([accessibilità dichiarata](basic-concepts.md#declared-accessibility)).

Se una o più dichiarazioni parziali di un tipo annidato includono `new` un modificatore, non viene segnalato alcun avviso se il tipo annidato nasconde un membro ereditato ([nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance)).

Se una o più dichiarazioni parziali di una classe includono un `abstract` modificatore, la classe viene considerata astratta ([classi astratte](classes.md#abstract-classes)). In caso contrario, la classe viene considerata non astratta.

Se una o più dichiarazioni parziali di una classe includono un `sealed` modificatore, la classe viene considerata sealed ([classi sealed](classes.md#sealed-classes)). In caso contrario, la classe viene considerata non sealed.

Si noti che una classe non può essere sia astratta che sealed.

Quando il `unsafe` modificatore viene utilizzato in una dichiarazione di tipo parziale, solo quella particolare parte viene considerata un contesto non sicuro ([contesti non sicuri](unsafe-code.md#unsafe-contexts)).

### <a name="type-parameters-and-constraints"></a>Parametri di tipo e vincoli

Se un tipo generico è dichiarato in più parti, ogni parte deve indicare i parametri di tipo. Ogni parte deve avere lo stesso numero di parametri di tipo e lo stesso nome per ogni parametro di tipo, nell'ordine.

Quando una dichiarazione di tipo generico parziale include vincoli`where` (clausole), i vincoli devono essere concordati con tutte le altre parti che includono vincoli. In particolare, ogni parte che include vincoli deve contenere vincoli per lo stesso set di parametri di tipo e per ogni parametro di tipo i set di vincoli primari, secondari e del costruttore devono essere equivalenti. Due set di vincoli sono equivalenti se contengono gli stessi membri. Se nessuna parte di un tipo generico parziale specifica vincoli di parametro di tipo, i parametri di tipo vengono considerati non vincolati.

Esempio
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
è corretto perché le parti che includono vincoli (le prime due) specificano in modo efficace lo stesso set di vincoli PRIMARY, Secondary e Constructor per lo stesso set di parametri di tipo, rispettivamente.

### <a name="base-class"></a>Classe base

Quando una dichiarazione di classe parziale include una specifica della classe di base, deve essere conforme a tutte le altre parti che includono una specifica della classe base. Se nessuna parte di una classe parziale include una specifica della classe base, la classe di `System.Object` base diventa ([classi base](classes.md#base-classes)).

### <a name="base-interfaces"></a>Interfacce di base

Il set di interfacce di base per un tipo dichiarato in più parti è l'Unione delle interfacce di base specificate in ogni parte. Una particolare interfaccia di base può essere denominata una sola volta in ogni parte, ma è consentito per più parti denominare le stesse interfacce di base. Deve essere presente solo un'implementazione dei membri di qualsiasi interfaccia di base specificata.

Nell'esempio
```csharp
partial class C: IA, IB {...}

partial class C: IC {...}

partial class C: IA, IB {...}
```
il set di interfacce di base per `C` la `IA`classe `IB`è, `IC`e.

In genere, ogni parte fornisce un'implementazione delle interfacce dichiarate su tale parte; Tuttavia, questo non è un requisito. Una parte può fornire l'implementazione per un'interfaccia dichiarata in una parte diversa:
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

### <a name="members"></a>Members

Fatta eccezione per i metodi parziali ([metodi parziali](classes.md#partial-methods)), il set di membri di un tipo dichiarato in più parti è semplicemente l'Unione del set di membri dichiarato in ogni parte. I corpi di tutte le parti della dichiarazione di tipo condividono lo stesso spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)) e l'ambito di ogni membro ([ambiti](basic-concepts.md#scopes)) si estende ai corpi di tutte le parti. Il dominio di accessibilità di qualsiasi membro include sempre tutte le parti del tipo di inclusione. un `private` membro dichiarato in una parte è accessibile liberamente da un'altra parte. Si tratta di un errore in fase di compilazione per dichiarare lo stesso membro in più di una parte del tipo, a meno che tale membro non sia un `partial` tipo con il modificatore.

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

L'ordinamento dei membri all'interno di un tipo è raramente C# significativo per il codice, ma può essere significativo in caso di interazione con altri linguaggi e ambienti. In questi casi, l'ordinamento dei membri all'interno di un tipo dichiarato in più parti non è definito.

### <a name="partial-methods"></a>Metodi parziali

I metodi parziali possono essere definiti in una parte di una dichiarazione di tipo e implementati in un altro. L'implementazione è facoltativa. Se nessuna parte implementa il metodo parziale, la dichiarazione del metodo parziale e tutte le chiamate a tale metodo vengono rimosse dalla dichiarazione del tipo risultante dalla combinazione delle parti.

I metodi parziali non possono definire modificatori di accesso, `private`ma sono implicitamente. Il tipo restituito deve essere `void`e i relativi parametri non possono avere `out` il modificatore. L'identificatore `partial` viene riconosciuto come una parola chiave speciale in una dichiarazione di metodo solo se viene visualizzato immediatamente `void` prima del tipo; in caso contrario, può essere usato come identificatore normale. Un metodo parziale non può implementare in modo esplicito i metodi di interfaccia.

Esistono due tipi di dichiarazioni di metodo parziale: Se il corpo della dichiarazione di metodo è un punto e virgola, la dichiarazione viene ***definita come dichiarazione di metodo parziale***che lo definisce. Se il corpo viene specificato come *blocco*, la dichiarazione viene definita come ***dichiarazione di metodo parziale di implementazione***. Nelle parti di una dichiarazione di tipo può essere presente una sola dichiarazione di metodo parziale di definizione con una determinata firma. può essere presente una sola dichiarazione di implementazione del metodo parziale con una firma specificata. Se viene fornita una dichiarazione di metodo parziale di implementazione, deve esistere una dichiarazione di metodo parziale di definizione corrispondente e le dichiarazioni devono corrispondere come specificato nell'esempio seguente:

* Le dichiarazioni devono avere gli stessi modificatori (anche se non necessariamente nello stesso ordine), il nome del metodo, il numero di parametri di tipo e il numero di parametri.
* I parametri corrispondenti nelle dichiarazioni devono avere gli stessi modificatori (anche se non necessariamente nello stesso ordine) e gli stessi tipi (differenze di modulo nei nomi dei parametri di tipo).
* I parametri di tipo corrispondenti nelle dichiarazioni devono avere gli stessi vincoli (differenze di modulo nei nomi dei parametri di tipo).

Una dichiarazione di metodo parziale di implementazione può comparire nella stessa parte della dichiarazione di metodo parziale di definizione corrispondente.

Solo un metodo parziale di definizione fa parte della risoluzione dell'overload. Di conseguenza, indipendentemente dal fatto che venga fornita una dichiarazione di implementazione, le espressioni di chiamata potrebbero risolversi in chiamate del metodo parziale. Poiché un metodo parziale restituisce `void`sempre, le espressioni di chiamata saranno sempre istruzioni di espressione. Inoltre, poiché un metodo parziale è implicitamente `private`, tali istruzioni si verificheranno sempre all'interno di una delle parti della dichiarazione di tipo all'interno della quale viene dichiarato il metodo parziale.

Se nessuna parte di una dichiarazione di tipo parziale contiene una dichiarazione di implementazione per un determinato metodo parziale, qualsiasi istruzione di espressione che la richiama viene semplicemente rimossa dalla dichiarazione di tipo combinato. Pertanto, l'espressione di chiamata, incluse le espressioni costitutive, non ha alcun effetto in fase di esecuzione. Anche il metodo parziale viene rimosso e non sarà un membro della dichiarazione di tipo combinato.

Se esiste una dichiarazione di implementazione per un determinato metodo parziale, le chiamate dei metodi parziali vengono mantenute. Il metodo parziale genera una dichiarazione di metodo simile alla dichiarazione di metodo parziale di implementazione, ad eccezione di quanto segue:

* Il `partial` modificatore non è incluso
* Gli attributi nella dichiarazione di metodo risultante sono gli attributi combinati della definizione e della dichiarazione di metodo parziale di implementazione in ordine non specificato. I duplicati non vengono rimossi.
* Gli attributi sui parametri della dichiarazione di metodo risultante sono gli attributi combinati dei parametri corrispondenti della definizione e la dichiarazione di metodo parziale di implementazione in ordine non specificato. I duplicati non vengono rimossi.

Se viene fornita una dichiarazione di definizione ma non una dichiarazione di implementazione per un metodo parziale M, si applicano le restrizioni seguenti:

* Si tratta di un errore in fase di compilazione per creare un delegato al metodo ([espressioni di creazione di delegati](expressions.md#delegate-creation-expressions)).
* Si tratta di un errore in fase di compilazione per `M` fare riferimento all'interno di una funzione anonima convertita in un tipo di albero delle espressioni ([valutazione delle conversioni di funzioni anonime nei tipi di albero delle espressioni](conversions.md#evaluation-of-anonymous-function-conversions-to-expression-tree-types)).
* Le espressioni che si verificano come parte di una `M` chiamata di non influiscono sullo stato di assegnazione definito ([assegnazione definita](variables.md#definite-assignment)), che può causare errori in fase di compilazione.
* `M`non può essere il punto di ingresso di un'applicazione ([avvio dell'applicazione](basic-concepts.md#application-startup)).

I metodi parziali sono utili per consentire a una parte di una dichiarazione di tipo di personalizzare il comportamento di un'altra parte, ad esempio, uno generato da uno strumento. Si consideri la seguente dichiarazione di classe parziale:
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

Se questa classe viene compilata senza altre parti, le dichiarazioni di metodo parziale di definizione e le relative chiamate verranno rimosse e la dichiarazione della classe combinata risultante sarà equivalente alla seguente:
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

Si supponga che, tuttavia, venga fornita un'altra parte, che fornisce dichiarazioni di implementazione dei metodi parziali:
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

La dichiarazione di classe combinata risultante sarà equivalente alla seguente:
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

### <a name="name-binding"></a>Associazione di nomi

Sebbene ogni parte di un tipo estensibile debba essere dichiarata all'interno dello stesso spazio dei nomi, le parti vengono in genere scritte in diverse dichiarazioni dello spazio dei nomi. È pertanto possibile `using` che siano presenti direttive diverse ([direttive using](namespaces.md#using-directives)) per ogni parte. Quando si interpretano nomi semplici ([inferenza del tipo](expressions.md#type-inference)) in una parte `using` , vengono considerate solo le direttive delle dichiarazioni dello spazio dei nomi che racchiudono tale parte. Ciò può comportare che lo stesso identificatore abbia significati diversi in parti diverse:
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

I membri di una classe sono costituiti dai membri introdotti dai relativi *class_member_declaration*e dai membri ereditati dalla classe di base diretta.

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

I membri di un tipo di classe sono divisi nelle categorie seguenti:

*  Costanti, che rappresentano i valori costanti associati alla classe ([costanti](classes.md#constants)).
*  Campi, ovvero le variabili della classe ([campi](classes.md#fields)).
*  Metodi, che implementano i calcoli e le azioni che possono essere eseguiti dalla classe ([Metodi](classes.md#methods)).
*  Proprietà che definiscono le caratteristiche denominate e le azioni associate alla lettura e alla scrittura di tali caratteristiche ([Proprietà](classes.md#properties)).
*  Eventi, che definiscono le notifiche che possono essere generate dalla classe ([eventi](classes.md#events)).
*  Indicizzatori, che consentono di indicizzare le istanze della classe nello stesso modo (sintatticamente) come matrici ([indicizzatori](classes.md#indexers)).
*  Operatori, che definiscono gli operatori di espressione che possono essere applicati alle istanze della classe ([operatori](classes.md#operators)).
*  Costruttori di istanze, che implementano le azioni necessarie per inizializzare istanze della classe ([costruttori di istanza](classes.md#instance-constructors))
*  Distruttori, che implementano le azioni da eseguire prima che le istanze della classe vengano eliminate definitivamente ([distruttori](classes.md#destructors)).
*  Costruttori statici, che implementano le azioni necessarie per inizializzare la classe stessa ([costruttori statici](classes.md#static-constructors)).
*  Tipi, che rappresentano i tipi locali della classe ([tipi annidati](classes.md#nested-types)).

I membri che possono contenere codice eseguibile sono collettivamente noti come *membri di funzione* del tipo di classe. I membri della funzione di un tipo di classe sono i metodi, le proprietà, gli eventi, gli indicizzatori, gli operatori, i costruttori di istanza, i distruttori e i costruttori statici del tipo di classe.

Un *class_declaration* crea un nuovo spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)) e il *class_member_declaration*immediatamente contenuto da *class_declaration* introduce nuovi membri in questo spazio di dichiarazione. Le regole seguenti si applicano a *class_member_declaration*s:

*  I costruttori di istanza, i distruttori e i costruttori statici devono avere lo stesso nome della classe che lo contiene immediatamente. Tutti gli altri membri devono avere nomi diversi dal nome della classe che lo contiene immediatamente.
*  Il nome di una costante, un campo, una proprietà, un evento o un tipo deve essere diverso dai nomi di tutti gli altri membri dichiarati nella stessa classe.
*  Il nome di un metodo deve essere diverso da quello dei nomi di tutti gli altri metodi non dichiarati nella stessa classe. Inoltre, la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) di un metodo deve essere diversa dalle firme di tutti gli altri metodi dichiarati nella stessa classe e due metodi dichiarati nella stessa classe potrebbero non avere firme che differiscono esclusivamente da `ref` e `out`.
*  La firma di un costruttore di istanza deve essere diversa dalle firme di tutti gli altri costruttori di istanza dichiarati nella stessa classe e due costruttori dichiarati nella stessa classe potrebbero non avere firme che differiscono `out`solo per `ref` e.
*  La firma di un indicizzatore deve essere diversa dalle firme di tutti gli altri indicizzatori dichiarati nella stessa classe.
*  La firma di un operatore deve essere diversa dalle firme di tutti gli altri operatori dichiarati nella stessa classe.

I membri ereditati di un tipo di classe ([ereditarietà](classes.md#inheritance)) non fanno parte dello spazio della dichiarazione di una classe. Pertanto, una classe derivata può dichiarare un membro con lo stesso nome o firma di un membro ereditato, che in effetti nasconde il membro ereditato.

### <a name="the-instance-type"></a>Tipo di istanza

A ogni dichiarazione di classe è associato un tipo associato ([tipi associati e non associati](types.md#bound-and-unbound-types)), il ***tipo di istanza***. Per una dichiarazione di classe generica, il tipo di istanza viene creato creando un tipo costruito ([tipi costruiti](types.md#constructed-types)) dalla dichiarazione del tipo, con ciascuno degli argomenti di tipo forniti che corrispondono al parametro di tipo corrispondente. Poiché il tipo di istanza utilizza i parametri di tipo, può essere utilizzato solo se i parametri di tipo sono inclusi nell'ambito. ovvero, all'interno della dichiarazione di classe. Il tipo di istanza è il tipo `this` di per il codice scritto all'interno della dichiarazione di classe. Per le classi non generiche, il tipo di istanza è semplicemente la classe dichiarata. Di seguito vengono illustrate diverse dichiarazioni di classe insieme ai relativi tipi di istanza: 
```csharp
class A<T>                           // instance type: A<T>
{
    class B {}                       // instance type: A<T>.B
    class C<U> {}                    // instance type: A<T>.C<U>
}

class D {}                           // instance type: D
```

### <a name="members-of-constructed-types"></a>Membri di tipi costruiti

I membri non ereditati di un tipo costruito vengono ottenuti sostituendo, per ogni *type_parameter* nella dichiarazione del membro, il *type_argument* corrispondente del tipo costruito. Il processo di sostituzione è basato sul significato semantico delle dichiarazioni di tipo e non è semplicemente una sostituzione testuale.

Ad esempio, data la dichiarazione di classe generica
```csharp
class Gen<T,U>
{
    public T[,] a;
    public void G(int i, T t, Gen<U,T> gt) {...}
    public U Prop { get {...} set {...} }
    public int H(double d) {...}
}
```
il tipo `Gen<int[],IComparable<string>>` costruito ha i membri seguenti:
```csharp
public int[,][] a;
public void G(int i, int[] t, Gen<IComparable<string>,int[]> gt) {...}
public IComparable<string> Prop { get {...} set {...} }
public int H(double d) {...}
```

Il `a` tipo del membro nella Dichiarazione `Gen` di classe generica è "matrice bidimensionale di `T`", pertanto il tipo del membro `a` nel tipo costruito sopra è "matrice bidimensionale di una matrice unidimensionale di `int`", o `int[,][]`.

All'interno dei membri della funzione di istanza `this` , il tipo di è il tipo di istanza ([il tipo di istanza](classes.md#the-instance-type)) della dichiarazione che lo contiene.

Tutti i membri di una classe generica possono usare parametri di tipo di qualsiasi classe contenitore, direttamente o come parte di un tipo costruito. Quando un particolare tipo costruito chiuso ([tipi aperti e chiusi](types.md#open-and-closed-types)) viene utilizzato in fase di esecuzione, ogni utilizzo di un parametro di tipo viene sostituito con l'argomento di tipo effettivo fornito al tipo costruito. Esempio:
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

Una classe ***eredita*** i membri del tipo di classe di base diretta. L'ereditarietà indica che una classe contiene in modo implicito tutti i membri del relativo tipo di classe di base diretta, ad eccezione dei costruttori di istanza, dei distruttori e dei costruttori statici della classe di base. Alcuni aspetti importanti dell'ereditarietà sono:

*  L'ereditarietà è transitiva. Se `C` è derivato da `B`ed `B` è derivato da `A`, `C` eredita i membri dichiarati in `B` , nonché i membri dichiarati `A`in.
*  Una classe derivata estende la relativa classe di base diretta. Una classe derivata può aggiungere nuovi membri a quelli ereditati, ma non può rimuovere la definizione di un membro ereditato.
*  I costruttori di istanza, i distruttori e i costruttori statici non vengono ereditati, ma tutti gli altri membri sono, indipendentemente dall'accessibilità dichiarata ([accesso ai membri](basic-concepts.md#member-access)). Tuttavia, a seconda dell'accessibilità dichiarata, i membri ereditati potrebbero non essere accessibili in una classe derivata.
*  Una classe derivata può ***nascondere*** ([nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance)) i membri ereditati dichiarando i nuovi membri con lo stesso nome o la stessa firma. Si noti tuttavia che nascondere un membro ereditato non rimuove quel membro, ma rende semplicemente inaccessibile tale membro direttamente tramite la classe derivata.
*  Un'istanza di una classe contiene un set di tutti i campi di istanza dichiarati nella classe e le relative classi di base e una conversione implicita ([conversioni di riferimenti implicite](conversions.md#implicit-reference-conversions)) esiste da un tipo di classe derivata a uno dei relativi tipi di classe di base. Pertanto, un riferimento a un'istanza di una classe derivata può essere considerato come un riferimento a un'istanza di una delle relative classi di base.
*  Una classe può dichiarare metodi, proprietà e indicizzatori virtuali e le classi derivate possono eseguire l'override dell'implementazione di questi membri di funzione. Ciò consente alle classi di presentare un comportamento polimorfico in cui le azioni eseguite dalla chiamata di un membro di funzione variano a seconda del tipo di runtime dell'istanza tramite cui viene richiamato il membro della funzione.

Il membro ereditato di un tipo di classe costruito è costituito dai membri del tipo di classe di base immediato ([classi base](classes.md#base-classes)), che viene trovato sostituendo gli argomenti di tipo del tipo costruito per ogni occorrenza dei parametri di tipo corrispondenti nell'oggetto  *specifica class_base* . Questi membri, a loro volta, vengono trasformati sostituendo, per ogni *type_parameter* nella dichiarazione del membro, il *type_argument* corrispondente della specifica *class_base* .

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

Nell'esempio precedente, il `D<int>` tipo costruito ha un membro `public int G(string s)` non ereditato ottenuto sostituendo l'argomento `int` di tipo per il parametro `T`di tipo. `D<int>`dispone anche di un membro ereditato dalla Dichiarazione `B`di classe. Questo membro ereditato è determinato dalla prima determinazione `B<int[]>` del tipo `D<int>` di classe di base di sostituendo `T` `int` per nella specifica `B<T[]>`della classe base. Quindi, come argomento di tipo per `B`, `int[]` viene sostituito `U` con in `public U F(long index)`, cedendo il membro `public int[] F(long index)`ereditato.

### <a name="the-new-modifier"></a>Nuovo modificatore

Un *class_member_declaration* è autorizzato a dichiarare un membro con lo stesso nome o firma di un membro ereditato. Quando si verifica questo problema, viene detto che il membro della classe derivata ***nasconde*** il membro della classe base. Nascondere un membro ereditato non viene considerato un errore, ma il compilatore genera un avviso. Per non visualizzare l'avviso, la dichiarazione del membro della classe derivata può includere `new` un modificatore per indicare che il membro derivato deve nascondere il membro di base. Questo argomento viene trattato ulteriormente in modo [nascosto tramite ereditarietà](basic-concepts.md#hiding-through-inheritance).

Se un `new` modificatore è incluso in una dichiarazione che non nasconde un membro ereditato, viene emesso un avviso a tale effetto. Questo avviso viene eliminato rimuovendo il `new` modificatore.

### <a name="access-modifiers"></a>Modificatori di accesso

Un *class_member_declaration* può avere uno dei cinque tipi possibili di accessibilità dichiarata ([accessibilità](basic-concepts.md#declared-accessibility)dichiarata): `public`, `protected internal`, `protected`, `internal` o `private`. Ad eccezione della `protected internal` combinazione, si tratta di un errore in fase di compilazione per specificare più di un modificatore di accesso. Quando un *class_member_declaration* non include modificatori di accesso, si presuppone `private`.

### <a name="constituent-types"></a>Tipi costitutivi

I tipi utilizzati nella dichiarazione di un membro sono denominati tipi costitutivi di tale membro. I tipi costitutivi possibili sono il tipo di una costante, un campo, una proprietà, un evento o un indicizzatore, il tipo restituito di un metodo o un operatore e i tipi di parametro di un metodo, un indicizzatore, un operatore o un costruttore di istanza. I tipi costitutivi di un membro devono essere accessibili almeno quanto il membro stesso ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).

### <a name="static-and-instance-members"></a>Membri statici e di istanza

I membri di una classe sono ***membri statici*** o ***membri di istanza***. In generale, è utile considerare i membri statici come appartenenti ai tipi di classe e ai membri di istanza come appartenenti a oggetti (istanze di tipi di classe).

Quando un campo, un metodo, una proprietà, un evento, un operatore o una `static` dichiarazione di costruttore include un modificatore, dichiara un membro statico. Inoltre, una costante o una dichiarazione di tipo dichiara in modo implicito un membro statico. I membri statici presentano le seguenti caratteristiche:

*  Quando si fa riferimento a un membro statico `M` in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, `E` deve indicare un tipo contenente `M`. Si tratta di un errore in fase di `E` compilazione per la denotazione di un'istanza di.
*  Un campo statico identifica esattamente un percorso di archiviazione da condividere con tutte le istanze di un determinato tipo di classe closed. Indipendentemente dal numero di istanze di un determinato tipo di classe chiusa, esiste una sola copia di un campo statico.
*  Un membro di funzione statica (metodo, proprietà, evento, operatore o costruttore) non funziona in un'istanza specifica e si tratta di un errore in fase di compilazione a cui fare `this` riferimento in un membro di funzione di questo tipo.

Quando un campo, un metodo, una proprietà, un evento, un indicizzatore, un costruttore o una dichiarazione di distruttore non include un `static` modificatore, dichiara un membro di istanza. Un membro di istanza viene talvolta denominato membro non statico. I membri di istanza hanno le caratteristiche seguenti:

*  Quando si fa riferimento a un membro di istanza `M` in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, `E` deve indicare un'istanza di un tipo contenente `M`. Si tratta di un errore in fase di `E` binding per per indicare un tipo.
*  Ogni istanza di una classe contiene un set separato di tutti i campi di istanza della classe.
*  Un membro della funzione di istanza (metodo, proprietà, indicizzatore, costruttore di istanza o distruttore) opera su un'istanza specificata della classe e questa istanza è accessibile come `this` ([questo accesso](expressions.md#this-access)).

Nell'esempio seguente vengono illustrate le regole per l'accesso ai membri statici e di istanza:
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

Il metodo `F` Mostra che in un membro della funzione di istanza, un *simple_name* ([nomi semplici](expressions.md#simple-names)) può essere usato per accedere ai membri di istanza e ai membri statici. Il metodo `G` Mostra che in un membro della funzione statica, si tratta di un errore in fase di compilazione per accedere a un membro di istanza tramite un *simple_name*. Il metodo `Main` Mostra che in un *member_access* ([accesso ai membri](expressions.md#member-access)), i membri dell'istanza devono essere accessibili tramite le istanze e i membri statici devono essere accessibili tramite i tipi.

### <a name="nested-types"></a>Tipi annidati

Un tipo dichiarato all'interno di una dichiarazione di classe o struct è denominato ***tipo annidato***. Un tipo dichiarato all'interno di un'unità di compilazione o di uno spazio dei nomi viene chiamato ***tipo non annidato***.

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
la `B` classe è un tipo annidato perché viene dichiarata all'interno `A` della classe `A`e la classe è un tipo non annidato perché viene dichiarata all'interno di un'unità di compilazione.

#### <a name="fully-qualified-name"></a>Nome completo

Il nome completo ([nomi](basic-concepts.md#fully-qualified-names)completi) per un tipo annidato è `S.N` dove `S` è il nome completo del tipo in cui è dichiarato il tipo `N` .

#### <a name="declared-accessibility"></a>Accessibilità dichiarata

I tipi non annidati possono `public` avere `internal` o dichiarati `internal` accessibilità e hanno dichiarato l'accessibilità per impostazione predefinita. I tipi annidati possono avere anche queste forme di accessibilità dichiarata, oltre a una o più forme aggiuntive di accessibilità dichiarate, a seconda che il tipo che lo contiene sia una classe o uno struct:

*  Un tipo annidato dichiarato in una classe può avere una qualsiasi delle cinque forme di accessibilità dichiarata `protected`( `internal``public`, `protected internal`, `private`, o) e, come altri membri della classe, `private` il valore predefinito è dichiarata. accessibilità.
*  Un tipo annidato dichiarato in uno struct può avere tre forme di accessibilità dichiarata (`public`, `internal`o `private`) e, come altri membri struct, per `private` impostazione predefinita è l'accessibilità dichiarata.

Esempio
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
dichiara una classe `Node`annidata privata.

#### <a name="hiding"></a>Nascondere

Un tipo annidato può nascondere[, ovvero nascondere, un](basic-concepts.md#name-hiding)membro di base. Il `new` modificatore è consentito nelle dichiarazioni di tipo annidato in modo che il nascondiglio possa essere espresso in modo esplicito. Esempio
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
Mostra una classe `M` annidata che nasconde il metodo `M` definito in `Base`.

#### <a name="this-access"></a>Questo accesso

Un tipo annidato e il relativo tipo che lo contiene non hanno una relazione speciale per quanto riguarda *THIS_ACCESS* ([questo accesso](expressions.md#this-access)). In particolare `this` , all'interno di un tipo annidato non può essere usato per fare riferimento ai membri di istanza del tipo che lo contiene. Nei casi in cui un tipo annidato deve accedere ai membri dell'istanza del tipo che lo contiene, è possibile fornire l' `this` accesso specificando l'oggetto per l'istanza del tipo che lo contiene come argomento del costruttore per il tipo annidato. Nell'esempio seguente
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
Mostra questa tecnica. Un'istanza di `C` crea un'istanza di `Nested` e passa il relativo `this` costruttore `Nested`a per fornire un accesso successivo ai `C`membri dell'istanza di.

#### <a name="access-to-private-and-protected-members-of-the-containing-type"></a>Accesso a membri privati e protetti del tipo che lo contiene

Un tipo annidato può accedere a tutti i membri accessibili al tipo che lo contiene, inclusi i membri del tipo che `private` lo contiene e `protected` l'accessibilità dichiarata. Esempio
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
Mostra una classe `C` che contiene una classe `Nested`annidata. All' `Nested`interno di, `G` il metodo chiama il `F` metodo statico `C`definito in `F` e ha un'accessibilità dichiarata privata.

Un tipo annidato può accedere anche a membri protetti definiti in un tipo di base del tipo che lo contiene. Nell'esempio
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
la `Derived.Nested` classe annidata accede al metodo `F` protetto definito nella `Derived`classe di base, `Base`, chiamando tramite un'istanza di `Derived`.

#### <a name="nested-types-in-generic-classes"></a>Tipi annidati in classi generiche

Una dichiarazione di classe generica può contenere dichiarazioni di tipo annidate. I parametri di tipo della classe contenitore possono essere utilizzati all'interno dei tipi annidati. Una dichiarazione di tipo annidato può contenere parametri di tipo aggiuntivi che si applicano solo al tipo annidato.

Ogni dichiarazione di tipo contenuta in una dichiarazione di classe generica è implicitamente una dichiarazione di tipo generico. Quando si scrive un riferimento a un tipo annidato all'interno di un tipo generico, il tipo costruito che lo contiene, inclusi i relativi argomenti di tipo, deve essere denominato. Tuttavia, dall'interno della classe esterna, il tipo annidato può essere usato senza qualificazione; il tipo di istanza della classe esterna può essere utilizzato in modo implicito quando si costruisce il tipo annidato. Nell'esempio seguente vengono illustrate tre diversi modi corretti per fare riferimento a un tipo `Inner`costruito creato da. i primi due sono equivalenti:
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

Sebbene non sia uno stile di programmazione valido, un parametro di tipo in un tipo annidato può nascondere un membro o un parametro di tipo dichiarato nel tipo esterno:
```csharp
class Outer<T>
{
    class Inner<T>        // Valid, hides Outer's T
    {
        public T t;       // Refers to Inner's T
    }
}
```

### <a name="reserved-member-names"></a>Nomi di membri riservati

Per semplificare l'implementazione C# della fase di esecuzione sottostante, per ogni dichiarazione del membro di origine che è una proprietà, un evento o un indicizzatore, l'implementazione deve riservare due firme del metodo in base al tipo di dichiarazione del membro, al nome e al tipo. Si tratta di un errore in fase di compilazione per un programma che dichiara un membro la cui firma corrisponde a una di queste firme riservate, anche se l'implementazione della fase di esecuzione sottostante non utilizza tali prenotazioni.

I nomi riservati non introducono dichiarazioni, quindi non partecipano alla ricerca di membri. Tuttavia, le firme del metodo riservato associate a una dichiarazione partecipano all'ereditarietà ([ereditarietà](classes.md#inheritance)) e possono essere nascoste con `new` il modificatore ([il nuovo modificatore](classes.md#the-new-modifier)).

La prenotazione di questi nomi svolge tre scopi:

*  Per consentire all'implementazione sottostante di usare un identificatore comune come nome di metodo per ottenere o impostare l'accesso alla C# funzionalità del linguaggio.
*  Per consentire ad altre lingue di interagire con un identificatore ordinario come nome di metodo per ottenere o impostare l'accesso C# alla funzionalità del linguaggio.
*  Per garantire che l'origine accettata da un compilatore conforme venga accettata da un'altra, rendendo le specifiche dei nomi di membro riservati coerenti in tutte C# le implementazioni.

La dichiarazione di un distruttore ([distruttori](classes.md#destructors)) causa anche la riservatezza di una firma ([nomi dei membri riservati ai distruttori](classes.md#member-names-reserved-for-destructors)).

#### <a name="member-names-reserved-for-properties"></a>Nomi dei membri riservati per le proprietà

Per una proprietà `P` ([proprietà](classes.md#properties)) di tipo `T`, sono riservate le firme seguenti:

```csharp
T get_P();
void set_P(T value);
```

Entrambe le firme sono riservate, anche se la proprietà è di sola lettura o di sola scrittura.

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
una classe `A` definisce una proprietà `P`di sola lettura, riservando in tal modo le `get_P` firme per i metodi e `set_P` . Una classe `B` deriva da `A` e nasconde entrambe le firme riservate. L'esempio produce l'output:
```console
123
123
456
```

#### <a name="member-names-reserved-for-events"></a>Nomi dei membri riservati per gli eventi

Per un evento `E` ([Events](classes.md#events)) del tipo `T`delegato, vengono riservate le firme seguenti:
```csharp
void add_E(T handler);
void remove_E(T handler);
```

#### <a name="member-names-reserved-for-indexers"></a>Nomi dei membri riservati per gli indicizzatori

Per un indicizzatore ([indicizzatori](classes.md#indexers)) di tipo `T` con parameter-list `L`, sono riservate le firme seguenti:
```csharp
T get_Item(L);
void set_Item(L, T value);
```

Entrambe le firme sono riservate, anche se l'indicizzatore è di sola lettura o di sola scrittura.

Il nome `Item` del membro è inoltre riservato.

#### <a name="member-names-reserved-for-destructors"></a>Nomi dei membri riservati per i distruttori

Per una classe che contiene un distruttore ([distruttori](classes.md#destructors)), è riservata la firma seguente:
```csharp
void Finalize();
```

## <a name="constants"></a>Costanti

Una ***costante*** è un membro di classe che rappresenta un valore costante, ovvero un valore che può essere calcolato in fase di compilazione. Un *constant_declaration* introduce una o più costanti di un tipo specificato.

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

Un *constant_declaration* può includere un set di *attributi* ([attributi](attributes.md)), un modificatore `new` ([il nuovo modificatore](classes.md#the-new-modifier)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)). Gli attributi e i modificatori si applicano a tutti i membri dichiarati da *constant_declaration*. Sebbene le costanti siano considerate membri statici, un *constant_declaration* non richiede né consente un modificatore `static`. È un errore che lo stesso modificatore venga visualizzato più volte in una dichiarazione di costante.

Il *tipo* di *constant_declaration* specifica il tipo di membri introdotti dalla dichiarazione. Il tipo è seguito da un elenco di *constant_declarator*, ognuno dei quali introduce un nuovo membro. Un *constant_declarator* è costituito da un *identificatore* che assegna un nome al membro, seguito da un token "`=`", seguito da un *constant_expression* ([espressioni costanti](expressions.md#constant-expressions)) che fornisce il valore del membro.

Il *tipo* specificato in una dichiarazione di costante deve essere `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, 0, 1, 2, 3, 4, *enum_type*o *reference_ digitare*. Ogni *constant_expression* deve restituire un valore del tipo di destinazione o di un tipo che può essere convertito nel tipo di destinazione mediante una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)).

Il *tipo* di una costante deve essere accessibile almeno quanto la costante ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).

Il valore di una costante viene ottenuto in un'espressione usando un *simple_name* ([nomi semplici](expressions.md#simple-names)) o un *member_access* ([accesso ai membri](expressions.md#member-access)).

Una costante può partecipare a un *constant_expression*. Pertanto, una costante può essere usata in qualsiasi costrutto che richiede un *constant_expression*. Esempi di tali costrutti includono `case` etichette, `goto case` istruzioni, `enum` dichiarazioni di membri, attributi e altre dichiarazioni di costanti.

Come descritto in [espressioni costanti](expressions.md#constant-expressions), un *constant_expression* è un'espressione che può essere valutata interamente in fase di compilazione. Poiché l'unico modo per creare un valore non null di un *reference_type* diverso da `string` consiste nell'applicare l'operatore `new` e poiché l'operatore `new` non è consentito in un *constant_expression*, l'unico valore possibile per le costanti di  *il reference_type*s diverso da `string` è `null`.

Quando si desidera un nome simbolico per un valore costante, ma quando il tipo di tale valore non è consentito in una dichiarazione di costante oppure quando il valore non può essere calcolato in fase di compilazione da un *constant_expression*, è possibile che un campo `readonly` (campi di sola[lettura](classes.md#readonly-fields)) in alternativa, usare.

Una dichiarazione di costante che dichiara più costanti è equivalente a più dichiarazioni di costanti singole con gli stessi attributi, modificatori e tipo. Esempio:
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

È consentito che le costanti dipendano da altre costanti all'interno dello stesso programma purché le dipendenze non siano di natura circolare. Il compilatore dispone automaticamente di una valutazione delle dichiarazioni di costanti nell'ordine appropriato. Nell'esempio
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
il `A.Y`compilatore valuta prima di tutto, quindi `B.Z`valuta e infine valuta `A.X`, producendo i valori `10`, `11`e `12`. Le dichiarazioni di costanti possono dipendere da costanti di altri programmi, ma tali dipendenze sono possibili solo in una direzione. Facendo riferimento all'esempio precedente, se `A` e `B` sono stati dichiarati in programmi distinti, `A.X` è `B.Z`possibile che dipenda da, ma `B.Z` potrebbe non dipendere `A.Y`simultaneamente da.

## <a name="fields"></a>Campi

Un ***campo*** è un membro che rappresenta una variabile associata a un oggetto o a una classe. Un *field_declaration* introduce uno o più campi di un tipo specificato.

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

Un *field_declaration* può includere un set di *attributi* ([attributi](attributes.md)), un modificatore `new` ([il nuovo modificatore](classes.md#the-new-modifier)), una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)) e un modificatore `static` ([ Campi statici e di istanza](classes.md#static-and-instance-fields). Inoltre, un *field_declaration* può includere un modificatore `readonly` ([campi](classes.md#readonly-fields)di sola lettura) o un modificatore `volatile` ([campi volatili](classes.md#volatile-fields)), ma non entrambi. Gli attributi e i modificatori si applicano a tutti i membri dichiarati da *field_declaration*. È un errore che lo stesso modificatore venga visualizzato più volte in una dichiarazione di campo.

Il *tipo* di *field_declaration* specifica il tipo di membri introdotti dalla dichiarazione. Il tipo è seguito da un elenco di *variable_declarator*, ognuno dei quali introduce un nuovo membro. Un *variable_declarator* è costituito da un *identificatore* che assegna un nome a tale membro, seguito facoltativamente da un token "`=`" e da un *variable_initializer* ([inizializzatori di variabile](classes.md#variable-initializers)) che fornisce il valore iniziale di tale membro.

Il *tipo* di un campo deve essere accessibile almeno quanto il campo stesso (vincoli di[accessibilità](basic-concepts.md#accessibility-constraints)).

Il valore di un campo viene ottenuto in un'espressione usando un *simple_name* ([nomi semplici](expressions.md#simple-names)) o un *member_access* ([accesso ai membri](expressions.md#member-access)). Il valore di un campo non di sola lettura viene modificato utilizzando un' *assegnazione* ([operatori di assegnazione](expressions.md#assignment-operators)). Il valore di un campo non di sola lettura può essere ottenuto e modificato usando gli operatori di incremento e decremento suffisso ([operatori di incremento e decremento suffisso](expressions.md#postfix-increment-and-decrement-operators)) e gli operatori di incremento e decremento del prefisso ([incremento e decremento prefisso) operatori](expressions.md#prefix-increment-and-decrement-operators)).

Una dichiarazione di campo che dichiara più campi equivale a più dichiarazioni di singoli campi con gli stessi attributi, modificatori e tipo. Esempio:
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

Quando una dichiarazione di campo include `static` un modificatore, i campi introdotti dalla dichiarazione sono ***campi statici***. Quando non `static` è presente alcun modificatore, i campi introdotti dalla dichiarazione sono ***campi di istanza***. I campi statici e di istanza sono due dei diversi tipi di variabili ([variabili](variables.md)) supportati da C#e, a volte, sono definiti rispettivamente come ***variabili statiche*** e ***variabili di istanza***.

Un campo statico non fa parte di un'istanza specifica; viene invece condiviso tra tutte le istanze di un tipo chiuso ([tipi aperti e chiusi](types.md#open-and-closed-types)). Indipendentemente dal numero di istanze di un tipo di classe chiuso, esiste una sola copia di un campo statico per il dominio dell'applicazione associato.

Esempio:
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

Un campo di istanza appartiene a un'istanza di. In particolare, ogni istanza di una classe contiene un set separato di tutti i campi di istanza di tale classe.

Quando si fa riferimento a un campo in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, se `M` è un campo statico, `E` deve indicare un tipo che contiene `M` e se `M` è un campo di istanza, e deve indicare un'istanza di un tipo che contiene `M`.

Le differenze tra i membri statici e i membri di istanza sono illustrate ulteriormente nei [membri statici e di istanza](classes.md#static-and-instance-members).

### <a name="readonly-fields"></a>Campi di sola lettura

Quando un *field_declaration* include un modificatore `readonly`, i campi introdotti dalla dichiarazione sono campi di sola ***lettura***. Le assegnazioni dirette ai campi di sola lettura possono essere eseguite solo come parte della dichiarazione o in un costruttore di istanza o in un costruttore statico nella stessa classe. Un campo di sola lettura può essere assegnato più volte in questi contesti. In particolare, le assegnazioni dirette `readonly` a un campo sono consentite solo nei contesti seguenti:

*  In *variable_declarator* che introduce il campo (includendo un *variable_initializer* nella dichiarazione).
*  Per un campo di istanza, nei costruttori di istanza della classe che contiene la dichiarazione di campo; per un campo statico, nel costruttore statico della classe che contiene la dichiarazione di campo. Questi sono anche gli unici contesti in cui è possibile passare un `readonly` campo `out` come parametro o `ref` .

Il tentativo di assegnare a un `readonly` campo o passarlo `out` come parametro o `ref` in qualsiasi altro contesto è un errore in fase di compilazione.

#### <a name="using-static-readonly-fields-for-constants"></a>Utilizzo di campi di sola lettura statici per le costanti

Un `static readonly` campo è utile quando si desidera un nome simbolico per un valore costante, ma quando il tipo del valore non è consentito in una `const` dichiarazione o quando il valore non può essere calcolato in fase di compilazione. Nell'esempio
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
i `Black`membri `White`,, `Red`, `const` e non`Blue` possono essere dichiarati come membri perché i relativi valori non possono essere calcolati in fase di compilazione. `Green` Tuttavia, dichiarando `static readonly` invece ha lo stesso effetto.

#### <a name="versioning-of-constants-and-static-readonly-fields"></a>Controllo delle versioni di costanti e campi di sola lettura statici

Le costanti e i campi di sola lettura hanno una semantica di controllo delle versioni binaria diversa. Quando un'espressione fa riferimento a una costante, il valore della costante viene ottenuto in fase di compilazione, ma quando un'espressione fa riferimento a un campo di sola lettura, il valore del campo non viene ottenuto fino alla fase di esecuzione. Si consideri un'applicazione costituita da due programmi distinti:
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

Gli `Program1` spazi `Program2` dei nomi e indicano due programmi compilati separatamente. Poiché `Program1.Utils.X` viene dichiarato come un campo statico `Console.WriteLine` di sola lettura, il valore restituito dall'istruzione non è noto in fase di compilazione, ma viene invece ottenuto in fase di esecuzione. Pertanto, se il valore di `X` viene modificato e `Program1` viene ricompilato, l' `Console.WriteLine` istruzione restituirà il nuovo valore anche se `Program2` non viene ricompilato. Tuttavia, era `X` una costante, il valore di `X` sarebbe stato ottenuto al momento `Program2` della compilazione e rimarrebbe inalterato dalle modifiche apportate in `Program1` fino `Program2` a quando non viene ricompilato.

### <a name="volatile-fields"></a>Campi volatili

Quando un *field_declaration* include un modificatore `volatile`, i campi introdotti dalla dichiarazione sono ***campi volatili***.

Per i campi non volatili, le tecniche di ottimizzazione che riordinano le istruzioni possono generare risultati imprevisti e imprevedibili nei programmi multithread che accedono a campi senza sincronizzazione, ad esempio quello fornito da *lock_statement* ([il istruzione lock](statements.md#the-lock-statement)). Queste ottimizzazioni possono essere eseguite dal compilatore, dal sistema della fase di esecuzione o dall'hardware. Per i campi volatili, tali ottimizzazioni di riordino sono limitate:

*  Una lettura di un campo volatile è detta ***lettura volatile***. Una lettura volatile ha una "semantica di acquisizione"; ovvero, è garantita prima di tutti i riferimenti alla memoria che si verificano dopo la sequenza di istruzioni.
*  Una scrittura di un campo volatile è detta ***scrittura volatile***. Una scrittura volatile presenta una "semantica di rilascio"; ovvero, è garantito che avvenga dopo i riferimenti alla memoria prima dell'istruzione di scrittura nella sequenza di istruzioni.

Queste limitazioni garantiscono che tutti i thread considereranno scritture di tipo volatile eseguite da altri thread nell'ordine in cui sono stati eseguiti. Non è necessaria un'implementazione conforme per fornire un singolo ordinamento totale delle scritture volatili, come visto da tutti i thread di esecuzione. Il tipo di un campo volatile deve essere uno dei seguenti:

*  *Reference_type*.
*  `byte`Tipo ,`char` ,,`System.UIntPtr`,,,,,, o. `sbyte` `short` `ushort` `int` `uint` `float` `bool` `System.IntPtr`
*  *Enum_type* con tipo di base enum `byte`, `sbyte`, `short`, `ushort`, `int` o `uint`.

Esempio
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
```console
result = 143
```

In questo esempio, il metodo `Main` avvia un nuovo thread che esegue il metodo `Thread2`. Questo metodo archivia un valore in un campo non volatile denominato `result`, quindi Archivia `true` nel campo `finished`volatile. Il thread principale attende che il campo `finished` sia impostato su `true`, quindi legge il campo `result`. Poiché `finished` è stato dichiarato `volatile`, il thread principale deve leggere il valore `143` dal campo `result`. Se il campo `finished` non è stato dichiarato `volatile`, è possibile che l'archivio `result` sia visibile al thread principale dopo l'archivio in `finished`e quindi affinché il thread principale legga il valore `0` dal campo `result`. `finished` La`volatile` dichiarazione come campo impedisce tale incoerenza.

### <a name="field-initialization"></a>Inizializzazione del campo

Il valore iniziale di un campo, sia che si tratti di un campo statico o di un campo di istanza, è il valore predefinito ([valori predefiniti](variables.md#default-values)) del tipo del campo. Non è possibile osservare il valore di un campo prima che questa inizializzazione predefinita venga eseguita e un campo non viene mai "inizializzato". Esempio
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
```console
b = False, i = 0
```
Poiché `b` e`i` vengono inizializzati automaticamente sui valori predefiniti.

### <a name="variable-initializers"></a>Inizializzatori di variabile

Le dichiarazioni di campo possono includere *variable_initializer*s. Per i campi statici, gli inizializzatori di variabile corrispondono a istruzioni di assegnazione eseguite durante l'inizializzazione della classe. Per i campi di istanza, gli inizializzatori di variabile corrispondono a istruzioni di assegnazione eseguite quando viene creata un'istanza della classe.

Esempio
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
```console
x = 1.4142135623731, i = 100, s = Hello
```
Poiché un'assegnazione `x` si verifica quando gli inizializzatori di campo statici eseguono e assegnazioni `s` a e si verificano quando vengono eseguiti gli inizializzatori di `i` campo dell'istanza.

L'inizializzazione del valore predefinito descritta nell' [inizializzazione del campo](classes.md#field-initialization) si verifica per tutti i campi, inclusi i campi con inizializzatori di variabile. Pertanto, quando una classe viene inizializzata, tutti i campi statici della classe vengono prima inizializzati sui valori predefiniti e quindi gli inizializzatori di campo statici vengono eseguiti in ordine testuale. Allo stesso modo, quando viene creata un'istanza di una classe, tutti i campi di istanza in tale istanza vengono innanzitutto inizializzati sui rispettivi valori predefiniti, quindi gli inizializzatori di campo dell'istanza vengono eseguiti in ordine testuale.

È possibile che i campi statici con inizializzatori di variabile siano osservati nello stato del valore predefinito. Tuttavia, questo è fortemente sconsigliato come una cosa di stile. Esempio
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
presenta questo comportamento. Nonostante le definizioni circolari di a e b, il programma è valido. Produce l'output
```console
a = 1, b = 2
```
Poiché i campi `a` statici e `b` vengono inizializzati `0` su (il valore predefinito `int`per) prima dell'esecuzione degli inizializzatori. Quando l'inizializzatore `a` per viene eseguito, il `b` valore di è zero, `a` quindi viene inizializzato su `1`. Quando l'inizializzatore `b` per viene eseguito, il `a` valore di `1`è già, `b` quindi viene inizializzato su `2`.

#### <a name="static-field-initialization"></a>Inizializzazione campo statico

Gli inizializzatori di variabile di campo statici di una classe corrispondono a una sequenza di assegnazioni che vengono eseguite nell'ordine testuale in cui vengono visualizzate nella dichiarazione di classe. Se nella classe è presente un costruttore statico ([costruttori statici](classes.md#static-constructors)), l'esecuzione degli inizializzatori di campo statici viene eseguita immediatamente prima dell'esecuzione del costruttore statico. In caso contrario, gli inizializzatori di campo statici vengono eseguiti in un tempo dipendente dall'implementazione prima del primo utilizzo di un campo statico di tale classe. Esempio
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
```console
Init A
Init B
1 1
```
o l'output:
```console
Init B
Init A
1 1
```
dato che l'esecuzione `X`dell'inizializzatore `Y`e dell'inizializzatore dell'oggetto può essere eseguito in entrambi i casi, è necessario che si verifichino solo prima dei riferimenti a tali campi. Tuttavia, nell'esempio:
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
```console
Init B
Init A
1 1
```
Poiché le regole per l'esecuzione dei costruttori statici (come definito nei [costruttori statici](classes.md#static-constructors)) forniscono il costruttore `B`statico (e di conseguenza `B`gli inizializzatori di campo statici) che devono essere `A`eseguiti prima di static inizializzatori di costruttori e campi.

#### <a name="instance-field-initialization"></a>Inizializzazione campo istanza

Gli inizializzatori di variabile di campo dell'istanza di una classe corrispondono a una sequenza di assegnazioni che vengono eseguite immediatamente dopo l'immissione in uno dei costruttori di istanza ([inizializzatori di costruttori](classes.md#constructor-initializers)) di tale classe. Gli inizializzatori di variabile vengono eseguiti nell'ordine testuale in cui vengono visualizzati nella dichiarazione della classe. La creazione e il processo di inizializzazione dell'istanza della classe vengono descritti ulteriormente nei [costruttori di istanza](classes.md#instance-constructors).

Un inizializzatore di variabile per un campo di istanza non può fare riferimento all'istanza in fase di creazione. Pertanto, si tratta di un errore in fase di compilazione per fare riferimento a `this` in un inizializzatore di variabile, poiché si tratta di un errore in fase di compilazione per un inizializzatore di variabile che fa riferimento a qualsiasi membro di istanza tramite un *simple_name*. Nell'esempio
```csharp
class A
{
    int x = 1;
    int y = x + 1;        // Error, reference to instance member of this
}
```
l'inizializzatore di `y` variabile per restituisce un errore in fase di compilazione perché fa riferimento a un membro dell'istanza creata.

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

Un *method_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)), il `new` ([il modificatore new](classes.md#the-new-modifier)), `static` ([static e instance Metodi](classes.md#static-and-instance-methods)), `virtual` ([metodi virtuali](classes.md#virtual-methods)), 0 ([metodi di override](classes.md#override-methods)), 2[(Metodi sealed](classes.md#sealed-methods)), 4 ([metodi astratti](classes.md#abstract-methods)) e i modificatori 6 ([metodi esterni](classes.md#external-methods)).

Una dichiarazione ha una combinazione valida di modificatori se si verificano tutte le condizioni seguenti:

*  La dichiarazione include una combinazione valida di modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)).
*  La dichiarazione non include lo stesso modificatore più volte.
*  La dichiarazione include al massimo uno dei modificatori seguenti: `static`, `virtual`e `override`.
*  La dichiarazione include al massimo uno dei modificatori seguenti: `new` e. `override`
*  Se la dichiarazione include il `abstract` modificatore, la dichiarazione non include i modificatori seguenti: `static`, `virtual` `sealed` o `extern`.
*  Se la dichiarazione include il `private` modificatore, la dichiarazione non include i modificatori seguenti: `virtual`, `override`o `abstract`.
*  Se la dichiarazione include il `sealed` modificatore, la dichiarazione include anche il `override` modificatore.
*  Se la dichiarazione include il `partial` modificatore, non include i modificatori seguenti: `new`, `public`, `protected`, `internal`, `private`, `virtual`,, `override` `sealed` , `abstract`o .`extern`

Un metodo con il `async` modificatore è una funzione asincrona e segue le regole descritte in [funzioni asincrone](classes.md#async-functions).

Il *return_type* di una dichiarazione di metodo specifica il tipo di valore calcolato e restituito dal metodo. *Return_type* è `void` se il metodo non restituisce un valore. Se la dichiarazione include il `partial` modificatore, il tipo restituito deve essere `void`.

*MEMBER_NAME* specifica il nome del metodo. A meno che il metodo non sia un'implementazione esplicita di un membro di interfaccia ([implementazioni esplicite di membri di interfaccia](interfaces.md#explicit-interface-member-implementations)), *MEMBER_NAME* è semplicemente un *identificatore*. Per un'implementazione esplicita di un membro di interfaccia, *MEMBER_NAME* è costituito da un *INTERFACE_TYPE* seguito da un "`.`" e da un *identificatore*.

Il *type_parameter_list* facoltativo specifica i parametri di tipo del metodo ([parametri di tipo](classes.md#type-parameters)). Se viene specificato un *type_parameter_list* , il metodo è un ***metodo generico***. Se il metodo ha un modificatore `extern`, non è possibile specificare un *type_parameter_list* .

Il *formal_parameter_list* facoltativo specifica i parametri del metodo ([parametri del metodo](classes.md#method-parameters)).

I *type_parameter_constraints_clause*facoltativi specificano i vincoli sui singoli parametri di tipo ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)) e possono essere specificati solo se viene fornito anche un *type_parameter_list* e il metodo non ha un modificatore `override`.

Il *return_type* e ognuno dei tipi a cui si fa riferimento nel *formal_parameter_list* di un metodo deve essere accessibile almeno quanto il metodo stesso ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).

*Method_body* è un punto e virgola, un ***corpo dell'istruzione*** o un corpo dell' ***espressione***. Il corpo di un'istruzione è costituito da un *blocco*, che specifica le istruzioni da eseguire quando viene richiamato il metodo. Il corpo di `=>` un'espressione è costituito da seguito da un' *espressione* e da un punto e virgola e indica una singola espressione da eseguire quando viene richiamato il metodo. 

Per i metodi `abstract` e `extern`, il *method_body* è costituito semplicemente da un punto e virgola. Per i metodi `partial` è possibile che *method_body* sia costituito da un punto e virgola, da un corpo del blocco o da un corpo dell'espressione. Per tutti gli altri metodi, *method_body* è un corpo del blocco o un corpo dell'espressione.

Se il *method_body* è costituito da un punto e virgola, la dichiarazione non può includere il modificatore `async`.

Il nome, l'elenco dei parametri di tipo e l'elenco di parametri formali di un metodo definiscono la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) del metodo. In particolare, la firma di un metodo è costituita dal nome, dal numero di parametri di tipo e dal numero, dai modificatori e dai tipi dei parametri formali. Per questi scopi, qualsiasi parametro di tipo del metodo che si verifica nel tipo di un parametro formale viene identificato senza il relativo nome, ma in base alla posizione ordinale nell'elenco di argomenti di tipo del metodo. Il tipo restituito non fa parte della firma di un metodo, né i nomi dei parametri di tipo o i parametri formali.

Il nome di un metodo deve essere diverso da quello dei nomi di tutti gli altri metodi non dichiarati nella stessa classe. Inoltre, la firma di un metodo deve essere diversa dalle firme di tutti gli altri metodi dichiarati nella stessa classe e due metodi dichiarati nella stessa classe potrebbero non avere firme che differiscono `ref` solo `out`per e.

Gli *type_parameter*del metodo si trovano nell'ambito di *method_declaration*e possono essere usati per formare i tipi in tutto l'ambito in *return_type*, *method_body*e *type_parameter_constraints_clause*, ma non negli *attributi*.

Tutti i parametri formali e i parametri di tipo devono avere nomi diversi.

### <a name="method-parameters"></a>Parametri del metodo

I parametri di un metodo, se presenti, sono dichiarati dal *formal_parameter_list*del metodo.

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

L'elenco di parametri formali è costituito da uno o più parametri delimitati da virgole di cui solo l'ultimo può essere un *parameter_array*.

Un *fixed_parameter* è costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), un modificatore `ref`, `out` o `this` facoltativo, un *tipo*, un *identificatore* e un *default_argument*facoltativo. Ogni *fixed_parameter* dichiara un parametro del tipo specificato con il nome specificato. Il `this` modificatore designa il metodo come metodo di estensione ed è consentito solo sul primo parametro di un metodo statico. I metodi di estensione sono descritti ulteriormente nei [metodi di estensione](classes.md#extension-methods).

Un *fixed_parameter* con *default_argument* è noto come ***parametro facoltativo***, mentre un *fixed_parameter* senza *default_argument* è un ***parametro obbligatorio***. Un parametro obbligatorio non può essere visualizzato dopo un parametro facoltativo in un *formal_parameter_list*.

Un parametro `ref` o `out` non può avere un *default_argument*. L' *espressione* in una *default_argument* deve essere una delle seguenti:

*  a *constant_expression*
*  espressione nel formato `new S()` in cui `S` è un tipo di valore
*  espressione nel formato `default(S)` in cui `S` è un tipo di valore

L' *espressione* deve essere convertibile in modo implicito da un'identità o Conversione Nullable nel tipo di parametro.

Se i parametri facoltativi si verificano in una dichiarazione di metodo parziale di implementazione ([metodi parziali](classes.md#partial-methods)), un'implementazione esplicita di un membro di interfaccia ([implementazioni esplicite di membri di interfaccia](interfaces.md#explicit-interface-member-implementations)) o in una dichiarazione dell'indicizzatore a parametro singolo ([ Indicizzatori](classes.md#indexers)) il compilatore deve restituire un avviso, poiché questi membri non possono mai essere richiamati in modo che consenta l'omissione degli argomenti.

Un *parameter_array* è costituito da un set facoltativo di *attributi* ([attributi](attributes.md)), da un modificatore `params`, da un *array_type*e da un *identificatore*. Una matrice di parametri dichiara un solo parametro del tipo di matrice specificato con il nome specificato. Il *array_type* di una matrice di parametri deve essere un tipo di matrice unidimensionale ([tipi di matrice](arrays.md#array-types)). In una chiamata al metodo, una matrice di parametri consente di specificare un solo argomento del tipo di matrice specificato oppure di specificare zero o più argomenti del tipo di elemento della matrice. Le matrici di parametri sono descritte ulteriormente in [matrici di parametri](classes.md#parameter-arrays).

Un *parameter_array* può verificarsi dopo un parametro facoltativo, ma non può avere un valore predefinito. l'omissione degli argomenti per un *parameter_array* comporterebbe invece la creazione di una matrice vuota.

Nell'esempio seguente vengono illustrati diversi tipi di parametri:
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

In *formal_parameter_list* per `M` `i` è un parametro ref obbligatorio, `d` è un parametro di valore obbligatorio, `b`, `s`, `o` e `t` sono parametri di valore facoltativi e `a` è una matrice di parametri.

Una dichiarazione di metodo crea uno spazio di dichiarazione separato per parametri, parametri di tipo e variabili locali. I nomi vengono introdotti in questo spazio di dichiarazione dall'elenco di parametri di tipo e dall'elenco di parametri formali del metodo e dalle dichiarazioni di variabili locali nel *blocco* del metodo. Per due membri di uno spazio di dichiarazione di metodo è necessario avere lo stesso nome. È un errore per lo spazio di dichiarazione del metodo e lo spazio di dichiarazione della variabile locale di uno spazio di dichiarazione annidato per contenere elementi con lo stesso nome.

Una chiamata a un metodo ([chiamate al metodo](expressions.md#method-invocations)) crea una copia, specifica della chiamata, dei parametri formali e delle variabili locali del metodo e l'elenco di argomenti della chiamata assegna valori o riferimenti a variabili al nuovo formale creato parametri. All'interno del *blocco* di un metodo è possibile fare riferimento ai parametri formali in base ai relativi identificatori nelle espressioni *Simple_name* ([nomi semplici](expressions.md#simple-names)).

Sono disponibili quattro tipi di parametri formali:

*  Parametri del valore, che vengono dichiarati senza modificatori.
*  Parametri di riferimento, dichiarati con `ref` il modificatore.
*  Parametri di output, dichiarati con `out` il modificatore.
*  Matrici di parametri, dichiarate con il `params` modificatore.

Come descritto in [firme e overload](basic-concepts.md#signatures-and-overloading), i `ref` modificatori e `out` fanno parte della firma di un metodo, ma il modificatore `params` non lo è.

#### <a name="value-parameters"></a>Parametri valore

Un parametro dichiarato senza modificatori è un parametro di valore. Un parametro di valore corrisponde a una variabile locale che ottiene il valore iniziale dall'argomento corrispondente fornito nella chiamata al metodo.

Quando un parametro formale è un parametro di valore, l'argomento corrispondente in una chiamata al metodo deve essere un'espressione convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) nel tipo di parametro formale.

Un metodo è autorizzato a assegnare nuovi valori a un parametro di valore. Tali assegnazioni influiscono solo sul percorso di archiviazione locale rappresentato dal parametro value, ma non hanno alcun effetto sull'argomento effettivo specificato nella chiamata al metodo.

#### <a name="reference-parameters"></a>Parametri di riferimento

Un parametro dichiarato con un `ref` modificatore è un parametro di riferimento. A differenza di un parametro di valore, un parametro di riferimento non crea un nuovo percorso di archiviazione. Un parametro Reference rappresenta invece lo stesso percorso di archiviazione della variabile specificata come argomento nella chiamata al metodo.

Quando un parametro formale è un parametro di riferimento, l'argomento corrispondente in una chiamata al metodo deve essere costituito dalla parola chiave `ref` seguita da un *variable_reference* ([regole precise per determinare l'assegnazione definita](variables.md#precise-rules-for-determining-definite-assignment)) dello stesso tipo di il parametro formale. Una variabile deve essere assegnata definitivamente prima di poter essere passata come parametro di riferimento.

All'interno di un metodo, un parametro di riferimento viene sempre considerato definitivamente assegnato.

Un metodo dichiarato come iteratore ([iteratori](classes.md#iterators)) non può avere parametri di riferimento.

Esempio
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
```console
i = 2, j = 1
```

Per la chiamata di `Swap` in `Main`, `x` rappresenta `i` e `y` rappresenta. `j` Quindi, la chiamata ha l'effetto di scambiare i valori di `i` e. `j`

In un metodo che accetta parametri di riferimento è possibile che più nomi rappresentino lo stesso percorso di archiviazione. Nell'esempio
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
la `F` chiamata di `b`in `G` `s` passa`a` un riferimento a per e. Pertanto, per tale chiamata, i `s`nomi, `a`e `b` fanno riferimento allo stesso percorso di archiviazione e le tre assegnazioni modificano il campo `s`dell'istanza.

#### <a name="output-parameters"></a>Parametri di output

Un parametro dichiarato con un `out` modificatore è un parametro di output. Analogamente a un parametro di riferimento, un parametro di output non crea un nuovo percorso di archiviazione. Un parametro di output rappresenta invece lo stesso percorso di archiviazione della variabile specificata come argomento nella chiamata al metodo.

Quando un parametro formale è un parametro di output, l'argomento corrispondente in una chiamata al metodo deve essere costituito dalla parola chiave `out` seguita da un *variable_reference* ([regole precise per determinare l'assegnazione definita](variables.md#precise-rules-for-determining-definite-assignment)) dello stesso tipo di parametro formale. Una variabile non deve essere definitivamente assegnata prima di poter essere passata come parametro di output, ma dopo una chiamata in cui una variabile è stata passata come parametro di output, la variabile viene considerata assegnata definitivamente.

All'interno di un metodo, come una variabile locale, un parametro di output viene inizialmente considerato non assegnato e deve essere assegnato definitivamente prima di utilizzare il relativo valore.

Ogni parametro di output di un metodo deve essere assegnato definitivamente prima che il metodo venga restituito.

Un metodo dichiarato come metodo parziale ([metodi parziali](classes.md#partial-methods)) o un iteratore ([iteratori](classes.md#iterators)) non può avere parametri di output.

I parametri di output vengono in genere usati nei metodi che producono più valori restituiti. Esempio:
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
```console
c:\Windows\System\
hello.txt
```

Si noti che `dir` le `name` variabili e possono essere non assegnate prima che vengano passate `SplitPath`a e che siano considerate definitivamente assegnate dopo la chiamata.

#### <a name="parameter-arrays"></a>Matrici di parametri

Un parametro dichiarato con un `params` modificatore è una matrice di parametri. Se un elenco di parametri formali include una matrice di parametri, deve essere l'ultimo parametro nell'elenco e deve essere di un tipo di matrice unidimensionale. Ad esempio, i tipi `string[]` e `string[][]` possono essere usati come tipo di una matrice di parametri, ma il tipo `string[,]` non può essere. Non è possibile combinare il `params` modificatore con i `ref` modificatori e `out`.

Una matrice di parametri consente di specificare gli argomenti in uno dei due modi seguenti in una chiamata al metodo:

*  L'argomento specificato per una matrice di parametri può essere una singola espressione convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) nel tipo di matrice di parametri. In questo caso, la matrice di parametri funge esattamente da parametro di valore.
*  In alternativa, la chiamata può specificare zero o più argomenti per la matrice di parametri, dove ogni argomento è un'espressione che è implicitamente convertibile ([conversioni implicite](conversions.md#implicit-conversions)) nel tipo di elemento della matrice di parametri. In questo caso, la chiamata crea un'istanza del tipo di matrice di parametri con una lunghezza corrispondente al numero di argomenti, inizializza gli elementi dell'istanza di matrice con i valori di argomento specificati e usa l'istanza di matrice appena creata come valore effettivo argomento.

Eccetto per consentire un numero variabile di argomenti in una chiamata, una matrice di parametri è esattamente equivalente a un parametro valore ([parametri valore](classes.md#value-parameters)) dello stesso tipo.

Esempio
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
```console
Array contains 3 elements: 1 2 3
Array contains 4 elements: 10 20 30 40
Array contains 0 elements:
```

La prima chiamata di passa `F` semplicemente la matrice `a` come parametro di valore. La seconda chiamata di `F` crea automaticamente un elemento a quattro elementi `int[]` con i valori dell'elemento specificati e passa l'istanza di matrice come parametro di valore. Analogamente, la terza chiamata di `F` crea un elemento `int[]` zero e passa l'istanza come parametro valore. La seconda e la terza chiamata sono esattamente equivalenti alla scrittura:
```csharp
F(new int[] {10, 20, 30, 40});
F(new int[] {});
```

Quando si esegue la risoluzione dell'overload, un metodo con una matrice di parametri può essere applicabile nel formato normale o nella forma espansa ([membro della funzione applicabile](expressions.md#applicable-function-member)). Il form espanso di un metodo è disponibile solo se il formato normale del metodo non è applicabile e solo se un metodo applicabile con la stessa firma del form espanso non è già dichiarato nello stesso tipo.

Esempio
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
```console
F();
F(object[]);
F(object,object);
F(object[]);
F(object[]);
```

Nell'esempio, due delle possibili forme espanse del metodo con una matrice di parametri sono già incluse nella classe come metodi normali. Tali forme espanse pertanto non vengono considerate quando si esegue la risoluzione dell'overload e la prima e la terza chiamata al metodo selezionano quindi i metodi normali. Quando una classe dichiara un metodo con una matrice di parametri, non è insolito includere anche alcuni dei form espansi come metodi normali. In questo modo è possibile evitare l'allocazione di un'istanza di matrice che si verifica quando viene richiamata una forma espansa di un metodo con una matrice di parametri.

Quando il tipo di una matrice di parametri `object[]`è, una potenziale ambiguità si verifica tra il formato normale del metodo e il formato speso per un singolo `object` parametro. Il motivo dell'ambiguità è che un oggetto `object[]` è implicitamente convertibile nel tipo `object`. Tuttavia, l'ambiguità non presenta alcun problema poiché può essere risolta inserendo un cast se necessario.

Esempio
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
```console
System.Int32 System.String System.Double
System.Object[]
System.Object[]
System.Int32 System.String System.Double
```

Nella prima e nell'ultima chiamata di `F`, il formato normale di `F` è applicabile perché esiste una conversione implicita dal tipo di argomento al tipo di parametro (entrambi sono di tipo `object[]`). Pertanto, la risoluzione dell'overload seleziona il formato `F`normale di e l'argomento viene passato come parametro di valore normale. Nella seconda e nella terza chiamata, il formato normale di `F` non è applicabile perché non esiste alcuna conversione implicita dal tipo di argomento al tipo di parametro (il tipo `object` non può essere convertito in modo `object[]`implicito nel tipo). Tuttavia, il form espanso di `F` è applicabile, quindi è selezionato dalla risoluzione dell'overload. Di conseguenza, viene creato un solo elemento `object[]` dalla chiamata e il singolo elemento della matrice viene inizializzato con il valore dell'argomento specificato (che a sua volta è un riferimento a un oggetto `object[]`).

### <a name="static-and-instance-methods"></a>Metodi statici e di istanza

Quando una dichiarazione di metodo include `static` un modificatore, tale metodo viene definito come metodo statico. Quando non `static` è presente alcun modificatore, viene definito un metodo di istanza.

Un metodo statico non agisce su un'istanza specifica e si tratta di un errore in fase di compilazione a cui fare `this` riferimento in un metodo statico.

Un metodo di istanza opera su una determinata istanza di una classe e a tale istanza è possibile accedere `this` come ([questo accesso](expressions.md#this-access)).

Quando si fa riferimento a un metodo in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, se `M` è un metodo statico, `E` deve indicare un tipo contenente `M` e se `M` è un metodo di istanza, `E` deve indicare un'istanza di un tipo contenente `M`.

Le differenze tra i membri statici e i membri di istanza sono illustrate ulteriormente nei [membri statici e di istanza](classes.md#static-and-instance-members).

### <a name="virtual-methods"></a>Metodi virtuali

Quando una dichiarazione di metodo di istanza `virtual` include un modificatore, tale metodo viene definito come metodo virtuale. Quando non `virtual` è presente alcun modificatore, il metodo viene definito come metodo non virtuale.

L'implementazione di un metodo non virtuale è invariante: L'implementazione è la stessa se il metodo viene richiamato su un'istanza della classe in cui è dichiarata o un'istanza di una classe derivata. Al contrario, l'implementazione di un metodo virtuale può essere sostituita dalle classi derivate. Il processo di sostituzione dell'implementazione di un metodo virtuale ereditato è noto come override ***di tale*** metodo ([metodi di override](classes.md#override-methods)).

In una chiamata a un metodo virtuale, il ***tipo di runtime*** dell'istanza per cui viene eseguita la chiamata determina l'implementazione del metodo effettivo da richiamare. In una chiamata a un metodo non virtuale, il ***tipo in fase di compilazione*** dell'istanza è il fattore determinante. In termini precisi, quando un metodo `N` denominato viene richiamato con un `A` elenco di argomenti in un'istanza con un tipo `C` in fase di compilazione e un `R` tipo in `R` fase di `C` esecuzione (dove è o una classe derivata da `C`), la chiamata viene elaborata come indicato di seguito:

*  Per prima cosa, la risoluzione dell' `C`overload `N`viene applicata `A`a, e per selezionare un `M` metodo specifico dal set di metodi dichiarato in e ereditato `C`da. Questa operazione è descritta in [chiamate al metodo](expressions.md#method-invocations).
*  Quindi, se `M` è un metodo non virtuale, `M` viene richiamato.
*  In caso `M` contrario, è un metodo virtuale e viene richiamata l' `M` implementazione più derivata `R` di rispetto a.

Per ogni metodo virtuale dichiarato in o ereditato da una classe, esiste un' ***implementazione più derivata*** del metodo rispetto a tale classe. L'implementazione più derivata di un metodo `M` virtuale rispetto a una classe `R` viene determinata nel modo seguente:

*  Se `R` contiene la Dichiarazione `virtual` di introduzione `M`di, si tratta dell'implementazione più derivata di `M`.
*  In caso contrario `R` , se `override` contiene `M`un valore di, si tratta dell'implementazione più `M`derivata di.
*  In caso contrario, l'implementazione più `M` derivata di rispetto `R` a corrisponde all'implementazione più derivata di `M` rispetto alla classe di base diretta di `R`.

Nell'esempio seguente vengono illustrate le differenze tra i metodi virtuali e non virtuali:
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

Nell'esempio, `A` introduce un metodo `F` non virtuale e un metodo `G`virtuale. La classe `B` introduce un nuovo metodo `F`non virtuale, nascondendo quindi l'oggetto `F`ereditato ed esegue l'override del metodo `G`ereditato. L'esempio produce l'output:
```console
A.F
B.F
B.G
B.G
```

Si noti che l' `a.G()` istruzione `B.G`richiama, non `A.G`. Questo è dovuto al fatto che il tipo in fase di esecuzione dell'istanza `B`(ovvero), non il tipo in fase di compilazione dell'istanza ( `A`ovvero), determina l'implementazione del metodo effettivo da richiamare.

Poiché i metodi possono nascondere i metodi ereditati, è possibile che una classe contenga diversi metodi virtuali con la stessa firma. Questo non presenta un problema di ambiguità, dal momento che tutto tranne il metodo più derivato è nascosto. Nell'esempio
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
le `C` classi `D` e contengono due metodi virtuali con la stessa firma: Quello introdotto da `A` e quello introdotto da `C`. Il metodo introdotto da `C` nasconde il metodo ereditato da. `A` Pertanto, la dichiarazione di override `D` in esegue l'override del `C`metodo introdotto da e non è possibile `D` eseguire l'override del metodo introdotto `A`da. L'esempio produce l'output:
```console
B.F
B.F
D.F
D.F
```

Si noti che è possibile richiamare il metodo virtuale nascosto accedendo a un'istanza di `D` tramite un tipo meno derivato in cui il metodo non è nascosto.

### <a name="override-methods"></a>Metodi di override

Quando una dichiarazione di metodo di istanza `override` include un modificatore, il metodo viene definito ***metodo di override***. Un metodo di override esegue l'override di un metodo virtuale ereditato con la stessa firma. Mentre una dichiarazione di metodo virtuale introduce un nuovo metodo, una dichiarazione di metodo di override specializza un metodo virtuale ereditato esistente specificando una nuova implementazione del metodo.

Il metodo sottoposto a override `override` da una dichiarazione è noto come ***metodo di base sottoposto a override***. Per `M` un metodo di override dichiarato in una `C`classe, il metodo di base sottoposto a override viene determinato esaminando ogni tipo `C`di classe di base di, a partire dal `C` tipo di classe di base diretta di e continuando con ogni operazione successiva tipo di classe di base diretta, finché in un determinato tipo di classe di base viene individuato almeno un metodo accessibile con la `M` stessa firma di dopo la sostituzione degli argomenti di tipo. Al fine di individuare il metodo di base sottoposto a override, un metodo viene considerato accessibile se `public` `protected` `protected internal`è, se è, se è o se `internal` è e dichiarato nello stesso programma di `C`.

Si verifica un errore in fase di compilazione a meno che non siano soddisfatte tutte le condizioni seguenti per una dichiarazione di override:

*  È possibile trovare un metodo di base sottoposto a override come descritto in precedenza.
*  Esiste esattamente un metodo di base sottoposto a override. Questa restrizione ha effetto solo se il tipo di classe base è un tipo costruito in cui la sostituzione di argomenti di tipo rende la firma di due metodi uguali.
*  Il metodo di base sottoposto a override è un metodo virtuale, astratto o di override. In altre parole, il metodo di base sottoposto a override non può essere statico o non virtuale.
*  Il metodo di base sottoposto a override non è un metodo sealed.
*  Il metodo di override e il metodo di base sottoposto a override hanno lo stesso tipo restituito.
*  La dichiarazione di override e il metodo di base sottoposto a override hanno la stessa accessibilità dichiarata. In altre parole, una dichiarazione di override non può modificare l'accessibilità del metodo virtuale. Tuttavia, se il metodo di base sottoposto a override è protetto come interno e viene dichiarato in un assembly diverso rispetto all'assembly che contiene il metodo di override, l'accessibilità dichiarata del metodo di override deve essere protetta.
*  La dichiarazione di override non specifica il parametro di tipo-Constraints-clausole. I vincoli vengono invece ereditati dal metodo di base sottoposto a override. Si noti che i vincoli che sono parametri di tipo nel metodo sottoposto a override possono essere sostituiti da argomenti di tipo nel vincolo ereditato. Questo può causare vincoli che non sono validi quando specificati in modo esplicito, ad esempio tipi di valore o tipi sealed.

Nell'esempio seguente viene illustrato il funzionamento delle regole di override per le classi generiche:
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

Una dichiarazione di override può accedere al metodo di base sottoposto a override utilizzando un *base_access* ([accesso di base](expressions.md#base-access)). Nell'esempio
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
la `base.PrintFields()` chiamata in `B` richiama il `PrintFields` metodo dichiarato in `A`. Un *base_access* Disabilita il meccanismo di chiamata virtuale e considera semplicemente il metodo di base come metodo non virtuale. Se la `B` chiamata è stata scritta `((A)this).PrintFields()`, richiamerebbe in modo ricorsivo `PrintFields` il metodo dichiarato `B`in, non quello dichiarato in `A`, poiché `PrintFields` è virtuale e il tipo in fase di esecuzione di `((A)this)` è .`B`

Solo se si include `override` un modificatore, un metodo esegue l'override di un altro metodo. In tutti gli altri casi, un metodo con la stessa firma di un metodo ereditato nasconde semplicemente il metodo ereditato. Nell'esempio
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
il `F` metodo in `B` non include un `override` modificatore e pertanto non esegue l'override `F` del metodo `A`in. Il `F` metodo in `B` nasconde invece il metodo in `A`e viene segnalato un avviso perché la dichiarazione non include un `new` modificatore.

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
il `F` metodo in `B` nasconde il metodo virtuale `F` ereditato da `A`. Poiché il nuovo `F` in `B` ha accesso privato, l'ambito include solo il corpo della `B` classe e non si estende a `C`. Pertanto, la dichiarazione di `F` in `C` è consentita per `F` eseguire l' `A`override dell'oggetto ereditato da.

### <a name="sealed-methods"></a>Metodi sealed

Quando una dichiarazione di metodo di istanza `sealed` include un modificatore, tale metodo viene definito come ***metodo sealed***. Se una dichiarazione di metodo di istanza `sealed` include il modificatore, deve includere `override` anche il modificatore. L' `sealed` utilizzo del modificatore impedisce a una classe derivata di eseguire l'override del metodo.

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
la classe `B` fornisce due metodi di override: `F` un metodo con il `sealed` modificatore e `G` un metodo che non lo è. `B`l'uso del sealed `modifier` impedisce `C` di eseguire ulteriori override `F`.

### <a name="abstract-methods"></a>Metodi astratti

Quando una dichiarazione di metodo di istanza `abstract` include un modificatore, tale metodo viene definito ***metodo astratto***. Anche se un metodo astratto è implicitamente un metodo virtuale, non può avere il modificatore `virtual`.

Una dichiarazione di metodo astratto introduce un nuovo metodo virtuale ma non fornisce un'implementazione di tale metodo. Al contrario, le classi derivate non astratte sono necessarie per fornire la propria implementazione eseguendo l'override del metodo. Poiché un metodo astratto non fornisce alcuna implementazione effettiva, il *method_body* di un metodo astratto è costituito semplicemente da un punto e virgola.

Le dichiarazioni di metodo abstract sono consentite solo nelle classi astratte ([classi astratte](classes.md#abstract-classes)).

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
la `Shape` classe definisce la nozione astratta di un oggetto Shape geometrico che può disegnare se stesso. Il `Paint` metodo è astratto perché non esiste un'implementazione predefinita significativa. Le `Ellipse` classi `Box` e sono implementazioni concrete `Shape` . Poiché queste classi sono non astratte, è necessario eseguire l'override del `Paint` metodo e fornire un'implementazione effettiva.

Si tratta di un errore in fase di compilazione per un *base_access* ([accesso di base](expressions.md#base-access)) per fare riferimento a un metodo astratto. Nell'esempio
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
viene segnalato un errore in fase di compilazione per `base.F()` la chiamata, perché fa riferimento a un metodo astratto.

Una dichiarazione di metodo astratto è consentita per eseguire l'override di un metodo virtuale. Ciò consente a una classe astratta di forzare la riimplementazione del metodo nelle classi derivate e rende non disponibile l'implementazione originale del metodo. Nell'esempio
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
la `A` classe dichiara un metodo virtuale, la `B` classe esegue l'override di questo metodo con un metodo `C` astratto e la classe esegue l'override del metodo astratto per fornire la propria implementazione.

### <a name="external-methods"></a>Metodi esterni

Quando una dichiarazione di metodo include `extern` un modificatore, viene definito un metodo ***esterno***. I metodi esterni vengono implementati esternamente, in genere utilizzando un C#linguaggio diverso da. Poiché una dichiarazione di metodo esterno non fornisce alcuna implementazione effettiva, il *method_body* di un metodo esterno è semplicemente costituito da un punto e virgola. Un metodo esterno potrebbe non essere generico.

Il `extern` modificatore viene in genere usato in combinazione `DllImport` con un attributo ([interoperabilità con componenti com e Win32](attributes.md#interoperation-with-com-and-win32-components)), consentendo l'implementazione di metodi esterni tramite dll (librerie a collegamento dinamico). L'ambiente di esecuzione può supportare altri meccanismi in base ai quali è possibile fornire implementazioni di metodi esterni.

Quando un metodo esterno include un `DllImport` attributo, la dichiarazione del metodo deve includere anche `static` un modificatore. Questo esempio illustra l'uso del `extern` modificatore e dell' `DllImport` attributo:
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

Quando una dichiarazione di metodo include `partial` un modificatore, tale metodo viene definito come ***metodo parziale***. I metodi parziali possono essere dichiarati solo come membri di tipi parziali ([tipi parziali](classes.md#partial-types)) e sono soggetti a una serie di restrizioni. I metodi parziali sono descritti ulteriormente in [metodi parziali](classes.md#partial-methods).

### <a name="extension-methods"></a>Metodi di estensione

Quando il primo parametro di un metodo include il `this` modificatore, tale metodo viene definito come ***metodo di estensione***. I metodi di estensione possono essere dichiarati solo in classi statiche non generiche non nidificate. Il primo parametro di un metodo di estensione non può avere modificatori diversi `this`da e il tipo di parametro non può essere un tipo di puntatore.

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

Un metodo di estensione è un metodo statico normale. Inoltre, quando la classe statica che lo contiene è nell'ambito, un metodo di estensione può essere richiamato usando la sintassi di chiamata del metodo di istanza ([chiamate al metodo di estensione](expressions.md#extension-method-invocations)), usando l'espressione Receiver come primo argomento.

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

Il `Slice` metodo è disponibile `string[]`in e il `ToInt32` metodo è disponibile in `string`, perché sono stati dichiarati come metodi di estensione. Il significato del programma è identico a quello riportato di seguito, usando le normali chiamate al metodo statico:
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

Il *method_body* di una dichiarazione di metodo è costituito da un corpo del blocco, da un corpo dell'espressione o da un punto e virgola.

Il ***tipo di risultato*** di un metodo `void` è se il tipo restituito `void`è o se il metodo è asincrono e il tipo restituito è `System.Threading.Tasks.Task`. In caso contrario, il tipo di risultato di un metodo non asincrono è il tipo restituito e il tipo di risultato di un metodo asincrono con `System.Threading.Tasks.Task<T>` tipo `T`restituito è.

Quando un metodo ha un `void` tipo di risultato e un corpo del `return` blocco,[le istruzioni (istruzione return](statements.md#the-return-statement)) nel blocco non sono autorizzate a specificare un'espressione. Se l'esecuzione del blocco di un metodo void viene completata normalmente (ovvero, il controllo esce dalla fine del corpo del metodo), il metodo restituisce semplicemente al chiamante corrente.
    
Quando un metodo ha un risultato `void` e un corpo dell'espressione, l'espressione `E` deve essere un *statement_expression*e il corpo è esattamente equivalente a un corpo del blocco nel formato `{ E; }`.
    
Quando un metodo ha un tipo di risultato non void e un corpo del blocco, `return` ogni istruzione nel blocco deve specificare un'espressione convertibile in modo implicito nel tipo di risultato. L'endpoint di un corpo di blocco di un metodo di restituzione di valori non deve essere raggiungibile. In altre parole, in un metodo di restituzione di valori con un corpo del blocco, il controllo non può propagarsi alla fine del corpo del metodo.
    
Quando un metodo ha un tipo di risultato non void e un corpo dell'espressione, l'espressione deve essere convertibile in modo implicito nel tipo di risultato e il corpo è esattamente equivalente a un corpo del `{ return E; }`blocco del form.
    
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
il metodo che restituisce `F` il valore genera un errore in fase di compilazione perché il controllo può propagarsi alla fine del corpo del metodo. I `G` metodi `H` e sono corretti perché tutti i possibili percorsi di esecuzione terminano in un'istruzione return che specifica un valore restituito. Il `I` metodo è corretto, perché il corpo è equivalente a un blocco di istruzioni con una sola istruzione return.

### <a name="method-overloading"></a>Overload di un metodo

Le regole di risoluzione dell'overload del metodo sono descritte in [inferenza del tipo](expressions.md#type-inference).

## <a name="properties"></a>Proprietà

Una ***Proprietà*** è un membro che fornisce l'accesso a una caratteristica di un oggetto o di una classe. Esempi di proprietà includono la lunghezza di una stringa, la dimensione di un tipo di carattere, la didascalia di una finestra, il nome di un cliente e così via. Le proprietà sono un'estensione naturale dei campi, entrambi sono membri denominati con tipi associati e la sintassi per l'accesso a campi e proprietà è la stessa. A differenza dei campi, tuttavia, le proprietà non denotano posizioni di memoria, ma hanno ***funzioni di accesso*** che specificano le istruzioni da eseguire nel momento in cui ne vengono letti o scritti i valori. Le proprietà forniscono pertanto un meccanismo per associare le azioni alla lettura e alla scrittura degli attributi di un oggetto. consentono inoltre di calcolare tali attributi.

Le proprietà vengono dichiarate usando *property_declaration*s:

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

Un *property_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)), il `new` ([il modificatore new](classes.md#the-new-modifier)), `static` ([static e instance Metodi](classes.md#static-and-instance-methods)), `virtual` ([metodi virtuali](classes.md#virtual-methods)), 0 ([metodi di override](classes.md#override-methods)), 2[(Metodi sealed](classes.md#sealed-methods)), 4 ([metodi astratti](classes.md#abstract-methods)) e i modificatori 6 ([metodi esterni](classes.md#external-methods)).

Le dichiarazioni di proprietà sono soggette alle stesse regole delle dichiarazioni di metodo ([Metodi](classes.md#methods)) per quanto concerne combinazioni valide di modificatori.

Il *tipo* di una dichiarazione di proprietà specifica il tipo della proprietà introdotta dalla dichiarazione e *MEMBER_NAME* specifica il nome della proprietà. A meno che la proprietà non sia un'implementazione esplicita di un membro di interfaccia, *MEMBER_NAME* è semplicemente un *identificatore*. Per un'implementazione esplicita di un membro di interfaccia ([implementazioni esplicite di membri di interfaccia](interfaces.md#explicit-interface-member-implementations)), *MEMBER_NAME* è costituito da un *interface_type* seguito da un "`.`" e da un *identificatore*.

Il *tipo* di una proprietà deve essere accessibile almeno quanto la proprietà stessa (vincoli di[accessibilità](basic-concepts.md#accessibility-constraints)).

Un *property_body* può essere costituito da un ***corpo della funzione di accesso*** o da un corpo di ***espressione***. In un corpo della funzione di accesso, *accessor_declarations*, che deve essere racchiuso tra token "`{`" e "`}`", dichiarare le funzioni di accesso ([funzioni di accesso](classes.md#accessors)) della proprietà. Le funzioni di accesso specificano le istruzioni eseguibili associate alla lettura e alla scrittura della proprietà.

Il `=>` corpo di un'espressione costituito da seguito da un' *espressione* `E` e da un punto e virgola è `{ get { return E; } }`esattamente equivalente al corpo dell'istruzione e pertanto può essere utilizzato solo per specificare le proprietà del solo metodo Get in cui il risultato di il getter viene fornito da un'unica espressione.

Un *property_initializer* può essere specificato solo per una proprietà implementata automaticamente ([proprietà implementate automaticamente](classes.md#automatically-implemented-properties)) e causa l'inizializzazione del campo sottostante di tali proprietà con il valore fornito dall' *espressione* .

Anche se la sintassi per l'accesso a una proprietà è identica a quella di un campo, una proprietà non viene classificata come variabile. Non è quindi possibile passare una proprietà come `ref` argomento o. `out`

Quando una dichiarazione di proprietà include `extern` un modificatore, la proprietà viene definita come ***proprietà esterna***. Poiché una dichiarazione di proprietà esterna non fornisce alcuna implementazione effettiva, ogni *accessor_declarations* è costituito da un punto e virgola.

### <a name="static-and-instance-properties"></a>Proprietà statiche e di istanza

Quando una dichiarazione di proprietà include `static` un modificatore, la proprietà viene definita come ***proprietà statica***. Quando non `static` è presente alcun modificatore, la proprietà viene definita come una ***proprietà dell'istanza***.

Una proprietà statica non è associata a un'istanza specifica e si tratta di un errore `this` in fase di compilazione a cui fare riferimento nelle funzioni di accesso di una proprietà statica.

Una proprietà dell'istanza è associata a una determinata istanza di una classe e a tale istanza è possibile accedere `this` come ([questo accesso](expressions.md#this-access)) nelle funzioni di accesso di tale proprietà.

Quando si fa riferimento a una proprietà in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, se `M` è una proprietà statica, `E` deve indicare un tipo che contiene `M` e se `M` è una proprietà dell'istanza, e deve indicare un'istanza di un tipo contenente `M`.

Le differenze tra i membri statici e i membri di istanza sono illustrate ulteriormente nei [membri statici e di istanza](classes.md#static-and-instance-members).

### <a name="accessors"></a>Funzioni

*Accessor_declarations* di una proprietà specificano le istruzioni eseguibili associate alla lettura e alla scrittura della proprietà.

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

Le dichiarazioni delle funzioni di accesso sono costituite da un *get_accessor_declaration*, un *set_accessor_declaration*o entrambi. Ogni dichiarazione di funzione di accesso è costituita dal token `get` o `set` seguito da un *accessor_modifier* facoltativo e da un *accessor_body*.

L'uso di *accessor_modifier*s è regolato dalle restrizioni seguenti:

*  Non è possibile usare *accessor_modifier* in un'interfaccia o in un'implementazione esplicita di un membro di interfaccia.
*  Per una proprietà o un indicizzatore senza modificatore `override`, un *accessor_modifier* è consentito solo se la proprietà o l'indicizzatore ha entrambe le funzioni di accesso `get` e `set`, quindi è consentito solo in una di queste funzioni di accesso.
*  Per una proprietà o un indicizzatore che include un modificatore `override`, una funzione di accesso deve corrispondere al *accessor_modifier*, se presente, della funzione di accesso sottoposta a override.
*  *Accessor_modifier* deve dichiarare un'accessibilità strettamente più restrittiva dell'accessibilità dichiarata della proprietà o dell'indicizzatore stesso. Per essere precisi:
   * Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata di `public`, il valore di *accessor_modifier* può essere `protected internal`, `internal`, `protected` o `private`.
   * Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata di `protected internal`, il valore di *accessor_modifier* può essere `internal`, `protected` o `private`.
   * Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata di `internal` o `protected`, il *accessor_modifier* deve essere `private`.
   * Se la proprietà o l'indicizzatore ha un'accessibilità dichiarata di `private`, non è possibile usare *accessor_modifier* .

Per le proprietà `abstract` e `extern`, il *accessor_body* per ogni funzione di accesso specificata è semplicemente un punto e virgola. Una proprietà non astratta e non extern può includere ogni *accessor_body* un punto e virgola, nel qual caso si tratta di una ***proprietà implementata automaticamente*** ([proprietà implementate automaticamente](classes.md#automatically-implemented-properties)). Una proprietà implementata automaticamente deve avere almeno una funzione di accesso get. Per le funzioni di accesso di qualsiasi altra proprietà non astratta e non extern, *accessor_body* è un *blocco* che specifica le istruzioni da eseguire quando viene richiamata la funzione di accesso corrispondente.

Una `get` funzione di accesso corrisponde a un metodo senza parametri con un valore restituito del tipo di proprietà. Fatta eccezione per la destinazione di un'assegnazione, quando si fa riferimento a una proprietà in un'espressione `get` , viene richiamata la funzione di accesso della proprietà per calcolare il valore della proprietà ([valori di espressioni](expressions.md#values-of-expressions)). Il corpo di una `get` funzione di accesso deve essere conforme alle regole per i metodi di restituzione di valori descritti nel [corpo del metodo](classes.md#method-body). In particolare, tutte `return` le istruzioni nel corpo di una `get` funzione di accesso devono specificare un'espressione convertibile in modo implicito nel tipo della proprietà. Inoltre, l'endpoint di una `get` funzione di accesso non deve essere raggiungibile.

Una `set` funzione di accesso corrisponde a un metodo con un singolo parametro di valore del tipo di `void` proprietà e un tipo restituito. Il parametro implicito di `set` una funzione di accesso `value`è sempre denominato. Quando si fa riferimento a una proprietà come destinazione di un'assegnazione ([operatori di assegnazione](expressions.md#assignment-operators)) o come operando di `++` o `--` ([operatori di incremento e decremento suffisso](expressions.md#postfix-increment-and-decrement-operators), [operatori di incremento e decremento del prefisso](expressions.md#prefix-increment-and-decrement-operators)), il la funzione di accesso viene richiamata con un argomento (il cui valore è quello del lato destro dell'assegnazione o l'operando `++` dell' `--` operatore OR) che fornisce il nuovo valore ([assegnazione semplice](expressions.md#simple-assignment)). `set` Il corpo di una `set` funzione di accesso deve essere conforme `void` alle regole per i metodi descritti nel [corpo del metodo](classes.md#method-body). In particolare, `return` le istruzioni nel `set` corpo della funzione di accesso non sono autorizzate a specificare un'espressione. Poiché una `set` funzione di accesso include implicitamente un `value`parametro denominato, si tratta di un errore in fase di compilazione per una variabile locale o `set` una dichiarazione di costante in una funzione di accesso con tale nome.

In base alla presenza o all'assenza delle `get` funzioni `set` di accesso e, una proprietà viene classificata come segue:

*  Una proprietà che include sia `get` una funzione di accesso `set` sia una funzione di accesso è detta proprietà di ***lettura/scrittura*** .
*  Una proprietà che ha solo una `get` funzione di accesso è detta proprietà di ***sola lettura*** . Si tratta di un errore in fase di compilazione perché una proprietà di sola lettura sia la destinazione di un'assegnazione.
*  Una proprietà che ha solo una `set` funzione di accesso è detta proprietà di ***sola scrittura*** . Fatta eccezione per la destinazione di un'assegnazione, si tratta di un errore in fase di compilazione per fare riferimento a una proprietà di sola scrittura in un'espressione.

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
il `Button` controllo dichiara una proprietà pubblica `Caption` . La `get` `caption` funzione`Caption` di accesso della proprietà restituisce la stringa archiviata nel campo privato. La `set` funzione di accesso controlla se il nuovo valore è diverso dal valore corrente e, in tal caso, archivia il nuovo valore e ridisegna il controllo. Le proprietà seguono spesso il modello illustrato in precedenza: La `get` funzione di accesso restituisce semplicemente un valore archiviato in un campo privato e `set` la funzione di accesso modifica tale campo privato e quindi esegue le azioni aggiuntive necessarie per aggiornare completamente lo stato dell'oggetto.

Data la `Button` classe precedente, di seguito è riportato un esempio di utilizzo `Caption` della proprietà:
```csharp
Button okButton = new Button();
okButton.Caption = "OK";            // Invokes set accessor
string s = okButton.Caption;        // Invokes get accessor
```

In questo caso `set` , la funzione di accesso viene richiamata assegnando un valore alla proprietà `get` e la funzione di accesso viene richiamata facendo riferimento alla proprietà in un'espressione.

Le `get` funzioni `set` di accesso e di una proprietà non sono membri distinti e non è possibile dichiarare separatamente le funzioni di accesso di una proprietà. Di conseguenza, non è possibile per le due funzioni di accesso di una proprietà di lettura/scrittura avere un'accessibilità diversa. Esempio
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
non dichiara una singola proprietà di lettura/scrittura. Dichiara invece due proprietà con lo stesso nome, una sola lettura e una sola scrittura. Poiché due membri dichiarati nella stessa classe non possono avere lo stesso nome, nell'esempio viene generato un errore in fase di compilazione.

Quando una classe derivata dichiara una proprietà con lo stesso nome di una proprietà ereditata, la proprietà derivata nasconde la proprietà ereditata in relazione alla lettura e alla scrittura. Nell'esempio
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
la `P` proprietà in `B` nasconde la `P` proprietà in `A` rispetto alla lettura e alla scrittura. Quindi, nelle istruzioni
```csharp
B b = new B();
b.P = 1;          // Error, B.P is read-only
((A)b).P = 1;     // Ok, reference to A.P
```
l'assegnazione a `b.P` causa la segnalazione di un errore in fase di compilazione, poiché la `P` proprietà di sola lettura `B` in nasconde la proprietà di sola `P` scrittura in `A`. Si noti, tuttavia, che è possibile usare un cast per accedere alla `P` proprietà Hidden.

A differenza dei campi pubblici, le proprietà forniscono una separazione tra lo stato interno di un oggetto e la relativa interfaccia pubblica. Si consideri l'esempio seguente:
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

Qui, la `Label` classe usa due `int` campi, `x` e `y`, per archiviare la relativa posizione. Il percorso è esposto pubblicamente sia come `X` `Y` proprietà che come proprietà e come `Location` proprietà di tipo `Point`. Se, in una versione futura di `Label`, diventa più comodo archiviare la posizione `Point` come internamente, è possibile apportare la modifica senza influire sull'interfaccia pubblica della classe:
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

Aveva `x` invece campi,`Label` sarebbe stato impossibile apportare tale modifica alla classe. `public readonly` `y`

L'esposizione dello stato attraverso le proprietà non è necessariamente meno efficiente rispetto all'esposizione diretta dei campi. In particolare, quando una proprietà non è virtuale e contiene solo una piccola quantità di codice, l'ambiente di esecuzione può sostituire le chiamate alle funzioni di accesso con il codice effettivo delle funzioni di accesso. Questo processo è noto come ***incorporamento***e rende l'accesso alle proprietà efficiente come l'accesso al campo, ma conserva la maggiore flessibilità delle proprietà.

Poiché la chiamata di `get` una funzione di accesso è concettualmente equivalente alla lettura del valore di un campo, viene considerato uno stile `get` di programmazione non valido per le funzioni di accesso che hanno effetti collaterali osservabili. Nell'esempio
```csharp
class Counter
{
    private int next;

    public int Next {
        get { return next++; }
    }
}
```
il valore della `Next` proprietà dipende dal numero di volte in cui è stato eseguito l'accesso alla proprietà. Pertanto, l'accesso alla proprietà produce un effetto collaterale osservabile e la proprietà deve essere implementata come metodo.

La convenzione "nessun effetto collaterale" per `get` le funzioni di accesso non `get` significa che le funzioni di accesso devono essere sempre scritte in modo da restituire semplicemente i valori archiviati nei campi. `get` Le funzioni di accesso spesso calcolano il valore di una proprietà tramite l'accesso a più campi o la chiamata di metodi. Tuttavia, una funzione di `get` accesso progettata correttamente non esegue alcuna azione che causi modifiche osservabili nello stato dell'oggetto.

Le proprietà possono essere usate per ritardare l'inizializzazione di una risorsa fino al momento in cui viene fatto riferimento per la prima volta. Esempio:
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

La `Console` classe contiene tre proprietà `Out`, `In`, e `Error`, che rappresentano rispettivamente i dispositivi di input, di output e di errore standard. Esponendo tali membri come proprietà, è `Console` possibile che la classe ritardi l'inizializzazione fino a quando non vengono effettivamente utilizzati. Ad esempio, al primo riferimento alla `Out` proprietà, come in
```csharp
Console.Out.WriteLine("hello, world");
```
viene creato `TextWriter` il sottostante per il dispositivo di output. Tuttavia, se l'applicazione non fa riferimento alle `In` proprietà `Error` e, non viene creato alcun oggetto per tali dispositivi.

### <a name="automatically-implemented-properties"></a>Proprietà implementate automaticamente

Una proprietà implementata automaticamente (o una ***proprietà automatica*** per Short), è una proprietà non-extern non astratta con corpi delle funzioni di accesso solo punto e virgola. Le proprietà automatiche devono avere una funzione di accesso get e possono facoltativamente avere una funzione di accesso set.

Quando una proprietà viene specificata come proprietà implementata automaticamente, un campo sottostante nascosto è automaticamente disponibile per la proprietà e le funzioni di accesso sono implementate per la lettura e la scrittura in quel campo sottostante. Se la proprietà auto non dispone di una funzione di accesso set, il campo sottostante `readonly` viene considerato (campi di sola[lettura](classes.md#readonly-fields)). Analogamente a `readonly` un campo, è anche possibile assegnare una proprietà auto solo Get a nel corpo di un costruttore della classe contenitore. Tale assegnazione viene assegnata direttamente al campo di supporto ReadOnly della proprietà.

Una proprietà automatica può facoltativamente avere un *property_initializer*, che viene applicato direttamente al campo sottostante come *variable_initializer* ([inizializzatori di variabile](classes.md#variable-initializers)).

L'esempio seguente:
```csharp
public class Point {
    public int X { get; set; } = 0;
    public int Y { get; set; } = 0;
}
```
equivale alla dichiarazione seguente:
```csharp
public class Point {
    private int __x = 0;
    private int __y = 0;
    public int X { get { return __x; } set { __x = value; } }
    public int Y { get { return __y; } set { __y = value; } }
}
```

L'esempio seguente:
```csharp
public class ReadOnlyPoint
{
    public int X { get; }
    public int Y { get; }
    public ReadOnlyPoint(int x, int y) { X = x; Y = y; }
}
```
equivale alla dichiarazione seguente:
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

Si noti che le assegnazioni al campo ReadOnly sono valide, perché si verificano all'interno del costruttore.


### <a name="accessibility"></a>Accessibilità

Se una funzione di accesso dispone di un *accessor_modifier*, il dominio di accessibilità ([domini di accessibilità](basic-concepts.md#accessibility-domains)) della funzione di accesso viene determinato mediante l'accessibilità dichiarata di *accessor_modifier*. Se una funzione di accesso non dispone di un *accessor_modifier*, il dominio di accessibilità della funzione di accesso è determinato dall'accessibilità dichiarata della proprietà o dell'indicizzatore.

La presenza di un *accessor_modifier* non influiscono mai sulla ricerca dei membri ([operatori](expressions.md#operators)) o sulla risoluzione dell'overload ([risoluzione dell'overload](expressions.md#overload-resolution)). I modificatori della proprietà o dell'indicizzatore determinano sempre la proprietà o l'indicizzatore a cui è associato, indipendentemente dal contesto dell'accesso.

Una volta selezionata una proprietà o un indicizzatore specifico, vengono utilizzati i domini di accessibilità delle funzioni di accesso specifiche per determinare se l'utilizzo è valido:

*  Se l'utilizzo è come valore ([valori di espressioni](expressions.md#values-of-expressions)), la `get` funzione di accesso deve esistere ed essere accessibile.
*  Se l'utilizzo è come destinazione di un'assegnazione semplice ([assegnazione semplice](expressions.md#simple-assignment)), la `set` funzione di accesso deve esistere ed essere accessibile.
*  Se l'utilizzo è come destinazione dell'assegnazione composta ([assegnazione composta](expressions.md#compound-assignment)) o come destinazione degli operatori `++` or `--` (,9[membri di funzione](expressions.md#function-members), espressioni di [chiamata](expressions.md#invocation-expressions)), entrambe le `get` funzioni di accesso e la `set` funzione di accesso deve esistere ed essere accessibile.

Nell'esempio seguente la proprietà `A.Text` è nascosta dalla proprietà `B.Text`, anche in contesti in cui viene chiamata solo la `set` funzione di accesso. Al contrario, la proprietà `B.Count` non è accessibile alla classe `M`, pertanto viene invece utilizzata `A.Count` la proprietà accessibile.

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

Una funzione di accesso usata per implementare un'interfaccia potrebbe non avere un *accessor_modifier*. Se per implementare un'interfaccia viene usata una sola funzione di accesso, l'altra funzione di accesso può essere dichiarata con un *accessor_modifier*:
```csharp
public interface I
{
    string Prop { get; }
}

public class C: I
{
    public string Prop {
        get { return "April"; }       // Must not have a modifier here
        internal set {...}            // Ok, because I.Prop has no set accessor
    }
}
```

### <a name="virtual-sealed-override-and-abstract-property-accessors"></a>Funzioni di accesso alle proprietà virtuali, sealed, override e astratte

Una `virtual` dichiarazione di proprietà specifica che le funzioni di accesso della proprietà sono virtuali. Il `virtual` modificatore si applica a entrambe le funzioni di accesso di una proprietà di lettura/scrittura. non è possibile che una sola funzione di accesso di una proprietà di lettura/scrittura sia virtuale.

Una `abstract` dichiarazione di proprietà specifica che le funzioni di accesso della proprietà sono virtuali, ma non fornisce un'implementazione effettiva delle funzioni di accesso. Al contrario, le classi derivate non astratte sono necessarie per fornire la propria implementazione per le funzioni di accesso eseguendo l'override della proprietà. Poiché una funzione di accesso per una dichiarazione di proprietà astratta non fornisce alcuna implementazione effettiva, il relativo *accessor_body* è semplicemente costituito da un punto e virgola.

Una dichiarazione di proprietà che include entrambi `abstract` i `override` modificatori e specifica che la proprietà è astratta ed esegue l'override di una proprietà di base. Anche le funzioni di accesso di tale proprietà sono astratte.

Le dichiarazioni di proprietà astratte sono consentite solo nelle classi astratte ([classi astratte](classes.md#abstract-classes)). È possibile eseguire l'override delle funzioni di accesso di una proprietà virtuale ereditata in una classe derivata includendo una dichiarazione di `override` proprietà che specifica una direttiva. Questa operazione è nota come ***dichiarazione di proprietà che esegue l'override***. Una dichiarazione di proprietà che esegue l'override non dichiara una nuova proprietà. Ma semplicemente specializza le implementazioni delle funzioni di accesso di una proprietà virtuale esistente.

Una dichiarazione di proprietà che esegue l'override deve specificare esattamente gli stessi modificatori di accessibilità, tipo e nome della proprietà ereditata. Se la proprietà ereditata ha solo una singola funzione di accesso (ad esempio, se la proprietà ereditata è di sola lettura o di sola scrittura), la proprietà che esegue l'override deve includere solo tale funzione di accesso. Se la proprietà ereditata include entrambe le funzioni di accesso (ad esempio, se la proprietà ereditata è di lettura/scrittura), la proprietà che esegue l'override può includere una singola funzione di accesso o entrambe le funzioni di accesso.

Una dichiarazione di proprietà che esegue l'override `sealed` può includere il modificatore. L'uso di questo modificatore impedisce a una classe derivata di eseguire ulteriormente l'override della proprietà. Anche le funzioni di accesso di una proprietà sealed sono sealed.

Ad eccezione delle differenze nella sintassi della dichiarazione e della chiamata, le funzioni di accesso Virtual, sealed, override e abstract si comportano esattamente come i metodi virtual, sealed, override e abstract. In particolare, le regole descritte in [metodi virtuali](classes.md#virtual-methods), [metodi di override](classes.md#override-methods), [Metodi sealed](classes.md#sealed-methods)e [metodi astratti](classes.md#abstract-methods) si applicano come se le funzioni di accesso fossero metodi di un form corrispondente:

*  Una `get` funzione di accesso corrisponde a un metodo senza parametri con un valore restituito del tipo di proprietà e gli stessi modificatori della proprietà che lo contiene.
*  Una `set` funzione di accesso corrisponde a un metodo con un singolo parametro di valore del tipo di `void` proprietà, un tipo restituito e gli stessi modificatori della proprietà che lo contiene.

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
`X`è una proprietà di sola lettura virtuale, `Y` è una proprietà di lettura/scrittura virtuale ed `Z` è una proprietà astratta di lettura/scrittura. Poiché `Z` è astratto, la classe `A` che lo contiene deve anche essere dichiarata astratta.

Una classe che deriva da viene `A` mostrata di seguito:
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

Qui, le dichiarazioni di, `X` `Y`e `Z` eseguono l'override delle dichiarazioni di proprietà. Ogni dichiarazione di proprietà corrisponde esattamente ai modificatori di accessibilità, al tipo e al nome della proprietà ereditata corrispondente. La `get` funzione di `X` accesso di `set` e la `Y` funzione di `base` accesso di usano la parola chiave per accedere alle funzioni di accesso ereditate. La dichiarazione di `Z` esegue l'override di entrambe le funzioni di accesso astratte, pertanto non esistono membri `B`di funzioni `B` astratte in attesa in e può essere una classe non astratta.

Quando una proprietà viene dichiarata `override`come, tutte le funzioni di accesso sottoposte a override devono essere accessibili al codice che esegue l'override. Inoltre, l'accessibilità dichiarata sia della proprietà che dell'indicizzatore e delle funzioni di accesso devono corrispondere a quelle del membro e delle funzioni di accesso sottoposti a override. Esempio:
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

## <a name="events"></a>Events

Un ***evento*** è un membro che consente a un oggetto o a una classe di fornire notifiche. I client possono alleghino il codice eseguibile per gli eventi fornendo i ***gestori eventi***.

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

Un *event_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)), il `new` ([il modificatore new](classes.md#the-new-modifier)), `static` ([static e instance Metodi](classes.md#static-and-instance-methods)), `virtual` ([metodi virtuali](classes.md#virtual-methods)), 0 ([metodi di override](classes.md#override-methods)), 2[(Metodi sealed](classes.md#sealed-methods)), 4 ([metodi astratti](classes.md#abstract-methods)) e i modificatori 6 ([metodi esterni](classes.md#external-methods)).

Le dichiarazioni di evento sono soggette alle stesse regole delle dichiarazioni di metodo ([Metodi](classes.md#methods)) per quanto concerne combinazioni valide di modificatori.

Il *tipo* di una dichiarazione di evento deve essere un *delegate_type* ([tipi di riferimento](types.md#reference-types)) e *delegate_type* deve essere accessibile almeno quanto l'evento stesso (vincoli di[accessibilità](basic-concepts.md#accessibility-constraints)).

Una dichiarazione di evento può includere *event_accessor_declarations*. Tuttavia, in caso contrario, per gli eventi non esterni, non astratti, il compilatore li fornisce automaticamente ([eventi simili ai campi](classes.md#field-like-events)); per gli eventi extern, le funzioni di accesso vengono fornite esternamente.

Una dichiarazione di evento che omette *event_accessor_declarations* definisce uno o più eventi, uno per ogni *variable_declarator*. Gli attributi e i modificatori si applicano a tutti i membri dichiarati da un *event_declaration*di questo tipo.

Si tratta di un errore in fase di compilazione per un *event_declaration* che include sia il modificatore `abstract` sia il *event_accessor_declarations*delimitato da parentesi graffe.

Quando una dichiarazione di evento include `extern` un modificatore, l'evento viene definito ***evento esterno***. Poiché una dichiarazione di evento esterno non fornisce alcuna implementazione effettiva, è un errore per includere sia il modificatore `extern` che *event_accessor_declarations*.

Si tratta di un errore in fase di compilazione per un *variable_declarator* di una dichiarazione di evento con un modificatore `abstract` o `external` per includere un *variable_initializer*.

Un evento può essere usato come operando sinistro degli operatori `+=` e `-=` ([Assegnazione di eventi](expressions.md#event-assignment)). Questi operatori vengono utilizzati rispettivamente per il collegamento di gestori eventi a o per rimuovere i gestori eventi da un evento e i modificatori di accesso dell'evento controllano i contesti in cui sono consentite tali operazioni.

Poiché `+=` e`-=` sono le uniche operazioni consentite in un evento all'esterno del tipo che dichiara l'evento, il codice esterno può aggiungere e rimuovere gestori per un evento, ma non può in alcun altro modo ottenere o modificare l'elenco sottostante di eventi gestori.

In un'operazione del form `x += y` o `x -= y`, quando `x` è un evento e il riferimento si verifica all'esterno del tipo che contiene la dichiarazione di `x`, il risultato dell'operazione è di tipo `void` (invece di tipo di `x`, con il valore di `x` dopo l'assegnazione. Questa regola impedisce al codice esterno di esaminare indirettamente il delegato sottostante di un evento.

Nell'esempio seguente viene illustrato il modo in cui i gestori eventi sono collegati `Button` alle istanze della classe:
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

In questo caso `LoginDialog` , il costruttore di `Button` istanze crea due istanze e connette i `Click` gestori eventi agli eventi.

### <a name="field-like-events"></a>Eventi simili a campi

All'interno del testo del programma della classe o dello struct che contiene la dichiarazione di un evento, è possibile usare alcuni eventi come i campi. Per poter essere usati in questo modo, un evento non deve essere `abstract` o `extern` e non deve includere in modo esplicito *event_accessor_declarations*. Un evento di questo tipo può essere utilizzato in qualsiasi contesto che consenta un campo. Il campo contiene un delegato ([delegati](delegates.md)) che fa riferimento all'elenco di gestori di eventi che sono stati aggiunti all'evento. Se non è stato aggiunto alcun gestore eventi, il campo contiene `null`.

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
`Click`viene usato come campo all'interno della `Button` classe. Come illustrato nell'esempio, il campo può essere esaminato, modificato e utilizzato nelle espressioni di chiamata dei delegati. Il `OnClick` metodo `Click` nella classe "genera" l'evento. `Button` Generare un evento equivale a richiamare il delegato rappresentato dall'evento. Non sono quindi necessari speciali costrutti di linguaggio per generare eventi. Si noti che la chiamata al delegato è preceduta da un controllo che garantisce che il delegato sia diverso da null.

Al di fuori della Dichiarazione `Button` della classe, `Click` il membro può essere usato solo sul lato sinistro degli `+=` operatori e `-=` , come in
```csharp
b.Click += new EventHandler(...);
```
che aggiunge un delegato all'elenco chiamate dell' `Click` evento e
```csharp
b.Click -= new EventHandler(...);
```
che rimuove un delegato dall'elenco chiamate dell' `Click` evento.

Quando si compila un evento di tipo campo, il compilatore crea automaticamente lo spazio di archiviazione per conservare il delegato e crea funzioni di accesso per l'evento che aggiungono o rimuovono gestori eventi nel campo delegato. Le operazioni di aggiunta e rimozione sono thread-safe e può (ma non è necessario) essere eseguite tenendo premuto il blocco ([istruzione lock](statements.md#the-lock-statement)) nell'oggetto contenitore per un evento di istanza o l'oggetto tipo (espressioni di[creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions)) per un evento statico.

Pertanto, una dichiarazione di evento di istanza nel formato seguente:
```csharp
class X
{
    public event D Ev;
}
```
verrà compilato in un valore equivalente a:
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
All'interno della `X`classe, i `Ev` riferimenti a sul lato sinistro degli `+=` operatori e `-=` provocano la chiamata delle funzioni di accesso Add e Remove. Tutti gli altri riferimenti `Ev` a vengono compilati in modo `__Ev` da fare riferimento al campo nascosto ([accesso ai membri](expressions.md#member-access)). Il nome "`__Ev`" è arbitrario. il campo nascosto potrebbe avere qualsiasi nome o nessun nome.

### <a name="event-accessors"></a>Funzioni di accesso agli eventi

Le dichiarazioni di evento in genere omettono *event_accessor_declarations*, come nell'esempio `Button` precedente. Una situazione in cui è necessario eseguire questa operazione riguarda il caso in cui il costo di archiviazione di un campo per ogni evento non è accettabile. In questi casi, una classe può includere *event_accessor_declarations* e usare un meccanismo privato per archiviare l'elenco dei gestori eventi.

*Event_accessor_declarations* di un evento specificano le istruzioni eseguibili associate all'aggiunta e alla rimozione di gestori eventi.

Le dichiarazioni delle funzioni di accesso sono costituite da un *add_accessor_declaration* e da un *remove_accessor_declaration*. Ogni dichiarazione di funzione di accesso è `add` costituita dal token o `remove` seguito da un *blocco*. Il *blocco* associato a un *add_accessor_declaration* specifica le istruzioni da eseguire quando viene aggiunto un gestore eventi e il *blocco* associato a un *remove_accessor_declaration* specifica le istruzioni da eseguire Quando viene rimosso un gestore eventi.

Ogni *add_accessor_declaration* e *remove_accessor_declaration* corrisponde a un metodo con un singolo parametro di valore del tipo di evento e un tipo restituito `void`. Il parametro implicito di una funzione di accesso `value`eventi è denominato. Quando un evento viene utilizzato in un'assegnazione di evento, viene utilizzata la funzione di accesso eventi appropriata. In particolare, se l'operatore `+=` di assegnazione è, viene usata la funzione di accesso Add e se l'operatore di assegnazione è `-=` , viene usata la funzione di accesso remove. In entrambi i casi, l'operando destro dell'operatore di assegnazione viene usato come argomento della funzione di accesso dell'evento. Il blocco di un *add_accessor_declaration* o *remove_accessor_declaration* deve essere conforme alle regole per i metodi `void` descritti nel [corpo del metodo](classes.md#method-body). In particolare, `return` le istruzioni in un blocco di questo tipo non sono autorizzate a specificare un'espressione.

Poiché una funzione di accesso agli eventi include implicitamente `value`un parametro denominato, si tratta di un errore in fase di compilazione per una variabile locale o una costante dichiarata in una funzione di accesso a un evento con tale nome.

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
la `Control` classe implementa un meccanismo di archiviazione interno per gli eventi. Il `AddEventHandler` metodo associa un valore delegato a una chiave, il `GetEventHandler` metodo restituisce il delegato attualmente associato a una chiave e il `RemoveEventHandler` metodo rimuove un delegato come gestore eventi per l'evento specificato. Presumibilmente, il meccanismo di archiviazione sottostante è progettato in modo che non vi sia alcun costo per l' `null` associazione di un valore delegato a una chiave, quindi gli eventi non gestiti non utilizzano alcuna risorsa di archiviazione.

### <a name="static-and-instance-events"></a>Eventi statici e di istanza

Quando una dichiarazione di evento include `static` un modificatore, l'evento viene definito ***evento statico***. Quando non `static` è presente alcun modificatore, l'evento viene definito ***evento dell'istanza***.

Un evento statico non è associato a un'istanza specifica e si tratta di un errore `this` in fase di compilazione a cui fare riferimento nelle funzioni di accesso di un evento statico.

Un evento di istanza è associato a una determinata istanza di una classe e a questa istanza è possibile accedere `this` come ([questo accesso](expressions.md#this-access)) nelle funzioni di accesso dell'evento.

Quando si fa riferimento a un evento in un *member_access* ([accesso ai membri](expressions.md#member-access)) nel formato `E.M`, se `M` è un evento statico, `E` deve indicare un tipo che contiene `M` e se `M` è un evento di istanza, e deve indicare un'istanza di un tipo che contiene `M`.

Le differenze tra i membri statici e i membri di istanza sono illustrate ulteriormente nei [membri statici e di istanza](classes.md#static-and-instance-members).

### <a name="virtual-sealed-override-and-abstract-event-accessors"></a>Funzioni di accesso a eventi virtuali, sealed, override e astratti

Una `virtual` dichiarazione di evento specifica che le funzioni di accesso di tale evento sono virtuali. Il `virtual` modificatore si applica a entrambe le funzioni di accesso di un evento.

Una `abstract` dichiarazione di evento specifica che le funzioni di accesso dell'evento sono virtuali, ma non fornisce un'implementazione effettiva delle funzioni di accesso. Al contrario, le classi derivate non astratte sono necessarie per fornire la propria implementazione per le funzioni di accesso eseguendo l'override dell'evento. Poiché una dichiarazione di evento astratto non fornisce alcuna implementazione effettiva, non può fornire *event_accessor_declarations*delimitati da parentesi graffe.

Una dichiarazione di evento che include entrambi `abstract` i `override` modificatori e specifica che l'evento è astratto ed esegue l'override di un evento di base. Anche le funzioni di accesso di tale evento sono astratte.

Le dichiarazioni di eventi astratti sono consentite solo nelle classi astratte ([classi astratte](classes.md#abstract-classes)).

Le funzioni di accesso di un evento virtuale ereditato possono essere sottoposte a override in una classe derivata includendo una Dichiarazione `override` di evento che specifica un modificatore. Questa operazione è nota come ***dichiarazione di evento di override***. Una dichiarazione di evento che esegue l'override non dichiara un nuovo evento. Ma semplicemente specializza le implementazioni delle funzioni di accesso di un evento virtuale esistente.

Una dichiarazione di evento di override deve specificare esattamente gli stessi modificatori di accessibilità, tipo e nome dell'evento sottoposto a override.

Una dichiarazione di evento di override può includere `sealed` il modificatore. L'uso di questo modificatore impedisce a una classe derivata di eseguire ulteriormente l'override dell'evento. Anche le funzioni di accesso di un evento Sealed sono sealed.

Si tratta di un errore in fase di compilazione per la dichiarazione di un evento che `new` esegue l'override per includere un modificatore.

Ad eccezione delle differenze nella sintassi della dichiarazione e della chiamata, le funzioni di accesso Virtual, sealed, override e abstract si comportano esattamente come i metodi virtual, sealed, override e abstract. In particolare, le regole descritte in [metodi virtuali](classes.md#virtual-methods), [metodi di override](classes.md#override-methods), [Metodi sealed](classes.md#sealed-methods)e [metodi astratti](classes.md#abstract-methods) si applicano come se le funzioni di accesso fossero metodi di un form corrispondente. Ogni funzione di accesso corrisponde a un metodo con un singolo parametro di valore del tipo di `void` evento, un tipo restituito e gli stessi modificatori dell'evento che lo contiene.

## <a name="indexers"></a>Indicizzatori

Un ***indicizzatore*** è un membro che consente l'indicizzazione di un oggetto nello stesso modo di una matrice. Gli indicizzatori vengono dichiarati usando *indexer_declaration*s:

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

Un *indexer_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)), il `new` ([il modificatore new](classes.md#the-new-modifier)), `virtual` ([metodi virtuali ](classes.md#virtual-methods)), `override` ([metodi di override](classes.md#override-methods)), 0[(Metodi sealed](classes.md#sealed-methods)), 2 ([metodi astratti](classes.md#abstract-methods)) e i modificatori 4 ([metodi esterni](classes.md#external-methods)).

Le dichiarazioni dell'indicizzatore sono soggette alle stesse regole delle dichiarazioni di metodo ([Metodi](classes.md#methods)) per quanto riguarda le combinazioni valide di modificatori, con la sola eccezione che il modificatore static non è consentito in una dichiarazione dell'indicizzatore.

I modificatori `virtual`, `override`e `abstract` si escludono a vicenda, tranne che in un caso. I `abstract` modificatori e `override` possono essere usati insieme in modo che un indicizzatore astratto possa eseguire l'override di uno virtuale.

Il *tipo* di una dichiarazione di indicizzatore specifica il tipo di elemento dell'indicizzatore introdotto dalla dichiarazione. A meno che l'indicizzatore non sia un'implementazione esplicita di un membro di interfaccia, il `this`tipo è seguito dalla parola chiave. Per un'implementazione esplicita di un membro di interfaccia, il *tipo* è seguito da un *INTERFACE_TYPE*, un "`.`" e la parola chiave `this`. A differenza di altri membri, gli indicizzatori non hanno nomi definiti dall'utente.

*Formal_parameter_list* specifica i parametri dell'indicizzatore. L'elenco di parametri formali di un indicizzatore corrisponde a quello di un metodo ([parametri del metodo](classes.md#method-parameters)), ad eccezione del fatto che è necessario specificare almeno un `ref` parametro `out` e che i modificatori di parametro e non sono consentiti.

Il *tipo* di un indicizzatore e ciascuno dei tipi a cui si fa riferimento in *formal_parameter_list* deve essere accessibile almeno quanto l'indicizzatore stesso ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).

Un *indexer_body* può essere costituito da un ***corpo della funzione di accesso*** o da un corpo di ***espressione***. In un corpo della funzione di accesso, *accessor_declarations*, che deve essere racchiuso tra token "`{`" e "`}`", dichiarare le funzioni di accesso ([funzioni di accesso](classes.md#accessors)) della proprietà. Le funzioni di accesso specificano le istruzioni eseguibili associate alla lettura e alla scrittura della proprietà.

Il corpo di un'espressione costituito`=>`da "" seguito da `E` un'espressione e da un punto e virgola è `{ get { return E; } }`esattamente equivalente al corpo dell'istruzione e pertanto può essere utilizzato solo per specificare gli indicizzatori solo Get in cui il risultato del Getter è fornito da un'unica espressione.

Anche se la sintassi per l'accesso a un indicizzatore è identica a quella di un elemento di matrice, un elemento indicizzatore non viene classificato come variabile. Non è quindi possibile passare un elemento indicizzatore come `ref` argomento o. `out`

L'elenco di parametri formali di un indicizzatore definisce la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) dell'indicizzatore. In particolare, la firma di un indicizzatore è costituita dal numero e dai tipi dei parametri formali. Il tipo di elemento e i nomi dei parametri formali non fanno parte della firma di un indicizzatore.

La firma di un indicizzatore deve essere diversa dalle firme di tutti gli altri indicizzatori dichiarati nella stessa classe.

Gli indicizzatori e le proprietà sono molto simili, ma si differenziano nei modi seguenti:

*  Una proprietà viene identificata in base al nome, mentre un indicizzatore viene identificato dalla firma.
*  È possibile accedere a una proprietà tramite un *simple_name* ([nomi semplici](expressions.md#simple-names)) o un *member_access* ([accesso ai membri](expressions.md#member-access)), mentre un elemento indicizzatore è accessibile tramite un *element_access* ([accesso all'indicizzatore](expressions.md#indexer-access)).
*  Una proprietà può essere un `static` membro, mentre un indicizzatore è sempre un membro di istanza.
*  Una `get` funzione di accesso di una proprietà corrisponde a un metodo senza parametri, mentre `get` una funzione di accesso di un indicizzatore corrisponde a un metodo con lo stesso elenco di parametri formali dell'indicizzatore.
*  Una `set` funzione di accesso di una proprietà corrisponde a un metodo con un solo `value`parametro denominato, `set` mentre una funzione di accesso di un indicizzatore corrisponde a un metodo con lo stesso elenco di parametri formali dell'indicizzatore, oltre a un parametro aggiuntivo. nome `value`.
*  Si tratta di un errore in fase di compilazione che consente a una funzione di accesso dell'indicizzatore di dichiarare una variabile locale con lo stesso nome di un parametro dell'indicizzatore.
*  In una dichiarazione di proprietà che esegue l'override, viene eseguito l'accesso alla `base.P`proprietà ereditata utilizzando la sintassi, dove `P` è il nome della proprietà. In una dichiarazione di indicizzatore che esegue l'override, viene eseguito l'accesso all'indicizzatore ereditato `E` usando la sintassi `base[E]`, dove è un elenco di espressioni separate da virgole.
*  Non esiste alcun concetto di "indicizzatore implementato automaticamente". È un errore avere un indicizzatore non astratto e non esterno con funzioni di accesso punto e virgola.

A parte queste differenze, tutte le regole definite nelle [funzioni di accesso](classes.md#accessors) e le [proprietà implementate automaticamente](classes.md#automatically-implemented-properties) si applicano alle funzioni di accesso dell'indicizzatore, oltre che alle funzioni di accesso alle proprietà.

Quando una dichiarazione di indicizzatore include `extern` un modificatore, l'indicizzatore viene definito ***indicizzatore esterno***. Poiché una dichiarazione di indicizzatore esterno non fornisce alcuna implementazione effettiva, ogni *accessor_declarations* è costituito da un punto e virgola.

Nell'esempio seguente viene dichiarata una `BitArray` classe che implementa un indicizzatore per accedere ai singoli bit nella matrice di bit.
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

Un'istanza della `BitArray` classe utilizza sostanzialmente meno memoria rispetto a un oggetto corrispondente `bool[]` (poiché ogni valore del primo occupa un solo bit anziché un solo byte di quest'ultimo), ma consente le stesse operazioni di un oggetto `bool[]`.

La classe `CountPrimes` seguente usa un `BitArray` e l'algoritmo classico "sieve" per calcolare il numero di numeri primi tra 1 e un valore massimo specificato:
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

Si noti che la sintassi per l'accesso agli elementi `BitArray` di è esattamente identica a quella di `bool[]`un oggetto.

Nell'esempio seguente viene illustrata una classe della griglia di 26 * 10 con un indicizzatore con due parametri. Il primo parametro deve essere una lettera maiuscola o minuscola nell'intervallo A-Z e il secondo deve essere un numero intero compreso nell'intervallo 0-9.

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

### <a name="indexer-overloading"></a>Overload dell'indicizzatore

Le regole di risoluzione dell'overload dell'indicizzatore sono descritte in [inferenza del tipo](expressions.md#type-inference).

## <a name="operators"></a>Operatori

Un ***operatore*** è un membro che definisce il significato di un operatore Expression che può essere applicato alle istanze della classe. Gli operatori vengono dichiarati usando *operator_declaration*s:

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
    | right_shift | '=='  | '!='  | '>'   | '<'   | '>='  | '<='
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

Sono disponibili tre categorie di operatori che eseguono l'overload: Operatori unari ([operatori unari](classes.md#unary-operators)), operatori binari ([operatori binari](classes.md#binary-operators)) e operatori di conversione ([operatori di conversione](classes.md#conversion-operators)).

*Operator_body* è un punto e virgola, un ***corpo dell'istruzione*** o un corpo dell' ***espressione***. Il corpo di un'istruzione è costituito da un *blocco*, che specifica le istruzioni da eseguire quando viene richiamato l'operatore. Il *blocco* deve essere conforme alle regole per i metodi che restituiscono valori descritti nel [corpo del metodo](classes.md#method-body). Il corpo di `=>` un'espressione è costituito da seguito da un'espressione e da un punto e virgola e indica una singola espressione da eseguire quando viene richiamato l'operatore.

Per gli operatori `extern`, il *operator_body* è costituito semplicemente da un punto e virgola. Per tutti gli altri operatori, *operator_body* è un corpo del blocco o un corpo dell'espressione.

Le regole seguenti si applicano a tutte le dichiarazioni di operatore:

*  Una dichiarazione di operatore deve includere sia `public` un modificatore che un `static` modificatore.
*  Il parametro o i parametri di un operatore devono essere parametri valore ([parametri valore](variables.md#value-parameters)). Si tratta di un errore in fase di compilazione per una dichiarazione di `ref` operatore `out` per specificare i parametri o.
*  La firma di un operatore ([operatori unari](classes.md#unary-operators), operatori [binari](classes.md#binary-operators), [operatori di conversione](classes.md#conversion-operators)) deve differire dalle firme di tutti gli altri operatori dichiarati nella stessa classe.
*  Tutti i tipi a cui viene fatto riferimento in una dichiarazione di operatore devono essere accessibili almeno quanto l'operatore stesso ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).
*  È un errore che lo stesso modificatore venga visualizzato più volte in una dichiarazione di operatore.

Ogni categoria di operatori impone restrizioni aggiuntive, come descritto nelle sezioni seguenti.

Come gli altri membri, gli operatori dichiarati in una classe di base vengono ereditati dalle classi derivate. Poiché le dichiarazioni di operatore richiedono sempre la classe o lo struct in cui l'operatore viene dichiarato per partecipare alla firma dell'operatore, non è possibile che un operatore dichiarato in una classe derivata nasconda un operatore dichiarato in una classe di base. Pertanto, il `new` modificatore non è mai necessario e quindi mai consentito in una dichiarazione di operatore.

Ulteriori informazioni sugli operatori unari e binari sono reperibili negli [operatori](expressions.md#operators).

Altre informazioni sugli operatori di conversione sono disponibili nelle [conversioni definite dall'utente](conversions.md#user-defined-conversions).

### <a name="unary-operators"></a>Operatori unari

Le regole seguenti si applicano alle dichiarazioni dell'operatore unario, dove `T` indica il tipo di istanza della classe o dello struct che contiene la dichiarazione dell'operatore:

*  Un operatore `+` `-`unario `~` `T` ,, o deve prendere un solo parametro di tipo o `T?` e può restituire qualsiasi tipo. `!`
*  Un operatore `++` unario o `--` deve prendere un solo parametro di `T` tipo `T?` o e deve restituire lo stesso tipo o un tipo derivato da esso.
*  Un operatore `true` unario o `false` deve prendere un solo parametro di `T` tipo `T?` o e deve restituire `bool`il tipo.

La firma di un operatore unario è costituita dal token`+`dell' `-`operatore `!`(, `++`, `--`, `true` `~`,, `false`, o) e dal tipo del singolo parametro formale. Il tipo restituito non fa parte della firma di un operatore unario, né è il nome del parametro formale.

Gli `true` operatori `false` unari e richiedono la dichiarazione di coppie. Si verifica un errore in fase di compilazione se una classe dichiara uno di questi operatori senza dichiarare anche l'altro. Gli `true` operatori `false` e sono descritti ulteriormente negli [operatori logici condizionali definiti dall'utente](expressions.md#user-defined-conditional-logical-operators) e nelle [espressioni booleane](expressions.md#boolean-expressions).

Nell'esempio seguente viene illustrata un'implementazione e l' `operator ++` utilizzo successivo di per una classe Vector di tipo Integer:
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

Si noti il modo in cui il metodo operator restituisce il valore prodotto aggiungendo 1 all'operando, esattamente come gli operatori di incremento e decremento suffisso ([operatori di incremento e decremento suffisso](expressions.md#postfix-increment-and-decrement-operators)) e gli operatori di incremento e decremento del prefisso ([prefisso operatori di incremento e decremento](expressions.md#prefix-increment-and-decrement-operators)). Diversamente da C++, questo metodo non deve modificare direttamente il valore del relativo operando. Infatti, la modifica del valore dell'operando violerebbe la semantica standard dell'operatore di incremento suffisso.

### <a name="binary-operators"></a>Operatori binari

Le regole seguenti si applicano alle dichiarazioni di operatori binari `T` , dove indica il tipo di istanza della classe o dello struct che contiene la dichiarazione dell'operatore:

*  Un operatore non di spostamento binario deve prendere due parametri, almeno uno dei quali deve avere il tipo `T` o `T?`e può restituire qualsiasi tipo.
*  Un operatore `<<` o `>>` binario deve assumere due parametri, il primo dei quali deve avere il `T` tipo `T?` o e il secondo di cui deve avere `int` il `int?`tipo o e può restituire qualsiasi tipo.

La firma di un operatore binario è costituita dal token dell'`+`operatore `-`( `*`, `/`, `%`, `&`, `|`, `^`, `<<`, `>>`,,, `==`, ,,`>`, o`>=`) eitipideidueparametriformali`<=`. `!=` `<` Il tipo restituito e i nomi dei parametri formali non fanno parte della firma di un operatore binario.

Per alcuni operatori binari è richiesta la dichiarazione di coppie. Per ogni dichiarazione di uno degli operatori di una coppia, è necessario che sia presente una dichiarazione corrispondente dell'altro operatore della coppia. Due dichiarazioni di operatore corrispondono quando hanno lo stesso tipo restituito e lo stesso tipo per ogni parametro. Per gli operatori seguenti è necessaria la dichiarazione Pair-Wise:

*  `operator ==` e `operator !=`
*  `operator >` e `operator <`
*  `operator >=` e `operator <=`

### <a name="conversion-operators"></a>Operatori di conversione

Una dichiarazione dell'operatore di conversione introduce una ***conversione definita dall'utente*** ([conversioni definite dall'utente](conversions.md#user-defined-conversions)) che aumenta le conversioni implicite ed esplicite predefinite.

Una dichiarazione dell'operatore di conversione che `implicit` include la parola chiave introduce una conversione implicita definita dall'utente. Le conversioni implicite possono verificarsi in varie situazioni, ad esempio chiamate di membri di funzioni, espressioni cast e assegnazioni. Questa operazione viene descritta ulteriormente nelle [conversioni implicite](conversions.md#implicit-conversions).

Una dichiarazione dell'operatore di conversione che `explicit` include la parola chiave introduce una conversione esplicita definita dall'utente. Le conversioni esplicite possono verificarsi nelle espressioni cast e sono descritte ulteriormente nelle [conversioni esplicite](conversions.md#explicit-conversions).

Un operatore di conversione converte da un tipo di origine, indicato dal tipo di parametro dell'operatore di conversione, a un tipo di destinazione, indicato dal tipo restituito dell'operatore di conversione.

Per un `S` tipo di origine e un tipo `T`di destinazione specificati `T` , se `S` o sono tipi `S0` Nullable `T0` , consentire e fare riferimento ai relativi `S0` tipi `T0` sottostanti; in caso contrario, e sono uguale a `S` e `T` rispettivamente. Una classe o uno struct è autorizzato a dichiarare una conversione da un tipo `S` di origine a un `T` tipo di destinazione solo se si verificano tutte le condizioni seguenti:

*  `S0`e `T0` sono tipi diversi.
*  `S0` O`T0` è il tipo di classe o struct in cui si verifica la dichiarazione di operatore.
*  Né `S0` né `T0` è un *INTERFACE_TYPE*.
*  Escludendo le conversioni definite dall'utente, non esiste una conversione da `S` a `T` o da `T` a `S`.

Ai fini di queste regole, qualsiasi parametro di tipo associato a `S` o `T` viene considerato tipi univoci che non hanno una relazione di ereditarietà con altri tipi e tutti i vincoli sui parametri di tipo vengono ignorati.

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
le prime due dichiarazioni di operatore sono consentite perché, a scopo di [indicizzatori](classes.md#indexers),3 `T` , `int` e `string` e rispettivamente sono considerati tipi univoci senza relazione. Tuttavia, il terzo operatore è un errore perché `C<T>` è la classe di base `D<T>`di.

Dalla seconda regola segue che un operatore di conversione deve eseguire la conversione da o verso il tipo di classe o struct in cui viene dichiarato l'operatore. È possibile, ad esempio, che una classe o un tipo `C` struct definisca una `C` conversione `int` da a `int` e `C`da a, ma `int` non `bool`da a.

Non è possibile ridefinire direttamente una conversione predefinita. Pertanto, gli operatori di conversione non sono autorizzati a eseguire la `object` conversione da o a perché le conversioni implicite `object` ed esplicite esistono già tra e tutti gli altri tipi. Analogamente, né l'origine né i tipi di destinazione di una conversione possono essere un tipo di base dell'altro, dal momento che una conversione sarebbe già esistente.

Tuttavia, è possibile dichiarare gli operatori sui tipi generici che, per determinati argomenti di tipo, specificano le conversioni già esistenti come conversioni predefinite. Nell'esempio
```csharp
struct Convertible<T>
{
    public static implicit operator Convertible<T>(T value) {...}
    public static explicit operator T(Convertible<T> value) {...}
}
```
Quando il `object` tipo viene specificato come argomento di tipo `T`per, il secondo operatore dichiara una conversione già esistente (la conversione implicita e, di conseguenza, una conversione esplicita esiste da qualsiasi tipo `object`al tipo).

Nei casi in cui esiste una conversione predefinita tra due tipi, le conversioni definite dall'utente tra tali tipi vengono ignorate. In particolare:

*  Se esiste una conversione implicita predefinita ([conversioni implicite](conversions.md#implicit-conversions)) dal tipo `S` al tipo `T`, tutte le conversioni definite dall'utente (implicite o esplicite) `S` da `T` a vengono ignorate.
*  Se una conversione esplicita predefinita ([conversioni esplicite](conversions.md#explicit-conversions)) `S` esiste dal tipo al tipo `T`, le conversioni esplicite definite dall'utente da `S` a `T` vengono ignorate. Inoltre

Se `T` è un tipo di interfaccia, le conversioni implicite definite dall' `S` utente `T` da a vengono ignorate.

In caso contrario, le conversioni implicite definite `S` dall' `T` utente da a vengono ancora prese in considerazione.

Per tutti i tipi `object`, ma gli operatori dichiarati `Convertible<T>` dal tipo precedente non sono in conflitto con le conversioni predefinite. Esempio:
```csharp
void F(int i, Convertible<int> n) {
    i = n;                          // Error
    i = (int)n;                     // User-defined explicit conversion
    n = i;                          // User-defined implicit conversion
    n = (Convertible<int>)i;        // User-defined implicit conversion
}
```

Per il tipo `object`, tuttavia, le conversioni predefinite nascondono le conversioni definite dall'utente in tutti i casi, ma uno:

```csharp
void F(object o, Convertible<object> n) {
    o = n;                         // Pre-defined boxing conversion
    o = (object)n;                 // Pre-defined boxing conversion
    n = o;                         // User-defined implicit conversion
    n = (Convertible<object>)o;    // Pre-defined unboxing conversion
}
```

Per le conversioni definite dall'utente non è consentito eseguire la conversione da o a *INTERFACE_TYPE*. In particolare, questa restrizione garantisce che non si verifichino trasformazioni definite dall'utente durante la conversione in un *INTERFACE_TYPE*e che una conversione in un *INTERFACE_TYPE* abbia esito positivo solo se l'oggetto in fase di conversione implementa il *INTERFACE_TYPE*specificato.

La firma di un operatore di conversione è costituita dal tipo di origine e dal tipo di destinazione. Si noti che questa è l'unica forma di membro per cui il tipo restituito fa parte della firma. La `implicit` classificazione `explicit` o di un operatore di conversione non fa parte della firma dell'operatore. Pertanto, una classe o uno struct non può dichiarare `implicit` sia un `explicit` operatore di conversione che con gli stessi tipi di origine e di destinazione.

In generale, le conversioni implicite definite dall'utente devono essere progettate in modo da non generare mai eccezioni e non perdere mai le informazioni. Se una conversione definita dall'utente può generare eccezioni (ad esempio, poiché l'argomento di origine non è compreso nell'intervallo) o la perdita di informazioni, ad esempio la rimozione di bit più significativi, tale conversione deve essere definita come conversione esplicita.

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
la conversione da `Digit` a `byte` è implicita, in quanto non genera mai eccezioni o perde informazioni, ma `byte` la `Digit` conversione da a `Digit` è esplicita poiché può rappresentare solo un subset della possibile valori di un `byte`oggetto.

## <a name="instance-constructors"></a>Costruttori di istanze

Un ***costruttore di istanza*** è un membro che implementa le azioni necessarie per inizializzare un'istanza di una classe, I costruttori di istanza vengono dichiarati utilizzando *constructor_declaration*s:

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

Un *constructor_declaration* può includere un set di *attributi* ([attributi](attributes.md)), una combinazione valida dei quattro modificatori di accesso ([modificatori di accesso](classes.md#access-modifiers)) e un modificatore `extern` ([metodi esterni](classes.md#external-methods)). Una dichiarazione del costruttore non può includere lo stesso modificatore più volte.

L' *identificatore* di un *constructor_declarator* deve denominare la classe in cui è dichiarato il costruttore di istanza. Se viene specificato un altro nome, si verificherà un errore in fase di compilazione.

Il *formal_parameter_list* facoltativo di un costruttore di istanza è soggetto alle stesse regole di *formal_parameter_list* di un metodo ([Metodi](classes.md#methods)). L'elenco di parametri formali definisce la firma ([firme e overload](basic-concepts.md#signatures-and-overloading)) di un costruttore di istanza e regola il processo in base al quale la risoluzione dell'overload ([inferenza del tipo](expressions.md#type-inference)) seleziona un particolare costruttore di istanza in una chiamata.

Ognuno dei tipi a cui viene fatto riferimento in *formal_parameter_list* di un costruttore di istanza deve essere accessibile almeno quanto il costruttore stesso ([vincoli di accessibilità](basic-concepts.md#accessibility-constraints)).

Il *constructor_initializer* facoltativo specifica un altro costruttore di istanza da richiamare prima di eseguire le istruzioni fornite in *constructor_body* del costruttore di istanza. Questa operazione viene descritta ulteriormente negli [inizializzatori del costruttore](classes.md#constructor-initializers).

Quando una dichiarazione di costruttore include `extern` un modificatore, il costruttore viene definito ***Costruttore esterno***. Poiché una dichiarazione del costruttore esterno non fornisce alcuna implementazione effettiva, la relativa *constructor_body* è costituita da un punto e virgola. Per tutti gli altri costruttori, *constructor_body* è costituito da un *blocco* che specifica le istruzioni per inizializzare una nuova istanza della classe. Corrisponde esattamente al *blocco* di un metodo di istanza con un `void` tipo restituito (corpo del[Metodo](classes.md#method-body)).

I costruttori di istanza non vengono ereditati. Pertanto, una classe non dispone di costruttori di istanza diversi da quelli effettivamente dichiarati nella classe. Se una classe non contiene dichiarazioni di costruttori di istanza, viene automaticamente fornito un costruttore di istanza predefinito ([costruttori predefiniti](classes.md#default-constructors)).

I costruttori di istanza vengono richiamati da *object_creation_expression*s ([espressioni di creazione di oggetti](expressions.md#object-creation-expressions)) e tramite *constructor_initializer*s.

### <a name="constructor-initializers"></a>Inizializzatori del costruttore

Tutti i costruttori di istanza (ad eccezione di quelli per la classe `object`) includono implicitamente una chiamata a un altro costruttore di istanza immediatamente prima di *constructor_body*. Il costruttore da richiamare in modo implicito è determinato da *constructor_initializer*:

*  Un inizializzatore del costruttore di istanza `base(argument_list)` del `base()` form o causa la richiamata di un costruttore di istanza dalla classe base diretta. Il costruttore viene selezionato utilizzando *argument_list* se presente e le regole di risoluzione dell'overload della [risoluzione dell'overload](expressions.md#overload-resolution). Il set di costruttori di istanza candidati è costituito da tutti i costruttori di istanza accessibili contenuti nella classe di base diretta o dal costruttore predefinito ([costruttori predefiniti](classes.md#default-constructors)), se non sono stati dichiarati costruttori di istanza nella classe di base diretta. Se il set è vuoto o se non è possibile identificare un singolo costruttore di istanze migliore, si verificherà un errore in fase di compilazione.
*  Un inizializzatore del costruttore di istanza `this(argument-list)` del `this()` form o causa la richiamata di un costruttore di istanza dalla classe stessa. Il costruttore viene selezionato utilizzando *argument_list* se presente e le regole di risoluzione dell'overload della [risoluzione dell'overload](expressions.md#overload-resolution). Il set di costruttori di istanza candidati è costituito da tutti i costruttori di istanza accessibili dichiarati nella classe stessa. Se il set è vuoto o se non è possibile identificare un singolo costruttore di istanze migliore, si verificherà un errore in fase di compilazione. Se una dichiarazione di un costruttore di istanza include un inizializzatore del costruttore che richiama il costruttore stesso, si verifica un errore in fase di compilazione.

Se un costruttore di istanza non dispone di un inizializzatore di costruttore, viene `base()` fornito implicitamente un inizializzatore del costruttore del modulo. Quindi, una dichiarazione del costruttore di istanza del form
```csharp
C(...) {...}
```
è esattamente equivalente a
```csharp
C(...): base() {...}
```

L'ambito dei parametri forniti dal *formal_parameter_list* di una dichiarazione del costruttore di istanza include l'inizializzatore del costruttore della dichiarazione. Un inizializzatore di costruttore è quindi autorizzato ad accedere ai parametri del costruttore. Esempio:
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

Un inizializzatore di costruttore di istanza non può accedere all'istanza da creare. Di conseguenza, si tratta di un errore in fase di compilazione per fare riferimento a `this` in un'espressione di argomento dell'inizializzatore del costruttore, in quanto si tratta di un errore in fase di compilazione per un'espressione di argomento che fa riferimento a qualsiasi membro di istanza tramite un *simple_name*.

### <a name="instance-variable-initializers"></a>Inizializzatori di variabile di istanza

Quando un costruttore di istanza non dispone di un inizializzatore di costruttore o un inizializzatore del costruttore nel formato `base(...)`, il costruttore esegue in modo implicito le inizializzazioni specificate da *variable_initializer*dei campi di istanza dichiarati in classe. Corrisponde a una sequenza di assegnazioni che vengono eseguite immediatamente alla voce al costruttore e prima della chiamata implicita del costruttore della classe base diretta. Gli inizializzatori di variabile vengono eseguiti nell'ordine testuale in cui vengono visualizzati nella dichiarazione della classe.

### <a name="constructor-execution"></a>Esecuzione del costruttore

Gli inizializzatori di variabile vengono trasformati in istruzioni di assegnazione e queste istruzioni di assegnazione vengono eseguite prima della chiamata del costruttore di istanze della classe di base. Questo ordinamento garantisce che tutti i campi di istanza vengano inizializzati dai rispettivi inizializzatori di variabile prima che vengano eseguite istruzioni con accesso a tale istanza.

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
Quando `new B()` viene utilizzato per creare un'istanza di `B`, viene prodotto l'output seguente:
```console
x = 1, y = 0
```

Il valore di `x` è 1 perché l'inizializzatore di variabile viene eseguito prima che venga richiamato il costruttore di istanza della classe base. Tuttavia, il valore di `y` è 0 (il valore predefinito di un `int`) perché l'assegnazione a `y` non viene eseguita finché il costruttore della classe base non restituisce.

È utile considerare gli inizializzatori di variabile di istanza e gli inizializzatori di costruttore come istruzioni inserite automaticamente prima di *constructor_body*. Esempio
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
contiene diversi inizializzatori di variabili. contiene anche inizializzatori di costruttori di entrambi i form`base` ( `this`e). L'esempio corrisponde al codice riportato di seguito, in cui ogni commento indica un'istruzione inserita automaticamente (la sintassi utilizzata per le chiamate al costruttore inserite automaticamente non è valida, ma serve semplicemente per illustrare il meccanismo).

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

Se una classe non contiene dichiarazioni di costruttori di istanza, viene automaticamente fornito un costruttore di istanza predefinito. Il costruttore predefinito richiama semplicemente il costruttore senza parametri della classe base diretta. Se la classe è astratta, l'accessibilità dichiarata per il costruttore predefinito è protetta. In caso contrario, l'accessibilità dichiarata per il costruttore predefinito è public. Pertanto, il costruttore predefinito è sempre nel formato

```csharp
protected C(): base() {}
```
oppure
```csharp
public C(): base() {}
```
dove `C` è il nome della classe. Se la risoluzione dell'overload non è in grado di determinare un candidato univoco per l'inizializzatore del costruttore della classe base, si verificherà un errore in fase di compilazione.

Nell'esempio
```csharp
class Message
{
    object sender;
    string text;
}
```
viene fornito un costruttore predefinito perché la classe non contiene dichiarazioni di costruttori di istanza. Di conseguenza, l'esempio è esattamente equivalente a
```csharp
class Message
{
    object sender;
    string text;

    public Message(): base() {}
}
```

### <a name="private-constructors"></a>Costruttori privati

Quando una classe `T` dichiara solo costruttori di istanza privati, non è possibile che le classi esterne al testo di programma di `T` derivano da `T` o per creare direttamente istanze di `T`. Se pertanto una classe contiene solo membri statici e non è destinata alla creazione di un'istanza, l'aggiunta di un costruttore di istanza privata vuota impedirà la creazione di istanze. Esempio:
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

La `Trig` classe raggruppa i metodi e le costanti correlati, ma non deve essere creata un'istanza. Dichiara pertanto un singolo costruttore di istanza privata vuota. Per evitare la generazione automatica di un costruttore predefinito, è necessario dichiarare almeno un costruttore di istanze.

### <a name="optional-instance-constructor-parameters"></a>Parametri facoltativi del costruttore di istanza

Il `this(...)` form dell'inizializzatore del costruttore viene comunemente usato in combinazione con l'overload per implementare parametri del costruttore di istanza facoltativi. Nell'esempio
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
i primi due costruttori di istanza forniscono semplicemente i valori predefiniti per gli argomenti mancanti. Entrambi usano un `this(...)` inizializzatore di costruttore per richiamare il terzo costruttore di istanza, che in realtà esegue l'inizializzazione della nuova istanza. L'effetto è che i parametri facoltativi del costruttore:
```csharp
Text t1 = new Text();                    // Same as Text(0, 0, null)
Text t2 = new Text(5, 10);               // Same as Text(5, 10, null)
Text t3 = new Text(5, 20, "Hello");
```

## <a name="static-constructors"></a>Costruttori statici

Un ***costruttore statico*** è un membro che implementa le azioni necessarie per inizializzare un tipo di classe closed. I costruttori statici vengono dichiarati usando *static_constructor_declaration*s:

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

Un *static_constructor_declaration* può includere un set di *attributi* ([attributi](attributes.md)) e un modificatore `extern` ([metodi esterni](classes.md#external-methods)).

L' *identificatore* di un *static_constructor_declaration* deve denominare la classe in cui è dichiarato il costruttore statico. Se viene specificato un altro nome, si verificherà un errore in fase di compilazione.

Quando una dichiarazione di costruttore statico include `extern` un modificatore, il costruttore statico viene definito ***costruttore statico esterno***. Poiché la dichiarazione di un costruttore statico esterno non fornisce alcuna implementazione effettiva, la relativa *static_constructor_body* è costituita da un punto e virgola. Per tutte le altre dichiarazioni di costruttori statici, *static_constructor_body* è costituito da un *blocco* che specifica le istruzioni da eseguire per inizializzare la classe. Corrisponde esattamente al *method_body* di un metodo statico con un tipo restituito `void` (corpo del[Metodo](classes.md#method-body)).

I costruttori statici non vengono ereditati e non possono essere chiamati direttamente.

Il costruttore statico per un tipo di classe Closed viene eseguito al massimo una volta in un determinato dominio applicazione. L'esecuzione di un costruttore statico viene attivata dal primo degli eventi seguenti che si verificano all'interno di un dominio dell'applicazione:

*  Viene creata un'istanza del tipo di classe.
*  Viene fatto riferimento a qualsiasi membro statico del tipo di classe.

Se una classe contiene il `Main` metodo ([avvio dell'applicazione](basic-concepts.md#application-startup)) in cui inizia l'esecuzione, il costruttore statico per tale classe viene eseguito `Main` prima che venga chiamato il metodo.

Per inizializzare un nuovo tipo di classe chiusa, viene creato innanzitutto un nuovo set di campi statici ([campi statici e di istanza](classes.md#static-and-instance-fields)) per quel particolare tipo chiuso. Ogni campo statico viene inizializzato sul valore predefinito ([valori predefiniti](variables.md#default-values)). Successivamente, vengono eseguiti gli inizializzatori di campo statici ([inizializzazione del campo statico](classes.md#static-field-initialization)) per i campi statici. Infine, viene eseguito il costruttore statico.

Esempio
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
deve produrre l'output:
```console
Init A
A.F
Init B
B.F
```
Poiché l'esecuzione del `A`costruttore statico di viene attivata dalla chiamata a `A.F`e l'esecuzione del `B`costruttore statico di viene attivata dalla chiamata a `B.F`.

È possibile creare dipendenze circolari che consentono di osservare i campi statici con inizializzatori di variabili nello stato del valore predefinito.

Esempio
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
```console
X = 1, Y = 2
```

Per eseguire il `Main` metodo, il sistema esegue prima l'inizializzatore `B.Y`per, prima del `B`costruttore statico della classe. `Y`l'inizializzatore `A`di ' s causa l'esecuzione del costruttore statico perché `A.X` viene fatto riferimento al valore di. Il costruttore statico di `A` a sua volta continua a calcolare il valore di `X`e, in questo modo, recupera il valore `Y`predefinito, che è zero. `A.X`viene quindi inizializzato su 1. Il processo di esecuzione `A`degli inizializzatori di campo statici e del costruttore statico viene quindi completato, restituendo al calcolo del valore iniziale `Y`di, il risultato del quale diventa 2.

Poiché il costruttore statico viene eseguito una sola volta per ogni tipo di classe costruito chiuso, è consigliabile applicare controlli di runtime sul parametro di tipo che non possono essere controllati in fase di compilazione tramite vincoli (vincoli del[parametro di tipo](classes.md#type-parameter-constraints)) . Ad esempio, il tipo seguente usa un costruttore statico per applicare che l'argomento di tipo è un'enumerazione:
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

Un ***distruttore*** è un membro che implementa le azioni necessarie per distruggere un'istanza di una classe. Un distruttore viene dichiarato usando un *destructor_declaration*:

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

Un *destructor_declaration* può includere un set di *attributi* ([attributi](attributes.md)).

L' *identificatore* di un *destructor_declaration* deve denominare la classe in cui è dichiarato il distruttore. Se viene specificato un altro nome, si verificherà un errore in fase di compilazione.

Quando una dichiarazione di distruttore include un `extern` modificatore, il distruttore viene definito un ***distruttore esterno***. Poiché una dichiarazione di distruttore esterna non fornisce alcuna implementazione effettiva, la relativa *destructor_body* è costituita da un punto e virgola. Per tutti gli altri distruttori, *destructor_body* è costituito da un *blocco* che specifica le istruzioni da eseguire per distruggere un'istanza della classe. Un *destructor_body* corrisponde esattamente al *method_body* di un metodo di istanza con un tipo restituito `void` ([corpo del metodo](classes.md#method-body)).

I distruttori non vengono ereditati. Pertanto, una classe non dispone di distruttori diversi da quello che può essere dichiarato in tale classe.

Poiché è necessario che un distruttore non abbia parametri, non può essere sottoposto a overload, quindi una classe può avere al massimo un distruttore.

I distruttori vengono richiamati automaticamente e non possono essere richiamati in modo esplicito. Un'istanza diventa idonea per la distruzione quando non è più possibile utilizzare tale istanza per il codice. L'esecuzione del distruttore per l'istanza può verificarsi in qualsiasi momento dopo che l'istanza diventa idonea per la distruzione. Quando un'istanza viene distrutta, i distruttori nella catena di ereditarietà di tale istanza vengono chiamati, in ordine, dal più derivato al meno derivato. Un distruttore può essere eseguito su qualsiasi thread. Per ulteriori informazioni sulle regole che determinano quando e come viene eseguito un distruttore, vedere [gestione automatica della memoria](basic-concepts.md#automatic-memory-management).

Output dell'esempio
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
Poiché i distruttori in una catena di ereditarietà vengono chiamati in ordine, dal più derivato al meno derivato.

I distruttori vengono implementati eseguendo l'override del metodo `Finalize` virtuale `System.Object`in. C#ai programmi non è consentito eseguire l'override di questo metodo o di chiamarlo direttamente o di eseguirne l'override. Ad esempio, il programma
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

Il compilatore si comporta come se questo metodo e le sue sostituzioni non esistessero affatto. Quindi, questo programma:
```csharp
class A 
{
    void Finalize() {}                            // permitted
}
```
è valido e il metodo Mostra il metodo `System.Object`Hides `Finalize` .

Per una descrizione del comportamento quando viene generata un'eccezione da un distruttore, vedere [come vengono gestite le eccezioni](exceptions.md#how-exceptions-are-handled).

## <a name="iterators"></a>Iterators

Un membro di funzione ([membri di funzione](expressions.md#function-members)) implementato utilizzando un blocco iteratore ([blocchi](statements.md#blocks)) viene definito ***iteratore***.

Un blocco iteratore può essere usato come corpo di un membro di funzione purché il tipo restituito del membro di funzione corrispondente sia una delle interfacce di enumeratore ([interfacce di enumeratore](classes.md#enumerator-interfaces)) o una delle interfacce enumerabili[(interfacce enumerabili](classes.md#enumerable-interfaces)) . Può verificarsi come *method_body*, *operator_body* o *accessor_body*, mentre gli eventi, i costruttori di istanza, i costruttori statici e i distruttori non possono essere implementati come iteratori.

Quando un membro di funzione viene implementato usando un blocco iteratore, si tratta di un errore in fase di compilazione per l'elenco di parametri formali del `ref` membro `out` della funzione per specificare i parametri o.

### <a name="enumerator-interfaces"></a>Interfacce enumeratore

Le ***interfacce dell'enumeratore*** sono l'interfaccia `System.Collections.IEnumerator` non generica e tutte le creazioni di istanze `System.Collections.Generic.IEnumerator<T>`dell'interfaccia generica. Per motivi di brevità, in questo capitolo viene fatto riferimento a queste interfacce `IEnumerator` rispettivamente `IEnumerator<T>`come e.

### <a name="enumerable-interfaces"></a>Interfacce enumerabili

Le ***interfacce enumerabili*** sono l'interfaccia `System.Collections.IEnumerable` non generica e tutte le creazioni di istanze dell' `System.Collections.Generic.IEnumerable<T>`interfaccia generica. Per motivi di brevità, in questo capitolo viene fatto riferimento a queste interfacce `IEnumerable` rispettivamente `IEnumerable<T>`come e.

### <a name="yield-type"></a>Tipo yield

Un iteratore produce una sequenza di valori, tutti dello stesso tipo. Questo tipo è denominato ***tipo yield*** dell'iteratore.

*  Tipo di Yield di un iteratore che `IEnumerator` restituisce `IEnumerable` o `object`è.
*  Tipo di Yield di un iteratore che `IEnumerator<T>` restituisce `IEnumerable<T>` o `T`è.

### <a name="enumerator-objects"></a>Oggetti enumeratore

Quando un membro di funzione che restituisce un tipo di interfaccia di enumerazione viene implementato utilizzando un blocco iteratore, la chiamata al membro della funzione non esegue immediatamente il codice nel blocco iteratore. Viene invece creato e restituito un ***oggetto enumeratore*** . Questo oggetto incapsula il codice specificato nel blocco iteratore e l'esecuzione del codice nel blocco iteratore si verifica quando viene richiamato il `MoveNext` metodo dell'oggetto enumeratore. Un oggetto enumeratore presenta le caratteristiche seguenti:

*  Implementa `IEnumerator` e `IEnumerator<T>`, dove`T` è il tipo di risultato dell'iteratore.
*  Implementa `System.IDisposable`.
*  Viene inizializzata con una copia dei valori degli argomenti (se presenti) e del valore dell'istanza passati al membro della funzione.
*  Ha quattro stati potenziali, ***prima***, ***in esecuzione***, ***sospesi***e ***dopo***, e si trova inizialmente nello stato ***before*** .

Un oggetto enumeratore è in genere un'istanza di una classe di enumeratori generata dal compilatore che incapsula il codice nel blocco iteratore e implementa le interfacce dell'enumeratore, ma sono possibili altri metodi di implementazione. Se una classe di enumeratori viene generata dal compilatore, tale classe sarà annidata, direttamente o indirettamente, nella classe contenente il membro della funzione, avrà l'accessibilità privata e avrà un nome riservato per l'uso del compilatore ([identificatori](lexical-structure.md#identifiers)).

Un oggetto enumeratore può implementare più interfacce rispetto a quelle specificate in precedenza.

Nelle sezioni seguenti viene descritto il comportamento esatto dei `MoveNext`membri `Current`, e `Dispose` delle `IEnumerable` implementazioni dell' `IEnumerable<T>` interfaccia e fornite da un oggetto enumeratore.

Si noti che gli oggetti enumeratore non `IEnumerator.Reset` supportano il metodo. Quando si richiama questo metodo, `System.NotSupportedException` viene generata un'eccezione.

#### <a name="the-movenext-method"></a>Metodo MoveNext

Il `MoveNext` metodo di un oggetto enumeratore incapsula il codice di un blocco iteratore. La chiamata del `MoveNext` metodo esegue il codice nel blocco iteratore e imposta la `Current` proprietà dell'oggetto enumeratore nel modo appropriato. L'azione esatta eseguita da `MoveNext` dipende dallo stato dell'oggetto enumeratore quando `MoveNext` viene richiamato:

*  Se lo stato dell'oggetto enumeratore è ***precedente***, richiamare `MoveNext`:
   * Imposta lo stato su ***in esecuzione***.
   * Inizializza i parametri (incluso `this`) del blocco iteratore sui valori degli argomenti e sul valore dell'istanza salvati quando l'oggetto enumeratore è stato inizializzato.
   * Esegue il blocco iteratore dall'inizio fino a quando l'esecuzione non viene interrotta (come descritto di seguito).
*  Se lo stato dell'oggetto enumeratore è ***in esecuzione***, il risultato della chiamata `MoveNext` non è specificato.
*  Se lo stato dell'oggetto enumeratore è ***sospeso***, richiamando `MoveNext`:
   * Imposta lo stato su ***in esecuzione***.
   * Ripristina i valori di tutte le variabili e i parametri locali (incluso) ai valori salvati durante l'ultima sospensione dell'esecuzione del blocco iteratore. Si noti che il contenuto degli oggetti a cui fanno riferimento queste variabili potrebbe essere stato modificato rispetto alla precedente chiamata a MoveNext.
   * Riprende l'esecuzione del blocco iteratore immediatamente dopo l' `yield return` istruzione che ha causato la sospensione dell'esecuzione e continua fino a quando l'esecuzione non viene interrotta (come descritto di seguito).
*  Se lo stato dell'oggetto enumeratore è ***dopo***, la chiamata `MoveNext` di `false`restituisce.


Quando `MoveNext` esegue il blocco iteratore, l'esecuzione può essere interrotta in quattro modi: Da un'istruzione, da un' `yield break` istruzione, che rileva la fine del blocco iteratore e da un'eccezione generata e propagata all'esterno del blocco iteratore. `yield return`

*  Quando viene `yield return` rilevata un'istruzione ([istruzione yield](statements.md#the-yield-statement)):
   * L'espressione specificata nell'istruzione viene valutata, convertita in modo implicito nel tipo yield e assegnata `Current` alla proprietà dell'oggetto enumeratore.
   * L'esecuzione del corpo dell'iteratore è sospesa. I valori di tutte le variabili e i parametri locali `this`(incluso) vengono salvati, così come il percorso `yield return` di questa istruzione. Se l' `yield return` istruzione si trova all'interno di `try` uno o più blocchi `finally` , i blocchi associati non vengono eseguiti in questo momento.
   * Lo stato dell'oggetto enumeratore viene modificato in ***sospeso***.
   * Il `MoveNext` metodo restituisce `true` al chiamante, a indicare che l'iterazione è stata avanzata correttamente in base al valore successivo.
*  Quando viene `yield break` rilevata un'istruzione ([istruzione yield](statements.md#the-yield-statement)):
   * Se l' `yield break` istruzione si trova all'interno di `try` uno o più blocchi `finally` , vengono eseguiti i blocchi associati.
   * Lo stato dell'oggetto enumeratore viene modificato in ***dopo***.
   * Il `MoveNext` metodo restituisce `false` al chiamante, a indicare che l'iterazione è completa.
*  Quando viene rilevata la fine del corpo dell'iteratore:
   * Lo stato dell'oggetto enumeratore viene modificato in ***dopo***.
   * Il `MoveNext` metodo restituisce `false` al chiamante, a indicare che l'iterazione è completa.
*  Quando viene generata un'eccezione e viene propagata all'esterno del blocco iteratore:
   * I `finally` blocchi appropriati nel corpo dell'iteratore sono stati eseguiti dalla propagazione delle eccezioni.
   * Lo stato dell'oggetto enumeratore viene modificato in ***dopo***.
   * La propagazione delle eccezioni continua al chiamante del `MoveNext` metodo.

#### <a name="the-current-property"></a>Proprietà corrente.

La `Current` proprietà di un oggetto enumeratore è `yield return` interessata dalle istruzioni nel blocco iteratore.

Quando un oggetto enumeratore si trova nello stato ***suspended*** , il `Current` valore di è il valore impostato dalla chiamata precedente `MoveNext`a. Quando un oggetto enumeratore si trova negli Stati ***before***, ***Running***o ***after*** , `Current` il risultato dell'accesso a non è specificato.

Per un iteratore con un tipo yield diverso `object`da, il risultato dell' `Current` accesso `IEnumerable` tramite l'implementazione dell'oggetto enumeratore corrisponde `IEnumerator<T>` all'accesso `Current` tramite l'oggetto dell'enumeratore implementazione ed esecuzione del cast del `object`risultato a.

#### <a name="the-dispose-method"></a>Metodo Dispose

Il `Dispose` metodo viene usato per pulire l'iterazione portando l'oggetto enumeratore allo stato ***after*** .

*  Se lo stato dell'oggetto enumeratore è ***precedente***, la chiamata `Dispose` di cambia lo stato in ***dopo***.
*  Se lo stato dell'oggetto enumeratore è ***in esecuzione***, il risultato della chiamata `Dispose` non è specificato.
*  Se lo stato dell'oggetto enumeratore è ***sospeso***, richiamando `Dispose`:
   * Imposta lo stato su ***in esecuzione***.
   * Esegue qualsiasi blocco finally come se l'ultima istruzione `yield return` eseguita fosse un' `yield break` istruzione. Se in questo modo viene generata un'eccezione e viene propagata all'esterno del corpo dell'iteratore, lo stato dell'oggetto enumeratore viene impostato su ***after*** e l'eccezione viene propagata `Dispose` al chiamante del metodo.
   * Imposta lo stato su ***after***.
*  Se lo stato dell'oggetto enumeratore è ***after***, la chiamata `Dispose` a non ha alcun effetto.

### <a name="enumerable-objects"></a>Oggetti enumerabili

Quando un membro di funzione che restituisce un tipo di interfaccia enumerabile viene implementato utilizzando un blocco iteratore, la chiamata al membro della funzione non esegue immediatamente il codice nel blocco iteratore. Viene invece creato e restituito un ***oggetto enumerabile*** . Il `GetEnumerator` metodo dell'oggetto enumerabile restituisce un oggetto enumeratore che incapsula il codice specificato nel blocco iteratore e l'esecuzione del codice nel blocco iteratore si verifica quando viene richiamato `MoveNext` il metodo dell'oggetto enumeratore. Un oggetto enumerabile presenta le caratteristiche seguenti:

*  Implementa `IEnumerable` e `IEnumerable<T>`, dove`T` è il tipo di risultato dell'iteratore.
*  Viene inizializzata con una copia dei valori degli argomenti (se presenti) e del valore dell'istanza passati al membro della funzione.

Un oggetto enumerabile è in genere un'istanza di una classe enumerabile generata dal compilatore che incapsula il codice nel blocco iteratore e implementa le interfacce enumerabili, ma sono possibili altri metodi di implementazione. Se una classe enumerabile viene generata dal compilatore, tale classe sarà nidificata, direttamente o indirettamente, nella classe contenente il membro della funzione, avrà accessibilità privata e avrà un nome riservato per l'uso del compilatore ([identificatori](lexical-structure.md#identifiers)).

Un oggetto enumerabile può implementare più interfacce rispetto a quelle specificate in precedenza. In particolare, un oggetto enumerabile può implementare `IEnumerator` anche `IEnumerator<T>`e, consentendo di fungere da enumeratore e enumeratore. In tale tipo di implementazione, la prima volta che viene richiamato `GetEnumerator` il metodo di un oggetto enumerabile, viene restituito l'oggetto enumerabile stesso. Le chiamate successive dell'oggetto `GetEnumerator`enumerabile, se presente, restituiscono una copia dell'oggetto enumerabile. Pertanto, ogni enumeratore restituito ha il proprio stato e le modifiche apportate in un enumeratore non avranno effetto su un'altra.

#### <a name="the-getenumerator-method"></a>Metodo GetEnumerator

Un oggetto enumerabile fornisce un'implementazione dei `GetEnumerator` metodi `IEnumerable` delle interfacce e `IEnumerable<T>` . I due `GetEnumerator` metodi condividono un'implementazione comune che acquisisce e restituisce un oggetto enumeratore disponibile. L'oggetto enumeratore viene inizializzato con i valori dell'argomento e il valore dell'istanza salvati quando l'oggetto enumerabile è stato inizializzato. in caso contrario, l'oggetto enumeratore funziona come descritto in [oggetti enumeratore](classes.md#enumerator-objects).

### <a name="implementation-example"></a>Esempio di implementazione

In questa sezione viene descritta una possibile implementazione di iteratori in termini C# di costrutti standard. L'implementazione descritta di seguito si basa sugli stessi principi usati dal compilatore Microsoft C# , ma non è un'implementazione obbligatoria o l'unico possibile.

La classe `Stack<T>` seguente `GetEnumerator` implementa il metodo usando un iteratore. L'iteratore enumera gli elementi dello stack nell'ordine dall'alto verso il basso.

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

Il `GetEnumerator` metodo può essere convertito in un'istanza di una classe di enumeratori generata dal compilatore che incapsula il codice nel blocco iteratore, come illustrato di seguito.

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

Nella conversione precedente, il codice nel blocco iteratore viene trasformato in una macchina a Stati e inserito nel `MoveNext` metodo della classe Enumerator. Inoltre, la variabile `i` locale viene trasformata in un campo nell'oggetto enumeratore, in modo che possa continuare a esistere tra le chiamate di. `MoveNext`

Nell'esempio seguente viene stampata una semplice tabella di moltiplicazione dei numeri interi da 1 a 10. Il `FromTo` metodo nell'esempio restituisce un oggetto enumerabile ed è implementato utilizzando un iteratore.

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

Il `FromTo` metodo può essere convertito in un'istanza di una classe Enumerable generata dal compilatore che incapsula il codice nel blocco iteratore, come illustrato di seguito.

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

La classe Enumerable implementa sia le interfacce enumerabili che le interfacce dell'enumeratore, consentendo di fungere da enumeratore e enumeratore. La prima volta che `GetEnumerator` il metodo viene richiamato, viene restituito l'oggetto enumerabile stesso. Le chiamate successive dell'oggetto `GetEnumerator`enumerabile, se presente, restituiscono una copia dell'oggetto enumerabile. Pertanto, ogni enumeratore restituito ha il proprio stato e le modifiche apportate in un enumeratore non avranno effetto su un'altra. Il `Interlocked.CompareExchange` metodo viene usato per garantire l'operazione thread-safe.

I `from` parametri `to` e vengono convertiti in campi nella classe Enumerable. Poiché `from` viene modificato nel blocco iteratore, viene introdotto `__from` un campo aggiuntivo per conservare il valore iniziale assegnato a `from` in ogni enumeratore.

Il `MoveNext` metodo genera un' `InvalidOperationException` eccezione se viene chiamato quando `__state` è `0`. In questo modo si impedisce l'utilizzo dell'oggetto enumerabile come oggetto enumeratore `GetEnumerator`senza prima chiamare.

Nell'esempio seguente viene illustrata una semplice classe Tree. La `Tree<T>` classe`GetEnumerator` implementa il metodo usando un iteratore. L'iteratore enumera gli elementi della struttura ad albero in ordine infisso.

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

Il `GetEnumerator` metodo può essere convertito in un'istanza di una classe di enumeratori generata dal compilatore che incapsula il codice nel blocco iteratore, come illustrato di seguito.

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

I temporaries generati dal compilatore usati nelle `foreach` istruzioni vengono rimossi `__left` nei campi e `__right` dell'oggetto enumeratore. Il `__state` campo dell'oggetto enumeratore viene aggiornato con attenzione, in modo `Dispose()` che il metodo corretto venga chiamato correttamente se viene generata un'eccezione. Si noti che non è possibile scrivere il codice tradotto con istruzioni `foreach` semplici.

## <a name="async-functions"></a>Funzioni asincrone

Un metodo ([Methods](classes.md#methods)) o una funzione anonima ([espressioni di funzione anonima](expressions.md#anonymous-function-expressions)) con il `async` modificatore è denominato ***funzione asincrona***. In generale, il termine ***Async*** viene usato per descrivere qualsiasi tipo di funzione con il `async` modificatore.

Si tratta di un errore in fase di compilazione per l'elenco di parametri formali di una funzione `ref` asincrona `out` per specificare i parametri o.

Il *return_type* di un metodo asincrono deve essere `void` o un ***tipo di attività***. I tipi di attività `System.Threading.Tasks.Task` sono e i tipi `System.Threading.Tasks.Task<T>`costruiti da. Per motivi di brevità, in questo capitolo viene fatto riferimento a questi tipi `Task` rispettivamente `Task<T>`come e. Un metodo asincrono che restituisce un tipo di attività viene detto restituzione di attività.

La definizione esatta dei tipi di attività è definita dall'implementazione, ma dal punto di vista del linguaggio un tipo di attività è in uno degli Stati incompleti, Succeeded o Faulted. Un'attività con errori registra un'eccezione pertinente. Un oggetto `Task<T>` è riuscito a registrare un `T`risultato di tipo. I tipi di attività sono awaitable e possono quindi essere gli operandi di espressioni await ([espressioni await](expressions.md#await-expressions)).

Una chiamata di funzione asincrona ha la possibilità di sospendere la valutazione per mezzo delle espressioni await ([await](expressions.md#await-expressions)) nel corpo. La valutazione può essere ripresa in un secondo momento nel punto in cui viene sospesa l'espressione await per mezzo di un ***delegato di ripresa***. Il delegato di ripresa è di `System.Action`tipo e, quando viene richiamato, la valutazione della chiamata della funzione asincrona riprenderà dall'espressione await da dove era stata interrotta. Il chiamante ***corrente*** di una chiamata di funzione asincrona è il chiamante originale se la chiamata di funzione non è mai stata sospesa o il chiamante più recente del delegato di ripresa in caso contrario.

### <a name="evaluation-of-a-task-returning-async-function"></a>Valutazione di una funzione asincrona che restituisce un'attività

La chiamata di una funzione asincrona che restituisce un'attività causa la generazione di un'istanza del tipo di attività restituito. Questa operazione viene definita ***attività return*** della funzione asincrona. L'attività inizialmente si trova in uno stato incompleto.

Il corpo della funzione asincrona viene quindi valutato fino a quando non viene sospeso (raggiungendo un'espressione await) o termina, in cui il controllo del punto viene restituito al chiamante, insieme all'attività return.

Quando il corpo della funzione asincrona termina, l'attività return viene spostata allo stato incompleto:

*  Se il corpo della funzione termina in seguito al raggiungimento di un'istruzione return o alla fine del corpo, qualsiasi valore di risultato viene registrato nell'attività return, che viene inserita in uno stato succeeded.
*  Se il corpo della funzione termina come risultato di un'eccezione non rilevata ([istruzione throw](statements.md#the-throw-statement)), l'eccezione viene registrata nell'attività return, che viene inserita in uno stato di errore.

### <a name="evaluation-of-a-void-returning-async-function"></a>Valutazione di una funzione asincrona che restituisce void

Se il tipo restituito della funzione asincrona è `void`, la valutazione differisce dal precedente nel modo seguente: Poiché non viene restituita alcuna attività, la funzione comunica invece il completamento e le eccezioni al ***contesto di sincronizzazione***del thread corrente. La definizione esatta del contesto di sincronizzazione è dipendente dall'implementazione, ma è una rappresentazione "Where" del thread corrente in esecuzione. Il contesto di sincronizzazione riceve una notifica quando inizia la valutazione di una funzione asincrona che restituisce void, viene completata correttamente o causa la generazione di un'eccezione non rilevata.

Ciò consente al contesto di tenere traccia del numero di funzioni asincrone che restituiscono void e di decidere come propagare le eccezioni in uscita.
