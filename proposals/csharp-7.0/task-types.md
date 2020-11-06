---
ms.openlocfilehash: 802722e4331a10228eb77676d28338ace5ef07b2
ms.sourcegitcommit: 4c8b0a1c815f6ed5f69e2bdff94da354b2908fed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93406336"
---
<a name="async-task-types-in-c"></a>Tipi di attività asincrone in C #
======================
Estendere `async` per supportare i _tipi di attività_ che corrispondono a un modello specifico, oltre ai tipi noti `System.Threading.Tasks.Task` e a `System.Threading.Tasks.Task<T>` .

## <a name="task-type"></a>Tipo di attività
Un _tipo di attività_ è `class` o `struct` con un _tipo di generatore_ associato identificato con `System.Runtime.CompilerServices.AsyncMethodBuilderAttribute` .
Il _tipo di attività_ può essere non generico, per i metodi asincroni che non restituiscono un valore, o generico, per i metodi che restituiscono un valore.

Per supportare `await` , il _tipo di attività_ deve avere un metodo accessibile corrispondente `GetAwaiter()` che restituisce un'istanza di un _tipo awaiter_ (vedere _espressioni awaitable C# 7.7.7.1_ ).
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
## <a name="builder-type"></a>Tipo di generatore
Il _tipo di generatore_ è `class` o `struct` corrisponde al _tipo di attività_ specifico.
Il _tipo di generatore_ può includere al massimo 1 parametro di tipo e non deve essere annidato in un tipo generico.
Il _tipo di generatore_ presenta i `public` metodi seguenti:
Per i tipi di _Generatore_ non generici `SetResult()` non dispone di parametri.
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
## <a name="execution"></a>Esecuzione
I tipi precedenti vengono usati dal compilatore per generare il codice per la macchina a stati di un `async` metodo.
Il codice generato è equivalente al codice generato per i metodi asincroni che restituiscono `Task` , `Task<T>` o `void` .
La differenza è che, per i tipi noti, i _tipi di generatore_ sono noti anche per il compilatore.

`Builder.Create()` viene richiamato per creare un'istanza del _tipo di generatore_.

Se la macchina a stati viene implementata come `struct` , `builder.SetStateMachine(stateMachine)` viene chiamato con un'istanza boxed della macchina a Stati che il generatore può memorizzare nella cache, se necessario.

`builder.Start(ref stateMachine)` viene richiamato per associare il generatore all'istanza della macchina a stati generata dal compilatore.
Il generatore deve chiamare `stateMachine.MoveNext()` in `Start()` o dopo che `Start()` ha restituito per far avanzare la macchina a Stati.
Dopo `Start()` la restituzione, il `async` metodo chiama l' `builder.Task` attività per restituire dal metodo asincrono.

Ogni chiamata a `stateMachine.MoveNext()` comporterà l'avanzamento della macchina a Stati.
Se la macchina a stati viene completata correttamente, `builder.SetResult()` viene chiamato il metodo con il valore restituito del metodo, se presente.
Se viene generata un'eccezione nella macchina a Stati, `builder.SetException(exception)` viene chiamato il metodo.

Se la macchina a stati raggiunge un' `await expr` espressione, `expr.GetAwaiter()` viene richiamato.
Se il awaiter implementa `ICriticalNotifyCompletion` e `IsCompleted` è false, la macchina a stati richiama `builder.AwaitUnsafeOnCompleted(ref awaiter, ref stateMachine)` .
`AwaitUnsafeOnCompleted()` deve chiamare `awaiter.OnCompleted(action)` con un'azione che chiama `stateMachine.MoveNext()` quando l'elemento awaiter viene completato. Analogamente per `INotifyCompletion` e `builder.AwaitOnCompleted()` .

## <a name="overload-resolution"></a>Overload Resolution
La risoluzione dell'overload viene estesa per riconoscere i _tipi di attività_ oltre a `Task` e `Task<T>` .

Un' `async` espressione lambda senza valore restituito è una corrispondenza esatta per un parametro del candidato di overload di un _tipo di attività_ non generica e un' `async` espressione lambda con tipo restituito `T` è una corrispondenza esatta per un parametro di overload candidate di _tipo di attività_ generico. 

In caso contrario, se un' `async` espressione lambda non è una corrispondenza esatta per uno dei due parametri candidati dei _tipi di attività_ , o una corrispondenza esatta per entrambi, ed esiste una conversione implicita da un tipo candidato all'altro, l'oggetto del candidato prevale. In caso contrario, valutare in modo ricorsivo i tipi `A` e `B` all'interno di `Task1<A>` e `Task2<B>` per una migliore corrispondenza.

In caso contrario `async` , se un'espressione lambda non è una corrispondenza esatta per uno dei due parametri candidati dei _tipi di attività_ , ma un candidato è un tipo più specializzato rispetto all'altro, il candidato più specializzato prevale.
