---
ms.openlocfilehash: 4096cd28e30fdffeac1ee3d8578343e51282f6f1
ms.sourcegitcommit: 77b2ab88a0091333c11e647d47d5c3c7e94d4e7c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452317"
---
<a name="low-level-struct-improvements"></a><span data-ttu-id="a0be6-101">Miglioramenti allo struct di basso livello</span><span class="sxs-lookup"><span data-stu-id="a0be6-101">Low Level Struct Improvements</span></span>
=====

## <a name="summary"></a><span data-ttu-id="a0be6-102">Summary</span><span class="sxs-lookup"><span data-stu-id="a0be6-102">Summary</span></span>
<span data-ttu-id="a0be6-103">Questa proposta è un'aggregazione di diverse proposte per `struct` migliorare le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="a0be6-103">This proposal is an aggregation of several different proposals for `struct` performance improvements.</span></span> <span data-ttu-id="a0be6-104">L'obiettivo è una progettazione che prende in considerazione le diverse proposte per creare un singolo set di funzionalità di base per i `struct` miglioramenti.</span><span class="sxs-lookup"><span data-stu-id="a0be6-104">The goal being a design which takes into account the various proposals to create a single overarching feature set for `struct` improvements.</span></span>

## <a name="motivation"></a><span data-ttu-id="a0be6-105">Motivazione</span><span class="sxs-lookup"><span data-stu-id="a0be6-105">Motivation</span></span>
<span data-ttu-id="a0be6-106">Nelle ultime versioni di C# sono state aggiunte diverse funzionalità per le prestazioni di basso livello al linguaggio: `ref` restituisce, `ref struct` , puntatori a funzione e così via... Questi sviluppatori .NET hanno consentito di creare codice a elevate prestazioni, continuando a sfruttare le regole del linguaggio C# per la sicurezza del tipo e della memoria.</span><span class="sxs-lookup"><span data-stu-id="a0be6-106">Over the last few releases C# has added a number of low level performance features to the language: `ref` returns, `ref struct`, function pointers, etc. ... These enabled .NET developers to create write highly performant code while continuing to leverage the C# language rules for type and memory safety.</span></span>
<span data-ttu-id="a0be6-107">È stata inoltre consentita la creazione di tipi di prestazioni fondamentali nelle librerie .NET, ad esempio `Span<T>` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-107">It also allowed the creation of fundamental performance types in the .NET libraries like `Span<T>`.</span></span>

<span data-ttu-id="a0be6-108">Dato che queste funzionalità hanno acquisito la trazione negli sviluppatori dell'ecosistema .NET, sia interne che esterne, ci hanno fornito informazioni sui punti di attrito rimanenti nell'ecosistema.</span><span class="sxs-lookup"><span data-stu-id="a0be6-108">As these features have gained traction in the .NET ecosystem developers, both internal and external, have been providing us with information on remaining friction points in the ecosystem.</span></span> <span data-ttu-id="a0be6-109">Posizioni in cui è ancora necessario eliminare `unsafe` il codice per ottenere il proprio lavoro o richiedere il runtime a tipi speciali di casi come `Span<T>` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-109">Places where they still need to drop to `unsafe` code to get their work, or require the runtime to special case types like `Span<T>`.</span></span> 

<span data-ttu-id="a0be6-110">Questa proposta mira a risolvere molti di questi problemi grazie alla creazione di una funzionalità di basso livello esistente.</span><span class="sxs-lookup"><span data-stu-id="a0be6-110">This proposal aims to address many of these concerns by building on top of our existing low level features.</span></span> <span data-ttu-id="a0be6-111">In particolare, mira a:</span><span class="sxs-lookup"><span data-stu-id="a0be6-111">Specifically it aims to:</span></span>

- <span data-ttu-id="a0be6-112">Consente `ref struct` ai tipi di dichiarare i `ref` campi.</span><span class="sxs-lookup"><span data-stu-id="a0be6-112">Allow `ref struct` types to declare `ref` fields.</span></span>
- <span data-ttu-id="a0be6-113">Consentire al runtime di definire completamente `Span<T>` usando il sistema di tipi C# e rimuovere il tipo di caso speciale come `ByReference<T>`</span><span class="sxs-lookup"><span data-stu-id="a0be6-113">Allow the runtime to fully define `Span<T>` using the C# type system and remove special case type like `ByReference<T>`</span></span>
- <span data-ttu-id="a0be6-114">Consente `struct` ai tipi di tornare `ref` ai campi.</span><span class="sxs-lookup"><span data-stu-id="a0be6-114">Allow `struct` types to return `ref` to their fields.</span></span>
- <span data-ttu-id="a0be6-115">Consenti la dichiarazione di `fixed` buffer sicuri per i tipi gestiti e non gestiti in `struct`</span><span class="sxs-lookup"><span data-stu-id="a0be6-115">Allow the declaration of safe `fixed` buffers for managed and unmanaged types in `struct`</span></span>

## <a name="detailed-design"></a><span data-ttu-id="a0be6-116">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="a0be6-116">Detailed Design</span></span> 
<span data-ttu-id="a0be6-117">Le regole per la `ref struct` sicurezza sono definite nel [documento span-Safety](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.2/span-safety.md).</span><span class="sxs-lookup"><span data-stu-id="a0be6-117">The rules for `ref struct` safety are defined in the [span-safety document](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.2/span-safety.md).</span></span> <span data-ttu-id="a0be6-118">In questo documento vengono descritte le modifiche necessarie a questo documento in seguito a questa proposta.</span><span class="sxs-lookup"><span data-stu-id="a0be6-118">This document will describe the required changes to this document as a result of this proposal.</span></span> <span data-ttu-id="a0be6-119">Una volta accettata come funzionalità approvata, queste modifiche verranno incorporate nel documento.</span><span class="sxs-lookup"><span data-stu-id="a0be6-119">Once accepted as an approved feature these changes will be incorporated into that document.</span></span>

### <a name="provide-ref-fields"></a><span data-ttu-id="a0be6-120">Specificare i campi di riferimento</span><span class="sxs-lookup"><span data-stu-id="a0be6-120">Provide ref fields</span></span>
<span data-ttu-id="a0be6-121">Il linguaggio consentirà agli sviluppatori di dichiarare i `ref` campi all'interno di un `ref struct` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-121">The language will allow developers to declare `ref` fields inside of a `ref struct`.</span></span> <span data-ttu-id="a0be6-122">Questa operazione può essere utile ad esempio quando si incapsulano istanze modificabili di grandi dimensioni `struct` o si definiscono tipi a prestazioni elevate come `Span<T>` nelle librerie oltre al runtime.</span><span class="sxs-lookup"><span data-stu-id="a0be6-122">This can be useful for example when encapsulating large mutable `struct` instances or defining high performance types like `Span<T>` in libraries besides the runtime.</span></span>

