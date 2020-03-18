---
ms.openlocfilehash: 6088a5cd41926d828013f1b8e5736fd2b7939e44
ms.sourcegitcommit: da452002c3f472165a0e1fa7759f494cc703ae31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "79485042"
---
# <a name="compile-time-enforcement-of-safety-for-ref-like-types"></a>Imposizione del tempo di compilazione per la sicurezza per i tipi simili a Ref

## <a name="introduction"></a>Introduzione

Il motivo principale per le regole di sicurezza aggiuntive quando si gestiscono tipi come `Span<T>` e `ReadOnlySpan<T>` è che tali tipi devono essere limitati allo stack di esecuzione.
 
Esistono due motivi per cui `Span<T>` e tipi simili devono essere tipi di solo stack.

1. `Span<T>` è semanticamente uno struct contenente un riferimento e un intervallo-`(ref T data, int length)`. Indipendentemente dall'implementazione effettiva, le Scritture in tale struct non sarebbero atomiche. Il "strappo" simultaneo di tale struct potrebbe comportare la possibilità di `length` non corrispondere al `data`, causando accessi fuori intervallo e violazioni di indipendenza dai tipi, che in definitiva potrebbero causare il danneggiamento dell'heap GC in codice apparentemente "sicuro".
2. Alcune implementazioni di `Span<T>` contengono letteralmente un puntatore gestito in uno dei relativi campi. I puntatori gestiti non sono supportati come campi di oggetti heap e il codice che gestisce per inserire un puntatore gestito nell'heap GC in genere si arresta in modo anomalo in fase di JIT.

Tutti i problemi indicati in precedenza verrebbero attenuati se le istanze di `Span<T>` sono vincolate a esistere solo nello stack di esecuzione. 

Un ulteriore problema si verifica a causa della composizione. In genere è consigliabile creare tipi di dati più complessi che incorporano `Span<T>` e `ReadOnlySpan<T>` istanze. Tali tipi compositi dovrebbero essere struct e condividere tutti i rischi e i requisiti di `Span<T>`. Di conseguenza, le regole di sicurezza descritte di seguito dovrebbero essere visualizzate come applicabile all'intera gamma di **_tipi simili a Ref_** .

