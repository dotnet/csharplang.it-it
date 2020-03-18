---
ms.openlocfilehash: 032cb8590a0b6e83f8ab6201e10720f1b254c605
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484531"
---
# <a name="nullable-enhanced-common-type"></a>Tipo comune migliorato Nullable

* [x] proposto
* [] Prototipo: nessuno
* [] Implementazione: nessuna
* [] Specifica: vedere di seguito

## <a name="summary"></a>Summary
[summary]: #summary

Si verifica una situazione in cui i risultati dell'algoritmo di tipo comune corrente sono controintuitivi e il programmatore aggiunge un cast ridondante al codice. Con questa modifica, un'espressione come `condition ? 1 : null` comporterebbe un valore di tipo `int?`e un'espressione, ad esempio `condition ? x : 1.0` dove `x` è di tipo `int?` comporterebbe un valore di tipo `double?`.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Si tratta di una ragione comune di ciò che si ritiene al programmatore come un codice standard inutile.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Viene modificata la specifica per [trovare il tipo comune migliore di un set di espressioni](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) per influenzare le situazioni seguenti:

- Se un'espressione è di un tipo di valore non nullable `T` e l'altro è un valore letterale null, il risultato è di tipo `T?`.
- Se un'espressione è di un tipo di valore Nullable `T?` e l'altra è di un tipo di valore `U`ed esiste una conversione implicita da `T` a `U`, il risultato è di tipo `U?`.

Questa operazione dovrebbe influire sugli aspetti seguenti del linguaggio:

- [espressione ternaria](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#conditional-operator)
- [espressione di creazione della matrice](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#array-creation-expressions) tipizzata in modo implicito
- deduzione del [tipo restituito di un'espressione lambda](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#inferred-return-type) per l'inferenza del tipo
- casi che coinvolgono generics, ad esempio la chiamata di `M<T>(T a, T b)` come `M(1, null)`.

Più precisamente, si modificano le sezioni seguenti della specifica (inserimenti in grassetto, eliminazioni in barrate):

> #### <a name="output-type-inferences"></a>Inferenza del tipo di output
> 
> Un' *inferenza del tipo di output* viene eseguita *da* un'espressione `E` *a* un tipo `T` nel modo seguente:
> 
> *  Se `E` è una funzione anonima con tipo restituito dedotto `U` ([tipo restituito derivato](expressions.md#inferred-return-type)) e `T` è un tipo delegato o un tipo di albero delle espressioni con tipo restituito `Tb`, viene eseguita un' *inferenza ad associazione inferiore* ([inferenza con associazione](expressions.md#lower-bound-inferences)inferiore) *da* `U` *a* `Tb`.
> *  In caso contrario, se `E` è un gruppo di metodi e `T` è un tipo delegato o un tipo di albero delle espressioni con tipi di parametro `T1...Tk` e tipo restituito `Tb`e la risoluzione dell'overload di `E` con i tipi `T1...Tk` genera un solo metodo con tipo restituito `U`, viene eseguita un' *inferenza con associazione inferiore* *da* `U` *a* `Tb`.
> *  \* * In caso contrario, se `E` è un'espressione con tipo di valore Nullable `U?`, viene eseguita un' *inferenza con associazione inferiore* *da* `U` *a* `T` e viene aggiunto un *limite null* a `T`. **
> *  In caso contrario, se `E` è un'espressione con tipo `U`, viene eseguita un' *inferenza con associazione inferiore* *da* `U` *a* `T`.
> *  **In caso contrario, se `E` è un'espressione costante con valore `null`, viene aggiunto un *limite null* a `T`** 
> *  In caso contrario, non vengono eseguite inferenze.

> #### <a name="fixing"></a>Fissaggio
> 
> Una variabile di tipo *unfixed* `Xi` con un set di limiti viene *fissata* come indicato di seguito:
> 
> *  Il set di *tipi candidati* `Uj` inizia come set di tutti i tipi nel set di limiti per `Xi`.
> *  Si esamina quindi ogni limite per `Xi` a sua volta: per ogni `U` con binding esatto di `Xi` tutti i tipi `Uj` che non sono identici a `U` vengono rimossi dal set candidato. Per ogni `U` con binding inferiore di `Xi` tutti i tipi `Uj` a cui *non* è presente una conversione implicita da `U` vengono rimossi dal set di candidati. Per ogni `U` limite superiore di `Xi` tutti i tipi `Uj` da cui *non* esiste una conversione implicita a `U` vengono rimossi dal set di candidati.
> *  Se tra i tipi candidati rimanenti `Uj` è presente un tipo univoco `V` da cui esiste una conversione implicita a tutti gli altri tipi candidati, ~~`Xi` viene fissata `V`.~~
>     -  **Se `V` è un tipo di valore ed è associato un valore *null* per `Xi`, `Xi` viene fissata `V?`**
>     -  **In caso contrario `Xi` viene corretto `V`**
> *  In caso contrario, l'inferenza del tipo non riesce.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Potrebbero esserci alcune incompatibilità introdotte da questa proposta.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

No.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

- [] Qual è la gravità dell'incompatibilità introdotta da questa proposta, se presente, e come è possibile moderarla?

## <a name="design-meetings"></a>Riunioni di progettazione

No.
