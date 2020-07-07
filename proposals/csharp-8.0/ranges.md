---
ms.openlocfilehash: bee562d64c402e645879b7811cc82c971d8b61cc
ms.sourcegitcommit: 9b736b5b73321f18a04e02d26596388f7ceb42fc
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86052207"
---
# <a name="ranges"></a>Intervalli

## <a name="summary"></a>Summary

Questa funzionalità prevede la distribuzione di due nuovi operatori che consentono `System.Index` di costruire `System.Range` oggetti e e di usarli per le raccolte di indicizzazione/sezionamento in fase di esecuzione.

## <a name="overview"></a>Panoramica

### <a name="well-known-types-and-members"></a>Tipi e membri ben noti

Per utilizzare i nuovi moduli sintattici per `System.Index` e `System.Range` , possono essere necessari nuovi tipi e membri noti, a seconda dei moduli sintattici utilizzati.

Per utilizzare l'operatore "Hat" ( `^` ), è necessario quanto segue

```csharp
namespace System
{
    public readonly struct Index
    {
        public Index(int value, bool fromEnd);
    }
}
```

Per usare il `System.Index` tipo come argomento in un accesso a un elemento della matrice, è necessario il membro seguente:

```csharp
int System.Index.GetOffset(int length);
```

La `..` sintassi per `System.Range` richiederà il `System.Range` tipo, nonché uno o più dei membri seguenti:

```csharp
namespace System
{
    public readonly struct Range
    {
        public Range(System.Index start, System.Index end);
        public static Range StartAt(System.Index start);
        public static Range EndAt(System.Index end);
        public static Range All { get; }
    }
}
```

La `..` sintassi consente la mancata presenza di entrambi gli argomenti. Indipendentemente dal numero di argomenti, il `Range` costruttore è sempre sufficiente per l'utilizzo della `Range` sintassi. Tuttavia, se uno degli altri membri è presente e uno o più `..` argomenti risultano mancanti, il membro appropriato può essere sostituito.

Infine, per un valore di tipo `System.Range` da utilizzare in un'espressione di accesso agli elementi della matrice, è necessario che sia presente il membro seguente:

```csharp
namespace System.Runtime.CompilerServices
{
    public static class RuntimeHelpers
    {
        public static T[] GetSubArray<T>(T[] array, System.Range range);
    }
}
```

### <a name="systemindex"></a>System. index

C# non è in grado di indicizzare una raccolta dalla fine, ma la maggior parte degli indicizzatori usa la nozione "from Start" o un'espressione "length-i". Viene introdotta una nuova espressione di indice che significa "dalla fine". La funzionalità introdurrà un nuovo operatore "Hat" del prefisso unario. Il suo singolo operando deve essere convertibile in `System.Int32` . Verrà ridotta alla `System.Index` chiamata al metodo factory appropriato.

Viene aumentata la grammatica per *unary_expression* con il seguente formato di sintassi aggiuntivo:

```antlr
unary_expression
    : '^' unary_expression
    ;
```

Questo indice viene chiamato *dall'operatore end* . L'indice predefinito degli operatori *finali* è il seguente:

```csharp
System.Index operator ^(int fromEnd);
```

Il comportamento di questo operatore viene definito solo per i valori di input maggiori o uguali a zero.

Esempi:

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var thirdItem = array[2];    // array[2]
var lastItem = array[^1];    // array[new Index(1, fromEnd: true)]
```

#### <a name="systemrange"></a>System. Range

C# non dispone di un modo sintattico per accedere a "intervalli" o "sezioni" di raccolte. In genere gli utenti sono obbligati a implementare strutture complesse per filtrare/utilizzare le sezioni di memoria o ricorrere a metodi LINQ come `list.Skip(5).Take(2)` . Con l'aggiunta di `System.Span<T>` e di altri tipi simili, è più importante avere questo tipo di operazione supportato a un livello più profondo nel linguaggio/Runtime e avere l'interfaccia unificata.

Il linguaggio introdurrà un nuovo operatore di intervallo `x..y` . Si tratta di un operatore infisso binario che accetta due espressioni. Uno degli operandi può essere omesso (esempi seguenti) ed è necessario convertirlo in `System.Index` . Verrà ridotta alla `System.Range` chiamata al metodo factory appropriato.

Per introdurre un nuovo livello di precedenza, vengono sostituite le regole della grammatica C# per *multiplicative_expression* con le seguenti opzioni:

```antlr
range_expression
    : unary_expression
    | range_expression? '..' range_expression?
    ;