La [specifica del linguaggio bozza](#draft-language-specification) ha lo scopo di garantire che i valori di un tipo simile a Ref avvengano solo nello stack.

## <a name="generalized-ref-like-types-in-source-code"></a>Tipi di `ref-like` generalizzati nel codice sorgente

`ref-like` struct sono contrassegnati in modo esplicito nel codice sorgente usando il modificatore `ref`:

```csharp
ref struct TwoSpans<T>
{
    // can have ref-like instance fields
    public Span<T> first;
    public Span<T> second;
} 

// error: arrays of ref-like types are not allowed. 
TwoSpans<T>[] arr = null;

```

La designazione di uno struct come ref-like consentirà allo struct di avere campi di istanza di tipo ref e renderà disponibili anche tutti i requisiti di tipi simili a Ref applicabili allo struct. 

## <a name="metadata-representation-or-ref-like-structs"></a>Rappresentazione dei metadati o struct di tipo Ref

Gli struct di tipo ref verranno contrassegnati con l'attributo **System. Runtime. CompilerServices. IsRefLikeAttribute** .

L'attributo verrà aggiunto alle librerie di base comuni, ad esempio `mscorlib`. Se l'attributo non è disponibile, il compilatore genererà uno interno in modo analogo ad altri attributi incorporati su richiesta, ad esempio `IsReadOnlyAttribute`.

Viene eseguita una misura aggiuntiva per impedire l'uso di struct di tipo Ref nei compilatori che non conoscono le regole di sicurezza (sono C# inclusi i compilatori prima di quello in cui è implementata la funzionalità). 

Non avendo altre alternative valide che funzionano nei compilatori obsoleti senza manutenzione, viene aggiunto un attributo `Obsolete` con una stringa nota a tutti gli struct di tipo Ref. I compilatori che sanno come usare i tipi di tipo ref ignoreranno questo particolare tipo di `Obsolete`.

Rappresentazione tipica dei metadati:

```csharp
    [IsRefLike]
    [Obsolete("Types with embedded references are not supported in this version of your compiler.")]
    public struct TwoSpans<T>
    {
       // . . . .
    }
```

Nota: non è l'obiettivo di fare in modo che qualsiasi uso di tipi simili a Ref nei compilatori precedenti non riesca 100%. Questo è difficile da raggiungere e non è strettamente necessario. Ad esempio, esiste sempre un modo per aggirare il `Obsolete` usando il codice dinamico o, ad esempio, la creazione di una matrice di tipi simili a Ref tramite reflection.

In particolare, se l'utente vuole effettivamente inserire un `Obsolete` o `Deprecated` attributo in un tipo simile a un riferimento, non sarà possibile scegliere un valore diverso da quello predefinito, perché non è possibile applicare `Obsolete` attributo più di una volta.  

## <a name="examples"></a>Esempi:

```csharp
SpanLikeType M1(ref SpanLikeType x, Span<byte> y)
{
    // this is all valid, unconcerned with stack-referring stuff
    var local = new SpanLikeType(y);
    x = local;
    return x;
}

void Test1(ref SpanLikeType param1, Span<byte> param2)
{
    Span<byte> stackReferring1 = stackalloc byte[10];
    var stackReferring2 = new SpanLikeType(stackReferring1);

    // this is allowed
    stackReferring2 = M1(ref stackReferring2, stackReferring1);

    // this is NOT allowed
    stackReferring2 = M1(ref param1, stackReferring1);

    // this is NOT allowed
    param1 = M1(ref stackReferring2, stackReferring1);

    // this is NOT allowed
    param2 = stackReferring1.Slice(10);

    // this is allowed
    param1 = new SpanLikeType(param2);

    // this is allowed
    stackReferring2 = param1;
}

ref SpanLikeType M2(ref SpanLikeType x)
{
    return ref x;
}

ref SpanLikeType Test2(ref SpanLikeType param1, Span<byte> param2)
{
    Span<byte> stackReferring1 = stackalloc byte[10];
    var stackReferring2 = new SpanLikeType(stackReferring1);

    ref var stackReferring3 = M2(ref stackReferring2);

    // this is allowed
    stackReferring3 = M1(ref stackReferring2, stackReferring1);

    // this is allowed
    M2(ref stackReferring3) = stackReferring2;

    // this is NOT allowed
    M1(ref param1) = stackReferring2;

    // this is NOT allowed
    param1 = stackReferring3;

    // this is NOT allowed
    return ref stackReferring3;

    // this is allowed
    return ref param1;
}

```

----------------

## <a name="draft-language-specification"></a>Specifica del linguaggio bozza

Di seguito viene descritto un set di regole di sicurezza per i tipi di tipo ref (`ref struct`s) per garantire che i valori di questi tipi vengano eseguiti solo nello stack. Un set di regole di sicurezza diverso e più semplice è possibile se le variabili locali non possono essere passate per riferimento. Questa specifica consente inoltre la riassegnazione sicura delle variabili locali Ref.

### <a name="overview"></a>Panoramica

Si associa a ogni espressione in fase di compilazione il concetto di ambito a cui l'espressione può eseguire l'escape, ovvero "safe-to-escape". Analogamente, per ogni lvalue si mantiene un concetto dell'ambito a cui è consentito l'escape di un riferimento a esso, ovvero "Ref-safe-to-escape". Per un'espressione lvalue specificata, potrebbero essere diversi.

Si tratta di una procedura analoga alla "restituzione sicura" della funzionalità variabili locali Ref, ma con granularità più fine. Dove il "safe-to-return" di un'espressione registra solo se (o meno) può sfuggire al metodo di inclusione nel suo complesso, i record di tipo safe-to-escape per cui è possibile eseguire l'escape, ovvero l'ambito di cui non è possibile evitare l'escape. Il meccanismo di sicurezza di base viene applicato come indicato di seguito. Data un'assegnazione da un'espressione E1 con un ambito sicuro a escape S1, a un'espressione (lvalue) E2 con ambito safe-to-escape S2, si tratta di un errore se S2 è un ambito più ampio rispetto a S1. In base alla costruzione, i due ambiti S1 e S2 si trovano in una relazione di annidamento, perché un'espressione valida è sempre sicura per essere restituita da un ambito che include l'espressione.

Per il momento in cui è sufficiente, ai fini dell'analisi, per supportare solo due ambiti esterni al metodo e l'ambito di primo livello del metodo. Ciò è dovuto al fatto che non è possibile creare valori di tipo Ref con ambiti interni e le variabili locali Ref non supportano la riassegnazione. Le regole, tuttavia, possono supportare più di due livelli di ambito.

Di seguito sono riportate le regole precise per il calcolo dello stato di *sicurezza per la restituzione* di un'espressione e le regole relative alla legalità delle espressioni.

### <a name="ref-safe-to-escape"></a>Ref-safe-to-escape

*Ref-safe-to-escape* è un ambito, che include un'espressione lvalue, a cui è possibile eseguire l'escape di un riferimento a lvalue in modo sicuro. Se tale ambito è l'intero metodo, si afferma che un riferimento a lvalue è *sicuro per restituire* dal metodo.

### <a name="safe-to-escape"></a>da Safe a escape

Il tipo *safe-to-escape* è un ambito, che racchiude un'espressione a cui è sicuro che il valore venga sottoposto a escape. Se tale ambito è l'intero metodo, si afferma che un valore è *sicuro per restituire* dal metodo.

Un'espressione il cui tipo non è un tipo `ref struct` è *safe-to-return* dall'intero metodo contenitore. In caso contrario, si fa riferimento alle regole seguenti.

#### <a name="parameters"></a>Parametri

Un lvalue che designa un parametro formale è *ref-safe-to-escape* (per riferimento) come indicato di seguito:
- Se il parametro è un parametro `ref`, `out`o `in`, è *ref-safe-to-escape* dall'intero metodo (ad esempio tramite un'istruzione `return ref`); in caso contrario
- Se il parametro è il `this` parametro di un tipo struct, è *ref-safe-to-escape* nell'ambito di primo livello del metodo (ma non dall'intero metodo); [Esempio](#struct-this-escape) di
- In caso contrario, il parametro è un parametro di valore ed è *ref-safe-to-escape* per l'ambito di primo livello del metodo, ma non dal metodo stesso.

Un'espressione che è un rvalue che designa l'uso di un parametro formale è *safe-to-escape* (per valore) dall'intero metodo (ad esempio tramite un'istruzione `return`). Si applica anche al parametro `this`.

