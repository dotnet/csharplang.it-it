---
ms.openlocfilehash: 76065293f652979ab395e131d657e44899c5a65b
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484552"
---
# <a name="simplified-null-argument-checking"></a>Controllo semplificato degli argomenti null

## <a name="summary"></a>Summary
Questa proposta fornisce una sintassi semplificata per la convalida degli argomenti dei metodi non `null` e la generazione di `ArgumentNullException` in modo appropriato.

## <a name="motivation"></a>Motivazione
Il lavoro sulla progettazione di tipi di riferimento Nullable ha causato l'esame del codice necessario per la convalida `null` argomenti. Dato che la NRT non influisce sulle esecuzioni di codice, gli sviluppatori devono ancora aggiungere `if (arg is null) throw` codice della piastra calda anche nei progetti completamente `null` puliti. Questo ci ha permesso di esplorare una sintassi minima per l'argomento `null` la convalida nel linguaggio. 

Anche se questa `null` sintassi di convalida dei parametri è prevista per coppie di frequente con NRT, la proposta è completamente indipendente. La sintassi può essere utilizzata indipendentemente dalle direttive `#nullable`.

## <a name="detailed-design"></a>Progettazione dettagliata 

### <a name="null-validation-parameter-syntax"></a>Sintassi dei parametri di convalida null
L'operatore Bang, `!`, può essere posizionato dopo un nome di parametro in un elenco di parametri e in questo C# modo il compilatore emetterà il codice di controllo `null` standard per il parametro. Questa operazione viene definita `null` sintassi del parametro di convalida. Ad esempio:

``` csharp
void M(string name!) {
    ...
}
```

Verranno convertiti in:

``` csharp
void M(string name) {
    if (name is null) {
        throw new ArgumentNullException(nameof(name));
    }
    ...
}
```

Il controllo `null` generato si verificherà prima del codice creato dallo sviluppatore nel metodo. Quando più parametri contengono l'operatore `!`, i controlli si verificheranno nello stesso ordine in cui vengono dichiarati i parametri.

``` csharp
void M(string p1, string p2) {
    if (p1 is null) {
        throw new ArgumentNullException(nameof(p1));
    }
    if (p2 is null) {
        throw new ArgumentNullException(nameof(p2));
    }
    ...
}
```

Il controllo sarà specifico per l'uguaglianza dei riferimenti `null`, non richiama `==` o alcun operatore definito dall'utente. Ciò significa anche che l'operatore `!` può essere aggiunto solo ai parametri il cui tipo può essere testato per verificarne l'uguaglianza rispetto a `null`. Ciò significa che non può essere usato su un parametro il cui tipo è noto come tipo di valore.

``` csharp
// Error: Cannot use ! on parameters who types derive from System.ValueType
void G<T>(T arg!) where T : struct {

}
```

Nel caso di un costruttore, la convalida `null` si verificherà prima di qualsiasi altro codice nel costruttore. Sono incluse le seguenti: 

- Concatenamento ad altri costruttori con `this` o `base` 
- Inizializzatori di campo che si verificano in modo implicito nel costruttore

Ad esempio:

``` csharp
class C {
    string field = GetString();
    C(string name!): this(name) {
        ...
    }
}
```

Verranno convertiti approssimativamente negli elementi seguenti:

``` csharp
class C {
    C(string name)
        if (name is null) {
            throw new ArgumentNullException(nameof(name));
        }
        field = GetString();
        :this(name);
        ...
}
```

Nota: questo non è un C# codice valido ma solo un'approssimazione delle operazioni svolte dall'implementazione. 

La sintassi del parametro di convalida `null` sarà valida anche negli elenchi di parametri lambda. Questa proprietà è valida anche nella sintassi del singolo parametro che non dispone di parentesi.

``` csharp
void G() {
    // An identity lambda which throws on a null input
    Func<string, string> s = x! => x;
}
```

La sintassi è valida anche per i parametri dei metodi iteratore. A differenza di altro codice nell'iteratore, si verificherà la convalida del `null` quando viene richiamato il metodo iteratore, non quando viene camminato l'enumeratore sottostante. Questo vale per gli iteratori tradizionali o `async`.

``` csharp
class Iterators {
    IEnumerable<char> GetCharacters(string s!) {
        foreach (var c in s) {
            yield return c;
        }
    }

    void Use() {
        // The invocation of GetCharacters will throw
        IEnumerable<char> e = GetCharacters(null);
    }
}
```

L'operatore `!` può essere utilizzato solo per gli elenchi di parametri a cui è associato un corpo del metodo. Ciò significa che non può essere usato in un metodo di `abstract`, `interface`, `delegate` o la definizione di un metodo `partial`.

### <a name="extending-is-null"></a>Estensione null
I tipi per i quali l'espressione `is null` è valida verranno estesi in modo da includere parametri di tipo non vincolati. In questo modo sarà possibile completare l'intento di verificare la presenza di `null` su tutti i tipi di `null` validi. In particolare, si tratta di tipi che non sono sicuramente noti come tipi di valore. Per esempio, i parametri di tipo che sono vincolati a `struct` non possono essere utilizzati con questa sintassi.

``` csharp
void NullCheck<T1, T2>(T1 p1, T2 p2) where T2 : struct {
    // Okay: T1 could be a class or struct here.
    if (p1 is null) {
        ...
    }

    // Error 
    if (p2 is null) { 
        ...
    }
}
```

