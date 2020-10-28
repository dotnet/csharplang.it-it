---
ms.openlocfilehash: 7ee72a2e3171940e93a2dc7048136a7ddc6b7698
ms.sourcegitcommit: 75ea1d741f92d6fda117caa189db17370f1b9f1c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92684059"
---
# <a name="recursive-pattern-matching"></a>Criteri di ricerca ricorsivi

## <a name="summary"></a>Riepilogo
[summary]: #summary

Le estensioni di criteri di ricerca per C# consentono molti dei vantaggi dei tipi di dati algebrici e dei criteri di ricerca dai linguaggi funzionali, ma in modo da integrarsi perfettamente con l'aspetto del linguaggio sottostante. Gli elementi di questo approccio sono ispirati dalle funzionalità correlate nei linguaggi di programmazione [F #](http://www.msr-waypoint.net/pubs/79947/p29-syme.pdf "Criteri di ricerca estendibili tramite un linguaggio leggero") e [scala](https://link.springer.com/content/pdf/10.1007%2F978-3-540-73589-2.pdf "Corrispondenza di oggetti con modelli, pagina 273").

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

### <a name="is-expression"></a>Espressione is

L' `is` operatore viene esteso per testare un'espressione rispetto a un *criterio* .

```antlr
relational_expression
    : is_pattern_expression
    ;
is_pattern_expression
    : relational_expression 'is' pattern
    ;
```

Questa forma di *relational_expression* è aggiunta ai moduli esistenti nella specifica C#. Si tratta di un errore in fase di compilazione se il *relational_expression* a sinistra del `is` token non designa un valore o non ha un tipo.

Ogni *identificatore* del modello introduce una nuova variabile locale *assegnata definitivamente* dopo l' `is` operatore `true` (ovvero *assegnata definitivamente quando è true* ).

> Nota: esiste tecnicamente un'ambiguità tra il *tipo* in `is-expression` e *constant_pattern* , una delle quali potrebbe essere un'analisi valida di un identificatore qualificato. Si tenta di associarlo come tipo per la compatibilità con le versioni precedenti del linguaggio; solo se l'operazione ha esito negativo, viene risolta quando si esegue un'espressione in altri contesti, fino alla prima cosa trovata (che deve essere una costante o un tipo). Questa ambiguità è presente solo sul lato destro di un' `is` espressione.

### <a name="patterns"></a>Modelli

Gli schemi vengono usati nell'operatore *is_pattern* , in una *switch_Statement* e in un *switch_expression* per esprimere la forma dei dati in base ai dati in ingresso (che chiameremo il valore di input). I modelli possono essere ricorsivi in modo che sia possibile trovare una corrispondenza tra parti dei dati e modelli secondari.

```antlr
pattern
    : declaration_pattern
    | constant_pattern
    | var_pattern
    | positional_pattern
    | property_pattern
    | discard_pattern
    ;
declaration_pattern
    : type simple_designation
    ;
constant_pattern
    : constant_expression
    ;
var_pattern
    : 'var' designation
    ;
positional_pattern
    : type? '(' subpatterns? ')' property_subpattern? simple_designation?
    ;
subpatterns
    : subpattern
    | subpattern ',' subpatterns
    ;
subpattern
    : pattern
    | identifier ':' pattern
    ;
property_subpattern
    : '{' '}'
    | '{' subpatterns ','? '}'
    ;
property_pattern
    : type? property_subpattern simple_designation?
    ;
simple_designation
    : single_variable_designation
    | discard_designation
    ;
discard_pattern
    : '_'
    ;
```

#### <a name="declaration-pattern"></a>Modello di dichiarazione

```antlr
declaration_pattern
    : type simple_designation
    ;
```

Il *declaration_pattern* verifica che un'espressione sia di un tipo specificato e ne esegue il cast a tale tipo se il test ha esito positivo. Questo può introdurre una variabile locale del tipo specificato denominato dall'identificatore specificato, se la designazione è un *single_variable_designation* . Tale variabile locale viene *assegnata definitivamente* quando il risultato dell'operazione di corrispondenza dei modelli è `true` .