#### <a name="locals"></a>Variabili locali

Un lvalue che designa una variabile locale è *ref-safe-to-escape* (per riferimento) come indicato di seguito:
- Se la variabile è una variabile di `ref`, il relativo *ref-safe-to-escape* viene tratto da *ref-safe-to-escape* della relativa espressione di inizializzazione; in caso contrario
- La variabile è *ref-safe-to-escape* nell'ambito in cui è stata dichiarata.

Un'espressione che è un rvalue che designa l'uso di una variabile locale è *safe-to-escape* (per valore) come indicato di seguito:
- Tuttavia, la regola generale precedente, un oggetto locale il cui tipo non è un tipo `ref struct` è *safe-to-return* dall'intero metodo contenitore.
- Se la variabile è una variabile di iterazione di un ciclo di `foreach`, l'ambito di *sicurezza* della variabile è identico a quello di *safe-to-escape* dell'espressione del ciclo di `foreach`.
- Un oggetto locale di tipo `ref struct` e non inizializzato al momento della dichiarazione è *sicuro per essere restituito* dall'intero metodo contenitore.
- In caso contrario, il tipo della variabile è un tipo di `ref struct` e la dichiarazione della variabile richiede un inizializzatore. L'ambito di *sicurezza* della variabile è identico *a* quello del relativo inizializzatore.

#### <a name="field-reference"></a>Riferimento ai campi

Un lvalue che designa un riferimento a un campo, `e.F`, è *ref-safe-to-escape* (per riferimento) come indicato di seguito:
- Se `e` è di un tipo riferimento, è *ref-safe-to-escape* dall'intero metodo; in caso contrario
- Se `e` è di un tipo valore, il relativo *ref-safe-to-escape* viene tratto dal *ref-safe-to-escape* di `e`.

Rvalue che designa un riferimento a un campo, `e.F`, dispone di un ambito *di tipo safe-to-escape* uguale a quello della `e`di *sicurezza* .

#### <a name="operators-including-"></a>Operatori che includono `?:`

L'applicazione di un operatore definito dall'utente viene considerata come una chiamata al metodo.

