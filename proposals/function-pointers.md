---
ms.openlocfilehash: d9080202f9413f8beb80db222d47f5fc082ae641
ms.sourcegitcommit: f3170512e7a3193efbcea52ec330648375e36915
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79485504"
---
# <a name="function-pointers"></a>Puntatori a funzione

## <a name="summary"></a>Summary

Questa proposta fornisce costrutti di linguaggio che espongono i codici operativi IL al quale attualmente non è possibile accedere in modo C# efficiente o, in oggi, `ldftn` e `calli`. Questi codici operativi IL può essere importante nel codice a prestazioni elevate e gli sviluppatori hanno bisogno di un modo efficiente per accedervi.

## <a name="motivation"></a>Motivazione

Le motivazioni e lo sfondo per questa funzionalità sono descritti nel seguente problema (come è una potenziale implementazione della funzionalità):

https://github.com/dotnet/csharplang/issues/191

Si tratta di una proposta di progettazione alternativa alle [funzioni intrinseche del compilatore](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)

## <a name="detailed-design"></a>Progettazione dettagliata

### <a name="function-pointers"></a>Puntatori funzione

Il linguaggio consentirà la dichiarazione di puntatori a funzione usando la sintassi del `delegate*`. La sintassi completa viene descritta in dettaglio nella sezione successiva, ma è concepita come la sintassi utilizzata da `Func` e `Action` dichiarazioni di tipo.

``` csharp
unsafe class Example {
    void Example(Action<int> a, delegate*<int, void> f) {
        a(42);
        f(42);
    }
}
```

Questi tipi sono rappresentati usando il tipo di puntatore a funzione, come descritto in ECMA-335. Ciò significa che la chiamata di un `delegate*` utilizzerà `calli` in cui la chiamata di un `delegate` utilizzerà `callvirt` sul metodo `Invoke`.
Sintatticamente, sebbene la chiamata sia identica per entrambi i costrutti.

La definizione ECMA-335 dei puntatori al metodo include la convenzione di chiamata come parte della firma del tipo (sezione 7,1).
La convenzione di chiamata predefinita verrà `managed`. È possibile specificare moduli alternativi aggiungendo il modificatore appropriato dopo la sintassi del `delegate*`: `managed`, `cdecl`, `stdcall`, `thiscall`o `unmanaged`. Esempio:

``` csharp
// This method will be invoked using the cdecl calling convention
delegate* cdecl<int, int>;

// This method will be invoked using the stdcall calling convention
delegate* stdcall<int, int>;
```

Le conversioni tra i tipi di `delegate*` vengono eseguite in base alla relativa firma, inclusa la convenzione di chiamata.

``` csharp
unsafe class Example {
    void Conversions() {
        delegate*<int, int, int> p1 = ...;
        delegate* managed<int, int, int> p2 = ...;
        delegate* cdecl<int, int, int> p3 = ...;

        p1 = p2; // okay p1 and p2 have compatible signatures
        Console.WriteLine(p2 == p1); // True
        p2 = p3; // error: calling conventions are incompatible
    }
}
```

Un tipo di `delegate*` è un tipo di puntatore che significa che ha tutte le funzionalità e le restrizioni di un tipo di puntatore standard:

- Valido solo in un contesto di `unsafe`.
- I metodi che contengono un `delegate*` parametro o un tipo restituito possono essere chiamati solo da un contesto di `unsafe`.
- Non può essere convertito in `object`.
- Non può essere usato come argomento generico.
- Può convertire in modo implicito `delegate*` in `void*`.
- Consente di convertire in modo esplicito da `void*` a `delegate*`.

Restrizioni:

- Gli attributi personalizzati non possono essere applicati a un `delegate*` o a uno dei relativi elementi.
- Non è possibile contrassegnare un parametro di `delegate*` come `params`
- Un tipo di `delegate*` presenta tutte le restrizioni di un tipo di puntatore normale.

