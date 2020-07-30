---
ms.openlocfilehash: bdefce3af78e44136dc2682e9e262cc8d6fae77b
ms.sourcegitcommit: a88d56e3131d7a94c65e637c276379541a3cd491
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87434485"
---
# <a name="lambda-discard-parameters"></a>Parametri di eliminazione lambda

## <a name="summary"></a>Summary

Consente di utilizzare le variabili Discard ( `_` ) come parametri di espressioni lambda e metodi anonimi.
Ad esempio:
- espressioni lambda: `(_, _) => 0` ,`(int _, int _) => 0`
- metodi anonimi:`delegate(int _, int _) { return 0; }`

## <a name="motivation"></a>Motivazione

Non è necessario denominare i parametri inutilizzati. Lo scopo delle eliminazioni è chiaro, ovvero non sono usate/scartate.

## <a name="detailed-design"></a>Progettazione dettagliata

[Parametri del metodo](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#method-parameters) Nell'elenco di parametri di un metodo lambda o anonimo con più di un parametro denominato `_` , tali parametri sono parametri di scarto.
Nota: se viene denominato un solo parametro `_` , si tratta di un parametro normale per motivi di compatibilità con le versioni precedenti.

Ignora parametri non introduce alcun nome in alcun ambito.
Si noti che ciò implica che non vengono `_` nascosti i nomi (carattere di sottolineatura).

[Nomi semplici](../../spec/expressions.md#simple-names) Se `K` è zero e il *simple_name* viene visualizzato all'interno di un *blocco* e se il *blocco*(o un *blocco*di inclusione) spazio di dichiarazione della variabile locale ([dichiarazioni](../../spec/basic-concepts.md#declarations)) contiene una variabile locale, parametro (ad eccezione dei parametri di scarto) o costante con nome `I` , il *simple_name* fa riferimento alla variabile locale, al parametro o alla costante ed è classificato come variabile o valore.

[Ambiti](../../spec/basic-concepts.md#scopes) Ad eccezione dei parametri di scarto, l'ambito di un parametro dichiarato in una *lambda_expression* ([espressioni di funzione anonima](../../spec/expressions.md#anonymous-function-expressions)) è il *anonymous_function_body* di tale *lambda_expression* ad eccezione dei parametri di scarto, l'ambito di un parametro dichiarato in una *anonymous_method_expression* ([espressioni di funzione anonima](../../spec/expressions.md#anonymous-function-expressions)) è il *blocco* di tale *anonymous_method_expression*.

## <a name="related-spec-sections"></a>Sezioni specifiche correlate
- [Parametri corrispondenti](../../spec/expressions.md#corresponding-parameters)
