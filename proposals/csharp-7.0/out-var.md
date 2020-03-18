---
ms.openlocfilehash: a0d80afc47e9f0073237db9b8d7a4f0b045c1b0b
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484510"
---
# <a name="out-variable-declarations"></a>Dichiarazioni di variabili out

La funzionalità di *dichiarazione della variabile out* consente di dichiarare una variabile nel percorso in cui viene passata come argomento `out`.

```antlr
argument_value
    : 'out' type identifier
    | ...
    ;
```

Una variabile dichiarata in questo modo viene chiamata *variabile out*. È possibile usare la parola chiave contestuale `var` per il tipo della variabile. L'ambito sarà identico a quello di una *variabile di tipo pattern* introdotta tramite criteri di ricerca.

In base alla specifica del linguaggio (sezione accesso agli elementi 7.6.7), l'elenco di argomenti di un accesso a elementi (espressione di indicizzazione) non contiene argomenti ref o out. Tuttavia, sono consentiti dal compilatore per diversi scenari, ad esempio gli indicizzatori dichiarati nei metadati che accettano `out`.

Nell'ambito di una variabile locale introdotta da un argument_value, si tratta di un errore in fase di compilazione per fare riferimento a tale variabile locale in una posizione testuale che precede la relativa dichiarazione.

È anche un errore fare riferimento a una variabile out tipizzata in modo implicito (§ 8.5.1) nello stesso elenco di argomenti che contiene immediatamente la relativa dichiarazione.

La risoluzione dell'overload viene modificata come segue:

Viene aggiunta una nuova conversione:

> Viene eseguita una *conversione da un'espressione* da una dichiarazione di variabile out tipizzata in modo implicito a ogni tipo.

Anche

> Il tipo di un argomento della variabile out tipizzato in modo esplicito è il tipo dichiarato.

e

> Un argomento della variabile out tipizzata in modo implicito non dispone di alcun tipo.

La *conversione da espressione* da una dichiarazione di variabile out tipizzata in modo implicito non è considerata migliore rispetto a qualsiasi altra *conversione da Expression*.

Il tipo di una variabile out tipizzata in modo implicito è il tipo del parametro corrispondente nella firma del metodo selezionato dalla risoluzione dell'overload.

Viene aggiunto il nuovo nodo della sintassi `DeclarationExpressionSyntax` per rappresentare la dichiarazione in un argomento out var.
