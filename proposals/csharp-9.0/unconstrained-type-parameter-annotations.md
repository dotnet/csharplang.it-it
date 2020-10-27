---
ms.openlocfilehash: 18f4447891d10072f4955478c4da7baa3ce9cc93
ms.sourcegitcommit: 9807f9b2e2105255c0a658f3afc908769bb31339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92550704"
---
# <a name="unconstrained-type-parameter-annotations"></a><span data-ttu-id="8ba2f-101">Annotazioni di parametri di tipo non vincolate</span><span class="sxs-lookup"><span data-stu-id="8ba2f-101">Unconstrained type parameter annotations</span></span>

## <a name="summary"></a><span data-ttu-id="8ba2f-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="8ba2f-102">Summary</span></span>

<span data-ttu-id="8ba2f-103">Consente le annotazioni Nullable per i parametri di tipo che non sono vincolati a tipi di valore o tipi di riferimento: `T?` .</span><span class="sxs-lookup"><span data-stu-id="8ba2f-103">Allow nullable annotations for type parameters that are not constrained to value types or reference types: `T?`.</span></span>
```C#
static T? FirstOrDefault<T>(this IEnumerable<T> collection) { ... }
```

## <a name="-annotation"></a><span data-ttu-id="8ba2f-104">Annotazione `?`</span><span class="sxs-lookup"><span data-stu-id="8ba2f-104">`?` annotation</span></span>

<span data-ttu-id="8ba2f-105">In C# 8, le `?` annotazioni possono essere applicate solo ai parametri di tipo che sono stati vincolati in modo esplicito ai tipi di valore o ai tipi di riferimento.</span><span class="sxs-lookup"><span data-stu-id="8ba2f-105">In C#8, `?` annotations could only be applied to type parameters that were explicitly constrained to value types or reference types.</span></span>
<span data-ttu-id="8ba2f-106">In C# 9, `?` le annotazioni possono essere applicate a qualsiasi parametro di tipo, indipendentemente dai vincoli.</span><span class="sxs-lookup"><span data-stu-id="8ba2f-106">In C#9, `?` annotations can be applied to any type parameter, regardless of constraints.</span></span>

<span data-ttu-id="8ba2f-107">A meno che un parametro di tipo non venga vincolato in modo esplicito ai tipi valore, le annotazioni possono essere applicate solo all'interno di un `#nullable enable` contesto.</span><span class="sxs-lookup"><span data-stu-id="8ba2f-107">Unless a type parameter is explicitly constrained to value types, annotations can only be applied within a `#nullable enable` context.</span></span>

<span data-ttu-id="8ba2f-108">Se un parametro di tipo `T` viene sostituito con un tipo di riferimento, `T?` rappresenta un'istanza nullable di tale tipo di riferimento.</span><span class="sxs-lookup"><span data-stu-id="8ba2f-108">If a type parameter `T` is substituted with a reference type, then `T?` represents a nullable instance of that reference type.</span></span>
```C#
var s1 = new string[0].FirstOrDefault();  // string? s1
var s2 = new string?[0].FirstOrDefault(); // string? s2
```

<span data-ttu-id="8ba2f-109">Se `T` viene sostituito con un tipo di valore, `T?` rappresenta un'istanza di `T` .</span><span class="sxs-lookup"><span data-stu-id="8ba2f-109">If `T` is substituted with a value type, then `T?` represents an instance of `T`.</span></span>
```C#
var i1 = new int[0].FirstOrDefault();  // int i1
var i2 = new int?[0].FirstOrDefault(); // int? i2
```

<span data-ttu-id="8ba2f-110">Se `T` viene sostituito con un tipo con annotazioni `U?` , `T?` rappresenta il tipo annotato `U?` anziché `U??` .</span><span class="sxs-lookup"><span data-stu-id="8ba2f-110">If `T` is substituted with an annotated type `U?`, then `T?` represents the annotated type `U?` rather than `U??`.</span></span>
```C#
var u1 = new U[0].FirstOrDefault();  // U? u1
var u2 = new U?[0].FirstOrDefault(); // U? u2
```

<span data-ttu-id="8ba2f-111">Se `T` viene sostituito con un tipo `U` , quindi `T?` rappresenta `U?` , anche all'interno di un `#nullable disable` contesto.</span><span class="sxs-lookup"><span data-stu-id="8ba2f-111">If `T` is substituted with a type `U`, then `T?` represents `U?`, even within a `#nullable disable` context.</span></span>
```C#
#nullable disable
var u3 = new U[0].FirstOrDefault();  // U? u3
```

