---
ms.openlocfilehash: 63dfdfee9ea6c16e162f483aa1298feed297daef
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484699"
---
# <a name="conditional-ref-expressions"></a><span data-ttu-id="c5049-101">Espressioni di riferimento condizionale</span><span class="sxs-lookup"><span data-stu-id="c5049-101">Conditional ref expressions</span></span>

<span data-ttu-id="c5049-102">Il modello di associazione di una variabile ref a una o a un'altra espressione in modo condizionale C#non è attualmente esprimibile in.</span><span class="sxs-lookup"><span data-stu-id="c5049-102">The pattern of binding a ref variable to one or another expression conditionally is not currently expressible in C#.</span></span>

<span data-ttu-id="c5049-103">La soluzione alternativa tipica consiste nell'introdurre un metodo come:</span><span class="sxs-lookup"><span data-stu-id="c5049-103">The typical workaround is to introduce a method like:</span></span>

```csharp
ref T Choice(bool condition, ref T consequence, ref T alternative)
{
    if (condition)
    {
         return ref consequence;
    }
    else
    {
         return ref alternative;
    }
}
```

<span data-ttu-id="c5049-104">Si noti che questa non è una sostituzione esatta di una terna poiché tutti gli argomenti devono essere valutati nel sito di chiamata.</span><span class="sxs-lookup"><span data-stu-id="c5049-104">Note that this is not an exact replacement of a ternary since all arguments must be evaluated at the call site.</span></span>

<span data-ttu-id="c5049-105">Il codice seguente non funzionerà come previsto:</span><span class="sxs-lookup"><span data-stu-id="c5049-105">The following will not work as expected:</span></span>

```csharp
       // will crash with NRE because 'arr[0]' will be executed unconditionally
      ref var r = ref Choice(arr != null, ref arr[0], ref otherArr[0]);
```

<span data-ttu-id="c5049-106">La sintassi proposta sarà simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="c5049-106">The proposed syntax would look like:</span></span>

```csharp
     <condition> ? ref <consequence> : ref <alternative>;
```

<span data-ttu-id="c5049-107">Il tentativo precedente con "Choice" può essere scritto _correttamente_ usando il ternario Ref come:</span><span class="sxs-lookup"><span data-stu-id="c5049-107">The above attempt with "Choice" can be _correctly_ written using ref ternary as:</span></span>

```csharp
     ref var r = ref (arr != null ? ref arr[0]: ref otherArr[0]);
```

<span data-ttu-id="c5049-108">La differenza dalla scelta è che l'accesso alle espressioni di conseguenza e alternative viene eseguito in modo _realmente_ condizionale, quindi non viene visualizzato un arresto anomalo se ```arr == null```</span><span class="sxs-lookup"><span data-stu-id="c5049-108">The difference from Choice is that consequence and alternative expressions are accessed in a _truly_ conditional manner, so we do not see a crash if ```arr == null```</span></span>

<span data-ttu-id="c5049-109">Il riferimento ternario è semplicemente una terna, in cui sia l'alternativa che la conseguenza sono refs.</span><span class="sxs-lookup"><span data-stu-id="c5049-109">The ternary ref is just a ternary where both alternative and consequence are refs.</span></span> <span data-ttu-id="c5049-110">Richiede naturalmente che gli operandi di conseguenza/alternativi siano lvalue.</span><span class="sxs-lookup"><span data-stu-id="c5049-110">It will naturally require that consequence/alternative operands are LValues.</span></span> <span data-ttu-id="c5049-111">Sarà inoltre necessario che la conseguenza e l'alternativa dispongano di tipi convertibili tra loro identità.</span><span class="sxs-lookup"><span data-stu-id="c5049-111">It will also require that consequence and alternative have types that are identity convertible to each other.</span></span>

