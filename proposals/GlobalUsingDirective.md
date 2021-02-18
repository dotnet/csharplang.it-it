---
ms.openlocfilehash: 22d29c5186db5be559647a0c72cc749de4b7890c
ms.sourcegitcommit: 1f5b1dc19d21038b59bfce169fd49e121a5a1f4e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101853"
---
# <a name="global-using-directive"></a>Direttiva using globale

La sintassi per una direttiva using viene estesa con una `global` parola chiave facoltativa che può seguire la `using` parola chiave:
```antlr
using_directive
  : 'using' 'global'? ('static' | name_equals)? name ';'
  ;
```

- Le direttive using globali sono consentite solo a livello di unità di compilazione (non possono essere usate all'interno di una dichiarazione dello spazio dei nomi).
- Le direttive using globali, se presenti, devono precedere le direttive using non globali. 
- L'ambito di una direttiva using globale si estende sulle dichiarazioni dei membri dello spazio dei nomi e sulle direttive using non globali di tutte le unità di compilazione all'interno del programma.
In particolare, l'ambito di una direttiva using globale non include altre direttive using globali. Pertanto, le direttive using globali peer o quelle di un'unità di compilazione diversa non interessano tra loro e l'ordine in cui vengono scritte non è significativo.
