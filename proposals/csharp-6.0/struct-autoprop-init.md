---
ms.openlocfilehash: ae8b583d82f99135e2ba731604cd1febff4fade7
ms.sourcegitcommit: 125539b88d970bb2924d17a997c47cc0c7b806fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83566752"
---
# <a name="relaxed-rules-for-auto-properties-in-structs"></a>Regole flessibili per le proprietà automatiche negli struct 

Prima di C# 6,0 non è stato possibile scrivere codice simile al seguente: 

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
 
Una soluzione alternativa consisteva nel richiamare il costruttore predefinito: 

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

oppure per inizializzare il valore predefinito: 

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

Sfortunatamente, entrambi i metodi disabilitano in modo efficace l'analisi di assegnazione definita per i campi di istanza per il costruttore specificato. 

C# 6,0 attenua queste regole tenendo traccia delle assegnazioni alle proprietà automatiche dell'istanza come se fossero assegnazioni ai campi sottoposti a backup, quindi il codice all'inizio di questa sezione è ora valido. 
