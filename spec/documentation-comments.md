# <a name="documentation-comments"></a>Commenti della documentazione

C# fornisce un meccanismo per i programmatori di documentare il codice usando una sintassi di commento speciali che contiene il testo XML. Nei file di codice sorgente, è possibile utilizzare i commenti con un determinato formato per indirizzare uno strumento per generare codice XML da tali commenti e gli elementi di codice sorgente, precedano. I commenti usando tale sintassi vengono chiamati ***commenti relativi alla documentazione***. Devono precedere immediatamente un tipo definito dall'utente (ad esempio, una classe, delegato o interfaccia) o un membro (ad esempio un campo, evento, proprietà o metodo). Lo strumento di generazione di XML viene chiamato il ***generatore di documentazione***. (Questo generatore può, ma non è necessario, il compilatore c# stesso.) L'output prodotto dal generatore di documentazione viene chiamato il ***file di documentazione***. Un file di documentazione viene usato come input per un ***Visualizzatore della documentazione***; un strumento intende produrre una sorta di rappresentazione visiva delle informazioni sul tipo e la relativa documentazione.

Questa specifica è indicato un set di tag da utilizzare nei commenti della documentazione, ma non è necessario utilizzare questi tag e gli altri tag può essere utilizzato, se si desidera, come vengono seguite lungo le regole di codice XML ben formato.

## <a name="introduction"></a>Introduzione

