---
ms.openlocfilehash: 392d932459ff0a4cb0d6d32c1606f73f9b913c68
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484454"
---
# <a name="covariant-return-types"></a>tipi restituiti covarianti

* [x] proposto
* [] Prototipo: non avviato
* [] Implementazione: non avviata
* [] Specifica: non avviata

## <a name="summary"></a>Summary
[summary]: #summary

Supportare _tipi restituiti covariante_. In particolare, consentire a un metodo di override di avere un tipo di riferimento più derivato rispetto al metodo sottoposto a override.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Si tratta di un modello comune nel codice in cui è necessario inventare nomi di metodo diversi per aggirare il vincolo di linguaggio che le sostituzioni devono restituire lo stesso tipo del metodo sottoposto a override. Vedere di seguito per un esempio della codebase di Roslyn.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Supportare _tipi restituiti covariante_. In particolare, consentire a un metodo di override di avere un tipo di riferimento più derivato rispetto al metodo sottoposto a override. Ciò si applica ai metodi e alle proprietà ed è supportato in classi e interfacce.

Questa operazione è utile nel modello Factory. Ad esempio, nella base di codice Roslyn avremmo

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

L'implementazione di questa operazione è che il compilatore emetta il metodo che esegue l'override come un metodo virtuale "nuovo" che nasconde il metodo della classe base, insieme a un _Metodo Bridge_ che implementa il metodo della classe base con una chiamata al metodo della classe derivata.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

- [] Ogni modifica della lingua deve corrispondere a se stessa.
- [] È necessario assicurarsi che le prestazioni siano ragionevoli, anche in caso di gerarchie di ereditarietà approfondita
- [] È necessario assicurarsi che gli artefatti della strategia di traduzione non influiscano sulla semantica del linguaggio, anche quando si utilizza IL nuovo IL da compilatori obsoleti.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

È possibile ridurre leggermente le regole del linguaggio per consentire, in origine,

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

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

- [] In che modo le API compilate per utilizzare questa funzionalità funzionano in versioni precedenti del linguaggio?

## <a name="design-meetings"></a>Riunioni di progettazione

Nessuno ancora. Ci sono state alcune discussioni in <https://github.com/dotnet/roslyn/issues/357>.