multiplicative_expression
    : range_expression
    | multiplicative_expression '*' range_expression
    | multiplicative_expression '/' range_expression
    | multiplicative_expression '%' range_expression
    ;
```

Tutti i form dell' *operatore di intervallo* hanno la stessa precedenza. Questo nuovo gruppo di precedenza è inferiore agli operatori *unari* e superiori rispetto agli *operatori aritmetici moltiplicativi*.

Viene chiamato l'operatore `..` *Range*. L'operatore di intervallo predefinito può essere compreso approssimativamente per corrispondere alla chiamata di un operatore incorporato nel formato seguente:

```csharp
System.Range operator ..(Index start = 0, Index end = ^0);
```

Esempi:

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var slice1 = array[2..^3];    // array[new Range(2, new Index(3, fromEnd: true))]
var slice2 = array[..^3];     // array[Range.EndAt(new Index(3, fromEnd: true))]
var slice3 = array[2..];      // array[Range.StartAt(2)]
var slice4 = array[..];       // array[Range.All]
```

Inoltre, `System.Index` deve disporre di una conversione implicita da `System.Int32` , in modo da evitare la necessità di eseguire l'overload di combinazioni di interi e indici su firme multidimensionali.

## <a name="adding-index-and-range-support-to-existing-library-types"></a>Aggiunta del supporto per l'indice e l'intervallo ai tipi di libreria esistenti

### <a name="implicit-index-support"></a>Supporto per gli indici impliciti

Il linguaggio fornirà un membro dell'indicizzatore di istanza con un solo parametro di tipo `Index` per i tipi che soddisfano i criteri seguenti:

- Il tipo è conteggiabile.
- Il tipo dispone di un indicizzatore di istanza accessibile che accetta un singolo `int` come argomento.
- Il tipo non dispone di un indicizzatore di istanza accessibile che accetta `Index` come primo parametro. `Index`Deve essere l'unico parametro oppure i parametri rimanenti devono essere facoltativi.

Un tipo può essere ***conteggiato*** se dispone di una proprietà denominata `Length` o `Count` con un getter accessibile e un tipo restituito `int` . Il linguaggio può usare questa proprietà per convertire un'espressione di tipo `Index` in un oggetto `int` in corrispondenza del punto dell'espressione senza la necessità di usare il tipo `Index` . Se `Length` `Count` sono presenti sia che, `Length` verranno preferiti. Per semplicità, la proposta userà il nome `Length` per rappresentare `Count` o `Length` .

Per questi tipi, il linguaggio agirà come se fosse presente un membro dell'indicizzatore nel formato in `T this[Index index]` cui `T` è il tipo restituito dell' `int` indicizzatore basato su, incluse le `ref` annotazioni di stile. Il nuovo membro avrà gli stessi `get` membri e `set` con l'accessibilità corrispondente dell' `int` indicizzatore.

Il nuovo indicizzatore verrà implementato convertendo l'argomento di tipo `Index` in un oggetto `int` ed emettendo una chiamata all' `int` indicizzatore basato su. Ai fini della discussione, si userà l'esempio di `receiver[expr]` . La conversione di `expr` in `int` viene eseguita nel modo seguente:

- Quando l'argomento è del formato `^expr2` e il tipo di `expr2` è `int` , verrà convertito in `receiver.Length - expr2` .
- In caso contrario, verrà tradotto come `expr.GetOffset(receiver.Length)` .

Questo consente agli sviluppatori di usare la `Index` funzionalità sui tipi esistenti senza che sia necessario apportare modifiche. Ad esempio:

