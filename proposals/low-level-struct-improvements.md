---
ms.openlocfilehash: a082598353ede157e6d5b3d219a04a85cbb292bf
ms.sourcegitcommit: 8cf85e8021b081f3bb2c71dede5e2aee9923bfa4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96596369"
---
<a name="low-level-struct-improvements"></a>Miglioramenti allo struct di basso livello
=====

## <a name="summary"></a>Riepilogo
Questa proposta è un'aggregazione di diverse proposte per `struct` migliorare le prestazioni. L'obiettivo è una progettazione che prende in considerazione le diverse proposte per creare un singolo set di funzionalità di base per i `struct` miglioramenti.

## <a name="motivation"></a>Motivazione
Nelle ultime versioni di C# sono state aggiunte diverse funzionalità per le prestazioni di basso livello al linguaggio: `ref` restituisce, `ref struct` , puntatori a funzione e così via... Questi sviluppatori .NET hanno consentito di creare codice a elevate prestazioni, continuando a sfruttare le regole del linguaggio C# per la sicurezza del tipo e della memoria.
È stata inoltre consentita la creazione di tipi di prestazioni fondamentali nelle librerie .NET, ad esempio `Span<T>` .

Dato che queste funzionalità hanno acquisito la trazione negli sviluppatori dell'ecosistema .NET, sia interne che esterne, ci hanno fornito informazioni sui punti di attrito rimanenti nell'ecosistema. Posizioni in cui è ancora necessario eliminare `unsafe` il codice per ottenere il proprio lavoro o richiedere il runtime a tipi speciali di casi come `Span<T>` . 

Questa proposta mira a risolvere molti di questi problemi grazie alla creazione di una funzionalità di basso livello esistente. In particolare, mira a:

- Consente `ref struct` ai tipi di dichiarare i `ref` campi.
- Consentire al runtime di definire completamente `Span<T>` usando il sistema di tipi C# e rimuovere il tipo di caso speciale come `ByReference<T>`
- Consente `struct` ai tipi di tornare `ref` ai campi.
- Consenti la dichiarazione di `fixed` buffer sicuri per i tipi gestiti e non gestiti in `struct`

## <a name="detailed-design"></a>Progettazione dettagliata 
Le regole per la `ref struct` sicurezza sono definite nel [documento span-Safety](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.2/span-safety.md). In questo documento vengono descritte le modifiche necessarie a questo documento in seguito a questa proposta. Una volta accettata come funzionalità approvata, queste modifiche verranno incorporate nel documento.

### <a name="provide-ref-fields"></a>Specificare i campi di riferimento
Il linguaggio consentirà agli sviluppatori di dichiarare i `ref` campi all'interno di un `ref struct` . Questa operazione può essere utile ad esempio quando si incapsulano istanze modificabili di grandi dimensioni `struct` o si definiscono tipi a prestazioni elevate come `Span<T>` nelle librerie oltre al runtime.

