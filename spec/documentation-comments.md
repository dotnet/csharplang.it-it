---
ms.openlocfilehash: c9f8417dc68153f02ceb72bb1d51f3615f3c4961
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "64488922"
---
# <a name="documentation-comments"></a><span data-ttu-id="5178f-101">Commenti sulla documentazione</span><span class="sxs-lookup"><span data-stu-id="5178f-101">Documentation comments</span></span>

<span data-ttu-id="5178f-102">C# fornisce un meccanismo per i programmatori di documentare il codice usando una sintassi di commento speciali che contiene il testo XML.</span><span class="sxs-lookup"><span data-stu-id="5178f-102">C# provides a mechanism for programmers to document their code using a special comment syntax that contains XML text.</span></span> <span data-ttu-id="5178f-103">Nei file di codice sorgente, è possibile utilizzare i commenti con un determinato formato per indirizzare uno strumento per generare codice XML da tali commenti e gli elementi di codice sorgente, precedano.</span><span class="sxs-lookup"><span data-stu-id="5178f-103">In source code files, comments having a certain form can be used to direct a tool to produce XML from those comments and the source code elements, which they precede.</span></span> <span data-ttu-id="5178f-104">I commenti usando tale sintassi vengono chiamati ***commenti relativi alla documentazione***.</span><span class="sxs-lookup"><span data-stu-id="5178f-104">Comments using such syntax are called ***documentation comments***.</span></span> <span data-ttu-id="5178f-105">Devono precedere immediatamente un tipo definito dall'utente (ad esempio, una classe, delegato o interfaccia) o un membro (ad esempio un campo, evento, proprietà o metodo).</span><span class="sxs-lookup"><span data-stu-id="5178f-105">They must immediately precede a user-defined type (such as a class, delegate, or interface) or a member (such as a field, event, property, or method).</span></span> <span data-ttu-id="5178f-106">Lo strumento di generazione di XML viene chiamato il ***generatore di documentazione***.</span><span class="sxs-lookup"><span data-stu-id="5178f-106">The XML generation tool is called the ***documentation generator***.</span></span> <span data-ttu-id="5178f-107">(Questo generatore può, ma non è necessario, il compilatore c# stesso.) L'output prodotto dal generatore di documentazione viene chiamato il ***file di documentazione***.</span><span class="sxs-lookup"><span data-stu-id="5178f-107">(This generator could be, but need not be, the C# compiler itself.) The output produced by the documentation generator is called the ***documentation file***.</span></span> <span data-ttu-id="5178f-108">Un file di documentazione viene usato come input per un ***Visualizzatore della documentazione***; un strumento intende produrre una sorta di rappresentazione visiva delle informazioni sul tipo e la relativa documentazione.</span><span class="sxs-lookup"><span data-stu-id="5178f-108">A documentation file is used as input to a ***documentation viewer***; a tool intended to produce some sort of visual display of type information and its associated documentation.</span></span>

<span data-ttu-id="5178f-109">Questa specifica è indicato un set di tag da utilizzare nei commenti della documentazione, ma non è necessario utilizzare questi tag e gli altri tag può essere utilizzato, se si desidera, come vengono seguite lungo le regole di codice XML ben formato.</span><span class="sxs-lookup"><span data-stu-id="5178f-109">This specification suggests a set of tags to be used in documentation comments, but use of these tags is not required, and other tags may be used if desired, as long the rules of well-formed XML are followed.</span></span>

## <a name="introduction"></a><span data-ttu-id="5178f-110">Introduzione</span><span class="sxs-lookup"><span data-stu-id="5178f-110">Introduction</span></span>

