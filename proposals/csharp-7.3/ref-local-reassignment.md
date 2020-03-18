---
ms.openlocfilehash: c1a77d9337ecd16f5ea1c30d18f6422552b0dfb2
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484601"
---
# <a name="ref-local-reassignment"></a>Riassegnazione locale Ref

In C# 7,3 è stato aggiunto il supporto per riassociare il referente di una variabile locale ref o di un parametro ref.

Al set di `assignment_operator`s viene aggiunto il codice seguente.

```antlr
assignment_operator
    : '=' 'ref'
    ;
```

L'operatore `=ref` viene chiamato ***operatore di assegnazione Ref***. Non è un *operatore di assegnazione composto*. L'operando sinistro deve essere un'espressione associata a una variabile locale Ref, un parametro ref (diverso da `this`) o un parametro out. L'operando destro deve essere un'espressione che restituisce un lvalue che designa un valore dello stesso tipo dell'operando sinistro.

L'operando destro deve essere assegnato in modo sicuro al punto dell'assegnazione Ref.

Quando l'operando sinistro viene associato a un parametro di `out`, è un errore se il parametro `out` non è stato assegnato definitivamente all'inizio dell'operatore di assegnazione di riferimento.

Se l'operando sinistro è un riferimento scrivibile (ovvero definisce un valore diverso da un parametro `ref readonly` local o `in`), l'operando destro deve essere un lvalue scrivibile.

L'operatore di assegnazione Ref restituisce un lvalue del tipo assegnato. È scrivibile se l'operando sinistro è scrivibile, ovvero non `ref readonly` o `in`.

Le regole di sicurezza per questo operatore sono:

- Per un `e1 = ref e2`di riassegnazione Ref, il valore di *riferimento-safe-to-escape* di `e2` deve essere almeno uguale a un ambito come *ref-safe-to-escape* di `e1`.

Dove *ref-safe-to-escape* è definito in [sicurezza per i tipi simili a Ref](../csharp-7.2/span-safety.md)
