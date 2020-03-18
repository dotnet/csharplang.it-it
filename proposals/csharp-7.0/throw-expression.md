---
ms.openlocfilehash: 2532a24e867930d2f27614f19c77585dbce80dfa
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484545"
---
# <a name="throw-expression"></a><span data-ttu-id="fda1f-101">Espressione throw</span><span class="sxs-lookup"><span data-stu-id="fda1f-101">Throw expression</span></span>

<span data-ttu-id="fda1f-102">Estendiamo il set di form di espressione da includere</span><span class="sxs-lookup"><span data-stu-id="fda1f-102">We extend the set of expression forms to include</span></span>

```antlr
throw_expression
    : 'throw' null_coalescing_expression
    ;

null_coalescing_expression
    : throw_expression
    ;
```

<span data-ttu-id="fda1f-103">Di seguito sono riportate le regole di tipo:</span><span class="sxs-lookup"><span data-stu-id="fda1f-103">The type rules are as follows:</span></span>

- <span data-ttu-id="fda1f-104">Un *throw_expression* non dispone di alcun tipo.</span><span class="sxs-lookup"><span data-stu-id="fda1f-104">A *throw_expression* has no type.</span></span>
- <span data-ttu-id="fda1f-105">Un *throw_expression* è convertibile in ogni tipo mediante una conversione implicita.</span><span class="sxs-lookup"><span data-stu-id="fda1f-105">A *throw_expression* is convertible to every type by an implicit conversion.</span></span>

<span data-ttu-id="fda1f-106">Un' *espressione throw* genera il valore prodotto dalla valutazione della *null_coalescing_expression*, che deve indicare un valore del tipo di classe `System.Exception`, di un tipo di classe che deriva da `System.Exception` o di un tipo di parametro di tipo che ha `System.Exception` (o una sottoclasse) come classe di base efficace.</span><span class="sxs-lookup"><span data-stu-id="fda1f-106">A *throw expression* throws the value produced by evaluating the *null_coalescing_expression*, which must denote a value of the class type `System.Exception`, of a class type that derives from `System.Exception` or of a type parameter type that has `System.Exception` (or a subclass thereof) as its effective base class.</span></span> <span data-ttu-id="fda1f-107">Se la valutazione dell'espressione produce `null`, viene invece generata una `System.NullReferenceException`.</span><span class="sxs-lookup"><span data-stu-id="fda1f-107">If evaluation of the expression produces `null`, a `System.NullReferenceException` is thrown instead.</span></span>

<span data-ttu-id="fda1f-108">Il comportamento in fase di esecuzione della valutazione di un' *espressione throw* è identico a quello [specificato per un' *istruzione throw*](../../spec/statements.md#the-throw-statement).</span><span class="sxs-lookup"><span data-stu-id="fda1f-108">The behavior at runtime of the evaluation of a *throw expression* is the same [as specified for a *throw statement*](../../spec/statements.md#the-throw-statement).</span></span>

<span data-ttu-id="fda1f-109">Di seguito sono riportate le regole di analisi del flusso:</span><span class="sxs-lookup"><span data-stu-id="fda1f-109">The flow-analysis rules are as follows:</span></span>

- <span data-ttu-id="fda1f-110">Per ogni variabile *v*, *v* viene definitivamente assegnata prima del *null_coalescing_expression* di un *throw_expression* IFF viene assegnata definitivamente prima del *throw_expression*.</span><span class="sxs-lookup"><span data-stu-id="fda1f-110">For every variable *v*, *v* is definitely assigned before the *null_coalescing_expression* of a *throw_expression* iff it is definitely assigned before the *throw_expression*.</span></span>
- <span data-ttu-id="fda1f-111">Per ogni variabile *v*, *v* viene assegnato definitivamente dopo *throw_expression*.</span><span class="sxs-lookup"><span data-stu-id="fda1f-111">For every variable *v*, *v* is definitely assigned after *throw_expression*.</span></span>

<span data-ttu-id="fda1f-112">Un' *espressione throw* è consentita solo nei contesti sintattici seguenti:</span><span class="sxs-lookup"><span data-stu-id="fda1f-112">A *throw expression* is permitted in only the following syntactic contexts:</span></span>
- <span data-ttu-id="fda1f-113">Come secondo o terzo operando di un operatore condizionale ternario `?:`</span><span class="sxs-lookup"><span data-stu-id="fda1f-113">As the second or third operand of a ternary conditional operator `?:`</span></span>
- <span data-ttu-id="fda1f-114">Come secondo operando di un operatore di Unione null `??`</span><span class="sxs-lookup"><span data-stu-id="fda1f-114">As the second operand of a null coalescing operator `??`</span></span>
- <span data-ttu-id="fda1f-115">Come corpo di un'espressione lambda o di un metodo con corpo di espressione.</span><span class="sxs-lookup"><span data-stu-id="fda1f-115">As the body of an expression-bodied lambda or method.</span></span>
