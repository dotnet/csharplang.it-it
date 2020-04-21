---
ms.openlocfilehash: ebbdab6d121f3001ac34a953b3de09768cda6344
ms.sourcegitcommit: 3f177e90b12e39d4d28f8bb1064df81a8e5912ca
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726068"
---
<a name="init-only-members"></a>Membri solo Init
=====

## <a name="summary"></a>Summary
Questa proposta aggiunge il concetto di proprietà solo init a C . Queste proprietà possono essere impostate nel punto `get` di creazione dell'oggetto, ma diventano effettissuose solo dopo il completamento della creazione dell'oggetto. In questo modo è possibile ottenere un modello non modificabile molto più flessibile in C. 

## <a name="motivation"></a>Motivazione
I meccanismi sottostanti per la creazione di dati non modificabili in C , non sono stati modificati dopo 1.0.The underlying mechanisms for building immutable data in C's no changed since 1.0. Essi rimangono:

1. Dichiarazione di `readonly`campi come .
1. Dichiarazione di proprietà `get` che contengono solo una funzione di accesso.

Questi meccanismi sono efficaci per consentire la costruzione di dati non modificabili, ma lo fanno aggiungendo il costo al codice boilerplate dei tipi e scegliendo tali tipi da funzionalità come gli inizializzatori di oggetto e di raccolta. Ciò significa che gli sviluppatori devono scegliere tra facilità d'uso e immutabilità.

Un semplice oggetto non `Point` modificabile come richiede il doppio del codice della piastra della caldaia per supportare la costruzione rispetto a dichiarare il tipo. Più grande è il tipo più grande è il costo di questa piastra caldaia:

```cs
struct Point
{
    public int X { get; }
    public int Y { get; }

    public Point(int X, int Y)
    {
        this.X = x;
        this.Y = y;
    }
}
```

La `init` funzione di accesso rende gli oggetti non modificabili più flessibili consentendo al chiamante di mutare i membri durante l'atto di costruzione. Ciò significa che le proprietà non modificabili dell'oggetto possono partecipare agli inizializzatori di oggetto e quindi elimina la necessità di tutti i boilerplate del costruttore nel tipo. Il `Point` tipo è ora semplicemente:

```cs
struct Point
{
    public int X { get; init; }
    public int Y { get; init; }
}
```

Il consumer può quindi utilizzare gli inizializzatori di oggetto per creare l'oggetto

```cs
var p = new Point() { X = 42, Y = 13 };
```

## <a name="detailed-design"></a>Progettazione dettagliata

### <a name="init-members"></a>membri init
Una proprietà init only viene `init` dichiarata utilizzando la `set` funzione di accesso al posto della funzione di accesso:

```cs
class Student
{
    public string FirstName { get; init; }
    public string LastName { get; init; }
}
```

Una proprietà di `init` istanza contenente una funzione di accesso viene considerata impostabile nelle circostanze seguenti:An instance property containing an accessor is considered settable in the following circumstances:

- Durante un inizializzatore di oggetto
- All'interno di un costruttore di `this` istanza del tipo contenitore o derivato,`base`
- All'interno dell'accesso `init` di `this` qualsiasi proprietà, su o`base`

I tempi precedenti `init` in cui le funzioni di accesso sono impostabili sono collettivamente indicati in questo documento come la fase di costruzione dell'oggetto.

Ciò `Student` significa che la classe può essere utilizzata nei modi seguenti:This means the class can be used in the following ways:

```cs
var s = new Student()
{
    FirstName = "Jared",
    LastName = "Parosns",
};
s.LastName = "Parsons"; // Error: LastName is not settable
```

Le regole `init` relative alle funzioni di accesso sono impostabili tra gerarchie di tipi. Se il membro è accessibile e l'oggetto è noto per essere in fase di costruzione, il membro è impostabile. Ciò consente specificamente quanto segue:

```cs
class Base
{
    public bool Value { get; init; }
}

class Derived : Base
{
    Derived()
    {
        // Not allowed with get only properties but allowed with init
        Value = true;
    }
}

class Consumption
{
    void Example()
    {
        var d = new Derived() { Value = true; };
    }
}

```

Nel punto `init` in cui viene richiamata una funzione di accesso, l'istanza è nota per essere nella fase di costruzione aperta. Pertanto `init` una funzione di accesso può eseguire le `set` seguenti azioni oltre a ciò che una normale funzione di accesso può fare:

