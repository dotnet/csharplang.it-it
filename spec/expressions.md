# <a name="expressions"></a>Espressioni

Un'espressione è una sequenza di operatori e operandi. In questo capitolo definisce la sintassi, l'ordine di valutazione di operandi e operatori e il significato di espressioni.

## <a name="expression-classifications"></a>Classificazioni delle espressioni

Un'espressione viene classificata nei seguenti modi:

*  Un valore. Ad ogni valore è associato un tipo.
*  Una variabile. Tutte le variabili sono associato un tipo, ovvero il tipo dichiarato della variabile.
*  Spazio dei nomi. Un'espressione con questa classificazione può solo apparire come lato sinistro di un *member_access* ([l'accesso ai membri](expressions.md#member-access)). In qualsiasi altro contesto, un'espressione classificata come uno spazio dei nomi genera un errore in fase di compilazione.
*  Tipo. Un'espressione con questa classificazione può solo apparire come lato sinistro di un *member_access* ([l'accesso ai membri](expressions.md#member-access)), o come operando per il `as` operatore ([l'operatore ](expressions.md#the-as-operator)), il `is` operatore ([l'operatore is](expressions.md#the-is-operator)), o la `typeof` operatore ([l'operatore typeof](expressions.md#the-typeof-operator)). In qualsiasi altro contesto, un'espressione classificata come un tipo causa un errore in fase di compilazione.
*  Un gruppo di metodi, ovvero un set di metodi di overload risultante da una ricerca di membri ([ricerca di membri](expressions.md#member-lookup)). Un gruppo di metodi potrebbe essere un'espressione di istanza e un elenco di argomenti tipo associato. Quando viene richiamato un metodo di istanza, il risultato della valutazione dell'espressione dell'istanza diventa l'istanza rappresentata dal `this` ([questo accesso](expressions.md#this-access)). Un gruppo di metodi è consentito in un' *invocation_expression* ([espressioni di chiamata](expressions.md#invocation-expressions)), una *delegate_creation_expression* ([delegare la creazione le espressioni](expressions.md#delegate-creation-expressions)) e il lato sinistro di un operatore e può essere convertito in modo implicito in un tipo delegato compatibile ([conversioni dei metodi di gruppo](conversions.md#method-group-conversions)). In qualsiasi altro contesto, un'espressione classificata come un gruppo di metodi provoca un errore in fase di compilazione.
*  Un valore letterale null. Un'espressione con questa classificazione può essere convertita in modo implicito in un tipo riferimento o nullable.
*  Una funzione anonima. Un'espressione con questa classificazione può essere convertita in modo implicito a un tipo delegato compatibile o un tipo di albero delle espressioni.
*  Accesso alla proprietà. Ogni accesso a proprietà è associato un tipo, ovvero il tipo della proprietà. Inoltre, un accesso a proprietà potrebbe essere un'espressione dell'istanza associata. Quando una funzione di accesso (il `get` o `set` block) di un'istanza viene richiamato l'accesso alle proprietà, il risultato della valutazione dell'espressione dell'istanza diventa l'istanza rappresentata dal `this` ([questo accesso](expressions.md#this-access)).
*  Accesso a un evento. Ogni accesso all'evento è associato un tipo, ovvero il tipo dell'evento. Accesso a un evento, inoltre, potrebbe essere un'espressione dell'istanza associata. Accesso a un evento potrebbe essere visualizzato come operando di sinistra del `+=` e `-=` operatori ([assegnazione evento](expressions.md#event-assignment)). In qualsiasi altro contesto, un'espressione classificata come un accesso a eventi genera un errore in fase di compilazione.
*  Accesso all'indicizzatore. Ogni accesso all'indicizzatore è associato un tipo, ovvero il tipo di elemento dell'indicizzatore. Inoltre, un accesso all'indicizzatore ha un'espressione di istanza e un elenco di argomenti. Quando una funzione di accesso (il `get` oppure `set` blocco) di un indicizzatore accesso viene richiamato, il risultato della valutazione dell'espressione dell'istanza diventa l'istanza rappresentata dal `this` ([questo accesso](expressions.md#this-access)) e il risultato di l'elenco di argomenti di valutazione diventa l'elenco di parametri della chiamata.
*  Nessun elemento. Ciò si verifica quando l'espressione è una chiamata di un metodo con un tipo restituito `void`. Un'espressione classificata come non è valido solo nel contesto di un *statement_expression* ([le istruzioni di espressione](statements.md#expression-statements)).

Il risultato finale di un'espressione è mai un spazio dei nomi, tipo, il gruppo di metodi o l'accesso agli eventi. Piuttosto, come indicato in precedenza, queste categorie di espressioni sono costrutti intermedi che sono consentiti solo in determinati contesti.

Accesso a un accesso a proprietà o indicizzatore è sempre riclassificato come valore mediante l'esecuzione di una chiamata del *funzione di accesso get* o nella *funzione di accesso set*. La funzione di accesso specifico è determinato dal contesto dell'accesso alla proprietà o indicizzatore: Se l'accesso è la destinazione di un'assegnazione, il *funzione di accesso set* viene richiamata per assegnare un nuovo valore ([assegnamento semplice](expressions.md#simple-assignment)). In caso contrario, il *funzione di accesso get* viene richiamato per ottenere il valore corrente ([i valori delle espressioni](expressions.md#values-of-expressions)).

### <a name="values-of-expressions"></a>Valori delle espressioni

La maggior parte dei Construct che comportano un'espressione in definitiva richiede l'espressione per indicare una ***valore***. In questi casi, se l'espressione effettiva indica uno spazio dei nomi, un tipo, un gruppo di metodi o nothing, si verifica un errore in fase di compilazione. Tuttavia, se l'espressione indica un accesso a proprietà, un accesso all'indicizzatore o una variabile, il valore della proprietà, indicizzatore o variabile viene sostituito in modo implicito:

*  Il valore di una variabile è semplicemente il valore attualmente archiviato nella posizione identificata dalla variabile. Deve essere considerata una variabile assegnata ([assegnazione certa](variables.md#definite-assignment)) prima che il relativo valore può essere ottenuto o in caso contrario, verrà generato un errore in fase di compilazione.
*  Il valore di un'espressione di accesso proprietà ottenuto chiamando il *funzione di accesso get* della proprietà. Se la proprietà non ha alcun *funzione di accesso get*, si verifica un errore in fase di compilazione. In caso contrario, la chiamata di un membro di funzione ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) viene eseguita, e il risultato della chiamata diventa il valore dell'espressione di accesso di proprietà.
*  Il valore di un'espressione di accesso dell'indicizzatore ottenuto chiamando il *funzione di accesso get* dell'indicizzatore. Se l'indicizzatore non ha alcun *funzione di accesso get*, si verifica un errore in fase di compilazione. In caso contrario, la chiamata di un membro di funzione ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) viene eseguita con l'argomento elenco associato all'espressione di accesso dell'indicizzatore, e il risultato della chiamata diventa il valore dell'espressione di accesso dell'indicizzatore.

## <a name="static-and-dynamic-binding"></a>Binding statico e dinamico

Il processo volto a determinare il significato di un'operazione in base al tipo o valore di espressioni che costituiscono (argomenti, gli operandi, ricevitori) è spesso detta ***associazione***. Ad esempio il significato di una chiamata al metodo è determinato in base al tipo di ricevitore e argomenti. Il significato di un operatore è determinato in base al tipo dei relativi operandi.

In c# il significato di un'operazione in genere è determinato in fase di compilazione, in base al tipo di fase di compilazione delle relative espressioni che lo costituiscono. Analogamente, se un'espressione contiene un errore, l'errore viene rilevato e segnalato dal compilatore. Questo approccio è noto come ***associazioni statiche***.

Tuttavia, se un'espressione è un'espressione dinamica (ad esempio è di tipo `dynamic`) indica che qualsiasi associazione che fa parte deve essere basati sul relativo tipo in fase di esecuzione (ad esempio il tipo effettivo dell'oggetto indica al runtime) anziché il tipo nel in fase di compilazione. Pertanto, l'associazione di tale operazione viene posticipata fino al momento in cui l'operazione deve essere eseguito durante l'esecuzione del programma. Ciò è detto ***binding dinamico***.

Quando un'operazione in modo dinamico è associata, senza alcuna verifica viene eseguita dal compilatore. Se, invece l'associazione di runtime non riesce, gli errori vengono segnalati come eccezioni in fase di esecuzione.

Le operazioni seguenti nel linguaggio c# sono soggetti a binding:

*  Accesso ai membri: `e.M`
*  Chiamata del metodo: `e.M(e1, ..., eN)`
*  Chiamata a un delegato:`e(e1, ..., eN)`
*  Accesso all'elemento: `e[e1, ..., eN]`
*  Creazione di oggetti: `new C(e1, ..., eN)`
*  Sottoposto a overload gli operatori unari: `+`, `-`, `!`, `~`, `++`, `--`, `true`, `false`
*  Sottoposto a overload gli operatori binari: `+`, `-`, `*`, `/`, `%`, `&`, `&&`, `|`, `||`, `??`, `^`, `<<` , `>>`, `==`,`!=`, `>`, `<`, `>=`, `<=`
*  Gli operatori di assegnazione: `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|=`, `^=`, `<<=`, `>>=`
*  Conversioni implicite ed esplicite

Quando non sono coinvolte espressioni dinamiche, c# impostata automaticamente su associazioni statiche, il che significa che vengono utilizzati i tipi in fase di compilazione di espressioni che costituiscono il processo di selezione. Tuttavia, quando una delle espressioni che costituiscono nelle operazioni elencate in precedenza è un'espressione dinamica, l'operazione viene invece associata in modo dinamico.

### <a name="binding-time"></a>In fase di associazione

Associazione statica ha luogo in fase di compilazione, mentre l'associazione dinamica viene eseguita in fase di esecuzione. Nelle sezioni seguenti, il termine ***in fase di associazione*** fa riferimento alla fase di compilazione o in fase di esecuzione, a seconda di quando avviene l'associazione.

L'esempio seguente illustra i concetti di binding statico e dinamico e in fase di associazione:
```csharp
object  o = 5;
dynamic d = 5;

Console.WriteLine(5);  // static  binding to Console.WriteLine(int)
Console.WriteLine(o);  // static  binding to Console.WriteLine(object)
Console.WriteLine(d);  // dynamic binding to Console.WriteLine(int)
```

Le prime due chiamate vengono associate in modo statico: l'overload del metodo `Console.WriteLine` viene selezionato in base al tipo di fase di compilazione del relativo argomento. Pertanto, la fase di associazione è in fase di compilazione.

La terza chiamata è associata in modo dinamico: l'overload del metodo `Console.WriteLine` viene selezionato in base al tipo di fase di esecuzione del relativo argomento. Ciò accade perché l'argomento è un'espressione dinamica: il tipo di fase di compilazione `dynamic`. Pertanto, la fase di associazione per la terza chiamata è in fase di esecuzione.

### <a name="dynamic-binding"></a>Associazione dinamica

Lo scopo di associazione dinamica è consentire ai programmi c# di interagirvi ***oggetti dinamici***, vale a dire gli oggetti che non seguono le regole normali di C# sistema di tipi. Oggetti dinamici possono essere oggetti di altri linguaggi di programmazione con i sistemi di tipi diversi, o possono essere oggetti che sono a livello di codice per implementare la propria semantica di associazione per diversi di operazioni di installazione.

Il meccanismo mediante il quale un oggetto dinamico implementa la propria semantica è definito dall'implementazione. Una determinata interfaccia, anche in questo caso implementazione definita, viene implementata dagli oggetti dinamici per segnalare il codice c# in fase di esecuzione che hanno una semantica speciale. Di conseguenza, ogni volta che le operazioni su un oggetto dinamico di associazione in modo dinamico, la propria semantica di associazione, anziché quelle del linguaggio c# come specificato in questo documento, intervenire.

Mentre lo scopo di associazione dinamica è consentire l'interazione con gli oggetti dinamici, c# consente l'associazione dinamica in tutti gli oggetti, se sono dinamici o meno. In questo modo per un'integrazione più uniforme di oggetti dinamici, come i risultati delle operazioni su di essi non stesse possono essere oggetti dinamici, ma sono comunque di un tipo sconosciuto per il programmatore in fase di compilazione. Anche associazione dinamica consente di eliminare il codice basato su reflection soggetta a errori anche quando nessuno degli oggetti interessati è oggetti dinamici.

Le sezioni seguenti per ciascun costrutto del linguaggio esattamente quando l'associazione dinamica viene applicato, cosa compilare il controllo in fase: se uno qualsiasi, viene applicata e quali la fase di compilazione risultato ed espressione classificazione è.

### <a name="types-of-constituent-expressions"></a>Tipi di espressioni che costituiscono

Quando un'operazione in modo statico associata, il tipo di un'espressione che lo costituiscono (ad esempio, un ricevitore, un argomento, un indice o un operando) viene considerato sempre il tipo di fase di compilazione dell'espressione.

Quando un'operazione in modo dinamico è associata, il tipo di un'espressione che lo costituiscono è determinato in modi diversi a seconda del tipo in fase di compilazione dell'espressione che lo costituiscono:

*  Un'espressione che costituiscono dei tipi in fase di compilazione `dynamic` ha il tipo del valore effettivo a cui l'espressione viene valutata in fase di esecuzione
*  Un'espressione che costituiscono il cui tipo di fase di compilazione è un parametro di tipo viene considerata il tipo di cui è associato il parametro di tipo in fase di esecuzione
*  In caso contrario, viene considerato l'espressione che costituiscono il relativo tipo in fase di compilazione.

## <a name="operators"></a>Operatori

Le espressioni sono costituite da ***operandi*** e ***operatori***. Gli operatori di un'espressione indicano le operazioni che devono essere eseguite sugli operandi. Alcuni esempi di operatori sono `+`, `-`, `*`, `/` e `new`, mentre i valori effettivi, i campi, le variabili locali e le espressioni sono esempi di operandi.

Esistono tre tipi di operatori:

*  Operatori unari. Gli operatori unari accettano un unico operando e usare la notazione prefissa (ad esempio `--x`) o la notazione suffissa (ad esempio `x++`).
*  Operatori binari. Gli operatori binari accettano due operandi e utilizzano la notazione infissa (ad esempio `x + y`).
*  operatore ternario. Solo un operatore ternario `?:`, esiste; accetta tre operandi e utilizza la notazione infissa (`c ? x : y`).

L'ordine di valutazione degli operatori in un'espressione è determinato dal ***precedenza*** e ***associatività*** degli operatori ([precedenza e associatività degli operatori](expressions.md#operator-precedence-and-associativity)) .

Gli operandi in un'espressione vengono valutati da sinistra a destra. Ad esempio, nella `F(i) + G(i++) * H(i)`, metodo `F` viene chiamato con il valore precedente della `i`, quindi il metodo `G` viene chiamato con il valore precedente della `i`e, infine, metodo `H` viene chiamato con il nuovo valore di `i`. Questo è separato dal e non correlati alla precedenza degli operatori.

Alcuni operatori possono essere ***sottoposti a overload***. L'overload degli operatori consente di specificare implementazioni operatore definito dall'utente di specificare per le operazioni in cui uno o entrambi gli operandi sono di un tipo di classe o struct definito dall'utente ([overload di operatori](expressions.md#operator-overloading)).

### <a name="operator-precedence-and-associativity"></a>Precedenza e associatività degli operatori

Se un'espressione contiene più operatori, la ***precedenza*** degli operatori determina l'ordine in cui vengono valutati i singoli operatori. Ad esempio, l'espressione `x + y * z` darà `x + (y * z)` perché la `*` operatore ha maggiore precedenza il file binario `+` operatore. La priorità di un operatore viene stabilita dalla definizione di grammatica associata. Ad esempio, un *additive_expression* è costituito da una sequenza di *multiplicative_expression*s separati da `+` oppure `-` operatori, offrendo così la `+` e `-` precedenza sugli operatori del `*`, `/`, e `%` operatori.

La tabella seguente riepiloga tutti gli operatori in ordine di precedenza dalla più alta alla più bassa:

| __Sezione__                                                                                   | __Categoria__                | __Operatori__ | 
|-----------------------------------------------------------------------------------------------|-----------------------------|---------------|
| [Espressioni primarie](expressions.md#primary-expressions)                                     | Primario                     | `x.y`  `f(x)`  `a[x]`  `x++`  `x--`  `new`  `typeof`  `default`  `checked`  `unchecked`  `delegate` | 
| [Operatori unari](expressions.md#unary-operators)                                             | Unario                       | `+`  `-`  `!`  `~`  `++x`  `--x`  `(T)x` | 
| [Operatori aritmetici](expressions.md#arithmetic-operators)                                   | Moltiplicazione              | `*`  `/`  `%` | 
| [Operatori aritmetici](expressions.md#arithmetic-operators)                                   | Addizione                    | `+`  `-`      | 
| [Operatori shift](expressions.md#shift-operators)                                             | Shift                       | `<<`  `>>`    | 
| [Operatori relazionali e di test del tipo](expressions.md#relational-and-type-testing-operators) | Operatori relazionali e operatori di test del tipo | `<`  `>`  `<=`  `>=`  `is`  `as` | 
| [Operatori relazionali e di test del tipo](expressions.md#relational-and-type-testing-operators) | Uguaglianza                    | `==`  `!=`    | 
| [Operatori logici](expressions.md#logical-operators)                                         | AND logico                 | `&`           | 
| [Operatori logici](expressions.md#logical-operators)                                         | XOR logico                 | `^`           | 
| [Operatori logici](expressions.md#logical-operators)                                         | OR logico                  | <code>&#124;</code>           |
| [Operatori logici condizionali](expressions.md#conditional-logical-operators)                 | AND condizionale             | `&&`          | 
| [Operatori logici condizionali](expressions.md#conditional-logical-operators)                 | OR condizionale              | <code>&#124;&#124;</code>          | 
| [Operatore null-coalescing](expressions.md#the-null-coalescing-operator)                   | Null-coalescing             | `??`          | 
| [Operatore condizionale](expressions.md#conditional-operator)                                   | Condizionale                 | `?:`          | 
| [Gli operatori di assegnazione](expressions.md#assignment-operators), [espressioni di funzioni anonime](expressions.md#anonymous-function-expressions)  | Assegnazione ed espressione lambda | `=`  `*=`  `/=`  `%=`  `+=`  `-=`  `<<=`  `>>=`  `&=`  `^=`  <code>&#124;=</code>  `=>` | 

Se un operando si trova tra i due operatori con la stessa precedenza, l'associatività degli operatori determina l'ordine in cui vengono eseguite le operazioni:

*  Fatta eccezione per gli operatori di assegnazione e l'operatore null-coalescing, sono tutti gli operatori binari ***associa all'operando sinistro***, vale a dire che le operazioni vengano eseguite da sinistra a destra. L'espressione `x + y + z` viene ad esempio valutata come `(x + y) + z`.
*  Gli operatori di assegnazione, l'operatore null-coalescing e l'operatore condizionale (`?:`) vengono ***associa all'operando destro***, vale a dire che operazioni vengono eseguite da destra a sinistra. L'espressione `x = y = z` viene ad esempio valutata come `x = (y = z)`.

È possibile controllare la precedenza e l'associatività usando le parentesi. Ad esempio, `x + y * z` prima moltiplica `y` per `z` e quindi somma il risultato a `x`, ma `(x + y) * z` prima somma `x` e `y` e quindi moltiplica il risultato per `z`.

### <a name="operator-overloading"></a>Overload degli operatori

Tutti gli operatori unari e binari sono predefiniti implementazioni che sono automaticamente disponibili in qualsiasi espressione. Oltre alle implementazioni predefinite, le implementazioni definite dall'utente possono essere introdotta includendo `operator` dichiarazioni di classi e struct ([operatori](classes.md#operators)). Implementazioni di operatori definiti dall'utente hanno sempre la precedenza sulle implementazioni di operatori predefiniti: Solo quando non applicabile operatore definito dall'utente esistano essere considerati le implementazioni di operatori predefiniti, come descritto in [risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution) e [overload degli operatori binari risoluzione](expressions.md#binary-operator-overload-resolution).

Il ***operatori unari che supportano l'overload*** sono:
```csharp
+   -   !   ~   ++   --   true   false
```

Sebbene `true` e `false` non vengono usati in modo esplicito nelle espressioni (e pertanto non sono incluse nella tabella delle precedenze in [precedenza e associatività degli operatori](expressions.md#operator-precedence-and-associativity)), sono considerati operatori perché sono richiamate in diversi contesti di espressione: espressioni booleane ([espressioni booleane](expressions.md#boolean-expressions)) ed espressioni che implicano condizionale ([operatore condizionale](expressions.md#conditional-operator)) e logica condizionale operatori ([degli operatori logici condizionali](expressions.md#conditional-logical-operators)).

Il ***che supportano l'overload degli operatori binari*** sono:
```csharp
+   -   *   /   %   &   |   ^   <<   >>   ==   !=   >   <   >=   <=
```

Solo gli operatori elencati in precedenza possono essere sottoposti a overload. In particolare, non è possibile eseguire l'overload di accesso ai membri, chiamata a un metodo o la `=`, `&&`, `||`, `??`, `?:`, `=>`, `checked`, `unchecked`, `new`, `typeof`, `default`, `as`, e `is` operatori.

Quando viene eseguito l'overload di un operatore binario, viene anche eseguito in modo implicito l'overload dell'operatore di assegnazione corrispondente, se presente. Ad esempio, l'overload dell'operatore `*` è anche un overload dell'operatore `*=`. Questo è descritto più dettagliatamente in [assegnazione composta](expressions.md#compound-assignment). Si noti che l'operatore di assegnazione (`=`) non possono essere sottoposti a overload. Un'assegnazione sempre esegue una semplice copia bit per bit di un valore in una variabile.

Eseguire il cast di operazioni, quali `(T)x`, sono sottoposti a overload, fornendo le conversioni definite dall'utente ([conversioni definite dall'utente](conversions.md#user-defined-conversions)).

Elemento accedere, ad esempio `a[x]`, non viene considerato un operatore che supporti l'overload. Definito dall'utente l'indicizzazione è invece supportato tramite gli indicizzatori ([indicizzatori](classes.md#indexers)).

Nelle espressioni, operatori vengono fatto riferimento usando la notazione di operatore e nelle dichiarazioni, gli operatori vengono fatto riferimento utilizzando una notazione funzionale. Nella tabella seguente viene illustrata la relazione tra le notazioni funzionale per operatori unari e binari e operatore. Nella prima voce, ovvero *op* denota qualsiasi operatore di prefisso unario che supporti l'overload. Nella seconda voce *op* identifica gli operatori unari `++` e `--` operatori. Nella terza voce *op* denota qualsiasi operatore binario che supporti l'overload.


| __Notazione dell'operatore__ | __Notazione funzionale__ |
|-----------------------|-------------------------|
| `op x`                | `operator op(x)`        | 
| `x op`                | `operator op(x)`        | 
| `x op y`              | `operator op(x,y)`      | 

Le dichiarazioni di operatore definito dall'utente è sempre necessario almeno uno dei parametri di tipo classe o struct che contiene la dichiarazione dell'operatore. Non è pertanto possibile che un operatore definito dall'utente avere la stessa firma già definito.

Le dichiarazioni di operatore definito dall'utente non è possibile modificare la sintassi, la precedenza o associazione di un operatore. Ad esempio, il `/` operatore è sempre un operatore binario, sempre con il livello di priorità specificato nel [precedenza e associatività degli operatori](expressions.md#operator-precedence-and-associativity)ed è sempre l'associazione all'operando a sinistra.

Sebbene sia possibile per un operatore definito dall'utente eseguire qualsiasi calcolo che ragionevolmente, le implementazioni che producono risultati diversi da quelli previsti in modo intuitivo sono fortemente sconsigliate. Ad esempio, un'implementazione di `operator ==` deve confrontare i due operandi per verificare l'uguaglianza e restituire un oggetto appropriato `bool` risultato.

Le descrizioni dei singoli operatori [espressioni primarie](expressions.md#primary-expressions) attraverso [operatori logici condizionali](expressions.md#conditional-logical-operators) specificare le implementazioni predefinite degli operatori e tutte le regole aggiuntive che si applicano a ogni operatore. Vengono inoltre usare i termini ***risoluzione dell'overload di operatore unario***, ***risoluzione dell'overload di operatore binario***, e ***innalzamento di livello***, definizioni dei quali sono vedere le sezioni seguenti.

### <a name="unary-operator-overload-resolution"></a>Risoluzione dell'overload di operatore unario

Un'operazione del form `op x` oppure `x op`, dove `op` è un operatore unario che supporti l'overload, e `x` è un'espressione di tipo `X`, viene elaborato come segue:

*  Il set di operatori definiti dall'utente candidato fornita da `X` per l'operazione `operator op(x)` viene determinato mediante le regole delle [operatori definiti dall'utente candidato](expressions.md#candidate-user-defined-operators).
*  Se il set di operatori definiti dall'utente candidato non è vuoto, questo diventa quindi il set di operatori di candidati per l'operazione. In caso contrario, l'unario predefinita `operator op` implementazioni, incluse le forme lift, diventano il set di operatori di candidati per l'operazione. Le implementazioni predefinite di un determinato operatore vengono specificate nella descrizione dell'operatore ([espressioni primarie](expressions.md#primary-expressions) e [operatori unari](expressions.md#unary-operators)).
*  Le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution) vengono applicate al set di operatori di candidati per selezionare l'operatore migliori rispetto all'elenco di argomenti `(x)`, e questo operatore diventa il risultato dell'overload processo di risoluzione. Se la risoluzione dell'overload non riesce selezionare un singolo operatore ottimo, si verifica un errore in fase di associazione.

### <a name="binary-operator-overload-resolution"></a>Risoluzione dell'overload di operatore binario

Un'operazione del form `x op y`, dove `op` è un operatore binario che supporti l'overload `x` è un'espressione di tipo `X`, e `y` è un'espressione di tipo `Y`, viene elaborato come segue:

*  Il set di operatori definiti dall'utente candidato fornita da `X` e `Y` per l'operazione `operator op(x,y)` è determinata. Il set è costituito dall'unione degli operatori candidato fornita da `X` e gli operatori candidato forniti da `Y`, ogni determinata usando le regole delle [operatori definiti dall'utente candidato](expressions.md#candidate-user-defined-operators). Se `X` e `Y` sono dello stesso tipo, o se `X` e `Y` derivano da un tipo di base comune, quindi gli operatori condiviso candidato presenti solo nel set combinato di una volta.
*  Se il set di operatori definiti dall'utente candidato non è vuoto, questo diventa quindi il set di operatori di candidati per l'operazione. In caso contrario, il file binario predefinito `operator op` implementazioni, incluse le forme lift, diventano il set di operatori di candidati per l'operazione. Le implementazioni predefinite di un determinato operatore vengono specificate nella descrizione dell'operatore ([operatori aritmetici](expressions.md#arithmetic-operators) attraverso [operatori logici condizionali](expressions.md#conditional-logical-operators)). Per gli operatori enum e il delegato predefiniti, gli unici operatori considerati sono quelle definite da un tipo di enumerazione o un delegato che rappresenta il tipo in fase di associazione di uno degli operandi.
*  Le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution) vengono applicate al set di operatori di candidati per selezionare l'operatore migliori rispetto all'elenco di argomenti `(x,y)`, e questo operatore diventa il risultato dell'overload processo di risoluzione. Se la risoluzione dell'overload non riesce selezionare un singolo operatore ottimo, si verifica un errore in fase di associazione.

### <a name="candidate-user-defined-operators"></a>Operatori definiti dall'utente candidato

Dato un tipo `T` e un'operazione `operator op(A)`, dove `op` è un operatore che supporti l'overload e `A` è un elenco di argomenti, il set di candidati operatori definiti dall'utente forniti da `T` per `operator op(A)` viene determinato come indicato di seguito:

*  Determinare il tipo `T0`. Se `T` è un tipo nullable `T0` è il tipo sottostante, in caso contrario `T0` è uguale a `T`.
*  Per tutti i `operator op` nelle dichiarazioni `T0` e tutti i moduli di questi operatori, è elevato se almeno un operatore è applicabile ([membro di funzione applicabile](expressions.md#applicable-function-member)) rispetto all'elenco di argomenti `A`, quindi il set di gli operatori candidato è costituito da tutti gli operatori applicabili in `T0`.
*  In caso contrario, se `T0` è `object`, il set di operatori candidato è vuoto.
*  In caso contrario, il set di operatori candidato disql `T0` è il set di operatori candidato forniti dalla classe di base diretta di `T0`, o la classe base effettiva del `T0` se `T0` è un parametro di tipo.

### <a name="numeric-promotions"></a>Promozioni numeriche

Innalzamento di livello consiste nell'eseguire automaticamente alcune conversioni implicite gli operandi degli operatori numerici binari e unario predefinita. Innalzamento di livello non è un meccanismo separato, ma piuttosto un effetto dell'applicazione di risoluzione dell'overload per gli operatori predefiniti. Innalzamento di livello in modo specifico non influiscono sulla valutazione degli operatori definiti dall'utente, anche se gli operatori definiti dall'utente possono essere implementati per ottenere risultati simili.

Ad esempio di innalzamento di livello, prendere in considerazione le implementazioni predefinite del file binario `*` operatore:

```csharp
int operator *(int x, int y);
uint operator *(uint x, uint y);
long operator *(long x, long y);
ulong operator *(ulong x, ulong y);
float operator *(float x, float y);
double operator *(double x, double y);
decimal operator *(decimal x, decimal y);
```

Eseguire l'overload quando le regole di risoluzione ([risoluzione dell'Overload](expressions.md#overload-resolution)) vengono applicati a questo set di operatori, l'effetto è selezionato il primo operatore per cui sono presenti conversioni implicite dai tipi di operando. Ad esempio, per l'operazione `b * s`, dove `b` è un `byte` e `s` è un `short`, dell'overload seleziona la risoluzione `operator *(int,int)` come operatore migliore. Di conseguenza, il risultato è che `b` e `s` vengono convertiti `int`, e il tipo del risultato è `int`. Analogamente, per l'operazione `i * d`, dove `i` è un `int` e `d` è un `double`, dell'overload seleziona la risoluzione dei `operator *(double,double)` come operatore migliore.

#### <a name="unary-numeric-promotions"></a>Promozioni numeriche unario

Unario numeric vengano promossi per gli operandi degli operatori `+`, `-`, e `~` operatori unari. Innalzamento di livello unario costituita semplicemente gli operandi di tipo di conversione `sbyte`, `byte`, `short`, `ushort`, o `char` al tipo `int`. Inoltre, per l'operatore unario `-` operatore unario innalzamento di livello converte gli operandi di tipo `uint` al tipo `long`.

#### <a name="binary-numeric-promotions"></a>Promozioni numeriche binarie

Innalzamento di livello binario viene generato per gli operandi degli operatori `+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `==`, `!=`, `>`, `<`, `>=`, e `<=` operatori binari. Innalzamento di livello binario in modo implicito converte entrambi gli operandi in un tipo comune che, in caso degli operatori non relazionale, diventa anche il tipo di risultato dell'operazione. Innalzamento di livello binaria costituita applicando le regole seguenti, nell'ordine che vengano visualizzati qui:

*  Se degli operandi è di tipo `decimal`, l'altro operando verrà convertito nel tipo `decimal`, o si verifica un errore in fase di associazione se l'altro operando è di tipo `float` o `double`.
*  In caso contrario, se degli operandi è di tipo `double`, l'altro operando verrà convertito nel tipo `double`.
*  In caso contrario, se degli operandi è di tipo `float`, l'altro operando verrà convertito nel tipo `float`.
*  In caso contrario, se degli operandi è di tipo `ulong`, l'altro operando verrà convertito nel tipo `ulong`, o si verifica un errore in fase di associazione se l'altro operando è di tipo `sbyte`, `short`, `int`, o `long`.
*  In caso contrario, se degli operandi è di tipo `long`, l'altro operando verrà convertito nel tipo `long`.
*  In caso contrario, se degli operandi è di tipo `uint` e l'altro operando è di tipo `sbyte`, `short`, o `int`, entrambi gli operandi verranno convertiti nel tipo `long`.
*  In caso contrario, se degli operandi è di tipo `uint`, l'altro operando verrà convertito nel tipo `uint`.
*  In caso contrario, entrambi gli operandi verranno convertiti nel tipo `int`.

Si noti che la prima regola impedisce l'esecuzione di qualsiasi operazione che combinano il `decimal` tipo con il `double` e `float` tipi. La regola seguente dal fatto che non sono presenti conversioni implicite tra i `decimal` tipo e il `double` e `float` tipi.

Si noti anche che non è possibile che un operando di tipo `ulong` quando l'altro operando è di tipo integrale con segno. Il motivo è che esiste alcun tipo integrale che può rappresentare la gamma completa di `ulong` , nonché i tipi integrali con segno.

In entrambi i casi precedenti, un'espressione cast è utilizzabile da convertire in modo esplicito uno degli operandi a un tipo compatibile con l'altro operando.

Nell'esempio
```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (1.0 + percent / 100.0);
}
```
si verifica un errore in fase di associazione perché una `decimal` non può essere moltiplicato con un `double`. L'errore viene risolto mediante la conversione in modo esplicito il secondo operando a `decimal`, come indicato di seguito:

```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (decimal)(1.0 + percent / 100.0);
}
```

### <a name="lifted-operators"></a>Operatori elevati

***Gli operatori è elevato*** consentono di predefinite e definite dall'utente operatori che operano sui tipi di valore non nullable per essere usato anche con le forme di tali tipi nullable. Gli operatori lift sono costruiti da operatori predefiniti e definiti dall'utente che soddisfano determinati requisiti, come descritto di seguito:

*   Per gli operatori unari

    ```csharp
    +  ++  -  --  !  ~
    ```

    esiste una forma lift di un operatore se i tipi di operando e il risultato sono entrambi tipi di valore non nullable. La forma elevata si ottiene mediante l'aggiunta di un singolo `?` modificatore ai tipi di operando e il risultato. L'operatore elevata produce un valore null se l'operando è null. In caso contrario, l'operatore lift Annulla il wrapping dell'operando, si applica l'operatore sottostante e include il risultato.

*   Per gli operatori binari

    ```csharp
    +  -  *  /  %  &  |  ^  <<  >>
    ```

    esiste una forma lift di un operatore se i tipi di operando e il risultato sono tutti i tipi di valore non nullable. La forma elevata si ottiene mediante l'aggiunta di un singolo `?` modificatore a ogni tipo di operando e il risultato. L'operatore elevata produce un valore null se uno o entrambi gli operandi sono null (un'eccezione in fase di `&` e `|` operatori del `bool?` digitare, come descritto in [booleani operatori logici](expressions.md#boolean-logical-operators)). In caso contrario, l'operatore lift Annulla il wrapping degli operandi, si applica l'operatore sottostante e include il risultato.

*   Per gli operatori di uguaglianza

    ```csharp
    ==  !=
    ```

    esiste una forma lift di un operatore se i tipi di operando sono entrambi tipi di valore non nullable e se il tipo di risultato è `bool`. La forma elevata si ottiene mediante l'aggiunta di un singolo `?` a ciascun tipo di operando. L'operatore lift considera uguali due valori null e un valore null non uguali a qualsiasi valore diverso da null. Se entrambi gli operandi sono diversi da null, l'operatore lift Annulla il wrapping degli operandi e applica l'operatore sottostante per produrre il `bool` risultato.

*   Per gli operatori relazionali

    ```csharp
    <  >  <=  >=
    ```

    esiste una forma lift di un operatore se i tipi di operando sono entrambi tipi di valore non nullable e se il tipo di risultato è `bool`. La forma elevata si ottiene mediante l'aggiunta di un singolo `?` a ciascun tipo di operando. L'operatore elevata produce il valore `false` se uno o entrambi gli operandi sono null. In caso contrario, l'operatore lift Annulla il wrapping degli operandi e applica l'operatore sottostante per produrre il `bool` risultato.

## <a name="member-lookup"></a>Ricerca di membri

Una ricerca di membri è il processo in base al quale viene determinato il significato di un nome nel contesto di un tipo. Una ricerca di membri può verificarsi come parte della valutazione di una *simple_name* ([nomi semplici](expressions.md#simple-names)) o un *member_access* ([l'accesso ai membri](expressions.md#member-access)) in un espressione. Se il *simple_name* o *member_access* avviene quando il *primary_expression* di un *invocation_expression* ([ Chiamate al metodo](expressions.md#method-invocations)), viene definito il membro da richiamare.

Se il membro è un metodo o evento, o se è una costante, un campo o una proprietà di un tipo di delegato ([delegati](delegates.md)) o il tipo `dynamic` ([tipo dinamico](types.md#the-dynamic-type)), quindi il membro è detto *invocable*.

Ricerca di membri prende in considerazione non solo il nome del membro, ma anche il numero di parametri di tipo che contiene il membro e indica se il membro è accessibile. Ai fini della ricerca dei membri, i metodi generici e tipi generici annidati hanno il numero di parametri di tipo indicati nelle rispettive dichiarazioni e tutti gli altri membri con zero parametri di tipo.

Una ricerca di membri di un nome `N` con `K`  parametri di tipo in un tipo `T` viene elaborato come segue:

*  Per prima cosa, un set di membri accessibili denominato `N` viene determinato:
    * Se `T` è un parametro di tipo, il set è l'unione dei set di membri accessibili denominati `N` in ciascuno dei tipi specificati come un vincolo primario o secondario vincolo ([digitare i vincoli del parametro](classes.md#type-parameter-constraints)) per  `T`, insieme ai set di membri accessibili denominato `N` in `object`.
    * In caso contrario, il set è costituito tutto accessibile ([l'accesso ai membri](basic-concepts.md#member-access)) membri denominati `N` nelle `T`, inclusi i membri ereditati e i membri accessibili denominati `N` in `object`. Se `T` è un tipo costruito, il set di membri viene ottenuto sostituendo gli argomenti di tipo come descritto in [i membri di tipi costruiti](classes.md#members-of-constructed-types). I membri che includono un `override` modificatore vengono esclusi dal set.
*  Successivamente, se `K` è uguale a zero, tutto annidati vengono rimossi i tipi cui dichiarazioni di includono i parametri di tipo. Se `K` è diverso da zero, tutti i membri con un numero diverso di parametri di tipo vengono rimossi. Si noti che quando `K` sono zero, i metodi con tipo di parametri non vengono rimossi, poiché il processo di inferenza del tipo ([inferenza del tipo](expressions.md#type-inference)) potrebbe essere in grado di dedurre gli argomenti di tipo.
*  Quindi, se il membro è *richiamato*, tutti gli elementi non-*invocable* i membri vengono rimossi dal set.
*  Successivamente, i membri che sono nascosti da altri membri vengono rimossi dal set. Per ogni membro `S.M` nel set, in cui `S` è il tipo in cui il membro `M` viene dichiarata, vengono applicate le regole seguenti:
    * Se `M` è una costante, campo, proprietà, eventi o membro di enumerazione, quindi tutti i membri dichiarati nel tipo di base `S` vengono rimosse dal set.
    * Se `M` è una dichiarazione del tipo, quindi tutti non tipi dichiarati nel tipo di base `S` vengono rimosse dal set e tutte le dichiarazioni con lo stesso numero di parametri di tipo come di tipo `M` dichiarato nel tipo di base `S` vengono rimossi dal set.
    * Se `M` è un metodo, quindi tutti i membri non metodo dichiarato in un tipo di base di `S` vengono rimosse dal set.
*  Successivamente, i membri di interfaccia che sono nascosti per i membri della classe vengono rimosse dal set. Questo passaggio ha effetto solo se `T` è un parametro di tipo e `T` è diverso da entrambi una classe base effettiva `object` e un insieme di interfacce efficace non vuoto ([digitare i vincoli del parametro](classes.md#type-parameter-constraints)). Per ogni membro `S.M` nel set, in cui `S` è il tipo in cui il membro `M` viene dichiarato, le regole seguenti vengono applicate se `S` è diverso da una dichiarazione di classe `object`:
    * Se `M` è una costante, campo, proprietà, eventi, membro di enumerazione o dichiarazione del tipo, quindi tutti i membri dichiarati in una dichiarazione di interfaccia vengono rimosse dal set.
    * Se `M` è un metodo, quindi tutti i membri non-method dichiarati in una dichiarazione di interfaccia vengono rimossi dal set e tutti i metodi con la stessa firma `M` dichiarati in un'interfaccia di dichiarazione vengono rimossi dal set.
*  Infine, avere rimosso i membri nascosti, viene determinato il risultato della ricerca:
    * Se il set è costituito da un solo membro che non è un metodo, questo membro è il risultato della ricerca.
    * In caso contrario, se il set contiene solo metodi, questo gruppo di metodi è il risultato della ricerca.
    * In caso contrario, la ricerca è ambigua e si verifica un errore in fase di associazione.

Per le ricerche di membri di tipi diversi dai parametri di tipo e le interfacce e le ricerche di membri nelle interfacce che sono strettamente a ereditarietà singola (ogni interfaccia nella catena di ereditarietà include esattamente una o nessuna interfaccia di base diretta), l'effetto delle regole di ricerca è è sufficiente che derivato membri di base Nascondi membri con lo stesso nome o firma. Tali ricerche a ereditarietà singola mai sono ambigue. L'ambiguità che può derivare dalle ricerche di membri nelle interfacce di ereditarietà multipla è descritte nel [l'accesso ai membri di interfaccia](interfaces.md#interface-member-access).

### <a name="base-types"></a>Tipi di base

Per scopi di ricerca di membri, un tipo `T` ha i tipi di base seguenti:

*  Se `T` viene `object`, quindi `T` non dispone di alcun tipo di base.
*  Se `T` è un *enum_type*, i tipi di base `T` sono i tipi di classe `System.Enum`, `System.ValueType`, e `object`.
*  Se `T` è un *struct_type*, i tipi di base `T` sono i tipi di classe `System.ValueType` e `object`.
*  Se `T` è un *class_type*, i tipi di base del `T` sono le classi di base `T`, incluso il tipo di classe `object`.
*  Se `T` è un *interface_type*, i tipi di base `T` sono le interfacce di base di `T` e il tipo di classe `object`.
*  Se `T` è un *array_type*, i tipi di base `T` sono i tipi di classe `System.Array` e `object`.
*  Se `T` è un *delegate_type*, i tipi di base `T` sono i tipi di classe `System.Delegate` e `object`.

## <a name="function-members"></a>Membri di funzione

I membri di funzione sono i membri che contengono istruzioni eseguibili. I membri di funzione sono sempre i membri dei tipi e non possono essere membri degli spazi dei nomi. C# definisce le categorie di membri di funzione seguenti:

*  Metodi
*  Proprietà
*  Eventi
*  Indicizzatori
*  Operatori definiti dall'utente
*  Costruttori di istanza
*  Costruttori statici
*  Distruttori

Ad eccezione di costruttori e distruttori statici (che non è possibile richiamare in modo esplicito), le istruzioni contenute nei membri di funzione vengono eseguite tramite le chiamate di funzione membro. La sintassi per la scrittura di una chiamata alla funzione membro dipende dalla categoria del membro di funzione specifica.

L'elenco di argomenti ([elenchi di argomenti](expressions.md#argument-lists)) di un membro di funzione chiamata fornisce i valori effettivi o riferimenti a variabili per i parametri del membro funzione.

Le chiamate dei metodi generici possono impiegare l'inferenza del tipo per determinare il set di argomenti di tipo da passare al metodo. Questo processo è descritto nella [inferenza del tipo](expressions.md#type-inference).

Nelle chiamate di metodi, indicizzatori, operatori e costruttori di istanza viene utilizzata la risoluzione dell'overload per determinare quali di un set di candidati di membri di funzione da richiamare. Questo processo è descritto nella [risoluzione dell'Overload](expressions.md#overload-resolution).

Dopo aver identificato un membro di funzione specifica in fase di associazione, possibilmente tramite la risoluzione dell'overload, il processo in fase di esecuzione effettivo di richiamare il membro di funzione è descritto nella [controllo della risoluzione dell'overload dinamiciinfasedicompilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

La tabella seguente riepiloga l'elaborazione che si verificano nei costrutti che includono le categorie di sei membri di funzione che possono essere richiamati in modo esplicito. Nella tabella `e`, `x`, `y`, e `value` indicano classificate come variabili o valori, le espressioni `T` indica un'espressione classificata come un tipo, `F` è il nome semplice di un metodo e `P` è il nome semplice di una proprietà.


| __Costrutto__     | __Esempio__    | __Descrizione__ |
|-------------------|----------------|-----------------|
| Chiamata del metodo | `F(x,y)`       | Risoluzione dell'overload viene applicata per selezionare il metodo migliore `F` nella classe o struct che lo contiene. Il metodo viene richiamato con l'elenco di argomenti `(x,y)`. Se il metodo non è `static`, l'espressione dell'istanza è `this`. | 
|                   | `T.F(x,y)`     | Risoluzione dell'overload viene applicata per selezionare il metodo migliore `F` nella classe o struct `T`. Se il metodo non è generato un errore in fase di associazione `static`. Il metodo viene richiamato con l'elenco di argomenti `(x,y)`. | 
|                   | `e.F(x,y)`     | Risoluzione dell'overload viene applicata per selezionare il metodo migliore F nella classe, struct o interfaccia fornita dal tipo di `e`. Se il metodo viene generato un errore in fase di associazione `static`. Il metodo viene richiamato con l'espressione dell'istanza `e` l'elenco di argomenti `(x,y)`. | 
| Accesso a proprietà   | `P`            | Il `get` funzione di accesso della proprietà `P` nella classe o struct che lo contiene viene richiamato. Si verifica un errore in fase di compilazione se `P` è di sola scrittura. Se `P` non è `static`, l'espressione dell'istanza è `this`. | 
|                   | `P = value`    | Il `set` funzione di accesso della proprietà `P` nella classe o struct che lo contiene viene richiamato con l'elenco di argomenti `(value)`. Si verifica un errore in fase di compilazione se `P` è di sola lettura. Se `P` non è `static`, l'espressione dell'istanza è `this`. | 
|                   | `T.P`          | Il `get` funzione di accesso della proprietà `P` nella classe o struct `T` viene richiamato. Si verifica un errore in fase di compilazione se `P` non è `static` oppure se `P` è di sola scrittura. | 
|                   | `T.P = value`  | Il `set` funzione di accesso della proprietà `P` nella classe o struct `T` viene richiamato con l'elenco di argomenti `(value)`. Si verifica un errore in fase di compilazione se `P` non è `static` oppure se `P` è di sola lettura. | 
|                   | `e.P`          | Il `get` funzione di accesso della proprietà `P` nella classe, struct o interfaccia fornita dal tipo di `e` viene richiamato con l'espressione dell'istanza `e`. Si verifica un errore in fase di associazione se `P` viene `static` oppure se `P` è di sola scrittura. | 
|                   | `e.P = value`  | Il `set` funzione di accesso della proprietà `P` nella classe, struct o interfaccia fornita dal tipo di `e` viene richiamato con l'espressione dell'istanza `e` l'elenco di argomenti `(value)`. Si verifica un errore in fase di associazione se `P` viene `static` oppure se `P` è di sola lettura. | 
| Accesso agli eventi      | `E += value`   | Il `add` funzione di accesso dell'evento `E` nella classe o struct che lo contiene viene richiamato. Se `E` è l'espressione di istanza non statico, è `this`. | 
|                   | `E -= value`   | Il `remove` funzione di accesso dell'evento `E` nella classe o struct che lo contiene viene richiamato. Se `E` è l'espressione di istanza non statico, è `this`. | 
|                   | `T.E += value` | Il `add` funzione di accesso dell'evento `E` nella classe o struct `T` viene richiamato. Si verifica un errore in fase di associazione se `E` non è statico. | 
|                   | `T.E -= value` | Il `remove` funzione di accesso dell'evento `E` nella classe o struct `T` viene richiamato. Si verifica un errore in fase di associazione se `E` non è statico. | 
|                   | `e.E += value` | Il `add` funzione di accesso dell'evento `E` nella classe, struct o interfaccia fornita dal tipo di `e` viene richiamato con l'espressione dell'istanza `e`. Si verifica un errore in fase di associazione se `E` è statico. | 
|                   | `e.E -= value` | Il `remove` funzione di accesso dell'evento `E` nella classe, struct o interfaccia fornita dal tipo di `e` viene richiamato con l'espressione dell'istanza `e`. Si verifica un errore in fase di associazione se `E` è statico. | 
| Accesso all'indicizzatore    | `e[x,y]`       | Risoluzione dell'overload viene applicata per selezionare l'indicizzatore migliore nella classe, struct o interfaccia fornita dal tipo di e. Il `get` funzione di accesso dell'indicizzatore viene richiamato con l'espressione dell'istanza `e` l'elenco di argomenti `(x,y)`. Se l'indicizzatore è in sola lettura, si verifica un errore in fase di associazione. | 
|                   | `e[x,y] = value` | Risoluzione dell'overload viene applicata per selezionare l'indicizzatore migliore di classe, struct o interfaccia fornita dal tipo di `e`. Il `set` funzione di accesso dell'indicizzatore viene richiamato con l'espressione dell'istanza `e` l'elenco di argomenti `(x,y,value)`. Se l'indicizzatore è di sola lettura, si verifica un errore in fase di associazione. | 
| Chiamata dell'operatore | `-x`         | Risoluzione dell'overload viene applicata per selezionare l'operatore unario ottimo nella classe o struttura fornita dal tipo di `x`. L'operatore selezionato viene richiamato con l'elenco di argomenti `(x)`. | 
|                     | `x + y`      | Risoluzione dell'overload viene applicata per selezionare l'operatore binario meglio nelle classi o struct di base per i tipi di `x` e `y`. L'operatore selezionato viene richiamato con l'elenco di argomenti `(x,y)`. | 
| Chiamata al costruttore di istanza | `new T(x,y)` | Risoluzione dell'overload viene applicata per selezionare il costruttore di istanza migliore nella classe o struct `T`. Viene chiamato il costruttore di istanza con l'elenco di argomenti `(x,y)`. | 

### <a name="argument-lists"></a>Elenchi di argomenti

Ogni chiamata alla funzione membro e il delegato include un elenco di argomenti che fornisce i valori effettivi o riferimenti a variabili per i parametri del membro funzione. La sintassi per specificare l'elenco di argomenti di una chiamata alla funzione membro dipende dalla funzione membro categoria:

*  Ad esempio costruttori, metodi, indicizzatori e delegati, gli argomenti sono specificati come un *argument_list*, come descritto di seguito. Per gli indicizzatori, quando si richiama il `set` della funzione di accesso, l'elenco di argomenti include inoltre l'espressione specificata come operando destro dell'operatore di assegnazione.
*  Per le proprietà, l'elenco di argomenti è vuoto quando si richiama il `get` funzione di accesso ed è costituito l'espressione specificata come operando destro dell'operatore di assegnazione quando si richiama il `set` della funzione di accesso.
*  Per gli eventi, l'elenco di argomenti è costituita dall'espressione specificata come operando destro del `+=` o `-=` operatore.
*  Per gli operatori definiti dall'utente, l'elenco di argomenti è costituito da due operandi dell'operatore binario o il singolo operando dell'operatore unario.

Gli argomenti delle proprietà ([proprietà](classes.md#properties)), gli eventi ([eventi](classes.md#events)) e operatori definiti dall'utente ([operatori](classes.md#operators)) vengono sempre passati come parametri di valore ([ Parametri del valore](classes.md#value-parameters)). Gli argomenti degli indicizzatori ([indicizzatori](classes.md#indexers)) vengono sempre passati come parametri di valore ([parametri del valore](classes.md#value-parameters)) o le matrici di parametri ([matrici di parametri](classes.md#parameter-arrays)). Parametri di riferimento e di output non sono supportati per queste categorie di membri di funzione.

Gli argomenti di una chiamata al costruttore, metodo, indicizzatore o delegato di istanza vengono specificati come un *argument_list*:

```antlr
argument_list
    : argument (',' argument)*
    ;

argument
    : argument_name? argument_value
    ;

argument_name
    : identifier ':'
    ;

argument_value
    : expression
    | 'ref' variable_reference
    | 'out' variable_reference
    ;
```

Un' *argument_list* costituito da uno o più *argomento*s, separati da virgole. Ogni argomento è costituito da facoltativo *nome_argomento* seguita da un *argument_value*. Un' *argomento* con un *nome_argomento* viene definito un ***argomento denominato***, mentre un *argomento* senza un  *nome_argomento* è un ***argomento posizionale***. È corretto per un argomento posizionale visualizzate dopo un argomento denominato in un *argument_list*.

Il *argument_value* può assumere uno dei formati seguenti:

*  Un' *espressione*, che indica che l'argomento viene passato come parametro di valore ([parametri del valore](classes.md#value-parameters)).
*  La parola chiave `ref` seguita da un *variable_reference* ([riferimenti a variabili](variables.md#variable-references)), che indica che l'argomento viene passato come parametro di riferimento ([fare riferimento ai parametri ](classes.md#reference-parameters)). Una variabile deve essere assegnata definitivamente ([assegnazione certa](variables.md#definite-assignment)) prima che può essere passata come parametro di riferimento. La parola chiave `out` seguita da un *variable_reference* ([riferimenti a variabili](variables.md#variable-references)), che indica che l'argomento viene passato come parametro di output ([iparametridiOutput](classes.md#output-parameters)). Viene considerata come una variabile assegnata ([assegnazione certa](variables.md#definite-assignment)) dopo una chiamata alla funzione membro in cui la variabile viene passata come parametro di output.

#### <a name="corresponding-parameters"></a>Parametri corrispondenti

Per ogni argomento nell'elenco di argomenti deve essere un parametro corrispondente nel membro di funzione o delegato richiamato.

Elenco di parametri usato di seguito viene determinata come segue:

*  Per i metodi virtuali e gli indicizzatori definiti nelle classi, l'elenco dei parametri viene prelevato dalla dichiarazione di più specifica o eseguire l'override del membro (funzione), iniziando con il tipo statico di ricevitore e la ricerca tramite le relative classi base.
*  Per i metodi di interfaccia e gli indicizzatori, viene selezionato l'elenco di parametri costituiscono la definizione del membro, iniziando con il tipo di interfaccia e la ricerca tramite le interfacce di base più specifica. Se non viene trovato alcun elenco di parametri univoci, viene costruito un elenco di parametri con nomi inaccessibili e nessun parametro facoltativo, in modo che le chiamate non è possibile usare i parametri denominati o omettere gli argomenti facoltativi.
*  Per i metodi parziali, viene usato l'elenco di parametri della dichiarazione di metodo parziale di definizione.
*  Per tutti gli altri membri di funzione e delegati è disponibile solo un elenco, l'unico parametro che corrisponde a quella usata.

La posizione di un argomento o un parametro viene definita come numero di argomenti o parametri precede nell'elenco di argomento o elenco di parametri.

I parametri corrispondenti per gli argomenti della funzione membro vengono stabiliti nel modo seguente:

*  Gli argomenti in di *argument_list* di costruttori di istanza, metodi, indicizzatori e delegati:
    * Un argomento posizionale in cui è presente un parametro predefinito nella stessa posizione nell'elenco di parametri corrisponde al parametro.
    * Un argomento posizionale di un membro di funzione con una matrice di parametri richiamato nella sua forma normale corrisponde alla matrice del parametro, che deve essere utilizzati nella stessa posizione nell'elenco dei parametri.
    * Un argomento posizionale di un membro di funzione con una matrice di parametri, richiamato in forma espansa, in cui è presente alcun parametro fisso nella stessa posizione nell'elenco dei parametri, corrisponde a un elemento della matrice di parametri.
    * Un argomento denominato corrispondente al parametro lo stesso nome nell'elenco dei parametri.
    * Per gli indicizzatori, quando si richiama il `set` funzione di accesso, l'espressione specificata come operando destro dell'operatore di assegnazione corrisponde a implicita `value` parametro del `set` dichiarazione di funzione di accesso.
*  Per le proprietà, quando si richiama il `get` funzione di accesso sono presenti argomenti. Quando si richiama il `set` funzione di accesso, l'espressione specificata come operando destro dell'operatore di assegnazione corrisponde a implicita `value` parametro del `set` dichiarazione di funzione di accesso.
*  Per gli operatori unari definito dall'utente (comprese le conversioni), l'unico operando corrisponde al singolo parametro della dichiarazione dell'operatore.
*  Per gli operatori binari definiti dall'utente, l'operando sinistro corrisponde al primo parametro e operando destro corrisponde al secondo parametro della dichiarazione dell'operatore.

#### <a name="run-time-evaluation-of-argument-lists"></a>Valutazione in fase di esecuzione degli elenchi di argomenti

Durante l'elaborazione in fase di esecuzione di una chiamata alla funzione membro ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), le espressioni o i riferimenti a variabili di un elenco di argomenti vengono valutati in ordine, da sinistra a destra, come di seguito:

*  Per un parametro di valore, viene valutata l'espressione dell'argomento e una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) al parametro corrispondente tipo viene eseguita. Il valore risultante diventa il valore iniziale del parametro del valore nella chiamata del membro funzione.
*  Per un parametro di riferimento o di output, il riferimento alla variabile viene valutato e il percorso di archiviazione risultante diventa il percorso di archiviazione rappresentato dal parametro nella chiamata del membro funzione. Se il riferimento alla variabile fornito come parametro di riferimento o di output è un elemento di un *reference_type*, viene eseguito un controllo in fase di esecuzione per garantire che il tipo di elemento della matrice è identico al tipo del parametro. Se questo controllo ha esito negativo, un `System.ArrayTypeMismatchException` viene generata un'eccezione.

I metodi, indicizzatori e costruttori di istanza possono dichiarare il parametro all'estrema destra sia una matrice di parametri ([matrici di parametri](classes.md#parameter-arrays)). Questi membri di funzione vengono richiamati in forma normale o in forma espansa in base alla scelta applicabile ([membro di funzione applicabile](expressions.md#applicable-function-member)):

*  Quando viene richiamato un membro di funzione con una matrice di parametri nella sua forma normale, l'argomento specificato per la matrice di parametri deve essere una singola espressione che è convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) per il tipo di matrice di parametri. In questo caso, la matrice di parametri funziona esattamente come un parametro di valore.
*  Quando viene richiamato un membro di funzione con una matrice di parametri in forma espansa, la chiamata è necessario specificare zero o più argomenti posizionali per la matrice di parametri, dove ogni argomento è un'espressione che è convertibile in modo implicito ([implicite le conversioni](conversions.md#implicit-conversions)) al tipo di elemento della matrice di parametri. In questo caso, la chiamata crea un'istanza del tipo di parametro matrice con una lunghezza corrispondente al numero di argomenti, inizializza gli elementi dell'istanza di matrice con i valori dell'argomento specificato e Usa l'istanza di matrice appena creata come l'effettivo argomento.

Le espressioni dell'elenco di argomenti vengono sempre valutate nell'ordine in cui sono scritti. Di conseguenza, l'esempio
```csharp
class Test
{
    static void F(int x, int y = -1, int z = -2) {
        System.Console.WriteLine("x = {0}, y = {1}, z = {2}", x, y, z);
    }

    static void Main() {
        int i = 0;
        F(i++, i++, i++);
        F(z: i++, x: i++);
    }
}
```
produce l'output
```
x = 0, y = 1, z = 2
x = 4, y = -1, z = 3
```

Le regole di CO-varianza matrice ([vovariante](arrays.md#array-covariance)) consentono di un valore di tipo matrice `A[]` come un riferimento a un'istanza di un tipo di matrice `B[]`, a condizione che esiste una conversione implicita del riferimento da `B` per `A`. A causa di queste regole, quando un elemento di un *reference_type* viene passato come parametro un riferimento o di output, è necessario per garantire che il tipo effettivo degli elementi della matrice è identico a quella del parametro di un controllo in fase di esecuzione. Nell'esempio
```csharp
class Test
{
    static void F(ref object x) {...}

    static void Main() {
        object[] a = new object[10];
        object[] b = new string[10];
        F(ref a[0]);        // Ok
        F(ref b[1]);        // ArrayTypeMismatchException
    }
}
```
la seconda chiamata di `F` fa sì che un `System.ArrayTypeMismatchException` eccezione perché il tipo dell'elemento effettivo dei `b` viene `string` e non `object`.

Quando viene richiamato un membro di funzione con una matrice di parametri in forma espansa, la chiamata viene elaborata esattamente come se un'espressione di creazione della matrice con un inizializzatore di matrice ([espressioni di creazione matrice](expressions.md#array-creation-expressions)) è stato inserito tutto il parametri espansi. Ad esempio, considerando la dichiarazione
```csharp
void F(int x, int y, params object[] args);
```
le chiamate seguenti nel formato espanso del metodo
```csharp
F(10, 20);
F(10, 20, 30, 40);
F(10, 20, 1, "hello", 3.0);
```
corrispondono esattamente a
```csharp
F(10, 20, new object[] {});
F(10, 20, new object[] {30, 40});
F(10, 20, new object[] {1, "hello", 3.0});
```

In particolare, si noti che viene creata una matrice vuota quando non sono specificati argomenti per la matrice di parametri.

Quando gli argomenti vengono omessi da un membro di funzione con parametri facoltativi corrispondenti, gli argomenti predefiniti della dichiarazione di membro di funzione vengono passati in modo implicito. Poiché questi sono sempre una costanti, la loro valutazione non avrà impatto sull'ordine di valutazione degli argomenti rimanenti.

### <a name="type-inference"></a>Inferenza del tipo

Quando viene chiamato un metodo generico senza specificare gli argomenti di tipo, una ***inferenza del tipo*** processo prova a dedurre argomenti tipo per la chiamata. La presenza di inferenza consente una sintassi più pratica da utilizzare per chiamare un metodo generico e consente al programmatore di evitare di specificare le informazioni sul tipo ridondanti. Ad esempio, considerando la dichiarazione di metodo:
```csharp
class Chooser
{
    static Random rand = new Random();

    public static T Choose<T>(T first, T second) {
        return (rand.Next(2) == 0)? first: second;
    }
}
```
è possibile richiamare il `Choose` metodo senza specificare in modo esplicito un argomento di tipo:
```csharp
int i = Chooser.Choose(5, 213);                 // Calls Choose<int>

string s = Chooser.Choose("foo", "bar");        // Calls Choose<string>
```

Tramite l'inferenza del tipo, gli argomenti di tipo `int` e `string` vengono determinati in base agli argomenti al metodo.

L'inferenza del tipo viene generato come parte dell'elaborazione in fase di associazione di una chiamata al metodo ([chiamate al metodo](expressions.md#method-invocations)) e viene eseguita prima la procedura di risoluzione dell'overload della chiamata. Quando un gruppo di metodi specifico viene specificato in una chiamata al metodo e nessun argomento di tipo viene specificato come parte della chiamata del metodo, l'inferenza del tipo viene applicato a ogni metodo generico nel gruppo di metodi. Se l'inferenza del tipo ha esito positivo, vengono utilizzati gli argomenti tipo dedotti per determinare i tipi di argomenti per la risoluzione dell'overload successivi. Se la risoluzione dell'overload viene scelto un metodo generico a quella per il richiamo, gli argomenti tipo dedotti sono usati come argomenti di tipo effettivo per la chiamata. Se l'inferenza del tipo per un particolare metodo ha esito negativo, tale metodo non fa parte della risoluzione dell'overload. L'errore di inferenza del tipo e in se stesso, non causa un errore in fase di associazione. Tuttavia, tale operazione spesso comporta un errore in fase di associazione durante la risoluzione dell'overload non sia stato possibile trovare alcun metodo applicabile.

Se il numero di argomenti fornito è diverso rispetto al numero di parametri del metodo, quindi inferenza ha immediatamente esito negativo. In caso contrario, si supponga che il metodo generico ha la firma seguente:
```csharp
Tr M<X1,...,Xn>(T1 x1, ..., Tm xm)
```

Con una chiamata al metodo del form `M(E1...Em)` l'attività di inferenza del tipo consiste nell'individuare gli argomenti di tipo univoco `S1...Sn` per ognuno dei parametri di tipo `X1...Xn` in modo che la chiamata `M<S1...Sn>(E1...Em)` diventa valido.

Durante il processo di inferenza di ogni parametro di tipo `Xi` può essere *fissa* a un determinato tipo `Si` oppure *unfixed* con un set associato di *limiti*. Ognuno dei limiti è un tipo `T`. Inizialmente ogni variabile di tipo `Xi` è unfixed con un set vuoto di limiti.

L'inferenza del tipo viene eseguita in fasi. Ogni fase proverà a dedurre argomenti tipo per basarsi sui risultati della fase precedente più variabili di tipo. La prima fase prevede alcune inferenze iniziali dei limiti, mentre la seconda fase corregge le variabili di tipo a tipi specifici e deduce ulteriormente i limiti. La seconda fase potrebbe dover essere ripetuto un numero di volte.

*Nota:* Tipo inferenza ha luogo non solo quando viene chiamato un metodo generico. L'inferenza del tipo per la conversione dei gruppi di metodi è descritto nella [inferenza dei tipi per la conversione dei gruppi di metodi](expressions.md#type-inference-for-conversion-of-method-groups) e trovare il tipo comune di un set di espressioni è descritta in [trovare il tipo migliore di un set comune espressioni](expressions.md#finding-the-best-common-type-of-a-set-of-expressions).

#### <a name="the-first-phase"></a>La prima fase

Per ognuno degli argomenti del metodo `Ei`:

*   Se `Ei` è una funzione anonima, un' *l'inferenza del tipo di parametro espliciti* ([inferenza dei tipi parametro esplicito](expressions.md#explicit-parameter-type-inferences)) viene eseguita dal `Ei` a `Ti`
*   In caso contrario, se `Ei` dispone di un tipo `U` e `xi` è un parametro un' *inferenza di livello inferiore* viene effettuata *da* `U` *a* `Ti`.
*   In caso contrario, se `Ei` dispone di un tipo `U` e `xi` è un `ref` o `out` parametro un' *esatte inferenza* viene effettuata *da* `U` *al* `Ti`.
*   In caso contrario, viene eseguita alcuna inferenza per questo argomento.


#### <a name="the-second-phase"></a>La seconda fase

La seconda fase procede nel modo seguente:

*   Tutti i *unfixed* variabili di tipo `Xi` che non lo fanno *dipendono* ([dipendenza](expressions.md#dependence)) qualsiasi `Xj` saranno stati corretti ([correzione](expressions.md#fixing)).
*   Se non esiste alcuna variabile di tipo, tutte le *unfixed* variabili di tipo `Xi` sono *risolto* per cui tutti gli elementi seguenti contengono:
    *   Non è presente almeno una variabile di tipo `Xj` che dipende da `Xi`
    *   `Xi` dispone un set non vuoto dei limiti
*   Se non esistono alcuna variabile di tipo e sono ancora presenti *unfixed* digitare variabili, inferenza ha esito negativo.
*   In caso contrario, se non ulteriormente *unfixed* variabili di tipo, l'inferenza del tipo ha esito positivo.
*   In caso contrario, per tutti gli argomenti `Ei` con tipo di parametro corrispondente `Ti` in cui le *tipi di output* ([tipi di Output](expressions.md#output-types)) contengono *unfixed* variabili di tipo `Xj` ma la *tipi di input* ([tipi di Input](expressions.md#input-types)), non sono un *l'inferenza del tipo di output* ([Output inferenza dei tipi ](expressions.md#output-type-inferences)) viene effettuata *dalla* `Ei` *alla* `Ti`. Quindi la seconda fase viene ripetuta.

#### <a name="input-types"></a>Tipi di input

Se `E` è un gruppo di metodi o tipizzate in modo implicito funzione anonima e `T` è un delegato, tipo o il tipo di albero delle espressioni quindi tutti i tipi dei parametri `T` sono *tipi di input* di `E` *con il tipo* `T`.

####  <a name="output-types"></a>Tipi di output

Se `E` è un gruppo di metodi o una funzione anonima e `T` è un delegato, tipo o il tipo di albero delle espressioni quindi il tipo restituito del `T` è un *tipo dell'output* `E` *con tipo*  `T`.

#### <a name="dependence"></a>Dipendenza da

Un' *unfixed* variabile di tipo `Xi` *dipende direttamente* una variabile di tipo unfixed `Xj` se per alcuni argomenti `Ek` con tipo `Tk` `Xj` si verifica un *tipo di input* dei `Ek` con tipo `Tk` e `Xi` si verifica un *tipo di output* di `Ek` con tipo `Tk`.

`Xj` *dipende* `Xi` se `Xj` *dipende direttamente* `Xi` oppure se `Xi` *dipende direttamente* `Xk` e `Xk` *dipende* `Xj`. Di conseguenza "dipende" è la chiusura transitiva ma non riflessiva "dipende direttamente".

#### <a name="output-type-inferences"></a>Inferenza dei tipi output

Un' *inferenza del tipo di output* viene effettuata *dalla* un'espressione `E` *a* un tipo `T` nel modo seguente:

*  Se `E` è una funzione anonima con tipo restituito dedotto `U` ([derivato il tipo restituito](expressions.md#inferred-return-type)) e `T` è un tipo delegato o un tipo di albero delle espressioni con tipo restituito `Tb`, quindi un *inferenza di livello inferiore* ([limite inferiore inferenze](expressions.md#lower-bound-inferences)) viene effettuata *da* `U` *a* `Tb`.
*  In caso contrario, se `E` è un gruppo di metodi e `T` è un tipo delegato o un tipo di albero delle espressioni con tipi di parametro `T1...Tk` e il tipo restituito `Tb`e risoluzione dell'overload delle `E` con i tipi `T1...Tk` produce un metodo con tipo restituito unico `U`, un *inferenza di livello inferiore* viene effettuata *da* `U` *per* `Tb`.
*  In caso contrario, se `E` è un'espressione con tipo `U`, quindi un *inferenza di livello inferiore* viene effettuata *da* `U` *a* `T`.
*  In caso contrario, viene eseguita alcuna inferenza.

#### <a name="explicit-parameter-type-inferences"></a>Inferenza dei tipi parametro espliciti

Un' *inferenza del tipo di parametro espliciti* viene effettuata *dalla* un'espressione `E` *a* un tipo `T` nel modo seguente:

*  Se `E` è una funzione anonima tipizzata in modo esplicito con i tipi di parametro `U1...Uk` e `T` è un tipo delegato o un tipo di albero delle espressioni con tipi di parametro `V1...Vk` quindi per ogni `Ui` un *esatta inferenza* ([esatto inferenze](expressions.md#exact-inferences)) viene effettuata *dalla* `Ui` *a* corrispondente `Vi`.

#### <a name="exact-inferences"></a>Inferenze esatte

Un *esatte inferenza* *da* un tipo `U` *per* un tipo `V` viene presa come segue:

*  Se `V` è uno dei *unfixed* `Xi` quindi `U` viene aggiunto al gruppo di limiti esatti per `Xi`.

*  In caso contrario, imposta `V1...Vk` e `U1...Uk` sono determinati dalla presenza di uno qualsiasi dei seguenti casi:

   *  `V` è un tipo di matrice `V1[...]` e `U` è un tipo matrice `U1[...]` dello stesso rango
   *  `V` è di tipo `V1?` e `U` è il tipo `U1?`
   *  `V` è un tipo costruito `C<V1...Vk>`e `U` è un tipo costruito `C<U1...Uk>`

   Se uno di questi casi si applica quindi un' *esatte inferenza* viene effettuata *da* ogni `Ui` *a* corrispondente `Vi`.

*  In caso contrario, viene eseguita alcuna inferenza.

#### <a name="lower-bound-inferences"></a>Limite inferiore inferenze

Oggetto *inferenza di livello inferiore* *dalla* un tipo `U` *a* un tipo `V` viene presa come segue:

*  Se `V` è uno dei *unfixed* `Xi` quindi `U` viene aggiunto al gruppo di limiti inferiori per `Xi`.
*  In caso contrario, se `V` è di tipo `V1?`e `U` è il tipo `U1?` inferenza un limite inferiore è costituito dalla `U1` a `V1`.
*  In caso contrario, imposta `U1...Uk` e `V1...Vk` sono determinati dalla presenza di uno qualsiasi dei seguenti casi:
   *  `V` è un tipo di matrice `V1[...]` e `U` è un tipo matrice `U1[...]` (o un parametro di tipo il cui tipo di base effettivo è `U1[...]`) dello stesso rango
   *  `V` è uno dei `IEnumerable<V1>`, `ICollection<V1>` oppure `IList<V1>` e `U` è un tipo di matrice unidimensionale `U1[]`(o un parametro di tipo il cui tipo di base effettivo è `U1[]`)
   *  `V` è un tipo di classe, struct, interfaccia o delegato costruito `C<V1...Vk>` e vi è un tipo univoco `C<U1...Uk>` in modo che `U` (oppure, se `U` è un parametro di tipo, la relativa classe base effettiva o qualsiasi membro di un set di interfacce effettive) è identica a, eredita (direttamente o indirettamente), o implementa (direttamente o indirettamente) `C<U1...Uk>`.

      (La restrizione di "univocità" significa che nell'interfaccia del case `C<T> {} class U: C<X>, C<Y> {}`, quindi viene eseguita alcuna inferenza quando derivandolo da `U` al `C<T>` perché `U1` potrebbe essere `X` o `Y`.)

   Se uno di questi casi si applica quindi viene eseguita un'inferenza *dal* ciascun `Ui` *al* corrispondente `Vi` come indicato di seguito:

   *  Se `Ui` non è noto per essere un tipo riferimento un' *esatte inferenza* viene effettuata
   *  In caso contrario, se `U` è un tipo matrice un' *limite inferiore inferenza* viene effettuata
   *  In caso contrario, se `V` viene `C<V1...Vk>` , l'inferenza dipende il parametro di tipo i-esimo `C`:
      *  Se è covariante un' *inferenza di livello inferiore* viene eseguita.
      *  Se è controvariante un' *inferenza del limite superiore* viene eseguita.
      *  Se è invariante un' *esatte inferenza* viene eseguita.
*  In caso contrario, viene eseguita alcuna inferenza.

#### <a name="upper-bound-inferences"></a>Limite superiore inferenze

Un' *inferenza del limite superiore* *dalla* un tipo `U` *a* un tipo `V` viene presa come segue:

*  Se `V` è uno dei *unfixed* `Xi` quindi `U` viene aggiunto al gruppo di limiti superiori per `Xi`.
*  In caso contrario, imposta `V1...Vk` e `U1...Uk` sono determinati dalla presenza di uno qualsiasi dei seguenti casi:
   *  `U` è un tipo di matrice `U1[...]` e `V` è un tipo matrice `V1[...]` dello stesso rango
   *  `U` è uno dei `IEnumerable<Ue>`, `ICollection<Ue>` oppure `IList<Ue>` e `V` è un tipo di matrice unidimensionale `Ve[]`
   *  `U` è di tipo `U1?` e `V` è il tipo `V1?`
   *  `U` viene costruito classe, struct, interfaccia o un tipo delegato `C<U1...Uk>` e `V` è una classe, struct, interfaccia o un tipo delegato che è identico a, eredita da (direttamente o indirettamente) o implementa (direttamente o indirettamente) un tipo univoco `C<V1...Vk>`

      (La restrizione di "univocità" significa che, se abbiamo `interface C<T>{} class V<Z>: C<X<Z>>, C<Y<Z>>{}`, viene eseguita alcuna inferenza quando derivandolo da `C<U1>` a `V<Q>`. Inferenze non vengano apportate dal `U1` a uno `X<Q>` o `Y<Q>`.)

   Se uno di questi casi si applica quindi viene eseguita un'inferenza *dal* ciascun `Ui` *al* corrispondente `Vi` come indicato di seguito:
   *  Se `Ui` non è noto per essere un tipo riferimento un' *esatte inferenza* viene effettuata
   *  In caso contrario, se `V` è un tipo matrice un' *inferenza del limite superiore* viene effettuata
   *  In caso contrario, se `U` viene `C<U1...Uk>` , l'inferenza dipende il parametro di tipo i-esimo `C`:
      *  Se è covariante un' *inferenza del limite superiore* viene eseguita.
      *  Se è controvariante un' *inferenza di livello inferiore* viene eseguita.
      *  Se è invariante un' *esatte inferenza* viene eseguita.
*  In caso contrario, viene eseguita alcuna inferenza.   

#### <a name="fixing"></a>La correzione

Un' *unfixed* variabile di tipo `Xi` con un set di limiti viene *risolto* come indicato di seguito:

*  Il set di *tipi di candidato* `Uj` inizia come il set di tutti i tipi nel set di limiti per `Xi`.
*  Viene quindi esaminato ogni limite per `Xi` a sua volta: Per ogni associazione esatto `U` dei `Xi` tutti i tipi `Uj` che non sono identici a `U` vengono rimosse dal set di candidati. Per ogni limite inferiore `U` dei `Xi` tutti i tipi `Uj` al quale sono presenti sia *non* una conversione implicita da `U` vengono rimosse dal set di candidati. Per ogni limite superiore `U` dei `Xi` tutti i tipi `Uj` al quale è costituita *non* una conversione implicita a `U` vengono rimosse dal set di candidati.
*  Se tra i tipi di candidato rimanenti `Uj` è presente un tipo univoco `V` da cui è presente una conversione implicita a tutti gli altri candidati tipi, quindi `Xi` fissa `V`.
*  In caso contrario, l'inferenza del tipo ha esito negativo.

#### <a name="inferred-return-type"></a>Tipo restituito dedotto

Il derivato il tipo restituito di una funzione anonima `F` viene usato durante la risoluzione dell'overload e l'inferenza del tipo. Il tipo restituito dedotto può essere determinato solo per una funzione anonima in cui parametro tutti i tipi sono noti in quanto viene assegnato in modo esplicito, fornite tramite una conversione funzione anonima o dedotto durante l'inferenza del tipo in un contenitore generico chiamata al metodo.

Il ***dedotto il tipo di risultato*** viene determinata come segue:

*  Se il corpo della `F` è un *espressione* che ha un tipo, il tipo di risultato derivato `F` è il tipo dell'espressione.
*  Se il corpo della `F` è un *block* e il set di espressioni del blocco `return` istruzioni ha un tipo comune `T` ([ricerca del tipo common migliore di un set di espressioni](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)), quindi il tipo di risultato derivato `F` è `T`.
*  In caso contrario, non è possibile dedurre un tipo di risultato per `F`.

Il ***dedotto il tipo restituito*** viene determinata come segue:

*  Se `F` è asincrono e il corpo della `F` è un'espressione classificata come nothing ([classificazioni delle espressioni](expressions.md#expression-classifications)), o un blocco di istruzioni in cui l'istruzione return non dispongono di espressioni, dedotto il tipo restituito è `System.Threading.Tasks.Task`
*  Se `F` è asincrona e ha un tipo di risultato derivato `T`, derivato il tipo restituito è `System.Threading.Tasks.Task<T>`.
*  Se `F` è non asincrone e ha un tipo di risultato derivato `T`, derivato il tipo restituito è `T`.
*  In caso contrario, non è possibile dedurre un tipo restituito per `F`.

Ad esempio di inferenza del tipo che interessa le funzioni anonime, prendere in considerazione la `Select` metodo di estensione dichiarato nel `System.Linq.Enumerable` classe:
```csharp
namespace System.Linq
{
    public static class Enumerable
    {
        public static IEnumerable<TResult> Select<TSource,TResult>(
            this IEnumerable<TSource> source,
            Func<TSource,TResult> selector)
        {
            foreach (TSource element in source) yield return selector(element);
        }
    }
}
```

Supponendo che il `System.Linq` dello spazio dei nomi è stato importato con un `using` clausola e una classe `Customer` con un `Name` vlastnosti typu `string`, il `Select` metodo può essere utilizzato per selezionare il nome di un elenco di clienti:
```csharp
List<Customer> customers = GetCustomerList();
IEnumerable<string> names = customers.Select(c => c.Name);
```

La chiamata al metodo di estensione ([chiamate al metodo di estensione](expressions.md#extension-method-invocations)) di `Select` viene elaborato riscrivendo la chiamata a una chiamata al metodo statico:
```csharp
IEnumerable<string> names = Enumerable.Select(customers, c => c.Name);
```

Poiché gli argomenti di tipo non sono stati specificati in modo esplicito, l'inferenza del tipo viene utilizzato per dedurre gli argomenti tipo. Prima di tutto, il `customers` argomento è correlato al `source` parametro, l'inferenza `T` essere `Customer`. Usando la funzione anonima digitare quindi il processo di inferenza descritto in precedenza, `c` ha tipo `Customer`e l'espressione `c.Name` è correlato al tipo restituito del `selector` parametro, l'inferenza `S` sia `string`. Di conseguenza, la chiamata è equivalente a
```csharp
Sequence.Select<Customer,string>(customers, (Customer c) => c.Name)
```
e il risultato è di tipo `IEnumerable<string>`.

L'esempio seguente illustra come anonimo tipo di funzione l'inferenza consente di informazioni sul tipo "flusso" tra gli argomenti nella chiamata a un metodo generico. Si consideri il metodo seguente:
```csharp
static Z F<X,Y,Z>(X value, Func<X,Y> f1, Func<Y,Z> f2) {
    return f2(f1(value));
}
```

Inferenza del tipo per la chiamata:
```csharp
double seconds = F("1:15:30", s => TimeSpan.Parse(s), t => t.TotalSeconds);
```
le operazioni seguenti: Per prima cosa, l'argomento `"1:15:30"` è correlato il `value` parametro, l'inferenza `X` essere `string`. Quindi, il parametro della funzione anonima prima, `s`, viene assegnato il tipo dedotto `string`e l'espressione `TimeSpan.Parse(s)` è correlato al tipo restituito del `f1`, l'inferenza `Y` essere `System.TimeSpan`. Infine, il parametro della seconda funzione anonima, `t`, viene assegnato il tipo dedotto `System.TimeSpan`e l'espressione `t.TotalSeconds` è correlato al tipo restituito del `f2`, l'inferenza `Z` essere `double`. Di conseguenza, il risultato della chiamata è di tipo `double`.

#### <a name="type-inference-for-conversion-of-method-groups"></a>Inferenza del tipo per la conversione dei gruppi di metodi

Simile alle chiamate di metodi generici, inferenza del tipo deve essere applicata anche quando un gruppo di metodi `M` che contiene un metodo generico viene convertito in un tipo delegato specificato `D` ([conversioni dei metodi di gruppo](conversions.md#method-group-conversions)). Dato un metodo
```csharp
Tr M<X1...Xn>(T1 x1 ... Tm xm)
```
e il gruppo di metodi `M` assegnato al tipo di delegato `D` è l'attività di inferenza del tipo per trovare gli argomenti di tipo `S1...Sn` in modo che l'espressione:
```csharp
M<S1...Sn>
```
diventa compatibile ([dichiarazioni Delegate](delegates.md#delegate-declarations)) con `D`.

A differenza di algoritmo di inferenza del tipo per le chiamate di metodo generico, in questo caso sono presenti solo argomento *tipi*, nessun argomento *espressioni*. In particolare, non sono presenti funzioni anonime e pertanto non è necessario per più fasi di inferenza.

Al contrario, tutti i `Xi` sono considerati *unfixed*e un *inferenza di livello inferiore* viene effettuata *da* ogni tipo di argomento `Uj` di `D` *al* tipo di parametro corrispondente `Tj` di `M`. Se per uno qualsiasi del `Xi` sono stati trovati senza limiti, l'inferenza del tipo ha esito negativo. In caso contrario, tutti i `Xi` vengono *fissa* corrispondente `Si`, che sono il risultato di inferenza del tipo.

#### <a name="finding-the-best-common-type-of-a-set-of-expressions"></a>Ricerca del tipo common migliore di un set di espressioni

In alcuni casi, un tipo comune deve essere derivata per un set di espressioni. In particolare i tipi di elementi delle matrici tipizzate in modo implicito e i tipi restituiti di funzioni anonime con *blocco* corpi di sono disponibili in questo modo.

Intuitivamente, dato un set di espressioni `E1...Em` questo inferenza deve essere equivalente alla chiamata di un metodo
```csharp
Tr M<X>(X x1 ... X xm)
```
con la `Ei` come argomenti.

Più precisamente, l'inferenza inizia con un *unfixed* variabile di tipo `X`. *Inferenza dei tipi di output* vengono quindi resi *dalla* ogni `Ei` *alla* `X`. Infine `X` viene *fissa* e, se ha esito positivo, l'oggetto risultante digitare `S` è il tipo comune migliore risulta per le espressioni. Se non `S` esiste, le espressioni non dispone di alcun tipo comune.

### <a name="overload-resolution"></a>Risoluzione dell'overload

Risoluzione dell'overload è un meccanismo in fase di associazione per la selezione il miglior membro di funzione di richiamata, dato un elenco di argomenti e un set di membri di funzione candidati. Risoluzione dell'overload seleziona il membro di funzione da richiamare nei contesti seguenti distinti all'interno di c#:

*  Chiamata di un metodo denominato un *invocation_expression* ([chiamate al metodo](expressions.md#method-invocations)).
*  Chiamata di un costruttore di istanza denominata un' *object_creation_expression* ([espressioni per la creazione dell'oggetto](expressions.md#object-creation-expressions)).
*  Chiamata di una funzione di accesso dell'indicizzatore tramite un *element_access* ([l'accesso all'elemento](expressions.md#element-access)).
*  Chiamata di un operatore predefinito o definito dall'utente a cui fa riferimento un'espressione ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution) e [risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)).

Ognuno di questi contesti definisce il set di membri di funzione candidati e l'elenco di argomenti in modo univoco, come descritto in dettaglio nelle sezioni elencate in precedenza. Ad esempio, il set di candidati per una chiamata al metodo non include i metodi contrassegnati `override` ([ricerca di membri](expressions.md#member-lookup)), e i metodi in una classe base non sono candidati se qualsiasi metodo in una classe derivata è applicabile ([ Chiamate al metodo](expressions.md#method-invocations)).

Dopo che sono stati identificati i membri di funzione candidati e l'elenco di argomenti, la selezione del miglior membro di funzione è lo stesso in tutti i casi:

*  Dato il set di membri di funzione candidati applicabili, il membro funzione migliore in quanto viene individuato. Se il set contiene un solo membro di funzione, tale membro di funzione è il miglior membro di funzione. In caso contrario, il miglior membro di funzione è il membro di una funzione che sia migliore rispetto a tutti gli altri membri di funzione rispetto all'elenco di argomenti specificato, purché ogni membro di funzione viene confrontato con tutti gli altri membri di funzione usando le regole in [ Miglior membro di funzione](expressions.md#better-function-member). Se si verifica non è uguale a un membro di funzione migliore di tutti gli altri membri di funzione, quindi la chiamata alla funzione membro è ambigua e si verifica un errore in fase di associazione.

Le sezioni seguenti definiscono l'esatto significato dei termini ***membro di funzione applicabile*** e ***miglior membro di funzione***.

#### <a name="applicable-function-member"></a>Membro di funzione applicabile

Un membro di funzione viene definito un' ***membro di funzione applicabile*** rispetto a un elenco di argomenti `A` quando tutte le operazioni seguenti sono vere:

*  Ogni argomento nel `A` corrisponde a un parametro nella dichiarazione di membro di funzione, come descritto in [i parametri corrispondenti](expressions.md#corresponding-parameters), e qualsiasi parametro a cui non corrisponde alcun argomento è un parametro facoltativo.
*  Per ogni argomento nel `A`, il modalità dell'argomento di passaggio dei parametri (ad esempio, valore, `ref`, o `out`) è identica alla modalità di passaggio del parametro corrispondente, e
   *  per un parametro di valore o una matrice di parametri, una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) esiste dall'argomento e il tipo del parametro corrispondente, o
   *  per un `ref` o `out` parametro, il tipo dell'argomento è identico al tipo del parametro corrispondente. Dopo tutto, un `ref` o `out` parametro è un alias per l'argomento passato.

Per un membro di funzione che include una matrice di parametri, se il membro di funzione è applicabile per le regole precedenti, viene definita sia applicabile nel relativo ***forma normale***. Se un membro di funzione che include una matrice di parametri non è applicabile in forma normale, il membro di funzione invece potrebbe essere applicabile nel relativo ***espanso form***:

*  La forma espansa viene costruita, sostituendo la matrice di parametri nella dichiarazione di membro di funzione con zero o più parametri di valore del tipo di elemento del parametro di matrice ad esempio che il numero di argomenti nell'elenco di argomenti `A` corrisponde al totale numero di parametri. Se `A` dispone di meno argomenti di maggiore del numero di parametri fissi nella dichiarazione di membro di funzione, la forma espansa del membro di funzione non può essere costruita e pertanto non è applicabile.
*  In caso contrario, la forma espansa è applicabile se per ogni argomento nel `A` è identica alla modalità di passaggio del parametro corrispondente, la modalità di passaggio dell'argomento e
   *  per un parametro di valore fisso o un parametro di valore creati dall'espansione del, una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) esistente dal tipo dell'argomento nel tipo del parametro corrispondente, o
   *  per un `ref` o `out` parametro, il tipo dell'argomento è identico al tipo del parametro corrispondente.

#### <a name="better-function-member"></a>Miglior membro di funzione

Ai fini di determinare il miglior membro di funzione, viene costruito un elenco di argomenti ridotta contenente solo le espressioni degli argomenti in ordine che vengono visualizzati nell'elenco di argomenti originale.

Elenchi di parametri per ogni funzione candidato membri vengono costruiti nel modo seguente:

*  La forma espansa viene utilizzata se il membro di funzione è applicabile solo in forma espansa.
*  I parametri facoltativi senza argomenti corrispondenti vengono rimossi dall'elenco di parametri
*  I parametri vengono riordinati in modo che vengano eseguiti nella stessa posizione come argomento corrispondente nell'elenco di argomenti.

Dato un elenco di argomenti `A` con un set di espressioni dell'argomento `{E1, E2, ..., En}` e due membri di funzione applicabile `Mp` e `Mq` con i tipi di parametro `{P1, P2, ..., Pn}` e `{Q1, Q2, ..., Qn}`, `Mp` viene definito come un ***miglior membro di funzione*** rispetto a `Mq` se

*  per ogni argomento, la conversione implicita da `Ex` al `Qx` non è migliore della conversione implicita dal `Ex` a `Px`, e
*  per almeno un argomento, la conversione da `Ex` al `Px` è migliore della conversione da `Ex` a `Qx`.

Quando si esegue questa versione di valutazione, se `Mp` oppure `Mq` è applicabile in forma espansa, quindi `Px` o `Qx` fa riferimento a un parametro in forma espansa all'elenco di parametri.

Nel caso in cui il parametro di tipo sequenze `{P1, P2, ..., Pn}` e `{Q1, Q2, ..., Qn}` sono equivalenti (ovvero ognuno `Pi` dispone di una conversione di identità ai corrispondenti `Qi`), vengono applicate le seguenti regole di risoluzione di conflitti, in ordine, per determinare la migliore membro di funzione.

*  Se `Mp` è un metodo non generico e `Mq` è un metodo generico, quindi `Mp` migliore `Mq`.
*  In caso contrario, se `Mp` è applicabile in forma normale e `Mq` ha una `params` matrice ed è applicabile solo in forma espansa, quindi `Mp` migliore `Mq`.
*  In caso contrario, se `Mp` è dichiarato più parametri rispetto `Mq`, quindi `Mp` migliore `Mq`. Ciò può verificarsi se entrambi i metodi presentano `params` matrici e sono applicabile solo nelle relative forme espanse.
*  In caso contrario se tutti i parametri del `Mp` dispongono di un argomento corrispondente mentre gli argomenti predefiniti devono essere sostituiti per almeno un parametro facoltativo nelle `Mq` quindi `Mp` migliore `Mq`.
*  In caso contrario, se `Mp` dispone di tipi di parametro più specifici rispetto `Mq`, quindi `Mp` migliore `Mq`. Let `{R1, R2, ..., Rn}` e `{S1, S2, ..., Sn}` rappresentano i tipi dei parametri non espanso e privo di istanze `Mp` e `Mq`. `Mp`di tipi di parametro sono più specifici rispetto alle `Mq`del se, per ogni parametro, `Rx` non è meno specifico `Sx`e, per almeno un parametro, `Rx` più specifico `Sx`:
   *  Un parametro di tipo è meno specifico rispetto a un parametro non di tipo.
   *  In modo ricorsivo, un tipo costruito è più specifico rispetto a un altro tipo costruito (con lo stesso numero di argomenti di tipo) se almeno un argomento di tipo è più specifico e nessun argomento di tipo è meno specifico argomento di tipo corrispondente in altro.
   *  Un tipo di matrice è più specifico di un altro tipo di matrice (con lo stesso numero di dimensioni) se il tipo di elemento della prima riga è più specifico rispetto al tipo di elemento del secondo.
*  In caso contrario, se un membro è un operatore non è elevato e l'altro è un operatore lift, quello non elevato è migliore.
*  In caso contrario, nessuno dei due membri di funzione sono migliore.

#### <a name="better-conversion-from-expression"></a>Conversione migliore dall'espressione

Ha una conversione implicita `C1` che converte da un'espressione `E` a un tipo `T1`e una conversione implicita `C2` che consente di convertire un'espressione `E` a un tipo `T2`, `C1` è un ***migliore conversione*** rispetto `C2` se `E` corrisponde esattamente `T2` e contiene almeno uno dei seguenti:

* `E` corrisponde esattamente `T1` ([esattamente corrispondenti espressioni](expressions.md#exactly-matching-expression))
* `T1` è una destinazione di conversione migliore `T2` ([definire meglio conversione](expressions.md#better-conversion-target))

#### <a name="exactly-matching-expression"></a>Espressione esattamente corrispondente

Data un'espressione `E` e un tipo `T`, `E` corrisponde esattamente `T` se una delle seguenti condizioni:

*  `E` dispone di un tipo `S`, e non esiste una conversione di identità da `S` a `T`
*  `E` funzione anonima `T` è un tipo di delegato `D` o un tipo di albero delle espressioni `Expression<D>` e una delle seguenti condizioni:
   *  Un tipo restituito dedotto `X` esiste per `E` nel contesto dell'elenco dei parametri `D` ([membro derivato il tipo restituito](expressions.md#inferred-return-type)), ed esiste una conversione di identità da `X` per il tipo restituito di `D`
   *  Entrambi `E` è non asincrone e `D` ha un tipo restituito `Y` oppure `E` è asincrono e `D` presenta un tipo restituito `Task<Y>`, e una delle seguenti condizioni:
      * Il corpo di `E` è un'espressione che corrisponde esattamente `Y`
      * Il corpo di `E` è un blocco di istruzioni in cui ogni istruzione return restituisce un'espressione che corrisponde esattamente `Y`

#### <a name="better-conversion-target"></a>Destinazione di conversione migliore

Ha due tipi diversi `T1` e `T2`, `T1` è una destinazione di conversione migliore `T2` se nessuna conversione implicita dai `T2` a `T1` esiste, e contiene almeno uno dei seguenti:

*  Una conversione implicita da `T1` a `T2` esiste
*  `T1` è un tipo delegato `D1` o un tipo di albero delle espressioni `Expression<D1>`, `T2` è un tipo delegato `D2` o un tipo di albero delle espressioni `Expression<D2>`, `D1` presenta un tipo restituito `S1` e una del blocchi seguenti:
   * `D2` restituisce void
   * `D2` ha un tipo restituito `S2`, e `S1` è una destinazione di conversione migliore rispetto a `S2`
*  `T1` viene `Task<S1>`, `T2` viene `Task<S2>`, e `S1` è una destinazione di conversione migliore rispetto a `S2`
*  `T1` viene `S1` o `S1?` in cui `S1` è un tipo integrale con segno, e `T2` viene `S2` oppure `S2?` dove `S2` è un tipo integrale senza segno. In particolare:
   * `S1` viene `sbyte` e `S2` viene `byte`, `ushort`, `uint`, o `ulong`
   * `S1` viene `short` e `S2` viene `ushort`, `uint`, o `ulong`
   * `S1` viene `int` e `S2` è `uint`, o `ulong`
   * `S1` viene `long` e `S2` è `ulong`

#### <a name="overloading-in-generic-classes"></a>L'overload in classi generiche

Anche se le firme dichiarato devono essere univoche, è possibile che la sostituzione degli argomenti di tipo comporta firme identiche. In questi casi, le regole di risoluzione di conflitti di risoluzione dell'overload precedente selezionerà il membro più specifico.

Gli esempi seguenti illustrano gli overload che siano valide e non è valido in base a questa regola:

```csharp
interface I1<T> {...}

interface I2<T> {...}

class G1<U>
{
    int F1(U u);                  // Overload resolution for G<int>.F1
    int F1(int i);                // will pick non-generic

    void F2(I1<U> a);             // Valid overload
    void F2(I2<U> a);
}

class G2<U,V>
{
    void F3(U u, V v);            // Valid, but overload resolution for
    void F3(V v, U u);            // G2<int,int>.F3 will fail

    void F4(U u, I1<V> v);        // Valid, but overload resolution for    
    void F4(I1<V> v, U u);        // G2<I1<int>,int>.F4 will fail

    void F5(U u1, I1<V> v2);      // Valid overload
    void F5(V v1, U u2);

    void F6(ref U u);             // valid overload
    void F6(out V v);
}
```

### <a name="compile-time-checking-of-dynamic-overload-resolution"></a>Controllo della risoluzione dell'overload dinamico in fase di compilazione

Per le operazioni associate più in modo dinamico il set di possibili candidati per la risoluzione è sconosciuto al momento della compilazione. In alcuni casi, tuttavia il set di candidati è noto in fase di compilazione:

*  Chiamate di metodo statico con argomenti dinamici
*  Chiamate ai metodi di istanza in cui il destinatario non è un'espressione dinamica
*  Chiamate di un indicizzatore in cui il destinatario non è un'espressione dinamica
*  Chiamate al costruttore con argomenti dinamici

In questi casi viene eseguita una verifica limitata di in fase di compilazione per ciascun candidato verificare se uno di essi probabilmente è stato possibile applicare in fase di esecuzione. Questo controllo è costituito dai passaggi seguenti:

*  Inferenza del tipo parziale: Qualsiasi tipo di argomento che non dipendono direttamente o indirettamente un argomento di tipo `dynamic` viene inferito usando le regole delle [inferenza del tipo](expressions.md#type-inference). I restanti argomenti di tipo sono sconosciuti.
*  Errore controllo applicabilità parziale: Applicabilità viene controllato in base alla [membro di funzione applicabile](expressions.md#applicable-function-member), ma verranno ignorati i parametri i cui tipi sono sconosciuti.
*  Se nessun candidato supera questo test, si verifica un errore in fase di compilazione.

### <a name="function-member-invocation"></a>Chiamata di funzione membro

In questa sezione descrive il processo che ha luogo in fase di esecuzione per richiamare un membro di funzione specifica. Si presuppone che un processo in fase di associazione già ha determinato il membro da richiamare, possibilmente applicando la risoluzione dell'overload a un set di membri di funzione candidati.

Ai fini della descrizione del processo di chiamata, i membri di funzione sono suddivisi in due categorie:

*  Membri di funzione statica. Questi sono costruttori di istanze, i metodi statici, funzioni di accesso a proprietà statiche e operatori definiti dall'utente. I membri di funzione statica sono sempre non virtuale.
*  Membri di funzione di istanza. Questi sono metodi di istanza, funzioni di accesso alle proprietà e indicizzatori. I membri di funzione di istanza sono non virtuali o virtuale e sono sempre richiamati in una determinata istanza. L'istanza viene calcolato da un'espressione dell'istanza e sia accessibile all'interno del membro di funzione come `this` ([questo accesso](expressions.md#this-access)).

L'elaborazione in fase di esecuzione di una chiamata alla funzione membro prevede i passaggi seguenti, dove `M` è il membro di funzione e, se `M` è un membro di istanza, `E` è l'espressione dell'istanza:

*  Se `M` è un membro di funzione statica:
   * L'elenco di argomenti viene valutata come descritto in [elenchi di argomenti](expressions.md#argument-lists).
   * `M` viene richiamato.

*  Se `M` viene dichiarato un membro di funzione di istanza un *value_type*:
   * `E` viene valutato. Se questa versione di valutazione genera un'eccezione, non viene eseguiti alcun ulteriore passaggio.
   * Se `E` non è classificato come una variabile e quindi una variabile locale temporanea dei `E`del tipo viene creato e il valore di `E` viene assegnato a tale variabile. `E` è quindi riclassificato come un riferimento alla variabile locale temporanea. Variabile temporanea è accessibile come `this` all'interno di `M`, ma non in altri modi. In questo modo, solo quando `E` è una variabile true è possibile che il chiamante deve osservare le modifiche che `M` apporta al `this`.
   * L'elenco di argomenti viene valutata come descritto in [elenchi di argomenti](expressions.md#argument-lists).
   * `M` viene richiamato. La variabile fa riferimento `E` diventa la variabile fa riferimento `this`.

*  Se `M` viene dichiarato un membro di funzione di istanza un *reference_type*:
   * `E` viene valutato. Se questa versione di valutazione genera un'eccezione, non viene eseguiti alcun ulteriore passaggio.
   * L'elenco di argomenti viene valutata come descritto in [elenchi di argomenti](expressions.md#argument-lists).
   * Se il tipo di `E` è un *value_type*, una conversione boxing ([conversioni Boxing](types.md#boxing-conversions)) viene eseguita per convertire `E` digitare `object`, e `E` viene considerato per essere di tipo `object` nei passaggi seguenti. In questo caso `M` può essere solo un membro di `System.Object`.
   * Il valore di `E` viene verificata la validità. Se il valore di `E` viene `null`, un `System.NullReferenceException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.
   * Viene determinato l'implementazione della funzione membro da richiamare:
     * Se la fase di associazione tipo di `E` è un'interfaccia, il membro di funzione da richiamare è l'implementazione di `M` fornita dal tipo di runtime dell'istanza a cui fanno riferimento `E`. Questo membro di funzione viene determinato applicando le regole di mapping di interfaccia ([mapping dell'interfaccia](interfaces.md#interface-mapping)) per determinare l'implementazione di `M` forniti dal tipo in fase di esecuzione dell'istanza a cui fanno riferimento `E`.
     * In caso contrario, se `M` è un membro di funzione virtuale, il membro di funzione da richiamare è l'implementazione di `M` fornita dal tipo di runtime dell'istanza a cui fanno riferimento `E`. Questo membro di funzione viene determinato applicando le regole per determinare l'implementazione più derivato ([metodi virtuali](classes.md#virtual-methods)) della `M` rispetto al tipo di runtime dell'istanza a cui fanno riferimento `E`.
     * In caso contrario, `M` è un membro di funzione non virtuale e il membro di funzione da richiamare è `M` stesso.
   * Viene richiamato l'implementazione della funzione membro definito nel passaggio precedente. L'oggetto fa riferimento `E` diventa l'oggetto a cui fanno riferimento `this`.

#### <a name="invocations-on-boxed-instances"></a>Chiamate in istanze sottoposto a conversione boxing

Un membro di funzione implementata in una *value_type* può essere richiamato tramite un'istanza di sottoposto a conversione boxing di tale *value_type* nelle situazioni seguenti:

*  Quando il membro di funzione è un `override` di un metodo ereditato dal tipo `object` e viene richiamato tramite un'espressione dell'istanza del tipo `object`.
*  Quando il membro di funzione è un'implementazione di un membro di funzione di interfaccia e viene richiamato tramite un'espressione dell'istanza di un *interface_type*.
*  Quando tramite un delegato viene richiamato il membro di funzione.

In queste situazioni, l'istanza sottoposto a conversione boxing viene considerata per contenere una variabile del *value_type*, e questa variabile diventa la variabile fa riferimento `this` entro la chiamata alla funzione membro. In particolare, ciò significa che quando viene richiamato un membro di funzione in un'istanza sottoposto a conversione boxing, è possibile che il membro di funzione modificare il valore contenuto nell'istanza.

## <a name="primary-expressions"></a>Espressioni principali:

Espressioni primarie includono i moduli più semplici delle espressioni.

```antlr
primary_expression
    : primary_no_array_creation_expression
    | array_creation_expression
    ;

primary_no_array_creation_expression
    : literal
    | interpolated_string_expression
    | simple_name
    | parenthesized_expression
    | member_access
    | invocation_expression
    | element_access
    | this_access
    | base_access
    | post_increment_expression
    | post_decrement_expression
    | object_creation_expression
    | delegate_creation_expression
    | anonymous_object_creation_expression
    | typeof_expression
    | checked_expression
    | unchecked_expression
    | default_value_expression
    | nameof_expression
    | anonymous_method_expression
    | primary_no_array_creation_expression_unsafe
    ;
```

Espressioni primarie sono divise tra *array_creation_expression*s e *primary_no_array_creation_expression*s. Considerando l'espressione di creazione matrici in questo modo, invece di elenco, con le altre forme espressione semplice, abilita la grammatica da non consentire, ad esempio di codice potenzialmente poco chiaro
```csharp
object o = new int[3][1];
```
in caso contrario, che verrebbe interpretato come
```csharp
object o = (new int[3])[1];
```

### <a name="literals"></a>Valori letterali

Oggetto *primary_expression* costituito da un *letterale* ([valori letterali](lexical-structure.md#literals)) è classificato come un valore.


### <a name="interpolated-strings"></a>Stringhe interpolate

Un' *interpolated_string_expression* costituito da un `$` segno seguito da un normale o verbatim valore letterale stringa, in cui aree libere, delimitate da `{` e `}`, racchiudere le espressioni e formattazione specifiche. Un'espressione di stringa interpolata è il risultato di un' *interpolated_string_literal* che è stato suddiviso in singoli token, come descritto in [i valori letterali stringa interpolata](lexical-structure.md#interpolated-string-literals).

```antlr
interpolated_string_expression
    : '$' interpolated_regular_string
    | '$' interpolated_verbatim_string
    ;

interpolated_regular_string
    : interpolated_regular_string_whole
    | interpolated_regular_string_start interpolated_regular_string_body interpolated_regular_string_end
    ;

interpolated_regular_string_body
    : interpolation (interpolated_regular_string_mid interpolation)*
    ;

interpolation
    : expression
    | expression ',' constant_expression
    ;

interpolated_verbatim_string
    : interpolated_verbatim_string_whole
    | interpolated_verbatim_string_start interpolated_verbatim_string_body interpolated_verbatim_string_end
    ;

interpolated_verbatim_string_body
    : interpolation (interpolated_verbatim_string_mid interpolation)+
    ;
```

Il *constant_expression* in un'interpolazione deve avere una conversione implicita a `int`.

Un' *interpolated_string_expression* viene classificata come un valore. Se viene immediatamente convertito `System.IFormattable` o `System.FormattableString` con una conversione implicita di stringa interpolata ([implicite interpolate conversioni di stringhe](conversions.md#implicit-interpolated-string-conversions)), l'espressione di stringa interpolata con tale tipo. In caso contrario, dispone del tipo `string`.

Se il tipo di una stringa interpolata `System.IFormattable` oppure `System.FormattableString`, il significato è una chiamata a `System.Runtime.CompilerServices.FormattableStringFactory.Create`. Se il tipo è `string`, il significato dell'espressione è una chiamata a `string.Format`. In entrambi i casi, l'elenco di argomenti della chiamata è costituito da una stringa di formato letterale con segnaposto per ogni interpolazione e un argomento per ogni espressione corrispondente per i segnaposto.

Il valore letterale stringa di formato viene costruita come indicato di seguito, dove `N` è il numero di interpolazione nel *interpolated_string_expression*:

*  Se un' *interpolated_regular_string_whole* o un' *interpolated_verbatim_string_whole* segue il `$` accede, il valore letterale stringa di formato è tale token.
*  In caso contrario, il valore letterale stringa di formato costituito da: 
   *  Prima di *interpolated_regular_string_start* o *interpolated_verbatim_string_start*
   *  Quindi per ogni numero `I` dal `0` a `N-1`: 
      * La rappresentazione decimale di `I`
      * Quindi, se corrispondente *interpolazione* ha una *constant_expression*, una `,` (virgola) seguito dalla rappresentazione di decimale del valore della *constant_expression*
      * L'oggetto *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_mid* o *interpolated_ verbatim_string_end* subito dopo l'interpolazione corrispondente.

Gli argomenti successivi sono semplicemente il *espressioni* dalle *interpolazioni* (se presente), in ordine.

TODO: esempi.


### <a name="simple-names"></a>Nomi semplici

Oggetto *simple_name* è costituito da un identificatore, seguito facoltativamente da un elenco di argomenti:

```antlr
simple_name
    : identifier type_argument_list?
    ;
```

Oggetto *simple_name* può essere nel formato `I` o del form `I<A1,...,Ak>`, dove `I` è un identificatore singolo e `<A1,...,Ak>` è una funzione facoltativa *type_argument_list*. Se non si specifica *type_argument_list* è specificato, prendere in considerazione `K` sia pari a zero. Il *simple_name* viene valutato e classificato come segue:

*  Se `K` è uguale a zero e il *simple_name* viene visualizzato all'interno di una *blocco* e, se il *blocco*del (o un tipo di inclusione *blocco*del) locale lo spazio di dichiarazione di variabile ([dichiarazioni](basic-concepts.md#declarations)) contiene una variabile locale, un parametro o una costante con nome `I`, quindi il *simple_name* fa riferimento alla variabile locale, parametro o una costante ed è classificato come una variabile o un valore.
*  Se `K` è uguale a zero e il *simple_name* viene visualizzato all'interno del corpo di una dichiarazione di metodo generico e se tale dichiarazione include un parametro di tipo con nome `I`, quindi il *simple_name*fa riferimento a tale parametro di tipo.
*  In caso contrario, per ogni tipo di istanza `T` ([del tipo di istanza](classes.md#the-instance-type)), a partire dal tipo di istanza della dichiarazione del tipo che lo contiene e continuare con il tipo di istanza di ogni classe o struct che lo contiene dichiarazione (se presente):
   *  Se `K` è zero e la dichiarazione di `T` include un parametro di tipo con nome `I`, quindi il *simple_name* fa riferimento a tale parametro di tipo.
   *  In caso contrario, se una ricerca di membri ([ricerca di membri](expressions.md#member-lookup)) della `I` nelle `T` con `K`  gli argomenti di tipo produce una corrispondenza:
      * Se `T` è il tipo di istanza del tipo classe o struct che la contiene e la ricerca individua uno o più metodi, il risultato è un gruppo di metodi con un'espressione dell'istanza associata di `this`. Se è stato specificato un elenco di argomenti, viene utilizzato nella chiamata a un metodo generico ([chiamate al metodo](expressions.md#method-invocations)).
      * In caso contrario, se `T` è il tipo di istanza del tipo classe o struct che li contiene, se la ricerca identifica un membro di istanza e se il riferimento si trova all'interno del corpo di un costruttore di istanza, un metodo di istanza o una funzione di accesso di istanza, il risultato è lo stesso dell'accesso ai membri ([accesso al membro](expressions.md#member-access)) nel formato `this.I`. Questo può accadere solo quando `K` è uguale a zero.
      * In caso contrario, il risultato è lo stesso dell'accesso ai membri ([accesso al membro](expressions.md#member-access)) nel formato `T.I` o `T.I<A1,...,Ak>`. In questo caso, è un errore in fase di associazione per il *simple_name* per fare riferimento a un membro di istanza.

*  In caso contrario, per ogni spazio dei nomi `N`, che inizia con lo spazio dei nomi in cui la *simple_name* , proseguendo con ogni inclusione dello spazio dei nomi (se presente) e terminando con lo spazio dei nomi globale, i passaggi seguenti sono valutato fino a quando non viene individuata un'entità:
   *  Se `K` è uguale a zero e `I` è il nome di uno spazio dei nomi in `N`, quindi:
      * Se la posizione in cui il *simple_name* si verifica è racchiuso da una dichiarazione dello spazio dei nomi per `N` e la dichiarazione dello spazio dei nomi contiene un' *extern_alias_directive* o  *using_alias_directive* che associa il nome `I` con uno spazio dei nomi o un tipo, il *simple_name* è ambiguo e si verifica un errore in fase di compilazione.
      * In caso contrario, il *simple_name* fa riferimento allo spazio dei nomi denominato `I` in `N`.
   *  In caso contrario, se `N` contiene un tipo accessibile con nome `I` e `K`  parametri di tipo, quindi:
      * Se `K` è zero e il percorso in cui la *simple_name* si verifica è racchiuso da una dichiarazione dello spazio dei nomi per `N` e la dichiarazione dello spazio dei nomi contiene un *extern_alias_directive*oppure *using_alias_directive* che associa il nome `I` con uno spazio dei nomi o un tipo, il *simple_name* è ambiguo e si verifica un errore in fase di compilazione.
      * In caso contrario, il *namespace_or_type_name* fa riferimento al tipo costruito con gli argomenti di tipo specificato.
   *  In caso contrario, se la posizione in cui il *simple_name* si verifica è racchiuso da una dichiarazione dello spazio dei nomi per `N`:
      * Se `K` è uguale a zero e la dichiarazione dello spazio dei nomi contiene un *extern_alias_directive* oppure *using_alias_directive* che associa il nome `I` con uno spazio dei nomi importato o tipo, il *simple_name* fa riferimento a tale spazio dei nomi o tipo.
      * In caso contrario, se le dichiarazioni degli spazi dei nomi e il tipo importato per la *using_namespace_directive*s e *using_static_directive*s della dichiarazione dello spazio dei nomi contiene esattamente un tipo accessibile o membro statico senza estensione con nome `I` e `K`  parametri di tipo, il *simple_name* fa riferimento a quel tipo o membro costruito con gli argomenti di tipo specificato.
      * In caso contrario, se gli spazi dei nomi e i tipi importati mediante il *using_namespace_directive*s della dichiarazione dello spazio dei nomi contiene più di un tipo accessibile o il metodo di estensione non membro statico con nome `I` e`K`  parametri di tipo, il *simple_name* è ambiguo e si verifica un errore.

   Si noti che questo passaggio è esattamente parallelo per il passaggio corrispondente per l'elaborazione di un *namespace_or_type_name* ([Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)).

*  In caso contrario, il *simple_name* è non definito e si verifica un errore in fase di compilazione.


### <a name="parenthesized-expressions"></a>Espressioni tra parentesi

Oggetto *parenthesized_expression* costituito da un *espressione* racchiuso tra parentesi.

```antlr
parenthesized_expression
    : '(' expression ')'
    ;
```

Oggetto *parenthesized_expression* viene valutato valutando il *espressione* all'interno delle parentesi. Se il *espressione* racchiusa tra parentesi indica uno spazio dei nomi o un tipo, si verifica un errore in fase di compilazione. In caso contrario, il risultato del *parenthesized_expression* è il risultato della valutazione dell'oggetto contenuto *espressione*.

### <a name="member-access"></a>Accesso ai membri

Oggetto *member_access* costituito da un *primary_expression*, una *predefined_type*, o un *qualified_alias_member*, seguito da un"`.`"token, seguita da un' *identificatore*, seguito facoltativamente da una *type_argument_list*.

```antlr
member_access
    : primary_expression '.' identifier type_argument_list?
    | predefined_type '.' identifier type_argument_list?
    | qualified_alias_member '.' identifier
    ;

predefined_type
    : 'bool'   | 'byte'  | 'char'  | 'decimal' | 'double' | 'float' | 'int' | 'long'
    | 'object' | 'sbyte' | 'short' | 'string'  | 'uint'   | 'ulong' | 'ushort'
    ;
```

Il *qualified_alias_member* definita in produzione [qualificatori di alias Namespace](namespaces.md#namespace-alias-qualifiers).

Oggetto *member_access* può essere nel formato `E.I` o del form `E.I<A1, ..., Ak>`, dove `E` è un'espressione primaria, `I` è un identificatore singolo e `<A1, ..., Ak>` è una funzione facoltativa  *type_argument_list*. Se non si specifica *type_argument_list* è specificato, prendere in considerazione `K` sia pari a zero.

Oggetto *member_access* con un *primary_expression* di tipo `dynamic` viene associata in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)). In questo caso il compilatore classifica l'accesso al membro come un accesso a proprietà di tipo `dynamic`. Le regole seguenti per determinare il significato del *member_access* vengono quindi applicati in fase di esecuzione, usando il tipo di fase di esecuzione anziché al tipo di fase di compilazione delle *primary_expression*. Se questa classificazione in fase di esecuzione conduce a un gruppo di metodi, l'accesso ai membri deve essere il *primary_expression* di un *invocation_expression*.

Il *member_access* viene valutato e classificato come segue:

*  Se `K` è uguale a zero e `E` è uno spazio dei nomi e `E` contiene uno spazio dei nomi annidato con nome `I`, il risultato è lo spazio dei nomi.
*  In caso contrario, se `E` è uno spazio dei nomi e `E` contiene un tipo accessibile con nome `I` e `K`  parametri di tipo, il risultato è il tipo costruito con gli argomenti di tipo specificato.
*  Se `E` è un *predefined_type* o una *primary_expression* classificata come un tipo, se `E` non è un parametro di tipo e la ricerca dei membri ([ricerca di membri](expressions.md#member-lookup)) dei `I` nelle `E` con `K`  parametri di tipo produce una corrispondenza, quindi `E.I` viene valutato e classificato come segue:
   *  Se `I` identifica un tipo, il risultato è il tipo costruito con gli argomenti di tipo specificato.
   *  Se `I` identifica uno o più metodi, il risultato è un gruppo di metodi senza espressione di istanza associato. Se è stato specificato un elenco di argomenti, viene utilizzato nella chiamata a un metodo generico ([chiamate al metodo](expressions.md#method-invocations)).
   *  Se `I` identifica un `static` proprietà, il risultato è un accesso a proprietà senza un'espressione di istanza associato.
   *  Se `I` identifica un `static` campo:
      * Se il campo `readonly` e il riferimento si trova fuori dal costruttore statico della classe o struct in cui il campo viene dichiarato, quindi il risultato è un valore, vale a dire il valore del campo statico `I` in `E`.
      * In caso contrario, il risultato è una variabile, ovvero il campo statico `I` in `E`.
   *  Se `I` identifica un `static` evento:
      * Se il riferimento si trova all'interno della classe o struct in cui viene dichiarato l'evento e l'evento è stato dichiarato senza *event_accessor_declarations* ([eventi](classes.md#events)), quindi `E.I` viene elaborato esattamente come se `I` fosse un campo statico.
      * In caso contrario, il risultato è l'accesso a un evento senza espressione di istanza associato.
   *  Se `I` identifica una costante, il risultato è un valore, vale a dire il valore della costante.
    * Se `I` identifica un membro di enumerazione, il risultato è un valore, vale a dire il valore del membro di enumerazione.
    * In caso contrario, `E.I` è un riferimento al membro non valido e si verifica un errore in fase di compilazione.
*  Se `E` è un accesso a proprietà, accesso all'indicizzatore, una variabile o un valore, il cui tipo è `T`e una ricerca di membri ([ricerca di membri](expressions.md#member-lookup)) di `I` nella `T` con `K`  argomenti di tipo produce una corrispondenza, quindi `E.I` viene valutato e classificato come segue:
   *  Innanzitutto, se `E` è una proprietà o l'accesso dell'indicizzatore, quindi il valore della proprietà o ottenuto accesso all'indicizzatore ([i valori delle espressioni](expressions.md#values-of-expressions)) e `E` è riclassificato come valore.
   *  Se `I` identifica uno o più metodi, il risultato è un gruppo di metodi con un'espressione dell'istanza associata di `E`. Se è stato specificato un elenco di argomenti, viene utilizzato nella chiamata a un metodo generico ([chiamate al metodo](expressions.md#method-invocations)).
   *  Se `I` identifica una proprietà dell'istanza,
      * Se `E` viene `this`, `I` identifica una proprietà implementata automaticamente ([proprietà implementate automaticamente](classes.md#automatically-implemented-properties)) senza un setter e il riferimento si trova all'interno di un costruttore di istanza per un tipo di classe o struct `T`, il risultato è una variabile, vale a dire il campo nascosto sottostante per la proprietà automatica specificato da `I` nell'istanza di `T` fornito dal `this`.
      * In caso contrario, il risultato è un accesso a proprietà con un'espressione dell'istanza associata di `E`.
   *  Se `T` è un *class_type* e `I` identifica un campo di istanza di tale *class_type*:
      * Se il valore di `E` viene `null`, quindi un `System.NullReferenceException` viene generata un'eccezione.
      * In caso contrario, se il campo `readonly` e il riferimento si trova all'esterno di un costruttore di istanza della classe in cui il campo viene dichiarato, quindi il risultato è un valore, vale a dire il valore del campo `I` nell'oggetto fa riferimento `E`.
      * In caso contrario, il risultato è una variabile, vale a dire il campo `I` nell'oggetto fa riferimento `E`.
   *  Se `T` è un *struct_type* e `I` identifica un campo di istanza di tale *struct_type*:
      * Se `E` è un valore, o se il campo `readonly` e il riferimento si trova all'esterno di un costruttore di istanza dello struct in cui il campo viene dichiarato, quindi il risultato è un valore, vale a dire il valore del campo `I` nell'istanza della struttura specificata da  `E`.
      * In caso contrario, il risultato è una variabile, vale a dire il campo `I` nell'istanza della struttura specificata da `E`.
   *  Se `I` identifica un evento di istanza:
      * Se il riferimento si trova all'interno della classe o struct in cui viene dichiarato l'evento e l'evento è stato dichiarato senza *event_accessor_declarations* ([eventi](classes.md#events)), e il riferimento non vengono eseguite con il parte sinistra di una `+=` oppure `-=` operatore, quindi `E.I` viene elaborato esattamente come se `I` era un campo di istanza.
      * In caso contrario, il risultato è l'accesso a un evento con un'espressione dell'istanza associata di `E`.
*  In caso contrario, viene effettuato un tentativo di elaborare `E.I` come una chiamata al metodo di estensione ([chiamate al metodo di estensione](expressions.md#extension-method-invocations)). In caso di errore, `E.I` è un riferimento al membro non valido e si verifica un errore in fase di associazione.

#### <a name="identical-simple-names-and-type-names"></a>I nomi dei tipi e nomi semplici identici

In un accesso ai membri del modulo `E.I`, se `E` è un identificatore singolo e se il significato dei `E` come un *simple_name* ([nomi semplici](expressions.md#simple-names)) è una costante, campo, proprietà, variabile locale, o un parametro con lo stesso tipo il significato dei `E` come un *type_name* ([Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)), quindi entrambi possibili significati della `E` sono è consentito. I due possibili significati della `E.I` mai sono ambigue, poiché `I` deve necessariamente essere un membro del tipo `E` in entrambi i casi. In altre parole, la regola semplicemente consente l'accesso ai membri statici e i tipi annidati di `E` in cui in caso contrario, potrebbe essersi verificato un errore in fase di compilazione. Ad esempio:
```csharp
struct Color
{
    public static readonly Color White = new Color(...);
    public static readonly Color Black = new Color(...);

    public Color Complement() {...}
}

class A
{
    public Color Color;                // Field Color of type Color

    void F() {
        Color = Color.Black;           // References Color.Black static member
        Color = Color.Complement();    // Invokes Complement() on Color field
    }

    static void G() {
        Color c = Color.White;         // References Color.White static member
    }
}
```

#### <a name="grammar-ambiguities"></a>Ambiguità di grammatica

Produzioni per *simple_name* ([nomi semplici](expressions.md#simple-names)) e *member_access* ([l'accesso ai membri](expressions.md#member-access)) può dar luogo a ambiguità di grammatica delle espressioni. Ad esempio, l'istruzione:
```
F(G<A,B>(7));
```
potrebbero essere interpretati come una chiamata a `F` con due argomenti, `G < A` e `B > (7)`. In alternativa, può essere interpretata come una chiamata a `F` con un solo argomento, ovvero una chiamata a un metodo generico `G` con due argomenti di tipo e un argomento regolare.

Se una sequenza di token può essere analizzata (nel contesto) come un *simple_name* ([nomi semplici](expressions.md#simple-names)), *member_access* ([l'accesso ai membri](expressions.md#member-access)), o *pointer_member_access* ([accesso ai membri del puntatore](unsafe-code.md#pointer-member-access)) che termina con un *type_argument_list* ([gli argomenti di tipo](types.md#type-arguments)), il token subito dopo la chiusura `>` token viene esaminato. Se è uno di
```csharp
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
```
il *type_argument_list* viene mantenuto come parte del *simple_name*, *member_access* oppure *pointer_member_access* e qualsiasi altre analisi della sequenza di token viene ignorata. In caso contrario, il *type_argument_list* non viene considerato a far parte delle *simple_name*, *member_access* o *pointer_member_access*, anche se è presente alcun ulteriore analisi della sequenza di token. Si noti che queste regole non vengono applicate durante l'analisi di un *type_argument_list* in un *namespace_or_type_name* ([Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)). L'istruzione
```csharp
F(G<A,B>(7));
```
verrà, in base a questa regola, interpretato come una chiamata a `F` con un solo argomento, ovvero una chiamata a un metodo generico `G` con due argomenti di tipo e un argomento regolare. Le istruzioni
```csharp
F(G < A, B > 7);
F(G < A, B >> 7);
```
verrà ognuno interpretato come una chiamata a `F` con due argomenti. L'istruzione
```csharp
x = F < A > +y;
```
verrà interpretato come un operatore minore di, maggiore di operatore e operatore unario, come se fosse stata scritta l'istruzione `x = (F < A) > (+y)`, anziché come un *simple_name* con un *type_argument_list* seguita da un operatore binario +. Nell'istruzione
```csharp
x = y is C<T> + z;
```
i token `C<T>` vengono interpretati come un *namespace_or_type_name* con un *type_argument_list*.

### <a name="invocation-expressions"></a>Espressioni di chiamata

Un' *invocation_expression* viene utilizzato per richiamare un metodo.

```antlr
invocation_expression
    : primary_expression '(' argument_list? ')'
    ;
```

Un' *invocation_expression* in modo dinamico è associato ([binding dinamico](expressions.md#dynamic-binding)) se contiene almeno uno dei seguenti:

* Il *primary_expression* è di tipo in fase di compilazione `dynamic`.
* Almeno un argomento dell'elemento facoltativo *argument_list* è di tipo in fase di compilazione `dynamic` e il *primary_expression* non dispone di un tipo delegato.

In questo caso il compilatore consente di classificare le *invocation_expression* come valore di tipo `dynamic`. Le regole seguenti per determinare il significato del *invocation_expression* vengono quindi applicati in fase di esecuzione, usando il tipo di fase di esecuzione anziché al tipo di fase di compilazione di quelle del *primary_expression* e gli argomenti che hanno il tipo di fase di compilazione `dynamic`. Se il *primary_expression* non è in fase di compilazione `dynamic`, quindi la chiamata del metodo sottoposto a un controllo in fase di compilazione limitato, come descritto in [controllo della risoluzione dell'overload dinamico in fase di compilazione ](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

Il *primary_expression* di un *invocation_expression* deve essere un gruppo di metodi o un valore di una *delegate_type*. Se il *primary_expression* è un gruppo di metodi, il *invocation_expression* è una chiamata al metodo ([chiamate al metodo](expressions.md#method-invocations)). Se il *primary_expression* è un valore di una *delegate_type*, il *invocation_expression* è una chiamata al delegato ([chiamatedidelegati](expressions.md#delegate-invocations)). Se il *primary_expression* è un gruppo di metodi né un valore di una *delegate_type*, si verifica un errore in fase di associazione.

L'opzione facoltativa *argument_list* ([elenchi di argomenti](expressions.md#argument-lists)) fornisce i valori o riferimenti a variabili per i parametri del metodo.

Il risultato della valutazione di un' *invocation_expression* viene classificata come indicato di seguito:

*  Se il *invocation_expression* richiama un metodo o delegato che restituisce `void`, il risultato è nothing. Un'espressione che viene classificata come non è consentita solo nel contesto di un *statement_expression* ([le istruzioni di espressione](statements.md#expression-statements)) o come corpo di un *lambda_expression*([Espressioni di funzioni anonime](expressions.md#anonymous-function-expressions)). In caso contrario, si verifica un errore in fase di associazione.
*  In caso contrario, il risultato è un valore del tipo restituito dal metodo o delegato.

#### <a name="method-invocations"></a>Chiamate ai metodi

Per una chiamata al metodo, il *primary_expression* delle *invocation_expression* deve essere un gruppo di metodi. Il gruppo di metodi identifica il metodo da richiamare o il set di metodi di overload da cui scegliere un metodo specifico da richiamare. Nel secondo caso, la scelta del metodo specifico da richiamare è in base al contesto fornito dai tipi degli argomenti in di *argument_list*.

L'elaborazione in fase di associazione di una chiamata al metodo del form `M(A)`, dove `M` è un gruppo di metodi (possibilmente con un *type_argument_list*), e `A` è facoltativo *argument_ elenco*, costituita dai passaggi seguenti:

*  Il set di metodi candidati per la chiamata al metodo viene costruito. Per ogni metodo `F` associati al gruppo metodo `M`:
   *  Se `F` non è generica, `F` è un candidato quando:
      * `M` non dispone di alcun elenco di argomenti tipo, e
      * `F` è applicabile alle `A` ([membro di funzione applicabile](expressions.md#applicable-function-member)).
   *  Se `F` è generico e `M` non dispone di alcun elenco di argomenti tipo, `F` è un candidato quando:
      * L'inferenza del tipo ([inferenza del tipo](expressions.md#type-inference)) ha esito positivo, l'inferenza di un elenco di argomenti tipo per la chiamata, e
      * Una volta che vengono sostituiti gli argomenti tipo dedotti per i parametri di tipo di metodo corrispondente, tutti i tipi costruiti nell'elenco dei parametri di F soddisfano i vincoli ([che soddisfano i vincoli](types.md#satisfying-constraints)) e nell'elenco di parametri di `F` è applicabile alle `A` ([membro di funzione applicabile](expressions.md#applicable-function-member)).
   *  Se `F` è generico e `M` include un elenco di argomenti, `F` è un candidato quando:
      * `F` ha lo stesso numero di parametri di tipo di metodo come sono stati specificati nell'elenco di argomenti tipo, e
      * Una volta che gli argomenti di tipo vengono sostituiti con i parametri di tipo di metodo corrispondente, tutti i tipi costruiti nell'elenco dei parametri di F soddisfano i vincoli ([che soddisfano i vincoli](types.md#satisfying-constraints)) e nell'elenco di parametri di `F` è applicabile alle `A` ([membro di funzione applicabile](expressions.md#applicable-function-member)).
*  Il set di metodi candidati viene ridotto affinché contenga solo i metodi da tipi più derivati: Per ogni metodo `C.F` nel set, in cui `C` è il tipo in cui il metodo `F` viene dichiarato, tutti i metodi dichiarati in un tipo di base di `C` vengono rimosse dal set. Inoltre, se `C` è diverso da un tipo di classe `object`, tutti i metodi dichiarati in un tipo di interfaccia vengono rimosse dal set. (Questa regola di quest'ultima ha effetto solo quando il gruppo di metodi è il risultato di una ricerca di membri in un parametro di tipo con una classe base effettiva diverso dall'oggetto e un insieme di interfacce efficace non vuoto.)
*  Se il set di metodi candidati risultante è vuoto, l'elaborazione e i passaggi seguenti non vengono applicate e invece viene effettuato un tentativo di elaborare la chiamata come una chiamata al metodo di estensione ([chiamate al metodo di estensione](expressions.md#extension-method-invocations)). In caso di errore, quindi esiste alcun metodo applicabile, e si verifica un errore in fase di associazione.
*  Il metodo migliore del set di metodi candidati viene identificato mediante le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution). Se non è possibile identificare un metodo migliore, la chiamata al metodo è ambiguo e si verifica un errore in fase di associazione. Quando si esegue la risoluzione dell'overload, i parametri di un metodo generico sono considerati dopo la sostituzione di argomenti di tipo (specificati o dedotti) per i parametri di tipo di metodo corrispondente.
*  Viene eseguita la convalida finale del metodo migliore scelta:
   * Il metodo viene convalidato nel contesto del gruppo di metodi: Se il metodo migliore è un metodo statico, il gruppo di metodi deve derivare da una *simple_name* o una *member_access* tramite un tipo. Se il metodo migliore è un metodo di istanza, il gruppo di metodi deve derivare da una *simple_name*, un *member_access* tramite una variabile o un valore, o un *base_access*. Se nessuno di questi requisiti è true, si verifica un errore in fase di associazione.
   * Se il metodo migliore è un metodo generico, gli argomenti di tipo (fornito o derivato) vengono controllati in base ai vincoli ([che soddisfano i vincoli](types.md#satisfying-constraints)) dichiarati nel metodo generico. Se qualsiasi argomento di tipo non soddisfa i relativi vincoli sul parametro di tipo, si verifica un errore in fase di associazione.

Dopo aver selezionato un metodo ed convalidato in fase di associazione per i passaggi precedenti, la chiamata in fase di esecuzione effettiva viene elaborata in base alle regole di chiamata di funzione membro descritto in [controllo della risoluzione dell'overload dinamico in fase di compilazione ](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

L'effetto intuitiva delle regole di risoluzione descritto in precedenza è come segue: Per individuare il metodo particolare richiamato da una chiamata al metodo, iniziare con il tipo indicato dalla chiamata del metodo e procedere fino alla catena di ereditarietà fino a quando non viene trovata almeno un metodo applicabile, accessibile, non-override dichiarazione. Quindi eseguire l'inferenza del tipo e sul set di metodi applicabili, accessibili, non-override dichiarati in quel tipo di risoluzione dell'overload e richiamare il metodo così selezionato. Se è stato trovato alcun metodo, provare invece a elaborare la chiamata come una chiamata al metodo di estensione.

#### <a name="extension-method-invocations"></a>Chiamate ai metodi di estensione

In una chiamata al metodo ([chiamate nelle istanze di tipo boxed](expressions.md#invocations-on-boxed-instances)) di uno dei formati
```csharp
expr . identifier ( )

expr . identifier ( args )

expr . identifier < typeargs > ( )

expr . identifier < typeargs > ( args )
```
Se la normale elaborazione della chiamata di non trova alcun metodo applicabile, viene effettuato un tentativo di elaborare il costrutto come una chiamata al metodo di estensione. Se *expr* o uno qualsiasi delle *args* dispone di tipi in fase di compilazione `dynamic`, i metodi di estensione non verranno applicata.

L'obiettivo consiste nel trovare il meglio *type_name* `C`, in modo che la chiamata del metodo statico corrispondente può essere eseguita:
```csharp
C . identifier ( expr )

C . identifier ( expr , args )

C . identifier < typeargs > ( expr )

C . identifier < typeargs > ( expr , args )
```

Un metodo di estensione `Ci.Mj` viene ***idonei*** se:

*  `Ci` è una classe non generica, non annidata
*  Il nome della `Mj` è *identificatore*
*  `Mj` è accessibile e applicabili quando vengono applicati agli argomenti come un metodo statico, come illustrato in precedenza
*  Esiste un'identità implicita, riferimento o conversione boxing dal *expr* al tipo del primo parametro di `Mj`.

La ricerca di `C` procede nel modo seguente:

*  Inizia con la dichiarazione dello spazio dei nomi contenitore più vicino, continuare con ogni dichiarazione dello spazio dei nomi che lo contiene e che termina con unità di compilazione che lo contiene, verranno eseguiti tentativi successivi per individuare un set di candidati dei metodi di estensione:
   * Se l'unità di compilazione o di spazio dei nomi specificato contiene direttamente le dichiarazioni di tipo non generico `Ci` con i metodi di estensione idonei `Mj`, quindi il set di questi metodi di estensione è il set di candidati.
   * Se i tipi `Ci` importato dal *using_static_declarations* e direttamente dichiarati negli spazi dei nomi importato da *using_namespace_directive*s nella compilazione o spazio dei nomi di unità specificata direttamente contiene i metodi di estensione idonei `Mj`, quindi il set di questi metodi di estensione è il set di candidati.
*  Se non viene trovato alcun set di candidati in qualsiasi unità di compilazione o di dichiarazione dello spazio dei nomi che lo contiene, si verifica un errore in fase di compilazione.
*  In caso contrario, la risoluzione dell'overload viene applicata al candidato impostato come descritto in ([risoluzione dell'Overload](expressions.md#overload-resolution)). Se non viene trovato alcun metodo migliore, si verifica un errore in fase di compilazione.
*  `C` è il tipo all'interno del quale il metodo migliore è dichiarato come metodo di estensione.

Usando `C` come destinazione, la chiamata al metodo viene quindi elaborata come una chiamata al metodo statico ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).

Le regole precedenti indicano che i metodi di istanza hanno la precedenza sui metodi di estensione, che i metodi di estensione disponibili nelle dichiarazioni dello spazio dei nomi interni hanno la precedenza sui metodi di estensione disponibili nelle dichiarazioni di spazio dei nomi esterno e tale estensione i metodi dichiarati direttamente in uno spazio dei nomi hanno la precedenza sui metodi di estensione importati in tale spazio dei nomi stesso con un utilizzo della direttiva dello spazio dei nomi. Ad esempio:
```csharp
public static class E
{
    public static void F(this object obj, int i) { }

    public static void F(this object obj, string s) { }
}

class A { }

class B
{
    public void F(int i) { }
}

class C
{
    public void F(object obj) { }
}

class X
{
    static void Test(A a, B b, C c) {
        a.F(1);              // E.F(object, int)
        a.F("hello");        // E.F(object, string)

        b.F(1);              // B.F(int)
        b.F("hello");        // E.F(object, string)

        c.F(1);              // C.F(object)
        c.F("hello");        // C.F(object)
    }
}
```

Nell'esempio riportato `B`del metodo ha la precedenza sul primo metodo di estensione, e `C`del metodo ha la precedenza su entrambi i metodi di estensione.

```csharp
public static class C
{
    public static void F(this int i) { Console.WriteLine("C.F({0})", i); }
    public static void G(this int i) { Console.WriteLine("C.G({0})", i); }
    public static void H(this int i) { Console.WriteLine("C.H({0})", i); }
}

namespace N1
{
    public static class D
    {
        public static void F(this int i) { Console.WriteLine("D.F({0})", i); }
        public static void G(this int i) { Console.WriteLine("D.G({0})", i); }
    }
}

namespace N2
{
    using N1;

    public static class E
    {
        public static void F(this int i) { Console.WriteLine("E.F({0})", i); }
    }

    class Test
    {
        static void Main(string[] args)
        {
            1.F();
            2.G();
            3.H();
        }
    }
}
```

L'output di questo esempio è:
```
E.F(1)
D.G(2)
C.H(3)
```
`D.G` ha la precedenza sul `C.G`, e `E.F` ha la precedenza rispetto a entrambe `D.F` e `C.F`.

#### <a name="delegate-invocations"></a>Chiamate del delegato

Per una chiamata al delegato, il *primary_expression* delle *invocation_expression* deve essere un valore di una *delegate_type*. Inoltre, prendere in considerazione la *delegate_type* sia un membro di funzione con lo stesso elenco di parametri del *delegate_type*, il *delegate_type* deve essere (applicabile [Membro di funzione applicabile](expressions.md#applicable-function-member)) rispetto al *argument_list* del *invocation_expression*.

L'elaborazione in fase di esecuzione di una chiamata al delegato del form `D(A)`, dove `D` è un *primary_expression* di un *delegate_type* e `A` è un facoltativo*argument_list*, costituita dai passaggi seguenti:

*  `D` viene valutato. Se questa versione di valutazione causa un'eccezione, non verrà eseguito alcun ulteriore passaggio.
*  Il valore di `D` viene verificata la validità. Se il valore di `D` viene `null`, un `System.NullReferenceException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.
*  In caso contrario, `D` è un riferimento a un'istanza del delegato. Membro chiamate di funzione ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) vengono eseguite su ogni entità richiamabile nell'elenco chiamate del delegato. Per le entità richiamabile costituito da un'istanza e un metodo di istanza, l'istanza per la chiamata è l'istanza contenuta nell'entità richiamabile.

### <a name="element-access"></a>Accesso all'elemento

Un' *element_access* costituito da un *primary_no_array_creation_expression*, seguito da un "`[`" token, seguito da un *argument_list*, seguito da un " `]`"token. Il *argument_list* costituito da uno o più *argomento*s, separati da virgole.

```antlr
element_access
    : primary_no_array_creation_expression '[' expression_list ']'
    ;
```

Il *argument_list* di un *element_access* non può contenere `ref` o `out` argomenti.

Un' *element_access* in modo dinamico è associato ([binding dinamico](expressions.md#dynamic-binding)) se contiene almeno uno dei seguenti:

* Il *primary_no_array_creation_expression* è di tipo in fase di compilazione `dynamic`.
* Almeno un'espressione del *argument_list* è di tipo in fase di compilazione `dynamic` e il *primary_no_array_creation_expression* non dispone di un tipo di matrice.

In questo caso il compilatore consente di classificare le *element_access* come valore di tipo `dynamic`. Le regole seguenti per determinare il significato del *element_access* vengono quindi applicati in fase di esecuzione, usando il tipo di fase di esecuzione anziché al tipo di fase di compilazione di quelle del *primary_no_array_creation_expression*e *argument_list* espressioni che hanno il tipo di fase di compilazione `dynamic`. Se il *primary_no_array_creation_expression* non è in fase di compilazione `dynamic`, quindi l'accesso all'elemento viene sottoposto a un controllo in fase di compilazione limitato come descritto in [controllo dinamico in fase di compilazione risoluzione dell'overload](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

Se il *primary_no_array_creation_expression* di un *element_access* è un valore di un *array_type*, il *element_access* è un Matrice di accesso ([matrice accesso](expressions.md#array-access)). In caso contrario, il *primary_no_array_creation_expression* deve essere una variabile o un valore di una classe, struct o tipo di interfaccia che ha uno o più membri di un indicizzatore, nel qual caso il *element_access* è un accesso all'indicizzatore ([accesso all'indicizzatore](expressions.md#indexer-access)).

#### <a name="array-access"></a>Accesso a matrici

Per un accesso a matrice, il *primary_no_array_creation_expression* delle *element_access* deve essere un valore di un *array_type*. Inoltre, il *argument_list* di una matrice per contenere gli argomenti denominati non è consentito l'accesso. Il numero di espressioni presenti il *argument_list* deve essere uguale all'ordine di priorità del *array_type*, e ogni espressione deve essere di tipo `int`, `uint`, `long`, `ulong`, oppure deve essere convertibile in modo implicito a uno o più di questi tipi.

Il risultato della valutazione di un accesso a matrice è una variabile del tipo di elemento della matrice, vale a dire l'elemento della matrice selezionata in base ai valori delle espressioni disponibili nel *argument_list*.

L'elaborazione in fase di esecuzione di un accesso a matrice nel formato `P[A]`, dove `P` è un *primary_no_array_creation_expression* di un *array_type* e `A` è un *argument_list*, costituita dai passaggi seguenti:

*  `P` viene valutato. Se questa versione di valutazione causa un'eccezione, non verrà eseguito alcun ulteriore passaggio.
*  Le espressioni di indice del *argument_list* vengono valutati nell'ordine, da sinistra a destra. Dopo la valutazione di ogni espressione di indice, una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) viene eseguita su uno dei seguenti tipi: `int`, `uint`, `long`, `ulong`. Viene scelto il primo tipo in questo elenco per il quale esiste una conversione implicita. Ad esempio, se l'espressione di indice è di tipo `short` quindi una conversione implicita a `int` verrà eseguita, poiché le conversioni implicite dal `short` al `int` e da `short` per `long` sono possibili. Se la valutazione di un'espressione di indice o la conversione implicita successive genera un'eccezione, non altre espressioni di indice vengono valutate e senza ulteriori passaggi vengono eseguiti.
*  Il valore di `P` viene verificata la validità. Se il valore di `P` viene `null`, un `System.NullReferenceException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.
*  Il valore di ogni espressione nel *argument_list* viene confrontato con i limiti di ciascuna dimensione dell'istanza della matrice cui fa riferimento `P`. Se uno o più valori sono compreso nell'intervallo, un `System.IndexOutOfRangeException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.
*  Viene calcolato il percorso dell'elemento della matrice specificata da espressioni indice; e questa località diventa il risultato dell'accesso alla matrice.

#### <a name="indexer-access"></a>Accesso all'indicizzatore

Per un accesso all'indicizzatore, il *primary_no_array_creation_expression* delle *element_access* deve essere una variabile o un valore di una classe, struct o tipo di interfaccia, e questo tipo deve implementare una o più agli indicizzatori che sono applicabili in base al *argument_list* delle *element_access*.

L'elaborazione in fase di associazione di un accesso dell'indicizzatore nel formato `P[A]`, dove `P` è un *primary_no_array_creation_expression* di una classe, struct o tipo di interfaccia `T`, e `A` è un *argument_list*, costituita dai passaggi seguenti:

*  Il set di indicizzatori fornita da `T` viene costruito. Il set è costituito da tutti gli indicizzatori dichiarati nella `T` o un tipo di base del `T` che non sono presenti `override` dichiarazioni e sono accessibili nel contesto corrente ([accesso ai membri](basic-concepts.md#member-access)).
*  Il set viene ridotto a questi indicizzatori che sono applicabili e non è nascosto da altri indicizzatori. Le regole seguenti vengono applicate a ogni indicizzatore `S.I` nel set, in cui `S` è il tipo in cui l'indicizzatore `I` viene dichiarato:
   * Se `I` non è applicabile alle `A` ([membro di funzione applicabile](expressions.md#applicable-function-member)), quindi `I` viene rimosso dal set.
   * Se `I` è applicabile alle `A` ([membro di funzione applicabile](expressions.md#applicable-function-member)), quindi tutti gli indicizzatori dichiarati nel tipo di base `S` vengono rimosse dal set.
   * Se `I` è applicabile alle `A` ([membro di funzione applicabile](expressions.md#applicable-function-member)) e `S` è diverso da un tipo di classe `object`, tutti gli indicizzatori dichiarati in un'interfaccia vengono rimosse dal set.
*  Se il set di indicizzatori candidati risultante è vuoto, quindi non esistono alcun indicizzatore applicabile, e si verifica un errore in fase di associazione.
*  L'indicizzatore del set di indicizzatori candidato migliore viene identificato mediante le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution). Se non è possibile identificare un singolo indicizzatore migliore, l'accesso all'indicizzatore è ambiguo e si verifica un errore in fase di associazione.
*  Le espressioni di indice del *argument_list* vengono valutati nell'ordine, da sinistra a destra. Il risultato dell'elaborazione l'accesso all'indicizzatore è un'espressione classificata come un accesso all'indicizzatore. L'espressione di accesso dell'indicizzatore fa riferimento all'indicizzatore definito nel passaggio precedente e dispone di un'espressione dell'istanza associata di `P` e un elenco di argomenti di `A`.

A seconda del contesto in cui viene utilizzato, un accesso all'indicizzatore fa sì che la chiamata di uno il *funzione di accesso get* o nella *funzione di accesso set* dell'indicizzatore. Se l'accesso all'indicizzatore è la destinazione di un'assegnazione, il *funzione di accesso set* viene richiamata per assegnare un nuovo valore ([assegnamento semplice](expressions.md#simple-assignment)). In tutti gli altri casi, il *funzione di accesso get* viene richiamato per ottenere il valore corrente ([i valori delle espressioni](expressions.md#values-of-expressions)).

### <a name="this-access"></a>Questo tipo di accesso

Oggetto *this_access* costituito dalla parola riservata `this`.

```antlr
this_access
    : 'this'
    ;
```

Oggetto *this_access* è consentito solo nel *blocco* di un costruttore di istanza, un metodo di istanza o una funzione di accesso di istanza. Ha una dei significati seguenti:

*  Quando `this` viene usato in un *primary_expression* all'interno di un costruttore di istanza di una classe, viene classificata come un valore. Il tipo del valore è il tipo di istanza ([il tipo di istanza](classes.md#the-instance-type)) della classe all'interno del quale si verifica l'utilizzo e il valore è un riferimento all'oggetto in fase di costruzione.
*  Quando `this` viene usato in un *primary_expression* all'interno di un metodo di istanza o una funzione di accesso di istanza di una classe, viene classificata come un valore. Il tipo del valore è il tipo di istanza ([il tipo di istanza](classes.md#the-instance-type)) della classe all'interno del quale si verifica l'utilizzo e il valore è un riferimento all'oggetto per cui è stato richiamato il metodo o funzione di accesso.
*  Quando `this` viene usato in un *primary_expression* all'interno di un costruttore di istanza di uno struct, viene classificata come una variabile. Il tipo della variabile è il tipo di istanza ([il tipo di istanza](classes.md#the-instance-type)) dello struct all'interno del quale si verifica l'utilizzo e la variabile rappresenta la struttura in fase di costruzione. Il `this` variabile di un costruttore di istanze di uno struct si comporta esattamente come un `out` parametro del tipo struct, in particolare, ciò significa che la variabile deve essere assegnata in ogni percorso di esecuzione dell'istanza costruttore.
*  Quando `this` viene usato in un *primary_expression* all'interno di un metodo di istanza o una funzione di accesso di istanza di uno struct, viene classificata come una variabile. Il tipo della variabile è il tipo di istanza ([il tipo di istanza](classes.md#the-instance-type)) dello struct in cui viene effettuata l'utilizzo.
   * Se il metodo o funzione di accesso non è un iteratore ([iteratori](classes.md#iterators)), il `this` variabile rappresenta la struttura per il quale il metodo o funzione di accesso è stato richiamato e si comporta esattamente come un `ref` parametro del tipo struct.
   * Se il metodo o funzione di accesso è un iteratore di `this` variabile rappresenta una copia della struttura per il quale il metodo o funzione di accesso è stato richiamato e si comporta esattamente come un parametro di valore del tipo struct.

Sfrutta `this` in un *primary_expression* in un contesto diverso da quelli elencati in precedenza è un errore in fase di compilazione. In particolare, non è possibile fare riferimento a `this` in un metodo statico, una funzione di accesso proprietà statica o in un *variable_initializer* di una dichiarazione di campo.

### <a name="base-access"></a>Accesso di base

Oggetto *base_access* costituito dalla parola riservata `base` seguita da un "`.`" token e un identificatore o un *argument_list* racchiuso tra parentesi quadre:

```antlr
base_access
    : 'base' '.' identifier
    | 'base' '[' expression_list ']'
    ;
```

Oggetto *base_access* consente di accedere ai membri di classe di base che sono nascoste in modo analogo i membri denominati nella classe corrente o uno struct. Oggetto *base_access* è consentito solo nel *blocco* di un costruttore di istanza, un metodo di istanza o una funzione di accesso di istanza. Quando `base.I` si verifica in una classe o struct, `I` deve indicare un membro della classe di base di quella classe o struct. Analogamente, quando `base[E]` si verifica in una classe, un indicizzatore applicabile deve essere presente nella classe di base.

In fase di associazione *base_access* espressioni nel formato `base.I` e `base[E]` vengono valutate come se fossero scritti `((B)this).I` e `((B)this)[E]`, dove `B` è la classe di base della classe o uno struct in cui viene utilizzato il costrutto. Pertanto `base.I` e `base[E]` corrispondono ai `this.I` e `this[E]`, ad eccezione di `this` viene visualizzato come un'istanza della classe di base.

Quando un *base_access* fa riferimento a un membro di funzione virtuale (un metodo, proprietà o indicizzatore), la determinazione di cui funzione membro da richiamare in fase di esecuzione ([controllo della risoluzione dell'overload dinamico in fase di compilazione ](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) viene modificato. Il membro di funzione che viene richiamato viene determinato mediante la ricerca di implementazione più derivata ([metodi virtuali](classes.md#virtual-methods)) del membro funzione rispetto al `B` (invece che rispetto al tipo di runtime di `this`, come sarebbe accade per un accesso non in base). Di conseguenza, all'interno di un' `override` di un `virtual` membro di funzione, un *base_access* può essere utilizzato per richiamare l'implementazione ereditata del membro di funzione. Se il membro di funzione viene fatto riferimento da un *base_access* è astratto, si verifica un errore in fase di associazione.

### <a name="postfix-increment-and-decrement-operators"></a>Forma suffissa di incremento e decremento (operatori)

```antlr
post_increment_expression
    : primary_expression '++'
    ;

post_decrement_expression
    : primary_expression '--'
    ;
```

L'operando di un'operazione di incremento o decremento operazione deve essere un'espressione classificata come una variabile, un accesso a proprietà o un accesso all'indicizzatore. Il risultato dell'operazione è un valore dello stesso tipo dell'operando.

Se il *primary_expression* dispone del tipo in fase di compilazione `dynamic` , quindi l'operatore è associato in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)), il *post_increment_expression*oppure *post_decrement_expression* ha il tipo di fase di compilazione `dynamic` e le regole seguenti vengono applicate in fase di esecuzione utilizzando il tipo di fase di esecuzione del *primary_expression*.

Se l'operando di un suffisso quale incrementa o operazione di decremento è una proprietà o l'accesso dell'indicizzatore, la proprietà o l'indicizzatore deve avere entrambe un `get` e un `set` della funzione di accesso. Se ciò non avviene, si verifica un errore in fase di associazione.

Risoluzione dell'overload di operatore unario ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico. Predefined `++` e `--` operatori esistono per i tipi seguenti: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char` , `float`, `double`, `decimal`e qualsiasi tipo di enumerazione. Oggetto predefinito `++` gli operatori restituiscono il valore ottenuto aggiungendo 1 all'operando e oggetto predefinito `--` operatori restituiscono il valore ottenuto sottraendo 1 operando. In un `checked` contesto, se il risultato dell'addizione o sottrazione è compreso nell'intervallo del tipo di risultato e il tipo di risultato è un tipo integrale o enumerazione, un `System.OverflowException` viene generata un'eccezione.

L'elaborazione in fase di esecuzione di un forma suffissa di incremento o decremento operazione del form `x++` o `x--` prevede i passaggi seguenti:

*   Se `x` viene classificato come una variabile:
    * `x` viene valutata per produrre la variabile.
    * Il valore di `x` viene salvato.
    * L'operatore selezionato viene richiamato con il valore salvato di `x` come argomento.
    * Il valore restituito dall'operatore viene archiviato nel percorso specificato dalla valutazione di `x`.
    * Il valore salvato di `x` diventa il risultato dell'operazione.
*   Se `x` viene classificato come accesso alla proprietà o l'indicizzatore:
    * L'espressione dell'istanza (se `x` non è `static`) e l'elenco di argomenti (se `x` un accesso dell'indicizzatore) associato `x` vengono valutate, e i risultati vengono usati nella successiva `get` e `set` chiamate di funzione di accesso.
    * Il `get` funzione di accesso di `x` viene richiamato e il valore restituito viene salvato.
    * L'operatore selezionato viene richiamato con il valore salvato di `x` come argomento.
    * Il `set` funzione di accesso di `x` viene richiamato con il valore restituito dall'operatore come relativo `value` argomento.
    * Il valore salvato di `x` diventa il risultato dell'operazione.

Il `++` e `--` operatori supportano anche notazione del prefisso ([incremento prefisso e decremento (operatori)](expressions.md#prefix-increment-and-decrement-operators)). In genere, il risultato del `x++` o `x--` è il valore di `x` prima dell'operazione, mentre il risultato del `++x` o `--x` è il valore di `x` al termine dell'operazione. In entrambi i casi `x` stesso ha lo stesso valore al termine dell'operazione.

Un' `operator ++` o `operator --` implementazione può essere richiamata usando la notazione prefissa o suffissa. Non è possibile disporre di implementazioni di operatori separati per le due notazioni.

### <a name="the-new-operator"></a>Operatore new

Il `new` operatore viene usato per creare nuove istanze dei tipi.

Esistono tre forme di `new` espressioni:

*  Espressioni per la creazione di oggetti vengono utilizzate per creare nuove istanze di tipi di classi e tipi di valore.
*  Espressioni di creazione della matrice vengono utilizzate per creare nuove istanze dei tipi di matrice.
*  Consentono di creare nuove istanze del delegato di tipi di espressioni per la creazione di delegato.

Il `new` operatore implica la creazione di un'istanza di un tipo, ma non implica necessariamente l'allocazione dinamica della memoria. In particolare, le istanze dei tipi di valore non richiedono alcuna memoria aggiuntiva oltre le variabili in cui si trovano e si verifica alcuna allocazione dinamiche quando `new` viene usato per creare istanze dei tipi di valore.

#### <a name="object-creation-expressions"></a>Espressioni per la creazione di oggetti

Un' *object_creation_expression* viene usato per creare una nuova istanza di un *class_type* o un *value_type*.

```antlr
object_creation_expression
    : 'new' type '(' argument_list? ')' object_or_collection_initializer?
    | 'new' type object_or_collection_initializer
    ;

object_or_collection_initializer
    : object_initializer
    | collection_initializer
    ;
```

Il *tipo* di un *object_creation_expression* deve essere un *class_type*, un *value_type* o una *type_parameter* . Il *tipo* non può essere un' `abstract` *class_type*.

L'opzione facoltativa *argument_list* ([elenchi di argomenti](expressions.md#argument-lists)) è consentito solo se la *tipo* è un *class_type* o un *struct_ tipo*.

Un'espressione di creazione di oggetti è possibile omettere l'elenco di argomenti del costruttore e racchiudere tra parentesi fornito che include un inizializzatore di oggetto o un inizializzatore di raccolta. Omettendo l'elenco di argomenti del costruttore e racchiudendoli tra parentesi equivale a specificare un elenco di argomenti vuoto.

L'elaborazione di un'espressione di creazione di oggetti che include un inizializzatore di oggetto o un inizializzatore di insieme è costituito da elaborazione prima di tutto il costruttore di istanza e quindi elaborando le inizializzazioni membro o un elemento specificate dall'inizializzatore di oggetto ([ Inizializzatori di oggetto](expressions.md#object-initializers)) o un inizializzatore di raccolta ([inizializzatori di insieme](expressions.md#collection-initializers)).

Se uno qualsiasi degli argomenti facoltativi *argument_list* dispone del tipo in fase di compilazione `dynamic` il *object_creation_expression* viene associata in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)) e vengono applicate le regole seguenti in fase di esecuzione usando il tipo di fase di esecuzione di questi argomenti del *argument_list* che hanno il tipo in fase di compilazione `dynamic`. Tuttavia, la creazione dell'oggetto sottoposto a un controllo in fase di compilazione limitato come descritto in [controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

L'elaborazione in fase di associazione di un *object_creation_expression* del form `new T(A)`, dove `T` è un *class_type* o un *value_type* e`A` è una funzione facoltativa *argument_list*, costituita dai passaggi seguenti:

*   Se `T` è un *value_type* e `A` non è presente:
    * Il *object_creation_expression* è una chiamata al costruttore predefinito. Il risultato del *object_creation_expression* è un valore di tipo `T`, vale a dire il valore predefinito per `T` come definito nella [System. ValueType di tipo](types.md#the-systemvaluetype-type).
*   In caso contrario, se `T` è un *type_parameter* e `A` non è presente:
    * Se nessun vincolo di tipo valore o il vincolo del costruttore ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)) è stato specificato per `T`, si verifica un errore in fase di associazione.
    * Il risultato del *object_creation_expression* è un valore del tipo di runtime che il parametro di tipo è stato associato, vale a dire il risultato ottenuto richiamando il costruttore predefinito di quel tipo. Il tipo di runtime può essere un tipo riferimento o un tipo valore.
*   In caso contrario, se `T` è un *class_type* o una *struct_type*:
    * Se `T` è un `abstract` *class_type*, si verifica un errore in fase di compilazione.
    * Per richiamare il costruttore di istanza viene determinato usando le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution). Il set di costruttori di istanza è costituito da tutti i costruttori di istanza accessibile dichiarati nel `T` che sono applicabili alle `A` ([membro di funzione applicabile](expressions.md#applicable-function-member)). Se il set di costruttori di istanza è vuoto o se non è possibile identificare un costruttore di istanza migliore, si verifica un errore in fase di associazione.
    * Il risultato del *object_creation_expression* è un valore di tipo `T`, vale a dire il valore generato richiamando il costruttore di istanza definito nel passaggio precedente.
*  In caso contrario, il *object_creation_expression* non è valido, e si verifica un errore in fase di associazione.

Anche se il *object_creation_expression* in modo dinamico è associato, il tipo di fase di compilazione è ancora `T`.

L'elaborazione in fase di esecuzione di un' *object_creation_expression* del form `new T(A)`, dove `T` viene *class_type* o un *struct_type* e`A` è una funzione facoltativa *argument_list*, costituita dai passaggi seguenti:

*   Se `T` è un *class_type*:
    * Una nuova istanza della classe `T` viene allocato. Se non è disponibile memoria sufficiente per allocare la nuova istanza, una `System.OutOfMemoryException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.
    * Tutti i campi della nuova istanza vengono inizializzati sui valori predefiniti ([i valori predefiniti](variables.md#default-values)).
    * Viene chiamato il costruttore di istanza in base alle regole di chiamata di funzione membro ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)). Un riferimento all'istanza appena allocata viene passato automaticamente al costruttore di istanza e l'istanza è accessibile all'interno di tale costruttore come `this`.
*   Se `T` è un *struct_type*:
    * Un'istanza del tipo `T` viene creato allocando una variabile locale temporanea. Poiché un costruttore di istanza di un *struct_type* è necessario assegnare in modo certo un valore per ogni campo dell'istanza viene creato, non è necessaria alcuna inizializzazione della variabile temporanea.
    * Viene chiamato il costruttore di istanza in base alle regole di chiamata di funzione membro ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)). Un riferimento all'istanza appena allocata viene passato automaticamente al costruttore di istanza e l'istanza è accessibile all'interno di tale costruttore come `this`.

#### <a name="object-initializers"></a>Inizializzatori di oggetto

Un' ***inizializzatore di oggetto*** specifica i valori per zero o più campi, proprietà o elementi indicizzati di un oggetto.

```antlr
object_initializer
    : '{' member_initializer_list? '}'
    | '{' member_initializer_list ',' '}'
    ;

member_initializer_list
    : member_initializer (',' member_initializer)*
    ;

member_initializer
    : initializer_target '=' initializer_value
    ;

initializer_target
    : identifier
    | '[' argument_list ']'
    ;

initializer_value
    : expression
    | object_or_collection_initializer
    ;
```

Un inizializzatore di oggetto è costituito da una sequenza di inizializzatori di membro, racchiusi `{` e `}` token e separati da virgole. Ciascuna *member_initializer* designa una destinazione per l'inizializzazione. Un *identificatore* deve denominare un campo accessibile o una proprietà dell'oggetto in fase di inizializzazione, mentre un *argument_list* racchiuso tra parentesi quadre deve specificare gli argomenti per un indicizzatore accessibile nel oggetto inizializzato. È un errore per un inizializzatore di oggetto includere più di un inizializzatore di membro per lo stesso campo o proprietà.

Ciascuna *initializer_target* è seguita da un segno di uguale e un'espressione, un inizializzatore di oggetto o un inizializzatore di raccolta. Non è possibile che le espressioni all'interno dell'inizializzatore di oggetto per fare riferimento all'oggetto appena creato che l'inizializzazione.

Un inizializzatore di membro che specifica un'espressione dopo il segno di uguale viene elaborato esattamente come un'assegnazione ([assegnamento semplice](expressions.md#simple-assignment)) alla destinazione.

Un inizializzatore di membro che consente di specificare un inizializzatore di oggetto dopo il segno di uguale è un ***inizializzatore di oggetto annidati***, vale a dire un'inizializzazione di un oggetto incorporato. Invece di assegnare un nuovo valore per il campo o proprietà, le assegnazioni nell'inizializzatore di oggetto annidati vengono trattate come le assegnazioni ai membri del campo o della proprietà. Gli inizializzatori di oggetto annidati non è possibile applicare alle proprietà con un tipo di valore o per i campi di sola lettura con un tipo di valore.

Un inizializzatore di membro che consente di specificare un inizializzatore di raccolta dopo il segno di uguale è un'inizializzazione di una raccolta incorporata. Invece di assegnare una nuova raccolta per il campo di destinazione, la proprietà o indicizzatore, gli elementi specificati nell'inizializzatore vengono aggiunti alla raccolta di cui viene fatto riferimento dalla destinazione. La destinazione deve essere di tipo raccolta che soddisfa i requisiti specificati nelle [inizializzatori di insieme](expressions.md#collection-initializers).

Gli argomenti da un inizializzatore di indice verranno sempre essere valutati esattamente una volta. Di conseguenza, anche se gli argomenti finire mai usato (ad esempio a causa di un inizializzatore nidificato vuoto), questi verranno valutati in relazione agli effetti collaterali.

La classe seguente rappresenta un punto con due coordinate:
```csharp
public class Point
{
    int x, y;

    public int X { get { return x; } set { x = value; } }
    public int Y { get { return y; } set { y = value; } }
}
```

Un'istanza di `Point` può essere creato e inizializzato come indicato di seguito:
```csharp
Point a = new Point { X = 0, Y = 1 };
```
che ha lo stesso effetto
```csharp
Point __a = new Point();
__a.X = 0;
__a.Y = 1; 
Point a = __a;
```
in cui `__a` è una variabile temporanea in caso contrario invisibile e non accessibile. La classe seguente rappresenta un rettangolo creato da due punti:
```csharp
public class Rectangle
{
    Point p1, p2;

    public Point P1 { get { return p1; } set { p1 = value; } }
    public Point P2 { get { return p2; } set { p2 = value; } }
}
```

Un'istanza di `Rectangle` può essere creato e inizializzato come indicato di seguito:
```csharp
Rectangle r = new Rectangle {
    P1 = new Point { X = 0, Y = 1 },
    P2 = new Point { X = 2, Y = 3 }
};
```
che ha lo stesso effetto
```csharp
Rectangle __r = new Rectangle();
Point __p1 = new Point();
__p1.X = 0;
__p1.Y = 1;
__r.P1 = __p1;
Point __p2 = new Point();
__p2.X = 2;
__p2.Y = 3;
__r.P2 = __p2; 
Rectangle r = __r;
```
in cui `__r`, `__p1` e `__p2` sono variabili temporanee che altrimenti risultano invisibile e non accessibili.

Se `Rectangle`del costruttore alloca due incorporati `Point` istanze
```csharp
public class Rectangle
{
    Point p1 = new Point();
    Point p2 = new Point();

    public Point P1 { get { return p1; } }
    public Point P2 { get { return p2; } }
}
```
il costrutto seguente può essere utilizzato per inizializzare l'oggetto incorporato `Point` istanze invece di assegnare le nuove istanze:
```csharp
Rectangle r = new Rectangle {
    P1 = { X = 0, Y = 1 },
    P2 = { X = 2, Y = 3 }
};
```
che ha lo stesso effetto
```csharp
Rectangle __r = new Rectangle();
__r.P1.X = 0;
__r.P1.Y = 1;
__r.P2.X = 2;
__r.P2.Y = 3;
Rectangle r = __r;
```

In base a una definizione appropriata di C, l'esempio seguente:
```csharp
var c = new C {
    x = true,
    y = { a = "Hello" },
    z = { 1, 2, 3 },
    ["x"] = 5,
    [0,0] = { "a", "b" },
    [1,2] = {}
};
```
è equivalente a questa serie di esercitazioni:
```csharp
C __c = new C();
__c.x = true;
__c.y.a = "Hello";
__c.z.Add(1); 
__c.z.Add(2);
__c.z.Add(3);
string __i1 = "x";
__c[__i1] = 5;
int __i2 = 0, __i3 = 0;
__c[__i2,__i3].Add("a");
__c[__i2,__i3].Add("b");
int __i4 = 1, __i5 = 2;
var c = __c;
```
in cui `__c`e così via sono variabili generate invisibile e non accessibili al codice sorgente. Si noti che gli argomenti per `[0,0]` vengono valutate una sola volta e gli argomenti per `[1,2]` vengono valutati una volta anche se non vengono mai usati.

#### <a name="collection-initializers"></a>Inizializzatori di raccolta

Un inizializzatore di raccolta specifica gli elementi di una raccolta.

```antlr
collection_initializer
    : '{' element_initializer_list '}'
    | '{' element_initializer_list ',' '}'
    ;

element_initializer_list
    : element_initializer (',' element_initializer)*
    ;

element_initializer
    : non_assignment_expression
    | '{' expression_list '}'
    ;

expression_list
    : expression (',' expression)*
    ;
```

Un inizializzatore di insieme è costituito da una sequenza di inizializzatori di elemento, racchiuso `{` e `}` token e separati da virgole. Ogni inizializzatore di elemento specifica un elemento da aggiungere all'oggetto raccolta in fase di inizializzazione ed è costituito da un elenco di espressioni racchiuse `{` e `}` token e separati da virgole.  Un inizializzatore di elemento di singola espressione può essere scritta senza parentesi graffe, ma non può quindi essere un'espressione di assegnazione, per evitare ambiguità con gli inizializzatori di membro. Il *non_assignment_expression* definita in produzione [espressione](expressions.md#expression).

Di seguito è riportato un esempio di un'espressione di creazione di oggetti che include un inizializzatore di raccolta:
```csharp
List<int> digits = new List<int> { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
```

L'oggetto raccolta in cui viene applicato un inizializzatore di raccolta deve essere di un tipo che implementa `System.Collections.IEnumerable` o si verifica un errore in fase di compilazione. Per ogni specificato elemento nell'ordine, l'inizializzatore di insieme richiama un `Add` metodo sull'oggetto destinazione dell'oggetto con l'elenco di espressioni dell'inizializzatore di elemento come elenco di argomenti, applicazione di ricerca di membri normali e risoluzione per ogni chiamata dell'overload. Di conseguenza, l'oggetto di raccolta deve avere un metodo di estensione o istanze applicabile con il nome `Add` per ogni inizializzatore di elemento.

La classe seguente rappresenta un contatto con un nome e un elenco di numeri di telefono:
```csharp
public class Contact
{
    string name;
    List<string> phoneNumbers = new List<string>();

    public string Name { get { return name; } set { name = value; } }

    public List<string> PhoneNumbers { get { return phoneNumbers; } }
}
```

Oggetto `List<Contact>` può essere creato e inizializzato come indicato di seguito:
```csharp
var contacts = new List<Contact> {
    new Contact {
        Name = "Chris Smith",
        PhoneNumbers = { "206-555-0101", "425-882-8080" }
    },
    new Contact {
        Name = "Bob Harris",
        PhoneNumbers = { "650-555-0199" }
    }
};
```
che ha lo stesso effetto
```csharp
var __clist = new List<Contact>();
Contact __c1 = new Contact();
__c1.Name = "Chris Smith";
__c1.PhoneNumbers.Add("206-555-0101");
__c1.PhoneNumbers.Add("425-882-8080");
__clist.Add(__c1);
Contact __c2 = new Contact();
__c2.Name = "Bob Harris";
__c2.PhoneNumbers.Add("650-555-0199");
__clist.Add(__c2);
var contacts = __clist;
```
in cui `__clist`, `__c1` e `__c2` sono variabili temporanee che altrimenti risultano invisibile e non accessibili.

#### <a name="array-creation-expressions"></a>Espressioni di creazione matrice

Un' *array_creation_expression* viene usato per creare una nuova istanza di un *array_type*.

```antlr
array_creation_expression
    : 'new' non_array_type '[' expression_list ']' rank_specifier* array_initializer?
    | 'new' array_type array_initializer
    | 'new' rank_specifier array_initializer
    ;
```

Un'espressione di creazione della matrice del primo form alloca un'istanza di matrice del tipo risultante da ognuna delle singole espressioni eliminazione dall'elenco di espressioni. Ad esempio, l'espressione di creazione matrice `new int[10,20]` produce un'istanza di matrice di tipo `int[,]`e l'espressione di creazione matrice `new int[10][,]` produce una matrice di tipo `int[][,]`. Ogni espressione nell'elenco dell'espressione deve essere di tipo `int`, `uint`, `long`, o `ulong`, o convertibile in modo implicito a uno o più di questi tipi. Il valore di ogni espressione determina la lunghezza della dimensione corrispondente nell'istanza di matrice appena allocato. Poiché la lunghezza di una dimensione di matrice deve essere non negativa, è un errore in fase di compilazione per avere una *constant_expression* con un valore negativo nell'elenco dell'espressione.

Tranne che in un contesto non sicuro ([contesti non sicuri](unsafe-code.md#unsafe-contexts)), il layout delle matrici è specificato.

Se un'espressione di creazione della matrice del primo form include un inizializzatore di matrice, ogni espressione nell'elenco dell'espressione deve essere una costante e le lunghezze di classificazione e dimensione specificate dall'elenco di espressioni devono corrispondere a quelli dell'inizializzatore di matrice.

In un'espressione di creazione della matrice nel formato secondo o terzo, la classificazione dell'identificatore di tipo o il numero di dimensioni di matrice specificato deve corrispondere a quello dell'inizializzatore di matrice. Le singole lunghezze vengono dedotti dal numero di elementi in ognuno dei corrispondenti livelli di nidificazione dell'inizializzatore di matrice. Di conseguenza, l'espressione
```csharp
new int[,] {{0, 1}, {2, 3}, {4, 5}}
```
corrisponde esattamente alla
```csharp
new int[3, 2] {{0, 1}, {2, 3}, {4, 5}}
```

Un'espressione di creazione della matrice nel formato terzo si intende un' ***tipizzate in modo implicito l'espressione di creazione matrici***. È simile al secondo formato, ad eccezione del fatto che il tipo di elemento della matrice non è assegnato in modo esplicito, ma considerato il tipo più comune ([trovare il tipo comune di un set di espressioni](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)) del set di espressioni nella matrice inizializzatore. Per una matrice multidimensionale, ad esempio, una posizione in cui il *rank_specifier* contiene almeno una virgola, questo set comprende tutti *expression*s trovato annidati *array_initializer*s.

Gli inizializzatori di matrice sono descritti dettagliatamente nella [gli inizializzatori di matrice](arrays.md#array-initializers).

Il risultato della valutazione di un'espressione di creazione della matrice viene classificato come un valore, vale a dire un riferimento all'istanza di matrice appena allocato. L'elaborazione in fase di esecuzione di un'espressione di creazione della matrice include i passaggi seguenti:

*  Le espressioni di lunghezza delle dimensioni di *expression_list* vengono valutati nell'ordine, da sinistra a destra. Dopo la valutazione di ogni espressione, una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) viene eseguita su uno dei seguenti tipi: `int`, `uint`, `long`, `ulong`. Viene scelto il primo tipo in questo elenco per il quale esiste una conversione implicita. Se la valutazione di un'espressione o la conversione implicita successive genera un'eccezione, non altre espressioni vengono valutate e viene eseguito alcun ulteriore passaggio.
*  I valori calcolati per le lunghezze delle dimensioni vengono convalidati come indicato di seguito. Se uno o più dei valori sono minori di zero, una `System.OverflowException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.
*  Un'istanza di matrice con le lunghezze delle dimensioni specificata viene allocata. Se non è disponibile memoria sufficiente per allocare la nuova istanza, una `System.OutOfMemoryException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.
*  Tutti gli elementi della nuova istanza di matrice vengono inizializzati sui valori predefiniti ([i valori predefiniti](variables.md#default-values)).
*  Se l'espressione di creazione matrice contiene un inizializzatore di matrice, ogni espressione dell'inizializzatore di matrice viene valutata e assegnata al relativo elemento corrispondente della matrice. Le valutazioni e le assegnazioni vengono eseguite nell'ordine vengono scritte nell'inizializzatore di matrice, in altre parole, gli elementi vengono inizializzati nell'indice ordine crescente, con la dimensione più a destra prima di tutto l'aumento. Se la valutazione di una determinata espressione o l'assegnazione successivi all'elemento della matrice corrispondente genera un'eccezione, quindi non viene inizializzato alcun altro elemento (e gli elementi rimanenti in questo modo verranno impostate sui valori predefiniti).

Un'espressione di creazione matrici consente la creazione di istanze di una matrice con elementi di un tipo di matrice, ma gli elementi di tale matrice devono essere inizializzati manualmente. Ad esempio, l'istruzione
```csharp
int[][] a = new int[100][];
```
Crea una matrice unidimensionale con 100 elementy typu `int[]`. Il valore iniziale di ogni elemento è `null`. Non è possibile che l'espressione di creazione matrice stessa anche creare un'istanza sottomatrici e l'istruzione
```csharp
int[][] a = new int[100][5];        // Error
```
Genera un errore in fase di compilazione. Creazione di un'istanza delle sottomatrici deve essere eseguita manualmente, come in
```csharp
int[][] a = new int[100][];
for (int i = 0; i < 100; i++) a[i] = new int[5];
```

Quando una matrice di matrici è caratterizzato da una forma "rettangolare", che risulta quando sottomatrici sono tutti della stessa lunghezza, è preferibile usare una matrice multidimensionale. Nell'esempio precedente, la creazione di istanze della matrice di matrici crea 101 oggetti, ovvero una matrice esterna e 100 sottomatrici. Al contrario,
```csharp
int[,] = new int[100, 5];
```
Crea un singolo oggetto, una matrice bidimensionale e l'allocazione in una singola istruzione.

Di seguito sono riportati esempi di espressioni di creazione della matrice tipizzata in modo implicito:
```csharp
var a = new[] { 1, 10, 100, 1000 };                       // int[]

var b = new[] { 1, 1.5, 2, 2.5 };                         // double[]

var c = new[,] { { "hello", null }, { "world", "!" } };   // string[,]

var d = new[] { 1, "one", 2, "two" };                     // Error
```

L'ultima espressione provoca un errore in fase di compilazione perché né `int` né `string` è implicitamente convertibile in altro e in tal caso non è più comune digitare. Un'espressione di creazione della matrice tipizzata in modo esplicito deve essere usata in questo caso, ad esempio si specifica il tipo è `object[]`. In alternativa, uno degli elementi può essere convertito in un tipo di base comune, che diventa quindi il tipo di elemento dedotto.

Espressioni di creazione matrice tipizzata in modo implicito possono essere combinate con gli inizializzatori di oggetto anonimi ([espressioni per la creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions)) per creare in modo anonimo strutture di dati tipizzato. Ad esempio:
```csharp
var contacts = new[] {
    new {
        Name = "Chris Smith",
        PhoneNumbers = new[] { "206-555-0101", "425-882-8080" }
    },
    new {
        Name = "Bob Harris",
        PhoneNumbers = new[] { "650-555-0199" }
    }
};
```

#### <a name="delegate-creation-expressions"></a>Espressioni per la creazione di delegato

Oggetto *delegate_creation_expression* viene usato per creare una nuova istanza di un *delegate_type*.

```antlr
delegate_creation_expression
    : 'new' delegate_type '(' expression ')'
    ;
```

L'argomento di un'espressione di creazione del delegato deve essere un gruppo di metodi, una funzione anonima o un valore del tipo time di compilazione `dynamic` o un *delegate_type*. Se l'argomento è un gruppo di metodi, identifica il metodo e, per un metodo di istanza, l'oggetto per cui si desidera creare un delegato. Se l'argomento è una funzione anonima direttamente definisce i parametri e il corpo del metodo di destinazione di delegato. Se l'argomento è un valore identifica un'istanza del delegato di cui si desidera creare una copia.

Se il *espressione* dispone del tipo in fase di compilazione `dynamic`, il *delegate_creation_expression* viene associata in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)) e le regole seguenti vengono applicati in fase di esecuzione utilizzando il tipo di fase di esecuzione del *espressione*. In caso contrario, le regole vengono applicate in fase di compilazione.

L'elaborazione in fase di associazione di un *delegate_creation_expression* del form `new D(E)`, dove `D` è un *delegate_type* e `E` è un *espressione* , costituita dai passaggi seguenti:

*  Se `E` è un gruppo di metodi, l'espressione di creazione del delegato viene elaborato esattamente come la conversione di un gruppo di metodi ([conversioni dei metodi di gruppo](conversions.md#method-group-conversions)) dal `E` a `D`.
*  Se `E` è una funzione anonima, l'espressione di creazione del delegato viene elaborato esattamente una conversione da funzione anonima ([conversioni delle funzioni anonime](conversions.md#anonymous-function-conversions)) dal `E` a `D`.
*  Se `E` è un valore `E` devono essere compatibili ([dichiarazioni Delegate](delegates.md#delegate-declarations)) con `D`, e il risultato è un riferimento a un delegato appena creato del tipo `D` che fa riferimento alla stessa chiamata elencato come `E`. Se `E` non è compatibile con `D`, si verifica un errore in fase di compilazione.

L'elaborazione in fase di esecuzione di un *delegate_creation_expression* del form `new D(E)`, dove `D` è un *delegate_type* e `E` è un *espressione* , costituita dai passaggi seguenti:

*   Se `E` è un gruppo di metodi, l'espressione di creazione del delegato viene valutata come una conversione del gruppo (metodo) ([conversioni dei metodi di gruppo](conversions.md#method-group-conversions)) dal `E` a `D`.
*   Se `E` è una funzione anonima, la creazione del delegato viene valutata come conversione da funzione anonima `E` al `D` ([conversioni delle funzioni anonime](conversions.md#anonymous-function-conversions)).
*   Se `E` è un valore di una *delegate_type*:
    * `E` viene valutato. Se questa versione di valutazione causa un'eccezione, non verrà eseguito alcun ulteriore passaggio.
    * Se il valore di `E` viene `null`, un `System.NullReferenceException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.
    * Una nuova istanza del tipo delegato `D` viene allocato. Se non è disponibile memoria sufficiente per allocare la nuova istanza, una `System.OutOfMemoryException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.
    * La nuova istanza di delegato viene inizializzata con lo stesso elenco chiamate dell'istanza del delegato rappresentato da `E`.

L'elenco chiamate di un delegato viene determinato quando viene creata un'istanza di delegato e quindi rimane costante per l'intera durata del delegato. Non in altre parole, è possibile modificare le entità richiamabile destinazione di un delegato dopo che è stato creato. Quando si combinano due delegati o uno viene rimosso da un altro ([dichiarazioni Delegate](delegates.md#delegate-declarations)), un nuovo delegato; nessun delegato esistente ha il suo contenuto modificato.

Non è possibile creare un delegato che fa riferimento a una proprietà, indicizzatore, l'operatore definito dall'utente, costruttore di istanza, distruttore o costruttore statico.

Come descritto in precedenza, quando viene creato un delegato da un gruppo di metodi, l'elenco di parametri formali e tipo restituito del delegato di determinare quale dei metodi di overload per selezionare. Nell'esempio
```csharp
delegate double DoubleFunc(double x);

class A
{
    DoubleFunc f = new DoubleFunc(Square);

    static float Square(float x) {
        return x * x;
    }

    static double Square(double x) {
        return x * x;
    }
}
```
il `A.f` campo viene inizializzato con un delegato che fa riferimento al secondo `Square` metodo perché tale metodo corrisponde esattamente all'elenco di parametri formali e tipo restituito di `DoubleFunc`. Era il secondo `Square` (metodo) non è stato presenta, potrebbe essersi verificato un errore in fase di compilazione.

#### <a name="anonymous-object-creation-expressions"></a>Espressioni per la creazione di oggetti anonimi

Un' *anonymous_object_creation_expression* viene usato per creare un oggetto di un tipo anonimo.

```antlr
anonymous_object_creation_expression
    : 'new' anonymous_object_initializer
    ;

anonymous_object_initializer
    : '{' member_declarator_list? '}'
    | '{' member_declarator_list ',' '}'
    ;

member_declarator_list
    : member_declarator (',' member_declarator)*
    ;

member_declarator
    : simple_name
    | member_access
    | base_access
    | null_conditional_member_access
    | identifier '=' expression
    ;
```

Un inizializzatore di oggetto anonimi dichiara un tipo anonimo e restituisce un'istanza di quel tipo. Un tipo anonimo è un tipo di classe anonimi che eredita direttamente da `object`. I membri di un tipo anonimo sono una sequenza di sola lettura proprietà dedotte dall'inizializzatore di oggetto anonimo usato per creare un'istanza del tipo. In particolare, un inizializzatore di oggetto anonimo del form
```csharp
new { p1 = e1, p2 = e2, ..., pn = en }
```
dichiara un tipo anonimo del form
```csharp
class __Anonymous1
{
    private readonly T1 f1;
    private readonly T2 f2;
    ...
    private readonly Tn fn;

    public __Anonymous1(T1 a1, T2 a2, ..., Tn an) {
        f1 = a1;
        f2 = a2;
        ...
        fn = an;
    }

    public T1 p1 { get { return f1; } }
    public T2 p2 { get { return f2; } }
    ...
    public Tn pn { get { return fn; } }

    public override bool Equals(object __o) { ... }
    public override int GetHashCode() { ... }
}
```
in cui ciascuna `Tx` è il tipo dell'espressione corrispondente `ex`. L'espressione utilizzata in una *member_declarator* deve presentare un tipo. Di conseguenza, è un errore in fase di compilazione per un'espressione in una *member_declarator* deve essere null o una funzione anonima. È anche un errore in fase di compilazione per l'espressione presentare un tipo unsafe.

I nomi di un tipo anonimo e del parametro da relativo `Equals` metodo vengono generati automaticamente dal compilatore e non è possibile farvi riferimento nel testo di programma.

All'interno del programma stesso, due inizializzatori di oggetto anonimi che specificano una sequenza di proprietà degli stessi nomi e tipi in fase di compilazione nello stesso ordine produrranno istanze dello stesso tipo anonimo.

Nell'esempio
```csharp
var p1 = new { Name = "Lawnmower", Price = 495.00 };
var p2 = new { Name = "Shovel", Price = 26.95 };
p1 = p2;
```
l'assegnazione nell'ultima riga è consentito perché `p1` e `p2` sono dello stesso tipo anonimo.

Il `Equals` e `GetHashcode` metodi nei tipi anonimi di eseguire l'override dei metodi ereditati dalla `object`e vengono definiti in termini del `Equals` e `GetHashcode` delle proprietà, in modo che due istanze dello stesso tipo anonimo sono uguali se e solo se tutte le relative proprietà sono uguali.

Un dichiaratore di membro può essere abbreviato utilizzando un nome semplice ([inferenza](expressions.md#type-inference)), un accesso ai membri ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), un accesso di base ([accessdiBase](expressions.md#base-access)) o un accesso ai membri condizionali null ([espressioni condizionali Null come gli inizializzatori di proiezione](expressions.md#null-conditional-expressions-as-projection-initializers)). Questa operazione viene definita un' ***inizializzatori di proiezione*** ed è l'abbreviazione per una dichiarazione di e l'assegnazione a una proprietà con lo stesso nome. In particolare, i dichiaratori di membri dei moduli
```csharp
identifier
expr.identifier
```
equivalgono esattamente alle seguenti, rispettivamente:
```csharp
identifier = identifier
identifier = expr.identifier
```

Di conseguenza, in un inizializzatore di proiezione le *identificatore* consente di selezionare il valore e il campo o proprietà a cui è assegnato il valore. Un inizializzatore di proiezione progetti in modo intuitivo, non solo un valore, ma anche il nome del valore.

### <a name="the-typeof-operator"></a>L'operatore typeof

Il `typeof` operatore viene usato per ottenere il `System.Type` oggetto per un tipo.

```antlr
typeof_expression
    : 'typeof' '(' type ')'
    | 'typeof' '(' unbound_type_name ')'
    | 'typeof' '(' 'void' ')'
    ;

unbound_type_name
    : identifier generic_dimension_specifier?
    | identifier '::' identifier generic_dimension_specifier?
    | unbound_type_name '.' identifier generic_dimension_specifier?
    ;

generic_dimension_specifier
    : '<' comma* '>'
    ;

comma
    : ','
    ;
```

La prima forma del *typeof_expression* costituito da un `typeof` parola chiave seguita da una parentesi *tipo*. Il risultato di un'espressione di questo modulo è il `System.Type` oggetto per il tipo indicato. È presente un solo `System.Type` oggetto per un determinato tipo. Ciò significa che per un tipo `T`, `typeof(T) == typeof(T)` è sempre true. Il *tipo* non può essere `dynamic`.

La seconda forma di *typeof_expression* costituito da un `typeof` parola chiave seguita da una parentesi *unbound_type_name*. Un' *unbound_type_name* è molto simile a un *type_name* ([Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)) ad eccezione del fatto che un *unbound_type_name* contiene *generic_dimension_specifier*s in cui un *type_name* contiene *type_argument_list*s. Quando l'operando di un *typeof_expression* è una sequenza di token che soddisfi le grammatiche di entrambi *unbound_type_name* e *type_name*, vale a dire quando questo contiene né una *generic_dimension_specifier* né *type_argument_list*, la sequenza di token è considerata un *type_name*. Il significato di un *unbound_type_name* viene determinata come segue:

*  Converte la sequenza di token a un *type_name* sostituendo ogni *generic_dimension_specifier* con un *type_argument_list* aventi lo stesso numero di virgole e il parola chiave `object` come ognuno *type_argument*.
*  Valutare l'oggetto risultante *type_name*, ignorando tutti i vincoli del parametro di tipo.
*  Il *unbound_type_name* risolta nel tipo generico non associato associato con il tipo costruito risultante ([associato e non associato tipi](types.md#bound-and-unbound-types)).

Il risultato del *typeof_expression* è il `System.Type` per risultante non associato di tipo generico dell'oggetto.

Terzo costituiti *typeof_expression* è costituito da un `typeof` parola chiave seguita da una parentesi `void` (parola chiave). Il risultato di un'espressione di questo modulo è il `System.Type` oggetto che rappresenta l'assenza di un tipo. L'oggetto di tipo restituito da `typeof(void)` è diverso dall'oggetto di tipo restituito per qualsiasi tipo. Questo oggetto di tipo speciale è utile nelle librerie di classi che consentono la reflection su metodi nel linguaggio in cui si desideri sono un modo per rappresentare il tipo restituito di qualsiasi metodo, inclusi i metodi di void, con un'istanza di tali metodi `System.Type`.

Il `typeof` operatore può essere usato in un parametro di tipo. Il risultato è il `System.Type` oggetto per il tipo di runtime che è stato associato al parametro di tipo. Il `typeof` operatore può essere utilizzato anche in un tipo costruito o un tipo generico non associato ([associate e non associato tipi](types.md#bound-and-unbound-types)). Il `System.Type` dell'oggetto per un tipo generico non associato non è lo stesso come il `System.Type` oggetto del tipo di istanza. Il tipo di istanza è sempre un tipo costruito chiuso in fase di esecuzione in modo relativo `System.Type` oggetto dipende dagli argomenti di tipo di runtime in uso, mentre il tipo generico non ha alcun argomento tipo.

L'esempio
```csharp
using System;

class X<T>
{
    public static void PrintTypes() {
        Type[] t = {
            typeof(int),
            typeof(System.Int32),
            typeof(string),
            typeof(double[]),
            typeof(void),
            typeof(T),
            typeof(X<T>),
            typeof(X<X<T>>),
            typeof(X<>)
        };
        for (int i = 0; i < t.Length; i++) {
            Console.WriteLine(t[i]);
        }
    }
}

class Test
{
    static void Main() {
        X<int>.PrintTypes();
    }
}
```
produce l'output seguente:
```
System.Int32
System.Int32
System.String
System.Double[]
System.Void
System.Int32
X`1[System.Int32]
X`1[X`1[System.Int32]]
X`1[T]
```

Si noti che `int` e `System.Int32` sono dello stesso tipo.

Si noti anche che il risultato del `typeof(X<>)` non dipende dall'argomento tipo, ma il risultato di `typeof(X<T>)` viene.

### <a name="the-checked-and-unchecked-operators"></a>Gli operatori checked e unchecked

Il `checked` e `unchecked` gli operatori vengono utilizzati per controllare le ***contesto di controllo dell'overflow*** per le conversioni e operazioni aritmetiche di tipo integrale.

```antlr
checked_expression
    : 'checked' '(' expression ')'
    ;

unchecked_expression
    : 'unchecked' '(' expression ')'
    ;
```

Il `checked` operatore valuta l'espressione contenuta in un contesto controllato e il `unchecked` operatore valuta l'espressione contenuta in un contesto non verificato. Oggetto *checked_expression* oppure *unchecked_expression* corrispondono esattamente a una *parenthesized_expression* ([espressioni tra parentesi](expressions.md#parenthesized-expressions)), ad eccezione del fatto che viene valutata l'espressione contenuta nel contesto di controllo dell'overflow specificata.

Contesto di controllo dell'overflow può anche essere controllato tramite il `checked` e `unchecked` istruzioni ([le istruzioni checked e unchecked](statements.md#the-checked-and-unchecked-statements)).

Le operazioni seguenti sono interessate dal contesto definito dal controllo dell'overflow di `checked` e `unchecked` operatori e le istruzioni:

*  Oggetto predefinito `++` e `--` operatori unari ([forma suffissa di incremento e decremento (operatori)](expressions.md#postfix-increment-and-decrement-operators) e [incremento prefisso e decremento (operatori)](expressions.md#prefix-increment-and-decrement-operators)), quando l'operando è di un valore integrale tipo.
*  Oggetto predefinito `-` operatore unario ([operatore meno unario](expressions.md#unary-minus-operator)), quando l'operando è di tipo integrale.
*  Oggetto predefinito `+`, `-`, `*`, e `/` operatori binari ([operatori aritmetici](expressions.md#arithmetic-operators)), quando entrambi gli operandi sono di tipi integrali.
*  Conversioni numeriche esplicite ([conversioni numeriche esplicite](conversions.md#explicit-numeric-conversions)) da un tipo integrale a un altro tipo integrale o da `float` o `double` a un tipo integrale.

Quando una delle operazioni precedenti produrre un risultato che è troppo grande per essere rappresentato nel tipo di destinazione, il contesto in cui l'operazione viene eseguiti controlli il comportamento risultante:

*  In un `checked` contesto, se l'operazione è un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)), si verifica un errore in fase di compilazione. In caso contrario, quando l'operazione viene eseguita in fase di esecuzione, un `System.OverflowException` viene generata un'eccezione.
*  In un `unchecked` contesto, il risultato viene troncato eliminando qualsiasi bit più significativi che non rientrano nel tipo di destinazione.

Per le espressioni non costanti (espressioni che vengono valutate in fase di esecuzione) che non sono racchiusi da qualsiasi `checked` oppure `unchecked` operatori o istruzioni, il contesto di controllo dell'overflow di predefinita è `unchecked` a meno che non fattori esterni (ad esempio compilatore opzioni e la configurazione dell'ambiente di esecuzione) chiama per `checked` valutazione.

Per le espressioni costanti (espressioni che possono essere valutate interamente in fase di compilazione), il contesto di controllo dell'overflow di predefinita è sempre `checked`. A meno che un'espressione costante viene inserita in modo esplicito un `unchecked` contesto, overflow che si verificano durante la valutazione in fase di compilazione dell'espressione sempre causano errori in fase di compilazione.

Il corpo di una funzione anonima non è influenzato `checked` o `unchecked` contesti in cui si verifica la funzione anonima.

Nell'esempio
```csharp
class Test
{
    static readonly int x = 1000000;
    static readonly int y = 1000000;

    static int F() {
        return checked(x * y);      // Throws OverflowException
    }

    static int G() {
        return unchecked(x * y);    // Returns -727379968
    }

    static int H() {
        return x * y;               // Depends on default
    }
}
```
non in fase di compilazione vengono segnalati errori poiché nessuna delle espressioni può essere valutata in fase di compilazione. In fase di esecuzione, il `F` metodo genera un `System.OverflowException`e il `G` metodo restituisce-727379968 (il più basso 32 bit del risultato out-of-range). Il comportamento dei `H` metodo dipende dal contesto per la compilazione di controllo dell'overflow di impostazione predefinita, ma è lo stesso `F` o uguale a quella `G`.

Nell'esempio
```csharp
class Test
{
    const int x = 1000000;
    const int y = 1000000;

    static int F() {
        return checked(x * y);      // Compile error, overflow
    }

    static int G() {
        return unchecked(x * y);    // Returns -727379968
    }

    static int H() {
        return x * y;               // Compile error, overflow
    }
}
```
l'overflow che si verificano quando si valutano le espressioni costanti nelle `F` e `H` causano errori in fase di compilazione da segnalare perché le espressioni vengono valutate un `checked` contesto. Un overflow si verifica anche quando si valuta l'espressione costante nel `G`, ma poiché la versione di valutazione viene eseguita un `unchecked` contesto, non viene segnalata l'overflow.

Il `checked` e `unchecked` operatori influiscono solo sul contesto per tali operazioni testualmente contenuti all'interno di controllo dell'overflow di "`(`"e"`)`" token. Gli operatori non hanno effetto sui membri di funzione che vengono richiamati come risultato di valutazione dell'espressione indipendente. Nell'esempio
```csharp
class Test
{
    static int Multiply(int x, int y) {
        return x * y;
    }

    static int F() {
        return checked(Multiply(1000000, 1000000));
    }
}
```
l'uso di `checked` nel `F` non influiscono sulla valutazione dei `x * y` in `Multiply`, quindi `x * y` viene valutato nel contesto di controllo dell'overflow predefinita.

Il `unchecked` operatore è utile quando si scrive costanti dei tipi integrali con segno in notazione esadecimale. Ad esempio:
```csharp
class Test
{
    public const int AllBits = unchecked((int)0xFFFFFFFF);

    public const int HighBit = unchecked((int)0x80000000);
}
```

Entrambe le costanti esadecimali riportato sopra sono di tipo `uint`. Poiché le costanti non sono comprese la `int` compresi, senza il `unchecked` operatore, i cast a `int` generano errori in fase di compilazione.

Il `checked` e `unchecked` operatori e le istruzioni consentono ai programmatori di controllare alcuni aspetti di alcuni calcoli numerici. Tuttavia, il comportamento di alcuni operatori numerici dipende da tipi di dati dei relativi operandi. Ad esempio, moltiplicazione di due numeri decimali genera sempre un'eccezione in caso di overflow anche interno un esplicitamente `unchecked` costruire. Analogamente, la moltiplicazione di due valori float non mai genera un'eccezione in caso di overflow anche interno un esplicitamente `checked` costruire. Inoltre, altri operatori mai sono interessate dalla modalità di controllo, predefinita o esplicita.

### <a name="default-value-expressions"></a>Espressioni con valore predefinito

Un'espressione con valore predefinito viene utilizzata per ottenere il valore predefinito ([i valori predefiniti](variables.md#default-values)) di un tipo. In genere un'espressione con valore predefinito viene utilizzata per i parametri di tipo, poiché potrebbe non essere noto se il parametro di tipo è un tipo di valore o un tipo riferimento. (Nessuna conversione esistente dal `null` letterale per un parametro di tipo, a meno che il parametro di tipo è noto come tipo di riferimento.)

```antlr
default_value_expression
    : 'default' '(' type ')'
    ;
```

Se il *tipo* in un *default_value_expression* viene valutata in fase di esecuzione a un tipo riferimento, il risultato è `null` convertiti in tale tipo. Se il *tipo* in un *default_value_expression* valuta in fase di esecuzione a un tipo valore, il risultato è la *value_type*del valore predefinito ([predefinito i costruttori](types.md#default-constructors)).

Oggetto *default_value_expression* è un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)) se il tipo è un tipo riferimento o un parametro di tipo noto per essere un tipo riferimento ([parametro di tipo i vincoli](classes.md#type-parameter-constraints)). Inoltre, un *default_value_expression* è un'espressione costante se il tipo è uno dei seguenti tipi di valore: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, o qualsiasi tipo di enumerazione.


### <a name="nameof-expressions"></a>Espressioni Nameof

Oggetto *nameof_expression* viene usato per ottenere il nome di un'entità program sotto forma di stringa costante.

```antlr
nameof_expression
    : 'nameof' '(' named_entity ')'
    ;

named_entity
    : simple_name
    | named_entity_target '.' identifier type_argument_list?
    ;

named_entity_target
    : 'this'
    | 'base'
    | named_entity 
    | predefined_type 
    | qualified_alias_member
    ;
```

Grammatica a proposito, il *named_entity* operando è sempre un'espressione. In quanto `nameof` non è una parola chiave riservata, un'espressione di nameof è sempre sintatticamente ambigua con una chiamata di nome semplice `nameof`. Per motivi di compatibilità, se una ricerca del nome ([nomi semplici](expressions.md#simple-names)) del nome della `nameof` ha esito positivo, l'espressione viene considerata come un' *invocation_expression* : indipendentemente dal fatto che la chiamata è legali. In caso contrario, è un *nameof_expression*.

Il significato del *named_entity* di un *nameof_expression* è il significato di esso come un'espressione; vale a dire, sia come un *simple_name*, un *base_access*  o un *member_access*. Tuttavia, in cui la ricerca è descritto nella [nomi semplici](expressions.md#simple-names) e [l'accesso ai membri](expressions.md#member-access) genera un errore perché è stato trovato un membro di istanza in un contesto statico, un *nameof_expression*non produce alcun errore di questo tipo.

Tratta di un errore in fase di compilazione per un *named_entity* designazione di un gruppo di metodi per avere una *type_argument_list*. Si tratta di un errore in fase di compilazione per un *named_entity_target* con il tipo `dynamic`.

Oggetto *nameof_expression* è un'espressione costante di tipo `string`, e non ha alcun effetto in fase di esecuzione. In particolare, relativi *named_entity* non viene valutata e viene ignorato per gli scopi di analisi di assegnazione certa ([regole generali per le espressioni semplici](variables.md#general-rules-for-simple-expressions)). Il valore è l'ultimo identificatore del *named_entity* prima dell'istruzione facoltativa *type_argument_list*trasformato nel modo seguente:

* Il prefisso "`@`", se usata, è stato rimosso.
* Ciascuna *unicode_escape_sequence* viene trasformato nel carattere Unicode corrispondente.
* Eventuali *formatting_characters* vengono rimossi.

Queste sono le stesse trasformazioni applicate [identificatori](lexical-structure.md#identifiers) durante il test di uguaglianza tra gli identificatori.

TODO: esempi

### <a name="anonymous-method-expressions"></a>Espressioni di metodo anonimo

Un' *anonymous_method_expression* è uno dei due modi per definire una funzione anonima. Queste sono descritte dettagliatamente nella [espressioni di funzioni anonime](expressions.md#anonymous-function-expressions).

## <a name="unary-operators"></a>Operatori unari

Il `?`, `+`, `-`, `!`, `~`, `++`, `--`, eseguire il cast, e `await` operatori vengono chiamati gli operatori unari.

```antlr
unary_expression
    : primary_expression
    | null_conditional_expression
    | '+' unary_expression
    | '-' unary_expression
    | '!' unary_expression
    | '~' unary_expression
    | pre_increment_expression
    | pre_decrement_expression
    | cast_expression
    | await_expression
    | unary_expression_unsafe
    ;
```

Se l'operando di un *unary_expression* dispone del tipo in fase di compilazione `dynamic`, in modo dinamico è associato ([binding dinamico](expressions.md#dynamic-binding)). In questo caso di tipo in fase di compilazione del *unary_expression* è `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di fase di esecuzione dell'operando.

### <a name="null-conditional-operator"></a>Operatore condizionale null

L'operatore condizionale null si applica un elenco di operazioni al relativo operando solo se l'operando è diverso da null. In caso contrario, il risultato dell'applicazione dell'operatore è `null`.

```antlr
null_conditional_expression
    : primary_expression null_conditional_operations
    ;

null_conditional_operations
    : null_conditional_operations? '?' '.' identifier type_argument_list?
    | null_conditional_operations? '?' '[' argument_list ']'
    | null_conditional_operations '.' identifier type_argument_list?
    | null_conditional_operations '[' argument_list ']'
    | null_conditional_operations '(' argument_list? ')'
    ;
```

L'elenco delle operazioni può includere l'accesso ai membri e le operazioni di accesso di elemento (che potrebbero essere essi stessi condizionali null), nonché la chiamata.

Ad esempio, l'espressione `a.b?[0]?.c()` è un *null_conditional_expression* con un *primary_expression* `a.b` e *null_conditional_operations* `?[0]` (accesso all'elemento condizionali null), `?.c` (accesso ai membri condizionali null) e `()` (chiamata).

Per un *null_conditional_expression* `E` con un *primary_expression* `P`, consentono `E0` l'espressione ottenuto rimuovendo testualmente iniziale `?`da ognuna delle *null_conditional_operations* di `E` che dispongono di uno. Concettualmente `E0` è l'espressione che verrà valutato se nessuno dei controlli null rappresentato dal `?`s si trova un `null`.

Inoltre, consentire `E1` essere l'espressione ottenuto rimuovendo testualmente iniziale `?` da solo la prima del *null_conditional_operations* in `E`. Ciò potrebbe causare una *primary-expression* (se si è verificato solo uno `?`) o a un altro *null_conditional_expression*.

Ad esempio, se `E` è l'espressione `a.b?[0]?.c()`, quindi `E0` è l'espressione `a.b[0].c()` e `E1` espressione `a.b[0]?.c()`.

Se `E0` viene classificato come nothing, quindi `E` viene classificato come nulla. In caso contrario, E è classificato come un valore.

`E0` e `E1` vengono usate per determinare il significato di `E`:

*  Se `E` avviene quando un *statement_expression* il significato di `E` è quello utilizzato per l'istruzione

   ```csharp
   if ((object)P != null) E1;
   ```

   ad eccezione del fatto che P viene valutato una sola volta.

*  In caso contrario, se `E0` viene classificato come non si verifica un errore in fase di compilazione.

*  In caso contrario, lasciare `T0` essere il tipo di `E0`.

   *  Se `T0` è un parametro di tipo che non è noto per essere un tipo riferimento o un tipo di valore non nullable, si verifica un errore in fase di compilazione.

   *  Se `T0` è un tipo di valore non nullable, è necessario il tipo di `E` viene `T0?`e il significato di `E` equivale a

      ```csharp
      ((object)P == null) ? (T0?)null : E1
      ```

      ad eccezione del fatto che `P` viene valutata una sola volta.

   *  In caso contrario, il tipo dell'espressione E viene T0, e il significato di E è identico

      ```csharp
      ((object)P == null) ? null : E1
      ```

      ad eccezione del fatto che `P` viene valutata una sola volta.

Se `E1` è a sua volta un *null_conditional_expression*, quindi cui queste regole vengono applicate anche in questo caso, la nidificazione dei test per `null` fino a quando non sono presenti ulteriori `?`del, e l'espressione è stata ridotta verso il basso per l'espressione primaria `E0`.

Ad esempio, se l'espressione `a.b?[0]?.c()` viene eseguita come un'espressione di istruzione, come l'istruzione:
```csharp
a.b?[0]?.c();
```
è equivalente al relativo significato:
```csharp
if (a.b != null) a.b[0]?.c();
```
anch ' esso equivalente a:
```csharp
if (a.b != null) if (a.b[0] != null) a.b[0].c();
```
Con la differenza che `a.b` e `a.b[0]` vengono valutate una sola volta.

Se si verifica in un contesto in cui viene utilizzato il valore, come in:
```csharp
var x = a.b?[0]?.c();
```
e supponendo che il tipo della chiamata finale non è un tipo di valore non nullable, è equivalente al relativo significato:
```csharp
var x = (a.b == null) ? null : (a.b[0] == null) ? null : a.b[0].c();
```
Con la differenza che `a.b` e `a.b[0]` vengono valutate una sola volta.

#### <a name="null-conditional-expressions-as-projection-initializers"></a>Espressioni condizionali null come gli inizializzatori di proiezione

Un'espressione condizionale null è consentita solo come un *member_declarator* in un *anonymous_object_creation_expression* ([espressioni per la creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions)) se termina con un accesso ai membri (facoltativamente condizionali null). Grammaticalmente, questo requisito può essere espresso come:

```antlr
null_conditional_member_access
    : primary_expression null_conditional_operations? '?' '.' identifier type_argument_list?
    | primary_expression null_conditional_operations '.' identifier type_argument_list?
    ;
```

Questo è un caso speciale della grammatica per *null_conditional_expression* sopra. L'ambiente di produzione per *member_declarator* nelle [espressioni per la creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions) quindi, include solo *null_conditional_member_access*.

#### <a name="null-conditional-expressions-as-statement-expressions"></a>Espressioni condizionali null come espressioni di istruzioni

Un'espressione condizionale null è consentita solo come un *statement_expression* ([le istruzioni di espressione](statements.md#expression-statements)) se termina con una chiamata. Grammaticalmente, questo requisito può essere espresso come:

```antlr
null_conditional_invocation_expression
    : primary_expression null_conditional_operations '(' argument_list? ')'
    ;
```

Questo è un caso speciale della grammatica per *null_conditional_expression* sopra. L'ambiente di produzione per *statement_expression* nelle [le istruzioni di espressione](statements.md#expression-statements) quindi, include solo *null_conditional_invocation_expression*.


### <a name="unary-plus-operator"></a>Operatore più unario

Per un'operazione del form `+x`, la risoluzione dell'overload di operatore unario ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico. L'operando viene convertito nel tipo di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore. Operatori più unario predefinito sono:

```csharp
int operator +(int x);
uint operator +(uint x);
long operator +(long x);
ulong operator +(ulong x);
float operator +(float x);
double operator +(double x);
decimal operator +(decimal x);
```

Per ognuno di questi operatori, il risultato è semplicemente il valore dell'operando.

### <a name="unary-minus-operator"></a>Operatore meno unario

Per un'operazione del form `-x`, la risoluzione dell'overload di operatore unario ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico. L'operando viene convertito nel tipo di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore. Gli operatori di negazione predefinite sono:

*  Negazione di integer:

   ```csharp
   int operator -(int x);
   long operator -(long x);
   ```

   Il risultato viene calcolato sottraendo `x` da zero. Se il valore di `x` è il più piccolo valore rappresentabile del tipo di operando (-2 ^ 31 per `int` o -2 ^ 63 per `long`), quindi la negazione di matematica `x` non è possibile rappresentare all'interno del tipo di operando. Se questo errore si verifica all'interno di un `checked` contesto, un `System.OverflowException` generata; se si verifica all'interno di un `unchecked` contesto, il risultato è il valore dell'operando e non viene segnalata l'overflow.

   Se l'operando dell'operatore di negazione è di tipo `uint`, viene convertito nel tipo `long`, e il tipo del risultato è `long`. Un'eccezione è la regola che consenta il `int` valore -2147483648 (-2 ^ 31) deve essere scritto come un valore letterale integer decimale ([i valori letterali Integer](lexical-structure.md#integer-literals)).

   Se l'operando dell'operatore di negazione è di tipo `ulong`, si verifica un errore in fase di compilazione. Un'eccezione è la regola che consenta il `long` valore -9223372036854775808 (-2 ^ 63) deve essere scritto come un valore letterale integer decimale ([i valori letterali Integer](lexical-structure.md#integer-literals)).

*  Negazione a virgola mobile:

   ```csharp
   float operator -(float x);
   double operator -(double x);
   ```

   Il risultato è il valore di `x` con segno opposto. Se `x` è NaN, il risultato è NaN.

*  Negazione decimale:

   ```csharp
   decimal operator -(decimal x);
   ```

   Il risultato viene calcolato sottraendo `x` da zero. Negazione decimale equivale a usare l'operatore di tipo unario `System.Decimal`.

### <a name="logical-negation-operator"></a>Operatore di negazione logica

Per un'operazione del form `!x`, la risoluzione dell'overload di operatore unario ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico. L'operando viene convertito nel tipo di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore. Esiste solo un operatore di negazione logica predefiniti:
```csharp
bool operator !(bool x);
```

Questo operatore calcola la negazione logica dell'operando: Se l'operando è `true`, il risultato è `false`. Se l'operando è `false`, il risultato è `true`.

### <a name="bitwise-complement-operator"></a>Operatore di complemento bit per bit

Per un'operazione del form `~x`, la risoluzione dell'overload di operatore unario ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico. L'operando viene convertito nel tipo di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore. Gli operatori di complemento bit per bit predefiniti sono:
```csharp
int operator ~(int x);
uint operator ~(uint x);
long operator ~(long x);
ulong operator ~(ulong x);
```

Per ognuno di questi operatori, il risultato dell'operazione è il complemento bit per bit di `x`.

Ogni tipo di enumerazione `E` in modo implicito fornisce l'operatore di complemento bit per bit seguenti:

```csharp
E operator ~(E x);
```

Il risultato della valutazione `~x`, dove `x` è un'espressione di un tipo di enumerazione `E` con un tipo sottostante `U`, è esattamente quello utilizzato per la valutazione `(E)(~(U)x)`, ad eccezione del fatto che la conversione a `E` è eseguita sempre come se si trova in un' `unchecked` contesto ([gli operatori checked e unchecked](expressions.md#the-checked-and-unchecked-operators)).

### <a name="prefix-increment-and-decrement-operators"></a>Incremento prefisso e decremento (operatori)

```antlr
pre_increment_expression
    : '++' unary_expression
    ;

pre_decrement_expression
    : '--' unary_expression
    ;
```

L'operando di un incremento prefisso o il decremento operazione deve essere un'espressione classificata come una variabile, un accesso a proprietà o un accesso all'indicizzatore. Il risultato dell'operazione è un valore dello stesso tipo dell'operando.

Se l'operando di un prefisso di incrementa o operazione di decremento è una proprietà o l'accesso dell'indicizzatore, la proprietà o l'indicizzatore deve avere entrambe un `get` e un `set` della funzione di accesso. Se ciò non avviene, si verifica un errore in fase di associazione.

Risoluzione dell'overload di operatore unario ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico. Predefined `++` e `--` operatori esistono per i tipi seguenti: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char` , `float`, `double`, `decimal`e qualsiasi tipo di enumerazione. Oggetto predefinito `++` gli operatori restituiscono il valore ottenuto aggiungendo 1 all'operando e oggetto predefinito `--` operatori restituiscono il valore ottenuto sottraendo 1 operando. In un `checked` contesto, se il risultato dell'addizione o sottrazione è compreso nell'intervallo del tipo di risultato e il tipo di risultato è un tipo integrale o enumerazione, un `System.OverflowException` viene generata un'eccezione.

L'elaborazione in fase di esecuzione di un incremento prefisso o del modulo operazione di decremento `++x` o `--x` prevede i passaggi seguenti:

*   Se `x` viene classificato come una variabile:
    * `x` viene valutata per produrre la variabile.
    * L'operatore selezionato viene richiamato con il valore di `x` come argomento.
    * Il valore restituito dall'operatore viene archiviato nel percorso specificato dalla valutazione di `x`.
    * Il valore restituito dall'operatore diventa il risultato dell'operazione.
*   Se `x` viene classificato come accesso alla proprietà o l'indicizzatore:
    * L'espressione dell'istanza (se `x` non è `static`) e l'elenco di argomenti (se `x` un accesso dell'indicizzatore) associato `x` vengono valutate, e i risultati vengono usati nella successiva `get` e `set` chiamate di funzione di accesso.
    * Il `get` funzione di accesso di `x` viene richiamato.
    * L'operatore selezionato viene richiamato con il valore restituito dal `get` come argomento della funzione di accesso.
    * Il `set` funzione di accesso di `x` viene richiamato con il valore restituito dall'operatore come relativo `value` argomento.
    * Il valore restituito dall'operatore diventa il risultato dell'operazione.

Il `++` e `--` operatori supportano anche in forma suffissa notazione ([forma suffissa di incremento e decremento (operatori)](expressions.md#postfix-increment-and-decrement-operators)). In genere, il risultato del `x++` o `x--` è il valore di `x` prima dell'operazione, mentre il risultato del `++x` o `--x` è il valore di `x` al termine dell'operazione. In entrambi i casi `x` stesso ha lo stesso valore al termine dell'operazione.

Un' `operator++` o `operator--` implementazione può essere richiamata usando la notazione prefissa o suffissa. Non è possibile disporre di implementazioni di operatori separati per le due notazioni.

### <a name="cast-expressions"></a>Espressioni cast

Oggetto *cast_expression* viene usata per convertire in modo esplicito un'espressione in un tipo specificato.

```antlr
cast_expression
    : '(' type ')' unary_expression
    ;
```

Oggetto *cast_expression* del form `(T)E`, dove `T` è un *tipo* e `E` è una *unary_expression*, consente di eseguire un'esplicita conversione ([conversioni esplicite](conversions.md#explicit-conversions)) del valore di `E` al tipo `T`. Se non esiste alcuna conversione esplicita dal `E` a `T`, si verifica un errore in fase di associazione. In caso contrario, il risultato è il valore prodotto dalla conversione esplicita. Il risultato è sempre classificato come un valore, anche se `E` indica una variabile.

La grammatica per un *cast_expression* alcune ambiguità sintattica. Ad esempio, l'espressione `(x)-y` potrebbero essere interpretati come un *cast_expression* (un cast di `-y` digitare `x`) o come un *additive_expression* combinati con un *parenthesized_expression* (che calcola il valore `x - y)`.

Per risolvere *cast_expression* ambiguità, esiste la regola seguente: Una sequenza di uno o più *token*s ([spazi vuoti](lexical-structure.md#white-space)) racchiuso tra parentesi viene considerato l'inizio di una *cast_expression* solo se almeno uno dei seguenti è vere:

*  La sequenza di token è grammatica corretta per un *tipo*, ma non per un *espressione*.
*  La sequenza di token è grammatica corretta per un *tipo*, e il token immediatamente successivo alla parentesi di chiusura è il token "`~`", il token "`!`", il token "`(`", un  *Identificatore* ([sequenze di escape di caratteri Unicode](lexical-structure.md#unicode-character-escape-sequences)), una *letterale* ([valori letterali](lexical-structure.md#literals)), o qualsiasi *parola chiave*([Parole chiave](lexical-structure.md#keywords)), ad eccezione `as` e `is`.

Il termine "grammatica corretta" sopra indica solo che la sequenza di token deve essere conforme alle specifiche regole grammaticali. E in particolare non prende in considerazione il significato effettivo di tutti gli identificatori che lo costituiscono. Ad esempio, se `x` e `y` sono gli identificatori, quindi `x.y` grammatica corretta per un tipo, anche se `x.y` non indica un tipo.

Dalla regola di risoluzione dell'ambiguità segue che, se `x` e `y` sono identificatori `(x)y`, `(x)(y)`, e `(x)(-y)` sono *cast_expression*s, ma `(x)-y` non lo è, anche se `x` identifica un tipo. Tuttavia, se `x` è una parola chiave che identifica un tipo predefinito (, ad esempio `int`), quindi tutti i quattro moduli vengono *cast_expression*s (perché tale parola chiave non può costituire un'espressione di per sé).

### <a name="await-expressions"></a>Espressioni Await

Viene utilizzato l'operatore await per sospendere la valutazione della funzione async contenitore fino al completamento dell'operazione asincrona rappresentata dall'operando.

```antlr
await_expression
    : 'await' unary_expression
    ;
```

Un' *await_expression* è consentito solo nel corpo di una funzione asincrona ([iteratori](classes.md#iterators)). All'interno di inclusione più vicina funzione async, un' *await_expression* potrebbe non verificarsi in queste posizioni:

*  All'interno di una funzione anonima annidati (non-async)
*  All'interno del blocco di un *lock_statement*
*  In un contesto unsafe

Si noti che un *await_expression* non può verificarsi nella maggior parte delle posizioni all'interno di un *query_expression*, in quanto quelli sintatticamente vengono trasformati per usare le espressioni lambda non asincrone.

All'interno di una funzione asincrona, `await` non può essere usato come identificatore. Di conseguenza non è disponibile alcuna ambiguità sintattica tra espressioni await e diverse espressioni che includono gli identificatori. Di fuori di funzioni asincrone `await` funge da identificatore normale.

L'operando di un' *await_expression* viene chiamato il ***attività***. Rappresenta un'operazione asincrona che può o potrebbe non essere completo al momento il *await_expression* viene valutata. Lo scopo dell'operatore await viene per sospendere l'esecuzione della funzione async contenitore fino al completamento dell'attività attesa e quindi ottenere il risultato.

#### <a name="awaitable-expressions"></a>Espressioni awaitable

È necessaria l'attività di un'espressione await ***awaitable***. Un'espressione `t` è di tipo awaitable se una delle seguenti condizioni:

*  `t` è di tipo in fase di compilazione `dynamic`
*  `t` dispone di un metodo di estensione o istanze accessibile chiamato `GetAwaiter` senza parametri e nessun parametro di tipo e un tipo restituito `A` per il quale tenere premuto tutti gli elementi seguenti:
   * `A` implementa l'interfaccia `System.Runtime.CompilerServices.INotifyCompletion` (d'ora in poi noto come `INotifyCompletion` per brevità)
   * `A` dispone di una proprietà di istanza accessibile e leggibile `IsCompleted` typu `bool`
   * `A` dispone di un metodo di istanza accessibile `GetResult` senza parametri e nessun parametro di tipo

Lo scopo del `GetAwaiter` metodo consiste nell'ottenere un ***awaiter*** per l'attività. Il tipo `A` viene chiamato il ***tipo di elemento awaiter*** per l'espressione await.

Lo scopo del `IsCompleted` proprietà consiste nel determinare se l'attività è già completata. In questo caso, non è necessario sospendere la valutazione.

Lo scopo del `INotifyCompletion.OnCompleted` metodo consiste nel sottoscrivere un "continuation" per l'attività; vale a dire un delegato (di tipo `System.Action`) che verrà richiamato una volta completata l'operazione.

Lo scopo del `GetResult` metodo consiste nell'ottenere il risultato dell'attività di completamento dell'operazione. Questo risultato può essere esito positivo, possibilmente con un valore del risultato, oppure può essere un'eccezione che viene generata dal `GetResult` (metodo).

#### <a name="classification-of-await-expressions"></a>Classificazione di espressioni await

L'espressione `await t` viene classificato come espressione `(t).GetAwaiter().GetResult()`. Di conseguenza, se il tipo restituito del `GetResult` viene `void`, il *await_expression* viene classificato come nulla. Se dispone di un tipo restituito non void `T`, il *await_expression* viene classificato come un valore di tipo `T`.

#### <a name="runtime-evaluation-of-await-expressions"></a>Runtime di valutazione di espressioni await

In fase di esecuzione, l'espressione `await t` viene valutata nel modo seguente:

*  Un elemento awaiter `a` viene ottenuto dalla valutazione dell'espressione `(t).GetAwaiter()`.
*  Oggetto `bool` `b` viene ottenuto dalla valutazione dell'espressione `(a).IsCompleted`.
*  Se `b` viene `false` quindi valutazione dipende dal fatto che `a` implementa l'interfaccia `System.Runtime.CompilerServices.ICriticalNotifyCompletion` (d'ora in poi noto come `ICriticalNotifyCompletion` per brevità). Questa verifica viene eseguita in fase; di associazione ad esempio in fase di esecuzione se `a` ha il tipo in fase di compilazione `dynamic`e in fase di compilazione in caso contrario. Let `r` indicano il delegato di ripresa ([iteratori](classes.md#iterators)):
    * Se `a` neimplementuje metodu `ICriticalNotifyCompletion`, quindi l'espressione `(a as (INotifyCompletion)).OnCompleted(r)` viene valutata.
    * Se `a` implementare `ICriticalNotifyCompletion`, quindi l'espressione `(a as (ICriticalNotifyCompletion)).UnsafeOnCompleted(r)` viene valutata.
    * La valutazione viene quindi sospesa e il controllo venga restituito al chiamante della funzione asincrona corrente.
*  Entrambi immediatamente dopo (se `b` è stata `true`), o al momento della successiva chiamata del delegato di ripresa (se `b` era `false`), l'espressione `(a).GetResult()` viene valutata. Se viene restituito un valore, tale valore è il risultato del *await_expression*. In caso contrario, il risultato è nothing.

Implementazione di awaiter dei metodi dell'interfaccia `INotifyCompletion.OnCompleted` e `ICriticalNotifyCompletion.UnsafeOnCompleted` dovrebbero provocare il delegato `r` da richiamare al massimo una volta. In caso contrario, il comportamento della funzione contenitore async è definito.

## <a name="arithmetic-operators"></a>Operatori aritmetici

Il `*`, `/`, `%`, `+`, e `-` gli operatori sono denominati operatori aritmetici.

```antlr
multiplicative_expression
    : unary_expression
    | multiplicative_expression '*' unary_expression
    | multiplicative_expression '/' unary_expression
    | multiplicative_expression '%' unary_expression
    ;

additive_expression
    : multiplicative_expression
    | additive_expression '+' multiplicative_expression
    | additive_expression '-' multiplicative_expression
    ;
```

Se un operando di un operatore aritmetico ha il tipo di fase di compilazione `dynamic`, quindi in modo dinamico è associata l'espressione ([binding dinamico](expressions.md#dynamic-binding)). In questo caso è il tipo di fase di compilazione dell'espressione `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di fase di esecuzione di tali operandi che presentano il tipo di fase di compilazione `dynamic`.

### <a name="multiplication-operator"></a>Operatore di moltiplicazione

Per un'operazione del form `x * y`, la risoluzione dell'overload di operatore binario ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Di seguito sono elencati gli operatori di moltiplicazione predefiniti. Tutti gli operatori calcolano il prodotto dei `x` e `y`.

*  Moltiplicazione integer:

   ```csharp
   int operator *(int x, int y);
   uint operator *(uint x, uint y);
   long operator *(long x, long y);
   ulong operator *(ulong x, ulong y);
   ```

   In un `checked` contesto, se il prodotto è compreso nell'intervallo del tipo di risultato, un `System.OverflowException` viene generata un'eccezione. In un `unchecked` contesto, eventuali overflow non vengono segnalati e qualsiasi bit più significativi significativo di fuori dell'intervallo del tipo di risultato vengono ignorati.


*  Moltiplicazione a virgola mobile:

   ```csharp
   float operator *(float x, float y);
   double operator *(double x, double y);
   ```

   Il prodotto viene calcolato in base alle regole aritmetiche IEEE 754. Nella tabella seguente sono elencati i risultati di tutte le possibili combinazioni di valori finiti diverso da zero, gli zeri, valori infiniti e NaN. Nella tabella `x` e `y` sono valori positivi finiti. `z` è il risultato di `x * y`. Se il risultato è troppo grande per il tipo di destinazione `z` è infinito. Se il risultato è troppo piccolo per il tipo di destinazione `z` è uguale a zero.

   |      |      |      |     |     |      |      |     |
   |:----:|-----:|:----:|:---:|:---:|:----:|:----:|:----|
   |      | + y   | -y   | +0  | -0  | +inf | -inf | NaN | 
   | +x   | +z   | -z   | +0  | -0  | +inf | -inf | NaN | 
   | -x   | -z   | +z   | -0  | +0  | -inf | +inf | NaN | 
   | +0   | +0   | -0   | +0  | -0  | NaN  | NaN  | NaN | 
   | -0   | -0   | +0   | -0  | +0  | NaN  | NaN  | NaN | 
   | +inf | +inf | -inf | NaN | NaN | +inf | -inf | NaN | 
   | -inf | -inf | +inf | NaN | NaN | -inf | +inf | NaN | 
   | NaN  | NaN  | NaN  | NaN | NaN | NaN  | NaN  | NaN | 

*  Moltiplicazione decimale:

   ```csharp
   decimal operator *(decimal x, decimal y);
   ```

   Se il valore risultante è troppo grande per essere rappresentato nel `decimal` formato, un `System.OverflowException` viene generata un'eccezione. Se il valore del risultato è troppo piccolo per essere rappresentato nel `decimal` formato, il risultato è uguale a zero. La scala del risultato, prima di qualsiasi arrotondamento, è la somma delle scale dei due operandi.

   Moltiplicazione decimale equivale all'uso dell'operatore di moltiplicazione di tipo `System.Decimal`.


### <a name="division-operator"></a>Operatore di divisione

Per un'operazione del form `x / y`, la risoluzione dell'overload di operatore binario ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Gli operatori di divisione predefiniti vengono elencati di seguito. Tutti gli operatori di calcolo il quoziente degli `x` e `y`.

*  Divisione di interi:

   ```csharp
   int operator /(int x, int y);
   uint operator /(uint x, uint y);
   long operator /(long x, long y);
   ulong operator /(ulong x, ulong y);
   ```

   Se il valore dell'operando destro è zero, una `System.DivideByZeroException` viene generata un'eccezione.

   La divisione Arrotonda il risultato verso lo zero. In questo modo il valore assoluto del risultato è l'integer più grande è minore o uguale al valore assoluto del quoziente di due operandi. Il risultato è zero o positivo quando i due operandi hanno lo stesso segno e zero o negativo quando dispone di due operandi segno opposto.

   Se l'operando sinistro è il più piccolo che è possibile rappresentare `int` oppure `long` valore e l'operando destro è `-1`, si verifica un overflow. In un `checked` contesto, in questo modo, un `System.ArithmeticException` (o una sottoclasse relativa) viene generata. In un' `unchecked` contesto, è definito dall'implementazione se un `System.ArithmeticException` (o una sottoclasse relativa) viene generata un'eccezione o l'overflow viene segnalato con il valore dell'operando di sinistra.

*  Divisione a virgola mobile:

   ```csharp
   float operator /(float x, float y);
   double operator /(double x, double y);
   ```

   Il quoziente viene calcolato in base alle regole aritmetiche IEEE 754. Nella tabella seguente sono elencati i risultati di tutte le possibili combinazioni di valori finiti diverso da zero, gli zeri, valori infiniti e NaN. Nella tabella `x` e `y` sono valori positivi finiti. `z` è il risultato di `x / y`. Se il risultato è troppo grande per il tipo di destinazione `z` è infinito. Se il risultato è troppo piccolo per il tipo di destinazione `z` è uguale a zero.

   |      |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | + y   | -y   | +0   | -0   | +inf | -inf | NaN  | 
   | +x   | +z   | -z   | +inf | -inf | +0   | -0   | NaN  | 
   | -x   | -z   | +z   | -inf | +inf | -0   | +0   | NaN  | 
   | +0   | +0   | -0   | NaN  | NaN  | +0   | -0   | NaN  | 
   | -0   | -0   | +0   | NaN  | NaN  | -0   | +0   | NaN  | 
   | +inf | +inf | -inf | +inf | -inf | NaN  | NaN  | NaN  | 
   | -inf | -inf | +inf | -inf | +inf | NaN  | NaN  | NaN  | 
   | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | 

*  Divisione decimale:

   ```csharp
   decimal operator /(decimal x, decimal y);
   ```

   Se il valore dell'operando destro è zero, una `System.DivideByZeroException` viene generata un'eccezione. Se il valore risultante è troppo grande per essere rappresentato nel `decimal` formato, un `System.OverflowException` viene generata un'eccezione. Se il valore del risultato è troppo piccolo per essere rappresentato nel `decimal` formato, il risultato è uguale a zero. La scala del risultato è la più piccola scala in grado di mantenere un risultato uguale al più vicino al valore decimale rappresentabile per il risultato matematico true.

   Divisione di decimali equivale a usare l'operatore di divisione del tipo `System.Decimal`.


### <a name="remainder-operator"></a>Operatore di resto

Per un'operazione del form `x % y`, la risoluzione dell'overload di operatore binario ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Di seguito sono elencati gli operatori di resto predefiniti. Tutti gli operatori di calcolo il resto della divisione tra `x` e `y`.

*  Resto intero:

   ```csharp
   int operator %(int x, int y);
   uint operator %(uint x, uint y);
   long operator %(long x, long y);
   ulong operator %(ulong x, ulong y);
   ```

   Il risultato del `x % y` è il valore prodotto da `x - (x / y) * y`. Se `y` è uguale a zero, una `System.DivideByZeroException` viene generata un'eccezione.

   Se l'operando sinistro è il più piccolo `int` oppure `long` valore e l'operando destro è `-1`, un `System.OverflowException` viene generata un'eccezione. In nessun caso esegue `x % y` generano un'eccezione in cui `x / y` non genererebbe un'eccezione.

*  Resto a virgola mobile:

   ```csharp
   float operator %(float x, float y);
   double operator %(double x, double y);
   ```

   Nella tabella seguente sono elencati i risultati di tutte le possibili combinazioni di valori finiti diverso da zero, gli zeri, valori infiniti e NaN. Nella tabella `x` e `y` sono valori positivi finiti. `z` è il risultato del `x % y` e viene calcolata come `x - n * y`, dove `n` è l'integer più grande è minore o uguale a `x / y`. Questo metodo di calcolo è analogo a quella utilizzata per gli operandi di numero intero, ma è diversa dalla definizione del valore IEEE 754 (in cui `n` è il numero intero più vicino `x / y`).

   |      |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | + y   | -y   | +0   | -0   | +inf | -inf | NaN  | 
   | +x   | +z   | +z   | NaN  | NaN  | x    | x    | NaN  | 
   | -x   | -z   | -z   | NaN  | NaN  | -x   | -x   | NaN  | 
   | +0   | +0   | +0   | NaN  | NaN  | +0   | +0   | NaN  | 
   | -0   | -0   | -0   | NaN  | NaN  | -0   | -0   | NaN  | 
   | +inf | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | 
   | -inf | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | 
   | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | 

*  Resto decimale:

   ```csharp
   decimal operator %(decimal x, decimal y);
   ```

   Se il valore dell'operando destro è zero, una `System.DivideByZeroException` viene generata un'eccezione. La scala del risultato, prima di qualsiasi arrotondamento, è il più elevato tra le scale dei due operandi e il segno del risultato, se diverso da zero, è uguale a quello di `x`.

   Resto decimale equivale a usare l'operatore di resto del tipo `System.Decimal`.


### <a name="addition-operator"></a>Operatore di addizione

Per un'operazione del form `x + y`, la risoluzione dell'overload di operatore binario ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Di seguito sono elencati gli operatori di addizione già. Per i tipi numerici e di enumerazione, gli operatori di addizione già calcolano la somma dei due operandi. Quando uno o entrambi gli operandi sono di tipo stringa, gli operatori di addizione già concatenano la rappresentazione di stringa degli operandi.

*  Aggiunta di integer:

   ```csharp
   int operator +(int x, int y);
   uint operator +(uint x, uint y);
   long operator +(long x, long y);
   ulong operator +(ulong x, ulong y);
   ```

   In un `checked` contesto, se la somma è compreso nell'intervallo del tipo di risultato, un `System.OverflowException` viene generata un'eccezione. In un `unchecked` contesto, eventuali overflow non vengono segnalati e qualsiasi bit più significativi significativo di fuori dell'intervallo del tipo di risultato vengono ignorati.

*  Addizione a virgola mobile:

   ```csharp
   float operator +(float x, float y);
   double operator +(double x, double y);
   ```

   La somma viene calcolata in base alle regole aritmetiche IEEE 754. Nella tabella seguente sono elencati i risultati di tutte le possibili combinazioni di valori finiti diverso da zero, gli zeri, valori infiniti e NaN. Nella tabella `x` e `y` sono valori finiti diverso da zero, e `z` è il risultato di `x + y`. Se `x` e `y` hanno la stessa grandezza ma segno opposto, `z` è zero positivo. Se `x + y` è troppo grande per essere rappresentato nel tipo di destinazione `z` è un numero infinito con lo stesso segno `x + y`.

   |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | y    | +0   | -0   | +inf | -inf | NaN  | 
   | x    | l    | x    | x    | +inf | -inf | NaN  | 
   | +0   | y    | +0   | +0   | +inf | -inf | NaN  | 
   | -0   | y    | +0   | -0   | +inf | -inf | NaN  | 
   | +inf | +inf | +inf | +inf | +inf | NaN  | NaN  | 
   | -inf | -inf | -inf | -inf | NaN  | -inf | NaN  | 
   | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | 

*  Addizione:

   ```csharp
   decimal operator +(decimal x, decimal y);
   ```

   Se il valore risultante è troppo grande per essere rappresentato nel `decimal` formato, un `System.OverflowException` viene generata un'eccezione. La scala del risultato, prima di qualsiasi arrotondamento, è il più elevato tra le scale dei due operandi.

   Addizione equivale all'uso dell'operatore di addizione typu `System.Decimal`.

*  Aggiunta di enumerazione. Ogni tipo di enumerazione in modo implicito fornisce i seguenti operatori, predefiniti in cui `E` è il tipo di enumerazione, e `U` è il tipo sottostante di `E`:

   ```csharp
   E operator +(E x, U y);
   E operator +(U x, E y);
   ```

   In fase di esecuzione questi operatori vengono valutati esattamente come `(E)((U)x + (U)y)`.

*  Concatenazione di stringhe:

   ```csharp
   string operator +(string x, string y);
   string operator +(string x, object y);
   string operator +(object x, string y);
   ```

   Questi overload del file binario `+` operatore esegue la concatenazione di stringhe. Se un operando di concatenazione di stringhe viene `null`, viene sostituito con una stringa vuota. In caso contrario, qualsiasi argomento non di tipo stringa viene convertito in una rappresentazione di stringa richiamando virtuale `ToString` metodo ereditato dal tipo `object`. Se `ToString` restituisce `null`, viene sostituito con una stringa vuota.

   ```csharp
   using System;
   
   class Test
   {
       static void Main() {
           string s = null;
           Console.WriteLine("s = >" + s + "<");        // displays s = ><
           int i = 1;
           Console.WriteLine("i = " + i);               // displays i = 1
           float f = 1.2300E+15F;
           Console.WriteLine("f = " + f);               // displays f = 1.23E+15
           decimal d = 2.900m;
           Console.WriteLine("d = " + d);               // displays d = 2.900
       }
   }
   ```

   Il risultato dell'operatore di concatenazione delle stringhe è una stringa che include i caratteri dell'operando sinistro seguito dai caratteri dell'operando destro. L'operatore di concatenazione delle stringhe non restituisce mai un `null` valore. Oggetto `System.OutOfMemoryException` può essere generata se non è disponibile memoria sufficiente per allocare la stringa risultante.

*  Combinazione di delegati. Ogni tipo di delegato in modo implicito fornisce i seguenti operatori predefiniti, in cui `D` è il tipo di delegato:

   ```csharp
   D operator +(D x, D y);
   ```

   Il file binario `+` operatore esegue una combinazione di delegati quando entrambi gli operandi sono dello stesso tipo delegato `D`. (Se gli operandi hanno tipi delegati diversi, si verifica un errore in fase di associazione.) Se è il primo operando `null`, il risultato dell'operazione è il valore del secondo operando (anche se sono anche `null`). In caso contrario, se il secondo operando `null`, il risultato dell'operazione è il valore del primo operando. In caso contrario, il risultato dell'operazione è un nuovo delegato dell'istanza che, quando richiamata, richiama il primo operando e richiama quindi il secondo operando. Per esempi di combinazione di delegati, vedere [operatore di sottrazione](expressions.md#subtraction-operator) e [chiamata a un delegato](delegates.md#delegate-invocation). Poiché `System.Delegate` non è un tipo di delegato `operator`  `+` non è definito.

### <a name="subtraction-operator"></a>Operatore di sottrazione

Per un'operazione del form `x - y`, la risoluzione dell'overload di operatore binario ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Gli operatori predefiniti sottrazione sono elencati di seguito. Gli operatori tutti sottrarre `y` da `x`.

*  Sottrazione di integer:

   ```csharp
   int operator -(int x, int y);
   uint operator -(uint x, uint y);
   long operator -(long x, long y);
   ulong operator -(ulong x, ulong y);
   ```

   In un `checked` contesto, se la differenza è compreso nell'intervallo del tipo di risultato, un `System.OverflowException` viene generata un'eccezione. In un `unchecked` contesto, eventuali overflow non vengono segnalati e qualsiasi bit più significativi significativo di fuori dell'intervallo del tipo di risultato vengono ignorati.

*  Sottrazione a virgola mobile:

   ```csharp
   float operator -(float x, float y);
   double operator -(double x, double y);
   ```

   La differenza viene calcolata in base alle regole aritmetiche IEEE 754. Nella tabella seguente sono elencati i risultati di tutte le possibili combinazioni di valori finiti diverso da zero, gli zeri, valori infiniti e NaN. Nella tabella `x` e `y` sono valori finiti diverso da zero, e `z` è il risultato di `x - y`. Se `x` e `y` sono uguali, `z` è zero positivo. Se `x - y` è troppo grande per essere rappresentato nel tipo di destinazione `z` è un numero infinito con lo stesso segno `x - y`.

   |      |      |      |      |      |      |     |
   |:----:|:----:|:----:|:----:|:----:|:----:|:---:|
   | NaN  | y    | +0   | -0   | +inf | -inf | NaN | 
   | x    | l    | x    | x    | -inf | +inf | NaN | 
   | +0   | -y   | +0   | +0   | -inf | +inf | NaN | 
   | -0   | -y   | -0   | +0   | -inf | +inf | NaN | 
   | +inf | +inf | +inf | +inf | NaN  | +inf | NaN | 
   | -inf | -inf | -inf | -inf | -inf | NaN  | NaN | 
   | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN | 

*  Sottrazione decimale:

   ```csharp
   decimal operator -(decimal x, decimal y);
   ```

   Se il valore risultante è troppo grande per essere rappresentato nel `decimal` formato, un `System.OverflowException` viene generata un'eccezione. La scala del risultato, prima di qualsiasi arrotondamento, è il più elevato tra le scale dei due operandi.

   Sottrazione decimale equivale a usare l'operatore di sottrazione del tipo `System.Decimal`.

*  Sottrazione di enumerazione. Ogni tipo di enumerazione fornisce in modo implicito dall'operatore predefinito seguente, dove `E` è il tipo di enumerazione, e `U` è il tipo sottostante di `E`:

   ```csharp
   U operator -(E x, E y);
   ```

   Questo operatore viene valutato esattamente come `(U)((U)x - (U)y)`. In altre parole, l'operatore calcola la differenza tra i valori ordinali delle `x` e `y`, e il tipo del risultato è il tipo sottostante dell'enumerazione.

   ```csharp
   E operator -(E x, U y);
   ```

   Questo operatore viene valutato esattamente come `(E)((U)x - y)`. In altre parole, l'operatore sottrae un valore dal tipo sottostante dell'enumerazione, producendo un valore dell'enumerazione.

*  Rimozione del delegato. Ogni tipo di delegato in modo implicito fornisce i seguenti operatori predefiniti, in cui `D` è il tipo di delegato:

   ```csharp
   D operator -(D x, D y);
   ```

   Il file binario `-` operatore esegue la rimozione dei delegati quando entrambi gli operandi sono dello stesso tipo delegato `D`. Se gli operandi hanno tipi delegati diversi, si verifica un errore in fase di associazione. Se è il primo operando `null`, il risultato dell'operazione è `null`. In caso contrario, se il secondo operando `null`, il risultato dell'operazione è il valore del primo operando. In caso contrario, entrambi gli operandi rappresentano gli elenchi di chiamata ([dichiarazioni Delegate](delegates.md#delegate-declarations)) con una o più voci e il risultato è costituito l'elenco del primo operando con il secondo rimosse le voci da un nuovo elenco di chiamate fornito l'elenco del secondo operando è un sottoelenco contiguo del primo.     (Per determinare l'uguaglianza sottoelenco, le voci corrispondenti vengono confrontate per l'operatore di uguaglianza delegato ([delegare gli operatori di uguaglianza](expressions.md#delegate-equality-operators)).) In caso contrario, il risultato è il valore dell'operando sinistro. Nessuno degli elenchi degli operandi viene modificato nel processo. Se l'elenco del secondo operando corrisponde a più sottoelenchi contigui voci nell'elenco del primo operando, viene rimosso il sottoelenco corrisponda più a destra delle voci contigui. Se la rimozione restituisce un elenco vuoto, il risultato è `null`. Ad esempio:

   ```csharp
   delegate void D(int x);
   
   class C
   {
       public static void M1(int i) { /* ... */ }
       public static void M2(int i) { /* ... */ }
   }

   class Test
   {
       static void Main() { 
           D cd1 = new D(C.M1);
           D cd2 = new D(C.M2);
           D cd3 = cd1 + cd2 + cd2 + cd1;   // M1 + M2 + M2 + M1
           cd3 -= cd1;                      // => M1 + M2 + M2
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd1 + cd2;                // => M2 + M1
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd2 + cd2;                // => M1 + M1
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd2 + cd1;                // => M1 + M2
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd1 + cd1;                // => M1 + M2 + M2 + M1
       }
   }
   ```

## <a name="shift-operators"></a>Operatori shift

Il `<<` e `>>` gli operatori vengono utilizzati per eseguire operazioni di spostamento di bit.

```antlr
shift_expression
    : additive_expression
    | shift_expression '<<' additive_expression
    | shift_expression right_shift additive_expression
    ;
```

Se un operando di un *shift_expression* dispone del tipo in fase di compilazione `dynamic`, quindi l'espressione è associata in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)). In questo caso è il tipo di fase di compilazione dell'espressione `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di fase di esecuzione di tali operandi che presentano il tipo di fase di compilazione `dynamic`.

Per un'operazione del form `x << count` oppure `x >> count`, la risoluzione dell'overload di operatore binario ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Quando si dichiara un operatore shift di overload, il tipo del primo operando deve essere sempre la classe o struct che contiene la dichiarazione dell'operatore e il tipo del secondo operando deve essere sempre `int`.

Operatori di spostamento predefiniti vengono elencati di seguito.

*  Spostamento a sinistra:

   ```csharp
   int operator <<(int x, int count);
   uint operator <<(uint x, int count);
   long operator <<(long x, int count);
   ulong operator <<(ulong x, int count);
   ```

   Il `<<` turni operatore `x` a sinistra di un numero di bit calcolata come descritto di seguito.

   Il bit più significativi compreso nell'intervallo del tipo di risultato di `x` vengono eliminati, i bit rimanenti vengono spostati a sinistra e le posizioni dei bit vuoti meno significativi vengono impostate su zero.

*  Spostamento a destra:

   ```csharp
   int operator >>(int x, int count);
   uint operator >>(uint x, int count);
   long operator >>(long x, int count);
   ulong operator >>(ulong x, int count);
   ```

   Il `>>` turni operatore `x` destra di un numero di bit calcolata come descritto di seguito.

   Quando `x` JE typu `int` oppure `long`, il bit meno significativi del `x` vengono scartati, i bit rimanenti vengono spostati a destra e i bit vuoti meno significativi vengono impostati su zero se `x` è un valore non negativo e impostata su 1 se `x` è negativo.

   Quando `x` JE typu `uint` oppure `ulong`, il bit meno significativi del `x` vengono eliminati, i bit rimanenti vengono spostati a destra e i bit vuoti meno significativi vengono impostati su zero.

Per gli operatori predefiniti, il numero di bit da spostare viene calcolato come segue:

*  Quando il tipo della `x` viene `int` oppure `uint`, il conteggio dello spostamento è dato dai cinque bit meno significativi del `count`. In altre parole, il conteggio dello spostamento viene calcolato da `count & 0x1F`.
*  Quando il tipo della `x` viene `long` oppure `ulong`, il conteggio dello spostamento è dato dai sei bit meno significativi del `count`. In altre parole, il conteggio dello spostamento viene calcolato da `count & 0x3F`.

Se il conteggio dello spostamento risultante è zero, gli operatori di spostamento è sufficiente restituiscono il valore di `x`.

Operazioni di spostamento mai provocare un overflow e producono gli stessi risultati in `checked` e `unchecked` contesti.

Quando l'operando sinistro del `>>` operatore è un tipo integrale con segno, l'operatore esegue uno scorrimento aritmetico a destra dove il valore del bit più significativo (il bit di segno) dell'operando viene propagato ai bit vuoti meno significativi. Quando l'operando sinistro del `>>` operatore è di tipo integrale senza segno, l'operatore esegue uno spostamento logico verso destra in cui i bit vuoti meno significativi vengono sempre impostati su zero. Per eseguire l'operazione opposta a quella dedotto dal tipo di operando, è possibile utilizzare i cast espliciti. Ad esempio, se `x` è una variabile di tipo `int`, l'operazione `unchecked((int)((uint)x >> y))` esegue uno spostamento logico a destra del `x`.

## <a name="relational-and-type-testing-operators"></a>Operatori relazionali e operatori di test del tipo

Il `==`, `!=`, `<`, `>`, `<=`, `>=`, `is` e `as` gli operatori sono denominati operatori relazionali e test del tipo.

```antlr
relational_expression
    : shift_expression
    | relational_expression '<' shift_expression
    | relational_expression '>' shift_expression
    | relational_expression '<=' shift_expression
    | relational_expression '>=' shift_expression
    | relational_expression 'is' type
    | relational_expression 'as' type
    ;

equality_expression
    : relational_expression
    | equality_expression '==' relational_expression
    | equality_expression '!=' relational_expression
    ;
```

Il `is` operatore è descritto nella [l'operatore](expressions.md#the-is-operator) e il `as` operatore è descritto in [l'operatore](expressions.md#the-as-operator).

Il `==`, `!=`, `<`, `>`, `<=` e `>=` operatori sono ***gli operatori di confronto***.

Se un operando di un operatore di confronto è il tipo di fase di compilazione `dynamic`, quindi in modo dinamico è associata l'espressione ([binding dinamico](expressions.md#dynamic-binding)). In questo caso è il tipo di fase di compilazione dell'espressione `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di fase di esecuzione di tali operandi che presentano il tipo di fase di compilazione `dynamic`.

Per un'operazione del form `x` *op* `y`, dove *op* è un operatore di confronto, la risoluzione dell'overload ([larisoluzionedell'overloaddioperatorebinario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Gli operatori di confronto predefinite sono descritti nelle sezioni seguenti. Tutti gli operatori di confronto predefiniti restituiscono un risultato di tipo `bool`, come descritto nella tabella seguente.


| __Operazione__ | __Risultato__                                                       |
|---------------|------------------------------------------------------------------|
| `x == y`      | `true` Se `x` è uguale a `y`, `false` in caso contrario,                 | 
| `x != y`      | `true` Se `x` non è uguale a `y`, `false` in caso contrario,             | 
| `x < y`       | `true` Se `x` è minore di `y`, `false` in caso contrario,                | 
| `x > y`       | `true` Se `x` è maggiore di quella `y`, `false` in caso contrario,             | 
| `x <= y`      | `true` Se `x` è minore o uguale a `y`, `false` in caso contrario,    | 
| `x >= y`      | `true` Se `x` è maggiore o uguale a `y`, `false` in caso contrario, | 

### <a name="integer-comparison-operators"></a>Operatori di confronto di integer

Gli operatori di confronto tra integer predefiniti sono:
```csharp
bool operator ==(int x, int y);
bool operator ==(uint x, uint y);
bool operator ==(long x, long y);
bool operator ==(ulong x, ulong y);

bool operator !=(int x, int y);
bool operator !=(uint x, uint y);
bool operator !=(long x, long y);
bool operator !=(ulong x, ulong y);

bool operator <(int x, int y);
bool operator <(uint x, uint y);
bool operator <(long x, long y);
bool operator <(ulong x, ulong y);

bool operator >(int x, int y);
bool operator >(uint x, uint y);
bool operator >(long x, long y);
bool operator >(ulong x, ulong y);

bool operator <=(int x, int y);
bool operator <=(uint x, uint y);
bool operator <=(long x, long y);
bool operator <=(ulong x, ulong y);

bool operator >=(int x, int y);
bool operator >=(uint x, uint y);
bool operator >=(long x, long y);
bool operator >=(ulong x, ulong y);
```

Ognuno di questi operatori vengono confrontati i valori numerici degli operandi di due integer e restituisce un `bool` valore che indica se è la relazione particolare `true` o `false`.

### <a name="floating-point-comparison-operators"></a>Operatori di confronto a virgola mobile

Gli operatori di confronto a virgola mobile predefiniti sono:
```csharp
bool operator ==(float x, float y);
bool operator ==(double x, double y);

bool operator !=(float x, float y);
bool operator !=(double x, double y);

bool operator <(float x, float y);
bool operator <(double x, double y);

bool operator >(float x, float y);
bool operator >(double x, double y);

bool operator <=(float x, float y);
bool operator <=(double x, double y);

bool operator >=(float x, float y);
bool operator >=(double x, double y);
```

Gli operatori confrontano gli operandi in base alle regole di standard IEEE 754:

*  Se degli operandi è NaN, il risultato viene `false` per tutti gli operatori tranne `!=`, per cui il risultato è `true`. Per qualsiasi coppia di operandi `x != y` produca sempre lo stesso risultato `!(x == y)`. Tuttavia, quando uno o entrambi gli operandi sono un valore NaN, la `<`, `>`, `<=`, e `>=` operatori non producono gli stessi risultati la negazione logica dell'operatore opposto. Ad esempio, se il valore della `x` e `y` è NaN, quindi `x < y` viene `false`, ma `!(x >= y)` è `true`.
*  Quando nessuno dei due operandi sono NaN, gli operatori confrontano i valori dei due operandi a virgola mobile e per quanto riguarda l'ordinamento

   ```
   -inf < -max < ... < -min < -0.0 == +0.0 < +min < ... < +max < +inf
   ```

   in cui `min` e `max` sono i valori minimo e massimo positivi finiti che possono essere rappresentati nel formato a virgola mobile specificato. Notevoli effetti di questo tipo di ordinamento sono:
   * Zero positivo e negativo zero viene considerati uguali.
   * Viene considerato un valore infinito negativo minore di tutti gli altri valori, ma uguale a un altro valore infinito negativo.
   * Un valore infinito positivo viene considerato maggiore di tutti gli altri valori, ma uguale a un altro numero infinito positivo.

### <a name="decimal-comparison-operators"></a>Operatori di confronto decimale

Gli operatori di confronto decimale predefiniti sono:
```csharp
bool operator ==(decimal x, decimal y);
bool operator !=(decimal x, decimal y);
bool operator <(decimal x, decimal y);
bool operator >(decimal x, decimal y);
bool operator <=(decimal x, decimal y);
bool operator >=(decimal x, decimal y);
```

Ognuno di questi operatori vengono confrontati i valori numerici dei due operandi decimali e restituisce un `bool` valore che indica se è la relazione particolare `true` o `false`. Ogni confronto decimale equivale all'uso dell'operatore di uguaglianza del tipo o corrispondente `System.Decimal`.

### <a name="boolean-equality-operators"></a>Operatori di uguaglianza booleano

Gli operatori di uguaglianza booleana predefiniti sono:
```csharp
bool operator ==(bool x, bool y);
bool operator !=(bool x, bool y);
```

Il risultato del `==` viene `true` se entrambi `x` e `y` sono `true` o se entrambi `x` e `y` sono `false`. In caso contrario, il risultato è `false`.

Il risultato del `!=` viene `false` se entrambi `x` e `y` sono `true` o se entrambi `x` e `y` sono `false`. In caso contrario, il risultato è `true`. Quando gli operandi sono di tipo `bool`, il `!=` operatore produce lo stesso risultato di `^` operatore.

### <a name="enumeration-comparison-operators"></a>Operatori di confronto di enumerazione

Ogni tipo di enumerazione in modo implicito fornisce gli operatori di confronto predefinite seguenti:
```csharp
bool operator ==(E x, E y);
bool operator !=(E x, E y);
bool operator <(E x, E y);
bool operator >(E x, E y);
bool operator <=(E x, E y);
bool operator >=(E x, E y);
```

Il risultato della valutazione `x op y`, dove `x` e `y` sono espressioni di un tipo di enumerazione `E` con un tipo sottostante `U`, e `op` è uno degli operatori di confronto, è esattamente identico la valutazione `((U)x) op ((U)y)`. In altre parole, gli operatori di confronto di tipo enumerazione semplicemente confrontano i valori integrali sottostanti tra i due operandi.

### <a name="reference-type-equality-operators"></a>Operatori di uguaglianza di tipo riferimento

Gli operatori di uguaglianza del tipo riferimento predefiniti sono:
```csharp
bool operator ==(object x, object y);
bool operator !=(object x, object y);
```

Gli operatori restituiscono il risultato del confronto tra i due riferimenti per verificarne l'uguaglianza o disuguaglianza.

Poiché gli operatori di uguaglianza di riferimenti predefiniti tipo accettano gli operandi di tipo `object`, si applicano a tutti i tipi che non dichiarano applicabili `operator ==` e `operator !=` membri. Al contrario, qualsiasi operatore di uguaglianza definito dall'utente applicabile nascondono in modo efficace agli operatori di uguaglianza del tipo riferimento predefiniti.

Gli operatori di uguaglianza del tipo riferimento predefiniti richiedono uno dei seguenti:

*  Entrambi gli operandi sono un valore di un tipo noto per essere un' *reference_type* o il valore letterale `null`. Inoltre, una conversione esplicita di riferimenti ([le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)) esistente dal tipo degli operandi nel tipo di altro operando.
*  Un operando è un valore di tipo `T` in cui `T` è un *type_parameter* e l'altro operando è il valore letterale `null`. Inoltre `T` non ha il vincolo di tipo valore.

A meno che una delle seguenti condizioni sono vere, si verifica un errore in fase di associazione. Le implicazioni principali di queste regole sono:

*  È un errore in fase di associazione da utilizzare gli operatori di uguaglianza del tipo riferimento predefinito per confrontare due riferimenti che risultano diversi in fase di associazione. Ad esempio, se i tipi in fase di associazione degli operandi sono due tipi di classe `A` e `B`e se nessuno di essi `A` né `B` deriva da un altro, quindi sarebbe impossibile ritestare per i due operandi a cui fare riferimento allo stesso oggetto. Di conseguenza, l'operazione è considerato un errore in fase di associazione.
*  Gli operatori di uguaglianza del tipo riferimento predefiniti non consentono la valore operandi di tipo da confrontare. Pertanto, a meno che un tipo struct dichiara i propri operatori di uguaglianza, non è possibile confrontare i valori di tale tipo struct.
*  Gli operatori di uguaglianza del tipo riferimento predefiniti non generano mai operazioni di conversione boxing si verificano per i relativi operandi. Sarebbe inutile per eseguire tali operazioni di conversione boxing, poiché i riferimenti alle istanze di boxed appena allocate necessariamente differiscono da tutti gli altri riferimenti.
*  Se un operando di un parametro di tipo `T` viene confrontato con `null`e il tipo di runtime del `T` è un tipo di valore, il risultato del confronto è `false`.

L'esempio seguente controlla se un argomento di un parametro di tipo senza vincoli è `null`.
```csharp
class C<T>
{
    void F(T x) {
        if (x == null) throw new ArgumentNullException();
        ...
    }
}
```

Il `x == null` costrutto è consentito anche se `T` può rappresentare un tipo di valore e il risultato è definito semplicemente `false` quando `T` è un tipo valore.

Per un'operazione del form `x == y` oppure `x != y`, se a eventuali `operator ==` oppure `operator !=` esiste, la risoluzione dell'overload di operatore ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) selezioneranno le regole che operatore anziché l'operatore di uguaglianza di tipo riferimento predefiniti. Tuttavia, è sempre possibile selezionare l'operatore di uguaglianza di riferimenti predefiniti tipo eseguendo il cast esplicito di uno o entrambi gli operandi nel tipo `object`. L'esempio
```csharp
using System;

class Test
{
    static void Main() {
        string s = "Test";
        string t = string.Copy(s);
        Console.WriteLine(s == t);
        Console.WriteLine((object)s == t);
        Console.WriteLine(s == (object)t);
        Console.WriteLine((object)s == (object)t);
    }
}
```
produce l'output
```
True
False
False
False
```

Il `s` e `t` variabili fanno riferimento a due distinti `string` istanze che contiene gli stessi caratteri. Gli output del primo confronto `True` perché l'operatore di uguaglianza di stringa predefinita ([operatori di uguaglianza delle stringhe](expressions.md#string-equality-operators)) sia selezionata quando entrambi gli operandi sono di tipo `string`. Gli altri confronti tutti di output `False` perché l'operatore di uguaglianza di tipo riferimento predefiniti viene selezionata quando uno o entrambi gli operandi sono di tipo `object`.

Si noti che la tecnica precedente non è significativa per i tipi di valore. L'esempio
```csharp
class Test
{
    static void Main() {
        int i = 123;
        int j = 123;
        System.Console.WriteLine((object)i == (object)j);
    }
}
```
emette `False` perché i cast creano riferimenti a due istanze separate di tipo boxed `int` valori.

### <a name="string-equality-operators"></a>Operatori di uguaglianza delle stringhe

Gli operatori di uguaglianza stringa predefinite sono:
```csharp
bool operator ==(string x, string y);
bool operator !=(string x, string y);
```

Due `string` valori vengono considerati uguali quando viene soddisfatta una delle operazioni seguenti:

*  Entrambi i valori sono `null`.
*  Entrambi i valori sono i riferimenti non null per le istanze di stringa contenenti caratteri identici e lunghezze identiche in ogni posizione del carattere.

Gli operatori di uguaglianza di stringa confrontano i valori stringa anziché riferimenti di stringa. Quando due istanze di stringhe separate contengono la stessa identica sequenza di caratteri, i valori delle stringhe sono uguali, ma i riferimenti sono diversi. Come descritto in [gli operatori di uguaglianza di riferimenti tipo](expressions.md#reference-type-equality-operators), degli operatori di uguaglianza del tipo riferimento possono essere utilizzati per confrontare i riferimenti a stringhe anziché i valori stringa.

### <a name="delegate-equality-operators"></a>Delegare gli operatori di uguaglianza

Ogni tipo di delegato in modo implicito fornisce gli operatori di confronto predefinite seguenti:

```csharp
bool operator ==(System.Delegate x, System.Delegate y);
bool operator !=(System.Delegate x, System.Delegate y);
```

Delegato di due istanze sono uguali come indicato di seguito:

*  Se si verifica una delle istanze del delegato `null`, sono uguali solo se entrambi sono `null`.
*  Se i delegati dispongono di diversi tipi di runtime non sono uguali.
*  Se le istanze dei delegati dispongono di un elenco chiamate ([dichiarazioni Delegate](delegates.md#delegate-declarations)), tali istanze sono uguali solo se gli elenchi sono la stessa lunghezza e ogni voce nell'elenco di chiamate è uguale (come definita di seguito) per la voce corrispondente, in ordine, nell'elenco chiamate di altro.

Le regole seguenti determinano l'uguaglianza tra le voci dell'elenco chiamate:

*  Se due voci elenco di chiamate fanno riferimento allo stesso oggetto statico (metodo), le voci sono uguali.
*  Se due voci elenco di chiamate fanno riferimento allo stesso metodo non statico sullo stesso oggetto di destinazione (come definito per gli operatori di uguaglianza di riferimento) le voci sono uguali.
*  Le voci dell'elenco chiamate prodotte dalla valutazione di semanticamente identico *anonymous_method_expression*s oppure *lambda_expression*s con lo stesso set (eventualmente vuoto) della variabile esterna acquisita le istanze sono consentite (ma non necessari) sia uguale.

### <a name="equality-operators-and-null"></a>Null e gli operatori di uguaglianza

Il `==` e `!=` operatori consentono un operando sia un valore di un tipo che ammette valori null e l'altro sia il `null` letterale, anche se non esiste alcun operatore predefinito o definiti dall'utente (in forma unlifted o form è elevato) per l'operazione.

Per un'operazione con uno dei formati
```csharp
x == null
null == x
x != null
null != x
```
in cui `x` è un'espressione di un tipo che ammette valori null, se la risoluzione dell'overload di operatore ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) non riesce a trovare un operatore applicabile, il risultato viene calcolato nel `HasValue` proprietà di `x`. In particolare, le prime due forme sono tradotti `!x.HasValue`, e ultimi due formati vengono convertite in `x.HasValue`.

### <a name="the-is-operator"></a>L'operatore is

Il `is` operatore viene usato per controllare in modo dinamico se il tipo di fase di esecuzione di un oggetto è compatibile con un tipo specificato. Il risultato dell'operazione `E is T`, dove `E` è un'espressione e `T` è un tipo, è un valore booleano valore che indica se `E` correttamente può essere convertito nel tipo `T` tramite una conversione di riferimenti, una conversione boxing conversione, o una conversione unboxing. L'operazione viene valutato come indicato di seguito, dopo che sono stati sostituiti da argomenti di tipo per tutti i parametri di tipo:

*  Se `E` è una funzione anonima, si verifica un errore in fase di compilazione
*  Se `E` è un gruppo di metodi o le `null` letterale, se il tipo di `E` è un tipo riferimento o un tipo nullable e il valore di `E` è null, il risultato è false.
*  In caso contrario, lasciare `D` rappresenta il tipo dinamico di `E` come indicato di seguito:
   * Se il tipo della `E` è un tipo di riferimento `D` è il tipo di runtime del riferimento all'istanza da `E`.
   * Se il tipo della `E` è un tipo nullable, `D` è il tipo sottostante del tipo nullable.
   * Se il tipo della `E` è un tipo, valore non nullable `D` è il tipo di `E`.
*  Il risultato dell'operazione dipende `D` e `T` come indicato di seguito:
   * Se `T` è un tipo riferimento, il risultato è true se `D` e `T` presentano lo stesso tipo, se `D` è un tipo di riferimento e una conversione implicita del riferimento da `D` alla `T` esiste, oppure se `D` è un tipo di valore e dalla conversione boxing `D` a `T` esiste.
   * Se `T` è un tipo che ammette valori null, il risultato è true se `D` è il tipo sottostante di `T`.
   * Se `T` è un tipo di valore non nullable, il risultato è true se `D` e `T` sono dello stesso tipo.
   * In caso contrario, il risultato è false.

Si noti che le conversioni definite dall'utente, non vengono considerate dal `is` operatore.

### <a name="the-as-operator"></a>L'operatore

Il `as` operatore viene usato per convertire in modo esplicito un valore in un determinato tipo di riferimento o nullable. A differenza di un'espressione cast ([espressioni Cast](expressions.md#cast-expressions)), il `as` operatore non genera mai un'eccezione. In alternativa, se la conversione indicata non è possibile, il valore risultante è `null`.

In un'operazione del form `E as T`, `E` deve essere un'espressione e `T` deve essere un tipo riferimento, un parametro di tipo noto per essere un tipo riferimento o un tipo nullable. Inoltre, almeno uno dei seguenti deve essere true o in caso contrario, verrà generato un errore in fase di compilazione:

*  Un'identità ([conversione di identità](conversions.md#identity-conversion)), implicita che ammette valori null ([conversioni implicite nullable](conversions.md#implicit-nullable-conversions)), implicita del riferimento ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)), conversione boxing ([ Le conversioni boxing](conversions.md#boxing-conversions)), esplicite che ammette valori null ([conversioni esplicite nullable](conversions.md#explicit-nullable-conversions)), riferimento esplicito ([le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)), o unboxing ([Conversioni di Unboxing](conversions.md#unboxing-conversions)) non esiste conversione da `E` a `T`.
*  Il tipo della `E` o `T` è un tipo aperto.
*  `E` è il `null` letterale.

Se il tipo di fase di compilazione del `E` non è `dynamic`, l'operazione `E as T` produce lo stesso risultato
```csharp
E is T ? (T)(E) : (T)null
```
con la differenza che `E` viene valutato una sola volta. Il compilatore può essere previsto a ottimizzare `E as T` eseguire al massimo un controllo di tipo dinamico anziché i due controlli di tipo dinamico in cui è inclusa l'espansione del precedente.

Se il tipo di fase di compilazione del `E` viene `dynamic`, a differenza dell'operatore di cast il `as` operatore non è associato in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)). Di conseguenza l'espansione in questo caso è:
```csharp
E is T ? (T)(object)(E) : (T)null
```

Si noti che alcune conversioni, ad esempio le conversioni definite dall'utente, non sono possibili con la `as` operatore e devono invece essere eseguite usando le espressioni cast.

Nell'esempio
```csharp
class X
{

    public string F(object o) {
        return o as string;        // OK, string is a reference type
    }

    public T G<T>(object o) where T: Attribute {
        return o as T;             // Ok, T has a class constraint
    }

    public U H<U>(object o) {
        return o as U;             // Error, U is unconstrained 
    }
}
```
il parametro di tipo `T` di `G` è noto per essere un tipo riferimento, perché contiene il vincolo di classe. Il parametro di tipo `U` dei `H` non è tuttavia; di conseguenza l'utilizzo delle `as` operatore in `H` non è consentita.

## <a name="logical-operators"></a>Operatori logici

Il `&`, `^`, e `|` gli operatori sono denominati operatori logici.

```antlr
and_expression
    : equality_expression
    | and_expression '&' equality_expression
    ;

exclusive_or_expression
    : and_expression
    | exclusive_or_expression '^' and_expression
    ;

inclusive_or_expression
    : exclusive_or_expression
    | inclusive_or_expression '|' exclusive_or_expression
    ;
```

Se un operando di un operatore logico con il tipo di fase di compilazione `dynamic`, quindi in modo dinamico è associata l'espressione ([binding dinamico](expressions.md#dynamic-binding)). In questo caso è il tipo di fase di compilazione dell'espressione `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di fase di esecuzione di tali operandi che presentano il tipo di fase di compilazione `dynamic`.

Per un'operazione del form `x op y`, dove `op` è uno degli operatori logici, la risoluzione dell'overload ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Gli operatori logici predefiniti sono descritte nelle sezioni seguenti.

### <a name="integer-logical-operators"></a>Operatori logici di integer

Gli operatori logici di integer predefiniti sono:
```csharp
int operator &(int x, int y);
uint operator &(uint x, uint y);
long operator &(long x, long y);
ulong operator &(ulong x, ulong y);

int operator |(int x, int y);
uint operator |(uint x, uint y);
long operator |(long x, long y);
ulong operator |(ulong x, ulong y);

int operator ^(int x, int y);
uint operator ^(uint x, uint y);
long operator ^(long x, long y);
ulong operator ^(ulong x, ulong y);
```

Il `&` operatore calcola bit per bit logico `AND` tra i due operandi, il `|` operatore calcola bit per bit logico `OR` dei due operandi e il `^` operatore calcola esclusivo logico bit per bit `OR` dei due operandi. Nessun overflow possono essere eseguite da queste operazioni.

### <a name="enumeration-logical-operators"></a>Operatori logici di enumerazione

Ogni tipo di enumerazione `E` in modo implicito offre operatori logici predefiniti di quanto segue:

```csharp
E operator &(E x, E y);
E operator |(E x, E y);
E operator ^(E x, E y);
```

Il risultato della valutazione `x op y`, dove `x` e `y` sono espressioni di un tipo di enumerazione `E` con un tipo sottostante `U`, e `op` è uno degli operatori logici, è esattamente identico la valutazione `(E)((U)x op (U)y)`. In altre parole, gli operatori logici di tipo di enumerazione è sufficiente eseguono l'operazione logica del tipo sottostante di due operandi.

### <a name="boolean-logical-operators"></a>Operatore logico booleano

L'operatore logico booleano sono:
```csharp
bool operator &(bool x, bool y);
bool operator |(bool x, bool y);
bool operator ^(bool x, bool y);
```

Il risultato di `x & y` è `true` se `x` e `y` sono `true`. In caso contrario, il risultato è `false`.

Il risultato del `x | y` viene `true` se uno dei due `x` oppure `y` è `true`. In caso contrario, il risultato è `false`.

Il risultato di `x ^ y` viene `true` se `x` viene `true` e `y` viene `false`, oppure `x` è `false` e `y` è `true`. In caso contrario, il risultato è `false`. Quando gli operandi sono di tipo `bool`, il `^` operatore calcola lo stesso risultato di `!=` operatore.

### <a name="nullable-boolean-logical-operators"></a>Operatori logici booleani che ammette valori null

Il tipo di valore boolean nullable `bool?` può rappresentare valori di tre `true`, `false`, e `null`ed è concettualmente simile al tipo a tre valori utilizzato per le espressioni booleane in SQL. Per garantire che i risultati prodotti dal `&` e `|` operatori per `bool?` gli operandi sono coerenti con la logica a tre valori di SQL, vengono forniti gli operatori predefiniti seguenti:

```csharp
bool? operator &(bool? x, bool? y);
bool? operator |(bool? x, bool? y);
```

Nella tabella seguente sono elencati i risultati prodotti da questi operatori per tutte le combinazioni dei valori `true`, `false`, e `null`.

| `x`     | `y`     | `x & y` | <code>x &#124; y</code> |
|:-------:|:-------:|:-------:|:-------:|
| `true`  | `true`  | `true`  | `true`  | 
| `true`  | `false` | `false` | `true`  | 
| `true`  | `null`  | `null`  | `true`  | 
| `false` | `true`  | `false` | `true`  | 
| `false` | `false` | `false` | `false` | 
| `false` | `null`  | `false` | `null`  | 
| `null`  | `true`  | `null`  | `true`  | 
| `null`  | `false` | `false` | `null`  | 
| `null`  | `null`  | `null`  | `null`  | 

## <a name="conditional-logical-operators"></a>Operatori logici condizionali

Il `&&` e `||` gli operatori sono denominati operatori logici condizionali. Vengono inoltre chiamati gli operatori logici "short circuii".

```antlr
conditional_and_expression
    : inclusive_or_expression
    | conditional_and_expression '&&' inclusive_or_expression
    ;

conditional_or_expression
    : conditional_and_expression
    | conditional_or_expression '||' conditional_and_expression
    ;
```

Il `&&` e `||` operatori sono versioni condizionale delle `&` e `|` operatori:

*  L'operazione `x && y` corrisponde all'operazione `x & y`, ad eccezione del fatto che `y` viene valutato solo se `x` non `false`.
*  L'operazione `x || y` corrisponde all'operazione `x | y`, ad eccezione del fatto che `y` viene valutato solo se `x` non `true`.

Se un operando di un operatore logico condizionale ha il tipo di fase di compilazione `dynamic`, quindi in modo dinamico è associata l'espressione ([binding dinamico](expressions.md#dynamic-binding)). In questo caso è il tipo di fase di compilazione dell'espressione `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di fase di esecuzione di tali operandi che presentano il tipo di fase di compilazione `dynamic`.

Un'operazione del form `x && y` oppure `x || y` avviene per la risoluzione dell'overload ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) come se l'operazione è stata scritta `x & y` o `x | y`. Quindi,

*  Se la risoluzione dell'overload non riesce a trovare un singolo operatore migliore o se la risoluzione dell'overload seleziona uno degli operatori logici integer predefiniti, si verifica un errore in fase di associazione.
*  In caso contrario, se l'operatore selezionato è uno degli operatori logici booleani predefiniti ([booleani operatori logici](expressions.md#boolean-logical-operators)) o gli operatori logici booleani che ammette valori null ([operatori logici booleani Nullable](expressions.md#nullable-boolean-logical-operators)), il l'operazione viene elaborata come descritto in [booleani operatori logici condizionali](expressions.md#boolean-conditional-logical-operators).
*  In caso contrario, l'operatore selezionato è un operatore definito dall'utente e l'operazione viene elaborata come descritto in [definite dall'utente di operatori logici condizionali](expressions.md#user-defined-conditional-logical-operators).

Non è possibile eseguire direttamente l'overload degli operatori logici condizionali. Tuttavia, poiché gli operatori logici condizionali vengono valutati in termini dei normali operatori logici, gli overload degli operatori logici comuni sono con alcune limitazioni, inoltre considerati overload degli operatori logici condizionali. Questo è descritto più dettagliatamente in [definite dall'utente di operatori logici condizionali](expressions.md#user-defined-conditional-logical-operators).

### <a name="boolean-conditional-logical-operators"></a>Operatori logici condizionali booleani

Quando gli operandi del `&&` o `||` sono di tipo `bool`, o quando gli operandi sono di tipi che non si definiscono dei `operator &` oppure `operator |`, ma definiscono le conversioni implicite a `bool`, l'operazione è elaborate come segue:

*  L'operazione `x && y` darà `x ? y : false`. In altre parole, `x` prima di tutto viene valutata e convertito nel tipo `bool`. Quindi, se `x` viene `true`, `y` viene valutata e convertito nel tipo `bool`, che diventa il risultato dell'operazione. In caso contrario, il risultato dell'operazione è `false`.
*  L'operazione `x || y` darà `x ? true : y`. In altre parole, `x` prima di tutto viene valutata e convertito nel tipo `bool`. Quindi, se `x` viene `true`, il risultato dell'operazione è `true`. In caso contrario, `y` viene valutata e convertito nel tipo `bool`, che diventa il risultato dell'operazione.

### <a name="user-defined-conditional-logical-operators"></a>Operatori logici condizionali definite dall'utente

Quando gli operandi del `&&` oppure `||` siano di tipi che dichiarano un applicabili definite dall'utente `operator &` oppure `operator |`, entrambe le operazioni seguenti deve essere impostato su true, in cui `T` è il tipo in cui è dichiarata l'operatore selezionato:

*  Il tipo restituito e il tipo di ogni parametro dell'operatore selezionato devono essere `T`. In altre parole, l'operatore deve calcolare logico `AND` o l'operatore logico `OR` dei due operandi di tipo `T`e deve restituire un risultato di tipo `T`.
*  `T` deve contenere dichiarazioni di `operator true` e `operator false`.

Se uno di questi requisiti non vengono soddisfatti, verrà generato un errore in fase di associazione. In caso contrario, il `&&` oppure `||` combinando definite dall'utente viene valutata operazione `operator true` o `operator false` con l'operatore definito dall'utente selezionato:

*  L'operazione `x && y` viene valutato come `T.false(x) ? x : T.&(x, y)`, dove `T.false(x)` è una chiamata del `operator false` dichiarato nel `T`, e `T.&(x, y)` è una chiamata dell'oggetto selezionato `operator &`. In altre parole, `x` viene valutato per primo e `operator false` viene richiamato per determinare se il risultato `x` è indubbiamente false. Quindi, se `x` viene sicuramente impostato su false, il risultato dell'operazione è il valore calcolato in precedenza per `x`. In caso contrario, `y` viene valutata viene selezionato `operator &` viene richiamato sul valore calcolato in precedenza per `x` e il valore calcolato per `y` per produrre il risultato dell'operazione.
*  L'operazione `x || y` viene valutato come `T.true(x) ? x : T.|(x, y)`, dove `T.true(x)` è una chiamata del `operator true` dichiarato nel `T`, e `T.|(x,y)` è una chiamata dell'oggetto selezionato `operator|`. In altre parole, `x` viene valutato per primo e `operator true` viene richiamato per determinare se il risultato `x` è assolutamente vero. Quindi, se `x` è certamente true, il risultato dell'operazione è il valore calcolato in precedenza per `x`. In caso contrario, `y` viene valutata viene selezionato `operator |` viene richiamato sul valore calcolato in precedenza per `x` e il valore calcolato per `y` per produrre il risultato dell'operazione.

In una di queste operazioni, l'espressione rappresentata da `x` è solo una volta valutati e l'espressione rappresentata da `y` è o non valutato o valutata esattamente una volta.

Per un esempio di un tipo che implementa `operator true` e `operator false`, vedere [tipo booleano Database](structs.md#database-boolean-type).

## <a name="the-null-coalescing-operator"></a>L'operatore null-coalescing

Il `??` operatore viene chiamato l'operatore null-coalescing.

```antlr
null_coalescing_expression
    : conditional_or_expression
    | conditional_or_expression '??' null_coalescing_expression
    ;
```

Un'espressione null-coalescing del form `a ?? b` richiede `a` sia di un tipo riferimento o un tipo nullable. Se `a` è diverso da null, il risultato del `a ?? b` viene `a`; in caso contrario, il risultato è `b`. L'operazione valuta `b` solo se `a` è null.

L'operatore null-coalescing è associativa a destra, ovvero le operazioni vengono eseguite da destra a sinistra. Ad esempio, un'espressione nel formato `a ?? b ?? c` darà `a ?? (b ?? c)`. In generale le condizioni, di un'espressione del form `E1 ?? E2 ?? ... ?? En` restituisce il primo degli operandi è diverso da null, o null se tutti gli operandi sono null.

Il tipo dell'espressione `a ?? b` dipende da quali conversioni implicite disponibili sugli operandi. In ordine di preferenza, il tipo di `a ?? b` viene `A0`, `A`, o `B`, dove `A` è il tipo di `a` (a condizione che `a` dispone di un tipo), `B` è il tipo di `b` ( purché `b` dispone di un tipo), e `A0` è il tipo sottostante del `A` se `A` è un tipo che ammette valori null, o `A` in caso contrario. In particolare, `a ?? b` viene elaborato come segue:

*  Se `A` esiste e non è un tipo nullable o un tipo riferimento, si verifica un errore in fase di compilazione.
*  Se `b` è un'espressione dinamica, il tipo di risultato `dynamic`. In fase di esecuzione, `a` viene valutato per primo. Se `a` non è null, `a` viene convertito in dinamico, e diventa il risultato dell'operazione. In caso contrario, `b` viene valutato e diventa il risultato dell'operazione.
*  In caso contrario, se `A` esista e sia un tipo nullable e non esiste una conversione implicita da `b` a `A0`, il tipo di risultato è `A0`. In fase di esecuzione, `a` viene valutato per primo. Se `a` non è null, `a` è scorporato tipo `A0`, e diventa il risultato dell'operazione. In caso contrario, `b` viene valutata e convertito nel tipo `A0`, e diventa il risultato dell'operazione.
*  In caso contrario, se `A` esiste ed è presente una conversione implicita da `b` al `A`, il tipo di risultato `A`. In fase di esecuzione, `a` viene valutato per primo. Se `a` non è null, `a` diventa il risultato dell'operazione. In caso contrario, `b` viene valutata e convertito nel tipo `A`, e diventa il risultato dell'operazione.
*  In caso contrario, se `b` dispone di un tipo `B` ed è presente una conversione implicita da `a` al `B`, il tipo di risultato `B`. In fase di esecuzione, `a` viene valutato per primo. Se `a` non è null, `a` è scorporato per digitare `A0` (se `A` esista e ammette valori null) e convertita nel tipo `B`, e diventa il risultato dell'operazione. In caso contrario, `b` viene valutato e diventa il risultato.
*  In caso contrario, `a` e `b` sono incompatibili e un errore in fase di compilazione si verifica.

## <a name="conditional-operator"></a>Operatore condizionale

Il `?:` operatore viene chiamato operatore condizionale. Si parla in alcuni casi anche l'operatore ternario.

```antlr
conditional_expression
    : null_coalescing_expression
    | null_coalescing_expression '?' expression ':' expression
    ;
```

Un'espressione condizionale del form `b ? x : y` innanzitutto valuta la condizione `b`. Quindi, se `b` viene `true`, `x` viene valutato e diventa il risultato dell'operazione. In caso contrario, `y` viene valutato e diventa il risultato dell'operazione. Un'espressione condizionale non restituisce mai entrambe `x` e `y`.

L'operatore condizionale è associativa a destra, ovvero le operazioni vengono eseguite da destra a sinistra. Ad esempio, un'espressione nel formato `a ? b : c ? d : e` darà `a ? b : (c ? d : e)`.

Il primo operando del `?:` operatore deve essere un'espressione che può essere convertita in modo implicito in `bool`, o un'espressione di un tipo che implementa `operator true`. Se viene soddisfatto nessuno di questi requisiti, si verifica un errore in fase di compilazione.

Il secondo e terzo operando `x` e `y`, del `?:` operatore controllare il tipo di espressione condizionale.

*  Se `x` è di tipo `X` e `y` ha tipo `Y` quindi
   * Se una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) esistente dal `X` al `Y`, ma non da `Y` alla `X`, quindi `Y` è il tipo dell'espressione condizionale.
   * Se una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) esistente dal `Y` al `X`, ma non da `X` alla `Y`, quindi `X` è il tipo dell'espressione condizionale.
   * In caso contrario, nessun tipo di espressione può essere determinato e si verifica un errore in fase di compilazione.
*  Se solo uno dei `x` e `y` ha un tipo ed entrambe `x` e `y`, di sono implicitamente convertibili in tale tipo, che è il tipo dell'espressione condizionale.
*  In caso contrario, nessun tipo di espressione può essere determinato e si verifica un errore in fase di compilazione.

L'elaborazione in fase di esecuzione di un'espressione condizionale del form `b ? x : y` prevede i passaggi seguenti:

*  Prima di tutto `b` viene valutato e il `bool` pari a `b` viene determinato:
   * Se una conversione implicita dal tipo della `b` al `bool` esiste, quindi viene eseguita la conversione implicita per produrre un `bool` valore.
   * In caso contrario, il `operator true` definito dal tipo di `b` richiamato per produrre un `bool` valore.
*  Se il `bool` valore prodotto dal passaggio precedente viene `true`, quindi `x` viene valutata e convertito nel tipo dell'espressione condizionale, che diventa il risultato dell'espressione condizionale.
*  In caso contrario, `y` viene valutata e convertito nel tipo dell'espressione condizionale, che diventa il risultato dell'espressione condizionale.

## <a name="anonymous-function-expressions"></a>Espressioni di funzioni anonime

Un' ***funzione anonima*** è un'espressione che rappresenta una definizione di metodo "inline". Una funzione anonima non dispone di un valore o un tipo in e di se stesso, ma è convertibile in un tipo di struttura ad albero espressioni o delegato compatibile. La valutazione di una conversione funzione anonima dipende dal tipo di destinazione della conversione: Se è un tipo delegato, la conversione restituisce un valore di delegato che fa riferimento il metodo che definisce la funzione anonima. Se è un tipo di albero delle espressioni, la conversione restituisce un albero delle espressioni che rappresenta la struttura del metodo come una struttura di oggetti.

Per motivi storici esistono due varianti sintattiche di funzioni anonime, vale a dire *lambda_expression*s e *anonymous_method_expression*s. Per quasi tutti gli scopi *lambda_expression*sono più ridotte ed espressive *anonymous_method_expression*s, che rimangono con le versioni precedenti del linguaggio per la compatibilità.

```antlr
lambda_expression
    : anonymous_function_signature '=>' anonymous_function_body
    ;

anonymous_method_expression
    : 'delegate' explicit_anonymous_function_signature? block
    ;

anonymous_function_signature
    : explicit_anonymous_function_signature
    | implicit_anonymous_function_signature
    ;

explicit_anonymous_function_signature
    : '(' explicit_anonymous_function_parameter_list? ')'
    ;

explicit_anonymous_function_parameter_list
    : explicit_anonymous_function_parameter (',' explicit_anonymous_function_parameter)*
    ;

explicit_anonymous_function_parameter
    : anonymous_function_parameter_modifier? type identifier
    ;

anonymous_function_parameter_modifier
    : 'ref'
    | 'out'
    ;

implicit_anonymous_function_signature
    : '(' implicit_anonymous_function_parameter_list? ')'
    | implicit_anonymous_function_parameter
    ;

implicit_anonymous_function_parameter_list
    : implicit_anonymous_function_parameter (',' implicit_anonymous_function_parameter)*
    ;

implicit_anonymous_function_parameter
    : identifier
    ;

anonymous_function_body
    : expression
    | block
    ;
```

Il `=>` operatore ha la stessa precedenza dell'assegnazione (`=`) e si associa all'operando destro.

Una funzione anonima con il `async` modificatore è una funzione asincrona e segue le regole descritte in [iteratori](classes.md#iterators).

I parametri di una funzione anonima sotto forma di una *lambda_expression* possono essere digitati in modo esplicito o implicito. In un elenco di parametri tipizzati in modo esplicito, viene dichiarato in modo esplicito il tipo di ogni parametro. In un elenco di parametri tipizzati in modo implicito, i tipi dei parametri vengono dedotti dal contesto in cui si verifica la funzione anonima, in particolare, quando la funzione anonima viene convertita in un tipo delegato compatibile o un tipo di albero delle espressioni, che fornisce tipo i tipi di parametro ([conversioni delle funzioni anonime](conversions.md#anonymous-function-conversions)).

In una funzione anonima con un parametro singolo, tipizzata in modo implicito, le parentesi che possono essere omessa dall'elenco di parametri. In altre parole, una funzione anonima del form
```csharp
( param ) => expr
```
può essere abbreviato utilizzando
```csharp
param => expr
```

Elenco di parametri di una funzione anonima sotto forma di un' *anonymous_method_expression* è facoltativo. Se non specificato, i parametri devono essere tipizzati in modo esplicito. Se non, la funzione anonima è convertibile in un delegato con qualsiasi parametro di elenco che non contengono `out` parametri.

Oggetto *block* corpo di una funzione anonima è raggiungibile ([endpoint e raggiungibilità](statements.md#end-points-and-reachability)), a meno che la funzione anonima si verifica all'interno di un'istruzione non raggiungibile.

Di seguito sono riportati alcuni esempi di funzioni anonime:

```csharp
x => x + 1                              // Implicitly typed, expression body
x => { return x + 1; }                  // Implicitly typed, statement body
(int x) => x + 1                        // Explicitly typed, expression body
(int x) => { return x + 1; }            // Explicitly typed, statement body
(x, y) => x * y                         // Multiple parameters
() => Console.WriteLine()               // No parameters
async (t1,t2) => await t1 + await t2    // Async
delegate (int x) { return x + 1; }      // Anonymous method expression
delegate { return 1 + 1; }              // Parameter list omitted
```

Il comportamento delle *lambda_expression*s e *anonymous_method_expression*s è uguali ad eccezione di quanto riportato di seguito:

*  *anonymous_method_expression*s consentono l'elenco di parametri per essere omesse del tutto, la convertibilità in qualsiasi elenco di parametri con valori di tipi delegati.
*  *lambda_expression*s consentono tipi di parametro per omettere e dedurre mentre *anonymous_method_expression*richiedono tipi di parametro indicare in modo esplicito.
*  Il corpo di una *lambda_expression* può essere un'espressione o un blocco di istruzioni mentre il corpo di un' *anonymous_method_expression* deve essere un blocco di istruzioni.
*  Solo *lambda_expression*hanno le conversioni in tipi di albero delle espressioni compatibili ([tipi di albero delle espressioni](types.md#expression-tree-types)).

### <a name="anonymous-function-signatures"></a>Firme di funzione anonima

L'opzione facoltativa *anonymous_function_signature* di una funzione anonima definisce i nomi e, facoltativamente, i tipi dei parametri formali per la funzione anonima. L'ambito dei parametri della funzione anonima è il *anonymous_function_body*. ([Ambiti](basic-concepts.md#scopes)) con l'elenco di parametri (se disponibile) anonimo-corpo del metodo costituisce uno spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)). È pertanto un errore in fase di compilazione per il nome di un parametro della funzione anonima corrisponda al nome di una variabile locale, costante locale o un parametro il cui ambito include il *anonymous_method_expression* o *lambda_ espressione*.

Se una funzione anonima ha un *explicit_anonymous_function_signature*, quindi il set di tipi delegati compatibili e tipi di albero delle espressioni è limitato a quelli aventi stessi tipi di parametro e modificatori nello stesso ordine. A differenza delle conversioni dei metodi di gruppo ([conversioni dei metodi di gruppo](conversions.md#method-group-conversions)), controvarianza dei tipi di parametro di funzione anonima non è supportato. Se una funzione anonima non ha un *anonymous_function_signature*, quindi il set di tipi delegati compatibili e tipi di albero delle espressioni è limitato a quelli che non hanno alcuna `out` parametri.

Si noti che un *anonymous_function_signature* non può includere attributi o una matrice di parametri. Tuttavia, un' *anonymous_function_signature* siano compatibili con un tipo di delegato il cui elenco di parametri contiene una matrice di parametri.

Si noti inoltre che la conversione a un tipo di albero delle espressioni, anche se compatibili tra loro, può comunque non riuscire in fase di compilazione ([tipi di albero delle espressioni](types.md#expression-tree-types)).

### <a name="anonymous-function-bodies"></a>Corpi delle funzioni anonime

Il corpo (*espressione* oppure *blocco*) di una funzione anonima è soggetto alle regole seguenti:

*  Se la funzione anonima include una firma, sono disponibili nel corpo i parametri specificati nella firma. Se la funzione anonima non dispone di alcuna firma può essere convertito in un tipo di delegato o tipo di espressione con parametri ([conversioni delle funzioni anonime](conversions.md#anonymous-function-conversions)), ma i parametri non sono accessibili nel corpo.
*  Ad eccezione di `ref` oppure `out` i parametri specificati nella firma (se presente) di inclusione più vicino funzione anonima, è un errore in fase di compilazione per il corpo per accedere a un `ref` o `out` parametro.
*  Quando il tipo della `this` è un tipo struct, è un errore in fase di compilazione per il corpo per accedere a `this`. Ciò è vero se l'accesso è esplicita (come in `this.x`) o impliciti (come mostrato nella `x` in cui `x` è un membro di istanza dello struct). Questa regola è sufficiente impedisce l'accesso e non influisce sulla ricerca di membri comporta un membro dello struct.
*  Il corpo può accedere alle variabili esterne ([Outer variabili](expressions.md#outer-variables)) della funzione anonima. Accesso a una variabile esterna farà riferimento all'istanza della variabile che è attiva al momento il *lambda_expression* oppure *anonymous_method_expression* viene valutata ([valutazione di espressioni di funzioni anonime](expressions.md#evaluation-of-anonymous-function-expressions)).
*  Tratta di un errore in fase di compilazione per il corpo conterrà un `goto` istruzione `break` istruzione o `continue` istruzione la cui destinazione è all'esterno del corpo o all'interno del corpo di una funzione anonima contenuta.
*  Oggetto `return` restituisce il controllo da una chiamata di inclusione più vicina istruzione nel corpo della funzione anonima, non dal membro della funzione contenitore. Un'espressione specificata in una `return` istruzione deve essere convertibile in modo implicito nel tipo restituito del tipo di delegato o espressione tipo di struttura ad albero in cui inclusione più vicina *lambda_expression* o *anonymous_ method_expression* viene convertito ([conversioni delle funzioni anonime](conversions.md#anonymous-function-conversions)).

Non viene specificato in modo esplicito se vi è alcun modo per eseguire il blocco di una funzione anonima diverso dalla valutazione e la chiamata di *lambda_expression* o *anonymous_method_expression*. In particolare, il compilatore potrebbe scegliere di implementare una funzione anonima per la sintesi di uno o più metodi o tipi denominati. I nomi di tali elementi sintetizzati devono essere un formato riservato per l'utilizzo del compilatore.

### <a name="overload-resolution-and-anonymous-functions"></a>Risoluzione dell'overload e le funzioni anonime

Funzioni anonime in un elenco di argomenti fanno parte di inferenza del tipo e la risoluzione dell'overload. Consultare [inferenza](expressions.md#type-inference) e [risoluzione dell'Overload](expressions.md#overload-resolution) per le regole precise.

L'esempio seguente illustra l'effetto delle funzioni anonime sulla risoluzione dell'overload.

```csharp
class ItemList<T>: List<T>
{
    public int Sum(Func<T,int> selector) {
        int sum = 0;
        foreach (T item in this) sum += selector(item);
        return sum;
    }

    public double Sum(Func<T,double> selector) {
        double sum = 0;
        foreach (T item in this) sum += selector(item);
        return sum;
    }
}
```

Il `ItemList<T>` classe ha due `Sum` metodi. Ognuno ha una `selector` argomento, che estrae il valore da sommare failover da un elemento di elenco. Il valore estratto può essere un' `int` o un `double` e la somma risulta in modo analogo può essere un' `int` o un `double`.

Il `Sum` , ad esempio è stato possibile utilizzare metodi per calcolare le somme in un elenco di righe di dettaglio in un ordine.

```csharp
class Detail
{
    public int UnitCount;
    public double UnitPrice;
    ...
}

void ComputeSums() {
    ItemList<Detail> orderDetails = GetOrderDetails(...);
    int totalUnits = orderDetails.Sum(d => d.UnitCount);
    double orderTotal = orderDetails.Sum(d => d.UnitPrice * d.UnitCount);
    ...
}
```

La prima chiamata di `orderDetails.Sum`, entrambe `Sum` metodi sono applicabili perché la funzione anonima `d => d. UnitCount` compatibile con entrambi `Func<Detail,int>` e `Func<Detail,double>`. Tuttavia, la risoluzione dell'overload seleziona la prima `Sum` metodo perché la conversione in `Func<Detail,int>` è migliore della conversione a `Func<Detail,double>`.

La seconda chiamata di `orderDetails.Sum`, solo il secondo `Sum` metodo è applicabile poiché la funzione anonima `d => d.UnitPrice * d.UnitCount` produce un valore di tipo `double`. Di conseguenza, l'overload resolution sceglie la seconda `Sum` per tale chiamata al metodo.

### <a name="anonymous-functions-and-dynamic-binding"></a>Funzioni anonime e l'associazione dinamica

Una funzione anonima non può essere un ricevitore, argomento o un operando di un'operazione dinamica associata.

### <a name="outer-variables"></a>Variabili esterne

Qualsiasi variabile locale, un parametro di valore o la matrice di parametri il cui ambito include il *lambda_expression* oppure *anonymous_method_expression* viene chiamato un ***variabile esterna*** della funzione anonima. In un membro di funzione di istanza di una classe, il `this` valore viene considerato un parametro di valore e una variabile di qualsiasi funzione anonima contenuta all'interno del membro di funzione esterna.

#### <a name="captured-outer-variables"></a>Acquisire le variabili esterne

Quando una variabile esterna fa riferimento una funzione anonima, la variabile esterna viene considerata come sono stati ***acquisite*** dalla funzione anonima. In genere, la durata di una variabile locale è limitata all'esecuzione del blocco o istruzione a cui è associato ([variabili locali](variables.md#local-variables)). Tuttavia, la durata di una variabile esterna acquisita viene esteso almeno fino a quando il delegato o albero delle espressioni creata dalla funzione anonima diventa idonea per garbage collection.

Nell'esempio
```csharp
using System;

delegate int D();

class Test
{
    static D F() {
        int x = 0;
        D result = () => ++x;
        return result;
    }

    static void Main() {
        D d = F();
        Console.WriteLine(d());
        Console.WriteLine(d());
        Console.WriteLine(d());
    }
}
```
la variabile locale `x` viene acquisito dalla funzione anonima e la durata degli `x` viene esteso almeno fino a quando il delegato restituito da `F` diventa idonea per garbage collection (che non viene eseguita fino alla fine di il programma). Poiché ogni chiamata alla funzione anonima viene eseguita nella stessa istanza di `x`, l'output dell'esempio è:
```
1
2
3
```

Quando una variabile locale o un parametro di valore viene acquisito da una funzione anonima, la variabile locale o il parametro non è più considerato come una variabile fissa ([variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)), ma viene invece considerata un spostabile variabile. In questo modo qualsiasi `unsafe` prima di tutto è necessario usare il codice che accetta l'indirizzo di una variabile acquisita outer il `fixed` istruzione per correggere la variabile.

Si noti che a differenza di una variabile non acquisita, una variabile acquisita locale può essere esposti contemporaneamente a più thread di esecuzione.

#### <a name="instantiation-of-local-variables"></a>Creazione di un'istanza delle variabili locali

Una variabile locale è considerata ***creare un'istanza*** quando l'esecuzione passa all'ambito della variabile. Ad esempio, quando viene richiamato il metodo seguente, la variabile locale `x` viene creata un'istanza e inizializzato tre volte, una volta per ogni iterazione del ciclo.

```csharp
static void F() {
    for (int i = 0; i < 3; i++) {
        int x = i * 2 + 1;
        ...
    }
}
```

Tuttavia, se si sposta la dichiarazione di `x` all'esterno del ciclo, verrà creata una sola istanza di `x`:
```csharp
static void F() {
    int x;
    for (int i = 0; i < 3; i++) {
        x = i * 2 + 1;
        ...
    }
}
```

Quando non è stato acquisito, non è possibile osservare esattamente la frequenza con cui viene creata un'istanza di una variabile locale, poiché le durate delle creazioni di istanze sono disgiunte, è possibile per ogni istanza è sufficiente usare la stessa posizione di archiviazione. Tuttavia, quando una funzione anonima consente di acquisire una variabile locale, risultano evidenti gli effetti della creazione dell'istanza.

L'esempio
```csharp
using System;

delegate void D();

class Test
{
    static D[] F() {
        D[] result = new D[3];
        for (int i = 0; i < 3; i++) {
            int x = i * 2 + 1;
            result[i] = () => { Console.WriteLine(x); };
        }
        return result;
    }

    static void Main() {
        foreach (D d in F()) d();
    }
}
```
produce l'output:
```
1
3
5
```

Tuttavia, quando la dichiarazione di `x` viene spostato all'esterno del ciclo:
```csharp
static D[] F() {
    D[] result = new D[3];
    int x;
    for (int i = 0; i < 3; i++) {
        x = i * 2 + 1;
        result[i] = () => { Console.WriteLine(x); };
    }
    return result;
}
```
L'output è:
```
5
5
5
```

Se un ciclo for e dichiara una variabile di iterazione, la variabile stessa viene considerata dichiarato all'esterno del ciclo. Di conseguenza, se si modifica l'esempio per acquisire la variabile di iterazione stesso:

```csharp
static D[] F() {
    D[] result = new D[3];
    for (int i = 0; i < 3; i++) {
        result[i] = () => { Console.WriteLine(i); };
    }
    return result;
}
```
viene acquisita solo un'istanza della variabile di iterazione, che produce l'output:
```
3
3
3
```

È possibile che i delegati di funzione anonima condividere alcune variabili acquisite ancora privo di istanze separate di altri utenti. Ad esempio, se `F` viene modificata in
```csharp
static D[] F() {
    D[] result = new D[3];
    int x = 0;
    for (int i = 0; i < 3; i++) {
        int y = 0;
        result[i] = () => { Console.WriteLine("{0} {1}", ++x, ++y); };
    }
    return result;
}
```
i tre delegati acquisiranno la stessa istanza di `x` ma istanze separate del `y`, e l'output è:
```
1 1
2 1
3 1
```

Separare le funzioni anonime possono acquisire la stessa istanza di una variabile esterna. Nell’esempio
```csharp
using System;

delegate void Setter(int value);

delegate int Getter();

class Test
{
    static void Main() {
        int x = 0;
        Setter s = (int value) => { x = value; };
        Getter g = () => { return x; };
        s(5);
        Console.WriteLine(g());
        s(10);
        Console.WriteLine(g());
    }
}
```
le due funzioni anonime acquisiscono la stessa istanza della variabile locale `x`, e può quindi "comunicare" tramite la variabile. L'output dell'esempio è:
```
5
10
```

### <a name="evaluation-of-anonymous-function-expressions"></a>Valutazione delle espressioni di funzione anonima

Una funzione anonima `F` deve essere sempre convertita in un tipo delegato `D` o un tipo di albero delle espressioni `E`, direttamente o tramite l'esecuzione di un'espressione di creazione del delegato `new D(F)`. Questa conversione determina il risultato della funzione anonima, come descritto in [conversioni delle funzioni anonime](conversions.md#anonymous-function-conversions).

## <a name="query-expressions"></a>Espressioni di query

***Le espressioni di query*** offrono una sintassi integrata di linguaggio per le query che è simile ai linguaggi di query gerarchici e relazionali come SQL e XQuery.

```antlr
query_expression
    : from_clause query_body
    ;

from_clause
    : 'from' type? identifier 'in' expression
    ;

query_body
    : query_body_clauses? select_or_group_clause query_continuation?
    ;

query_body_clauses
    : query_body_clause
    | query_body_clauses query_body_clause
    ;

query_body_clause
    : from_clause
    | let_clause
    | where_clause
    | join_clause
    | join_into_clause
    | orderby_clause
    ;

let_clause
    : 'let' identifier '=' expression
    ;

where_clause
    : 'where' boolean_expression
    ;

join_clause
    : 'join' type? identifier 'in' expression 'on' expression 'equals' expression
    ;

join_into_clause
    : 'join' type? identifier 'in' expression 'on' expression 'equals' expression 'into' identifier
    ;

orderby_clause
    : 'orderby' orderings
    ;

orderings
    : ordering (',' ordering)*
    ;

ordering
    : expression ordering_direction?
    ;

ordering_direction
    : 'ascending'
    | 'descending'
    ;

select_or_group_clause
    : select_clause
    | group_clause
    ;

select_clause
    : 'select' expression
    ;

group_clause
    : 'group' expression 'by' expression
    ;

query_continuation
    : 'into' identifier query_body
    ;
```

Un'espressione di query inizia con un `from` clausola e termina con un `select` o `group` clausola. Iniziale `from` clausola può essere seguita da zero o più `from`, `let`, `where`, `join` o `orderby` clausole. Ciascun `from` clausola è un generatore di introduzione a un ***variabile di intervallo*** che include gli elementi di una ***sequenza***. Ogni `let` clausola introduce una variabile di intervallo che rappresenta un valore calcolato mediante variabili di intervallo precedente. Ogni `where` clausola è un filtro che escluda gli elementi dal risultato. Ogni `join` clausola WHERE che confronta le chiavi specificate della sequenza di origine con le chiavi di un'altra sequenza, producendo coppie corrispondenti. Ogni `orderby` clausola Riordina gli elementi in base ai criteri specificati. L'elemento finale `select` o `group` clausola specifica la forma del risultato in termini di variabili di intervallo. Infine, un `into` clausola può essere utilizzata da "inserire" query trattando i risultati di una query come un generatore in una query successiva.

### <a name="ambiguities-in-query-expressions"></a>Ambiguità nelle espressioni di query

Le espressioni di query contengono un numero di "parole chiave contestuali", ad esempio, gli identificatori che hanno un significato speciale in un contesto specificato. In particolare tratta `from`, `where`, `join`, `on`, `equals`, `into`, `let`, `orderby`, `ascending`, `descending`, `select`, `group` e `by`. Per evitare ambiguità in espressioni di query ha causate dall'utilizzo misto di questi identificatori come nomi semplici o le parole chiave, questi identificatori sono considerati le parole chiave quando si verificano in un punto qualsiasi all'interno di un'espressione di query.

A tale scopo, un'espressione di query è qualsiasi espressione che inizia con "`from identifier`"seguito da tutti i token ad eccezione di"`;`","`=`"o"`,`".

Per usare queste parole come identificatori all'interno di un'espressione di query, possono essere preceduti da "`@`" ([identificatori](lexical-structure.md#identifiers)).

### <a name="query-expression-translation"></a>Conversione dell'espressione di query

Il linguaggio c# non specifica la semantica di esecuzione delle espressioni di query. Piuttosto, le espressioni di query vengono convertite in chiamate dei metodi che rispettano il *criterio di espressione di query* ([il criterio di espressione di query](expressions.md#the-query-expression-pattern)). In particolare, le espressioni di query vengono convertite in chiamate di metodi denominati `Where`, `Select`, `SelectMany`, `Join`, `GroupJoin`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `GroupBy`, e `Cast`. Questi metodi devono avere firme particolare e tipi di risultati, come descritto in [il criterio di espressione di query](expressions.md#the-query-expression-pattern). Questi metodi possono essere metodi di istanza dell'oggetto sottoposto a query o i metodi di estensione che sono esterni all'oggetto, e implementare l'esecuzione effettiva della query.

La traduzione da espressioni di query per le chiamate ai metodi è un mapping sintattico che si verifica prima di qualsiasi tipo di binding o è stata eseguita la risoluzione dell'overload. La traduzione è garantito che sia sintatticamente corretta, ma non è garantito per produrre codice c# semanticamente corretto. In seguito alla conversione di espressioni di query, le chiamate al metodo risultante vengono elaborati come chiamate di metodo normale e ciò a sua volta potrebbe emergere errori, ad esempio, se non sono presenti i metodi, se gli argomenti hanno tipi non corretti o se i metodi sono generici e l'inferenza del tipo ha esito negativo.

Un'espressione di query viene elaborata tramite l'applicazione più volte le seguenti traduzioni fino a quando non sono possibili non ulteriori riduzioni. Le traduzioni sono elencate in ordine di applicazione: ogni sezione si presuppone che le traduzioni nelle sezioni precedenti sono state eseguite con attenzione e una volta esaurito, una sezione sarà non in un secondo momento è possibile accedere durante l'elaborazione dell'espressione di query stessa.

Assegnazione alle variabili di intervallo non è consentito nelle espressioni di query. Un'implementazione c# può tuttavia non impone questa limitazione, poiché ciò potrebbe talvolta non essere possibile con lo schema di conversione sintattica presentato in questo articolo.

Alcune conversioni inseriscono le variabili di intervallo con identificatori trasparenti indicati da `*`. Vengono illustrate le proprietà speciali degli identificatori trasparente ulteriormente in [trasparenti identificatori](expressions.md#transparent-identifiers).

#### <a name="select-and-groupby-clauses-with-continuations"></a>Clausole SELECT e groupby con le continuazioni

Un'espressione di query con una continuazione
```csharp
from ... into x ...
```
viene convertito in
```csharp
from x in ( from ... ) ...
```

Le traduzioni nelle sezioni seguenti si presuppongono che le query hanno no `into` le continuazioni.

L'esempio
```csharp
from c in customers
group c by c.Country into g
select new { Country = g.Key, CustCount = g.Count() }
```
viene convertito in
```csharp
from g in
    from c in customers
    group c by c.Country
select new { Country = g.Key, CustCount = g.Count() }
```
la traduzione finale di cui è
```csharp
customers.
GroupBy(c => c.Country).
Select(g => new { Country = g.Key, CustCount = g.Count() })
```

#### <a name="explicit-range-variable-types"></a>Tipi di variabile di intervallo esplicita

Oggetto `from` clausola che specifica in modo esplicito un tipo di variabile di intervallo
```csharp
from T x in e
```
viene convertito in
```csharp
from x in ( e ) . Cast < T > ( )
```

Oggetto `join` clausola che specifica in modo esplicito un tipo di variabile di intervallo
```
join T x in e on k1 equals k2
```
viene convertito in
```
join x in ( e ) . Cast < T > ( ) on k1 equals k2
```

Le traduzioni nelle sezioni seguenti presuppongono che le query non hanno nessun tipo di variabile di intervallo esplicita.

L'esempio
```csharp
from Customer c in customers
where c.City == "London"
select c
```
viene convertito in
```csharp
from c in customers.Cast<Customer>()
where c.City == "London"
select c
```
la traduzione finale di cui è
```csharp
customers.
Cast<Customer>().
Where(c => c.City == "London")
```

Tipi di variabile di intervallo espliciti sono utili per l'esecuzione di query le raccolte che implementano non generica `IEnumerable` interfaccia, ma non il tipo generico `IEnumerable<T>` interfaccia. Nell'esempio precedente, questo sarebbe il caso se `customers` erano di tipo `ArrayList`.

#### <a name="degenerate-query-expressions"></a>Espressioni di query degenere

Un'espressione di query del form
```csharp
from x in e select x
```
viene convertito in
```csharp
( e ) . Select ( x => x )
```

L'esempio
```csharp
from c in customers
select c
```
viene convertito in
```csharp
customers.Select(c => c)
```

Un'espressione di query degenere è quella più facilmente seleziona gli elementi dell'origine. In una fase successiva della traduzione rimuove degenere query inserite da altri passaggi di traduzione sostituendoli con relativa origine. È importante tuttavia per garantire che il risultato di una query espressione non è mai l'oggetto di origine stesso, che potrebbe rivelare il tipo e l'identità dell'origine per il client della query. Di conseguenza questo passaggio consente di proteggere degenere query scritte direttamente nel codice sorgente in modo esplicito chiamando `Select` nell'origine. Spetta poi gli implementatori di `Select` e altri operatori di query per garantire che questi metodi non restituiscono mai l'oggetto di origine stesso.

#### <a name="from-let-where-join-and-orderby-clauses"></a>Dal, let, where, clausole join e orderby

Un'espressione di query con una seconda `from` clausola seguita da un `select` clausola
```csharp
from x1 in e1
from x2 in e2
select v
```
viene convertito in
```csharp
( e1 ) . SelectMany( x1 => e2 , ( x1 , x2 ) => v )
```

Un'espressione di query con una seconda `from` clausola seguita da un elemento diverso da un `select` clausola:

```csharp
from x1 in e1
from x2 in e2
...
```
viene convertito in
```csharp
from * in ( e1 ) . SelectMany( x1 => e2 , ( x1 , x2 ) => new { x1 , x2 } )
...
```

Un'espressione di query con un `let` clausola
```csharp
from x in e
let y = f
...
```
viene convertito in
```csharp
from * in ( e ) . Select ( x => new { x , y = f } )
...
```

Un'espressione di query con un `where` clausola
```csharp
from x in e
where f
...
```
viene convertito in
```csharp
from x in ( e ) . Where ( x => f )
...
```

Un'espressione di query con un `join` clausola senza un' `into` seguita da un `select` clausola
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2
select v
```
viene convertito in
```csharp
( e1 ) . Join( e2 , x1 => k1 , x2 => k2 , ( x1 , x2 ) => v )
```

Un'espressione di query con un `join` clausola senza un' `into` seguita da un elemento diverso da un `select` clausola
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2
...
```
viene convertito in
```csharp
from * in ( e1 ) . Join( e2 , x1 => k1 , x2 => k2 , ( x1 , x2 ) => new { x1 , x2 })
...
```

Un'espressione di query con un `join` clausola con un `into` seguita da un `select` clausola
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2 into g
select v
```
viene convertito in
```csharp
( e1 ) . GroupJoin( e2 , x1 => k1 , x2 => k2 , ( x1 , g ) => v )
```

Un'espressione di query con un `join` clausola con un `into` seguita da un elemento diverso da un `select` clausola
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2 into g
...
```
viene convertito in
```csharp
from * in ( e1 ) . GroupJoin( e2 , x1 => k1 , x2 => k2 , ( x1 , g ) => new { x1 , g })
...
```

Un'espressione di query con un `orderby` clausola
```csharp
from x in e
orderby k1 , k2 , ..., kn
...
```
viene convertito in
```csharp
from x in ( e ) . 
OrderBy ( x => k1 ) . 
ThenBy ( x => k2 ) .
... .
ThenBy ( x => kn )
...
```

Se un ordinamento clausola che specifica un `descending` indicatori di direzione, una chiamata di `OrderByDescending` o `ThenByDescending` viene invece generata.

Per le conversioni seguenti presuppongono che non esistono nessun `let`, `where`, `join` oppure `orderby` clausole e non più di un iniziale `from` clausola in ogni espressione di query.

L'esempio
```csharp
from c in customers
from o in c.Orders
select new { c.Name, o.OrderID, o.Total }
```
viene convertito in
```csharp
customers.
SelectMany(c => c.Orders,
     (c,o) => new { c.Name, o.OrderID, o.Total }
)
```

L'esempio
```csharp
from c in customers
from o in c.Orders
orderby o.Total descending
select new { c.Name, o.OrderID, o.Total }
```
viene convertito in
```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.OrderID, o.Total }
```
la traduzione finale di cui è
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.OrderID, x.o.Total })
```
in cui `x` è un identificatore generato dal compilatore che è altrimenti invisibili e inaccessibili.

L'esempio
```csharp
from o in orders
let t = o.Details.Sum(d => d.UnitPrice * d.Quantity)
where t >= 1000
select new { o.OrderID, Total = t }
```
viene convertito in
```csharp
from * in orders.
    Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) })
where t >= 1000 
select new { o.OrderID, Total = t }
```
la traduzione finale di cui è
```csharp
orders.
Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) }).
Where(x => x.t >= 1000).
Select(x => new { x.o.OrderID, Total = x.t })
```
in cui `x` è un identificatore generato dal compilatore che è altrimenti invisibili e inaccessibili.

L'esempio
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
select new { c.Name, o.OrderDate, o.Total }
```
viene convertito in
```csharp
customers.Join(orders, c => c.CustomerID, o => o.CustomerID,
    (c, o) => new { c.Name, o.OrderDate, o.Total })
```

L'esempio
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID into co
let n = co.Count()
where n >= 10
select new { c.Name, OrderCount = n }
```
viene convertito in
```csharp
from * in customers.
    GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
        (c, co) => new { c, co })
let n = co.Count()
where n >= 10 
select new { c.Name, OrderCount = n }
```
la traduzione finale di cui è
```csharp
customers.
GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
    (c, co) => new { c, co }).
Select(x => new { x, n = x.co.Count() }).
Where(y => y.n >= 10).
Select(y => new { y.x.c.Name, OrderCount = y.n)
```
in cui `x` e `y` sono identificatori generato dal compilatore che altrimenti risultano invisibile e non accessibili.

L'esempio
```csharp
from o in orders
orderby o.Customer.Name, o.Total descending
select o
```
la conversione finale è
```csharp
orders.
OrderBy(o => o.Customer.Name).
ThenByDescending(o => o.Total)
```

#### <a name="select-clauses"></a>Clausole SELECT

Un'espressione di query del form
```csharp
from x in e select v
```
viene convertito in
```csharp
( e ) . Select ( x => v )
```
tranne quando v è l'identificatore di x, la conversione è semplicemente
```csharp
( e )
```

Esempio:
```csharp
from c in customers.Where(c => c.City == "London")
select c
```
viene convertito semplicemente in
```csharp
customers.Where(c => c.City == "London")
```

#### <a name="groupby-clauses"></a>Clausole di GroupBy

Un'espressione di query del form
```csharp
from x in e group v by k
```
viene convertito in
```csharp
( e ) . GroupBy ( x => k , x => v )
```
tranne quando v è l'identificatore è la traduzione di x,
```csharp
( e ) . GroupBy ( x => k )
```

L'esempio
```csharp
from c in customers
group c.Name by c.Country
```
viene convertito in
```csharp
customers.
GroupBy(c => c.Country, c => c.Name)
```

#### <a name="transparent-identifiers"></a>Identificatori trasparenti

Alcune conversioni inseriscono le variabili di intervallo con ***identificatori trasparenti*** indicato da `*`. Identificatori trasparenti non sono una funzionalità del linguaggio appropriata; Esistono solo come passaggio intermedio nel processo di conversione di espressione di query.

Quando una conversione di query inserisce un identificatore trasparente, ulteriormente i passaggi di traduzione propagano l'identificatore trasparente in funzioni anonime e inizializzatori di oggetti anonimi. In questi contesti, identificatori trasparenti comportano nel modo seguente:

*  Quando si verifica un identificatore trasparente come parametro in una funzione anonima, i membri del tipo anonimo associato vengono automaticamente nell'ambito del corpo della funzione anonima.
*  Quando un membro con un identificatore trasparente è nell'ambito, i membri di tale membro rientrano nell'ambito anche.
*  Quando si verifica un identificatore trasparente come un dichiaratore di membro in un inizializzatore di oggetto anonimi, introduce un membro con un identificatore trasparente.
*  Nei passaggi della conversione descritti in precedenza, gli identificatori trasparenti vengono sempre introdotte insieme ai tipi anonimi, con l'intenzione di acquisire più variabili di intervallo come membri di un singolo oggetto. Un'implementazione del linguaggio c# può utilizzare un meccanismo diverso rispetto ai tipi anonimi per raggruppare più variabili di intervallo. Gli esempi di conversione seguenti presuppongono che vengono usati tipi anonimi e mostrano come trasparenti identificatori possono essere convertiti da subito.

L'esempio
```csharp
from c in customers
from o in c.Orders
orderby o.Total descending
select new { c.Name, o.Total }
```
viene convertito in
```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.Total }
```

che viene ulteriormente tradotti
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(* => o.Total).
Select(* => new { c.Name, o.Total })
```
che, quando vengono cancellati gli identificatori trasparenti, equivale a
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.Total })
```
in cui `x` è un identificatore generato dal compilatore che è altrimenti invisibili e inaccessibili.

L'esempio
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```
viene convertito in
```csharp
from * in customers.
    Join(orders, c => c.CustomerID, o => o.CustomerID, 
        (c, o) => new { c, o })
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```
che viene ulteriormente ridotta a
```csharp
customers.
Join(orders, c => c.CustomerID, o => o.CustomerID, (c, o) => new { c, o }).
Join(details, * => o.OrderID, d => d.OrderID, (*, d) => new { *, d }).
Join(products, * => d.ProductID, p => p.ProductID, (*, p) => new { *, p }).
Select(* => new { c.Name, o.OrderDate, p.ProductName })
```
la traduzione finale di cui è
```csharp
customers.
Join(orders, c => c.CustomerID, o => o.CustomerID,
    (c, o) => new { c, o }).
Join(details, x => x.o.OrderID, d => d.OrderID,
    (x, d) => new { x, d }).
Join(products, y => y.d.ProductID, p => p.ProductID,
    (y, p) => new { y, p }).
Select(z => new { z.y.x.c.Name, z.y.x.o.OrderDate, z.p.ProductName })
```
in cui `x`, `y`, e `z` sono identificatori generato dal compilatore che altrimenti risultano invisibile e non accessibili.

### <a name="the-query-expression-pattern"></a>Il criterio di espressione di query

Il ***criterio di espressione di Query*** stabilisce una serie di metodi che i tipi possono implementare per supportare le espressioni di query. Poiché le espressioni di query vengono convertite in chiamate al metodo tramite un mapping sintattico, i tipi includono una notevole flessibilità nella modalità di implementazione del modello di espressione di query. Ad esempio, i metodi del modello possono essere implementati come metodi di istanza o come metodi di estensione perché i due hanno la stessa sintassi di chiamata e i metodi possono richiedere delegati o alberi delle espressioni perché sono convertibili in entrambe le funzioni anonime.

La forma consigliata di un tipo generico `C<T>` che supporta il modello di espressione di query è illustrato di seguito. Un tipo generico viene usato per illustrare le relazioni appropriate tra i tipi di parametro e il risultato, ma è possibile implementare il modello per nonché i tipi non generici.

```csharp
delegate R Func<T1,R>(T1 arg1);

delegate R Func<T1,T2,R>(T1 arg1, T2 arg2);

class C
{
    public C<T> Cast<T>();
}

class C<T> : C
{
    public C<T> Where(Func<T,bool> predicate);

    public C<U> Select<U>(Func<T,U> selector);

    public C<V> SelectMany<U,V>(Func<T,C<U>> selector,
        Func<T,U,V> resultSelector);

    public C<V> Join<U,K,V>(C<U> inner, Func<T,K> outerKeySelector,
        Func<U,K> innerKeySelector, Func<T,U,V> resultSelector);

    public C<V> GroupJoin<U,K,V>(C<U> inner, Func<T,K> outerKeySelector,
        Func<U,K> innerKeySelector, Func<T,C<U>,V> resultSelector);

    public O<T> OrderBy<K>(Func<T,K> keySelector);

    public O<T> OrderByDescending<K>(Func<T,K> keySelector);

    public C<G<K,T>> GroupBy<K>(Func<T,K> keySelector);

    public C<G<K,E>> GroupBy<K,E>(Func<T,K> keySelector,
        Func<T,E> elementSelector);
}

class O<T> : C<T>
{
    public O<T> ThenBy<K>(Func<T,K> keySelector);

    public O<T> ThenByDescending<K>(Func<T,K> keySelector);
}

class G<K,T> : C<T>
{
    public K Key { get; }
}
```

I metodi precedenti usano i tipi delegati generici `Func<T1,R>` e `Func<T1,T2,R>`, ma è stato altrettanto bene avere usati altri tipi di struttura ad albero di delegato o un'espressione con le stesse relazioni nei tipi di parametro e risultato.

Si noti che la relazione tra consigliata `C<T>` e `O<T>` che assicura che le `ThenBy` e `ThenByDescending` metodi sono disponibili solo sul risultato di un' `OrderBy` o `OrderByDescending`. Si noti inoltre la forma del risultato di consigliata `GroupBy` : una sequenza di sequenze, in cui ogni sequenza interna ha un'ulteriore `Key` proprietà.

Il `System.Linq` dello spazio dei nomi fornisce un'implementazione del modello di operatore di query per qualsiasi tipo che implementa il `System.Collections.Generic.IEnumerable<T>` interfaccia.

## <a name="assignment-operators"></a>Operatori di assegnazione

Gli operatori di assegnazione assegnano un nuovo valore a una variabile, una proprietà, un evento o un elemento dell'indicizzatore.

```antlr
assignment
    : unary_expression assignment_operator expression
    ;

assignment_operator
    : '='
    | '+='
    | '-='
    | '*='
    | '/='
    | '%='
    | '&='
    | '|='
    | '^='
    | '<<='
    | right_shift_assignment
    ;
```

L'operando sinistro di un'assegnazione deve essere un'espressione classificata come una variabile, un accesso a proprietà, un accesso all'indicizzatore o accesso a un evento.

Il `=` viene chiamato operatore le ***operatore di assegnazione semplice***. Assegna il valore dell'operando di destra alla variabile, proprietà o indicizzatore all'elemento rappresentato dall'operando sinistro. L'operando sinistro dell'operatore di assegnazione semplice potrebbe non essere l'accesso a un evento (ad eccezione di quanto descritto nella [di eventi campo](classes.md#field-like-events)). L'operatore di assegnazione semplice è descritto in [assegnamento semplice](expressions.md#simple-assignment).

Gli operatori di assegnazione diverso dal `=` operatore vengono chiamati i ***operatori di assegnazione composta***. Questi operatori eseguono l'operazione indicata su due operandi e quindi assegnare il valore risultante alla variabile, proprietà o indicizzatore all'elemento rappresentato dall'operando sinistro. Operatori di assegnamento composti sono descritte nel [assegnazione composta](expressions.md#compound-assignment).

Il `+=` e `-=` operatori con un'espressione di accesso eventi come operando di sinistra vengono chiamati i *gli operatori di assegnazione di evento*. Nessun altro operatore di assegnazione è valido con accesso a un evento come operando di sinistra. Gli operatori di assegnazione di eventi sono descritti [assegnazione di eventi](expressions.md#event-assignment).

Gli operatori di assegnazione sono associativa a destra, ovvero le operazioni vengono eseguite da destra a sinistra. Ad esempio, un'espressione nel formato `a = b = c` darà `a = (b = c)`.

### <a name="simple-assignment"></a>Assegnazione singola

Il `=` operatore viene chiamato operatore di assegnazione semplice.

Se l'operando sinistro di un'assegnazione semplice è nel formato `E.P` oppure `E[Ei]` in cui `E` dispone del tipo in fase di compilazione `dynamic`, quindi l'assegnazione è associata in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)). In questo caso è il tipo di fase di compilazione dell'espressione di assegnazione `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione in base al tipo di runtime di `E`.

In un'assegnazione semplice, l'operando destro deve essere un'espressione che è implicitamente convertibile nel tipo dell'operando sinistro. L'operazione assegna il valore dell'operando di destra alla variabile, proprietà o indicizzatore all'elemento rappresentato dall'operando sinistro.

Il risultato di un'espressione di assegnazione semplice è il valore assegnato all'operando di sinistra. Il risultato ha lo stesso tipo dell'operando di sinistra ed è sempre classificato come un valore.

Se l'operando sinistro è un accesso alla proprietà o indicizzatore, la proprietà o l'indicizzatore deve avere un `set` della funzione di accesso. Se ciò non avviene, si verifica un errore in fase di associazione.

L'elaborazione in fase di esecuzione di un'assegnazione semplice nel formato `x = y` prevede i passaggi seguenti:

*  Se `x` viene classificato come una variabile:
   * `x` viene valutata per produrre la variabile.
   * `y` viene valutata e, se necessario, convertito nel tipo della `x` tramite una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)).
   * Se la variabile rappresentata da `x` è un elemento di un *reference_type*, viene eseguito un controllo in fase di esecuzione per garantire che il valore calcolato per `y` è compatibile con l'istanza di matrice di cui `x` è un elemento. La verifica ha esito positivo se `y` viene `null`, o se una conversione implicita del riferimento ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)) esistente dal tipo effettivo dell'istanza a cui fanno riferimento `y` al tipo di elemento effettivo istanza della matrice contenente `x`. In caso contrario viene generata un'eccezione `System.ArrayTypeMismatchException`.
   * Il valore risultante dalla valutazione e la conversione dei `y` viene archiviato nella posizione specificata dalla valutazione di `x`.
*  Se `x` viene classificato come accesso alla proprietà o l'indicizzatore:
   * L'espressione dell'istanza (se `x` non è `static`) e l'elenco di argomenti (se `x` un accesso dell'indicizzatore) associato `x` vengono valutate, e i risultati vengono usati nella successiva `set` chiamata di funzione di accesso.
   * `y` viene valutata e, se necessario, convertito nel tipo della `x` tramite una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)).
   * Il `set` funzione di accesso di `x` viene richiamato con il valore calcolato per `y` come relativo `value` argomento.

Le regole di CO-varianza matrice ([vovariante](arrays.md#array-covariance)) consentono di un valore di tipo matrice `A[]` come un riferimento a un'istanza di un tipo di matrice `B[]`, a condizione che esiste una conversione implicita del riferimento da `B` per `A`. A causa di queste regole, l'assegnazione a un elemento di un *reference_type* richiede un controllo in fase di esecuzione per garantire che il valore assegnato è compatibile con l'istanza di matrice. Nell'esempio
```csharp
string[] sa = new string[10];
object[] oa = sa;

oa[0] = null;               // Ok
oa[1] = "Hello";            // Ok
oa[2] = new ArrayList();    // ArrayTypeMismatchException
```
fa sì che l'ultima assegnazione di un `System.ArrayTypeMismatchException` viene generata perché un'istanza di `ArrayList` non possono essere archiviati in un elemento di un `string[]`.

Quando una proprietà o indicizzatore dichiarato un *struct_type* è la destinazione di un'assegnazione, l'espressione dell'istanza associata alla proprietà o l'accesso all'indicizzatore deve essere classificata come una variabile. Se l'espressione dell'istanza è classificato come un valore, si verifica un errore in fase di associazione. Because of [l'accesso ai membri](expressions.md#member-access), la stessa regola si applica anche ai campi.

Date le dichiarazioni:
```csharp
struct Point
{
    int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int X {
        get { return x; }
        set { x = value; }
    }

    public int Y {
        get { return y; }
        set { y = value; }
    }
}

struct Rectangle
{
    Point a, b;

    public Rectangle(Point a, Point b) {
        this.a = a;
        this.b = b;
    }

    public Point A {
        get { return a; }
        set { a = value; }
    }

    public Point B {
        get { return b; }
        set { b = value; }
    }
}
```
Nell'esempio
```csharp
Point p = new Point();
p.X = 100;
p.Y = 100;
Rectangle r = new Rectangle();
r.A = new Point(10, 10);
r.B = p;
```
le assegnazioni per `p.X`, `p.Y`, `r.A`, e `r.B` sono consentite perché `p` e `r` sono variabili. Tuttavia, nell'esempio
```csharp
Rectangle r = new Rectangle();
r.A.X = 10;
r.A.Y = 10;
r.B.X = 100;
r.B.Y = 100;
```
le assegnazioni sono tutti non valide, poiché `r.A` e `r.B` non sono variabili.

### <a name="compound-assignment"></a>Assegnazione composta

Se l'operando sinistro di un'assegnazione composta è nel formato `E.P` oppure `E[Ei]` in cui `E` dispone del tipo in fase di compilazione `dynamic`, quindi l'assegnazione è associata in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)). In questo caso è il tipo di fase di compilazione dell'espressione di assegnazione `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione in base al tipo di runtime di `E`.

Un'operazione del form `x op= y` avviene per la risoluzione dell'overload di operatore binario ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) come se l'operazione è stata scritta `x op y`. Quindi,

*  Se il tipo restituito dell'operatore selezionato è implicitamente convertibile nel tipo della `x`, l'operazione viene valutato come `x = x op y`, ad eccezione del fatto che `x` viene valutata una sola volta.
*  In caso contrario, se l'operatore selezionato è già definito, se il tipo restituito dell'operatore selezionato è convertibile in modo esplicito al tipo di `x`e se `y` è implicitamente convertibile nel tipo di `x` o l'operatore è un operatore, quindi l'operazione viene valutato come Shift `x = (T)(x op y)`, dove `T` è il tipo di `x`, ad eccezione del fatto che `x` viene valutata una sola volta.
*  In caso contrario, l'assegnazione composta non è valido e si verifica un errore in fase di associazione.

Il termine "valutato una sola volta" indica che nella valutazione della `x op y`, i risultati di eventuali espressioni che costituiscono dei `x` sono temporaneamente salvati e riutilizzati quando si esegue l'assegnazione al `x`. Ad esempio, nell'assegnazione `A()[B()] += C()`, dove `A` è un metodo di restituzione `int[]`, e `B` e `C` sono i metodi che restituiscono `int`, i metodi vengono richiamati solo una volta, nell'ordine `A`, `B`, `C`.

Quando l'operando sinistro di un'assegnazione composta è un accesso a proprietà o l'accesso dell'indicizzatore, la proprietà o l'indicizzatore deve avere entrambe un `get` funzione di accesso e un `set` della funzione di accesso. Se ciò non avviene, si verifica un errore in fase di associazione.

La seconda regola sopra permessi `x op= y` deve essere valutata come `x = (T)(x op y)` in determinati contesti. La regola esiste in modo che gli operatori predefiniti utilizzabili come gli operatori composti quando l'operando sinistro è di tipo `sbyte`, `byte`, `short`, `ushort`, o `char`. Anche se entrambi gli argomenti sono di uno di questi tipi, gli operatori predefiniti producono un risultato di tipo `int`, come descritto in [Promozioni numeriche binarie](expressions.md#binary-numeric-promotions). In questo modo, senza un cast non sarebbe possibile assegnare il risultato all'operando di sinistra.

L'effetto della regola per gli operatori predefiniti intuitivo consiste semplicemente `x op= y` è consentita solo se entrambi dei `x op y` e `x = y` sono consentite. Nell'esempio
```csharp
byte b = 0;
char ch = '\0';
int i = 0;

b += 1;             // Ok
b += 1000;          // Error, b = 1000 not permitted
b += i;             // Error, b = i not permitted
b += (byte)i;       // Ok

ch += 1;            // Error, ch = 1 not permitted
ch += (char)1;      // Ok
```
il motivo intuitivo per ogni errore è che una semplice assegnazione corrispondente sarebbe anche stato un errore.

Questo significa anche che supportano operazioni di assegnazione composta operazioni è elevato. Nell'esempio
```csharp
int? i = 0;
i += 1;             // Ok
```
l'operatore lift `+(int?,int?)` viene usato.

### <a name="event-assignment"></a>Assegnazione di eventi

Se l'operando sinistro di un `+=` o `-=` operatore viene classificato come accesso a un evento, quindi l'espressione viene valutata nel modo seguente:

*  L'espressione dell'istanza, se presente, dell'accesso all'evento viene valutata.
*  L'operando destro del `+=` oppure `-=` operatore viene valutato e, se necessario, convertito nel tipo dell'operando sinistro tramite una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)).
*  Una funzione di accesso di evento dell'evento viene richiamato, tramite l'elenco di argomenti costituito l'operando di destra, dopo la valutazione e, se necessario, la conversione. Se l'operatore è stata `+=`, il `add` viene richiamata la funzione di accesso; se l'operatore è stato `-=`, il `remove` viene richiamata una funzione di accesso.

Un'espressione di assegnazione di eventi non restituisce un valore. Di conseguenza, un'espressione di assegnazione di evento è valida solo nel contesto di un *statement_expression* ([le istruzioni di espressione](statements.md#expression-statements)).

## <a name="expression"></a>Espressione

Un' *espressione* può essere una *non_assignment_expression* o un *assegnazione*.

```antlr
expression
    : non_assignment_expression
    | assignment
    ;

non_assignment_expression
    : conditional_expression
    | lambda_expression
    | query_expression
    ;
```

## <a name="constant-expressions"></a>Espressioni costanti

Oggetto *constant_expression* è un'espressione che può essere valutata interamente in fase di compilazione.

```antlr
constant_expression
    : expression
    ;
```

Un'espressione costante deve essere il `null` valore letterale o con uno dei seguenti tipi: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char` , `float`, `double`, `decimal`, `bool`, `object`, `string`, o qualsiasi tipo di enumerazione. Solo i seguenti costrutti sono consentiti nelle espressioni costanti:

*  Valori letterali (incluso il `null` letterale).
*  I riferimenti a `const` i membri dei tipi di classi e struct.
*  Riferimenti ai membri dei tipi di enumerazione.
*  I riferimenti a `const` parametri o variabili locali
*  Tra parentesi le sottoespressioni, che sono essi stessi espressioni costanti.
*  Espressioni cast, purché il tipo di destinazione è uno dei tipi elencati in precedenza.
*  `checked` e `unchecked` espressioni
*  Espressioni con valore predefinito
*  Espressioni Nameof
*  Oggetto predefinito `+`, `-`, `!`, e `~` operatori unari.
*  Oggetto predefinito `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `&`, `|`, `^`, `&&`, `||`, `==`, `!=`, `<`, `>`, `<=`, e `>=` gli operatori binari, fornito ogni operando è di tipo elencato in precedenza.
*  Il `?:` operatore condizionale.

Nelle espressioni costanti sono consentite le conversioni seguenti:

*  Conversioni di identità
*  Conversioni numeriche
*  Conversioni enumerazione
*  Conversioni di espressioni costanti
*  Conversioni dei riferimenti impliciti ed espliciti, a condizione che l'origine delle conversioni sia un'espressione costante che restituisce il valore null.

Altre conversioni tra cui la conversione boxing, le conversioni dei riferimenti implicita e conversione unboxing di valori non null non sono consentite nelle espressioni costanti. Ad esempio:
```csharp
class C {
    const object i = 5;         // error: boxing conversion not permitted
    const object str = "hello"; // error: implicit reference conversion
}
```
l'inizializzazione dei è un errore perché non è necessaria una conversione boxing. L'inizializzazione di str è un errore perché è necessaria una conversione implicita del riferimento da un valore diverso da null.

Ogni volta che un'espressione di cui soddisfa i requisiti elencati in precedenza, l'espressione viene valutata in fase di compilazione. Questo vale anche se l'espressione è una sottoespressione di un'espressione più ampia che contiene costrutti non costante.

La valutazione in fase di compilazione di espressioni costanti Usa le stesse regole come versione di valutazione in fase di esecuzione delle espressioni non costanti, ad eccezione del fatto che in fase di esecuzione valutazione avrebbe generato un'eccezione, la valutazione in fase di compilazione genera un errore in fase di compilazione si verifichi.

A meno che un'espressione costante viene inserita in modo esplicito un `unchecked` contesto, overflow che si verificano nelle conversioni e operazioni aritmetiche di tipo integrale durante la valutazione in fase di compilazione dell'espressione sempre causano errori in fase di compilazione ([Le espressioni costanti](expressions.md#constant-expressions)).

Espressioni costanti verificano nei contesti elencati di seguito. In questi contesti, si verifica un errore in fase di compilazione se un'espressione non può essere valutata interamente in fase di compilazione.

*  Le dichiarazioni di costante ([costanti](classes.md#constants)).
*  Dichiarazioni di membro di enumerazione ([membri Enum](enums.md#enum-members)).
*  Gli argomenti di elenchi di parametri formali predefiniti ([parametri di metodo](classes.md#method-parameters))
*  `case` le etichette di un `switch` istruzione ([dell'istruzione switch](statements.md#the-switch-statement)).
*  `goto case` istruzioni ([l'istruzione goto](statements.md#the-goto-statement)).
*  Lunghezze delle dimensioni in un'espressione di creazione di matrici ([espressioni di creazione matrice](expressions.md#array-creation-expressions)) che include un inizializzatore.
*  Attributi ([attributi](attributes.md)).

Una conversione implicita espressione costante ([conversioni implicite di espressioni costanti](conversions.md#implicit-constant-expression-conversions)) consente a un'espressione costante di tipo `int` da convertire in `sbyte`, `byte`, `short`, `ushort`, `uint`, o `ulong`, a condizione che il valore dell'espressione costante è compreso nell'intervallo del tipo di destinazione.

## <a name="boolean-expressions"></a>espressioni booleane

Oggetto *boolean_expression* è un'espressione che produce un risultato di tipo `bool`; entrambi direttamente o tramite l'applicazione di `operator true` in determinati contesti come specificato nell'esempio seguente.

```antlr
boolean_expression
    : expression
    ;
```

L'espressione condizionale controllo di un' *if_statement* ([if istruzione](statements.md#the-if-statement)), *while_statement* ([l'istruzione while](statements.md#the-while-statement)), *do_statement* ([istruzione do](statements.md#the-do-statement)), o *for_statement* ([l'istruzione for](statements.md#the-for-statement)) è un *boolean_ espressione*. Il controllo dell'espressione condizionale del `?:` operatore ([operatore condizionale](expressions.md#conditional-operator)) segue le stesse regole una *boolean_expression*, ma ai fini dell'operatore di precedenza viene classificata come un *conditional_or_expression*.

Oggetto *boolean_expression* `E` deve essere in grado di produrre un valore di tipo `bool`, come indicato di seguito:

*  Se `E` è implicitamente convertibile in `bool` in fase di esecuzione viene applicata tale conversione implicita.
*  In caso contrario, la risoluzione dell'overload di operatore unario ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution)) viene usato per trovare un'implementazione univoca migliore dell'operatore `true` su `E`, e che l'implementazione viene applicata in fase di esecuzione.
*  Se non viene trovato alcun operatore di questo tipo, si verifica un errore in fase di associazione.

Il `DBBool` tipo struct in [Database di tipo booleano](structs.md#database-boolean-type) viene fornito un esempio di un tipo che implementa `operator true` e `operator false`.
