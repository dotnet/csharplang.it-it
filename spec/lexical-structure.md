# <a name="lexical-structure"></a><span data-ttu-id="e1345-101">Struttura lessicale</span><span class="sxs-lookup"><span data-stu-id="e1345-101">Lexical structure</span></span>

## <a name="programs"></a><span data-ttu-id="e1345-102">Programs</span><span class="sxs-lookup"><span data-stu-id="e1345-102">Programs</span></span>

<span data-ttu-id="e1345-103">In c# ***program*** costituito da uno o più ***i file di origine***, noto formalmente come ***unità di compilazione*** ([unità di compilazione](namespaces.md#compilation-units)).</span><span class="sxs-lookup"><span data-stu-id="e1345-103">A C# ***program*** consists of one or more ***source files***, known formally as ***compilation units*** ([Compilation units](namespaces.md#compilation-units)).</span></span> <span data-ttu-id="e1345-104">Un file di origine è una sequenza ordinata di caratteri Unicode.</span><span class="sxs-lookup"><span data-stu-id="e1345-104">A source file is an ordered sequence of Unicode characters.</span></span> <span data-ttu-id="e1345-105">File di origine sono in genere una corrispondenza uno a uno con i file in un file system, ma questa corrispondenza non è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="e1345-105">Source files typically have a one-to-one correspondence with files in a file system, but this correspondence is not required.</span></span> <span data-ttu-id="e1345-106">Per assicurare la massima portabilità, è consigliabile che i file in un file system deve essere codificato con UTF-8 di codifica.</span><span class="sxs-lookup"><span data-stu-id="e1345-106">For maximal portability, it is recommended that files in a file system be encoded with the UTF-8 encoding.</span></span>

<span data-ttu-id="e1345-107">Dal punto di vista concettuale, un programma viene compilato utilizzando tre passaggi:</span><span class="sxs-lookup"><span data-stu-id="e1345-107">Conceptually speaking, a program is compiled using three steps:</span></span>

1. <span data-ttu-id="e1345-108">Trasformazione che converte un file da un repertorio di caratteri specifico e lo schema di codifica in una sequenza di caratteri Unicode.</span><span class="sxs-lookup"><span data-stu-id="e1345-108">Transformation, which converts a file from a particular character repertoire and encoding scheme into a sequence of Unicode characters.</span></span>
2. <span data-ttu-id="e1345-109">Analisi lessicale, che converte un flusso di caratteri Unicode di input in un flusso di token.</span><span class="sxs-lookup"><span data-stu-id="e1345-109">Lexical analysis, which translates a stream of Unicode input characters into a stream of tokens.</span></span>
3. <span data-ttu-id="e1345-110">Analisi sintattica che converte il flusso dei token nel codice eseguibile.</span><span class="sxs-lookup"><span data-stu-id="e1345-110">Syntactic analysis, which translates the stream of tokens into executable code.</span></span>

## <a name="grammars"></a><span data-ttu-id="e1345-111">Grammatiche</span><span class="sxs-lookup"><span data-stu-id="e1345-111">Grammars</span></span>

<span data-ttu-id="e1345-112">Questa specifica viene visualizzata la sintassi del linguaggio c# linguaggio di programmazione tramite le grammatiche di due.</span><span class="sxs-lookup"><span data-stu-id="e1345-112">This specification presents the syntax of the C# programming language using two grammars.</span></span> <span data-ttu-id="e1345-113">Il ***grammatica lessicale*** ([grammatica lessicale](lexical-structure.md#lexical-grammar)) che definisce come caratteri Unicode vengono combinati per formare terminatori di riga, gli spazi vuoti, commenti, i token e le direttive di pre-elaborazione.</span><span class="sxs-lookup"><span data-stu-id="e1345-113">The ***lexical grammar*** ([Lexical grammar](lexical-structure.md#lexical-grammar)) defines how Unicode characters are combined to form line terminators, white space, comments, tokens, and pre-processing directives.</span></span> <span data-ttu-id="e1345-114">Il ***grammatica lessicale*** ([grammatica lessicale](lexical-structure.md#syntactic-grammar)) definisce come i token creati tramite la grammatica lessicale vengono combinati per formare programmi c#.</span><span class="sxs-lookup"><span data-stu-id="e1345-114">The ***syntactic grammar*** ([Syntactic grammar](lexical-structure.md#syntactic-grammar)) defines how the tokens resulting from the lexical grammar are combined to form C# programs.</span></span>

### <a name="grammar-notation"></a><span data-ttu-id="e1345-115">Notazione grammaticale</span><span class="sxs-lookup"><span data-stu-id="e1345-115">Grammar notation</span></span>

<span data-ttu-id="e1345-116">Grammatiche lessicali e sintattiche vengono presentate nell'uso della notazione dello strumento ANTLR grammatica Backus-Naur form.</span><span class="sxs-lookup"><span data-stu-id="e1345-116">The lexical and syntactic grammars are presented in Backus-Naur form using the notation of the ANTLR grammar tool.</span></span>

### <a name="lexical-grammar"></a><span data-ttu-id="e1345-117">Grammatica lessicale</span><span class="sxs-lookup"><span data-stu-id="e1345-117">Lexical grammar</span></span>

<span data-ttu-id="e1345-118">Grammatica lessicale del linguaggio c# viene presentata nella [analisi lessicale](lexical-structure.md#lexical-analysis), [token](lexical-structure.md#tokens), e [direttive di pre-elaborazione](lexical-structure.md#pre-processing-directives).</span><span class="sxs-lookup"><span data-stu-id="e1345-118">The lexical grammar of C# is presented in [Lexical analysis](lexical-structure.md#lexical-analysis), [Tokens](lexical-structure.md#tokens), and [Pre-processing directives](lexical-structure.md#pre-processing-directives).</span></span> <span data-ttu-id="e1345-119">I simboli terminali della grammatica lessicale sono i caratteri del set di caratteri Unicode e la grammatica lessicale specifica come vengono combinati caratteri per i token di formato ([token](lexical-structure.md#tokens)), gli spazi vuoti ([gli spazi vuoti](lexical-structure.md#white-space)), i commenti ([commenti](lexical-structure.md#comments)) e le direttive di pre-elaborazione ([direttive di pre-elaborazione](lexical-structure.md#pre-processing-directives)).</span><span class="sxs-lookup"><span data-stu-id="e1345-119">The terminal symbols of the lexical grammar are the characters of the Unicode character set, and the lexical grammar specifies how characters are combined to form tokens ([Tokens](lexical-structure.md#tokens)), white space ([White space](lexical-structure.md#white-space)), comments ([Comments](lexical-structure.md#comments)), and pre-processing directives ([Pre-processing directives](lexical-structure.md#pre-processing-directives)).</span></span>

<span data-ttu-id="e1345-120">Ogni file di origine in un programma c# deve essere conforme per il *input* produzione della grammatica lessicale ([analisi lessicale](lexical-structure.md#lexical-analysis)).</span><span class="sxs-lookup"><span data-stu-id="e1345-120">Every source file in a C# program must conform to the *input* production of the lexical grammar ([Lexical analysis](lexical-structure.md#lexical-analysis)).</span></span>

### <a name="syntactic-grammar"></a><span data-ttu-id="e1345-121">Grammatica lessicale</span><span class="sxs-lookup"><span data-stu-id="e1345-121">Syntactic grammar</span></span>

<span data-ttu-id="e1345-122">La grammatica sintattica del linguaggio c# viene descritta nei capitoli e appendici, come indicato di seguito in questo capitolo.</span><span class="sxs-lookup"><span data-stu-id="e1345-122">The syntactic grammar of C# is presented in the chapters and appendices that follow this chapter.</span></span> <span data-ttu-id="e1345-123">I simboli terminali della grammatica sintattico sono i token definiti dalla grammatica lessicale e sintattica grammatica specifica come i token vengono combinati per formare c# programmi.</span><span class="sxs-lookup"><span data-stu-id="e1345-123">The terminal symbols of the syntactic grammar are the tokens defined by the lexical grammar, and the syntactic grammar specifies how tokens are combined to form C# programs.</span></span>

<span data-ttu-id="e1345-124">Ogni file di origine in un programma c# deve essere conforme per il *compilation_unit* produzione della grammatica sintattica ([unità di compilazione](namespaces.md#compilation-units)).</span><span class="sxs-lookup"><span data-stu-id="e1345-124">Every source file in a C# program must conform to the *compilation_unit* production of the syntactic grammar ([Compilation units](namespaces.md#compilation-units)).</span></span>

## <a name="lexical-analysis"></a><span data-ttu-id="e1345-125">Analisi lessicale</span><span class="sxs-lookup"><span data-stu-id="e1345-125">Lexical analysis</span></span>

<span data-ttu-id="e1345-126">Il *input* produzione definisce la struttura lessicale di un file di origine c#.</span><span class="sxs-lookup"><span data-stu-id="e1345-126">The *input* production defines the lexical structure of a C# source file.</span></span> <span data-ttu-id="e1345-127">Ogni file di origine in un programma c# deve essere conformi a questo ambiente di produzione di grammatica lessicale.</span><span class="sxs-lookup"><span data-stu-id="e1345-127">Each source file in a C# program must conform to this lexical grammar production.</span></span>

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

<span data-ttu-id="e1345-128">Cinque elementi di base costituiscono la struttura lessicale di un file di origine c#: i caratteri di terminazione di riga ([dei caratteri di terminazione di riga](lexical-structure.md#line-terminators)), gli spazi vuoti ([spazi vuoti](lexical-structure.md#white-space)), i commenti ([commenti](lexical-structure.md#comments)), token ([token](lexical-structure.md#tokens)), le direttive di pre-elaborazione e ([direttive di pre-elaborazione](lexical-structure.md#pre-processing-directives)).</span><span class="sxs-lookup"><span data-stu-id="e1345-128">Five basic elements make up the lexical structure of a C# source file: Line terminators ([Line terminators](lexical-structure.md#line-terminators)), white space ([White space](lexical-structure.md#white-space)), comments ([Comments](lexical-structure.md#comments)), tokens ([Tokens](lexical-structure.md#tokens)), and pre-processing directives ([Pre-processing directives](lexical-structure.md#pre-processing-directives)).</span></span> <span data-ttu-id="e1345-129">Tra questi elementi di base, solo i token sono importanti nella grammatica sintattica di un programma c# ([grammatica lessicale](lexical-structure.md#syntactic-grammar)).</span><span class="sxs-lookup"><span data-stu-id="e1345-129">Of these basic elements, only tokens are significant in the syntactic grammar of a C# program ([Syntactic grammar](lexical-structure.md#syntactic-grammar)).</span></span>

<span data-ttu-id="e1345-130">L'elaborazione di un file di origine c# lessicale consiste nella riduzione del file in una sequenza di token che diventa l'input per l'analisi sintattica.</span><span class="sxs-lookup"><span data-stu-id="e1345-130">The lexical processing of a C# source file consists of reducing the file into a sequence of tokens which becomes the input to the syntactic analysis.</span></span> <span data-ttu-id="e1345-131">I caratteri di terminazione di riga, gli spazi vuoti e commenti possono essere usato per separare i token e le direttive di pre-elaborazione possono causare sezioni del file di origine deve essere ignorato, ma in caso contrario, questi elementi lessicali avere alcun impatto sulla struttura sintattica di un programma c#.</span><span class="sxs-lookup"><span data-stu-id="e1345-131">Line terminators, white space, and comments can serve to separate tokens, and pre-processing directives can cause sections of the source file to be skipped, but otherwise these lexical elements have no impact on the syntactic structure of a C# program.</span></span>

