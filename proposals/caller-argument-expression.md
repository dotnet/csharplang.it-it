---
ms.openlocfilehash: fa3326bf69c83b6042b1db7b5567fd5c28d6f81a
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484524"
---
# <a name="callerargumentexpression"></a><span data-ttu-id="08f30-101">CallerArgumentExpression</span><span class="sxs-lookup"><span data-stu-id="08f30-101">CallerArgumentExpression</span></span>

* <span data-ttu-id="08f30-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="08f30-102">[x] Proposed</span></span>
* <span data-ttu-id="08f30-103">[] Prototipo: non avviato</span><span class="sxs-lookup"><span data-stu-id="08f30-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="08f30-104">[] Implementazione: non avviata</span><span class="sxs-lookup"><span data-stu-id="08f30-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="08f30-105">[] Specifica: non avviata</span><span class="sxs-lookup"><span data-stu-id="08f30-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="08f30-106">Summary</span><span class="sxs-lookup"><span data-stu-id="08f30-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="08f30-107">Consente agli sviluppatori di acquisire le espressioni passate a un metodo per consentire messaggi di errore migliori nelle API di diagnostica/test e ridurre le sequenze di tasti.</span><span class="sxs-lookup"><span data-stu-id="08f30-107">Allow developers to capture the expressions passed to a method, to enable better error messages in diagnostic/testing APIs and reduce keystrokes.</span></span>

## <a name="motivation"></a><span data-ttu-id="08f30-108">Motivazione</span><span class="sxs-lookup"><span data-stu-id="08f30-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="08f30-109">Quando la convalida di un'asserzione o un argomento ha esito negativo, lo sviluppatore vuole sapere quanto più possibile su dove e perché non è riuscito.</span><span class="sxs-lookup"><span data-stu-id="08f30-109">When an assertion or argument validation fails, the developer wants to know as much as possible about where and why it failed.</span></span> <span data-ttu-id="08f30-110">Tuttavia, le API di diagnostica odierne non facilitano completamente questa operazione.</span><span class="sxs-lookup"><span data-stu-id="08f30-110">However, today's diagnostic APIs do not fully facilitate this.</span></span> <span data-ttu-id="08f30-111">Si consideri il seguente metodo:</span><span class="sxs-lookup"><span data-stu-id="08f30-111">Consider the following method:</span></span>

```csharp
T Single<T>(this T[] array)
{
    Debug.Assert(array != null);
    Debug.Assert(array.Length == 1);

    return array[0];
}
```

<span data-ttu-id="08f30-112">Quando una delle asserzioni ha esito negativo, nella traccia dello stack verranno specificati solo il nome del file, il numero di riga e il nome del metodo.</span><span class="sxs-lookup"><span data-stu-id="08f30-112">When one of the asserts fail, only the filename, line number, and method name will be provided in the stack trace.</span></span> <span data-ttu-id="08f30-113">Lo sviluppatore non sarà in grado di stabilire quale asserzione ha avuto esito negativo da queste informazioni: dovrà aprire il file e passare al numero di riga specificato per verificare che si è verificato un errore.</span><span class="sxs-lookup"><span data-stu-id="08f30-113">The developer will not be able to tell which assert failed from this information-- (s)he will have to open the file and navigate to the provided line number to see what went wrong.</span></span>

<span data-ttu-id="08f30-114">Questo è anche il motivo per cui i Framework di test devono fornire un'ampia gamma di metodi Assert.</span><span class="sxs-lookup"><span data-stu-id="08f30-114">This is also the reason testing frameworks have to provide a variety of assert methods.</span></span> <span data-ttu-id="08f30-115">Con xUnit, `Assert.True` e `Assert.False` non vengono usati di frequente perché non forniscono un contesto sufficiente sulle operazioni non riuscite.</span><span class="sxs-lookup"><span data-stu-id="08f30-115">With xUnit, `Assert.True` and `Assert.False` are not frequently used because they do not provide enough context about what failed.</span></span>