La semantica di runtime di questa espressione è che testa il tipo di runtime dell'operando di sinistra *relational_expression* sul *tipo* nel modello.  Se è di quel tipo di runtime (o di un sottotipo) e non `null` , il risultato di `is operator` è `true` .

Alcune combinazioni di tipo statico del lato sinistro e del tipo specificato vengono considerate incompatibili e generano un errore in fase di compilazione. Un valore di tipo statico `E` viene definito *modello compatibile* con un tipo `T` se esiste una conversione di identità, una conversione di un riferimento implicito, una conversione boxing, una conversione esplicita di un riferimento o una conversione unboxing da `E` a `T` oppure se uno di questi tipi è un tipo aperto. Si tratta di un errore in fase di compilazione se un input di tipo `E` non è compatibile con i *modelli* con il *tipo* in un modello di tipo con cui corrisponde.

Il modello di tipo è utile per l'esecuzione di test dei tipi di riferimento in fase di esecuzione e sostituisce l'idioma

```csharp
var v = expr as Type;
if (v != null) { // code using v
```

Con l'aspetto leggermente più conciso

```csharp
if (expr is Type v) { // code using v
```

Si tratta di un errore se il *tipo* è un tipo di valore Nullable.

Il modello di tipo può essere usato per testare i valori dei tipi nullable: un valore di tipo `Nullable<T>` (o un oggetto boxed `T` ) corrisponde a un modello di tipo `T2 id` se il valore è non null e il tipo di `T2` è `T` o di un tipo o di un'interfaccia di base di `T` . Ad esempio, nel frammento di codice

```csharp
int? x = 3;
if (x is int v) { // code using v
```

La condizione dell' `if` istruzione è in fase di `true` esecuzione e la variabile `v` include il valore `3` di tipo `int` all'interno del blocco. Dopo il blocco, la variabile `v` è nell'ambito ma non è definitivamente assegnata.

#### <a name="constant-pattern"></a>Criterio costante

```antlr
constant_pattern
    : constant_expression
    ;
```

Un criterio costante verifica il valore di un'espressione rispetto a un valore costante. La costante può essere qualsiasi espressione costante, ad esempio un valore letterale, il nome di una variabile dichiarata `const` o una costante di enumerazione. Quando il valore di input non è un tipo aperto, l'espressione costante viene convertita in modo implicito nel tipo dell'espressione corrispondente. Se il tipo del valore di input non è *compatibile* con il modello con il tipo dell'espressione costante, l'operazione di corrispondenza dei modelli è un errore.

Il modello *c* viene considerato corrispondente al valore di input convertito *e* se `object.Equals(c, e)` restituisce `true` .

Si prevede di vedere `e is null` come il modo più comune per eseguire il test `null` nel codice appena scritto, perché non può richiamare un definito dall'utente `operator==` .

#### <a name="var-pattern"></a>Modello var

```antlr
var_pattern
    : 'var' designation
    ;
designation
    : simple_designation
    | tuple_designation
    ;
simple_designation
    : single_variable_designation
    | discard_designation
    ;
single_variable_designation
    : identifier
    ;
discard_designation
    : _
    ;
tuple_designation
    : '(' designations? ')'
    ;
designations
    : designation
    | designations ',' designation
    ;
```

Se la *designazione* è una *simple_designation* , un'espressione *e* corrisponde al modello. In altre parole, una corrispondenza con un *modello var* ha sempre esito positivo con un *simple_designation* . Se il *simple_designation* è un *single_variable_designation* , il valore di *e* viene associato a una variabile locale appena introdotta. Il tipo della variabile locale è il tipo statico di *e* .

Se la *designazione* è un *tuple_designation* , il modello è equivalente a una *positional_pattern* della `(var` *designazione* del modulo,... `)` dove le *designazioni* sono quelle trovate all'interno dell' *tuple_designation* .  Ad esempio, il modello `var (x, (y, z))` è equivalente a `(var x, (var y, var z))` .

