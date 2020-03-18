---
ms.openlocfilehash: ac4c8760e3b6a0934f01ae634f666af60aa1c0fe
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484566"
---
# <a name="compiler-intrinsics"></a><span data-ttu-id="0fd46-101">Intrinseci del compilatore</span><span class="sxs-lookup"><span data-stu-id="0fd46-101">Compiler Intrinsics</span></span>

## <a name="summary"></a><span data-ttu-id="0fd46-102">Summary</span><span class="sxs-lookup"><span data-stu-id="0fd46-102">Summary</span></span>

<span data-ttu-id="0fd46-103">Questa proposta fornisce costrutti di linguaggio che espongono i codici operativi di basso livello a cui attualmente non è possibile accedere in modo efficiente o: `ldftn`, `ldvirtftn`, `ldtoken` e `calli`.</span><span class="sxs-lookup"><span data-stu-id="0fd46-103">This proposal provides language constructs that expose low level IL opcodes that cannot currently be accessed efficiently, or at all: `ldftn`, `ldvirtftn`, `ldtoken` and `calli`.</span></span> <span data-ttu-id="0fd46-104">Questi codici operativi di basso livello possono essere importanti nel codice a prestazioni elevate e gli sviluppatori hanno bisogno di un modo efficiente per accedervi.</span><span class="sxs-lookup"><span data-stu-id="0fd46-104">These low level opcodes can be important in high performance code and developers need an efficient way to access them.</span></span>

## <a name="motivation"></a><span data-ttu-id="0fd46-105">Motivazione</span><span class="sxs-lookup"><span data-stu-id="0fd46-105">Motivation</span></span>

<span data-ttu-id="0fd46-106">Le motivazioni e lo sfondo per questa funzionalità sono descritti nel seguente problema (come è una potenziale implementazione della funzionalità):</span><span class="sxs-lookup"><span data-stu-id="0fd46-106">The motivations and background for this feature are described in the following issue (as is a potential implementation of the feature):</span></span> 

https://github.com/dotnet/csharplang/issues/191

<span data-ttu-id="0fd46-107">Questa proposta di progettazione alternativa è riguardante la revisione di un'implementazione del prototipo della proposta originale da parte di @msjabby e l'uso in una codebase significativa.</span><span class="sxs-lookup"><span data-stu-id="0fd46-107">This alternate design proposal comes after reviewing a prototype implementation of the original proposal by @msjabby as well as the use throughout a significant code base.</span></span> <span data-ttu-id="0fd46-108">Questa progettazione è stata eseguita con un input significativo da @mjsabby, @tmat e @jkotas.</span><span class="sxs-lookup"><span data-stu-id="0fd46-108">This design was done with significant input from @mjsabby, @tmat and @jkotas.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="0fd46-109">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="0fd46-109">Detailed Design</span></span> 

### <a name="allow-address-of-to-target-methods"></a><span data-ttu-id="0fd46-110">Consenti indirizzo di ai metodi di destinazione</span><span class="sxs-lookup"><span data-stu-id="0fd46-110">Allow address of to target methods</span></span>

<span data-ttu-id="0fd46-111">I gruppi di metodi saranno ora consentiti come argomenti per un'espressione address-of.</span><span class="sxs-lookup"><span data-stu-id="0fd46-111">Method groups will now be allowed as arguments to an address-of expression.</span></span> <span data-ttu-id="0fd46-112">Il tipo di tale espressione sarà `void*`.</span><span class="sxs-lookup"><span data-stu-id="0fd46-112">The type of such an expression will be `void*`.</span></span> 

``` csharp
class Util { 
    public static void Log() { } 
}

// ldftn Util.Log
void* ptr = &Util.Log; 
```

<span data-ttu-id="0fd46-113">Dato che non esiste alcuna conversione del delegato, l'unico meccanismo per filtrare i membri nel gruppo di metodi è l'accesso statico/istanza.</span><span class="sxs-lookup"><span data-stu-id="0fd46-113">Given there is no delegate conversion here the only mechanism for filtering members in the method group is by static / instance access.</span></span> <span data-ttu-id="0fd46-114">Se non è in grado di distinguere i membri, si verificherà un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="0fd46-114">If that cannot distinguish the members then a compile time error will occur.</span></span>

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

