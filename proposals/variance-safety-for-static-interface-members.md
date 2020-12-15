---
ms.openlocfilehash: f1ab0e063cc8b7a44c70ea1e52027cda30e064b0
ms.sourcegitcommit: efac0d1e909d222dce5b18b10ae0d0b1d309b757
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97502849"
---
# <a name="variance-safety-for-static-interface-members"></a><span data-ttu-id="f0028-101">Sicurezza della varianza per i membri dell'interfaccia statica</span><span class="sxs-lookup"><span data-stu-id="f0028-101">Variance Safety for static interface members</span></span>

## <a name="summary"></a><span data-ttu-id="f0028-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="f0028-102">Summary</span></span>

<span data-ttu-id="f0028-103">Consente ai membri statici non virtuali nelle interfacce di trattare i parametri di tipo nelle rispettive dichiarazioni come invarianti, indipendentemente dalla varianza dichiarata.</span><span class="sxs-lookup"><span data-stu-id="f0028-103">Allow static, non-virtual members in interfaces to treat type parameters in their declarations as invariant, regardless of their declared variance.</span></span>

## <a name="motivation"></a><span data-ttu-id="f0028-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="f0028-104">Motivation</span></span>


- https://github.com/dotnet/csharplang/issues/3275
- https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-06-24.md#interface-static-member-variance

<span data-ttu-id="f0028-105">È stata considerata la varianza nei `static` membri dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="f0028-105">We considered variance in `static` interface members.</span></span> <span data-ttu-id="f0028-106">Attualmente, per i parametri di tipo covariante usati in questi membri, devono seguire le regole standard complete della varianza, causando un'incoerenza con il modo in cui i `static` campi vengono gestiti rispetto alle `static` proprietà o ai metodi:</span><span class="sxs-lookup"><span data-stu-id="f0028-106">Today, for co/contravariant type parameters used in these members, they must follow the full standard rules of variance, leading to some inconsistency with the way that `static` fields are treated vs `static` properties or methods:</span></span>

```cs
public interface I<out T>
{
    static Task<T> F = Task.FromResult(default(T)); // No problem
    static Task<T> P => Task.FromResult(default(T));   //CS1961
    static Task<T> M() => Task.FromResult(default(T));    //CS1961
    static event EventHandler<T> E; // CS1961
}
```

<span data-ttu-id="f0028-107">Poiché questi membri sono `static` e non virtuali, non sono presenti problemi di sicurezza: non è possibile derivare un membro Looser/più limitato in qualche modo, digitando l'interfaccia ed eseguendo l'override del membro.</span><span class="sxs-lookup"><span data-stu-id="f0028-107">Because these members are `static` and non-virtual, there aren't any safety issues here: you can't derive a looser/more restricted member in some fashion by subtyping the interface and overriding the member.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="f0028-108">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="f0028-108">Detailed Design</span></span>

