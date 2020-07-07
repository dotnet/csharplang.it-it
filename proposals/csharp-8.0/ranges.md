---
ms.openlocfilehash: bee562d64c402e645879b7811cc82c971d8b61cc
ms.sourcegitcommit: 9b736b5b73321f18a04e02d26596388f7ceb42fc
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86052207"
---
# <a name="ranges"></a><span data-ttu-id="99b05-101">Intervalli</span><span class="sxs-lookup"><span data-stu-id="99b05-101">Ranges</span></span>

## <a name="summary"></a><span data-ttu-id="99b05-102">Summary</span><span class="sxs-lookup"><span data-stu-id="99b05-102">Summary</span></span>

<span data-ttu-id="99b05-103">Questa funzionalità prevede la distribuzione di due nuovi operatori che consentono `System.Index` di costruire `System.Range` oggetti e e di usarli per le raccolte di indicizzazione/sezionamento in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="99b05-103">This feature is about delivering two new operators that allow constructing `System.Index` and `System.Range` objects, and using them to index/slice collections at runtime.</span></span>

## <a name="overview"></a><span data-ttu-id="99b05-104">Panoramica</span><span class="sxs-lookup"><span data-stu-id="99b05-104">Overview</span></span>

### <a name="well-known-types-and-members"></a><span data-ttu-id="99b05-105">Tipi e membri ben noti</span><span class="sxs-lookup"><span data-stu-id="99b05-105">Well-known types and members</span></span>

<span data-ttu-id="99b05-106">Per utilizzare i nuovi moduli sintattici per `System.Index` e `System.Range` , possono essere necessari nuovi tipi e membri noti, a seconda dei moduli sintattici utilizzati.</span><span class="sxs-lookup"><span data-stu-id="99b05-106">To use the new syntactic forms for `System.Index` and `System.Range`, new well-known types and members may be necessary, depending on which syntactic forms are used.</span></span>

<span data-ttu-id="99b05-107">Per utilizzare l'operatore "Hat" ( `^` ), è necessario quanto segue</span><span class="sxs-lookup"><span data-stu-id="99b05-107">To use the "hat" operator (`^`), the following is required</span></span>

```csharp
namespace System
{
    public readonly struct Index
    {
        public Index(int value, bool fromEnd);
    }
}
```

<span data-ttu-id="99b05-108">Per usare il `System.Index` tipo come argomento in un accesso a un elemento della matrice, è necessario il membro seguente:</span><span class="sxs-lookup"><span data-stu-id="99b05-108">To use the `System.Index` type as an argument in an array element access, the following member is required:</span></span>

```csharp
int System.Index.GetOffset(int length);
```

<span data-ttu-id="99b05-109">La `..` sintassi per `System.Range` richiederà il `System.Range` tipo, nonché uno o più dei membri seguenti:</span><span class="sxs-lookup"><span data-stu-id="99b05-109">The `..` syntax for `System.Range` will require the `System.Range` type, as well as one or more of the following members:</span></span>

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

<span data-ttu-id="99b05-110">La `..` sintassi consente la mancata presenza di entrambi gli argomenti.</span><span class="sxs-lookup"><span data-stu-id="99b05-110">The `..` syntax allows for either, both, or none of its arguments to be absent.</span></span> <span data-ttu-id="99b05-111">Indipendentemente dal numero di argomenti, il `Range` costruttore è sempre sufficiente per l'utilizzo della `Range` sintassi.</span><span class="sxs-lookup"><span data-stu-id="99b05-111">Regardless of the number of arguments, the `Range` constructor is always sufficient for using the `Range` syntax.</span></span> <span data-ttu-id="99b05-112">Tuttavia, se uno degli altri membri è presente e uno o più `..` argomenti risultano mancanti, il membro appropriato può essere sostituito.</span><span class="sxs-lookup"><span data-stu-id="99b05-112">However, if any of the other members are present and one or more of the `..` arguments are missing, the appropriate member may be substituted.</span></span>

<span data-ttu-id="99b05-113">Infine, per un valore di tipo `System.Range` da utilizzare in un'espressione di accesso agli elementi della matrice, è necessario che sia presente il membro seguente:</span><span class="sxs-lookup"><span data-stu-id="99b05-113">Finally, for a value of type `System.Range` to be used in an array element access expression, the following member must be present:</span></span>

```csharp
namespace System.Runtime.CompilerServices
{
    public static class RuntimeHelpers
    {
        public static T[] GetSubArray<T>(T[] array, System.Range range);
    }
}
```

### <a name="systemindex"></a><span data-ttu-id="99b05-114">System. index</span><span class="sxs-lookup"><span data-stu-id="99b05-114">System.Index</span></span>

<span data-ttu-id="99b05-115">C# non è in grado di indicizzare una raccolta dalla fine, ma la maggior parte degli indicizzatori usa la nozione "from Start" o un'espressione "length-i".</span><span class="sxs-lookup"><span data-stu-id="99b05-115">C# has no way of indexing a collection from the end, but rather most indexers use the "from start" notion, or do a "length - i" expression.</span></span> <span data-ttu-id="99b05-116">Viene introdotta una nuova espressione di indice che significa "dalla fine".</span><span class="sxs-lookup"><span data-stu-id="99b05-116">We introduce a new Index expression that means "from the end".</span></span> <span data-ttu-id="99b05-117">La funzionalità introdurrà un nuovo operatore "Hat" del prefisso unario.</span><span class="sxs-lookup"><span data-stu-id="99b05-117">The feature will introduce a new unary prefix "hat" operator.</span></span> <span data-ttu-id="99b05-118">Il suo singolo operando deve essere convertibile in `System.Int32` .</span><span class="sxs-lookup"><span data-stu-id="99b05-118">Its single operand must be convertible to `System.Int32`.</span></span> <span data-ttu-id="99b05-119">Verrà ridotta alla `System.Index` chiamata al metodo factory appropriato.</span><span class="sxs-lookup"><span data-stu-id="99b05-119">It will be lowered into the appropriate `System.Index` factory method call.</span></span>

