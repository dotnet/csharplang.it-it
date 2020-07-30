---
ms.openlocfilehash: 9712631e9f0a70d9a08624d0aa2053d8bcca525c
ms.sourcegitcommit: a88d56e3131d7a94c65e637c276379541a3cd491
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87440758"
---
# <a name="nullable-constructor-analysis"></a><span data-ttu-id="84338-101">Analisi del Costruttore Nullable</span><span class="sxs-lookup"><span data-stu-id="84338-101">Nullable constructor analysis</span></span>

<span data-ttu-id="84338-102">Questa proposta è destinata a risolvere alcuni dei problemi in sospeso con l'analisi dei costruttori Nullable.</span><span class="sxs-lookup"><span data-stu-id="84338-102">This proposal is intended to resolve a few of the outstanding problems with nullable constructor analysis.</span></span>

## <a name="how-it-works-currently"></a><span data-ttu-id="84338-103">Funzionamento attuale</span><span class="sxs-lookup"><span data-stu-id="84338-103">How it works currently</span></span>

<span data-ttu-id="84338-104">L'analisi Nullable dei costruttori funziona essenzialmente eseguendo un passaggio di assegnazione definito e segnalando un avviso se un costruttore non Inizializza un membro del tipo di riferimento non Nullable (ad esempio, un campo, una proprietà automatica o un evento simile a un campo) in tutti i percorsi del codice.</span><span class="sxs-lookup"><span data-stu-id="84338-104">Nullable analysis of constructors works by essentially running a definite assignment pass and reporting a warning if a constructor does not initialize a non-nullable reference type member (for example: a field, auto-property, or field-like event) in all code paths.</span></span> <span data-ttu-id="84338-105">Il costruttore viene altrimenti considerato come un metodo ordinario a scopo di analisi.</span><span class="sxs-lookup"><span data-stu-id="84338-105">The constructor is otherwise treated like an ordinary method for analysis purposes.</span></span> <span data-ttu-id="84338-106">Questo approccio presenta alcuni problemi.</span><span class="sxs-lookup"><span data-stu-id="84338-106">This approach comes with a few problems.</span></span>

<span data-ttu-id="84338-107">Il primo è che lo stato del flusso iniziale dei membri non è accurato:</span><span class="sxs-lookup"><span data-stu-id="84338-107">First is that the initial flow state of members is not accurate:</span></span>

```cs
public class C
{
    public string Prop { get; set; }
    public C() // we get no "uninitialized member" warning, as expected
    {
        Prop.ToString(); // unexpectedly, there is no "possible null receiver" warning here
        Prop = "";
    }
}
```

<span data-ttu-id="84338-108">Un altro è che le istruzioni Asserts/' Throw ' non impediscono gli avvisi di inizializzazione del campo:</span><span class="sxs-lookup"><span data-stu-id="84338-108">Another is that assertions/'throw' statements do not prevent field initialization warnings:</span></span>

```cs
public class C
{
    public string Prop { get; set; }
    public C() // unexpected warning: 'Prop' is not initialized
    {
        Init();

        if (Prop is null)
        {
            throw new Exception();
        }
    }

    void Init()
    {
        Prop = "some default";
    }
}
```

## <a name="an-alternative-approach"></a><span data-ttu-id="84338-109">Un approccio alternativo</span><span class="sxs-lookup"><span data-stu-id="84338-109">An alternative approach</span></span>

<span data-ttu-id="84338-110">È possibile risolvere questo problema adottando invece un approccio simile a `[MemberNotNull]` Analysis, in cui i campi sono contrassegnati come "null" e viene visualizzato un avviso se si esce dal metodo quando un campo è ancora in uno stato Maybe-null.</span><span class="sxs-lookup"><span data-stu-id="84338-110">We can address this by instead taking an approach similar to `[MemberNotNull]` analysis, where fields are marked maybe-null and a warning is given if we ever exit the method when a field is still in a maybe-null state.</span></span> <span data-ttu-id="84338-111">È possibile eseguire questa operazione introducendo le regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="84338-111">We can do this by introducing the following rules:</span></span>

