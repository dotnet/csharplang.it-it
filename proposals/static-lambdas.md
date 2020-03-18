---
ms.openlocfilehash: 6f05bbc1365e59d737103b586db9d4a242c6d306
ms.sourcegitcommit: e9afb74cc1abd56db93b4b50bd5e6765e27c1c5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "79485084"
---
# <a name="static-lambdas"></a>Espressioni lambda statiche

## <a name="summary"></a>Summary

Supportano le espressioni lambda che non consentono l'acquisizione dello stato dall'ambito che lo contiene.

## <a name="motivation"></a>Motivazione

Evitare di acquisire lo stato involontariamente dal contesto di inclusione.

## <a name="detailed-design"></a>Progettazione dettagliata

Le espressioni lambda con `static` non possono acquisire lo stato dall'ambito che lo contiene.
Di conseguenza, variabili locali, parametri e `this` dall'ambito di inclusione non sono disponibili all'interno di un'espressione lambda `static`.

Una `static` lambda non può fare riferimento a membri di istanza da un riferimento implicito o esplicito `this` o `base`.

Un'espressione lambda `static` può fare riferimento a `static` membri dall'ambito che lo contiene.

Un'espressione lambda `static` può fare riferimento a `constant` definizioni dall'ambito che lo contiene.

`nameof()` in un'espressione lambda `static` può fare riferimento a variabili locali, parametri o `this` o `base` dall'ambito che lo contiene.

Le regole di accessibilità per i membri `private` nell'ambito di inclusione sono le stesse per le espressioni lambda `static` e non`static`.

Non viene garantita alcuna garanzia in merito al fatto che una definizione lambda `static` venga emessa come metodo `static` nei metadati. Questa operazione viene lasciata all'implementazione del compilatore per ottimizzare.

Una funzione locale non`static` o un'espressione lambda può acquisire lo stato da un `static` lambda di inclusione ma non può acquisire lo stato al di fuori della `static` lambda di inclusione.

In un albero delle espressioni è possibile utilizzare un'espressione lambda `static`.

La rimozione del modificatore `static` da un'espressione lambda in un programma valido non comporta la modifica del significato del programma.
