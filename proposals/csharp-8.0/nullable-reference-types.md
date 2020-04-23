---
ms.openlocfilehash: 54f9a372ca0329a284f06876f544e0b4936af02a
ms.sourcegitcommit: 356ee04506a2a82292be25d7b029e7ce2a39e63a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82037402"
---
# <a name="nullable-reference-types-in-c"></a><span data-ttu-id="92d6b-101">Nullable reference types in C #</span><span class="sxs-lookup"><span data-stu-id="92d6b-101">Nullable reference types in C#</span></span> #

<span data-ttu-id="92d6b-102">L'obiettivo di questa funzionalità è:</span><span class="sxs-lookup"><span data-stu-id="92d6b-102">The goal of this feature is to:</span></span>

* <span data-ttu-id="92d6b-103">Consentire agli sviluppatori di esprimere se una variabile, un parametro o un risultato di un tipo di riferimento deve essere null o meno.</span><span class="sxs-lookup"><span data-stu-id="92d6b-103">Allow developers to express whether a variable, parameter or result of a reference type is intended to be null or not.</span></span>
* <span data-ttu-id="92d6b-104">Fornire avvisi quando tali variabili, parametri e risultati non vengono utilizzati in base a tale finalità.</span><span class="sxs-lookup"><span data-stu-id="92d6b-104">Provide warnings when such variables, parameters and results are not used according to that intent.</span></span>

## <a name="expression-of-intent"></a><span data-ttu-id="92d6b-105">Espressione di intenti</span><span class="sxs-lookup"><span data-stu-id="92d6b-105">Expression of intent</span></span>

<span data-ttu-id="92d6b-106">Il linguaggio `T?` contiene già la sintassi per i tipi di valore.</span><span class="sxs-lookup"><span data-stu-id="92d6b-106">The language already contains the `T?` syntax for value types.</span></span> <span data-ttu-id="92d6b-107">È semplice estendere questa sintassi ai tipi di riferimento.</span><span class="sxs-lookup"><span data-stu-id="92d6b-107">It is straightforward to extend this syntax to reference types.</span></span>

<span data-ttu-id="92d6b-108">Si presuppone che lo scopo di un `T` tipo di riferimento non decorato sia che sia non null.</span><span class="sxs-lookup"><span data-stu-id="92d6b-108">It is assumed that the intent of an unadorned reference type `T` is for it to be non-null.</span></span>

## <a name="checking-of-nullable-references"></a><span data-ttu-id="92d6b-109">Controllo dei riferimenti nullable</span><span class="sxs-lookup"><span data-stu-id="92d6b-109">Checking of nullable references</span></span>

<span data-ttu-id="92d6b-110">Un'analisi del flusso tiene traccia delle variabili di riferimento nullable.</span><span class="sxs-lookup"><span data-stu-id="92d6b-110">A flow analysis tracks nullable reference variables.</span></span> <span data-ttu-id="92d6b-111">Se l'analisi ritiene che non sarebbero null (ad esempio dopo un assegno o un'assegnazione), il relativo valore verrà considerato un riferimento non null.</span><span class="sxs-lookup"><span data-stu-id="92d6b-111">Where the analysis deems that they would not be null (e.g. after a check or an assignment), their value will be considered a non-null reference.</span></span>

<span data-ttu-id="92d6b-112">Un riferimento nullable può anche essere considerato in `x!` modo esplicito come non null con l'operatore suffisso (l'operatore "damnit"), perché quando l'analisi del flusso non può stabilire una situazione non null che lo sviluppatore sa che esiste.</span><span class="sxs-lookup"><span data-stu-id="92d6b-112">A nullable reference can also explicitly be treated as non-null with the postfix `x!` operator (the "damnit" operator), for when flow analysis cannot establish a non-null situation that the developer knows is there.</span></span>

<span data-ttu-id="92d6b-113">In caso contrario, viene fornito un avviso se un riferimento nullable viene dereferenziato o viene convertito in un tipo non null.</span><span class="sxs-lookup"><span data-stu-id="92d6b-113">Otherwise, a warning is given if a nullable reference is dereferenced, or is converted to a non-null type.</span></span>

