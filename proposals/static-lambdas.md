---
ms.openlocfilehash: efe927e6d3abdde5a350eadc9208949710bb4a39
ms.sourcegitcommit: b901db48deffcbe2ec7cc08ec6cb376d1f7faecb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85345202"
---
# <a name="static-lambdas"></a>Espressioni lambda statiche

## <a name="summary"></a>Riepilogo

Supportano le espressioni lambda che non consentono l'acquisizione dello stato dall'ambito che lo contiene.

## <a name="motivation"></a>Motivazione

Evitare di acquisire lo stato involontariamente dal contesto di inclusione.

## <a name="detailed-design"></a>Progettazione dettagliata

Le espressioni lambda con `static` non possono acquisire lo stato dall'ambito che lo contiene.
Di conseguenza, variabili locali, parametri e `this` dall'ambito di inclusione non sono disponibili all'interno di un' `static` espressione lambda.

Un' `static` espressione lambda non può fare riferimento a membri di istanza da un riferimento implicito o esplicito `this` o `base` .

Un' `static` espressione lambda può fare riferimento `static` ai membri dell'ambito che lo contiene.

Un' `static` espressione lambda può fare riferimento a `constant` definizioni dall'ambito che lo contiene.

`nameof()`in un' `static` espressione lambda può fare riferimento a variabili locali, parametri o `this` o `base` dall'ambito di inclusione.

Le regole di accessibilità per `private` i membri nell'ambito di inclusione sono le stesse per `static` e non per le `static` espressioni lambda.

Non viene garantita alcuna garanzia in merito al fatto che una `static` definizione lambda venga emessa come `static` Metodo nei metadati. Questa operazione viene lasciata all'implementazione del compilatore per ottimizzare.

Una `static` funzione o un'espressione lambda non locale può acquisire lo stato da un' `static` espressione lambda di inclusione ma non può acquisire lo stato al di fuori dell'espressione lambda di inclusione `static` .

La rimozione del `static` modificatore da un'espressione lambda in un programma valido non comporta la modifica del significato del programma.
