---
ms.openlocfilehash: fecd5a6c1e0f6c7a7a7beac0e4e60445281c7846
ms.sourcegitcommit: 1b488e76c2c07aafc377bc5e8a7197252c82f425
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "79485098"
---
# <a name="static-local-functions"></a>Funzioni locali statiche

## <a name="summary"></a>Summary

Supportano le funzioni locali che non consentono l'acquisizione dello stato dall'ambito che lo contiene.

## <a name="motivation"></a>Motivazione

Evitare di acquisire lo stato involontariamente dal contesto di inclusione.
Consente di usare le funzioni locali negli scenari in cui è necessario un `static` metodo.

## <a name="detailed-design"></a>Progettazione dettagliata

Una funzione locale dichiarata `static` non è in grado di acquisire lo stato dall'ambito che lo contiene.
Di conseguenza, variabili locali, parametri e `this` dall'ambito di inclusione non sono disponibili all'interno di una funzione `static` locale.

Una `static` funzione locale non può fare riferimento a membri di istanza da un riferimento implicito o esplicito `this` o `base`.

Una funzione locale `static` può fare riferimento a `static` membri dall'ambito che lo contiene.

Una funzione locale `static` può fare riferimento a definizioni `constant` dall'ambito che lo contiene.

`nameof()` in una funzione locale `static` può fare riferimento a variabili locali, parametri o `this` o `base` dall'ambito che lo contiene.

Le regole di accessibilità per i membri `private` nell'ambito di inclusione sono le stesse per `static` e non`static` funzioni locali.

Una `static` definizione di funzione locale viene emessa come metodo di `static` nei metadati, anche se utilizzata solo in un delegato.

Una funzione locale non`static` o un'espressione lambda può acquisire lo stato da una funzione `static` locale che lo contiene ma non può acquisire lo stato al di fuori della funzione `static` locale di inclusione.

Non è possibile richiamare una funzione locale `static` in un albero delle espressioni.

Una chiamata a una funzione locale viene emessa come `call` anziché `callvirt`, indipendentemente dal fatto che la funzione locale sia `static`ta.

Risoluzione dell'overload di una chiamata all'interno di una funzione locale non interessata dal fatto che la funzione locale sia `static`.

La rimozione del modificatore `static` da una funzione locale in un programma valido non comporta la modifica del significato del programma.

## <a name="design-meetings"></a>Riunioni di progettazione

https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-09-10.md#static-local-functions
