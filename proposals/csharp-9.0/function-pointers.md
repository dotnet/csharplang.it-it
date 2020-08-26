---
ms.openlocfilehash: 9a30d64b8df1913b77e5fb1d3ccb27f8a64c1460
ms.sourcegitcommit: 31289a8255732df5c0379a6d197142109b7afced
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88897607"
---
# <a name="function-pointers"></a>Puntatori a funzione

## <a name="summary"></a>Riepilogo

Questa proposta fornisce costrutti di linguaggio che espongono i codici operativi IL al quale attualmente non è possibile accedere in modo efficiente, o, in C#: `ldftn` e `calli` . Questi codici operativi IL può essere importante nel codice a prestazioni elevate e gli sviluppatori hanno bisogno di un modo efficiente per accedervi.

## <a name="motivation"></a>Motivazione

Le motivazioni e lo sfondo per questa funzionalità sono descritti nel seguente problema (come è una potenziale implementazione della funzionalità):

https://github.com/dotnet/csharplang/issues/191

Si tratta di una proposta di progettazione alternativa alle [funzioni intrinseche del compilatore](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)

## <a name="detailed-design"></a>Progettazione dettagliata

### <a name="function-pointers"></a>Puntatori funzione

Il linguaggio consentirà la dichiarazione di puntatori a funzione usando la `delegate*` sintassi. La sintassi completa viene descritta in dettaglio nella sezione successiva, ma è concepita come la sintassi utilizzata dalle dichiarazioni di `Func` `Action` tipo e.

``` csharp
unsafe class Example {
    void Example(Action<int> a, delegate*<int, void> f) {
        a(42);
        f(42);
    }
}
```

Questi tipi sono rappresentati usando il tipo di puntatore a funzione, come descritto in ECMA-335. Ciò significa che la chiamata di un oggetto utilizzerà la `delegate*` `calli` posizione in cui la chiamata di un oggetto `delegate` verrà utilizzata `callvirt` nel `Invoke` metodo.
Sintatticamente, sebbene la chiamata sia identica per entrambi i costrutti.

La definizione ECMA-335 dei puntatori al metodo include la convenzione di chiamata come parte della firma del tipo (sezione 7,1).
La convenzione di chiamata predefinita sarà `managed` . È possibile specificare le convenzioni di chiamata non gestite inserendo una `unmanaged` parola chiave dopo la `delegate*` sintassi, che utilizzerà l'impostazione predefinita della piattaforma di Runtime. È quindi possibile specificare convenzioni non gestite specifiche tra parentesi quadre per la `unmanaged` parola chiave specificando qualsiasi tipo che inizia con `CallConv` nello `System.Runtime.CompilerServices` spazio dei nomi, lasciando il `CallConv` prefisso. Questi tipi devono provenire dalla libreria principale del programma e il set di combinazioni valide è dipendente dalla piattaforma.

``` csharp
//This method has a managed calling convention. This is the same as leaving the managed keyword off.
delegate* managed<int, int>;

// This method will be invoked using whatever the default unmanaged calling convention on the runtime
// platform is. This is platform and architecture dependent and is determined by the CLR at runtime.
delegate* unmanaged<int, int>;

// This method will be invoked using the cdecl calling convention
// Cdecl maps to System.Runtime.CompilerServices.CallConvCdecl
delegate* unmanaged[Cdecl] <int, int>;

// This method will be invoked using the stdcall calling convention, and suppresses GC transition
// Stdcall maps to System.Runtime.CompilerServices.CallConvStdcall
// SuppressGCTransition maps to System.Runtime.CompilerServices.CallConvSuppressGCTransition
delegate* unmanaged[Stdcall, SuppressGCTransition] <int, int>;
```

Le conversioni tra `delegate*` i tipi vengono eseguite in base alla relativa firma, inclusa la convenzione di chiamata.