<span data-ttu-id="8ba2f-112">Per i valori restituiti, equivale `T?` a `[MaybeNull]T` ; per i valori di argomento, equivale `T?` a `[AllowNull]T` .</span><span class="sxs-lookup"><span data-stu-id="8ba2f-112">For return values, `T?` is equivalent to `[MaybeNull]T`; for argument values, `T?` is equivalent to `[AllowNull]T`.</span></span>
<span data-ttu-id="8ba2f-113">L'equivalenza è importante quando si esegue l'override o l'implementazione di interfacce da un assembly compilato con C# 8.</span><span class="sxs-lookup"><span data-stu-id="8ba2f-113">The equivalence is important when overriding or implementing interfaces from an assembly compiled with C#8.</span></span>
```C#
public abstract class A
{
    [return: MaybeNull] public abstract T F1<T>();
    public abstract void F2<T>([AllowNull] T t);
}

public class B : A
{
    public override T? F1<T>() where T : default { ... }       // matches A.F1<T>()
    public override void F2<T>(T? t) where T : default { ... } // matches A.F2<T>()
}
```

## <a name="default-constraint"></a><span data-ttu-id="8ba2f-114">`default` vincolo</span><span class="sxs-lookup"><span data-stu-id="8ba2f-114">`default` constraint</span></span>

<span data-ttu-id="8ba2f-115">Per compatibilità con il codice esistente in cui i metodi generici sottoposti a override e implementati in modo esplicito non potevano includere clausole di vincolo esplicito, `T?` in un metodo sottoposto a override o implementato in modo esplicito viene considerato come `Nullable<T>` Where `T` è un tipo di valore</span><span class="sxs-lookup"><span data-stu-id="8ba2f-115">For compatibility with existing code where overridden and explicitly implemented generic methods could not include explicit constraint clauses, `T?` in an overridden or explicitly implemented method is treated as `Nullable<T>` where `T` is a value type.</span></span>

<span data-ttu-id="8ba2f-116">Per consentire le annotazioni per i parametri di tipo vincolati ai tipi di riferimento, C# 8 ha consentito esplicitamente `where T : class` e `where T : struct` vincoli sul metodo sottoposto a override o implementato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="8ba2f-116">To allow annotations for type parameters constrained to reference types, C#8 allowed explicit `where T : class` and `where T : struct` constraints on the overridden or explicitly implemented method.</span></span>
```C#
class A1
{
    public virtual void F1<T>(T? t) where T : struct { }
    public virtual void F1<T>(T? t) where T : class { }
}

class B1 : A1
{
    public override void F1<T>(T? t) /*where T : struct*/ { }
    public override void F1<T>(T? t) where T : class { }
}
```

<span data-ttu-id="8ba2f-117">Per consentire le annotazioni per i parametri di tipo non vincolati ai tipi di riferimento o ai tipi di valore, C# 9 consente un nuovo `where T : default` vincolo.</span><span class="sxs-lookup"><span data-stu-id="8ba2f-117">To allow annotations for type parameters that are not constrained to reference types or value types, C#9 allows a new `where T : default` constraint.</span></span>
```C#
class A2
{
    public virtual void F2<T>(T? t) where T : struct { }
    public virtual void F2<T>(T? t) { }
}

class B2 : A2
{
    public override void F2<T>(T? t) /*where T : struct*/ { }
    public override void F2<T>(T? t) where T : default { }
}
```

<span data-ttu-id="8ba2f-118">Non è possibile usare un `default` vincolo diverso dall'override di un metodo o di un'implementazione esplicita.</span><span class="sxs-lookup"><span data-stu-id="8ba2f-118">It is an error to use a `default` constraint other than on a method override or explicit implementation.</span></span>
<span data-ttu-id="8ba2f-119">È un errore usare un `default` vincolo quando il parametro di tipo corrispondente nel metodo di interfaccia o sottoposto a override è vincolato a un tipo di riferimento o a un tipo di valore.</span><span class="sxs-lookup"><span data-stu-id="8ba2f-119">It is an error to use a `default` constraint when the corresponding type parameter in the overridden or interface method is constrained to a reference type or value type.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="8ba2f-120">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="8ba2f-120">Design meetings</span></span>

- https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-11-25.md
- https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-06-17.md#t