<span data-ttu-id="08f30-116">Anche se la situazione è un po' migliore per la convalida degli argomenti poiché i nomi degli argomenti non validi vengono visualizzati allo sviluppatore, lo sviluppatore deve passare manualmente questi nomi alle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="08f30-116">While the situation is a bit better for argument validation because the names of invalid arguments are shown to the developer, the developer must pass these names to exceptions manually.</span></span> <span data-ttu-id="08f30-117">Se l'esempio precedente è stato riscritto in modo da usare la convalida degli argomenti tradizionale invece di `Debug.Assert`, l'aspetto sarà simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="08f30-117">If the above example were rewritten to use traditional argument validation instead of `Debug.Assert`, it would look like</span></span>

```csharp
T Single<T>(this T[] array)
{
    if (array == null)
    {
        throw new ArgumentNullException(nameof(array));
    }

    if (array.Length != 1)
    {
        throw new ArgumentException("Array must contain a single element.", nameof(array));
    }

    return array[0];
}
```

<span data-ttu-id="08f30-118">Si noti che `nameof(array)` necessario passare a ogni eccezione, sebbene sia già chiaro dal contesto quale argomento non è valido.</span><span class="sxs-lookup"><span data-stu-id="08f30-118">Notice that `nameof(array)` must be passed to each exception, although it's already clear from context which argument is invalid.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="08f30-119">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="08f30-119">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="08f30-120">Negli esempi precedenti, includendo la stringa `"array != null"` o `"array.Length == 1"` nel messaggio di asserzione, lo sviluppatore può determinare le operazioni non riuscite.</span><span class="sxs-lookup"><span data-stu-id="08f30-120">In the above examples, including the string `"array != null"` or `"array.Length == 1"` in the assert message would help the developer determine what failed.</span></span> <span data-ttu-id="08f30-121">Immettere `CallerArgumentExpression`: si tratta di un attributo che può essere usato dal Framework per ottenere la stringa associata a un argomento specifico del metodo.</span><span class="sxs-lookup"><span data-stu-id="08f30-121">Enter `CallerArgumentExpression`: it's an attribute the framework can use to obtain the string associated with a particular method argument.</span></span> <span data-ttu-id="08f30-122">Lo aggiungeremo a `Debug.Assert` in questo modo</span><span class="sxs-lookup"><span data-stu-id="08f30-122">We would add it to `Debug.Assert` like so</span></span>

```csharp
public static class Debug
{
    public static void Assert(bool condition, [CallerArgumentExpression("condition")] string message = null);
}
```

<span data-ttu-id="08f30-123">Il codice sorgente nell'esempio precedente rimane invariato.</span><span class="sxs-lookup"><span data-stu-id="08f30-123">The source code in the above example would stay the same.</span></span> <span data-ttu-id="08f30-124">Tuttavia, il codice che il compilatore emette effettivamente corrisponde a</span><span class="sxs-lookup"><span data-stu-id="08f30-124">However, the code the compiler actually emits would correspond to</span></span>

```csharp
T Single<T>(this T[] array)
{
    Debug.Assert(array != null, "array != null");
    Debug.Assert(array.Length == 1, "array.Length == 1");

    return array[0];
}
```

<span data-ttu-id="08f30-125">Il compilatore riconosce in modo specifico l'attributo in `Debug.Assert`.</span><span class="sxs-lookup"><span data-stu-id="08f30-125">The compiler specially recognizes the attribute on `Debug.Assert`.</span></span> <span data-ttu-id="08f30-126">Passa la stringa associata all'argomento a cui si fa riferimento nel costruttore dell'attributo (in questo caso, `condition`) nel sito di chiamata.</span><span class="sxs-lookup"><span data-stu-id="08f30-126">It passes the string associated with the argument referred to in the attribute's constructor (in this case, `condition`) at the call site.</span></span> <span data-ttu-id="08f30-127">Quando l'asserzione ha esito negativo, verrà visualizzata la condizione false per lo sviluppatore, che saprà quale operazione non è riuscita.</span><span class="sxs-lookup"><span data-stu-id="08f30-127">When either assert fails, the developer will be shown the condition that was false and will know which one failed.</span></span>

