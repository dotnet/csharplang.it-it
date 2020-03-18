---
ms.openlocfilehash: 36f3e6204d12c2569b3a55f3a47f58337e8a08e4
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484629"
---
# <a name="indexing-fixed-fields-should-not-require-pinning-regardless-of-the-movableunmovable-context"></a><span data-ttu-id="03a1f-101">L'indicizzazione di campi `fixed` non deve richiedere il blocco indipendentemente dal contesto mobile/unmovibile.</span><span class="sxs-lookup"><span data-stu-id="03a1f-101">Indexing `fixed` fields should not require pinning regardless of the movable/unmovable context.</span></span> #

<span data-ttu-id="03a1f-102">La modifica ha la dimensione di una correzione di bug.</span><span class="sxs-lookup"><span data-stu-id="03a1f-102">The change has the size of a bug fix.</span></span> <span data-ttu-id="03a1f-103">Può trovarsi in 7,3 e non è in conflitto con qualsiasi direzione.</span><span class="sxs-lookup"><span data-stu-id="03a1f-103">It can be in 7.3 and does not conflict with whatever direction we take further.</span></span>
<span data-ttu-id="03a1f-104">Questa modifica consente solo di consentire il funzionamento dello scenario seguente anche se `s` è mobile.</span><span class="sxs-lookup"><span data-stu-id="03a1f-104">This change is only about allowing the following scenario to work even though `s` is moveable.</span></span> <span data-ttu-id="03a1f-105">È già valido quando `s` non è mobile.</span><span class="sxs-lookup"><span data-stu-id="03a1f-105">It is already valid when `s` is not moveable.</span></span> 

<span data-ttu-id="03a1f-106">Nota: in entrambi i casi, è ancora necessario `unsafe` contesto.</span><span class="sxs-lookup"><span data-stu-id="03a1f-106">NOTE: in either case, it still requires `unsafe` context.</span></span> <span data-ttu-id="03a1f-107">È possibile leggere dati non inizializzati o persino fuori intervallo.</span><span class="sxs-lookup"><span data-stu-id="03a1f-107">It is possible to read uninitialized data or even out of range.</span></span> <span data-ttu-id="03a1f-108">Che non è in corso di modifica.</span><span class="sxs-lookup"><span data-stu-id="03a1f-108">That is not changing.</span></span>

```csharp
unsafe struct S
{
    public fixed int myFixedField[10];
}

class Program
{
    static S s;

    unsafe static void Main()
    {
        int p = s.myFixedField[5]; // indexing fixed-size array fields would be ok
    }
}
```

<span data-ttu-id="03a1f-109">La principale "sfida" che ho visto qui è come spiegare il relax nella specifica. In particolare, dal momento che le operazioni seguenti dovrebbero comunque avere un blocco.</span><span class="sxs-lookup"><span data-stu-id="03a1f-109">The main “challenge” that I see here is how to explain the relaxation in the spec. In particular, since the following would still need pinning.</span></span> <span data-ttu-id="03a1f-110">Poiché `s` è mobile e il campo viene usato in modo esplicito come puntatore)</span><span class="sxs-lookup"><span data-stu-id="03a1f-110">(because `s` is moveable and we explicitly use the field as a pointer)</span></span>

```csharp
unsafe struct S
{
    public fixed int myFixedField[10];
}

class Program
{
    static S s;

    unsafe static void Main()
    {
        int* ptr = s.myFixedField; // taking a pointer explicitly still requires pinning.
        int p = ptr[5];
    }
}
```

<span data-ttu-id="03a1f-111">Uno dei motivi per cui è necessario aggiungere la destinazione quando è mobile è l'artefatto della strategia di generazione del codice, ovvero si esegue sempre la conversione in un puntatore non gestito e quindi si impone l'aggiunta dell'utente tramite `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="03a1f-111">One reason why we require pinning of the target when it is movable is the artifact of our code generation strategy, - we always convert to an unmanaged pointer and thus force the user to pin via `fixed` statement.</span></span> <span data-ttu-id="03a1f-112">Tuttavia, la conversione in non gestita non è necessaria quando si esegue l'indicizzazione.</span><span class="sxs-lookup"><span data-stu-id="03a1f-112">However, conversion to unmanaged is unnecessary when doing indexing.</span></span> <span data-ttu-id="03a1f-113">La stessa matematica del puntatore non sicuro è ugualmente applicabile quando il ricevitore è sotto forma di puntatore gestito.</span><span class="sxs-lookup"><span data-stu-id="03a1f-113">The same unsafe pointer math is equally applicable when we have the receiver in the form of a managed pointer.</span></span> <span data-ttu-id="03a1f-114">In tal caso, il riferimento intermedio è gestito (GC-tracked) e il blocco non è necessario.</span><span class="sxs-lookup"><span data-stu-id="03a1f-114">If we do that, then the intermediate ref is managed (GC-tracked) and the pinning is unnecessary.</span></span>

<span data-ttu-id="03a1f-115">Il https://github.com/dotnet/roslyn/pull/24966 delle modifiche è un prototipo di richiesta pull che attenua questo requisito.</span><span class="sxs-lookup"><span data-stu-id="03a1f-115">The change https://github.com/dotnet/roslyn/pull/24966 is a prototype PR that relaxes this requirement.</span></span>
