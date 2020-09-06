---
ms.openlocfilehash: 92b4be885dc744ca0a0c6d0645ce9f6d3427ddd9
ms.sourcegitcommit: 3a6c79012a8c53fe84330c09080105ac177ff93d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2020
ms.locfileid: "89501653"
---
# <a name="simplified-null-argument-checking"></a><span data-ttu-id="f1f17-101">Controllo semplificato degli argomenti null</span><span class="sxs-lookup"><span data-stu-id="f1f17-101">Simplified Null Argument Checking</span></span>

## <a name="summary"></a><span data-ttu-id="f1f17-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="f1f17-102">Summary</span></span>
<span data-ttu-id="f1f17-103">Questa proposta fornisce una sintassi semplificata per la convalida degli argomenti del metodo e non viene `null` generata in `ArgumentNullException` modo appropriato.</span><span class="sxs-lookup"><span data-stu-id="f1f17-103">This proposal provides a simplified syntax for validating method arguments are not `null` and throwing `ArgumentNullException` appropriately.</span></span>

## <a name="motivation"></a><span data-ttu-id="f1f17-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="f1f17-104">Motivation</span></span>
<span data-ttu-id="f1f17-105">Il lavoro sulla progettazione dei tipi di riferimento Nullable ha causato l'esame del codice necessario per la `null` convalida degli argomenti.</span><span class="sxs-lookup"><span data-stu-id="f1f17-105">The work on designing nullable reference types has caused us to examine the code necessary for `null` argument validation.</span></span> <span data-ttu-id="f1f17-106">Dato che la NRT non influisce sulle esecuzioni di codice, gli sviluppatori devono ancora aggiungere il `if (arg is null) throw` codice della piastra caldaia anche nei progetti completamente `null` puliti.</span><span class="sxs-lookup"><span data-stu-id="f1f17-106">Given that NRT doesn't affect code execution developers still must add `if (arg is null) throw` boiler plate code even in projects which are fully `null` clean.</span></span> <span data-ttu-id="f1f17-107">Questo ci ha permesso di esplorare una sintassi minima per `null` la convalida degli argomenti nel linguaggio.</span><span class="sxs-lookup"><span data-stu-id="f1f17-107">This gave us the desire to explore a minimal syntax for argument `null` validation in the language.</span></span> 

<span data-ttu-id="f1f17-108">Sebbene si `null` prevede che questa sintassi di convalida dei parametri venga associata di frequente con NRT, la proposta è completamente indipendente.</span><span class="sxs-lookup"><span data-stu-id="f1f17-108">While this `null` parameter validation syntax is expected to pair frequently with NRT, the proposal is fully independent of it.</span></span> <span data-ttu-id="f1f17-109">La sintassi può essere utilizzata indipendentemente dalle `#nullable` direttive.</span><span class="sxs-lookup"><span data-stu-id="f1f17-109">The syntax can be used independent of `#nullable` directives.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="f1f17-110">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="f1f17-110">Detailed Design</span></span> 

### <a name="null-validation-parameter-syntax"></a><span data-ttu-id="f1f17-111">Sintassi dei parametri di convalida null</span><span class="sxs-lookup"><span data-stu-id="f1f17-111">Null validation parameter syntax</span></span>
<span data-ttu-id="f1f17-112">L'operatore Bang, `!` , può essere posizionato dopo un nome di parametro in un elenco di parametri e in questo modo il compilatore C# emetterà il `null` codice di controllo standard per il parametro.</span><span class="sxs-lookup"><span data-stu-id="f1f17-112">The bang operator, `!`, can be positioned after a parameter name in a parameter list and this will cause the C# compiler to emit standard `null` checking code for that parameter.</span></span> <span data-ttu-id="f1f17-113">Questa operazione viene definita `null` sintassi del parametro di convalida.</span><span class="sxs-lookup"><span data-stu-id="f1f17-113">This is referred to as `null` validation parameter syntax.</span></span> <span data-ttu-id="f1f17-114">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f1f17-114">For example:</span></span>

``` csharp
void M(string name!) {
    ...
}
```

<span data-ttu-id="f1f17-115">Verranno convertiti in:</span><span class="sxs-lookup"><span data-stu-id="f1f17-115">Will be translated into:</span></span>