Si tratta di un errore se il nome viene `var` associato a un tipo.

#### <a name="discard-pattern"></a>Elimina modello

```antlr
discard_pattern
    : '_'
    ;
```

Un'espressione *e* corrisponde sempre al modello `_` . In altre parole, ogni espressione corrisponde al modello di scarto.

Un modello di scarto non può essere usato come modello di un *is_pattern_expression* .

#### <a name="positional-pattern"></a>Modello posizionale

Un criterio posizionale verifica che il valore di input non sia `null` , richiama un metodo appropriato `Deconstruct` ed esegue ulteriori criteri di ricerca sui valori risultanti.  Supporta inoltre una sintassi del modello simile a una tupla (senza il tipo fornito) quando il tipo del valore di input è uguale al tipo che contiene `Deconstruct` o se il tipo del valore di input è un tipo di tupla o se il tipo del valore di input è `object` o `ITuple` e il tipo di runtime dell'espressione implementa `ITuple` .

```antlr
positional_pattern
    : type? '(' subpatterns? ')' property_subpattern? simple_designation?
    ;
subpatterns
    : subpattern
    | subpattern ',' subpatterns
    ;
subpattern
    : pattern
    | identifier ':' pattern
    ;
```

Se il *tipo* viene omesso, lo consideriamo come il tipo statico del valore di input.

Data la corrispondenza di un valore di input con il *tipo* di pattern `(` *subpattern_list* `)` , viene selezionato un metodo eseguendo una ricerca nel *tipo* per le dichiarazioni accessibili `Deconstruct` e selezionando una tra di esse usando le stesse regole della dichiarazione di decostruzione.

Se un *positional_pattern* omette il tipo, presenta un solo *sottomodello* senza *identificatore* , non ha *property_subpattern* e non ha *simple_designation* . Questo ambiguità tra un *constant_pattern* racchiuso tra parentesi e una *positional_pattern* .

Per estrarre i valori in base ai criteri dell'elenco,
- Se il *tipo* è stato omesso e il tipo del valore di input è un tipo di tupla, il numero di sottomodelli deve corrispondere alla cardinalità della tupla. Ogni elemento di tupla viene associato al *criterio secondario* corrispondente e la corrispondenza ha esito positivo se tutti gli elementi hanno esito positivo. Se un *sottomodello* ha un *identificatore* , deve denominare un elemento tupla nella posizione corrispondente nel tipo di tupla.
- In caso contrario, se un oggetto appropriato `Deconstruct` esiste come membro di *tipo* , si verifica un errore in fase di compilazione se il tipo del valore di input non è compatibile con i *modelli* con il *tipo* . In fase di esecuzione il valore di input viene testato rispetto al *tipo* . Se questa operazione ha esito negativo, la corrispondenza del criterio posizionale ha esito negativo. Se ha esito positivo, il valore di input viene convertito in questo tipo e `Deconstruct` viene richiamato con le nuove variabili generate dal compilatore per ricevere i `out` parametri. Ogni valore ricevuto viene individuato in base al *criterio secondario* corrispondente e la corrispondenza ha esito positivo se tutti questi valori hanno esito positivo. Se un *sottomodello* ha un *identificatore* , deve denominare un parametro nella posizione corrispondente di `Deconstruct` .
- In caso contrario, se il *tipo* è stato omesso e il valore di input è di tipo `object` o `ITuple` o di un tipo che può essere convertito in `ITuple` mediante una conversione implicita di riferimento e non viene visualizzato alcun *identificatore* tra i modelli, viene trovata una corrispondenza con `ITuple` .
- In caso contrario, il modello è un errore in fase di compilazione.

L'ordine in cui i criteri di ricerca vengono confrontati in fase di esecuzione non è specificato e una corrispondenza non riuscita potrebbe non tentare di trovare la corrispondenza con tutti i sottomodelli.

