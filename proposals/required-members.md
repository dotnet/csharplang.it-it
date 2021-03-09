---
ms.openlocfilehash: ba36f1c95ee399c90885c351edc48b079c3bf860
ms.sourcegitcommit: 8a5955168e85fec63b404189cbbe8ca79c4fb34c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102470119"
---
# <a name="required-members"></a>Membri obbligatori

## <a name="summary"></a>Riepilogo

Questa proposta aggiunge un modo per specificare che una proprietà o un campo deve essere impostato durante l'inizializzazione dell'oggetto, forzando l'autore dell'istanza a fornire un valore iniziale per il membro in un inizializzatore di oggetto nel sito di creazione.

## <a name="motivation"></a>Motivazione

Le gerarchie di oggetti oggi richiedono una grande quantità di standard per il trasporto di dati in tutti i livelli della gerarchia. Verrà ora esaminata una semplice gerarchia che interessa un `Person` come potrebbe essere definita in C# 8:

```cs
class Person
{
    public string FirstName { get; }
    public string MiddleName { get; }
    public string LastName { get; }

    public Person(string firstName, string lastName, string? middleName = null)
    {
        FirstName = firstName;
        LastName = lastName;
        MiddleName = middleName ?? string.Empty;
    }
}

class Student : Person
{
    public int ID { get; }
    public Person(int id, string firstName, string lastName, string? middleName = null)
        : base(firstName, lastName, middleName)
    {
        ID = id;
    }
}
```

In questo articolo è disponibile un numero elevato di ripetizioni:

1. Alla radice della gerarchia, il tipo di ogni proprietà deve essere ripetuto due volte e il nome deve essere ripetuto quattro volte.
2. Al livello derivato, il tipo di ogni proprietà ereditata deve essere ripetuto una volta e il nome deve essere ripetuto due volte.

Si tratta di una gerarchia semplice con 3 proprietà e 1 livello di ereditarietà, ma molti esempi reali di questi tipi di gerarchie passano a molti livelli, accumulando un numero maggiore e maggiore di proprietà da passare durante l'esecuzione. Roslyn è uno di questi codebase, ad esempio, nei vari tipi di albero che rendono CSTs e AST. Questo annidamento è abbastanza noioso da consentire ai generatori di codice di generare i costruttori e le definizioni di questi tipi e molti clienti hanno approcci simili al problema. In C# 9 sono stati introdotti i record, che per alcuni scenari può migliorare:

```cs
record Person(string FirstName, string LastName, string MiddleName = "");
record Student(int ID, string FirstName, string LastName, string MiddleName = "") : Person(FirstName, LastName, MiddleName);
```

`record`Elimina la prima origine di duplicazione, ma la seconda origine di duplicazione rimane invariata. Sfortunatamente, si tratta dell'origine della duplicazione che cresce man mano che la gerarchia cresce ed è la parte più dolorosa della duplicazione da correggere dopo aver apportato una modifica alla gerarchia, in quanto è necessaria per l'inseguimento della gerarchia attraverso tutti i relativi percorsi, possibilmente anche tra

Come soluzione alternativa per evitare questa duplicazione, sono stati rilevati i consumer che abbracciano gli inizializzatori di oggetto come un modo per evitare la scrittura di costruttori. Prima di C# 9, tuttavia, questo aveva due svantaggi principali:

1. La gerarchia di oggetti deve essere completamente modificabile, con `set` funzioni di accesso per ogni proprietà.
2. Non esiste alcun modo per garantire che ogni istante di un oggetto del grafico imposti ogni membro.

In C# 9 è stato risolto il primo problema, introducendo la `init` funzione di accesso, queste proprietà possono essere impostate per la creazione o l'inizializzazione di un oggetto, ma non successivamente. Tuttavia, abbiamo ancora il secondo problema: le proprietà in C# sono state facoltative a partire da C# 1,0. I tipi di riferimento Nullable, introdotti in C# 8,0, fanno parte di questo problema: se un costruttore non Inizializza una proprietà di tipo riferimento non nullable, viene visualizzato un avviso per l'utente. Tuttavia, questo non risolve il problema: l'utente non vuole ripetere le parti di tipo grande nel costruttore, ma desidera passare la _richiesta_ di impostare le proprietà per gli utenti. Non fornisce inoltre avvisi relativi a `ID` da `Student` , poiché si tratta di un tipo di valore. Questi scenari sono estremamente comuni nel modello di database ORM, ad esempio EF Core, che necessitano di un costruttore pubblico senza parametri, ma che quindi guidano i valori Null delle righe in base al supporto di valori Null delle proprietà.