``` csharp
unsafe class Example {
    void Conversions() {
        delegate*<int, int, int> p1 = ...;
        delegate* managed<int, int, int> p2 = ...;
        delegate* unmanaged<int, int, int> p3 = ...;

        p1 = p2; // okay p1 and p2 have compatible signatures
        Console.WriteLine(p2 == p1); // True
        p2 = p3; // error: calling conventions are incompatible
    }
}
```

Un `delegate*` tipo è un tipo di puntatore che significa che ha tutte le funzionalità e le restrizioni di un tipo di puntatore standard:

- Valido solo in un `unsafe` contesto.
- I metodi che contengono un `delegate*` parametro o un tipo restituito possono essere chiamati solo da un `unsafe` contesto.
- Non può essere convertito in `object` .
- Non può essere usato come argomento generico.
- Può convertire in modo implicito `delegate*` in `void*` .
- Consente di eseguire la conversione esplicita da `void*` a `delegate*` .

Restrizioni:

- Gli attributi personalizzati non possono essere applicati a un oggetto o a uno `delegate*` dei relativi elementi.
- Un `delegate*` parametro non può essere contrassegnato come `params`
- Un `delegate*` tipo ha tutte le restrizioni di un tipo di puntatore normale.
- Non è possibile eseguire l'aritmetica del puntatore direttamente sui tipi di puntatore a funzione.

### <a name="function-pointer-syntax"></a>Sintassi del puntatore a funzione

La sintassi del puntatore a funzione completa è rappresentata dalla grammatica seguente:

```antlr
pointer_type
    : ...
    | funcptr_type
    ;

funcptr_type
    : 'delegate' '*' calling_convention_specifier? '<' funcptr_parameter_list funcptr_return_type '>'
    ;

calling_convention_specifier
    : 'managed'
    | 'unmanaged' ('[' unmanaged_calling_convention ']')?
    ;

unmanaged_calling_convention
    : 'Cdecl'
    | 'Stdcall'
    | 'Thiscall'
    | 'Fastcall'
    | identifier (',' identifier)*
    ;

funptr_parameter_list
    : (funcptr_parameter ',')*
    ;

funcptr_parameter
    : funcptr_parameter_modifier? type
    ;

funcptr_return_type
    : funcptr_return_modifier? return_type
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

Se non `calling_convention_specifier` viene specificato alcun valore, il valore predefinito è `managed` . La codifica dei metadati precisa di `calling_convention_specifier` e di quali `identifier` sono i validi in `unmanaged_calling_convention` viene analizzata nella [rappresentazione dei metadati delle convenzioni di chiamata](#metadata-representation-of-calling-conventions).

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
- Dal _ \_ tipo funcptr_ `F0` a un _altro \_ tipo funcptr_ `F1` , purché siano soddisfatte tutte le condizioni seguenti:
    - `F0` e `F1` hanno lo stesso numero di parametri e ogni parametro `D0n` in `F0` ha gli stessi `ref` `out` modificatori, o del `in` parametro corrispondente `D1n` in `F1` .
    - Per ogni parametro di valore (un parametro senza `ref` `out` `in` modificatore, o), esiste una conversione di identità, una conversione implicita di un riferimento o una conversione del puntatore implicita dal tipo di parametro in `F0` al tipo di parametro corrispondente in `F1` .
    - Per ogni `ref` `out` parametro, o `in` , il tipo di parametro in `F0` corrisponde al tipo di parametro corrispondente in `F1` .
    - Se il tipo restituito è per valore (No `ref` o `ref readonly` ), esiste un'identità, un riferimento implicito o una conversione del puntatore implicita dal tipo restituito di `F1` al tipo restituito di `F0` .
    - Se il tipo restituito è per riferimento ( `ref` o `ref readonly` ), il tipo restituito e i `ref` modificatori di `F1` sono gli stessi del tipo restituito e `ref` dei modificatori di `F0` .
    - La convenzione di chiamata di `F0` è uguale alla convenzione di chiamata di `F1` .

### <a name="allow-address-of-to-target-methods"></a>Consenti indirizzo-di ai metodi di destinazione

I gruppi di metodi saranno ora consentiti come argomenti per un'espressione address-of. Il tipo di tale espressione sarà un `delegate*` che ha la firma equivalente del metodo di destinazione e una convenzione di chiamata gestita:

``` csharp
unsafe class Util {
    public static void Log() { }

