# <a name="variables"></a>Variabili

Le variabili rappresentano posizioni di archiviazione. Ogni variabile dispone di un tipo che determina quali valori possono essere archiviati nella variabile. C# è un linguaggio indipendente dai tipi e il compilatore C# garantisce che i valori archiviati nelle variabili sono sempre del tipo appropriato. Il valore di una variabile può essere modificato tramite l'assegnazione o tramite l'uso del `++` e `--` operatori.

Deve essere una variabile ***assegnata in modo definitivo*** ([assegnazione certa](variables.md#definite-assignment)) prima di poter ottenere il relativo valore.

Come descritto nelle sezioni seguenti, le variabili sono entrambi ***inizialmente assegnata*** oppure ***assegnate inizialmente***. Una variabile assegnata inizialmente ha un valore iniziale ben definito e viene considerata sempre assegnata in modo definitivo. Una variabile non assegnata inizialmente prevede alcun valore iniziale. Per una variabile assegnata inizialmente assegnata in modo da considerare in una determinata località, deve verificarsi un'assegnazione alla variabile in ogni percorso di esecuzione potenziale di tale posizione.

## <a name="variable-categories"></a>Categorie di variabili

C# definisce sette categorie di variabili: variabili statiche, le variabili di istanza, gli elementi della matrice, parametri con valori, i parametri per riferimento, i parametri di output e le variabili locali. Le sezioni che seguono descrivono ognuna di queste categorie.

Nell'esempio
```csharp
class A
{
public static int x;
int y;

void F(int[] v, int a, ref int b, out int c) {
int i = 1;
c = a + b++;
}
}
```
`x` è una variabile statica `y` è una variabile di istanza `v[0]` è un elemento della matrice `a` è un parametro, `b` è un parametro di riferimento, `c` è un parametro di output e `i` è una variabile locale.

### <a name="static-variables"></a>Variabili statiche

Un campo dichiarato con la `static` modificatore viene chiamato un ***variabile statica***. Una variabile statica viene generato prima dell'esecuzione del costruttore statico ([costruttori statici](classes.md#static-constructors)) per il tipo di contenitore e cessa di esistere quando il dominio dell'applicazione associato cessa di esistere.

Il valore iniziale di una variabile statica è il valore predefinito ([i valori predefiniti](variables.md#default-values)) del tipo della variabile.

Ai fini di controllo dell'assegnazione definita, una variabile statica viene considerata inizialmente assegnata.

### <a name="instance-variables"></a>Variabili di istanza

Un campo dichiarato senza il `static` modificatore viene chiamato un ***variabile di istanza***.

#### <a name="instance-variables-in-classes"></a>Variabili di istanza nelle classi

Una variabile di istanza di una classe viene generata quando viene creata una nuova istanza della classe e cessa di esistere quando non sono presenti riferimenti a quell'istanza ed è stato eseguito un distruttore dell'istanza (se presente).

Il valore iniziale di una variabile di istanza di una classe è il valore predefinito ([i valori predefiniti](variables.md#default-values)) del tipo della variabile.

Ai fini di controllo dell'assegnazione definita, una variabile di istanza di una classe viene considerata come inizialmente assegnata.

#### <a name="instance-variables-in-structs"></a>Variabili di istanza negli struct

Una variabile di istanza di uno struct è esattamente la stessa durata della variabile di struct a cui appartiene. In altre parole, quando una variabile di un tipo struct viene generata o cessa di esistere, questa operazione anche per le variabili di istanza dello struct.

Lo stato di assegnazione iniziale di una variabile di istanza di uno struct è uguale a quello della variabile di uno struct che lo contiene. In altre parole, quando una variabile di uno struct viene considerata inizialmente assegnata, questa operazione sono troppo relative variabili di istanza e se una variabile di uno struct viene considerata assegnata inizialmente, le variabili di istanza in modo analogo sono assegnate.

### <a name="array-elements"></a>Elementi della matrice

Gli elementi della matrice vengono create quando viene creata un'istanza di matrice e cessano di esistere quando non sono presenti riferimenti a tale istanza di matrice.

Il valore iniziale della ognuno degli elementi della matrice è il valore predefinito ([i valori predefiniti](variables.md#default-values)) del tipo degli elementi della matrice.

Ai fini di controllo dell'assegnazione definita, viene considerato un elemento di matrice inizialmente assegnata.

### <a name="value-parameters"></a>Parametri di valore

Un parametro dichiarato senza un `ref` oppure `out` modificatore è un ***parametro value***.

Un parametro di valore viene generato al momento della chiamata del membro di funzione ((metodo), costruttore di istanza, della funzione di accesso o operator) o della funzione anonima a cui il parametro appartiene e viene inizializzato con il valore dell'argomento specificato nella chiamata. Un parametro di valore in genere cessa di esistere al momento della restituzione del membro di funzione o della funzione anonima. Tuttavia, se il parametro value viene acquisito mediante una funzione anonima ([espressioni di funzioni anonime](expressions.md#anonymous-function-expressions)), la durata si estende ad almeno fino a quando il delegato o è idoneo per albero delle espressioni creata da tale funzione anonima operazione di Garbage collection.

Ai fini di controllo dell'assegnazione definita, viene considerato un parametro di valore inizialmente assegnata.

### <a name="reference-parameters"></a>Parametri per riferimento

Un parametro dichiarato con un `ref` modificatore è una ***fa riferimento a parametro***.

Un parametro di riferimento non crea un nuovo percorso di archiviazione. Al contrario, un parametro di riferimento rappresenta la stessa posizione di archiviazione della variabile specificata come argomento nella chiamata alla funzione anonima o membro di funzione. Di conseguenza, il valore di un parametro di riferimento è sempre lo stesso come la variabile sottostante.

Le regole di assegnazione certa seguenti si applicano ai parametri di riferimento. Tenere presenti le regole diverse per i parametri di output descritto nella [parametri di Output](variables.md#output-parameters).

*  Una variabile deve essere assegnata definitivamente ([assegnazione certa](variables.md#definite-assignment)) prima che può essere passata come parametro di riferimento in una chiamata alla funzione membro o un delegato.
*  All'interno di un membro di funzione o di una funzione anonima viene considerato come inizialmente assegnato un parametro di riferimento.

All'interno di un metodo di istanza o una funzione di accesso di istanza di un tipo struct, il `this` parola chiave si comporta esattamente come un parametro di riferimento del tipo di struttura ([questo accesso](expressions.md#this-access)).

### <a name="output-parameters"></a>Parametri di output

Un parametro dichiarato con un `out` modificatore è un ***parametro di output***.

Un parametro di output non crea un nuovo percorso di archiviazione. Al contrario, un parametro di output rappresenta la stessa posizione di archiviazione della variabile specificata come argomento nella chiamata di funzione membro o un delegato. Di conseguenza, il valore di un parametro di output è sempre lo stesso come la variabile sottostante.

Le regole di assegnazione certa seguenti si applicano ai parametri di output. Le regole per i parametri per riferimento descritto in [facciano riferimento ai parametri](variables.md#reference-parameters).

*  Una variabile necessario non essere assegnata prima che può essere passato come parametro di output in un membro di funzione o chiamata a un delegato.
*  Seguendo il normale completamento di una chiamata alla funzione membro o un delegato, ogni variabile passata come parametro di output è considerata assegnato in tale percorso di esecuzione.
*  All'interno di un membro di funzione o una funzione anonima, un parametro di output viene considerato assegnato inizialmente.
*  Ogni parametro di output di un membro di funzione o una funzione anonima deve essere assolutamente assegnato ([assegnazione certa](variables.md#definite-assignment)) prima che la funzione membro o una funzione anonima restituisce normalmente.

All'interno di un costruttore di istanza di un tipo struct, il `this` parola chiave si comporta esattamente come un parametro di output del tipo di struttura ([questo accesso](expressions.md#this-access)).

### <a name="local-variables"></a>Variabili locali

Oggetto ***variabile locale*** dichiarato da una *local_variable_declaration*, che possono verificarsi in un *blocco*, un *for_statement*, un *switch_statement* o un *using_statement*; o un' *foreach_statement* o una *specific_catch_clause* per un *try_statement*.

La durata di una variabile locale è la parte dell'esecuzione del programma durante il quale archiviazione è garantita a essere riservato anche per tale. Questa durata si estende ad almeno da una voce nel *block*, *for_statement*, *switch_statement*, *using_statement*, *foreach_statement*, o *specific_catch_clause* a cui è associato, fino all'esecuzione di tale *blocco*, *for_statement*, *switch_statement*, *using_statement*, *foreach_statement*, o *specific_catch_clause* estremità in alcun modo. (Immettere un racchiuso *blocco* o chiamare un metodo sospende, ma non termina, l'esecuzione dell'oggetto corrente *blocco*, *for_statement*, *switch_statement* , *using_statement*, *foreach_statement*, oppure *specific_catch_clause*.) Se la variabile locale viene acquisita mediante una funzione anonima ([acquisire variabili esterne](expressions.md#captured-outer-variables)), la durata si estende ad almeno finché la struttura ad albero di delegato o un'espressione creata dalla funzione anonima, insieme a eventuali altri oggetti che provengono da Fare riferimento alla variabile acquisita, sono idoneo per operazioni di garbage collection.

Se l'elemento padre *block*, *for_statement*, *switch_statement*, *using_statement*, *foreach_statement*, oppure *specific_catch_clause* immesso in modo ricorsivo, viene creata ogni volta che, una nuova istanza della variabile locale e la relativa *local_variable_initializer*, se presente, viene valutato ogni volta.

Una variabile locale dovuta a un *local_variable_declaration* non viene inizializzato automaticamente e pertanto non ha alcun valore predefinito. Ai fini di controllo dell'assegnazione definita, una variabile locale dovuti a un *local_variable_declaration* viene considerato assegnate inizialmente. Oggetto *local_variable_declaration* può includere una *local_variable_initializer*, nel qual caso la variabile viene considerata assegnata solo dopo l'espressione di inizializzazione ([ Le istruzioni di dichiarazione](variables.md#declaration-statements)).

All'interno dell'ambito di una variabile locale dovuta a un *local_variable_declaration*, tratta di un errore in fase di compilazione per fare riferimento alla variabile locale in una posizione di testo che precede il *local_variable_declarator*. Se la dichiarazione di variabile locale è implicita ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)), è anche possibile fare riferimento alla variabile all'interno di relativo *local_variable_declarator*.

Una variabile locale dovuta a un *foreach_statement* o una *specific_catch_clause* viene considerata assegnata nel relativo ambito.

La durata effettiva di una variabile locale è dipendente dall'implementazione. Ad esempio, un compilatore in modo statico potrebbe determinare che una variabile locale in un blocco viene utilizzata solo per una piccola parte di tale blocco. Tramite questa analisi, il compilatore di generare codice che prevede una durata più breve rispetto al relativo blocco contiene memorizzazione della variabile.

Lo spazio di archiviazione a cui fa riferimento una variabile di riferimento locale viene recuperata indipendentemente dalla durata di tale variabile di riferimento locale ([gestione automatica della memoria](basic-concepts.md#automatic-memory-management)).

## <a name="default-values"></a>Valori predefiniti

Le seguenti categorie di variabili vengono inizializzate automaticamente i valori predefiniti:

*  Variabili statiche.
*  Variabili di istanza delle istanze della classe.
*  Elementi della matrice.

Il valore predefinito di una variabile dipende dal tipo della variabile e viene determinato come segue:

*  Per una variabile di un *value_type*, il valore predefinito è lo stesso come il valore calcolato per il *value_type*del costruttore predefinito ([costruttori predefiniti](types.md#default-constructors)).
*  Per una variabile di un *reference_type*, il valore predefinito è `null`.

L'inizializzazione con i valori predefiniti è in genere eseguita quando il gestore della memoria o di garbage collection inizializzare la memoria per tutti i bit a zero prima viene allocato per l'uso. Per questo motivo, è consigliabile usare tutti i bit a zero per rappresentare il riferimento null.

## <a name="definite-assignment"></a>Assegnazione definita

In una posizione specificata nel codice eseguibile di un membro di funzione, una variabile viene detto ***assegnata in modo definitivo*** se il compilatore può dimostrare, mediante l'analisi del flusso statico ([regole Precise per determinare il assegnazione](variables.md#precise-rules-for-determining-definite-assignment)), che la variabile è stata inizializzata automaticamente o è stato la destinazione di almeno un'assegnazione. In termini generali, le regole di assegnazione certa sono:

*  Una variabile inizialmente assegnata ([inizialmente assegnata variabili](variables.md#initially-assigned-variables)) viene sempre considerata assegnata.
*  Una variabile non inizialmente assegnata ([inizialmente senza assegnazione variabili](variables.md#initially-unassigned-variables)) viene considerata assegnata a una posizione specificata se tutti i possibili percorsi di esecuzione di tale posizione contengono almeno uno dei seguenti:
    * Un'assegnazione semplice ([assegnamento semplice](expressions.md#simple-assignment)) in cui la variabile è il secondo operando.
    * Un'espressione di chiamata ([espressioni di chiamata](expressions.md#invocation-expressions)) o un'espressione di creazione di oggetti ([espressioni per la creazione dell'oggetto](expressions.md#object-creation-expressions)) che passa la variabile come parametro di output.
    * Per una variabile locale, una dichiarazione di variabile locale ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)) che include un inizializzatore di variabile.

La specifica formale di base delle regole informale sopra descritto nella [variabili assegnate inizialmente](variables.md#initially-assigned-variables), [inizialmente senza assegnazione variabili](variables.md#initially-unassigned-variables), e [regole Precise per la determinazione assegnazione certa](variables.md#precise-rules-for-determining-definite-assignment).

Gli stati di assegnazione certa delle variabili di istanza di un *struct_type* variabile viene tenuta traccia singolarmente che collettivamente. Oltre alle regole precedenti, le regole seguenti sono valide per *struct_type* variabili e le relative variabili di istanza:

*  Se una variabile di istanza viene considerata assegnata che lo contiene *struct_type* viene considerata come variabile assegnata.
*  Oggetto *struct_type* variabile è considerata assegnata se ognuna delle relative variabili di istanza viene considerata assegnata.

Assegnazione certa è un requisito nei contesti seguenti:

*  Una variabile deve essere assegnata in modo univoco ogni posizione in cui viene ottenuto il valore. Ciò garantisce che i valori non definiti mai verificano. Per ottenere il valore della variabile, tranne quando viene considerata l'occorrenza di una variabile in un'espressione
    * la variabile è il secondo operando di un'assegnazione semplice,
    * la variabile viene passata come parametro di output, o
    * la variabile è una *struct_type* variabile e viene generato come operando di sinistra di un accesso ai membri.
*  Una variabile deve essere assegnata in modo univoco ogni posizione in cui viene passato come parametro di riferimento. Ciò garantisce che il membro di funzione da richiamare possibile prendere in considerazione il parametro di riferimento inizialmente assegnato.
*  Tutti i parametri di output di un membro di funzione devono essere assolutamente assegnati a ogni posizione in cui viene restituito il membro di funzione (tramite un `return` istruzione o quando l'esecuzione raggiunge la fine del corpo funzione membro). Ciò garantisce che i membri di funzione non restituiscono valori non definiti nei parametri di output, rendendo il compilatore consideri una chiamata alla funzione membro che accetta una variabile come parametro di output equivalente a un'assegnazione alla variabile.
*  Il `this` variabile di un *struct_type* costruttore di istanza deve essere assegnata in ogni posizione in cui viene restituito tale costruttore di istanza.

### <a name="initially-assigned-variables"></a>Variabili inizialmente assegnate

Le categorie di variabili seguenti sono classificate come inizialmente assegnata:

*  Variabili statiche.
*  Variabili di istanza delle istanze della classe.
*  Variabili di istanza di struct inizialmente assegnato variabili.
*  Elementi della matrice.
*  Parametri del valore.
*  Parametri per riferimento.
*  Le variabili dichiarate un `catch` clausola o `foreach` istruzione.

### <a name="initially-unassigned-variables"></a>Variabili non assegnate inizialmente

Le categorie di variabili seguenti sono classificate come non assegnate inizialmente:

*  Variabili di istanza delle variabili assegnate inizialmente struct.
*  Parametri di output, inclusa la `this` variabile dei costruttori di istanza struct.
*  Le variabili locali, ad eccezione di quelli dichiarati in un `catch` clausola o `foreach` istruzione.

### <a name="precise-rules-for-determining-definite-assignment"></a>Regole precise per la determinazione di assegnazione definita

Per determinare che tutte le variabili utilizzate sono assegnata, il compilatore deve usare un processo che è equivalente a quella descritta in questa sezione.

Il compilatore elabora il corpo di ogni membro di funzione che dispone di uno o più variabili assegnate inizialmente. Per ogni variabile assegnate inizialmente *v*, il compilatore determina una ***stato assegnazione certa*** per *v* ogni quanto riportato di seguito nel membro di funzione:

*  All'inizio di ogni istruzione
*  In corrispondenza del punto di fine ([endpoint e raggiungibilità](statements.md#end-points-and-reachability)) di ogni istruzione
*  In ogni arco che trasferisce il controllo a un'altra istruzione o al punto finale di un'istruzione
*  All'inizio di ogni espressione
*  Alla fine di ogni espressione

Lo stato di assegnazione certa *v* può essere:

*  Assegnata in modo definitivo. Ciò indica che in tutti i flussi di controllo possibili a questo punto *v* è stato assegnato un valore.
*  Non sarà assegnato. Per lo stato di una variabile alla fine di un'espressione di tipo `bool`, lo stato di una variabile che non viene assegnata in modo definitivo maggio, ma non necessariamente, rientrano in uno degli stati secondari seguenti:
    * Assegnata dopo un'espressione. Questo stato indica che *v* viene assegnata in modo definitivo se l'espressione booleana valutata come true, ma non viene necessariamente assegnato se l'espressione booleana valutata come falsa.
    * Assegnata dopo un'espressione. Questo stato indica che *v* viene assegnata in modo definitivo se l'espressione booleana valutata come falsa, ma non viene necessariamente assegnato se l'espressione booleana valutata come vera.

Le regole seguenti determinano il modo in stato di una variabile *v* viene determinato in ogni posizione.

#### <a name="general-rules-for-statements"></a>Regole generali per le istruzioni

*  *v* non viene assolutamente assegnata all'inizio del corpo di un membro di funzione.
*  *v* assegnata all'inizio di qualsiasi istruzione non raggiungibile.
*  Lo stato di assegnazione certa *v* all'inizio di qualsiasi altra istruzione è determinata controllando lo stato di assegnazione certa del *v* su tutti i trasferimenti di flusso di controllo che usano l'inizio di tale istruzione. Se (e solo se) *v* assegnata in tutti i trasferimenti del flusso di controllo, quindi *v* assegnata all'inizio dell'istruzione. Il set di trasferimenti del flusso di controllo possibili è determinato esattamente come per il controllo della raggiungibilità di istruzione ([endpoint e raggiungibilità](statements.md#end-points-and-reachability)).
*  Lo stato di assegnazione certa *v* in corrispondenza del punto finale di un blocco `checked`, `unchecked`, `if`, `while`, `do`, `for`, `foreach`, `lock`, `using`, oppure `switch` istruzione è determinata controllando lo stato di assegnazione certa del *v* su tutti i trasferimenti di flusso di controllo che il punto finale dell'istruzione di destinazione. Se *v* assegnata in tutti i trasferimenti del flusso di controllo, quindi *v* assegnata in corrispondenza del punto di fine dell'istruzione. In caso contrario, *v* non viene assolutamente assegnata in corrispondenza del punto di fine dell'istruzione. Il set di trasferimenti del flusso di controllo possibili è determinato esattamente come per il controllo della raggiungibilità di istruzione ([endpoint e raggiungibilità](statements.md#end-points-and-reachability)).

#### <a name="block-statements-checked-and-unchecked-statements"></a>Istruzioni di blocco, opzione è selezionate e istruzioni deselezionate

Lo stato di assegnazione certa *v* sul controllo trasferimento alla prima istruzione dell'elenco di istruzioni nel blocco (o al punto di fine del blocco, se l'istruzione di elenco è vuota) è quello utilizzato per l'istruzione di assegnazione certa del *v* prima del blocco `checked`, o `unchecked` istruzione.

#### <a name="expression-statements"></a>Istruzioni di espressione

Per un'istruzione di espressione *stmt* che contiene l'espressione *expr*:

*  *v* con lo stesso stato di assegnazione definite all'inizio del *expr* come all'inizio del *stmt*.
*  Se *v* se assegnata alla fine del *expr*, sarà assegnata in corrispondenza del punto di fine del *stmt*; in caso contrario, non viene assolutamente assegnata in corrispondenza del punto finale della *stmt*.

#### <a name="declaration-statements"></a>Istruzioni di dichiarazione

*  Se *stmt* è un'istruzione di dichiarazione senza inizializzatori, quindi *v* con lo stesso stato di assegnazione definite in corrispondenza del punto di fine del *stmt* come all'inizio del *stmt*.
*  Se *stmt* è lo stato di assegnazione definite per un'istruzione di dichiarazione con inizializzatori, quindi *v* viene determinato come se *stmt* fosse un elenco di istruzioni, con un'assegnazione istruzione per ogni dichiarazione con un inizializzatore (nell'ordine di dichiarazione).

#### <a name="if-statements"></a>Se le istruzioni

Per un `if` istruzione *stmt* del form:
```csharp
if ( expr ) then_stmt else else_stmt
```

*  *v* con lo stesso stato di assegnazione definite all'inizio del *expr* come all'inizio del *stmt*.
*  Se *v* è assegnata alla fine del *expr*, quindi sarà assegnata al trasferimento del controllo per *then_stmt* e a una delle due *else_stmt*  o per l'endpoint del *stmt* se non è presente alcuna clausola else.
*  Se *v* ha lo stato "assegnato in precedenza dopo un'espressione true" in fondo *expr*, quindi sarà assegnata al trasferimento del controllo per *then_stmt*e non assegnata al trasferimento del controllo a una delle due *else_stmt* o per l'endpoint di *stmt* se non è presente alcuna clausola else.
*  Se *v* ha lo stato "assegnato in precedenza dopo l'espressione false" alla fine del *expr*, quindi sarà assegnata al trasferimento del controllo per *else_stmt*e non assegnata in modo definitivo il trasferimento del controllo al *then_stmt*. Sarà assegnata nel punto finale *stmt* solo se è assegnata al punto finale del *then_stmt*.
*  In caso contrario, *v* viene considerata non assegnata al trasferimento del controllo a uno il *then_stmt* oppure *else_stmt*, o per l'endpoint di  *stmt* se non è presente alcuna clausola else.

#### <a name="switch-statements"></a>Le istruzioni switch

In un `switch` istruzione *stmt* con un'espressione di controllo *expr*:

*  Lo stato di assegnazione certa del *v* all'inizio del *expr* equivale allo stato del *v* all'inizio del *stmt*.
*  Lo stato di assegnazione certa *v* nel flusso di controllo del trasferimento a un elenco di istruzioni di blocco switch raggiungibile equivale allo stato di assegnazione certa del *v* alla fine del *expr*.

#### <a name="while-statements"></a>Mentre le istruzioni

Per un `while` istruzione *stmt* del form:
```csharp
while ( expr ) while_body
```

*  *v* con lo stesso stato di assegnazione definite all'inizio del *expr* come all'inizio del *stmt*.
*  Se *v* è assegnata alla fine del *expr*, quindi sarà assegnata al trasferimento del controllo per *while_body* e per il punto finale di  *stmt*.
*  Se *v* ha lo stato "assegnato in precedenza dopo un'espressione true" in fondo *expr*, quindi sarà assegnata al trasferimento del controllo per *while_body*, ma non l'endpoint di sia assegnata *stmt*.
*  Se *v* ha lo stato "assegnato in precedenza dopo l'espressione false" alla fine del *expr*, quindi sarà assegnata al trasferimento del controllo al punto di fine del *stmt* , ma non assegnata in modo al trasferimento del controllo *while_body*.

#### <a name="do-statements"></a>Le istruzioni

Per un `do` istruzione *stmt* del form:
```csharp
do do_body while ( expr ) ;
```

*  *v* con lo stesso stato di assegnazione certa il trasferimento del controllo dall'inizio del *stmt* al *do_body* come all'inizio del *stmt*.
*  *v* con lo stesso stato di assegnazione definite all'inizio del *expr* come corrispondente all'endpoint del *do_body*.
*  Se *v* è assegnata alla fine del *expr*, quindi sarà assegnata al trasferimento del controllo al punto di fine del *stmt*.
*  Se *v* ha lo stato "assegnato in precedenza dopo l'espressione false" alla fine del *expr*, quindi sarà assegnata al trasferimento del controllo al punto di fine del *stmt* .

#### <a name="for-statements"></a>Per le istruzioni

Assegnazione certa cercano un `for` istruzione del form:
```csharp
for ( for_initializer ; for_condition ; for_iterator ) embedded_statement
```
l'operazione viene eseguita come se fosse scritta l'istruzione:
```csharp
{
for_initializer ;
while ( for_condition ) {
embedded_statement ;
for_iterator ;
}
}
```

Se il *for_condition* viene omesso dal `for` istruzione e quindi la valutazione dell'assegnazione certa prosegue come se *for_condition* sono stati sostituiti con `true` nell'espansione precedenza .

#### <a name="break-continue-and-goto-statements"></a>Interrompere, continuare a e le istruzioni goto

Lo stato di assegnazione certa del *v* trasferimento del controllo dovuta a un `break`, `continue`, o `goto` istruzione è uguale allo stato di assegnazione certa di *v* nel inizio dell'istruzione.

#### <a name="throw-statements"></a>Istruzioni throw

Per un'istruzione *stmt* del form
```csharp
throw expr ;
```

Lo stato di assegnazione certa del *v* all'inizio del *expr* equivale allo stato di assegnazione certa del *v* all'inizio del *stmt*.

#### <a name="return-statements"></a>Istruzione return

Per un'istruzione *stmt* del form
```csharp
return expr ;
```

*  Lo stato di assegnazione certa del *v* all'inizio del *expr* equivale allo stato di assegnazione certa del *v* all'inizio del *stmt*.
*  Se *v* è un parametro di output, quindi si deve assolutamente assegnato:
    * Dopo aver *expr*
    * o alla fine del `finally` block di una `try` - `finally` o `try` - `catch` - `finally` che racchiude il `return` istruzione.

Per un'istruzione stmt del form:
```csharp
return ;
```

*  Se *v* è un parametro di output, quindi si deve assolutamente assegnato:
    * prima di *stmt*
    * o alla fine del `finally` block di una `try` - `finally` o `try` - `catch` - `finally` che racchiude il `return` istruzione.

#### <a name="try-catch-statements"></a>Istruzioni try-catch

Per un'istruzione *stmt* del form:
```csharp
try try_block
catch(...) catch_block_1
...
catch(...) catch_block_n
```

*  Lo stato di assegnazione certa del *v* all'inizio del *try_block* equivale allo stato di assegnazione certa del *v* all'inizio del *stmt*.
*  Lo stato di assegnazione certa del *v* all'inizio del *catch_block_i* (per qualsiasi *ho*) è uguale allo stato di assegnazione certa di *v*all'inizio di *stmt*.
*  Lo stato di assegnazione certa del *v* nel punto finale *stmt* è assegnata se (e solo se) *v* sarà assegnato al punto finale di  *try_block* e ogni *catch_block_i* (per ogni *posso* da 1 a *n*).

#### <a name="try-finally-statements"></a>Istruzioni try-finally

Per un `try` istruzione *stmt* del form:
```csharp
try try_block finally finally_block
```

*  Lo stato di assegnazione certa del *v* all'inizio del *try_block* equivale allo stato di assegnazione certa del *v* all'inizio del *stmt*.
*  Lo stato di assegnazione certa del *v* all'inizio del *finally_block* equivale allo stato di assegnazione certa del *v* all'inizio del *stmt* .
*  Lo stato di assegnazione certa del *v* nel punto finale *stmt* viene assegnata se (e solo se) viene soddisfatta almeno una delle operazioni seguenti:
    * *v* assegnata al punto finale di *try_block*
    * *v* assegnata al punto finale di *finally_block*

Se un trasferimento di flusso di controllo (ad esempio, un `goto` istruzione) viene eseguita che inizia all'interno *try_block*e termina all'esterno di *try_block*, quindi *v* anche considerata assegnata tale trasferimento del controllo qualora *v* assegnata al punto finale del *finally_block*. (Non si tratta di un solo se: se *v* assegnata per un altro motivo questo trasferimento del controllo, quindi viene comunque considerato assegnata.)

#### <a name="try-catch-finally-statements"></a>Istruzioni try-catch-finally

Analisi di assegnazione definite per un `try` - `catch` - `finally` istruzione del form:
```csharp
try try_block
catch(...) catch_block_1
...
catch(...) catch_block_n
finally *finally_block*
```
viene eseguita come se si trattasse di un `try` - `finally` istruzione che racchiude un `try` - `catch` istruzione:
```csharp
try {
try try_block
catch(...) catch_block_1
...
catch(...) catch_block_n
}
finally finally_block
```

Nell'esempio seguente viene illustrato come i diversi blocchi di un `try` istruzione ([l'istruzione try](statements.md#the-try-statement)) sull'assegnazione definita.
```csharp
class A
{
static void F() {
int i, j;
try {
goto LABEL;
// neither i nor j definitely assigned
i = 1;
// i definitely assigned
}

catch {
// neither i nor j definitely assigned
i = 3;
// i definitely assigned
}

finally {
// neither i nor j definitely assigned
j = 5;
// j definitely assigned
}
// i and j definitely assigned
LABEL:;
// j definitely assigned

}
}
```

#### <a name="foreach-statements"></a>Istruzioni di foreach

Per un `foreach` istruzione *stmt* del form:
```csharp
foreach ( type identifier in expr ) embedded_statement
```

*  Lo stato di assegnazione certa del *v* all'inizio del *expr* equivale allo stato del *v* all'inizio del *stmt*.
*  Lo stato di assegnazione certa del *v* trasferimento del controllo a *embedded_statement* o per il punto finale di *stmt* equivale allo stato di *v* in fondo *expr*.

#### <a name="using-statements"></a>Utilizzo di istruzioni

Per un `using` istruzione *stmt* del form:
```csharp
using ( resource_acquisition ) embedded_statement
```

*  Lo stato di assegnazione certa del *v* all'inizio del *resource_acquisition* equivale allo stato del *v* all'inizio del *stmt*.
*  Lo stato di assegnazione certa del *v* sul trasferimento del controllo a *embedded_statement* equivale allo stato del *v* alla fine di *resource_ acquisizione*.

#### <a name="lock-statements"></a>Istruzioni di blocco

Per un `lock` istruzione *stmt* del form:
```csharp
lock ( expr ) embedded_statement
```

*  Lo stato di assegnazione certa del *v* all'inizio del *expr* equivale allo stato del *v* all'inizio del *stmt*.
*  Lo stato di assegnazione certa del *v* sul trasferimento del controllo a *embedded_statement* equivale allo stato del *v* alla fine di *expr*.

#### <a name="yield-statements"></a>Istruzioni yield

Per un `yield return` istruzione *stmt* del form:
```csharp
yield return expr ;
```

*  Lo stato di assegnazione certa del *v* all'inizio del *expr* equivale allo stato del *v* all'inizio del *stmt*.
*  Lo stato di assegnazione certa del *v* alla fine del *stmt* equivale allo stato del *v* alla fine del *expr*.
*  Oggetto `yield break` istruzione non ha alcun effetto sullo stato di assegnazione definite.

#### <a name="general-rules-for-simple-expressions"></a>Regole generali per le espressioni semplici

La regola seguente si applica a questi tipi di espressioni: i valori letterali ([valori letterali](expressions.md#literals)), nomi semplici ([nomi semplici](expressions.md#simple-names)), espressioni di accesso membri ([accesso ai membri](expressions.md#member-access)), le espressioni di accesso di base non indicizzata ([l'accesso di Base](expressions.md#base-access)), `typeof` espressioni ([l'operatore typeof](expressions.md#the-typeof-operator)), espressioni con valore predefinito ([espressioni con valore predefinito ](expressions.md#default-value-expressions)) e `nameof` espressioni ([espressioni Nameof](expressions.md#nameof-expressions)).

*  Lo stato di assegnazione certa *v* alla fine di tale espressione è uguale allo stato di assegnazione certa di *v* all'inizio dell'espressione.

#### <a name="general-rules-for-expressions-with-embedded-expressions"></a>Regole generali per le espressioni con espressioni incorporate

Le regole seguenti si applicano a questi tipi di espressioni: espressioni tra parentesi ([espressioni tra parentesi](expressions.md#parenthesized-expressions)), le espressioni di accesso di elementi ([l'accesso all'elemento](expressions.md#element-access)) di base con espressioni di accesso l'indicizzazione ([l'accesso di Base](expressions.md#base-access)), incrementare e decrementare espressioni ([forma suffissa di incremento e decremento (operatori)](expressions.md#postfix-increment-and-decrement-operators), [incremento prefisso e decremento (operatori)](expressions.md#prefix-increment-and-decrement-operators)), le espressioni cast ([le espressioni Cast](expressions.md#cast-expressions)), unario `+`, `-`, `~`, `*` espressioni binarie `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `<`, `<=`, `>`, `>=`, `==`, `!=`, `is`, `as`, `&`, `|`, `^` espressioni ([operatori aritmetici](expressions.md#arithmetic-operators), [operatori Shift](expressions.md#shift-operators), [relazionali e operatori di test del tipo](expressions.md#relational-and-type-testing-operators) [Gli operatori logici](expressions.md#logical-operators)), le espressioni di assegnazione composta ([assegnazione composta](expressions.md#compound-assignment)), `checked` e `unchecked` espressioni ([il checked e unchecked gli operatori](expressions.md#the-checked-and-unchecked-operators)), oltre a espressioni di creazione della matrice e un delegato ([il nuovo operatore](expressions.md#the-new-operator)).

Ognuna di queste espressioni ha uno o più sottoespressioni che vengono valutate in modo non condizionale in un ordine prestabilito. Ad esempio, il file binario `%` operatore valuta il lato sinistro dell'operatore, quindi il lato destro. Un'operazione di indicizzazione valuta l'espressione indicizzata e valuta quindi ognuna delle espressioni di indice, in ordine da sinistra a destra. Per un'espressione *expr*, che ha le sottoespressioni *e1, e2,..., eN*, valutati nell'ordine indicato:

*  Lo stato di assegnazione certa *v* all'inizio del *e1* equivale allo stato di assegnazione definite all'inizio del *expr*.
*  Lo stato di assegnazione certa del *v* all'inizio del *ei* (*ho* maggiore di uno) è uguale allo stato di assegnazione definita alla fine della sottoespressione precedente.
*  Lo stato di assegnazione certa del *v* alla fine del *expr* equivale allo stato di assegnazione definita alla fine di *eN*

#### <a name="invocation-expressions-and-object-creation-expressions"></a>Espressioni di chiamata e le espressioni di creazione di oggetti

Per un'espressione di chiamata *expr* del form:
```csharp
primary_expression ( arg1 , arg2 , ... , argN )
```
o un'espressione di creazione oggetto del form:
```csharp
new type ( arg1 , arg2 , ... , argN )
```

*  Per un'espressione di chiamata, lo stato di assegnazione certa del *v* prima *primary_expression* equivale allo stato del *v* prima *expr*.
*  Per un'espressione di chiamata, lo stato di assegnazione certa del *v* prima *arg1* equivale allo stato del *v* dopo *primary_expression*.
*  Per un'espressione di creazione di oggetti, lo stato di assegnazione certa del *v* prima *arg1* equivale allo stato del *v* prima *expr*.
*  Per ogni argomento *argi*, lo stato di assegnazione certa *v* dopo *argi* è determinato dalle regole di espressione normale, ignorando qualsiasi `ref` o `out`modificatori.
*  Per ogni argomento *argi* per qualsiasi *ho* maggiore di uno, lo stato di assegnazione certa del *v* prima *argi* è quello utilizzato per lo stato di *v* dopo la precedente *arg*.
*  Se la variabile *v* viene passato come un `out` argomento (ad esempio, un argomento del form `out v`) in una qualsiasi di argomenti, quindi lo stato del *v* dopo *expr* assegnata. In caso contrario, lo stato del *v* dopo *expr* equivale allo stato del *v* dopo *argN*.
*  Per gli inizializzatori di matrice ([espressioni di creazione matrice](expressions.md#array-creation-expressions)), inizializzatori di oggetto ([inizializzatori di oggetto](expressions.md#object-initializers)), gli inizializzatori di insieme ([gli inizializzatori di insieme](expressions.md#collection-initializers)) e gli inizializzatori di oggetto anonimi ([espressioni per la creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions)), lo stato di assegnazione certa è determinato dall'espansione di questi costrutti definiti in termini di.

#### <a name="simple-assignment-expressions"></a>Espressioni di assegnazione semplice

Per un'espressione *expr* del form `w = expr_rhs`:

*  Lo stato di assegnazione certa del *v* prima *expr_rhs* equivale allo stato di assegnazione certa del *v* prima *expr*.
*  Lo stato di assegnazione certa *v* dopo *expr* è determinato dal:
   * Se *w* coincide con la variabile *v*, quindi lo stato di assegnazione certa dei *v* dopo *expr* viene assegnata in modo definitivo.
   * In caso contrario, se l'assegnazione avviene all'interno del costruttore di istanza di un tipo struct, se *w* è un accesso a proprietà che definisce una proprietà implementata automaticamente *P* nell'istanza in fase di costruzione e *v* è il campo nascosto sottostante della *P*, quindi lo stato di assegnazione certa *v* dopo *expr* è senz'altro assegnato.
   * In caso contrario, lo stato di assegnazione certa del *v* dopo *expr* equivale allo stato di assegnazione certa del *v* dopo *expr_rhs*.

#### <a name="-conditional-and-expressions"></a>& & (AND condizionale) espressioni

Per un'espressione *expr* del form `expr_first && expr_second`:

*  Lo stato di assegnazione certa del *v* prima *expr_first* equivale allo stato di assegnazione certa del *v* prima *expr*.
*  Lo stato di assegnazione certa del *v* prima *expr_second* viene assegnata in modo definitivo se lo stato del *v* dopo *expr_first* è assegnazione definitiva o "assegnata dopo un'espressione true". In caso contrario, sarà non assegnata.
*  Lo stato di assegnazione certa *v* dopo *expr* è determinato dal:
    * Se *expr_first* è un'espressione costante con il valore `false`, quindi lo stato di assegnazione certa *v* dopo *expr* è quello utilizzato per l'assegnazione definita stato del *v* dopo *expr_first*.
    * In caso contrario, se lo stato del *v* dopo *expr_first* è assegnata, quindi lo stato del *v* dopo *expr* viene assegnata in modo definitivo.
    * In caso contrario, se lo stato del *v* dopo *expr_second* è assegnata e lo stato del *v* dopo *expr_first* è "senz'altro assegnato dopo un'espressione false", quindi lo stato del *v* dopo *expr* viene assegnata in modo definitivo.
    * In caso contrario, se lo stato del *v* dopo *expr_second* assegnata o "assegnata dopo un'espressione true", quindi lo stato del *v* dopo  *Expr* "sarà assegnato dopo un'espressione true".
    * In caso contrario, se lo stato del *v* dopo *expr_first* sia "assegnata dopo l'espressione false" e lo stato del *v* dopo *expr_second* è "assegnata dopo l'espressione false", quindi lo stato del *v* dopo *expr* "sarà assegnato dopo l'espressione false".
    * In caso contrario, lo stato del *v* dopo *expr* non viene assolutamente assegnata.

Nell'esempio
```csharp
class A
{
    static void F(int x, int y) {
        int i;
        if (x >= 0 && (i = y) >= 0) {
            // i definitely assigned
        }
        else {
            // i not definitely assigned
        }
        // i not definitely assigned
    }
}
```
la variabile `i` viene considerata assegnata in una delle istruzioni incorporate di un' `if` istruzione ma non in altro. Nel `if` istruzione nel metodo `F`, la variabile `i` è assegnata la prima istruzione incorporata in quanto l'esecuzione dell'espressione `(i = y)` precede sempre l'esecuzione di questa istruzione incorporata. Al contrario, la variabile `i` non viene assolutamente assegnata nella seconda istruzione incorporata, allora `x >= 0` potrebbe risultare falsa, risultante nella variabile `i` non assegnate.

#### <a name="-conditional-or-expressions"></a>|| (OR condizionale) espressioni

Per un'espressione *expr* del form `expr_first || expr_second`:

*  Lo stato di assegnazione certa del *v* prima *expr_first* equivale allo stato di assegnazione certa del *v* prima *expr*.
*  Lo stato di assegnazione certa del *v* prima *expr_second* viene assegnata in modo definitivo se lo stato del *v* dopo *expr_first* è assegnazione definitiva o "assegnata dopo l'espressione false". In caso contrario, sarà non assegnata.
*  L'istruzione di assegnazione certa della *v* dopo *expr* è determinata da:
    * Se *expr_first* è un'espressione costante con il valore `true`, quindi lo stato di assegnazione certa *v* dopo *expr* è quello utilizzato per l'assegnazione definita stato del *v* dopo *expr_first*.
    * In caso contrario, se lo stato del *v* dopo *expr_first* è assegnata, quindi lo stato del *v* dopo *expr* viene assegnata in modo definitivo.
    * In caso contrario, se lo stato del *v* dopo *expr_second* è assegnata e lo stato del *v* dopo *expr_first* è "senz'altro assegnato dopo un'espressione true", quindi lo stato del *v* dopo *expr* viene assegnata in modo definitivo.
    * In caso contrario, se lo stato del *v* dopo *expr_second* assegnata o "assegnata dopo l'espressione false", quindi lo stato del *v* dopo *expr* "sarà assegnato dopo l'espressione false".
    * In caso contrario, se lo stato del *v* dopo *expr_first* sia "assegnata dopo un'espressione true" e lo stato del *v* dopo *expr_second*è "assegnata dopo un'espressione true", quindi lo stato dei *v* dopo *expr* "sarà assegnato dopo un'espressione true".
    * In caso contrario, lo stato del *v* dopo *expr* non viene assolutamente assegnata.

Nell'esempio
```csharp
class A
{
    static void G(int x, int y) {
        int i;
        if (x >= 0 || (i = y) >= 0) {
            // i not definitely assigned
        }
        else {
            // i definitely assigned
        }
        // i not definitely assigned
    }
}
```
la variabile `i` viene considerata assegnata in una delle istruzioni incorporate di un' `if` istruzione ma non in altro. Nel `if` istruzione nel metodo `G`, la variabile `i` è assegnata la seconda istruzione incorporata in quanto l'esecuzione dell'espressione `(i = y)` precede sempre l'esecuzione di questa istruzione incorporata. Al contrario, la variabile `i` non viene assolutamente assegnata nella prima istruzione incorporata, allora `x >= 0` potrebbe risultare true, risultante nella variabile `i` non assegnate.

#### <a name="-logical-negation-expressions"></a>! espressioni (negazione logica)

Per un'espressione *expr* del form `! expr_operand`:

*  Lo stato di assegnazione certa del *v* prima *expr_operand* equivale allo stato di assegnazione certa del *v* prima *expr*.
*  Lo stato di assegnazione certa *v* dopo *expr* è determinato dal:
    * Se lo stato del *v* dopo * expr_operand * è assegnata, quindi lo stato del *v* dopo *expr* viene assegnata in modo definitivo.
    * Se lo stato del *v* dopo * expr_operand * non sarà assegnato, quindi lo stato del *v* dopo *expr* non viene assolutamente assegnata.
    * Se lo stato del *v* dopo * expr_operand * è "assegnata dopo l'espressione false", quindi lo stato del *v* dopo *expr* "assegnata dopo true espressione".
    * Se lo stato del *v* dopo * expr_operand * è "assegnata dopo un'espressione true", quindi lo stato del *v* dopo *expr* "assegnata dopo false espressione".

#### <a name="-null-coalescing-expressions"></a>?? espressioni (null-coalescing)

Per un'espressione *expr* del form `expr_first ?? expr_second`:

*  Lo stato di assegnazione certa del *v* prima *expr_first* equivale allo stato di assegnazione certa del *v* prima *expr*.
*  Lo stato di assegnazione certa del *v* prima *expr_second* equivale allo stato di assegnazione certa del *v* dopo *expr_first*.
*  L'istruzione di assegnazione certa della *v* dopo *expr* è determinata da:
    * Se *expr_first* è un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)) con valore null, il lo stato del *v* dopo *expr* è lo stesso lo stato del *v* dopo *expr_second*.
*  In caso contrario, lo stato del *v* dopo *expr* equivale allo stato di assegnazione certa del *v* dopo *expr_first*.

#### <a name="-conditional-expressions"></a>?: (condizionale) espressioni

Per un'espressione *expr* del form `expr_cond ? expr_true : expr_false`:

*  Lo stato di assegnazione certa del *v* prima *expr_cond* equivale allo stato del *v* prima *expr*.
*  Lo stato di assegnazione certa *v* prima *expr_true* viene assegnata in modo definitivo se e solo se una delle seguenti condizioni:
    * *expr_cond* è un'espressione costante con il valore `false`
    * lo stato del *v* dopo *expr_cond* viene assegnata in modo definitivo o "assegnata in precedenza dopo un'espressione true".
*  Lo stato di assegnazione certa *v* prima *expr_false* viene assegnata in modo definitivo se e solo se una delle seguenti condizioni:
    * *expr_cond* è un'espressione costante con il valore `true`
*  lo stato del *v* dopo *expr_cond* viene assegnata in modo definitivo o "assegnata in precedenza dopo l'espressione false".
*  Lo stato di assegnazione certa *v* dopo *expr* è determinato dal:
    * Se *expr_cond* è un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)) con il valore `true` quindi lo stato del *v* dopo *expr* equivale allo stato del *v* dopo *expr_true*.
    * In caso contrario, se *expr_cond* è un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)) con il valore `false` quindi lo stato del *v* dopo *expr* equivale allo stato del *v* dopo *expr_false*.
    * In caso contrario, se lo stato del *v* dopo *expr_true* assegnata e lo stato del *v* dopo *expr_false* è senz'altro assegnato, quindi lo stato del *v* dopo *expr* viene assegnata in modo definitivo.
    * In caso contrario, lo stato del *v* dopo *expr* non viene assolutamente assegnata.

#### <a name="anonymous-functions"></a>Funzioni anonime

Per un *lambda_expression* oppure *anonymous_method_expression* *expr* con un corpo (entrambi *blocco* o *espressione* ) *corpo*:

*  Lo stato di assegnazione definite a una variabile esterna *v* prima *corpo* equivale allo stato del *v* prima *expr*. Lo stato di assegnazione certa delle variabili esterne, ovvero viene ereditato dal contesto della funzione anonima.
*  Lo stato di assegnazione definite a una variabile esterna *v* dopo *expr* equivale allo stato del *v* prima *expr*.

L'esempio
```csharp
delegate bool Filter(int i);

void F() {
    int max;

    // Error, max is not definitely assigned
    Filter f = (int n) => n < max;

    max = 5;
    DoWork(f);
}
```
Genera un errore in fase di compilazione poiché `max` non viene assolutamente assegnata in cui viene dichiarata la funzione anonima. L'esempio
```csharp
delegate void D();

void F() {
    int n;
    D d = () => { n = 1; };

    d();

    // Error, n is not definitely assigned
    Console.WriteLine(n);
}
```
viene generato un errore in fase di compilazione dopo l'assegnazione al `n` nella funzione anonima non ha alcun effetto sullo stato di assegnazione certa `n` all'esterno della funzione anonima.

## <a name="variable-references"></a>Riferimenti a variabili

Oggetto *variable_reference* è un *espressione* che viene classificato come una variabile. Oggetto *variable_reference* indica un percorso di archiviazione che è possibile accedere per ottenere il valore corrente e per archiviare un nuovo valore.

```antlr
variable_reference
    : expression
    ;
```

In C e C++, un *variable_reference* è noto come un' *lvalue*.

## <a name="atomicity-of-variable-references"></a>Atomicità dei riferimenti alle variabili

Letture e scritture dei seguenti tipi di dati sono atomiche: `bool`, `char`, `byte`, `sbyte`, `short`, `ushort`, `uint`, `int`, `float`e tipi di riferimento. Inoltre, le letture e scritture dei tipi di enumerazione con un tipo sottostante nell'elenco precedente anche sono atomiche. Letture e scritture di altri tipi, inclusi `long`, `ulong`, `double`, e `decimal`, nonché i tipi definiti dall'utente, non sono necessariamente essere atomici. A parte le funzioni della libreria progettate a tale scopo, non vi è alcuna garanzia di lettura-modifica-scrittura atomica, ad esempio nel caso di incremento o decremento.

