---
ms.openlocfilehash: f61039abd6bd557ac0ea625e6aac1c8bafa57b02
ms.sourcegitcommit: 892af9016b3317a8fae12d195014dc38ba51cf16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71704080"
---
# <a name="expressions"></a>Espressioni

Un'espressione è una sequenza di operatori e operandi. Questo capitolo definisce la sintassi, l'ordine di valutazione degli operandi e degli operatori e il significato delle espressioni.

## <a name="expression-classifications"></a>Classificazioni di espressioni

Un'espressione viene classificata nei seguenti modi:

*  Un valore. Ad ogni valore è associato un tipo.
*  Variabile. A ogni variabile è associato un tipo, ovvero il tipo dichiarato della variabile.
*  Spazio dei nomi. Un'espressione con questa classificazione può essere visualizzata solo come parte sinistra di un *member_access* ([accesso ai membri](expressions.md#member-access)). In qualsiasi altro contesto, un'espressione classificata come uno spazio dei nomi causa un errore in fase di compilazione.
*  Tipo. Un'espressione con questa classificazione può essere visualizzata solo come parte sinistra di un *member_access* ([accesso ai membri](expressions.md#member-access)) o come operando per l'operatore `as` ([operatore as](expressions.md#the-as-operator)), l'operatore `is` ([l'operatore is](expressions.md#the-is-operator)) o il @no operatore __t-6 ([operatore typeof](expressions.md#the-typeof-operator)). In qualsiasi altro contesto, un'espressione classificata come tipo causa un errore in fase di compilazione.
*  Un gruppo di metodi, ovvero un set di metodi di overload risultante da una ricerca di membri ([ricerca di membri](expressions.md#member-lookup)). A un gruppo di metodi può essere associata un'espressione di istanza e un elenco di argomenti di tipo associato. Quando viene richiamato un metodo di istanza, il risultato della valutazione dell'espressione di istanza diventa l'istanza rappresentata da `this` ([questo accesso](expressions.md#this-access)). Un gruppo di metodi è consentito in un *invocation_expression* ([espressioni di chiamata](expressions.md#invocation-expressions)), un *delegate_creation_expression* (espressioni di[creazione di delegati](expressions.md#delegate-creation-expressions)) e come parte sinistra di un operatore is e può essere implicitamente convertito in un tipo delegato compatibile ([conversioni di gruppi di metodi](conversions.md#method-group-conversions)). In qualsiasi altro contesto, un'espressione classificata come un gruppo di metodi causa un errore in fase di compilazione.
*  Valore letterale null. Un'espressione con questa classificazione può essere convertita in modo implicito in un tipo di riferimento o un tipo Nullable.
*  Funzione anonima. Un'espressione con questa classificazione può essere convertita in modo implicito in un tipo delegato o un tipo di albero delle espressioni compatibile.
*  Accesso A una proprietà. A ogni accesso alle proprietà è associato un tipo, ovvero il tipo della proprietà. Un accesso a proprietà può inoltre avere un'espressione di istanza associata. Quando viene richiamata una funzione di accesso (blocco `get` o `set`) di un accesso alla proprietà dell'istanza, il risultato della valutazione dell'espressione di istanza diventa l'istanza rappresentata da `this` ([questo accesso](expressions.md#this-access)).
*  Accesso a un evento. A ogni accesso agli eventi è associato un tipo, ovvero il tipo dell'evento. Inoltre, è possibile che a un accesso a un evento sia associata un'espressione di istanza. È possibile che l'accesso agli eventi appaia come operando sinistro degli operatori `+=` e `-=` ([assegnazione evento](expressions.md#event-assignment)). In qualsiasi altro contesto, un'espressione classificata come accesso agli eventi causa un errore in fase di compilazione.
*  Accesso dell'indicizzatore. A ogni accesso dell'indicizzatore è associato un tipo, ovvero il tipo di elemento dell'indicizzatore. Inoltre, un accesso all'indicizzatore ha un'espressione di istanza associata e un elenco di argomenti associato. Quando viene richiamata una funzione di accesso, ovvero il blocco `get` o `set`, di un indicizzatore, il risultato della valutazione dell'espressione di istanza diventa l'istanza rappresentata da `this` ([questo accesso](expressions.md#this-access)) e il risultato della valutazione dell'elenco di argomenti diventa elenco di parametri della chiamata.
*  Niente. Questo errore si verifica quando l'espressione è una chiamata di un metodo con un tipo restituito `void`. Un'espressione classificata come Nothing è valida solo nel contesto di un *statement_expression* ([istruzioni Expression](statements.md#expression-statements)).

Il risultato finale di un'espressione non è mai uno spazio dei nomi, un tipo, un gruppo di metodi o l'accesso agli eventi. Piuttosto, come indicato in precedenza, queste categorie di espressioni sono costrutti intermedi che sono consentiti solo in determinati contesti.

Un accesso a una proprietà o un indicizzatore viene sempre riclassificato come valore eseguendo una chiamata della funzione di accesso *Get* o della *funzione*di accesso set. La funzione di accesso specifica è determinata dal contesto dell'accesso alla proprietà o all'indicizzatore: Se l'accesso è la destinazione di un'assegnazione, viene richiamata la funzione di accesso *set* per assegnare un nuovo valore ([assegnazione semplice](expressions.md#simple-assignment)). In caso contrario, viene richiamata la *funzione di accesso get* per ottenere il valore corrente ([valori di espressioni](expressions.md#values-of-expressions)).

### <a name="values-of-expressions"></a>Valori delle espressioni

Per la maggior parte dei costrutti che coinvolgono un'espressione, è necessario che l'espressione denoti un ***valore***. In questi casi, se l'espressione effettiva indica uno spazio dei nomi, un tipo, un gruppo di metodi o Nothing, si verifica un errore in fase di compilazione. Tuttavia, se l'espressione denota un accesso a una proprietà, un accesso all'indicizzatore o una variabile, il valore della proprietà, dell'indicizzatore o della variabile viene sostituito in modo implicito:

*  Il valore di una variabile è semplicemente il valore attualmente archiviato nella posizione di archiviazione identificata dalla variabile. Una variabile deve essere considerata definitivamente assegnata ([assegnazione definita](variables.md#definite-assignment)) prima di poter ottenere il relativo valore o in caso contrario si verifica un errore in fase di compilazione.
*  Il valore di un'espressione di accesso alla proprietà viene ottenuto richiamando la funzione di accesso *Get* della proprietà. Se la proprietà non dispone di una *funzione di accesso get*, si verifica un errore in fase di compilazione. In caso contrario, viene eseguita una chiamata del membro della funzione ([controllo della fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) e il risultato della chiamata diventa il valore dell'espressione di accesso alla proprietà.
*  Il valore di un'espressione di accesso dell'indicizzatore viene ottenuto richiamando la funzione di accesso *Get* dell'indicizzatore. Se l'indicizzatore non dispone di una *funzione di accesso get*, si verifica un errore in fase di compilazione. In caso contrario, la chiamata di un membro di funzione ([controllo della fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) viene eseguita con l'elenco di argomenti associato all'espressione di accesso dell'indicizzatore e il risultato della chiamata diventa il valore dell'accesso dell'indicizzatore. espressione.

## <a name="static-and-dynamic-binding"></a>Associazione statica e dinamica

Il processo di determinazione del significato di un'operazione basata sul tipo o sul valore delle espressioni costituenti (argomenti, operandi e destinatari) viene spesso definito ***Binding***. Ad esempio, il significato di una chiamata al metodo viene determinato in base al tipo del destinatario e degli argomenti. Il significato di un operatore viene determinato in base al tipo degli operandi.

Il C# significato di un'operazione viene in genere determinato in fase di compilazione, in base al tipo in fase di compilazione delle espressioni costituenti. Analogamente, se un'espressione contiene un errore, l'errore viene rilevato e segnalato dal compilatore. Questo approccio è noto come ***associazione statica***.

Tuttavia, se un'espressione è un'espressione dinamica, ad esempio il tipo `dynamic`, questo indica che qualsiasi associazione di cui fa parte deve essere basata sul tipo in fase di esecuzione (ovvero il tipo effettivo dell'oggetto che denota in fase di esecuzione) anziché il tipo a cui è associato in fase di compilazione. Il binding di tale operazione viene quindi rinviato fino al momento in cui l'operazione deve essere eseguita durante l'esecuzione del programma. Questa operazione viene definita ***associazione dinamica***.

Quando un'operazione è associata dinamicamente, il compilatore esegue il controllo minimo o no. In alternativa, se l'associazione in fase di esecuzione ha esito negativo, gli errori vengono segnalati come eccezioni in fase di esecuzione.

Le operazioni seguenti in C# sono soggette all'associazione:

*  Accesso ai membri: `e.M`
*  Chiamata al metodo: `e.M(e1, ..., eN)`
*  Chiamata al delegato: `e(e1, ..., eN)`
*  Accesso agli elementi: `e[e1, ..., eN]`
*  Creazione oggetto: `new C(e1, ..., eN)`
*  Operatori unari di overload: `+`, `-`, `!`, `~`, `++`, `--`, `true`, `false`
*  Operatori binari di overload: `+`, `-`, `*`, `/`, `%`, `&`, `&&`, `|`, `||`, `??`, 0, 1, 2, 3, 4, 5, 6, 7, 8
*  Operatori di assegnazione: `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|=`, `^=`, `<<=`, 0
*  Conversioni implicite ed esplicite

Quando non sono implicate espressioni dinamiche C# , il valore predefinito è l'associazione statica, il che significa che i tipi in fase di compilazione delle espressioni costituenti vengono utilizzati nel processo di selezione. Tuttavia, quando una delle espressioni costitutive nelle operazioni elencate in precedenza è un'espressione dinamica, l'operazione viene associata in modo dinamico.

### <a name="binding-time"></a>Tempo di binding

L'associazione statica viene eseguita in fase di compilazione, mentre l'associazione dinamica avviene in fase di esecuzione. Nelle sezioni seguenti il termine ***Binding-Time*** si riferisce al runtime o alla fase di compilazione, a seconda del momento in cui si verifica l'associazione.

Nell'esempio seguente vengono illustrate le nozioni di associazione statica e dinamica e del tempo di binding:
```csharp
object  o = 5;
dynamic d = 5;

Console.WriteLine(5);  // static  binding to Console.WriteLine(int)
Console.WriteLine(o);  // static  binding to Console.WriteLine(object)
Console.WriteLine(d);  // dynamic binding to Console.WriteLine(int)
```

Le prime due chiamate sono associate in modo statico: l'overload di `Console.WriteLine` viene scelto in base al tipo in fase di compilazione del relativo argomento. In questo modo, il tempo di binding è in fase di compilazione.

La terza chiamata viene associata in modo dinamico: l'overload di `Console.WriteLine` viene scelto in base al tipo in fase di esecuzione del relativo argomento. Questo problema si verifica perché l'argomento è un'espressione dinamica. il tipo in fase di compilazione è `dynamic`. Il tempo di binding per la terza chiamata è quindi in fase di esecuzione.

### <a name="dynamic-binding"></a>Associazione dinamica

Lo scopo dell'associazione dinamica consiste nel consentire C# ai programmi di interagire con ***gli oggetti dinamici***, ovvero gli oggetti che non seguono le regole normali del C# sistema di tipi. È possibile che gli oggetti dinamici siano oggetti di altri linguaggi di programmazione con sistemi di tipi diversi o che siano oggetti a livello di codice per implementare la propria semantica di binding per diverse operazioni.

Il meccanismo mediante il quale un oggetto dinamico implementa la propria semantica è definito dall'implementazione. Una data interfaccia definita, di nuovo implementata, è implementata dagli oggetti dinamici per segnalare al C# Runtime che hanno una semantica speciale. Pertanto, ogni volta che le operazioni su un oggetto dinamico vengono associate in modo dinamico, la relativa semantica di binding C# , invece di quelle di come specificato in questo documento, assume la forma.

Sebbene lo scopo dell'associazione dinamica sia consentire l'interoperabilità con oggetti dinamici C# , consente l'associazione dinamica su tutti gli oggetti, indipendentemente dal fatto che siano dinamici o meno. Ciò consente un'integrazione più uniforme degli oggetti dinamici, perché i risultati delle operazioni su di essi non possono essere oggetti dinamici, ma sono ancora di un tipo sconosciuto al programmatore in fase di compilazione. Inoltre, l'associazione dinamica consente di eliminare codice basato su reflection con errori anche se nessun oggetto è soggetto ad oggetti dinamici.

Le sezioni seguenti descrivono per ogni costrutto nel linguaggio esattamente quando viene applicato il binding dinamico, il controllo del tempo di compilazione, se presente, e il risultato della fase di compilazione e la classificazione delle espressioni.

### <a name="types-of-constituent-expressions"></a>Tipi di espressioni costituenti

Quando un'operazione viene associata in modo statico, il tipo di un'espressione costituente (ad esempio un ricevitore, un argomento, un indice o un operando) viene sempre considerato il tipo in fase di compilazione dell'espressione.

Quando un'operazione viene associata dinamicamente, il tipo di un'espressione costituente viene determinato in modi diversi a seconda del tipo in fase di compilazione dell'espressione costituente:

*  Un'espressione costituente di tipo in fase di compilazione `dynamic` è considerata come il tipo del valore effettivo restituito dall'espressione in fase di esecuzione
*  Un'espressione costituente il cui tipo in fase di compilazione è un parametro di tipo è considerato il tipo a cui è associato il parametro di tipo in fase di esecuzione
*  In caso contrario, l'espressione costituente viene considerata come il tipo in fase di compilazione.

## <a name="operators"></a>Operatori

Le espressioni sono costruite da ***operandi*** e ***operatori***. Gli operatori di un'espressione indicano le operazioni che devono essere eseguite sugli operandi. Alcuni esempi di operatori sono `+`, `-`, `*`, `/` e `new`, mentre i valori effettivi, i campi, le variabili locali e le espressioni sono esempi di operandi.

Sono disponibili tre tipi di operatori:

*  Operatori unari. Gli operatori unari accettano un operando e utilizzano la notazione del prefisso (ad esempio `--x`) o la notazione suffissa (ad esempio `x++`).
*  Operatori binari. Gli operatori binari accettano due operandi e utilizzano la notazione infissa (ad esempio `x + y`).
*  Operatore ternario. Esiste solo un operatore ternario, `?:`, accetta tre operandi e usa la notazione infissa (`c ? x : y`).

L'ordine di valutazione degli operatori in un'espressione è determinato dalla ***precedenza*** e dall' ***associatività*** degli operatori ([precedenza e associatività](expressions.md#operator-precedence-and-associativity)degli operatori).

Gli operandi in un'espressione vengono valutati da sinistra a destra. Ad esempio, in `F(i) + G(i++) * H(i)`, il metodo `F` viene chiamato usando il valore precedente di `i`, quindi il metodo `G` viene chiamato con il valore precedente `i` e, infine, il metodo `H` viene chiamato con il nuovo valore di `i`. Questa operazione è separata da e non è correlata alla precedenza degli operatori.

Certi operatori possono essere ***sottoposti a overload***. L'overload degli operatori consente di specificare implementazioni di operatori definiti dall'utente per le operazioni in cui uno o entrambi gli operandi sono di una classe o di un tipo struct definito dall'utente ([Overload degli operatori](expressions.md#operator-overloading)).

### <a name="operator-precedence-and-associativity"></a>Precedenza e associatività degli operatori

Se un'espressione contiene più operatori, la ***precedenza*** degli operatori determina l'ordine in cui vengono valutati i singoli operatori. Ad esempio, l'espressione `x + y * z` viene valutata come `x + (y * z)` perché l'operatore `*` ha precedenza più alta rispetto all'operatore binario `+`. La precedenza di un operatore viene stabilita dalla definizione della produzione della grammatica associata. Ad esempio, un *additive_expression* è costituito da una sequenza di *multiplicative_expression*s separati da operatori `+` o `-`, assegnando in tal modo agli operatori `+` e `-` la precedenza rispetto a `*`, `/` e @no__ operatori t-8.

La tabella seguente riepiloga tutti gli operatori in ordine di precedenza, dal più alto al più basso:

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
| [Operatori di assegnazione](expressions.md#assignment-operators), [espressioni di funzioni anonime](expressions.md#anonymous-function-expressions)  | Assegnazione ed espressione lambda | `=`  `*=`  `/=`  `%=`  `+=`  `-=`  `<<=`  `>>=`  `&=`  `^=`  <code>&#124;=</code>  `=>` | 

Quando si verifica un operando tra due operatori con la stessa precedenza, l'associatività degli operatori controlla l'ordine in cui vengono eseguite le operazioni:

*  Ad eccezione degli operatori di assegnazione e dell'operatore di Unione null, tutti gli operatori binari sono ***associativi a sinistra***, ovvero le operazioni vengono eseguite da sinistra a destra. L'espressione `x + y + z` viene ad esempio valutata come `(x + y) + z`.
*  Gli operatori di assegnazione, l'operatore di Unione null e l'operatore condizionale (`?:`) sono ***associativi a destra***, ovvero le operazioni vengono eseguite da destra a sinistra. L'espressione `x = y = z` viene ad esempio valutata come `x = (y = z)`.

È possibile controllare la precedenza e l'associatività usando le parentesi. Ad esempio, `x + y * z` prima moltiplica `y` per `z` e quindi somma il risultato a `x`, ma `(x + y) * z` prima somma `x` e `y` e quindi moltiplica il risultato per `z`.

### <a name="operator-overloading"></a>Overload degli operatori

Tutti gli operatori unari e binari hanno implementazioni predefinite che sono automaticamente disponibili in qualsiasi espressione. Oltre alle implementazioni predefinite, le implementazioni definite dall'utente possono essere introdotte includendo dichiarazioni `operator` in classi e struct ([operatori](classes.md#operators)). Le implementazioni degli operatori definite dall'utente hanno sempre la precedenza sulle implementazioni di operatori predefinite: Solo quando non esiste alcuna implementazione di operatore definito dall'utente applicabile, verranno considerate le implementazioni degli operatori predefinite, come descritto in risoluzione dell' [Overload dell'operatore unario](expressions.md#unary-operator-overload-resolution) e [risoluzione dell'overload dell'operatore binario](expressions.md#binary-operator-overload-resolution).

Gli ***operatori unari*** che eseguono l'overload sono:
```csharp
+   -   !   ~   ++   --   true   false
```

Anche se `true` e `false` non vengono usati in modo esplicito nelle espressioni (e pertanto non sono inclusi nella tabella di precedenza in [precedenza e associatività degli operatori](expressions.md#operator-precedence-and-associativity)), sono considerati operatori perché vengono richiamati in più espressioni contesti: espressioni booleane ([espressioni booleane](expressions.md#boolean-expressions)) ed espressioni che interessano l'operatore condizionale ([operatore condizionale](expressions.md#conditional-operator)) e gli operatori logici condizionali ([operatori logici](expressions.md#conditional-logical-operators)condizionali).

Gli ***operatori binari*** che eseguono l'overload sono:
```csharp
+   -   *   /   %   &   |   ^   <<   >>   ==   !=   >   <   >=   <=
```

È possibile eseguire l'overload solo degli operatori elencati in precedenza. In particolare, non è possibile eseguire l'overload dell'accesso ai membri, della chiamata al metodo o dell'`=`, `&&`, `||`, `??`, `?:`, `=>`, `checked`, `unchecked`, `new`, `typeof`, 0, 1 e gli operatori 2.

Quando viene eseguito l'overload di un operatore binario, viene anche eseguito in modo implicito l'overload dell'operatore di assegnazione corrispondente, se presente. Un overload dell'operatore `*`, ad esempio, è anche un overload dell'operatore `*=`. Questa operazione viene descritta ulteriormente nell' [assegnazione composta](expressions.md#compound-assignment). Si noti che non è possibile eseguire l'overload dell'operatore di assegnazione (`=`). Un'assegnazione esegue sempre una semplice copia bit di un valore in una variabile.

Le operazioni cast, ad esempio `(T)x`, vengono sottocaricate fornendo conversioni definite dall'utente ([conversioni definite dall'utente](conversions.md#user-defined-conversions)).

L'accesso agli elementi, ad esempio `a[x]`, non è considerato un operatore che può essere utilizzato per l'overload. L'indicizzazione definita dall'utente è invece supportata tramite gli indicizzatori ([indicizzatori](classes.md#indexers)).

Nelle espressioni viene fatto riferimento agli operatori usando la notazione dell'operatore e, nelle dichiarazioni, viene fatto riferimento agli operatori usando la notazione funzionale. Nella tabella seguente viene illustrata la relazione tra operatore e notazioni funzionali per gli operatori unari e binari. Nella prima voce, *op* denota qualsiasi operatore di prefisso unario che è in overload. Nella seconda voce, *op* indica gli operatori unaria suffisso `++` e `--`. Nella terza voce, *op* denota qualsiasi operatore binario di overload.


| __Notazione operatore__ | __Notazione funzionale__ |
|-----------------------|-------------------------|
| `op x`                | `operator op(x)`        | 
| `x op`                | `operator op(x)`        | 
| `x op y`              | `operator op(x,y)`      | 

Per le dichiarazioni di operatore definite dall'utente è sempre necessario che almeno uno dei parametri sia del tipo di classe o struct che contiene la dichiarazione di operatore. Non è quindi possibile che un operatore definito dall'utente abbia la stessa firma di un operatore predefinito.

Le dichiarazioni di operatore definite dall'utente non possono modificare la sintassi, la precedenza o l'associatività di un operatore. Ad esempio, l'operatore `/` è sempre un operatore binario, ha sempre il livello di precedenza specificato in [precedenza e associatività degli operatori](expressions.md#operator-precedence-and-associativity)ed è sempre associato a sinistra.

Sebbene sia possibile che un operatore definito dall'utente esegua qualsiasi calcolo, le implementazioni che producono risultati diversi da quelli previsti in modo intuitivo sono fortemente sconsigliate. Ad esempio, un'implementazione di `operator ==` deve confrontare i due operandi per verificarne l'uguaglianza e restituire un risultato `bool` appropriato.

Le descrizioni dei singoli operatori nelle [espressioni primarie](expressions.md#primary-expressions) tramite [gli operatori logici condizionali](expressions.md#conditional-logical-operators) specificano le implementazioni predefinite degli operatori ed eventuali regole aggiuntive che si applicano a ogni operatore. Le descrizioni utilizzano i termini risoluzione dell' ***Overload dell'operatore unario***, ***risoluzione dell'overload degli operatori binari***e ***innalzamento***di livello numerico, le cui definizioni sono disponibili nelle sezioni riportate di seguito.

### <a name="unary-operator-overload-resolution"></a>Risoluzione dell'overload dell'operatore unario

Un'operazione nel formato `op x` o `x op`, dove `op` è un operatore unario che esegue l'overload e `x` è un'espressione di tipo `X`, viene elaborata come indicato di seguito:

*  Il set di operatori definiti dall'utente candidati fornito da `X` per l'operazione `operator op(x)` viene determinato mediante le regole degli [operatori candidati definiti dall'utente](expressions.md#candidate-user-defined-operators).
*  Se il set di operatori definiti dall'utente candidato non è vuoto, diventa il set di operatori candidati per l'operazione. In caso contrario, le implementazioni unarie `operator op` predefinite, inclusi i moduli rimossi, diventano il set di operatori candidati per l'operazione. Le implementazioni predefinite di un determinato operatore vengono specificate nella descrizione dell'operatore ([espressioni primarie](expressions.md#primary-expressions) e [operatori unari](expressions.md#unary-operators)).
*  Le regole di risoluzione dell'overload della [risoluzione dell'overload](expressions.md#overload-resolution) vengono applicate al set di operatori candidati per selezionare l'operatore migliore rispetto all'elenco di argomenti `(x)` e questo operatore diventa il risultato del processo di risoluzione dell'overload. Se la risoluzione dell'overload non riesce a selezionare un singolo operatore migliore, si verifica un errore in fase di binding.

### <a name="binary-operator-overload-resolution"></a>Risoluzione dell'overload dell'operatore binario

Un'operazione nel formato `x op y`, dove `op` è un operatore binario che può essere sottoposto a overload, `x` è un'espressione di tipo `X` e `y` è un'espressione di tipo `Y`, viene elaborata come indicato di seguito:

*  Viene determinato il set di operatori candidati definiti dall'utente forniti da `X` e `Y` per l'operazione `operator op(x,y)`. Il set è costituito dall'Unione degli operatori candidati forniti da `X` e dagli operatori candidati forniti da `Y`, ciascuno dei quali viene determinato mediante le regole degli [operatori candidati definiti dall'utente](expressions.md#candidate-user-defined-operators). Se `X` e `Y` sono dello stesso tipo o se `X` e `Y` derivano da un tipo di base comune, gli operatori candidati condivisi vengono eseguiti solo una volta nel set combinato.
*  Se il set di operatori definiti dall'utente candidato non è vuoto, diventa il set di operatori candidati per l'operazione. In caso contrario, le implementazioni binarie `operator op` predefinite, inclusi i moduli rimossi, diventano il set di operatori candidati per l'operazione. Le implementazioni predefinite di un determinato operatore vengono specificate nella descrizione dell'operatore ([operatori aritmetici](expressions.md#arithmetic-operators) tramite [operatori logici condizionali](expressions.md#conditional-logical-operators)). Per gli operatori enum e Delegate predefiniti, gli unici operatori considerati sono quelli definiti da un tipo enum o delegate che rappresenta il tipo in fase di binding di uno degli operandi.
*  Le regole di risoluzione dell'overload della [risoluzione dell'overload](expressions.md#overload-resolution) vengono applicate al set di operatori candidati per selezionare l'operatore migliore rispetto all'elenco di argomenti `(x,y)` e questo operatore diventa il risultato del processo di risoluzione dell'overload. Se la risoluzione dell'overload non riesce a selezionare un singolo operatore migliore, si verifica un errore in fase di binding.

### <a name="candidate-user-defined-operators"></a>Operatori definiti dall'utente candidati

Dato un tipo `T` e un'operazione `operator op(A)`, dove `op` è un operatore che esegue l'overload e `A` è un elenco di argomenti, il set di operatori candidati definiti dall'utente fornito da `T` per `operator op(A)` viene determinato nel modo seguente:

*  Determinare il tipo `T0`. Se `T` è un tipo nullable, `T0` è il tipo sottostante, in caso contrario `T0` è uguale a `T`.
*  Per tutte le dichiarazioni `operator op` in `T0` e tutte le forme sollevate di tali operatori, se almeno un operatore è applicabile ([membro della funzione applicabile](expressions.md#applicable-function-member)) rispetto all'elenco di argomenti `A`, il set di operatori candidati è costituito da tutti questi operatori applicabili in `T0`.
*  In caso contrario, se `T0` è `object`, il set di operatori candidati è vuoto.
*  In caso contrario, il set di operatori candidati fornito da `T0` è il set di operatori candidati fornito dalla classe di base diretta di `T0` o la classe di base effettiva di `T0` se `T0` è un parametro di tipo.

### <a name="numeric-promotions"></a>Promozioni numeriche

La promozione numerica è costituita dall'esecuzione automatica di alcune conversioni implicite degli operandi degli operatori numerici unari e binari predefiniti. La promozione numerica non è un meccanismo distinto, bensì un effetto dell'applicazione della risoluzione dell'overload agli operatori predefiniti. La promozione numerica non influisce specificamente sulla valutazione degli operatori definiti dall'utente, anche se gli operatori definiti dall'utente possono essere implementati in modo da presentare effetti simili.

Come esempio di promozione numerica, prendere in considerazione le implementazioni predefinite dell'operatore binario `*`:

```csharp
int operator *(int x, int y);
uint operator *(uint x, uint y);
long operator *(long x, long y);
ulong operator *(ulong x, ulong y);
float operator *(float x, float y);
double operator *(double x, double y);
decimal operator *(decimal x, decimal y);
```

Quando vengono applicate regole di risoluzione dell'overload ([risoluzione dell'overload](expressions.md#overload-resolution)) a questo set di operatori, l'effetto è quello di selezionare il primo degli operatori per cui esistono conversioni implicite dai tipi di operando. Per l'operazione `b * s`, ad esempio, dove `b` è un `byte` e `s` è un `short`, la risoluzione dell'overload seleziona `operator *(int,int)` come operatore migliore. Pertanto, l'effetto è che `b` e `s` vengono convertiti in `int` e il tipo del risultato è `int`. Analogamente, per l'operazione `i * d`, dove `i` è un `int` e `d` è un `double`, la risoluzione dell'overload seleziona `operator *(double,double)` come operatore migliore.

#### <a name="unary-numeric-promotions"></a>Promozioni numeriche unarie

La promozione numerica unaria si verifica per gli operandi degli operatori unari predefiniti `+`, `-` e `~`. La promozione numerica unaria consiste semplicemente nella conversione di operandi di tipo `sbyte`, `byte`, `short`, `ushort` o `char` al tipo `int`. Inoltre, per l'operatore unario `-`, la promozione numerica unaria converte gli operandi di tipo `uint` nel tipo `long`.

#### <a name="binary-numeric-promotions"></a>Promozioni numeriche binarie

L'innalzamento di tipo numerico binario si verifica per gli operandi degli operatori predefiniti `+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `==`, `!=`, 0, 1, 2 e 3. L'innalzamento di livello numerico binario converte in modo implicito entrambi gli operandi in un tipo comune, che, nel caso degli operatori non relazionali, diventa anche il tipo di risultato dell'operazione. L'innalzamento di numero binario consiste nell'applicare le regole seguenti, nell'ordine in cui sono visualizzate:

*  Se uno degli operandi è di tipo `decimal`, l'altro operando viene convertito nel tipo `decimal` oppure si verifica un errore in fase di binding se l'altro operando è di tipo `float` o `double`.
*  In caso contrario, se uno degli operandi è di tipo `double`, l'altro operando verrà convertito nel tipo `double`.
*  In caso contrario, se uno degli operandi è di tipo `float`, l'altro operando verrà convertito nel tipo `float`.
*  In caso contrario, se uno degli operandi è di tipo `ulong`, l'altro operando verrà convertito nel tipo `ulong` o si verificherà un errore in fase di binding se l'altro operando è di tipo `sbyte`, `short`, `int` o `long`.
*  In caso contrario, se uno degli operandi è di tipo `long`, l'altro operando verrà convertito nel tipo `long`.
*  In caso contrario, se uno degli operandi è di tipo `uint` e l'altro operando è di tipo `sbyte`, `short` o `int`, entrambi gli operandi vengono convertiti nel tipo `long`.
*  In caso contrario, se uno degli operandi è di tipo `uint`, l'altro operando verrà convertito nel tipo `uint`.
*  In caso contrario, entrambi gli operandi vengono convertiti nel tipo `int`.

Si noti che la prima regola impedisce qualsiasi operazione che combina il tipo `decimal` con i tipi `double` e `float`. La regola segue dal fatto che non esistono conversioni implicite tra il tipo `decimal` e i tipi `double` e `float`.

Si noti inoltre che non è possibile che un operando sia di tipo `ulong` quando l'altro operando è di un tipo integrale con segno. Il motivo è che non esiste alcun tipo integrale che può rappresentare l'intervallo completo di `ulong` e i tipi integrali con segno.

In entrambi i casi precedenti, è possibile usare un'espressione cast per convertire in modo esplicito un operando in un tipo compatibile con l'altro operando.

Nell'esempio
```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (1.0 + percent / 100.0);
}
```
si verifica un errore in fase di binding perché un `decimal` non può essere moltiplicato per un `double`. L'errore viene risolto convertendo in modo esplicito il secondo operando in `decimal`, come indicato di seguito:

```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (decimal)(1.0 + percent / 100.0);
}
```

### <a name="lifted-operators"></a>Operatori rimossi

***Gli operatori rimossi*** consentono agli operatori predefiniti e definiti dall'utente che operano su tipi di valore non nullable di essere utilizzati anche con forme Nullable di tali tipi. Gli operatori rimossi vengono costruiti da operatori predefiniti e definiti dall'utente che soddisfano determinati requisiti, come descritto di seguito:

*   Per gli operatori unari

    ```csharp
    +  ++  -  --  !  ~
    ```

    un modulo elevato di un operatore esiste se i tipi di operando e di risultato sono entrambi tipi di valore non nullable. Il form elevato viene costruito aggiungendo un singolo modificatore `?` all'operando e ai tipi di risultato. L'operatore lifted produce un valore null se l'operando è null. In caso contrario, l'operatore lifted Elimina il wrapping dell'operando, applica l'operatore sottostante ed esegue il wrapping del risultato.

*   Per gli operatori binari

    ```csharp
    +  -  *  /  %  &  |  ^  <<  >>
    ```

    Se l'operando e i tipi di risultato sono tutti tipi di valore non nullable, è presente una forma Lifted di un operatore. Il form elevato viene costruito aggiungendo un singolo modificatore `?` a ogni operando e tipo di risultato. L'operatore lifted produce un valore null se uno o entrambi gli operandi sono null (un'eccezione è costituita dagli operatori `&` e `|` del tipo `bool?`, come descritto in [operatori logici booleani](expressions.md#boolean-logical-operators)). In caso contrario, l'operatore lifted esegue l'unwrapping degli operandi, applica l'operatore sottostante ed esegue il wrapping del risultato.

*   Per gli operatori di uguaglianza

    ```csharp
    ==  !=
    ```

    un modulo Lifted di un operatore esiste se i tipi di operando sono entrambi tipi di valore non nullable e se il tipo di risultato è `bool`. Il form elevato viene costruito aggiungendo un singolo modificatore `?` a ogni tipo di operando. L'operatore lifted considera due valori null uguali e un valore null diverso da un valore diverso da null. Se entrambi gli operandi sono non null, l'operatore lifted Annulla il wrapping degli operandi e applica l'operatore sottostante per produrre il risultato `bool`.

*   Per gli operatori relazionali

    ```csharp
    <  >  <=  >=
    ```

    un modulo Lifted di un operatore esiste se i tipi di operando sono entrambi tipi di valore non nullable e se il tipo di risultato è `bool`. Il form elevato viene costruito aggiungendo un singolo modificatore `?` a ogni tipo di operando. L'operatore lifted produce il valore `false` se uno o entrambi gli operandi sono null. In caso contrario, l'operatore lifted esegue l'unwrapping degli operandi e applica l'operatore sottostante per produrre il risultato `bool`.

## <a name="member-lookup"></a>Ricerca di membri

Una ricerca di membri è il processo in base al quale viene determinato il significato di un nome nel contesto di un tipo. Una ricerca di membri può essere eseguita come parte della valutazione di un *simple_name* ([nomi semplici](expressions.md#simple-names)) o di un *member_access* ([accesso ai membri](expressions.md#member-access)) in un'espressione. Se *simple_name* o *member_access* si verifica come *primary_expression* di un *invocation_expression* ([chiamate al metodo](expressions.md#method-invocations)), viene detto che il membro viene richiamato.

Se un membro è un metodo o un evento o se è una costante, un campo o una proprietà di un tipo delegato ([delegati](delegates.md)) o il tipo `dynamic` ([tipo dinamico](types.md#the-dynamic-type)), il membro viene definito *richiamabile*.

La ricerca di membri considera non solo il nome di un membro, ma anche il numero di parametri di tipo del membro e se il membro è accessibile. Ai fini della ricerca dei membri, i metodi generici e i tipi generici annidati hanno il numero di parametri di tipo indicati nelle rispettive dichiarazioni e tutti gli altri membri hanno zero parametri di tipo.

Una ricerca di membri di un nome @ no__t-0 con i parametri `K` @ no__t-2type in un tipo @ no__t-3 viene elaborata come indicato di seguito:

*  In primo luogo, viene determinato un set di membri accessibili denominato @ no__t-0:
    * Se `T` è un parametro di tipo, il set è l'Unione dei set di membri accessibili denominati @ no__t-1 in ognuno dei tipi specificati come vincolo primario o secondario (vincoli di[parametro di tipo](classes.md#type-parameter-constraints)) per @ no__t-3, insieme al set di membri accessibili con nome @ no__t-4 in `object`.
    * In caso contrario, il set è costituito da tutti i membri accessibili ([accesso ai membri](basic-concepts.md#member-access)) denominati @ no__t-1 in @ no__t-2, inclusi i membri ereditati e i membri accessibili denominati @ no__t-3 in `object`. Se `T` è un tipo costruito, il set di membri viene ottenuto sostituendo gli argomenti di tipo come descritto in [membri dei tipi costruiti](classes.md#members-of-constructed-types). I membri che includono un modificatore `override` vengono esclusi dal set.
*  Se quindi `K` è zero, tutti i tipi annidati le cui dichiarazioni includono parametri di tipo vengono rimossi. Se `K` è diverso da zero, vengono rimossi tutti i membri con un numero diverso di parametri di tipo. Si noti che quando `K` è zero, i metodi con parametri di tipo non vengono rimossi, perché il processo di inferenza del tipo ([inferenza](expressions.md#type-inference)del tipo) potrebbe essere in grado di dedurre gli argomenti di tipo.
*  Successivamente, se il membro viene *richiamato*, tutti i membri non*richiamabile* vengono rimossi dal set.
*  Successivamente, i membri che sono nascosti da altri membri vengono rimossi dal set. Per ogni membro `S.M` nel set, dove `S` è il tipo in cui viene dichiarato il membro @ no__t-2, vengono applicate le regole seguenti:
    * Se `M` è una costante, un campo, una proprietà, un evento o un membro di enumerazione, tutti i membri dichiarati in un tipo di base di `S` vengono rimossi dal set.
    * Se `M` è una dichiarazione di tipo, tutti i tipi non dichiarati in un tipo di base di `S` vengono rimossi dal set e tutte le dichiarazioni di tipo con lo stesso numero di parametri di tipo di `M` dichiarate in un tipo di base di `S` vengono rimosse dal set.
    * Se `M` è un metodo, tutti i membri non del metodo dichiarati in un tipo di base di `S` vengono rimossi dal set.
*  Quindi, i membri di interfaccia nascosti dai membri della classe vengono rimossi dal set. Questo passaggio ha effetto solo se `T` è un parametro di tipo e `T` include sia una classe di base valida diversa da `object` sia un set di interfacce effettive non vuote ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)). Per ogni membro `S.M` nel set, dove `S` è il tipo in cui viene dichiarato il membro `M`, vengono applicate le regole seguenti se `S` è una dichiarazione di classe diversa da `object`:
    * Se `M` è una costante, un campo, una proprietà, un evento, un membro di enumerazione o una dichiarazione di tipo, tutti i membri dichiarati in una dichiarazione di interfaccia vengono rimossi dal set.
    * Se `M` è un metodo, tutti i membri non di metodo dichiarati in una dichiarazione di interfaccia vengono rimossi dal set e tutti i metodi con la stessa firma di `M` dichiarati in una dichiarazione di interfaccia vengono rimossi dal set.
*  Infine, dopo aver rimosso i membri nascosti, viene determinato il risultato della ricerca:
    * Se il set è costituito da un singolo membro che non è un metodo, questo membro è il risultato della ricerca.
    * In caso contrario, se il set contiene solo metodi, questo gruppo di metodi è il risultato della ricerca.
    * In caso contrario, la ricerca è ambigua e si verifica un errore in fase di binding.

Per le ricerche di membri in tipi diversi da parametri e interfacce di tipo e ricerche di membri in interfacce che sono esclusivamente a ereditarietà singola (ogni interfaccia nella catena di ereditarietà ha esattamente zero o un'interfaccia di base diretta), l'effetto delle regole di ricerca è semplicemente i membri derivati nascondono i membri di base con lo stesso nome o la stessa firma. Queste ricerche con ereditarietà singola non sono mai ambigue. Le ambiguità che possono derivare da ricerche di membri nelle interfacce con ereditarietà multipla sono descritte in [accesso ai membri di interfaccia](interfaces.md#interface-member-access).

### <a name="base-types"></a>Tipi di base

Ai fini della ricerca dei membri, un tipo `T` è considerato con i tipi di base seguenti:

*  Se `T` è `object`, `T` non ha alcun tipo di base.
*  Se `T` è un *enum_type*, i tipi di base di `T` sono i tipi di classe `System.Enum`, `System.ValueType` e `object`.
*  Se `T` è un *struct_type*, i tipi di base di `T` sono i tipi di classe `System.ValueType` e `object`.
*  Se `T` è un *class_type*, i tipi di base di `T` sono le classi base di `T`, incluso il tipo di classe `object`.
*  Se `T` è un *INTERFACE_TYPE*, i tipi di base di `T` sono le interfacce di base di `T` e il tipo di classe `object`.
*  Se `T` è un *array_type*, i tipi di base di `T` sono i tipi di classe `System.Array` e `object`.
*  Se `T` è un *delegate_type*, i tipi di base di `T` sono i tipi di classe `System.Delegate` e `object`.

## <a name="function-members"></a>Membri di funzione

I membri di funzione sono membri che contengono istruzioni eseguibili. I membri di funzione sono sempre membri di tipi e non possono essere membri di spazi dei nomi. C#definisce le categorie seguenti di membri di funzione:

*  Metodi
*  Proprietà
*  Events
*  Indicizzatori
*  Operatori definiti dall'utente
*  Costruttori di istanze
*  Costruttori statici
*  Distruttori

Ad eccezione dei distruttori e dei costruttori statici (che non possono essere richiamati in modo esplicito), le istruzioni contenute nei membri della funzione vengono eseguite tramite chiamate di membri di funzione. La sintassi effettiva per la scrittura di una chiamata di membro di funzione dipende dalla categoria di membri della funzione specifica.

L'elenco di argomenti ([elenchi di argomenti](expressions.md#argument-lists)) della chiamata di un membro di funzione fornisce valori effettivi o riferimenti a variabili per i parametri del membro della funzione.

Le chiamate dei metodi generici possono utilizzare l'inferenza del tipo per determinare il set di argomenti di tipo da passare al metodo. Questo processo è descritto in [inferenza del tipo](expressions.md#type-inference).

Le chiamate di metodi, indicizzatori, operatori e costruttori di istanza utilizzano la risoluzione dell'overload per determinare quale di un set candidato di membri di funzione richiamare. Questo processo è descritto in [risoluzione dell'overload](expressions.md#overload-resolution).

Una volta identificato un particolare membro di funzione in fase di binding, eventualmente tramite la risoluzione dell'overload, il processo di runtime effettivo per richiamare il membro della funzione viene descritto in fase di [compilazione controllo della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

Nella tabella seguente viene riepilogata l'elaborazione che si verifica nei costrutti che coinvolgono le sei categorie di membri di funzione che possono essere richiamate in modo esplicito. Nella tabella, `e`, `x`, `y` e `value` indicano espressioni classificate come variabili o valori, `T` indica un'espressione classificata come tipo, `F` è il nome semplice di un metodo e `P` è il nome semplice di una proprietà.


| __Costruire__     | __Esempio__    | __Descrizione__ |
|-------------------|----------------|-----------------|
| Chiamata del metodo | `F(x,y)`       | Viene applicata la risoluzione dell'overload per selezionare il metodo migliore `F` nella classe o nello struct che lo contiene. Il metodo viene richiamato con l'elenco di argomenti `(x,y)`. Se il metodo non è `static`, l'espressione dell'istanza è `this`. | 
|                   | `T.F(x,y)`     | Viene applicata la risoluzione dell'overload per selezionare il metodo migliore `F` nella classe o nello struct `T`. Si verifica un errore in fase di binding se il metodo non è `static`. Il metodo viene richiamato con l'elenco di argomenti `(x,y)`. | 
|                   | `e.F(x,y)`     | Viene applicata la risoluzione dell'overload per selezionare il miglior metodo F nella classe, nello struct o nell'interfaccia specificata dal tipo di `e`. Si verifica un errore in fase di binding se il metodo è `static`. Il metodo viene richiamato con l'espressione dell'istanza `e` e l'elenco di argomenti `(x,y)`. | 
| Accesso a proprietà   | `P`            | Viene richiamata la funzione di accesso `get` della proprietà `P` nella classe o nello struct che lo contiene. Si verifica un errore in fase di compilazione se `P` è di sola scrittura. Se `P` non è `static`, l'espressione dell'istanza è `this`. | 
|                   | `P = value`    | La funzione di accesso `set` della proprietà `P` nella classe o nello struct contenitore viene richiamata con l'elenco di argomenti `(value)`. Si verifica un errore in fase di compilazione se `P` è di sola lettura. Se `P` non è `static`, l'espressione dell'istanza è `this`. | 
|                   | `T.P`          | Viene richiamata la funzione di accesso `get` della proprietà `P` nella classe o nello struct `T`. Si verifica un errore in fase di compilazione se `P` non è `static` o se `P` è di sola scrittura. | 
|                   | `T.P = value`  | La funzione di accesso `set` della proprietà `P` nella classe o nello struct `T` viene richiamata con l'elenco di argomenti `(value)`. Si verifica un errore in fase di compilazione se `P` non è `static` o se `P` è di sola lettura. | 
|                   | `e.P`          | La funzione di accesso `get` della proprietà `P` nella classe, nello struct o nell'interfaccia fornita dal tipo di `e` viene richiamata con l'espressione dell'istanza `e`. Si verifica un errore in fase di binding se `P` è `static` o se `P` è di sola scrittura. | 
|                   | `e.P = value`  | La funzione di accesso `set` della proprietà `P` nella classe, nello struct o nell'interfaccia fornita dal tipo di `e` viene richiamata con l'espressione dell'istanza `e` e l'elenco di argomenti `(value)`. Si verifica un errore in fase di binding se `P` è `static` o se `P` è di sola lettura. | 
| Accesso agli eventi      | `E += value`   | Viene richiamata la funzione di accesso `add` dell'evento `E` nella classe o nello struct che lo contiene. Se `E` non è statico, l'espressione dell'istanza è `this`. | 
|                   | `E -= value`   | Viene richiamata la funzione di accesso `remove` dell'evento `E` nella classe o nello struct che lo contiene. Se `E` non è statico, l'espressione dell'istanza è `this`. | 
|                   | `T.E += value` | Viene richiamata la funzione di accesso `add` dell'evento `E` nella classe o nello struct `T`. Si verifica un errore in fase di binding se `E` non è statico. | 
|                   | `T.E -= value` | Viene richiamata la funzione di accesso `remove` dell'evento `E` nella classe o nello struct `T`. Si verifica un errore in fase di binding se `E` non è statico. | 
|                   | `e.E += value` | La funzione di accesso `add` dell'evento `E` nella classe, nello struct o nell'interfaccia fornita dal tipo di `e` viene richiamata con l'espressione dell'istanza `e`. Si verifica un errore in fase di binding se `E` è statico. | 
|                   | `e.E -= value` | La funzione di accesso `remove` dell'evento `E` nella classe, nello struct o nell'interfaccia fornita dal tipo di `e` viene richiamata con l'espressione dell'istanza `e`. Si verifica un errore in fase di binding se `E` è statico. | 
| Accesso all'indicizzatore    | `e[x,y]`       | Viene applicata la risoluzione dell'overload per selezionare il migliore indicizzatore nella classe, nello struct o nell'interfaccia fornita dal tipo di e. La funzione di accesso `get` dell'indicizzatore viene richiamata con l'espressione dell'istanza `e` e l'elenco di argomenti `(x,y)`. Si verifica un errore in fase di binding se l'indicizzatore è di sola scrittura. | 
|                   | `e[x,y] = value` | Viene applicata la risoluzione dell'overload per selezionare il migliore indicizzatore nella classe, nello struct o nell'interfaccia specificata dal tipo di `e`. La funzione di accesso `set` dell'indicizzatore viene richiamata con l'espressione dell'istanza `e` e l'elenco di argomenti `(x,y,value)`. Si verifica un errore in fase di binding se l'indicizzatore è di sola lettura. | 
| Chiamata operatore | `-x`         | Viene applicata la risoluzione dell'overload per selezionare il migliore operatore unario nella classe o nello struct fornito dal tipo di `x`. L'operatore selezionato viene richiamato con l'elenco di argomenti `(x)`. | 
|                     | `x + y`      | Viene applicata la risoluzione dell'overload per selezionare l'operatore binario migliore nelle classi o negli struct specificati dai tipi di `x` e `y`. L'operatore selezionato viene richiamato con l'elenco di argomenti `(x,y)`. | 
| Chiamata del costruttore di istanza | `new T(x,y)` | Viene applicata la risoluzione dell'overload per selezionare il costruttore di istanza migliore nella classe o nello struct `T`. Il costruttore di istanza viene richiamato con l'elenco di argomenti `(x,y)`. | 

### <a name="argument-lists"></a>Elenchi di argomenti

Ogni membro della funzione e la chiamata al delegato includono un elenco di argomenti che fornisce valori effettivi o riferimenti a variabili per i parametri del membro della funzione. La sintassi per specificare l'elenco di argomenti della chiamata di un membro di funzione dipende dalla categoria del membro della funzione:

*  Per i costruttori di istanza, i metodi, gli indicizzatori e i delegati, gli argomenti vengono specificati come *argument_list*, come descritto di seguito. Per gli indicizzatori, quando si richiama la funzione di accesso `set`, l'elenco di argomenti include inoltre l'espressione specificata come operando destro dell'operatore di assegnazione.
*  Per le proprietà, l'elenco di argomenti è vuoto quando si richiama la funzione di accesso `get` ed è costituito dall'espressione specificata come operando destro dell'operatore di assegnazione quando si richiama la funzione di accesso `set`.
*  Per gli eventi, l'elenco di argomenti è costituito dall'espressione specificata come operando destro dell'operatore `+=` o `-=`.
*  Per gli operatori definiti dall'utente, l'elenco di argomenti è costituito dal singolo operando dell'operatore unario o dai due operandi dell'operatore binario.

Gli argomenti delle proprietà ([Proprietà](classes.md#properties)), gli eventi ([eventi](classes.md#events)) e gli operatori definiti dall'utente ([operatori](classes.md#operators)) vengono sempre passati come parametri valore ([parametri valore](classes.md#value-parameters)). Gli argomenti degli indicizzatori ([indicizzatori](classes.md#indexers)) vengono sempre passati come parametri del valore ([parametri di valore](classes.md#value-parameters)) o matrici di parametri ([matrici](classes.md#parameter-arrays)di parametri). I parametri di riferimento e di output non sono supportati per queste categorie di membri di funzione.

Gli argomenti di un costruttore di istanza, un metodo, un indicizzatore o una chiamata a un delegato vengono specificati come *argument_list*:

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

Un *argument_list* è costituito da uno o più *argomenti*, separati da virgole. Ogni argomento è costituito da un *argument_name* facoltativo seguito da un *argument_value*. Un *argomento con* *argument_name* viene definito ***argomento denominato***, mentre un *argomento senza* *argument_name* è un ***argomento posizionale***. Per visualizzare un argomento posizionale dopo un argomento denominato in un *argument_list*, è un errore.

Il *argument_value* può assumere uno dei seguenti formati:

*  *Espressione*, che indica che l'argomento viene passato come parametro del valore ([parametri del valore](classes.md#value-parameters)).
*  Parola chiave `ref` seguita da un *variable_reference* ([riferimenti a variabili](variables.md#variable-references)), che indica che l'argomento viene passato come parametro di riferimento ([parametri di riferimento](classes.md#reference-parameters)). Una variabile deve essere assegnata definitivamente ([assegnazione definita](variables.md#definite-assignment)) prima di poter essere passata come parametro di riferimento. Parola chiave `out` seguita da un *variable_reference* ([riferimenti a variabili](variables.md#variable-references)), che indica che l'argomento viene passato come parametro di output ([parametri di output](classes.md#output-parameters)). Una variabile viene considerata definitivamente assegnata ([assegnazione definita](variables.md#definite-assignment)) dopo una chiamata del membro della funzione in cui la variabile viene passata come parametro di output.

#### <a name="corresponding-parameters"></a>Parametri corrispondenti

Per ogni argomento in un elenco di argomenti deve essere presente un parametro corrispondente nel membro della funzione o nel delegato richiamato.

L'elenco di parametri utilizzato nel codice seguente viene determinato nel modo seguente:

*  Per i metodi virtuali e gli indicizzatori definiti nelle classi, l'elenco di parametri viene selezionato dalla dichiarazione o dall'override più specifici del membro della funzione, a partire dal tipo statico del destinatario e dalla ricerca nelle relative classi di base.
*  Per i metodi di interfaccia e gli indicizzatori, l'elenco di parametri viene selezionato come la definizione più specifica del membro, a partire dal tipo di interfaccia e la ricerca nelle interfacce di base. Se non viene trovato alcun elenco di parametri univoco, viene creato un elenco di parametri con nomi inaccessibili senza parametri facoltativi, in modo che le chiamate non possano utilizzare parametri denominati o omettere argomenti facoltativi.
*  Per i metodi parziali, viene usato l'elenco di parametri della dichiarazione di metodo parziale di definizione.
*  Per tutti gli altri membri e delegati di funzioni è disponibile un solo elenco di parametri, ovvero quello usato.

La posizione di un argomento o di un parametro è definita come numero di argomenti o parametri che lo precedono nell'elenco di argomenti o nell'elenco di parametri.

I parametri corrispondenti per gli argomenti del membro della funzione vengono definiti come segue:

*  Argomenti in *argument_list* di costruttori di istanza, metodi, indicizzatori e delegati:
    * Un argomento posizionale in cui un parametro fisso si trova nella stessa posizione dell'elenco di parametri corrisponde al parametro.
    * Un argomento posizionale di un membro di funzione con una matrice di parametri richiamata nel formato normale corrisponde alla matrice di parametri, che deve essere presente nella stessa posizione dell'elenco dei parametri.
    * Un argomento posizionale di un membro di funzione con una matrice di parametri richiamata nel relativo form espanso, in cui nessun parametro fisso si trova nella stessa posizione nell'elenco di parametri, corrisponde a un elemento nella matrice di parametri.
    * Un argomento denominato corrisponde al parametro con lo stesso nome nell'elenco dei parametri.
    * Per gli indicizzatori, quando si richiama la funzione di accesso `set`, l'espressione specificata come operando di destra dell'operatore di assegnazione corrisponde al parametro implicito `value` della dichiarazione della funzione di accesso `set`.
*  Per le proprietà, quando si richiama la funzione di accesso `get` non sono presenti argomenti. Quando si richiama la funzione di accesso `set`, l'espressione specificata come operando di destra dell'operatore di assegnazione corrisponde al parametro implicito `value` della dichiarazione della funzione di accesso `set`.
*  Per gli operatori unari definiti dall'utente (incluse le conversioni), il singolo operando corrisponde al singolo parametro della dichiarazione di operatore.
*  Per gli operatori binari definiti dall'utente, l'operando sinistro corrisponde al primo parametro e l'operando destro corrisponde al secondo parametro della dichiarazione di operatore.

#### <a name="run-time-evaluation-of-argument-lists"></a>Valutazione in fase di esecuzione degli elenchi di argomenti

Durante l'elaborazione in fase di esecuzione della chiamata di un membro di funzione ([controllo in fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), le espressioni o i riferimenti alle variabili di un elenco di argomenti vengono valutati in ordine, da sinistra a destra, come indicato di seguito:

*  Per un parametro di valore, viene valutata l'espressione dell'argomento e viene eseguita una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) nel tipo di parametro corrispondente. Il valore risultante diventa il valore iniziale del parametro value nella chiamata del membro della funzione.
*  Per un parametro di riferimento o di output, il riferimento alla variabile viene valutato e il percorso di archiviazione risultante diventa il percorso di archiviazione rappresentato dal parametro nella chiamata del membro della funzione. Se il riferimento a una variabile fornito come riferimento o parametro di output è un elemento di matrice di un *reference_type*, viene eseguito un controllo di runtime per garantire che il tipo di elemento della matrice sia identico al tipo del parametro. Se il controllo ha esito negativo, viene generata un'eccezione `System.ArrayTypeMismatchException`.

I metodi, gli indicizzatori e i costruttori di istanze possono dichiarare il parametro più a destra in modo che sia una matrice di parametri ([matrici di parametri](classes.md#parameter-arrays)). Tali membri di funzione vengono richiamati nel formato normale o nel relativo form espanso a seconda del valore applicabile ([membro della funzione applicabile](expressions.md#applicable-function-member)):

*  Quando un membro di funzione con una matrice di parametri viene richiamato nel formato normale, l'argomento specificato per la matrice di parametri deve essere una singola espressione convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) nel tipo di matrice di parametri. In questo caso, la matrice di parametri funge esattamente da parametro di valore.
*  Quando un membro di funzione con una matrice di parametri viene richiamato nella sua forma espansa, la chiamata deve specificare zero o più argomenti posizionali per la matrice di parametri, dove ogni argomento è un'espressione convertibile in modo implicito ([conversioni implicite ](conversions.md#implicit-conversions)) al tipo di elemento della matrice di parametri. In questo caso, la chiamata crea un'istanza del tipo di matrice di parametri con una lunghezza corrispondente al numero di argomenti, inizializza gli elementi dell'istanza di matrice con i valori di argomento specificati e usa l'istanza di matrice appena creata come valore effettivo argomento.

Le espressioni di un elenco di argomenti vengono sempre valutate nell'ordine in cui vengono scritte. Quindi, l'esempio
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
```console
x = 0, y = 1, z = 2
x = 4, y = -1, z = 3
```

Le regole di covarianza della matrice ([covarianza di matrice](arrays.md#array-covariance)) consentono a un valore di un tipo di matrice `A[]` di essere un riferimento a un'istanza di un tipo di matrice `B[]`, purché esista una conversione implicita di un riferimento da `B` a `A`. A causa di queste regole, quando un elemento di matrice di un *reference_type* viene passato come parametro di riferimento o di output, è necessario un controllo Runtime per garantire che il tipo di elemento effettivo della matrice sia identico a quello del parametro. Nell'esempio
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
la seconda chiamata di `F` determina la generazione di un `System.ArrayTypeMismatchException` perché il tipo di elemento effettivo di `b` è `string` e non `object`.

Quando un membro di funzione con una matrice di parametri viene richiamato nella sua forma espansa, la chiamata viene elaborata esattamente come se fosse stata inserita un'espressione di creazione di matrice con un inizializzatore di matrice ([espressioni di creazione di matrici](expressions.md#array-creation-expressions)) intorno ai parametri espansi. Ad esempio, in base alla dichiarazione
```csharp
void F(int x, int y, params object[] args);
```
le chiamate seguenti della forma espansa del metodo
```csharp
F(10, 20);
F(10, 20, 30, 40);
F(10, 20, 1, "hello", 3.0);
```
corrisponde esattamente a
```csharp
F(10, 20, new object[] {});
F(10, 20, new object[] {30, 40});
F(10, 20, new object[] {1, "hello", 3.0});
```

In particolare, si noti che viene creata una matrice vuota quando sono presenti zero argomenti per la matrice di parametri.

Quando gli argomenti vengono omessi da un membro della funzione con i parametri facoltativi corrispondenti, gli argomenti predefiniti della dichiarazione del membro della funzione vengono passati in modo implicito. Poiché questi sono sempre costanti, la valutazione non avrà alcun effetto sull'ordine di valutazione degli argomenti rimanenti.

### <a name="type-inference"></a>Inferenza del tipo

Quando un metodo generico viene chiamato senza specificare argomenti di tipo, un processo di ***inferenza del tipo*** tenta di dedurre gli argomenti di tipo per la chiamata. La presenza dell'inferenza del tipo consente di usare una sintassi più pratica per chiamare un metodo generico e consente al programmatore di evitare di specificare informazioni sui tipi ridondanti. Ad esempio, data la dichiarazione del metodo:
```csharp
class Chooser
{
    static Random rand = new Random();

    public static T Choose<T>(T first, T second) {
        return (rand.Next(2) == 0)? first: second;
    }
}
```
è possibile richiamare il metodo `Choose` senza specificare esplicitamente un argomento di tipo:
```csharp
int i = Chooser.Choose(5, 213);                 // Calls Choose<int>

string s = Chooser.Choose("foo", "bar");        // Calls Choose<string>
```

Tramite l'inferenza del tipo, gli argomenti di tipo `int` e `string` sono determinati dagli argomenti del metodo.

L'inferenza del tipo si verifica come parte dell'elaborazione in fase di binding di una chiamata al metodo ([chiamate al metodo](expressions.md#method-invocations)) e viene eseguita prima del passaggio di risoluzione dell'overload della chiamata. Quando un particolare gruppo di metodi viene specificato in una chiamata al metodo e nessun argomento di tipo viene specificato come parte della chiamata al metodo, l'inferenza del tipo viene applicata a ogni metodo generico nel gruppo di metodi. Se l'inferenza del tipo ha esito positivo, gli argomenti di tipo dedotti vengono usati per determinare i tipi di argomenti per la risoluzione dell'overload successiva. Se la risoluzione dell'overload sceglie un metodo generico come quello da richiamare, gli argomenti di tipo dedotti vengono usati come argomenti di tipo effettivi per la chiamata. Se l'inferenza del tipo per un particolare metodo ha esito negativo, il metodo non partecipa alla risoluzione dell'overload. L'errore di inferenza del tipo, in e di se stesso, non provoca un errore in fase di binding. Tuttavia, spesso genera un errore in fase di binding quando la risoluzione dell'overload non riesce a trovare i metodi applicabili.

Se il numero di argomenti specificato è diverso dal numero di parametri nel metodo, l'inferenza ha immediatamente esito negativo. In caso contrario, si supponga che il metodo generico abbia la firma seguente:
```csharp
Tr M<X1,...,Xn>(T1 x1, ..., Tm xm)
```

Con una chiamata al metodo nel formato `M(E1...Em)` l'attività di inferenza del tipo è trovare gli argomenti di tipo univoco `S1...Sn` per ognuno dei parametri di tipo `X1...Xn` in modo che la chiamata `M<S1...Sn>(E1...Em)` diventi valida.

Durante il processo di inferenza ogni parametro di tipo `Xi` è stato *corretto* a un tipo particolare `Si` o non è stato *risolto* con un set associato di *limiti*. Ogni limite è di tipo `T`. Inizialmente ogni variabile di tipo `Xi` non è fissata con un set vuoto di limiti.

L'inferenza del tipo si verifica in fasi. Ogni fase tenterà di dedurre gli argomenti di tipo per più variabili di tipo in base ai risultati della fase precedente. La prima fase crea alcune inferenze iniziali dei limiti, mentre la seconda fase corregge le variabili di tipo in tipi specifici e deduce ulteriori limiti. La seconda fase può essere ripetuta per un certo numero di volte.

*Nota:* L'inferenza del tipo viene eseguita non solo quando viene chiamato un metodo generico. L'inferenza del tipo per la conversione dei gruppi di metodi è descritta in [inferenza del tipo per la conversione dei gruppi di metodi](expressions.md#type-inference-for-conversion-of-method-groups) e la ricerca del tipo comune migliore di un set di espressioni viene descritta in [ricerca del tipo comune migliore di un set di espressioni](expressions.md#finding-the-best-common-type-of-a-set-of-expressions).

#### <a name="the-first-phase"></a>Prima fase

Per ogni argomento del metodo `Ei`:

*   Se `Ei` è una funzione anonima, viene eseguita un' *inferenza esplicita del tipo di parametro* ([inferenza del tipo di parametro esplicito](expressions.md#explicit-parameter-type-inferences)) da `Ei` a `Ti`
*   In caso contrario, se `Ei` ha un tipo `U` e `xi` è un parametro di valore, viene eseguita un' *inferenza con binding inferiore* *da* `U` *a* `Ti`.
*   In caso contrario, se `Ei` ha un tipo `U` e `xi` è un parametro `ref` o `out`, viene eseguita un' *inferenza esatta* *da* `U` *a* `Ti`.
*   In caso contrario, non viene eseguita alcuna inferenza per questo argomento.


#### <a name="the-second-phase"></a>Seconda fase

La seconda fase procede come segue:

*   Tutte le variabili di tipo non *fisse* `Xi` che non *dipendono* da ([dipendenza](expressions.md#dependence)) qualsiasi `Xj` sono fisse ([correzione](expressions.md#fixing)).
*   Se non esistono variabili di questo tipo, tutte le variabili di tipo non *fisse* `Xi` sono *fisse* per le quali si verificano tutte le condizioni seguenti:
    *   Esiste almeno una variabile di tipo `Xj` che dipende da `Xi`
    *   `Xi` ha un set di limiti non vuoto
*   Se non esiste alcuna variabile di tipo e sono ancora presenti variabili di tipo non *fisse* , l'inferenza del tipo ha esito negativo.
*   In caso contrario, se non esistono altre variabili di tipo non *fisse* , l'inferenza del tipo ha esito positivo.
*   In caso contrario, per tutti gli argomenti `Ei` con il tipo di parametro corrispondente `Ti` in cui i *tipi* di output ([tipi di output](expressions.md#output-types)) contengono variabili di tipo non *fisse* `Xj` ma i *tipi di input* (tipi di[input](expressions.md#input-types)) non lo sono.  *l'inferenza del tipo di output* ([inferenza del tipo di output](expressions.md#output-type-inferences)) viene eseguita *da* 1 *a* 3. Quindi, la seconda fase viene ripetuta.

#### <a name="input-types"></a>Tipi di input

Se `E` è un gruppo di metodi o una funzione anonima tipizzata in modo implicito e `T` è un tipo delegato o un tipo di albero delle espressioni, tutti i tipi di parametro di `T` sono *tipi di input* di `E` *con tipo* `T`.

####  <a name="output-types"></a>Tipi di output

Se `E` è un gruppo di metodi o una funzione anonima e `T` è un tipo delegato o un tipo di albero delle espressioni, il tipo restituito di `T` è un *tipo di output di* `E` *con tipo* `T`.

#### <a name="dependence"></a>Dipendenza

Una variabile di tipo *unfixed* `Xi` *dipende direttamente da* una variabile di tipo non fixed `Xj` se per alcuni argomenti `Ek` con tipo `Tk` `Xj` si verifica in un *tipo di input* `Ek` con tipo `Tk` e 0 si verifica in un  *tipo di output* di 2 con tipo 3.

`Xj` *dipende* da `Xi` se `Xj` *dipende direttamente da* `Xi` o se `Xi` dipende *direttamente da* `Xk` e `Xk` *dipende* da 1. Pertanto "dipende da" è la chiusura transitiva ma non riflessiva di "dipende direttamente da".

#### <a name="output-type-inferences"></a>Inferenza del tipo di output

Un' *inferenza del tipo di output* viene eseguita *da* un'espressione `E` *a* un tipo `T` nel modo seguente:

*  Se `E` è una funzione anonima con tipo restituito dedotto `U` ([tipo restituito derivato](expressions.md#inferred-return-type)) e `T` è un tipo delegato o un tipo di albero delle espressioni con tipo restituito `Tb`, quindi un' *inferenza con binding inferiore* ([inferenza con associazione inferiore](expressions.md#lower-bound-inferences)) viene eseguita *da* `U` *a* 0.
*  In caso contrario, se `E` è un gruppo di metodi e `T` è un tipo delegato o un tipo di albero delle espressioni con tipi di parametro `T1...Tk` e tipo restituito `Tb` e la risoluzione dell'overload di `E` con i tipi `T1...Tk` produce un solo metodo con tipo restituito `U` , quindi viene eseguita un' *inferenza con associazione inferiore* *da* `U` *a* 1.
*  In caso contrario, se `E` è un'espressione con tipo `U`, viene eseguita un' *inferenza con binding inferiore* *da* `U` *a* `T`.
*  In caso contrario, non vengono eseguite inferenze.

#### <a name="explicit-parameter-type-inferences"></a>Inferenza del tipo di parametro esplicito

Un' *inferenza esplicita del tipo di parametro* viene eseguita *da* un'espressione `E` *a* un tipo `T` nel modo seguente:

*  Se `E` è una funzione anonima tipizzata in modo esplicito con tipi di parametro `U1...Uk` e `T` è un tipo delegato o un tipo di albero delle espressioni con i tipi di parametro `V1...Vk`, per ogni `Ui` viene apportata un'*inferenza esatta* ([Inferenze esatte](expressions.md#exact-inferences)) *da* `Ui` *all'* `Vi` corrispondente.

#### <a name="exact-inferences"></a>Inferenza esatta

Un' *inferenza esatta* *da* un tipo `U` *a* un tipo `V` viene effettuata come segue:

*  Se `V` è uno dei `Xi` non *corretti* , viene aggiunto `U` al set di limiti esatti per `Xi`.

*  In caso contrario, i set `V1...Vk` e `U1...Uk` vengono determinati controllando se si verifica uno dei casi seguenti:

   *  `V` è un tipo di matrice `V1[...]` e `U` è un tipo di matrice `U1[...]` dello stesso rango
   *  `V` è il tipo `V1?` e `U` è il tipo `U1?`
   *  `V` è un tipo costruito `C<V1...Vk>` e `U` è un tipo costruito `C<U1...Uk>`

   Se si verifica uno di questi casi, viene eseguita un' *inferenza esatta* *da* ogni `Ui` *al* `Vi` corrispondente.

*  In caso contrario, non vengono eseguite inferenze.

#### <a name="lower-bound-inferences"></a>Inferenza con associazione inferiore

Un' *inferenza con associazione inferiore* *da* un tipo `U` *a* un tipo `V` viene effettuata come segue:

*  Se `V` è uno dei `Xi` non *corretti* , viene aggiunto `U` al set di limiti inferiori per `Xi`.
*  In caso contrario, se `V` è il tipo `V1?` e `U` è il tipo `U1?`, viene eseguita un'inferenza del limite inferiore da `U1` a `V1`.
*  In caso contrario, i set `U1...Uk` e `V1...Vk` vengono determinati controllando se si verifica uno dei casi seguenti:
   *  `V` è un tipo di matrice `V1[...]` e `U` è un tipo di matrice `U1[...]` (oppure un parametro di tipo il cui tipo di base effettivo è `U1[...]`) dello stesso rango
   *  `V` è uno dei `IEnumerable<V1>`, `ICollection<V1>` o `IList<V1>` e `U` è un tipo di matrice unidimensionale `U1[]` (o un parametro di tipo il cui tipo di base effettivo è `U1[]`)
   *  `V` è una classe costruita, uno struct, un tipo di interfaccia o delegato `C<V1...Vk>` ed è presente un tipo univoco `C<U1...Uk>` in modo che `U` (o, se `U` è un parametro di tipo, la relativa classe di base effettiva o qualsiasi membro del set di interfacce effettivo) è identica a , eredita da (direttamente o indirettamente) o implementa (direttamente o indirettamente) `C<U1...Uk>`.

      La restrizione "univocità" significa che nell'interfaccia del case `C<T> {} class U: C<X>, C<Y> {}`, non viene eseguita alcuna inferenza quando si deduce da `U` a `C<T>` perché `U1` potrebbe essere `X` o `Y`.

   Se si verifica uno di questi casi, viene eseguita un'inferenza *da* ogni `Ui` *al* `Vi` corrispondente come indicato di seguito:

   *  Se `Ui` non è noto come tipo di riferimento, viene eseguita un' *inferenza esatta*
   *  In caso contrario, se `U` è un tipo di matrice, viene eseguita un' *inferenza con associazione inferiore*
   *  In caso contrario, se `V` è `C<V1...Vk>`, l'inferenza dipende dal parametro di tipo i-th di `C`:
      *  Se è covariante, viene eseguita un' *inferenza con associazione inferiore* .
      *  Se è controvariante, viene eseguita un' *inferenza del limite superiore* .
      *  Se è invariante, viene eseguita un' *inferenza esatta* .
*  In caso contrario, non vengono eseguite inferenze.

#### <a name="upper-bound-inferences"></a>Inferenze con associazione superiore

Un' *inferenza con binding superiore* *da* un tipo `U` *a* un tipo `V` viene effettuata come segue:

*  Se `V` è uno dei `Xi` non *corretti* , viene aggiunto `U` al set di limiti superiori per `Xi`.
*  In caso contrario, i set `V1...Vk` e `U1...Uk` vengono determinati controllando se si verifica uno dei casi seguenti:
   *  `U` è un tipo di matrice `U1[...]` e `V` è un tipo di matrice `V1[...]` dello stesso rango
   *  `U` è uno dei `IEnumerable<Ue>`, `ICollection<Ue>` o `IList<Ue>` e `V` è un tipo di matrice unidimensionale `Ve[]`
   *  `U` è il tipo `U1?` e `V` è il tipo `V1?`
   *  `U` è costituito da classe, struct, interfaccia o tipo delegato `C<U1...Uk>` e `V` è una classe, uno struct, un'interfaccia o un tipo delegato identico a, eredita da (direttamente o indirettamente) o implementa (direttamente o indirettamente) un tipo univoco `C<V1...Vk>`

      La restrizione "univocità" significa che se è `interface C<T>{} class V<Z>: C<X<Z>>, C<Y<Z>>{}`, non viene eseguita alcuna inferenza quando si deduce da `C<U1>` a `V<Q>`. Le inferenze non vengono apportate da `U1` a `X<Q>` o `Y<Q>`.

   Se si verifica uno di questi casi, viene eseguita un'inferenza *da* ogni `Ui` *al* `Vi` corrispondente come indicato di seguito:
   *  Se `Ui` non è noto come tipo di riferimento, viene eseguita un' *inferenza esatta*
   *  In caso contrario, se `V` è un tipo di matrice, viene eseguita un' *inferenza con limite superiore*
   *  In caso contrario, se `U` è `C<U1...Uk>`, l'inferenza dipende dal parametro di tipo i-th di `C`:
      *  Se è covariante, viene eseguita un' *inferenza del limite superiore* .
      *  Se è controvariante, viene eseguita un' *inferenza con associazione inferiore* .
      *  Se è invariante, viene eseguita un' *inferenza esatta* .
*  In caso contrario, non vengono eseguite inferenze.   

#### <a name="fixing"></a>Fissaggio

Una variabile di tipo *unfixed* `Xi` con un set di limiti viene *fissata* come indicato di seguito:

*  Il set di *tipi candidati* `Uj` inizia come set di tutti i tipi nel set di limiti per `Xi`.
*  Si esaminerà quindi ogni limite per `Xi` a sua volta: Per ogni limite esatto `U` di `Xi` tutti i tipi `Uj` che non sono identici a `U` vengono rimossi dal set candidato. Per ogni limite inferiore `U` di `Xi` tutti i tipi `Uj` in cui *non* esiste una conversione implicita da `U` vengono rimossi dal set candidato. Per ogni limite superiore `U` di `Xi` tutti i tipi `Uj` da cui *non* esiste una conversione implicita a `U` vengono rimossi dal set di candidati.
*  Se tra i tipi candidati rimanenti `Uj` esiste un tipo univoco `V` da cui esiste una conversione implicita a tutti gli altri tipi candidati, `Xi` viene fissato a `V`.
*  In caso contrario, l'inferenza del tipo non riesce.

#### <a name="inferred-return-type"></a>Tipo restituito derivato

Il tipo restituito derivato di una funzione anonima `F` viene usato durante l'inferenza del tipo e la risoluzione dell'overload. Il tipo restituito derivato può essere determinato solo per una funzione anonima in cui tutti i tipi di parametro sono noti, perché sono specificati in modo esplicito, forniti tramite una conversione di funzione anonima o dedotti durante l'inferenza del tipo in un oggetto generico di inclusione chiamata al metodo.

Il ***tipo di risultato derivato*** viene determinato nel modo seguente:

*  Se il corpo di `F` è un' *espressione* con un tipo, il tipo di risultato derivato di `F` è il tipo di tale espressione.
*  Se il corpo di `F` è un *blocco* e il set di espressioni nelle istruzioni `return` del blocco ha un tipo comune migliore `T` ([ricerca del tipo comune migliore di un set di espressioni](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)), il tipo di risultato derivato di `F` è `T`.
*  In caso contrario, non è possibile dedurre un tipo di risultato per `F`.

Il ***tipo restituito derivato*** viene determinato nel modo seguente:

*  Se `F` è async e il corpo di `F` è un'espressione classificata come Nothing ([classificazioni di espressioni](expressions.md#expression-classifications)) o un blocco di istruzioni in cui nessuna istruzione return contiene espressioni, il tipo restituito derivato sarà `System.Threading.Tasks.Task`
*  Se `F` è async e ha un tipo di risultato derivato `T`, il tipo restituito derivato è `System.Threading.Tasks.Task<T>`.
*  Se `F` è non asincrono e ha un tipo di risultato derivato `T`, il tipo restituito derivato è `T`.
*  In caso contrario, non è possibile dedurre un tipo restituito per `F`.

Come esempio di inferenza del tipo che include funzioni anonime, si consideri il metodo di estensione `Select` dichiarato nella classe `System.Linq.Enumerable`:
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

Supponendo che lo spazio dei nomi `System.Linq` sia stato importato con una clausola `using` e che sia stata specificata una classe `Customer` con una proprietà `Name` di tipo `string`, è possibile usare il metodo `Select` per selezionare i nomi di un elenco di clienti:
```csharp
List<Customer> customers = GetCustomerList();
IEnumerable<string> names = customers.Select(c => c.Name);
```

La chiamata al metodo di estensione ([chiamate al metodo di estensione](expressions.md#extension-method-invocations)) di `Select` viene elaborata riscrivendo la chiamata in una chiamata a un metodo statico:
```csharp
IEnumerable<string> names = Enumerable.Select(customers, c => c.Name);
```

Poiché gli argomenti di tipo non sono stati specificati in modo esplicito, viene usata l'inferenza del tipo per dedurre gli argomenti tipo. In primo luogo, l'argomento `customers` è correlato al parametro `source`, deferendo `T` come `Customer`. Quindi, utilizzando il processo di inferenza del tipo di funzione anonimo descritto in precedenza, a `c` viene assegnato il tipo `Customer` e l'espressione `c.Name` è correlata al tipo restituito del parametro `selector`, deferendo `S` come `string`. Quindi, la chiamata è equivalente a
```csharp
Sequence.Select<Customer,string>(customers, (Customer c) => c.Name)
```
e il risultato è di tipo `IEnumerable<string>`.

Nell'esempio seguente viene illustrato come l'inferenza del tipo di funzione anonima consente alle informazioni sul tipo di "propagarsi" tra gli argomenti in una chiamata a un metodo generico Dato il metodo:
```csharp
static Z F<X,Y,Z>(X value, Func<X,Y> f1, Func<Y,Z> f2) {
    return f2(f1(value));
}
```

Inferenza del tipo per la chiamata:
```csharp
double seconds = F("1:15:30", s => TimeSpan.Parse(s), t => t.TotalSeconds);
```
procedere come segue: In primo luogo, l'argomento `"1:15:30"` è correlato al parametro `value`, deferendo `X` come `string`. Quindi, al parametro della prima funzione anonima, `s`, viene assegnato il tipo dedotto `string` e l'espressione `TimeSpan.Parse(s)` è correlata al tipo restituito di `f1`, deferendo `Y` come `System.TimeSpan`. Infine, al parametro della seconda funzione anonima, `t`, viene assegnato il tipo dedotto `System.TimeSpan` e l'espressione `t.TotalSeconds` è correlata al tipo restituito di `f2`, deferendo `Z` come `double`. Il risultato della chiamata è quindi di tipo `double`.

#### <a name="type-inference-for-conversion-of-method-groups"></a>Inferenza del tipo per la conversione dei gruppi di metodi

Analogamente alle chiamate di metodi generici, l'inferenza del tipo deve essere applicata anche quando un gruppo di metodi `M` contenente un metodo generico viene convertito in un tipo delegato specificato `D` ([conversioni di gruppi di metodi](conversions.md#method-group-conversions)). Dato un metodo
```csharp
Tr M<X1...Xn>(T1 x1 ... Tm xm)
```
e il gruppo di metodi `M` da assegnare al tipo delegato `D` l'attività di inferenza del tipo consiste nel trovare gli argomenti di tipo `S1...Sn` in modo che l'espressione:
```csharp
M<S1...Sn>
```
diventa compatibile ([dichiarazioni di delegati](delegates.md#delegate-declarations)) con `D`.

Diversamente dall'algoritmo di inferenza del tipo per le chiamate ai metodi generici, in questo caso sono disponibili solo *tipi*di argomento, nessuna *espressione*di argomento. In particolare, non sono presenti funzioni anonime e pertanto non sono necessarie più fasi di inferenza.

Al contrario, tutti `Xi` sono considerati non *corretti*e viene eseguita un' *inferenza con associazione inferiore* *da* ogni tipo di argomento `Uj` di `D` *al* tipo di parametro corrispondente `Tj` di `M`. Se per uno dei `Xi` non sono stati trovati limiti, l'inferenza del tipo ha esito negativo. In caso contrario, tutti i `Xi` vengono *fissati* al `Si` corrispondente, che è il risultato dell'inferenza del tipo.

#### <a name="finding-the-best-common-type-of-a-set-of-expressions"></a>Ricerca del tipo comune migliore di un set di espressioni

In alcuni casi, è necessario dedurre un tipo comune per un set di espressioni. In particolare, i tipi di elemento delle matrici tipizzate in modo implicito e i tipi restituiti di funzioni anonime con corpi di *blocco* vengono individuati in questo modo.

In modo intuitivo, dato un set di espressioni `E1...Em` questa inferenza dovrebbe essere equivalente alla chiamata a un metodo
```csharp
Tr M<X>(X x1 ... X xm)
```
con il `Ei` come argomenti.

Più precisamente, l'inferenza inizia con una variabile di tipo non *fixed* `X`. Le *inferenze del tipo di output* vengono quindi effettuate *da* ogni `Ei` *a* `X`. Infine, `X` è *fisso* e, in caso di esito positivo, il tipo risultante `S` è il tipo più comune risultante per le espressioni. Se non esiste alcun `S`, le espressioni non hanno un tipo comune migliore.

### <a name="overload-resolution"></a>Risoluzione dell'overload

La risoluzione dell'overload è un meccanismo in fase di associazione per la selezione del miglior membro di funzione da richiamare dato un elenco di argomenti e un set di membri di funzione candidati. Risoluzione dell'overload consente di selezionare il membro della funzione da richiamare nei contesti C#distinti seguenti all'interno di:

*  Chiamata di un metodo denominato in un *invocation_expression* (chiamate al[Metodo](expressions.md#method-invocations)).
*  Chiamata di un costruttore di istanza denominato in un *object_creation_expression* ([espressioni di creazione di oggetti](expressions.md#object-creation-expressions)).
*  Chiamata di una funzione di accesso dell'indicizzatore tramite un *element_access* ([accesso agli elementi](expressions.md#element-access)).
*  Chiamata di un operatore predefinito o definito dall'utente a cui si fa riferimento in un'espressione ([risoluzione dell'overload dell'operatore unario](expressions.md#unary-operator-overload-resolution) e [risoluzione dell'overload dell'operatore binario](expressions.md#binary-operator-overload-resolution)).

Ognuno di questi contesti definisce il set di membri di funzione candidati e l'elenco di argomenti in modo univoco, come descritto in dettaglio nelle sezioni elencate in precedenza. Ad esempio, il set di candidati per una chiamata al metodo non include i metodi contrassegnati `override` ([ricerca di membri](expressions.md#member-lookup)) e i metodi in una classe di base non sono candidati se un metodo in una classe derivata è applicabile ([chiamate al metodo](expressions.md#method-invocations)).

Una volta identificati i membri della funzione candidata e l'elenco di argomenti, la selezione del membro della funzione migliore è la stessa in tutti i casi:

*  Dato il set di membri di funzione candidati applicabili, viene individuato il membro della funzione migliore nel set. Se il set contiene un solo membro della funzione, il membro della funzione è il membro della funzione migliore. In caso contrario, il membro di funzione migliore è il membro di una funzione che è migliore di tutti gli altri membri della funzione rispetto all'elenco di argomenti specificato, a condizione che ogni membro di funzione venga confrontato con tutti gli altri membri di funzione utilizzando le regole in una [funzione migliore membro](expressions.md#better-function-member). Se non è presente esattamente un membro di funzione migliore di tutti gli altri membri della funzione, la chiamata del membro della funzione è ambigua e si verifica un errore in fase di binding.

Le sezioni seguenti definiscono il significato esatto dei termini membro della ***funzione applicabile*** e ***membro di funzione migliore***.

#### <a name="applicable-function-member"></a>Membro di funzione applicabile

Un membro di funzione viene definito membro della ***funzione applicabile*** rispetto a un elenco di argomenti `A` quando si verificano tutte le condizioni seguenti:

*  Ogni argomento in `A` corrisponde a un parametro nella dichiarazione del membro della funzione come descritto in [parametri corrispondenti](expressions.md#corresponding-parameters)e qualsiasi parametro a cui non corrisponde alcun argomento è un parametro facoltativo.
*  Per ogni argomento in `A`, la modalità di passaggio del parametro dell'argomento (ad esempio, value, `ref` o `out`) è identica alla modalità di passaggio del parametro del parametro corrispondente e
   *  per un parametro di valore o una matrice di parametri, esiste una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) dall'argomento al tipo del parametro corrispondente.
   *  per un parametro `ref` o `out`, il tipo dell'argomento è identico al tipo del parametro corrispondente. Dopo tutto, un parametro `ref` o `out` è un alias per l'argomento passato.

Per un membro di funzione che include una matrice di parametri, se il membro della funzione è applicabile dalle regole precedenti, viene definito applicabile nel ***formato normale***. Se un membro di funzione che include una matrice di parametri non è applicabile nel formato normale, il membro della funzione può essere invece applicabile nel ***formato espanso***:

*  Il form espanso viene costruito sostituendo la matrice di parametri nella dichiarazione del membro della funzione con zero o più parametri del valore del tipo di elemento della matrice di parametri in modo che il numero di argomenti nell'elenco di argomenti `A` corrisponda al numero totale di parametri. Se `A` ha meno argomenti rispetto al numero di parametri fissi nella dichiarazione del membro della funzione, il form espanso del membro della funzione non può essere costruito e pertanto non è applicabile.
*  In caso contrario, il form espanso è applicabile se per ogni argomento in `A` la modalità di passaggio del parametro dell'argomento è identica alla modalità di passaggio del parametro del parametro corrispondente e
   *  per un parametro di valore fisso o un parametro di valore creato dall'espansione, esiste una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) dal tipo dell'argomento al tipo del parametro corrispondente.
   *  per un parametro `ref` o `out`, il tipo dell'argomento è identico al tipo del parametro corrispondente.

#### <a name="better-function-member"></a>Membro di funzione migliore

Ai fini della determinazione del membro di funzione migliore, un elenco di argomenti rimossi A viene costruito contenente solo le espressioni di argomento stesse nell'ordine in cui compaiono nell'elenco di argomenti originale.

Gli elenchi di parametri per ogni membro della funzione candidata vengono costruiti nel modo seguente:

*  Il form espanso viene usato se il membro della funzione è applicabile solo nel form espanso.
*  I parametri facoltativi senza argomenti corrispondenti vengono rimossi dall'elenco di parametri
*  I parametri vengono riordinati in modo che si verifichino nella stessa posizione dell'argomento corrispondente nell'elenco di argomenti.

Dato un elenco di argomenti `A` con un set di espressioni di argomento `{E1, E2, ..., En}` e due membri di funzione applicabili `Mp` e `Mq` con i tipi di parametro `{P1, P2, ..., Pn}` e `{Q1, Q2, ..., Qn}`, `Mp` è definito come ***membro della funzione migliore*** di `Mq` se

*  per ogni argomento, la conversione implicita da `Ex` a `Qx` non è migliore rispetto alla conversione implicita da `Ex` a `Px` e
*  per almeno un argomento, la conversione da `Ex` a `Px` è preferibile rispetto alla conversione da `Ex` a `Qx`.

Quando si esegue questa valutazione, se `Mp` o `Mq` è applicabile nella forma espansa, `Px` o `Qx` fa riferimento a un parametro nella forma espansa dell'elenco di parametri.

Nel caso in cui il tipo di parametro sequences @ no__t-0 e `{Q1, Q2, ..., Qn}` siano equivalenti (ad esempio, ogni `Pi` ha una conversione di identità nel `Qi` corrispondente), per determinare il membro di funzione migliore verranno applicate le regole di suddivisione in ordine.

*  Se `Mp` è un metodo non generico e `Mq` è un metodo generico, `Mp` è migliore di `Mq`.
*  In caso contrario, se `Mp` è applicabile nel formato normale e `Mq` ha una matrice `params` ed è applicabile solo nel formato espanso, `Mp` è migliore di `Mq`.
*  In caso contrario, se `Mp` ha più parametri dichiarati rispetto a `Mq`, `Mp` è migliore di `Mq`. Questo problema può verificarsi se entrambi i metodi hanno matrici `params` e sono applicabili solo nei moduli espansi.
*  In caso contrario, se tutti i parametri di `Mp` hanno un argomento corrispondente mentre gli argomenti predefiniti devono essere sostituiti da almeno un parametro facoltativo in `Mq`, `Mp` è migliore di `Mq`.
*  In caso contrario, se `Mp` ha tipi di parametro più specifici rispetto a `Mq`, `Mp` è migliore di `Mq`. Consentire a `{R1, R2, ..., Rn}` e `{S1, S2, ..., Sn}` di rappresentare i tipi di parametro non sottostanti e non espansi di `Mp` e `Mq`. i tipi di parametro di `Mp` sono più specifici di `Mq` se, per ogni parametro, `Rx` non è meno specifico di `Sx` e, per almeno un parametro, `Rx` è più specifico di `Sx`:
   *  Un parametro di tipo è meno specifico di un parametro non di tipo.
   *  In modo ricorsivo, un tipo costruito è più specifico di un altro tipo costruito (con lo stesso numero di argomenti di tipo) se almeno un argomento di tipo è più specifico e nessun argomento di tipo è meno specifico dell'argomento di tipo corrispondente nell'altro.
   *  Un tipo di matrice è più specifico di un altro tipo di matrice (con lo stesso numero di dimensioni) se il tipo di elemento del primo è più specifico del tipo di elemento del secondo.
*  In caso contrario, se un membro è un operatore non Lift e l'altro è un operatore lifted, è preferibile che non si sollevi.
*  In caso contrario, nessun membro della funzione è migliore.

#### <a name="better-conversion-from-expression"></a>Migliore conversione dall'espressione

Data una conversione implicita `C1` che converte da un'espressione `E` a un tipo `T1` e una conversione implicita `C2` che converte da un'espressione `E` a un tipo `T2`, `C1` è una ***conversione migliore*** rispetto a `C2` se @no__ t-9 non corrisponde esattamente 0 ed è presente almeno uno dei seguenti elementi:

* `E` corrisponde esattamente `T1` ([espressione corrispondente esattamente](expressions.md#exactly-matching-expression))
* `T1` è una destinazione di conversione migliore rispetto a `T2` ([migliore destinazione di conversione](expressions.md#better-conversion-target))

#### <a name="exactly-matching-expression"></a>Espressione corrispondente esatta

Data un'espressione `E` e un tipo `T`, `E` corrisponde esattamente `T` Se uno degli elementi seguenti include:

*  `E` ha un tipo `S` ed esiste una conversione di identità da `S` a `T`
*  `E` è una funzione anonima, `T` è un tipo delegato `D` o un tipo di albero delle espressioni `Expression<D>` e uno dei seguenti include:
   *  Un tipo restituito derivato `X` esiste per `E` nel contesto dell'elenco di parametri di `D` ([tipo restituito derivato](expressions.md#inferred-return-type)) ed esiste una conversione di identità da `X` al tipo restituito di `D`
   *  @No__t-0 è non asincrono e `D` ha un tipo restituito `Y` o `E` è async e `D` ha un tipo restituito `Task<Y>` e uno dei seguenti contiene:
      * Il corpo di `E` è un'espressione che corrisponde esattamente `Y`
      * Il corpo di `E` è un blocco di istruzioni in cui ogni istruzione return restituisce un'espressione che corrisponde esattamente `Y`

#### <a name="better-conversion-target"></a>Migliore destinazione di conversione

Dati due tipi diversi `T1` e `T2`, `T1` è un obiettivo di conversione migliore rispetto a `T2` Se non esiste alcuna conversione implicita da `T2` a `T1` e almeno uno dei seguenti:

*  Esiste una conversione implicita da `T1` a `T2`
*  `T1` è un tipo delegato `D1` o un tipo di albero delle espressioni `Expression<D1>`, `T2` è un tipo delegato `D2` o un tipo di albero delle espressioni `Expression<D2>`, `D1` ha un tipo restituito `S1` e uno dei seguenti contiene :
   * `D2` restituisce void
   * `D2` ha un tipo restituito `S2` e `S1` è un obiettivo di conversione migliore rispetto a `S2`
*  `T1` è `Task<S1>`, `T2` è `Task<S2>` e `S1` è una destinazione di conversione migliore rispetto a `S2`
*  `T1` è `S1` o `S1?` dove `S1` è un tipo integrale con segno e `T2` è `S2` o `S2?` dove `S2` è un tipo integrale senza segno. In particolare:
   * `S1` è `sbyte` e `S2` è `byte`, `ushort`, `uint` o `ulong`
   * `S1` è `short` e `S2` è `ushort`, `uint` o `ulong`
   * `S1` è `int` e `S2` è `uint` o `ulong`
   * `S1` è `long` e `S2` è `ulong`

#### <a name="overloading-in-generic-classes"></a>Overload in classi generiche

Mentre le firme dichiarate devono essere univoche, è possibile che la sostituzione di argomenti di tipo comportasse firme identiche. In questi casi, le regole di associazione della risoluzione dell'overload precedente preleveranno il membro più specifico.

Gli esempi seguenti mostrano gli overload validi e non validi in base a questa regola:

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

### <a name="compile-time-checking-of-dynamic-overload-resolution"></a>Verifica della risoluzione dell'overload dinamico in fase di compilazione

Per la maggior parte delle operazioni con associazione dinamica il set di possibili candidati per la risoluzione non è noto in fase di compilazione. In alcuni casi, tuttavia, il set di candidati è noto in fase di compilazione:

*  Chiamate di metodo statiche con argomenti dinamici
*  Chiamate al metodo di istanza in cui il ricevitore non è un'espressione dinamica
*  L'indicizzatore chiama dove il ricevitore non è un'espressione dinamica
*  Chiamate al costruttore con argomenti dinamici

In questi casi viene eseguito un controllo in fase di compilazione limitato per ogni candidato per verificare se una di esse potrebbe essere applicata in fase di esecuzione. Questo controllo è costituito dai passaggi seguenti:

*  Inferenza del tipo parziale: Qualsiasi argomento di tipo che non dipende direttamente o indirettamente da un argomento di tipo `dynamic` viene dedotto usando le regole dell' [inferenza del tipo](expressions.md#type-inference). Gli argomenti di tipo rimanenti sono sconosciuti.
*  Verifica dell'applicabilità parziale: L'applicabilità viene controllata in base al [membro della funzione applicabile](expressions.md#applicable-function-member), ma ignorando i parametri i cui tipi sono sconosciuti.
*  Se nessun candidato supera questo test, si verificherà un errore in fase di compilazione.

### <a name="function-member-invocation"></a>Chiamata del membro di funzione

In questa sezione viene descritto il processo che si verifica in fase di esecuzione per richiamare un particolare membro di funzione. Si presuppone che un processo in fase di binding abbia già determinato il membro specifico da richiamare, eventualmente applicando la risoluzione dell'overload a un set di membri della funzione candidata.

Ai fini della descrizione del processo di chiamata, i membri della funzione sono divisi in due categorie:

*  Membri della funzione statica. Si tratta di costruttori di istanza, metodi statici, funzioni di accesso alle proprietà statiche e operatori definiti dall'utente. I membri della funzione statica sono sempre non virtuali.
*  Membri della funzione di istanza. Si tratta di metodi di istanza, funzioni di accesso alle proprietà dell'istanza e funzioni di accesso dell'indicizzatore. I membri della funzione di istanza non sono virtuali o virtuali e vengono sempre richiamati in una particolare istanza. L'istanza viene calcolata da un'espressione di istanza e diventa accessibile all'interno del membro della funzione come `this` ([questo accesso](expressions.md#this-access)).

L'elaborazione in fase di esecuzione della chiamata di un membro di funzione è costituita dai passaggi seguenti, in cui `M` è il membro della funzione e, se `M` è un membro di istanza, `E` è l'espressione dell'istanza:

*  Se `M` è un membro della funzione statica:
   * L'elenco di argomenti viene valutato come descritto negli [elenchi di argomenti](expressions.md#argument-lists).
   * viene richiamato `M`.

*  Se `M` è un membro della funzione di istanza dichiarato in un *value_type*:
   * `E` viene valutato. Se questa valutazione causa un'eccezione, non vengono eseguiti altri passaggi.
   * Se `E` non è classificato come variabile, viene creata una variabile locale temporanea di tipo `E` e il valore di `E` viene assegnato a tale variabile. `E` viene quindi riclassificato come riferimento a tale variabile locale temporanea. La variabile temporanea è accessibile come @no__t 0 entro @no__t 1, ma non in altro modo. Pertanto, solo quando `E` è una vera variabile, il chiamante può osservare le modifiche apportate da `M` a `this`.
   * L'elenco di argomenti viene valutato come descritto negli [elenchi di argomenti](expressions.md#argument-lists).
   * viene richiamato `M`. La variabile a cui fa riferimento `E` diventa la variabile a cui fa riferimento `this`.

*  Se `M` è un membro della funzione di istanza dichiarato in un *reference_type*:
   * `E` viene valutato. Se questa valutazione causa un'eccezione, non vengono eseguiti altri passaggi.
   * L'elenco di argomenti viene valutato come descritto negli [elenchi di argomenti](expressions.md#argument-lists).
   * Se il tipo di `E` è un *value_type*, viene eseguita una conversione boxing ([conversioni boxing](types.md#boxing-conversions)) per convertire `E` nel tipo `object` e `E` è considerato di tipo `object` nei passaggi seguenti. In questo caso, `M` può essere solo un membro di `System.Object`.
   * Il valore di `E` è verificato come valido. Se il valore di `E` è `null`, viene generata una `System.NullReferenceException` e non vengono eseguiti altri passaggi.
   * Viene determinata l'implementazione del membro della funzione da richiamare:
     * Se il tipo in fase di binding di `E` è un'interfaccia, il membro della funzione da richiamare è l'implementazione di `M` fornita dal tipo di runtime dell'istanza a cui viene fatto riferimento da `E`. Questo membro di funzione viene determinato applicando le regole di mapping dell'interfaccia ([mapping dell'interfaccia](interfaces.md#interface-mapping)) per determinare l'implementazione di `M` fornita dal tipo di runtime dell'istanza a cui fa riferimento `E`.
     * In caso contrario, se `M` è un membro di funzione virtuale, il membro della funzione da richiamare è l'implementazione di `M` fornita dal tipo di runtime dell'istanza a cui fa riferimento `E`. Questo membro di funzione viene determinato applicando le regole per determinare l'implementazione più derivata ([metodi virtuali](classes.md#virtual-methods)) di `M` rispetto al tipo di runtime dell'istanza a cui fa riferimento `E`.
     * In caso contrario, `M` è un membro di funzione non virtuale e il membro della funzione da richiamare è `M`.
   * Viene richiamata l'implementazione del membro di funzione determinata nel passaggio precedente. L'oggetto a cui fa riferimento `E` diventa l'oggetto a cui fa riferimento `this`.

#### <a name="invocations-on-boxed-instances"></a>Chiamate nelle istanze boxed

Un membro di funzione implementato in un *value_type* può essere richiamato tramite un'istanza boxed di *value_type* nelle situazioni seguenti:

*  Quando il membro della funzione è un `override` di un metodo ereditato dal tipo `object` e viene richiamato tramite un'espressione di istanza di tipo `object`.
*  Quando il membro della funzione è un'implementazione di un membro di funzione di interfaccia e viene richiamato tramite un'espressione di istanza di un *INTERFACE_TYPE*.
*  Quando il membro della funzione viene richiamato tramite un delegato.

In queste situazioni, l'istanza boxed viene considerata come contenente una variabile di *value_type*e questa variabile diventa la variabile a cui fa riferimento `this` all'interno della chiamata del membro della funzione. In particolare, ciò significa che quando un membro di funzione viene richiamato su un'istanza boxed, il membro della funzione può modificare il valore contenuto nell'istanza boxed.

## <a name="primary-expressions"></a>Espressioni principali:

Le espressioni primarie includono forme di espressioni più semplici.

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

Le espressioni primarie sono divise tra *array_creation_expression*s e *primary_no_array_creation_expression*s. Il trattamento di array-creation-expression in questo modo, anziché elencarlo insieme ad altre semplici forme di espressioni, consente alla grammatica di impedire codice potenzialmente confuso, ad esempio
```csharp
object o = new int[3][1];
```
che altrimenti verrebbe interpretato come
```csharp
object o = (new int[3])[1];
```

### <a name="literals"></a>Valori letterali

Un *primary_expression* costituito da un valore *letterale* ([valori letterali](lexical-structure.md#literals)) viene classificato come valore.


### <a name="interpolated-strings"></a>Stringhe interpolate

Un *interpolated_string_expression* è costituito da un segno `$` seguito da un valore letterale stringa normale o Verbatim, dove buchi, delimitati da `{` e `}`, racchiudere le espressioni e le specifiche di formattazione. Un'espressione stringa interpolata è il risultato di un *interpolated_string_literal* suddiviso in singoli token, come descritto in [valori letterali stringa interpolata](lexical-structure.md#interpolated-string-literals).

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

Il *constant_expression* in un'interpolazione deve avere una conversione implicita in `int`.

Un *interpolated_string_expression* è classificato come valore. Se viene immediatamente convertita in `System.IFormattable` o `System.FormattableString` con una conversione di stringa interpolata implicita ([conversioni implicite di stringhe](conversions.md#implicit-interpolated-string-conversions)interpolate), il tipo dell'espressione stringa interpolata è. In caso contrario, il tipo è `string`.

Se il tipo di una stringa interpolata è `System.IFormattable` o `System.FormattableString`, il significato è una chiamata a `System.Runtime.CompilerServices.FormattableStringFactory.Create`. Se il tipo è `string`, il significato dell'espressione è una chiamata a `string.Format`. In entrambi i casi, l'elenco di argomenti della chiamata è costituito da un valore letterale stringa di formato con segnaposto per ogni interpolazione e da un argomento per ogni espressione corrispondente ai segnaposto.

Il valore letterale stringa di formato viene costruito come indicato di seguito, dove `N` è il numero di interpolazioni in *interpolated_string_expression*:

*  Se un *interpolated_regular_string_whole* o un *interpolated_verbatim_string_whole* segue il segno di `$`, il valore letterale della stringa di formato è tale token.
*  In caso contrario, il valore letterale stringa di formato è costituito da: 
   *  Prima *interpolated_regular_string_start* o *interpolated_verbatim_string_start*
   *  Quindi, per ogni numero `I` da `0` a `N-1`: 
      * Rappresentazione decimale di `I`
      * Quindi, se l' *interpolazione* corrispondente dispone di un *constant_expression*, un `,` (virgola) seguito dalla rappresentazione decimale del valore di *constant_expression*
      * Quindi, *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_mid* o *interpolated_verbatim_string_end* immediatamente dopo l'interpolazione corrispondente.

Gli argomenti successivi sono semplicemente le *espressioni* delle *interpolazioni* (se presenti), nell'ordine.

TODO: esempi.


### <a name="simple-names"></a>Nomi semplici

Un *simple_name* è costituito da un identificatore, seguito facoltativamente da un elenco di argomenti di tipo:

```antlr
simple_name
    : identifier type_argument_list?
    ;
```

Un *simple_name* è nel formato `I` o nel formato `I<A1,...,Ak>`, dove `I` è un identificatore singolo e `<A1,...,Ak>` è un *type_argument_list*facoltativo. Se non viene specificato alcun *type_argument_list* , prendere in considerazione `K` come zero. *Simple_name* viene valutato e classificato come segue:

*  Se `K` è zero e il *simple_name* viene visualizzato all'interno di un *blocco* e *se lo spazio di dichiarazione*della variabile locale (o un *blocco*di inclusione) ([dichiarazioni](basic-concepts.md#declarations)) contiene una variabile locale, un parametro o una costante con nome @ no__t-6, il *simple_name* fa riferimento a tale variabile, parametro o costante locale ed è classificato come variabile o valore.
*  Se `K` è zero e *simple_name* viene visualizzato all'interno del corpo di una dichiarazione di metodo generico e se tale dichiarazione include un parametro di tipo con il nome @ no__t-2, *simple_name* fa riferimento a tale parametro di tipo.
*  In caso contrario, per ogni istanza digitare @ no__t-0 ([il tipo di istanza](classes.md#the-instance-type)), iniziando con il tipo di istanza della dichiarazione di tipo di inclusione immediata e continuando con il tipo di istanza di ogni classe contenitore o dichiarazione struct (se presente):
   *  Se `K` è zero e la dichiarazione di `T` include un parametro di tipo denominato @ no__t-2, il *simple_name* fa riferimento a tale parametro di tipo.
   *  In caso contrario, se la ricerca di un membro ([ricerca di membri](expressions.md#member-lookup)) di `I` in `T` con argomenti `K` @ no__t-4type genera una corrispondenza:
      * Se `T` è il tipo di istanza della classe o del tipo struct che lo contiene immediatamente e la ricerca identifica uno o più metodi, il risultato è un gruppo di metodi a cui è associata un'espressione di istanza di `this`. Se è stato specificato un elenco di argomenti di tipo, questo viene usato nella chiamata a un metodo generico (chiamate al[Metodo](expressions.md#method-invocations)).
      * In caso contrario, se `T` è il tipo di istanza della classe o del tipo struct immediatamente racchiuso, se la ricerca identifica un membro di istanza e se il riferimento si verifica all'interno del corpo di un costruttore di istanza, un metodo di istanza o una funzione di accesso dell'istanza, il risultato equivale a un accesso ai membri ([accesso ai membri](expressions.md#member-access)) nel formato `this.I`. Questo problema può verificarsi solo quando `K` è zero.
      * In caso contrario, il risultato è lo stesso di un accesso ai membri ([accesso ai membri](expressions.md#member-access)) nel formato `T.I` o `T.I<A1,...,Ak>`. In questo caso, si tratta di un errore in fase di binding per *simple_name* per fare riferimento a un membro di istanza.

*  In caso contrario, per ogni spazio dei nomi @ no__t-0, a partire dallo spazio dei nomi in cui si verifica *simple_name* , continuando con ogni spazio dei nomi che lo contiene (se presente) e terminando con lo spazio dei nomi globale, i passaggi seguenti vengono valutati fino a quando non viene individuata un'entità:
   *  Se `K` è zero e `I` è il nome di uno spazio dei nomi in @ no__t-2, quindi:
      * Se il percorso in cui si verifica *simple_name* è racchiuso da una dichiarazione dello spazio dei nomi per `N` e la dichiarazione dello spazio dei nomi contiene un *extern_alias_directive* o *using_alias_directive* che associa il nome @ no__t-4 a un spazio dei nomi o tipo, *simple_name* è ambiguo e si verifica un errore in fase di compilazione.
      * In caso contrario, *simple_name* fa riferimento allo spazio dei nomi denominato `I` in `N`.
   *  In caso contrario, se `N` contiene un tipo accessibile con nome @ no__t-1 e `K` @ no__t-3type parametri, quindi:
      * Se `K` è zero e la posizione in cui si verifica *simple_name* è racchiusa tra una dichiarazione dello spazio dei nomi per `N` e la dichiarazione dello spazio dei nomi contiene un *extern_alias_directive* o *using_alias_directive* che associa il nome @ no__t-5 con uno spazio dei nomi o un tipo, il *simple_name* è ambiguo e si verifica un errore in fase di compilazione.
      * In caso contrario, *namespace_or_type_name* fa riferimento al tipo costruito con gli argomenti di tipo specificati.
   *  In caso contrario, se il percorso in cui si verifica *simple_name* è racchiuso da una dichiarazione dello spazio dei nomi per @ no__t-1:
      * Se `K` è zero e la dichiarazione dello spazio dei nomi contiene un *extern_alias_directive* o *using_alias_directive* che associa il nome @ no__t-3 con uno spazio dei nomi o un tipo importato, *simple_name* fa riferimento a tale spazio dei nomi o tipo.
      * In caso contrario, se gli spazi dei nomi e le dichiarazioni di tipo importati da *using_namespace_directive*s e *using_static_directive*della dichiarazione dello spazio dei nomi contengono esattamente un tipo accessibile o un membro statico non di estensione con nome @ no__ i parametri t-2 e `K` @ no__t-4type, quindi *simple_name* fa riferimento a quel tipo o membro costruito con gli argomenti di tipo specificati.
      * In caso contrario, se gli spazi dei nomi e i tipi importati dai *using_namespace_directive*della dichiarazione dello spazio dei nomi contengono più di un tipo accessibile o un membro statico del metodo non di estensione con il nome @ no__t-1 e i parametri `K` @ no__t-3type, *simple_name* è ambiguo e si verifica un errore.

   Si noti che questo passaggio intero è esattamente parallelo al passaggio corrispondente nell'elaborazione di un *namespace_or_type_name* ([nomi di spazio dei nomi e di tipi](basic-concepts.md#namespace-and-type-names)).

*  In caso contrario, *simple_name* non è definito e si verifica un errore in fase di compilazione.


### <a name="parenthesized-expressions"></a>Espressioni racchiuse tra parentesi

Un *parenthesized_expression* è costituito da un' *espressione* racchiusa tra parentesi.

```antlr
parenthesized_expression
    : '(' expression ')'
    ;
```

Un *parenthesized_expression* viene valutato valutando l' *espressione* all'interno delle parentesi. Se l' *espressione* racchiusa tra parentesi denota uno spazio dei nomi o un tipo, si verificherà un errore in fase di compilazione. In caso contrario, il risultato di *parenthesized_expression* è il risultato della valutazione dell' *espressione*contenuta.

### <a name="member-access"></a>Accesso ai membri

Un *member_access* è costituito da un *primary_expression*, da un *predefined_type*o da un *qualified_alias_member*, seguito da un token "`.`", seguito da un *identificatore*, seguito facoltativamente da un *type_argument_list* .

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

La produzione *qualified_alias_member* è definita nei [qualificatori alias degli spazi dei nomi](namespaces.md#namespace-alias-qualifiers).

Un *member_access* è nel formato `E.I` o nel formato `E.I<A1, ..., Ak>`, dove `E` è un'espressione primaria, `I` è un identificatore singolo e `<A1, ..., Ak>` è un *type_argument_list*facoltativo. Se non viene specificato alcun *type_argument_list* , prendere in considerazione `K` come zero.

Un *member_access* con *primary_expression* di tipo `dynamic` è associato in modo dinamico ([associazione dinamica](expressions.md#dynamic-binding)). In questo caso, il compilatore classifica l'accesso ai membri come accesso alla proprietà di tipo `dynamic`. Le regole seguenti per determinare il significato di *member_access* vengono quindi applicate in fase di esecuzione, usando il tipo di runtime anziché il tipo in fase di compilazione di *primary_expression*. Se questa classificazione in fase di esecuzione conduce a un gruppo di metodi, l'accesso ai membri deve essere il *primary_expression* di un *invocation_expression*.

*Member_access* viene valutato e classificato come segue:

*  Se `K` è zero e `E` è uno spazio dei nomi e `E` contiene uno spazio dei nomi annidato denominato @ no__t-3, il risultato è tale spazio dei nomi.
*  In caso contrario, se `E` è uno spazio dei nomi e `E` contiene un tipo accessibile con i parametri Name @ no__t-2 e `K` @ no__t-4type, il risultato sarà il tipo costruito con gli argomenti di tipo specificati.
*  Se `E` è un *predefined_type* o un *primary_expression* Classificato come tipo, se `E` non è un parametro di tipo e se una ricerca di membri ([ricerca di membri](expressions.md#member-lookup)) di `I` in `E` con parametri `K` @ no__t-8type genera una corrispondenza, quindi `E.I` viene valutato e classificato come segue:
   *  Se `I` identifica un tipo, il risultato è il tipo costruito con gli argomenti di tipo specificati.
   *  Se `I` identifica uno o più metodi, il risultato è un gruppo di metodi senza espressione dell'istanza associata. Se è stato specificato un elenco di argomenti di tipo, questo viene usato nella chiamata a un metodo generico (chiamate al[Metodo](expressions.md#method-invocations)).
   *  Se `I` identifica una proprietà `static`, il risultato è l'accesso a una proprietà senza espressione dell'istanza associata.
   *  Se `I` identifica un campo `static`:
      * Se il campo è `readonly` e il riferimento si verifica all'esterno del costruttore statico della classe o dello struct in cui viene dichiarato il campo, il risultato è un valore, ovvero il valore del campo statico @ no__t-1 in @ no__t-2.
      * In caso contrario, il risultato è una variabile, ovvero il campo statico @ no__t-0 in @ no__t-1.
   *  Se `I` identifica un evento `static`:
      * Se il riferimento si verifica all'interno della classe o dello struct in cui viene dichiarato l'evento e l'evento è stato dichiarato senza *event_accessor_declarations* ([Events](classes.md#events)), `E.I` viene elaborato esattamente come se `I` fosse un campo statico.
      * In caso contrario, il risultato è un accesso a eventi senza espressione di istanza associata.
   *  Se `I` identifica una costante, il risultato è un valore, ovvero il valore della costante.
    * Se `I` identifica un membro di enumerazione, il risultato è un valore, ovvero il valore del membro di enumerazione.
    * In caso contrario, `E.I` è un riferimento al membro non valido e si verifica un errore in fase di compilazione.
*  Se `E` è un accesso a proprietà, l'accesso a un indicizzatore, una variabile o un valore, il tipo di che è @ no__t-1 e una ricerca di membri ([ricerca di membri](expressions.md#member-lookup)) di `I` in `T` con argomenti `K` @ no__t-6type genera una corrispondenza, viene valutato `E.I` e Classificato come segue:
   *  Prima di tutto, se `E` è un accesso a una proprietà o un indicizzatore, viene ottenuto il valore dell'accesso alla proprietà o all'indicizzatore ([valori di espressioni](expressions.md#values-of-expressions)) e `E` viene riclassificato come valore.
   *  Se `I` identifica uno o più metodi, il risultato è un gruppo di metodi a cui è associata un'espressione di istanza `E`. Se è stato specificato un elenco di argomenti di tipo, questo viene usato nella chiamata a un metodo generico (chiamate al[Metodo](expressions.md#method-invocations)).
   *  Se `I` identifica una proprietà dell'istanza,
      * Se `E` è `this`, `I` identifica una proprietà implementata automaticamente ([proprietà implementate automaticamente](classes.md#automatically-implemented-properties)) senza setter e il riferimento si verifica all'interno di un costruttore di istanza per un tipo di classe o struct `T`, quindi il risultato è una variabile, vale a dire il campo sottostante nascosto per la proprietà automatica fornita da `I` nell'istanza di `T` fornita da `this`.
      * In caso contrario, il risultato è l'accesso a una proprietà con un'espressione di istanza associata @ no__t-0.
   *  Se `T` è un *class_type* e `I` identifica un campo di istanza di tale *class_type*:
      * Se il valore di `E` è `null`, viene generata un'`System.NullReferenceException`.
      * In caso contrario, se il campo è `readonly` e il riferimento si verifica all'esterno di un costruttore di istanza della classe in cui viene dichiarato il campo, il risultato è un valore, ovvero il valore del campo @ no__t-1 nell'oggetto a cui fa riferimento @ no__t-2.
      * In caso contrario, il risultato è una variabile, ovvero il campo @ no__t-0 nell'oggetto a cui fa riferimento @ no__t-1.
   *  Se `T` è un *struct_type* e `I` identifica un campo di istanza di tale *struct_type*:
      * Se `E` è un valore o se il campo è `readonly` e il riferimento si verifica all'esterno di un costruttore di istanza dello struct in cui viene dichiarato il campo, il risultato è un valore, ovvero il valore del campo @ no__t-2 nell'istanza struct fornita da @ no__t-3.
      * In caso contrario, il risultato è una variabile, ovvero il campo @ no__t-0 nell'istanza struct fornita da @ no__t-1.
   *  Se `I` identifica un evento di istanza:
      * Se il riferimento si verifica all'interno della classe o dello struct in cui viene dichiarato l'evento e l'evento è stato dichiarato senza *event_accessor_declarations* ([Events](classes.md#events)) e il riferimento non viene eseguito come lato sinistro di un operatore `+=` o `-=` , quindi `E.I` viene elaborato esattamente come se `I` fosse un campo di istanza.
      * In caso contrario, il risultato è un accesso a eventi con un'espressione di istanza associata @ no__t-0.
*  In caso contrario, viene effettuato un tentativo di elaborare `E.I` come chiamata al metodo di estensione ([chiamate al metodo di estensione](expressions.md#extension-method-invocations)). Se l'operazione ha esito negativo, `E.I` è un riferimento a un membro non valido e si verifica un errore in fase di binding.

#### <a name="identical-simple-names-and-type-names"></a>Nomi semplici e nomi di tipi identici

In un accesso ai membri nel formato `E.I`, se `E` è un identificatore singolo e se il significato di `E` come *simple_name* ([nomi semplici](expressions.md#simple-names)) è una costante, un campo, una proprietà, una variabile locale o un parametro con lo stesso tipo del significato di `E` come *type_name* ([nomi di spazio dei nomi e tipi](basic-concepts.md#namespace-and-type-names)), quindi sono consentiti entrambi i significati di `E`. I due possibili significati di `E.I` non sono mai ambigui perché `I` deve necessariamente essere un membro del tipo `E` in entrambi i casi. In altre parole, la regola consente semplicemente l'accesso ai membri statici e ai tipi annidati di `E` in cui si è verificato un errore in fase di compilazione. Esempio:
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

#### <a name="grammar-ambiguities"></a>Ambiguità della grammatica

Le produzioni per *simple_name* ([nomi semplici](expressions.md#simple-names)) e *member_access* ([accesso ai membri](expressions.md#member-access)) possono dare luogo a ambiguità nella grammatica per le espressioni. Ad esempio, l'istruzione:
```csharp
F(G<A,B>(7));
```
può essere interpretato come una chiamata a `F` con due argomenti, `G < A` e `B > (7)`. In alternativa, può essere interpretato come una chiamata a `F` con un argomento, ovvero una chiamata a un metodo generico @ no__t-1 con due argomenti di tipo e un argomento regolare.

Se una sequenza di token può essere analizzata (nel contesto) come *simple_name* ([nomi semplici](expressions.md#simple-names)), *member_access* ([accesso ai membri](expressions.md#member-access)) o *pointer_member_access* ([accesso ai membri del puntatore](unsafe-code.md#pointer-member-access)) che termina con un *type_argument_ List* ([argomenti di tipo](types.md#type-arguments)): viene esaminato il token immediatamente successivo al token di chiusura `>`. Se è uno dei
```csharp
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
```
la *type_argument_list* viene quindi mantenuta come parte di *simple_name*, *member_access* o *pointer_member_access* e qualsiasi altra possibile analisi della sequenza di token viene eliminata. In caso contrario, *type_argument_list* non viene considerato parte di *simple_name*, *member_access* o *pointer_member_access*, anche se non è possibile analizzare la sequenza di token. Si noti che queste regole non vengono applicate durante l'analisi di un *type_argument_list* in un *namespace_or_type_name* ([nomi di spazio dei nomi e di tipo](basic-concepts.md#namespace-and-type-names)). L'istruzione
```csharp
F(G<A,B>(7));
```
in base a questa regola, sarà interpretato come una chiamata a `F` con un argomento, ovvero una chiamata a un metodo generico `G` con due argomenti di tipo e un argomento normale. Istruzioni
```csharp
F(G < A, B > 7);
F(G < A, B >> 7);
```
ogni oggetto verrà interpretato come una chiamata a `F` con due argomenti. L'istruzione
```csharp
x = F < A > +y;
```
verrà interpretato come un operatore minore di, un operatore maggiore di e un operatore unario più, come se l'istruzione fosse stata scritta `x = (F < A) > (+y)`, anziché come *simple_name* con un *type_argument_list* seguito da un operatore binario più. Nell'istruzione
```csharp
x = y is C<T> + z;
```
i token `C<T>` vengono interpretati come *namespace_or_type_name* con *type_argument_list*.

### <a name="invocation-expressions"></a>Espressioni di chiamata

Viene usato un *invocation_expression* per richiamare un metodo.

```antlr
invocation_expression
    : primary_expression '(' argument_list? ')'
    ;
```

Un *invocation_expression* è associato in modo dinamico ([associazione dinamica](expressions.md#dynamic-binding)) se è presente almeno uno dei seguenti elementi:

* Il *primary_expression* ha un tipo in fase di compilazione `dynamic`.
* Almeno un argomento del *argument_list* facoltativo dispone del tipo in fase di compilazione `dynamic` e *primary_expression* non dispone di un tipo delegato.

In questo caso il compilatore classifica *invocation_expression* come valore di tipo `dynamic`. Le regole seguenti per determinare il significato di *invocation_expression* vengono quindi applicate in fase di esecuzione, usando il tipo in fase di esecuzione anziché il tipo in fase di compilazione di quelli di *primary_expression* e gli argomenti che hanno il tipo in fase di compilazione @no_ _T-2. Se il *primary_expression* non dispone di un tipo in fase di compilazione `dynamic`, la chiamata al metodo viene sottoposta a un controllo del tempo di compilazione limitato come descritto nel [controllo della risoluzione dell'overload dinamico in fase di](expressions.md#compile-time-checking-of-dynamic-overload-resolution)compilazione.

Il *primary_expression* di un *invocation_expression* deve essere un gruppo di metodi o un valore di *delegate_type*. Se *primary_expression* è un gruppo di metodi, *invocation_expression* è una chiamata al metodo ([chiamate al metodo](expressions.md#method-invocations)). Se *primary_expression* è un valore di *delegate_type*, *invocation_expression* è una chiamata a un delegato (chiamate di[delegati](expressions.md#delegate-invocations)). Se *primary_expression* non è né un gruppo di metodi né un valore di *delegate_type*, si verifica un errore in fase di binding.

Il *argument_list* facoltativo ([elenchi di argomenti](expressions.md#argument-lists)) fornisce valori o riferimenti a variabili per i parametri del metodo.

Il risultato della valutazione di un *invocation_expression* è classificato nel modo seguente:

*  Se *invocation_expression* richiama un metodo o un delegato che restituisce `void`, il risultato è Nothing. Un'espressione classificata come Nothing è consentita solo nel contesto di un *statement_expression* ([istruzioni Expression](statements.md#expression-statements)) o come corpo di un *lambda_expression* ([espressioni di funzione anonime](expressions.md#anonymous-function-expressions)). In caso contrario, si verifica un errore in fase di binding.
*  In caso contrario, il risultato è un valore del tipo restituito dal metodo o dal delegato.

#### <a name="method-invocations"></a>Chiamate al metodo

Per una chiamata al metodo, il *primary_expression* di *invocation_expression* deve essere un gruppo di metodi. Il gruppo di metodi identifica il metodo da richiamare o il set di metodi di overload da cui scegliere un metodo specifico da richiamare. Nel secondo caso, la determinazione del metodo specifico da richiamare si basa sul contesto fornito dai tipi degli argomenti in *argument_list*.

L'elaborazione in fase di binding di una chiamata al metodo nel formato `M(A)`, dove `M` è un gruppo di metodi (probabilmente incluso un *type_argument_list*) e `A` è un *argument_list*facoltativo, è costituito dai passaggi seguenti:

*  Viene costruito il set di metodi candidati per la chiamata al metodo. Per ogni metodo `F` associato al gruppo di metodi `M`:
   *  Se `F` è non generico, `F` è un candidato nei casi seguenti:
      * `M` non ha un elenco di argomenti di tipo e
      * `F` è applicabile rispetto a `A` ([membro della funzione applicabile](expressions.md#applicable-function-member)).
   *  Se `F` è generico e `M` non ha un elenco di argomenti di tipo, `F` è un candidato quando:
      * L'inferenza del tipo ([inferenza del tipo](expressions.md#type-inference)) ha esito positivo, deducendo un elenco di argomenti di tipo per la chiamata e
      * Una volta che gli argomenti di tipo dedotti vengono sostituiti con i parametri di tipo di metodo corrispondenti, tutti i tipi costruiti nell'elenco di parametri di F soddisfano i vincoli ([vincoli soddisfacenti](types.md#satisfying-constraints)) e l'elenco di parametri di `F` è applicabile con rispetto a `A` ([membro della funzione applicabile](expressions.md#applicable-function-member)).
   *  Se `F` è generico e `M` include un elenco di argomenti di tipo, `F` è un candidato nei casi seguenti:
      * `F` ha lo stesso numero di parametri di tipo di metodo forniti nell'elenco di argomenti di tipo e
      * Una volta che gli argomenti di tipo vengono sostituiti con i parametri di tipo di metodo corrispondenti, tutti i tipi costruiti nell'elenco di parametri di F soddisfano i vincoli ([vincoli soddisfacenti](types.md#satisfying-constraints)) e l'elenco dei parametri di `F` è applicabile rispetto a `A` ([membro della funzione applicabile](expressions.md#applicable-function-member)).
*  Il set di metodi candidati è ridotto in modo che contenga solo metodi dei tipi più derivati: Per ogni metodo `C.F` nel set, dove `C` è il tipo in cui viene dichiarato il metodo `F`, tutti i metodi dichiarati in un tipo di base di `C` vengono rimossi dal set. Inoltre, se `C` è un tipo di classe diverso da `object`, tutti i metodi dichiarati in un tipo di interfaccia vengono rimossi dal set. Questa seconda regola ha effetto solo quando il gruppo di metodi è il risultato di una ricerca di membri su un parametro di tipo con una classe di base efficace diversa da Object e un set di interfacce effettive non vuote.
*  Se il set di metodi candidati risultante è vuoto, l'ulteriore elaborazione nei passaggi seguenti viene abbandonata e viene invece eseguito un tentativo di elaborare la chiamata come chiamata al metodo di estensione (chiamate al[metodo di estensione](expressions.md#extension-method-invocations)). Se l'operazione ha esito negativo, non esiste alcun metodo applicabile e si verifica un errore in fase di binding.
*  Il metodo migliore del set di metodi candidati viene identificato mediante le regole di risoluzione dell'overload della [risoluzione dell'overload](expressions.md#overload-resolution). Se non è possibile identificare un singolo metodo migliore, la chiamata al metodo è ambigua e si verifica un errore in fase di binding. Quando si esegue la risoluzione dell'overload, i parametri di un metodo generico vengono considerati dopo la sostituzione degli argomenti di tipo (forniti o dedotti) per i parametri di tipo di metodo corrispondenti.
*  Viene eseguita la convalida finale del metodo migliore scelto:
   * Il metodo viene convalidato nel contesto del gruppo di metodi: Se il metodo migliore è un metodo statico, il gruppo di metodi deve essere risultato da un *simple_name* o da un *member_access* tramite un tipo. Se il metodo migliore è un metodo di istanza, il gruppo di metodi deve essere risultato da un *simple_name*, un *member_access* tramite una variabile o un valore oppure un *base_access*. Se nessuno di questi requisiti è true, si verifica un errore in fase di binding.
   * Se il metodo migliore è un metodo generico, gli argomenti di tipo (forniti o dedotti) vengono controllati in base ai vincoli ([vincoli di soddisfazione](types.md#satisfying-constraints)) dichiarati nel metodo generico. Se un argomento di tipo non soddisfa i vincoli corrispondenti nel parametro di tipo, si verificherà un errore in fase di binding.

Una volta che un metodo è stato selezionato e convalidato in fase di binding con i passaggi precedenti, l'effettiva chiamata in fase di esecuzione viene elaborata in base alle regole della chiamata del membro della funzione descritta nel [controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

L'effetto intuitivo delle regole di risoluzione descritte in precedenza è il seguente: Per individuare il metodo specifico richiamato da una chiamata al metodo, iniziare con il tipo indicato dalla chiamata al metodo e procedere alla catena di ereditarietà finché non viene trovata almeno una dichiarazione di metodo applicabile, accessibile e non di override. Eseguire quindi l'inferenza del tipo e la risoluzione dell'overload sul set di metodi applicabili, accessibili e non di override dichiarati in quel tipo e richiamare il metodo selezionato. Se non è stato trovato alcun metodo, provare invece a elaborare la chiamata come chiamata al metodo di estensione.

#### <a name="extension-method-invocations"></a>Chiamate al metodo di estensione

In una chiamata al metodo ([chiamate sulle istanze boxed](expressions.md#invocations-on-boxed-instances)) di uno dei form
```csharp
expr . identifier ( )

expr . identifier ( args )

expr . identifier < typeargs > ( )

expr . identifier < typeargs > ( args )
```
Se la normale elaborazione della chiamata non trova metodi applicabili, viene effettuato un tentativo di elaborare il costrutto come una chiamata al metodo di estensione. Se *expr* o uno qualsiasi degli *argomenti* ha un tipo in fase di compilazione `dynamic`, i metodi di estensione non verranno applicati.

L'obiettivo è trovare il migliore *type_name* `C`, in modo che possa essere eseguita la chiamata al metodo statico corrispondente:
```csharp
C . identifier ( expr )

C . identifier ( expr , args )

C . identifier < typeargs > ( expr )

C . identifier < typeargs > ( expr , args )
```

Un metodo di estensione `Ci.Mj` è ***idoneo*** se:

*  `Ci` è una classe non generica non annidata
*  Il nome di `Mj` è *identificatore*
*  `Mj` è accessibile e applicabile quando viene applicato agli argomenti come metodo statico, come illustrato in precedenza.
*  Un'identità implicita, un riferimento o una conversione boxing esiste da *expr* al tipo del primo parametro di `Mj`.

La ricerca di `C` procede come segue:

*  A partire dalla dichiarazione dello spazio dei nomi di inclusione più vicina, continuando con ogni dichiarazione dello spazio dei nomi di inclusione e terminando con l'unità di compilazione che lo contiene, vengono eseguiti tentativi successivi per trovare un set candidato di metodi di estensione:
   * Se lo spazio dei nomi o l'unità di compilazione specificata contiene direttamente dichiarazioni di tipo non generico `Ci` con i metodi di estensione idonei `Mj`, il set di tali metodi di estensione è il set candidato.
   * Se i tipi `Ci` importati da *using_static_declarations* e dichiarati direttamente negli spazi dei nomi importati da *using_namespace_directive*nello spazio dei nomi o nell'unità di compilazione specificata contengono direttamente i metodi di estensione idonei `Mj`, il set di questi metodi di estensione è il set candidato.
*  Se non viene trovato alcun set candidato in alcuna dichiarazione dello spazio dei nomi che lo contiene o in un'unità di compilazione, si verifica un errore in fase di compilazione.
*  In caso contrario, la risoluzione dell'overload viene applicata al set candidato come descritto in ([risoluzione dell'overload](expressions.md#overload-resolution)). Se non viene trovato alcun metodo migliore, si verifica un errore in fase di compilazione.
*  `C` è il tipo all'interno del quale il metodo migliore viene dichiarato come metodo di estensione.

Utilizzando `C` come destinazione, la chiamata al metodo viene quindi elaborata come chiamata a un metodo statico ([controllo della fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).

Le regole precedenti indicano che i metodi di istanza hanno la precedenza sui metodi di estensione, che i metodi di estensione disponibili nelle dichiarazioni dello spazio dei nomi interno hanno la precedenza sui metodi di estensione disponibili nelle dichiarazioni dello spazio dei nomi esterno e tale estensione i metodi dichiarati direttamente in uno spazio dei nomi hanno la precedenza sui metodi di estensione importati nello stesso spazio dei nomi con una direttiva namespace using. Esempio:
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

Nell'esempio, il metodo `B` ha la precedenza sul primo metodo di estensione e il metodo di `C` ha la precedenza su entrambi i metodi di estensione.

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
```console
E.F(1)
D.G(2)
C.H(3)
```
`D.G` ha la precedenza su `C.G` e `E.F` ha la precedenza su `D.F` e `C.F`.

#### <a name="delegate-invocations"></a>Chiamate di delegati

Per la chiamata di un delegato, il *primary_expression* di *invocation_expression* deve essere un valore di *delegate_type*. Inoltre, considerando che *delegate_type* è un membro di funzione con lo stesso elenco di parametri di *delegate_type*, *delegate_type* deve essere applicabile ([membro della funzione applicabile](expressions.md#applicable-function-member)) rispetto a *argument_ elenco* di *invocation_expression*.

L'elaborazione in fase di esecuzione di una chiamata del delegato nel formato `D(A)`, dove `D` è un *primary_expression* di *delegate_type* e `A` è un *argument_list*facoltativo, è costituito dai passaggi seguenti:

*  `D` viene valutato. Se questa valutazione causa un'eccezione, non vengono eseguiti altri passaggi.
*  Il valore di `D` è verificato come valido. Se il valore di `D` è `null`, viene generata una `System.NullReferenceException` e non vengono eseguiti altri passaggi.
*  In caso contrario, `D` è un riferimento a un'istanza di delegato. Le chiamate del membro della funzione ([controllo della fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) vengono eseguite su ognuna delle entità chiamabili nell'elenco chiamate del delegato. Per le entità richiamabili costituite da un'istanza e da un metodo di istanza, l'istanza per la chiamata è l'istanza contenuta nell'entità chiamabile.

### <a name="element-access"></a>Accesso agli elementi

Un *element_access* è costituito da un *primary_no_array_creation_expression*, seguito da un token "`[`", seguito da un *argument_list*, seguito da un token "`]`". Il *argument_list* è costituito da uno o più *argomenti*, separati da virgole.

```antlr
element_access
    : primary_no_array_creation_expression '[' expression_list ']'
    ;
```

Il *argument_list* di un *element_access* non può contenere argomenti `ref` o `out`.

Un *element_access* è associato in modo dinamico ([associazione dinamica](expressions.md#dynamic-binding)) se è presente almeno uno dei seguenti elementi:

* Il *primary_no_array_creation_expression* ha un tipo in fase di compilazione `dynamic`.
* Almeno un'espressione di *argument_list* ha un tipo in fase di compilazione `dynamic` e *primary_no_array_creation_expression* non dispone di un tipo di matrice.

In questo caso il compilatore classifica *element_access* come valore di tipo `dynamic`. Le regole seguenti per determinare il significato di *element_access* vengono quindi applicate in fase di esecuzione, usando il tipo di runtime anziché il tipo in fase di compilazione di quelle di *primary_no_array_creation_expression* e *argument_list* espressioni che hanno il tipo in fase di compilazione `dynamic`. Se il *primary_no_array_creation_expression* non dispone di un tipo in fase di compilazione `dynamic`, l'accesso agli elementi subisce un controllo del tempo di compilazione limitato, come descritto in [controllo in fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

Se il *primary_no_array_creation_expression* di un *element_access* è un valore di *array_type*, *element_access* è un accesso alla matrice ([accesso alla matrice](expressions.md#array-access)). In caso contrario, *primary_no_array_creation_expression* deve essere una variabile o un valore di una classe, uno struct o un tipo di interfaccia con uno o più membri dell'indicizzatore, nel qual caso *element_access* è un accesso all'indicizzatore ([accesso all'indicizzatore](expressions.md#indexer-access)).

#### <a name="array-access"></a>Accesso a matrici

Per l'accesso a una matrice, *primary_no_array_creation_expression* di *element_access* deve essere un valore di *array_type*. Inoltre, l' *argument_list* di un accesso alla matrice non può contenere argomenti denominati. Il numero di espressioni in *argument_list* deve corrispondere a quello di *array_type*e ogni espressione deve essere di tipo `int`, `uint`, `long`, `ulong` oppure deve essere convertibile in modo implicito in uno o più di questi tipi.

Il risultato della valutazione di un accesso alla matrice è una variabile del tipo di elemento della matrice, ovvero l'elemento di matrice selezionato dai valori delle espressioni in *argument_list*.

L'elaborazione in fase di esecuzione di un accesso alla matrice nel formato `P[A]`, dove `P` è un *primary_no_array_creation_expression* di *array_type* e `A` è un *argument_list*, è costituito dai passaggi seguenti:

*  `P` viene valutato. Se questa valutazione causa un'eccezione, non vengono eseguiti altri passaggi.
*  Le espressioni di indice di *argument_list* vengono valutate in ordine, da sinistra a destra. Dopo la valutazione di ogni espressione di indice, viene eseguita una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) a uno dei seguenti tipi: `int`, `uint`, `long`, `ulong`. Viene scelto il primo tipo in questo elenco per cui è stata scelta una conversione implicita. Se, ad esempio, l'espressione dell'indice è di tipo `short`, viene eseguita una conversione implicita in `int`, poiché le conversioni implicite da `short` a `int` e da `short` a `long` sono possibili. Se la valutazione di un'espressione di indice o della successiva conversione implicita genera un'eccezione, non vengono valutate ulteriori espressioni di indice e non vengono eseguiti altri passaggi.
*  Il valore di `P` è verificato come valido. Se il valore di `P` è `null`, viene generata una `System.NullReferenceException` e non vengono eseguiti altri passaggi.
*  Il valore di ogni espressione in *argument_list* viene confrontato con i limiti effettivi di ogni dimensione dell'istanza di matrice a cui fa riferimento `P`. Se uno o più valori non sono compresi nell'intervallo, viene generato un `System.IndexOutOfRangeException` e non vengono eseguiti altri passaggi.
*  Il percorso dell'elemento della matrice fornito dalle espressioni di indice viene calcolato e questo percorso diventa il risultato dell'accesso alla matrice.

#### <a name="indexer-access"></a>Accesso all'indicizzatore

Per un accesso all'indicizzatore, *primary_no_array_creation_expression* di *element_access* deve essere una variabile o un valore di una classe, uno struct o un tipo di interfaccia e questo tipo deve implementare uno o più indicizzatori applicabili rispetto al *argument_list* di *element_access*.

L'elaborazione in fase di binding di un accesso all'indicizzatore nel formato `P[A]`, dove `P` è un *primary_no_array_creation_expression* di una classe, uno struct o un tipo di interfaccia `T` e `A` è un *argument_list*, è costituito dagli elementi seguenti: passaggi

*  Viene costruito il set di indicizzatori fornito da `T`. Il set è costituito da tutti gli indicizzatori dichiarati in `T` o da un tipo di base di `T` che non sono dichiarazioni `override` e sono accessibili nel contesto corrente ([accesso ai membri](basic-concepts.md#member-access)).
*  Il set viene ridotto agli indicizzatori applicabili e non nascosti da altri indicizzatori. Le regole seguenti vengono applicate a ogni indicizzatore `S.I` nel set, dove `S` è il tipo in cui viene dichiarato l'indicizzatore `I`:
   * Se `I` non è applicabile rispetto a `A` ([membro della funzione applicabile](expressions.md#applicable-function-member)), `I` viene rimosso dal set.
   * Se `I` è applicabile rispetto a `A` ([membro della funzione applicabile](expressions.md#applicable-function-member)), tutti gli indicizzatori dichiarati in un tipo di base di `S` vengono rimossi dal set.
   * Se `I` è applicabile rispetto a `A` ([membro della funzione applicabile](expressions.md#applicable-function-member)) e `S` è un tipo di classe diverso da `object`, tutti gli indicizzatori dichiarati in un'interfaccia vengono rimossi dal set.
*  Se il set risultante di indicizzatori candidati è vuoto, non esiste alcun indicizzatore applicabile e si verifica un errore in fase di binding.
*  Il migliore indicizzatore del set di indicizzatori candidati viene identificato usando le regole di risoluzione dell'overload della [risoluzione dell'overload](expressions.md#overload-resolution). Se non è possibile identificare un singolo indicizzatore migliore, l'accesso dell'indicizzatore è ambiguo e si verifica un errore in fase di binding.
*  Le espressioni di indice di *argument_list* vengono valutate in ordine, da sinistra a destra. Il risultato dell'elaborazione dell'accesso dell'indicizzatore è un'espressione classificata come accesso all'indicizzatore. L'espressione di accesso all'indicizzatore fa riferimento all'indicizzatore determinato nel passaggio precedente e ha un'espressione di istanza associata di `P` e un elenco di argomenti associato di `A`.

A seconda del contesto in cui viene usato, un accesso all'indicizzatore causa la chiamata della funzione di accesso *Get* o della funzione di accesso *set* dell'indicizzatore. Se l'accesso dell'indicizzatore è la destinazione di un'assegnazione, viene richiamata la *funzione di accesso set* per assegnare un nuovo valore ([assegnazione semplice](expressions.md#simple-assignment)). In tutti gli altri casi, viene richiamata la *funzione di accesso get* per ottenere il valore corrente ([valori di espressioni](expressions.md#values-of-expressions)).

### <a name="this-access"></a>Questo accesso

Un *THIS_ACCESS* è costituito dalla parola riservata `this`.

```antlr
this_access
    : 'this'
    ;
```

Un *THIS_ACCESS* è consentito solo nel *blocco* di un costruttore di istanza, un metodo di istanza o una funzione di accesso di istanza. Ha uno dei significati seguenti:

*  Quando `this` viene usato in un *primary_expression* all'interno di un costruttore di istanza di una classe, viene classificato come valore. Il tipo di valore è il tipo di istanza ([il tipo di istanza](classes.md#the-instance-type)) della classe in cui si verifica l'utilizzo e il valore è un riferimento all'oggetto che viene costruito.
*  Quando `this` viene usato in un *primary_expression* all'interno di un metodo di istanza o di una funzione di accesso dell'istanza di una classe, viene classificato come valore. Il tipo di valore è il tipo di istanza ([il tipo di istanza](classes.md#the-instance-type)) della classe in cui si verifica l'utilizzo e il valore è un riferimento all'oggetto per il quale è stato richiamato il metodo o la funzione di accesso.
*  Quando `this` viene usato in un *primary_expression* all'interno di un costruttore di istanza di uno struct, viene classificato come variabile. Il tipo della variabile è il tipo di istanza ([il tipo di istanza](classes.md#the-instance-type)) dello struct in cui si verifica l'utilizzo e la variabile rappresenta lo struct costruito. La variabile `this` di un costruttore di istanza di uno struct si comporta esattamente come un parametro `out` del tipo struct, in particolare questo significa che la variabile deve essere assegnata definitivamente in ogni percorso di esecuzione del costruttore di istanza.
*  Quando `this` viene usato in un *primary_expression* all'interno di un metodo di istanza o di una funzione di accesso dell'istanza di uno struct, viene classificato come variabile. Il tipo della variabile è il tipo di istanza ([il tipo di istanza](classes.md#the-instance-type)) dello struct in cui si verifica l'utilizzo.
   * Se il metodo o la funzione di accesso non è un iteratore ([iteratori](classes.md#iterators)), la variabile `this` rappresenta lo struct per il quale è stato richiamato il metodo o la funzione di accesso e si comporta esattamente come un parametro `ref` del tipo struct.
   * Se il metodo o la funzione di accesso è un iteratore, la variabile `this` rappresenta una copia dello struct per cui è stato richiamato il metodo o la funzione di accesso e si comporta esattamente come un parametro di valore del tipo struct.

L'uso di `this` in un *primary_expression* in un contesto diverso da quelli elencati in precedenza è un errore in fase di compilazione. In particolare, non è possibile fare riferimento a `this` in un metodo statico, una funzione di accesso a una proprietà statica o in un *variable_initializer* di una dichiarazione di campo.

### <a name="base-access"></a>Accesso di base

Un *base_access* è costituito dalla parola riservata `base` seguita da un token "`.`" e da un identificatore o da un *argument_list* racchiuso tra parentesi quadre:

```antlr
base_access
    : 'base' '.' identifier
    | 'base' '[' expression_list ']'
    ;
```

Un *base_access* viene usato per accedere ai membri della classe di base nascosti da membri denominati in modo analogo nella classe o nello struct corrente. Un *base_access* è consentito solo nel *blocco* di un costruttore di istanza, un metodo di istanza o una funzione di accesso di istanza. Quando `base.I` si verifica in una classe o in uno struct, `I` deve indicare un membro della classe di base di tale classe o struct. Analogamente, quando si verifica `base[E]` in una classe, è necessario che nella classe di base sia presente un indicizzatore applicabile.

In fase di binding, le espressioni *base_access* nel formato `base.I` e `base[E]` vengono valutate esattamente come se fossero scritte `((B)this).I` e `((B)this)[E]`, dove `B` è la classe di base della classe o dello struct in cui si verifica il costrutto. Pertanto, `base.I` e `base[E]` corrispondono a `this.I` e `this[E]`, eccetto `this` viene visualizzato come un'istanza della classe di base.

Quando un *base_access* fa riferimento a un membro di una funzione virtuale (un metodo, una proprietà o un indicizzatore), la determinazione del membro della funzione da richiamare in fase di esecuzione ([controllo della fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) viene modificata. Il membro della funzione richiamato è determinato dalla ricerca dell'implementazione più derivata ([metodi virtuali](classes.md#virtual-methods)) del membro della funzione rispetto a `B` (anziché rispetto al tipo in fase di esecuzione di `this`, in quanto sarebbe usuale in un accesso non di base). . Quindi, all'interno di un `override` di un membro della funzione `virtual`, è possibile usare un *base_access* per richiamare l'implementazione ereditata del membro della funzione. Se il membro della funzione a cui fa riferimento un *base_access* è astratto, si verifica un errore in fase di binding.

### <a name="postfix-increment-and-decrement-operators"></a>Operatori di incremento e decremento in forma suffissa

```antlr
post_increment_expression
    : primary_expression '++'
    ;

post_decrement_expression
    : primary_expression '--'
    ;
```

L'operando di un'operazione di incremento o di decremento suffisso deve essere un'espressione classificata come variabile, un accesso a una proprietà o un accesso all'indicizzatore. Il risultato dell'operazione è un valore dello stesso tipo dell'operando.

Se il *primary_expression* ha il tipo in fase di compilazione `dynamic`, l'operatore è associato in modo dinamico ([associazione dinamica](expressions.md#dynamic-binding)), *post_increment_expression* o *post_decrement_expression* ha il tipo in fase di compilazione `dynamic` e le regole seguenti vengono applicate in fase di esecuzione usando il tipo di runtime di *primary_expression*.

Se l'operando di un'operazione di incremento o di decremento suffisso è un accesso a una proprietà o a un indicizzatore, la proprietà o l'indicizzatore deve avere entrambe le funzioni di accesso `get` e `set`. In caso contrario, si verificherà un errore in fase di binding.

Viene applicata la risoluzione dell'overload dell'operatore unario ([risoluzione dell'overload dell'operatore unario](expressions.md#unary-operator-overload-resolution)) per selezionare l'implementazione di un operatore specifico. Per i tipi seguenti sono disponibili operatori predefiniti `++` e `--`: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, 0, 1, 2, 3 e qualsiasi tipo enum. Gli operatori `++` predefiniti restituiscono il valore prodotto aggiungendo 1 all'operando e gli operatori `--` predefiniti restituiscono il valore prodotto sottraendo 1 dall'operando. In un contesto `checked`, se il risultato di questa operazione di aggiunta o sottrazione non è compreso nell'intervallo del tipo di risultato e il tipo di risultato è un tipo integrale o un tipo enum, viene generata un'`System.OverflowException`.

L'elaborazione in fase di esecuzione di un'operazione di incremento o decremento suffisso nel formato `x++` o `x--` prevede i passaggi seguenti:

*   Se `x` è classificato come variabile:
    * `x` viene valutato per produrre la variabile.
    * Il valore di `x` viene salvato.
    * L'operatore selezionato viene richiamato con il valore salvato di `x` come argomento.
    * Il valore restituito dall'operatore viene archiviato nella posizione specificata dalla valutazione di `x`.
    * Il valore salvato di `x` diventa il risultato dell'operazione.
*   Se `x` è classificato come accesso a una proprietà o a un indicizzatore:
    * L'espressione dell'istanza (se `x` non è `static`) e l'elenco di argomenti (se `x` è un accesso all'indicizzatore) associato a `x` vengono valutati e i risultati vengono utilizzati nelle chiamate di funzione di accesso `get` e `set` successive.
    * Viene richiamata la funzione di accesso `get` di `x` e viene salvato il valore restituito.
    * L'operatore selezionato viene richiamato con il valore salvato di `x` come argomento.
    * La funzione di accesso `set` di `x` viene richiamata con il valore restituito dall'operatore come argomento `value`.
    * Il valore salvato di `x` diventa il risultato dell'operazione.

Gli operatori `++` e `--` supportano anche la notazione del prefisso ([operatori di incremento e decremento del prefisso](expressions.md#prefix-increment-and-decrement-operators)). In genere, il risultato di `x++` o `x--` è il valore di `x` prima dell'operazione, mentre il risultato di `++x` o `--x` è il valore di `x` dopo l'operazione. In entrambi i casi, `x` ha lo stesso valore dopo l'operazione.

Un'implementazione `operator ++` o `operator --` può essere richiamata usando la notazione suffissa o prefisso. Non è possibile avere implementazioni di operatori separate per le due notazioni.

### <a name="the-new-operator"></a>Operatore new

L'operatore `new` viene utilizzato per creare nuove istanze di tipi.

Esistono tre forme di espressioni `new`:

*  Le espressioni di creazione di oggetti vengono usate per creare nuove istanze di tipi di classe e tipi di valore.
*  Le espressioni di creazione di matrici vengono usate per creare nuove istanze di tipi matrice.
*  Le espressioni di creazione dei delegati vengono usate per creare nuove istanze di tipi delegati.

L'operatore `new` implica la creazione di un'istanza di un tipo, ma non implica necessariamente l'allocazione dinamica della memoria. In particolare, le istanze di tipi valore non richiedono memoria aggiuntiva oltre le variabili in cui risiedono e non si verificano allocazioni dinamiche quando si usa `new` per creare istanze di tipi di valore.

#### <a name="object-creation-expressions"></a>Espressioni di creazione di oggetti

Un *object_creation_expression* viene usato per creare una nuova istanza di *class_type* o *value_type*.

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

Il *tipo* di *object_creation_expression* deve essere *class_type*, *value_type* o *type_parameter*. Il *tipo* non può essere un *class_type*`abstract`.

La *argument_list* facoltativa[(elenchi di argomenti](expressions.md#argument-lists)) è consentita solo se il *tipo* è *class_type* o *struct_type*.

Un'espressione di creazione di oggetti può omettere l'elenco di argomenti del costruttore e racchiudere le parentesi purché includa un inizializzatore di oggetto o un inizializzatore di raccolta. Omettere l'elenco di argomenti del costruttore e le parentesi di inclusione equivale a specificare un elenco di argomenti vuoto.

L'elaborazione di un'espressione di creazione di un oggetto che include un inizializzatore di oggetto o un inizializzatore di raccolta consiste nella prima elaborazione del costruttore di istanza e quindi nell'elaborazione delle inizializzazioni di membri o elementi specificate dall'inizializzatore di oggetto ([ Inizializzatori di oggetto](expressions.md#object-initializers)) o inizializzatori di raccolta ([inizializzatori di insieme](expressions.md#collection-initializers)).

Se uno degli argomenti nel *argument_list* facoltativo dispone del tipo in fase di compilazione `dynamic`, il *object_creation_expression* è associato in modo dinamico ([associazione dinamica](expressions.md#dynamic-binding)) e le regole seguenti vengono applicate in fase di esecuzione tramite il runtime tipo degli argomenti di *argument_list* con il tipo di fase di compilazione `dynamic`. Tuttavia, la creazione dell'oggetto viene sottoposta a un controllo del tempo di compilazione limitato, come descritto in [controllo in fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution).

L'elaborazione in fase di binding di un *object_creation_expression* nel formato `new T(A)`, dove `T` è un *class_type* o *value_type* e `A` è un *argument_list*facoltativo, è costituito dai passaggi seguenti:

*   Se `T` è *value_type* e `A` non è presente:
    * *Object_creation_expression* è una chiamata del costruttore predefinito. Il risultato di *object_creation_expression* è un valore di tipo `T`, ovvero il valore predefinito per `T` come definito nel [tipo System. ValueType](types.md#the-systemvaluetype-type).
*   In caso contrario, se `T` è *type_parameter* e `A` non è presente:
    * Se non è stato specificato alcun vincolo del tipo di valore o del costruttore ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)) per `T`, si verifica un errore in fase di binding.
    * Il risultato di *object_creation_expression* è un valore del tipo in fase di esecuzione a cui è stato associato il parametro di tipo, ovvero il risultato della chiamata del costruttore predefinito di quel tipo. Il tipo in fase di esecuzione può essere un tipo riferimento o un tipo valore.
*   In caso contrario, se `T` è un *class_type* o un *struct_type*:
    * Se `T` è un *class_type*`abstract`, si verifica un errore in fase di compilazione.
    * Il costruttore di istanza da richiamare viene determinato mediante le regole di risoluzione dell'overload della [risoluzione dell'overload](expressions.md#overload-resolution). Il set di costruttori di istanza candidati è costituito da tutti i costruttori di istanza accessibili dichiarati in `T` applicabili rispetto a `A` ([membro della funzione applicabile](expressions.md#applicable-function-member)). Se il set di costruttori di istanza candidati è vuoto o se non è possibile identificare un singolo costruttore di istanze migliore, si verificherà un errore in fase di binding.
    * Il risultato di *object_creation_expression* è un valore di tipo `T`, ovvero il valore generato richiamando il costruttore di istanza determinato nel passaggio precedente.
*  In caso contrario, *object_creation_expression* non è valido e si verifica un errore in fase di binding.

Anche se il *object_creation_expression* è associato in modo dinamico, il tipo in fase di compilazione è ancora `T`.

L'elaborazione in fase di esecuzione di un *object_creation_expression* nel formato `new T(A)`, dove `T` è *class_type* o *struct_type* e `A` è un *argument_list*facoltativo, è costituito dai passaggi seguenti:

*   Se `T` è un *class_type*:
    * Viene allocata una nuova istanza della classe `T`. Se la memoria disponibile non è sufficiente per allocare la nuova istanza, viene generata un'`System.OutOfMemoryException` e non vengono eseguiti altri passaggi.
    * Tutti i campi della nuova istanza vengono inizializzati sui relativi valori predefiniti ([valori predefiniti](variables.md#default-values)).
    * Il costruttore di istanza viene richiamato in base alle regole della chiamata del membro della funzione ([controllo della fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution)). Un riferimento all'istanza appena allocata viene passato automaticamente al costruttore di istanza e l'istanza è accessibile dall'interno di tale costruttore come `this`.
*   Se `T` è un *struct_type*:
    * Un'istanza di tipo `T` viene creata allocando una variabile locale temporanea. Poiché è necessario un costruttore di istanza di un *struct_type* per assegnare definitivamente un valore a ogni campo dell'istanza da creare, non è necessaria alcuna inizializzazione della variabile temporanea.
    * Il costruttore di istanza viene richiamato in base alle regole della chiamata del membro della funzione ([controllo della fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution)). Un riferimento all'istanza appena allocata viene passato automaticamente al costruttore di istanza e l'istanza è accessibile dall'interno di tale costruttore come `this`.

#### <a name="object-initializers"></a>Inizializzatori di oggetto

Un ***inizializzatore di oggetto*** specifica i valori per zero o più campi, proprietà o elementi indicizzati di un oggetto.

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

Un inizializzatore di oggetto è costituito da una sequenza di inizializzatori di membri, racchiusi tra token `{` e `}` e separati da virgole. Ogni *member_initializer* designa una destinazione per l'inizializzazione. Un *identificatore* deve denominare una proprietà o un campo accessibile dell'oggetto da inizializzare, mentre un *argument_list* racchiuso tra parentesi quadre deve specificare argomenti per un indicizzatore accessibile nell'oggetto da inizializzare. Un inizializzatore di oggetto può includere più di un inizializzatore di membro per lo stesso campo o proprietà.

Ogni *initializer_target* è seguito da un segno di uguale e da un'espressione, da un inizializzatore di oggetto o da un inizializzatore di raccolta. Non è possibile che le espressioni all'interno dell'inizializzatore di oggetto facciano riferimento all'oggetto appena creato che sta inizializzando.

Un inizializzatore di membro che specifica un'espressione dopo il segno di uguale viene elaborato allo stesso modo di un'assegnazione ([assegnazione semplice](expressions.md#simple-assignment)) alla destinazione.

Inizializzatore di membro che specifica un inizializzatore di oggetto dopo che il segno di uguale è un ***inizializzatore di oggetto annidato***, ovvero un'inizializzazione di un oggetto incorporato. Anziché assegnare un nuovo valore al campo o alla proprietà, le assegnazioni nell'inizializzatore di oggetto annidato vengono considerate come assegnazioni ai membri del campo o della proprietà. Gli inizializzatori di oggetti annidati non possono essere applicati alle proprietà con un tipo di valore o a campi di sola lettura con un tipo di valore.

Un inizializzatore di membro che specifica un inizializzatore di raccolta dopo il segno di uguale è un'inizializzazione di una raccolta incorporata. Anziché assegnare una nuova raccolta al campo di destinazione, alla proprietà o all'indicizzatore, gli elementi forniti nell'inizializzatore vengono aggiunti alla raccolta a cui fa riferimento la destinazione. La destinazione deve essere di un tipo di raccolta che soddisfa i requisiti specificati negli [inizializzatori di insieme](expressions.md#collection-initializers).

Gli argomenti di un inizializzatore di indice verranno sempre valutati esattamente una volta. Pertanto, anche se gli argomenti non vengono mai usati, ad esempio a causa di un inizializzatore nidificato vuoto, verranno valutati per gli effetti collaterali.

La classe seguente rappresenta un punto con due Coordinate:
```csharp
public class Point
{
    int x, y;

    public int X { get { return x; } set { x = value; } }
    public int Y { get { return y; } set { y = value; } }
}
```

È possibile creare e inizializzare un'istanza di `Point` come indicato di seguito:
```csharp
Point a = new Point { X = 0, Y = 1 };
```
che ha lo stesso effetto di
```csharp
Point __a = new Point();
__a.X = 0;
__a.Y = 1; 
Point a = __a;
```
dove `__a` è una variabile temporanea altrimenti invisibile e inaccessibile. La classe seguente rappresenta un rettangolo creato da due punti:
```csharp
public class Rectangle
{
    Point p1, p2;

    public Point P1 { get { return p1; } set { p1 = value; } }
    public Point P2 { get { return p2; } set { p2 = value; } }
}
```

È possibile creare e inizializzare un'istanza di `Rectangle` come indicato di seguito:
```csharp
Rectangle r = new Rectangle {
    P1 = new Point { X = 0, Y = 1 },
    P2 = new Point { X = 2, Y = 3 }
};
```
che ha lo stesso effetto di
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
dove `__r`, `__p1` e `__p2` sono variabili temporanee che altrimenti sono invisibili e inaccessibili.

Se il costruttore di `Rectangle` alloca le due istanze `Point` incorporate
```csharp
public class Rectangle
{
    Point p1 = new Point();
    Point p2 = new Point();

    public Point P1 { get { return p1; } }
    public Point P2 { get { return p2; } }
}
```
il costrutto seguente può essere usato per inizializzare le istanze `Point` incorporate anziché assegnare nuove istanze:
```csharp
Rectangle r = new Rectangle {
    P1 = { X = 0, Y = 1 },
    P2 = { X = 2, Y = 3 }
};
```
che ha lo stesso effetto di
```csharp
Rectangle __r = new Rectangle();
__r.P1.X = 0;
__r.P1.Y = 1;
__r.P2.X = 2;
__r.P2.Y = 3;
Rectangle r = __r;
```

Data la definizione appropriata di C, l'esempio seguente:
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
equivale a questa serie di assegnazioni:
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
laddove `__c` e così via, vengono generate variabili che sono invisibili e inaccessibili al codice sorgente. Si noti che gli argomenti per `[0,0]` vengono valutati una sola volta e gli argomenti per `[1,2]` vengono valutati una sola volta, anche se non vengono mai utilizzati.

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

Un inizializzatore di raccolta è costituito da una sequenza di inizializzatori di elementi, racchiusi tra token `{` e `}` e separati da virgole. Ogni inizializzatore di elemento specifica un elemento da aggiungere all'oggetto della raccolta che viene inizializzato ed è costituito da un elenco di espressioni racchiuse tra i token `{` e `}` e separati da virgole.  Un inizializzatore di elemento a espressione singola può essere scritto senza parentesi graffe, ma non può essere quindi un'espressione di assegnazione, per evitare ambiguità con gli inizializzatori di membri. L'ambiente di produzione *non_assignment_expression* è definito in [Expression](expressions.md#expression).

Di seguito è riportato un esempio di espressione di creazione di un oggetto che include un inizializzatore di raccolta:
```csharp
List<int> digits = new List<int> { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
```

L'oggetto raccolta a cui è applicato un inizializzatore di raccolta deve essere di un tipo che implementa `System.Collections.IEnumerable` o si verifica un errore in fase di compilazione. Per ogni elemento specificato nell'ordine, l'inizializzatore di raccolta richiama un metodo `Add` sull'oggetto di destinazione con l'elenco di espressioni dell'inizializzatore di elemento come elenco di argomenti, applicando la normale ricerca dei membri e la risoluzione dell'overload per ogni chiamata. Pertanto, l'oggetto raccolta deve avere un'istanza o un metodo di estensione applicabile con il nome `Add` per ogni inizializzatore di elemento.

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

È possibile creare e inizializzare un `List<Contact>` come indicato di seguito:
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
che ha lo stesso effetto di
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
dove `__clist`, `__c1` e `__c2` sono variabili temporanee che altrimenti sono invisibili e inaccessibili.

#### <a name="array-creation-expressions"></a>Espressioni di creazione di matrici

Un *array_creation_expression* viene usato per creare una nuova istanza di un *array_type*.

```antlr
array_creation_expression
    : 'new' non_array_type '[' expression_list ']' rank_specifier* array_initializer?
    | 'new' array_type array_initializer
    | 'new' rank_specifier array_initializer
    ;
```

Un'espressione di creazione di matrici del primo form alloca un'istanza di matrice del tipo risultante dall'eliminazione di ognuna delle singole espressioni dall'elenco di espressioni. Ad esempio, l'espressione di creazione della matrice `new int[10,20]` produce un'istanza di matrice di tipo `int[,]` e l'espressione di creazione della matrice `new int[10][,]` produce una matrice di tipo `int[][,]`. Ogni espressione nell'elenco di espressioni deve essere di tipo `int`, `uint`, `long` o `ulong` o convertibile in modo implicito in uno o più di questi tipi. Il valore di ogni espressione determina la lunghezza della dimensione corrispondente nell'istanza della matrice appena allocata. Poiché la lunghezza di una dimensione di matrice deve essere non negativa, è un errore in fase di compilazione avere un *constant_expression* con un valore negativo nell'elenco di espressioni.

Ad eccezione di un contesto non sicuro ([contesti non sicuri](unsafe-code.md#unsafe-contexts)), il layout delle matrici non è specificato.

Se un'espressione di creazione di matrici del primo form include un inizializzatore di matrice, ogni espressione nell'elenco di espressioni deve essere una costante e le lunghezze e le lunghezze delle dimensioni specificate dall'elenco di espressioni devono corrispondere a quelle dell'inizializzatore di matrice.

In un'espressione di creazione di matrici della seconda o della terza forma, il rango del tipo di matrice o dell'identificatore di rango specificato deve corrispondere a quello dell'inizializzatore di matrice. Le singole lunghezze delle dimensioni vengono dedotte dal numero di elementi in ciascuno dei livelli di annidamento corrispondenti dell'inizializzatore di matrice. Quindi, l'espressione
```csharp
new int[,] {{0, 1}, {2, 3}, {4, 5}}
```
corrisponde esattamente a
```csharp
new int[3, 2] {{0, 1}, {2, 3}, {4, 5}}
```

Un'espressione di creazione di matrici del terzo form viene definita espressione di ***creazione di una matrice tipizzata in modo implicito***. È simile al secondo formato, ad eccezione del fatto che il tipo di elemento della matrice non viene specificato in modo esplicito, ma è determinato come il tipo comune migliore ([ricerca del tipo comune migliore di un set di espressioni](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)) del set di espressioni nell'inizializzatore di matrice. Per una matrice multidimensionale, ad esempio una in cui *rank_specifier* contiene almeno una virgola, questo set comprende tutte le *espressioni*presenti in *array_initializer*annidate.

Gli inizializzatori di matrice sono descritti ulteriormente negli [inizializzatori di matrice](arrays.md#array-initializers).

Il risultato della valutazione di un'espressione di creazione di matrici è classificato come valore, ovvero un riferimento all'istanza di matrice appena allocata. L'elaborazione in fase di esecuzione di un'espressione di creazione di matrici prevede i passaggi seguenti:

*  Le espressioni di lunghezza della dimensione di *expression_list* vengono valutate in ordine, da sinistra a destra. Dopo la valutazione di ogni espressione, viene eseguita una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) a uno dei seguenti tipi: `int`, `uint`, `long`, `ulong`. Viene scelto il primo tipo in questo elenco per cui è stata scelta una conversione implicita. Se la valutazione di un'espressione o della successiva conversione implicita genera un'eccezione, non vengono valutate altre espressioni e non vengono eseguiti altri passaggi.
*  I valori calcolati per le lunghezze delle dimensioni vengono convalidati come indicato di seguito. Se uno o più valori sono minori di zero, viene generata un'`System.OverflowException` e non vengono eseguiti altri passaggi.
*  Viene allocata un'istanza di matrice con le lunghezze delle dimensioni specificate. Se la memoria disponibile non è sufficiente per allocare la nuova istanza, viene generata un'`System.OutOfMemoryException` e non vengono eseguiti altri passaggi.
*  Tutti gli elementi della nuova istanza della matrice vengono inizializzati sui relativi valori predefiniti ([valori predefiniti](variables.md#default-values)).
*  Se l'espressione di creazione della matrice contiene un inizializzatore di matrice, ogni espressione nell'inizializzatore di matrice viene valutata e assegnata all'elemento della matrice corrispondente. Le valutazioni e le assegnazioni vengono eseguite nell'ordine in cui le espressioni vengono scritte nell'inizializzatore di matrice. in altre parole, gli elementi vengono inizializzati in ordine di indice crescente, con la dimensione più a destra che aumenta per prima. Se la valutazione di un'espressione specificata o l'assegnazione successiva all'elemento della matrice corrispondente genera un'eccezione, non vengono inizializzati altri elementi (e gli elementi rimanenti avranno quindi i valori predefiniti).

Un'espressione di creazione di matrici consente di creare un'istanza di una matrice con elementi di un tipo matrice, ma gli elementi di tale matrice devono essere inizializzati manualmente. Ad esempio, l'istruzione
```csharp
int[][] a = new int[100][];
```
Crea una matrice unidimensionale con 100 elementi di tipo `int[]`. Il valore iniziale di ogni elemento è `null`. Per la stessa espressione di creazione della matrice non è possibile creare anche un'istanza delle sottomatrici e l'istruzione
```csharp
int[][] a = new int[100][5];        // Error
```
genera un errore in fase di compilazione. La creazione di istanze delle sottomatrici deve invece essere eseguita manualmente, come in
```csharp
int[][] a = new int[100][];
for (int i = 0; i < 100; i++) a[i] = new int[5];
```

Quando una matrice di matrici ha una forma "rettangolare", ovvero quando le sottomatrici hanno la stessa lunghezza, è più efficiente usare una matrice multidimensionale. Nell'esempio precedente, la creazione di un'istanza della matrice di matrici crea oggetti 101, ovvero una matrice esterna e 100 sottomatrici. Al contrario,
```csharp
int[,] = new int[100, 5];
```
consente di creare un solo oggetto, una matrice bidimensionale e di eseguire l'allocazione in un'unica istruzione.

Di seguito sono riportati alcuni esempi di espressioni di creazione di matrici tipizzate in modo implicito:
```csharp
var a = new[] { 1, 10, 100, 1000 };                       // int[]

var b = new[] { 1, 1.5, 2, 2.5 };                         // double[]

var c = new[,] { { "hello", null }, { "world", "!" } };   // string[,]

var d = new[] { 1, "one", 2, "two" };                     // Error
```

L'ultima espressione causa un errore in fase di compilazione perché nessuna delle due `int` né `string` è implicitamente convertibile nell'altra, quindi non esiste alcun tipo comune migliore. In questo caso, è necessario usare un'espressione di creazione di matrici tipizzata in modo esplicito, ad esempio specificando il tipo da `object[]`. In alternativa, è possibile eseguire il cast di uno degli elementi in un tipo di base comune, che diventerebbe quindi il tipo di elemento derivato.

È possibile combinare espressioni di creazione di matrici tipizzate in modo implicito con inizializzatori di oggetti anonimi ([espressioni di creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions)) per creare strutture di dati tipizzate anonimamente. Esempio:
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

#### <a name="delegate-creation-expressions"></a>Espressioni di creazione di delegati

Un *delegate_creation_expression* viene usato per creare una nuova istanza di un *delegate_type*.

```antlr
delegate_creation_expression
    : 'new' delegate_type '(' expression ')'
    ;
```

L'argomento di un'espressione di creazione del delegato deve essere un gruppo di metodi, una funzione anonima o un valore del tipo di tempo di compilazione `dynamic` o *delegate_type*. Se l'argomento è un gruppo di metodi, identifica il metodo e, per un metodo di istanza, l'oggetto per il quale creare un delegato. Se l'argomento è una funzione anonima, definisce direttamente i parametri e il corpo del metodo della destinazione del delegato. Se l'argomento è un valore, identifica un'istanza di delegato di cui creare una copia.

Se l' *espressione* ha il tipo in fase di compilazione `dynamic`, *delegate_creation_expression* è associato in modo dinamico ([associazione dinamica](expressions.md#dynamic-binding)) e le regole seguenti vengono applicate in fase di esecuzione usando il tipo di runtime dell' *espressione*. In caso contrario, le regole vengono applicate in fase di compilazione.

L'elaborazione in fase di binding di un *delegate_creation_expression* nel formato `new D(E)`, dove `D` è un *delegate_type* e `E` è un' *espressione*, è costituita dai passaggi seguenti:

*  Se `E` è un gruppo di metodi, l'espressione di creazione del delegato viene elaborata in modo analogo alla conversione di un gruppo di metodi ([conversioni di gruppi di metodi](conversions.md#method-group-conversions)) da `E` a `D`.
*  Se `E` è una funzione anonima, l'espressione di creazione del delegato viene elaborata allo stesso modo di una conversione di funzione anonima ([conversioni di funzioni anonime](conversions.md#anonymous-function-conversions)) da `E` a `D`.
*  Se `E` è un valore, `E` deve essere compatibile ([dichiarazioni delegate](delegates.md#delegate-declarations)) con `D` e il risultato è un riferimento a un delegato appena creato di tipo `D` che fa riferimento allo stesso elenco chiamate di `E`. Se `E` non è compatibile con `D`, si verifica un errore in fase di compilazione.

L'elaborazione in fase di esecuzione di un *delegate_creation_expression* nel formato `new D(E)`, dove `D` è un *delegate_type* e `E` è un' *espressione*, è costituita dai passaggi seguenti:

*   Se `E` è un gruppo di metodi, l'espressione di creazione del delegato viene valutata come conversione di un gruppo di metodi ([conversioni di gruppi di metodi](conversions.md#method-group-conversions)) da `E` a `D`.
*   Se `E` è una funzione anonima, la creazione del delegato viene valutata come conversione di funzione anonima da `E` a `D` ([conversioni di funzioni anonime](conversions.md#anonymous-function-conversions)).
*   Se `E` è un valore di *delegate_type*:
    * `E` viene valutato. Se questa valutazione causa un'eccezione, non vengono eseguiti altri passaggi.
    * Se il valore di `E` è `null`, viene generata una `System.NullReferenceException` e non vengono eseguiti altri passaggi.
    * Viene allocata una nuova istanza del tipo delegato `D`. Se la memoria disponibile non è sufficiente per allocare la nuova istanza, viene generata un'`System.OutOfMemoryException` e non vengono eseguiti altri passaggi.
    * La nuova istanza del delegato viene inizializzata con lo stesso elenco chiamate dell'istanza del delegato fornita da `E`.

L'elenco chiamate di un delegato viene determinato quando viene creata un'istanza del delegato e quindi rimane costante per l'intera durata del delegato. In altre parole, non è possibile modificare le entità chiamabili di destinazione di un delegato dopo che è stato creato. Quando due delegati vengono combinati o uno viene rimosso da un altro ([dichiarazioni di delegati](delegates.md#delegate-declarations)), viene restituito un nuovo delegato. nessun delegato esistente ne è stato modificato il contenuto.

Non è possibile creare un delegato che fa riferimento a una proprietà, un indicizzatore, un operatore definito dall'utente, un costruttore di istanza, un distruttore o un costruttore statico.

Come descritto in precedenza, quando viene creato un delegato da un gruppo di metodi, l'elenco di parametri formali e il tipo restituito del delegato determinano quale dei metodi di overload selezionare. Nell'esempio
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
il campo `A.f` viene inizializzato con un delegato che fa riferimento al secondo metodo `Square` perché tale metodo corrisponde esattamente all'elenco di parametri formali e al tipo restituito di `DoubleFunc`. Se il secondo metodo `Square` non è presente, si è verificato un errore in fase di compilazione.

#### <a name="anonymous-object-creation-expressions"></a>Espressioni di creazione di oggetti anonimi

Un *anonymous_object_creation_expression* viene usato per creare un oggetto di tipo anonimo.

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

Un inizializzatore di oggetto anonimo dichiara un tipo anonimo e restituisce un'istanza di quel tipo. Un tipo anonimo è un tipo di classe senza nome che eredita direttamente da `object`. I membri di un tipo anonimo sono una sequenza di proprietà di sola lettura dedotte dall'inizializzatore di oggetto anonimo utilizzato per creare un'istanza del tipo. In particolare, un inizializzatore di oggetto anonimo nel formato
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
dove ogni `Tx` è il tipo dell'espressione corrispondente `ex`. L'espressione utilizzata in un *member_declarator* deve avere un tipo. Quindi, si tratta di un errore in fase di compilazione per un'espressione in un *member_declarator* che può essere null o una funzione anonima. Si tratta inoltre di un errore in fase di compilazione per l'espressione di un tipo unsafe.

I nomi di un tipo anonimo e del parametro al relativo metodo `Equals` vengono generati automaticamente dal compilatore e non è possibile farvi riferimento nel testo del programma.

All'interno dello stesso programma, due inizializzatori di oggetti anonimi che specificano una sequenza di proprietà con gli stessi nomi e tipi in fase di compilazione nello stesso ordine produrranno istanze dello stesso tipo anonimo.

Nell'esempio
```csharp
var p1 = new { Name = "Lawnmower", Price = 495.00 };
var p2 = new { Name = "Shovel", Price = 26.95 };
p1 = p2;
```
l'assegnazione sull'ultima riga è consentita perché `p1` e `p2` sono dello stesso tipo anonimo.

I metodi `Equals` e `GetHashcode` sui tipi anonimi eseguono l'override dei metodi ereditati da `object` e sono definiti in termini di `Equals` e `GetHashcode` delle proprietà, in modo che due istanze dello stesso tipo anonimo siano uguali solo se tutte le relative proprietà sono uguale.

Un dichiaratore di membro può essere abbreviato in un nome semplice ([inferenza del tipo](expressions.md#type-inference)), un accesso ai membri ([controllo della fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), un accesso di base ([accesso di base](expressions.md#base-access)) o un accesso a membri condizionali null ([ Espressioni condizionali null come inizializzatori di proiezione](expressions.md#null-conditional-expressions-as-projection-initializers)). Si tratta di un ***inizializzatore di proiezione*** ed è una sintassi abbreviata per una dichiarazione di e l'assegnazione a una proprietà con lo stesso nome. In particolare, dichiaratori di membri dei form
```csharp
identifier
expr.identifier
```
sono esattamente equivalenti rispettivamente a quanto segue:
```csharp
identifier = identifier
identifier = expr.identifier
```

Quindi, in un inizializzatore di proiezione l' *identificatore* seleziona sia il valore che il campo o la proprietà a cui è assegnato il valore. In modo intuitivo, un inizializzatore di proiezione proietta non solo un valore, ma anche il nome del valore.

### <a name="the-typeof-operator"></a>Operatore typeof

L'operatore `typeof` viene usato per ottenere l'oggetto `System.Type` per un tipo.

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

La prima forma di *typeof_expression* è costituita da una parola chiave `typeof` seguita da un *tipo*racchiuso tra parentesi. Il risultato di un'espressione di questo form è l'oggetto `System.Type` per il tipo indicato. Esiste un solo oggetto `System.Type` per qualsiasi tipo specificato. Ciò significa che per un tipo @ no__t-0, `typeof(T) == typeof(T)` è sempre true. Il *tipo* non può essere `dynamic`.

La seconda forma di *typeof_expression* è costituita da una parola chiave `typeof` seguita da un *unbound_type_name*tra parentesi. Un *unbound_type_name* è molto simile a un *type_name* ([nomi di spazio dei nomi e tipi](basic-concepts.md#namespace-and-type-names)), ad eccezione del fatto che un *unbound_type_name* contiene *generic_dimension_specifier*s dove un *type_name* contiene *Type_ argument_list*s. Quando l'operando di un *typeof_expression* è una sequenza di token che soddisfa le grammatiche di *unbound_type_name* e *type_name*, ovvero quando non contiene né un *generic_dimension_specifier* né un *type_argument _List*, la sequenza di token viene considerata come *type_name*. Il significato di un *unbound_type_name* viene determinato nel modo seguente:

*  Convertire la sequenza di token in un *type_name* sostituendo ogni *generic_dimension_specifier* con un *type_argument_list* con lo stesso numero di virgole e la parola chiave `object` come ogni *type_argument*.
*  Valutare il *type_name*risultante, ignorando tutti i vincoli del parametro di tipo.
*  *Unbound_type_name* viene risolto nel tipo generico non associato associato al tipo costruito risultante ([tipi associati e non associati](types.md#bound-and-unbound-types)).

Il risultato di *typeof_expression* è l'oggetto `System.Type` per il tipo generico non associato risultante.

La terza forma di *typeof_expression* è costituita da una parola chiave `typeof` seguita da una parola chiave `void` racchiusa tra parentesi. Il risultato di un'espressione di questo form è l'oggetto `System.Type` che rappresenta l'assenza di un tipo. L'oggetto tipo restituito da `typeof(void)` è diverso dall'oggetto tipo restituito per qualsiasi tipo. Questo oggetto di tipo speciale è utile nelle librerie di classi che consentono la reflection sui metodi nel linguaggio, in cui tali metodi vogliono avere un modo per rappresentare il tipo restituito di qualsiasi metodo, inclusi i metodi void, con un'istanza di `System.Type`.

L'operatore `typeof` può essere usato in un parametro di tipo. Il risultato è l'oggetto `System.Type` per il tipo in fase di esecuzione associato al parametro di tipo. L'operatore `typeof` può essere usato anche in un tipo costruito o in un tipo generico non associato ([tipi associati e non associati](types.md#bound-and-unbound-types)). L'oggetto `System.Type` per un tipo generico non associato è diverso dall'oggetto `System.Type` del tipo di istanza. Il tipo di istanza è sempre un tipo costruito chiuso in fase di esecuzione, in modo che il relativo oggetto `System.Type` dipenda dagli argomenti di tipo runtime in uso, mentre il tipo generico non associato non dispone di argomenti di tipo.

Esempio
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
```console
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

Si noti inoltre che il risultato di `typeof(X<>)` non dipende dall'argomento di tipo, ma il risultato di `typeof(X<T>)`.

### <a name="the-checked-and-unchecked-operators"></a>Operatori Checked e Unchecked

Gli operatori `checked` e `unchecked` vengono utilizzati per controllare il ***contesto*** di controllo dell'overflow per le conversioni e le operazioni aritmetiche di tipo integrale.

```antlr
checked_expression
    : 'checked' '(' expression ')'
    ;

unchecked_expression
    : 'unchecked' '(' expression ')'
    ;
```

L'operatore `checked` valuta l'espressione contenuta in un contesto controllato e l'operatore `unchecked` valuta l'espressione contenuta in un contesto non controllato. Un *checked_expression* o *unchecked_expression* corrisponde esattamente a un *parenthesized_expression* ([espressioni racchiuse tra parentesi](expressions.md#parenthesized-expressions)), ad eccezione del fatto che l'espressione contenuta viene valutata nel contesto di controllo dell'overflow specificato .

Il contesto di controllo dell'overflow può essere controllato anche tramite le istruzioni `checked` e `unchecked` ([le istruzioni checked e unchecked](statements.md#the-checked-and-unchecked-statements)).

Le operazioni seguenti sono interessate dal contesto di controllo dell'overflow stabilito dagli operatori e dalle istruzioni `checked` e `unchecked`:

*  Gli operatori unari predefiniti `++` e `--` ([operatori di incremento e decremento suffisso](expressions.md#postfix-increment-and-decrement-operators) e [operatori di incremento e decremento del prefisso](expressions.md#prefix-increment-and-decrement-operators)), quando l'operando è di tipo integrale.
*  Operatore unario `-` predefinito ([operatore unario meno](expressions.md#unary-minus-operator)), quando l'operando è di tipo integrale.
*  Gli operatori binari predefiniti `+`, `-`, `*` e `/` ([operatori aritmetici](expressions.md#arithmetic-operators)), quando entrambi gli operandi sono di tipo integrale.
*  Conversioni numeriche esplicite ([conversioni numeriche esplicite](conversions.md#explicit-numeric-conversions)) da un tipo integrale a un altro tipo integrale o da `float` o `double` a un tipo integrale.

Quando una delle operazioni precedenti produce un risultato troppo grande per essere rappresentato nel tipo di destinazione, il contesto in cui viene eseguita l'operazione Controlla il comportamento risultante:

*  In un contesto `checked`, se l'operazione è un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)), si verifica un errore in fase di compilazione. In caso contrario, quando l'operazione viene eseguita in fase di esecuzione, viene generata un'`System.OverflowException`.
*  In un contesto `unchecked`, il risultato viene troncato eliminando eventuali bit di ordine superiore che non rientrano nel tipo di destinazione.

Per le espressioni non costanti (espressioni che vengono valutate in fase di esecuzione) che non sono racchiuse tra operatori o istruzioni `checked` o `unchecked`, il contesto di controllo dell'overflow predefinito è `unchecked` a meno che non siano presenti fattori esterni (ad esempio, opzioni del compilatore e configurazione dell'ambiente di esecuzione) chiamata per la valutazione `checked`.

Per le espressioni costanti (espressioni che possono essere valutate completamente in fase di compilazione), il contesto di controllo dell'overflow predefinito è sempre `checked`. A meno che un'espressione costante non venga inserita in modo esplicito in un contesto `unchecked`, i flussi che si verificano durante la valutazione in fase di compilazione dell'espressione provocano sempre errori in fase di compilazione.

Il corpo di una funzione anonima non è influenzato dai contesti `checked` o `unchecked` in cui si verifica la funzione anonima.

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
non vengono segnalati errori in fase di compilazione perché nessuna delle espressioni può essere valutata in fase di compilazione. In fase di esecuzione, il metodo `F` genera un `System.OverflowException` e il metodo `G` restituisce-727379968 (i 32 bit meno significativi del risultato non compreso nell'intervallo). Il comportamento del metodo `H` dipende dal contesto di controllo dell'overflow predefinito per la compilazione, ma è uguale a `F` o uguale a `G`.

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
gli overflow che si verificano durante la valutazione delle espressioni costanti in `F` e `H` causano la segnalazione degli errori in fase di compilazione perché le espressioni vengono valutate in un contesto `checked`. Si verifica un overflow anche durante la valutazione dell'espressione costante in `G`, ma poiché la valutazione viene eseguita in un contesto `unchecked`, l'overflow non viene segnalato.

Gli operatori `checked` e `unchecked` influiscono solo sul contesto di controllo dell'overflow per le operazioni che sono contenute testualmente nei token "`(`" e "`)`". Gli operatori non hanno alcun effetto sui membri di funzione richiamati come risultato della valutazione dell'espressione contenuta. Nell'esempio
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
l'uso di `checked` in `F` non influisce sulla valutazione di `x * y` in `Multiply`, pertanto `x * y` viene valutato nel contesto di controllo dell'overflow predefinito.

L'operatore `unchecked` è utile per la scrittura di costanti dei tipi integrali con segno in notazione esadecimale. Esempio:
```csharp
class Test
{
    public const int AllBits = unchecked((int)0xFFFFFFFF);

    public const int HighBit = unchecked((int)0x80000000);
}
```

Entrambe le costanti esadecimali precedenti sono di tipo `uint`. Poiché le costanti si trovano al di fuori dell'intervallo `int`, senza l'operatore `unchecked`, il cast a `int` produrrebbe errori in fase di compilazione.

Gli operatori e le istruzioni `checked` e `unchecked` consentono ai programmatori di controllare alcuni aspetti di alcuni calcoli numerici. Tuttavia, il comportamento di alcuni operatori numerici dipende dai tipi di dati degli operandi. Se ad esempio si moltiplicano due decimali, viene sempre generata un'eccezione in caso di overflow anche all'interno di un costrutto `unchecked` in modo esplicito. Analogamente, la moltiplicazione di due float non produce mai un'eccezione in un overflow anche all'interno di un costrutto `checked` in modo esplicito. Inoltre, gli altri operatori non sono mai interessati dalla modalità di controllo, sia che si tratti di un valore predefinito o esplicito.

### <a name="default-value-expressions"></a>Espressioni con valore predefinito

Un'espressione con valore predefinito viene utilizzata per ottenere il valore predefinito ([valori predefiniti](variables.md#default-values)) di un tipo. Un'espressione con valore predefinito viene in genere utilizzata per i parametri di tipo, poiché potrebbe non essere noto se il parametro di tipo è un tipo di valore o un tipo di riferimento. Non esiste alcuna conversione dal valore letterale `null` a un parametro di tipo, a meno che il parametro di tipo non sia un tipo di riferimento.

```antlr
default_value_expression
    : 'default' '(' type ')'
    ;
```

Se il *tipo* in un *default_value_expression* restituisce in fase di esecuzione a un tipo di riferimento, il risultato è `null` convertito in quel tipo. Se il *tipo* in un *default_value_expression* restituisce in fase di esecuzione a un tipo valore, il risultato è il valore predefinito di *value_type*([costruttori predefiniti](types.md#default-constructors)).

Un *default_value_expression* è un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)) se il tipo è un tipo di riferimento o un parametro di tipo che è noto come tipo di riferimento ([vincoli del parametro di tipo](classes.md#type-parameter-constraints)). Inoltre, un *default_value_expression* è un'espressione costante se il tipo è uno dei seguenti tipi di valore: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, 0, 1, 2, 3 o qualsiasi tipo di enumerazione.


### <a name="nameof-expressions"></a>Espressioni NameOf

Un *nameof_expression* viene usato per ottenere il nome di un'entità di programma come stringa costante.

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

In termini grammaticali, l'operando *named_entity* è sempre un'espressione. Poiché `nameof` non è una parola chiave riservata, un'espressione NameOf è sempre sintatticamente ambigua con una chiamata del nome semplice `nameof`. Per motivi di compatibilità, se la ricerca del nome ([nomi semplici](expressions.md#simple-names)) del nome `nameof` ha esito positivo, l'espressione viene considerata come *invocation_expression* , indipendentemente dal fatto che la chiamata sia valida. In caso contrario, si tratta di un *nameof_expression*.

Il significato di *named_entity* di un *nameof_expression* è il significato di esso come espressione; ovvero come *simple_name*, *base_access* o *member_access*. Tuttavia, quando la ricerca descritta in [nomi semplici](expressions.md#simple-names) e [accesso ai membri](expressions.md#member-access) genera un errore perché un membro di istanza è stato trovato in un contesto statico, un *nameof_expression* genera un errore di questo tipo.

Si tratta di un errore in fase di compilazione per un *named_entity* che designa un gruppo di metodi in modo che disponga di un *type_argument_list*. Si tratta di un errore in fase di compilazione per un *named_entity_target* di tipo `dynamic`.

Un *nameof_expression* è un'espressione costante di tipo `string` e non ha alcun effetto in fase di esecuzione. In particolare, la relativa *named_entity* non viene valutata e viene ignorata ai fini dell'analisi dell'assegnazione definita ([regole generali per le espressioni semplici](variables.md#general-rules-for-simple-expressions)). Il valore è l'ultimo identificatore di *named_entity* prima dell' *type_argument_list*finale facoltativa, trasformato nel modo seguente:

* Il prefisso "`@`", se usato, viene rimosso.
* Ogni *unicode_escape_sequence* viene trasformato nel carattere Unicode corrispondente.
* Eventuali *formatting_characters* vengono rimossi.

Si tratta delle stesse trasformazioni applicate negli [identificatori](lexical-structure.md#identifiers) quando si verifica l'uguaglianza tra gli identificatori.

TODO: esempi

### <a name="anonymous-method-expressions"></a>Espressioni di metodo anonime

Un *anonymous_method_expression* è uno dei due modi per definire una funzione anonima. Questi sono descritti ulteriormente in [espressioni di funzioni anonime](expressions.md#anonymous-function-expressions).

## <a name="unary-operators"></a>Operatori unari

Gli operatori `?`, `+`, `-`, `!`, `~`, `++`, `--`, cast e `await` sono denominati operatori unari.

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

Se l'operando di un *unary_expression* ha il tipo in fase di compilazione `dynamic`, è associato in modo dinamico ([associazione dinamica](expressions.md#dynamic-binding)). In questo caso, il tipo in fase di compilazione di *unary_expression* è `dynamic` e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di runtime dell'operando.

### <a name="null-conditional-operator"></a>Operatore condizionale null

L'operatore condizionale null applica un elenco di operazioni al relativo operando solo se l'operando è diverso da null. In caso contrario, il risultato dell'applicazione dell'operatore è `null`.

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

L'elenco di operazioni può includere operazioni di accesso ai membri e di accesso agli elementi, che possono essere a loro volta condizionali null, nonché la chiamata.

Ad esempio, l'espressione `a.b?[0]?.c()` è un *null_conditional_expression* con *primary_expression* `a.b` e *null_conditional_operations* `?[0]` (accesso a elementi condizionali null), `?.c` (membro condizionale null Access) e `()` (chiamata).

Per *null_conditional_expression* `E` con *primary_expression* `P`, lasciare che `E0` sia l'espressione ottenuta rimuovendo in modo testuale il `?` principale da ognuno dei *null_conditional_operations* di `E` che ne sono disponibili. Concettualmente, `E0` è l'espressione che verrà valutata se nessuno dei controlli null rappresentati dall'`?`S trova un `null`.

Inoltre, lasciare che `E1` sia l'espressione ottenuta in modo testuale rimuovendo il `?` iniziale da solo il primo di *null_conditional_operations* in `E`. Questo può causare un' *espressione primaria* (se è presente solo un `?`) o a un altro *null_conditional_expression*.

Se, ad esempio, `E` è l'espressione `a.b?[0]?.c()`, `E0` è l'espressione `a.b[0].c()` e `E1` è l'espressione `a.b[0]?.c()`.

Se `E0` è classificato come Nothing, `E` viene classificato come Nothing. In caso contrario E è classificato come valore.

`E0` e `E1` vengono utilizzati per determinare il significato di `E`:

*  Se `E` si verifica come *statement_expression* , il significato di `E` è uguale all'istruzione

   ```csharp
   if ((object)P != null) E1;
   ```

   ad eccezione del fatto che P viene valutato una sola volta.

*  In caso contrario, se `E0` è classificato come Nothing, si verifica un errore in fase di compilazione.

*  In caso contrario, lasciare che `T0` sia il tipo di `E0`.

   *  Se `T0` è un parametro di tipo che non è noto come tipo di riferimento o tipo di valore non nullable, si verifica un errore in fase di compilazione.

   *  Se `T0` è un tipo di valore non nullable, il tipo di `E` è `T0?` e il significato di `E` è uguale a

      ```csharp
      ((object)P == null) ? (T0?)null : E1
      ```

      ad eccezione del fatto che `P` viene valutato una sola volta.

   *  In caso contrario, il tipo di E è T0 e il significato di E è uguale a

      ```csharp
      ((object)P == null) ? null : E1
      ```

      ad eccezione del fatto che `P` viene valutato una sola volta.

Se `E1` è a sua volta un *null_conditional_expression*, queste regole vengono riapplicate, nidificando i test per `null` fino a quando non sono presenti ulteriori `?` e l'espressione è stata ridotta fino all'espressione primaria `E0`.

Se, ad esempio, l'espressione `a.b?[0]?.c()` si verifica come istruzione-Expression, come nell'istruzione:
```csharp
a.b?[0]?.c();
```
il suo significato è equivalente a:
```csharp
if (a.b != null) a.b[0]?.c();
```
che è di nuovo equivalente a:
```csharp
if (a.b != null) if (a.b[0] != null) a.b[0].c();
```
Ad eccezione del fatto che `a.b` e `a.b[0]` vengono valutate una sola volta.

Se si verifica in un contesto in cui viene utilizzato il relativo valore, come in:
```csharp
var x = a.b?[0]?.c();
```
e supponendo che il tipo della chiamata finale non sia un tipo di valore non nullable, il suo significato è equivalente a:
```csharp
var x = (a.b == null) ? null : (a.b[0] == null) ? null : a.b[0].c();
```
ad eccezione del fatto che `a.b` e `a.b[0]` vengono valutate una sola volta.

#### <a name="null-conditional-expressions-as-projection-initializers"></a>Espressioni condizionali null come inizializzatori di proiezione

Un'espressione condizionale null è consentita solo come *member_declarator* in un *anonymous_object_creation_expression* (espressioni di[creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions)) se termina con un accesso ai membri (facoltativo-Conditional null). Grammaticalmente, questo requisito può essere espresso come segue:

```antlr
null_conditional_member_access
    : primary_expression null_conditional_operations? '?' '.' identifier type_argument_list?
    | primary_expression null_conditional_operations '.' identifier type_argument_list?
    ;
```

Si tratta di un caso speciale della grammatica per *null_conditional_expression* in precedenza. La produzione per *member_declarator* nelle [espressioni di creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions) include quindi solo *null_conditional_member_access*.

#### <a name="null-conditional-expressions-as-statement-expressions"></a>Espressioni condizionali null come espressioni di istruzione

Un'espressione condizionale null è consentita solo come *statement_expression* ([istruzioni Expression](statements.md#expression-statements)) se termina con una chiamata. Grammaticalmente, questo requisito può essere espresso come segue:

```antlr
null_conditional_invocation_expression
    : primary_expression null_conditional_operations '(' argument_list? ')'
    ;
```

Si tratta di un caso speciale della grammatica per *null_conditional_expression* in precedenza. La produzione per *statement_expression* nelle [istruzioni Expression](statements.md#expression-statements) include quindi solo *null_conditional_invocation_expression*.


### <a name="unary-plus-operator"></a>Operatore più unario

Per un'operazione nel formato `+x`, viene applicata la risoluzione dell'overload dell'operatore unario ([risoluzione dell'overload dell'operatore unario](expressions.md#unary-operator-overload-resolution)) per selezionare l'implementazione di un operatore specifico. L'operando viene convertito nel tipo di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore. Gli operatori unario più predefiniti sono:

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

Per un'operazione nel formato `-x`, viene applicata la risoluzione dell'overload dell'operatore unario ([risoluzione dell'overload dell'operatore unario](expressions.md#unary-operator-overload-resolution)) per selezionare l'implementazione di un operatore specifico. L'operando viene convertito nel tipo di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore. Gli operatori di negazione predefiniti sono:

*  Negazione integer:

   ```csharp
   int operator -(int x);
   long operator -(long x);
   ```

   Il risultato viene calcolato sottraendo `x` da zero. Se il valore di `x` è il più piccolo valore rappresentabile del tipo di operando (-2 ^ 31 per `int` o-2 ^ 63 per `long`), la negazione matematica di `x` non può essere rappresentata all'interno del tipo di operando. Se questa situazione si verifica all'interno di un contesto `checked`, viene generata un'`System.OverflowException`; Se si verifica all'interno di un contesto `unchecked`, il risultato è il valore dell'operando e l'overflow non viene segnalato.

   Se l'operando dell'operatore di negazione è di tipo `uint`, viene convertito nel tipo `long` e il tipo del risultato è `long`. Un'eccezione è la regola che consente di scrivere il valore `int`-2147483648 (-2 ^ 31) come valore letterale integer decimale (valori[letterali integer](lexical-structure.md#integer-literals)).

   Se l'operando dell'operatore di negazione è di tipo `ulong`, si verifica un errore in fase di compilazione. Un'eccezione è la regola che consente la scrittura del valore `long` (-2 ^ 63) come valore letterale integer decimale (valori[letterali integer](lexical-structure.md#integer-literals)).

*  Negazione a virgola mobile:

   ```csharp
   float operator -(float x);
   double operator -(double x);
   ```

   Il risultato è il valore di `x` con il segno invertito. Se `x` è NaN, anche il risultato è NaN.

*  Negazione decimale:

   ```csharp
   decimal operator -(decimal x);
   ```

   Il risultato viene calcolato sottraendo `x` da zero. La negazione decimale equivale all'uso dell'operatore unario minus di tipo `System.Decimal`.

### <a name="logical-negation-operator"></a>Operatore di negazione logica

Per un'operazione nel formato `!x`, viene applicata la risoluzione dell'overload dell'operatore unario ([risoluzione dell'overload dell'operatore unario](expressions.md#unary-operator-overload-resolution)) per selezionare l'implementazione di un operatore specifico. L'operando viene convertito nel tipo di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore. Esiste un solo operatore di negazione logica predefinito:
```csharp
bool operator !(bool x);
```

Questo operatore calcola la negazione logica dell'operando: Se l'operando è `true`, il risultato sarà `false`. Se l'operando è `false`, il risultato sarà `true`.

### <a name="bitwise-complement-operator"></a>Operatore di complemento bit per bit

Per un'operazione nel formato `~x`, viene applicata la risoluzione dell'overload dell'operatore unario ([risoluzione dell'overload dell'operatore unario](expressions.md#unary-operator-overload-resolution)) per selezionare l'implementazione di un operatore specifico. L'operando viene convertito nel tipo di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore. Gli operatori di complemento bit per bit predefiniti sono:
```csharp
int operator ~(int x);
uint operator ~(uint x);
long operator ~(long x);
ulong operator ~(ulong x);
```

Per ognuno di questi operatori, il risultato dell'operazione è il complemento bit per bit di `x`.

Ogni tipo di enumerazione `E` fornisce in modo implicito l'operatore di complemento bit per bit seguente:

```csharp
E operator ~(E x);
```

Il risultato della valutazione di `~x`, dove `x` è un'espressione di un tipo di enumerazione `E` con un tipo sottostante `U`, è esattamente uguale alla valutazione di `(E)(~(U)x)`, ad eccezione del fatto che la conversione a `E` viene sempre eseguita come se si trovasse in un contesto `unchecked` ( [Operatori checked e unchecked](expressions.md#the-checked-and-unchecked-operators).

### <a name="prefix-increment-and-decrement-operators"></a>Operatori di incremento e decremento in forma prefissa

```antlr
pre_increment_expression
    : '++' unary_expression
    ;

pre_decrement_expression
    : '--' unary_expression
    ;
```

L'operando di un'operazione di incremento o di decremento del prefisso deve essere un'espressione classificata come variabile, un accesso a una proprietà o un accesso all'indicizzatore. Il risultato dell'operazione è un valore dello stesso tipo dell'operando.

Se l'operando di un'operazione di incremento o di decremento del prefisso è un accesso a una proprietà o a un indicizzatore, la proprietà o l'indicizzatore deve avere sia una funzione di accesso `get` che una `set`. In caso contrario, si verificherà un errore in fase di binding.

Viene applicata la risoluzione dell'overload dell'operatore unario ([risoluzione dell'overload dell'operatore unario](expressions.md#unary-operator-overload-resolution)) per selezionare l'implementazione di un operatore specifico. Per i tipi seguenti sono disponibili operatori predefiniti `++` e `--`: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, 0, 1, 2, 3 e qualsiasi tipo enum. Gli operatori `++` predefiniti restituiscono il valore prodotto aggiungendo 1 all'operando e gli operatori `--` predefiniti restituiscono il valore prodotto sottraendo 1 dall'operando. In un contesto `checked`, se il risultato di questa operazione di aggiunta o sottrazione non è compreso nell'intervallo del tipo di risultato e il tipo di risultato è un tipo integrale o un tipo enum, viene generata un'`System.OverflowException`.

L'elaborazione in fase di esecuzione di un'operazione di incremento o decremento del prefisso nel formato `++x` o `--x` prevede i passaggi seguenti:

*   Se `x` è classificato come variabile:
    * `x` viene valutato per produrre la variabile.
    * L'operatore selezionato viene richiamato con il valore `x` come argomento.
    * Il valore restituito dall'operatore viene archiviato nella posizione specificata dalla valutazione di `x`.
    * Il valore restituito dall'operatore diventa il risultato dell'operazione.
*   Se `x` è classificato come accesso a una proprietà o a un indicizzatore:
    * L'espressione dell'istanza (se `x` non è `static`) e l'elenco di argomenti (se `x` è un accesso all'indicizzatore) associato a `x` vengono valutati e i risultati vengono utilizzati nelle chiamate di funzione di accesso `get` e `set` successive.
    * Viene richiamata la funzione di accesso `get` di `x`.
    * L'operatore selezionato viene richiamato con il valore restituito dalla funzione di accesso `get` come argomento.
    * La funzione di accesso `set` di `x` viene richiamata con il valore restituito dall'operatore come argomento `value`.
    * Il valore restituito dall'operatore diventa il risultato dell'operazione.

Gli operatori `++` e `--` supportano anche la notazione suffissa ([operatori di incremento e decremento suffisso](expressions.md#postfix-increment-and-decrement-operators)). In genere, il risultato di `x++` o `x--` è il valore di `x` prima dell'operazione, mentre il risultato di `++x` o `--x` è il valore di `x` dopo l'operazione. In entrambi i casi, `x` ha lo stesso valore dopo l'operazione.

Un'implementazione `operator++` o `operator--` può essere richiamata usando la notazione suffissa o prefisso. Non è possibile avere implementazioni di operatori separate per le due notazioni.

### <a name="cast-expressions"></a>Espressioni cast

Un *cast_expression* viene utilizzato per convertire in modo esplicito un'espressione in un tipo specificato.

```antlr
cast_expression
    : '(' type ')' unary_expression
    ;
```

Un *cast_expression* nel formato `(T)E`, dove `T` è un *tipo* e `E` è un *unary_expression*, esegue una conversione esplicita ([conversioni esplicite](conversions.md#explicit-conversions)) del valore di `E` nel tipo `T`. Se non esiste alcuna conversione esplicita da `E` a `T`, si verifica un errore in fase di binding. In caso contrario, il risultato è il valore prodotto dalla conversione esplicita. Il risultato viene sempre classificato come valore, anche se `E` denota una variabile.

La grammatica per un *cast_expression* conduce a determinate ambiguità sintattica. Ad esempio, l'espressione `(x)-y` può essere interpretata come *cast_expression* (un cast di `-y` al tipo `x`) o come *additive_expression* combinato con un *parenthesized_expression* (che calcola il valore `x - y)`.

Per risolvere le ambiguità di *cast_expression* , esiste la regola seguente: Una sequenza di uno o più *token*s ([spazi vuoti](lexical-structure.md#white-space)) racchiusa tra parentesi viene considerata l'inizio di un *cast_expression* solo se si verifica almeno una delle condizioni seguenti:

*  La sequenza di token è la grammatica corretta per un *tipo*, ma non per un' *espressione*.
*  La sequenza di token è la grammatica corretta per un *tipo*e il token che segue immediatamente le parentesi di chiusura è il token "`~`", il token "`!`", il token "`(`", un *identificatore* ([sequenze di escape dei caratteri Unicode). ](lexical-structure.md#unicode-character-escape-sequences)), un *valore letterale* ([literal](lexical-structure.md#literals)) o qualsiasi *parola chiave* ([parole](lexical-structure.md#keywords)chiave) tranne 0 e 1.

Il termine "grammatica corretta" riportata in precedenza significa solo che la sequenza di token deve essere conforme alla particolare produzione grammaticale. In particolare, non considera il significato effettivo di tutti gli identificatori costitutivi. Se, ad esempio, `x` e `y` sono identificatori, `x.y` è la grammatica corretta per un tipo, anche se `x.y` non denota effettivamente un tipo.

Dalla regola di risoluzione dell'ambiguità segue che, se `x` e `y` sono identificatori, `(x)y`, `(x)(y)` e `(x)(-y)` sono *cast_expression*s, ma `(x)-y` non lo è, anche se `x` identifica un tipo. Tuttavia, se `x` è una parola chiave che identifica un tipo predefinito, ad esempio `int`, tutti e quattro i moduli sono *cast_expression*s (perché tale parola chiave non può essere un'espressione da sola).

### <a name="await-expressions"></a>Espressioni await

L'operatore await viene usato per sospendere la valutazione della funzione asincrona di inclusione fino al completamento dell'operazione asincrona rappresentata dall'operando.

```antlr
await_expression
    : 'await' unary_expression
    ;
```

Una *await_expression* è consentita solo nel corpo di una funzione asincrona ([iteratori](classes.md#iterators)). All'interno della funzione asincrona di inclusione più vicina, un *await_expression* non può verificarsi in queste posizioni:

*  All'interno di una funzione anonima (non asincrona) nidificata
*  All'interno del blocco di un *lock_statement*
*  In un contesto non sicuro

Si noti che non è possibile eseguire un *await_expression* nella maggior parte dei punti all'interno di un *query_expression*, perché sono sintatticamente trasformati in modo da usare espressioni lambda non asincrone.

All'interno di una funzione asincrona, non è possibile usare `await` come identificatore. Non esiste pertanto alcuna ambiguità sintattica tra le espressioni await e le varie espressioni che coinvolgono gli identificatori. All'esterno delle funzioni asincrone, `await` funge da identificatore normale.

L'operando di un *await_expression* è denominato ***Task***. Rappresenta un'operazione asincrona che può essere o meno completata al momento della valutazione del *await_expression* . Lo scopo dell'operatore await è sospendere l'esecuzione della funzione asincrona di inclusione fino al completamento dell'attività attesa e quindi ottenere il risultato.

#### <a name="awaitable-expressions"></a>Espressioni awaitable

È necessario che l'attività di un'espressione await sia ***awaitable***. Un'espressione `t` è awaitable se uno degli elementi seguenti include:

*  `t` è di tipo tempo di compilazione `dynamic`
*  `t` dispone di un'istanza accessibile o di un metodo di estensione denominato `GetAwaiter` senza parametri e nessun parametro di tipo e un tipo restituito `A` per il quale tutti gli elementi seguenti contengono:
   * `A` implementa l'interfaccia `System.Runtime.CompilerServices.INotifyCompletion` (in seguito nota come `INotifyCompletion` per brevità)
   * `A` ha una proprietà di istanza accessibile e leggibile `IsCompleted` di tipo `bool`
   * `A` ha un metodo di istanza accessibile `GetResult` senza parametri e nessun parametro di tipo

Lo scopo del metodo `GetAwaiter` è quello di ottenere un ***awaiter*** per l'attività. Il tipo `A` viene chiamato ***tipo awaiter*** per l'espressione await.

Lo scopo della proprietà `IsCompleted` è determinare se l'attività è già stata completata. In tal caso, non è necessario sospendere la valutazione.

Lo scopo del metodo `INotifyCompletion.OnCompleted` consiste nell'iscrivere una "continuazione" all'attività; ovvero un delegato (di tipo `System.Action`) che verrà richiamato una volta completata l'attività.

Lo scopo del metodo `GetResult` è ottenere il risultato dell'attività dopo che è stata completata. Questo risultato può essere completato correttamente, possibilmente con un valore di risultato, oppure un'eccezione generata dal metodo `GetResult`.

#### <a name="classification-of-await-expressions"></a>Classificazione delle espressioni await

L'espressione `await t` è classificata allo stesso modo dell'espressione `(t).GetAwaiter().GetResult()`. Pertanto, se il tipo restituito di `GetResult` è `void`, *await_expression* viene classificato come Nothing. Se dispone di un tipo restituito non void `T`, *await_expression* viene classificato come valore di tipo `T`.

#### <a name="runtime-evaluation-of-await-expressions"></a>Valutazione del runtime delle espressioni await

In fase di esecuzione, l'espressione `await t` viene valutata nel modo seguente:

*  Un awaiter `a` viene ottenuto tramite la valutazione dell'espressione `(t).GetAwaiter()`.
*  Viene ottenuto un `bool` `b` tramite la valutazione dell'espressione `(a).IsCompleted`.
*  Se `b` è `false`, la valutazione dipende dal fatto che `a` implementi l'interfaccia `System.Runtime.CompilerServices.ICriticalNotifyCompletion` (in seguito nota come `ICriticalNotifyCompletion` per brevità). Questa verifica viene eseguita in fase di binding. ad esempio, in fase di esecuzione se `a` ha il tipo di fase di compilazione `dynamic` e in fase di compilazione in caso contrario. Let `r` indica il delegato di ripresa ([iteratori](classes.md#iterators)):
    * Se `a` non implementa `ICriticalNotifyCompletion`, viene valutata l'espressione `(a as (INotifyCompletion)).OnCompleted(r)`.
    * Se `a` implementa `ICriticalNotifyCompletion`, viene valutata l'espressione `(a as (ICriticalNotifyCompletion)).UnsafeOnCompleted(r)`.
    * La valutazione viene quindi sospesa e il controllo viene restituito al chiamante corrente della funzione asincrona.
*  Immediatamente dopo (se `b` era `true`) o dopo una chiamata successiva del delegato di ripresa (se `b` era `false`), viene valutata l'espressione `(a).GetResult()`. Se restituisce un valore, tale valore è il risultato di *await_expression*. In caso contrario, il risultato è Nothing.

L'implementazione di un awaiter dei metodi di interfaccia `INotifyCompletion.OnCompleted` e `ICriticalNotifyCompletion.UnsafeOnCompleted` dovrebbe far sì che il delegato `r` venga richiamato al massimo una volta. In caso contrario, il comportamento della funzione asincrona di inclusione non è definito.

## <a name="arithmetic-operators"></a>Operatori aritmetici

Gli operatori `*`, `/`, `%`, `+` e `-` sono detti operatori aritmetici.

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

Se un operando di un operatore aritmetico ha il tipo in fase di compilazione `dynamic`, l'espressione viene associata dinamicamente ([associazione dinamica](expressions.md#dynamic-binding)). In questo caso il tipo in fase di compilazione dell'espressione è `dynamic` e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di runtime degli operandi che hanno il tipo in fase di compilazione `dynamic`.

### <a name="multiplication-operator"></a>Operatore di moltiplicazione

Per un'operazione nel formato `x * y`, viene applicata la risoluzione dell'overload degli operatori binari ([risoluzione dell'overload degli operatori binari](expressions.md#binary-operator-overload-resolution)) per selezionare un'implementazione specifica di un operatore. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Gli operatori di moltiplicazione predefiniti sono elencati di seguito. Tutti gli operatori calcolano il prodotto di `x` e `y`.

*  Moltiplicazione Integer:

   ```csharp
   int operator *(int x, int y);
   uint operator *(uint x, uint y);
   long operator *(long x, long y);
   ulong operator *(ulong x, ulong y);
   ```

   In un contesto `checked`, se il prodotto non è compreso nell'intervallo del tipo di risultato, viene generata un'`System.OverflowException`. In un contesto `unchecked`, gli overflow non vengono segnalati e tutti i bit di ordine elevato significativi che non rientrano nell'intervallo del tipo di risultato vengono eliminati.


*  Moltiplicazione a virgola mobile:

   ```csharp
   float operator *(float x, float y);
   double operator *(double x, double y);
   ```

   Il prodotto viene calcolato in base alle regole dell'aritmetica IEEE 754. Nella tabella seguente sono elencati i risultati di tutte le possibili combinazioni di valori finiti diversi da zero, zeri, infiniti e NaN. Nella tabella `x` e `y` sono valori finiti positivi. `z` è il risultato di `x * y`. Se il risultato è troppo grande per il tipo di destinazione, `z` è infinito. Se il risultato è troppo piccolo per il tipo di destinazione, `z` è zero.

   |      |      |      |     |     |      |      |     |
   |:----:|-----:|:----:|:---:|:---:|:----:|:----:|:----|
   |      | \+ y   | -y   | +0  | -0  | +inf | -inf | NaN | 
   | \+ x   | +z   | -z   | +0  | -0  | +inf | -inf | NaN | 
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

   Se il valore risultante è troppo grande per essere rappresentato nel formato `decimal`, viene generata una `System.OverflowException`. Se il valore del risultato è troppo piccolo per essere rappresentato nel formato `decimal`, il risultato è zero. La scala del risultato, prima di qualsiasi arrotondamento, è la somma delle scale dei due operandi.

   La moltiplicazione decimale è equivalente all'uso dell'operatore di moltiplicazione di tipo `System.Decimal`.


### <a name="division-operator"></a>Operatore di divisione

Per un'operazione nel formato `x / y`, viene applicata la risoluzione dell'overload degli operatori binari ([risoluzione dell'overload degli operatori binari](expressions.md#binary-operator-overload-resolution)) per selezionare un'implementazione specifica di un operatore. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Gli operatori di divisione predefiniti sono elencati di seguito. Tutti gli operatori calcolano il quoziente di `x` e `y`.

*  Divisione Integer:

   ```csharp
   int operator /(int x, int y);
   uint operator /(uint x, uint y);
   long operator /(long x, long y);
   ulong operator /(ulong x, ulong y);
   ```

   Se il valore dell'operando destro è zero, viene generata un'eccezione `System.DivideByZeroException`.

   La divisione arrotonda il risultato verso lo zero. Pertanto, il valore assoluto del risultato è il numero intero massimo possibile minore o uguale al valore assoluto del quoziente dei due operandi. Il risultato è zero o positivo quando i due operandi hanno lo stesso segno e zero o negativo quando i due operandi hanno segni opposti.

   Se l'operando sinistro è il valore più piccolo rappresentabile `int` o `long` e l'operando destro è `-1`, si verifica un overflow. In un contesto `checked`, viene generata un'eccezione `System.ArithmeticException` (o una sottoclasse). In un contesto `unchecked`, viene definito in fase di implementazione, a seconda che venga generata una `System.ArithmeticException` (o una sottoclasse) oppure che l'overflow non venga segnalato con il valore risultante che corrisponde a quello dell'operando sinistro.

*  Divisione a virgola mobile:

   ```csharp
   float operator /(float x, float y);
   double operator /(double x, double y);
   ```

   Il quoziente viene calcolato in base alle regole dell'aritmetica IEEE 754. Nella tabella seguente sono elencati i risultati di tutte le possibili combinazioni di valori finiti diversi da zero, zeri, infiniti e NaN. Nella tabella `x` e `y` sono valori finiti positivi. `z` è il risultato di `x / y`. Se il risultato è troppo grande per il tipo di destinazione, `z` è infinito. Se il risultato è troppo piccolo per il tipo di destinazione, `z` è zero.

   |      |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | \+ y   | -y   | +0   | -0   | +inf | -inf | NaN  | 
   | \+ x   | +z   | -z   | +inf | -inf | +0   | -0   | NaN  | 
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

   Se il valore dell'operando destro è zero, viene generata un'eccezione `System.DivideByZeroException`. Se il valore risultante è troppo grande per essere rappresentato nel formato `decimal`, viene generata una `System.OverflowException`. Se il valore del risultato è troppo piccolo per essere rappresentato nel formato `decimal`, il risultato è zero. La scala del risultato è la scala più piccola che conserverà un risultato uguale al valore decimale rappresentabile più vicino al vero risultato matematico.

   La divisione decimale equivale all'uso dell'operatore di divisione di tipo `System.Decimal`.


### <a name="remainder-operator"></a>Operatore di resto

Per un'operazione nel formato `x % y`, viene applicata la risoluzione dell'overload degli operatori binari ([risoluzione dell'overload degli operatori binari](expressions.md#binary-operator-overload-resolution)) per selezionare un'implementazione specifica di un operatore. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Gli operatori di resto predefiniti sono elencati di seguito. Tutti gli operatori calcolano il resto della divisione tra `x` e `y`.

*  Resto integer:

   ```csharp
   int operator %(int x, int y);
   uint operator %(uint x, uint y);
   long operator %(long x, long y);
   ulong operator %(ulong x, ulong y);
   ```

   Il risultato di `x % y` è il valore prodotto da `x - (x / y) * y`. Se `y` è zero, viene generata un'`System.DivideByZeroException`.

   Se l'operando sinistro è il valore più piccolo `int` o `long` e l'operando destro è `-1`, viene generata un'`System.OverflowException`. In nessun caso `x % y` genera un'eccezione in cui `x / y` non genera un'eccezione.

*  Resto a virgola mobile:

   ```csharp
   float operator %(float x, float y);
   double operator %(double x, double y);
   ```

   Nella tabella seguente sono elencati i risultati di tutte le possibili combinazioni di valori finiti diversi da zero, zeri, infiniti e NaN. Nella tabella `x` e `y` sono valori finiti positivi. `z` è il risultato di `x % y` e viene calcolato come `x - n * y`, dove `n` è il numero intero massimo possibile minore o uguale a `x / y`. Questo metodo di calcolo del resto è analogo a quello usato per gli operandi Integer, ma è diverso dalla definizione IEEE 754 (in cui `n` è il numero intero più vicino al `x / y`).

   |      |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | \+ y   | -y   | +0   | -0   | +inf | -inf | NaN  | 
   | \+ x   | +z   | +z   | NaN  | NaN  | x    | x    | NaN  | 
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

   Se il valore dell'operando destro è zero, viene generata un'eccezione `System.DivideByZeroException`. La scala del risultato, prima di qualsiasi arrotondamento, è la maggiore delle scale dei due operandi e il segno del risultato, se diverso da zero, è uguale a quello di `x`.

   Il resto decimale equivale all'uso dell'operatore resto di tipo `System.Decimal`.


### <a name="addition-operator"></a>Operatore di addizione

Per un'operazione nel formato `x + y`, viene applicata la risoluzione dell'overload degli operatori binari ([risoluzione dell'overload degli operatori binari](expressions.md#binary-operator-overload-resolution)) per selezionare un'implementazione specifica di un operatore. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Gli operatori di addizione predefiniti sono elencati di seguito. Per i tipi numerici e di enumerazione, gli operatori di addizione predefiniti calcolano la somma dei due operandi. Quando uno o entrambi gli operandi sono di tipo stringa, gli operatori di addizione predefiniti concatenano la rappresentazione di stringa degli operandi.

*  Aggiunta Integer:

   ```csharp
   int operator +(int x, int y);
   uint operator +(uint x, uint y);
   long operator +(long x, long y);
   ulong operator +(ulong x, ulong y);
   ```

   In un contesto `checked`, se la somma non è compresa nell'intervallo del tipo di risultato, viene generata un'`System.OverflowException`. In un contesto `unchecked`, gli overflow non vengono segnalati e tutti i bit di ordine elevato significativi che non rientrano nell'intervallo del tipo di risultato vengono eliminati.

*  Aggiunta a virgola mobile:

   ```csharp
   float operator +(float x, float y);
   double operator +(double x, double y);
   ```

   La somma viene calcolata in base alle regole dell'aritmetica IEEE 754. Nella tabella seguente sono elencati i risultati di tutte le possibili combinazioni di valori finiti diversi da zero, zeri, infiniti e NaN. Nella tabella, `x` e `y` sono valori finiti diversi da zero e `z` è il risultato di `x + y`. Se `x` e `y` hanno la stessa grandezza ma segni opposti, `z` è zero positivo. Se `x + y` è troppo grande per essere rappresentato nel tipo di destinazione, `z` è un infinito con lo stesso segno di `x + y`.

   |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | y    | +0   | -0   | +inf | -inf | NaN  | 
   | x    | l    | x    | x    | +inf | -inf | NaN  | 
   | +0   | y    | +0   | +0   | +inf | -inf | NaN  | 
   | -0   | y    | +0   | -0   | +inf | -inf | NaN  | 
   | +inf | +inf | +inf | +inf | +inf | NaN  | NaN  | 
   | -inf | -inf | -inf | -inf | NaN  | -inf | NaN  | 
   | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | NaN  | 

*  Addizione decimale:

   ```csharp
   decimal operator +(decimal x, decimal y);
   ```

   Se il valore risultante è troppo grande per essere rappresentato nel formato `decimal`, viene generata una `System.OverflowException`. La scala del risultato, prima di qualsiasi arrotondamento, è la maggiore delle scale dei due operandi.

   L'addizione decimale equivale a usare l'operatore di addizione di tipo `System.Decimal`.

*  Aggiunta dell'enumerazione. Ogni tipo di enumerazione fornisce in modo implicito gli operatori predefiniti seguenti, dove `E` è il tipo enum e `U` è il tipo sottostante di `E`:

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

   Questi overload dell'operatore binario `+` eseguono la concatenazione di stringhe. Se un operando di concatenazione di stringhe è `null`, viene sostituita una stringa vuota. In caso contrario, qualsiasi argomento non di tipo stringa viene convertito nella relativa rappresentazione di stringa richiamando il metodo virtuale `ToString` ereditato dal tipo `object`. Se `ToString` restituisce `null`, viene sostituita una stringa vuota.

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

   Il risultato dell'operatore di concatenazione delle stringhe è una stringa costituita dai caratteri dell'operando sinistro, seguiti dai caratteri dell'operando di destra. L'operatore di concatenazione delle stringhe non restituisce mai un valore `null`. Se non è disponibile memoria sufficiente per allocare la stringa risultante, è possibile che venga generata un'`System.OutOfMemoryException`.

*  Combinazione di delegati. Ogni tipo delegato fornisce in modo implicito l'operatore predefinito seguente, dove `D` è il tipo delegato:

   ```csharp
   D operator +(D x, D y);
   ```

   L'operatore `+` binario esegue una combinazione di delegati quando entrambi gli operandi sono di tipo delegato `D`. Se gli operandi hanno tipi delegati diversi, si verifica un errore in fase di binding. Se il primo operando è `null`, il risultato dell'operazione è il valore del secondo operando (anche se è anche `null`). In caso contrario, se il secondo operando è `null`, il risultato dell'operazione è il valore del primo operando. In caso contrario, il risultato dell'operazione è una nuova istanza di delegato che, quando richiamata, richiama il primo operando e quindi richiama il secondo operando. Per esempi di combinazione di delegati, vedere [operatore di sottrazione](expressions.md#subtraction-operator) e [chiamata al delegato](delegates.md#delegate-invocation). Poiché `System.Delegate` non è un tipo delegato, `operator` @ no__t-2 non è definito.

### <a name="subtraction-operator"></a>Operatore di sottrazione

Per un'operazione nel formato `x - y`, viene applicata la risoluzione dell'overload degli operatori binari ([risoluzione dell'overload degli operatori binari](expressions.md#binary-operator-overload-resolution)) per selezionare un'implementazione specifica di un operatore. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Gli operatori di sottrazione predefiniti sono elencati di seguito. Gli operatori sottraggono tutti `y` da `x`.

*  Sottrazione integer:

   ```csharp
   int operator -(int x, int y);
   uint operator -(uint x, uint y);
   long operator -(long x, long y);
   ulong operator -(ulong x, ulong y);
   ```

   In un contesto `checked`, se la differenza non è compresa nell'intervallo del tipo di risultato, viene generata un'`System.OverflowException`. In un contesto `unchecked`, gli overflow non vengono segnalati e tutti i bit di ordine elevato significativi che non rientrano nell'intervallo del tipo di risultato vengono eliminati.

*  Sottrazione a virgola mobile:

   ```csharp
   float operator -(float x, float y);
   double operator -(double x, double y);
   ```

   La differenza viene calcolata in base alle regole dell'aritmetica IEEE 754. Nella tabella seguente sono elencati i risultati di tutte le possibili combinazioni di valori finiti diversi da zero, zeri, infiniti e NaNs. Nella tabella, `x` e `y` sono valori finiti diversi da zero e `z` è il risultato di `x - y`. Se `x` e `y` sono uguali, `z` è zero positivo. Se `x - y` è troppo grande per essere rappresentato nel tipo di destinazione, `z` è un infinito con lo stesso segno di `x - y`.

   |      |      |      |      |      |      |     |
   |:----:|:----:|:----:|:----:|:----:|:----:|:---:|
   |      | y    | +0   | -0   | +inf | -inf | NaN | 
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

   Se il valore risultante è troppo grande per essere rappresentato nel formato `decimal`, viene generata una `System.OverflowException`. La scala del risultato, prima di qualsiasi arrotondamento, è la maggiore delle scale dei due operandi.

   La sottrazione decimale equivale all'uso dell'operatore di sottrazione di tipo `System.Decimal`.

*  Sottrazione di enumerazione. Ogni tipo di enumerazione fornisce in modo implicito l'operatore predefinito seguente, dove `E` è il tipo enum e `U` è il tipo sottostante di `E`:

   ```csharp
   U operator -(E x, E y);
   ```

   Questo operatore viene valutato esattamente come `(U)((U)x - (U)y)`. In altre parole, l'operatore calcola la differenza tra i valori ordinali di `x` e `y` e il tipo del risultato è il tipo sottostante dell'enumerazione.

   ```csharp
   E operator -(E x, U y);
   ```

   Questo operatore viene valutato esattamente come `(E)((U)x - y)`. In altre parole, l'operatore sottrae un valore dal tipo sottostante dell'enumerazione, restituendo un valore dell'enumerazione.

*  Rimozione del delegato. Ogni tipo delegato fornisce in modo implicito l'operatore predefinito seguente, dove `D` è il tipo delegato:

   ```csharp
   D operator -(D x, D y);
   ```

   L'operatore `-` binario esegue la rimozione dei delegati quando entrambi gli operandi sono di tipo delegato `D`. Se gli operandi hanno tipi delegati diversi, si verifica un errore in fase di binding. Se il primo operando è `null`, il risultato dell'operazione è `null`. In caso contrario, se il secondo operando è `null`, il risultato dell'operazione è il valore del primo operando. In caso contrario, entrambi gli operandi rappresentano gli elenchi chiamate ([dichiarazioni delegate](delegates.md#delegate-declarations)) con una o più voci e il risultato è un nuovo elenco chiamate costituito dall'elenco del primo operando con le voci del secondo operando rimosse da esso, purché il secondo l'elenco degli operandi è un sottoelenco contiguo appropriato del primo oggetto.     Per determinare l'uguaglianza di sottoelenchi, le voci corrispondenti vengono confrontate come per l'operatore di uguaglianza dei delegati ([operatori di uguaglianza dei delegati](expressions.md#delegate-equality-operators)). In caso contrario, il risultato è il valore dell'operando sinistro. Nessuno degli operandi è stato modificato nel processo. Se l'elenco del secondo operando corrisponde a più sottoelenchi di voci contigue nell'elenco del primo operando, viene rimosso il sottoelenco corrispondente più a destra delle voci contigue. Se la rimozione restituisce un elenco vuoto, il risultato è `null`. Esempio:

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

Gli operatori `<<` e `>>` vengono utilizzati per eseguire operazioni di spostamento di bit.

```antlr
shift_expression
    : additive_expression
    | shift_expression '<<' additive_expression
    | shift_expression right_shift additive_expression
    ;
```

Se un operando di un *shift_expression* ha il tipo in fase di compilazione `dynamic`, l'espressione viene associata dinamicamente ([associazione dinamica](expressions.md#dynamic-binding)). In questo caso il tipo in fase di compilazione dell'espressione è `dynamic` e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di runtime degli operandi che hanno il tipo in fase di compilazione `dynamic`.

Per un'operazione nel formato `x << count` o `x >> count`, viene applicata la risoluzione dell'overload degli operatori binari ([risoluzione dell'overload degli operatori binari](expressions.md#binary-operator-overload-resolution)) per selezionare un'implementazione specifica dell'operatore. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Quando si dichiara un operatore Shift di overload, il tipo del primo operando deve essere sempre la classe o lo struct che contiene la dichiarazione dell'operatore e il tipo del secondo operando deve sempre essere `int`.

Gli operatori shift predefiniti sono elencati di seguito.

*  Spostamento a sinistra:

   ```csharp
   int operator <<(int x, int count);
   uint operator <<(uint x, int count);
   long operator <<(long x, int count);
   ulong operator <<(ulong x, int count);
   ```

   L'operatore `<<` sposta `x` a sinistra di un numero di bit calcolato come descritto di seguito.

   I bit più significativi che non rientrano nell'intervallo del tipo di risultato di `x` vengono eliminati, i bit rimanenti vengono spostati a sinistra e le posizioni di bit vuote di ordine inferiore vengono impostate su zero.

*  Spostamento a destra:

   ```csharp
   int operator >>(int x, int count);
   uint operator >>(uint x, int count);
   long operator >>(long x, int count);
   ulong operator >>(ulong x, int count);
   ```

   L'operatore `>>` sposta `x` a destra di un numero di bit calcolato come descritto di seguito.

   Quando `x` è di tipo `int` o `long`, i bit meno significativi di `x` vengono eliminati, i bit rimanenti vengono spostati a destra e le posizioni di bit vuote di ordine superiore sono impostate su zero se `x` è non negativo e impostato su uno se `x` è negativo.

   Quando `x` è di tipo `uint` o `ulong`, i bit meno significativi di `x` vengono eliminati, i bit rimanenti vengono spostati a destra e le posizioni di bit vuote di ordine superiore sono impostate su zero.

Per gli operatori predefiniti, il numero di bit da spostare viene calcolato nel modo seguente:

*  Quando il tipo di `x` è `int` o `uint`, il numero di spostamenti viene assegnato dai cinque bit di ordine inferiore di `count`. In altre parole, il conteggio dello spostamento viene calcolato da `count & 0x1F`.
*  Quando il tipo di `x` è `long` o `ulong`, il conteggio dello spostamento viene assegnato dai sei bit meno significativi di `count`. In altre parole, il conteggio dello spostamento viene calcolato da `count & 0x3F`.

Se il numero di spostamenti risultante è pari a zero, gli operatori shift restituiscono semplicemente il valore di `x`.

Le operazioni di spostamento non causano mai overflow e producono gli stessi risultati nei contesti `checked` e `unchecked`.

Quando l'operando sinistro dell'operatore `>>` è di un tipo integrale con segno, l'operatore esegue un turno aritmetico a destra, in cui il valore del bit più significativo (il bit di segno) dell'operando viene propagato alle posizioni di bit vuote di ordine superiore. Quando l'operando sinistro dell'operatore `>>` è di un tipo integrale senza segno, l'operatore esegue un diritto di spostamento logico in cui le posizioni di bit vuote di ordine elevato sono sempre impostate su zero. Per eseguire l'operazione opposta di quella dedotta dal tipo di operando, è possibile usare cast espliciti. Se, ad esempio, `x` è una variabile di tipo `int`, l'operazione `unchecked((int)((uint)x >> y))` esegue un cambio logico a destra di `x`.

## <a name="relational-and-type-testing-operators"></a>Operatori relazionali e operatori di test del tipo

Gli operatori `==`, `!=`, `<`, `>`, `<=`, `>=`, `is` e `as` sono detti operatori relazionali e di test dei tipi.

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

L'operatore `is` è descritto nell'operatore [is](expressions.md#the-is-operator) e l'operatore `as` viene descritto nell'operatore [As](expressions.md#the-as-operator).

Gli operatori `==`, `!=`, `<`, `>`, `<=` e `>=` sono ***operatori di confronto***.

Se un operando di un operatore di confronto ha il tipo in fase di compilazione `dynamic`, l'espressione viene associata dinamicamente ([associazione dinamica](expressions.md#dynamic-binding)). In questo caso il tipo in fase di compilazione dell'espressione è `dynamic` e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di runtime degli operandi che hanno il tipo in fase di compilazione `dynamic`.

Per un'operazione nel formato `x` *op* `y`, dove *op* è un operatore di confronto, viene applicata la risoluzione dell'overload (risoluzione dell'[Overload degli operatori binari](expressions.md#binary-operator-overload-resolution)) per selezionare un'implementazione specifica dell'operatore. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Gli operatori di confronto predefiniti sono descritti nelle sezioni seguenti. Tutti gli operatori di confronto predefiniti restituiscono un risultato di tipo `bool`, come descritto nella tabella seguente.


| __Operazione__ | __Risultato__                                                       |
|---------------|------------------------------------------------------------------|
| `x == y`      | `true` se `x` è uguale a `y`, `false` in caso contrario                 | 
| `x != y`      | `true` se `x` è diverso da `y`, `false` in caso contrario             | 
| `x < y`       | `true` se `x` è minore di `y`, `false` in caso contrario                | 
| `x > y`       | `true` se `x` è maggiore di `y`, `false` in caso contrario             | 
| `x <= y`      | `true` se `x` è minore o uguale a `y`, `false` in caso contrario    | 
| `x >= y`      | `true` se `x` è maggiore o uguale a `y`, `false` in caso contrario | 

### <a name="integer-comparison-operators"></a>Operatori di confronto integer

Gli operatori di confronto Integer predefiniti sono:
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

Ognuno di questi operatori confronta i valori numerici dei due operandi Integer e restituisce un valore `bool` che indica se la relazione specifica è `true` o `false`.

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

Gli operatori confrontano gli operandi secondo le regole dello standard IEEE 754:

*  Se uno degli operandi è NaN, il risultato è `false` per tutti gli operatori eccetto `!=`, per il quale il risultato è `true`. Per due operandi qualsiasi, `x != y` produce sempre lo stesso risultato di `!(x == y)`. Tuttavia, quando uno o entrambi gli operandi sono NaN, gli operatori `<`, `>`, `<=` e `>=` non producono gli stessi risultati della negazione logica dell'operatore opposto. Ad esempio, se uno dei `x` e `y` è NaN, `x < y` è `false`, ma `!(x >= y)` è `true`.
*  Quando nessuno degli operandi è NaN, gli operatori confrontano i valori dei due operandi a virgola mobile rispetto all'ordinamento

   ```csharp
   -inf < -max < ... < -min < -0.0 == +0.0 < +min < ... < +max < +inf
   ```

   dove `min` e `max` sono i valori finiti positivi più piccoli e più grandi, che possono essere rappresentati nel formato a virgola mobile specificato. Gli effetti significativi di questo ordine sono i seguenti:
   * Gli zeri negativi e positivi sono considerati uguali.
   * Un infinito negativo è considerato minore di tutti gli altri valori, ma è uguale a un altro infinito negativo.
   * Un infinito positivo è considerato maggiore di tutti gli altri valori, ma uguale a un altro infinito positivo.

### <a name="decimal-comparison-operators"></a>Operatori di confronto decimali

Gli operatori di confronto decimali predefiniti sono:
```csharp
bool operator ==(decimal x, decimal y);
bool operator !=(decimal x, decimal y);
bool operator <(decimal x, decimal y);
bool operator >(decimal x, decimal y);
bool operator <=(decimal x, decimal y);
bool operator >=(decimal x, decimal y);
```

Ognuno di questi operatori confronta i valori numerici dei due operandi decimali e restituisce un valore `bool` che indica se la relazione specifica è `true` o `false`. Ogni confronto decimale equivale a usare l'operatore relazionale o di uguaglianza corrispondente di tipo `System.Decimal`.

### <a name="boolean-equality-operators"></a>Operatori di uguaglianza booleani

Gli operatori di uguaglianza booleani predefiniti sono:
```csharp
bool operator ==(bool x, bool y);
bool operator !=(bool x, bool y);
```

Il risultato di `==` è `true` se `x` e `y` sono `true` o se `x` e `y` sono `false`. In caso contrario, il risultato è `false`.

Il risultato di `!=` è `false` se `x` e `y` sono `true` o se `x` e `y` sono `false`. In caso contrario, il risultato è `true`. Quando gli operandi sono di tipo `bool`, l'operatore `!=` produce lo stesso risultato dell'operatore `^`.

### <a name="enumeration-comparison-operators"></a>Operatori di confronto di enumerazione

Ogni tipo di enumerazione fornisce in modo implicito gli operatori di confronto predefiniti seguenti:
```csharp
bool operator ==(E x, E y);
bool operator !=(E x, E y);
bool operator <(E x, E y);
bool operator >(E x, E y);
bool operator <=(E x, E y);
bool operator >=(E x, E y);
```

Il risultato della valutazione di `x op y`, in cui `x` e `y` sono espressioni di un tipo di enumerazione `E` con un tipo sottostante `U` e `op` è uno degli operatori di confronto, è esattamente uguale alla valutazione di `((U)x) op ((U)y)`. In altre parole, gli operatori di confronto del tipo di enumerazione confrontano semplicemente i valori integrali sottostanti dei due operandi.

### <a name="reference-type-equality-operators"></a>Operatori di uguaglianza del tipo di riferimento

Gli operatori di uguaglianza del tipo di riferimento predefiniti sono:
```csharp
bool operator ==(object x, object y);
bool operator !=(object x, object y);
```

Gli operatori restituiscono il risultato del confronto tra i due riferimenti per verificarne l'uguaglianza o la mancata uguaglianza.

Poiché gli operatori di uguaglianza del tipo di riferimento predefiniti accettano gli operandi di tipo `object`, si applicano a tutti i tipi che non dichiarano i membri applicabili `operator ==` e `operator !=`. Viceversa, gli operatori di uguaglianza definiti dall'utente applicabili nascondono efficacemente gli operatori di uguaglianza del tipo di riferimento predefiniti.

Gli operatori di uguaglianza del tipo di riferimento predefiniti richiedono uno dei seguenti elementi:

*  Entrambi gli operandi sono un valore di un tipo noto come *reference_type* o valore letterale `null`. Inoltre, esiste una conversione esplicita del riferimento ([conversioni esplicite di riferimento](conversions.md#explicit-reference-conversions)) dal tipo di uno degli operandi al tipo dell'altro operando.
*  Un operando è un valore di tipo `T` dove `T` è un *type_parameter* e l'altro operando è il valore letterale `null`. Inoltre `T` non dispone del vincolo di tipo valore.

A meno che una di queste condizioni non sia vera, si verifica un errore in fase di binding. Le implicazioni rilevanti di queste regole sono:

*  Si tratta di un errore in fase di binding per usare gli operatori di uguaglianza del tipo di riferimento predefiniti per confrontare due riferimenti che sono noti come diversi in fase di binding. Se, ad esempio, i tipi in fase di binding degli operandi sono due tipi di classe `A` e `B` e se nessuno dei due `A` né `B` deriva dall'altro, non sarebbe possibile che i due operandi facciano riferimento allo stesso oggetto. L'operazione viene pertanto considerata un errore in fase di binding.
*  Gli operatori di uguaglianza del tipo di riferimento predefiniti non consentono il confronto degli operandi dei tipi di valore. Pertanto, a meno che un tipo struct non dichiari i propri operatori di uguaglianza, non è possibile confrontare i valori di tale tipo di struct.
*  Gli operatori di uguaglianza del tipo di riferimento predefiniti non provocano mai operazioni di conversione boxing per i rispettivi operandi. Sarebbe inutile eseguire tali operazioni di conversione boxing, dal momento che i riferimenti alle istanze boxed appena allocate sarebbero necessariamente diversi da tutti gli altri riferimenti.
*  Se un operando di un tipo di parametro di tipo `T` viene confrontato con `null` e il tipo in fase di esecuzione di `T` è un tipo di valore, il risultato del confronto è `false`.

Nell'esempio seguente viene verificato se un argomento di un tipo di parametro di tipo non vincolato è `null`.
```csharp
class C<T>
{
    void F(T x) {
        if (x == null) throw new ArgumentNullException();
        ...
    }
}
```

Il costrutto `x == null` è consentito anche se `T` potrebbe rappresentare un tipo di valore e il risultato viene semplicemente definito come `false` se `T` è un tipo valore.

Per un'operazione nel formato `x == y` o `x != y`, se è presente una `operator ==` o `operator !=` applicabile, le regole di risoluzione dell'overload degli operatori ([risoluzione dell'overload degli operatori binari](expressions.md#binary-operator-overload-resolution)) selezioneranno l'operatore anziché l'uguaglianza del tipo di riferimento predefinito operatore. Tuttavia, è sempre possibile selezionare l'operatore di uguaglianza del tipo di riferimento predefinito eseguendo il cast esplicito di uno o entrambi gli operandi al tipo `object`. Esempio
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
```console
True
False
False
False
```

Le variabili `s` e `t` fanno riferimento a due istanze Distinct `string` contenenti gli stessi caratteri. Il primo output del confronto `True` perché l'operatore di uguaglianza delle stringhe predefinito ([operatori di uguaglianza di stringa](expressions.md#string-equality-operators)) viene selezionato quando entrambi gli operandi sono di tipo `string`. I restanti confronti tutti gli output `False` perché l'operatore di uguaglianza del tipo di riferimento predefinito è selezionato quando uno o entrambi gli operandi sono di tipo `object`.

Si noti che la tecnica precedente non è significativa per i tipi di valore. Esempio
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
Restituisce `False` perché i cast creano riferimenti a due istanze separate di valori boxed `int`.

### <a name="string-equality-operators"></a>Operatori di uguaglianza delle stringhe

Gli operatori di uguaglianza di stringa predefiniti sono:
```csharp
bool operator ==(string x, string y);
bool operator !=(string x, string y);
```

Due valori @no__t 0 sono considerati uguali quando si verifica una delle condizioni seguenti:

*  Entrambi i valori sono `null`.
*  Entrambi i valori sono riferimenti non null alle istanze di stringa con lunghezze identiche e caratteri identici in ogni posizione del carattere.

Gli operatori di uguaglianza di stringa confrontano i valori stringa anziché i riferimenti di stringa. Quando due istanze di stringa separate contengono esattamente la stessa sequenza di caratteri, i valori delle stringhe sono uguali, ma i riferimenti sono diversi. Come descritto in [operatori di uguaglianza dei tipi di riferimento](expressions.md#reference-type-equality-operators), gli operatori di uguaglianza del tipo di riferimento possono essere usati per confrontare i riferimenti di stringa anziché i valori stringa.

### <a name="delegate-equality-operators"></a>Operatori di uguaglianza delegati

Ogni tipo delegato fornisce in modo implicito gli operatori di confronto predefiniti seguenti:

```csharp
bool operator ==(System.Delegate x, System.Delegate y);
bool operator !=(System.Delegate x, System.Delegate y);
```

Due istanze delegate sono considerate uguali, come indicato di seguito:

*  Se una delle istanze delegate è `null`, sono uguali se e solo se entrambe sono `null`.
*  Se i delegati hanno un tipo diverso in fase di esecuzione, non sono mai uguali.
*  Se entrambe le istanze del delegato hanno un elenco chiamate ([dichiarazioni di Delegate](delegates.md#delegate-declarations)), tali istanze sono uguali se e solo se i relativi elenchi chiamate hanno la stessa lunghezza e ogni voce nell'elenco chiamate è uguale (come definito di seguito) al corrispondente voce, nell'ordine, nell'elenco chiamate dell'altro.

Le regole seguenti regolano l'uguaglianza delle voci dell'elenco chiamate:

*  Se due voci dell'elenco chiamate si riferiscono entrambi allo stesso metodo statico, le voci sono uguali.
*  Se due voci dell'elenco chiamate fanno riferimento allo stesso metodo non statico sullo stesso oggetto di destinazione (come definito dagli operatori di uguaglianza dei riferimenti), le voci sono uguali.
*  Le voci dell'elenco chiamate generate dalla valutazione di *anonymous_method_expression*o *lambda_expression*di semanticamente identiche con lo stesso set (possibilmente vuoto) di istanze di variabili esterne acquisite sono consentite (ma non necessarie) come uguale.

### <a name="equality-operators-and-null"></a>Operatori di uguaglianza e null

Gli operatori `==` e `!=` consentono a un operando di essere un valore di un tipo nullable e l'altro come valore letterale `null`, anche se per l'operazione non è presente alcun operatore predefinito o definito dall'utente (in formato non elevato o in formato Lift).

Per un'operazione di uno dei form
```csharp
x == null
null == x
x != null
null != x
```
dove `x` è un'espressione di un tipo nullable, se la risoluzione dell'overload dell'operatore ([risoluzione dell'overload dell'operatore binario](expressions.md#binary-operator-overload-resolution)) non riesce a trovare un operatore applicabile, il risultato viene invece calcolato dalla proprietà `HasValue` di `x`. In particolare, le prime due forme vengono convertite in `!x.HasValue` e le ultime due forme vengono convertite in `x.HasValue`.

### <a name="the-is-operator"></a>Operatore is

L'operatore `is` viene utilizzato per controllare dinamicamente se il tipo di runtime di un oggetto è compatibile con un tipo specificato. Il risultato dell'operazione `E is T`, dove `E` è un'espressione e `T` è un tipo, è un valore booleano che indica se `E` può essere convertito correttamente nel tipo `T` mediante una conversione di riferimento, una conversione boxing o una conversione unboxing. L'operazione viene valutata come segue, dopo che gli argomenti di tipo sono stati sostituiti per tutti i parametri di tipo:

*  Se `E` è una funzione anonima, si verifica un errore in fase di compilazione
*  Se `E` è un gruppo di metodi o il valore letterale `null`, se il tipo di `E` è un tipo di riferimento o un tipo nullable e il valore di `E` è null, il risultato è false.
*  In caso contrario, lasciare che `D` rappresenti il tipo dinamico di `E`, come indicato di seguito:
   * Se il tipo di `E` è un tipo di riferimento, `D` è il tipo in fase di esecuzione del riferimento all'istanza da `E`.
   * Se il tipo di `E` è un tipo nullable, `D` è il tipo sottostante del tipo Nullable.
   * Se il tipo di `E` è un tipo di valore non nullable, `D` è il tipo di `E`.
*  Il risultato dell'operazione dipende da `D` e `T`, come indicato di seguito:
   * Se `T` è un tipo di riferimento, il risultato è true se `D` e `T` sono dello stesso tipo, se `D` è un tipo di riferimento e la conversione implicita di un riferimento da `D` a `T` esiste o se `D` è un tipo valore e una conversione boxing da `D` per `T` esiste.
   * Se `T` è un tipo nullable, il risultato è true se `D` è il tipo sottostante di `T`.
   * Se `T` è un tipo di valore non nullable, il risultato è true se `D` e `T` sono dello stesso tipo.
   * In caso contrario, il risultato è false.

Si noti che le conversioni definite dall'utente non vengono considerate dall'operatore `is`.

### <a name="the-as-operator"></a>Operatore As

L'operatore `as` viene usato per convertire in modo esplicito un valore in un tipo di riferimento o un tipo nullable specificato. A differenza di un'espressione cast ([espressioni cast](expressions.md#cast-expressions)), l'operatore `as` non genera mai un'eccezione. Se invece la conversione indicata non è possibile, il valore risultante è `null`.

In un'operazione nel formato `E as T`, `E` deve essere un'espressione e `T` deve essere un tipo di riferimento, un parametro di tipo noto come tipo di riferimento o un tipo Nullable. Inoltre, almeno uno dei seguenti elementi deve essere true o in caso contrario si verifica un errore in fase di compilazione:

*  Identità ([conversione di identità](conversions.md#identity-conversion)), Nullable implicito ([conversioni implicite Nullable](conversions.md#implicit-nullable-conversions)), riferimento implicito ([conversioni implicite dei riferimenti](conversions.md#implicit-reference-conversions)), conversione boxing ([conversioni boxing](conversions.md#boxing-conversions)), Nullable esplicito ([Nullable esplicito). le conversioni](conversions.md#explicit-nullable-conversions)), il riferimento esplicito (conversioni[esplicite dei riferimenti](conversions.md#explicit-reference-conversions)) o la conversione unboxing (conversione[unboxing](conversions.md#unboxing-conversions)) esiste da `E` a `T`.
*  Il tipo di `E` o `T` è un tipo aperto.
*  `E` è il valore letterale `null`.

Se il tipo in fase di compilazione di `E` non è `dynamic`, l'operazione `E as T` produce lo stesso risultato di
```csharp
E is T ? (T)(E) : (T)null
```
con la differenza che `E` viene valutato una sola volta. Si può prevedere che il compilatore ottimizzi `E as T` per eseguire al massimo un controllo del tipo dinamico, anziché i due controlli dei tipi dinamici implicati dall'espansione precedente.

Se il tipo in fase di compilazione di `E` è `dynamic`, a differenza dell'operatore cast, l'operatore `as` non è associato in modo dinamico ([associazione dinamica](expressions.md#dynamic-binding)). Quindi, in questo caso, l'espansione è:
```csharp
E is T ? (T)(object)(E) : (T)null
```

Si noti che alcune conversioni, ad esempio le conversioni definite dall'utente, non sono possibili con l'operatore `as` e devono invece essere eseguite mediante espressioni cast.

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
il parametro di tipo `T` di `G` è noto come tipo di riferimento, perché contiene il vincolo di classe. Il parametro di tipo `U` di `H` non è tuttavia; l'uso dell'operatore `as` in `H`, quindi, non è consentito.

## <a name="logical-operators"></a>Operatori logici

Gli operatori `&`, `^` e `|` sono detti operatori logici.

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

Se un operando di un operatore logico dispone del tipo in fase di compilazione `dynamic`, l'espressione viene associata dinamicamente ([associazione dinamica](expressions.md#dynamic-binding)). In questo caso il tipo in fase di compilazione dell'espressione è `dynamic` e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di runtime degli operandi che hanno il tipo in fase di compilazione `dynamic`.

Per un'operazione nel formato `x op y`, dove `op` è uno degli operatori logici, viene applicata la risoluzione dell'overload ([risoluzione dell'overload degli operatori binari](expressions.md#binary-operator-overload-resolution)) per selezionare l'implementazione di un operatore specifico. Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.

Gli operatori logici predefiniti sono descritti nelle sezioni seguenti.

### <a name="integer-logical-operators"></a>Operatori logici integer

Gli operatori logici integer predefiniti sono:
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

L'operatore `&` calcola l'@no__t logico bit per bit dei due operandi, l'operatore `|` calcola il @no__t logico bit per bit dei due operandi e l'operatore `^` calcola l'@no__t esclusivo logico bit per bit dei due operandi. Non sono possibili overflow da queste operazioni.

### <a name="enumeration-logical-operators"></a>Operatori logici di enumerazione

Ogni tipo di enumerazione `E` fornisce in modo implicito gli operatori logici predefiniti seguenti:

```csharp
E operator &(E x, E y);
E operator |(E x, E y);
E operator ^(E x, E y);
```

Il risultato della valutazione di `x op y`, in cui `x` e `y` sono espressioni di un tipo di enumerazione `E` con un tipo sottostante `U` e `op` è uno degli operatori logici, è esattamente uguale alla valutazione di `(E)((U)x op (U)y)`. In altre parole, gli operatori logici di tipo enumerazione eseguono semplicemente l'operazione logica sul tipo sottostante dei due operandi.

### <a name="boolean-logical-operators"></a>Operatori logici booleani

Gli operatori logici booleani predefiniti sono:
```csharp
bool operator &(bool x, bool y);
bool operator |(bool x, bool y);
bool operator ^(bool x, bool y);
```

Il risultato di `x & y` è `true` se `x` e `y` sono `true`. In caso contrario, il risultato è `false`.

Il risultato di `x | y` è `true` se `x` o `y` è `true`. In caso contrario, il risultato è `false`.

Il risultato di `x ^ y` è `true` se `x` è `true` e `y` è `false` o `x` è `false` e `y` è `true`. In caso contrario, il risultato è `false`. Quando gli operandi sono di tipo `bool`, l'operatore `^` calcola lo stesso risultato dell'operatore `!=`.

### <a name="nullable-boolean-logical-operators"></a>Operatori logici booleani nullable

Il tipo booleano nullable `bool?` può rappresentare tre valori, `true`, `false` e `null`, ed è concettualmente simile al tipo a tre valori usato per le espressioni booleane in SQL. Per garantire che i risultati prodotti dagli operatori `&` e `|` per gli operandi `bool?` siano coerenti con la logica a tre valori di SQL, vengono forniti gli operatori predefiniti seguenti:

```csharp
bool? operator &(bool? x, bool? y);
bool? operator |(bool? x, bool? y);
```

Nella tabella seguente sono elencati i risultati ottenuti da questi operatori per tutte le combinazioni dei valori `true`, `false` e `null`.

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

Gli operatori `&&` e `||` sono detti operatori logici condizionali. Sono detti anche operatori logici di "corto circuito".

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

Gli operatori `&&` e `||` sono versioni condizionali degli operatori `&` e `|`:

*  L'operazione `x && y` corrisponde all'operazione `x & y`, ad eccezione del fatto che `y` viene valutato solo se `x` non è `false`.
*  L'operazione `x || y` corrisponde all'operazione `x | y`, ad eccezione del fatto che `y` viene valutato solo se `x` non è `true`.

Se un operando di un operatore logico condizionale ha il tipo in fase di compilazione `dynamic`, l'espressione viene associata dinamicamente ([associazione dinamica](expressions.md#dynamic-binding)). In questo caso il tipo in fase di compilazione dell'espressione è `dynamic` e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di runtime degli operandi che hanno il tipo in fase di compilazione `dynamic`.

Un'operazione nel formato `x && y` o `x || y` viene elaborata applicando la risoluzione dell'overload ([risoluzione dell'overload dell'operatore binario](expressions.md#binary-operator-overload-resolution)) come se l'operazione fosse stata scritta `x & y` o `x | y`. Quindi

*  Se la risoluzione dell'overload non riesce a trovare un singolo operatore migliore o se la risoluzione dell'overload seleziona uno degli operatori logici integer predefiniti, si verificherà un errore in fase di binding.
*  In caso contrario, se l'operatore selezionato è uno degli operatori logici booleani predefiniti ([operatori logici booleani](expressions.md#boolean-logical-operators)) o gli operatori logici booleani nullable ([operatori logici booleani nullable](expressions.md#nullable-boolean-logical-operators)), l'operazione viene elaborata come descritto in [ Operatori logici condizionali booleani](expressions.md#boolean-conditional-logical-operators).
*  In caso contrario, l'operatore selezionato è un operatore definito dall'utente e l'operazione viene elaborata come descritto in [operatori logici condizionali definiti dall'utente](expressions.md#user-defined-conditional-logical-operators).

Non è possibile eseguire direttamente l'overload degli operatori logici condizionali. Tuttavia, poiché gli operatori logici condizionali vengono valutati in termini di operatori logici normali, gli overload degli operatori logici normali sono, con alcune restrizioni, considerati anche gli overload degli operatori logici condizionali. Questa operazione viene descritta in modo più approfondito negli [operatori logici condizionali definiti dall'utente](expressions.md#user-defined-conditional-logical-operators).

### <a name="boolean-conditional-logical-operators"></a>Operatori logici condizionali booleani

Quando gli operandi di `&&` o `||` sono di tipo `bool` o quando gli operandi sono di tipi che non definiscono un `operator &` o `operator |` applicabile, ma definiscono le conversioni implicite in `bool`, l'operazione viene elaborata come indicato di seguito. :

*  L'operazione `x && y` viene valutata come `x ? y : false`. In altre parole, `x` viene prima valutato e convertito nel tipo `bool`. Quindi, se `x` è `true`, `y` viene valutato e convertito nel tipo `bool` e questo diventa il risultato dell'operazione. In caso contrario, il risultato dell'operazione è `false`.
*  L'operazione `x || y` viene valutata come `x ? true : y`. In altre parole, `x` viene prima valutato e convertito nel tipo `bool`. Quindi, se `x` è `true`, il risultato dell'operazione è `true`. In caso contrario, `y` viene valutato e convertito nel tipo `bool` e questo diventa il risultato dell'operazione.

### <a name="user-defined-conditional-logical-operators"></a>Operatori logici condizionali definiti dall'utente

Quando gli operandi di `&&` o `||` sono di tipo che dichiarano un valore definito dall'utente `operator &` o `operator |`, devono essere soddisfatte entrambe le condizioni seguenti, dove `T` è il tipo in cui viene dichiarato l'operatore selezionato:

*  Il tipo restituito e il tipo di ogni parametro dell'operatore selezionato devono essere `T`. In altre parole, l'operatore deve calcolare la logica `AND` o la @no__t logica-1 di due operandi di tipo `T` e deve restituire un risultato di tipo `T`.
*  `T` deve contenere dichiarazioni di `operator true` e `operator false`.

Si verifica un errore in fase di binding se uno di questi requisiti non è soddisfatto. In caso contrario, l'operazione `&&` o `||` viene valutata combinando il `operator true` o `operator false` definito dall'utente con l'operatore definito dall'utente selezionato:

*  L'operazione `x && y` viene valutata come `T.false(x) ? x : T.&(x, y)`, dove `T.false(x)` è una chiamata della `operator false` dichiarata in `T` e `T.&(x, y)` è una chiamata del `operator &` selezionato. In altre parole, `x` viene prima valutato e `operator false` viene richiamato sul risultato per determinare se `x` è decisamente false. Quindi, se `x` è decisamente false, il risultato dell'operazione è il valore calcolato in precedenza per `x`. In caso contrario, viene valutato `y` e il `operator &` selezionato viene richiamato sul valore calcolato in precedenza per `x` e il valore calcolato per `y` per produrre il risultato dell'operazione.
*  L'operazione `x || y` viene valutata come `T.true(x) ? x : T.|(x, y)`, dove `T.true(x)` è una chiamata della `operator true` dichiarata in `T` e `T.|(x,y)` è una chiamata del `operator|` selezionato. In altre parole, `x` viene prima valutato e `operator true` viene richiamato sul risultato per determinare se `x` è sicuramente true. Quindi, se `x` è sicuramente true, il risultato dell'operazione è il valore calcolato in precedenza per `x`. In caso contrario, viene valutato `y` e il `operator |` selezionato viene richiamato sul valore calcolato in precedenza per `x` e il valore calcolato per `y` per produrre il risultato dell'operazione.

In una di queste operazioni, l'espressione fornita da `x` viene valutata una sola volta e l'espressione fornita da `y` non viene valutata o valutata esattamente una volta.

Per un esempio di un tipo che implementa `operator true` e `operator false`, vedere [database Boolean type](structs.md#database-boolean-type).

## <a name="the-null-coalescing-operator"></a>Operatore di Unione null

L'operatore `??` è denominato operatore di Unione null.

```antlr
null_coalescing_expression
    : conditional_or_expression
    | conditional_or_expression '??' null_coalescing_expression
    ;
```

Un'espressione di Unione null nel formato `a ?? b` richiede che `a` sia di tipo nullable o Reference. Se `a` è diverso da null, il risultato di `a ?? b` è `a`; in caso contrario, il risultato è `b`. L'operazione valuta `b` solo se `a` è null.

L'operatore di Unione null è associato a destra, ovvero le operazioni sono raggruppate da destra a sinistra. Ad esempio, un'espressione nel formato `a ?? b ?? c` viene valutata come `a ?? (b ?? c)`. In termini generali, un'espressione nel formato `E1 ?? E2 ?? ... ?? En` restituisce il primo degli operandi che è non null o null se tutti gli operandi sono null.

Il tipo di espressione `a ?? b` dipende dalle conversioni implicite disponibili negli operandi. In ordine di preferenza, il tipo di `a ?? b` è `A0`, `A` o `B`, dove `A` è il tipo di `a` (purché `a` abbia un tipo), `B` è il tipo di `b` (purché `b` abbia un tipo) e 0 è il tipo sottostante di 1 se 2 è un tipo Nullable oppure 3 in caso contrario. In particolare, `a ?? b` viene elaborato come segue:

*  Se `A` esiste e non è un tipo nullable o un tipo riferimento, si verifica un errore in fase di compilazione.
*  Se `b` è un'espressione dinamica, il tipo di risultato sarà `dynamic`. In fase di esecuzione, `a` viene prima valutato. Se `a` non è null, `a` viene convertito in Dynamic e questo diventa il risultato. In caso contrario, viene valutato `b`, che diventa il risultato.
*  In caso contrario, se `A` esiste ed è un tipo Nullable ed esiste una conversione implicita da `b` a `A0`, il tipo di risultato sarà `A0`. In fase di esecuzione, `a` viene prima valutato. Se `a` non è null, viene annullato il wrapper `a` con il tipo `A0`, che diventa il risultato. In caso contrario, `b` viene valutato e convertito nel tipo `A0` e questo diventa il risultato.
*  In caso contrario, se `A` esiste ed esiste una conversione implicita da `b` a `A`, il tipo di risultato sarà `A`. In fase di esecuzione, `a` viene prima valutato. Se `a` non è null, `a` diventa il risultato. In caso contrario, `b` viene valutato e convertito nel tipo `A` e questo diventa il risultato.
*  In caso contrario, se `b` ha un tipo `B` ed esiste una conversione implicita da `a` a `B`, il tipo di risultato sarà `B`. In fase di esecuzione, `a` viene prima valutato. Se `a` non è null, viene annullato il incapsulamento di `a` nel tipo `A0` (se `A` esiste e ammette valori null) e viene convertito nel tipo `B`, che diventa il risultato. In caso contrario, `b` viene valutato e diventa il risultato.
*  In caso contrario, `a` e `b` sono incompatibili e si verifica un errore in fase di compilazione.

## <a name="conditional-operator"></a>Operatore condizionale

L'operatore `?:` è denominato operatore condizionale. Viene anche chiamato operatore ternario.

```antlr
conditional_expression
    : null_coalescing_expression
    | null_coalescing_expression '?' expression ':' expression
    ;
```

Un'espressione condizionale nel formato `b ? x : y` valuta prima di tutto la condizione `b`. Quindi, se `b` è `true`, `x` viene valutato e diventa il risultato dell'operazione. In caso contrario, `y` viene valutato e diventa il risultato dell'operazione. Un'espressione condizionale non valuta mai sia `x` che `y`.

L'operatore condizionale è associato a destra, ovvero le operazioni sono raggruppate da destra a sinistra. Ad esempio, un'espressione nel formato `a ? b : c ? d : e` viene valutata come `a ? b : (c ? d : e)`.

Il primo operando dell'operatore `?:` deve essere un'espressione che può essere convertita in modo implicito in `bool` o un'espressione di un tipo che implementa `operator true`. Se nessuno di questi requisiti viene soddisfatto, si verifica un errore in fase di compilazione.

Il secondo e il terzo operando, `x` e `y`, dell'operatore `?:` controllano il tipo di espressione condizionale.

*  Se `x` è di tipo `X` e `y` è di tipo `Y`,
   * Se esiste una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) da `X` a `Y`, ma non da `Y` a `X`, `Y` è il tipo dell'espressione condizionale.
   * Se esiste una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) da `Y` a `X`, ma non da `X` a `Y`, `X` è il tipo dell'espressione condizionale.
   * In caso contrario, non è possibile determinare alcun tipo di espressione e si verifica un errore in fase di compilazione.
*  Se solo uno dei `x` e `y` ha un tipo e sia `x` che `y`, di sono convertibili in modo implicito in tale tipo, ovvero il tipo dell'espressione condizionale.
*  In caso contrario, non è possibile determinare alcun tipo di espressione e si verifica un errore in fase di compilazione.

L'elaborazione in fase di esecuzione di un'espressione condizionale nel formato `b ? x : y` è costituita dai passaggi seguenti:

*  Prima viene valutato `b` e viene determinato il valore `bool` di `b`:
   * Se esiste una conversione implicita dal tipo di `b` al `bool`, viene eseguita la conversione implicita per produrre un valore `bool`.
   * In caso contrario, il `operator true` definito dal tipo di `b` viene richiamato per produrre un valore `bool`.
*  Se il valore `bool` prodotto dal passaggio precedente è `true`, `x` viene valutato e convertito nel tipo dell'espressione condizionale e diventa il risultato dell'espressione condizionale.
*  In caso contrario, `y` viene valutato e convertito nel tipo dell'espressione condizionale, che diventa il risultato dell'espressione condizionale.

## <a name="anonymous-function-expressions"></a>Espressioni di funzioni anonime

Una ***funzione anonima*** è un'espressione che rappresenta una definizione di metodo "inline". Una funzione anonima non dispone di un valore o di un tipo in e di se stesso, ma è convertibile in un tipo di albero delle espressioni o di delegato compatibile. La valutazione di una conversione di funzione anonima dipende dal tipo di destinazione della conversione: Se è un tipo delegato, la conversione restituisce un valore delegato che fa riferimento al metodo definito dalla funzione anonima. Se è un tipo di albero delle espressioni, la conversione restituisce un albero delle espressioni che rappresenta la struttura del metodo come struttura dell'oggetto.

Per motivi cronologici sono disponibili due versioni sintattiche di funzioni anonime, ovvero *lambda_expression*s e *anonymous_method_expression*s. Per quasi tutti gli scopi, *lambda_expression*s sono più concisi ed espressivi di *anonymous_method_expression*s, che rimangono nella lingua per la compatibilità con le versioni precedenti.

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

L'operatore `=>` ha la stessa precedenza dell'assegnazione (`=`) ed è associativo a destra.

Una funzione anonima con il modificatore `async` è una funzione asincrona e segue le regole descritte negli [iteratori](classes.md#iterators).

I parametri di una funzione anonima sotto forma di *lambda_expression* possono essere tipizzati in modo esplicito o implicito. In un elenco di parametri tipizzato in modo esplicito, il tipo di ogni parametro viene dichiarato in modo esplicito. In un elenco di parametri tipizzato in modo implicito, i tipi di parametri vengono dedotti dal contesto in cui si verifica la funzione anonima, in particolare quando la funzione anonima viene convertita in un tipo di delegato compatibile o in un tipo di albero delle espressioni, il tipo fornisce tipi di parametro ([conversioni di funzioni anonime](conversions.md#anonymous-function-conversions)).

In una funzione anonima con un singolo parametro tipizzato in modo implicito, le parentesi possono essere omesse dall'elenco di parametri. In altre parole, una funzione anonima del form
```csharp
( param ) => expr
```
può essere abbreviato in
```csharp
param => expr
```

L'elenco di parametri di una funzione anonima sotto forma di *anonymous_method_expression* è facoltativo. Se specificato, i parametri devono essere tipizzati in modo esplicito. In caso contrario, la funzione anonima può essere convertita in un delegato con un elenco di parametri che non contiene parametri `out`.

Il corpo di un *blocco* di una funzione anonima è raggiungibile ([punti finali e raggiungibilità](statements.md#end-points-and-reachability)), a meno che la funzione anonima non venga eseguita all'interno di un'istruzione non raggiungibile.

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

Il comportamento di *lambda_expression*s e *anonymous_method_expression*s è identico, ad eccezione dei punti seguenti:

*  *anonymous_method_expression*s consentono di omettere completamente l'elenco di parametri, cedendo la convertibilità ai tipi delegati di qualsiasi elenco di parametri di valore.
*  i *lambda_expression*s consentono l'omissione e l'inferenza di tipi di parametro, mentre *anonymous_method_expression*richiede che i tipi di parametro siano dichiarati in modo esplicito.
*  Il corpo di un *lambda_expression* può essere un'espressione o un blocco di istruzioni, mentre il corpo di un *anonymous_method_expression* deve essere un blocco di istruzioni.
*  Solo *lambda_expression*s hanno conversioni ai tipi di albero delle espressioni compatibili ([tipi di albero delle espressioni](types.md#expression-tree-types)).

### <a name="anonymous-function-signatures"></a>Firme di funzioni anonime

Il *anonymous_function_signature* facoltativo di una funzione anonima definisce i nomi e, facoltativamente, i tipi di parametri formali per la funzione anonima. L'ambito dei parametri della funzione anonima è *anonymous_function_body*. ([Ambiti](basic-concepts.md#scopes)) Insieme all'elenco dei parametri (se specificato), il corpo del metodo anonimo costituisce uno spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)). Si tratta quindi di un errore in fase di compilazione per il nome di un parametro della funzione anonima in modo che corrisponda al nome di una variabile locale, di una costante o di un parametro locale il cui ambito includa *anonymous_method_expression* o *lambda_expression*.

Se una funzione anonima ha un *explicit_anonymous_function_signature*, il set di tipi delegati e tipi di albero delle espressioni compatibili è limitato a quelli che hanno gli stessi tipi di parametro e modificatori nello stesso ordine. Diversamente dalle conversioni di gruppi di metodi ([conversioni di gruppi di metodi](conversions.md#method-group-conversions)), la controvarianza dei tipi di parametro della funzione anonima non è supportata. Se una funzione anonima non dispone di un *anonymous_function_signature*, il set di tipi delegati e tipi di albero delle espressioni compatibili è limitato a quelli che non hanno parametri `out`.

Si noti che un *anonymous_function_signature* non può includere attributi o una matrice di parametri. Tuttavia, un *anonymous_function_signature* può essere compatibile con un tipo delegato il cui elenco di parametri contiene una matrice di parametri.

Si noti inoltre che la conversione in un tipo di albero delle espressioni, anche se compatibile, può ancora avere esito negativo in fase di compilazione ([tipi di albero delle espressioni](types.md#expression-tree-types)).

### <a name="anonymous-function-bodies"></a>Corpi di funzioni anonime

Il corpo (*espressione* o *blocco*) di una funzione anonima è soggetto alle regole seguenti:

*  Se la funzione anonima include una firma, i parametri specificati nella firma sono disponibili nel corpo. Se la funzione anonima non ha alcuna firma, può essere convertita in un tipo di delegato o di espressione con parametri ([conversioni di funzioni anonime](conversions.md#anonymous-function-conversions)), ma non è possibile accedere ai parametri nel corpo.
*  Ad eccezione dei parametri `ref` o `out` specificati nella firma (se presente) della funzione anonima di inclusione più vicina, si tratta di un errore in fase di compilazione per il corpo per accedere a un parametro `ref` o `out`.
*  Quando il tipo di `this` è un tipo struct, si tratta di un errore in fase di compilazione per il corpo per accedere `this`. Si tratta di un valore che indica se l'accesso è esplicito (come in `this.x`) o implicito (come in `x`, dove `x` è un membro di istanza dello struct). Questa regola impedisce semplicemente tale accesso e non influisce sull'eventuale risultato della ricerca dei membri in un membro dello struct.
*  Il corpo ha accesso alle variabili esterne ([variabili esterne](expressions.md#outer-variables)) della funzione anonima. L'accesso a una variabile esterna fa riferimento all'istanza della variabile attiva nel momento in cui viene valutato il valore di *lambda_expression* o *anonymous_method_expression* ([valutazione di espressioni di funzione anonime](expressions.md#evaluation-of-anonymous-function-expressions)).
*  Si tratta di un errore in fase di compilazione per il corpo che contiene un'istruzione `goto`, un'istruzione `break` o un'istruzione `continue` la cui destinazione è esterna al corpo o all'interno del corpo di una funzione anonima contenuta.
*  Un'istruzione `return` nel corpo restituisce il controllo da una chiamata della funzione anonima di inclusione più vicina, non dal membro della funzione contenitore. Un'espressione specificata in un'istruzione `return` deve essere convertibile in modo implicito nel tipo restituito del tipo delegato o dell'albero delle espressioni in cui viene convertito il *lambda_expression* o *anonymous_method_expression* di inclusione più vicino ( [Conversioni di funzioni anonime](conversions.md#anonymous-function-conversions)).

Non è specificato in modo esplicito se esiste un modo per eseguire il blocco di una funzione anonima diversa dalla valutazione e dalla chiamata di *lambda_expression* o *anonymous_method_expression*. In particolare, il compilatore può scegliere di implementare una funzione anonima sintetizzando uno o più metodi o tipi denominati. I nomi di tali elementi sintetizzati devono essere di un modulo riservato per l'utilizzo da parte del compilatore.

### <a name="overload-resolution-and-anonymous-functions"></a>Risoluzione dell'overload e funzioni anonime

Le funzioni anonime in un elenco di argomenti partecipano all'inferenza del tipo e alla risoluzione dell'overload. Per le regole esatte, vedere [inferenza del tipo](expressions.md#type-inference) e [risoluzione dell'overload](expressions.md#overload-resolution) .

Nell'esempio seguente viene illustrato l'effetto delle funzioni anonime sulla risoluzione dell'overload.

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

La classe `ItemList<T>` ha due metodi `Sum`. Ogni accetta un argomento `selector`, che estrae il valore da sommare da un elemento di elenco. Il valore estratto può essere un `int` o un `double` e la somma risultante è analoga a un `int` o a un `double`.

I metodi `Sum` possono ad esempio essere utilizzati per calcolare somme da un elenco di righe di dettaglio in un ordine.

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

Nella prima chiamata di `orderDetails.Sum`, entrambi i metodi `Sum` sono applicabili perché la funzione anonima `d => d. UnitCount` è compatibile con `Func<Detail,int>` e `Func<Detail,double>`. Tuttavia, la risoluzione dell'overload preleva il primo metodo `Sum` perché la conversione in `Func<Detail,int>` è migliore rispetto alla conversione in `Func<Detail,double>`.

Nella seconda chiamata di `orderDetails.Sum`, è applicabile solo il secondo metodo `Sum` perché la funzione anonima `d => d.UnitPrice * d.UnitCount` produce un valore di tipo `double`. Pertanto, la risoluzione dell'overload sceglie il secondo metodo `Sum` per la chiamata.

### <a name="anonymous-functions-and-dynamic-binding"></a>Funzioni anonime e associazione dinamica

Una funzione anonima non può essere un destinatario, un argomento o un operando di un'operazione associata dinamicamente.

### <a name="outer-variables"></a>Variabili esterne

Qualsiasi variabile locale, parametro di valore o matrice di parametri il cui ambito include *lambda_expression* o *anonymous_method_expression* è detta ***variabile esterna*** della funzione anonima. In un membro della funzione di istanza di una classe, il valore `this` viene considerato un parametro di valore ed è una variabile esterna di qualsiasi funzione anonima contenuta nel membro della funzione.

#### <a name="captured-outer-variables"></a>Variabili esterne acquisite

Quando una funzione anonima fa riferimento a una variabile esterna, viene detto che la variabile esterna è stata ***acquisita*** dalla funzione anonima. In genere, la durata di una variabile locale è limitata all'esecuzione del blocco o dell'istruzione a cui è associata ([variabili locali](variables.md#local-variables)). Tuttavia, la durata di una variabile esterna acquisita viene estesa almeno finché il delegato o l'albero delle espressioni creato dalla funzione anonima non diventa idoneo per Garbage Collection.

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
la variabile locale `x` viene acquisita dalla funzione anonima e la durata di `x` viene estesa almeno finché il delegato restituito da `F` diventa idoneo per Garbage Collection (che non si verifica fino alla fine del programma). Poiché ogni chiamata della funzione Anonymous opera sulla stessa istanza di `x`, l'output dell'esempio è:
```console
1
2
3
```

Quando una variabile locale o un parametro di valore viene acquisito da una funzione anonima, la variabile o il parametro locale non è più considerato una variabile fissa ([variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)), ma è invece considerata una variabile mobile. Pertanto, qualsiasi codice @no__t 0 che accetta l'indirizzo di una variabile esterna acquisita deve innanzitutto utilizzare l'istruzione `fixed` per correggere la variabile.

Si noti che a differenza di una variabile non acquisita, una variabile locale acquisita può essere esposta simultaneamente a più thread di esecuzione.

#### <a name="instantiation-of-local-variables"></a>Creazione di istanze di variabili locali

Una variabile locale viene considerata come ***un'istanza*** quando l'esecuzione entra nell'ambito della variabile. Ad esempio, quando viene richiamato il metodo seguente, viene creata un'istanza della variabile locale `x` e viene inizializzata tre volte, una volta per ogni iterazione del ciclo.

```csharp
static void F() {
    for (int i = 0; i < 3; i++) {
        int x = i * 2 + 1;
        ...
    }
}
```

Tuttavia, lo stato di trasferimento della dichiarazione di `x` all'esterno del ciclo comporta una singola creazione di istanza di `x`:
```csharp
static void F() {
    int x;
    for (int i = 0; i < 3; i++) {
        x = i * 2 + 1;
        ...
    }
}
```

Quando non viene acquisita, non è possibile osservare esattamente la frequenza con cui viene creata un'istanza della variabile locale, perché la durata delle creazioni di istanze è disgiunta. è possibile che ogni creazione di istanza usi semplicemente lo stesso percorso di archiviazione. Tuttavia, quando una funzione anonima acquisisce una variabile locale, gli effetti della creazione di un'istanza diventano evidenti.

Esempio
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
```console
1
3
5
```

Tuttavia, quando la dichiarazione di `x` viene spostata all'esterno del ciclo:
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
```console
5
5
5
```

Se un ciclo for dichiara una variabile di iterazione, la variabile stessa viene considerata dichiarata all'esterno del ciclo. Quindi, se l'esempio viene modificato per acquisire la variabile di iterazione stessa:

```csharp
static D[] F() {
    D[] result = new D[3];
    for (int i = 0; i < 3; i++) {
        result[i] = () => { Console.WriteLine(i); };
    }
    return result;
}
```
viene acquisita una sola istanza della variabile di iterazione che produce l'output:
```console
3
3
3
```

È possibile che i delegati di funzioni anonime possano condividere alcune variabili acquisite, ma hanno istanze separate di altri utenti. Ad esempio, se `F` viene modificato in
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
i tre delegati acquisiscono la stessa istanza di `x` ma istanze separate di `y` e l'output è:
```console
1 1
2 1
3 1
```

Funzioni anonime separate possono acquisire la stessa istanza di una variabile esterna. Nell’esempio
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
le due funzioni anonime acquisiscono la stessa istanza della variabile locale `x` e possono quindi "comunicare" tramite tale variabile. L'output dell'esempio è:
```console
5
10
```

### <a name="evaluation-of-anonymous-function-expressions"></a>Valutazione di espressioni di funzione anonime

Una funzione anonima `F` deve essere sempre convertita in un tipo delegato `D` o un tipo di albero delle espressioni `E`, direttamente o tramite l'esecuzione di un'espressione di creazione del delegato `new D(F)`. Questa conversione determina il risultato della funzione anonima, come descritto in [conversioni di funzioni anonime](conversions.md#anonymous-function-conversions).

## <a name="query-expressions"></a>Espressioni di query

Le ***espressioni di query*** forniscono una sintassi integrata del linguaggio per le query simili ai linguaggi di query relazionali e gerarchici, ad esempio SQL e XQuery.

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

Un'espressione di query inizia con una clausola `from` e termina con una clausola `select` o `group`. La clausola `from` iniziale può essere seguita da zero o più clausole `from`, `let`, `where`, `join` o `orderby`. Ogni clausola `from` è un generatore che introduce una ***variabile di intervallo*** che spazia sugli elementi di una ***sequenza***. Ogni clausola `let` introduce una variabile di intervallo che rappresenta un valore calcolato per mezzo di variabili di intervallo precedenti. Ogni clausola `where` è un filtro che esclude gli elementi dal risultato. Ogni clausola `join` Confronta le chiavi specificate della sequenza di origine con le chiavi di un'altra sequenza, ottenendo coppie corrispondenti. Ogni clausola `orderby` Riordina gli elementi in base ai criteri specificati. La clausola finale `select` o `group` specifica la forma del risultato in termini di variabili di intervallo. Infine, è possibile usare una clausola `into` per eseguire il "splicing" delle query trattando i risultati di una query come un generatore in una query successiva.

### <a name="ambiguities-in-query-expressions"></a>Ambiguità nelle espressioni di query

Le espressioni di query contengono un numero di "parole chiave contestuali", ovvero identificatori che hanno un significato speciale in un determinato contesto. In particolare, si tratta di `from`, `where`, `join`, `on`, `equals`, `into`, `let`, `orderby`, `ascending`, `descending`, 0, 1 e 2. Per evitare ambiguità nelle espressioni di query causate dall'utilizzo misto di questi identificatori come parole chiave o nomi semplici, questi identificatori vengono considerati parole chiave quando si verificano in qualsiasi punto all'interno di un'espressione di query.

A tale scopo, un'espressione di query è qualsiasi espressione che inizia con "`from identifier`" seguito da qualsiasi token ad eccezione di "`;`", "`=`" o "`,`".

Per usare queste parole come identificatori all'interno di un'espressione di query, è possibile che siano preceduti da "`@`" ([identificatori](lexical-structure.md#identifiers)).

### <a name="query-expression-translation"></a>Conversione di espressioni di query

Il C# linguaggio non specifica la semantica di esecuzione delle espressioni di query. Piuttosto, le espressioni di query vengono convertite in chiamate di metodi che rispettano il *modello di espressione* di query ([modello di espressione di query](expressions.md#the-query-expression-pattern)). In particolare, le espressioni di query vengono convertite in chiamate di metodi denominati `Where`, `Select`, `SelectMany`, `Join`, `GroupJoin`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `GroupBy` e 0. Questi metodi dovrebbero avere firme e tipi di risultati specifici, come descritto nel [modello di espressione di query](expressions.md#the-query-expression-pattern). Questi metodi possono essere metodi di istanza dell'oggetto sottoposto a query o metodi di estensione esterni all'oggetto e implementano l'effettiva esecuzione della query.

La conversione dalle espressioni di query alle chiamate di metodo è un mapping sintattico che si verifica prima dell'esecuzione di qualsiasi associazione di tipo o risoluzione dell'overload. Si garantisce che la traduzione sia sintatticamente corretta, ma non è garantita la produzione di codice semanticamente corretto C# . In seguito alla conversione di espressioni di query, le chiamate al metodo risultanti vengono elaborate come chiamate al metodo normali e ciò può a sua volta rivelare errori, ad esempio se i metodi non esistono, se gli argomenti presentano tipi errati o se i metodi sono generici e l'inferenza del tipo non riesce.

Un'espressione di query viene elaborata applicando ripetutamente le seguenti traduzioni fino a quando non sono possibili ulteriori riduzioni. Le traduzioni sono elencate in ordine di applicazione: ogni sezione presuppone che le traduzioni nelle sezioni precedenti siano state eseguite in modo esaustivo e, una volta esaurita, una sezione non verrà rivisitata in un secondo momento nell'elaborazione della stessa espressione di query.

L'assegnazione a variabili di intervallo non è consentita nelle espressioni di query. Tuttavia, C# un'implementazione è autorizzata a non sempre applicare questa restrizione, perché questo potrebbe talvolta non essere possibile con lo schema di conversione sintattico presentato qui.

Alcune traduzioni inseriscono variabili di intervallo con identificatori trasparenti identificati da `*`. Le proprietà speciali degli identificatori trasparenti sono illustrate ulteriormente negli [identificatori trasparenti](expressions.md#transparent-identifiers).

#### <a name="select-and-groupby-clauses-with-continuations"></a>Clausole SELECT e GroupBy con continuazioni

Espressione di query con una continuazione
```csharp
from ... into x ...
```
viene convertito in
```csharp
from x in ( from ... ) ...
```

Le traduzioni nelle sezioni seguenti presuppongono che le query non dispongano di continuazioni `into`.

Esempio
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
la traduzione finale di
```csharp
customers.
GroupBy(c => c.Country).
Select(g => new { Country = g.Key, CustCount = g.Count() })
```

#### <a name="explicit-range-variable-types"></a>Tipi di variabili di intervallo esplicite

Clausola `from` che specifica in modo esplicito un tipo di variabile di intervallo
```csharp
from T x in e
```
viene convertito in
```csharp
from x in ( e ) . Cast < T > ( )
```

Clausola `join` che specifica in modo esplicito un tipo di variabile di intervallo
```csharp
join T x in e on k1 equals k2
```
viene convertito in
```csharp
join x in ( e ) . Cast < T > ( ) on k1 equals k2
```

Le traduzioni nelle sezioni seguenti presuppongono che le query non includano tipi di variabile di intervallo espliciti.

Esempio
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
la traduzione finale di
```csharp
customers.
Cast<Customer>().
Where(c => c.City == "London")
```

I tipi di variabile di intervallo espliciti sono utili per eseguire query su raccolte che implementano l'interfaccia non generica `IEnumerable`, ma non l'interfaccia generica `IEnumerable<T>`. Nell'esempio precedente, questo sarebbe il caso se `customers` fosse di tipo `ArrayList`.

#### <a name="degenerate-query-expressions"></a>Degenerare espressioni di query

Espressione di query nel formato
```csharp
from x in e select x
```
viene convertito in
```csharp
( e ) . Select ( x => x )
```

Esempio
```csharp
from c in customers
select c
```
viene convertito in
```csharp
customers.Select(c => c)
```

Un'espressione di query degenere consente di selezionare in modo banale gli elementi dell'origine. Una fase successiva della traduzione rimuove le query degenerate introdotte da altri passaggi di traduzione sostituendolo con l'origine. È tuttavia importante verificare che il risultato di un'espressione di query non sia mai l'oggetto di origine, in quanto ciò potrebbe rivelare il tipo e l'identità del codice sorgente al client della query. Questo passaggio consente pertanto di proteggere le query degenerate scritte direttamente nel codice sorgente chiamando in modo esplicito `Select` nell'origine. Per assicurarsi che questi metodi non restituiscano mai l'oggetto di origine, gli implementatori di `Select` e altri operatori di query.

#### <a name="from-let-where-join-and-orderby-clauses"></a>Clausole from, Let, where, join e OrderBy

Espressione di query con una seconda clausola `from` seguita da una clausola `select`
```csharp
from x1 in e1
from x2 in e2
select v
```
viene convertito in
```csharp
( e1 ) . SelectMany( x1 => e2 , ( x1 , x2 ) => v )
```

Un'espressione di query con una seconda clausola `from` seguita da un valore diverso da una clausola `select`:

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

Espressione di query con una clausola `let`
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

Espressione di query con una clausola `where`
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

Espressione di query con una clausola `join` senza `into` seguita da una clausola `select`
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2
select v
```
viene convertito in
```csharp
( e1 ) . Join( e2 , x1 => k1 , x2 => k2 , ( x1 , x2 ) => v )
```

Espressione di query con una clausola `join` senza `into` seguita da un valore diverso da una clausola `select`
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

Espressione di query con una clausola `join` con un `into` seguito da una clausola `select`
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2 into g
select v
```
viene convertito in
```csharp
( e1 ) . GroupJoin( e2 , x1 => k1 , x2 => k2 , ( x1 , g ) => v )
```

Un'espressione di query con una clausola `join` con un `into` seguito da un elemento diverso da una clausola `select`
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

Espressione di query con una clausola `orderby`
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

Se una clausola di ordinamento specifica un indicatore di direzione `descending`, viene invece prodotta una chiamata di `OrderByDescending` o `ThenByDescending`.

Nelle traduzioni seguenti si presuppone che non esistano clausole `let`, `where`, `join` o `orderby` e non più di una clausola `from` iniziale in ogni espressione di query.

Esempio
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

Esempio
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
la traduzione finale di
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.OrderID, x.o.Total })
```
dove `x` è un identificatore generato dal compilatore che altrimenti è invisibile e inaccessibile.

Esempio
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
la traduzione finale di
```csharp
orders.
Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) }).
Where(x => x.t >= 1000).
Select(x => new { x.o.OrderID, Total = x.t })
```
dove `x` è un identificatore generato dal compilatore che altrimenti è invisibile e inaccessibile.

Esempio
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

Esempio
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
la traduzione finale di
```csharp
customers.
GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
    (c, co) => new { c, co }).
Select(x => new { x, n = x.co.Count() }).
Where(y => y.n >= 10).
Select(y => new { y.x.c.Name, OrderCount = y.n)
```
dove `x` e `y` sono identificatori generati dal compilatore che altrimenti sono invisibili e inaccessibili.

Esempio
```csharp
from o in orders
orderby o.Customer.Name, o.Total descending
select o
```
ha la traduzione finale
```csharp
orders.
OrderBy(o => o.Customer.Name).
ThenByDescending(o => o.Total)
```

#### <a name="select-clauses"></a>Clausola SELECT

Espressione di query nel formato
```csharp
from x in e select v
```
viene convertito in
```csharp
( e ) . Select ( x => v )
```
eccetto quando v è l'identificatore x, la traduzione è semplicemente
```csharp
( e )
```

Esempio:
```csharp
from c in customers.Where(c => c.City == "London")
select c
```
viene semplicemente convertito in
```csharp
customers.Where(c => c.City == "London")
```

#### <a name="groupby-clauses"></a>Clausole GroupBy

Espressione di query nel formato
```csharp
from x in e group v by k
```
viene convertito in
```csharp
( e ) . GroupBy ( x => k , x => v )
```
eccetto quando v è l'identificatore x, la traduzione è
```csharp
( e ) . GroupBy ( x => k )
```

Esempio
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

Alcune traduzioni inseriscono variabili di intervallo con ***identificatori trasparenti identificati*** da `*`. Gli identificatori trasparenti non sono una funzionalità del linguaggio corretta. esistono solo come un passaggio intermedio nel processo di conversione delle espressioni di query.

Quando una conversione di query inserisce un identificatore trasparente, ulteriori passaggi di conversione propagano l'identificatore trasparente in funzioni anonime e inizializzatori di oggetti anonimi. In questi contesti gli identificatori trasparenti hanno il seguente comportamento:

*  Quando si verifica un identificatore trasparente come parametro in una funzione anonima, i membri del tipo anonimo associato vengono automaticamente inclusi nell'ambito nel corpo della funzione anonima.
*  Quando un membro con un identificatore transparent è nell'ambito, anche i membri di tale membro sono inclusi nell'ambito.
*  Quando un identificatore trasparente si verifica come un dichiaratore di membro in un inizializzatore di oggetto anonimo, introduce un membro con un identificatore Transparent.
*  Nei passaggi di traduzione descritti in precedenza, gli identificatori trasparenti vengono sempre introdotti insieme ai tipi anonimi, con lo scopo di acquisire più variabili di intervallo come membri di un singolo oggetto. Un'implementazione di C# è autorizzata a usare un meccanismo diverso rispetto ai tipi anonimi per raggruppare più variabili di intervallo. Negli esempi di traduzione seguenti si presuppone che vengano utilizzati i tipi anonimi e viene illustrato il modo in cui gli identificatori trasparenti possono essere convertiti.

Esempio
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

che viene ulteriormente tradotta in
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(* => o.Total).
Select(* => new { c.Name, o.Total })
```
che, quando gli identificatori trasparenti vengono cancellati, è equivalente a
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.Total })
```
dove `x` è un identificatore generato dal compilatore che altrimenti è invisibile e inaccessibile.

Esempio
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
che è ulteriormente ridotto a
```csharp
customers.
Join(orders, c => c.CustomerID, o => o.CustomerID, (c, o) => new { c, o }).
Join(details, * => o.OrderID, d => d.OrderID, (*, d) => new { *, d }).
Join(products, * => d.ProductID, p => p.ProductID, (*, p) => new { *, p }).
Select(* => new { c.Name, o.OrderDate, p.ProductName })
```
la traduzione finale di
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
dove `x`, `y` e `z` sono identificatori generati dal compilatore che altrimenti sono invisibili e inaccessibili.

### <a name="the-query-expression-pattern"></a>Modello di espressione di query

Il ***modello di espressione di query*** stabilisce un modello di metodi che i tipi possono implementare per supportare le espressioni di query. Poiché le espressioni di query vengono convertite in chiamate ai metodi mediante un mapping sintattico, i tipi hanno una notevole flessibilità nel modo in cui implementano il modello di espressione di query. Ad esempio, i metodi del modello possono essere implementati come metodi di istanza o come metodi di estensione perché i due hanno la stessa sintassi di chiamata e i metodi possono richiedere delegati o alberi delle espressioni perché le funzioni anonime sono convertibili in entrambi.

La forma consigliata di un tipo generico `C<T>` che supporta il modello di espressione di query è illustrata di seguito. Un tipo generico viene usato per illustrare le relazioni appropriate tra i tipi di parametro e di risultato, ma è possibile implementare il modello anche per i tipi non generici.

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

I metodi precedenti usano i tipi delegati generici `Func<T1,R>` e `Func<T1,T2,R>`, ma potrebbero avere anche usato altri tipi di albero delle espressioni o di delegato con le stesse relazioni nei tipi di parametro e di risultato.

Si noti la relazione consigliata tra `C<T>` e `O<T>` che garantisce che i metodi `ThenBy` e `ThenByDescending` siano disponibili solo sul risultato di un `OrderBy` o `OrderByDescending`. Si noti anche la forma consigliata del risultato di `GroupBy`, ovvero una sequenza di sequenze, in cui ogni sequenza interna ha una proprietà `Key` aggiuntiva.

Lo spazio dei nomi `System.Linq` fornisce un'implementazione del modello di operatore di query per qualsiasi tipo che implementa l'interfaccia `System.Collections.Generic.IEnumerable<T>`.

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

L'operando sinistro di un'assegnazione deve essere un'espressione classificata come variabile, un accesso a una proprietà, un accesso all'indicizzatore o un accesso a un evento.

L'operatore `=` è denominato ***operatore di assegnazione semplice***. Assegna il valore dell'operando destro alla variabile, alla proprietà o all'elemento dell'indicizzatore dato dall'operando sinistro. L'operando sinistro dell'operatore di assegnazione semplice non può essere un accesso a un evento (ad eccezione di quanto descritto in [eventi di tipo campo](classes.md#field-like-events)). L'operatore di assegnazione semplice viene descritto in [assegnazione semplice](expressions.md#simple-assignment).

Gli operatori di assegnazione diversi dall'operatore `=` vengono chiamati ***operatori di assegnazione composti***. Questi operatori eseguono l'operazione indicata sui due operandi, quindi assegnano il valore risultante alla variabile, alla proprietà o all'elemento dell'indicizzatore dato dall'operando sinistro. Gli operatori di assegnazione composti sono descritti in [assegnazione composta](expressions.md#compound-assignment).

Gli operatori `+=` e `-=` con un'espressione di accesso agli eventi come operando sinistro vengono chiamati *operatori di assegnazione degli eventi*. Nessun altro operatore di assegnazione è valido con un accesso a un evento come operando sinistro. Gli operatori di assegnazione eventi sono descritti in [assegnazione evento](expressions.md#event-assignment).

Gli operatori di assegnazione sono associativi a destra, ovvero le operazioni sono raggruppate da destra a sinistra. Ad esempio, un'espressione nel formato `a = b = c` viene valutata come `a = (b = c)`.

### <a name="simple-assignment"></a>Assegnazione singola

L'operatore `=` è denominato operatore di assegnazione semplice.

Se l'operando sinistro di un'assegnazione semplice è nel formato `E.P` o `E[Ei]` in cui `E` presenta il tipo in fase di compilazione `dynamic`, l'assegnazione è associata dinamicamente ([associazione dinamica](expressions.md#dynamic-binding)). In questo caso il tipo in fase di compilazione dell'espressione di assegnazione è `dynamic` e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione in base al tipo di runtime `E`.

In un'assegnazione semplice, l'operando destro deve essere un'espressione convertibile in modo implicito nel tipo dell'operando sinistro. L'operazione assegna il valore dell'operando destro alla variabile, alla proprietà o all'elemento dell'indicizzatore fornito dall'operando sinistro.

Il risultato di un'espressione di assegnazione semplice è il valore assegnato all'operando sinistro. Il risultato è dello stesso tipo dell'operando sinistro ed è sempre classificato come valore.

Se l'operando sinistro è un accesso a una proprietà o un indicizzatore, è necessario che la proprietà o l'indicizzatore disponga di una funzione di accesso `set`. In caso contrario, si verificherà un errore in fase di binding.

L'elaborazione in fase di esecuzione di un'assegnazione semplice del modulo `x = y` è costituita dai passaggi seguenti:

*  Se `x` è classificato come variabile:
   * `x` viene valutato per produrre la variabile.
   * `y` viene valutato e, se necessario, convertito nel tipo di `x` tramite una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)).
   * Se la variabile fornita da `x` è un elemento di matrice di un *reference_type*, viene eseguito un controllo della fase di esecuzione per garantire che il valore calcolato per `y` sia compatibile con l'istanza di matrice di cui `x` è un elemento. Il controllo ha esito positivo se `y` è `null` o se esiste una conversione implicita dei riferimenti ([conversioni di riferimenti impliciti](conversions.md#implicit-reference-conversions)) dal tipo effettivo dell'istanza a cui viene fatto riferimento da `y` al tipo di elemento effettivo dell'istanza di matrice contenente `x`. In caso contrario viene generata un'eccezione `System.ArrayTypeMismatchException`.
   * Il valore risultante dalla valutazione e dalla conversione di `y` viene archiviato nella posizione specificata dalla valutazione di `x`.
*  Se `x` è classificato come accesso a una proprietà o a un indicizzatore:
   * L'espressione dell'istanza (se `x` non è `static`) e l'elenco di argomenti (se `x` è un accesso all'indicizzatore) associato a `x` vengono valutati e i risultati vengono utilizzati nella successiva chiamata della funzione di accesso `set`.
   * `y` viene valutato e, se necessario, convertito nel tipo di `x` tramite una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)).
   * Viene richiamata la funzione di accesso `set` di `x` con il valore calcolato per `y` come argomento `value`.

Le regole di covarianza della matrice ([covarianza di matrice](arrays.md#array-covariance)) consentono a un valore di un tipo di matrice `A[]` di essere un riferimento a un'istanza di un tipo di matrice `B[]`, purché esista una conversione implicita di un riferimento da `B` a `A`. A causa di queste regole, l'assegnazione a un elemento di matrice di un *reference_type* richiede un controllo della fase di esecuzione per garantire che il valore assegnato sia compatibile con l'istanza di matrice. Nell'esempio
```csharp
string[] sa = new string[10];
object[] oa = sa;

oa[0] = null;               // Ok
oa[1] = "Hello";            // Ok
oa[2] = new ArrayList();    // ArrayTypeMismatchException
```
L'ultima assegnazione causa la generazione di un `System.ArrayTypeMismatchException` perché un'istanza di `ArrayList` non può essere archiviata in un elemento di un `string[]`.

Quando una proprietà o un indicizzatore dichiarato in un *struct_type* è la destinazione di un'assegnazione, l'espressione dell'istanza associata alla proprietà o all'indicizzatore deve essere classificata come variabile. Se l'espressione dell'istanza è classificata come valore, si verifica un errore in fase di binding. A causa dell' [accesso ai membri](expressions.md#member-access), la stessa regola si applica anche ai campi.

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
sono consentite le assegnazioni a `p.X`, `p.Y`, `r.A` e `r.B` perché `p` e `r` sono variabili. Tuttavia, nell'esempio
```csharp
Rectangle r = new Rectangle();
r.A.X = 10;
r.A.Y = 10;
r.B.X = 100;
r.B.Y = 100;
```
le assegnazioni sono tutte non valide, dal momento che `r.A` e `r.B` non sono variabili.

### <a name="compound-assignment"></a>Assegnazione composta

Se l'operando sinistro di un'assegnazione composta è nel formato `E.P` o `E[Ei]` in cui `E` presenta il tipo in fase di compilazione `dynamic`, l'assegnazione è associata dinamicamente ([associazione dinamica](expressions.md#dynamic-binding)). In questo caso il tipo in fase di compilazione dell'espressione di assegnazione è `dynamic` e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione in base al tipo di runtime `E`.

Un'operazione nel formato `x op= y` viene elaborata applicando la risoluzione dell'overload dell'operatore binario ([risoluzione dell'overload dell'operatore binario](expressions.md#binary-operator-overload-resolution)) come se l'operazione fosse stata scritta `x op y`. Quindi

*  Se il tipo restituito dell'operatore selezionato è convertibile in modo implicito nel tipo di `x`, l'operazione viene valutata come `x = x op y`, ad eccezione del fatto che `x` viene valutato una sola volta.
*  In caso contrario, se l'operatore selezionato è un operatore predefinito, se il tipo restituito dell'operatore selezionato è convertibile in modo esplicito nel tipo di `x` e se `y` è convertibile in modo implicito nel tipo di `x` o se l'operatore è un operatore Shift , quindi l'operazione viene valutata come `x = (T)(x op y)`, dove `T` è il tipo di `x`, ad eccezione del fatto che `x` viene valutato una sola volta.
*  In caso contrario, l'assegnazione composta non è valida e si verifica un errore in fase di binding.

Il termine "valutato una sola volta" significa che nella valutazione di `x op y`, i risultati di qualsiasi espressione costituente di `x` vengono salvati temporaneamente e quindi riusati quando si esegue l'assegnazione a `x`. Ad esempio, nell'assegnazione `A()[B()] += C()`, dove `A` è un metodo che restituisce `int[]` e `B` e `C` sono metodi che restituiscono `int`, i metodi vengono richiamati una sola volta, nell'ordine `A`, `B`, `C`.

Quando l'operando sinistro di un'assegnazione composta è un accesso a una proprietà o un indicizzatore, la proprietà o l'indicizzatore deve avere sia una funzione di accesso `get` sia una funzione di accesso `set`. In caso contrario, si verificherà un errore in fase di binding.

La seconda regola precedente consente la valutazione di `x op= y` come `x = (T)(x op y)` in determinati contesti. La regola esiste in modo tale che gli operatori predefiniti possono essere utilizzati come operatori composti quando l'operando sinistro è di tipo `sbyte`, `byte`, `short`, `ushort` o `char`. Anche quando entrambi gli argomenti sono di uno di questi tipi, gli operatori predefiniti producono un risultato di tipo `int`, come descritto in [Promotion numeric Binary](expressions.md#binary-numeric-promotions). Senza un cast, quindi, non sarebbe possibile assegnare il risultato all'operando sinistro.

L'effetto intuitivo della regola per gli operatori predefiniti è semplicemente che `x op= y` è consentito se sono consentiti sia `x op y` che `x = y`. Nell'esempio
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
il motivo intuitivo di ogni errore è che anche un'assegnazione semplice corrispondente potrebbe essere un errore.

Ciò significa anche che le operazioni di assegnazione composta supportano le operazioni lifted. Nell'esempio
```csharp
int? i = 0;
i += 1;             // Ok
```
viene usato l'operatore lifted `+(int?,int?)`.

### <a name="event-assignment"></a>Assegnazione evento

Se l'operando sinistro di un operatore `+=` o `-=` è classificato come accesso a un evento, l'espressione viene valutata nel modo seguente:

*  L'espressione dell'istanza, se presente, dell'accesso agli eventi viene valutata.
*  L'operando di destra dell'operatore `+=` o `-=` viene valutato e, se necessario, convertito nel tipo dell'operando sinistro tramite una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)).
*  Viene richiamata una funzione di accesso eventi dell'evento, con elenco di argomenti costituito dall'operando di destra, dopo la valutazione e, se necessario, la conversione. Se l'operatore è `+=`, viene richiamata la funzione di accesso `add`; Se l'operatore è `-=`, viene richiamata la funzione di accesso `remove`.

Un'espressione di assegnazione di evento non restituisce alcun valore. Pertanto, un'espressione di assegnazione eventi è valida solo nel contesto di un *statement_expression* ([istruzioni Expression](statements.md#expression-statements)).

## <a name="expression"></a>Expression

Un' *espressione* può essere un *non_assignment_expression* o un' *assegnazione*.

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

Un *constant_expression* è un'espressione che può essere valutata interamente in fase di compilazione.

```antlr
constant_expression
    : expression
    ;
```

Un'espressione costante deve essere il valore letterale `null` o un valore con uno dei seguenti tipi: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, 0, 1, 2, 3, 4 , 5 o qualsiasi tipo di enumerazione. Nelle espressioni costanti sono consentiti solo i costrutti seguenti:

*  Valori letterali (compreso il valore letterale `null`).
*  Riferimenti a membri `const` dei tipi di classe e struct.
*  Riferimenti a membri di tipi di enumerazione.
*  Riferimenti a parametri `const` o variabili locali
*  Espressioni secondarie tra parentesi, ovvero espressioni costanti.
*  Le espressioni cast, purché il tipo di destinazione sia uno dei tipi elencati in precedenza.
*  espressioni `checked` e `unchecked`
*  Espressioni con valore predefinito
*  Espressioni NameOf
*  Gli operatori unari predefiniti `+`, `-`, `!` e `~`.
*  Il @no__t predefinito-0, `-`, `*`, `/`, `%`, `<<`, `>>`, `&`, `|`, `^`, 0, 1, 2, 3, 4, 5, 6 e 7 operatori binari, purché ogni operando sia di tipo digitare elencato sopra.
*  Operatore condizionale `?:`.

Nelle espressioni costanti sono consentite le conversioni seguenti:

*  Conversioni di identità
*  Conversioni numeriche
*  Conversioni di enumerazione
*  Conversioni di espressioni costanti
*  Conversioni di riferimenti implicite ed esplicite, a condizione che l'origine delle conversioni sia un'espressione costante che restituisce il valore null.

Nelle espressioni costanti non sono consentite altre conversioni, incluse le conversioni boxing, unboxing e riferimento implicito di valori non null. Esempio:
```csharp
class C {
    const object i = 5;         // error: boxing conversion not permitted
    const object str = "hello"; // error: implicit reference conversion
}
```
l'inizializzazione di i è un errore perché è necessaria una conversione boxing. L'inizializzazione di str è un errore perché è necessaria una conversione implicita di un riferimento da un valore non null.

Ogni volta che un'espressione soddisfa i requisiti elencati sopra, l'espressione viene valutata in fase di compilazione. Questo vale anche se l'espressione è una sottoespressione di un'espressione più grande che contiene costrutti non costanti.

La valutazione in fase di compilazione delle espressioni costanti usa le stesse regole della valutazione in fase di esecuzione delle espressioni non costanti, ad eccezione del fatto che la valutazione in fase di esecuzione genera un'eccezione, mentre la valutazione in fase di compilazione genera un errore in fase di compilazione.

A meno che un'espressione costante non venga inserita in modo esplicito in un contesto `unchecked`, i flussi che si verificano nelle operazioni aritmetiche di tipo integrale e nelle conversioni durante la valutazione in fase di compilazione dell'espressione provocano sempre errori in fase di compilazione ([costante espressioni](expressions.md#constant-expressions)).

Le espressioni costanti si verificano nei contesti elencati di seguito. In questi contesti, si verifica un errore in fase di compilazione se non è possibile valutare completamente un'espressione in fase di compilazione.

*  Dichiarazioni di costanti ([costanti](classes.md#constants)).
*  Dichiarazioni di membri di enumerazione ([membri enum](enums.md#enum-members)).
*  Argomenti predefiniti degli elenchi di parametri formali ([parametri del metodo](classes.md#method-parameters))
*  etichette `case` di un'istruzione `switch` ([istruzione switch](statements.md#the-switch-statement)).
*  istruzioni `goto case` ([istruzione goto](statements.md#the-goto-statement)).
*  Lunghezza delle dimensioni in un'espressione di creazione di matrici ([espressioni di creazione di matrici](expressions.md#array-creation-expressions)) che include un inizializzatore.
*  Attributi ([attributi](attributes.md)).

Una conversione implicita di espressioni costanti ([conversioni implicite di espressioni costanti](conversions.md#implicit-constant-expression-conversions)) consente a un'espressione costante di tipo `int` di essere convertita in `sbyte`, `byte`, `short`, `ushort`, `uint` o `ulong`, a condizione che il valore di l'espressione costante rientra nell'intervallo del tipo di destinazione.

## <a name="boolean-expressions"></a>espressioni booleane

Un *Boolean_expression* è un'espressione che restituisce un risultato di tipo `bool`; direttamente o tramite l'applicazione di `operator true` in determinati contesti, come specificato nell'esempio seguente.

```antlr
boolean_expression
    : expression
    ;
```

Espressione condizionale di controllo di un *if_Statement* ([istruzione If](statements.md#the-if-statement)), *while_statement* ([istruzione while](statements.md#the-while-statement)), *do_statement* ([istruzione do](statements.md#the-do-statement)) o *for_Statement* ([per ](statements.md#the-for-statement)è un *Boolean_expression*. L'espressione condizionale di controllo dell'operatore `?:` ([operatore condizionale](expressions.md#conditional-operator)) segue le stesse regole di un *Boolean_expression*, ma per motivi di precedenza degli operatori viene classificato come *conditional_or_expression*.

Per poter produrre un valore di tipo `bool`, è necessario un *boolean_expression* `E`, come indicato di seguito:

*  Se `E` è implicitamente convertibile in `bool`, in fase di esecuzione viene applicata la conversione implicita.
*  In caso contrario, viene usata la risoluzione dell'overload dell'operatore unario ([risoluzione dell'overload dell'operatore unario](expressions.md#unary-operator-overload-resolution)) per trovare un'implementazione migliore univoca di operator `true` in `E` e tale implementazione viene applicata in fase di esecuzione.
*  Se non viene trovato alcun operatore di questo tipo, si verificherà un errore in fase di binding.

Il tipo di struct `DBBool` in [tipo di database booleano](structs.md#database-boolean-type) fornisce un esempio di un tipo che implementa `operator true` e `operator false`.
