---
ms.openlocfilehash: 9e7059b579060e547e4665ac50518581fe3512e6
ms.sourcegitcommit: 52624f54c0ad99a4d659fe4e2560a472be795c49
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81005704"
---
# <a name="function-pointers"></a>Puntatori a funzione

## <a name="summary"></a>Riepilogo

Questa proposta fornisce costrutti di linguaggio che espongono i codici operativi `ldftn` IL `calli`a cui non è attualmente possibile accedere in modo efficiente o, oggi in C: e . Questi codici operativi IL possono essere importanti nel codice ad alte prestazioni e gli sviluppatori hanno bisogno di un modo efficiente per accedervi.

## <a name="motivation"></a>Motivazione

Le motivazioni e le informazioni di base per questa funzionalità sono descritte nel seguente numero (come è una potenziale implementazione della funzionalità):

https://github.com/dotnet/csharplang/issues/191

Si tratta di una proposta di progettazione alternativa alle [intrinseche del compilatore](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)

## <a name="detailed-design"></a>Progettazione dettagliata

### <a name="function-pointers"></a>Puntatori funzione

Il linguaggio consentirà la dichiarazione `delegate*` di puntatori a funzione utilizzando la sintassi. La sintassi completa è descritta in dettaglio nella sezione `Func` successiva, ma è destinata ad assomigliare alla sintassi utilizzata dalle dichiarazioni e `Action` type.

``` csharp
unsafe class Example {
    void Example(Action<int> a, delegate*<int, void> f) {
        a(42);
        f(42);
    }
}
```

Questi tipi vengono rappresentati utilizzando il tipo di puntatore a funzione come descritto in ECMA-335. Ciò significa che `delegate*` la `calli` chiamata di `delegate` un `callvirt` verrà `Invoke` utilizzato dove la chiamata di un verrà utilizzato sul metodo.
Sintatticamente, anche se la chiamata è identica per entrambi i costrutti.

La definizione ECMA-335 dei puntatori al metodo include la convenzione di chiamata come parte della firma del tipo (sezione 7.1).
La convenzione di `managed`chiamata predefinita sarà . È possibile specificare forme alternative aggiungendo `delegate*` il `managed` `cdecl`modificatore `thiscall`appropriato `unmanaged`dopo la sintassi: , , `stdcall`, o . Esempio:

``` csharp
// This method will be invoked using the cdecl calling convention
delegate* cdecl<int, int>;

// This method will be invoked using the stdcall calling convention
delegate* stdcall<int, int>;
```

Le conversioni tra `delegate*` tipi vengono eseguite in base alla firma, inclusa la convenzione di chiamata.

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

Un `delegate*` tipo è un tipo di puntatore che significa che ha tutte le funzionalità e le restrizioni di un tipo di puntatore standard:A type is a pointer type which means it has all of the capabilities and restrictions of a standard pointer type:

- Valido solo `unsafe` in un contesto.
- I metodi `delegate*` che contengono un parametro `unsafe` o un tipo restituito possono essere chiamati solo da un contesto.
- Non può `object`essere convertito in .
- Non può essere utilizzato come argomento generico.
- Può convertire `delegate*` in `void*`modo implicito in .
- Può convertire `void*` in `delegate*`modo esplicito da a .

Restrizioni:

- Gli attributi personalizzati non `delegate*` possono essere applicati a uno o a i relativi elementi.
- Un `delegate*` parametro non può essere contrassegnato come`params`
- Un `delegate*` tipo ha tutte le restrizioni di un tipo di puntatore normale.

### <a name="function-pointer-syntax"></a>Sintassi del puntatore a funzione

La sintassi completa del puntatore a funzione è rappresentata dalla grammatica seguente:

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

La `unmanaged` convenzione di chiamata rappresenta la convenzione di chiamata predefinita per il codice nativo nella piattaforma corrente e viene codificata come winapi.
Tutte `calling_convention`le s sono parole chiave `delegate*`contestuali se precedute da un oggetto .

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