Questa proposta cerca di risolvere questi problemi introducendo una nuova funzionalità per C#: membri obbligatori. I membri obbligatori dovranno essere inizializzati dai consumer, anziché dall'autore del tipo, con varie personalizzazioni per consentire la flessibilità per più costruttori e altri scenari.

## <a name="detailed-design"></a>Progettazione dettagliata

`class``struct` `record` i tipi, e consentono di dichiarare un _\_ \_ elenco di membri obbligatorio_. Questo elenco è l'elenco di tutte le proprietà e i campi di un tipo considerati _necessari_ e deve essere inizializzato durante la costruzione e l'inizializzazione di un'istanza del tipo. I tipi ereditano automaticamente questi elenchi dai rispettivi tipi di base, offrendo un'esperienza apparentemente non intuitiva che rimuove codice standard e ripetitivo.

### <a name="required-modifier"></a>`required` modificatore

Si aggiunge `'required'` all'elenco di modificatori nel modificatore di _campo \__ e nel _\_ modificatore della proprietà_. L' _\_ \_ elenco di membri obbligatorio_ di un tipo è costituito da tutti i membri a cui sono stati `required` applicati. Di conseguenza, il `Person` tipo di precedenti ora è simile al seguente:

```cs
public class Person
{
    // The default constructor requires that FirstName and LastName be set at construction time
    public required string FirstName { get; init; }
    public string MiddleName { get; init; } = "";
    public required string LastName { get; init; }
}
```

Tutti i costruttori in un tipo che dispone di _un \_ \_ elenco di membri obbligatorio_ annuncia automaticamente un _contratto_ che i consumer del tipo devono inizializzare tutte le proprietà nell'elenco. È un errore per un costruttore annunciare un contratto che richiede un membro che non è almeno accessibile del costruttore. Ad esempio:

```cs
public class C
{
    public required int Prop { get; protected init; }

    // Advertises that Prop is required. This is fine, because the constructor is just as accessible as the property initer.
    protected C() {}

    // Error: ctor C(object) is more accessible than required property Prop.init.
    public C(object otherArg) {}
}
```

`required` è valido solo nei `class` `struct` tipi, e `record` . Non è valido nei `interface` tipi.

### <a name="init-clauses"></a>`init` Clausole

Un costruttore può rimuovere un membro obbligatorio dal relativo contratto aggiungendo una `init` clausola che specifica il nome del membro da rimuovere. Ad esempio:

```cs
public class C
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    // Advertises that just Prop1 is required.
    public C() : init(Prop2)
    {
        Prop2 = 2;
        Console.WriteLine($"Prop2 is {Prop2}")
    }
}
```

Una clausola INIT può inoltre fornire il valore di inizializzazione per la proprietà inline. Queste assegnazioni vengono eseguite prima che venga eseguito il corpo del costruttore, dopo la chiamata di base se ne esiste una:

```cs
public class C
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    // Sets Prop2 to 2 before the constructor body is run
    public C() : init(Prop2 = 2)
    {
        Console.WriteLine($"Prop2 is {Prop1}")
    }
}
```

Una clausola INIT può rimuovere tutti i requisiti usando l' `init required` abbreviazione:

```cs
public class C
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    // Advertises that there are no requirements
    public C() : init required
    {
        Prop1 = 1;
        Prop2 = 2;
    }
}
```

_\_ \_ elenco di membri obbligatorio_ catena in una gerarchia di tipi. Il _contratto_ di un costruttore non include solo i membri obbligatori del tipo corrente, ma anche i membri obbligatori dal tipo di base e dalle interfacce che implementa. Se il costruttore derivato chiama un costruttore di base che rimuove alcuni di questi membri dall'elenco, il costruttore derivato rimuove anche tali membri dall'elenco.