<span data-ttu-id="92d6b-114">Viene visualizzato un avviso `S[]` durante `T?[]` la `S?[]` `T[]`conversione da e verso a .</span><span class="sxs-lookup"><span data-stu-id="92d6b-114">A warning is given when converting from `S[]` to `T?[]` and from `S?[]` to `T[]`.</span></span>

<span data-ttu-id="92d6b-115">Viene visualizzato un avviso `C<S>` durante `C<T?>` la conversione da a`out`except quando il `C<S?>` `C<T>` parametro di tipo è covariante ( ) e quando si esegue la conversione da a except quando il parametro di tipo è controvariante ( ).`in`</span><span class="sxs-lookup"><span data-stu-id="92d6b-115">A warning is given when converting from `C<S>` to `C<T?>` except when the type parameter is covariant (`out`), and when converting from `C<S?>` to `C<T>` except when the type parameter is contravariant (`in`).</span></span>

<span data-ttu-id="92d6b-116">Viene visualizzato un `C<T?>` avviso se il parametro di tipo ha vincoli non null.</span><span class="sxs-lookup"><span data-stu-id="92d6b-116">A warning is given on `C<T?>` if the type parameter has non-null constraints.</span></span>

## <a name="checking-of-non-null-references"></a><span data-ttu-id="92d6b-117">Controllo dei riferimenti non Null</span><span class="sxs-lookup"><span data-stu-id="92d6b-117">Checking of non-null references</span></span>

<span data-ttu-id="92d6b-118">Viene visualizzato un avviso se un valore letterale null viene assegnato a una variabile non null o passato come parametro non null.</span><span class="sxs-lookup"><span data-stu-id="92d6b-118">A warning is given if a null literal is assigned to a non-null variable or passed as a non-null parameter.</span></span>

<span data-ttu-id="92d6b-119">Viene inoltre visualizzato un avviso se un costruttore non inizializza in modo esplicito i campi di riferimento non null.</span><span class="sxs-lookup"><span data-stu-id="92d6b-119">A warning is also given if a constructor does not explicitly initialize non-null reference fields.</span></span>

<span data-ttu-id="92d6b-120">Non è possibile tenere traccia in modo adeguato che tutti gli elementi di una matrice di riferimenti non null vengono inizializzati.</span><span class="sxs-lookup"><span data-stu-id="92d6b-120">We cannot adequately track that all elements of an array of non-null references are initialized.</span></span> <span data-ttu-id="92d6b-121">Tuttavia, è possibile emettere un avviso se nessun elemento di una matrice appena creata viene assegnato a prima che la matrice viene letta o passata.</span><span class="sxs-lookup"><span data-stu-id="92d6b-121">However, we could issue a warning if no element of a newly created array is assigned to before the array is read from or passed on.</span></span> <span data-ttu-id="92d6b-122">Questo potrebbe gestire il caso comune senza essere troppo rumoroso.</span><span class="sxs-lookup"><span data-stu-id="92d6b-122">That might handle the common case without being too noisy.</span></span>

<span data-ttu-id="92d6b-123">Dobbiamo decidere se `default(T)` genera un avviso o viene semplicemente `T?`trattato come di tipo .</span><span class="sxs-lookup"><span data-stu-id="92d6b-123">We need to decide whether `default(T)` generates a warning, or is simply treated as being of the type `T?`.</span></span>

## <a name="metadata-representation"></a><span data-ttu-id="92d6b-124">Rappresentazione dei metadati</span><span class="sxs-lookup"><span data-stu-id="92d6b-124">Metadata representation</span></span>

<span data-ttu-id="92d6b-125">Gli ornamenti di supporto dei valori Null devono essere rappresentati nei metadati come attributi.</span><span class="sxs-lookup"><span data-stu-id="92d6b-125">Nullability adornments should be represented in metadata as attributes.</span></span> <span data-ttu-id="92d6b-126">Ciò significa che i compilatori di livello inferiore li ignoreranno.</span><span class="sxs-lookup"><span data-stu-id="92d6b-126">This means that downlevel compilers will ignore them.</span></span>

