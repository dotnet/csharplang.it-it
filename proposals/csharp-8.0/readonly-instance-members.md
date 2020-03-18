---
ms.openlocfilehash: d0bb80305ccc755a555cf47a1d010fc4cb9a7bcd
ms.sourcegitcommit: 5688b13e66dd77b224a1223338de9e3b1f66d7f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79485518"
---
# <a name="readonly-instance-members"></a><span data-ttu-id="0a79e-101">Membri dell'istanza di ReadOnly</span><span class="sxs-lookup"><span data-stu-id="0a79e-101">Readonly Instance Members</span></span>

<span data-ttu-id="0a79e-102">Problema da sostenere: <https://github.com/dotnet/csharplang/issues/1710></span><span class="sxs-lookup"><span data-stu-id="0a79e-102">Championed Issue: <https://github.com/dotnet/csharplang/issues/1710></span></span>

## <a name="summary"></a><span data-ttu-id="0a79e-103">Summary</span><span class="sxs-lookup"><span data-stu-id="0a79e-103">Summary</span></span>
[summary]: #summary

<span data-ttu-id="0a79e-104">Consente di specificare che i singoli membri di istanza in uno struct non modificano lo stato, nello stesso modo in cui `readonly struct` non specifica che i membri dell'istanza modifichino lo stato.</span><span class="sxs-lookup"><span data-stu-id="0a79e-104">Provide a way to specify individual instance members on a struct do not modify state, in the same way that `readonly struct` specifies no instance members modify state.</span></span>

<span data-ttu-id="0a79e-105">Vale la pena notare che `readonly instance member`! = `pure instance member`.</span><span class="sxs-lookup"><span data-stu-id="0a79e-105">It is worth noting that `readonly instance member` != `pure instance member`.</span></span> <span data-ttu-id="0a79e-106">Un membro di istanza `pure` garantisce che lo stato non verrà modificato.</span><span class="sxs-lookup"><span data-stu-id="0a79e-106">A `pure` instance member guarantees no state will be modified.</span></span> <span data-ttu-id="0a79e-107">Un membro di istanza `readonly` garantisce solo che lo stato dell'istanza non verrà modificato.</span><span class="sxs-lookup"><span data-stu-id="0a79e-107">A `readonly` instance member only guarantees that instance state will not be modified.</span></span>

<span data-ttu-id="0a79e-108">Tutti i membri di istanza in una `readonly struct` possono essere considerati `readonly instance members`in modo implicito.</span><span class="sxs-lookup"><span data-stu-id="0a79e-108">All instance members on a `readonly struct` could be considered implicitly `readonly instance members`.</span></span> <span data-ttu-id="0a79e-109">I `readonly instance members` espliciti dichiarati in struct non di sola lettura si comportano allo stesso modo.</span><span class="sxs-lookup"><span data-stu-id="0a79e-109">Explicit `readonly instance members` declared on non-readonly structs would behave in the same manner.</span></span> <span data-ttu-id="0a79e-110">Ad esempio, creeranno comunque copie nascoste se è stato chiamato un membro di istanza (nell'istanza corrente o in un campo dell'istanza) che era non ReadOnly.</span><span class="sxs-lookup"><span data-stu-id="0a79e-110">For example, they would still create hidden copies if you called an instance member (on the current instance or on a field of the instance) which was itself not-readonly.</span></span>

## <a name="motivation"></a><span data-ttu-id="0a79e-111">Motivazione</span><span class="sxs-lookup"><span data-stu-id="0a79e-111">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="0a79e-112">Attualmente, gli utenti hanno la possibilità di creare `readonly struct` tipi che il compilatore impone che tutti i campi siano di sola lettura (e, per estensione, che nessuno dei membri di istanza modifichi lo stato).</span><span class="sxs-lookup"><span data-stu-id="0a79e-112">Today, users have the ability to create `readonly struct` types which the compiler enforces that all fields are readonly (and by extension, that no instance members modify the state).</span></span> <span data-ttu-id="0a79e-113">Tuttavia, esistono alcuni scenari in cui è presente un'API esistente che espone campi accessibili o che dispone di una combinazione di membri mutanti e non mutanti.</span><span class="sxs-lookup"><span data-stu-id="0a79e-113">However, there are some scenarios where you have an existing API that exposes accessible fields or that has a mix of mutating and non-mutating members.</span></span> <span data-ttu-id="0a79e-114">In questi casi, non è possibile contrassegnare il tipo come `readonly` (si tratta di una modifica di rilievo).</span><span class="sxs-lookup"><span data-stu-id="0a79e-114">Under these circumstances, you cannot mark the type as `readonly` (it would be a breaking change).</span></span>

