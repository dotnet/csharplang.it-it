---
ms.openlocfilehash: 07b4afe4a3fcbf10c978f05e642dfd8a47d53ea5
ms.sourcegitcommit: 194a043db72b9244f8db45db326cc82de6cec965
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80217203"
---

# <a name="target-typed-new-expressions"></a>Espressioni di `new` tipizzate come destinazione

* [x] proposto
* [prototipo](https://github.com/alrz/roslyn/tree/features/target-typed-new) [x]
* [] Implementazione
* [] Specifica

## <a name="summary"></a>Summary
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
```

### <a name="miscellaneous"></a>Varie

`throw new()` non è consentito.

Il `new` tipizzato per la destinazione non è consentito con gli operatori binari.

Non è consentito quando non è disponibile alcun tipo di destinazione: operatori unari, raccolta di un `foreach`, in una `using`, in una decostruzione, in un'espressione `await` come proprietà di tipo anonimo (`new { Prop = new() }`), in un'istruzione `lock`, in un `sizeof`, in un'istruzione `fixed`, in un accesso ai membri (`new().field`), in un'operazione inviata dinamicamente (`someDynamic.Method(new())`), in una query LINQ, come operando dell'operatore `is`, come operando sinistro dell'operatore `??` ,  ...

Non è inoltre consentito come `ref`.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Si sono verificati alcuni problemi con tipi di destinazione `new` la creazione di nuove categorie di modifiche di rilievo, ma è già presente con `null` e `default`e questo non è stato un problema significativo.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

La maggior parte dei reclami relativi ai tipi troppo lunghi per duplicare l'inizializzazione del campo riguarda gli *argomenti di tipo* , non il tipo stesso, è possibile dedurre solo gli argomenti di tipo come `new Dictionary(...)` (o simile) e dedurre gli argomenti di tipo localmente dagli argomenti o dall'inizializzatore di raccolta.

## <a name="questions"></a>Domande
[questions]: #questions

- È consigliabile proibire gli utilizzi negli alberi delle espressioni? non
- Interazione tra la funzionalità e gli argomenti `dynamic` (nessun trattamento speciale)
- Funzionamento di IntelliSense con `new()` (solo quando è presente un solo tipo di destinazione)

## <a name="design-meetings"></a>Riunioni di progettazione

- [LDM-2017-10-18](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-10-18.md#100)
- [LDM-2018-05-21](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-05-21.md)
- [LDM-2018-06-25](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-06-25.md)
- [LDM-2018-08-22](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-08-22.md#target-typed-new)
- [LDM-2018-10-17](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md)