<span data-ttu-id="99b05-120">Viene aumentata la grammatica per *unary_expression* con il seguente formato di sintassi aggiuntivo:</span><span class="sxs-lookup"><span data-stu-id="99b05-120">We augment the grammar for *unary_expression* with the following additional syntax form:</span></span>

```antlr
unary_expression
    : '^' unary_expression
    ;
```

<span data-ttu-id="99b05-121">Questo indice viene chiamato *dall'operatore end* .</span><span class="sxs-lookup"><span data-stu-id="99b05-121">We call this the *index from end* operator.</span></span> <span data-ttu-id="99b05-122">L'indice predefinito degli operatori *finali* è il seguente:</span><span class="sxs-lookup"><span data-stu-id="99b05-122">The predefined *index from end* operators are as follows:</span></span>

```csharp
System.Index operator ^(int fromEnd);
```

<span data-ttu-id="99b05-123">Il comportamento di questo operatore viene definito solo per i valori di input maggiori o uguali a zero.</span><span class="sxs-lookup"><span data-stu-id="99b05-123">The behavior of this operator is only defined for input values greater than or equal to zero.</span></span>

<span data-ttu-id="99b05-124">Esempi:</span><span class="sxs-lookup"><span data-stu-id="99b05-124">Examples:</span></span>

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var thirdItem = array[2];    // array[2]
var lastItem = array[^1];    // array[new Index(1, fromEnd: true)]
```

#### <a name="systemrange"></a><span data-ttu-id="99b05-125">System. Range</span><span class="sxs-lookup"><span data-stu-id="99b05-125">System.Range</span></span>

<span data-ttu-id="99b05-126">C# non dispone di un modo sintattico per accedere a "intervalli" o "sezioni" di raccolte.</span><span class="sxs-lookup"><span data-stu-id="99b05-126">C# has no syntactic way to access "ranges" or "slices" of collections.</span></span> <span data-ttu-id="99b05-127">In genere gli utenti sono obbligati a implementare strutture complesse per filtrare/utilizzare le sezioni di memoria o ricorrere a metodi LINQ come `list.Skip(5).Take(2)` .</span><span class="sxs-lookup"><span data-stu-id="99b05-127">Usually users are forced to implement complex structures to filter/operate on slices of memory, or resort to LINQ methods like `list.Skip(5).Take(2)`.</span></span> <span data-ttu-id="99b05-128">Con l'aggiunta di `System.Span<T>` e di altri tipi simili, è più importante avere questo tipo di operazione supportato a un livello più profondo nel linguaggio/Runtime e avere l'interfaccia unificata.</span><span class="sxs-lookup"><span data-stu-id="99b05-128">With the addition of `System.Span<T>` and other similar types, it becomes more important to have this kind of operation supported on a deeper level in the language/runtime, and have the interface unified.</span></span>

<span data-ttu-id="99b05-129">Il linguaggio introdurrà un nuovo operatore di intervallo `x..y` .</span><span class="sxs-lookup"><span data-stu-id="99b05-129">The language will introduce a new range operator `x..y`.</span></span> <span data-ttu-id="99b05-130">Si tratta di un operatore infisso binario che accetta due espressioni.</span><span class="sxs-lookup"><span data-stu-id="99b05-130">It is a binary infix operator that accepts two expressions.</span></span> <span data-ttu-id="99b05-131">Uno degli operandi può essere omesso (esempi seguenti) ed è necessario convertirlo in `System.Index` .</span><span class="sxs-lookup"><span data-stu-id="99b05-131">Either operand can be omitted (examples below), and they have to be convertible to `System.Index`.</span></span> <span data-ttu-id="99b05-132">Verrà ridotta alla `System.Range` chiamata al metodo factory appropriato.</span><span class="sxs-lookup"><span data-stu-id="99b05-132">It will be lowered to the appropriate `System.Range` factory method call.</span></span>

<span data-ttu-id="99b05-133">Per introdurre un nuovo livello di precedenza, vengono sostituite le regole della grammatica C# per *multiplicative_expression* con le seguenti opzioni:</span><span class="sxs-lookup"><span data-stu-id="99b05-133">We replace the C# grammar rules for *multiplicative_expression* with the following (in order to introduce a new precedence level):</span></span>

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

<span data-ttu-id="99b05-134">Tutti i form dell' *operatore di intervallo* hanno la stessa precedenza.</span><span class="sxs-lookup"><span data-stu-id="99b05-134">All forms of the *range operator* have the same precedence.</span></span> <span data-ttu-id="99b05-135">Questo nuovo gruppo di precedenza è inferiore agli operatori *unari* e superiori rispetto agli *operatori aritmetici moltiplicativi*.</span><span class="sxs-lookup"><span data-stu-id="99b05-135">This new precedence group is lower than the *unary operators* and higher than the *multiplicative arithmetic operators*.</span></span>

<span data-ttu-id="99b05-136">Viene chiamato l'operatore `..` *Range*.</span><span class="sxs-lookup"><span data-stu-id="99b05-136">We call the `..` operator the *range operator*.</span></span> <span data-ttu-id="99b05-137">L'operatore di intervallo predefinito può essere compreso approssimativamente per corrispondere alla chiamata di un operatore incorporato nel formato seguente:</span><span class="sxs-lookup"><span data-stu-id="99b05-137">The built-in range operator can roughly be understood to correspond to the invocation of a built-in operator of this form:</span></span>

```csharp
System.Range operator ..(Index start = 0, Index end = ^0);
```

<span data-ttu-id="99b05-138">Esempi:</span><span class="sxs-lookup"><span data-stu-id="99b05-138">Examples:</span></span>

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var slice1 = array[2..^3];    // array[new Range(2, new Index(3, fromEnd: true))]
var slice2 = array[..^3];     // array[Range.EndAt(new Index(3, fromEnd: true))]
var slice3 = array[2..];      // array[Range.StartAt(2)]
var slice4 = array[..];       // array[Range.All]
```