<span data-ttu-id="92d6b-127">È necessario decidere se sono incluse solo le annotazioni nullable o se è anche presente qualche indicazione se non null è "on" nell'assembly.</span><span class="sxs-lookup"><span data-stu-id="92d6b-127">We need to decide if only nullable annotations are included, or there's also some indication of whether non-null was "on" in the assembly.</span></span>

## <a name="generics"></a><span data-ttu-id="92d6b-128">Generics</span><span class="sxs-lookup"><span data-stu-id="92d6b-128">Generics</span></span>

<span data-ttu-id="92d6b-129">Se un `T` parametro di tipo ha vincoli non nullable, viene considerato come non nullable all'interno del relativo ambito.</span><span class="sxs-lookup"><span data-stu-id="92d6b-129">If a type parameter `T` has non-nullable constraints, it is treated as non-nullable within its scope.</span></span>

<span data-ttu-id="92d6b-130">Se un parametro di tipo non è vincolato o ha solo vincoli nullable, la situazione è un po' più complessa: ciò significa che l'argomento di tipo corrispondente potrebbe *essere* nullable o non nullable.</span><span class="sxs-lookup"><span data-stu-id="92d6b-130">If a type parameter is unconstrained or has only nullable constraints, the situation is a little more complex: this means that the corresponding type argument could be *either* nullable or non-nullable.</span></span> <span data-ttu-id="92d6b-131">La cosa sicura da fare in questa situazione è considerare il parametro di tipo *come* nullable e non nullable, dando avvisi quando uno dei due viene violato.</span><span class="sxs-lookup"><span data-stu-id="92d6b-131">The safe thing to do in that situation is to treat the type parameter as *both* nullable and non-nullable, giving warnings when either is violated.</span></span> 

<span data-ttu-id="92d6b-132">È necessario considerare se devono essere consentiti vincoli espliciti di riferimento nullable.</span><span class="sxs-lookup"><span data-stu-id="92d6b-132">It is worth considering whether explicit nullable reference constraints should be allowed.</span></span> <span data-ttu-id="92d6b-133">Si noti, tuttavia, che non è possibile evitare di avere tipi di riferimento nullable in *modo implicito* essere vincoli in alcuni casi (vincoli ereditati).</span><span class="sxs-lookup"><span data-stu-id="92d6b-133">Note, however, that we cannot avoid having nullable reference types *implicitly* be constraints in certain cases (inherited constraints).</span></span>

<span data-ttu-id="92d6b-134">Il `class` vincolo non è null.</span><span class="sxs-lookup"><span data-stu-id="92d6b-134">The `class` constraint is non-null.</span></span> <span data-ttu-id="92d6b-135">È possibile `class?` considerare se deve essere un vincolo nullable valido che denota "tipo di riferimento nullable".</span><span class="sxs-lookup"><span data-stu-id="92d6b-135">We can consider whether `class?` should be a valid nullable constraint denoting "nullable reference type".</span></span>

## <a name="type-inference"></a><span data-ttu-id="92d6b-136">Inferenza</span><span class="sxs-lookup"><span data-stu-id="92d6b-136">Type inference</span></span>

<span data-ttu-id="92d6b-137">Nell'inferenza del tipo, se un tipo che contribuisce è un tipo di riferimento nullable, il tipo risultante deve essere nullable.</span><span class="sxs-lookup"><span data-stu-id="92d6b-137">In type inference, if a contributing type is a nullable reference type, the resulting type should be nullable.</span></span> <span data-ttu-id="92d6b-138">In altre parole, viene propagato il valore null.</span><span class="sxs-lookup"><span data-stu-id="92d6b-138">In other words, nullness is propagated.</span></span>

<span data-ttu-id="92d6b-139">È necessario considerare `null` se il valore letterale come espressione partecipante deve contribuire con nullità.</span><span class="sxs-lookup"><span data-stu-id="92d6b-139">We should consider whether the `null` literal as a participating expression should contribute nullness.</span></span> <span data-ttu-id="92d6b-140">Non oggi: per i tipi di valore porta a un errore, mentre per i tipi di riferimento il valore null converte correttamente nel tipo normale.</span><span class="sxs-lookup"><span data-stu-id="92d6b-140">It doesn't today: for value types it leads to an error, whereas for reference types the null successfully converts to the plain type.</span></span>

