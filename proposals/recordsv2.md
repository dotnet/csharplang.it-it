---
ms.openlocfilehash: 5636157ba54e93587847d6f2f7aac2dc675f3112
ms.sourcegitcommit: af27912886f22cda9b98b7769447d85cd9732736
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "79485000"
---

# <a name="records-v2"></a>Record V2

In passato abbiamo pensato ai record come una funzionalità per consentire l'utilizzo dei dati.

"Lavorare con i dati" è un grande gruppo con diversi facet, quindi può essere interessante esaminare ogni isolamento. Iniziamo esaminando un esempio di record oggi e alcuni dei relativi svantaggi.

Ad esempio, un record semplice verrebbe definito oggi come segue

```C#
public class UserInfo
{
    public string Username { get; set; }
    public string Email { get; set; }
    public bool IsAdmin  { get; set; } = false;
}
```

e l'utilizzo verrebbe letto

```C#
void M()
{
    var userInfo = new UserInfo() 
    {
        Username = "andy",
        Email = "angocke@microsoft.com",
        IsAdmin = true
    };
}
```

Questo codice presenta vantaggi significativi:

1. La definizione è resiliente della versione, le proprietà possono essere aggiunte o spostate facilmente
2. Le proprietà possono essere impostate in qualsiasi ordine e i nomi nell'inizializzazione corrispondono sempre alle funzioni di accesso
3. Le proprietà con valori predefiniti possono semplicemente essere ignorate

Il primo difetto è che ora le proprietà devono essere modificabili.

# <a name="mutability"></a>Modificabilità

C# Per fornire un modo per impostare un membro `readonly` negli inizializzatori di oggetto.
Poiché alcuni tipi potrebbero non essere stati progettati tenendo conto di questa inizializzazione, è anche possibile acconsentire esplicitamente.

La soluzione proposta è un nuovo modificatore, `initonly`, che può essere applicato a proprietà e campi:

```C#
public class UserInfo
{
    public initonly string Username { get; }
    public initonly string Email { get; }
    public initonly bool IsAdmin { get; } = false;
}
```

Il codegen è sorprendentemente semplice: è sufficiente impostare il campo ReadOnly.
In particolare, le proprietà ridotte avranno un aspetto simile al seguente:

```C#
public class UserInfo
{
    private readonly string <Backing>_username;
    public string get_Username() => <Backing>_username;
    [return: modreq(initonly)]
    public void set_Username(string value) { <Backing>_username = value; }
    ...
}
```

CLR considera non verificabili i campi di sola lettura, ma non quelli non sicuri. Per supportare un verificatore più avanzato, viene proposta la regola seguente: un campo di sola lettura può essere modificato solo all'interno di `initonly` metodi o su un nuovo oggetto che si trova nello stack CLR e non è stato pubblicato tramite una chiamata al metodo o all'archivio.

Questo dovrebbe risolvere molti dei problemi di mutabilità nell'esempio `UserInfo`, senza richiedere strategie di generazione complesse o fragili. Tuttavia, l'immutabilità presenta un nuovo problema: la semplice creazione di un oggetto con modifiche.

# <a name="with-ing"></a>Con-Ing

Quando si esegue la programmazione con l'immutabilità, le modifiche apportate a un oggetto vengono eseguite costruendo una copia con le modifiche anziché apportare le modifiche direttamente nell'oggetto. Sfortunatamente, non esiste un modo pratico per eseguire questa operazione C#in, nemmeno con i record in stile corrente. In precedenza è stato proposto che venisse fornito un tipo di metodo "with" generato automaticamente per i record che implementano tale funzionalità. Se si dispone di un meccanismo di questo tipo, è importante che funzioni con i membri del `initonly`. A tale scopo, viene proposto di aggiungere un'espressione `with`, analoga a un inizializzatore di oggetto. L'utilizzo di esempio sarà il seguente:

```C#
var userInfo = new UserInfo() 
{
    Username = "andy",
    Email = "angocke@microsoft.com",
    IsAdmin = true
};
var newUserName = userInfo with { Username = "angocke" };
```