<span data-ttu-id="99b05-139">Inoltre, `System.Index` deve disporre di una conversione implicita da `System.Int32` , in modo da evitare la necessità di eseguire l'overload di combinazioni di interi e indici su firme multidimensionali.</span><span class="sxs-lookup"><span data-stu-id="99b05-139">Moreover, `System.Index` should have an implicit conversion from `System.Int32`, in order to avoid the need to overload mixing integers and indexes over multi-dimensional signatures.</span></span>

## <a name="adding-index-and-range-support-to-existing-library-types"></a><span data-ttu-id="99b05-140">Aggiunta del supporto per l'indice e l'intervallo ai tipi di libreria esistenti</span><span class="sxs-lookup"><span data-stu-id="99b05-140">Adding Index and Range support to existing library types</span></span>

### <a name="implicit-index-support"></a><span data-ttu-id="99b05-141">Supporto per gli indici impliciti</span><span class="sxs-lookup"><span data-stu-id="99b05-141">Implicit Index support</span></span>

<span data-ttu-id="99b05-142">Il linguaggio fornirà un membro dell'indicizzatore di istanza con un solo parametro di tipo `Index` per i tipi che soddisfano i criteri seguenti:</span><span class="sxs-lookup"><span data-stu-id="99b05-142">The language will provide an instance indexer member with a single parameter of type `Index` for types which meet the following criteria:</span></span>

- <span data-ttu-id="99b05-143">Il tipo è conteggiabile.</span><span class="sxs-lookup"><span data-stu-id="99b05-143">The type is Countable.</span></span>
- <span data-ttu-id="99b05-144">Il tipo dispone di un indicizzatore di istanza accessibile che accetta un singolo `int` come argomento.</span><span class="sxs-lookup"><span data-stu-id="99b05-144">The type has an accessible instance indexer which takes a single `int` as the argument.</span></span>
- <span data-ttu-id="99b05-145">Il tipo non dispone di un indicizzatore di istanza accessibile che accetta `Index` come primo parametro.</span><span class="sxs-lookup"><span data-stu-id="99b05-145">The type does not have an accessible instance indexer which takes an `Index` as the first parameter.</span></span> <span data-ttu-id="99b05-146">`Index`Deve essere l'unico parametro oppure i parametri rimanenti devono essere facoltativi.</span><span class="sxs-lookup"><span data-stu-id="99b05-146">The `Index` must be the only parameter or the remaining parameters must be optional.</span></span>

<span data-ttu-id="99b05-147">Un tipo può essere ***conteggiato*** se dispone di una proprietà denominata `Length` o `Count` con un getter accessibile e un tipo restituito `int` .</span><span class="sxs-lookup"><span data-stu-id="99b05-147">A type is ***Countable*** if it  has a property named `Length` or `Count` with an accessible getter and a return type of `int`.</span></span> <span data-ttu-id="99b05-148">Il linguaggio può usare questa proprietà per convertire un'espressione di tipo `Index` in un oggetto `int` in corrispondenza del punto dell'espressione senza la necessità di usare il tipo `Index` .</span><span class="sxs-lookup"><span data-stu-id="99b05-148">The language can make use of this property to convert an expression of type `Index` into an `int` at the point of the expression without the need to use the type `Index` at all.</span></span> <span data-ttu-id="99b05-149">Se `Length` `Count` sono presenti sia che, `Length` verranno preferiti.</span><span class="sxs-lookup"><span data-stu-id="99b05-149">In case both `Length` and `Count` are present, `Length` will be preferred.</span></span> <span data-ttu-id="99b05-150">Per semplicità, la proposta userà il nome `Length` per rappresentare `Count` o `Length` .</span><span class="sxs-lookup"><span data-stu-id="99b05-150">For simplicity going forward, the proposal will use the name `Length` to represent `Count` or `Length`.</span></span>

<span data-ttu-id="99b05-151">Per questi tipi, il linguaggio agirà come se fosse presente un membro dell'indicizzatore nel formato in `T this[Index index]` cui `T` è il tipo restituito dell' `int` indicizzatore basato su, incluse le `ref` annotazioni di stile.</span><span class="sxs-lookup"><span data-stu-id="99b05-151">For such types, the language will act as if there is an indexer member of the form `T this[Index index]` where `T` is the return type of the `int` based indexer including any `ref` style annotations.</span></span> <span data-ttu-id="99b05-152">Il nuovo membro avrà gli stessi `get` membri e `set` con l'accessibilità corrispondente dell' `int` indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="99b05-152">The new member will have the same `get` and `set` members with matching accessibility as the `int` indexer.</span></span>

<span data-ttu-id="99b05-153">Il nuovo indicizzatore verrà implementato convertendo l'argomento di tipo `Index` in un oggetto `int` ed emettendo una chiamata all' `int` indicizzatore basato su.</span><span class="sxs-lookup"><span data-stu-id="99b05-153">The new indexer will be implemented by converting the argument of type `Index` into an `int` and emitting a call to the `int` based indexer.</span></span> <span data-ttu-id="99b05-154">Ai fini della discussione, si userà l'esempio di `receiver[expr]` .</span><span class="sxs-lookup"><span data-stu-id="99b05-154">For discussion purposes, let's use the example of `receiver[expr]`.</span></span> <span data-ttu-id="99b05-155">La conversione di `expr` in `int` viene eseguita nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="99b05-155">The conversion of `expr` to `int` will occur as follows:</span></span>

- <span data-ttu-id="99b05-156">Quando l'argomento è del formato `^expr2` e il tipo di `expr2` è `int` , verrà convertito in `receiver.Length - expr2` .</span><span class="sxs-lookup"><span data-stu-id="99b05-156">When the argument is of the form `^expr2` and the type of `expr2` is `int`, it will be translated to `receiver.Length - expr2`.</span></span>
- <span data-ttu-id="99b05-157">In caso contrario, verrà tradotto come `expr.GetOffset(receiver.Length)` .</span><span class="sxs-lookup"><span data-stu-id="99b05-157">Otherwise, it will be translated as `expr.GetOffset(receiver.Length)`.</span></span>

