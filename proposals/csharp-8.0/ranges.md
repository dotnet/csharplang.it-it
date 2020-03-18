---
ms.openlocfilehash: d6519ff57b4a98c4eec8ccbf310303432ac3255e
ms.sourcegitcommit: 65ea1e6dc02853e37e7f2088e2b6cc08d01d1044
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "79485028"
---
# <a name="ranges"></a>Ranges

## <a name="summary"></a>Summary

Questa funzionalità prevede la distribuzione di due nuovi operatori che consentono di costruire `System.Index` e `System.Range` oggetti e di usarli per indicizzare/sezionare raccolte in fase di esecuzione.

## <a name="overview"></a>Panoramica

### <a name="well-known-types-and-members"></a>Tipi e membri ben noti

Per utilizzare i nuovi moduli sintattici per `System.Index` e `System.Range`, possono essere necessari nuovi tipi e membri noti, a seconda dei moduli sintattici utilizzati.

Per utilizzare l'operatore "Hat" (`^`), è necessario quanto segue

```csharp
namespace System
{
    public readonly struct Index
    {
        public Index(int value, bool fromEnd);
    }
}
```

Per usare il tipo `System.Index` come argomento in un accesso a un elemento della matrice, è necessario il membro seguente:

```csharp
int System.Index.GetOffset(int length);
```

La sintassi `..` per `System.Range` richiederà il tipo di `System.Range`, oltre a uno o più dei membri seguenti:

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

La sintassi `..` consente l'assenza di entrambi gli argomenti, né nessuno dei relativi argomenti. Indipendentemente dal numero di argomenti, il costruttore `Range` è sempre sufficiente per l'utilizzo della sintassi di `Range`. Tuttavia, se uno degli altri membri è presente e uno o più argomenti di `..` risultano mancanti, il membro appropriato può essere sostituito.

Infine, per un valore di tipo `System.Range` essere utilizzato in un'espressione di accesso agli elementi della matrice, è necessario che sia presente il membro seguente:

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

C#non ha alcun modo di indicizzare una raccolta dalla fine, ma la maggior parte degli indicizzatori usa la nozione "from Start" o un'espressione "length-i". Viene introdotta una nuova espressione di indice che significa "dalla fine". La funzionalità introdurrà un nuovo operatore "Hat" del prefisso unario. Il suo singolo operando deve essere convertibile in `System.Int32`. Verrà ridotta nella chiamata al metodo factory `System.Index` appropriato.

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
var thirdItem = list[2];                // list[2]
var lastItem = list[^1];                // list[Index.CreateFromEnd(1)]

var multiDimensional = list[3, ^2]      // list[3, Index.CreateFromEnd(2)]
```

#### <a name="systemrange"></a>System. Range

C#non dispone di un modo sintattico per accedere a "intervalli" o "sezioni" di raccolte. In genere gli utenti sono obbligati a implementare strutture complesse per filtrare/utilizzare le sezioni di memoria o ricorrere a metodi LINQ come `list.Skip(5).Take(2)`. Con l'aggiunta di `System.Span<T>` e altri tipi simili, è più importante avere questo tipo di operazione supportato a un livello più profondo nel linguaggio/Runtime e avere l'interfaccia unificata.

Il linguaggio introdurrà un nuovo operatore di intervallo `x..y`. Si tratta di un operatore infisso binario che accetta due espressioni. Uno degli operandi può essere omesso (esempi seguenti) ed è necessario convertirlo in `System.Index`. Verrà ridotta alla chiamata al metodo factory `System.Range` appropriato.

-Le regole di C# grammatica per *multiplicative_expression* vengono sostituite con quanto segue (per introdurre un nuovo livello di precedenza):

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

Tutti i form dell' *operatore di intervallo* hanno la stessa precedenza. Questo nuovo gruppo di precedenza è inferiore agli operatori *unari* e superiori rispetto agli *operatori aritmetici mulitiplicative*.

L'operatore `..` viene chiamato operatore *Range*. L'operatore di intervallo predefinito può essere compreso approssimativamente per corrispondere alla chiamata di un operatore incorporato nel formato seguente:

```csharp
    System.Range operator ..(Index start = 0, Index end = ^0);
