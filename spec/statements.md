---
ms.openlocfilehash: 8f9551b9e7f70379836c23a60f0d37dc02f8e18e
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229702"
---
# <a name="statements"></a>Istruzioni

C# offre un'ampia gamma di istruzioni. La maggior parte di queste istruzioni saranno nota agli sviluppatori che conoscono in C e C++.

```antlr
statement
    : labeled_statement
    | declaration_statement
    | embedded_statement
    ;

embedded_statement
    : block
    | empty_statement
    | expression_statement
    | selection_statement
    | iteration_statement
    | jump_statement
    | try_statement
    | checked_statement
    | unchecked_statement
    | lock_statement
    | using_statement
    | yield_statement
    | embedded_statement_unsafe
    ;
```

Il *embedded_statement* non terminale viene usato per le istruzioni presenti in altre istruzioni. L'uso di *embedded_statement* invece *istruzione* esclude l'uso di istruzioni di dichiarazione e istruzioni con etichetta in questi contesti. L'esempio
```csharp
void F(bool b) {
    if (b)
        int i = 44;
}
```
Genera un errore in fase di compilazione perché un `if` istruzione richiede un *embedded_statement* anziché un *istruzione* per relativo se ramo. Se questo fosse consentito codice, quindi la variabile `i` sarebbe dichiarata, ma non è stato mai utilizzato. Si noti tuttavia che, inserendo `i`della dichiarazione in un blocco, l'esempio è valido.

## <a name="end-points-and-reachability"></a>Endpoint e raggiungibilità

Ogni istruzione è un' ***punto di fine***. In pratica, il punto finale di un'istruzione è la posizione immediatamente successiva l'istruzione. Le regole di esecuzione delle istruzioni composte (istruzioni che contengono istruzioni incorporate) specificano l'azione da eseguita quando il controllo raggiunge il punto finale di un'istruzione incorporata. Ad esempio, quando il controllo raggiunge il punto finale di un'istruzione in un blocco, il controllo viene trasferito all'istruzione successiva nel blocco.

Se un'istruzione che può essere raggiunta in esecuzione, l'istruzione viene detto ***raggiungibile***. Per contro, se non è possibile che venga eseguita un'istruzione, l'istruzione è detta ***raggiungibile***.

Nell'esempio
```csharp
void F() {
    Console.WriteLine("reachable");
    goto Label;
    Console.WriteLine("unreachable");
    Label:
    Console.WriteLine("reachable");
}
```
la seconda chiamata di `Console.WriteLine` non è raggiungibile perché non è possibile che l'istruzione viene eseguita.

Un avviso viene segnalato se il compilatore determina che un'istruzione non è raggiungibile. Si tratta in particolare non un errore per un'istruzione sia raggiungibile.

