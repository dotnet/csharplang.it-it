---
ms.openlocfilehash: 52b43abd2d8fb56088a68c7169289a63c43ce96f
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297477"
---
# <a name="suppress-emitting-of-localsinit-flag"></a><span data-ttu-id="5189c-101">Non consente l'emissione del `localsinit` flag.</span><span class="sxs-lookup"><span data-stu-id="5189c-101">Suppress emitting of `localsinit` flag.</span></span>

* <span data-ttu-id="5189c-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="5189c-102">[x] Proposed</span></span>
* <span data-ttu-id="5189c-103">[] Prototipo: non avviato</span><span class="sxs-lookup"><span data-stu-id="5189c-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="5189c-104">[] Implementazione: non avviata</span><span class="sxs-lookup"><span data-stu-id="5189c-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="5189c-105">[] Specifica: non avviata</span><span class="sxs-lookup"><span data-stu-id="5189c-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="5189c-106">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="5189c-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="5189c-107">Consente l'eliminazione dell'emissione del `localsinit` flag tramite l' `SkipLocalsInitAttribute` attributo.</span><span class="sxs-lookup"><span data-stu-id="5189c-107">Allow suppressing emit of `localsinit` flag via `SkipLocalsInitAttribute` attribute.</span></span> 

## <a name="motivation"></a><span data-ttu-id="5189c-108">Motivazione</span><span class="sxs-lookup"><span data-stu-id="5189c-108">Motivation</span></span>
[motivation]: #motivation


### <a name="background"></a><span data-ttu-id="5189c-109">Background</span><span class="sxs-lookup"><span data-stu-id="5189c-109">Background</span></span>
<span data-ttu-id="5189c-110">Le variabili locali per specifiche CLR che non contengono riferimenti non vengono inizializzate su un valore specifico dalla macchina virtuale/JIT.</span><span class="sxs-lookup"><span data-stu-id="5189c-110">Per CLR spec local variables that do not contain references are not initialized to a particular value by the VM/JIT.</span></span> <span data-ttu-id="5189c-111">La lettura da tali variabili senza inizializzazione è indipendente dai tipi, ma in caso contrario il comportamento non è definito e specifico dell'implementazione.</span><span class="sxs-lookup"><span data-stu-id="5189c-111">Reading from such variables without initialization is type-safe, but otherwise the behavior is undefined and implementation specific.</span></span> <span data-ttu-id="5189c-112">In genere, le variabili locali non inizializzate contengono tutti i valori rimasti nella memoria che ora è occupata dal stack frame.</span><span class="sxs-lookup"><span data-stu-id="5189c-112">Typically uninitialized locals contain whatever values were left in the memory that is now occupied by the stack frame.</span></span> <span data-ttu-id="5189c-113">Questo potrebbe portare a un comportamento non deterministico e a riprodurre bug.</span><span class="sxs-lookup"><span data-stu-id="5189c-113">That could lead to nondeterministic behavior and hard to reproduce bugs.</span></span> 

<span data-ttu-id="5189c-114">Esistono due modi per "assegnare" una variabile locale:</span><span class="sxs-lookup"><span data-stu-id="5189c-114">There are two ways to "assign" a local variable:</span></span> 
- <span data-ttu-id="5189c-115">archiviando un valore o</span><span class="sxs-lookup"><span data-stu-id="5189c-115">by storing a value or</span></span> 
- <span data-ttu-id="5189c-116">specificando `localsinit` il flag che impone che tutti gli elementi allocati formano il pool di memoria locale come nota inizializzata su zero: sono inclusi sia le variabili locali che `stackalloc` i dati.</span><span class="sxs-lookup"><span data-stu-id="5189c-116">by specifying `localsinit` flag which forces everything that is allocated form the local memory pool to be zero-initialized NOTE: this includes both local variables and `stackalloc` data.</span></span>    

<span data-ttu-id="5189c-117">L'utilizzo di dati non inizializzati è sconsigliato e non è consentito nel codice verificabile.</span><span class="sxs-lookup"><span data-stu-id="5189c-117">Use of uninitialized data is discouraged and is not allowed in verifiable code.</span></span> <span data-ttu-id="5189c-118">Sebbene sia possibile dimostrare che, per mezzo dell'analisi dei flussi, è consentito che l'algoritmo di verifica sia conservativo e che sia semplicemente necessario che `localsinit` sia impostato.</span><span class="sxs-lookup"><span data-stu-id="5189c-118">While it might be possible to prove that by the means of flow analysis, it is permitted for the verification algorithm to be conservative and simply require that `localsinit` is set.</span></span>