<span data-ttu-id="08f30-128">Per la convalida degli argomenti, l'attributo non può essere utilizzato direttamente, ma può essere utilizzato tramite una classe helper:</span><span class="sxs-lookup"><span data-stu-id="08f30-128">For argument validation, the attribute cannot be used directly, but can be made use of through a helper class:</span></span>

```csharp
public static class Verify
{
    public static void Argument(bool condition, string message, [CallerArgumentExpression("condition")] string conditionExpression = null)
    {
        if (!condition) throw new ArgumentException(message: message, paramName: conditionExpression);
    }

    public static void InRange(int argument, int low, int high,
        [CallerArgumentExpression("argument")] string argumentExpression = null,
        [CallerArgumentExpression("low")] string lowExpression = null,
        [CallerArgumentExpression("high")] string highExpression = null)
    {
        if (argument < low)
        {
            throw new ArgumentOutOfRangeException(paramName: argumentExpression,
                message: $"{argumentExpression} ({argument}) cannot be less than {lowExpression} ({low}).");
        }

        if (argument > high)
        {
            throw new ArgumentOutOfRangeException(paramName: argumentExpression,
                message: $"{argumentExpression} ({argument}) cannot be greater than {highExpression} ({high}).");
        }
    }

    public static void NotNull<T>(T argument, [CallerArgumentExpression("argument")] string argumentExpression = null)
        where T : class
    {
        if (argument == null) throw new ArgumentNullException(paramName: argumentExpression);
    }
}

T Single<T>(this T[] array)
{
    Verify.NotNull(array); // paramName: "array"
    Verify.Argument(array.Length == 1, "Array must contain a single element."); // paramName: "array.Length == 1"

    return array[0];
}

T ElementAt(this T[] array, int index)
{
    Verify.NotNull(array); // paramName: "array"
    // paramName: "index"
    // message: "index (-1) cannot be less than 0 (0).", or
    //          "index (6) cannot be greater than array.Length - 1 (5)."
    Verify.InRange(index, 0, array.Length - 1);

    return array[index];
}
```

<span data-ttu-id="08f30-129">Una proposta per aggiungere tale classe helper al Framework è in corso in https://github.com/dotnet/corefx/issues/17068.</span><span class="sxs-lookup"><span data-stu-id="08f30-129">A proposal to add such a helper class to the framework is underway at https://github.com/dotnet/corefx/issues/17068.</span></span> <span data-ttu-id="08f30-130">Se questa funzionalità del linguaggio è stata implementata, è possibile aggiornare la proposta per sfruttare i vantaggi di questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="08f30-130">If this language feature was implemented, the proposal could be updated to take advantage of this feature.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="08f30-131">Metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="08f30-131">Extension methods</span></span>

<span data-ttu-id="08f30-132">È possibile fare riferimento al parametro `this` in un metodo di estensione da `CallerArgumentExpression`.</span><span class="sxs-lookup"><span data-stu-id="08f30-132">The `this` parameter in an extension method may be referenced by `CallerArgumentExpression`.</span></span> <span data-ttu-id="08f30-133">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="08f30-133">For example:</span></span>

```csharp
public static void ShouldBe<T>(this T @this, T expected, [CallerArgumentExpression("this")] string thisExpression = null) {}

contestant.Points.ShouldBe(1337); // thisExpression: "contestant.Points"
```

