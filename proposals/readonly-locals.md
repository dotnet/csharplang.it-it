---
ms.openlocfilehash: 922353d043653ddb651534a01f3fb98f85cd756e
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484482"
---
# <a name="readonly-locals-and-parameters"></a>variabili locali e parametri ReadOnly

* [x] proposto
* [] Prototype
* [] Implementazione
* [] Specifica

## <a name="summary"></a>Summary
[summary]: #summary

Consente di annotare variabili locali e parametri come ReadOnly per evitare la mutazione superficiale di tali variabili locali e parametri.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Attualmente, è possibile applicare la parola chiave `readonly` ai campi; Questo ha l'effetto di garantire che un campo possa essere scritto durante la costruzione (costruzione statica nel caso di un campo statico o di una costruzione dell'istanza nel caso di un campo di istanza), che consente agli sviluppatori di evitare errori sovrascrivendo accidentalmente lo stato che non deve essere modificato. Ma i campi non sono gli unici a cui gli sviluppatori vogliono assicurare che i valori non vengano mutati. In particolare, è normale creare una variabile locale per archiviare lo stato temporaneo e aggiornare accidentalmente tale stato temporaneo può comportare calcoli errati e altri bug, soprattutto quando tali "variabili locali" vengono acquisite nelle espressioni lambda, a quel punto vengono innalzate di livello ai campi, ma non esiste alcun modo per contrassegnare tali campi come `readonly`.

## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Le variabili locali saranno annotabili come `readonly` e il compilatore assicurerà che vengano impostate solo al momento della dichiarazione (alcune variabili locali in C# sono già implicitamente ReadOnly, ad esempio la variabile di iterazione in un ciclo ' foreach ' o la variabile utilizzata in un blocco ' using ', ma attualmente lo sviluppatore non è in grado di contrassegnare altre variabili locali come `readonly`). Tali variabili locali `readonly` devono disporre di un inizializzatore:

```csharp
readonly long maxBytesToDelete = (stream.LimitBytes - stream.MaxBytes) / 10;
...
maxBytesToDelete = 0; // Error: can't assign to readonly locals outside of declaration
```

E come abbreviazione per `readonly var`, è possibile usare la parola chiave contestuale esistente `let`, ad esempio

```csharp
let maxBytesToDelete = (stream.LimitBytes - stream.MaxBytes) / 10;
...
maxBytesToDelete = 0; // Error: can't assign to readonly locals outside of declaration
```

Non esistono vincoli speciali per ciò che può essere l'inizializzatore e possono essere qualsiasi elemento attualmente valido come inizializzatore per le variabili locali, ad esempio

```csharp
readonly T data = arg1 ?? arg2;
```

`readonly` sulle variabili locali è particolarmente utile quando si lavora con le espressioni lambda e le chiusure. Quando un metodo anonimo o un'espressione lambda accede allo stato locale dall'ambito che lo contiene, questo stato viene acquisito in una chiusura dal compilatore, che è rappresentato da una "classe di visualizzazione".  Ogni "local" acquisito è un campo di questa classe, ma poiché il compilatore genera questo campo per conto dell'utente, non è possibile annotarlo come `readonly` per impedire che l'espressione lambda scriva erroneamente in "local" (tra virgolette perché in realtà non è locale, almeno non nel codice MSIL risultante). Con `readonly` variabili locali, il compilatore può impedire che l'espressione lambda scriva in locale, che è particolarmente utile in scenari che coinvolgono il multithreading in cui una scrittura errata potrebbe causare un bug di concorrenza pericoloso ma raro e difficile da trovare.

```csharp
readonly long index = ...;
Parallel.ForEach(data, item => {
    T element = item[index];
    index = 0; // Error: can't assign to readonly locals outside of declaration
});
```

Come forma speciale di local, i parametri saranno anche annotabili come `readonly`. Questo non avrebbe alcun effetto su ciò che il chiamante del metodo è in grado di passare al parametro (proprio come non esiste alcun vincolo sui valori che possono essere archiviati in un campo di `readonly`), ma come per qualsiasi `readonly` locale, il compilatore impedisce al codice di scrivere nel parametro dopo la dichiarazione, il che significa che il corpo del metodo non è consentito per la scrittura nel parametro.

```csharp
public void Update(readonly int index = 0) // Default values are ok though not required
{
    ...
    index = 0; // Error: can't assign to readonly parameters
    ...
}
```

`readonly` parametri non influiscono sulla firma o sui metadati generati dal compilatore per il metodo e influiscono semplicemente sul modo in cui il compilatore gestisce la compilazione del corpo del metodo. Quindi, ad esempio, un metodo virtuale di base potrebbe avere un parametro di `readonly` e tale parametro potrebbe essere scrivibile in una sostituzione.

Come per i campi, `readonly` per variabili locali e parametri è superficiale, che influisce sul percorso di archiviazione, ma non influisce in modo transitivo sull'oggetto grafico. Tuttavia, anche come con i campi, la chiamata di un metodo su un `readonly` struct locale/parametro effettuerà effettivamente una copia dello struct e chiamerà il metodo sulla copia, per evitare mutazioni interne di `this`.

`readonly` variabili locali e i parametri non possono essere passati come argomenti `ref` o `out`, a meno che non sia supportata anche la `ref readonly`.

## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

- `val` possibile utilizzare come una forma abbreviata alternativa a `let`.

## <a name="unresolved-questions"></a>Domande non risolte
[unresolved]: #unresolved-questions

- `readonly ref` / `ref readonly` / `readonly ref readonly`: ho lasciato la questione di come gestire `ref readonly` come separato da questa proposta.
- Questa proposta non affronta gli struct di sola lettura/i tipi non modificabili. Che rimane per una proposta separata.

## <a name="design-meetings"></a>Riunioni di progettazione

- Descritto brevemente il 21 gennaio 2015 (<https://github.com/dotnet/roslyn/issues/98>)
