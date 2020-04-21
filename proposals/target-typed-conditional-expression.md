---
ms.openlocfilehash: 80ccdb75e2f5a022e367f3c023ea4464195deaaf
ms.sourcegitcommit: 95f5f86ba2e2a23cd4fb37bd9d1ff690c83d1191
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81647134"
---
# <a name="target-typed-conditional-expression"></a>Espressione condizionale tipizzato di destinazioneTarget-Typed Expression

Per un'espressione `c ? e1 : e2`condizionale, quando

1. non esiste un `e1` tipo `e2`comune per e , o
2. per cui esiste un tipo comune, ma una delle espressioni `e1` o `e2` non ha alcuna conversione implicita in quel tipo

viene definita una nuova conversione di *espressioni condizionali* che `T` consente una conversione implicita `e1` dall'espressione condizionale a qualsiasi tipo per il quale esiste un'espressione di conversione da a `T` e anche da `e2` a `T`.  Si tratta di un errore se un'espressione condizionale non ha un tipo comune tra `e1` e `e2` né è soggetta a una *conversione*di espressione condizionale .

### <a name="open-issues"></a>Open Issues

Desideriamo estendere questa digitazione di destinazione ai casi in `e1` `e2` cui l'espressione condizionale ha un tipo comune per e ma non vi è alcuna conversione da tale tipo comune al tipo di destinazione. Ciò porterebbe la digitazione di destinazione dell'espressione condizionale in allineamento della digitazione di destinazione dell'espressione switch. Tuttavia siamo preoccupati che sarebbe un cambiamento radicale:

```csharp
M(b ? 1 : 2); // calls M(long) without this feature; calls M(short) with this feature

void M(short);
void M(long);
```

È possibile ridurre l'ambito della modifica sostanziale modificando le regole per una *migliore conversione dall'espressione*: la conversione da un'espressione condizionale a T1 è una conversione migliore *dall'espressione*rispetto alla conversione in T2 se la conversione in T1 non è una *conversione* di espressione condizionale e la conversione in T2 è una conversione di espressione condizionale .  Che risolve il cambiamento sostanziale nel `M(long)` programma di cui sopra (chiama con o senza questa funzione). Questo approccio ha due piccoli aspetti negativi.  In primo luogo, non è proprio uguale all'espressione switch:

```csharp
M(b ? 1 : 2); // calls M(long)
M(b switch { true => 1, false => 2 }); // calls M(short)
```

Questo è ancora un cambiamento sostanziale, ma il suo campo di applicazione è meno probabile che influenzano i programmi reali:

```csharp
M(b ? 1 : 2, 1); // calls M(long, long) without this feature; ambiguous with this feature.

M(short, short);
M(long, long);
```

Questo diventa ambiguo `long` perché la conversione in è migliore per il primo argomento `short` (perché non utilizza la `short` *conversione dell'espressione condizionale*), ma la conversione in è migliore per il secondo argomento (perché è una destinazione di *conversione migliore* di `long`). Questa modifica di interruzione sembra meno grave perché non modifica silenziosamente il comportamento di un programma esistente.

Se scegliamo di apportare questa modifica alla proposta, cambieremmo

> #### <a name="better-conversion-from-expression"></a>Migliore conversione dall'espressione
> 
> Data una `C1` conversione implicita `E` che esegue `T1`la conversione `C2` da un'espressione `E` a `T2`un `C1` tipo e una conversione implicita che esegue la conversione da un'espressione a un tipo , è una ***conversione migliore*** rispetto a `C2` if `E` non corrisponde `T2` esattamente e almeno uno degli elementi seguenti è valido:
> 
> * `E`corrisponde `T1` esattamente a ([Esattamente corrispondenti a Expression](expressions.md#exactly-matching-expression))
> * `T1`è un obiettivo `T2` di conversione migliore di ([Migliore obiettivo](expressions.md#better-conversion-target)di conversione )

to

> #### <a name="better-conversion-from-expression"></a>Migliore conversione dall'espressione
> 
> Data una `C1` conversione implicita `E` che esegue `T1`la conversione `C2` da un'espressione `E` a `T2`un `C1` tipo e una conversione implicita che esegue la conversione da un'espressione a un tipo , è una ***conversione migliore*** rispetto a `C2` if `E` non corrisponde `T2` esattamente e almeno uno degli elementi seguenti è valido:
> 
> * `E`corrisponde `T1` esattamente a ([Esattamente corrispondenti a Expression](expressions.md#exactly-matching-expression))
> * `T1`è una destinazione `T2` di conversione migliore rispetto `C1` a ( Migliore destinazione di[conversione](expressions.md#better-conversion-target)) e non è una *conversione* di espressione condizionale o `C2` è una conversione di espressione condizionale.