<span data-ttu-id="0fd46-115">L'espressione AddressOf in questo contesto verrà implementata nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="0fd46-115">The addressof expression in this context will be implemented in the following manner:</span></span>

- <span data-ttu-id="0fd46-116">ldftn: quando il metodo non è virtuale.</span><span class="sxs-lookup"><span data-stu-id="0fd46-116">ldftn: when the method is non-virtual.</span></span>
- <span data-ttu-id="0fd46-117">ldvirtftn: quando il metodo è virtuale.</span><span class="sxs-lookup"><span data-stu-id="0fd46-117">ldvirtftn: when the method is virtual.</span></span>

<span data-ttu-id="0fd46-118">Limitazioni di questa funzionalità:</span><span class="sxs-lookup"><span data-stu-id="0fd46-118">Restrictions of this feature:</span></span>

- <span data-ttu-id="0fd46-119">I metodi di istanza possono essere specificati solo quando si usa un'espressione di chiamata su un valore</span><span class="sxs-lookup"><span data-stu-id="0fd46-119">Instance methods can only be specified when using an invocation expression on a value</span></span>
- <span data-ttu-id="0fd46-120">Impossibile utilizzare le funzioni locali in `&`.</span><span class="sxs-lookup"><span data-stu-id="0fd46-120">Local functions cannot be used in `&`.</span></span> <span data-ttu-id="0fd46-121">I dettagli di implementazione di questi metodi non sono deliberatamente specificati dal linguaggio.</span><span class="sxs-lookup"><span data-stu-id="0fd46-121">The implementation details of these methods are deliberately not specified by the language.</span></span> <span data-ttu-id="0fd46-122">Ciò include la presenza di un'istanza statica rispetto a quella con cui vengono emesse le firme.</span><span class="sxs-lookup"><span data-stu-id="0fd46-122">This includes whether they are static vs. instance or exactly what signature they are emitted with.</span></span>

### <a name="handleof"></a><span data-ttu-id="0fd46-123">handleof</span><span class="sxs-lookup"><span data-stu-id="0fd46-123">handleof</span></span>

<span data-ttu-id="0fd46-124">La parola chiave contestuale `handleof` convertirà un campo, un membro o un tipo nel `RuntimeHandle` tipo equivalente usando l'istruzione `ldtoken`.</span><span class="sxs-lookup"><span data-stu-id="0fd46-124">The `handleof` contextual keyword will translate a field, member or type into their equivalent `RuntimeHandle` type using the `ldtoken` instruction.</span></span> <span data-ttu-id="0fd46-125">Il tipo esatto dell'espressione dipenderà dal tipo di nome in `handleof`:</span><span class="sxs-lookup"><span data-stu-id="0fd46-125">The exact type of the expression will depend on the kind of the name in `handleof`:</span></span>

- <span data-ttu-id="0fd46-126">campo: `RuntimeFieldHandle`</span><span class="sxs-lookup"><span data-stu-id="0fd46-126">field: `RuntimeFieldHandle`</span></span>
- <span data-ttu-id="0fd46-127">Tipo: `RuntimeTypeHandle`</span><span class="sxs-lookup"><span data-stu-id="0fd46-127">type: `RuntimeTypeHandle`</span></span>
- <span data-ttu-id="0fd46-128">Metodo: `RuntimeMethodHandle`</span><span class="sxs-lookup"><span data-stu-id="0fd46-128">method: `RuntimeMethodHandle`</span></span>

<span data-ttu-id="0fd46-129">Gli argomenti da `handleof` sono identici a `nameof`.</span><span class="sxs-lookup"><span data-stu-id="0fd46-129">The arguments to `handleof` are identical to `nameof`.</span></span> <span data-ttu-id="0fd46-130">Deve essere un nome semplice, un nome qualificato, un accesso ai membri, un accesso di base con un membro specificato oppure questo accesso con un membro specificato.</span><span class="sxs-lookup"><span data-stu-id="0fd46-130">It must be a simple name, qualified name, member access, base access with a specified member, or this access with a specified member.</span></span> <span data-ttu-id="0fd46-131">L'espressione dell'argomento identifica una definizione di codice, ma non viene mai valutata.</span><span class="sxs-lookup"><span data-stu-id="0fd46-131">The argument expression identifies a code definition, but it is never evaluated.</span></span>

