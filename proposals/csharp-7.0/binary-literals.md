---
ms.openlocfilehash: 6f6c24e826e9fe9b9e8c97549add1029f00bcf60
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484426"
---
# <a name="binary-literals"></a>Valori letterali binari

Esiste una richiesta relativamente comune di aggiungere valori letterali binari a C# e VB. Per le maschere di maschera (ad esempio, le enumerazioni di flag) questo aspetto è effettivamente utile, ma sarebbe anche ideale solo a scopo didattico.

I valori letterali binari avranno un aspetto simile al seguente:

```csharp
int nineteen = 0b10011;
```

Sono sintatticamente e semanticamente identici ai valori letterali esadecimali, tranne che per l'uso di `b`/`B` anziché `x`/`X`, che hanno solo cifre `0` e `1` e interpretate in base 2 anziché 16.

Il costo di implementazione di questi e un lieve overhead concettuale per gli utenti del linguaggio è ridotto.

## <a name="syntax"></a>Sintassi

La grammatica è la seguente:

```antlr
integer-literal:
    : ...
    | binary-integer-literal
    ;
binary-integer-literal:
    : `0b` binary-digits integer-type-suffix-opt
    | `0B` binary-digits integer-type-suffix-opt
    ;
binary-digits:
    : binary-digit
    | binary-digits binary-digit
    ;
binary-digit:
    : `0`
    | `1`
    ;
```
