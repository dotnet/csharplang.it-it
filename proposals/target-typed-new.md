---
ms.openlocfilehash: f000dda7eeb1c4f17c26f94c326a12a9d0014288
ms.sourcegitcommit: 1e1c7c72b156e2fbc54d6d6ac8d21bca9934d8d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80281970"
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

## <a name="specification"></a>Specifiche
[design]: #detailed-design

Viene accettata una nuova forma sintattica, *target_typed_new* della *object_creation_expression* , in cui il *tipo* è facoltativo.

```antlr
object_creation_expression
    : 'new' type '(' argument_list? ')' object_or_collection_initializer?
    | 'new' type object_or_collection_initializer
    | target_typed_new
    ;
target_typed_new
    : 'new' '(' argument_list? ')' object_or_collection_initializer?
    ;
```

Un'espressione *target_typed_new* non dispone di un tipo. Tuttavia, esiste una nuova *conversione di creazione di oggetti* che è una conversione implicita da Expression, esistente da un *target_typed_new* a ogni tipo.

Dato un tipo di destinazione `T`, il tipo `T0` è il tipo sottostante di `T`se `T` è un'istanza di `System.Nullable`. In caso contrario `T0` viene `T`. Il significato di un'espressione *target_typed_new* convertita nel tipo `T` corrisponde al significato di un *object_creation_expression* corrispondente che specifica `T0` come tipo.

Si tratta di un errore in fase di compilazione se una *target_typed_new* viene utilizzata come operando di un operatore unario o binario o se viene utilizzata quando non è soggetta alla *conversione della creazione*di un oggetto.

> **Problema di apertura:** è necessario consentire i delegati e le tuple come tipo di destinazione?

Le regole sopra elencate includono i delegati (un tipo di riferimento) e le Tuple (un tipo struct). Anche se entrambi i tipi sono costruibile, se il tipo è deducebile, è possibile usare una funzione anonima o un valore letterale di tupla.
```cs
(int a, int b) t = new(1, 2); // "new" is redundant
Action a = new(() => {}); // "new" is redundant

(int a, int b) t = new(); // OK; same as (0, 0)
Action a = new(); // no constructor found
```

### <a name="miscellaneous"></a>Varie

Di seguito sono riportate le conseguenze della specifica:

- `throw new()` è consentito (il tipo di destinazione è `System.Exception`)
- Il `new` tipizzato per la destinazione non è consentito con gli operatori binari.
- Non è consentito quando non è disponibile alcun tipo di destinazione: operatori unari, raccolta di un `foreach`, in una `using`, in una decostruzione, in un'espressione `await` come proprietà di tipo anonimo (`new { Prop = new() }`), in un'istruzione `lock`, in un `sizeof`, in un'istruzione `fixed`, in un accesso ai membri (`new().field`), in un'operazione inviata dinamicamente (`someDynamic.Method(new())`), in una query LINQ, come operando dell'operatore `is`, come operando sinistro dell'operatore `??` ,  ...
- Non è inoltre consentito come `ref`.
- I tipi di tipi seguenti non sono consentiti come destinazioni della conversione
  - **Tipi enum:** `new()` funzionerà (`new Enum()` funziona per fornire il valore predefinito), ma `new(1)` non funzionerà perché i tipi enum non dispongono di un costruttore.
  - **Tipi di interfaccia:** Questa operazione funziona allo stesso modo dell'espressione di creazione corrispondente per i tipi COM.
  - **Tipi di matrice:** le matrici necessitano di una sintassi speciale per fornire la lunghezza.    
  - **dinamica:** non è consentito `new dynamic()`, quindi non è consentito `new()` con `dynamic` come tipo di destinazione.
  - **Tuple:** Hanno lo stesso significato della creazione di un oggetto usando il tipo sottostante.
  - Sono esclusi anche tutti gli altri tipi non consentiti nella *object_creation_expression* , ad esempio i tipi di puntatore.   

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
- [LDM-2020-03-25](https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md)