Per determinare se una determinata istruzione o il punto finale è raggiungibile, il compilatore esegue l'analisi di flusso in base alle regole raggiungibilità definiti per ogni istruzione. L'analisi di flusso prende in considerazione i valori delle espressioni costanti ([espressioni costanti](expressions.md#constant-expressions)) che controllano il comportamento delle istruzioni, ma i valori possibili di espressioni non costanti non vengono considerati. In altre parole, per scopi di analisi di flusso di controllo, un'espressione non costante di un determinato tipo viene considerata per avere qualsiasi valore di quel tipo.

Nell'esempio
```csharp
void F() {
    const int i = 1;
    if (i == 2) Console.WriteLine("unreachable");
}
```
l'espressione booleana del `if` istruzione è un'espressione costante perché entrambi gli operandi del `==` operatore sono costanti. Poiché l'espressione costante viene valutata in fase di compilazione, che restituisce il valore `false`, il `Console.WriteLine` chiamata viene considerata non raggiungibile. Tuttavia, se `i` viene modificato per essere una variabile locale
```csharp
void F() {
    int i = 1;
    if (i == 2) Console.WriteLine("reachable");
}
```
il `Console.WriteLine` chiamata viene considerata raggiungibile, anche se, in realtà non verrà mai eseguito.

Il *blocco* di una funzione membro viene sempre considerato raggiungibile. Per la valutazione in successione la raggiungibilità delle regole di ogni istruzione in un blocco, è possibile determinare la raggiungibilità di qualsiasi istruzione specificata.

Nell'esempio
```csharp
void F(int x) {
    Console.WriteLine("start");
    if (x < 0) Console.WriteLine("negative");
}
```
il problema relativo alla raggiungibilità del secondo `Console.WriteLine` viene determinata come segue:

*  Il primo `Console.WriteLine` istruzione di espressione perché il blocco del `F` metodo sia raggiungibile.
*  Il punto finale del primo `Console.WriteLine` istruzione di espressione è raggiungibile perché tale istruzione è raggiungibile.
*  Il `if` istruzione non è raggiungibile perché il punto di fine del primo `Console.WriteLine` istruzione di espressione è raggiungibile.
*  La seconda `Console.WriteLine` istruzione di espressione perché l'espressione booleana del `if` istruzione non ha il valore costante `false`.

Esistono due casi in cui è un errore in fase di compilazione per il punto finale di un'istruzione sia raggiungibile:

*  Poiché il `switch` istruzione non consente una sezione di switch "giungano" alla sezione switch successiva, è un errore in fase di compilazione per il punto di fine dell'elenco di istruzioni di una sezione di switch sia raggiungibile. Se si verifica questo errore, è in genere un valore che indica che un `break` istruzione non è presente.
*  È un errore in fase di compilazione per il punto finale del blocco di un membro di funzione che calcola un valore di essere raggiungibile. Se si verifica questo errore, in genere è un valore che indica che un `return` istruzione non è presente.

## <a name="blocks"></a>Blocchi

Un *blocco* consente di scrivere più istruzioni nei contesti in cui ne è consentita una sola.

```antlr
block
    : '{' statement_list? '}'
    ;
```

Oggetto *block* costituito facoltativo *statement_list* ([istruzione Elenca](statements.md#statement-lists)), racchiuso tra parentesi graffe. Se l'elenco di istruzioni viene omesso, si dice che il blocco può essere vuoto.

Un blocco può contenere istruzioni di dichiarazione ([istruzioni di dichiarazione](statements.md#declaration-statements)). L'ambito di una costante o variabile locale dichiarata in un blocco è il blocco.

Un blocco viene eseguito come segue:

*  Se il blocco è vuoto, il controllo viene trasferito al punto di fine del blocco.
*  Se il blocco non è vuoto, il controllo viene trasferito all'elenco di istruzioni. Se e quando il controllo raggiunge il punto di fine dell'elenco di istruzioni, il controllo viene trasferito al punto di fine del blocco.

Nell'elenco di istruzioni di un blocco è raggiungibile se il blocco stesso è raggiungibile.

Se il blocco è vuoto o se il punto di fine dell'elenco di istruzioni è raggiungibile, il punto finale di un blocco è raggiungibile.

Oggetto *block* che contiene uno o più `yield` istruzioni ([l'istruzione yield](statements.md#the-yield-statement)) viene chiamato un blocco iteratore. Blocchi di iteratori vengono usati per implementare i membri di funzione come gli iteratori ([iteratori](classes.md#iterators)). Alcune restrizioni aggiuntive si applicano ai blocchi di iteratore:

*  Tratta di un errore in fase di compilazione per un `return` istruzione venga visualizzato in un blocco iteratore (ma `yield return` istruzioni sono permesse).
*  Tratta di un errore in fase di compilazione per un blocco iteratore contenga un contesto non sicuro ([contesti non sicuri](unsafe-code.md#unsafe-contexts)). Un blocco iteratore sempre definisce un contesto di sicuro, anche quando la relativa dichiarazione è annidata in un contesto unsafe.

### <a name="statement-lists"></a>Elenchi di istruzioni

Oggetto ***elenco di istruzioni*** è costituito da uno o più istruzioni scritte nella sequenza. Gli elenchi di istruzioni si verificano nel *blocco*s ([blocchi](statements.md#blocks)) e in *switch_block*s ([istruzione switch](statements.md#the-switch-statement)).

```antlr
statement_list
    : statement+
    ;
```

Viene eseguito un elenco di istruzioni di trasferimento del controllo alla prima istruzione. Se e quando il controllo raggiunge il punto finale di un'istruzione, il controllo viene trasferito all'istruzione successiva. Se e quando il controllo raggiunge il punto finale dell'ultima istruzione, il controllo viene trasferito al punto di fine dell'elenco di istruzioni.

Un'istruzione in un elenco di istruzioni è raggiungibile solo se viene soddisfatta almeno una delle operazioni seguenti:

*  L'istruzione è la prima istruzione e l'elenco di istruzioni è raggiungibile.
*  Il punto finale dell'istruzione precedente è raggiungibile.
*  L'istruzione è un'istruzione con etichetta e l'etichetta fa riferimento un raggiungibile `goto` istruzione.

Se il punto finale dell'ultima istruzione nell'elenco è raggiungibile, il punto finale di un elenco di istruzioni è raggiungibile.

## <a name="the-empty-statement"></a>Istruzione vuota

Un' *empty_statement* non esegue alcuna operazione.

```antlr
empty_statement
    : ';'
    ;
```

Un'istruzione vuota viene utilizzata quando non sono presenti operazioni da eseguire in un contesto in cui è richiesta un'istruzione.

Esecuzione di un'istruzione vuota trasferisce semplicemente il controllo al punto di fine dell'istruzione. Di conseguenza, il punto finale di un'istruzione vuota è raggiungibile se l'istruzione vuota è raggiungibile.

Un'istruzione vuota può essere utilizzata durante la scrittura di un `while` istruzione con un corpo null:
```csharp
bool ProcessMessage() {...}

void ProcessMessages() {
    while (ProcessMessage())
        ;
}
```

Inoltre, un'istruzione vuota è utilizzabile per dichiarare un'etichetta subito prima del tag "`}`" di un blocco:
```csharp
void F() {
    ...
    if (done) goto exit;
    ...
    exit: ;
}
```

## <a name="labeled-statements"></a>Istruzioni con etichetta

Oggetto *labeled_statement* consente un'istruzione può essere preceduta da un'etichetta. Istruzioni con etichetta sono consentite in blocchi, ma non sono consentite come istruzioni incorporate.

```antlr
labeled_statement
    : identifier ':' statement
    ;
```

Un'istruzione con etichetta dichiara un'etichetta con il nome specificato per il *identificatore*. L'ambito di un'etichetta è l'intero blocco in cui è dichiarata l'etichetta, inclusi eventuali blocchi annidati. È un errore in fase di compilazione per due etichette con il nome stesso in modo che gli ambiti sovrapposti.

Un'etichetta è possibile fare riferimento dal `goto` istruzioni ([istruzione goto](statements.md#the-goto-statement)) all'interno dell'ambito dell'etichetta. Ciò significa che `goto` istruzioni possono trasferire il controllo all'interno di blocchi e da blocchi, ma mai in blocchi.

Le etichette sono proprio spazio di dichiarazione e non interferiscono con altri identificatori. L'esempio
```csharp
int F(int x) {
    if (x >= 0) goto x;
    x = -x;
    x: return x;
}
```
è valido e viene utilizzato il nome `x` come parametro e un'etichetta.

Esecuzione di un'istruzione con etichetta corrisponde esattamente all'esecuzione dell'istruzione che segue l'etichetta.

Oltre la raggiungibilità fornita da normale flusso di controllo, un'istruzione con etichetta è raggiungibile solo se l'etichetta fa riferimento un raggiungibile `goto` istruzione. (Eccezione: Se un `goto` istruzione si trova all'interno una `try` che include un `finally` blocco e l'istruzione con etichetta non è compreso il `try`e il punto finale del `finally` blocco non è raggiungibile, quindi l'istruzione con etichetta non è raggiungibile da che `goto` istruzione.)

## <a name="declaration-statements"></a>Istruzioni di dichiarazione

Oggetto *declaration_statement* dichiara una variabile locale o una costante. Le istruzioni di dichiarazione sono consentite in blocchi, ma non sono consentite come istruzioni incorporate.

```antlr
declaration_statement
    : local_variable_declaration ';'
    | local_constant_declaration ';'
    ;
```

### <a name="local-variable-declarations"></a>Dichiarazioni di variabili locali

Oggetto *local_variable_declaration* dichiara uno o più variabili locali.

```antlr
local_variable_declaration
    : local_variable_type local_variable_declarators
    ;

local_variable_type
    : type
    | 'var'
    ;

local_variable_declarators
    : local_variable_declarator
    | local_variable_declarators ',' local_variable_declarator
    ;

local_variable_declarator
    : identifier
    | identifier '=' local_variable_initializer
    ;

local_variable_initializer
    : expression
    | array_initializer
    | local_variable_initializer_unsafe
    ;
```

Il *local_variable_type* di un *local_variable_declaration* direttamente specifica il tipo delle variabili introdotte dalla dichiarazione, o con l'identificatore indica `var` che il tipo può essere presupposta basato su un inizializzatore. Il tipo è seguito da un elenco delle *local_variable_declarator*s, ognuno dei quali viene introdotta una nuova variabile. Oggetto *local_variable_declarator* costituito da un *identifier* che denomina la variabile, seguita facoltativamente da un "`=`" token e un *local_variable_initializer* che specifica il valore iniziale della variabile.

Nel contesto di una dichiarazione di variabile locale, l'identificatore var agisce come una parola chiave contestuale ([parole chiave](lexical-structure.md#keywords)). Quando la *local_variable_type* è specificato come `var` e nessun tipo denominato `var` è incluso nell'ambito, la dichiarazione è un ***tipizzate in modo implicito la dichiarazione di variabile locale***, il cui tipo è dedotto dal tipo dell'espressione dell'inizializzatore associato. Dichiarazioni di variabili locali tipizzate in modo implicito sono soggette alle restrizioni seguenti:

*  Il *local_variable_declaration* non può includere più *local_variable_declarator*s.
*  Il *local_variable_declarator* deve includere un *local_variable_initializer*.
*  Il *local_variable_initializer* deve essere un' *espressione*.
*  L'inizializzatore *espressione* deve presentare un tipo in fase di compilazione.
*  L'inizializzatore *espressione* non può fare riferimento alla variabile dichiarata stesso

Di seguito è riportati esempi di dichiarazioni di variabili locali tipizzate in modo implicito non corrette:

```csharp
var x;               // Error, no initializer to infer type from
var y = {1, 2, 3};   // Error, array initializer not permitted
var z = null;        // Error, null does not have a type
var u = x => x + 1;  // Error, anonymous functions do not have a type
var v = v++;         // Error, initializer cannot refer to variable itself
```

Viene ottenuto il valore di una variabile locale in un'espressione che usa un' *simple_name* ([nomi semplici](expressions.md#simple-names)), e il valore di una variabile locale viene modificato tramite un' *assegnazione* ( [Gli operatori di assegnazione](expressions.md#assignment-operators)). Una variabile locale deve essere assegnata definitivamente ([assegnazione certa](variables.md#definite-assignment)) in ogni posizione in cui viene ottenuto il relativo valore.

L'ambito di una variabile locale dichiarata un *local_variable_declaration* è il blocco in cui si trova la dichiarazione. Si tratta di un errore per fare riferimento a una variabile locale in una posizione di testo che precede il *local_variable_declarator* della variabile locale. Nell'ambito di una variabile locale, è un errore in fase di compilazione per dichiarare local un'altra variabile o costante con lo stesso nome.

Una dichiarazione di variabile locale che dichiara più variabili equivale a più dichiarazioni di variabili singole con lo stesso tipo. Inoltre, un inizializzatore di variabile in una dichiarazione di variabile locale corrisponde esattamente a un'istruzione di assegnazione di inserimento immediatamente dopo la dichiarazione.

L'esempio
```csharp
void F() {
    int x = 1, y, z = x * 2;
}
```
corrisponde esattamente alla
```csharp
void F() {
    int x; x = 1;
    int y;
    int z; z = x * 2;
}
```

In una dichiarazione di variabile locale tipizzata in modo implicito, il tipo di variabile locale dichiarata presuppone che sia uguale al tipo dell'espressione utilizzata per inizializzare la variabile. Ad esempio:
```csharp
var i = 5;
var s = "Hello";
var d = 1.0;
var numbers = new int[] {1, 2, 3};
var orders = new Dictionary<int,Order>();
```

Il tipizzata implicitamente dichiarazioni di variabili locali precedenti equivalgono esattamente alle seguenti dichiarazioni tipizzate in modo esplicito:
```csharp
int i = 5;
string s = "Hello";
double d = 1.0;
int[] numbers = new int[] {1, 2, 3};
Dictionary<int,Order> orders = new Dictionary<int,Order>();
```

### <a name="local-constant-declarations"></a>Dichiarazioni di costante locale

Oggetto *local_constant_declaration* dichiara una o più costanti locali.

```antlr
local_constant_declaration
    : 'const' type constant_declarators
    ;

constant_declarators
    : constant_declarator (',' constant_declarator)*
    ;

constant_declarator
    : identifier '=' constant_expression
    ;
```

Il *tipo* di un *local_constant_declaration* specifica il tipo di costanti introdotte dalla dichiarazione. Il tipo è seguito da un elenco delle *constant_declarator*s, ognuno dei quali viene introdotta una nuova costante. Oggetto *constant_declarator* costituito da un *identificatore* che assegna il nome di costante, seguita da un "`=`" token, seguito da un *constant_expression* ([ Espressioni costanti](expressions.md#constant-expressions)) che specifica il valore della costante.

Il *tipo* e *constant_expression* di una dichiarazione di costante locale deve seguire le stesse regole a quelle di una dichiarazione di membro costante ([costanti](classes.md#constants)).

Viene ottenuto il valore di una costante locale in un'espressione che usa un' *simple_name* ([nomi semplici](expressions.md#simple-names)).

L'ambito di una costante locale è il blocco in cui si trova la dichiarazione. Si tratta di un errore per fare riferimento a una costante locale in una posizione di testo che precede relativi *constant_declarator*. Nell'ambito di una costante locale, è un errore in fase di compilazione per dichiarare local un'altra variabile o costante con lo stesso nome.

Una dichiarazione di costante locale che dichiara più costanti equivale a più dichiarazioni di singole costanti con lo stesso tipo.

## <a name="expression-statements"></a>Istruzioni di espressione

Un' *expression_statement* valuta un'espressione specificata. Il valore calcolato dall'espressione, se presente, viene eliminato.

```antlr
expression_statement
    : statement_expression ';'
    ;

statement_expression
    : invocation_expression
    | null_conditional_invocation_expression
    | object_creation_expression
    | assignment
    | post_increment_expression
    | post_decrement_expression
    | pre_increment_expression
    | pre_decrement_expression
    | await_expression
    ;
```

Non tutte le espressioni sono consentite come istruzioni. In particolare, espressioni, ad esempio `x + y` e `x == 1` che semplicemente calcolare un valore (che verrà eliminato), non sono consentiti come istruzioni.

Esecuzione di un' *expression_statement* valuta l'espressione indipendente e quindi trasferisce il controllo al punto finale delle *expression_statement*. Il punto finale di un' *expression_statement* raggiungibile se tale *expression_statement* è raggiungibile.

## <a name="selection-statements"></a>Istruzioni di selezione

Istruzioni di selezione selezionare uno dei numerosi possibili istruzioni per l'esecuzione in base al valore di un'espressione.

```antlr
selection_statement
    : if_statement
    | switch_statement
    ;
```

### <a name="the-if-statement"></a>Se l'istruzione

Il `if` istruzione consente di selezionare un'istruzione per l'esecuzione in base al valore di un'espressione booleana.

```antlr
if_statement
    : 'if' '(' boolean_expression ')' embedded_statement
    | 'if' '(' boolean_expression ')' embedded_statement 'else' embedded_statement
    ;
```

Un' `else` part è associata al livello lessicale più vicina precedente `if` quello consentito dalla sintassi. Di conseguenza, un `if` istruzione del form
```csharp
if (x) if (y) F(); else G();
```
equivale a
```csharp
if (x) {
    if (y) {
        F();
    }
    else {
        G();
    }
}
```

Un `if` istruzione viene eseguita come indicato di seguito:

*  Il *boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)) viene valutata.
*  Se l'espressione booleana restituisce `true`, il controllo viene trasferito alla prima istruzione incorporata. Se e quando il controllo raggiunge il punto finale di tale istruzione, il controllo viene trasferito al punto finale del `if` istruzione.
*  Se l'espressione booleana restituisce `false` e, se un `else` parte è presente, il controllo viene trasferito alla seconda istruzione incorporata. Se e quando il controllo raggiunge il punto finale di tale istruzione, il controllo viene trasferito al punto finale del `if` istruzione.
*  Se l'espressione booleana restituisce `false` e, se un' `else` parte non è presente, il controllo viene trasferito al punto finale del `if` istruzione.

La prima istruzione di incorporata un' `if` istruzione è raggiungibile se il `if` istruzione sia raggiungibile e che l'espressione booleana non ha il valore costante `false`.

La seconda istruzione di incorporata un' `if` istruzione, se presente, è raggiungibile se il `if` istruzione sia raggiungibile e che l'espressione booleana non ha il valore costante `true`.

Il punto finale di un `if` istruzione è raggiungibile solo se il punto finale di almeno una delle relative istruzioni incorporate è raggiungibile. Inoltre, come punto alla fine di un' `if` istruzione senza alcun `else` parte è raggiungibile se il `if` istruzione sia raggiungibile e che l'espressione booleana non ha il valore costante `true`.

### <a name="the-switch-statement"></a>L'istruzione switch

L'istruzione switch seleziona per l'esecuzione di un elenco di istruzioni con un'etichetta di commutatore associato che corrisponde al valore dell'espressione switch.

```antlr
switch_statement
    : 'switch' '(' expression ')' switch_block
    ;

switch_block
    : '{' switch_section* '}'
    ;

switch_section
    : switch_label+ statement_list
    ;

switch_label
    : 'case' constant_expression ':'
    | 'default' ':'
    ;
```

Oggetto *switch_statement* costituita dalla parola chiave `switch`, seguita da un'espressione tra parentesi (chiamata espressione switch), seguita da una *switch_block*. Il *switch_block* costituita da zero o più *switch_section*s, racchiuso tra parentesi graffe. Ciascuna *switch_section* costituito da uno o più *switch_label*s seguita da una *statement_list* ([istruzione Elenca](statements.md#statement-lists)).

Il ***che controllano di tipo*** di un `switch` istruzione viene stabilita dall'espressione switch.

*  Se il tipo dell'espressione switch `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `bool`, `char`, `string`, o un *enum_type*, o se è il tipo nullable corrispondente a uno di questi tipi, questo sarà l'oggetto tipo del `switch` istruzione.
*  In caso contrario, uno definito dall'utente la conversione implicita ([conversioni definite dall'utente](conversions.md#user-defined-conversions)) deve esistere dal tipo dell'espressione switch a uno dei seguenti che controllano i tipi possibili: `sbyte`, `byte`, `short` , `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `string`, o un tipo nullable corrispondente a uno di tali tipi.
*  In caso contrario, se non esiste alcuna conversione implicita o se più di una conversione implicita esiste, verrà generato un errore in fase di compilazione.

L'espressione costante della ognuno `case` etichetta deve indicare un valore convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) per il tipo che implementano la governance del `switch` istruzione. Si verifica un errore in fase di compilazione se due o più `case` le etichette nella stessa `switch` istruzione specificare lo stesso valore costante.

Può esistere al massimo un `default` label in un'istruzione switch.

Oggetto `switch` istruzione viene eseguita come indicato di seguito:

*  L'espressione switch viene valutata e convertito nel tipo che implementano la governance.
*  Se una delle costanti specificato in un `case` etichetta nella stessa `switch` istruzione è uguale al valore dell'espressione switch, il controllo viene trasferito all'elenco di istruzioni seguente corrispondente `case` etichetta.
*  Se nessuna costante specificati in `case` le etichette nella stessa `switch` è uguale al valore dell'espressione switch, istruzione e, se un `default` è presente alcuna etichetta, il controllo viene trasferito all'istruzione successiva all'elenco di `default` etichetta.
*  Se nessuna costante specificati in `case` le etichette nella stessa `switch` è uguale al valore dell'espressione switch, istruzione e, se non `default` è presente alcuna etichetta, il controllo viene trasferito al punto finale del `switch` istruzione.

Se il punto di fine dell'elenco di istruzioni di una sezione di switch è raggiungibile, verrà generato un errore in fase di compilazione. Questo è noto come la regola "passaggio non consentito". L'esempio
```csharp
switch (i) {
case 0:
    CaseZero();
    break;
case 1:
    CaseOne();
    break;
default:
    CaseOthers();
    break;
}
```
è valida perché nessuna sezione opzione ha un punto finale raggiungibile. Diversamente da C e C++, l'esecuzione di una sezione di switch non è consentito "giungano" alla sezione switch successiva e l'esempio
```csharp
switch (i) {
case 0:
    CaseZero();
case 1:
    CaseZeroOrOne();
default:
    CaseAny();
}
```
Genera un errore in fase di compilazione. Quando l'esecuzione di una sezione di switch deve essere seguita dall'esecuzione di un'altra sezione di switch, l'impostazione esplicita `goto case` o `goto default` istruzione deve essere utilizzata:
```csharp
switch (i) {
case 0:
    CaseZero();
    goto case 1;
case 1:
    CaseZeroOrOne();
    goto default;
default:
    CaseAny();
    break;
}
```

Più etichette sono consentite in una *switch_section*. L'esempio
```csharp
switch (i) {
case 0:
    CaseZero();
    break;
case 1:
    CaseOne();
    break;
case 2:
default:
    CaseTwo();
    break;
}
```
è valido. L'esempio non violi la regola "passaggio non consentito" perché le etichette `case 2:` e `default:` fanno parte dello stesso *switch_section*.

La regola "passaggio non consentito" impedisce un tipo comune di bug che si verificano in C e C++ quando `break` istruzioni accidentalmente vengono omessi. Inoltre, a causa di questa regola, le sezioni switch di un `switch` istruzione può essere riorganizzata in modo arbitrario senza influenzare il comportamento dell'istruzione. Ad esempio, le sezioni del `switch` istruzione precedente può essere annullata senza influenzare il comportamento dell'istruzione:
```csharp
switch (i) {
default:
    CaseAny();
    break;
case 1:
    CaseZeroOrOne();
    goto default;
case 0:
    CaseZero();
    goto case 1;
}
```

Elenco di istruzioni di una sezione Opzioni in genere termina con un `break`, `goto case`, o `goto default` istruzione, ma qualsiasi costrutto che esegue il rendering Impossibile raggiungere il punto di fine dell'elenco di istruzioni è consentito. Ad esempio, un `while` istruzione controllata dall'espressione booleana `true` è noto che mai raggiungere il punto finale. Analogamente, un `throw` o `return` istruzione sempre trasferisce il controllo in un' posizione e non raggiunge mai il punto finale. Di conseguenza, l'esempio seguente è valido:
```csharp
switch (i) {
case 0:
    while (true) F();
case 1:
    throw new ArgumentException();
case 2:
    return;
}
```

Il tipo che implementano la governance di un' `switch` istruzione potrebbe essere il tipo `string`. Ad esempio:
```csharp
void DoCommand(string command) {
    switch (command.ToLower()) {
    case "run":
        DoRun();
        break;
    case "save":
        DoSave();
        break;
    case "quit":
        DoQuit();
        break;
    default:
        InvalidCommand(command);
        break;
    }
}
```

Ad esempio gli operatori di uguaglianza di stringa ([gli operatori di uguaglianza di stringhe](expressions.md#string-equality-operators)), il `switch` istruzione distinzione maiuscole / minuscole e verrà eseguita una determinata sezione switch solo se la stringa dell'espressione switch corrisponda esattamente un `case` etichetta costante.

Quando l'oggetto tipo di un `switch` istruzione viene `string`, il valore `null` è consentito come una costante di etichetta case.

Il *statement_list*s di un *switch_block* può contenere istruzioni di dichiarazione ([istruzioni di dichiarazione](statements.md#declaration-statements)). L'ambito di una costante o variabile locale dichiarata in un blocco switch è il blocco switch.

Elenco di istruzioni di una determinata sezione switch è raggiungibile se il `switch` istruzione sia raggiungibile e che viene soddisfatta almeno una delle operazioni seguenti:

*  L'espressione switch è un valore non costante.
*  L'espressione switch è un valore costante che corrisponde a un `case` etichetta nella sezione Opzioni.
*  L'espressione switch è un valore costante che non corrisponde ad alcuno `case` etichetta e la sezione switch contiene il `default` etichetta.
*  Un'etichetta switch della sezione switch fa riferimento un raggiungibile `goto case` o `goto default` istruzione.

Il punto finale di un `switch` istruzione sia raggiungibile in presenza di almeno una delle operazioni seguenti:

*  Il `switch` istruzione contiene un raggiungibile `break` istruzione che viene chiuso il `switch` istruzione.
*  Il `switch` istruzione è raggiungibile, l'espressione switch è un valore non costante e nessun `default` è presente alcuna etichetta.
*  Il `switch` istruzione è raggiungibile, l'espressione switch è un valore costante che non corrisponde ad alcuno `case` etichetta e nessun `default` è presente alcuna etichetta.

## <a name="iteration-statements"></a>Istruzioni di iterazione

Istruzioni di iterazione eseguire ripetutamente un'istruzione incorporata.

```antlr
iteration_statement
    : while_statement
    | do_statement
    | for_statement
    | foreach_statement
    ;
```

### <a name="the-while-statement"></a>L'istruzione while

Il `while` istruzione esegue in modo condizionale in un'istruzione incorporata zero o più volte.

```antlr
while_statement
    : 'while' '(' boolean_expression ')' embedded_statement
    ;
```

Oggetto `while` istruzione viene eseguita come indicato di seguito:

*  Il *boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)) viene valutata.
*  Se l'espressione booleana restituisce `true`, il controllo viene trasferito all'istruzione incorporata. Quando e se il controllo raggiunge il punto finale dell'istruzione incorporata (possibilmente dall'esecuzione di un `continue` istruzione), il controllo viene trasferito all'inizio del `while` istruzione.
*  Se l'espressione booleana restituisce `false`, il controllo viene trasferito al punto finale del `while` istruzione.

All'interno dell'istruzione incorporata in un `while` istruzione, un' `break` istruzione ([l'istruzione break](statements.md#the-break-statement)) può essere utilizzato per trasferire il controllo al punto finale del `while` istruzione (in modo da interrompere l'iterazione di incorporato istruzione) e un `continue` istruzione ([l'istruzione continue](statements.md#the-continue-statement)) può essere utilizzato per trasferire il controllo al punto di fine dell'istruzione incorporata (in modo da eseguire un'altra iterazione del `while` istruzione).

L'istruzione incorporata in un `while` istruzione è raggiungibile se il `while` istruzione sia raggiungibile e che l'espressione booleana non ha il valore costante `false`.

Il punto finale di un `while` istruzione sia raggiungibile in presenza di almeno una delle operazioni seguenti:

*  Il `while` istruzione contiene un raggiungibile `break` istruzione che viene chiuso il `while` istruzione.
*  Il `while` istruzione sia raggiungibile e che l'espressione booleana non ha il valore costante `true`.

### <a name="the-do-statement"></a>Istruzione do

Il `do` istruzione esegue in modo condizionale in un'istruzione incorporata una o più volte.

```antlr
do_statement
    : 'do' embedded_statement 'while' '(' boolean_expression ')' ';'
    ;
```

Oggetto `do` istruzione viene eseguita come indicato di seguito:

*  Il controllo viene trasferito all'istruzione incorporata.
*  Quando e se il controllo raggiunge il punto finale dell'istruzione incorporata (possibilmente dall'esecuzione di un `continue` istruzione), il *boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)) viene valutata. Se l'espressione booleana restituisce `true`, il controllo viene trasferito all'inizio del `do` istruzione. In caso contrario, il controllo viene trasferito al punto finale del `do` istruzione.

All'interno dell'istruzione incorporata in un `do` istruzione, un' `break` istruzione ([l'istruzione break](statements.md#the-break-statement)) può essere utilizzato per trasferire il controllo al punto finale del `do` istruzione (in modo da interrompere l'iterazione di incorporato istruzione) e un `continue` istruzione ([l'istruzione continue](statements.md#the-continue-statement)) può essere utilizzato per trasferire il controllo al punto di fine dell'istruzione incorporata.

L'istruzione incorporata in un `do` istruzione è raggiungibile se il `do` istruzione sia raggiungibile.

Il punto finale di un `do` istruzione sia raggiungibile in presenza di almeno una delle operazioni seguenti:

*  Il `do` istruzione contiene un raggiungibile `break` istruzione che viene chiuso il `do` istruzione.
*  Il punto finale dell'istruzione incorporata sia raggiungibile e che l'espressione booleana non ha il valore costante `true`.

### <a name="the-for-statement"></a>L'istruzione for

Il `for` istruzione restituisce una sequenza di espressioni di inizializzazione e quindi, mentre una condizione è true, ripetutamente esegue un'istruzione incorporata e restituisce una sequenza di espressioni di iterazione.

```antlr
for_statement
    : 'for' '(' for_initializer? ';' for_condition? ';' for_iterator? ')' embedded_statement
    ;

for_initializer
    : local_variable_declaration
    | statement_expression_list
    ;

for_condition
    : boolean_expression
    ;

for_iterator
    : statement_expression_list
    ;

statement_expression_list
    : statement_expression (',' statement_expression)*
    ;
```

Il *for_initializer*, se presente, è costituita da un *local_variable_declaration* ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)) o un elenco di *statement_ espressione*s ([istruzioni di espressione](statements.md#expression-statements)) separati da virgole. L'ambito di una variabile locale dichiarata da un *for_initializer* inizia in corrispondenza la *local_variable_declarator* per la variabile e si estende fino alla fine dell'istruzione incorporata. L'ambito include il *for_condition* e il *for_iterator*.

Il *for_condition*, se presente, deve essere un *boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)).

Il *for_iterator*, se presente, è costituito da un elenco delle *statement_expression*s ([istruzioni di espressione](statements.md#expression-statements)) separati da virgole.

Oggetto per l'istruzione viene eseguito come indicato di seguito:

*  Se un *for_initializer* presente, gli inizializzatori di variabili o espressioni di istruzioni vengono eseguite nell'ordine in cui sono scritti. Questo passaggio viene eseguito solo una volta.
*  Se un *for_condition* è presente, viene valutata.
*  Se il *for_condition* non è presente o se il risultato della valutazione `true`, il controllo viene trasferito all'istruzione incorporata. Quando e se il controllo raggiunge il punto finale dell'istruzione incorporata (anche in seguito all'esecuzione di un `continue` istruzione), le espressioni del *for_iterator*, se presente, vengono valutati in sequenza, e quindi un'altra iterazione eseguito, inizia con la valutazione del *for_condition* nel passaggio precedente.
*  Se il *for_condition* sia presente e la versione di valutazione produce `false`, il controllo viene trasferito al punto finale del `for` istruzione.

All'interno dell'istruzione incorporata in un `for` istruzione, un' `break` istruzione ([l'istruzione break](statements.md#the-break-statement)) può essere utilizzato per trasferire il controllo al punto finale del `for` istruzione (in modo da interrompere l'iterazione di incorporato istruzione) e un `continue` istruzione ([l'istruzione continue](statements.md#the-continue-statement)) può essere utilizzato per trasferire il controllo al punto di fine dell'istruzione incorporata (in modo da eseguire il *for_iterator* e esecuzione di un'altra iterazione del `for` iniziale dell'istruzione, con il *for_condition*).

L'istruzione incorporata in un `for` istruzione è raggiungibile solo se viene soddisfatta una delle operazioni seguenti:

*  Il `for` istruzione sia raggiungibile e nessun *for_condition* è presente.
*  Il `for` istruzione sia raggiungibile e una *for_condition* sia presente e non ha il valore costante `false`.

Il punto finale di un `for` istruzione sia raggiungibile in presenza di almeno una delle operazioni seguenti:

*  Il `for` istruzione contiene un raggiungibile `break` istruzione che viene chiuso il `for` istruzione.
*  Il `for` istruzione sia raggiungibile e una *for_condition* sia presente e non ha il valore costante `true`.

### <a name="the-foreach-statement"></a>L'istruzione foreach

Il `foreach` istruzione enumera gli elementi di una raccolta, l'esecuzione di un'istruzione incorporata per ogni elemento della raccolta.

```antlr
foreach_statement
    : 'foreach' '(' local_variable_type identifier 'in' expression ')' embedded_statement
    ;
```

Il *tipo* e *identifier* di un `foreach` istruzione dichiara il ***variabile di iterazione*** dell'istruzione. Se il `var` identificatore viene specificato come il *local_variable_type*e nessun tipo denominato `var` è incluso nell'ambito, viene definita la variabile di iterazione un' ***variabile di iterazione tipizzate in modo implicito***, e presuppone che sia il tipo di elemento del relativo tipo di `foreach` istruzione, come specificato di seguito. La variabile di iterazione corrisponde a una variabile locale di sola lettura con un ambito esteso tramite l'istruzione incorporata. Durante l'esecuzione di un `foreach` istruzione, la variabile di iterazione rappresenta l'elemento della raccolta per il quale viene eseguita un'iterazione. Si verifica un errore in fase di compilazione se l'istruzione incorporata tenta di modificare la variabile di iterazione (tramite l'assegnazione o la `++` e `--` operatori) oppure passare la variabile di iterazione come una `ref` o `out` parametro.

Nell'esempio seguente, per brevità, `IEnumerable`, `IEnumerator`, `IEnumerable<T>` e `IEnumerator<T>` fanno riferimento ai tipi corrispondenti negli spazi dei nomi `System.Collections` e `System.Collections.Generic`.

L'elaborazione in fase di compilazione di un'istruzione foreach determina innanzitutto le ***tipo di raccolta***, ***tipo di enumeratore*** e ***tipo di elemento*** dell'espressione. Questa operazione avviene nel modo seguente:

*  Se il tipo `X` dei *espressione* è un tipo di matrice non vi è una conversione implicita del riferimento da `X` per il `IEnumerable` interfaccia (poiché `System.Array` implementa questa interfaccia). Il ***tipo di raccolta*** è la `IEnumerable` interfaccia, il ***tipo di enumeratore*** è il `IEnumerator` interfaccia e il ***tipo di elemento*** è il tipo di elemento del tipo di matrice `X`.
*  Se il tipo `X` di *espressione* viene `dynamic` non vi è una conversione implicita da *espressione* per il `IEnumerable` interfaccia ([dinamico implicita le conversioni](conversions.md#implicit-dynamic-conversions)). Il ***tipo di raccolta*** è la `IEnumerable` interfaccia e il ***tipo di enumeratore*** è il `IEnumerator` interfaccia. Se il `var` identificatore viene specificato come il *local_variable_type* il ***tipo di elemento*** è `dynamic`, in caso contrario è `object`.
*  In caso contrario, determinare se il tipo `X` dispone di un oggetto appropriato `GetEnumerator` metodo:
   * Eseguire la ricerca dei membri del tipo `X` con identificatore `GetEnumerator` e nessun argomento di tipo. Se la ricerca di membri non produce una corrispondenza, o che produce un'ambiguità, o produce una corrispondenza che non è un gruppo di metodi, controllare per un'interfaccia enumerabile, come descritto di seguito. È consigliabile che verrà generato un avviso se la ricerca di membri produce qualsiasi ad eccezione di un gruppo di metodi o alcuna corrispondenza.
   * Eseguire la risoluzione dell'overload con il gruppo di metodi risultante e un elenco di argomenti vuoto. Se la risoluzione dell'overload determina in alcun metodo applicabile, un'ambiguità o risultati in un singolo metodo migliore, ma tale metodo è statico o non pubblico, cercare un'interfaccia enumerabile come descritto di seguito. È consigliabile che verrà generato un avviso se la risoluzione dell'overload produce qualsiasi ad eccezione di un metodo di istanza pubblici non ambigue o alcun metodo applicabile.
   * Se il tipo restituito `E` del `GetEnumerator` (metodo) non è una classe, viene prodotto il tipo di struct o interfaccia, un errore e viene eseguita alcuna operazione ulteriormente.
   * Ricerca di membri viene eseguita su `E` con l'identificatore `Current` e nessun argomento di tipo. Se il membro individuata alcuna corrispondenza, il risultato è un errore o il risultato è diverso da una proprietà di istanza pubblici che consente la lettura, viene generato un errore e viene eseguita alcuna operazione ulteriormente.
   * Ricerca di membri viene eseguita su `E` con l'identificatore `MoveNext` e nessun argomento di tipo. Se il membro individuata alcuna corrispondenza, il risultato è un errore o il risultato è diverso da un gruppo di metodi, viene generato un errore e viene eseguita alcuna operazione ulteriormente.
   * Risoluzione dell'overload viene eseguita sul gruppo di metodi con un elenco di argomenti vuoto. Se i risultati di risoluzione dell'overload in alcun metodo applicabile, i risultati in un'ambiguità o i risultati in un metodo migliore, ma tale metodo è statico o non pubblico, o il tipo restituito non `bool`, viene generato un errore e viene eseguita alcuna operazione ulteriormente.
   * Il ***tipo di raccolta*** è `X`, il ***tipo di enumeratore*** è `E`e il ***tipo dell'elemento*** è il tipo del `Current` proprietà.

*  In caso contrario, cercare un'interfaccia enumerabile:
   * Se tra tutti i tipi `Ti` per cui non esiste una conversione implicita da `X` a `IEnumerable<Ti>`, è presente un tipo univoco `T` modo tale che `T` non è `dynamic` e per tutti gli altri `Ti` è presente un la conversione implicita da `IEnumerable<T>` al `IEnumerable<Ti>`, il ***tipo di raccolta*** è l'interfaccia `IEnumerable<T>`, la ***tipo di enumeratore*** è l'interfaccia `IEnumerator<T>`e il ***tipo di elemento*** è `T`.
   * In caso contrario, se è presente più di un tale tipo `T`, viene generato un errore e viene eseguita alcuna operazione ulteriormente.
   * In caso contrario, se è presente una conversione implicita da `X` per il `System.Collections.IEnumerable` interfaccia, il ***tipo di raccolta*** è questa interfaccia, il ***tipo di enumeratore*** è l'interfaccia `System.Collections.IEnumerator`e il ***tipo di elemento*** è `object`.
   * In caso contrario, viene generato un errore e viene eseguita alcuna operazione ulteriormente.

I passaggi precedenti, se ha esito positivo, in modo non ambiguo producono un tipo di raccolta `C`, tipo di enumeratore `E` e il tipo di elemento `T`. Un'istruzione foreach del form
```csharp
foreach (V v in x) embedded_statement
```
viene quindi espansa per:
```csharp
{
    E e = ((C)(x)).GetEnumerator();
    try {
        while (e.MoveNext()) {
            V v = (V)(T)e.Current;
            embedded_statement
        }
    }
    finally {
        ... // Dispose e
    }
}
```

La variabile `e` non è visibile e accessibile all'espressione `x` o l'istruzione incorporata o qualsiasi altro codice sorgente del programma. La variabile `v` è di sola lettura in un'istruzione incorporata. Se non esiste una conversione esplicita ([conversioni esplicite](conversions.md#explicit-conversions)) da `T` (tipo di elemento) per `V` (la *local_variable_type* nell'istruzione foreach), viene generato un errore e non altri passaggi da eseguire. Se `x` ha il valore `null`, un `System.NullReferenceException` viene generata un'eccezione in fase di esecuzione.

Un'implementazione può implementare una determinata istruzione foreach in modo diverso, ad esempio, per motivi di prestazioni, purché il comportamento è coerente con l'espansione del precedente.

Il posizionamento dei `v` all'interno di while è importante per la modalità viene acquisito da qualsiasi funzione anonima che si verificano nel ciclo il *embedded_statement*.

Ad esempio:
```csharp
int[] values = { 7, 9, 13 };
Action f = null;

foreach (var value in values)
{
    if (f == null) f = () => Console.WriteLine("First value: " + value);
}

f();
```
Se `v` è stato dichiarato all'esterno di while loop, possono essere condivisi tra tutte le iterazioni e il relativo valore dopo il per ciclo sarebbe il valore finale, `13`, ovvero che la chiamata di `f` stamperebbe. Al contrario, poiché ogni iterazione ha il proprio variabile `v`, quella acquisita dal `f` nella prima iterazione continuerà a contenere il valore `7`, che è ciò che sarà stampato. (Nota: le versioni precedenti di C# dichiarato `v` ciclo esterno di while.)

Il corpo del blocco finally viene costruito seguendo i passaggi seguenti:

*  Se è presente una conversione implicita da `E` per il `System.IDisposable` interfaccia, quindi
   *  Se `E` è un tipo di valore non nullable la clausola finally viene espansa sull'equivalente della semantica:

      ```csharp
      finally {
          ((System.IDisposable)e).Dispose();
      }
      ```

   *  In caso contrario, la clausola finally viene espansa sull'equivalente della semantica:

      ```csharp
      finally {
          if (e != null) ((System.IDisposable)e).Dispose();
      }
      ```

   Tuttavia se `E` è un tipo di valore o creare un'istanza di un tipo di valore, quindi il cast di un parametro di tipo `e` a `System.IDisposable` non causerà la conversione boxing si verifichi.

*  In caso contrario, se `E` è un tipo sealed, la clausola finally viene espansa in un blocco vuoto:

   ```csharp
   finally {
   }
   ```

*  In caso contrario, la clausola finally viene espansa per:

   ```csharp
   finally {
       System.IDisposable d = e as System.IDisposable;
       if (d != null) d.Dispose();
   }
   ```    

   La variabile locale `d` non è visibile e accessibile a qualsiasi codice utente. In particolare, non è in conflitto con qualsiasi altra variabile il cui ambito include il blocco finally.

L'ordine in cui `foreach` scorre gli elementi di una matrice, è il seguente: Per gli elementi di matrici unidimensionali vengono attraversati in ordine di indice crescente, partire dall'indice `0` fino all'indice `Length - 1`. Per le matrici multidimensionali, gli elementi vengono scorsi in modo che gli indici della dimensione più a destra vengono prima un aumento, quindi la successiva dimensione a sinistra, e così via a sinistra.

Ciascun valore in una matrice bidimensionale, in ordine degli elementi viene visualizzato nell'esempio seguente:
```csharp
using System;

class Test
{
    static void Main() {
        double[,] values = {
            {1.2, 2.3, 3.4, 4.5},
            {5.6, 6.7, 7.8, 8.9}
        };

        foreach (double elementValue in values)
            Console.Write("{0} ", elementValue);

        Console.WriteLine();
    }
}
```
Come indicato di seguito è riportato l'output prodotto:
```csharp
1.2 2.3 3.4 4.5 5.6 6.7 7.8 8.9
```

Nell'esempio
```csharp
int[] numbers = { 1, 3, 5, 7, 9 };
foreach (var n in numbers) Console.WriteLine(n);
```
il tipo della `n` viene dedotto come `int`, il tipo di elemento di `numbers`.

## <a name="jump-statements"></a>Istruzioni di salto

Istruzioni di salto trasferisce incondizionatamente il controllo.

```antlr
jump_statement
    : break_statement
    | continue_statement
    | goto_statement
    | return_statement
    | throw_statement
    ;
```

Il percorso in cui un'istruzione jump trasferisce il controllo viene chiamato il ***destinazione*** dell'istruzione di spostamento.

Quando un'istruzione di salto si verifica all'interno di un blocco e la destinazione dell'istruzione jump è esterna al blocco, l'istruzione di salto viene detto ***uscire*** il blocco. Mentre un'istruzione di salto può trasferire il controllo all'esterno di un blocco, è non possibile trasferire il controllo mai in un blocco.

Esecuzione di istruzioni di salto è ulteriormente complicata dalla presenza di intermedi `try` istruzioni. In assenza di tali `try` (istruzioni), un'istruzione jump trasferisce il controllo dell'istruzione di salto per il valore di destinazione. In presenza di questo tipo di protezione `try` (istruzioni), l'esecuzione risulta più complessa. Se l'istruzione di salto termina uno o più `try` con i blocchi associati `finally` blocchi di controllo viene trasferito inizialmente per il `finally` blocco di più interna `try` istruzione. Quando e se il controllo raggiunge il punto finale di un `finally` blocco di controllo viene trasferito al `finally` blocco di inclusione successivo `try` istruzione. Questo processo viene ripetuto finché il `finally` blocchi di tutti interessati `try` istruzioni sono state eseguite.

Nell'esempio
```csharp
using System;

class Test
{
    static void Main() {
        while (true) {
            try {
                try {
                    Console.WriteLine("Before break");
                    break;
                }
                finally {
                    Console.WriteLine("Innermost finally block");
                }
            }
            finally {
                Console.WriteLine("Outermost finally block");
            }
        }
        Console.WriteLine("After break");
    }
}
```
il `finally` associati a due blocchi `try` istruzioni vengono eseguite prima che il controllo viene trasferito nella destinazione dell'istruzione di spostamento.

Come indicato di seguito è riportato l'output prodotto:
```
Before break
Innermost finally block
Outermost finally block
After break
```

### <a name="the-break-statement"></a>L'istruzione break

Il `break` istruzione abbandona inclusione più vicina `switch`, `while`, `do`, `for`, o `foreach` istruzione.

```antlr
break_statement
    : 'break' ';'
    ;
```

La destinazione di una `break` istruzione è il punto finale di inclusione più vicina `switch`, `while`, `do`, `for`, o `foreach` istruzione. Se un `break` istruzione non è racchiuso da un `switch`, `while`, `do`, `for`, o `foreach` istruzione, verrà generato un errore in fase di compilazione.

Quando più `switch`, `while`, `do`, `for`, o `foreach` istruzioni sono annidate all'interno di altro, un `break` informativa si applica solo all'istruzione più interno. Per trasferire il controllo con più livelli di nidificazione, un `goto` istruzione ([istruzione goto](statements.md#the-goto-statement)) deve essere utilizzato.

Oggetto `break` non può uscire dall'istruzione un' `finally` blocco ([l'istruzione try](statements.md#the-try-statement)). Quando un `break` istruzione si trova all'interno di un `finally` block, la destinazione delle `break` istruzione deve essere all'interno della stessa `finally` bloccare; in caso contrario, si verifica un errore in fase di compilazione.

Oggetto `break` istruzione viene eseguita come indicato di seguito:

*  Se il `break` istruzione esce da uno o più `try` blocchi con associati `finally` blocchi di controllo viene trasferito inizialmente al `finally` blocco di più interna `try` istruzione. Quando e se il controllo raggiunge il punto finale di un `finally` blocco di controllo viene trasferito al `finally` blocco di inclusione successivo `try` istruzione. Questo processo viene ripetuto finché il `finally` blocchi di tutti interessati `try` istruzioni sono state eseguite.
*  Il controllo viene trasferito alla destinazione del `break` istruzione.

Poiché un `break` istruzione in modo incondizionato trasferisce il controllo in un' posizione, il punto finale di un `break` istruzione non è raggiungibile.

### <a name="the-continue-statement"></a>L'istruzione continue

Il `continue` istruzione avvia una nuova iterazione di inclusione più vicina `while`, `do`, `for`, o `foreach` istruzione.

```antlr
continue_statement
    : 'continue' ';'
    ;
```

La destinazione di una `continue` istruzione è il punto finale dell'ultima istruzione di inclusione più vicina `while`, `do`, `for`, o `foreach` istruzione. Se un `continue` istruzione non è racchiuso da un `while`, `do`, `for`, o `foreach` istruzione, verrà generato un errore in fase di compilazione.

Quando più `while`, `do`, `for`, o `foreach` istruzioni sono annidate all'interno di altro, un `continue` informativa si applica solo all'istruzione più interno. Per trasferire il controllo con più livelli di nidificazione, un `goto` istruzione ([istruzione goto](statements.md#the-goto-statement)) deve essere utilizzato.

Oggetto `continue` non può uscire dall'istruzione un' `finally` blocco ([l'istruzione try](statements.md#the-try-statement)). Quando un `continue` istruzione si trova all'interno di un `finally` block, la destinazione delle `continue` istruzione deve essere all'interno della stessa `finally` bloccare; in caso contrario, verrà generato un errore in fase di compilazione.

Oggetto `continue` istruzione viene eseguita come indicato di seguito:

*  Se il `continue` istruzione esce da uno o più `try` blocchi con associati `finally` blocchi di controllo viene trasferito inizialmente al `finally` blocco di più interna `try` istruzione. Quando e se il controllo raggiunge il punto finale di un `finally` blocco di controllo viene trasferito al `finally` blocco di inclusione successivo `try` istruzione. Questo processo viene ripetuto finché il `finally` blocchi di tutti interessati `try` istruzioni sono state eseguite.
*  Il controllo viene trasferito alla destinazione del `continue` istruzione.

Poiché un `continue` istruzione in modo incondizionato trasferisce il controllo in un' posizione, il punto finale di un `continue` istruzione non è raggiungibile.

### <a name="the-goto-statement"></a>Istruzione GoTo

Il `goto` istruzione trasferisce il controllo a un'istruzione che è contrassegnata da un'etichetta.

```antlr
goto_statement
    : 'goto' identifier ';'
    | 'goto' 'case' constant_expression ';'
    | 'goto' 'default' ';'
    ;
```

La destinazione di una `goto` *identificatore* istruzione è l'istruzione con etichetta con l'etichetta specificata. Se un'etichetta con il nome specificato non esiste nel membro della funzione corrente o se il `goto` istruzione non è all'interno dell'ambito dell'etichetta, si verifica un errore in fase di compilazione. Questa regola consente di utilizzare un `goto` istruzione per trasferire il controllo all'esterno di un ambito annidato, ma non in un ambito annidato. Nell'esempio
```csharp
using System;

class Test
{
    static void Main(string[] args) {
        string[,] table = {
            {"Red", "Blue", "Green"},
            {"Monday", "Wednesday", "Friday"}
        };

        foreach (string str in args) {
            int row, colm;
            for (row = 0; row <= 1; ++row)
                for (colm = 0; colm <= 2; ++colm)
                    if (str == table[row,colm])
                         goto done;

            Console.WriteLine("{0} not found", str);
            continue;
    done:
            Console.WriteLine("Found {0} at [{1}][{2}]", str, row, colm);
        }
    }
}
```
un `goto` istruzione viene usata per trasferire il controllo all'esterno di un ambito annidato.

La destinazione di una `goto case` istruzione è l'elenco di istruzioni in immediatamente che li racchiude `switch` istruzione ([l'istruzione switch](statements.md#the-switch-statement)), che contiene un `case` etichetta con il valore costante specificato. Se il `goto case` istruzione non si trova a un `switch` istruzione, se il *constant_expression* non è implicitamente convertibile ([conversioni implicite](conversions.md#implicit-conversions)) per il tipo che implementano la governance del più vicino che li racchiude `switch` istruzione, oppure se l'inclusione più vicina `switch` istruzione non contiene un `case` assegnare un'etichetta con il valore costante specificato, verrà generato un errore in fase di compilazione.

La destinazione di una `goto default` istruzione è l'elenco di istruzioni in immediatamente che li racchiude `switch` istruzione ([l'istruzione switch](statements.md#the-switch-statement)), che contiene un `default` etichetta. Se il `goto default` istruzione non è racchiuso un `switch` istruzione, oppure se inclusione più vicina `switch` istruzione non contiene un `default` assegnare un'etichetta, si verifica un errore in fase di compilazione.

Oggetto `goto` non può uscire dall'istruzione un' `finally` blocco ([l'istruzione try](statements.md#the-try-statement)). Quando un `goto` istruzione si trova all'interno di un `finally` block, la destinazione delle `goto` istruzione deve essere all'interno della stessa `finally` blocco, o in caso contrario, verrà generato un errore in fase di compilazione.

Oggetto `goto` istruzione viene eseguita come indicato di seguito:

*  Se il `goto` istruzione esce da uno o più `try` blocchi con associati `finally` blocchi di controllo viene trasferito inizialmente al `finally` blocco di più interna `try` istruzione. Quando e se il controllo raggiunge il punto finale di un `finally` blocco di controllo viene trasferito al `finally` blocco di inclusione successivo `try` istruzione. Questo processo viene ripetuto finché il `finally` blocchi di tutti interessati `try` istruzioni sono state eseguite.
*  Il controllo viene trasferito alla destinazione del `goto` istruzione.

Poiché un `goto` istruzione in modo incondizionato trasferisce il controllo in un' posizione, il punto finale di un `goto` istruzione non è raggiungibile.

### <a name="the-return-statement"></a>L'istruzione return

Il `return` istruzione restituisce il controllo al chiamante corrente della funzione in cui il `return` viene visualizzata l'istruzione.

```antlr
return_statement
    : 'return' expression? ';'
    ;
```

Oggetto `return` istruzione senza un'espressione può essere utilizzata solo in un membro di funzione che calcola un valore, vale a dire, un metodo con tipo di risultato ([corpo del metodo](classes.md#method-body)) `void`, il `set` della funzione di accesso di una proprietà o un indicizzatore, il `add` e `remove` funzioni di accesso di un evento, un costruttore di istanza, un costruttore statico o un distruttore.

Oggetto `return` istruzione con un'espressione può essere usata solo in un membro di funzione che calcola un valore, vale a dire, un metodo con un tipo di risultato diverso da void, il `get` funzione di accesso di una proprietà o indicizzatore o un operatore definito dall'utente. Una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) deve esistere dal tipo dell'espressione nel tipo restituito del membro di funzione che lo contiene.

Restituire può anche essere usate all'interno delle espressioni di funzioni anonime ([espressioni di funzioni anonime](expressions.md#anonymous-function-expressions)) e partecipare a determinare quali conversioni esistono per tali funzioni.

Tratta di un errore in fase di compilazione per un `return` istruzione venga visualizzato un `finally` blocco ([l'istruzione try](statements.md#the-try-statement)).

Oggetto `return` istruzione viene eseguita come indicato di seguito:

*  Se il `return` istruzione specifica un'espressione, l'espressione viene valutata e il valore risultante viene convertito nel tipo restituito della funzione contenitore da parte di una conversione implicita. Il risultato della conversione diventa il valore del risultato prodotto dalla funzione.
*  Se il `return` istruzione è racchiuso da uno o più `try` o `catch` con i blocchi associati `finally` blocchi di controllo viene trasferito inizialmente al `finally` blocco di più interna `try` istruzione. Quando e se il controllo raggiunge il punto finale di un `finally` blocco di controllo viene trasferito al `finally` blocco di inclusione successivo `try` istruzione. Questo processo viene ripetuto finché il `finally` blocchi di inclusione tutti `try` istruzioni sono state eseguite.
*  Se la funzione che lo contiene non è una funzione asincrona, il controllo viene restituito al chiamante della funzione contenitore insieme al valore di risultato, se presente.
*  Se la funzione che lo contiene è una funzione asincrona, il controllo venga restituito al chiamante corrente e il valore del risultato, se presente, viene registrato nell'attività restituito come descritto in ([enumeratore (interfacce)](classes.md#enumerator-interfaces)).

Poiché un `return` istruzione in modo incondizionato trasferisce il controllo in un' posizione, il punto finale di un `return` istruzione non è raggiungibile.

### <a name="the-throw-statement"></a>L'istruzione throw

Il `throw` istruzione genera un'eccezione.

```antlr
throw_statement
    : 'throw' expression? ';'
    ;
```

Oggetto `throw` istruzione con un'espressione genera il valore restituito dalla valutazione dell'espressione. L'espressione deve indicare un valore del tipo di classe `System.Exception`, di un tipo di classe che deriva da `System.Exception` o di un tipo di parametro di tipo con `System.Exception` (o una sottoclasse relativa) come classe di base effettivo. Se la valutazione dell'espressione produce `null`, un `System.NullReferenceException` invece generata.

Oggetto `throw` istruzione senza un'espressione può essere utilizzata solo in un `catch` block, nel qual caso tale istruzione genera nuovamente l'eccezione che viene attualmente gestito da tale `catch` blocco.

Poiché un `throw` istruzione in modo incondizionato trasferisce il controllo in un' posizione, il punto finale di un `throw` istruzione non è raggiungibile.

Quando viene generata un'eccezione, il controllo viene trasferito al primo `catch` clausola in un'inclusione `try` informativa in grado di gestire l'eccezione. Il processo che viene eseguita dal punto dell'eccezione generata per il punto di trasferimento del controllo in un gestore di eccezioni appropriato è noto come ***propagazione delle eccezioni***. La propagazione di un'eccezione è costituito da valutare più volte i passaggi seguenti solo un `catch` clausola che corrisponde all'eccezione viene trovato. In questa descrizione, il ***throw punto*** iniziale corrisponde alla posizione in corrispondenza del quale viene generata l'eccezione.

*  Nel membro della funzione corrente, ogni `try` viene esaminato l'istruzione che include il punto di generazione. Istruzione for each `S`, che inizia con più interna `try` istruzione iniziale e finendo con più esterna `try` istruzione, vengono valutati i passaggi seguenti:

   * Se il `try` blocco `S` racchiude il punto di generazione e S contiene uno o più `catch` clausole, il `catch` clausole vengono esaminate nell'ordine di visualizzazione per individuare un gestore appropriato per l'eccezione, in base alle regole specificate Sezione [l'istruzione try](statements.md#the-try-statement). Se un oggetto corrispondente `catch` clausola si trova, la propagazione delle eccezioni viene completata per il trasferimento del controllo al blocco di cui `catch` clausola.

   * In caso contrario, se il `try` blocco o un `catch` blocco `S` include il punto di generazione e, se `S` ha una `finally` blocco di controllo viene trasferito al `finally` blocco. Se il `finally` blocco Genera eccezione un altro, interrompere l'elaborazione dell'eccezione corrente. In caso contrario, quando il controllo raggiunge il punto finale del `finally` blocco, continua l'elaborazione dell'eccezione corrente.

*  Se un gestore di eccezioni non è accessibile dalla chiamata alla funzione corrente, la chiamata alla funzione viene terminata e si verifica una delle operazioni seguenti:

   * Se la funzione corrente è non asincrone, i passaggi precedenti vengono ripetuti per il chiamante della funzione con un punto di generazione corrispondente all'istruzione da cui è stato richiamato il membro di funzione.

   * Se la funzione corrente è asincrono e restituzione di task, l'eccezione viene registrata nell'attività restituito, che viene inserito in uno stato faulted o cancelled come descritto in [enumeratore (interfacce)](classes.md#enumerator-interfaces).

   * Se la funzione corrente è asincrono e che restituisce void, il contesto di sincronizzazione del thread corrente è una notifica come descritto in [interfacce enumerabili](classes.md#enumerable-interfaces).

*  Se l'elaborazione delle eccezioni termina tutte le chiamate al membro di funzione nel thread corrente, che indica che il thread non ha alcun gestore per l'eccezione, quindi il thread stesso verrà terminato. L'impatto di tale azione è definito dall'implementazione.

## <a name="the-try-statement"></a>L'istruzione try

Il `try` istruzione fornisce un meccanismo per intercettare le eccezioni che si verificano durante l'esecuzione di un blocco. Inoltre, il `try` istruzione offre la possibilità di specificare un blocco di codice che viene sempre eseguito quando il controllo lascia il `try` istruzione.

```antlr
try_statement
    : 'try' block catch_clause+
    | 'try' block finally_clause
    | 'try' block catch_clause+ finally_clause
    ;

catch_clause
    : 'catch' exception_specifier? exception_filter?  block
    ;

exception_specifier
    : '(' type identifier? ')'
    ;

exception_filter
    : 'when' '(' expression ')'
    ;

finally_clause
    : 'finally' block
    ;
```

Esistono tre forme possibili di `try` istruzioni:

*  Oggetto `try` blocco seguito da uno o più `catch` blocchi.
*  Oggetto `try` blocco seguito da un `finally` blocco.
*  Oggetto `try` blocco seguito da uno o più `catch` blocchi seguita da un `finally` blocco.

Quando un `catch` clausola consente di specificare un *exception_specifier*, il tipo deve essere `System.Exception`, un tipo che deriva da `System.Exception` o un tipo di parametro di tipo che ha `System.Exception` (o una sottoclasse relativa) come relativi effettiva classe di base.

Quando un `catch` clausola specifica sia un' *exception_specifier* con un *identificatore*, un ***variabile dell'eccezione*** del nome specificato e il tipo è dichiarato. La variabile dell'eccezione corrisponde a una variabile locale con un ambito che ricopre il `catch` clausola. Durante l'esecuzione del *exception_filter* e *blocco*, la variabile dell'eccezione rappresenta l'eccezione attualmente gestita. Ai fini di controllo dell'assegnazione definita, la variabile dell'eccezione viene considerata assegnata nel relativo ambito.

A meno che un `catch` clausola include un nome di variabile di eccezione, non è possibile accedere all'oggetto eccezione nel filtro e `catch` blocco.

Oggetto `catch` clausola che non specifica un' *exception_specifier* viene chiamato un generale `catch` clausola.

Alcuni linguaggi di programmazione possono supportare le eccezioni che non sono rappresentabili come un oggetto derivato da `System.Exception`, anche se tali eccezioni non possono mai essere generate dal codice C#. Generale `catch` clausola può essere usata per intercettare tali eccezioni. Di conseguenza, una general `catch` clausola è semanticamente diversa da quella più specifica il tipo `System.Exception`, in quanto nel primo caso può inoltre rilevare eccezioni da altri linguaggi.

Per individuare un gestore per un'eccezione, `catch` clausole vengono esaminate in ordine lessicale. Se un `catch` clausola specifica un tipo, ma nessun filtro eccezioni, è un errore in fase di compilazione per una versione successiva `catch` clausola nella stessa `try` istruzione per specificare un tipo che equivale a o deriva da, indipendente dai tipi. Se un `catch` clausola specifica alcun tipo e alcun filtro, deve essere l'ultimo `catch` clausola tale `try` istruzione.

All'interno di un `catch` blocco, una `throw` istruzione ([l'istruzione throw](statements.md#the-throw-statement)) senza un'espressione è utilizzabile per generare nuovamente l'eccezione che ha rilevato il `catch` blocco. Le assegnazioni a una variabile dell'eccezione non alterano l'eccezione che viene generata nuovamente.

Nell'esempio
```csharp
using System;

class Test
{
    static void F() {
        try {
            G();
        }
        catch (Exception e) {
            Console.WriteLine("Exception in F: " + e.Message);
            e = new Exception("F");
            throw;                // re-throw
        }
    }

    static void G() {
        throw new Exception("G");
    }

    static void Main() {
        try {
            F();
        }
        catch (Exception e) {
            Console.WriteLine("Exception in Main: " + e.Message);
        }
    }
}
```
il metodo `F` rileva un'eccezione, scrive alcune informazioni di diagnostica nella console, consente di modificare la variabile dell'eccezione e genera nuovamente l'eccezione. L'eccezione che viene generato nuovamente è l'eccezione originale, in modo che l'output del programma:
```
Exception in F: G
Exception in Main: G
```

Se non fosse generata il primo blocco catch `e` invece di generare nuovamente l'eccezione corrente, l'output ottenuto sarà il seguente:
```csharp
Exception in F: G
Exception in Main: F
```

Tratta di un errore in fase di compilazione per un `break`, `continue`, o `goto` istruzione per trasferire il controllo fuori un `finally` blocco. Quando un `break`, `continue`, o `goto` istruzione si trova un `finally` blocco, la destinazione dell'istruzione devono essere all'interno della stessa `finally` blocco, o in caso contrario, verrà generato un errore in fase di compilazione.

Tratta di un errore in fase di compilazione per un `return` istruzione all'interno un `finally` blocco.

Oggetto `try` istruzione viene eseguita come indicato di seguito:

*  Per il controllo viene trasferito il `try` blocco.
*  Quando e se il controllo raggiunge il punto finale del `try` blocco:
   *  Se il `try` istruzione presenta un `finally` blocco, il `finally` blocco viene eseguito.
   *  Il controllo viene trasferito al punto finale del `try` istruzione.

*  Se un'eccezione viene propagata per il `try` durante l'esecuzione dell'istruzione il `try` blocco:
   *  Il `catch` clausole, se presenti, vengono esaminate nell'ordine di visualizzazione per individuare un gestore appropriato per l'eccezione. Se un `catch` clausola non specifica un tipo o specifica il tipo di eccezione o un tipo di base del tipo di eccezione:
      *  Se il `catch` clausola dichiara una variabile di eccezione, l'oggetto eccezione viene assegnato alla variabile dell'eccezione.
      *  Se il `catch` clausola dichiara un filtro eccezioni, il filtro viene valutato. Se viene restituito `false`, la clausola catch non è una corrispondenza e la ricerca continua tramite qualsiasi successiva `catch` clausole per un gestore appropriato.
      *  In caso contrario, il `catch` clausola viene considerata una corrispondenza e il controllo viene trasferito sul corrispondente `catch` blocco.
      *  Quando e se il controllo raggiunge il punto finale del `catch` blocco:
         * Se il `try` istruzione presenta un `finally` blocco, il `finally` blocco viene eseguito.
         * Il controllo viene trasferito al punto finale del `try` istruzione.
      *  Se un'eccezione viene propagata per il `try` durante l'esecuzione dell'istruzione il `catch` blocco:
         *  Se il `try` istruzione presenta un `finally` blocco, il `finally` blocco viene eseguito.
         *  L'eccezione viene propagata alla successiva inclusione `try` istruzione.
   *  Se il `try` istruzione non ha alcun `catch` clausole o se nessun `catch` clausola corrisponde all'eccezione:
      *  Se il `try` istruzione presenta un `finally` blocco, il `finally` blocco viene eseguito.
      *  L'eccezione viene propagata alla successiva inclusione `try` istruzione.

Le istruzioni di una `finally` blocco vengono sempre eseguiti quando il controllo lascia un `try` istruzione. Ciò è vero se il trasferimento del controllo viene eseguita come risultato dell'esecuzione normale, in seguito all'esecuzione una `break`, `continue`, `goto`, o `return` istruzione, o come risultato la propagazione di un'eccezione del `try` istruzione.

Se viene generata un'eccezione durante l'esecuzione di un `finally` blocca e non rilevata all'interno della stessa blocco finally, l'eccezione viene propagata alla successiva inclusione `try` istruzione. Se un'altra eccezione era in corso la propagazione, tale eccezione viene persa. Il processo di propagazione di un'eccezione viene trattato ulteriormente nella descrizione del `throw` istruzione ([l'istruzione throw](statements.md#the-throw-statement)).

Il `try` block di una `try` istruzione è raggiungibile se il `try` istruzione sia raggiungibile.

Oggetto `catch` blocco di un `try` istruzione è raggiungibile se il `try` istruzione sia raggiungibile.

Il `finally` block di una `try` istruzione è raggiungibile se il `try` istruzione sia raggiungibile.

Il punto finale di un `try` istruzione è raggiungibile solo se entrambe le operazioni seguenti sono vere:

*  Il punto finale del `try` blocco è raggiungibile o punto alla fine di almeno un `catch` blocco sia raggiungibile.
*  Se un `finally` blocco è presente, il punto finale del `finally` blocco sia raggiungibile.

## <a name="the-checked-and-unchecked-statements"></a>Le istruzioni checked e unchecked

Il `checked` e `unchecked` istruzioni usate per controllare le ***contesto di controllo dell'overflow*** per le conversioni e operazioni aritmetiche di tipo integrale.

```antlr
checked_statement
    : 'checked' block
    ;

unchecked_statement
    : 'unchecked' block
    ;
```

Il `checked` istruzione fa sì che tutte le espressioni nel *blocco* deve essere valutata in un contesto controllato e il `unchecked` istruzione fa sì che tutte le espressioni nel *blocco* deve essere valutata un contesto non verificato.

Il `checked` e `unchecked` istruzioni rappresentano l'equivalente per il `checked` e `unchecked` operatori ([gli operatori checked e unchecked](expressions.md#the-checked-and-unchecked-operators)), ad eccezione del fatto che operano in base a blocchi anziché le espressioni .

## <a name="the-lock-statement"></a>Istruzione lock

Il `lock` istruzione Ottiene il blocco a esclusione reciproca per un determinato oggetto, esegue un'istruzione e quindi rilascia il blocco.

```antlr
lock_statement
    : 'lock' '(' expression ')' embedded_statement
    ;
```

L'espressione di un `lock` istruzione deve indicare un valore di un tipo noto per essere un *reference_type*. Nessuna conversione boxing implicito ([conversioni Boxing](conversions.md#boxing-conversions)) non viene mai eseguita per l'espressione di un `lock` istruzione e, pertanto è un errore in fase di compilazione per l'espressione indicare un valore di un *value_type*.

Oggetto `lock` istruzione del form
```csharp
lock (x) ...
```
in cui `x` è un'espressione di un *reference_type*, equivale a
```csharp
bool __lockWasTaken = false;
try {
    System.Threading.Monitor.Enter(x, ref __lockWasTaken);
    ...
}
finally {
    if (__lockWasTaken) System.Threading.Monitor.Exit(x);
}
```
con la differenza che `x` viene valutato una sola volta.

Mentre è attivo un blocco a esclusione reciproca, l'esecuzione di codice nello stesso thread di esecuzione nonché ottenere e rilasciare il blocco. Tuttavia, l'esecuzione di codice in altri thread viene impedito ottenendo il blocco fino a quando il blocco viene rilasciato.

Blocco `System.Type` oggetti per sincronizzare l'accesso ai dati statici non è consigliata. Impedire l'altro codice sullo stesso tipo, che può causare deadlock. Un approccio migliore consiste per sincronizzare l'accesso ai dati statici il blocco di un oggetto statico privato. Ad esempio:
```csharp
class Cache
{
    private static readonly object synchronizationObject = new object();

    public static void Add(object x) {
        lock (Cache.synchronizationObject) {
            ...
        }
    }

    public static void Remove(object x) {
        lock (Cache.synchronizationObject) {
            ...
        }
    }
}
```

## <a name="the-using-statement"></a>Istruzione using

Il `using` istruzione Ottiene uno o più risorse, esegue un'istruzione e quindi elimina la risorsa.

```antlr
using_statement
    : 'using' '(' resource_acquisition ')' embedded_statement
    ;

resource_acquisition
    : local_variable_declaration
    | expression
    ;
```

Oggetto ***resource*** è una classe o struct che implementa `System.IDisposable`, che include un singolo metodo senza parametri denominato `Dispose`. Il codice che usa una risorsa può chiamare `Dispose` per indicare che la risorsa non è più necessario. Se `Dispose` non viene chiamato, quindi eliminazione automatica si verifica alla fine di conseguenza la garbage collection.

Se la forma di *resource_acquisition* viene *local_variable_declaration* quindi il tipo del *local_variable_declaration* deve essere `dynamic` o un tipo che può essere convertito in modo implicito nel `System.IDisposable`. Se la forma di *resource_acquisition* viene *expression* questa espressione deve essere convertibile in modo implicito in `System.IDisposable`.

Le variabili locali dichiarate un *resource_acquisition* sono di sola lettura e deve includere un inizializzatore. Si verifica un errore in fase di compilazione se l'istruzione incorporata tenta di modificare queste variabili locali (tramite l'assegnazione o la `++` e `--` operatori), accettare l'indirizzo di loro o passarli come `ref` o `out` parametri.

Oggetto `using` istruzione viene convertita in tre parti: acquisizione, utilizzo ed eliminazione. Uso della risorsa è incluso in modo implicito in un `try` istruzione che include un `finally` clausola. Ciò `finally` determina l'eliminazione della risorsa. Se un `null` risorsa acquisita, quindi Nessuna chiamata a `Dispose` viene effettuata, e viene generata alcuna eccezione. Se la risorsa è di tipo `dynamic` viene convertito in modo dinamico mediante una conversione implicita dinamica ([conversioni implicite dinamiche](conversions.md#implicit-dynamic-conversions)) a `IDisposable` durante l'acquisizione per garantire che la conversione è esito positivo, prima dell'utilizzo e l'eliminazione.

Oggetto `using` istruzione del form
```csharp
using (ResourceType resource = expression) statement
```
corrisponde a uno dei tre possibili espansioni. Quando si `ResourceType` è un tipo di valore non nullable, è l'espansione
```csharp
{
    ResourceType resource = expression;
    try {
        statement;
    }
    finally {
        ((IDisposable)resource).Dispose();
    }
}
```

In caso contrario, quando `ResourceType` è un tipo valore nullable o un tipo riferimento diverso da `dynamic`, l'espansione è
```csharp
{
    ResourceType resource = expression;
    try {
        statement;
    }
    finally {
        if (resource != null) ((IDisposable)resource).Dispose();
    }
}
```

In caso contrario, quando `ResourceType` è `dynamic`, l'espansione è
```csharp
{
    ResourceType resource = expression;
    IDisposable d = (IDisposable)resource;
    try {
        statement;
    }
    finally {
        if (d != null) d.Dispose();
    }
}
```

In entrambe le espansioni, la `resource` variabile è di sola lettura in un'istruzione incorporata e il `d` variabile è inaccessibile e invisibile, l'istruzione incorporata.

Un'implementazione può implementare una determinata istruzione utilizzando in modo diverso, ad esempio, per motivi di prestazioni, purché il comportamento è coerente con l'espansione del precedente.

Oggetto `using` istruzione del form
```csharp
using (expression) statement
```
dispone di tre possibili espansioni stesso. In questo caso `ResourceType` in modo implicito è il tipo di fase di compilazione di `expression`, se presente. In caso contrario, l'interfaccia `IDisposable` stesso viene utilizzato come il `ResourceType`. Il `resource` variabile è inaccessibile e invisibile, l'istruzione incorporata.

Quando un *resource_acquisition* assume la forma di un *local_variable_declaration*, è possibile acquisire più risorse di un determinato tipo. Oggetto `using` istruzione del form
```csharp
using (ResourceType r1 = e1, r2 = e2, ..., rN = eN) statement
```
è esattamente equivalente a una sequenza di annidata `using` istruzioni:
```csharp
using (ResourceType r1 = e1)
    using (ResourceType r2 = e2)
        ...
            using (ResourceType rN = eN)
                statement
```

L'esempio seguente crea un file denominato `log.txt` e scrive due righe di testo nel file. Nell'esempio viene quindi apre uno stesso file per la lettura e copia le righe di testo contenute nella console.
```csharp
using System;
using System.IO;

class Test
{
    static void Main() {
        using (TextWriter w = File.CreateText("log.txt")) {
            w.WriteLine("This is line one");
            w.WriteLine("This is line two");
        }

        using (TextReader r = File.OpenText("log.txt")) {
            string s;
            while ((s = r.ReadLine()) != null) {
                Console.WriteLine(s);
            }

        }
    }
}
```

Poiché il `TextWriter` e `TextReader` sono classi che implementano le `IDisposable` interfaccia, è possibile usare l'esempio `using` istruzioni per assicurarsi che il file sottostante venga chiusa correttamente seguendo la scrittura o operazioni di lettura.

## <a name="the-yield-statement"></a>L'istruzione yield

Il `yield` istruzione viene utilizzata in un blocco iteratore ([blocchi](statements.md#blocks)) per produrre un valore all'oggetto enumeratore ([oggetti dell'enumeratore](classes.md#enumerator-objects)) o un oggetto enumerabile ([degli oggetti enumerabili](classes.md#enumerable-objects)) un iteratore o per segnalare la fine dell'iterazione.

```antlr
yield_statement
    : 'yield' 'return' expression ';'
    | 'yield' 'break' ';'
    ;
```

`yield` non è una parola riservata. ha un significato speciale solo quando utilizzata prima un `return` o `break` (parola chiave). In altri contesti, `yield` può essere usato come identificatore.

Esistono diverse restrizioni relative a dove un `yield` istruzione può essere specificata, come descritto di seguito.

*  Tratta di un errore in fase di compilazione per un `yield` istruzione (di qualsiasi forma) all'esterno di un *method_body*, *operator_body* o *accessor_body*
*  Tratta di un errore in fase di compilazione per un `yield` istruzione (di qualsiasi forma) all'interno di una funzione anonima.
*  Tratta di un errore in fase di compilazione per un `yield` istruzione (di qualsiasi forma) venga visualizzato nel `finally` clausola di un `try` istruzione.
*  Tratta di un errore in fase di compilazione per un `yield return` trovarsi in qualsiasi punto dell'istruzione una `try` istruzione che contiene gli eventuali `catch` clausole.

L'esempio seguente mostra alcuni usi validi e non validi di `yield` istruzioni.

```csharp
delegate IEnumerable<int> D();

IEnumerator<int> GetEnumerator() {
    try {
        yield return 1;        // Ok
        yield break;           // Ok
    }
    finally {
        yield return 2;        // Error, yield in finally
        yield break;           // Error, yield in finally
    }

    try {
        yield return 3;        // Error, yield return in try...catch
        yield break;           // Ok
    }
    catch {
        yield return 4;        // Error, yield return in try...catch
        yield break;           // Ok
    }

    D d = delegate { 
        yield return 5;        // Error, yield in an anonymous function
    }; 
}

int MyMethod() {
    yield return 1;            // Error, wrong return type for an iterator block
}
```

Una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) deve esistere dal tipo dell'espressione nella `yield return` istruzione per il tipo di yield ([Yield tipo](classes.md#yield-type)) dell'iteratore.

Oggetto `yield return` istruzione viene eseguita come indicato di seguito:

*  L'espressione specificata nell'istruzione viene valutata in modo implicito convertito nel tipo di yield e assegnato al `Current` proprietà dell'oggetto enumeratore.
*  L'esecuzione del blocco iteratore viene sospesa. Se il `yield return` istruzione è all'interno di uno o più `try` blocca, associato `finally` blocchi non vengono eseguiti in questo momento.
*  Il `MoveNext` metodo dell'oggetto enumeratore restituisce `true` al relativo chiamante, che indica che l'oggetto enumeratore viene spostato sull'elemento successivo.

La chiamata successiva dell'oggetto enumeratore `MoveNext` metodo riprende l'esecuzione del blocco di iteratore da cui è stato sospeso.

Oggetto `yield break` istruzione viene eseguita come indicato di seguito:

*  Se il `yield break` istruzione è racchiuso da uno o più `try` blocchi con associati `finally` blocchi di controllo viene trasferito inizialmente al `finally` blocco di più interna `try` istruzione. Quando e se il controllo raggiunge il punto finale di un `finally` blocco di controllo viene trasferito al `finally` blocco di inclusione successivo `try` istruzione. Questo processo viene ripetuto finché il `finally` blocchi di inclusione tutti `try` istruzioni sono state eseguite.
*  Il controllo viene restituito al chiamante del blocco iteratore. Valori possibili sono i `MoveNext` metodo o `Dispose` metodo dell'oggetto enumeratore.

Poiché un `yield break` istruzione in modo incondizionato trasferisce il controllo in un' posizione, il punto finale di un `yield break` istruzione non è raggiungibile.
