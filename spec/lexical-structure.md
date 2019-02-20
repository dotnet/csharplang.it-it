# <a name="lexical-structure"></a>Struttura lessicale

## <a name="programs"></a>Programs

In c# ***program*** costituito da uno o più ***i file di origine***, noto formalmente come ***unità di compilazione*** ([unità di compilazione](namespaces.md#compilation-units)). Un file di origine è una sequenza ordinata di caratteri Unicode. File di origine sono in genere una corrispondenza uno a uno con i file in un file system, ma questa corrispondenza non è obbligatorio. Per assicurare la massima portabilità, è consigliabile che i file in un file system deve essere codificato con UTF-8 di codifica.

Dal punto di vista concettuale, un programma viene compilato utilizzando tre passaggi:

1. Trasformazione che converte un file da un repertorio di caratteri specifico e lo schema di codifica in una sequenza di caratteri Unicode.
2. Analisi lessicale, che converte un flusso di caratteri Unicode di input in un flusso di token.
3. Analisi sintattica che converte il flusso dei token nel codice eseguibile.

## <a name="grammars"></a>Grammatiche

Questa specifica viene visualizzata la sintassi del linguaggio c# linguaggio di programmazione tramite le grammatiche di due. Il ***grammatica lessicale*** ([grammatica lessicale](lexical-structure.md#lexical-grammar)) che definisce come caratteri Unicode vengono combinati per formare terminatori di riga, gli spazi vuoti, commenti, i token e le direttive di pre-elaborazione. Il ***grammatica lessicale*** ([grammatica lessicale](lexical-structure.md#syntactic-grammar)) definisce come i token creati tramite la grammatica lessicale vengono combinati per formare programmi c#.

### <a name="grammar-notation"></a>Notazione grammaticale

Grammatiche lessicali e sintattiche vengono presentate nell'uso della notazione dello strumento ANTLR grammatica Backus-Naur form.

### <a name="lexical-grammar"></a>Grammatica lessicale

Grammatica lessicale del linguaggio c# viene presentata nella [analisi lessicale](lexical-structure.md#lexical-analysis), [token](lexical-structure.md#tokens), e [direttive di pre-elaborazione](lexical-structure.md#pre-processing-directives). I simboli terminali della grammatica lessicale sono i caratteri del set di caratteri Unicode e la grammatica lessicale specifica come vengono combinati caratteri per i token di formato ([token](lexical-structure.md#tokens)), gli spazi vuoti ([gli spazi vuoti](lexical-structure.md#white-space)), i commenti ([commenti](lexical-structure.md#comments)) e le direttive di pre-elaborazione ([direttive di pre-elaborazione](lexical-structure.md#pre-processing-directives)).

Ogni file di origine in un programma c# deve essere conforme per il *input* produzione della grammatica lessicale ([analisi lessicale](lexical-structure.md#lexical-analysis)).

### <a name="syntactic-grammar"></a>Grammatica lessicale

La grammatica sintattica del linguaggio c# viene descritta nei capitoli e appendici, come indicato di seguito in questo capitolo. I simboli terminali della grammatica sintattico sono i token definiti dalla grammatica lessicale e sintattica grammatica specifica come i token vengono combinati per formare c# programmi.

Ogni file di origine in un C# programma deve essere conforme al *compilation_unit* produzione della grammatica sintattica ([unità di compilazione](namespaces.md#compilation-units)).

## <a name="lexical-analysis"></a>Analisi lessicale

Il *input* produzione definisce la struttura lessicale di un file di origine c#. Ogni file di origine in un programma c# deve essere conformi a questo ambiente di produzione di grammatica lessicale.

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

Cinque elementi di base costituiscono la struttura lessicale di un C# file di origine: I caratteri di terminazione di riga ([dei caratteri di terminazione di riga](lexical-structure.md#line-terminators)), gli spazi vuoti ([gli spazi vuoti](lexical-structure.md#white-space)), commenti ([commenti](lexical-structure.md#comments)), i token ([token](lexical-structure.md#tokens)), e direttive di pre-elaborazione ([direttive di pre-elaborazione](lexical-structure.md#pre-processing-directives)). Tra questi elementi di base, solo i token sono importanti nella grammatica sintattica di un programma c# ([grammatica lessicale](lexical-structure.md#syntactic-grammar)).

L'elaborazione di un file di origine c# lessicale consiste nella riduzione del file in una sequenza di token che diventa l'input per l'analisi sintattica. I caratteri di terminazione di riga, gli spazi vuoti e commenti possono essere usato per separare i token e le direttive di pre-elaborazione possono causare sezioni del file di origine deve essere ignorato, ma in caso contrario, questi elementi lessicali avere alcun impatto sulla struttura sintattica di un programma c#.