<span data-ttu-id="08f30-134">`thisExpression` riceverà l'espressione corrispondente all'oggetto prima del punto.</span><span class="sxs-lookup"><span data-stu-id="08f30-134">`thisExpression` will receive the expression corresponding to the object before the dot.</span></span> <span data-ttu-id="08f30-135">Se viene chiamato con la sintassi del metodo statico, ad esempio `Ext.ShouldBe(contestant.Points, 1337)`, si comporterà come se il primo parametro non venisse contrassegnato come `this`.</span><span class="sxs-lookup"><span data-stu-id="08f30-135">If it's called with static method syntax, e.g. `Ext.ShouldBe(contestant.Points, 1337)`, it will behave as if first parameter wasn't marked `this`.</span></span>

<span data-ttu-id="08f30-136">Deve essere sempre presente un'espressione corrispondente al parametro `this`.</span><span class="sxs-lookup"><span data-stu-id="08f30-136">There should always be an expression corresponding to the `this` parameter.</span></span> <span data-ttu-id="08f30-137">Anche se un'istanza di una classe chiama un metodo di estensione su se stesso, ad esempio `this.Single()` dall'interno di un tipo di raccolta, il `this` è richiesto dal compilatore in modo che `"this"` venga superato.</span><span class="sxs-lookup"><span data-stu-id="08f30-137">Even if an instance of a class calls an extension method on itself, e.g. `this.Single()` from inside a collection type, the `this` is mandated by the compiler so `"this"` will get passed.</span></span> <span data-ttu-id="08f30-138">Se questa regola viene modificata in futuro, è possibile provare a passare `null` o una stringa vuota.</span><span class="sxs-lookup"><span data-stu-id="08f30-138">If this rule is changed in the future, we can consider passing `null` or the empty string.</span></span>

### <a name="extra-details"></a><span data-ttu-id="08f30-139">Dettagli aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="08f30-139">Extra details</span></span>

- <span data-ttu-id="08f30-140">Analogamente agli altri attributi `Caller*`, ad esempio `CallerMemberName`, questo attributo può essere utilizzato solo su parametri con valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="08f30-140">Like the other `Caller*` attributes, such as `CallerMemberName`, this attribute may only be used on parameters with default values.</span></span>
- <span data-ttu-id="08f30-141">Sono consentiti più parametri contrassegnati con `CallerArgumentExpression`, come illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="08f30-141">Multiple parameters marked with `CallerArgumentExpression` are permitted, as shown above.</span></span>
- <span data-ttu-id="08f30-142">Lo spazio dei nomi dell'attributo verrà `System.Runtime.CompilerServices`.</span><span class="sxs-lookup"><span data-stu-id="08f30-142">The attribute's namespace will be `System.Runtime.CompilerServices`.</span></span>
- <span data-ttu-id="08f30-143">Se viene specificato `null` o una stringa che non è un nome di parametro (ad esempio `"notAParameterName"`), il compilatore passerà una stringa vuota.</span><span class="sxs-lookup"><span data-stu-id="08f30-143">If `null` or a string that is not a parameter name (e.g. `"notAParameterName"`) is provided, the compiler will pass in an empty string.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="08f30-144">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="08f30-144">Drawbacks</span></span>
[drawbacks]: #drawbacks

- <span data-ttu-id="08f30-145">Gli utenti che sanno come usare i decompilatori saranno in grado di visualizzare parte del codice sorgente nei siti di chiamata per i metodi contrassegnati con questo attributo.</span><span class="sxs-lookup"><span data-stu-id="08f30-145">People who know how to use decompilers will be able to see some of the source code at call sites for methods marked with this attribute.</span></span> <span data-ttu-id="08f30-146">Questo potrebbe essere indesiderato o imprevisto per il software di origine chiusa.</span><span class="sxs-lookup"><span data-stu-id="08f30-146">This may be undesirable/unexpected for closed-source software.</span></span>