### <a name="function-pointer-syntax"></a>Sintassi del puntatore a funzione

La sintassi del puntatore a funzione completa è rappresentata dalla grammatica seguente:

```antlr
pointer_type
    : ...
    | funcptr_type
    ;

funcptr_type
    : 'delegate' '*' calling_convention? '<' (funcptr_parameter_modifier? type ',')* funcptr_return_modifier? return_type '>'
    ;

calling_convention
    : 'cdecl'
    | 'managed'
    | 'stdcall'
    | 'thiscall'
    | 'unmanaged'
    ;

funcptr_parameter_modifier
    : 'ref'
    | 'out'
    | 'in'
    ;

funcptr_return_modifier
    : 'ref'
    | 'ref readonly'
    ;
```

La convenzione di chiamata `unmanaged` rappresenta la convenzione di chiamata predefinita per il codice nativo nella piattaforma corrente e viene codificata come WINAPI.
Tutte `calling_convention`s sono parole chiave contestuali quando precedute da una `delegate*`.

``` csharp
delegate int Func1(string s);
delegate Func1 Func2(Func1 f);

// Function pointer equivalent without calling convention
delegate*<string, int>;
delegate*<delegate*<string, int>, delegate*<string, int>>;

// Function pointer equivalent with calling convention
delegate* managed<string, int>;
delegate*<delegate* managed<string, int>, delegate*<string, int>>;
```

### <a name="function-pointer-conversions"></a>Conversioni di puntatori a funzione

