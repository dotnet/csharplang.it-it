---
ms.openlocfilehash: c1ff78f64b1529e6b648d5cbe5b80e507642fac8
ms.sourcegitcommit: ec31c2771e390305a430c407e29f72d8299a4c72
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99985908"
---
# <a name="asyncmethodbuilder-override"></a><span data-ttu-id="41cf2-101">Override di AsyncMethodBuilder</span><span class="sxs-lookup"><span data-stu-id="41cf2-101">AsyncMethodBuilder override</span></span>

* <span data-ttu-id="41cf2-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="41cf2-102">[x] Proposed</span></span>
* <span data-ttu-id="41cf2-103">[] Prototipo: non avviato</span><span class="sxs-lookup"><span data-stu-id="41cf2-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="41cf2-104">[] Implementazione: non avviata</span><span class="sxs-lookup"><span data-stu-id="41cf2-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="41cf2-105">[] Specifica: non avviata</span><span class="sxs-lookup"><span data-stu-id="41cf2-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="41cf2-106">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="41cf2-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="41cf2-107">Consente l'override per metodo del generatore di metodi asincroni da usare.</span><span class="sxs-lookup"><span data-stu-id="41cf2-107">Allow per-method override of the async method builder to use.</span></span>
<span data-ttu-id="41cf2-108">Per alcuni metodi asincroni si vuole personalizzare la chiamata di `Builder.Create()` per usare un _tipo di generatore_ diverso e possibilmente passare informazioni aggiuntive sullo stato.</span><span class="sxs-lookup"><span data-stu-id="41cf2-108">For some async methods we want to customize the invocation of `Builder.Create()` to use a different _builder type_ and possibly pass some additional state information.</span></span>

```C#
[AsyncMethodBuilderOverride(typeof(PoolingAsyncValueTaskMethodBuilder<int>))] // new, referring to some custom builder type
static async ValueTask<int> ExampleAsync() { ... }
```

## <a name="motivation"></a><span data-ttu-id="41cf2-109">Motivazione</span><span class="sxs-lookup"><span data-stu-id="41cf2-109">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="41cf2-110">Attualmente, i generatori di metodi asincroni sono collegati a un tipo specificato usato come tipo restituito di un metodo asincrono.</span><span class="sxs-lookup"><span data-stu-id="41cf2-110">Today, async method builders are tied to a given type used as a return type of an async method.</span></span>  <span data-ttu-id="41cf2-111">Ad esempio, qualsiasi metodo dichiarato come `async Task` utilizza `AsyncTaskMethodBuilder` e qualsiasi metodo dichiarato come `async ValueTask<T>` utilizza `AsyncValueTaskMethodBuilder<T>` .</span><span class="sxs-lookup"><span data-stu-id="41cf2-111">For example, any method that's declared as `async Task` uses `AsyncTaskMethodBuilder`, and any method that's declared as `async ValueTask<T>` uses `AsyncValueTaskMethodBuilder<T>`.</span></span>  <span data-ttu-id="41cf2-112">Questo è dovuto all' `[AsyncMethodBuilder(Type)]` attributo del tipo utilizzato come tipo restituito, ad esempio, `ValueTask<T>` è attribuito come `[AsyncMethodBuilder(typeof(AsyncValueTaskMethodBuilder<>))]` .</span><span class="sxs-lookup"><span data-stu-id="41cf2-112">This is due to the `[AsyncMethodBuilder(Type)]` attribute on the type used as a return type, e.g. `ValueTask<T>` is attributed as `[AsyncMethodBuilder(typeof(AsyncValueTaskMethodBuilder<>))]`.</span></span> <span data-ttu-id="41cf2-113">Questo risolve la maggior parte dei casi comuni, ma lascia alcuni buchi rilevanti per gli scenari avanzati.</span><span class="sxs-lookup"><span data-stu-id="41cf2-113">This addresses the majority common case, but it leaves a few notable holes for advanced scenarios.</span></span>

<span data-ttu-id="41cf2-114">In .NET 5 è stata fornita una funzionalità sperimentale che fornisce due modalità in cui `AsyncValueTaskMethodBuilder` e `AsyncValueTaskMethodBuilder<T>` operano.</span><span class="sxs-lookup"><span data-stu-id="41cf2-114">In .NET 5, an experimental feature was shipped that provides two modes in which `AsyncValueTaskMethodBuilder` and `AsyncValueTaskMethodBuilder<T>` operate.</span></span>  <span data-ttu-id="41cf2-115">La modalità attiva per impostazione predefinita è identica a quella in cui è stata introdotta la funzionalità: quando la macchina a stati deve essere sollevata nell'heap, viene allocato un oggetto per archiviare lo stato e il metodo asincrono restituisce un oggetto `ValueTask{<T>}` supportato da `Task{<T>}` .</span><span class="sxs-lookup"><span data-stu-id="41cf2-115">The on-by-default mode is the same as has been there since the functionality was introduced: when the state machine needs to be lifted to the heap, an object is allocated to store the state, and the async method returns a `ValueTask{<T>}` backed by a `Task{<T>}`.</span></span>  <span data-ttu-id="41cf2-116">Tuttavia, se viene impostata una variabile di ambiente, tutti i generatori del processo passano a una modalità in cui, invece, le `ValueTask{<T>}` istanze sono supportate dalle implementazioni riutilizzabili in `IValueTaskSource{<T>}` pool.</span><span class="sxs-lookup"><span data-stu-id="41cf2-116">However, if an environment variable is set, all builders in the process switch to a mode where, instead, the `ValueTask{<T>}` instances are backed by reusable `IValueTaskSource{<T>}` implementations that are pooled.</span></span>  <span data-ttu-id="41cf2-117">Ogni metodo asincrono dispone di un proprio pool con un numero massimo fisso di istanze consentite per il pool e, a condizione che non venga mai restituito un numero maggiore di tale numero al pool, in modo che venga ripreso in pool contemporaneamente, i `async ValueTask<{T}>` metodi diventano senza alcun sovraccarico di allocazione GC.</span><span class="sxs-lookup"><span data-stu-id="41cf2-117">Each async method has its own pool with a fixed maximum number of instances allowed to be pooled, and as long as no more than that number are ever returned to the pool to be pooled at the same time, `async ValueTask<{T}>` methods effectively become free of any GC allocation overhead.</span></span>