``` csharp
void M(string name) {
    if (name is null) {
        throw new ArgumentNullException(nameof(name));
    }
    ...
}
```

<span data-ttu-id="f1f17-116">Il controllo generato si verificherà `null` prima del codice creato dallo sviluppatore nel metodo.</span><span class="sxs-lookup"><span data-stu-id="f1f17-116">The generated `null` check will occur before any developer authored code in the method.</span></span> <span data-ttu-id="f1f17-117">Quando più parametri contengono l' `!` operatore, i controlli si verificheranno nello stesso ordine in cui vengono dichiarati i parametri.</span><span class="sxs-lookup"><span data-stu-id="f1f17-117">When multiple parameters contain the `!` operator then the checks will occur in the same order as the parameters are declared.</span></span>

``` csharp
void M(string p1, string p2) {
    if (p1 is null) {
        throw new ArgumentNullException(nameof(p1));
    }
    if (p2 is null) {
        throw new ArgumentNullException(nameof(p2));
    }
    ...
}
```

<span data-ttu-id="f1f17-118">Il controllo sarà specifico per l'uguaglianza dei riferimenti a `null` , non richiama `==` né operatori definiti dall'utente.</span><span class="sxs-lookup"><span data-stu-id="f1f17-118">The check will be specifically for reference equality to `null`, it does not invoke `==` or any user defined operators.</span></span> <span data-ttu-id="f1f17-119">Ciò significa anche `!` che l'operatore può essere aggiunto solo ai parametri il cui tipo può essere testato per verificarne l'uguaglianza `null` .</span><span class="sxs-lookup"><span data-stu-id="f1f17-119">This also means the `!` operator can only be added to parameters whose type can be tested for equality against `null`.</span></span> <span data-ttu-id="f1f17-120">Ciò significa che non può essere usato su un parametro il cui tipo è noto come tipo di valore.</span><span class="sxs-lookup"><span data-stu-id="f1f17-120">This means it can't be used on a parameter whose type is known to be a value type.</span></span>

``` csharp
// Error: Cannot use ! on parameters who types derive from System.ValueType
void G<T>(T arg!) where T : struct {

}
```

<span data-ttu-id="f1f17-121">Nel caso di un costruttore, la `null` convalida viene eseguita prima di qualsiasi altro codice nel costruttore.</span><span class="sxs-lookup"><span data-stu-id="f1f17-121">In the case of a constructor, the `null` validation will occur before any other code in the constructor.</span></span> <span data-ttu-id="f1f17-122">Sono incluse le seguenti:</span><span class="sxs-lookup"><span data-stu-id="f1f17-122">That includes:</span></span> 

- <span data-ttu-id="f1f17-123">Concatenamento ad altri costruttori con `this` o `base`</span><span class="sxs-lookup"><span data-stu-id="f1f17-123">Chaining to other constructors with `this` or `base`</span></span> 
- <span data-ttu-id="f1f17-124">Inizializzatori di campo che si verificano in modo implicito nel costruttore</span><span class="sxs-lookup"><span data-stu-id="f1f17-124">Field initializers which implicitly occur in the constructor</span></span>

<span data-ttu-id="f1f17-125">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f1f17-125">For example:</span></span>

``` csharp
class C {
    string field = GetString();
    C(string name!): this(name) {
        ...
    }
}
```

<span data-ttu-id="f1f17-126">Verranno convertiti approssimativamente negli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="f1f17-126">Will be roughly translated into the following:</span></span>

``` csharp
class C {
    C(string name)
        if (name is null) {
            throw new ArgumentNullException(nameof(name));
        }
        field = GetString();
        :this(name);
        ...
}
```

<span data-ttu-id="f1f17-127">Nota: si tratta di un codice C# non valido, ma solo di un'approssimazione del funzionamento dell'implementazione.</span><span class="sxs-lookup"><span data-stu-id="f1f17-127">Note: this is not legal C# code but instead just an approximation of what the implementation does.</span></span> 

<span data-ttu-id="f1f17-128">La `null` sintassi dei parametri di convalida sarà valida anche negli elenchi di parametri lambda.</span><span class="sxs-lookup"><span data-stu-id="f1f17-128">The `null` validation parameter syntax will also be valid on lambda parameter lists.</span></span> <span data-ttu-id="f1f17-129">Questa proprietà è valida anche nella sintassi del singolo parametro che non dispone di parentesi.</span><span class="sxs-lookup"><span data-stu-id="f1f17-129">This is valid even in the single parameter syntax that lacks parens.</span></span>