- <span data-ttu-id="08f30-147">Sebbene questo non sia un difetto nella funzionalità stessa, è possibile che esista già un'API `Debug.Assert` che accetta solo una `bool`.</span><span class="sxs-lookup"><span data-stu-id="08f30-147">Although this is not a flaw in the feature itself, a source of concern may be that there exists a `Debug.Assert` API today that only takes a `bool`.</span></span> <span data-ttu-id="08f30-148">Anche se l'overload che accetta un messaggio aveva il secondo parametro contrassegnato con questo attributo e reso facoltativo, il compilatore avrebbe comunque scelto il nessun messaggio nella risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="08f30-148">Even if the overload taking a message had its second parameter marked with this attribute and made optional, the compiler would still pick the no-message one in overload resolution.</span></span> <span data-ttu-id="08f30-149">Pertanto, l'overload di nessun messaggio deve essere rimosso per sfruttare questa funzionalità, che potrebbe essere una modifica di rilievo binaria (anche se non di origine).</span><span class="sxs-lookup"><span data-stu-id="08f30-149">Therefore, the no-message overload would have to be removed to take advantage of this feature, which would be a binary (although not source) breaking change.</span></span>

## <a name="alternatives"></a><span data-ttu-id="08f30-150">Alternativi</span><span class="sxs-lookup"><span data-stu-id="08f30-150">Alternatives</span></span>
[alternatives]: #alternatives

- <span data-ttu-id="08f30-151">Se la possibilità di visualizzare il codice sorgente nei siti di chiamata per i metodi che usano questo attributo dimostra che si tratta di un problema, è possibile fare in modo che gli effetti dell'attributo siano espliciti.</span><span class="sxs-lookup"><span data-stu-id="08f30-151">If being able to see source code at call sites for methods that use this attribute proves to be a problem, we can make the attribute's effects opt-in.</span></span> <span data-ttu-id="08f30-152">Gli sviluppatori la consentiranno di usare un attributo `[assembly: EnableCallerArgumentExpression]` a livello di assembly che inserisce `AssemblyInfo.cs`.</span><span class="sxs-lookup"><span data-stu-id="08f30-152">Developers will enable it through an assembly-wide `[assembly: EnableCallerArgumentExpression]` attribute they put in `AssemblyInfo.cs`.</span></span>
  - <span data-ttu-id="08f30-153">Se gli effetti dell'attributo non sono abilitati, la chiamata ai metodi contrassegnati con l'attributo non è un errore, in modo da consentire ai metodi esistenti di utilizzare l'attributo e mantenere la compatibilità con l'origine.</span><span class="sxs-lookup"><span data-stu-id="08f30-153">In the case the attribute's effects are not enabled, calling methods marked with the attribute would not be an error, to allow existing methods to use the attribute and maintain source compatibility.</span></span> <span data-ttu-id="08f30-154">Tuttavia, l'attributo viene ignorato e il metodo viene chiamato con qualsiasi valore predefinito fornito.</span><span class="sxs-lookup"><span data-stu-id="08f30-154">However, the attribute would be ignored and the method would be called with whatever default value was provided.</span></span>

```csharp
// Assembly1

void Foo(string bar); // V1
void Foo(string bar, string barExpression = "not provided"); // V2
void Foo(string bar, [CallerArgumentExpression("bar")] string barExpression = "not provided"); // V3

// Assembly2

Foo(a); // V1: Compiles to Foo(a), V2, V3: Compiles to Foo(a, "not provided")
Foo(a, "provided"); // V2, V3: Compiles to Foo(a, "provided")

// Assembly3

[assembly: EnableCallerArgumentExpression]

Foo(a); // V1: Compiles to Foo(a), V2: Compiles to Foo(a, "not provided"), V3: Compiles to Foo(a, "a")
Foo(a, "provided"); // V2, V3: Compiles to Foo(a, "provided")
```

- <span data-ttu-id="08f30-155">Per evitare che si verifichi un[ drawbacks] [problema di compatibilità binaria] ogni volta che si desidera aggiungere nuove informazioni sul chiamante a `Debug.Assert`, una soluzione alternativa consiste nell'aggiungere uno struct `CallerInfo` al Framework che contiene tutte le informazioni necessarie sul chiamante.</span><span class="sxs-lookup"><span data-stu-id="08f30-155">To prevent the [binary compatibility problem][drawbacks] from occurring every time we want to add new caller info to `Debug.Assert`, an alternative solution would be to add a `CallerInfo` struct to the framework that contains all the necessary information about the caller.</span></span>

