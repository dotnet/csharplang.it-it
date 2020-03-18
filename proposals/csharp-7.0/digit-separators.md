---
ms.openlocfilehash: 5476f4438ad79a26b3615154f789d8ed04cb61aa
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484503"
---
# <a name="digit-separators"></a>Separatori di cifre

La possibilità di raggruppare le cifre in valori letterali numerici di grandi dimensioni potrebbe avere un impatto notevole sulla leggibilità e senza alcun aspetto negativo. 

L'aggiunta di valori letterali binari (#215) aumenta la probabilità che i valori letterali numerici siano lunghi, quindi le due funzionalità si migliorano. 

Si seguiranno Java e altri e si userà un carattere di sottolineatura `_` come separatore di cifre. Potrebbe essere presente ovunque in un valore letterale numerico (eccetto il primo e l'ultimo carattere), perché raggruppamenti diversi possono avere senso in scenari diversi e soprattutto per basi numeriche diverse:

```csharp
int bin = 0b1001_1010_0001_0100;
int hex = 0x1b_a0_44_fe;
int dec = 33_554_432;
int weird = 1_2__3___4____5_____6______7_______8________9;
double real = 1_000.111_1e-1_000;
```

Qualsiasi sequenza di cifre può essere separata da caratteri di sottolineatura, possibilmente più di un carattere di sottolineatura tra due cifre consecutive. Sono consentiti in decimali e esponenti, ma dopo la regola precedente potrebbero non essere visualizzati accanto al separatore decimale (`10_.0`), accanto al carattere esponente (`1.1e_1`) o accanto all'identificatore di tipo (`10_f`). Se utilizzati in valori letterali binari e esadecimali, potrebbero non essere visualizzati immediatamente dopo la `0x` o `0b`.

La sintassi è semplice e i separatori non hanno alcun effetto semantico. vengono semplicemente ignorati.

Si tratta di un valore ampio ed è facile da implementare.