<span data-ttu-id="0fd46-132">L'espressione `handleof` viene valutata in fase di esecuzione e ha un tipo restituito di `RuntimeHandle`.</span><span class="sxs-lookup"><span data-stu-id="0fd46-132">The `handleof` expression is evaluated at runtime and has a return type of `RuntimeHandle`.</span></span> <span data-ttu-id="0fd46-133">Questa operazione può essere eseguita in codice sicuro e non sicuro.</span><span class="sxs-lookup"><span data-stu-id="0fd46-133">This can be executed in safe code as well as unsafe.</span></span> 

``` 
RuntimeHandle stringHandle = handleof(string);
```

<span data-ttu-id="0fd46-134">Limitazioni di questa funzionalità:</span><span class="sxs-lookup"><span data-stu-id="0fd46-134">Restrictions of this feature:</span></span>

- <span data-ttu-id="0fd46-135">Impossibile utilizzare le proprietà in un'espressione `handleof`.</span><span class="sxs-lookup"><span data-stu-id="0fd46-135">Properties cannot be used in a `handleof` expression.</span></span>
- <span data-ttu-id="0fd46-136">Non è possibile usare l'espressione `handleof` se è presente un nome di `handleof` esistente nell'ambito.</span><span class="sxs-lookup"><span data-stu-id="0fd46-136">The `handleof` expression cannot be used when there is an existing `handleof` name in scope.</span></span> <span data-ttu-id="0fd46-137">Ad esempio un tipo, uno spazio dei nomi e così via...</span><span class="sxs-lookup"><span data-stu-id="0fd46-137">For example a type, namespace, etc ...</span></span>

### <a name="calli"></a><span data-ttu-id="0fd46-138">calli</span><span class="sxs-lookup"><span data-stu-id="0fd46-138">calli</span></span>

<span data-ttu-id="0fd46-139">Il compilatore aggiungerà il supporto per un nuovo tipo di funzione `extern` che si traduce in modo efficiente in un'istruzione `.calli`.</span><span class="sxs-lookup"><span data-stu-id="0fd46-139">The compiler will add support for a new type of `extern` function that efficiently translates into a `.calli` instruction.</span></span> <span data-ttu-id="0fd46-140">L'attributo extern verrà contrassegnato con un attributo con la forma seguente:</span><span class="sxs-lookup"><span data-stu-id="0fd46-140">The extern attribute will be marked with an attribute of the following shape:</span></span>

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

<span data-ttu-id="0fd46-141">Ciò consente agli sviluppatori di definire metodi nel formato seguente:</span><span class="sxs-lookup"><span data-stu-id="0fd46-141">This allows developers to define methods in the following form:</span></span>

``` csharp
[CallIndirect(CallingConvention.Cdecl)]
static extern int MapValue(string s, void *ptr);

unsafe {
    var i = MapValue("42", &int.Parse);
    Console.WriteLine(i);
}
```

<span data-ttu-id="0fd46-142">Restrizioni relative al metodo a cui è applicato l'attributo `CallIndirect`:</span><span class="sxs-lookup"><span data-stu-id="0fd46-142">Restrictions on the method which has the `CallIndirect` attribute applied:</span></span>

- <span data-ttu-id="0fd46-143">Non è possibile avere un attributo `DllImport`.</span><span class="sxs-lookup"><span data-stu-id="0fd46-143">Cannot have a `DllImport` attribute.</span></span>
- <span data-ttu-id="0fd46-144">Non può essere generico.</span><span class="sxs-lookup"><span data-stu-id="0fd46-144">Cannot be generic.</span></span>

## <a name="open-issues"></a><span data-ttu-id="0fd46-145">Problemi aperti</span><span class="sxs-lookup"><span data-stu-id="0fd46-145">Open Issues</span></span>

### <a name="callingconvention"></a><span data-ttu-id="0fd46-146">CallingConvention</span><span class="sxs-lookup"><span data-stu-id="0fd46-146">CallingConvention</span></span>

<span data-ttu-id="0fd46-147">Il `CallIndirectAttribute` come progettato utilizza l'enumerazione `CallingConvention`, che non dispone di una voce per le convenzioni di chiamata gestite.</span><span class="sxs-lookup"><span data-stu-id="0fd46-147">The `CallIndirectAttribute` as designed uses the `CallingConvention` enum which lacks an entry for managed calling conventions.</span></span> <span data-ttu-id="0fd46-148">L'enumerazione deve essere estesa per includere questa convenzione di chiamata o l'attributo deve adottare un approccio diverso.</span><span class="sxs-lookup"><span data-stu-id="0fd46-148">The enum either needs to be extended to include this calling convention or the attribute needs to take a different approach.</span></span>

