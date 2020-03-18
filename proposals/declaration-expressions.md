---
ms.openlocfilehash: d2064ec1637e50962262c9380281abd5e1711402
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484580"
---
# <a name="declaration-expressions"></a>Espressioni di dichiarazione

Supportare le assegnazioni di dichiarazione come espressioni.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Consente l'inizializzazione nel punto della dichiarazione in più casi, semplificando il codice e consentendo l'uso di `var`.

```csharp
SpecialType ReferenceType =>
    (var st = _type.SpecialType).IsValueType() ? SpecialType.None : st;
```

Consente le dichiarazioni per `ref` argomenti, in modo analogo a `out var`.

```csharp
Convert(source, destination, ref List<Diagnostic> diagnostics = null);
```

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Le espressioni vengono estese per includere l'assegnazione di dichiarazione. La precedenza è uguale a quella dell'assegnazione.

```antlr
expression
    : non_assignment_expression
    | assignment
    | declaration_assignment_expression // new
    ;
declaration_assignment_expression // new
    : declaration_expression '=' local_variable_initializer
    ;
declaration_expression // C# 7.0
    | type variable_designation
    ;
```

L'assegnazione della dichiarazione è di un singolo oggetto locale.

Il tipo di un'espressione di assegnazione di dichiarazione è il tipo della dichiarazione.
Se il tipo è `var`, il tipo dedotto è il tipo dell'espressione di inizializzazione. 

L'espressione di assegnazione di dichiarazione può essere un l-value, per `ref` valori di argomento in particolare.

Se l'espressione di assegnazione di dichiarazione dichiara un tipo di valore e l'espressione è un r-value, il valore dell'espressione è una copia.

L'espressione di assegnazione di dichiarazione può dichiarare un `ref` local.
Si verifica un'ambiguità quando `ref` viene usato per un'espressione di dichiarazione in un argomento `ref`.
L'inizializzatore di variabile locale determina se la dichiarazione è un `ref` local.

```csharp
F(ref int x = IntFunc());    // int x;
F(ref int y = RefIntFunc()); // ref int y;
```

L'ambito di variabili locali dichiarate nelle espressioni di assegnazione di dichiarazione è lo stesso ambito delle espressioni di dichiarazione corrispondenti da C # 7.0.

Si tratta di un errore in fase di compilazione per fare riferimento a un oggetto locale nel testo che precede l'espressione di dichiarazione.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives
Nessuna modifica. Questa funzionalità è solo una forma abbreviata sintattica.

Espressioni di sequenza più generali: vedere [#377](https://github.com/dotnet/csharplang/issues/377).

Per consentire l'uso di `var` in più casi, consentire la dichiarazione e l'assegnazione separate di `var` variabili locali e dedurre il tipo dalle assegnazioni da tutti i percorsi del codice.

## <a name="see-also"></a>Vedere anche
[see-also]: #see-also
Vedere espressione di dichiarazione di base in [#595](https://github.com/dotnet/csharplang/issues/595).

Vedere Dichiarazione di decostruzione nella funzionalità di [decostruzione](https://github.com/dotnet/roslyn/blob/master/docs/features/deconstruction.md) .
