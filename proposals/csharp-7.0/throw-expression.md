---
ms.openlocfilehash: 2532a24e867930d2f27614f19c77585dbce80dfa
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484545"
---
# <a name="throw-expression"></a>Espressione throw

Estendiamo il set di form di espressione da includere

```antlr
throw_expression
    : 'throw' null_coalescing_expression
    ;

null_coalescing_expression
    : throw_expression
    ;
```

Di seguito sono riportate le regole di tipo:

- Un *throw_expression* non dispone di alcun tipo.
- Un *throw_expression* è convertibile in ogni tipo mediante una conversione implicita.

Un' *espressione throw* genera il valore prodotto dalla valutazione della *null_coalescing_expression*, che deve indicare un valore del tipo di classe `System.Exception`, di un tipo di classe che deriva da `System.Exception` o di un tipo di parametro di tipo che ha `System.Exception` (o una sottoclasse) come classe di base efficace. Se la valutazione dell'espressione produce `null`, viene invece generata una `System.NullReferenceException`.

Il comportamento in fase di esecuzione della valutazione di un' *espressione throw* è identico a quello [specificato per un' *istruzione throw*](../../spec/statements.md#the-throw-statement).

Di seguito sono riportate le regole di analisi del flusso:

- Per ogni variabile *v*, *v* viene definitivamente assegnata prima del *null_coalescing_expression* di un *throw_expression* IFF viene assegnata definitivamente prima del *throw_expression*.
- Per ogni variabile *v*, *v* viene assegnato definitivamente dopo *throw_expression*.

Un' *espressione throw* è consentita solo nei contesti sintattici seguenti:
- Come secondo o terzo operando di un operatore condizionale ternario `?:`
- Come secondo operando di un operatore di Unione null `??`
- Come corpo di un'espressione lambda o di un metodo con corpo di espressione.
