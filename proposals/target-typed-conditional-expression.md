---
ms.openlocfilehash: cd73a3d7289205f65f5144a98d32da06dfed3efc
ms.sourcegitcommit: e355841daad8c4672fae6a49c98653952d89a9cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83775420"
---
# <a name="target-typed-conditional-expression"></a>Espressione condizionale tipizzata di destinazione

Per un'espressione condizionale `c ? e1 : e2` , quando

1. non esiste alcun tipo comune per `e1` e `e2` , o
2. per cui esiste un tipo comune, ma una delle espressioni `e1` o `e2` Nessuna conversione implicita a tale tipo

viene definita una nuova *conversione di espressione condizionale* che consente una conversione implicita dall'espressione condizionale a qualsiasi tipo `T` per il quale esiste una conversione da espressione da `e1` a `T` e anche da `e2` a `T` .  Se un'espressione condizionale non dispone di un tipo comune tra `e1` e `e2` né è soggetto a una conversione di *espressione condizionale*, è un errore.

### <a name="open-issues"></a>Problemi non risolti

Si vuole estendere la tipizzazione della destinazione ai casi in cui l'espressione condizionale ha un tipo comune per `e1` e `e2` ma non esiste alcuna conversione da tale tipo comune al tipo di destinazione. In questo modo, la tipizzazione della destinazione dell'espressione condizionale verrà allineata alla tipizzazione della destinazione dell'espressione switch. Tuttavia, si tratta di una modifica sostanziale:

```csharp
M(b ? 1 : 2); // calls M(long) without this feature; calls M(short) with this feature

void M(short);
void M(long);
```

È possibile ridurre l'ambito della modifica sostanziale modificando le regole per una *migliore conversione dall'espressione*: la conversione da un'espressione condizionale a T1 è una conversione migliore dall'espressione rispetto alla conversione in T2 se la conversione a T1 non è una *conversione di espressione condizionale* e la conversione a T2 è una *conversione di espressione condizionale*.  Che risolve la modifica di rilievo nel programma precedente (chiama `M(long)` con o senza questa funzionalità). Questo approccio presenta due svantaggi limitati.  In primo luogo, non è esattamente uguale all'espressione switch:

```csharp
M(b ? 1 : 2); // calls M(long)
M(b switch { true => 1, false => 2 }); // calls M(short)
```

Si tratta comunque di una modifica sostanziale, ma è meno probabile che il relativo ambito influisca sui programmi reali:

```csharp
M(b ? 1 : 2, 1); // calls M(long, long) without this feature; ambiguous with this feature.

M(short, short);
M(long, long);
```

Questa operazione diventa ambigua perché la conversione a `long` è migliore per il primo argomento, perché non usa la *conversione dell'espressione condizionale*, ma la conversione a `short` è migliore per il secondo argomento (perché `short` è un *obiettivo di conversione migliore* di `long` ). Questa modifica di rilievo sembra meno grave perché non modifica automaticamente il comportamento di un programma esistente.

Se si decide di apportare questa modifica alla proposta, si modificherà

> #### <a name="better-conversion-from-expression"></a>Migliore conversione dall'espressione
> 
> Una conversione implicita `C1` che converte da un'espressione `E` a un tipo `T1` e una conversione implicita `C2` che converte da un'espressione `E` a un tipo `T2` , `C1` è una ***conversione migliore*** rispetto a `C2` se non `E` corrisponde esattamente a e almeno `T2` una delle seguenti condizioni:
> 
> * `E`corrisponde esattamente `T1` ([espressione corrispondente esattamente](expressions.md#exactly-matching-expression))
> * `T1`è una destinazione di conversione migliore rispetto a `T2` ([migliore destinazione di conversione](expressions.md#better-conversion-target))

to

> #### <a name="better-conversion-from-expression"></a>Migliore conversione dall'espressione
> 
> Una conversione implicita `C1` che converte da un'espressione `E` a un tipo `T1` e una conversione implicita `C2` che converte da un'espressione `E` a un tipo `T2` , `C1` è una ***conversione migliore*** rispetto a `C2` se non `E` corrisponde esattamente a e almeno `T2` una delle seguenti condizioni:
> 
> * `E`corrisponde esattamente `T1` ([espressione corrispondente esattamente](expressions.md#exactly-matching-expression))
> * **`C1`non è una *conversione di espressione condizionale* ed `C2` è una * conversione di espressione condizionale * * *.
> * `T1`è una destinazione di conversione migliore rispetto a `T2` ([migliore destinazione di conversione](expressions.md#better-conversion-target)) * * e `C1` e `C2` sono entrambe *conversioni di espressioni condizionali* o nessuna delle due è una * conversione di espressione condizionale * * *.