1. Chiamare `init` altre funzioni `this` di accesso disponibili tramite o`base`
1. Assegnare `readonly` campi dichiarati nello stesso tipo

```cs
class Complex
{
    readonly int Field1;
    int Field2;
    int Prop1 { get; init ; }
    int Prop2
    {
        get => 42;
        init
        {
            Field1 = 13; // okay
            Field2 = 13; // okay
            Prop1 = 13; // okay
        }
    }
}
```

La possibilità `readonly` di assegnare campi da una `init` funzione di accesso è limitata ai campi dichiarati nello stesso tipo della funzione di accesso. Non può essere `readonly` utilizzato per assegnare campi in un tipo di base. Questa regola garantisce che gli autori dei tipi mandino il controllo sul comportamento di mutabilità del tipo. Gli sviluppatori che non `init` desiderano utilizzare non possono essere influenzati da altri tipi che scelgono di farlo:

```cs
class Base
{
    internal readonly int Field;
    internal int Property
    {
        get => Field;
        init => Field = value; // Okay
    }

    internal int OtherProperty { get; init; }
}

class Derived : Base
{
    internal readonly int DerivedField;
    internal int DerivedProperty
    {
        get => DerivedField;
        init
        {
            DerivedField = 42;  // Okay
            Property = 0;       // Okay
            Field = 13;         // Error Field is readonly
        }
    }

    public Derived()
    {
        Property = 42;  // Okay 
        Field = 13;     // Error Field is readonly
    }
}
```

Quando `init` viene utilizzato in una proprietà virtuale, anche `init`tutti gli override devono essere contrassegnati come . Allo stesso modo non è `set` possibile `init`eseguire l'override di un semplice con .

```cs
class Base
{
    public virtual int Property { get; init; }
}

class C1 : Base
{
    public override int Property { get; init; }
}

class C2 : Base
{
    // Error: Property must have init to override Base.Property
    public override int Property { get; set; }
}
```

Una `interface` dichiarazione può `init` anche partecipare all'inizializzazione dello stile tramite il modello seguente:An declaration can also participate in style initialization via the following pattern:

```cs
interface IPerson
{
    string Name { get; init; }
}

class Init
{
    void M<T>() where T : IPerson, new()
    {
        var local = new T()
        {
            Name = "Jared"
        };
        local.Name = "Jraed"; // Error
    }
}
```

Restrizioni di questa funzione:
- La `init` funzione di accesso può essere utilizzata solo sulle proprietà dell'istanzaThe accessor can only be used on instance properties
- Una proprietà non `init` può `set` contenere entrambe una funzione di accesso e
- Tutti gli override di `init` una proprietà `init`devono avere se la base dispone di . Questa regola si applica anche all'implementazione dell'interfaccia.

### <a name="metadata-encoding"></a>Codifica dei metadati 
Le `init` funzioni di accesso alle `set` proprietà verranno generate come funzione di `IsInitOnly`accesso standard con il tipo restituito contrassegnato con un modreq di . Questo è un nuovo tipo che avrà la seguente definizione:

```cs
namespace System.Runtime.CompilerServices
{
    public sealed class IsInitOnly
    {
    }
}
```

Il compilatore corrisponderà al tipo in base al nome completo. Non è necessario che venga visualizzato nella libreria principale. Se sono presenti più tipi con questo nome, il compilatore legherà break nel seguente ordine:

1. Quello definito nel progetto in fase di compilazione
1. Quello definito in corelib

Se nessuno di questi esiste, verrà generato un errore di ambiguità del tipo.