Commenti con una sintassi speciale utilizzabile per indirizzare uno strumento per generare codice XML da tali commenti e gli elementi di codice sorgente, precedano. Questi commenti sono commenti a riga singola che iniziano con tre barre (`///`), o delimitati commenti che iniziano con una barra e due stelle (`/**`). Devono precedere immediatamente un tipo definito dall'utente (ad esempio, una classe, delegato o interfaccia) o un membro che si annota (ad esempio, un campo, evento, proprietà o metodo). Sezioni dell'attributo ([specifica degli attributi](attributes.md#attribute-specification)) sono considerati parte di dichiarazioni, in modo che i commenti della documentazione devono precedere gli attributi applicati a un tipo o membro.

__Sintassi:__

```antlr
single_line_doc_comment
    : '///' input_character*
    ;

delimited_doc_comment
    : '/**' delimited_comment_section* asterisk+ '/'
    ;
```

In un *single_line_doc_comment*, se è presente un *uno spazio vuoto* carattere che segue il `///` caratteri in ogni la *single_line_doc_comment*s adiacenti all'oggetto corrente *single_line_doc_comment*, quindi che *uno spazio vuoto* carattere non è incluso nell'output XML.

In un delimitato--commento doc, se il primo carattere diverso dallo spazio sulla seconda riga è un asterisco e lo stesso modello di caratteri di spazio vuoto facoltativo e il carattere asterisco viene ripetuta all'inizio di ogni riga del commento di doc delimitato, quindi i caratteri del modello ripetuto non sono inclusi nell'output XML. Il modello può includere spazi vuoti dopo il, sia prima che il carattere asterisco.

__Esempio:__

```csharp
/// <summary>Class <c>Point</c> models a point in a two-dimensional
/// plane.</summary>
///
public class Point 
{
    /// <summary>method <c>draw</c> renders the point.</summary>
    void draw() {...}
}
```

Il testo nei commenti della documentazione deve essere corretto in base alle regole del linguaggio XML (http://www.w3.org/TR/REC-xml). Se il codice XML non è valido, viene generato un avviso e il file di documentazione conterrà un commento per informare che si è verificato un errore.

Anche se gli sviluppatori sono liberi di creare il proprio set di tag, un set è definito [tag consigliati](documentation-comments.md#recommended-tags). Alcuni tag consigliati hanno un significato speciale:

*  Il `<param>` tag viene usato per descrivere i parametri. Se viene utilizzato un tag di questo tipo, il generatore di documentazione necessario verificare che il parametro specificato esista e che tutti i parametri sono descritte nei commenti della documentazione. Se tale verifica ha esito negativo, il generatore di documentazione genera un avviso.
*  L'attributo `cref` può essere associato a qualsiasi tag per fornire un riferimento a un elemento del codice. Il generatore di documentazione deve verificare l'esistenza di questo elemento di codice. Se la verifica ha esito negativo, il generatore di documentazione genera un avviso. Durante la ricerca di un nome descritto in una `cref` attributo, il generatore di documentazione deve rispettare la visibilità dello spazio dei nomi in base alla `using` istruzioni che si trovano all'interno del codice sorgente. Per gli elementi di codice che sono di tipo generici, la normale sintassi generica (ad esempio "`List<T>`") non può essere usato perché produce codice XML non valido. Posto tra parentesi quadre è possibile usare le parentesi graffe (ie "`List{T}`"), oppure è possibile usare la sintassi di escape XML (ad esempio "`List&lt;T&gt;`").
*  Il `<summary>` tag dovrà essere utilizzato da un visualizzatore della documentazione per visualizzare informazioni aggiuntive su un tipo o membro.
*  Il `<include>` tag include le informazioni da un file XML esterno.

Notare che il file di documentazione non fornisce informazioni complete sul tipo e i membri (ad esempio, non contiene alcuna informazione sul tipo). Per ottenere tali informazioni su un tipo o membro, il file della documentazione deve essere usato in combinazione con reflection sul tipo effettivo o sul membro.

## <a name="recommended-tags"></a>Tag consigliati

Il generatore di documentazione deve accettare ed elaborare qualsiasi tag valido secondo le regole del linguaggio XML. I tag seguenti forniscono le funzionalità comunemente utilizzate nella documentazione dell'utente. (Naturalmente, gli altri tag sono possibili).


| __Tag__          | __Sezione__                                            | __Scopo__                                            |
|------------------|--------------------------------------------------------|--------------------------------------------------------|
| `<c>`            | [`<c>`](documentation-comments.md#c)                   | Impostare il testo in un tipo di carattere del codice simile a                           | 
| `<code>`         | [`<code>`](documentation-comments.md#code)             | Impostare uno o più righe di output di codice o il programma di origine |
| `<example>`      | [`<example>`](documentation-comments.md#example)       | Indicare un esempio                                    |
| `<exception>`    | [`<exception>`](documentation-comments.md#exception)   | Identifica un metodo può generare eccezioni           |
| `<include>`      | [`<include>`](documentation-comments.md#include)       | Include XML da un file esterno                     |
| `<list>`         | [`<list>`](documentation-comments.md#list)             | Creare un elenco o una tabella                                 |
| `<para>`         | [`<para>`](documentation-comments.md#para)             | Consentire l'aggiunta al testo di una struttura                   |
| `<param>`        | [`<param>`](documentation-comments.md#param)           | Descrivere un parametro per un metodo o costruttore       |
| `<paramref>`     | [`<paramref>`](documentation-comments.md#paramref)     | Stabilire se una parola è un nome di parametro               |
| `<permission>`   | [`<permission>`](documentation-comments.md#permission) | L'accessibilità di sicurezza di un membro del documento        |
| `<remark>`       | [`<remark>`](documentation-comments.md#remark)         | Vengono descritti informazioni aggiuntive su un tipo           |
| `<returns>`      | [`<returns>`](documentation-comments.md#returns)       | Descrivere il valore restituito di un metodo                  |
| `<see>`          | [`<see>`](documentation-comments.md#see)               | Specificare un collegamento                                         |
| `<seealso>`      | [`<seealso>`](documentation-comments.md#seealso)       | Generare una voce di vedere anche                              |
| `<summary>`      | [`<summary>`](documentation-comments.md#summary)       | Descrivere un tipo o un membro di un tipo                  |
| `<value>`        | [`<value>`](documentation-comments.md#value)           | Descrivere una proprietà                                    |
| `<typeparam>`    |                                                        | Descrivere un parametro di tipo generico                      |
| `<typeparamref>` |                                                        | Stabilire se una parola è un nome di parametro di tipo          |

### `<c>`

Questo tag fornisce un meccanismo per indicare che un frammento di testo all'interno di una descrizione deve essere impostato in un carattere speciale come quello usato per un blocco di codice. Per le righe di codice effettivo, utilizzare `<code>` ([`<code>`](documentation-comments.md#code)).

__Sintassi:__

```xml
<c>text</c>
```

__Esempio:__

```csharp
/// <summary>Class <c>Point</c> models a point in a two-dimensional
/// plane.</summary>

public class Point 
{
    // ...
}
```

### `<code>`

Questo tag viene usato per impostare uno o più righe di output di codice o il programma di origine in un carattere speciale. Per i piccoli frammenti di codice in testo descrittivo, utilizzare `<c>` ([`<c>`](documentation-comments.md#c)).

__Sintassi:__

```xml
<code>source code or program output</code>
```

__Esempio:__

```csharp
/// <summary>This method changes the point's location by
///    the given x- and y-offsets.
/// <example>For example:
/// <code>
///    Point p = new Point(3,5);
///    p.Translate(-1,3);
/// </code>
/// results in <c>p</c>'s having the value (2,8).
/// </example>
/// </summary>

public void Translate(int xor, int yor) {
    X += xor;
    Y += yor;
}   
```

### `<example>`

Questo tag consente al codice di esempio all'interno di un commento, per specificare la modalità può essere utilizzato un metodo o altro membro della raccolta. In genere, ciò anche comporta l'uso del tag `<code>` ([`<code>`](documentation-comments.md#code)) nonché.

__Sintassi:__

```xml
<example>description</example>
```

__Esempio:__

Visualizzare `<code>` ([`<code>`](documentation-comments.md#code)) per un esempio.

### `<exception>`

Questo tag consente di documentare le eccezioni che può generare un metodo.

__Sintassi:__

```xml
<exception cref="member">description</exception>
```

dove

* `member` è il nome di un membro. Il generatore di documentazione controlla che il membro specificato esista e converte `member` al nome canonico dell'elemento nel file di documentazione.
* `description` è una descrizione delle circostanze in cui viene generata l'eccezione.

__Esempio:__

```csharp
public class DataBaseOperations
{
    /// <exception cref="MasterFileFormatCorruptException"></exception>
    /// <exception cref="MasterFileLockedOpenException"></exception>
    public static void ReadRecord(int flag) {
        if (flag == 1)
            throw new MasterFileFormatCorruptException();
        else if (flag == 2)
            throw new MasterFileLockedOpenException();
        // ...
    } 
}
```

### `<include>`

Questo tag consente anche dal documento XML che è esterno al file del codice sorgente. Il file esterno deve essere un documento XML ben formato e un'espressione XPath viene applicata a un documento per specificare quali XML da tale documento da includere. Il `<include>` tag verrà quindi sostituito con le informazioni XML del documento esterna.

__Sintassi:__

```
<include file="filename" path="xpath" />
```

dove

* `filename` è il nome del file di un file XML esterno. Viene interpretato il nome del file relativo al file che contiene il tag di inclusione.
* `xpath` è un'espressione XPath che seleziona alcune XML nel file XML esterno.

__Esempio:__

Se il codice sorgente contiene una dichiarazione, ad esempio:

```csharp
/// <include file="docs.xml" path='extradoc/class[@name="IntList"]/*' />
public class IntList { ... }
```

il file esterno "docs. XML" sia stato soddisfacente e il contenuto seguente:

```xml
<?xml version="1.0"?>
<extradoc>
  <class name="IntList">
     <summary>
        Contains a list of integers.
     </summary>
  </class>
  <class name="StringList">
     <summary>
        Contains a list of integers.
     </summary>
  </class>
</extradoc>
```

quindi la documentazione stessa è output come se il codice sorgente contenuto:

```csharp
/// <summary>
///    Contains a list of integers.
/// </summary>
public class IntList { ... }
```

### `<list>`

Questo tag viene usato per creare un elenco o una tabella di elementi. Può contenere un `<listheader>` blocco per definire la riga di intestazione della tabella o una definizione elenco. (Quando si definisce una tabella, solo una voce per `term` nell'intestazione devono essere fornite.)

Ogni elemento nell'elenco viene specificato con un `<item>` blocco. Quando si crea un elenco di definizioni, entrambe `term` e `description` deve essere specificato. Tuttavia, per una tabella, elenco puntato o numerato, solo `description` è necessario specificarlo.

__Sintassi:__

```xml
<list type="bullet" | "number" | "table">
   <listheader>
      <term>term</term>
      <description>*description*</description>
   </listheader>
   <item>
      <term>term</term>
      <description>*description*</description>
   </item>
    ...
   <item>
      <term>term</term>
      <description>description</description>
   </item>
</list>
```

dove

* `term` è il termine da definire, la cui definizione è `description`.
* `description` è un elemento in un elenco puntato o numerato oppure la definizione di un `term`.

__Esempio:__

```csharp
public class MyClass
{
    /// <summary>Here is an example of a bulleted list:
    /// <list type="bullet">
    /// <item>
    /// <description>Item 1.</description>
    /// </item>
    /// <item>
    /// <description>Item 2.</description>
    /// </item>
    /// </list>
    /// </summary>
    public static void Main () {
        // ...
    }
}
```

### `<para>`

Questo tag è utilizzato all'interno di altri tag, ad esempio `<summary>` ([`<remark>`](documentation-comments.md#remark)) o `<returns>` ([`<returns>`](documentation-comments.md#returns)) e consente una struttura da aggiungere al testo.

__Sintassi:__

```xml
<para>content</para>
```

in cui `content` è il testo del paragrafo.

__Esempio:__

```csharp
/// <summary>This is the entry point of the Point class testing program.
/// <para>This program tests each method and operator, and
/// is intended to be run after any non-trivial maintenance has
/// been performed on the Point class.</para></summary>
public static void Main() {
    // ...
}
```

### `<param>`

Questo tag viene usato per descrivere un parametro per un metodo, un costruttore o un indicizzatore.

__Sintassi:__

```xml
<param name="name">description</param>
```

dove

* `name` È il nome del parametro.
* `description` è una descrizione del parametro.

__Esempio:__

```csharp
/// <summary>This method changes the point's location to
///    the given coordinates.</summary>
/// <param name="xor">the new x-coordinate.</param>
/// <param name="yor">the new y-coordinate.</param>
public void Move(int xor, int yor) {
    X = xor;
    Y = yor;
}
```

### `<paramref>`

Questo tag viene usato per indicare che una parola è un parametro. Il file di documentazione può essere elaborato per formattare questo parametro in modo specifico.

__Sintassi:__

```xml
<paramref name="name"/>
```

in cui `name` è il nome del parametro.

__Esempio:__

```csharp
/// <summary>This constructor initializes the new Point to
///    (<paramref name="xor"/>,<paramref name="yor"/>).</summary>
/// <param name="xor">the new Point's x-coordinate.</param>
/// <param name="yor">the new Point's y-coordinate.</param>

public Point(int xor, int yor) {
    X = xor;
    Y = yor;
}
```

### `<permission>`

Questo tag consente l'accessibilità di sicurezza di un membro da documentare.

__Sintassi:__

```xml
<permission cref="member">description</permission>
```

dove

* `member` è il nome di un membro. Il generatore di documentazione controlla che l'elemento di codice specificato esista e converte *membro* al nome canonico dell'elemento nel file di documentazione.
* `description` è una descrizione dell'accesso al membro.

__Esempio:__

```csharp
/// <permission cref="System.Security.PermissionSet">Everyone can
/// access this method.</permission>

public static void Test() {
    // ...
}
```

### `<remark>`

Questo tag viene usato per specificare informazioni aggiuntive su un tipo. (Usare `<summary>` ([`<summary>`](documentation-comments.md#summary)) per descrivere il tipo stesso e i membri di un tipo.)

__Sintassi:__

```xml
<remark>description</remark>
```

in cui `description` è il testo delle osservazioni.

__Esempio:__

```csharp
/// <summary>Class <c>Point</c> models a point in a 
/// two-dimensional plane.</summary>
/// <remark>Uses polar coordinates</remark>
public class Point 
{
    // ...
}
```

### `<returns>`

Questo tag viene usato per descrivere il valore restituito di un metodo.

__Sintassi:__

```xml
<returns>description</returns>
```

in cui `description` è una descrizione del valore restituito.

__Esempio:__

```csharp
/// <summary>Report a point's location as a string.</summary>
/// <returns>A string representing a point's location, in the form (x,y),
///    without any leading, trailing, or embedded whitespace.</returns>
public override string ToString() {
    return "(" + X + "," + Y + ")";
}
```

### `<see>`

Questo tag consente di inserire un collegamento a essere specificata all'interno del testo. Uso `<seealso>` ([`<seealso>`](documentation-comments.md#seealso)) per indicare il testo che deve essere visualizzato in una sezione Vedere anche.

__Sintassi:__

```xml
<see cref="member"/>
```

in cui `member` è il nome del membro. Il generatore di documentazione controlla che l'elemento di codice specificato esista e che viene modificato *membro* al nome dell'elemento nel file di documentazione.

__Esempio:__

```csharp
/// <summary>This method changes the point's location to
///    the given coordinates.</summary>
/// <see cref="Translate"/>
public void Move(int xor, int yor) {
    X = xor;
    Y = yor;
}

/// <summary>This method changes the point's location by
///    the given x- and y-offsets.
/// </summary>
/// <see cref="Move"/>
public void Translate(int xor, int yor) {
    X += xor;
    Y += yor;
}
```

### `<seealso>`

Questo tag consente una voce da generare per la sezione Vedere anche. Uso `<see>` ([`<see>`](documentation-comments.md#see)) per specificare un collegamento nel testo.

__Sintassi:__

```xml
<seealso cref="member"/>
```

in cui `member` è il nome del membro. Il generatore di documentazione controlla che l'elemento di codice specificato esista e che viene modificato *membro* al nome dell'elemento nel file di documentazione.

__Esempio:__

```csharp
/// <summary>This method determines whether two Points have the same
///    location.</summary>
/// <seealso cref="operator=="/>
/// <seealso cref="operator!="/>
public override bool Equals(object o) {
    // ...
}
```

### `<summary>`

Questo tag è utilizzabile per descrivere un tipo o un membro di un tipo. Uso `<remark>` ([`<remark>`](documentation-comments.md#remark)) per descrivere il tipo stesso.

__Sintassi:__

```xml
<summary>description</summary>
```

in cui `description` è riportato un riepilogo del tipo o membro.

__Esempio:__

```csharp
/// <summary>This constructor initializes the new Point to (0,0).</summary>
public Point() : this(0,0) {
}
```

### `<value>`

Questo tag consente di descrivere una proprietà.

__Sintassi:__

```xml
<value>property description</value>
```

in cui `property description` è una descrizione per la proprietà.

__Esempio:__

```csharp
/// <value>Property <c>X</c> represents the point's x-coordinate.</value>
public int X
{
    get { return x; }
    set { x = value; }
}
```

### `<typeparam>`

Questo tag viene usato per descrivere un parametro di tipo generico per una classe, struct, interfaccia, delegato o metodo.

__Sintassi:__

```xml
<typeparam name="name">description</typeparam>
```

in cui `name` è il nome del parametro di tipo, e `description` è la relativa descrizione.

__Esempio:__

```csharp
/// <summary>A generic list class.</summary>
/// <typeparam name="T">The type stored by the list.</typeparam>
public class MyList<T> {
    ...
}
```

### `<typeparamref>`

Questo tag viene usato per indicare che una parola è un parametro di tipo. Il file di documentazione può essere elaborato per formattare questo parametro di tipo in modo specifico.

__Sintassi:__

```xml
<typeparamref name="name"/>
```

in cui `name` è il nome del parametro di tipo.

__Esempio:__

```csharp
/// <summary>This method fetches data and returns a list of <typeparamref name="T"/>.</summary>
/// <param name="query">query to execute</param>
public List<T> FetchData<T>(string query) {
    ...
}
```

## <a name="processing-the-documentation-file"></a>L'elaborazione del file di documentazione

Il generatore di documentazione genera una stringa ID per ogni elemento nel codice sorgente che è contrassegnato con un commento della documentazione. Questa stringa ID identifica in modo univoco un elemento di origine. Un visualizzatore della documentazione è possibile usare una stringa ID per identificare il corrispondente elemento metadati/reflection a cui si applica la documentazione.

Il file di documentazione non è una rappresentazione gerarchica del codice sorgente. piuttosto, è un elenco semplice con una stringa ID generato per ogni elemento.

### <a name="id-string-format"></a>Formato della stringa ID

Quando genera le stringhe di ID, il generatore di documentazione osserva le regole seguenti:

*  Assenza di spazi vuoti nella stringa.

*  La prima parte della stringa identifica il tipo di membro in corso documentati, applicato tramite un singolo carattere seguito da due punti. Sono definiti i tipi di membri seguenti:

   | __Carattere__ | __Descrizione__                                             |
   |---------------|-------------------------------------------------------------|
   | E             | event                                                       |
   | F             | Campo                                                       |
   | M             | Metodo (compresi i costruttori, distruttori e operatori) |
   | N             | Spazio dei nomi                                                   |
   | P             | Proprietà (compresi gli indicizzatori)                               |
   | T             | Tipo (ad esempio classi, delegato, enum, interfaccia e struct) |
   | !             | Stringa di errore. il resto della stringa contiene informazioni sull'errore. Ad esempio, il generatore di documentazione genera informazioni di errore per i collegamenti che non possono essere risolto. |

*  La seconda parte della stringa è il nome completo dell'elemento, iniziando dalla radice dello spazio dei nomi. Il nome dell'elemento, relativa inclusione i tipi e lo spazio dei nomi sono separati da punti. Se il nome dell'elemento stesso contiene punti, vengono sostituiti con `#(U+0023)` caratteri. (Si presuppone che nessun elemento dispone di tale carattere nel nome.)
*  Per i metodi e proprietà con argomenti, l'elenco degli argomenti nel seguente modo, racchiuso tra parentesi. Per coloro che senza argomenti, le parentesi vengono omesse. Gli argomenti sono separati da virgole. La codifica di ciascun argomento è lo stesso come firma dell'interfaccia della riga, come indicato di seguito:
   *  Gli argomenti sono rappresentati dal relativo nome di documentazione che si basa sul nome completo, modificato come segue:
      * Gli argomenti che rappresentano i tipi generici hanno un'aggiunta `` ` `` carattere (apice inverso) seguito dal numero di parametri di tipo
      * Negli argomenti con la `out` oppure `ref` modificatore hanno un `@` dopo il nome del tipo. Gli argomenti passati per valore o mediante `params` non hanno una speciale notazione.
      * Gli argomenti di matrici vengono rappresentati come `[lowerbound:size, ... , lowerbound:size]` in cui il numero di virgole indica il numero di dimensioni meno uno e i limiti inferiore e dimensioni di ogni dimensione, se è noto, sono rappresentati in formato decimale. Se non vengano specificata un limite inferiore o dimensioni, viene omesso. Se vengono omessi il limite inferiore e le dimensioni per una determinata dimensione, il `:` viene omesso anche. Le matrici multidimensionali sono rappresentate da una `[]` per ogni livello.
      * Gli argomenti che hanno tipi di puntatore diverso da void vengono rappresentati usando un `*` dopo il nome del tipo. Un puntatore void viene rappresentato con un nome di tipo `System.Void`.
      * Gli argomenti che fanno riferimento a parametri di tipo generico per i tipi di vengono codificati utilizzando la `` ` `` (apice inverso) carattere seguito da indice a base zero del parametro di tipo.
      * Gli argomenti che usano parametri di tipo generico definiti nei metodi utilizzano un double-apice inverso ``` `` ``` anziché il `` ` `` utilizzate per i tipi.
      * Gli argomenti che fanno riferimento a tipi generici costruiti vengono codificati utilizzando il tipo generico, seguito da `{`, seguito da un elenco delimitato da virgole degli argomenti di tipo, seguito da `}`.

### <a name="id-string-examples"></a>Esempi di stringhe ID

Gli esempi seguenti mostrano un frammento di codice c#, con una stringa ID prodotto da ogni elemento di origine in grado di avere un commento della documentazione:

*  I tipi vengono rappresentati usando il relativo nome completo, ampliato con le informazioni generiche:

   ```csharp
   enum Color { Red, Blue, Green }

   namespace Acme
   {
       interface IProcess {...}

       struct ValueType {...}

       class Widget: IProcess
       {
           public class NestedClass {...}
           public interface IMenuItem {...}
           public delegate void Del(int i);
           public enum Direction { North, South, East, West }
       }

       class MyList<T>
       {
           class Helper<U,V> {...}
       }
   }

   "T:Color"
   "T:Acme.IProcess"
   "T:Acme.ValueType"
   "T:Acme.Widget"
   "T:Acme.Widget.NestedClass"
   "T:Acme.Widget.IMenuItem"
   "T:Acme.Widget.Del"
   "T:Acme.Widget.Direction"
   "T:Acme.MyList`1"
   "T:Acme.MyList`1.Helper`2"
   ```

*  I campi sono rappresentati da un nome completo:

   ```csharp
   namespace Acme
   {
       struct ValueType
       {
           private int total;
       }
   
       class Widget: IProcess
       {
           public class NestedClass
           {
               private int value;
           }
   
           private string message;
           private static Color defaultColor;
           private const double PI = 3.14159;
           protected readonly double monthlyAverage;
           private long[] array1;
           private Widget[,] array2;
           private unsafe int *pCount;
           private unsafe float **ppValues;
       }
   }

   "F:Acme.ValueType.total"
   "F:Acme.Widget.NestedClass.value"
   "F:Acme.Widget.message"
   "F:Acme.Widget.defaultColor"
   "F:Acme.Widget.PI"
   "F:Acme.Widget.monthlyAverage"
   "F:Acme.Widget.array1"
   "F:Acme.Widget.array2"
   "F:Acme.Widget.pCount"
   "F:Acme.Widget.ppValues"
   ```

*  Costruttori.

   ```csharp
   namespace Acme
   {
       class Widget: IProcess
       {
           static Widget() {...}
           public Widget() {...}
           public Widget(string s) {...}
       }
   }

   "M:Acme.Widget.#cctor"
   "M:Acme.Widget.#ctor"
   "M:Acme.Widget.#ctor(System.String)"
   ```

*  Distruttori.

   ```csharp
   namespace Acme
   {
       class Widget: IProcess
       {
           ~Widget() {...}
       }
   }
   
   "M:Acme.Widget.Finalize"
   ```

*  Metodi.

   ```csharp
   namespace Acme
   {
       struct ValueType
       {
           public void M(int i) {...}
       }

       class Widget: IProcess
       {
           public class NestedClass
           {
               public void M(int i) {...}
           }

           public static void M0() {...}
           public void M1(char c, out float f, ref ValueType v) {...}
           public void M2(short[] x1, int[,] x2, long[][] x3) {...}
           public void M3(long[][] x3, Widget[][,,] x4) {...}
           public unsafe void M4(char *pc, Color **pf) {...}
           public unsafe void M5(void *pv, double *[][,] pd) {...}
           public void M6(int i, params object[] args) {...}
       }

       class MyList<T>
       {
           public void Test(T t) { }
       }

       class UseList
       {
           public void Process(MyList<int> list) { }
           public MyList<T> GetValues<T>(T inputValue) { return null; }
       }
   }

   "M:Acme.ValueType.M(System.Int32)"
   "M:Acme.Widget.NestedClass.M(System.Int32)"
   "M:Acme.Widget.M0"
   "M:Acme.Widget.M1(System.Char,System.Single@,Acme.ValueType@)"
   "M:Acme.Widget.M2(System.Int16[],System.Int32[0:,0:],System.Int64[][])"
   "M:Acme.Widget.M3(System.Int64[][],Acme.Widget[0:,0:,0:][])"
   "M:Acme.Widget.M4(System.Char*,Color**)"
   "M:Acme.Widget.M5(System.Void*,System.Double*[0:,0:][])"
   "M:Acme.Widget.M6(System.Int32,System.Object[])"
   "M:Acme.MyList`1.Test(`0)"
   "M:Acme.UseList.Process(Acme.MyList{System.Int32})"
   "M:Acme.UseList.GetValues``(``0)"
   ```

*  Le proprietà e indicizzatori.

   ```csharp
   namespace Acme
   {
       class Widget: IProcess
       {
           public int Width { get {...} set {...} }
           public int this[int i] { get {...} set {...} }
           public int this[string s, int i] { get {...} set {...} }
       }
   }

   "P:Acme.Widget.Width"
   "P:Acme.Widget.Item(System.Int32)"
   "P:Acme.Widget.Item(System.String,System.Int32)"
   ```

*  eventi.

   ```csharp
   namespace Acme
   {
       class Widget: IProcess
       {
           public event Del AnEvent;
       }
   }

   "E:Acme.Widget.AnEvent"
   ```

*  Operatori unari.

   ```csharp
   namespace Acme
   {
       class Widget: IProcess
       {
           public static Widget operator+(Widget x) {...}
       }
   }

   "M:Acme.Widget.op_UnaryPlus(Acme.Widget)"
   ```

   Il set completo di nomi di funzione di operatore unario utilizzato è il seguente: `op_UnaryPlus`, `op_UnaryNegation`, `op_LogicalNot`, `op_OnesComplement`, `op_Increment`, `op_Decrement`, `op_True`, e `op_False`.

*  Operatori binari.

   ```csharp
   namespace Acme
   {
       class Widget: IProcess
       {
           public static Widget operator+(Widget x1, Widget x2) {...}
       }
   }

   "M:Acme.Widget.op_Addition(Acme.Widget,Acme.Widget)"
   ```

   Il set completo di nomi di funzione di operatore binario usato è il seguente: `op_Addition`, `op_Subtraction`, `op_Multiply`, `op_Division`, `op_Modulus`, `op_BitwiseAnd`, `op_BitwiseOr`, `op_ExclusiveOr`, `op_LeftShift`, `op_RightShift`, `op_Equality`, `op_Inequality`, `op_LessThan`, `op_LessThanOrEqual`, `op_GreaterThan`, e `op_GreaterThanOrEqual`.

*  Gli operatori di conversione hanno una parentesi finale "`~`" seguito dal tipo restituito.

   ```csharp
   namespace Acme
   {
       class Widget: IProcess
       {
           public static explicit operator int(Widget x) {...}
           public static implicit operator long(Widget x) {...}
       }
   }

   "M:Acme.Widget.op_Explicit(Acme.Widget)~System.Int32"
   "M:Acme.Widget.op_Implicit(Acme.Widget)~System.Int64"
   ```

## <a name="an-example"></a>un esempio

### <a name="c-source-code"></a>Il codice sorgente c#

L'esempio seguente mostra il codice sorgente di un `Point` classe:

```csharp
namespace Graphics
{

/// <summary>Class <c>Point</c> models a point in a two-dimensional plane.
/// </summary>
public class Point 
{

    /// <summary>Instance variable <c>x</c> represents the point's
    ///    x-coordinate.</summary>
    private int x;

    /// <summary>Instance variable <c>y</c> represents the point's
    ///    y-coordinate.</summary>
    private int y;

    /// <value>Property <c>X</c> represents the point's x-coordinate.</value>
    public int X
    {
        get { return x; }
        set { x = value; }
    }

    /// <value>Property <c>Y</c> represents the point's y-coordinate.</value>
    public int Y
    {
        get { return y; }
        set { y = value; }
    }

    /// <summary>This constructor initializes the new Point to
    ///    (0,0).</summary>
    public Point() : this(0,0) {}

    /// <summary>This constructor initializes the new Point to
    ///    (<paramref name="xor"/>,<paramref name="yor"/>).</summary>
    /// <param><c>xor</c> is the new Point's x-coordinate.</param>
    /// <param><c>yor</c> is the new Point's y-coordinate.</param>
    public Point(int xor, int yor) {
        X = xor;
        Y = yor;
    }

    /// <summary>This method changes the point's location to
    ///    the given coordinates.</summary>
    /// <param><c>xor</c> is the new x-coordinate.</param>
    /// <param><c>yor</c> is the new y-coordinate.</param>
    /// <see cref="Translate"/>
    public void Move(int xor, int yor) {
        X = xor;
        Y = yor;
    }

    /// <summary>This method changes the point's location by
    ///    the given x- and y-offsets.
    /// <example>For example:
    /// <code>
    ///    Point p = new Point(3,5);
    ///    p.Translate(-1,3);
    /// </code>
    /// results in <c>p</c>'s having the value (2,8).
    /// </example>
    /// </summary>
    /// <param><c>xor</c> is the relative x-offset.</param>
    /// <param><c>yor</c> is the relative y-offset.</param>
    /// <see cref="Move"/>
    public void Translate(int xor, int yor) {
        X += xor;
        Y += yor;
    }

    /// <summary>This method determines whether two Points have the same
    ///    location.</summary>
    /// <param><c>o</c> is the object to be compared to the current object.
    /// </param>
    /// <returns>True if the Points have the same location and they have
    ///    the exact same type; otherwise, false.</returns>
    /// <seealso cref="operator=="/>
    /// <seealso cref="operator!="/>
    public override bool Equals(object o) {
        if (o == null) {
            return false;
        }

        if (this == o) {
            return true;
        }

        if (GetType() == o.GetType()) {
            Point p = (Point)o;
            return (X == p.X) && (Y == p.Y);
        }
        return false;
    }

    /// <summary>Report a point's location as a string.</summary>
    /// <returns>A string representing a point's location, in the form (x,y),
    ///    without any leading, training, or embedded whitespace.</returns>
    public override string ToString() {
        return "(" + X + "," + Y + ")";
    }

    /// <summary>This operator determines whether two Points have the same
    ///    location.</summary>
    /// <param><c>p1</c> is the first Point to be compared.</param>
    /// <param><c>p2</c> is the second Point to be compared.</param>
    /// <returns>True if the Points have the same location and they have
    ///    the exact same type; otherwise, false.</returns>
    /// <seealso cref="Equals"/>
    /// <seealso cref="operator!="/>
    public static bool operator==(Point p1, Point p2) {
        if ((object)p1 == null || (object)p2 == null) {
            return false;
        }

        if (p1.GetType() == p2.GetType()) {
            return (p1.X == p2.X) && (p1.Y == p2.Y);
        }

        return false;
    }

    /// <summary>This operator determines whether two Points have the same
    ///    location.</summary>
    /// <param><c>p1</c> is the first Point to be compared.</param>
    /// <param><c>p2</c> is the second Point to be compared.</param>
    /// <returns>True if the Points do not have the same location and the
    ///    exact same type; otherwise, false.</returns>
    /// <seealso cref="Equals"/>
    /// <seealso cref="operator=="/>
    public static bool operator!=(Point p1, Point p2) {
        return !(p1 == p2);
    }

    /// <summary>This is the entry point of the Point class testing
    /// program.
    /// <para>This program tests each method and operator, and
    /// is intended to be run after any non-trivial maintenance has
    /// been performed on the Point class.</para></summary>
    public static void Main() {
        // class test code goes here
    }
}
}
```

### <a name="resulting-xml"></a>Codice XML risultante

Di seguito è riportato l'output prodotto dal generatore di documentazione uno quando viene specificato il codice sorgente per la classe `Point`, come illustrato in precedenza:

```xml
<?xml version="1.0"?>
<doc>
    <assembly>
        <name>Point</name>
    </assembly>
    <members>
        <member name="T:Graphics.Point">
            <summary>Class <c>Point</c> models a point in a two-dimensional
            plane.
            </summary>
        </member>

        <member name="F:Graphics.Point.x">
            <summary>Instance variable <c>x</c> represents the point's
            x-coordinate.</summary>
        </member>

        <member name="F:Graphics.Point.y">
            <summary>Instance variable <c>y</c> represents the point's
            y-coordinate.</summary>
        </member>

        <member name="M:Graphics.Point.#ctor">
            <summary>This constructor initializes the new Point to
        (0,0).</summary>
        </member>

        <member name="M:Graphics.Point.#ctor(System.Int32,System.Int32)">
            <summary>This constructor initializes the new Point to
            (<paramref name="xor"/>,<paramref name="yor"/>).</summary>
            <param><c>xor</c> is the new Point's x-coordinate.</param>
            <param><c>yor</c> is the new Point's y-coordinate.</param>
        </member>

        <member name="M:Graphics.Point.Move(System.Int32,System.Int32)">
            <summary>This method changes the point's location to
            the given coordinates.</summary>
            <param><c>xor</c> is the new x-coordinate.</param>
            <param><c>yor</c> is the new y-coordinate.</param>
            <see cref="M:Graphics.Point.Translate(System.Int32,System.Int32)"/>
        </member>

        <member
            name="M:Graphics.Point.Translate(System.Int32,System.Int32)">
            <summary>This method changes the point's location by
            the given x- and y-offsets.
            <example>For example:
            <code>
            Point p = new Point(3,5);
            p.Translate(-1,3);
            </code>
            results in <c>p</c>'s having the value (2,8).
            </example>
            </summary>
            <param><c>xor</c> is the relative x-offset.</param>
            <param><c>yor</c> is the relative y-offset.</param>
            <see cref="M:Graphics.Point.Move(System.Int32,System.Int32)"/>
        </member>

        <member name="M:Graphics.Point.Equals(System.Object)">
            <summary>This method determines whether two Points have the same
            location.</summary>
            <param><c>o</c> is the object to be compared to the current
            object.
            </param>
            <returns>True if the Points have the same location and they have
            the exact same type; otherwise, false.</returns>
            <seealso
      cref="M:Graphics.Point.op_Equality(Graphics.Point,Graphics.Point)"/>
            <seealso
      cref="M:Graphics.Point.op_Inequality(Graphics.Point,Graphics.Point)"/>
        </member>

        <member name="M:Graphics.Point.ToString">
            <summary>Report a point's location as a string.</summary>
            <returns>A string representing a point's location, in the form
            (x,y),
            without any leading, training, or embedded whitespace.</returns>
        </member>

        <member
       name="M:Graphics.Point.op_Equality(Graphics.Point,Graphics.Point)">
            <summary>This operator determines whether two Points have the
            same
            location.</summary>
            <param><c>p1</c> is the first Point to be compared.</param>
            <param><c>p2</c> is the second Point to be compared.</param>
            <returns>True if the Points have the same location and they have
            the exact same type; otherwise, false.</returns>
            <seealso cref="M:Graphics.Point.Equals(System.Object)"/>
            <seealso
     cref="M:Graphics.Point.op_Inequality(Graphics.Point,Graphics.Point)"/>
        </member>

        <member
      name="M:Graphics.Point.op_Inequality(Graphics.Point,Graphics.Point)">
            <summary>This operator determines whether two Points have the
            same
            location.</summary>
            <param><c>p1</c> is the first Point to be compared.</param>
            <param><c>p2</c> is the second Point to be compared.</param>
            <returns>True if the Points do not have the same location and
            the
            exact same type; otherwise, false.</returns>
            <seealso cref="M:Graphics.Point.Equals(System.Object)"/>
            <seealso
      cref="M:Graphics.Point.op_Equality(Graphics.Point,Graphics.Point)"/>
        </member>

        <member name="M:Graphics.Point.Main">
            <summary>This is the entry point of the Point class testing
            program.
            <para>This program tests each method and operator, and
            is intended to be run after any non-trivial maintenance has
            been performed on the Point class.</para></summary>
        </member>

        <member name="P:Graphics.Point.X">
            <value>Property <c>X</c> represents the point's
            x-coordinate.</value>
        </member>

        <member name="P:Graphics.Point.Y">
            <value>Property <c>Y</c> represents the point's
            y-coordinate.</value>
        </member>
    </members>
</doc>
```
