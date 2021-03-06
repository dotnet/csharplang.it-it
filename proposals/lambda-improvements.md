---
ms.openlocfilehash: 138d66c669e5db527ffe24d2bbb004b5fd2f6db9
ms.sourcegitcommit: 00b0f04deb7ad7b7a1f16ed8e9ec40620d87e774
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2021
ms.locfileid: "102250407"
---
# <a name="lambda-improvements"></a><span data-ttu-id="2381d-101">Miglioramenti lambda</span><span class="sxs-lookup"><span data-stu-id="2381d-101">Lambda improvements</span></span>

## <a name="summary"></a><span data-ttu-id="2381d-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="2381d-102">Summary</span></span>
<span data-ttu-id="2381d-103">Modifiche proposte:</span><span class="sxs-lookup"><span data-stu-id="2381d-103">Proposed changes:</span></span>
1. <span data-ttu-id="2381d-104">Consenti le espressioni lambda con attributi</span><span class="sxs-lookup"><span data-stu-id="2381d-104">Allow lambdas with attributes</span></span>
2. <span data-ttu-id="2381d-105">Consenti le espressioni lambda con tipo restituito esplicito</span><span class="sxs-lookup"><span data-stu-id="2381d-105">Allow lambdas with explicit return type</span></span>
3. <span data-ttu-id="2381d-106">Dedurre un tipo delegato naturale per le espressioni lambda e i gruppi di metodi</span><span class="sxs-lookup"><span data-stu-id="2381d-106">Infer a natural delegate type for lambdas and method groups</span></span>

## <a name="motivation"></a><span data-ttu-id="2381d-107">Motivazione</span><span class="sxs-lookup"><span data-stu-id="2381d-107">Motivation</span></span>
<span data-ttu-id="2381d-108">Il supporto per gli attributi sulle espressioni lambda fornirebbe parità con i metodi e le funzioni locali.</span><span class="sxs-lookup"><span data-stu-id="2381d-108">Support for attributes on lambdas would provide parity with methods and local functions.</span></span>

<span data-ttu-id="2381d-109">Il supporto per i tipi restituiti espliciti fornirebbe la simmetria con i parametri lambda in cui è possibile specificare tipi espliciti.</span><span class="sxs-lookup"><span data-stu-id="2381d-109">Support for explicit return types would provide symmetry with lambda parameters where explicit types can be specified.</span></span>
<span data-ttu-id="2381d-110">Consentire i tipi restituiti espliciti fornisce anche il controllo sulle prestazioni del compilatore nelle espressioni lambda nidificate in cui la risoluzione dell'overload deve associare il corpo dell'espressione lambda al momento per determinare la firma.</span><span class="sxs-lookup"><span data-stu-id="2381d-110">Allowing explicit return types would also provide control over compiler performance in nested lambdas where overload resolution must bind the lambda body currently to determine the signature.</span></span>

<span data-ttu-id="2381d-111">Un tipo naturale per le espressioni lambda e i gruppi di metodi consentirà più scenari in cui è possibile usare le espressioni lambda e i gruppi di metodi senza un tipo delegato esplicito, anche come inizializzatori nelle `var` dichiarazioni.</span><span class="sxs-lookup"><span data-stu-id="2381d-111">A natural type for lambda expressions and method groups will allow more scenarios where lambdas and method groups may be used without an explicit delegate type, including as initializers in `var` declarations.</span></span>