Il design `IsInitOnly` per è più coperto in [questo numero](https://github.com/dotnet/runtime/issues/34978)

## <a name="questions"></a>Domande

### <a name="breaking-changes"></a>Modifiche di rilievo
Uno dei principali punti cardine di come questa funzione è codificata scenderà alla seguente domanda: 

> Si tratta di una `init` modifica `set`di rottura binaria per sostituire con ?

La `init` sostituzione `set` con e rendendo così una proprietà completamente scrivibile non è mai una modifica di rilievo di origine in una proprietà non virtuale. Espande semplicemente il set di scenari in cui è possibile scrivere la proprietà. L'unico comportamento in questione è se questo rimane o meno un cambiamento di rottura binaria.

Se vogliamo fare il `init` `set` cambiamento di una fonte e binario modifica compatibile allora ci costringerà la nostra mano sul modreq vs. Se d'altra parte questo è visto come un non-interessante allora questo renderà il modreq vs. decisione attributo meno impatto.

**Risoluzione** Questo scenario non è considerato convincente da LDM.

### <a name="modreqs-vs-attributes"></a>Modreqs e attributi
La strategia `init` di generazione per le funzioni di accesso alle proprietà deve scegliere tra l'utilizzo di attributi o modreq quando si emette durante i metadati. Questi hanno diversi compromessi che devono essere considerati.

L'aggiunta di una funzione di accesso set di proprietà con una dichiarazione modreq significa che i compilatori compatibili con CLI ignoreranno la funzione di accesso a meno che non comprenda il modreq. Ciò significa che `init` solo i compilatori consapevoli di leggerà il membro. I compilatori `init` che non `set` sono a conoscenza ignoreranno il membro e pertanto non considereranno accidentalmente la proprietà come lettura/scrittura. 

Lo svantaggio di modreq diventa `init` una parte `set` della firma binaria della funzione di accesso. L'aggiunta `init` o la rimozione interromperà la compatibilità binaria dell'applicazione.

L'utilizzo di `set` attributi per annotare la funzione di accesso significa che solo i compilatori che comprendono l'attributo sapranno limitare l'accesso ad esso. Un compilatore `init` inconsapevole verrà visualizzato come una semplice proprietà di lettura/scrittura e consentirà l'accesso.

Questo sarebbe apparentemente significato questa decisione è una scelta tra la sicurezza supplementare a scapito della compatibilità binaria. Scavare un po 'la sicurezza in più non è esattamente quello che sembra. Non proteggerà dalle seguenti circostanze:

1. Riflessione `public` sui membri
1. L'uso di`dynamic` 
1. Compilatori che non riconoscono modreqs

Va anche considerato che, una volta completate le regole `init` di verifica IL per .NET 5, sarà una di queste regole. Ciò significa che l'applicazione aggiuntiva sarà ottenuta dalla semplice verifica dei compilatori che emettono IL verificabile.

I linguaggi principali per .NET (C , F e VB) `init` verranno tutti aggiornati per riconoscere queste funzioni di accesso. Quindi l'unico scenario realistico qui è `init` quando un compilatore c '9 emette proprietà e sono visti da un vecchio set di strumenti come C , VB 15, ecc ... Il linguaggio C. 8. Questo è il compromesso da considerare e pesare contro la compatibilità binaria.

**Nota:** Questa discussione si applica principalmente ai membri, non ai campi. Mentre `init` i campi sono stati rifiutati da LDM sono ancora interessanti da considerare per la discussione modreq vs attributo. La `init` caratteristica per i campi è `readonly`un rilassamento della restrizione esistente di . Ciò significa che se `readonly` emettiamo i campi come : un attributo non vi è `readonly`alcun rischio di precedenti compilatori uso improprio del campo perché sarebbero già riconoscere . Quindi l'utilizzo di un modreq qui non aggiunge alcuna protezione aggiuntiva.

**Risoluzione** La funzione utilizzerà un modreq `init` per codificare il setter della proprietà. I fattori convincente erano (in nessun ordine particolare):

* Desiderio di scoraggiare i `init` compilatori meno recenti dalla violazione della semantica
* Desiderio di effettuare `init` l'aggiunta o la rimozione in una `virtual` dichiarazione o `interface` sia una modifica di interruzione di origine e binaria.

Dato non c'era anche `init` alcun supporto significativo per la rimozione di essere una modifica binaria compatibile ha fatto la scelta di utilizzare modreq dritto in avanti.

### <a name="init-vs-initonly"></a>init vs.
Ci sono state tre forme di sintassi che hanno ottenuto una considerazione significativa durante la nostra riunione LDM:

```cs
// 1. Use init 
int Option1 { get; init; }
// 2. Use init set
int Option2 { get; init set; }
// 3. Use initonly
int Option3 { get; initonly; }
```

**Risoluzione** Non c'era nessuna sintassi che era schiacciante favorito in LDM.

Un punto che ha ottenuto un'attenzione significativa è `init` stato come la scelta della sintassi avrebbe influito sulla nostra capacità di fare i membri come una caratteristica generale in futuro.
Scegliere l'opzione 1 significherebbe che sarebbe difficile definire `init` `get` una proprietà che abbia un metodo di stile in futuro. Alla fine si è deciso che se `init` abbiamo deciso di `init` andare avanti con i membri generali in futuro `init set`potremmo consentire di essere un modificatore nella lista delle funzioni di accesso di proprietà, nonché una breve mano per . Essenzialmente le due dichiarazioni seguenti sarebbero identiche.

```cs
int Property1 { get; init; }
int Property1 { get; init set; }
```

È stata presa la `init` decisione di procedere con come funzione di accesso autonoma nell'elenco delle finestre di accesso alle proprietà.

### <a name="warn-on-failed-init"></a>Avvisa in caso di mancato init
Si consideri lo scenario seguente. Un tipo dichiara `init` un unico membro che non è impostato nel costruttore. Il codice che costruisce l'oggetto deve ricevere un avviso se non è stato possibile inizializzare il valore?

A quel punto è chiaro che il campo non sarà mai impostato e quindi `private` ha molte somiglianze con l'avviso intorno a non riuscire a inizializzare i dati. Quindi un avvertimento sarebbe apparentemente avere qualche valore qui?

Ci sono aspetti negativi significativi di questo avviso però:
1. Si complica la storia di `readonly` `init`compatibilità di passare a . 
1. Richiede di portare metadati aggiuntivi per indicare i membri che devono essere inizializzati dal chiamante.

Inoltre, se crediamo che ci sia valore qui nello scenario generale di costringere i creatori di oggetti per essere avvertiti / error'd su campi specifici, allora questo probabilmente ha senso come una caratteristica generale. Non vi è alcun motivo `init` per cui dovrebbe essere limitato solo ai membri.

**Risoluzione** Non ci sarà alcun `init` avviso sul consumo di campi e proprietà.

LDM vuole avere una discussione più ampia sull'idea di campi e proprietà obbligatori. Questo potrebbe indurci a tornare e `init` riconsiderare la nostra posizione sui membri e la convalida.

## <a name="allow-init-as-a-field-modifier"></a>Consenti init come modificatore di campo
Allo stesso `init` modo può servire come una funzione di accesso di proprietà potrebbe anche `init` servire come una designazione sui campi per dare loro comportamenti simili come proprietà.
Ciò consentirebbe l'assegnazione del campo prima del completamento della costruzione da parte del tipo, dei tipi derivati o degli inizializzatori di oggetto.

```cs
class Student
{
    public init string FirstName;
    public init string LastName;
}

var s = new Student()
{
    FirstName = "Jarde",
    LastName = "Parsons",
}

s.FirstName = "Jared"; // Error FirstName is readonly
```

Nei metadati questi campi verrebbero `readonly` contrassegnati allo stesso modo dei campi, `init` ma con un attributo aggiuntivo o modreq per indicare che sono campi di stile. 

**Risoluzione** LDM è d'accordo che questa proposta è valida, ma nel complesso lo scenario si sentiva disgiunto dalle proprietà. La decisione è stata `init` quella di procedere solo con le proprietà per ora. Questo ha un livello adeguato `init` di flessibilità `readonly` in quanto una proprietà può mutare un campo sul tipo dichiarante della proprietà. Questo verrà riconsiderato se vi è un feedback significativo dei clienti che giustifica lo scenario.

### <a name="allow-init-as-a-type-modifier"></a>Allow init come modificatore di tipo
Allo stesso modo `readonly` in cui il `struct` modificatore può `readonly`essere `init` applicato a a per `struct` `class` dichiarare automaticamente tutti `init`i campi come , l'unico modificatore può essere dichiarato su o per contrassegnare automaticamente tutti i campi come .
Ciò significa che le due dichiarazioni di tipo seguenti sono equivalenti:This means the following two type declarations are equivalent:

```cs
struct Point
{
    public init int X;
    public init int Y;
}

// vs. 

init struct Point
{
    public int X;
    public int Y;
}
```

**Risoluzione** Questa funzione è troppo *carina* `readonly struct` qui e contrasta con la funzione su cui si basa. La `readonly struct` funzione è semplice `readonly` in quanto si applica a tutti i membri: campi, metodi, ecc ... La `init struct` funzionalità si applicherebbe solo alle proprietà. Questo in realtà finisce per rendere confuso per gli utenti. 

Dato `init` che è valido solo su alcuni aspetti di un tipo abbiamo respinto l'idea di averlo come modificatore di tipo.

## <a name="considerations"></a>Considerazioni

### <a name="compatibility"></a>Compatibilità
La `init` funzione è progettata per `get` essere compatibile con le sole proprietà esistenti. In particolare è destinato ad essere un cambiamento `get` completamente additivo per una proprietà che è solo oggi, ma desidera più flexbile oggetto semantica di creazione.

Si consideri ad esempio il tipo seguente:For example consider the following type:

```cs
class Name
{
    public string First { get; }
    public string Last { get; }

    public Name(string first, string last)
    {
        First = first;
        Last = last;
    }
}
```

L'aggiunta a queste proprietà è una modifica unificatore:Adding `init` to these properties is a un-breaking change:

```cs
class Name
{
    public string First { get; init; }
    public string Last { get; init; }

    public Name(string first, string last)
    {
        First = first;
        Last = last;
    }
}
```

### <a name="il-verification"></a>Verifica IL
Quando .NET Core decide di implementare nuovamente la verifica IL, `init` le regole dovranno essere modificate per tenere conto dei membri. Questo dovrà essere incluso nelle modifiche delle regole per `readonly` non-mutating acess ai dati.

Le regole di verifica IL dovranno essere suddivise in due parti: 

1. Consentire `init` ai `readonly` membri di impostare un campo.
1. Determinare quando `init` un membro può essere legalmente chiamato.

Il primo è un semplice adeguamento alle regole esistenti. Il verificatore IL può essere `init` insegnato a riconoscere i membri `readonly` e da lì `this` deve solo considerare un campo da impostare in tale membro.

La seconda regola è più complessa. Nel semplice caso degli inizializzatori di oggetto, la regola è semplice. Deve essere legale `init` chiamare i membri `new` quando il risultato di un'espressione è ancora nello stack. Questo fino a quando il valore non è stato archiviato in un locale, elemento di `init` matrice o campo o passato come argomento a un altro metodo sarà comunque valido chiamare i membri. In questo modo, una `new` volta pubblicato il risultato dell'espressione in un identificatore denominato (diverso `this`da ), non sarà più valido chiamare `init` i membri. 

Il caso più complicato `init` però è quando `await`si combinano membri, inizializzatori di oggetto e . Ciò può causare l'oggetto appena creato per essere temporaneamente issato in una macchina a stati e quindi messo in un campo.

```cs
var student = new Student() 
{
    Name = await SomeMethod()
};
```

Qui il `new Student()` risultato di sarà issato in una `Name` macchina a stati come un campo prima che si verifichi il set di si verifica. Il compilatore dovrà contrassegnare tali campi issati in modo che il verificatore IL comprenda che non `init`sono accessibili all'utente e pertanto non violi la semantica prevista di .

### <a name="init-members"></a>membri init
Il `init` modificatore può essere esteso per essere applicato a tutti i membri di istanza. Ciò generalizzerebbe `init` il concetto di durante la costruzione dell'oggetto e consentirebbe `init` ai tipi di dichiarare metodi helper che potrebbero partecipare al processo di costruzione per inizializzare campi e proprietà.

Tali membri avrebbero tutte le richieste `init` che una funzione di accesso fa in questo disegno. La necessità è discutibile però e questo può essere aggiunto in modo sicuro in una versione futura della lingua in modo compatibile.

### <a name="generate-three-accessors"></a>Generare tre funzioni di accessoGenerate three accessors
Una potenziale `init` implementazione `init` delle proprietà `set`consiste nel rendere completamente separati da . Ciò significa che una proprietà può potenzialmente `set` `init`avere tre diverse funzioni di accesso: `get`, e .

Questo ha il potenziale vantaggio di consentire l'uso di modreq per applicare la correttezza pur mantenendo la compatibilità binaria. L'attuazione sarebbe approssimativamente la seguente:

1. Una `init` funzione di accesso viene sempre `set`generata se è presente un oggetto . Quando non è definito dallo sviluppatore `set`è semplicemente un riferimento a . 
1. Il set di una proprietà in un `init` inizializzatore di `set` oggetto verrà sempre utilizzato se presente, ma eseguire il rollback a se manca.

Ciò significa che uno `init` sviluppatore può sempre eliminare in modo sicuro da una proprietà. 

Lo svantaggio di questo disegno è `init` che è utile solo `set`se viene **sempre** emesso quando c'è un . La lingua non può `init` sapere se è stato cancellato in passato, deve presumere che fosse e quindi il `init` deve sempre essere emesso. Ciò causerebbe un'espansione significativa dei metadati e semplicemente non vale il costo della compatibilità qui.
