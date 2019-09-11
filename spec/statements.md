---
ms.openlocfilehash: 94346034a667ad4af26796c0c4bbc96d6ed79aba
ms.sourcegitcommit: 7f7fc6e9e195e51b7ff8229aeaa70aa9fbbb63cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876833"
---
# <a name="statements"></a>Istruzioni

C#fornisce una serie di istruzioni. La maggior parte di queste istruzioni sarà familiare per gli sviluppatori che hanno programmato in C++C e.

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

Il *embedded_statement* non terminale viene usato per le istruzioni visualizzate all'interno di altre istruzioni. L'utilizzo di *embedded_statement* anziché di un' *istruzione* esclude l'utilizzo di istruzioni di dichiarazione e istruzioni con etichetta in questi contesti. Esempio
```csharp
void F(bool b) {
    if (b)
        int i = 44;
}
```
genera un errore in fase di compilazione perché un' `if` istruzione richiede un *embedded_statement* anziché un' *istruzione* per il relativo ramo if. Se questo codice è stato consentito, la variabile `i` verrebbe dichiarata, ma non potrebbe mai essere utilizzata. Si noti, tuttavia, che `i`inserendo la dichiarazione in un blocco, l'esempio è valido.

## <a name="end-points-and-reachability"></a>Punti finali e raggiungibilità

Ogni istruzione ha un ***endpoint***. In termini intuitivi, il punto finale di un'istruzione è il percorso immediatamente successivo all'istruzione. Le regole di esecuzione per le istruzioni composite (istruzioni che contengono istruzioni incorporate) specificano l'azione eseguita quando il controllo raggiunge il punto finale di un'istruzione incorporata. Ad esempio, quando il controllo raggiunge il punto finale di un'istruzione in un blocco, il controllo viene trasferito all'istruzione successiva nel blocco.

Se un'istruzione può essere raggiunta dall'esecuzione, l'istruzione è detta ***raggiungibile***. Viceversa, se non è possibile che venga eseguita un'istruzione, l'istruzione è detta non ***raggiungibile***.

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
la seconda chiamata di `Console.WriteLine` non è raggiungibile perché non è possibile che l'istruzione venga eseguita.

Viene segnalato un avviso se il compilatore determina che un'istruzione non è raggiungibile. In particolare, non è un errore perché un'istruzione non sia raggiungibile.

Per determinare se un'istruzione o un endpoint specifico è raggiungibile, il compilatore esegue l'analisi del flusso in base alle regole di raggiungibilità definite per ogni istruzione. L'analisi del flusso prende in considerazione i valori delle espressioni costanti ([espressioni costanti](expressions.md#constant-expressions)) che controllano il comportamento delle istruzioni, ma i valori possibili delle espressioni non costanti non vengono considerati. In altre parole, per finalità di analisi del flusso di controllo, un'espressione non costante di un determinato tipo viene considerata come un possibile valore di quel tipo.

Nell'esempio
```csharp
void F() {
    const int i = 1;
    if (i == 2) Console.WriteLine("unreachable");
}
```
l'espressione booleana dell' `if` istruzione è un'espressione costante perché entrambi gli `==` operandi dell'operatore sono costanti. Poiché l'espressione costante viene valutata in fase di compilazione, producendo `false`il valore `Console.WriteLine` , la chiamata viene considerata irraggiungibile. Tuttavia, se `i` viene modificato come variabile locale
```csharp
void F() {
    int i = 1;
    if (i == 2) Console.WriteLine("reachable");
}
```
la `Console.WriteLine` chiamata è considerata raggiungibile, anche se, in realtà, non verrà mai eseguita.

Il *blocco* di un membro di funzione è sempre considerato raggiungibile. Se successivamente si valutano le regole di raggiungibilità di ogni istruzione in un blocco, è possibile determinare la raggiungibilità di una determinata istruzione.

Nell'esempio
```csharp
void F(int x) {
    Console.WriteLine("start");
    if (x < 0) Console.WriteLine("negative");
}
```
la raggiungibilità della seconda `Console.WriteLine` viene determinata nel modo seguente:

*  La prima `Console.WriteLine` istruzione `F` di espressione è raggiungibile perché il blocco del metodo è raggiungibile.
*  Il punto finale della prima `Console.WriteLine` istruzione di espressione è raggiungibile perché tale istruzione è raggiungibile.
*  L' `if` istruzione è raggiungibile perché il punto finale della prima `Console.WriteLine` istruzione di espressione è raggiungibile.
*  La seconda `Console.WriteLine` istruzione `if` di espressione è raggiungibile perché l'espressione booleana dell'istruzione non ha il valore `false`costante.

Ci sono due situazioni in cui si verifica un errore in fase di compilazione per il raggiungimento del punto finale di un'istruzione:

*  Poiché l' `switch` istruzione non consente a una sezione switch di passare alla sezione switch successiva, si tratta di un errore in fase di compilazione per il punto finale dell'elenco di istruzioni di una sezione di opzioni da raggiungere. Se si verifica questo errore, in genere indica che manca un' `break` istruzione.
*  Si tratta di un errore in fase di compilazione per il punto finale del blocco di un membro di funzione che calcola un valore in modo che sia raggiungibile. Se si verifica questo errore, in genere indica che un' `return` istruzione è mancante.

## <a name="blocks"></a>Blocchi

Un *blocco* consente di scrivere più istruzioni nei contesti in cui ne è consentita una sola.

```antlr
block
    : '{' statement_list? '}'
    ;
```