<span data-ttu-id="99b05-158">Questo consente agli sviluppatori di usare la `Index` funzionalità sui tipi esistenti senza che sia necessario apportare modifiche.</span><span class="sxs-lookup"><span data-stu-id="99b05-158">This allows for developers to use the `Index` feature on existing types without the need for modification.</span></span> <span data-ttu-id="99b05-159">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="99b05-159">For example:</span></span>

``` csharp
List<char> list = ...;
var value = list[^1];

// Gets translated to
var value = list[list.Count - 1];
```

<span data-ttu-id="99b05-160">Le `receiver` espressioni e verranno distribuite `Length` nel modo appropriato per assicurarsi che gli effetti collaterali vengano eseguiti solo una volta.</span><span class="sxs-lookup"><span data-stu-id="99b05-160">The `receiver` and `Length` expressions will be spilled as appropriate to ensure any side effects are only executed once.</span></span> <span data-ttu-id="99b05-161">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="99b05-161">For example:</span></span>

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

<span data-ttu-id="99b05-162">Con questo codice viene stampato "Get length 3".</span><span class="sxs-lookup"><span data-stu-id="99b05-162">This code will print "Get Length 3".</span></span>

### <a name="implicit-range-support"></a><span data-ttu-id="99b05-163">Supporto di intervalli impliciti</span><span class="sxs-lookup"><span data-stu-id="99b05-163">Implicit Range support</span></span>

<span data-ttu-id="99b05-164">Il linguaggio fornirà un membro dell'indicizzatore di istanza con un solo parametro di tipo `Range` per i tipi che soddisfano i criteri seguenti:</span><span class="sxs-lookup"><span data-stu-id="99b05-164">The language will provide an instance indexer member with a single parameter of type `Range` for types which meet the following criteria:</span></span>

- <span data-ttu-id="99b05-165">Il tipo è conteggiabile.</span><span class="sxs-lookup"><span data-stu-id="99b05-165">The type is Countable.</span></span>
- <span data-ttu-id="99b05-166">Il tipo dispone di un membro accessibile denominato `Slice` che ha due parametri di tipo `int` .</span><span class="sxs-lookup"><span data-stu-id="99b05-166">The type has an accessible member named `Slice` which has two parameters of type `int`.</span></span>
- <span data-ttu-id="99b05-167">Il tipo non dispone di un indicizzatore di istanza che accetta un singolo `Range` come primo parametro.</span><span class="sxs-lookup"><span data-stu-id="99b05-167">The type does not have an instance indexer which takes a single `Range` as the first parameter.</span></span> <span data-ttu-id="99b05-168">`Range`Deve essere l'unico parametro oppure i parametri rimanenti devono essere facoltativi.</span><span class="sxs-lookup"><span data-stu-id="99b05-168">The `Range` must be the only parameter or the remaining parameters must be optional.</span></span>

<span data-ttu-id="99b05-169">Per questi tipi, il linguaggio viene associato come se fosse presente un membro dell'indicizzatore nel formato in `T this[Range range]` cui `T` è il tipo restituito del `Slice` metodo, incluse le `ref` annotazioni di stile.</span><span class="sxs-lookup"><span data-stu-id="99b05-169">For such types, the language will bind as if there is an indexer member of the form `T this[Range range]` where `T` is the return type of the `Slice` method including any `ref` style annotations.</span></span> <span data-ttu-id="99b05-170">Il nuovo membro avrà anche l'accessibilità corrispondente a `Slice` .</span><span class="sxs-lookup"><span data-stu-id="99b05-170">The new member will also have matching accessibility with `Slice`.</span></span>

<span data-ttu-id="99b05-171">Quando l' `Range` indicizzatore basato è associato a un'espressione denominata `receiver` , viene ridotto convertendo l' `Range` espressione in due valori che vengono quindi passati al `Slice` metodo.</span><span class="sxs-lookup"><span data-stu-id="99b05-171">When the `Range` based indexer is bound on an expression named `receiver`, it will be lowered by converting the `Range` expression into two values that are then passed to the `Slice` method.</span></span> <span data-ttu-id="99b05-172">Ai fini della discussione, si userà l'esempio di `receiver[expr]` .</span><span class="sxs-lookup"><span data-stu-id="99b05-172">For discussion purposes, let's use the example of `receiver[expr]`.</span></span>

<span data-ttu-id="99b05-173">Il primo argomento di `Slice` verrà ottenuto convertendo l'espressione tipizzata Range nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="99b05-173">The first argument of `Slice` will be obtained by converting the range typed expression in the following way:</span></span>

- <span data-ttu-id="99b05-174">Quando `expr` è del form `expr1..expr2` (dove `expr2` può essere omesso) e `expr1` ha il tipo `int` , verrà generato come `expr1` .</span><span class="sxs-lookup"><span data-stu-id="99b05-174">When `expr` is of the form `expr1..expr2` (where `expr2` can be omitted) and `expr1` has type `int`, then it will be emitted as `expr1`.</span></span>
- <span data-ttu-id="99b05-175">Quando `expr` è nel formato `^expr1..expr2` (dove `expr2` può essere omesso), verrà generato come `receiver.Length - expr1` .</span><span class="sxs-lookup"><span data-stu-id="99b05-175">When `expr` is of the form `^expr1..expr2` (where `expr2` can be omitted), then it will be emitted as `receiver.Length - expr1`.</span></span>
- <span data-ttu-id="99b05-176">Quando `expr` è nel formato `..expr2` (dove `expr2` può essere omesso), verrà generato come `0` .</span><span class="sxs-lookup"><span data-stu-id="99b05-176">When `expr` is of the form `..expr2` (where `expr2` can be omitted), then it will be emitted as `0`.</span></span>
- <span data-ttu-id="99b05-177">In caso contrario, verrà generato come `expr.Start.GetOffset(receiver.Length)` .</span><span class="sxs-lookup"><span data-stu-id="99b05-177">Otherwise, it will be emitted as `expr.Start.GetOffset(receiver.Length)`.</span></span>

