---
ms.openlocfilehash: 165de098ddbd753416e01c4ff8e44750d9b7f66c
ms.sourcegitcommit: a0b59a6768813152b4b5b6df3637041a64aba2ee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92185115"
---
# <a name="name-shadowing-in-nested-functions"></a>Shadowing del nome in funzioni annidate

## <a name="summary"></a>Riepilogo

Consentire i nomi delle variabili nelle espressioni lambda e nelle funzioni locali per riutilizzare (e ombreggiare) i nomi del metodo o della funzione contenitore.

## <a name="detailed-design"></a>Progettazione dettagliata

Con `-langversion:8` , i nomi delle variabili locali, le funzioni locali, i parametri, i parametri di tipo e le variabili di intervallo in un'espressione lambda o in una funzione locale possono riutilizzare i nomi di variabili locali, funzioni locali, parametri, parametri di tipo e variabili di intervallo da un metodo o una funzione di inclusione. Il nome nella funzione annidata nasconde il simbolo con lo stesso nome della funzione contenitore all'interno della funzione nidificata.

Lo shadowing è supportato per `static` le `static` funzioni lambda e non locali.

Non è possibile modificare il comportamento utilizzando `-langversion:7.3` o versioni precedenti: i nomi nelle funzioni annidate che i nomi Shadow del metodo o della funzione contenitore vengono segnalati come errori in tali casi.

Tutte le ombreggiature consentite in precedenza sono ancora supportate con `-langversion:8` . Ad esempio, i nomi delle variabili possono nascondere il tipo e i nomi dei membri; i nomi delle variabili e possono includere il metodo di inclusione o i nomi delle funzioni locali.

Lo shadowing di un nome dichiarato in un ambito di inclusione nella stessa lambda o nella stessa funzione locale viene comunque segnalato come un errore.

Viene segnalato un avviso per un parametro di tipo in una funzione locale che nasconde un parametro di tipo nel tipo, nel metodo o nella funzione di inclusione.

## <a name="design-meetings"></a>Riunioni di progettazione

- https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-09-10.md#static-local-functions
- https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-01-16.md#shadowing-in-nested-functions