##### <a name="example"></a>Esempio

Questo esempio usa molte delle funzionalità descritte in questa specifica

``` c#
    var newState = (GetState(), action, hasKey) switch {
        (DoorState.Closed, Action.Open, _) => DoorState.Opened,
        (DoorState.Opened, Action.Close, _) => DoorState.Closed,
        (DoorState.Closed, Action.Lock, true) => DoorState.Locked,
        (DoorState.Locked, Action.Unlock, true) => DoorState.Closed,
        (var state, _, _) => state };
```

#### <a name="property-pattern"></a>Modello di proprietà

Un modello di proprietà controlla che il valore di input non sia `null` e che corrisponda in modo ricorsivo ai valori estratti dall'uso di proprietà o campi accessibili.

```antlr
property_pattern
    : type? property_subpattern simple_designation?
    ;
property_subpattern
    : '{' '}'
    | '{' subpatterns ','? '}'
    ;
```

È un errore se un _sottomodello_ di un _property_pattern_ non contiene un _identificatore_ (deve essere nel secondo formato, che ha un _identificatore_ ).  Una virgola finale dopo l'ultimo criterio secondario è facoltativa.

Si noti che un modello di controllo null rientra in un modello di proprietà semplice. Per verificare se la stringa `s` è non null, è possibile scrivere uno dei moduli seguenti

```csharp
if (s is object o) ... // o is of type object
if (s is string x) ... // x is of type string
if (s is {} x) ... // x is of type string
if (s is {}) ...
```

Data la corrispondenza di un'espressione *e* con il *tipo* di pattern `{` *property_pattern_list* `}` , si tratta di un errore in fase di compilazione se l'espressione *e* non è compatibile con i *modelli* con il tipo *T* designato dal *tipo* . Se il tipo è assente, viene importato come tipo statico di *e* . Se l' *identificatore* è presente, viene dichiarata una variabile di modello *di tipo Type.* Ognuno degli identificatori visualizzati sul lato sinistro della relativa *property_pattern_list* deve designare una proprietà leggibile accessibile o un campo di *T* . Se è presente la *simple_designation* del *property_pattern* , definisce una variabile di tipo pattern *T* .

In fase di esecuzione, l'espressione viene testata rispetto a *T* . Se l'operazione ha esito negativo, il modello di proprietà non riesce e il risultato è `false` . Se ha esito positivo, ogni campo *property_subpattern* o proprietà viene letto e il relativo valore corrisponde al criterio corrispondente. Il risultato dell'intera corrispondenza è `false` solo se il risultato di uno di questi è `false` . L'ordine in cui vengono confrontati i modelli non è specificato e una corrispondenza non riuscita potrebbe non corrispondere a tutti i sottomodelli in fase di esecuzione. Se la corrispondenza ha esito positivo e il *simple_designation* del *property_pattern* è un *single_variable_designation* , definisce una variabile di tipo *T* a cui viene assegnato il valore corrispondente.

> Nota: il modello di proprietà può essere usato per la corrispondenza dei modelli con i tipi anonimi.

##### <a name="example"></a>Esempio

```csharp
if (o is string { Length: 5 } s)
```

### <a name="switch-expression"></a>Espressione switch

Viene aggiunta una *switch_expression* alla `switch` semantica di tipo supporto per un contesto dell'espressione.

La sintassi del linguaggio C# è aumentata con le seguenti produzioni sintattiche:

```antlr
multiplicative_expression
    : switch_expression
    | multiplicative_expression '*' switch_expression
    | multiplicative_expression '/' switch_expression
    | multiplicative_expression '%' switch_expression
    ;
switch_expression
    : range_expression 'switch' '{' '}'
    | range_expression 'switch' '{' switch_expression_arms ','? '}'
    ;
switch_expression_arms
    : switch_expression_arm
    | switch_expression_arms ',' switch_expression_arm
    ;
switch_expression_arm
    : pattern case_guard? '=>' expression
    ;
case_guard
    : 'when' null_coalescing_expression
    ;
```

