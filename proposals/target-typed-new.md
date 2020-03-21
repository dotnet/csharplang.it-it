---
ms.openlocfilehash: 38740069a2e105f920fa275c443f4560055e2901
ms.sourcegitcommit: 9aa177443b83116fe1be2ab28e2c7291947fe32d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2020
ms.locfileid: "80108370"
---

# <a name="target-typed-new-expressions"></a>Espressioni di `new` tipizzate come destinazione

* [x] proposto
* [prototipo](https://github.com/alrz/roslyn/tree/features/target-typed-new) [x]
* [] Implementazione
* [] Specifica

## <a name="summary"></a>Riepilogo
[summary]: #summary

Non richiede la specifica del tipo per i costruttori quando il tipo è noto. 

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Consente l'inizializzazione del campo senza duplicare il tipo.
```cs
Dictionary<string, List<int>> field = new() {
    { "item1", new() { 1, 2, 3 } }
};
```

Consente di omettere il tipo quando può essere dedotto dall'utilizzo.
```cs
XmlReader.Create(reader, new() { IgnoreWhitespace = true });
```

Creare un'istanza di un oggetto senza sillabare il tipo.
```cs
private readonly static object s_syncObj = new();
```

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

La sintassi *object_creation_expression* verrebbe modificata per rendere facoltativo il *tipo* quando sono presenti parentesi. Questa operazione è necessaria per risolvere l'ambiguità con *anonymous_object_creation_expression*.
```antlr
object_creation_expression
    : 'new' type? '(' argument_list? ')' object_or_collection_initializer?
    | 'new' type object_or_collection_initializer
    ;
```

Una `new` tipizzata come destinazione è convertibile in qualsiasi tipo. Di conseguenza, non contribuisce alla risoluzione dell'overload. Questa operazione viene eseguita principalmente per evitare modifiche di rilievo imprevedibili.

L'elenco di argomenti e le espressioni dell'inizializzatore verranno associati dopo che il tipo è stato determinato.

Il tipo dell'espressione verrebbe dedotto dal tipo di destinazione, che dovrebbe essere uno dei seguenti:

- **Qualsiasi tipo di struct** (inclusi i tipi di tupla)
- **Qualsiasi tipo di riferimento** (inclusi i tipi delegati)
- **Qualsiasi parametro di tipo** con un costruttore o un vincolo `struct`

con le eccezioni seguenti:

- **Tipi enum:** non tutti i tipi enum contengono la costante zero, quindi è consigliabile usare il membro enum esplicito.
- **Tipi di interfaccia:** si tratta di una funzionalità di nicchia che deve essere preferibile per citare in modo esplicito il tipo.
- **Tipi di matrice:** le matrici necessitano di una sintassi speciale per fornire la lunghezza.
- **dinamica:** non è consentito `new dynamic()`, quindi non è consentito `new()` con `dynamic` come tipo di destinazione.

Sono esclusi anche tutti gli altri tipi non consentiti nella *object_creation_expression* , ad esempio i tipi di puntatore.

Quando il tipo di destinazione è un tipo di valore Nullable, il `new` tipizzato di destinazione verrà convertito nel tipo sottostante invece che nel tipo Nullable.

> **Problema di apertura:** è necessario consentire i delegati e le tuple come tipo di destinazione?

Le regole sopra elencate includono i delegati (un tipo di riferimento) e le Tuple (un tipo struct). Anche se entrambi i tipi sono costruibile, se il tipo è deducebile, è possibile usare una funzione anonima o un valore letterale di tupla.
```cs
(int a, int b) t = new(1, 2); // "new" is redundant
Action a = new(() => {}); // "new" is redundant

(int a, int b) t = new(); // ruled out by "use of struct default constructor"
Action a = new(); // no constructor found

### Miscellaneous

`throw new()` is disallowed.

Target-typed `new` is not allowed with binary operators.

It is disallowed when there is no type to target: unary operators, collection of a `foreach`, in a `using`, in a deconstruction, in an `await` expression, as an anonymous type property (`new { Prop = new() }`), in a `lock` statement, in a `sizeof`, in a `fixed` statement, in a member access (`new().field`), in a dynamically dispatched operation (`someDynamic.Method(new())`), in a LINQ query, as the operand of the `is` operator, as the left operand of the `??` operator,  ...

It is also disallowed as a `ref`.

## Drawbacks
[drawbacks]: #drawbacks

There were some concerns with target-typed `new` creating new categories of breaking changes, but we already have that with `null` and `default`, and that has not been a significant problem.

## Alternatives
[alternatives]: #alternatives

Most of complaints about types being too long to duplicate in field initialization is about *type arguments* not the type itself, we could infer only type arguments like `new Dictionary(...)` (or similar) and infer type arguments locally from arguments or the collection initializer.

## Questions
[questions]: #questions

- Should we forbid usages in expression trees? (no)
- How the feature interacts with `dynamic` arguments? (no special treatment)
- How IntelliSense should work with `new()`? (only when there is a single target-type)

## Design meetings

- [LDM-2017-10-18](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-10-18.md#100)
- [LDM-2018-05-21](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-05-21.md)
- [LDM-2018-06-25](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-06-25.md)
- [LDM-2018-08-22](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-08-22.md#target-typed-new)
- [LDM-2018-10-17](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md)
