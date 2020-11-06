---
ms.openlocfilehash: 802722e4331a10228eb77676d28338ace5ef07b2
ms.sourcegitcommit: 4c8b0a1c815f6ed5f69e2bdff94da354b2908fed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93406336"
---
<a name="async-task-types-in-c"></a><span data-ttu-id="51319-101">Tipi di attività asincrone in C #</span><span class="sxs-lookup"><span data-stu-id="51319-101">Async Task Types in C#</span></span>
======================
<span data-ttu-id="51319-102">Estendere `async` per supportare i _tipi di attività_ che corrispondono a un modello specifico, oltre ai tipi noti `System.Threading.Tasks.Task` e a `System.Threading.Tasks.Task<T>` .</span><span class="sxs-lookup"><span data-stu-id="51319-102">Extend `async` to support _task types_ that match a specific pattern, in addition to the well known types `System.Threading.Tasks.Task` and `System.Threading.Tasks.Task<T>`.</span></span>

## <a name="task-type"></a><span data-ttu-id="51319-103">Tipo di attività</span><span class="sxs-lookup"><span data-stu-id="51319-103">Task Type</span></span>
<span data-ttu-id="51319-104">Un _tipo di attività_ è `class` o `struct` con un _tipo di generatore_ associato identificato con `System.Runtime.CompilerServices.AsyncMethodBuilderAttribute` .</span><span class="sxs-lookup"><span data-stu-id="51319-104">A _task type_ is a `class` or `struct` with an associated _builder type_ identified with `System.Runtime.CompilerServices.AsyncMethodBuilderAttribute`.</span></span>
<span data-ttu-id="51319-105">Il _tipo di attività_ può essere non generico, per i metodi asincroni che non restituiscono un valore, o generico, per i metodi che restituiscono un valore.</span><span class="sxs-lookup"><span data-stu-id="51319-105">The _task type_ may be non-generic, for async methods that do not return a value, or generic, for methods that return a value.</span></span>

<span data-ttu-id="51319-106">Per supportare `await` , il _tipo di attività_ deve avere un metodo accessibile corrispondente `GetAwaiter()` che restituisce un'istanza di un _tipo awaiter_ (vedere _espressioni awaitable C# 7.7.7.1_ ).</span><span class="sxs-lookup"><span data-stu-id="51319-106">To support `await`, the _task type_ must have a corresponding, accessible `GetAwaiter()` method that returns an instance of an _awaiter type_ (see _C# 7.7.7.1 Awaitable expressions_ ).</span></span>
```cs
[AsyncMethodBuilder(typeof(MyTaskMethodBuilder<>))]
class MyTask<T>
{
    public Awaiter<T> GetAwaiter();
}

class Awaiter<T> : INotifyCompletion
{
    public bool IsCompleted { get; }
    public T GetResult();
    public void OnCompleted(Action completion);
}
```
## <a name="builder-type"></a><span data-ttu-id="51319-107">Tipo di generatore</span><span class="sxs-lookup"><span data-stu-id="51319-107">Builder Type</span></span>
<span data-ttu-id="51319-108">Il _tipo di generatore_ è `class` o `struct` corrisponde al _tipo di attività_ specifico.</span><span class="sxs-lookup"><span data-stu-id="51319-108">The _builder type_ is a `class` or `struct` that corresponds to the specific _task type_.</span></span>
<span data-ttu-id="51319-109">Il _tipo di generatore_ può includere al massimo 1 parametro di tipo e non deve essere annidato in un tipo generico.</span><span class="sxs-lookup"><span data-stu-id="51319-109">The _builder type_ can have at most 1 type parameter and must not be nested in a generic type.</span></span>
<span data-ttu-id="51319-110">Il _tipo di generatore_ presenta i `public` metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="51319-110">The _builder type_ has the following `public` methods.</span></span>
<span data-ttu-id="51319-111">Per i tipi di _Generatore_ non generici `SetResult()` non dispone di parametri.</span><span class="sxs-lookup"><span data-stu-id="51319-111">For non-generic _builder types_ , `SetResult()` has no parameters.</span></span>
```cs
class MyTaskMethodBuilder<T>
{
    public static MyTaskMethodBuilder<T> Create();

    public void Start<TStateMachine>(ref TStateMachine stateMachine)
        where TStateMachine : IAsyncStateMachine;

    public void SetStateMachine(IAsyncStateMachine stateMachine);
    public void SetException(Exception exception);
    public void SetResult(T result);

    public void AwaitOnCompleted<TAwaiter, TStateMachine>(
        ref TAwaiter awaiter, ref TStateMachine stateMachine)
        where TAwaiter : INotifyCompletion
        where TStateMachine : IAsyncStateMachine;
    public void AwaitUnsafeOnCompleted<TAwaiter, TStateMachine>(
        ref TAwaiter awaiter, ref TStateMachine stateMachine)
        where TAwaiter : ICriticalNotifyCompletion
        where TStateMachine : IAsyncStateMachine;

    public MyTask<T> Task { get; }
}
```
## <a name="execution"></a><span data-ttu-id="51319-112">Esecuzione</span><span class="sxs-lookup"><span data-stu-id="51319-112">Execution</span></span>
<span data-ttu-id="51319-113">I tipi precedenti vengono usati dal compilatore per generare il codice per la macchina a stati di un `async` metodo.</span><span class="sxs-lookup"><span data-stu-id="51319-113">The types above are used by the compiler to generate the code for the state machine of an `async` method.</span></span>
<span data-ttu-id="51319-114">Il codice generato è equivalente al codice generato per i metodi asincroni che restituiscono `Task` , `Task<T>` o `void` .</span><span class="sxs-lookup"><span data-stu-id="51319-114">(The generated code is equivalent to the code generated for async methods that return `Task`, `Task<T>`, or `void`.</span></span>
<span data-ttu-id="51319-115">La differenza è che, per i tipi noti, i _tipi di generatore_ sono noti anche per il compilatore.</span><span class="sxs-lookup"><span data-stu-id="51319-115">The difference is, for those well known types, the _builder types_ are also known to the compiler.)</span></span>