``` csharp
List<char> list = ...;
var value = list[^1];

// Gets translated to
var value = list[list.Count - 1];
```

Le `receiver` espressioni e verranno distribuite `Length` nel modo appropriato per assicurarsi che gli effetti collaterali vengano eseguiti solo una volta. Ad esempio:

``` csharp
class Collection {
    private int[] _array = new[] { 1, 2, 3 };

    int Length {
        get {
            Console.Write("Length ");
            return _array.Length;
        }
    }

    int this[int index] => _array[index];
}

class SideEffect {
    Collection Get() {
        Console.Write("Get ");
        return new Collection();
    }

    void Use() {
        int i = Get()[^1];
        Console.WriteLine(i);
    }
}
```

Con questo codice viene stampato "Get length 3".

### <a name="implicit-range-support"></a>Supporto di intervalli impliciti

Il linguaggio fornirà un membro dell'indicizzatore di istanza con un solo parametro di tipo `Range` per i tipi che soddisfano i criteri seguenti:

- Il tipo è conteggiabile.
- Il tipo dispone di un membro accessibile denominato `Slice` che ha due parametri di tipo `int` .
- Il tipo non dispone di un indicizzatore di istanza che accetta un singolo `Range` come primo parametro. `Range`Deve essere l'unico parametro oppure i parametri rimanenti devono essere facoltativi.

Per questi tipi, il linguaggio viene associato come se fosse presente un membro dell'indicizzatore nel formato in `T this[Range range]` cui `T` è il tipo restituito del `Slice` metodo, incluse le `ref` annotazioni di stile. Il nuovo membro avrà anche l'accessibilità corrispondente a `Slice` .

Quando l' `Range` indicizzatore basato è associato a un'espressione denominata `receiver` , viene ridotto convertendo l' `Range` espressione in due valori che vengono quindi passati al `Slice` metodo. Ai fini della discussione, si userà l'esempio di `receiver[expr]` .

Il primo argomento di `Slice` verrà ottenuto convertendo l'espressione tipizzata Range nel modo seguente:

- Quando `expr` è del form `expr1..expr2` (dove `expr2` può essere omesso) e `expr1` ha il tipo `int` , verrà generato come `expr1` .
- Quando `expr` è nel formato `^expr1..expr2` (dove `expr2` può essere omesso), verrà generato come `receiver.Length - expr1` .
- Quando `expr` è nel formato `..expr2` (dove `expr2` può essere omesso), verrà generato come `0` .
- In caso contrario, verrà generato come `expr.Start.GetOffset(receiver.Length)` .

Questo valore verrà nuovamente utilizzato nel calcolo del secondo `Slice` argomento. Quando si esegue questa operazione, viene definito `start` . Il secondo argomento di `Slice` verrà ottenuto convertendo l'espressione tipizzata Range nel modo seguente:

- Quando `expr` è del form `expr1..expr2` (dove `expr1` può essere omesso) e `expr2` ha il tipo `int` , verrà generato come `expr2 - start` .
- Quando `expr` è nel formato `expr1..^expr2` (dove `expr1` può essere omesso), verrà generato come `(receiver.Length - expr2) - start` .
- Quando `expr` è nel formato `expr1..` (dove `expr1` può essere omesso), verrà generato come `receiver.Length - start` .
- In caso contrario, verrà generato come `expr.End.GetOffset(receiver.Length) - start` .

Le `receiver` `Length` espressioni, e verranno distribuite nel modo `expr` appropriato per assicurarsi che gli effetti collaterali vengano eseguiti solo una volta. Ad esempio:

``` csharp
class Collection {
    private int[] _array = new[] { 1, 2, 3 };

    int Length {
        get {
            Console.Write("Length ");
            return _array.Length;
        }
    }

    int[] Slice(int start, int length) {
        var slice = new int[length];
        Array.Copy(_array, start, slice, 0, length);
        return slice;
    }
}

class SideEffect {
    Collection Get() {
        Console.Write("Get ");
        return new Collection();
    }

    void Use() {
        var array = Get()[0..2];
        Console.WriteLine(array.length);
    }
}
```