## <a name="considerations"></a><span data-ttu-id="0fd46-149">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="0fd46-149">Considerations</span></span>

### <a name="disambiguating-method-groups"></a><span data-ttu-id="0fd46-150">Gruppi di metodi distinguere</span><span class="sxs-lookup"><span data-stu-id="0fd46-150">Disambiguating method groups</span></span>

<span data-ttu-id="0fd46-151">Sono state riportate alcune informazioni sulle funzionalità che semplificano la risoluzione delle ambiguità dei gruppi di metodi passati a un'espressione address-of.</span><span class="sxs-lookup"><span data-stu-id="0fd46-151">There was some discussion around features that would make it easier to disambiguate method groups passed to an address-of expression.</span></span> <span data-ttu-id="0fd46-152">Ad esempio, aggiungendo potenzialmente elementi Signature alla sintassi:</span><span class="sxs-lookup"><span data-stu-id="0fd46-152">For instance potentially adding signature elements to the syntax:</span></span>

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

<span data-ttu-id="0fd46-153">Questa operazione è stata rifiutata perché non è stato possibile eseguire un caso interessante né è possibile che venga fornita una sintassi semplice.</span><span class="sxs-lookup"><span data-stu-id="0fd46-153">This was rejected because a compelling case could not be made nor could a simple syntax be envisioned here.</span></span> <span data-ttu-id="0fd46-154">Esiste anche una soluzione piuttosto semplice: definire un altro metodo che non è ambiguo e USA C# il codice per chiamare la funzione desiderata.</span><span class="sxs-lookup"><span data-stu-id="0fd46-154">Also there is a fairly straight forward work around: simple define another method that is unambiguous and uses C# code to call into the desired function.</span></span> 

``` csharp
class Workaround {
    public static void LocalLog() => Util.Log();
}
unsafe { 
    void* ptr = &Workaround.LocalLog;
}
```

<span data-ttu-id="0fd46-155">Questa operazione diventa ancora più semplice se `static` funzioni locali entrano nella lingua.</span><span class="sxs-lookup"><span data-stu-id="0fd46-155">This becomes even simpler if `static` local functions enter the language.</span></span> <span data-ttu-id="0fd46-156">Quindi, potrebbe essere definita nella stessa funzione che ha utilizzato l'operazione ambigua address-of:</span><span class="sxs-lookup"><span data-stu-id="0fd46-156">Then the work around could be defined in the same function that used the ambiguous address-of operation:</span></span>

``` csharp
unsafe { 
    static void LocalLog() => Util.Log();
    void* ptr = &Workaround.LocalLog;
}
```

### <a name="loadtypetokenint32"></a><span data-ttu-id="0fd46-157">LoadTypeTokenInt32</span><span class="sxs-lookup"><span data-stu-id="0fd46-157">LoadTypeTokenInt32</span></span>

<span data-ttu-id="0fd46-158">La proposta originale consentiva di caricare i token dei metadati come `int` valori in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="0fd46-158">The original proposal allowed for metadata tokens to be loaded as `int` values at compile time.</span></span> <span data-ttu-id="0fd46-159">Hanno essenzialmente `tokenof` con gli stessi argomenti di `handleof` ma viene valutato in fase di compilazione a una costante `int`.</span><span class="sxs-lookup"><span data-stu-id="0fd46-159">Essentially have `tokenof` that has the same arguments as `handleof` but is evaluated at compile time to an `int` constant.</span></span> 

<span data-ttu-id="0fd46-160">Questa operazione è stata rifiutata perché causa un problema significativo per le riscritture IL (di cui .NET dispone di molti).</span><span class="sxs-lookup"><span data-stu-id="0fd46-160">This was rejected as it causes significant problem for IL rewrites (of which .NET has many).</span></span> <span data-ttu-id="0fd46-161">Tali riscrittori spesso manipolano le tabelle di metadati in modo da invalidare tali valori.</span><span class="sxs-lookup"><span data-stu-id="0fd46-161">Such rewriters often manipulate the metadata tables in a way that could invalidate these values.</span></span> <span data-ttu-id="0fd46-162">Non esiste un modo ragionevole per i rewriter di aggiornare questi valori quando vengono archiviati come semplici valori `int`.</span><span class="sxs-lookup"><span data-stu-id="0fd46-162">There is no reasonable way for such rewriters to update these values when they are stored as simple `int` values.</span></span>