<span data-ttu-id="0a79e-115">Questo in genere non ha un notevole effetto, tranne nel caso di `in` parametri.</span><span class="sxs-lookup"><span data-stu-id="0a79e-115">This normally doesn't have much impact, except in the case of `in` parameters.</span></span> <span data-ttu-id="0a79e-116">Con `in` parametri per gli struct non di sola lettura, il compilatore effettuerà una copia del parametro per ogni chiamata del membro di istanza, poiché non può garantire che la chiamata non modifichi lo stato interno.</span><span class="sxs-lookup"><span data-stu-id="0a79e-116">With `in` parameters for non-readonly structs, the compiler will make a copy of the parameter for each instance member invocation, since it cannot guarantee that the invocation does not modify internal state.</span></span> <span data-ttu-id="0a79e-117">Ciò può comportare una grande quantità di copie e le prestazioni complessive peggiori rispetto a quando si è appena passato lo struct direttamente per valore.</span><span class="sxs-lookup"><span data-stu-id="0a79e-117">This can lead to a multitude of copies and worse overall performance than if you had just passed the struct directly by value.</span></span> <span data-ttu-id="0a79e-118">Per un esempio, vedere questo codice su [sharplab](https://sharplab.io/#v2:CYLg1APgAgDABFAjAbgLACgNQMxwM4AuATgK4DGBcAagKYUD2RATBgN4ZycK4BmANvQCGlAB5p0XbnH5DKAT3GSOXHNIHC4AGRoA7AOYEAFgGUAjiUFEawZZ3YTJXPTQK3H9x54QB2OAAoROAAqOBEASjgwNy8YvzlguDkwxS8AXzd09EysXCgmOABhOA8VXnVKAFk/AEsdajoCRnyAN0E+EhoIks8oX1b2mgA6bX0jMwsrYEi4fo7h3QMTc0trFM5M1KA==)</span><span class="sxs-lookup"><span data-stu-id="0a79e-118">For an example, see this code on [sharplab](https://sharplab.io/#v2:CYLg1APgAgDABFAjAbgLACgNQMxwM4AuATgK4DGBcAagKYUD2RATBgN4ZycK4BmANvQCGlAB5p0XbnH5DKAT3GSOXHNIHC4AGRoA7AOYEAFgGUAjiUFEawZZ3YTJXPTQK3H9x54QB2OAAoROAAqOBEASjgwNy8YvzlguDkwxS8AXzd09EysXCgmOABhOA8VXnVKAFk/AEsdajoCRnyAN0E+EhoIks8oX1b2mgA6bX0jMwsrYEi4fo7h3QMTc0trFM5M1KA==)</span></span>

<span data-ttu-id="0a79e-119">Alcuni altri scenari in cui possono verificarsi copie nascoste includono `static readonly fields` e `literals`.</span><span class="sxs-lookup"><span data-stu-id="0a79e-119">Some other scenarios where hidden copies can occur include `static readonly fields` and `literals`.</span></span> <span data-ttu-id="0a79e-120">Se sono supportate in futuro, `blittable constants` finiranno nella stessa barca; si tratta attualmente di una copia completa (alla chiamata del membro di istanza) se lo struct non è contrassegnato `readonly`.</span><span class="sxs-lookup"><span data-stu-id="0a79e-120">If they are supported in the future, `blittable constants` would end up in the same boat; that is they all currently necessitate a full copy (on instance member invocation) if the struct is not marked `readonly`.</span></span>

## <a name="design"></a><span data-ttu-id="0a79e-121">Progettazione</span><span class="sxs-lookup"><span data-stu-id="0a79e-121">Design</span></span>
[design]: #design

<span data-ttu-id="0a79e-122">Consentire a un utente di specificare che un membro di istanza è, se stesso, `readonly` e non modifica lo stato dell'istanza (con tutte le verifiche appropriate eseguite dal compilatore, ovviamente).</span><span class="sxs-lookup"><span data-stu-id="0a79e-122">Allow a user to specify that an instance member is, itself, `readonly` and does not modify the state of the instance (with all the appropriate verification done by the compiler, of course).</span></span> <span data-ttu-id="0a79e-123">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0a79e-123">For example:</span></span>

