---
ms.openlocfilehash: ecdad8c863d0695bc901e4d96d9ca3decbc248eb
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484615"
---
# <a name="nullable-reference-types-in-c"></a>Tipi di riferimento nullable inC# #

L'obiettivo di questa funzionalità è:

* Consente agli sviluppatori di esprimere se una variabile, un parametro o un risultato di un tipo di riferimento è destinato a essere null o meno.
* Fornire avvisi quando tali variabili, parametri e risultati non vengono utilizzati in base a tale finalità.

## <a name="expression-of-intent"></a>Espressione di finalità

Il linguaggio contiene già la sintassi `T?` per i tipi di valore. È semplice estendere questa sintassi ai tipi di riferimento.

Si presuppone che lo scopo di un tipo di riferimento non decorato `T` non sia null.

## <a name="checking-of-nullable-references"></a>Controllo dei riferimenti Nullable

Un'analisi di flusso tiene traccia delle variabili di riferimento Nullable. Se l'analisi considera che non sarebbero null (ad esempio, dopo un controllo o un'assegnazione), il relativo valore verrà considerato un riferimento non null.

Un riferimento Nullable può essere trattato anche in modo esplicito come non null con l'operatore suffisso `x!` (l'operatore "dannazione"), quando l'analisi dei flussi non è in grado di stabilire una situazione non null che lo sviluppatore conosce è presente.

In caso contrario, viene fornito un avviso se un riferimento Nullable è dereferenziato o viene convertito in un tipo non null.

Quando si esegue la conversione da `S[]` a `T?[]` e da `S?[]` a `T[]`, viene visualizzato un avviso.

Viene fornito un avviso quando si esegue la conversione da `C<S>` a `C<T?>` tranne quando il parametro di tipo è covariante (`out`) e quando si esegue la conversione da `C<S?>` a `C<T>` tranne quando il parametro di tipo è controvariante (`in`).

Viene visualizzato un avviso in `C<T?>` se il parametro di tipo presenta vincoli non null. 

## <a name="checking-of-non-null-references"></a>Verifica dei riferimenti non null

Viene fornito un avviso se un valore letterale null viene assegnato a una variabile non null o passato come parametro non null.

Viene inoltre fornito un avviso se un costruttore non inizializza in modo esplicito i campi di riferimento non null.

Non è possibile rilevare adeguatamente che vengono inizializzati tutti gli elementi di una matrice di riferimenti non null. Tuttavia, è possibile emettere un avviso se nessun elemento di una matrice appena creata viene assegnato a prima che la matrice venga letta o passata. Questo potrebbe gestire il caso comune senza essere troppo rumoroso.

È necessario decidere se `default(T)` genera un avviso o semplicemente essere considerato come il tipo `T?`.

## <a name="metadata-representation"></a>Rappresentazione dei metadati

Le aree di strumenti per il supporto di valori null devono essere rappresentate nei metadati come attributi. Ciò significa che i compilatori di livello inferiore li ignoreranno.

È necessario decidere se sono incluse solo le annotazioni nullable o se è presente anche un'indicazione relativa alla presenza di valori non null nell'assembly.

## <a name="generics"></a>Generics

Se un parametro di tipo `T` presenta vincoli non nullable, viene considerato come non nullable all'interno del relativo ambito.

Se un parametro di tipo non è vincolato o ha solo vincoli Nullable, la situazione è un po' più complessa: ciò significa che l'argomento di tipo corrispondente *potrebbe essere nullable o non nullable.* La cosa più sicura da fare in questa situazione consiste nel considerare il parametro di *tipo come Nullable* e non nullable, fornendo avvisi quando uno dei due viene violato. 

Vale la pena considerare se devono essere consentiti i vincoli espliciti Nullable Reference. Si noti, tuttavia, che non è possibile evitare che i tipi di riferimento Nullable siano *implicitamente* vincoli in determinati casi (vincoli ereditati).

Il vincolo `class` è diverso da null. È possibile considerare se `class?` deve essere un vincolo Nullable valido che indica "tipo di riferimento Nullable".

## <a name="type-inference"></a>Inferenza del tipo

Nell'inferenza del tipo, se un tipo che contribuisce è un tipo di riferimento Nullable, il tipo risultante deve essere nullable. In altre parole, viene propagato un valore null.

