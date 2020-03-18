---
ms.openlocfilehash: 922353d043653ddb651534a01f3fb98f85cd756e
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "79484482"
---
# <a name="readonly-locals-and-parameters"></a><span data-ttu-id="563be-101">variabili locali e parametri ReadOnly</span><span class="sxs-lookup"><span data-stu-id="563be-101">readonly locals and parameters</span></span>

* <span data-ttu-id="563be-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="563be-102">[x] Proposed</span></span>
* <span data-ttu-id="563be-103">[] Prototype</span><span class="sxs-lookup"><span data-stu-id="563be-103">[ ] Prototype</span></span>
* <span data-ttu-id="563be-104">[] Implementazione</span><span class="sxs-lookup"><span data-stu-id="563be-104">[ ] Implementation</span></span>
* <span data-ttu-id="563be-105">[] Specifica</span><span class="sxs-lookup"><span data-stu-id="563be-105">[ ] Specification</span></span>

## <a name="summary"></a><span data-ttu-id="563be-106">Summary</span><span class="sxs-lookup"><span data-stu-id="563be-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="563be-107">Consente di annotare variabili locali e parametri come ReadOnly per evitare la mutazione superficiale di tali variabili locali e parametri.</span><span class="sxs-lookup"><span data-stu-id="563be-107">Allow locals and parameters to be annotated as readonly in order to prevent shallow mutation of those locals and parameters.</span></span>

## <a name="motivation"></a><span data-ttu-id="563be-108">Motivazione</span><span class="sxs-lookup"><span data-stu-id="563be-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="563be-109">Attualmente, è possibile applicare la parola chiave `readonly` ai campi; Questo ha l'effetto di garantire che un campo possa essere scritto durante la costruzione (costruzione statica nel caso di un campo statico o di una costruzione dell'istanza nel caso di un campo di istanza), che consente agli sviluppatori di evitare errori sovrascrivendo accidentalmente lo stato che non deve essere modificato.</span><span class="sxs-lookup"><span data-stu-id="563be-109">Today, the `readonly` keyword can be applied to fields; this has the effect of ensuring that a field can only be written to during construction (static construction in the case of a static field, or instance construction in the case of an instance field), which helps developers avoid mistakes by accidentally overwriting state which should not be modified.</span></span> <span data-ttu-id="563be-110">Ma i campi non sono gli unici a cui gli sviluppatori vogliono assicurare che i valori non vengano mutati.</span><span class="sxs-lookup"><span data-stu-id="563be-110">But fields aren't the only places developers want to ensure that values aren't mutated.</span></span> <span data-ttu-id="563be-111">In particolare, è normale creare una variabile locale per archiviare lo stato temporaneo e aggiornare accidentalmente tale stato temporaneo può comportare calcoli errati e altri bug, soprattutto quando tali "variabili locali" vengono acquisite nelle espressioni lambda, a quel punto vengono innalzate di livello ai campi, ma non esiste alcun modo per contrassegnare tali campi come `readonly`.</span><span class="sxs-lookup"><span data-stu-id="563be-111">In particular, it's common to create a local variable to store temporary state, and accidentally updating that temporary state can result in erroneous calculations and other such bugs, especially when such "locals" are captured in lambdas, at which point they are lifted to fields, but there's no way today to mark such lifted fields as `readonly`.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="563be-112">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="563be-112">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="563be-113">Le variabili locali saranno annotabili come `readonly` e il compilatore assicurerà che vengano impostate solo al momento della dichiarazione (alcune variabili locali in C# sono già implicitamente ReadOnly, ad esempio la variabile di iterazione in un ciclo ' foreach ' o la variabile utilizzata in un blocco ' using ', ma attualmente lo sviluppatore non è in grado di contrassegnare altre variabili locali come `readonly`).</span><span class="sxs-lookup"><span data-stu-id="563be-113">Locals will be annotatable as `readonly` as well, with the compiler ensuring that they're only set at the time of declaration (certain locals in C# are already implicitly readonly, such as the iteration variable in a 'foreach' loop or the used variable in a 'using' block, but currently a developer has no ability to mark other locals as `readonly`).</span></span> <span data-ttu-id="563be-114">Tali variabili locali `readonly` devono disporre di un inizializzatore:</span><span class="sxs-lookup"><span data-stu-id="563be-114">Such `readonly` locals must have an initializer:</span></span>

