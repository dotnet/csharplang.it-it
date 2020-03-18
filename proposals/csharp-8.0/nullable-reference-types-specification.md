---
ms.openlocfilehash: aecbf4298053debdb479ad3aaf6e3db468918455
ms.sourcegitcommit: 02b535d712cc9e022290e14d9e0324508b28f231
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2020
ms.locfileid: "79485210"
---
# <a name="nullable-reference-types-specification"></a><span data-ttu-id="da828-101">Specifica di tipi di riferimento Nullable</span><span class="sxs-lookup"><span data-stu-id="da828-101">Nullable Reference Types Specification</span></span>

<span data-ttu-id="da828-102">Questo è un lavoro in corso. diverse parti sono mancanti o incomplete.</span><span class="sxs-lookup"><span data-stu-id="da828-102">\*\*\* This is a work in progress - several parts are missing or incomplete.</span></span> ***

## <a name="syntax"></a><span data-ttu-id="da828-103">Sintassi</span><span class="sxs-lookup"><span data-stu-id="da828-103">Syntax</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="da828-104">Tipi riferimento nullable</span><span class="sxs-lookup"><span data-stu-id="da828-104">Nullable reference types</span></span>

<span data-ttu-id="da828-105">I tipi di riferimento Nullable hanno la stessa sintassi `T?` la forma abbreviata di tipi di valore Nullable, ma non hanno un formato lungo corrispondente.</span><span class="sxs-lookup"><span data-stu-id="da828-105">Nullable reference types have the same syntax `T?` as the short form of nullable value types, but do not have a corresponding long form.</span></span>

<span data-ttu-id="da828-106">Ai fini della specifica, la produzione `nullable_type` corrente viene rinominata `nullable_value_type`e viene aggiunta una `nullable_reference_type` di produzione:</span><span class="sxs-lookup"><span data-stu-id="da828-106">For the purposes of the specification, the current `nullable_type` production is renamed to `nullable_value_type`, and a `nullable_reference_type` production is added:</span></span>

```antlr
reference_type
    : ...
    | nullable_reference_type
    ;
    
nullable_reference_type
    : non_nullable_reference_type '?'
    ;
    
non_nullable_reference_type
    : type
    ;
```

<span data-ttu-id="da828-107">Il `non_nullable_reference_type` in un `nullable_reference_type` deve essere un tipo di riferimento non Nullable (classe, interfaccia, delegato o matrice) oppure un parametro di tipo vincolato a un tipo di riferimento non Nullable (tramite il vincolo `class` o una classe diversa da `object`).</span><span class="sxs-lookup"><span data-stu-id="da828-107">The `non_nullable_reference_type` in a `nullable_reference_type` must be a non-nullable reference type (class, interface, delegate or array), or a type parameter that is constrained to be a non-nullable reference type (through the `class` constraint, or a class other than `object`).</span></span>

<span data-ttu-id="da828-108">I tipi di riferimento nullable non possono essere presenti nelle posizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="da828-108">Nullable reference types cannot occur in the following positions:</span></span>

- <span data-ttu-id="da828-109">come classe o interfaccia di base</span><span class="sxs-lookup"><span data-stu-id="da828-109">as a base class or interface</span></span>
- <span data-ttu-id="da828-110">come ricevitore di un `member_access`</span><span class="sxs-lookup"><span data-stu-id="da828-110">as the receiver of a `member_access`</span></span>
- <span data-ttu-id="da828-111">come `type` in una `object_creation_expression`</span><span class="sxs-lookup"><span data-stu-id="da828-111">as the `type` in an `object_creation_expression`</span></span>
- <span data-ttu-id="da828-112">come `delegate_type` in una `delegate_creation_expression`</span><span class="sxs-lookup"><span data-stu-id="da828-112">as the `delegate_type` in a `delegate_creation_expression`</span></span>
- <span data-ttu-id="da828-113">come `type` in un `is_expression`, un `catch_clause` o un `type_pattern`</span><span class="sxs-lookup"><span data-stu-id="da828-113">as the `type` in an `is_expression`, a `catch_clause` or a `type_pattern`</span></span>
- <span data-ttu-id="da828-114">come `interface` in un nome di membro di interfaccia completo</span><span class="sxs-lookup"><span data-stu-id="da828-114">as the `interface` in a fully qualified interface member name</span></span>

<span data-ttu-id="da828-115">Viene fornito un avviso in un `nullable_reference_type` in cui il contesto di annotazione Nullable è disabilitato.</span><span class="sxs-lookup"><span data-stu-id="da828-115">A warning is given on a `nullable_reference_type` where the nullable annotation context is disabled.</span></span>

### <a name="nullable-class-constraint"></a><span data-ttu-id="da828-116">Vincolo di classe Nullable</span><span class="sxs-lookup"><span data-stu-id="da828-116">Nullable class constraint</span></span>

<span data-ttu-id="da828-117">Il vincolo `class` presenta una controparte Nullable `class?`:</span><span class="sxs-lookup"><span data-stu-id="da828-117">The `class` constraint has a nullable counterpart `class?`:</span></span>

```antlr
primary_constraint
    : ...
    | 'class' '?'
    ;
```

### <a name="the-null-forgiving-operator"></a><span data-ttu-id="da828-118">Operatore che perdona i valori null</span><span class="sxs-lookup"><span data-stu-id="da828-118">The null-forgiving operator</span></span>

<span data-ttu-id="da828-119">L'operatore `!` post-correzione è denominato operatore che perdona i valori null.</span><span class="sxs-lookup"><span data-stu-id="da828-119">The post-fix `!` operator is called the null-forgiving operator.</span></span>

```antlr
primary_expression
    : ...
    | null_forgiving_expression
    ;
    
null_forgiving_expression
    : primary_expression '!'
    ;
```

<span data-ttu-id="da828-120">Il `primary_expression` deve essere di un tipo riferimento.</span><span class="sxs-lookup"><span data-stu-id="da828-120">The `primary_expression` must be of a reference type.</span></span>  

<span data-ttu-id="da828-121">L'operatore `!` suffisso non ha alcun effetto in fase di esecuzione. restituisce il risultato dell'espressione sottostante.</span><span class="sxs-lookup"><span data-stu-id="da828-121">The postfix `!` operator has no runtime effect - it evaluates to the result of the underlying expression.</span></span> <span data-ttu-id="da828-122">Il suo unico ruolo è modificare lo stato null dell'espressione e limitare gli avvisi in base al relativo utilizzo.</span><span class="sxs-lookup"><span data-stu-id="da828-122">Its only role is to change the null state of the expression, and to limit warnings given on its use.</span></span>

### <a name="nullable-implicitly-typed-local-variables"></a><span data-ttu-id="da828-123">variabili locali tipizzate in modo implicito Nullable</span><span class="sxs-lookup"><span data-stu-id="da828-123">nullable implicitly typed local variables</span></span>

<span data-ttu-id="da828-124">`var` deduce un tipo con annotazioni per i tipi di riferimento.</span><span class="sxs-lookup"><span data-stu-id="da828-124">`var` infers an annotated type for reference types.</span></span>
<span data-ttu-id="da828-125">Ad esempio, in `var s = "";` `var` viene dedotto come `string?`.</span><span class="sxs-lookup"><span data-stu-id="da828-125">For instance, in `var s = "";` the `var` is inferred as `string?`.</span></span>

### <a name="nullable-compiler-directives"></a><span data-ttu-id="da828-126">Direttive del compilatore Nullable</span><span class="sxs-lookup"><span data-stu-id="da828-126">Nullable compiler directives</span></span>

<span data-ttu-id="da828-127">`#nullable` direttive controllano i contesti di annotazione e avviso Nullable.</span><span class="sxs-lookup"><span data-stu-id="da828-127">`#nullable` directives control the nullable annotation and warning contexts.</span></span>

```antlr
pp_directive
    : ...
    | pp_nullable
    ;
    
pp_nullable
    : whitespace? '#' whitespace? 'nullable' whitespace nullable_action pp_new_line
    ;
    
nullable_action
    : 'disable'
    | 'enable'
    | 'restore'
    ;
```

