---
ms.openlocfilehash: ac4c8760e3b6a0934f01ae634f666af60aa1c0fe
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484566"
---
# <a name="compiler-intrinsics"></a>Intrinseci del compilatore

## <a name="summary"></a>Summary

Questa proposta fornisce costrutti di linguaggio che espongono i codici operativi di basso livello a cui attualmente non è possibile accedere in modo efficiente o: `ldftn`, `ldvirtftn`, `ldtoken` e `calli`. Questi codici operativi di basso livello possono essere importanti nel codice a prestazioni elevate e gli sviluppatori hanno bisogno di un modo efficiente per accedervi.

## <a name="motivation"></a>Motivazione

Le motivazioni e lo sfondo per questa funzionalità sono descritti nel seguente problema (come è una potenziale implementazione della funzionalità): 

https://github.com/dotnet/csharplang/issues/191

Questa proposta di progettazione alternativa è riguardante la revisione di un'implementazione del prototipo della proposta originale da parte di @msjabby e l'uso in una codebase significativa. Questa progettazione è stata eseguita con un input significativo da @mjsabby, @tmat e @jkotas.

## <a name="detailed-design"></a>Progettazione dettagliata 

### <a name="allow-address-of-to-target-methods"></a>Consenti indirizzo di ai metodi di destinazione

I gruppi di metodi saranno ora consentiti come argomenti per un'espressione address-of. Il tipo di tale espressione sarà `void*`. 

``` csharp
class Util { 
    public static void Log() { } 
}

// ldftn Util.Log
void* ptr = &Util.Log; 
```

Dato che non esiste alcuna conversione del delegato, l'unico meccanismo per filtrare i membri nel gruppo di metodi è l'accesso statico/istanza. Se non è in grado di distinguere i membri, si verificherà un errore in fase di compilazione.

``` csharp
class Util { 
    public void Log() { } 
    public void Log(string p1) { } 
    public static void Log(int i) { };
}

unsafe {
    // Error: Method group Log has more than one applicable candidate.
    void* ptr1 = &Log; 

    // Okay: only one static member to consider here.
    void* ptr2 = &Util.Log;
}
```

L'espressione AddressOf in questo contesto verrà implementata nel modo seguente:

- ldftn: quando il metodo non è virtuale.
- ldvirtftn: quando il metodo è virtuale.

Limitazioni di questa funzionalità:

- I metodi di istanza possono essere specificati solo quando si usa un'espressione di chiamata su un valore
- Impossibile utilizzare le funzioni locali in `&`. I dettagli di implementazione di questi metodi non sono deliberatamente specificati dal linguaggio. Ciò include la presenza di un'istanza statica rispetto a quella con cui vengono emesse le firme.

### <a name="handleof"></a>handleof

La parola chiave contestuale `handleof` convertirà un campo, un membro o un tipo nel `RuntimeHandle` tipo equivalente usando l'istruzione `ldtoken`. Il tipo esatto dell'espressione dipenderà dal tipo di nome in `handleof`:

- campo: `RuntimeFieldHandle`
- Tipo: `RuntimeTypeHandle`
- Metodo: `RuntimeMethodHandle`

Gli argomenti da `handleof` sono identici a `nameof`. Deve essere un nome semplice, un nome qualificato, un accesso ai membri, un accesso di base con un membro specificato oppure questo accesso con un membro specificato. L'espressione dell'argomento identifica una definizione di codice, ma non viene mai valutata.

L'espressione `handleof` viene valutata in fase di esecuzione e ha un tipo restituito di `RuntimeHandle`. Questa operazione può essere eseguita in codice sicuro e non sicuro. 

``` 
RuntimeHandle stringHandle = handleof(string);
```

Limitazioni di questa funzionalità:

- Impossibile utilizzare le proprietà in un'espressione `handleof`.
- Non è possibile usare l'espressione `handleof` se è presente un nome di `handleof` esistente nell'ambito. Ad esempio un tipo, uno spazio dei nomi e così via...

### <a name="calli"></a>calli

Il compilatore aggiungerà il supporto per un nuovo tipo di funzione `extern` che si traduce in modo efficiente in un'istruzione `.calli`. L'attributo extern verrà contrassegnato con un attributo con la forma seguente:

``` csharp
[AttributeUsage(AttributeTargets.Method)]
public sealed class CallIndirectAttribute : Attribute
{
    public CallingConvention CallingConvention { get; }
    public CallIndirectAttribute(CallingConvention callingConvention)
    {
        CallingConvention = callingConvention;
    }
}
```

