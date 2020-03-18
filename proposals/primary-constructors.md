---
ms.openlocfilehash: 77c9ffda3a59cc5f3dcc3ec9848600c6c5e03eed
ms.sourcegitcommit: 27487fa0294f4cdb7e5f2478884149e05314fd8a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "79484972"
---
# <a name="primary-constructors"></a>Costruttori primari

* [x] proposto
* [] Prototipo: non avviato
* [] Implementazione: non avviata
* [] Specifica: non avviata

## <a name="summary"></a>Summary
[summary]: #summary

Le classi possono avere un elenco di parametri e, quando lo fanno, la relativa specifica della classe di base può avere un elenco di argomenti.
I parametri del costruttore primario rientrano nell'ambito della dichiarazione di classe e, se vengono acquisiti da un membro di funzione o da una funzione anonima, vengono archiviati come campi privati nella classe.

## <a name="motivation"></a>Motivazione
[motivation]: #motivation

Nel codice di inizializzazione del programma è comune avere molto standard. Nel caso generale, una determinata parte dei dati `x` è indicata più volte:

- Come campo privato `_x`
- Come parametro `x` a un costruttore
- In un `_x = x;` di assegnazione del campo dal parametro nel costruttore
- Come proprietà `X`
- Nel setter della proprietà `x = value;`
- Nel getter della proprietà `return x;`

``` c#
class C
{
    private string _x;
    
    public C(string x)
    {
        _x = x;
    }
    public string X
    {
        get => _x;
        set { if (value == null) throw new NullArgumentException(nameof(X)); _x = value; }
    }
}
```

Per le proprietà che non richiedono la convalida o il calcolo, la noia può essere ridotta usando le proprietà automatiche, riducendo così la necessità di dichiarare un campo sottostante esplicito per la proprietà. Tuttavia, se la proprietà richiede qualsiasi tipo di logica oltre a quella fornita da una proprietà automatica, il risultato è il migliore.

I costruttori primari riducono invece il sovraccarico inserendo gli argomenti del costruttore direttamente nell'ambito della classe, evitando di nuovo la necessità di dichiarare in modo esplicito un campo sottostante. Quindi, l'esempio precedente diventa:

``` c#
class C(string x)
{
    public string X
    {
        get => x;
        set { if (value == null) throw new NullArgumentException(nameof(X)); x = value; }
    }
}
```

In questo esempio, il costruttore primario riduce il numero di entità denominate per `x` da tre a due, evitando il `_x` campo sottostante. Rimuove due delle tre dichiarazioni di membro (mantenendo solo la dichiarazione di proprietà) e riduce il numero totale di menzioni di `x`/`_x`/`X` da 8 a cinque.


## <a name="detailed-design"></a>Progettazione dettagliata
[design]: #detailed-design

Le classi possono avere un elenco di parametri:

``` c#
public class C(int i, string s)
{
    ...
}
```

L'elenco di parametri comporta la dichiarazione implicita di un costruttore per la classe, con la stessa accessibilità della classe stessa.

``` c#
new C(5, "Hello");
```

I parametri del costruttore primario rientrano nell'ambito di tutto il corpo della classe. Se vengono acquisiti da un membro di funzione o da una funzione anonima, vengono archiviati come campi privati nella classe. Se vengono utilizzati solo durante l'inizializzazione, non verranno archiviati nell'oggetto.

``` c#
public class C(int i, string s)
{
    int[] a = new int[i]; // i not captured
    public int S => s;    // s captured
}
```

Se una classe con un costruttore primario ha una specifica della classe di base, può avere un elenco di argomenti. Funge da elenco di argomenti per un inizializzatore di `base(...)` del costruttore dichiarato in modo implicito. Se non viene fornito alcun elenco di argomenti, viene utilizzato un oggetto vuoto.

``` c#
public class C(int i, string s) : B(s)
{
    ...
}
```
La classe può avere anche costruttori definiti in modo esplicito, ma tutti devono usare un inizializzatore `this(...)`. In questo modo si garantisce che il costruttore primario venga sempre chiamato quando viene costruita una nuova istanza.

Tutti gli inizializzatori nel corpo della classe diventeranno assegnazioni nel costruttore generato. Ciò significa che, a differenza di altre classi, gli inizializzatori vengono eseguiti *dopo* che è stato richiamato il costruttore di base, non prima. Inoltre, la classe generata conterrà le assegnazioni per inizializzare tutti i campi privati generati per archiviare i parametri del costruttore primario acquisiti dai membri. Questi membri vengono riscritti per utilizzare il campo privato anziché il parametro in modo analogo alle chiusure per le espressioni lambda. I campi primari generati vengono inizializzati per primi, quindi le assegnazioni generate dall'inizializzatore vengono eseguite nell'ordine di visualizzazione nella classe.

Per l'esempio precedente, l'effetto della dichiarazione di classe è come se fosse riscritto nel modo seguente:

``` c#
public class C : B
{
    public C(int i, string s) : base(s)
    {
        __s = s;        // store parameter s for captured use
        a = new int[i]; // initialize a
    }
    int __s; // generated field for capture of s
    
    int[] a;
    public int S => __s; // s replaced with captured __s
}
```

L'acquisizione presenta restrizioni simili all'acquisizione di variabili locali da parte delle espressioni lambda. Ad esempio, i parametri `ref` e `out` sono consentiti nei costruttori primari, ma non è possibile acquisire i corpi dei membri.


## <a name="drawbacks"></a>Svantaggi
[drawbacks]: #drawbacks

In ordine di importanza approssimativa.

