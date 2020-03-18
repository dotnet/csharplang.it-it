---
ms.openlocfilehash: b8d975a8fc95af6980feaae6be35160646fe2cd2
ms.sourcegitcommit: 32abf01f2e43be29114bfcf8f8ed1cc4e3eaded2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2019
ms.locfileid: "79485140"
---
# <a name="permit-stackalloc-in-nested-contexts"></a><span data-ttu-id="1a242-101">Consenti `stackalloc` nei contesti annidati</span><span class="sxs-lookup"><span data-stu-id="1a242-101">Permit `stackalloc` in nested contexts</span></span>

### <a name="stack-allocation"></a><span data-ttu-id="1a242-102">Allocazione dello stack</span><span class="sxs-lookup"><span data-stu-id="1a242-102">Stack allocation</span></span>

<span data-ttu-id="1a242-103">Si modifica l' [*allocazione dello stack*](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#stack-allocation) della C# sezione della specifica del linguaggio per attenuare le posizioni in cui è possibile che venga visualizzata un'espressione `stackalloc`.</span><span class="sxs-lookup"><span data-stu-id="1a242-103">We modify the section [*Stack allocation*](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#stack-allocation) of the C# language specification to relax the places when a `stackalloc` expression may appear.</span></span> <span data-ttu-id="1a242-104">Elimina</span><span class="sxs-lookup"><span data-stu-id="1a242-104">We delete</span></span>

``` antlr
local_variable_initializer_unsafe
    : stackalloc_initializer
    ;

stackalloc_initializer
    : 'stackalloc' unmanaged_type '[' expression ']'
    ;
```

<span data-ttu-id="1a242-105">e sostituirli con</span><span class="sxs-lookup"><span data-stu-id="1a242-105">and replace them with</span></span>

``` antlr
primary_no_array_creation_expression
    : stackalloc_initializer
    ;

stackalloc_initializer
    : 'stackalloc' unmanaged_type '[' expression? ']' array_initializer?
    | 'stackalloc' '[' expression? ']' array_initializer
    ;
```

<span data-ttu-id="1a242-106">Si noti che l'aggiunta di *array_initializer* un array_initializer *stackalloc_initializer* (e l'espressione dell'indice facoltativa) è un' [estensione C# in 7,3](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.3/stackalloc-array-initializers.md) e non è descritta qui.</span><span class="sxs-lookup"><span data-stu-id="1a242-106">Note that the addition of an *array_initializer* to *stackalloc_initializer* (and making the index expression optional) was an [extension in C# 7.3](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.3/stackalloc-array-initializers.md) and is not described here.</span></span>

<span data-ttu-id="1a242-107">Il *tipo di elemento* dell'espressione `stackalloc` è il *unmanaged_type* denominato nell'espressione stackalloc, se presente, o il tipo comune tra gli elementi del *array_initializer* in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="1a242-107">The *element type* of the `stackalloc` expression is the *unmanaged_type* named in the stackalloc expression, if any, or the common type among the elements of the *array_initializer* otherwise.</span></span>

<span data-ttu-id="1a242-108">Il tipo di *stackalloc_initializer* con il *tipo di elemento* `K` dipende dal contesto sintattico:</span><span class="sxs-lookup"><span data-stu-id="1a242-108">The type of the *stackalloc_initializer* with *element type* `K` depends on its syntactic context:</span></span>
- <span data-ttu-id="1a242-109">Se il *stackalloc_initializer* viene visualizzato direttamente come *local_variable_initializer* di un'istruzione *local_variable_declaration* o di un *for_initializer*, il tipo è `K*`.</span><span class="sxs-lookup"><span data-stu-id="1a242-109">If the *stackalloc_initializer* appears directly as the *local_variable_initializer* of a *local_variable_declaration* statement or a *for_initializer*, then its type is `K*`.</span></span>
- <span data-ttu-id="1a242-110">In caso contrario, il tipo è `System.Span<K>`.</span><span class="sxs-lookup"><span data-stu-id="1a242-110">Otherwise its type is `System.Span<K>`.</span></span>

### <a name="stackalloc-conversion"></a><span data-ttu-id="1a242-111">Conversione stackalloc</span><span class="sxs-lookup"><span data-stu-id="1a242-111">Stackalloc Conversion</span></span>

<span data-ttu-id="1a242-112">La *conversione stackalloc* è una nuova conversione implicita incorporata da Expression.</span><span class="sxs-lookup"><span data-stu-id="1a242-112">The *stackalloc conversion* is a new built-in implicit conversion from expression.</span></span> <span data-ttu-id="1a242-113">Quando il tipo di un *stackalloc_initializer* viene `K*`, esiste una conversione implicita *stackalloc* dalla *stackalloc_initializer* al tipo `System.Span<K>`.</span><span class="sxs-lookup"><span data-stu-id="1a242-113">When the type of a *stackalloc_initializer* is `K*`, there is an implicit *stackalloc conversion* from the *stackalloc_initializer* to the type `System.Span<K>`.</span></span>
