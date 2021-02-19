---
ms.openlocfilehash: 22d29c5186db5be559647a0c72cc749de4b7890c
ms.sourcegitcommit: 1f5b1dc19d21038b59bfce169fd49e121a5a1f4e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101853"
---
# <a name="global-using-directive"></a><span data-ttu-id="ce42e-101">Direttiva using globale</span><span class="sxs-lookup"><span data-stu-id="ce42e-101">Global Using Directive</span></span>

<span data-ttu-id="ce42e-102">La sintassi per una direttiva using viene estesa con una `global` parola chiave facoltativa che può seguire la `using` parola chiave:</span><span class="sxs-lookup"><span data-stu-id="ce42e-102">Syntax for a using directive is extended with an optional `global` keyword that can follow the `using` keyword:</span></span>
```antlr
using_directive
  : 'using' 'global'? ('static' | name_equals)? name ';'
  ;
```

- <span data-ttu-id="ce42e-103">Le direttive using globali sono consentite solo a livello di unità di compilazione (non possono essere usate all'interno di una dichiarazione dello spazio dei nomi).</span><span class="sxs-lookup"><span data-stu-id="ce42e-103">Global Using Directives are allowed only on the Compilation Unit level (cannot be used inside a namespace declaration).</span></span>
- <span data-ttu-id="ce42e-104">Le direttive using globali, se presenti, devono precedere le direttive using non globali.</span><span class="sxs-lookup"><span data-stu-id="ce42e-104">Global Using directives, if any, must precede any non-global using directives.</span></span> 
- <span data-ttu-id="ce42e-105">L'ambito di una direttiva using globale si estende sulle dichiarazioni dei membri dello spazio dei nomi e sulle direttive using non globali di tutte le unità di compilazione all'interno del programma.</span><span class="sxs-lookup"><span data-stu-id="ce42e-105">The scope of a Global Using Directive extends over the namespace member declarations and non-global using directives of all compilation units within the program.</span></span>
<span data-ttu-id="ce42e-106">In particolare, l'ambito di una direttiva using globale non include altre direttive using globali.</span><span class="sxs-lookup"><span data-stu-id="ce42e-106">The scope of a Global Using Directive specifically does not include other Global Using Directives.</span></span> <span data-ttu-id="ce42e-107">Pertanto, le direttive using globali peer o quelle di un'unità di compilazione diversa non interessano tra loro e l'ordine in cui vengono scritte non è significativo.</span><span class="sxs-lookup"><span data-stu-id="ce42e-107">Thus, peer Global Using Directives or those from a different compilation unit do not affect each other, and the order in which they are written is insignificant.</span></span>