<span data-ttu-id="84338-112">**Un costruttore su un tipo di riferimento senza inizializzatore** *o*</span><span class="sxs-lookup"><span data-stu-id="84338-112">**A constructor on a reference type with no initializer** *or*</span></span>  
<span data-ttu-id="84338-113">**Un costruttore in un tipo di riferimento con un `: base(...)` inizializzatore** ha uno stato di flusso Nullable iniziale determinato da:</span><span class="sxs-lookup"><span data-stu-id="84338-113">**A constructor on a reference type with a `: base(...)` initializer** has an initial nullable flow state determined by:</span></span>
- <span data-ttu-id="84338-114">Inizializzazione dei membri del tipo di base sullo stato dichiarato, poiché si prevede che il costruttore di base Inizializza i membri di base.</span><span class="sxs-lookup"><span data-stu-id="84338-114">Initializing base type members to their declared state, since we expect the base constructor to initialize the base members.</span></span>
- <span data-ttu-id="84338-115">Inizializzando quindi tutti i membri applicabili del tipo sullo stato assegnato assegnando un `default` valore letterale al membro.</span><span class="sxs-lookup"><span data-stu-id="84338-115">Then initializing all applicable members in the type to the state given by assigning a `default` literal to the member.</span></span> <span data-ttu-id="84338-116">Un membro è applicabile se è un'istanza di e il costruttore analizzato è un'istanza o se il membro è statico e il costruttore analizzato è statico.</span><span class="sxs-lookup"><span data-stu-id="84338-116">A member is applicable if it is instance and the constructor being analyzed is instance, or if the member is static and the constructor being analyzed is static.</span></span> 
  - <span data-ttu-id="84338-117">Si prevede che il valore `default` letterale produca uno `NotNull` stato per i tipi di valore non nullable, uno `MaybeNull` stato per i tipi di riferimento o tipi di valore nullable e uno `MaybeDefault` stato per i generics non vincolati.</span><span class="sxs-lookup"><span data-stu-id="84338-117">We expect the `default` literal to yield a `NotNull` state for non-nullable value types, a `MaybeNull` state for reference types or nullable value types, and a `MaybeDefault` state for unconstrained generics.</span></span>
- <span data-ttu-id="84338-118">Quindi, visitando gli inizializzatori per i membri applicabili, aggiornando di conseguenza lo stato del flusso.</span><span class="sxs-lookup"><span data-stu-id="84338-118">Then visiting the initializers for the applicable members, updating the flow state accordingly.</span></span>
  - <span data-ttu-id="84338-119">Questo consente l'inizializzazione di alcuni membri di riferimento non nullable usando un inizializzatore di campo/proprietà e altri da inizializzare all'interno del costruttore.</span><span class="sxs-lookup"><span data-stu-id="84338-119">This allows some non-nullable reference members to be initialized using a field/property initializer, and others to be initialized within the constructor.</span></span>
  - <span data-ttu-id="84338-120">Si prevede che il compilatore analizzerà e aggiornerà i dati di diagnostica sugli inizializzatori una volta, quindi copierà lo stato del flusso risultante come stato iniziale per ogni costruttore che non dispone di un `: this(...)` inizializzatore.</span><span class="sxs-lookup"><span data-stu-id="84338-120">The expectation is that the compiler will flow-analyze and report diagnostics on the initializers once, then copy the resulting flow state as the initial state for each constructor which does not have a `: this(...)` initializer.</span></span>

<span data-ttu-id="84338-121">**Un costruttore su un tipo di valore con un `: this()` inizializzatore che fa riferimento al costruttore del tipo di valore predefinito** ha uno stato di flusso iniziale fornito inizializzando tutti i membri applicabili allo stato fornito assegnando un `default` valore letterale al membro.</span><span class="sxs-lookup"><span data-stu-id="84338-121">**A constructor on a value type with a `: this()` initializer referencing the default value type constructor** has an initial flow state given by initializing all applicable members to the state given by assigning a `default` literal to the member.</span></span>

