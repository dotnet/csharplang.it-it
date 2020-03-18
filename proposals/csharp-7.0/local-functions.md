---
ms.openlocfilehash: a4b0fbbc600eaf1e705ad8e6bd9fcecb44100761
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484440"
---
# <a name="local-functions"></a>Funzioni locali

Si estende C# per supportare la dichiarazione di funzioni nell'ambito del blocco. Le funzioni locali possono usare (acquisire) variabili dall'ambito che lo contiene.

Il compilatore usa l'analisi di flusso per rilevare le variabili usate da una funzione locale prima di assegnarle un valore. Per ogni chiamata della funzione è necessario assegnare definitivamente tali variabili. Analogamente, il compilatore determina quali variabili vengono assegnate definitivamente al ritorno. Tali variabili vengono considerate assegnate definitivamente dopo che la funzione locale è stata richiamata.

Le funzioni locali possono essere chiamate da un punto lessicale prima della relativa definizione. Le istruzioni di dichiarazione di funzione locale non generano un avviso quando non sono raggiungibili.

TODO: _scrivere specifiche_

## <a name="syntax-grammar"></a>Grammatica della sintassi

Questa grammatica viene rappresentata come diff dalla grammatica delle specifiche corrente.

```diff
declaration-statement
    : local-variable-declaration ';'
    | local-constant-declaration ';'
+   | local-function-declaration
    ;

+local-function-declaration
+   : local-function-header local-function-body
+   ;

+local-function-header
+   : local-function-modifiers? return-type identifier type-parameter-list?
+       ( formal-parameter-list? ) type-parameter-constraints-clauses
+   ;

+local-function-modifiers
+   : (async | unsafe)
+   ;

+local-function-body
+   : block
+   | arrow-expression-body
+   ;
```

Le funzioni locali possono usare variabili definite nell'ambito di inclusione. Per l'implementazione corrente è necessario che ogni variabile letta all'interno di una funzione locale venga assegnata in modo definitivo, come se venisse eseguita la funzione locale in corrispondenza del punto di definizione. Inoltre, la definizione della funzione locale deve essere stata "eseguita" in qualsiasi punto di utilizzo.

Dopo aver sperimentato questo aspetto (ad esempio, non è possibile definire due funzioni locali ricorsivamente ricorsive), è stato modificato il modo in cui si vuole che l'assegnazione definita funzioni. La revisione (non ancora implementata) consiste nel fare in modo che tutte le variabili locali lette in una funzione locale siano assegnate definitivamente a ogni chiamata della funzione locale. Si tratta di un'operazione più complessa rispetto al suono e c'è un gruppo di lavoro rimanente per renderlo funzionante. Al termine, sarà possibile spostare le funzioni locali alla fine del blocco di inclusione.

Le nuove regole di assegnazione definite sono incompatibili con l'inferenza del tipo restituito di una funzione locale, pertanto è probabile che sia in corso la rimozione del supporto per dedurre il tipo restituito.

A meno che non si converta una funzione locale in un delegato, l'acquisizione viene eseguita in frame che sono tipi valore. Ciò significa che non si ottiene alcuna pressione GC dall'uso di funzioni locali con l'acquisizione.

### <a name="reachability"></a>Raggiungibilità

Aggiungiamo alla specifica

> Il corpo di un'espressione lambda con corpo di istruzione o di una funzione locale è considerato raggiungibile.
