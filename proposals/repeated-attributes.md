---
ms.openlocfilehash: a26af810ced3fe5a7d7108f38a22d40aa64543f5
ms.sourcegitcommit: cc68af0b2a6e2ef5780eeb43935600b5927ee720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86157202"
---
# <a name="repeated-attributes-in-partial-members"></a>Attributi ripetuti in membri parziali

## <a name="summary"></a>Riepilogo

Consentire a ogni dichiarazione di un membro parziale di applicare in modo indipendente un attributo non contrassegnato con `[AttributeUsage(AllowMultiple = true)]` , purché gli argomenti dell'attributo siano identici in tutte le applicazioni.

## <a name="motivation"></a>Motivazione

Quando si considerano gli attributi presenti in un metodo "parziale", le unioni di linguaggio uniscono tutti gli attributi in tutte le posizioni corrispondenti in entrambe le dichiarazioni. Il metodo seguente, ad esempio, `M` presenta gli attributi `A` e `B` .

```cs
[A]
partial void M();

[B]
partial void M() { }
```

Ciò significa che gli attributi che non sono contrassegnati non `[AttributeUsage(AllowMultiple = true)]` possono essere presenti in entrambe le parti:

```cs
[A]
partial void M();

[A] // error: duplicate attribute!
partial void M() { }
```

Si tratta di un problema di usabilità/leggibilità, perché alcuni attributi sono progettati per informare l'utente e/o il gestore del metodo di quali pre/postcondizioni o invarianti il metodo richiede. Ad esempio:

```cs
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue(out object? value) { ... }
```

Un membro parziale facilita in genere la relazione tra un generatore di codice e un utente finale. ogni entità fornisce una delle dichiarazioni del membro parziale affinché un generatore di codice fornisca funzionalità all'utente o per consentire all'utente di accedere a un punto di estensione nel codice generato. Nel caso in cui una sola dichiarazione possa avere questi attributi di applicazione singola, il generatore e l'utente non possono comunicare efficacemente i loro requisiti. Se un generatore produce una dichiarazione di definizione con un `NotNullWhen` attributo, ad esempio, l'utente non può scrivere una dichiarazione di implementazione con lo stesso attributo, anche se la postcondizione è applicabile all'implementazione e controllata dal compilatore. Ciò consente di creare confusione per gli utenti quando si verificano le cause principali degli avvisi o quando si tenta di comprendere i comportamenti di un metodo.

## <a name="solution"></a>Soluzione

Consente di utilizzare un attributo non AllowMultiple una volta in ogni dichiarazione parziale, purché gli argomenti dell'attributo siano identici. Poiché gli argomenti di attributo sono tutte costanti, questo non dovrebbe essere molto difficile da verificare in fase di compilazione. Quando gli attributi vengono unificati tra le dichiarazioni, ogni attributo non AllowMultiple verrà deduplicato e verrà emessa solo un'istanza dell'attributo.

```cs
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue([NotNullWhen(true)] out object? value) { ... } // ok

// equivalent to:
public bool TryGetValue([NotNullWhen(true)] out object value) { ... }

// error when attribute arguments do not match
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue([NotNullWhen(false)] out object? value) { ... } // error
```

### <a name="open-questions"></a>Domande aperte

1. Questa ripetizione degli attributi deve essere consentita per le dichiarazioni di tipo ' Partial ' o solo per i membri non di tipo (ad esempio metodi)?
2. Gli attributi che *consentono l'* uso di più utilizzi su un simbolo possono "acconsentire" alla deduplicazione degli utilizzi equivalenti di un attributo?

### <a name="design-meetings"></a>Riunioni di progettazione
#### <a name="6th-july-2020"></a>[6 luglio 2020](/meetings/2020/LDM-2020-07-06.md#repeated-attributes-on-partial-members)
La proposta viene accettata.
  - La ripetizione degli attributi non AllowMultiple sarà consentita tra le dichiarazioni di tipi parziali (aprire la domanda 1).
  - L'applicazione ripetuta di attributi AllowMultiple non cambierà nel comportamento e un meccanismo di "consenso esplicito" per la deduplicazione può essere considerato in una proposta futura (aprire la domanda 2).