Ciò consente agli sviluppatori di definire metodi nel formato seguente:

``` csharp
[CallIndirect(CallingConvention.Cdecl)]
static extern int MapValue(string s, void *ptr);

unsafe {
    var i = MapValue("42", &int.Parse);
    Console.WriteLine(i);
}
```

Restrizioni relative al metodo a cui è applicato l'attributo `CallIndirect`:

- Non è possibile avere un attributo `DllImport`.
- Non può essere generico.

## <a name="open-issues"></a>Problemi aperti

### <a name="callingconvention"></a>CallingConvention

Il `CallIndirectAttribute` come progettato utilizza l'enumerazione `CallingConvention`, che non dispone di una voce per le convenzioni di chiamata gestite. L'enumerazione deve essere estesa per includere questa convenzione di chiamata o l'attributo deve adottare un approccio diverso.

## <a name="considerations"></a>Considerazioni

### <a name="disambiguating-method-groups"></a>Gruppi di metodi distinguere

Sono state riportate alcune informazioni sulle funzionalità che semplificano la risoluzione delle ambiguità dei gruppi di metodi passati a un'espressione address-of. Ad esempio, aggiungendo potenzialmente elementi Signature alla sintassi:

``` csharp
class Util {
    public static void Log() { ... }
    public static void Log(string) { ... }
}

unsafe {
    // Error: ambiguous Log
    void *ptr1 = &Util.Log;

    // Use Util.Log();
    void *ptr2 = &Util.Log();
}
```

Questa operazione è stata rifiutata perché non è stato possibile eseguire un caso interessante né è possibile che venga fornita una sintassi semplice. Esiste anche una soluzione piuttosto semplice: definire un altro metodo che non è ambiguo e USA C# il codice per chiamare la funzione desiderata. 

``` csharp
class Workaround {
    public static void LocalLog() => Util.Log();
}
unsafe { 
    void* ptr = &Workaround.LocalLog;
}
```

Questa operazione diventa ancora più semplice se `static` funzioni locali entrano nella lingua. Quindi, potrebbe essere definita nella stessa funzione che ha utilizzato l'operazione ambigua address-of:

``` csharp
unsafe { 
    static void LocalLog() => Util.Log();
    void* ptr = &Workaround.LocalLog;
}
```

### <a name="loadtypetokenint32"></a>LoadTypeTokenInt32

La proposta originale consentiva di caricare i token dei metadati come `int` valori in fase di compilazione. Hanno essenzialmente `tokenof` con gli stessi argomenti di `handleof` ma viene valutato in fase di compilazione a una costante `int`. 

Questa operazione è stata rifiutata perché causa un problema significativo per le riscritture IL (di cui .NET dispone di molti). Tali riscrittori spesso manipolano le tabelle di metadati in modo da invalidare tali valori. Non esiste un modo ragionevole per i rewriter di aggiornare questi valori quando vengono archiviati come semplici valori `int`.

L'idea sottostante di avere un handle opaco per le voci di metadati continuerà a essere esplorata dal team di Runtime. 

## <a name="future-considerations"></a>Considerazioni

### <a name="static-local-functions"></a>funzioni locali statiche

Si fa riferimento alla [proposta](https://github.com/dotnet/csharplang/issues/1565) per consentire il modificatore `static` sulle funzioni locali. Una funzione di questo tipo verrebbe generata come `static` e con la firma esatta specificata nel codice sorgente. Una funzione di questo tipo deve essere un argomento valido per `&` perché non contiene alcun problema attualmente presente nelle funzioni locali.

### <a name="nativecallableattribute"></a>NativeCallableAttribute

CLR dispone di una funzionalità che consente di emettere metodi gestiti in modo che possano essere richiamati direttamente dal codice nativo. Questa operazione viene eseguita aggiungendo il `NativeCallableAttribute` ai metodi. Tale metodo può essere chiamato solo dal codice nativo e quindi deve contenere solo tipi copiabili nella firma. La chiamata dal codice gestito genera un errore di Runtime. 

Questa funzionalità può essere adattata a questa proposta in modo analogo al seguente:

- Passaggio di una funzione definita nel codice gestito al codice nativo come puntatore a funzione (via address-of) senza overhead nel codice gestito o nativo. 
- Il runtime può introdurre errori di utilizzo del sito per tali funzioni nel codice gestito per impedire che vengano richiamate in fase di compilazione.