<span data-ttu-id="da828-128">le direttive `#pragma warning` vengono espanse per consentire la modifica del contesto di avviso nullable e per consentire l'abilitazione di singoli avvisi anche quando sono disabilitati per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="da828-128">`#pragma warning` directives are expanded to allow changing the nullable warning context, and to allow individual warnings to be enabled on even when they're disabled by default:</span></span>

```antlr
pragma_warning_body
    : ...
    | 'warning' whitespace nullable_action whitespace 'nullable'
    ;

warning_action
    : ...
    | 'enable'
    ;
```

<span data-ttu-id="da828-129">Si noti che il nuovo form di `pragma_warning_body` utilizza `nullable_action`, non `warning_action`.</span><span class="sxs-lookup"><span data-stu-id="da828-129">Note that the new form of `pragma_warning_body` uses `nullable_action`, not `warning_action`.</span></span>

## <a name="nullable-contexts"></a><span data-ttu-id="da828-130">Contesti nullable</span><span class="sxs-lookup"><span data-stu-id="da828-130">Nullable contexts</span></span>

<span data-ttu-id="da828-131">Ogni riga del codice sorgente ha un *contesto di annotazione Nullable* e un *contesto di avviso Nullable*.</span><span class="sxs-lookup"><span data-stu-id="da828-131">Every line of source code has a *nullable annotation context* and a *nullable warning context*.</span></span> <span data-ttu-id="da828-132">Questi controllano se le annotazioni Nullable hanno effetto e se vengono specificati avvisi di supporto dei valori null.</span><span class="sxs-lookup"><span data-stu-id="da828-132">These control whether nullable annotations have effect, and whether nullability warnings are given.</span></span> <span data-ttu-id="da828-133">Il contesto di annotazione di una determinata riga è *disabilitato* o *abilitato*.</span><span class="sxs-lookup"><span data-stu-id="da828-133">The annotation context of a given line is either *disabled* or *enabled*.</span></span> <span data-ttu-id="da828-134">Il contesto di avviso di una determinata riga è *disabilitato* o *abilitato*.</span><span class="sxs-lookup"><span data-stu-id="da828-134">The warning context of a given line is either *disabled* or *enabled*.</span></span>