```csharp
string? n = "world";
var x = b ? "Hello" : n; // string?
var y = b ? "Hello" : null; // string? or error
var z = b ? 7 : null; // Error today, could be int?
```

## <a name="null-guard-guidance"></a><span data-ttu-id="92d6b-141">Guida di protezione null</span><span class="sxs-lookup"><span data-stu-id="92d6b-141">Null guard guidance</span></span>

<span data-ttu-id="92d6b-142">Come funzionalità, i tipi di riferimento nullable consentono agli sviluppatori di esprimere la propria finalità e forniscono avvisi tramite l'analisi del flusso se tale finalità è contraddetta.</span><span class="sxs-lookup"><span data-stu-id="92d6b-142">As a feature, nullable reference types allow developers to express their intent, and provide warnings through flow analysis if that intent is contradicted.</span></span> <span data-ttu-id="92d6b-143">C'è una domanda comune se siano necessarie o meno guardie nulle.</span><span class="sxs-lookup"><span data-stu-id="92d6b-143">There is a common question as to whether or not null guards are necessary.</span></span>

### <a name="example-of-null-guard"></a><span data-ttu-id="92d6b-144">Esempio di null guard</span><span class="sxs-lookup"><span data-stu-id="92d6b-144">Example of null guard</span></span>

```csharp
public void DoWork(Worker worker)
{
    // Guard against worker being null
    if (worker is null)
    {
        throw new ArgumentNullException(nameof(worker));
    }

    // Otherwise use worker argument
}
```

<span data-ttu-id="92d6b-145">Nell'esempio precedente, `DoWork` la funzione `Worker` accetta a e `null`protegge da esso potenzialmente essere .</span><span class="sxs-lookup"><span data-stu-id="92d6b-145">In the previous example, the `DoWork` function accepts a `Worker` and guards against it potentially being `null`.</span></span> <span data-ttu-id="92d6b-146">Se `worker` l'argomento è `null` `throw`, la funzione verrà . `DoWork`</span><span class="sxs-lookup"><span data-stu-id="92d6b-146">If the `worker` argument is `null`, the `DoWork` function will `throw`.</span></span> <span data-ttu-id="92d6b-147">Con i tipi di riferimento nullable, il codice `Worker` dell'esempio `null`precedente rende l'intento che il parametro *non* sia .</span><span class="sxs-lookup"><span data-stu-id="92d6b-147">With nullable reference types, the code in the previous example makes the intent that the `Worker` parameter would *not* be `null`.</span></span> <span data-ttu-id="92d6b-148">Se `DoWork` la funzione era un'API pubblica, ad esempio un pacchetto NuGet o una libreria condivisa, come guida è consigliabile lasciare le guardie null.</span><span class="sxs-lookup"><span data-stu-id="92d6b-148">If the `DoWork` function was a public API, such as a NuGet package or a shared library - as guidance you should leave null guards in place.</span></span> <span data-ttu-id="92d6b-149">Come API pubblica, l'unica garanzia che `null` un chiamante non passa è proteggersi da esso.</span><span class="sxs-lookup"><span data-stu-id="92d6b-149">As a public API, the only guarantee that a caller isn't passing `null` is to guard against it.</span></span>

### <a name="express-intent"></a><span data-ttu-id="92d6b-150">Intento espresso</span><span class="sxs-lookup"><span data-stu-id="92d6b-150">Express intent</span></span>

<span data-ttu-id="92d6b-151">Un utilizzo più interessante dell'esempio precedente `Worker` consiste `null`nell'esprimere che il parametro potrebbe essere , rendendo così la protezione null più appropriata.</span><span class="sxs-lookup"><span data-stu-id="92d6b-151">A more compelling use of the previous example is to express that the `Worker` parameter could be `null`, thus making the null guard more appropriate.</span></span> <span data-ttu-id="92d6b-152">Se si rimuove la guardia null nell'esempio seguente, il compilatore avverte che è possibile dereferenziare null.</span><span class="sxs-lookup"><span data-stu-id="92d6b-152">If you remove the null guard in the following example, the compiler warns that you may be dereferencing null.</span></span> <span data-ttu-id="92d6b-153">Indipendentemente da ciò, entrambe le guardie nulli sono ancora valide.</span><span class="sxs-lookup"><span data-stu-id="92d6b-153">Regardless, both null guards are still valid.</span></span>