    void Use() {
        delegate*<void> ptr1 = &Util.Log;

        // Error: type "delegate*<void>" not compatible with "delegate*<int>";

   }
}
```

In un contesto non sicuro, un metodo `M` è compatibile con un tipo di puntatore a funzione `F` se si verificano tutte le condizioni seguenti:
- `M` e `F` hanno lo stesso numero di parametri e ogni parametro in `M` ha gli stessi `ref` `out` modificatori, o del `in` parametro corrispondente in `F` .
- Per ogni parametro di valore (un parametro senza `ref` `out` `in` modificatore, o), esiste una conversione di identità, una conversione implicita di un riferimento o una conversione del puntatore implicita dal tipo di parametro in `M` al tipo di parametro corrispondente in `F` .
- Per ogni `ref` `out` parametro, o `in` , il tipo di parametro in `M` corrisponde al tipo di parametro corrispondente in `F` .
- Se il tipo restituito è per valore (No `ref` o `ref readonly` ), esiste un'identità, un riferimento implicito o una conversione del puntatore implicita dal tipo restituito di `F` al tipo restituito di `M` .
- Se il tipo restituito è per riferimento ( `ref` o `ref readonly` ), il tipo restituito e i `ref` modificatori di `F` sono gli stessi del tipo restituito e `ref` dei modificatori di `M` .
- La convenzione di chiamata di `M` è uguale alla convenzione di chiamata di `F` . Sono inclusi sia il bit della convenzione di chiamata sia i flag della convenzione di chiamata specificati nell'identificatore non gestito.
- `M` è un metodo statico.

In un contesto non sicuro, esiste una conversione implicita da un'espressione address-of la cui destinazione è un gruppo `E` di metodi a un tipo di puntatore a funzione compatibile `F` se `E` contiene almeno un metodo applicabile nel formato normale a un elenco di argomenti costruito usando i tipi di parametro e i modificatori di `F` , come descritto di seguito.
- Viene selezionato un singolo metodo `M` corrispondente alla chiamata di un metodo del form `E(A)` con le modifiche seguenti:
   - L'elenco `A` degli argomenti è un elenco di espressioni, ciascuna classificata come variabile e con il tipo e il modificatore ( `ref` , `out` o `in` ) dell' _ \_ \_ elenco di parametri funcptr_ corrispondente di `F` .
   - I metodi candidati sono solo i metodi applicabili nel formato normale, non quelli applicabili nella forma espansa.
   - I metodi candidati sono solo i metodi statici.
- Se l'algoritmo di risoluzione dell'overload genera un errore, si verifica un errore in fase di compilazione. In caso contrario, l'algoritmo produce un unico metodo migliore `M` con lo stesso numero di parametri di `F` e la conversione viene considerata esistente.
- Il metodo selezionato `M` deve essere compatibile (come definito in precedenza) con il tipo di puntatore a funzione `F` . In caso contrario, si verifica un errore in fase di compilazione.
- Il risultato della conversione è un puntatore a funzione di tipo `F` .

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

L'operatore address-of verrà implementato utilizzando l' `ldftn` istruzione.

Limitazioni di questa funzionalità:

- Si applica solo ai metodi contrassegnati come `static` .
- Le `static` funzioni non locali non possono essere utilizzate in `&` . I dettagli di implementazione di questi metodi non sono deliberatamente specificati dal linguaggio. Ciò include la presenza di un'istanza statica rispetto a quella con cui vengono emesse le firme.


### <a name="operators-on-function-pointer-types"></a>Operatori sui tipi di puntatore a funzione

La sezione in codice unsafe sugli operatori viene modificata come segue:

> In un contesto non sicuro sono disponibili numerosi costrutti per operare su tutti i _pointer \_ type_s che non sono _funcptr \_ type_s:
>
> *  L' `*` operatore può essere utilizzato per eseguire un riferimento indiretto del puntatore ([riferimento indiretto](../../spec/unsafe-code.md#pointer-indirection)).
> *  L' `->` operatore può essere usato per accedere a un membro di uno struct tramite un puntatore ([accesso al membro del puntatore](../../spec/unsafe-code.md#pointer-member-access)).
> *  L' `[]` operatore può essere utilizzato per indicizzare un puntatore ([accesso all'elemento del puntatore](../../spec/unsafe-code.md#pointer-element-access)).
> *  L' `&` operatore può essere utilizzato per ottenere l'indirizzo di una variabile ([l'operatore address-of](../../spec/unsafe-code.md#the-address-of-operator)).
> *  Gli `++` `--` operatori e possono essere utilizzati per incrementare e decrementare i puntatori ([incremento e decremento del puntatore](../../spec/unsafe-code.md#pointer-increment-and-decrement)).
> *  Gli `+` `-` operatori e possono essere utilizzati per eseguire l'aritmetica dei puntatori ([aritmetica del puntatore](../../spec/unsafe-code.md#pointer-arithmetic)).
> *  Gli `==` `!=` operatori,, `<` ,, `>` `<=` e `=>` possono essere usati per confrontare i puntatori ([confronto tra](../../spec/unsafe-code.md#pointer-comparison)puntatori).
> *  L' `stackalloc` operatore può essere utilizzato per allocare memoria dallo stack di chiamate ([buffer a dimensione fissa](../../spec/unsafe-code.md#fixed-size-buffers)).
> *  L' `fixed` istruzione può essere utilizzata per correggere temporaneamente una variabile in modo che sia possibile ottenere l'indirizzo ([istruzione fixed](../../spec/unsafe-code.md#the-fixed-statement)).
> 
> In un contesto non sicuro sono disponibili numerosi costrutti per operare su tutti i _funcptr \_ type_s:
> *  L' `&` operatore può essere utilizzato per ottenere l'indirizzo dei metodi statici ([Consenti indirizzo-di ai metodi di destinazione](#allow-address-of-to-target-methods))
> *  Gli `==` `!=` operatori,, `<` ,, `>` `<=` e `=>` possono essere usati per confrontare i puntatori ([confronto tra](../../spec/unsafe-code.md#pointer-comparison)puntatori).

Inoltre, si modificano tutte le sezioni in `Pointers in expressions` per impedire tipi di puntatore a funzione, eccetto `Pointer comparison` e `The sizeof operator` .

### <a name="better-function-member"></a>Membro di funzione migliore

La specifica del membro di funzione migliore verrà modificata in modo da includere la riga seguente:

> Un `delegate*` è più specifico di `void*`

Ciò significa che è possibile eseguire l'overload su `void*` e un oggetto `delegate*` e continuare a usare l'operatore address-of.

## <a name="metadata-representation-of-in-out-and-ref-readonly-parameters-and-return-types"></a>Rappresentazione dei metadati `in` di `out` parametri, e `ref readonly` e tipi restituiti

Le firme del puntatore a funzione non hanno un percorso di flag di parametro, pertanto è necessario codificare se i parametri e il tipo restituito sono `in` , `out` o `ref readonly` usando modreqs.

### `in`

Riutilizziamo `System.Runtime.InteropServices.InAttribute` , applicato come oggetto `modreq` all'identificatore Ref su un parametro o un tipo restituito, per indicare quanto segue:
* Se applicato a un identificatore di riferimento del parametro, questo parametro viene trattato come `in` .
* Se applicato all'identificatore Ref del tipo restituito, il tipo restituito viene considerato come `ref readonly` .

### `out`

Si usa `System.Runtime.InteropServices.OutAttribute` , applicato come oggetto `modreq` all'identificatore Ref su un tipo di parametro, per indicare che il parametro è un `out` parametro.

### <a name="errors"></a>Errors

* Si tratta di un errore da applicare `OutAttribute` come modreq a un tipo restituito.
* È un errore applicare sia che `InAttribute` `OutAttribute` come modreq a un tipo di parametro.
* Se vengono specificati tramite modopt, verranno ignorati.

### <a name="metadata-representation-of-calling-conventions"></a>Rappresentazione dei metadati delle convenzioni di chiamata

Le convenzioni di chiamata vengono codificate nella firma di un metodo nei metadati mediante una combinazione del `CallKind` flag nella firma e zero o più `modopt` all'inizio della firma. ECMA-335 dichiara attualmente gli elementi seguenti nel `CallKind` flag:

```antlr
CallKind
   : default
   | unmanaged cdecl
   | unmanaged fastcall
   | unmanaged thiscall
   | unmanaged stdcall
   | varargs
   ;
