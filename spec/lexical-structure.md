---
ms.openlocfilehash: 5fbe0267b5b33b1a24dbdca493d118c576092573
ms.sourcegitcommit: 7f7fc6e9e195e51b7ff8229aeaa70aa9fbbb63cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876922"
---
# <a name="lexical-structure"></a><span data-ttu-id="66c41-101">Struttura lessicale</span><span class="sxs-lookup"><span data-stu-id="66c41-101">Lexical structure</span></span>

## <a name="programs"></a><span data-ttu-id="66c41-102">Programs</span><span class="sxs-lookup"><span data-stu-id="66c41-102">Programs</span></span>

<span data-ttu-id="66c41-103">Un C# ***programma*** è costituito da uno o più ***file di origine***, noti formalmente come ***unità*** di compilazione ([unità di compilazione](namespaces.md#compilation-units)).</span><span class="sxs-lookup"><span data-stu-id="66c41-103">A C# ***program*** consists of one or more ***source files***, known formally as ***compilation units*** ([Compilation units](namespaces.md#compilation-units)).</span></span> <span data-ttu-id="66c41-104">Un file di origine è una sequenza ordinata di caratteri Unicode.</span><span class="sxs-lookup"><span data-stu-id="66c41-104">A source file is an ordered sequence of Unicode characters.</span></span> <span data-ttu-id="66c41-105">I file di origine hanno in genere una corrispondenza uno-a-uno con i file in un file system, ma questa corrispondenza non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="66c41-105">Source files typically have a one-to-one correspondence with files in a file system, but this correspondence is not required.</span></span> <span data-ttu-id="66c41-106">Per la massima portabilità, è consigliabile che i file in un file system siano codificati con la codifica UTF-8.</span><span class="sxs-lookup"><span data-stu-id="66c41-106">For maximal portability, it is recommended that files in a file system be encoded with the UTF-8 encoding.</span></span>

<span data-ttu-id="66c41-107">In teoria, un programma viene compilato con tre passaggi:</span><span class="sxs-lookup"><span data-stu-id="66c41-107">Conceptually speaking, a program is compiled using three steps:</span></span>

1. <span data-ttu-id="66c41-108">Trasformazione, che converte un file da un particolare repertorio di caratteri e uno schema di codifica in una sequenza di caratteri Unicode.</span><span class="sxs-lookup"><span data-stu-id="66c41-108">Transformation, which converts a file from a particular character repertoire and encoding scheme into a sequence of Unicode characters.</span></span>
2. <span data-ttu-id="66c41-109">Analisi lessicale, che converte un flusso di caratteri di input Unicode in un flusso di token.</span><span class="sxs-lookup"><span data-stu-id="66c41-109">Lexical analysis, which translates a stream of Unicode input characters into a stream of tokens.</span></span>
3. <span data-ttu-id="66c41-110">Analisi sintattica, che converte il flusso di token in codice eseguibile.</span><span class="sxs-lookup"><span data-stu-id="66c41-110">Syntactic analysis, which translates the stream of tokens into executable code.</span></span>

## <a name="grammars"></a><span data-ttu-id="66c41-111">Grammatiche</span><span class="sxs-lookup"><span data-stu-id="66c41-111">Grammars</span></span>

<span data-ttu-id="66c41-112">Questa specifica presenta la sintassi del linguaggio C# di programmazione utilizzando due grammatiche.</span><span class="sxs-lookup"><span data-stu-id="66c41-112">This specification presents the syntax of the C# programming language using two grammars.</span></span> <span data-ttu-id="66c41-113">La ***grammatica lessicale*** ([grammatica lessicale](lexical-structure.md#lexical-grammar)) definisce il modo in cui i caratteri Unicode vengono combinati per i terminatori di riga del modulo, gli spazi vuoti, i commenti, i token e le direttive di pre-elaborazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-113">The ***lexical grammar*** ([Lexical grammar](lexical-structure.md#lexical-grammar)) defines how Unicode characters are combined to form line terminators, white space, comments, tokens, and pre-processing directives.</span></span> <span data-ttu-id="66c41-114">La ***grammatica*** sintattica ([grammatica sintattica](lexical-structure.md#syntactic-grammar)) definisce il modo in cui i token risultanti dalla grammatica lessicale C# vengono combinati per formare i programmi.</span><span class="sxs-lookup"><span data-stu-id="66c41-114">The ***syntactic grammar*** ([Syntactic grammar](lexical-structure.md#syntactic-grammar)) defines how the tokens resulting from the lexical grammar are combined to form C# programs.</span></span>

### <a name="grammar-notation"></a><span data-ttu-id="66c41-115">Notazione grammatica</span><span class="sxs-lookup"><span data-stu-id="66c41-115">Grammar notation</span></span>

<span data-ttu-id="66c41-116">Le grammatiche lessicali e sintattiche vengono presentate in forma Backus-Naur usando la notazione dello strumento di grammatica ANTLR.</span><span class="sxs-lookup"><span data-stu-id="66c41-116">The lexical and syntactic grammars are presented in Backus-Naur form using the notation of the ANTLR grammar tool.</span></span>

### <a name="lexical-grammar"></a><span data-ttu-id="66c41-117">Grammatica lessicale</span><span class="sxs-lookup"><span data-stu-id="66c41-117">Lexical grammar</span></span>

<span data-ttu-id="66c41-118">La grammatica lessicale di C# viene presentata in [analisi lessicale](lexical-structure.md#lexical-analysis), [token](lexical-structure.md#tokens)e [direttive di pre-elaborazione](lexical-structure.md#pre-processing-directives).</span><span class="sxs-lookup"><span data-stu-id="66c41-118">The lexical grammar of C# is presented in [Lexical analysis](lexical-structure.md#lexical-analysis), [Tokens](lexical-structure.md#tokens), and [Pre-processing directives](lexical-structure.md#pre-processing-directives).</span></span> <span data-ttu-id="66c41-119">I simboli terminali della grammatica lessicale sono i caratteri del set di caratteri Unicode e la grammatica lessicale specifica il modo in cui i caratteri vengono combinati per formare token ([token](lexical-structure.md#tokens)), spazi vuoti ([spazi vuoti](lexical-structure.md#white-space)), commenti ([Commenti](lexical-structure.md#comments)) e direttive di pre-elaborazione ([direttive di pre-elaborazione](lexical-structure.md#pre-processing-directives)).</span><span class="sxs-lookup"><span data-stu-id="66c41-119">The terminal symbols of the lexical grammar are the characters of the Unicode character set, and the lexical grammar specifies how characters are combined to form tokens ([Tokens](lexical-structure.md#tokens)), white space ([White space](lexical-structure.md#white-space)), comments ([Comments](lexical-structure.md#comments)), and pre-processing directives ([Pre-processing directives](lexical-structure.md#pre-processing-directives)).</span></span>

<span data-ttu-id="66c41-120">Ogni file di origine in C# un programma deve essere conforme alla produzione di *input* della grammatica lessicale ([analisi lessicale](lexical-structure.md#lexical-analysis)).</span><span class="sxs-lookup"><span data-stu-id="66c41-120">Every source file in a C# program must conform to the *input* production of the lexical grammar ([Lexical analysis](lexical-structure.md#lexical-analysis)).</span></span>

### <a name="syntactic-grammar"></a><span data-ttu-id="66c41-121">Grammatica sintattica</span><span class="sxs-lookup"><span data-stu-id="66c41-121">Syntactic grammar</span></span>

<span data-ttu-id="66c41-122">La grammatica sintattica C# di viene presentata nei capitoli e nelle appendici che seguono questo capitolo.</span><span class="sxs-lookup"><span data-stu-id="66c41-122">The syntactic grammar of C# is presented in the chapters and appendices that follow this chapter.</span></span> <span data-ttu-id="66c41-123">I simboli terminali della grammatica sintattica sono i token definiti dalla grammatica lessicale e la grammatica sintattica specifica il modo in cui i token vengono combinati C# per formare i programmi.</span><span class="sxs-lookup"><span data-stu-id="66c41-123">The terminal symbols of the syntactic grammar are the tokens defined by the lexical grammar, and the syntactic grammar specifies how tokens are combined to form C# programs.</span></span>

<span data-ttu-id="66c41-124">Ogni file di origine in C# un programma deve essere conforme alla produzione *compilation_unit* della grammatica sintattica ([unità di compilazione](namespaces.md#compilation-units)).</span><span class="sxs-lookup"><span data-stu-id="66c41-124">Every source file in a C# program must conform to the *compilation_unit* production of the syntactic grammar ([Compilation units](namespaces.md#compilation-units)).</span></span>

## <a name="lexical-analysis"></a><span data-ttu-id="66c41-125">Analisi lessicale</span><span class="sxs-lookup"><span data-stu-id="66c41-125">Lexical analysis</span></span>

<span data-ttu-id="66c41-126">La produzione di *input* definisce la struttura lessicale di C# un file di origine.</span><span class="sxs-lookup"><span data-stu-id="66c41-126">The *input* production defines the lexical structure of a C# source file.</span></span> <span data-ttu-id="66c41-127">Ogni file di origine in C# un programma deve essere conforme alla produzione di grammatica lessicale.</span><span class="sxs-lookup"><span data-stu-id="66c41-127">Each source file in a C# program must conform to this lexical grammar production.</span></span>

```antlr
input
    : input_section?
    ;

input_section
    : input_section_part+
    ;

input_section_part
    : input_element* new_line
    | pp_directive
    ;

input_element
    : whitespace
    | comment
    | token
    ;
```

<span data-ttu-id="66c41-128">Cinque elementi di base costituiscono la struttura lessicale di un C# file di origine: Terminatori di riga ([terminatori di riga](lexical-structure.md#line-terminators)), spazi vuoti ([spazi vuoti](lexical-structure.md#white-space)), commenti ([Commenti](lexical-structure.md#comments)), token ([token](lexical-structure.md#tokens)) e direttive di pre-elaborazione ([direttive di pre-elaborazione](lexical-structure.md#pre-processing-directives)).</span><span class="sxs-lookup"><span data-stu-id="66c41-128">Five basic elements make up the lexical structure of a C# source file: Line terminators ([Line terminators](lexical-structure.md#line-terminators)), white space ([White space](lexical-structure.md#white-space)), comments ([Comments](lexical-structure.md#comments)), tokens ([Tokens](lexical-structure.md#tokens)), and pre-processing directives ([Pre-processing directives](lexical-structure.md#pre-processing-directives)).</span></span> <span data-ttu-id="66c41-129">Di questi elementi di base, solo i token sono significativi nella grammatica sintattica di C# un programma ([grammatica sintattica](lexical-structure.md#syntactic-grammar)).</span><span class="sxs-lookup"><span data-stu-id="66c41-129">Of these basic elements, only tokens are significant in the syntactic grammar of a C# program ([Syntactic grammar](lexical-structure.md#syntactic-grammar)).</span></span>

<span data-ttu-id="66c41-130">L'elaborazione lessicale di un C# file di origine è costituita dalla riduzione del file in una sequenza di token che diventa l'input per l'analisi sintattica.</span><span class="sxs-lookup"><span data-stu-id="66c41-130">The lexical processing of a C# source file consists of reducing the file into a sequence of tokens which becomes the input to the syntactic analysis.</span></span> <span data-ttu-id="66c41-131">I terminatori di riga, gli spazi vuoti e i commenti possono essere utilizzati per separare i token, mentre le direttive di pre-elaborazione possono causare l'omissione di sezioni del file di origine. in caso contrario, questi elementi lessicali non hanno C# alcun effetto sulla struttura sintattica di un programma.</span><span class="sxs-lookup"><span data-stu-id="66c41-131">Line terminators, white space, and comments can serve to separate tokens, and pre-processing directives can cause sections of the source file to be skipped, but otherwise these lexical elements have no impact on the syntactic structure of a C# program.</span></span>

