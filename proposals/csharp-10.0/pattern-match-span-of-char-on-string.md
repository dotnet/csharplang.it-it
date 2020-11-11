---
ms.openlocfilehash: 02ebf35fc43fee1183be5d0118e03e02b17f41a9
ms.sourcegitcommit: 38908f71e529669d1e049db6c6cce2cad65c1727
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94498310"
---
# <a name="pattern-match-spanchar-on-a-constant-string"></a>Criterio `Span<char>` di corrispondenza in una stringa costante

* [x] proposto
* [x] prototipo: completato
* Implementazione di [x]: in corso
* [] Specifica: non avviata

## <a name="summary"></a>Riepilogo
[summary]: #summary

Consentire ai criteri di ricerca `Span<char>` e a `ReadOnlySpan<char>` su una stringa costante.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Per le prestazioni, l'utilizzo di `Span<char>` e `ReadOnlySpan<char>` è preferibile rispetto alla stringa in molti scenari. Il Framework ha aggiunto molte nuove API che consentono di usare `ReadOnlySpan<char>` al posto di un `string` .

Un'operazione comune sulle stringhe consiste nell'usare un'opzione per verificare se si tratta di un valore specifico e il compilatore ottimizza tale opzione. Tuttavia, non è attualmente possibile eseguire la stessa operazione in modo `ReadOnlySpan<char>` efficiente, oltre a implementare manualmente l'opzione e l'ottimizzazione.

Per incoraggiare l'adozione di `ReadOnlySpan<char>` , è possibile utilizzare criteri di ricerca, in una costante, in modo da consentirne l' `ReadOnlySpan<char>` `string` utilizzo anche in un'opzione.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

La [specifica](../csharp-7.0/pattern-matching.md#constant-pattern) per i modelli costanti viene modificata come segue (l'aggiunta proposta viene visualizzata in grassetto):

> Un criterio costante verifica il valore di un'espressione rispetto a un valore costante. La costante può essere qualsiasi espressione costante, ad esempio un valore letterale, il nome di una variabile dichiarata `const` , una costante di enumerazione o un' `typeof` espressione e così via.
>
> Se e *e* *c* sono di tipo integrale, il modello viene considerato corrispondente se il risultato dell'espressione `e == c` è `true` .
>
> **Se *e* è di tipo `System.Span<char>` o `System.ReadOnlySpan<char>` e *c* è una stringa costante e *c* non ha un valore costante di `null` , il modello viene considerato corrispondente se `System.MemoryExtensions.SequenceEqual<char>(e, System.MemoryExtensions.AsSpan(c))` restituisce `true` .**
> 
> In caso contrario, il modello viene considerato corrispondente se `object.Equals(e, c)` restituisce `true` . In questo caso si tratta di un errore in fase di compilazione se il tipo statico di *e* non è *compatibile* con il tipo della costante.

`System.Span<T>` e `System.ReadOnlySpan<T>` vengono confrontati in base al nome, devono essere `ref struct` s e possono essere definiti all'esterno di corlib. `System.MemoryExtensions` corrisponde al nome e può essere definito all'esterno di corlib. La firma di `System.MemoryExtensions.SequenceEqual` deve corrispondere `public static bool SequenceEqual<T>(System.Span<T>, System.ReadOnlySpan<T>)` rispettivamente a e `public static bool SequenceEqual<T>(System.ReadOnlySpan<T>, System.ReadOnlySpan<T>)` la firma di `System.MemoryExtensions.AsSpan` deve corrispondere a `public static System.ReadOnlySpan<char> AsSpan(string)` . I metodi con parametri facoltativi sono esclusi dalla considerazione.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

nessuno

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

nessuno

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

nessuno

## <a name="design-meetings"></a>Riunioni di progettazione

https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-10-07.md#readonlyspanchar-patterns
