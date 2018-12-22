# <a name="conversions"></a>Conversioni

Oggetto ***conversione*** consente a un'espressione deve essere considerato in corso di un determinato tipo. Una conversione potrebbe essere un'espressione di un determinato tipo per essere considerata come se presentasse un tipo diverso, altrimenti potrebbe verificarsi un'espressione senza un tipo da ottenere un tipo. Conversioni possono risultare ***implicita*** oppure ***esplicite***, e determina se è necessario un cast esplicito. Ad esempio, la conversione dal tipo `int` al tipo `long` è implicito, in tal caso espressioni di tipo `int` può essere gestito in modo implicito come tipo `long`. La conversione dal tipo `long` al tipo `int`, è esplicito e pertanto non è richiesto un cast esplicito.

```csharp
int a = 123;
long b = a;         // implicit conversion from int to long
int c = (int) b;    // explicit conversion from long to int
```

Alcune conversioni sono definite dal linguaggio. I programmi possono anche definire le proprie conversioni ([conversioni definite dall'utente](conversions.md#user-defined-conversions)).

## <a name="implicit-conversions"></a>Conversioni implicite

Le conversioni seguenti sono classificate come le conversioni implicite:

*  Conversioni di identità
*  Conversioni numeriche implicite
*  Conversioni implicite di enumerazione.
*  Conversioni implicite che ammette valori null
*  Conversioni di valori letterali null
*  Conversioni dei riferimenti impliciti
*  Conversioni boxing
*  Conversioni implicite dinamiche
*  Conversioni implicite di espressioni costanti
*  Conversioni implicite definite dall'utente
*  Conversioni delle funzioni anonime
*  Conversioni dei metodi di gruppo

Le conversioni implicite possono verificarsi in diverse situazioni, tra cui le chiamate di funzione membro ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), le espressioni cast ([espressioni Cast](expressions.md#cast-expressions)), assegnazioni e ([gli operatori di assegnazione](expressions.md#assignment-operators)).

Le conversioni implicite predefinite hanno sempre esito positivo e non possono causare la generazione di eccezioni. Progettato correttamente le conversioni implicite definite dall'utente devono presentare anche queste caratteristiche.

Ai fini di conversione, i tipi `object` e `dynamic` vengono considerati equivalenti.

Tuttavia, conversioni dinamiche ([conversioni implicite dinamiche](conversions.md#implicit-dynamic-conversions) e [conversioni esplicite dinamiche](conversions.md#explicit-dynamic-conversions)) si applicano solo alle espressioni di tipo `dynamic` ([tipo dinamico](types.md#the-dynamic-type)).

### <a name="identity-conversion"></a>Conversione di identità

Una conversione di identità converte da qualsiasi tipo nello stesso tipo. Questa conversione esiste un'entità che dispone già di un tipo di richiesta si può affermare sia convertibile in quel tipo.

*  Poiché object e dynamic vengono considerati equivalenti è una conversione di identità tra `object` e `dynamic`e tra tipi costruiti che sono gli stessi durante la sostituzione di tutte le occorrenze dei `dynamic` con `object`.

### <a name="implicit-numeric-conversions"></a>Conversioni numeriche implicite

Alle conversioni numeriche implicite sono:

*  Dal `sbyte` al `short`, `int`, `long`, `float`, `double`, o `decimal`.
*  Dal `byte` al `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `float`, `double`, o `decimal`.
*  Dal `short` al `int`, `long`, `float`, `double`, o `decimal`.
*  Dal `ushort` al `int`, `uint`, `long`, `ulong`, `float`, `double`, o `decimal`.
*  Dal `int` al `long`, `float`, `double`, o `decimal`.
*  Dal `uint` al `long`, `ulong`, `float`, `double`, o `decimal`.
*  Dal `long` al `float`, `double`, o `decimal`.
*  Dal `ulong` al `float`, `double`, o `decimal`.
*  Dal `char` al `ushort`, `int`, `uint`, `long`, `ulong`, `float`, `double`, o `decimal`.
*  Dal `float` a `double`.

Le conversioni da `int`, `uint`, `long`, o `ulong` per `float` e dal `long` oppure `ulong` per `double` potrebbe causare una perdita di precisione, ma non verrà mai causa una perdita di grandezza. Le altre conversioni numeriche implicite non comportano la perdita di tutte le informazioni.

Non sono presenti conversioni implicite per il `char` digita, in modo che i valori degli altri tipi integrali non vengono automaticamente convertiti il `char` tipo.

### <a name="implicit-enumeration-conversions"></a>Conversioni implicite di enumerazione

Una conversione implicita enumerazione consente il *decimal_integer_literal* `0` da convertire in qualsiasi *enum_type* e a qualsiasi *nullable_type* cui il tipo sottostante è un *enum_type*. Nel secondo caso viene valutata eseguendo la conversione a sottostante *enum_type* e il risultato di wrapping ([tipi Nullable](types.md#nullable-types)).

### <a name="implicit-interpolated-string-conversions"></a>Conversioni implicite stringa interpolata

Implicita interpolate consente la conversione di stringa un' *interpolated_string_expression* ([stringhe interpolate](expressions.md#interpolated-strings)) da convertire in `System.IFormattable` o `System.FormattableString` (che implementa `System.IFormattable`).

Quando questa conversione viene applicata un valore stringa non è composto dalla stringa interpolata. Invece un'istanza di `System.FormattableString` viene creata, come descritto [stringhe interpolate](expressions.md#interpolated-strings).

### <a name="implicit-nullable-conversions"></a>Conversioni implicite che ammette valori null

Conversioni implicite predefinite che operano sui tipi di valore non nullable sono anche utilizzabile con le forme di tali tipi nullable. Per ognuna delle identità impliciti predefiniti e conversioni numeriche che consentono di convertire da un tipo di valore non nullable `S` a un tipo di valore non nullable `T`, esistono le seguenti conversioni implicite che ammette valori null:

*  Una conversione implicita da `S?` a `T?`.
*  Una conversione implicita da `S` a `T?`.

Valutazione di una conversione implicita che ammette valori null in base una conversione da sottostante `S` a `T` procede nel modo seguente:

*  Se la conversione che ammette valori null dal `S?` a `T?`:
    * Se il valore di origine è null (`HasValue` proprietà è false), il risultato è il valore null di tipo `T?`.
    * In caso contrario, la conversione viene valutata come un annullamento del wrapping da `S?` a `S`, seguita dalla conversione da sottostante `S` a `T`, seguito da un ritorno a capo ([tipi Nullable](types.md#nullable-types)) da `T` a `T?`.

*  Se la conversione che ammette valori null è da `S` al `T?`, la conversione viene valutata come la conversione da sottostante `S` a `T` seguita da un ritorno a capo da `T` per `T?`.

### <a name="null-literal-conversions"></a>Conversioni di valori letterali null

Esiste una conversione implicita dal `null` letterale a qualsiasi tipo che ammette valori null. Questa conversione produce il valore null ([tipi Nullable](types.md#nullable-types)) del tipo che ammette valori null specificato.

### <a name="implicit-reference-conversions"></a>Conversioni dei riferimenti impliciti

Le conversioni di riferimenti impliciti sono:

*  Da qualsiasi *reference_type* al `object` e `dynamic`.
*  Da qualsiasi *class_type* `S` a qualsiasi *class_type* `T`, fornito `S` è derivato da `T`.
*  Da qualsiasi *class_type* `S` a qualsiasi *interface_type* `T`, fornito `S` implementa `T`.
*  Da qualsiasi *interface_type* `S` a qualsiasi *interface_type* `T`, fornito `S` è derivato da `T`.
*  Da un *array_type* `S` con un tipo di elemento `SE` a un *array_type* `T` con un tipo di elemento `TE`, purché tutti gli elementi seguenti sono vere:
    * `S` e `T` differiscono solo nel tipo di elemento. In altre parole, `S` e `T` hanno lo stesso numero di dimensioni.
    * Entrambe `SE` e `TE` vengono *reference_type*s.
    * Esiste una conversione implicita del riferimento da `SE` a `TE`.
*  Da qualsiasi *array_type* a `System.Array` e le interfacce implementate.
*  Da un tipo di matrice unidimensionale `S[]` al `System.Collections.Generic.IList<T>` e le relative interfacce di base, purché esista una conversione implicita di identità o un riferimento dal `S` a `T`.
*  Da qualsiasi *delegate_type* a `System.Delegate` e le interfacce implementate.
*  Dal valore letterale null a qualsiasi *reference_type*.
*  Da qualsiasi *reference_type* a un *reference_type* `T` se dispone di una conversione implicita di identità o un riferimento a un *reference_type* `T0` e `T0` dispone di una conversione di identità per `T`.
*  Da qualsiasi *reference_type* a un tipo interfaccia o delegato `T` se dispone di una conversione implicita di identità o un riferimento a un tipo interfaccia o delegato `T0` e `T0` è convertibile la varianza ([ La conversione della varianza](interfaces.md#variance-conversion)) a `T`.
*  Le conversioni implicite digitare i parametri sono noti come tipi di riferimento. Visualizzare [conversioni implicite con i parametri di tipo](conversions.md#implicit-conversions-involving-type-parameters) per altre informazioni sulle conversioni implicite parametri di tipo.

Le conversioni di riferimenti impliciti sono conversioni tra *reference_type*che hanno sempre esito positivo e pertanto non richiede alcun controllo in fase di esecuzione.

Le conversioni dei riferimenti, implicite o esplicite, non modificano mai l'identità referenziale dell'oggetto da convertire. In altre parole, mentre la conversione di un riferimento può modificare il tipo del riferimento, non modifica mai il tipo o il valore dell'oggetto a cui si fa riferimento.

### <a name="boxing-conversions"></a>Conversioni boxing

Una conversione boxing consente una *value_type* da convertire in modo implicito in un tipo di riferimento. Esiste una conversione boxing da una qualsiasi *non_nullable_value_type* al `object` e `dynamic`, alla `System.ValueType` e a qualsiasi *interface_type* implementata dal *non_ nullable_value_type*. Inoltre un' *enum_type* può essere convertito nel tipo `System.Enum`.

Esiste una conversione boxing da una *nullable_type* a un tipo riferimento, se e solo se la conversione boxing esiste da sottostante *non_nullable_value_type* al tipo di riferimento.

Un tipo di valore ha una conversione boxing in un tipo di interfaccia `I` se è presente una conversione boxing di un tipo di interfaccia `I0` e `I0` dispone di una conversione di identità per `I`.

Un tipo di valore ha una conversione boxing in un tipo di interfaccia `I` se è presente una conversione boxing di un tipo interfaccia o delegato `I0` e `I0` è convertibile la varianza ([conversione della varianza](interfaces.md#variance-conversion)) al `I`.

Conversione boxing di un valore di una *non_nullable_value_type* consiste nell'allocare un'istanza dell'oggetto e copia le *value_type* valore in tale istanza. Uno struct può essere sottoposto a boxing per il tipo `System.ValueType`, dal momento che è una classe base per tutti gli struct ([ereditarietà](structs.md#inheritance)).

Conversione boxing di un valore di una *nullable_type* procede nel modo seguente:

*  Se il valore di origine è null (`HasValue` proprietà è false), il risultato è un riferimento null del tipo di destinazione.
*  In caso contrario, il risultato è un riferimento a boxed `T` prodotti dalla rimozione del wrapping e il boxing del valore di origine.

Le conversioni boxing ulteriori informazioni sono vedere [conversioni Boxing](types.md#boxing-conversions).

### <a name="implicit-dynamic-conversions"></a>Conversioni implicite dinamiche

Non esiste una conversione implicita dinamica da un'espressione di tipo `dynamic` a qualsiasi tipo `T`. La conversione in modo dinamico è associata ([binding dinamico](expressions.md#dynamic-binding)), il che significa che una conversione implicita verrà cercata in fase di esecuzione dal tipo di fase di esecuzione dell'espressione a `T`. Se non viene trovata alcuna conversione, viene generata un'eccezione in fase di esecuzione.

Si noti che la conversione implicita apparentemente viola il Consiglio all'inizio della [conversioni implicite](conversions.md#implicit-conversions) che una conversione implicita non dovrebbe mai provocare un'eccezione. Tuttavia, non l'operazione di conversione, ma il *ricerca* della conversione che causa l'eccezione. Il rischio di eccezioni di run-time è dovuto all'uso dell'associazione dinamica. Se non si desidera l'associazione dinamica della conversione, l'espressione può essere convertita prima di tutto a `object`e quindi al tipo desiderato.

L'esempio seguente illustra le conversioni implicite di dinamiche:

```csharp
object o  = "object"
dynamic d = "dynamic";

string s1 = o; // Fails at compile-time -- no conversion exists
string s2 = d; // Compiles and succeeds at run-time
int i     = d; // Compiles but fails at run-time -- no conversion exists
```

Le assegnazioni per `s2` e `i` entrambi impiegare le conversioni implicite dinamiche, in cui l'associazione delle operazioni viene sospesa fino alla fase di esecuzione. In fase di esecuzione, vengono richieste le conversioni implicite dal tipo di runtime del `d`  --  `string` : il tipo di destinazione. Viene rilevata che una conversione `string` ma non al `int`.

### <a name="implicit-constant-expression-conversions"></a>Conversioni implicite di espressioni costanti

Una conversione implicita espressione costante consente le conversioni seguenti:

*  Oggetto *constant_expression* ([espressioni costanti](expressions.md#constant-expressions)) di tipo `int` può essere convertito nel tipo `sbyte`, `byte`, `short`, `ushort`, `uint`, oppure `ulong`, ha fornito il valore della *constant_expression* è compreso nell'intervallo del tipo di destinazione.
*  Oggetto *constant_expression* typu `long` può essere convertito nel tipo `ulong`, fornito il valore del *constant_expression* non sia negativa.

### <a name="implicit-conversions-involving-type-parameters"></a>Conversioni implicite con i parametri di tipo

Per un parametro di tipo specificato esistono le seguenti conversioni implicite `T`:

*  Dal `T` alla classe di base efficace `C`, da `T` per qualsiasi classe di base di `C`e da `T` a qualsiasi interfaccia implementata da `C`. Se in fase di esecuzione, at `T` è un tipo di valore, la conversione viene eseguita la conversione boxing. In caso contrario, la conversione viene eseguita come una conversione implicita del riferimento o conversione di identità.
*  Dal `T` a un tipo di interfaccia `I` nel `T`efficace interfaccia set e da `T` per qualsiasi interfaccia di base di `I`. Se in fase di esecuzione, at `T` è un tipo di valore, la conversione viene eseguita la conversione boxing. In caso contrario, la conversione viene eseguita come una conversione implicita del riferimento o conversione di identità.
*  Dal `T` a un parametro di tipo `U`, specificato `T` dipende `U` ([digitare i vincoli del parametro](classes.md#type-parameter-constraints)). Se in fase di esecuzione, at `U` è un tipo di valore, quindi `T` e `U` sono necessariamente dello stesso tipo e viene eseguita alcuna conversione. In caso contrario, se `T` è un tipo di valore, la conversione viene eseguita la conversione boxing. In caso contrario, la conversione viene eseguita come una conversione implicita del riferimento o conversione di identità.
*  Dal valore letterale null per `T`, fornito `T` è noto come tipo di riferimento.
*  Dal `T` a un tipo riferimento `I` se dispone di una conversione implicita a un tipo di riferimento `S0` e `S0` dispone di una conversione di identità per `S`. In fase di esecuzione la conversione viene eseguita esattamente come la conversione a `S0`.
*  Dal `T` a un tipo di interfaccia `I` se dispone di una conversione implicita a un tipo interfaccia o delegato `I0` e `I0` è convertibile la varianza per `I` ([conversione della varianza](interfaces.md#variance-conversion) ). Se in fase di esecuzione, at `T` è un tipo di valore, la conversione viene eseguita la conversione boxing. In caso contrario, la conversione viene eseguita come una conversione implicita del riferimento o conversione di identità.

Se `T` è noto per essere un tipo riferimento ([digitare i vincoli del parametro](classes.md#type-parameter-constraints)), tutte le conversioni sopra sono classificate come le conversioni dei riferimenti impliciti ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)). Se `T` non è noto per essere un tipo riferimento, le conversioni precedenti vengono classificate come le conversioni boxing ([conversioni Boxing](conversions.md#boxing-conversions)).

### <a name="user-defined-implicit-conversions"></a>Conversioni implicite definite dall'utente

Una conversione implicita definito dall'utente è costituito da una conversione implicita standard facoltativa, seguita dall'esecuzione di un operatore definito dall'utente la conversione implicita, seguito da un altro facoltativa standard la conversione implicita. Le regole precise per la valutazione delle conversioni implicite definite dall'utente sono descritti [l'elaborazione delle conversioni implicite definite dall'utente](conversions.md#processing-of-user-defined-implicit-conversions).

### <a name="anonymous-function-conversions-and-method-group-conversions"></a>Le conversioni di funzione anonima e di gruppo (metodo)

Funzioni anonime e i gruppi di metodi non dispongono di tipi di se stessi, ma possono essere convertiti in modo implicito in tipi delegati o tipi di albero delle espressioni. Conversioni delle funzioni anonime sono descritti più dettagliatamente [conversioni delle funzioni anonime](conversions.md#anonymous-function-conversions) metodo gruppo conversioni e in [conversioni dei metodi di gruppo](conversions.md#method-group-conversions).

## <a name="explicit-conversions"></a>Conversioni esplicite

Le conversioni seguenti sono classificate come le conversioni esplicite:

*  Tutte le conversioni implicite.
*  Conversioni numeriche esplicite.
*  Conversioni enumerazione esplicita.
*  Conversioni esplicite che ammette valori null.
*  Conversioni riferimento esplicito.
*  Conversioni esplicite dell'interfaccia.
*  Conversione unboxing.
*  Conversioni esplicite dinamiche
*  Conversioni esplicite definite dall'utente.

Le conversioni esplicite possono verificarsi nelle espressioni cast ([espressioni Cast](expressions.md#cast-expressions)).

Il set di conversioni esplicite include tutte le conversioni implicite. Ciò significa che sono consentite espressioni cast ridondanti.

Le conversioni esplicite che non sono conversioni implicite sono conversioni che non hanno sempre esito positivo, le conversioni per cui sono noti per determinare una perdita di informazioni e le conversioni tra domini di tipi diversi di merito esplicita notazione.

### <a name="explicit-numeric-conversions"></a>Conversioni numeriche esplicite

Conversioni numeriche esplicite sono riportate le conversioni da un *numeric_type* a un'altra *numeric_type* per il quale una conversione numerica implicita ([conversioni numeriche implicite](conversions.md#implicit-numeric-conversions)) non esiste già:

*  Dal `sbyte` al `byte`, `ushort`, `uint`, `ulong`, o `char`.
*  Dal `byte` al `sbyte` e `char`.
*  Dal `short` al `sbyte`, `byte`, `ushort`, `uint`, `ulong`, o `char`.
*  Dal `ushort` al `sbyte`, `byte`, `short`, o `char`.
*  Dal `int` al `sbyte`, `byte`, `short`, `ushort`, `uint`, `ulong`, o `char`.
*  Dal `uint` al `sbyte`, `byte`, `short`, `ushort`, `int`, o `char`.
*  Dal `long` al `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `ulong`, o `char`.
*  Dal `ulong` al `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, o `char`.
*  Dal `char` al `sbyte`, `byte`, o `short`.
*  Dal `float` al `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, o `decimal`.
*  Dal `double` al `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, o `decimal`.
*  Dal `decimal` al `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, o `double`.

Poiché le conversioni esplicite includono tutte le conversioni numeriche implicite ed esplicite, è sempre possibile eseguire la conversione da una qualsiasi *numeric_type* a qualsiasi altra *numeric_type* usando una coppia (espressione cast [Espressioni cast](expressions.md#cast-expressions)).

Conversioni numeriche esplicite determinare una perdita di informazioni o che si verifichi la generazione di eccezioni. Una conversione numerica esplicita viene elaborata come segue:

*  Per una conversione da un tipo integrale a un altro tipo integrale, l'elaborazione dipende dal contesto di controllo dell'overflow ([gli operatori checked e unchecked](expressions.md#the-checked-and-unchecked-operators)) in cui la conversione richiede inserire:
    * In un `checked` contesto, la conversione riesce se il valore dell'operando di origine è compreso nell'intervallo del tipo di destinazione, ma genera un `System.OverflowException` se il valore dell'operando di origine è compreso nell'intervallo del tipo di destinazione.
    * In un `unchecked` contesto, la conversione sempre ha esito positivo e procede nel modo seguente.
        * Se il tipo di origine è maggiore del tipo di destinazione, il valore di origine viene troncato rimuovendo i relativi "extra" bit più rilevanti. Il risultato viene quindi trattato come un valore del tipo di destinazione.
        * Se il tipo di origine è inferiore al tipo di destinazione, il valore di origine ha estensione firma o estensione zero in modo da avere le stesse dimensioni del tipo di destinazione. L'estensione firma viene usata se il tipo di origine dispone della firma; l'estensione zero viene usata se il tipo di origine è privo di firma. Il risultato viene quindi trattato come un valore del tipo di destinazione.
        * Se il tipo di origine ha le stesse dimensioni del tipo di destinazione, il valore di origine viene considerato un valore del tipo di destinazione.
*  Per una conversione da `decimal` a un tipo integrale, il valore di origine viene arrotondato per difetto al valore integrale più vicino e tale valore viene quindi il risultato della conversione. Se il valore integrale risultante non rientra nell'intervallo del tipo di destinazione, un `System.OverflowException` viene generata un'eccezione.
*  Per una conversione da `float` oppure `double` a un tipo integrale, l'elaborazione dipende dal contesto di controllo dell'overflow ([gli operatori checked e unchecked](expressions.md#the-checked-and-unchecked-operators)) in cui la conversione richiede inserire:
    * In un `checked` contesto, la conversione avviene nel modo seguente:
        * Se il valore dell'operando è NaN o infinito, un `System.OverflowException` viene generata un'eccezione.
        * In caso contrario, l'operando di origine viene arrotondato per difetto al valore integrale più vicino. Se è questo valore integrale compreso nell'intervallo del tipo di destinazione questo valore è il risultato della conversione.
        * In caso contrario viene generata un'eccezione `System.OverflowException`.
    * In un `unchecked` contesto, la conversione sempre ha esito positivo e procede nel modo seguente.
        * Se il valore dell'operando è un valore NaN o infinito, il risultato della conversione è un valore non specificato del tipo di destinazione.
        * In caso contrario, l'operando di origine viene arrotondato per difetto al valore integrale più vicino. Se è questo valore integrale compreso nell'intervallo del tipo di destinazione questo valore è il risultato della conversione.
        * In caso contrario, il risultato della conversione è un valore non specificato del tipo di destinazione.
*  Per una conversione da `double` al `float`, il `double` valore viene arrotondato al più vicino `float` valore. Se il `double` valore è troppo piccolo per essere rappresentato come un `float`, il risultato diventa zero positivo o negativo. Se il `double` valore è troppo grande per essere rappresentato come un `float`, il risultato diventa infinito positivo o negativo. Se il `double` valore è NaN, il risultato è NaN.
*  Per una conversione da `float` oppure `double` al `decimal`, il valore di origine viene convertito in `decimal` rappresentazione e arrotondato al numero più vicino successivo dopo la posizione decimale 28, se necessario ([tipo decimale](types.md#the-decimal-type)). Se il valore di origine è troppo piccolo per essere rappresentato come un `decimal`, il risultato diventa zero. Se il valore di origine è NaN, infinito o troppo grande per essere rappresentato come un `decimal`, un `System.OverflowException` viene generata un'eccezione.
*  Per una conversione da `decimal` a `float` oppure `double`, il `decimal` valore viene arrotondato al più vicino `double` o `float` valore. Durante questa conversione possibile perdita di precisione, comporta mai la generazione di un'eccezione.

### <a name="explicit-enumeration-conversions"></a>Conversioni enumerazione esplicita

Le conversioni enumerazione esplicita sono:

*  Dal `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, o `decimal` a qualsiasi *enum_type*.
*  Da qualsiasi *enum_type* al `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, o `decimal`.
*  Da qualsiasi *enum_type* a qualsiasi altra *enum_type*.

Una conversione esplicita di enumerazione tra due tipi viene elaborata, considerando qualsiasi partecipanti *enum_type* con il tipo sottostante di tale *enum_type*ed eseguendo un implicita o esplicita conversione numerica tra i tipi risultanti. Si consideri, ad esempio, un' *enum_type* `E` con e il tipo sottostante del `int`, una conversione da `E` alla `byte` viene elaborato come una conversione numerica esplicita ([Explicit conversioni numeriche](conversions.md#explicit-numeric-conversions)) da `int` al `byte`e una conversione da `byte` al `E` viene elaborato come una conversione numerica implicita ([conversioni numeriche implicite](conversions.md#implicit-numeric-conversions)) dal `byte` a `int`.

### <a name="explicit-nullable-conversions"></a>Conversioni esplicite che ammette valori null

***Conversioni esplicite nullable*** autorizzazione predefiniti per le conversioni esplicite che operano sui tipi di valore non nullable per essere usato anche con le forme di tali tipi nullable. Per ognuna delle conversioni esplicite predefinite che consentono di convertire da un tipo di valore non nullable `S` a un tipo di valore non nullable `T` ([conversione di identità](conversions.md#identity-conversion), [conversioni numeriche implicite](conversions.md#implicit-numeric-conversions), [Conversioni implicite enumerazione](conversions.md#implicit-enumeration-conversions), [conversioni numeriche esplicite](conversions.md#explicit-numeric-conversions), e [conversioni enumerazione esplicita](conversions.md#explicit-enumeration-conversions)), quanto segue sono disponibili conversioni nullable:

*  Una conversione esplicita dal `S?` a `T?`.
*  Una conversione esplicita dal `S` a `T?`.
*  Una conversione esplicita dal `S?` a `T`.

Valutazione di una conversione che ammette valori null in base una conversione da sottostante `S` a `T` procede nel modo seguente:

*  Se la conversione che ammette valori null dal `S?` a `T?`:
    * Se il valore di origine è null (`HasValue` proprietà è false), il risultato è il valore null di tipo `T?`.
    * In caso contrario, la conversione viene valutata come un annullamento del wrapping da `S?` al `S`, seguita dalla conversione sottostante da `S` al `T`, seguito da un ritorno a capo da `T` a `T?`.
*  Se la conversione che ammette valori null è da `S` al `T?`, la conversione viene valutata come la conversione da sottostante `S` a `T` seguita da un ritorno a capo da `T` per `T?`.
*  Se la conversione che ammette valori null è da `S?` al `T`, la conversione viene valutata come un annullamento del wrapping dalla `S?` a `S` seguita dalla conversione sottostante da `S` a `T`.

Si noti che un tentativo di annullare il wrapping di un valore nullable genererà un'eccezione se il valore è `null`.

### <a name="explicit-reference-conversions"></a>Conversioni dei riferimenti espliciti

Le conversioni di riferimenti espliciti sono:

*  Dal `object` e `dynamic` a qualsiasi altra *reference_type*.
*  Da qualsiasi *class_type* `S` a qualsiasi *class_type* `T`, fornito `S` è una classe di base di `T`.
*  Da qualsiasi *class_type* `S` a qualsiasi *interface_type* `T`, fornito `S` non sia sealed e forniti `S` neimplementuje metodu `T`.
*  Da qualsiasi *interface_type* `S` a qualsiasi *class_type* `T`, fornito `T` non è sealed o fornito `T` implementa `S`.
*  Da qualsiasi *interface_type* `S` a qualsiasi *interface_type* `T`, fornito `S` non è derivato da `T`.
*  Da un *array_type* `S` con un tipo di elemento `SE` a un *array_type* `T` con un tipo di elemento `TE`, purché tutti gli elementi seguenti sono vere:
    * `S` e `T` differiscono solo nel tipo di elemento. In altre parole, `S` e `T` hanno lo stesso numero di dimensioni.
    * Entrambe `SE` e `TE` vengono *reference_type*s.
    * Esiste una conversione esplicita di riferimenti dal `SE` a `TE`.
*  Dal `System.Array` e le interfacce implementate in qualsiasi *array_type*.
*  Da un tipo di matrice unidimensionale `S[]` al `System.Collections.Generic.IList<T>` e le relative interfacce di base, purché esista una conversione esplicita di riferimenti dal `S` a `T`.
*  Dal `System.Collections.Generic.IList<S>` e interfacce di base a un tipo di matrice unidimensionale `T[]`, a condizione che vi è una conversione esplicita di identità o un riferimento dal `S` a `T`.
*  Dal `System.Delegate` e le interfacce implementate in qualsiasi *delegate_type*.
*  Da un tipo di riferimento a un tipo riferimento `T` se dispone di una conversione esplicita di riferimenti a un tipo riferimento `T0` e `T0` dispone di una conversione di identità `T`.
*  Da un tipo di riferimento a un tipo interfaccia o delegato `T` se dispone di una conversione esplicita di riferimenti a un tipo interfaccia o delegato `T0` e il valore `T0` è convertibile la varianza per `T` o `T` è convertibile la varianza per `T0` ([conversione della varianza](interfaces.md#variance-conversion)).
*  Dal `D<S1...Sn>` al `D<T1...Tn>` in cui `D<X1...Xn>` è un tipo delegato generico, `D<S1...Sn>` non è compatibile con o identico al `D<T1...Tn>`e per ogni parametro di tipo `Xi` di `D` contenuti nelle seguenti:
    * Se `Xi` è invariabile, quindi `Si` è identica alla `Ti`.
    * Se `Xi` è covariante, non vi è una conversione di identità o un riferimento implicita o esplicita da `Si` a `Ti`.
    * Se `Xi` è controvariante, quindi `Si` e `Ti` sono identici o entrambi i tipi di riferimento.
*  Conversioni esplicite che includono parametri di tipo che sono noti come tipi di riferimento. Per altre informazioni dettagliate sulle conversioni esplicite con i parametri di tipo, vedere [conversioni esplicite con i parametri di tipo](conversions.md#explicit-conversions-involving-type-parameters).

Le conversioni di riferimenti espliciti sono conversioni tra tipi di riferimento che richiedono controlli in fase di esecuzione per assicurarsi che siano corretti.

Per una conversione esplicita di riferimento abbia esito positivo in fase di esecuzione, il valore dell'operando di origine deve essere `null`, o il tipo effettivo dell'oggetto a cui fa riferimento l'operando di origine deve essere un tipo che può essere convertito nel tipo di destinazione da un riferimento implicito conversione ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)) o una conversione boxing ([conversioni Boxing](conversions.md#boxing-conversions)). Se una conversione esplicita di riferimento non riesce, un `System.InvalidCastException` viene generata un'eccezione.

Le conversioni dei riferimenti, implicite o esplicite, non modificano mai l'identità referenziale dell'oggetto da convertire. In altre parole, mentre la conversione di un riferimento può modificare il tipo del riferimento, non modifica mai il tipo o il valore dell'oggetto a cui si fa riferimento.

### <a name="unboxing-conversions"></a>Conversione unboxing

Una conversione unboxing consente un tipo di riferimento da convertire in modo esplicito in una *value_type*. Esiste una conversione unboxing dai tipi `object`, `dynamic` e `System.ValueType` a qualsiasi *non_nullable_value_type*e da qualsiasi *interface_type* a qualsiasi *non_ nullable_value_type* che implementa le *interface_type*. Inoltre digitare `System.Enum` possono essere boxed a qualsiasi *enum_type*.

Esiste una conversione unboxing da un tipo di riferimento a un *nullable_type* se esiste una conversione unboxing dal tipo di riferimento sottostante *non_nullable_value_type* del  *nullable_type*.

Un tipo di valore `S` dispone di una conversione unboxing di un tipo di interfaccia `I` se dispone di una conversione unboxing di un tipo di interfaccia `I0` e `I0` dispone di una conversione di identità per `I`.

Un tipo di valore `S` dispone di una conversione unboxing di un tipo di interfaccia `I` se dispone di una conversione unboxing di un tipo interfaccia o delegato `I0` e il valore `I0` è convertibile la varianza per `I` o`I`sia convertibile la varianza per `I0` ([conversione della varianza](interfaces.md#variance-conversion)).

Un'operazione unboxing prevede di verificare prima che l'istanza dell'oggetto è un valore boxed di dato *value_type*e quindi copiando il valore all'esterno dell'istanza. Conversione unboxing di un riferimento null a un *nullable_type* produce il valore null delle *nullable_type*. Uno struct può essere boxed dal tipo `System.ValueType`, dal momento che è una classe base per tutti gli struct ([ereditarietà](structs.md#inheritance)).

Vengono descritte dettagliatamente nella conversione unboxing [conversioni di Unboxing](types.md#unboxing-conversions).

### <a name="explicit-dynamic-conversions"></a>Conversioni esplicite dinamiche

Esiste una conversione dinamica esplicita da un'espressione di tipo `dynamic` a qualsiasi tipo `T`. La conversione in modo dinamico è associata ([binding dinamico](expressions.md#dynamic-binding)), il che significa che una conversione esplicita verrà cercata in fase di esecuzione dal tipo di fase di esecuzione dell'espressione a `T`. Se non viene trovata alcuna conversione, viene generata un'eccezione in fase di esecuzione.

Se non si desidera l'associazione dinamica della conversione, l'espressione può essere convertita prima di tutto a `object`e quindi al tipo desiderato.

Si supponga che è definita la classe seguente:
```csharp
class C
{
    int i;

    public C(int i) { this.i = i; }

    public static explicit operator C(string s) 
    {
        return new C(int.Parse(s));
    }
}
```

L'esempio seguente illustra le conversioni esplicite di dinamiche:
```csharp
object o  = "1";
dynamic d = "2";

var c1 = (C)o; // Compiles, but explicit reference conversion fails
var c2 = (C)d; // Compiles and user defined conversion succeeds
```

La conversione dei migliori `o` a `C` viene trovato in fase di compilazione per essere eseguita una conversione esplicita di riferimenti. L'operazione non riesce in fase di esecuzione, in quanto `"1"` non è in effetti un `C`. La conversione di `d` al `C` tuttavia, come una conversione esplicita dinamica, viene sospesa alla fase di esecuzione, in cui un utente definito conversione dal tipo di runtime del `d`  --  `string` , ovvero a `C` viene trovato, e ha esito positivo.

### <a name="explicit-conversions-involving-type-parameters"></a>Conversioni esplicite che includono parametri di tipo

Per un parametro di tipo specificato esistono le seguenti conversioni esplicite `T`:

*  Dalla classe di base efficace `C` dei `T` a `T` e da qualsiasi classe di base di `C` a `T`. Se in fase di esecuzione, at `T` è un tipo di valore, la conversione viene eseguita come una conversione unboxing. In caso contrario, la conversione viene eseguita come un riferimento esplicito o conversione di identità.
*  Da qualsiasi tipo di interfaccia a `T`. Se in fase di esecuzione, at `T` è un tipo di valore, la conversione viene eseguita come una conversione unboxing. In caso contrario, la conversione viene eseguita come un riferimento esplicito o conversione di identità.
*  Dal `T` a qualsiasi *interface_type* `I` fornito non esiste già una conversione implicita da `T` a `I`. Se in fase di esecuzione, at `T` è un tipo di valore, la conversione viene eseguita la conversione boxing seguita da una conversione esplicita di riferimento. In caso contrario, la conversione viene eseguita come un riferimento esplicito o conversione di identità.
*  Da un parametro di tipo `U` al `T`, specificato `T` dipende `U` ([digitare i vincoli del parametro](classes.md#type-parameter-constraints)). Se in fase di esecuzione, at `U` è un tipo di valore, quindi `T` e `U` sono necessariamente dello stesso tipo e viene eseguita alcuna conversione. In caso contrario, se `T` è un tipo di valore, la conversione viene eseguita come una conversione unboxing. In caso contrario, la conversione viene eseguita come un riferimento esplicito o conversione di identità.

Se `T` è destinata a essere un tipo riferimento, tutte le conversioni sopra sono classificate come le conversioni dei riferimenti espliciti ([le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)). Se `T` non è noto per essere un tipo riferimento, le conversioni sopra sono classificate come unboxing conversioni ([conversione Unboxing](conversions.md#unboxing-conversions)).

Le regole precedenti non permettono una conversione esplicita diretta da un parametro di tipo senza vincoli in un tipo non di interfaccia, che potrebbe essere sorprendente. Il motivo di questa regola è per evitare confusione e rendere la semantica di tali conversioni. Si consideri ad esempio la seguente dichiarazione:
```csharp
class X<T>
{
    public static long F(T t) {
        return (long)t;                // Error 
    }
}
```

Se la conversione esplicita di direct `t` a `int` fosse consentito, ci si aspetterebbe facilmente che `X<int>.F(7)` restituirebbe `7L`. Tuttavia, non lo avrebbe, poiché le conversioni numeriche standard vengono considerate solo quando i tipi sono noti per essere un valore numerico in fase di associazione. Per rendere la semantica chiari, nell'esempio precedente sarà necessario scrivere:
```csharp
class X<T>
{
    public static long F(T t) {
        return (long)(object)t;        // Ok, but will only work when T is long
    }
}
```

Questo codice verrà quindi compilato ma l'esecuzione `X<int>.F(7)` genererà un'eccezione in fase di esecuzione, poiché boxed `int` non può essere convertito direttamente in un `long`.

### <a name="user-defined-explicit-conversions"></a>Conversioni esplicite definite dall'utente

Una conversione esplicita definita dall'utente è costituito da una conversione esplicita standard facoltativa, seguita dall'esecuzione di un operatore di conversione implicita o esplicita definita dall'utente, seguito da un'altra conversione esplicita standard facoltativa. Le regole precise per la valutazione di conversioni esplicite definite dall'utente sono descritti [elaborazione di conversioni esplicite definite dall'utente](conversions.md#processing-of-user-defined-explicit-conversions).

## <a name="standard-conversions"></a>Conversioni standard

Le conversioni standard sono conversioni predefinite che possono verificarsi come parte di una conversione definita dall'utente.

### <a name="standard-implicit-conversions"></a>Conversioni implicite

Le seguenti conversioni implicite sono classificate come le conversioni implicite standard:

*  Conversioni di identità ([conversione di identità](conversions.md#identity-conversion))
*  Conversioni numeriche implicite ([conversioni numeriche implicite](conversions.md#implicit-numeric-conversions))
*  Conversioni implicite che ammette valori null ([conversioni implicite nullable](conversions.md#implicit-nullable-conversions))
*  Le conversioni dei riferimenti impliciti ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions))
*  Le conversioni boxing ([conversioni Boxing](conversions.md#boxing-conversions))
*  Le conversioni implicite di espressioni costanti ([conversioni implicite dinamiche](conversions.md#implicit-dynamic-conversions))
*  Conversioni implicite con i parametri di tipo ([conversioni implicite con i parametri di tipo](conversions.md#implicit-conversions-involving-type-parameters))

Le conversioni implicite standard escludere specificamente le conversioni implicite definite dall'utente.

### <a name="standard-explicit-conversions"></a>Conversioni esplicite standard

Le conversioni esplicite standard sono tutte le conversioni implicite standard oltre il subset di conversioni esplicite per il quale esiste una conversione implicita standard opposta. In altre parole, se uno standard implicito non esiste conversione da un tipo `A` a un tipo `B`, quindi esiste una conversione esplicita standard dal tipo `A` per digitare `B` e dal tipo `B` al tipo `A`.

## <a name="user-defined-conversions"></a>Conversioni definite dall'utente

C# consente le conversioni implicite ed esplicite predefinite essere integrati con ***conversioni definite dall'utente***. Conversioni definite dall'utente vengono introdotti con la dichiarazione di operatori di conversione ([gli operatori di conversione](classes.md#conversion-operators)) nei tipi di classi e struct.

### <a name="permitted-user-defined-conversions"></a>Consentite conversioni definite dall'utente

C# consente solo alcune conversioni definite dall'utente da dichiarare. In particolare, non è possibile ridefinire una conversione implicita o esplicita già esistente.

Per un tipo di origine specificato `S` e tipo di destinazione `T`, se `S` oppure `T` sono i tipi nullable, consentire `S0` e `T0` fanno riferimento ai tipi sottostanti, in caso contrario `S0` e `T0` sono uguale a `S` e `T` rispettivamente. Una classe o struct è possibile dichiarare una conversione da un tipo di origine `S` a un tipo di destinazione `T` solo se tutte le operazioni seguenti sono vere:

*  `S0` e `T0` sono tipi diversi.
*  Sia `S0` o `T0` è il tipo di classe o struct in cui viene eseguita la dichiarazione di operatore.
*  Né `S0` né `T0` è un *interface_type*.
*  Escludendo le conversioni definite dall'utente, che non esiste una conversione da `S` al `T` o da `T` a `S`.

Le restrizioni relative alle conversioni definite dall'utente sono illustrate dettagliatamente nella [gli operatori di conversione](classes.md#conversion-operators).

### <a name="lifted-conversion-operators"></a>Operatori di conversione elevata

Dato un operatore di conversione definita dall'utente che esegue la conversione da un tipo di valore non nullable `S` a un tipo di valore non nullable `T`, un ***operatore di conversione è elevato*** esiste che converte da `S?` alla `T?`. Questo operatore di conversione lift esegue un annullamento del wrapping da `S?` al `S` seguita dalla conversione definita dall'utente da `S` al `T` seguita da un ritorno a capo da `T` a `T?`, ad eccezione del fatto che un valore null Valued `S?` viene convertito direttamente in un valore null con valori di `T?`.

Un operatore di conversione lift ha la stessa classificazione implicita o esplicita come proprio operatore di conversione definita dall'utente sottostante. Il termine "conversione definita dall'utente" si applica all'utilizzo di entrambi gli operatori di conversione è elevato e definite dall'utente.

### <a name="evaluation-of-user-defined-conversions"></a>Valutazione delle conversioni definite dall'utente

Una conversione definita dall'utente consente di convertire un valore dal relativo tipo, denominato il ***tipo di origine***, a un altro tipo, chiamato il ***tipo di destinazione***. Interfacce di valutazione di una conversione definita dall'utente su come trovare i ***più specifica*** operatore di conversione definita dall'utente per i tipi di origine e destinazione particolari. Questo aspetto è suddiviso in diversi passaggi:

*  Trovare il set di classi e struct da cui gli operatori di conversione definita dall'utente verranno considerati. Questo set include il tipo di origine e le relative classi base e il tipo di destinazione e le relative classi base (con il presupposto che solo le classi e struct possono dichiarare gli operatori definiti dall'utente, e che i tipi non classe presentano classi base). Ai fini di questo passaggio, se il tipo di origine o di destinazione è un *nullable_type*, il tipo sottostante viene invece usato.
*  Da tale set di tipi, sono applicabili per determinare quale definite dall'utente e gli operatori di conversione è elevato. Per un operatore di conversione sia applicabile, deve essere possibile eseguire una conversione standard ([conversioni Standard](conversions.md#standard-conversions)) dal tipo di origine all'operando di tipo di operatore che deve essere possibile eseguire una conversione standard del tipo di risultato dell'operatore a cui il tipo di destinazione.
*  Dal set di operatori definiti dall'utente applicabili, individuazione dell'operatore in modo non ambiguo di più specifico. In termini generali, l'operatore più specifico è l'operatore il cui tipo di operando "più vicino" al tipo di origine e il cui tipo di risultato è "più vicino" al tipo di destinazione. Gli operatori di conversione definita dall'utente sono preferibili rispetto agli operatori di conversione elevata. Nelle sezioni seguenti sono definite esattamente le regole per la definizione dell'operatore di conversione definita dall'utente più specifico.

Dopo aver identificato un operatore di conversione definita dall'utente più specifico, l'esecuzione effettiva della conversione definita dall'utente include fino a tre passaggi:

*  In primo luogo, se necessario, eseguire una conversione standard dal tipo di origine al tipo di operando dell'operatore di conversione definita dall'utente o elevata.
*  Successivamente, richiamando l'operatore di conversione definita dall'utente o elevata per eseguire la conversione.
*  Infine, se necessario, eseguire una conversione standard dal tipo di risultato dell'operatore di conversione definita dall'utente o lift per il tipo di destinazione.

Valutazione di una conversione definita dall'utente non comporta più di un operatore di conversione definita dall'utente o elevata. In altre parole, una conversione dal tipo `S` al tipo `T` mai prima di tutto verrà eseguita una conversione definita dall'utente da `S` al `X` e quindi eseguire una conversione definita dall'utente da `X` a `T`.

Le definizioni esatte di valutazione delle conversioni implicite o esplicite definite dall'utente vengono fornite nelle sezioni seguenti. Apportare le definizioni di utilizzare le seguenti condizioni:

*  Se la conversione implicita standard ([conversioni implicite](conversions.md#standard-implicit-conversions)) esistente da un tipo `A` a un tipo `B`e se nessuno di essi `A` né `B` sono *interface_type*s, quindi `A` è detto ***incluse nel*** `B`, e `B` è detto ***encompass*** `A`.
*  Il ***tipo più inclusivo*** in un set di tipi è il tipo che comprende tutti gli altri tipi nel set. Se nessun singolo tipo comprende tutti gli altri tipi, il set non dispone di alcun tipo di portata più ampia. In altre parole, il tipo più inclusivo è il tipo "massimo" nel set, ovvero il tipo in cui ciascuno degli altri tipi può essere convertito in modo implicito.
*  Il ***tipo maggiormente*** in un set di tipi è il tipo incluso in tutti gli altri tipi nel set. Se nessun singolo tipo incluso in tutti gli altri tipi, quindi il set non presenta maggiormente tipo. In altre parole, il tipo maggiormente è il tipo "minimo" nel set, ovvero il tipo che può essere convertito in modo implicito in ognuno degli altri tipi.

### <a name="processing-of-user-defined-implicit-conversions"></a>Elaborazione delle conversioni implicite definite dall'utente

Una conversione implicita dal tipo definito dall'utente `S` al tipo `T` viene elaborato come segue:

*  Determinare i tipi `S0` e `T0`. Se `S` oppure `T` sono tipi nullable, `S0` e `T0` sono i tipi sottostanti, in caso contrario `S0` e `T0` sono uguali a `S` e `T` rispettivamente.
*  Trovare il set di tipi, `D`, da quali conversione definita dall'utente verranno considerati operatori. Questo set è costituito `S0` (se `S0` è una classe o struct), le classi di base di `S0` (se `S0` è una classe), e `T0` (se `T0` è una classe o struct).
*  Trovare il set di operatori di conversione definita dall'utente e lift applicabili `U`. Questo set è costituito dagli operatori di conversione implicita definite dall'utente e lift dichiarati nelle classi o struct in `D` che esegue la conversione da un tipo che include `S` a un tipo incluso in `T`. Se `U` è vuoto, la conversione è indefinita e si verifica un errore in fase di compilazione.
*  Trovare il tipo di origine più specifico `SX`, degli operatori in `U`:
    * Se uno qualsiasi degli operatori in `U` convertire `S`, quindi `SX` è `S`.
    * In caso contrario, `SX` è il tipo maggiormente nel set combinato dei tipi di origine degli operatori in `U`. Se esattamente uno maggiormente tipo non viene trovato, quindi la conversione è ambigua e si verifica un errore in fase di compilazione.
*  Trovare il tipo di destinazione più specifico `TX`, degli operatori in `U`:
    * Se uno qualsiasi degli operatori in `U` convertire `T`, quindi `TX` è `T`.
    * In caso contrario, `TX` è il tipo più inclusivo nel set combinato dei tipi di destinazione degli operatori in `U`. Se non viene trovato un tipo più inclusivo, quindi la conversione è ambigua e si verifica un errore in fase di compilazione.
*  Trovare l'operatore di conversione più specifico:
    * Se `U` contiene esattamente un operatore di conversione definita dall'utente che esegue la conversione da `SX` a `TX`, il valore è l'operatore di conversione più specifico.
    * In caso contrario, se `U` contiene esattamente un operatore di conversione elevata che consente di conversione `SX` a `TX`, il valore è l'operatore di conversione più specifico.
    * In caso contrario, la conversione è ambigua e si verifica un errore in fase di compilazione.
*  Infine, applicare la conversione:
    * Se `S` non è `SX`, quindi è una conversione implicita standard da `S` a `SX` viene eseguita.
    * L'operatore di conversione più specifico viene richiamata per convertire `SX` a `TX`.
    * Se `TX` non è `T`, quindi è una conversione implicita standard da `TX` a `T` viene eseguita.

### <a name="processing-of-user-defined-explicit-conversions"></a>Elaborazione di conversioni esplicite definite dall'utente

Una conversione esplicita dal tipo definito dall'utente `S` al tipo `T` viene elaborato come segue:

*  Determinare i tipi `S0` e `T0`. Se `S` oppure `T` sono tipi nullable, `S0` e `T0` sono i tipi sottostanti, in caso contrario `S0` e `T0` sono uguali a `S` e `T` rispettivamente.
*  Trovare il set di tipi, `D`, da quali conversione definita dall'utente verranno considerati operatori. Questo set è costituito `S0` (se `S0` è una classe o struct), le classi di base di `S0` (se `S0` è una classe), `T0` (se `T0` è una classe o struct) e le classi di base di `T0` (se `T0`è una classe).
*  Trovare il set di operatori di conversione definita dall'utente e lift applicabili `U`. Questo set è costituita dall'utente e lift implicita o operatori di conversione espliciti dichiarate nelle classi o struct in `D` cui eseguire la conversione da un tipo che include o è incluso in `S` a un tipo che comprende o è incluso in `T`. Se `U` è vuoto, la conversione è indefinita e si verifica un errore in fase di compilazione.
*  Trovare il tipo di origine più specifico `SX`, degli operatori in `U`:
    * Se uno qualsiasi degli operatori in `U` convertire `S`, quindi `SX` è `S`.
    * In caso contrario, se uno qualsiasi degli operatori in `U` eseguire la conversione da tipi che includono `S`, quindi `SX` è il tipo maggiormente nel set combinato dei tipi di origine di tali operatori. Se nessun maggiormente tipo possa essere disponibile, quindi la conversione è ambigua e si verifica un errore in fase di compilazione.
    * In caso contrario, `SX` è il tipo più inclusivo nel set combinato dei tipi di origine degli operatori in `U`. Se non viene trovato un tipo più inclusivo, quindi la conversione è ambigua e si verifica un errore in fase di compilazione.
*  Trovare il tipo di destinazione più specifico `TX`, degli operatori in `U`:
    * Se uno qualsiasi degli operatori in `U` convertire `T`, quindi `TX` è `T`.
    * In caso contrario, se uno qualsiasi degli operatori in `U` convertire tipi che sono inclusi `T`, quindi `TX` è il tipo più inclusivo nel set combinato dei tipi di destinazione di tali operatori. Se non viene trovato un tipo più inclusivo, quindi la conversione è ambigua e si verifica un errore in fase di compilazione.
    * In caso contrario, `TX` è il tipo maggiormente nel set combinato dei tipi di destinazione degli operatori in `U`. Se nessun maggiormente tipo possa essere disponibile, quindi la conversione è ambigua e si verifica un errore in fase di compilazione.
*  Trovare l'operatore di conversione più specifico:
    * Se `U` contiene esattamente un operatore di conversione definita dall'utente che esegue la conversione da `SX` a `TX`, il valore è l'operatore di conversione più specifico.
    * In caso contrario, se `U` contiene esattamente un operatore di conversione elevata che consente di conversione `SX` a `TX`, il valore è l'operatore di conversione più specifico.
    * In caso contrario, la conversione è ambigua e si verifica un errore in fase di compilazione.
*  Infine, applicare la conversione:
    * Se `S` non è `SX`, quindi una conversione standard esplicita da `S` a `SX` viene eseguita.
    * L'operatore di conversione definita dall'utente più specifico viene richiamata per convertire `SX` a `TX`.
    * Se `TX` non è `T`, quindi una conversione standard esplicita da `TX` a `T` viene eseguita.

## <a name="anonymous-function-conversions"></a>Conversioni delle funzioni anonime

Un' *anonymous_method_expression* oppure *lambda_expression* viene classificato come una funzione anonima ([espressioni di funzioni anonime](expressions.md#anonymous-function-expressions)). L'espressione non dispone di un tipo ma può essere convertito in modo implicito in un tipo delegato compatibile o un tipo di albero delle espressioni. In particolare, una funzione anonima `F` è compatibile con un tipo delegato `D` fornito:

*  Se `F` contiene un *anonymous_function_signature*, quindi `D` e `F` hanno lo stesso numero di parametri.
*  Se `F` non contiene un *anonymous_function_signature*, quindi `D` può avere zero o più parametri di qualsiasi tipo, purché nessun parametro con `D` ha la `out` modificatore di parametro.
*  Se `F` presenta un elenco di parametri tipizzati in modo esplicito, ogni parametro nel `D` ha lo stesso tipo e modificatori del parametro corrispondente nel `F`.
*  Se `F` è un elenco di un parametro tipizzato in modo implicito `D` non ha alcun `ref` o `out` parametri.
*  Se il corpo della `F` è un'espressione e `D` ha una `void` tipo restituito o `F` è asincrono e `D` presenta il tipo restituito `Task`, quindi quando ogni parametro di `F` viene assegnato il tipo del parametro corrispondente nel `D`, il corpo del `F` è un'espressione valida (wrt [espressioni](expressions.md)) che potrebbe essere consentito, come un *statement_expression* ([Istruzioni di espressione](statements.md#expression-statements)).
*  Se il corpo della `F` è un blocco di istruzioni e `D` ha una `void` tipo restituito o `F` è asincrono e `D` presenta il tipo restituito `Task`, quindi quando ogni parametro di `F` viene assegnato il tipo di il parametro corrispondente nel `D`, il corpo del `F` è un blocco di istruzioni valido (wrt [blocchi](statements.md#blocks)) in cui nessun `return` istruzione specifica un'espressione.
*  Se il corpo della `F` è un'espressione, e *entrambi* `F` è non asincrone e `D` presenta un tipo restituito non void `T`, *oppure* `F` è asincrono e `D` ha un tipo restituito `Task<T>`, quindi quando ogni parametro dello `F` viene assegnato il tipo del parametro corrispondente nel `D`, il corpo della `F` è un'espressione valida (wrt [ Le espressioni](expressions.md)) che è implicitamente convertibile in `T`.
*  Se il corpo della `F` è un blocco di istruzioni, e *entrambi* `F` è non asincrone e `D` presenta un tipo restituito non void `T`, *oppure* `F` è asincrono e `D` ha un tipo restituito `Task<T>`, quindi quando ogni parametro del `F` viene assegnato il tipo del parametro corrispondente nella `D`, il corpo della `F` è un blocco di istruzioni valido (wrt [blocchi ](statements.md#blocks)) con un punto di fine non raggiungibile in cui ciascun `return` istruzione specifica un'espressione che è implicitamente convertibile in `T`.

Ai fini della brevità, in questa sezione Usa la versione abbreviata per i tipi di attività `Task` e `Task<T>` ([funzioni asincrone](classes.md#async-functions)).

Un'espressione lambda `F` è compatibile con un tipo di albero delle espressioni `Expression<D>` se `F` compatibile con il tipo di delegato `D`. Si noti che questo non è applicabile ai metodi anonimi, solo le espressioni lambda.

Alcune espressioni lambda non possono essere convertiti in tipi di albero delle espressioni: Anche se la conversione *esiste*, ha esito negativo in fase di compilazione. Ciò si verifica quando l'espressione lambda:

*  È un *blocco* corpo
*  Contiene gli operatori di assegnazione semplice o composta
*  Contiene un'espressione MDX associata in modo dinamico
*  È asincrono

Gli esempi seguenti usano un tipo delegato generico `Func<A,R>` che rappresenta una funzione che accetta un argomento di tipo `A` e restituisce un valore di tipo `R`:
```csharp
delegate R Func<A,R>(A arg);
```

Nelle assegnazioni
```csharp
Func<int,int> f1 = x => x + 1;                 // Ok

Func<int,double> f2 = x => x + 1;              // Ok

Func<double,int> f3 = x => x + 1;              // Error

Func<int, Task<int>> f4 = async x => x + 1;    // Ok
```
il parametro e tipo restituito di ogni funzione anonima dipendono dal tipo della variabile a cui è assegnata alla funzione anonima.

La prima assegnazione converte correttamente la funzione anonima per il tipo delegato `Func<int,int>` perché, quando `x` è di tipo dato `int`, `x+1` è un'espressione valida che è implicitamente convertibile nel tipo `int`.

Analogamente, la seconda assegnazione converte correttamente la funzione anonima per il tipo delegato `Func<int,double>` perché il risultato del `x+1` (di tipo `int`) è implicitamente convertibile nel tipo `double`.

Tuttavia, l'assegnazione del terzo è un errore in fase di compilazione perché, quando `x` ha tipo `double`, il risultato del `x+1` (di tipo `double`) non è convertibile in modo implicito nel tipo `int`.

L'assegnazione del quarto converte correttamente la funzione async anonimi nel tipo delegato `Func<int, Task<int>>` perché il risultato del `x+1` (di tipo `int`) è implicitamente convertibile nel tipo di risultato `int` del tipo di attività `Task<int>`.

Funzioni anonime possono influenzare la risoluzione dell'overload e partecipare all'inferenza del tipo. Visualizzare [membri funzione](expressions.md#function-members) per altri dettagli.

### <a name="evaluation-of-anonymous-function-conversions-to-delegate-types"></a>Valutazione delle conversioni di tipi delegati funzioni anonime

Conversione di una funzione anonima in un tipo delegato produce un'istanza di delegato che fa riferimento alla funzione anonima e il set di variabili esterne acquisite che sono attive al momento della valutazione (eventualmente vuoto). Quando il delegato viene richiamato, il corpo della funzione anonima viene eseguito. Il codice nel corpo viene eseguito utilizzando il set di variabili esterne acquisite fa riferimento il delegato.

L'elenco chiamate di un delegato generato da una funzione anonima contiene una singola voce. L'oggetto di destinazione e metodo di destinazione del delegato non sono state specificate. In particolare, non viene specificato se è l'oggetto di destinazione del delegato `null`, il `this` valore del membro di funzione che lo contiene, o un altro oggetto.

Le conversioni delle funzioni anonime semanticamente identiche con lo stesso set (eventualmente vuota) di istanze di variabili esterne acquisiti per gli stessi tipi di delegato sono consentite (ma non obbligatorio) per restituire la stessa istanza di delegato. Il termine semanticamente identico viene usato qui per indicare che l'esecuzione delle funzioni anonime, in tutti i casi, produce gli stessi effetti ha gli stessi argomenti. Questa regola consente al codice, ad esempio il comando seguente per essere ottimizzato.

```csharp
delegate double Function(double x);

class Test
{
    static double[] Apply(double[] a, Function f) {
        double[] result = new double[a.Length];
        for (int i = 0; i < a.Length; i++) result[i] = f(a[i]);
        return result;
    }

    static void F(double[] a, double[] b) {
        a = Apply(a, (double x) => Math.Sin(x));
        b = Apply(b, (double y) => Math.Sin(y));
        ...
    }
}
```

Poiché i due delegati di funzione anonima dispongono dello stesso (vuoto) set di variabili esterne acquisite e poiché le funzioni anonime sono semanticamente identiche, il compilatore è consentito utilizzare i delegati per fare riferimento allo stesso metodo di destinazione. In effetti, il compilatore può restituire la stessa istanza di delegato da entrambe le espressioni di funzione anonima.

### <a name="evaluation-of-anonymous-function-conversions-to-expression-tree-types"></a>Valutazione delle conversioni di tipi di albero delle espressioni funzioni anonime

Conversione di una funzione anonima in un tipo di albero delle espressioni produce un albero delle espressioni ([tipi di albero delle espressioni](types.md#expression-tree-types)). Più precisamente, valutazione della conversione funzione anonima comporta la costruzione di una struttura oggetto che rappresenta la struttura della funzione anonima se stesso. La struttura esatta dell'albero delle espressioni, nonché il relativo processo di creazione, sono di tipo definito dall'implementazione.

### <a name="implementation-example"></a>Esempio di implementazione

Questa sezione illustra una possibile implementazione di conversioni delle funzioni anonime in termini di altri costrutti in c#. L'implementazione descritta di seguito si basa sugli stessi principi usati dal compilatore Microsoft C#, ma è in alcun modo un'implementazione obbligatoria e non è l'unica possibile. Solo brevemente sono indicate le conversioni in alberi delle espressioni, come la semantica esatta esulano dall'ambito di questa specifica.

Il resto di questa sezione vengono forniti vari esempi di codice che contiene funzioni anonime con caratteristiche diverse. Per ogni esempio, viene fornita una traduzione corrispondente al codice che usa solo altri costrutti in c#. Negli esempi, l'identificatore `D` viene assunto da rappresentano il tipo delegato seguenti:
```csharp
public delegate void D();
```

La forma più semplice di una funzione anonima è uno che non acquisisce alcun variabili esterne:
```csharp
class Test
{
    static void F() {
        D d = () => { Console.WriteLine("test"); };
    }
}
```

Ciò può essere convertito in un'istanza di delegato che fa riferimento a un metodo statico generato dal compilatore in cui viene inserito il codice della funzione anonima:
```csharp
class Test
{
    static void F() {
        D d = new D(__Method1);
    }

    static void __Method1() {
        Console.WriteLine("test");
    }
}
```

Nell'esempio seguente, la funzione anonima, fa riferimento a membri di istanza di `this`:
```csharp
class Test
{
    int x;

    void F() {
        D d = () => { Console.WriteLine(x); };
    }
}
```

Ciò può essere convertito in un metodo di istanza generato dal compilatore che contiene il codice della funzione anonima:
```csharp
class Test
{
    int x;

    void F() {
        D d = new D(__Method1);
    }

    void __Method1() {
        Console.WriteLine(x);
    }
}
```

In questo esempio, la funzione anonima consente di acquisire una variabile locale:
```csharp
class Test
{
    void F() {
        int y = 123;
        D d = () => { Console.WriteLine(y); };
    }
}
```

La durata della variabile locale deve ora essere estesi almeno la durata del delegato di funzione anonima. Ciò può essere ottenuto da "sottraendo" della variabile locale in un campo di una classe generata dal compilatore. Creazione di un'istanza della variabile locale ([creazione di un'istanza delle variabili locali](expressions.md#instantiation-of-local-variables)) corrisponde quindi alla creazione di un'istanza di classe generato dal compilatore e l'accesso alla variabile locale corrisponde all'accesso a un campo nell'istanza di la classe generata dal compilatore. Inoltre, la funzione anonima diventa un metodo di istanza della classe generata dal compilatore:
```csharp
class Test
{
    void F() {
        __Locals1 __locals1 = new __Locals1();
        __locals1.y = 123;
        D d = new D(__locals1.__Method1);
    }

    class __Locals1
    {
        public int y;

        public void __Method1() {
            Console.WriteLine(y);
        }
    }
}
```

Infine, l'anonimi funzione seguente acquisisce `this` , nonché due variabili locali con durate diverse:
```csharp
class Test
{
    int x;

    void F() {
        int y = 123;
        for (int i = 0; i < 10; i++) {
            int z = i * 2;
            D d = () => { Console.WriteLine(x + y + z); };
        }
    }
}
```

In questo caso, viene creata una classe di generato dal compilatore per ogni istruzione blocco in cui variabili locali vengono acquisite in modo che le variabili locali nei blocchi di diversi possono avere durate indipendenti. Un'istanza di `__Locals2`, la classe generata dal compilatore per il blocco di istruzione interna, contiene la variabile locale `z` e un campo che fa riferimento a un'istanza di `__Locals1`.  Un'istanza di `__Locals1`, la classe generata dal compilatore per il blocco di istruzione esterna, contiene la variabile locale `y` e un campo che fa riferimento a `this` del membro funzione contenitore. Con queste strutture di dati è possibile raggiungere tutte acquisite le variabili esterne tramite un'istanza di `__Local2`, e il codice della funzione anonima in questo modo può essere implementato come metodo di istanza di tale classe.

```csharp
class Test
{
    void F() {
        __Locals1 __locals1 = new __Locals1();
        __locals1.__this = this;
        __locals1.y = 123;
        for (int i = 0; i < 10; i++) {
            __Locals2 __locals2 = new __Locals2();
            __locals2.__locals1 = __locals1;
            __locals2.z = i * 2;
            D d = new D(__locals2.__Method1);
        }
    }

    class __Locals1
    {
        public Test __this;
        public int y;
    }

    class __Locals2
    {
        public __Locals1 __locals1;
        public int z;

        public void __Method1() {
            Console.WriteLine(__locals1.__this.x + __locals1.y + z);
        }
    }
}
```

La stessa tecnica applicata qui per acquisire le variabili locali può essere usata anche la conversione di funzioni anonime in alberi delle espressioni: Riferimenti agli oggetti generato dal compilatore possono essere archiviati nell'albero delle espressioni e l'accesso alle variabili locali può essere rappresentata come campo accede su tali oggetti. Il vantaggio di questo approccio è che consente le variabili locali "elevate" devono essere condivisi tra i delegati e gli alberi delle espressioni.

## <a name="method-group-conversions"></a>Conversioni dei metodi di gruppo

Una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) esistente da un gruppo di metodi ([classificazioni delle espressioni](expressions.md#expression-classifications)) a un tipo delegato compatibile. Dato un tipo delegato `D` e un'espressione `E` che viene classificato come un gruppo di metodi, esiste una conversione implicita dal `E` al `D` se `E` contiene almeno un metodo che è applicabile nel relativo (forma normale [Membro di funzione applicabile](expressions.md#applicable-function-member)) per un elenco di argomenti costruito mediante l'uso dei tipi di parametro e modificatori di `D`, come descritto di seguito.

L'applicazione in fase di compilazione di una conversione da un gruppo di metodi `E` a un tipo delegato `D` è descritta di seguito. Si noti che l'esistenza di una conversione implicita da `E` a `D` non garantisce che l'applicazione in fase di compilazione della conversione verrà completata senza errori.

*  Un singolo metodo `M` sia selezionata corrispondente a una chiamata al metodo ([chiamate al metodo](expressions.md#method-invocations)) nel formato `E(A)`, con le modifiche seguenti:
    * L'elenco di argomenti `A` è riportato un elenco di espressioni, ognuno classificate come una variabile e con il tipo e il modificatore (`ref` oppure `out`) del parametro corrispondente nel *formal_parameter_list* del `D`.
    * I metodi candidato considerati sono solo quelli che sono applicabili in base al formato normale ([membro di funzione applicabile](expressions.md#applicable-function-member)), non quelle applicabili solo in forma espansa.
*  Se l'algoritmo di [chiamate al metodo](expressions.md#method-invocations) genera un errore, quindi si verifica un errore in fase di compilazione. In caso contrario, l'algoritmo produce un singolo metodo ottimo `M` aventi lo stesso numero di parametri come `D` e la conversione è considerata presente.
*  Il metodo selezionato `M` devono essere compatibili ([compatibilità dei delegati](delegates.md#delegate-compatibility)) con il tipo di delegato `D`, oppure in caso contrario, si è verificato un errore in fase di compilazione.
*  Se il metodo selezionato `M` è un metodo di istanza, l'espressione dell'istanza associata `E` determina l'oggetto di destinazione del delegato.
*  Se il metodo selezionato M è un metodo di estensione che è identificato per mezzo di un accesso ai membri in un'espressione dell'istanza, tale espressione dell'istanza determina l'oggetto di destinazione del delegato.
*  Il risultato della conversione è un valore di tipo `D`, vale a dire un delegato appena creato che fa riferimento all'oggetto di destinazione e metodo selezionato.
*  Si noti che questo processo può portare alla creazione di un delegato a un metodo di estensione, se l'algoritmo di [chiamate al metodo](expressions.md#method-invocations) non riesce a trovare un metodo di istanza, ma ha esito positivo nell'elaborazione della chiamata di `E(A)` come un'estensione chiamata al metodo ([chiamate al metodo di estensione](expressions.md#extension-method-invocations)). Consente di acquisire un delegato creato in questo modo il metodo di estensione così come primo argomento.

L'esempio seguente illustra le conversioni dei gruppi di metodo:
```csharp
delegate string D1(object o);

delegate object D2(string s);

delegate object D3();

delegate string D4(object o, params object[] a);

delegate string D5(int i);

class Test
{
    static string F(object o) {...}

    static void G() {
        D1 d1 = F;            // Ok
        D2 d2 = F;            // Ok
        D3 d3 = F;            // Error -- not applicable
        D4 d4 = F;            // Error -- not applicable in normal form
        D5 d5 = F;            // Error -- applicable but not compatible

    }
}
```

L'assegnazione al `d1` converte in modo implicito il gruppo di metodi `F` su un valore di tipo `D1`.

L'assegnazione al `d2` Mostra come è possibile creare un delegato a un metodo che dispone di tipi di parametro meno derivati (controvarianti) e un tipo più derivato (covariante restituito).

L'assegnazione al `d3` Mostra come non esiste alcuna conversione se il metodo non è applicabile.

L'assegnazione al `d4` viene illustrato come il metodo deve essere applicabile in forma normale.

L'assegnazione al `d5` Mostra come parametro e tipo restituito del metodo del delegato è consentiti a differire solo per i tipi di riferimento.

Come con tutte le altre conversioni implicite ed esplicite, l'operatore di cast è utilizzabile per eseguire in modo esplicito la conversione di un gruppo di metodi. Di conseguenza, l'esempio
```csharp
object obj = new EventHandler(myDialog.OkClick);
```
è invece possibile scrivere
```csharp
object obj = (EventHandler)myDialog.OkClick;
```

I gruppi di metodi potrebbero influenzare la risoluzione dell'overload e partecipare all'inferenza del tipo. Visualizzare [membri funzione](expressions.md#function-members) per altri dettagli.

La valutazione in fase di esecuzione della conversione di un gruppo di metodi procede nel modo seguente:

*  Se il metodo selezionato in fase di compilazione è un metodo di istanza o è un metodo di estensione che viene eseguito come un metodo di istanza, l'oggetto di destinazione del delegato è determinato dall'espressione dell'istanza associata `E`:
    * Viene valutata l'espressione dell'istanza. Se questa versione di valutazione causa un'eccezione, non verrà eseguito alcun ulteriore passaggio.
    * Se l'espressione dell'istanza di un *reference_type*, il valore calcolato per l'espressione dell'istanza diventa l'oggetto di destinazione. Se il metodo selezionato è un metodo di istanza e l'oggetto di destinazione è `null`, un `System.NullReferenceException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.
    * Se l'espressione dell'istanza di un *value_type*, un'operazione di conversione boxing ([conversioni Boxing](types.md#boxing-conversions)) viene eseguita per convertire il valore in un oggetto, e tale oggetto diventa l'oggetto di destinazione.
*  In caso contrario, il metodo selezionato fa parte di una chiamata al metodo statico e l'oggetto di destinazione del delegato è `null`.
*  Una nuova istanza del tipo delegato `D` viene allocato. Se non è disponibile memoria sufficiente per allocare la nuova istanza, una `System.OutOfMemoryException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.
*  La nuova istanza di delegato viene inizializzata con un riferimento al metodo che è stato determinato in fase di compilazione e un riferimento all'oggetto di destinazione viene calcolato in precedenza.