<span data-ttu-id="84338-122">**Un costruttore su un tipo di riferimento con un `: this(...)` inizializzatore** *o*</span><span class="sxs-lookup"><span data-stu-id="84338-122">**A constructor on a reference type with a `: this(...)` initializer** *or*</span></span>  
<span data-ttu-id="84338-123">**Un costruttore su un tipo di valore con un inizializzatore che non fa riferimento al costruttore del tipo di valore predefinito** *o*</span><span class="sxs-lookup"><span data-stu-id="84338-123">**A constructor on a value type with an initializer not referencing the default value type constructor** *or*</span></span>  
<span data-ttu-id="84338-124">**Un costruttore in un tipo di valore senza inizializzatore** ha lo stesso stato del flusso Nullable iniziale come metodo normale.</span><span class="sxs-lookup"><span data-stu-id="84338-124">**A constructor on a value type with no initializer** has the same initial nullable flow state as an ordinary method.</span></span>  
<span data-ttu-id="84338-125">I membri hanno uno stato iniziale in base alle annotazioni dichiarate e agli attributi di supporto dei valori null.</span><span class="sxs-lookup"><span data-stu-id="84338-125">Members have an initial state based on the declared annotations and nullability attributes.</span></span> <span data-ttu-id="84338-126">Nel caso dei tipi di valore, si prevede che l'analisi di assegnazione definita fornisca il livello di sicurezza desiderato quando non è presente alcun `: this(...)` inizializzatore.</span><span class="sxs-lookup"><span data-stu-id="84338-126">In the case of value types, we expect definite assignment analysis to provide the desired level of safety when there is no `: this(...)` initializer.</span></span> <span data-ttu-id="84338-127">Questo comportamento è identico a quello esistente.</span><span class="sxs-lookup"><span data-stu-id="84338-127">This is the same as the existing behavior.</span></span>

<span data-ttu-id="84338-128">In **corrispondenza di ogni ' Return ' esplicito o implicito in un costruttore**, viene visualizzato un avviso per ogni membro il cui stato di flusso è incompatibile con le relative annotazioni e attributi di supporto dei valori null.</span><span class="sxs-lookup"><span data-stu-id="84338-128">**At each explicit or implicit 'return' in a constructor**, we give a warning for each member whose flow state is incompatible with its annotations and nullability attributes.</span></span> <span data-ttu-id="84338-129">Un proxy ragionevole a questo scopo è: se l'assegnazione del membro a se stessa nel punto di ritorno genera un avviso di supporto di valori null, viene generato un avviso di supporto di valori null nel punto di ripristino.</span><span class="sxs-lookup"><span data-stu-id="84338-129">A reasonable proxy for this is: if assigning the member to itself at the return point would produce a nullability warning, then a nullability warning will be produced at the return point.</span></span>

<span data-ttu-id="84338-130">In alcuni scenari è possibile che si verifichino molti avvisi per gli stessi membri.</span><span class="sxs-lookup"><span data-stu-id="84338-130">It's possible this could result in a lot of warnings for the same members in some scenarios.</span></span> <span data-ttu-id="84338-131">Come obiettivo "Stretch", penso che dovremmo considerare le "ottimizzazioni" seguenti:</span><span class="sxs-lookup"><span data-stu-id="84338-131">As a "stretch goal" I think we should consider the following "optimizations":</span></span>
- <span data-ttu-id="84338-132">Se un membro ha uno stato di flusso incompatibile in tutti i punti restituiti in un costruttore applicabile, viene visualizzato un avviso sulla sintassi del nome del costruttore invece che su ogni punto restituito singolarmente.</span><span class="sxs-lookup"><span data-stu-id="84338-132">If a member has an incompatible flow state at all return points in an applicable constructor, we warn on the constructor's name syntax instead of on each return point individually.</span></span>
- <span data-ttu-id="84338-133">Se un membro ha uno stato di flusso incompatibile in tutti i punti restituiti in tutti i costruttori applicabili, viene visualizzato un avviso sulla dichiarazione del membro stesso.</span><span class="sxs-lookup"><span data-stu-id="84338-133">If a member has an incompatible flow state at all return points in all applicable constructors, we warn on the member declaration itself.</span></span>

## <a name="consequences-of-this-approach"></a><span data-ttu-id="84338-134">Conseguenze di questo approccio</span><span class="sxs-lookup"><span data-stu-id="84338-134">Consequences of this approach</span></span>

```cs
public class C
{
    public string Prop { get; set; }
    public C()
    {
        Prop = null; // Warning: cannot assign null to 'Prop'
    } // Warning: Prop may be null when exiting 'C.C()'
    
    // This is consistent with currently shipped behavior:
    [MemberNotNull(nameof(Prop))]
    void M()
    {
        Prop = null; // Warning: cannot assign null to 'Prop'
    } // Warning: Prop may be null when exiting 'C.M()'
}
```