<span data-ttu-id="da828-135">Entrambi i contesti possono essere specificati a livello di progetto (all' C# esterno del codice sorgente) o in qualsiasi punto all'interno di un file di origine tramite `#nullable` direttive del pre-processore.</span><span class="sxs-lookup"><span data-stu-id="da828-135">Both contexts can be specified at the project level (outside of C# source code), or anywhere within a source file via `#nullable` pre-processor directives.</span></span> <span data-ttu-id="da828-136">Se non vengono specificate impostazioni a livello di progetto, il valore predefinito è per entrambi i contesti da *disabilitare*.</span><span class="sxs-lookup"><span data-stu-id="da828-136">If no project level settings are provided the default is for both contexts to be *disabled*.</span></span>

<span data-ttu-id="da828-137">La direttiva `#nullable` controlla l'annotazione e i contesti di avviso all'interno del testo di origine e hanno la precedenza sulle impostazioni a livello di progetto.</span><span class="sxs-lookup"><span data-stu-id="da828-137">The `#nullable` directive controls the annotation and warning contexts within the source text, and take precedence over the project-level settings.</span></span>

<span data-ttu-id="da828-138">Una direttiva imposta i contesti che controlla per le righe di codice successive, fino a quando un'altra direttiva ne esegue l'override o fino alla fine del file di origine.</span><span class="sxs-lookup"><span data-stu-id="da828-138">A directive sets the context(s) it controls for subsequent lines of code, until another directive overrides it, or until the end of the source file.</span></span>

<span data-ttu-id="da828-139">L'effetto delle direttive è il seguente:</span><span class="sxs-lookup"><span data-stu-id="da828-139">The effect of the directives is as follows:</span></span>

- <span data-ttu-id="da828-140">`#nullable disable`: imposta l'annotazione nullable e i contesti di avviso su *disabled*</span><span class="sxs-lookup"><span data-stu-id="da828-140">`#nullable disable`: Sets the nullable annotation and warning contexts to *disabled*</span></span>
- <span data-ttu-id="da828-141">`#nullable enable`: imposta l'annotazione nullable e i contesti di avviso su *Enabled*</span><span class="sxs-lookup"><span data-stu-id="da828-141">`#nullable enable`: Sets the nullable annotation and warning contexts to *enabled*</span></span>
- <span data-ttu-id="da828-142">`#nullable restore`: Ripristina i contesti di avviso e di annotazione Nullable nelle impostazioni del progetto</span><span class="sxs-lookup"><span data-stu-id="da828-142">`#nullable restore`: Restores the nullable annotation and warning contexts to project settings</span></span>
- <span data-ttu-id="da828-143">`#nullable disable annotations`: imposta il contesto di annotazione Nullable su *disabled*</span><span class="sxs-lookup"><span data-stu-id="da828-143">`#nullable disable annotations`: Sets the nullable annotation context to *disabled*</span></span>
- <span data-ttu-id="da828-144">`#nullable enable annotations`: imposta il contesto di annotazione Nullable su *Enabled*</span><span class="sxs-lookup"><span data-stu-id="da828-144">`#nullable enable annotations`: Sets the nullable annotation context to *enabled*</span></span>
- <span data-ttu-id="da828-145">`#nullable restore annotations`: Ripristina il contesto di annotazione Nullable nelle impostazioni del progetto</span><span class="sxs-lookup"><span data-stu-id="da828-145">`#nullable restore annotations`: Restores the nullable annotation context to project settings</span></span>
- <span data-ttu-id="da828-146">`#nullable disable warnings`: imposta il contesto di avviso Nullable su *disabled*</span><span class="sxs-lookup"><span data-stu-id="da828-146">`#nullable disable warnings`: Sets the nullable warning context to *disabled*</span></span>
- <span data-ttu-id="da828-147">`#nullable enable warnings`: imposta il contesto di avviso Nullable su *Enabled*</span><span class="sxs-lookup"><span data-stu-id="da828-147">`#nullable enable warnings`: Sets the nullable warning context to *enabled*</span></span>
- <span data-ttu-id="da828-148">`#nullable restore warnings`: Ripristina il contesto di avviso Nullable nelle impostazioni del progetto</span><span class="sxs-lookup"><span data-stu-id="da828-148">`#nullable restore warnings`: Restores the nullable warning context to project settings</span></span>

## <a name="nullability-of-types"></a><span data-ttu-id="da828-149">Supporto dei valori Null dei tipi</span><span class="sxs-lookup"><span data-stu-id="da828-149">Nullability of types</span></span>

<span data-ttu-id="da828-150">Un tipo specificato può avere uno dei quattro nullabilities: *ignaro*, non *null*, *Nullable* e *Unknown*.</span><span class="sxs-lookup"><span data-stu-id="da828-150">A given type can have one of four nullabilities: *Oblivious*, *nonnullable*, *nullable* and *unknown*.</span></span> 

<span data-ttu-id="da828-151">I tipi *sconosciuti* e non *null* possono causare avvisi se viene assegnato un potenziale valore `null`.</span><span class="sxs-lookup"><span data-stu-id="da828-151">*Nonnullable* and *unknown* types may cause warnings if a potential `null` value is assigned to them.</span></span> <span data-ttu-id="da828-152">I tipi *ignari* e *Nullable* , tuttavia, sono "*assegnabili a null*" e possono avere `null` valori assegnati senza avvisi.</span><span class="sxs-lookup"><span data-stu-id="da828-152">*Oblivious* and *nullable* types, however, are "*null-assignable*" and can have `null` values assigned to them without warnings.</span></span> 

<span data-ttu-id="da828-153">I tipi *ignari* e non *null* possono essere dereferenziati o assegnati senza avvisi.</span><span class="sxs-lookup"><span data-stu-id="da828-153">*Oblivious* and *nonnullable* types can be dereferenced or assigned without warnings.</span></span> <span data-ttu-id="da828-154">I valori di tipi *Nullable* e *Unknown* , tuttavia, sono "*null-Yielding*" e possono causare avvisi quando vengono dereferenziati o assegnati senza il controllo null appropriato.</span><span class="sxs-lookup"><span data-stu-id="da828-154">Values of *nullable* and *unknown* types, however, are "*null-yielding*" and may cause warnings when dereferenced or assigned without proper null checking.</span></span> 

<span data-ttu-id="da828-155">Lo *stato null predefinito* di un tipo che cede un valore null è "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="da828-155">The *default null state* of a null-yielding type is "maybe null".</span></span> <span data-ttu-id="da828-156">Lo stato null predefinito di un tipo non null restituisce "not null".</span><span class="sxs-lookup"><span data-stu-id="da828-156">The default null state of a non-null-yielding type is "not null".</span></span>

<span data-ttu-id="da828-157">Il tipo di tipo e il contesto di annotazione nullable in cui si verificano determinano il supporto di valori null:</span><span class="sxs-lookup"><span data-stu-id="da828-157">The kind of type and the nullable annotation context it occurs in determine its nullability:</span></span>

- <span data-ttu-id="da828-158">Un tipo di valore che non può essere null `S` è sempre non *null*</span><span class="sxs-lookup"><span data-stu-id="da828-158">A nonnullable value type `S` is always *nonnullable*</span></span>
- <span data-ttu-id="da828-159">Un tipo di valore Nullable `S?` è sempre *Nullable*</span><span class="sxs-lookup"><span data-stu-id="da828-159">A nullable value type `S?` is always *nullable*</span></span>
- <span data-ttu-id="da828-160">Un tipo di riferimento senza annotazioni `C` in un contesto di annotazione *disabilitato* è *ignaro*</span><span class="sxs-lookup"><span data-stu-id="da828-160">An unannotated reference type `C` in a *disabled* annotation context is *oblivious*</span></span>
- <span data-ttu-id="da828-161">Un tipo di riferimento senza annotazioni `C` in un contesto di annotazione *abilitato* non può essere *null*</span><span class="sxs-lookup"><span data-stu-id="da828-161">An unannotated reference type `C` in an *enabled* annotation context is *nonnullable*</span></span>
- <span data-ttu-id="da828-162">Un tipo di riferimento Nullable `C?` *ammette i valori null* (ma è possibile che venga restituito un avviso in un contesto di annotazione *disabilitato* )</span><span class="sxs-lookup"><span data-stu-id="da828-162">A nullable reference type `C?` is *nullable* (but a warning may be yielded in a *disabled* annotation context)</span></span>

<span data-ttu-id="da828-163">I parametri di tipo prendono inoltre in considerazione i vincoli:</span><span class="sxs-lookup"><span data-stu-id="da828-163">Type parameters additionally take their constraints into account:</span></span>

- <span data-ttu-id="da828-164">Un parametro di tipo `T` dove tutti i vincoli (se presenti) sono tipi che restituiscono valori null (*Nullable* e *Unknown*) o il vincolo `class?` è *sconosciuto*</span><span class="sxs-lookup"><span data-stu-id="da828-164">A type parameter `T` where all constraints (if any) are either null-yielding types (*nullable* and *unknown*) or the `class?` constraint is *unknown*</span></span>
- <span data-ttu-id="da828-165">Parametro di tipo `T` dove almeno un vincolo è *ignaro* o non può essere *null* o uno dei vincoli `struct` o `class` è</span><span class="sxs-lookup"><span data-stu-id="da828-165">A type parameter `T` where at least one constraint is either *oblivious* or *nonnullable* or one of the `struct` or `class` constraints is</span></span>
    - <span data-ttu-id="da828-166">*ignaro* in un contesto di annotazione *disabilitato*</span><span class="sxs-lookup"><span data-stu-id="da828-166">*oblivious* in a *disabled* annotation context</span></span>
    - <span data-ttu-id="da828-167">non *ammette valori null* in un contesto di annotazione *abilitato*</span><span class="sxs-lookup"><span data-stu-id="da828-167">*nonnullable* in an *enabled* annotation context</span></span>
- <span data-ttu-id="da828-168">Parametro di tipo Nullable `T?` dove almeno uno dei vincoli di `T`è *ignaro* o non può essere *null* o uno dei vincoli `struct` o `class`</span><span class="sxs-lookup"><span data-stu-id="da828-168">A nullable type parameter `T?` where at least one of `T`'s constraints is *oblivious* or *nonnullable* or one of the `struct` or `class` constraints, is</span></span>
    - <span data-ttu-id="da828-169">*Nullable* in un contesto di annotazione *disabilitato* (ma viene restituito un avviso)</span><span class="sxs-lookup"><span data-stu-id="da828-169">*nullable* in a *disabled* annotation context (but a warning is yielded)</span></span>
    - <span data-ttu-id="da828-170">*Nullable* in un contesto di annotazione *abilitato*</span><span class="sxs-lookup"><span data-stu-id="da828-170">*nullable* in an *enabled* annotation context</span></span>

<span data-ttu-id="da828-171">Per un parametro di tipo `T`, `T?` è consentito solo se `T` è noto come tipo di valore o è noto come tipo di riferimento.</span><span class="sxs-lookup"><span data-stu-id="da828-171">For a type parameter `T`, `T?` is only allowed if `T` is known to be a value type or known to be a reference type.</span></span>

### <a name="oblivious-vs-nonnullable"></a><span data-ttu-id="da828-172">E non nullable</span><span class="sxs-lookup"><span data-stu-id="da828-172">Oblivious vs nonnullable</span></span>

<span data-ttu-id="da828-173">Si ritiene che un `type` venga eseguito in un determinato contesto di annotazione quando l'ultimo token del tipo è incluso in tale contesto.</span><span class="sxs-lookup"><span data-stu-id="da828-173">A `type` is deemed to occur in a given annotation context when the last token of the type is within that context.</span></span>

<span data-ttu-id="da828-174">Se un tipo di riferimento specificato `C` nel codice sorgente viene interpretato come ignaro o non nullo dipende dal contesto dell'annotazione di tale codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="da828-174">Whether a given reference type `C` in source code is interpreted as oblivious or nonnullable depends on the annotation context of that source code.</span></span> <span data-ttu-id="da828-175">Una volta stabilito, viene considerato parte di quel tipo e "lo sposta con esso", ad esempio durante la sostituzione di argomenti di tipo generico.</span><span class="sxs-lookup"><span data-stu-id="da828-175">But once established, it is considered part of that type, and "travels with it" e.g. during substitution of generic type arguments.</span></span> <span data-ttu-id="da828-176">È come se è presente un'annotazione come `?` sul tipo, ma invisibile.</span><span class="sxs-lookup"><span data-stu-id="da828-176">It is as if there is an annotation like `?` on the type, but invisible.</span></span>

## <a name="constraints"></a><span data-ttu-id="da828-177">Vincoli</span><span class="sxs-lookup"><span data-stu-id="da828-177">Constraints</span></span>

<span data-ttu-id="da828-178">I tipi di riferimento nullable possono essere utilizzati come vincoli generici.</span><span class="sxs-lookup"><span data-stu-id="da828-178">Nullable reference types can be used as generic constraints.</span></span> <span data-ttu-id="da828-179">Inoltre `object` è ora valido come vincolo esplicito.</span><span class="sxs-lookup"><span data-stu-id="da828-179">Furthermore `object` is now valid as an explicit constraint.</span></span> <span data-ttu-id="da828-180">L'assenza di un vincolo è ora equivalente a un vincolo di `object?` (anziché `object`), ma, a differenza di `object` prima di, `object?` non è vietato come vincolo esplicito.</span><span class="sxs-lookup"><span data-stu-id="da828-180">Absence of a constraint is now equivalent to an `object?` constraint (instead of `object`), but (unlike `object` before) `object?` is not prohibited as an explicit constraint.</span></span>

<span data-ttu-id="da828-181">`class?` è un nuovo vincolo che indica "probabile tipo di riferimento Nullable", mentre `class` denota "tipo di riferimento non null".</span><span class="sxs-lookup"><span data-stu-id="da828-181">`class?` is a new constraint denoting "possibly nullable reference type", whereas `class` denotes "nonnullable reference type".</span></span>

<span data-ttu-id="da828-182">Il supporto di valori null di un argomento di tipo o di un vincolo non ha alcun effetto sul fatto che il tipo soddisfi il vincolo, ad eccezione del caso in cui è già presente. i tipi di valore nullable non soddisfano il vincolo `struct`.</span><span class="sxs-lookup"><span data-stu-id="da828-182">The nullability of a type argument or of a constraint does not impact whether the type satisfies the constraint, except where that is already the case today (nullable value types do not satisfy the `struct` constraint).</span></span> <span data-ttu-id="da828-183">Tuttavia, se l'argomento di tipo non soddisfa i requisiti di supporto dei valori null del vincolo, è possibile che venga fornito un avviso.</span><span class="sxs-lookup"><span data-stu-id="da828-183">However, if the type argument does not satisfy the nullability requirements of the constraint, a warning may be given.</span></span>

## <a name="null-state-and-null-tracking"></a><span data-ttu-id="da828-184">Stato null e rilevamento null</span><span class="sxs-lookup"><span data-stu-id="da828-184">Null state and null tracking</span></span>

<span data-ttu-id="da828-185">Ogni espressione in una determinata posizione di origine ha uno *stato null*, che indica se si ritiene che possa restituire potenzialmente null.</span><span class="sxs-lookup"><span data-stu-id="da828-185">Every expression in a given source location has a *null state*, which indicated whether it is believed to potentially evaluate to null.</span></span> <span data-ttu-id="da828-186">Lo stato null può essere "not null" o "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="da828-186">The null state is either "not null" or "maybe null".</span></span> <span data-ttu-id="da828-187">Lo stato null viene utilizzato per determinare se è necessario che venga fornito un avviso sulle conversioni e le dereferenziazioni non sicure null.</span><span class="sxs-lookup"><span data-stu-id="da828-187">The null state is used to determine whether a warning should be given about null-unsafe conversions and dereferences.</span></span>

### <a name="null-tracking-for-variables"></a><span data-ttu-id="da828-188">Rilevamento di valori null per le variabili</span><span class="sxs-lookup"><span data-stu-id="da828-188">Null tracking for variables</span></span>

<span data-ttu-id="da828-189">Per alcune espressioni che indicano variabili o proprietà, lo stato null viene rilevato tra le occorrenze, in base alle assegnazioni, i test eseguiti su di essi e il flusso di controllo tra di essi.</span><span class="sxs-lookup"><span data-stu-id="da828-189">For certain expressions denoting variables or properties, the null state is tracked between occurrences, based on assignments to them, tests performed on them and the control flow between them.</span></span> <span data-ttu-id="da828-190">Questa operazione è simile alla modalità di rilevamento dell'assegnazione definita per le variabili.</span><span class="sxs-lookup"><span data-stu-id="da828-190">This is similar to how definite assignment is tracked for variables.</span></span> <span data-ttu-id="da828-191">Le espressioni registrate sono quelle con il formato seguente:</span><span class="sxs-lookup"><span data-stu-id="da828-191">The tracked expressions are the ones of the following form:</span></span>

```antlr
tracked_expression
    : simple_name
    | this
    | base
    | tracked_expression '.' identifier
    ;
```

<span data-ttu-id="da828-192">Dove gli identificatori denotano i campi o le proprietà.</span><span class="sxs-lookup"><span data-stu-id="da828-192">Where the identifiers denote fields or properties.</span></span>

<span data-ttu-id="da828-193">***Descrivere le transizioni di stato null simili all'assegnazione definita***</span><span class="sxs-lookup"><span data-stu-id="da828-193">***Describe null state transitions similar to definite assignment***</span></span>

### <a name="null-state-for-expressions"></a><span data-ttu-id="da828-194">Stato null per le espressioni</span><span class="sxs-lookup"><span data-stu-id="da828-194">Null state for expressions</span></span>

<span data-ttu-id="da828-195">Lo stato null di un'espressione deriva dal formato e dal tipo e dallo stato null delle variabili in esso incluse.</span><span class="sxs-lookup"><span data-stu-id="da828-195">The null state of an expression is derived from its form and type, and from the null state of variables involved in it.</span></span>

### <a name="literals"></a><span data-ttu-id="da828-196">Valori letterali</span><span class="sxs-lookup"><span data-stu-id="da828-196">Literals</span></span>

<span data-ttu-id="da828-197">Lo stato null di un valore letterale `null` è "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="da828-197">The null state of a `null` literal is "maybe null".</span></span> <span data-ttu-id="da828-198">Lo stato null di un valore letterale `default` che viene convertito in un tipo che non è un tipo di valore non nullo è "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="da828-198">The null state of a `default` literal that is being converted to a type that is known not to be a nonnullable value type is "maybe null".</span></span> <span data-ttu-id="da828-199">Lo stato null di qualsiasi altro valore letterale è "not null".</span><span class="sxs-lookup"><span data-stu-id="da828-199">The null state of any other literal is "not null".</span></span>

### <a name="simple-names"></a><span data-ttu-id="da828-200">Nomi semplici</span><span class="sxs-lookup"><span data-stu-id="da828-200">Simple names</span></span>

<span data-ttu-id="da828-201">Se una `simple_name` non è classificata come valore, il suo stato null è "not null".</span><span class="sxs-lookup"><span data-stu-id="da828-201">If a `simple_name` is not classified as a value, its null state is "not null".</span></span> <span data-ttu-id="da828-202">In caso contrario, si tratta di un'espressione rilevata e il relativo stato null è il relativo stato null rilevato in questa posizione di origine.</span><span class="sxs-lookup"><span data-stu-id="da828-202">Otherwise it is a tracked expression, and its null state is its tracked null state at this source location.</span></span>

### <a name="member-access"></a><span data-ttu-id="da828-203">Accesso ai membri</span><span class="sxs-lookup"><span data-stu-id="da828-203">Member access</span></span>

<span data-ttu-id="da828-204">Se una `member_access` non è classificata come valore, il suo stato null è "not null".</span><span class="sxs-lookup"><span data-stu-id="da828-204">If a `member_access` is not classified as a value, its null state is "not null".</span></span> <span data-ttu-id="da828-205">In caso contrario, se si tratta di un'espressione rilevata, il relativo stato null è il relativo stato null rilevato in questa posizione di origine.</span><span class="sxs-lookup"><span data-stu-id="da828-205">Otherwise, if it is a tracked expression, its null state is its tracked null state at this source location.</span></span> <span data-ttu-id="da828-206">In caso contrario, lo stato null è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="da828-206">Otherwise its null state is the default null state of its type.</span></span>

### <a name="invocation-expressions"></a><span data-ttu-id="da828-207">Espressioni di chiamata</span><span class="sxs-lookup"><span data-stu-id="da828-207">Invocation expressions</span></span>

<span data-ttu-id="da828-208">Se un `invocation_expression` richiama un membro dichiarato con uno o più attributi per un comportamento null speciale, lo stato null è determinato da tali attributi.</span><span class="sxs-lookup"><span data-stu-id="da828-208">If an `invocation_expression` invokes a member that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="da828-209">In caso contrario, lo stato null dell'espressione è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="da828-209">Otherwise the null state of the expression is the default null state of its type.</span></span>

### <a name="element-access"></a><span data-ttu-id="da828-210">Accesso agli elementi</span><span class="sxs-lookup"><span data-stu-id="da828-210">Element access</span></span>

<span data-ttu-id="da828-211">Se un `element_access` richiama un indicizzatore dichiarato con uno o più attributi per un comportamento null speciale, lo stato null è determinato da tali attributi.</span><span class="sxs-lookup"><span data-stu-id="da828-211">If an `element_access` invokes an indexer that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="da828-212">In caso contrario, lo stato null dell'espressione è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="da828-212">Otherwise the null state of the expression is the default null state of its type.</span></span>

### <a name="base-access"></a><span data-ttu-id="da828-213">Accesso di base</span><span class="sxs-lookup"><span data-stu-id="da828-213">Base access</span></span>

<span data-ttu-id="da828-214">Se `B` indica il tipo di base del tipo di inclusione, `base.I` ha lo stesso stato null di `((B)this).I` e `base[E]` ha lo stesso stato null di `((B)this)[E]`.</span><span class="sxs-lookup"><span data-stu-id="da828-214">If `B` denotes the base type of the enclosing type, `base.I` has the same null state as `((B)this).I` and `base[E]` has the same null state as `((B)this)[E]`.</span></span>

### <a name="default-expressions"></a><span data-ttu-id="da828-215">Espressioni predefinite</span><span class="sxs-lookup"><span data-stu-id="da828-215">Default expressions</span></span>

<span data-ttu-id="da828-216">`default(T)` ha lo stato null "non null" se `T` è noto come tipo di valore che non ammette valori null.</span><span class="sxs-lookup"><span data-stu-id="da828-216">`default(T)` has the null state "non-null" if `T` is known to be a nonnullable value type.</span></span> <span data-ttu-id="da828-217">In caso contrario, lo stato null è "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="da828-217">Otherwise it has the null state "maybe null".</span></span>

### <a name="null-conditional-expressions"></a><span data-ttu-id="da828-218">Espressioni condizionali null</span><span class="sxs-lookup"><span data-stu-id="da828-218">Null-conditional expressions</span></span>

<span data-ttu-id="da828-219">Uno `null_conditional_expression` ha lo stato null "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="da828-219">A `null_conditional_expression` has the null state "maybe null".</span></span>

### <a name="cast-expressions"></a><span data-ttu-id="da828-220">Espressioni cast</span><span class="sxs-lookup"><span data-stu-id="da828-220">Cast expressions</span></span>

<span data-ttu-id="da828-221">Se un'espressione cast `(T)E` richiama una conversione definita dall'utente, lo stato null dell'espressione è lo stato null predefinito per il relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="da828-221">If a cast expression `(T)E` invokes a user-defined conversion, then the null state of the expression is the default null state for its type.</span></span> <span data-ttu-id="da828-222">In caso contrario, se `T` restituisce null (*Nullable* o *Unknown*), lo stato null è "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="da828-222">Otherwise, if `T` is null-yielding (*nullable* or *unknown*) then the null state is "maybe null".</span></span> <span data-ttu-id="da828-223">In caso contrario, lo stato null corrisponde allo stato null del `E`.</span><span class="sxs-lookup"><span data-stu-id="da828-223">Otherwise the null state is the same as the null state of `E`.</span></span>

### <a name="await-expressions"></a><span data-ttu-id="da828-224">Espressioni await</span><span class="sxs-lookup"><span data-stu-id="da828-224">Await expressions</span></span>

<span data-ttu-id="da828-225">Lo stato null del `await E` è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="da828-225">The null state of `await E` is the default null state of its type.</span></span>

### <a name="the-as-operator"></a><span data-ttu-id="da828-226">Operatore `as`</span><span class="sxs-lookup"><span data-stu-id="da828-226">The `as` operator</span></span>

<span data-ttu-id="da828-227">Un'espressione `as` ha lo stato null "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="da828-227">An `as` expression has the null state "maybe null".</span></span>

### <a name="the-null-coalescing-operator"></a><span data-ttu-id="da828-228">Operatore di Unione null</span><span class="sxs-lookup"><span data-stu-id="da828-228">The null-coalescing operator</span></span>

<span data-ttu-id="da828-229">`E1 ?? E2` ha lo stesso stato null di `E2`</span><span class="sxs-lookup"><span data-stu-id="da828-229">`E1 ?? E2` has the same null state as `E2`</span></span>

### <a name="the-conditional-operator"></a><span data-ttu-id="da828-230">Operatore condizionale</span><span class="sxs-lookup"><span data-stu-id="da828-230">The conditional operator</span></span>

<span data-ttu-id="da828-231">Lo stato null del `E1 ? E2 : E3` è "not null" se lo stato null sia di `E2` che di `E3` sono "not null".</span><span class="sxs-lookup"><span data-stu-id="da828-231">The null state of `E1 ? E2 : E3` is "not null" if the null state of both `E2` and `E3` are "not null".</span></span> <span data-ttu-id="da828-232">In caso contrario, è "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="da828-232">Otherwise it is "maybe null".</span></span>

### <a name="query-expressions"></a><span data-ttu-id="da828-233">Espressioni di query</span><span class="sxs-lookup"><span data-stu-id="da828-233">Query expressions</span></span>

<span data-ttu-id="da828-234">Lo stato null di un'espressione di query è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="da828-234">The null state of a query expression is the default null state of its type.</span></span>

### <a name="assignment-operators"></a><span data-ttu-id="da828-235">Operatori di assegnazione</span><span class="sxs-lookup"><span data-stu-id="da828-235">Assignment operators</span></span>

<span data-ttu-id="da828-236">`E1 = E2` e `E1 op= E2` hanno lo stesso stato null di `E2` dopo l'applicazione di tutte le conversioni implicite.</span><span class="sxs-lookup"><span data-stu-id="da828-236">`E1 = E2` and `E1 op= E2` have the same null state as `E2` after any implicit conversions have been applied.</span></span>

### <a name="unary-and-binary-operators"></a><span data-ttu-id="da828-237">Operatori unari e binari</span><span class="sxs-lookup"><span data-stu-id="da828-237">Unary and binary operators</span></span>

<span data-ttu-id="da828-238">Se un operatore unario o binario richiama un operatore definito dall'utente dichiarato con uno o più attributi per un comportamento null speciale, lo stato null è determinato da tali attributi.</span><span class="sxs-lookup"><span data-stu-id="da828-238">If a unary or binary operator invokes an user-defined operator that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="da828-239">In caso contrario, lo stato null dell'espressione è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="da828-239">Otherwise the null state of the expression is the default null state of its type.</span></span>

<span data-ttu-id="da828-240">***Si tratta di un'operazione speciale per la `+` binaria su stringhe e delegati?***</span><span class="sxs-lookup"><span data-stu-id="da828-240">***Something special to do for binary `+` over strings and delegates?***</span></span>

### <a name="expressions-that-propagate-null-state"></a><span data-ttu-id="da828-241">Espressioni che propagano lo stato null</span><span class="sxs-lookup"><span data-stu-id="da828-241">Expressions that propagate null state</span></span>

<span data-ttu-id="da828-242">`(E)`, `checked(E)` e `unchecked(E)` hanno tutti lo stesso stato null `E`.</span><span class="sxs-lookup"><span data-stu-id="da828-242">`(E)`, `checked(E)` and `unchecked(E)` all have the same null state as `E`.</span></span>

### <a name="expressions-that-are-never-null"></a><span data-ttu-id="da828-243">Espressioni che non sono mai null</span><span class="sxs-lookup"><span data-stu-id="da828-243">Expressions that are never null</span></span>

<span data-ttu-id="da828-244">Lo stato null dei seguenti form di espressione è sempre "not null":</span><span class="sxs-lookup"><span data-stu-id="da828-244">The null state of the following expression forms is always "not null":</span></span>

- <span data-ttu-id="da828-245">`this` accesso</span><span class="sxs-lookup"><span data-stu-id="da828-245">`this` access</span></span>
- <span data-ttu-id="da828-246">stringhe interpolate</span><span class="sxs-lookup"><span data-stu-id="da828-246">interpolated strings</span></span>
- <span data-ttu-id="da828-247">espressioni di `new` (oggetto, delegato, oggetto anonimo e espressioni di creazione di matrici)</span><span class="sxs-lookup"><span data-stu-id="da828-247">`new` expressions (object, delegate, anonymous object and array creation expressions)</span></span>
- <span data-ttu-id="da828-248">Espressioni `typeof`</span><span class="sxs-lookup"><span data-stu-id="da828-248">`typeof` expressions</span></span>
- <span data-ttu-id="da828-249">Espressioni `nameof`</span><span class="sxs-lookup"><span data-stu-id="da828-249">`nameof` expressions</span></span>
- <span data-ttu-id="da828-250">funzioni anonime (metodi anonimi ed espressioni lambda)</span><span class="sxs-lookup"><span data-stu-id="da828-250">anonymous functions (anonymous methods and lambda expressions)</span></span>
- <span data-ttu-id="da828-251">espressioni con indulgenza null</span><span class="sxs-lookup"><span data-stu-id="da828-251">null-forgiving expressions</span></span>
- <span data-ttu-id="da828-252">Espressioni `is`</span><span class="sxs-lookup"><span data-stu-id="da828-252">`is` expressions</span></span>

## <a name="type-inference"></a><span data-ttu-id="da828-253">Inferenza del tipo</span><span class="sxs-lookup"><span data-stu-id="da828-253">Type inference</span></span>

### <a name="type-inference-for-var"></a><span data-ttu-id="da828-254">Inferenza del tipo per `var`</span><span class="sxs-lookup"><span data-stu-id="da828-254">Type inference for `var`</span></span>

<span data-ttu-id="da828-255">Il tipo dedotto per le variabili locali dichiarate con `var` viene informato dallo stato null dell'espressione di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="da828-255">The type inferred for local variables declared with `var` is informed by the null state of the initializing expression.</span></span>

```csharp
var x = E;
```

<span data-ttu-id="da828-256">Se il tipo di `E` è un tipo di riferimento Nullable `C?` e lo stato null di `E` è "not null", il tipo dedotto per `x` è `C`.</span><span class="sxs-lookup"><span data-stu-id="da828-256">If the type of `E` is a nullable reference type `C?` and the null state of `E` is "not null" then the type inferred for `x` is `C`.</span></span> <span data-ttu-id="da828-257">In caso contrario, il tipo dedotto è il tipo di `E`.</span><span class="sxs-lookup"><span data-stu-id="da828-257">Otherwise, the inferred type is the type of `E`.</span></span>

<span data-ttu-id="da828-258">Il supporto di valori null del tipo dedotto per `x` viene determinato come descritto in precedenza, in base al contesto di annotazione del `var`, come se il tipo fosse stato specificato in modo esplicito in tale posizione.</span><span class="sxs-lookup"><span data-stu-id="da828-258">The nullability of the type inferred for `x` is determined as described above, based on the annotation context of the `var`, just as if the type had been given explicitly in that position.</span></span>

### <a name="type-inference-for-var"></a><span data-ttu-id="da828-259">Inferenza del tipo per `var?`</span><span class="sxs-lookup"><span data-stu-id="da828-259">Type inference for `var?`</span></span>

<span data-ttu-id="da828-260">Il tipo dedotto per le variabili locali dichiarate con `var?` è indipendente dallo stato null dell'espressione di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="da828-260">The type inferred for local variables declared with `var?` is independent of the null state of the initializing expression.</span></span>

```csharp
var? x = E;
```

<span data-ttu-id="da828-261">Se il tipo `T` di `E` è un tipo di valore nullable o un tipo di riferimento Nullable, il tipo dedotto per `x` è `T`.</span><span class="sxs-lookup"><span data-stu-id="da828-261">If the type `T` of `E` is a nullable value type or a nullable reference type then the type inferred for `x` is `T`.</span></span> <span data-ttu-id="da828-262">In caso contrario, se `T` è un tipo di valore che non ammette valori null `S` il tipo dedotto è `S?`.</span><span class="sxs-lookup"><span data-stu-id="da828-262">Otherwise, if `T` is a nonnullable value type `S` the type inferred is `S?`.</span></span> <span data-ttu-id="da828-263">In caso contrario, se `T` è un tipo di riferimento non null `C` il tipo dedotto è `C?`.</span><span class="sxs-lookup"><span data-stu-id="da828-263">Otherwise, if `T` is a nonnullable reference type `C` the type inferred is `C?`.</span></span> <span data-ttu-id="da828-264">In caso contrario, la dichiarazione non è valida.</span><span class="sxs-lookup"><span data-stu-id="da828-264">Otherwise, the declaration is illegal.</span></span>

<span data-ttu-id="da828-265">Il supporto di valori null del tipo dedotto per `x` è sempre *Nullable*.</span><span class="sxs-lookup"><span data-stu-id="da828-265">The nullability of the type inferred for `x` is always *nullable*.</span></span>

### <a name="generic-type-inference"></a><span data-ttu-id="da828-266">Inferenza del tipo generico</span><span class="sxs-lookup"><span data-stu-id="da828-266">Generic type inference</span></span>

<span data-ttu-id="da828-267">L'inferenza del tipo generico è stata migliorata per determinare se i tipi di riferimento derivati devono essere nullable o meno.</span><span class="sxs-lookup"><span data-stu-id="da828-267">Generic type inference is enhanced to help decide whether inferred reference types should be nullable or not.</span></span> <span data-ttu-id="da828-268">Si tratta di un tentativo ottimale, che non è in grado di generare avvisi, ma può causare avvisi Nullable quando i tipi dedotti dell'overload selezionato vengono applicati agli argomenti.</span><span class="sxs-lookup"><span data-stu-id="da828-268">This is a best effort, and does not in and of itself yield warnings, but may lead to nullable warnings when the inferred types of the selected overload are applied to the arguments.</span></span>

<span data-ttu-id="da828-269">L'inferenza del tipo non si basa sul contesto di annotazione dei tipi in ingresso.</span><span class="sxs-lookup"><span data-stu-id="da828-269">The type inference does not rely on the annotation context of incoming types.</span></span> <span data-ttu-id="da828-270">Viene invece dedotto un `type` che acquisisce il proprio contesto di annotazione da dove "sarebbe stato" se fosse stato espresso in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="da828-270">Instead a `type` is inferred which acquires its own annotation context from where it "would have been" if it had been expressed explicitly.</span></span> <span data-ttu-id="da828-271">In questo modo viene sottolineato il ruolo di inferenza del tipo come praticità per gli elementi che potrebbero essere stati scritti.</span><span class="sxs-lookup"><span data-stu-id="da828-271">This underscores the role of type inference as a convenience for what you could have written yourself.</span></span>

<span data-ttu-id="da828-272">Più precisamente, il contesto dell'annotazione per un argomento di tipo derivato è il contesto del token che sarebbe stato seguito dall'elenco di parametri di tipo `<...>`, ne era presente uno. ovvero il nome del metodo generico chiamato.</span><span class="sxs-lookup"><span data-stu-id="da828-272">More precisely, the annotation context for an inferred type argument is the context of the token that would have been followed by the `<...>` type parameter list, had there been one; i.e. the name of the generic method being called.</span></span> <span data-ttu-id="da828-273">Per le espressioni di query che vengono convertite in chiamate di questo tipo, il contesto viene tratto dalla parola chiave contestuale iniziale della clausola di query da cui viene generata la chiamata.</span><span class="sxs-lookup"><span data-stu-id="da828-273">For query expressions that translate to such calls, the context is taken from the initial contextual keyword of the query clause from which the call is generated.</span></span>

### <a name="the-first-phase"></a><span data-ttu-id="da828-274">Prima fase</span><span class="sxs-lookup"><span data-stu-id="da828-274">The first phase</span></span>

<span data-ttu-id="da828-275">I tipi di riferimento Nullable scorrono nei limiti delle espressioni iniziali, come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="da828-275">Nullable reference types flow into the bounds from the initial expressions, as described below.</span></span> <span data-ttu-id="da828-276">Vengono inoltre introdotti due nuovi tipi di limiti, ovvero `null` e `default`.</span><span class="sxs-lookup"><span data-stu-id="da828-276">In addition, two new kinds of bounds, namely `null` and `default` are introduced.</span></span> <span data-ttu-id="da828-277">Il loro scopo è quello di eseguire le occorrenze di `null` o `default` nelle espressioni di input, che possono causare il Nullable di un tipo derivato, anche in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="da828-277">Their purpose is to carry through occurrences of `null` or `default` in the input expressions, which may cause an inferred type to be nullable, even when it otherwise wouldn't.</span></span> <span data-ttu-id="da828-278">Funziona anche per i tipi di *valore* Nullable, che sono stati migliorati per prelevare il "valore null" nel processo di inferenza.</span><span class="sxs-lookup"><span data-stu-id="da828-278">This works even for nullable *value* types, which are enhanced to pick up "nullness" in the inference process.</span></span>

<span data-ttu-id="da828-279">La determinazione dei limiti da aggiungere nella prima fase viene migliorata nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="da828-279">The determination of what bounds to add in the first phase are enhanced as follows:</span></span>

<span data-ttu-id="da828-280">Se un argomento `Ei` ha un tipo di riferimento, il tipo `U` usato per l'inferenza dipende dallo stato null di `Ei` e dal tipo dichiarato:</span><span class="sxs-lookup"><span data-stu-id="da828-280">If an argument `Ei` has a reference type, the type `U` used for inference depends on the null state of `Ei` as well as its declared type:</span></span>
- <span data-ttu-id="da828-281">Se il tipo dichiarato è un tipo di riferimento non nullo `U0` o un tipo di riferimento Nullable `U0?`</span><span class="sxs-lookup"><span data-stu-id="da828-281">If the declared type is a nonnullable reference type `U0` or a nullable reference type `U0?` then</span></span>
    - <span data-ttu-id="da828-282">Se lo stato null del `Ei` è "not null", `U` è `U0`</span><span class="sxs-lookup"><span data-stu-id="da828-282">if the null state of `Ei` is "not null" then `U` is `U0`</span></span>
    - <span data-ttu-id="da828-283">Se lo stato null del `Ei` è "Maybe null", `U` è `U0?`</span><span class="sxs-lookup"><span data-stu-id="da828-283">if the null state of `Ei` is "maybe null" then `U` is `U0?`</span></span>
- <span data-ttu-id="da828-284">In caso contrario, se `Ei` ha un tipo dichiarato, `U` è quel tipo</span><span class="sxs-lookup"><span data-stu-id="da828-284">Otherwise if `Ei` has a declared type, `U` is that type</span></span>
- <span data-ttu-id="da828-285">In caso contrario, se `Ei` è `null` quindi `U` è il limite speciale `null`</span><span class="sxs-lookup"><span data-stu-id="da828-285">Otherwise if `Ei` is `null` then `U` is the special bound `null`</span></span>
- <span data-ttu-id="da828-286">In caso contrario, se `Ei` è `default` quindi `U` è il limite speciale `default`</span><span class="sxs-lookup"><span data-stu-id="da828-286">Otherwise if `Ei` is `default` then `U` is the special bound `default`</span></span>
- <span data-ttu-id="da828-287">In caso contrario, non viene eseguita alcuna inferenza.</span><span class="sxs-lookup"><span data-stu-id="da828-287">Otherwise no inference is made.</span></span>

### <a name="exact-upper-bound-and-lower-bound-inferences"></a><span data-ttu-id="da828-288">Inferenze esatte, con associazione superiore e con associazione inferiore</span><span class="sxs-lookup"><span data-stu-id="da828-288">Exact, upper-bound and lower-bound inferences</span></span>

<span data-ttu-id="da828-289">In inferenza *dal* tipo `U` al tipo `V`, se `V` è un tipo *di* riferimento Nullable `V0?`, viene utilizzato `V0` anziché `V` nelle clausole seguenti.</span><span class="sxs-lookup"><span data-stu-id="da828-289">In inferences *from* the type `U` *to* the type `V`, if `V` is a nullable reference type `V0?`, then `V0` is used instead of `V` in the following clauses.</span></span>
- <span data-ttu-id="da828-290">Se `V` è una delle variabili di tipo non fixed, `U` viene aggiunto come limite esatto, superiore o inferiore come prima</span><span class="sxs-lookup"><span data-stu-id="da828-290">If `V` is one of the unfixed type variables, `U` is added as an exact, upper or lower bound as before</span></span>
- <span data-ttu-id="da828-291">In caso contrario, se `U` è `null` o `default`, non viene eseguita alcuna inferenza</span><span class="sxs-lookup"><span data-stu-id="da828-291">Otherwise, if `U` is `null` or `default`, no inference is made</span></span>
- <span data-ttu-id="da828-292">In caso contrario, se `U` è un tipo di riferimento Nullable `U0?`, `U0` viene utilizzato anziché `U` nelle clausole successive.</span><span class="sxs-lookup"><span data-stu-id="da828-292">Otherwise, if `U` is a nullable reference type `U0?`, then `U0` is used instead of `U` in the subsequent clauses.</span></span>

<span data-ttu-id="da828-293">L'essenza è che i valori null che riguardano direttamente una delle variabili di tipo non fisso vengono conservati nei limiti.</span><span class="sxs-lookup"><span data-stu-id="da828-293">The essence is that nullability that pertains directly to one of the unfixed type variables is preserved into its bounds.</span></span> <span data-ttu-id="da828-294">Per gli inferenza che rimaledicono ulteriormente i tipi di origine e di destinazione, d'altra parte, il supporto dei valori null viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="da828-294">For the inferences that recurse further into the source and target types, on the other hand, nullability is ignored.</span></span> <span data-ttu-id="da828-295">Può corrispondere o meno, ma in caso contrario, verrà emesso un avviso in un secondo momento se viene scelto e applicato l'overload.</span><span class="sxs-lookup"><span data-stu-id="da828-295">It may or may not match, but if it doesn't, a warning will be issued later if the overload is chosen and applied.</span></span>

### <a name="fixing"></a><span data-ttu-id="da828-296">Fissaggio</span><span class="sxs-lookup"><span data-stu-id="da828-296">Fixing</span></span>

<span data-ttu-id="da828-297">La specifica attualmente non esegue un processo valido per descrivere cosa accade quando più limiti sono convertibili tra loro identità, ma sono diversi.</span><span class="sxs-lookup"><span data-stu-id="da828-297">The spec currently does not do a good job of describing what happens when multiple bounds are identity convertible to each other, but are different.</span></span> <span data-ttu-id="da828-298">Questo problema può verificarsi tra `object` e `dynamic`, tra i tipi di tupla che differiscono solo per i nomi degli elementi, tra i tipi costruiti e ora anche tra `C` e `C?` per i tipi di riferimento.</span><span class="sxs-lookup"><span data-stu-id="da828-298">This may happen between `object` and `dynamic`, between tuple types that differ only in element names, between types constructed thereof and now also between `C` and `C?` for reference types.</span></span>

<span data-ttu-id="da828-299">È inoltre necessario propagare "null" dalle espressioni di input al tipo di risultato.</span><span class="sxs-lookup"><span data-stu-id="da828-299">In addition we need to propagate "nullness" from the input expressions to the result type.</span></span> 

<span data-ttu-id="da828-300">Per gestire queste operazioni, è necessario aggiungere altre fasi per la correzione, che ora è:</span><span class="sxs-lookup"><span data-stu-id="da828-300">To handle these we add more phases to fixing, which is now:</span></span>

1. <span data-ttu-id="da828-301">Raccogliere tutti i tipi in tutti i limiti come candidati, rimuovendo `?` da tutti i tipi di riferimento Nullable</span><span class="sxs-lookup"><span data-stu-id="da828-301">Gather all the types in all the bounds as candidates, removing `?` from all that are nullable reference types</span></span>
2. <span data-ttu-id="da828-302">Elimina i candidati in base ai requisiti dei limiti esatti, inferiore e superiore (mantenendo i limiti di `null` e `default`)</span><span class="sxs-lookup"><span data-stu-id="da828-302">Eliminate candidates based on requirements of exact, lower and upper bounds (keeping `null` and `default` bounds)</span></span>
3. <span data-ttu-id="da828-303">Elimina i candidati che non dispongono di una conversione implicita a tutti gli altri candidati</span><span class="sxs-lookup"><span data-stu-id="da828-303">Eliminate candidates that do not have an implicit conversion to all the other candidates</span></span>
4. <span data-ttu-id="da828-304">Se i candidati rimanenti non hanno tutte le conversioni di identità tra loro, l'inferenza del tipo ha esito negativo</span><span class="sxs-lookup"><span data-stu-id="da828-304">If the remaining candidates do not all have identity conversions to one another, then type inference fails</span></span>
5. <span data-ttu-id="da828-305">*Unisci* i candidati rimanenti come descritto di seguito</span><span class="sxs-lookup"><span data-stu-id="da828-305">*Merge* the remaining candidates as described below</span></span>
6. <span data-ttu-id="da828-306">Se il candidato risultante è un tipo di riferimento o un tipo di valore non null e *tutti* i limiti esatti o *uno* dei limiti inferiori sono tipi di valore Nullable, tipi di riferimento nullable, `null` o `default`, `?` viene aggiunto al candidato risultante, rendendolo un tipo di valore nullable o un tipo di riferimento.</span><span class="sxs-lookup"><span data-stu-id="da828-306">If the resulting candidate is a reference type or a nonnullable value type and *all* of the exact bounds or *any* of the lower bounds are nullable value types, nullable reference types, `null` or `default`, then `?` is added to the resulting candidate, making it a nullable value type or reference type.</span></span>

<span data-ttu-id="da828-307">L' *Unione* viene descritta tra due tipi candidati.</span><span class="sxs-lookup"><span data-stu-id="da828-307">*Merging* is described between two candidate types.</span></span> <span data-ttu-id="da828-308">Si tratta di transitive e commutative, quindi i candidati possono essere Uniti in qualsiasi ordine con lo stesso risultato finale.</span><span class="sxs-lookup"><span data-stu-id="da828-308">It is transitive and commutative, so the candidates can be merged in any order with the same ultimate result.</span></span> <span data-ttu-id="da828-309">Non è definito se i due tipi candidati non sono convertibili in identità.</span><span class="sxs-lookup"><span data-stu-id="da828-309">It is undefined if the two candidate types are not identity convertible to each other.</span></span>

<span data-ttu-id="da828-310">La funzione *merge* accetta due tipi candidati e una direzione ( *+* o *-* ):</span><span class="sxs-lookup"><span data-stu-id="da828-310">The *Merge* function takes two candidate types and a direction (*+* or *-*):</span></span>

- <span data-ttu-id="da828-311">*Merge*(`T`, `T`, *d*) = t</span><span class="sxs-lookup"><span data-stu-id="da828-311">*Merge*(`T`, `T`, *d*) = T</span></span>
- <span data-ttu-id="da828-312">*Merge*(`S`, `T?`, *+* ) = *merge*(`S?`, `T`, *+* ) = *merge*(`S`, `T`, *+* )`?`</span><span class="sxs-lookup"><span data-stu-id="da828-312">*Merge*(`S`, `T?`, *+*) = *Merge*(`S?`, `T`, *+*) = *Merge*(`S`, `T`, *+*)`?`</span></span>
- <span data-ttu-id="da828-313">*Merge*(`S`, `T?`, *-* ) = *merge*(`S?`, `T`, *-* ) = *merge*(`S`, `T`, *-* )</span><span class="sxs-lookup"><span data-stu-id="da828-313">*Merge*(`S`, `T?`, *-*) = *Merge*(`S?`, `T`, *-*) = *Merge*(`S`, `T`, *-*)</span></span>
- <span data-ttu-id="da828-314">*Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *+* ) = `C<`*merge*(`S1`, `T1`, *d1*)`,...,`*merge*(`Sn`, `Tn`, *DN*)`>`, *dove*</span><span class="sxs-lookup"><span data-stu-id="da828-314">*Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *+*) = `C<`*Merge*(`S1`, `T1`, *d1*)`,...,`*Merge*(`Sn`, `Tn`, *dn*)`>`, *where*</span></span>
    - <span data-ttu-id="da828-315">`di` =  *+* se il parametro di tipo `i`' di `C<...>` è covariante</span><span class="sxs-lookup"><span data-stu-id="da828-315">`di` = *+* if the `i`'th type parameter of `C<...>` is covariant</span></span>
    - <span data-ttu-id="da828-316">`di` =  *-* se il parametro di tipo `i`' di `C<...>` è contra-o invariante</span><span class="sxs-lookup"><span data-stu-id="da828-316">`di` = *-* if the `i`'th type parameter of `C<...>` is contra- or invariant</span></span>
