---
ms.openlocfilehash: 7ea62713416ef82034963aef06f3cb11703342ed
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484608"
---
# <a name="stackalloc-array-initializers"></a>Inizializzatori di matrice stackalloc

## <a name="summary"></a>Summary
[summary]: #summary

Consente di usare la sintassi dell'inizializzatore di matrici con `stackalloc`.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Le matrici ordinarie possono avere elementi inizializzati al momento della creazione. Sembra ragionevole consentirne l'uso in `stackalloc` caso.

Il motivo per cui questa sintassi non è consentita con `stackalloc` si verifica abbastanza spesso.  
Vedere, ad esempio, [#1112](https://github.com/dotnet/csharplang/issues/1112)

## <a name="detailed-design"></a>Progettazione dettagliata

Le matrici ordinarie possono essere create tramite la sintassi seguente:

```csharp
new int[3]
new int[3] { 1, 2, 3 }
new int[] { 1, 2, 3 }
new[] { 1, 2, 3 }
```

È necessario consentire la creazione di matrici allocate nello stack tramite:  

```csharp
stackalloc int[3]               // currently allowed
stackalloc int[3] { 1, 2, 3 }
stackalloc int[] { 1, 2, 3 }
stackalloc[] { 1, 2, 3 }
```

La semantica di tutti i case è approssimativamente identica a quella delle matrici.  
Nell'ultimo caso, ad esempio, il tipo di elemento viene dedotto dall'inizializzatore e deve essere un tipo "non gestito".

Nota: la funzionalità non dipende dalla destinazione `Span<T>`. È altrettanto applicabile in `T*` caso, quindi non sembra ragionevole predicarlo in `Span<T>` caso.  

## <a name="translation"></a>Traduzione

L'implementazione ingenua può semplicemente inizializzare la matrice subito dopo la creazione tramite una serie di assegnazioni di elementi.  

Analogamente a quanto avviene con le matrici, potrebbe essere possibile e opportuno rilevare i casi in cui tutti o la maggior parte degli elementi sono tipi copiabili e utilizzano tecniche più efficienti copiando lo stato creato in precedenza di tutti gli elementi costanti. 

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Si tratta di una funzionalità di praticità. È possibile semplicemente non eseguire alcuna operazione.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

## <a name="design-meetings"></a>Riunioni di progettazione

Nessuno ancora. 
