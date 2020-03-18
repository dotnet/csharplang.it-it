---
ms.openlocfilehash: 392d932459ff0a4cb0d6d32c1606f73f9b913c68
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484454"
---
# <a name="covariant-return-types"></a><span data-ttu-id="39d24-101">tipi restituiti covarianti</span><span class="sxs-lookup"><span data-stu-id="39d24-101">covariant return types</span></span>

* <span data-ttu-id="39d24-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="39d24-102">[x] Proposed</span></span>
* <span data-ttu-id="39d24-103">[] Prototipo: non avviato</span><span class="sxs-lookup"><span data-stu-id="39d24-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="39d24-104">[] Implementazione: non avviata</span><span class="sxs-lookup"><span data-stu-id="39d24-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="39d24-105">[] Specifica: non avviata</span><span class="sxs-lookup"><span data-stu-id="39d24-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="39d24-106">Summary</span><span class="sxs-lookup"><span data-stu-id="39d24-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="39d24-107">Supportare _tipi restituiti covariante_.</span><span class="sxs-lookup"><span data-stu-id="39d24-107">Support _covariant return types_.</span></span> <span data-ttu-id="39d24-108">In particolare, consentire a un metodo di override di avere un tipo di riferimento più derivato rispetto al metodo sottoposto a override.</span><span class="sxs-lookup"><span data-stu-id="39d24-108">Specifically, allow an overriding method to have a more derived reference type than the method it overrides.</span></span>

## <a name="motivation"></a><span data-ttu-id="39d24-109">Motivazione</span><span class="sxs-lookup"><span data-stu-id="39d24-109">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="39d24-110">Si tratta di un modello comune nel codice in cui è necessario inventare nomi di metodo diversi per aggirare il vincolo di linguaggio che le sostituzioni devono restituire lo stesso tipo del metodo sottoposto a override.</span><span class="sxs-lookup"><span data-stu-id="39d24-110">It is a common pattern in code that different method names have to be invented to work around the language constraint that overrides must return the same type as the overridden method.</span></span> <span data-ttu-id="39d24-111">Vedere di seguito per un esempio della codebase di Roslyn.</span><span class="sxs-lookup"><span data-stu-id="39d24-111">See below for an example from the Roslyn code base.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="39d24-112">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="39d24-112">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="39d24-113">Supportare _tipi restituiti covariante_.</span><span class="sxs-lookup"><span data-stu-id="39d24-113">Support _covariant return types_.</span></span> <span data-ttu-id="39d24-114">In particolare, consentire a un metodo di override di avere un tipo di riferimento più derivato rispetto al metodo sottoposto a override.</span><span class="sxs-lookup"><span data-stu-id="39d24-114">Specifically, allow an overriding method to have a more derived reference type than the method it overrides.</span></span> <span data-ttu-id="39d24-115">Ciò si applica ai metodi e alle proprietà ed è supportato in classi e interfacce.</span><span class="sxs-lookup"><span data-stu-id="39d24-115">This would apply to methods and properties, and be supported in classes and interfaces.</span></span>

<span data-ttu-id="39d24-116">Questa operazione è utile nel modello Factory.</span><span class="sxs-lookup"><span data-stu-id="39d24-116">This would be useful in the factory pattern.</span></span> <span data-ttu-id="39d24-117">Ad esempio, nella base di codice Roslyn avremmo</span><span class="sxs-lookup"><span data-stu-id="39d24-117">For example, in the Roslyn code base we would have</span></span>

``` cs
class Compilation ...
{
    virtual Compilation WithOptions(Options options)...
}
```

``` cs
class CSharpCompilation : Compilation
{
    override CSharpCompilation WithOptions(Options options)...
}
```

<span data-ttu-id="39d24-118">L'implementazione di questa operazione è che il compilatore emetta il metodo che esegue l'override come un metodo virtuale "nuovo" che nasconde il metodo della classe base, insieme a un _Metodo Bridge_ che implementa il metodo della classe base con una chiamata al metodo della classe derivata.</span><span class="sxs-lookup"><span data-stu-id="39d24-118">The implementation of this would be for the compiler to emit the overriding method as a "new" virtual method that hides the base class method, along with a _bridge method_ that implements the base class method with a call to the derived class method.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="39d24-119">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="39d24-119">Drawbacks</span></span>
[drawbacks]: #drawbacks

- <span data-ttu-id="39d24-120">[] Ogni modifica della lingua deve corrispondere a se stessa.</span><span class="sxs-lookup"><span data-stu-id="39d24-120">[ ] Every language change must pay for itself.</span></span>
- <span data-ttu-id="39d24-121">[] È necessario assicurarsi che le prestazioni siano ragionevoli, anche in caso di gerarchie di ereditarietà approfondita</span><span class="sxs-lookup"><span data-stu-id="39d24-121">[ ] We should ensure that the performance is reasonable, even in the case of deep inheritance hierarchies</span></span>
- <span data-ttu-id="39d24-122">[] È necessario assicurarsi che gli artefatti della strategia di traduzione non influiscano sulla semantica del linguaggio, anche quando si utilizza IL nuovo IL da compilatori obsoleti.</span><span class="sxs-lookup"><span data-stu-id="39d24-122">[ ] We should ensure that artifacts of the translation strategy do not affect language semantics, even when consuming new IL from old compilers.</span></span>

## <a name="alternatives"></a><span data-ttu-id="39d24-123">Alternativi</span><span class="sxs-lookup"><span data-stu-id="39d24-123">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="39d24-124">È possibile ridurre leggermente le regole del linguaggio per consentire, in origine,</span><span class="sxs-lookup"><span data-stu-id="39d24-124">We could relax the language rules slightly to allow, in source,</span></span>

```csharp
abstract class Cloneable
{
    public abstract Cloneable Clone();
}

class Digit : Cloneable
{
    public override Cloneable Clone()
    {
        return this.Clone();
    }

    public new Digit Clone() // Error: 'Digit' already defines a member called 'Clone' with the same parameter types
    {
        return this;
    }
}
```

## <a name="unresolved-questions"></a><span data-ttu-id="39d24-125">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="39d24-125">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="39d24-126">[] In che modo le API compilate per utilizzare questa funzionalità funzionano in versioni precedenti del linguaggio?</span><span class="sxs-lookup"><span data-stu-id="39d24-126">[ ] How will APIs that have been compiled to use this feature work in older versions of the language?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="39d24-127">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="39d24-127">Design meetings</span></span>

<span data-ttu-id="39d24-128">Nessuno ancora.</span><span class="sxs-lookup"><span data-stu-id="39d24-128">None yet.</span></span> <span data-ttu-id="39d24-129">Ci sono state alcune discussioni in <https://github.com/dotnet/roslyn/issues/357>.</span><span class="sxs-lookup"><span data-stu-id="39d24-129">There has been some discussion at <https://github.com/dotnet/roslyn/issues/357>.</span></span>