- <span data-ttu-id="da828-317">*Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *-* ) = `C<`*merge*(`S1`, `T1`, *d1*)`,...,`*merge*(`Sn`, `Tn`, *DN*)`>`, *dove*</span><span class="sxs-lookup"><span data-stu-id="da828-317">*Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *-*) = `C<`*Merge*(`S1`, `T1`, *d1*)`,...,`*Merge*(`Sn`, `Tn`, *dn*)`>`, *where*</span></span>
    - <span data-ttu-id="da828-318">`di` =  *-* se il parametro di tipo `i`' di `C<...>` è covariante</span><span class="sxs-lookup"><span data-stu-id="da828-318">`di` = *-* if the `i`'th type parameter of `C<...>` is covariant</span></span>
    - <span data-ttu-id="da828-319">`di` =  *+* se il parametro di tipo `i`' di `C<...>` è contra-o invariante</span><span class="sxs-lookup"><span data-stu-id="da828-319">`di` = *+* if the `i`'th type parameter of `C<...>` is contra- or invariant</span></span>
- <span data-ttu-id="da828-320">*Merge*(`(S1 s1,..., Sn sn)`, `(T1 t1,..., Tn tn)`, *d*) = `(`*merge*(`S1`, `T1`, *d*)`n1,...,`*merge*(`Sn`, `Tn`, *d*) `nn)`, *dove*</span><span class="sxs-lookup"><span data-stu-id="da828-320">*Merge*(`(S1 s1,..., Sn sn)`, `(T1 t1,..., Tn tn)`, *d*) = `(`*Merge*(`S1`, `T1`, *d*)`n1,...,`*Merge*(`Sn`, `Tn`, *d*) `nn)`, *where*</span></span>
    - <span data-ttu-id="da828-321">`ni` è assente se `si` e `ti` sono diverse o se entrambe sono assenti</span><span class="sxs-lookup"><span data-stu-id="da828-321">`ni` is absent if `si` and `ti` differ, or if both are absent</span></span>
    - <span data-ttu-id="da828-322">`ni` viene `si` se `si` e `ti` sono uguali</span><span class="sxs-lookup"><span data-stu-id="da828-322">`ni` is `si` if `si` and `ti` are the same</span></span>