L'oggetto `newUserName` risultante sarà una copia di `userInfo`, con `Username` impostato su "angocke".
Il valore di codegen sull'espressione `with` sarebbe anche simile all'inizializzatore di oggetto: viene costruito un nuovo oggetto, quindi il set di `Username` `initonly` verrebbe chiamato nel corpo del metodo.

Naturalmente, la differenza è che il nuovo oggetto costruito non è una semplice creazione di un nuovo oggetto, è un duplicato dell'oggetto originale. Per fornire questa funzionalità, è necessario che l'oggetto fornisca un "with Constructor" che fornisce un oggetto duplicato. Un costruttore di `With` di esempio avrà un aspetto simile al seguente:

```C#
class UserInfo
{
    ...
    [WithConstructor] // placeholder syntax, up for debate
    public UserInfo With()
    {
        return new UserInfo() { Username = this.Username, Email = this.Email, IsAdmin = this.IsAdmin };
    }
}
```

In particolare, l'espressione `with` imposta `initonly` membri, proprio come l'inizializzatore di oggetto, pertanto per supportare la verifica è necessario assicurarsi che l'oggetto non possa essere stato pubblicato prima che i membri del `initonly` siano impostati. Per applicare questa operazione, l'attributo `WithConstructor` (o la sintassi equivalente) imporrà una nuova regola per il metodo: tutte le istruzioni return devono contenere direttamente un'espressione di creazione di oggetti, possibilmente con un inizializzatore di oggetto.

Se il costruttore di `With` richiede la convalida, l'utente può introdurre un costruttore per eseguire tale convalida, ad esempio

```C#
class UserInfo
{
    ...
    private UserInfo(UserInfo original)
    {
        // validation code
    }
    [WithConstructor]
    public UserInfo With() => new UserInfo(this);
}
```

L'ultimo elemento di complessità associato a `With` è l'ereditarietà. Se il record è estensibile, sarà necessario fornire un nuovo `With` per la sottoclasse. Questa operazione può essere eseguita nel modo seguente:

```C#
class Base
{
    ...
    protected Base(Base original)
    {
        // validation
    }
    [WithConstructor]
    public virtual Base With() => new Base(this);
}
class Derived : Base
{
    ...
    protected Derived(Derived original)
    : base(original)
    {
        // validation
    }
    [WithConstructor]
    public override Derived With() => new Derived(this);
}
```