Il *switch_expression* non è consentito come *expression_statement* .

> In una revisione futura si sta cercando di rilassarvi.

Il tipo di *switch_expression* è il [*tipo comune migliore*](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) delle espressioni visualizzate a destra dei `=>` token della *switch_expression_arm* s se tale tipo esiste e l'espressione in ogni ARM dell'espressione switch può essere convertita in modo implicito in tale tipo.  Viene inoltre aggiunta una nuova conversione dell' *espressione switch* , ovvero una conversione implicita predefinita da un'espressione switch a ogni tipo per il `T` quale esiste una conversione implicita dall'espressione di ogni ARM a `T` .

Si tratta di un errore se il modello di un *switch_expression_arm* non può influenzare il risultato perché alcuni criteri e Guard precedenti corrisponderanno sempre.

Un'espressione switch è definita *esauriente* se un ARM dell'espressione switch gestisce ogni valore dell'input.  Il compilatore genera un avviso se un'espressione switch non è *completa* .

In fase di esecuzione, il risultato della *switch_expression* è il valore dell' *espressione* del primo *switch_expression_arm* per il quale l'espressione sul lato sinistro del *switch_expression* corrisponde al modello del *switch_expression_arm* e per il quale il *case_guard* del *switch_expression_arm* , se presente, restituisce `true` . Se non esiste un *switch_expression_arm* di questo tipo, il *switch_expression* genera un'istanza dell'eccezione `System.Runtime.CompilerServices.SwitchExpressionException` .

### <a name="optional-parens-when-switching-on-a-tuple-literal"></a>Parentesi opzionali quando si passa a un valore letterale di tupla

Per attivare un valore letterale di tupla usando il *switch_Statement* , è necessario scrivere le parentesi che sembrano essere ridondanti

```csharp
switch ((a, b))
{
```

Per consentire

```csharp
switch (a, b)
{
```

le parentesi dell'istruzione switch sono facoltative quando l'espressione da attivare è un valore letterale di tupla.

### <a name="order-of-evaluation-in-pattern-matching"></a>Ordine di valutazione nella corrispondenza dei modelli

Fornire la flessibilità del compilatore per riordinare le operazioni eseguite durante la corrispondenza dei modelli può consentire la flessibilità che può essere usata per migliorare l'efficienza della corrispondenza dei modelli. Il requisito (non applicato) prevede che le proprietà a cui si accede in un modello e i metodi di decostruzione debbano essere "pure" (effetto collaterale gratuito, idempotente e così via). Ciò non significa che verrà aggiunta la purezza come concetto di linguaggio, ma solo che la flessibilità del compilatore verrà riordinata.

**Risoluzione 2018-04-04 LDM** : confermato: il compilatore è autorizzato a riordinare le chiamate a `Deconstruct` , gli accessi alle proprietà e le chiamate dei metodi in `ITuple` e può presumere che i valori restituiti siano gli stessi da più chiamate. Il compilatore non deve richiamare funzioni che non possono influire sul risultato e sarà necessario prestare particolare attenzione prima di apportare eventuali modifiche all'ordine di valutazione generato dal compilatore in futuro.

### <a name="some-possible-optimizations"></a>Alcune possibili ottimizzazioni

La compilazione di criteri di ricerca può trarre vantaggio da parti comuni di modelli. Se, ad esempio, il test del tipo di primo livello di due modelli successivi in una *switch_Statement* è dello stesso tipo, il codice generato può ignorare il test del tipo per il secondo modello.

Quando alcuni dei modelli sono numeri interi o stringhe, il compilatore può generare lo stesso tipo di codice generato per un'istruzione switch nelle versioni precedenti del linguaggio.

Per ulteriori informazioni su questi tipi di ottimizzazioni, vedere [[Scott e Ramsey (2000)]](https://www.cs.tufts.edu/~nr/cs257/archive/norman-ramsey/match.pdf "Quando Match-Compilation euristica è importante?").