- <span data-ttu-id="da828-323">*Merge*(`object`, `dynamic`) = *merge*(`dynamic`, `object`) = `dynamic`</span><span class="sxs-lookup"><span data-stu-id="da828-323">*Merge*(`object`, `dynamic`) = *Merge*(`dynamic`, `object`) = `dynamic`</span></span>

## <a name="warnings"></a><span data-ttu-id="da828-324">Avvisi</span><span class="sxs-lookup"><span data-stu-id="da828-324">Warnings</span></span>

### <a name="potential-null-assignment"></a><span data-ttu-id="da828-325">Potenziale assegnazione null</span><span class="sxs-lookup"><span data-stu-id="da828-325">Potential null assignment</span></span>

### <a name="potential-null-dereference"></a><span data-ttu-id="da828-326">Potenziale dereferenziazione null</span><span class="sxs-lookup"><span data-stu-id="da828-326">Potential null dereference</span></span>

### <a name="constraint-nullability-mismatch"></a><span data-ttu-id="da828-327">Mancata corrispondenza dei valori null per i vincoli</span><span class="sxs-lookup"><span data-stu-id="da828-327">Constraint nullability mismatch</span></span>

### <a name="nullable-types-in-disabled-annotation-context"></a><span data-ttu-id="da828-328">Tipi nullable nel contesto di annotazione disabilitato</span><span class="sxs-lookup"><span data-stu-id="da828-328">Nullable types in disabled annotation context</span></span>

## <a name="attributes-for-special-null-behavior"></a><span data-ttu-id="da828-329">Attributi per un comportamento null speciale</span><span class="sxs-lookup"><span data-stu-id="da828-329">Attributes for special null behavior</span></span>