<span data-ttu-id="5189c-119">In passato il compilatore C# emette `localsinit` il flag su tutti i metodi che dichiarano variabili locali.</span><span class="sxs-lookup"><span data-stu-id="5189c-119">Historically C# compiler emits `localsinit` flag on all methods that declare locals.</span></span>

<span data-ttu-id="5189c-120">Mentre C# usa un'analisi di assegnazione definitiva più restrittiva rispetto a quella richiesta da CLR (C# deve anche considerare l'ambito delle variabili locali), non è assolutamente garantito che il codice risultante sia formalmente verificabile:</span><span class="sxs-lookup"><span data-stu-id="5189c-120">While C# employs definite-assignment analysis which is more strict than what CLR spec would require (C# also needs to consider scoping of locals), it is not strictly guaranteed that the resulting code would be formally verifiable:</span></span>
- <span data-ttu-id="5189c-121">Le regole di CLR e C# potrebbero non essere concordate se il passaggio di un argomento As locale `out` è un oggetto `use` .</span><span class="sxs-lookup"><span data-stu-id="5189c-121">CLR and C# rules may not agree on whether passing a local as `out` argument is a `use`.</span></span>
- <span data-ttu-id="5189c-122">Le regole di CLR e C# potrebbero non concordare sul trattamento dei rami condizionali quando le condizioni sono note (propagazione costante).</span><span class="sxs-lookup"><span data-stu-id="5189c-122">CLR and C# rules may not agree on treatment of conditional branches when conditions are known (constant propagation).</span></span>
- <span data-ttu-id="5189c-123">CLR potrebbe essere semplicemente necessario `localinits` , perché è consentito.</span><span class="sxs-lookup"><span data-stu-id="5189c-123">CLR could as well simply require `localinits`, since that is permitted.</span></span>  

### <a name="problem"></a><span data-ttu-id="5189c-124">Problema</span><span class="sxs-lookup"><span data-stu-id="5189c-124">Problem</span></span>
<span data-ttu-id="5189c-125">Nell'applicazione a prestazioni elevate il costo dell'inizializzazione zero forzata potrebbe essere evidente.</span><span class="sxs-lookup"><span data-stu-id="5189c-125">In high-performance application the cost of forced zero-initialization could be noticeable.</span></span> <span data-ttu-id="5189c-126">È particolarmente evidente quando `stackalloc` si usa.</span><span class="sxs-lookup"><span data-stu-id="5189c-126">It is particularly noticeable when `stackalloc` is used.</span></span>

<span data-ttu-id="5189c-127">In alcuni casi JIT può Elide l'inizializzazione zero iniziale delle singole variabili locali quando tale inizializzazione viene "terminata" da assegnazioni successive.</span><span class="sxs-lookup"><span data-stu-id="5189c-127">In some cases JIT can elide initial zero-initialization of individual locals when such initialization is "killed" by subsequent assignments.</span></span> <span data-ttu-id="5189c-128">Non tutti i JIT eseguono questa operazione e tale ottimizzazione ha limiti.</span><span class="sxs-lookup"><span data-stu-id="5189c-128">Not all JITs do this and such optimization has limits.</span></span> <span data-ttu-id="5189c-129">Non è utile per `stackalloc` .</span><span class="sxs-lookup"><span data-stu-id="5189c-129">It does not help with `stackalloc`.</span></span>

<span data-ttu-id="5189c-130">Per illustrare che il problema è reale, esiste un bug noto in cui un metodo che non contiene `IL` variabili locali non ha `localsinit` flag.</span><span class="sxs-lookup"><span data-stu-id="5189c-130">To illustrate that the problem is real - there is a known bug where a method not containing any `IL` locals would not have `localsinit` flag.</span></span> <span data-ttu-id="5189c-131">Il bug è già stato sfruttato dagli utenti inserendo `stackalloc` questi metodi, intenzionalmente per evitare i costi di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="5189c-131">The bug is already being exploited by users by putting `stackalloc` into such methods - intentionally to avoid initialization costs.</span></span> <span data-ttu-id="5189c-132">Ciò è dovuto al fatto che l'assenza di `IL` variabili locali è una metrica instabile e può variare a seconda delle modifiche apportate alla strategia codegen.</span><span class="sxs-lookup"><span data-stu-id="5189c-132">That is despite the fact that absence of `IL` locals is an unstable metric and may vary depending on changes in codegen strategy.</span></span> <span data-ttu-id="5189c-133">Il bug dovrebbe essere risolto e gli utenti dovrebbero ottenere un metodo più documentato e affidabile per l'eliminazione del flag.</span><span class="sxs-lookup"><span data-stu-id="5189c-133">The bug should be fixed and users should get a more documented and reliable way of suppressing the flag.</span></span> 