<span data-ttu-id="c5049-112">Il tipo dell'espressione verrà calcolato in modo analogo a quello della terna normale.</span><span class="sxs-lookup"><span data-stu-id="c5049-112">The type of the expression will be computed similarly to the one for the regular ternary.</span></span> <span data-ttu-id="c5049-113">I.E.</span><span class="sxs-lookup"><span data-stu-id="c5049-113">I.E.</span></span> <span data-ttu-id="c5049-114">Se la conseguenza e l'alternativa hanno un'identità convertibile, ma tipi diversi, verranno applicate le regole di Unione dei tipi esistenti.</span><span class="sxs-lookup"><span data-stu-id="c5049-114">in a case if consequence and alternative have identity convertible, but different types, the existing type-merging rules will apply.</span></span>

<span data-ttu-id="c5049-115">La modalità Safe-to-return verrà considerata conservativa dagli operandi condizionali.</span><span class="sxs-lookup"><span data-stu-id="c5049-115">Safe-to-return will be assumed conservatively from the conditional operands.</span></span> <span data-ttu-id="c5049-116">Se non è sicuro restituire l'intero elemento, non è sicuro restituire.</span><span class="sxs-lookup"><span data-stu-id="c5049-116">If either is unsafe to return the whole thing is unsafe to return.</span></span>

<span data-ttu-id="c5049-117">La terna di riferimento è un LValue e, di conseguenza, può essere passata/assegnata/restituita per riferimento;</span><span class="sxs-lookup"><span data-stu-id="c5049-117">Ref ternary is an LValue and as such it can be passed/assigned/returned by reference;</span></span>

```csharp
     // pass by reference
     foo(ref (arr != null ? ref arr[0]: ref otherArr[0]));

     // return by reference
     return ref (arr != null ? ref arr[0]: ref otherArr[0]);
```

<span data-ttu-id="c5049-118">Essendo un LValue, può anche essere assegnato a.</span><span class="sxs-lookup"><span data-stu-id="c5049-118">Being an LValue, it can also be assigned to.</span></span> 

```csharp
    // assign to
    (arr != null ? ref arr[0]: ref otherArr[0]) = 1;
```

<span data-ttu-id="c5049-119">Il ternario ref può essere usato anche in un contesto normale (non Ref).</span><span class="sxs-lookup"><span data-stu-id="c5049-119">Ref ternary can be used in a regular (not ref) context as well.</span></span> <span data-ttu-id="c5049-120">Anche se non sarebbe comune, dal momento che era sufficiente usare una terna normale.</span><span class="sxs-lookup"><span data-stu-id="c5049-120">Although it would not be common since you could as well just use a regular ternary.</span></span>

```csharp
     int x = (arr != null ? ref arr[0]: ref otherArr[0]);
```


___

<span data-ttu-id="c5049-121">Note sull'implementazione:</span><span class="sxs-lookup"><span data-stu-id="c5049-121">Implementation notes:</span></span> 

<span data-ttu-id="c5049-122">La complessità dell'implementazione sembrerebbe la dimensione di una correzione di bug da moderato a grande.</span><span class="sxs-lookup"><span data-stu-id="c5049-122">The complexity of the implementation would seem to be the size of a moderate-to-large bug fix.</span></span> <span data-ttu-id="c5049-123">-I. E non sono molto costosi.</span><span class="sxs-lookup"><span data-stu-id="c5049-123">- I.E not very expensive.</span></span>
<span data-ttu-id="c5049-124">Non sono necessarie modifiche alla sintassi o all'analisi.</span><span class="sxs-lookup"><span data-stu-id="c5049-124">I do not think we need any changes to the syntax or parsing.</span></span>
<span data-ttu-id="c5049-125">Non si verifica alcun effetto sui metadati o sull'interoperabilità.</span><span class="sxs-lookup"><span data-stu-id="c5049-125">There is no effect on metadata or interop.</span></span> <span data-ttu-id="c5049-126">La funzionalità è completamente basata su espressioni.</span><span class="sxs-lookup"><span data-stu-id="c5049-126">The feature is completely expression based.</span></span>
<span data-ttu-id="c5049-127">Nessun effetto sul debug o sul PDB</span><span class="sxs-lookup"><span data-stu-id="c5049-127">No effect on debugging/PDB either</span></span>
