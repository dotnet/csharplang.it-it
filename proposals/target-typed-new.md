---
ms.openlocfilehash: 4e2a536bab00859b003e8d967cb1927a99a9fa21
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484538"
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

- **Qualsiasi tipo di struct**
- **Qualsiasi tipo di riferimento**
- **Qualsiasi parametro di tipo** con un costruttore o un vincolo `struct`

con le eccezioni seguenti:

- **Tipi enum:** non tutti i tipi enum contengono la costante zero, quindi è consigliabile usare il membro enum esplicito.
- **Tipi di interfaccia:** si tratta di una funzionalità di nicchia che deve essere preferibile per citare in modo esplicito il tipo.
- **Tipi di matrice:** le matrici necessitano di una sintassi speciale per fornire la lunghezza.
- **Costruttore predefinito struct**: questa regola esclude tutti i tipi primitivi e la maggior parte dei tipi di valore. Se si desidera utilizzare il valore predefinito di tali tipi, è possibile scrivere `default`.

Sono esclusi anche tutti gli altri tipi non consentiti nella *object_creation_expression* , ad esempio i tipi di puntatore.

> **Problema di apertura:** è necessario consentire i delegati e le tuple come tipo di destinazione?

Le regole sopra elencate includono i delegati (un tipo di riferimento) e le Tuple (un tipo struct). Anche se entrambi i tipi sono costruibile, se il tipo è deducebile, è possibile usare una funzione anonima o un valore letterale di tupla.
```cs
(int a, int b) t = new(1, 2); // "new" is redundant
Action a = new(() => {}); // "new" is redundant

(int a, int b) t = new(); // ruled out by "use of struct default constructor"
Action a = new(); // no constructor found

var x = new() == (1, 2); // ruled out by "use of struct default constructor"
var x = new(1, 2) == (1, 2) // "new" is redundant
```


> **Problema di apertura:** è necessario consentire `throw new()` con `Exception` come tipo di destinazione?

Attualmente `throw null`, ma non `throw default` (anche se avrebbe lo stesso effetto). D'altra parte, `throw new()` potrebbe essere effettivamente utile come abbreviazione per `throw new Exception(...)`. Si noti che è già consentito dalla specifica corrente. `Exception` è un tipo di riferimento e la specifica dell'istruzione throw indica che l'espressione viene convertita in `Exception`.

> **Problema aperto:** è possibile consentire l'utilizzo di una `new` tipizzata come destinazione con operatori aritmetici e di confronto definiti dall'utente?

Per il confronto, `default` supporta solo gli operatori di uguaglianza (definiti dall'utente e incorporati). Potrebbe essere utile supportare anche altri operatori per `new()`?

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

No.

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
