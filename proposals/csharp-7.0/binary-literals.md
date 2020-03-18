---
ms.openlocfilehash: 6f6c24e826e9fe9b9e8c97549add1029f00bcf60
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484426"
---
# <a name="binary-literals"></a><span data-ttu-id="986d7-101">Valori letterali binari</span><span class="sxs-lookup"><span data-stu-id="986d7-101">Binary literals</span></span>

<span data-ttu-id="986d7-102">Esiste una richiesta relativamente comune di aggiungere valori letterali binari a C# e VB.</span><span class="sxs-lookup"><span data-stu-id="986d7-102">There’s a relatively common request to add binary literals to C# and VB.</span></span> <span data-ttu-id="986d7-103">Per le maschere di maschera (ad esempio, le enumerazioni di flag) questo aspetto è effettivamente utile, ma sarebbe anche ideale solo a scopo didattico.</span><span class="sxs-lookup"><span data-stu-id="986d7-103">For bitmasks (e.g. flag enums) this seems genuinely useful, but it would also be great just for educational purposes.</span></span>

<span data-ttu-id="986d7-104">I valori letterali binari avranno un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="986d7-104">Binary literals would look like this:</span></span>

```csharp
int nineteen = 0b10011;
```

<span data-ttu-id="986d7-105">Sono sintatticamente e semanticamente identici ai valori letterali esadecimali, tranne che per l'uso di `b`/`B` anziché `x`/`X`, che hanno solo cifre `0` e `1` e interpretate in base 2 anziché 16.</span><span class="sxs-lookup"><span data-stu-id="986d7-105">Syntactically and semantically they are identical to hexadecimal literals, except for using `b`/`B` instead of `x`/`X`, having only digits `0` and `1` and being interpreted in base 2 instead of 16.</span></span>

<span data-ttu-id="986d7-106">Il costo di implementazione di questi e un lieve overhead concettuale per gli utenti del linguaggio è ridotto.</span><span class="sxs-lookup"><span data-stu-id="986d7-106">There’s little cost to implementing these, and little conceptual overhead to users of the language.</span></span>

## <a name="syntax"></a><span data-ttu-id="986d7-107">Sintassi</span><span class="sxs-lookup"><span data-stu-id="986d7-107">Syntax</span></span>

<span data-ttu-id="986d7-108">La grammatica è la seguente:</span><span class="sxs-lookup"><span data-stu-id="986d7-108">The grammar would be as follows:</span></span>

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