<span data-ttu-id="51319-116">`Builder.Create()` viene richiamato per creare un'istanza del _tipo di generatore_.</span><span class="sxs-lookup"><span data-stu-id="51319-116">`Builder.Create()` is invoked to create an instance of the _builder type_.</span></span>

<span data-ttu-id="51319-117">Se la macchina a stati viene implementata come `struct` , `builder.SetStateMachine(stateMachine)` viene chiamato con un'istanza boxed della macchina a Stati che il generatore può memorizzare nella cache, se necessario.</span><span class="sxs-lookup"><span data-stu-id="51319-117">If the state machine is implemented as a `struct`, then `builder.SetStateMachine(stateMachine)` is called with a boxed instance of the state machine that the builder can cache if necessary.</span></span>

<span data-ttu-id="51319-118">`builder.Start(ref stateMachine)` viene richiamato per associare il generatore all'istanza della macchina a stati generata dal compilatore.</span><span class="sxs-lookup"><span data-stu-id="51319-118">`builder.Start(ref stateMachine)` is invoked to associate the builder with compiler-generated state machine instance.</span></span>
<span data-ttu-id="51319-119">Il generatore deve chiamare `stateMachine.MoveNext()` in `Start()` o dopo che `Start()` ha restituito per far avanzare la macchina a Stati.</span><span class="sxs-lookup"><span data-stu-id="51319-119">The builder must call `stateMachine.MoveNext()` either in `Start()` or after `Start()` has returned to advance the state machine.</span></span>
<span data-ttu-id="51319-120">Dopo `Start()` la restituzione, il `async` metodo chiama l' `builder.Task` attività per restituire dal metodo asincrono.</span><span class="sxs-lookup"><span data-stu-id="51319-120">After `Start()` returns, the `async` method calls `builder.Task` for the task to return from the async method.</span></span>

<span data-ttu-id="51319-121">Ogni chiamata a `stateMachine.MoveNext()` comporterà l'avanzamento della macchina a Stati.</span><span class="sxs-lookup"><span data-stu-id="51319-121">Each call to `stateMachine.MoveNext()` will advance the state machine.</span></span>
<span data-ttu-id="51319-122">Se la macchina a stati viene completata correttamente, `builder.SetResult()` viene chiamato il metodo con il valore restituito del metodo, se presente.</span><span class="sxs-lookup"><span data-stu-id="51319-122">If the state machine completes successfully, `builder.SetResult()` is called, with  the method return value if any.</span></span>
<span data-ttu-id="51319-123">Se viene generata un'eccezione nella macchina a Stati, `builder.SetException(exception)` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="51319-123">If an exception is thrown in the state machine, `builder.SetException(exception)` is called.</span></span>

