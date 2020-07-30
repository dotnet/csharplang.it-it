---
ms.openlocfilehash: 0642f28e921ea224a157394226d43df2f3f81a10
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297514"
---
<a name="init-only-setters"></a>Setter solo init
=====

## <a name="summary"></a>Summary
Questa proposta aggiunge il concetto di proprietà e indicizzatori solo init a C#. Queste proprietà e indicizzatori possono essere impostati al momento della creazione dell'oggetto, ma diventano efficaci `get` solo dopo il completamento della creazione dell'oggetto.
In questo modo è possibile disporre di un modello non modificabile molto più flessibile in C#. 

## <a name="motivation"></a>Motivazione
I meccanismi sottostanti per la compilazione di dati non modificabili in C# non sono stati modificati dopo 1,0. Rimangono:

1. Dichiarazione di campi come `readonly` .
1. Dichiarazione delle proprietà che contengono solo una `get` funzione di accesso.

Questi meccanismi sono efficaci per consentire la costruzione di dati non modificabili, ma lo fanno aggiungendo costi al codice standard dei tipi e scegliendo tali tipi di funzionalità come gli inizializzatori di oggetto e di raccolta. Ciò significa che gli sviluppatori devono scegliere tra semplicità d'uso e immutabilità.

Un oggetto non modificabile semplice `Point` , ad esempio, richiede il doppio del codice della piastra calda per supportare la costruzione, così come per dichiarare il tipo. Maggiore è il tipo, maggiore è il costo di questa piastra caldatura:

```cs
struct Point
{
    public int X { get; }
    public int Y { get; }

    public Point(int x, int y)
    {
        this.X = x;
        this.Y = y;
    }
}
```

La `init` funzione di accesso rende più flessibili gli oggetti non modificabili consentendo al chiamante di mutare i membri durante l'esecuzione dell'operazione. Ciò significa che le proprietà non modificabili dell'oggetto possono partecipare agli inizializzatori di oggetto e pertanto elimina la necessità di tutti i tipi di standard del costruttore nel tipo. Il `Point` tipo è ora semplice:

```cs
struct Point
{
    public int X { get; init; }
    public int Y { get; init; }
}
```

Il consumer può quindi usare gli inizializzatori di oggetto per creare l'oggetto

```cs
var p = new Point() { X = 42, Y = 13 };
```

## <a name="detailed-design"></a>Progettazione dettagliata

### <a name="init-accessors"></a>funzioni di accesso init
Una proprietà (o un indicizzatore) solo init viene dichiarata utilizzando la `init` funzione di accesso al posto della `set` funzione di accesso:

```cs
class Student
{
    public string FirstName { get; init; }
    public string LastName { get; init; }
}
```

Una proprietà dell'istanza contenente una `init` funzione di accesso viene considerata impostabile nelle circostanze seguenti, tranne quando in una funzione o in una lambda locale:

- Durante un inizializzatore di oggetto
- Durante un `with` inizializzatore di espressione
- All'interno di un costruttore di istanza del tipo che lo contiene o derivato, su `this` o`base`
- All'interno della `init` funzione di accesso di qualsiasi proprietà, in `this` o`base`
- Utilizzi degli attributi interni con i parametri denominati

Gli orari in cui `init` è possibile impostare le funzioni di accesso sono definiti collettivamente in questo documento come fase di costruzione dell'oggetto.

Ciò significa che la `Student` classe può essere utilizzata nei modi seguenti:

```cs
var s = new Student()
{
    FirstName = "Jared",
    LastName = "Parosns",
};
s.LastName = "Parsons"; // Error: LastName is not settable
```

Le regole relative alle `init` funzioni di accesso che è possibile impostare si estendono in più gerarchie di tipi. Se il membro è accessibile e l'oggetto è noto come fase di costruzione, il membro è impostabile. Ciò consente in modo specifico quanto segue:

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