```

Di questi, i puntatori a funzione in C# supporteranno tutti i, tranne `varargs` .

Inoltre, il runtime (e infine 335) verrà aggiornato in modo da includere un nuovo `CallKind` nelle nuove piattaforme. Attualmente non è presente un nome formale, ma questo documento utilizzerà `unmanaged ext` come segnaposto per il nuovo formato della convenzione di chiamata estendibile. Con No `modopt` s, `unmanaged ext` è la convenzione di chiamata predefinita della piattaforma, `unmanaged` senza le parentesi quadre.

#### <a name="mapping-the-calling_convention_specifier-to-a-callkind"></a>Mapping `calling_convention_specifier` di a un `CallKind`

Un oggetto `calling_convention_specifier` che viene omesso o specificato come `managed` , viene mappato a `default` `CallKind` . Si tratta del valore predefinito `CallKind` di qualsiasi metodo senza attributi `UnmanagedCallersOnly` .

C# riconosce 4 identificatori speciali che vengono mappati a specifiche non gestite esistenti `CallKind` da ECMA 335. Affinché questo mapping venga eseguito, è necessario specificare i relativi identificatori in modo autonomo, senza altri identificatori, e questo requisito viene codificato nelle specifiche per `unmanaged_calling_convention` s. Questi identificatori sono `Cdecl` , `Thiscall` , `Stdcall` e `Fastcall` , che corrispondono rispettivamente a `unmanaged cdecl` , `unmanaged thiscall` , `unmanaged stdcall` e `unmanaged fastcall` . Se viene specificato più di un `identifer` oggetto o se il singolo `identifier` non è costituito dagli identificatori specifici, viene eseguita la ricerca di nomi speciali sull'identificatore con le regole seguenti:

* Viene anteposto l'oggetto `identifier` con la stringa `CallConv`
* Si osserveranno solo i tipi definiti nello `System.Runtime.CompilerServices` spazio dei nomi.
* Si osserveranno solo i tipi definiti nella libreria principale dell'applicazione, ovvero la libreria che definisce `System.Object` e non ha dipendenze.
* Vengono esaminati solo i tipi pubblici.

Se la ricerca ha esito positivo su tutti gli oggetti `identifier` specificati in un oggetto `unmanaged_calling_convention` , viene codificato `CallKind` come `unmanaged ext` e vengono codificati tutti i tipi risolti nel set di oggetti `modopt` all'inizio della firma del puntatore a funzione. Si noti che queste regole indicano che gli utenti non possono precedere questi `identifier` con `CallConv` , perché ciò comporta la ricerca `CallConvCallConvVectorCall` .

Quando si interpretano i metadati, si esamina prima di tutto `CallKind` . Se si tratta di un valore diverso da `unmanaged ext` , si ignoreranno tutti i `modopt` nel tipo restituito ai fini della determinazione della convenzione di chiamata e si utilizzerà solo `CallKind` . Se `CallKind` è `unmanaged ext` , si esamina il modopts all'inizio del tipo di puntatore a funzione, prendendo l'Unione di tutti i tipi che soddisfano i requisiti seguenti:

* L'oggetto è definito nella libreria principale, ovvero la libreria che fa riferimento ad altre librerie e non definisce `System.Object` .
* Il tipo è definito nello `System.Runtime.CompilerServices` spazio dei nomi.
* Il tipo inizia con il prefisso `CallConv` .
* Il tipo è public.

 Rappresentano i tipi che devono essere trovati quando si esegue la ricerca in `identifier` un oggetto `unmanaged_calling_convention` quando si definisce un tipo di puntatore a funzione nell'origine.

Non è possibile tentare di usare un puntatore a funzione con un valore `CallKind` di `unmanaged ext` se il runtime di destinazione non supporta la funzionalità. Questa operazione verrà determinata cercando la presenza della `System.Runtime.CompilerServices.RuntimeFeature.UnmanagedCallKind` costante. Se questa costante è presente, il runtime viene considerato come supporto della funzionalità.

### `System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute`

`System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute` attributo utilizzato da CLR per indicare che un metodo deve essere chiamato con una convenzione di chiamata specifica. Per questo motivo, viene introdotto il supporto seguente per l'utilizzo dell'attributo:

* È un errore chiamare direttamente un metodo annotato con questo attributo da C#. Gli utenti devono ottenere un puntatore a funzione per il metodo e quindi richiamare il puntatore.
* Non è possibile applicare l'attributo a qualsiasi elemento diverso da un metodo statico comune o da una normale funzione locale statica.
Il compilatore C# contrassegnerà tutti i metodi non statici o statici non ordinari importati dai metadati con questo attributo come non supportati dal linguaggio.
* È un errore per un metodo contrassegnato con l'attributo per avere un parametro o un tipo restituito diverso da `unmanaged_type` .
* Si tratta di un errore per un metodo contrassegnato con l'attributo con parametri di tipo, anche se tali parametri di tipo sono limitati a `unmanaged` .
* Non è possibile contrassegnare un metodo in un tipo generico con l'attributo.
* È un errore convertire un metodo contrassegnato con l'attributo in un tipo delegato.
* Non è un errore specificare i tipi per `UnmanagedCallersOnly.CallConvs` che non soddisfano i requisiti per la chiamata di Convention `modopt` s nei metadati.

Quando si determina la convenzione di chiamata di un metodo contrassegnato con un `UnmanagedCallersOnly` attributo valido, il compilatore esegue i controlli seguenti sui tipi specificati nella `CallConvs` proprietà per determinare gli effettivi `CallKind` e `modopt` che devono essere utilizzati per determinare la convenzione di chiamata:

* Se non viene specificato alcun tipo, `CallKind` viene considerato come `unmanaged ext` , senza alcuna convenzione `modopt` di chiamata all'inizio del tipo di puntatore a funzione.
* Se è stato specificato un tipo e tale tipo è denominato `CallConvCdecl` ,, `CallConvThiscall` `CallConvStdcall` o `CallConvFastcall` , l'oggetto `CallKind` viene trattato come,, `unmanaged cdecl` `unmanaged thiscall` `unmanaged stdcall` o `unmanaged fastcall` , rispettivamente, senza convenzione di chiamata `modopt` all'inizio del tipo di puntatore a funzione.
* Se vengono specificati più tipi o se il tipo singolo non è denominato uno dei tipi indicati in precedenza, `CallKind` viene considerato come `unmanaged ext` , con l'Unione dei tipi specificati come `modopt` s all'inizio del tipo di puntatore a funzione.

Il compilatore esamina quindi questo effetto `CallKind` e la `modopt` raccolta e usa le normali regole di metadati per determinare la convenzione di chiamata finale del tipo di puntatore a funzione.

## <a name="open-questions"></a>Domande aperte

### <a name="detecting-runtime-support-for-unmanaged-ext"></a>Rilevamento del supporto runtime per `unmanaged ext`

https://github.com/dotnet/runtime/issues/38135 tiene traccia dell'aggiunta di questo flag. A seconda dei commenti e suggerimenti della revisione, si utilizzerà la proprietà specificata nel problema oppure si utilizzerà la presenza di `UnmanagedCallersOnlyAttribute` come flag che determina se i runtime supportano `unmanaged ext` .

## <a name="considerations"></a>Considerazioni

### <a name="allow-instance-methods"></a>Consenti metodi di istanza

È possibile estendere la proposta per supportare i metodi di istanza sfruttando la convenzione di chiamata dell'interfaccia della riga di comando `EXPLICITTHIS` (denominata `instance` nel codice C#). Questa forma di puntatori a funzione dell'interfaccia della riga di comando inserisce il `this` parametro come primo parametro esplicito della sintassi del puntatore a funzione.

``` csharp
unsafe class Instance {
    void Use() {
        delegate* instance<Instance, string> f = &ToString;
        f(this);
    }
}
```

Si tratta di un suono che aggiunge alcune complicazioni alla proposta. In particolare perché i puntatori a funzione che differiscono dalla convenzione di chiamata `instance` e `managed` sarebbero incompatibili anche se entrambi i casi vengono usati per richiamare i metodi gestiti con la stessa firma C#. Inoltre, in ogni caso, si considera utile la presenza di una semplice soluzione: usare una `static` funzione locale.

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

Anziché richiedere `unsafe` a ogni utilizzo di un `delegate*` , richiederlo solo nel punto in cui un gruppo di metodi viene convertito in un oggetto `delegate*` . Questo è il punto in cui vengono rilevati i problemi di sicurezza di base (sapendo che l'assembly contenitore non può essere scaricato mentre il valore è attivo). La richiesta `unsafe` di altre posizioni può essere considerata eccessiva.

Questo è il modo in cui la progettazione è stata originariamente progettata. Ma le regole del linguaggio risultavano molto scomode. Non è possibile nascondere il fatto che si tratta di un valore di puntatore che continua a leggere anche senza la `unsafe` parola chiave. Ad esempio, la conversione a `object` non può essere consentita, non può essere un membro di `class` , e così via. Il progetto C# è necessario `unsafe` per tutti gli utilizzi del puntatore e pertanto questa progettazione lo segue.

Gli sviluppatori saranno comunque in grado di presentare un wrapper _sicuro_ sopra i `delegate*` valori nello stesso modo in cui fanno oggi i normali tipi di puntatore. Tenere in considerazione:

``` csharp
unsafe struct Action {
    delegate*<void> _ptr;

