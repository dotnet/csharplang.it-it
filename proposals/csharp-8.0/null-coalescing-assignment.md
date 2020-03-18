---
ms.openlocfilehash: fdd858c895d56a7a6b410e6ea7be3032e4851fd6
ms.sourcegitcommit: 5a88d5432d32c690c6b870fc4be32cf26cadd76f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2019
ms.locfileid: "79484958"
---
# <a name="null-coalescing-assignment"></a>Assegnazione null coalescing

* [x] proposto
* [] Prototipo: non avviato
* [] Implementazione: non avviata
* [] Specifica: di seguito

## <a name="summary"></a>Summary
[summary]: #summary

Semplifica un modello di codifica comune a cui a una variabile viene assegnato un valore se è null.

Come parte di questa proposta, verranno rilasciati anche i requisiti di tipo in `??` per consentire a un'espressione il cui tipo è un parametro di tipo non vincolato da usare sul lato sinistro.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

È comune vedere il codice del modulo

```csharp
if (variable == null)
{
    variable = expression;
}
```

Questa proposta aggiunge un operatore binario non che esegue l'overload al linguaggio che esegue questa funzione.

Sono state apportate almeno otto richieste della community separate per questa funzionalità.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Viene aggiunta una nuova forma di operatore di assegnazione

``` antlr
assignment_operator
    : '??='
    ;
```

Che segue le [regole semantiche esistenti per gli operatori di assegnazione composti](../../spec/expressions.md#compound-assignment), ad eccezione del fatto che Elide l'assegnazione se il lato sinistro non è null. Di seguito sono riportate le regole per questa funzionalità.

Dato `a ??= b`, dove `A` è il tipo di `a`, `B` è il tipo di `b`e `A0` è il tipo sottostante di `A` se `A` è un tipo di valore Nullable:

1. Se `A` non esiste o è un tipo di valore non nullable, si verifica un errore in fase di compilazione.
2. Se `B` non è convertibile in modo implicito in `A` o `A0` (se `A0` esiste), si verifica un errore in fase di compilazione.
3. Se `A0` esiste e `B` è implicitamente convertibile in `A0`e `B` non è dinamico, il tipo di `a ??= b` è `A0`. `a ??= b` viene valutata in fase di esecuzione come:
   ```C#
   var tmp = a.GetValueOrDefault();
   if (!a.HasValue) { tmp = b; a = tmp; }
   tmp
   ```
   Con la differenza che `a` viene valutata una sola volta.
4. In caso contrario, il tipo di `a ??= b` è `A`. `a ??= b` viene valutata in fase di esecuzione come `a ?? (a = b)`, ad eccezione del fatto che `a` viene valutata una sola volta.


Per il relax dei requisiti dei tipi di `??`, viene aggiornata la specifica in cui viene attualmente indicato, dato `a ?? b`, in cui `A` è il tipo di `a`:

> 1. Se esiste e non è un tipo nullable o un tipo riferimento, si verifica un errore in fase di compilazione.

Questo requisito è stato rilassato per:

1. Se esiste e è un tipo di valore non nullable, si verifica un errore in fase di compilazione.

Ciò consente all'operatore di Unione null di funzionare sui parametri di tipo non vincolati, poiché il parametro di tipo non vincolato esiste, non è un tipo nullable e non è un tipo di riferimento.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Come per qualsiasi funzionalità del linguaggio, è necessario stabilire se la complessità aggiuntiva al linguaggio venga ripagata in base a una maggiore chiarezza offerta C# al corpo dei programmi che trarrebbero vantaggio dalla funzionalità.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Il programmatore può scrivere `(x = x ?? y)`, `if (x == null) x = y;`o `x ?? (x = y)` manualmente.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

- [] Richiede la revisione di LDM
- [] È necessario supportare anche `&&=` e operatori di `||=`?

## <a name="design-meetings"></a>Riunioni di progettazione

No.
