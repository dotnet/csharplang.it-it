---
ms.openlocfilehash: 2026fc1bf9d3576b967cbc2e9a670aa44b7eab3a
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71704014"
---
# <a name="documentation-comments"></a>Commenti sulla documentazione

C#fornisce un meccanismo per i programmatori per documentare il codice utilizzando una speciale sintassi di commento che contiene testo XML. Nei file di codice sorgente, i commenti con un determinato modulo possono essere usati per indirizzare uno strumento per produrre codice XML da tali commenti e dagli elementi del codice sorgente, che precedono. I commenti che usano tale sintassi sono detti ***commenti alla documentazione***. Devono precedere immediatamente un tipo definito dall'utente (ad esempio una classe, un delegato o un'interfaccia) o un membro (ad esempio un campo, un evento, una proprietà o un metodo). Lo strumento di generazione XML è denominato ***Generatore di documentazione***. Questo generatore può essere, ma non deve essere, il C# compilatore stesso. L'output prodotto dal generatore di documentazione è denominato ***file di documentazione***. Un file di documentazione viene utilizzato come input per un ***Visualizzatore di documentazione***. strumento progettato per produrre una sorta di visualizzazione visiva delle informazioni sul tipo e la relativa documentazione associata.

Questa specifica suggerisce un set di tag da usare nei commenti della documentazione, ma l'uso di questi tag non è obbligatorio e altri tag possono essere usati, se necessario, a condizione che vengano seguite le regole del codice XML ben formato.

## <a name="introduction"></a>Introduzione

