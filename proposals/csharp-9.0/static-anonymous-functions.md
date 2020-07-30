---
ms.openlocfilehash: 0a6b3a4aa86bac8e7855ec2f4c50ac022941000e
ms.sourcegitcommit: 89bbdd101f539cefd41b2b8b9087fc9cdbcf3c62
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87412480"
---
# <a name="static-anonymous-functions"></a>Funzioni anonime statiche

## <a name="summary"></a>Summary

Consente un modificatore ' static ' per le espressioni lambda e i metodi anonimi, che non consente l'acquisizione di variabili locali o lo stato dell'istanza dagli ambiti contenitore.

## <a name="motivation"></a>Motivazione

Evitare l'acquisizione accidentale dello stato dal contesto di inclusione, che può causare un periodo di conservazione imprevisto di oggetti acquisiti o allocazioni aggiuntive impreviste.

## <a name="detailed-design"></a>Progettazione dettagliata

Un metodo lambda o anonimo può avere un `static` modificatore. Il `static` modificatore indica che l'espressione lambda o il metodo anonimo è una *funzione statica anonima*.

Una *funzione anonima statica* non può acquisire lo stato dall'ambito che lo contiene.
Di conseguenza, variabili locali, parametri e `this` dall'ambito di inclusione non sono disponibili all'interno di una *funzione anonima statica*.

Una *funzione anonima statica* non può fare riferimento a membri di istanza da un riferimento implicito o esplicito `this` o `base` .

Una *funzione statica anonima* può fare riferimento a `static` membri dall'ambito che lo contiene.

Una *funzione statica anonima* può fare riferimento a `constant` definizioni dall'ambito che lo contiene.

`nameof()`in una *funzione statica anonima* può fare riferimento a variabili locali, parametri o `this` o `base` dall'ambito di inclusione.

Le regole di accessibilità per `private` i membri nell'ambito di inclusione sono le stesse per le `static` funzioni e non `static` anonime.

Non viene garantita alcuna garanzia in merito al fatto che una definizione di *funzione statica anonima* venga emessa come `static` Metodo nei metadati. Questa operazione viene lasciata all'implementazione del compilatore per ottimizzare.

Una funzione non `static` locale o una funzione anonima può acquisire lo stato da una *funzione statica anonima* di inclusione, ma non può acquisire lo stato all'esterno della *funzione anonima*di inclusione statica.

La rimozione del `static` modificatore da una funzione anonima in un programma valido non comporta la modifica del significato del programma.