<span data-ttu-id="5178f-111">Commenti con una sintassi speciale utilizzabile per indirizzare uno strumento per generare codice XML da tali commenti e gli elementi di codice sorgente, precedano.</span><span class="sxs-lookup"><span data-stu-id="5178f-111">Comments having a special form can be used to direct a tool to produce XML from those comments and the source code elements, which they precede.</span></span> <span data-ttu-id="5178f-112">Questi commenti sono commenti a riga singola che iniziano con tre barre (`///`), o delimitati commenti che iniziano con una barra e due stelle (`/**`).</span><span class="sxs-lookup"><span data-stu-id="5178f-112">Such comments are single-line comments that start with three slashes (`///`), or delimited comments that start with a slash and two stars (`/**`).</span></span> <span data-ttu-id="5178f-113">Devono precedere immediatamente un tipo definito dall'utente (ad esempio, una classe, delegato o interfaccia) o un membro che si annota (ad esempio, un campo, evento, proprietà o metodo).</span><span class="sxs-lookup"><span data-stu-id="5178f-113">They must immediately precede a user-defined type (such as a class, delegate, or interface) or a member (such as a field, event, property, or method) that they annotate.</span></span> <span data-ttu-id="5178f-114">Sezioni dell'attributo ([specifica degli attributi](attributes.md#attribute-specification)) sono considerati parte di dichiarazioni, in modo che i commenti della documentazione devono precedere gli attributi applicati a un tipo o membro.</span><span class="sxs-lookup"><span data-stu-id="5178f-114">Attribute sections ([Attribute specification](attributes.md#attribute-specification)) are considered part of declarations, so documentation comments must precede attributes applied to a type or member.</span></span>

<span data-ttu-id="5178f-115">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-115">__Syntax:__</span></span>

```antlr
single_line_doc_comment
    : '///' input_character*
    ;

delimited_doc_comment
    : '/**' delimited_comment_section* asterisk+ '/'
    ;
```

<span data-ttu-id="5178f-116">In un *single_line_doc_comment*, se è presente un *uno spazio vuoto* carattere che segue il `///` caratteri in ogni la *single_line_doc_comment*s adiacenti all'oggetto corrente *single_line_doc_comment*, quindi che *uno spazio vuoto* carattere non è incluso nell'output XML.</span><span class="sxs-lookup"><span data-stu-id="5178f-116">In a *single_line_doc_comment*, if there is a *whitespace* character following the `///` characters on each of the *single_line_doc_comment*s adjacent to the current *single_line_doc_comment*, then that *whitespace* character is not included in the XML output.</span></span>

<span data-ttu-id="5178f-117">In un delimitato--commento doc, se il primo carattere diverso dallo spazio sulla seconda riga è un asterisco e lo stesso modello di caratteri di spazio vuoto facoltativo e il carattere asterisco viene ripetuta all'inizio di ogni riga del commento di doc delimitato, quindi i caratteri del modello ripetuto non sono inclusi nell'output XML.</span><span class="sxs-lookup"><span data-stu-id="5178f-117">In a delimited-doc-comment, if the first non-whitespace character on the second line is an asterisk and the same pattern of optional whitespace characters and an asterisk character is repeated at the beginning of each of the line within the delimited-doc-comment, then the characters of the repeated pattern are not included in the XML output.</span></span> <span data-ttu-id="5178f-118">Il modello può includere spazi vuoti dopo il, sia prima che il carattere asterisco.</span><span class="sxs-lookup"><span data-stu-id="5178f-118">The pattern may include whitespace characters after, as well as before, the asterisk character.</span></span>

<span data-ttu-id="5178f-119">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-119">__Example:__</span></span>

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

<span data-ttu-id="5178f-120">Il testo nei commenti della documentazione deve essere corretto in base alle regole del linguaggio XML (https://www.w3.org/TR/REC-xml).</span><span class="sxs-lookup"><span data-stu-id="5178f-120">The text within documentation comments must be well formed according to the rules of XML (https://www.w3.org/TR/REC-xml).</span></span> <span data-ttu-id="5178f-121">Se il codice XML non è valido, viene generato un avviso e il file di documentazione conterrà un commento per informare che si è verificato un errore.</span><span class="sxs-lookup"><span data-stu-id="5178f-121">If the XML is ill formed, a warning is generated and the documentation file will contain a comment saying that an error was encountered.</span></span>

<span data-ttu-id="5178f-122">Anche se gli sviluppatori sono liberi di creare il proprio set di tag, un set è definito [tag consigliati](documentation-comments.md#recommended-tags).</span><span class="sxs-lookup"><span data-stu-id="5178f-122">Although developers are free to create their own set of tags, a recommended set is defined in [Recommended tags](documentation-comments.md#recommended-tags).</span></span> <span data-ttu-id="5178f-123">Alcuni tag consigliati hanno un significato speciale:</span><span class="sxs-lookup"><span data-stu-id="5178f-123">Some of the recommended tags have special meanings:</span></span>

*  <span data-ttu-id="5178f-124">Il `<param>` tag viene usato per descrivere i parametri.</span><span class="sxs-lookup"><span data-stu-id="5178f-124">The `<param>` tag is used to describe parameters.</span></span> <span data-ttu-id="5178f-125">Se viene utilizzato un tag di questo tipo, il generatore di documentazione necessario verificare che il parametro specificato esista e che tutti i parametri sono descritte nei commenti della documentazione.</span><span class="sxs-lookup"><span data-stu-id="5178f-125">If such a tag is used, the documentation generator must verify that the specified parameter exists and that all parameters are described in documentation comments.</span></span> <span data-ttu-id="5178f-126">Se tale verifica ha esito negativo, il generatore di documentazione genera un avviso.</span><span class="sxs-lookup"><span data-stu-id="5178f-126">If such verification fails, the documentation generator issues a warning.</span></span>
*  <span data-ttu-id="5178f-127">L'attributo `cref` può essere associato a qualsiasi tag per fornire un riferimento a un elemento del codice.</span><span class="sxs-lookup"><span data-stu-id="5178f-127">The `cref` attribute can be attached to any tag to provide a reference to a code element.</span></span> <span data-ttu-id="5178f-128">Il generatore di documentazione deve verificare l'esistenza di questo elemento di codice.</span><span class="sxs-lookup"><span data-stu-id="5178f-128">The documentation generator must verify that this code element exists.</span></span> <span data-ttu-id="5178f-129">Se la verifica ha esito negativo, il generatore di documentazione genera un avviso.</span><span class="sxs-lookup"><span data-stu-id="5178f-129">If the verification fails, the documentation generator issues a warning.</span></span> <span data-ttu-id="5178f-130">Durante la ricerca di un nome descritto in una `cref` attributo, il generatore di documentazione deve rispettare la visibilità dello spazio dei nomi in base alla `using` istruzioni che si trovano all'interno del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="5178f-130">When looking for a name described in a `cref` attribute, the documentation generator must respect namespace visibility according to `using` statements appearing within the source code.</span></span> <span data-ttu-id="5178f-131">Per gli elementi di codice che sono di tipo generici, la normale sintassi generica (vale a dire, "`List<T>`") non può essere usato perché produce codice XML non valido.</span><span class="sxs-lookup"><span data-stu-id="5178f-131">For code elements that are generic, the normal generic syntax (that is, "`List<T>`") cannot be used because it produces invalid XML.</span></span> <span data-ttu-id="5178f-132">Le parentesi graffe è possibile usare invece le parentesi (vale a dire, "`List{T}`"), oppure è possibile usare la sintassi di escape XML (vale a dire, "`List&lt;T&gt;`").</span><span class="sxs-lookup"><span data-stu-id="5178f-132">Braces can be used instead of brackets (that is, "`List{T}`"), or the XML escape syntax can be used (that is, "`List&lt;T&gt;`").</span></span>
*  <span data-ttu-id="5178f-133">Il `<summary>` tag dovrà essere utilizzato da un visualizzatore della documentazione per visualizzare informazioni aggiuntive su un tipo o membro.</span><span class="sxs-lookup"><span data-stu-id="5178f-133">The `<summary>` tag is intended to be used by a documentation viewer to display additional information about a type or member.</span></span>
*  <span data-ttu-id="5178f-134">Il `<include>` tag include le informazioni da un file XML esterno.</span><span class="sxs-lookup"><span data-stu-id="5178f-134">The `<include>` tag includes information from an external XML file.</span></span>

<span data-ttu-id="5178f-135">Notare che il file di documentazione non fornisce informazioni complete sul tipo e i membri (ad esempio, non contiene alcuna informazione sul tipo).</span><span class="sxs-lookup"><span data-stu-id="5178f-135">Note carefully that the documentation file does not provide full information about the type and members (for example, it does not contain any type information).</span></span> <span data-ttu-id="5178f-136">Per ottenere tali informazioni su un tipo o membro, il file della documentazione deve essere usato in combinazione con reflection sul tipo effettivo o sul membro.</span><span class="sxs-lookup"><span data-stu-id="5178f-136">To get such information about a type or member, the documentation file must be used in conjunction with reflection on the actual type or member.</span></span>

## <a name="recommended-tags"></a><span data-ttu-id="5178f-137">Tag consigliati</span><span class="sxs-lookup"><span data-stu-id="5178f-137">Recommended tags</span></span>

<span data-ttu-id="5178f-138">Il generatore di documentazione deve accettare ed elaborare qualsiasi tag valido secondo le regole del linguaggio XML.</span><span class="sxs-lookup"><span data-stu-id="5178f-138">The documentation generator must accept and process any tag that is valid according to the rules of XML.</span></span> <span data-ttu-id="5178f-139">I tag seguenti forniscono le funzionalità comunemente utilizzate nella documentazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="5178f-139">The following tags provide commonly used functionality in user documentation.</span></span> <span data-ttu-id="5178f-140">(Naturalmente, gli altri tag sono possibili).</span><span class="sxs-lookup"><span data-stu-id="5178f-140">(Of course, other tags are possible.)</span></span>


| <span data-ttu-id="5178f-141">__Tag__</span><span class="sxs-lookup"><span data-stu-id="5178f-141">__Tag__</span></span>          | <span data-ttu-id="5178f-142">__Sezione__</span><span class="sxs-lookup"><span data-stu-id="5178f-142">__Section__</span></span>                                            | <span data-ttu-id="5178f-143">__Scopo__</span><span class="sxs-lookup"><span data-stu-id="5178f-143">__Purpose__</span></span>                                            |
|------------------|--------------------------------------------------------|--------------------------------------------------------|
| `<c>`            | [`<c>`](documentation-comments.md#c)                   | <span data-ttu-id="5178f-144">Impostare il testo in un tipo di carattere del codice simile a</span><span class="sxs-lookup"><span data-stu-id="5178f-144">Set text in a code-like font</span></span>                           | 
| `<code>`         | [`<code>`](documentation-comments.md#code)             | <span data-ttu-id="5178f-145">Impostare uno o più righe di output di codice o il programma di origine</span><span class="sxs-lookup"><span data-stu-id="5178f-145">Set one or more lines of source code or program output</span></span> |
| `<example>`      | [`<example>`](documentation-comments.md#example)       | <span data-ttu-id="5178f-146">Indicare un esempio</span><span class="sxs-lookup"><span data-stu-id="5178f-146">Indicate an example</span></span>                                    |
| `<exception>`    | [`<exception>`](documentation-comments.md#exception)   | <span data-ttu-id="5178f-147">Identifica un metodo può generare eccezioni</span><span class="sxs-lookup"><span data-stu-id="5178f-147">Identifies the exceptions a method can throw</span></span>           |
| `<include>`      | [`<include>`](documentation-comments.md#include)       | <span data-ttu-id="5178f-148">Include XML da un file esterno</span><span class="sxs-lookup"><span data-stu-id="5178f-148">Includes XML from an external file</span></span>                     |
| `<list>`         | [`<list>`](documentation-comments.md#list)             | <span data-ttu-id="5178f-149">Creare un elenco o una tabella</span><span class="sxs-lookup"><span data-stu-id="5178f-149">Create a list or table</span></span>                                 |
| `<para>`         | [`<para>`](documentation-comments.md#para)             | <span data-ttu-id="5178f-150">Consentire l'aggiunta al testo di una struttura</span><span class="sxs-lookup"><span data-stu-id="5178f-150">Permit structure to be added to text</span></span>                   |
| `<param>`        | [`<param>`](documentation-comments.md#param)           | <span data-ttu-id="5178f-151">Descrivere un parametro per un metodo o costruttore</span><span class="sxs-lookup"><span data-stu-id="5178f-151">Describe a parameter for a method or constructor</span></span>       |
| `<paramref>`     | [`<paramref>`](documentation-comments.md#paramref)     | <span data-ttu-id="5178f-152">Stabilire se una parola è un nome di parametro</span><span class="sxs-lookup"><span data-stu-id="5178f-152">Identify that a word is a parameter name</span></span>               |
| `<permission>`   | [`<permission>`](documentation-comments.md#permission) | <span data-ttu-id="5178f-153">L'accessibilità di sicurezza di un membro del documento</span><span class="sxs-lookup"><span data-stu-id="5178f-153">Document the security accessibility of a member</span></span>        |
| `<remark>`       | [`<remark>`](documentation-comments.md#remark)         | <span data-ttu-id="5178f-154">Vengono descritti informazioni aggiuntive su un tipo</span><span class="sxs-lookup"><span data-stu-id="5178f-154">Describe additional information about a type</span></span>           |
| `<returns>`      | [`<returns>`](documentation-comments.md#returns)       | <span data-ttu-id="5178f-155">Descrivere il valore restituito di un metodo</span><span class="sxs-lookup"><span data-stu-id="5178f-155">Describe the return value of a method</span></span>                  |
| `<see>`          | [`<see>`](documentation-comments.md#see)               | <span data-ttu-id="5178f-156">Specificare un collegamento</span><span class="sxs-lookup"><span data-stu-id="5178f-156">Specify a link</span></span>                                         |
| `<seealso>`      | [`<seealso>`](documentation-comments.md#seealso)       | <span data-ttu-id="5178f-157">Generare una voce di vedere anche</span><span class="sxs-lookup"><span data-stu-id="5178f-157">Generate a See Also entry</span></span>                              |
| `<summary>`      | [`<summary>`](documentation-comments.md#summary)       | <span data-ttu-id="5178f-158">Descrivere un tipo o un membro di un tipo</span><span class="sxs-lookup"><span data-stu-id="5178f-158">Describe a type or a member of a type</span></span>                  |
| `<value>`        | [`<value>`](documentation-comments.md#value)           | <span data-ttu-id="5178f-159">Descrivere una proprietà</span><span class="sxs-lookup"><span data-stu-id="5178f-159">Describe a property</span></span>                                    |
| `<typeparam>`    |                                                        | <span data-ttu-id="5178f-160">Descrivere un parametro di tipo generico</span><span class="sxs-lookup"><span data-stu-id="5178f-160">Describe a generic type parameter</span></span>                      |
| `<typeparamref>` |                                                        | <span data-ttu-id="5178f-161">Stabilire se una parola è un nome di parametro di tipo</span><span class="sxs-lookup"><span data-stu-id="5178f-161">Identify that a word is a type parameter name</span></span>          |

### `<c>`

<span data-ttu-id="5178f-162">Questo tag fornisce un meccanismo per indicare che un frammento di testo all'interno di una descrizione deve essere impostato in un carattere speciale come quello usato per un blocco di codice.</span><span class="sxs-lookup"><span data-stu-id="5178f-162">This tag provides a mechanism to indicate that a fragment of text within a description should be set in a special font such as that used for a block of code.</span></span> <span data-ttu-id="5178f-163">Per le righe di codice effettivo, utilizzare `<code>` ([`<code>`](documentation-comments.md#code)).</span><span class="sxs-lookup"><span data-stu-id="5178f-163">For lines of actual code, use `<code>` ([`<code>`](documentation-comments.md#code)).</span></span>

<span data-ttu-id="5178f-164">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-164">__Syntax:__</span></span>

```xml
<c>text</c>
```

<span data-ttu-id="5178f-165">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-165">__Example:__</span></span>

```csharp
/// <summary>Class <c>Point</c> models a point in a two-dimensional
/// plane.</summary>

public class Point 
{
    // ...
}
```

### `<code>`

<span data-ttu-id="5178f-166">Questo tag viene usato per impostare uno o più righe di output di codice o il programma di origine in un carattere speciale.</span><span class="sxs-lookup"><span data-stu-id="5178f-166">This tag is used to set one or more lines of source code or program output in some special font.</span></span> <span data-ttu-id="5178f-167">Per i piccoli frammenti di codice in testo descrittivo, utilizzare `<c>` ([`<c>`](documentation-comments.md#c)).</span><span class="sxs-lookup"><span data-stu-id="5178f-167">For small code fragments in narrative, use `<c>` ([`<c>`](documentation-comments.md#c)).</span></span>

<span data-ttu-id="5178f-168">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-168">__Syntax:__</span></span>

```xml
<code>source code or program output</code>
```

<span data-ttu-id="5178f-169">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-169">__Example:__</span></span>

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

<span data-ttu-id="5178f-170">Questo tag consente al codice di esempio all'interno di un commento, per specificare la modalità può essere utilizzato un metodo o altro membro della raccolta.</span><span class="sxs-lookup"><span data-stu-id="5178f-170">This tag allows example code within a comment, to specify how a method or other library member may be used.</span></span> <span data-ttu-id="5178f-171">In genere, ciò anche comporta l'uso del tag `<code>` ([`<code>`](documentation-comments.md#code)) nonché.</span><span class="sxs-lookup"><span data-stu-id="5178f-171">Ordinarily, this would also involve use of the tag `<code>` ([`<code>`](documentation-comments.md#code)) as well.</span></span>

<span data-ttu-id="5178f-172">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-172">__Syntax:__</span></span>

```xml
<example>description</example>
```

<span data-ttu-id="5178f-173">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-173">__Example:__</span></span>

<span data-ttu-id="5178f-174">Visualizzare `<code>` ([`<code>`](documentation-comments.md#code)) per un esempio.</span><span class="sxs-lookup"><span data-stu-id="5178f-174">See `<code>` ([`<code>`](documentation-comments.md#code)) for an example.</span></span>

### `<exception>`

<span data-ttu-id="5178f-175">Questo tag consente di documentare le eccezioni che può generare un metodo.</span><span class="sxs-lookup"><span data-stu-id="5178f-175">This tag provides a way to document the exceptions a method can throw.</span></span>

<span data-ttu-id="5178f-176">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-176">__Syntax:__</span></span>

```xml
<exception cref="member">description</exception>
```

<span data-ttu-id="5178f-177">dove</span><span class="sxs-lookup"><span data-stu-id="5178f-177">where</span></span>

* <span data-ttu-id="5178f-178">`member` è il nome di un membro.</span><span class="sxs-lookup"><span data-stu-id="5178f-178">`member` is the name of a member.</span></span> <span data-ttu-id="5178f-179">Il generatore di documentazione controlla che il membro specificato esista e converte `member` al nome canonico dell'elemento nel file di documentazione.</span><span class="sxs-lookup"><span data-stu-id="5178f-179">The documentation generator checks that the given member exists and translates `member` to the canonical element name in the documentation file.</span></span>
* <span data-ttu-id="5178f-180">`description` è una descrizione delle circostanze in cui viene generata l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="5178f-180">`description` is a description of the circumstances in which the exception is thrown.</span></span>

<span data-ttu-id="5178f-181">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-181">__Example:__</span></span>

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

<span data-ttu-id="5178f-182">Questo tag consente anche dal documento XML che è esterno al file del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="5178f-182">This tag allows including information from an XML document that is external to the source code file.</span></span> <span data-ttu-id="5178f-183">Il file esterno deve essere un documento XML ben formato e un'espressione XPath viene applicata a un documento per specificare quali XML da tale documento da includere.</span><span class="sxs-lookup"><span data-stu-id="5178f-183">The external file must be a well-formed XML document, and an XPath expression is applied to that document to specify what XML from that document to include.</span></span> <span data-ttu-id="5178f-184">Il `<include>` tag verrà quindi sostituito con le informazioni XML del documento esterna.</span><span class="sxs-lookup"><span data-stu-id="5178f-184">The `<include>` tag is then replaced with the selected XML from the external document.</span></span>

<span data-ttu-id="5178f-185">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-185">__Syntax:__</span></span>

```
<include file="filename" path="xpath" />
```

<span data-ttu-id="5178f-186">dove</span><span class="sxs-lookup"><span data-stu-id="5178f-186">where</span></span>

* <span data-ttu-id="5178f-187">`filename` è il nome del file di un file XML esterno.</span><span class="sxs-lookup"><span data-stu-id="5178f-187">`filename` is the file name of an external XML file.</span></span> <span data-ttu-id="5178f-188">Viene interpretato il nome del file relativo al file che contiene il tag di inclusione.</span><span class="sxs-lookup"><span data-stu-id="5178f-188">The file name is interpreted relative to the file that contains the include tag.</span></span>
* <span data-ttu-id="5178f-189">`xpath` è un'espressione XPath che seleziona alcune XML nel file XML esterno.</span><span class="sxs-lookup"><span data-stu-id="5178f-189">`xpath` is an XPath expression that selects some of the XML in the external XML file.</span></span>

<span data-ttu-id="5178f-190">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-190">__Example:__</span></span>

<span data-ttu-id="5178f-191">Se il codice sorgente contiene una dichiarazione, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5178f-191">If the source code contained a declaration like:</span></span>

```csharp
/// <include file="docs.xml" path='extradoc/class[@name="IntList"]/*' />
public class IntList { ... }
```

<span data-ttu-id="5178f-192">il file esterno "docs. XML" sia stato soddisfacente e il contenuto seguente:</span><span class="sxs-lookup"><span data-stu-id="5178f-192">and the external file "docs.xml" had the following contents:</span></span>

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

<span data-ttu-id="5178f-193">quindi la documentazione stessa è output come se il codice sorgente contenuto:</span><span class="sxs-lookup"><span data-stu-id="5178f-193">then the same documentation is output as if the source code contained:</span></span>

```csharp
/// <summary>
///    Contains a list of integers.
/// </summary>
public class IntList { ... }
```

### `<list>`

<span data-ttu-id="5178f-194">Questo tag viene usato per creare un elenco o una tabella di elementi.</span><span class="sxs-lookup"><span data-stu-id="5178f-194">This tag is used to create a list or table of items.</span></span> <span data-ttu-id="5178f-195">Può contenere un `<listheader>` blocco per definire la riga di intestazione della tabella o una definizione elenco.</span><span class="sxs-lookup"><span data-stu-id="5178f-195">It may contain a `<listheader>` block to define the heading row of either a table or definition list.</span></span> <span data-ttu-id="5178f-196">(Quando si definisce una tabella, solo una voce per `term` nell'intestazione devono essere fornite.)</span><span class="sxs-lookup"><span data-stu-id="5178f-196">(When defining a table, only an entry for `term` in the heading need be supplied.)</span></span>

<span data-ttu-id="5178f-197">Ogni elemento nell'elenco viene specificato con un `<item>` blocco.</span><span class="sxs-lookup"><span data-stu-id="5178f-197">Each item in the list is specified with an `<item>` block.</span></span> <span data-ttu-id="5178f-198">Quando si crea un elenco di definizioni, entrambe `term` e `description` deve essere specificato.</span><span class="sxs-lookup"><span data-stu-id="5178f-198">When creating a definition list, both `term` and `description` must be specified.</span></span> <span data-ttu-id="5178f-199">Tuttavia, per una tabella, elenco puntato o numerato, solo `description` è necessario specificarlo.</span><span class="sxs-lookup"><span data-stu-id="5178f-199">However, for a table, bulleted list, or numbered list, only `description` need be specified.</span></span>

<span data-ttu-id="5178f-200">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-200">__Syntax:__</span></span>

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

<span data-ttu-id="5178f-201">dove</span><span class="sxs-lookup"><span data-stu-id="5178f-201">where</span></span>

* <span data-ttu-id="5178f-202">`term` è il termine da definire, la cui definizione è `description`.</span><span class="sxs-lookup"><span data-stu-id="5178f-202">`term` is the term to define, whose definition is in `description`.</span></span>
* <span data-ttu-id="5178f-203">`description` è un elemento in un elenco puntato o numerato oppure la definizione di un `term`.</span><span class="sxs-lookup"><span data-stu-id="5178f-203">`description` is either an item in a bullet or numbered list, or the definition of a `term`.</span></span>

<span data-ttu-id="5178f-204">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-204">__Example:__</span></span>

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

<span data-ttu-id="5178f-205">Questo tag è utilizzato all'interno di altri tag, ad esempio `<summary>` ([`<remark>`](documentation-comments.md#remark)) o `<returns>` ([`<returns>`](documentation-comments.md#returns)) e consente una struttura da aggiungere al testo.</span><span class="sxs-lookup"><span data-stu-id="5178f-205">This tag is for use inside other tags, such as `<summary>` ([`<remark>`](documentation-comments.md#remark)) or `<returns>` ([`<returns>`](documentation-comments.md#returns)), and permits structure to be added to text.</span></span>

<span data-ttu-id="5178f-206">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-206">__Syntax:__</span></span>

```xml
<para>content</para>
```

<span data-ttu-id="5178f-207">in cui `content` è il testo del paragrafo.</span><span class="sxs-lookup"><span data-stu-id="5178f-207">where `content` is the text of the paragraph.</span></span>

<span data-ttu-id="5178f-208">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-208">__Example:__</span></span>

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

<span data-ttu-id="5178f-209">Questo tag viene usato per descrivere un parametro per un metodo, un costruttore o un indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="5178f-209">This tag is used to describe a parameter for a method, constructor, or indexer.</span></span>

<span data-ttu-id="5178f-210">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-210">__Syntax:__</span></span>

```xml
<param name="name">description</param>
```

<span data-ttu-id="5178f-211">dove</span><span class="sxs-lookup"><span data-stu-id="5178f-211">where</span></span>

* <span data-ttu-id="5178f-212">`name` È il nome del parametro.</span><span class="sxs-lookup"><span data-stu-id="5178f-212">`name` is the name of the parameter.</span></span>
* <span data-ttu-id="5178f-213">`description` è una descrizione del parametro.</span><span class="sxs-lookup"><span data-stu-id="5178f-213">`description` is a description of the parameter.</span></span>

<span data-ttu-id="5178f-214">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-214">__Example:__</span></span>

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

<span data-ttu-id="5178f-215">Questo tag viene usato per indicare che una parola è un parametro.</span><span class="sxs-lookup"><span data-stu-id="5178f-215">This tag is used to indicate that a word is a parameter.</span></span> <span data-ttu-id="5178f-216">Il file di documentazione può essere elaborato per formattare questo parametro in modo specifico.</span><span class="sxs-lookup"><span data-stu-id="5178f-216">The documentation file can be processed to format this parameter in some distinct way.</span></span>

<span data-ttu-id="5178f-217">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-217">__Syntax:__</span></span>

```xml
<paramref name="name"/>
```

<span data-ttu-id="5178f-218">in cui `name` è il nome del parametro.</span><span class="sxs-lookup"><span data-stu-id="5178f-218">where `name` is the name of the parameter.</span></span>

<span data-ttu-id="5178f-219">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-219">__Example:__</span></span>

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

<span data-ttu-id="5178f-220">Questo tag consente l'accessibilità di sicurezza di un membro da documentare.</span><span class="sxs-lookup"><span data-stu-id="5178f-220">This tag allows the security accessibility of a member to be documented.</span></span>

<span data-ttu-id="5178f-221">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-221">__Syntax:__</span></span>

```xml
<permission cref="member">description</permission>
```

<span data-ttu-id="5178f-222">dove</span><span class="sxs-lookup"><span data-stu-id="5178f-222">where</span></span>

* <span data-ttu-id="5178f-223">`member` è il nome di un membro.</span><span class="sxs-lookup"><span data-stu-id="5178f-223">`member` is the name of a member.</span></span> <span data-ttu-id="5178f-224">Il generatore di documentazione controlla che l'elemento di codice specificato esista e converte *membro* al nome canonico dell'elemento nel file di documentazione.</span><span class="sxs-lookup"><span data-stu-id="5178f-224">The documentation generator checks that the given code element exists and translates *member* to the canonical element name in the documentation file.</span></span>
* <span data-ttu-id="5178f-225">`description` è una descrizione dell'accesso al membro.</span><span class="sxs-lookup"><span data-stu-id="5178f-225">`description` is a description of the access to the member.</span></span>

<span data-ttu-id="5178f-226">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-226">__Example:__</span></span>

```csharp
/// <permission cref="System.Security.PermissionSet">Everyone can
/// access this method.</permission>

public static void Test() {
    // ...
}
```

### `<remark>`

<span data-ttu-id="5178f-227">Questo tag viene usato per specificare informazioni aggiuntive su un tipo.</span><span class="sxs-lookup"><span data-stu-id="5178f-227">This tag is used to specify extra information about a type.</span></span> <span data-ttu-id="5178f-228">(Usare `<summary>` ([`<summary>`](documentation-comments.md#summary)) per descrivere il tipo stesso e i membri di un tipo.)</span><span class="sxs-lookup"><span data-stu-id="5178f-228">(Use `<summary>` ([`<summary>`](documentation-comments.md#summary)) to describe the type itself and the members of a type.)</span></span>

<span data-ttu-id="5178f-229">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-229">__Syntax:__</span></span>

```xml
<remark>description</remark>
```

<span data-ttu-id="5178f-230">in cui `description` è il testo delle osservazioni.</span><span class="sxs-lookup"><span data-stu-id="5178f-230">where `description` is the text of the remark.</span></span>

<span data-ttu-id="5178f-231">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-231">__Example:__</span></span>

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

<span data-ttu-id="5178f-232">Questo tag viene usato per descrivere il valore restituito di un metodo.</span><span class="sxs-lookup"><span data-stu-id="5178f-232">This tag is used to describe the return value of a method.</span></span>

<span data-ttu-id="5178f-233">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-233">__Syntax:__</span></span>

```xml
<returns>description</returns>
```

<span data-ttu-id="5178f-234">in cui `description` è una descrizione del valore restituito.</span><span class="sxs-lookup"><span data-stu-id="5178f-234">where `description` is a description of the return value.</span></span>

<span data-ttu-id="5178f-235">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-235">__Example:__</span></span>

```csharp
/// <summary>Report a point's location as a string.</summary>
/// <returns>A string representing a point's location, in the form (x,y),
///    without any leading, trailing, or embedded whitespace.</returns>
public override string ToString() {
    return "(" + X + "," + Y + ")";
}
```

### `<see>`

<span data-ttu-id="5178f-236">Questo tag consente di inserire un collegamento a essere specificata all'interno del testo.</span><span class="sxs-lookup"><span data-stu-id="5178f-236">This tag allows a link to be specified within text.</span></span> <span data-ttu-id="5178f-237">Uso `<seealso>` ([`<seealso>`](documentation-comments.md#seealso)) per indicare il testo che deve essere visualizzato in una sezione Vedere anche.</span><span class="sxs-lookup"><span data-stu-id="5178f-237">Use `<seealso>` ([`<seealso>`](documentation-comments.md#seealso)) to indicate text that is to appear in a See Also section.</span></span>

<span data-ttu-id="5178f-238">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-238">__Syntax:__</span></span>

```xml
<see cref="member"/>
```

<span data-ttu-id="5178f-239">in cui `member` è il nome del membro.</span><span class="sxs-lookup"><span data-stu-id="5178f-239">where `member` is the name of a member.</span></span> <span data-ttu-id="5178f-240">Il generatore di documentazione controlla che l'elemento di codice specificato esista e che viene modificato *membro* al nome dell'elemento nel file di documentazione.</span><span class="sxs-lookup"><span data-stu-id="5178f-240">The documentation generator checks that the given code element exists and changes *member* to the element name in the generated documentation file.</span></span>

<span data-ttu-id="5178f-241">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-241">__Example:__</span></span>

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

<span data-ttu-id="5178f-242">Questo tag consente una voce da generare per la sezione Vedere anche.</span><span class="sxs-lookup"><span data-stu-id="5178f-242">This tag allows an entry to be generated for the See Also section.</span></span> <span data-ttu-id="5178f-243">Uso `<see>` ([`<see>`](documentation-comments.md#see)) per specificare un collegamento nel testo.</span><span class="sxs-lookup"><span data-stu-id="5178f-243">Use `<see>` ([`<see>`](documentation-comments.md#see)) to specify a link from within text.</span></span>

<span data-ttu-id="5178f-244">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-244">__Syntax:__</span></span>

```xml
<seealso cref="member"/>
```

<span data-ttu-id="5178f-245">in cui `member` è il nome del membro.</span><span class="sxs-lookup"><span data-stu-id="5178f-245">where `member` is the name of a member.</span></span> <span data-ttu-id="5178f-246">Il generatore di documentazione controlla che l'elemento di codice specificato esista e che viene modificato *membro* al nome dell'elemento nel file di documentazione.</span><span class="sxs-lookup"><span data-stu-id="5178f-246">The documentation generator checks that the given code element exists and changes *member* to the element name in the generated documentation file.</span></span>

<span data-ttu-id="5178f-247">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-247">__Example:__</span></span>

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

Questo tag è utilizzabile per descrivere un tipo o un membro di un tipo. <span data-ttu-id="5178f-249">Uso `<remark>` ([`<remark>`](documentation-comments.md#remark)) per descrivere il tipo stesso.</span><span class="sxs-lookup"><span data-stu-id="5178f-249">Use `<remark>` ([`<remark>`](documentation-comments.md#remark)) to describe the type itself.</span></span>

<span data-ttu-id="5178f-250">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-250">__Syntax:__</span></span>

```xml
<summary>description</summary>
```

<span data-ttu-id="5178f-251">in cui `description` è riportato un riepilogo del tipo o membro.</span><span class="sxs-lookup"><span data-stu-id="5178f-251">where `description` is a summary of the type or member.</span></span>

<span data-ttu-id="5178f-252">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-252">__Example:__</span></span>

```csharp
/// <summary>This constructor initializes the new Point to (0,0).</summary>
public Point() : this(0,0) {
}
```

### `<value>`

<span data-ttu-id="5178f-253">Questo tag consente di descrivere una proprietà.</span><span class="sxs-lookup"><span data-stu-id="5178f-253">This tag allows a property to be described.</span></span>

<span data-ttu-id="5178f-254">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-254">__Syntax:__</span></span>

```xml
<value>property description</value>
```

<span data-ttu-id="5178f-255">in cui `property description` è una descrizione per la proprietà.</span><span class="sxs-lookup"><span data-stu-id="5178f-255">where `property description` is a description for the property.</span></span>

<span data-ttu-id="5178f-256">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-256">__Example:__</span></span>

```csharp
/// <value>Property <c>X</c> represents the point's x-coordinate.</value>
public int X
{
    get { return x; }
    set { x = value; }
}
```

### `<typeparam>`

<span data-ttu-id="5178f-257">Questo tag viene usato per descrivere un parametro di tipo generico per una classe, struct, interfaccia, delegato o metodo.</span><span class="sxs-lookup"><span data-stu-id="5178f-257">This tag is used to describe a generic type parameter for a class, struct, interface, delegate, or method.</span></span>

<span data-ttu-id="5178f-258">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-258">__Syntax:__</span></span>

```xml
<typeparam name="name">description</typeparam>
```

<span data-ttu-id="5178f-259">in cui `name` è il nome del parametro di tipo, e `description` è la relativa descrizione.</span><span class="sxs-lookup"><span data-stu-id="5178f-259">where `name` is the name of the type parameter, and `description` is its description.</span></span>

<span data-ttu-id="5178f-260">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-260">__Example:__</span></span>

```csharp
/// <summary>A generic list class.</summary>
/// <typeparam name="T">The type stored by the list.</typeparam>
public class MyList<T> {
    ...
}
```

### `<typeparamref>`

<span data-ttu-id="5178f-261">Questo tag viene usato per indicare che una parola è un parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="5178f-261">This tag is used to indicate that a word is a type parameter.</span></span> <span data-ttu-id="5178f-262">Il file di documentazione può essere elaborato per formattare questo parametro di tipo in modo specifico.</span><span class="sxs-lookup"><span data-stu-id="5178f-262">The documentation file can be processed to format this type parameter in some distinct way.</span></span>

<span data-ttu-id="5178f-263">__Sintassi:__</span><span class="sxs-lookup"><span data-stu-id="5178f-263">__Syntax:__</span></span>

```xml
<typeparamref name="name"/>
```

<span data-ttu-id="5178f-264">in cui `name` è il nome del parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="5178f-264">where `name` is the name of the type parameter.</span></span>

<span data-ttu-id="5178f-265">__Esempio:__</span><span class="sxs-lookup"><span data-stu-id="5178f-265">__Example:__</span></span>

```csharp
/// <summary>This method fetches data and returns a list of <typeparamref name="T"/>.</summary>
/// <param name="query">query to execute</param>
public List<T> FetchData<T>(string query) {
    ...
}
```

## <a name="processing-the-documentation-file"></a><span data-ttu-id="5178f-266">L'elaborazione del file di documentazione</span><span class="sxs-lookup"><span data-stu-id="5178f-266">Processing the documentation file</span></span>

<span data-ttu-id="5178f-267">Il generatore di documentazione genera una stringa ID per ogni elemento nel codice sorgente che è contrassegnato con un commento della documentazione.</span><span class="sxs-lookup"><span data-stu-id="5178f-267">The documentation generator generates an ID string for each element in the source code that is tagged with a documentation comment.</span></span> <span data-ttu-id="5178f-268">Questa stringa ID identifica in modo univoco un elemento di origine.</span><span class="sxs-lookup"><span data-stu-id="5178f-268">This ID string uniquely identifies a source element.</span></span> <span data-ttu-id="5178f-269">Un visualizzatore della documentazione è possibile usare una stringa ID per identificare il corrispondente elemento metadati/reflection a cui si applica la documentazione.</span><span class="sxs-lookup"><span data-stu-id="5178f-269">A documentation viewer can use an ID string to identify the corresponding metadata/reflection item to which the documentation applies.</span></span>

<span data-ttu-id="5178f-270">Il file di documentazione non è una rappresentazione gerarchica del codice sorgente. piuttosto, è un elenco semplice con una stringa ID generato per ogni elemento.</span><span class="sxs-lookup"><span data-stu-id="5178f-270">The documentation file is not a hierarchical representation of the source code; rather, it is a flat list with a generated ID string for each element.</span></span>

### <a name="id-string-format"></a><span data-ttu-id="5178f-271">Formato della stringa ID</span><span class="sxs-lookup"><span data-stu-id="5178f-271">ID string format</span></span>

<span data-ttu-id="5178f-272">Quando genera le stringhe di ID, il generatore di documentazione osserva le regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="5178f-272">The documentation generator observes the following rules when it generates the ID strings:</span></span>

*  <span data-ttu-id="5178f-273">Assenza di spazi vuoti nella stringa.</span><span class="sxs-lookup"><span data-stu-id="5178f-273">No white space is placed in the string.</span></span>

*  <span data-ttu-id="5178f-274">La prima parte della stringa identifica il tipo di membro in corso documentati, applicato tramite un singolo carattere seguito da due punti.</span><span class="sxs-lookup"><span data-stu-id="5178f-274">The first part of the string identifies the kind of member being documented, via a single character followed by a colon.</span></span> <span data-ttu-id="5178f-275">Sono definiti i tipi di membri seguenti:</span><span class="sxs-lookup"><span data-stu-id="5178f-275">The following kinds of members are defined:</span></span>

   | <span data-ttu-id="5178f-276">__Carattere__</span><span class="sxs-lookup"><span data-stu-id="5178f-276">__Character__</span></span> | <span data-ttu-id="5178f-277">__Descrizione__</span><span class="sxs-lookup"><span data-stu-id="5178f-277">__Description__</span></span>                                             |
   |---------------|-------------------------------------------------------------|
   | <span data-ttu-id="5178f-278">E</span><span class="sxs-lookup"><span data-stu-id="5178f-278">E</span></span>             | <span data-ttu-id="5178f-279">event</span><span class="sxs-lookup"><span data-stu-id="5178f-279">Event</span></span>                                                       |
   | <span data-ttu-id="5178f-280">F</span><span class="sxs-lookup"><span data-stu-id="5178f-280">F</span></span>             | <span data-ttu-id="5178f-281">Campo</span><span class="sxs-lookup"><span data-stu-id="5178f-281">Field</span></span>                                                       |
   | <span data-ttu-id="5178f-282">M</span><span class="sxs-lookup"><span data-stu-id="5178f-282">M</span></span>             | <span data-ttu-id="5178f-283">Metodo (compresi i costruttori, distruttori e operatori)</span><span class="sxs-lookup"><span data-stu-id="5178f-283">Method (including constructors, destructors, and operators)</span></span> |
   | <span data-ttu-id="5178f-284">N</span><span class="sxs-lookup"><span data-stu-id="5178f-284">N</span></span>             | <span data-ttu-id="5178f-285">Spazio dei nomi</span><span class="sxs-lookup"><span data-stu-id="5178f-285">Namespace</span></span>                                                   |
   | <span data-ttu-id="5178f-286">P</span><span class="sxs-lookup"><span data-stu-id="5178f-286">P</span></span>             | <span data-ttu-id="5178f-287">Proprietà (compresi gli indicizzatori)</span><span class="sxs-lookup"><span data-stu-id="5178f-287">Property (including indexers)</span></span>                               |
   | <span data-ttu-id="5178f-288">T</span><span class="sxs-lookup"><span data-stu-id="5178f-288">T</span></span>             | <span data-ttu-id="5178f-289">Tipo (ad esempio classi, delegato, enum, interfaccia e struct)</span><span class="sxs-lookup"><span data-stu-id="5178f-289">Type (such as class, delegate, enum, interface, and struct)</span></span> |
   | <span data-ttu-id="5178f-290">!</span><span class="sxs-lookup"><span data-stu-id="5178f-290">!</span></span>             | <span data-ttu-id="5178f-291">Stringa di errore. il resto della stringa contiene informazioni sull'errore.</span><span class="sxs-lookup"><span data-stu-id="5178f-291">Error string; the rest of the string provides information about the error.</span></span> <span data-ttu-id="5178f-292">Ad esempio, il generatore di documentazione genera informazioni di errore per i collegamenti che non possono essere risolto.</span><span class="sxs-lookup"><span data-stu-id="5178f-292">For example, the documentation generator generates error information for links that cannot be resolved.</span></span> |

*  <span data-ttu-id="5178f-293">La seconda parte della stringa è il nome completo dell'elemento, iniziando dalla radice dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="5178f-293">The second part of the string is the fully qualified name of the element, starting at the root of the namespace.</span></span> <span data-ttu-id="5178f-294">Il nome dell'elemento, relativa inclusione i tipi e lo spazio dei nomi sono separati da punti.</span><span class="sxs-lookup"><span data-stu-id="5178f-294">The name of the element, its enclosing type(s), and namespace are separated by periods.</span></span> <span data-ttu-id="5178f-295">Se il nome dell'elemento stesso contiene punti, vengono sostituiti con `#(U+0023)` caratteri.</span><span class="sxs-lookup"><span data-stu-id="5178f-295">If the name of the item itself has periods, they are replaced by `#(U+0023)` characters.</span></span> <span data-ttu-id="5178f-296">(Si presuppone che nessun elemento dispone di tale carattere nel nome.)</span><span class="sxs-lookup"><span data-stu-id="5178f-296">(It is assumed that no element has this character in its name.)</span></span>
*  <span data-ttu-id="5178f-297">Per i metodi e proprietà con argomenti, l'elenco degli argomenti nel seguente modo, racchiuso tra parentesi.</span><span class="sxs-lookup"><span data-stu-id="5178f-297">For methods and properties with arguments, the argument list follows, enclosed in parentheses.</span></span> <span data-ttu-id="5178f-298">Per coloro che senza argomenti, le parentesi vengono omesse.</span><span class="sxs-lookup"><span data-stu-id="5178f-298">For those without arguments, the parentheses are omitted.</span></span> <span data-ttu-id="5178f-299">Gli argomenti sono separati da virgole.</span><span class="sxs-lookup"><span data-stu-id="5178f-299">The arguments are separated by commas.</span></span> <span data-ttu-id="5178f-300">La codifica di ciascun argomento è lo stesso come firma dell'interfaccia della riga, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="5178f-300">The encoding of each argument is the same as a CLI signature, as follows:</span></span>
   *  <span data-ttu-id="5178f-301">Gli argomenti sono rappresentati dal relativo nome di documentazione che si basa sul nome completo, modificato come segue:</span><span class="sxs-lookup"><span data-stu-id="5178f-301">Arguments are represented by their documentation name, which is based on their fully qualified name, modified as follows:</span></span>
      * <span data-ttu-id="5178f-302">Gli argomenti che rappresentano i tipi generici hanno un'aggiunta `` ` `` carattere (apice inverso) seguito dal numero di parametri di tipo</span><span class="sxs-lookup"><span data-stu-id="5178f-302">Arguments that represent generic types have an appended `` ` `` (backtick) character followed by the number of type parameters</span></span>
      * <span data-ttu-id="5178f-303">Negli argomenti con la `out` oppure `ref` modificatore hanno un `@` dopo il nome del tipo.</span><span class="sxs-lookup"><span data-stu-id="5178f-303">Arguments having the `out` or `ref` modifier have an `@` following their type name.</span></span> <span data-ttu-id="5178f-304">Gli argomenti passati per valore o mediante `params` non hanno una speciale notazione.</span><span class="sxs-lookup"><span data-stu-id="5178f-304">Arguments passed by value or via `params` have no special notation.</span></span>
      * <span data-ttu-id="5178f-305">Gli argomenti di matrici vengono rappresentati come `[lowerbound:size, ... , lowerbound:size]` in cui il numero di virgole indica il numero di dimensioni meno uno e i limiti inferiore e dimensioni di ogni dimensione, se è noto, sono rappresentati in formato decimale.</span><span class="sxs-lookup"><span data-stu-id="5178f-305">Arguments that are arrays are represented as `[lowerbound:size, ... , lowerbound:size]` where the number of commas is the rank less one, and the lower bounds and size of each dimension, if known, are represented in decimal.</span></span> <span data-ttu-id="5178f-306">Se non vengano specificata un limite inferiore o dimensioni, viene omesso.</span><span class="sxs-lookup"><span data-stu-id="5178f-306">If a lower bound or size is not specified, it is omitted.</span></span> <span data-ttu-id="5178f-307">Se vengono omessi il limite inferiore e le dimensioni per una determinata dimensione, il `:` viene omesso anche.</span><span class="sxs-lookup"><span data-stu-id="5178f-307">If the lower bound and size for a particular dimension are omitted, the `:` is omitted as well.</span></span> <span data-ttu-id="5178f-308">Le matrici multidimensionali sono rappresentate da una `[]` per ogni livello.</span><span class="sxs-lookup"><span data-stu-id="5178f-308">Jagged arrays are represented by one `[]` per level.</span></span>
      * <span data-ttu-id="5178f-309">Gli argomenti che hanno tipi di puntatore diverso da void vengono rappresentati usando un `*` dopo il nome del tipo.</span><span class="sxs-lookup"><span data-stu-id="5178f-309">Arguments that have pointer types other than void are represented using a `*` following the type name.</span></span> <span data-ttu-id="5178f-310">Un puntatore void viene rappresentato con un nome di tipo `System.Void`.</span><span class="sxs-lookup"><span data-stu-id="5178f-310">A void pointer is represented using a type name of `System.Void`.</span></span>
      * <span data-ttu-id="5178f-311">Gli argomenti che fanno riferimento a parametri di tipo generico per i tipi di vengono codificati utilizzando la `` ` `` (apice inverso) carattere seguito da indice a base zero del parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="5178f-311">Arguments that refer to generic type parameters defined on types are encoded using the `` ` `` (backtick) character followed by the zero-based index of the type parameter.</span></span>
      * <span data-ttu-id="5178f-312">Gli argomenti che usano parametri di tipo generico definiti nei metodi utilizzano un double-apice inverso ``` `` ``` anziché il `` ` `` utilizzate per i tipi.</span><span class="sxs-lookup"><span data-stu-id="5178f-312">Arguments that use generic type parameters defined in methods use a double-backtick ``` `` ``` instead of the `` ` `` used for types.</span></span>
      * <span data-ttu-id="5178f-313">Gli argomenti che fanno riferimento a tipi generici costruiti vengono codificati utilizzando il tipo generico, seguito da `{`, seguito da un elenco delimitato da virgole degli argomenti di tipo, seguito da `}`.</span><span class="sxs-lookup"><span data-stu-id="5178f-313">Arguments that refer to constructed generic types are encoded using the generic type, followed by `{`, followed by a comma-separated list of type arguments, followed by `}`.</span></span>

### <a name="id-string-examples"></a><span data-ttu-id="5178f-314">Esempi di stringhe ID</span><span class="sxs-lookup"><span data-stu-id="5178f-314">ID string examples</span></span>

<span data-ttu-id="5178f-315">Gli esempi seguenti mostrano un frammento di codice c#, con una stringa ID prodotto da ogni elemento di origine in grado di avere un commento della documentazione:</span><span class="sxs-lookup"><span data-stu-id="5178f-315">The following examples each show a fragment of C# code, along with the ID string produced from each source element capable of having a documentation comment:</span></span>

*  <span data-ttu-id="5178f-316">I tipi vengono rappresentati usando il relativo nome completo, ampliato con le informazioni generiche:</span><span class="sxs-lookup"><span data-stu-id="5178f-316">Types are represented using their fully qualified name, augmented with generic information:</span></span>

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

*  <span data-ttu-id="5178f-317">I campi sono rappresentati da un nome completo:</span><span class="sxs-lookup"><span data-stu-id="5178f-317">Fields are represented by their fully qualified name:</span></span>

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

*  <span data-ttu-id="5178f-318">Costruttori.</span><span class="sxs-lookup"><span data-stu-id="5178f-318">Constructors.</span></span>

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

*  <span data-ttu-id="5178f-319">Distruttori.</span><span class="sxs-lookup"><span data-stu-id="5178f-319">Destructors.</span></span>

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

*  <span data-ttu-id="5178f-320">Metodi.</span><span class="sxs-lookup"><span data-stu-id="5178f-320">Methods.</span></span>

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

*  <span data-ttu-id="5178f-321">Le proprietà e indicizzatori.</span><span class="sxs-lookup"><span data-stu-id="5178f-321">Properties and indexers.</span></span>

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

*  <span data-ttu-id="5178f-322">eventi.</span><span class="sxs-lookup"><span data-stu-id="5178f-322">Events.</span></span>

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

*  <span data-ttu-id="5178f-323">Operatori unari.</span><span class="sxs-lookup"><span data-stu-id="5178f-323">Unary operators.</span></span>

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

   <span data-ttu-id="5178f-324">Il set completo di nomi di funzione di operatore unario utilizzato è il seguente: `op_UnaryPlus`, `op_UnaryNegation`, `op_LogicalNot`, `op_OnesComplement`, `op_Increment`, `op_Decrement`, `op_True`, e `op_False`.</span><span class="sxs-lookup"><span data-stu-id="5178f-324">The complete set of unary operator function names used is as follows: `op_UnaryPlus`, `op_UnaryNegation`, `op_LogicalNot`, `op_OnesComplement`, `op_Increment`, `op_Decrement`, `op_True`, and `op_False`.</span></span>

*  <span data-ttu-id="5178f-325">Operatori binari.</span><span class="sxs-lookup"><span data-stu-id="5178f-325">Binary operators.</span></span>

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

   <span data-ttu-id="5178f-326">Il set completo di nomi di funzione di operatore binario usato è il seguente: `op_Addition`, `op_Subtraction`, `op_Multiply`, `op_Division`, `op_Modulus`, `op_BitwiseAnd`, `op_BitwiseOr`, `op_ExclusiveOr`, `op_LeftShift`, `op_RightShift`, `op_Equality`, `op_Inequality`, `op_LessThan`, `op_LessThanOrEqual`, `op_GreaterThan`, e `op_GreaterThanOrEqual`.</span><span class="sxs-lookup"><span data-stu-id="5178f-326">The complete set of binary operator function names used is as follows: `op_Addition`, `op_Subtraction`, `op_Multiply`, `op_Division`, `op_Modulus`, `op_BitwiseAnd`, `op_BitwiseOr`, `op_ExclusiveOr`, `op_LeftShift`, `op_RightShift`, `op_Equality`, `op_Inequality`, `op_LessThan`, `op_LessThanOrEqual`, `op_GreaterThan`, and `op_GreaterThanOrEqual`.</span></span>

*  <span data-ttu-id="5178f-327">Gli operatori di conversione hanno una parentesi finale "`~`" seguito dal tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="5178f-327">Conversion operators have a trailing "`~`" followed by the return type.</span></span>

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

## <a name="an-example"></a><span data-ttu-id="5178f-328">un esempio</span><span class="sxs-lookup"><span data-stu-id="5178f-328">An example</span></span>

### <a name="c-source-code"></a><span data-ttu-id="5178f-329">Il codice sorgente c#</span><span class="sxs-lookup"><span data-stu-id="5178f-329">C# source code</span></span>

<span data-ttu-id="5178f-330">L'esempio seguente mostra il codice sorgente di un `Point` classe:</span><span class="sxs-lookup"><span data-stu-id="5178f-330">The following example shows the source code of a `Point` class:</span></span>

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

### <a name="resulting-xml"></a><span data-ttu-id="5178f-331">Codice XML risultante</span><span class="sxs-lookup"><span data-stu-id="5178f-331">Resulting XML</span></span>

<span data-ttu-id="5178f-332">Di seguito è riportato l'output prodotto dal generatore di documentazione uno quando viene specificato il codice sorgente per la classe `Point`, come illustrato in precedenza:</span><span class="sxs-lookup"><span data-stu-id="5178f-332">Here is the output produced by one documentation generator when given the source code for class `Point`, shown above:</span></span>

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
