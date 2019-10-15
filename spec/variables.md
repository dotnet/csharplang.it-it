---
ms.openlocfilehash: a01cf9387b8dc47de036bf0bd1496c19a441d81c
ms.sourcegitcommit: 7f7fc6e9e195e51b7ff8229aeaa70aa9fbbb63cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876804"
---
# <a name="variables"></a>Variabili

Le variabili rappresentano i percorsi di archiviazione. Ogni variabile dispone di un tipo che determina quali valori possono essere archiviati nella variabile. C#è un linguaggio indipendente dai tipi e il C# compilatore garantisce che i valori archiviati nelle variabili siano sempre del tipo appropriato. Il valore di una variabile può essere modificato tramite l'assegnazione o l' `++` uso degli operatori e. `--`

Una variabile deve essere ***assegnata definitivamente*** ([assegnazione definita](variables.md#definite-assignment)) prima di poter ottenere il relativo valore.

Come descritto nelle sezioni seguenti, le variabili vengono ***inizialmente assegnate*** o ***inizialmente non assegnate***. Una variabile inizialmente assegnata ha un valore iniziale ben definito ed è sempre considerata assegnata definitivamente. Una variabile inizialmente non assegnata non ha un valore iniziale. Affinché una variabile inizialmente non assegnata venga considerata definitivamente assegnata in una determinata posizione, un'assegnazione alla variabile deve essere eseguita in ogni percorso di esecuzione possibile che conduce a tale posizione.

## <a name="variable-categories"></a>Categorie di variabili

C#definisce sette categorie di variabili: variabili statiche, variabili di istanza, elementi di matrice, parametri di valore, parametri di riferimento, parametri di output e variabili locali. Le sezioni seguenti descrivono ognuna di queste categorie.

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
`x`è una variabile statica, `y` è una variabile di istanza `v[0]` , è un elemento di `a` matrice, è un parametro `b` di valore, è un `c` parametro di riferimento, è un `i` parametro di output e è una variabile locale .

### <a name="static-variables"></a>Variabili statiche

Un campo dichiarato con il `static` modificatore è denominato ***variabile statica***. Una variabile statica è presente prima dell'esecuzione del costruttore statico ([costruttori statici](classes.md#static-constructors)) per il tipo che lo contiene e cessa di esistere quando il dominio applicazione associato cessa di esistere.

Il valore iniziale di una variabile statica è il valore predefinito ([valori predefiniti](variables.md#default-values)) del tipo della variabile.

Ai fini del controllo di assegnazione definito, una variabile statica viene considerata inizialmente assegnata.

### <a name="instance-variables"></a>Variabili di istanza

Un campo dichiarato senza il `static` modificatore viene chiamato ***variabile di istanza***.

#### <a name="instance-variables-in-classes"></a>Variabili di istanza nelle classi

Una variabile di istanza di una classe diventa disponibile quando viene creata una nuova istanza di tale classe e cessa di esistere quando non vi sono riferimenti a tale istanza e il distruttore dell'istanza, se presente, è stato eseguito.

Il valore iniziale di una variabile di istanza di una classe è il valore predefinito ([valori predefiniti](variables.md#default-values)) del tipo della variabile.

Ai fini del controllo di assegnazione definito, una variabile di istanza di una classe viene considerata inizialmente assegnata.

#### <a name="instance-variables-in-structs"></a>Variabili di istanza negli struct

Una variabile di istanza di uno struct ha esattamente la stessa durata della variabile struct a cui appartiene. In altre parole, quando una variabile di un tipo struct entra in vigore o cessa di esistere, è necessario eseguire anche le variabili di istanza dello struct.

Lo stato di assegnazione iniziale di una variabile di istanza di uno struct è identico a quello della variabile struct che lo contiene. In altre parole, quando una variabile struct viene considerata inizialmente assegnata, anch ' essa sono variabili di istanza e, quando una variabile struct viene considerata inizialmente non assegnata, le variabili di istanza non vengono assegnate in modo analogo.

### <a name="array-elements"></a>Elementi di matrice

Gli elementi di una matrice entrano in vigore quando viene creata un'istanza di matrice e smettono di esistere quando non vi sono riferimenti a tale istanza di matrice.

Il valore iniziale di ogni elemento di una matrice è il valore predefinito ([valori predefiniti](variables.md#default-values)) del tipo degli elementi della matrice.

Ai fini del controllo di assegnazione definito, viene considerato inizialmente assegnato un elemento di matrice.

### <a name="value-parameters"></a>Parametri valore

Un parametro dichiarato senza un `ref` modificatore o `out` è un ***parametro di valore***.

Un parametro di valore entra in vigore alla chiamata del membro della funzione (metodo, costruttore di istanza, funzione di accesso o operatore) o funzione anonima a cui appartiene il parametro e viene inizializzato con il valore dell'argomento fornito nella chiamata. Un parametro di valore in genere smette di esistere al ritorno del membro della funzione o della funzione anonima. Tuttavia, se il parametro value viene acquisito da una funzione anonima ([espressioni di funzione anonima](expressions.md#anonymous-function-expressions)), la durata si estende almeno finché il delegato o l'albero delle espressioni creato da tale funzione anonima non sarà idoneo per Garbage Collection.

Ai fini del controllo di assegnazione definito, un parametro di valore viene considerato inizialmente assegnato.

### <a name="reference-parameters"></a>Parametri per riferimento

Un parametro dichiarato con un `ref` modificatore è un ***parametro di riferimento***.

Un parametro di riferimento non crea un nuovo percorso di archiviazione. Un parametro Reference rappresenta invece lo stesso percorso di archiviazione della variabile specificata come argomento nel membro della funzione o in una chiamata di funzione anonima. Pertanto, il valore di un parametro di riferimento è sempre lo stesso della variabile sottostante.

Le seguenti regole di assegnazione definite si applicano ai parametri di riferimento. Si notino le diverse regole per i parametri di output descritti in [parametri di output](variables.md#output-parameters).

*  Una variabile deve essere assegnata definitivamente ([assegnazione definita](variables.md#definite-assignment)) prima di poter essere passata come parametro di riferimento in una chiamata a un membro di funzione o a un delegato.
*  All'interno di un membro di funzione o di una funzione anonima, viene considerato inizialmente assegnato un parametro di riferimento.

All'interno di un metodo di istanza o di una funzione di accesso `this` dell'istanza di un tipo struct, la parola chiave si comporta esattamente come un parametro di riferimento del tipo struct ([questo accesso](expressions.md#this-access)).

### <a name="output-parameters"></a>Parametri di output

Un parametro dichiarato con un `out` modificatore è un ***parametro di output***.

Un parametro di output non crea un nuovo percorso di archiviazione. Un parametro di output rappresenta invece lo stesso percorso di archiviazione della variabile specificata come argomento nel membro della funzione o nella chiamata al delegato. Pertanto, il valore di un parametro di output corrisponde sempre alla variabile sottostante.

Le seguenti regole di assegnazione definite si applicano ai parametri di output. Si notino le diverse regole per i parametri di riferimento descritti in [parametri di riferimento](variables.md#reference-parameters).

*  Una variabile non deve essere definitivamente assegnata prima di poter essere passata come parametro di output in una chiamata a un membro di funzione o a un delegato.
*  Dopo il normale completamento di un membro di funzione o di una chiamata a un delegato, ogni variabile passata come parametro di output viene considerata assegnata in tale percorso di esecuzione.
*  All'interno di un membro di funzione o di una funzione anonima, un parametro di output viene considerato inizialmente non assegnato.
*  Ogni parametro di output di un membro di funzione o di una funzione anonima deve essere assegnato definitivamente ([assegnazione definita](variables.md#definite-assignment)) prima che il membro della funzione o la funzione anonima restituisca normalmente.

All'interno di un costruttore di istanza di un tipo `this` struct, la parola chiave si comporta esattamente come un parametro di output del tipo struct ([questo accesso](expressions.md#this-access)).

### <a name="local-variables"></a>Variabili locali

Una ***variabile locale*** viene dichiarata da un *local_variable_declaration*, che può verificarsi in un *blocco*, un *for_Statement*, un *switch_Statement* o un *using_statement*; o da un *foreach_statement* o un *specific_catch_clause* per un *try_statement*.

La durata di una variabile locale è la parte dell'esecuzione del programma durante la quale è garantita la riservatezza dell'archiviazione. Questa durata estende almeno dall'immissione nel *blocco*, *for_Statement*, *switch_Statement*, *using_statement*, *foreach_statement*o *specific_catch_clause* a cui è associato, fino a l'esecuzione di tale *blocco*, *for_Statement*, *switch_Statement*, *using_statement*, *foreach_statement*o *specific_catch_clause* termina in qualsiasi modo. (L'immissione di un *blocco* racchiuso o la chiamata di un metodo viene sospesa, ma non termina, l'esecuzione del *blocco*corrente, *for_Statement*, *switch_Statement*, *using_statement*, *foreach_statement*o *specific_ catch_clause*.) Se la variabile locale viene acquisita da una funzione anonima ([variabili esterne acquisite](expressions.md#captured-outer-variables)), la durata si estende almeno fino a quando il delegato o l'albero delle espressioni creato dalla funzione anonima, insieme ad altri oggetti che fanno riferimento al la variabile acquisita è idonea per Garbage Collection.

Se il *blocco*padre, *for_Statement*, *switch_Statement*, *using_statement*, *foreach_statement*o *specific_catch_clause* viene immesso in modo ricorsivo, viene creata una nuova istanza della variabile locale Time e il relativo *local_variable_initializer*, se presente, viene valutato ogni volta.

Una variabile locale introdotta da un *local_variable_declaration* non viene inizializzata automaticamente e pertanto non ha alcun valore predefinito. Ai fini del controllo di assegnazione definito, una variabile locale introdotta da un *local_variable_declaration* viene considerata inizialmente non assegnata. Un *local_variable_declaration* può includere un *local_variable_initializer*, nel qual caso la variabile viene considerata assegnata definitivamente solo dopo l'espressione di inizializzazione ([istruzioni di dichiarazione](variables.md#declaration-statements)).

Nell'ambito di una variabile locale introdotta da un *local_variable_declaration*, si tratta di un errore in fase di compilazione per fare riferimento a tale variabile locale in una posizione testuale che precede il *local_variable_declarator*. Se la dichiarazione di variabile locale è implicita ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)), è anche un errore fare riferimento alla variabile all'interno di *local_variable_declarator*.

Una variabile locale introdotta da un *foreach_statement* o un *specific_catch_clause* viene considerata definitivamente assegnata nell'intero ambito.

La durata effettiva di una variabile locale è dipendente dall'implementazione. Un compilatore, ad esempio, potrebbe determinare in modo statico che una variabile locale in un blocco viene utilizzata solo per una piccola parte di tale blocco. Utilizzando questa analisi, il compilatore potrebbe generare codice che comportano una durata più breve del relativo spazio di archiviazione della variabile rispetto al blocco che lo contiene.

Lo spazio di archiviazione a cui fa riferimento una variabile di riferimento locale viene recuperato indipendentemente dalla durata della variabile di riferimento locale ([gestione automatica della memoria](basic-concepts.md#automatic-memory-management)).

## <a name="default-values"></a>Valori predefiniti

Le seguenti categorie di variabili vengono inizializzate automaticamente sui rispettivi valori predefiniti:

*  Variabili statiche.
*  Variabili di istanza delle istanze della classe.
*  Elementi di matrice.

Il valore predefinito di una variabile dipende dal tipo della variabile e viene determinato nel modo seguente:

*  Per una variabile di un *value_type*, il valore predefinito è lo stesso del valore calcolato dal costruttore predefinito di *value_type*([costruttori predefiniti](types.md#default-constructors)).
*  Per una variabile di un *reference_type*, il valore predefinito è `null`.

L'inizializzazione dei valori predefiniti viene in genere eseguita con il gestore della memoria o Garbage Collector inizializzare la memoria su tutti i bit-zero prima che venga allocato per l'utilizzo. Per questo motivo, è consigliabile utilizzare All-bits-zero per rappresentare il riferimento null.

## <a name="definite-assignment"></a>Assegnazione definita

In una determinata posizione nel codice eseguibile di un membro di funzione, una variabile viene ***definita definitivamente assegnata*** se il compilatore è in grado di dimostrare, da un'analisi di flusso statica specifica ([regole precise per determinare l'assegnazione definita](variables.md#precise-rules-for-determining-definite-assignment)), che la variabile è stata inizializzata automaticamente o è stata la destinazione di almeno un'assegnazione. Dichiarate in modo informale, le regole di assegnazione definitiva sono:

*  Una variabile inizialmente assegnata (le[variabili assegnate inizialmente](variables.md#initially-assigned-variables)) viene sempre considerata definitivamente assegnata.
*  Una variabile inizialmente non assegnata ([inizialmente variabili non assegnate](variables.md#initially-unassigned-variables)) viene considerata definitivamente assegnata in una determinata posizione se tutti i possibili percorsi di esecuzione che portano a tale posizione contengono almeno uno degli elementi seguenti:
    * Assegnazione semplice ([assegnazione semplice](expressions.md#simple-assignment)) in cui la variabile è l'operando sinistro.
    * Espressione di chiamata ([espressioni di chiamata](expressions.md#invocation-expressions)) o espressione di creazione di oggetti (espressioni di[creazione di oggetti](expressions.md#object-creation-expressions)) che passa la variabile come parametro di output.
    * Per una variabile locale, una dichiarazione di variabile locale ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)) che include un inizializzatore di variabile.

La specifica formale sottostante le regole informali precedenti è descritta in [variabili inizialmente assegnate](variables.md#initially-assigned-variables), [variabili inizialmente non assegnate](variables.md#initially-unassigned-variables)e [regole precise per determinare l'assegnazione definita](variables.md#precise-rules-for-determining-definite-assignment).

Gli Stati di assegnazione definiti delle variabili di istanza di una variabile *struct_type* vengono rilevati singolarmente e collettivamente. Oltre alle regole precedenti, le regole seguenti si applicano alle variabili *struct_type* e alle relative variabili di istanza:

*  Una variabile di istanza viene considerata definitivamente assegnata se la variabile *struct_type* che lo contiene viene considerata definitivamente assegnata.
*  Una variabile *struct_type* viene considerata definitivamente assegnata se ogni variabile di istanza viene considerata assegnata definitivamente.

L'assegnazione definita è un requisito nei contesti seguenti:

*  Una variabile deve essere assegnata in modo sicuro in ogni posizione in cui viene ottenuto il relativo valore. In questo modo si garantisce che non si verifichino mai valori non definiti. L'occorrenza di una variabile in un'espressione viene considerata come ottenere il valore della variabile, tranne quando
    * la variabile è l'operando sinistro di un'assegnazione semplice,
    * la variabile viene passata come parametro di output o
    * la variabile è una variabile *struct_type* e si verifica come operando sinistro di un accesso ai membri.
*  Una variabile deve essere assegnata in modo sicuro in ogni posizione in cui viene passata come parametro di riferimento. In questo modo il membro della funzione richiamato può considerare il parametro di riferimento inizialmente assegnato.
*  Tutti i parametri di output di un membro di funzione devono essere assegnati in modo sicuro in ogni posizione in cui il `return` membro della funzione restituisce (tramite un'istruzione o tramite l'esecuzione che raggiunge la fine del corpo del membro della funzione). Ciò garantisce che i membri di funzione non restituiscano valori non definiti nei parametri di output, consentendo al compilatore di prendere in considerazione la chiamata di un membro di funzione che accetta una variabile come parametro di output equivalente a un'assegnazione alla variabile.
*  La `this` variabile di un costruttore di istanza di *struct_type* deve essere assegnata in modo sicuro in ogni posizione in cui il costruttore dell'istanza restituisce.

### <a name="initially-assigned-variables"></a>Variabili assegnate inizialmente

Le categorie di variabili seguenti sono classificate inizialmente come assegnate:

*  Variabili statiche.
*  Variabili di istanza delle istanze della classe.
*  Variabili di istanza delle variabili struct inizialmente assegnate.
*  Elementi di matrice.
*  Parametri del valore.
*  Parametri di riferimento.
*  Variabili dichiarate `catch` in una clausola `foreach` o in un'istruzione.

### <a name="initially-unassigned-variables"></a>Variabili inizialmente non assegnate

Le categorie di variabili seguenti sono classificate inizialmente come non assegnate:

*  Variabili di istanza di variabili struct inizialmente non assegnate.
*  Parametri di output, inclusa `this` la variabile dei costruttori di istanze struct.
*  Variabili locali, ad eccezione di quelle dichiarate in `foreach` una `catch` clausola o in un'istruzione.

### <a name="precise-rules-for-determining-definite-assignment"></a>Regole precise per determinare l'assegnazione definitiva

Per determinare che ogni variabile utilizzata è assegnata definitivamente, il compilatore deve usare un processo equivalente a quello descritto in questa sezione.

Il compilatore elabora il corpo di ogni membro di funzione che dispone di una o più variabili inizialmente non assegnate. Per ogni variabile inizialmente non assegnata *v*, il compilatore determina uno ***stato di assegnazione definito*** per *v* in ognuno dei punti seguenti del membro della funzione:

*  All'inizio di ogni istruzione
*  Al punto finale ([punti finali e raggiungibilità](statements.md#end-points-and-reachability)) di ogni istruzione
*  In ogni arco che trasferisce il controllo a un'altra istruzione o al punto finale di un'istruzione
*  All'inizio di ogni espressione
*  Alla fine di ogni espressione

Lo stato di assegnazione definito di *v* può essere uno dei seguenti:

*  Assegnato definitivamente. Ciò indica che in tutti i flussi di controllo possibili fino a questo punto è stato assegnato un valore a *v* .
*  Non assegnato definitivamente. Per lo stato di una variabile alla fine di un'espressione di tipo `bool`, lo stato di una variabile che non è assegnata definitivamente può (ma non necessariamente) rientrare in uno degli stati secondari seguenti:
    * Assegnato definitivamente dopo un'espressione true. Questo stato indica che *v* viene assegnato definitivamente se l'espressione booleana è stata valutata come true, ma non è necessariamente assegnata se l'espressione booleana viene valutata come false.
    * Assegnato definitivamente dopo un'espressione false. Questo stato indica che *v* viene assegnato definitivamente se l'espressione booleana viene valutata come false, ma non viene necessariamente assegnata se l'espressione booleana restituisce true.

Le regole seguenti determinano il modo in cui viene determinato lo stato di una variabile *v* in ogni posizione.

#### <a name="general-rules-for-statements"></a>Regole generali per le istruzioni

*  *v* non viene assegnato definitivamente all'inizio del corpo di un membro di funzione.
*  *v* viene assegnato definitivamente all'inizio di un'istruzione non raggiungibile.
*  Lo stato di assegnazione definito di *v* all'inizio di qualsiasi altra istruzione è determinato dal controllo dello stato di assegnazione definito di *v* in tutti i trasferimenti di flusso di controllo destinati all'inizio dell'istruzione. Se (e solo se) *v* viene assegnato in modo sicuro a tutti questi trasferimenti del flusso di controllo, *v* viene assegnato definitivamente all'inizio dell'istruzione. Il set di possibili trasferimenti del flusso di controllo viene determinato in modo analogo al controllo della raggiungibilità delle istruzioni ([punti finali e raggiungibilità](statements.md#end-points-and-reachability)).
*  Stato di assegnazione definito di *v* in corrispondenza del punto finale di un blocco `checked`, `unchecked` `do`, `if` `foreach` `for` `while`,,,,, `lock`, `using`, o `switch`l'istruzione viene determinata controllando lo stato di assegnazione definito di *v* in tutti i trasferimenti del flusso di controllo che hanno come destinazione il punto finale dell'istruzione. Se *v* è assegnato definitivamente a tutti questi trasferimenti del flusso di controllo, *v* viene assegnato definitivamente al punto finale dell'istruzione. In caso contrario *v* non è assegnato definitivamente al punto finale dell'istruzione. Il set di possibili trasferimenti del flusso di controllo viene determinato in modo analogo al controllo della raggiungibilità delle istruzioni ([punti finali e raggiungibilità](statements.md#end-points-and-reachability)).

#### <a name="block-statements-checked-and-unchecked-statements"></a>Istruzioni Block, checked e unchecked

Lo stato di assegnazione definito di *v* nel controllo viene trasferito alla prima istruzione dell'elenco di istruzioni nel blocco (o al punto finale del blocco, se l'elenco di istruzioni è vuoto) equivale all'istruzione di assegnazione definita di *v* prima del blocco istruzione `checked`, o `unchecked` .

#### <a name="expression-statements"></a>Istruzioni di espressione

Per un'istruzione di espressione *stmt* costituita dall'espressione *expr*:

*  *v* ha lo stesso stato di assegnazione definita all'inizio di *expr* come all'inizio di *stmt*.
*  Se *v* viene assegnato definitivamente alla fine di *expr*, viene assegnato definitivamente al punto finale di *stmt*; in caso contrario non viene assegnato definitivamente al punto finale di *stmt*.

#### <a name="declaration-statements"></a>Istruzioni di dichiarazione

*  Se *stmt* è un'istruzione di dichiarazione senza inizializzatori, *v* ha lo stesso stato di assegnazione definito al punto finale di *stmt* come all'inizio di *stmt*.
*  Se *stmt* è un'istruzione di dichiarazione con inizializzatori, lo stato di assegnazione definito per *v* viene determinato come se *stmt* fosse un elenco di istruzioni, con un'istruzione di assegnazione per ogni dichiarazione con un inizializzatore (nell'ordine di Dichiarazione).

#### <a name="if-statements"></a>Istruzioni If

Per un' `if` istruzione *stmt* nel formato:
```csharp
if ( expr ) then_stmt else else_stmt
```

*  *v* ha lo stesso stato di assegnazione definita all'inizio di *expr* come all'inizio di *stmt*.
*  Se la *v* viene assegnata definitivamente alla fine di *expr*, viene definitivamente assegnata al trasferimento del flusso di controllo a *then_stmt* e a *else_stmt* o all'endpoint di *stmt* se non è presente alcuna clausola else.
*  Se *v* ha lo stato "assegnato definitivamente dopo l'espressione true" alla fine di *expr*, viene definitivamente assegnato al trasferimento del flusso di controllo a *then_stmt*e non è assegnato definitivamente al trasferimento del flusso di controllo a *else_ stmt* o all'endpoint di *stmt* se non è presente alcuna clausola else.
*  Se *v* ha lo stato "assegnata in modo definitivo dopo false Expression" alla fine di *expr*, viene definitivamente assegnato al trasferimento del flusso di controllo a *else_stmt*e non assegnato definitivamente al trasferimento del flusso di controllo a *then_stmt* . Viene assegnato in modo sicuro all'endpoint di *stmt* , se e solo se viene assegnato definitivamente all'endpoint di *then_stmt*.
*  In caso contrario, *v* viene considerato non assegnato definitivamente al trasferimento del flusso di controllo a *then_stmt* o *else_stmt*oppure all'endpoint di *stmt* se non è presente alcuna clausola else.

#### <a name="switch-statements"></a>Istruzioni switch

In un' `switch` istruzione *stmt* con un'espressione di controllo *expr*:

*  Lo stato di assegnazione definito di *v* all'inizio di *expr* corrisponde allo stato di *v* all'inizio di *stmt*.
*  Lo stato di assegnazione definito di *v* nel trasferimento del flusso di controllo a un elenco di istruzioni switch Block raggiungibili è identico allo stato di assegnazione definito di *v* alla fine di *expr*.

#### <a name="while-statements"></a>Istruzioni while

Per un' `while` istruzione *stmt* nel formato:
```csharp
while ( expr ) while_body
```

*  *v* ha lo stesso stato di assegnazione definita all'inizio di *expr* come all'inizio di *stmt*.
*  Se la *v* viene assegnata definitivamente alla fine di *expr*, viene definitivamente assegnata al trasferimento del flusso di controllo a *while_body* e al punto finale di *stmt*.
*  Se *v* ha lo stato "assegnato definitivamente dopo l'espressione true" alla fine di *expr*, viene definitivamente assegnato al trasferimento del flusso di controllo a *while_body*, ma non assegnato definitivamente all'endpoint di *stmt*.
*  Se *v* ha lo stato "assegnata in modo definitivo dopo false Expression" alla fine di *expr*, viene definitivamente assegnato nel trasferimento del flusso di controllo al punto finale di *stmt*, ma non è stato assegnato in modo definitivo sul trasferimento del flusso di controllo a *while _body*.

#### <a name="do-statements"></a>Istruzioni do

Per un' `do` istruzione *stmt* nel formato:
```csharp
do do_body while ( expr ) ;
```

*  *v* ha lo stesso stato di assegnazione definito nel trasferimento del flusso di controllo dall'inizio di *stmt* a *do_body* come all'inizio di *stmt*.
*  *v* ha lo stesso stato di assegnazione definita all'inizio di *expr* come nel punto finale di *do_body*.
*  Se la *v* viene assegnata definitivamente alla fine di *expr*, viene definitivamente assegnata al punto finale di *stmt*nel trasferimento del flusso di controllo.
*  Se *v* ha lo stato "definitely Assigned after false Expression" alla fine di *expr*, viene assegnato definitivamente nel trasferimento del flusso di controllo al punto finale di *stmt*.

#### <a name="for-statements"></a>Istruzioni for

Controllo di assegnazione definito per `for` un'istruzione nel formato:
```csharp
for ( for_initializer ; for_condition ; for_iterator ) embedded_statement
```
viene eseguita come se l'istruzione fosse scritta:
```csharp
{
    for_initializer ;
    while ( for_condition ) {
        embedded_statement ;
        for_iterator ;
    }
}
```

Se *for_condition* viene omesso dall' `for` istruzione, la valutazione dell'assegnazione definita procede come se *for_condition* venisse sostituito con `true` nell'espansione precedente.

#### <a name="break-continue-and-goto-statements"></a>Istruzioni break, continue e GOTO

Lo stato di assegnazione definito di *v* nel trasferimento del flusso di controllo causato `break`da un'istruzione `goto` , `continue`o equivale allo stato di assegnazione definito di *v* all'inizio dell'istruzione.

#### <a name="throw-statements"></a>Istruzioni throw

Per un'istruzione *stmt* nel formato
```csharp
throw expr ;
```

Lo stato di assegnazione definito di *v* all'inizio di *expr* corrisponde allo stato di assegnazione definito di *v* all'inizio di *stmt*.

#### <a name="return-statements"></a>Istruzioni return

Per un'istruzione *stmt* nel formato
```csharp
return expr ;
```

*  Lo stato di assegnazione definito di *v* all'inizio di *expr* corrisponde allo stato di assegnazione definito di *v* all'inizio di *stmt*.
*  Se *v* è un parametro di output, è necessario che sia assegnato definitivamente quanto segue:
    * dopo *expr*
    * o alla fine del `finally` blocco di un oggetto `try` - ocheracchiude`return` l'istruzione. `catch` `try` - `finally` - `finally`

Per un'istruzione stmt nel formato:
```csharp
return ;
```

*  Se *v* è un parametro di output, è necessario che sia assegnato definitivamente quanto segue:
    * prima di *stmt*
    * o alla fine del `finally` blocco di un oggetto `try` - ocheracchiude`return` l'istruzione. `catch` `try` - `finally` - `finally`

#### <a name="try-catch-statements"></a>Istruzioni try-catch

Per un'istruzione *stmt* nel formato:
```csharp
try try_block
catch(...) catch_block_1
...
catch(...) catch_block_n
```

*  Lo stato di assegnazione definito di *v* all'inizio di *try_block* è lo stesso dello stato di assegnazione definito di *v* all'inizio di *stmt*.
*  Lo stato di assegnazione definito di *v* all'inizio di *catch_block_i* (per qualsiasi *i*) corrisponde allo stato di assegnazione definito di *v* all'inizio di *stmt*.
*  Lo stato di assegnazione definito di *v* all'endpoint di *stmt* viene assegnato definitivamente se (e solo se) *v* viene assegnato definitivamente all'endpoint di *try_block* e ogni *catch_block_i* (per ogni *i* da 1 a *n* ).

#### <a name="try-finally-statements"></a>Istruzioni try-finally

Per un' `try` istruzione *stmt* nel formato:
```csharp
try try_block finally finally_block
```

*  Lo stato di assegnazione definito di *v* all'inizio di *try_block* è lo stesso dello stato di assegnazione definito di *v* all'inizio di *stmt*.
*  Lo stato di assegnazione definito di *v* all'inizio di *finally_block* è lo stesso dello stato di assegnazione definito di *v* all'inizio di *stmt*.
*  Lo stato di assegnazione definito di *v* all'endpoint di *stmt* viene assegnato definitivamente se (e solo se) viene soddisfatta almeno una delle condizioni seguenti:
    * *v* viene assegnato definitivamente all'endpoint di *try_block*
    * *v* viene assegnato definitivamente all'endpoint di *finally_block*

Se viene eseguito il trasferimento di un flusso di controllo `goto` (ad esempio, un'istruzione) che inizia all'interno di *try_block*e termina all'esterno di *try_block*, *v* viene anche considerato definitivamente assegnato a tale trasferimento del flusso di controllo se *v* è assegnato definitivamente all'endpoint di *finally_block*. (Non si tratta di un solo se, se *v* è assegnato definitivamente per un altro motivo in questo trasferimento del flusso di controllo, viene comunque considerato assegnato definitivamente).

#### <a name="try-catch-finally-statements"></a>Istruzioni try-catch-finally

Analisi di assegnazione definita per `try` un' - `catch` - istruzione nelformato:`finally`
```csharp
try try_block
catch(...) catch_block_1
...
catch(...) catch_block_n
finally *finally_block*
```
viene eseguita come se l'istruzione fosse un' `try` - `try` - `finally` istruzione che include un' `catch` istruzione:
```csharp
try {
    try try_block
    catch(...) catch_block_1
    ...
    catch(...) catch_block_n
}
finally finally_block
```

Nell'esempio seguente viene illustrato il modo in cui i `try` diversi blocchi di un'istruzione ([istruzione try](statements.md#the-try-statement)) influiscono sull'assegnazione definita.
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

#### <a name="foreach-statements"></a>Istruzioni foreach

Per un' `foreach` istruzione *stmt* nel formato:
```csharp
foreach ( type identifier in expr ) embedded_statement
```

*  Lo stato di assegnazione definito di *v* all'inizio di *expr* corrisponde allo stato di *v* all'inizio di *stmt*.
*  Lo stato di assegnazione definito di *v* nel trasferimento del flusso di controllo a *embedded_statement* o al punto finale di *stmt* è uguale allo stato di *v* alla fine di *expr*.

#### <a name="using-statements"></a>Istruzioni using

Per un' `using` istruzione *stmt* nel formato:
```csharp
using ( resource_acquisition ) embedded_statement
```

*  Lo stato di assegnazione definito di *v* all'inizio di *resource_acquisition* è uguale allo stato di *v* all'inizio di *stmt*.
*  Lo stato di assegnazione definito di *v* nel trasferimento del flusso di controllo a *embedded_statement* è lo stesso dello stato *v* alla fine di *resource_acquisition*.

#### <a name="lock-statements"></a>Istruzioni lock

Per un' `lock` istruzione *stmt* nel formato:
```csharp
lock ( expr ) embedded_statement
```

*  Lo stato di assegnazione definito di *v* all'inizio di *expr* corrisponde allo stato di *v* all'inizio di *stmt*.
*  Lo stato di assegnazione definito di *v* nel trasferimento del flusso di controllo a *embedded_statement* corrisponde allo stato di *v* alla fine di *expr*.

#### <a name="yield-statements"></a>Istruzioni yield

Per un' `yield return` istruzione *stmt* nel formato:
```csharp
yield return expr ;
```

*  Lo stato di assegnazione definito di *v* all'inizio di *expr* corrisponde allo stato di *v* all'inizio di *stmt*.
*  Lo stato di assegnazione definito di *v* alla fine di *stmt* è uguale a quello di *v* alla fine di *expr*.
*  Un' `yield break` istruzione non ha alcun effetto sullo stato di assegnazione definito.

#### <a name="general-rules-for-simple-expressions"></a>Regole generali per le espressioni semplici

La regola seguente si applica a questi tipi di espressioni: i valori letterali ([valori letterali](expressions.md#literals)), i nomi semplici ([nomi semplici](expressions.md#simple-names)), le espressioni di accesso ai membri ([accesso ai membri](expressions.md#member-access)), le espressioni di accesso di base non indicizzate ([accesso di base](expressions.md#base-access)), `typeof`espressioni ([operatore typeof](expressions.md#the-typeof-operator)), espressioni con valore predefinito ([espressioni con valore predefinito](expressions.md#default-value-expressions)) `nameof` ed espressioni ([espressioni NameOf](expressions.md#nameof-expressions)).

*  Lo stato di assegnazione definito di *v* alla fine di tale espressione corrisponde allo stato di assegnazione definito di *v* all'inizio dell'espressione.

#### <a name="general-rules-for-expressions-with-embedded-expressions"></a>Regole generali per le espressioni con espressioni incorporate

Le regole seguenti si applicano a questi tipi di espressioni: espressioni tra parentesi ([espressioni racchiuse tra parentesi](expressions.md#parenthesized-expressions)), espressioni di accesso agli elementi ([accesso agli elementi](expressions.md#element-access)), espressioni di accesso di base con indicizzazione ([accesso di base](expressions.md#base-access)), incremento e espressioni di decremento ([operatori di incremento e decremento suffisso](expressions.md#postfix-increment-and-decrement-operators), [operatori di incremento e decremento di prefisso](expressions.md#prefix-increment-and-decrement-operators)), espressioni cast ([espressioni cast](expressions.md#cast-expressions)), unario `+`, `-`, `~`, `*` espressioni, Binary `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `<`, `<=`, `>`, `>=`, `==`, `!=`, `is`, `as`, `&`, `|`, `^` espressioni ([operatori aritmetici](expressions.md#arithmetic-operators), [operatori Shift](expressions.md#shift-operators), [relazionali e test di tipo operatori](expressions.md#relational-and-type-testing-operators), [operatori logici](expressions.md#logical-operators)), espressioni di assegnazione composta ([assegnazione composta](expressions.md#compound-assignment)), `checked` ed `unchecked` espressioni ([operatori checked e unchecked](expressions.md#the-checked-and-unchecked-operators)), più matrici e Delegate espressioni di creazione ([nuovo operatore](expressions.md#the-new-operator)).

Ognuna di queste espressioni ha una o più sottoespressioni che vengono valutate in modo non condizionale in un ordine fisso. Ad esempio, l'operatore `%` binario valuta il lato sinistro dell'operatore, quindi la parte destra. Un'operazione di indicizzazione valuta l'espressione indicizzata, quindi valuta ognuna delle espressioni di indice, in ordine da sinistra a destra. Per un'espressione *expr*, che include sottoespressioni *E1, E2,..., en*, valutate nell'ordine seguente:

*  Lo stato di assegnazione definito di *v* all'inizio di *E1* corrisponde allo stato di assegnazione definito all'inizio di *expr*.
*  Lo stato di assegnazione definito di *v* all'inizio di *ei* (*maggiore* di uno) corrisponde allo stato di assegnazione definito alla fine della sottoespressione precedente.
*  Lo stato di assegnazione definito di *v* alla fine di *expr* corrisponde allo stato di assegnazione definito alla fine di *en*

#### <a name="invocation-expressions-and-object-creation-expressions"></a>Espressioni di chiamata ed espressioni di creazione di oggetti

Per un'espressione di chiamata *expr* nel formato:
```csharp
primary_expression ( arg1 , arg2 , ... , argN )
```
o un'espressione di creazione di oggetti nel formato seguente:
```csharp
new type ( arg1 , arg2 , ... , argN )
```

*  Per un'espressione di chiamata, lo stato di assegnazione definito di *v* prima di *primary_expression* è lo stesso dello stato *v* prima di *expr*.
*  Per un'espressione di chiamata, lo stato di assegnazione definito di *v* prima di *arg1* è lo stesso dello stato *v* dopo *primary_expression*.
*  Per un'espressione di creazione di un oggetto, lo stato di assegnazione definito di *v* prima di *arg1* è lo stesso dello stato *v* prima di *expr*.
*  Per ogni argomento *Argi*, lo stato di assegnazione definito di *v* dopo *Argi* è determinato dalle regole di espressione normali, ignorando `ref` eventuali `out` modificatori o.
*  Per ogni argomento *Argi* per i *maggiori di* uno, lo stato di assegnazione definito di *v* prima di *Argi* è lo stesso dello stato *v* dopo l' *arg*precedente.
*  Se la variabile *v* `out` viene passata come argomento (ad esempio, un argomento del `out v`form) in uno degli argomenti, lo stato di *v* dopo *expr* viene assegnato definitivamente. In caso contrario lo stato *v* dopo *expr* corrisponde allo stato di *v* dopo *argN*.
*  Per gli inizializzatori di matrice ([espressioni di creazione di matrici](expressions.md#array-creation-expressions)), inizializzatori di oggetto ([inizializzatori di oggetto](expressions.md#object-initializers)), inizializzatori di insieme ([inizializzatori di insieme](expressions.md#collection-initializers)) e inizializzatori di oggetti anonimi (creazione di[oggetti anonimi espressioni](expressions.md#anonymous-object-creation-expressions)), lo stato di assegnazione definito è determinato dall'espansione che questi costrutti sono definiti in termini di.

#### <a name="simple-assignment-expressions"></a>Espressioni di assegnazione semplici

Per un'espressione *expr* nel formato `w = expr_rhs`:

*  Lo stato di assegnazione definito di *v* prima di *expr_rhs* è lo stesso dello stato di assegnazione definito di *v* prima di *expr*.
*  Lo stato di assegnazione definito di *v* dopo *expr* è determinato da:
   * Se *w* è la stessa variabile di *v*, lo stato di assegnazione definito di *v* dopo *expr* viene assegnato definitivamente.
   * In caso contrario, se l'assegnazione si verifica all'interno del costruttore di istanza di un tipo struct, se *w* è un accesso alla proprietà che designa una proprietà implementata automaticamente *P* nell'istanza da costruire e *v* è il campo sottostante nascosto di *P*, lo stato di assegnazione definito di *v* dopo *expr* viene assegnato definitivamente.
   * In caso contrario, lo stato di assegnazione definito di *v* dopo *expr* corrisponde allo stato di assegnazione definito di *v* dopo *expr_rhs*.

#### <a name="-conditional-and-expressions"></a>espressioni & & (condizionale e)

Per un'espressione *expr* nel formato `expr_first && expr_second`:

*  Lo stato di assegnazione definito di *v* prima di *expr_first* è lo stesso dello stato di assegnazione definito di *v* prima di *expr*.
*  Lo stato di assegnazione definito di *v* prima di *expr_second* viene assegnato definitivamente se lo stato di *v* dopo *expr_first* è definitivamente assegnato o "assegnato definitivamente dopo l'espressione true". In caso contrario, non viene assegnato definitivamente.
*  Lo stato di assegnazione definito di *v* dopo *expr* è determinato da:
    * Se *expr_first* è un'espressione costante con il valore `false`, lo stato di assegnazione definito di *v* dopo *expr* corrisponde allo stato di assegnazione definito di *v* dopo *expr_first*.
    * In caso contrario, se lo stato di *v* dopo *expr_first* è assegnato definitivamente, lo stato di *v* dopo *expr* viene assegnato definitivamente.
    * In caso contrario, se lo stato di *v* dopo *expr_second* è assegnato definitivamente e lo stato di *v* dopo *expr_first* è "definitivamente assegnato dopo false Expression", lo stato di *v* dopo *expr* è sicuramente assegnato.
    * In caso contrario, se lo stato di *v* dopo *expr_second* è assegnato definitivamente o "assegnato definitivamente dopo l'espressione true", lo stato di *v* dopo *expr* è "definitely Assigned after true Expression".
    * In caso contrario, se lo stato di *v* dopo *expr_first* è "definitely Assigned after false Expression" e lo stato di *v* dopo *expr_second* è "definitely Assigned after false Expression", lo stato di *v* dopo  *expr* è "assegnato definitivamente dopo false Expression".
    * In caso contrario, lo stato di *v* dopo *expr* non viene assegnato definitivamente.

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
la variabile `i` viene considerata definitivamente assegnata in una delle istruzioni incorporate di `if` un'istruzione ma non nell'altra. Nell'istruzione nel metodo `F`, la variabile `i` viene assegnata definitivamente nella prima istruzione incorporata perché l'esecuzione dell'espressione `(i = y)` precede sempre l'esecuzione di questa istruzione incorporata. `if` Al contrario, la variabile `i` non viene assegnata definitivamente nella seconda istruzione incorporata, `x >= 0` perché potrebbe avere testato false, causando la `i` mancata assegnazione della variabile.

#### <a name="-conditional-or-expressions"></a>|| espressioni (condizionali o)

Per un'espressione *expr* nel formato `expr_first || expr_second`:

*  Lo stato di assegnazione definito di *v* prima di *expr_first* è lo stesso dello stato di assegnazione definito di *v* prima di *expr*.
*  Lo stato di assegnazione definito di *v* prima di *expr_second* viene assegnato definitivamente se lo stato di *v* dopo *expr_first* è definitivamente assegnato o "assegnato definitivamente dopo false Expression". In caso contrario, non viene assegnato definitivamente.
*  L'istruzione di assegnazione definita di *v* dopo *expr* è determinata da:
    * Se *expr_first* è un'espressione costante con il valore `true`, lo stato di assegnazione definito di *v* dopo *expr* corrisponde allo stato di assegnazione definito di *v* dopo *expr_first*.
    * In caso contrario, se lo stato di *v* dopo *expr_first* è assegnato definitivamente, lo stato di *v* dopo *expr* viene assegnato definitivamente.
    * In caso contrario, se lo stato di *v* dopo *expr_second* è assegnato definitivamente e lo stato di *v* dopo *expr_first* è "definitivamente assegnato dopo l'espressione true", lo stato di *v* dopo *expr* è sicuramente assegnato.
    * In caso contrario, se lo stato di *v* dopo *expr_second* è assegnato definitivamente o "assegnato definitivamente dopo false Expression", lo stato di *v* dopo *expr* sarà "definitely Assigned after false Expression".
    * In caso contrario, se lo stato di *v* dopo *expr_first* è "assegnato definitivamente dopo l'espressione true" e lo stato di *v* dopo *expr_second* è "definitivamente assegnato dopo l'espressione true", lo stato di *v* dopo *expr* è "assegnato definitivamente dopo l'espressione true".
    * In caso contrario, lo stato di *v* dopo *expr* non viene assegnato definitivamente.

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
la variabile `i` viene considerata definitivamente assegnata in una delle istruzioni incorporate di `if` un'istruzione ma non nell'altra. Nell'istruzione nel metodo `G`, la variabile `i` viene assegnata definitivamente nella seconda istruzione incorporata perché l'esecuzione dell'espressione `(i = y)` precede sempre l'esecuzione di questa istruzione incorporata. `if` Al contrario, la variabile `i` non viene assegnata definitivamente nella prima istruzione incorporata, `x >= 0` perché potrebbe avere testato true, causando la `i` mancata assegnazione della variabile.

#### <a name="-logical-negation-expressions"></a>! espressioni (negazione logica)

Per un'espressione *expr* nel formato `! expr_operand`:

*  Lo stato di assegnazione definito di *v* prima di *expr_operand* è lo stesso dello stato di assegnazione definito di *v* prima di *expr*.
*  Lo stato di assegnazione definito di *v* dopo *expr* è determinato da:
    * Se lo stato di *v* after * expr_operand * viene assegnato definitivamente, lo stato di *v* dopo *expr* viene assegnato definitivamente.
    * Se lo stato di *v* after * expr_operand * non è assegnato definitivamente, lo stato di *v* dopo *expr* non viene assegnato definitivamente.
    * Se lo stato di *v* after * expr_operand * è "definitely Assigned after false Expression", lo stato di *v* after *expr* è "definitely Assigned after true Expression".
    * Se lo stato di *v* after * expr_operand * è "assegnato definitivamente dopo l'espressione true", lo stato di *v* dopo *expr* è "definitely Assigned after false Expression".

#### <a name="-null-coalescing-expressions"></a>?? espressioni (Unione di valori null)

Per un'espressione *expr* nel formato `expr_first ?? expr_second`:

*  Lo stato di assegnazione definito di *v* prima di *expr_first* è lo stesso dello stato di assegnazione definito di *v* prima di *expr*.
*  Lo stato di assegnazione definito di *v* prima di *expr_second* è lo stesso dello stato di assegnazione definito di *v* dopo *expr_first*.
*  L'istruzione di assegnazione definita di *v* dopo *expr* è determinata da:
    * Se *expr_first* è un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)) con valore null, lo stato di *v* dopo *expr* sarà lo stesso dello stato *v* dopo *expr_second*.
*  In caso contrario, lo stato di *v* dopo *expr* corrisponde allo stato di assegnazione definito di *v* dopo *expr_first*.

#### <a name="-conditional-expressions"></a>espressioni?: (condizionale)

Per un'espressione *expr* nel formato `expr_cond ? expr_true : expr_false`:

*  Lo stato di assegnazione definito di *v* prima di *expr_cond* è uguale allo stato di *v* prima di *expr*.
*  Lo stato di assegnazione definito di *v* prima di *expr_true* viene assegnato definitivamente se e solo se uno degli elementi seguenti include:
    * *expr_cond* è un'espressione costante con il valore`false`
    * lo stato di *v* dopo *expr_cond* è assegnato definitivamente o "assegnato definitivamente dopo l'espressione true".
*  Lo stato di assegnazione definito di *v* prima di *expr_false* viene assegnato definitivamente se e solo se uno degli elementi seguenti include:
    * *expr_cond* è un'espressione costante con il valore`true`
*  lo stato di *v* dopo *expr_cond* è assegnato definitivamente o "assegnato definitivamente dopo false Expression".
*  Lo stato di assegnazione definito di *v* dopo *expr* è determinato da:
    * Se *expr_cond* è un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)) con valore `true` , lo stato di *v* dopo *expr* sarà lo stesso dello stato *v* dopo *expr_true*.
    * In caso contrario, se *expr_cond* è un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)) `false` con valore, lo stato di *v* dopo *expr* sarà lo stesso dello stato *v* dopo *expr_false*.
    * In caso contrario, se lo stato di *v* dopo *expr_true* è assegnato definitivamente e lo stato di *v* dopo *expr_false* è definitivamente assegnato, lo stato di *v* dopo *expr* viene assegnato definitivamente.
    * In caso contrario, lo stato di *v* dopo *expr* non viene assegnato definitivamente.

#### <a name="anonymous-functions"></a>Funzioni anonime

Per *lambda_expression* o *anonymous_method_expression* *expr* *con corpo (* *blocco* o *espressione*):

*  Lo stato di assegnazione definito di una variabile esterna *v* before *Body* corrisponde allo stato di *v* prima di *expr*. Ovvero lo stato di assegnazione definito delle variabili esterne viene ereditato dal contesto della funzione anonima.
*  Lo stato di assegnazione definito di una variabile esterna *v* dopo *expr* è identico a quello di *v* prima di *expr*.

Esempio
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
genera un errore in fase di compilazione `max` perché non è assegnato definitivamente quando viene dichiarata la funzione anonima. Esempio
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
genera inoltre un errore in fase di compilazione perché l'assegnazione `n` a nella funzione anonima non ha alcun effetto sullo stato di `n` assegnazione definito all'esterno della funzione anonima.

## <a name="variable-references"></a>Riferimenti a variabili

Un *variable_reference* è un' *espressione* classificata come variabile. Un *variable_reference* indica un percorso di archiviazione a cui è possibile accedere sia per recuperare il valore corrente sia per archiviare un nuovo valore.

```antlr
variable_reference
    : expression
    ;
```

In C e C++un *variable_reference* è noto come *lvalue*.

## <a name="atomicity-of-variable-references"></a>Atomicità dei riferimenti a variabili

Le letture e le scritture dei tipi di dati seguenti `bool`sono `char`atomiche `sbyte`: `short` `byte`,,, `int`, `float`, `ushort`, `uint`,, e tipi di riferimento. Inoltre, le letture e le scritture dei tipi enum con un tipo sottostante nell'elenco precedente sono anche atomiche. Le letture e le scritture di altri `long`tipi `ulong`, tra cui `decimal`,, `double`e, nonché i tipi definiti dall'utente, non sono necessariamente atomiche. Oltre alle funzioni di libreria progettate a tale scopo, non esiste alcuna garanzia di Atomic read-modify-write, ad esempio in caso di incremento o decremento.