<span data-ttu-id="84338-135">Lo scenario precedente produce più avvisi corrispondenti alla stessa proprietà.</span><span class="sxs-lookup"><span data-stu-id="84338-135">The above scenario produces multiple warnings corresponding to the same property.</span></span> <span data-ttu-id="84338-136">Se sono presenti più punti restituiti nel metodo, è possibile che vengano generati molti avvisi a seconda del numero di punti restituiti in cui un membro ha uno stato di flusso non valido.</span><span class="sxs-lookup"><span data-stu-id="84338-136">If there are more return points in the method, indefinitely many warnings could be produced depending on the number of return points in which a member has a bad flow state.</span></span>

<span data-ttu-id="84338-137">Tuttavia, questo è coerente con il comportamento fornito per `[MemberNotNull]` `[NotNull]` gli attributi e: viene visualizzato un avviso quando si verifica un valore non valido e viene visualizzato un avviso quando si restituisce il punto in cui la variabile potrebbe contenere un valore non valido.</span><span class="sxs-lookup"><span data-stu-id="84338-137">However, this is consistent with the behavior we have shipped for `[MemberNotNull]` and `[NotNull]` attributes: we warn when a bad value goes in, and we warn again when you return where the variable could contain a bad value.</span></span>

---

```cs
public class C
{
    public string Prop { get; set; }
    public C()
    {
        Prop.ToString(); // Warning: dereference of a possible null reference.
    } // No warning: Prop's flow state was 'promoted' to NotNull after dereference
    
    // This is also consistent with currently shipped behavior:
    [MemberNotNull(nameof(Prop))]
    void M()
    {
        Prop.ToString(); // Warning: dereference of a possible null reference.
    } // No warning: Prop's flow state was 'promoted' to NotNull after dereference
}
```

<span data-ttu-id="84338-138">In questo scenario non viene mai effettivamente inizializzato `Prop` , ma sappiamo che se viene restituito normalmente da questo costruttore, è necessario che prop venga inizializzato in qualche modo.</span><span class="sxs-lookup"><span data-stu-id="84338-138">In this scenario we never really initialize `Prop`, but we know that if we return normally from this constructor then Prop must have somehow gotten initialized.</span></span> <span data-ttu-id="84338-139">Questo avviso, anche se non è ideale, sembra essere appropriato per puntare l'utente verso la posizione in cui si è verificato il problema.</span><span class="sxs-lookup"><span data-stu-id="84338-139">Thus this warning, while not ideal, does seem to be adequate for pointing the user toward where their problem lies.</span></span>

<span data-ttu-id="84338-140">Analogamente, questo è coerente con il comportamento fornito di `[MemberNotNull]` e `[NotNull]` .</span><span class="sxs-lookup"><span data-stu-id="84338-140">Similarly, this is consistent with the shipped behavior of `[MemberNotNull]` and `[NotNull]`.</span></span>

---

```cs
class C
{
    string Prop1 { get; set; }
    string Prop2 { get; set; }

    public C(bool a)
    {
        if (a)
        {
            Prop1 = "hello";
            return; // warning for Prop2
        }
        else
        {
            return; // warning for Prop1 and for Prop2
        }
    }
}
```

<span data-ttu-id="84338-141">In questo scenario viene illustrata l'indipendenza degli avvisi a ogni punto di ripristino, nonché il modo in cui gli avvisi possono essere moltiplicati per un singolo membro all'interno di un singolo costruttore.</span><span class="sxs-lookup"><span data-stu-id="84338-141">This scenario demonstrates the independence of warnings at each return point, as well as the way warnings can multiply for a single member within a single constructor.</span></span> <span data-ttu-id="84338-142">È possibile che si verifichino metodi per ridurre la ridondanza degli avvisi, ma questo perfezionamento potrebbe essere più recente e migliorare `[MemberNotNull]` / `[NotNull]` l'analisi nello stesso momento.</span><span class="sxs-lookup"><span data-stu-id="84338-142">It feels like there might be methods of reducing redundancy of the warnings, but this refinement could come later and improve `[MemberNotNull]`/`[NotNull]` analysis at the same time.</span></span> <span data-ttu-id="84338-143">Per i costruttori con logica condizionale complessa, sembra essere un miglioramento per indicare "in questo caso, non è stato ancora inizializzato un elemento" e si dice semplicemente che "in questo punto non è stato inizializzato alcun elemento".</span><span class="sxs-lookup"><span data-stu-id="84338-143">For constructors with complex conditional logic, it does seem to be an improvement to say "at this return, you haven't initialized something yet" versus simply saying "somewhere in here, you didn't initialize something".</span></span>