Per un operatore che restituisce un rvalue, ad esempio `e1 + e2` o `c ? e1 : e2`, il valore *safe-to-escape* del risultato è l'ambito più piccolo tra il livello di *sicurezza di escape* degli operandi dell'operatore.  Di conseguenza, per un operatore unario che restituisce un rvalue, ad esempio `+e`, il valore *safe-to-escape* del risultato è il *safe-to-escape* dell'operando.

Per un operatore che restituisce un lvalue, ad esempio `c ? ref e1 : ref e2`
- *ref-safe-to-escape* del risultato è l'ambito più restrittivo tra *ref-safe-to-escape* degli operandi dell'operatore.
- il *safe-to-escape* degli operandi deve essere d'accordo e questo è il *safe-to-escape* del lvalue risultante.

#### <a name="method-invocation"></a>Chiamata al metodo

Un lvalue risultante da una chiamata al metodo che restituisce un riferimento `e1.M(e2, ...)` è *ref-safe-to-escape* il più piccolo degli ambiti seguenti:
- Intero metodo contenitore
- *ref-safe-to-escape* di tutte le espressioni `ref` e `out` argomenti (escluso il ricevitore)
- Per ogni `in` parametro del metodo, se è presente un'espressione corrispondente che è un lvalue, il relativo *ref-safe-to-escape*, in caso contrario l'ambito di inclusione più vicino
- il carattere di *escape sicuro per* tutte le espressioni di argomento (incluso il ricevitore)

> Nota: l'ultimo punto elenco è necessario per gestire il codice, ad esempio
> ```csharp
> var sp = new Span(...)
> return ref sp[0];
> ```
> o
> ```csharp
> return ref M(sp, 0);
> ```

Un rvalue risultante da una chiamata al metodo `e1.M(e2, ...)` è *sicuro per l'escape* dal più piccolo degli ambiti seguenti:
- Intero metodo contenitore
- il carattere di *escape sicuro per* tutte le espressioni di argomento (incluso il ricevitore)

#### <a name="an-rvalue"></a>Rvalue
Un rvalue è *ref-safe-to-escape* dall'ambito di inclusione più vicino. Questo problema si verifica ad esempio in una chiamata, ad esempio `M(ref d.Length)` in cui `d` è di tipo `dynamic`. È inoltre coerente con (ed eventualmente sussume) la gestione degli argomenti corrispondenti ai parametri `in`. *

#### <a name="property-invocations"></a>Chiamate di proprietà

Chiamata di proprietà (`get` o `set`) considerata come chiamata di metodo del metodo sottostante dalle regole precedenti.

#### `stackalloc`