<span data-ttu-id="2381d-112">La richiesta di tipi delegati espliciti per le espressioni lambda e i gruppi di metodi è un punto di attrito per i clienti ed è diventato un ostacolo per lo stato di avanzamento in ASP.NET con lavoro recente in [MapAction](https://github.com/dotnet/aspnetcore/pull/29878).</span><span class="sxs-lookup"><span data-stu-id="2381d-112">Requiring explicit delegate types for lambdas and method groups has been a friction point for customers, and has become an impediment to progress in ASP.NET with recent work on [MapAction](https://github.com/dotnet/aspnetcore/pull/29878).</span></span>

<span data-ttu-id="2381d-113">[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) senza modifiche proposte ( `MapAction()` accetta un `System.Delegate` argomento):</span><span class="sxs-lookup"><span data-stu-id="2381d-113">[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) without proposed changes (`MapAction()` takes a `System.Delegate` argument):</span></span>
```csharp
[HttpGet("/")] Todo GetTodo() => new(Id: 0, Name: "Name");
app.MapAction((Func<Todo>)GetTodo);

[HttpPost("/")] Todo PostTodo([FromBody] Todo todo) => todo);
app.MapAction((Func<Todo, Todo>)PostTodo);
```

<span data-ttu-id="2381d-114">[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) con tipi naturali per i gruppi di metodi:</span><span class="sxs-lookup"><span data-stu-id="2381d-114">[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) with natural types for method groups:</span></span>
```csharp
[HttpGet("/")] Todo GetTodo() => new(Id: 0, Name: "Name");
app.MapAction(GetTodo);

[HttpPost("/")] Todo PostTodo([FromBody] Todo todo) => todo);
app.MapAction(PostTodo);
```

<span data-ttu-id="2381d-115">[MapAction ASP.NET](https://github.com/dotnet/aspnetcore/pull/29878) con attributi e tipi naturali per le espressioni lambda:</span><span class="sxs-lookup"><span data-stu-id="2381d-115">[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) with attributes and natural types for lambda expressions:</span></span>
```csharp
app.MapAction([HttpGet("/")] () => new Todo(Id: 0, Name: "Name"));
app.MapAction([HttpPost("/")] ([FromBody] Todo todo) => todo);
```

## <a name="attributes"></a><span data-ttu-id="2381d-116">Attributi</span><span class="sxs-lookup"><span data-stu-id="2381d-116">Attributes</span></span>
<span data-ttu-id="2381d-117">Gli attributi possono essere aggiunti alle espressioni lambda.</span><span class="sxs-lookup"><span data-stu-id="2381d-117">Attributes may be added to lambda expressions.</span></span>
```csharp
f = [MyAttribute] x => x;          // [MyAttribute]lambda
f = [MyAttribute] (int x) => x;    // [MyAttribute]lambda
f = [MyAttribute] static x => x;   // [MyAttribute]lambda
f = [return: MyAttribute] () => 1; // [return: MyAttribute]lambda
```

<span data-ttu-id="2381d-118">_Se gli attributi vengono aggiunti all'intera espressione, le parentesi devono essere necessarie per l'elenco di parametri? (Non `[MyAttribute] x => x` è consentito? In caso affermativo, cosa accade `[MyAttribute] static x => x` ?)_</span><span class="sxs-lookup"><span data-stu-id="2381d-118">_Should parentheses be required for the parameter list if attributes are added to the entire expression? (Should `[MyAttribute] x => x` be disallowed? If so, what about `[MyAttribute] static x => x`?)_</span></span>

<span data-ttu-id="2381d-119">Gli attributi possono essere aggiunti ai parametri lambda dichiarati con tipi espliciti.</span><span class="sxs-lookup"><span data-stu-id="2381d-119">Attributes may be added to lambda parameters that are declared with explicit types.</span></span>
```csharp
f = ([MyAttribute] x) => x;      // syntax error
f = ([MyAttribute] int x) => x;  // [MyAttribute]x
```

<span data-ttu-id="2381d-120">Gli attributi non sono supportati per i metodi anonimi dichiarati con la `delegate { }` sintassi.</span><span class="sxs-lookup"><span data-stu-id="2381d-120">Attributes are not supported for anonymous methods declared with `delegate { }` syntax.</span></span>
```csharp
f = [MyAttribute] delegate { return 1; };         // syntax error
f = delegate ([MyAttribute] int x) { return x; }; // syntax error
```

<span data-ttu-id="2381d-121">Gli attributi nell'espressione lambda o nei parametri lambda verranno inviati ai metadati nel metodo che esegue il mapping all'espressione lambda.</span><span class="sxs-lookup"><span data-stu-id="2381d-121">Attributes on the lambda expression or lambda parameters will be emitted to metadata on the method that maps to the lambda.</span></span>

<span data-ttu-id="2381d-122">In generale, i clienti non devono dipendere dal modo in cui le espressioni lambda e le funzioni locali vengono mappate dall'origine ai metadati.</span><span class="sxs-lookup"><span data-stu-id="2381d-122">In general, customers should not depend on how lambda expressions and local functions map from source to metadata.</span></span> <span data-ttu-id="2381d-123">Il modo in cui vengono emesse le espressioni lambda e le funzioni locali può essere modificato tra le versioni del compilatore.</span><span class="sxs-lookup"><span data-stu-id="2381d-123">How lambdas and local functions are emitted can, and has, changed between compiler versions.</span></span>

<span data-ttu-id="2381d-124">Le modifiche proposte sono destinate allo `Delegate` scenario guidato.</span><span class="sxs-lookup"><span data-stu-id="2381d-124">The changes proposed here are targeted at the `Delegate` driven scenario.</span></span>
<span data-ttu-id="2381d-125">Deve essere valido per esaminare l'oggetto `MethodInfo` associato a un' `Delegate` istanza di per determinare la firma dell'espressione lambda o della funzione locale, inclusi eventuali attributi espliciti e metadati aggiuntivi emessi dal compilatore, ad esempio i parametri predefiniti.</span><span class="sxs-lookup"><span data-stu-id="2381d-125">It should be valid to inspect the `MethodInfo` associated with a `Delegate` instance to determine the signature of the lambda expression or local function including any explicit attributes and additional metadata emitted by the compiler such as default parameters.</span></span>
<span data-ttu-id="2381d-126">Questo consente ai team come ASP.NET di rendere disponibili gli stessi comportamenti per le espressioni lambda e le funzioni locali come metodi normali.</span><span class="sxs-lookup"><span data-stu-id="2381d-126">This allows teams such as ASP.NET to make available the same behaviors for lambdas and local functions as ordinary methods.</span></span>

## <a name="explicit-return-type"></a><span data-ttu-id="2381d-127">Tipo restituito esplicito</span><span class="sxs-lookup"><span data-stu-id="2381d-127">Explicit return type</span></span>
<span data-ttu-id="2381d-128">È possibile specificare un tipo restituito esplicito dopo l'elenco di parametri.</span><span class="sxs-lookup"><span data-stu-id="2381d-128">An explicit return type may be specified after the parameter list.</span></span>
```csharp
f = () : T => default;              // () : T
f = x : short => 1;                 // <unknown> : short
f = (ref int x) : ref int => ref x; // ref int : ref int
f = static _ : void => { };         // <unknown> : void
```

<span data-ttu-id="2381d-129">I tipi restituiti espliciti non sono supportati per i metodi anonimi dichiarati con la `delegate { }` sintassi.</span><span class="sxs-lookup"><span data-stu-id="2381d-129">Explicit return types are not supported for anonymous methods declared with `delegate { }` syntax.</span></span>
```csharp
f = delegate : int { return 1; };         // syntax error
f = delegate (int x) : int { return x; }; // syntax error
```

## <a name="natural-delegate-type"></a><span data-ttu-id="2381d-130">Tipo delegato naturale</span><span class="sxs-lookup"><span data-stu-id="2381d-130">Natural delegate type</span></span>
<span data-ttu-id="2381d-131">Un'espressione lambda ha un tipo naturale se i tipi di parametri sono espliciti e il tipo restituito è esplicito o se è presente un tipo comune tra i tipi naturali di tutte le `return` espressioni nel corpo.</span><span class="sxs-lookup"><span data-stu-id="2381d-131">A lambda expression has a natural type if the parameters types are explicit and either the return type is explicit or there is a common type from the natural types of all `return` expressions in the body.</span></span>

<span data-ttu-id="2381d-132">Il tipo naturale è un tipo delegato in cui i tipi di parametro sono i tipi di parametro lambda espliciti e il tipo restituito `R` è:</span><span class="sxs-lookup"><span data-stu-id="2381d-132">The natural type is a delegate type where the parameter types are the explicit lambda parameter types and the return type `R` is:</span></span>
- <span data-ttu-id="2381d-133">Se il tipo restituito lambda è esplicito, viene usato il tipo.</span><span class="sxs-lookup"><span data-stu-id="2381d-133">if the lambda return type is explicit, that type is used;</span></span>
- <span data-ttu-id="2381d-134">Se l'espressione lambda non ha espressioni restituite, il tipo restituito è `void` o `System.Threading.Tasks.Task` se `async` ;</span><span class="sxs-lookup"><span data-stu-id="2381d-134">if the lambda has no return expressions, the return type is `void` or `System.Threading.Tasks.Task` if `async`;</span></span>
- <span data-ttu-id="2381d-135">Se il tipo comune del tipo naturale di tutte le `return` espressioni nel corpo è il tipo `R0` , il tipo restituito è `R0` o `System.Threading.Tasks.Task<R0>` se `async` .</span><span class="sxs-lookup"><span data-stu-id="2381d-135">if the common type from the natural type of all `return` expressions in the body is the type `R0`, the return type is `R0` or `System.Threading.Tasks.Task<R0>` if `async`.</span></span>

<span data-ttu-id="2381d-136">Un gruppo di metodi ha un tipo naturale se il gruppo di metodi contiene un solo metodo.</span><span class="sxs-lookup"><span data-stu-id="2381d-136">A method group has a natural type if the method group contains a single method.</span></span>

<span data-ttu-id="2381d-137">Un gruppo di metodi può fare riferimento ai metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="2381d-137">A method group might refer to extension methods.</span></span> <span data-ttu-id="2381d-138">In genere, la risoluzione del gruppo di metodi cerca i metodi di estensione in modo differito, solo scorrendo gli ambiti dello spazio dei nomi successivi fino a trovare i metodi di estensione che corrispondono al tipo</span><span class="sxs-lookup"><span data-stu-id="2381d-138">Normally method group resolution searches for extension methods lazily, only iterating through successive namespace scopes until extension methods are found that match the target type.</span></span> <span data-ttu-id="2381d-139">Per determinare il tipo naturale, tuttavia, è necessaria la ricerca in tutti gli ambiti dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="2381d-139">But to determine the natural type will require searching all namespace scopes.</span></span> <span data-ttu-id="2381d-140">_Per ridurre al minimo il binding non necessario, il tipo naturale dovrebbe essere calcolato solo nei casi in cui non esiste un tipo di destinazione, ovvero solo per calcolare il tipo naturale nei casi in cui è necessario._</span><span class="sxs-lookup"><span data-stu-id="2381d-140">_To minimize unnecessary binding, perhaps natural type should be calculated only in cases where there is no target type - that is, only calculate the natural type in cases where it is needed._</span></span>

<span data-ttu-id="2381d-141">Il tipo delegato per l'espressione lambda o il gruppo di metodi e i tipi di parametro `P1, ..., Pn` e il tipo restituito `R` è:</span><span class="sxs-lookup"><span data-stu-id="2381d-141">The delegate type for the lambda or method group and parameter types `P1, ..., Pn` and return type `R` is:</span></span>
- <span data-ttu-id="2381d-142">Se un parametro o un valore restituito non è per valore o se sono presenti più di 16 parametri oppure se uno dei tipi di parametro o restituisce non sono argomenti di tipo validi (ad indicare `(int* p) => { }` ), il delegato è un `internal` tipo delegato anonimo sintetizzato con firma che corrisponde al gruppo di metodi o lambda e con i nomi di parametro `arg1, ..., argn` o `arg` se un singolo parametro;</span><span class="sxs-lookup"><span data-stu-id="2381d-142">if any parameter or return value is not by value, or there are more than 16 parameters, or any of the parameter types or return are not valid type arguments (say, `(int* p) => { }`), then the delegate is a synthesized `internal` anonymous delegate type with signature that matches the lambda or method group, and with parameter names `arg1, ..., argn` or `arg` if a single parameter;</span></span>
- <span data-ttu-id="2381d-143">Se `R` è `void` , il tipo delegato è `System.Action<P1, ..., Pn>` ;</span><span class="sxs-lookup"><span data-stu-id="2381d-143">if `R` is `void`, then the delegate type is `System.Action<P1, ..., Pn>`;</span></span>
- <span data-ttu-id="2381d-144">in caso contrario, il tipo delegato è `System.Func<P1, ..., Pn, R>` .</span><span class="sxs-lookup"><span data-stu-id="2381d-144">otherwise the delegate type is `System.Func<P1, ..., Pn, R>`.</span></span>

<span data-ttu-id="2381d-145">`modopt()` o `modreq()` nella firma del gruppo di metodi vengono ignorati nel tipo delegato corrispondente.</span><span class="sxs-lookup"><span data-stu-id="2381d-145">`modopt()` or `modreq()` in the method group signature are ignored in the corresponding delegate type.</span></span>

<span data-ttu-id="2381d-146">Se due espressioni lambda o gruppi di metodi nella stessa compilazione richiedono tipi delegati sintetizzati con gli stessi tipi di parametro e modificatori e lo stesso tipo restituito e gli stessi modificatori, il compilatore utilizzerà lo stesso tipo delegato sintetizzato.</span><span class="sxs-lookup"><span data-stu-id="2381d-146">If two lambda expressions or method groups in the same compilation require synthesized delegate types with the same parameter types and modifiers and the same return type and modifiers, the compiler will use the same synthesized delegate type.</span></span>

<span data-ttu-id="2381d-147">Le espressioni lambda o i gruppi di metodi con tipi naturali possono essere usati come inizializzatori nelle `var` dichiarazioni.</span><span class="sxs-lookup"><span data-stu-id="2381d-147">Lambdas or method groups with natural types can be used as initializers in `var` declarations.</span></span>

```csharp
var f1 = () => default;        // error: no natural type
var f2 = x => { };             // error: no natural type
var f3 = x => x;               // error: no natural type
var f4 = () => 1;              // System.Func<int>
var f5 = () : string => null;  // System.Func<string>
```

```csharp
static void F1() { }
static void F1<T>(this T t) { }
static void F2(this string s) { }

var f6 = F1;    // error: multiple methods
var f7 = "".F1; // System.Action
var f8 = F2;    // System.Action<string> 
```

<span data-ttu-id="2381d-148">I tipi delegati sintetizzati sono implicitamente co-e controvariante.</span><span class="sxs-lookup"><span data-stu-id="2381d-148">The synthesized delegate types are implicitly co- and contra-variant.</span></span>
```csharp
var fA = (IEnumerable<string> e, ref int i) => { }; // void DA$(IEnumerable<string>, ref int);
fA = (IEnumerable<object> e, ref int i) => { };     // ok

var fB = (IEnumerable<object> e, ref int i) => { }; // void DB$(IEnumerable<object>, ref int);
fB = (IEnumerable<string> e, ref int i) => { };     // error: parameter type mismatch
```

### <a name="implicit-conversion-to-systemdelegate"></a><span data-ttu-id="2381d-149">Conversione implicita in `System.Delegate`</span><span class="sxs-lookup"><span data-stu-id="2381d-149">Implicit conversion to `System.Delegate`</span></span>
<span data-ttu-id="2381d-150">Una conseguenza dell'inferenza di un tipo naturale è che le espressioni lambda e i gruppi di metodi con tipo naturale sono convertibili in modo implicito in `System.Delegate` .</span><span class="sxs-lookup"><span data-stu-id="2381d-150">A consequence of inferring a natural type is that lambda expressions and method groups with natural type are implicitly convertible to `System.Delegate`.</span></span>
```csharp
static void Invoke(Func<string> f) { }
static void Invoke(Delegate d) { }

static string GetString() => "";
static int GetInt() => 0;

Invoke(() => "");  // Invoke(Func<string>)
Invoke(() => 0);   // Invoke(Delegate) [new]

Invoke(GetString); // Invoke(Func<string>)
Invoke(GetInt);    // Invoke(Delegate) [new]
```

<span data-ttu-id="2381d-151">Se non è possibile dedurre un tipo naturale, non esiste alcuna conversione implicita in `System.Delegate` .</span><span class="sxs-lookup"><span data-stu-id="2381d-151">If a natural type cannot be inferred, there is no implicit conversion to `System.Delegate`.</span></span>
```csharp
static void Invoke(Delegate d) { }

Invoke(Console.WriteLine); // error: cannot to 'Delegate'; multiple candidate methods
Invoke(x => x);            // error: cannot to 'Delegate'; no natural type for 'x'
```

<span data-ttu-id="2381d-152">Per evitare una modifica di rilievo, verrà aggiornata la risoluzione dell'overload per preferire le espressioni e i delegati fortemente tipizzati `System.Delegate` .</span><span class="sxs-lookup"><span data-stu-id="2381d-152">To avoid a breaking change, overload resolution will be updated to prefer strongly-typed delegates and expressions over `System.Delegate`.</span></span>
<span data-ttu-id="2381d-153">_Nell'esempio seguente viene illustrata la regola di suddivisione delle espressioni lambda. Esiste un esempio equivalente per i gruppi di metodi?_</span><span class="sxs-lookup"><span data-stu-id="2381d-153">_The example below demonstrates the tie-breaking rule for lambdas. Is there an equivalent example for method groups?_</span></span>
```csharp
static void Execute(Expression<Func<string>> e) { }
static void Execute(Delegate d) { }

static string GetString() => "";
static int GetInt() => 0;

Execute(() => "");  // Execute(Expression<Func<string>>) [tie-breaker]
Execute(() => 0);   // Execute(Delegate) [new]

Execute(GetString); // Execute(Delegate) [new]
Execute(GetInt);    // Execute(Delegate) [new]
```

## <a name="syntax"></a><span data-ttu-id="2381d-154">Sintassi</span><span class="sxs-lookup"><span data-stu-id="2381d-154">Syntax</span></span>

```antlr
lambda_expression
  : attribute_list* modifier* lambda_parameters (':' type)? '=>' (block | body)
  ;

lambda_parameters
  : lambda_parameter
  | '(' (lambda_parameter (',' lambda_parameter)*)? ')'
  ;

lambda_parameter
  : identifier
  | (attribute_list* modifier* type)? identifier equals_value_clause?
  ;
```

<span data-ttu-id="2381d-155">_La `: type` sintassi del tipo restituito introduce ambiguità con `?:` che non possono essere risolte facilmente?_</span><span class="sxs-lookup"><span data-stu-id="2381d-155">_Does the `: type` return type syntax introduce ambiguities with `?:` that cannot be resolved easily?_</span></span>

<span data-ttu-id="2381d-156">_È necessario consentire gli attributi sui parametri senza tipi espliciti, ad esempio `([MyAttribute] x) => { }` ? Non sono consentiti modificatori sui parametri senza tipi espliciti, ad esempio `(ref x) => { }` ._</span><span class="sxs-lookup"><span data-stu-id="2381d-156">_Should we allow attributes on parameters without explicit types, such as `([MyAttribute] x) => { }`? (We don't allow modifiers on parameters without explicit types, such as `(ref x) => { }`.)_</span></span>

## <a name="design-meetings"></a><span data-ttu-id="2381d-157">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="2381d-157">Design meetings</span></span>

- https://github.com/dotnet/csharplang/blob/master/meetings/2021/LDM-2021-03-03.md