``` csharp
void G() {
    // An identity lambda which throws on a null input
    Func<string, string> s = x! => x;
}
```

<span data-ttu-id="f1f17-130">La sintassi è valida anche per i parametri dei metodi iteratore.</span><span class="sxs-lookup"><span data-stu-id="f1f17-130">The syntax is also valid on parameters to iterator methods.</span></span> <span data-ttu-id="f1f17-131">A differenza di altro codice nell'iteratore `null` , la convalida viene eseguita quando viene richiamato il metodo iteratore, non quando viene camminato l'enumeratore sottostante.</span><span class="sxs-lookup"><span data-stu-id="f1f17-131">Unlike other code in the iterator the `null` validation will occur when the iterator method is invoked, not when the underlying enumerator is walked.</span></span> <span data-ttu-id="f1f17-132">Questo vale per gli `async` iteratori tradizionali o.</span><span class="sxs-lookup"><span data-stu-id="f1f17-132">This is true for traditional or `async` iterators.</span></span>

``` csharp
class Iterators {
    IEnumerable<char> GetCharacters(string s!) {
        foreach (var c in s) {
            yield return c;
        }
    }

    void Use() {
        // The invocation of GetCharacters will throw
        IEnumerable<char> e = GetCharacters(null);
    }
}
```

<span data-ttu-id="f1f17-133">L' `!` operatore può essere usato solo per gli elenchi di parametri a cui è associato un corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="f1f17-133">The `!` operator can only be used for parameter lists which have an associated method body.</span></span> <span data-ttu-id="f1f17-134">Ciò significa che non può essere utilizzato in una definizione di metodo `abstract` , `interface` `delegate` o `partial` .</span><span class="sxs-lookup"><span data-stu-id="f1f17-134">This means it cannot be used in an `abstract` method, `interface`, `delegate` or `partial` method definition.</span></span>

### <a name="extending-is-null"></a><span data-ttu-id="f1f17-135">Estensione null</span><span class="sxs-lookup"><span data-stu-id="f1f17-135">Extending is null</span></span>
<span data-ttu-id="f1f17-136">I tipi per i quali l'espressione `is null` è valida verranno estesi in modo da includere parametri di tipo non vincolati.</span><span class="sxs-lookup"><span data-stu-id="f1f17-136">The types for which the expression `is null` is valid will be extended to include unconstrained type parameters.</span></span> <span data-ttu-id="f1f17-137">Ciò consentirà al responsabile IT di soddisfare tutti i `null` tipi di `null` controllo validi.</span><span class="sxs-lookup"><span data-stu-id="f1f17-137">This will allow it to fill the intent of checking for `null` on all types which a `null` check is valid.</span></span> <span data-ttu-id="f1f17-138">In particolare, si tratta di tipi che non sono sicuramente noti come tipi di valore.</span><span class="sxs-lookup"><span data-stu-id="f1f17-138">Specifically that is types which are not definitely known to be value types.</span></span> <span data-ttu-id="f1f17-139">Per esempio, i parametri di tipo che sono vincolati a `struct` non possono essere usati con questa sintassi.</span><span class="sxs-lookup"><span data-stu-id="f1f17-139">For example Type parameters which are constrained to `struct` cannot be used with this syntax.</span></span>

``` csharp
void NullCheck<T1, T2>(T1 p1, T2 p2) where T2 : struct {
    // Okay: T1 could be a class or struct here.
    if (p1 is null) {
        ...
    }

    // Error 
    if (p2 is null) { 
        ...
    }
}
```

<span data-ttu-id="f1f17-140">Il comportamento di `is null` in un parametro di tipo sarà identico a quello di `== null` oggi.</span><span class="sxs-lookup"><span data-stu-id="f1f17-140">The behavior of `is null` on a type parameter will be the same as `== null` today.</span></span> <span data-ttu-id="f1f17-141">Nei casi in cui viene creata un'istanza del parametro di tipo come tipo di valore, il codice verrà valutato come `false` .</span><span class="sxs-lookup"><span data-stu-id="f1f17-141">In the cases where the type parameter is instantiated as a value type the code will be evaluated as `false`.</span></span> <span data-ttu-id="f1f17-142">Per i casi in cui si tratta di un tipo di riferimento, il codice eseguirà un `is null` controllo appropriato.</span><span class="sxs-lookup"><span data-stu-id="f1f17-142">For cases where it is a reference type the code will do a proper `is null` check.</span></span>