Un'espressione stackalloc è un rvalue che è *sicuro* da escludere dall'ambito di primo livello del metodo (ma non dall'intero metodo).

#### <a name="constructor-invocations"></a>Chiamate al costruttore

Un'espressione `new` che richiama un costruttore rispetta le stesse regole di una chiamata al metodo considerata per restituire il tipo costruito.

Inoltre, *safe-to-escape* non è più ampio del più piccolo tra quelli di tipo *safe-to-escape* di tutti gli argomenti o operandi delle espressioni dell'inizializzatore di oggetto, in modo ricorsivo se è presente l'inizializzatore. 

#### <a name="span-constructor"></a>Costruttore Span
Il linguaggio si basa su `Span<T>` non dispone di un costruttore nel formato seguente:

```csharp
void Example(ref int x)
{
    // Create a span of length one
    var span = new Span<int>(ref x); 
}
```

Un costruttore di questo tipo rende `Span<T>` che vengono utilizzati come campi non distinguibili da un campo di `ref`. Le regole di sicurezza descritte in questo documento dipendono da `ref` campi che non sono un C#costrutto valido in o .NET.

#### <a name="default-expressions"></a>Espressioni `default`

Un'espressione `default` è *sicura per l'escape* dall'intero metodo contenitore.

## <a name="language-constraints"></a>Vincoli della lingua

Si vuole garantire che nessuna variabile locale `ref`, né variabile di tipo `ref struct`, faccia riferimento alla memoria dello stack o alle variabili che non sono più attive. Sono pertanto presenti i vincoli di linguaggio seguenti:

- Non è possibile rimuovere un parametro ref, né un oggetto local ref né un parametro o una variabile locale di un tipo `ref struct` in un'espressione lambda o in una funzione locale.

- Né un parametro ref né un parametro di un tipo `ref struct` possono essere un argomento su un metodo iteratore o un metodo `async`.

- Né un oggetto locale Ref né un tipo locale di un `ref struct` possono essere nell'ambito nel punto di un'istruzione `yield return` o di un'espressione `await`.

- Un tipo di `ref struct` non può essere utilizzato come argomento di tipo o come tipo di elemento in un tipo di tupla.

- Un tipo di `ref struct` non può essere il tipo dichiarato di un campo, ad eccezione del fatto che può essere il tipo dichiarato di un campo di istanza di un altro `ref struct`.

- Un tipo di `ref struct` non può essere il tipo di elemento di una matrice.

- Un valore di un tipo di `ref struct` non può essere boxed:
  - Non esiste alcuna conversione da un tipo di `ref struct` al tipo `object` o al tipo `System.ValueType`.
  - Non è possibile dichiarare un tipo di `ref struct` per implementare alcuna interfaccia
  - Nessun metodo di istanza dichiarato in `object` o in `System.ValueType` ma non sottoposto a override in un tipo di `ref struct` può essere chiamato con un destinatario di tale tipo `ref struct`.
  - Nessun metodo di istanza di un tipo di `ref struct` può essere acquisito dalla conversione del metodo in un tipo delegato.

- Per un `ref e1 = ref e2`di riassegnazione Ref, il valore di *riferimento-safe-to-escape* di `e2` deve essere almeno uguale a un ambito come *ref-safe-to-escape* di `e1`.

- Per un'istruzione return Ref `return ref e1`, *ref-safe-to-escape* di `e1` deve essere *ref-safe-to-escape* dall'intero metodo. (TODO: è necessaria anche una regola *in base alla* quale `e1` deve essere protetta dall'intero metodo o è ridondante?)

- Per un'istruzione return `return e1`, il valore *safe-to-escape* di `e1` deve essere *safe-to-escape* dall'intero metodo.

- Per un `e1 = e2`di assegnazione, se il tipo di `e1` è un tipo di `ref struct`, il valore *safe-to-escape* del `e2` deve essere almeno uguale a un ambito come il livello di *sicurezza* di `e1`.

- Per una chiamata al metodo se è presente un `ref` o `out` argomento di un tipo di `ref struct` (incluso il ricevitore), con la modalità *di escape* E1, nessun argomento (incluso il ricevitore) può avere una *sicurezza di escape* più stretta di E1. [Esempio](#method-arguments-must-match)

- Una funzione locale o una funzione anonima non può fare riferimento a un parametro o locale di `ref struct` tipo dichiarato in un ambito di inclusione.

> ***Problema aperto:*** È necessaria una regola che consenta di generare un errore quando è necessario eseguire il versamento di un valore dello stack di un tipo di `ref struct` in un'espressione await, ad esempio nel codice
> ```csharp
> Foo(new Span<int>(...), await e2);
> ```

## <a name="explanations"></a>Spiegazioni
Queste spiegazioni ed esempi illustrano il motivo per cui sono presenti molte delle regole di sicurezza precedenti

### <a name="method-arguments-must-match"></a>Gli argomenti del metodo devono corrispondere
Quando si richiama un metodo in cui è presente un `out`, `ref` parametro che è un `ref struct` incluso il ricevitore, il `ref struct` deve avere la stessa durata. Questa operazione è necessaria C# perché deve prendere tutte le decisioni relative alla sicurezza della durata, in base alle informazioni disponibili nella firma del metodo e alla durata dei valori nel sito di chiamata. 

Quando sono presenti `ref` parametri `ref struct` quindi esiste la possibilità di scambiare il contenuto. Quindi, nel sito di chiamata è necessario assicurarsi che tutti questi **potenziali** swap siano compatibili. Se la lingua non è stata applicata, sarà possibile scrivere codice errato come quello riportato di seguito.

```csharp
void M1(ref Span<int> s1)
{
    Span<int> s2 = stackalloc int[1];
    Swap(ref s1, ref s2);
}

void Swap(ref Span<int> x, ref int Span<int> y)
{
    // This will effectively assign the stackalloc to the s1 parameter and allow it
    // to escape to the caller of M1
    ref x = ref y; 
}
```

La restrizione sul ricevitore è necessaria perché mentre nessun contenuto è Ref-safe-to-escape, può archiviare i valori specificati. Ciò significa che con durate non corrispondenti è possibile creare un foro di sicurezza dei tipi nel modo seguente:

```csharp
ref struct S
{
    public Span<int> Span;

    public void Set(Span<int> span)
    {
        Span = span;
    }
}

void Broken(ref S s)
{
    Span<int> span = stackalloc int[1];

    // The result of a stackalloc is now stored in s.Span and escaped to the caller
    // of Broken
    s.Set(span); 
}
```

### <a name="struct-this-escape"></a>Struttura Escape
Quando si tratta di un'estensione delle regole di sicurezza, il valore `this` in un membro di istanza viene modellato come parametro per il membro. A questo punto, per un `struct` il tipo di `this` è effettivamente `ref S` dove in un `class` è semplicemente `S` (per i membri di un `class / struct` denominato S). 

Tuttavia `this` dispone di regole di escape diverse rispetto ad altri parametri di `ref`. In particolare, non è Ref-safe-to-escape mentre altri parametri sono:

```csharp
ref struct S
{ 
    int Field;

    // Illegal because this isn't safe to escape as ref
    ref int Get() => ref Field;

    // Legal
    ref int GetParam(ref int p) => ref p;
}
```

Il motivo di questa restrizione è in realtà molto semplice con la chiamata del membro `struct`. Esistono alcune regole che devono essere elaborate in relazione alla chiamata del membro sui membri `struct` in cui il ricevitore è un rvalue. Ma questo approccio è molto intuitivo. 

Il motivo di questa restrizione riguarda effettivamente la chiamata all'interfaccia. In particolare, si tratta di un valore che indica se l'esempio seguente non deve o non deve essere compilato;

```csharp
interface I1
{
    ref int Get();
}

ref int Use<T>(T p)
    where T : I1
{
    return ref p.Get();
}
```

Si consideri il caso in cui viene creata un'istanza di `T` come `struct`. Se il `this` parametro è Ref-safe-to-escape, il ritorno di `p.Get` può puntare allo stack (in particolare può essere un campo all'interno del tipo di cui è stata creata un'istanza di `T`). Ciò significa che il linguaggio non può consentire la compilazione di questo esempio perché potrebbe restituire un `ref` a una posizione dello stack. D'altra parte, se `this` non è Ref-safe-to-escape, `p.Get` non può fare riferimento allo stack e pertanto è sicuro restituire. 

Questo è il motivo per cui l'evasione di `this` in una `struct` è molto importante per le interfacce. Questa operazione può essere eseguita, ma presenta un compromesso. Alla fine, la progettazione si è rivelata a favore di rendere più flessibili le interfacce. 

In futuro, tuttavia, è possibile che ci si rilassi. 

## <a name="future-considerations"></a>Considerazioni

### <a name="length-one-spant-over-ref-values"></a>Lunghezza uno span\<T > sui valori Ref
Sebbene non sia valido oggi, esistono casi in cui la creazione di una lunghezza `Span<T>` istanza su un valore può risultare utile:

```csharp
void RefExample()
{
    int x = ...;

    // Today creating a length one Span<int> requires a stackalloc and a new 
    // local
    Span<int> span1 = stackalloc [] { x };
    Use(span1);
    x = span1[0]; 

    // Simpler to just allow length one span
    var span2 = new Span<int>(ref x);
    Use(span2);
}
```

Questa funzionalità risulta più interessante se si rilevano le restrizioni sui [buffer a dimensione fissa](https://github.com/dotnet/csharplang/blob/master/proposals/fixed-sized-buffers.md) in quanto consentono di `Span<T>` istanze di lunghezza ancora maggiore. 

Se è necessario arrestare questo percorso, è possibile che il linguaggio soddisfi questo problema assicurando che le istanze `Span<T>` siano state rivolte verso il basso. Si tratta di una condizione di *escape sicura* per l'ambito in cui sono stati creati. In questo modo si garantisce che la lingua non debba mai prendere in considerazione un valore `ref` di escape di un metodo tramite un `ref struct` restituire o un campo di `ref struct`. Questo potrebbe anche richiedere ulteriori modifiche per riconoscere questi costruttori come l'acquisizione di un parametro di `ref` in questo modo.