In un contesto unsafe, il set di conversioni implicite disponibili (conversioni implicite) viene esteso per includere le seguenti conversioni implicite del puntatore:
- [_Conversioni esistenti_](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#pointer-conversions)
- Dal `F0` tipo _funcptr\__ a un altro tipo `F1` _funcptr\__ , purché tutte le seguenti condizioni siano vere:
    - `F0`e `F1` hanno lo stesso numero di `D0n` `F0` parametri e `ref` `out`ogni `in` parametro in ha `D1n` `F1`gli stessi modificatori , , o del parametro corrispondente in .
    - Per ogni parametro di `ref`valore `out`(parametro senza modificatore , o o `in` ), esiste una `F0` conversione di identità, una conversione implicita dei riferimenti o una conversione implicita del puntatore dal tipo di parametro in al tipo di parametro corrispondente in `F1`.
    - Per `ref`ogni `out`parametro , o `in` `F0` , il tipo di parametro in è lo stesso del tipo di parametro corrispondente in `F1`.
    - Se il tipo restituito è `ref` `ref readonly`in base al valore (no o ), esiste `F1` un'identità, `F0`un riferimento implicito o una conversione implicita del puntatore dal tipo restituito di al tipo restituito di .
    - Se il tipo restituito`ref` è `ref readonly`per riferimento `ref` ( o `F1` ), il tipo restituito `ref` e `F0`i modificatori di sono uguali al tipo restituito e ai modificatori di .
    - La convenzione `F0` di chiamata di è `F1`uguale alla convenzione di chiamata di .

### <a name="allow-address-of-to-target-methods"></a>Consentire i metodi address-of a target

I gruppi di metodi saranno ora consentiti come argomenti a un'espressione address-of. Il tipo di tale espressione `delegate*` sarà un che ha la firma equivalente del metodo di destinazione e una convenzione di chiamata gestita:The type of such an expression will be a which has the equivalent signature of the target method and a managed calling convention:

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

In un contesto unsafe, un metodo `M` è `F` compatibile con un tipo di puntatore a funzione se tutte le seguenti sono vere:
- `M`e `F` hanno lo stesso numero di `D` parametri e `ref` `out`ogni `in` parametro in ha `F`gli stessi modificatori , , o del parametro corrispondente in .
- Per ogni parametro di `ref`valore `out`(parametro senza modificatore , o o `in` ), esiste una `M` conversione di identità, una conversione implicita dei riferimenti o una conversione implicita del puntatore dal tipo di parametro in al tipo di parametro corrispondente in `F`.
- Per `ref`ogni `out`parametro , o `in` `M` , il tipo di parametro in è lo stesso del tipo di parametro corrispondente in `F`.
- Se il tipo restituito è `ref` `ref readonly`in base al valore (no o ), esiste `F` un'identità, `M`un riferimento implicito o una conversione implicita del puntatore dal tipo restituito di al tipo restituito di .
- Se il tipo restituito`ref` è `ref readonly`per riferimento `ref` ( o `F` ), il tipo restituito `ref` e `M`i modificatori di sono uguali al tipo restituito e ai modificatori di .
- La convenzione `M` di chiamata di è `F`uguale alla convenzione di chiamata di .
- `M` è un metodo statico.

In un contesto unsafe esiste una conversione implicita da `E` un'espressione address-of `F` `E` la cui destinazione è un gruppo di metodi a un tipo di puntatore `F`a funzione compatibile se contiene almeno un metodo applicabile nella sua forma normale a un elenco di argomenti costruito utilizzando i tipi di parametro e i modificatori di , come descritto di seguito.
- Viene selezionato `M` un singolo metodo corrispondente a `E(A)` una chiamata al metodo del form con le seguenti modifiche:
   - L'elenco `A` di argomenti è un elenco di espressioni, ognuna`ref` `out`classificata `in`come variabile e con il tipo e il modificatore ( , , o ) dell'elenco di _\_\_parametri formali_ corrispondente di `D`.
   - I metodi candidati sono solo i metodi applicabili nella loro forma normale, non quelli applicabili nella loro forma espansa.
   - I metodi candidati sono solo quei metodi che sono statici.
- Se l'algoritmo delle chiamate al metodo genera un errore, si verifica un errore in fase di compilazione. In caso contrario, l'algoritmo produce un singolo metodo `M` migliore con lo stesso numero di parametri `F` e la conversione viene considerata esistente.
- Il metodo `M` selezionato deve essere compatibile (come definito `F`in precedenza) con il tipo di puntatore a funzione . In caso contrario, si verifica un errore in fase di compilazione.
- Il risultato della conversione è `F`un puntatore a funzione di tipo .

Esiste una conversione implicita da un'espressione `E` address-of la cui destinazione è un gruppo di `void*` metodi se è presente un solo metodo `M` statico in `E`.
Se è presente un metodo statico, `E` il `M`singolo metodo migliore da è .
In caso contrario, si verifica un errore in fase di compilazione.

Ciò significa che gli sviluppatori possono dipendere dalle regole di risoluzione dell'overload per lavorare insieme all'operatore address-of:This means developers can depend on overload resolution rules to work in conjunction with the address-of operator:

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

L'operatore address-of verrà `ldftn` implementato mediante l'istruzione .

Restrizioni di questa funzione:

- Si applica solo `static`ai metodi contrassegnati come .
- Le`static` funzioni non locali `&`non possono essere utilizzate in . I dettagli di implementazione di questi metodi non sono deliberatamente specificati dal linguaggio. Ciò include se sono statici rispetto all'istanza o esattamente la firma con cui vengono generate.


### <a name="operators-on-function-pointer-types"></a>Operatori sui tipi di puntatore a funzioneOperators on Function Pointer Types

La sezione nel codice unsafe sugli operatori viene modificata come tale:The section in unsafe code on operators is modified as such:

> In un contesto unsafe, sono disponibili diversi\_costrutti per\_l'utilizzo di tutti i type_s _pointer che non _funcptr type_s:
>
> *  L'operatore `*` può essere utilizzato per eseguire il riferimento indiretto del puntatore ([riferimento indiretto del puntatore](unsafe-code.md#pointer-indirection)).
> *  L'operatore `->` può essere utilizzato per accedere a un membro di una struttura tramite un puntatore ([Accesso ai membri del puntatore](unsafe-code.md#pointer-member-access)).
> *  L'operatore `[]` può essere utilizzato per indicizzare un puntatore ([Accesso all'elemento Puntatore](unsafe-code.md#pointer-element-access)).
> *  L'operatore `&` può essere utilizzato per ottenere l'indirizzo di una variabile ([L'operatore address-of](unsafe-code.md#the-address-of-operator)).
> *  Gli `++` `--` operatori e possono essere utilizzati per incrementare e diminuire i puntatori ([Incremento e decremento del puntatore](unsafe-code.md#pointer-increment-and-decrement)).
> *  Gli `+` `-` operatori e possono essere utilizzati per eseguire l'aritmetica dei puntatori ([Pointer arithmetic](unsafe-code.md#pointer-arithmetic)).
> *  Gli `==` `!=`operatori `<` `>`, `<=`, `=>` , , e possono essere utilizzati per confrontare i puntatori ([Confronto di puntatori](unsafe-code.md#pointer-comparison)).
> *  L'operatore `stackalloc` può essere utilizzato per allocare memoria dallo stack di chiamate ( buffer a[dimensione fissa](unsafe-code.md#fixed-size-buffers)).
> *  L'istruzione `fixed` può essere utilizzata per correggere temporaneamente una variabile in modo che sia possibile ottenere il relativo indirizzo ([L'istruzione fixed](unsafe-code.md#the-fixed-statement)).
> 
> In un contesto unsafe, sono disponibili diversi\_costrutti per l'utilizzo su tutti i _funcptr type_s:
> *  L'operatore `&` può essere utilizzato per ottenere l'indirizzo dei metodi statici ([Consenti address-of a target](function-pointers.md#allow-address-of-to-target-methods)methods )
> *  Gli `==` `!=`operatori `<` `>`, `<=`, `=>` , , e possono essere utilizzati per confrontare i puntatori ([Confronto di puntatori](unsafe-code.md#pointer-comparison)).

Inoltre, vengono modificate tutte `Pointers in expressions` le sezioni in per `Pointer comparison` proibire i tipi di puntatori a funzione, ad eccezione di e `The sizeof operator`.

### <a name="better-function-member"></a>Membro funzione migliore

La specifica del membro funzione migliore verrà modificata per includere la riga seguente:The better function member specification will be changed to include the following line:

> A `delegate*` è più specifico di`void*`

Ciò significa che è `void*` possibile `delegate*` sovraccaricare e un e ancora in modo ragionevole utilizzare l'operatore address-of.

## <a name="open-issues"></a>Open Issues

### <a name="nativecallableattribute"></a>NativeCallableAttribute

Si tratta di un attributo utilizzato da CLR per evitare il prologo gestito a nativo durante la chiamata. I metodi contrassegnati da questo attributo sono chiamabili solo dal codice nativo, non gestiti (non è possibile chiamare metodi, creare un delegato e così via). L'attributo non è speciale per mscorlib; il runtime tratterà qualsiasi attributo con questo nome con la stessa semantica.

È possibile che il runtime e il linguaggio collaborino per supportare completamente questa operazione. La lingua può scegliere di `static` trattare `NativeCallable` i `delegate*` membri address-of con un attributo come un con la convenzione di chiamata specificata.

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

Inoltre la lingua sarebbe probabilmente anche voglia di:

- Contrassegnare tutte le chiamate gestite a un metodo contrassegnato con `NativeCallable` come errore. Dato che la funzione non può essere richiamata dal codice gestito, il compilatore deve impedire agli sviluppatori di tentare una chiamata di questo tipo.
- Impedire le conversioni `delegate` del gruppo di `NativeCallable`metodi quando il metodo è contrassegnato con .

Questo non è `NativeCallable` necessario sostenere però. Il compilatore `NativeCallable` può supportare l'attributo come sta utilizzando la sintassi esistente. Il programma avrebbe semplicemente `void*` bisogno di eseguire `delegate*` il cast a prima del cast alla firma corretta. Non sarebbe peggio del sostegno odierno.

``` csharp
void* v = &CloseHandle;
delegate* cdecl<IntPtr, bool> f1 = (delegate* cdecl<IntPtr, bool>)v;
```

### <a name="extensible-set-of-unmanaged-calling-conventions"></a>Set estendibile di convenzioni di chiamata non gestiteExtensible set of unmanaged calling conventions

Il set di convenzioni di chiamata non gestite supportate dalle codifiche ECMA-335 correnti è obsoleto. Sono state riscontrate richieste di aggiunta del supporto per più convenzioni di chiamata non gestite, ad esempio:We have seen requests to add support for more unmanaged calling conventions, for example:

- [vectorcall (chiamata vettoriale)](https://docs.microsoft.com/cpp/cpp/vectorcall)https://github.com/dotnet/coreclr/issues/12120
- StdCall con esplicito questohttps://github.com/dotnet/coreclr/pull/23974#issuecomment-482991750

La progettazione di questa funzionalità deve consentire l'estensione del set di convenzioni di chiamata non gestite in base alle esigenze future. I problemi includono spazio limitato per la codifica delle convenzioni `IMAGE_CEE_CS_CALLCONV_MASK`di chiamata (12 su 16 valori vengono presi in ) e il numero di posizioni che devono essere toccate per aggiungere una nuova convenzione di chiamata. Una potenziale soluzione consiste nell'introdurre una [`System.Runtime.InteropServices.CallingConvention`](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.callingconvention) nuova codifica che rappresenta la convenzione di chiamata utilizzando enum.

Per riferimento, https://github.com/llvm/llvm-project/blob/master/llvm/include/llvm/IR/CallingConv.h include l'elenco delle convenzioni di chiamata supportate da LLVM. Sebbene sia improbabile che .NET debba mai supportarli tutti, dimostra che lo spazio delle convenzioni di chiamata è molto ricco.

## <a name="considerations"></a>Considerazioni

### <a name="allow-instance-methods"></a>Consentire metodi di istanza

La proposta potrebbe essere estesa per supportare `EXPLICITTHIS` i metodi `instance` di istanza sfruttando la convenzione di chiamata CLI (denominato nel codice C . Questa forma di puntatori `this` a funzione CLI inserisce il parametro come primo parametro esplicito della sintassi del puntatore a funzione.

``` csharp
unsafe class Instance {
    void Use() {
        delegate* instance<Instance, string> f = &ToString;
        f(this);
    }
}
```

Questo è valido, ma aggiunge qualche complicazione alla proposta. In particolare perché i puntatori a `instance` `managed` funzione che differivano dalla convenzione di chiamata e sarebbero incompatibili anche se entrambi i casi vengono utilizzati per richiamare i metodi gestiti con la stessa firma di C. Anche in ogni caso considerato dove questo sarebbe utile avere c'era un semplice lavoro intorno: utilizzare una `static` funzione locale.

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

Anziché `unsafe` richiedere a ogni `delegate*`utilizzo di un oggetto , è necessario `delegate*`solo nel punto in cui un gruppo di metodi viene convertito in un oggetto . È qui che entrano in gioco i problemi di sicurezza di base (sapendo che l'assieme che lo contiene non può essere scaricato mentre il valore è attivo). Richiedere `unsafe` in altre posizioni può essere visto come eccessivo.

Questo è il modo in cui il design è stato originariamente progettato. Ma le regole linguistiche risultanti sembravano molto scomode. È impossibile nascondere il fatto che si tratta di un valore `unsafe` del puntatore e ha continuato a sbirciare anche senza la parola chiave. Ad esempio la `object` conversione in non può essere consentita, `class`non può essere un membro di un, ecc ... La progettazione di `unsafe` C , è necessario per tutti gli utilizzi del puntatore e quindi questa progettazione segue che.

Gli sviluppatori saranno comunque _safe_ in grado `delegate*` di presentare un wrapper sicuro sopra i valori nello stesso modo in cui avvengono per i tipi di puntatore normali oggi. Prendere in considerazione:

``` csharp
unsafe struct Action {
    delegate*<void> _ptr;

    Action(delegate*<void> ptr) => _ptr = ptr;
    public void Invoke() => _ptr();
}
```

### <a name="using-delegates"></a>Utilizzo dei delegati

Anziché utilizzare un nuovo `delegate*`elemento di `delegate` sintassi, è sufficiente utilizzare i tipi esistenti con il `*` seguente tipo:

``` csharp
Func<object, object, bool>* ptr = &object.ReferenceEquals;
```

La gestione della convenzione di chiamata `delegate` può essere eseguita `CallingConvention` annotando i tipi con un attributo che specifica un valore. La mancanza di un attributo significherebbe la convenzione di chiamata gestita.

Codificare questo in IL è problematico. Il valore sottostante deve essere rappresentato come un puntatore ma deve anche:

1. Avere un tipo univoco per consentire gli overload con diversi tipi di puntatore a funzione.
1. Essere equivalente per scopi OHI oltre i limiti dell'assieme.

L'ultimo punto è particolarmente problematico. Ciò significa che `Func<int>*` ogni assembly che utilizza deve `Func<int>*` codificare un tipo equivalente nei metadati anche se è definito in un assembly anche se non controllarlo.
Inoltre qualsiasi altro tipo definito con `System.Func<T>` il nome in un assembly che non è mscorlib deve essere diverso da quello definito in mscorlib.

Un'opzione che è stata esplorata `mod_req(Func<int>) void*`è stata l'emissione di un puntatore come . Questo non funziona anche `mod_req` se come `TypeSpec` un non può associarsi a un e quindi non può indirizzare le istanze generiche.

### <a name="named-function-pointers"></a>Puntatori a funzione denominata

La sintassi del puntatore a funzione può essere complessa, in particolare nei casi complessi come i puntatori a funzione annidati. Anziché fare in modo che gli sviluppatori eseguano la firma ogni `delegate`volta che il linguaggio potrebbe consentire dichiarazioni denominate di puntatori a funzione come avviene con .

``` csharp
func* void Action();

unsafe class NamedExample {
    void M(Action a) {
        a();
    }
}
```

Parte del problema qui è la primitiva CLI sottostante non ha nomi, quindi questo sarebbe puramente un'invenzione di C , e richiedono un po ' di lavoro di metadati per abilitare. Questo è fattibile, ma è un'importante questione di lavoro. In sostanza, è necessario che il linguaggio C'è un complemento alla tabella def del tipo esclusivamente per questi nomi.

Anche quando sono stati esaminati gli argomenti per i puntatori a funzione denominati abbiamo scoperto che potevano applicarsi altrettanto bene a una serie di altri scenari. Ad esempio, sarebbe altrettanto conveniente dichiarare le tuple denominate per ridurre la necessità di digitare la firma completa in tutti i casi.

``` csharp
(int x, int y) Point;

class NamedTupleExample {
    void M(Point p) {
        Console.WriteLine(p.x);
    }
}
```

Dopo la discussione abbiamo deciso `delegate*` di non consentire la dichiarazione denominata dei tipi. Se troviamo che c'è una notevole necessità per questo in base al feedback sull'utilizzo dei clienti, allora esamineremo una soluzione di denominazione che funziona per i puntatori a funzione, tuple, generics, ecc ... Questo è probabile che sia simile in `typedef` forma ad altri suggerimenti come il pieno supporto nella lingua.

## <a name="future-considerations"></a>Considerazioni

### <a name="static-local-functions"></a>funzioni locali statiche

Questo si riferisce alla `static` [proposta](https://github.com/dotnet/csharplang/issues/1565) di consentire il modificatore sulle funzioni locali. Tale funzione sarebbe garantita per essere `static` emessa come e con la firma esatta specificata nel codice sorgente. Tale funzione dovrebbe essere un `&` argomento valido in quanto non contiene nessuno dei problemi che le funzioni locali hanno oggi

### <a name="static-delegates"></a>delegati statici

Ciò [the proposal](https://github.com/dotnet/csharplang/issues/302) si riferisce alla proposta `delegate` di consentire la `static` dichiarazione di tipi che possono riferirsi solo ai membri. Il vantaggio è `delegate` che tali istanze possono essere senza allocazione e migliori negli scenari sensibili alle prestazioni.

Se viene implementata la `static delegate` funzionalità del puntatore a funzione, la proposta verrà probabilmente chiusa. Il vantaggio proposto di tale funzionalità è la natura priva di allocazione. Tuttavia recenti indagini hanno trovato che non è possibile ottenere a causa di scarico dell'assemblaggio. Deve essere presente una `static delegate` maniglia forte dal al metodo a cui fa riferimento per evitare che l'assembly venga scaricato da sotto di esso.

Per mantenere `static delegate` ogni istanza sarebbe necessario allocare un nuovo handle che sia in contrasto con gli obiettivi della proposta. C'erano alcuni disegni in cui l'allocazione potrebbe essere ammortizzata a una singola allocazione per sito di chiamata, ma che era un po 'complesso e non sembrava vale la pena il trade off.

Ciò significa che gli sviluppatori devono essenzialmente decidere tra i seguenti compromessi:

1. Sicurezza di fronte allo scarico dell'assemblaggio: ciò richiede allocazioni e quindi `delegate` è già un'opzione sufficiente.
1. Nessuna sicurezza di fronte allo `delegate*`scarico dell'assemblaggio: utilizzare un . Questo può essere `struct` eseguito il `unsafe` wrapping in un per consentire l'utilizzo all'esterno di un contesto nel resto del codice.