<span data-ttu-id="51319-124">Se la macchina a stati raggiunge un' `await expr` espressione, `expr.GetAwaiter()` viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="51319-124">If the state machine reaches an `await expr` expression, `expr.GetAwaiter()` is invoked.</span></span>
<span data-ttu-id="51319-125">Se il awaiter implementa `ICriticalNotifyCompletion` e `IsCompleted` è false, la macchina a stati richiama `builder.AwaitUnsafeOnCompleted(ref awaiter, ref stateMachine)` .</span><span class="sxs-lookup"><span data-stu-id="51319-125">If the awaiter implements `ICriticalNotifyCompletion` and `IsCompleted` is false, the state machine invokes `builder.AwaitUnsafeOnCompleted(ref awaiter, ref stateMachine)`.</span></span>
<span data-ttu-id="51319-126">`AwaitUnsafeOnCompleted()` deve chiamare `awaiter.OnCompleted(action)` con un'azione che chiama `stateMachine.MoveNext()` quando l'elemento awaiter viene completato.</span><span class="sxs-lookup"><span data-stu-id="51319-126">`AwaitUnsafeOnCompleted()` should call `awaiter.OnCompleted(action)` with an action that calls `stateMachine.MoveNext()` when the awaiter completes.</span></span> <span data-ttu-id="51319-127">Analogamente per `INotifyCompletion` e `builder.AwaitOnCompleted()` .</span><span class="sxs-lookup"><span data-stu-id="51319-127">Similarly for `INotifyCompletion` and `builder.AwaitOnCompleted()`.</span></span>

## <a name="overload-resolution"></a><span data-ttu-id="51319-128">Overload Resolution</span><span class="sxs-lookup"><span data-stu-id="51319-128">Overload Resolution</span></span>
<span data-ttu-id="51319-129">La risoluzione dell'overload viene estesa per riconoscere i _tipi di attività_ oltre a `Task` e `Task<T>` .</span><span class="sxs-lookup"><span data-stu-id="51319-129">Overload resolution is extended to recognize _task types_ in addition to `Task` and `Task<T>`.</span></span>

<span data-ttu-id="51319-130">Un' `async` espressione lambda senza valore restituito è una corrispondenza esatta per un parametro del candidato di overload di un _tipo di attività_ non generica e un' `async` espressione lambda con tipo restituito `T` è una corrispondenza esatta per un parametro di overload candidate di _tipo di attività_ generico.</span><span class="sxs-lookup"><span data-stu-id="51319-130">An `async` lambda with no return value is an exact match for an overload candidate parameter of non-generic _task type_ , and an `async` lambda with return type `T` is an exact match for an overload candidate parameter of generic _task type_.</span></span> 

<span data-ttu-id="51319-131">In caso contrario, se un' `async` espressione lambda non è una corrispondenza esatta per uno dei due parametri candidati dei _tipi di attività_ , o una corrispondenza esatta per entrambi, ed esiste una conversione implicita da un tipo candidato all'altro, l'oggetto del candidato prevale.</span><span class="sxs-lookup"><span data-stu-id="51319-131">Otherwise if an `async` lambda is not an exact match for either of two candidate parameters of _task types_ , or an exact match for both, and there is an implicit conversion from one candidate type to the other, the from candidate wins.</span></span> <span data-ttu-id="51319-132">In caso contrario, valutare in modo ricorsivo i tipi `A` e `B` all'interno di `Task1<A>` e `Task2<B>` per una migliore corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="51319-132">Otherwise recursively evaluate the types `A` and `B` within `Task1<A>` and `Task2<B>` for better match.</span></span>

<span data-ttu-id="51319-133">In caso contrario `async` , se un'espressione lambda non è una corrispondenza esatta per uno dei due parametri candidati dei _tipi di attività_ , ma un candidato è un tipo più specializzato rispetto all'altro, il candidato più specializzato prevale.</span><span class="sxs-lookup"><span data-stu-id="51319-133">Otherwise if an `async` lambda is not an exact match for either of two candidate parameters of _task types_ , but one candidate is a more specialized type than the other, the more specialized candidate wins.</span></span>
