---
ms.openlocfilehash: 368a2e5c076b1c2c3483b5f09ffafd28da229416
ms.sourcegitcommit: 84c16c14e06ef8451662d4ffc4bdb8dd3ef1cbd8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2020
ms.locfileid: "86469159"
---
# <a name="variance-safety-for-static-interface-members"></a><span data-ttu-id="76229-101">Sicurezza della varianza per i membri dell'interfaccia statica</span><span class="sxs-lookup"><span data-stu-id="76229-101">Variance Safety for static interface members</span></span>

## <a name="summary"></a><span data-ttu-id="76229-102">Summary</span><span class="sxs-lookup"><span data-stu-id="76229-102">Summary</span></span>

<span data-ttu-id="76229-103">Consente ai membri statici non virtuali nelle interfacce di trattare i parametri di tipo nelle rispettive dichiarazioni come invarianti, indipendentemente dalla varianza dichiarata.</span><span class="sxs-lookup"><span data-stu-id="76229-103">Allow static, non-virtual members in interfaces to treat type parameters in their declarations as invariant, regardless of their declared variance.</span></span>

## <a name="motivation"></a><span data-ttu-id="76229-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="76229-104">Motivation</span></span>


- https://github.com/dotnet/csharplang/issues/3275
- https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-06-24.md#interface-static-member-variance

<span data-ttu-id="76229-105">È stata considerata la varianza nei `static` membri dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="76229-105">We considered variance in `static` interface members.</span></span> <span data-ttu-id="76229-106">Attualmente, per i parametri di tipo covariante usati in questi membri, devono seguire le regole standard complete della varianza, causando un'incoerenza con il modo in cui i `static` campi vengono gestiti rispetto alle `static` proprietà o ai metodi:</span><span class="sxs-lookup"><span data-stu-id="76229-106">Today, for co/contravariant type parameters used in these members, they must follow the full standard rules of variance, leading to some inconsistency with the way that `static` fields are treated vs `static` properties or methods:</span></span>

```cs
public interface I<out T>
{
    static Task<T> F = Task.FromResult(default(T)); // No problem
    static Task<T> P => Task.FromResult(default(T));   //CS1961
    static Task<T> M() => Task.FromResult(default(T));    //CS1961
    static event EventHandler<T> E; // CS1961
}
```

<span data-ttu-id="76229-107">Poiché questi membri sono `static` e non virtuali, non sono presenti problemi di sicurezza: non è possibile derivare un membro Looser/più limitato in qualche modo, digitando l'interfaccia ed eseguendo l'override del membro.</span><span class="sxs-lookup"><span data-stu-id="76229-107">Because these members are `static` and non-virtual, there aren't any safety issues here: you can't derive a looser/more restricted member in some fashion by subtyping the interface and overriding the member.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="76229-108">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="76229-108">Detailed Design</span></span>

