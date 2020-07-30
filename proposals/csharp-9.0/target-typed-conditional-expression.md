---
ms.openlocfilehash: f5a97f699b552094b8b21aeceabe2aac0a478d98
ms.sourcegitcommit: a88d56e3131d7a94c65e637c276379541a3cd491
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87434457"
---
# <a name="target-typed-conditional-expression"></a>Espressione condizionale tipizzata di destinazione

## <a name="conditional-expression-conversion"></a>Conversione di espressioni condizionali

Per un'espressione condizionale `c ? e1 : e2` , quando

1. non esiste alcun tipo comune per `e1` e `e2` , o
2. per cui esiste un tipo comune, ma una delle espressioni `e1` o `e2` Nessuna conversione implicita a tale tipo

viene definita una nuova conversione implicita di *espressioni condizionali* che consente una conversione implicita dall'espressione condizionale a qualsiasi tipo `T` per il quale è presente una conversione da espressione da `e1` a `T` e anche da `e2` a `T` .  Se un'espressione condizionale non dispone di un tipo comune tra `e1` e `e2` né è soggetto a una conversione di *espressione condizionale*, è un errore.

## <a name="better-conversion-from-expression"></a>Migliore conversione dall'espressione

Modifiche apportate

> #### <a name="better-conversion-from-expression"></a>Migliore conversione dall'espressione
> 
> Una conversione implicita `C1` che converte da un'espressione `E` a un tipo `T1` e una conversione implicita `C2` che converte da un'espressione `E` a un tipo `T2` , `C1` è una ***conversione migliore*** rispetto a `C2` se non `E` corrisponde esattamente a e almeno `T2` una delle seguenti condizioni:
> 
> * `E`corrisponde esattamente `T1` ([espressione corrispondente esattamente](../../spec/expressions.md#exactly-matching-expression))
> * `T1`è una destinazione di conversione migliore rispetto a `T2` ([migliore destinazione di conversione](../../spec/expressions.md#better-conversion-target))

in

> #### <a name="better-conversion-from-expression"></a>Migliore conversione dall'espressione
> 
> Una conversione implicita `C1` che converte da un'espressione `E` a un tipo `T1` e una conversione implicita `C2` che converte da un'espressione `E` a un tipo `T2` , `C1` è una ***conversione migliore*** rispetto a `C2` se non `E` corrisponde esattamente a e almeno `T2` una delle seguenti condizioni:
> 
> * `E`corrisponde esattamente `T1` ([espressione corrispondente esattamente](../../spec/expressions.md#exactly-matching-expression))
> * **`C1`non è una *conversione di espressione condizionale* ed `C2` è una * conversione di espressione condizionale * * *.
> * `T1`è una destinazione di conversione migliore rispetto a `T2` ([migliore destinazione di conversione](../../spec/expressions.md#better-conversion-target)) * * e `C1` e `C2` sono entrambe *conversioni di espressioni condizionali* o nessuna delle due è una * conversione di espressione condizionale * * *.

## <a name="cast-expression"></a>Espressione cast

La specifica del linguaggio C# corrente indica

> Un *cast_expression* del form `(T)E` , dove `T` è un *tipo* e `E` è un *unary_expression*, esegue una conversione esplicita ([conversioni esplicite](../../spec/conversions.md#explicit-conversions)) del valore di `E` nel tipo `T` .

In presenza della conversione dell' *espressione condizionale* può essere presente più di una possibile conversione da `E` a `T` . Con l'aggiunta della *conversione di espressioni condizionali*, si preferisce qualsiasi altra conversione a una *conversione di espressione*condizionale e si usa la *conversione dell'espressione condizionale* solo come ultima risorsa.

## <a name="design-notes"></a>Note di progettazione

Il motivo per cui la modifica alla *conversione migliore da Expression* è la gestione di un caso simile al seguente:

```csharp
M(b ? 1 : 2);

void M(short);
void M(long);
```

Questo approccio presenta due svantaggi limitati.  In primo luogo, non è esattamente uguale all'espressione switch:

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

Il motivo per le note sull'espressione cast è quello di gestire un caso simile al seguente:

```csharp
_ = (short)(b ? 1 : 2);
```

Questo programma utilizza attualmente la conversione esplicita da `int` a `short` e si desidera mantenere il significato della lingua corrente del programma.  La modifica potrebbe non essere osservabile in fase di esecuzione, ma con il programma seguente la modifica sarebbe osservabile:

```csharp
_ = (A)(b ? c : d);
```

dove `c` è di tipo `C` , `d` è di tipo `D` ed esiste una conversione implicita definita dall'utente da a e una conversione implicita definita dall'utente da a `C` `D` `D` `A` e una conversione implicita definita dall'utente da `C` a `A` . Se questo codice viene compilato prima di C# 9,0, quando `b` è true viene convertito da `c` a `D` then a `A` . Se si usa la *conversione dell'espressione condizionale*, quando `b` è true viene convertito da `c` a `A` direttamente, che esegue una sequenza diversa di codice utente. Quindi consideriamo la *conversione dell'espressione condizionale* come ultima risorsa in un cast, per mantenere il comportamento esistente.