Nel punto in `init` cui viene richiamata una funzione di accesso, l'istanza è nota come fase di costruzione aperta. Di conseguenza `init` , una funzione di accesso può eseguire le azioni seguenti oltre a ciò che può essere eseguita da una `set` funzione di accesso normale:

1. Chiama altre `init` funzioni di accesso disponibili tramite `this` o`base`
1. Assegnare `readonly` campi dichiarati sullo stesso tipo tramite`this`

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

La possibilità di assegnare `readonly` campi da una `init` funzione di accesso è limitata ai campi dichiarati sullo stesso tipo della funzione di accesso. Non può essere usato per assegnare `readonly` campi in un tipo di base. Questa regola garantisce che gli autori dei tipi rimangano in grado di controllare il comportamento di modificabilità del tipo. Gli sviluppatori che non desiderano utilizzare `init` non possono essere interessati da altri tipi che scelgono di eseguire questa operazione:

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

Quando `init` si usa in una proprietà virtuale, anche tutte le sostituzioni devono essere contrassegnate come `init` . Analogamente, non è possibile eseguire l'override di una semplice `set` con `init` .

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

Una `interface` dichiarazione può anche partecipare all' `init` inizializzazione dello stile tramite il modello seguente:

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

Limitazioni di questa funzionalità:
- La `init` funzione di accesso può essere utilizzata solo nelle proprietà dell'istanza
- Una proprietà non può contenere sia `init` una `set` funzione di accesso che
- Tutte le sostituzioni di una proprietà devono avere `init` se la base era `init` . Questa regola si applica anche all'implementazione dell'interfaccia.

### <a name="metadata-encoding"></a>Codifica dei metadati 
`init`Le funzioni di accesso alle proprietà verranno emesse come `set` funzione di accesso standard con il tipo restituito contrassegnato con un modreq di `IsExternalInit` . Si tratta di un nuovo tipo che avrà la definizione seguente:

```cs
namespace System.Runtime.CompilerServices
{
    public sealed class IsExternalInit
    {
    }
}
```

Il compilatore corrisponderà al tipo in base al nome completo. Non è necessario che venga visualizzato nella libreria principale. Se sono presenti più tipi con questo nome, il compilatore verrà vincolato nell'ordine seguente:

1. Quello definito nel progetto da compilare
1. Quello definito in corelib

Se nessuna di queste esiste, verrà generato un errore di ambiguità del tipo.