Il comportamento di `is null` in un parametro di tipo sarà uguale a quello `== null` oggi. Nei casi in cui viene creata un'istanza del parametro di tipo come tipo di valore, il codice verrà valutato come `false`. Per i casi in cui si tratta di un tipo di riferimento, il codice eseguirà un controllo `is null` appropriato.

### <a name="intersection-with-nullable-reference-types"></a>Intersezione con tipi di riferimento Nullable
Qualsiasi parametro con un operatore `!` applicato al nome verrà avviato con lo stato nullable non `null`. Questo vale anche se il tipo del parametro stesso è potenzialmente `null`. Che può verificarsi con un tipo nullable in modo esplicito, ad esempio `string?`, oppure con un parametro di tipo non vincolato. 

Quando una `!` sintassi dei parametri viene combinata con un tipo nullable in modo esplicito sul parametro, viene emesso un avviso dal compilatore:

``` csharp
void WarnCase<T>(
    string? name!, // Warning: combining explicit null checking with a nullable type
    T value1 // Okay
)
```

## <a name="open-issues"></a>Problemi aperti
nessuno

## <a name="considerations"></a>Considerazioni

### <a name="constructors"></a>Costruttori
La generazione di codice per i costruttori significa che è presente una modifica del comportamento ridotta, ma osservabile, in fase di passaggio dalla convalida `null` standard e dalla sintassi del parametro di convalida `null` (`!`). Il controllo `null` la convalida standard si verifica dopo che entrambi gli inizializzatori di campo e qualsiasi chiamata `base` o `this`. Ciò significa che uno sviluppatore non può necessariamente migrare il 100% della convalida `null` alla nuova sintassi. I costruttori richiedono almeno alcune ispezioni.

Dopo la discussione, anche se è stato deciso che è molto improbabile che si verifichino problemi di adozione significativi. È più logico che il controllo `null` venga eseguito prima della logica del costruttore. Può rivedere se vengono individuati problemi di compatibilità significativi.

### <a name="warning-when-mixing--and-"></a>Avviso durante la combinazione? e!
Si è verificata una lunga discussione sulla possibilità o meno di emettere un avviso quando viene applicata la sintassi del `!` a un parametro tipizzato in modo esplicito a un tipo Nullable. In apparenza sembra una dichiarazione insensata dello sviluppatore, ma in alcuni casi le gerarchie dei tipi possono forzare gli sviluppatori in una situazione di questo tipo. 

Si consideri la seguente gerarchia di classi in una serie di assembly (presupponendo che tutti siano compilati con il controllo `null` abilitato):

``` csharp
// Assembly1
abstract class C1 {
    protected abstract void M(object o); 
}

// Assembly2
abstract class C2 : C1 {

}

// Assembly3
abstract class C3 : C2 { 
    protected override void M(object o!) {
        ...
    }
}
```

Qui l'autore di `C3` ha deciso di aggiungere `null` convalida al parametro `o`. Questo è completamente in linea con il modo in cui la funzionalità è destinata all'utilizzo.

Si supponga ora che in un secondo momento l'autore di Assembly2 decida di aggiungere la seguente sostituzione:

``` csharp
// Assembly2
abstract class C2 : C1 {
   protected override void M(object? o) { 
       ...
   }
}
```

Questa operazione è consentita dai tipi di riferimento Nullable, perché è lecito rendere il contratto più flessibile per le posizioni di input. La funzionalità NRT in generale consente la covarianza ragionevole per il supporto di valori null per parametri/restituzione. Tuttavia, il linguaggio esegue il controllo della covarianza sulla base dell'override più specifico, non della dichiarazione originale. Ciò significa che l'autore di Assembly3 riceverà un avviso sul tipo di `o` non corrispondente e sarà necessario modificare la firma nel seguente per eliminarlo: 

``` csharp
// Assembly3
abstract class C3 : C2 { 
    protected override void M(object? o!) {
        ...
    }
}
```

A questo punto l'autore di Assembly3 dispone di alcune opzioni:

- Possono accettare o evitare l'avviso relativo a `object?` e `object` non corrispondenti.
- Possono accettare o evitare l'avviso relativo a `object?` e `!` non corrispondenti.
- Possono semplicemente rimuovere il controllo di convalida `null` (eliminare `!` ed eseguire il controllo esplicito)

Si tratta di uno scenario reale, ma per il momento è opportuno procedere con l'avviso. Se l'avviso si verifica più spesso del previsto, è possibile rimuoverlo in un secondo momento (il contrario non è vero).

### <a name="implicit-property-setter-arguments"></a>Argomenti del setter di proprietà implicite
L'argomento `value` di un parametro è implicito e non viene visualizzato in un elenco di parametri. Ciò significa che non può essere la destinazione di questa funzionalità. È possibile estendere la sintassi del setter della proprietà per includere un elenco di parametri per consentire l'applicazione dell'operatore `!`. Questa funzionalità, tuttavia, riduce l'idea di questa funzionalità rendendo più semplice la convalida `null`. Di conseguenza, l'argomento `value` implicito non funzionerà con questa funzionalità.

## <a name="future-considerations"></a>Considerazioni
nessuno
