---
ms.openlocfilehash: 5fbe0267b5b33b1a24dbdca493d118c576092573
ms.sourcegitcommit: 7f7fc6e9e195e51b7ff8229aeaa70aa9fbbb63cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876922"
---
# <a name="lexical-structure"></a>Struttura lessicale

## <a name="programs"></a>Programs

Un C# ***programma*** è costituito da uno o più ***file di origine***, noti formalmente come ***unità*** di compilazione ([unità di compilazione](namespaces.md#compilation-units)). Un file di origine è una sequenza ordinata di caratteri Unicode. I file di origine hanno in genere una corrispondenza uno-a-uno con i file in un file system, ma questa corrispondenza non è obbligatoria. Per la massima portabilità, è consigliabile che i file in un file system siano codificati con la codifica UTF-8.

In teoria, un programma viene compilato con tre passaggi:

1. Trasformazione, che converte un file da un particolare repertorio di caratteri e uno schema di codifica in una sequenza di caratteri Unicode.
2. Analisi lessicale, che converte un flusso di caratteri di input Unicode in un flusso di token.
3. Analisi sintattica, che converte il flusso di token in codice eseguibile.

## <a name="grammars"></a>Grammatiche

Questa specifica presenta la sintassi del linguaggio C# di programmazione utilizzando due grammatiche. La ***grammatica lessicale*** ([grammatica lessicale](lexical-structure.md#lexical-grammar)) definisce il modo in cui i caratteri Unicode vengono combinati per i terminatori di riga del modulo, gli spazi vuoti, i commenti, i token e le direttive di pre-elaborazione. La ***grammatica*** sintattica ([grammatica sintattica](lexical-structure.md#syntactic-grammar)) definisce il modo in cui i token risultanti dalla grammatica lessicale C# vengono combinati per formare i programmi.

### <a name="grammar-notation"></a>Notazione grammatica

Le grammatiche lessicali e sintattiche vengono presentate in forma Backus-Naur usando la notazione dello strumento di grammatica ANTLR.

### <a name="lexical-grammar"></a>Grammatica lessicale

La grammatica lessicale di C# viene presentata in [analisi lessicale](lexical-structure.md#lexical-analysis), [token](lexical-structure.md#tokens)e [direttive di pre-elaborazione](lexical-structure.md#pre-processing-directives). I simboli terminali della grammatica lessicale sono i caratteri del set di caratteri Unicode e la grammatica lessicale specifica il modo in cui i caratteri vengono combinati per formare token ([token](lexical-structure.md#tokens)), spazi vuoti ([spazi vuoti](lexical-structure.md#white-space)), commenti ([Commenti](lexical-structure.md#comments)) e direttive di pre-elaborazione ([direttive di pre-elaborazione](lexical-structure.md#pre-processing-directives)).

Ogni file di origine in C# un programma deve essere conforme alla produzione di *input* della grammatica lessicale ([analisi lessicale](lexical-structure.md#lexical-analysis)).

### <a name="syntactic-grammar"></a>Grammatica sintattica

La grammatica sintattica C# di viene presentata nei capitoli e nelle appendici che seguono questo capitolo. I simboli terminali della grammatica sintattica sono i token definiti dalla grammatica lessicale e la grammatica sintattica specifica il modo in cui i token vengono combinati C# per formare i programmi.

Ogni file di origine in C# un programma deve essere conforme alla produzione *compilation_unit* della grammatica sintattica ([unità di compilazione](namespaces.md#compilation-units)).

## <a name="lexical-analysis"></a>Analisi lessicale

La produzione di *input* definisce la struttura lessicale di C# un file di origine. Ogni file di origine in C# un programma deve essere conforme alla produzione di grammatica lessicale.

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

Cinque elementi di base costituiscono la struttura lessicale di un C# file di origine: Terminatori di riga ([terminatori di riga](lexical-structure.md#line-terminators)), spazi vuoti ([spazi vuoti](lexical-structure.md#white-space)), commenti ([Commenti](lexical-structure.md#comments)), token ([token](lexical-structure.md#tokens)) e direttive di pre-elaborazione ([direttive di pre-elaborazione](lexical-structure.md#pre-processing-directives)). Di questi elementi di base, solo i token sono significativi nella grammatica sintattica di C# un programma ([grammatica sintattica](lexical-structure.md#syntactic-grammar)).

L'elaborazione lessicale di un C# file di origine è costituita dalla riduzione del file in una sequenza di token che diventa l'input per l'analisi sintattica. I terminatori di riga, gli spazi vuoti e i commenti possono essere utilizzati per separare i token, mentre le direttive di pre-elaborazione possono causare l'omissione di sezioni del file di origine. in caso contrario, questi elementi lessicali non hanno C# alcun effetto sulla struttura sintattica di un programma.