```csharp
public struct Vector2
{
    public float x;
    public float y;

    public readonly float GetLengthReadonly()
    {
        return MathF.Sqrt(LengthSquared);
    }

    public float GetLength()
    {
        return MathF.Sqrt(LengthSquared);
    }

    public readonly float GetLengthIllegal()
    {
        var tmp = MathF.Sqrt(LengthSquared);

        x = tmp;    // Compiler error, cannot write x
        y = tmp;    // Compiler error, cannot write y

        return tmp;
    }

    public float LengthSquared
    {
        readonly get
        {
            return (x * x) +
                   (y * y);
        }
    }
}

public static class MyClass
{
    public static float ExistingBehavior(in Vector2 vector)
    {
        // This code causes a hidden copy, the compiler effectively emits:
        //    var tmpVector = vector;
        //    return tmpVector.GetLength();
        //
        // This is done because the compiler doesn't know that `GetLength()`
        // won't mutate `vector`.

        return vector.GetLength();
    }

    public static float ReadonlyBehavior(in Vector2 vector)
    {
        // This code is emitted exactly as listed. There are no hidden
        // copies as the `readonly` modifier indicates that the method
        // won't mutate `vector`.

        return vector.GetLengthReadonly();
    }
}
```

<span data-ttu-id="0a79e-124">ReadOnly può essere applicato alle funzioni di accesso alle proprietà per indicare che `this` non verrà modificato nella funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="0a79e-124">Readonly can be applied to property accessors to indicate that `this` will not be mutated in the accessor.</span></span> <span data-ttu-id="0a79e-125">Gli esempi seguenti hanno setter di sola lettura perché tali funzioni di accesso modificano lo stato del campo membro, ma non modificano il valore del campo membro.</span><span class="sxs-lookup"><span data-stu-id="0a79e-125">The following examples have readonly setters because those accessors modify the state of member field, but do not modify the value of that member field.</span></span>

```csharp
public int Prop1
{
    readonly get
    {
        return this._store["Prop1"];
    }
    readonly set
    {
        this._store["Prop1"] = value;
    }
}
```

<span data-ttu-id="0a79e-126">Quando `readonly` viene applicato alla sintassi della proprietà, significa che tutte le funzioni di accesso vengono `readonly`.</span><span class="sxs-lookup"><span data-stu-id="0a79e-126">When `readonly` is applied to the property syntax, it means that all accessors are `readonly`.</span></span>

```csharp
public readonly int Prop2
{
    get
    {
        return this._store["Prop2"];
    }
    set
    {
        this._store["Prop2"] = value;
    }
}
```

<span data-ttu-id="0a79e-127">ReadOnly può essere applicato solo alle funzioni di accesso che non mutano il tipo che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="0a79e-127">Readonly can only be applied to accessors which do not mutate the containing type.</span></span>

```csharp
public int Prop3
{
    readonly get
    {
        return this._prop3;
    }
    set
    {
        this._prop3 = value;
    }
}
```

<span data-ttu-id="0a79e-128">ReadOnly può essere applicato ad alcune proprietà implementate automaticamente, ma non avrà un effetto significativo.</span><span class="sxs-lookup"><span data-stu-id="0a79e-128">Readonly can be applied to some auto-implemented properties, but it won't have a meaningful effect.</span></span> <span data-ttu-id="0a79e-129">Il compilatore considererà tutti i Getter implementati automaticamente come ReadOnly indipendentemente dalla presenza della parola chiave `readonly`.</span><span class="sxs-lookup"><span data-stu-id="0a79e-129">The compiler will treat all auto-implemented getters as readonly whether or not the `readonly` keyword is present.</span></span>

```csharp
// Allowed
public readonly int Prop4 { get; }
public int Prop5 { readonly get; }
public int Prop6 { readonly get; set; }

// Not allowed
public readonly int Prop7 { get; set; }
public int Prop8 { get; readonly set; }
```

<span data-ttu-id="0a79e-130">ReadOnly può essere applicato agli eventi implementati manualmente, ma non agli eventi di tipo campo.</span><span class="sxs-lookup"><span data-stu-id="0a79e-130">Readonly can be applied to manually-implemented events, but not field-like events.</span></span> <span data-ttu-id="0a79e-131">ReadOnly non può essere applicato a singole funzioni di accesso agli eventi (Add/Remove).</span><span class="sxs-lookup"><span data-stu-id="0a79e-131">Readonly cannot be applied to individual event accessors (add/remove).</span></span>