### <a name="intersection-with-nullable-reference-types"></a><span data-ttu-id="f1f17-143">Intersezione con tipi di riferimento Nullable</span><span class="sxs-lookup"><span data-stu-id="f1f17-143">Intersection with Nullable Reference Types</span></span>
<span data-ttu-id="f1f17-144">Qualsiasi parametro a cui è `!` applicato un operatore verrà avviato con lo stato Nullable `null` .</span><span class="sxs-lookup"><span data-stu-id="f1f17-144">Any parameter which has a `!` operator applied to it's name will start with the nullable state being not `null`.</span></span> <span data-ttu-id="f1f17-145">Questo vale anche se il tipo del parametro stesso è potenzialmente `null` .</span><span class="sxs-lookup"><span data-stu-id="f1f17-145">This is true even if the type of the parameter itself is potentially `null`.</span></span> <span data-ttu-id="f1f17-146">Che può verificarsi con un tipo nullable in modo esplicito, ad esempio `string?` , o con un parametro di tipo non vincolato.</span><span class="sxs-lookup"><span data-stu-id="f1f17-146">That can occur with an explicitly nullable type, such as say `string?`, or with an unconstrained type parameter.</span></span> 

<span data-ttu-id="f1f17-147">Quando una `!` sintassi sui parametri viene combinata con un tipo nullable in modo esplicito sul parametro, viene emesso un avviso dal compilatore:</span><span class="sxs-lookup"><span data-stu-id="f1f17-147">When a `!` syntax on parameters is combined with an explicitly nullable type on the parameter then a warning will be issued by the compiler:</span></span>

``` csharp
void WarnCase<T>(
    string? name!, // Warning: combining explicit null checking with a nullable type
    T value1 // Okay
)
```

## <a name="open-issues"></a><span data-ttu-id="f1f17-148">Problemi non risolti</span><span class="sxs-lookup"><span data-stu-id="f1f17-148">Open Issues</span></span>
<span data-ttu-id="f1f17-149">Nessuno</span><span class="sxs-lookup"><span data-stu-id="f1f17-149">None</span></span>

## <a name="considerations"></a><span data-ttu-id="f1f17-150">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="f1f17-150">Considerations</span></span>

### <a name="constructors"></a><span data-ttu-id="f1f17-151">Costruttori</span><span class="sxs-lookup"><span data-stu-id="f1f17-151">Constructors</span></span>
<span data-ttu-id="f1f17-152">La generazione di codice per i costruttori significa che è presente una modifica del comportamento ridotta, ma osservabile, durante il passaggio dalla `null` convalida standard odierna e la `null` sintassi del parametro di convalida ( `!` ).</span><span class="sxs-lookup"><span data-stu-id="f1f17-152">The code generation for constructors means there is a small, but observable, behavior change when moving from standard `null` validation today and the `null` validation parameter syntax (`!`).</span></span> <span data-ttu-id="f1f17-153">Il `null` controllo della convalida standard si verifica dopo che entrambi gli inizializzatori di campo e qualsiasi `base` `this` chiamata o.</span><span class="sxs-lookup"><span data-stu-id="f1f17-153">The `null` check in standard validation occurs after both field initializers and any `base` or `this` calls.</span></span> <span data-ttu-id="f1f17-154">Ciò significa che uno sviluppatore non può necessariamente migrare il 100% della `null` convalida alla nuova sintassi.</span><span class="sxs-lookup"><span data-stu-id="f1f17-154">This means a developer can't necessarily migrate 100% of their `null` validation to the new syntax.</span></span> <span data-ttu-id="f1f17-155">I costruttori richiedono almeno alcune ispezioni.</span><span class="sxs-lookup"><span data-stu-id="f1f17-155">Constructors at least require some inspection.</span></span>