* La proposta usa la sintassi che è stata proposta anche per i record posizionali. Se si desiderano entrambe le funzionalità, è necessario disporre di alcune sistemazioni. ad esempio è stato proposto un modificatore `data` nei record.
* Le dimensioni di allocazione degli oggetti costruiti sono meno ovvie, perché il compilatore determina se allocare un campo per un parametro del costruttore primario basato sul testo completo della classe. Questo rischio è simile all'acquisizione implicita delle variabili da parte delle espressioni lambda.
* Una tentazione comune (o un modello accidentale) potrebbe essere l'acquisizione del parametro "Same" a più livelli di ereditarietà mentre viene passata alla catena di costruttori invece di attribuire in modo esplicito un campo protetto nella classe di base, causando allocazioni duplicate. per gli stessi dati negli oggetti. Questo è molto simile al rischio attuale di eseguire l'override delle proprietà automatiche con proprietà automatiche. 
* Come suggerito in precedenza, non esiste alcuna posizione per la logica aggiuntiva che in genere può essere espressa nei corpi del costruttore. L'estensione "Primary Constructor Bodies" sotto si rivolge a questo.
* Come proposto, la semantica dell'ordine di esecuzione è leggermente diversa rispetto a quella dei costruttori normali. Questa situazione potrebbe essere ovvia, ma al costo di alcune proposte di estensione, in particolare "corpi del costruttore primario".
* La proposta funziona solo quando è possibile designare un singolo costruttore come primario.
* Non è possibile avere un'accessibilità separata della classe e del costruttore primario. Ad esempio, nelle situazioni in cui i costruttori pubblici delegano tutti a un costruttore privato "Build-it-all" che sarebbe necessario. Se necessario, è possibile proporre la sintassi per tale versione in un secondo momento.


## <a name="alternatives"></a>Alternativi
[alternatives]: #alternatives

I record posizionali completi possono essere un'alternativa oppure possono coesistere con i costruttori primari, a seconda delle specifiche. Consentono una *maggiore* abbreviazione in un *minor* numero di scenari. Quindi, entrambi sono potenzialmente utili, ma la loro presenza potrebbe essere eccessiva, a meno che non possano essere perfettamente integrati tra loro.


## <a name="possible-extensions"></a>Estensioni possibili
[extensions]: #possible-extensions

Si tratta di varianti o aggiunte alla proposta di base che possono essere considerate insieme a essa o in una fase successiva, se ritenute utili.

### <a name="primary-constructor-bodies"></a>Corpi del costruttore primario

I costruttori stessi spesso contengono la logica di convalida del parametro o altro codice di inizializzazione non banale che non può essere espresso come inizializzatori.

I costruttori primari possono essere estesi per consentire la visualizzazione dei blocchi di istruzioni direttamente nel corpo della classe. Tali istruzioni verranno inserite nel costruttore generato nel punto in cui compaiono tra l'inizializzazione delle assegnazioni e verrebbero quindi eseguite intervallate dagli inizializzatori. Ad esempio:

``` c#
public class C(int i, string s) : B(s)
{
    {
        if (i < 0) throw new ArgumentOutOfRangeException(nameof(i));
    }
    int[] a = new int[i];
    public int S => s;
}
```

### <a name="initializer-fields-and-initializer-functions"></a>Campi inizializzatori e funzioni inizializzatore

In una classe con un costruttore primario è possibile prendere in considerazione le dichiarazioni di campo e di metodo senza modificatori di accessibilità come variabili locali e funzioni locali:

* Analogamente ai parametri del costruttore primario, i "campi dell'inizializzatore" vengono acquisiti in un campo privato effettivo se sono stati usati nei membri della funzione.
* Le "funzioni inizializzatore" verrebbero considerate solo per acquisire i parametri del costruttore primario e i campi di inizializzazione se fossero usati in altri membri di funzione. Se non vengono acquisiti, potrebbero essere generati in modo più ottimale, come le funzioni locali.
* Analogamente ai parametri del costruttore primario, non sono disponibili tramite l'accesso ai membri, ma solo come nome semplice.

Questa operazione può essere utilizzata per variabili temporanee e funzioni di supporto rilevanti solo per l'inizializzazione:

``` c#
public class C(string s)
{
    int size = s.Length;             // not captured
    int[] Create() => new int[size]; // not captured
    int[] a = Create();
    ...
}
```

Questo può essere troppo sottile, soprattutto perché l'assenza di modificatori di accessibilità altrove significa semplicemente `private`. 

### <a name="initializer-statements"></a>Istruzioni inizializzatore

Una combinazione radicale delle estensioni precedenti consiste nel consentire semplicemente le istruzioni direttamente nel corpo della classe. Tali istruzioni sono esattamente come i corpi dei costruttori intervallati proposte in precedenza, ad eccezione del fatto che non devono essere racchiusi in `{ }`. Per poter essere sufficientemente utile, le variabili locali e le funzioni di supporto devono essere esprimibile anche al livello superiore della classe, nel modo in cui vengono esplorate nell'estensione "campi dell'inizializzatore e funzioni di inizializzazione" sopra.


### <a name="member-access"></a>Accesso ai membri

La proposta principale considera i parametri del costruttore primario come parametri che possono essere definiti solo come nomi semplici. In alternativa, è possibile farvi riferimento come se fossero campi privati, ad esempio con un accesso ai membri, *anche* se talvolta non vengono generati come campi. In questo modo è possibile fare riferimento a tali `this.x` quando vengono nascoste dalle variabili locali e a cui si accede da un'istanza diversa come `other.x`.

Se applicato all'estensione "campi inizializzatori e funzioni inizializzatore", questo ridurrebbe anche il grado di differenza rispetto ai membri privati normali. L'unica differenza è che il compilatore è libero di Elide dall'oggetto se usato solo durante l'inizializzazione.

