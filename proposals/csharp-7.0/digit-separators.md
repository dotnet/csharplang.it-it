---
ms.openlocfilehash: 5476f4438ad79a26b3615154f789d8ed04cb61aa
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484503"
---
# <a name="digit-separators"></a><span data-ttu-id="e9e33-101">Separatori di cifre</span><span class="sxs-lookup"><span data-stu-id="e9e33-101">Digit separators</span></span>

<span data-ttu-id="e9e33-102">La possibilità di raggruppare le cifre in valori letterali numerici di grandi dimensioni potrebbe avere un impatto notevole sulla leggibilità e senza alcun aspetto negativo.</span><span class="sxs-lookup"><span data-stu-id="e9e33-102">Being able to group digits in large numeric literals would have great readability impact and no significant downside.</span></span> 

<span data-ttu-id="e9e33-103">L'aggiunta di valori letterali binari (#215) aumenta la probabilità che i valori letterali numerici siano lunghi, quindi le due funzionalità si migliorano.</span><span class="sxs-lookup"><span data-stu-id="e9e33-103">Adding binary literals (#215) would increase the likelihood of numeric literals being long, so the two features enhance each other.</span></span> 

<span data-ttu-id="e9e33-104">Si seguiranno Java e altri e si userà un carattere di sottolineatura `_` come separatore di cifre.</span><span class="sxs-lookup"><span data-stu-id="e9e33-104">We would follow Java and others, and use an underscore `_` as a digit separator.</span></span> <span data-ttu-id="e9e33-105">Potrebbe essere presente ovunque in un valore letterale numerico (eccetto il primo e l'ultimo carattere), perché raggruppamenti diversi possono avere senso in scenari diversi e soprattutto per basi numeriche diverse:</span><span class="sxs-lookup"><span data-stu-id="e9e33-105">It would be able to occur everywhere in a numeric literal (except as the first and last character), since different groupings may make sense in different scenarios and especially for different numeric bases:</span></span>

```csharp
int bin = 0b1001_1010_0001_0100;
int hex = 0x1b_a0_44_fe;
int dec = 33_554_432;
int weird = 1_2__3___4____5_____6______7_______8________9;
double real = 1_000.111_1e-1_000;
```

<span data-ttu-id="e9e33-106">Qualsiasi sequenza di cifre può essere separata da caratteri di sottolineatura, possibilmente più di un carattere di sottolineatura tra due cifre consecutive.</span><span class="sxs-lookup"><span data-stu-id="e9e33-106">Any sequence of digits may be separated by underscores, possibly more than one underscore between two consecutive digits.</span></span> <span data-ttu-id="e9e33-107">Sono consentiti in decimali e esponenti, ma dopo la regola precedente potrebbero non essere visualizzati accanto al separatore decimale (`10_.0`), accanto al carattere esponente (`1.1e_1`) o accanto all'identificatore di tipo (`10_f`).</span><span class="sxs-lookup"><span data-stu-id="e9e33-107">They are allowed in decimals as well as exponents, but following the previous rule, they may not appear next to the decimal (`10_.0`), next to the exponent character (`1.1e_1`), or next to the type specifier (`10_f`).</span></span> <span data-ttu-id="e9e33-108">Se utilizzati in valori letterali binari e esadecimali, potrebbero non essere visualizzati immediatamente dopo la `0x` o `0b`.</span><span class="sxs-lookup"><span data-stu-id="e9e33-108">When used in binary and hexadecimal literals, they may not appear immediately following the `0x` or `0b`.</span></span>

<span data-ttu-id="e9e33-109">La sintassi è semplice e i separatori non hanno alcun effetto semantico. vengono semplicemente ignorati.</span><span class="sxs-lookup"><span data-stu-id="e9e33-109">The syntax is straightforward, and the separators have no semantic impact - they are simply ignored.</span></span>

<span data-ttu-id="e9e33-110">Si tratta di un valore ampio ed è facile da implementare.</span><span class="sxs-lookup"><span data-stu-id="e9e33-110">This has broad value and is easy to implement.</span></span>
