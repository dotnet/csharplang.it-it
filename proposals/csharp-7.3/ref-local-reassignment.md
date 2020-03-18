---
ms.openlocfilehash: c1a77d9337ecd16f5ea1c30d18f6422552b0dfb2
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484601"
---
# <a name="ref-local-reassignment"></a><span data-ttu-id="7a0ad-101">Riassegnazione locale Ref</span><span class="sxs-lookup"><span data-stu-id="7a0ad-101">Ref Local Reassignment</span></span>

<span data-ttu-id="7a0ad-102">In C# 7,3 è stato aggiunto il supporto per riassociare il referente di una variabile locale ref o di un parametro ref.</span><span class="sxs-lookup"><span data-stu-id="7a0ad-102">In C# 7.3, we add support for rebinding the referent of a ref local variable or a ref parameter.</span></span>

<span data-ttu-id="7a0ad-103">Al set di `assignment_operator`s viene aggiunto il codice seguente.</span><span class="sxs-lookup"><span data-stu-id="7a0ad-103">We add the following to the set of `assignment_operator`s.</span></span>

```antlr
assignment_operator
    : '=' 'ref'
    ;
```

<span data-ttu-id="7a0ad-104">L'operatore `=ref` viene chiamato ***operatore di assegnazione Ref***.</span><span class="sxs-lookup"><span data-stu-id="7a0ad-104">The `=ref` operator is called the ***ref assignment operator***.</span></span> <span data-ttu-id="7a0ad-105">Non è un *operatore di assegnazione composto*.</span><span class="sxs-lookup"><span data-stu-id="7a0ad-105">It is not a *compound assignment operator*.</span></span> <span data-ttu-id="7a0ad-106">L'operando sinistro deve essere un'espressione associata a una variabile locale Ref, un parametro ref (diverso da `this`) o un parametro out.</span><span class="sxs-lookup"><span data-stu-id="7a0ad-106">The left operand must be an expression that binds to a ref local variable, a ref parameter (other than `this`), or an out parameter.</span></span> <span data-ttu-id="7a0ad-107">L'operando destro deve essere un'espressione che restituisce un lvalue che designa un valore dello stesso tipo dell'operando sinistro.</span><span class="sxs-lookup"><span data-stu-id="7a0ad-107">The right operand must be an expression that yields an lvalue designating a value of the same type as the left operand.</span></span>

<span data-ttu-id="7a0ad-108">L'operando destro deve essere assegnato in modo sicuro al punto dell'assegnazione Ref.</span><span class="sxs-lookup"><span data-stu-id="7a0ad-108">The right operand must be definitely assigned at the point of the ref assignment.</span></span>

<span data-ttu-id="7a0ad-109">Quando l'operando sinistro viene associato a un parametro di `out`, è un errore se il parametro `out` non è stato assegnato definitivamente all'inizio dell'operatore di assegnazione di riferimento.</span><span class="sxs-lookup"><span data-stu-id="7a0ad-109">When the left operand binds to an `out` parameter, it is an error if that `out` parameter has not been definitely assigned at the beginning of the ref assignment operator.</span></span>

<span data-ttu-id="7a0ad-110">Se l'operando sinistro è un riferimento scrivibile (ovvero definisce un valore diverso da un parametro `ref readonly` local o `in`), l'operando destro deve essere un lvalue scrivibile.</span><span class="sxs-lookup"><span data-stu-id="7a0ad-110">If the left operand is a writeable ref (i.e. it designates anything other than a `ref readonly` local or  `in` parameter), then the right operand must be a writeable lvalue.</span></span>

<span data-ttu-id="7a0ad-111">L'operatore di assegnazione Ref restituisce un lvalue del tipo assegnato.</span><span class="sxs-lookup"><span data-stu-id="7a0ad-111">The ref assignment operator yields an lvalue of the assigned type.</span></span> <span data-ttu-id="7a0ad-112">È scrivibile se l'operando sinistro è scrivibile, ovvero non `ref readonly` o `in`.</span><span class="sxs-lookup"><span data-stu-id="7a0ad-112">It is writeable if the left operand is writeable (i.e. not `ref readonly` or `in`).</span></span>

<span data-ttu-id="7a0ad-113">Le regole di sicurezza per questo operatore sono:</span><span class="sxs-lookup"><span data-stu-id="7a0ad-113">The safety rules for this operator are:</span></span>

- <span data-ttu-id="7a0ad-114">Per un `e1 = ref e2`di riassegnazione Ref, il valore di *riferimento-safe-to-escape* di `e2` deve essere almeno uguale a un ambito come *ref-safe-to-escape* di `e1`.</span><span class="sxs-lookup"><span data-stu-id="7a0ad-114">For a ref reassignment `e1 = ref e2`, the *ref-safe-to-escape* of `e2` must be at least as wide a scope as the *ref-safe-to-escape* of `e1`.</span></span>

<span data-ttu-id="7a0ad-115">Dove *ref-safe-to-escape* è definito in [sicurezza per i tipi simili a Ref](../csharp-7.2/span-safety.md)</span><span class="sxs-lookup"><span data-stu-id="7a0ad-115">Where *ref-safe-to-escape* is defined in [Safety for ref-like types](../csharp-7.2/span-safety.md)</span></span>