<span data-ttu-id="76229-109">Ecco il contenuto proposto per la sezione relativa alla sicurezza Vaiance della specifica del linguaggio ( https://github.com/dotnet/csharplang/blob/master/spec/interfaces.md#variance-safety) .</span><span class="sxs-lookup"><span data-stu-id="76229-109">Here is the proposed content for Vaiance Safety section of the language specification (https://github.com/dotnet/csharplang/blob/master/spec/interfaces.md#variance-safety).</span></span>
<span data-ttu-id="76229-110">La modifica è l'aggiunta di "*queste restrizioni non si applicano a ocurrances di tipi nelle dichiarazioni di membri statici".*</span><span class="sxs-lookup"><span data-stu-id="76229-110">The change is the addition of "*These restrictions do not apply to ocurrances of types within declarations of static members.*"</span></span> <span data-ttu-id="76229-111">frase all'inizio della sezione.</span><span class="sxs-lookup"><span data-stu-id="76229-111">sentence at the beginning of the section.</span></span> 

### <a name="variance-safety"></a><span data-ttu-id="76229-112">Sicurezza della varianza</span><span class="sxs-lookup"><span data-stu-id="76229-112">Variance safety</span></span>

<span data-ttu-id="76229-113">L'occorrenza delle annotazioni di varianza nell'elenco dei parametri di tipo di un tipo limita le posizioni in cui i tipi possono essere presenti all'interno della dichiarazione del tipo.</span><span class="sxs-lookup"><span data-stu-id="76229-113">The occurrence of variance annotations in the type parameter list of a type restricts the places where types can occur within the type declaration.</span></span>
<span data-ttu-id="76229-114">*Queste restrizioni non si applicano a ocurrances di tipi nelle dichiarazioni di membri statici.*</span><span class="sxs-lookup"><span data-stu-id="76229-114">*These restrictions do not apply to ocurrances of types within declarations of static members.*</span></span>

<span data-ttu-id="76229-115">Un tipo `T` è ***unsafe di output*** se uno degli elementi seguenti include:</span><span class="sxs-lookup"><span data-stu-id="76229-115">A type `T` is ***output-unsafe*** if one of the following holds:</span></span>

*  <span data-ttu-id="76229-116">`T`è un parametro di tipo controvariante</span><span class="sxs-lookup"><span data-stu-id="76229-116">`T` is a contravariant type parameter</span></span>
*  <span data-ttu-id="76229-117">`T`è un tipo di matrice con un tipo di elemento non sicuro di output</span><span class="sxs-lookup"><span data-stu-id="76229-117">`T` is an array type with an output-unsafe element type</span></span>
*  <span data-ttu-id="76229-118">`T`è un tipo di interfaccia o delegato `S<A1,...,Ak>` costruito da un tipo generico `S<X1,...,Xk>` in cui per almeno uno `Ai` dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="76229-118">`T` is an interface or delegate type `S<A1,...,Ak>` constructed from a generic type `S<X1,...,Xk>` where for at least one `Ai` one of the following holds:</span></span>
   * <span data-ttu-id="76229-119">`Xi`è covariante o invariante e non `Ai` è sicuro per l'output.</span><span class="sxs-lookup"><span data-stu-id="76229-119">`Xi` is covariant or invariant and `Ai` is output-unsafe.</span></span>
   * <span data-ttu-id="76229-120">`Xi`è controvariante o invariante ed `Ai` è indipendente dall'input.</span><span class="sxs-lookup"><span data-stu-id="76229-120">`Xi` is contravariant or invariant and `Ai` is input-safe.</span></span>
   
<span data-ttu-id="76229-121">Un tipo `T` è ***unsafe di input*** se uno degli elementi seguenti include:</span><span class="sxs-lookup"><span data-stu-id="76229-121">A type `T` is ***input-unsafe*** if one of the following holds:</span></span>

*  <span data-ttu-id="76229-122">`T`è un parametro di tipo covariante</span><span class="sxs-lookup"><span data-stu-id="76229-122">`T` is a covariant type parameter</span></span>
*  <span data-ttu-id="76229-123">`T`è un tipo di matrice con un tipo di elemento non sicuro di input</span><span class="sxs-lookup"><span data-stu-id="76229-123">`T` is an array type with an input-unsafe element type</span></span>
*  <span data-ttu-id="76229-124">`T`è un tipo di interfaccia o delegato `S<A1,...,Ak>` costruito da un tipo generico `S<X1,...,Xk>` in cui per almeno uno `Ai` dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="76229-124">`T` is an interface or delegate type `S<A1,...,Ak>` constructed from a generic type `S<X1,...,Xk>` where for at least one `Ai` one of the following holds:</span></span>
   * <span data-ttu-id="76229-125">`Xi`è covariante o invariante e non `Ai` è sicuro da input.</span><span class="sxs-lookup"><span data-stu-id="76229-125">`Xi` is covariant or invariant and `Ai` is input-unsafe.</span></span>
   * <span data-ttu-id="76229-126">`Xi`è controvariante o invariante e non `Ai` è sicuro per l'output.</span><span class="sxs-lookup"><span data-stu-id="76229-126">`Xi` is contravariant or invariant and `Ai` is output-unsafe.</span></span>

<span data-ttu-id="76229-127">In modo intuitivo, un tipo non sicuro di output è vietato in una posizione di output e un tipo di input non sicuro è vietato in una posizione di input.</span><span class="sxs-lookup"><span data-stu-id="76229-127">Intuitively, an output-unsafe type is prohibited in an output position, and an input-unsafe type is prohibited in an input position.</span></span>

<span data-ttu-id="76229-128">Un tipo è ***indipendente dall'output*** se non è indipendente dall'output e indipendente dall' ***input*** se non è non sicuro da input.</span><span class="sxs-lookup"><span data-stu-id="76229-128">A type is ***output-safe*** if it is not output-unsafe, and ***input-safe*** if it is not input-unsafe.</span></span>


## <a name="other-considerations"></a><span data-ttu-id="76229-129">Altre considerazioni</span><span class="sxs-lookup"><span data-stu-id="76229-129">Other Considerations</span></span>

<span data-ttu-id="76229-130">Si è inoltre preso in considerazione se questa situazione potrebbe interferire con alcuni altri miglioramenti che ci auguriamo di prendere in considerazione per i ruoli, le classi di tipi e le estensioni.</span><span class="sxs-lookup"><span data-stu-id="76229-130">We also considered whether this could potentially interfere with some of the other enhancements we hope to make regarding roles, type classes, and extensions.</span></span> <span data-ttu-id="76229-131">Questi dovrebbero essere tutti i seguenti: non sarà possibile retconn i membri statici esistenti per la virtualizzazione per impostazione predefinita per le interfacce, perché questo potrebbe essere una modifica di rilievo a più livelli, anche senza modificare il comportamento di varianza.</span><span class="sxs-lookup"><span data-stu-id="76229-131">These should all be fine: we won't be able to retconn the existing static members to be virtual-by-default for interfaces, as that would end up being a breaking change on multiple levels, even without changing the variance behavior here.</span></span>
