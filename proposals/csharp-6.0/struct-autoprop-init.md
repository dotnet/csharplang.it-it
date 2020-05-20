---
ms.openlocfilehash: ae8b583d82f99135e2ba731604cd1febff4fade7
ms.sourcegitcommit: 125539b88d970bb2924d17a997c47cc0c7b806fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83566752"
---
# <a name="relaxed-rules-for-auto-properties-in-structs"></a><span data-ttu-id="98cae-101">Regole flessibili per le proprietà automatiche negli struct</span><span class="sxs-lookup"><span data-stu-id="98cae-101">Relaxed rules for auto-properties in structs</span></span> 

<span data-ttu-id="98cae-102">Prima di C# 6,0 non è stato possibile scrivere codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="98cae-102">Before C# 6.0 you could not write code like this:</span></span> 

```csharp
struct S 
{ 
    int X { get; set; } 
    int Y { get; set; } 
    public S(int x, int y) 
    { 
        this.X = x; 
        this.Y = y; 
    } 
} 
```

```none
error CS0188: The 'this' object cannot be used before all of its fields are assigned to
error CS0843: Backing field for automatically implemented property 'S.X' must be fully assigned before control is returned to the caller. Consider calling the default constructor from a constructor initializer. 
error CS0843: Backing field for automatically implemented property 'S.Y' must be fully assigned before control is returned to the caller. Consider calling the default constructor from a constructor initializer. 
```
 
<span data-ttu-id="98cae-103">Una soluzione alternativa consisteva nel richiamare il costruttore predefinito:</span><span class="sxs-lookup"><span data-stu-id="98cae-103">A workaround was to invoke the default constructor:</span></span> 

```csharp
struct S 
{ 
    int X { get; set; } 
    int Y { get; set; } 
    public S(int x, int y) : this() 
    { 
        this.X = x; 
        this.Y = y; 
    } 
} 
```

<span data-ttu-id="98cae-104">oppure per inizializzare il valore predefinito:</span><span class="sxs-lookup"><span data-stu-id="98cae-104">or to initialize this to the default value:</span></span> 

```csharp
struct S 
{ 
    int X { get; set; } 
    int Y { get; set; } 
    public S(int x, int y) 
    { 
        this = default(S); 
        this.X = x; 
        this.Y = y; 
    } 
} 
```

<span data-ttu-id="98cae-105">Sfortunatamente, entrambi i metodi disabilitano in modo efficace l'analisi di assegnazione definita per i campi di istanza per il costruttore specificato.</span><span class="sxs-lookup"><span data-stu-id="98cae-105">Unfortunately, both methods effectively disabled definite assignment analysis for instance fields for the given constructor.</span></span> 

<span data-ttu-id="98cae-106">C# 6,0 attenua queste regole tenendo traccia delle assegnazioni alle proprietà automatiche dell'istanza come se fossero assegnazioni ai campi sottoposti a backup, quindi il codice all'inizio di questa sezione è ora valido.</span><span class="sxs-lookup"><span data-stu-id="98cae-106">C# 6.0 relaxes these rules by tracking assignments to instance auto-properties as if they were assignments to their backing fields, so the code in the beginning of this section is now valid.</span></span> 