<span data-ttu-id="e1345-132">Nel caso di valori letterali di stringa interpolata ([valori letterali stringa interpolata](lexical-structure.md#interpolated-string-literals)) un singolo token inizialmente prodotto dall'analisi lessicale, ma è suddiviso in diversi elementi di input più volte sottoposti ad analisi lessicale fino a quando tutti i valori letterali di stringa interpolata sono stati risolti.</span><span class="sxs-lookup"><span data-stu-id="e1345-132">In the case of interpolated string literals ([Interpolated string literals](lexical-structure.md#interpolated-string-literals)) a single token is initially produced by lexical analysis, but is broken up into several input elements which are repeatedly subjected to lexical analysis until all interpolated string literals have been resolved.</span></span> <span data-ttu-id="e1345-133">I token risultanti quindi servono come input per l'analisi sintattica.</span><span class="sxs-lookup"><span data-stu-id="e1345-133">The resulting tokens then serve as input to the syntactic analysis.</span></span>

<span data-ttu-id="e1345-134">Quando più produzioni grammatica lessicale corrisponde a una sequenza di caratteri in un file di origine, durante l'elaborazione lessicale sempre costituisce l'elemento lessicale possibili più lunga.</span><span class="sxs-lookup"><span data-stu-id="e1345-134">When several lexical grammar productions match a sequence of characters in a source file, the lexical processing always forms the longest possible lexical element.</span></span> <span data-ttu-id="e1345-135">Ad esempio, la sequenza di caratteri `//` viene elaborato come l'inizio di un commento a riga singola in quanto l'elemento lessicale è più lungo di un singolo `/` token.</span><span class="sxs-lookup"><span data-stu-id="e1345-135">For example, the character sequence `//` is processed as the beginning of a single-line comment because that lexical element is longer than a single `/` token.</span></span>

### <a name="line-terminators"></a><span data-ttu-id="e1345-136">Caratteri di terminazione di riga</span><span class="sxs-lookup"><span data-stu-id="e1345-136">Line terminators</span></span>

<span data-ttu-id="e1345-137">I caratteri di terminazione di riga consente di dividere i caratteri di un file di origine c# righe.</span><span class="sxs-lookup"><span data-stu-id="e1345-137">Line terminators divide the characters of a C# source file into lines.</span></span>

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

<span data-ttu-id="e1345-138">Per compatibilità con origine gli strumenti di modifica che aggiungono i marcatori di fine del file di codice e per abilitare un'origine file da visualizzare correttamente come una sequenza di righe terminate, vengono applicate le trasformazioni seguenti in ordine, per ogni file di origine in un programma c#:</span><span class="sxs-lookup"><span data-stu-id="e1345-138">For compatibility with source code editing tools that add end-of-file markers, and to enable a source file to be viewed as a sequence of properly terminated lines, the following transformations are applied, in order, to every source file in a C# program:</span></span>

*  <span data-ttu-id="e1345-139">Se l'ultimo carattere del file di origine è un carattere CTRL + Z (`U+001A`), tale carattere viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="e1345-139">If the last character of the source file is a Control-Z character (`U+001A`), this character is deleted.</span></span>
*  <span data-ttu-id="e1345-140">Un carattere di ritorno a capo (`U+000D`) viene aggiunto alla fine del file di origine se tale file di origine non è vuoto e se l'ultimo carattere del file di origine non è un ritorno a capo (`U+000D`), un avanzamento riga (`U+000A`), un separatore di riga (`U+2028`), o un separatore di paragrafo (`U+2029`).</span><span class="sxs-lookup"><span data-stu-id="e1345-140">A carriage-return character (`U+000D`) is added to the end of the source file if that source file is non-empty and if the last character of the source file is not a carriage return (`U+000D`), a line feed (`U+000A`), a line separator (`U+2028`), or a paragraph separator (`U+2029`).</span></span>

### <a name="comments"></a><span data-ttu-id="e1345-141">Commenti</span><span class="sxs-lookup"><span data-stu-id="e1345-141">Comments</span></span>

<span data-ttu-id="e1345-142">Sono supportati due tipi di commenti: commenti delimitatori e commenti a riga singola.</span><span class="sxs-lookup"><span data-stu-id="e1345-142">Two forms of comments are supported: single-line comments and delimited comments.</span></span> <span data-ttu-id="e1345-143">***Commenti a riga singola*** iniziano con i caratteri `//` ed estendere alla fine della riga di origine.</span><span class="sxs-lookup"><span data-stu-id="e1345-143">***Single-line comments*** start with the characters `//` and extend to the end of the source line.</span></span> <span data-ttu-id="e1345-144">***Commenti delimitati*** iniziano con i caratteri `/*` e di fine con i caratteri `*/`.</span><span class="sxs-lookup"><span data-stu-id="e1345-144">***Delimited comments*** start with the characters `/*` and end with the characters `*/`.</span></span> <span data-ttu-id="e1345-145">Commenti delimitati possono estendersi su più righe.</span><span class="sxs-lookup"><span data-stu-id="e1345-145">Delimited comments may span multiple lines.</span></span>

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
    : '/*' delimited_comment_section* asterisk* '/'
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

<span data-ttu-id="e1345-146">Non annidano commenti.</span><span class="sxs-lookup"><span data-stu-id="e1345-146">Comments do not nest.</span></span> <span data-ttu-id="e1345-147">Le sequenze di caratteri `/*` e `*/` hanno alcun significato speciale all'interno di un `//` commento e le sequenze di caratteri `//` e `/*` hanno alcun significato speciale all'interno di un commento delimitato da virgole.</span><span class="sxs-lookup"><span data-stu-id="e1345-147">The character sequences `/*` and `*/` have no special meaning within a `//` comment, and the character sequences `//` and `/*` have no special meaning within a delimited comment.</span></span>

<span data-ttu-id="e1345-148">I commenti non vengono elaborati all'interno di valori letterali carattere e stringa.</span><span class="sxs-lookup"><span data-stu-id="e1345-148">Comments are not processed within character and string literals.</span></span>

<span data-ttu-id="e1345-149">L'esempio</span><span class="sxs-lookup"><span data-stu-id="e1345-149">The example</span></span>
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
<span data-ttu-id="e1345-150">include un commento delimitato da virgole.</span><span class="sxs-lookup"><span data-stu-id="e1345-150">includes a delimited comment.</span></span>

<span data-ttu-id="e1345-151">L'esempio</span><span class="sxs-lookup"><span data-stu-id="e1345-151">The example</span></span>
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
<span data-ttu-id="e1345-152">Mostra alcuni commenti a riga singola.</span><span class="sxs-lookup"><span data-stu-id="e1345-152">shows several single-line comments.</span></span>

### <a name="white-space"></a><span data-ttu-id="e1345-153">Spazio vuoto</span><span class="sxs-lookup"><span data-stu-id="e1345-153">White space</span></span>

<span data-ttu-id="e1345-154">Lo spazio vuoto viene definito come carattere di avanzamento di qualsiasi carattere Unicode classe Zs (che include il carattere spazio), nonché il carattere di tabulazione orizzontale, il carattere di tabulazione verticale e il form.</span><span class="sxs-lookup"><span data-stu-id="e1345-154">White space is defined as any character with Unicode class Zs (which includes the space character) as well as the horizontal tab character, the vertical tab character, and the form feed character.</span></span>

```antlr
whitespace
    : '<Any character with Unicode class Zs>'
    | '<Horizontal tab character (U+0009)>'
    | '<Vertical tab character (U+000B)>'
    | '<Form feed character (U+000C)>'
    ;
```

## <a name="tokens"></a><span data-ttu-id="e1345-155">token</span><span class="sxs-lookup"><span data-stu-id="e1345-155">Tokens</span></span>

<span data-ttu-id="e1345-156">Sono disponibili diversi tipi di token: identificatori, parole chiave, valori letterali, operatori e segni di punteggiatura.</span><span class="sxs-lookup"><span data-stu-id="e1345-156">There are several kinds of tokens: identifiers, keywords, literals, operators, and punctuators.</span></span> <span data-ttu-id="e1345-157">Gli spazi vuoti e commenti non sono i token, anche se vengono usate come separatori di token.</span><span class="sxs-lookup"><span data-stu-id="e1345-157">White space and comments are not tokens, though they act as separators for tokens.</span></span>

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

### <a name="unicode-character-escape-sequences"></a><span data-ttu-id="e1345-158">Sequenze di escape di caratteri Unicode</span><span class="sxs-lookup"><span data-stu-id="e1345-158">Unicode character escape sequences</span></span>

<span data-ttu-id="e1345-159">Una sequenza di escape di caratteri Unicode rappresenta un carattere Unicode.</span><span class="sxs-lookup"><span data-stu-id="e1345-159">A Unicode character escape sequence represents a Unicode character.</span></span> <span data-ttu-id="e1345-160">Le sequenze di escape di caratteri Unicode vengono elaborate in identificatori ([identificatori](lexical-structure.md#identifiers)), caratteri letterali ([caratteri letterali](lexical-structure.md#character-literals)) e i valori letterali stringa normali ([valoriletteralistringa](lexical-structure.md#string-literals)).</span><span class="sxs-lookup"><span data-stu-id="e1345-160">Unicode character escape sequences are processed in identifiers ([Identifiers](lexical-structure.md#identifiers)), character literals ([Character literals](lexical-structure.md#character-literals)), and regular string literals ([String literals](lexical-structure.md#string-literals)).</span></span> <span data-ttu-id="e1345-161">Un escape di caratteri Unicode non verrà elaborato in qualsiasi altra posizione (ad esempio, in modo da formare un operatore, un segno di punteggiatura o una parola chiave).</span><span class="sxs-lookup"><span data-stu-id="e1345-161">A Unicode character escape is not processed in any other location (for example, to form an operator, punctuator, or keyword).</span></span>

```antlr
unicode_escape_sequence
    : '\\u' hex_digit hex_digit hex_digit hex_digit
    | '\\U' hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit
    ;
```

<span data-ttu-id="e1345-162">Una sequenza di escape Unicode rappresenta il singolo carattere Unicode costituito dalla seguente numero esadecimale di "`\u`"o"`\U`" caratteri.</span><span class="sxs-lookup"><span data-stu-id="e1345-162">A Unicode escape sequence represents the single Unicode character formed by the hexadecimal number following the "`\u`" or "`\U`" characters.</span></span> <span data-ttu-id="e1345-163">Poiché c# usa una codifica a 16 bit di punti di codice Unicode in caratteri e i valori stringa, un carattere Unicode compreso nell'intervallo da u+10000 e U + 10FFFF non è consentito in un valore letterale carattere e viene rappresentato con una coppia di surrogati Unicode in un valore letterale stringa.</span><span class="sxs-lookup"><span data-stu-id="e1345-163">Since C# uses a 16-bit encoding of Unicode code points in characters and string values, a Unicode character in the range U+10000 to U+10FFFF is not permitted in a character literal and is represented using a Unicode surrogate pair in a string literal.</span></span> <span data-ttu-id="e1345-164">Non sono supportati i caratteri Unicode con i punti di codice di sopra di 0x10FFFF.</span><span class="sxs-lookup"><span data-stu-id="e1345-164">Unicode characters with code points above 0x10FFFF are not supported.</span></span>

<span data-ttu-id="e1345-165">Non vengono eseguite più traduzioni.</span><span class="sxs-lookup"><span data-stu-id="e1345-165">Multiple translations are not performed.</span></span> <span data-ttu-id="e1345-166">Ad esempio, la stringa letterale "`\u005Cu005C`"è equivalente a"`\u005C`"anziché"`\`".</span><span class="sxs-lookup"><span data-stu-id="e1345-166">For instance, the string literal "`\u005Cu005C`" is equivalent to "`\u005C`" rather than "`\`".</span></span> <span data-ttu-id="e1345-167">Il valore Unicode `\u005C` è il carattere "`\`".</span><span class="sxs-lookup"><span data-stu-id="e1345-167">The Unicode value `\u005C` is the character "`\`".</span></span>

<span data-ttu-id="e1345-168">L'esempio</span><span class="sxs-lookup"><span data-stu-id="e1345-168">The example</span></span>
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
<span data-ttu-id="e1345-169">vengono illustrati diversi utilizzi della `\u0066`, ovvero la sequenza di escape per la lettera "`f`".</span><span class="sxs-lookup"><span data-stu-id="e1345-169">shows several uses of `\u0066`, which is the escape sequence for the letter "`f`".</span></span> <span data-ttu-id="e1345-170">Il programma è equivalente a</span><span class="sxs-lookup"><span data-stu-id="e1345-170">The program is equivalent to</span></span>
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

### <a name="identifiers"></a><span data-ttu-id="e1345-171">Identificatori</span><span class="sxs-lookup"><span data-stu-id="e1345-171">Identifiers</span></span>

<span data-ttu-id="e1345-172">Le regole relative agli identificatori descritte in questa sezione corrispondono esattamente a quelle indicate da di Unicode Standard Annex 31, ad eccezione del fatto che un carattere di sottolineatura è consentita come un carattere iniziale (come avviene in genere nel linguaggio di programmazione C), sono sequenze di escape di Unicode negli identificatori e il "`@`" carattere è consentito come prefisso per abilitare le parole chiave da usare come identificatori.</span><span class="sxs-lookup"><span data-stu-id="e1345-172">The rules for identifiers given in this section correspond exactly to those recommended by the Unicode Standard Annex 31, except that underscore is allowed as an initial character (as is traditional in the C programming language), Unicode escape sequences are permitted in identifiers, and the "`@`" character is allowed as a prefix to enable keywords to be used as identifiers.</span></span>

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

<span data-ttu-id="e1345-173">Per informazioni sulle classi di caratteri Unicode indicato in precedenza, vedere lo Unicode Standard, versione 3.0, sezione 4.5.</span><span class="sxs-lookup"><span data-stu-id="e1345-173">For information on the Unicode character classes mentioned above, see The Unicode Standard, Version 3.0, section 4.5.</span></span>

<span data-ttu-id="e1345-174">Esempi di identificatori validi includono "`identifier1`","`_identifier2`", e "`@if`".</span><span class="sxs-lookup"><span data-stu-id="e1345-174">Examples of valid identifiers include "`identifier1`", "`_identifier2`", and "`@if`".</span></span>

<span data-ttu-id="e1345-175">Un identificatore in un programma conforme deve essere nel formato canonico definito dal formato di normalizzazione Unicode C, come definito da Unicode Standard Annex 15.</span><span class="sxs-lookup"><span data-stu-id="e1345-175">An identifier in a conforming program must be in the canonical format defined by Unicode Normalization Form C, as defined by Unicode Standard Annex 15.</span></span> <span data-ttu-id="e1345-176">Il comportamento quando si verificano un identificatore non nel formato di normalizzazione C è definito dall'implementazione; Tuttavia, la diagnostica non è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="e1345-176">The behavior when encountering an identifier not in Normalization Form C is implementation-defined; however, a diagnostic is not required.</span></span>

<span data-ttu-id="e1345-177">Il prefisso "`@`" consente l'uso delle parole chiave come identificatori, che risulta utile quando l'interazione con altri linguaggi di programmazione.</span><span class="sxs-lookup"><span data-stu-id="e1345-177">The prefix "`@`" enables the use of keywords as identifiers, which is useful when interfacing with other programming languages.</span></span> <span data-ttu-id="e1345-178">Il carattere `@` non è parte effettiva dell'identificatore, l'identificatore potrebbe essere visualizzato in altri linguaggi come un identificatore normale, senza il prefisso.</span><span class="sxs-lookup"><span data-stu-id="e1345-178">The character `@` is not actually part of the identifier, so the identifier might be seen in other languages as a normal identifier, without the prefix.</span></span> <span data-ttu-id="e1345-179">Un identificatore con un `@` prefisso viene chiamato un ***identificatore verbatim***.</span><span class="sxs-lookup"><span data-stu-id="e1345-179">An identifier with an `@` prefix is called a ***verbatim identifier***.</span></span> <span data-ttu-id="e1345-180">Usare il `@` prefisso per gli identificatori che non sono parole chiave è consentito, ma sconsigliato per una questione di stile.</span><span class="sxs-lookup"><span data-stu-id="e1345-180">Use of the `@` prefix for identifiers that are not keywords is permitted, but strongly discouraged as a matter of style.</span></span>

<span data-ttu-id="e1345-181">Esempio:</span><span class="sxs-lookup"><span data-stu-id="e1345-181">The example:</span></span>
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
<span data-ttu-id="e1345-182">definisce una classe denominata "`class`"con un metodo statico denominato"`static`"che accetta un parametro denominato"`bool`".</span><span class="sxs-lookup"><span data-stu-id="e1345-182">defines a class named "`class`" with a static method named "`static`" that takes a parameter named "`bool`".</span></span> <span data-ttu-id="e1345-183">Si noti che poiché Usa sequenze di escape Unicode non sono consentiti nelle parole chiave, il token "`cl\u0061ss`"è un identificatore ed è lo stesso identificatore"`@class`".</span><span class="sxs-lookup"><span data-stu-id="e1345-183">Note that since Unicode escapes are not permitted in keywords, the token "`cl\u0061ss`" is an identifier, and is the same identifier as "`@class`".</span></span>

<span data-ttu-id="e1345-184">Due identificatori sono considerati uguali se sono identici dopo le trasformazioni seguenti vengono applicate nell'ordine:</span><span class="sxs-lookup"><span data-stu-id="e1345-184">Two identifiers are considered the same if they are identical after the following transformations are applied, in order:</span></span>

*  <span data-ttu-id="e1345-185">Il prefisso "`@`", se usata, è stato rimosso.</span><span class="sxs-lookup"><span data-stu-id="e1345-185">The prefix "`@`", if used, is removed.</span></span>
*  <span data-ttu-id="e1345-186">Ciascuna *unicode_escape_sequence* viene trasformato nel carattere Unicode corrispondente.</span><span class="sxs-lookup"><span data-stu-id="e1345-186">Each *unicode_escape_sequence* is transformed into its corresponding Unicode character.</span></span>
*  <span data-ttu-id="e1345-187">Eventuali *formatting_character*s vengono rimossi.</span><span class="sxs-lookup"><span data-stu-id="e1345-187">Any *formatting_character*s are removed.</span></span>

<span data-ttu-id="e1345-188">I caratteri di sottolineatura di identificatori che contengono due consecutive (`U+005F`) sono riservati per uso dall'implementazione.</span><span class="sxs-lookup"><span data-stu-id="e1345-188">Identifiers containing two consecutive underscore characters (`U+005F`) are reserved for use by the implementation.</span></span> <span data-ttu-id="e1345-189">Ad esempio, un'implementazione potrebbe fornire parole chiave estese che iniziano con due caratteri di sottolineatura.</span><span class="sxs-lookup"><span data-stu-id="e1345-189">For example, an implementation might provide extended keywords that begin with two underscores.</span></span>

### <a name="keywords"></a><span data-ttu-id="e1345-190">Parole chiave</span><span class="sxs-lookup"><span data-stu-id="e1345-190">Keywords</span></span>

<span data-ttu-id="e1345-191">Oggetto ***parola chiave*** è una sequenza simile a identificatore di caratteri che è riservato e non può essere utilizzata come un identificatore eccetto quando preceduto dal `@` carattere.</span><span class="sxs-lookup"><span data-stu-id="e1345-191">A ***keyword*** is an identifier-like sequence of characters that is reserved, and cannot be used as an identifier except when prefaced by the `@` character.</span></span>

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

<span data-ttu-id="e1345-192">In alcune posizioni nella grammatica, specifici identificatori hanno un significato speciale, ma non sono parole chiave.</span><span class="sxs-lookup"><span data-stu-id="e1345-192">In some places in the grammar, specific identifiers have special meaning, but are not keywords.</span></span> <span data-ttu-id="e1345-193">Tali identificatori sono talvolta detti "parole chiave contestuali".</span><span class="sxs-lookup"><span data-stu-id="e1345-193">Such identifiers are sometimes referred to as "contextual keywords".</span></span> <span data-ttu-id="e1345-194">Ad esempio, all'interno di una dichiarazione di proprietà, il "`get`"e"`set`" identificatori hanno un significato speciale ([funzioni di accesso](classes.md#accessors)).</span><span class="sxs-lookup"><span data-stu-id="e1345-194">For example, within a property declaration, the "`get`" and "`set`" identifiers have special meaning ([Accessors](classes.md#accessors)).</span></span> <span data-ttu-id="e1345-195">Diverso da un identificatore `get` o `set` non è mai consentito nelle posizioni seguenti, in modo che questo uso non è in conflitto con un utilizzo di queste parole come identificatori.</span><span class="sxs-lookup"><span data-stu-id="e1345-195">An identifier other than `get` or `set` is never permitted in these locations, so this use does not conflict with a use of these words as identifiers.</span></span> <span data-ttu-id="e1345-196">In altri casi, come con l'identificatore "`var`" nelle dichiarazioni di variabile locale tipizzate in modo implicito ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)), una parola chiave contestuale può determinare conflitti con nomi dichiarati.</span><span class="sxs-lookup"><span data-stu-id="e1345-196">In other cases, such as with the identifier "`var`" in implicitly typed local variable declarations ([Local variable declarations](statements.md#local-variable-declarations)), a contextual keyword can conflict with declared names.</span></span> <span data-ttu-id="e1345-197">In questi casi, il nome dichiarato ha la precedenza rispetto all'utilizzo dell'identificatore come una parola chiave contestuale.</span><span class="sxs-lookup"><span data-stu-id="e1345-197">In such cases, the declared name takes precedence over the use of the identifier as a contextual keyword.</span></span>

### <a name="literals"></a><span data-ttu-id="e1345-198">Valori letterali</span><span class="sxs-lookup"><span data-stu-id="e1345-198">Literals</span></span>

<span data-ttu-id="e1345-199">Oggetto ***letterale*** è una rappresentazione nel codice sorgente di un valore.</span><span class="sxs-lookup"><span data-stu-id="e1345-199">A ***literal*** is a source code representation of a value.</span></span>

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

#### <a name="boolean-literals"></a><span data-ttu-id="e1345-200">Valori letterali booleani</span><span class="sxs-lookup"><span data-stu-id="e1345-200">Boolean literals</span></span>

<span data-ttu-id="e1345-201">Esistono due valori letterali booleani: `true` e `false`.</span><span class="sxs-lookup"><span data-stu-id="e1345-201">There are two boolean literal values: `true` and `false`.</span></span>

```antlr
boolean_literal
    : 'true'
    | 'false'
    ;
```

<span data-ttu-id="e1345-202">Il tipo di un *boolean_literal* è `bool`.</span><span class="sxs-lookup"><span data-stu-id="e1345-202">The type of a *boolean_literal* is `bool`.</span></span>

#### <a name="integer-literals"></a><span data-ttu-id="e1345-203">Valori letterali Integer</span><span class="sxs-lookup"><span data-stu-id="e1345-203">Integer literals</span></span>

<span data-ttu-id="e1345-204">I valori letterali integer vengono usati per scrivere i valori dei tipi `int`, `uint`, `long`, e `ulong`.</span><span class="sxs-lookup"><span data-stu-id="e1345-204">Integer literals are used to write values of types `int`, `uint`, `long`, and `ulong`.</span></span> <span data-ttu-id="e1345-205">I valori letterali integer hanno due forme possibili: decimale ed esadecimale.</span><span class="sxs-lookup"><span data-stu-id="e1345-205">Integer literals have two possible forms: decimal and hexadecimal.</span></span>

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

<span data-ttu-id="e1345-206">Il tipo di valore letterale integer è determinato come segue:</span><span class="sxs-lookup"><span data-stu-id="e1345-206">The type of an integer literal is determined as follows:</span></span>

*  <span data-ttu-id="e1345-207">Se il valore letterale non ha alcun suffisso, ha il primo di questi tipi in cui può essere rappresentato il relativo valore: `int`, `uint`, `long`, `ulong`.</span><span class="sxs-lookup"><span data-stu-id="e1345-207">If the literal has no suffix, it has the first of these types in which its value can be represented: `int`, `uint`, `long`, `ulong`.</span></span>
*  <span data-ttu-id="e1345-208">Se il valore letterale presenta il suffisso `U` oppure `u`, è il primo di questi tipi in cui può essere rappresentato il relativo valore: `uint`, `ulong`.</span><span class="sxs-lookup"><span data-stu-id="e1345-208">If the literal is suffixed by `U` or `u`, it has the first of these types in which its value can be represented: `uint`, `ulong`.</span></span>
*  <span data-ttu-id="e1345-209">Se il valore letterale presenta il suffisso `L` oppure `l`, è il primo di questi tipi in cui può essere rappresentato il relativo valore: `long`, `ulong`.</span><span class="sxs-lookup"><span data-stu-id="e1345-209">If the literal is suffixed by `L` or `l`, it has the first of these types in which its value can be represented: `long`, `ulong`.</span></span>
*  <span data-ttu-id="e1345-210">Se il valore letterale presenta il suffisso `UL`, `Ul`, `uL`, `ul`, `LU`, `Lu`, `lU`, o `lu`, è di tipo `ulong`.</span><span class="sxs-lookup"><span data-stu-id="e1345-210">If the literal is suffixed by `UL`, `Ul`, `uL`, `ul`, `LU`, `Lu`, `lU`, or `lu`, it is of type `ulong`.</span></span>

<span data-ttu-id="e1345-211">Se il valore rappresentato da un valore letterale integer è compreso nell'intervallo di `ulong` digitare, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="e1345-211">If the value represented by an integer literal is outside the range of the `ulong` type, a compile-time error occurs.</span></span>

<span data-ttu-id="e1345-212">Come una questione di stile, è consigliabile che "`L`"è possibile usare al posto di"`l`" durante la scrittura di valori letterali di tipo `long`, in quanto è facile confondere la lettera "`l`"con la cifra"`1`".</span><span class="sxs-lookup"><span data-stu-id="e1345-212">As a matter of style, it is suggested that "`L`" be used instead of "`l`" when writing literals of type `long`, since it is easy to confuse the letter "`l`" with the digit "`1`".</span></span>

<span data-ttu-id="e1345-213">Per consentire il più piccolo possibile `int` e `long` valori vengano scritti come valori letterali integer decimale, esistono due regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="e1345-213">To permit the smallest possible `int` and `long` values to be written as decimal integer literals, the following two rules exist:</span></span>

* <span data-ttu-id="e1345-214">Quando un *decimal_integer_literal* con il valore 2147483648 (2 ^ 31) e nessun *integer_type_suffix* viene visualizzato come token immediatamente successivo al token di operatore meno unario ([con operatore meno unario operatore](expressions.md#unary-minus-operator)), il risultato è una costante di tipo `int` con il valore tra -2147483648 (-2 ^ 31).</span><span class="sxs-lookup"><span data-stu-id="e1345-214">When a *decimal_integer_literal* with the value 2147483648 (2^31) and no *integer_type_suffix* appears as the token immediately following a unary minus operator token ([Unary minus operator](expressions.md#unary-minus-operator)), the result is a constant of type `int` with the value -2147483648 (-2^31).</span></span> <span data-ttu-id="e1345-215">In tutti gli altri casi, questo tipo una *decimal_integer_literal* JE typu `uint`.</span><span class="sxs-lookup"><span data-stu-id="e1345-215">In all other situations, such a *decimal_integer_literal* is of type `uint`.</span></span>
* <span data-ttu-id="e1345-216">Quando un *decimal_integer_literal* con il valore 9223372036854775808 (2 ^ 63) e nessun *integer_type_suffix* o nella *integer_type_suffix* `L` o `l` viene visualizzato come token immediatamente successivo al token di operatore meno unario ([operatore meno unario](expressions.md#unary-minus-operator)), il risultato è una costante di tipo `long` con il valore -9223372036854775808 (-2 ^ 63).</span><span class="sxs-lookup"><span data-stu-id="e1345-216">When a *decimal_integer_literal* with the value 9223372036854775808 (2^63) and no *integer_type_suffix* or the *integer_type_suffix* `L` or `l` appears as the token immediately following a unary minus operator token ([Unary minus operator](expressions.md#unary-minus-operator)), the result is a constant of type `long` with the value -9223372036854775808 (-2^63).</span></span> <span data-ttu-id="e1345-217">In tutti gli altri casi, questo tipo una *decimal_integer_literal* JE typu `ulong`.</span><span class="sxs-lookup"><span data-stu-id="e1345-217">In all other situations, such a *decimal_integer_literal* is of type `ulong`.</span></span>

#### <a name="real-literals"></a><span data-ttu-id="e1345-218">I valori letterali reali</span><span class="sxs-lookup"><span data-stu-id="e1345-218">Real literals</span></span>

<span data-ttu-id="e1345-219">I valori letterali reali vengono usati per scrivere i valori dei tipi `float`, `double`, e `decimal`.</span><span class="sxs-lookup"><span data-stu-id="e1345-219">Real literals are used to write values of types `float`, `double`, and `decimal`.</span></span>

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

<span data-ttu-id="e1345-220">Se nessun *real_type_suffix* è specificato, il tipo di valore letterale reale è `double`.</span><span class="sxs-lookup"><span data-stu-id="e1345-220">If no *real_type_suffix* is specified, the type of the real literal is `double`.</span></span> <span data-ttu-id="e1345-221">In caso contrario, il suffisso del tipo reale determina il tipo di valore letterale reale, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="e1345-221">Otherwise, the real type suffix determines the type of the real literal, as follows:</span></span>

*  <span data-ttu-id="e1345-222">Un valore letterale reale presenta il suffisso `F` oppure `f` JE typu `float`.</span><span class="sxs-lookup"><span data-stu-id="e1345-222">A real literal suffixed by `F` or `f` is of type `float`.</span></span> <span data-ttu-id="e1345-223">Ad esempio, i valori letterali `1f`, `1.5f`, `1e10f`, e `123.456F` sono tutti di tipo `float`.</span><span class="sxs-lookup"><span data-stu-id="e1345-223">For example, the literals `1f`, `1.5f`, `1e10f`, and `123.456F` are all of type `float`.</span></span>
*  <span data-ttu-id="e1345-224">Un valore letterale reale presenta il suffisso `D` oppure `d` JE typu `double`.</span><span class="sxs-lookup"><span data-stu-id="e1345-224">A real literal suffixed by `D` or `d` is of type `double`.</span></span> <span data-ttu-id="e1345-225">Ad esempio, i valori letterali `1d`, `1.5d`, `1e10d`, e `123.456D` sono tutti di tipo `double`.</span><span class="sxs-lookup"><span data-stu-id="e1345-225">For example, the literals `1d`, `1.5d`, `1e10d`, and `123.456D` are all of type `double`.</span></span>
*  <span data-ttu-id="e1345-226">Un valore letterale reale presenta il suffisso `M` oppure `m` JE typu `decimal`.</span><span class="sxs-lookup"><span data-stu-id="e1345-226">A real literal suffixed by `M` or `m` is of type `decimal`.</span></span> <span data-ttu-id="e1345-227">Ad esempio, i valori letterali `1m`, `1.5m`, `1e10m`, e `123.456M` sono tutti di tipo `decimal`.</span><span class="sxs-lookup"><span data-stu-id="e1345-227">For example, the literals `1m`, `1.5m`, `1e10m`, and `123.456M` are all of type `decimal`.</span></span> <span data-ttu-id="e1345-228">Questo valore letterale viene convertito in una `decimal` valore prendendo il valore esatto e, se necessario, arrotondamento al più vicino rappresentabile utilizzando il metodo di arrotondamento ([del tipo decimal](types.md#the-decimal-type)).</span><span class="sxs-lookup"><span data-stu-id="e1345-228">This literal is converted to a `decimal` value by taking the exact value, and, if necessary, rounding to the nearest representable value using banker's rounding ([The decimal type](types.md#the-decimal-type)).</span></span> <span data-ttu-id="e1345-229">Qualsiasi scala evidente nel valore letterale viene mantenuta, a meno che il valore viene arrotondato o il valore è zero (nel qual caso quest'ultimo il segno e la scala è 0).</span><span class="sxs-lookup"><span data-stu-id="e1345-229">Any scale apparent in the literal is preserved unless the value is rounded or the value is zero (in which latter case the sign and scale will be 0).</span></span> <span data-ttu-id="e1345-230">Di conseguenza, il valore letterale `2.900m` verranno analizzati per formare il separatore decimale con segno `0`, coefficiente `2900`e la scala `3`.</span><span class="sxs-lookup"><span data-stu-id="e1345-230">Hence, the literal `2.900m` will be parsed to form the decimal with sign `0`, coefficient `2900`, and scale `3`.</span></span>

<span data-ttu-id="e1345-231">Se il valore letterale specificato non può essere rappresentato nel tipo indicato, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="e1345-231">If the specified literal cannot be represented in the indicated type, a compile-time error occurs.</span></span>

<span data-ttu-id="e1345-232">Il valore di un valore letterale reale typu `float` o `double` è determinato dall'utilizzo IEEE modalità "arrotondamento al valore più vicino".</span><span class="sxs-lookup"><span data-stu-id="e1345-232">The value of a real literal of type `float` or `double` is determined by using the IEEE "round to nearest" mode.</span></span>

<span data-ttu-id="e1345-233">Si noti che in un valore letterale reale, cifre decimali sono sempre obbligatori dopo il separatore decimale.</span><span class="sxs-lookup"><span data-stu-id="e1345-233">Note that in a real literal, decimal digits are always required after the decimal point.</span></span> <span data-ttu-id="e1345-234">Ad esempio, `1.3F` è un valore letterale reale ma `1.F` non.</span><span class="sxs-lookup"><span data-stu-id="e1345-234">For example, `1.3F` is a real literal but `1.F` is not.</span></span>

#### <a name="character-literals"></a><span data-ttu-id="e1345-235">Valori letterali carattere</span><span class="sxs-lookup"><span data-stu-id="e1345-235">Character literals</span></span>

<span data-ttu-id="e1345-236">Un valore letterale carattere rappresenta un singolo carattere e in genere costituito da un carattere racchiuso tra virgolette, come in `'a'`.</span><span class="sxs-lookup"><span data-stu-id="e1345-236">A character literal represents a single character, and usually consists of a character in quotes, as in `'a'`.</span></span>

<span data-ttu-id="e1345-237">Nota: La notazione grammaticale ANTLR effettua quanto segue confusione.</span><span class="sxs-lookup"><span data-stu-id="e1345-237">Note: The ANTLR grammar notation makes the following confusing!</span></span> <span data-ttu-id="e1345-238">In ANTLR, quando si scrivono `\'` è l'acronimo di una virgoletta singola `'`.</span><span class="sxs-lookup"><span data-stu-id="e1345-238">In ANTLR, when you write `\'` it stands for a single quote `'`.</span></span> <span data-ttu-id="e1345-239">E quando si scrivono `\\` è l'acronimo di una singola barra rovesciata `\`.</span><span class="sxs-lookup"><span data-stu-id="e1345-239">And when you write `\\` it stands for a single backslash `\`.</span></span> <span data-ttu-id="e1345-240">Pertanto la prima regola per un valore letterale carattere significa che inizia con una virgoletta singola, seguito da un carattere e quindi una virgoletta singola.</span><span class="sxs-lookup"><span data-stu-id="e1345-240">Therefore the first rule for a character literal means it starts with a single quote, then a character, then a single quote.</span></span> <span data-ttu-id="e1345-241">E sono sequenze di escape semplici possibili undici `\'`, `\"`, `\\`, `\0`, `\a`, `\b`, `\f`, `\n`, `\r`, `\t`, `\v`.</span><span class="sxs-lookup"><span data-stu-id="e1345-241">And the eleven possible simple escape sequences are `\'`, `\"`, `\\`, `\0`, `\a`, `\b`, `\f`, `\n`, `\r`, `\t`, `\v`.</span></span>

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

<span data-ttu-id="e1345-242">Un carattere che segue il carattere barra rovesciata (`\`) in un *carattere* deve essere uno dei seguenti caratteri: `'`, `"`, `\`, `0`, `a`, `b` , `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span><span class="sxs-lookup"><span data-stu-id="e1345-242">A character that follows a backslash character (`\`) in a *character* must be one of the following characters: `'`, `"`, `\`, `0`, `a`, `b`, `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span></span> <span data-ttu-id="e1345-243">In caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="e1345-243">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="e1345-244">Una sequenza di escape esadecimale rappresenta un singolo carattere Unicode, con il valore corretto per il numero esadecimale che segue "`\x`".</span><span class="sxs-lookup"><span data-stu-id="e1345-244">A hexadecimal escape sequence represents a single Unicode character, with the value formed by the hexadecimal number following "`\x`".</span></span>

<span data-ttu-id="e1345-245">Se il valore rappresentato da un valore letterale carattere è maggiore di `U+FFFF`, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="e1345-245">If the value represented by a character literal is greater than `U+FFFF`, a compile-time error occurs.</span></span>

<span data-ttu-id="e1345-246">Una sequenza di escape di caratteri Unicode ([sequenze di escape di caratteri Unicode](lexical-structure.md#unicode-character-escape-sequences)) in un valore letterale carattere deve essere compreso nell'intervallo `U+0000` a `U+FFFF`.</span><span class="sxs-lookup"><span data-stu-id="e1345-246">A Unicode character escape sequence ([Unicode character escape sequences](lexical-structure.md#unicode-character-escape-sequences)) in a character literal must be in the range `U+0000` to `U+FFFF`.</span></span>

<span data-ttu-id="e1345-247">Una sequenza di escape semplici rappresenta una codifica dei caratteri Unicode, come descritto nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="e1345-247">A simple escape sequence represents a Unicode character encoding, as described in the table below.</span></span>


| <span data-ttu-id="e1345-248">__Sequenza di escape__</span><span class="sxs-lookup"><span data-stu-id="e1345-248">__Escape sequence__</span></span> | <span data-ttu-id="e1345-249">__Nome carattere__</span><span class="sxs-lookup"><span data-stu-id="e1345-249">__Character name__</span></span> | <span data-ttu-id="e1345-250">__Codifica Unicode__</span><span class="sxs-lookup"><span data-stu-id="e1345-250">__Unicode encoding__</span></span> |
|---------------------|--------------------|----------------------|
| `\'`                | <span data-ttu-id="e1345-251">Virgoletta singola</span><span class="sxs-lookup"><span data-stu-id="e1345-251">Single quote</span></span>       | `0x0027`             | 
| `\"`                | <span data-ttu-id="e1345-252">Virgoletta doppia</span><span class="sxs-lookup"><span data-stu-id="e1345-252">Double quote</span></span>       | `0x0022`             | 
| `\\`                | <span data-ttu-id="e1345-253">Barra rovesciata</span><span class="sxs-lookup"><span data-stu-id="e1345-253">Backslash</span></span>          | `0x005C`             | 
| `\0`                | <span data-ttu-id="e1345-254">Null</span><span class="sxs-lookup"><span data-stu-id="e1345-254">Null</span></span>               | `0x0000`             | 
| `\a`                | <span data-ttu-id="e1345-255">Avviso</span><span class="sxs-lookup"><span data-stu-id="e1345-255">Alert</span></span>              | `0x0007`             | 
| `\b`                | <span data-ttu-id="e1345-256">Backspace</span><span class="sxs-lookup"><span data-stu-id="e1345-256">Backspace</span></span>          | `0x0008`             | 
| `\f`                | <span data-ttu-id="e1345-257">Avanzamento carta</span><span class="sxs-lookup"><span data-stu-id="e1345-257">Form feed</span></span>          | `0x000C`             | 
| `\n`                | <span data-ttu-id="e1345-258">Nuova riga</span><span class="sxs-lookup"><span data-stu-id="e1345-258">New line</span></span>           | `0x000A`             | 
| `\r`                | <span data-ttu-id="e1345-259">Ritorno a capo</span><span class="sxs-lookup"><span data-stu-id="e1345-259">Carriage return</span></span>    | `0x000D`             | 
| `\t`                | <span data-ttu-id="e1345-260">Tabulazione orizzontale</span><span class="sxs-lookup"><span data-stu-id="e1345-260">Horizontal tab</span></span>     | `0x0009`             | 
| `\v`                | <span data-ttu-id="e1345-261">Tabulazione verticale</span><span class="sxs-lookup"><span data-stu-id="e1345-261">Vertical tab</span></span>       | `0x000B`             | 

<span data-ttu-id="e1345-262">Il tipo di un *character_literal* è `char`.</span><span class="sxs-lookup"><span data-stu-id="e1345-262">The type of a *character_literal* is `char`.</span></span>

#### <a name="string-literals"></a><span data-ttu-id="e1345-263">Valori letterali stringa</span><span class="sxs-lookup"><span data-stu-id="e1345-263">String literals</span></span>

<span data-ttu-id="e1345-264">C# supporta due forme di valori letterali stringa: ***valori letterali stringa normali*** e ***i valori letterali stringa verbatim***.</span><span class="sxs-lookup"><span data-stu-id="e1345-264">C# supports two forms of string literals: ***regular string literals*** and ***verbatim string literals***.</span></span>

<span data-ttu-id="e1345-265">Un valore letterale stringa normale è costituito da zero o più caratteri racchiusi tra virgolette doppie, come in `"hello"`e possono includere entrambe le sequenze di escape semplici (ad esempio `\t` il carattere di tabulazione) ed esadecimale e le sequenze di escape Unicode.</span><span class="sxs-lookup"><span data-stu-id="e1345-265">A regular string literal consists of zero or more characters enclosed in double quotes, as in `"hello"`, and may include both simple escape sequences (such as `\t` for the tab character), and hexadecimal and Unicode escape sequences.</span></span>

<span data-ttu-id="e1345-266">Un valore letterale stringa verbatim è costituito un `@` carattere seguito da un carattere di virgolette doppie, zero o più caratteri e un carattere di virgolette doppie di chiusura.</span><span class="sxs-lookup"><span data-stu-id="e1345-266">A verbatim string literal consists of an `@` character followed by a double-quote character, zero or more characters, and a closing double-quote character.</span></span> <span data-ttu-id="e1345-267">È un semplice esempio `@"hello"`.</span><span class="sxs-lookup"><span data-stu-id="e1345-267">A simple example is `@"hello"`.</span></span> <span data-ttu-id="e1345-268">In una stringa letterale verbatim, i caratteri tra i delimitatori vengono interpretati come verbatim, l'unica eccezione da una *quote_escape_sequence*.</span><span class="sxs-lookup"><span data-stu-id="e1345-268">In a verbatim string literal, the characters between the delimiters are interpreted verbatim, the only exception being a *quote_escape_sequence*.</span></span> <span data-ttu-id="e1345-269">In particolare, le sequenze di escape semplici ed esadecimale e le sequenze di escape Unicode non vengono elaborate nei valori letterali stringa verbatim.</span><span class="sxs-lookup"><span data-stu-id="e1345-269">In particular, simple escape sequences, and hexadecimal and Unicode escape sequences are not processed in verbatim string literals.</span></span> <span data-ttu-id="e1345-270">Un valore letterale stringa verbatim può estendersi su più righe.</span><span class="sxs-lookup"><span data-stu-id="e1345-270">A verbatim string literal may span multiple lines.</span></span>

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

<span data-ttu-id="e1345-271">Un carattere che segue il carattere barra rovesciata (`\`) in un *regular_string_literal_character* deve essere uno dei seguenti caratteri: `'`, `"`, `\`, `0`, `a` , `b`, `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span><span class="sxs-lookup"><span data-stu-id="e1345-271">A character that follows a backslash character (`\`) in a *regular_string_literal_character* must be one of the following characters: `'`, `"`, `\`, `0`, `a`, `b`, `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span></span> <span data-ttu-id="e1345-272">In caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="e1345-272">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="e1345-273">L'esempio</span><span class="sxs-lookup"><span data-stu-id="e1345-273">The example</span></span>
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
<span data-ttu-id="e1345-274">Mostra una serie di valori letterali stringa.</span><span class="sxs-lookup"><span data-stu-id="e1345-274">shows a variety of string literals.</span></span> <span data-ttu-id="e1345-275">L'ultimo valore letterale stringa, `j`, è una stringa letterale verbatim che si estende su più righe.</span><span class="sxs-lookup"><span data-stu-id="e1345-275">The last string literal, `j`, is a verbatim string literal that spans multiple lines.</span></span> <span data-ttu-id="e1345-276">I caratteri tra virgolette, inclusi gli spazi vuoti, ad esempio caratteri di nuova riga, vengono mantenuti verbatim.</span><span class="sxs-lookup"><span data-stu-id="e1345-276">The characters between the quotation marks, including white space such as new line characters, are preserved verbatim.</span></span>

<span data-ttu-id="e1345-277">Poiché una sequenza di escape esadecimale può avere un numero variabile di cifre esadecimali mancano, il valore letterale stringa `"\x123"` contiene un singolo carattere con valore esadecimale 123.</span><span class="sxs-lookup"><span data-stu-id="e1345-277">Since a hexadecimal escape sequence can have a variable number of hex digits, the string literal `"\x123"` contains a single character with hex value 123.</span></span> <span data-ttu-id="e1345-278">Per creare una stringa contenente il carattere con valore esadecimale, seguita dal carattere 3 12, si potrà scrivere `"\x00123"` o `"\x12" + "3"` invece.</span><span class="sxs-lookup"><span data-stu-id="e1345-278">To create a string containing the character with hex value 12 followed by the character 3, one could write `"\x00123"` or `"\x12" + "3"` instead.</span></span>

<span data-ttu-id="e1345-279">Il tipo di un *string_literal* è `string`.</span><span class="sxs-lookup"><span data-stu-id="e1345-279">The type of a *string_literal* is `string`.</span></span>

<span data-ttu-id="e1345-280">Ogni valore letterale stringa non comporta necessariamente in una nuova istanza di stringa.</span><span class="sxs-lookup"><span data-stu-id="e1345-280">Each string literal does not necessarily result in a new string instance.</span></span> <span data-ttu-id="e1345-281">Quando due o più valori letterali stringa che sono equivalenti in base all'operatore di uguaglianza di stringhe ([gli operatori di uguaglianza di stringhe](expressions.md#string-equality-operators)) vengono visualizzati nello stesso programma, questi valori letterali stringa fare riferimento alla stessa istanza di stringa.</span><span class="sxs-lookup"><span data-stu-id="e1345-281">When two or more string literals that are equivalent according to the string equality operator ([String equality operators](expressions.md#string-equality-operators)) appear in the same program, these string literals refer to the same string instance.</span></span> <span data-ttu-id="e1345-282">Ad esempio, l'output prodotto dal</span><span class="sxs-lookup"><span data-stu-id="e1345-282">For instance, the output produced by</span></span>
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
<span data-ttu-id="e1345-283">è `True` perché i due valori letterali fa riferimento alla stessa istanza di stringa.</span><span class="sxs-lookup"><span data-stu-id="e1345-283">is `True` because the two literals refer to the same string instance.</span></span>

#### <a name="interpolated-string-literals"></a><span data-ttu-id="e1345-284">Valori letterali di stringa interpolata</span><span class="sxs-lookup"><span data-stu-id="e1345-284">Interpolated string literals</span></span>

<span data-ttu-id="e1345-285">Valori letterali di stringa interpolata sono simili ai valori letterali stringa, ma contenere aree vuote racchiusi `{` e `}`, in cui possono verificarsi le espressioni.</span><span class="sxs-lookup"><span data-stu-id="e1345-285">Interpolated string literals are similar to string literals, but contain holes delimited by `{` and `}`, wherein expressions can occur.</span></span> <span data-ttu-id="e1345-286">In fase di esecuzione, le espressioni vengono valutate con lo scopo con le forme testuale sostituite nella stringa nella posizione in cui si verifica il problema.</span><span class="sxs-lookup"><span data-stu-id="e1345-286">At runtime, the expressions are evaluated with the purpose of having their textual forms substituted into the string at the place where the hole occurs.</span></span> <span data-ttu-id="e1345-287">La sintassi e semantica dell'interpolazione di stringhe è descritti nella sezione ([stringhe interpolate](expressions.md#interpolated-strings)).</span><span class="sxs-lookup"><span data-stu-id="e1345-287">The syntax and semantics of string interpolation are described in section ([Interpolated strings](expressions.md#interpolated-strings)).</span></span>

<span data-ttu-id="e1345-288">Ad esempio valori letterali stringa, valori letterali di stringa interpolata possono essere regolare o verbatim.</span><span class="sxs-lookup"><span data-stu-id="e1345-288">Like string literals, interpolated string literals can be either regular or verbatim.</span></span> <span data-ttu-id="e1345-289">Valori letterali di stringa interpolata regolari sono delimitati da `$"` e `"`, e sono racchiusi tra valori letterali di stringa interpolata verbatim `$@"` e `"`.</span><span class="sxs-lookup"><span data-stu-id="e1345-289">Interpolated regular string literals are delimited by `$"` and `"`, and interpolated verbatim string literals are delimited by `$@"` and `"`.</span></span>

<span data-ttu-id="e1345-290">Come altri valori letterali, analisi lessicale di un valore letterale di stringa interpolata restituisce inizialmente un singolo token, in base alla grammatica seguente.</span><span class="sxs-lookup"><span data-stu-id="e1345-290">Like other literals, lexical analysis of an interpolated string literal initially results in a single token, as per the grammar below.</span></span> <span data-ttu-id="e1345-291">Tuttavia, prima dell'analisi sintattico, il singolo token con un valore letterale di stringa interpolata viene suddiviso in token diversi per le parti della stringa di aree libere di inclusione e gli elementi di input che si verificano nei fori lessicalmente sono analizzati anche in questo caso.</span><span class="sxs-lookup"><span data-stu-id="e1345-291">However, before syntactic analysis, the single token of an interpolated string literal is broken into several tokens for the parts of the string enclosing the holes, and the input elements occurring in the holes are lexically analysed again.</span></span> <span data-ttu-id="e1345-292">Ciò a sua volta può produrre più valori letterali stringa interpolata per essere elaborati, ma, se lessicalmente corretto, potrebbe portare a una sequenza di token per l'analisi sintattica per l'elaborazione.</span><span class="sxs-lookup"><span data-stu-id="e1345-292">This may in turn produce more interpolated string literals to be processed, but, if lexically correct, will eventually lead to a sequence of tokens for syntactic analysis to process.</span></span>

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

<span data-ttu-id="e1345-293">Un' *interpolated_string_literal* token è reinterpretato come più i token e altri elementi di input come indicato di seguito, in ordine di occorrenza nel *interpolated_string_literal*:</span><span class="sxs-lookup"><span data-stu-id="e1345-293">An *interpolated_string_literal* token is reinterpreted as multiple tokens and other input elements as follows, in order of occurrence in the *interpolated_string_literal*:</span></span>

* <span data-ttu-id="e1345-294">Le occorrenze delle opzioni seguenti vengono interpretati nuovamente come singoli token separati: il leader `$` segno, *interpolated_regular_string_whole*, *interpolated_regular_string_start*, *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_whole*,  *interpolated_verbatim_string_start*, *interpolated_verbatim_string_mid* e *interpolated_verbatim_string_end*.</span><span class="sxs-lookup"><span data-stu-id="e1345-294">Occurrences of the following are reinterpreted as separate individual tokens: the leading `$` sign, *interpolated_regular_string_whole*, *interpolated_regular_string_start*, *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_whole*, *interpolated_verbatim_string_start*, *interpolated_verbatim_string_mid* and *interpolated_verbatim_string_end*.</span></span>
* <span data-ttu-id="e1345-295">Le occorrenze di *regular_balanced_text* e *verbatim_balanced_text* tra queste sono rielaborati come un' *input_section* ([analisi lessicale ](lexical-structure.md#lexical-analysis)) e vengono interpretati nuovamente come la sequenza di elementi di input risultante.</span><span class="sxs-lookup"><span data-stu-id="e1345-295">Occurrences of *regular_balanced_text* and *verbatim_balanced_text* between these are reprocessed as an *input_section* ([Lexical analysis](lexical-structure.md#lexical-analysis)) and are reinterpreted as the resulting sequence of input elements.</span></span> <span data-ttu-id="e1345-296">A sua volta, potrebbero essere presenti token letterali di stringa interpolata reinterpretato.</span><span class="sxs-lookup"><span data-stu-id="e1345-296">These may in turn include interpolated string literal tokens to be reinterpreted.</span></span>

<span data-ttu-id="e1345-297">Analisi sintattica verranno ricombinare i token in un' *interpolated_string_expression* ([stringhe interpolate](expressions.md#interpolated-strings)).</span><span class="sxs-lookup"><span data-stu-id="e1345-297">Syntactic analysis will recombine the tokens into an *interpolated_string_expression* ([Interpolated strings](expressions.md#interpolated-strings)).</span></span>

<span data-ttu-id="e1345-298">Esempi di attività</span><span class="sxs-lookup"><span data-stu-id="e1345-298">Examples TODO</span></span>


#### <a name="the-null-literal"></a><span data-ttu-id="e1345-299">Il valore letterale null</span><span class="sxs-lookup"><span data-stu-id="e1345-299">The null literal</span></span>

```antlr
null_literal
    : 'null'
    ;
```

<span data-ttu-id="e1345-300">Il *null_literal* può essere convertito in modo implicito in un tipo riferimento o nullable.</span><span class="sxs-lookup"><span data-stu-id="e1345-300">The  *null_literal* can be implicitly converted to a reference type or nullable type.</span></span>

### <a name="operators-and-punctuators"></a><span data-ttu-id="e1345-301">Gli operatori e puntatori</span><span class="sxs-lookup"><span data-stu-id="e1345-301">Operators and punctuators</span></span>

<span data-ttu-id="e1345-302">Sono disponibili diversi tipi di operatori e segni di punteggiatura.</span><span class="sxs-lookup"><span data-stu-id="e1345-302">There are several kinds of operators and punctuators.</span></span> <span data-ttu-id="e1345-303">Gli operatori vengono utilizzati nelle espressioni per descrivere le operazioni che interessano una o più operandi.</span><span class="sxs-lookup"><span data-stu-id="e1345-303">Operators are used in expressions to describe operations involving one or more operands.</span></span> <span data-ttu-id="e1345-304">Ad esempio, l'espressione `a + b` utilizza le `+` per aggiungere i due operandi `a` e `b`.</span><span class="sxs-lookup"><span data-stu-id="e1345-304">For example, the expression `a + b` uses the `+` operator to add the two operands `a` and `b`.</span></span> <span data-ttu-id="e1345-305">Segni di punteggiatura sono per il raggruppamento e separazione.</span><span class="sxs-lookup"><span data-stu-id="e1345-305">Punctuators are for grouping and separating.</span></span>

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

<span data-ttu-id="e1345-306">La barra verticale nel *right_shift* e *right_shift_assignment* produzioni vengono utilizzate per indicare che, a differenza di altri produzioni nella grammatica sintattica, senza caratteri di qualsiasi tipo (non ancora lo spazio vuoto) non è consentito tra i token.</span><span class="sxs-lookup"><span data-stu-id="e1345-306">The vertical bar in the *right_shift* and *right_shift_assignment* productions are used to indicate that, unlike other productions in the syntactic grammar, no characters of any kind (not even whitespace) are allowed between the tokens.</span></span> <span data-ttu-id="e1345-307">Queste ultime vengono trattate in modo specifico per consentire la corretta gestione dei *type_parameter_list*s ([parametri di tipo](classes.md#type-parameters)).</span><span class="sxs-lookup"><span data-stu-id="e1345-307">These productions are treated specially in order to enable the correct  handling of *type_parameter_list*s ([Type parameters](classes.md#type-parameters)).</span></span>

## <a name="pre-processing-directives"></a><span data-ttu-id="e1345-308">Direttive di pre-elaborazione</span><span class="sxs-lookup"><span data-stu-id="e1345-308">Pre-processing directives</span></span>

<span data-ttu-id="e1345-309">Le direttive di pre-elaborazione consentono di ignorare in modo condizionale le sezioni dei file di origine, per segnalare l'errore e le condizioni di avviso, nonché di definire le aree distinte del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="e1345-309">The pre-processing directives provide the ability to conditionally skip sections of source files, to report error and warning conditions, and to delineate distinct regions of source code.</span></span> <span data-ttu-id="e1345-310">Il termine "pre-elaborazione direttive" viene usato solo per coerenza con i linguaggi di programmazione C e C++.</span><span class="sxs-lookup"><span data-stu-id="e1345-310">The term "pre-processing directives" is used only for consistency with the C and C++ programming languages.</span></span> <span data-ttu-id="e1345-311">In c#, non vi è alcun passaggio di pre-elaborazione separato; direttive di pre-elaborazione vengono elaborate come parte della fase di analisi lessicale.</span><span class="sxs-lookup"><span data-stu-id="e1345-311">In C#, there is no separate pre-processing step; pre-processing directives are processed as part of the lexical analysis phase.</span></span>

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

<span data-ttu-id="e1345-312">Sono disponibili le seguenti direttive di pre-elaborazione:</span><span class="sxs-lookup"><span data-stu-id="e1345-312">The following pre-processing directives are available:</span></span>

*  <span data-ttu-id="e1345-313">`#define` e `#undef`, che vengono usati per definire e annullare la definizione, rispettivamente, i simboli di compilazione condizionale ([direttive di dichiarazione](lexical-structure.md#declaration-directives)).</span><span class="sxs-lookup"><span data-stu-id="e1345-313">`#define` and `#undef`, which are used to define and undefine, respectively, conditional compilation symbols ([Declaration directives](lexical-structure.md#declaration-directives)).</span></span>
*  <span data-ttu-id="e1345-314">`#if`, `#elif`, `#else`, e `#endif`, che consentono di ignorare in modo condizionale le sezioni del codice sorgente ([direttive di compilazione condizionale](lexical-structure.md#conditional-compilation-directives)).</span><span class="sxs-lookup"><span data-stu-id="e1345-314">`#if`, `#elif`, `#else`, and `#endif`, which are used to conditionally skip sections of source code ([Conditional compilation directives](lexical-structure.md#conditional-compilation-directives)).</span></span>
*  <span data-ttu-id="e1345-315">`#line`, che consente di controllare i numeri di riga generati per gli errori e avvisi ([direttive di riga](lexical-structure.md#line-directives)).</span><span class="sxs-lookup"><span data-stu-id="e1345-315">`#line`, which is used to control line numbers emitted for errors and warnings ([Line directives](lexical-structure.md#line-directives)).</span></span>
*  <span data-ttu-id="e1345-316">`#error` e `#warning`, che sono usati per inviare gli errori e avvisi, rispettivamente ([direttive diagnostica](lexical-structure.md#diagnostic-directives)).</span><span class="sxs-lookup"><span data-stu-id="e1345-316">`#error` and `#warning`, which are used to issue errors and warnings, respectively ([Diagnostic directives](lexical-structure.md#diagnostic-directives)).</span></span>
*  <span data-ttu-id="e1345-317">`#region` e `#endregion`, che sono usati per contrassegnare in modo esplicito le sezioni del codice sorgente ([direttive Region](lexical-structure.md#region-directives)).</span><span class="sxs-lookup"><span data-stu-id="e1345-317">`#region` and `#endregion`, which are used to explicitly mark sections of source code ([Region directives](lexical-structure.md#region-directives)).</span></span>
*  <span data-ttu-id="e1345-318">`#pragma`, che consente di specificare le informazioni contestuali facoltative del compilatore ([direttive Pragma](lexical-structure.md#pragma-directives)).</span><span class="sxs-lookup"><span data-stu-id="e1345-318">`#pragma`, which is used to specify optional contextual information to the compiler ([Pragma directives](lexical-structure.md#pragma-directives)).</span></span>

<span data-ttu-id="e1345-319">Una direttiva di pre-elaborazione sempre occupa una riga separata del codice sorgente e inizia sempre con un `#` carattere e un nome di direttiva pre-elaborazione.</span><span class="sxs-lookup"><span data-stu-id="e1345-319">A pre-processing directive always occupies a separate line of source code and always begins with a `#` character and a pre-processing directive name.</span></span> <span data-ttu-id="e1345-320">Gli spazi vuoti possono verificarsi prima la `#` carattere così come tra la `#` carattere e il nome della direttiva.</span><span class="sxs-lookup"><span data-stu-id="e1345-320">White space may occur before the `#` character and between the `#` character and the directive name.</span></span>

<span data-ttu-id="e1345-321">Una riga di origine contenente un `#define`, `#undef`, `#if`, `#elif`, `#else`, `#endif`, `#line`, o `#endregion` direttiva può terminare con un commento a riga singola.</span><span class="sxs-lookup"><span data-stu-id="e1345-321">A source line containing a `#define`, `#undef`, `#if`, `#elif`, `#else`, `#endif`, `#line`, or `#endregion` directive may end with a single-line comment.</span></span> <span data-ttu-id="e1345-322">Commenti delimitati (il `/* */` stile di visualizzazione dei commenti) non sono consentite righe nel codice sorgente contenente le direttive di pre-elaborazione.</span><span class="sxs-lookup"><span data-stu-id="e1345-322">Delimited comments (the `/* */` style of comments) are not permitted on source lines containing pre-processing directives.</span></span>

<span data-ttu-id="e1345-323">Direttive di pre-elaborazione non sono i token e non fanno parte della grammatica sintattica del linguaggio c#.</span><span class="sxs-lookup"><span data-stu-id="e1345-323">Pre-processing directives are not tokens and are not part of the syntactic grammar of C#.</span></span> <span data-ttu-id="e1345-324">Tuttavia, le direttive di pre-elaborazione possono essere utilizzate per includere o escludere le sequenze di token e possono in questo modo influire sul significato di un programma c#.</span><span class="sxs-lookup"><span data-stu-id="e1345-324">However, pre-processing directives can be used to include or exclude sequences of tokens and can in that way affect the meaning of a C# program.</span></span> <span data-ttu-id="e1345-325">Ad esempio, quando viene compilato, il programma:</span><span class="sxs-lookup"><span data-stu-id="e1345-325">For example, when compiled, the program:</span></span>
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
<span data-ttu-id="e1345-326">risultati nella stessa sequenza esatta dei token del programma:</span><span class="sxs-lookup"><span data-stu-id="e1345-326">results in the exact same sequence of tokens as the program:</span></span>
```csharp
class C
{
    void F() {}
    void I() {}
}
```

<span data-ttu-id="e1345-327">Pertanto, mentre al livello lessicale, i due programmi sono piuttosto diversi, sintatticamente, sono identici.</span><span class="sxs-lookup"><span data-stu-id="e1345-327">Thus, whereas lexically, the two programs are quite different, syntactically, they are identical.</span></span>

### <a name="conditional-compilation-symbols"></a><span data-ttu-id="e1345-328">Simboli di compilazione condizionale</span><span class="sxs-lookup"><span data-stu-id="e1345-328">Conditional compilation symbols</span></span>

<span data-ttu-id="e1345-329">La funzionalità di compilazione condizionale fornita dal `#if`, `#elif`, `#else`, e `#endif` direttive viene controllato tramite espressioni di pre-elaborazione ([pre-elaborazione espressioni](lexical-structure.md#pre-processing-expressions)) e simboli di compilazione condizionale.</span><span class="sxs-lookup"><span data-stu-id="e1345-329">The conditional compilation functionality provided by the `#if`, `#elif`, `#else`, and `#endif` directives is controlled through pre-processing expressions ([Pre-processing expressions](lexical-structure.md#pre-processing-expressions)) and conditional compilation symbols.</span></span>

```antlr
conditional_symbol
    : '<Any identifier_or_keyword except true or false>'
    ;
```

<span data-ttu-id="e1345-330">Un simbolo di compilazione condizionale ha due stati possibili: ***definite*** oppure ***indefinito***.</span><span class="sxs-lookup"><span data-stu-id="e1345-330">A conditional compilation symbol has two possible states: ***defined*** or ***undefined***.</span></span> <span data-ttu-id="e1345-331">All'inizio dell'elaborazione lessicale di un file di origine, un simbolo di compilazione condizionale non è definito, a meno che non è stato definito in modo esplicito tramite un meccanismo esterno (ad esempio, un'opzione del compilatore da riga di comando).</span><span class="sxs-lookup"><span data-stu-id="e1345-331">At the beginning of the lexical processing of a source file, a conditional compilation symbol is undefined unless it has been explicitly defined by an external mechanism (such as a command-line compiler option).</span></span> <span data-ttu-id="e1345-332">Quando un `#define` direttiva viene elaborata, il simbolo di compilazione condizionale indicato nell'istruzione diventa definito nel file di origine.</span><span class="sxs-lookup"><span data-stu-id="e1345-332">When a `#define` directive is processed, the conditional compilation symbol named in that directive becomes defined in that source file.</span></span> <span data-ttu-id="e1345-333">Il simbolo rimarrà definito fino a quando un `#undef` direttiva per lo stesso simbolo viene elaborato o finché non viene raggiunta la fine del file di origine.</span><span class="sxs-lookup"><span data-stu-id="e1345-333">The symbol remains defined until an `#undef` directive for that same symbol is processed, or until the end of the source file is reached.</span></span> <span data-ttu-id="e1345-334">Un'implicazione di ciò è che `#define` e `#undef` direttive in un file di origine non hanno alcun effetto sugli altri file di origine nello stesso programma.</span><span class="sxs-lookup"><span data-stu-id="e1345-334">An implication of this is that `#define` and `#undef` directives in one source file have no effect on other source files in the same program.</span></span>

<span data-ttu-id="e1345-335">Quando viene fatto riferimento in un'espressione di pre-elaborazione, un simbolo di compilazione condizionale definito ha il valore booleano `true`, e un simbolo di compilazione condizionale non definito ha il valore booleano `false`.</span><span class="sxs-lookup"><span data-stu-id="e1345-335">When referenced in a pre-processing expression, a defined conditional compilation symbol has the boolean value `true`, and an undefined conditional compilation symbol has the boolean value `false`.</span></span> <span data-ttu-id="e1345-336">Non è necessario che i simboli di compilazione condizionale in modo esplicito prima dichiararla vi viene fatto riferimento nelle espressioni pre-elaborazione.</span><span class="sxs-lookup"><span data-stu-id="e1345-336">There is no requirement that conditional compilation symbols be explicitly declared before they are referenced in pre-processing expressions.</span></span> <span data-ttu-id="e1345-337">Al contrario, i simboli non dichiarati non vengono semplicemente definiti e pertanto avere il valore `false`.</span><span class="sxs-lookup"><span data-stu-id="e1345-337">Instead, undeclared symbols are simply undefined and thus have the value `false`.</span></span>

<span data-ttu-id="e1345-338">Lo spazio dei nomi per i simboli di compilazione condizionale è distinto e separato da tutte le altre entità denominata in un programma c#.</span><span class="sxs-lookup"><span data-stu-id="e1345-338">The name space for conditional compilation symbols is distinct and separate from all other named entities in a C# program.</span></span> <span data-ttu-id="e1345-339">Simboli di compilazione condizionale è possano fare riferimento solo nelle `#define` e `#undef` direttive e nelle espressioni pre-elaborazione.</span><span class="sxs-lookup"><span data-stu-id="e1345-339">Conditional compilation symbols can only be referenced in `#define` and `#undef` directives and in pre-processing expressions.</span></span>

### <a name="pre-processing-expressions"></a><span data-ttu-id="e1345-340">Le espressioni di pre-elaborazione</span><span class="sxs-lookup"><span data-stu-id="e1345-340">Pre-processing expressions</span></span>

<span data-ttu-id="e1345-341">Le espressioni di pre-elaborazione possono verificarsi nelle `#if` e `#elif` direttive.</span><span class="sxs-lookup"><span data-stu-id="e1345-341">Pre-processing expressions can occur in `#if` and `#elif` directives.</span></span> <span data-ttu-id="e1345-342">Gli operatori `!`, `==`, `!=`, `&&` e `||` sono consentiti nelle espressioni, di pre-elaborazione e le parentesi possono essere usate per il raggruppamento.</span><span class="sxs-lookup"><span data-stu-id="e1345-342">The operators `!`, `==`, `!=`, `&&` and `||` are permitted in pre-processing expressions, and parentheses may be used for grouping.</span></span>

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

<span data-ttu-id="e1345-343">Quando viene fatto riferimento in un'espressione di pre-elaborazione, un simbolo di compilazione condizionale definito ha il valore booleano `true`, e un simbolo di compilazione condizionale non definito ha il valore booleano `false`.</span><span class="sxs-lookup"><span data-stu-id="e1345-343">When referenced in a pre-processing expression, a defined conditional compilation symbol has the boolean value `true`, and an undefined conditional compilation symbol has the boolean value `false`.</span></span>

<span data-ttu-id="e1345-344">Valutazione di un'espressione di pre-elaborazione sempre produce un valore booleano.</span><span class="sxs-lookup"><span data-stu-id="e1345-344">Evaluation of a pre-processing expression always yields a boolean value.</span></span> <span data-ttu-id="e1345-345">Le regole di valutazione di un'espressione di pre-elaborazione sono identici a quelli per un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)), ad eccezione del fatto che l'unica entità definite dall'utente che è possibile fare riferimento siano simboli di compilazione condizionale .</span><span class="sxs-lookup"><span data-stu-id="e1345-345">The rules of evaluation for a pre-processing expression are the same as those for a constant expression ([Constant expressions](expressions.md#constant-expressions)), except that the only user-defined entities that can be referenced are conditional compilation symbols.</span></span>

### <a name="declaration-directives"></a><span data-ttu-id="e1345-346">Direttive di dichiarazione</span><span class="sxs-lookup"><span data-stu-id="e1345-346">Declaration directives</span></span>

<span data-ttu-id="e1345-347">Le direttive di dichiarazione vengono utilizzate per definire o annullare la definizione dei simboli di compilazione condizionale.</span><span class="sxs-lookup"><span data-stu-id="e1345-347">The declaration directives are used to define or undefine conditional compilation symbols.</span></span>

```antlr
pp_declaration
    : whitespace? '#' whitespace? 'define' whitespace conditional_symbol pp_new_line
    | whitespace? '#' whitespace? 'undef' whitespace conditional_symbol pp_new_line
    ;

pp_new_line
    : whitespace? single_line_comment? new_line
    ;
```

<span data-ttu-id="e1345-348">L'elaborazione di un `#define` direttiva fa sì che il simbolo di compilazione condizionale specificato diventare definito, a partire da riga di origine che segue la direttiva.</span><span class="sxs-lookup"><span data-stu-id="e1345-348">The processing of a `#define` directive causes the given conditional compilation symbol to become defined, starting with the source line that follows the directive.</span></span> <span data-ttu-id="e1345-349">Analogamente, l'elaborazione di un `#undef` direttiva fa sì che il simbolo di compilazione condizionale specificato diventi indefinito, a partire da riga di origine che segue la direttiva.</span><span class="sxs-lookup"><span data-stu-id="e1345-349">Likewise, the processing of an `#undef` directive causes the given conditional compilation symbol to become undefined, starting with the source line that follows the directive.</span></span>

<span data-ttu-id="e1345-350">Eventuali `#define` e `#undef` direttive in un file di origine devono verificarsi prima del primo *token* ([token](lexical-structure.md#tokens)) nel file di origine; in caso contrario, in fase di compilazione si verifica un errore.</span><span class="sxs-lookup"><span data-stu-id="e1345-350">Any `#define` and `#undef` directives in a source file must occur before the first *token* ([Tokens](lexical-structure.md#tokens)) in the source file; otherwise a compile-time error occurs.</span></span> <span data-ttu-id="e1345-351">In pratica, `#define` e `#undef` direttive devono precedere qualsiasi codice"reale" nel file di origine.</span><span class="sxs-lookup"><span data-stu-id="e1345-351">In intuitive terms, `#define` and `#undef` directives must precede any "real code" in the source file.</span></span>

<span data-ttu-id="e1345-352">Esempio:</span><span class="sxs-lookup"><span data-stu-id="e1345-352">The example:</span></span>
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
<span data-ttu-id="e1345-353">è valido perché il `#define` direttive precedono il primo token (il `namespace` parola chiave) nel file di origine.</span><span class="sxs-lookup"><span data-stu-id="e1345-353">is valid because the `#define` directives precede the first token (the `namespace` keyword) in the source file.</span></span>

<span data-ttu-id="e1345-354">L'esempio seguente genera un errore in fase di compilazione perché un `#define` segue codice reale:</span><span class="sxs-lookup"><span data-stu-id="e1345-354">The following example results in a compile-time error because a `#define` follows real code:</span></span>
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

<span data-ttu-id="e1345-355">Oggetto `#define` può definire un simbolo di compilazione condizionale già definite, senza che qualsiasi nuova `#undef` per tale simbolo.</span><span class="sxs-lookup"><span data-stu-id="e1345-355">A `#define` may define a conditional compilation symbol that is already defined, without there being any intervening `#undef` for that symbol.</span></span> <span data-ttu-id="e1345-356">L'esempio seguente definisce un simbolo di compilazione condizionale `A` e definisce anche in questo caso.</span><span class="sxs-lookup"><span data-stu-id="e1345-356">The example below defines a conditional compilation symbol `A` and then defines it again.</span></span>
```csharp
#define A
#define A
```

<span data-ttu-id="e1345-357">Oggetto `#undef` potrebbe "Rimuovi" un simbolo di compilazione condizionale non è definito.</span><span class="sxs-lookup"><span data-stu-id="e1345-357">A `#undef` may "undefine" a conditional compilation symbol that is not defined.</span></span> <span data-ttu-id="e1345-358">L'esempio seguente definisce un simbolo di compilazione condizionale `A` e quindi rimuove la definizione due volte; Sebbene il secondo `#undef` non ha alcun effetto, è ancora valido.</span><span class="sxs-lookup"><span data-stu-id="e1345-358">The example below defines a conditional compilation symbol `A` and then undefines it twice; although the second `#undef` has no effect, it is still valid.</span></span>
```csharp
#define A
#undef A
#undef A
```

### <a name="conditional-compilation-directives"></a><span data-ttu-id="e1345-359">Direttive di compilazione condizionale</span><span class="sxs-lookup"><span data-stu-id="e1345-359">Conditional compilation directives</span></span>

<span data-ttu-id="e1345-360">Le direttive di compilazione condizionale consentono di includere o escludere parti di un file di origine in modo condizionale.</span><span class="sxs-lookup"><span data-stu-id="e1345-360">The conditional compilation directives are used to conditionally include or exclude portions of a source file.</span></span>

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

<span data-ttu-id="e1345-361">Come indicato dalla sintassi, le direttive di compilazione condizionale devono essere scritto come set costituiti, in ordine, un `#if` direttiva, zero o più `#elif` direttive, zero o uno `#else` direttiva e un `#endif` direttiva.</span><span class="sxs-lookup"><span data-stu-id="e1345-361">As indicated by the syntax, conditional compilation directives must be written as sets consisting of, in order, an `#if` directive, zero or more `#elif` directives, zero or one `#else` directive, and an `#endif` directive.</span></span> <span data-ttu-id="e1345-362">Tra tali direttive sono sezioni condizionali del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="e1345-362">Between the directives are conditional sections of source code.</span></span> <span data-ttu-id="e1345-363">Ogni sezione è controllata dalla direttiva immediatamente precedente.</span><span class="sxs-lookup"><span data-stu-id="e1345-363">Each section is controlled by the immediately preceding directive.</span></span> <span data-ttu-id="e1345-364">Una sezione condizionale possa contenere direttive di compilazione condizionale annidato purché queste direttive formano set completi.</span><span class="sxs-lookup"><span data-stu-id="e1345-364">A conditional section may itself contain nested conditional compilation directives provided these directives form complete sets.</span></span>

<span data-ttu-id="e1345-365">Oggetto *pp_conditional* consente di selezionare al massimo uno tra i contenuti *conditional_section*s durante la normale elaborazione lessicali:</span><span class="sxs-lookup"><span data-stu-id="e1345-365">A *pp_conditional* selects at most one of the contained *conditional_section*s for normal lexical processing:</span></span>

*  <span data-ttu-id="e1345-366">Il *pp_expression*s delle `#if` e `#elif` direttive vengono valutate nell'ordine fino a quando non uno produce `true`.</span><span class="sxs-lookup"><span data-stu-id="e1345-366">The *pp_expression*s of the `#if` and `#elif` directives are evaluated in order until one yields `true`.</span></span> <span data-ttu-id="e1345-367">Se un'espressione produce `true`, il *conditional_section* della direttiva corrispondente sia selezionata.</span><span class="sxs-lookup"><span data-stu-id="e1345-367">If an expression yields `true`, the *conditional_section* of the corresponding directive is selected.</span></span>
*  <span data-ttu-id="e1345-368">Se tutti gli oggetti *pp_expression*yield s `false`e, se un' `#else` direttiva è presente, il *conditional_section* del `#else` direttiva è selezionata.</span><span class="sxs-lookup"><span data-stu-id="e1345-368">If all *pp_expression*s yield `false`, and if an `#else` directive is present, the *conditional_section* of the `#else` directive is selected.</span></span>
*  <span data-ttu-id="e1345-369">In caso contrario, no *conditional_section* sia selezionata.</span><span class="sxs-lookup"><span data-stu-id="e1345-369">Otherwise, no *conditional_section* is selected.</span></span>

<span data-ttu-id="e1345-370">Oggetto selezionato *conditional_section*, se presente, viene elaborato come un normale *input_section*: codice sorgente contenuto nella sezione deve essere conformi alla grammatica lessicale; i token vengono generati dall'origine codice nella sezione; e le direttive di pre-elaborazione nella sezione hanno effetti prescritti.</span><span class="sxs-lookup"><span data-stu-id="e1345-370">The selected *conditional_section*, if any, is processed as a normal *input_section*: the source code contained in the section must adhere to the lexical grammar; tokens are generated from the source code in the section; and pre-processing directives in the section have the prescribed effects.</span></span>

<span data-ttu-id="e1345-371">Il rimanente *conditional_section*s, se presente, vengono elaborati come *skipped_section*s: ad eccezione di direttive di pre-elaborazione, il codice sorgente nella sezione necessario non rispetta il lessicale grammatica; no i token vengono generati dal codice sorgente nella sezione; e le direttive di pre-elaborazione della sezione devono essere livello lessicale corrette ma non vengono elaborate in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="e1345-371">The remaining *conditional_section*s, if any, are processed as *skipped_section*s: except for pre-processing directives, the source code in the section need not adhere to the lexical grammar; no tokens are generated from the source code in the section; and pre-processing directives in the section must be lexically correct but are not otherwise processed.</span></span> <span data-ttu-id="e1345-372">All'interno di un *conditional_section* che viene elaborata come una *skipped_section*qualsiasi nidificato *conditional_section*s (contenuti in annidate `#if`... `#endif` e `#region`... `#endregion` costruisce) inoltre vengono elaborati come *skipped_section*s.</span><span class="sxs-lookup"><span data-stu-id="e1345-372">Within a *conditional_section* that is being processed as a *skipped_section*, any nested *conditional_section*s (contained in nested `#if`...`#endif` and `#region`...`#endregion` constructs) are also processed as *skipped_section*s.</span></span>

<span data-ttu-id="e1345-373">Nell'esempio seguente viene illustrata la compilazione condizionale possono annidare le direttive:</span><span class="sxs-lookup"><span data-stu-id="e1345-373">The following example illustrates how conditional compilation directives can nest:</span></span>
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

<span data-ttu-id="e1345-374">Ad eccezione di direttive di pre-elaborazione, il codice sorgente ignorato non soggetti a analisi lessicale.</span><span class="sxs-lookup"><span data-stu-id="e1345-374">Except for pre-processing directives, skipped source code is not subject to lexical analysis.</span></span> <span data-ttu-id="e1345-375">Ad esempio, è valida nonostante il commento senza terminazione in quanto segue il `#else` sezione:</span><span class="sxs-lookup"><span data-stu-id="e1345-375">For example, the following is valid despite the unterminated comment in the `#else` section:</span></span>
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

<span data-ttu-id="e1345-376">Si noti tuttavia che le direttive di pre-elaborazione sono necessarie sia lessicalmente corretto anche in ignorati sezioni del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="e1345-376">Note, however, that pre-processing directives are required to be lexically correct even in skipped sections of source code.</span></span>

<span data-ttu-id="e1345-377">Direttive di pre-elaborazione non vengono elaborate quando vengono visualizzate all'interno degli elementi di input a più righe.</span><span class="sxs-lookup"><span data-stu-id="e1345-377">Pre-processing directives are not processed when they appear inside multi-line input elements.</span></span> <span data-ttu-id="e1345-378">Ad esempio, il programma:</span><span class="sxs-lookup"><span data-stu-id="e1345-378">For example, the program:</span></span>
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
<span data-ttu-id="e1345-379">risultati nell'output:</span><span class="sxs-lookup"><span data-stu-id="e1345-379">results in the output:</span></span>
```
hello,
#if Debug
        world
#else
        Nebraska
#endif
```

<span data-ttu-id="e1345-380">In casi particolari, il set di direttive di pre-elaborazione che viene elaborato potrebbe dipendere la valutazione del *pp_expression*.</span><span class="sxs-lookup"><span data-stu-id="e1345-380">In peculiar cases, the set of pre-processing directives that is processed might depend on the evaluation of the *pp_expression*.</span></span> <span data-ttu-id="e1345-381">Esempio:</span><span class="sxs-lookup"><span data-stu-id="e1345-381">The example:</span></span>
```csharp
#if X
    /*
#else
    /* */ class Q { }
#endif
```
<span data-ttu-id="e1345-382">genera sempre lo stesso flusso di token (`class` `Q` `{` `}`), indipendentemente dal o meno `X` è definito.</span><span class="sxs-lookup"><span data-stu-id="e1345-382">always produces the same token stream (`class` `Q` `{` `}`), regardless of whether or not `X` is defined.</span></span> <span data-ttu-id="e1345-383">Se `X` è definito, vengono elaborate solo le direttive `#if` e `#endif`, a causa di commenti su più righe.</span><span class="sxs-lookup"><span data-stu-id="e1345-383">If `X` is defined, the only processed directives are `#if` and `#endif`, due to the multi-line comment.</span></span> <span data-ttu-id="e1345-384">Se `X` è definito, quindi tre direttive (`#if`, `#else`, `#endif`) fanno parte del set di direttiva.</span><span class="sxs-lookup"><span data-stu-id="e1345-384">If `X` is undefined, then three directives (`#if`, `#else`, `#endif`) are part of the directive set.</span></span>

### <a name="diagnostic-directives"></a><span data-ttu-id="e1345-385">Direttive di diagnostica</span><span class="sxs-lookup"><span data-stu-id="e1345-385">Diagnostic directives</span></span>

<span data-ttu-id="e1345-386">Le direttive di diagnostica vengono utilizzate per generare in modo esplicito i messaggi di errore e avviso segnalati nello stesso modo degli altri errori di compilazione e gli avvisi.</span><span class="sxs-lookup"><span data-stu-id="e1345-386">The diagnostic directives are used to explicitly generate error and warning messages that are reported in the same way as other compile-time errors and warnings.</span></span>

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

<span data-ttu-id="e1345-387">Esempio:</span><span class="sxs-lookup"><span data-stu-id="e1345-387">The example:</span></span>
```csharp
#warning Code review needed before check-in

#if Debug && Retail
    #error A build can't be both debug and retail
#endif

class Test {...}
```
<span data-ttu-id="e1345-388">sempre genera un avviso ("revisione del codice necessaria prima dell'archiviazione") e genera un errore in fase di compilazione ("una compilazione non può essere sia di debug delle vendite al dettaglio") se i simboli condizionali `Debug` e `Retail` sono definito.</span><span class="sxs-lookup"><span data-stu-id="e1345-388">always produces a warning ("Code review needed before check-in"), and produces a compile-time error ("A build can't be both debug and retail") if the conditional symbols `Debug` and `Retail` are both defined.</span></span> <span data-ttu-id="e1345-389">Si noti che un *pp_message* può contenere testo arbitrario, in particolare, deve necessariamente contenere ben formato token, come illustrato per la virgoletta singola nella parola `can't`.</span><span class="sxs-lookup"><span data-stu-id="e1345-389">Note that a *pp_message* can contain arbitrary text; specifically, it need not contain well-formed tokens, as shown by the single quote in the word `can't`.</span></span>

### <a name="region-directives"></a><span data-ttu-id="e1345-390">Direttive regione</span><span class="sxs-lookup"><span data-stu-id="e1345-390">Region directives</span></span>

<span data-ttu-id="e1345-391">Le direttive region vengono utilizzate per contrassegnare in modo esplicito le aree del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="e1345-391">The region directives are used to explicitly mark regions of source code.</span></span>

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

<span data-ttu-id="e1345-392">Alcun significato semantico non è collegato a un'area; le aree devono essere utilizzati dal programmatore o mediante strumenti automatizzati per contrassegnare una sezione del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="e1345-392">No semantic meaning is attached to a region; regions are intended for use by the programmer or by automated tools to mark a section of source code.</span></span> <span data-ttu-id="e1345-393">Il messaggio specificato un `#region` o `#endregion` direttiva analogamente non ha alcun significato semantico; serve semplicemente a identificare l'area geografica.</span><span class="sxs-lookup"><span data-stu-id="e1345-393">The message specified in a `#region` or `#endregion` directive likewise has no semantic meaning; it merely serves to identify the region.</span></span> <span data-ttu-id="e1345-394">Corrispondenza `#region` e `#endregion` direttive potrebbero essere diversa *pp_message*s.</span><span class="sxs-lookup"><span data-stu-id="e1345-394">Matching `#region` and `#endregion` directives may have different *pp_message*s.</span></span>

<span data-ttu-id="e1345-395">L'elaborazione lessicale di un'area:</span><span class="sxs-lookup"><span data-stu-id="e1345-395">The lexical processing of a region:</span></span>
```csharp
#region
...
#endregion
```
<span data-ttu-id="e1345-396">corrisponde esattamente all'elaborazione lessicale di una direttiva di compilazione condizionale del form:</span><span class="sxs-lookup"><span data-stu-id="e1345-396">corresponds exactly to the lexical processing of a conditional compilation directive of the form:</span></span>
```csharp
#if true
...
#endif
```

### <a name="line-directives"></a><span data-ttu-id="e1345-397">Direttive di riga</span><span class="sxs-lookup"><span data-stu-id="e1345-397">Line directives</span></span>

<span data-ttu-id="e1345-398">Le direttive di riga possono essere usate per modificare i numeri di riga e nomi di file di origine che vengono segnalati dal compilatore in output, ad esempio avvisi ed errori e usate da attributi informativi sul chiamante ([attributi informativi sul chiamante](attributes.md#caller-info-attributes)).</span><span class="sxs-lookup"><span data-stu-id="e1345-398">Line directives may be used to alter the line numbers and source file names that are reported by the compiler in output such as warnings and errors, and that are used by caller info attributes ([Caller info attributes](attributes.md#caller-info-attributes)).</span></span>

<span data-ttu-id="e1345-399">Direttive di riga vengono utilizzate più comunemente in metaprogrammazione gli strumenti che generano codice sorgente c# da altri input di testo.</span><span class="sxs-lookup"><span data-stu-id="e1345-399">Line directives are most commonly used in meta-programming tools that generate C# source code from some other text input.</span></span>

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

<span data-ttu-id="e1345-400">Se non si specifica `#line` direttive sono presenti, il compilatore segnala i numeri di riga true e nomi di file di origine nel relativo output.</span><span class="sxs-lookup"><span data-stu-id="e1345-400">When no `#line` directives are present, the compiler reports true line numbers and source file names in its output.</span></span> <span data-ttu-id="e1345-401">Durante l'elaborazione di un `#line` direttiva che include un *line_indicator* non `default`, il compilatore considera la riga dopo la direttiva come se avessero il numero di riga (e nome file, se specificato).</span><span class="sxs-lookup"><span data-stu-id="e1345-401">When processing a `#line` directive that includes a *line_indicator* that is not `default`, the compiler treats the line after the directive as having the given line number (and file name, if specified).</span></span>

<span data-ttu-id="e1345-402">Oggetto `#line default` direttiva inverte l'effetto di tutte le direttive #line precedenti.</span><span class="sxs-lookup"><span data-stu-id="e1345-402">A `#line default` directive reverses the effect of all preceding #line directives.</span></span> <span data-ttu-id="e1345-403">Il compilatore segnala le informazioni sulla riga true per le righe successive, esattamente come se no `#line` direttive fosse state elaborate.</span><span class="sxs-lookup"><span data-stu-id="e1345-403">The compiler reports true line information for subsequent lines, precisely as if no `#line` directives had been processed.</span></span>

<span data-ttu-id="e1345-404">Oggetto `#line hidden` direttiva non ha alcun effetto sul file e i numeri di riga segnalati per errore messaggi, ma influisce sul debug a livello di origine.</span><span class="sxs-lookup"><span data-stu-id="e1345-404">A `#line hidden` directive has no effect on the file and line numbers reported in error messages, but does affect source level debugging.</span></span> <span data-ttu-id="e1345-405">Durante il debug, tutte le righe tra un `#line hidden` direttiva e il successivo `#line` direttiva (non `#line hidden`) non dispone di alcuna informazione di numeri di riga.</span><span class="sxs-lookup"><span data-stu-id="e1345-405">When debugging, all lines between a `#line hidden` directive and the subsequent `#line` directive (that is not `#line hidden`) have no line number information.</span></span> <span data-ttu-id="e1345-406">Quando si esegue l'istruzione tramite il codice nel debugger, queste righe verranno completamente ignorate.</span><span class="sxs-lookup"><span data-stu-id="e1345-406">When stepping through code in the debugger, these lines will be skipped entirely.</span></span>

<span data-ttu-id="e1345-407">Si noti che un *nome_file* differisce da un valore letterale stringa normale in caratteri di escape non vengono elaborati; il "`\`" carattere indica semplicemente un carattere di barra rovesciata comune all'interno di un *file_name*.</span><span class="sxs-lookup"><span data-stu-id="e1345-407">Note that a *file_name* differs from a regular string literal in that escape characters are not processed; the "`\`" character simply designates an ordinary backslash character within a *file_name*.</span></span>

### <a name="pragma-directives"></a><span data-ttu-id="e1345-408">Direttive pragma</span><span class="sxs-lookup"><span data-stu-id="e1345-408">Pragma directives</span></span>

<span data-ttu-id="e1345-409">Il `#pragma` direttiva pre-elaborazione viene usata per specificare le informazioni contestuali facoltative al compilatore.</span><span class="sxs-lookup"><span data-stu-id="e1345-409">The `#pragma` preprocessing directive is used to specify optional contextual information to the compiler.</span></span> <span data-ttu-id="e1345-410">Le informazioni specificate un `#pragma` direttiva non verrà mai modificato la semantica di programma.</span><span class="sxs-lookup"><span data-stu-id="e1345-410">The information supplied in a `#pragma` directive will never change program semantics.</span></span>

```antlr
pp_pragma
    : whitespace? '#' whitespace? 'pragma' whitespace pragma_body pp_new_line
    ;

pragma_body
    : pragma_warning_body
    ;
```

<span data-ttu-id="e1345-411">C# fornisce `#pragma` direttive per controllare gli avvisi del compilatore.</span><span class="sxs-lookup"><span data-stu-id="e1345-411">C# provides `#pragma` directives to control compiler warnings.</span></span> <span data-ttu-id="e1345-412">Le versioni future del linguaggio possono includere altri `#pragma` direttive.</span><span class="sxs-lookup"><span data-stu-id="e1345-412">Future versions of the language may include additional `#pragma` directives.</span></span> <span data-ttu-id="e1345-413">Per garantire l'interoperabilità con altri compilatori c#, il compilatore Microsoft c# non generano errori di compilazione non conosciuto `#pragma` direttive; non tali direttive tuttavia generare avvisi.</span><span class="sxs-lookup"><span data-stu-id="e1345-413">To ensure interoperability with other C# compilers, the Microsoft C# compiler does not issue compilation errors for unknown `#pragma` directives; such directives do however generate warnings.</span></span>

#### <a name="pragma-warning"></a><span data-ttu-id="e1345-414">Avviso (pragma)</span><span class="sxs-lookup"><span data-stu-id="e1345-414">Pragma warning</span></span>

<span data-ttu-id="e1345-415">Il `#pragma warning` direttiva viene usata per disabilitare o il ripristino di tutti o un set specifico di avviso messaggi durante la compilazione del testo di programma successive.</span><span class="sxs-lookup"><span data-stu-id="e1345-415">The `#pragma warning` directive is used to disable or restore all or a particular set of warning messages during compilation of the subsequent program text.</span></span>

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

<span data-ttu-id="e1345-416">Oggetto `#pragma warning` direttiva che omette l'elenco degli avvisi di influisce su tutti gli avvisi.</span><span class="sxs-lookup"><span data-stu-id="e1345-416">A `#pragma warning` directive that omits the warning list affects all warnings.</span></span> <span data-ttu-id="e1345-417">Oggetto `#pragma warning` direttiva di include un elenco di avvisi interessa solo gli avvisi che vengono specificati nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="e1345-417">A `#pragma warning` directive the includes a warning list affects only those warnings that are specified in the list.</span></span>

<span data-ttu-id="e1345-418">Oggetto `#pragma warning disable` direttiva Disabilita tutti o il set specificato di avvisi.</span><span class="sxs-lookup"><span data-stu-id="e1345-418">A `#pragma warning disable` directive disables all or the given set of warnings.</span></span>

<span data-ttu-id="e1345-419">Oggetto `#pragma warning restore` direttivo ripristini tutti o il set specificato di avvisi allo stato in cui è stato in vigore all'inizio di un'unità di compilazione.</span><span class="sxs-lookup"><span data-stu-id="e1345-419">A `#pragma warning restore` directive restores all or the given set of warnings to the state that was in effect at the beginning of the compilation unit.</span></span> <span data-ttu-id="e1345-420">Si noti che se un particolare messaggio di avviso è stata disabilitata dall'esterno, un `#pragma warning restore` (se per tutti o l'avviso specifico) non sarà possibile nuovamente abilitare tale avviso.</span><span class="sxs-lookup"><span data-stu-id="e1345-420">Note that if a particular warning was disabled externally, a `#pragma warning restore` (whether for all or the specific warning) will not re-enable that warning.</span></span>

<span data-ttu-id="e1345-421">Nell'esempio seguente viene illustrato l'utilizzo di `#pragma warning` disabilitare temporaneamente il messaggio di avviso segnalato quando obsoleto membri referenziati, utilizzando il numero di avviso del compilatore Microsoft c#.</span><span class="sxs-lookup"><span data-stu-id="e1345-421">The following example shows use of `#pragma warning` to temporarily disable the warning reported when obsoleted members are referenced, using the warning number from the Microsoft C# compiler.</span></span>
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