```cs
public class Base
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    public Base() : init(Prop1 = 1) {}
}

public class Derived
{
    public required int Prop3 { get; set; }
    public required int Prop4 { get; set; }

    // Only advertises that Prop2 and Prop3 are required, because `base()` removed Prop1 from the list, and the init clause removes Prop4
    public Derived : base() init(Prop4 = 4) { }
}
```

### <a name="initialization-requirement"></a>Requisito di inizializzazione

I membri specificati in una clausola INIT devono essere assegnati definitivamente alla fine del corpo del costruttore. In caso contrario, viene generato un errore. Per supportare una logica di inizializzazione più complessa, questo errore può essere eliminato utilizzando l' `!` operatore:

```cs
public class C
{
    public required int Prop { get; set; }

    // Error: Prop is not definitely assigned at the end of the constructor body
    public C(int param) : init(Prop)
    {
        Initialize(param);
    }

    // No error: Prop! suppresses it
    public C() : init(Prop!)
        => Initialize(1);

    public void Initialize(int param) => Prop = param;
}
```

`!`È anche possibile applicare l'operatore a per evitare `init required` il controllo di tutte le proprietà obbligatorie per un tipo.

```cs
public class C
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    // No errors: init required! suppresses
    public C() : init required! {}
}
```

#### <a name="grammar"></a>Grammatica

La grammatica per un `constructor_initializer` viene modificata come segue:

```antlr
constructor_initializer
    : ':' constructor_chain
    | ':' init_clause
    | ':' constructor_chain init_clause
    ;

constructor_chain
    : 'base' '(' argument_list? ')'
    | 'this' '(' argument_list? ')'
    ;

init_clause
    : 'init' '(' init_argument_list ')'
    | 'init' 'required' '!'?
    ;

init_argument_list
    : init_argument (',' init_argument)*
    ;

init_argument
    : identifier init_argument_initializer?
    | identifier '!'
    ;

init_argument_initializer
    : '=' expression
    ;
```

### <a name="new-constraint"></a>`new()` vincolo

Un tipo con un costruttore senza parametri che annuncia un _contratto_ non può essere sostituito da un parametro di tipo vincolato a `new()` , perché non esiste alcun modo per la creazione di un'istanza generica per garantire che i requisiti siano soddisfatti.

## <a name="open-questions"></a>Domande aperte

### <a name="syntax-questions"></a>Domande sulla sintassi

* Qual è `init` la parola giusta? `init` un modificatore di suffisso nel costruttore potrebbe interferire se si vuole riutilizzarlo per le factory e abilitare anche `init` metodi con un modificatore di prefisso. Altre possibilità:
    * `set`
* `required`Il modificatore di destra per specificare che tutti i membri vengono inizializzati? Altri suggerimenti:
    * `default`
    * `all`
    * Con un! per indicare la logica complessa
* È necessario un separatore tra `base` / `this` e `init` ?
    * `:` separatore
    * separatore ','
* `required`Il modificatore è corretto? Altre alternative suggerite:
    * `req`
    * `require`
    * `mustinit`
    * `must`
    * `explicit`

### <a name="init-clause-restrictions"></a>Limitazioni della clausola INIT

È necessario consentire l'accesso a `this` nella clausola INIT? Se si vuole che l'assegnazione in `init` sia una sintassi abbreviata per l'assegnazione del membro nel costruttore stesso, sembra che sia necessario.

Inoltre, crea un nuovo ambito, `base()` ad esempio, o condivide lo stesso ambito del corpo del metodo? Questa operazione è particolarmente importante per elementi come le funzioni locali, a cui la clausola INIT può accedere o per lo shadowing del nome, se un'espressione init introduce una variabile tramite il `out` parametro.

### <a name="base-requirement-chaining-representation-in-metadata"></a>Rappresentazione concatenamento requisito base nei metadati

Per un'implementazione ideale della rappresentazione dei metadati, ogni costruttore contrassegna il costruttore di base che chiama in un certo modo. in questo modo, se i tipi base e derivati si trovano in assembly diversi, un aggiornamento della versione nell'assembly di base verrebbe riflesso accuratamente nell'utilizzo del tipo derivato senza dover aggiornare l'assembly derivato. Tuttavia, non è possibile codificare un token di metodo in una firma, quindi è necessario trovare un'altra strategia di codifica. Questa strategia sarà intrinsecamente fragile a diversi scenari potenziali, pertanto potrebbe essere più pragmatico ripetere semplicemente tutti i membri rimossi nell'elenco dei membri rimossi del costruttore derivato.