<span data-ttu-id="41cf2-118">Esistono diversi problemi con questa modalità sperimentale, tuttavia, che è il motivo per cui è disattivata per impostazione predefinita e b) è probabile che venga rimossa in una versione futura, a meno che non siano presenti nuove informazioni molto interessanti ( https://github.com/dotnet/runtime/issues/13633) .</span><span class="sxs-lookup"><span data-stu-id="41cf2-118">There are several problems with this experimental mode, however, which is both why a) it's off by default and b) we're likely to remove it in a future release unless very compelling new information emerges (https://github.com/dotnet/runtime/issues/13633).</span></span>
- <span data-ttu-id="41cf2-119">Introduce una differenza comportamentale per i consumer dell'oggetto restituito `ValueTask{<T>}` se `ValueTask` non viene utilizzato in base alla specifica.  Quando è supportato da `Task` , è possibile eseguire le `ValueTask` operazioni con un `Task` , ad esempio await it più volte, await it simultaneamente, blocca in attesa del completamento e così via.  Tuttavia, quando è supportato da un arbitrario `IValueTaskSource` , queste operazioni non sono consentite e il cambio automatico dal primo al secondo può causare bug.</span><span class="sxs-lookup"><span data-stu-id="41cf2-119">It introduces a behavioral difference for consumers of the returned `ValueTask{<T>}` if that `ValueTask` isn't being consumed according to spec.  When it's backed by a `Task`, you can do with the `ValueTask` things you can do with a `Task`, like await it multiple times, await it concurrently, block waiting for it to complete, etc.  But when it's backed by an arbitrary `IValueTaskSource`, such operations are prohibited, and automatically switching from the former to the latter can lead to bugs.</span></span>  <span data-ttu-id="41cf2-120">Con l'opzione a livello di processo e che interessano tutti i `async ValueTask` metodi del processo, indipendentemente dal fatto che vengano controllate o meno, il martello è troppo grande.</span><span class="sxs-lookup"><span data-stu-id="41cf2-120">With the switch at the process level and affecting all `async ValueTask` methods in the process, whether you control them or not, it's too big a hammer.</span></span>
- <span data-ttu-id="41cf2-121">Non è necessariamente una vittoria sulle prestazioni e può rappresentare una regressione in alcune situazioni.</span><span class="sxs-lookup"><span data-stu-id="41cf2-121">It's not necessarily a performance win, and could represent a regression in some situations.</span></span>  <span data-ttu-id="41cf2-122">L'implementazione sta commerciando il costo del pool (l'accesso a un pool non è gratuito) con il costo di GC e in varie situazioni il GC può vincere.</span><span class="sxs-lookup"><span data-stu-id="41cf2-122">The implementation is trading the cost of pooling (accessing a pool isn't free) with the cost of GC, and in various situations the GC can win.</span></span>  <span data-ttu-id="41cf2-123">Anche in questo caso, l'applicazione del pool a tutti i `async ValueTask` metodi del processo, anziché essere selettiva rispetto a quelli che trarrebbero maggiore vantaggio, è troppo grande.</span><span class="sxs-lookup"><span data-stu-id="41cf2-123">Again, applying the pooling to all `async ValueTask` methods in the process rather than being selective about the ones it would most benefit is too big a hammer.</span></span>
- <span data-ttu-id="41cf2-124">Aggiunge alla dimensione IL di un'applicazione tagliata, anche se il flag non è impostato, quindi alla dimensione ASM risultante.</span><span class="sxs-lookup"><span data-stu-id="41cf2-124">It adds to the IL size of a trimmed application, even if the flag isn't set, and then to the resulting asm size.</span></span>  <span data-ttu-id="41cf2-125">È possibile che sia possibile aggirare i miglioramenti apportati all'implementazione per indicare che per una distribuzione specifica la variabile di ambiente sarà sempre falsa, ma, come attualmente, ogni `async ValueTask` metodo ha visto, ad esempio, un aumento del footprint binario di ~ 2K nelle immagini AOT a causa di questa opzione, e, ancora una volta, che si applica a tutti i `async ValueTask` metodi nell'intera chiusura dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="41cf2-125">It's possible that can be worked around with improvements to the implementation to teach it that for a given deployment the environment variable will always be false, but as it stands today, every `async ValueTask` method saw for example an ~2K binary footprint increase in aot images due to this option, and, again, that applies to all `async ValueTask` methods in the whole application closure.</span></span>
- <span data-ttu-id="41cf2-126">Metodi diversi possono trarre vantaggio da diversi livelli di controllo, ad esempio le dimensioni del pool utilizzate a causa della conoscenza del metodo e della relativa modalità di utilizzo, ma la stessa impostazione viene applicata a tutti gli utilizzi del generatore.</span><span class="sxs-lookup"><span data-stu-id="41cf2-126">Different methods may benefit from differing levels of control, e.g. the size of the pool employed because of knowledge of the method and how it's used, but the same setting is applied to all uses of the builder.</span></span>  <span data-ttu-id="41cf2-127">Si può immaginare di aggirare il problema, in modo che il codice del generatore usi la reflection in fase di esecuzione per cercare un attributo, ma che aggiunga spese di run-time significative e probabilmente nel percorso di avvio.</span><span class="sxs-lookup"><span data-stu-id="41cf2-127">One could imagine working around that by having the builder code use reflection at runtime to look for some attribute, but that adds significant run-time expense, and likely on the startup path.</span></span>

<span data-ttu-id="41cf2-128">Oltre a tutti questi problemi con il pool esistente, è anche possibile impedire agli sviluppatori di scrivere i propri generatori personalizzati per i tipi di cui non sono proprietari.</span><span class="sxs-lookup"><span data-stu-id="41cf2-128">On top of all of these issues with the existing pooling, it's also the case that developers are prevented from writing their own customized builders for types they don't own.</span></span>  <span data-ttu-id="41cf2-129">Se, ad esempio, uno sviluppatore desidera implementare il proprio supporto per la creazione di pool, deve presentare anche un nuovo tipo simile a un'attività, anziché semplicemente poter utilizzare `{Value}Task{<T>}` , perché l'attributo che specifica il generatore è solo specificabile nella dichiarazione di tipo del tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="41cf2-129">If, for example, a developer wants to implement their own pooling support, they also have to introduce a brand new task-like type, rather than just being able to use `{Value}Task{<T>}`, because the attribute specifying the builder is only specifiable on the type declaration of the return type.</span></span>

<span data-ttu-id="41cf2-130">È necessario un modo per consentire a un singolo metodo asincrono di acconsentire esplicitamente a un generatore specifico.</span><span class="sxs-lookup"><span data-stu-id="41cf2-130">We need a way to have an individual async method opt-in to a specific builder.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="41cf2-131">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="41cf2-131">Detailed design</span></span>
[design]: #detailed-design

#### <a name="p0-asyncmethodbuilderoverrideattribute-applied-on-async-methods"></a><span data-ttu-id="41cf2-132">P0: AsyncMethodBuilderOverrideAttribute applicato ai metodi asincroni</span><span class="sxs-lookup"><span data-stu-id="41cf2-132">P0: AsyncMethodBuilderOverrideAttribute applied on async methods</span></span>

<span data-ttu-id="41cf2-133">In `dotnet/runtime` aggiungere `AsyncMethodBuilderOverrideAttribute` :</span><span class="sxs-lookup"><span data-stu-id="41cf2-133">In `dotnet/runtime`, add `AsyncMethodBuilderOverrideAttribute`:</span></span>
```csharp
namespace System.Runtime.CompilerServices
{
    /// <summary>
    /// Indicates the type of the async method builder that should be used by a language compiler to
    /// build the attributed method.
    /// </summary>
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Struct | AttributeTargets.Interface | AttributeTargets.Method | AttributeTargets.Module, Inherited = false, AllowMultiple = false)]
    public sealed class AsyncMethodBuilderOverrideAttribute : Attribute
    {
        /// <summary>Initializes the <see cref="AsyncMethodBuilderOverrideAttribute"/>.</summary>
        /// <param name="builderType">The <see cref="Type"/> of the associated builder.</param>
        public AsyncMethodBuilderOverrideAttribute(Type builderType) => BuilderType = builderType;

        // for scoped application (use property for targetReturnType? have compiler check that it's provided)
        public AsyncMethodBuilderOverrideAttribute(Type builderType, Type targetReturnType) => ...

        /// <summary>Gets the <see cref="Type"/> of the associated builder.</summary>
        public Type BuilderType { get; }
    }
}
```

<span data-ttu-id="41cf2-134">Nel compilatore C# preferiscono l'attributo nel metodo quando si determina il generatore da usare rispetto a quello definito nel tipo.</span><span class="sxs-lookup"><span data-stu-id="41cf2-134">In the C# compiler, prefer the attribute on the method when determining what builder to use over the one defined on the type.</span></span>  <span data-ttu-id="41cf2-135">Ad esempio, oggi se un metodo viene definito come:</span><span class="sxs-lookup"><span data-stu-id="41cf2-135">For example, today if a method is defined as:</span></span>
```C#
public async ValueTask<T> ExampleAsync() { ... }
```
<span data-ttu-id="41cf2-136">il compilatore genererà codice analogo al seguente:</span><span class="sxs-lookup"><span data-stu-id="41cf2-136">the compiler will generate code akin to:</span></span>
```C#
[AsyncStateMachine(typeof(<ExampleAsync>d__29))]
[CompilerGenerated]
static ValueTask<int> ExampleAsync()
{
    <ExampleAsync>d__29 stateMachine;
    stateMachine.<>t__builder = AsyncValueTaskMethodBuilder<int>.Create();
    stateMachine.<>1__state = -1;
    stateMachine.<>t__builder.Start(ref stateMachine);
    return stateMachine.<>t__builder.Task;
}
```
<span data-ttu-id="41cf2-137">Con questa modifica, se lo sviluppatore ha scritto:</span><span class="sxs-lookup"><span data-stu-id="41cf2-137">With this change, if the developer wrote:</span></span>
```C#
[AsyncMethodBuilderOverride(typeof(PoolingAsyncValueTaskMethodBuilder<int>))] // new, referring to some custom builder type
static async ValueTask<int> ExampleAsync() { ... }
```
<span data-ttu-id="41cf2-138">verrebbe invece compilato in:</span><span class="sxs-lookup"><span data-stu-id="41cf2-138">it would instead be compiled to:</span></span>
```C#
[AsyncStateMachine(typeof(<ExampleAsync>d__29))]
[CompilerGenerated]
[AsyncMethodBuilder(typeof(PoolingAsyncValueTaskMethodBuilder<int>))] // retained but not necessary anymore
static ValueTask<int> ExampleAsync()
{
    <ExampleAsync>d__29 stateMachine;
    stateMachine.<>t__builder = PoolingAsyncValueTaskMethodBuilder<int>.Create(); // <>t__builder now a different type
    stateMachine.<>1__state = -1;
    stateMachine.<>t__builder.Start(ref stateMachine);
    return stateMachine.<>t__builder.Task;
}
```

<span data-ttu-id="41cf2-139">Solo le piccole aggiunte consentono:</span><span class="sxs-lookup"><span data-stu-id="41cf2-139">Just those small additions enable:</span></span>
- <span data-ttu-id="41cf2-140">Chiunque scriva il proprio generatore che può essere applicato a metodi asincroni che restituiscono `Task<T>` e `ValueTask<T>`</span><span class="sxs-lookup"><span data-stu-id="41cf2-140">Anyone to write their own builder that can be applied to async methods that return `Task<T>` and `ValueTask<T>`</span></span>
- <span data-ttu-id="41cf2-141">Come "chiunque", il runtime per fornire il supporto del generatore sperimentale come nuovi tipi di generatore pubblici che possono essere scelti in base al metodo; il supporto esistente verrebbe rimosso dai generatori esistenti.</span><span class="sxs-lookup"><span data-stu-id="41cf2-141">As "anyone", the runtime to ship the experimental builder support as new public builder types that can be opted into on a method-by-method basis; the existing support would be removed from the existing builders.</span></span>  <span data-ttu-id="41cf2-142">I metodi, inclusi quelli che si occupano delle librerie principali, possono quindi essere attribuiti a caso per caso per usare il supporto del pool, senza alcun effetto su altri metodi non attribuiti.</span><span class="sxs-lookup"><span data-stu-id="41cf2-142">Methods (including some we care about in the core libraries) can then be attributed on a case-by-case basis to use the pooling support, without impacting any other unattributed methods.</span></span>

<span data-ttu-id="41cf2-143">e con modifiche minime alla superficie di attacco o funzioni nel compilatore.</span><span class="sxs-lookup"><span data-stu-id="41cf2-143">and with minimal surface area changes or feature work in the compiler.</span></span>


<span data-ttu-id="41cf2-144">Si noti che è necessario il codice generato per consentire la restituzione di un tipo diverso dal `Create` Metodo:</span><span class="sxs-lookup"><span data-stu-id="41cf2-144">Note that we need the emitted code to allow a different type being returned from `Create` method:</span></span>
```
AsyncPooledBuilder _builder = AsyncPooledBuilderWithSize4.Create();
```

#### <a name="p1-passing-state-to-the-builder-instantiation"></a><span data-ttu-id="41cf2-145">P1: passaggio dello stato alla creazione dell'istanza del generatore</span><span class="sxs-lookup"><span data-stu-id="41cf2-145">P1: Passing state to the builder instantiation</span></span>

<span data-ttu-id="41cf2-146">In alcuni scenari può essere utile passare alcune informazioni al generatore.</span><span class="sxs-lookup"><span data-stu-id="41cf2-146">In some scenarios, it would be useful to pass some information to the builder.</span></span>  <span data-ttu-id="41cf2-147">Questo può essere statico (ad esempio, le costanti che configurano la dimensione del pool da usare) o dinamico (ad esempio, riferimento alla cache o singleton da usare).</span><span class="sxs-lookup"><span data-stu-id="41cf2-147">This could be static (e.g. constants configuring the size of the pool to use) or dynamic (e.g. reference to cache or singleton to use).</span></span>

<span data-ttu-id="41cf2-148">Sono state rilevate alcune opzioni (documentate di seguito per il record), ma si consiglia di non eseguire alcuna operazione (opzione #0) finché non si determina che il supporto dello stato dinamico potrebbe essere utile.</span><span class="sxs-lookup"><span data-stu-id="41cf2-148">We brainstormed a few options (documented below for the record) but end up recommending doing nothing (option #0) until we determine that supporting dynamic state would be worthwhile.</span></span>

##### <a name="option-0-no-extra-state"></a><span data-ttu-id="41cf2-149">Opzione 0: nessun stato aggiuntivo</span><span class="sxs-lookup"><span data-stu-id="41cf2-149">Option 0: no extra state</span></span>

<span data-ttu-id="41cf2-150">È possibile approssimare il passaggio di informazioni statiche eseguendo il wrapping dei tipi di generatore.</span><span class="sxs-lookup"><span data-stu-id="41cf2-150">It is possible to approximate passing static information by wrapping builder types.</span></span>
<span data-ttu-id="41cf2-151">Ad esempio, è possibile creare un tipo di generatore personalizzato che hardcoded una determinata configurazione:</span><span class="sxs-lookup"><span data-stu-id="41cf2-151">For instance, one could create a custom builder type that hardcodes a certain configuration:</span></span>

```C#
public struct AsyncPooledBuilderWithSize4
{
    AsyncPooledBuilder Create() => AsyncPooledBuilder.Create(4);
}
```

##### <a name="option-1-use-constants-in-attribute-as-arguments-for-create"></a><span data-ttu-id="41cf2-152">Opzione 1: usare le costanti nell'attributo come argomenti per `Create`</span><span class="sxs-lookup"><span data-stu-id="41cf2-152">Option 1: use constants in attribute as arguments for `Create`</span></span>

<span data-ttu-id="41cf2-153">`AsyncMethodBuilderOverrideAttribute`Avrebbe accettato alcune informazioni aggiuntive:</span><span class="sxs-lookup"><span data-stu-id="41cf2-153">The `AsyncMethodBuilderOverrideAttribute` would have accept some additional information:</span></span>
```C#
    public AsyncMethodBuilderOverrideAttribute(Type builderType, params object[] args)
```

<span data-ttu-id="41cf2-154">Argomenti raccolti nell'attributo:</span><span class="sxs-lookup"><span data-stu-id="41cf2-154">The arguments collected in the attribute:</span></span>
```C#
[AsyncMethodBuilderOverride(typeof(AsyncPooledBuilder), 4)]
```
<span data-ttu-id="41cf2-155">viene usato nella chiamata del `Create` Metodo:</span><span class="sxs-lookup"><span data-stu-id="41cf2-155">would be used in invocation of the `Create` method:</span></span>
```C#
AsyncPooledBuilder.Create(4);
```

##### <a name="option-2-use-arguments-of-the-async-method"></a><span data-ttu-id="41cf2-156">Opzione 2: usare gli argomenti del metodo asincrono</span><span class="sxs-lookup"><span data-stu-id="41cf2-156">Option 2: use arguments of the async method</span></span>

<span data-ttu-id="41cf2-157">Oltre a `AsyncMethodBuilderOverrideAttribute` un attributo per contrassegnare uno dei parametri del metodo asincrono:</span><span class="sxs-lookup"><span data-stu-id="41cf2-157">In addition to `AsyncMethodBuilderOverrideAttribute` we would have an attribute to tag one of the async method's parameters:</span></span>
```C#
[AsyncMethodBuilderOverride(typeof(AsyncPooledBuilder))]
async ValueTask MyMethodAsync(/* regular arguments */, [FOR_BUILDER] int i)
```

<span data-ttu-id="41cf2-158">Questo comporterebbe il passaggio del valore per il parametro alla `Create` chiamata:</span><span class="sxs-lookup"><span data-stu-id="41cf2-158">This would result in the value for that parameter being passed to the `Create` invocation:</span></span>
```C#
BuilderType.Create(i);
```

<span data-ttu-id="41cf2-159">Nella maggior parte dei casi, l'utente potrebbe scrivere un wrapper per ottenere la firma desiderata:</span><span class="sxs-lookup"><span data-stu-id="41cf2-159">In most cases, the user would end up writing a wrapper to achieve the desired signature:</span></span>
```C#
public ValueTask MyMethodWrapperAsync(/* regular parameters */)
{
    return MyMethodAsync(/* pass values from regular parameters through */, 4); // static or dynamic value for the builder
}
```

<span data-ttu-id="41cf2-160">Uno può anche passare i delegati memorizzati nella cache in questo modo:</span><span class="sxs-lookup"><span data-stu-id="41cf2-160">One could even pass cached delegates this way:</span></span>
```C#
.ctor()
{
    s_func_take = () => get_item();
    s_action_put = t => free_item(t);
}

public ValueTask MyMethodWrapperAsync(/* regular parameters */)
{
   return MyMethodAsync(/* pass values from regular parameters through */, (s_func_take, s_action_put));
}
```

<span data-ttu-id="41cf2-161">Questo approccio è simile al `EnumeratorCancellationAttribute` funzionamento di.</span><span class="sxs-lookup"><span data-stu-id="41cf2-161">This approach resembles how `EnumeratorCancellationAttribute` works.</span></span> <span data-ttu-id="41cf2-162">Il parametro aggiuntivo, tuttavia, non è utile per il codice utente, quindi la firma e la macchina a Stati sono inquinate.</span><span class="sxs-lookup"><span data-stu-id="41cf2-162">But the extra parameter isn't useful to user code, so we're polluting the signature and state machine.</span></span>
<span data-ttu-id="41cf2-163">Questo approccio si sovrappone con l'opzione #1, quindi è probabile che non si desideri supportare entrambi.</span><span class="sxs-lookup"><span data-stu-id="41cf2-163">This approach overlaps with option #1, so we probably wouldn't want to support both.</span></span>

##### <a name="option-3-pass-a-lambda-that-instantiates-builders"></a><span data-ttu-id="41cf2-164">Opzione 3: passare un'espressione lambda che crea un'istanza di generatori</span><span class="sxs-lookup"><span data-stu-id="41cf2-164">Option 3: pass a lambda that instantiates builders</span></span>

<span data-ttu-id="41cf2-165">Come sostituzione per `AsyncMethodBuilderOverrideAttribute` (o possibilmente in aggiunta), avremmo un attributo per contrassegnare uno dei parametri del metodo asincrono:</span><span class="sxs-lookup"><span data-stu-id="41cf2-165">As a replacement for `AsyncMethodBuilderOverrideAttribute` (or possibly in addition to it) we would have an attribute to tag one of the async method's parameters:</span></span>
```C#
async ValueTask MyMethodAsync(/* regular parameters */, [FOR_BUILDER] Func<AsyncPooledBuilder> lambda)
```
<span data-ttu-id="41cf2-166">Il parametro deve avere un tipo delegato senza parametri e restituire un tipo di generatore.</span><span class="sxs-lookup"><span data-stu-id="41cf2-166">That parameter would need to have a delegate type with no parameters and returning a builder type.</span></span>

<span data-ttu-id="41cf2-167">Il compilatore potrebbe generare:</span><span class="sxs-lookup"><span data-stu-id="41cf2-167">The compiler could them generate:</span></span>
```C#
...
static ValueTask<int> MyMethodAsync(/* regular parameters */, [FOR_BUILDER] Func<AsyncPooledBuilder> lambda)
{
    <MyMethodAsync>d__29 stateMachine;
    stateMachine.<>t__builder = lambda();
    ...
}
```

<span data-ttu-id="41cf2-168">È ancora presente il problema dell'inquinamento della firma del metodo e della macchina a Stati.</span><span class="sxs-lookup"><span data-stu-id="41cf2-168">We still have the problem of polluting the method signature and the state machine.</span></span>

<span data-ttu-id="41cf2-169">Nel caso in cui si desideri un generatore contenente due delegati memorizzati nella cache</span><span class="sxs-lookup"><span data-stu-id="41cf2-169">In the case where we want a builder holding two cached delegates</span></span>

```C#
.ctor()
{
    s_func_take = () => get_item();
    s_action_put = t => free_item(t);
    s_func = () => Builder.Create(take: s_func_take, put: s_action_put);
}

public ValueTask MyMethodWrapperAsync(...)
{
    return MyMethodAsync(..., s_func);
}
```

#### <a name="p2-enable-at-the-module-and-type-level-as-well"></a><span data-ttu-id="41cf2-170">P2: abilitare anche a livello di modulo (e digitare?)</span><span class="sxs-lookup"><span data-stu-id="41cf2-170">P2: Enable at the module (and type?) level as well</span></span>

<span data-ttu-id="41cf2-171">Uno sviluppatore che desidera utilizzare un generatore personalizzato specifico per tutti i relativi metodi può eseguire questa operazione inserendo l'attributo pertinente per ogni metodo.</span><span class="sxs-lookup"><span data-stu-id="41cf2-171">A developer that wants to using a specific custom builder for all of their methods can do so by putting the relevant attribute on each method.</span></span>  <span data-ttu-id="41cf2-172">Tuttavia, è anche possibile consentire l'attribuzione a livello di modulo o di tipo, nel qual caso ogni metodo pertinente all'interno di tale ambito si comporterebbe come se fosse annotato direttamente, ad esempio</span><span class="sxs-lookup"><span data-stu-id="41cf2-172">But we could also enable attributing at the module or type level, in which case every relevant method within that scope would behave as if it were directly annotated, e.g.</span></span>
```C#
[module: AsyncMethodBuilderOverride(typeof(PoolingAsyncValueTaskMethodBuilder), typeof(ValueTask)]
[module: AsyncMethodBuilderOverride(typeof(PoolingAsyncValueTaskMethodBuilder<>), typeof(ValueTask<>)]

class MyClass
{
    public async ValueTask Method1Async() { ... } // would use PoolingAsyncValueTaskMethodBuilder
    public async ValueTask<int> Method2Async() { ... } // would use PoolingAsyncValueTaskMethodBuilder<int>
    public async ValueTask<string> Method3Async() { ... } // would use PoolingAsyncValueTaskMethodBuilder<string>
}
```

<span data-ttu-id="41cf2-173">A tale proposito, aggiungere i membri seguenti all'attributo:</span><span class="sxs-lookup"><span data-stu-id="41cf2-173">For this we would add the following members to the attribute:</span></span>
```C#
namespace System.Runtime.CompilerServices
{
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Struct | AttributeTargets.Interface | AttributeTargets.Method | AttributeTargets.Module, Inherited = false, AllowMultiple = false)]
    public sealed class AsyncMethodBuilderOverrideAttribute : Attribute
    {
        ...
        // for scoped application (use property for targetReturnType? have compiler check that it's provided)
        public AsyncMethodBuilderOverrideAttribute(Type builderType, Type targetReturnType) => ...

        public Type TargetReturnType { get; }
    }
}
```

<span data-ttu-id="41cf2-174">Questa operazione non solo renderebbe più semplice l'inserimento dell'attributo su ogni metodo, ma renderebbe più semplice l'utilizzo di compilazioni diverse che usavano diversi generatori.</span><span class="sxs-lookup"><span data-stu-id="41cf2-174">This would not only make it more convenient than putting the attribute on every method, it would also make it easier to employ different builds that used different builders.</span></span>  <span data-ttu-id="41cf2-175">Una compilazione ottimizzata per la velocità effettiva potrebbe includere, ad esempio, un file con estensione cs che specifica un generatore di pool in un attributo a livello di modulo, mentre una compilazione ottimizzata per le dimensioni potrebbe includere un file con estensione cs che specifica un generatore minimalista che sceglie di usare più allocazione/conversione boxing anziché numerose ottimizzazioni della velocità effettiva e della specializzazione del codice.</span><span class="sxs-lookup"><span data-stu-id="41cf2-175">For example, a build optimized for throughput might include a .cs file that specifies a pooling builder in a module-level attribute, whereas a build optimized for size might a include a .cs file that specifies a minimalistic builder that opts to use more allocation/boxing instead of lots of generic specialization and throughput optimizations that lead to code bloat.</span></span>


## <a name="drawbacks"></a><span data-ttu-id="41cf2-176">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="41cf2-176">Drawbacks</span></span>
[drawbacks]: #drawbacks

* <span data-ttu-id="41cf2-177">La sintassi per l'applicazione di tale attributo a un metodo è Verbose.</span><span class="sxs-lookup"><span data-stu-id="41cf2-177">The syntax for applying such an attribute to a method is verbose.</span></span>  <span data-ttu-id="41cf2-178">L'effetto di questa operazione viene ridotto se uno sviluppatore può applicarlo a più metodi, ad esempio a livello di tipo o di modulo.</span><span class="sxs-lookup"><span data-stu-id="41cf2-178">The impact of this is lessened if a developer can apply it to multiple methods en mass, e.g. at the type or module level.</span></span>

## <a name="alternatives"></a><span data-ttu-id="41cf2-179">Alternativi</span><span class="sxs-lookup"><span data-stu-id="41cf2-179">Alternatives</span></span>
[alternatives]: #alternatives

- <span data-ttu-id="41cf2-180">Implementare un tipo diverso per l'attività e esporre la differenza ai consumer.</span><span class="sxs-lookup"><span data-stu-id="41cf2-180">Implement a different task-like type and expose that difference to consumers.</span></span>  <span data-ttu-id="41cf2-181">`ValueTask` è stata resa estendibile tramite l' `IValueTaskSource` interfaccia per evitare tale necessità.</span><span class="sxs-lookup"><span data-stu-id="41cf2-181">`ValueTask` was made extensible via the `IValueTaskSource` interface to avoid that need, however.</span></span>
- <span data-ttu-id="41cf2-182">Risolvere solo la parte del problema relativa al pool di ValueTask, abilitando l'esperimento come implementazione on-by-default-only.</span><span class="sxs-lookup"><span data-stu-id="41cf2-182">Address just the ValueTask pooling part of the issue by enabling the experiment as the on-by-default-and-only implementation.</span></span>  <span data-ttu-id="41cf2-183">Questo non risolve altri aspetti, ad esempio la configurazione del pool, o l'abilitazione di un altro utente a fornire il proprio generatore.</span><span class="sxs-lookup"><span data-stu-id="41cf2-183">That doesn't address other aspects, such as configuring the pooling, or enabling someone else to provide their own builder.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="41cf2-184">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="41cf2-184">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

1. <span data-ttu-id="41cf2-185">**Attributo.**</span><span class="sxs-lookup"><span data-stu-id="41cf2-185">**Attribute.**</span></span> <span data-ttu-id="41cf2-186">È necessario riutilizzare `[AsyncMethodBuilder(typeof(...))]` o introdurre ancora un altro attributo?</span><span class="sxs-lookup"><span data-stu-id="41cf2-186">Should we reuse `[AsyncMethodBuilder(typeof(...))]` or introduce yet another attribute?</span></span>
2. <span data-ttu-id="41cf2-187">**Sostituire o creare anche.**</span><span class="sxs-lookup"><span data-stu-id="41cf2-187">**Replace or also create.**</span></span> <span data-ttu-id="41cf2-188">Tutti gli esempi di questa proposta sono relativi alla sostituzione di un generatore di tipo attività compilabile.</span><span class="sxs-lookup"><span data-stu-id="41cf2-188">All of the examples in this proposal are about replacing a buildable task-like's builder.</span></span>  <span data-ttu-id="41cf2-189">L'ambito della funzionalità dovrebbe essere sufficiente?</span><span class="sxs-lookup"><span data-stu-id="41cf2-189">Should the feature be scoped to just that?</span></span> <span data-ttu-id="41cf2-190">In alternativa, è possibile usare questo attributo in un metodo con un tipo restituito che non dispone già di un generatore (ad esempio, un'interfaccia comune)?</span><span class="sxs-lookup"><span data-stu-id="41cf2-190">Or should you be able to use this attribute on a method with a return type that doesn't already have a builder (e.g. some common interface)?</span></span>  <span data-ttu-id="41cf2-191">Che può influisca sulla risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="41cf2-191">That could impact overload resolution.</span></span>
3. <span data-ttu-id="41cf2-192">**Virtuals/Interfaces.**</span><span class="sxs-lookup"><span data-stu-id="41cf2-192">**Virtuals / Interfaces.**</span></span> <span data-ttu-id="41cf2-193">Qual è il comportamento se l'attributo viene specificato in un metodo di interfaccia?</span><span class="sxs-lookup"><span data-stu-id="41cf2-193">What is the behavior if the attribute is specified on an interface method?</span></span>  <span data-ttu-id="41cf2-194">Credo che debba essere un NOP o un avviso o un errore del compilatore, ma non dovrebbe influisca sulle implementazioni dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="41cf2-194">I think it should either be a nop or a compiler warning/error, but it shouldn't impact implementations of the interface.</span></span>  <span data-ttu-id="41cf2-195">Esiste una domanda simile per i metodi di base sottoposti a override e di nuovo non credo che l'attributo nel metodo di base debba influisca sul comportamento di un'implementazione di override.</span><span class="sxs-lookup"><span data-stu-id="41cf2-195">A similar question exists for base methods that are overridden, and there again I don't think the attribute on the base method should impact how an override implementation behaves.</span></span> <span data-ttu-id="41cf2-196">Si noti che l'attributo corrente ha ereditato = false sul relativo AttributeUsage.</span><span class="sxs-lookup"><span data-stu-id="41cf2-196">Note the current attribute has Inherited = false on its AttributeUsage.</span></span>
4. <span data-ttu-id="41cf2-197">**Precedenza.**</span><span class="sxs-lookup"><span data-stu-id="41cf2-197">**Precedence.**</span></span> <span data-ttu-id="41cf2-198">Se si vuole eseguire l'annotazione a livello di modulo o di tipo, è necessario decidere quale sia l'attribuzione vincente nel caso in cui siano state applicate più, ad esempio una sul metodo, una sul modulo contenitore.</span><span class="sxs-lookup"><span data-stu-id="41cf2-198">If we wanted to do the module/type-level annotation, we would need to decide on which attribution wins in the case where multiple ones applied (e.g. one on the method, one on the containing module).</span></span>  <span data-ttu-id="41cf2-199">È anche necessario determinare se questa operazione richiederebbe l'uso di un attributo diverso (vedere (1) sopra), ad esempio quale sarebbe il comportamento se un tipo simile a un'attività era nello stesso ambito?</span><span class="sxs-lookup"><span data-stu-id="41cf2-199">We would also need to determine if this would necessitate using a different attribute (see (1) above), e.g. what would the behavior be if a task-like type was in the same scope?</span></span>  <span data-ttu-id="41cf2-200">In alternativa, se un'attività compilabile, ad esempio, aveva metodi asincroni su di essa, verrebbe influenzato dall'attributo applicato al tipo simile a un'attività per specificare il generatore predefinito?</span><span class="sxs-lookup"><span data-stu-id="41cf2-200">Or if a buildable task-like itself had async methods on it, would they be influenced by the attributed applied to the task-like type to specify its default builder?</span></span>
5. <span data-ttu-id="41cf2-201">**Generatori privati**.</span><span class="sxs-lookup"><span data-stu-id="41cf2-201">**Private Builders**.</span></span> <span data-ttu-id="41cf2-202">Il compilatore deve supportare i generatori di metodi asincroni non pubblici?</span><span class="sxs-lookup"><span data-stu-id="41cf2-202">Should the compiler support non-public async method builders?</span></span> <span data-ttu-id="41cf2-203">Questa non è una specifica di oggi, ma sperimentale sono supportate solo quelle pubbliche.</span><span class="sxs-lookup"><span data-stu-id="41cf2-203">This is not spec'd today, but experimentally we only support public ones.</span></span>  <span data-ttu-id="41cf2-204">Questo ha senso quando l'attributo viene applicato a un tipo per controllare quale generatore viene usato con quel tipo, perché chiunque scriva un metodo asincrono con quel tipo come tipo restituito dovrebbe avere accesso al generatore.</span><span class="sxs-lookup"><span data-stu-id="41cf2-204">That makes some sense when the attribute is applied to a type to control what builder is used with that type, since anyone writing an async method with that type as the return type would need access to the builder.</span></span>  <span data-ttu-id="41cf2-205">Tuttavia, con questa nuova funzionalità, quando l'attributo viene applicato a un metodo, influisca solo sull'implementazione di tale metodo e pertanto può fare ragionevolmente riferimento a un generatore non pubblico.</span><span class="sxs-lookup"><span data-stu-id="41cf2-205">However, with this new feature, when that attribute is applied to a method, it only impacts the implementation of that method, and thus could reasonably reference a non-public builder.</span></span>  <span data-ttu-id="41cf2-206">È probabile che si desideri supportare gli autori di librerie con quelli non pubblici che vogliono usare.</span><span class="sxs-lookup"><span data-stu-id="41cf2-206">Likely we will want to support library authors who have non-public ones they want to use.</span></span>
6. <span data-ttu-id="41cf2-207">**Stato passthrough per consentire un pool più efficiente**.</span><span class="sxs-lookup"><span data-stu-id="41cf2-207">**Passthrough state to enable more efficient pooling**.</span></span>  <span data-ttu-id="41cf2-208">Si consideri un tipo come SslStream o WebSocket.</span><span class="sxs-lookup"><span data-stu-id="41cf2-208">Consider a type like SslStream or WebSocket.</span></span>  <span data-ttu-id="41cf2-209">Che espongono operazioni asincrone di lettura/scrittura e consentono la lettura e la scrittura simultanee, ma al massimo un'operazione di lettura alla volta e al massimo 1 operazione di scrittura alla volta.</span><span class="sxs-lookup"><span data-stu-id="41cf2-209">These expose read/write async operations, and allow for reading and writing to happen concurrently but at most 1 read operation at a time and at most 1 write operation at a time.</span></span>  <span data-ttu-id="41cf2-210">Questa soluzione è ideale per il pool, perché ogni istanza di SslStream o WebSocket richiede al massimo un oggetto in pool per le letture e un oggetto in pool per le Scritture.</span><span class="sxs-lookup"><span data-stu-id="41cf2-210">That makes these ideal for pooling, as each SslStream or WebSocket instance would need at most one pooled object for reads and one pooled object for writes.</span></span>  <span data-ttu-id="41cf2-211">Inoltre, un pool centralizzato è eccessivo: piuttosto che pagare i costi di un pool centrale che deve essere gestito e di accesso sincronizzato, ogni SslStream/WebSocket può semplicemente gestire un campo per archiviare il singleton per il lettore e un singleton per il writer, eliminando tutti i conflitti per il pool ed eliminando tutte le operazioni di gestione associate ai limiti del pool.</span><span class="sxs-lookup"><span data-stu-id="41cf2-211">Further, a centralized pool is overkill: rather than paying the costs of having a central pool that needs to be managed and access synchronized, every SslStream/WebSocket could just maintain a field to store the singleton for the reader and a singleton for the writer, eliminating all contention for pooling and eliminating all management associated with pool limits.</span></span>  <span data-ttu-id="41cf2-212">Il problema è la modalità di connessione di un campo arbitrario nell'istanza di con il meccanismo di pooling utilizzato dal generatore.</span><span class="sxs-lookup"><span data-stu-id="41cf2-212">The problem is, how to connect an arbitrary field on the instance with the pooling mechanism employed by the builder.</span></span>  <span data-ttu-id="41cf2-213">Potremmo almeno renderlo possibile se avessimo passato tutti gli argomenti al metodo asincrono in una firma corrispondente nel metodo create del generatore (o forse un metodo Bind separato, o qualche cosa), ma il generatore doveva essere specializzato per quel tipo specifico, conoscendo i campi.</span><span class="sxs-lookup"><span data-stu-id="41cf2-213">We could at least make it possible if we passed through all arguments to the async method into a corresponding signature on the builder's Create method (or maybe a separate Bind method, or some such thing), but then the builder would need to be specialized for that specific type, knowing about its fields.</span></span>  <span data-ttu-id="41cf2-214">Il metodo Create potrebbe potenzialmente accettare un delegato Rent e un delegato return e il metodo asincrono potrebbe essere appositamente creato per accettare tali argomenti (insieme a uno stato dell'oggetto da passare).</span><span class="sxs-lookup"><span data-stu-id="41cf2-214">The Create method could potentially take a rent delegate and a return delegate, and the async method could be specially crafted to accept such arguments (along with an object state to be passed in).</span></span>  <span data-ttu-id="41cf2-215">La soluzione ideale è quella di creare un'ottima soluzione, in quanto renderebbe il meccanismo significativamente più potente e prezioso.</span><span class="sxs-lookup"><span data-stu-id="41cf2-215">It would be great to come up with a good solution here, as it would make the mechanism significantly more powerful and valuable.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="41cf2-216">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="41cf2-216">Design meetings</span></span>

<span data-ttu-id="41cf2-217">Collegamento alle note sulla progettazione che interessano questa proposta e descrivono in una frase per ogni modifica apportata.</span><span class="sxs-lookup"><span data-stu-id="41cf2-217">Link to design notes that affect this proposal, and describe in one sentence for each what changes they led to.</span></span>
