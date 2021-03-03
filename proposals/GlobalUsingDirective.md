---
ms.openlocfilehash: e752cfc2356bbbad34f23b48791058cde4968765
ms.sourcegitcommit: f0590512a5b191faa4ae591a89bbdd71cec819a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2021
ms.locfileid: "101635343"
---
# <a name="global-using-directive"></a>Direttiva using globale

La sintassi per una direttiva using viene estesa con una `global` parola chiave facoltativa che può precedere la `using` parola chiave:
```antlr
using_directive
  : 'global'? 'using' ('static' | name_equals)? name ';'
  ;
```

- Le direttive using globali sono consentite solo a livello di unità di compilazione (non possono essere usate all'interno di una dichiarazione dello spazio dei nomi).
- Le direttive using globali, se presenti, devono precedere le direttive using non globali. 
- L'ambito di una direttiva using globale si estende sulle dichiarazioni dei membri dello spazio dei nomi e sulle direttive using non globali di tutte le unità di compilazione all'interno del programma.
In particolare, l'ambito di una direttiva using globale non include altre direttive using globali. Pertanto, le direttive using globali peer o quelle di un'unità di compilazione diversa non interessano tra loro e l'ordine in cui vengono scritte non è significativo.