<span data-ttu-id="f0028-109">Ecco il contenuto proposto per la sezione relativa alla sicurezza Vaiance della specifica del linguaggio ( https://github.com/dotnet/csharplang/blob/master/spec/interfaces.md#variance-safety) .</span><span class="sxs-lookup"><span data-stu-id="f0028-109">Here is the proposed content for Vaiance Safety section of the language specification (https://github.com/dotnet/csharplang/blob/master/spec/interfaces.md#variance-safety).</span></span>
<span data-ttu-id="f0028-110">La modifica è l'aggiunta di "*queste restrizioni non si applicano a ocurrances di tipi nelle dichiarazioni di membri statici".*</span><span class="sxs-lookup"><span data-stu-id="f0028-110">The change is the addition of "*These restrictions do not apply to ocurrances of types within declarations of static members.*"</span></span> <span data-ttu-id="f0028-111">frase all'inizio della sezione.</span><span class="sxs-lookup"><span data-stu-id="f0028-111">sentence at the beginning of the section.</span></span> 

### <a name="variance-safety"></a><span data-ttu-id="f0028-112">Sicurezza della varianza</span><span class="sxs-lookup"><span data-stu-id="f0028-112">Variance safety</span></span>

<span data-ttu-id="f0028-113">L'occorrenza delle annotazioni di varianza nell'elenco dei parametri di tipo di un tipo limita le posizioni in cui i tipi possono essere presenti all'interno della dichiarazione del tipo.</span><span class="sxs-lookup"><span data-stu-id="f0028-113">The occurrence of variance annotations in the type parameter list of a type restricts the places where types can occur within the type declaration.</span></span>
<span data-ttu-id="f0028-114">*Queste restrizioni non si applicano a ocurrances di tipi nelle dichiarazioni di membri statici.*</span><span class="sxs-lookup"><span data-stu-id="f0028-114">*These restrictions do not apply to ocurrances of types within declarations of static members.*</span></span>

<span data-ttu-id="f0028-115">Un tipo `T` è \***output-unsafe** _ se uno degli elementi seguenti include:</span><span class="sxs-lookup"><span data-stu-id="f0028-115">A type `T` is \***output-unsafe** _ if one of the following holds:</span></span>

<span data-ttu-id="f0028-116">_  `T` è un parametro di tipo controvariante</span><span class="sxs-lookup"><span data-stu-id="f0028-116">_  `T` is a contravariant type parameter</span></span>
*  <span data-ttu-id="f0028-117">`T` è un tipo di matrice con un tipo di elemento non sicuro di output</span><span class="sxs-lookup"><span data-stu-id="f0028-117">`T` is an array type with an output-unsafe element type</span></span>
*  <span data-ttu-id="f0028-118">`T` è un tipo di interfaccia o delegato `S<A1,...,Ak>` costruito da un tipo generico `S<X1,...,Xk>` in cui per almeno uno `Ai` dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="f0028-118">`T` is an interface or delegate type `S<A1,...,Ak>` constructed from a generic type `S<X1,...,Xk>` where for at least one `Ai` one of the following holds:</span></span>
   * <span data-ttu-id="f0028-119">`Xi` è covariante o invariante e non `Ai` è sicuro per l'output.</span><span class="sxs-lookup"><span data-stu-id="f0028-119">`Xi` is covariant or invariant and `Ai` is output-unsafe.</span></span>
   * <span data-ttu-id="f0028-120">`Xi` è controvariante o invariante ed `Ai` è indipendente dall'input.</span><span class="sxs-lookup"><span data-stu-id="f0028-120">`Xi` is contravariant or invariant and `Ai` is input-safe.</span></span>
   
<span data-ttu-id="f0028-121">Un tipo `T` è \***input-unsafe** _ se uno degli elementi seguenti include:</span><span class="sxs-lookup"><span data-stu-id="f0028-121">A type `T` is \***input-unsafe** _ if one of the following holds:</span></span>

<span data-ttu-id="f0028-122">_  `T` è un parametro di tipo covariante</span><span class="sxs-lookup"><span data-stu-id="f0028-122">_  `T` is a covariant type parameter</span></span>
*  <span data-ttu-id="f0028-123">`T` è un tipo di matrice con un tipo di elemento non sicuro di input</span><span class="sxs-lookup"><span data-stu-id="f0028-123">`T` is an array type with an input-unsafe element type</span></span>
*  <span data-ttu-id="f0028-124">`T` è un tipo di interfaccia o delegato `S<A1,...,Ak>` costruito da un tipo generico `S<X1,...,Xk>` in cui per almeno uno `Ai` dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="f0028-124">`T` is an interface or delegate type `S<A1,...,Ak>` constructed from a generic type `S<X1,...,Xk>` where for at least one `Ai` one of the following holds:</span></span>
   * <span data-ttu-id="f0028-125">`Xi` è covariante o invariante e non `Ai` è sicuro da input.</span><span class="sxs-lookup"><span data-stu-id="f0028-125">`Xi` is covariant or invariant and `Ai` is input-unsafe.</span></span>
   * <span data-ttu-id="f0028-126">`Xi` è controvariante o invariante e non `Ai` è sicuro per l'output.</span><span class="sxs-lookup"><span data-stu-id="f0028-126">`Xi` is contravariant or invariant and `Ai` is output-unsafe.</span></span>

<span data-ttu-id="f0028-127">In modo intuitivo, un tipo non sicuro di output è vietato in una posizione di output e un tipo di input non sicuro è vietato in una posizione di input.</span><span class="sxs-lookup"><span data-stu-id="f0028-127">Intuitively, an output-unsafe type is prohibited in an output position, and an input-unsafe type is prohibited in an input position.</span></span>

<span data-ttu-id="f0028-128">Un tipo è ***output-Safe** _ se non è unsafe di output, e _ *_input-safe_** se non è non sicuro da input.</span><span class="sxs-lookup"><span data-stu-id="f0028-128">A type is ***output-safe** _ if it is not output-unsafe, and _ *_input-safe_** if it is not input-unsafe.</span></span>


## <a name="other-considerations"></a><span data-ttu-id="f0028-129">Altre considerazioni</span><span class="sxs-lookup"><span data-stu-id="f0028-129">Other Considerations</span></span>

<span data-ttu-id="f0028-130">Si è inoltre preso in considerazione se questa situazione potrebbe interferire con alcuni altri miglioramenti che ci auguriamo di prendere in considerazione per i ruoli, le classi di tipi e le estensioni.</span><span class="sxs-lookup"><span data-stu-id="f0028-130">We also considered whether this could potentially interfere with some of the other enhancements we hope to make regarding roles, type classes, and extensions.</span></span> <span data-ttu-id="f0028-131">Queste dovrebbero essere tutte: non sarà possibile reimpostare i membri statici esistenti in modo che siano virtuali per impostazione predefinita per le interfacce, perché questo potrebbe essere una modifica di rilievo a più livelli, anche senza modificare il comportamento di varianza.</span><span class="sxs-lookup"><span data-stu-id="f0028-131">These should all be fine: we won't be able to retcon the existing static members to be virtual-by-default for interfaces, as that would end up being a breaking change on multiple levels, even without changing the variance behavior here.</span></span>