È necessario valutare se il valore letterale `null` come espressione partecipante debba contribuire a un valore null. Non è oggi: per i tipi di valore genera un errore, mentre per i tipi di riferimento il valore null viene convertito correttamente nel tipo normale. 

```csharp
string? n = "world";
var x = b ? "Hello" : n; // string?
var y = b ? "Hello" : null; // string? or error
var z = b ? 7 : null; // Error today, could be int?
```

## <a name="breaking-changes"></a>Modifiche di rilievo

Gli avvisi non null rappresentano una modifica ovvia del codice esistente e devono essere accompagnati da un meccanismo di consenso esplicito.

Meno ovviamente, gli avvisi dei tipi Nullable (come descritto in precedenza) rappresentano una modifica sostanziale del codice esistente in determinati scenari in cui il supporto di valori null è implicito:

* I parametri di tipo non vincolati verranno considerati implicitamente Nullable, quindi l'assegnazione a `object` o l'accesso ad, ad esempio `ToString` genererà avvisi.
* Se l'inferenza del tipo deduce un valore null dalle espressioni `null`, il codice esistente a volte restituisce Nullable anziché i tipi non nullable, che possono causare nuovi avvisi.

Pertanto, gli avvisi nullable devono anche essere facoltativi

Infine, l'aggiunta di annotazioni a un'API esistente sarà una modifica sostanziale per gli utenti che hanno scelto gli avvisi quando eseguono l'aggiornamento della libreria. Questo, inoltre, merita la possibilità di acconsentire esplicitamente o meno. "Desidero correggere le correzioni di bug, ma non sono pronto per gestire le nuove annotazioni"

In breve, è necessario essere in grado di acconsentire esplicitamente o meno:
* Avvisi Nullable
* Avvisi non null
* Avvisi da annotazioni in altri file

La granularità del consenso esplicito suggerisce un modello di tipo analizzatore, in cui le porzioni di codice possono acconsentire esplicitamente e uscire con i pragma e i livelli di gravità possono essere scelti dall'utente. Inoltre, le opzioni per libreria ("Ignora le annotazioni da JSON.NET fino a quando non si è pronti a gestire il calo") possono essere esprimibile nel codice come attributi.

La progettazione dell'esperienza di consenso esplicito/transizione è fondamentale per il successo e l'utilità di questa funzionalità. È necessario assicurarsi che:

* Gli utenti possono adottare gradualmente il controllo dei valori null come desiderano
* Gli autori di librerie possono aggiungere annotazioni di supporto per i valori null senza timore di compromettere
* Nonostante queste, non c'è un senso di "incubo per la configurazione"

## <a name="tweaks"></a>Tweaks

È possibile considerare di non usare le annotazioni `?` sulle variabili locali, ma solo di osservare se vengono usate in base a ciò che viene assegnato. Non prediligo questo problema; Credo che dovremmo esprimere in modo uniforme le loro finalità.

È possibile considerare una sintassi abbreviata `T! x` sui parametri, che genera automaticamente un controllo null in fase di esecuzione.

Alcuni modelli sui tipi generici, ad esempio `FirstOrDefault` o `TryGet`, hanno un comportamento leggermente strano con argomenti di tipo non nullable, perché producono in modo esplicito valori predefiniti in determinate situazioni. Potremmo provare a sfumare il sistema di tipi per adattarlo meglio. Ad esempio, è possibile consentire `?` sui parametri di tipo non vincolati, anche se l'argomento di tipo potrebbe già ammettere valori null. Dubito che valga la pena e che comporti stranezze correlate all'interazione con I tipi di *valore* Nullable. 

## <a name="nullable-value-types"></a>Tipi valore nullable

È possibile prendere in considerazione l'adozione di parte della semantica precedente anche per i tipi di valore Nullable.

È già stata indicata l'inferenza del tipo, in cui è possibile dedurre `int?` da `(7, null)`, anziché semplicemente restituire un errore.

Un'altra opportunità consiste nell'applicare l'analisi del flusso ai tipi di valore Nullable. Quando vengono ritenuti non null, si potrebbe effettivamente consentire l'uso di come tipo non nullable in determinati modi, ad esempio l'accesso ai membri. È sufficiente prestare attenzione che siano preferibili gli elementi che *è possibile eseguire* in un tipo di valore Nullable, per motivi di compatibilità.
