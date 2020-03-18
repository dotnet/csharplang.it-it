---
ms.openlocfilehash: 4f66c0f60d05ed6509a1d0843318a71d1b36c351
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484706"
---
# <a name="pattern-matching-with-generics"></a>criteri di ricerca con generics

* [x] proposto
* [] Prototipo:
* [] Implementazione:
* [] Specifica:

## <a name="summary"></a>Summary
[summary]: #summary

La specifica per l' [operatore C# As esistente](../../spec/expressions.md#the-as-operator) consente di non eseguire alcuna conversione tra il tipo dell'operando e il tipo specificato quando uno dei due è un tipo aperto. Tuttavia, in C# 7 il modello di `Type identifier` richiede la conversione tra il tipo di input e il tipo specificato.

Si consiglia di ridurre questa situazione e di modificare `expression is Type identifier`, oltre a essere consentite nelle condizioni in cui è C# consentito in 7, per consentire anche quando `expression as Type` sarebbe consentito. In particolare, i nuovi casi sono casi in cui il tipo dell'espressione o il tipo specificato è un tipo aperto. 

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

I casi in cui la corrispondenza dei modelli dovrebbe "ovviamente" essere consentita, attualmente non è possibile compilare. Vedere, ad esempio, https://github.com/dotnet/roslyn/issues/16195.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Si modifica il paragrafo nella specifica dei criteri di ricerca (l'aggiunta proposta viene visualizzata in grassetto):

> Alcune combinazioni di tipo statico del lato sinistro e del tipo specificato vengono considerate incompatibili e generano un errore in fase di compilazione. Un valore di tipo statico `E` viene definito *modello compatibile* con il tipo `T` se esiste una conversione di identità, una conversione di un riferimento implicito, una conversione boxing, una conversione di riferimento esplicita o una conversione unboxing da `E` a `T`**oppure se `E` o `T` è un tipo aperto**. Si tratta di un errore in fase di compilazione se un'espressione di tipo `E` non è compatibile con il tipo in un modello di tipo con cui corrisponde.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

No.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

No.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

No.

## <a name="design-meetings"></a>Riunioni di progettazione

LDM ha preso in considerazione questa domanda e si è ritenuta una modifica a livello di correzione di bug. Si tratta di una funzionalità di linguaggio distinta, perché la modifica dopo il rilascio della lingua introduce un'incompatibilità con le versioni successive. Per usare la modifica proposta è necessario che il programmatore specifichi la versione del linguaggio 7,1.