I commenti con un modulo speciale possono essere usati per indirizzare uno strumento per produrre codice XML da tali commenti e dagli elementi del codice sorgente, che precedono. Tali commenti sono commenti a riga singola che iniziano con tre barre (`///`) o commenti delimitati che iniziano con una barra e due stelle (`/**`). Devono precedere immediatamente un tipo definito dall'utente (ad esempio una classe, un delegato o un'interfaccia) o un membro (ad esempio un campo, un evento, una proprietà o un metodo) che annotano. Le sezioni degli attributi ([specifica dell'attributo](attributes.md#attribute-specification)) sono considerate parte di dichiarazioni, quindi i commenti della documentazione devono precedere gli attributi applicati a un tipo o a un membro.

__Sintassi__

```antlr
single_line_doc_comment
    : '///' input_character*
    ;

delimited_doc_comment
    : '/**' delimited_comment_section* asterisk+ '/'
    ;
```

In una *single_line_doc_comment*, se è presente un carattere di *spazio vuoto* che segue i `///` caratteri in ogni *single_line_doc_comment*adiacente al *single_line_doc_comment*corrente, lo *spazio vuoto* il carattere non è incluso nell'output XML.

In un commento-doc delimitato, se il primo carattere diverso da uno spazio nella seconda riga è un asterisco e lo stesso modello di spazi vuoti facoltativi e un asterisco vengono ripetuti all'inizio di ogni riga nel commento delimitato-doc- i caratteri del criterio ripetuto non sono quindi inclusi nell'output XML. Il modello può includere spazi vuoti dopo, così come prima, il carattere asterisco.

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

Il testo contenuto nei commenti della documentazione deve essere ben formato in base alle regole di https://www.w3.org/TR/REC-xml) XML (. Se il codice XML non è in formato corretto, viene generato un avviso e il file di documentazione conterrà un commento che informa che si è verificato un errore.

Anche se gli sviluppatori sono liberi di creare il proprio set di tag, è possibile definire un set consigliato in [Tag consigliati](documentation-comments.md#recommended-tags). Alcuni tag consigliati hanno un significato speciale:

*  Il `<param>` tag viene usato per descrivere i parametri. Se viene usato un tag di questo tipo, il generatore di documentazione deve verificare che il parametro specificato esista e che tutti i parametri siano descritti nei commenti della documentazione. Se la verifica ha esito negativo, il generatore di documentazione genera un avviso.
*  L'attributo `cref` può essere associato a qualsiasi tag per fornire un riferimento a un elemento del codice. Il generatore di documentazione deve verificare che questo elemento di codice esista. Se la verifica ha esito negativo, il generatore di documentazione genera un avviso. Quando si cerca un nome descritto in un `cref` attributo, il generatore di documentazione deve rispettare la visibilità dello `using` spazio dei nomi in base alle istruzioni visualizzate all'interno del codice sorgente. Per gli elementi di codice generici, non è possibile usare la normale sintassi generica (ovvero "`List<T>`") perché produce codice XML non valido. È possibile utilizzare le parentesi graffe anziché le parentesi quadre (ovvero "`List{T}`") oppure la sintassi di escape XML (ovvero`List&lt;T&gt;`"").
*  Il `<summary>` tag deve essere utilizzato da un visualizzatore di documentazione per visualizzare informazioni aggiuntive su un tipo o un membro.
*  Il `<include>` tag include informazioni provenienti da un file XML esterno.

Si noti che il file di documentazione non fornisce informazioni complete sul tipo e sui membri (ad esempio, non contiene informazioni sul tipo). Per ottenere tali informazioni su un tipo o un membro, è necessario utilizzare il file di documentazione insieme alla reflection sul tipo o sul membro effettivo.

## <a name="recommended-tags"></a>Tag consigliati

Il generatore di documentazione deve accettare ed elaborare qualsiasi tag valido in base alle regole di XML. I seguenti tag forniscono la funzionalità comunemente utilizzata nella documentazione dell'utente. Naturalmente, sono possibili altri tag.


| __Tag__          | __Sezione__                                            | __Scopo__                                            |
|------------------|--------------------------------------------------------|--------------------------------------------------------|
| `<c>`            | [`<c>`](documentation-comments.md#c)                   | Impostare il testo in un tipo di carattere simile a codice                           | 
| `<code>`         | [`<code>`](documentation-comments.md#code)             | Impostare una o più righe di codice sorgente o output del programma |
| `<example>`      | [`<example>`](documentation-comments.md#example)       | Indicare un esempio                                    |
| `<exception>`    | [`<exception>`](documentation-comments.md#exception)   | Identifica le eccezioni che possono essere generate da un metodo           |
| `<include>`      | [`<include>`](documentation-comments.md#include)       | Include XML da un file esterno                     |
| `<list>`         | [`<list>`](documentation-comments.md#list)             | Creare un elenco o una tabella                                 |
| `<para>`         | [`<para>`](documentation-comments.md#para)             | Consenti aggiunta struttura al testo                   |
| `<param>`        | [`<param>`](documentation-comments.md#param)           | Descrivere un parametro per un metodo o un costruttore       |
| `<paramref>`     | [`<paramref>`](documentation-comments.md#paramref)     | Verificare che una parola sia un nome di parametro               |
| `<permission>`   | [`<permission>`](documentation-comments.md#permission) | Documentare l'accessibilità di sicurezza di un membro        |
| `<remarks>`      | [`<remarks>`](documentation-comments.md#remarks)       | Descrivere informazioni aggiuntive su un tipo           |
| `<returns>`      | [`<returns>`](documentation-comments.md#returns)       | Descrivere il valore restituito di un metodo                  |
| `<see>`          | [`<see>`](documentation-comments.md#see)               | Specificare un collegamento                                         |
| `<seealso>`      | [`<seealso>`](documentation-comments.md#seealso)       | Genera una voce See also                              |
| `<summary>`      | [`<summary>`](documentation-comments.md#summary)       | Descrivere un tipo o un membro di un tipo                  |
| `<value>`        | [`<value>`](documentation-comments.md#value)           | Descrivere una proprietà                                    |
| `<typeparam>`    |                                                        | Descrivere un parametro di tipo generico                      |
| `<typeparamref>` |                                                        | Verificare che una parola sia un nome di parametro di tipo          |

### `<c>`

Questo tag fornisce un meccanismo per indicare che un frammento di testo all'interno di una descrizione deve essere impostato in un tipo di carattere speciale, ad esempio quello usato per un blocco di codice. Per le righe di codice effettivo, `<code>` utilizzare[`<code>`](documentation-comments.md#code)().

__Sintassi__

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

Questo tag viene usato per impostare una o più righe di codice sorgente o output del programma in un tipo di carattere speciale. Per i frammenti di codice di dimensioni ridotte `<c>` nella[`<c>`](documentation-comments.md#c)descrizione, utilizzare ().

__Sintassi__

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

Questo tag consente il codice di esempio all'interno di un commento, per specificare come può essere usato un metodo o un altro membro della libreria. In genere, questa operazione comporta anche l'uso del tag `<code>` ([`<code>`](documentation-comments.md#code)).

__Sintassi__

```xml
<example>description</example>
```

__Esempio:__

Per `<code>` un[`<code>`](documentation-comments.md#code)esempio, vedere ().

### `<exception>`

Questo tag fornisce un modo per documentare le eccezioni che possono essere generate da un metodo.

__Sintassi__

```xml
<exception cref="member">description</exception>
```

dove

* `member`nome di un membro. Il generatore di documentazione verifica che il membro specificato esista e `member` si traduce nel nome canonico dell'elemento nel file di documentazione.
* `description`Descrizione delle circostanze in cui viene generata l'eccezione.

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

Questo tag consente di includere informazioni da un documento XML esterno al file di codice sorgente. Il file esterno deve essere un documento XML ben formato e un'espressione XPath viene applicata a tale documento per specificare il codice XML del documento da includere. Il `<include>` tag viene quindi sostituito con il codice XML selezionato dal documento esterno.

__Sintassi__

```xml
<include file="filename" path="xpath" />
```

dove

* `filename`nome file di un file XML esterno. Il nome del file viene interpretato in relazione al file che contiene il tag di inclusione.
* `xpath`espressione XPath che consente di selezionare parte del codice XML nel file XML esterno.

__Esempio:__

Se il codice sorgente contiene una dichiarazione come:

```csharp
/// <include file="docs.xml" path='extradoc/class[@name="IntList"]/*' />
public class IntList { ... }
```

e il file esterno "docs. xml" presenta il contenuto seguente:

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

la stessa documentazione viene quindi restituita come se il codice sorgente contenesse:

```csharp
/// <summary>
///    Contains a list of integers.
/// </summary>
public class IntList { ... }
```

### `<list>`

Questo tag viene usato per creare un elenco o una tabella di elementi. Può contenere un `<listheader>` blocco per definire la riga di intestazione di un elenco di tabelle o definizioni. Quando si definisce una tabella, è necessario fornire solo `term` una voce per nell'intestazione.

Ogni elemento nell'elenco viene specificato con un `<item>` blocco. Quando si crea un elenco di definizioni `term` , `description` è necessario specificare sia che. Tuttavia, per una tabella, un elenco puntato o un elenco numerato, `description` è necessario specificare solo.

__Sintassi__

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

* `term`termine da definire, la cui definizione è in `description`.
* `description`è un elemento in un elenco puntato o numerato o la definizione di un `term`oggetto.

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

Questo tag è da usare all'interno di altri tag, `<summary>` ad[`<remarks>`](documentation-comments.md#remarks)esempio ( `<returns>` )[`<returns>`](documentation-comments.md#returns)o (), e consente di aggiungere la struttura al testo.

__Sintassi__

```xml
<para>content</para>
```

dove `content` è il testo del paragrafo.

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

__Sintassi__

```xml
<param name="name">description</param>
```

dove

* `name`nome del parametro.
* `description`Descrizione del parametro.

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

Questo tag viene usato per indicare che una parola è un parametro. Il file di documentazione può essere elaborato per formattare questo parametro in modo distinto.

__Sintassi__

```xml
<paramref name="name"/>
```

dove `name` è il nome del parametro.

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

Questo tag consente di documentare l'accessibilità di sicurezza di un membro.

__Sintassi__

```xml
<permission cref="member">description</permission>
```

dove

* `member`nome di un membro. Il generatore di documentazione verifica che l'elemento di codice specificato esista e converte il *membro* nel nome canonico dell'elemento nel file di documentazione.
* `description`Descrizione dell'accesso al membro.

__Esempio:__

```csharp
/// <permission cref="System.Security.PermissionSet">Everyone can
/// access this method.</permission>

public static void Test() {
    // ...
}
```

### `<remarks>`

Questo tag viene usato per specificare informazioni aggiuntive su un tipo. (Usare `<summary>` ([`<summary>`](documentation-comments.md#summary)) per descrivere il tipo stesso e i membri di un tipo.

__Sintassi__

```xml
<remarks>description</remarks>
```

dove `description` è il testo del contrassegno.

__Esempio:__

```csharp
/// <summary>Class <c>Point</c> models a point in a 
/// two-dimensional plane.</summary>
/// <remarks>Uses polar coordinates</remarks>
public class Point 
{
    // ...
}
```

### `<returns>`

Questo tag viene usato per descrivere il valore restituito di un metodo.

__Sintassi__

```xml
<returns>description</returns>
```

dove `description` è una descrizione del valore restituito.

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

Questo tag consente di specificare un collegamento all'interno del testo. Usare `<seealso>` [(`<seealso>`](documentation-comments.md#seealso)) per indicare il testo da visualizzare in una sezione vedere anche.

__Sintassi__

```xml
<see cref="member"/>
```

dove `member` è il nome di un membro. Il generatore di documentazione verifica che l'elemento di codice specificato esista e modifichi il *membro* nel nome dell'elemento nel file di documentazione generato.

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

Questo tag consente la generazione di una voce per la sezione vedere anche. Usare `<see>` [(`<see>`](documentation-comments.md#see)) per specificare un collegamento dall'interno del testo.

__Sintassi__

```xml
<seealso cref="member"/>
```

dove `member` è il nome di un membro. Il generatore di documentazione verifica che l'elemento di codice specificato esista e modifichi il *membro* nel nome dell'elemento nel file di documentazione generato.

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

Questo tag può essere usato per descrivere un tipo o un membro di un tipo. Usare `<remarks>` [(`<remarks>`](documentation-comments.md#remarks)) per descrivere il tipo stesso.

__Sintassi__

```xml
<summary>description</summary>
```

dove `description` è un riepilogo del tipo o del membro.

__Esempio:__

```csharp
/// <summary>This constructor initializes the new Point to (0,0).</summary>
public Point() : this(0,0) {
}
```

### `<value>`

Questo tag consente la descrizione di una proprietà.

__Sintassi__

```xml
<value>property description</value>
```

dove `property description` è una descrizione per la proprietà.

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

Questo tag viene usato per descrivere un parametro di tipo generico per una classe, uno struct, un'interfaccia, un delegato o un metodo.

__Sintassi__

```xml
<typeparam name="name">description</typeparam>
```

dove `name` è il nome del parametro di tipo e `description` è la relativa descrizione.

__Esempio:__

```csharp
/// <summary>A generic list class.</summary>
/// <typeparam name="T">The type stored by the list.</typeparam>
public class MyList<T> {
    ...
}
```

### `<typeparamref>`

Questo tag viene usato per indicare che una parola è un parametro di tipo. Il file di documentazione può essere elaborato per formattare questo parametro di tipo in modo distinto.

__Sintassi__

```xml
<typeparamref name="name"/>
```

dove `name` è il nome del parametro di tipo.

__Esempio:__

```csharp
/// <summary>This method fetches data and returns a list of <typeparamref name="T"/>.</summary>
/// <param name="query">query to execute</param>
public List<T> FetchData<T>(string query) {
    ...
}
```

## <a name="processing-the-documentation-file"></a>Elaborazione del file di documentazione

Il generatore di documentazione genera una stringa ID per ogni elemento nel codice sorgente contrassegnato con un commento della documentazione. Questa stringa ID identifica in modo univoco un elemento di origine. Un visualizzatore di documentazione può utilizzare una stringa ID per identificare l'elemento di reflection o metadati corrispondente a cui si applica la documentazione.

Il file di documentazione non è una rappresentazione gerarchica del codice sorgente. si tratta invece di un elenco semplice con una stringa ID generata per ogni elemento.

### <a name="id-string-format"></a>Formato stringa ID

Quando genera le stringhe ID, il generatore di documentazione osserva le regole seguenti:

*  Assenza di spazi vuoti nella stringa.

*  La prima parte della stringa identifica il tipo di membro che viene documentato, tramite un singolo carattere seguito da due punti. Sono definiti i seguenti tipi di membri:

   | __Carattere__ | __Descrizione__                                             |
   |---------------|-------------------------------------------------------------|
   | E             | event                                                       |
   | F             | Campo                                                       |
   | M             | Metodo (inclusi costruttori, distruttori e operatori) |
   | N             | Spazio dei nomi                                                   |
   | P             | Proprietà (inclusi gli indicizzatori)                               |
   | T             | Tipo (ad esempio classe, delegato, enumerazione, interfaccia e struct) |
   | !             | Stringa di errore; il resto della stringa fornisce informazioni sull'errore. Ad esempio, il generatore di documentazione genera informazioni sugli errori per i collegamenti che non possono essere risolti. |

*  La seconda parte della stringa è il nome completo dell'elemento, a partire dalla radice dello spazio dei nomi. Il nome dell'elemento, i tipi di inclusione e lo spazio dei nomi sono separati da punti. Se il nome dell'elemento ha punti, questi vengono sostituiti da `#(U+0023)` caratteri. Si presuppone che nessun elemento abbia questo carattere nel nome.
*  Per i metodi e le proprietà con argomenti, l'elenco di argomenti segue, racchiuso tra parentesi. Per gli argomenti senza argomenti, le parentesi vengono omesse. Gli argomenti sono separati da virgole. La codifica di ogni argomento è identica alla firma dell'interfaccia della riga di comando, come indicato di seguito:
   *  Gli argomenti sono rappresentati dal nome della documentazione, che è basato sul nome completo, modificato come segue:
      * Gli argomenti che rappresentano tipi generici hanno un `` ` `` carattere (apice inverso) accodato seguito dal numero di parametri di tipo
      * Gli argomenti con `out` il `ref` modificatore o `@` hanno un nome di tipo successivo. Gli argomenti passati per valore o `params` via non hanno una notazione speciale.
      * Gli argomenti che sono matrici sono rappresentati `[lowerbound:size, ... , lowerbound:size]` come dove il numero di virgole è il rango minore di uno e i limiti inferiori e le dimensioni di ogni dimensione, se noti, sono rappresentati in decimali. Se non si specifica una dimensione o un limite inferiore, viene omesso. Se il limite inferiore e le dimensioni di una determinata dimensione vengono omessi, `:` viene omesso anche. Le matrici di matrici sono rappresentate da `[]` una per ogni livello.
      * Gli argomenti con tipi di puntatore diversi da void sono rappresentati `*` utilizzando un oggetto che segue il nome del tipo. Un puntatore void viene rappresentato usando un nome di `System.Void`tipo.
      * Gli argomenti che fanno riferimento a parametri di tipo generico definiti sui tipi vengono codificati `` ` `` utilizzando il carattere (apice inverso) seguito dall'indice in base zero del parametro di tipo.
      * Gli argomenti che usano parametri di tipo generico definiti nei metodi usano un doppio apice ``` `` ``` anziché l'oggetto `` ` `` usato per i tipi.
      * Gli argomenti che fanno riferimento a tipi generici costruiti vengono codificati usando il tipo generico, `{`seguito da, seguito da un elenco delimitato da virgole di argomenti `}`di tipo, seguito da.

### <a name="id-string-examples"></a>Esempi di stringa ID

Gli esempi seguenti mostrano un frammento di C# codice, insieme alla stringa ID prodotta da ogni elemento di origine in grado di presentare un commento sulla documentazione:

*  I tipi sono rappresentati usando il nome completo, arricchito con informazioni generiche:

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

*  I campi sono rappresentati dal nome completo:

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

*  Proprietà e indicizzatori.

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

*  Eventi.

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

   Il set completo di nomi di funzione di operatore unario utilizzato è `op_UnaryPlus`il `op_UnaryNegation`seguente `op_LogicalNot`:, `op_Increment`, `op_Decrement`, `op_True` `op_OnesComplement`,, `op_False`, e.

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

   Il set completo di nomi di funzione di operatore binario utilizzato è il `op_Addition`seguente `op_Subtraction`: `op_Multiply`, `op_Division`, `op_Modulus`, `op_BitwiseAnd`, `op_BitwiseOr`, `op_ExclusiveOr`, `op_LeftShift`, `op_RightShift`,,, `op_Equality`, ,,`op_LessThan`, e`op_GreaterThan`. `op_LessThanOrEqual` `op_Inequality` `op_GreaterThanOrEqual`

*  Gli operatori di conversione hanno un "`~`" finale seguito dal tipo restituito.

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

### <a name="c-source-code"></a>C#codice sorgente

Nell'esempio seguente viene illustrato il codice sorgente di `Point` una classe:

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

### <a name="resulting-xml"></a>XML risultante

Di seguito è riportato l'output prodotto da un generatore di documentazione quando viene fornito il `Point`codice sorgente per la classe, illustrato in precedenza:

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