Nel caso di valori letterali stringa interpolati ([valori letterali stringa interpolata](lexical-structure.md#interpolated-string-literals)), un singolo token viene inizialmente prodotto dall'analisi lessicale, ma viene suddiviso in diversi elementi di input che vengono ripetutamente sottoposti a analisi lessicale fino a quando non tutte le interpolate i valori letterali stringa sono stati risolti. I token risultanti vengono quindi usati come input per l'analisi sintattica.

Quando più produzioni di grammatica lessicali corrispondono a una sequenza di caratteri in un file di origine, l'elaborazione lessicale costituisce sempre l'elemento lessicale più lungo possibile. Ad esempio, la sequenza `//` di caratteri viene elaborata come l'inizio di un commento a riga singola, perché tale elemento lessicale è più lungo di un singolo `/` token.

### <a name="line-terminators"></a>Terminatori di riga

I caratteri di terminazione di riga dividono C# i caratteri di un file di origine in righe.

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

Per la compatibilità con gli strumenti di modifica del codice sorgente che aggiungono marcatori di fine file e per consentire la visualizzazione di un file di origine come sequenza di righe terminate correttamente, le trasformazioni seguenti vengono applicate, in ordine, a ogni C# file di origine in un programma:

*  Se l'ultimo carattere del file di origine è un carattere di controllo Z (`U+001A`), questo carattere viene eliminato.
*  Un carattere di ritorno a capo`U+000D`() viene aggiunto alla fine del file di origine se il file di origine non è vuoto e l'ultimo carattere del file di origine non è un ritorno a capo (`U+000D`), un avanzamento riga (`U+000A`), un separatore di riga (`U+2028`) o un separatore di paragrafo`U+2029`().

### <a name="comments"></a>Commenti

Sono supportati due tipi di Commenti: Commenti a riga singola e commenti delimitati. I ***Commenti a riga singola*** iniziano con i `//` caratteri e si estendono alla fine della riga di origine. I ***commenti delimitati*** iniziano con i `/*` caratteri e terminano con `*/`i caratteri. I commenti delimitati possono estendersi su più righe.

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

I commenti non sono annidati. `/*` Le sequenze di caratteri e `*/` non hanno un significato speciale all' `//` interno di un commento e le sequenze `//` di `/*` caratteri e non hanno un significato speciale in un commento delimitato.

I commenti non vengono elaborati all'interno di valori letterali carattere e stringa.

Esempio
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
include un commento delimitato.

Esempio
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
Mostra diversi commenti a riga singola.

### <a name="white-space"></a>Spazio vuoto

Lo spazio vuoto viene definito come qualsiasi carattere con la classe Unicode ZS (che include lo spazio), nonché il carattere di tabulazione orizzontale, il carattere di tabulazione verticale e il carattere di avanzamento del modulo.

```antlr
whitespace
    : '<Any character with Unicode class Zs>'
    | '<Horizontal tab character (U+0009)>'
    | '<Vertical tab character (U+000B)>'
    | '<Form feed character (U+000C)>'
    ;
```

## <a name="tokens"></a>token

Esistono diversi tipi di token: identificatori, parole chiave, valori letterali, operatori e segni di punteggiatura. Gli spazi vuoti e i commenti non sono token, ma fungono da separatori per i token.

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

Una sequenza di escape di caratteri Unicode rappresenta un carattere Unicode. Le sequenze di escape di caratteri Unicode vengono elaborate in identificatori ([identificatori](lexical-structure.md#identifiers)), valori letterali carattere ([valori letterali carattere](lexical-structure.md#character-literals)) e valori letterali stringa regolari ([valori letterali stringa](lexical-structure.md#string-literals)). Un carattere di escape Unicode non viene elaborato in nessun'altra posizione, ad esempio per formare un operatore, un punteggiatore o una parola chiave.

```antlr
unicode_escape_sequence
    : '\\u' hex_digit hex_digit hex_digit hex_digit
    | '\\U' hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit
    ;
```

Una sequenza di escape Unicode rappresenta il singolo carattere Unicode formato dal numero esadecimale che segue`\u`i caratteri "`\U`" o "". Poiché C# utilizza una codifica a 16 bit di punti di codice Unicode in caratteri e valori stringa, un carattere Unicode compreso nell'intervallo da u + 10000 a u + 10FFFF non è consentito in un valore letterale carattere e viene rappresentato utilizzando una coppia di surrogati Unicode in un valore letterale stringa. I caratteri Unicode con punti di codice precedenti a 0x10FFFF non sono supportati.

Non vengono eseguite più traduzioni. Ad esempio, il valore letterale`\u005Cu005C`stringa "" è equivalente`\u005C`a "" invece`\`di "". Il valore `\u005C` Unicode è il carattere "`\`".

Esempio
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
Mostra diversi usi di `\u0066`, ovvero la sequenza di escape per la lettera "`f`". Il programma è equivalente a
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

Le regole per gli identificatori indicati in questa sezione corrispondono esattamente a quelle consigliate dall'allegato 31 Unicode standard, ad eccezione del fatto che il carattere di sottolineatura è consentito come carattere iniziale (come nel linguaggio di programmazione C), le sequenze di escape Unicode sono consentito negli identificatori e il carattere "`@`" è consentito come prefisso per consentire l'uso delle parole chiave come identificatori.

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

Per informazioni sulle classi di caratteri Unicode indicate sopra, vedere la sezione 4,5 dello standard Unicode, versione 3,0.

Esempi di identificatori validi sono "`identifier1`", "`_identifier2`" e "`@if`".

Un identificatore in un programma di conformità deve essere nel formato canonico definito dal formato di normalizzazione Unicode C, come definito dall'allegato 15 Unicode standard. Il comportamento quando viene rilevato un identificatore non nel formato di normalizzazione C viene definito dall'implementazione; Tuttavia, non è necessaria una diagnostica.

Il prefisso "`@`" consente l'uso di parole chiave come identificatori, che risulta utile quando si interagisce con altri linguaggi di programmazione. Il carattere `@` non è in realtà parte dell'identificatore, quindi l'identificatore può essere visualizzato in altri linguaggi come identificatore normale, senza il prefisso. Un identificatore con `@` prefisso è denominato ***identificatore Verbatim***. L'uso del `@` prefisso per gli identificatori che non sono parole chiave è consentito, ma fortemente sconsigliato come una questione di stile.

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
definisce una classe denominata "`class`" con un metodo statico denominato "`static`" che accetta un parametro denominato "`bool`". Si noti che poiché le escape Unicode non sono consentite nelle parole chiave`cl\u0061ss`, il token "" è un identificatore ed è lo stesso`@class`identificatore di "".

Due identificatori sono considerati uguali se sono identici dopo l'applicazione delle trasformazioni seguenti, nell'ordine:

*  Il prefisso "`@`", se usato, viene rimosso.
*  Ogni *unicode_escape_sequence* viene trasformato nel carattere Unicode corrispondente.
*  Tutti i *formatting_character*vengono rimossi.

Gli identificatori contenenti due caratteri di sottolineatura consecutivi (`U+005F`) sono riservati per l'utilizzo da parte dell'implementazione. Un'implementazione, ad esempio, può fornire parole chiave estese che iniziano con due caratteri di sottolineatura.

### <a name="keywords"></a>Parole chiave

Una ***parola chiave*** è una sequenza di caratteri di tipo identificatore riservata e non può essere usata come identificatore tranne quando è preceduta dal `@` carattere.

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

In alcuni punti della grammatica, gli identificatori specifici hanno un significato speciale, ma non sono parole chiave. Tali identificatori sono talvolta denominati "parole chiave contestuali". Ad esempio, all'interno di una dichiarazione di proprietà`get`, gli identificatori "" e "`set`" hanno un significato speciale ([funzioni di accesso](classes.md#accessors)). Un identificatore diverso da `get` o `set` non è mai consentito in questi percorsi, quindi questo utilizzo non è in conflitto con l'utilizzo di queste parole come identificatori. In altri casi, ad esempio con l'identificatore "`var`" nelle dichiarazioni di variabili locali tipizzate in modo implicito ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)), una parola chiave contestuale può entrare in conflitto con i nomi dichiarati. In questi casi, il nome dichiarato ha la precedenza sull'utilizzo dell'identificatore come parola chiave contestuale.

### <a name="literals"></a>Valori letterali

Un valore ***letterale*** è una rappresentazione del codice sorgente di un valore.

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

Sono presenti due valori letterali booleani `true` : `false`e.

```antlr
boolean_literal
    : 'true'
    | 'false'
    ;
```

Il tipo di *boolean_literal* è `bool`.

#### <a name="integer-literals"></a>Valori letterali Integer

I valori letterali integer vengono usati per scrivere valori `int`di `uint`tipo `long`,, `ulong`e. I valori letterali integer hanno due formati possibili: Decimal ed esadecimale.

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

Il tipo di un valore letterale integer viene determinato nel modo seguente:

*  Se il valore letterale non ha alcun suffisso, presenta il primo di questi tipi in cui può essere rappresentato `int`il `uint`relativo valore `ulong`:,, `long`,.
*  Se il valore letterale è suffisso `u`da `U` o, dispone del primo di questi tipi in cui può essere rappresentato il relativo `uint`valore `ulong`:,.
*  Se il valore letterale è suffisso `l`da `L` o, dispone del primo di questi tipi in cui può essere rappresentato il relativo `long`valore `ulong`:,.
*  Se il valore letterale è suffisso `Ul`da `uL` `UL`, `ul`, `LU`, `Lu`, `lU`,, `lu`o, è di tipo `ulong`.

Se il valore rappresentato da un valore letterale integer non è compreso nell' `ulong` intervallo del tipo, si verifica un errore in fase di compilazione.

Come una questione di stile, si consiglia di usare "`L`" invece di "`l`" durante la scrittura di valori letterali di `long`tipo, poiché è facile confondere la lettera "`l`" con la cifra "`1`".

Per consentire la scrittura dei valori `int` più `long` piccoli e possibili come valori letterali integer decimali, sono disponibili le due regole seguenti:

* Quando un *decimal_integer_literal* con valore 2147483648 (2 ^ 31) e nessun *integer_type_suffix* viene visualizzato come token immediatamente successivo a un token operatore meno unario ([operatore unario meno](expressions.md#unary-minus-operator)), il risultato è una costante di tipo `int`con il valore-2147483648 (-2 ^ 31). In tutti gli altri casi, tale *decimal_integer_literal* è di tipo `uint`.
* Quando un *decimal_integer_literal* con il valore 9.223.372.036.854.775.808 (2 ^ 63) e nessun *integer_type_suffix* o *integer_type_suffix* `L` o `l` viene visualizzato come token immediatamente dopo un meno unario token operatore ([operatore unario meno](expressions.md#unary-minus-operator)), il risultato è una costante di `long` tipo con il valore-9.223.372.036.854.775.808 (-2 ^ 63). In tutti gli altri casi, tale *decimal_integer_literal* è di tipo `ulong`.

#### <a name="real-literals"></a>Valori letterali reali

I valori letterali reali vengono usati per scrivere valori `float`di `double`tipo, `decimal`e.

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

Se non viene specificato alcun *real_type_suffix* , il tipo del valore letterale `double`reale è. In caso contrario, il suffisso di tipo reale determina il tipo del valore letterale reale, come indicato di seguito:

*  Un valore letterale reale con `F` suffisso o `f` è `float`di tipo. Ad esempio, `1f`i valori letterali `1.5f` `1e10f`,, e `123.456F` sono tutti di tipo `float`.
*  Un valore letterale reale con `D` suffisso o `d` è `double`di tipo. Ad esempio, `1d`i valori letterali `1.5d` `1e10d`,, e `123.456D` sono tutti di tipo `double`.
*  Un valore letterale reale con `M` suffisso o `m` è `decimal`di tipo. Ad esempio, `1m`i valori letterali `1.5m` `1e10m`,, e `123.456M` sono tutti di tipo `decimal`. Il valore letterale viene convertito `decimal` in un valore accettando il valore esatto e, se necessario, l'arrotondamento al valore rappresentabile più vicino utilizzando l'arrotondamento del Banker ([il tipo decimale](types.md#the-decimal-type)). Qualsiasi scala apparente nel valore letterale viene mantenuta, a meno che il valore non venga arrotondato o se il valore è zero, nel qual caso il segno e la scala saranno pari a 0. Di conseguenza, il `2.900m` valore letterale verrà analizzato in modo da formare `0`il separatore decimale `3`con segno, coefficiente `2900`e scala.

Se il valore letterale specificato non può essere rappresentato nel tipo indicato, si verifica un errore in fase di compilazione.

Il valore di un valore letterale reale `float` di `double` tipo o viene determinato tramite la modalità "arrotonda a" più vicina "di IEEE.

Si noti che in un valore letterale reale, le cifre decimali sono sempre obbligatorie dopo il separatore decimale. Ad esempio, `1.3F` è un valore letterale `1.F` reale, ma non lo è.

#### <a name="character-literals"></a>Valori letterali carattere

Un valore letterale carattere rappresenta un singolo carattere e in genere è costituito da un carattere tra `'a'`virgolette, come in.

Nota: La notazione della grammatica ANTLR rende più confuse le operazioni seguenti. In ANTLR, quando si scrive `\'` , si tratta di una singola `'`virgoletta. E quando si scrive `\\` , si sta per una singola `\`barra rovesciata. La prima regola per un valore letterale carattere significa quindi che inizia con una virgoletta singola, quindi con un carattere e quindi con una virgoletta singola. E le undici possibili sequenze di escape semplici sono `\'` `\\`, `\"` `\0`,,, `\a`, `\b`, `\f`, `\n`, `\r`, `\t` ,`\v`.

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

Un carattere che segue un carattere barra rovesciata (`\`) in un *carattere* deve essere uno dei `'` `\`caratteri seguenti: `"`,, `0`, `a`, `b`, `f` , , `n`, `r`, `t`, `u`, `U`, `x`, `v`. In caso contrario, si verifica un errore in fase di compilazione.

Una sequenza di escape esadecimale rappresenta un singolo carattere Unicode, con il valore formato dal numero esadecimale`\x`che segue "".

Se il valore rappresentato da un valore letterale carattere è `U+FFFF`maggiore di, si verifica un errore in fase di compilazione.

Una sequenza di escape di caratteri Unicode ([sequenze di escape di caratteri Unicode](lexical-structure.md#unicode-character-escape-sequences)) in un valore letterale carattere `U+0000` deve `U+FFFF`essere compresa nell'intervallo tra e.

Una semplice sequenza di escape rappresenta una codifica dei caratteri Unicode, come descritto nella tabella seguente.


| __Sequenza di escape__ | __Nome carattere__ | __Codifica Unicode__ |
|---------------------|--------------------|----------------------|
| `\'`                | Virgoletta singola       | `0x0027`             | 
| `\"`                | Virgoletta doppia       | `0x0022`             | 
| `\\`| Barra rovesciata |`0x005C`             | 
| `\0`                | Null               | `0x0000`             | 
| `\a`                | Avviso              | `0x0007`             | 
| `\b`                | Backspace          | `0x0008`             | 
| `\f`                | Avanzamento carta          | `0x000C`             | 
| `\n`                | Nuova riga           | `0x000A`             | 
| `\r`                | Ritorno a capo    | `0x000D`             | 
| `\t`                | Tabulazione orizzontale     | `0x0009`             | 
| `\v`                | Tabulazione verticale       | `0x000B`             | 

Il tipo di *character_literal* è `char`.

#### <a name="string-literals"></a>Valori letterali stringa

C#supporta due forme di valori letterali stringa: ***valori letterali stringa normali*** e ***valori letterali stringa Verbatim***.

Un valore letterale stringa regolare è costituito da zero o più caratteri racchiusi tra `"hello"`virgolette doppie, come in, e può includere sia sequenze `\t` di escape semplici (ad esempio per il carattere di tabulazione) che sequenze di escape esadecimali e Unicode.

Un valore letterale stringa Verbatim è `@` costituito da un carattere seguito da un carattere di virgolette doppie, da zero o più caratteri e da un carattere di virgolette doppie di chiusura. Un esempio semplice è `@"hello"`. In un valore letterale stringa Verbatim i caratteri compresi tra i delimitatori vengono interpretati come Verbatim, l'unica eccezione è rappresentata da un *quote_escape_sequence*. In particolare, le sequenze di escape semplici e le sequenze di escape esadecimali e Unicode non vengono elaborate nei valori letterali stringa Verbatim. Un valore letterale stringa Verbatim può estendersi su più righe.

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

`\`Un carattere che segue un carattere barra rovesciata () in una *regular_string_literal_character* deve essere uno dei `'` `\`caratteri seguenti: `"`,, `0`, `a`, `b` , , `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`. In caso contrario, si verifica un errore in fase di compilazione.

Esempio
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
Mostra un'ampia gamma di valori letterali stringa. L'ultimo valore letterale `j`stringa,, è un valore letterale stringa Verbatim che si estende su più righe. I caratteri tra virgolette, inclusi gli spazi vuoti, ad esempio i caratteri di nuova riga, vengono conservati testualmente.

Poiché una sequenza di escape esadecimale può avere un numero variabile di cifre esadecimali, `"\x123"` il valore letterale stringa contiene un singolo carattere con valore esadecimale 123. Per creare una stringa contenente il carattere con valore esadecimale 12 seguito dal carattere 3, è possibile scrivere `"\x00123"` o `"\x12" + "3"` in alternativa.

Il tipo di *string_literal* è `string`.

Ogni valore letterale stringa non comporta necessariamente la creazione di una nuova istanza di stringa. Quando nello stesso programma sono presenti due o più valori letterali stringa equivalenti in base all'operatore di uguaglianza delle stringhe ([operatori di uguaglianza di stringa](expressions.md#string-equality-operators)), questi valori letterali stringa fanno riferimento alla stessa istanza di stringa. Ad esempio, l'output prodotto da
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
è `True` perché i due valori letterali fanno riferimento alla stessa istanza di stringa.

#### <a name="interpolated-string-literals"></a>Valori letterali stringa interpolati

I valori letterali stringa interpolati sono simili ai valori letterali stringa, ma contengono `{` fori `}`delimitati da e, dove possono verificarsi espressioni. In fase di esecuzione, le espressioni vengono valutate con lo scopo di inserire i moduli testuali nella stringa in corrispondenza del punto in cui si verifica il foro. La sintassi e la semantica dell'interpolazione di stringhe sono descritte nella sezione ([stringhe interpolate](expressions.md#interpolated-strings)).

Come i valori letterali stringa, i valori letterali stringa interpolati possono essere regolari o Verbatim. I valori letterali stringa regolari interpolati sono `$"` delimitati da e `"`e i valori letterali stringa Verbatim interpolati `"`sono delimitati da `$@"` e.

Analogamente ad altri valori letterali, l'analisi lessicale di un valore letterale di stringa interpolata inizialmente restituisce un singolo token, in base alla grammatica riportata di seguito. Tuttavia, prima dell'analisi sintattica, il singolo token di un valore letterale stringa interpolato viene suddiviso in diversi token per le parti della stringa che racchiudono i buchi e gli elementi di input che si verificano nei buchi vengono di nuovo analizzati in modo lessicale. Questa operazione può a sua volta produrre più valori letterali stringa interpolati da elaborare, ma, in caso di correzione lessicale, condurrà alla fine una sequenza di token per l'elaborazione dell'analisi sintattica.

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

Un token *interpolated_string_literal* viene reinterpretato come più token e altri elementi di input come indicato di seguito, in ordine di occorrenza in *interpolated_string_literal*:

* Le occorrenze dei seguenti vengono riinterpretate come singoli token separati: il segno principale `$` , *interpolated_regular_string_whole*, *interpolated_regular_string_start*, *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_whole*, *interpolated_verbatim_string_start*, *interpolated_verbatim_string_mid* e *interpolated_verbatim_string_end*.
* Le occorrenze di *regular_balanced_text* e *verbatim_balanced_text* tra questi vengono rielaborate come *input_section* ([analisi lessicale](lexical-structure.md#lexical-analysis)) e vengono riinterpretate come la sequenza risultante di elementi di input. Questi possono a sua volta includere token letterali stringa interpolati da riinterpretare.

Con l'analisi sintattica i token vengono ricombinati in un *interpolated_string_expression* ([stringhe interpolate](expressions.md#interpolated-strings)).

Esempi TODO


#### <a name="the-null-literal"></a>Valore letterale null

```antlr
null_literal
    : 'null'
    ;
```

*Null_literal* può essere convertito in modo implicito in un tipo di riferimento o un tipo Nullable.

### <a name="operators-and-punctuators"></a>Operatori e segni di punteggiatura

Esistono diversi tipi di operatori e segni di punteggiatura. Gli operatori vengono utilizzati nelle espressioni per descrivere le operazioni che coinvolgono uno o più operandi. Ad esempio, l'espressione `a + b` usa l' `+` operatore per `a` aggiungere i due operandi e `b`. I segni di punteggiatura sono per il raggruppamento e la separazione.

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

La barra verticale nelle produzioni *right_shift* e *right_shift_assignment* viene usata per indicare che, a differenza di altre produzioni nella grammatica sintattica, non sono consentiti caratteri di qualsiasi tipo (non anche uno spazio vuoto) tra i token. Queste produzioni vengono trattate in modo specifico per consentire la gestione corretta di *type_parameter_list*s ([parametri di tipo](classes.md#type-parameters)).

## <a name="pre-processing-directives"></a>Direttive di pre-elaborazione

Le direttive di pre-elaborazione offrono la possibilità di ignorare in modo condizionale le sezioni dei file di origine, di segnalare le condizioni di errore e di avviso e di delineare aree distinte del codice sorgente. Il termine "direttive di pre-elaborazione" viene usato solo per coerenza con i linguaggi C++ di programmazione C e. In C#non è presente alcun passaggio di pre-elaborazione separato. le direttive di pre-elaborazione vengono elaborate come parte della fase di analisi lessicale.

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

*  `#define`e `#undef`, che vengono usati per definire e annullare la definizione, rispettivamente, dei simboli di compilazione condizionale ([direttive di dichiarazione](lexical-structure.md#declaration-directives)).
*  `#if`, `#elif`, `#else` e`#endif`, che vengono usati per ignorare in modo condizionale le sezioni del codice sorgente ([direttive di compilazione condizionale](lexical-structure.md#conditional-compilation-directives)).
*  `#line`, utilizzato per controllare i numeri di riga emessi per errori e avvisi ([direttive line](lexical-structure.md#line-directives)).
*  `#error`e `#warning`, che vengono utilizzati per emettere errori e avvisi, rispettivamente ([direttive diagnostiche](lexical-structure.md#diagnostic-directives)).
*  `#region`e `#endregion`, che vengono usati per contrassegnare in modo esplicito le sezioni del codice sorgente ([direttive Region](lexical-structure.md#region-directives)).
*  `#pragma`, che viene usato per specificare informazioni contestuali facoltative al compilatore ([direttive pragma](lexical-structure.md#pragma-directives)).

Una direttiva di pre-elaborazione occupa sempre una riga di codice sorgente separata e inizia sempre con `#` un carattere e un nome di direttiva di pre-elaborazione. Gli spazi vuoti possono verificarsi prima `#` del carattere e tra `#` il carattere e il nome della direttiva.

Una riga di origine contenente `#define`una `#undef`direttiva `#if`, `#elif`, `#else`, `#endif`, `#line`,, o`#endregion` può terminare con un commento a riga singola. I `/* */` commenti delimitati (stile dei commenti) non sono consentiti nelle righe di origine contenenti direttive di pre-elaborazione.

Le direttive di pre-elaborazione non sono token e non fanno parte della grammatica sintattica C#di. Tuttavia, le direttive di pre-elaborazione possono essere usate per includere o escludere sequenze di token e possono in questo modo influenzare il significato di C# un programma. Ad esempio, durante la compilazione, il programma:
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
restituisce esattamente la stessa sequenza di token del programma:
```csharp
class C
{
    void F() {}
    void I() {}
}
```

Pertanto, mentre lessicalmente, i due programmi sono piuttosto diversi, sintatticamente identici.

### <a name="conditional-compilation-symbols"></a>Simboli di compilazione condizionale

La funzionalità di compilazione condizionale fornita `#if`dalle `#elif`direttive, `#endif` , `#else`e è controllata tramite espressioni di pre-elaborazione ([espressioni di pre-elaborazione](lexical-structure.md#pre-processing-expressions)) e condizionale simboli di compilazione.

```antlr
conditional_symbol
    : '<Any identifier_or_keyword except true or false>'
    ;
```

Un simbolo di compilazione condizionale ha due stati possibili: ***definito*** o non ***definito***. All'inizio dell'elaborazione lessicale di un file di origine, un simbolo di compilazione condizionale non è definito, a meno che non sia stato definito in modo esplicito da un meccanismo esterno, ad esempio un'opzione del compilatore da riga di comando. Quando una `#define` direttiva viene elaborata, il simbolo di compilazione condizionale denominato in tale direttiva viene definito in tale file di origine. Il simbolo rimane definito fino a `#undef` quando non viene elaborata una direttiva per lo stesso simbolo o fino a quando non viene raggiunta la fine del file di origine. Ciò implica che `#define` le direttive e `#undef` in un file di origine non hanno effetto su altri file di origine nello stesso programma.

Quando viene fatto riferimento in un'espressione di pre-elaborazione, un simbolo di compilazione condizionale definito `true`presenta il valore booleano e un simbolo di compilazione condizionale `false`non definito ha il valore booleano. Non è necessario che i simboli di compilazione condizionale vengano dichiarati in modo esplicito prima di farvi riferimento nelle espressioni di pre-elaborazione. Al contrario, i simboli non dichiarati sono semplicemente indefiniti e `false`quindi hanno il valore.

Lo spazio dei nomi per i simboli di compilazione condizionale è distinto e separato da tutte C# le altre entità denominate di un programma. È possibile fare riferimento ai simboli di compilazione condizionale `#undef` solo nelle `#define` direttive e e nelle espressioni di pre-elaborazione.

### <a name="pre-processing-expressions"></a>Espressioni di pre-elaborazione

Le espressioni di pre-elaborazione possono `#if` verificarsi `#elif` nelle direttive e. Gli operatori `!` `==` ,`&&` , e sonoconsentitinelleespressionidipre-elaborazioneeleparentesipossonoessereutilizzateperilraggruppamento.`||` `!=`

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

Quando viene fatto riferimento in un'espressione di pre-elaborazione, un simbolo di compilazione condizionale definito `true`presenta il valore booleano e un simbolo di compilazione condizionale `false`non definito ha il valore booleano.

La valutazione di un'espressione di pre-elaborazione restituisce sempre un valore booleano. Le regole di valutazione per un'espressione di pre-elaborazione sono le stesse di quelle per un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)), ad eccezione del fatto che le uniche entità definite dall'utente a cui è possibile fare riferimento sono simboli di compilazione condizionale.

### <a name="declaration-directives"></a>Direttive di dichiarazione

Le direttive di dichiarazione vengono usate per definire o annullare la definizione dei simboli di compilazione condizionale.

```antlr
pp_declaration
    : whitespace? '#' whitespace? 'define' whitespace conditional_symbol pp_new_line
    | whitespace? '#' whitespace? 'undef' whitespace conditional_symbol pp_new_line
    ;

pp_new_line
    : whitespace? single_line_comment? new_line
    ;
```

L'elaborazione di una `#define` direttiva determina la definizione del simbolo di compilazione condizionale specificato, a partire dalla riga di origine che segue la direttiva. Analogamente, l'elaborazione di `#undef` una direttiva fa sì che il simbolo di compilazione condizionale specificato diventi non definito, a partire dalla riga di origine che segue la direttiva.

Tutte `#define` le `#undef` direttive e in un file di origine devono essere presenti prima del primo *token* ([token](lexical-structure.md#tokens)) nel file di origine. in caso contrario, si verifica un errore in fase di compilazione. In termini intuitivi `#define` , `#undef` le direttive e devono precedere qualsiasi "codice reale" nel file di origine.

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
è valido perché le `#define` direttive precedono il primo token (la `namespace` parola chiave) nel file di origine.

Nell'esempio seguente viene restituito un errore in fase di compilazione perché `#define` un oggetto segue il codice reale:
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

Un `#define` oggetto può definire un simbolo di compilazione condizionale già definito, senza l' `#undef` intervento di tale simbolo. Nell'esempio seguente viene definito un simbolo `A` di compilazione condizionale che viene quindi definito nuovamente.
```csharp
#define A
#define A
```

Un `#undef` può "annullare la definizione" di un simbolo di compilazione condizionale non definito. Nell'esempio seguente viene definito un simbolo `A` di compilazione condizionale che viene quindi dedefinito due volte. Sebbene il secondo `#undef` non abbia alcun effetto, è ancora valido.
```csharp
#define A
#undef A
#undef A
```

### <a name="conditional-compilation-directives"></a>Direttive di compilazione condizionale

Le direttive di compilazione condizionale vengono utilizzate per includere o escludere in modo condizionale parti di un file di origine.

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

Come indicato dalla sintassi, le direttive di compilazione condizionale devono essere scritte come set costituiti, in ordine, `#if` da una direttiva, da `#elif` zero o più direttive, `#else` da zero o una `#endif` direttiva e da una direttiva. Tra le direttive sono incluse sezioni condizionali del codice sorgente. Ogni sezione è controllata dalla direttiva immediatamente precedente. Una sezione condizionale può contenere le direttive di compilazione condizionale annidate purché queste direttive formino set completi.

Un *pp_conditional* seleziona al massimo uno dei *conditional_section*contenuti per l'elaborazione lessicale normale:

*  Gli *pp_expression*delle `#if` direttive e `#elif` vengono `true`valutati in ordine fino a quando non viene restituito un risultato. Se un'espressione restituisce `true`, viene selezionato il *conditional_section* della direttiva corrispondente.
*  Se tutti i *pp_expression*di `false`produzione e se `#else` è `#else` presente una direttiva, viene selezionato il *conditional_section* della direttiva.
*  In caso contrario, non viene selezionato alcun *conditional_section* .

Il *conditional_section*selezionato, se presente, viene elaborato come un normale *input_section*: il codice sorgente contenuto nella sezione deve rispettare la grammatica lessicale; i token vengono generati dal codice sorgente nella sezione. e le direttive di pre-elaborazione nella sezione presentano gli effetti previsti.

Gli eventuali *conditional_section*rimanenti vengono elaborati come *skipped_section*s: ad eccezione delle direttive di pre-elaborazione, il codice sorgente nella sezione non deve rispettare la grammatica lessicale; non viene generato alcun token dal codice sorgente nella sezione. e le direttive di pre-elaborazione nella sezione devono essere corrette dal punto di vista lessicale ma non vengono elaborate in altro modo. All'interno di un *conditional_section* che viene elaborato come *skipped_section*, qualsiasi *conditional_section*annidato, contenuto in un `#if`oggetto annidato... `#endif` e`#region`... i costrutti) vengono elaborati anche come *skipped_section.* `#endregion`

Nell'esempio seguente viene illustrato il modo in cui le direttive di compilazione condizionale possono annidarsi:
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

Ad eccezione delle direttive di pre-elaborazione, il codice sorgente ignorato non è soggetto all'analisi lessicale. Ad esempio, il codice seguente è valido nonostante il commento senza terminazione `#else` nella sezione:
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

Si noti, tuttavia, che è necessario che le direttive di pre-elaborazione siano corrette in modo lessicale anche nelle sezioni ignorate del codice sorgente.

Le direttive di pre-elaborazione non vengono elaborate quando vengono visualizzate all'interno di elementi di input su più righe. Ad esempio, il programma:
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
Restituisce l'output:
```
hello,
#if Debug
        world
#else
        Nebraska
#endif
```

Nei casi particolari, il set di direttive di pre-elaborazione elaborate potrebbe dipendere dalla valutazione del *pp_expression*. Esempio:
```csharp
#if X
    /*
#else
    /* */ class Q { }
#endif
```
produce sempre lo stesso flusso di token`class` ( `}` `Q` `{` ), indipendentemente dal fatto che `X` sia definito o meno. Se `X` è definito, le uniche direttive elaborate `#if` sono `#endif`e, a causa del commento su più righe. Se `X` non è definito, tre direttive (`#if`, `#else`, `#endif`) fanno parte del set di direttive.

### <a name="diagnostic-directives"></a>Direttive di diagnostica

Le direttive di diagnostica vengono utilizzate per generare in modo esplicito messaggi di errore e di avviso segnalati nello stesso modo in cui si verificano gli errori e gli avvisi in fase di compilazione.

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
genera sempre un avviso ("Revisione del codice necessaria prima dell'archiviazione") e genera un errore in fase di compilazione ("una compilazione non può essere sia debug che retail") se i `Debug` simboli `Retail` condizionali e sono entrambi definiti. Si noti che un *pp_message* può contenere testo arbitrario; in particolare, non è necessario che contenga token ben formati, come illustrato dalla virgoletta singola della parola `can't`.

### <a name="region-directives"></a>Direttive Region

Le direttive Region vengono usate per contrassegnare in modo esplicito le aree del codice sorgente.

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

Nessun significato semantico associato a un'area; le aree sono destinate all'uso da parte del programmatore o da strumenti automatici per contrassegnare una sezione del codice sorgente. Il messaggio specificato in una `#region` direttiva `#endregion` o non ha un significato semantico, ma serve solo per identificare l'area. Le `#region` direttive e `#endregion` corrispondenti possono avere *pp_message*diverse.

Elaborazione lessicale di un'area:
```csharp
#region
...
#endregion
```
corrisponde esattamente all'elaborazione lessicale di una direttiva di compilazione condizionale nel formato:
```csharp
#if true
...
#endif
```

### <a name="line-directives"></a>Direttive line

Le direttive line possono essere usate per modificare i numeri di riga e i nomi dei file di origine segnalati dal compilatore nell'output, ad esempio avvisi ed errori, e usati dagli attributi delle informazioni sul chiamante ([attributi delle informazioni sul chiamante](attributes.md#caller-info-attributes)).

Le direttive line sono comunemente usate negli strumenti di metaprogrammazione che generano C# codice sorgente da un altro input di testo.

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

Quando non `#line` è presente alcuna direttiva, il compilatore segnala i numeri di riga reali e i nomi dei file di origine nell'output. Quando si elabora `#line` una direttiva che include un *line_indicator* che non `default`lo è, il compilatore considera la riga dopo la direttiva come avente il numero di riga specificato (e il nome del file, se specificato).

Una `#line default` direttiva inverte l'effetto di tutte le direttive #line precedenti. Il compilatore segnala le informazioni sulla riga reale per le righe successive, esattamente `#line` come se non fosse stata elaborata alcuna direttiva.

Una `#line hidden` direttiva non ha alcun effetto sui numeri di riga e file restituiti nei messaggi di errore, ma influisce sul debug a livello di origine. Quando si esegue il debug, tutte `#line hidden` le righe tra una `#line` direttiva e la direttiva successiva `#line hidden`, ovvero not, non dispongono di informazioni sul numero di riga. Quando si esegue il codice istruzione per istruzione nel debugger, queste righe verranno ignorate interamente.

Si noti che un *file_name* differisce da un valore letterale stringa normale in quanto i caratteri di escape non vengono elaborati. il carattere`\`"" indica semplicemente un carattere barra rovesciata comune all'interno di un *file_name*.

### <a name="pragma-directives"></a>Pragma (direttive)

La `#pragma` direttiva di pre-elaborazione viene utilizzata per specificare informazioni contestuali facoltative per il compilatore. Le informazioni fornite in una `#pragma` direttiva non modificano mai la semantica del programma.

```antlr
pp_pragma
    : whitespace? '#' whitespace? 'pragma' whitespace pragma_body pp_new_line
    ;

pragma_body
    : pragma_warning_body
    ;
```

C#fornisce `#pragma` direttive per controllare gli avvisi del compilatore. Le versioni future della lingua possono includere direttive aggiuntive `#pragma` . Per garantire l'interoperabilità C# con altri compilatori C# , il compilatore Microsoft non rilascia errori di `#pragma` compilazione per le direttive sconosciute. tali direttive, tuttavia, generano avvisi.

#### <a name="pragma-warning"></a>avviso pragma

La `#pragma warning` direttiva viene utilizzata per disabilitare o ripristinare tutto o un particolare set di messaggi di avviso durante la compilazione del testo del programma successivo.

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

Una `#pragma warning` direttiva che omette l'elenco di avvisi influiscono su tutti gli avvisi. Una `#pragma warning` direttiva che include un elenco di avvisi influiscono solo sugli avvisi specificati nell'elenco.

Una `#pragma warning disable` direttiva Disabilita tutto o il set di avvisi specificato.

Una `#pragma warning restore` direttiva Ripristina tutto o il set di avvisi specificato nello stato attivo all'inizio dell'unità di compilazione. Si noti che se un particolare avviso è stato disabilitato esternamente, un `#pragma warning restore` (indipendentemente dal fatto che sia o l'avviso specifico) non riabilita tale avviso.

Nell'esempio seguente viene illustrato l' `#pragma warning` utilizzo di per disabilitare temporaneamente l'avviso segnalato quando viene fatto riferimento a membri obsoleti, utilizzando il numero di C# avviso del compilatore Microsoft.
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