```

Esempi:

```csharp
var slice1 = list[2..^3];               // list[Range.Create(2, Index.CreateFromEnd(3))]
var slice2 = list[..^3];                // list[Range.ToEnd(Index.CreateFromEnd(3))]
var slice3 = list[2..];                 // list[Range.FromStart(2)]
var slice4 = list[..];                  // list[Range.All]

var multiDimensional = list[1..2, ..]   // list[Range.Create(1, 2), Range.All]
```

Inoltre, `System.Index` deve avere una conversione implicita da `System.Int32`, in modo da non dover eseguire l'overload per combinare numeri interi e indici su firme multidimensionali.

## <a name="adding-index-and-range-support-to-existing-library-types"></a>Aggiunta del supporto per l'indice e l'intervallo ai tipi di libreria esistenti

### <a name="implicit-index-support"></a>Supporto per gli indici impliciti

Il linguaggio fornirà un membro dell'indicizzatore di istanza con un solo parametro di tipo `Index` per i tipi che soddisfano i criteri seguenti:

- Il tipo è conteggiabile.
- Il tipo dispone di un indicizzatore di istanza accessibile che accetta un singolo `int` come argomento.
- Il tipo non dispone di un indicizzatore di istanza accessibile che accetta un `Index` come primo parametro. Il `Index` deve essere l'unico parametro oppure i parametri rimanenti devono essere facoltativi.

Un tipo può essere ***conteggiato*** se dispone di una proprietà denominata `Length` o `Count` con un getter accessibile e un tipo restituito di `int`. Il linguaggio può usare questa proprietà per convertire un'espressione di tipo `Index` in un `int` nel punto dell'espressione senza la necessità di usare il tipo `Index`. Se sono presenti sia `Length` che `Count`, `Length` sarà preferibile. Per semplicità, la proposta userà il nome `Length` per rappresentare `Count` o `Length`.

Per questi tipi, il linguaggio fungerà da un membro di indice nel formato `T this[Index index]` dove `T` è il tipo restituito dell'indicizzatore basato su `int`, incluse le annotazioni di stile `ref`. Il nuovo membro avrà lo stesso `get` e `set` membri con accessibilità corrispondente come indicizzatore `int`. 

Il nuovo indicizzatore verrà implementato convertendo l'argomento di tipo `Index` in un `int` e generando una chiamata all'indicizzatore basato su `int`. Ai fini della discussione, è possibile usare l'esempio di `receiver[expr]`. La conversione di `expr` in `int` si verificherà come segue:

- Quando l'argomento è nel formato `^expr2` e il tipo di `expr2` è `int`, viene convertito in `receiver.Length - expr2`.
- In caso contrario, verrà tradotto come `expr.GetOffset(receiver.Length)`.

Questo consente agli sviluppatori di usare la funzionalità `Index` sui tipi esistenti senza che sia necessario apportare modifiche. Ad esempio:

``` csharp
List<char> list = ...;
var value = list[^1]; 