<span data-ttu-id="99b05-178">Questo valore verrà nuovamente utilizzato nel calcolo del secondo `Slice` argomento.</span><span class="sxs-lookup"><span data-stu-id="99b05-178">This value will be re-used in the calculation of the second `Slice` argument.</span></span> <span data-ttu-id="99b05-179">Quando si esegue questa operazione, viene definito `start` .</span><span class="sxs-lookup"><span data-stu-id="99b05-179">When doing so it will be referred to as `start`.</span></span> <span data-ttu-id="99b05-180">Il secondo argomento di `Slice` verrà ottenuto convertendo l'espressione tipizzata Range nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="99b05-180">The second argument of `Slice` will be obtained by converting the range typed expression in the following way:</span></span>

- <span data-ttu-id="99b05-181">Quando `expr` è del form `expr1..expr2` (dove `expr1` può essere omesso) e `expr2` ha il tipo `int` , verrà generato come `expr2 - start` .</span><span class="sxs-lookup"><span data-stu-id="99b05-181">When `expr` is of the form `expr1..expr2` (where `expr1` can be omitted) and `expr2` has type `int`, then it will be emitted as `expr2 - start`.</span></span>
- <span data-ttu-id="99b05-182">Quando `expr` è nel formato `expr1..^expr2` (dove `expr1` può essere omesso), verrà generato come `(receiver.Length - expr2) - start` .</span><span class="sxs-lookup"><span data-stu-id="99b05-182">When `expr` is of the form `expr1..^expr2` (where `expr1` can be omitted), then it will be emitted as `(receiver.Length - expr2) - start`.</span></span>
- <span data-ttu-id="99b05-183">Quando `expr` è nel formato `expr1..` (dove `expr1` può essere omesso), verrà generato come `receiver.Length - start` .</span><span class="sxs-lookup"><span data-stu-id="99b05-183">When `expr` is of the form `expr1..` (where `expr1` can be omitted), then it will be emitted as `receiver.Length - start`.</span></span>
- <span data-ttu-id="99b05-184">In caso contrario, verrà generato come `expr.End.GetOffset(receiver.Length) - start` .</span><span class="sxs-lookup"><span data-stu-id="99b05-184">Otherwise, it will be emitted as `expr.End.GetOffset(receiver.Length) - start`.</span></span>

<span data-ttu-id="99b05-185">Le `receiver` `Length` espressioni, e verranno distribuite nel modo `expr` appropriato per assicurarsi che gli effetti collaterali vengano eseguiti solo una volta.</span><span class="sxs-lookup"><span data-stu-id="99b05-185">The `receiver`, `Length`, and `expr` expressions will be spilled as appropriate to ensure any side effects are only executed once.</span></span> <span data-ttu-id="99b05-186">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="99b05-186">For example:</span></span>

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

<span data-ttu-id="99b05-187">Con questo codice viene stampato "get Length 2".</span><span class="sxs-lookup"><span data-stu-id="99b05-187">This code will print "Get Length 2".</span></span>

<span data-ttu-id="99b05-188">Il linguaggio è il caso speciale dei seguenti tipi noti:</span><span class="sxs-lookup"><span data-stu-id="99b05-188">The language will special case the following known types:</span></span>

- <span data-ttu-id="99b05-189">`string`: verrà usato il metodo al `Substring` posto di `Slice` .</span><span class="sxs-lookup"><span data-stu-id="99b05-189">`string`: the method `Substring` will be used instead of `Slice`.</span></span>
- <span data-ttu-id="99b05-190">`array`: verrà usato il metodo al `System.Reflection.CompilerServices.GetSubArray` posto di `Slice` .</span><span class="sxs-lookup"><span data-stu-id="99b05-190">`array`: the method `System.Reflection.CompilerServices.GetSubArray` will be used instead of `Slice`.</span></span>

## <a name="alternatives"></a><span data-ttu-id="99b05-191">Alternativi</span><span class="sxs-lookup"><span data-stu-id="99b05-191">Alternatives</span></span>

<span data-ttu-id="99b05-192">I nuovi operatori ( `^` e `..` ) sono dello zucchero sintattico.</span><span class="sxs-lookup"><span data-stu-id="99b05-192">The new operators (`^` and `..`) are syntactic sugar.</span></span> <span data-ttu-id="99b05-193">La funzionalità può essere implementata da chiamate esplicite ai `System.Index` `System.Range` metodi factory, ma comporterà un maggior numero di codice standard e l'esperienza non sarà intuitiva.</span><span class="sxs-lookup"><span data-stu-id="99b05-193">The functionality can be implemented by explicit calls to `System.Index` and `System.Range` factory methods, but it will result in a lot more boilerplate code, and the experience will be unintuitive.</span></span>

## <a name="il-representation"></a><span data-ttu-id="99b05-194">Rappresentazione IL</span><span class="sxs-lookup"><span data-stu-id="99b05-194">IL Representation</span></span>

<span data-ttu-id="99b05-195">Questi due operatori verranno abbassati alle normali chiamate all'indicizzatore o al metodo, senza modifiche nei livelli del compilatore successivi.</span><span class="sxs-lookup"><span data-stu-id="99b05-195">These two operators will be lowered to regular indexer/method calls, with no change in subsequent compiler layers.</span></span>

## <a name="runtime-behavior"></a><span data-ttu-id="99b05-196">Comportamento in fase di esecuzione</span><span class="sxs-lookup"><span data-stu-id="99b05-196">Runtime behavior</span></span>

