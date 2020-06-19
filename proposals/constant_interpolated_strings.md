---
ms.openlocfilehash: 4400cc56fc52fb36cdd19809ff7fb8f13706c161
ms.sourcegitcommit: eb00bb077e46c46807d804e9e1de3d794fb32405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "85070845"
---
# <a name="constant-interpolated-strings"></a>Stringhe interpolate costanti

* [x] proposto
* [] Prototipo: [non avviato](https://github.com/kevinsun-dev/roslyn/BRANCH_NAME)
* [] Implementazione: [non avviata](https://github.com/dotnet/roslyn/BRANCH_NAME)
* [] Specifica: [non avviata](pr/1)

## <a name="summary"></a>Summary
[summary]: #summary

Consente la generazione di costanti da stringhe interpolate di tipo costante stringa.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Il codice seguente è già valido:
```
public class C
{
    const string S1 = "Hello world";
    const string S2 = "Hello" + " " + "World";
    const string S3 = S1 + " Kevin, welcome to the team!";
}
```
Tuttavia, sono state apportate numerose richieste della community per rendere valide le condizioni seguenti:
```
public class C
{
    const string S1 = $"Hello world";
    const string S2 = $"Hello{" "}World";
    const string S3 = $"{S1} Kevin, welcome to the team!";
}
```
Questa proposta rappresenta il passaggio logico successivo per la generazione di stringhe costanti, in cui la sintassi di stringa esistente che funziona in altre situazioni viene eseguita per le costanti.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Di seguito sono rappresentate le specifiche aggiornate per le espressioni costanti in questa nuova proposta. Le specifiche correnti da cui questo è stato direttamente basato su sono disponibili [qui](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#constant-expressions).

### <a name="constant-expressions"></a>Espressioni costanti

Un *constant_expression* è un'espressione che può essere valutata interamente in fase di compilazione.

```antlr
constant_expression
    : expression
    ;
```

Un'espressione costante deve essere un `null` valore letterale o un valore con uno dei tipi seguenti: `sbyte` ,, `byte` `short` , `ushort` , `int` , `uint` , `long` , `ulong` , `char` , `float` , `double` , `decimal` , `bool` , `object` , `string` o qualsiasi tipo di enumerazione. Nelle espressioni costanti sono consentiti solo i costrutti seguenti:

*  Valori letterali (incluso il `null` valore letterale).
*  Riferimenti ai `const` membri dei tipi di classe e struct.
*  Riferimenti a membri di tipi di enumerazione.
*  Riferimenti a `const` parametri o variabili locali
*  Espressioni secondarie tra parentesi, ovvero espressioni costanti.
*  Le espressioni cast, purché il tipo di destinazione sia uno dei tipi elencati in precedenza.
*  `checked``unchecked`espressioni and
*  Espressioni con valore predefinito
*  Espressioni NameOf
*  Gli `+` `-` operatori unari,, `!` e predefiniti `~` .
*  Gli operatori binari predefiniti,,,,,,,, `+` `-` `*` `/` `%` `<<` `>>` `&` `|` , `^` , `&&` , `||` , `==` , `!=` , `<` , `>` , `<=` e `>=` , purché ogni operando sia di un tipo elencato sopra.
*  `?:`Operatore condizionale.
*  *Le stringhe interpolate `${}` , purché tutti i componenti siano espressioni costanti di tipo `string` e tutti i componenti interpolati non hanno identificatori di formato e allineamento.*

Nelle espressioni costanti sono consentite le conversioni seguenti:

*  Conversioni di identità
*  Conversioni numeriche
*  Conversioni di enumerazione
*  Conversioni di espressioni costanti
*  Conversioni di riferimenti implicite ed esplicite, a condizione che l'origine delle conversioni sia un'espressione costante che restituisce il valore null.

Nelle espressioni costanti non sono consentite altre conversioni, incluse le conversioni boxing, unboxing e riferimento implicito di valori non null. Ad esempio:
```csharp
class C 
{
    const object i = 5;         // error: boxing conversion not permitted
    const object str = "hello"; // error: implicit reference conversion
}
```
l'inizializzazione di i è un errore perché è necessaria una conversione boxing. L'inizializzazione di str è un errore perché è necessaria una conversione implicita di un riferimento da un valore non null.

Ogni volta che un'espressione soddisfa i requisiti elencati sopra, l'espressione viene valutata in fase di compilazione. Questo vale anche se l'espressione è una sottoespressione di un'espressione più grande che contiene costrutti non costanti.

La valutazione in fase di compilazione delle espressioni costanti usa le stesse regole della valutazione in fase di esecuzione delle espressioni non costanti, ad eccezione del fatto che la valutazione in fase di esecuzione genera un'eccezione, mentre la valutazione in fase di compilazione genera un errore in fase di compilazione.

A meno che un'espressione costante non venga inserita in modo esplicito in un `unchecked` contesto, i flussi che si verificano nelle operazioni aritmetiche di tipo integrale e nelle conversioni durante la valutazione in fase di compilazione dell'espressione provocano sempre errori in fase di compilazione ([espressioni costanti](expressions.md#constant-expressions)).

Le espressioni costanti si verificano nei contesti elencati di seguito. In questi contesti, si verifica un errore in fase di compilazione se non è possibile valutare completamente un'espressione in fase di compilazione.

*  Dichiarazioni di costanti ([costanti](classes.md#constants)).
*  Dichiarazioni di membri di enumerazione ([membri enum](enums.md#enum-members)).
*  Argomenti predefiniti degli elenchi di parametri formali ([parametri del metodo](classes.md#method-parameters))
*  `case`etichette di un' `switch` istruzione ([istruzione switch](statements.md#the-switch-statement)).
*  `goto case`istruzioni ([istruzione goto](statements.md#the-goto-statement)).
*  Lunghezza delle dimensioni in un'espressione di creazione di matrici ([espressioni di creazione di matrici](expressions.md#array-creation-expressions)) che include un inizializzatore.
*  Attributi ([attributi](attributes.md)).

Una conversione implicita di espressioni costanti ([conversioni implicite di espressioni costanti](conversions.md#implicit-constant-expression-conversions)) consente a un'espressione costante di tipo di `int` essere convertita in `sbyte` , `byte` , `short` , `ushort` , `uint` o `ulong` , purché il valore dell'espressione costante sia compreso nell'intervallo del tipo di destinazione.

## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

Questa proposta aggiunge ulteriore complessità al compilatore in Exchange per un'applicabilità più ampia di stringhe interpolate. Poiché queste stringhe vengono completamente valutate in fase di compilazione, le funzionalità di formattazione automatica preziose delle stringhe interpolate sono meno necessario. La maggior parte dei casi d'uso può essere ampiamente replicata tramite le alternative seguenti.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

L' `+` operatore corrente per la stringa concatnation può combinare le stringhe in modo analogo alla proposta corrente.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

Quali parti della progettazione sono ancora indecise?

## <a name="design-meetings"></a>Riunioni di progettazione

Collegamento alle note sulla progettazione che interessano questa proposta e descrivono in una frase per ogni modifica apportata.