```csharp
readonly long maxBytesToDelete = (stream.LimitBytes - stream.MaxBytes) / 10;
...
maxBytesToDelete = 0; // Error: can't assign to readonly locals outside of declaration
```

<span data-ttu-id="563be-115">E come abbreviazione per `readonly var`, è possibile usare la parola chiave contestuale esistente `let`, ad esempio</span><span class="sxs-lookup"><span data-stu-id="563be-115">And as shorthand for `readonly var`, the existing contextual keyword `let` may be used, e.g.</span></span>

```csharp
let maxBytesToDelete = (stream.LimitBytes - stream.MaxBytes) / 10;
...
maxBytesToDelete = 0; // Error: can't assign to readonly locals outside of declaration
```

<span data-ttu-id="563be-116">Non esistono vincoli speciali per ciò che può essere l'inizializzatore e possono essere qualsiasi elemento attualmente valido come inizializzatore per le variabili locali, ad esempio</span><span class="sxs-lookup"><span data-stu-id="563be-116">There are no special constraints for what the initializer can be, and can be anything currently valid as an initializer for locals, e.g.</span></span>

```csharp
readonly T data = arg1 ?? arg2;
```

<span data-ttu-id="563be-117">`readonly` sulle variabili locali è particolarmente utile quando si lavora con le espressioni lambda e le chiusure.</span><span class="sxs-lookup"><span data-stu-id="563be-117">`readonly` on locals is particularly valuable when working with lambdas and closures.</span></span> <span data-ttu-id="563be-118">Quando un metodo anonimo o un'espressione lambda accede allo stato locale dall'ambito che lo contiene, questo stato viene acquisito in una chiusura dal compilatore, che è rappresentato da una "classe di visualizzazione".</span><span class="sxs-lookup"><span data-stu-id="563be-118">When an anonymous method or lambda accesses local state from the enclosing scope, that state is captured into a closure by the compiler, which is represented by a "display class."</span></span>  <span data-ttu-id="563be-119">Ogni "local" acquisito è un campo di questa classe, ma poiché il compilatore genera questo campo per conto dell'utente, non è possibile annotarlo come `readonly` per impedire che l'espressione lambda scriva erroneamente in "local" (tra virgolette perché in realtà non è locale, almeno non nel codice MSIL risultante).</span><span class="sxs-lookup"><span data-stu-id="563be-119">Each "local" that's captured is a field in this class, yet because the compiler is generating this field on your behalf, you have no opportunity to annotate it as `readonly` in order to prevent the lambda from erroneously writing to the "local" (in quotes because it's really not a local, at least not in the resulting MSIL).</span></span> <span data-ttu-id="563be-120">Con `readonly` variabili locali, il compilatore può impedire che l'espressione lambda scriva in locale, che è particolarmente utile in scenari che coinvolgono il multithreading in cui una scrittura errata potrebbe causare un bug di concorrenza pericoloso ma raro e difficile da trovare.</span><span class="sxs-lookup"><span data-stu-id="563be-120">With `readonly` locals, the compiler can prevent the lambda from writing to local, which is particularly valuable in scenarios involving multithreading where an erroneous write could result in a dangerous but rare and hard-to-find concurrency bug.</span></span>

```csharp
readonly long index = ...;
Parallel.ForEach(data, item => {
    T element = item[index];
    index = 0; // Error: can't assign to readonly locals outside of declaration
});
```