<span data-ttu-id="f1f17-156">Dopo la discussione, anche se è stato deciso che è molto improbabile che si verifichino problemi di adozione significativi.</span><span class="sxs-lookup"><span data-stu-id="f1f17-156">After discussion though it was decided that this is very unlikely to cause any significant adoption issues.</span></span> <span data-ttu-id="f1f17-157">È più logico che il `null` controllo venga eseguito prima della logica del costruttore.</span><span class="sxs-lookup"><span data-stu-id="f1f17-157">It's more logical that the `null` check run before any logic in the constructor does.</span></span> <span data-ttu-id="f1f17-158">Può rivedere se vengono individuati problemi di compatibilità significativi.</span><span class="sxs-lookup"><span data-stu-id="f1f17-158">Can revisit if significant compat issues are discovered.</span></span>

### <a name="warning-when-mixing--and-"></a><span data-ttu-id="f1f17-159">Avviso durante la combinazione?</span><span class="sxs-lookup"><span data-stu-id="f1f17-159">Warning when mixing ?</span></span> <span data-ttu-id="f1f17-160">e!</span><span class="sxs-lookup"><span data-stu-id="f1f17-160">and !</span></span>
<span data-ttu-id="f1f17-161">Si è verificata una lunga discussione sulla possibilità o meno di emettere un avviso quando la `!` sintassi viene applicata a un parametro tipizzato in modo esplicito a un tipo Nullable.</span><span class="sxs-lookup"><span data-stu-id="f1f17-161">There was a lengthy discussion on whether or not a warning should be issued when the `!` syntax is applied to a parameter which is explicitly typed to a nullable type.</span></span> <span data-ttu-id="f1f17-162">In apparenza sembra una dichiarazione insensata dello sviluppatore, ma in alcuni casi le gerarchie dei tipi possono forzare gli sviluppatori in una situazione di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="f1f17-162">On the surface it seems like a nonsensical declaration by the developer but there are cases where type hierarchies could force developers into such a situation.</span></span> 

<span data-ttu-id="f1f17-163">Si consideri la seguente gerarchia di classi in una serie di assembly (presupponendo che tutti siano compilati con `null` il controllo abilitato):</span><span class="sxs-lookup"><span data-stu-id="f1f17-163">Consider the following class hierarchy across a series of assemblies (assuming all are compiled with `null` checking enabled):</span></span>

``` csharp
// Assembly1
abstract class C1 {
    protected abstract void M(object o); 
}

// Assembly2
abstract class C2 : C1 {

}

// Assembly3
abstract class C3 : C2 { 
    protected override void M(object o!) {
        ...
    }
}
```

<span data-ttu-id="f1f17-164">Qui l'autore di `C3` ha deciso di aggiungere `null` la convalida al parametro `o` .</span><span class="sxs-lookup"><span data-stu-id="f1f17-164">Here the author of `C3` decided to add `null` validation to the parameter `o`.</span></span> <span data-ttu-id="f1f17-165">Questo è completamente in linea con il modo in cui la funzionalità è destinata all'utilizzo.</span><span class="sxs-lookup"><span data-stu-id="f1f17-165">This is completely in line with how the feature is intended to be used.</span></span>

<span data-ttu-id="f1f17-166">Si supponga ora che in un secondo momento l'autore di Assembly2 decida di aggiungere la seguente sostituzione:</span><span class="sxs-lookup"><span data-stu-id="f1f17-166">Now imagine at a later date the author of Assembly2 decides to add the following override:</span></span>

``` csharp
// Assembly2
abstract class C2 : C1 {
   protected override void M(object? o) { 
       ...
   }
}
```

<span data-ttu-id="f1f17-167">Questa operazione è consentita dai tipi di riferimento Nullable, perché è lecito rendere il contratto più flessibile per le posizioni di input.</span><span class="sxs-lookup"><span data-stu-id="f1f17-167">This is allowed by nullable reference types as it's legal to make the contract more flexible for input positions.</span></span> <span data-ttu-id="f1f17-168">La funzionalità NRT in generale consente la covarianza ragionevole per il supporto di valori null per parametri/restituzione.</span><span class="sxs-lookup"><span data-stu-id="f1f17-168">The NRT feature in general allows for reasonable co/contravariance on parameter / return nullability.</span></span> <span data-ttu-id="f1f17-169">Tuttavia, il linguaggio esegue il controllo della covarianza sulla base dell'override più specifico, non della dichiarazione originale.</span><span class="sxs-lookup"><span data-stu-id="f1f17-169">However the language does the co/contravariance checking based on the most specific override, not the original declaration.</span></span> <span data-ttu-id="f1f17-170">Ciò significa che l'autore di Assembly3 riceverà un avviso sul tipo di `o` non corrispondenza e dovrà modificare la firma nel seguente per eliminarlo:</span><span class="sxs-lookup"><span data-stu-id="f1f17-170">This means the author of Assembly3 will get a warning about the type of `o` not matching and will need to change the signature to the following to eliminate it:</span></span> 