<span data-ttu-id="a0be6-123">Attualmente i `ref` campi vengono eseguiti nel runtime usando il `ByReference<T>` tipo che il runtime considera effettivo come un `ref` campo.</span><span class="sxs-lookup"><span data-stu-id="a0be6-123">Today `ref` fields accomplished in the runtime by using the `ByReference<T>` type which the runtime treats effective as a `ref` field.</span></span> <span data-ttu-id="a0be6-124">Ciò significa tuttavia che solo il repository di runtime può sfruttare appieno il `ref` comportamento del campo come e che tutti gli utilizzi di esso richiedono la verifica manuale della sicurezza.</span><span class="sxs-lookup"><span data-stu-id="a0be6-124">This means though that only the runtime repository can take full advantage of `ref` field like behavior and all uses of it require manual verification of safety.</span></span> <span data-ttu-id="a0be6-125">Parte della [motivazione di questo lavoro](https://github.com/dotnet/runtime/issues/32060) consiste nel rimuovere `ByReference<T>` e usare `ref` i campi appropriati in tutte le basi di codice.</span><span class="sxs-lookup"><span data-stu-id="a0be6-125">Part of the [motivation for this work](https://github.com/dotnet/runtime/issues/32060) is to remove `ByReference<T>` and use proper `ref` fields in all code bases.</span></span> <span data-ttu-id="a0be6-126">La parte complessa per consentire le `ref` dichiarazioni dei campi è la definizione di regole che `Span<T>` possono essere definite utilizzando `ref` campi senza interruzioni di compatibilità con il codice esistente.</span><span class="sxs-lookup"><span data-stu-id="a0be6-126">The challenging part about allowing `ref` fields declarations though comes in defining rules such that `Span<T>` can be defined using `ref` fields without breaking compatibility with existing code.</span></span> 

<span data-ttu-id="a0be6-127">Prima di immergersi nei problemi, è opportuno notare che i `ref` campi richiedono solo un numero ridotto di modifiche mirate alle regole di sicurezza dell'intervallo esistenti.</span><span class="sxs-lookup"><span data-stu-id="a0be6-127">Before diving into the problems here it should be noted that `ref` fields only require a small number of targeted changes to our existing span safety rules.</span></span> <span data-ttu-id="a0be6-128">In alcuni casi non è nemmeno possibile supportare nuove funzionalità, ma razionalizzare l' `Span<T>` utilizzo dei dati esistenti `ref` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-128">In some cases it's not even to support new features but to rationalize our existing `Span<T>` usage of `ref` data.</span></span> <span data-ttu-id="a0be6-129">Questa sezione della proposta è piuttosto complessa perché mi sembra importante comunicare il "perché" di queste modifiche nel maggior dettaglio possibile e fornire esempi di supporto.</span><span class="sxs-lookup"><span data-stu-id="a0be6-129">This section of the proposal is quite involved though because I feel it's important to communicate the "why" of these changes in as much detail as possible and providing supporting samples.</span></span> <span data-ttu-id="a0be6-130">In questo modo si garantisce che le modifiche siano valide, oltre a offrire agli sviluppatori futuri una migliore comprensione delle scelte effettuate in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="a0be6-130">This is to both ensure the changes are sound as well as giving future developers a better understanding of the choices made here.</span></span>

<span data-ttu-id="a0be6-131">Per comprendere i problemi, è opportuno prendere in considerazione il modo in cui `Span<T>` verranno esaminati i campi una volta `ref` supportati.</span><span class="sxs-lookup"><span data-stu-id="a0be6-131">To understand the challenges here let's first consider how `Span<T>` will look once `ref` fields are supported.</span></span>

```cs
// This is the eventual definition of Span<T> once we add ref fields into the
// language
readonly ref struct Span<T>
{
    ref readonly T _field;
    int _length;

    // This constructor does not exist today however will be added as a
    // part of changing Span<T> to have ref fields. It is a convenient, and
    // safe, way to create a length one span over a stack value that today 
    // requires unsafe code.
    public Span(ref T value)
    {
        ref _field = ref value;
        _length = 1;
    }
}
```

<span data-ttu-id="a0be6-132">Il costruttore definito qui presenta un problema perché i valori restituiti devono necessariamente avere durate limitate per molti input.</span><span class="sxs-lookup"><span data-stu-id="a0be6-132">The constructor defined here presents a problem because its return values must necessarily have restricted lifetimes for many inputs.</span></span> <span data-ttu-id="a0be6-133">Si consideri che se un parametro locale viene passato da `ref` a questo costruttore, l'oggetto restituito `Span<T>` deve avere un ambito *sicuro per l'escape* dell'ambito della dichiarazione locale.</span><span class="sxs-lookup"><span data-stu-id="a0be6-133">Consider that if a local parameter is passed by `ref` into this constructor that the returned `Span<T>` must have a *safe-to-escape* scope of the local's declaration scope.</span></span>

```cs
Span<int> CreatingAndReturningSpan()
{
    int i = 42;

    // This must be an error in the new design because it stores stack 
    // state in the Span. 
    return new Span<int>(ref i);

    // This must be legal in the new design because it is legal today (it 
    // cannot store stack state)
    return new Span<int>(new int[] { });
}
```

<span data-ttu-id="a0be6-134">Allo stesso tempo è lecito avere attualmente metodi che accettano un `ref` parametro e restituiscono un oggetto `Span<T>` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-134">At the same time it is legal to have methods today which take a `ref` parameter and return a `Span<T>`.</span></span>  <span data-ttu-id="a0be6-135">Questi metodi hanno una grande somiglianza con il costruttore appena aggiunto `Span<T>` , ovvero accettano `ref` , restituiscono `Span<T>` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-135">These methods bear a lot of similarity to the newly added `Span<T>` constructor: take a `ref`, return a `Span<T>`.</span></span> <span data-ttu-id="a0be6-136">Tuttavia, la durata del valore restituito di questi metodi non viene mai limitata dagli input.</span><span class="sxs-lookup"><span data-stu-id="a0be6-136">However the lifetime of the return value of these methods is never restricted by the inputs.</span></span>
<span data-ttu-id="a0be6-137">Le regole di sicurezza dell'intervallo esistenti considerano tali valori come sempre *sicuri* all'esterno del metodo contenitore.</span><span class="sxs-lookup"><span data-stu-id="a0be6-137">The existing span safety rules consider such values as effectively always *safe-to-escape* outside the enclosing method.</span></span>

```cs
class ExistingScenarios
{
    Span<T> CreateSpan<T>(ref T p)
    {
        // The implementation of this method is irrelevant. From the point of
        // the consumer the returned value is always safe to return.
        ... 
    }

    Span<T> Examples<T>(ref T p, T[] array)
    {
        // Legal today
        return CreateSpan(ref p); 

        // Legal today, must remain legal
        T local = default;
        return CreateSpan(ref local);

        // Legal for any possible value that could be used as an argument
        return CreateSpan(...);
    }
}
```

<span data-ttu-id="a0be6-138">Il motivo per cui tutti gli esempi precedenti sono validi è il fatto che nella progettazione esistente non esiste alcun modo per restituire `Span<T>` un riferimento allo stato di input della chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="a0be6-138">The reason that all of the above samples are legal is because in the existing design there is no way for the return `Span<T>` to store a reference to the input state of the method call.</span></span> <span data-ttu-id="a0be6-139">Ciò è dovuto al fatto che le regole span Safety dipendono in modo esplicito da `Span<T>` non avere un costruttore che accetta un `ref` parametro e lo archivia come campo.</span><span class="sxs-lookup"><span data-stu-id="a0be6-139">This is because the span safety rules explicitly depend on `Span<T>` not having a constructor which takes a `ref` parameter and stores it as a field.</span></span> 

```cs
class ExistingAssumptions
{
    Span<T> CreateSpan<T>(ref T p)
    {
        // !!! Cannot happen today !!!
        // The existing span safety rules specifically call out that this method
        // cannot exist hence they can assume all returns from CreateSpan are
        // safe to return.
        return new Span<T>(ref p);
    }
}
```

<span data-ttu-id="a0be6-140">Le regole definite per i `ref` campi devono garantire `Span<T>` che il costruttore limiti correttamente l'ambito di *sicurezza da Escape* degli oggetti costruiti nei casi in cui acquisisce `ref` lo stato.</span><span class="sxs-lookup"><span data-stu-id="a0be6-140">The rules we define for `ref` fields must ensure the `Span<T>` constructor properly restricts the *safe-to-escape* scope of constructed objects in the cases it captures `ref` state.</span></span> <span data-ttu-id="a0be6-141">Allo stesso tempo, è necessario assicurarsi di non interrompere le regole di utilizzo esistenti per metodi quali `CreateSpan<T>` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-141">At the same time it must ensure that we don't break the existing consumption rules for methods like `CreateSpan<T>`.</span></span> 

```cs
class GoalOfRefFields
{
    Span<T> CreateSpan<T>(ref T p)
    {
        // ERROR: the existing consumption rules for CreateSpan believe this 
        // can never happen hence we must continue to enforce that it cannot
        return new Span<T>(ref p);

        // Okay: this is legal today
        return new Span<int>(new int[] { });
    }

    Span<int> ConsumptionCompatibility()
    {
        // Okay: this is legal today and must remain legal.
        int local = 42;
        return CreateSpan(ref local);

        // Okay: the arguments don't actually matter here. Literally any value 
        // could be passed to this method and the return of it would still be 
        // *safe-to-escape* outside the enclosing method. 
        return CreateSpan(...);
    }
}
```

<span data-ttu-id="a0be6-142">Questa tensione tra consentire a costruttori come `Span<T>(ref T field)` e garantire la compatibilità con `ref struct` metodi di restituzione come `CreateSpan<T>` è un punto pivot chiave nella progettazione dei `ref` campi.</span><span class="sxs-lookup"><span data-stu-id="a0be6-142">This tension between allowing constructors such as `Span<T>(ref T field)` and ensuring compatibility with `ref struct` returning methods like `CreateSpan<T>` is a key pivot point in the design of `ref` fields.</span></span>

<span data-ttu-id="a0be6-143">A tale scopo, verranno modificate le regole di escape per una chiamata al costruttore, che oggi corrisponde alla chiamata al metodo, in un oggetto `ref struct` che contiene **direttamente** un `ref` campo come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="a0be6-143">To do this we will change the escape rules for a constructor invocation, which today are the same as method invocation, on a `ref struct` that **directly** contains a `ref` field as follows:</span></span>
- <span data-ttu-id="a0be6-144">Se il costruttore contiene `ref` parametri, `out` o `in` e gli argomenti non fanno tutti riferimento all'heap, il carattere di *escape sicuro* del risultato sarà l'ambito corrente</span><span class="sxs-lookup"><span data-stu-id="a0be6-144">If the constructor contains any `ref`, `out` or `in` parameters, and the arguments do not all refer to the heap, then the *safe-to-escape* of the return will be the current scope</span></span>
- <span data-ttu-id="a0be6-145">Altrimenti, se il costruttore contiene `ref struct` parametri, il livello di *sicurezza di escape* del restituito sarà l'ambito corrente</span><span class="sxs-lookup"><span data-stu-id="a0be6-145">Else if the constructor contains any `ref struct` parameters then the *safe-to-escape* of the return will be the current scope</span></span>
- <span data-ttu-id="a0be6-146">Altrimenti il *safe-to-escape* sarà l'esterno dell'ambito del metodo</span><span class="sxs-lookup"><span data-stu-id="a0be6-146">Else the *safe-to-escape* will be the outside the method scope</span></span>

<span data-ttu-id="a0be6-147">Esaminiamo queste regole nel contesto degli esempi per comprendere meglio il loro effetto.</span><span class="sxs-lookup"><span data-stu-id="a0be6-147">Let's examine these rules in the context of samples to better understand their impact.</span></span>

```cs
ref struct RS
{
    ref int _field;

    public RS(int[] array, int index)
    {
        ref _field = ref array[index];
    }

    public RS(ref int i)
    {
        ref _field = ref i;
    }

    static RS CreateRS(ref int i)
    {
        // The implementation of this method is irrelevant to the safety rule
        // examples below. The returned value is always *safe-to-escape* outside
        // the enclosing method scope
    }

    static RS RuleExamples(ref int i, int[] array)
    {
        var rs1 = new RS(ref i);

        // ERROR by bullet 1: the safe-to-escape scope of 'rs1' is the current
        // scope.
        return rs1; 

        var rs2 = new RS(array, 0);

        // Okay by bullet 2: the safe-to-escape scope of 'rs2' is outside the 
        // method scope.
        return rs2; 

        int local = 42;

        // ERROR by bullet 1: the safe-to-escape scope is the current scope
        return new RS(ref local);
        return new RS(ref i);

        // Okay because rules for method calls have not changed. This is legal
        // today hence it must be legal in the presence of ref fields.
        return CreateRS(ref local);
        return CreateRS(ref i);
    }
}
```

<span data-ttu-id="a0be6-148">È importante notare che, ai fini della regola sopra qualsiasi utilizzo del concatenamento del costruttore tramite `this` , viene considerata una chiamata al costruttore.</span><span class="sxs-lookup"><span data-stu-id="a0be6-148">It is important to note that for the purposes of the rule above any use of constructor chaining via `this` is considered a constructor invocation.</span></span> <span data-ttu-id="a0be6-149">Il risultato della chiamata al costruttore concatenato viene considerato come un ritorno al costruttore originale, quindi le regole *sicure di escape* entrano in gioco.</span><span class="sxs-lookup"><span data-stu-id="a0be6-149">The result of the chained constructor call is considered to be returning to the original constructor hence *safe-to-escape* rules come into play.</span></span> <span data-ttu-id="a0be6-150">Questo è importante per evitare esempi non sicuri come i seguenti:</span><span class="sxs-lookup"><span data-stu-id="a0be6-150">That is important in avoiding unsafe examples like the following:</span></span>

```cs
ref struct RS1
{
    ref int _field;
    public RS1(ref int p)
    {
        ref _field = ref p;
    }
}

ref struct RS2
{
    RS1 _field;
    public RS2(RS1 p)
    {
        // Okay
        _field = p;
    }

    public RS2(ref int i)
    {
        // ERROR: The *safe-to-escape* scope of the constructor here is the 
        // current method scope while the *safe-to-escape* scope of `this` is
        // outside the current method scope hence this assignment is illegal
        _field = new RS1(ref i);
    }

    public RS2(ref int i)  
        // ERROR: the *safe-to-escape* return of :this the current method scope
        // but the 'this' parameter has a *safe-to-escape* outside the current
        // method scope
        : this(new RS1(ref i))
    {

    }
}
```

<span data-ttu-id="a0be6-151">La limitazione delle regole del costruttore a solo `ref struct` che contengono direttamente il `ref` campo è un altro importante problema di compatibilità.</span><span class="sxs-lookup"><span data-stu-id="a0be6-151">The limiting of the constructor rules to just `ref struct` that directly contain `ref` field is another important compatibility concern.</span></span> <span data-ttu-id="a0be6-152">Si consideri che la maggior parte dei `ref struct` riferimenti attualmente contengono indirettamente `Span<T>` riferimenti.</span><span class="sxs-lookup"><span data-stu-id="a0be6-152">Consider that the majority of `ref struct` defined today indirectly contain `Span<T>` references.</span></span> <span data-ttu-id="a0be6-153">Ciò significa che, per estensione, i campi verranno inclusi indirettamente `ref` dopo l' `Span<T>` adozione dei `ref` campi.</span><span class="sxs-lookup"><span data-stu-id="a0be6-153">That mean by extension they will indirectly contain `ref` fields once `Span<T>` adopts `ref` fields.</span></span> <span data-ttu-id="a0be6-154">È quindi importante assicurarsi che le regole di *sicurezza per la restituzione* dei costruttori su questi tipi non cambino.</span><span class="sxs-lookup"><span data-stu-id="a0be6-154">Hence it's important to ensure the *safe-to-return* rules of constructors on these types do not change.</span></span> <span data-ttu-id="a0be6-155">Per questo motivo le restrizioni devono essere valide solo per i tipi che contengono direttamente un `ref` campo.</span><span class="sxs-lookup"><span data-stu-id="a0be6-155">That is why the restrictions must only apply to types that directly contain a `ref` field.</span></span>

<span data-ttu-id="a0be6-156">Esempio di come entra in gioco.</span><span class="sxs-lookup"><span data-stu-id="a0be6-156">Example of where this comes into play.</span></span>

```cs
ref struct Container
{
    LargeStruct _largeStruct;
    Span<int> _span;

    public Container(in LargeStruct largeStruct, Span<int> span)
    {
        _largeStruct = largeStruct;
        _span = span;
    }
}
```

<span data-ttu-id="a0be6-157">Analogamente all' `CreateSpan<T>` esempio prima della restituzione da *safe a escape* del `Container` costruttore non è influenzato dal `largeStruct` parametro.</span><span class="sxs-lookup"><span data-stu-id="a0be6-157">Much like the `CreateSpan<T>` example before the *safe-to-escape* return of the `Container` constructor is not impacted by the `largeStruct` parameter.</span></span> <span data-ttu-id="a0be6-158">Se le nuove regole del costruttore sono state applicate a questo tipo, la compatibilità con il codice esistente verrebbe interrotta.</span><span class="sxs-lookup"><span data-stu-id="a0be6-158">If the new constructor rules were applied to this type then it would break compatibility with existing code.</span></span> <span data-ttu-id="a0be6-159">Le regole esistenti sono sufficienti anche per i costruttori esistenti in modo da impedire la simulazione `ref` dei campi archiviando i campi nei `Span<T>` campi.</span><span class="sxs-lookup"><span data-stu-id="a0be6-159">The existing rules are also sufficient for existing constructors to prevent them from simulating `ref` fields by storing them into `Span<T>` fields.</span></span>

```cs
ref struct RS4
{
    Span<int> _span;

    public RS4(Span<int> span)
    {
        // Legal today and the rules for this constructor invocation 
        // remain unchanged
        _span = span;
    }

    public RS4(ref int i)
    {
        // ERROR. Bullet 1 of the new constructor rules gives this newly created
        // Span<T> a *safe-to-escape* of the current scope. The 'this' parameter
        // though has a *safe-to-escape* outside the current method. Hence this
        // is illegal by assignment rules because it's assigning a smaller scope
        // to a larger one.
        _span = new Span(ref i);
    }

    // Legal today, must remain legal for compat. If the new constructor rules 
    // applied to 'RS4' though this would be illegal. This is why the new 
    // constructor rules have a restriction to directly defining a ref field
    // 
    // Only ref struct which explicitly opt into ref fields would see a breaking
    // change here.
    static RS4 CreateContainer(ref int i) => new RS4(ref i);
}
```

<span data-ttu-id="a0be6-160">Questa progettazione richiede anche che le regole per la durata dei campi vengano espanse, in quanto le regole attualmente non ne rappresentano semplicemente un conto.</span><span class="sxs-lookup"><span data-stu-id="a0be6-160">This design also requires that the rules for field lifetimes be expanded as the rules today simply don't account for them.</span></span> <span data-ttu-id="a0be6-161">È importante sottolineare che l'espansione delle regole in questo caso non definisce un nuovo comportamento, ma piuttosto il comportamento che esisteva a lungo.</span><span class="sxs-lookup"><span data-stu-id="a0be6-161">It's important to note that our expansion of the rules here is not defining new behavior but rather accounting for behavior that has long existed.</span></span> <span data-ttu-id="a0be6-162">Le regole di sicurezza `ref struct` per l'utilizzo del riconoscimento completo e l'account per la possibilità che conterranno `ref struct` lo stato `ref` e tale `ref` stato verranno esposte ai consumer.</span><span class="sxs-lookup"><span data-stu-id="a0be6-162">The safety rules around using `ref struct` fully acknowledge and account for the possibility that `ref struct` will contain `ref` state and that `ref` state will be exposed to consumers.</span></span> <span data-ttu-id="a0be6-163">L'esempio più evidente è l'indicizzatore in `Span<T>` :</span><span class="sxs-lookup"><span data-stu-id="a0be6-163">The most prominent example of this is the indexer on `Span<T>`:</span></span>

``` cs
readonly ref struct Span<T>
{
    public ref T this[int index] => ...; 
}
```

<span data-ttu-id="a0be6-164">Questo espone direttamente lo `ref` stato all'interno `Span<T>` di e l'account per le regole di sicurezza da span per questo.</span><span class="sxs-lookup"><span data-stu-id="a0be6-164">This directly exposes the `ref` state inside `Span<T>` and the span safety rules account for this.</span></span> <span data-ttu-id="a0be6-165">Indica se è stato implementato come `ByReference<T>` o se `ref` i campi sono immateriali a tali regole.</span><span class="sxs-lookup"><span data-stu-id="a0be6-165">Whether that was implemented as `ByReference<T>` or `ref` fields is immaterial to those rules.</span></span> <span data-ttu-id="a0be6-166">`ref`Per consentire i campi, tuttavia, è necessario definire le regole in modo che rientrino nelle regole di consumo esistenti per `ref struct` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-166">As a part of allowing `ref` fields though we must define their rules such that they fit into the existing consumption rules for `ref struct`.</span></span> <span data-ttu-id="a0be6-167">In particolare, questo deve tenere conto del fatto che è *legale per* un `ref struct` per restituire lo `ref` stato `ref` del consumer.</span><span class="sxs-lookup"><span data-stu-id="a0be6-167">Specifically this must account for the fact that it's legal *today* for a `ref struct` to return its `ref` state as `ref` to the consumer.</span></span> 

<span data-ttu-id="a0be6-168">Per comprendere le modifiche proposte, è utile esaminare prima di tutto le regole esistenti per la chiamata al metodo in base *a Ref-safe-to-escape* e il modo in cui vengono considerate per uno `ref struct` stato di esposizione `ref` :</span><span class="sxs-lookup"><span data-stu-id="a0be6-168">To understand the proposed changes it's helpful to first review the existing rules for method invocation around *ref-safe-to-escape* and how they account for a `ref struct` exposing `ref` state today:</span></span>

> <span data-ttu-id="a0be6-169">Lvalue risultante da una chiamata al metodo di restituzione di Ref E1. M (E2,...) è *ref-safe-to-escape* il più piccolo degli ambiti seguenti:</span><span class="sxs-lookup"><span data-stu-id="a0be6-169">An lvalue resulting from a ref-returning method invocation e1.M(e2, ...) is *ref-safe-to-escape* the smallest of the following scopes:</span></span>
> 1. <span data-ttu-id="a0be6-170">Intero metodo contenitore</span><span class="sxs-lookup"><span data-stu-id="a0be6-170">The entire enclosing method</span></span>
> 2. <span data-ttu-id="a0be6-171">*Ref-safe-to-escape* di tutte le espressioni di argomento ref e out (escluso il ricevitore)</span><span class="sxs-lookup"><span data-stu-id="a0be6-171">The *ref-safe-to-escape* of all ref and out argument expressions (excluding the receiver)</span></span>
> 3. <span data-ttu-id="a0be6-172">Per ogni parametro nel parametro del metodo, se esiste un'espressione corrispondente che è un lvalue, il relativo *ref-safe-to-escape*, in caso contrario l'ambito di inclusione più vicino</span><span class="sxs-lookup"><span data-stu-id="a0be6-172">For each in parameter of the method, if there is a corresponding expression that is an lvalue, its *ref-safe-to-escape*, otherwise the nearest enclosing scope</span></span>
> 4. <span data-ttu-id="a0be6-173">il carattere di *escape sicuro per* tutte le espressioni di argomento (incluso il ricevitore)</span><span class="sxs-lookup"><span data-stu-id="a0be6-173">the *safe-to-escape* of all argument expressions (including the receiver)</span></span>

<span data-ttu-id="a0be6-174">Il quarto elemento fornisce il punto di sicurezza critico intorno a uno `ref struct` `ref` stato di esposizione ai chiamanti.</span><span class="sxs-lookup"><span data-stu-id="a0be6-174">The fourth item provides the critical safety point around a `ref struct` exposing `ref` state to callers.</span></span> <span data-ttu-id="a0be6-175">Quando lo `ref` stato archiviato in un oggetto `ref struct` si riferisce allo stack, l'ambito *di sicurezza da evitare* `ref struct` sarà al massimo l'ambito che definisce lo stato a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="a0be6-175">When the `ref` state stored in a `ref struct` refers to the stack then the *safe-to-escape* scope for that `ref struct` will be at most the scope which defines the state being referred to.</span></span> <span data-ttu-id="a0be6-176">Per questo motivo, la limitazione di *ref-safe-to-escape* delle chiamate di un oggetto `ref struct` all'ambito *safe-to-escape* del ricevitore garantisce che le durate siano corrette.</span><span class="sxs-lookup"><span data-stu-id="a0be6-176">Hence limiting the *ref-safe-to-escape* of invocations of a `ref struct` to the *safe-to-escape* scope of the receiver ensures the lifetimes are correct.</span></span>

<span data-ttu-id="a0be6-177">Si consideri, ad esempio, l'indicizzatore in `Span<T>` cui vengono restituiti i `ref` campi da `ref` oggi.</span><span class="sxs-lookup"><span data-stu-id="a0be6-177">Consider as an example the indexer on `Span<T>` which is returning `ref` fields by `ref` today.</span></span> <span data-ttu-id="a0be6-178">Il quarto elemento è quello che fornisce la sicurezza:</span><span class="sxs-lookup"><span data-stu-id="a0be6-178">The fourth item here is what provides the safety here:</span></span>

```cs
ref int Examples()
{
    Span<int> s1 = stackalloc int[5];
    // ERROR: illegal because the *safe-to-escape* scope of `s1` is the current
    // method scope hence that limits the *ref-safe-to-escape" to the current
    // method scope as well.
    return ref s1[0];

    // SUCCESS: legal because the *safe-to-escape* scope of `s2` is outside
    // the current method scope hence the *ref-safe-to-escape* is as well
    Span<int> s2 = default;
    return ref s2[0];
}
```

<span data-ttu-id="a0be6-179">Per tenere conto dei `ref` campi, le regole *ref-safe-to-escape* per i campi verranno modificate in base a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="a0be6-179">To account for `ref` fields the *ref-safe-to-escape* rules for fields will be adjusted to the following:</span></span>

> <span data-ttu-id="a0be6-180">Un lvalue che designa un riferimento a un campo, e. F, è *ref-safe-to-escape* (per riferimento) come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="a0be6-180">An lvalue designating a reference to a field, e.F, is *ref-safe-to-escape* (by reference) as follows:</span></span>
> - <span data-ttu-id="a0be6-181">Se `F` è un `ref` campo e `e` è `this` , è *di tipo ref-safe-to-escape* dal metodo contenitore.</span><span class="sxs-lookup"><span data-stu-id="a0be6-181">If `F` is a `ref` field and `e` is `this`, it is *ref-safe-to-escape* from the enclosing method.</span></span>
> - <span data-ttu-id="a0be6-182">In caso contrario, se `F` è un campo, il `ref` relativo ambito *ref-safe-to-escape* è l'ambito *sicuro di escape* di `e` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-182">Else if `F` is a `ref` field its *ref-safe-to-escape* scope is the *safe-to-escape* scope of `e`.</span></span>
> - <span data-ttu-id="a0be6-183">In caso contrario `e` , se è di un tipo riferimento, è *ref-safe-to-escape* dal metodo contenitore.</span><span class="sxs-lookup"><span data-stu-id="a0be6-183">Else if `e` is of a reference type, it is *ref-safe-to-escape* from the enclosing method.</span></span>
> - <span data-ttu-id="a0be6-184">In caso contrario, il relativo *ref-safe-to-escape* viene tratto da *ref-safe-to-escape* di `e` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-184">Else its *ref-safe-to-escape* is taken from the *ref-safe-to-escape* of `e`.</span></span>

<span data-ttu-id="a0be6-185">Questo consente in modo esplicito la `ref` restituzione di campi come `ref` da un `ref struct` campo, ma non normali, che verrà trattato in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="a0be6-185">This explicitly allows for `ref` fields being returned as `ref` from a `ref struct` but not normal fields (that will be covered later).</span></span> 

```cs
ref struct RS
{
    ref int _refField;
    int _field;

    // Okay: this falls into bullet one above. 
    public ref int Prop1 => ref _refField;

    // ERROR: This is bullet four above and the *ref-safe-to-escape* of `this`
    // in a `struct` is the current method scope.
    public ref int Prop2 => ref _field;

    public RS(int[] array)
    {
        ref _refField = ref array[0];
    }

    public RS(ref int i)
    {
        ref _refField = ref i;
    }

    public RS CreateRS() => ...;

    public ref int M1(RS rs)
    {
        ref int local1 = ref rs.Prop1;

        // Okay: this falls into bullet two above and the *safe-to-escape* of
        // `rs` is outside the current method scope. Hence the *ref-safe-to-escape*
        // of `local1` is outside the current method scope.
        return ref local;

        // Okay: this falls into bullet two above and the *safe-to-escape* of
        // `rs` is outside the current method scope. Hence the *ref-safe-to-escape*
        // of `local1` is outside the current method scope.
        //
        // In fact in this scenario you can guarantee that the value returned
        // from Prop1 must exist on the heap. 
        RS local2 = CreateRS();
        return ref local2.Prop1;

        // ERROR: the *safe-to-escape* of `local4` here is the current method 
        // scope by the revised constructor rules. This falls into bullet two 
        // above and hence based on that allowed scope.
        int local3 = 42;
        var local4 = new RS(ref local3);
        return ref local4.Prop1;

    }
}
```

<span data-ttu-id="a0be6-186">Le regole per l'assegnazione devono anche essere modificate per tenere conto dei `ref` campi.</span><span class="sxs-lookup"><span data-stu-id="a0be6-186">The rules for assignment also need to be adjusted to account for `ref` fields.</span></span>
<span data-ttu-id="a0be6-187">Questa progettazione consente solo l' `ref` assegnazione di un `ref` campo durante la costruzione di un oggetto o quando il valore è noto per fare riferimento all'heap.</span><span class="sxs-lookup"><span data-stu-id="a0be6-187">This design only allows for `ref` assignment of a `ref` field during object construction or when the value is known to refer to the heap.</span></span> <span data-ttu-id="a0be6-188">La costruzione di oggetti include nel costruttore del tipo dichiarante, all'interno delle `init` funzioni di accesso e nelle espressioni dell'inizializzatore di oggetto.</span><span class="sxs-lookup"><span data-stu-id="a0be6-188">Object construction includes in the constructor of the declaring type, inside `init` accessors and inside object initializer expressions.</span></span> <span data-ttu-id="a0be6-189">`ref` `ref` In questo caso, l'oggetto assegnato al campo deve disporre di *ref-safe-to-escape* maggiore del destinatario del campo:</span><span class="sxs-lookup"><span data-stu-id="a0be6-189">Further the `ref` being assigned to the `ref` field in this case must have *ref-safe-to-escape* greater than the receiver of the field:</span></span> 

- <span data-ttu-id="a0be6-190">Costruttori: il valore deve essere *ref-safe-to-escape* all'esterno del costruttore</span><span class="sxs-lookup"><span data-stu-id="a0be6-190">Constructors: The value must be *ref-safe-to-escape* outside the constructor</span></span>
- <span data-ttu-id="a0be6-191">`init` funzioni di accesso: il valore limitato ai valori noti per fare riferimento all'heap come funzioni di accesso non può avere `ref` parametri</span><span class="sxs-lookup"><span data-stu-id="a0be6-191">`init` accessors:  The value limited to values that are known to refer to the heap as accessors can't have `ref` parameters</span></span>
- <span data-ttu-id="a0be6-192">inizializzatori di oggetto: il valore può avere qualsiasi valore *ref-safe-to-escape* , in quanto verrà inserito nel calcolo della *sicurezza di escape* dell'oggetto costruito da regole esistenti.</span><span class="sxs-lookup"><span data-stu-id="a0be6-192">object initializers: The value can have any *ref-safe-to-escape* value as this will feed into the calculation of the *safe-to-escape* of the constructed object by existing rules.</span></span>

<span data-ttu-id="a0be6-193">Un `ref` campo può essere assegnato solo all'esterno di un costruttore quando il valore è noto per fare riferimento all'heap.</span><span class="sxs-lookup"><span data-stu-id="a0be6-193">A `ref` field can only be assigned outside a constructor when the value is known to refer to the heap.</span></span> <span data-ttu-id="a0be6-194">Questa operazione è consentita perché è sicura nel percorso di assegnazione (soddisfa le regole di assegnazione del campo per garantire che il valore assegnato abbia una durata almeno pari al ricevitore), così come non richiede aggiornamenti per le regole di chiamata del metodo esistente.</span><span class="sxs-lookup"><span data-stu-id="a0be6-194">That is allowed because it is both safe at the assignment location (meets the field assignment rules for ensuring the value being assigned has a lifetime at least as large as the receiver) as well as requires no updates to the existing method invocation rules.</span></span> 

<span data-ttu-id="a0be6-195">Questa progettazione non consente l'assegnazione di `ref` campo generale al di fuori della costruzione dell'oggetto a causa di limitazioni esistenti sulle durate.</span><span class="sxs-lookup"><span data-stu-id="a0be6-195">This design does not allow for general `ref` field assignment outside object construction due to existing limitations on lifetimes.</span></span> <span data-ttu-id="a0be6-196">In particolare, costituisce una sfida per scenari come i seguenti:</span><span class="sxs-lookup"><span data-stu-id="a0be6-196">Specifically it poses challenges for scenarios like the following:</span></span>

```cs
ref struct SmallSpan
{
    public ref int _field;

    // Notice once again we're back at the same problem as the original 
    // CreateSpan method: a method returning a ref struct and taking a ref
    // parameter
    SmallSpan TrickyRefAssignment(ref int i)
    {
        // *safe-to-escape* is outside the current method by current rules.
        SmallSpan s = default;

        // The *ref-safe-to-escape* of 'i' is the same as the *safe-to-escape*
        // of 's' hence most assignment rules would allow it.
        ref s._field = ref i;

        // ERROR: this must be disallowed for the exact same reasons we can't 
        // return a Span<T> wrapping the parameter: the consumption rules
        // believe such state smuggling cannot exist
        return s;
    }

    SmallSpan SafeRefAssignment()
    {
        int[] array = new int[] { 42, 13 };
        SmallSpan s = default;

        // Okay: the value being assigned here is known to refer to the heap 
        // hence it is allowed by our rules above because it requires no changes
        // to existing method invocation rules (hence preserves compat)
        ref s._field = ref array[i];

        return s;
    }

    SmallSpan BadUsage()
    {
        // Legal today and must remain legal (and safe)
        int i = 0;
        return TrickyRefAssignment(ref i);
    }
}
```

<span data-ttu-id="a0be6-197">Sono disponibili scelte di progettazione per consentire una `ref` riassegnazione più flessibile dei campi.</span><span class="sxs-lookup"><span data-stu-id="a0be6-197">There are designs choices we could make to allow more flexible `ref` re-assignment of fields.</span></span> <span data-ttu-id="a0be6-198">Ad esempio, può essere consentito nei casi in cui è stato rilevato che il ricevitore aveva un ambito *di sicurezza da Escape* che non era al di fuori dell'ambito del metodo corrente.</span><span class="sxs-lookup"><span data-stu-id="a0be6-198">For example it could be allowed in cases where we knew the receiver had a *safe-to-escape* scope that was not outside the current method scope.</span></span> <span data-ttu-id="a0be6-199">Inoltre, è possibile fornire la sintassi per rendere più semplici i valori rivolti verso il basso per dichiarare: essenzialmente i valori con ambiti di *sicurezza a escape* limitati al metodo corrente.</span><span class="sxs-lookup"><span data-stu-id="a0be6-199">Further we could provide syntax for making such downward facing values easier to declare: essentially values that have *safe-to-escape* scopes restricted to the current method.</span></span> <span data-ttu-id="a0be6-200">Questo tipo di progettazione è illustrato di [seguito](https://github.com/dotnet/csharplang/discussions/1130).</span><span class="sxs-lookup"><span data-stu-id="a0be6-200">Such a design is discussed [here](https://github.com/dotnet/csharplang/discussions/1130)).</span></span>
<span data-ttu-id="a0be6-201">Tuttavia, la complessità aggiuntiva di tali regole non sembra valere per i casi limitati abilitati.</span><span class="sxs-lookup"><span data-stu-id="a0be6-201">However extra complexity of such rules do not seem to be worth the limited cases this enables.</span></span> <span data-ttu-id="a0be6-202">È possibile che vengano rilevati esempi interessanti.</span><span class="sxs-lookup"><span data-stu-id="a0be6-202">Should compelling samples come up we can revisit this decision.</span></span>

<span data-ttu-id="a0be6-203">Ciò significa tuttavia che `ref` i campi sono in gran parte pratici `ref readonly` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-203">This means though that `ref` fields are largely in practice `ref readonly`.</span></span> <span data-ttu-id="a0be6-204">Le eccezioni principali sono gli inizializzatori di oggetto e quando il valore è noto per fare riferimento all'heap.</span><span class="sxs-lookup"><span data-stu-id="a0be6-204">The main exceptions being object initializers and when the value is known to refer to the heap.</span></span>

<span data-ttu-id="a0be6-205">Un `ref` campo verrà emesso nei metadati usando la `ELEMENT_TYPE_BYREF` firma.</span><span class="sxs-lookup"><span data-stu-id="a0be6-205">A `ref` field will be emitted into metadata using the `ELEMENT_TYPE_BYREF` signature.</span></span> <span data-ttu-id="a0be6-206">Questa operazione non è diversa rispetto alla modalità di creazione di `ref` variabili locali o `ref` argomenti.</span><span class="sxs-lookup"><span data-stu-id="a0be6-206">This is no different than how we emit `ref` locals or `ref` arguments.</span></span> <span data-ttu-id="a0be6-207">Ad esempio `ref int _field` , verrà generato come `ELEMENT_TYPE_BYREF ELEMENT_TYPE_I4` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-207">For example `ref int _field` will be emitted as `ELEMENT_TYPE_BYREF ELEMENT_TYPE_I4`.</span></span> <span data-ttu-id="a0be6-208">Questa operazione richiede l'aggiornamento di ECMA335 per consentire questa voce, ma dovrebbe essere piuttosto semplice.</span><span class="sxs-lookup"><span data-stu-id="a0be6-208">This will require us to update ECMA335 to allow this entry but this should be rather straight forward.</span></span>

<span data-ttu-id="a0be6-209">Gli sviluppatori possono continuare a inizializzare un oggetto `ref struct` con un `ref` campo usando l' `default` espressione, nel qual caso tutti i campi dichiarati avranno `ref` il valore `null` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-209">Developers can continue to initialize a `ref struct` with a `ref` field using the `default` expression in which case all declared `ref` fields will have the value `null`.</span></span> <span data-ttu-id="a0be6-210">Qualsiasi tentativo di utilizzare tali campi comporterà la generazione di un' `NullReferenceException` eccezione.</span><span class="sxs-lookup"><span data-stu-id="a0be6-210">Any attempt to use such fields will result in a `NullReferenceException` being thrown.</span></span>

```cs
struct S1 
{
    public ref int Value;
}

S1 local = default;
local.Value.ToString(); // throws NullReferenceException
```

<span data-ttu-id="a0be6-211">Anche se il linguaggio C# prevede che un oggetto `ref` non può essere `null` valido a livello di runtime e ha una semantica ben definita.</span><span class="sxs-lookup"><span data-stu-id="a0be6-211">While the C# language pretends that a `ref` cannot be `null` this is legal at the runtime level and has well defined semantics.</span></span> <span data-ttu-id="a0be6-212">Gli sviluppatori che introducono `ref` campi nei propri tipi devono essere consapevoli di questa possibilità e dovrebbero essere **fortemente** sconsigliati a causa della perdita di questo dettaglio nell'utilizzo del codice.</span><span class="sxs-lookup"><span data-stu-id="a0be6-212">Developers who introduce `ref` fields into their types need to be aware of this possibility and should be **strongly** discouraged from leaking this detail into consuming code.</span></span> <span data-ttu-id="a0be6-213">`ref`I campi devono invece essere convalidati come non null usando gli [Helper di runtime](https://github.com/dotnet/runtime/pull/40008) e generando quando un oggetto non inizializzato `struct` viene usato in modo errato.</span><span class="sxs-lookup"><span data-stu-id="a0be6-213">Instead `ref` fields should be validated as non-null using the [runtime helpers](https://github.com/dotnet/runtime/pull/40008) and throwing when an uninitialized `struct` is used incorrectly.</span></span>

```cs
struct S1 
{
    private ref int Value;

    public int GetValue()
    {
        if (System.Runtime.CompilerServices.Unsafe.IsNullRef(ref Value))
        {
            throw new InvalidOperationException(...);
        }

        return Value;
    }
}
```

<span data-ttu-id="a0be6-214">Note varie:</span><span class="sxs-lookup"><span data-stu-id="a0be6-214">Misc Notes:</span></span>
- <span data-ttu-id="a0be6-215">Un `ref` campo può essere dichiarato solo all'interno di un `ref struct`</span><span class="sxs-lookup"><span data-stu-id="a0be6-215">A `ref` field can only be declared inside of a `ref struct`</span></span> 
- <span data-ttu-id="a0be6-216">`ref`Non è possibile dichiarare un campo`static`</span><span class="sxs-lookup"><span data-stu-id="a0be6-216">A `ref` field cannot be declared `static`</span></span>
- <span data-ttu-id="a0be6-217">Un `ref` campo può essere `ref` assegnato solo nel costruttore del tipo dichiarante.</span><span class="sxs-lookup"><span data-stu-id="a0be6-217">A `ref` field can only be `ref` assigned in the constructor of the declaring type.</span></span>
- <span data-ttu-id="a0be6-218">Il processo di generazione dell'assembly di riferimento deve mantenere la presenza di un `ref` campo all'interno di un `ref struct`</span><span class="sxs-lookup"><span data-stu-id="a0be6-218">The reference assembly generation process must preserve the presence of a `ref` field inside a `ref struct`</span></span> 
- <span data-ttu-id="a0be6-219">Un oggetto `ref readonly struct` deve dichiarare i `ref` campi come `ref readonly`</span><span class="sxs-lookup"><span data-stu-id="a0be6-219">A `ref readonly struct` must declare its `ref` fields as `ref readonly`</span></span>
- <span data-ttu-id="a0be6-220">Le regole di protezione dell'intervallo per costruttori, campi e assegnazioni devono essere aggiornate come descritto in questo documento.</span><span class="sxs-lookup"><span data-stu-id="a0be6-220">The span safety rules for constructors, fields and assignment must be updated as outlined in this document.</span></span>
- <span data-ttu-id="a0be6-221">Le regole di sicurezza dell'intervallo devono includere la definizione dei `ref` valori che "fanno riferimento all'heap".</span><span class="sxs-lookup"><span data-stu-id="a0be6-221">The span safety rules need to include the definition of `ref` values that "refer to the heap".</span></span> 

### <a name="provide-struct-this-escape-annotation"></a><span data-ttu-id="a0be6-222">Specificare l'annotazione escape per struct</span><span class="sxs-lookup"><span data-stu-id="a0be6-222">Provide struct this escape annotation</span></span>
<span data-ttu-id="a0be6-223">Le regole per l'ambito di `this` in un `struct` limitano l'ambito *ref-safe-to-escape* al metodo corrente.</span><span class="sxs-lookup"><span data-stu-id="a0be6-223">The rules for the scope of `this` in a `struct` limit the *ref-safe-to-escape* scope to the current method.</span></span> <span data-ttu-id="a0be6-224">Ciò significa che nessuno `this` dei due campi può restituire per riferimento al chiamante.</span><span class="sxs-lookup"><span data-stu-id="a0be6-224">That means neither `this`, nor any of its fields can return by reference to the caller.</span></span>

```cs
struct S
{
    int _field;
    // Error: this, and hence _field, can't return by ref
    public ref int Prop => ref _field;
}
```

<span data-ttu-id="a0be6-225">Non ci sono problemi intrinseci con l' `struct` escape `this` per riferimento.</span><span class="sxs-lookup"><span data-stu-id="a0be6-225">There is nothing inherently wrong with a `struct` escaping `this` by reference.</span></span>
<span data-ttu-id="a0be6-226">Al contrario, la giustificazione di questa regola è la presenza di un equilibrio tra l'usabilità di `struct` e `interfaces` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-226">Instead the justification for this rule is that it strikes a balance between the usability of `struct` and `interfaces`.</span></span> <span data-ttu-id="a0be6-227">Se un oggetto può essere sottoposto a `struct` escape `this` per riferimento, ridurrebbe significativamente l'utilizzo dei `ref` ritorni nelle interfacce.</span><span class="sxs-lookup"><span data-stu-id="a0be6-227">If a `struct` could escape `this` by reference then it would significantly reduce the use of `ref` returns in interfaces.</span></span>

```cs
interface I1
{
    ref int Prop { get; }
}

struct S1 : I1
{
    int _field;
    public ref int Prop => _ref field;

    // When T is a struct type, like S1 this would end up returning a reference
    // to the parameter
    static ref int M<T>(T p) where T : I1 => ref p.Prop;
}
```

<span data-ttu-id="a0be6-228">La giustificazione è ragionevole, ma introduce anche un attrito `struct` non necessario sui membri che non partecipano alle chiamate di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="a0be6-228">The justification here is reasonable but it also introduces unnecessary friction on `struct` members that don't participate in interface invocations.</span></span> 

<span data-ttu-id="a0be6-229">Uno scenario di compatibilità principale da tenere presente quando si avvicinano le modifiche è il seguente:</span><span class="sxs-lookup"><span data-stu-id="a0be6-229">One key compatibility scenario that we have to keep in mind when approaching changes here is the following:</span></span>

```cs
struct S1
{
    ref int GetValue() => ...
}

class Example
{
    ref int M()
    {
        // Okay: this is always allowed no matter how `local` is initialized
        S1 local = default;
        return local.GetValue();
    }
}
```

<span data-ttu-id="a0be6-230">Questa operazione funziona perché le regole di sicurezza per `ref` return attualmente non prendono in considerazione la durata di `this` (perché non può restituire uno `ref` stato interno).</span><span class="sxs-lookup"><span data-stu-id="a0be6-230">This works because the safety rules for `ref` return today do not take into account the lifetime of `this` (because it can't return a `ref` to internal state).</span></span> <span data-ttu-id="a0be6-231">Ciò significa che la `ref` restituzione da un `struct` può restituire al di fuori dell'ambito del metodo contenitore tranne nei casi in cui sono presenti `ref` parametri o un oggetto `ref struct` che non è *sicuro da evitare l'escape* all'esterno dell'ambito del metodo contenitore.</span><span class="sxs-lookup"><span data-stu-id="a0be6-231">This means that `ref` returns from a `struct` can return outside the enclosing method scope except in cases where there are `ref` parameters or a `ref struct` which is not *safe-to-escape* outside the enclosing method scope.</span></span> <span data-ttu-id="a0be6-232">Quindi, la soluzione qui non è semplice quanto consentire la `ref` restituzione di campi nei metodi non di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="a0be6-232">Hence the solution here is not as easy as allowing `ref` return of fields in non-interface methods.</span></span>

<span data-ttu-id="a0be6-233">Per rimuovere questo attrito, il linguaggio fornirà l'attributo `[ThisRefEscapes]` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-233">To remove this friction the language will provide the attribute `[ThisRefEscapes]`.</span></span>
<span data-ttu-id="a0be6-234">Quando questo attributo viene applicato a un metodo di istanza, alla proprietà dell'istanza o alla funzione di accesso dell'istanza di un oggetto `struct` o, `ref struct` il `this` parametro viene considerato *ref-safe-to-escape* all'esterno del metodo contenitore.</span><span class="sxs-lookup"><span data-stu-id="a0be6-234">When this attribute is applied to an instance method, instance property or instance accessor of a `struct` or `ref struct` then the `this` parameter will be considered *ref-safe-to-escape* outside the enclosing method.</span></span>

<span data-ttu-id="a0be6-235">Questo consente una maggiore flessibilità nelle `struct` definizioni che possono iniziare a tornare `ref` ai rispettivi campi.</span><span class="sxs-lookup"><span data-stu-id="a0be6-235">This allows for greater flexibility in `struct` definitions as they can begin returning `ref` to their fields.</span></span> <span data-ttu-id="a0be6-236">Che consente tipi come `FrugalList<T>` :</span><span class="sxs-lookup"><span data-stu-id="a0be6-236">That allows for types like `FrugalList<T>`:</span></span>

```cs
struct FrugalList<T>
{
    private T _item0;
    private T _item1;
    private T _item2;

    public int Count = 3;

    public ref T this[int index]
    {
        [ThisRefEscapes]
        get
        {
            switch (index)
            {
                case 0: return ref _item1;
                case 1: return ref _item2;
                case 2: return ref _item3;
                default: throw null;
            }
        }
    }
}
```

<span data-ttu-id="a0be6-237">Questo naturalmente, in base alle regole esistenti nella specifica span Safety, consente la restituzione di campi transitivi oltre ai campi diretti.</span><span class="sxs-lookup"><span data-stu-id="a0be6-237">This will naturally, by the existing rules in the span safety spec, allow for returning transitive fields in addition to direct fields.</span></span>

```cs
struct ListWithDefault<T>
{
    private FrugalList<T> _list;
    private T _default;

    public ref T this[int index]
    {
        [ThisRefEscapes]
        get
        {
            if (index >= _list.Count)
            {
                return ref _default;
            }

            return ref _list[index];
        }
    }
}
```

<span data-ttu-id="a0be6-238">I membri che contengono l' `[ThisRefEscapes]` attributo non possono essere usati per implementare i membri di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="a0be6-238">Members which contain the `[ThisRefEscapes]` attribute cannot be used to implement interface members.</span></span> <span data-ttu-id="a0be6-239">In questo modo si nasconde la natura della durata del membro nel `interface` sito di chiamata e potrebbero verificarsi calcoli di durata non corretti.</span><span class="sxs-lookup"><span data-stu-id="a0be6-239">This would hide the lifetime nature of the member at the `interface` call site and would lead to incorrect lifetime calculations.</span></span>

<span data-ttu-id="a0be6-240">Per tenere conto di questa modifica, la sezione "Parameters" del documento span Safety verrà aggiornata in modo da includere quanto segue:</span><span class="sxs-lookup"><span data-stu-id="a0be6-240">To account for this change the "Parameters" section of the span safety document will be updated to include the following:</span></span>

- <span data-ttu-id="a0be6-241">Se il parametro è il `this` parametro di un `struct` tipo, è *ref-safe-to-escape* nell'ambito superiore del metodo contenitore a meno che il metodo non sia annotato con, `[ThisRefEscapes]` nel qual caso è *ref-safe-to-escape* all'esterno del metodo contenitore.</span><span class="sxs-lookup"><span data-stu-id="a0be6-241">If the parameter is the `this` parameter of a `struct` type, it is *ref-safe-to-escape* to the top scope of the enclosing method unless the method is annotated with `[ThisRefEscapes]` in which case it is *ref-safe-to-escape* outside the enclosing method.</span></span>

<span data-ttu-id="a0be6-242">Note varie:</span><span class="sxs-lookup"><span data-stu-id="a0be6-242">Misc Notes:</span></span>
- <span data-ttu-id="a0be6-243">Un membro contrassegnato come `[ThisRefEscapes]` non può implementare un `interface` metodo o essere `overrides`</span><span class="sxs-lookup"><span data-stu-id="a0be6-243">A member marked as `[ThisRefEscapes]` can not implement an `interface` method or be `overrides`</span></span>
- <span data-ttu-id="a0be6-244">Un membro contrassegnato come `[ThisRefEscapes]` verrà generato con un oggetto `modreq` su tale attributo.</span><span class="sxs-lookup"><span data-stu-id="a0be6-244">A member marked as `[ThisRefEscapes]` will be emitted with a `modreq` on that attribute.</span></span>
- <span data-ttu-id="a0be6-245">L'oggetto `RefEscapesAttribute` verrà definito nello `System.Runtime.CompilerServices` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="a0be6-245">The `RefEscapesAttribute` will be defined in the `System.Runtime.CompilerServices` namespace.</span></span>

### <a name="safe-fixed-size-buffers"></a><span data-ttu-id="a0be6-246">Buffer a dimensione fissa sicura</span><span class="sxs-lookup"><span data-stu-id="a0be6-246">Safe fixed size buffers</span></span>
<span data-ttu-id="a0be6-247">Il linguaggio ridurrà le restrizioni sulle matrici a dimensione fissa in modo che possano essere dichiarate in codice sicuro e il tipo di elemento possa essere gestito o non gestito.</span><span class="sxs-lookup"><span data-stu-id="a0be6-247">The language will relax the restrictions on fixed sized arrays such that they can be declared in safe code and the element type can be managed or unmanaged.</span></span> <span data-ttu-id="a0be6-248">Questa operazione renderà i tipi come i seguenti:</span><span class="sxs-lookup"><span data-stu-id="a0be6-248">This will make types like the following legal:</span></span>

```cs
internal struct CharBuffer
{
    internal fixed char Data[128];
}
```

<span data-ttu-id="a0be6-249">Queste dichiarazioni, in modo analogo alle `unsafe` parti del contatore, definiranno una sequenza di `N` elementi nel tipo che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="a0be6-249">These declarations, much like their `unsafe` counter parts, will define a sequence of `N` elements in the containing type.</span></span> <span data-ttu-id="a0be6-250">È possibile accedere a questi membri con un indicizzatore e convertirli anche in `Span<T>` istanze di e `ReadOnlySpan<T>` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-250">These members can be accessed with an indexer and can also be converted to `Span<T>` and `ReadOnlySpan<T>` instances.</span></span>

<span data-ttu-id="a0be6-251">Quando si esegue l'indicizzazione in un `fixed` buffer di tipo `T` `readonly` , è necessario prendere in considerazione lo stato del contenitore.</span><span class="sxs-lookup"><span data-stu-id="a0be6-251">When indexing into a `fixed` buffer of type `T` the `readonly` state of the container must be taken into account.</span></span>  <span data-ttu-id="a0be6-252">Se il contenitore è `readonly` quindi, l'indicizzatore restituisce `ref readonly T` else `ref T` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-252">If the container is `readonly` then the indexer returns `ref readonly T` else it returns `ref T`.</span></span> 

<span data-ttu-id="a0be6-253">L'accesso a un `fixed` buffer senza un indicizzatore non ha un tipo naturale, tuttavia è convertibile in `Span<T>` tipi.</span><span class="sxs-lookup"><span data-stu-id="a0be6-253">Accessing a `fixed` buffer without an indexer has no natural type however it is convertible to `Span<T>` types.</span></span> <span data-ttu-id="a0be6-254">Nel caso in cui il contenitore sia `readonly` il buffer è implicitamente convertibile in `ReadOnlySpan<T>` , altrimenti può eseguire la conversione implicita in `Span<T>` o `ReadOnlySpan<T>` (la `Span<T>` conversione è considerata *migliore*).</span><span class="sxs-lookup"><span data-stu-id="a0be6-254">In the case the container is `readonly` the buffer is implicitly convertible to `ReadOnlySpan<T>`, else it can implicitly convert to `Span<T>` or `ReadOnlySpan<T>` (the `Span<T>` conversion is considered *better*).</span></span> 

<span data-ttu-id="a0be6-255">L' `Span<T>` istanza risultante avrà una lunghezza uguale alla dimensione dichiarata nel `fixed` buffer.</span><span class="sxs-lookup"><span data-stu-id="a0be6-255">The resulting `Span<T>` instance will have a length equal to the size declared on the `fixed` buffer.</span></span> <span data-ttu-id="a0be6-256">L'ambito *safe-to-escape* del valore restituito sarà uguale all'ambito *safe-to-escape* del contenitore.</span><span class="sxs-lookup"><span data-stu-id="a0be6-256">The *safe-to-escape* scope of the returned value will be equal to the *safe-to-escape* scope of the container.</span></span>

<span data-ttu-id="a0be6-257">Per ogni `fixed` dichiarazione in un tipo in cui il tipo di elemento è `T` il linguaggio genererà un `get` solo metodo indicizzatore corrispondente il cui tipo restituito è `ref T` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-257">For each `fixed` declaration in a type where the element type is `T` the language will generate a corresponding `get` only indexer method whose return type is `ref T`.</span></span> <span data-ttu-id="a0be6-258">L'indicizzatore verrà annotato con l' `[ThisRefEscapes]` attributo, perché l'implementazione restituirà campi del tipo dichiarante.</span><span class="sxs-lookup"><span data-stu-id="a0be6-258">The indexer will be annotated with the `[ThisRefEscapes]` attribute as the implementation will be returning fields of the declaring type.</span></span> <span data-ttu-id="a0be6-259">L'accessibilità del membro corrisponderà all'accessibilità nel `fixed` campo.</span><span class="sxs-lookup"><span data-stu-id="a0be6-259">The accessibility of the member will match the accessibility on the `fixed` field.</span></span>

<span data-ttu-id="a0be6-260">Ad esempio, la firma dell'indicizzatore per `CharBuffer.Data` sarà la seguente:</span><span class="sxs-lookup"><span data-stu-id="a0be6-260">For example, the signature of the indexer for `CharBuffer.Data` will be the following:</span></span>

```cs
[ThisRefEscapes]
internal ref char <>DataIndexer(int index) => ...;
```

<span data-ttu-id="a0be6-261">Se l'indice specificato non è compreso nei limiti dichiarati della `fixed` matrice `IndexOutOfRangeException` , verrà generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="a0be6-261">If the provided index is outside the declared bounds of the `fixed` array then an `IndexOutOfRangeException` will be thrown.</span></span> <span data-ttu-id="a0be6-262">Nel caso in cui venga fornito un valore costante, questo verrà sostituito da un riferimento diretto all'elemento appropriato.</span><span class="sxs-lookup"><span data-stu-id="a0be6-262">In the case a constant value is provided then it will be replaced with a direct reference to the appropriate element.</span></span> <span data-ttu-id="a0be6-263">A meno che la costante non sia al di fuori dei limiti dichiarati, nel qual caso si verificherà un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="a0be6-263">Unless the constant is outside the declared bounds in which case a compile time error would occur.</span></span>

<span data-ttu-id="a0be6-264">Verrà inoltre generata una funzione di accesso denominata per ogni `fixed` buffer che fornisce per valore `get` e `set` operazioni.</span><span class="sxs-lookup"><span data-stu-id="a0be6-264">There will also be a named accessor generated for each `fixed` buffer that provides by value `get` and `set` operations.</span></span> <span data-ttu-id="a0be6-265">Ciò significa che i `fixed` buffer saranno più simili alla semantica della matrice esistente con una funzione di `ref` accesso, nonché `get` `set` le operazioni ByVal e.</span><span class="sxs-lookup"><span data-stu-id="a0be6-265">Having this means that `fixed` buffers will more closely resemble existing array semantics by having a `ref` accessor as well as byval `get` and `set` operations.</span></span> <span data-ttu-id="a0be6-266">Ciò significa che i compilatori avranno la stessa flessibilità durante la creazione di buffer che utilizzano il codice `fixed` , come avviene quando si utilizzano matrici.</span><span class="sxs-lookup"><span data-stu-id="a0be6-266">This means compilers will have the same flexibility when emitting code consuming `fixed` buffers as they do when consuming arrays.</span></span> <span data-ttu-id="a0be6-267">Questa operazione deve essere simile `await` a quella dei `fixed` buffer più facili da creare.</span><span class="sxs-lookup"><span data-stu-id="a0be6-267">This should be operations like `await` over `fixed` buffers easier to emit.</span></span> 

<span data-ttu-id="a0be6-268">Questo offre anche il vantaggio aggiuntivo che renderà `fixed` più semplice l'utilizzo dei buffer da altri linguaggi.</span><span class="sxs-lookup"><span data-stu-id="a0be6-268">This also has the added benefit that it will make `fixed` buffers easier to consume from other languages.</span></span> <span data-ttu-id="a0be6-269">Gli indicizzatori denominati sono una funzionalità esistente dalla versione 1,0 di .NET.</span><span class="sxs-lookup"><span data-stu-id="a0be6-269">Named indexers is a feature that has existed since the 1.0 release of .NET.</span></span> <span data-ttu-id="a0be6-270">Anche le lingue che non possono emettere direttamente un indicizzatore denominato possono in genere utilizzarle (C# è un esempio valido).</span><span class="sxs-lookup"><span data-stu-id="a0be6-270">Even languages which cannot directly emit a named indexer can generally consume them (C# is actually a good example of this).</span></span>

<span data-ttu-id="a0be6-271">Ci sarà anche un `get` per valore e la `set` funzione di accesso generata per ogni</span><span class="sxs-lookup"><span data-stu-id="a0be6-271">There will also be a by value `get` and `set` accessor generated for every</span></span> 

<span data-ttu-id="a0be6-272">L'archiviazione di backup per il buffer verrà generata usando l' `[InlineArray]` attributo.</span><span class="sxs-lookup"><span data-stu-id="a0be6-272">The backing storage for the buffer will be generated using the `[InlineArray]` attribute.</span></span> <span data-ttu-id="a0be6-273">Si tratta di un meccanismo illustrato in [èusarlo 12320](https://github.com/dotnet/runtime/issues/12320) che consente di dichiarare in modo efficiente la sequenza di campi dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="a0be6-273">This is a mechanism discussed in [isuse 12320](https://github.com/dotnet/runtime/issues/12320) which allows specifically for the case of efficiently declaring sequence of fields of the same type.</span></span>

<span data-ttu-id="a0be6-274">Questo particolare problema è ancora in discussione attiva e l'aspettativa è che l'implementazione di questa funzionalità seguirà tuttavia la discussione.</span><span class="sxs-lookup"><span data-stu-id="a0be6-274">This particular issue is still under active discussion and the expectation is that the implementation of this feature will follow however that discussion goes.</span></span>

### <a name="provide-parameter-escape-annotations"></a><span data-ttu-id="a0be6-275">Specificare le annotazioni di escape del parametro</span><span class="sxs-lookup"><span data-stu-id="a0be6-275">Provide parameter escape annotations</span></span>
<span data-ttu-id="a0be6-276">**questa sezione è ancora in fase di sviluppo** Una delle regole che causano un attrito ripetuto nel codice di basso livello è la regola "argomenti del metodo che devono corrispondere".</span><span class="sxs-lookup"><span data-stu-id="a0be6-276">**THIS SECTION STILL IN DEVELOPMENT** One of the rules that causes repeated friction in low level code is the "Method Arguments must Match" rule.</span></span> <span data-ttu-id="a0be6-277">Tale regola indica che, nel caso in cui una chiamata al metodo abbia almeno una `ref struct` passata da, `ref / out` nessuno degli altri parametri può avere un valore *safe-to-escape* più piccolo rispetto al parametro.</span><span class="sxs-lookup"><span data-stu-id="a0be6-277">That rule states that in the case a a method call has at least one `ref struct` passed by `ref / out` then none of the other parameters can have a *safe-to-escape* value narrower than that parameter.</span></span>
<span data-ttu-id="a0be6-278">Per estensione se sono presenti due parametri di questo tipo, la modalità di *escape sicura* di tutti i parametri deve essere uguale.</span><span class="sxs-lookup"><span data-stu-id="a0be6-278">By extension if there are two such parameters then the *safe-to-escape* of all parameters must be equal.</span></span>

<span data-ttu-id="a0be6-279">Questa regola esiste per impedire scenari come i seguenti:</span><span class="sxs-lookup"><span data-stu-id="a0be6-279">This rule exists to prevent scenarios like the following:</span></span>

```cs
struct RS
{
    Span<int> _field;
    void Set(Span<int> p)
    {
        _field = p;
    }

    static void DangerousCode(ref RS p)
    {
        Span<int> span = stackalloc int[] { 42 };

        // Error: if allowed this would let the method return a pointer to 
        // the stack
        p.Set(span);
    }
}
```

<span data-ttu-id="a0be6-280">Questa regola esiste perché la lingua deve presupporre che questi valori possano eseguire l'escape fino alla durata massima consentita.</span><span class="sxs-lookup"><span data-stu-id="a0be6-280">This rule exists because the language must assume that these values can escape to their maximum allowed lifetime.</span></span> <span data-ttu-id="a0be6-281">In molti casi, tuttavia, le implementazioni del metodo non eseguono l'escape di questi valori.</span><span class="sxs-lookup"><span data-stu-id="a0be6-281">In many cases though the method implementations do not escape these values.</span></span> <span data-ttu-id="a0be6-282">Di conseguenza, l'attrito causato qui non è necessario.</span><span class="sxs-lookup"><span data-stu-id="a0be6-282">Hence the friction caused here is unnecessary.</span></span>

<span data-ttu-id="a0be6-283">Per rimuovere questo attrito, il linguaggio fornirà l'attributo `[DoesNotEscape]` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-283">To remove this friction the language will provide the attribute `[DoesNotEscape]`.</span></span> <span data-ttu-id="a0be6-284">Quando applicato ai parametri, l'ambito di *sicurezza di escape* del parametro verrà considerato l'ambito principale del metodo dichiarante.</span><span class="sxs-lookup"><span data-stu-id="a0be6-284">When applied to parameters the *safe-to-escape* scope of the parameter will be considered the top scope of the declaring method.</span></span> <span data-ttu-id="a0be6-285">Non può restituire al di fuori di esso.</span><span class="sxs-lookup"><span data-stu-id="a0be6-285">It cannot return outside of it.</span></span> <span data-ttu-id="a0be6-286">Analogamente, l'attributo può essere applicato ai membri di istanza, alle proprietà dell'istanza o alle funzioni di accesso dell'istanza e avrà lo stesso effetto sul `this` parametro.</span><span class="sxs-lookup"><span data-stu-id="a0be6-286">Likewise the attribute can be applied to instance members, instance properties or instance accessors and it will have the same effect on the `this` parameter.</span></span>

<span data-ttu-id="a0be6-287">Per tenere conto di questa modifica, la sezione "Parameters" del documento span Safety verrà aggiornata in modo da includere quanto segue:</span><span class="sxs-lookup"><span data-stu-id="a0be6-287">To account for this change the "Parameters" section of the span safety document will be updated to include the following:</span></span>

- <span data-ttu-id="a0be6-288">Se il parametro è contrassegnato con è sicuro da usare come `[DoesNotEscape]` *escape* per l'ambito superiore del metodo contenitore.</span><span class="sxs-lookup"><span data-stu-id="a0be6-288">If the parameter is marked with `[DoesNotEscape]`it is *safe-to-escape* to the top scope of the containing method.</span></span> <span data-ttu-id="a0be6-289">Poiché questo valore non è in grado di eseguire l'escape dal metodo, non è considerato parte del set di input di tipo *safe-to-escape* generale quando si calcolano le restituzione di questo metodo.</span><span class="sxs-lookup"><span data-stu-id="a0be6-289">Because this value cannot escape from the method it is not considered a part of the general *safe-to-escape* input set when calculating returns of this method.</span></span>

<span data-ttu-id="a0be6-290">**LA REGOLA PRECEDENTE RICHIEDE UN LAVORO**</span><span class="sxs-lookup"><span data-stu-id="a0be6-290">**THAT RULE ABOVE NEEDS WORK**</span></span>

```cs
struct RS
{
    Span<int> _field;
    void Set([DoesNotEscape] Span<int> p)
    {
        // Error: the *safe-to-escape* of p is the top scope of the method while
        // the *safe-to-escape* of 'this' is outside the method. Hence this is
        // illegal by the standard assignment rules
        _field = p; 
    }

    static RS M(ref RS rs1, [DoesNotEscape]RS rs2)
    {
        Span<int> span = stackalloc int[] { 42 };

        // Okay: The parameter here is not a part of the calculated "must match"
        // set because it can't be returned hence this is legal.
        rs2.Set(span);

        // Error: the *safe-to-escape* scope of 'rs2' is the top scope of this
        // method
        return rs2;
    }
}
```

<span data-ttu-id="a0be6-291">Note varie:</span><span class="sxs-lookup"><span data-stu-id="a0be6-291">Misc Notes:</span></span>
- <span data-ttu-id="a0be6-292">L'oggetto  `DoesNotEscapeAttribute` verrà definito nello `System.Runtime.CompilerServices` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="a0be6-292">The  `DoesNotEscapeAttribute` will be defined in the `System.Runtime.CompilerServices` namespace.</span></span>

## <a name="considerations"></a><span data-ttu-id="a0be6-293">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="a0be6-293">Considerations</span></span>

### <a name="keywords-vs-attributes"></a><span data-ttu-id="a0be6-294">Parole chiave e attributi</span><span class="sxs-lookup"><span data-stu-id="a0be6-294">Keywords vs. attributes</span></span>
<span data-ttu-id="a0be6-295">Questa struttura chiama per usare gli attributi per annotare le nuove regole di durata per `struct` i membri.</span><span class="sxs-lookup"><span data-stu-id="a0be6-295">This design calls for using attributes to annotate the new lifetime rules for `struct` members.</span></span> <span data-ttu-id="a0be6-296">Questa operazione potrebbe essere stata eseguita altrettanto facilmente con le parole chiave contestuali.</span><span class="sxs-lookup"><span data-stu-id="a0be6-296">This also could've been done just as easily with contextual keywords.</span></span> <span data-ttu-id="a0be6-297">Ad esempio: `scoped` e `escapes` potrebbero essere stati usati anziché `DoesNotEscape` e `ThisRefEscapes` .</span><span class="sxs-lookup"><span data-stu-id="a0be6-297">For instance: `scoped` and `escapes` could have been used instead of `DoesNotEscape` and `ThisRefEscapes`.</span></span>

<span data-ttu-id="a0be6-298">Le parole chiave, anche quelle contestuali, hanno un peso molto più pesante nel linguaggio degli attributi.</span><span class="sxs-lookup"><span data-stu-id="a0be6-298">Keywords, even the contextual ones, have a much heavier weight in the language than attributes.</span></span> <span data-ttu-id="a0be6-299">I casi d'uso di queste funzionalità risolvono, sebbene molto prezioso, influiscano su un numero ridotto di sviluppatori.</span><span class="sxs-lookup"><span data-stu-id="a0be6-299">The use cases these features solve, while very valuable, impact a small number of developers.</span></span> <span data-ttu-id="a0be6-300">Si tenga presente che solo una parte degli sviluppatori di livello superiore sta definendo `ref struct` le istanze e quindi considera che solo una parte di tali sviluppatori utilizzerà queste nuove funzionalità di durata.</span><span class="sxs-lookup"><span data-stu-id="a0be6-300">Consider that only a fraction of high end developers are defining `ref struct` instances and then consider that only a fraction of those developers will be using these new lifetime features.</span></span>
<span data-ttu-id="a0be6-301">Non sembra giustificare l'aggiunta di una nuova parola chiave contestuale al linguaggio.</span><span class="sxs-lookup"><span data-stu-id="a0be6-301">That doesn't seem to justify adding a new contextual keyword to the language.</span></span>

<span data-ttu-id="a0be6-302">Ciò significa che la correttezza del programma verrà definita in termini di attributi.</span><span class="sxs-lookup"><span data-stu-id="a0be6-302">This does mean that program correctness will be defined in terms of attributes though.</span></span> <span data-ttu-id="a0be6-303">Questo è un po' di un'area grigia per la lingua, ma un modello consolidato per il Runtime.</span><span class="sxs-lookup"><span data-stu-id="a0be6-303">That is a bit of a gray area for the language side of things but an established pattern for the runtime.</span></span> 

## <a name="open-issues"></a><span data-ttu-id="a0be6-304">Problemi non risolti</span><span class="sxs-lookup"><span data-stu-id="a0be6-304">Open Issues</span></span>

### <a name="allow-fixed-buffer-locals"></a><span data-ttu-id="a0be6-305">Consenti variabili locali del buffer fisse</span><span class="sxs-lookup"><span data-stu-id="a0be6-305">Allow fixed buffer locals</span></span>
<span data-ttu-id="a0be6-306">Questa progettazione consente i `fixed` buffer sicuri che possono supportare qualsiasi tipo.</span><span class="sxs-lookup"><span data-stu-id="a0be6-306">This design allows for safe `fixed` buffers that can support any type.</span></span> <span data-ttu-id="a0be6-307">Una possibile estensione consente `fixed` di dichiarare tali buffer come variabili locali.</span><span class="sxs-lookup"><span data-stu-id="a0be6-307">One possible extension here is allowing such `fixed` buffers to be declared as local variables.</span></span> <span data-ttu-id="a0be6-308">Ciò consente di sostituire un numero di `stackalloc` operazioni esistenti con un `fixed` buffer.</span><span class="sxs-lookup"><span data-stu-id="a0be6-308">This would allow a number of existing `stackalloc` operations to be replaced with a `fixed` buffer.</span></span> <span data-ttu-id="a0be6-309">Espande anche il set di scenari in cui è possibile che le allocazioni dello stile dello stack siano `stackalloc` limitate ai tipi di elementi non gestiti, mentre `fixed` i buffer non lo sono.</span><span class="sxs-lookup"><span data-stu-id="a0be6-309">It would also expand the set of scenarios we could have stack style allocations as `stackalloc` is limited to unmanaged element types while `fixed` buffers are not.</span></span> 

```cs
class FixedBufferLocals
{
    void Example()
    {
        Span<int> span = stakalloc int[42];
        int buffer[42];
    }
}
```

<span data-ttu-id="a0be6-310">Questa operazione viene eseguita insieme, ma è necessario estendere la sintassi per le variabili locali.</span><span class="sxs-lookup"><span data-stu-id="a0be6-310">This holds together but does require us to extend the syntax for locals a bit.</span></span> <span data-ttu-id="a0be6-311">Non chiaro se questo è o non vale la pena di complicare ulteriormente.</span><span class="sxs-lookup"><span data-stu-id="a0be6-311">Unclear if this is or isn't worth the extra complexity.</span></span> <span data-ttu-id="a0be6-312">È possibile che si decida di no per il momento e riportarlo in un secondo momento, se è necessaria una sufficiente dimostrazione</span><span class="sxs-lookup"><span data-stu-id="a0be6-312">Possible we could decide no for now and bring back later if sufficient need is demonstrated.</span></span>

<span data-ttu-id="a0be6-313">Esempio di dove si può trarre vantaggio: https://github.com/dotnet/runtime/pull/34149</span><span class="sxs-lookup"><span data-stu-id="a0be6-313">Example of where this would be beneficial: https://github.com/dotnet/runtime/pull/34149</span></span>

### <a name="allow-multi-dimensional-fixed-buffers"></a><span data-ttu-id="a0be6-314">Consenti buffer fissi multidimensionali</span><span class="sxs-lookup"><span data-stu-id="a0be6-314">Allow multi-dimensional fixed buffers</span></span>
<span data-ttu-id="a0be6-315">Se la progettazione per i `fixed` buffer viene estesa per includere matrici di stile multidimensionali,</span><span class="sxs-lookup"><span data-stu-id="a0be6-315">Should the design for `fixed` buffers be extended to include multi-dimensional style arrays?</span></span> <span data-ttu-id="a0be6-316">Consente essenzialmente le dichiarazioni come le seguenti:</span><span class="sxs-lookup"><span data-stu-id="a0be6-316">Essentially allowing for declarations like the following:</span></span>

```cs
struct Dimensions
{
    int array[42, 13];
}
```

## <a name="future-considerations"></a><span data-ttu-id="a0be6-317">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="a0be6-317">Future Considerations</span></span>

## <a name="related-information"></a><span data-ttu-id="a0be6-318">Informazioni correlate</span><span class="sxs-lookup"><span data-stu-id="a0be6-318">Related Information</span></span>

### <a name="issues"></a><span data-ttu-id="a0be6-319">Problemi</span><span class="sxs-lookup"><span data-stu-id="a0be6-319">Issues</span></span>
<span data-ttu-id="a0be6-320">Di seguito sono riportati i problemi correlati a questa proposta:</span><span class="sxs-lookup"><span data-stu-id="a0be6-320">The following issues are all related to this proposal:</span></span>

- https://github.com/dotnet/csharplang/issues/1130
- https://github.com/dotnet/csharplang/issues/1147
- https://github.com/dotnet/csharplang/issues/992
- https://github.com/dotnet/csharplang/issues/1314
- https://github.com/dotnet/csharplang/issues/2208
- https://github.com/dotnet/runtime/issues/32060

### <a name="proposals"></a><span data-ttu-id="a0be6-321">Proposte</span><span class="sxs-lookup"><span data-stu-id="a0be6-321">Proposals</span></span>
<span data-ttu-id="a0be6-322">Le seguenti proposte sono correlate a questa proposta:</span><span class="sxs-lookup"><span data-stu-id="a0be6-322">The following proposals are related to this proposal:</span></span>

- https://github.com/dotnet/csharplang/blob/725763343ad44a9251b03814e6897d87fe553769/proposals/fixed-sized-buffers.md

### <a name="fun-samples"></a><span data-ttu-id="a0be6-323">Esempi divertenti</span><span class="sxs-lookup"><span data-stu-id="a0be6-323">Fun Samples</span></span>

```cs
ref struct StackLinkedListNode<T>
{
    T _value;
    ref StackLinkedListNode<T> _next;

    public T Value => _value;

    public bool HasNext => !Unsafe.IsNullRef(ref _next);

    public ref StackLinkedListNode<T> Next 
    {
        get
        {
            if (!HasNext)
            {
                throw new InvalidOperationException("No next node");
            }

            return ref _next;
        }
    }

    public StackLinkedListNode(T value)
    {
        this = default;
        _value = value;
    }

    public StackLinkedListNode(T value, ref StackLinkedListNode<T> next)
    {
        _value = value;
        ref _next = ref next;
    }
}
```
