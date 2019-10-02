---
ms.openlocfilehash: 2026fc1bf9d3576b967cbc2e9a670aa44b7eab3a
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71704014"
---
# <a name="documentation-comments"></a><span data-ttu-id="c3986-101">Commenti sulla documentazione</span><span class="sxs-lookup"><span data-stu-id="c3986-101">Documentation comments</span></span>

<span data-ttu-id="c3986-102">C#fornisce un meccanismo per i programmatori per documentare il codice utilizzando una speciale sintassi di commento che contiene testo XML.</span><span class="sxs-lookup"><span data-stu-id="c3986-102">C# provides a mechanism for programmers to document their code using a special comment syntax that contains XML text.</span></span> <span data-ttu-id="c3986-103">Nei file di codice sorgente, i commenti con un determinato modulo possono essere usati per indirizzare uno strumento per produrre codice XML da tali commenti e dagli elementi del codice sorgente, che precedono.</span><span class="sxs-lookup"><span data-stu-id="c3986-103">In source code files, comments having a certain form can be used to direct a tool to produce XML from those comments and the source code elements, which they precede.</span></span> <span data-ttu-id="c3986-104">I commenti che usano tale sintassi sono detti ***commenti alla documentazione***.</span><span class="sxs-lookup"><span data-stu-id="c3986-104">Comments using such syntax are called ***documentation comments***.</span></span> <span data-ttu-id="c3986-105">Devono precedere immediatamente un tipo definito dall'utente (ad esempio una classe, un delegato o un'interfaccia) o un membro (ad esempio un campo, un evento, una proprietà o un metodo).</span><span class="sxs-lookup"><span data-stu-id="c3986-105">They must immediately precede a user-defined type (such as a class, delegate, or interface) or a member (such as a field, event, property, or method).</span></span> <span data-ttu-id="c3986-106">Lo strumento di generazione XML è denominato ***Generatore di documentazione***.</span><span class="sxs-lookup"><span data-stu-id="c3986-106">The XML generation tool is called the ***documentation generator***.</span></span> <span data-ttu-id="c3986-107">Questo generatore può essere, ma non deve essere, il C# compilatore stesso. L'output prodotto dal generatore di documentazione è denominato ***file di documentazione***.</span><span class="sxs-lookup"><span data-stu-id="c3986-107">(This generator could be, but need not be, the C# compiler itself.) The output produced by the documentation generator is called the ***documentation file***.</span></span> <span data-ttu-id="c3986-108">Un file di documentazione viene utilizzato come input per un ***Visualizzatore di documentazione***. strumento progettato per produrre una sorta di visualizzazione visiva delle informazioni sul tipo e la relativa documentazione associata.</span><span class="sxs-lookup"><span data-stu-id="c3986-108">A documentation file is used as input to a ***documentation viewer***; a tool intended to produce some sort of visual display of type information and its associated documentation.</span></span>

<span data-ttu-id="c3986-109">Questa specifica suggerisce un set di tag da usare nei commenti della documentazione, ma l'uso di questi tag non è obbligatorio e altri tag possono essere usati, se necessario, a condizione che vengano seguite le regole del codice XML ben formato.</span><span class="sxs-lookup"><span data-stu-id="c3986-109">This specification suggests a set of tags to be used in documentation comments, but use of these tags is not required, and other tags may be used if desired, as long the rules of well-formed XML are followed.</span></span>

## <a name="introduction"></a><span data-ttu-id="c3986-110">Introduzione</span><span class="sxs-lookup"><span data-stu-id="c3986-110">Introduction</span></span>

