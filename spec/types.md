---
ms.openlocfilehash: 088c4a77cecde490c556c44c239a3496f896582e
ms.sourcegitcommit: 4ddf18d000734c1b6d0a48127bf338086fc3f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73616131"
---
# <a name="types"></a>Tipi

I tipi della C# lingua sono divisi in due categorie principali: ***tipi di valore*** e tipi di ***riferimento***. Sia i tipi di valore che i tipi di riferimento possono essere ***tipi generici***che accettano uno o più ***parametri di tipo***. I parametri di tipo possono designare sia i tipi di valore che i tipi di riferimento.

```antlr
type
    : value_type
    | reference_type
    | type_parameter
    | type_unsafe
    ;
```

La categoria finale dei tipi, puntatori, è disponibile solo nel codice unsafe. Questa operazione viene descritta ulteriormente nei [tipi di puntatore](unsafe-code.md#pointer-types).

I tipi di valore differiscono dai tipi di riferimento in quanto le variabili dei tipi di valore contengono direttamente i dati, mentre le variabili dei tipi di riferimento archiviano i ***riferimenti*** ai relativi dati, il secondo noto come ***oggetti***. Con i tipi riferimento, è possibile che due variabili facciano riferimento allo stesso oggetto ed è quindi possibile che le operazioni su una variabile influiscano sull'oggetto a cui fa riferimento l'altra variabile. Con i tipi di valore, ogni variabile ha una propria copia dei dati e non è possibile che le operazioni su uno influiscano sull'altro.

C#il sistema di tipi è unificato in modo che un valore di qualsiasi tipo possa essere considerato come un oggetto. In C# ogni tipo deriva direttamente o indirettamente dal tipo classe `object` e `object` è la classe di base principale di tutti i tipi. I valori dei tipi riferimento vengono trattati come oggetti semplicemente visualizzando tali valori come tipi `object`. I valori dei tipi valore vengono trattati come oggetti eseguendo operazioni di conversione boxing e unboxing ([conversione boxing e](types.md#boxing-and-unboxing)unboxing).

## <a name="value-types"></a>Tipi valore

Un tipo di valore può essere un tipo struct o un tipo di enumerazione. C#fornisce un set di tipi struct predefiniti denominati ***tipi semplici***. I tipi semplici vengono identificati tramite parole riservate.

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

A differenza di una variabile di un tipo riferimento, una variabile di un tipo di valore può contenere il valore `null` solo se il tipo di valore è un tipo Nullable.  Per ogni tipo di valore che non ammette i valori null è presente un tipo di valore nullable corrispondente che indica lo stesso set di valori e il valore `null`.

L'assegnazione a una variabile di un tipo di valore crea una copia del valore da assegnare. Questo comportamento è diverso dall'assegnazione a una variabile di un tipo riferimento, che copia il riferimento, ma non l'oggetto identificato dal riferimento.

### <a name="the-systemvaluetype-type"></a>Tipo System. ValueType

Tutti i tipi valore ereditano in modo implicito dalla classe `System.ValueType`, che a sua volta eredita dalla classe `object`. Non è possibile derivare un tipo da un tipo di valore e i tipi di valore sono pertanto sealed in modo implicito ([classi sealed](classes.md#sealed-classes)).

Si noti che `System.ValueType` non è un *value_type*. Piuttosto, si tratta di un *class_type* da cui vengono derivati automaticamente tutti i *value_type*.

### <a name="default-constructors"></a>Costruttori predefiniti

Tutti i tipi valore dichiarano in modo implicito un costruttore di istanza pubblico senza parametri denominato ***costruttore predefinito***. Il costruttore predefinito restituisce un'istanza con inizializzazione zero nota come ***valore predefinito*** per il tipo di valore:

*  Per tutti *Simple_Type*s, il valore predefinito è il valore prodotto da uno schema di bit di tutti gli zeri:
    * Per `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`e `ulong`, il valore predefinito è `0`.
    * Per `char`, il valore predefinito è `'\x0000'`.
    * Per `float`, il valore predefinito è `0.0f`.
    * Per `double`, il valore predefinito è `0.0d`.
    * Per `decimal`, il valore predefinito è `0.0m`.
    * Per `bool`, il valore predefinito è `false`.
*  Per un `E`*enum_type* , il valore predefinito è `0`, convertito nel tipo `E`.
*  Per un *struct_type*, il valore predefinito è il valore prodotto impostando tutti i campi del tipo di valore sul valore predefinito e tutti i campi del tipo di riferimento su `null`.
*  Per un *nullable_type* , il valore predefinito è un'istanza per la quale la proprietà `HasValue` è false e la proprietà `Value` non è definita. Il valore predefinito è noto anche come ***valore null*** del tipo Nullable.

Analogamente a qualsiasi altro costruttore di istanza, il costruttore predefinito di un tipo di valore viene richiamato usando l'operatore `new`. Per motivi di efficienza, questo requisito non ha lo scopo di garantire che l'implementazione generi una chiamata al costruttore. Nell'esempio seguente le variabili `i` e `j` sono entrambe inizializzate su zero.

```csharp
class A
{
    void F() {
        int i = 0;
        int j = new int();
    }
}
```

Poiché ogni tipo di valore include implicitamente un costruttore di istanza pubblico senza parametri, non è possibile che un tipo di struct contenga una dichiarazione esplicita di un costruttore senza parametri. A un tipo struct è tuttavia consentito dichiarare costruttori di istanze con parametri ([costruttori](structs.md#constructors)).

### <a name="struct-types"></a>Tipi struct

Un tipo struct è un tipo valore che può dichiarare costanti, campi, metodi, proprietà, indicizzatori, operatori, costruttori di istanze, costruttori statici e tipi annidati. La dichiarazione dei tipi struct è descritta in [dichiarazioni struct](structs.md#struct-declarations).

### <a name="simple-types"></a>Tipi semplici

C#fornisce un set di tipi struct predefiniti denominati ***tipi semplici***. I tipi semplici vengono identificati tramite parole riservate, ma queste parole riservate sono semplicemente alias per i tipi struct predefiniti nello spazio dei nomi `System`, come descritto nella tabella seguente.


| __Parola riservata__ | __Tipo con alias__ |
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

Poiché un tipo semplice alias un tipo struct, ogni tipo semplice dispone di membri. Ad esempio, in `int` sono presenti i membri dichiarati in `System.Int32` e i membri ereditati da `System.Object`e sono consentite le istruzioni seguenti:

```csharp
int i = int.MaxValue;           // System.Int32.MaxValue constant
string s = i.ToString();        // System.Int32.ToString() instance method
string t = 123.ToString();      // System.Int32.ToString() instance method
```

I tipi semplici si differenziano da altri tipi struct in quanto permettono alcune operazioni aggiuntive:

*  La maggior parte dei tipi semplici consente la creazione di valori mediante la scrittura di valori *letterali* (valori[letterali](lexical-structure.md#literals)). Ad esempio, `123` è un valore letterale di tipo `int` e `'a'` è un valore letterale di tipo `char`. C#non esegue il provisioning di valori letterali di tipi struct in generale e i valori non predefiniti di altri tipi di struct vengono sempre creati tramite costruttori di istanza di tali tipi struct.
*  Quando gli operandi di un'espressione sono tutte costanti di tipo semplice, il compilatore può valutare l'espressione in fase di compilazione. Tale espressione è nota come *constant_expression* ([espressioni costanti](expressions.md#constant-expressions)). Le espressioni che coinvolgono operatori definiti da altri tipi di struct non sono considerate espressioni costanti.
*  Tramite dichiarazioni `const` è possibile dichiarare costanti dei tipi semplici ([costanti](classes.md#constants)). Non è possibile avere costanti di altri tipi struct, ma un effetto simile viene fornito dai campi `static readonly`.
*  Le conversioni che coinvolgono tipi semplici possono partecipare alla valutazione degli operatori di conversione definiti da altri tipi struct, ma un operatore di conversione definito dall'utente non può mai partecipare alla valutazione di un altro operatore definito dall'utente ([valutazione di conversioni definite dall'utente](conversions.md#evaluation-of-user-defined-conversions)).

### <a name="integral-types"></a>Tipi integrali

C#supporta nove tipi integrali: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`e `char`. I tipi integrali hanno le dimensioni e gli intervalli di valori seguenti:

*  Il tipo di `sbyte` rappresenta interi con segno a 8 bit con valori compresi tra-128 e 127.
*  Il tipo di `byte` rappresenta interi senza segno a 8 bit con valori compresi tra 0 e 255.
*  Il tipo di `short` rappresenta interi con segno a 16 bit con valori compresi tra-32768 e 32767.
*  Il tipo di `ushort` rappresenta interi senza segno a 16 bit con valori compresi tra 0 e 65535.
*  Il tipo di `int` rappresenta interi con segno a 32 bit compresi tra-2147483648 e 2147483647.
*  Il tipo di `uint` rappresenta interi senza segno a 32 bit con valori compresi tra 0 e 4294967295.
*  Il tipo di `long` rappresenta interi con segno a 64 bit con valori compresi tra-9.223.372.036.854.775.808 e 9223372036854775807.
*  Il tipo di `ulong` rappresenta interi senza segno a 64 bit con valori compresi tra 0 e 18.446.744.073.709.551.615.
*  Il tipo di `char` rappresenta interi senza segno a 16 bit con valori compresi tra 0 e 65535. Il set di valori possibili per il tipo di `char` corrisponde al set di caratteri Unicode. Sebbene `char` abbia la stessa rappresentazione di `ushort`, non tutte le operazioni consentite su un solo tipo sono consentite nell'altra.

Gli operatori unari e binari di tipo integrale operano sempre con precisione a 32 bit con segno, precisione a 32 bit senza segno, precisione 64 bit firmata o precisione a 64 bit senza segno:

*  Per gli operatori unario `+` e `~`, l'operando viene convertito nel tipo `T`, dove `T` è il primo di `int`, `uint`, `long`e `ulong` che possono rappresentare completamente tutti i valori possibili dell'operando. L'operazione viene quindi eseguita utilizzando la precisione del tipo `T`e il tipo del risultato è `T`.
*  Per l'operatore unario `-`, l'operando viene convertito nel tipo `T`, dove `T` è il primo `int` e `long` che possono rappresentare completamente tutti i valori possibili dell'operando. L'operazione viene quindi eseguita utilizzando la precisione del tipo `T`e il tipo del risultato è `T`. Non è possibile applicare l'operatore unario `-` a operandi di tipo `ulong`.
*  Per gli operatori Binary `+`, `-`, `*`, `/`, `%`, `&`, `^`, `|`, `==`, `!=`, `>`, `<`, `>=`e `<=` , gli operandi vengono convertiti nel tipo `T`, dove `T` è il primo dei `int`, `uint`, `long`e `ulong` che possono rappresentare completamente tutti i valori possibili di entrambi gli operandi. L'operazione viene quindi eseguita utilizzando la precisione del tipo `T`e il tipo del risultato viene `T` (o `bool` per gli operatori relazionali). Non è consentito un solo operando di tipo `long` e l'altro di tipo `ulong` con gli operatori binari.
*  Per gli operatori `<<` binari e `>>`, l'operando sinistro viene convertito nel tipo `T`, dove `T` è il primo di `int`, `uint`, `long`e `ulong` che possono rappresentare completamente tutti i valori possibili dell'operando. L'operazione viene quindi eseguita utilizzando la precisione del tipo `T`e il tipo del risultato è `T`.

Il tipo di `char` è classificato come tipo integrale, ma è diverso dagli altri tipi integrali in due modi:

*  Non esistono conversioni implicite da altri tipi al tipo di `char`. In particolare, anche se i tipi `sbyte`, `byte`e `ushort` contengono intervalli di valori che sono completamente rappresentabili utilizzando il tipo di `char`, le conversioni implicite da `sbyte`, `byte`o `ushort` a `char` non esistono.
*  Le costanti del tipo di `char` devono essere scritte come *character_literal*s o come *integer_literal*s in combinazione con un cast al tipo `char`. Ad esempio, `(char)10` equivale a `'\x000A'`.

Gli operatori e le istruzioni `checked` e `unchecked` vengono utilizzati per controllare il controllo dell'overflow per le conversioni e le operazioni aritmetiche di tipo integrale ([gli operatori checked e unchecked](expressions.md#the-checked-and-unchecked-operators)). In un contesto di `checked`, un overflow genera un errore in fase di compilazione o causa la generazione di un `System.OverflowException`. In un contesto di `unchecked`, gli overflow vengono ignorati e i bit più significativi che non rientrano nel tipo di destinazione vengono rimossi.

### <a name="floating-point-types"></a>Tipi a virgola mobile

C#supporta due tipi a virgola mobile: `float` e `double`. I tipi `float` e `double` sono rappresentati usando i formati IEEE 754 a precisione singola a 32 bit e a 64 bit, che forniscono i set di valori seguenti:

*  Zero positivo e zero negativo. Nella maggior parte dei casi, zero positivo e zero negativo si comportano in modo identico al valore semplice zero, ma alcune operazioni distinguono tra i due ([operatore di divisione](expressions.md#division-operator)).
*  Infinito positivo e infinito negativo. Gli infiniti sono prodotti da operazioni come la divisione di un numero diverso da zero. Ad esempio, `1.0 / 0.0` restituisce un valore infinito positivo e `-1.0 / 0.0` restituisce un infinito negativo.
*  Valore ***not-a-number*** , spesso abbreviato Nan. NaNs sono prodotti da operazioni a virgola mobile non valide, ad esempio la divisione di zero per zero.
*  Set finito di valori diversi da zero nel formato `s * m * 2^e`, dove `s` è 1 o-1 e `m` e `e` sono determinati dal tipo a virgola mobile specifico: per `float`, `0 < m < 2^24` e `-149 <= e <= 104`e per `double`, `0 < m < 2^53` e `-1075 <= e <= 970`. I numeri a virgola mobile denormalizzati sono considerati valori validi diversi da zero.

Il tipo di `float` può rappresentare valori compresi tra circa `1.5 * 10^-45` `3.4 * 10^38` con una precisione di 7 cifre.

Il tipo di `double` può rappresentare valori compresi tra circa `5.0 * 10^-324` `1.7 × 10^308` con una precisione di 15-16 cifre.

Se uno degli operandi di un operatore binario è di un tipo a virgola mobile, l'altro operando deve essere di un tipo integrale o di un tipo a virgola mobile e l'operazione viene valutata nel modo seguente:

*  Se uno degli operandi è di tipo integrale, l'operando viene convertito nel tipo a virgola mobile dell'altro operando.
*  Quindi, se uno degli operandi è di tipo `double`, l'altro operando viene convertito in `double`, l'operazione viene eseguita utilizzando almeno `double` intervallo e la precisione e il tipo del risultato è `double` (o `bool` per gli operatori relazionali).
*  In caso contrario, l'operazione viene eseguita utilizzando almeno `float` intervallo e la precisione e il tipo del risultato viene `float` (o `bool` per gli operatori relazionali).

Gli operatori a virgola mobile, inclusi gli operatori di assegnazione, non producono mai eccezioni. In situazioni eccezionali, invece, le operazioni a virgola mobile producono zero, infinito o NaN, come descritto di seguito:

*  Se il risultato di un'operazione a virgola mobile è troppo piccolo per il formato di destinazione, il risultato dell'operazione diventa zero positivo o negativo.
*  Se il risultato di un'operazione a virgola mobile è troppo grande per il formato di destinazione, il risultato dell'operazione diventa un infinito positivo o un valore infinito negativo.
*  Se un'operazione a virgola mobile non è valida, il risultato dell'operazione diventa NaN.
*  Se uno o entrambi gli operandi di un'operazione a virgola mobile è NaN, il risultato dell'operazione diventa NaN.

Le operazioni a virgola mobile possono essere eseguite con una precisione maggiore rispetto al tipo di risultato dell'operazione. Ad esempio, alcune architetture hardware supportano un tipo a virgola mobile "Extended" o "long double" con maggiore intervallo e precisione rispetto al tipo di `double` ed eseguono in modo implicito tutte le operazioni a virgola mobile utilizzando questo tipo di precisione più elevata. Solo a costi eccessivi in termini di prestazioni, è possibile eseguire tali architetture hardware per eseguire operazioni a virgola mobile con una precisione minore e piuttosto che richiedere che un'implementazione infastidisca sia le prestazioni che la precisione, consenta un tipo di precisione maggiore. C# da usare per tutte le operazioni a virgola mobile. Oltre a produrre risultati più precisi, questo raramente presenta effetti misurabili. Tuttavia, nelle espressioni del form `x * y / z`, in cui la moltiplicazione produce un risultato esterno all'intervallo di `double`, ma la divisione successiva riporta il risultato temporaneo nell'intervallo di `double`, il fatto che l'espressione venga valutata in un un formato di intervallo superiore può causare la produzione di un risultato finito anziché di un infinito.

### <a name="the-decimal-type"></a>Tipo decimale

Il tipo `decimal` è un tipo dati a 128 bit adatto per i calcoli finanziari e monetari. Il tipo di `decimal` può rappresentare valori compresi tra `1.0 * 10^-28` e approssimativamente `7.9 * 10^28` con 28-29 cifre significative.

Il set finito di valori di tipo `decimal` è nel formato `(-1)^s * c * 10^-e`, dove il segno `s` è 0 o 1, il coefficiente `c` viene fornito da `0 <= *c* < 2^96`e il `e` della scala è tale che `0 <= e <= 28`. Il tipo di `decimal` non supporta zeri con segno, infiniti o NaN. Un `decimal` è rappresentato come un intero a 96 bit ridimensionato da una potenza di dieci. Per `decimal`s con un valore assoluto minore di `1.0m`, il valore è esatto rispetto alla posizione del 28 ° decimale, ma non più. Per `decimal`s con un valore assoluto maggiore o uguale a `1.0m`, il valore è esatto a 28 o 29 cifre. Contrariamente ai tipi di dati `float` e `double`, i numeri frazionari decimali, ad esempio 0,1, possono essere rappresentati esattamente nella rappresentazione `decimal`. Nelle rappresentazioni `float` e `double`, tali numeri sono spesso frazioni infinite, rendendo le rappresentazioni più soggette a errori di arrotondamento.

Se uno degli operandi di un operatore binario è di tipo `decimal`, l'altro operando deve essere di tipo integrale o di tipo `decimal`. Se è presente un operando di tipo integrale, viene convertito in `decimal` prima dell'esecuzione dell'operazione.

Il risultato di un'operazione su valori di tipo `decimal` è il risultato del calcolo di un risultato esatto (mantenendo la scala, come definito per ogni operatore) e quindi arrotondamento per adattarsi alla rappresentazione. I risultati vengono arrotondati al valore rappresentabile più vicino e, quando un risultato è ugualmente vicino a due valori rappresentabili, al valore che ha un numero pari nella posizione della cifra meno significativa (noto come "arrotondamento del banco"). Un risultato zero ha sempre un segno di 0 e una scala pari a 0.

Se un'operazione aritmetica decimale produce un valore minore o uguale a `5 * 10^-29` in un valore assoluto, il risultato dell'operazione diventa zero. Se un'operazione aritmetica `decimal` produce un risultato troppo grande per il formato di `decimal`, viene generata un'`System.OverflowException`.

Il tipo di `decimal` ha una precisione maggiore ma un intervallo inferiore rispetto ai tipi a virgola mobile. Pertanto, le conversioni dai tipi a virgola mobile a `decimal` possono produrre eccezioni di overflow e le conversioni da `decimal` ai tipi a virgola mobile possono causare una perdita di precisione. Per questi motivi, non esiste alcuna conversione implicita tra i tipi a virgola mobile e `decimal`e senza cast espliciti, non è possibile combinare operandi a virgola mobile e `decimal` nella stessa espressione.

### <a name="the-bool-type"></a>Tipo bool

Il tipo di `bool` rappresenta le quantità logiche booleane. I valori possibili di tipo `bool` sono `true` e `false`.

Non esistono conversioni standard tra `bool` e altri tipi. In particolare, il tipo di `bool` è distinto e separato dai tipi integrali e non è possibile usare un valore `bool` al posto di un valore integrale e viceversa.

Nei linguaggi C e C++ , un valore a virgola mobile o integrale zero oppure un puntatore null può essere convertito nel valore booleano `false`e un valore a virgola mobile o integrale diverso da zero oppure un puntatore non null può essere convertito nel valore booleano `true`. In C#tali conversioni vengono eseguite confrontando in modo esplicito un valore a virgola mobile o integrale con zero oppure confrontando in modo esplicito un riferimento a un oggetto `null`.

### <a name="enumeration-types"></a>Tipi di enumerazione

Un tipo di enumerazione è un tipo distinto con costanti denominate. Ogni tipo di enumerazione ha un tipo sottostante, che deve essere `byte`, `sbyte`, `short`, `ushort`, `int`, `uint`, `long` o `ulong`. Il set di valori del tipo di enumerazione corrisponde al set di valori del tipo sottostante. I valori del tipo di enumerazione non sono limitati ai valori delle costanti denominate. I tipi di enumerazione vengono definiti tramite dichiarazioni di enumerazione ([dichiarazioni enum](enums.md#enum-declarations)).

### <a name="nullable-types"></a>Tipi nullable

Un tipo nullable può rappresentare tutti i valori del ***tipo sottostante*** più un valore null aggiuntivo. Un tipo nullable viene scritto `T?`, dove `T` è il tipo sottostante. Questa sintassi è abbreviata per `System.Nullable<T>`e i due moduli possono essere usati in modo interscambiabile.

Un ***tipo valore non nullable*** corrisponde a qualsiasi tipo di valore diverso da `System.Nullable<T>` e dalla relativa abbreviazione `T?` (per qualsiasi `T`), più qualsiasi parametro di tipo vincolato a un tipo di valore non Nullable (ovvero, qualsiasi parametro di tipo con un `struct` vincolo). Il tipo di `System.Nullable<T>` specifica il vincolo di tipo valore per `T` ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)), il che significa che il tipo sottostante di un tipo nullable può essere qualsiasi tipo di valore non nullable. Il tipo sottostante di un tipo nullable non può essere un tipo nullable o un tipo riferimento. Ad esempio, `int??` e `string?` sono tipi non validi.

Un'istanza di un tipo Nullable `T?` dispone di due proprietà pubbliche di sola lettura:

*  Proprietà `HasValue` di tipo `bool`
*  Proprietà `Value` di tipo `T`

Un'istanza per la quale `HasValue` è true viene definito non null. Un'istanza non null contiene un valore noto e `Value` restituisce tale valore.

Un'istanza per la quale `HasValue` è false viene definito null. Un'istanza null ha un valore non definito. Se si tenta di leggere il `Value` di un'istanza null, viene generata un'`System.InvalidOperationException`. Il processo di accesso alla proprietà `Value` di un'istanza nullable viene definito annullamento del ***wrapping***.

Oltre al costruttore predefinito, ogni tipo Nullable `T?` dispone di un costruttore pubblico che accetta un solo argomento di tipo `T`. Dato un valore `x` di tipo `T`, una chiamata al costruttore del form

```csharp
new T?(x)
```
Crea un'istanza non null di `T?` per cui è `x`la proprietà `Value`. Il processo di creazione di un'istanza non null di un tipo Nullable per un determinato valore viene definito ***incapsulamento***.

Le conversioni implicite sono disponibili dal valore letterale `null` a `T?` ([conversioni di valori letterali null](conversions.md#null-literal-conversions)) e da `T` a `T?` ([conversioni implicite Nullable](conversions.md#implicit-nullable-conversions)).

## <a name="reference-types"></a>Tipi riferimento

Un tipo di riferimento è un tipo di classe, un tipo di interfaccia, un tipo di matrice o un tipo di delegato.

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

Un valore del tipo di riferimento è un riferimento a un' ***istanza*** del tipo, il secondo noto come ***oggetto***. Il valore speciale `null` è compatibile con tutti i tipi riferimento e indica l'assenza di un'istanza.

### <a name="class-types"></a>Tipi classe

Un tipo di classe definisce una struttura di dati che contiene membri dati (costanti e campi), membri di funzione (metodi, proprietà, eventi, indicizzatori, operatori, costruttori di istanze, distruttori e costruttori statici) e tipi annidati. I tipi di classe supportano l'ereditarietà, un meccanismo in base al quale le classi derivate possono estendere e specializzare classi base. Le istanze dei tipi di classe vengono create usando *object_creation_expression*s ([espressioni di creazione di oggetti](expressions.md#object-creation-expressions)).

I tipi di classe sono descritti nelle [classi](classes.md).

Alcuni tipi di classe predefiniti hanno un C# significato speciale nel linguaggio, come descritto nella tabella seguente.


| __Tipo classe__     | __Descrizione__                                         |
|--------------------|---------------------------------------------------------|
| `System.Object`    | Classe base finale di tutti gli altri tipi. Vedere [il tipo di oggetto](types.md#the-object-type). | 
| `System.String`    | Tipo di stringa del C# linguaggio. Vedere [il tipo di stringa](types.md#the-string-type).         |
| `System.ValueType` | Classe di base di tutti i tipi di valore. Vedere [il tipo System. ValueType](types.md#the-systemvaluetype-type).          |
| `System.Enum`      | Classe di base di tutti i tipi enum. Vedere [enumerazioni](enums.md).              |
| `System.Array`     | Classe di base di tutti i tipi di matrice. Vdere [Matrici](arrays.md).             |
| `System.Delegate`  | Classe di base di tutti i tipi delegati. Vedere [delegati](delegates.md).          |
| `System.Exception` | Classe di base di tutti i tipi di eccezione. Vedere [eccezioni](exceptions.md).         |

### <a name="the-object-type"></a>Tipo di oggetto

Il `object` tipo di classe è la classe base finale di tutti gli altri tipi. Ogni tipo in C# deriva direttamente o indirettamente dal tipo di classe `object`.

La parola chiave `object` è semplicemente un alias per la classe predefinita `System.Object`.

### <a name="the-dynamic-type"></a>Tipo dinamico

Il tipo di `dynamic`, ad esempio `object`, può fare riferimento a qualsiasi oggetto. Quando gli operatori vengono applicati a espressioni di tipo `dynamic`, la relativa risoluzione viene posticipata finché il programma non viene eseguito. Pertanto, se l'operatore non può essere legalmente applicato all'oggetto a cui si fa riferimento, non viene specificato alcun errore durante la compilazione. Viene invece generata un'eccezione quando la risoluzione dell'operatore non riesce in fase di esecuzione.

Lo scopo è quello di consentire l'associazione dinamica, descritta in dettaglio nell' [associazione dinamica](expressions.md#dynamic-binding).

`dynamic` è considerato identico a `object` ad eccezione dei seguenti aspetti:

*  Le operazioni sulle espressioni di tipo `dynamic` possono essere associati dinamicamente ([associazione dinamica](expressions.md#dynamic-binding)).
*  L'inferenza del tipo ([inferenza del tipo](expressions.md#type-inference)) preferisce `dynamic` rispetto `object` se entrambi sono candidati.

A causa di questa equivalenza, i seguenti elementi contengono:

*  Esiste una conversione implicita di identità tra `object` e `dynamic`e tra tipi costruiti identici quando si sostituisce `dynamic` con `object`
*  Le conversioni implicite ed esplicite da e verso `object` si applicano anche a e da `dynamic`.
*  Le firme del metodo identiche quando si sostituisce `dynamic` con `object` sono considerate la stessa firma
*  Il tipo `dynamic` non è distinguibile dal `object` in fase di esecuzione.
*  Un'espressione di tipo `dynamic` viene definita ***espressione dinamica***.

### <a name="the-string-type"></a>Tipo di stringa

Il tipo di `string` è un tipo di classe sealed che eredita direttamente da `object`. Le istanze della classe `string` rappresentano stringhe di caratteri Unicode.

I valori del tipo di `string` possono essere scritti come valori letterali stringa ([valori letterali stringa](lexical-structure.md#string-literals)).

La parola chiave `string` è semplicemente un alias per la classe predefinita `System.String`.

### <a name="interface-types"></a>Tipi interfaccia

Un'interfaccia definisce un contratto. Una classe o uno struct che implementa un'interfaccia deve rispettare il relativo contratto. Un'interfaccia può ereditare da più interfacce di base e una classe o uno struct può implementare più interfacce.

I tipi di interfaccia sono descritti in [interfacce](interfaces.md).

### <a name="array-types"></a>Tipi matrice

Una matrice è una struttura di dati che contiene zero o più variabili a cui si accede tramite indici calcolati. Le variabili contenute in una matrice, denominate anche elementi della matrice, sono tutti dello stesso tipo e questo tipo è denominato tipo di elemento della matrice.

I tipi di matrice sono descritti in [matrici](arrays.md).

### <a name="delegate-types"></a>Tipi delegato

Un delegato è una struttura di dati che fa riferimento a uno o più metodi. Per i metodi di istanza, si riferisce anche alle istanze di oggetti corrispondenti.

L'equivalente più vicino di un delegato in C o C++ è un puntatore a funzione, ma mentre un puntatore a funzione può fare riferimento solo a funzioni statiche, un delegato può fare riferimento a metodi statici e di istanza. Nel secondo caso, il delegato archivia non solo un riferimento al punto di ingresso del metodo, ma anche un riferimento all'istanza dell'oggetto su cui richiamare il metodo.

I tipi delegati sono descritti in [delegati](delegates.md).

## <a name="boxing-and-unboxing"></a>Boxing e unboxing

Il concetto di conversione boxing e unboxing è fondamentale per C#il sistema di tipi. Fornisce un bridge tra *value_type*s e *reference_type*consentendo a qualsiasi valore di *value_type* di essere convertito in e dal tipo `object`. Boxing e unboxing consentono una visualizzazione unificata del sistema di tipi in cui un valore di qualsiasi tipo può essere considerato come un oggetto.

### <a name="boxing-conversions"></a>Conversioni boxing

Una conversione boxing consente a un *value_type* di essere convertito in modo implicito in un *reference_type*. Sono disponibili le conversioni boxing seguenti:

*  Da qualsiasi *value_type* al tipo `object`.
*  Da qualsiasi *value_type* al tipo `System.ValueType`.
*  Da qualsiasi *non_nullable_value_type* a qualsiasi *INTERFACE_TYPE* implementato da *value_type*.
*  Da qualsiasi *nullable_type* a qualsiasi *INTERFACE_TYPE* implementato dal tipo sottostante di *nullable_type*.
*  Da qualsiasi *enum_type* al tipo `System.Enum`.
*  Da qualsiasi *nullable_type* con un *enum_type* sottostante al tipo `System.Enum`.
*  Si noti che una conversione implicita da un parametro di tipo verrà eseguita come conversione boxing se in fase di esecuzione viene eseguita la conversione da un tipo valore a un tipo riferimento ([conversioni implicite che coinvolgono parametri di tipo](conversions.md#implicit-conversions-involving-type-parameters)).

La conversione boxing di un valore di *non_nullable_value_type* consiste nell'allocazione di un'istanza dell'oggetto e nella copia del valore *non_nullable_value_type* in tale istanza.

La conversione boxing di un valore di un *nullable_type* produce un riferimento null se è il valore `null` (`HasValue` è `false`) o il risultato dell'annullamento del wrapping e della conversione boxing del valore sottostante in caso contrario.

Il processo effettivo di conversione boxing di un valore di *non_nullable_value_type* viene illustrato meglio immaginando l'esistenza di una ***classe Boxing***generica, che si comporta come se fosse stata dichiarata come indicato di seguito:

```csharp
sealed class Box<T>: System.ValueType
{
    T value;

    public Box(T t) {
        value = t;
    }
}
```

Il Boxing di un valore `v` di tipo `T` ora consiste nell'eseguire l'espressione `new Box<T>(v)`e restituire l'istanza risultante come valore di tipo `object`. Quindi, le istruzioni
```csharp
int i = 123;
object box = i;
```
corrisponde concettualmente a
```csharp
int i = 123;
object box = new Box<int>(i);
```

Una classe Boxing come `Box<T>` precedente non esiste effettivamente e il tipo dinamico di un valore boxed non è effettivamente un tipo di classe. Al contrario, un valore boxed di tipo `T` ha il tipo dinamico `T`e un controllo di tipo dinamico che usa l'operatore `is` può semplicemente fare riferimento al tipo `T`. Di seguito è riportato un esempio:
```csharp
int i = 123;
object box = i;
if (box is int) {
    Console.Write("Box contains an int");
}
```
la stringa "`Box contains an int`" viene restituita nella console.

Una conversione boxing implica la creazione di una copia del valore sottoporre a Boxing. Questa operazione è diversa rispetto alla conversione di un *reference_type* nel tipo `object`, in cui il valore continua a fare riferimento alla stessa istanza e viene semplicemente considerato come il tipo meno derivato `object`. Ad esempio, in base alla dichiarazione
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
il valore 10 verrà restituito nella console perché l'operazione di conversione boxing implicita che si verifica nell'assegnazione di `p` a `box` causa la copia del valore di `p`. Se `Point` stata dichiarata `class`, il valore 20 verrebbe restituito perché `p` e `box` farebbero riferimento alla stessa istanza.

### <a name="unboxing-conversions"></a>Conversione unboxing

Una conversione unboxing consente a un *reference_type* di essere convertito in modo esplicito in un *value_type*. Sono disponibili le seguenti conversioni unboxing:

*  Dal tipo `object` a qualsiasi *value_type*.
*  Dal tipo `System.ValueType` a qualsiasi *value_type*.
*  Da qualsiasi *INTERFACE_TYPE* a qualsiasi *non_nullable_value_type* che implementi *INTERFACE_TYPE*.
*  Da qualsiasi *INTERFACE_TYPE* a qualsiasi *nullable_type* il cui tipo sottostante implementi *INTERFACE_TYPE*.
*  Dal tipo `System.Enum` a qualsiasi *enum_type*.
*  Dal tipo `System.Enum` a qualsiasi *nullable_type* con un *enum_type*sottostante.
*  Si noti che una conversione esplicita in un parametro di tipo verrà eseguita come conversione unboxing se in fase di esecuzione termina la conversione da un tipo di riferimento a un tipo valore ([conversioni dinamiche esplicite](conversions.md#explicit-dynamic-conversions)).

Un'operazione di conversione unboxing in un *non_nullable_value_type* consiste nel verificare innanzitutto che l'istanza dell'oggetto sia un valore boxed del *non_nullable_value_type*specificato e quindi copiare il valore all'esterno dell'istanza.

La conversione unboxing in un *nullable_type* produce il valore null di *nullable_type* se l'operando di origine è `null`o il risultato di cui è stato eseguito il Boxing unboxing dell'istanza dell'oggetto nel tipo sottostante di *nullable_type* in caso contrario.

Facendo riferimento alla classe boxing immaginaria descritta nella sezione precedente, una conversione unboxing di un oggetto `box` a un `T` *value_type* consiste nell'esecuzione dell'espressione `((Box<T>)box).value`. Quindi, le istruzioni
```csharp
object box = 123;
int i = (int)box;
```
corrisponde concettualmente a
```csharp
object box = new Box<int>(123);
int i = ((Box<int>)box).value;
```

Affinché una conversione unboxing in un determinato *non_nullable_value_type* abbia esito positivo in fase di esecuzione, il valore dell'operando di origine deve essere un riferimento a un valore boxed di tale *non_nullable_value_type*. Se l'operando di origine è `null`, viene generata un'`System.NullReferenceException`. Se l'operando di origine è un riferimento a un oggetto incompatibile, viene generata un'`System.InvalidCastException`.

Affinché una conversione unboxing in un determinato *nullable_type* abbia esito positivo in fase di esecuzione, il valore dell'operando di origine deve essere `null` o un riferimento a un valore boxed dell' *non_nullable_value_type* sottostante di *nullable_type*. Se l'operando di origine è un riferimento a un oggetto incompatibile, viene generata un'`System.InvalidCastException`.

## <a name="constructed-types"></a>Tipi costruiti

Una dichiarazione di tipo generico, di per sé, denota un ***tipo generico non associato*** usato come "progetto" per formare molti tipi diversi, mediante l'applicazione di ***argomenti di tipo***. Gli argomenti di tipo vengono scritti tra parentesi angolari (`<` e `>`) immediatamente dopo il nome del tipo generico. Un tipo che include almeno un argomento di tipo è denominato ***tipo costruito***. Un tipo costruito può essere usato nella maggior parte dei punti della lingua in cui è possibile visualizzare un nome di tipo. Un tipo generico non associato può essere utilizzato solo all'interno di un *typeof_expression* ([l'operatore typeof](expressions.md#the-typeof-operator)).

I tipi costruiti possono essere usati anche nelle espressioni come nomi semplici ([nomi semplici](expressions.md#simple-names)) o quando si accede a un membro ([accesso ai membri](expressions.md#member-access)).

Quando un *namespace_or_type_name* viene valutato, vengono considerati solo i tipi generici con il numero corretto di parametri di tipo. Pertanto, è possibile utilizzare lo stesso identificatore per identificare tipi diversi, purché i tipi abbiano numeri diversi di parametri di tipo. Questa operazione è utile quando si combinano classi generiche e non generiche nello stesso programma:

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

Un *type_name* può identificare un tipo costruito anche se non specifica direttamente i parametri di tipo. Questa situazione può verificarsi quando un tipo è annidato all'interno di una dichiarazione di classe generica e il tipo di istanza della dichiarazione che lo contiene viene usato in modo implicito per la ricerca del nome ([tipi annidati nelle classi generiche](classes.md#nested-types-in-generic-classes)):

```csharp
class Outer<T>
{
    public class Inner {...}

    public Inner i;                // Type of i is Outer<T>.Inner
}
```

Nel codice unsafe non è possibile usare un tipo costruito come *unmanaged_type* ([tipi puntatore](unsafe-code.md#pointer-types)).

### <a name="type-arguments"></a>Argomenti di tipo

Ogni argomento in un elenco di argomenti di tipo è semplicemente un *tipo*.

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

In codice unsafe ([codice unsafe](unsafe-code.md)), un *type_argument* non può essere un tipo di puntatore. Ogni argomento di tipo deve soddisfare tutti i vincoli sul parametro di tipo corrispondente ([vincoli del parametro di tipo](classes.md#type-parameter-constraints)).

### <a name="open-and-closed-types"></a>Tipi aperti e chiusi

Tutti i tipi possono essere classificati come ***tipi aperti*** o ***tipi chiusi***. Un tipo aperto è un tipo che include parametri di tipo. Più precisamente:

*  Un parametro di tipo definisce un tipo aperto.
*  Un tipo matrice è un tipo aperto se e solo se il relativo tipo di elemento è un tipo aperto.
*  Un tipo costruito è un tipo aperto se e solo se uno o più dei relativi argomenti di tipo è un tipo aperto. Un tipo annidato costruito è un tipo aperto se e solo se uno o più dei relativi argomenti di tipo o gli argomenti di tipo del tipo che lo contiene è un tipo aperto.

Un tipo chiuso è un tipo che non è un tipo aperto.

In fase di esecuzione, tutto il codice all'interno di una dichiarazione di tipo generico viene eseguito nel contesto di un tipo costruito chiuso che è stato creato applicando gli argomenti di tipo alla dichiarazione generica. Ogni parametro di tipo all'interno del tipo generico è associato a un tipo di runtime specifico. L'elaborazione in fase di esecuzione di tutte le istruzioni e le espressioni viene sempre eseguita con i tipi chiusi e i tipi aperti si verificano solo durante l'elaborazione in fase di compilazione.

Ogni tipo costruito chiuso dispone di un proprio set di variabili statiche, che non sono condivise con altri tipi costruiti chiusi. Poiché un tipo aperto non esiste in fase di esecuzione, non sono presenti variabili statiche associate a un tipo aperto. Due tipi costruiti chiusi sono dello stesso tipo se vengono costruiti dallo stesso tipo generico non associato e gli argomenti di tipo corrispondenti sono dello stesso tipo.

### <a name="bound-and-unbound-types"></a>Tipi associati e non associati

Il termine ***tipo non associato*** fa riferimento a un tipo non generico o a un tipo generico non associato. Il termine ***tipo associato*** fa riferimento a un tipo non generico o a un tipo costruito.

Un tipo non associato fa riferimento all'entità dichiarata da una dichiarazione di tipo. Un tipo generico non associato non è a sua volta un tipo e non può essere utilizzato come tipo di variabile, argomento o valore restituito o come tipo di base. L'unico costrutto in cui è possibile fare riferimento a un tipo generico non associato è l'espressione `typeof` ([operatore typeof](expressions.md#the-typeof-operator)).

### <a name="satisfying-constraints"></a>Vincoli di soddisfazione

Ogni volta che viene fatto riferimento a un tipo costruito o a un metodo generico, gli argomenti di tipo forniti vengono controllati in base ai vincoli del parametro di tipo dichiarati nel metodo o nel tipo generico ([vincoli del parametro di tipo](classes.md#type-parameter-constraints)). Per ogni clausola `where`, l'argomento di tipo `A` che corrisponde al parametro di tipo denominato viene verificato rispetto a ogni vincolo come segue:

*  Se il vincolo è un tipo di classe, un tipo di interfaccia o un parametro di tipo, consentire `C` rappresentare tale vincolo con gli argomenti di tipo specificati, sostituendo tutti i parametri di tipo presenti nel vincolo. Per soddisfare il vincolo, è necessario che il tipo `A` sia convertibile nel tipo `C` in uno dei seguenti casi:
    * Conversione di identità ([conversione di identità](conversions.md#identity-conversion))
    * Conversione implicita di riferimenti ([conversioni implicite di riferimenti](conversions.md#implicit-reference-conversions))
    * Conversione boxing ([conversioni boxing](conversions.md#boxing-conversions)), purché il tipo a sia un tipo di valore non nullable.
    * Un riferimento implicito, una conversione boxing o un parametro di tipo da un parametro di tipo `A` `C`.
*  Se il vincolo è il vincolo di tipo di riferimento (`class`), il tipo `A` deve soddisfare uno degli elementi seguenti:
    * `A` è un tipo di interfaccia, un tipo di classe, un tipo di delegato o un tipo di matrice. Si noti che `System.ValueType` e `System.Enum` sono tipi di riferimento che soddisfano questo vincolo.
    * `A` è un parametro di tipo che è noto come tipo di riferimento ([vincoli del parametro di tipo](classes.md#type-parameter-constraints)).
*  Se il vincolo è il vincolo di tipo valore (`struct`), il tipo `A` deve soddisfare uno degli elementi seguenti:
    * `A` è un tipo struct o un tipo enum, ma non un tipo Nullable. Si noti che `System.ValueType` e `System.Enum` sono tipi di riferimento che non soddisfano questo vincolo.
    * `A` è un parametro di tipo con il vincolo di tipo valore ([vincoli del parametro di tipo](classes.md#type-parameter-constraints)).
*  Se il vincolo è il vincolo del costruttore `new()`, il tipo `A` non deve essere `abstract` e deve avere un costruttore pubblico senza parametri. Questa operazione viene soddisfatta se si verifica una delle condizioni seguenti:
    * `A` è un tipo di valore, perché tutti i tipi di valore hanno un costruttore predefinito pubblico ([costruttori predefiniti](types.md#default-constructors)).
    * `A` è un parametro di tipo con il vincolo del costruttore ([vincoli del parametro di tipo](classes.md#type-parameter-constraints)).
    * `A` è un parametro di tipo con il vincolo di tipo valore ([vincoli del parametro di tipo](classes.md#type-parameter-constraints)).
    * `A` è una classe che non è `abstract` e contiene un costruttore di `public` dichiarato in modo esplicito senza parametri.
    * `A` non è `abstract` e dispone di un costruttore predefinito ([costruttori predefiniti](classes.md#default-constructors)).

Si verifica un errore in fase di compilazione se uno o più vincoli del parametro di tipo non vengono soddisfatti dagli argomenti di tipo specificati.

Poiché i parametri di tipo non vengono ereditati, i vincoli non vengono mai ereditati. Nell'esempio seguente `D` necessario specificare il vincolo sul parametro di tipo `T` in modo che `T` soddisfi il vincolo imposto dalla classe base `B<T>`. Al contrario, la classe `E` non deve specificare un vincolo, perché `List<T>` implementa `IEnumerable` per qualsiasi `T`.

```csharp
class B<T> where T: IEnumerable {...}

class D<T>: B<T> where T: IEnumerable {...}

class E<T>: B<List<T>> {...}
```

## <a name="type-parameters"></a>Parametri di tipo

Un parametro di tipo è un identificatore che designa un tipo di valore o un tipo di riferimento a cui è associato il parametro in fase di esecuzione.

```antlr
type_parameter
    : identifier
    ;
```

Poiché è possibile creare un'istanza di un parametro di tipo con molti argomenti di tipo effettivi diversi, i parametri di tipo hanno operazioni e restrizioni leggermente diverse rispetto ad altri tipi. tra cui:

*  Non è possibile usare direttamente un parametro di tipo per dichiarare una classe base ([classe base](classes.md#base-class)) o un'interfaccia ([elenchi di parametri di tipo Variant](interfaces.md#variant-type-parameter-lists)).
*  Le regole per la ricerca dei membri sui parametri di tipo variano in base ai vincoli applicati al parametro di tipo. Sono descritte in dettaglio nella [ricerca di membri](expressions.md#member-lookup).
*  Le conversioni disponibili per un parametro di tipo variano in base ai vincoli applicati al parametro di tipo. Sono descritte in dettaglio in [conversioni implicite che coinvolgono parametri di tipo](conversions.md#implicit-conversions-involving-type-parameters) e [conversioni dinamiche esplicite](conversions.md#explicit-dynamic-conversions).
*  Il valore letterale `null` non può essere convertito in un tipo specificato da un parametro di tipo, eccetto se il parametro di tipo è noto come tipo di riferimento ([conversioni implicite che coinvolgono parametri di tipo](conversions.md#implicit-conversions-involving-type-parameters)). Tuttavia, è possibile usare un'espressione `default` ([espressioni con valore predefinito](expressions.md#default-value-expressions)). Inoltre, un valore con un tipo specificato da un parametro di tipo può essere confrontato con `null` utilizzando `==` e `!=` ([operatori di uguaglianza del tipo di riferimento](expressions.md#reference-type-equality-operators)), a meno che il parametro di tipo non disponga del vincolo di tipo valore.
*  Un'espressione `new` ([espressioni di creazione di oggetti](expressions.md#object-creation-expressions)) può essere utilizzata solo con un parametro di tipo se il parametro di tipo è vincolato da un *constructor_constraint* o dal vincolo di tipo valore (vincoli del[parametro di tipo](classes.md#type-parameter-constraints)).
*  Non è possibile usare un parametro di tipo in qualsiasi punto all'interno di un attributo.
*  Non è possibile usare un parametro di tipo nell'accesso ai membri ([accesso ai membri](expressions.md#member-access)) o nel nome del tipo ([nomi di tipo e spazio dei](basic-concepts.md#namespace-and-type-names)nomi) per identificare un membro statico o un tipo annidato.
*  Nel codice unsafe non è possibile usare un parametro di tipo come *unmanaged_type* ([tipi di puntatore](unsafe-code.md#pointer-types)).

Come tipo, i parametri di tipo sono puramente un costrutto in fase di compilazione. In fase di esecuzione, ogni parametro di tipo è associato a un tipo runtime specificato fornendo un argomento di tipo alla dichiarazione di tipo generico. Pertanto, il tipo di una variabile dichiarata con un parametro di tipo sarà, in fase di esecuzione, un tipo costruito chiuso ([tipi aperti e chiusi](types.md#open-and-closed-types)). L'esecuzione in fase di esecuzione di tutte le istruzioni e le espressioni che coinvolgono parametri di tipo utilizza il tipo effettivo fornito come argomento di tipo per il parametro.

## <a name="expression-tree-types"></a>Tipi di albero delle espressioni

Gli ***alberi delle espressioni*** consentono di rappresentare le espressioni lambda come strutture di dati anziché come codice eseguibile. Gli alberi delle espressioni sono valori dei ***tipi di albero delle espressioni*** nel formato `System.Linq.Expressions.Expression<D>`, dove `D` è qualsiasi tipo delegato. Per il resto di questa specifica si faranno riferimento a questi tipi usando la sintassi abbreviata `Expression<D>`.

Se esiste una conversione da un'espressione lambda a un tipo delegato `D`, esiste anche una conversione nel tipo di albero delle espressioni `Expression<D>`. Mentre la conversione di un'espressione lambda in un tipo delegato genera un delegato che fa riferimento a codice eseguibile per l'espressione lambda, la conversione in un tipo di albero delle espressioni crea una rappresentazione dell'albero delle espressioni dell'espressione lambda.

Gli alberi delle espressioni sono rappresentazioni efficienti dei dati in memoria delle espressioni lambda e rendono trasparente ed esplicita la struttura dell'espressione lambda.

Analogamente a un tipo di delegato `D`, `Expression<D>` si dice che i tipi di parametro e restituiti sono identici a quelli di `D`.

Nell'esempio seguente viene rappresentata un'espressione lambda sia come codice eseguibile che come albero delle espressioni. Poiché esiste una conversione per `Func<int,int>`, esiste una conversione anche per `Expression<Func<int,int>>`:

```csharp
Func<int,int> del = x => x + 1;                    // Code

Expression<Func<int,int>> exp = x => x + 1;        // Data
```

Dopo queste assegnazioni, il delegato `del` fa riferimento a un metodo che restituisce `x + 1`e l'albero delle espressioni `exp` fa riferimento a una struttura di dati che descrive l'espressione `x => x + 1`.

La definizione esatta del tipo generico `Expression<D>` nonché le regole precise per la costruzione di un albero delle espressioni quando un'espressione lambda viene convertita in un tipo di albero delle espressioni, non rientra nell'ambito di questa specifica.

Due aspetti importanti da rendere esplicito:

*  Non tutte le espressioni lambda possono essere convertite in alberi delle espressioni. Ad esempio, le espressioni lambda con i corpi delle istruzioni e le espressioni lambda che contengono espressioni di assegnazione non possono essere rappresentate. In questi casi, una conversione esiste ancora, ma avrà esito negativo in fase di compilazione. Queste eccezioni sono descritte in dettaglio in [conversioni di funzioni anonime](conversions.md#anonymous-function-conversions).
*   `Expression<D>` offre un metodo di istanza `Compile` che produce un delegato di tipo `D`:

    ```csharp
    Func<int,int> del2 = exp.Compile();
    ```

    La chiamata a questo delegato causa l'esecuzione del codice rappresentato dall'albero delle espressioni. In questo modo, date le definizioni precedenti, CANC e del2 sono equivalenti e le due istruzioni seguenti avranno lo stesso effetto:

    ```csharp
    int i1 = del(1);
    
    int i2 = del2(1);
    ```

    Al termine dell'esecuzione del codice, `i1` e `i2` avranno il valore `2`.

