---
ms.openlocfilehash: 0c8bc2b5072ea7f86189b41a1cdbf2a449661b05
ms.sourcegitcommit: 33a60a1db1d42d21d959acfeb127e647150173aa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2020
ms.locfileid: "79509507"
---
# <a name="attributes-on-local-functions"></a>Attributi nelle funzioni locali

## <a name="attributes"></a>Attributi

Le dichiarazioni di funzione locali ora possono avere [attributi](../spec/attributes.md). I parametri e i parametri di tipo nelle funzioni locali possono avere anche attributi.

Gli attributi con un significato specificato quando vengono applicati a un metodo, i relativi parametri o i parametri di tipo avranno lo stesso significato quando vengono applicati rispettivamente a una funzione locale, ai relativi parametri o ai parametri di tipo.

Una funzione locale può essere resa condizionale nello stesso senso di un [metodo condizionale](../spec/attributes.md#the-conditional-attribute) , decorando l'oggetto con un `[ConditionalAttribute]`. È inoltre necessario `static`una funzione locale condizionale. Tutte le restrizioni sui metodi condizionali si applicano anche alle funzioni locali condizionali, incluso il tipo restituito deve essere `void`.

## <a name="extern"></a>Extern

Il modificatore `extern` è ora consentito nelle funzioni locali. In questo modo la funzione locale è esterna allo stesso senso di un [metodo esterno](../spec/classes.md#external-methods).

Analogamente a un metodo esterno, il *corpo della funzione locale* di una funzione locale esterna deve essere un punto e virgola. Un *corpo della funzione locale* con punto e virgola è consentito solo in una funzione locale esterna. 

È inoltre necessario `static`una funzione locale esterna.

## <a name="syntax"></a>Sintassi

La [grammatica delle funzioni locali](csharp-7.0/local-functions.md#syntax-grammar) viene modificata come segue:
```
local-function-header
    : attributes? local-function-modifiers? return-type identifier type-parameter-list?
        ( formal-parameter-list? ) type-parameter-constraints-clauses
    ;

local-function-modifiers
    : (async | unsafe | static | extern)*
    ;

local-function-body
    : block
    | arrow-expression-body
    | ';'
    ;
```