<span data-ttu-id="c3986-111">I commenti con un modulo speciale possono essere usati per indirizzare uno strumento per produrre codice XML da tali commenti e dagli elementi del codice sorgente, che precedono.</span><span class="sxs-lookup"><span data-stu-id="c3986-111">Comments having a special form can be used to direct a tool to produce XML from those comments and the source code elements, which they precede.</span></span> <span data-ttu-id="c3986-112">Tali commenti sono commenti a riga singola che iniziano con tre barre (`///`) o commenti delimitati che iniziano con una barra e due stelle (`/**`).</span><span class="sxs-lookup"><span data-stu-id="c3986-112">Such comments are single-line comments that start with three slashes (`///`), or delimited comments that start with a slash and two stars (`/**`).</span></span> <span data-ttu-id="c3986-113">Devono precedere immediatamente un tipo definito dall'utente (ad esempio una classe, un delegato o un'interfaccia) o un membro (ad esempio un campo, un evento, una proprietà o un metodo) che annotano.</span><span class="sxs-lookup"><span data-stu-id="c3986-113">They must immediately precede a user-defined type (such as a class, delegate, or interface) or a member (such as a field, event, property, or method) that they annotate.</span></span> <span data-ttu-id="c3986-114">Le sezioni degli attributi ([specifica dell'attributo](attributes.md#attribute-specification)) sono considerate parte di dichiarazioni, quindi i commenti della documentazione devono precedere gli attributi applicati a un tipo o a un membro.</span><span class="sxs-lookup"><span data-stu-id="c3986-114">Attribute sections ([Attribute specification](attributes.md#attribute-specification)) are considered part of declarations, so documentation comments must precede attributes applied to a type or member.</span></span>

<span data-ttu-id="c3986-115">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-115">__Syntax:__</span></span>

```antlr
single_line_doc_comment
    : '///' input_character*
    ;

delimited_doc_comment
    : '/**' delimited_comment_section* asterisk+ '/'
    ;
```

<span data-ttu-id="c3986-116">In una *single_line_doc_comment*, se è presente un carattere di *spazio vuoto* che segue i `///` caratteri in ogni *single_line_doc_comment*adiacente al *single_line_doc_comment*corrente, lo *spazio vuoto* il carattere non è incluso nell'output XML.</span><span class="sxs-lookup"><span data-stu-id="c3986-116">In a *single_line_doc_comment*, if there is a *whitespace* character following the `///` characters on each of the *single_line_doc_comment*s adjacent to the current *single_line_doc_comment*, then that *whitespace* character is not included in the XML output.</span></span>

<span data-ttu-id="c3986-117">In un commento-doc delimitato, se il primo carattere diverso da uno spazio nella seconda riga è un asterisco e lo stesso modello di spazi vuoti facoltativi e un asterisco vengono ripetuti all'inizio di ogni riga nel commento delimitato-doc- i caratteri del criterio ripetuto non sono quindi inclusi nell'output XML.</span><span class="sxs-lookup"><span data-stu-id="c3986-117">In a delimited-doc-comment, if the first non-whitespace character on the second line is an asterisk and the same pattern of optional whitespace characters and an asterisk character is repeated at the beginning of each of the line within the delimited-doc-comment, then the characters of the repeated pattern are not included in the XML output.</span></span> <span data-ttu-id="c3986-118">Il modello può includere spazi vuoti dopo, così come prima, il carattere asterisco.</span><span class="sxs-lookup"><span data-stu-id="c3986-118">The pattern may include whitespace characters after, as well as before, the asterisk character.</span></span>

<span data-ttu-id="c3986-119">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-119">__Example:__</span></span>

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

<span data-ttu-id="c3986-120">Il testo contenuto nei commenti della documentazione deve essere ben formato in base alle regole di https://www.w3.org/TR/REC-xml) XML (.</span><span class="sxs-lookup"><span data-stu-id="c3986-120">The text within documentation comments must be well formed according to the rules of XML (https://www.w3.org/TR/REC-xml).</span></span> <span data-ttu-id="c3986-121">Se il codice XML non è in formato corretto, viene generato un avviso e il file di documentazione conterrà un commento che informa che si è verificato un errore.</span><span class="sxs-lookup"><span data-stu-id="c3986-121">If the XML is ill formed, a warning is generated and the documentation file will contain a comment saying that an error was encountered.</span></span>

<span data-ttu-id="c3986-122">Anche se gli sviluppatori sono liberi di creare il proprio set di tag, è possibile definire un set consigliato in [Tag consigliati](documentation-comments.md#recommended-tags).</span><span class="sxs-lookup"><span data-stu-id="c3986-122">Although developers are free to create their own set of tags, a recommended set is defined in [Recommended tags](documentation-comments.md#recommended-tags).</span></span> <span data-ttu-id="c3986-123">Alcuni tag consigliati hanno un significato speciale:</span><span class="sxs-lookup"><span data-stu-id="c3986-123">Some of the recommended tags have special meanings:</span></span>

*  <span data-ttu-id="c3986-124">Il `<param>` tag viene usato per descrivere i parametri.</span><span class="sxs-lookup"><span data-stu-id="c3986-124">The `<param>` tag is used to describe parameters.</span></span> <span data-ttu-id="c3986-125">Se viene usato un tag di questo tipo, il generatore di documentazione deve verificare che il parametro specificato esista e che tutti i parametri siano descritti nei commenti della documentazione.</span><span class="sxs-lookup"><span data-stu-id="c3986-125">If such a tag is used, the documentation generator must verify that the specified parameter exists and that all parameters are described in documentation comments.</span></span> <span data-ttu-id="c3986-126">Se la verifica ha esito negativo, il generatore di documentazione genera un avviso.</span><span class="sxs-lookup"><span data-stu-id="c3986-126">If such verification fails, the documentation generator issues a warning.</span></span>
*  <span data-ttu-id="c3986-127">L'attributo `cref` può essere associato a qualsiasi tag per fornire un riferimento a un elemento del codice.</span><span class="sxs-lookup"><span data-stu-id="c3986-127">The `cref` attribute can be attached to any tag to provide a reference to a code element.</span></span> <span data-ttu-id="c3986-128">Il generatore di documentazione deve verificare che questo elemento di codice esista.</span><span class="sxs-lookup"><span data-stu-id="c3986-128">The documentation generator must verify that this code element exists.</span></span> <span data-ttu-id="c3986-129">Se la verifica ha esito negativo, il generatore di documentazione genera un avviso.</span><span class="sxs-lookup"><span data-stu-id="c3986-129">If the verification fails, the documentation generator issues a warning.</span></span> <span data-ttu-id="c3986-130">Quando si cerca un nome descritto in un `cref` attributo, il generatore di documentazione deve rispettare la visibilità dello `using` spazio dei nomi in base alle istruzioni visualizzate all'interno del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="c3986-130">When looking for a name described in a `cref` attribute, the documentation generator must respect namespace visibility according to `using` statements appearing within the source code.</span></span> <span data-ttu-id="c3986-131">Per gli elementi di codice generici, non è possibile usare la normale sintassi generica (ovvero "`List<T>`") perché produce codice XML non valido.</span><span class="sxs-lookup"><span data-stu-id="c3986-131">For code elements that are generic, the normal generic syntax (that is, "`List<T>`") cannot be used because it produces invalid XML.</span></span> <span data-ttu-id="c3986-132">È possibile utilizzare le parentesi graffe anziché le parentesi quadre (ovvero "`List{T}`") oppure la sintassi di escape XML (ovvero`List&lt;T&gt;`"").</span><span class="sxs-lookup"><span data-stu-id="c3986-132">Braces can be used instead of brackets (that is, "`List{T}`"), or the XML escape syntax can be used (that is, "`List&lt;T&gt;`").</span></span>
*  <span data-ttu-id="c3986-133">Il `<summary>` tag deve essere utilizzato da un visualizzatore di documentazione per visualizzare informazioni aggiuntive su un tipo o un membro.</span><span class="sxs-lookup"><span data-stu-id="c3986-133">The `<summary>` tag is intended to be used by a documentation viewer to display additional information about a type or member.</span></span>
*  <span data-ttu-id="c3986-134">Il `<include>` tag include informazioni provenienti da un file XML esterno.</span><span class="sxs-lookup"><span data-stu-id="c3986-134">The `<include>` tag includes information from an external XML file.</span></span>

<span data-ttu-id="c3986-135">Si noti che il file di documentazione non fornisce informazioni complete sul tipo e sui membri (ad esempio, non contiene informazioni sul tipo).</span><span class="sxs-lookup"><span data-stu-id="c3986-135">Note carefully that the documentation file does not provide full information about the type and members (for example, it does not contain any type information).</span></span> <span data-ttu-id="c3986-136">Per ottenere tali informazioni su un tipo o un membro, è necessario utilizzare il file di documentazione insieme alla reflection sul tipo o sul membro effettivo.</span><span class="sxs-lookup"><span data-stu-id="c3986-136">To get such information about a type or member, the documentation file must be used in conjunction with reflection on the actual type or member.</span></span>

## <a name="recommended-tags"></a><span data-ttu-id="c3986-137">Tag consigliati</span><span class="sxs-lookup"><span data-stu-id="c3986-137">Recommended tags</span></span>

<span data-ttu-id="c3986-138">Il generatore di documentazione deve accettare ed elaborare qualsiasi tag valido in base alle regole di XML.</span><span class="sxs-lookup"><span data-stu-id="c3986-138">The documentation generator must accept and process any tag that is valid according to the rules of XML.</span></span> <span data-ttu-id="c3986-139">I seguenti tag forniscono la funzionalità comunemente utilizzata nella documentazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="c3986-139">The following tags provide commonly used functionality in user documentation.</span></span> <span data-ttu-id="c3986-140">Naturalmente, sono possibili altri tag.</span><span class="sxs-lookup"><span data-stu-id="c3986-140">(Of course, other tags are possible.)</span></span>


| <span data-ttu-id="c3986-141">__Tag__</span><span class="sxs-lookup"><span data-stu-id="c3986-141">__Tag__</span></span>          | <span data-ttu-id="c3986-142">__Sezione__</span><span class="sxs-lookup"><span data-stu-id="c3986-142">__Section__</span></span>                                            | <span data-ttu-id="c3986-143">__Scopo__</span><span class="sxs-lookup"><span data-stu-id="c3986-143">__Purpose__</span></span>                                            |
|------------------|--------------------------------------------------------|--------------------------------------------------------|
| `<c>`            | [`<c>`](documentation-comments.md#c)                   | <span data-ttu-id="c3986-144">Impostare il testo in un tipo di carattere simile a codice</span><span class="sxs-lookup"><span data-stu-id="c3986-144">Set text in a code-like font</span></span>                           | 
| `<code>`         | [`<code>`](documentation-comments.md#code)             | <span data-ttu-id="c3986-145">Impostare una o più righe di codice sorgente o output del programma</span><span class="sxs-lookup"><span data-stu-id="c3986-145">Set one or more lines of source code or program output</span></span> |
| `<example>`      | [`<example>`](documentation-comments.md#example)       | <span data-ttu-id="c3986-146">Indicare un esempio</span><span class="sxs-lookup"><span data-stu-id="c3986-146">Indicate an example</span></span>                                    |
| `<exception>`    | [`<exception>`](documentation-comments.md#exception)   | <span data-ttu-id="c3986-147">Identifica le eccezioni che possono essere generate da un metodo</span><span class="sxs-lookup"><span data-stu-id="c3986-147">Identifies the exceptions a method can throw</span></span>           |
| `<include>`      | [`<include>`](documentation-comments.md#include)       | <span data-ttu-id="c3986-148">Include XML da un file esterno</span><span class="sxs-lookup"><span data-stu-id="c3986-148">Includes XML from an external file</span></span>                     |
| `<list>`         | [`<list>`](documentation-comments.md#list)             | <span data-ttu-id="c3986-149">Creare un elenco o una tabella</span><span class="sxs-lookup"><span data-stu-id="c3986-149">Create a list or table</span></span>                                 |
| `<para>`         | [`<para>`](documentation-comments.md#para)             | <span data-ttu-id="c3986-150">Consenti aggiunta struttura al testo</span><span class="sxs-lookup"><span data-stu-id="c3986-150">Permit structure to be added to text</span></span>                   |
| `<param>`        | [`<param>`](documentation-comments.md#param)           | <span data-ttu-id="c3986-151">Descrivere un parametro per un metodo o un costruttore</span><span class="sxs-lookup"><span data-stu-id="c3986-151">Describe a parameter for a method or constructor</span></span>       |
| `<paramref>`     | [`<paramref>`](documentation-comments.md#paramref)     | <span data-ttu-id="c3986-152">Verificare che una parola sia un nome di parametro</span><span class="sxs-lookup"><span data-stu-id="c3986-152">Identify that a word is a parameter name</span></span>               |
| `<permission>`   | [`<permission>`](documentation-comments.md#permission) | <span data-ttu-id="c3986-153">Documentare l'accessibilità di sicurezza di un membro</span><span class="sxs-lookup"><span data-stu-id="c3986-153">Document the security accessibility of a member</span></span>        |
| `<remarks>`      | [`<remarks>`](documentation-comments.md#remarks)       | <span data-ttu-id="c3986-154">Descrivere informazioni aggiuntive su un tipo</span><span class="sxs-lookup"><span data-stu-id="c3986-154">Describe additional information about a type</span></span>           |
| `<returns>`      | [`<returns>`](documentation-comments.md#returns)       | <span data-ttu-id="c3986-155">Descrivere il valore restituito di un metodo</span><span class="sxs-lookup"><span data-stu-id="c3986-155">Describe the return value of a method</span></span>                  |
| `<see>`          | [`<see>`](documentation-comments.md#see)               | <span data-ttu-id="c3986-156">Specificare un collegamento</span><span class="sxs-lookup"><span data-stu-id="c3986-156">Specify a link</span></span>                                         |
| `<seealso>`      | [`<seealso>`](documentation-comments.md#seealso)       | <span data-ttu-id="c3986-157">Genera una voce See also</span><span class="sxs-lookup"><span data-stu-id="c3986-157">Generate a See Also entry</span></span>                              |
| `<summary>`      | [`<summary>`](documentation-comments.md#summary)       | <span data-ttu-id="c3986-158">Descrivere un tipo o un membro di un tipo</span><span class="sxs-lookup"><span data-stu-id="c3986-158">Describe a type or a member of a type</span></span>                  |
| `<value>`        | [`<value>`](documentation-comments.md#value)           | <span data-ttu-id="c3986-159">Descrivere una proprietà</span><span class="sxs-lookup"><span data-stu-id="c3986-159">Describe a property</span></span>                                    |
| `<typeparam>`    |                                                        | <span data-ttu-id="c3986-160">Descrivere un parametro di tipo generico</span><span class="sxs-lookup"><span data-stu-id="c3986-160">Describe a generic type parameter</span></span>                      |
| `<typeparamref>` |                                                        | <span data-ttu-id="c3986-161">Verificare che una parola sia un nome di parametro di tipo</span><span class="sxs-lookup"><span data-stu-id="c3986-161">Identify that a word is a type parameter name</span></span>          |

### `<c>`

<span data-ttu-id="c3986-162">Questo tag fornisce un meccanismo per indicare che un frammento di testo all'interno di una descrizione deve essere impostato in un tipo di carattere speciale, ad esempio quello usato per un blocco di codice.</span><span class="sxs-lookup"><span data-stu-id="c3986-162">This tag provides a mechanism to indicate that a fragment of text within a description should be set in a special font such as that used for a block of code.</span></span> <span data-ttu-id="c3986-163">Per le righe di codice effettivo, `<code>` utilizzare[`<code>`](documentation-comments.md#code)().</span><span class="sxs-lookup"><span data-stu-id="c3986-163">For lines of actual code, use `<code>` ([`<code>`](documentation-comments.md#code)).</span></span>

<span data-ttu-id="c3986-164">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-164">__Syntax:__</span></span>

```xml
<c>text</c>
```

<span data-ttu-id="c3986-165">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-165">__Example:__</span></span>

```csharp
/// <summary>Class <c>Point</c> models a point in a two-dimensional
/// plane.</summary>

public class Point 
{
    // ...
}
```

### `<code>`

<span data-ttu-id="c3986-166">Questo tag viene usato per impostare una o più righe di codice sorgente o output del programma in un tipo di carattere speciale.</span><span class="sxs-lookup"><span data-stu-id="c3986-166">This tag is used to set one or more lines of source code or program output in some special font.</span></span> <span data-ttu-id="c3986-167">Per i frammenti di codice di dimensioni ridotte `<c>` nella[`<c>`](documentation-comments.md#c)descrizione, utilizzare ().</span><span class="sxs-lookup"><span data-stu-id="c3986-167">For small code fragments in narrative, use `<c>` ([`<c>`](documentation-comments.md#c)).</span></span>

<span data-ttu-id="c3986-168">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-168">__Syntax:__</span></span>

```xml
<code>source code or program output</code>
```

<span data-ttu-id="c3986-169">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-169">__Example:__</span></span>

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

<span data-ttu-id="c3986-170">Questo tag consente il codice di esempio all'interno di un commento, per specificare come può essere usato un metodo o un altro membro della libreria.</span><span class="sxs-lookup"><span data-stu-id="c3986-170">This tag allows example code within a comment, to specify how a method or other library member may be used.</span></span> <span data-ttu-id="c3986-171">In genere, questa operazione comporta anche l'uso del tag `<code>` ([`<code>`](documentation-comments.md#code)).</span><span class="sxs-lookup"><span data-stu-id="c3986-171">Ordinarily, this would also involve use of the tag `<code>` ([`<code>`](documentation-comments.md#code)) as well.</span></span>

<span data-ttu-id="c3986-172">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-172">__Syntax:__</span></span>

```xml
<example>description</example>
```

<span data-ttu-id="c3986-173">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-173">__Example:__</span></span>

<span data-ttu-id="c3986-174">Per `<code>` un[`<code>`](documentation-comments.md#code)esempio, vedere ().</span><span class="sxs-lookup"><span data-stu-id="c3986-174">See `<code>` ([`<code>`](documentation-comments.md#code)) for an example.</span></span>

### `<exception>`

<span data-ttu-id="c3986-175">Questo tag fornisce un modo per documentare le eccezioni che possono essere generate da un metodo.</span><span class="sxs-lookup"><span data-stu-id="c3986-175">This tag provides a way to document the exceptions a method can throw.</span></span>

<span data-ttu-id="c3986-176">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-176">__Syntax:__</span></span>

```xml
<exception cref="member">description</exception>
```

<span data-ttu-id="c3986-177">dove</span><span class="sxs-lookup"><span data-stu-id="c3986-177">where</span></span>

* <span data-ttu-id="c3986-178">`member`nome di un membro.</span><span class="sxs-lookup"><span data-stu-id="c3986-178">`member` is the name of a member.</span></span> <span data-ttu-id="c3986-179">Il generatore di documentazione verifica che il membro specificato esista e `member` si traduce nel nome canonico dell'elemento nel file di documentazione.</span><span class="sxs-lookup"><span data-stu-id="c3986-179">The documentation generator checks that the given member exists and translates `member` to the canonical element name in the documentation file.</span></span>
* <span data-ttu-id="c3986-180">`description`Descrizione delle circostanze in cui viene generata l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="c3986-180">`description` is a description of the circumstances in which the exception is thrown.</span></span>

<span data-ttu-id="c3986-181">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-181">__Example:__</span></span>

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

<span data-ttu-id="c3986-182">Questo tag consente di includere informazioni da un documento XML esterno al file di codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="c3986-182">This tag allows including information from an XML document that is external to the source code file.</span></span> <span data-ttu-id="c3986-183">Il file esterno deve essere un documento XML ben formato e un'espressione XPath viene applicata a tale documento per specificare il codice XML del documento da includere.</span><span class="sxs-lookup"><span data-stu-id="c3986-183">The external file must be a well-formed XML document, and an XPath expression is applied to that document to specify what XML from that document to include.</span></span> <span data-ttu-id="c3986-184">Il `<include>` tag viene quindi sostituito con il codice XML selezionato dal documento esterno.</span><span class="sxs-lookup"><span data-stu-id="c3986-184">The `<include>` tag is then replaced with the selected XML from the external document.</span></span>

<span data-ttu-id="c3986-185">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-185">__Syntax:__</span></span>

```xml
<include file="filename" path="xpath" />
```

<span data-ttu-id="c3986-186">dove</span><span class="sxs-lookup"><span data-stu-id="c3986-186">where</span></span>

* <span data-ttu-id="c3986-187">`filename`nome file di un file XML esterno.</span><span class="sxs-lookup"><span data-stu-id="c3986-187">`filename` is the file name of an external XML file.</span></span> <span data-ttu-id="c3986-188">Il nome del file viene interpretato in relazione al file che contiene il tag di inclusione.</span><span class="sxs-lookup"><span data-stu-id="c3986-188">The file name is interpreted relative to the file that contains the include tag.</span></span>
* <span data-ttu-id="c3986-189">`xpath`espressione XPath che consente di selezionare parte del codice XML nel file XML esterno.</span><span class="sxs-lookup"><span data-stu-id="c3986-189">`xpath` is an XPath expression that selects some of the XML in the external XML file.</span></span>

<span data-ttu-id="c3986-190">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-190">__Example:__</span></span>

<span data-ttu-id="c3986-191">Se il codice sorgente contiene una dichiarazione come:</span><span class="sxs-lookup"><span data-stu-id="c3986-191">If the source code contained a declaration like:</span></span>

```csharp
/// <include file="docs.xml" path='extradoc/class[@name="IntList"]/*' />
public class IntList { ... }
```

<span data-ttu-id="c3986-192">e il file esterno "docs. xml" presenta il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="c3986-192">and the external file "docs.xml" had the following contents:</span></span>

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

<span data-ttu-id="c3986-193">la stessa documentazione viene quindi restituita come se il codice sorgente contenesse:</span><span class="sxs-lookup"><span data-stu-id="c3986-193">then the same documentation is output as if the source code contained:</span></span>

```csharp
/// <summary>
///    Contains a list of integers.
/// </summary>
public class IntList { ... }
```

### `<list>`

<span data-ttu-id="c3986-194">Questo tag viene usato per creare un elenco o una tabella di elementi.</span><span class="sxs-lookup"><span data-stu-id="c3986-194">This tag is used to create a list or table of items.</span></span> <span data-ttu-id="c3986-195">Può contenere un `<listheader>` blocco per definire la riga di intestazione di un elenco di tabelle o definizioni.</span><span class="sxs-lookup"><span data-stu-id="c3986-195">It may contain a `<listheader>` block to define the heading row of either a table or definition list.</span></span> <span data-ttu-id="c3986-196">Quando si definisce una tabella, è necessario fornire solo `term` una voce per nell'intestazione.</span><span class="sxs-lookup"><span data-stu-id="c3986-196">(When defining a table, only an entry for `term` in the heading need be supplied.)</span></span>

<span data-ttu-id="c3986-197">Ogni elemento nell'elenco viene specificato con un `<item>` blocco.</span><span class="sxs-lookup"><span data-stu-id="c3986-197">Each item in the list is specified with an `<item>` block.</span></span> <span data-ttu-id="c3986-198">Quando si crea un elenco di definizioni `term` , `description` è necessario specificare sia che.</span><span class="sxs-lookup"><span data-stu-id="c3986-198">When creating a definition list, both `term` and `description` must be specified.</span></span> <span data-ttu-id="c3986-199">Tuttavia, per una tabella, un elenco puntato o un elenco numerato, `description` è necessario specificare solo.</span><span class="sxs-lookup"><span data-stu-id="c3986-199">However, for a table, bulleted list, or numbered list, only `description` need be specified.</span></span>

<span data-ttu-id="c3986-200">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-200">__Syntax:__</span></span>

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

<span data-ttu-id="c3986-201">dove</span><span class="sxs-lookup"><span data-stu-id="c3986-201">where</span></span>

* <span data-ttu-id="c3986-202">`term`termine da definire, la cui definizione è in `description`.</span><span class="sxs-lookup"><span data-stu-id="c3986-202">`term` is the term to define, whose definition is in `description`.</span></span>
* <span data-ttu-id="c3986-203">`description`è un elemento in un elenco puntato o numerato o la definizione di un `term`oggetto.</span><span class="sxs-lookup"><span data-stu-id="c3986-203">`description` is either an item in a bullet or numbered list, or the definition of a `term`.</span></span>

<span data-ttu-id="c3986-204">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-204">__Example:__</span></span>

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

<span data-ttu-id="c3986-205">Questo tag è da usare all'interno di altri tag, `<summary>` ad[`<remarks>`](documentation-comments.md#remarks)esempio ( `<returns>` )[`<returns>`](documentation-comments.md#returns)o (), e consente di aggiungere la struttura al testo.</span><span class="sxs-lookup"><span data-stu-id="c3986-205">This tag is for use inside other tags, such as `<summary>` ([`<remarks>`](documentation-comments.md#remarks)) or `<returns>` ([`<returns>`](documentation-comments.md#returns)), and permits structure to be added to text.</span></span>

<span data-ttu-id="c3986-206">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-206">__Syntax:__</span></span>

```xml
<para>content</para>
```

<span data-ttu-id="c3986-207">dove `content` è il testo del paragrafo.</span><span class="sxs-lookup"><span data-stu-id="c3986-207">where `content` is the text of the paragraph.</span></span>

<span data-ttu-id="c3986-208">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-208">__Example:__</span></span>

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

<span data-ttu-id="c3986-209">Questo tag viene usato per descrivere un parametro per un metodo, un costruttore o un indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="c3986-209">This tag is used to describe a parameter for a method, constructor, or indexer.</span></span>

<span data-ttu-id="c3986-210">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-210">__Syntax:__</span></span>

```xml
<param name="name">description</param>
```

<span data-ttu-id="c3986-211">dove</span><span class="sxs-lookup"><span data-stu-id="c3986-211">where</span></span>

* <span data-ttu-id="c3986-212">`name`nome del parametro.</span><span class="sxs-lookup"><span data-stu-id="c3986-212">`name` is the name of the parameter.</span></span>
* <span data-ttu-id="c3986-213">`description`Descrizione del parametro.</span><span class="sxs-lookup"><span data-stu-id="c3986-213">`description` is a description of the parameter.</span></span>

<span data-ttu-id="c3986-214">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-214">__Example:__</span></span>

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

<span data-ttu-id="c3986-215">Questo tag viene usato per indicare che una parola è un parametro.</span><span class="sxs-lookup"><span data-stu-id="c3986-215">This tag is used to indicate that a word is a parameter.</span></span> <span data-ttu-id="c3986-216">Il file di documentazione può essere elaborato per formattare questo parametro in modo distinto.</span><span class="sxs-lookup"><span data-stu-id="c3986-216">The documentation file can be processed to format this parameter in some distinct way.</span></span>

<span data-ttu-id="c3986-217">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-217">__Syntax:__</span></span>

```xml
<paramref name="name"/>
```

<span data-ttu-id="c3986-218">dove `name` è il nome del parametro.</span><span class="sxs-lookup"><span data-stu-id="c3986-218">where `name` is the name of the parameter.</span></span>

<span data-ttu-id="c3986-219">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-219">__Example:__</span></span>

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

<span data-ttu-id="c3986-220">Questo tag consente di documentare l'accessibilità di sicurezza di un membro.</span><span class="sxs-lookup"><span data-stu-id="c3986-220">This tag allows the security accessibility of a member to be documented.</span></span>

<span data-ttu-id="c3986-221">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-221">__Syntax:__</span></span>

```xml
<permission cref="member">description</permission>
```

<span data-ttu-id="c3986-222">dove</span><span class="sxs-lookup"><span data-stu-id="c3986-222">where</span></span>

* <span data-ttu-id="c3986-223">`member`nome di un membro.</span><span class="sxs-lookup"><span data-stu-id="c3986-223">`member` is the name of a member.</span></span> <span data-ttu-id="c3986-224">Il generatore di documentazione verifica che l'elemento di codice specificato esista e converte il *membro* nel nome canonico dell'elemento nel file di documentazione.</span><span class="sxs-lookup"><span data-stu-id="c3986-224">The documentation generator checks that the given code element exists and translates *member* to the canonical element name in the documentation file.</span></span>
* <span data-ttu-id="c3986-225">`description`Descrizione dell'accesso al membro.</span><span class="sxs-lookup"><span data-stu-id="c3986-225">`description` is a description of the access to the member.</span></span>

<span data-ttu-id="c3986-226">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-226">__Example:__</span></span>

```csharp
/// <permission cref="System.Security.PermissionSet">Everyone can
/// access this method.</permission>

public static void Test() {
    // ...
}
```

### `<remarks>`

<span data-ttu-id="c3986-227">Questo tag viene usato per specificare informazioni aggiuntive su un tipo.</span><span class="sxs-lookup"><span data-stu-id="c3986-227">This tag is used to specify extra information about a type.</span></span> <span data-ttu-id="c3986-228">(Usare `<summary>` ([`<summary>`](documentation-comments.md#summary)) per descrivere il tipo stesso e i membri di un tipo.</span><span class="sxs-lookup"><span data-stu-id="c3986-228">(Use `<summary>` ([`<summary>`](documentation-comments.md#summary)) to describe the type itself and the members of a type.)</span></span>

<span data-ttu-id="c3986-229">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-229">__Syntax:__</span></span>

```xml
<remarks>description</remarks>
```

<span data-ttu-id="c3986-230">dove `description` è il testo del contrassegno.</span><span class="sxs-lookup"><span data-stu-id="c3986-230">where `description` is the text of the remark.</span></span>

<span data-ttu-id="c3986-231">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-231">__Example:__</span></span>

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

<span data-ttu-id="c3986-232">Questo tag viene usato per descrivere il valore restituito di un metodo.</span><span class="sxs-lookup"><span data-stu-id="c3986-232">This tag is used to describe the return value of a method.</span></span>

<span data-ttu-id="c3986-233">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-233">__Syntax:__</span></span>

```xml
<returns>description</returns>
```

<span data-ttu-id="c3986-234">dove `description` è una descrizione del valore restituito.</span><span class="sxs-lookup"><span data-stu-id="c3986-234">where `description` is a description of the return value.</span></span>

<span data-ttu-id="c3986-235">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-235">__Example:__</span></span>

```csharp
/// <summary>Report a point's location as a string.</summary>
/// <returns>A string representing a point's location, in the form (x,y),
///    without any leading, trailing, or embedded whitespace.</returns>
public override string ToString() {
    return "(" + X + "," + Y + ")";
}
```

### `<see>`

<span data-ttu-id="c3986-236">Questo tag consente di specificare un collegamento all'interno del testo.</span><span class="sxs-lookup"><span data-stu-id="c3986-236">This tag allows a link to be specified within text.</span></span> <span data-ttu-id="c3986-237">Usare `<seealso>` [(`<seealso>`](documentation-comments.md#seealso)) per indicare il testo da visualizzare in una sezione vedere anche.</span><span class="sxs-lookup"><span data-stu-id="c3986-237">Use `<seealso>` ([`<seealso>`](documentation-comments.md#seealso)) to indicate text that is to appear in a See Also section.</span></span>

<span data-ttu-id="c3986-238">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-238">__Syntax:__</span></span>

```xml
<see cref="member"/>
```

<span data-ttu-id="c3986-239">dove `member` è il nome di un membro.</span><span class="sxs-lookup"><span data-stu-id="c3986-239">where `member` is the name of a member.</span></span> <span data-ttu-id="c3986-240">Il generatore di documentazione verifica che l'elemento di codice specificato esista e modifichi il *membro* nel nome dell'elemento nel file di documentazione generato.</span><span class="sxs-lookup"><span data-stu-id="c3986-240">The documentation generator checks that the given code element exists and changes *member* to the element name in the generated documentation file.</span></span>

<span data-ttu-id="c3986-241">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-241">__Example:__</span></span>

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

<span data-ttu-id="c3986-242">Questo tag consente la generazione di una voce per la sezione vedere anche.</span><span class="sxs-lookup"><span data-stu-id="c3986-242">This tag allows an entry to be generated for the See Also section.</span></span> <span data-ttu-id="c3986-243">Usare `<see>` [(`<see>`](documentation-comments.md#see)) per specificare un collegamento dall'interno del testo.</span><span class="sxs-lookup"><span data-stu-id="c3986-243">Use `<see>` ([`<see>`](documentation-comments.md#see)) to specify a link from within text.</span></span>

<span data-ttu-id="c3986-244">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-244">__Syntax:__</span></span>

```xml
<seealso cref="member"/>
```

<span data-ttu-id="c3986-245">dove `member` è il nome di un membro.</span><span class="sxs-lookup"><span data-stu-id="c3986-245">where `member` is the name of a member.</span></span> <span data-ttu-id="c3986-246">Il generatore di documentazione verifica che l'elemento di codice specificato esista e modifichi il *membro* nel nome dell'elemento nel file di documentazione generato.</span><span class="sxs-lookup"><span data-stu-id="c3986-246">The documentation generator checks that the given code element exists and changes *member* to the element name in the generated documentation file.</span></span>

<span data-ttu-id="c3986-247">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-247">__Example:__</span></span>

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

Questo tag può essere usato per descrivere un tipo o un membro di un tipo. <span data-ttu-id="c3986-249">Usare `<remarks>` [(`<remarks>`](documentation-comments.md#remarks)) per descrivere il tipo stesso.</span><span class="sxs-lookup"><span data-stu-id="c3986-249">Use `<remarks>` ([`<remarks>`](documentation-comments.md#remarks)) to describe the type itself.</span></span>

<span data-ttu-id="c3986-250">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-250">__Syntax:__</span></span>

```xml
<summary>description</summary>
```

<span data-ttu-id="c3986-251">dove `description` è un riepilogo del tipo o del membro.</span><span class="sxs-lookup"><span data-stu-id="c3986-251">where `description` is a summary of the type or member.</span></span>

<span data-ttu-id="c3986-252">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-252">__Example:__</span></span>

```csharp
/// <summary>This constructor initializes the new Point to (0,0).</summary>
public Point() : this(0,0) {
}
```

### `<value>`

<span data-ttu-id="c3986-253">Questo tag consente la descrizione di una proprietà.</span><span class="sxs-lookup"><span data-stu-id="c3986-253">This tag allows a property to be described.</span></span>

<span data-ttu-id="c3986-254">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-254">__Syntax:__</span></span>

```xml
<value>property description</value>
```

<span data-ttu-id="c3986-255">dove `property description` è una descrizione per la proprietà.</span><span class="sxs-lookup"><span data-stu-id="c3986-255">where `property description` is a description for the property.</span></span>

<span data-ttu-id="c3986-256">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-256">__Example:__</span></span>

```csharp
/// <value>Property <c>X</c> represents the point's x-coordinate.</value>
public int X
{
    get { return x; }
    set { x = value; }
}
```

### `<typeparam>`

<span data-ttu-id="c3986-257">Questo tag viene usato per descrivere un parametro di tipo generico per una classe, uno struct, un'interfaccia, un delegato o un metodo.</span><span class="sxs-lookup"><span data-stu-id="c3986-257">This tag is used to describe a generic type parameter for a class, struct, interface, delegate, or method.</span></span>

<span data-ttu-id="c3986-258">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-258">__Syntax:__</span></span>

```xml
<typeparam name="name">description</typeparam>
```

<span data-ttu-id="c3986-259">dove `name` è il nome del parametro di tipo e `description` è la relativa descrizione.</span><span class="sxs-lookup"><span data-stu-id="c3986-259">where `name` is the name of the type parameter, and `description` is its description.</span></span>

<span data-ttu-id="c3986-260">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-260">__Example:__</span></span>

```csharp
/// <summary>A generic list class.</summary>
/// <typeparam name="T">The type stored by the list.</typeparam>
public class MyList<T> {
    ...
}
```

### `<typeparamref>`

<span data-ttu-id="c3986-261">Questo tag viene usato per indicare che una parola è un parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="c3986-261">This tag is used to indicate that a word is a type parameter.</span></span> <span data-ttu-id="c3986-262">Il file di documentazione può essere elaborato per formattare questo parametro di tipo in modo distinto.</span><span class="sxs-lookup"><span data-stu-id="c3986-262">The documentation file can be processed to format this type parameter in some distinct way.</span></span>

<span data-ttu-id="c3986-263">__Sintassi__</span><span class="sxs-lookup"><span data-stu-id="c3986-263">__Syntax:__</span></span>

```xml
<typeparamref name="name"/>
```

<span data-ttu-id="c3986-264">dove `name` è il nome del parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="c3986-264">where `name` is the name of the type parameter.</span></span>

<span data-ttu-id="c3986-265">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="c3986-265">__Example:__</span></span>

```csharp
/// <summary>This method fetches data and returns a list of <typeparamref name="T"/>.</summary>
/// <param name="query">query to execute</param>
public List<T> FetchData<T>(string query) {
    ...
}
```

## <a name="processing-the-documentation-file"></a><span data-ttu-id="c3986-266">Elaborazione del file di documentazione</span><span class="sxs-lookup"><span data-stu-id="c3986-266">Processing the documentation file</span></span>

<span data-ttu-id="c3986-267">Il generatore di documentazione genera una stringa ID per ogni elemento nel codice sorgente contrassegnato con un commento della documentazione.</span><span class="sxs-lookup"><span data-stu-id="c3986-267">The documentation generator generates an ID string for each element in the source code that is tagged with a documentation comment.</span></span> <span data-ttu-id="c3986-268">Questa stringa ID identifica in modo univoco un elemento di origine.</span><span class="sxs-lookup"><span data-stu-id="c3986-268">This ID string uniquely identifies a source element.</span></span> <span data-ttu-id="c3986-269">Un visualizzatore di documentazione può utilizzare una stringa ID per identificare l'elemento di reflection o metadati corrispondente a cui si applica la documentazione.</span><span class="sxs-lookup"><span data-stu-id="c3986-269">A documentation viewer can use an ID string to identify the corresponding metadata/reflection item to which the documentation applies.</span></span>

<span data-ttu-id="c3986-270">Il file di documentazione non è una rappresentazione gerarchica del codice sorgente. si tratta invece di un elenco semplice con una stringa ID generata per ogni elemento.</span><span class="sxs-lookup"><span data-stu-id="c3986-270">The documentation file is not a hierarchical representation of the source code; rather, it is a flat list with a generated ID string for each element.</span></span>

### <a name="id-string-format"></a><span data-ttu-id="c3986-271">Formato stringa ID</span><span class="sxs-lookup"><span data-stu-id="c3986-271">ID string format</span></span>

<span data-ttu-id="c3986-272">Quando genera le stringhe ID, il generatore di documentazione osserva le regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="c3986-272">The documentation generator observes the following rules when it generates the ID strings:</span></span>

*  <span data-ttu-id="c3986-273">Assenza di spazi vuoti nella stringa.</span><span class="sxs-lookup"><span data-stu-id="c3986-273">No white space is placed in the string.</span></span>

*  <span data-ttu-id="c3986-274">La prima parte della stringa identifica il tipo di membro che viene documentato, tramite un singolo carattere seguito da due punti.</span><span class="sxs-lookup"><span data-stu-id="c3986-274">The first part of the string identifies the kind of member being documented, via a single character followed by a colon.</span></span> <span data-ttu-id="c3986-275">Sono definiti i seguenti tipi di membri:</span><span class="sxs-lookup"><span data-stu-id="c3986-275">The following kinds of members are defined:</span></span>

   | <span data-ttu-id="c3986-276">__Carattere__</span><span class="sxs-lookup"><span data-stu-id="c3986-276">__Character__</span></span> | <span data-ttu-id="c3986-277">__Descrizione__</span><span class="sxs-lookup"><span data-stu-id="c3986-277">__Description__</span></span>                                             |
   |---------------|-------------------------------------------------------------|
   | <span data-ttu-id="c3986-278">E</span><span class="sxs-lookup"><span data-stu-id="c3986-278">E</span></span>             | <span data-ttu-id="c3986-279">event</span><span class="sxs-lookup"><span data-stu-id="c3986-279">Event</span></span>                                                       |
   | <span data-ttu-id="c3986-280">F</span><span class="sxs-lookup"><span data-stu-id="c3986-280">F</span></span>             | <span data-ttu-id="c3986-281">Campo</span><span class="sxs-lookup"><span data-stu-id="c3986-281">Field</span></span>                                                       |
   | <span data-ttu-id="c3986-282">M</span><span class="sxs-lookup"><span data-stu-id="c3986-282">M</span></span>             | <span data-ttu-id="c3986-283">Metodo (inclusi costruttori, distruttori e operatori)</span><span class="sxs-lookup"><span data-stu-id="c3986-283">Method (including constructors, destructors, and operators)</span></span> |
   | <span data-ttu-id="c3986-284">N</span><span class="sxs-lookup"><span data-stu-id="c3986-284">N</span></span>             | <span data-ttu-id="c3986-285">Spazio dei nomi</span><span class="sxs-lookup"><span data-stu-id="c3986-285">Namespace</span></span>                                                   |
   | <span data-ttu-id="c3986-286">P</span><span class="sxs-lookup"><span data-stu-id="c3986-286">P</span></span>             | <span data-ttu-id="c3986-287">Proprietà (inclusi gli indicizzatori)</span><span class="sxs-lookup"><span data-stu-id="c3986-287">Property (including indexers)</span></span>                               |
   | <span data-ttu-id="c3986-288">T</span><span class="sxs-lookup"><span data-stu-id="c3986-288">T</span></span>             | <span data-ttu-id="c3986-289">Tipo (ad esempio classe, delegato, enumerazione, interfaccia e struct)</span><span class="sxs-lookup"><span data-stu-id="c3986-289">Type (such as class, delegate, enum, interface, and struct)</span></span> |
   | <span data-ttu-id="c3986-290">!</span><span class="sxs-lookup"><span data-stu-id="c3986-290">!</span></span>             | <span data-ttu-id="c3986-291">Stringa di errore; il resto della stringa fornisce informazioni sull'errore.</span><span class="sxs-lookup"><span data-stu-id="c3986-291">Error string; the rest of the string provides information about the error.</span></span> <span data-ttu-id="c3986-292">Ad esempio, il generatore di documentazione genera informazioni sugli errori per i collegamenti che non possono essere risolti.</span><span class="sxs-lookup"><span data-stu-id="c3986-292">For example, the documentation generator generates error information for links that cannot be resolved.</span></span> |

*  <span data-ttu-id="c3986-293">La seconda parte della stringa è il nome completo dell'elemento, a partire dalla radice dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="c3986-293">The second part of the string is the fully qualified name of the element, starting at the root of the namespace.</span></span> <span data-ttu-id="c3986-294">Il nome dell'elemento, i tipi di inclusione e lo spazio dei nomi sono separati da punti.</span><span class="sxs-lookup"><span data-stu-id="c3986-294">The name of the element, its enclosing type(s), and namespace are separated by periods.</span></span> <span data-ttu-id="c3986-295">Se il nome dell'elemento ha punti, questi vengono sostituiti da `#(U+0023)` caratteri.</span><span class="sxs-lookup"><span data-stu-id="c3986-295">If the name of the item itself has periods, they are replaced by `#(U+0023)` characters.</span></span> <span data-ttu-id="c3986-296">Si presuppone che nessun elemento abbia questo carattere nel nome.</span><span class="sxs-lookup"><span data-stu-id="c3986-296">(It is assumed that no element has this character in its name.)</span></span>
*  <span data-ttu-id="c3986-297">Per i metodi e le proprietà con argomenti, l'elenco di argomenti segue, racchiuso tra parentesi.</span><span class="sxs-lookup"><span data-stu-id="c3986-297">For methods and properties with arguments, the argument list follows, enclosed in parentheses.</span></span> <span data-ttu-id="c3986-298">Per gli argomenti senza argomenti, le parentesi vengono omesse.</span><span class="sxs-lookup"><span data-stu-id="c3986-298">For those without arguments, the parentheses are omitted.</span></span> <span data-ttu-id="c3986-299">Gli argomenti sono separati da virgole.</span><span class="sxs-lookup"><span data-stu-id="c3986-299">The arguments are separated by commas.</span></span> <span data-ttu-id="c3986-300">La codifica di ogni argomento è identica alla firma dell'interfaccia della riga di comando, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="c3986-300">The encoding of each argument is the same as a CLI signature, as follows:</span></span>
   *  <span data-ttu-id="c3986-301">Gli argomenti sono rappresentati dal nome della documentazione, che è basato sul nome completo, modificato come segue:</span><span class="sxs-lookup"><span data-stu-id="c3986-301">Arguments are represented by their documentation name, which is based on their fully qualified name, modified as follows:</span></span>
      * <span data-ttu-id="c3986-302">Gli argomenti che rappresentano tipi generici hanno un `` ` `` carattere (apice inverso) accodato seguito dal numero di parametri di tipo</span><span class="sxs-lookup"><span data-stu-id="c3986-302">Arguments that represent generic types have an appended `` ` `` (backtick) character followed by the number of type parameters</span></span>
      * <span data-ttu-id="c3986-303">Gli argomenti con `out` il `ref` modificatore o `@` hanno un nome di tipo successivo.</span><span class="sxs-lookup"><span data-stu-id="c3986-303">Arguments having the `out` or `ref` modifier have an `@` following their type name.</span></span> <span data-ttu-id="c3986-304">Gli argomenti passati per valore o `params` via non hanno una notazione speciale.</span><span class="sxs-lookup"><span data-stu-id="c3986-304">Arguments passed by value or via `params` have no special notation.</span></span>
      * <span data-ttu-id="c3986-305">Gli argomenti che sono matrici sono rappresentati `[lowerbound:size, ... , lowerbound:size]` come dove il numero di virgole è il rango minore di uno e i limiti inferiori e le dimensioni di ogni dimensione, se noti, sono rappresentati in decimali.</span><span class="sxs-lookup"><span data-stu-id="c3986-305">Arguments that are arrays are represented as `[lowerbound:size, ... , lowerbound:size]` where the number of commas is the rank less one, and the lower bounds and size of each dimension, if known, are represented in decimal.</span></span> <span data-ttu-id="c3986-306">Se non si specifica una dimensione o un limite inferiore, viene omesso.</span><span class="sxs-lookup"><span data-stu-id="c3986-306">If a lower bound or size is not specified, it is omitted.</span></span> <span data-ttu-id="c3986-307">Se il limite inferiore e le dimensioni di una determinata dimensione vengono omessi, `:` viene omesso anche.</span><span class="sxs-lookup"><span data-stu-id="c3986-307">If the lower bound and size for a particular dimension are omitted, the `:` is omitted as well.</span></span> <span data-ttu-id="c3986-308">Le matrici di matrici sono rappresentate da `[]` una per ogni livello.</span><span class="sxs-lookup"><span data-stu-id="c3986-308">Jagged arrays are represented by one `[]` per level.</span></span>
      * <span data-ttu-id="c3986-309">Gli argomenti con tipi di puntatore diversi da void sono rappresentati `*` utilizzando un oggetto che segue il nome del tipo.</span><span class="sxs-lookup"><span data-stu-id="c3986-309">Arguments that have pointer types other than void are represented using a `*` following the type name.</span></span> <span data-ttu-id="c3986-310">Un puntatore void viene rappresentato usando un nome di `System.Void`tipo.</span><span class="sxs-lookup"><span data-stu-id="c3986-310">A void pointer is represented using a type name of `System.Void`.</span></span>
      * <span data-ttu-id="c3986-311">Gli argomenti che fanno riferimento a parametri di tipo generico definiti sui tipi vengono codificati `` ` `` utilizzando il carattere (apice inverso) seguito dall'indice in base zero del parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="c3986-311">Arguments that refer to generic type parameters defined on types are encoded using the `` ` `` (backtick) character followed by the zero-based index of the type parameter.</span></span>
      * <span data-ttu-id="c3986-312">Gli argomenti che usano parametri di tipo generico definiti nei metodi usano un doppio apice ``` `` ``` anziché l'oggetto `` ` `` usato per i tipi.</span><span class="sxs-lookup"><span data-stu-id="c3986-312">Arguments that use generic type parameters defined in methods use a double-backtick ``` `` ``` instead of the `` ` `` used for types.</span></span>
      * <span data-ttu-id="c3986-313">Gli argomenti che fanno riferimento a tipi generici costruiti vengono codificati usando il tipo generico, `{`seguito da, seguito da un elenco delimitato da virgole di argomenti `}`di tipo, seguito da.</span><span class="sxs-lookup"><span data-stu-id="c3986-313">Arguments that refer to constructed generic types are encoded using the generic type, followed by `{`, followed by a comma-separated list of type arguments, followed by `}`.</span></span>

### <a name="id-string-examples"></a><span data-ttu-id="c3986-314">Esempi di stringa ID</span><span class="sxs-lookup"><span data-stu-id="c3986-314">ID string examples</span></span>

<span data-ttu-id="c3986-315">Gli esempi seguenti mostrano un frammento di C# codice, insieme alla stringa ID prodotta da ogni elemento di origine in grado di presentare un commento sulla documentazione:</span><span class="sxs-lookup"><span data-stu-id="c3986-315">The following examples each show a fragment of C# code, along with the ID string produced from each source element capable of having a documentation comment:</span></span>

*  <span data-ttu-id="c3986-316">I tipi sono rappresentati usando il nome completo, arricchito con informazioni generiche:</span><span class="sxs-lookup"><span data-stu-id="c3986-316">Types are represented using their fully qualified name, augmented with generic information:</span></span>

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

*  <span data-ttu-id="c3986-317">I campi sono rappresentati dal nome completo:</span><span class="sxs-lookup"><span data-stu-id="c3986-317">Fields are represented by their fully qualified name:</span></span>

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

*  <span data-ttu-id="c3986-318">Costruttori.</span><span class="sxs-lookup"><span data-stu-id="c3986-318">Constructors.</span></span>

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

*  <span data-ttu-id="c3986-319">Distruttori.</span><span class="sxs-lookup"><span data-stu-id="c3986-319">Destructors.</span></span>

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

*  <span data-ttu-id="c3986-320">Metodi.</span><span class="sxs-lookup"><span data-stu-id="c3986-320">Methods.</span></span>

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

*  <span data-ttu-id="c3986-321">Proprietà e indicizzatori.</span><span class="sxs-lookup"><span data-stu-id="c3986-321">Properties and indexers.</span></span>

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

*  <span data-ttu-id="c3986-322">Eventi.</span><span class="sxs-lookup"><span data-stu-id="c3986-322">Events.</span></span>

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

*  <span data-ttu-id="c3986-323">Operatori unari.</span><span class="sxs-lookup"><span data-stu-id="c3986-323">Unary operators.</span></span>

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

   <span data-ttu-id="c3986-324">Il set completo di nomi di funzione di operatore unario utilizzato è `op_UnaryPlus`il `op_UnaryNegation`seguente `op_LogicalNot`:, `op_Increment`, `op_Decrement`, `op_True` `op_OnesComplement`,, `op_False`, e.</span><span class="sxs-lookup"><span data-stu-id="c3986-324">The complete set of unary operator function names used is as follows: `op_UnaryPlus`, `op_UnaryNegation`, `op_LogicalNot`, `op_OnesComplement`, `op_Increment`, `op_Decrement`, `op_True`, and `op_False`.</span></span>

*  <span data-ttu-id="c3986-325">Operatori binari.</span><span class="sxs-lookup"><span data-stu-id="c3986-325">Binary operators.</span></span>

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

   <span data-ttu-id="c3986-326">Il set completo di nomi di funzione di operatore binario utilizzato è il `op_Addition`seguente `op_Subtraction`: `op_Multiply`, `op_Division`, `op_Modulus`, `op_BitwiseAnd`, `op_BitwiseOr`, `op_ExclusiveOr`, `op_LeftShift`, `op_RightShift`,,, `op_Equality`, ,,`op_LessThan`, e`op_GreaterThan`. `op_LessThanOrEqual` `op_Inequality` `op_GreaterThanOrEqual`</span><span class="sxs-lookup"><span data-stu-id="c3986-326">The complete set of binary operator function names used is as follows: `op_Addition`, `op_Subtraction`, `op_Multiply`, `op_Division`, `op_Modulus`, `op_BitwiseAnd`, `op_BitwiseOr`, `op_ExclusiveOr`, `op_LeftShift`, `op_RightShift`, `op_Equality`, `op_Inequality`, `op_LessThan`, `op_LessThanOrEqual`, `op_GreaterThan`, and `op_GreaterThanOrEqual`.</span></span>

*  <span data-ttu-id="c3986-327">Gli operatori di conversione hanno un "`~`" finale seguito dal tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="c3986-327">Conversion operators have a trailing "`~`" followed by the return type.</span></span>

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

## <a name="an-example"></a><span data-ttu-id="c3986-328">un esempio</span><span class="sxs-lookup"><span data-stu-id="c3986-328">An example</span></span>

### <a name="c-source-code"></a><span data-ttu-id="c3986-329">C#codice sorgente</span><span class="sxs-lookup"><span data-stu-id="c3986-329">C# source code</span></span>

<span data-ttu-id="c3986-330">Nell'esempio seguente viene illustrato il codice sorgente di `Point` una classe:</span><span class="sxs-lookup"><span data-stu-id="c3986-330">The following example shows the source code of a `Point` class:</span></span>

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

### <a name="resulting-xml"></a><span data-ttu-id="c3986-331">XML risultante</span><span class="sxs-lookup"><span data-stu-id="c3986-331">Resulting XML</span></span>

<span data-ttu-id="c3986-332">Di seguito è riportato l'output prodotto da un generatore di documentazione quando viene fornito il `Point`codice sorgente per la classe, illustrato in precedenza:</span><span class="sxs-lookup"><span data-stu-id="c3986-332">Here is the output produced by one documentation generator when given the source code for class `Point`, shown above:</span></span>

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