## <a name="detailed-design"></a><span data-ttu-id="5189c-134">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="5189c-134">Detailed design</span></span>

<span data-ttu-id="5189c-135">Consente `System.Runtime.CompilerServices.SkipLocalsInitAttribute` di specificare come metodo per indicare al compilatore di non emettere `localsinit` flag.</span><span class="sxs-lookup"><span data-stu-id="5189c-135">Allow specifying `System.Runtime.CompilerServices.SkipLocalsInitAttribute` as a way to tell the compiler to not emit `localsinit` flag.</span></span>
 
<span data-ttu-id="5189c-136">Il risultato finale sarà che le variabili locali non possono essere inizializzate a zero da JIT, che nella maggior parte dei casi non è osservabile in C#.</span><span class="sxs-lookup"><span data-stu-id="5189c-136">The end result of this will be that the locals may not be zero-initialized by the JIT, which is in most cases unobservable in C#.</span></span>  
<span data-ttu-id="5189c-137">Oltre a questi `stackalloc` dati non verranno inizializzati a zero.</span><span class="sxs-lookup"><span data-stu-id="5189c-137">In addition to that `stackalloc` data will not be zero-initialized.</span></span> <span data-ttu-id="5189c-138">Questo è decisamente osservabile, ma è anche lo scenario più motivante.</span><span class="sxs-lookup"><span data-stu-id="5189c-138">That is definitely observable, but also is the most motivating scenario.</span></span>

<span data-ttu-id="5189c-139">Le destinazioni degli attributi consentite e riconosciute sono: `Method` ,, `Property` `Module` , `Class` , `Struct` , `Interface` , `Constructor` .</span><span class="sxs-lookup"><span data-stu-id="5189c-139">Permitted and recognized attribute targets are: `Method`, `Property`, `Module`, `Class`, `Struct`, `Interface`, `Constructor`.</span></span> <span data-ttu-id="5189c-140">Tuttavia, il compilatore non richiede che l'attributo venga definito con le destinazioni elencate, né l'assembly in cui è definito l'attributo.</span><span class="sxs-lookup"><span data-stu-id="5189c-140">However compiler will not require that attribute is defined with the listed targets nor it will care in which assembly the attribute is defined.</span></span> 

<span data-ttu-id="5189c-141">Quando si specifica attribute in un contenitore ( `class` , `module` , che contiene il metodo per un metodo annidato,...), il flag influiscono su tutti i metodi contenuti nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="5189c-141">When attribute is specified on a container (`class`, `module`, containing method for a nested method, ...), the flag affects all methods contained within the container.</span></span>

<span data-ttu-id="5189c-142">I metodi sintetizzati "ereditano" il flag dal contenitore/proprietario logico.</span><span class="sxs-lookup"><span data-stu-id="5189c-142">Synthesized methods "inherit" the flag from the logical container/owner.</span></span> 

<span data-ttu-id="5189c-143">Il flag ha effetto solo sulla strategia codegen per i corpi dei metodi effettivi.</span><span class="sxs-lookup"><span data-stu-id="5189c-143">The flag affects only codegen strategy for actual method bodies.</span></span> <span data-ttu-id="5189c-144">I.E.</span><span class="sxs-lookup"><span data-stu-id="5189c-144">I.E.</span></span> <span data-ttu-id="5189c-145">il flag non ha alcun effetto sui metodi astratti e non viene propagato ai metodi di override e di implementazione.</span><span class="sxs-lookup"><span data-stu-id="5189c-145">the flag has no effect on abstract methods and is not propagated to overriding/implementing methods.</span></span>