Con questo codice viene stampato "get Length 2".

Il linguaggio è il caso speciale dei seguenti tipi noti:

- `string`: verrà usato il metodo al `Substring` posto di `Slice` .
- `array`: verrà usato il metodo al `System.Reflection.CompilerServices.GetSubArray` posto di `Slice` .

## <a name="alternatives"></a>Alternativi

I nuovi operatori ( `^` e `..` ) sono dello zucchero sintattico. La funzionalità può essere implementata da chiamate esplicite ai `System.Index` `System.Range` metodi factory, ma comporterà un maggior numero di codice standard e l'esperienza non sarà intuitiva.

## <a name="il-representation"></a>Rappresentazione IL

Questi due operatori verranno abbassati alle normali chiamate all'indicizzatore o al metodo, senza modifiche nei livelli del compilatore successivi.

## <a name="runtime-behavior"></a>Comportamento in fase di esecuzione

- Il compilatore può ottimizzare gli indicizzatori per i tipi incorporati, ad esempio matrici e stringhe, e ridurre l'indicizzazione ai metodi esistenti appropriati.
- `System.Index`genererà se costruito con un valore negativo.
- `^0`non genera un'eccezione, ma si traduce nella lunghezza della raccolta/enumerabile a cui viene fornito.
- `Range.All`è semanticamente equivalente a `0..^0` e può essere decostruito in questi indici.

## <a name="considerations"></a>Considerazioni

### <a name="detect-indexable-based-on-icollection"></a>Rileva indicizzabile basato su ICollection

L'ispirazione per questo comportamento è stata inizializzatori di raccolta. Uso della struttura di un tipo per indicare che ha scelto una funzionalità. Nel caso di inizializzatori di insieme, i tipi possono optare per la funzionalità implementando l'interfaccia `IEnumerable` (non generica).

Questa proposta ha inizialmente richiesto che i tipi implementino `ICollection` per essere qualificati come indicizzabili. Che richiedevano diversi casi speciali:

- `ref struct`: non possono implementare interfacce, ma i tipi come `Span<T>` sono ideali per il supporto di indice/intervallo.
- `string`: non implementa `ICollection` e aggiunge `interface` un costo elevato.

Ciò significa che è già necessario supportare i tipi di chiave maiuscole/minuscole. L'involucro speciale di `string` è meno interessante perché il linguaggio esegue questa operazione in altre aree ( `foreach` riduzione, costanti e così via). L'involucro speciale di si riferisce `ref struct` maggiormente alla combinazione di maiuscole e minuscole per un'intera classe di tipi. Vengono etichettati come indicizzabili se hanno semplicemente una proprietà denominata `Count` con un tipo restituito `int` .

Dopo la considerazione, la progettazione è stata normalizzata per indicare che qualsiasi tipo con una proprietà `Count`  /  `Length` con un tipo restituito `int` è indicizzabile. Che rimuove tutte le maiuscole e minuscole speciale, anche per le `string` matrici e.

### <a name="detect-just-count"></a>Rileva solo conteggio

Il rilevamento dei nomi delle proprietà `Count` o `Length` complica la progettazione. La scelta di una sola per la standardizzazione, tuttavia, non è sufficiente perché termina con l'esclusione di un numero elevato di tipi:

- Use `Length` : esclude praticamente ogni raccolta in System. Collections e negli spazi dei nomi secondari. Che tendono a derivare da `ICollection` e quindi preferiscono `Count` rispetto alla lunghezza.
- Use `Count` : esclusioni `string` , matrici `Span<T>` e `ref struct` tipi più basati

La complicazione aggiuntiva al rilevamento iniziale dei tipi indicizzabili è stata superata dalla relativa semplificazione in altri aspetti.

### <a name="choice-of-slice-as-a-name"></a>Scelta della sezione come nome