```csharp
public void DoWork(Worker? worker)
{
    // Guard against worker being null
    if (worker is null)
    {
        throw new ArgumentNullException(nameof(worker));
    }

    // Otherwise use worker argument
}
```

<span data-ttu-id="92d6b-154">Per le API non pubbliche, ad esempio il codice sorgente interamente in controllo da uno sviluppatore o un team di sviluppo: i tipi di riferimento nullable potrebbero consentire la rimozione sicura di server di protezione null in cui gli sviluppatori possono garantire che non sia necessario.</span><span class="sxs-lookup"><span data-stu-id="92d6b-154">For non-public APIs, such as source code entirely in control by a developer or dev team - the nullable reference types could allow for the safe removal of null guards where the developers can guarantee it is not necessary.</span></span> <span data-ttu-id="92d6b-155">La funzionalità può essere utile per gli avvisi, ma non `NullReferenceException`può garantire che in fase di esecuzione l'esecuzione del codice possa generare un oggetto .</span><span class="sxs-lookup"><span data-stu-id="92d6b-155">The feature can help with warnings, but it cannot guarantee that at runtime code execution could result in a `NullReferenceException`.</span></span>

## <a name="breaking-changes"></a><span data-ttu-id="92d6b-156">Modifiche di rilievo</span><span class="sxs-lookup"><span data-stu-id="92d6b-156">Breaking changes</span></span>

<span data-ttu-id="92d6b-157">Gli avvisi non Null sono un'ovvia modifica di interruzione del codice esistente e devono essere accompagnati da un meccanismo di consenso esplicito.</span><span class="sxs-lookup"><span data-stu-id="92d6b-157">Non-null warnings are an obvious breaking change on existing code, and should be accompanied with an opt-in mechanism.</span></span>

<span data-ttu-id="92d6b-158">Meno ovviamente, gli avvisi da tipi nullable (come descritto in precedenza) sono una modifica sostanziale sul codice esistente in alcuni scenari in cui il supporto di valori Null è implicito:Less obviously, warnings from nullable types (as described above) are a breaking change on existing code in certain scenarios where the nullability is implicit:</span><span class="sxs-lookup"><span data-stu-id="92d6b-158">Less obviously, warnings from nullable types (as described above) are a breaking change on existing code in certain scenarios where the nullability is implicit:</span></span>

* <span data-ttu-id="92d6b-159">I parametri di tipo non vincolati verranno considerati `object` come nullable in `ToString` modo implicito, pertanto l'assegnazione a o l'accesso ad esempio produrrà avvisi.</span><span class="sxs-lookup"><span data-stu-id="92d6b-159">Unconstrained type parameters will be treated as implicitly nullable, so assigning them to `object` or accessing e.g. `ToString` will yield warnings.</span></span>
* <span data-ttu-id="92d6b-160">se l'inferenza del tipo `null` deduce nullità dalle espressioni, il codice esistente talvolta restituisce nullable anziché tipi non nullable, che possono causare nuovi avvisi.</span><span class="sxs-lookup"><span data-stu-id="92d6b-160">if type inference infers nullness from `null` expressions, then existing code will sometimes yield nullable rather than non-nullable types, which can lead to new warnings.</span></span>

<span data-ttu-id="92d6b-161">Pertanto, gli avvisi nullable devono essere facoltativi</span><span class="sxs-lookup"><span data-stu-id="92d6b-161">So nullable warnings also need to be optional</span></span>