- <span data-ttu-id="99b05-197">Il compilatore può ottimizzare gli indicizzatori per i tipi incorporati, ad esempio matrici e stringhe, e ridurre l'indicizzazione ai metodi esistenti appropriati.</span><span class="sxs-lookup"><span data-stu-id="99b05-197">Compiler can optimize indexers for built-in types like arrays and strings, and lower the indexing to the appropriate existing methods.</span></span>
- <span data-ttu-id="99b05-198">`System.Index`genererà se costruito con un valore negativo.</span><span class="sxs-lookup"><span data-stu-id="99b05-198">`System.Index` will throw if constructed with a negative value.</span></span>
- <span data-ttu-id="99b05-199">`^0`non genera un'eccezione, ma si traduce nella lunghezza della raccolta/enumerabile a cui viene fornito.</span><span class="sxs-lookup"><span data-stu-id="99b05-199">`^0` does not throw, but it translates to the length of the collection/enumerable it is supplied to.</span></span>
- <span data-ttu-id="99b05-200">`Range.All`è semanticamente equivalente a `0..^0` e può essere decostruito in questi indici.</span><span class="sxs-lookup"><span data-stu-id="99b05-200">`Range.All` is semantically equivalent to `0..^0`, and can be deconstructed to these indices.</span></span>

## <a name="considerations"></a><span data-ttu-id="99b05-201">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="99b05-201">Considerations</span></span>

### <a name="detect-indexable-based-on-icollection"></a><span data-ttu-id="99b05-202">Rileva indicizzabile basato su ICollection</span><span class="sxs-lookup"><span data-stu-id="99b05-202">Detect Indexable based on ICollection</span></span>

<span data-ttu-id="99b05-203">L'ispirazione per questo comportamento è stata inizializzatori di raccolta.</span><span class="sxs-lookup"><span data-stu-id="99b05-203">The inspiration for this behavior was collection initializers.</span></span> <span data-ttu-id="99b05-204">Uso della struttura di un tipo per indicare che ha scelto una funzionalità.</span><span class="sxs-lookup"><span data-stu-id="99b05-204">Using the structure of a type to convey that it had opted into a feature.</span></span> <span data-ttu-id="99b05-205">Nel caso di inizializzatori di insieme, i tipi possono optare per la funzionalità implementando l'interfaccia `IEnumerable` (non generica).</span><span class="sxs-lookup"><span data-stu-id="99b05-205">In the case of collection initializers types can opt into the feature by implementing the interface `IEnumerable` (non generic).</span></span>

<span data-ttu-id="99b05-206">Questa proposta ha inizialmente richiesto che i tipi implementino `ICollection` per essere qualificati come indicizzabili.</span><span class="sxs-lookup"><span data-stu-id="99b05-206">This proposal initially required that types implement `ICollection` in order to qualify as Indexable.</span></span> <span data-ttu-id="99b05-207">Che richiedevano diversi casi speciali:</span><span class="sxs-lookup"><span data-stu-id="99b05-207">That required a number of special cases though:</span></span>

- <span data-ttu-id="99b05-208">`ref struct`: non possono implementare interfacce, ma i tipi come `Span<T>` sono ideali per il supporto di indice/intervallo.</span><span class="sxs-lookup"><span data-stu-id="99b05-208">`ref struct`: these cannot implement interfaces yet types like `Span<T>` are ideal for index / range support.</span></span>
- <span data-ttu-id="99b05-209">`string`: non implementa `ICollection` e aggiunge `interface` un costo elevato.</span><span class="sxs-lookup"><span data-stu-id="99b05-209">`string`: does not implement `ICollection` and adding that `interface` has a large cost.</span></span>

<span data-ttu-id="99b05-210">Ciò significa che è già necessario supportare i tipi di chiave maiuscole/minuscole.</span><span class="sxs-lookup"><span data-stu-id="99b05-210">This means to support key types special casing is already needed.</span></span> <span data-ttu-id="99b05-211">L'involucro speciale di `string` è meno interessante perché il linguaggio esegue questa operazione in altre aree ( `foreach` riduzione, costanti e così via). L'involucro speciale di si riferisce `ref struct` maggiormente alla combinazione di maiuscole e minuscole per un'intera classe di tipi.</span><span class="sxs-lookup"><span data-stu-id="99b05-211">The special casing of `string` is less interesting as the language does this in other areas (`foreach` lowering, constants, etc ...). The special casing of `ref struct` is more concerning as it's special casing an entire class of types.</span></span> <span data-ttu-id="99b05-212">Vengono etichettati come indicizzabili se hanno semplicemente una proprietà denominata `Count` con un tipo restituito `int` .</span><span class="sxs-lookup"><span data-stu-id="99b05-212">They get labeled as Indexable if they simply have a property named `Count` with a return type of `int`.</span></span>

<span data-ttu-id="99b05-213">Dopo la considerazione, la progettazione è stata normalizzata per indicare che qualsiasi tipo con una proprietà `Count`  /  `Length` con un tipo restituito `int` è indicizzabile.</span><span class="sxs-lookup"><span data-stu-id="99b05-213">After consideration the design was normalized to say that any type which has a property `Count` / `Length` with a return type of `int` is Indexable.</span></span> <span data-ttu-id="99b05-214">Che rimuove tutte le maiuscole e minuscole speciale, anche per le `string` matrici e.</span><span class="sxs-lookup"><span data-stu-id="99b05-214">That removes all special casing, even for `string` and arrays.</span></span>

### <a name="detect-just-count"></a><span data-ttu-id="99b05-215">Rileva solo conteggio</span><span class="sxs-lookup"><span data-stu-id="99b05-215">Detect just Count</span></span>

<span data-ttu-id="99b05-216">Il rilevamento dei nomi delle proprietà `Count` o `Length` complica la progettazione.</span><span class="sxs-lookup"><span data-stu-id="99b05-216">Detecting on the property names `Count` or `Length` does complicate the design a bit.</span></span> <span data-ttu-id="99b05-217">La scelta di una sola per la standardizzazione, tuttavia, non è sufficiente perché termina con l'esclusione di un numero elevato di tipi:</span><span class="sxs-lookup"><span data-stu-id="99b05-217">Picking just one to standardize though is not sufficient as it ends up excluding a large number of types:</span></span>