<span data-ttu-id="563be-121">Come forma speciale di local, i parametri saranno anche annotabili come `readonly`.</span><span class="sxs-lookup"><span data-stu-id="563be-121">As a special form of local, parameters will also be annotatable as `readonly`.</span></span> <span data-ttu-id="563be-122">Questo non avrebbe alcun effetto su ciò che il chiamante del metodo è in grado di passare al parametro (proprio come non esiste alcun vincolo sui valori che possono essere archiviati in un campo di `readonly`), ma come per qualsiasi `readonly` locale, il compilatore impedisce al codice di scrivere nel parametro dopo la dichiarazione, il che significa che il corpo del metodo non è consentito per la scrittura nel parametro.</span><span class="sxs-lookup"><span data-stu-id="563be-122">This would have no effect on what the caller of the method is able to pass to the parameter (just as there's no constraint on what values may be stored into a `readonly` field), but as with any `readonly` local, the compiler would prohibit code from writing to the parameter after declaration, which means the body of the method is prohibited from writing to the parameter.</span></span>

```csharp
public void Update(readonly int index = 0) // Default values are ok though not required
{
    ...
    index = 0; // Error: can't assign to readonly parameters
    ...
}
```

<span data-ttu-id="563be-123">`readonly` parametri non influiscono sulla firma o sui metadati generati dal compilatore per il metodo e influiscono semplicemente sul modo in cui il compilatore gestisce la compilazione del corpo del metodo.</span><span class="sxs-lookup"><span data-stu-id="563be-123">`readonly` parameters do not affect the signature/metadata emitted by the compiler for that method, and simply affect how the compiler handles the compilation of the method's body.</span></span> <span data-ttu-id="563be-124">Quindi, ad esempio, un metodo virtuale di base potrebbe avere un parametro di `readonly` e tale parametro potrebbe essere scrivibile in una sostituzione.</span><span class="sxs-lookup"><span data-stu-id="563be-124">Thus, for example, a base virtual method could have a `readonly` parameter, and that parameter could be writable in an override.</span></span>

<span data-ttu-id="563be-125">Come per i campi, `readonly` per variabili locali e parametri è superficiale, che influisce sul percorso di archiviazione, ma non influisce in modo transitivo sull'oggetto grafico.</span><span class="sxs-lookup"><span data-stu-id="563be-125">As with fields, `readonly` for locals and parameters is shallow, affecting the storage location but not transitively affecting the object graph.</span></span> <span data-ttu-id="563be-126">Tuttavia, anche come con i campi, la chiamata di un metodo su un `readonly` struct locale/parametro effettuerà effettivamente una copia dello struct e chiamerà il metodo sulla copia, per evitare mutazioni interne di `this`.</span><span class="sxs-lookup"><span data-stu-id="563be-126">However, also as with fields, calling a method on a `readonly` local/parameter struct will actually make a copy of the struct and call the method on the copy, in order to avoid internal mutation of `this`.</span></span>

<span data-ttu-id="563be-127">`readonly` variabili locali e i parametri non possono essere passati come argomenti `ref` o `out`, a meno che non sia supportata anche la `ref readonly`.</span><span class="sxs-lookup"><span data-stu-id="563be-127">`readonly` locals and parameters can't be passed as `ref` or `out` arguments, unless/until `ref readonly` is also supported.</span></span>

## <a name="alternatives"></a><span data-ttu-id="563be-128">Alternativi</span><span class="sxs-lookup"><span data-stu-id="563be-128">Alternatives</span></span>
[alternatives]: #alternatives

- <span data-ttu-id="563be-129">`val` possibile utilizzare come una forma abbreviata alternativa a `let`.</span><span class="sxs-lookup"><span data-stu-id="563be-129">`val` could be used as an alternative shorthand to `let`.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="563be-130">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="563be-130">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="563be-131">`readonly ref` / `ref readonly` / `readonly ref readonly`: ho lasciato la questione di come gestire `ref readonly` come separato da questa proposta.</span><span class="sxs-lookup"><span data-stu-id="563be-131">`readonly ref` / `ref readonly` / `readonly ref readonly`: I've left the question of how to handle `ref readonly` as separate from this proposal.</span></span>
- <span data-ttu-id="563be-132">Questa proposta non affronta gli struct di sola lettura/i tipi non modificabili.</span><span class="sxs-lookup"><span data-stu-id="563be-132">This proposal does not tackle readonly structs / immutable types.</span></span> <span data-ttu-id="563be-133">Che rimane per una proposta separata.</span><span class="sxs-lookup"><span data-stu-id="563be-133">That is left for a separate proposal.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="563be-134">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="563be-134">Design meetings</span></span>

- <span data-ttu-id="563be-135">Descritto brevemente il 21 gennaio 2015 (<https://github.com/dotnet/roslyn/issues/98>)</span><span class="sxs-lookup"><span data-stu-id="563be-135">Briefly discussed on Jan 21, 2015 (<https://github.com/dotnet/roslyn/issues/98>)</span></span>
