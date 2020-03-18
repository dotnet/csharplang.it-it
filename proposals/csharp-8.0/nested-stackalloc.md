---
ms.openlocfilehash: b8d975a8fc95af6980feaae6be35160646fe2cd2
ms.sourcegitcommit: 32abf01f2e43be29114bfcf8f8ed1cc4e3eaded2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2019
ms.locfileid: "79485140"
---
# <a name="permit-stackalloc-in-nested-contexts"></a>Consenti `stackalloc` nei contesti annidati

### <a name="stack-allocation"></a>Allocazione dello stack

Si modifica l' [*allocazione dello stack*](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#stack-allocation) della C# sezione della specifica del linguaggio per attenuare le posizioni in cui è possibile che venga visualizzata un'espressione `stackalloc`. Elimina

``` antlr
local_variable_initializer_unsafe
    : stackalloc_initializer
    ;

stackalloc_initializer
    : 'stackalloc' unmanaged_type '[' expression ']'
    ;
```

e sostituirli con

``` antlr
primary_no_array_creation_expression
    : stackalloc_initializer
    ;

stackalloc_initializer
    : 'stackalloc' unmanaged_type '[' expression? ']' array_initializer?
    | 'stackalloc' '[' expression? ']' array_initializer
    ;
```

Si noti che l'aggiunta di *array_initializer* un array_initializer *stackalloc_initializer* (e l'espressione dell'indice facoltativa) è un' [estensione C# in 7,3](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.3/stackalloc-array-initializers.md) e non è descritta qui.

Il *tipo di elemento* dell'espressione `stackalloc` è il *unmanaged_type* denominato nell'espressione stackalloc, se presente, o il tipo comune tra gli elementi del *array_initializer* in caso contrario.

Il tipo di *stackalloc_initializer* con il *tipo di elemento* `K` dipende dal contesto sintattico:
- Se il *stackalloc_initializer* viene visualizzato direttamente come *local_variable_initializer* di un'istruzione *local_variable_declaration* o di un *for_initializer*, il tipo è `K*`.
- In caso contrario, il tipo è `System.Span<K>`.

### <a name="stackalloc-conversion"></a>Conversione stackalloc

La *conversione stackalloc* è una nuova conversione implicita incorporata da Expression. Quando il tipo di un *stackalloc_initializer* viene `K*`, esiste una conversione implicita *stackalloc* dalla *stackalloc_initializer* al tipo `System.Span<K>`.