- <span data-ttu-id="99b05-218">Use `Length` : esclude praticamente ogni raccolta in System. Collections e negli spazi dei nomi secondari.</span><span class="sxs-lookup"><span data-stu-id="99b05-218">Use `Length`: excludes pretty much every collection in System.Collections and sub-namespaces.</span></span> <span data-ttu-id="99b05-219">Che tendono a derivare da `ICollection` e quindi preferiscono `Count` rispetto alla lunghezza.</span><span class="sxs-lookup"><span data-stu-id="99b05-219">Those tend to derive from `ICollection` and hence prefer `Count` over length.</span></span>
- <span data-ttu-id="99b05-220">Use `Count` : esclusioni `string` , matrici `Span<T>` e `ref struct` tipi più basati</span><span class="sxs-lookup"><span data-stu-id="99b05-220">Use `Count`: excludes `string`, arrays, `Span<T>` and most `ref struct` based types</span></span>

<span data-ttu-id="99b05-221">La complicazione aggiuntiva al rilevamento iniziale dei tipi indicizzabili è stata superata dalla relativa semplificazione in altri aspetti.</span><span class="sxs-lookup"><span data-stu-id="99b05-221">The extra complication on the initial detection of Indexable types is outweighed by its simplification in other aspects.</span></span>

### <a name="choice-of-slice-as-a-name"></a><span data-ttu-id="99b05-222">Scelta della sezione come nome</span><span class="sxs-lookup"><span data-stu-id="99b05-222">Choice of Slice as a name</span></span>

<span data-ttu-id="99b05-223">Il nome `Slice` è stato scelto poiché è il nome standard de facto per le operazioni di stile sezione in .NET.</span><span class="sxs-lookup"><span data-stu-id="99b05-223">The name `Slice` was chosen as it's the de-facto standard name for slice style operations in .NET.</span></span> <span data-ttu-id="99b05-224">A partire da netcoreapp 2.1 tutti i tipi di stile span usano il nome `Slice` per le operazioni di sezionamento.</span><span class="sxs-lookup"><span data-stu-id="99b05-224">Starting with netcoreapp2.1 all span style types use the name `Slice` for slicing operations.</span></span> <span data-ttu-id="99b05-225">Prima di netcoreapp 2.1 non ci sono esempi di sezionamento da esaminare per un esempio.</span><span class="sxs-lookup"><span data-stu-id="99b05-225">Prior to netcoreapp2.1 there really aren't any examples of slicing to look to for an example.</span></span> <span data-ttu-id="99b05-226">I tipi come `List<T>` , `ArraySegment<T>` , `SortedList<T>` sarebbero stati ideali per l'sezionamento, ma il concetto non esisteva al momento dell'aggiunta dei tipi.</span><span class="sxs-lookup"><span data-stu-id="99b05-226">Types like `List<T>`, `ArraySegment<T>`, `SortedList<T>` would've been ideal for slicing but the concept didn't exist when types were added.</span></span>

<span data-ttu-id="99b05-227">Pertanto, `Slice` essendo l'unico esempio, è stato scelto come nome.</span><span class="sxs-lookup"><span data-stu-id="99b05-227">Thus, `Slice` being the sole example, it was chosen as the name.</span></span>

### <a name="index-target-type-conversion"></a><span data-ttu-id="99b05-228">Conversione del tipo di destinazione dell'indice</span><span class="sxs-lookup"><span data-stu-id="99b05-228">Index target type conversion</span></span>

<span data-ttu-id="99b05-229">Un altro modo per visualizzare la `Index` trasformazione in un'espressione di indicizzatore è la conversione di un tipo di destinazione.</span><span class="sxs-lookup"><span data-stu-id="99b05-229">Another way to view the `Index` transformation in an indexer expression is as a target type conversion.</span></span> <span data-ttu-id="99b05-230">Anziché eseguire l'associazione come se fosse presente un membro del form `return_type this[Index]` , il linguaggio assegna invece una conversione tipizzata di destinazione a `int` .</span><span class="sxs-lookup"><span data-stu-id="99b05-230">Instead of binding as if there is a member of the form `return_type this[Index]`, the language instead assigns a target typed conversion to `int`.</span></span>

<span data-ttu-id="99b05-231">Questo concetto può essere generalizzato a tutti gli accessi ai membri sui tipi numerabili.</span><span class="sxs-lookup"><span data-stu-id="99b05-231">This concept could be generalized to all member access on Countable types.</span></span> <span data-ttu-id="99b05-232">Ogni volta che un'espressione con tipo `Index` viene usata come argomento per una chiamata del membro di istanza e il destinatario è conteggiabile, l'espressione avrà una conversione del tipo di destinazione in `int` .</span><span class="sxs-lookup"><span data-stu-id="99b05-232">Whenever an expression with type `Index` is used as an argument to an instance member invocation and the receiver is Countable then the expression will have a target type conversion to `int`.</span></span> <span data-ttu-id="99b05-233">Le chiamate ai membri applicabili a questa conversione includono metodi, indicizzatori, proprietà, metodi di estensione e così via. Solo i costruttori vengono esclusi perché non dispongono di ricevitore.</span><span class="sxs-lookup"><span data-stu-id="99b05-233">The member invocations applicable for this conversion include methods, indexers, properties, extension methods, etc ... Only constructors are excluded as they have no receiver.</span></span>

<span data-ttu-id="99b05-234">La conversione del tipo di destinazione verrà implementata come indicato di seguito per qualsiasi espressione il cui tipo è `Index` .</span><span class="sxs-lookup"><span data-stu-id="99b05-234">The target type conversion will be implemented as follows for any expression which has a type of `Index`.</span></span> <span data-ttu-id="99b05-235">Ai fini della discussione, è possibile usare l'esempio seguente `receiver[expr]` :</span><span class="sxs-lookup"><span data-stu-id="99b05-235">For discussion purposes lets use the example of `receiver[expr]`:</span></span>