In un contesto non sicuro, il set di conversioni implicite disponibili (conversioni implicite) viene esteso in modo da includere le seguenti conversioni implicite del puntatore:
- [_Conversioni esistenti_](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#pointer-conversions)
- Da _funcptr\_tipo_ `F0` a un altro _tipo di\_di funcptr_ `F1`, purché siano soddisfatte tutte le condizioni seguenti:
    - `F0` e `F1` hanno lo stesso numero di parametri e ogni parametro `D0n` in `F0` ha gli stessi modificatori `ref`, `out`o `in` del parametro corrispondente `D1n` in `F1`.
    - Per ogni parametro di valore (un parametro senza `ref`, `out`o modificatore di `in`), esiste una conversione di identità, una conversione di un riferimento implicito o una conversione del puntatore implicita dal tipo di parametro in `F0` al tipo di parametro corrispondente in `F1`.
    - Per ogni parametro `ref`, `out`o `in`, il tipo di parametro in `F0` corrisponde al tipo di parametro corrispondente in `F1`.
    - Se il tipo restituito è per valore (nessuna `ref` o `ref readonly`), esiste un'identità, un riferimento implicito o una conversione del puntatore implicita dal tipo restituito di `F1` al tipo restituito di `F0`.
    - Se il tipo restituito è per riferimento (`ref` o `ref readonly`), il tipo restituito e i modificatori di `ref` di `F1` corrispondono al tipo restituito e ai modificatori di `ref` di `F0`.
    - La convenzione di chiamata di `F0` corrisponde alla convenzione di chiamata di `F1`.

### <a name="allow-address-of-to-target-methods"></a>Consenti indirizzo-di ai metodi di destinazione

I gruppi di metodi saranno ora consentiti come argomenti per un'espressione address-of. Il tipo di tale espressione sarà un `delegate*` che ha la firma equivalente del metodo di destinazione e una convenzione di chiamata gestita:

``` csharp
unsafe class Util {
    public static void Log() { }

    void Use() {
        delegate*<void> ptr1 = &Util.Log;

        // Error: type "delegate*<void>" not compatible with "delegate*<int>";
        delegate*<int> ptr2 = &Util.Log;

        // Okay. Conversion to void* is always allowed.
        void* v = &Util.Log;
   }
}
```

In un contesto unsafe, un metodo `M` è compatibile con un tipo di puntatore a funzione `F` se si verificano tutte le condizioni seguenti:
- `M` e `F` hanno lo stesso numero di parametri e ogni parametro in `D` ha gli stessi modificatori `ref`, `out`o `in` del parametro corrispondente in `F`.
- Per ogni parametro di valore (un parametro senza `ref`, `out`o modificatore di `in`), esiste una conversione di identità, una conversione di un riferimento implicito o una conversione del puntatore implicita dal tipo di parametro in `M` al tipo di parametro corrispondente in `F`.
- Per ogni parametro `ref`, `out`o `in`, il tipo di parametro in `M` corrisponde al tipo di parametro corrispondente in `F`.
- Se il tipo restituito è per valore (nessuna `ref` o `ref readonly`), esiste un'identità, un riferimento implicito o una conversione del puntatore implicita dal tipo restituito di `F` al tipo restituito di `M`.
- Se il tipo restituito è per riferimento (`ref` o `ref readonly`), il tipo restituito e i modificatori di `ref` di `F` corrispondono al tipo restituito e ai modificatori di `ref` di `M`.
- La convenzione di chiamata di `M` corrisponde alla convenzione di chiamata di `F`.
- `M` è un metodo statico.

In un contesto non sicuro, esiste una conversione implicita da un'espressione address-of la cui destinazione è un gruppo di metodi `E` a un tipo di puntatore a funzione compatibile `F` se `E` contiene almeno un metodo applicabile nel formato normale a un elenco di argomenti costruito usando i tipi di parametro e i modificatori di `F`, come descritto di seguito.
- Viene selezionato un solo metodo `M` corrispondente a una chiamata al metodo del form `E(A)` con le modifiche seguenti:
   - L'elenco di argomenti `A` è un elenco di espressioni, ciascuna classificata come variabile e con il tipo e il modificatore (`ref`, `out`o `in`) del _parametro\_formale corrispondente\_elenco_ di `D`.
   - I metodi candidati sono solo i metodi che sono solo quelli applicabili nel formato normale, non quelli applicabili nella forma espansa.
- Se l'algoritmo delle chiamate di metodo genera un errore, si verifica un errore in fase di compilazione. In caso contrario, l'algoritmo produce un unico metodo migliore `M` avere lo stesso numero di parametri `F` e la conversione viene considerata esistente.
- Il metodo selezionato `M` deve essere compatibile (come definito in precedenza) con il tipo di puntatore a funzione `F`. In caso contrario, si verifica un errore in fase di compilazione.
- Il risultato della conversione è un puntatore a funzione di tipo `F`.

Esiste una conversione implicita da un'espressione address-of la cui destinazione è un gruppo di metodi `E` `void*` se è presente un solo metodo statico `M` in `E`.
Se è presente un metodo statico, il singolo metodo migliore da `E` è `M`.
In caso contrario, si verifica un errore in fase di compilazione.

Ciò significa che gli sviluppatori possono dipendere dalle regole di risoluzione dell'overload per lavorare insieme all'operatore address-of:

``` csharp
unsafe class Util {
    public static void Log() { }
    public static void Log(string p1) { }
    public static void Log(int i) { };

    void Use() {
        delegate*<void> a1 = &Log; // Log()
        delegate*<int, void> a2 = &Log; // Log(int i)

        // Error: ambiguous conversion from method group Log to "void*"
        void* v = &Log;
    }
```

L'operatore address-of verrà implementato utilizzando l'istruzione `ldftn`.

Limitazioni di questa funzionalità:

- Si applica solo ai metodi contrassegnati come `static`.
- Impossibile utilizzare le funzioni locali non`static` in `&`. I dettagli di implementazione di questi metodi non sono deliberatamente specificati dal linguaggio. Ciò include la presenza di un'istanza statica rispetto a quella con cui vengono emesse le firme.

### <a name="better-function-member"></a>Membro di funzione migliore

La specifica del membro di funzione migliore verrà modificata in modo da includere la riga seguente:

> Una `delegate*` è più specifica di `void*`

Ciò significa che è possibile eseguire l'overload su `void*` e un `delegate*` e comunque utilizzare l'operatore address-of.

## <a name="open-issues"></a>Problemi aperti

### <a name="nativecallableattribute"></a>NativeCallableAttribute

Si tratta di un attributo utilizzato da CLR per evitare il prologo gestito a nativo quando si richiama. I metodi contrassegnati da questo attributo sono richiamabili solo da codice nativo, non gestito (non è possibile chiamare metodi, creare un delegato e così via). L'attributo non è speciale per mscorlib; il runtime considererà qualsiasi attributo con questo nome con la stessa semantica.

È possibile che il runtime e il linguaggio collaborino per supportare completamente questa operazione. Il linguaggio può scegliere di considerare i membri `static` address-of con un attributo `NativeCallable` come `delegate*` con la convenzione di chiamata specificata.

``` csharp
unsafe class NativeCallableExample {
    [NativeCallable(CallingConvention.CDecl)]
    static void CloseHandle(IntPtr p) => Marshal.FreeHGlobal(p);

    void Use() {
        delegate*<IntPtr, void> p1 = &CloseHandle; // Error: Invalid calling convention

        delegate* cdecl<IntPtr, void> p2 = &CloseHandle; // Okay
    }
}

```

Inoltre, è probabile che il linguaggio desideri:

- Contrassegnare qualsiasi chiamata gestita a un metodo contrassegnato con `NativeCallable` come un errore. Dato che la funzione non può essere richiamata dal codice gestito, il compilatore deve impedire agli sviluppatori di provare tale chiamata.
- Impedisci le conversioni dei gruppi di metodi `delegate` quando il metodo è contrassegnato con `NativeCallable`.

Questa operazione non è necessaria per supportare `NativeCallable` tuttavia. Il compilatore è in grado di supportare l'attributo `NativeCallable` come utilizza la sintassi esistente. Il programma deve semplicemente eseguire il cast a `void*` prima di eseguire il cast alla firma `delegate*` corretta. Questa situazione non è ancora peggiore del supporto.

``` csharp
void* v = &CloseHandle;
delegate* cdecl<IntPtr, bool> f1 = (delegate* cdecl<IntPtr, bool>)v;
```

### <a name="extensible-set-of-unmanaged-calling-conventions"></a>Set estensibile di convenzioni di chiamata non gestite

Il set di convenzioni di chiamata non gestite supportate dalle codifiche ECMA-335 correnti è obsoleto. Sono state rilevate richieste per aggiungere il supporto per più convenzioni di chiamata non gestite, ad esempio:

- https://github.com/dotnet/coreclr/issues/12120 [vectorcall](https://docs.microsoft.com/cpp/cpp/vectorcall)
- StdCall con questa https://github.com/dotnet/coreclr/pull/23974#issuecomment-482991750 esplicita

La progettazione di questa funzionalità consente di estendere il set di convenzioni di chiamata non gestite in base alle esigenze in futuro. I problemi includono spazio limitato per la codifica delle convenzioni di chiamata (12 valori di 16 vengono presi in `IMAGE_CEE_CS_CALLCONV_MASK`) e il numero di posizioni che devono essere modificate per aggiungere una nuova convenzione di chiamata. Una possibile soluzione consiste nell'introdurre una nuova codifica che rappresenti la convenzione di chiamata usando [`System.Runtime.InteropServices.CallingConvention`](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.callingconvention) enum.

Per riferimento https://github.com/llvm/llvm-project/blob/master/llvm/include/llvm/IR/CallingConv.h contiene l'elenco delle convenzioni di chiamata supportate da LLVM. Sebbene sia improbabile che .NET debba necessariamente supportare tutti questi elementi, dimostra che lo spazio delle convenzioni di chiamata è molto ricco.

## <a name="considerations"></a>Considerazioni

### <a name="allow-instance-methods"></a>Consenti metodi di istanza

È possibile estendere la proposta per supportare i metodi di istanza sfruttando la convenzione di chiamata dell'interfaccia della riga di C# comando `EXPLICITTHIS` (denominata `instance` nel codice). Questa forma di puntatori a funzione dell'interfaccia della riga di comando inserisce il parametro `this` come primo parametro esplicito della sintassi del puntatore a funzione.

``` csharp
unsafe class Instance {
    void Use() {
        delegate* instance<Instance, string> f = &ToString;
        f(this);
    }
}
```

Si tratta di un suono che aggiunge alcune complicazioni alla proposta. In particolare, poiché i puntatori a funzione che differiscono dalla convenzione di chiamata `instance` e `managed` sarebbero incompatibili anche se entrambi i casi vengono usati per richiamare metodi C# gestiti con la stessa firma. Anche in tutti i casi in cui questo potrebbe essere utile avere un lavoro semplice: usare una funzione locale `static`.

``` csharp
unsafe class Instance {
    void Use() {
        static string toString(Instance i) = i.ToString();
        delgate*<Instance, string> f = &toString;
        f(this);
    }
}
```

### <a name="dont-require-unsafe-at-declaration"></a>Non richiedere unsafe alla dichiarazione

Anziché richiedere `unsafe` a ogni utilizzo di un `delegate*`, richiederlo solo nel punto in cui un gruppo di metodi viene convertito in un `delegate*`. Questo è il punto in cui vengono rilevati i problemi di sicurezza di base (sapendo che l'assembly contenitore non può essere scaricato mentre il valore è attivo). La richiesta di `unsafe` nelle altre posizioni può essere considerata eccessiva.

Questo è il modo in cui la progettazione è stata originariamente progettata. Ma le regole del linguaggio risultavano molto scomode. Non è possibile nascondere il fatto che si tratta di un valore di puntatore e la visualizzazione continua anche senza la parola chiave `unsafe`. La conversione in `object`, ad esempio, non può essere consentita, ma non può essere un membro di un `class`e così via. Il C# progetto prevede di richiedere `unsafe` per tutti gli utilizzi dei puntatori e di conseguenza questa progettazione lo segue.

Gli sviluppatori saranno comunque in grado di presentare un wrapper _sicuro_ oltre ai valori `delegate*` nello stesso modo in cui fanno oggi i normali tipi di puntatore. Prendere in considerazione:

``` csharp
unsafe struct Action {
    delegate*<void> _ptr;

    Action(delegate*<void> ptr) => _ptr = ptr;
    public void Invoke() => _ptr();
}
```

### <a name="using-delegates"></a>Uso di delegati

Invece di usare un nuovo elemento della sintassi, `delegate*`, è sufficiente usare i tipi di `delegate` esistenti con una `*` che segue il tipo:

``` csharp
Func<object, object, bool>* ptr = &object.ReferenceEquals;
```

La gestione della convenzione di chiamata può essere eseguita annotando i tipi di `delegate` con un attributo che specifica un valore `CallingConvention`. La mancanza di un attributo significa la convenzione di chiamata gestita.

La codifica in IL è problematica. Il valore sottostante deve essere rappresentato come puntatore, ma deve anche:

1. Hanno un tipo univoco per consentire gli overload con tipi di puntatore a funzione diversi.
1. Essere equivalente a scopi OHI tra i limiti di assembly.

L'ultimo punto è particolarmente problematico. Ciò significa che ogni assembly che usa `Func<int>*` deve codificare un tipo equivalente nei metadati anche se `Func<int>*` è definito in un assembly, sebbene non controlli.
Inoltre, tutti gli altri tipi definiti con il nome `System.Func<T>` in un assembly che non è mscorlib devono essere diversi dalla versione definita in mscorlib.

Un'opzione esplorata è stata la creazione di un puntatore di questo tipo come `mod_req(Func<int>) void*`. Questa operazione non funziona anche se un `mod_req` non può essere associato a una `TypeSpec` e pertanto non può fare riferimento a creazioni di istanze generiche.

### <a name="named-function-pointers"></a>Puntatori a funzioni denominate

La sintassi del puntatore a funzione può essere complessa, soprattutto in casi complessi come i puntatori a funzioni annidate. Anziché fare in modo che gli sviluppatori digitino la firma ogni volta che la lingua potrebbe consentire le dichiarazioni denominate dei puntatori a funzione, come avviene con `delegate`.

``` csharp
func* void Action();

unsafe class NamedExample {
    void M(Action a) {
        a();
    }
}
```

Parte del problema è che la primitiva dell'interfaccia della riga di comando sottostante non ha nomi, C# pertanto si tratterebbe puramente di un'invenzione e richiederebbe un po' di lavoro di metadati da abilitare. Questa operazione è fattibile, ma è una questione significativa di lavoro. In sostanza C# , è necessario disporre di un complementare alla tabella def del tipo esclusivamente per questi nomi.

Inoltre, quando sono stati esaminati gli argomenti per i puntatori a funzione denominati, è possibile che si applichino ugualmente a diversi altri scenari. Ad esempio, sarebbe altrettanto semplice dichiarare le tuple denominate per ridurre la necessità di digitare la firma completa in tutti i casi.

``` csharp
(int x, int y) Point;

class NamedTupleExample {
    void M(Point p) {
        Console.WriteLine(p.x);
    }
}
```

Dopo la discussione si è deciso di non consentire la dichiarazione denominata dei tipi di `delegate*`. Se viene rilevata una necessità significativa in base ai commenti e suggerimenti sull'utilizzo dei clienti, viene esaminata una soluzione di denominazione che funziona per i puntatori a funzione, le tuple, i generics e così via. È probabile che questo aspetto sia simile ad altri suggerimenti, come il supporto completo `typedef` nel linguaggio.

## <a name="future-considerations"></a>Considerazioni

### <a name="static-local-functions"></a>funzioni locali statiche

Si fa riferimento alla [proposta](https://github.com/dotnet/csharplang/issues/1565) per consentire il modificatore `static` sulle funzioni locali. Una funzione di questo tipo verrebbe generata come `static` e con la firma esatta specificata nel codice sorgente. Una funzione di questo tipo deve essere un argomento valido per `&` perché non contiene alcun problema attualmente presente nelle funzioni locali

### <a name="static-delegates"></a>delegati statici

Si fa riferimento alla [proposta](https://github.com/dotnet/csharplang/issues/302) per consentire la dichiarazione di tipi di `delegate` che possono fare riferimento solo ai membri `static`. Il vantaggio è che tali istanze di `delegate` possono essere allocazione gratuite e migliori negli scenari sensibili alle prestazioni.

Se la funzionalità del puntatore a funzione è implementata, è probabile che la proposta di `static delegate` venga chiusa. Il vantaggio proposto da questa funzionalità è la natura di allocazione. Sono state rilevate tuttavia ricerche recenti che non è possibile ottenere a causa dello scaricamento di assembly. Il `static delegate` al metodo a cui si riferisce deve essere un handle sicuro per evitare che l'assembly venga scaricato dall'interno.

Per mantenere ogni `static delegate` istanza è necessario allocare un nuovo handle che esegue il contatore agli obiettivi della proposta. Sono stati creati alcuni progetti in cui l'allocazione può essere ammortizzata in una singola allocazione per ogni chiamata, ma si tratta di un po' complesso che non sembra essere il compromesso.

Ciò significa che gli sviluppatori devono essenzialmente decidere tra i compromessi seguenti:

1. Sicurezza in caso di scaricamento di assembly: è necessario allocazioni e quindi `delegate` è già un'opzione sufficiente.
1. Nessuna protezione in caso di scaricamento di assembly: usare un `delegate*`. Questo può essere incluso in un `struct` per consentire l'utilizzo all'esterno di un contesto di `unsafe` nel resto del codice.
