---
ms.openlocfilehash: 25e95b3ab8c384a7e66e59a7f9422cc9699074d7
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484720"
---
# <a name="infer-tuple-names-aka-tuple-projection-initializers"></a>Deduce i nomi di tupla (noto anche come. inizializzatori di proiezione di tupla)

## <a name="summary"></a>Summary
[summary]: #summary

In molti casi comuni, questa funzionalità consente di omettere i nomi degli elementi di tupla e di inferirli. Invece di digitare `(f1: x.f1, f2: x?.f2)`, ad esempio, i nomi degli elementi "F1" e "F2" possono essere dedotti da `(x.f1, x?.f2)`.

Questo comportamento è parallelo al comportamento dei tipi anonimi, che consentono di dedurre i nomi dei membri durante la creazione. Ad esempio, `new { x.f1, y?.f2 }` dichiara i membri "F1" e "F2".

Questa operazione è particolarmente utile quando si utilizzano le tuple in LINQ:

```csharp
// "c" and "result" have element names "f1" and "f2"
var result = list.Select(c => (c.f1, c.f2)).Where(t => t.f2 == 1); 
```

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

La modifica presenta due parti:

1.  Provare a dedurre un nome candidato per ogni elemento della tupla che non ha un nome esplicito:
    -   Uso delle stesse regole dell'inferenza dei nomi per i tipi anonimi.
        - In C#, questo consente tre casi: `y` (identificatore), `x.y` (accesso semplice ai membri) e `x?.y` (accesso condizionale).
        - In VB Questo consente altri casi, ad esempio `x.y()`.
    -   Rifiuto dei nomi delle tuple riservate (maiuscole/minuscole in C#, senza distinzione tra maiuscole e minuscole in VB) perché non sono consentiti o sono già impliciti. Ad esempio `ItemN`, `Rest`e `ToString`.
    -   Se i nomi dei candidati sono duplicati (senza distinzione tra C#maiuscole e minuscole in VB) all'interno dell'intera tupla, i candidati verranno eliminati.
2.  Durante le conversioni (che controllano e avvisano l'eliminazione dei nomi dai valori letterali di tupla), i nomi derivati non producono alcun avviso. In questo modo si evita di suddividere il codice tupla esistente.

Si noti che la regola per la gestione dei duplicati è diversa da quella per i tipi anonimi. Ad esempio, `new { x.f1, x.f1 }` genera un errore, ma `(x.f1, x.f1)` comunque consentiti (solo senza nomi dedotti). In questo modo si evita di suddividere il codice tupla esistente.

Per coerenza, lo stesso vale per le tuple generate da decostruzione-assegnazioni ( C#in):

```csharp
// tuple has element names "f1" and "f2" 
var tuple = ((x.f1, x?.f2) = (1, 2));
```

Lo stesso vale anche per le tuple VB, usando le regole specifiche di VB per dedurre il nome dall'espressione e i confronti dei nomi senza distinzione tra maiuscole e minuscole.

Quando si usa C# il compilatore 7,1 (o versioni successive) con la versione del linguaggio "7,0", i nomi degli elementi vengono dedotti (nonostante la funzionalità non sia disponibile), ma è presente un errore di utilizzo del sito per il tentativo di accesso. In questo modo si limiteranno le aggiunte del nuovo codice che in seguito si affronterà al problema di compatibilità (descritto di seguito).

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Lo svantaggio principale è che questa operazione introduce un break di C# compatibilità da 7,0:

```csharp
Action y = () => M();
var t = (x: x, y);
t.y(); // this might have previously picked up an extension method called “y”, but would now call the lambda.
```

Questo break accettabile è stato rilevato dal Consiglio di compatibilità, dato che è limitato e l'intervallo di tempo dalle Tuple C# spedite (in 7,0) è breve.

## <a name="references"></a>Riferimenti
- [LDM del 4 aprile 2017](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-05.md#tuple-names)
- [Discussione su GitHub](https://github.com/dotnet/csharplang/issues/370) (grazie @alrz per la presentazione di questo problema)
- [Progettazione di Tuple](https://github.com/dotnet/roslyn/blob/master/docs/features/tuples.md)
