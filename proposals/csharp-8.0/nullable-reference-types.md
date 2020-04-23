---
ms.openlocfilehash: 54f9a372ca0329a284f06876f544e0b4936af02a
ms.sourcegitcommit: 356ee04506a2a82292be25d7b029e7ce2a39e63a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82037402"
---
# <a name="nullable-reference-types-in-c"></a>Nullable reference types in C # #

L'obiettivo di questa funzionalità è:

* Consentire agli sviluppatori di esprimere se una variabile, un parametro o un risultato di un tipo di riferimento deve essere null o meno.
* Fornire avvisi quando tali variabili, parametri e risultati non vengono utilizzati in base a tale finalità.

## <a name="expression-of-intent"></a>Espressione di intenti

Il linguaggio `T?` contiene già la sintassi per i tipi di valore. È semplice estendere questa sintassi ai tipi di riferimento.

Si presuppone che lo scopo di un `T` tipo di riferimento non decorato sia che sia non null.

## <a name="checking-of-nullable-references"></a>Controllo dei riferimenti nullable

Un'analisi del flusso tiene traccia delle variabili di riferimento nullable. Se l'analisi ritiene che non sarebbero null (ad esempio dopo un assegno o un'assegnazione), il relativo valore verrà considerato un riferimento non null.

Un riferimento nullable può anche essere considerato in `x!` modo esplicito come non null con l'operatore suffisso (l'operatore "damnit"), perché quando l'analisi del flusso non può stabilire una situazione non null che lo sviluppatore sa che esiste.

In caso contrario, viene fornito un avviso se un riferimento nullable viene dereferenziato o viene convertito in un tipo non null.

Viene visualizzato un avviso `S[]` durante `T?[]` la `S?[]` `T[]`conversione da e verso a .

Viene visualizzato un avviso `C<S>` durante `C<T?>` la conversione da a`out`except quando il `C<S?>` `C<T>` parametro di tipo è covariante ( ) e quando si esegue la conversione da a except quando il parametro di tipo è controvariante ( ).`in`

Viene visualizzato un `C<T?>` avviso se il parametro di tipo ha vincoli non null.

## <a name="checking-of-non-null-references"></a>Controllo dei riferimenti non Null

Viene visualizzato un avviso se un valore letterale null viene assegnato a una variabile non null o passato come parametro non null.

Viene inoltre visualizzato un avviso se un costruttore non inizializza in modo esplicito i campi di riferimento non null.

Non è possibile tenere traccia in modo adeguato che tutti gli elementi di una matrice di riferimenti non null vengono inizializzati. Tuttavia, è possibile emettere un avviso se nessun elemento di una matrice appena creata viene assegnato a prima che la matrice viene letta o passata. Questo potrebbe gestire il caso comune senza essere troppo rumoroso.

Dobbiamo decidere se `default(T)` genera un avviso o viene semplicemente `T?`trattato come di tipo .

## <a name="metadata-representation"></a>Rappresentazione dei metadati

Gli ornamenti di supporto dei valori Null devono essere rappresentati nei metadati come attributi. Ciò significa che i compilatori di livello inferiore li ignoreranno.

È necessario decidere se sono incluse solo le annotazioni nullable o se è anche presente qualche indicazione se non null è "on" nell'assembly.

## <a name="generics"></a>Generics

Se un `T` parametro di tipo ha vincoli non nullable, viene considerato come non nullable all'interno del relativo ambito.

Se un parametro di tipo non è vincolato o ha solo vincoli nullable, la situazione è un po' più complessa: ciò significa che l'argomento di tipo corrispondente potrebbe *essere* nullable o non nullable. La cosa sicura da fare in questa situazione è considerare il parametro di tipo *come* nullable e non nullable, dando avvisi quando uno dei due viene violato. 

È necessario considerare se devono essere consentiti vincoli espliciti di riferimento nullable. Si noti, tuttavia, che non è possibile evitare di avere tipi di riferimento nullable in *modo implicito* essere vincoli in alcuni casi (vincoli ereditati).

Il `class` vincolo non è null. È possibile `class?` considerare se deve essere un vincolo nullable valido che denota "tipo di riferimento nullable".

## <a name="type-inference"></a>Inferenza

Nell'inferenza del tipo, se un tipo che contribuisce è un tipo di riferimento nullable, il tipo risultante deve essere nullable. In altre parole, viene propagato il valore null.

È necessario considerare `null` se il valore letterale come espressione partecipante deve contribuire con nullità. Non oggi: per i tipi di valore porta a un errore, mentre per i tipi di riferimento il valore null converte correttamente nel tipo normale.

```csharp
string? n = "world";
var x = b ? "Hello" : n; // string?
var y = b ? "Hello" : null; // string? or error
var z = b ? 7 : null; // Error today, could be int?
```

## <a name="null-guard-guidance"></a>Guida di protezione null

Come funzionalità, i tipi di riferimento nullable consentono agli sviluppatori di esprimere la propria finalità e forniscono avvisi tramite l'analisi del flusso se tale finalità è contraddetta. C'è una domanda comune se siano necessarie o meno guardie nulle.

### <a name="example-of-null-guard"></a>Esempio di null guard

```csharp
public void DoWork(Worker worker)
{
    // Guard against worker being null
    if (worker is null)
    {
        throw new ArgumentNullException(nameof(worker));
    }

    // Otherwise use worker argument
}
```