``` csharp
// Assembly3
abstract class C3 : C2 { 
    protected override void M(object? o!) {
        ...
    }
}
```

<span data-ttu-id="f1f17-171">A questo punto l'autore di Assembly3 dispone di alcune opzioni:</span><span class="sxs-lookup"><span data-stu-id="f1f17-171">At this point the author of Assembly3 has a few choices:</span></span>

- <span data-ttu-id="f1f17-172">Possono accettare o evitare l'avviso e non `object?` `object` corrispondono.</span><span class="sxs-lookup"><span data-stu-id="f1f17-172">They can accept / suppress the warning about `object?` and `object` mismatch.</span></span>
- <span data-ttu-id="f1f17-173">Possono accettare o evitare l'avviso e non `object?` `!` corrispondono.</span><span class="sxs-lookup"><span data-stu-id="f1f17-173">They can accept / suppress the warning about `object?` and `!` mismatch.</span></span>
- <span data-ttu-id="f1f17-174">Possono semplicemente rimuovere il `null` controllo di convalida (eliminare `!` ed eseguire il controllo esplicito)</span><span class="sxs-lookup"><span data-stu-id="f1f17-174">They can just remove the `null` validation check (delete `!` and do explicit checking)</span></span>

<span data-ttu-id="f1f17-175">Si tratta di uno scenario reale, ma per il momento è opportuno procedere con l'avviso.</span><span class="sxs-lookup"><span data-stu-id="f1f17-175">This is a real scenario but for now the idea is to move forward with the warning.</span></span> <span data-ttu-id="f1f17-176">Se l'avviso si verifica più spesso del previsto, è possibile rimuoverlo in un secondo momento (il contrario non è vero).</span><span class="sxs-lookup"><span data-stu-id="f1f17-176">If it turns out the warning happens more frequently than we anticipate then we can remove it later (the reverse is not true).</span></span>

### <a name="implicit-property-setter-arguments"></a><span data-ttu-id="f1f17-177">Argomenti del setter di proprietà implicite</span><span class="sxs-lookup"><span data-stu-id="f1f17-177">Implicit property setter arguments</span></span>
<span data-ttu-id="f1f17-178">L' `value` argomento di un parametro è implicito e non viene visualizzato in un elenco di parametri.</span><span class="sxs-lookup"><span data-stu-id="f1f17-178">The `value` argument of a parameter is implicit and does not appear in any parameter list.</span></span> <span data-ttu-id="f1f17-179">Ciò significa che non può essere la destinazione di questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="f1f17-179">That means it cannot be a target of this feature.</span></span> <span data-ttu-id="f1f17-180">È possibile estendere la sintassi del setter di proprietà per includere un elenco di parametri per consentire l' `!` applicazione dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="f1f17-180">The property setter syntax could be extended to include a parameter list to allow the `!` operator to be applied.</span></span> <span data-ttu-id="f1f17-181">Ma questo riduce l'idea di questa funzionalità rendendo `null` più semplice la convalida.</span><span class="sxs-lookup"><span data-stu-id="f1f17-181">But that cuts against the idea of this feature making `null` validation simpler.</span></span> <span data-ttu-id="f1f17-182">Di conseguenza, l' `value` argomento implicito non funzionerà con questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="f1f17-182">As such the implicit `value` argument just won't work with this feature.</span></span>

## <a name="future-considerations"></a><span data-ttu-id="f1f17-183">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="f1f17-183">Future Considerations</span></span>
<span data-ttu-id="f1f17-184">Nessuno</span><span class="sxs-lookup"><span data-stu-id="f1f17-184">None</span></span>
