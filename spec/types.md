# <a name="types"></a>Tipi

I tipi del linguaggio c# sono suddivisi in due categorie principali: ***i tipi di valore*** e ***fanno riferimento ai tipi***. I tipi di valore e tipi di riferimento possono essere ***i tipi generici***, che accettano uno o più ***parametri di tipo***. I parametri di tipo possono designare entrambi i tipi valore e tipi di riferimento.

```antlr
type
    : value_type
    | reference_type
    | type_parameter
    | type_unsafe
    ;
```

La categoria di tipi, i puntatori, finale è disponibile solo nel codice non sicuro. Questo argomento verrà trattato dettagliatamente nella [tipi di puntatore](unsafe-code.md#pointer-types).

I tipi di valore differiscono dai tipi di riferimento in quanto le variabili dei tipi valore contengono direttamente i propri dati, mentre le variabili del riferimento a tipi di archivio ***riferimenti*** ai dati, i noti come ***oggetti***. Con i tipi riferimento, è possibile che due variabili possono fare riferimento allo stesso oggetto e pertanto le operazioni su una variabile influiscano sull'oggetto a cui fa riferimento l'altra. Tipi di valore, ogni variabile ha una propria copia dei dati e non è possibile per le operazioni su una variabile influiscano su altra.

Il sistema di tipi di # è unificato in modo che un valore di qualsiasi tipo può essere considerato come un oggetto. In C# ogni tipo deriva direttamente o indirettamente dal tipo classe `object` e `object` è la classe di base principale di tutti i tipi. I valori dei tipi riferimento vengono trattati come oggetti semplicemente visualizzando tali valori come tipi `object`. I valori dei tipi di valore vengono trattati come oggetti mediante l'esecuzione di operazioni di conversione boxing e unboxing ([conversioni Boxing e unboxing](types.md#boxing-and-unboxing)).

## <a name="value-types"></a>Tipi valore

Un tipo di valore è un tipo struct o un tipo di enumerazione. C# offre un set di tipi di struct predefinito denominato il ***i tipi semplici***. I tipi semplici vengono identificati tramite le parole riservate.

```antlr
value_type
    : struct_type
    | enum_type
    ;

struct_type
    : type_name
    | simple_type
    | nullable_type
    ;

simple_type
    : numeric_type
    | 'bool'
    ;

numeric_type
    : integral_type
    | floating_point_type
    | 'decimal'
    ;

integral_type
    : 'sbyte'
    | 'byte'
    | 'short'
    | 'ushort'
    | 'int'
    | 'uint'
    | 'long'
    | 'ulong'
    | 'char'
    ;

floating_point_type
    : 'float'
    | 'double'
    ;

nullable_type
    : non_nullable_value_type '?'
    ;

non_nullable_value_type
    : type
    ;

enum_type
    : type_name
    ;
```

A differenza di una variabile di un tipo riferimento, una variabile di un tipo valore può contenere il valore `null` solo se il tipo di valore è un tipo nullable.  Per ogni tipo di valore non nullable è presente un corrispondente tipo di valore nullable che indica lo stesso set di valori più il valore `null`.

Assegnazione a una variabile di un tipo valore consente di creare una copia del valore da assegnare. Questo comportamento è diverso dall'assegnazione a una variabile di un tipo riferimento, che consente di copiare il riferimento, ma non l'oggetto identificato dal riferimento.

### <a name="the-systemvaluetype-type"></a>Il tipo System. ValueType

Tutti i tipi valore ereditano in modo implicito dalla classe `System.ValueType`che, a sua volta eredita dalla classe `object`. Non è possibile per qualsiasi tipo da cui derivare un tipo di valore e tipi di valore in questo modo sono implicitamente sealed ([le classi Sealed](classes.md#sealed-classes)).

Si noti che `System.ValueType` non è a sua volta un *value_type*. Si tratta piuttosto di una *class_type* da cui tutti gli *value_type*derivano automaticamente.

### <a name="default-constructors"></a>Costruttori predefiniti

Tutti i tipi di valore dichiarano implicitamente un costruttore di istanza pubblico senza parametri denominato il ***costruttore predefinito***. Il costruttore predefinito restituisce un'istanza a inizializzazione zero nota come il ***il valore predefinito*** per il tipo di valore:

*  Per tutti i *simple_type*s, il valore predefinito è il valore restituito da uno schema di bit di tutti gli zero:
    * Per la `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, e `ulong`, il valore predefinito è `0`.
    * Per la `char`, il valore predefinito è `'\x0000'`.
    * Per la `float`, il valore predefinito è `0.0f`.
    * Per la `double`, il valore predefinito è `0.0d`.
    * Per la `decimal`, il valore predefinito è `0.0m`.
    * Per la `bool`, il valore predefinito è `false`.
*  Per un *enum_type* `E`, il valore predefinito è `0`, convertito nel tipo `E`.
*  Per un *struct_type*, il valore predefinito è il valore generato impostando tutti i campi di tipo di valore al relativo valore predefinito e riferimento a tutti i campi di tipo per `null`.
*  Per un *nullable_type* il valore predefinito è un'istanza per il quale il `HasValue` proprietà è false e `Value` proprietà non è definita. Il valore predefinito è noto anche come il ***valore null*** del tipo che ammette valori null.

Come qualsiasi altro costruttore di istanza, viene richiamato il costruttore predefinito di un tipo di valore usando il `new` operatore. Per motivi di efficienza, questo requisito non è pensato per generare una chiamata al costruttore. Nell'esempio seguente, le variabili `i` e `j` vengono inizializzate su zero.

```csharp
class A
{
    void F() {
        int i = 0;
        int j = new int();
    }
}
```

Poiché ogni tipo di valore ha implicitamente un costruttore di istanza pubblico senza parametri, non è possibile che un tipo struct contenere una dichiarazione esplicita di un costruttore senza parametri. Un tipo struct, tuttavia è possibile dichiarare costruttori di istanze con parametri ([costruttori](structs.md#constructors)).

### <a name="struct-types"></a>Tipi struct

Un tipo struct è un tipo di valore che è possibile dichiarare le costanti, campi, metodi, proprietà, indicizzatori, operatori, costruttori di istanza, costruttori statici e i tipi annidati. La dichiarazione di tipi struct è descritto nella [dichiarazioni Struct](structs.md#struct-declarations).

### <a name="simple-types"></a>Tipi semplici

C# offre un set di tipi di struct predefinito denominato il ***i tipi semplici***. I tipi semplici vengono identificati tramite le parole riservate, ma queste parole riservate sono semplicemente gli alias per i tipi struct predefiniti in di `System` dello spazio dei nomi, come descritto nella tabella seguente.


| __Parola riservata__ | __Tipo di alias__ |
|-------------------|------------------|
| `sbyte`           | `System.SByte`   | 
| `byte`            | `System.Byte`    | 
| `short`           | `System.Int16`   | 
| `ushort`          | `System.UInt16`  | 
| `int`             | `System.Int32`   | 
| `uint`            | `System.UInt32`  | 
| `long`            | `System.Int64`   | 
| `ulong`           | `System.UInt64`  | 
| `char`            | `System.Char`    | 
| `float`           | `System.Single`  | 
| `double`          | `System.Double`  | 
| `bool`            | `System.Boolean` | 
| `decimal`         | `System.Decimal` | 

Poiché un semplice tipo alias di un tipo struct, ogni tipo semplice ha membri. Ad esempio, `int` ha i membri dichiarati nella `System.Int32` e i membri ereditati da `System.Object`, e le istruzioni seguenti sono consentite:

```csharp
int i = int.MaxValue;           // System.Int32.MaxValue constant
string s = i.ToString();        // System.Int32.ToString() instance method
string t = 123.ToString();      // System.Int32.ToString() instance method
```

I tipi semplici si differenziano da altri tipi struct in quanto permettono alcune operazioni aggiuntive:

*  La maggior parte dei tipi semplici consentono di valori per essere create scrivendo *valori letterali* ([i valori letterali](lexical-structure.md#literals)). Ad esempio, `123` è un valore letterale di tipo `int` e `'a'` è un valore letterale di tipo `char`. C# non rende in genere non esiste alcun provisioning per i valori letterali dei tipi di struct e i valori non predefiniti di altri tipi di struct vengono sempre creati tramite i costruttori di istanza di tali tipi struct.
*  Quando gli operandi dell'espressione sono tutte costanti di tipo semplice, è possibile che il compilatore valutare l'espressione in fase di compilazione. Tale espressione è noto come un *constant_expression* ([espressioni costanti](expressions.md#constant-expressions)). Espressioni che includono gli operatori definiti da altri tipi di struct non sono considerate espressioni costanti.
*  Attraverso `const` dichiarazioni è possibile dichiarare le costanti dei tipi semplici ([costanti](classes.md#constants)). Non è possibile disporre di costanti di altri tipi di struct, ma viene fornito un risultato simile da `static readonly` campi.
*  Conversioni di tipi semplici possono far parte di valutazione degli operatori di conversione definiti da altri tipi di struct, ma un operatore di conversione definita dall'utente non può far parte di valutazione di un altro operatore definito dall'utente ([valutazione di conversioni definite dall'utente](conversions.md#evaluation-of-user-defined-conversions)).

### <a name="integral-types"></a>Tipi integrali

C# supporta nove tipi integrali: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, e `char`. I tipi integrali sono le dimensioni e gli intervalli dei valori seguenti:

*  Il `sbyte` rappresenta di tipo signed integer a 8 bit con valori compresi tra -128 e 127.
*  Il `byte` tipo rappresenta interi senza segno a 8 bit con valori compresi tra 0 e 255.
*  Il `short` rappresenta di tipo signed integer a 16 bit con valori compresi tra -32768 e 32767.
*  Il `ushort` tipo rappresenta interi senza segno a 16 bit con valori compresi tra 0 e 65535.
*  Il `int` rappresenta di tipo signed integer a 32 bit con valori compresi tra -2147483648 e 2147483647.
*  Il `uint` tipo rappresenta interi senza segno a 32 bit con valori compresi tra 0 e 4294967295.
*  Il `long` rappresenta di tipo signed integer a 64 bit con valori compresi tra -9223372036854775808 e 9223372036854775807.
*  Il `ulong` tipo rappresenta interi senza segno a 64 bit con valori compresi tra 0 e 18446744073709551615.
*  Il `char` tipo rappresenta interi senza segno a 16 bit con valori compresi tra 0 e 65535. Il set di valori possibili per il `char` tipo corrisponde al set di caratteri Unicode. Sebbene `char` ha la stessa rappresentazione di `ushort`, non tutte le operazioni consentite su un tipo sono consentite in altro.

Il tipo integrale operatori unari e binari operano sempre con precisione a 32 bit con segno, precisione a 32 bit senza segno, precisione a 64 bit con segno o senza segno a 64 bit precisione:

*  Per l'operatore unario `+` e `~` operatori, l'operando viene convertito nel tipo `T`, dove `T` è la prima delle `int`, `uint`, `long`, e `ulong` che può rappresentare completamente tutti valori possibili dell'operando. L'operazione viene quindi eseguita con la precisione del tipo `T`, e il tipo del risultato è `T`.
*  Per l'operatore unario `-` operatore, operando verrà convertito nel tipo `T`, dove `T` è la prima delle `int` e `long` che può rappresentare completamente tutti i valori possibili dell'operando. L'operazione viene quindi eseguita con la precisione del tipo `T`, e il tipo del risultato è `T`. Operatore unario `-` operatore non può essere applicato a operandi di tipo `ulong`.
*  Per il file binario `+`, `-`, `*`, `/`, `%`, `&`, `^`, `|`, `==`, `!=`, `>`, `<`, `>=`, e `<=` operatori, gli operandi verranno convertiti nel tipo `T`, dove `T` è la prima delle `int`, `uint`, `long`, e `ulong` che può rappresentare completamente tutte le possibili valori di entrambi gli operandi. L'operazione viene quindi eseguita con la precisione del tipo `T`, e il tipo del risultato è `T` (o `bool` per gli operatori relazionali). Non è consentito per un operando di tipo `long` e l'altro di tipo `ulong` con gli operatori binari.
*  Per il file binario `<<` e `>>` operatori, il secondo operando viene convertito nel tipo `T`, dove `T` è la prima delle `int`, `uint`, `long`, e `ulong` che può rappresentare completamente tutti valori possibili dell'operando. L'operazione viene quindi eseguita con la precisione del tipo `T`, e il tipo del risultato è `T`.

Il `char` tipo viene classificato come un tipo integrale, ma differisce dagli altri tipi integrali in due modi:

*  Non sono presenti conversioni implicite di altri tipi di `char` tipo. In particolare, anche se il `sbyte`, `byte`, e `ushort` tipi hanno intervalli di valori che sono completamente rappresentabile utilizzando la `char` digita, le conversioni implicite dal `sbyte`, `byte`, o `ushort` a `char` non esistono.
*  Costanti del `char` tipo deve essere scritto come *character_literal*s o come *integer_literal*s in combinazione con un cast al tipo `char`. Ad esempio, `(char)10` equivale a `'\x000A'`.

Il `checked` e `unchecked` istruzioni e gli operatori consentono di controllare controllo di overflow per conversioni e operazioni aritmetiche di tipo integrale ([gli operatori checked e unchecked](expressions.md#the-checked-and-unchecked-operators)). In un `checked` contesto, un overflow genera un errore in fase di compilazione o causa un `System.OverflowException` generata. In un `unchecked` contesto di overflow vengono ignorati e vengono eliminati tutti i bit più significativi che non rientrano nel tipo di destinazione.

### <a name="floating-point-types"></a>Tipi a virgola mobile

C# supporta due tipi a virgola mobile: `float` e `double`. Il `float` e `double` tipi vengono rappresentati usando i 32 bit e precisione singola e a 64 bit a precisione doppia IEEE 754 formati, che forniscono i seguenti insiemi di valori:

*  Positivo zero e zero negativo. Nella maggior parte delle situazioni, zero positivo e negativo zero hanno lo stesso comportamento come valore semplice da zero, ma alcune operazioni di distinguono tra i due ([operatore di divisione](expressions.md#division-operator)).
*  Infinito positivo e un numero infinito negativo. Valori infiniti vengono prodotte da operazioni quali la divisione di un numero diverso da zero per zero. Ad esempio, `1.0 / 0.0` produce un numero infinito positivo, e `-1.0 / 0.0` restituisce infinito negativo.
*  Il ***non un numero*** valore, spesso abbreviato NaN. NaN generati dalle operazioni a virgola mobile non valide, ad esempio divisione per zero di zero.
*  Insieme finito di valori zero nel formato `s * m * 2^e`, dove `s` è 1 o -1, e `m` e `e` dipendono dal tipo a virgola mobile particolare: Per la `float`, `0 < m < 2^24` e `-149 <= e <= 104`e per `double`, `0 < m < 2^53` e `1075 <= e <= 970`. Numeri a virgola mobile denormalizzati sono considerati validi valori diversi da zero.

Il `float` tipo può rappresentare valori compresi tra circa `1.5 * 10^-45` a `3.4 * 10^38` con una precisione di 7 cifre.

Il `double` tipo può rappresentare valori compresi tra circa `5.0 * 10^-324` a `1.7 × 10^308` con un'approssimazione di 15-16 cifre.

Se uno degli operandi di un operatore binario è di tipo a virgola mobile, quindi l'altro operando deve essere di un tipo integrale o un tipo a virgola mobile e l'operazione viene valutata nel modo seguente:

*  Se uno degli operandi è di tipo integrale, tale operando verrà convertito nel tipo a virgola mobile di altro operando.
*  Quindi, se uno degli operandi è di tipo `double`, l'altro operando viene convertito in `double`, l'operazione viene eseguita utilizzando almeno `double` intervallo e precisione e il tipo del risultato viene `double` (o `bool` per il operatori relazionali).
*  In caso contrario, l'operazione viene eseguita utilizzando almeno `float` intervallo e precisione e il tipo del risultato viene `float` (o `bool` per gli operatori relazionali).

Gli operatori a virgola mobile, inclusi gli operatori di assegnazione, mai generano eccezioni. In situazioni eccezionali, invece, le operazioni a virgola mobile e producono zero, infinito o NaN, come descritto di seguito:

*  Se il risultato di un'operazione a virgola mobile è troppo piccolo per il formato di destinazione, il risultato dell'operazione diventa zero positivo o negativo.
*  Se il risultato di un'operazione a virgola mobile è troppo grande per il formato di destinazione, il risultato dell'operazione sarà infinito positivo o negativo.
*  Se un'operazione a virgola mobile non è valida, il risultato dell'operazione diventa NaN.
*  Se uno o entrambi gli operandi di un'operazione a virgola mobile è NaN, il risultato dell'operazione diventa NaN.

Operazioni a virgola mobile possono essere eseguite con una maggiore precisione rispetto al tipo di risultato dell'operazione. Ad esempio, alcune architetture hardware supportano un tipo a virgola mobile "extended" o "long double" con una gamma più ampia e offre una precisione maggiore di `double` digitare ed eseguire in modo implicito tutte le operazioni a virgola mobile con questo tipo con precisione superiore. Comporterebbe costi di prestazioni tali architetture hardware per poter eseguire operazioni a virgola mobile con precisione inferiore, e invece di richiedere un'implementazione per rinunciare le prestazioni e la precisione, c# consente di essere un tipo di precisione superiore utilizzato per tutte le operazioni a virgola mobile. Diverso da fornire risultati più precisi, questo raramente ha effetti misurabili. Tuttavia, nelle espressioni del form `x * y / z`, in cui il risultato della moltiplicazione che non è compreso il `double` intervallo, ma la divisione successiva Visualizza i risultati temporanei nel `double` intervallo, il fatto che l'espressione è valutata in un intervallo più alto formato può causare un risultato finito anziché un numero infinito.

### <a name="the-decimal-type"></a>Il tipo decimal

Il tipo `decimal` è un tipo dati a 128 bit adatto per i calcoli finanziari e monetari. Il `decimal` tipo può rappresentare valori compresi tra `1.0 * 10^-28` a circa `7.9 * 10^28` con 28-29 cifre significative.

Insieme finito di valori di tipo `decimal` hanno la forma `(-1)^s * c * 10^-e`, dove il segno `s` è 0 o 1, il coefficiente `c` è dato dai `0 <= *c* < 2^96`e la scala `e` è tale che `0 <= e <= 28`. Il `decimal` tipo nepodporuje zero con segno, valori infiniti o NaN. Oggetto `decimal` è rappresentato come intero a 96 bit scalato in base a una potenza di dieci. Per la `decimal`con un valore assoluto minore `1.0m`, il valore è esatta per la posizione decimale 28, ma non oltre. Per la `decimal`con un valore assoluto maggiore o uguale a `1.0m`, il valore è esatto a 28 o 29 cifre. Contrario al `float` e `double` tipi di dati, i numeri decimali frazionari come 0.1 possono essere rappresentati esattamente nel `decimal` rappresentazione. Nel `float` e `double` rappresentazioni, questi numeri sono spesso infinite frazioni, rendendo più inclini a arrotondamento errori.

Se uno degli operandi di un operatore binario JE typu `decimal`, quindi l'altro operando deve essere di tipo integrale o di tipo `decimal`. Se è presente un operando di tipo integrale, questo viene convertito in `decimal` prima che l'operazione viene eseguita.

Il risultato di un'operazione sui valori di tipo `decimal` è che si otterrebbe calcolando un risultato esatto (mantenimento scalabilità, come definito per ogni operatore presente) e quindi arrotondando per adattare la rappresentazione. I risultati vengono arrotondati per di più prossimo valore rappresentabile, e, quando il risultato è egualmente vicino a due valori rappresentabili, il valore con un numero pari alla posizione meno significativo (questo è noto come "il metodo di arrotondamento"). Alcun risultato, ha sempre un segno di 0 e una scala pari a 0.

Se un'operazione aritmetica decimale produce un valore minore o uguale a `5 * 10^-29` in valore assoluto, il risultato dell'operazione diventa zero. Se un `decimal` aritmetica produce un risultato troppo grande per il `decimal` formato, un `System.OverflowException` viene generata un'eccezione.

Il `decimal` tipo ha una precisione maggiore intervallo di dimensioni ridotte rispetto ai tipi a virgola mobile. Di conseguenza, le conversioni dai tipi a virgola mobile a `decimal` potrebbe generare eccezioni di overflow e le conversioni da `decimal` ai tipi a virgola mobile potrebbe causare la perdita di precisione. Per questi motivi, non esiste alcuna conversione implicite tra i tipi a virgola mobile e `decimal`, e senza cast espliciti, non è possibile combinare a virgola mobile e `decimal` operandi nella stessa espressione.

### <a name="the-bool-type"></a>Il tipo bool

Il `bool` tipo rappresenta le quantità di logiche booleane. I possibili valori di tipo `bool` vengono `true` e `false`.

Non esiste alcuna conversione standard tra `bool` e altri tipi. In particolare, il `bool` è di tipo distinto e separato da tipi integrali e un' `bool` valore non può essere utilizzato al posto di un valore integrale e viceversa.

Nei linguaggi C e C++, un valore pari a zero integrale o a virgola mobile o un puntatore null può essere convertito nel valore booleano `false`, e un valore integrale o a virgola mobile e diverso da zero o un puntatore null non può essere convertito nel valore booleano `true`. In c#, queste conversioni vengono portate a termine confrontando in modo esplicito un valore integrale o a virgola mobile a zero o confrontando in modo esplicito un riferimento a oggetto `null`.

### <a name="enumeration-types"></a>Tipi di enumerazione

Un tipo di enumerazione è un tipo distinto con costanti denominate. Ogni tipo di enumerazione ha un tipo sottostante, che deve essere `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` o `ulong`. Il set di valori del tipo di enumerazione è quello utilizzato per il set di valori del tipo sottostante. Valori del tipo di enumerazione non sono limitati ai valori delle costanti denominate. I tipi di enumerazione vengono definiti tramite le dichiarazioni di enumerazione ([le dichiarazioni di enumerazione](enums.md#enum-declarations)).

### <a name="nullable-types"></a>Tipi nullable

Un tipo nullable può rappresentare tutti i valori del relativo ***tipo sottostante*** oltre a un altro valore null. Un tipo nullable è scritto `T?`, dove `T` è il tipo sottostante. Questa sintassi è la sintassi abbreviata per `System.Nullable<T>`, e le due forme possono essere usate in modo intercambiabile.

Oggetto ***tipo di valore non nullable*** viceversa è qualsiasi tipo di valore diverso da `System.Nullable<T>` e dalla relativa abbreviazione `T?` (per qualsiasi `T`), oltre a qualsiasi parametro di tipo vincolato a essere un tipo di valore non nullable (ovvero, qualsiasi parametro di tipo con un `struct` vincolo). Il `System.Nullable<T>` tipo specifica il vincolo di tipo valore per `T` ([digitare i vincoli del parametro](classes.md#type-parameter-constraints)), il che significa che il tipo sottostante di un tipo nullable può essere qualsiasi tipo di valore non nullable. Il tipo sottostante di un tipo nullable non può essere un tipo nullable o un tipo riferimento. Ad esempio, `int??` e `string?` sono tipi non è validi.

Un'istanza di un tipo nullable `T?` ha due proprietà pubbliche di sola lettura:

*  Oggetto `HasValue` vlastnosti typu `bool`
*  Oggetto `Value` vlastnosti typu `T`

Un'istanza per il quale `HasValue` è true viene definito per essere non null. Un'istanza non null contiene un valore noto e `Value` restituisce tale valore.

Un'istanza per il quale `HasValue` è FALSO è detto null. Un'istanza null è un valore indefinito. Tentativo di leggere il `Value` di un'istanza null causa un `System.InvalidOperationException` generata. Il processo di accesso di `Value` proprietà di un'istanza che ammette valori null è detta ***eseguire l'operazione Unwrap***.

Oltre al costruttore predefinito, tutti i tipi nullable `T?` ha un costruttore pubblico che accetta un singolo argomento di tipo `T`. Dato un valore `x` di tipo `T`, una chiamata del costruttore del form

```csharp
new T?(x)
```
Crea un'istanza non null di `T?` per il quale il `Value` è di proprietà `x`. Il processo di creazione di un'istanza non null di un tipo che ammette valori null per un determinato valore viene definito ***wrapping***.

Le conversioni implicite sono disponibili il `null` valore letterale `T?` ([conversioni di valori letterali Null](conversions.md#null-literal-conversions)) e da `T` a `T?` ([conversioni implicite nullable](conversions.md#implicit-nullable-conversions)).

## <a name="reference-types"></a>Tipi riferimento

Un tipo di riferimento è un tipo di classe, un tipo di interfaccia, un tipo di matrice o un tipo delegato.

```antlr
reference_type
    : class_type
    | interface_type
    | array_type
    | delegate_type
    ;

class_type
    : type_name
    | 'object'
    | 'dynamic'
    | 'string'
    ;

interface_type
    : type_name
    ;

array_type
    : non_array_type rank_specifier+
    ;

non_array_type
    : type
    ;

rank_specifier
    : '[' dim_separator* ']'
    ;

dim_separator
    : ','
    ;

delegate_type
    : type_name
    ;
```

Un valore di tipo riferimento è un riferimento a un ***istanza*** del tipo, nota come un' ***oggetto***. Il valore speciale `null` è compatibile con tutti i tipi riferimento e indica l'assenza di un'istanza.

### <a name="class-types"></a>Tipi classe

Un tipo di classe definisce una struttura di dati che contiene i membri dati costanti e i campi, membri funzione (metodi, proprietà, eventi, indicizzatori, operatori, costruttori di istanze, distruttori e i costruttori statici) e i tipi annidati. I tipi classe supportano l'ereditarietà, un meccanismo in base al quale le classi derivate possono estendere e specializzare le classi di base. Le istanze di tipi di classe vengono create utilizzando *object_creation_expression*s ([espressioni per la creazione dell'oggetto](expressions.md#object-creation-expressions)).

Tipi di classe sono descritte nel [classi](classes.md).

Alcuni tipi di classe predefiniti hanno un significato speciale in c#, come descritto nella tabella seguente.


| __Tipo di classe__     | __Descrizione__                                         |
|--------------------|---------------------------------------------------------|
| `System.Object`    | La classe di base fondamentale di tutti gli altri tipi. Visualizzare [il tipo di oggetto](types.md#the-object-type). | 
| `System.String`    | Il tipo di stringa del linguaggio c#. Visualizzare [il tipo di stringa](types.md#the-string-type).         |
| `System.ValueType` | Classe di base di tutti i tipi di valore. Visualizzare [System. ValueType di tipo](types.md#the-systemvaluetype-type).          |
| `System.Enum`      | Classe di base di tutti i tipi di enumerazione. Visualizzare [enumerazioni](enums.md).              |
| `System.Array`     | Classe di base di tutti i tipi di matrice. Vdere [Matrici](arrays.md).             |
| `System.Delegate`  | Classe di base di tutti i tipi di delegato. Visualizzare [delegati](delegates.md).          |
| `System.Exception` | Classe di base di tutti i tipi di eccezione. Visualizzare [eccezioni](exceptions.md).         |

### <a name="the-object-type"></a>Il tipo di oggetto

Il `object` il tipo di classe è la classe di base fondamentale di tutti gli altri tipi. In c# ogni tipo deriva direttamente o indirettamente dal `object` tipo di classe.

La parola chiave `object` è semplicemente un alias per la classe predefinita `System.Object`.

### <a name="the-dynamic-type"></a>Il tipo dinamico

Il `dynamic` digitare, ad esempio `object`, può fare riferimento a qualsiasi oggetto. Quando gli operatori vengono applicati alle espressioni di tipo `dynamic`, le relative soluzioni viene posticipata fino a quando non viene eseguito il programma. Di conseguenza, se l'operatore non può essere legalmente applicato all'oggetto di riferimento, viene restituito alcun errore durante la compilazione. Al contrario verrà generata un'eccezione durante la risoluzione dell'operatore non riesce in fase di esecuzione.

Il suo scopo è consentire l'associazione dinamica, descritto dettagliatamente nelle [binding dinamico](expressions.md#dynamic-binding).

`dynamic` è considerato identico al `object` tranne che per i seguenti aspetti:

*  Operazioni su espressioni di tipo `dynamic` può essere associato in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)).
*  L'inferenza del tipo ([inferenza del tipo](expressions.md#type-inference)) preferiranno `dynamic` failover `object` se entrambi sono candidati.

A causa di questa equivalenza, tenere presente quanto segue:

*  È una conversione di identità implicite tra `object` e `dynamic`e tra tipi costruiti che sono gli stessi quando si sostituiscono `dynamic` con `object`
*  Le conversioni implicite ed esplicite da e verso `object` si applicano anche a e da `dynamic`.
*  Firme del metodo che sono gli stessi quando si sostituiscono `dynamic` con `object` sono considerate la stessa firma
*  Il tipo `dynamic` non è distinguibile da `object` in fase di esecuzione.
*  Un'espressione di tipo `dynamic` viene definito un ***espressione dinamica***.

### <a name="the-string-type"></a>Il tipo di stringa

Il `string` tipo è un tipo di classe sealed che eredita direttamente da `object`. Le istanze del `string` classe rappresentano stringhe di caratteri Unicode.

I valori del `string` tipo può essere scritte come valori letterali stringa ([valori letterali stringa](lexical-structure.md#string-literals)).

La parola chiave `string` è semplicemente un alias per la classe predefinita `System.String`.

### <a name="interface-types"></a>Tipi interfaccia

Un'interfaccia definisce un contratto. Una classe o struct che implementa un'interfaccia deve aderire al rispettivo contratto. Un'interfaccia può ereditare da più interfacce di base e una classe o struct può implementare più interfacce.

Tipi di interfaccia sono descritte nel [interfacce](interfaces.md).

### <a name="array-types"></a>Tipi matrice

Una matrice è una struttura di dati che contiene zero o più variabili accessibili tramite indici calcolati. Le variabili contenute in una matrice, chiamata anche gli elementi della matrice, sono tutti dello stesso tipo, e questo tipo viene definito il tipo di elemento della matrice.

I tipi matrice sono descritte nel [matrici](arrays.md).

### <a name="delegate-types"></a>Tipi delegato

Un delegato è una struttura di dati che fa riferimento a uno o più metodi. Ad esempio metodi, si intende anche istanze degli oggetti corrispondenti.

L'equivalente più vicino di un delegato in C o C++ è un puntatore a funzione, ma mentre un puntatore a funzione può fare riferimento solo a funzioni statiche, un delegato può fare riferimento sia statici e metodi di istanza. Nel secondo caso, il delegato archivia non solo un riferimento al punto di ingresso del metodo, ma anche un riferimento all'istanza dell'oggetto sul quale richiamare il metodo.

I tipi delegati sono descritte nel [delegati](delegates.md).

## <a name="boxing-and-unboxing"></a>Boxing e unboxing

Il concetto di conversione boxing e unboxing è fondamentale per il sistema di tipi di #. Fornisce un collegamento tra *value_type*s e *reference_type*s consentono qualsiasi valore di una *value_type* da convertire in e dal tipo `object`. Boxing e unboxing consente una visualizzazione unificata del sistema di tipi in cui un valore di qualsiasi tipo può essere considerato in definitiva come oggetto.

### <a name="boxing-conversions"></a>Conversioni boxing

Una conversione boxing consente una *value_type* da convertire in modo implicito in un *reference_type*. Sono disponibili le conversioni boxing seguenti:

*  Da qualsiasi *value_type* al tipo `object`.
*  Da qualsiasi *value_type* al tipo `System.ValueType`.
*  Da qualsiasi *non_nullable_value_type* a qualsiasi *interface_type* implementata dal *value_type*.
*  Da qualsiasi *nullable_type* a qualsiasi *interface_type* implementate dal tipo sottostante del *nullable_type*.
*  Da qualsiasi *enum_type* al tipo `System.Enum`.
*  Da qualsiasi *nullable_type* con un oggetto sottostante *enum_type* al tipo `System.Enum`.
*  Notare che verrà eseguita una conversione implicita da un parametro di tipo come conversione boxing se in fase di esecuzione viene eseguita la conversione da un tipo di valore a un tipo riferimento ([conversioni implicite con i parametri di tipo](conversions.md#implicit-conversions-involving-type-parameters)).

Conversione boxing di un valore di una *non_nullable_value_type* consiste nell'allocare un'istanza dell'oggetto e copia le *non_nullable_value_type* valore in tale istanza.

Conversione boxing di un valore di una *nullable_type* produce un riferimento null se è la `null` valore (`HasValue` è `false`), o il risultato dell'annullamento del wrapping e conversione boxing in caso contrario, il valore sottostante.

L'effettivo processo di conversione boxing di un valore di una *non_nullable_value_type* è la risposta è chiarita immaginando l'esistenza di un oggetto generico ***classe boxing***, che si comporta come se fosse dichiarato come indicato di seguito:

```csharp
sealed class Box<T>: System.ValueType
{
    T value;

    public Box(T t) {
        value = t;
    }
}
```

Conversione boxing di un valore `v` typu `T` ora è costituito da esegue l'espressione `new Box<T>(v)`e restituendo l'istanza risultante come un valore di tipo `object`. Di conseguenza, le istruzioni
```csharp
int i = 123;
object box = i;
```
a livello concettuale corrispondono alle
```csharp
int i = 123;
object box = new Box<int>(i);
```

Una classe di conversione boxing, ad esempio `Box<T>` sopra in realtà non esiste e il tipo dinamico di un valore boxed non è effettivamente un tipo di classe. Al contrario, valore boxed del tipo `T` ha il tipo dinamico `T`e un controllo di tipo dinamico mediante il `is` operatore può semplicemente fare riferimento a tipo `T`. Ad esempio,
```csharp
int i = 123;
object box = i;
if (box is int) {
    Console.Write("Box contains an int");
}
```
restituirà la stringa "`Box contains an int`" nella console.

Conversione boxing implica la copia del valore da convertire. Questo comportamento è diverso rispetto alla conversione di un *reference_type* al tipo `object`, in cui il valore continua a fare riferimento alla stessa istanza e viene considerato solo come tipo meno derivato `object`. Ad esempio, considerando la dichiarazione
```csharp
struct Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
le istruzioni seguenti
```csharp
Point p = new Point(10, 10);
object box = p;
p.x = 20;
Console.Write(((Point)box).x);
```
restituirà il valore 10 nella console perché l'operazione di conversione boxing implicita che si verifica l'assegnazione di `p` al `box` fa sì che il valore di `p` da copiare. Aveva `Point` stato dichiarato un `class` invece il valore 20 sarebbe output poiché `p` e `box` fa riferimento alla stessa istanza.

### <a name="unboxing-conversions"></a>Conversione unboxing

Una conversione unboxing consente una *reference_type* da convertire in modo esplicito in un *value_type*. Sono disponibili le seguenti conversioni unboxing:

*  Dal tipo `object` a qualsiasi *value_type*.
*  Dal tipo `System.ValueType` a qualsiasi *value_type*.
*  Da qualsiasi *interface_type* a qualsiasi *non_nullable_value_type* che implementa il *interface_type*.
*  Da qualsiasi *interface_type* a qualsiasi *nullable_type* il cui tipo sottostante implementa il *interface_type*.
*  Dal tipo `System.Enum` a qualsiasi *enum_type*.
*  Dal tipo `System.Enum` a qualsiasi *nullable_type* con un oggetto sottostante *enum_type*.
*  Notare che verrà eseguita una conversione esplicita di un parametro di tipo come una conversione unboxing se in fase di esecuzione viene eseguita la conversione da un tipo riferimento a un tipo valore ([conversioni esplicite dinamiche](conversions.md#explicit-dynamic-conversions)).

Un'operazione di unboxing un *non_nullable_value_type* è costituito da verificare prima che l'istanza dell'oggetto è un valore boxed del determinato *non_nullable_value_type*e quindi copiando il valore fuori il istanza.

Conversione unboxing di un *nullable_type* produce il valore null delle *nullable_type* se l'operando di origine è `null`, o il risultato con wrapping di unboxing l'istanza dell'oggetto per il tipo sottostante del *nullable_type* in caso contrario.

Che fa riferimento alla classe di conversione boxing immaginaria descritta nella sezione precedente, una conversione unboxing di un oggetto `box` a un *value_type* `T` corrisponde all'esecuzione dell'espressione `((Box<T>)box).value`. Di conseguenza, le istruzioni
```csharp
object box = 123;
int i = (int)box;
```
a livello concettuale corrispondono alle
```csharp
object box = new Box<int>(123);
int i = ((Box<int>)box).value;
```

Per una conversione unboxing di un determinato *non_nullable_value_type* per la corretta in fase di esecuzione, il valore dell'operando di origine deve essere un riferimento a un valore boxed di tale *non_nullable_value_type*. Se l'operando di origine è `null`, un `System.NullReferenceException` viene generata un'eccezione. Se l'operando di origine è un riferimento a un oggetto incompatibile, un `System.InvalidCastException` viene generata un'eccezione.

Per una conversione unboxing di un determinato *nullable_type* per la corretta in fase di esecuzione, il valore dell'operando di origine deve essere `null` o un riferimento a un valore boxed dell'oggetto sottostante *non_nullable_value_type* del *nullable_type*. Se l'operando di origine è un riferimento a un oggetto incompatibile, un `System.InvalidCastException` viene generata un'eccezione.

## <a name="constructed-types"></a>Tipi costruiti

Una dichiarazione di tipo generico, di per sé, denota un ***annullare l'associazione di tipo generico*** che viene utilizzato come un' "istantanea" in modo da formare vari tipi differenti, applicando ***gli argomenti di tipo***. Gli argomenti di tipo vengono scritti tra parentesi angolari (`<` e `>`) subito dopo il nome del tipo generico. Un tipo che include almeno un argomento di tipo viene chiamato un ***tipo costruito***. Un tipo costruito è utilizzabile nella maggior parte delle posizioni nel linguaggio in cui può essere visualizzato un nome di tipo. Un tipo generico non associato può essere usato solo all'interno di un *typeof_expression* ([l'operatore typeof](expressions.md#the-typeof-operator)).

Tipi costruiti possono anche essere utilizzati nelle espressioni come nomi semplici ([nomi semplici](expressions.md#simple-names)) o quando si accede a un membro ([l'accesso ai membri](expressions.md#member-access)).

Quando un *namespace_or_type_name* viene valutati, solo i tipi generici con il numero corretto di parametri sono considerati di tipo. Di conseguenza, è possibile usare lo stesso identificatore per identificare i tipi diversi, purché i tipi hanno diversi numeri di parametri di tipo. Ciò è utile quando si combinano le classi generiche e non generici nello stesso programma:

```csharp
namespace Widgets
{
    class Queue {...}
    class Queue<TElement> {...}
}

namespace MyApplication
{
    using Widgets;

    class X
    {
        Queue q1;            // Non-generic Widgets.Queue
        Queue<int> q2;       // Generic Widgets.Queue
    }
}
```

Oggetto *type_name* può identificare un tipo costruito, anche se non specifica i parametri di tipo direttamente. Ciò può verificarsi in cui un tipo annidato all'interno di una dichiarazione di classe generica, e il tipo di istanza di dichiarazione che lo contiene è implicitamente per la ricerca del nome ([annidati di tipi in classi generiche](classes.md#nested-types-in-generic-classes)):

```csharp
class Outer<T>
{
    public class Inner {...}

    public Inner i;                // Type of i is Outer<T>.Inner
}
```

Nel codice unsafe, un tipo costruito non può essere utilizzato come un *unmanaged_type* ([tipi di puntatore](unsafe-code.md#pointer-types)).

### <a name="type-arguments"></a>Argomenti di tipo

Ciascun argomento in un elenco di argomenti è semplicemente una *tipo*.

```antlr
type_argument_list
    : '<' type_arguments '>'
    ;

type_arguments
    : type_argument (',' type_argument)*
    ;

type_argument
    : type
    ;
```

Nel codice non sicuro ([codice Unsafe](unsafe-code.md)), un *type_argument* potrebbe non essere un tipo di puntatore. Ogni argomento tipo deve soddisfare tutti i vincoli nel corrispondente parametro di tipo ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)).

### <a name="open-and-closed-types"></a>Tipi aperti e chiusi

Tutti i tipi possono essere classificati come ***tipi aperti*** oppure ***tipi chiusi***. Un tipo aperto è un tipo che prevede parametri di tipo. In particolare:

*  Un parametro di tipo definisce un tipo aperto.
*  Un tipo di matrice è un tipo aperto solo se il tipo di elemento è un tipo aperto.
*  Un tipo costruito è un tipo aperto solo se uno o più dei relativi argomenti di tipo è un tipo aperto. Un tipo costruito annidato è un tipo aperto solo se uno o più dei relativi argomenti di tipo o gli argomenti di tipo dei relativi tipi che lo contiene è un tipo aperto.

Un tipo chiuso è un tipo che non è un tipo aperto.

In fase di esecuzione, tutto il codice all'interno di una dichiarazione di tipo generico viene eseguita nel contesto di un tipo costruito chiuso che è stato creato applicando gli argomenti tipo per la dichiarazione generica. Ogni parametro di tipo all'interno del tipo generico è associato a un determinato tipo in fase di esecuzione. Si verifica sempre l'elaborazione in fase di esecuzione di tutte le istruzioni e le espressioni con tipi chiusi e i tipi aperti si verificano solo durante la fase di compilazione l'elaborazione.

Ogni tipo costruito chiuso ha un proprio set di variabili statiche, che non sono condivise con qualsiasi altro tipo costruito chiuso. Poiché non esiste un tipo aperto in fase di esecuzione, vi sono variabili statiche associate a un tipo aperto. Due tipi costruiti chiusi sono dello stesso tipo se vengono costruiti dallo stesso tipo generico non associato e relativi argomenti di tipo corrispondenti sono dello stesso tipo.

### <a name="bound-and-unbound-types"></a>Tipi associati e non associati

Il termine ***annullare l'associazione di tipo*** fa riferimento a un tipo non generico o un tipo generico non associato. Il termine ***associata tipo*** fa riferimento a un tipo non generico o un tipo costruito.

Un tipo non associato fa riferimento all'entità dichiarato da una dichiarazione del tipo. Un tipo generico non associato non è un tipo e non può essere utilizzato come tipo di variabile, argomento o valore restituito o come un tipo di base. Il costrutto solo in cui è possibile fare riferimento un tipo generico non associato è il `typeof` espressione ([l'operatore typeof](expressions.md#the-typeof-operator)).

### <a name="satisfying-constraints"></a>Rispetto dei vincoli

Ogni volta che un tipo costruito o metodo generico viene fatto riferimento, in base ai vincoli di parametro di tipo dichiarati per il tipo o metodo generico vengono controllati gli argomenti di tipo fornito ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)). Per ognuno `where` clausola, l'argomento di tipo `A` che corrisponde al denominato parametro di tipo viene confrontato con ogni vincolo come indicato di seguito:

*  Se il vincolo è un parametro di tipo, un tipo di interfaccia o un tipo di classe, lasciar `C` rappresentano che un vincolo con gli argomenti di tipo fornito sostituito per qualsiasi parametro di tipo che vengono visualizzati nel vincolo. Per soddisfare il vincolo, deve essere il caso indipendente dai tipi `A` convertibile nel tipo `C` da uno dei seguenti:
    * Una conversione di identità ([conversione di identità](conversions.md#identity-conversion))
    * Una conversione implicita del riferimento ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions))
    * Conversione boxing ([conversioni Boxing](conversions.md#boxing-conversions)), a condizione che il tipo è un tipo di valore non nullable.
    * Riferimento, la conversione boxing o tipo di parametro conversione implicita da un parametro di tipo `A` a `C`.
*  Se il vincolo è il vincolo di tipo riferimento (`class`), il tipo `A` deve soddisfare uno dei seguenti:
    * `A` è un tipo di interfaccia, tipo di classe, tipo di delegato o tipo di matrice. Si noti che `System.ValueType` e `System.Enum` sono tipi di riferimento che soddisfano questo vincolo.
    * `A` è un parametro di tipo noto per essere un tipo riferimento ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)).
*  Se il vincolo è il vincolo di tipo valore (`struct`), il tipo `A` deve soddisfare uno dei seguenti:
    * `A` è un tipo struct o tipo di enumerazione, ma non un tipo nullable. Si noti che `System.ValueType` e `System.Enum` sono tipi di riferimento che non soddisfano questo vincolo.
    * `A` è un parametro di tipo con il vincolo di tipo valore ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)).
*  Se il vincolo è il vincolo del costruttore `new()`, il tipo `A` non deve essere `abstract` e deve avere un costruttore pubblico senza parametri. Ciò viene soddisfatta se viene soddisfatta una delle operazioni seguenti:
    * `A` è un tipo di valore, poiché tutti i tipi valore hanno un costruttore pubblico predefinito ([costruttori predefiniti](types.md#default-constructors)).
    * `A` è un parametro di tipo con il vincolo del costruttore ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)).
    * `A` è un parametro di tipo con il vincolo di tipo valore ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)).
    * `A` è una classe che non è `abstract` e contiene un dichiarato in modo esplicito `public` costruttore senza parametri.
    * `A` non è `abstract` e ha un costruttore predefinito ([costruttori predefiniti](classes.md#default-constructors)).

Se uno o più vincoli del parametro di tipo non sono soddisfatte dagli argomenti di tipo specificato, si verifica un errore in fase di compilazione.

Poiché i parametri di tipo non vengono ereditati, i vincoli non sono mai ereditate uno. Nell'esempio riportato di seguito `D` deve specificare il vincolo nel parametro di tipo `T` in modo che `T` soddisfa il vincolo imposto dalla classe di base `B<T>`. Classe invece `E` non è necessario specificare un vincolo, in quanto `List<T>` implementa `IEnumerable` per qualsiasi `T`.

```csharp
class B<T> where T: IEnumerable {...}

class D<T>: B<T> where T: IEnumerable {...}

class E<T>: B<List<T>> {...}
```

## <a name="type-parameters"></a>Parametri di tipo

Un parametro di tipo è un identificatore che designa un tipo valore o tipo di riferimento che è associato il parametro in fase di esecuzione.

```antlr
type_parameter
    : identifier
    ;
```

Poiché è possibile creare istanze di un parametro di tipo con molti argomenti di tipo effettivo diverso, i parametri di tipo sono leggermente diversi di operazioni e le restrizioni rispetto ad altri tipi. Sono inclusi:

*  Un parametro di tipo non può essere utilizzato direttamente per dichiarare una classe di base ([classe di Base](classes.md#base-class)) o l'interfaccia ([elenchi di parametri di tipo variante](interfaces.md#variant-type-parameter-lists)).
*  Le regole per la ricerca di membri nel tipo di parametri dipendono i vincoli, se presente, applicato al parametro di tipo. Sono descritti in dettaglio nelle [ricerca di membri](expressions.md#member-lookup).
*  Le conversioni disponibili per un parametro di tipo variano in base ai vincoli, se presente, applicato al parametro di tipo. Sono descritti in dettaglio nelle [conversioni implicite con i parametri di tipo](conversions.md#implicit-conversions-involving-type-parameters) e [conversioni esplicite dinamiche](conversions.md#explicit-dynamic-conversions).
*  Il valore letterale `null` non può essere convertito in un tipo specificato da un parametro di tipo, eccetto se il parametro di tipo è noto per essere un tipo riferimento ([conversioni implicite con i parametri di tipo](conversions.md#implicit-conversions-involving-type-parameters)). Tuttavia, un `default` espressione ([espressioni con valore predefinito](expressions.md#default-value-expressions)) è invece possibile usare. Inoltre, un valore con un tipo specificato da un parametro di tipo può essere confrontato con `null` usando `==` e `!=` ([gli operatori di uguaglianza di riferimenti tipo](expressions.md#reference-type-equality-operators)), a meno che il parametro di tipo contiene il vincolo di tipo valore.
*  Oggetto `new` espressione ([espressioni per la creazione dell'oggetto](expressions.md#object-creation-expressions)) può essere usato con un parametro di tipo solo se il parametro di tipo è vincolato da una *constructor_constraint* o il valore (vincoloditipo[ Digitare i vincoli del parametro](classes.md#type-parameter-constraints)).
*  Un parametro di tipo non possa essere utilizzato ovunque all'interno di un attributo.
*  Un parametro di tipo non può essere usato in un accesso ai membri ([accesso al membro](expressions.md#member-access)) o nome di tipo ([Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)) per identificare un membro statico o un tipo annidato.
*  Nel codice unsafe, un parametro di tipo non può essere utilizzato come un *unmanaged_type* ([tipi di puntatore](unsafe-code.md#pointer-types)).

Come un tipo, i parametri di tipo sono esclusivamente un costrutto in fase di compilazione. In fase di esecuzione, ogni parametro di tipo è associato a un tipo in fase di esecuzione che è stato specificato, fornendo un argomento di tipo per la dichiarazione di tipo generico. Di conseguenza, il tipo di una variabile dichiarata con un parametro di tipo sarà, in fase di esecuzione, essere un tipo costruito chiuso ([aperto e chiuso tipi](types.md#open-and-closed-types)). La fase di esecuzione di tutte le istruzioni e le espressioni che includono parametri di tipo viene utilizzato il tipo effettivo che è stato fornito come argomento di tipo per quel parametro.

## <a name="expression-tree-types"></a>Tipi di albero delle espressioni

***Gli alberi delle espressioni*** consentire le espressioni lambda per essere rappresentato come strutture dei dati anziché codice eseguibile. Gli alberi delle espressioni sono valori di ***tipi di albero delle espressioni*** del form `System.Linq.Expressions.Expression<D>`, dove `D` è qualsiasi tipo di delegato. Nella parte restante di questa specifica si farà riferimento a questi tipi usando la sintassi abbreviata `Expression<D>`.

Se esiste una conversione da un'espressione lambda a un tipo delegato `D`, disponibile anche una conversione nel tipo di albero delle espressioni `Expression<D>`. Mentre la conversione di un'espressione lambda a un tipo delegato genera un delegato che fa riferimento a codice eseguibile per l'espressione lambda, la conversione a un tipo di albero delle espressioni crea una rappresentazione di albero delle espressioni dell'espressione lambda.

Gli alberi delle espressioni sono rappresentazioni efficiente dei dati in memoria delle espressioni lambda e creare la struttura dell'espressione lambda trasparente ed esplicite.

Esattamente come un tipo delegato `D`, `Expression<D>` dice che ha parametri e tipi restituiti, che sono identici a quelli di `D`.

Nell'esempio seguente rappresenta un'espressione lambda come codice eseguibile e come un albero delle espressioni. Poiché esiste una conversione in `Func<int,int>`, disponibile anche una conversione da `Expression<Func<int,int>>`:

```csharp
Func<int,int> del = x => x + 1;                    // Code

Expression<Func<int,int>> exp = x => x + 1;        // Data
```

Seguendo queste assegnazioni, il delegato `del` fa riferimento a un metodo che restituisce `x + 1`e l'albero delle espressioni `exp` fa riferimento a una struttura di dati che descrive l'espressione `x => x + 1`.

La definizione esatta del tipo generico `Expression<D>` , nonché le regole precise per la costruzione di un albero delle espressioni quando un'espressione lambda viene convertita in un tipo di albero delle espressioni, sono entrambi all'esterno dell'ambito di questa specifica.

Due elementi vengono importante rendere esplicito:

*  Non tutte le espressioni lambda possono essere convertite in alberi delle espressioni. Ad esempio, le espressioni lambda con corpi di istruzione e le espressioni lambda che contengono espressioni di assegnazione non possono essere rappresentato. In questi casi, una conversione ancora esiste, ma avrà esito negativo in fase di compilazione. Queste eccezioni sono descritti in dettaglio [conversioni delle funzioni anonime](conversions.md#anonymous-function-conversions).
*   `Expression<D>` offre un metodo di istanza `Compile` che produce un delegato del tipo `D`:

    ```csharp
    Func<int,int> del2 = exp.Compile();
    ```

    Richiamando il delegato fa sì che il codice rappresentato dall'albero delle espressioni deve essere eseguito. Di conseguenza, date le definizioni precedenti, CANC e del2 sono equivalenti e le due istruzioni seguenti avranno lo stesso effetto:

    ```csharp
    int i1 = del(1);
    
    int i2 = del2(1);
    ```

    Dopo avere eseguito questo codice `i1` e `i2` entrambi avranno il valore `2`.

