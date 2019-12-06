---
ms.openlocfilehash: 4d6d28a3127bc701867afe157aa5496377a06f69
ms.sourcegitcommit: 63d276488c9770a565fd787020783ffc1d2af9d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74868004"
---
# <a name="conversions"></a>Conversioni

Una ***conversione*** consente a un'espressione di essere considerata come un tipo particolare. Una conversione può causare che un'espressione di un tipo specificato venga considerata come avente un tipo diverso oppure può causare un'espressione senza un tipo per ottenere un tipo. Le conversioni possono essere ***implicite*** o ***esplicite***e determinare se è necessario un cast esplicito. Ad esempio, la conversione dal tipo `int` al tipo `long` è implicita, quindi le espressioni di tipo `int` possono essere considerate in modo implicito come tipo `long`. La conversione opposta, dal tipo `long` al tipo `int`, è esplicita ed è quindi necessario un cast esplicito.

```csharp
int a = 123;
long b = a;         // implicit conversion from int to long
int c = (int) b;    // explicit conversion from long to int
```

Alcune conversioni sono definite dal linguaggio. I programmi possono anche definire conversioni personalizzate ([conversioni definite dall'utente](conversions.md#user-defined-conversions)).

## <a name="implicit-conversions"></a>Conversioni implicite

Le conversioni seguenti sono classificate come conversioni implicite:

*  Conversioni di identità
*  Conversioni numeriche implicite
*  Conversioni di enumerazione implicite
*  Conversioni implicite di stringhe interpolate
*  Conversioni implicite Nullable
*  Conversioni di valori letterali null
*  Conversioni di riferimenti implicite
*  Conversioni boxing
*  Conversioni dinamiche implicite
*  Conversioni implicite di espressioni costanti
*  Conversioni implicite definite dall'utente
*  Conversioni di funzioni anonime
*  Conversioni di gruppi di metodi

Le conversioni implicite possono verificarsi in varie situazioni, incluse le chiamate ai membri della funzione ([controllo della fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), le espressioni cast ([espressioni cast](expressions.md#cast-expressions)) e le assegnazioni ([operatori di assegnazione](expressions.md#assignment-operators)).

Le conversioni implicite predefinite hanno sempre esito positivo e non generano mai eccezioni. Le conversioni implicite definite dall'utente progettate correttamente devono presentare anche queste caratteristiche.

Ai fini della conversione, i tipi `object` e `dynamic` sono considerati equivalenti.

Tuttavia, le conversioni dinamiche (le conversioni dinamiche[implicite](conversions.md#implicit-dynamic-conversions) e le [conversioni dinamiche esplicite](conversions.md#explicit-dynamic-conversions)) si applicano solo alle espressioni di tipo `dynamic` ([il tipo dinamico](types.md#the-dynamic-type)).

### <a name="identity-conversion"></a>Conversione di identità

Una conversione di identità converte da qualsiasi tipo nello stesso tipo. Questa conversione esiste in modo che un'entità che dispone già di un tipo obbligatorio possa essere convertita in tale tipo.

*  Poiché `object` e `dynamic` sono considerati equivalenti, esiste una conversione di identità tra `object` e `dynamic`e tra tipi costruiti identici quando si sostituiscono tutte le occorrenze di `dynamic` con `object`.

### <a name="implicit-numeric-conversions"></a>Conversioni numeriche implicite

Le conversioni numeriche implicite sono:

*  Da `sbyte` a `short`, `int`, `long`, `float`, `double`o `decimal`.
*  Da `byte` a `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `float`, `double`o `decimal`.
*  Da `short` a `int`, `long`, `float`, `double`o `decimal`.
*  Da `ushort` a `int`, `uint`, `long`, `ulong`, `float`, `double`o `decimal`.
*  Da `int` a `long`, `float`, `double`o `decimal`.
*  Da `uint` a `long`, `ulong`, `float`, `double`o `decimal`.
*  Da `long` a `float`, `double`o `decimal`.
*  Da `ulong` a `float`, `double`o `decimal`.
*  Da `char` a `ushort`, `int`, `uint`, `long`, `ulong`, `float`, `double`o `decimal`.
*  Da `float` a `double`.

Le conversioni da `int`, `uint`, `long`o `ulong` a `float` e da `long` o `ulong` a `double` possono causare una perdita di precisione, ma non provocheranno mai una perdita di grandezza. Le altre conversioni numeriche implicite non perdono mai le informazioni.

Non esistono conversioni implicite al tipo `char`, pertanto i valori degli altri tipi integrali non vengono convertiti automaticamente nel tipo di `char`.

### <a name="implicit-enumeration-conversions"></a>Conversioni di enumerazione implicite

Una conversione di enumerazione implicita consente di convertire il *decimal_integer_literal* `0` in qualsiasi *enum_type* e in qualsiasi *nullable_type* il cui tipo sottostante è un *enum_type*. Nel secondo caso la conversione viene valutata mediante la conversione nel *enum_type* sottostante e il wrapping del risultato ([tipi nullable](types.md#nullable-types)).

### <a name="implicit-interpolated-string-conversions"></a>Conversioni implicite di stringhe interpolate

Una conversione implicita di stringa interpolata consente di convertire un *interpolated_string_expression* ([stringhe interpolate](expressions.md#interpolated-strings)) in `System.IFormattable` o `System.FormattableString` (che implementa `System.IFormattable`).

Quando questa conversione viene applicata, un valore stringa non è composto dalla stringa interpolata. Viene invece creata un'istanza di `System.FormattableString`, come descritto in [stringhe interpolate](expressions.md#interpolated-strings).

### <a name="implicit-nullable-conversions"></a>Conversioni implicite Nullable

Le conversioni implicite predefinite che operano su tipi di valore non nullable possono essere utilizzate anche con forme Nullable di tali tipi. Per ognuna delle conversioni numeriche implicite e di identità predefinite che convertono da un tipo di valore non nullable `S` a un tipo di valore non nullable `T`, esistono le seguenti conversioni implicite Nullable:

*  Conversione implicita da `S?` a `T?`.
*  Conversione implicita da `S` a `T?`.

La valutazione di una conversione Nullable implicita basata su una conversione sottostante da `S` a `T` procede come segue:

*  Se la conversione Nullable è da `S?` a `T?`:
    * Se il valore di origine è null (`HasValue` proprietà è false), il risultato è il valore null di tipo `T?`.
    * In caso contrario, la conversione viene valutata come annullamento del wrapping da `S?` a `S`, seguito dalla conversione sottostante da `S` a `T`, seguito da un wrapping ([tipi nullable](types.md#nullable-types)) da `T` a `T?`.

*  Se la conversione Nullable è da `S` a `T?`, la conversione viene valutata come la conversione sottostante da `S` a `T` seguito da un wrapping da `T` a `T?`.

### <a name="null-literal-conversions"></a>Conversioni di valori letterali null

Esiste una conversione implicita dal valore letterale `null` a qualsiasi tipo Nullable. Questa conversione produce il valore null ([tipi nullable](types.md#nullable-types)) del tipo nullable specificato.

### <a name="implicit-reference-conversions"></a>Conversioni di riferimenti implicite

Le conversioni implicite dei riferimenti sono:

*  Da qualsiasi *reference_type* `object` e `dynamic`.
*  Da qualsiasi *class_type* `S` a qualsiasi *class_type* `T`, fornito `S` viene derivato da `T`.
*  Da qualsiasi *class_type* `S` a qualsiasi *INTERFACE_TYPE* `T`, fornito `S` implementa `T`.
*  Da qualsiasi *interface_type* `S` a qualsiasi *INTERFACE_TYPE* `T`, fornito `S` viene derivato da `T`.
*  Da un *array_type* `S` con un tipo di elemento `SE` a un *array_type* `T` con un tipo di elemento `TE`, purché siano soddisfatte tutte le condizioni seguenti:
    * `S` e `T` differiscono solo per il tipo di elemento. In altre parole, `S` e `T` hanno lo stesso numero di dimensioni.
    * `SE` e `TE` sono *reference_type*s.
    * Una conversione implicita di riferimento esiste dal `SE` al `TE`.
*  Da qualsiasi *array_type* `System.Array` e dalle interfacce che implementa.
*  Da un tipo di matrice unidimensionale `S[]` `System.Collections.Generic.IList<T>` e le relative interfacce di base, purché sia presente un'identità implicita o una conversione di riferimenti da `S` a `T`.
*  Da qualsiasi *delegate_type* `System.Delegate` e dalle interfacce che implementa.
*  Dal valore letterale null a qualsiasi *reference_type*.
*  Da qualsiasi *reference_type* a un *reference_type* `T` se è presente un'identità implicita o una conversione di riferimenti a un `T0` *reference_type* e `T0` dispone di una conversione di identità in `T`.
*  Da qualsiasi *reference_type* a un tipo di interfaccia o delegato `T` se è presente un'identità implicita o una conversione di un riferimento a un tipo di interfaccia o delegato `T0` e `T0` è una conversione della varianza ([conversione della varianza](interfaces.md#variance-conversion)) a `T`.
*  Conversioni implicite che coinvolgono parametri di tipo noti come tipi di riferimento. Per ulteriori informazioni sulle conversioni implicite che coinvolgono parametri di tipo, vedere [conversioni implicite che coinvolgono parametri di tipo](conversions.md#implicit-conversions-involving-type-parameters) .

Le conversioni implicite dei riferimenti sono quelle conversioni tra *reference_type*s che è possibile dimostrare di avere sempre esito positivo e pertanto non richiedono alcun controllo in fase di esecuzione.

Le conversioni dei riferimenti, implicite o esplicite, non modificano mai l'identità referenziale dell'oggetto da convertire. In altre parole, mentre una conversione di riferimento può modificare il tipo di riferimento, non modifica mai il tipo o il valore dell'oggetto a cui si fa riferimento.

### <a name="boxing-conversions"></a>Conversioni boxing

Una conversione boxing consente a un *value_type* di essere convertito in modo implicito in un tipo di riferimento. Esiste una conversione boxing da qualsiasi *non_nullable_value_type* per `object` e `dynamic`, per `System.ValueType` e per qualsiasi *interface_type* implementato dal *non_nullable_value_type*. Inoltre, un *enum_type* può essere convertito nel tipo `System.Enum`.

Una conversione boxing esiste da un *nullable_type* a un tipo riferimento, se e solo se esiste una conversione boxing dall' *non_nullable_value_type* sottostante al tipo di riferimento.

Un tipo di valore ha una conversione boxing in un tipo di interfaccia `I` se è presente una conversione boxing in un tipo di interfaccia `I0` e `I0` ha una conversione di identità in `I`.

Un tipo di valore ha una conversione boxing in un tipo di interfaccia `I` se è presente una conversione boxing in un tipo di interfaccia o delegato `I0` e `I0` è una conversione della varianza ([conversione della varianza](interfaces.md#variance-conversion)) a `I`.

La conversione boxing di un valore *non_nullable_value_type* consiste nell'allocazione di un'istanza dell'oggetto e nella copia del valore *value_type* in tale istanza. È possibile eseguire il Boxing di uno struct al tipo `System.ValueType`, perché si tratta di una classe di base per tutti gli struct ([ereditarietà](structs.md#inheritance)).

Il Boxing di un valore di un *nullable_type* procede come segue:

*  Se il valore di origine è null (`HasValue` proprietà è false), il risultato è un riferimento null del tipo di destinazione.
*  In caso contrario, il risultato è un riferimento a un `T` boxed prodotto dalla rimozione del wrapping e dalla conversione boxing del valore di origine.

Le conversioni boxing sono descritte ulteriormente nelle [conversioni boxing](types.md#boxing-conversions).

### <a name="implicit-dynamic-conversions"></a>Conversioni dinamiche implicite

Una conversione dinamica implicita esiste da un'espressione di tipo `dynamic` a qualsiasi tipo `T`. La conversione è associata dinamicamente ([associazione dinamica](expressions.md#dynamic-binding)), il che significa che una conversione implicita verrà cercata in fase di esecuzione dal tipo in fase di esecuzione dell'espressione `T`. Se non viene trovata alcuna conversione, viene generata un'eccezione in fase di esecuzione.

Si noti che questa conversione implicita viola apparentemente il Consiglio all'inizio di [conversioni implicite](conversions.md#implicit-conversions) che una conversione implicita non deve mai generare un'eccezione. Tuttavia, non è la conversione stessa, ma la *ricerca* della conversione che genera l'eccezione. Il rischio di eccezioni in fase di esecuzione è intrinseco all'utilizzo dell'associazione dinamica. Se non si desidera un binding dinamico della conversione, l'espressione può essere prima convertita in `object`e quindi nel tipo desiderato.

Nell'esempio seguente vengono illustrate le conversioni dinamiche implicite:

```csharp
object o  = "object"
dynamic d = "dynamic";

string s1 = o; // Fails at compile-time -- no conversion exists
string s2 = d; // Compiles and succeeds at run-time
int i     = d; // Compiles but fails at run-time -- no conversion exists
```

Le assegnazioni per `s2` e `i` entrambi utilizzano conversioni dinamiche implicite, in cui l'associazione delle operazioni viene sospesa fino alla fase di esecuzione. In fase di esecuzione, le conversioni implicite vengono cercate dal tipo in fase di esecuzione `d` -- `string`, al tipo di destinazione. È stata rilevata una conversione da `string` ma non da `int`.

### <a name="implicit-constant-expression-conversions"></a>Conversioni implicite di espressioni costanti

Una conversione implicita di espressioni costanti consente le conversioni seguenti:

*  Un *constant_expression* ([espressioni costanti](expressions.md#constant-expressions)) di tipo `int` può essere convertito nel tipo `sbyte`, `byte`, `short`, `ushort`, `uint`o `ulong`, a condizione che il valore del *constant_expression* sia compreso nell'intervallo del tipo di destinazione.
*  Un *constant_expression* di tipo `long` può essere convertito nel tipo `ulong`, purché il valore del *constant_expression* non sia negativo.

### <a name="implicit-conversions-involving-type-parameters"></a>Conversioni implicite che coinvolgono parametri di tipo

Per un parametro di tipo specificato esistono le conversioni implicite seguenti `T`:

*  Da `T` alla classe base effettiva `C`, da `T` a qualsiasi classe di base di `C`e da `T` a qualsiasi interfaccia implementata da `C`. In fase di esecuzione, se `T` è un tipo di valore, la conversione viene eseguita come conversione boxing. In caso contrario, la conversione viene eseguita come conversione di un riferimento implicito o conversione di identità.
*  Da `T` a un tipo di interfaccia `I` nel set di interfacce effettivo `T`e da `T` a qualsiasi interfaccia di base di `I`. In fase di esecuzione, se `T` è un tipo di valore, la conversione viene eseguita come conversione boxing. In caso contrario, la conversione viene eseguita come conversione di un riferimento implicito o conversione di identità.
*  Da `T` a un parametro di tipo `U`, fornito `T` dipende da `U` ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)). In fase di esecuzione, se `U` è un tipo di valore, `T` e `U` sono necessariamente dello stesso tipo e non viene eseguita alcuna conversione. In caso contrario, se `T` è un tipo valore, la conversione viene eseguita come conversione boxing. In caso contrario, la conversione viene eseguita come conversione di un riferimento implicito o conversione di identità.
*  Dal valore letterale null a `T`, fornito `T` è noto come tipo di riferimento.
*  Da `T` a un tipo di riferimento `I` se dispone di una conversione implicita in un tipo riferimento `S0` e `S0` ha una conversione di identità in `S`. In fase di esecuzione la conversione viene eseguita in modo analogo alla conversione in `S0`.
*  Da `T` a un tipo di interfaccia `I` se dispone di una conversione implicita in un tipo di interfaccia o delegato `I0` e `I0` è una varianza convertibile in `I` ([conversione della varianza](interfaces.md#variance-conversion)). In fase di esecuzione, se `T` è un tipo di valore, la conversione viene eseguita come conversione boxing. In caso contrario, la conversione viene eseguita come conversione di un riferimento implicito o conversione di identità.

Se `T` è noto come tipo di riferimento ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)), le conversioni precedenti vengono tutte classificate come conversioni implicite dei riferimenti (conversioni di[riferimenti implicite](conversions.md#implicit-reference-conversions)). Se `T` non è noto come tipo di riferimento, le conversioni precedenti vengono classificate come conversioni boxing (conversioni[Boxing](conversions.md#boxing-conversions)).

### <a name="user-defined-implicit-conversions"></a>Conversioni implicite definite dall'utente

Una conversione implicita definita dall'utente è costituita da una conversione implicita standard facoltativa, seguita dall'esecuzione di un operatore di conversione implicita definito dall'utente, seguita da un'altra conversione implicita standard facoltativa. Le regole esatte per la valutazione delle conversioni implicite definite dall'utente sono descritte in [elaborazione di conversioni implicite definite dall'utente](conversions.md#processing-of-user-defined-implicit-conversions).

### <a name="anonymous-function-conversions-and-method-group-conversions"></a>Conversioni di funzioni anonime e conversioni di gruppi di metodi

Le funzioni anonime e i gruppi di metodi non hanno tipi in e di se stessi, ma possono essere convertiti in modo implicito in tipi delegati o tipi di alberi delle espressioni. Le conversioni di funzioni anonime sono descritte in modo più dettagliato nelle conversioni di [funzioni anonime](conversions.md#anonymous-function-conversions) e nelle conversioni di gruppi di metodi nelle [conversioni dei gruppi di metodi](conversions.md#method-group-conversions).

## <a name="explicit-conversions"></a>Conversioni esplicite

Le conversioni seguenti sono classificate come conversioni esplicite:

*  Tutte le conversioni implicite.
*  Conversioni numeriche esplicite.
*  Conversioni esplicite dell'enumerazione.
*  Conversioni esplicite Nullable.
*  Conversioni esplicite dei riferimenti.
*  Conversioni esplicite dell'interfaccia.
*  Conversione unboxing.
*  Conversioni dinamiche esplicite
*  Conversioni esplicite definite dall'utente.

Le conversioni esplicite possono verificarsi nelle espressioni cast ([espressioni cast](expressions.md#cast-expressions)).

Il set di conversioni esplicite include tutte le conversioni implicite. Ciò significa che sono consentite espressioni cast ridondanti.

Le conversioni esplicite che non sono conversioni implicite sono conversioni che non possono essere verificate per sempre esito positivo, le conversioni che potrebbero perdere informazioni e le conversioni tra domini di tipi sufficientemente diversi da Merit esplicito notazione.

### <a name="explicit-numeric-conversions"></a>Conversioni numeriche esplicite

Le conversioni numeriche esplicite sono le conversioni da un *numeric_type* a un altro *numeric_type* per cui non esiste già una conversione numerica implicita ([conversioni numeriche implicite](conversions.md#implicit-numeric-conversions)):

*  Da `sbyte` a `byte`, `ushort`, `uint`, `ulong`o `char`.
*  Da `byte` a `sbyte` e `char`.
*  Da `short` a `sbyte`, `byte`, `ushort`, `uint`, `ulong`o `char`.
*  Da `ushort` a `sbyte`, `byte`, `short`o `char`.
*  Da `int` a `sbyte`, `byte`, `short`, `ushort`, `uint`, `ulong`o `char`.
*  Da `uint` a `sbyte`, `byte`, `short`, `ushort`, `int`o `char`.
*  Da `long` a `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `ulong`o `char`.
*  Da `ulong` a `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`o `char`.
*  Da `char` a `sbyte`, `byte`o `short`.
*  Da `float` a `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`o `decimal`.
*  Da `double` a `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`o `decimal`.
*  Da `decimal` a `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`o `double`.

Poiché le conversioni esplicite includono tutte le conversioni numeriche implicite ed esplicite, è sempre possibile eseguire la conversione da qualsiasi *numeric_type* a qualsiasi altra *numeric_type* utilizzando un'espressione cast ([espressioni cast](expressions.md#cast-expressions)).

Le conversioni numeriche esplicite potrebbero perdere informazioni o causare la generazione di eccezioni. Una conversione numerica esplicita viene elaborata come segue:

*  Per una conversione da un tipo integrale a un altro tipo integrale, l'elaborazione dipende dal contesto di controllo dell'overflow ([gli operatori checked e unchecked](expressions.md#the-checked-and-unchecked-operators)) in cui viene eseguita la conversione:
    * In un contesto di `checked` la conversione ha esito positivo se il valore dell'operando di origine è compreso nell'intervallo del tipo di destinazione, ma genera un'`System.OverflowException` se il valore dell'operando di origine non è compreso nell'intervallo del tipo di destinazione.
    * In un contesto `unchecked` la conversione ha sempre esito positivo e procede come segue.
        * Se il tipo di origine è maggiore del tipo di destinazione, il valore di origine viene troncato rimuovendo i relativi "extra" bit più rilevanti. Il risultato viene quindi trattato come un valore del tipo di destinazione.
        * Se il tipo di origine è inferiore al tipo di destinazione, il valore di origine ha estensione firma o estensione zero in modo da avere le stesse dimensioni del tipo di destinazione. L'estensione firma viene usata se il tipo di origine dispone della firma; l'estensione zero viene usata se il tipo di origine è privo di firma. Il risultato viene quindi trattato come un valore del tipo di destinazione.
        * Se il tipo di origine ha le stesse dimensioni del tipo di destinazione, il valore di origine viene considerato un valore del tipo di destinazione.
*  Per una conversione da `decimal` a un tipo integrale, il valore di origine viene arrotondato per eccesso al valore integrale più vicino e questo valore integrale diventa il risultato della conversione. Se il valore integrale risultante non rientra nell'intervallo del tipo di destinazione, viene generata un'`System.OverflowException`.
*  Per una conversione da `float` o `double` a un tipo integrale, l'elaborazione dipende dal contesto di controllo dell'overflow ([gli operatori checked e unchecked](expressions.md#the-checked-and-unchecked-operators)) in cui viene eseguita la conversione:
    * In un contesto di `checked` la conversione procede come segue:
        * Se il valore dell'operando è NaN o infinite, viene generata un'`System.OverflowException`.
        * In caso contrario, l'operando di origine viene arrotondato verso lo zero al valore integrale più vicino. Se questo valore integrale è compreso nell'intervallo del tipo di destinazione, questo valore è il risultato della conversione.
        * In caso contrario viene generata un'eccezione `System.OverflowException`.
    * In un contesto `unchecked` la conversione ha sempre esito positivo e procede come segue.
        * Se il valore dell'operando è NaN o infinito, il risultato della conversione è un valore non specificato del tipo di destinazione.
        * In caso contrario, l'operando di origine viene arrotondato verso lo zero al valore integrale più vicino. Se questo valore integrale è compreso nell'intervallo del tipo di destinazione, questo valore è il risultato della conversione.
        * In caso contrario, il risultato della conversione è un valore non specificato del tipo di destinazione.
*  Per una conversione da `double` a `float`, il valore del `double` viene arrotondato al valore `float` più vicino. Se il valore `double` è troppo piccolo per essere rappresentato come `float`, il risultato diventa zero positivo o negativo. Se il valore `double` è troppo grande per essere rappresentato come `float`, il risultato diventa un infinito positivo o un infinito negativo. Se il valore `double` è NaN, anche il risultato è NaN.
*  Per una conversione da `float` o da `double` a `decimal`, il valore di origine viene convertito in `decimal` rappresentazione e arrotondato al numero più vicino dopo la posizione decimale 28, se necessario ([tipo decimale](types.md#the-decimal-type)). Se il valore di origine è troppo piccolo per essere rappresentato come `decimal`, il risultato diventa zero. Se il valore di origine è NaN, infinito o troppo grande per essere rappresentato come `decimal`, viene generata un'`System.OverflowException`.
*  Per una conversione da `decimal` a `float` o `double`, il valore `decimal` viene arrotondato al valore `double` o `float` più vicino. Sebbene questa conversione possa perdere precisione, non genera mai un'eccezione.

### <a name="explicit-enumeration-conversions"></a>Conversioni esplicite dell'enumerazione

Le conversioni esplicite dell'enumerazione sono:

*  Da `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`o `decimal` a qualsiasi *enum_type*.
*  Da qualsiasi *enum_type* a `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`o `decimal`.
*  Da qualsiasi *enum_type* a qualsiasi altra *enum_type*.

Una conversione esplicita dell'enumerazione tra due tipi viene elaborata trattando qualsiasi *enum_type* partecipante come tipo sottostante di tale *enum_type*e quindi eseguendo una conversione numerica implicita o esplicita tra i tipi risultanti. Ad esempio, dato un *enum_type* `E` con e il tipo sottostante di `int`, una conversione da `E` a `byte` viene elaborata come conversione numerica esplicita ([conversioni numeriche esplicite](conversions.md#explicit-numeric-conversions)) da `int` a `byte`e una conversione da `byte` a `E` viene elaborata come conversione numerica implicita ([conversioni numeriche implicite](conversions.md#implicit-numeric-conversions)) da `byte` a `int`.

### <a name="explicit-nullable-conversions"></a>Conversioni esplicite Nullable

Le ***conversioni esplicite Nullable*** consentono conversioni esplicite predefinite che operano su tipi di valore non nullable per essere utilizzate anche con forme Nullable di tali tipi. Per ognuna delle conversioni esplicite predefinite che consentono di eseguire la conversione da un tipo di valore non nullable `S` a un tipo di valore non nullable `T` ([conversione di identità](conversions.md#identity-conversion), [conversioni numeriche implicite](conversions.md#implicit-numeric-conversions), [conversioni di enumerazione implicite](conversions.md#implicit-enumeration-conversions), conversioni [numeriche esplicite](conversions.md#explicit-numeric-conversions)e [conversioni esplicite dell'enumerazione](conversions.md#explicit-enumeration-conversions)), esistono le seguenti conversioni Nullable:

*  Conversione esplicita da `S?` a `T?`.
*  Conversione esplicita da `S` a `T?`.
*  Conversione esplicita da `S?` a `T`.

La valutazione di una conversione Nullable basata su una conversione sottostante da `S` a `T` procede come segue:

*  Se la conversione Nullable è da `S?` a `T?`:
    * Se il valore di origine è null (`HasValue` proprietà è false), il risultato è il valore null di tipo `T?`.
    * In caso contrario, la conversione viene valutata come annullamento del wrapping da `S?` a `S`, seguito dalla conversione sottostante da `S` a `T`, seguito da un wrapping da `T` a `T?`.
*  Se la conversione Nullable è da `S` a `T?`, la conversione viene valutata come la conversione sottostante da `S` a `T` seguito da un wrapping da `T` a `T?`.
*  Se la conversione Nullable è da `S?` a `T`, la conversione viene valutata come annullamento del wrapping da `S?` a `S` seguito dalla conversione sottostante da `S` a `T`.

Si noti che un tentativo di annullare il wrapping di un valore Nullable genererà un'eccezione se il valore è `null`.

### <a name="explicit-reference-conversions"></a>Conversioni esplicite dei riferimenti

Le conversioni esplicite dei riferimenti sono:

*  Da `object` e `dynamic` ad altri *reference_type*.
*  Da qualsiasi *class_type* `S` a qualsiasi *class_type* `T`, fornito `S` è una classe di base di `T`.
*  Da qualsiasi *class_type* `S` a qualsiasi *INTERFACE_TYPE* `T`, fornito `S` non è sealed e fornito `S` non implementa `T`.
*  Da qualsiasi *interface_type* `S` a qualsiasi *class_type* `T`, fornito `T` non è sealed o fornito `T` implementa `S`.
*  Da qualsiasi *interface_type* `S` a qualsiasi *INTERFACE_TYPE* `T`, fornito `S` non è derivato da `T`.
*  Da un *array_type* `S` con un tipo di elemento `SE` a un *array_type* `T` con un tipo di elemento `TE`, purché siano soddisfatte tutte le condizioni seguenti:
    * `S` e `T` differiscono solo per il tipo di elemento. In altre parole, `S` e `T` hanno lo stesso numero di dimensioni.
    * `SE` e `TE` sono *reference_type*s.
    * Una conversione esplicita del riferimento esiste dal `SE` al `TE`.
*  Da `System.Array` e dalle interfacce implementate in qualsiasi *array_type*.
*  Da un tipo di matrice unidimensionale `S[]` `System.Collections.Generic.IList<T>` e le interfacce di base, purché sia presente una conversione esplicita del riferimento da `S` a `T`.
*  Da `System.Collections.Generic.IList<S>` e dalle relative interfacce di base a un tipo di matrice unidimensionale `T[]`, a condizione che esista un'identità esplicita o una conversione di riferimenti da `S` a `T`.
*  Da `System.Delegate` e dalle interfacce implementate in qualsiasi *delegate_type*.
*  Da un tipo riferimento a un tipo riferimento `T` se dispone di una conversione esplicita di un riferimento a un tipo di riferimento `T0` e `T0` ha una conversione di identità `T`.
*  Da un tipo di riferimento a un tipo di interfaccia o delegato `T` se è presente una conversione esplicita del riferimento a un'interfaccia o a un tipo di delegato `T0` e `T0` è la conversione della varianza in `T` o `T` è la conversione della varianza a `T0` ([conversione della varianza](interfaces.md#variance-conversion)).
*  Da `D<S1...Sn>` a `D<T1...Tn>` dove `D<X1...Xn>` è un tipo delegato generico, `D<S1...Sn>` non è compatibile con o è identico a `D<T1...Tn>`e per ogni parametro di tipo `Xi` di `D` il seguente:
    * Se `Xi` è invariante, `Si` è identico a `Ti`.
    * Se `Xi` è covariante, esiste un'identità implicita o esplicita o una conversione di riferimenti da `Si` a `Ti`.
    * Se `Xi` è controvariante, `Si` e `Ti` sono entrambi tipi di riferimento o identici.
*  Conversioni esplicite che coinvolgono parametri di tipo noti come tipi di riferimento. Per ulteriori informazioni sulle conversioni esplicite che coinvolgono parametri di tipo, vedere [conversioni esplicite che coinvolgono parametri di tipo](conversions.md#explicit-conversions-involving-type-parameters).

Le conversioni esplicite dei riferimenti sono quelle conversioni tra i tipi di riferimento che richiedono controlli run-time per assicurarsi che siano corrette.

Affinché una conversione esplicita di un riferimento abbia esito positivo in fase di esecuzione, il valore dell'operando di origine deve essere `null`oppure il tipo effettivo dell'oggetto a cui fa riferimento l'operando di origine deve essere un tipo che può essere convertito nel tipo di destinazione mediante una conversione implicita di riferimento ([conversioni di riferimenti implicite](conversions.md#implicit-reference-conversions)) o conversione boxing ([conversioni boxing](conversions.md#boxing-conversions)). Se una conversione esplicita del riferimento non riesce, viene generata un'`System.InvalidCastException`.

Le conversioni dei riferimenti, implicite o esplicite, non modificano mai l'identità referenziale dell'oggetto da convertire. In altre parole, mentre una conversione di riferimento può modificare il tipo di riferimento, non modifica mai il tipo o il valore dell'oggetto a cui si fa riferimento.

### <a name="unboxing-conversions"></a>Conversione unboxing

Una conversione unboxing consente la conversione esplicita di un tipo di riferimento in un *value_type*. Esiste una conversione unboxing dai tipi `object`, `dynamic` e `System.ValueType` a qualsiasi *non_nullable_value_type*e da qualsiasi *interface_type* a qualsiasi *non_nullable_value_type* che implementi la *INTERFACE_TYPE*. Inoltre, il tipo `System.Enum` può essere unboxed per qualsiasi *enum_type*.

Una conversione unboxing esiste da un tipo di riferimento a un *nullable_type* se esiste una conversione unboxing dal tipo di riferimento al *non_nullable_value_type* sottostante della *nullable_type*.

Un tipo di valore `S` dispone di una conversione unboxing da un tipo di interfaccia `I` se è presente una conversione unboxing da un tipo di interfaccia `I0` e `I0` ha una conversione di identità in `I`.

Un tipo di valore `S` dispone di una conversione unboxing da un tipo di interfaccia `I` se è presente una conversione unboxing da un tipo di interfaccia o delegato `I0` e la `I0` è varianza-convertibile in `I` o `I` è la varianza-convertibile in `I0` ([conversione della varianza](interfaces.md#variance-conversion)).

Un'operazione di conversione unboxing consiste nel verificare innanzitutto che l'istanza dell'oggetto sia un valore boxed del *value_type*specificato e quindi copiare il valore all'esterno dell'istanza. Se si unboxing un riferimento null a un *nullable_type* , viene prodotto il valore null del *nullable_type*. Uno struct può essere unboxed dal tipo `System.ValueType`, perché si tratta di una classe di base per tutti gli struct ([ereditarietà](structs.md#inheritance)).

Le conversioni unboxing sono descritte ulteriormente nelle [conversioni unboxing](types.md#unboxing-conversions).

### <a name="explicit-dynamic-conversions"></a>Conversioni dinamiche esplicite

Una conversione dinamica esplicita esiste da un'espressione di tipo `dynamic` a qualsiasi tipo `T`. La conversione è associata in modo dinamico ([associazione dinamica](expressions.md#dynamic-binding)), il che significa che una conversione esplicita verrà cercata in fase di esecuzione dal tipo in fase di esecuzione dell'espressione `T`. Se non viene trovata alcuna conversione, viene generata un'eccezione in fase di esecuzione.

Se non si desidera un binding dinamico della conversione, l'espressione può essere prima convertita in `object`e quindi nel tipo desiderato.

Si supponga che venga definita la classe seguente:
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

Nell'esempio seguente vengono illustrate le conversioni dinamiche esplicite:
```csharp
object o  = "1";
dynamic d = "2";

var c1 = (C)o; // Compiles, but explicit reference conversion fails
var c2 = (C)d; // Compiles and user defined conversion succeeds
```

La migliore conversione di `o` in `C` viene trovata in fase di compilazione come conversione esplicita dei riferimenti. Questa operazione ha esito negativo in fase di esecuzione, perché `"1"` non è in realtà una `C`. La conversione di `d` in `C` tuttavia, come conversione dinamica esplicita, viene sospesa in fase di esecuzione, in cui viene rilevata una conversione definita dall'utente dal tipo in fase di esecuzione di `d` -- `string`--a `C`, che ha esito positivo.

### <a name="explicit-conversions-involving-type-parameters"></a>Conversioni esplicite che coinvolgono parametri di tipo

Per un parametro di tipo specificato esistono le conversioni esplicite seguenti `T`:

*  Dalla classe base valida `C` di `T` per `T` e da qualsiasi classe di base di `C` a `T`. In fase di esecuzione, se `T` è un tipo di valore, la conversione viene eseguita come conversione unboxing. In caso contrario, la conversione viene eseguita come conversione esplicita di un riferimento o conversione di identità.
*  Da qualsiasi tipo di interfaccia a `T`. In fase di esecuzione, se `T` è un tipo di valore, la conversione viene eseguita come conversione unboxing. In caso contrario, la conversione viene eseguita come conversione esplicita di un riferimento o conversione di identità.
*  Da `T` a qualsiasi *interface_type* `I` purché non esista già una conversione implicita da `T` a `I`. In fase di esecuzione, se `T` è un tipo valore, la conversione viene eseguita come conversione boxing seguita da una conversione esplicita del riferimento. In caso contrario, la conversione viene eseguita come conversione esplicita di un riferimento o conversione di identità.
*  Da un parametro di tipo `U` `T`, fornito `T` dipende da `U` ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)). In fase di esecuzione, se `U` è un tipo di valore, `T` e `U` sono necessariamente dello stesso tipo e non viene eseguita alcuna conversione. In caso contrario, se `T` è un tipo valore, la conversione viene eseguita come conversione unboxing. In caso contrario, la conversione viene eseguita come conversione esplicita di un riferimento o conversione di identità.

Se `T` è noto come tipo di riferimento, le conversioni precedenti vengono tutte classificate come conversioni esplicite dei riferimenti ([conversioni esplicite dei riferimenti](conversions.md#explicit-reference-conversions)). Se `T` non è noto come tipo di riferimento, le conversioni precedenti vengono classificate come conversioni unboxing (conversione[unboxing](conversions.md#unboxing-conversions)).

Le regole precedenti non consentono una conversione esplicita diretta da un parametro di tipo non vincolato a un tipo non di interfaccia, che potrebbe essere sorprendente. Il motivo di questa regola è impedire confusione e rendere chiara la semantica di tali conversioni. Si consideri ad esempio la seguente dichiarazione:
```csharp
class X<T>
{
    public static long F(T t) {
        return (long)t;                // Error 
    }
}
```

Se è stata consentita la conversione diretta esplicita di `t` in `int`, è possibile che `X<int>.F(7)` restituisca `7L`. Tuttavia, non lo sarebbe, perché le conversioni numeriche standard vengono considerate solo quando i tipi sono noti come numerici in fase di binding. Per chiarire la semantica, è necessario scrivere l'esempio precedente:
```csharp
class X<T>
{
    public static long F(T t) {
        return (long)(object)t;        // Ok, but will only work when T is long
    }
}
```

Questo codice verrà ora compilato, ma l'esecuzione di `X<int>.F(7)` genererebbe un'eccezione in fase di esecuzione, poiché non è possibile convertire una `int` boxed direttamente in un `long`.

### <a name="user-defined-explicit-conversions"></a>Conversioni esplicite definite dall'utente

Una conversione esplicita definita dall'utente è costituita da una conversione esplicita standard facoltativa, seguita dall'esecuzione di un operatore di conversione esplicito o implicito definito dall'utente, seguita da un'altra conversione esplicita standard facoltativa. Le regole esatte per la valutazione delle conversioni esplicite definite dall'utente sono descritte in [elaborazione di conversioni esplicite definite dall'utente](conversions.md#processing-of-user-defined-explicit-conversions).

## <a name="standard-conversions"></a>Conversioni standard

Le conversioni standard sono quelle predefinite che possono verificarsi come parte di una conversione definita dall'utente.

### <a name="standard-implicit-conversions"></a>Conversioni implicite standard

Le conversioni implicite seguenti sono classificate come conversioni implicite standard:

*  Conversioni di identità ([conversione di identità](conversions.md#identity-conversion))
*  Conversioni numeriche implicite ([conversioni numeriche implicite](conversions.md#implicit-numeric-conversions))
*  Conversioni implicite Nullable ([conversioni implicite Nullable](conversions.md#implicit-nullable-conversions))
*  Conversioni implicite di riferimenti ([conversioni implicite di riferimenti](conversions.md#implicit-reference-conversions))
*  Conversioni boxing ([conversioni boxing](conversions.md#boxing-conversions))
*  Conversioni implicite di espressioni costanti ([conversioni dinamiche implicite](conversions.md#implicit-dynamic-conversions))
*  Conversioni implicite che coinvolgono parametri di tipo ([conversioni implicite che coinvolgono parametri di tipo](conversions.md#implicit-conversions-involving-type-parameters))

Le conversioni implicite standard escludono in modo specifico le conversioni implicite definite dall'utente.

### <a name="standard-explicit-conversions"></a>Conversioni esplicite standard

Le conversioni esplicite standard sono tutte conversioni implicite standard e il subset delle conversioni esplicite per le quali esiste una conversione implicita standard opposta. In altre parole, se esiste una conversione implicita standard da un tipo `A` a un tipo `B`, una conversione esplicita standard esiste dal tipo `A` al tipo `B` e dal tipo `B` al tipo `A`.

## <a name="user-defined-conversions"></a>Conversioni definite dall'utente

C#consente di aumentare le conversioni implicite ed esplicite predefinite in base alle ***conversioni definite dall'utente***. Le conversioni definite dall'utente vengono introdotte dichiarando operatori di conversione ([operatori di conversione](classes.md#conversion-operators)) in tipi di classe e struct.

### <a name="permitted-user-defined-conversions"></a>Conversioni definite dall'utente consentite

C#consente di dichiarare solo determinate conversioni definite dall'utente. In particolare, non è possibile ridefinire una conversione implicita o esplicita già esistente.

Per un tipo di origine specificato `S` e il tipo di destinazione `T`, se `S` o `T` sono tipi nullable, consentire a `S0` e `T0` di fare riferimento ai tipi sottostanti, in caso contrario `S0` e `T0` sono uguali a `S` e `T` rispettivamente. Una classe o uno struct è autorizzato a dichiarare una conversione da un tipo di origine `S` a un tipo di destinazione `T` solo se si verificano tutte le condizioni seguenti:

*  `S0` e `T0` sono tipi diversi.
*  `S0` o `T0` è il tipo di classe o struct in cui si verifica la dichiarazione di operatore.
*  Né `S0` né `T0` sono *INTERFACE_TYPE*.
*  Escludendo le conversioni definite dall'utente, non esiste una conversione da `S` a `T` o da `T` a `S`.

Le restrizioni che si applicano alle conversioni definite dall'utente vengono descritte ulteriormente negli [operatori di conversione](classes.md#conversion-operators).

### <a name="lifted-conversion-operators"></a>Operatori di conversione accurati

Dato un operatore di conversione definito dall'utente che converte da un tipo di valore non nullable `S` a un tipo di valore non nullable `T`, esiste un ***operatore di conversione accurato*** che converte da `S?` a `T?`. Questo operatore di conversione Lift esegue l'annullamento del wrapping da `S?` a `S` seguito dalla conversione definita dall'utente da `S` a `T` seguito da un wrapping da `T` a `T?`, ad eccezione del fatto che un valore null `S?` viene convertito direttamente in un `T?`con valori null.

Un operatore di conversione Lift ha la stessa classificazione implicita o esplicita dell'operatore di conversione definito dall'utente sottostante. Il termine "conversione definita dall'utente" si applica all'utilizzo di operatori di conversione sia definiti dall'utente che rimossi.

### <a name="evaluation-of-user-defined-conversions"></a>Valutazione delle conversioni definite dall'utente

Una conversione definita dall'utente converte un valore dal tipo, denominato tipo di ***origine***, a un altro tipo, denominato ***tipo di destinazione***. Valutazione di un centro di conversione definito dall'utente per la ricerca dell'operatore di conversione definito dall'utente ***più specifico*** per i tipi di origine e di destinazione specifici. Questa determinazione è suddivisa in diversi passaggi:

*  Ricerca del set di classi e struct da cui verranno considerati gli operatori di conversione definiti dall'utente. Questo set è costituito dal tipo di origine e dalle relative classi base e dal tipo di destinazione e dalle relative classi di base (con i presupposti impliciti che solo classi e struct possono dichiarare operatori definiti dall'utente e che i tipi non di classe non hanno classi base). Ai fini di questo passaggio, se il tipo di origine o di destinazione è un *nullable_type*, viene invece utilizzato il tipo sottostante.
*  Da tale set di tipi, che determina quali operatori di conversione definiti dall'utente e rimossi sono applicabili. Affinché un operatore di conversione sia applicabile, deve essere possibile eseguire una conversione standard ([conversioni standard](conversions.md#standard-conversions)) dal tipo di origine al tipo di operando dell'operatore e deve essere possibile eseguire una conversione standard dal tipo di risultato dell'operatore al tipo di destinazione.
*  Dal set di operatori definiti dall'utente applicabili, che determinano l'operatore che non è ambiguamente più specifico. In termini generali, l'operatore più specifico è l'operatore il cui tipo di operando è "più vicino" al tipo di origine e il cui tipo di risultato è "più vicino" al tipo di destinazione. Gli operatori di conversione definiti dall'utente sono preferiti rispetto agli operatori di conversione rimossi. Nelle sezioni seguenti vengono definite le regole esatte per la definizione dell'operatore di conversione definito dall'utente più specifico.

Una volta identificato un operatore di conversione definito dall'utente più specifico, l'esecuzione effettiva della conversione definita dall'utente prevede fino a tre passaggi:

*  Prima di tutto, se necessario, eseguire una conversione standard dal tipo di origine al tipo di operando dell'operatore di conversione definito dall'utente o lifted.
*  Richiamando quindi l'operatore di conversione definito dall'utente o lifted per eseguire la conversione.
*  Infine, se necessario, l'esecuzione di una conversione standard dal tipo di risultato dell'operatore di conversione definito dall'utente o lifted al tipo di destinazione.

La valutazione di una conversione definita dall'utente non prevede mai più di un operatore di conversione definito dall'utente o lifted. In altre parole, una conversione dal tipo `S` al tipo `T` non eseguirà mai prima una conversione definita dall'utente da `S` a `X` e quindi eseguirà una conversione definita dall'utente da `X` a `T`.

Nelle sezioni seguenti vengono fornite le definizioni esatte della valutazione delle conversioni esplicite o implicite definite dall'utente. Le definizioni utilizzano i termini seguenti:

*  Se una conversione implicita standard ([conversioni implicite standard](conversions.md#standard-implicit-conversions)) esiste da un tipo `A` a un tipo `B`e se nessuna delle due `A` né `B` sono *interface_type*s, `A` viene detto che è ***incluso*** `B`e `B` viene detto che ***comprende*** `A`.
*  Il ***tipo più incluso*** in un set di tipi è quello che comprende tutti gli altri tipi nel set. Se nessun tipo singolo include tutti gli altri tipi, il set non include il tipo più incluso. In termini più intuitivi, il tipo più comprendente è il tipo "più grande" nel set, ovvero quello in cui ogni altro tipo può essere convertito in modo implicito.
*  Il ***tipo più incluso*** in un set di tipi è quello incluso in tutti gli altri tipi del set. Se nessun tipo singolo è incluso in tutti gli altri tipi, il set non ha un tipo più incluso. In termini più intuitivi, il tipo più incluso è il tipo "più piccolo" nel set, ovvero quello che può essere convertito in modo implicito in ognuno degli altri tipi.

### <a name="processing-of-user-defined-implicit-conversions"></a>Elaborazione delle conversioni implicite definite dall'utente

Una conversione implicita definita dall'utente dal tipo `S` al tipo `T` viene elaborata come indicato di seguito:

*  Determinare i tipi `S0` e `T0`. Se `S` o `T` sono tipi nullable, `S0` e `T0` sono i tipi sottostanti, in caso contrario `S0` e `T0` sono uguali a `S` e `T` rispettivamente.
*  Trovare il set di tipi, `D`, da cui verranno considerati gli operatori di conversione definiti dall'utente. Questo set è costituito da `S0` (se `S0` è una classe o uno struct), le classi di base di `S0` (se `S0` è una classe) e `T0` (se `T0` è una classe o uno struct).
*  Trovare il set di operatori di conversione rimossi e definiti dall'utente applicabili `U`. Questo set è costituito dagli operatori di conversione impliciti definiti dall'utente e rimossi dichiarati dalle classi o dagli struct in `D` che convertono da un tipo che comprende `S` a un tipo incluso in `T`. Se `U` è vuoto, la conversione non è definita e si verifica un errore in fase di compilazione.
*  Trovare il tipo di origine più specifico, `SX`, degli operatori in `U`:
    * Se uno degli operatori di `U` converte da `S`, `SX` `S`.
    * In caso contrario, `SX` è il tipo più incluso nel set combinato di tipi di origine degli operatori `U`. Se non è possibile trovare esattamente un tipo più incluso, la conversione è ambigua e si verifica un errore in fase di compilazione.
*  Trovare il tipo di destinazione più specifico, `TX`, degli operatori in `U`:
    * Se uno degli operatori di `U` converte in `T`, `TX` è `T`.
    * In caso contrario, `TX` è il tipo più incluso nel set combinato di tipi di destinazione degli operatori nella `U`. Se non è possibile trovare esattamente un tipo più onnicomprensivo, la conversione è ambigua e si verifica un errore in fase di compilazione.
*  Trovare l'operatore di conversione più specifico:
    * Se `U` contiene esattamente un operatore di conversione definito dall'utente che converte da `SX` a `TX`, si tratta dell'operatore di conversione più specifico.
    * In caso contrario, se `U` contiene esattamente un operatore di conversione lifted che converte da `SX` a `TX`, si tratta dell'operatore di conversione più specifico.
    * In caso contrario, la conversione è ambigua e si verifica un errore in fase di compilazione.
*  Infine, applicare la conversione:
    * Se `S` non è `SX`, viene eseguita una conversione implicita standard da `S` a `SX`.
    * Viene richiamato l'operatore di conversione più specifico per eseguire la conversione da `SX` a `TX`.
    * Se `TX` non è `T`, viene eseguita una conversione implicita standard da `TX` a `T`.

### <a name="processing-of-user-defined-explicit-conversions"></a>Elaborazione delle conversioni esplicite definite dall'utente

Una conversione esplicita definita dall'utente dal tipo `S` al tipo `T` viene elaborata come indicato di seguito:

*  Determinare i tipi `S0` e `T0`. Se `S` o `T` sono tipi nullable, `S0` e `T0` sono i tipi sottostanti, in caso contrario `S0` e `T0` sono uguali a `S` e `T` rispettivamente.
*  Trovare il set di tipi, `D`, da cui verranno considerati gli operatori di conversione definiti dall'utente. Questo set è costituito da `S0` (se `S0` è una classe o uno struct), le classi di base di `S0` (se `S0` è una classe), `T0` (se `T0` è una classe o uno struct) e le classi di base di `T0` (se `T0` è una classe).
*  Trovare il set di operatori di conversione rimossi e definiti dall'utente applicabili `U`. Questo set è costituito dagli operatori di conversione espliciti o impliciti definiti dall'utente e rimossi dichiarati dalle classi o dagli struct in `D` che convertono da un tipo incluso o incluso `S` a un tipo che comprende o è incluso in `T`. Se `U` è vuoto, la conversione non è definita e si verifica un errore in fase di compilazione.
*  Trovare il tipo di origine più specifico, `SX`, degli operatori in `U`:
    * Se uno degli operatori di `U` converte da `S`, `SX` `S`.
    * In caso contrario, se uno degli operatori di `U` converte da tipi che includono `S`, `SX` è il tipo più incluso nel set combinato di tipi di origine di tali operatori. Se non è possibile trovare un tipo più incluso, la conversione è ambigua e si verifica un errore in fase di compilazione.
    * In caso contrario, `SX` è il tipo più incluso nel set combinato di tipi di origine degli operatori `U`. Se non è possibile trovare esattamente un tipo più onnicomprensivo, la conversione è ambigua e si verifica un errore in fase di compilazione.
*  Trovare il tipo di destinazione più specifico, `TX`, degli operatori in `U`:
    * Se uno degli operatori di `U` converte in `T`, `TX` è `T`.
    * In caso contrario, se uno degli operatori di `U` converte in tipi inclusi da `T`, `TX` è il tipo più incluso nel set combinato di tipi di destinazione di tali operatori. Se non è possibile trovare esattamente un tipo più onnicomprensivo, la conversione è ambigua e si verifica un errore in fase di compilazione.
    * In caso contrario, `TX` è il tipo più incluso nel set combinato di tipi di destinazione degli operatori `U`. Se non è possibile trovare un tipo più incluso, la conversione è ambigua e si verifica un errore in fase di compilazione.
*  Trovare l'operatore di conversione più specifico:
    * Se `U` contiene esattamente un operatore di conversione definito dall'utente che converte da `SX` a `TX`, si tratta dell'operatore di conversione più specifico.
    * In caso contrario, se `U` contiene esattamente un operatore di conversione lifted che converte da `SX` a `TX`, si tratta dell'operatore di conversione più specifico.
    * In caso contrario, la conversione è ambigua e si verifica un errore in fase di compilazione.
*  Infine, applicare la conversione:
    * Se `S` non è `SX`, viene eseguita una conversione esplicita standard da `S` a `SX`.
    * Viene richiamato l'operatore di conversione più specifico definito dall'utente per eseguire la conversione da `SX` a `TX`.
    * Se `TX` non è `T`, viene eseguita una conversione esplicita standard da `TX` a `T`.

## <a name="anonymous-function-conversions"></a>Conversioni di funzioni anonime

Un *anonymous_method_expression* o un *lambda_expression* è classificato come funzione anonima ([espressioni di funzione anonime](expressions.md#anonymous-function-expressions)). L'espressione non ha un tipo, ma può essere convertita in modo implicito in un tipo di delegato o di albero delle espressioni compatibile. In particolare, una funzione anonima `F` è compatibile con un tipo delegato `D` fornito:

*  Se `F` contiene un *anonymous_function_signature*, `D` e `F` hanno lo stesso numero di parametri.
*  Se `F` non contiene un *anonymous_function_signature*, `D` può avere zero o più parametri di qualsiasi tipo, a condizione che nessun parametro di `D` abbia il modificatore di parametro `out`.
*  Se `F` dispone di un elenco di parametri tipizzato in modo esplicito, ogni parametro in `D` ha lo stesso tipo e modificatori del parametro corrispondente in `F`.
*  Se `F` dispone di un elenco di parametri tipizzato in modo implicito, `D` non dispone di parametri `ref` o `out`.
*  Se il corpo di `F` è un'espressione e `D` dispone di un tipo restituito `void` o `F` è async e `D` ha il tipo restituito `Task`, quando a ogni parametro di `F` viene assegnato il tipo del parametro corrispondente in `D`, il corpo di `F` è un'espressione valida ( [espressioni](expressions.md)WRT) che verrebbe consentita come *statement_expression* ([istruzioni Expression](statements.md#expression-statements)).
*  Se il corpo di `F` è un blocco di istruzioni e `D` dispone di un tipo restituito `void` o `F` è async e `D` ha il tipo restituito `Task`, quando a ogni parametro di `F` viene assegnato il tipo del parametro corrispondente in `D`, il corpo di `F` è un blocco di istruzione valido ( [blocchi](statements.md#blocks)WRT) in cui nessuna istruzione `return` specifica un'espressione.
*  Se il corpo di `F` è un'espressione *e `F` è non asincrono e `D`* ha un tipo restituito non void `T`*o* `F` è async e `D` ha un tipo restituito `Task<T>`, quando a ogni parametro di `F` viene assegnato il tipo del parametro corrispondente in `D`, il corpo di `F` è un'espressione valida ( [espressioni](expressions.md)WRT) convertibile in modo implicito in `T`.
*  Se il corpo di `F` è un *blocco di istruzioni e `F` è* non asincrono e `D` ha un tipo restituito non void `T`, *o* `F` è async e `D` dispone di un tipo restituito `Task<T>`, quindi quando ogni parametro di `F` viene assegnato il tipo del parametro corrispondente in `D`, il corpo di `F` è un blocco di istruzioni valido ( [blocchi](statements.md#blocks)WRT) con un punto finale non raggiungibile in cui ogni istruzione `return` specifica un'espressione convertibile in modo implicito in `T`.

Per motivi di brevità, in questa sezione viene utilizzata la forma breve per i tipi di attività `Task` e `Task<T>` ([funzioni asincrone](classes.md#async-functions)).

Un'espressione lambda `F` è compatibile con un tipo di albero delle espressioni `Expression<D>` se `F` è compatibile con il tipo di delegato `D`. Si noti che questa operazione non si applica ai metodi anonimi, ma solo alle espressioni lambda.

Alcune espressioni lambda non possono essere convertite in tipi di albero delle espressioni: anche se la conversione *esiste*, l'operazione non riesce in fase di compilazione. Questa situazione si verifica se l'espressione lambda:

*  Ha un corpo del *blocco*
*  Contiene operatori di assegnazione semplici o composti
*  Contiene un'espressione associata dinamicamente
*  Asincrono

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
il parametro e i tipi restituiti di ogni funzione anonima sono determinati dal tipo della variabile a cui è assegnata la funzione anonima.

La prima assegnazione converte correttamente la funzione anonima nel tipo delegato `Func<int,int>` perché, quando `x` viene assegnato il tipo `int`, `x+1` è un'espressione valida convertibile in modo implicito nel tipo `int`.

Analogamente, la seconda assegnazione converte correttamente la funzione anonima nel tipo delegato `Func<int,double>` perché il risultato di `x+1` (di tipo `int`) è convertibile in modo implicito nel tipo `double`.

Tuttavia, la terza assegnazione è un errore in fase di compilazione perché, quando `x` viene assegnato il tipo `double`, il risultato di `x+1` (di tipo `double`) non è convertibile in modo implicito nel tipo `int`.

La quarta assegnazione converte correttamente la funzione asincrona anonima nel tipo delegato `Func<int, Task<int>>` perché il risultato di `x+1` (di tipo `int`) è convertibile in modo implicito nel tipo di risultato `int` del tipo di attività `Task<int>`.

Le funzioni anonime possono influenzare la risoluzione dell'overload e partecipare all'inferenza del tipo. Per ulteriori informazioni, vedere [membri di funzione](expressions.md#function-members) .

### <a name="evaluation-of-anonymous-function-conversions-to-delegate-types"></a>Valutazione delle conversioni di funzioni anonime nei tipi delegati

La conversione di una funzione anonima in un tipo delegato produce un'istanza di delegato che fa riferimento alla funzione anonima e al set di variabili esterne acquisite (possibilmente vuote) attive al momento della valutazione. Quando il delegato viene richiamato, viene eseguito il corpo della funzione anonima. Il codice nel corpo viene eseguito utilizzando il set di variabili esterne acquisite a cui fa riferimento il delegato.

L'elenco chiamate di un delegato prodotto da una funzione anonima contiene una singola voce. L'oggetto di destinazione esatto e il metodo di destinazione del delegato non sono specificati. In particolare, non viene specificato se l'oggetto di destinazione del delegato è `null`, il valore `this` del membro della funzione contenitore o un altro oggetto.

Le conversioni di funzioni anonime semanticamente identiche con lo stesso set (possibilmente vuoto) di istanze di variabili esterne acquisite agli stessi tipi delegati sono consentite (ma non necessarie) per restituire la stessa istanza del delegato. Il termine semanticamente identico viene usato qui per indicare che l'esecuzione delle funzioni anonime, in tutti i casi, produce gli stessi effetti dato gli stessi argomenti. Questa regola consente di ottimizzare il codice come il seguente.

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

Poiché i due delegati di funzione anonima hanno lo stesso set (vuoto) di variabili esterne acquisite e poiché le funzioni anonime sono semanticamente identiche, il compilatore può fare riferimento allo stesso metodo di destinazione. Il compilatore può effettivamente restituire la stessa istanza del delegato da entrambe le espressioni di funzioni anonime.

### <a name="evaluation-of-anonymous-function-conversions-to-expression-tree-types"></a>Valutazione delle conversioni di funzioni anonime nei tipi di albero delle espressioni

La conversione di una funzione anonima in un tipo di albero delle espressioni genera un albero delle espressioni ([tipi di albero delle espressioni](types.md#expression-tree-types)). Più precisamente, la valutazione della conversione di funzione anonima comporta la costruzione di una struttura di oggetti che rappresenta la struttura della funzione anonima stessa. La struttura precisa dell'albero delle espressioni e il processo esatto per crearla sono definite dall'implementazione.

### <a name="implementation-example"></a>Esempio di implementazione

In questa sezione viene descritta una possibile implementazione di conversioni di funzioni anonime in C# termini di altri costrutti. L'implementazione descritta di seguito si basa sugli stessi principi usati dal compilatore Microsoft C# , ma non è un'implementazione obbligatoria, né è l'unico possibile. Indica solo brevemente le conversioni agli alberi delle espressioni, perché la semantica esatta esula dall'ambito di questa specifica.

Nella parte restante di questa sezione vengono forniti alcuni esempi di codice che contengono funzioni anonime con caratteristiche diverse. Per ogni esempio, viene fornita una traduzione corrispondente al codice che usa C# solo altri costrutti. Negli esempi, l'identificatore `D` si presuppone che rappresenti il tipo delegato seguente:
```csharp
public delegate void D();
```

La forma più semplice di una funzione anonima è quella che non acquisisce variabili esterne:
```csharp
class Test
{
    static void F() {
        D d = () => { Console.WriteLine("test"); };
    }
}
```

Questo può essere convertito in un'istanza di delegato che fa riferimento a un metodo statico generato dal compilatore in cui viene inserito il codice della funzione anonima:
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

Nell'esempio seguente la funzione anonima fa riferimento ai membri di istanza di `this`:
```csharp
class Test
{
    int x;

    void F() {
        D d = () => { Console.WriteLine(x); };
    }
}
```

Questo può essere convertito in un metodo di istanza generato dal compilatore che contiene il codice della funzione anonima:
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

In questo esempio, la funzione anonima acquisisce una variabile locale:
```csharp
class Test
{
    void F() {
        int y = 123;
        D d = () => { Console.WriteLine(y); };
    }
}
```

La durata della variabile locale deve ora essere estesa almeno alla durata del delegato della funzione anonima. Questa operazione può essere eseguita mediante il "sollevamento" della variabile locale in un campo di una classe generata dal compilatore. La creazione di un'istanza della variabile locale ([creazione di un'istanza di variabili locali](expressions.md#instantiation-of-local-variables)) corrisponde quindi alla creazione di un'istanza della classe generata dal compilatore e l'accesso alla variabile locale corrisponde all'accesso a un campo nell'istanza della classe generata dal compilatore. Inoltre, la funzione anonima diventa un metodo di istanza della classe generata dal compilatore:
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

Infine, la funzione anonima seguente acquisisce `this` e due variabili locali con durate diverse:
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

In questo caso viene creata una classe generata dal compilatore per ogni blocco di istruzioni in cui vengono acquisite le variabili locali in modo che le variabili locali nei diversi blocchi possano avere durate indipendenti. Un'istanza di `__Locals2`, la classe generata dal compilatore per il blocco di istruzioni interno, contiene la variabile locale `z` e un campo che fa riferimento a un'istanza di `__Locals1`.  Un'istanza di `__Locals1`, la classe generata dal compilatore per il blocco di istruzioni esterno, contiene la variabile locale `y` e un campo che fa riferimento `this` del membro della funzione contenitore. Con queste strutture di dati è possibile raggiungere tutte le variabili esterne acquisite tramite un'istanza di `__Local2`e il codice della funzione anonima può quindi essere implementato come metodo di istanza di quella classe.

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

La stessa tecnica applicata qui per acquisire le variabili locali può essere usata anche durante la conversione di funzioni anonime in alberi delle espressioni: i riferimenti agli oggetti generati dal compilatore possono essere archiviati nell'albero delle espressioni e l'accesso alle variabili locali può essere rappresentati come accessi al campo su questi oggetti. Il vantaggio di questo approccio è che consente di condividere le variabili locali "elevate" tra i delegati e gli alberi delle espressioni.

## <a name="method-group-conversions"></a>Conversioni di gruppi di metodi

Una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) esiste da un gruppo di metodi ([classificazioni di espressioni](expressions.md#expression-classifications)) a un tipo delegato compatibile. Dato un tipo di delegato `D` e un'espressione `E` classificata come un gruppo di metodi, esiste una conversione implicita da `E` a `D` se `E` contiene almeno un metodo applicabile nel formato normale (membro della[funzione applicabile](expressions.md#applicable-function-member)) a un elenco di argomenti costruito usando i tipi di parametro e i modificatori di `D`, come descritto di seguito.

L'applicazione in fase di compilazione di una conversione da un gruppo di metodi `E` a un tipo delegato `D` è descritta nell'esempio seguente. Si noti che l'esistenza di una conversione implicita da `E` a `D` non garantisce che l'applicazione della conversione in fase di compilazione avrà esito positivo senza errori.

*  Viene selezionato un solo metodo `M` corrispondente a una chiamata al metodo ([chiamate al metodo](expressions.md#method-invocations)) del form `E(A)`, con le modifiche seguenti:
    * L'elenco di argomenti `A` è un elenco di espressioni, ciascuna classificata come variabile e con il tipo e il modificatore (`ref` o `out`) del parametro corrispondente nell' *formal_parameter_list* di `D`.
    * I metodi candidati considerati sono solo quelli applicabili nel formato normale ([membro della funzione applicabile](expressions.md#applicable-function-member)), non quelli applicabili solo nel formato espanso.
*  Se l'algoritmo delle [chiamate di metodo](expressions.md#method-invocations) genera un errore, si verifica un errore in fase di compilazione. In caso contrario, l'algoritmo produce un unico metodo migliore `M` avere lo stesso numero di parametri `D` e la conversione viene considerata esistente.
*  Il `M` del metodo selezionato deve essere compatibile (la compatibilità con i[delegati](delegates.md#delegate-compatibility)) con il tipo delegato `D`. in caso contrario, si verifica un errore in fase di compilazione.
*  Se il metodo selezionato `M` è un metodo di istanza, l'espressione dell'istanza associata a `E` determina l'oggetto di destinazione del delegato.
*  Se il metodo selezionato M è un metodo di estensione che è indicato tramite un accesso a un membro in un'espressione di istanza, tale espressione di istanza determina l'oggetto di destinazione del delegato.
*  Il risultato della conversione è un valore di tipo `D`, ovvero un delegato appena creato che fa riferimento al metodo selezionato e all'oggetto di destinazione.
*  Si noti che questo processo può causare la creazione di un delegato a un metodo di estensione, se l'algoritmo delle [chiamate al metodo](expressions.md#method-invocations) non riesce a trovare un metodo di istanza ma ha esito positivo nell'elaborazione della chiamata di `E(A)` come chiamata a un metodo di estensione (chiamate al metodo di[estensione](expressions.md#extension-method-invocations)). Un delegato creato in questo modo acquisisce il metodo di estensione e il primo argomento.

Nell'esempio seguente vengono illustrate le conversioni dei gruppi di metodi:
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

L'assegnazione a `d1` converte in modo implicito il gruppo di metodi `F` in un valore di tipo `D1`.

L'assegnazione a `d2` Mostra come è possibile creare un delegato per un metodo con tipi di parametro meno derivati (controvariante) e un tipo restituito più derivato (covariante).

L'assegnazione a `d3` Mostra in che modo non esiste alcuna conversione se il metodo non è applicabile.

L'assegnazione a `d4` Mostra come il metodo deve essere applicabile nel formato normale.

L'assegnazione a `d5` Mostra come il parametro e i tipi restituiti del delegato e del metodo possono differire solo per i tipi di riferimento.

Come per tutte le altre conversioni implicite ed esplicite, l'operatore cast può essere utilizzato per eseguire in modo esplicito la conversione di un gruppo di metodi. Quindi, l'esempio
```csharp
object obj = new EventHandler(myDialog.OkClick);
```
in alternativa, è possibile scrivere
```csharp
object obj = (EventHandler)myDialog.OkClick;
```

I gruppi di metodi possono influenzare la risoluzione dell'overload e partecipare all'inferenza del tipo. Per ulteriori informazioni, vedere [membri di funzione](expressions.md#function-members) .

La valutazione in fase di esecuzione della conversione di un gruppo di metodi procede come segue:

*  Se il metodo selezionato in fase di compilazione è un metodo di istanza o è un metodo di estensione a cui si accede come metodo di istanza, l'oggetto di destinazione del delegato è determinato dall'espressione dell'istanza associata a `E`:
    * L'espressione dell'istanza viene valutata. Se questa valutazione causa un'eccezione, non vengono eseguiti altri passaggi.
    * Se l'espressione dell'istanza è di un *reference_type*, il valore calcolato dall'espressione di istanza diventa l'oggetto di destinazione. Se il metodo selezionato è un metodo di istanza e l'oggetto di destinazione è `null`, viene generata un'`System.NullReferenceException` e non vengono eseguiti altri passaggi.
    * Se l'espressione dell'istanza è di un *value_type*, viene eseguita un'operazione di conversione boxing ([conversioni boxing](types.md#boxing-conversions)) per convertire il valore in un oggetto e questo oggetto diventa l'oggetto di destinazione.
*  In caso contrario, il metodo selezionato fa parte di una chiamata a un metodo statico e l'oggetto di destinazione del delegato viene `null`.
*  Viene allocata una nuova istanza del tipo delegato `D`. Se la memoria disponibile non è sufficiente per allocare la nuova istanza, viene generata un'`System.OutOfMemoryException` e non vengono eseguiti altri passaggi.
*  La nuova istanza del delegato viene inizializzata con un riferimento al metodo determinato in fase di compilazione e un riferimento all'oggetto di destinazione calcolato sopra.