Si noti un'ulteriore complessità: per eseguire l'override del costruttore `With` con il tipo derivato, il linguaggio dovrà supportare anche tipi restituiti covarianti nelle sostituzioni.
Per [questa funzionalità è già presente una](https://github.com/dotnet/csharplang/blob/725763343ad44a9251b03814e6897d87fe553769/proposals/covariant-returns.md)proposta distinta.

**Svantaggi**

- L'esecuzione di tutte le istruzioni return in `WithConstructor`s contiene nuove espressioni di oggetto è restrittiva.
  Questo potrebbe essere mitigato dall'analisi dei flussi che garantisce che il nuovo oggetto non sfugga al metodo
- Per supportare la varianza nelle sostituzioni tramite trucchi del compilatore sono necessari metodi stub, che aumenteranno quadratico con la profondità dell'ereditarietà. La necessità di un metodo stub è causata da un requisito di runtime che sostituisce esattamente le firme. Se il requisito di runtime è stato allentato, i metodi dello stub non sarebbero necessari.
- Se si usano costruttori concatenati del modulo `Type(Type original)` si riserva effettivamente il costruttore per l'uso del modello. Poiché i costruttori hanno una semantica univoca e non possono essere rinominati, questa operazione potrebbe essere limitata.


## <a name="wrapping-it-all-up-records"></a>Wrapping: record

Le funzionalità sopra indicate consentono uno stile di programmazione molto difficile in precedenza. Tuttavia, anche con le nuove funzionalità, può essere abbastanza dettagliata e soggetta a errori per annotare tutto. Ci sono anche alcuni elementi, ad esempio Equals e GetHashCode, che possono essere già scritti oggi, ma solo laboriosi.
Inoltre, un difetto significativo nell'implementazione dell'uguaglianza rispetto a queste nuove primitive è che l'uguaglianza strutturale è un elemento che deve essere modificato con il tipo di dati man mano che vengono aggiunti nuovi dati, ma durante la gestione manuale è probabile che queste operazioni possano essere sincronizzate.

Pertanto, viene proposto che supporti C# la nuova sintassi per i record, non per fornire nuove funzionalità, ma per impostare i valori predefiniti e generare codice progettato per l'utilizzo nei record. La sintassi di esempio avrà un aspetto simile al seguente

```C#
data class UserInfo
{
    public string Username { get; }
    public string Email { get; }
    public bool IsAdmin { get; } = false;
}
```

Il codice generato per questa classe considera tutti i campi pubblici e le proprietà automatiche come membri strutturali del record. I membri del record possono essere personalizzati usando un nuovo attributo `RecordMember(bool)` che può essere usato per includere o escludere i membri. I membri dei record vengono `initonly` per impostazione predefinita e l'uguaglianza verrebbe generata automaticamente per la classe in base ai membri del record. In qualsiasi momento, il comportamento di questi membri potrebbe essere personalizzato semplicemente dichiarando tali membri nell'origine. L'implementazione scritta dall'utente sostituisce l'implementazione predefinita in tutti i modelli di utilizzo.

Si noti che l'uguaglianza in caso di ereditarietà è complessa, ma sembra che sia stata risolta adeguatamente nella [proposta di altri record](records.md).

## <a name="primary-constructors"></a>Costruttori primari

Nella proposta di record precedente è stata inclusa anche una nuova sintassi per un elenco di parametri nel tipo stesso, ad esempio

```C#
class Point(int X, int Y);
```

Nella nuova progettazione, l'elenco di parametri è una funzionalità ortogonale C# , che può essere integrata in modo pulito con i record. Se un costruttore primario è incluso in un record, avrà nuove impostazioni predefinite, proprio come i campi pubblici e le proprietà automatiche: i parametri nel costruttore primario verrebbero usati per generare proprietà del membro record pubblico con lo stesso nome. Inoltre, è ora possibile usare il costruttore primario per generare automaticamente un oggetto Deconstructor.

Ad esempio, il record seguente con un costruttore primario

```C#
data class Point(int X, int Y);
```

equivale a

```C#
data class Point
{
    public int X { get; }
    public int Y { get; }

    public Point(int x, int y)
    {
        X = x;
        Y = y;
    }

    public void Deconstruct(out int X, out int Y)
    {
        X = this.X;
        Y = this.Y;
    }
}
```

e la generazione definitiva del precedente sarebbe

```C#
class Point
{
    public initonly int X { get; }
    public initonly int Y { get; }

    public Point(int x, int y)
    {
        X = x;
        Y = y;
    }

    protected Point(Point other)
    : this(other.X, other.Y)
    { }

    [WithConstructor]
    public virtual Point With() => new Point(this);

    public void Deconstruct(out int X, out int Y)
    {
        X = this.X;
        Y = this.Y;
    }

    // Generated equality
}
```

Si noti che sono state prese in considerazione un'altra informazione per una classe di dati con un costruttore primario: anziché impostare i campi primari all'interno del costruttore protetto generato, viene delegato al costruttore primario. Se la classe Point aveva un altro membro record non primario, ad esempio

```C#
data class Point(int X, int Y)
{
    public int Z { get; }
}
```

quindi, il costruttore protetto generato verrà modificato come segue:

```C#
class Point
{
    // ...
    protected Point(Point other)
    : this(other.X, other.Y)
    {
        Z = other.Z;
    }
    // ...
}
```

In particolare, questo non risponde alle operazioni da eseguire sull'ereditarietà dei record con i costruttori primari. Ad esempio,

```C#
data class A(int X, int Y);
data class B(int X, int Y, int Z) : A;
```

Anziché risolverli in modo arbitrario, un approccio più esplicito potrebbe richiedere l'inclusione di un elenco di parametri con l'elenco di base, ad esempio

```C#
data class A(int X, int Y);
data class B(int X, int Y, int Z) : A(X, Y);
```

L'elenco di parametri nell'elenco base verrebbe quindi applicato a una chiamata `base` nel costruttore primario generato:

```C#
class B
{
    // ..
    public B(int x, int y, int z)
    : base(x, y)
    // ..
}
```

Per quanto riguarda il modo in cui un costruttore primario può significare all'esterno di un record, questo è ancora aperto per una proposta ulteriore.