- <span data-ttu-id="99b05-236">Quando `expr` è del form `^expr2` e il tipo di `expr2` è `int` , verrà convertito in `receiver.Length - expr2` .</span><span class="sxs-lookup"><span data-stu-id="99b05-236">When `expr` is of the form `^expr2` and the type of `expr2` is `int`, it will be translated to `receiver.Length - expr2`.</span></span>
- <span data-ttu-id="99b05-237">In caso contrario, verrà tradotto come `expr.GetOffset(receiver.Length)` .</span><span class="sxs-lookup"><span data-stu-id="99b05-237">Otherwise, it will be translated as `expr.GetOffset(receiver.Length)`.</span></span>

<span data-ttu-id="99b05-238">Le `receiver` espressioni e verranno distribuite `Length` nel modo appropriato per assicurarsi che gli effetti collaterali vengano eseguiti solo una volta.</span><span class="sxs-lookup"><span data-stu-id="99b05-238">The `receiver` and `Length` expressions will be spilled as appropriate to ensure any side effects are only executed once.</span></span> <span data-ttu-id="99b05-239">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="99b05-239">For example:</span></span>

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

<span data-ttu-id="99b05-240">Con questo codice viene stampato "Get length 3".</span><span class="sxs-lookup"><span data-stu-id="99b05-240">This code will print "Get Length 3".</span></span>

<span data-ttu-id="99b05-241">Questa funzionalità può essere utile per qualsiasi membro che dispone di un parametro che rappresenta un indice.</span><span class="sxs-lookup"><span data-stu-id="99b05-241">This feature would be beneficial to any member which had a parameter that represented an index.</span></span> <span data-ttu-id="99b05-242">Ad esempio `List<T>.InsertAt`.</span><span class="sxs-lookup"><span data-stu-id="99b05-242">For example `List<T>.InsertAt`.</span></span> <span data-ttu-id="99b05-243">Questo può anche causare confusione, perché il linguaggio non può fornire indicazioni sulla necessità o meno di un'espressione per l'indicizzazione.</span><span class="sxs-lookup"><span data-stu-id="99b05-243">This also has the potential for confusion as the language can't give any guidance as to whether or not an expression is meant for indexing.</span></span> <span data-ttu-id="99b05-244">È possibile convertire qualsiasi `Index` espressione in `int` quando si richiama un membro su un tipo conteggiabile.</span><span class="sxs-lookup"><span data-stu-id="99b05-244">All it can do is convert any `Index` expression to `int` when invoking a member on a Countable type.</span></span>

<span data-ttu-id="99b05-245">Restrizioni:</span><span class="sxs-lookup"><span data-stu-id="99b05-245">Restrictions:</span></span>

- <span data-ttu-id="99b05-246">Questa conversione è applicabile solo quando l'espressione con tipo `Index` è direttamente un argomento per il membro.</span><span class="sxs-lookup"><span data-stu-id="99b05-246">This conversion is only applicable when the expression with type `Index` is directly an argument to the member.</span></span> <span data-ttu-id="99b05-247">Non si applica ad alcuna espressione nidificata.</span><span class="sxs-lookup"><span data-stu-id="99b05-247">It would not apply to any nested expressions.</span></span>

## <a name="decisions-made-during-implementation"></a><span data-ttu-id="99b05-248">Decisioni prese durante l'implementazione</span><span class="sxs-lookup"><span data-stu-id="99b05-248">Decisions made during implementation</span></span>

- <span data-ttu-id="99b05-249">Tutti i membri del criterio devono essere membri di istanza</span><span class="sxs-lookup"><span data-stu-id="99b05-249">All members in the pattern must be instance members</span></span>
- <span data-ttu-id="99b05-250">Se viene trovato un metodo length ma il tipo restituito è errato, continuare a cercare il conteggio</span><span class="sxs-lookup"><span data-stu-id="99b05-250">If a Length method is found but it has the wrong return type, continue looking for Count</span></span>
- <span data-ttu-id="99b05-251">L'indicizzatore usato per il modello di indice deve avere esattamente un parametro int</span><span class="sxs-lookup"><span data-stu-id="99b05-251">The indexer used for the Index pattern must have exactly one int parameter</span></span>
- <span data-ttu-id="99b05-252">Il metodo slice usato per il modello di intervallo deve avere esattamente due parametri int</span><span class="sxs-lookup"><span data-stu-id="99b05-252">The Slice method used for the Range pattern must have exactly two int parameters</span></span>
- <span data-ttu-id="99b05-253">Quando si cercano i membri del modello, si cercano le definizioni originali, non i membri costruiti</span><span class="sxs-lookup"><span data-stu-id="99b05-253">When looking for the pattern members, we look for original definitions, not constructed members</span></span>

## <a name="design-meetings"></a><span data-ttu-id="99b05-254">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="99b05-254">Design meetings</span></span>

- [<span data-ttu-id="99b05-255">10 gennaio 2018</span><span class="sxs-lookup"><span data-stu-id="99b05-255">Jan 10, 2018</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-01-10.md)
- [<span data-ttu-id="99b05-256">18 gennaio 2018</span><span class="sxs-lookup"><span data-stu-id="99b05-256">Jan 18, 2018</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-01-18.md)
- [<span data-ttu-id="99b05-257">22 gennaio 2018</span><span class="sxs-lookup"><span data-stu-id="99b05-257">Jan 22, 2018</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-01-22.md)
- [<span data-ttu-id="99b05-258">3 dicembre 2018</span><span class="sxs-lookup"><span data-stu-id="99b05-258">Dec 3, 2018</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-12-03.md)
- [<span data-ttu-id="99b05-259">25 mar 2019</span><span class="sxs-lookup"><span data-stu-id="99b05-259">Mar 25, 2019</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-03-25.md#pattern-based-indexing-with-index-and-range)
- [<span data-ttu-id="99b05-260">1 ° aprile 2019</span><span class="sxs-lookup"><span data-stu-id="99b05-260">April 1st, 2019</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-04-01.md)
- [<span data-ttu-id="99b05-261">15 aprile 2019</span><span class="sxs-lookup"><span data-stu-id="99b05-261">April 15, 2019</span></span>](https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-04-15.md#follow-up-decisions-for-pattern-based-indexrange)