Nel caso di valori letterali di stringa interpolata ([valori letterali stringa interpolata](lexical-structure.md#interpolated-string-literals)) un singolo token inizialmente prodotto dall'analisi lessicale, ma è suddiviso in diversi elementi di input più volte sottoposti ad analisi lessicale fino a quando tutti i valori letterali di stringa interpolata sono stati risolti. I token risultanti quindi servono come input per l'analisi sintattica.

Quando più produzioni grammatica lessicale corrisponde a una sequenza di caratteri in un file di origine, durante l'elaborazione lessicale sempre costituisce l'elemento lessicale possibili più lunga. Ad esempio, la sequenza di caratteri `//` viene elaborato come l'inizio di un commento a riga singola in quanto l'elemento lessicale è più lungo di un singolo `/` token.

### <a name="line-terminators"></a>Caratteri di terminazione di riga

I caratteri di terminazione di riga consente di dividere i caratteri di un file di origine c# righe.

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

Per compatibilità con origine gli strumenti di modifica che aggiungono i marcatori di fine del file di codice e per abilitare un'origine file da visualizzare correttamente come una sequenza di righe terminate, vengono applicate le trasformazioni seguenti in ordine, per ogni file di origine in un programma c#:

*  Se l'ultimo carattere del file di origine è un carattere CTRL + Z (`U+001A`), tale carattere viene eliminato.
*  Un carattere di ritorno a capo (`U+000D`) viene aggiunto alla fine del file di origine se tale file di origine non è vuoto e se l'ultimo carattere del file di origine non è un ritorno a capo (`U+000D`), un avanzamento riga (`U+000A`), un separatore di riga (`U+2028`), o un separatore di paragrafo (`U+2029`).

### <a name="comments"></a>Commenti

Sono supportati due tipi di commenti: commenti delimitatori e commenti a riga singola. ***Commenti a riga singola*** iniziano con i caratteri `//` ed estendere alla fine della riga di origine. ***Commenti delimitati*** iniziano con i caratteri `/*` e di fine con i caratteri `*/`. Commenti delimitati possono estendersi su più righe.

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

Non annidano commenti. Le sequenze di caratteri `/*` e `*/` hanno alcun significato speciale all'interno di un `//` commento e le sequenze di caratteri `//` e `/*` hanno alcun significato speciale all'interno di un commento delimitato da virgole.

I commenti non vengono elaborati all'interno di valori letterali carattere e stringa.

L'esempio
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
include un commento delimitato da virgole.

L'esempio
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
Mostra alcuni commenti a riga singola.

### <a name="white-space"></a>Spazio vuoto

Lo spazio vuoto viene definito come carattere di avanzamento di qualsiasi carattere Unicode classe Zs (che include il carattere spazio), nonché il carattere di tabulazione orizzontale, il carattere di tabulazione verticale e il form.

```antlr
whitespace
    : '<Any character with Unicode class Zs>'
    | '<Horizontal tab character (U+0009)>'
    | '<Vertical tab character (U+000B)>'
    | '<Form feed character (U+000C)>'
    ;
```

## <a name="tokens"></a>token

Sono disponibili diversi tipi di token: identificatori, parole chiave, valori letterali, operatori e segni di punteggiatura. Gli spazi vuoti e commenti non sono i token, anche se vengono usate come separatori di token.

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

### <a name="unicode-character-escape-sequences"></a>Sequenze di escape di caratteri Unicode

Una sequenza di escape di caratteri Unicode rappresenta un carattere Unicode. Le sequenze di escape di caratteri Unicode vengono elaborate in identificatori ([identificatori](lexical-structure.md#identifiers)), caratteri letterali ([caratteri letterali](lexical-structure.md#character-literals)) e i valori letterali stringa normali ([valoriletteralistringa](lexical-structure.md#string-literals)). Un escape di caratteri Unicode non verrà elaborato in qualsiasi altra posizione (ad esempio, in modo da formare un operatore, un segno di punteggiatura o una parola chiave).

```antlr
unicode_escape_sequence
    : '\\u' hex_digit hex_digit hex_digit hex_digit
    | '\\U' hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit
    ;
```

Una sequenza di escape Unicode rappresenta il singolo carattere Unicode costituito dalla seguente numero esadecimale di "`\u`"o"`\U`" caratteri. Poiché c# usa una codifica a 16 bit di punti di codice Unicode in caratteri e i valori stringa, un carattere Unicode compreso nell'intervallo da u+10000 e U + 10FFFF non è consentito in un valore letterale carattere e viene rappresentato con una coppia di surrogati Unicode in un valore letterale stringa. Non sono supportati i caratteri Unicode con i punti di codice di sopra di 0x10FFFF.

Non vengono eseguite più traduzioni. Ad esempio, la stringa letterale "`\u005Cu005C`"è equivalente a"`\u005C`"anziché"`\`". Il valore Unicode `\u005C` è il carattere "`\`".

L'esempio
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
vengono illustrati diversi utilizzi della `\u0066`, ovvero la sequenza di escape per la lettera "`f`". Il programma è equivalente a
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

### <a name="identifiers"></a>Identificatori

Le regole relative agli identificatori descritte in questa sezione corrispondono esattamente a quelle indicate da di Unicode Standard Annex 31, ad eccezione del fatto che un carattere di sottolineatura è consentita come un carattere iniziale (come avviene in genere nel linguaggio di programmazione C), sono sequenze di escape di Unicode negli identificatori e il "`@`" carattere è consentito come prefisso per abilitare le parole chiave da usare come identificatori.

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

Per informazioni sulle classi di caratteri Unicode indicato in precedenza, vedere lo Unicode Standard, versione 3.0, sezione 4.5.

Esempi di identificatori validi includono "`identifier1`","`_identifier2`", e "`@if`".

Un identificatore in un programma conforme deve essere nel formato canonico definito dal formato di normalizzazione Unicode C, come definito da Unicode Standard Annex 15. Il comportamento quando si verificano un identificatore non nel formato di normalizzazione C è definito dall'implementazione; Tuttavia, la diagnostica non è obbligatoria.

Il prefisso "`@`" consente l'uso delle parole chiave come identificatori, che risulta utile quando l'interazione con altri linguaggi di programmazione. Il carattere `@` non è parte effettiva dell'identificatore, l'identificatore potrebbe essere visualizzato in altri linguaggi come un identificatore normale, senza il prefisso. Un identificatore con un `@` prefisso viene chiamato un ***identificatore verbatim***. Usare il `@` prefisso per gli identificatori che non sono parole chiave è consentito, ma sconsigliato per una questione di stile.

Esempio:
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
definisce una classe denominata "`class`"con un metodo statico denominato"`static`"che accetta un parametro denominato"`bool`". Si noti che poiché Usa sequenze di escape Unicode non sono consentiti nelle parole chiave, il token "`cl\u0061ss`"è un identificatore ed è lo stesso identificatore"`@class`".

Due identificatori sono considerati uguali se sono identici dopo le trasformazioni seguenti vengono applicate nell'ordine:

*  Il prefisso "`@`", se usata, è stato rimosso.
*  Ciascuna *unicode_escape_sequence* viene trasformato nel carattere Unicode corrispondente.
*  Eventuali *formatting_character*s vengono rimossi.

I caratteri di sottolineatura di identificatori che contengono due consecutive (`U+005F`) sono riservati per uso dall'implementazione. Ad esempio, un'implementazione potrebbe fornire parole chiave estese che iniziano con due caratteri di sottolineatura.

### <a name="keywords"></a>Parole chiave

Oggetto ***parola chiave*** è una sequenza simile a identificatore di caratteri che è riservato e non può essere utilizzata come un identificatore eccetto quando preceduto dal `@` carattere.

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

In alcune posizioni nella grammatica, specifici identificatori hanno un significato speciale, ma non sono parole chiave. Tali identificatori sono talvolta detti "parole chiave contestuali". Ad esempio, all'interno di una dichiarazione di proprietà, il "`get`"e"`set`" identificatori hanno un significato speciale ([funzioni di accesso](classes.md#accessors)). Diverso da un identificatore `get` o `set` non è mai consentito nelle posizioni seguenti, in modo che questo uso non è in conflitto con un utilizzo di queste parole come identificatori. In altri casi, come con l'identificatore "`var`" nelle dichiarazioni di variabile locale tipizzate in modo implicito ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)), una parola chiave contestuale può determinare conflitti con nomi dichiarati. In questi casi, il nome dichiarato ha la precedenza rispetto all'utilizzo dell'identificatore come una parola chiave contestuale.

### <a name="literals"></a>Valori letterali

Oggetto ***letterale*** è una rappresentazione nel codice sorgente di un valore.

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

#### <a name="boolean-literals"></a>Valori letterali booleani

Esistono due valori letterali booleani: `true` e `false`.

```antlr
boolean_literal
    : 'true'
    | 'false'
    ;
```

Il tipo di un *boolean_literal* è `bool`.

#### <a name="integer-literals"></a>Valori letterali Integer

I valori letterali integer vengono usati per scrivere i valori dei tipi `int`, `uint`, `long`, e `ulong`. I valori letterali integer hanno due forme possibili: decimale ed esadecimale.

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

Il tipo di valore letterale integer è determinato come segue:

*  Se il valore letterale non ha alcun suffisso, ha il primo di questi tipi in cui può essere rappresentato il relativo valore: `int`, `uint`, `long`, `ulong`.
*  Se il valore letterale presenta il suffisso `U` oppure `u`, è il primo di questi tipi in cui può essere rappresentato il relativo valore: `uint`, `ulong`.
*  Se il valore letterale presenta il suffisso `L` oppure `l`, è il primo di questi tipi in cui può essere rappresentato il relativo valore: `long`, `ulong`.
*  Se il valore letterale presenta il suffisso `UL`, `Ul`, `uL`, `ul`, `LU`, `Lu`, `lU`, o `lu`, è di tipo `ulong`.

Se il valore rappresentato da un valore letterale integer è compreso nell'intervallo di `ulong` digitare, si verifica un errore in fase di compilazione.

Come una questione di stile, è consigliabile che "`L`"è possibile usare al posto di"`l`" durante la scrittura di valori letterali di tipo `long`, in quanto è facile confondere la lettera "`l`"con la cifra"`1`".

Per consentire il più piccolo possibile `int` e `long` valori vengano scritti come valori letterali integer decimale, esistono due regole seguenti:

* Quando un *decimal_integer_literal* con il valore 2147483648 (2 ^ 31) e nessun *integer_type_suffix* viene visualizzato come token immediatamente successivo al token di operatore meno unario ([con operatore meno unario operatore](expressions.md#unary-minus-operator)), il risultato è una costante di tipo `int` con il valore tra -2147483648 (-2 ^ 31). In tutti gli altri casi, questo tipo una *decimal_integer_literal* JE typu `uint`.
* Quando un *decimal_integer_literal* con il valore 9223372036854775808 (2 ^ 63) e nessun *integer_type_suffix* o nella *integer_type_suffix* `L` o `l` viene visualizzato come token immediatamente successivo al token di operatore meno unario ([operatore meno unario](expressions.md#unary-minus-operator)), il risultato è una costante di tipo `long` con il valore -9223372036854775808 (-2 ^ 63). In tutti gli altri casi, questo tipo una *decimal_integer_literal* JE typu `ulong`.

#### <a name="real-literals"></a>I valori letterali reali

I valori letterali reali vengono usati per scrivere i valori dei tipi `float`, `double`, e `decimal`.

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

Se nessun *real_type_suffix* è specificato, il tipo di valore letterale reale è `double`. In caso contrario, il suffisso del tipo reale determina il tipo di valore letterale reale, come indicato di seguito:

*  Un valore letterale reale presenta il suffisso `F` oppure `f` JE typu `float`. Ad esempio, i valori letterali `1f`, `1.5f`, `1e10f`, e `123.456F` sono tutti di tipo `float`.
*  Un valore letterale reale presenta il suffisso `D` oppure `d` JE typu `double`. Ad esempio, i valori letterali `1d`, `1.5d`, `1e10d`, e `123.456D` sono tutti di tipo `double`.
*  Un valore letterale reale presenta il suffisso `M` oppure `m` JE typu `decimal`. Ad esempio, i valori letterali `1m`, `1.5m`, `1e10m`, e `123.456M` sono tutti di tipo `decimal`. Questo valore letterale viene convertito in una `decimal` valore prendendo il valore esatto e, se necessario, arrotondamento al più vicino rappresentabile utilizzando il metodo di arrotondamento ([del tipo decimal](types.md#the-decimal-type)). Qualsiasi scala evidente nel valore letterale viene mantenuta, a meno che il valore viene arrotondato o il valore è zero (nel qual caso quest'ultimo il segno e la scala è 0). Di conseguenza, il valore letterale `2.900m` verranno analizzati per formare il separatore decimale con segno `0`, coefficiente `2900`e la scala `3`.

Se il valore letterale specificato non può essere rappresentato nel tipo indicato, si verifica un errore in fase di compilazione.

Il valore di un valore letterale reale typu `float` o `double` è determinato dall'utilizzo IEEE modalità "arrotondamento al valore più vicino".

Si noti che in un valore letterale reale, cifre decimali sono sempre obbligatori dopo il separatore decimale. Ad esempio, `1.3F` è un valore letterale reale ma `1.F` non.

#### <a name="character-literals"></a>Valori letterali carattere

Un valore letterale carattere rappresenta un singolo carattere e in genere costituito da un carattere racchiuso tra virgolette, come in `'a'`.

Nota: La notazione grammaticale ANTLR rende poco chiaro quanto segue. In ANTLR, quando si scrivono `\'` è l'acronimo di una virgoletta singola `'`. E quando si scrivono `\\` è l'acronimo di una singola barra rovesciata `\`. Pertanto la prima regola per un valore letterale carattere significa che inizia con una virgoletta singola, seguito da un carattere e quindi una virgoletta singola. E sono sequenze di escape semplici possibili undici `\'`, `\"`, `\\`, `\0`, `\a`, `\b`, `\f`, `\n`, `\r`, `\t`, `\v`.

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

Un carattere che segue il carattere barra rovesciata (`\`) in un *carattere* deve essere uno dei seguenti caratteri: `'`, `"`, `\`, `0`, `a`, `b` , `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`. In caso contrario, si verifica un errore in fase di compilazione.

Una sequenza di escape esadecimale rappresenta un singolo carattere Unicode, con il valore corretto per il numero esadecimale che segue "`\x`".

Se il valore rappresentato da un valore letterale carattere è maggiore di `U+FFFF`, si verifica un errore in fase di compilazione.

Una sequenza di escape di caratteri Unicode ([sequenze di escape di caratteri Unicode](lexical-structure.md#unicode-character-escape-sequences)) in un valore letterale carattere deve essere compreso nell'intervallo `U+0000` a `U+FFFF`.

Una sequenza di escape semplici rappresenta una codifica dei caratteri Unicode, come descritto nella tabella seguente.


| __Sequenza di escape__ | __Nome carattere__ | __Codifica Unicode__ |
|---------------------|--------------------|----------------------|
| `\'`                | Virgoletta singola       | `0x0027`             | 
| `\"`                | Virgoletta doppia       | `0x0022`             | 
| `\\`                | Barra rovesciata          | `0x005C`             | 
| `\0`                | Null               | `0x0000`             | 
| `\a`                | Avviso              | `0x0007`             | 
| `\b`                | Backspace          | `0x0008`             | 
| `\f`                | Avanzamento carta          | `0x000C`             | 
| `\n`                | Nuova riga           | `0x000A`             | 
| `\r`                | Ritorno a capo    | `0x000D`             | 
| `\t`                | Tabulazione orizzontale     | `0x0009`             | 
| `\v`                | Tabulazione verticale       | `0x000B`             | 

Il tipo di un *character_literal* è `char`.

#### <a name="string-literals"></a>Valori letterali stringa

C# supporta due forme di valori letterali stringa: ***valori letterali stringa normali*** e ***i valori letterali stringa verbatim***.

Un valore letterale stringa normale è costituito da zero o più caratteri racchiusi tra virgolette doppie, come in `"hello"`e possono includere entrambe le sequenze di escape semplici (ad esempio `\t` il carattere di tabulazione) ed esadecimale e le sequenze di escape Unicode.

Un valore letterale stringa verbatim è costituito un `@` carattere seguito da un carattere di virgolette doppie, zero o più caratteri e un carattere di virgolette doppie di chiusura. È un semplice esempio `@"hello"`. In una stringa letterale verbatim, i caratteri tra i delimitatori vengono interpretati come verbatim, l'unica eccezione da una *quote_escape_sequence*. In particolare, le sequenze di escape semplici ed esadecimale e le sequenze di escape Unicode non vengono elaborate nei valori letterali stringa verbatim. Un valore letterale stringa verbatim può estendersi su più righe.

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

Un carattere che segue il carattere barra rovesciata (`\`) in un *regular_string_literal_character* deve essere uno dei seguenti caratteri: `'`, `"`, `\`, `0`, `a` , `b`, `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`. In caso contrario, si verifica un errore in fase di compilazione.

L'esempio
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
Mostra una serie di valori letterali stringa. L'ultimo valore letterale stringa, `j`, è una stringa letterale verbatim che si estende su più righe. I caratteri tra virgolette, inclusi gli spazi vuoti, ad esempio caratteri di nuova riga, vengono mantenuti verbatim.

Poiché una sequenza di escape esadecimale può avere un numero variabile di cifre esadecimali mancano, il valore letterale stringa `"\x123"` contiene un singolo carattere con valore esadecimale 123. Per creare una stringa contenente il carattere con valore esadecimale, seguita dal carattere 3 12, si potrà scrivere `"\x00123"` o `"\x12" + "3"` invece.

Il tipo di un *string_literal* è `string`.

Ogni valore letterale stringa non comporta necessariamente in una nuova istanza di stringa. Quando due o più valori letterali stringa che sono equivalenti in base all'operatore di uguaglianza di stringhe ([gli operatori di uguaglianza di stringhe](expressions.md#string-equality-operators)) vengono visualizzati nello stesso programma, questi valori letterali stringa fare riferimento alla stessa istanza di stringa. Ad esempio, l'output prodotto dal
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
è `True` perché i due valori letterali fa riferimento alla stessa istanza di stringa.

#### <a name="interpolated-string-literals"></a>Valori letterali di stringa interpolata

Valori letterali di stringa interpolata sono simili ai valori letterali stringa, ma contenere aree vuote racchiusi `{` e `}`, in cui possono verificarsi le espressioni. In fase di esecuzione, le espressioni vengono valutate con lo scopo con le forme testuale sostituite nella stringa nella posizione in cui si verifica il problema. La sintassi e semantica dell'interpolazione di stringhe è descritti nella sezione ([stringhe interpolate](expressions.md#interpolated-strings)).

Ad esempio valori letterali stringa, valori letterali di stringa interpolata possono essere regolare o verbatim. Valori letterali di stringa interpolata regolari sono delimitati da `$"` e `"`, e sono racchiusi tra valori letterali di stringa interpolata verbatim `$@"` e `"`.

Come altri valori letterali, analisi lessicale di un valore letterale di stringa interpolata restituisce inizialmente un singolo token, in base alla grammatica seguente. Tuttavia, prima dell'analisi sintattico, il singolo token con un valore letterale di stringa interpolata viene suddiviso in token diversi per le parti della stringa di aree libere di inclusione e gli elementi di input che si verificano nei fori lessicalmente sono analizzati anche in questo caso. Ciò a sua volta può produrre più valori letterali stringa interpolata per essere elaborati, ma, se lessicalmente corretto, potrebbe portare a una sequenza di token per l'analisi sintattica per l'elaborazione.

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

Un' *interpolated_string_literal* token è reinterpretato come più i token e altri elementi di input come indicato di seguito, in ordine di occorrenza nel *interpolated_string_literal*:

* Le occorrenze delle opzioni seguenti vengono interpretati nuovamente come singoli token separati: il leader `$` segno, *interpolated_regular_string_whole*, *interpolated_regular_string_start*, *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_whole*,  *interpolated_verbatim_string_start*, *interpolated_verbatim_string_mid* e *interpolated_verbatim_string_end*.
* Le occorrenze di *regular_balanced_text* e *verbatim_balanced_text* tra queste sono rielaborati come un' *input_section* ([analisi lessicale ](lexical-structure.md#lexical-analysis)) e vengono interpretati nuovamente come la sequenza di elementi di input risultante. A sua volta, potrebbero essere presenti token letterali di stringa interpolata reinterpretato.

Analisi sintattica verranno ricombinare i token in un' *interpolated_string_expression* ([stringhe interpolate](expressions.md#interpolated-strings)).

Esempi di attività


#### <a name="the-null-literal"></a>Il valore letterale null

```antlr
null_literal
    : 'null'
    ;
```

Il *null_literal* può essere convertito in modo implicito in un tipo riferimento o nullable.

### <a name="operators-and-punctuators"></a>Gli operatori e puntatori

Sono disponibili diversi tipi di operatori e segni di punteggiatura. Gli operatori vengono utilizzati nelle espressioni per descrivere le operazioni che interessano una o più operandi. Ad esempio, l'espressione `a + b` utilizza le `+` per aggiungere i due operandi `a` e `b`. Segni di punteggiatura sono per il raggruppamento e separazione.

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

La barra verticale nel *right_shift* e *right_shift_assignment* produzioni vengono utilizzate per indicare che, a differenza di altri produzioni nella grammatica sintattica, senza caratteri di qualsiasi tipo (non ancora lo spazio vuoto) non è consentito tra i token. Queste ultime vengono trattate in modo specifico per consentire la corretta gestione dei *type_parameter_list*s ([parametri di tipo](classes.md#type-parameters)).

## <a name="pre-processing-directives"></a>Direttive di pre-elaborazione

Le direttive di pre-elaborazione consentono di ignorare in modo condizionale le sezioni dei file di origine, per segnalare l'errore e le condizioni di avviso, nonché di definire le aree distinte del codice sorgente. Il termine "pre-elaborazione direttive" viene usato solo per coerenza con i linguaggi di programmazione C e C++. In c#, non vi è alcun passaggio di pre-elaborazione separato; direttive di pre-elaborazione vengono elaborate come parte della fase di analisi lessicale.

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

Sono disponibili le seguenti direttive di pre-elaborazione:

*  `#define` e `#undef`, che vengono usati per definire e annullare la definizione, rispettivamente, i simboli di compilazione condizionale ([direttive di dichiarazione](lexical-structure.md#declaration-directives)).
*  `#if`, `#elif`, `#else`, e `#endif`, che consentono di ignorare in modo condizionale le sezioni del codice sorgente ([direttive di compilazione condizionale](lexical-structure.md#conditional-compilation-directives)).
*  `#line`, che consente di controllare i numeri di riga generati per gli errori e avvisi ([direttive di riga](lexical-structure.md#line-directives)).
*  `#error` e `#warning`, che sono usati per inviare gli errori e avvisi, rispettivamente ([direttive diagnostica](lexical-structure.md#diagnostic-directives)).
*  `#region` e `#endregion`, che sono usati per contrassegnare in modo esplicito le sezioni del codice sorgente ([direttive Region](lexical-structure.md#region-directives)).
*  `#pragma`, che consente di specificare le informazioni contestuali facoltative del compilatore ([direttive Pragma](lexical-structure.md#pragma-directives)).

Una direttiva di pre-elaborazione sempre occupa una riga separata del codice sorgente e inizia sempre con un `#` carattere e un nome di direttiva pre-elaborazione. Gli spazi vuoti possono verificarsi prima la `#` carattere così come tra la `#` carattere e il nome della direttiva.

Una riga di origine contenente un `#define`, `#undef`, `#if`, `#elif`, `#else`, `#endif`, `#line`, o `#endregion` direttiva può terminare con un commento a riga singola. Commenti delimitati (il `/* */` stile di visualizzazione dei commenti) non sono consentite righe nel codice sorgente contenente le direttive di pre-elaborazione.

Direttive di pre-elaborazione non sono i token e non fanno parte della grammatica sintattica del linguaggio c#. Tuttavia, le direttive di pre-elaborazione possono essere utilizzate per includere o escludere le sequenze di token e possono in questo modo influire sul significato di un programma c#. Ad esempio, quando viene compilato, il programma:
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
risultati nella stessa sequenza esatta dei token del programma:
```csharp
class C
{
    void F() {}
    void I() {}
}
```

Pertanto, mentre al livello lessicale, i due programmi sono piuttosto diversi, sintatticamente, sono identici.

### <a name="conditional-compilation-symbols"></a>Simboli di compilazione condizionale

La funzionalità di compilazione condizionale fornita dal `#if`, `#elif`, `#else`, e `#endif` direttive viene controllato tramite espressioni di pre-elaborazione ([pre-elaborazione espressioni](lexical-structure.md#pre-processing-expressions)) e simboli di compilazione condizionale.

```antlr
conditional_symbol
    : '<Any identifier_or_keyword except true or false>'
    ;
```

Un simbolo di compilazione condizionale ha due stati possibili: ***definite*** oppure ***indefinito***. All'inizio dell'elaborazione lessicale di un file di origine, un simbolo di compilazione condizionale non è definito, a meno che non è stato definito in modo esplicito tramite un meccanismo esterno (ad esempio, un'opzione del compilatore da riga di comando). Quando un `#define` direttiva viene elaborata, il simbolo di compilazione condizionale indicato nell'istruzione diventa definito nel file di origine. Il simbolo rimarrà definito fino a quando un `#undef` direttiva per lo stesso simbolo viene elaborato o finché non viene raggiunta la fine del file di origine. Un'implicazione di ciò è che `#define` e `#undef` direttive in un file di origine non hanno alcun effetto sugli altri file di origine nello stesso programma.

Quando viene fatto riferimento in un'espressione di pre-elaborazione, un simbolo di compilazione condizionale definito ha il valore booleano `true`, e un simbolo di compilazione condizionale non definito ha il valore booleano `false`. Non è necessario che i simboli di compilazione condizionale in modo esplicito prima dichiararla vi viene fatto riferimento nelle espressioni pre-elaborazione. Al contrario, i simboli non dichiarati non vengono semplicemente definiti e pertanto avere il valore `false`.

Lo spazio dei nomi per i simboli di compilazione condizionale è distinto e separato da tutte le altre entità denominata in un programma c#. Simboli di compilazione condizionale è possano fare riferimento solo nelle `#define` e `#undef` direttive e nelle espressioni pre-elaborazione.

### <a name="pre-processing-expressions"></a>Le espressioni di pre-elaborazione

Le espressioni di pre-elaborazione possono verificarsi nelle `#if` e `#elif` direttive. Gli operatori `!`, `==`, `!=`, `&&` e `||` sono consentiti nelle espressioni, di pre-elaborazione e le parentesi possono essere usate per il raggruppamento.

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

Quando viene fatto riferimento in un'espressione di pre-elaborazione, un simbolo di compilazione condizionale definito ha il valore booleano `true`, e un simbolo di compilazione condizionale non definito ha il valore booleano `false`.

Valutazione di un'espressione di pre-elaborazione sempre produce un valore booleano. Le regole di valutazione di un'espressione di pre-elaborazione sono identici a quelli per un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)), ad eccezione del fatto che l'unica entità definite dall'utente che è possibile fare riferimento siano simboli di compilazione condizionale .

### <a name="declaration-directives"></a>Direttive di dichiarazione

Le direttive di dichiarazione vengono utilizzate per definire o annullare la definizione dei simboli di compilazione condizionale.

```antlr
pp_declaration
    : whitespace? '#' whitespace? 'define' whitespace conditional_symbol pp_new_line
    | whitespace? '#' whitespace? 'undef' whitespace conditional_symbol pp_new_line
    ;

pp_new_line
    : whitespace? single_line_comment? new_line
    ;
```

L'elaborazione di un `#define` direttiva fa sì che il simbolo di compilazione condizionale specificato diventare definito, a partire da riga di origine che segue la direttiva. Analogamente, l'elaborazione di un `#undef` direttiva fa sì che il simbolo di compilazione condizionale specificato diventi indefinito, a partire da riga di origine che segue la direttiva.

Eventuali `#define` e `#undef` direttive in un file di origine devono verificarsi prima del primo *token* ([token](lexical-structure.md#tokens)) nel file di origine; in caso contrario, in fase di compilazione si verifica un errore. In pratica, `#define` e `#undef` direttive devono precedere qualsiasi codice"reale" nel file di origine.

Esempio:
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
è valido perché il `#define` direttive precedono il primo token (il `namespace` parola chiave) nel file di origine.

L'esempio seguente genera un errore in fase di compilazione perché un `#define` segue codice reale:
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

Oggetto `#define` può definire un simbolo di compilazione condizionale già definite, senza che qualsiasi nuova `#undef` per tale simbolo. L'esempio seguente definisce un simbolo di compilazione condizionale `A` e definisce anche in questo caso.
```csharp
#define A
#define A
```

Oggetto `#undef` potrebbe "Rimuovi" un simbolo di compilazione condizionale non è definito. L'esempio seguente definisce un simbolo di compilazione condizionale `A` e quindi rimuove la definizione due volte; Sebbene il secondo `#undef` non ha alcun effetto, è ancora valido.
```csharp
#define A
#undef A
#undef A
```

### <a name="conditional-compilation-directives"></a>Direttive di compilazione condizionale

Le direttive di compilazione condizionale consentono di includere o escludere parti di un file di origine in modo condizionale.

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

Come indicato dalla sintassi, le direttive di compilazione condizionale devono essere scritto come set costituiti, in ordine, un `#if` direttiva, zero o più `#elif` direttive, zero o uno `#else` direttiva e un `#endif` direttiva. Tra tali direttive sono sezioni condizionali del codice sorgente. Ogni sezione è controllata dalla direttiva immediatamente precedente. Una sezione condizionale possa contenere direttive di compilazione condizionale annidato purché queste direttive formano set completi.

Oggetto *pp_conditional* consente di selezionare al massimo uno tra i contenuti *conditional_section*s durante la normale elaborazione lessicali:

*  Il *pp_expression*s delle `#if` e `#elif` direttive vengono valutate nell'ordine fino a quando non uno produce `true`. Se un'espressione produce `true`, il *conditional_section* della direttiva corrispondente sia selezionata.
*  Se tutti gli oggetti *pp_expression*yield s `false`e, se un' `#else` direttiva è presente, il *conditional_section* del `#else` direttiva è selezionata.
*  In caso contrario, no *conditional_section* sia selezionata.

Oggetto selezionato *conditional_section*, se presente, viene elaborato come un normale *input_section*: codice sorgente contenuto nella sezione deve essere conformi alla grammatica lessicale; i token vengono generati dall'origine codice nella sezione; e le direttive di pre-elaborazione nella sezione hanno effetti prescritti.

Il rimanente *conditional_section*s, se presente, vengono elaborati come *skipped_section*s: ad eccezione di direttive di pre-elaborazione, il codice sorgente nella sezione necessario non rispetta il lessicale grammatica; no i token vengono generati dal codice sorgente nella sezione; e le direttive di pre-elaborazione della sezione devono essere livello lessicale corrette ma non vengono elaborate in caso contrario. All'interno di un *conditional_section* che viene elaborata come una *skipped_section*qualsiasi nidificato *conditional_section*s (contenuti in annidate `#if`... `#endif` e `#region`... `#endregion` costruisce) inoltre vengono elaborati come *skipped_section*s.

Nell'esempio seguente viene illustrata la compilazione condizionale possono annidare le direttive:
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

Ad eccezione di direttive di pre-elaborazione, il codice sorgente ignorato non soggetti a analisi lessicale. Ad esempio, è valida nonostante il commento senza terminazione in quanto segue il `#else` sezione:
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

Si noti tuttavia che le direttive di pre-elaborazione sono necessarie sia lessicalmente corretto anche in ignorati sezioni del codice sorgente.

Direttive di pre-elaborazione non vengono elaborate quando vengono visualizzate all'interno degli elementi di input a più righe. Ad esempio, il programma:
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
risultati nell'output:
```
hello,
#if Debug
        world
#else
        Nebraska
#endif
```

In casi particolari, il set di direttive di pre-elaborazione che viene elaborato potrebbe dipendere la valutazione del *pp_expression*. Esempio:
```csharp
#if X
    /*
#else
    /* */ class Q { }
#endif
```
genera sempre lo stesso flusso di token (`class` `Q` `{` `}`), indipendentemente dal o meno `X` è definito. Se `X` è definito, vengono elaborate solo le direttive `#if` e `#endif`, a causa di commenti su più righe. Se `X` è definito, quindi tre direttive (`#if`, `#else`, `#endif`) fanno parte del set di direttiva.

### <a name="diagnostic-directives"></a>Direttive di diagnostica

Le direttive di diagnostica vengono utilizzate per generare in modo esplicito i messaggi di errore e avviso segnalati nello stesso modo degli altri errori di compilazione e gli avvisi.

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

Esempio:
```csharp
#warning Code review needed before check-in

#if Debug && Retail
    #error A build can't be both debug and retail
#endif

class Test {...}
```
sempre genera un avviso ("revisione del codice necessaria prima dell'archiviazione") e genera un errore in fase di compilazione ("una compilazione non può essere sia di debug delle vendite al dettaglio") se i simboli condizionali `Debug` e `Retail` sono definito. Si noti che un *pp_message* può contenere testo arbitrario, in particolare, deve necessariamente contenere ben formato token, come illustrato per la virgoletta singola nella parola `can't`.

### <a name="region-directives"></a>Direttive regione

Le direttive region vengono utilizzate per contrassegnare in modo esplicito le aree del codice sorgente.

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

Alcun significato semantico non è collegato a un'area; le aree devono essere utilizzati dal programmatore o mediante strumenti automatizzati per contrassegnare una sezione del codice sorgente. Il messaggio specificato un `#region` o `#endregion` direttiva analogamente non ha alcun significato semantico; serve semplicemente a identificare l'area geografica. Corrispondenza `#region` e `#endregion` direttive potrebbero essere diversa *pp_message*s.

L'elaborazione lessicale di un'area:
```csharp
#region
...
#endregion
```
corrisponde esattamente all'elaborazione lessicale di una direttiva di compilazione condizionale del form:
```csharp
#if true
...
#endif
```

### <a name="line-directives"></a>Direttive di riga

Le direttive di riga possono essere usate per modificare i numeri di riga e nomi di file di origine che vengono segnalati dal compilatore in output, ad esempio avvisi ed errori e usate da attributi informativi sul chiamante ([attributi informativi sul chiamante](attributes.md#caller-info-attributes)).

Direttive di riga vengono utilizzate più comunemente in metaprogrammazione gli strumenti che generano codice sorgente c# da altri input di testo.

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

Se non si specifica `#line` direttive sono presenti, il compilatore segnala i numeri di riga true e nomi di file di origine nel relativo output. Durante l'elaborazione di un `#line` direttiva che include un *line_indicator* non `default`, il compilatore considera la riga dopo la direttiva come se avessero il numero di riga (e nome file, se specificato).

Oggetto `#line default` direttiva inverte l'effetto di tutte le direttive #line precedenti. Il compilatore segnala le informazioni sulla riga true per le righe successive, esattamente come se no `#line` direttive fosse state elaborate.

Oggetto `#line hidden` direttiva non ha alcun effetto sul file e i numeri di riga segnalati per errore messaggi, ma influisce sul debug a livello di origine. Durante il debug, tutte le righe tra un `#line hidden` direttiva e il successivo `#line` direttiva (non `#line hidden`) non dispone di alcuna informazione di numeri di riga. Quando si esegue l'istruzione tramite il codice nel debugger, queste righe verranno completamente ignorate.

Si noti che un *nome_file* differisce da un valore letterale stringa normale in caratteri di escape non vengono elaborati; il "`\`" carattere indica semplicemente un carattere di barra rovesciata comune all'interno di un *file_name*.

### <a name="pragma-directives"></a>Direttive pragma

Il `#pragma` direttiva pre-elaborazione viene usata per specificare le informazioni contestuali facoltative al compilatore. Le informazioni specificate un `#pragma` direttiva non verrà mai modificato la semantica di programma.

```antlr
pp_pragma
    : whitespace? '#' whitespace? 'pragma' whitespace pragma_body pp_new_line
    ;

pragma_body
    : pragma_warning_body
    ;
```

C# fornisce `#pragma` direttive per controllare gli avvisi del compilatore. Le versioni future del linguaggio possono includere altri `#pragma` direttive. Per garantire l'interoperabilità con altri compilatori c#, il compilatore Microsoft c# non generano errori di compilazione non conosciuto `#pragma` direttive; non tali direttive tuttavia generare avvisi.

#### <a name="pragma-warning"></a>Avviso (pragma)

Il `#pragma warning` direttiva viene usata per disabilitare o il ripristino di tutti o un set specifico di avviso messaggi durante la compilazione del testo di programma successive.

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

Oggetto `#pragma warning` direttiva che omette l'elenco degli avvisi di influisce su tutti gli avvisi. Oggetto `#pragma warning` direttiva di include un elenco di avvisi interessa solo gli avvisi che vengono specificati nell'elenco.

Oggetto `#pragma warning disable` direttiva Disabilita tutti o il set specificato di avvisi.

Oggetto `#pragma warning restore` direttivo ripristini tutti o il set specificato di avvisi allo stato in cui è stato in vigore all'inizio di un'unità di compilazione. Si noti che se un particolare messaggio di avviso è stata disabilitata dall'esterno, un `#pragma warning restore` (se per tutti o l'avviso specifico) non sarà possibile nuovamente abilitare tale avviso.

Nell'esempio seguente viene illustrato l'utilizzo di `#pragma warning` disabilitare temporaneamente il messaggio di avviso segnalato quando obsoleto membri referenziati, utilizzando il numero di avviso del compilatore Microsoft c#.
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