<span data-ttu-id="5189c-146">Si tratta in modo esplicito di una **_funzionalità del compilatore_** e **_non di una funzionalità del linguaggio_**.</span><span class="sxs-lookup"><span data-stu-id="5189c-146">This is explicitly a **_compiler feature_** and **_not a language feature_**.</span></span>  
<span data-ttu-id="5189c-147">In modo analogo alla riga di comando del compilatore, la funzionalità controlla i dettagli di implementazione di una strategia di codegen specifica e non deve essere necessaria per la specifica C#.</span><span class="sxs-lookup"><span data-stu-id="5189c-147">Similarly to compiler command line switches the feature controls implementation details of a particular codegen strategy and does not need to be required by the C# spec.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="5189c-148">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="5189c-148">Drawbacks</span></span>
[drawbacks]: #drawbacks

- <span data-ttu-id="5189c-149">I compilatori precedenti/altri potrebbero non rispettare l'attributo.</span><span class="sxs-lookup"><span data-stu-id="5189c-149">Old/other compilers may not honor the attribute.</span></span>
<span data-ttu-id="5189c-150">L'attributo è un comportamento compatibile.</span><span class="sxs-lookup"><span data-stu-id="5189c-150">Ignoring the attribute is compatible behavior.</span></span> <span data-ttu-id="5189c-151">Solo può comportare una lieve hit delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="5189c-151">Only may result in a slight perf hit.</span></span>

- <span data-ttu-id="5189c-152">Il codice senza `localinits` flag può attivare errori di verifica.</span><span class="sxs-lookup"><span data-stu-id="5189c-152">The code without `localinits` flag may trigger verification failures.</span></span>
<span data-ttu-id="5189c-153">Gli utenti che richiedono questa funzionalità non sono in genere interessati alla verificabilità.</span><span class="sxs-lookup"><span data-stu-id="5189c-153">Users that ask for this feature are generally unconcerned with verifiability.</span></span> 
 
- <span data-ttu-id="5189c-154">L'applicazione dell'attributo a livelli superiori rispetto a un singolo metodo ha un effetto non locale, che è osservabile quando `stackalloc` si usa.</span><span class="sxs-lookup"><span data-stu-id="5189c-154">Applying the attribute at higher levels than an individual method has nonlocal effect, which is observable when `stackalloc` is used.</span></span> <span data-ttu-id="5189c-155">Tuttavia, questo è lo scenario più richiesto.</span><span class="sxs-lookup"><span data-stu-id="5189c-155">Yet, this is the most requested scenario.</span></span>

## <a name="alternatives"></a><span data-ttu-id="5189c-156">Alternativi</span><span class="sxs-lookup"><span data-stu-id="5189c-156">Alternatives</span></span>
[alternatives]: #alternatives

- <span data-ttu-id="5189c-157">omettere il `localinits` flag quando il metodo viene dichiarato nel `unsafe` contesto.</span><span class="sxs-lookup"><span data-stu-id="5189c-157">omit `localinits` flag when method is declared in `unsafe` context.</span></span> <span data-ttu-id="5189c-158">Questo potrebbe causare una modifica del comportamento invisibile all'utente e pericolosa da deterministica a non deterministica nel caso di `stackalloc` .</span><span class="sxs-lookup"><span data-stu-id="5189c-158">That could cause silent and dangerous behavior change from deterministic to nondeterministic in a case of `stackalloc`.</span></span>

- <span data-ttu-id="5189c-159">omettere `localinits` sempre il flag.</span><span class="sxs-lookup"><span data-stu-id="5189c-159">omit `localinits` flag always.</span></span>
<span data-ttu-id="5189c-160">Peggio ancora.</span><span class="sxs-lookup"><span data-stu-id="5189c-160">Even worse than above.</span></span>

- <span data-ttu-id="5189c-161">omettere `localinits` il flag a meno che non `stackalloc` venga usato nel corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="5189c-161">omit `localinits` flag unless `stackalloc` is used in the method body.</span></span>
<span data-ttu-id="5189c-162">Non risolve lo scenario più richiesto e può disabilitare il codice non verificabile senza che sia possibile ripristinarlo.</span><span class="sxs-lookup"><span data-stu-id="5189c-162">Does not address the most requested scenario and may turn code unverifiable with no option to revert that back.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="5189c-163">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="5189c-163">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="5189c-164">L'attributo deve essere effettivamente emesso ai metadati?</span><span class="sxs-lookup"><span data-stu-id="5189c-164">Should the attribute be actually emitted to metadata?</span></span> 

## <a name="design-meetings"></a><span data-ttu-id="5189c-165">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="5189c-165">Design meetings</span></span>

<span data-ttu-id="5189c-166">Nessuno ancora.</span><span class="sxs-lookup"><span data-stu-id="5189c-166">None yet.</span></span> 