---
ms.openlocfilehash: 3d10cacef98e802333c8cbe65edb93c19c74cabf
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484496"
---
# <a name="null-conditional-await"></a>null-await condizionale

* [x] proposto
* [] Prototipo: nessuno
* [] Implementazione: nessuna
* [] Specifica: avviata, di seguito

## <a name="summary"></a>Summary
[summary]: #summary

Supporta un'espressione nel formato `await? e`, che attende `e` se è non null. in caso contrario, restituisce `null`.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Si tratta di un modello di codifica comune e questa funzionalità avrebbe una buona sinergia con gli operatori esistenti di propagazione di valori null e con Unione null.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Viene aggiunto un nuovo modulo del *await_expression*:

```antlr
await_expression
    : 'await' '?' unary_expression
    ;
```

L'operatore `await` condizionale null attende il proprio operando solo se l'operando è diverso da null. In caso contrario, il risultato dell'applicazione dell'operatore è null.

Il tipo del risultato viene calcolato utilizzando le [regole per l'operatore condizionale null](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#null-conditional-operator).

> **Nota:** Se `e` è di tipo `Task`, `await? e;` non esegue alcuna operazione se `e` è `null`e await `e` se non è `null`.
>
> Se `e` è di tipo `Task<K>` dove `K` è un tipo di valore, `await? e` produrrebbe un valore di tipo `K?`.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Come per qualsiasi funzionalità del linguaggio, è necessario stabilire se la complessità aggiuntiva al linguaggio venga ripagata in base a una maggiore chiarezza offerta C# al corpo dei programmi che trarrebbero vantaggio dalla funzionalità.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

Sebbene richieda codice standard, gli utilizzi di questo operatore possono spesso essere sostituiti da un'espressione simile `(e == null) ? null : await e` o un'istruzione come `if (e != null) await e`.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

- [] Richiede la revisione di LDM

## <a name="design-meetings"></a>Riunioni di progettazione

No.