    Action(delegate*<void> ptr) => _ptr = ptr;
    public void Invoke() => _ptr();
}
```

### <a name="using-delegates"></a>Uso di delegati

Invece di usare un nuovo elemento della sintassi, `delegate*` , è sufficiente usare `delegate` i tipi esistenti con `*` il tipo seguente:

``` csharp
Func<object, object, bool>* ptr = &object.ReferenceEquals;
```

La gestione della convenzione di chiamata può essere eseguita annotando i `delegate` tipi con un attributo che specifica un `CallingConvention` valore. La mancanza di un attributo significa la convenzione di chiamata gestita.

La codifica in IL è problematica. Il valore sottostante deve essere rappresentato come puntatore, ma deve anche:

1. Hanno un tipo univoco per consentire gli overload con tipi di puntatore a funzione diversi.
1. Essere equivalente a scopi OHI tra i limiti di assembly.

L'ultimo punto è particolarmente problematico. Ciò significa che ogni assembly utilizzato da `Func<int>*` deve codificare un tipo equivalente nei metadati anche se `Func<int>*` è definito in un assembly, sebbene non controlli.
Inoltre, tutti gli altri tipi definiti con il nome `System.Func<T>` in un assembly che non è mscorlib devono essere diversi dalla versione definita in mscorlib.

Una delle opzioni Esplorate è stata la creazione di un puntatore di questo tipo come `mod_req(Func<int>) void*` . Questa operazione non funziona anche se `mod_req` non è possibile eseguire l'associazione a un oggetto `TypeSpec` e pertanto non è possibile fare riferimento a istanze generiche.

### <a name="named-function-pointers"></a>Puntatori a funzioni denominate

La sintassi del puntatore a funzione può essere complessa, soprattutto in casi complessi come i puntatori a funzioni annidate. Anziché fare in modo che gli sviluppatori digitino la firma ogni volta che la lingua potrebbe consentire le dichiarazioni denominate dei puntatori a funzione, come avviene con `delegate` .

``` csharp
func* void Action();