<span data-ttu-id="92d6b-162">Infine, l'aggiunta di annotazioni a un'API esistente sarà una modifica sostanziale per gli utenti che hanno acconsentito esplicitamente agli avvisi, quando aggiornano la libreria.</span><span class="sxs-lookup"><span data-stu-id="92d6b-162">Finally, adding annotations to an existing API will be a breaking change to users who have opted in to warnings, when they upgrade the library.</span></span> <span data-ttu-id="92d6b-163">Anche questo merita la possibilità di aderire o uscire. "Voglio le correzioni di bug, ma non sono pronto ad affrontare le loro nuove annotazioni"</span><span class="sxs-lookup"><span data-stu-id="92d6b-163">This, too, merits the ability to opt in or out. "I want the bug fixes, but I am not ready to deal with their new annotations"</span></span>

<span data-ttu-id="92d6b-164">In sintesi, è necessario essere in grado di attivare/disattivare:</span><span class="sxs-lookup"><span data-stu-id="92d6b-164">In summary, you need to be able to opt in/out of:</span></span>
* <span data-ttu-id="92d6b-165">Avvisi nullableNullable warnings</span><span class="sxs-lookup"><span data-stu-id="92d6b-165">Nullable warnings</span></span>
* <span data-ttu-id="92d6b-166">Avvisi non Null</span><span class="sxs-lookup"><span data-stu-id="92d6b-166">Non-null warnings</span></span>
* <span data-ttu-id="92d6b-167">Avvisi delle annotazioni in altri file</span><span class="sxs-lookup"><span data-stu-id="92d6b-167">Warnings from annotations in other files</span></span>

<span data-ttu-id="92d6b-168">La granularità dell'opt-in suggerisce un modello simile a un analizzatore, in cui le fasce di codice possono attivare e disattivare con pragmas e i livelli di gravità possono essere scelti dall'utente.</span><span class="sxs-lookup"><span data-stu-id="92d6b-168">The granularity of the opt-in suggests an analyzer-like model, where swaths of code can opt in and out with pragmas and severity levels can be chosen by the user.</span></span> <span data-ttu-id="92d6b-169">Inoltre, le opzioni per libreria ("ignorare le annotazioni da JSON.NET fino a quando non sono pronto a gestire la caduta") possono essere esprimibili nel codice come attributi.</span><span class="sxs-lookup"><span data-stu-id="92d6b-169">Additionally, per-library options ("ignore the annotations from JSON.NET until I'm ready to deal with the fall out") may be expressible in code as attributes.</span></span>

<span data-ttu-id="92d6b-170">Il design dell'esperienza di opt-in/transizione è fondamentale per il successo e l'utilità di questa funzione.</span><span class="sxs-lookup"><span data-stu-id="92d6b-170">The design of the opt-in/transition experience is crucial to the success and usefulness of this feature.</span></span> <span data-ttu-id="92d6b-171">Dobbiamo assicurarci che:</span><span class="sxs-lookup"><span data-stu-id="92d6b-171">We need to make sure that:</span></span>

* <span data-ttu-id="92d6b-172">Gli utenti possono adottare il controllo della nullità gradualmente come vogliono</span><span class="sxs-lookup"><span data-stu-id="92d6b-172">Users can adopt nullability checking gradually as they want to</span></span>
* <span data-ttu-id="92d6b-173">Gli autori di librerie possono aggiungere annotazioni di supporto ai valori Null senza timore di interrompere i clienti</span><span class="sxs-lookup"><span data-stu-id="92d6b-173">Library authors can add nullability annotations without fear of breaking customers</span></span>
* <span data-ttu-id="92d6b-174">Nonostante questi, non c'è un senso di "incubo di configurazione"</span><span class="sxs-lookup"><span data-stu-id="92d6b-174">Despite these, there is not a sense of "configuration nightmare"</span></span>

## <a name="tweaks"></a><span data-ttu-id="92d6b-175">Tweaks</span><span class="sxs-lookup"><span data-stu-id="92d6b-175">Tweaks</span></span>

