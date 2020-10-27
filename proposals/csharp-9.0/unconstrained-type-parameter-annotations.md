---
ms.openlocfilehash: 18f4447891d10072f4955478c4da7baa3ce9cc93
ms.sourcegitcommit: 9807f9b2e2105255c0a658f3afc908769bb31339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92550704"
---
# <a name="unconstrained-type-parameter-annotations"></a>Annotazioni di parametri di tipo non vincolate

## <a name="summary"></a>Riepilogo

Consente le annotazioni Nullable per i parametri di tipo che non sono vincolati a tipi di valore o tipi di riferimento: `T?` .
```C#
static T? FirstOrDefault<T>(this IEnumerable<T> collection) { ... }
```

## <a name="-annotation"></a>Annotazione `?`

In C# 8, le `?` annotazioni possono essere applicate solo ai parametri di tipo che sono stati vincolati in modo esplicito ai tipi di valore o ai tipi di riferimento.
In C# 9, `?` le annotazioni possono essere applicate a qualsiasi parametro di tipo, indipendentemente dai vincoli.

A meno che un parametro di tipo non venga vincolato in modo esplicito ai tipi valore, le annotazioni possono essere applicate solo all'interno di un `#nullable enable` contesto.

Se un parametro di tipo `T` viene sostituito con un tipo di riferimento, `T?` rappresenta un'istanza nullable di tale tipo di riferimento.
```C#
var s1 = new string[0].FirstOrDefault();  // string? s1
var s2 = new string?[0].FirstOrDefault(); // string? s2
```

Se `T` viene sostituito con un tipo di valore, `T?` rappresenta un'istanza di `T` .
```C#
var i1 = new int[0].FirstOrDefault();  // int i1
var i2 = new int?[0].FirstOrDefault(); // int? i2
```

Se `T` viene sostituito con un tipo con annotazioni `U?` , `T?` rappresenta il tipo annotato `U?` anziché `U??` .
```C#
var u1 = new U[0].FirstOrDefault();  // U? u1
var u2 = new U?[0].FirstOrDefault(); // U? u2
```

Se `T` viene sostituito con un tipo `U` , quindi `T?` rappresenta `U?` , anche all'interno di un `#nullable disable` contesto.
```C#
#nullable disable
var u3 = new U[0].FirstOrDefault();  // U? u3
```

Per i valori restituiti, equivale `T?` a `[MaybeNull]T` ; per i valori di argomento, equivale `T?` a `[AllowNull]T` .
L'equivalenza è importante quando si esegue l'override o l'implementazione di interfacce da un assembly compilato con C# 8.
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

## <a name="default-constraint"></a>`default` vincolo

Per compatibilità con il codice esistente in cui i metodi generici sottoposti a override e implementati in modo esplicito non potevano includere clausole di vincolo esplicito, `T?` in un metodo sottoposto a override o implementato in modo esplicito viene considerato come `Nullable<T>` Where `T` è un tipo di valore

Per consentire le annotazioni per i parametri di tipo vincolati ai tipi di riferimento, C# 8 ha consentito esplicitamente `where T : class` e `where T : struct` vincoli sul metodo sottoposto a override o implementato in modo esplicito.
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

Per consentire le annotazioni per i parametri di tipo non vincolati ai tipi di riferimento o ai tipi di valore, C# 9 consente un nuovo `where T : default` vincolo.
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

Non è possibile usare un `default` vincolo diverso dall'override di un metodo o di un'implementazione esplicita.
È un errore usare un `default` vincolo quando il parametro di tipo corrispondente nel metodo di interfaccia o sottoposto a override è vincolato a un tipo di riferimento o a un tipo di valore.

## <a name="design-meetings"></a>Riunioni di progettazione

- https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-11-25.md
- https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-06-17.md#t