Nell'esempio precedente, `DoWork` la funzione `Worker` accetta a e `null`protegge da esso potenzialmente essere . Se `worker` l'argomento è `null` `throw`, la funzione verrà . `DoWork` Con i tipi di riferimento nullable, il codice `Worker` dell'esempio `null`precedente rende l'intento che il parametro *non* sia . Se `DoWork` la funzione era un'API pubblica, ad esempio un pacchetto NuGet o una libreria condivisa, come guida è consigliabile lasciare le guardie null. Come API pubblica, l'unica garanzia che `null` un chiamante non passa è proteggersi da esso.

### <a name="express-intent"></a>Intento espresso

Un utilizzo più interessante dell'esempio precedente `Worker` consiste `null`nell'esprimere che il parametro potrebbe essere , rendendo così la protezione null più appropriata. Se si rimuove la guardia null nell'esempio seguente, il compilatore avverte che è possibile dereferenziare null. Indipendentemente da ciò, entrambe le guardie nulli sono ancora valide.

```csharp
public void DoWork(Worker? worker)
{
    // Guard against worker being null
    if (worker is null)
    {
        throw new ArgumentNullException(nameof(worker));
    }

    // Otherwise use worker argument
}
```

Per le API non pubbliche, ad esempio il codice sorgente interamente in controllo da uno sviluppatore o un team di sviluppo: i tipi di riferimento nullable potrebbero consentire la rimozione sicura di server di protezione null in cui gli sviluppatori possono garantire che non sia necessario. La funzionalità può essere utile per gli avvisi, ma non `NullReferenceException`può garantire che in fase di esecuzione l'esecuzione del codice possa generare un oggetto .

## <a name="breaking-changes"></a>Modifiche di rilievo

Gli avvisi non Null sono un'ovvia modifica di interruzione del codice esistente e devono essere accompagnati da un meccanismo di consenso esplicito.

Meno ovviamente, gli avvisi da tipi nullable (come descritto in precedenza) sono una modifica sostanziale sul codice esistente in alcuni scenari in cui il supporto di valori Null è implicito:Less obviously, warnings from nullable types (as described above) are a breaking change on existing code in certain scenarios where the nullability is implicit:

* I parametri di tipo non vincolati verranno considerati `object` come nullable in `ToString` modo implicito, pertanto l'assegnazione a o l'accesso ad esempio produrrà avvisi.
* se l'inferenza del tipo `null` deduce nullità dalle espressioni, il codice esistente talvolta restituisce nullable anziché tipi non nullable, che possono causare nuovi avvisi.

Pertanto, gli avvisi nullable devono essere facoltativi

Infine, l'aggiunta di annotazioni a un'API esistente sarà una modifica sostanziale per gli utenti che hanno acconsentito esplicitamente agli avvisi, quando aggiornano la libreria. Anche questo merita la possibilità di aderire o uscire. "Voglio le correzioni di bug, ma non sono pronto ad affrontare le loro nuove annotazioni"

In sintesi, è necessario essere in grado di attivare/disattivare:
* Avvisi nullableNullable warnings
* Avvisi non Null
* Avvisi delle annotazioni in altri file

La granularità dell'opt-in suggerisce un modello simile a un analizzatore, in cui le fasce di codice possono attivare e disattivare con pragmas e i livelli di gravità possono essere scelti dall'utente. Inoltre, le opzioni per libreria ("ignorare le annotazioni da JSON.NET fino a quando non sono pronto a gestire la caduta") possono essere esprimibili nel codice come attributi.

Il design dell'esperienza di opt-in/transizione è fondamentale per il successo e l'utilità di questa funzione. Dobbiamo assicurarci che:

* Gli utenti possono adottare il controllo della nullità gradualmente come vogliono
* Gli autori di librerie possono aggiungere annotazioni di supporto ai valori Null senza timore di interrompere i clienti
* Nonostante questi, non c'è un senso di "incubo di configurazione"

## <a name="tweaks"></a>Tweaks

Potremmo considerare di `?` non usare le annotazioni sulla gente del posto, ma solo osservando se vengono utilizzate in base a ciò che viene loro assegnato. Non lo preferisco; Penso che dovremmo lasciare che la gente esprima il loro intento.

Potremmo considerare una `T! x` scorciatoia sui parametri, che genera automaticamente un controllo null di runtime.

Alcuni modelli su tipi `FirstOrDefault` generici, ad esempio o `TryGet`, hanno un comportamento leggermente strano con argomenti di tipo non nullable, perché restituiscono in modo esplicito valori predefiniti in determinate situazioni. Potremmo cercare di sfumare il sistema di tipo per accogliere questi meglio. Ad esempio, è `?` possibile consentire su parametri di tipo non vincolati, anche se l'argomento di tipo potrebbe già essere nullable. Dubito che ne valga la pena, e porta a stranezze legate all'interazione con i tipi di *valore* nullable. 

## <a name="nullable-value-types"></a>Tipi valore nullable

Si potrebbe prendere in considerazione l'adozione di alcune delle semantica di cui sopra per i tipi di valore nullable pure.

Abbiamo già menzionato l'inferenza `int?` del `(7, null)`tipo, dove potremmo dedurre da , invece di dare solo un errore.

Un'altra opportunità consiste nell'applicare l'analisi del flusso ai tipi di valore nullable. Quando sono ritenuti non null, è possibile consentire l'utilizzo come tipo non nullable in alcuni modi (ad esempio l'accesso ai membri). Dobbiamo solo stare attenti che le cose che si possono *già* fare su un tipo di valore nullable sarà preferito, per motivi di compatibilità indietro.