```csharp
// Allowed
public readonly event Action<EventArgs> Event1
{
    add { }
    remove { }
}

// Not allowed
public readonly event Action<EventArgs> Event2;
public event Action<EventArgs> Event3
{
    readonly add { }
    readonly remove { }
}
public static readonly event Event4
{
    add { }
    remove { }
}
```

<span data-ttu-id="0a79e-132">Altri esempi di sintassi:</span><span class="sxs-lookup"><span data-stu-id="0a79e-132">Some other syntax examples:</span></span>

* <span data-ttu-id="0a79e-133">Membri con corpo di espressione: `public readonly float ExpressionBodiedMember => (x * x) + (y * y);`</span><span class="sxs-lookup"><span data-stu-id="0a79e-133">Expression bodied members: `public readonly float ExpressionBodiedMember => (x * x) + (y * y);`</span></span>
* <span data-ttu-id="0a79e-134">Vincoli generici: `public static readonly void GenericMethod<T>(T value) where T : struct { }`</span><span class="sxs-lookup"><span data-stu-id="0a79e-134">Generic constraints: `public static readonly void GenericMethod<T>(T value) where T : struct { }`</span></span>

<span data-ttu-id="0a79e-135">Il compilatore emetterebbe il membro di istanza, come di consueto, e creerebbe anche un attributo riconosciuto dal compilatore che indica che il membro di istanza non modifica lo stato.</span><span class="sxs-lookup"><span data-stu-id="0a79e-135">The compiler would emit the instance member, as usual, and would additionally emit a compiler recognized attribute indicating that the instance member does not modify state.</span></span> <span data-ttu-id="0a79e-136">In questo modo, il parametro `this` nascosto diventa `in T` invece che `ref T`.</span><span class="sxs-lookup"><span data-stu-id="0a79e-136">This effectively causes the hidden `this` parameter to become `in T` instead of `ref T`.</span></span>

<span data-ttu-id="0a79e-137">Ciò consente all'utente di chiamare in modo sicuro il metodo di istanza detto senza che il compilatore debba creare una copia.</span><span class="sxs-lookup"><span data-stu-id="0a79e-137">This would allow the user to safely call said instance method without the compiler needing to make a copy.</span></span>

<span data-ttu-id="0a79e-138">Le restrizioni includono:</span><span class="sxs-lookup"><span data-stu-id="0a79e-138">The restrictions would include:</span></span>

* <span data-ttu-id="0a79e-139">Non è possibile applicare il modificatore `readonly` a metodi, costruttori o distruttori statici.</span><span class="sxs-lookup"><span data-stu-id="0a79e-139">The `readonly` modifier cannot be applied to static methods, constructors or destructors.</span></span>
* <span data-ttu-id="0a79e-140">Non è possibile applicare il modificatore `readonly` ai delegati.</span><span class="sxs-lookup"><span data-stu-id="0a79e-140">The `readonly` modifier cannot be applied to delegates.</span></span>
* <span data-ttu-id="0a79e-141">Non è possibile applicare il modificatore `readonly` ai membri della classe o dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="0a79e-141">The `readonly` modifier cannot be applied to members of class or interface.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="0a79e-142">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="0a79e-142">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="0a79e-143">Gli stessi svantaggi esistenti con `readonly struct` metodi attuali.</span><span class="sxs-lookup"><span data-stu-id="0a79e-143">Same drawbacks as exist with `readonly struct` methods today.</span></span> <span data-ttu-id="0a79e-144">Alcuni codici possono comunque causare la copia nascosta.</span><span class="sxs-lookup"><span data-stu-id="0a79e-144">Certain code may still cause hidden copies.</span></span>

## <a name="notes"></a><span data-ttu-id="0a79e-145">Note</span><span class="sxs-lookup"><span data-stu-id="0a79e-145">Notes</span></span>
[notes]: #notes

<span data-ttu-id="0a79e-146">Potrebbe anche essere possibile usare un attributo o un'altra parola chiave.</span><span class="sxs-lookup"><span data-stu-id="0a79e-146">Using an attribute or another keyword may also be possible.</span></span>

<span data-ttu-id="0a79e-147">Questa proposta è piuttosto correlata a (ma è più un sottoinsieme di) `functional purity` e/o `constant expressions`, che hanno entrambe alcune proposte esistenti.</span><span class="sxs-lookup"><span data-stu-id="0a79e-147">This proposal is somewhat related to (but is more a subset of) `functional purity` and/or `constant expressions`, both of which have had some existing proposals.</span></span>
