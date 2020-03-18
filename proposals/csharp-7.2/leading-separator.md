---
ms.openlocfilehash: 6ec94aaabb2c52393a87ee450dbae972b6a50bd5
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484713"
---
# <a name="allow-digit-separator-after-0b-or-0x"></a><span data-ttu-id="adbf3-101">Consenti separatore cifre dopo 0B o 0x</span><span class="sxs-lookup"><span data-stu-id="adbf3-101">Allow digit separator after 0b or 0x</span></span>

<span data-ttu-id="adbf3-102">In C# 7,2, viene esteso il set di posizioni che i separatori di cifre (il carattere di sottolineatura) possono apparire in valori letterali integrali.</span><span class="sxs-lookup"><span data-stu-id="adbf3-102">In C# 7.2, we extend the set of places that digit separators (the underscore character) can appear in integral literals.</span></span> <span data-ttu-id="adbf3-103">[A partire C# da 7,0, i separatori sono consentiti tra le cifre di un valore letterale](../csharp-7.0/digit-separators.md).</span><span class="sxs-lookup"><span data-stu-id="adbf3-103">[Beginning in C# 7.0, separators are permitted between the digits of a literal](../csharp-7.0/digit-separators.md).</span></span> <span data-ttu-id="adbf3-104">Ora, in C# 7,2, i separatori di cifre sono consentiti anche prima della prima cifra significativa di un valore letterale binario o esadecimale, dopo il prefisso.</span><span class="sxs-lookup"><span data-stu-id="adbf3-104">Now, in C# 7.2, we also permit digit separators before the first significant digit of a binary or hexadecimal literal, after the prefix.</span></span>

```csharp
    123      // permitted in C# 1.0 and later
    1_2_3    // permitted in C# 7.0 and later
    0x1_2_3  // permitted in C# 7.0 and later
    0b101    // binary literals added in C# 7.0
    0b1_0_1  // permitted in C# 7.0 and later

    // in C# 7.2, _ is permitted after the `0x` or `0b`
    0x_1_2   // permitted in C# 7.2 and later
    0b_1_0_1 // permitted in C# 7.2 and later
```

<span data-ttu-id="adbf3-105">Non è consentito un valore letterale integer decimale con un carattere di sottolineatura iniziali.</span><span class="sxs-lookup"><span data-stu-id="adbf3-105">We do not permit a decimal integer literal to have a leading underscore.</span></span> <span data-ttu-id="adbf3-106">Un token come `_123` è un identificatore.</span><span class="sxs-lookup"><span data-stu-id="adbf3-106">A token such as `_123` is an identifier.</span></span>