<span data-ttu-id="0fd46-163">L'idea sottostante di avere un handle opaco per le voci di metadati continuerà a essere esplorata dal team di Runtime.</span><span class="sxs-lookup"><span data-stu-id="0fd46-163">The underlying idea of having an opaque handle for metadata entries will continue to be explored by the runtime team.</span></span> 

## <a name="future-considerations"></a><span data-ttu-id="0fd46-164">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="0fd46-164">Future Considerations</span></span>

### <a name="static-local-functions"></a><span data-ttu-id="0fd46-165">funzioni locali statiche</span><span class="sxs-lookup"><span data-stu-id="0fd46-165">static local functions</span></span>

<span data-ttu-id="0fd46-166">Si fa riferimento alla [proposta](https://github.com/dotnet/csharplang/issues/1565) per consentire il modificatore `static` sulle funzioni locali.</span><span class="sxs-lookup"><span data-stu-id="0fd46-166">This refers to [the proposal](https://github.com/dotnet/csharplang/issues/1565) to allow the `static` modifier on local functions.</span></span> <span data-ttu-id="0fd46-167">Una funzione di questo tipo verrebbe generata come `static` e con la firma esatta specificata nel codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="0fd46-167">Such a function would be guaranteed to be emitted as `static` and with the exact signature specified in source code.</span></span> <span data-ttu-id="0fd46-168">Una funzione di questo tipo deve essere un argomento valido per `&` perché non contiene alcun problema attualmente presente nelle funzioni locali.</span><span class="sxs-lookup"><span data-stu-id="0fd46-168">Such a function should be a valid argument to `&` as it contains none of the problems local functions have today.</span></span>

### <a name="nativecallableattribute"></a><span data-ttu-id="0fd46-169">NativeCallableAttribute</span><span class="sxs-lookup"><span data-stu-id="0fd46-169">NativeCallableAttribute</span></span>

<span data-ttu-id="0fd46-170">CLR dispone di una funzionalità che consente di emettere metodi gestiti in modo che possano essere richiamati direttamente dal codice nativo.</span><span class="sxs-lookup"><span data-stu-id="0fd46-170">The CLR has a feature that allows for managed methods to be emitted in such a way that they are directly callable from native code.</span></span> <span data-ttu-id="0fd46-171">Questa operazione viene eseguita aggiungendo il `NativeCallableAttribute` ai metodi.</span><span class="sxs-lookup"><span data-stu-id="0fd46-171">This is done by adding the `NativeCallableAttribute` to methods.</span></span> <span data-ttu-id="0fd46-172">Tale metodo può essere chiamato solo dal codice nativo e quindi deve contenere solo tipi copiabili nella firma.</span><span class="sxs-lookup"><span data-stu-id="0fd46-172">Such a method is only callable from native code and hence must contain only blittable types in the signature.</span></span> <span data-ttu-id="0fd46-173">La chiamata dal codice gestito genera un errore di Runtime.</span><span class="sxs-lookup"><span data-stu-id="0fd46-173">Calling from managed code results in a runtime error.</span></span> 

<span data-ttu-id="0fd46-174">Questa funzionalità può essere adattata a questa proposta in modo analogo al seguente:</span><span class="sxs-lookup"><span data-stu-id="0fd46-174">This feature would pattern well with this proposal as it would allow:</span></span>

- <span data-ttu-id="0fd46-175">Passaggio di una funzione definita nel codice gestito al codice nativo come puntatore a funzione (via address-of) senza overhead nel codice gestito o nativo.</span><span class="sxs-lookup"><span data-stu-id="0fd46-175">Passing a function defined in managed code to native code as a function pointer (via address-of) with no overhead in managed or native code.</span></span> 
- <span data-ttu-id="0fd46-176">Il runtime può introdurre errori di utilizzo del sito per tali funzioni nel codice gestito per impedire che vengano richiamate in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="0fd46-176">Runtime can introduce use site errors for such functions in managed code to prevent them from being invoked at compile time.</span></span>