### <a name="warning-vs-error"></a>Errore di Visual Studio

Non impostare un membro necessario come un avviso o un errore? È certamente possibile ingannare il sistema, tramite `Activator.CreateInstance(typeof(C))` o similare, il che significa che potrebbe non essere possibile garantire completamente che tutte le proprietà siano sempre impostate. Viene anche consentita l'eliminazione della diagnostica nel sito del costruttore tramite `!` , che in genere non consente errori. Tuttavia, la funzionalità è analoga ai campi di sola lettura o alle proprietà init, in quanto si è riscontrato un errore hardware se gli utenti tentano di impostare tale membro dopo l'inizializzazione, ma possono essere elusi tramite reflection.

### <a name="silly-diagnostics"></a>Diagnostica "sciocco"

Dato questo codice:

```cs
class C
{
    public required object? O;
    public C() : init(O = null) {}
}
```

Dovrebbe emettere un tipo di diagnostica `O` contrassegnato come obbligatorio, ma mai richiesto in un contratto? Gli sviluppatori possono trovare le proprietà contrassegnate come necessarie per essere utili come rete di sicurezza.

## <a name="discussed-questions"></a>Domande discusse

### <a name="level-of-enforcement-for-init-clauses"></a>Livello di imposizione per le `init` clausole

Si impone rigorosamente che i membri specificati in una `init` clausola senza inizializzatore debbano inizializzare tutti i membri? È probabile che l'operazione venga eseguita. in caso contrario, viene creato un semplice guasto. Tuttavia, viene anche eseguito il rischio di introdurre nuovamente gli stessi problemi risolti con `MemberNotNull` in C# 9. Se si vuole applicare questa impostazione, è probabile che un metodo helper indichi un metodo per indicare che è necessario impostare un membro. Di seguito sono riportate alcune possibili sintassi illustrate in questo argomento:

* `init`Metodi allow. Questi metodi possono essere chiamati solo da un costruttore o da un altro `init` metodo e possono accedere `this` come se fosse nel costruttore (IE, set `readonly` e `init` Fields/Properties). Questo può essere combinato con `init` clausole su tali metodi. Una `init` clausola viene considerata soddisfatta se il membro nella clausola viene assegnato definitivamente nel corpo del metodo o del costruttore. La chiamata a un metodo con una `init` clausola che include un membro viene conteggiata come assegnazione a tale membro.
Se si decide che si tratta di una route che si vuole perseguire, ora o in futuro, è probabile che non si debba usare `init` come parola chiave per la clausola init in un costruttore, perché questo potrebbe creare confusione.
* Consente all' `!` operatore di disattivare in modo esplicito l'avviso o l'errore. Se l'inizializzazione di un membro viene eseguita in modo complesso, ad esempio in un metodo condiviso, l'utente può aggiungere un oggetto `!` alla clausola INIT per indicare che il compilatore non deve verificare la presenza di inizializzazione.

Dopo la discussione, si vuole l'idea dell' `!` operatore. Consente all'utente di essere intenzionale su scenari più complessi, ma anche di non creare un foro di progettazione di grandi dimensioni intorno ai metodi init e annotare ogni metodo come impostare i membri X o Y. `!` è stato scelto perché è già stato usato per la disattivazione degli avvisi nullable e viene usato per indicare al compilatore "sono più intelligente di te" in un'altra posizione è un'estensione naturale del formato di sintassi.

### <a name="required-interface-members"></a>Membri di interfaccia necessari

Questa proposta non consente alle interfacce di contrassegnare i membri come richiesto. In questo modo è possibile evitare di dover individuare scenari complessi `new()` e vincoli di interfaccia nei generics in questo momento ed è direttamente correlato alle fabbriche e alla costruzione generica. Per assicurarsi di avere spazio di progettazione in quest'area, non è possibile usare le `required` interfacce e impedire ai tipi _con \_ \_ elenchi di membri richiesti_ di sostituire i parametri di tipo vincolati a `new()` . Quando si vuole esaminare in modo più ampio gli scenari di costruzione generici con le factory, è possibile rivedere questo problema.