// Gets translated to 
var value = list[list.Count - 1]; 
```

Le espressioni `receiver` e `Length` verranno distribuite nel modo appropriato per assicurarsi che gli effetti collaterali vengano eseguiti solo una volta. Ad esempio:

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
- Il tipo dispone di un membro accessibile denominato `Slice` che dispone di due parametri di tipo `int`.
- Il tipo non ha un indicizzatore di istanza che accetta un singolo `Range` come primo parametro. Il `Range` deve essere l'unico parametro oppure i parametri rimanenti devono essere facoltativi.

Per questi tipi, il linguaggio viene associato come se fosse presente un membro di indice nel formato `T this[Range range]` dove `T` è il tipo restituito del metodo `Slice`, incluse le annotazioni di stile `ref`. Il nuovo membro avrà anche l'accessibilità corrispondente con `Slice`. 

Quando l'indicizzatore basato su `Range` viene associato a un'espressione denominata `receiver`, verrà ridotto convertendo l'espressione `Range` in due valori che vengono quindi passati al metodo `Slice`. Ai fini della discussione, è possibile usare l'esempio di `receiver[expr]`.

Il primo argomento di `Slice` verrà ottenuto convertendo l'espressione tipizzata nel modo seguente:

- Quando `expr` è nel formato `expr1..expr2` (dove è possibile omettere `expr2`) e `expr1` è di tipo `int`, verrà generato come `expr1`.
- Quando `expr` è nel formato `^expr1..expr2` (dove `expr2` può essere omesso), verrà generato come `receiver.Length - expr1`.
- Quando `expr` è nel formato `..expr2` (dove `expr2` può essere omesso), verrà generato come `0`.
- In caso contrario, verrà generato come `expr.Start.GetOffset(receiver.Length)`.

Questo valore verrà riutilizzato nel calcolo del secondo argomento `Slice`. In tal caso, verrà indicato come `start`. Il secondo argomento di `Slice` verrà ottenuto convertendo l'espressione di tipo intervallo nel modo seguente:

- Quando `expr` è nel formato `expr1..expr2` (dove è possibile omettere `expr1`) e `expr2` è di tipo `int`, verrà generato come `expr2 - start`.
- Quando `expr` è nel formato `expr1..^expr2` (dove `expr1` può essere omesso), verrà generato come `(receiver.Length - expr2) - start`.
- Quando `expr` è nel formato `expr1..` (dove `expr1` può essere omesso), verrà generato come `receiver.Length - start`.
- In caso contrario, verrà generato come `expr.End.GetOffset(receiver.Length) - start`.

Le espressioni `receiver`, `Length` e `expr` verranno distribuite nel modo appropriato per assicurarsi che gli effetti collaterali vengano eseguiti solo una volta. Ad esempio:

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

- `string`: verrà usato il metodo `Substring` al posto di `Slice`.
- `array`: verrà usato il metodo `System.Reflection.CompilerServices.GetSubArray` al posto di `Slice`.

## <a name="alternatives"></a>Alternativi

I nuovi operatori (`^` e `..`) sono di zucchero sintattico. La funzionalità può essere implementata da chiamate esplicite a `System.Index` e `System.Range` metodi factory, ma comporterà un maggior numero di codice standard e l'esperienza non sarà intuitiva.

## <a name="il-representation"></a>Rappresentazione IL

Questi due operatori verranno abbassati alle normali chiamate all'indicizzatore o al metodo, senza modifiche nei livelli del compilatore successivi.

## <a name="runtime-behavior"></a>Comportamento in fase di esecuzione

- Il compilatore può ottimizzare gli indicizzatori per i tipi incorporati, ad esempio matrici e stringhe, e ridurre l'indicizzazione ai metodi esistenti appropriati.
- `System.Index` genererà se costruito con un valore negativo.
- `^0` non genera, ma viene convertito nella lunghezza della raccolta/enumerabile a cui viene fornito.
- `Range.All` è semanticamente equivalente ai `0..^0`e può essere decostruito in questi indici.

## <a name="considerations"></a>Considerazioni

### <a name="detect-indexable-based-on-icollection"></a>Rileva indicizzabile basato su ICollection

L'ispirazione per questo comportamento è stata inizializzatori di raccolta. Uso della struttura di un tipo per indicare che ha scelto una funzionalità. Nel caso di inizializzatori di insieme, i tipi possono optare per la funzionalità implementando l'interfaccia `IEnumerable` (non generico).

Questa proposta ha inizialmente richiesto che i tipi implementino `ICollection` per qualificarsi come indicizzabile. Che richiedevano diversi casi speciali:

- `ref struct`: non è possibile implementare interfacce, ma i tipi come `Span<T>` sono ideali per il supporto di indici/intervalli. 
- `string`: non implementa `ICollection` e l'aggiunta di tale `interface` ha un costo elevato.

Ciò significa che è già necessario supportare i tipi di chiave maiuscole/minuscole. La combinazione di maiuscole e minuscole di `string` è meno interessante in quanto il linguaggio esegue questa operazione in altre aree (`foreach` riduzione, costanti e così via). L'involucro speciale di `ref struct` si riferisce maggiormente alla combinazione di maiuscole e minuscole per un'intera classe di tipi. Vengono etichettati come indicizzabili se hanno semplicemente una proprietà denominata `Count` con un tipo restituito di `int`. 

Una volta considerato che la progettazione è stata normalizzata per indicare che qualsiasi tipo con una proprietà `Count` / `Length` con un tipo restituito di `int` è indicizzabile. Che rimuove tutte le maiuscole e minuscole, anche per `string` e matrici.

### <a name="detect-just-count"></a>Rileva solo conteggio

Il rilevamento dei nomi delle proprietà `Count` o `Length` complica leggermente la progettazione. La scelta di una sola per la standardizzazione, tuttavia, non è sufficiente perché termina con l'esclusione di un numero elevato di tipi:

- Use `Length`: esclude praticamente ogni raccolta in System. Collections e negli spazi dei nomi secondari. Che tendono a derivare da `ICollection` e quindi preferiscono `Count` lungo la lunghezza.
- Use `Count`: esclude `string`, matrici, `Span<T>` e la maggior parte dei tipi basati su `ref struct`

La complicazione aggiuntiva al rilevamento iniziale dei tipi indicizzabili è stata superata dalla relativa semplificazione in altri aspetti.

### <a name="choice-of-slice-as-a-name"></a>Scelta della sezione come nome

Il nome `Slice` è stato scelto poiché è il nome standard de facto per le operazioni di stile sezione in .NET. A partire da netcoreapp 2.1 tutti i tipi di stile span usano il nome `Slice` per le operazioni di sezionamento. Prima di netcoreapp 2.1 non ci sono esempi di sezionamento da esaminare per un esempio. I tipi come `List<T>`, `ArraySegment<T>``SortedList<T>` sarebbero stati ideali per l'sezionamento, ma il concetto non esisteva al momento dell'aggiunta dei tipi. 

Pertanto, `Slice` essere l'unico esempio, è stato scelto come nome.

### <a name="index-target-type-conversion"></a>Conversione del tipo di destinazione dell'indice

Un altro modo per visualizzare la trasformazione `Index` in un'espressione di indicizzatore è la conversione di un tipo di destinazione. Anziché eseguire l'associazione come se fosse presente un membro del form `return_type this[Index]`, il linguaggio assegna invece una conversione tipizzata di destinazione a `int`. 

Questo concetto può essere generalizzato a tutti gli accessi ai membri sui tipi numerabili. Ogni volta che un'espressione con tipo `Index` viene utilizzata come argomento per una chiamata a un membro di istanza e il destinatario è conteggiabile, l'espressione avrà una conversione del tipo di destinazione in `int`. Le chiamate ai membri applicabili a questa conversione includono metodi, indicizzatori, proprietà, metodi di estensione e così via. Solo i costruttori vengono esclusi perché non dispongono di ricevitore. 

La conversione del tipo di destinazione verrà implementata come indicato di seguito per qualsiasi espressione con un tipo di `Index`. Ai fini della discussione, è possibile usare l'esempio di `receiver[expr]`:

- Quando `expr` ha il formato `^expr2` e il tipo di `expr2` è `int`, verrà convertito in `receiver.Length - expr2`.
- In caso contrario, verrà tradotto come `expr.GetOffset(receiver.Length)`.

Le espressioni `receiver` e `Length` verranno distribuite nel modo appropriato per assicurarsi che gli effetti collaterali vengano eseguiti solo una volta. Ad esempio:

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

Questa funzionalità può essere utile per qualsiasi membro che dispone di un parametro che rappresenta un indice. Ad esempio `List<T>.InsertAt`. Questo può anche causare confusione, perché il linguaggio non può fornire indicazioni sulla necessità o meno di un'espressione per l'indicizzazione. È possibile convertire qualsiasi espressione `Index` in `int` quando si richiama un membro su un tipo conteggiabile. 

Restrizioni:

- Questa conversione è applicabile solo quando l'espressione con tipo `Index` è direttamente un argomento per il membro. Non si applica ad alcuna espressione nidificata.

## <a name="decisions-made-during-implementation"></a>Decisioni prese durante l'implementazione

- Tutti i membri del criterio devono essere membri di istanza
- Se viene trovato un metodo length ma il tipo restituito è errato, continuare a cercare il conteggio
- L'indicizzatore usato per il modello di indice deve avere esattamente un parametro int
- Il metodo slice usato per il modello di intervallo deve avere esattamente due parametri int
- Quando si cercano i membri del modello, si cercano le definizioni originali, non i membri costruiti

## <a name="design-meetings"></a>Riunioni di progettazione

- https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-04-01.md

## <a name="questions"></a>Domande
