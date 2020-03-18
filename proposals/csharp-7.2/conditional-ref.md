---
ms.openlocfilehash: 63dfdfee9ea6c16e162f483aa1298feed297daef
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484699"
---
# <a name="conditional-ref-expressions"></a>Espressioni di riferimento condizionale

Il modello di associazione di una variabile ref a una o a un'altra espressione in modo condizionale C#non è attualmente esprimibile in.

La soluzione alternativa tipica consiste nell'introdurre un metodo come:

```csharp
ref T Choice(bool condition, ref T consequence, ref T alternative)
{
    if (condition)
    {
         return ref consequence;
    }
    else
    {
         return ref alternative;
    }
}
```

Si noti che questa non è una sostituzione esatta di una terna poiché tutti gli argomenti devono essere valutati nel sito di chiamata.

Il codice seguente non funzionerà come previsto:

```csharp
       // will crash with NRE because 'arr[0]' will be executed unconditionally
      ref var r = ref Choice(arr != null, ref arr[0], ref otherArr[0]);
```

La sintassi proposta sarà simile alla seguente:

```csharp
     <condition> ? ref <consequence> : ref <alternative>;
```

Il tentativo precedente con "Choice" può essere scritto _correttamente_ usando il ternario Ref come:

```csharp
     ref var r = ref (arr != null ? ref arr[0]: ref otherArr[0]);
```

La differenza dalla scelta è che l'accesso alle espressioni di conseguenza e alternative viene eseguito in modo _realmente_ condizionale, quindi non viene visualizzato un arresto anomalo se ```arr == null```

Il riferimento ternario è semplicemente una terna, in cui sia l'alternativa che la conseguenza sono refs. Richiede naturalmente che gli operandi di conseguenza/alternativi siano lvalue. Sarà inoltre necessario che la conseguenza e l'alternativa dispongano di tipi convertibili tra loro identità.

Il tipo dell'espressione verrà calcolato in modo analogo a quello della terna normale. I.E. Se la conseguenza e l'alternativa hanno un'identità convertibile, ma tipi diversi, verranno applicate le regole di Unione dei tipi esistenti.

La modalità Safe-to-return verrà considerata conservativa dagli operandi condizionali. Se non è sicuro restituire l'intero elemento, non è sicuro restituire.

La terna di riferimento è un LValue e, di conseguenza, può essere passata/assegnata/restituita per riferimento;

```csharp
     // pass by reference
     foo(ref (arr != null ? ref arr[0]: ref otherArr[0]));

     // return by reference
     return ref (arr != null ? ref arr[0]: ref otherArr[0]);
```

Essendo un LValue, può anche essere assegnato a. 

```csharp
    // assign to
    (arr != null ? ref arr[0]: ref otherArr[0]) = 1;
```

Il ternario ref può essere usato anche in un contesto normale (non Ref). Anche se non sarebbe comune, dal momento che era sufficiente usare una terna normale.

```csharp
     int x = (arr != null ? ref arr[0]: ref otherArr[0]);
```


___

Note sull'implementazione: 

La complessità dell'implementazione sembrerebbe la dimensione di una correzione di bug da moderato a grande. -I. E non sono molto costosi.
Non sono necessarie modifiche alla sintassi o all'analisi.
Non si verifica alcun effetto sui metadati o sull'interoperabilità. La funzionalità è completamente basata su espressioni.
Nessun effetto sul debug o sul PDB