Un *blocco* è costituito da un *statement_list* facoltativo ([elenchi di istruzioni](statements.md#statement-lists)) racchiuso tra parentesi graffe. Se l'elenco di istruzioni viene omesso, il blocco viene definito vuoto.

Un blocco può contenere istruzioni di dichiarazione ([istruzioni di dichiarazione](statements.md#declaration-statements)). L'ambito di una variabile locale o di una costante dichiarata in un blocco è il blocco.

Un blocco viene eseguito come segue:

*  Se il blocco è vuoto, il controllo viene trasferito al punto finale del blocco.
*  Se il blocco non è vuoto, il controllo viene trasferito all'elenco delle istruzioni. Quando e se il controllo raggiunge il punto finale dell'elenco di istruzioni, il controllo viene trasferito al punto finale del blocco.

L'elenco di istruzioni di un blocco è raggiungibile se il blocco stesso è raggiungibile.

Il punto finale di un blocco è raggiungibile se il blocco è vuoto o se il punto finale dell'elenco di istruzioni è raggiungibile.

Un *blocco* che contiene una o più `yield` istruzioni ([l'istruzione yield](statements.md#the-yield-statement)) viene definito blocco iteratore. I blocchi iteratori vengono usati per implementare i membri della funzione come iteratori ([iteratori](classes.md#iterators)). Per i blocchi iteratori vengono applicate alcune restrizioni aggiuntive:

*  Si tratta di un errore in fase di compilazione `return` perché un'istruzione venga visualizzata in un blocco iteratore (ma `yield return` sono consentite le istruzioni).
*  Si tratta di un errore in fase di compilazione per un blocco iteratore che contiene un contesto non sicuro ([contesti non sicuri](unsafe-code.md#unsafe-contexts)). Un blocco iteratore definisce sempre un contesto sicuro, anche quando la relativa dichiarazione è annidata in un contesto non sicuro.

### <a name="statement-lists"></a>Elenchi di istruzioni

Un ***elenco*** di istruzioni è costituito da una o più istruzioni scritte in sequenza. Gli elenchi di istruzioni si verificano in *Block*s[(blocks) e](statements.md#blocks)in *switch_block*s ([l'istruzione switch](statements.md#the-switch-statement)).

```antlr
statement_list
    : statement+
    ;
```

Un elenco di istruzioni viene eseguito trasferendo il controllo alla prima istruzione. Quando e se il controllo raggiunge il punto finale di un'istruzione, il controllo viene trasferito all'istruzione successiva. Quando e se il controllo raggiunge il punto finale dell'ultima istruzione, il controllo viene trasferito al punto finale dell'elenco di istruzioni.

Un'istruzione in un elenco di istruzioni è raggiungibile se si verifica almeno una delle condizioni seguenti:

*  L'istruzione è la prima istruzione e l'elenco di istruzioni è raggiungibile.
*  Il punto finale dell'istruzione precedente è raggiungibile.
*  L'istruzione è un'istruzione con etichetta e l'etichetta fa riferimento a un'istruzione raggiungibile `goto` .

Il punto finale di un elenco di istruzioni è raggiungibile se il punto finale dell'ultima istruzione nell'elenco è raggiungibile.

## <a name="the-empty-statement"></a>Istruzione vuota

Un *empty_statement* non esegue alcuna operazione.

```antlr
empty_statement
    : ';'
    ;
```

Quando non sono presenti operazioni da eseguire in un contesto in cui è richiesta un'istruzione, viene utilizzata un'istruzione vuota.

L'esecuzione di un'istruzione vuota trasferisce semplicemente il controllo al punto finale dell'istruzione. Pertanto, il punto finale di un'istruzione vuota è raggiungibile se l'istruzione Empty è raggiungibile.

Quando si scrive un' `while` istruzione con un corpo null, è possibile utilizzare un'istruzione vuota:
```csharp
bool ProcessMessage() {...}

void ProcessMessages() {
    while (ProcessMessage())
        ;
}
```

Inoltre, è possibile utilizzare un'istruzione vuota per dichiarare un'etichetta immediatamente prima della chiusura`}`di un blocco:
```csharp
void F() {
    ...
    if (done) goto exit;
    ...
    exit: ;
}
```

## <a name="labeled-statements"></a>Istruzioni con etichetta

Un *labeled_statement* consente a un'istruzione di essere preceduta da un'etichetta. Le istruzioni con etichetta sono consentite in blocchi, ma non sono consentite come istruzioni incorporate.

```antlr
labeled_statement
    : identifier ':' statement
    ;
```

Un'istruzione con etichetta dichiara un'etichetta con il nome specificato dall' *identificatore*. L'ambito di un'etichetta è l'intero blocco in cui è dichiarata l'etichetta, inclusi tutti i blocchi annidati. Si tratta di un errore in fase di compilazione per due etichette con lo stesso nome con ambiti sovrapposti.

È possibile fare riferimento a un'etichetta `goto` dalle istruzioni ([istruzione goto](statements.md#the-goto-statement)) nell'ambito dell'etichetta. Ciò significa che `goto` le istruzioni possono trasferire il controllo all'interno di blocchi e blocchi, ma mai in blocchi.

Le etichette hanno uno spazio di dichiarazione e non interferiscono con altri identificatori. Esempio
```csharp
int F(int x) {
    if (x >= 0) goto x;
    x = -x;
    x: return x;
}
```
è valido e usa il nome `x` sia come parametro che come etichetta.

L'esecuzione di un'istruzione con etichetta corrisponde esattamente all'esecuzione dell'istruzione che segue l'etichetta.

Oltre alla raggiungibilità fornita da un normale flusso di controllo, un'istruzione con etichetta è raggiungibile se un' `goto` istruzione raggiungibile fa riferimento a tale etichetta. Eccezione Se un' `goto` istruzione si trova all' `try` interno di un `finally` oggetto che include un blocco `try`e l'istruzione con etichetta è esterna a e il punto finale `finally` del blocco non è raggiungibile, l'istruzione con etichetta non è raggiungibile da Questa `goto` istruzione).

## <a name="declaration-statements"></a>Istruzioni di dichiarazione

Un *declaration_statement* dichiara una variabile o una costante locale. Le istruzioni di dichiarazione sono consentite in blocchi, ma non sono consentite come istruzioni incorporate.

```antlr
declaration_statement
    : local_variable_declaration ';'
    | local_constant_declaration ';'
    ;
```

### <a name="local-variable-declarations"></a>Dichiarazioni di variabili locali

Un *local_variable_declaration* dichiara una o più variabili locali.

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

Il *local_variable_type* di un *local_variable_declaration* specifica direttamente il tipo delle variabili introdotte dalla dichiarazione o indica con l'identificatore `var` che il tipo deve essere dedotto in base a un inizializzatore. Il tipo è seguito da un elenco di *local_variable_declarator*, ognuno dei quali introduce una nuova variabile. Un *local_variable_declarator* è costituito da un *identificatore* che denomina la variabile, seguito facoltativamente da`=`un token "" e da un *local_variable_initializer* che fornisce il valore iniziale della variabile.

Nel contesto di una dichiarazione di variabile locale, l'identificatore var funge da[parola chiave contestuale (](lexical-structure.md#keywords)Keywords). Quando *local_variable_type* viene specificato come `var` e nessun tipo denominato `var` è nell'ambito, la dichiarazione è una dichiarazione di ***variabile locale tipizzata in modo implicito***, il cui tipo viene dedotto dal tipo dell'inizializzatore associato espressione. Le dichiarazioni di variabili locali tipizzate in modo implicito sono soggette alle restrizioni seguenti:

*  *Local_variable_declaration* non può includere più *local_variable_declarator*.
*  *Local_variable_declarator* deve includere un *local_variable_initializer*.
*  *Local_variable_initializer* deve essere un' *espressione*.
*  L' *espressione* dell'inizializzatore deve avere un tipo in fase di compilazione.
*  L' *espressione* dell'inizializzatore non può fare riferimento alla variabile dichiarata stessa

Di seguito sono riportati alcuni esempi di dichiarazioni di variabili locali non corrette tipizzate in modo implicito:

```csharp
var x;               // Error, no initializer to infer type from
var y = {1, 2, 3};   // Error, array initializer not permitted
var z = null;        // Error, null does not have a type
var u = x => x + 1;  // Error, anonymous functions do not have a type
var v = v++;         // Error, initializer cannot refer to variable itself
```

Il valore di una variabile locale viene ottenuto in un'espressione usando un *simple_name* ([nomi semplici](expressions.md#simple-names)) e il valore di una variabile locale viene modificato usando un' *assegnazione* ([operatori di assegnazione](expressions.md#assignment-operators)). Una variabile locale deve essere assegnata definitivamente ([assegnazione definita](variables.md#definite-assignment)) in ogni posizione in cui viene ottenuto il relativo valore.

L'ambito di una variabile locale dichiarata in un *local_variable_declaration* è il blocco in cui si verifica la dichiarazione. Si tratta di un errore per fare riferimento a una variabile locale in una posizione testuale che precede *local_variable_declarator* della variabile locale. Nell'ambito di una variabile locale, si tratta di un errore in fase di compilazione per dichiarare un'altra variabile o costante locale con lo stesso nome.

Una dichiarazione di variabile locale che dichiara più variabili è equivalente a più dichiarazioni di variabili singole con lo stesso tipo. Inoltre, un inizializzatore di variabile in una dichiarazione di variabile locale corrisponde esattamente a un'istruzione di assegnazione inserita immediatamente dopo la dichiarazione.

Esempio
```csharp
void F() {
    int x = 1, y, z = x * 2;
}
```
corrisponde esattamente a
```csharp
void F() {
    int x; x = 1;
    int y;
    int z; z = x * 2;
}
```

In una dichiarazione di variabile locale tipizzata in modo implicito, il tipo della variabile locale dichiarata viene considerato uguale al tipo dell'espressione utilizzata per inizializzare la variabile. Ad esempio:
```csharp
var i = 5;
var s = "Hello";
var d = 1.0;
var numbers = new int[] {1, 2, 3};
var orders = new Dictionary<int,Order>();
```

Le dichiarazioni di variabili locali tipizzate in modo implicito sopra sono esattamente equivalenti alle dichiarazioni tipizzate in modo esplicito seguenti:
```csharp
int i = 5;
string s = "Hello";
double d = 1.0;
int[] numbers = new int[] {1, 2, 3};
Dictionary<int,Order> orders = new Dictionary<int,Order>();
```

### <a name="local-constant-declarations"></a>Dichiarazioni di costanti locali

Un *local_constant_declaration* dichiara una o più costanti locali.

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

Il *tipo* di *local_constant_declaration* specifica il tipo delle costanti introdotte dalla dichiarazione. Il tipo è seguito da un elenco di *constant_declarator*, ognuno dei quali introduce una nuova costante. Un *constant_declarator* è costituito da un *identificatore* che assegna un nome alla costante,`=`seguito da un token "", seguito da un *constant_expression* ([espressioni costanti](expressions.md#constant-expressions)) che fornisce il valore della costante.

Il *tipo* e il *constant_expression* di una dichiarazione di costante locale devono rispettare le stesse regole di una dichiarazione di membro costante ([costanti](classes.md#constants)).

Il valore di una costante locale viene ottenuto in un'espressione usando un *simple_name* ([nomi semplici](expressions.md#simple-names)).

L'ambito di una costante locale è il blocco in cui si verifica la dichiarazione. Si tratta di un errore per fare riferimento a una costante locale in una posizione testuale che precede il relativo *constant_declarator*. Nell'ambito di una costante locale, si tratta di un errore in fase di compilazione per dichiarare un'altra variabile o costante locale con lo stesso nome.

Una dichiarazione di costante locale che dichiara più costanti è equivalente a più dichiarazioni di costanti singole con lo stesso tipo.

## <a name="expression-statements"></a>Istruzioni di espressione

Un *expression_statement* valuta un'espressione specificata. Il valore calcolato dall'espressione, se presente, viene ignorato.

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

Non tutte le espressioni sono consentite come istruzioni. In particolare, `x + y` le espressioni come e `x == 1` che calcolano semplicemente un valore (che verrà rimosso) non sono consentite come istruzioni.

L'esecuzione di un *expression_statement* valuta l'espressione contenuta e quindi trasferisce il controllo al punto finale di *expression_statement*. Il punto finale di un *expression_statement* è raggiungibile se il *expression_statement* è raggiungibile.

## <a name="selection-statements"></a>Istruzioni di selezione

Istruzioni di selezione consente di selezionare una delle diverse istruzioni per l'esecuzione in base al valore di un'espressione.

```antlr
selection_statement
    : if_statement
    | switch_statement
    ;
```

### <a name="the-if-statement"></a>Istruzione if

L' `if` istruzione seleziona un'istruzione per l'esecuzione in base al valore di un'espressione booleana.

```antlr
if_statement
    : 'if' '(' boolean_expression ')' embedded_statement
    | 'if' '(' boolean_expression ')' embedded_statement 'else' embedded_statement
    ;
```

Una `else` parte è associata al valore precedente `if` lessicalmente più vicino consentito dalla sintassi. Quindi, un' `if` istruzione nel formato
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

Un' `if` istruzione viene eseguita come indicato di seguito:

*  Viene valutato *Boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)).
*  Se l'espressione booleana restituisce `true`, il controllo viene trasferito alla prima istruzione incorporata. Quando e se il controllo raggiunge il punto finale di tale istruzione, il controllo viene trasferito al punto finale dell' `if` istruzione.
*  Se l'espressione booleana restituisce `false` e se è `else` presente una parte, il controllo viene trasferito alla seconda istruzione incorporata. Quando e se il controllo raggiunge il punto finale di tale istruzione, il controllo viene trasferito al punto finale dell' `if` istruzione.
*  Se l'espressione booleana restituisce `false` e se una `else` parte non è presente, il controllo viene trasferito al punto finale dell' `if` istruzione.

La prima istruzione incorporata di `if` un'istruzione è raggiungibile se l' `if` istruzione è raggiungibile e l'espressione booleana non ha il valore `false`costante.

La seconda istruzione incorporata di `if` un'istruzione, se presente, è raggiungibile se l' `if` istruzione è raggiungibile e l'espressione booleana non ha il valore `true`costante.

Il punto finale di un' `if` istruzione è raggiungibile se il punto finale di almeno una delle relative istruzioni incorporate è raggiungibile. Inoltre, il punto `if` finale di un'istruzione senza `else` parte è raggiungibile se l' `if` istruzione è raggiungibile e l'espressione booleana non ha il valore `true`costante.

### <a name="the-switch-statement"></a>Istruzione switch

L'istruzione switch seleziona per l'esecuzione di un elenco di istruzioni con un'etichetta switch associata che corrisponde al valore dell'espressione switch.

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

Un *switch_Statement* è costituito dalla `switch`parola chiave, seguita da un'espressione racchiusa tra parentesi (denominata espressione switch), seguita da un *switch_block*. *Switch_block* è costituito da zero o più *switch_section*, racchiusi tra parentesi graffe. Ogni *switch_section* è costituito da uno o più *switch_label*, seguiti da un *statement_list* ([elenchi di istruzioni](statements.md#statement-lists)).

Il ***tipo di regola*** di un' `switch` istruzione viene stabilito dall'espressione switch.

*  Se `sbyte`il tipo dell'espressione switch è `uint` `int` `ushort` `short`, `byte`,,,,, `long`, `ulong`, ,`bool` ,`string`o `char`  *enum_type*o se si tratta del tipo nullable corrispondente a uno di questi tipi, si tratta del tipo di governance dell' `switch` istruzione.
*  In caso contrario, è necessario che esista esattamente una conversione implicita definita dall'utente ([conversioni definite dall'utente](conversions.md#user-defined-conversions)) dal tipo dell'espressione switch a uno dei seguenti tipi di regola: `sbyte`, `byte`, `short`, `ushort` , `int`, `uint`, ,`long` ,`char`,o, un tipo nullable corrispondente a uno di questi tipi. `ulong` `string`
*  In caso contrario, se non esiste alcuna conversione implicita o se è presente più di una conversione implicita, si verifica un errore in fase di compilazione.

L'espressione costante di ogni `case` etichetta deve indicare un valore convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) nel tipo di regola dell' `switch` istruzione. Si verifica un errore in fase di compilazione se due `case` o più etichette nella `switch` stessa istruzione specificano lo stesso valore costante.

In un'istruzione switch può essere `default` presente al massimo un'etichetta.

Un' `switch` istruzione viene eseguita come indicato di seguito:

*  L'espressione switch viene valutata e convertita nel tipo di regola.
*  Se una delle costanti specificate in un' `case` etichetta nella stessa `switch` istruzione è uguale al valore dell'espressione switch, il controllo viene trasferito all' `case` elenco di istruzioni che segue l'etichetta corrispondente.
*  Se nessuna `case` delle costanti specificate nelle etichette nella stessa `switch` istruzione è uguale al valore dell'espressione switch e se è presente un' `default` etichetta, il controllo `default` viene trasferito all'elenco di istruzioni che segue etichetta.
*  Se nessuna `case` delle costanti specificate nelle etichette nella stessa `switch` istruzione è uguale al valore dell'espressione switch e se non è presente alcuna `default` etichetta, il controllo `switch` viene trasferito al punto finale dell'istruzione.

Se il punto finale dell'elenco di istruzioni di una sezione switch è raggiungibile, si verifica un errore in fase di compilazione. Questa operazione è nota come regola "No fall through". Esempio
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
è valido perché nessuna sezione switch ha un endpoint raggiungibile. Diversamente da C e C++, l'esecuzione di una sezione switch non è consentita per passare alla sezione switch successiva e l'esempio
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
genera un errore in fase di compilazione. Quando l'esecuzione di una sezione switch deve essere seguita dall'esecuzione di un'altra sezione switch, è `goto case` necessario `goto default` usare un'istruzione o esplicita:
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

In un *switch_section*sono consentite più etichette. Esempio
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
è valido. L'esempio non viola la regola "No fall through" perché le etichette `case 2:` e `default:` fanno parte dello stesso *switch_section*.

La regola "No fall through" impedisce una classe comune di bug che si verificano in C++ C `break` e in caso di omissione accidentale delle istruzioni. Inoltre, a causa di questa regola, le sezioni switch di un' `switch` istruzione possono essere riorganizzate arbitrariamente senza influire sul comportamento dell'istruzione. Le sezioni dell' `switch` istruzione precedente, ad esempio, possono essere invertite senza influire sul comportamento dell'istruzione:
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

L'elenco di istruzioni di una sezione switch termina in genere `break`in `goto case`un'istruzione `goto default` , o, ma qualsiasi costrutto che esegue il rendering del punto finale dell'elenco di istruzioni non raggiungibile è consentito. Ad esempio, un' `while` istruzione controllata dall'espressione `true` booleana è nota che non raggiunge mai il punto finale. Analogamente, `throw` un' `return` istruzione o trasferisce sempre il controllo altrove e non raggiunge mai il punto finale. Di conseguenza, l'esempio seguente è valido:
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

Il tipo di regola di un' `switch` istruzione può essere il tipo `string`. Ad esempio:
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

Analogamente agli operatori di uguaglianza di stringa (operatori di uguaglianza `switch` di[stringa](expressions.md#string-equality-operators)), l'istruzione fa distinzione tra maiuscole e minuscole e consente di eseguire una determinata `case` sezione switch solo se la stringa dell'espressione switch corrisponde esattamente a una costante Label.

Quando il tipo di regola di un' `switch` istruzione è `string`, il valore `null` è consentito come costante di etichetta case.

Il *statement_list*s di un *switch_block* può contenere istruzioni di dichiarazione ([istruzioni di dichiarazione](statements.md#declaration-statements)). L'ambito di una variabile locale o di una costante dichiarata in un blocco switch è il blocco switch.

L'elenco di istruzioni di una sezione switch specificata è raggiungibile se l' `switch` istruzione è raggiungibile e viene soddisfatta almeno una delle condizioni seguenti:

*  L'espressione switch è un valore non costante.
*  L'espressione switch è un valore costante che corrisponde a `case` un'etichetta nella sezione switch.
*  L'espressione switch è un valore costante che non corrisponde ad `case` alcuna etichetta e la sezione switch contiene l' `default` etichetta.
*  Un'etichetta switch della sezione switch è a cui fa riferimento un'istruzione `goto case` o `goto default` raggiungibile.

Il punto finale di un' `switch` istruzione è raggiungibile se si verifica almeno una delle condizioni seguenti:

*  L' `switch` istruzione contiene un'istruzione `break` raggiungibile che esce dall' `switch` istruzione.
*  L' `switch` istruzione è raggiungibile, l'espressione switch è un valore non costante e non è presente alcuna `default` etichetta.
*  L' `switch` istruzione è raggiungibile, l'espressione switch è un valore costante che non corrisponde ad alcuna `case` etichetta e non è `default` presente alcuna etichetta.

## <a name="iteration-statements"></a>Istruzioni di iterazione

Le istruzioni di iterazione eseguono ripetutamente un'istruzione incorporata.

```antlr
iteration_statement
    : while_statement
    | do_statement
    | for_statement
    | foreach_statement
    ;
```

### <a name="the-while-statement"></a>Istruzione while

L' `while` istruzione esegue in modo condizionale un'istruzione incorporata zero o più volte.

```antlr
while_statement
    : 'while' '(' boolean_expression ')' embedded_statement
    ;
```

Un' `while` istruzione viene eseguita come indicato di seguito:

*  Viene valutato *Boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)).
*  Se l'espressione booleana restituisce `true`, il controllo viene trasferito all'istruzione incorporata. Quando e se il controllo raggiunge il punto finale dell'istruzione incorporata (possibilmente dall'esecuzione `continue` di un'istruzione), il controllo viene trasferito all'inizio `while` dell'istruzione.
*  Se l'espressione booleana restituisce `false`, il controllo viene trasferito al punto finale `while` dell'istruzione.

All'interno dell'istruzione incorporata `while` di un'istruzione `break` , un'istruzione ([istruzione break](statements.md#the-break-statement)) può essere utilizzata per trasferire il `while` controllo al punto finale dell'istruzione (in modo che termina l'iterazione dell'istruzione incorporata) e `continue` è possibile utilizzare l'istruzione ([istruzione continue](statements.md#the-continue-statement)) per trasferire il controllo al punto finale dell'istruzione incorporata, eseguendo così un'altra `while` iterazione dell'istruzione.

L'istruzione incorporata di `while` un'istruzione è raggiungibile se l' `while` istruzione è raggiungibile e l'espressione booleana non ha il valore `false`costante.

Il punto finale di un' `while` istruzione è raggiungibile se si verifica almeno una delle condizioni seguenti:

*  L' `while` istruzione contiene un'istruzione `break` raggiungibile che esce dall' `while` istruzione.
*  L' `while` istruzione è raggiungibile e l'espressione booleana non ha il valore `true`costante.

### <a name="the-do-statement"></a>Istruzione do

L' `do` istruzione esegue in modo condizionale un'istruzione incorporata una o più volte.

```antlr
do_statement
    : 'do' embedded_statement 'while' '(' boolean_expression ')' ';'
    ;
```

Un' `do` istruzione viene eseguita come indicato di seguito:

*  Il controllo viene trasferito all'istruzione incorporata.
*  Quando e se il controllo raggiunge il punto finale dell'istruzione incorporata (possibilmente dall'esecuzione `continue` di un'istruzione), viene valutato *Boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)). Se l'espressione booleana restituisce `true`, il controllo viene trasferito all'inizio `do` dell'istruzione. In caso contrario, il controllo viene trasferito al punto finale `do` dell'istruzione.

All'interno dell'istruzione incorporata `do` di un'istruzione `break` , un'istruzione ([istruzione break](statements.md#the-break-statement)) può essere utilizzata per trasferire il `do` controllo al punto finale dell'istruzione (in modo che termina l'iterazione dell'istruzione incorporata) e `continue` l'istruzione ([istruzione continue](statements.md#the-continue-statement)) può essere utilizzata per trasferire il controllo al punto finale dell'istruzione incorporata.

L'istruzione incorporata di `do` un'istruzione è raggiungibile se l' `do` istruzione è raggiungibile.

Il punto finale di un' `do` istruzione è raggiungibile se si verifica almeno una delle condizioni seguenti:

*  L' `do` istruzione contiene un'istruzione `break` raggiungibile che esce dall' `do` istruzione.
*  Il punto finale dell'istruzione incorporata è raggiungibile e l'espressione booleana non ha il valore `true`costante.

### <a name="the-for-statement"></a>Istruzione for

L' `for` istruzione valuta una sequenza di espressioni di inizializzazione e quindi, mentre una condizione è true, esegue ripetutamente un'istruzione incorporata e valuta una sequenza di espressioni di iterazione.

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

Il valore di *for_initializer*, se presente, è costituito da un *local_variable_declaration* ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)) o da un elenco di *statement_expression*s ([istruzioni di espressione](statements.md#expression-statements)) separate da virgole. L'ambito di una variabile locale dichiarata da un *for_initializer* inizia da *local_variable_declarator* per la variabile e si estende fino alla fine dell'istruzione incorporata. L'ambito include *for_condition* e *for_iterator*.

*For_condition*, se presente, deve essere un *Boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)).

Il valore di *for_iterator*, se presente, è costituito da un elenco di *statement_expression*([istruzioni di espressione](statements.md#expression-statements)) separate da virgole.

Un'istruzione for viene eseguita come indicato di seguito:

*  Se è presente un *for_initializer* , gli inizializzatori di variabile o le espressioni di istruzione vengono eseguiti nell'ordine in cui vengono scritti. Questo passaggio viene eseguito una sola volta.
*  Se è presente un *for_condition* , viene valutato.
*  Se *for_condition* non è presente o se la valutazione restituisce `true`, il controllo viene trasferito all'istruzione incorporata. Quando e se il controllo raggiunge il punto finale dell'istruzione incorporata (possibilmente dall'esecuzione `continue` di un'istruzione), le espressioni di *for_iterator*, se presenti, vengono valutate in sequenza e quindi viene eseguita un'altra iterazione, a partire da valutazione del *for_condition* nel passaggio precedente.
*  Se *for_condition* è presente e la valutazione restituisce `false`, il controllo viene trasferito al punto `for` finale dell'istruzione.

All'interno dell'istruzione incorporata `for` di un'istruzione `break` , un'istruzione ([istruzione break](statements.md#the-break-statement)) può essere utilizzata per trasferire il `for` controllo al punto finale dell'istruzione (in modo che termina l'iterazione dell'istruzione incorporata) e `continue` l'istruzione ([istruzione continue](statements.md#the-continue-statement)) può essere utilizzata per trasferire il controllo al punto finale dell'istruzione incorporata, eseguendo in questo modo *for_iterator* ed `for` eseguendo un'altra iterazione dell'istruzione, a partire da *for_condition*).

L'istruzione incorporata di `for` un'istruzione è raggiungibile se si verifica una delle condizioni seguenti:

*  L' `for` istruzione è raggiungibile e non è presente alcun *for_condition* .
*  L' `for` istruzione è raggiungibile e un *for_condition* è presente e non ha il valore `false`costante.

Il punto finale di un' `for` istruzione è raggiungibile se si verifica almeno una delle condizioni seguenti:

*  L' `for` istruzione contiene un'istruzione `break` raggiungibile che esce dall' `for` istruzione.
*  L' `for` istruzione è raggiungibile e un *for_condition* è presente e non ha il valore `true`costante.

### <a name="the-foreach-statement"></a>Istruzione foreach

L' `foreach` istruzione enumera gli elementi di una raccolta, eseguendo un'istruzione incorporata per ogni elemento della raccolta.

```antlr
foreach_statement
    : 'foreach' '(' local_variable_type identifier 'in' expression ')' embedded_statement
    ;
```

Il *tipo* e l' *identificatore* di `foreach` un'istruzione dichiarano la ***variabile di iterazione*** dell'istruzione. Se l' `var` identificatore viene specificato come *local_variable_type*e non è presente alcun tipo `var` denominato nell'ambito, la variabile di iterazione è detta ***variabile di iterazione tipizzata in modo implicito***e il tipo viene considerato come il tipo di elemento del `foreach` , come specificato di seguito. La variabile di iterazione corrisponde a una variabile locale di sola lettura con un ambito che si estende sull'istruzione incorporata. Durante l'esecuzione di `foreach` un'istruzione, la variabile di iterazione rappresenta l'elemento della raccolta per il quale è attualmente in corso l'esecuzione di un'iterazione. Si verifica un errore in fase di compilazione se l'istruzione incorporata tenta di modificare la variabile di iterazione `++` ( `--` tramite assegnazione o gli operatori e) o di `ref` passare `out` la variabile di iterazione come parametro o.

Nell'esempio seguente, per brevità `IEnumerable`, `IEnumerator` `IEnumerable<T>` , e `IEnumerator<T>` si riferiscono ai tipi corrispondenti negli spazi `System.Collections` dei `System.Collections.Generic`nomi e.

L'elaborazione in fase di compilazione di un'istruzione foreach determina innanzitutto il tipo di ***raccolta***, il tipo di ***enumeratore*** e il ***tipo di elemento*** dell'espressione. Questa determinazione procede come segue:

*  Se il tipo `X` di *espressione* è un tipo di matrice, esiste una `X` `IEnumerable` conversione implicita del riferimento da all'interfaccia ( `System.Array` poiché implementa questa interfaccia). Il ***tipo di raccolta*** è `IEnumerable` l'interfaccia, ***il tipo di enumeratore*** è `IEnumerator` l'interfaccia e il tipo di ***elemento*** è il tipo di `X`elemento del tipo di matrice.
*  Se il tipo `X` di *espressione* è `dynamic` , esiste `IEnumerable` una conversione implicita dall' *espressione* all'interfaccia ([conversioni dinamiche implicite](conversions.md#implicit-dynamic-conversions)). Il ***tipo di raccolta*** è `IEnumerable` l'interfaccia e il ***tipo di enumeratore*** è l' `IEnumerator` interfaccia. Se l' `var` identificatore viene specificato come *local_variable_type* , il ***tipo di elemento*** è `dynamic`; in caso contrario `object`, è.
*  In caso contrario, determinare se `X` il tipo dispone `GetEnumerator` di un metodo appropriato:
   * Eseguire la ricerca di membri sul `X` tipo con `GetEnumerator` identificatore e nessun argomento di tipo. Se la ricerca dei membri non produce una corrispondenza o produce un'ambiguità o produce una corrispondenza che non è un gruppo di metodi, verificare la presenza di un'interfaccia enumerabile come descritto di seguito. Si consiglia di emettere un avviso se la ricerca di membri produce qualsiasi elemento tranne un gruppo di metodi o nessuna corrispondenza.
   * Eseguire la risoluzione dell'overload usando il gruppo di metodi risultante e un elenco di argomenti vuoto. Se la risoluzione dell'overload non produce metodi applicabili, comporta un'ambiguità o produce un unico metodo migliore, ma tale metodo è statico o non pubblico, verificare la presenza di un'interfaccia enumerabile come descritto di seguito. Si consiglia di emettere un avviso se la risoluzione dell'overload produce qualsiasi elemento tranne un metodo di istanza pubblica non ambiguo o senza metodi applicabili.
   * Se il tipo `E` restituito `GetEnumerator` del metodo non è una classe, uno struct o un tipo di interfaccia, viene generato un errore e non vengono eseguiti altri passaggi.
   * La ricerca del membro viene `E` eseguita con l' `Current` identificatore e nessun argomento di tipo. Se la ricerca dei membri non produce corrispondenze, il risultato è un errore o il risultato è qualsiasi eccezione, ad eccezione di una proprietà di istanza pubblica che consente la lettura, viene generato un errore e non vengono eseguiti altri passaggi.
   * La ricerca del membro viene `E` eseguita con l' `MoveNext` identificatore e nessun argomento di tipo. Se la ricerca dei membri non produce corrispondenze, il risultato è un errore o il risultato è qualsiasi eccezione a un gruppo di metodi, viene generato un errore e non vengono eseguiti altri passaggi.
   * La risoluzione dell'overload viene eseguita sul gruppo di metodi con un elenco di argomenti vuoto. Se la risoluzione dell'overload non produce metodi applicabili, comporta un'ambiguità o produce un unico metodo migliore, ma tale metodo è statico o non pubblico oppure il tipo restituito non `bool`lo è, viene generato un errore e non vengono eseguiti altri passaggi.
   * Il ***tipo*** di raccolta `X`è, il tipo di `E` ***enumeratore*** è `Current` e il tipo di elemento è il tipo della proprietà.

*  In caso contrario, verificare la presenza di un'interfaccia enumerabile:
   * Se tra tutti i tipi `Ti` per i quali esiste una conversione implicita `X` da `IEnumerable<Ti>`a, esiste un tipo `T` univoco che `T` non `dynamic` è e per tutti gli altri `Ti` è presente un `IEnumerable<T>` la conversione implicita da a, quindi il tipo di ***raccolta*** è l' `IEnumerable<T>`interfaccia, il tipo di `IEnumerator<T>` `IEnumerable<Ti>`enumeratore è l'interfaccia e il tipo di ***elemento*** è `T`.
   * In caso contrario, se è presente più di un `T`tipo di questo tipo, viene generato un errore e non vengono eseguiti altri passaggi.
   * In caso contrario, se esiste una `X` conversione implicita da `System.Collections.IEnumerable` all'interfaccia, il ***tipo di raccolta*** è questa interfaccia, il tipo di ***enumeratore*** è l'interfaccia `System.Collections.IEnumerator`e il tipo di ***elemento*** è `object`.
   * In caso contrario, viene generato un errore e non vengono eseguiti altri passaggi.

I passaggi precedenti, se riusciti, producono in modo non ambiguo un `C`tipo di raccolta `E` , un tipo `T`di enumeratore e un tipo di elemento. Istruzione foreach nel formato
```csharp
foreach (V v in x) embedded_statement
```
viene quindi espanso in:
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

La variabile `e` non è visibile o accessibile all'espressione `x` o all'istruzione incorporata o a qualsiasi altro codice sorgente del programma. La variabile `v` è di sola lettura nell'istruzione incorporata. Se non è presente una conversione esplicita ([conversioni esplicite](conversions.md#explicit-conversions)) `T` da (il tipo di elemento `V` ) a ( *local_variable_type* nell'istruzione foreach), viene generato un errore e non vengono eseguiti altri passaggi. Se `x` ha il valore `null`, viene `System.NullReferenceException` generata un'eccezione in fase di esecuzione.

Un'implementazione può implementare una determinata istruzione foreach in modo diverso, ad esempio per motivi di prestazioni, purché il comportamento sia coerente con l'espansione precedente.

La posizione di `v` all'interno del ciclo while è importante per il modo in cui viene acquisita da qualsiasi funzione anonima che si verifica in *embedded_statement*.

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
Se `v` è stato dichiarato all'esterno del ciclo while, verrebbe condiviso tra tutte le iterazioni e il relativo valore dopo il ciclo for sarebbe il valore finale, `13`, che corrisponde alla chiamata di `f` stampa. Al contrario, poiché ogni iterazione dispone di `v`una propria variabile, quella `f` acquisita dalla prima iterazione continuerà a contenere `7`il valore, che è quello che verrà stampato. (Nota: le versioni precedenti C# di `v` sono state dichiarate all'esterno del ciclo while).

Il corpo del blocco finally viene costruito in base ai passaggi seguenti:

*  Se esiste una conversione implicita da `E` `System.IDisposable` all'interfaccia,
   *  Se `E` è un tipo di valore non nullable, la clausola finally viene espansa nell'equivalente semantico di:

      ```csharp
      finally {
          ((System.IDisposable)e).Dispose();
      }
      ```

   *  In caso contrario, la clausola finally viene espansa all'equivalente semantico di:

      ```csharp
      finally {
          if (e != null) ((System.IDisposable)e).Dispose();
      }
      ```

   ad eccezione del `E` fatto che se è un tipo di valore o un parametro di tipo di cui è stata creata un'istanza `e` a `System.IDisposable` un tipo di valore, il cast di a non comporterà la conversione boxing.

*  In caso contrario `E` , se è un tipo sealed, la clausola finally viene espansa in un blocco vuoto:

   ```csharp
   finally {
   }
   ```

*  In caso contrario, la clausola finally viene espansa in:

   ```csharp
   finally {
       System.IDisposable d = e as System.IDisposable;
       if (d != null) d.Dispose();
   }
   ```    

   La variabile `d` locale non è visibile o accessibile a qualsiasi codice utente. In particolare, non è in conflitto con altre variabili il cui ambito include il blocco finally.

L'ordine in cui `foreach` attraversa gli elementi di una matrice è il seguente: Per gli elementi delle matrici unidimensionali viene attraversato l'ordine di indice crescente, iniziando con `0` index e terminando `Length - 1`con index. Per le matrici multidimensionali, gli elementi vengono attraversati in modo che gli indici della dimensione più a destra vengano aumentati per primi, quindi la dimensione sinistra successiva e così via verso sinistra.

Nell'esempio seguente viene stampato ogni valore in una matrice bidimensionale, nell'ordine degli elementi:
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
L'output prodotto è il seguente:
```csharp
1.2 2.3 3.4 4.5 5.6 6.7 7.8 8.9
```

Nell'esempio
```csharp
int[] numbers = { 1, 3, 5, 7, 9 };
foreach (var n in numbers) Console.WriteLine(n);
```
il tipo di `n` viene dedotto `int`come, il tipo di elemento di `numbers`.

## <a name="jump-statements"></a>Istruzioni di salto

Istruzioni Jump che trasferiscono il controllo in modo non condizionale.

```antlr
jump_statement
    : break_statement
    | continue_statement
    | goto_statement
    | return_statement
    | throw_statement
    ;
```

Il percorso in cui un'istruzione Jump trasferisce il controllo è chiamato la ***destinazione*** dell'istruzione Jump.

Quando un'istruzione Jump si verifica all'interno di un blocco e la destinazione dell'istruzione Jump è esterna a tale blocco, viene detto che l'istruzione Jump ***esce*** dal blocco. Mentre un'istruzione Jump può trasferire il controllo da un blocco, non può mai trasferire il controllo in un blocco.

L'esecuzione di istruzioni di salto è complessa dalla presenza di `try` istruzioni che interessano. In assenza di tali `try` istruzioni, un'istruzione Jump trasferisce in modo incondizionato il controllo dall'istruzione Jump alla relativa destinazione. In presenza di tali `try` istruzioni, l'esecuzione è più complessa. Se l'istruzione Jump esce da uno o più `try` blocchi con blocchi `finally` associati, il controllo `finally` viene inizialmente trasferito al blocco dell'istruzione più `try` interna. Quando e se il controllo raggiunge il punto finale di `finally` un blocco, il `finally` controllo viene trasferito al blocco della successiva istruzione di `try` inclusione. Questo processo viene ripetuto fino a `finally` quando non vengono eseguiti i `try` blocchi di tutte le istruzioni coinvolte.

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
i `finally` blocchi associati a due `try` istruzioni vengono eseguiti prima che il controllo venga trasferito alla destinazione dell'istruzione Jump.

L'output prodotto è il seguente:
```
Before break
Innermost finally block
Outermost finally block
After break
```

### <a name="the-break-statement"></a>Istruzione break

L' `break` istruzione esce dall'istruzione di inclusione `switch`, `while` `do` `for`,, o `foreach` più vicina.

```antlr
break_statement
    : 'break' ';'
    ;
```

La destinazione di un' `break` istruzione è il punto finale dell'istruzione di inclusione `switch`più `while`vicina `do`, `for`,, `foreach` o. Se un' `break` istruzione non è racchiusa tra `switch`un' `while`istruzione `do`, `for`,, `foreach` o, si verifica un errore in fase di compilazione.

Quando più `switch`istruzioni `while` `break` ,, ,`for` o`foreach` sono annidate all'interno di un'altra, un'istruzione si applica solo all'istruzione più interna. `do` Per trasferire il controllo tra più livelli di nidificazione `goto` , è necessario utilizzare un'istruzione ([istruzione goto](statements.md#the-goto-statement)).

Un' `break` istruzione non può uscire `finally` da un blocco ([istruzione try](statements.md#the-try-statement)). Quando un' `break` istruzione si verifica all' `finally` interno di un blocco `break` , la destinazione dell'istruzione deve trovarsi `finally` all'interno dello stesso blocco; in caso contrario, si verifica un errore in fase di compilazione.

Un' `break` istruzione viene eseguita come indicato di seguito:

*  Se l' `break` istruzione esce da uno o più `try` blocchi con `finally` blocchi `finally` associati, il controllo viene inizialmente trasferito al blocco dell'istruzione più `try` interna. Quando e se il controllo raggiunge il punto finale di `finally` un blocco, il `finally` controllo viene trasferito al blocco della successiva istruzione di `try` inclusione. Questo processo viene ripetuto fino a `finally` quando non vengono eseguiti i `try` blocchi di tutte le istruzioni coinvolte.
*  Il controllo viene trasferito alla destinazione dell' `break` istruzione.

Poiché un' `break` istruzione trasferisce in modo incondizionato il controllo altrove, il `break` punto finale di un'istruzione non è mai raggiungibile.

### <a name="the-continue-statement"></a>Istruzione continue

L' `continue` istruzione avvia una nuova iterazione dell'istruzione di inclusione `do` `while`, `for`, o `foreach` più vicina.

```antlr
continue_statement
    : 'continue' ';'
    ;
```

La destinazione di un' `continue` istruzione è il punto finale dell'istruzione incorporata dell'istruzione, `do`, `for`o `while` `foreach` di inclusione più vicina. Se un' `continue` istruzione non è racchiusa tra `while`un' `do`istruzione `for`,, `foreach` o, si verifica un errore in fase di compilazione.

Quando più `while`istruzioni `do` `continue` ,, o `foreach` sono annidate all'interno di un'altra, un'istruzione si applica solo all'istruzione più interna. `for` Per trasferire il controllo tra più livelli di nidificazione `goto` , è necessario utilizzare un'istruzione ([istruzione goto](statements.md#the-goto-statement)).

Un' `continue` istruzione non può uscire `finally` da un blocco ([istruzione try](statements.md#the-try-statement)). Quando un' `continue` istruzione si verifica all' `finally` interno di un blocco `continue` , la destinazione dell'istruzione deve trovarsi `finally` all'interno dello stesso blocco; in caso contrario, si verifica un errore in fase di compilazione.

Un' `continue` istruzione viene eseguita come indicato di seguito:

*  Se l' `continue` istruzione esce da uno o più `try` blocchi con `finally` blocchi `finally` associati, il controllo viene inizialmente trasferito al blocco dell'istruzione più `try` interna. Quando e se il controllo raggiunge il punto finale di `finally` un blocco, il `finally` controllo viene trasferito al blocco della successiva istruzione di `try` inclusione. Questo processo viene ripetuto fino a `finally` quando non vengono eseguiti i `try` blocchi di tutte le istruzioni coinvolte.
*  Il controllo viene trasferito alla destinazione dell' `continue` istruzione.

Poiché un' `continue` istruzione trasferisce in modo incondizionato il controllo altrove, il `continue` punto finale di un'istruzione non è mai raggiungibile.

### <a name="the-goto-statement"></a>Istruzione GoTo

L' `goto` istruzione trasferisce il controllo a un'istruzione contrassegnata da un'etichetta.

```antlr
goto_statement
    : 'goto' identifier ';'
    | 'goto' 'case' constant_expression ';'
    | 'goto' 'default' ';'
    ;
```

La destinazione di un' `goto` istruzione *Identifier* è l'istruzione con etichetta con l'etichetta specificata. Se un'etichetta con il nome specificato non esiste nel membro della funzione corrente o se l' `goto` istruzione non è inclusa nell'ambito dell'etichetta, si verifica un errore in fase di compilazione. Questa regola consente di utilizzare un' `goto` istruzione per trasferire il controllo da un ambito annidato, ma non da un ambito annidato. Nell'esempio
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
viene `goto` utilizzata un'istruzione per trasferire il controllo da un ambito annidato.

La destinazione di un' `goto case` istruzione è l'elenco di istruzioni nell'istruzione di inclusione `switch` immediata ([istruzione switch](statements.md#the-switch-statement)), che contiene un' `case` etichetta con il valore costante specificato. Se l' `goto case` istruzione non è racchiusa tra `switch` un'istruzione, se *constant_expression* non è implicitamente convertibile ([conversioni implicite](conversions.md#implicit-conversions)) nel tipo di regola dell'istruzione di inclusione `switch` più vicina oppure se l'istruzione di inclusione `switch` più vicina non contiene un' `case` etichetta con il valore costante specificato, si verifica un errore in fase di compilazione.

La destinazione di un' `goto default` istruzione è l'elenco di istruzioni nell'istruzione di inclusione `switch` immediata ([istruzione switch](statements.md#the-switch-statement)), che contiene un' `default` etichetta. Se l' `goto default` istruzione non è racchiusa tra `switch` un'istruzione o l'istruzione di inclusione `switch` più vicina non contiene un' `default` etichetta, si verificherà un errore in fase di compilazione.

Un' `goto` istruzione non può uscire `finally` da un blocco ([istruzione try](statements.md#the-try-statement)). Quando un' `goto` istruzione si verifica all' `finally` interno di un blocco `goto` , la destinazione dell'istruzione deve trovarsi `finally` all'interno dello stesso blocco o in caso contrario si verifica un errore in fase di compilazione.

Un' `goto` istruzione viene eseguita come indicato di seguito:

*  Se l' `goto` istruzione esce da uno o più `try` blocchi con `finally` blocchi `finally` associati, il controllo viene inizialmente trasferito al blocco dell'istruzione più `try` interna. Quando e se il controllo raggiunge il punto finale di `finally` un blocco, il `finally` controllo viene trasferito al blocco della successiva istruzione di `try` inclusione. Questo processo viene ripetuto fino a `finally` quando non vengono eseguiti i `try` blocchi di tutte le istruzioni coinvolte.
*  Il controllo viene trasferito alla destinazione dell' `goto` istruzione.

Poiché un' `goto` istruzione trasferisce in modo incondizionato il controllo altrove, il `goto` punto finale di un'istruzione non è mai raggiungibile.

### <a name="the-return-statement"></a>Istruzione return

L' `return` istruzione restituisce il controllo al chiamante corrente della funzione in cui viene visualizzata `return` l'istruzione.

```antlr
return_statement
    : 'return' expression? ';'
    ;
```

Un' `return` istruzione senza espressione può essere utilizzata solo in un membro di funzione che non calcola un valore, ovvero un metodo con il tipo di risultato ([corpo del metodo](classes.md#method-body) `void`), la `set` funzione di accesso di una proprietà o un indicizzatore, `add` il funzioni `remove` di accesso e di un evento, un costruttore di istanza, un costruttore statico o un distruttore.

Un' `return` istruzione con un'espressione può essere utilizzata solo in un membro di funzione che calcola un valore, ovvero un metodo con un tipo di risultato non void, la `get` funzione di accesso di una proprietà o un indicizzatore o un operatore definito dall'utente. Una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) deve esistere dal tipo dell'espressione al tipo restituito del membro della funzione contenitore.

È inoltre possibile utilizzare le istruzioni return nel corpo di espressioni di funzione anonime ([espressioni di funzione anonime](expressions.md#anonymous-function-expressions)) e partecipare alla determinazione delle conversioni esistenti per tali funzioni.

Si tratta di un errore in fase di compilazione `return` per un'istruzione da visualizzare `finally` in un blocco ([istruzione try](statements.md#the-try-statement)).

Un' `return` istruzione viene eseguita come indicato di seguito:

*  Se l' `return` istruzione specifica un'espressione, l'espressione viene valutata e il valore risultante viene convertito nel tipo restituito della funzione che lo contiene mediante una conversione implicita. Il risultato della conversione diventa il valore risultante prodotto dalla funzione.
*  Se l' `return` istruzione è racchiusa tra uno o `try` più `catch` blocchi o con `finally` blocchi associati, il controllo viene inizialmente trasferito `finally` al blocco dell'istruzione `try` più interna. Quando e se il controllo raggiunge il punto finale di `finally` un blocco, il `finally` controllo viene trasferito al blocco della successiva istruzione di `try` inclusione. Questo processo viene ripetuto fino a `finally` quando non vengono eseguiti i `try` blocchi di tutte le istruzioni di inclusione.
*  Se la funzione che lo contiene non è una funzione asincrona, il controllo viene restituito al chiamante della funzione contenitore insieme al valore del risultato, se presente.
*  Se la funzione contenitore è una funzione asincrona, il controllo viene restituito al chiamante corrente e il valore del risultato, se presente, viene registrato nell'attività restituita come descritto in ([interfacce enumeratore](classes.md#enumerator-interfaces)).

Poiché un' `return` istruzione trasferisce in modo incondizionato il controllo altrove, il `return` punto finale di un'istruzione non è mai raggiungibile.

### <a name="the-throw-statement"></a>Istruzione throw

L' `throw` istruzione genera un'eccezione.

```antlr
throw_statement
    : 'throw' expression? ';'
    ;
```

Un' `throw` istruzione con un'espressione genera il valore prodotto dalla valutazione dell'espressione. L'espressione deve indicare un valore del tipo `System.Exception`di classe, di un tipo di classe che deriva da `System.Exception` o di un tipo di parametro di tipo che `System.Exception` ha (o una sottoclasse) come classe di base valida. Se la valutazione dell'espressione produce `null`, viene `System.NullReferenceException` invece generata un'eccezione.

Un' `throw` istruzione senza espressione può essere utilizzata solo in un `catch` blocco, nel qual caso tale istruzione genera di nuovo l'eccezione attualmente gestita da tale `catch` blocco.

Poiché un' `throw` istruzione trasferisce in modo incondizionato il controllo altrove, il `throw` punto finale di un'istruzione non è mai raggiungibile.

Quando viene generata un'eccezione, il controllo viene trasferito alla prima `catch` clausola in un'istruzione contenitore `try` in grado di gestire l'eccezione. Il processo che si verifica dal punto dell'eccezione generata al momento del trasferimento del controllo a un gestore di eccezioni adatto è noto come ***propagazione delle eccezioni***. La propagazione di un'eccezione è costituita dalla valutazione ripetuta dei `catch` passaggi seguenti fino a quando non viene trovata una clausola che corrisponde all'eccezione. In questa descrizione, il ***punto di generazione*** è inizialmente la posizione in cui viene generata l'eccezione.

*  Nel membro della funzione corrente, viene `try` esaminata ogni istruzione che racchiude il punto di generazione. Per ogni istruzione `S`, a partire dall'istruzione `try` più interna e terminando con `try` l'istruzione più esterna, vengono valutati i passaggi seguenti:

   * Se il `S` `catch` `catch` blocco di racchiude il punto di generazione e se S ha una o più clausole, le clausole vengono esaminate in ordine di aspetto per individuare un gestore appropriato per l'eccezione, in base alle regole specificate in `try` Sezione [espressione try](statements.md#the-try-statement). Se viene individuata una clausola corrispondente `catch` , la propagazione delle eccezioni viene completata trasferendo il controllo al blocco `catch` della clausola.

   * In caso contrario, `try` se il blocco `catch` o un `S` blocco di racchiude il punto di generazione e `S` se dispone `finally` di un blocco, il controllo viene `finally` trasferito nel blocco. Se il `finally` blocco genera un'altra eccezione, l'elaborazione dell'eccezione corrente viene terminata. In caso contrario, quando il controllo raggiunge il punto `finally` finale del blocco, viene continuata l'elaborazione dell'eccezione corrente.

*  Se un gestore di eccezioni non si trova nella chiamata della funzione corrente, la chiamata alla funzione viene terminata e si verifica una delle condizioni seguenti:

   * Se la funzione corrente è non asincrona, i passaggi precedenti vengono ripetuti per il chiamante della funzione con un punto di generazione corrispondente all'istruzione da cui il membro della funzione è stato richiamato.

   * Se la funzione corrente è asincrona e restituisce un'attività, l'eccezione viene registrata nell'attività return, che viene inserita in uno stato di errore o annullato, come descritto in [interfacce dell'enumeratore](classes.md#enumerator-interfaces).

   * Se la funzione corrente è asincrona e restituisce un risultato null, viene notificato il contesto di sincronizzazione del thread corrente come descritto in [interfacce enumerabili](classes.md#enumerable-interfaces).

*  Se l'elaborazione delle eccezioni termina tutte le chiamate del membro della funzione nel thread corrente, a indicare che il thread non ha alcun gestore per l'eccezione, il thread viene interrotto. L'effetto di tale interruzione è definito dall'implementazione.

## <a name="the-try-statement"></a>Istruzione try

L' `try` istruzione fornisce un meccanismo per intercettare le eccezioni che si verificano durante l'esecuzione di un blocco. L' `try` istruzione offre inoltre la possibilità di specificare un blocco di codice che viene sempre eseguito quando il controllo lascia l' `try` istruzione.

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

Esistono tre possibili forme di `try` istruzioni:

*  Un `try` blocco seguito da uno o più `catch` blocchi.
*  Un `try` blocco seguito da un `finally` blocco.
*  Un `try` blocco seguito da uno o più `catch` blocchi seguiti da `finally` un blocco.

Quando una `catch` clausola specifica un *exception_specifier*, il tipo deve essere `System.Exception`, un tipo che deriva da `System.Exception` o un tipo di parametro di tipo che `System.Exception` ha (o una sottoclasse) come classe di base valida.

Quando una `catch` clausola specifica sia un *exception_specifier* con un *identificatore*, viene dichiarata una ***variabile di eccezione*** con il nome e il tipo specificati. La variabile di eccezione corrisponde a una variabile locale con un ambito che si estende `catch` sulla clausola. Durante l'esecuzione del *exception_filter* e del *blocco*, la variabile di eccezione rappresenta l'eccezione attualmente gestita. Ai fini del controllo di assegnazione definito, la variabile di eccezione viene considerata definitivamente assegnata nell'intero ambito.

A meno che `catch` una clausola non includa un nome di variabile di eccezione, non è possibile accedere all'oggetto eccezione `catch` nel filtro e nel blocco.

Una `catch` clausola che non specifica un *exception_specifier* è detta clausola generale `catch` .

Alcuni linguaggi di programmazione possono supportare eccezioni che non possono essere rappresentate come un oggetto derivato `System.Exception`da, sebbene tali eccezioni non possano mai essere C# generate dal codice. È possibile `catch` utilizzare una clausola generale per intercettare tali eccezioni. Pertanto, una clausola `catch` generale è semanticamente diversa da quella che specifica il tipo `System.Exception`, in quanto il primo può rilevare anche eccezioni da altri linguaggi.

Per individuare un gestore per un'eccezione, `catch` le clausole vengono esaminate in ordine lessicale. Se una `catch` clausola specifica un tipo ma nessun filtro eccezioni, si tratta di un errore in fase di compilazione per `catch` una clausola successiva nella `try` stessa istruzione per specificare un tipo che è uguale a o è derivato da tale tipo. Se una `catch` clausola non specifica alcun tipo e nessun filtro, deve essere l'ultima `catch` clausola `try` dell'istruzione.

All'interno `catch` di un blocco `throw` , un'istruzione ([istruzione throw](statements.md#the-throw-statement)) senza espressione può essere utilizzata per generare di nuovo `catch` l'eccezione rilevata dal blocco. Le assegnazioni a una variabile di eccezione non modificano l'eccezione generata nuovamente.

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
il metodo `F` rileva un'eccezione, scrive alcune informazioni di diagnostica nella console, modifica la variabile di eccezione e genera nuovamente l'eccezione. L'eccezione generata di nuovo è l'eccezione originale, quindi l'output prodotto è:
```
Exception in F: G
Exception in Main: G
```

Se è stato generato `e` il primo blocco catch anziché rigenerare l'eccezione corrente, l'output prodotto sarà il seguente:
```
Exception in F: G
Exception in Main: F
```

Si tratta di un errore in fase di compilazione `break`per `continue`un'istruzione `goto` , o per trasferire il controllo all' `finally` esterno di un blocco. Quando un' `break`istruzione `continue`, o `goto` si verifica in un `finally` blocco, la destinazione dell'istruzione deve trovarsi all'interno dello `finally` stesso blocco o in caso contrario si verifica un errore in fase di compilazione.

Si tratta di un errore in fase di compilazione `return` per un'istruzione che si `finally` verifica in un blocco.

Un' `try` istruzione viene eseguita come indicato di seguito:

*  Il `try` controllo viene trasferito nel blocco.
*  Quando e se il controllo raggiunge il punto finale del `try` blocco:
   *  Se l' `try` istruzione ha un `finally` blocco, il `finally` blocco viene eseguito.
   *  Il controllo viene trasferito al punto finale dell' `try` istruzione.

*  Se un'eccezione viene propagata all' `try` istruzione durante l'esecuzione `try` del blocco:
   *  Le `catch` clausole, se presenti, vengono esaminate in ordine di aspetto per individuare un gestore appropriato per l'eccezione. Se una `catch` clausola non specifica un tipo o specifica il tipo di eccezione o un tipo di base del tipo di eccezione:
      *  Se la `catch` clausola dichiara una variabile di eccezione, l'oggetto eccezione viene assegnato alla variabile di eccezione.
      *  Se la `catch` clausola dichiara un filtro eccezioni, il filtro viene valutato. Se restituisce `false`, la clausola catch non è una corrispondenza e la ricerca continua con le clausole successive `catch` per un gestore appropriato.
      *  In caso contrario `catch` , la clausola viene considerata una corrispondenza e il controllo viene trasferito al `catch` blocco corrispondente.
      *  Quando e se il controllo raggiunge il punto finale del `catch` blocco:
         * Se l' `try` istruzione ha un `finally` blocco, il `finally` blocco viene eseguito.
         * Il controllo viene trasferito al punto finale dell' `try` istruzione.
      *  Se un'eccezione viene propagata all' `try` istruzione durante l'esecuzione `catch` del blocco:
         *  Se l' `try` istruzione ha un `finally` blocco, il `finally` blocco viene eseguito.
         *  L'eccezione viene propagata alla successiva istruzione di `try` inclusione.
   *  Se l' `try` istruzione non `catch` ha clausole o se nessuna `catch` clausola corrisponde all'eccezione:
      *  Se l' `try` istruzione ha un `finally` blocco, il `finally` blocco viene eseguito.
      *  L'eccezione viene propagata alla successiva istruzione di `try` inclusione.

Le istruzioni di un `finally` blocco vengono sempre eseguite quando il controllo lascia `try` un'istruzione. Questo vale se il trasferimento del controllo si verifica in seguito all'esecuzione normale, in seguito `break`all'esecuzione di un'istruzione, `continue`, `goto`o o `return` a seguito della propagazione di `try` un'eccezione dal istruzione.

Se viene generata un'eccezione durante l'esecuzione di `finally` un blocco e non viene rilevata all'interno dello stesso blocco finally, l'eccezione viene propagata `try` alla successiva istruzione di inclusione. Se è in corso la propagazione di un'altra eccezione, l'eccezione viene persa. Il processo di propagazione di un'eccezione viene discusso ulteriormente nella descrizione dell' `throw` istruzione ([istruzione throw](statements.md#the-throw-statement)).

Il `try` blocco di un' `try` istruzione è raggiungibile se l' `try` istruzione è raggiungibile.

Un `catch` blocco di un' `try` istruzione è raggiungibile se l' `try` istruzione è raggiungibile.

Il `finally` blocco di un' `try` istruzione è raggiungibile se l' `try` istruzione è raggiungibile.

Il punto finale di un' `try` istruzione è raggiungibile se si verificano entrambe le condizioni seguenti:

*  Il punto finale del `try` blocco è raggiungibile oppure è raggiungibile il punto finale di almeno un `catch` blocco.
*  Se è `finally` presente un blocco, il punto finale `finally` del blocco è raggiungibile.

## <a name="the-checked-and-unchecked-statements"></a>Istruzioni checked e unchecked

Le `checked` istruzioni `unchecked` e vengono utilizzate per controllare il ***contesto di controllo dell'overflow*** per le conversioni e le operazioni aritmetiche di tipo integrale.

```antlr
checked_statement
    : 'checked' block
    ;

unchecked_statement
    : 'unchecked' block
    ;
```

L' `checked` istruzione fa in modo che tutte le espressioni del *blocco* vengano valutate in un contesto controllato `unchecked` e l'istruzione provochi la valutazione di tutte le espressioni del *blocco* in un contesto non verificato.

Le `checked` istruzioni `unchecked` e`checked` sono esattamente equivalenti agli operatori `unchecked` and ([gli operatori checked e unchecked](expressions.md#the-checked-and-unchecked-operators)), ad eccezione del fatto che operano su blocchi anziché su espressioni.

## <a name="the-lock-statement"></a>Istruzione lock

L' `lock` istruzione ottiene il blocco di esclusione reciproca per un determinato oggetto, esegue un'istruzione e quindi rilascia il blocco.

```antlr
lock_statement
    : 'lock' '(' expression ')' embedded_statement
    ;
```

L'espressione di un' `lock` istruzione deve indicare un valore di un tipo noto come *reference_type*. Non viene mai eseguita alcuna conversione boxing implicita ([conversioni boxing](conversions.md#boxing-conversions)) per l'espressione di `lock` un'istruzione e pertanto si tratta di un errore in fase di compilazione perché l'espressione denoti un valore di *value_type*.

Un' `lock` istruzione nel formato
```csharp
lock (x) ...
```
dove `x` è un'espressione di un *reference_type*, è esattamente equivalente a
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

Mentre viene mantenuto un blocco di esclusione reciproca, il codice in esecuzione nello stesso thread di esecuzione può anche ottenere e rilasciare il blocco. Tuttavia, il codice in esecuzione in altri thread viene bloccato dall'ottenimento del blocco fino al rilascio del blocco.

Non `System.Type` è consigliabile bloccare gli oggetti per sincronizzare l'accesso ai dati statici. Un altro codice potrebbe bloccarsi sullo stesso tipo, che può causare un deadlock. Un approccio migliore consiste nel sincronizzare l'accesso ai dati statici bloccando un oggetto statico privato. Ad esempio:
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

L' `using` istruzione ottiene una o più risorse, esegue un'istruzione e quindi Elimina la risorsa.

```antlr
using_statement
    : 'using' '(' resource_acquisition ')' embedded_statement
    ;

resource_acquisition
    : local_variable_declaration
    | expression
    ;
```

Una ***risorsa*** è una classe o uno struct che `System.IDisposable`implementa, che include un singolo metodo senza parametri `Dispose`denominato. Il codice che usa una risorsa può chiamare `Dispose` per indicare che la risorsa non è più necessaria. Se `Dispose` non viene chiamato, l'eliminazione automatica viene infine eseguita come conseguenza del Garbage Collection.

Se il formato di *resource_acquisition* è *local_variable_declaration* , il tipo di `dynamic` *local_variable_declaration* deve essere o un tipo che può essere convertito in modo implicito in `System.IDisposable`. Se il formato di *resource_acquisition* è *Expression* , l'espressione deve essere convertibile in modo `System.IDisposable`implicito in.

Le variabili locali dichiarate in un *resource_acquisition* sono di sola lettura e devono includere un inizializzatore. Si verifica un errore in fase di compilazione se l'istruzione incorporata tenta di modificare queste variabili locali (tramite `++` assegnazione `--` o gli operatori e), di prenderne l'indirizzo o di `ref` passarle come parametri o `out` .

Un' `using` istruzione viene convertita in tre parti: acquisizione, utilizzo e eliminazione. L'utilizzo della risorsa viene racchiuso in modo implicito in un' `try` istruzione che include una `finally` clausola. Questa `finally` clausola Elimina la risorsa. Se viene `null` acquisita una risorsa, non viene effettuata `Dispose` alcuna chiamata a e non viene generata alcuna eccezione. Se la risorsa è di tipo `dynamic` , viene convertita dinamicamente tramite una conversione dinamica implicita ([conversioni dinamiche implicite](conversions.md#implicit-dynamic-conversions)) `IDisposable` in durante l'acquisizione, in modo da garantire che la conversione venga eseguita prima dell'utilizzo e eliminazione.

Un' `using` istruzione nel formato
```csharp
using (ResourceType resource = expression) statement
```
corrisponde a una delle tre espansioni possibili. Quando `ResourceType` è un tipo di valore non nullable, l'espansione è
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

In caso contrario `ResourceType` , quando è un tipo di valore nullable o un tipo `dynamic`di riferimento diverso da, l'espansione è
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

In caso contrario `ResourceType` , `dynamic`quando è, l'espansione è
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

In una delle due espansioni `resource` , la variabile è di sola lettura nell'istruzione incorporata e `d` la variabile è inaccessibile in e invisibile a, l'istruzione incorporata.

Un'implementazione di può implementare una determinata istruzione using in modo diverso, ad esempio per motivi di prestazioni, purché il comportamento sia coerente con l'espansione precedente.

Un' `using` istruzione nel formato
```csharp
using (expression) statement
```
presenta le stesse tre espansioni possibili. In questo caso `ResourceType` è implicitamente il tipo in fase `expression`di compilazione di, se presente. In caso contrario `IDisposable` `ResourceType`, l'interfaccia stessa viene utilizzata come. La `resource` variabile è inaccessibile in e invisibile a, l'istruzione incorporata.

Quando un *resource_acquisition* assume il formato di un *local_variable_declaration*, è possibile acquisire più risorse di un determinato tipo. Un' `using` istruzione nel formato
```csharp
using (ResourceType r1 = e1, r2 = e2, ..., rN = eN) statement
```
è esattamente equivalente a una sequenza di istruzioni `using` nidificate:
```csharp
using (ResourceType r1 = e1)
    using (ResourceType r2 = e2)
        ...
            using (ResourceType rN = eN)
                statement
```

Nell'esempio seguente viene creato un file `log.txt` denominato e vengono scritte due righe di testo nel file. Nell'esempio viene quindi aperto lo stesso file per la lettura e la copia delle righe di testo contenute nella console.
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

Poiché le classi `TextReader`eimplementano l' `IDisposable` interfaccia, l'esempio può `using` utilizzare le istruzioni per garantire che il file sottostante venga chiuso correttamente dopo le operazioni di scrittura o lettura. `TextWriter`

## <a name="the-yield-statement"></a>Istruzione yield

L' `yield` istruzione viene utilizzata in un blocco iteratore ([blocchi](statements.md#blocks)) per restituire un valore all'oggetto enumeratore ([oggetti enumeratore](classes.md#enumerator-objects)) o a un oggetto enumerabile ([oggetti enumerabile](classes.md#enumerable-objects)) di un iteratore o per segnalare la fine dell'iterazione.

```antlr
yield_statement
    : 'yield' 'return' expression ';'
    | 'yield' 'break' ';'
    ;
```

`yield`non è una parola riservata. ha un significato speciale solo quando viene usato immediatamente prima `return` di `break` una parola chiave o. In altri contesti, `yield` può essere usato come identificatore.

Esistono diverse restrizioni per la posizione in `yield` cui può essere visualizzata un'istruzione, come descritto di seguito.

*  Si tratta di un errore in fase di compilazione `yield` per un'istruzione (di uno dei due form) che viene visualizzata all'esterno di *method_body*, *operator_body* o *accessor_body*
*  Si tratta di un errore in fase di compilazione `yield` per l'inclusione di un'istruzione (di uno dei due form) all'interno di una funzione anonima.
*  Si tratta di un errore in fase di compilazione `yield` per l' `finally` inclusione di un'istruzione (di uno dei due form `try` ) nella clausola di un'istruzione.
*  Si tratta di un errore in fase di compilazione `yield return` per un'istruzione che viene visualizzata `try` in un punto qualsiasi `catch` di un'istruzione che contiene clausole.

Nell'esempio seguente vengono illustrati alcuni usi validi e `yield` non validi delle istruzioni.

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

Una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) deve esistere dal tipo dell'espressione nell' `yield return` istruzione al tipo yield ([tipo](classes.md#yield-type)di risultato) dell'iteratore.

Un' `yield return` istruzione viene eseguita come indicato di seguito:

*  L'espressione specificata nell'istruzione viene valutata, convertita in modo implicito nel tipo yield e assegnata `Current` alla proprietà dell'oggetto enumeratore.
*  L'esecuzione del blocco iteratore è sospesa. Se l' `yield return` istruzione si trova all'interno di `try` uno o più blocchi `finally` , i blocchi associati non vengono eseguiti in questo momento.
*  Il `MoveNext` metodo dell'oggetto enumeratore viene `true` restituito al chiamante, a indicare che l'oggetto enumeratore è stato spostato correttamente sull'elemento successivo.

La chiamata successiva al `MoveNext` metodo dell'oggetto enumeratore riprende l'esecuzione del blocco iteratore dal punto in cui è stata sospesa l'ultima volta.

Un' `yield break` istruzione viene eseguita come indicato di seguito:

*  Se l' `yield break` istruzione è racchiusa tra uno o `try` più blocchi con `finally` `finally` blocchi associati, il controllo viene inizialmente trasferito al blocco dell'istruzione `try` più interna. Quando e se il controllo raggiunge il punto finale di `finally` un blocco, il `finally` controllo viene trasferito al blocco della successiva istruzione di `try` inclusione. Questo processo viene ripetuto fino a `finally` quando non vengono eseguiti i `try` blocchi di tutte le istruzioni di inclusione.
*  Il controllo viene restituito al chiamante del blocco iteratore. Si tratta del `MoveNext` metodo o `Dispose` del metodo dell'oggetto enumeratore.

Poiché un' `yield break` istruzione trasferisce in modo incondizionato il controllo altrove, il `yield break` punto finale di un'istruzione non è mai raggiungibile.