Il progetto per `IsExternalInit` è più trattato in [questo problema](https://github.com/dotnet/runtime/issues/34978)

## <a name="questions"></a>Domande

### <a name="breaking-changes"></a>Modifiche di rilievo
Uno dei principali punti cardine nel modo in cui questa funzionalità viene codificata dipende dalla domanda seguente: 

> Si tratta di una modifica di rilievo binaria da sostituire `init` con `set` ?

La sostituzione `init` di con `set` e rendendo una proprietà completamente scrivibile non è mai una modifica di rilievo di origine in una proprietà non virtuale. Espande semplicemente il set di scenari in cui è possibile scrivere la proprietà. L'unico comportamento in questione è il fatto che rimanga una modifica di rilievo binaria.

Se si vuole apportare la modifica di `init` a `set` una modifica di origine e di compatibilità binaria, l'utente forza la decisione sulla modreq rispetto alla decisione degli attributi riportata di seguito perché esclude modreqs come Soulution. Se invece si tratta di un aspetto non interessante, questa operazione renderà la decisione di confronto tra l'modreq e l'attributo meno incisiva.

**Risoluzione** dei problemi Questo scenario non è considerato interessante da LDM.

### <a name="modreqs-vs-attributes"></a>Confronto tra Modreqs e attributi
La strategia Emit per le `init` funzioni di accesso alle proprietà deve scegliere se usare gli attributi o modreqs durante la creazione durante i metadati. Si tratta di compromessi diversi che devono essere presi in considerazione.

Annotando una funzione di accesso set di proprietà con una dichiarazione modreq significa che i compilatori conformi CLI ignoreranno la funzione di accesso a meno che non riconosca modreq Ciò significa che solo i compilatori a conoscenza di `init` leggeranno il membro. I compilatori che `init` non conoscono ignoreranno la `set` funzione di accesso e pertanto non considereranno accidentalmente la proprietà come di lettura/scrittura. 

Lo svantaggio di modreq è `init` costituito da una parte della firma binaria della `set` funzione di accesso. L'aggiunta o `init` la rimozione comporterà l'eliminazione di compatbility binari dell'applicazione.

L'uso di attributi per annotare la `set` funzione di accesso significa che solo i compilatori che comprendono l'attributo sapranno limitare l'accesso. Un compilatore ignaro di lo vedrà `init` come una semplice proprietà di lettura/scrittura e consentirà l'accesso.

Questa decisione potrebbe essere la scelta tra una maggiore sicurezza a scapito della compatibilità binaria. L'approfondimento della protezione aggiuntiva non è esattamente quello che sembra. Non verrà protetta dalle seguenti circostanze:

1. Reflection sui `public` membri
1. Utilizzo di`dynamic` 
1. Compilatori che non riconoscono modreqs

Si deve anche considerare che, quando si completano le regole di verifica IL per .NET 5, `init` sarà una di queste regole. Ciò significa che l'imposizione aggiuntiva sarà ottenuta semplicemente verificando i compilatori che emettono IL verificabile.

Le lingue primarie per .NET (C#, F # e VB) verranno tutte aggiornate per riconoscere tali `init` funzioni di accesso. Di conseguenza, l'unico scenario realistico è quando un compilatore C# 9 emette `init` proprietà e vengono visualizzate da un set di strumenti precedente, ad esempio C# 8, VB 15 e così via. C# 8. Questo è il compromesso da considerare e da valutare la compatibilità binaria.

**Nota** Questa discussione si applica principalmente solo ai membri e non ai campi. Mentre `init` i campi sono stati rifiutati da LDM, sono comunque interessanti da considerare per la discussione di modreq rispetto all'attributo. La `init` funzionalità per i campi è un rilassamento della restrizione esistente di `readonly` . Ciò significa che se i campi vengono creati come `readonly` + un attributo, non esiste alcun rischio che i compilatori meno recenti utilizzino il campo in quanto riconoscono già `readonly` . Quindi, l'uso di un modreq qui non aggiunge alcuna protezione aggiuntiva.

**Risoluzione** dei problemi La funzionalità userà un modreq per codificare il setter della proprietà `init` . I fattori interessanti sono stati (in nessun ordine particolare):

* Desiderio di scoraggiare i compilatori meno recenti dalla violazione della `init` semantica
* Desiderio di aggiungere o rimuovere `init` in una `virtual` dichiarazione o `interface` una modifica di rilievo di origine e binaria.

Dato che non esisteva un supporto significativo per la rimozione `init` di una modifica compatibile con binario, era possibile scegliere di usare modreq in modo semplice.

### <a name="init-vs-initonly"></a>init rispetto a initonly
Sono stati rilevati tre forme di sintassi che hanno preso in considerazione in modo significativo durante la riunione LDM:

```cs
// 1. Use init 
int Option1 { get; init; }
// 2. Use init set
int Option2 { get; init set; }
// 3. Use initonly
int Option3 { get; initonly; }
```

**Risoluzione** dei problemi Non c'era una sintassi estremamente favorita in LDM.

Un punto che ha avuto un notevole interesse è stato il modo in cui la scelta della sintassi avrà un impatto sulla possibilità di eseguire i `init` membri come funzionalità generale in futuro.
Se si sceglie l'opzione 1, sarebbe difficile definire una proprietà con un `init` `get` metodo di stile in futuro. Infine, è stato deciso che se avessimo deciso di proseguire con `init` i membri generali in futuro, avremmo potuto `init` essere un modificatore nell'elenco delle funzioni di accesso alle proprietà, così come un breve termine per `init set` . Essenzialmente le due dichiarazioni seguenti sarebbero identiche.

```cs
int Property1 { get; init; }
int Property1 { get; init set; }
```

È stata presa la decisione di procedere con `init` una funzione di accesso autonoma nell'elenco delle funzioni di accesso alle proprietà.

### <a name="warn-on-failed-init"></a>Avvisa in caso di inizializzazione non riuscita
Si consideri lo scenario seguente. Un tipo dichiara un `init` solo membro che non è impostato nel costruttore. Se il codice che costruisce l'oggetto riceve un avviso se non è stato possibile inizializzare il valore?

A questo punto è chiaro che il campo non verrà mai impostato e pertanto presenta numerose analogie con l'avviso che non riesce a inizializzare i `private` dati. Quindi, un avviso potrebbe avere un valore apparentemente simile a questo?

Questo avviso tuttavia presenta svantaggi significativi:
1. Complica la storia di compatibilità della modifica `readonly` a `init` . 
1. Per indicare i membri che devono essere inizializzati dal chiamante, è necessario portare metadati aggiuntivi.

Inoltre, se si ritiene che sia presente un valore nello scenario generale di forzare l'avviso o l'errore degli autori di oggetti in base a campi specifici, è probabile che questa funzionalità abbia un aspetto significativo. Non esiste alcun motivo per cui deve essere limitato solo ai `init` membri.

**Risoluzione** dei problemi Non verrà visualizzato alcun avviso relativo all'utilizzo di `init` campi e proprietà.

LDM desidera una discussione più ampia sull'idea dei campi e delle proprietà obbligatori. Questo potrebbe causare la restituzione e la rivalutazione della posizione dei `init` membri e della convalida.

## <a name="allow-init-as-a-field-modifier"></a>Consenti init come modificatore di campo
Allo stesso modo può fungere `init` da funzione di accesso alla proprietà, può anche fungere da designazione sui campi per fornire comportamenti simili come `init` Proprietà.
Ciò consente di assegnare il campo prima che la costruzione sia stata completata da tipo, tipi derivati o inizializzatori di oggetto.

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

Nei metadati questi campi verrebbero contrassegnati nello stesso modo dei campi, `readonly` ma con un attributo o modreq aggiuntivo per indicare che si tratta di `init` campi di stile. 

**Risoluzione** dei problemi LDM accetta questa proposta è un suono, ma in generale lo scenario si è risentito disgiunto dalle proprietà. La decisione consisteva nel procedere solo con le `init` proprietà per il momento. Si tratta di un livello di flessibilità adatto perché una `init` proprietà può mutare un `readonly` campo nel tipo dichiarante della proprietà. Questo verrà ripreso in considerazione se è presente un feedback significativo per i clienti che giustifica lo scenario.

### <a name="allow-init-as-a-type-modifier"></a>Consenti init come modificatore di tipo
Allo stesso modo, il `readonly` modificatore può essere applicato a un `struct` per dichiarare automaticamente tutti i campi come `readonly` , l' `init` unico modificatore può essere dichiarato in un `struct` o `class` per contrassegnare automaticamente tutti i campi come `init` .
Ciò significa che le due dichiarazioni di tipo seguenti sono equivalenti:

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

**Risoluzione** dei problemi Questa funzionalità è troppo *carina* ed è in conflitto con la `readonly struct` funzionalità su cui è basata. La `readonly struct` funzionalità è semplice in quanto si applica `readonly` a tutti i membri: campi, metodi e così via. La `init struct` funzionalità si applica solo alle proprietà. Questa operazione sta effettivamente creando confusione per gli utenti. 

Dato che `init` è valido solo per determinati aspetti di un tipo, si è rifiutato il concetto di come modificatore di tipo.

## <a name="considerations"></a>Considerazioni

### <a name="compatibility"></a>Compatibilità
La `init` funzionalità è progettata per essere compatibile con le `get` proprietà esistenti. In particolare, è concepita come una modifica completamente additiva per una proprietà che è `get` solo oggi ma che desidera una semantica di creazione di oggetti Flexbile.

Si consideri ad esempio il tipo seguente:

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

`init`L'aggiunta a queste proprietà è una modifica senza interruzioni:

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
Quando .NET Core decide di implementare nuovamente la verifica il, le regole dovranno essere modificate per tenere conto dei `init` membri. Questa operazione deve essere inclusa nelle modifiche regolate per le voci ACE non in mutazione ai `readonly` dati.

Le regole di verifica il devono essere suddivise in due parti: 

1. Consentire `init` ai membri di impostare un `readonly` campo.
1. Determinazione del momento in cui un `init` membro può essere chiamato legalmente.

Il primo è una semplice regolazione delle regole esistenti. Il verificatore di IL può essere insegnato a riconoscere `init` i membri e da qui è sufficiente prendere `readonly` in considerazione un campo da impostare `this` in un membro di questo tipo.

La seconda regola è più complessa. Nel caso semplice degli inizializzatori di oggetto la regola è diretta. È consigliabile chiamare `init` i membri quando il risultato di un' `new` espressione è ancora nello stack. Fino a quando il valore non è stato archiviato in un campo o elemento di matrice locale o passato come argomento a un altro metodo, sarà ancora possibile chiamare `init` i membri. In questo modo si garantisce che, una volta che il risultato dell' `new` espressione è stato pubblicato in un identificatore denominato (diverso da `this` ), non sarà più consentito chiamare `init` i membri. 

Il caso più complicato consiste tuttavia nel combinare `init` membri, inizializzatori di oggetto e `await` . In questo modo, l'oggetto appena creato verrà temporaneamente sottoposto a sollevamento in una macchina a Stati e quindi inserito in un campo.

```cs
var student = new Student() 
{
    Name = await SomeMethod()
};
```

Qui il risultato di `new Student()` sarà hoised in una macchina a stati come un campo prima del set di `Name` si verifica. Il compilatore dovrà contrassegnare tali campi in modo che il verificatore il riconosca che non sono accessibili dall'utente e pertanto non viola la semantica desiderata di `init` .

### <a name="init-members"></a>membri init
Il `init` modificatore può essere esteso per essere applicato a tutti i membri di istanza. In questo modo si generalizza il concetto di `init` durante la costruzione dell'oggetto e i tipi consentono di dichiarare metodi helper che potrebbero partecipare nel processo di costruzione per inizializzare `init` campi e proprietà.

Tali membri avranno tutti i restricions di una `init` funzione di accesso in questa progettazione. La necessità è comunque discutibile e questo può essere aggiunto in modo sicuro in una versione futura del linguaggio in modo compatibile.

### <a name="generate-three-accessors"></a>Genera tre funzioni di accesso
Una delle possibili implementazioni delle `init` proprietà è quella di eseguire `init` completamente la separazione da `set` . Ciò significa che una proprietà può avere potenzialmente tre funzioni di accesso diverse: `get` , `set` e `init` .

Questo offre il vantaggio potenziale di consentire l'uso di modreq per applicare la correttezza mantenendo la compatibilità binaria. L'implementazione sarà approssimativamente la seguente:

1. Una `init` funzione di accesso viene sempre emessa se è presente un oggetto `set` . Quando non è definito dallo sviluppatore, è semplicemente un riferimento a `set` . 
1. Il set di una proprietà in un inizializzatore di oggetto utilizzerà sempre `init` se presente, ma eseguirà il fallback a `set` se manca.

Ciò significa che uno sviluppatore può sempre eliminare in modo sicuro `init` da una proprietà. 

Lo svantaggio di questa progettazione è che è utile solo se `init` viene **sempre** emesso quando esiste una `set` . Il linguaggio non può `init` essere in grado di stabilire se è stato eliminato in passato, deve presupporre che sia stato e pertanto `init` deve essere sempre emesso. Questo potrebbe causare un'espansione significativa dei metadati e non vale la pena per il costo della compatibilità.