<span data-ttu-id="92d6b-176">Potremmo considerare di `?` non usare le annotazioni sulla gente del posto, ma solo osservando se vengono utilizzate in base a ciò che viene loro assegnato.</span><span class="sxs-lookup"><span data-stu-id="92d6b-176">We could consider not using the `?` annotations on locals, but just observing whether they are used in accordance with what gets assigned to them.</span></span> <span data-ttu-id="92d6b-177">Non lo preferisco; Penso che dovremmo lasciare che la gente esprima il loro intento.</span><span class="sxs-lookup"><span data-stu-id="92d6b-177">I don't favor this; I think we should uniformly let people express their intent.</span></span>

<span data-ttu-id="92d6b-178">Potremmo considerare una `T! x` scorciatoia sui parametri, che genera automaticamente un controllo null di runtime.</span><span class="sxs-lookup"><span data-stu-id="92d6b-178">We could consider a shorthand `T! x` on parameters, that auto-generates a runtime null check.</span></span>

<span data-ttu-id="92d6b-179">Alcuni modelli su tipi `FirstOrDefault` generici, ad esempio o `TryGet`, hanno un comportamento leggermente strano con argomenti di tipo non nullable, perché restituiscono in modo esplicito valori predefiniti in determinate situazioni.</span><span class="sxs-lookup"><span data-stu-id="92d6b-179">Certain patterns on generic types, such as `FirstOrDefault` or `TryGet`, have slightly weird behavior with non-nullable type arguments, because they explicitly yield default values in certain situations.</span></span> <span data-ttu-id="92d6b-180">Potremmo cercare di sfumare il sistema di tipo per accogliere questi meglio.</span><span class="sxs-lookup"><span data-stu-id="92d6b-180">We could try to nuance the type system to accommodate these better.</span></span> <span data-ttu-id="92d6b-181">Ad esempio, è `?` possibile consentire su parametri di tipo non vincolati, anche se l'argomento di tipo potrebbe già essere nullable.</span><span class="sxs-lookup"><span data-stu-id="92d6b-181">For instance, we could allow `?` on unconstrained type parameters, even though the type argument could already be nullable.</span></span> <span data-ttu-id="92d6b-182">Dubito che ne valga la pena, e porta a stranezze legate all'interazione con i tipi di *valore* nullable.</span><span class="sxs-lookup"><span data-stu-id="92d6b-182">I doubt that it is worth it, and it leads to weirdness related to interaction with nullable *value* types.</span></span> 

## <a name="nullable-value-types"></a><span data-ttu-id="92d6b-183">Tipi valore nullable</span><span class="sxs-lookup"><span data-stu-id="92d6b-183">Nullable value types</span></span>

<span data-ttu-id="92d6b-184">Si potrebbe prendere in considerazione l'adozione di alcune delle semantica di cui sopra per i tipi di valore nullable pure.</span><span class="sxs-lookup"><span data-stu-id="92d6b-184">We could consider adopting some of the above semantics for nullable value types as well.</span></span>

<span data-ttu-id="92d6b-185">Abbiamo già menzionato l'inferenza `int?` del `(7, null)`tipo, dove potremmo dedurre da , invece di dare solo un errore.</span><span class="sxs-lookup"><span data-stu-id="92d6b-185">We already mentioned type inference, where we could infer `int?` from `(7, null)`, instead of just giving an error.</span></span>

<span data-ttu-id="92d6b-186">Un'altra opportunità consiste nell'applicare l'analisi del flusso ai tipi di valore nullable.</span><span class="sxs-lookup"><span data-stu-id="92d6b-186">Another opportunity is to apply the flow analysis to nullable value types.</span></span> <span data-ttu-id="92d6b-187">Quando sono ritenuti non null, è possibile consentire l'utilizzo come tipo non nullable in alcuni modi (ad esempio l'accesso ai membri).</span><span class="sxs-lookup"><span data-stu-id="92d6b-187">When they are deemed non-null, we could actually allow using as the non-nullable type in certain ways (e.g. member access).</span></span> <span data-ttu-id="92d6b-188">Dobbiamo solo stare attenti che le cose che si possono *già* fare su un tipo di valore nullable sarà preferito, per motivi di compatibilità indietro.</span><span class="sxs-lookup"><span data-stu-id="92d6b-188">We just have to be careful that the things that you can *already* do on a nullable value type will be preferred, for back compat reasons.</span></span>
