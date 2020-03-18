---
ms.openlocfilehash: d0bb80305ccc755a555cf47a1d010fc4cb9a7bcd
ms.sourcegitcommit: 5688b13e66dd77b224a1223338de9e3b1f66d7f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79485518"
---
# <a name="readonly-instance-members"></a>Membri dell'istanza di ReadOnly

Problema da sostenere: <https://github.com/dotnet/csharplang/issues/1710>

## <a name="summary"></a>Summary
[summary]: #summary

Consente di specificare che i singoli membri di istanza in uno struct non modificano lo stato, nello stesso modo in cui `readonly struct` non specifica che i membri dell'istanza modifichino lo stato.

Vale la pena notare che `readonly instance member`! = `pure instance member`. Un membro di istanza `pure` garantisce che lo stato non verrà modificato. Un membro di istanza `readonly` garantisce solo che lo stato dell'istanza non verrà modificato.

Tutti i membri di istanza in una `readonly struct` possono essere considerati `readonly instance members`in modo implicito. I `readonly instance members` espliciti dichiarati in struct non di sola lettura si comportano allo stesso modo. Ad esempio, creeranno comunque copie nascoste se è stato chiamato un membro di istanza (nell'istanza corrente o in un campo dell'istanza) che era non ReadOnly.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Attualmente, gli utenti hanno la possibilità di creare `readonly struct` tipi che il compilatore impone che tutti i campi siano di sola lettura (e, per estensione, che nessuno dei membri di istanza modifichi lo stato). Tuttavia, esistono alcuni scenari in cui è presente un'API esistente che espone campi accessibili o che dispone di una combinazione di membri mutanti e non mutanti. In questi casi, non è possibile contrassegnare il tipo come `readonly` (si tratta di una modifica di rilievo).

Questo in genere non ha un notevole effetto, tranne nel caso di `in` parametri. Con `in` parametri per gli struct non di sola lettura, il compilatore effettuerà una copia del parametro per ogni chiamata del membro di istanza, poiché non può garantire che la chiamata non modifichi lo stato interno. Ciò può comportare una grande quantità di copie e le prestazioni complessive peggiori rispetto a quando si è appena passato lo struct direttamente per valore. Per un esempio, vedere questo codice su [sharplab](https://sharplab.io/#v2:CYLg1APgAgDABFAjAbgLACgNQMxwM4AuATgK4DGBcAagKYUD2RATBgN4ZycK4BmANvQCGlAB5p0XbnH5DKAT3GSOXHNIHC4AGRoA7AOYEAFgGUAjiUFEawZZ3YTJXPTQK3H9x54QB2OAAoROAAqOBEASjgwNy8YvzlguDkwxS8AXzd09EysXCgmOABhOA8VXnVKAFk/AEsdajoCRnyAN0E+EhoIks8oX1b2mgA6bX0jMwsrYEi4fo7h3QMTc0trFM5M1KA==)

Alcuni altri scenari in cui possono verificarsi copie nascoste includono `static readonly fields` e `literals`. Se sono supportate in futuro, `blittable constants` finiranno nella stessa barca; si tratta attualmente di una copia completa (alla chiamata del membro di istanza) se lo struct non è contrassegnato `readonly`.

## <a name="design"></a>Progettazione
[design]: #design

Consentire a un utente di specificare che un membro di istanza è, se stesso, `readonly` e non modifica lo stato dell'istanza (con tutte le verifiche appropriate eseguite dal compilatore, ovviamente). Ad esempio:

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

ReadOnly può essere applicato alle funzioni di accesso alle proprietà per indicare che `this` non verrà modificato nella funzione di accesso. Gli esempi seguenti hanno setter di sola lettura perché tali funzioni di accesso modificano lo stato del campo membro, ma non modificano il valore del campo membro.

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

Quando `readonly` viene applicato alla sintassi della proprietà, significa che tutte le funzioni di accesso vengono `readonly`.

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

ReadOnly può essere applicato solo alle funzioni di accesso che non mutano il tipo che lo contiene.

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

ReadOnly può essere applicato ad alcune proprietà implementate automaticamente, ma non avrà un effetto significativo. Il compilatore considererà tutti i Getter implementati automaticamente come ReadOnly indipendentemente dalla presenza della parola chiave `readonly`.

```csharp
// Allowed
public readonly int Prop4 { get; }
public int Prop5 { readonly get; }
public int Prop6 { readonly get; set; }

// Not allowed
public readonly int Prop7 { get; set; }
public int Prop8 { get; readonly set; }
```

ReadOnly può essere applicato agli eventi implementati manualmente, ma non agli eventi di tipo campo. ReadOnly non può essere applicato a singole funzioni di accesso agli eventi (Add/Remove).

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

Altri esempi di sintassi:

* Membri con corpo di espressione: `public readonly float ExpressionBodiedMember => (x * x) + (y * y);`
* Vincoli generici: `public static readonly void GenericMethod<T>(T value) where T : struct { }`

Il compilatore emetterebbe il membro di istanza, come di consueto, e creerebbe anche un attributo riconosciuto dal compilatore che indica che il membro di istanza non modifica lo stato. In questo modo, il parametro `this` nascosto diventa `in T` invece che `ref T`.

Ciò consente all'utente di chiamare in modo sicuro il metodo di istanza detto senza che il compilatore debba creare una copia.

Le restrizioni includono:

* Non è possibile applicare il modificatore `readonly` a metodi, costruttori o distruttori statici.
* Non è possibile applicare il modificatore `readonly` ai delegati.
* Non è possibile applicare il modificatore `readonly` ai membri della classe o dell'interfaccia.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Gli stessi svantaggi esistenti con `readonly struct` metodi attuali. Alcuni codici possono comunque causare la copia nascosta.

## <a name="notes"></a>Note
[notes]: #notes

Potrebbe anche essere possibile usare un attributo o un'altra parola chiave.

Questa proposta è piuttosto correlata a (ma è più un sottoinsieme di) `functional purity` e/o `constant expressions`, che hanno entrambe alcune proposte esistenti.