Attualmente i `ref` campi vengono eseguiti nel runtime usando il `ByReference<T>` tipo che il runtime considera effettivo come un `ref` campo. Ciò significa tuttavia che solo il repository di runtime può sfruttare appieno il `ref` comportamento del campo come e che tutti gli utilizzi di esso richiedono la verifica manuale della sicurezza. Parte della [motivazione di questo lavoro](https://github.com/dotnet/runtime/issues/32060) consiste nel rimuovere `ByReference<T>` e usare `ref` i campi appropriati in tutte le basi di codice. La parte complessa per consentire le `ref` dichiarazioni dei campi è la definizione di regole che `Span<T>` possono essere definite utilizzando `ref` campi senza interruzioni di compatibilità con il codice esistente. 

Prima di immergersi nei problemi, è opportuno notare che i `ref` campi richiedono solo un numero ridotto di modifiche mirate alle regole di sicurezza dell'intervallo esistenti. In alcuni casi non è nemmeno possibile supportare nuove funzionalità, ma razionalizzare l' `Span<T>` utilizzo dei dati esistenti `ref` . Questa sezione della proposta è piuttosto complessa perché mi sembra importante comunicare il "perché" di queste modifiche nel maggior dettaglio possibile e fornire esempi di supporto. In questo modo si garantisce che le modifiche siano valide, oltre a offrire agli sviluppatori futuri una migliore comprensione delle scelte effettuate in questo articolo.

Per comprendere i problemi, è opportuno prendere in considerazione il modo in cui `Span<T>` verranno esaminati i campi una volta `ref` supportati.

```cs
// This is the eventual definition of Span<T> once we add ref fields into the
// language
readonly ref struct Span<T>
{
    ref readonly T _field;
    int _length;

    // This constructor does not exist today however will be added as a
    // part of changing Span<T> to have ref fields. It is a convenient, and
    // safe, way to create a length one span over a stack value that today 
    // requires unsafe code.
    public Span(ref T value)
    {
        ref _field = ref value;
        _length = 1;
    }
}
```

Il costruttore definito qui presenta un problema perché i valori restituiti devono necessariamente avere durate limitate per molti input. Si consideri che se un parametro locale viene passato da `ref` a questo costruttore, l'oggetto restituito `Span<T>` deve avere un ambito *sicuro per l'escape* dell'ambito della dichiarazione locale.

```cs
Span<int> CreatingAndReturningSpan()
{
    int i = 42;

    // This must be an error in the new design because it stores stack 
    // state in the Span. 
    return new Span<int>(ref i);

    // This must be legal in the new design because it is legal today (it 
    // cannot store stack state)
    return new Span<int>(new int[] { });
}
```

Allo stesso tempo è lecito avere attualmente metodi che accettano un `ref` parametro e restituiscono un oggetto `Span<T>` .  Questi metodi hanno una grande somiglianza con il costruttore appena aggiunto `Span<T>` , ovvero accettano `ref` , restituiscono `Span<T>` . Tuttavia, la durata del valore restituito di questi metodi non viene mai limitata dagli input.
Le regole di sicurezza dell'intervallo esistenti considerano tali valori come sempre *sicuri* all'esterno del metodo contenitore.

```cs
class ExistingScenarios
{
    Span<T> CreateSpan<T>(ref T p)
    {
        // The implementation of this method is irrelevant. From the point of
        // the consumer the returned value is always safe to return.
        ... 
    }

    Span<T> Examples<T>(ref T p, T[] array)
    {
        // Legal today
        return CreateSpan(ref p); 

        // Legal today, must remain legal
        T local = default;
        return CreateSpan(ref local);

        // Legal for any possible value that could be used as an argument
        return CreateSpan(...);
    }
}
```

Il motivo per cui tutti gli esempi precedenti sono validi è il fatto che nella progettazione esistente non esiste alcun modo per restituire `Span<T>` un riferimento allo stato di input della chiamata al metodo. Ciò è dovuto al fatto che le regole span Safety dipendono in modo esplicito da `Span<T>` non avere un costruttore che accetta un `ref` parametro e lo archivia come campo. 

```cs
class ExistingAssumptions
{
    Span<T> CreateSpan<T>(ref T p)
    {
        // !!! Cannot happen today !!!
        // The existing span safety rules specifically call out that this method
        // cannot exist hence they can assume all returns from CreateSpan are
        // safe to return.
        return new Span<T>(ref p);
    }
}
```

Le regole definite per i `ref` campi devono garantire `Span<T>` che il costruttore limiti correttamente l'ambito di *sicurezza da Escape* degli oggetti costruiti nei casi in cui acquisisce `ref` lo stato. Allo stesso tempo, è necessario assicurarsi di non interrompere le regole di utilizzo esistenti per metodi quali `CreateSpan<T>` . 

```cs
class GoalOfRefFields
{
    Span<T> CreateSpan<T>(ref T p)
    {
        // ERROR: the existing consumption rules for CreateSpan believe this 
        // can never happen hence we must continue to enforce that it cannot
        return new Span<T>(ref p);

        // Okay: this is legal today
        return new Span<int>(new int[] { });
    }

    Span<int> ConsumptionCompatibility()
    {
        // Okay: this is legal today and must remain legal.
        int local = 42;
        return CreateSpan(ref local);

        // Okay: the arguments don't actually matter here. Literally any value 
        // could be passed to this method and the return of it would still be 
        // *safe-to-escape* outside the enclosing method. 
        return CreateSpan(...);
    }
}
```

Questa tensione tra consentire a costruttori come `Span<T>(ref T field)` e garantire la compatibilità con `ref struct` metodi di restituzione come `CreateSpan<T>` è un punto pivot chiave nella progettazione dei `ref` campi.

A tale scopo, verranno modificate le regole di escape per una chiamata al costruttore, che oggi corrisponde alla chiamata al metodo, in un oggetto `ref struct` che contiene **direttamente** un `ref` campo come indicato di seguito:
- Se il costruttore contiene `ref` parametri, `out` o `in` e gli argomenti non fanno tutti riferimento all'heap, il carattere di *escape sicuro* del risultato sarà l'ambito corrente
- Altrimenti, se il costruttore contiene `ref struct` parametri, il livello di *sicurezza di escape* del restituito sarà l'ambito corrente
- Altrimenti il *safe-to-escape* sarà l'esterno dell'ambito del metodo

Esaminiamo queste regole nel contesto degli esempi per comprendere meglio il loro effetto.

```cs
ref struct RS
{
    ref int _field;

    public RS(int[] array, int index)
    {
        ref _field = ref array[index];
    }

    public RS(ref int i)
    {
        ref _field = ref i;
    }

    static RS CreateRS(ref int i)
    {
        // The implementation of this method is irrelevant to the safety rule
        // examples below. The returned value is always *safe-to-escape* outside
        // the enclosing method scope
    }

    static RS RuleExamples(ref int i, int[] array)
    {
        var rs1 = new RS(ref i);

        // ERROR by bullet 1: the safe-to-escape scope of 'rs1' is the current
        // scope.
        return rs1; 

        var rs2 = new RS(array, 0);

        // Okay by bullet 2: the safe-to-escape scope of 'rs2' is outside the 
        // method scope.
        return rs2; 

        int local = 42;

        // ERROR by bullet 1: the safe-to-escape scope is the current scope
        return new RS(ref local);
        return new RS(ref i);

        // Okay because rules for method calls have not changed. This is legal
        // today hence it must be legal in the presence of ref fields.
        return CreateRS(ref local);
        return CreateRS(ref i);
    }
}
```

È importante notare che, ai fini della regola sopra qualsiasi utilizzo del concatenamento del costruttore tramite `this` , viene considerata una chiamata al costruttore. Il risultato della chiamata al costruttore concatenato viene considerato come un ritorno al costruttore originale, quindi le regole *sicure di escape* entrano in gioco. Questo è importante per evitare esempi non sicuri come i seguenti:

```cs
ref struct RS1
{
    ref int _field;
    public RS1(ref int p)
    {
        ref _field = ref p;
    }
}

ref struct RS2
{
    RS1 _field;
    public RS2(RS1 p)
    {
        // Okay
        _field = p;
    }

    public RS2(ref int i)
    {
        // ERROR: The *safe-to-escape* scope of the constructor here is the 
        // current method scope while the *safe-to-escape* scope of `this` is
        // outside the current method scope hence this assignment is illegal
        _field = new RS1(ref i);
    }

    public RS2(ref int i)  
        // ERROR: the *safe-to-escape* return of :this the current method scope
        // but the 'this' parameter has a *safe-to-escape* outside the current
        // method scope
        : this(new RS1(ref i))
    {

    }
}
```

La limitazione delle regole del costruttore a solo `ref struct` che contengono direttamente il `ref` campo è un altro importante problema di compatibilità. Si consideri che la maggior parte dei `ref struct` riferimenti attualmente contengono indirettamente `Span<T>` riferimenti. Ciò significa che, per estensione, i campi verranno inclusi indirettamente `ref` dopo l' `Span<T>` adozione dei `ref` campi. È quindi importante assicurarsi che le regole di *sicurezza per la restituzione* dei costruttori su questi tipi non cambino. Per questo motivo le restrizioni devono essere valide solo per i tipi che contengono direttamente un `ref` campo.

Esempio di come entra in gioco.

```cs
ref struct Container
{
    LargeStruct _largeStruct;
    Span<int> _span;

    public Container(in LargeStruct largeStruct, Span<int> span)
    {
        _largeStruct = largeStruct;
        _span = span;
    }
}
```

Analogamente all' `CreateSpan<T>` esempio prima della restituzione da *safe a escape* del `Container` costruttore non è influenzato dal `largeStruct` parametro. Se le nuove regole del costruttore sono state applicate a questo tipo, la compatibilità con il codice esistente verrebbe interrotta. Le regole esistenti sono sufficienti anche per i costruttori esistenti in modo da impedire la simulazione `ref` dei campi archiviando i campi nei `Span<T>` campi.

```cs
ref struct RS4
{
    Span<int> _span;

    public RS4(Span<int> span)
    {
        // Legal today and the rules for this constructor invocation 
        // remain unchanged
        _span = span;
    }

    public RS4(ref int i)
    {
        // ERROR. Bullet 1 of the new constructor rules gives this newly created
        // Span<T> a *safe-to-escape* of the current scope. The 'this' parameter
        // though has a *safe-to-escape* outside the current method. Hence this
        // is illegal by assignment rules because it's assigning a smaller scope
        // to a larger one.
        _span = new Span(ref i);
    }

    // Legal today, must remain legal for compat. If the new constructor rules 
    // applied to 'RS4' though this would be illegal. This is why the new 
    // constructor rules have a restriction to directly defining a ref field
    // 
    // Only ref struct which explicitly opt into ref fields would see a breaking
    // change here.
    static RS4 CreateContainer(ref int i) => new RS4(ref i);
}
```

Questa progettazione richiede anche che le regole per la durata dei campi vengano espanse, in quanto le regole attualmente non ne rappresentano semplicemente un conto. È importante sottolineare che l'espansione delle regole in questo caso non definisce un nuovo comportamento, ma piuttosto il comportamento che esisteva a lungo. Le regole di sicurezza `ref struct` per l'utilizzo del riconoscimento completo e l'account per la possibilità che conterranno `ref struct` lo stato `ref` e tale `ref` stato verranno esposte ai consumer. L'esempio più evidente è l'indicizzatore in `Span<T>` :

``` cs
readonly ref struct Span<T>
{
    public ref T this[int index] => ...; 
}
```

Questo espone direttamente lo `ref` stato all'interno `Span<T>` di e l'account per le regole di sicurezza da span per questo. Indica se è stato implementato come `ByReference<T>` o se `ref` i campi sono immateriali a tali regole. `ref`Per consentire i campi, tuttavia, è necessario definire le regole in modo che rientrino nelle regole di consumo esistenti per `ref struct` . In particolare, questo deve tenere conto del fatto che è *legale per* un `ref struct` per restituire lo `ref` stato `ref` del consumer. 

Per comprendere le modifiche proposte, è utile esaminare prima di tutto le regole esistenti per la chiamata al metodo in base *a Ref-safe-to-escape* e il modo in cui vengono considerate per uno `ref struct` stato di esposizione `ref` :

> Lvalue risultante da una chiamata al metodo di restituzione di Ref E1. M (E2,...) è *ref-safe-to-escape* il più piccolo degli ambiti seguenti:
> 1. Intero metodo contenitore
> 2. *Ref-safe-to-escape* di tutte le espressioni di argomento ref e out (escluso il ricevitore)
> 3. Per ogni parametro nel parametro del metodo, se esiste un'espressione corrispondente che è un lvalue, il relativo *ref-safe-to-escape*, in caso contrario l'ambito di inclusione più vicino
> 4. il carattere di *escape sicuro per* tutte le espressioni di argomento (incluso il ricevitore)

Il quarto elemento fornisce il punto di sicurezza critico intorno a uno `ref struct` `ref` stato di esposizione ai chiamanti. Quando lo `ref` stato archiviato in un oggetto `ref struct` si riferisce allo stack, l'ambito *di sicurezza da evitare* `ref struct` sarà al massimo l'ambito che definisce lo stato a cui si fa riferimento. Per questo motivo, la limitazione di *ref-safe-to-escape* delle chiamate di un oggetto `ref struct` all'ambito *safe-to-escape* del ricevitore garantisce che le durate siano corrette.

Si consideri, ad esempio, l'indicizzatore in `Span<T>` cui vengono restituiti i `ref` campi da `ref` oggi. Il quarto elemento è quello che fornisce la sicurezza:

```cs
ref int Examples()
{
    Span<int> s1 = stackalloc int[5];
    // ERROR: illegal because the *safe-to-escape* scope of `s1` is the current
    // method scope hence that limits the *ref-safe-to-escape" to the current
    // method scope as well.
    return ref s1[0];

    // SUCCESS: legal because the *safe-to-escape* scope of `s2` is outside
    // the current method scope hence the *ref-safe-to-escape* is as well
    Span<int> s2 = default;
    return ref s2[0];
}
```

Per tenere conto dei `ref` campi, le regole *ref-safe-to-escape* per i campi verranno modificate in base a quanto segue:

> Un lvalue che designa un riferimento a un campo, e. F, è *ref-safe-to-escape* (per riferimento) come indicato di seguito:
> - Se `F` è un `ref` campo e `e` è `this` , è *di tipo ref-safe-to-escape* dal metodo contenitore.
> - In caso contrario, se `F` è un campo, il `ref` relativo ambito *ref-safe-to-escape* è l'ambito *sicuro di escape* di `e` .
> - In caso contrario `e` , se è di un tipo riferimento, è *ref-safe-to-escape* dal metodo contenitore.
> - In caso contrario, il relativo *ref-safe-to-escape* viene tratto da *ref-safe-to-escape* di `e` .

Questo consente in modo esplicito la `ref` restituzione di campi come `ref` da un `ref struct` campo, ma non normali, che verrà trattato in un secondo momento. 

```cs
ref struct RS
{
    ref int _refField;
    int _field;

    // Okay: this falls into bullet one above. 
    public ref int Prop1 => ref _refField;

    // ERROR: This is bullet four above and the *ref-safe-to-escape* of `this`
    // in a `struct` is the current method scope.
    public ref int Prop2 => ref _field;

    public RS(int[] array)
    {
        ref _refField = ref array[0];
    }

    public RS(ref int i)
    {
        ref _refField = ref i;
    }

    public RS CreateRS() => ...;

    public ref int M1(RS rs)
    {
        ref int local1 = ref rs.Prop1;

        // Okay: this falls into bullet two above and the *safe-to-escape* of
        // `rs` is outside the current method scope. Hence the *ref-safe-to-escape*
        // of `local1` is outside the current method scope.
        return ref local;

        // Okay: this falls into bullet two above and the *safe-to-escape* of
        // `rs` is outside the current method scope. Hence the *ref-safe-to-escape*
        // of `local1` is outside the current method scope.
        //
        // In fact in this scenario you can guarantee that the value returned
        // from Prop1 must exist on the heap. 
        RS local2 = CreateRS();
        return ref local2.Prop1;

        // ERROR: the *safe-to-escape* of `local4` here is the current method 
        // scope by the revised constructor rules. This falls into bullet two 
        // above and hence based on that allowed scope.
        int local3 = 42;
        var local4 = new RS(ref local3);
        return ref local4.Prop1;

    }
}
```

Le regole per l'assegnazione devono anche essere modificate per tenere conto dei `ref` campi.
Questa progettazione consente solo l' `ref` assegnazione di un `ref` campo durante la costruzione di un oggetto o quando il valore è noto per fare riferimento all'heap. La costruzione di oggetti include nel costruttore del tipo dichiarante, all'interno delle `init` funzioni di accesso e nelle espressioni dell'inizializzatore di oggetto. `ref` `ref` In questo caso, l'oggetto assegnato al campo deve disporre di *ref-safe-to-escape* maggiore del destinatario del campo: 

- Costruttori: il valore deve essere *ref-safe-to-escape* all'esterno del costruttore
- `init` funzioni di accesso: il valore limitato ai valori noti per fare riferimento all'heap come funzioni di accesso non può avere `ref` parametri
- inizializzatori di oggetto: il valore può avere qualsiasi valore *ref-safe-to-escape* , in quanto verrà inserito nel calcolo della *sicurezza di escape* dell'oggetto costruito da regole esistenti.

Un `ref` campo può essere assegnato solo all'esterno di un costruttore quando il valore è noto per fare riferimento all'heap. Questa operazione è consentita perché è sicura nel percorso di assegnazione (soddisfa le regole di assegnazione del campo per garantire che il valore assegnato abbia una durata almeno pari al ricevitore), così come non richiede aggiornamenti per le regole di chiamata del metodo esistente. 

Questa progettazione non consente l'assegnazione di `ref` campo generale al di fuori della costruzione dell'oggetto a causa di limitazioni esistenti sulle durate. In particolare, costituisce una sfida per scenari come i seguenti:

```cs
ref struct SmallSpan
{
    public ref int _field;

    // Notice once again we're back at the same problem as the original 
    // CreateSpan method: a method returning a ref struct and taking a ref
    // parameter
    SmallSpan TrickyRefAssignment(ref int i)
    {
        // *safe-to-escape* is outside the current method by current rules.
        SmallSpan s = default;

        // The *ref-safe-to-escape* of 'i' is the same as the *safe-to-escape*
        // of 's' hence most assignment rules would allow it.
        ref s._field = ref i;

        // ERROR: this must be disallowed for the exact same reasons we can't 
        // return a Span<T> wrapping the parameter: the consumption rules
        // believe such state smuggling cannot exist
        return s;
    }

    SmallSpan SafeRefAssignment()
    {
        int[] array = new int[] { 42, 13 };
        SmallSpan s = default;

        // Okay: the value being assigned here is known to refer to the heap 
        // hence it is allowed by our rules above because it requires no changes
        // to existing method invocation rules (hence preserves compat)
        ref s._field = ref array[i];

        return s;
    }

    SmallSpan BadUsage()
    {
        // Legal today and must remain legal (and safe)
        int i = 0;
        return TrickyRefAssignment(ref i);
    }
}
```

Sono disponibili scelte di progettazione per consentire una `ref` riassegnazione più flessibile dei campi. Ad esempio, può essere consentito nei casi in cui è stato rilevato che il ricevitore aveva un ambito *di sicurezza da Escape* che non era al di fuori dell'ambito del metodo corrente. Inoltre, è possibile fornire la sintassi per rendere più semplici i valori rivolti verso il basso per dichiarare: essenzialmente i valori con ambiti di *sicurezza a escape* limitati al metodo corrente. Questo tipo di progettazione è illustrato di [seguito](https://github.com/dotnet/csharplang/discussions/1130).
Tuttavia, la complessità aggiuntiva di tali regole non sembra valere per i casi limitati abilitati. È possibile che vengano rilevati esempi interessanti.

Ciò significa tuttavia che `ref` i campi sono in gran parte pratici `ref readonly` . Le eccezioni principali sono gli inizializzatori di oggetto e quando il valore è noto per fare riferimento all'heap.

Un `ref` campo verrà emesso nei metadati usando la `ELEMENT_TYPE_BYREF` firma. Questa operazione non è diversa rispetto alla modalità di creazione di `ref` variabili locali o `ref` argomenti. Ad esempio `ref int _field` , verrà generato come `ELEMENT_TYPE_BYREF ELEMENT_TYPE_I4` . Questa operazione richiede l'aggiornamento di ECMA335 per consentire questa voce, ma dovrebbe essere piuttosto semplice.

Gli sviluppatori possono continuare a inizializzare un oggetto `ref struct` con un `ref` campo usando l' `default` espressione, nel qual caso tutti i campi dichiarati avranno `ref` il valore `null` . Qualsiasi tentativo di utilizzare tali campi comporterà la generazione di un' `NullReferenceException` eccezione.

```cs
struct S1 
{
    public ref int Value;
}

S1 local = default;
local.Value.ToString(); // throws NullReferenceException
```

Anche se il linguaggio C# prevede che un oggetto `ref` non può essere `null` valido a livello di runtime e ha una semantica ben definita. Gli sviluppatori che introducono `ref` campi nei propri tipi devono essere consapevoli di questa possibilità e dovrebbero essere **fortemente** sconsigliati a causa della perdita di questo dettaglio nell'utilizzo del codice. `ref`I campi devono invece essere convalidati come non null usando gli [Helper di runtime](https://github.com/dotnet/runtime/pull/40008) e generando quando un oggetto non inizializzato `struct` viene usato in modo errato.

```cs
struct S1 
{
    private ref int Value;

    public int GetValue()
    {
        if (System.Runtime.CompilerServices.Unsafe.IsNullRef(ref Value))
        {
            throw new InvalidOperationException(...);
        }

        return Value;
    }
}
```

Note varie:
- Un `ref` campo può essere dichiarato solo all'interno di un `ref struct` 
- `ref`Non è possibile dichiarare un campo`static`
- Un `ref` campo può essere `ref` assegnato solo nel costruttore del tipo dichiarante.
- Il processo di generazione dell'assembly di riferimento deve mantenere la presenza di un `ref` campo all'interno di un `ref struct` 
- Un oggetto `ref readonly struct` deve dichiarare i `ref` campi come `ref readonly`
- Le regole di protezione dell'intervallo per costruttori, campi e assegnazioni devono essere aggiornate come descritto in questo documento.
- Le regole di sicurezza dell'intervallo devono includere la definizione dei `ref` valori che "fanno riferimento all'heap". 

### <a name="provide-struct-this-escape-annotation"></a>Specificare l'annotazione escape per struct
Le regole per l'ambito di `this` in un `struct` limitano l'ambito *ref-safe-to-escape* al metodo corrente. Ciò significa che nessuno `this` dei due campi può restituire per riferimento al chiamante.

```cs
struct S
{
    int _field;
    // Error: this, and hence _field, can't return by ref
    public ref int Prop => ref _field;
}
```

Non ci sono problemi intrinseci con l' `struct` escape `this` per riferimento.
Al contrario, la giustificazione di questa regola è la presenza di un equilibrio tra l'usabilità di `struct` e `interfaces` . Se un oggetto può essere sottoposto a `struct` escape `this` per riferimento, ridurrebbe significativamente l'utilizzo dei `ref` ritorni nelle interfacce.

```cs
interface I1
{
    ref int Prop { get; }
}

struct S1 : I1
{
    int _field;
    public ref int Prop => _ref field;

    // When T is a struct type, like S1 this would end up returning a reference
    // to the parameter
    static ref int M<T>(T p) where T : I1 => ref p.Prop;
}
```

La giustificazione è ragionevole, ma introduce anche un attrito `struct` non necessario sui membri che non partecipano alle chiamate di interfaccia. 

Uno scenario di compatibilità principale da tenere presente quando si avvicinano le modifiche è il seguente:

```cs
struct S1
{
    ref int GetValue() => ...
}

class Example
{
    ref int M()
    {
        // Okay: this is always allowed no matter how `local` is initialized
        S1 local = default;
        return local.GetValue();
    }
}
```

Questa operazione funziona perché le regole di sicurezza per `ref` return attualmente non prendono in considerazione la durata di `this` (perché non può restituire uno `ref` stato interno). Ciò significa che la `ref` restituzione da un `struct` può restituire al di fuori dell'ambito del metodo contenitore tranne nei casi in cui sono presenti `ref` parametri o un oggetto `ref struct` che non è *sicuro da evitare l'escape* all'esterno dell'ambito del metodo contenitore. Quindi, la soluzione qui non è semplice quanto consentire la `ref` restituzione di campi nei metodi non di interfaccia.

Per rimuovere questo attrito, il linguaggio fornirà l'attributo `[ThisRefEscapes]` .
Quando questo attributo viene applicato a un metodo di istanza, alla proprietà dell'istanza o alla funzione di accesso dell'istanza di un oggetto `struct` o, `ref struct` il `this` parametro viene considerato *ref-safe-to-escape* all'esterno del metodo contenitore.

Questo consente una maggiore flessibilità nelle `struct` definizioni che possono iniziare a tornare `ref` ai rispettivi campi. Che consente tipi come `FrugalList<T>` :

```cs
struct FrugalList<T>
{
    private T _item0;
    private T _item1;
    private T _item2;

    public int Count = 3;

    public ref T this[int index]
    {
        [ThisRefEscapes]
        get
        {
            switch (index)
            {
                case 0: return ref _item1;
                case 1: return ref _item2;
                case 2: return ref _item3;
                default: throw null;
            }
        }
    }
}
```

Questo naturalmente, in base alle regole esistenti nella specifica span Safety, consente la restituzione di campi transitivi oltre ai campi diretti.

```cs
struct ListWithDefault<T>
{
    private FrugalList<T> _list;
    private T _default;

    public ref T this[int index]
    {
        [ThisRefEscapes]
        get
        {
            if (index >= _list.Count)
            {
                return ref _default;
            }

            return ref _list[index];
        }
    }
}
```

I membri che contengono l' `[ThisRefEscapes]` attributo non possono essere usati per implementare i membri di interfaccia. In questo modo si nasconde la natura della durata del membro nel `interface` sito di chiamata e potrebbero verificarsi calcoli di durata non corretti.

Per tenere conto di questa modifica, la sezione "Parameters" del documento span Safety verrà aggiornata in modo da includere quanto segue:

- Se il parametro è il `this` parametro di un `struct` tipo, è *ref-safe-to-escape* nell'ambito superiore del metodo contenitore a meno che il metodo non sia annotato con, `[ThisRefEscapes]` nel qual caso è *ref-safe-to-escape* all'esterno del metodo contenitore.

Note varie:
- Un membro contrassegnato come `[ThisRefEscapes]` non può implementare un `interface` metodo o essere `overrides`
- Un membro contrassegnato come `[ThisRefEscapes]` verrà generato con un oggetto `modreq` su tale attributo.
- L'oggetto `RefEscapesAttribute` verrà definito nello `System.Runtime.CompilerServices` spazio dei nomi.

### <a name="safe-fixed-size-buffers"></a>Buffer a dimensione fissa sicura
Il linguaggio ridurrà le restrizioni sulle matrici a dimensione fissa in modo che possano essere dichiarate in codice sicuro e il tipo di elemento possa essere gestito o non gestito. Questa operazione renderà i tipi come i seguenti:

```cs
internal struct CharBuffer
{
    internal fixed char Data[128];
}
```

Queste dichiarazioni, in modo analogo alle `unsafe` parti del contatore, definiranno una sequenza di `N` elementi nel tipo che lo contiene. È possibile accedere a questi membri con un indicizzatore e convertirli anche in `Span<T>` istanze di e `ReadOnlySpan<T>` .

Quando si esegue l'indicizzazione in un `fixed` buffer di tipo `T` `readonly` , è necessario prendere in considerazione lo stato del contenitore.  Se il contenitore è `readonly` quindi, l'indicizzatore restituisce `ref readonly T` else `ref T` . 

L'accesso a un `fixed` buffer senza un indicizzatore non ha un tipo naturale, tuttavia è convertibile in `Span<T>` tipi. Nel caso in cui il contenitore sia `readonly` il buffer è implicitamente convertibile in `ReadOnlySpan<T>` , altrimenti può eseguire la conversione implicita in `Span<T>` o `ReadOnlySpan<T>` (la `Span<T>` conversione è considerata *migliore*). 

L' `Span<T>` istanza risultante avrà una lunghezza uguale alla dimensione dichiarata nel `fixed` buffer. L'ambito *safe-to-escape* del valore restituito sarà uguale all'ambito *safe-to-escape* del contenitore.

Per ogni `fixed` dichiarazione in un tipo in cui il tipo di elemento è `T` il linguaggio genererà un `get` solo metodo indicizzatore corrispondente il cui tipo restituito è `ref T` . L'indicizzatore verrà annotato con l' `[ThisRefEscapes]` attributo, perché l'implementazione restituirà campi del tipo dichiarante. L'accessibilità del membro corrisponderà all'accessibilità nel `fixed` campo.

Ad esempio, la firma dell'indicizzatore per `CharBuffer.Data` sarà la seguente:

```cs
[ThisRefEscapes]
internal ref char <>DataIndexer(int index) => ...;
```

Se l'indice specificato non è compreso nei limiti dichiarati della `fixed` matrice `IndexOutOfRangeException` , verrà generata un'eccezione. Nel caso in cui venga fornito un valore costante, questo verrà sostituito da un riferimento diretto all'elemento appropriato. A meno che la costante non sia al di fuori dei limiti dichiarati, nel qual caso si verificherà un errore in fase di compilazione.

Verrà inoltre generata una funzione di accesso denominata per ogni `fixed` buffer che fornisce per valore `get` e `set` operazioni. Ciò significa che i `fixed` buffer saranno più simili alla semantica della matrice esistente con una funzione di `ref` accesso, nonché `get` `set` le operazioni ByVal e. Ciò significa che i compilatori avranno la stessa flessibilità durante la creazione di buffer che utilizzano il codice `fixed` , come avviene quando si utilizzano matrici. Questa operazione deve essere simile `await` a quella dei `fixed` buffer più facili da creare. 

Questo offre anche il vantaggio aggiuntivo che renderà `fixed` più semplice l'utilizzo dei buffer da altri linguaggi. Gli indicizzatori denominati sono una funzionalità esistente dalla versione 1,0 di .NET. Anche le lingue che non possono emettere direttamente un indicizzatore denominato possono in genere utilizzarle (C# è un esempio valido).

Ci sarà anche un `get` per valore e la `set` funzione di accesso generata per ogni 

L'archiviazione di backup per il buffer verrà generata usando l' `[InlineArray]` attributo. Si tratta di un meccanismo illustrato in [èusarlo 12320](https://github.com/dotnet/runtime/issues/12320) che consente di dichiarare in modo efficiente la sequenza di campi dello stesso tipo.

Questo particolare problema è ancora in discussione attiva e l'aspettativa è che l'implementazione di questa funzionalità seguirà tuttavia la discussione.

### <a name="provide-parameter-does-not-escape-annotations"></a>Il parametro provide non esegue l'escape delle annotazioni
Un'origine di attrito ripetuto nel codice di basso livello è l'ambito di escape predefinito per i parametri di tipo *safe-to-escape* all'esterno del corpo del metodo contenitore. Si tratta di un valore predefinito sensibile perché si allinea con i modelli di codifica di .NET nel suo complesso. Nel codice di basso livello è disponibile un utilizzo maggiore di `ref struct` e questo ambito predefinito può causare attrito con altre parti delle regole di protezione dell'intervallo.

Il punto di attrito principale si verifica a causa del vincolo seguente per le chiamate al metodo:

> Per una chiamata al metodo se è presente un argomento ref o out di un tipo struct di riferimento (incluso il ricevitore), con la sicurezza di escape E1, nessun argomento (incluso il ricevitore) può avere una sicurezza di escape più stretta di E1

Questa regola viene in genere eseguita con i metodi di istanza in `ref struct` cui almeno un parametro è anche un `ref struct` . Si tratta di un modello comune nel codice di basso livello in cui `ref struct` i tipi usano `Span<T>` in genere i parametri nei rispettivi metodi. Prendere in considerazione tutti gli oggetti di tipo Builder o writer usati `Span<T>` da per passare i buffer.

Questa regola esiste per impedire scenari come i seguenti:

```cs
ref struct RS
{
    Span<int> _field;
    void Set(Span<int> p)
    {
        _field = p;
    }

    static void DangerousCode(ref RS p)
    {
        Span<int> span = stackalloc int[] { 42 };

        // Error: if allowed this would let the method return a reference to 
        // the stack
        p.Set(span);
    }
}
```

Essenzialmente questa regola esiste perché il linguaggio deve presupporre che tutti gli input di un metodo sfuggono al rispettivo ambito massimo consentito. Nel caso precedente, il linguaggio deve presupporre che i parametri vengano ignorati nei campi del destinatario.

In pratica, sebbene esistano molti di questi metodi che non eseguono mai il escape del parametro.
Si tratta semplicemente di un valore utilizzato all'interno dell'implementazione di. 

```cs
ref struct JsonReader
{
    Span<char> _buffer;
    int _position;

    internal bool TextEquals(ReadOnySpan<char> text)
    {
        var current = _buffer.Slice(_position, text.Length);
        return current == text;
    }
}

class C
{
    static void M(ref JsonReader reader)
    {
        Span<char> span = stackalloc char[4];
        span[0] = 'd';
        span[1] = 'o';
        span[2] = 'g';

        // Error: The *safe-to-escape* of `span` is the current method scope 
        // while `reader` is outside the current method scope hence this fails
        // by the above rule.
        if (reader.TextEquals(span)
        {
            ...
        })
    }
}
```

Per ovviare a questo codice di basso livello, si ricorrerà a `unsafe` trucchi per far pensare al compilatore il ciclo di vita dei rispettivi `ref struct` . Questo consente di ridurre significativamente la proposta di valore di `ref struct` in quanto sono destinati a evitare di `unsafe` scrivere codice a prestazioni elevate.

L'altra posizione in cui l'ambito di escape predefinito del parametro causa un attrito è quando vengono riassegnati all'interno del corpo di un metodo. Ad esempio, se il corpo di un metodo decide di applicare in modo condizionale l'escape all'input usando valori allocati dello stack. Ancora una volta questa operazione si blocca.

```cs
void WriteData(ReadOnlySpan<char> data)
{
    if (data.Contains(':'))
    {
        Span<char> buffer = stackalloc char[256];
        Escape(data, buffer, out var length);

        // Error: Cannot assign `buffer` to `data` here as the *safe-to-escape*
        // scope of `buffer` is to the current method scope while `buffer` is
        // outside the current method scope
        data = buffer.Slice(0, length);
    }

    WriteDataCore(data);
}
```

Questo modello è piuttosto comune nel codice .NET e funziona correttamente quando `ref struct` non è necessario un oggetto. Una volta che gli utenti adottano `ref struct` anche se impone loro di modificare i modelli qui e spesso ricorrono a `unsafe` aggirare le limitazioni qui.

Per rimuovere questo attrito, il linguaggio fornirà l'attributo `[DoesNotEscape]` . Questo può essere applicato a parametri di qualsiasi tipo o membro di istanza definito in `ref struct` . Quando viene applicato ai parametri, l'ambito *di sicurezza di escape* e *ref-safe-to-escape* sarà l'ambito del metodo corrente. Quando applicato ai membri di istanza con `ref struct` la stessa limitazione, si applica al `this` parametro.

```cs
class C
{
    static Span<int> M1(Span<int> p1, [DoesNotEscape] Span<int> p2)
    {
        // Okay: the *safe-to-escape* here is still outside the enclosing scope
        // of the current method.
        return p1; 

        // ERROR: the [DoesNotEscape] attribute changes the *safe-to-escape* 
        // to be limited to the current method scope. Hence it cannot be 
        // returned
        return p2; 

        // ERROR: `local` has the same *safe-to-escape* as `p2` hence it cannot
        // be returned.
        Span<int> local = p2;
        return p2; 
    }
}
```

Per tenere conto di questa modifica, la sezione "Parameters" del documento span Safety verrà aggiornata in modo da includere il punto elenco seguente:

- Se il parametro è contrassegnato con è `[DoesNotEscape]` *safe-to-escape* e *ref-safe-to-escape* nell'ambito del metodo contenitore. 

È importante sottolineare che questa operazione bloccherà in modo naturale la possibilità che tali parametri vengano ignorati archiviando come campi. I ricevitori passati da `ref` , o `this` su `ref struct` , hanno un ambito *sicuro per l'escape* al di fuori del metodo corrente. Di conseguenza, l'assegnazione da un `[DoesNotEscape]` parametro a un campo su un valore di questo tipo ha esito negativo in base alle regole di assegnazione dei campi esistenti: l'ambito del ricevitore è maggiore del valore assegnato.

```cs
ref struct S
{
    Span<int> _field;

    void M1(Span<int> p1, [DoesNotEscape] Span<int> p2)
    {
        // Okay: the *safe-to-escape* here is still outside the enclosing scope
        // of the current method and hence the same as the receiver.
        _field = p1;

        // ERROR: the [DoesNotEscape] attribute changes the *safe-to-escape* 
        // to be limited to the current method scope. Hence it cannot be 
        // assigned to a receiver than has a *safe-to-escape* scope outside the 
        // current method.
        _field = p2;
    }
}
```

Dato che i parametri sono limitati in questo modo, verrà aggiornata anche la sezione "chiamata al metodo" per attenuare le regole. In tutti i casi in cui si sta considerando la durata di *riferimento-safe-to-* Escape o di tipo *safe-to-escape* degli argomenti, la specifica cambierà in modo da ignorare gli argomenti che si allineano ai parametri contrassegnati come `[DoesNotEscape]` . Poiché questi argomenti non possono uscire dalla durata, non è necessario prendere in considerazione la durata dei valori restituiti.

Ad esempio, l'ultima riga di calcolo *safe-to-escape* dei ritorni cambierà in 

> il carattere di escape sicuro per tutte le espressioni di argomento, incluso il ricevitore. **Questa operazione escluderà tutti gli argomenti che si allineano con i parametri contrassegnati come [DoesNotEscape]**

Note varie:
- L'oggetto  `DoesNotEscapeAttribute` verrà definito nello `System.Runtime.CompilerServices` spazio dei nomi.
- `DoesNotEscapeAttribute`Non può essere combinato con l' `[ThisRefEscapes]` attributo. in questo modo si ottiene un errore.
- `DoesNotEscapeAttribute`Verrà generato come`modreq`

## <a name="considerations"></a>Considerazioni

### <a name="keywords-vs-attributes"></a>Parole chiave e attributi
Questa struttura chiama per usare gli attributi per annotare le nuove regole di durata per `struct` i membri. Questa operazione potrebbe essere stata eseguita altrettanto facilmente con le parole chiave contestuali. Ad esempio: `scoped` e `escapes` potrebbero essere stati usati anziché `DoesNotEscape` e `ThisRefEscapes` .

Le parole chiave, anche quelle contestuali, hanno un peso molto più pesante nel linguaggio degli attributi. I casi d'uso di queste funzionalità risolvono, sebbene molto prezioso, influiscano su un numero ridotto di sviluppatori. Si tenga presente che solo una parte degli sviluppatori di livello superiore sta definendo `ref struct` le istanze e quindi considera che solo una parte di tali sviluppatori utilizzerà queste nuove funzionalità di durata.
Non sembra giustificare l'aggiunta di una nuova parola chiave contestuale al linguaggio.

Ciò significa che la correttezza del programma verrà definita in termini di attributi. Questo è un po' di un'area grigia per la lingua, ma un modello consolidato per il Runtime. 

## <a name="open-issues"></a>Problemi non risolti

### <a name="allow-fixed-buffer-locals"></a>Consenti variabili locali del buffer fisse
Questa progettazione consente i `fixed` buffer sicuri che possono supportare qualsiasi tipo. Una possibile estensione consente `fixed` di dichiarare tali buffer come variabili locali. Ciò consente di sostituire un numero di `stackalloc` operazioni esistenti con un `fixed` buffer. Espande anche il set di scenari in cui è possibile che le allocazioni dello stile dello stack siano `stackalloc` limitate ai tipi di elementi non gestiti, mentre `fixed` i buffer non lo sono. 

```cs
class FixedBufferLocals
{
    void Example()
    {
        Span<int> span = stakalloc int[42];
        int buffer[42];
    }
}
```

Questa operazione viene eseguita insieme, ma è necessario estendere la sintassi per le variabili locali. Non chiaro se questo è o non vale la pena di complicare ulteriormente. È possibile che si decida di no per il momento e riportarlo in un secondo momento, se è necessaria una sufficiente dimostrazione

Esempio di dove si può trarre vantaggio: https://github.com/dotnet/runtime/pull/34149

### <a name="allow-multi-dimensional-fixed-buffers"></a>Consenti buffer fissi multidimensionali
Se la progettazione per i `fixed` buffer viene estesa per includere matrici di stile multidimensionali, Consente essenzialmente le dichiarazioni come le seguenti:

```cs
struct Dimensions
{
    int array[42, 13];
}
```

## <a name="future-considerations"></a>Considerazioni

### <a name="allowing-attributes-on-locals"></a>Consentire attributi nelle variabili locali
Un altro punto di attrito per gli sviluppatori `ref struct` che usano è che le variabili locali possono risentirsi degli stessi problemi dei parametri rispetto alla durata della dichiarazione. Che può rendere difficile l'utilizzo di `ref struct` che vengono assegnati a più percorsi in cui almeno uno dei percorsi è un ambito di *sicurezza di escape* limitato. 

```cs
int length = ...;
Span<byte> span;
if (length > StackAllocLimit)
{
    span = new Span(new byte[length]);
}
else
{
    // Error: The *safe-to-escape* of `span` was decided to be outside the 
    // current method scope hence it can't be the target of a stackalloc
    span = stackalloc byte[length];
}
```

Per `Span<T>` gli sviluppatori che possono ovviare a questo problema, inizializzare il locale con un `stackalloc` di dimensioni pari a zero. In questo modo, l'ambito *safe-to-escape* diventa il metodo corrente e viene ottimizzato dal compilatore. Si tratta in effetti di una sintassi per la creazione di un oggetto `[DoesNotEscape]` locale.

```cs
int length = ...;
Span<byte> span = stackalloc byte[0];
if (length > StackAllocLimit)
{
    span = new Span(new byte[length]);
}
else
{
    // Okay
    span = stackalloc byte[length];
}
```

Questa operazione funziona solo per `Span<T>` , ma non esiste alcun meccanismo di utilizzo generico per `ref struct` i valori. Tuttavia `[DoesNotEscape]` , l'attributo fornisce esattamente la semantica desiderata qui. Se si decide in futuro di consentire agli attributi di applicare le variabili locali, si fornirebbe un sollievo immediato a questo scenario.

## <a name="related-information"></a>Informazioni correlate

### <a name="issues"></a>Problemi
Di seguito sono riportati i problemi correlati a questa proposta:

- https://github.com/dotnet/csharplang/issues/1130
- https://github.com/dotnet/csharplang/issues/1147
- https://github.com/dotnet/csharplang/issues/992
- https://github.com/dotnet/csharplang/issues/1314
- https://github.com/dotnet/csharplang/issues/2208
- https://github.com/dotnet/runtime/issues/32060

### <a name="proposals"></a>Proposte
Le seguenti proposte sono correlate a questa proposta:

- https://github.com/dotnet/csharplang/blob/725763343ad44a9251b03814e6897d87fe553769/proposals/fixed-sized-buffers.md

### <a name="existing-samples"></a>Esempi esistenti

[Utf8JsonReader](https://github.com/dotnet/runtime/blob/f1a7cb3fdd7ffc4ce7d996b7ac6867ffe2c953b9/src/libraries/System.Text.Json/src/System/Text/Json/Reader/Utf8JsonReader.cs#L523-L528)

Questo frammento di codice specifico richiede unsafe perché si verificano problemi con il passaggio di un oggetto `Span<T>` che può essere allocato nello stack a un metodo di istanza su un oggetto `ref struct` . Anche se questo parametro non viene acquisito, il linguaggio deve presupporre che sia e pertanto causa un attrito inutilmente.

[Utf8JsonWriter](https://github.com/dotnet/runtime/blob/f1a7cb3fdd7ffc4ce7d996b7ac6867ffe2c953b9/src/libraries/System.Text.Json/src/System/Text/Json/Writer/Utf8JsonWriter.WriteProperties.String.cs#L122-L127)

Questo frammento di codice desidera modificare un parametro eseguendo l'escape degli elementi dei dati. I dati di escape possono essere allocati nello stack per migliorare l'efficienza. Anche se il parametro non è preceduto da un carattere di escape, il compilatore lo assegna a un ambito di *escape sicuro* all'esterno del metodo contenitore perché è un parametro. Questo significa che, per poter usare l'allocazione dello stack, l'implementazione deve usare per `unsafe` assegnare di nuovo il parametro dopo l'escape dei dati.

### <a name="fun-samples"></a>Esempi divertenti

```cs
ref struct StackLinkedListNode<T>
{
    T _value;
    ref StackLinkedListNode<T> _next;

    public T Value => _value;

    public bool HasNext => !Unsafe.IsNullRef(ref _next);

    public ref StackLinkedListNode<T> Next 
    {
        get
        {
            if (!HasNext)
            {
                throw new InvalidOperationException("No next node");
            }

            return ref _next;
        }
    }

    public StackLinkedListNode(T value)
    {
        this = default;
        _value = value;
    }

    public StackLinkedListNode(T value, ref StackLinkedListNode<T> next)
    {
        _value = value;
        ref _next = ref next;
    }
}
```