```csharp
struct CallerInfo
{
    public string MemberName { get; set; }
    public string TypeName { get; set; }
    public string Namespace { get; set; }
    public string FullTypeName { get; set; }
    public string FilePath { get; set; }
    public int LineNumber { get; set; }
    public int ColumnNumber { get; set; }
    public Type Type { get; set; }
    public MethodBase Method { get; set; }
    public string[] ArgumentExpressions { get; set; }
}

[Flags]
enum CallerInfoOptions
{
    MemberName = 1, TypeName = 2, ...
}

public static class Debug
{
    public static void Assert(bool condition,
        // If a flag is not set here, the corresponding CallerInfo member is not populated by the caller, so it's
        // pay-for-play friendly.
        [CallerInfo(CallerInfoOptions.FilePath | CallerInfoOptions.Method | CallerInfoOptions.ArgumentExpressions)] CallerInfo callerInfo = default(CallerInfo))
    {
        string filePath = callerInfo.FilePath;
        MethodBase method = callerInfo.Method;
        string conditionExpression = callerInfo.ArgumentExpressions[0];
        ...
    }
}

class Bar
{
    void Foo()
    {
        Debug.Assert(false);

        // Translates to:

        var callerInfo = new CallerInfo();
        callerInfo.FilePath = @"C:\Bar.cs";
        callerInfo.Method = MethodBase.GetCurrentMethod();
        callerInfo.ArgumentExpressions = new string[] { "false" };
        Debug.Assert(false, callerInfo);
    }
}
```

<span data-ttu-id="08f30-156">Questa operazione è stata proposta in origine in https://github.com/dotnet/csharplang/issues/87.</span><span class="sxs-lookup"><span data-stu-id="08f30-156">This was originally proposed at https://github.com/dotnet/csharplang/issues/87.</span></span>

<span data-ttu-id="08f30-157">Questo approccio presenta alcuni svantaggi:</span><span class="sxs-lookup"><span data-stu-id="08f30-157">There are a few disadvantages of this approach:</span></span>

- <span data-ttu-id="08f30-158">Nonostante sia facile da pagare, è possibile specificare quali proprietà sono necessarie, ma è comunque possibile danneggiare significativamente le prestazioni allocando una matrice per le espressioni/chiamate `MethodBase.GetCurrentMethod` anche quando l'asserzione passa.</span><span class="sxs-lookup"><span data-stu-id="08f30-158">Despite being pay-for-play friendly by allowing you to specify which properties you need, it could still hurt perf significantly by allocating an array for the expressions/calling `MethodBase.GetCurrentMethod` even when the assert passes.</span></span>

- <span data-ttu-id="08f30-159">Inoltre, anche se il passaggio di un nuovo flag all'attributo `CallerInfo` non è una modifica di rilievo, `Debug.Assert` non sarà garantita la ricezione del nuovo parametro dai siti di chiamata compilati in base a una versione precedente del metodo.</span><span class="sxs-lookup"><span data-stu-id="08f30-159">Additionally, while passing a new flag to the `CallerInfo` attribute won't be a breaking change, `Debug.Assert` won't be guaranteed to actually receive that new parameter from call sites that compiled against an old version of the method.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="08f30-160">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="08f30-160">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="08f30-161">TBD</span><span class="sxs-lookup"><span data-stu-id="08f30-161">TBD</span></span>

## <a name="design-meetings"></a><span data-ttu-id="08f30-162">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="08f30-162">Design meetings</span></span>

<span data-ttu-id="08f30-163">N/D</span><span class="sxs-lookup"><span data-stu-id="08f30-163">N/A</span></span>
