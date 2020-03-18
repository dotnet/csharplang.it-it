---
ms.openlocfilehash: 6695664c3d5ca73f66e792b7ce2ec9993aceea05
ms.sourcegitcommit: 42ef673ecc883009c865f8384249881a546df216
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2019
ms.locfileid: "79485154"
---
# <a name="lambda-discard-parameters"></a>Parametri di eliminazione lambda

## <a name="summary"></a>Summary

Consenti le eliminazioni (`_`) da utilizzare come parametri di espressioni lambda e metodi anonimi.
Ad esempio:
- espressioni lambda: `(_, _) => 0`, `(int _, int _) => 0`
- metodi anonimi: `delegate(int _, int _) { return 0; }`

## <a name="motivation"></a>Motivazione

Non è necessario denominare i parametri inutilizzati. Lo scopo delle eliminazioni è chiaro, ovvero non sono usate/scartate.

## <a name="detailed-design"></a>Progettazione dettagliata

[Parametri del metodo](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#method-parameters) Nell'elenco di parametri di un metodo lambda o anonimo con più di un parametro denominato `_`, tali parametri sono parametri di scarto.
Nota: se un singolo parametro è denominato `_`, si tratta di un parametro normale per motivi di compatibilità con le versioni precedenti.

Ignora parametri non introduce alcun nome in alcun ambito.
Si noti che ciò implica che non vengono nascosti i nomi di `_` (carattere di sottolineatura).

[Nomi semplici](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#simple-names) Se `K` è zero e il *simple_name* viene visualizzato all'interno di un *blocco* e se il *blocco*(o un *blocco*di inclusione) spazio di dichiarazione della variabile locale ([dichiarazioni](basic-concepts.md#declarations)) contiene una variabile locale, un parametro (ad eccezione dei parametri di scarto) o una costante con nome `I`, il *simple_name* fa riferimento a tale variabile locale, parametro o costante ed è classificato come una variabile o un valore.

[Ambiti](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#scopes) Ad eccezione dei parametri di scarto, l'ambito di un parametro dichiarato in una *lambda_expression* ([espressioni di funzione anonima](expressions.md#anonymous-function-expressions)) è il *anonymous_function_body* di tale *lambda_expression* ad eccezione dei parametri di scarto, l'ambito di un parametro dichiarato in una *anonymous_method_expression* ([espressioni di funzione anonima](expressions.md#anonymous-function-expressions)) è il *blocco* di tale *anonymous_method_expression*.

## <a name="related-spec-sections"></a>Sezioni specifiche correlate
- [Parametri corrispondenti](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#corresponding-parameters)