Il nome `Slice` è stato scelto poiché è il nome standard de facto per le operazioni di stile sezione in .NET. A partire da netcoreapp 2.1 tutti i tipi di stile span usano il nome `Slice` per le operazioni di sezionamento. Prima di netcoreapp 2.1 non ci sono esempi di sezionamento da esaminare per un esempio. I tipi come `List<T>` , `ArraySegment<T>` , `SortedList<T>` sarebbero stati ideali per l'sezionamento, ma il concetto non esisteva al momento dell'aggiunta dei tipi.

Pertanto, `Slice` essendo l'unico esempio, è stato scelto come nome.

### <a name="index-target-type-conversion"></a>Conversione del tipo di destinazione dell'indice

Un altro modo per visualizzare la `Index` trasformazione in un'espressione di indicizzatore è la conversione di un tipo di destinazione. Anziché eseguire l'associazione come se fosse presente un membro del form `return_type this[Index]` , il linguaggio assegna invece una conversione tipizzata di destinazione a `int` .

Questo concetto può essere generalizzato a tutti gli accessi ai membri sui tipi numerabili. Ogni volta che un'espressione con tipo `Index` viene usata come argomento per una chiamata del membro di istanza e il destinatario è conteggiabile, l'espressione avrà una conversione del tipo di destinazione in `int` . Le chiamate ai membri applicabili a questa conversione includono metodi, indicizzatori, proprietà, metodi di estensione e così via. Solo i costruttori vengono esclusi perché non dispongono di ricevitore.

La conversione del tipo di destinazione verrà implementata come indicato di seguito per qualsiasi espressione il cui tipo è `Index` . Ai fini della discussione, è possibile usare l'esempio seguente `receiver[expr]` :

- Quando `expr` è del form `^expr2` e il tipo di `expr2` è `int` , verrà convertito in `receiver.Length - expr2` .
- In caso contrario, verrà tradotto come `expr.GetOffset(receiver.Length)` .

Le `receiver` espressioni e verranno distribuite `Length` nel modo appropriato per assicurarsi che gli effetti collaterali vengano eseguiti solo una volta. Ad esempio:

``` csharp
class Collection {
    private int[] _array = new[] { 1, 2, 3 };

    int Length {
        get {
            Console.Write("Length ");
            return _array.Length;
        }
    }

    int GetAt(int index) => _array[index];
}

class SideEffect {
    Collection Get() {
        Console.Write("Get ");
        return new Collection();
    }

    void Use() {
        int i = Get().GetAt(^1);
        Console.WriteLine(i);
    }
}
```

Con questo codice viene stampato "Get length 3".

Questa funzionalità può essere utile per qualsiasi membro che dispone di un parametro che rappresenta un indice. Ad esempio `List<T>.InsertAt`. Questo può anche causare confusione, perché il linguaggio non può fornire indicazioni sulla necessità o meno di un'espressione per l'indicizzazione. È possibile convertire qualsiasi `Index` espressione in `int` quando si richiama un membro su un tipo conteggiabile.

Restrizioni:

- Questa conversione è applicabile solo quando l'espressione con tipo `Index` è direttamente un argomento per il membro. Non si applica ad alcuna espressione nidificata.

## <a name="decisions-made-during-implementation"></a>Decisioni prese durante l'implementazione

- Tutti i membri del criterio devono essere membri di istanza
- Se viene trovato un metodo length ma il tipo restituito è errato, continuare a cercare il conteggio
- L'indicizzatore usato per il modello di indice deve avere esattamente un parametro int
- Il metodo slice usato per il modello di intervallo deve avere esattamente due parametri int
- Quando si cercano i membri del modello, si cercano le definizioni originali, non i membri costruiti

## <a name="design-meetings"></a>Riunioni di progettazione

- [10 gennaio 2018](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-01-10.md)
- [18 gennaio 2018](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-01-18.md)
- [22 gennaio 2018](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-01-22.md)
- [3 dicembre 2018](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-12-03.md)
- [25 mar 2019](https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-03-25.md#pattern-based-indexing-with-index-and-range)
- [1 ° aprile 2019](https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-04-01.md)
- [15 aprile 2019](https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-04-15.md#follow-up-decisions-for-pattern-based-indexrange)