<span data-ttu-id="66c41-132">Nel caso di valori letterali stringa interpolati ([valori letterali stringa interpolata](lexical-structure.md#interpolated-string-literals)), un singolo token viene inizialmente prodotto dall'analisi lessicale, ma viene suddiviso in diversi elementi di input che vengono ripetutamente sottoposti a analisi lessicale fino a quando non tutte le interpolate i valori letterali stringa sono stati risolti.</span><span class="sxs-lookup"><span data-stu-id="66c41-132">In the case of interpolated string literals ([Interpolated string literals](lexical-structure.md#interpolated-string-literals)) a single token is initially produced by lexical analysis, but is broken up into several input elements which are repeatedly subjected to lexical analysis until all interpolated string literals have been resolved.</span></span> <span data-ttu-id="66c41-133">I token risultanti vengono quindi usati come input per l'analisi sintattica.</span><span class="sxs-lookup"><span data-stu-id="66c41-133">The resulting tokens then serve as input to the syntactic analysis.</span></span>

<span data-ttu-id="66c41-134">Quando più produzioni di grammatica lessicali corrispondono a una sequenza di caratteri in un file di origine, l'elaborazione lessicale costituisce sempre l'elemento lessicale più lungo possibile.</span><span class="sxs-lookup"><span data-stu-id="66c41-134">When several lexical grammar productions match a sequence of characters in a source file, the lexical processing always forms the longest possible lexical element.</span></span> <span data-ttu-id="66c41-135">Ad esempio, la sequenza `//` di caratteri viene elaborata come l'inizio di un commento a riga singola, perché tale elemento lessicale è più lungo di un singolo `/` token.</span><span class="sxs-lookup"><span data-stu-id="66c41-135">For example, the character sequence `//` is processed as the beginning of a single-line comment because that lexical element is longer than a single `/` token.</span></span>

### <a name="line-terminators"></a><span data-ttu-id="66c41-136">Terminatori di riga</span><span class="sxs-lookup"><span data-stu-id="66c41-136">Line terminators</span></span>

<span data-ttu-id="66c41-137">I caratteri di terminazione di riga dividono C# i caratteri di un file di origine in righe.</span><span class="sxs-lookup"><span data-stu-id="66c41-137">Line terminators divide the characters of a C# source file into lines.</span></span>

```antlr
new_line
    : '<Carriage return character (U+000D)>'
    | '<Line feed character (U+000A)>'
    | '<Carriage return character (U+000D) followed by line feed character (U+000A)>'
    | '<Next line character (U+0085)>'
    | '<Line separator character (U+2028)>'
    | '<Paragraph separator character (U+2029)>'
    ;
```

<span data-ttu-id="66c41-138">Per la compatibilità con gli strumenti di modifica del codice sorgente che aggiungono marcatori di fine file e per consentire la visualizzazione di un file di origine come sequenza di righe terminate correttamente, le trasformazioni seguenti vengono applicate, in ordine, a ogni C# file di origine in un programma:</span><span class="sxs-lookup"><span data-stu-id="66c41-138">For compatibility with source code editing tools that add end-of-file markers, and to enable a source file to be viewed as a sequence of properly terminated lines, the following transformations are applied, in order, to every source file in a C# program:</span></span>

*  <span data-ttu-id="66c41-139">Se l'ultimo carattere del file di origine è un carattere di controllo Z (`U+001A`), questo carattere viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="66c41-139">If the last character of the source file is a Control-Z character (`U+001A`), this character is deleted.</span></span>
*  <span data-ttu-id="66c41-140">Un carattere di ritorno a capo`U+000D`() viene aggiunto alla fine del file di origine se il file di origine non è vuoto e l'ultimo carattere del file di origine non è un ritorno a capo (`U+000D`), un avanzamento riga (`U+000A`), un separatore di riga (`U+2028`) o un separatore di paragrafo`U+2029`().</span><span class="sxs-lookup"><span data-stu-id="66c41-140">A carriage-return character (`U+000D`) is added to the end of the source file if that source file is non-empty and if the last character of the source file is not a carriage return (`U+000D`), a line feed (`U+000A`), a line separator (`U+2028`), or a paragraph separator (`U+2029`).</span></span>

### <a name="comments"></a><span data-ttu-id="66c41-141">Commenti</span><span class="sxs-lookup"><span data-stu-id="66c41-141">Comments</span></span>

<span data-ttu-id="66c41-142">Sono supportati due tipi di Commenti: Commenti a riga singola e commenti delimitati.</span><span class="sxs-lookup"><span data-stu-id="66c41-142">Two forms of comments are supported: single-line comments and delimited comments.</span></span> <span data-ttu-id="66c41-143">I ***Commenti a riga singola*** iniziano con i `//` caratteri e si estendono alla fine della riga di origine.</span><span class="sxs-lookup"><span data-stu-id="66c41-143">***Single-line comments*** start with the characters `//` and extend to the end of the source line.</span></span> <span data-ttu-id="66c41-144">I ***commenti delimitati*** iniziano con i `/*` caratteri e terminano con `*/`i caratteri.</span><span class="sxs-lookup"><span data-stu-id="66c41-144">***Delimited comments*** start with the characters `/*` and end with the characters `*/`.</span></span> <span data-ttu-id="66c41-145">I commenti delimitati possono estendersi su più righe.</span><span class="sxs-lookup"><span data-stu-id="66c41-145">Delimited comments may span multiple lines.</span></span>

```antlr
comment
    : single_line_comment
    | delimited_comment
    ;

single_line_comment
    : '//' input_character*
    ;

input_character
    : '<Any Unicode character except a new_line_character>'
    ;

new_line_character
    : '<Carriage return character (U+000D)>'
    | '<Line feed character (U+000A)>'
    | '<Next line character (U+0085)>'
    | '<Line separator character (U+2028)>'
    | '<Paragraph separator character (U+2029)>'
    ;

delimited_comment
    : '/*' delimited_comment_section* asterisk+ '/'
    ;

delimited_comment_section
    : '/'
    | asterisk* not_slash_or_asterisk
    ;

asterisk
    : '*'
    ;

not_slash_or_asterisk
    : '<Any Unicode character except / or *>'
    ;
```

<span data-ttu-id="66c41-146">I commenti non sono annidati.</span><span class="sxs-lookup"><span data-stu-id="66c41-146">Comments do not nest.</span></span> <span data-ttu-id="66c41-147">`/*` Le sequenze di caratteri e `*/` non hanno un significato speciale all' `//` interno di un commento e le sequenze `//` di `/*` caratteri e non hanno un significato speciale in un commento delimitato.</span><span class="sxs-lookup"><span data-stu-id="66c41-147">The character sequences `/*` and `*/` have no special meaning within a `//` comment, and the character sequences `//` and `/*` have no special meaning within a delimited comment.</span></span>

<span data-ttu-id="66c41-148">I commenti non vengono elaborati all'interno di valori letterali carattere e stringa.</span><span class="sxs-lookup"><span data-stu-id="66c41-148">Comments are not processed within character and string literals.</span></span>

<span data-ttu-id="66c41-149">Esempio</span><span class="sxs-lookup"><span data-stu-id="66c41-149">The example</span></span>
```csharp
/* Hello, world program
   This program writes "hello, world" to the console
*/
class Hello
{
    static void Main() {
        System.Console.WriteLine("hello, world");
    }
}
```
<span data-ttu-id="66c41-150">include un commento delimitato.</span><span class="sxs-lookup"><span data-stu-id="66c41-150">includes a delimited comment.</span></span>

<span data-ttu-id="66c41-151">Esempio</span><span class="sxs-lookup"><span data-stu-id="66c41-151">The example</span></span>
```csharp
// Hello, world program
// This program writes "hello, world" to the console
//
class Hello // any name will do for this class
{
    static void Main() { // this method must be named "Main"
        System.Console.WriteLine("hello, world");
    }
}
```
<span data-ttu-id="66c41-152">Mostra diversi commenti a riga singola.</span><span class="sxs-lookup"><span data-stu-id="66c41-152">shows several single-line comments.</span></span>

### <a name="white-space"></a><span data-ttu-id="66c41-153">Spazio vuoto</span><span class="sxs-lookup"><span data-stu-id="66c41-153">White space</span></span>

<span data-ttu-id="66c41-154">Lo spazio vuoto viene definito come qualsiasi carattere con la classe Unicode ZS (che include lo spazio), nonché il carattere di tabulazione orizzontale, il carattere di tabulazione verticale e il carattere di avanzamento del modulo.</span><span class="sxs-lookup"><span data-stu-id="66c41-154">White space is defined as any character with Unicode class Zs (which includes the space character) as well as the horizontal tab character, the vertical tab character, and the form feed character.</span></span>

```antlr
whitespace
    : '<Any character with Unicode class Zs>'
    | '<Horizontal tab character (U+0009)>'
    | '<Vertical tab character (U+000B)>'
    | '<Form feed character (U+000C)>'
    ;
```

## <a name="tokens"></a><span data-ttu-id="66c41-155">token</span><span class="sxs-lookup"><span data-stu-id="66c41-155">Tokens</span></span>

<span data-ttu-id="66c41-156">Esistono diversi tipi di token: identificatori, parole chiave, valori letterali, operatori e segni di punteggiatura.</span><span class="sxs-lookup"><span data-stu-id="66c41-156">There are several kinds of tokens: identifiers, keywords, literals, operators, and punctuators.</span></span> <span data-ttu-id="66c41-157">Gli spazi vuoti e i commenti non sono token, ma fungono da separatori per i token.</span><span class="sxs-lookup"><span data-stu-id="66c41-157">White space and comments are not tokens, though they act as separators for tokens.</span></span>

```antlr
token
    : identifier
    | keyword
    | integer_literal
    | real_literal
    | character_literal
    | string_literal
    | interpolated_string_literal
    | operator_or_punctuator
    ;
```

### <a name="unicode-character-escape-sequences"></a><span data-ttu-id="66c41-158">Sequenze di escape di caratteri Unicode</span><span class="sxs-lookup"><span data-stu-id="66c41-158">Unicode character escape sequences</span></span>

<span data-ttu-id="66c41-159">Una sequenza di escape di caratteri Unicode rappresenta un carattere Unicode.</span><span class="sxs-lookup"><span data-stu-id="66c41-159">A Unicode character escape sequence represents a Unicode character.</span></span> <span data-ttu-id="66c41-160">Le sequenze di escape di caratteri Unicode vengono elaborate in identificatori ([identificatori](lexical-structure.md#identifiers)), valori letterali carattere ([valori letterali carattere](lexical-structure.md#character-literals)) e valori letterali stringa regolari ([valori letterali stringa](lexical-structure.md#string-literals)).</span><span class="sxs-lookup"><span data-stu-id="66c41-160">Unicode character escape sequences are processed in identifiers ([Identifiers](lexical-structure.md#identifiers)), character literals ([Character literals](lexical-structure.md#character-literals)), and regular string literals ([String literals](lexical-structure.md#string-literals)).</span></span> <span data-ttu-id="66c41-161">Un carattere di escape Unicode non viene elaborato in nessun'altra posizione, ad esempio per formare un operatore, un punteggiatore o una parola chiave.</span><span class="sxs-lookup"><span data-stu-id="66c41-161">A Unicode character escape is not processed in any other location (for example, to form an operator, punctuator, or keyword).</span></span>

```antlr
unicode_escape_sequence
    : '\\u' hex_digit hex_digit hex_digit hex_digit
    | '\\U' hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit
    ;
```

<span data-ttu-id="66c41-162">Una sequenza di escape Unicode rappresenta il singolo carattere Unicode formato dal numero esadecimale che segue`\u`i caratteri "`\U`" o "".</span><span class="sxs-lookup"><span data-stu-id="66c41-162">A Unicode escape sequence represents the single Unicode character formed by the hexadecimal number following the "`\u`" or "`\U`" characters.</span></span> <span data-ttu-id="66c41-163">Poiché C# utilizza una codifica a 16 bit di punti di codice Unicode in caratteri e valori stringa, un carattere Unicode compreso nell'intervallo da u + 10000 a u + 10FFFF non è consentito in un valore letterale carattere e viene rappresentato utilizzando una coppia di surrogati Unicode in un valore letterale stringa.</span><span class="sxs-lookup"><span data-stu-id="66c41-163">Since C# uses a 16-bit encoding of Unicode code points in characters and string values, a Unicode character in the range U+10000 to U+10FFFF is not permitted in a character literal and is represented using a Unicode surrogate pair in a string literal.</span></span> <span data-ttu-id="66c41-164">I caratteri Unicode con punti di codice precedenti a 0x10FFFF non sono supportati.</span><span class="sxs-lookup"><span data-stu-id="66c41-164">Unicode characters with code points above 0x10FFFF are not supported.</span></span>

<span data-ttu-id="66c41-165">Non vengono eseguite più traduzioni.</span><span class="sxs-lookup"><span data-stu-id="66c41-165">Multiple translations are not performed.</span></span> <span data-ttu-id="66c41-166">Ad esempio, il valore letterale`\u005Cu005C`stringa "" è equivalente`\u005C`a "" invece`\`di "".</span><span class="sxs-lookup"><span data-stu-id="66c41-166">For instance, the string literal "`\u005Cu005C`" is equivalent to "`\u005C`" rather than "`\`".</span></span> <span data-ttu-id="66c41-167">Il valore `\u005C` Unicode è il carattere "`\`".</span><span class="sxs-lookup"><span data-stu-id="66c41-167">The Unicode value `\u005C` is the character "`\`".</span></span>

<span data-ttu-id="66c41-168">Esempio</span><span class="sxs-lookup"><span data-stu-id="66c41-168">The example</span></span>
```csharp
class Class1
{
    static void Test(bool \u0066) {
        char c = '\u0066';
        if (\u0066)
            System.Console.WriteLine(c.ToString());
    }        
}
```
<span data-ttu-id="66c41-169">Mostra diversi usi di `\u0066`, ovvero la sequenza di escape per la lettera "`f`".</span><span class="sxs-lookup"><span data-stu-id="66c41-169">shows several uses of `\u0066`, which is the escape sequence for the letter "`f`".</span></span> <span data-ttu-id="66c41-170">Il programma è equivalente a</span><span class="sxs-lookup"><span data-stu-id="66c41-170">The program is equivalent to</span></span>
```csharp
class Class1
{
    static void Test(bool f) {
        char c = 'f';
        if (f)
            System.Console.WriteLine(c.ToString());
    }        
}
```

### <a name="identifiers"></a><span data-ttu-id="66c41-171">Identificatori</span><span class="sxs-lookup"><span data-stu-id="66c41-171">Identifiers</span></span>

<span data-ttu-id="66c41-172">Le regole per gli identificatori indicati in questa sezione corrispondono esattamente a quelle consigliate dall'allegato 31 Unicode standard, ad eccezione del fatto che il carattere di sottolineatura è consentito come carattere iniziale (come nel linguaggio di programmazione C), le sequenze di escape Unicode sono consentito negli identificatori e il carattere "`@`" è consentito come prefisso per consentire l'uso delle parole chiave come identificatori.</span><span class="sxs-lookup"><span data-stu-id="66c41-172">The rules for identifiers given in this section correspond exactly to those recommended by the Unicode Standard Annex 31, except that underscore is allowed as an initial character (as is traditional in the C programming language), Unicode escape sequences are permitted in identifiers, and the "`@`" character is allowed as a prefix to enable keywords to be used as identifiers.</span></span>

```antlr
identifier
    : available_identifier
    | '@' identifier_or_keyword
    ;

available_identifier
    : '<An identifier_or_keyword that is not a keyword>'
    ;

identifier_or_keyword
    : identifier_start_character identifier_part_character*
    ;

identifier_start_character
    : letter_character
    | '_'
    ;

identifier_part_character
    : letter_character
    | decimal_digit_character
    | connecting_character
    | combining_character
    | formatting_character
    ;

letter_character
    : '<A Unicode character of classes Lu, Ll, Lt, Lm, Lo, or Nl>'
    | '<A unicode_escape_sequence representing a character of classes Lu, Ll, Lt, Lm, Lo, or Nl>'
    ;

combining_character
    : '<A Unicode character of classes Mn or Mc>'
    | '<A unicode_escape_sequence representing a character of classes Mn or Mc>'
    ;

decimal_digit_character
    : '<A Unicode character of the class Nd>'
    | '<A unicode_escape_sequence representing a character of the class Nd>'
    ;

connecting_character
    : '<A Unicode character of the class Pc>'
    | '<A unicode_escape_sequence representing a character of the class Pc>'
    ;

formatting_character
    : '<A Unicode character of the class Cf>'
    | '<A unicode_escape_sequence representing a character of the class Cf>'
    ;
```

<span data-ttu-id="66c41-173">Per informazioni sulle classi di caratteri Unicode indicate sopra, vedere la sezione 4,5 dello standard Unicode, versione 3,0.</span><span class="sxs-lookup"><span data-stu-id="66c41-173">For information on the Unicode character classes mentioned above, see The Unicode Standard, Version 3.0, section 4.5.</span></span>

<span data-ttu-id="66c41-174">Esempi di identificatori validi sono "`identifier1`", "`_identifier2`" e "`@if`".</span><span class="sxs-lookup"><span data-stu-id="66c41-174">Examples of valid identifiers include "`identifier1`", "`_identifier2`", and "`@if`".</span></span>

<span data-ttu-id="66c41-175">Un identificatore in un programma di conformità deve essere nel formato canonico definito dal formato di normalizzazione Unicode C, come definito dall'allegato 15 Unicode standard.</span><span class="sxs-lookup"><span data-stu-id="66c41-175">An identifier in a conforming program must be in the canonical format defined by Unicode Normalization Form C, as defined by Unicode Standard Annex 15.</span></span> <span data-ttu-id="66c41-176">Il comportamento quando viene rilevato un identificatore non nel formato di normalizzazione C viene definito dall'implementazione; Tuttavia, non è necessaria una diagnostica.</span><span class="sxs-lookup"><span data-stu-id="66c41-176">The behavior when encountering an identifier not in Normalization Form C is implementation-defined; however, a diagnostic is not required.</span></span>

<span data-ttu-id="66c41-177">Il prefisso "`@`" consente l'uso di parole chiave come identificatori, che risulta utile quando si interagisce con altri linguaggi di programmazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-177">The prefix "`@`" enables the use of keywords as identifiers, which is useful when interfacing with other programming languages.</span></span> <span data-ttu-id="66c41-178">Il carattere `@` non è in realtà parte dell'identificatore, quindi l'identificatore può essere visualizzato in altri linguaggi come identificatore normale, senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="66c41-178">The character `@` is not actually part of the identifier, so the identifier might be seen in other languages as a normal identifier, without the prefix.</span></span> <span data-ttu-id="66c41-179">Un identificatore con `@` prefisso è denominato ***identificatore Verbatim***.</span><span class="sxs-lookup"><span data-stu-id="66c41-179">An identifier with an `@` prefix is called a ***verbatim identifier***.</span></span> <span data-ttu-id="66c41-180">L'uso del `@` prefisso per gli identificatori che non sono parole chiave è consentito, ma fortemente sconsigliato come una questione di stile.</span><span class="sxs-lookup"><span data-stu-id="66c41-180">Use of the `@` prefix for identifiers that are not keywords is permitted, but strongly discouraged as a matter of style.</span></span>

<span data-ttu-id="66c41-181">Esempio:</span><span class="sxs-lookup"><span data-stu-id="66c41-181">The example:</span></span>
```csharp
class @class
{
    public static void @static(bool @bool) {
        if (@bool)
            System.Console.WriteLine("true");
        else
            System.Console.WriteLine("false");
    }    
}

class Class1
{
    static void M() {
        cl\u0061ss.st\u0061tic(true);
    }
}
```
<span data-ttu-id="66c41-182">definisce una classe denominata "`class`" con un metodo statico denominato "`static`" che accetta un parametro denominato "`bool`".</span><span class="sxs-lookup"><span data-stu-id="66c41-182">defines a class named "`class`" with a static method named "`static`" that takes a parameter named "`bool`".</span></span> <span data-ttu-id="66c41-183">Si noti che poiché le escape Unicode non sono consentite nelle parole chiave`cl\u0061ss`, il token "" è un identificatore ed è lo stesso`@class`identificatore di "".</span><span class="sxs-lookup"><span data-stu-id="66c41-183">Note that since Unicode escapes are not permitted in keywords, the token "`cl\u0061ss`" is an identifier, and is the same identifier as "`@class`".</span></span>

<span data-ttu-id="66c41-184">Due identificatori sono considerati uguali se sono identici dopo l'applicazione delle trasformazioni seguenti, nell'ordine:</span><span class="sxs-lookup"><span data-stu-id="66c41-184">Two identifiers are considered the same if they are identical after the following transformations are applied, in order:</span></span>

*  <span data-ttu-id="66c41-185">Il prefisso "`@`", se usato, viene rimosso.</span><span class="sxs-lookup"><span data-stu-id="66c41-185">The prefix "`@`", if used, is removed.</span></span>
*  <span data-ttu-id="66c41-186">Ogni *unicode_escape_sequence* viene trasformato nel carattere Unicode corrispondente.</span><span class="sxs-lookup"><span data-stu-id="66c41-186">Each *unicode_escape_sequence* is transformed into its corresponding Unicode character.</span></span>
*  <span data-ttu-id="66c41-187">Tutti i *formatting_character*vengono rimossi.</span><span class="sxs-lookup"><span data-stu-id="66c41-187">Any *formatting_character*s are removed.</span></span>

<span data-ttu-id="66c41-188">Gli identificatori contenenti due caratteri di sottolineatura consecutivi (`U+005F`) sono riservati per l'utilizzo da parte dell'implementazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-188">Identifiers containing two consecutive underscore characters (`U+005F`) are reserved for use by the implementation.</span></span> <span data-ttu-id="66c41-189">Un'implementazione, ad esempio, può fornire parole chiave estese che iniziano con due caratteri di sottolineatura.</span><span class="sxs-lookup"><span data-stu-id="66c41-189">For example, an implementation might provide extended keywords that begin with two underscores.</span></span>

### <a name="keywords"></a><span data-ttu-id="66c41-190">Parole chiave</span><span class="sxs-lookup"><span data-stu-id="66c41-190">Keywords</span></span>

<span data-ttu-id="66c41-191">Una ***parola chiave*** è una sequenza di caratteri di tipo identificatore riservata e non può essere usata come identificatore tranne quando è preceduta dal `@` carattere.</span><span class="sxs-lookup"><span data-stu-id="66c41-191">A ***keyword*** is an identifier-like sequence of characters that is reserved, and cannot be used as an identifier except when prefaced by the `@` character.</span></span>

```antlr
keyword
    : 'abstract' | 'as'       | 'base'       | 'bool'      | 'break'
    | 'byte'     | 'case'     | 'catch'      | 'char'      | 'checked'
    | 'class'    | 'const'    | 'continue'   | 'decimal'   | 'default'
    | 'delegate' | 'do'       | 'double'     | 'else'      | 'enum'
    | 'event'    | 'explicit' | 'extern'     | 'false'     | 'finally'
    | 'fixed'    | 'float'    | 'for'        | 'foreach'   | 'goto'
    | 'if'       | 'implicit' | 'in'         | 'int'       | 'interface'
    | 'internal' | 'is'       | 'lock'       | 'long'      | 'namespace'
    | 'new'      | 'null'     | 'object'     | 'operator'  | 'out'
    | 'override' | 'params'   | 'private'    | 'protected' | 'public'
    | 'readonly' | 'ref'      | 'return'     | 'sbyte'     | 'sealed'
    | 'short'    | 'sizeof'   | 'stackalloc' | 'static'    | 'string'
    | 'struct'   | 'switch'   | 'this'       | 'throw'     | 'true'
    | 'try'      | 'typeof'   | 'uint'       | 'ulong'     | 'unchecked'
    | 'unsafe'   | 'ushort'   | 'using'      | 'virtual'   | 'void'
    | 'volatile' | 'while'
    ;
```

<span data-ttu-id="66c41-192">In alcuni punti della grammatica, gli identificatori specifici hanno un significato speciale, ma non sono parole chiave.</span><span class="sxs-lookup"><span data-stu-id="66c41-192">In some places in the grammar, specific identifiers have special meaning, but are not keywords.</span></span> <span data-ttu-id="66c41-193">Tali identificatori sono talvolta denominati "parole chiave contestuali".</span><span class="sxs-lookup"><span data-stu-id="66c41-193">Such identifiers are sometimes referred to as "contextual keywords".</span></span> <span data-ttu-id="66c41-194">Ad esempio, all'interno di una dichiarazione di proprietà`get`, gli identificatori "" e "`set`" hanno un significato speciale ([funzioni di accesso](classes.md#accessors)).</span><span class="sxs-lookup"><span data-stu-id="66c41-194">For example, within a property declaration, the "`get`" and "`set`" identifiers have special meaning ([Accessors](classes.md#accessors)).</span></span> <span data-ttu-id="66c41-195">Un identificatore diverso da `get` o `set` non è mai consentito in questi percorsi, quindi questo utilizzo non è in conflitto con l'utilizzo di queste parole come identificatori.</span><span class="sxs-lookup"><span data-stu-id="66c41-195">An identifier other than `get` or `set` is never permitted in these locations, so this use does not conflict with a use of these words as identifiers.</span></span> <span data-ttu-id="66c41-196">In altri casi, ad esempio con l'identificatore "`var`" nelle dichiarazioni di variabili locali tipizzate in modo implicito ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)), una parola chiave contestuale può entrare in conflitto con i nomi dichiarati.</span><span class="sxs-lookup"><span data-stu-id="66c41-196">In other cases, such as with the identifier "`var`" in implicitly typed local variable declarations ([Local variable declarations](statements.md#local-variable-declarations)), a contextual keyword can conflict with declared names.</span></span> <span data-ttu-id="66c41-197">In questi casi, il nome dichiarato ha la precedenza sull'utilizzo dell'identificatore come parola chiave contestuale.</span><span class="sxs-lookup"><span data-stu-id="66c41-197">In such cases, the declared name takes precedence over the use of the identifier as a contextual keyword.</span></span>

### <a name="literals"></a><span data-ttu-id="66c41-198">Valori letterali</span><span class="sxs-lookup"><span data-stu-id="66c41-198">Literals</span></span>

<span data-ttu-id="66c41-199">Un valore ***letterale*** è una rappresentazione del codice sorgente di un valore.</span><span class="sxs-lookup"><span data-stu-id="66c41-199">A ***literal*** is a source code representation of a value.</span></span>

```antlr
literal
    : boolean_literal
    | integer_literal
    | real_literal
    | character_literal
    | string_literal
    | null_literal
    ;
```

#### <a name="boolean-literals"></a><span data-ttu-id="66c41-200">Valori letterali booleani</span><span class="sxs-lookup"><span data-stu-id="66c41-200">Boolean literals</span></span>

<span data-ttu-id="66c41-201">Sono presenti due valori letterali booleani `true` : `false`e.</span><span class="sxs-lookup"><span data-stu-id="66c41-201">There are two boolean literal values: `true` and `false`.</span></span>

```antlr
boolean_literal
    : 'true'
    | 'false'
    ;
```

<span data-ttu-id="66c41-202">Il tipo di *boolean_literal* è `bool`.</span><span class="sxs-lookup"><span data-stu-id="66c41-202">The type of a *boolean_literal* is `bool`.</span></span>

#### <a name="integer-literals"></a><span data-ttu-id="66c41-203">Valori letterali Integer</span><span class="sxs-lookup"><span data-stu-id="66c41-203">Integer literals</span></span>

<span data-ttu-id="66c41-204">I valori letterali integer vengono usati per scrivere valori `int`di `uint`tipo `long`,, `ulong`e.</span><span class="sxs-lookup"><span data-stu-id="66c41-204">Integer literals are used to write values of types `int`, `uint`, `long`, and `ulong`.</span></span> <span data-ttu-id="66c41-205">I valori letterali integer hanno due formati possibili: Decimal ed esadecimale.</span><span class="sxs-lookup"><span data-stu-id="66c41-205">Integer literals have two possible forms: decimal and hexadecimal.</span></span>

```antlr
integer_literal
    : decimal_integer_literal
    | hexadecimal_integer_literal
    ;

decimal_integer_literal
    : decimal_digit+ integer_type_suffix?
    ;

decimal_digit
    : '0' | '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' | '9'
    ;

integer_type_suffix
    : 'U' | 'u' | 'L' | 'l' | 'UL' | 'Ul' | 'uL' | 'ul' | 'LU' | 'Lu' | 'lU' | 'lu'
    ;

hexadecimal_integer_literal
    : '0x' hex_digit+ integer_type_suffix?
    | '0X' hex_digit+ integer_type_suffix?
    ;

hex_digit
    : '0' | '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' | '9'
    | 'A' | 'B' | 'C' | 'D' | 'E' | 'F' | 'a' | 'b' | 'c' | 'd' | 'e' | 'f';
```

<span data-ttu-id="66c41-206">Il tipo di un valore letterale integer viene determinato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="66c41-206">The type of an integer literal is determined as follows:</span></span>

*  <span data-ttu-id="66c41-207">Se il valore letterale non ha alcun suffisso, presenta il primo di questi tipi in cui può essere rappresentato `int`il `uint`relativo valore `ulong`:,, `long`,.</span><span class="sxs-lookup"><span data-stu-id="66c41-207">If the literal has no suffix, it has the first of these types in which its value can be represented: `int`, `uint`, `long`, `ulong`.</span></span>
*  <span data-ttu-id="66c41-208">Se il valore letterale è suffisso `u`da `U` o, dispone del primo di questi tipi in cui può essere rappresentato il relativo `uint`valore `ulong`:,.</span><span class="sxs-lookup"><span data-stu-id="66c41-208">If the literal is suffixed by `U` or `u`, it has the first of these types in which its value can be represented: `uint`, `ulong`.</span></span>
*  <span data-ttu-id="66c41-209">Se il valore letterale è suffisso `l`da `L` o, dispone del primo di questi tipi in cui può essere rappresentato il relativo `long`valore `ulong`:,.</span><span class="sxs-lookup"><span data-stu-id="66c41-209">If the literal is suffixed by `L` or `l`, it has the first of these types in which its value can be represented: `long`, `ulong`.</span></span>
*  <span data-ttu-id="66c41-210">Se il valore letterale è suffisso `Ul`da `uL` `UL`, `ul`, `LU`, `Lu`, `lU`,, `lu`o, è di tipo `ulong`.</span><span class="sxs-lookup"><span data-stu-id="66c41-210">If the literal is suffixed by `UL`, `Ul`, `uL`, `ul`, `LU`, `Lu`, `lU`, or `lu`, it is of type `ulong`.</span></span>

<span data-ttu-id="66c41-211">Se il valore rappresentato da un valore letterale integer non è compreso nell' `ulong` intervallo del tipo, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-211">If the value represented by an integer literal is outside the range of the `ulong` type, a compile-time error occurs.</span></span>

<span data-ttu-id="66c41-212">Come una questione di stile, si consiglia di usare "`L`" invece di "`l`" durante la scrittura di valori letterali di `long`tipo, poiché è facile confondere la lettera "`l`" con la cifra "`1`".</span><span class="sxs-lookup"><span data-stu-id="66c41-212">As a matter of style, it is suggested that "`L`" be used instead of "`l`" when writing literals of type `long`, since it is easy to confuse the letter "`l`" with the digit "`1`".</span></span>

<span data-ttu-id="66c41-213">Per consentire la scrittura dei valori `int` più `long` piccoli e possibili come valori letterali integer decimali, sono disponibili le due regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="66c41-213">To permit the smallest possible `int` and `long` values to be written as decimal integer literals, the following two rules exist:</span></span>

* <span data-ttu-id="66c41-214">Quando un *decimal_integer_literal* con valore 2147483648 (2 ^ 31) e nessun *integer_type_suffix* viene visualizzato come token immediatamente successivo a un token operatore meno unario ([operatore unario meno](expressions.md#unary-minus-operator)), il risultato è una costante di tipo `int`con il valore-2147483648 (-2 ^ 31).</span><span class="sxs-lookup"><span data-stu-id="66c41-214">When a *decimal_integer_literal* with the value 2147483648 (2^31) and no *integer_type_suffix* appears as the token immediately following a unary minus operator token ([Unary minus operator](expressions.md#unary-minus-operator)), the result is a constant of type `int` with the value -2147483648 (-2^31).</span></span> <span data-ttu-id="66c41-215">In tutti gli altri casi, tale *decimal_integer_literal* è di tipo `uint`.</span><span class="sxs-lookup"><span data-stu-id="66c41-215">In all other situations, such a *decimal_integer_literal* is of type `uint`.</span></span>
* <span data-ttu-id="66c41-216">Quando un *decimal_integer_literal* con il valore 9.223.372.036.854.775.808 (2 ^ 63) e nessun *integer_type_suffix* o *integer_type_suffix* `L` o `l` viene visualizzato come token immediatamente dopo un meno unario token operatore ([operatore unario meno](expressions.md#unary-minus-operator)), il risultato è una costante di `long` tipo con il valore-9.223.372.036.854.775.808 (-2 ^ 63).</span><span class="sxs-lookup"><span data-stu-id="66c41-216">When a *decimal_integer_literal* with the value 9223372036854775808 (2^63) and no *integer_type_suffix* or the *integer_type_suffix* `L` or `l` appears as the token immediately following a unary minus operator token ([Unary minus operator](expressions.md#unary-minus-operator)), the result is a constant of type `long` with the value -9223372036854775808 (-2^63).</span></span> <span data-ttu-id="66c41-217">In tutti gli altri casi, tale *decimal_integer_literal* è di tipo `ulong`.</span><span class="sxs-lookup"><span data-stu-id="66c41-217">In all other situations, such a *decimal_integer_literal* is of type `ulong`.</span></span>

#### <a name="real-literals"></a><span data-ttu-id="66c41-218">Valori letterali reali</span><span class="sxs-lookup"><span data-stu-id="66c41-218">Real literals</span></span>

<span data-ttu-id="66c41-219">I valori letterali reali vengono usati per scrivere valori `float`di `double`tipo, `decimal`e.</span><span class="sxs-lookup"><span data-stu-id="66c41-219">Real literals are used to write values of types `float`, `double`, and `decimal`.</span></span>

```antlr
real_literal
    : decimal_digit+ '.' decimal_digit+ exponent_part? real_type_suffix?
    | '.' decimal_digit+ exponent_part? real_type_suffix?
    | decimal_digit+ exponent_part real_type_suffix?
    | decimal_digit+ real_type_suffix
    ;

exponent_part
    : 'e' sign? decimal_digit+
    | 'E' sign? decimal_digit+
    ;

sign
    : '+'
    | '-'
    ;

real_type_suffix
    : 'F' | 'f' | 'D' | 'd' | 'M' | 'm'
    ;
```

<span data-ttu-id="66c41-220">Se non viene specificato alcun *real_type_suffix* , il tipo del valore letterale `double`reale è.</span><span class="sxs-lookup"><span data-stu-id="66c41-220">If no *real_type_suffix* is specified, the type of the real literal is `double`.</span></span> <span data-ttu-id="66c41-221">In caso contrario, il suffisso di tipo reale determina il tipo del valore letterale reale, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="66c41-221">Otherwise, the real type suffix determines the type of the real literal, as follows:</span></span>

*  <span data-ttu-id="66c41-222">Un valore letterale reale con `F` suffisso o `f` è `float`di tipo.</span><span class="sxs-lookup"><span data-stu-id="66c41-222">A real literal suffixed by `F` or `f` is of type `float`.</span></span> <span data-ttu-id="66c41-223">Ad esempio, `1f`i valori letterali `1.5f` `1e10f`,, e `123.456F` sono tutti di tipo `float`.</span><span class="sxs-lookup"><span data-stu-id="66c41-223">For example, the literals `1f`, `1.5f`, `1e10f`, and `123.456F` are all of type `float`.</span></span>
*  <span data-ttu-id="66c41-224">Un valore letterale reale con `D` suffisso o `d` è `double`di tipo.</span><span class="sxs-lookup"><span data-stu-id="66c41-224">A real literal suffixed by `D` or `d` is of type `double`.</span></span> <span data-ttu-id="66c41-225">Ad esempio, `1d`i valori letterali `1.5d` `1e10d`,, e `123.456D` sono tutti di tipo `double`.</span><span class="sxs-lookup"><span data-stu-id="66c41-225">For example, the literals `1d`, `1.5d`, `1e10d`, and `123.456D` are all of type `double`.</span></span>
*  <span data-ttu-id="66c41-226">Un valore letterale reale con `M` suffisso o `m` è `decimal`di tipo.</span><span class="sxs-lookup"><span data-stu-id="66c41-226">A real literal suffixed by `M` or `m` is of type `decimal`.</span></span> <span data-ttu-id="66c41-227">Ad esempio, `1m`i valori letterali `1.5m` `1e10m`,, e `123.456M` sono tutti di tipo `decimal`.</span><span class="sxs-lookup"><span data-stu-id="66c41-227">For example, the literals `1m`, `1.5m`, `1e10m`, and `123.456M` are all of type `decimal`.</span></span> <span data-ttu-id="66c41-228">Il valore letterale viene convertito `decimal` in un valore accettando il valore esatto e, se necessario, l'arrotondamento al valore rappresentabile più vicino utilizzando l'arrotondamento del Banker ([il tipo decimale](types.md#the-decimal-type)).</span><span class="sxs-lookup"><span data-stu-id="66c41-228">This literal is converted to a `decimal` value by taking the exact value, and, if necessary, rounding to the nearest representable value using banker's rounding ([The decimal type](types.md#the-decimal-type)).</span></span> <span data-ttu-id="66c41-229">Qualsiasi scala apparente nel valore letterale viene mantenuta, a meno che il valore non venga arrotondato o se il valore è zero, nel qual caso il segno e la scala saranno pari a 0.</span><span class="sxs-lookup"><span data-stu-id="66c41-229">Any scale apparent in the literal is preserved unless the value is rounded or the value is zero (in which latter case the sign and scale will be 0).</span></span> <span data-ttu-id="66c41-230">Di conseguenza, il `2.900m` valore letterale verrà analizzato in modo da formare `0`il separatore decimale `3`con segno, coefficiente `2900`e scala.</span><span class="sxs-lookup"><span data-stu-id="66c41-230">Hence, the literal `2.900m` will be parsed to form the decimal with sign `0`, coefficient `2900`, and scale `3`.</span></span>

<span data-ttu-id="66c41-231">Se il valore letterale specificato non può essere rappresentato nel tipo indicato, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-231">If the specified literal cannot be represented in the indicated type, a compile-time error occurs.</span></span>

<span data-ttu-id="66c41-232">Il valore di un valore letterale reale `float` di `double` tipo o viene determinato tramite la modalità "arrotonda a" più vicina "di IEEE.</span><span class="sxs-lookup"><span data-stu-id="66c41-232">The value of a real literal of type `float` or `double` is determined by using the IEEE "round to nearest" mode.</span></span>

<span data-ttu-id="66c41-233">Si noti che in un valore letterale reale, le cifre decimali sono sempre obbligatorie dopo il separatore decimale.</span><span class="sxs-lookup"><span data-stu-id="66c41-233">Note that in a real literal, decimal digits are always required after the decimal point.</span></span> <span data-ttu-id="66c41-234">Ad esempio, `1.3F` è un valore letterale `1.F` reale, ma non lo è.</span><span class="sxs-lookup"><span data-stu-id="66c41-234">For example, `1.3F` is a real literal but `1.F` is not.</span></span>

#### <a name="character-literals"></a><span data-ttu-id="66c41-235">Valori letterali carattere</span><span class="sxs-lookup"><span data-stu-id="66c41-235">Character literals</span></span>

<span data-ttu-id="66c41-236">Un valore letterale carattere rappresenta un singolo carattere e in genere è costituito da un carattere tra `'a'`virgolette, come in.</span><span class="sxs-lookup"><span data-stu-id="66c41-236">A character literal represents a single character, and usually consists of a character in quotes, as in `'a'`.</span></span>

<span data-ttu-id="66c41-237">Nota: La notazione della grammatica ANTLR rende più confuse le operazioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="66c41-237">Note: The ANTLR grammar notation makes the following confusing!</span></span> <span data-ttu-id="66c41-238">In ANTLR, quando si scrive `\'` , si tratta di una singola `'`virgoletta.</span><span class="sxs-lookup"><span data-stu-id="66c41-238">In ANTLR, when you write `\'` it stands for a single quote `'`.</span></span> <span data-ttu-id="66c41-239">E quando si scrive `\\` , si sta per una singola `\`barra rovesciata.</span><span class="sxs-lookup"><span data-stu-id="66c41-239">And when you write `\\` it stands for a single backslash `\`.</span></span> <span data-ttu-id="66c41-240">La prima regola per un valore letterale carattere significa quindi che inizia con una virgoletta singola, quindi con un carattere e quindi con una virgoletta singola.</span><span class="sxs-lookup"><span data-stu-id="66c41-240">Therefore the first rule for a character literal means it starts with a single quote, then a character, then a single quote.</span></span> <span data-ttu-id="66c41-241">E le undici possibili sequenze di escape semplici sono `\'` `\\`, `\"` `\0`,,, `\a`, `\b`, `\f`, `\n`, `\r`, `\t` ,`\v`.</span><span class="sxs-lookup"><span data-stu-id="66c41-241">And the eleven possible simple escape sequences are `\'`, `\"`, `\\`, `\0`, `\a`, `\b`, `\f`, `\n`, `\r`, `\t`, `\v`.</span></span>

```antlr
character_literal
    : '\'' character '\''
    ;

character
    : single_character
    | simple_escape_sequence
    | hexadecimal_escape_sequence
    | unicode_escape_sequence
    ;

single_character
    : '<Any character except \' (U+0027), \\ (U+005C), and new_line_character>'
    ;

simple_escape_sequence
    : '\\\'' | '\\"' | '\\\\' | '\\0' | '\\a' | '\\b' | '\\f' | '\\n' | '\\r' | '\\t' | '\\v'
    ;

hexadecimal_escape_sequence
    : '\\x' hex_digit hex_digit? hex_digit? hex_digit?;
```

<span data-ttu-id="66c41-242">Un carattere che segue un carattere barra rovesciata (`\`) in un *carattere* deve essere uno dei `'` `\`caratteri seguenti: `"`,, `0`, `a`, `b`, `f` , , `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span><span class="sxs-lookup"><span data-stu-id="66c41-242">A character that follows a backslash character (`\`) in a *character* must be one of the following characters: `'`, `"`, `\`, `0`, `a`, `b`, `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span></span> <span data-ttu-id="66c41-243">In caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-243">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="66c41-244">Una sequenza di escape esadecimale rappresenta un singolo carattere Unicode, con il valore formato dal numero esadecimale`\x`che segue "".</span><span class="sxs-lookup"><span data-stu-id="66c41-244">A hexadecimal escape sequence represents a single Unicode character, with the value formed by the hexadecimal number following "`\x`".</span></span>

<span data-ttu-id="66c41-245">Se il valore rappresentato da un valore letterale carattere è `U+FFFF`maggiore di, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-245">If the value represented by a character literal is greater than `U+FFFF`, a compile-time error occurs.</span></span>

<span data-ttu-id="66c41-246">Una sequenza di escape di caratteri Unicode ([sequenze di escape di caratteri Unicode](lexical-structure.md#unicode-character-escape-sequences)) in un valore letterale carattere `U+0000` deve `U+FFFF`essere compresa nell'intervallo tra e.</span><span class="sxs-lookup"><span data-stu-id="66c41-246">A Unicode character escape sequence ([Unicode character escape sequences](lexical-structure.md#unicode-character-escape-sequences)) in a character literal must be in the range `U+0000` to `U+FFFF`.</span></span>

<span data-ttu-id="66c41-247">Una semplice sequenza di escape rappresenta una codifica dei caratteri Unicode, come descritto nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="66c41-247">A simple escape sequence represents a Unicode character encoding, as described in the table below.</span></span>


| <span data-ttu-id="66c41-248">__Sequenza di escape__</span><span class="sxs-lookup"><span data-stu-id="66c41-248">__Escape sequence__</span></span> | <span data-ttu-id="66c41-249">__Nome carattere__</span><span class="sxs-lookup"><span data-stu-id="66c41-249">__Character name__</span></span> | <span data-ttu-id="66c41-250">__Codifica Unicode__</span><span class="sxs-lookup"><span data-stu-id="66c41-250">__Unicode encoding__</span></span> |
|---------------------|--------------------|----------------------|
| `\'`                | <span data-ttu-id="66c41-251">Virgoletta singola</span><span class="sxs-lookup"><span data-stu-id="66c41-251">Single quote</span></span>       | `0x0027`             | 
| `\"`                | <span data-ttu-id="66c41-252">Virgoletta doppia</span><span class="sxs-lookup"><span data-stu-id="66c41-252">Double quote</span></span>       | `0x0022`             | 
| <span data-ttu-id="66c41-253">`\\`| Barra rovesciata |`0x005C`</span><span class="sxs-lookup"><span data-stu-id="66c41-253">`\\`                | Backslash          | `0x005C`</span></span>             | 
| `\0`                | <span data-ttu-id="66c41-254">Null</span><span class="sxs-lookup"><span data-stu-id="66c41-254">Null</span></span>               | `0x0000`             | 
| `\a`                | <span data-ttu-id="66c41-255">Avviso</span><span class="sxs-lookup"><span data-stu-id="66c41-255">Alert</span></span>              | `0x0007`             | 
| `\b`                | <span data-ttu-id="66c41-256">Backspace</span><span class="sxs-lookup"><span data-stu-id="66c41-256">Backspace</span></span>          | `0x0008`             | 
| `\f`                | <span data-ttu-id="66c41-257">Avanzamento carta</span><span class="sxs-lookup"><span data-stu-id="66c41-257">Form feed</span></span>          | `0x000C`             | 
| `\n`                | <span data-ttu-id="66c41-258">Nuova riga</span><span class="sxs-lookup"><span data-stu-id="66c41-258">New line</span></span>           | `0x000A`             | 
| `\r`                | <span data-ttu-id="66c41-259">Ritorno a capo</span><span class="sxs-lookup"><span data-stu-id="66c41-259">Carriage return</span></span>    | `0x000D`             | 
| `\t`                | <span data-ttu-id="66c41-260">Tabulazione orizzontale</span><span class="sxs-lookup"><span data-stu-id="66c41-260">Horizontal tab</span></span>     | `0x0009`             | 
| `\v`                | <span data-ttu-id="66c41-261">Tabulazione verticale</span><span class="sxs-lookup"><span data-stu-id="66c41-261">Vertical tab</span></span>       | `0x000B`             | 

<span data-ttu-id="66c41-262">Il tipo di *character_literal* è `char`.</span><span class="sxs-lookup"><span data-stu-id="66c41-262">The type of a *character_literal* is `char`.</span></span>

#### <a name="string-literals"></a><span data-ttu-id="66c41-263">Valori letterali stringa</span><span class="sxs-lookup"><span data-stu-id="66c41-263">String literals</span></span>

<span data-ttu-id="66c41-264">C#supporta due forme di valori letterali stringa: ***valori letterali stringa normali*** e ***valori letterali stringa Verbatim***.</span><span class="sxs-lookup"><span data-stu-id="66c41-264">C# supports two forms of string literals: ***regular string literals*** and ***verbatim string literals***.</span></span>

<span data-ttu-id="66c41-265">Un valore letterale stringa regolare è costituito da zero o più caratteri racchiusi tra `"hello"`virgolette doppie, come in, e può includere sia sequenze `\t` di escape semplici (ad esempio per il carattere di tabulazione) che sequenze di escape esadecimali e Unicode.</span><span class="sxs-lookup"><span data-stu-id="66c41-265">A regular string literal consists of zero or more characters enclosed in double quotes, as in `"hello"`, and may include both simple escape sequences (such as `\t` for the tab character), and hexadecimal and Unicode escape sequences.</span></span>

<span data-ttu-id="66c41-266">Un valore letterale stringa Verbatim è `@` costituito da un carattere seguito da un carattere di virgolette doppie, da zero o più caratteri e da un carattere di virgolette doppie di chiusura.</span><span class="sxs-lookup"><span data-stu-id="66c41-266">A verbatim string literal consists of an `@` character followed by a double-quote character, zero or more characters, and a closing double-quote character.</span></span> <span data-ttu-id="66c41-267">Un esempio semplice è `@"hello"`.</span><span class="sxs-lookup"><span data-stu-id="66c41-267">A simple example is `@"hello"`.</span></span> <span data-ttu-id="66c41-268">In un valore letterale stringa Verbatim i caratteri compresi tra i delimitatori vengono interpretati come Verbatim, l'unica eccezione è rappresentata da un *quote_escape_sequence*.</span><span class="sxs-lookup"><span data-stu-id="66c41-268">In a verbatim string literal, the characters between the delimiters are interpreted verbatim, the only exception being a *quote_escape_sequence*.</span></span> <span data-ttu-id="66c41-269">In particolare, le sequenze di escape semplici e le sequenze di escape esadecimali e Unicode non vengono elaborate nei valori letterali stringa Verbatim.</span><span class="sxs-lookup"><span data-stu-id="66c41-269">In particular, simple escape sequences, and hexadecimal and Unicode escape sequences are not processed in verbatim string literals.</span></span> <span data-ttu-id="66c41-270">Un valore letterale stringa Verbatim può estendersi su più righe.</span><span class="sxs-lookup"><span data-stu-id="66c41-270">A verbatim string literal may span multiple lines.</span></span>

```antlr
string_literal
    : regular_string_literal
    | verbatim_string_literal
    ;

regular_string_literal
    : '"' regular_string_literal_character* '"'
    ;

regular_string_literal_character
    : single_regular_string_literal_character
    | simple_escape_sequence
    | hexadecimal_escape_sequence
    | unicode_escape_sequence
    ;

single_regular_string_literal_character
    : '<Any character except " (U+0022), \\ (U+005C), and new_line_character>'
    ;

verbatim_string_literal
    : '@"' verbatim_string_literal_character* '"'
    ;

verbatim_string_literal_character
    : single_verbatim_string_literal_character
    | quote_escape_sequence
    ;

single_verbatim_string_literal_character
    : '<any character except ">'
    ;

quote_escape_sequence
    : '""'
    ;
```

<span data-ttu-id="66c41-271">`\`Un carattere che segue un carattere barra rovesciata () in una *regular_string_literal_character* deve essere uno dei `'` `\`caratteri seguenti: `"`,, `0`, `a`, `b` , , `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span><span class="sxs-lookup"><span data-stu-id="66c41-271">A character that follows a backslash character (`\`) in a *regular_string_literal_character* must be one of the following characters: `'`, `"`, `\`, `0`, `a`, `b`, `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span></span> <span data-ttu-id="66c41-272">In caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-272">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="66c41-273">Esempio</span><span class="sxs-lookup"><span data-stu-id="66c41-273">The example</span></span>
```csharp
string a = "hello, world";                   // hello, world
string b = @"hello, world";                  // hello, world

string c = "hello \t world";                 // hello      world
string d = @"hello \t world";                // hello \t world

string e = "Joe said \"Hello\" to me";       // Joe said "Hello" to me
string f = @"Joe said ""Hello"" to me";      // Joe said "Hello" to me

string g = "\\\\server\\share\\file.txt";    // \\server\share\file.txt
string h = @"\\server\share\file.txt";       // \\server\share\file.txt

string i = "one\r\ntwo\r\nthree";
string j = @"one
two
three";
```
<span data-ttu-id="66c41-274">Mostra un'ampia gamma di valori letterali stringa.</span><span class="sxs-lookup"><span data-stu-id="66c41-274">shows a variety of string literals.</span></span> <span data-ttu-id="66c41-275">L'ultimo valore letterale `j`stringa,, è un valore letterale stringa Verbatim che si estende su più righe.</span><span class="sxs-lookup"><span data-stu-id="66c41-275">The last string literal, `j`, is a verbatim string literal that spans multiple lines.</span></span> <span data-ttu-id="66c41-276">I caratteri tra virgolette, inclusi gli spazi vuoti, ad esempio i caratteri di nuova riga, vengono conservati testualmente.</span><span class="sxs-lookup"><span data-stu-id="66c41-276">The characters between the quotation marks, including white space such as new line characters, are preserved verbatim.</span></span>

<span data-ttu-id="66c41-277">Poiché una sequenza di escape esadecimale può avere un numero variabile di cifre esadecimali, `"\x123"` il valore letterale stringa contiene un singolo carattere con valore esadecimale 123.</span><span class="sxs-lookup"><span data-stu-id="66c41-277">Since a hexadecimal escape sequence can have a variable number of hex digits, the string literal `"\x123"` contains a single character with hex value 123.</span></span> <span data-ttu-id="66c41-278">Per creare una stringa contenente il carattere con valore esadecimale 12 seguito dal carattere 3, è possibile scrivere `"\x00123"` o `"\x12" + "3"` in alternativa.</span><span class="sxs-lookup"><span data-stu-id="66c41-278">To create a string containing the character with hex value 12 followed by the character 3, one could write `"\x00123"` or `"\x12" + "3"` instead.</span></span>

<span data-ttu-id="66c41-279">Il tipo di *string_literal* è `string`.</span><span class="sxs-lookup"><span data-stu-id="66c41-279">The type of a *string_literal* is `string`.</span></span>

<span data-ttu-id="66c41-280">Ogni valore letterale stringa non comporta necessariamente la creazione di una nuova istanza di stringa.</span><span class="sxs-lookup"><span data-stu-id="66c41-280">Each string literal does not necessarily result in a new string instance.</span></span> <span data-ttu-id="66c41-281">Quando nello stesso programma sono presenti due o più valori letterali stringa equivalenti in base all'operatore di uguaglianza delle stringhe ([operatori di uguaglianza di stringa](expressions.md#string-equality-operators)), questi valori letterali stringa fanno riferimento alla stessa istanza di stringa.</span><span class="sxs-lookup"><span data-stu-id="66c41-281">When two or more string literals that are equivalent according to the string equality operator ([String equality operators](expressions.md#string-equality-operators)) appear in the same program, these string literals refer to the same string instance.</span></span> <span data-ttu-id="66c41-282">Ad esempio, l'output prodotto da</span><span class="sxs-lookup"><span data-stu-id="66c41-282">For instance, the output produced by</span></span>
```csharp
class Test
{
    static void Main() {
        object a = "hello";
        object b = "hello";
        System.Console.WriteLine(a == b);
    }
}
```
<span data-ttu-id="66c41-283">è `True` perché i due valori letterali fanno riferimento alla stessa istanza di stringa.</span><span class="sxs-lookup"><span data-stu-id="66c41-283">is `True` because the two literals refer to the same string instance.</span></span>

#### <a name="interpolated-string-literals"></a><span data-ttu-id="66c41-284">Valori letterali stringa interpolati</span><span class="sxs-lookup"><span data-stu-id="66c41-284">Interpolated string literals</span></span>

<span data-ttu-id="66c41-285">I valori letterali stringa interpolati sono simili ai valori letterali stringa, ma contengono `{` fori `}`delimitati da e, dove possono verificarsi espressioni.</span><span class="sxs-lookup"><span data-stu-id="66c41-285">Interpolated string literals are similar to string literals, but contain holes delimited by `{` and `}`, wherein expressions can occur.</span></span> <span data-ttu-id="66c41-286">In fase di esecuzione, le espressioni vengono valutate con lo scopo di inserire i moduli testuali nella stringa in corrispondenza del punto in cui si verifica il foro.</span><span class="sxs-lookup"><span data-stu-id="66c41-286">At runtime, the expressions are evaluated with the purpose of having their textual forms substituted into the string at the place where the hole occurs.</span></span> <span data-ttu-id="66c41-287">La sintassi e la semantica dell'interpolazione di stringhe sono descritte nella sezione ([stringhe interpolate](expressions.md#interpolated-strings)).</span><span class="sxs-lookup"><span data-stu-id="66c41-287">The syntax and semantics of string interpolation are described in section ([Interpolated strings](expressions.md#interpolated-strings)).</span></span>

<span data-ttu-id="66c41-288">Come i valori letterali stringa, i valori letterali stringa interpolati possono essere regolari o Verbatim.</span><span class="sxs-lookup"><span data-stu-id="66c41-288">Like string literals, interpolated string literals can be either regular or verbatim.</span></span> <span data-ttu-id="66c41-289">I valori letterali stringa regolari interpolati sono `$"` delimitati da e `"`e i valori letterali stringa Verbatim interpolati `"`sono delimitati da `$@"` e.</span><span class="sxs-lookup"><span data-stu-id="66c41-289">Interpolated regular string literals are delimited by `$"` and `"`, and interpolated verbatim string literals are delimited by `$@"` and `"`.</span></span>

<span data-ttu-id="66c41-290">Analogamente ad altri valori letterali, l'analisi lessicale di un valore letterale di stringa interpolata inizialmente restituisce un singolo token, in base alla grammatica riportata di seguito.</span><span class="sxs-lookup"><span data-stu-id="66c41-290">Like other literals, lexical analysis of an interpolated string literal initially results in a single token, as per the grammar below.</span></span> <span data-ttu-id="66c41-291">Tuttavia, prima dell'analisi sintattica, il singolo token di un valore letterale stringa interpolato viene suddiviso in diversi token per le parti della stringa che racchiudono i buchi e gli elementi di input che si verificano nei buchi vengono di nuovo analizzati in modo lessicale.</span><span class="sxs-lookup"><span data-stu-id="66c41-291">However, before syntactic analysis, the single token of an interpolated string literal is broken into several tokens for the parts of the string enclosing the holes, and the input elements occurring in the holes are lexically analysed again.</span></span> <span data-ttu-id="66c41-292">Questa operazione può a sua volta produrre più valori letterali stringa interpolati da elaborare, ma, in caso di correzione lessicale, condurrà alla fine una sequenza di token per l'elaborazione dell'analisi sintattica.</span><span class="sxs-lookup"><span data-stu-id="66c41-292">This may in turn produce more interpolated string literals to be processed, but, if lexically correct, will eventually lead to a sequence of tokens for syntactic analysis to process.</span></span>

```antlr
interpolated_string_literal
    : '$' interpolated_regular_string_literal
    | '$' interpolated_verbatim_string_literal
    ;

interpolated_regular_string_literal
    : interpolated_regular_string_whole
    | interpolated_regular_string_start  interpolated_regular_string_literal_body interpolated_regular_string_end
    ;

interpolated_regular_string_literal_body
    : regular_balanced_text
    | interpolated_regular_string_literal_body interpolated_regular_string_mid regular_balanced_text
    ;

interpolated_regular_string_whole
    : '"' interpolated_regular_string_character* '"'
    ;

interpolated_regular_string_start
    : '"' interpolated_regular_string_character* '{'
    ;

interpolated_regular_string_mid
    : interpolation_format? '}' interpolated_regular_string_characters_after_brace? '{'
    ;

interpolated_regular_string_end
    : interpolation_format? '}' interpolated_regular_string_characters_after_brace? '"'
    ;

interpolated_regular_string_characters_after_brace
    : interpolated_regular_string_character_no_brace
    | interpolated_regular_string_characters_after_brace interpolated_regular_string_character
    ;

interpolated_regular_string_character
    : single_interpolated_regular_string_character
    | simple_escape_sequence
    | hexadecimal_escape_sequence
    | unicode_escape_sequence
    | open_brace_escape_sequence
    | close_brace_escape_sequence
    ;

interpolated_regular_string_character_no_brace
    : '<Any interpolated_regular_string_character except close_brace_escape_sequence and any hexadecimal_escape_sequence or unicode_escape_sequence designating } (U+007D)>'
    ;

single_interpolated_regular_string_character
    : '<Any character except \" (U+0022), \\ (U+005C), { (U+007B), } (U+007D), and new_line_character>'
    ;

open_brace_escape_sequence
    : '{{'
    ;

close_brace_escape_sequence
    : '}}'
    ;
    
regular_balanced_text
    : regular_balanced_text_part+
    ;

regular_balanced_text_part
    : single_regular_balanced_text_character
    | delimited_comment
    | '@' identifier_or_keyword
    | string_literal
    | interpolated_string_literal
    | '(' regular_balanced_text ')'
    | '[' regular_balanced_text ']'
    | '{' regular_balanced_text '}'
    ;
    
single_regular_balanced_text_character
    : '<Any character except / (U+002F), @ (U+0040), \" (U+0022), $ (U+0024), ( (U+0028), ) (U+0029), [ (U+005B), ] (U+005D), { (U+007B), } (U+007D) and new_line_character>'
    | '</ (U+002F), if not directly followed by / (U+002F) or * (U+002A)>'
    ;
    
interpolation_format
    : interpolation_format_character+
    ;
    
interpolation_format_character
    : '<Any character except \" (U+0022), : (U+003A), { (U+007B) and } (U+007D)>'
    ;
    
interpolated_verbatim_string_literal
    : interpolated_verbatim_string_whole
    | interpolated_verbatim_string_start interpolated_verbatim_string_literal_body interpolated_verbatim_string_end
    ;

interpolated_verbatim_string_literal_body
    : verbatim_balanced_text
    | interpolated_verbatim_string_literal_body interpolated_verbatim_string_mid verbatim_balanced_text
    ;
    
interpolated_verbatim_string_whole
    : '@"' interpolated_verbatim_string_character* '"'
    ;
    
interpolated_verbatim_string_start
    : '@"' interpolated_verbatim_string_character* '{'
    ;
    
interpolated_verbatim_string_mid
    : interpolation_format? '}' interpolated_verbatim_string_characters_after_brace? '{'
    ;
    
interpolated_verbatim_string_end
    : interpolation_format? '}' interpolated_verbatim_string_characters_after_brace? '"'
    ;
    
interpolated_verbatim_string_characters_after_brace
    : interpolated_verbatim_string_character_no_brace
    | interpolated_verbatim_string_characters_after_brace interpolated_verbatim_string_character
    ;
    
interpolated_verbatim_string_character
    : single_interpolated_verbatim_string_character
    | quote_escape_sequence
    | open_brace_escape_sequence
    | close_brace_escape_sequence
    ;
    
interpolated_verbatim_string_character_no_brace
    : '<Any interpolated_verbatim_string_character except close_brace_escape_sequence>'
    ;
    
single_interpolated_verbatim_string_character
    : '<Any character except \" (U+0022), { (U+007B) and } (U+007D)>'
    ;
    
verbatim_balanced_text
    : verbatim_balanced_text_part+
    ;

verbatim_balanced_text_part
    : single_verbatim_balanced_text_character
    | comment
    | '@' identifier_or_keyword
    | string_literal
    | interpolated_string_literal
    | '(' verbatim_balanced_text ')'
    | '[' verbatim_balanced_text ']'
    | '{' verbatim_balanced_text '}'
    ;
    
single_verbatim_balanced_text_character
    : '<Any character except / (U+002F), @ (U+0040), \" (U+0022), $ (U+0024), ( (U+0028), ) (U+0029), [ (U+005B), ] (U+005D), { (U+007B) and } (U+007D)>'
    | '</ (U+002F), if not directly followed by / (U+002F) or * (U+002A)>'
    ;
```

<span data-ttu-id="66c41-293">Un token *interpolated_string_literal* viene reinterpretato come più token e altri elementi di input come indicato di seguito, in ordine di occorrenza in *interpolated_string_literal*:</span><span class="sxs-lookup"><span data-stu-id="66c41-293">An *interpolated_string_literal* token is reinterpreted as multiple tokens and other input elements as follows, in order of occurrence in the *interpolated_string_literal*:</span></span>

* <span data-ttu-id="66c41-294">Le occorrenze dei seguenti vengono riinterpretate come singoli token separati: il segno principale `$` , *interpolated_regular_string_whole*, *interpolated_regular_string_start*, *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_whole*, *interpolated_verbatim_string_start*, *interpolated_verbatim_string_mid* e *interpolated_verbatim_string_end*.</span><span class="sxs-lookup"><span data-stu-id="66c41-294">Occurrences of the following are reinterpreted as separate individual tokens: the leading `$` sign, *interpolated_regular_string_whole*, *interpolated_regular_string_start*, *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_whole*, *interpolated_verbatim_string_start*, *interpolated_verbatim_string_mid* and *interpolated_verbatim_string_end*.</span></span>
* <span data-ttu-id="66c41-295">Le occorrenze di *regular_balanced_text* e *verbatim_balanced_text* tra questi vengono rielaborate come *input_section* ([analisi lessicale](lexical-structure.md#lexical-analysis)) e vengono riinterpretate come la sequenza risultante di elementi di input.</span><span class="sxs-lookup"><span data-stu-id="66c41-295">Occurrences of *regular_balanced_text* and *verbatim_balanced_text* between these are reprocessed as an *input_section* ([Lexical analysis](lexical-structure.md#lexical-analysis)) and are reinterpreted as the resulting sequence of input elements.</span></span> <span data-ttu-id="66c41-296">Questi possono a sua volta includere token letterali stringa interpolati da riinterpretare.</span><span class="sxs-lookup"><span data-stu-id="66c41-296">These may in turn include interpolated string literal tokens to be reinterpreted.</span></span>

<span data-ttu-id="66c41-297">Con l'analisi sintattica i token vengono ricombinati in un *interpolated_string_expression* ([stringhe interpolate](expressions.md#interpolated-strings)).</span><span class="sxs-lookup"><span data-stu-id="66c41-297">Syntactic analysis will recombine the tokens into an *interpolated_string_expression* ([Interpolated strings](expressions.md#interpolated-strings)).</span></span>

<span data-ttu-id="66c41-298">Esempi TODO</span><span class="sxs-lookup"><span data-stu-id="66c41-298">Examples TODO</span></span>


#### <a name="the-null-literal"></a><span data-ttu-id="66c41-299">Valore letterale null</span><span class="sxs-lookup"><span data-stu-id="66c41-299">The null literal</span></span>

```antlr
null_literal
    : 'null'
    ;
```

<span data-ttu-id="66c41-300">*Null_literal* può essere convertito in modo implicito in un tipo di riferimento o un tipo Nullable.</span><span class="sxs-lookup"><span data-stu-id="66c41-300">The  *null_literal* can be implicitly converted to a reference type or nullable type.</span></span>

### <a name="operators-and-punctuators"></a><span data-ttu-id="66c41-301">Operatori e segni di punteggiatura</span><span class="sxs-lookup"><span data-stu-id="66c41-301">Operators and punctuators</span></span>

<span data-ttu-id="66c41-302">Esistono diversi tipi di operatori e segni di punteggiatura.</span><span class="sxs-lookup"><span data-stu-id="66c41-302">There are several kinds of operators and punctuators.</span></span> <span data-ttu-id="66c41-303">Gli operatori vengono utilizzati nelle espressioni per descrivere le operazioni che coinvolgono uno o più operandi.</span><span class="sxs-lookup"><span data-stu-id="66c41-303">Operators are used in expressions to describe operations involving one or more operands.</span></span> <span data-ttu-id="66c41-304">Ad esempio, l'espressione `a + b` usa l' `+` operatore per `a` aggiungere i due operandi e `b`.</span><span class="sxs-lookup"><span data-stu-id="66c41-304">For example, the expression `a + b` uses the `+` operator to add the two operands `a` and `b`.</span></span> <span data-ttu-id="66c41-305">I segni di punteggiatura sono per il raggruppamento e la separazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-305">Punctuators are for grouping and separating.</span></span>

```antlr
operator_or_punctuator
    : '{'  | '}'  | '['  | ']'  | '('   | ')'  | '.'  | ','  | ':'  | ';'
    | '+'  | '-'  | '*'  | '/'  | '%'   | '&'  | '|'  | '^'  | '!'  | '~'
    | '='  | '<'  | '>'  | '?'  | '??'  | '::' | '++' | '--' | '&&' | '||'
    | '->' | '==' | '!=' | '<=' | '>='  | '+=' | '-=' | '*=' | '/=' | '%='
    | '&=' | '|=' | '^=' | '<<' | '<<=' | '=>'
    ;

right_shift
    : '>>'
    ;

right_shift_assignment
    : '>>='
    ;
```

<span data-ttu-id="66c41-306">La barra verticale nelle produzioni *right_shift* e *right_shift_assignment* viene usata per indicare che, a differenza di altre produzioni nella grammatica sintattica, non sono consentiti caratteri di qualsiasi tipo (non anche uno spazio vuoto) tra i token.</span><span class="sxs-lookup"><span data-stu-id="66c41-306">The vertical bar in the *right_shift* and *right_shift_assignment* productions are used to indicate that, unlike other productions in the syntactic grammar, no characters of any kind (not even whitespace) are allowed between the tokens.</span></span> <span data-ttu-id="66c41-307">Queste produzioni vengono trattate in modo specifico per consentire la gestione corretta di *type_parameter_list*s ([parametri di tipo](classes.md#type-parameters)).</span><span class="sxs-lookup"><span data-stu-id="66c41-307">These productions are treated specially in order to enable the correct  handling of *type_parameter_list*s ([Type parameters](classes.md#type-parameters)).</span></span>

## <a name="pre-processing-directives"></a><span data-ttu-id="66c41-308">Direttive di pre-elaborazione</span><span class="sxs-lookup"><span data-stu-id="66c41-308">Pre-processing directives</span></span>

<span data-ttu-id="66c41-309">Le direttive di pre-elaborazione offrono la possibilità di ignorare in modo condizionale le sezioni dei file di origine, di segnalare le condizioni di errore e di avviso e di delineare aree distinte del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="66c41-309">The pre-processing directives provide the ability to conditionally skip sections of source files, to report error and warning conditions, and to delineate distinct regions of source code.</span></span> <span data-ttu-id="66c41-310">Il termine "direttive di pre-elaborazione" viene usato solo per coerenza con i linguaggi C++ di programmazione C e.</span><span class="sxs-lookup"><span data-stu-id="66c41-310">The term "pre-processing directives" is used only for consistency with the C and C++ programming languages.</span></span> <span data-ttu-id="66c41-311">In C#non è presente alcun passaggio di pre-elaborazione separato. le direttive di pre-elaborazione vengono elaborate come parte della fase di analisi lessicale.</span><span class="sxs-lookup"><span data-stu-id="66c41-311">In C#, there is no separate pre-processing step; pre-processing directives are processed as part of the lexical analysis phase.</span></span>

```antlr
pp_directive
    : pp_declaration
    | pp_conditional
    | pp_line
    | pp_diagnostic
    | pp_region
    | pp_pragma
    ;
```

<span data-ttu-id="66c41-312">Sono disponibili le seguenti direttive di pre-elaborazione:</span><span class="sxs-lookup"><span data-stu-id="66c41-312">The following pre-processing directives are available:</span></span>

*  <span data-ttu-id="66c41-313">`#define`e `#undef`, che vengono usati per definire e annullare la definizione, rispettivamente, dei simboli di compilazione condizionale ([direttive di dichiarazione](lexical-structure.md#declaration-directives)).</span><span class="sxs-lookup"><span data-stu-id="66c41-313">`#define` and `#undef`, which are used to define and undefine, respectively, conditional compilation symbols ([Declaration directives](lexical-structure.md#declaration-directives)).</span></span>
*  <span data-ttu-id="66c41-314">`#if`, `#elif`, `#else` e`#endif`, che vengono usati per ignorare in modo condizionale le sezioni del codice sorgente ([direttive di compilazione condizionale](lexical-structure.md#conditional-compilation-directives)).</span><span class="sxs-lookup"><span data-stu-id="66c41-314">`#if`, `#elif`, `#else`, and `#endif`, which are used to conditionally skip sections of source code ([Conditional compilation directives](lexical-structure.md#conditional-compilation-directives)).</span></span>
*  <span data-ttu-id="66c41-315">`#line`, utilizzato per controllare i numeri di riga emessi per errori e avvisi ([direttive line](lexical-structure.md#line-directives)).</span><span class="sxs-lookup"><span data-stu-id="66c41-315">`#line`, which is used to control line numbers emitted for errors and warnings ([Line directives](lexical-structure.md#line-directives)).</span></span>
*  <span data-ttu-id="66c41-316">`#error`e `#warning`, che vengono utilizzati per emettere errori e avvisi, rispettivamente ([direttive diagnostiche](lexical-structure.md#diagnostic-directives)).</span><span class="sxs-lookup"><span data-stu-id="66c41-316">`#error` and `#warning`, which are used to issue errors and warnings, respectively ([Diagnostic directives](lexical-structure.md#diagnostic-directives)).</span></span>
*  <span data-ttu-id="66c41-317">`#region`e `#endregion`, che vengono usati per contrassegnare in modo esplicito le sezioni del codice sorgente ([direttive Region](lexical-structure.md#region-directives)).</span><span class="sxs-lookup"><span data-stu-id="66c41-317">`#region` and `#endregion`, which are used to explicitly mark sections of source code ([Region directives](lexical-structure.md#region-directives)).</span></span>
*  <span data-ttu-id="66c41-318">`#pragma`, che viene usato per specificare informazioni contestuali facoltative al compilatore ([direttive pragma](lexical-structure.md#pragma-directives)).</span><span class="sxs-lookup"><span data-stu-id="66c41-318">`#pragma`, which is used to specify optional contextual information to the compiler ([Pragma directives](lexical-structure.md#pragma-directives)).</span></span>

<span data-ttu-id="66c41-319">Una direttiva di pre-elaborazione occupa sempre una riga di codice sorgente separata e inizia sempre con `#` un carattere e un nome di direttiva di pre-elaborazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-319">A pre-processing directive always occupies a separate line of source code and always begins with a `#` character and a pre-processing directive name.</span></span> <span data-ttu-id="66c41-320">Gli spazi vuoti possono verificarsi prima `#` del carattere e tra `#` il carattere e il nome della direttiva.</span><span class="sxs-lookup"><span data-stu-id="66c41-320">White space may occur before the `#` character and between the `#` character and the directive name.</span></span>

<span data-ttu-id="66c41-321">Una riga di origine contenente `#define`una `#undef`direttiva `#if`, `#elif`, `#else`, `#endif`, `#line`,, o`#endregion` può terminare con un commento a riga singola.</span><span class="sxs-lookup"><span data-stu-id="66c41-321">A source line containing a `#define`, `#undef`, `#if`, `#elif`, `#else`, `#endif`, `#line`, or `#endregion` directive may end with a single-line comment.</span></span> <span data-ttu-id="66c41-322">I `/* */` commenti delimitati (stile dei commenti) non sono consentiti nelle righe di origine contenenti direttive di pre-elaborazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-322">Delimited comments (the `/* */` style of comments) are not permitted on source lines containing pre-processing directives.</span></span>

<span data-ttu-id="66c41-323">Le direttive di pre-elaborazione non sono token e non fanno parte della grammatica sintattica C#di.</span><span class="sxs-lookup"><span data-stu-id="66c41-323">Pre-processing directives are not tokens and are not part of the syntactic grammar of C#.</span></span> <span data-ttu-id="66c41-324">Tuttavia, le direttive di pre-elaborazione possono essere usate per includere o escludere sequenze di token e possono in questo modo influenzare il significato di C# un programma.</span><span class="sxs-lookup"><span data-stu-id="66c41-324">However, pre-processing directives can be used to include or exclude sequences of tokens and can in that way affect the meaning of a C# program.</span></span> <span data-ttu-id="66c41-325">Ad esempio, durante la compilazione, il programma:</span><span class="sxs-lookup"><span data-stu-id="66c41-325">For example, when compiled, the program:</span></span>
```csharp
#define A
#undef B

class C
{
#if A
    void F() {}
#else
    void G() {}
#endif

#if B
    void H() {}
#else
    void I() {}
#endif
}
```
<span data-ttu-id="66c41-326">restituisce esattamente la stessa sequenza di token del programma:</span><span class="sxs-lookup"><span data-stu-id="66c41-326">results in the exact same sequence of tokens as the program:</span></span>
```csharp
class C
{
    void F() {}
    void I() {}
}
```

<span data-ttu-id="66c41-327">Pertanto, mentre lessicalmente, i due programmi sono piuttosto diversi, sintatticamente identici.</span><span class="sxs-lookup"><span data-stu-id="66c41-327">Thus, whereas lexically, the two programs are quite different, syntactically, they are identical.</span></span>

### <a name="conditional-compilation-symbols"></a><span data-ttu-id="66c41-328">Simboli di compilazione condizionale</span><span class="sxs-lookup"><span data-stu-id="66c41-328">Conditional compilation symbols</span></span>

<span data-ttu-id="66c41-329">La funzionalità di compilazione condizionale fornita `#if`dalle `#elif`direttive, `#endif` , `#else`e è controllata tramite espressioni di pre-elaborazione ([espressioni di pre-elaborazione](lexical-structure.md#pre-processing-expressions)) e condizionale simboli di compilazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-329">The conditional compilation functionality provided by the `#if`, `#elif`, `#else`, and `#endif` directives is controlled through pre-processing expressions ([Pre-processing expressions](lexical-structure.md#pre-processing-expressions)) and conditional compilation symbols.</span></span>

```antlr
conditional_symbol
    : '<Any identifier_or_keyword except true or false>'
    ;
```

<span data-ttu-id="66c41-330">Un simbolo di compilazione condizionale ha due stati possibili: ***definito*** o non ***definito***.</span><span class="sxs-lookup"><span data-stu-id="66c41-330">A conditional compilation symbol has two possible states: ***defined*** or ***undefined***.</span></span> <span data-ttu-id="66c41-331">All'inizio dell'elaborazione lessicale di un file di origine, un simbolo di compilazione condizionale non è definito, a meno che non sia stato definito in modo esplicito da un meccanismo esterno, ad esempio un'opzione del compilatore da riga di comando.</span><span class="sxs-lookup"><span data-stu-id="66c41-331">At the beginning of the lexical processing of a source file, a conditional compilation symbol is undefined unless it has been explicitly defined by an external mechanism (such as a command-line compiler option).</span></span> <span data-ttu-id="66c41-332">Quando una `#define` direttiva viene elaborata, il simbolo di compilazione condizionale denominato in tale direttiva viene definito in tale file di origine.</span><span class="sxs-lookup"><span data-stu-id="66c41-332">When a `#define` directive is processed, the conditional compilation symbol named in that directive becomes defined in that source file.</span></span> <span data-ttu-id="66c41-333">Il simbolo rimane definito fino a `#undef` quando non viene elaborata una direttiva per lo stesso simbolo o fino a quando non viene raggiunta la fine del file di origine.</span><span class="sxs-lookup"><span data-stu-id="66c41-333">The symbol remains defined until an `#undef` directive for that same symbol is processed, or until the end of the source file is reached.</span></span> <span data-ttu-id="66c41-334">Ciò implica che `#define` le direttive e `#undef` in un file di origine non hanno effetto su altri file di origine nello stesso programma.</span><span class="sxs-lookup"><span data-stu-id="66c41-334">An implication of this is that `#define` and `#undef` directives in one source file have no effect on other source files in the same program.</span></span>

<span data-ttu-id="66c41-335">Quando viene fatto riferimento in un'espressione di pre-elaborazione, un simbolo di compilazione condizionale definito `true`presenta il valore booleano e un simbolo di compilazione condizionale `false`non definito ha il valore booleano.</span><span class="sxs-lookup"><span data-stu-id="66c41-335">When referenced in a pre-processing expression, a defined conditional compilation symbol has the boolean value `true`, and an undefined conditional compilation symbol has the boolean value `false`.</span></span> <span data-ttu-id="66c41-336">Non è necessario che i simboli di compilazione condizionale vengano dichiarati in modo esplicito prima di farvi riferimento nelle espressioni di pre-elaborazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-336">There is no requirement that conditional compilation symbols be explicitly declared before they are referenced in pre-processing expressions.</span></span> <span data-ttu-id="66c41-337">Al contrario, i simboli non dichiarati sono semplicemente indefiniti e `false`quindi hanno il valore.</span><span class="sxs-lookup"><span data-stu-id="66c41-337">Instead, undeclared symbols are simply undefined and thus have the value `false`.</span></span>

<span data-ttu-id="66c41-338">Lo spazio dei nomi per i simboli di compilazione condizionale è distinto e separato da tutte C# le altre entità denominate di un programma.</span><span class="sxs-lookup"><span data-stu-id="66c41-338">The name space for conditional compilation symbols is distinct and separate from all other named entities in a C# program.</span></span> <span data-ttu-id="66c41-339">È possibile fare riferimento ai simboli di compilazione condizionale `#undef` solo nelle `#define` direttive e e nelle espressioni di pre-elaborazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-339">Conditional compilation symbols can only be referenced in `#define` and `#undef` directives and in pre-processing expressions.</span></span>

### <a name="pre-processing-expressions"></a><span data-ttu-id="66c41-340">Espressioni di pre-elaborazione</span><span class="sxs-lookup"><span data-stu-id="66c41-340">Pre-processing expressions</span></span>

<span data-ttu-id="66c41-341">Le espressioni di pre-elaborazione possono `#if` verificarsi `#elif` nelle direttive e.</span><span class="sxs-lookup"><span data-stu-id="66c41-341">Pre-processing expressions can occur in `#if` and `#elif` directives.</span></span> <span data-ttu-id="66c41-342">Gli operatori `!` `==` ,`&&` , e sonoconsentitinelleespressionidipre-elaborazioneeleparentesipossonoessereutilizzateperilraggruppamento.`||` `!=`</span><span class="sxs-lookup"><span data-stu-id="66c41-342">The operators `!`, `==`, `!=`, `&&` and `||` are permitted in pre-processing expressions, and parentheses may be used for grouping.</span></span>

```antlr
pp_expression
    : whitespace? pp_or_expression whitespace?
    ;

pp_or_expression
    : pp_and_expression
    | pp_or_expression whitespace? '||' whitespace? pp_and_expression
    ;

pp_and_expression
    : pp_equality_expression
    | pp_and_expression whitespace? '&&' whitespace? pp_equality_expression
    ;

pp_equality_expression
    : pp_unary_expression
    | pp_equality_expression whitespace? '==' whitespace? pp_unary_expression
    | pp_equality_expression whitespace? '!=' whitespace? pp_unary_expression
    ;

pp_unary_expression
    : pp_primary_expression
    | '!' whitespace? pp_unary_expression
    ;

pp_primary_expression
    : 'true'
    | 'false'
    | conditional_symbol
    | '(' whitespace? pp_expression whitespace? ')'
    ;
```

<span data-ttu-id="66c41-343">Quando viene fatto riferimento in un'espressione di pre-elaborazione, un simbolo di compilazione condizionale definito `true`presenta il valore booleano e un simbolo di compilazione condizionale `false`non definito ha il valore booleano.</span><span class="sxs-lookup"><span data-stu-id="66c41-343">When referenced in a pre-processing expression, a defined conditional compilation symbol has the boolean value `true`, and an undefined conditional compilation symbol has the boolean value `false`.</span></span>

<span data-ttu-id="66c41-344">La valutazione di un'espressione di pre-elaborazione restituisce sempre un valore booleano.</span><span class="sxs-lookup"><span data-stu-id="66c41-344">Evaluation of a pre-processing expression always yields a boolean value.</span></span> <span data-ttu-id="66c41-345">Le regole di valutazione per un'espressione di pre-elaborazione sono le stesse di quelle per un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)), ad eccezione del fatto che le uniche entità definite dall'utente a cui è possibile fare riferimento sono simboli di compilazione condizionale.</span><span class="sxs-lookup"><span data-stu-id="66c41-345">The rules of evaluation for a pre-processing expression are the same as those for a constant expression ([Constant expressions](expressions.md#constant-expressions)), except that the only user-defined entities that can be referenced are conditional compilation symbols.</span></span>

### <a name="declaration-directives"></a><span data-ttu-id="66c41-346">Direttive di dichiarazione</span><span class="sxs-lookup"><span data-stu-id="66c41-346">Declaration directives</span></span>

<span data-ttu-id="66c41-347">Le direttive di dichiarazione vengono usate per definire o annullare la definizione dei simboli di compilazione condizionale.</span><span class="sxs-lookup"><span data-stu-id="66c41-347">The declaration directives are used to define or undefine conditional compilation symbols.</span></span>

```antlr
pp_declaration
    : whitespace? '#' whitespace? 'define' whitespace conditional_symbol pp_new_line
    | whitespace? '#' whitespace? 'undef' whitespace conditional_symbol pp_new_line
    ;

pp_new_line
    : whitespace? single_line_comment? new_line
    ;
```

<span data-ttu-id="66c41-348">L'elaborazione di una `#define` direttiva determina la definizione del simbolo di compilazione condizionale specificato, a partire dalla riga di origine che segue la direttiva.</span><span class="sxs-lookup"><span data-stu-id="66c41-348">The processing of a `#define` directive causes the given conditional compilation symbol to become defined, starting with the source line that follows the directive.</span></span> <span data-ttu-id="66c41-349">Analogamente, l'elaborazione di `#undef` una direttiva fa sì che il simbolo di compilazione condizionale specificato diventi non definito, a partire dalla riga di origine che segue la direttiva.</span><span class="sxs-lookup"><span data-stu-id="66c41-349">Likewise, the processing of an `#undef` directive causes the given conditional compilation symbol to become undefined, starting with the source line that follows the directive.</span></span>

<span data-ttu-id="66c41-350">Tutte `#define` le `#undef` direttive e in un file di origine devono essere presenti prima del primo *token* ([token](lexical-structure.md#tokens)) nel file di origine. in caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-350">Any `#define` and `#undef` directives in a source file must occur before the first *token* ([Tokens](lexical-structure.md#tokens)) in the source file; otherwise a compile-time error occurs.</span></span> <span data-ttu-id="66c41-351">In termini intuitivi `#define` , `#undef` le direttive e devono precedere qualsiasi "codice reale" nel file di origine.</span><span class="sxs-lookup"><span data-stu-id="66c41-351">In intuitive terms, `#define` and `#undef` directives must precede any "real code" in the source file.</span></span>

<span data-ttu-id="66c41-352">Esempio:</span><span class="sxs-lookup"><span data-stu-id="66c41-352">The example:</span></span>
```csharp
#define Enterprise

#if Professional || Enterprise
    #define Advanced
#endif

namespace Megacorp.Data
{
    #if Advanced
    class PivotTable {...}
    #endif
}
```
<span data-ttu-id="66c41-353">è valido perché le `#define` direttive precedono il primo token (la `namespace` parola chiave) nel file di origine.</span><span class="sxs-lookup"><span data-stu-id="66c41-353">is valid because the `#define` directives precede the first token (the `namespace` keyword) in the source file.</span></span>

<span data-ttu-id="66c41-354">Nell'esempio seguente viene restituito un errore in fase di compilazione perché `#define` un oggetto segue il codice reale:</span><span class="sxs-lookup"><span data-stu-id="66c41-354">The following example results in a compile-time error because a `#define` follows real code:</span></span>
```csharp
#define A
namespace N
{
    #define B
    #if B
    class Class1 {}
    #endif
}
```

<span data-ttu-id="66c41-355">Un `#define` oggetto può definire un simbolo di compilazione condizionale già definito, senza l' `#undef` intervento di tale simbolo.</span><span class="sxs-lookup"><span data-stu-id="66c41-355">A `#define` may define a conditional compilation symbol that is already defined, without there being any intervening `#undef` for that symbol.</span></span> <span data-ttu-id="66c41-356">Nell'esempio seguente viene definito un simbolo `A` di compilazione condizionale che viene quindi definito nuovamente.</span><span class="sxs-lookup"><span data-stu-id="66c41-356">The example below defines a conditional compilation symbol `A` and then defines it again.</span></span>
```csharp
#define A
#define A
```

<span data-ttu-id="66c41-357">Un `#undef` può "annullare la definizione" di un simbolo di compilazione condizionale non definito.</span><span class="sxs-lookup"><span data-stu-id="66c41-357">A `#undef` may "undefine" a conditional compilation symbol that is not defined.</span></span> <span data-ttu-id="66c41-358">Nell'esempio seguente viene definito un simbolo `A` di compilazione condizionale che viene quindi dedefinito due volte. Sebbene il secondo `#undef` non abbia alcun effetto, è ancora valido.</span><span class="sxs-lookup"><span data-stu-id="66c41-358">The example below defines a conditional compilation symbol `A` and then undefines it twice; although the second `#undef` has no effect, it is still valid.</span></span>
```csharp
#define A
#undef A
#undef A
```

### <a name="conditional-compilation-directives"></a><span data-ttu-id="66c41-359">Direttive di compilazione condizionale</span><span class="sxs-lookup"><span data-stu-id="66c41-359">Conditional compilation directives</span></span>

<span data-ttu-id="66c41-360">Le direttive di compilazione condizionale vengono utilizzate per includere o escludere in modo condizionale parti di un file di origine.</span><span class="sxs-lookup"><span data-stu-id="66c41-360">The conditional compilation directives are used to conditionally include or exclude portions of a source file.</span></span>

```antlr
pp_conditional
    : pp_if_section pp_elif_section* pp_else_section? pp_endif
    ;

pp_if_section
    : whitespace? '#' whitespace? 'if' whitespace pp_expression pp_new_line conditional_section?
    ;

pp_elif_section
    : whitespace? '#' whitespace? 'elif' whitespace pp_expression pp_new_line conditional_section?
    ;

pp_else_section:
    | whitespace? '#' whitespace? 'else' pp_new_line conditional_section?
    ;

pp_endif
    : whitespace? '#' whitespace? 'endif' pp_new_line
    ;

conditional_section
    : input_section
    | skipped_section
    ;

skipped_section
    : skipped_section_part+
    ;

skipped_section_part
    : skipped_characters? new_line
    | pp_directive
    ;

skipped_characters
    : whitespace? not_number_sign input_character*
    ;

not_number_sign
    : '<Any input_character except #>'
    ;
```

<span data-ttu-id="66c41-361">Come indicato dalla sintassi, le direttive di compilazione condizionale devono essere scritte come set costituiti, in ordine, `#if` da una direttiva, da `#elif` zero o più direttive, `#else` da zero o una `#endif` direttiva e da una direttiva.</span><span class="sxs-lookup"><span data-stu-id="66c41-361">As indicated by the syntax, conditional compilation directives must be written as sets consisting of, in order, an `#if` directive, zero or more `#elif` directives, zero or one `#else` directive, and an `#endif` directive.</span></span> <span data-ttu-id="66c41-362">Tra le direttive sono incluse sezioni condizionali del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="66c41-362">Between the directives are conditional sections of source code.</span></span> <span data-ttu-id="66c41-363">Ogni sezione è controllata dalla direttiva immediatamente precedente.</span><span class="sxs-lookup"><span data-stu-id="66c41-363">Each section is controlled by the immediately preceding directive.</span></span> <span data-ttu-id="66c41-364">Una sezione condizionale può contenere le direttive di compilazione condizionale annidate purché queste direttive formino set completi.</span><span class="sxs-lookup"><span data-stu-id="66c41-364">A conditional section may itself contain nested conditional compilation directives provided these directives form complete sets.</span></span>

<span data-ttu-id="66c41-365">Un *pp_conditional* seleziona al massimo uno dei *conditional_section*contenuti per l'elaborazione lessicale normale:</span><span class="sxs-lookup"><span data-stu-id="66c41-365">A *pp_conditional* selects at most one of the contained *conditional_section*s for normal lexical processing:</span></span>

*  <span data-ttu-id="66c41-366">Gli *pp_expression*delle `#if` direttive e `#elif` vengono `true`valutati in ordine fino a quando non viene restituito un risultato.</span><span class="sxs-lookup"><span data-stu-id="66c41-366">The *pp_expression*s of the `#if` and `#elif` directives are evaluated in order until one yields `true`.</span></span> <span data-ttu-id="66c41-367">Se un'espressione restituisce `true`, viene selezionato il *conditional_section* della direttiva corrispondente.</span><span class="sxs-lookup"><span data-stu-id="66c41-367">If an expression yields `true`, the *conditional_section* of the corresponding directive is selected.</span></span>
*  <span data-ttu-id="66c41-368">Se tutti i *pp_expression*di `false`produzione e se `#else` è `#else` presente una direttiva, viene selezionato il *conditional_section* della direttiva.</span><span class="sxs-lookup"><span data-stu-id="66c41-368">If all *pp_expression*s yield `false`, and if an `#else` directive is present, the *conditional_section* of the `#else` directive is selected.</span></span>
*  <span data-ttu-id="66c41-369">In caso contrario, non viene selezionato alcun *conditional_section* .</span><span class="sxs-lookup"><span data-stu-id="66c41-369">Otherwise, no *conditional_section* is selected.</span></span>

<span data-ttu-id="66c41-370">Il *conditional_section*selezionato, se presente, viene elaborato come un normale *input_section*: il codice sorgente contenuto nella sezione deve rispettare la grammatica lessicale; i token vengono generati dal codice sorgente nella sezione. e le direttive di pre-elaborazione nella sezione presentano gli effetti previsti.</span><span class="sxs-lookup"><span data-stu-id="66c41-370">The selected *conditional_section*, if any, is processed as a normal *input_section*: the source code contained in the section must adhere to the lexical grammar; tokens are generated from the source code in the section; and pre-processing directives in the section have the prescribed effects.</span></span>

<span data-ttu-id="66c41-371">Gli eventuali *conditional_section*rimanenti vengono elaborati come *skipped_section*s: ad eccezione delle direttive di pre-elaborazione, il codice sorgente nella sezione non deve rispettare la grammatica lessicale; non viene generato alcun token dal codice sorgente nella sezione. e le direttive di pre-elaborazione nella sezione devono essere corrette dal punto di vista lessicale ma non vengono elaborate in altro modo.</span><span class="sxs-lookup"><span data-stu-id="66c41-371">The remaining *conditional_section*s, if any, are processed as *skipped_section*s: except for pre-processing directives, the source code in the section need not adhere to the lexical grammar; no tokens are generated from the source code in the section; and pre-processing directives in the section must be lexically correct but are not otherwise processed.</span></span> <span data-ttu-id="66c41-372">All'interno di un *conditional_section* che viene elaborato come *skipped_section*, qualsiasi *conditional_section*annidato, contenuto in un `#if`oggetto annidato... `#endif` e`#region`... i costrutti) vengono elaborati anche come *skipped_section.* `#endregion`</span><span class="sxs-lookup"><span data-stu-id="66c41-372">Within a *conditional_section* that is being processed as a *skipped_section*, any nested *conditional_section*s (contained in nested `#if`...`#endif` and `#region`...`#endregion` constructs) are also processed as *skipped_section*s.</span></span>

<span data-ttu-id="66c41-373">Nell'esempio seguente viene illustrato il modo in cui le direttive di compilazione condizionale possono annidarsi:</span><span class="sxs-lookup"><span data-stu-id="66c41-373">The following example illustrates how conditional compilation directives can nest:</span></span>
```csharp
#define Debug       // Debugging on
#undef Trace        // Tracing off

class PurchaseTransaction
{
    void Commit() {
        #if Debug
            CheckConsistency();
            #if Trace
                WriteToLog(this.ToString());
            #endif
        #endif
        CommitHelper();
    }
}
```

<span data-ttu-id="66c41-374">Ad eccezione delle direttive di pre-elaborazione, il codice sorgente ignorato non è soggetto all'analisi lessicale.</span><span class="sxs-lookup"><span data-stu-id="66c41-374">Except for pre-processing directives, skipped source code is not subject to lexical analysis.</span></span> <span data-ttu-id="66c41-375">Ad esempio, il codice seguente è valido nonostante il commento senza terminazione `#else` nella sezione:</span><span class="sxs-lookup"><span data-stu-id="66c41-375">For example, the following is valid despite the unterminated comment in the `#else` section:</span></span>
```csharp
#define Debug        // Debugging on

class PurchaseTransaction
{
    void Commit() {
        #if Debug
            CheckConsistency();
        #else
            /* Do something else
        #endif
    }
}
```

<span data-ttu-id="66c41-376">Si noti, tuttavia, che è necessario che le direttive di pre-elaborazione siano corrette in modo lessicale anche nelle sezioni ignorate del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="66c41-376">Note, however, that pre-processing directives are required to be lexically correct even in skipped sections of source code.</span></span>

<span data-ttu-id="66c41-377">Le direttive di pre-elaborazione non vengono elaborate quando vengono visualizzate all'interno di elementi di input su più righe.</span><span class="sxs-lookup"><span data-stu-id="66c41-377">Pre-processing directives are not processed when they appear inside multi-line input elements.</span></span> <span data-ttu-id="66c41-378">Ad esempio, il programma:</span><span class="sxs-lookup"><span data-stu-id="66c41-378">For example, the program:</span></span>
```csharp
class Hello
{
    static void Main() {
        System.Console.WriteLine(@"hello, 
#if Debug
        world
#else
        Nebraska
#endif
        ");
    }
}
```
<span data-ttu-id="66c41-379">Restituisce l'output:</span><span class="sxs-lookup"><span data-stu-id="66c41-379">results in the output:</span></span>
```
hello,
#if Debug
        world
#else
        Nebraska
#endif
```

<span data-ttu-id="66c41-380">Nei casi particolari, il set di direttive di pre-elaborazione elaborate potrebbe dipendere dalla valutazione del *pp_expression*.</span><span class="sxs-lookup"><span data-stu-id="66c41-380">In peculiar cases, the set of pre-processing directives that is processed might depend on the evaluation of the *pp_expression*.</span></span> <span data-ttu-id="66c41-381">Esempio:</span><span class="sxs-lookup"><span data-stu-id="66c41-381">The example:</span></span>
```csharp
#if X
    /*
#else
    /* */ class Q { }
#endif
```
<span data-ttu-id="66c41-382">produce sempre lo stesso flusso di token`class` ( `}` `Q` `{` ), indipendentemente dal fatto che `X` sia definito o meno.</span><span class="sxs-lookup"><span data-stu-id="66c41-382">always produces the same token stream (`class` `Q` `{` `}`), regardless of whether or not `X` is defined.</span></span> <span data-ttu-id="66c41-383">Se `X` è definito, le uniche direttive elaborate `#if` sono `#endif`e, a causa del commento su più righe.</span><span class="sxs-lookup"><span data-stu-id="66c41-383">If `X` is defined, the only processed directives are `#if` and `#endif`, due to the multi-line comment.</span></span> <span data-ttu-id="66c41-384">Se `X` non è definito, tre direttive (`#if`, `#else`, `#endif`) fanno parte del set di direttive.</span><span class="sxs-lookup"><span data-stu-id="66c41-384">If `X` is undefined, then three directives (`#if`, `#else`, `#endif`) are part of the directive set.</span></span>

### <a name="diagnostic-directives"></a><span data-ttu-id="66c41-385">Direttive di diagnostica</span><span class="sxs-lookup"><span data-stu-id="66c41-385">Diagnostic directives</span></span>

<span data-ttu-id="66c41-386">Le direttive di diagnostica vengono utilizzate per generare in modo esplicito messaggi di errore e di avviso segnalati nello stesso modo in cui si verificano gli errori e gli avvisi in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-386">The diagnostic directives are used to explicitly generate error and warning messages that are reported in the same way as other compile-time errors and warnings.</span></span>

```antlr
pp_diagnostic
    : whitespace? '#' whitespace? 'error' pp_message
    | whitespace? '#' whitespace? 'warning' pp_message
    ;

pp_message
    : new_line
    | whitespace input_character* new_line
    ;
```

<span data-ttu-id="66c41-387">Esempio:</span><span class="sxs-lookup"><span data-stu-id="66c41-387">The example:</span></span>
```csharp
#warning Code review needed before check-in

#if Debug && Retail
    #error A build can't be both debug and retail
#endif

class Test {...}
```
<span data-ttu-id="66c41-388">genera sempre un avviso ("Revisione del codice necessaria prima dell'archiviazione") e genera un errore in fase di compilazione ("una compilazione non può essere sia debug che retail") se i `Debug` simboli `Retail` condizionali e sono entrambi definiti.</span><span class="sxs-lookup"><span data-stu-id="66c41-388">always produces a warning ("Code review needed before check-in"), and produces a compile-time error ("A build can't be both debug and retail") if the conditional symbols `Debug` and `Retail` are both defined.</span></span> <span data-ttu-id="66c41-389">Si noti che un *pp_message* può contenere testo arbitrario; in particolare, non è necessario che contenga token ben formati, come illustrato dalla virgoletta singola della parola `can't`.</span><span class="sxs-lookup"><span data-stu-id="66c41-389">Note that a *pp_message* can contain arbitrary text; specifically, it need not contain well-formed tokens, as shown by the single quote in the word `can't`.</span></span>

### <a name="region-directives"></a><span data-ttu-id="66c41-390">Direttive Region</span><span class="sxs-lookup"><span data-stu-id="66c41-390">Region directives</span></span>

<span data-ttu-id="66c41-391">Le direttive Region vengono usate per contrassegnare in modo esplicito le aree del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="66c41-391">The region directives are used to explicitly mark regions of source code.</span></span>

```antlr
pp_region
    : pp_start_region conditional_section? pp_end_region
    ;

pp_start_region
    : whitespace? '#' whitespace? 'region' pp_message
    ;

pp_end_region
    : whitespace? '#' whitespace? 'endregion' pp_message
    ;
```

<span data-ttu-id="66c41-392">Nessun significato semantico associato a un'area; le aree sono destinate all'uso da parte del programmatore o da strumenti automatici per contrassegnare una sezione del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="66c41-392">No semantic meaning is attached to a region; regions are intended for use by the programmer or by automated tools to mark a section of source code.</span></span> <span data-ttu-id="66c41-393">Il messaggio specificato in una `#region` direttiva `#endregion` o non ha un significato semantico, ma serve solo per identificare l'area.</span><span class="sxs-lookup"><span data-stu-id="66c41-393">The message specified in a `#region` or `#endregion` directive likewise has no semantic meaning; it merely serves to identify the region.</span></span> <span data-ttu-id="66c41-394">Le `#region` direttive e `#endregion` corrispondenti possono avere *pp_message*diverse.</span><span class="sxs-lookup"><span data-stu-id="66c41-394">Matching `#region` and `#endregion` directives may have different *pp_message*s.</span></span>

<span data-ttu-id="66c41-395">Elaborazione lessicale di un'area:</span><span class="sxs-lookup"><span data-stu-id="66c41-395">The lexical processing of a region:</span></span>
```csharp
#region
...
#endregion
```
<span data-ttu-id="66c41-396">corrisponde esattamente all'elaborazione lessicale di una direttiva di compilazione condizionale nel formato:</span><span class="sxs-lookup"><span data-stu-id="66c41-396">corresponds exactly to the lexical processing of a conditional compilation directive of the form:</span></span>
```csharp
#if true
...
#endif
```

### <a name="line-directives"></a><span data-ttu-id="66c41-397">Direttive line</span><span class="sxs-lookup"><span data-stu-id="66c41-397">Line directives</span></span>

<span data-ttu-id="66c41-398">Le direttive line possono essere usate per modificare i numeri di riga e i nomi dei file di origine segnalati dal compilatore nell'output, ad esempio avvisi ed errori, e usati dagli attributi delle informazioni sul chiamante ([attributi delle informazioni sul chiamante](attributes.md#caller-info-attributes)).</span><span class="sxs-lookup"><span data-stu-id="66c41-398">Line directives may be used to alter the line numbers and source file names that are reported by the compiler in output such as warnings and errors, and that are used by caller info attributes ([Caller info attributes](attributes.md#caller-info-attributes)).</span></span>

<span data-ttu-id="66c41-399">Le direttive line sono comunemente usate negli strumenti di metaprogrammazione che generano C# codice sorgente da un altro input di testo.</span><span class="sxs-lookup"><span data-stu-id="66c41-399">Line directives are most commonly used in meta-programming tools that generate C# source code from some other text input.</span></span>

```antlr
pp_line
    : whitespace? '#' whitespace? 'line' whitespace line_indicator pp_new_line
    ;

line_indicator
    : decimal_digit+ whitespace file_name
    | decimal_digit+
    | 'default'
    | 'hidden'
    ;

file_name
    : '"' file_name_character+ '"'
    ;

file_name_character
    : '<Any input_character except ">'
    ;
```

<span data-ttu-id="66c41-400">Quando non `#line` è presente alcuna direttiva, il compilatore segnala i numeri di riga reali e i nomi dei file di origine nell'output.</span><span class="sxs-lookup"><span data-stu-id="66c41-400">When no `#line` directives are present, the compiler reports true line numbers and source file names in its output.</span></span> <span data-ttu-id="66c41-401">Quando si elabora `#line` una direttiva che include un *line_indicator* che non `default`lo è, il compilatore considera la riga dopo la direttiva come avente il numero di riga specificato (e il nome del file, se specificato).</span><span class="sxs-lookup"><span data-stu-id="66c41-401">When processing a `#line` directive that includes a *line_indicator* that is not `default`, the compiler treats the line after the directive as having the given line number (and file name, if specified).</span></span>

<span data-ttu-id="66c41-402">Una `#line default` direttiva inverte l'effetto di tutte le direttive #line precedenti.</span><span class="sxs-lookup"><span data-stu-id="66c41-402">A `#line default` directive reverses the effect of all preceding #line directives.</span></span> <span data-ttu-id="66c41-403">Il compilatore segnala le informazioni sulla riga reale per le righe successive, esattamente `#line` come se non fosse stata elaborata alcuna direttiva.</span><span class="sxs-lookup"><span data-stu-id="66c41-403">The compiler reports true line information for subsequent lines, precisely as if no `#line` directives had been processed.</span></span>

<span data-ttu-id="66c41-404">Una `#line hidden` direttiva non ha alcun effetto sui numeri di riga e file restituiti nei messaggi di errore, ma influisce sul debug a livello di origine.</span><span class="sxs-lookup"><span data-stu-id="66c41-404">A `#line hidden` directive has no effect on the file and line numbers reported in error messages, but does affect source level debugging.</span></span> <span data-ttu-id="66c41-405">Quando si esegue il debug, tutte `#line hidden` le righe tra una `#line` direttiva e la direttiva successiva `#line hidden`, ovvero not, non dispongono di informazioni sul numero di riga.</span><span class="sxs-lookup"><span data-stu-id="66c41-405">When debugging, all lines between a `#line hidden` directive and the subsequent `#line` directive (that is not `#line hidden`) have no line number information.</span></span> <span data-ttu-id="66c41-406">Quando si esegue il codice istruzione per istruzione nel debugger, queste righe verranno ignorate interamente.</span><span class="sxs-lookup"><span data-stu-id="66c41-406">When stepping through code in the debugger, these lines will be skipped entirely.</span></span>

<span data-ttu-id="66c41-407">Si noti che un *file_name* differisce da un valore letterale stringa normale in quanto i caratteri di escape non vengono elaborati. il carattere`\`"" indica semplicemente un carattere barra rovesciata comune all'interno di un *file_name*.</span><span class="sxs-lookup"><span data-stu-id="66c41-407">Note that a *file_name* differs from a regular string literal in that escape characters are not processed; the "`\`" character simply designates an ordinary backslash character within a *file_name*.</span></span>

### <a name="pragma-directives"></a><span data-ttu-id="66c41-408">Pragma (direttive)</span><span class="sxs-lookup"><span data-stu-id="66c41-408">Pragma directives</span></span>

<span data-ttu-id="66c41-409">La `#pragma` direttiva di pre-elaborazione viene utilizzata per specificare informazioni contestuali facoltative per il compilatore.</span><span class="sxs-lookup"><span data-stu-id="66c41-409">The `#pragma` preprocessing directive is used to specify optional contextual information to the compiler.</span></span> <span data-ttu-id="66c41-410">Le informazioni fornite in una `#pragma` direttiva non modificano mai la semantica del programma.</span><span class="sxs-lookup"><span data-stu-id="66c41-410">The information supplied in a `#pragma` directive will never change program semantics.</span></span>

```antlr
pp_pragma
    : whitespace? '#' whitespace? 'pragma' whitespace pragma_body pp_new_line
    ;

pragma_body
    : pragma_warning_body
    ;
```

<span data-ttu-id="66c41-411">C#fornisce `#pragma` direttive per controllare gli avvisi del compilatore.</span><span class="sxs-lookup"><span data-stu-id="66c41-411">C# provides `#pragma` directives to control compiler warnings.</span></span> <span data-ttu-id="66c41-412">Le versioni future della lingua possono includere direttive aggiuntive `#pragma` .</span><span class="sxs-lookup"><span data-stu-id="66c41-412">Future versions of the language may include additional `#pragma` directives.</span></span> <span data-ttu-id="66c41-413">Per garantire l'interoperabilità C# con altri compilatori C# , il compilatore Microsoft non rilascia errori di `#pragma` compilazione per le direttive sconosciute. tali direttive, tuttavia, generano avvisi.</span><span class="sxs-lookup"><span data-stu-id="66c41-413">To ensure interoperability with other C# compilers, the Microsoft C# compiler does not issue compilation errors for unknown `#pragma` directives; such directives do however generate warnings.</span></span>

#### <a name="pragma-warning"></a><span data-ttu-id="66c41-414">avviso pragma</span><span class="sxs-lookup"><span data-stu-id="66c41-414">Pragma warning</span></span>

<span data-ttu-id="66c41-415">La `#pragma warning` direttiva viene utilizzata per disabilitare o ripristinare tutto o un particolare set di messaggi di avviso durante la compilazione del testo del programma successivo.</span><span class="sxs-lookup"><span data-stu-id="66c41-415">The `#pragma warning` directive is used to disable or restore all or a particular set of warning messages during compilation of the subsequent program text.</span></span>

```antlr
pragma_warning_body
    : 'warning' whitespace warning_action
    | 'warning' whitespace warning_action whitespace warning_list
    ;

warning_action
    : 'disable'
    | 'restore'
    ;

warning_list
    : decimal_digit+ (whitespace? ',' whitespace? decimal_digit+)*
    ;
```

<span data-ttu-id="66c41-416">Una `#pragma warning` direttiva che omette l'elenco di avvisi influiscono su tutti gli avvisi.</span><span class="sxs-lookup"><span data-stu-id="66c41-416">A `#pragma warning` directive that omits the warning list affects all warnings.</span></span> <span data-ttu-id="66c41-417">Una `#pragma warning` direttiva che include un elenco di avvisi influiscono solo sugli avvisi specificati nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="66c41-417">A `#pragma warning` directive that includes a warning list affects only those warnings that are specified in the list.</span></span>

<span data-ttu-id="66c41-418">Una `#pragma warning disable` direttiva Disabilita tutto o il set di avvisi specificato.</span><span class="sxs-lookup"><span data-stu-id="66c41-418">A `#pragma warning disable` directive disables all or the given set of warnings.</span></span>

<span data-ttu-id="66c41-419">Una `#pragma warning restore` direttiva Ripristina tutto o il set di avvisi specificato nello stato attivo all'inizio dell'unità di compilazione.</span><span class="sxs-lookup"><span data-stu-id="66c41-419">A `#pragma warning restore` directive restores all or the given set of warnings to the state that was in effect at the beginning of the compilation unit.</span></span> <span data-ttu-id="66c41-420">Si noti che se un particolare avviso è stato disabilitato esternamente, un `#pragma warning restore` (indipendentemente dal fatto che sia o l'avviso specifico) non riabilita tale avviso.</span><span class="sxs-lookup"><span data-stu-id="66c41-420">Note that if a particular warning was disabled externally, a `#pragma warning restore` (whether for all or the specific warning) will not re-enable that warning.</span></span>

<span data-ttu-id="66c41-421">Nell'esempio seguente viene illustrato l' `#pragma warning` utilizzo di per disabilitare temporaneamente l'avviso segnalato quando viene fatto riferimento a membri obsoleti, utilizzando il numero di C# avviso del compilatore Microsoft.</span><span class="sxs-lookup"><span data-stu-id="66c41-421">The following example shows use of `#pragma warning` to temporarily disable the warning reported when obsoleted members are referenced, using the warning number from the Microsoft C# compiler.</span></span>
```csharp
using System;

class Program
{
    [Obsolete]
    static void Foo() {}

    static void Main() {
#pragma warning disable 612
    Foo();
#pragma warning restore 612
    }
}
```