unsafe class NamedExample {
    void M(Action a) {
        a();
    }
}
```

Una parte del problema è che la primitiva dell'interfaccia della riga di comando sottostante non ha nomi, pertanto si tratterebbe puramente di un'invenzione in C# e richiederebbe un po' di lavoro di metadati da abilitare. Questa operazione è fattibile, ma è una questione significativa di lavoro. In sostanza, è necessario che C# disponga di un complementare alla tabella def del tipo esclusivamente per questi nomi.

Inoltre, quando sono stati esaminati gli argomenti per i puntatori a funzione denominati, è possibile che si applichino ugualmente a diversi altri scenari. Ad esempio, sarebbe altrettanto semplice dichiarare le tuple denominate per ridurre la necessità di digitare la firma completa in tutti i casi.

``` csharp
(int x, int y) Point;

class NamedTupleExample {
    void M(Point p) {
        Console.WriteLine(p.x);
    }
}
```

Dopo la discussione si è deciso di non consentire la dichiarazione denominata dei `delegate*` tipi. Se viene rilevata una necessità significativa in base ai commenti e suggerimenti sull'utilizzo dei clienti, viene esaminata una soluzione di denominazione che funziona per i puntatori a funzione, le tuple, i generics e così via. È probabile che questo aspetto sia simile a quello di altri suggerimenti `typedef` , come il supporto completo nel linguaggio.

## <a name="future-considerations"></a>Considerazioni

### <a name="static-delegates"></a>delegati statici

Si fa riferimento alla [proposta](https://github.com/dotnet/csharplang/issues/302) per consentire la dichiarazione di `delegate` tipi che possono fare riferimento solo ai `static` membri. Il vantaggio è che tali `delegate` istanze possono essere allocazione gratuite e migliori negli scenari sensibili alle prestazioni.

Se la funzionalità del puntatore a funzione è implementata `static delegate` , la proposta verrà probabilmente chiusa. Il vantaggio proposto da questa funzionalità è la natura di allocazione. Sono state rilevate tuttavia ricerche recenti che non è possibile ottenere a causa dello scaricamento di assembly. È necessario che sia presente un handle sicuro da `static delegate` al metodo a cui si riferisce per evitare che l'assembly venga scaricato dal sottoinsieme.

Per mantenere ogni `static delegate` istanza è necessario allocare un nuovo handle che esegue il contatore agli obiettivi della proposta. Sono stati creati alcuni progetti in cui l'allocazione può essere ammortizzata in una singola allocazione per ogni chiamata, ma si tratta di un po' complesso che non sembra essere il compromesso.

Ciò significa che gli sviluppatori devono essenzialmente decidere tra i compromessi seguenti:

1. Sicurezza in caso di scaricamento di assembly: è necessario allocazioni e pertanto `delegate` è già un'opzione sufficiente.
1. Nessun aspetto di sicurezza in caso di scaricamento dell'assembly: usare un `delegate*` . Questo può essere incluso in un oggetto `struct` per consentire l'utilizzo all'esterno `unsafe` di un contesto nel resto del codice.
