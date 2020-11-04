---
ms.openlocfilehash: 39291abb97f11662520467fbe0e3430ea2da621d
ms.sourcegitcommit: 29df547564c4ffc51b1dedf8369dc91e8f0ba854
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93344346"
---
# <a name="nullable-reference-types-specification"></a><span data-ttu-id="25b56-101">Specifica di tipi di riferimento Nullable</span><span class="sxs-lookup"><span data-stu-id="25b56-101">Nullable Reference Types Specification</span></span>

<span data-ttu-id="25b56-102">Questo è un lavoro in corso. diverse parti sono mancanti o incomplete.</span><span class="sxs-lookup"><span data-stu-id="25b56-102">\*\*\*This is a work in progress - several parts are missing or incomplete.</span></span> <span data-ttu-id="25b56-103">Una versione aggiornata di questo documento è disponibile nella cartella C# 9.</span><span class="sxs-lookup"><span data-stu-id="25b56-103">An updated version of this document can be found in the C# 9 folder.</span></span> <span data-ttu-id="25b56-104">\*\*_</span><span class="sxs-lookup"><span data-stu-id="25b56-104">\*\*_</span></span>

## <a name="syntax"></a><span data-ttu-id="25b56-105">Sintassi</span><span class="sxs-lookup"><span data-stu-id="25b56-105">Syntax</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="25b56-106">Tipi riferimento nullable</span><span class="sxs-lookup"><span data-stu-id="25b56-106">Nullable reference types</span></span>

<span data-ttu-id="25b56-107">I tipi di riferimento Nullable hanno la stessa sintassi della `T?` forma abbreviata di tipi di valore Nullable, ma non hanno una forma estesa corrispondente.</span><span class="sxs-lookup"><span data-stu-id="25b56-107">Nullable reference types have the same syntax `T?` as the short form of nullable value types, but do not have a corresponding long form.</span></span>

<span data-ttu-id="25b56-108">Ai fini della specifica, la `nullable_type` produzione corrente viene rinominata in `nullable_value_type` e `nullable_reference_type` viene aggiunta una produzione:</span><span class="sxs-lookup"><span data-stu-id="25b56-108">For the purposes of the specification, the current `nullable_type` production is renamed to `nullable_value_type`, and a `nullable_reference_type` production is added:</span></span>

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

<span data-ttu-id="25b56-109">`non_nullable_reference_type`In un `nullable_reference_type` deve essere un tipo di riferimento non Nullable (classe, interfaccia, delegato o matrice) oppure un parametro di tipo vincolato a un tipo di riferimento non Nullable (tramite il `class` vincolo o una classe diversa da `object` ).</span><span class="sxs-lookup"><span data-stu-id="25b56-109">The `non_nullable_reference_type` in a `nullable_reference_type` must be a non-nullable reference type (class, interface, delegate or array), or a type parameter that is constrained to be a non-nullable reference type (through the `class` constraint, or a class other than `object`).</span></span>

<span data-ttu-id="25b56-110">I tipi di riferimento nullable non possono essere presenti nelle posizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="25b56-110">Nullable reference types cannot occur in the following positions:</span></span>

- <span data-ttu-id="25b56-111">come classe o interfaccia di base</span><span class="sxs-lookup"><span data-stu-id="25b56-111">as a base class or interface</span></span>
- <span data-ttu-id="25b56-112">come ricevitore di un `member_access`</span><span class="sxs-lookup"><span data-stu-id="25b56-112">as the receiver of a `member_access`</span></span>
- <span data-ttu-id="25b56-113">come `type` in un `object_creation_expression`</span><span class="sxs-lookup"><span data-stu-id="25b56-113">as the `type` in an `object_creation_expression`</span></span>
- <span data-ttu-id="25b56-114">come `delegate_type` in un `delegate_creation_expression`</span><span class="sxs-lookup"><span data-stu-id="25b56-114">as the `delegate_type` in a `delegate_creation_expression`</span></span>
- <span data-ttu-id="25b56-115">come `type` in un oggetto `is_expression` , un oggetto `catch_clause` o un oggetto `type_pattern`</span><span class="sxs-lookup"><span data-stu-id="25b56-115">as the `type` in an `is_expression`, a `catch_clause` or a `type_pattern`</span></span>
- <span data-ttu-id="25b56-116">come `interface` in un nome di membro di interfaccia completo</span><span class="sxs-lookup"><span data-stu-id="25b56-116">as the `interface` in a fully qualified interface member name</span></span>

<span data-ttu-id="25b56-117">Viene fornito un avviso in un oggetto in `nullable_reference_type` cui il contesto di annotazione Nullable è disabilitato.</span><span class="sxs-lookup"><span data-stu-id="25b56-117">A warning is given on a `nullable_reference_type` where the nullable annotation context is disabled.</span></span>

### <a name="nullable-class-constraint"></a><span data-ttu-id="25b56-118">Vincolo di classe Nullable</span><span class="sxs-lookup"><span data-stu-id="25b56-118">Nullable class constraint</span></span>

<span data-ttu-id="25b56-119">Il `class` vincolo presenta una controparte Nullable `class?` :</span><span class="sxs-lookup"><span data-stu-id="25b56-119">The `class` constraint has a nullable counterpart `class?`:</span></span>

```antlr
primary_constraint
    : ...
    | 'class' '?'
    ;
```

### <a name="the-null-forgiving-operator"></a><span data-ttu-id="25b56-120">Operatore che perdona i valori null</span><span class="sxs-lookup"><span data-stu-id="25b56-120">The null-forgiving operator</span></span>

<span data-ttu-id="25b56-121">L'operatore post-correzione `!` è denominato operatore che perdona i valori null.</span><span class="sxs-lookup"><span data-stu-id="25b56-121">The post-fix `!` operator is called the null-forgiving operator.</span></span>

```antlr
primary_expression
    : ...
    | null_forgiving_expression
    ;
    
null_forgiving_expression
    : primary_expression '!'
    ;
```

<span data-ttu-id="25b56-122">`primary_expression`Deve essere di un tipo di riferimento.</span><span class="sxs-lookup"><span data-stu-id="25b56-122">The `primary_expression` must be of a reference type.</span></span>  

<span data-ttu-id="25b56-123">L' `!` operatore di suffisso non ha alcun effetto in fase di esecuzione. restituisce il risultato dell'espressione sottostante.</span><span class="sxs-lookup"><span data-stu-id="25b56-123">The postfix `!` operator has no runtime effect - it evaluates to the result of the underlying expression.</span></span> <span data-ttu-id="25b56-124">Il suo unico ruolo è modificare lo stato null dell'espressione e limitare gli avvisi in base al relativo utilizzo.</span><span class="sxs-lookup"><span data-stu-id="25b56-124">Its only role is to change the null state of the expression, and to limit warnings given on its use.</span></span>

### <a name="nullable-implicitly-typed-local-variables"></a><span data-ttu-id="25b56-125">variabili locali tipizzate in modo implicito Nullable</span><span class="sxs-lookup"><span data-stu-id="25b56-125">nullable implicitly typed local variables</span></span>

<span data-ttu-id="25b56-126">`var` deduce un tipo con annotazioni per i tipi di riferimento.</span><span class="sxs-lookup"><span data-stu-id="25b56-126">`var` infers an annotated type for reference types.</span></span>
<span data-ttu-id="25b56-127">Ad esempio, in `var s = "";` `var` viene dedotto come `string?` .</span><span class="sxs-lookup"><span data-stu-id="25b56-127">For instance, in `var s = "";` the `var` is inferred as `string?`.</span></span>

### <a name="nullable-compiler-directives"></a><span data-ttu-id="25b56-128">Direttive del compilatore Nullable</span><span class="sxs-lookup"><span data-stu-id="25b56-128">Nullable compiler directives</span></span>

<span data-ttu-id="25b56-129">`#nullable` le direttive controllano i contesti di annotazione e avviso Nullable.</span><span class="sxs-lookup"><span data-stu-id="25b56-129">`#nullable` directives control the nullable annotation and warning contexts.</span></span>

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

<span data-ttu-id="25b56-130">`#pragma warning` le direttive vengono espanse per consentire la modifica del contesto di avviso nullable e per consentire l'abilitazione di singoli avvisi anche quando sono disabilitati per impostazione predefinita:</span><span class="sxs-lookup"><span data-stu-id="25b56-130">`#pragma warning` directives are expanded to allow changing the nullable warning context, and to allow individual warnings to be enabled on even when they're disabled by default:</span></span>

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

<span data-ttu-id="25b56-131">Si noti che il nuovo formato di `pragma_warning_body` Usa `nullable_action` , non `warning_action` .</span><span class="sxs-lookup"><span data-stu-id="25b56-131">Note that the new form of `pragma_warning_body` uses `nullable_action`, not `warning_action`.</span></span>

## <a name="nullable-contexts"></a><span data-ttu-id="25b56-132">Contesti nullable</span><span class="sxs-lookup"><span data-stu-id="25b56-132">Nullable contexts</span></span>

<span data-ttu-id="25b56-133">Ogni riga del codice sorgente ha un _nullable contesto di annotazione \* e un *contesto di avviso Nullable*.</span><span class="sxs-lookup"><span data-stu-id="25b56-133">Every line of source code has a _nullable annotation context\* and a *nullable warning context*.</span></span> <span data-ttu-id="25b56-134">Questi controllano se le annotazioni Nullable hanno effetto e se vengono specificati avvisi di supporto dei valori null.</span><span class="sxs-lookup"><span data-stu-id="25b56-134">These control whether nullable annotations have effect, and whether nullability warnings are given.</span></span> <span data-ttu-id="25b56-135">Il contesto di annotazione di una determinata riga è *disabilitato* o *abilitato*.</span><span class="sxs-lookup"><span data-stu-id="25b56-135">The annotation context of a given line is either *disabled* or *enabled*.</span></span> <span data-ttu-id="25b56-136">Il contesto di avviso di una determinata riga è *disabilitato* o *abilitato*.</span><span class="sxs-lookup"><span data-stu-id="25b56-136">The warning context of a given line is either *disabled* or *enabled*.</span></span>

<span data-ttu-id="25b56-137">Entrambi i contesti possono essere specificati a livello di progetto (all'esterno del codice sorgente C#) o in qualsiasi punto all'interno di un file di origine tramite `#nullable` direttive pre-processore.</span><span class="sxs-lookup"><span data-stu-id="25b56-137">Both contexts can be specified at the project level (outside of C# source code), or anywhere within a source file via `#nullable` pre-processor directives.</span></span> <span data-ttu-id="25b56-138">Se non vengono specificate impostazioni a livello di progetto, il valore predefinito è per entrambi i contesti da *disabilitare*.</span><span class="sxs-lookup"><span data-stu-id="25b56-138">If no project level settings are provided the default is for both contexts to be *disabled*.</span></span>

<span data-ttu-id="25b56-139">La `#nullable` direttiva controlla l'annotazione e i contesti di avviso all'interno del testo di origine e hanno la precedenza sulle impostazioni a livello di progetto.</span><span class="sxs-lookup"><span data-stu-id="25b56-139">The `#nullable` directive controls the annotation and warning contexts within the source text, and take precedence over the project-level settings.</span></span>

<span data-ttu-id="25b56-140">Una direttiva imposta i contesti che controlla per le righe di codice successive, fino a quando un'altra direttiva ne esegue l'override o fino alla fine del file di origine.</span><span class="sxs-lookup"><span data-stu-id="25b56-140">A directive sets the context(s) it controls for subsequent lines of code, until another directive overrides it, or until the end of the source file.</span></span>

<span data-ttu-id="25b56-141">L'effetto delle direttive è il seguente:</span><span class="sxs-lookup"><span data-stu-id="25b56-141">The effect of the directives is as follows:</span></span>

- <span data-ttu-id="25b56-142">`#nullable disable`: Imposta l'annotazione nullable e i contesti di avviso su *disabled*</span><span class="sxs-lookup"><span data-stu-id="25b56-142">`#nullable disable`: Sets the nullable annotation and warning contexts to *disabled*</span></span>
- <span data-ttu-id="25b56-143">`#nullable enable`: Imposta l'annotazione nullable e i contesti di avviso su *Enabled*</span><span class="sxs-lookup"><span data-stu-id="25b56-143">`#nullable enable`: Sets the nullable annotation and warning contexts to *enabled*</span></span>
- <span data-ttu-id="25b56-144">`#nullable restore`: Ripristina i contesti di avviso e di annotazione Nullable nelle impostazioni del progetto</span><span class="sxs-lookup"><span data-stu-id="25b56-144">`#nullable restore`: Restores the nullable annotation and warning contexts to project settings</span></span>
- <span data-ttu-id="25b56-145">`#nullable disable annotations`: Imposta il contesto di annotazione Nullable su *disabled*</span><span class="sxs-lookup"><span data-stu-id="25b56-145">`#nullable disable annotations`: Sets the nullable annotation context to *disabled*</span></span>
- <span data-ttu-id="25b56-146">`#nullable enable annotations`: Imposta il contesto di annotazione Nullable su *Enabled*</span><span class="sxs-lookup"><span data-stu-id="25b56-146">`#nullable enable annotations`: Sets the nullable annotation context to *enabled*</span></span>
- <span data-ttu-id="25b56-147">`#nullable restore annotations`: Ripristina il contesto di annotazione Nullable nelle impostazioni del progetto</span><span class="sxs-lookup"><span data-stu-id="25b56-147">`#nullable restore annotations`: Restores the nullable annotation context to project settings</span></span>
- <span data-ttu-id="25b56-148">`#nullable disable warnings`: Imposta il contesto di avviso Nullable su *disabled*</span><span class="sxs-lookup"><span data-stu-id="25b56-148">`#nullable disable warnings`: Sets the nullable warning context to *disabled*</span></span>
- <span data-ttu-id="25b56-149">`#nullable enable warnings`: Imposta il contesto di avviso Nullable su *Enabled*</span><span class="sxs-lookup"><span data-stu-id="25b56-149">`#nullable enable warnings`: Sets the nullable warning context to *enabled*</span></span>
- <span data-ttu-id="25b56-150">`#nullable restore warnings`: Ripristina il contesto di avviso Nullable nelle impostazioni del progetto</span><span class="sxs-lookup"><span data-stu-id="25b56-150">`#nullable restore warnings`: Restores the nullable warning context to project settings</span></span>

## <a name="nullability-of-types"></a><span data-ttu-id="25b56-151">Supporto dei valori Null dei tipi</span><span class="sxs-lookup"><span data-stu-id="25b56-151">Nullability of types</span></span>

<span data-ttu-id="25b56-152">Un tipo specificato può avere uno dei quattro nullabilities: *ignaro* , non *null* , *Nullable* e *Unknown*.</span><span class="sxs-lookup"><span data-stu-id="25b56-152">A given type can have one of four nullabilities: *Oblivious* , *nonnullable* , *nullable* and *unknown*.</span></span> 

<span data-ttu-id="25b56-153">I tipi *sconosciuti* e non *null* possono causare avvisi se `null` viene assegnato un valore potenziale.</span><span class="sxs-lookup"><span data-stu-id="25b56-153">*Nonnullable* and *unknown* types may cause warnings if a potential `null` value is assigned to them.</span></span> <span data-ttu-id="25b56-154">I tipi *ignari* e *Nullable* , tuttavia, sono " *assegnabili a null* " e possono avere `null` valori assegnati senza avvisi.</span><span class="sxs-lookup"><span data-stu-id="25b56-154">*Oblivious* and *nullable* types, however, are " *null-assignable* " and can have `null` values assigned to them without warnings.</span></span> 

<span data-ttu-id="25b56-155">I tipi *ignari* e non *null* possono essere dereferenziati o assegnati senza avvisi.</span><span class="sxs-lookup"><span data-stu-id="25b56-155">*Oblivious* and *nonnullable* types can be dereferenced or assigned without warnings.</span></span> <span data-ttu-id="25b56-156">I valori di tipi *Nullable* e *Unknown* , tuttavia, sono " *null-Yielding* " e possono causare avvisi quando vengono dereferenziati o assegnati senza il controllo null appropriato.</span><span class="sxs-lookup"><span data-stu-id="25b56-156">Values of *nullable* and *unknown* types, however, are " *null-yielding* " and may cause warnings when dereferenced or assigned without proper null checking.</span></span> 

<span data-ttu-id="25b56-157">Lo *stato null predefinito* di un tipo che cede un valore null è "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="25b56-157">The *default null state* of a null-yielding type is "maybe null".</span></span> <span data-ttu-id="25b56-158">Lo stato null predefinito di un tipo non null restituisce "not null".</span><span class="sxs-lookup"><span data-stu-id="25b56-158">The default null state of a non-null-yielding type is "not null".</span></span>

<span data-ttu-id="25b56-159">Il tipo di tipo e il contesto di annotazione nullable in cui si verificano determinano il supporto di valori null:</span><span class="sxs-lookup"><span data-stu-id="25b56-159">The kind of type and the nullable annotation context it occurs in determine its nullability:</span></span>

- <span data-ttu-id="25b56-160">Un tipo di valore che non ammette valori null `S` è sempre non *null*</span><span class="sxs-lookup"><span data-stu-id="25b56-160">A nonnullable value type `S` is always *nonnullable*</span></span>
- <span data-ttu-id="25b56-161">Un tipo di valore Nullable `S?` è sempre *Nullable*</span><span class="sxs-lookup"><span data-stu-id="25b56-161">A nullable value type `S?` is always *nullable*</span></span>
- <span data-ttu-id="25b56-162">Un tipo di riferimento senza annotazioni `C` in un contesto di annotazione *disabilitato* è *ignaro*</span><span class="sxs-lookup"><span data-stu-id="25b56-162">An unannotated reference type `C` in a *disabled* annotation context is *oblivious*</span></span>
- <span data-ttu-id="25b56-163">Un tipo di riferimento non annotato `C` in un contesto di annotazione *abilitato* non è *null*</span><span class="sxs-lookup"><span data-stu-id="25b56-163">An unannotated reference type `C` in an *enabled* annotation context is *nonnullable*</span></span>
- <span data-ttu-id="25b56-164">Un tipo di riferimento Nullable ammette i `C?` *valori null* (ma è possibile che venga restituito un avviso in un contesto di annotazione *disabilitato* )</span><span class="sxs-lookup"><span data-stu-id="25b56-164">A nullable reference type `C?` is *nullable* (but a warning may be yielded in a *disabled* annotation context)</span></span>

<span data-ttu-id="25b56-165">I parametri di tipo prendono inoltre in considerazione i vincoli:</span><span class="sxs-lookup"><span data-stu-id="25b56-165">Type parameters additionally take their constraints into account:</span></span>

- <span data-ttu-id="25b56-166">Parametro di tipo in `T` cui tutti i vincoli (se presenti) sono tipi che restituiscono valori null ( *Nullable* e *Unknown* ) oppure il `class?` vincolo è *sconosciuto*</span><span class="sxs-lookup"><span data-stu-id="25b56-166">A type parameter `T` where all constraints (if any) are either null-yielding types ( *nullable* and *unknown* ) or the `class?` constraint is *unknown*</span></span>
- <span data-ttu-id="25b56-167">Parametro di tipo `T` in cui almeno un vincolo è *ignaro* o non può essere *null* o uno dei `struct` `class` vincoli o è</span><span class="sxs-lookup"><span data-stu-id="25b56-167">A type parameter `T` where at least one constraint is either *oblivious* or *nonnullable* or one of the `struct` or `class` constraints is</span></span>
    - <span data-ttu-id="25b56-168">*ignaro* in un contesto di annotazione *disabilitato*</span><span class="sxs-lookup"><span data-stu-id="25b56-168">*oblivious* in a *disabled* annotation context</span></span>
    - <span data-ttu-id="25b56-169">non *ammette valori null* in un contesto di annotazione *abilitato*</span><span class="sxs-lookup"><span data-stu-id="25b56-169">*nonnullable* in an *enabled* annotation context</span></span>
- <span data-ttu-id="25b56-170">Parametro di tipo Nullable `T?` in cui almeno uno dei `T` vincoli è *ignaro* o non può essere *null* o uno dei `struct` `class` vincoli o, is</span><span class="sxs-lookup"><span data-stu-id="25b56-170">A nullable type parameter `T?` where at least one of `T`'s constraints is *oblivious* or *nonnullable* or one of the `struct` or `class` constraints, is</span></span>
    - <span data-ttu-id="25b56-171">*Nullable* in un contesto di annotazione *disabilitato* (ma viene restituito un avviso)</span><span class="sxs-lookup"><span data-stu-id="25b56-171">*nullable* in a *disabled* annotation context (but a warning is yielded)</span></span>
    - <span data-ttu-id="25b56-172">*Nullable* in un contesto di annotazione *abilitato*</span><span class="sxs-lookup"><span data-stu-id="25b56-172">*nullable* in an *enabled* annotation context</span></span>

<span data-ttu-id="25b56-173">Per un parametro di tipo `T` , `T?` è consentito solo se è noto come tipo di `T` valore o è noto come tipo di riferimento.</span><span class="sxs-lookup"><span data-stu-id="25b56-173">For a type parameter `T`, `T?` is only allowed if `T` is known to be a value type or known to be a reference type.</span></span>

### <a name="nested-functions"></a><span data-ttu-id="25b56-174">Funzioni annidate</span><span class="sxs-lookup"><span data-stu-id="25b56-174">Nested functions</span></span>

<span data-ttu-id="25b56-175">Le funzioni annidate (espressioni lambda e funzioni locali) vengono trattate come metodi, tranne che per quanto riguarda le variabili acquisite.</span><span class="sxs-lookup"><span data-stu-id="25b56-175">Nested functions (lambdas and local functions) are treated like methods, except in regards to their captured variables.</span></span>
<span data-ttu-id="25b56-176">Lo stato predefinito di una variabile acquisita all'interno di un'espressione lambda o di una funzione locale è l'intersezione dello stato Nullable della variabile in tutti gli "utilizzi" della funzione nidificata.</span><span class="sxs-lookup"><span data-stu-id="25b56-176">The default state of a captured variable inside a lambda or local function is the intersection of the nullable state of the variable at all the "uses" of that nested function.</span></span> <span data-ttu-id="25b56-177">L'uso di una funzione è una chiamata a tale funzione o in cui viene convertito in un delegato.</span><span class="sxs-lookup"><span data-stu-id="25b56-177">A use of a function is either a call to that function, or where it is converted to a delegate.</span></span>

### <a name="oblivious-vs-nonnullable"></a><span data-ttu-id="25b56-178">E non nullable</span><span class="sxs-lookup"><span data-stu-id="25b56-178">Oblivious vs nonnullable</span></span>

<span data-ttu-id="25b56-179">Un `type` viene considerato che si verifica in un contesto di annotazione specificato quando l'ultimo token del tipo si trova all'interno di tale contesto.</span><span class="sxs-lookup"><span data-stu-id="25b56-179">A `type` is deemed to occur in a given annotation context when the last token of the type is within that context.</span></span>

<span data-ttu-id="25b56-180">Se un tipo di riferimento specificato `C` nel codice sorgente viene interpretato come ignaro o non nullo dipende dal contesto dell'annotazione di tale codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="25b56-180">Whether a given reference type `C` in source code is interpreted as oblivious or nonnullable depends on the annotation context of that source code.</span></span> <span data-ttu-id="25b56-181">Una volta stabilito, viene considerato parte di quel tipo e "lo sposta con esso", ad esempio durante la sostituzione di argomenti di tipo generico.</span><span class="sxs-lookup"><span data-stu-id="25b56-181">But once established, it is considered part of that type, and "travels with it" e.g. during substitution of generic type arguments.</span></span> <span data-ttu-id="25b56-182">È come se fosse presente un'annotazione come `?` nel tipo, ma invisibile.</span><span class="sxs-lookup"><span data-stu-id="25b56-182">It is as if there is an annotation like `?` on the type, but invisible.</span></span>

## <a name="constraints"></a><span data-ttu-id="25b56-183">Vincoli</span><span class="sxs-lookup"><span data-stu-id="25b56-183">Constraints</span></span>

<span data-ttu-id="25b56-184">I tipi di riferimento nullable possono essere utilizzati come vincoli generici.</span><span class="sxs-lookup"><span data-stu-id="25b56-184">Nullable reference types can be used as generic constraints.</span></span> <span data-ttu-id="25b56-185">Inoltre `object` , è ora valido come vincolo esplicito.</span><span class="sxs-lookup"><span data-stu-id="25b56-185">Furthermore `object` is now valid as an explicit constraint.</span></span> <span data-ttu-id="25b56-186">L'assenza di un vincolo è ora equivalente a un `object?` vincolo (anziché `object` ), ma (a differenza di `object` before) `object?` non è vietato come vincolo esplicito.</span><span class="sxs-lookup"><span data-stu-id="25b56-186">Absence of a constraint is now equivalent to an `object?` constraint (instead of `object`), but (unlike `object` before) `object?` is not prohibited as an explicit constraint.</span></span>

<span data-ttu-id="25b56-187">`class?` è un nuovo vincolo che indica "probabile tipo di riferimento Nullable", mentre denota `class` "tipo di riferimento non null".</span><span class="sxs-lookup"><span data-stu-id="25b56-187">`class?` is a new constraint denoting "possibly nullable reference type", whereas `class` denotes "nonnullable reference type".</span></span>

<span data-ttu-id="25b56-188">Il supporto di valori null di un argomento di tipo o di un vincolo non ha alcun effetto sul fatto che il tipo soddisfi il vincolo, ad eccezione del caso in cui è già presente. i tipi di valore nullable non soddisfano il `struct` vincolo.</span><span class="sxs-lookup"><span data-stu-id="25b56-188">The nullability of a type argument or of a constraint does not impact whether the type satisfies the constraint, except where that is already the case today (nullable value types do not satisfy the `struct` constraint).</span></span> <span data-ttu-id="25b56-189">Tuttavia, se l'argomento di tipo non soddisfa i requisiti di supporto dei valori null del vincolo, è possibile che venga fornito un avviso.</span><span class="sxs-lookup"><span data-stu-id="25b56-189">However, if the type argument does not satisfy the nullability requirements of the constraint, a warning may be given.</span></span>

## <a name="null-state-and-null-tracking"></a><span data-ttu-id="25b56-190">Stato null e rilevamento null</span><span class="sxs-lookup"><span data-stu-id="25b56-190">Null state and null tracking</span></span>

<span data-ttu-id="25b56-191">Ogni espressione in una determinata posizione di origine ha uno *stato null* , che indica se si ritiene che possa restituire potenzialmente null.</span><span class="sxs-lookup"><span data-stu-id="25b56-191">Every expression in a given source location has a *null state* , which indicated whether it is believed to potentially evaluate to null.</span></span> <span data-ttu-id="25b56-192">Lo stato null può essere "not null" o "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="25b56-192">The null state is either "not null" or "maybe null".</span></span> <span data-ttu-id="25b56-193">Lo stato null viene utilizzato per determinare se è necessario che venga fornito un avviso sulle conversioni e le dereferenziazioni non sicure null.</span><span class="sxs-lookup"><span data-stu-id="25b56-193">The null state is used to determine whether a warning should be given about null-unsafe conversions and dereferences.</span></span>

### <a name="null-tracking-for-variables"></a><span data-ttu-id="25b56-194">Rilevamento di valori null per le variabili</span><span class="sxs-lookup"><span data-stu-id="25b56-194">Null tracking for variables</span></span>

<span data-ttu-id="25b56-195">Per alcune espressioni che indicano variabili o proprietà, lo stato null viene rilevato tra le occorrenze, in base alle assegnazioni, i test eseguiti su di essi e il flusso di controllo tra di essi.</span><span class="sxs-lookup"><span data-stu-id="25b56-195">For certain expressions denoting variables or properties, the null state is tracked between occurrences, based on assignments to them, tests performed on them and the control flow between them.</span></span> <span data-ttu-id="25b56-196">Questa operazione è simile alla modalità di rilevamento dell'assegnazione definita per le variabili.</span><span class="sxs-lookup"><span data-stu-id="25b56-196">This is similar to how definite assignment is tracked for variables.</span></span> <span data-ttu-id="25b56-197">Le espressioni registrate sono quelle con il formato seguente:</span><span class="sxs-lookup"><span data-stu-id="25b56-197">The tracked expressions are the ones of the following form:</span></span>

```antlr
tracked_expression
    : simple_name
    | this
    | base
    | tracked_expression '.' identifier
    ;
```

<span data-ttu-id="25b56-198">Dove gli identificatori denotano i campi o le proprietà.</span><span class="sxs-lookup"><span data-stu-id="25b56-198">Where the identifiers denote fields or properties.</span></span>

<span data-ttu-id="25b56-199">\***Descrivere le transizioni di stato null simili all'assegnazione definita** _</span><span class="sxs-lookup"><span data-stu-id="25b56-199">\***Describe null state transitions similar to definite assignment** _</span></span>

### <a name="null-state-for-expressions"></a><span data-ttu-id="25b56-200">Stato null per le espressioni</span><span class="sxs-lookup"><span data-stu-id="25b56-200">Null state for expressions</span></span>

<span data-ttu-id="25b56-201">Lo stato null di un'espressione deriva dal formato e dal tipo e dallo stato null delle variabili in esso incluse.</span><span class="sxs-lookup"><span data-stu-id="25b56-201">The null state of an expression is derived from its form and type, and from the null state of variables involved in it.</span></span>

### <a name="literals"></a><span data-ttu-id="25b56-202">Valori letterali</span><span class="sxs-lookup"><span data-stu-id="25b56-202">Literals</span></span>

<span data-ttu-id="25b56-203">Lo stato null di un `null` valore letterale è "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="25b56-203">The null state of a `null` literal is "maybe null".</span></span> <span data-ttu-id="25b56-204">Lo stato null di un `default` valore letterale che viene convertito in un tipo che non è un tipo di valore non null è "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="25b56-204">The null state of a `default` literal that is being converted to a type that is known not to be a nonnullable value type is "maybe null".</span></span> <span data-ttu-id="25b56-205">Lo stato null di qualsiasi altro valore letterale è "not null".</span><span class="sxs-lookup"><span data-stu-id="25b56-205">The null state of any other literal is "not null".</span></span>

### <a name="simple-names"></a><span data-ttu-id="25b56-206">Nomi semplici</span><span class="sxs-lookup"><span data-stu-id="25b56-206">Simple names</span></span>

<span data-ttu-id="25b56-207">Se un oggetto `simple_name` non è classificato come valore, il relativo stato null è "not null".</span><span class="sxs-lookup"><span data-stu-id="25b56-207">If a `simple_name` is not classified as a value, its null state is "not null".</span></span> <span data-ttu-id="25b56-208">In caso contrario, si tratta di un'espressione rilevata e il relativo stato null è il relativo stato null rilevato in questa posizione di origine.</span><span class="sxs-lookup"><span data-stu-id="25b56-208">Otherwise it is a tracked expression, and its null state is its tracked null state at this source location.</span></span>

### <a name="member-access"></a><span data-ttu-id="25b56-209">Accesso ai membri</span><span class="sxs-lookup"><span data-stu-id="25b56-209">Member access</span></span>

<span data-ttu-id="25b56-210">Se un oggetto `member_access` non è classificato come valore, il relativo stato null è "not null".</span><span class="sxs-lookup"><span data-stu-id="25b56-210">If a `member_access` is not classified as a value, its null state is "not null".</span></span> <span data-ttu-id="25b56-211">In caso contrario, se si tratta di un'espressione rilevata, il relativo stato null è il relativo stato null rilevato in questa posizione di origine.</span><span class="sxs-lookup"><span data-stu-id="25b56-211">Otherwise, if it is a tracked expression, its null state is its tracked null state at this source location.</span></span> <span data-ttu-id="25b56-212">In caso contrario, lo stato null è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="25b56-212">Otherwise its null state is the default null state of its type.</span></span>

### <a name="invocation-expressions"></a><span data-ttu-id="25b56-213">Espressioni di chiamata</span><span class="sxs-lookup"><span data-stu-id="25b56-213">Invocation expressions</span></span>

<span data-ttu-id="25b56-214">Se un oggetto `invocation_expression` richiama un membro dichiarato con uno o più attributi per un comportamento null speciale, lo stato null è determinato da tali attributi.</span><span class="sxs-lookup"><span data-stu-id="25b56-214">If an `invocation_expression` invokes a member that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="25b56-215">In caso contrario, lo stato null dell'espressione è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="25b56-215">Otherwise the null state of the expression is the default null state of its type.</span></span>

### <a name="element-access"></a><span data-ttu-id="25b56-216">Accesso a elementi</span><span class="sxs-lookup"><span data-stu-id="25b56-216">Element access</span></span>

<span data-ttu-id="25b56-217">Se un oggetto `element_access` richiama un indicizzatore dichiarato con uno o più attributi per un comportamento null speciale, lo stato null è determinato da tali attributi.</span><span class="sxs-lookup"><span data-stu-id="25b56-217">If an `element_access` invokes an indexer that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="25b56-218">In caso contrario, lo stato null dell'espressione è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="25b56-218">Otherwise the null state of the expression is the default null state of its type.</span></span>

### <a name="base-access"></a><span data-ttu-id="25b56-219">Accesso di base</span><span class="sxs-lookup"><span data-stu-id="25b56-219">Base access</span></span>

<span data-ttu-id="25b56-220">Se `B` denota il tipo di base del tipo di inclusione, `base.I` ha lo stesso stato null di `((B)this).I` e `base[E]` ha lo stesso stato null di `((B)this)[E]` .</span><span class="sxs-lookup"><span data-stu-id="25b56-220">If `B` denotes the base type of the enclosing type, `base.I` has the same null state as `((B)this).I` and `base[E]` has the same null state as `((B)this)[E]`.</span></span>

### <a name="default-expressions"></a><span data-ttu-id="25b56-221">Espressioni predefinite</span><span class="sxs-lookup"><span data-stu-id="25b56-221">Default expressions</span></span>

<span data-ttu-id="25b56-222">`default(T)` ha lo stato null "non null" Se `T` è noto come tipo di valore non null.</span><span class="sxs-lookup"><span data-stu-id="25b56-222">`default(T)` has the null state "non-null" if `T` is known to be a nonnullable value type.</span></span> <span data-ttu-id="25b56-223">In caso contrario, lo stato null è "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="25b56-223">Otherwise it has the null state "maybe null".</span></span>

### <a name="null-conditional-expressions"></a><span data-ttu-id="25b56-224">Espressioni condizionali null</span><span class="sxs-lookup"><span data-stu-id="25b56-224">Null-conditional expressions</span></span>

<span data-ttu-id="25b56-225">Uno `null_conditional_expression` ha lo stato null "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="25b56-225">A `null_conditional_expression` has the null state "maybe null".</span></span>

### <a name="cast-expressions"></a><span data-ttu-id="25b56-226">Espressioni cast</span><span class="sxs-lookup"><span data-stu-id="25b56-226">Cast expressions</span></span>

<span data-ttu-id="25b56-227">Se un'espressione cast `(T)E` richiama una conversione definita dall'utente, lo stato null dell'espressione sarà lo stato null predefinito per il relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="25b56-227">If a cast expression `(T)E` invokes a user-defined conversion, then the null state of the expression is the default null state for its type.</span></span> <span data-ttu-id="25b56-228">In caso contrario, se `T` restituisce null (_nullable \* o *Unknown* ), lo stato null è "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="25b56-228">Otherwise, if `T` is null-yielding (_nullable\* or *unknown* ) then the null state is "maybe null".</span></span> <span data-ttu-id="25b56-229">In caso contrario, lo stato null corrisponde allo stato null di `E` .</span><span class="sxs-lookup"><span data-stu-id="25b56-229">Otherwise the null state is the same as the null state of `E`.</span></span>

### <a name="await-expressions"></a><span data-ttu-id="25b56-230">Espressioni await</span><span class="sxs-lookup"><span data-stu-id="25b56-230">Await expressions</span></span>

<span data-ttu-id="25b56-231">Lo stato null di `await E` è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="25b56-231">The null state of `await E` is the default null state of its type.</span></span>

### <a name="the-as-operator"></a><span data-ttu-id="25b56-232">Operatore `as`</span><span class="sxs-lookup"><span data-stu-id="25b56-232">The `as` operator</span></span>

<span data-ttu-id="25b56-233">Un' `as` espressione ha lo stato null "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="25b56-233">An `as` expression has the null state "maybe null".</span></span>

### <a name="the-null-coalescing-operator"></a><span data-ttu-id="25b56-234">Operatore di Unione null</span><span class="sxs-lookup"><span data-stu-id="25b56-234">The null-coalescing operator</span></span>

<span data-ttu-id="25b56-235">`E1 ?? E2` ha lo stesso stato null di `E2`</span><span class="sxs-lookup"><span data-stu-id="25b56-235">`E1 ?? E2` has the same null state as `E2`</span></span>

### <a name="the-conditional-operator"></a><span data-ttu-id="25b56-236">Operatore condizionale</span><span class="sxs-lookup"><span data-stu-id="25b56-236">The conditional operator</span></span>

<span data-ttu-id="25b56-237">Lo stato null di `E1 ? E2 : E3` è "not null" se lo stato null di `E2` e è `E3` "not null".</span><span class="sxs-lookup"><span data-stu-id="25b56-237">The null state of `E1 ? E2 : E3` is "not null" if the null state of both `E2` and `E3` are "not null".</span></span> <span data-ttu-id="25b56-238">In caso contrario, è "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="25b56-238">Otherwise it is "maybe null".</span></span>

### <a name="query-expressions"></a><span data-ttu-id="25b56-239">Espressioni di query</span><span class="sxs-lookup"><span data-stu-id="25b56-239">Query expressions</span></span>

<span data-ttu-id="25b56-240">Lo stato null di un'espressione di query è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="25b56-240">The null state of a query expression is the default null state of its type.</span></span>

### <a name="assignment-operators"></a><span data-ttu-id="25b56-241">Operatori di assegnazione</span><span class="sxs-lookup"><span data-stu-id="25b56-241">Assignment operators</span></span>

<span data-ttu-id="25b56-242">`E1 = E2` e `E1 op= E2` hanno lo stesso stato null di `E2` dopo l'applicazione di tutte le conversioni implicite.</span><span class="sxs-lookup"><span data-stu-id="25b56-242">`E1 = E2` and `E1 op= E2` have the same null state as `E2` after any implicit conversions have been applied.</span></span>

### <a name="unary-and-binary-operators"></a><span data-ttu-id="25b56-243">Operatori unari e binari</span><span class="sxs-lookup"><span data-stu-id="25b56-243">Unary and binary operators</span></span>

<span data-ttu-id="25b56-244">Se un operatore unario o binario richiama un operatore definito dall'utente dichiarato con uno o più attributi per un comportamento null speciale, lo stato null è determinato da tali attributi.</span><span class="sxs-lookup"><span data-stu-id="25b56-244">If a unary or binary operator invokes an user-defined operator that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="25b56-245">In caso contrario, lo stato null dell'espressione è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="25b56-245">Otherwise the null state of the expression is the default null state of its type.</span></span>

<span data-ttu-id="25b56-246">\*Si tratta **di un'operazione speciale per binari `+` su stringhe e delegati?** _</span><span class="sxs-lookup"><span data-stu-id="25b56-246">\***Something special to do for binary `+` over strings and delegates?** _</span></span>

### <a name="expressions-that-propagate-null-state"></a><span data-ttu-id="25b56-247">Espressioni che propagano lo stato null</span><span class="sxs-lookup"><span data-stu-id="25b56-247">Expressions that propagate null state</span></span>

<span data-ttu-id="25b56-248">`(E)`, `checked(E)` e `unchecked(E)` hanno tutti lo stesso stato null di `E` .</span><span class="sxs-lookup"><span data-stu-id="25b56-248">`(E)`, `checked(E)` and `unchecked(E)` all have the same null state as `E`.</span></span>

### <a name="expressions-that-are-never-null"></a><span data-ttu-id="25b56-249">Espressioni che non sono mai null</span><span class="sxs-lookup"><span data-stu-id="25b56-249">Expressions that are never null</span></span>

<span data-ttu-id="25b56-250">Lo stato null dei seguenti form di espressione è sempre "not null":</span><span class="sxs-lookup"><span data-stu-id="25b56-250">The null state of the following expression forms is always "not null":</span></span>

- <span data-ttu-id="25b56-251">`this` accesso</span><span class="sxs-lookup"><span data-stu-id="25b56-251">`this` access</span></span>
- <span data-ttu-id="25b56-252">stringhe interpolate</span><span class="sxs-lookup"><span data-stu-id="25b56-252">interpolated strings</span></span>
- <span data-ttu-id="25b56-253">`new` espressioni (oggetto, delegato, oggetto anonimo e espressioni di creazione di matrici)</span><span class="sxs-lookup"><span data-stu-id="25b56-253">`new` expressions (object, delegate, anonymous object and array creation expressions)</span></span>
- <span data-ttu-id="25b56-254">Espressioni `typeof`</span><span class="sxs-lookup"><span data-stu-id="25b56-254">`typeof` expressions</span></span>
- <span data-ttu-id="25b56-255">Espressioni `nameof`</span><span class="sxs-lookup"><span data-stu-id="25b56-255">`nameof` expressions</span></span>
- <span data-ttu-id="25b56-256">funzioni anonime (metodi anonimi ed espressioni lambda)</span><span class="sxs-lookup"><span data-stu-id="25b56-256">anonymous functions (anonymous methods and lambda expressions)</span></span>
- <span data-ttu-id="25b56-257">espressioni con indulgenza null</span><span class="sxs-lookup"><span data-stu-id="25b56-257">null-forgiving expressions</span></span>
- <span data-ttu-id="25b56-258">Espressioni `is`</span><span class="sxs-lookup"><span data-stu-id="25b56-258">`is` expressions</span></span>

## <a name="type-inference"></a><span data-ttu-id="25b56-259">Inferenza del tipo</span><span class="sxs-lookup"><span data-stu-id="25b56-259">Type inference</span></span>

### <a name="type-inference-for-var"></a><span data-ttu-id="25b56-260">Inferenza del tipo per `var`</span><span class="sxs-lookup"><span data-stu-id="25b56-260">Type inference for `var`</span></span>

<span data-ttu-id="25b56-261">Il tipo dedotto per le variabili locali dichiarate con `var` viene informato dallo stato null dell'espressione di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="25b56-261">The type inferred for local variables declared with `var` is informed by the null state of the initializing expression.</span></span>

```csharp
var x = E;
```

<span data-ttu-id="25b56-262">Se il tipo di `E` è un tipo di riferimento Nullable `C?` e lo stato null di `E` è "not null", il tipo dedotto per `x` è `C` .</span><span class="sxs-lookup"><span data-stu-id="25b56-262">If the type of `E` is a nullable reference type `C?` and the null state of `E` is "not null" then the type inferred for `x` is `C`.</span></span> <span data-ttu-id="25b56-263">In caso contrario, il tipo dedotto è il tipo di `E` .</span><span class="sxs-lookup"><span data-stu-id="25b56-263">Otherwise, the inferred type is the type of `E`.</span></span>

<span data-ttu-id="25b56-264">Il supporto di valori null del tipo dedotto per `x` è determinato come descritto sopra, in base al contesto di annotazione di `var` , come se il tipo fosse stato specificato in modo esplicito in tale posizione.</span><span class="sxs-lookup"><span data-stu-id="25b56-264">The nullability of the type inferred for `x` is determined as described above, based on the annotation context of the `var`, just as if the type had been given explicitly in that position.</span></span>

### <a name="type-inference-for-var"></a><span data-ttu-id="25b56-265">Inferenza del tipo per `var?`</span><span class="sxs-lookup"><span data-stu-id="25b56-265">Type inference for `var?`</span></span>

<span data-ttu-id="25b56-266">Il tipo dedotto per le variabili locali dichiarate con `var?` è indipendente dallo stato null dell'espressione di inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="25b56-266">The type inferred for local variables declared with `var?` is independent of the null state of the initializing expression.</span></span>

```csharp
var? x = E;
```

<span data-ttu-id="25b56-267">Se il tipo `T` di `E` è un tipo di valore nullable o un tipo di riferimento Nullable, il tipo dedotto per `x` è `T` .</span><span class="sxs-lookup"><span data-stu-id="25b56-267">If the type `T` of `E` is a nullable value type or a nullable reference type then the type inferred for `x` is `T`.</span></span> <span data-ttu-id="25b56-268">In caso contrario, se `T` è un tipo di valore che non ammette valori null, `S` il tipo dedotto è `S?` .</span><span class="sxs-lookup"><span data-stu-id="25b56-268">Otherwise, if `T` is a nonnullable value type `S` the type inferred is `S?`.</span></span> <span data-ttu-id="25b56-269">In caso contrario, se `T` è un tipo di riferimento che non ammette i valori null, `C` il tipo dedotto è `C?` .</span><span class="sxs-lookup"><span data-stu-id="25b56-269">Otherwise, if `T` is a nonnullable reference type `C` the type inferred is `C?`.</span></span> <span data-ttu-id="25b56-270">In caso contrario, la dichiarazione non è valida.</span><span class="sxs-lookup"><span data-stu-id="25b56-270">Otherwise, the declaration is illegal.</span></span>

<span data-ttu-id="25b56-271">Il supporto di valori null del tipo dedotto per `x` è sempre _nullable \*.</span><span class="sxs-lookup"><span data-stu-id="25b56-271">The nullability of the type inferred for `x` is always _nullable\*.</span></span>

### <a name="generic-type-inference"></a><span data-ttu-id="25b56-272">Inferenza del tipo generico</span><span class="sxs-lookup"><span data-stu-id="25b56-272">Generic type inference</span></span>

<span data-ttu-id="25b56-273">L'inferenza del tipo generico è stata migliorata per determinare se i tipi di riferimento derivati devono essere nullable o meno.</span><span class="sxs-lookup"><span data-stu-id="25b56-273">Generic type inference is enhanced to help decide whether inferred reference types should be nullable or not.</span></span> <span data-ttu-id="25b56-274">Si tratta di un tentativo ottimale, che non è in grado di generare avvisi, ma può causare avvisi Nullable quando i tipi dedotti dell'overload selezionato vengono applicati agli argomenti.</span><span class="sxs-lookup"><span data-stu-id="25b56-274">This is a best effort, and does not in and of itself yield warnings, but may lead to nullable warnings when the inferred types of the selected overload are applied to the arguments.</span></span>

<span data-ttu-id="25b56-275">L'inferenza del tipo non si basa sul contesto di annotazione dei tipi in ingresso.</span><span class="sxs-lookup"><span data-stu-id="25b56-275">The type inference does not rely on the annotation context of incoming types.</span></span> <span data-ttu-id="25b56-276">Viene invece `type` dedotto un oggetto che acquisisce il proprio contesto di annotazione da dove "sarebbe stato" se fosse stato espresso in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="25b56-276">Instead a `type` is inferred which acquires its own annotation context from where it "would have been" if it had been expressed explicitly.</span></span> <span data-ttu-id="25b56-277">In questo modo viene sottolineato il ruolo di inferenza del tipo come praticità per gli elementi che potrebbero essere stati scritti.</span><span class="sxs-lookup"><span data-stu-id="25b56-277">This underscores the role of type inference as a convenience for what you could have written yourself.</span></span>

<span data-ttu-id="25b56-278">Più precisamente, il contesto dell'annotazione per un argomento di tipo derivato è il contesto del token che sarebbe stato seguito dall' `<...>` elenco dei parametri di tipo, ne era presente uno, ovvero il nome del metodo generico chiamato.</span><span class="sxs-lookup"><span data-stu-id="25b56-278">More precisely, the annotation context for an inferred type argument is the context of the token that would have been followed by the `<...>` type parameter list, had there been one; i.e. the name of the generic method being called.</span></span> <span data-ttu-id="25b56-279">Per le espressioni di query che vengono convertite in chiamate di questo tipo, il contesto viene tratto dalla parola chiave contestuale iniziale della clausola di query da cui viene generata la chiamata.</span><span class="sxs-lookup"><span data-stu-id="25b56-279">For query expressions that translate to such calls, the context is taken from the initial contextual keyword of the query clause from which the call is generated.</span></span>

### <a name="the-first-phase"></a><span data-ttu-id="25b56-280">Prima fase</span><span class="sxs-lookup"><span data-stu-id="25b56-280">The first phase</span></span>

<span data-ttu-id="25b56-281">I tipi di riferimento Nullable scorrono nei limiti delle espressioni iniziali, come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="25b56-281">Nullable reference types flow into the bounds from the initial expressions, as described below.</span></span> <span data-ttu-id="25b56-282">Inoltre, `null` vengono introdotti due nuovi tipi di limiti, ovvero e `default` .</span><span class="sxs-lookup"><span data-stu-id="25b56-282">In addition, two new kinds of bounds, namely `null` and `default` are introduced.</span></span> <span data-ttu-id="25b56-283">Il loro scopo è quello di eseguire le occorrenze di `null` o `default` nelle espressioni di input, che possono causare il Nullable di un tipo derivato, anche in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="25b56-283">Their purpose is to carry through occurrences of `null` or `default` in the input expressions, which may cause an inferred type to be nullable, even when it otherwise wouldn't.</span></span> <span data-ttu-id="25b56-284">Funziona anche per i tipi di *valore* Nullable, che sono stati migliorati per prelevare il "valore null" nel processo di inferenza.</span><span class="sxs-lookup"><span data-stu-id="25b56-284">This works even for nullable *value* types, which are enhanced to pick up "nullness" in the inference process.</span></span>

<span data-ttu-id="25b56-285">La determinazione dei limiti da aggiungere nella prima fase viene migliorata nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="25b56-285">The determination of what bounds to add in the first phase are enhanced as follows:</span></span>

<span data-ttu-id="25b56-286">Se un argomento `Ei` ha un tipo di riferimento, il tipo `U` usato per l'inferenza dipende dallo stato null di e dal `Ei` tipo dichiarato:</span><span class="sxs-lookup"><span data-stu-id="25b56-286">If an argument `Ei` has a reference type, the type `U` used for inference depends on the null state of `Ei` as well as its declared type:</span></span>
- <span data-ttu-id="25b56-287">Se il tipo dichiarato è un tipo di riferimento non null `U0` o un tipo di riferimento Nullable, `U0?`</span><span class="sxs-lookup"><span data-stu-id="25b56-287">If the declared type is a nonnullable reference type `U0` or a nullable reference type `U0?` then</span></span>
    - <span data-ttu-id="25b56-288">Se lo stato null di `Ei` è "not null", `U` sarà `U0`</span><span class="sxs-lookup"><span data-stu-id="25b56-288">if the null state of `Ei` is "not null" then `U` is `U0`</span></span>
    - <span data-ttu-id="25b56-289">Se lo stato null di `Ei` è "Maybe null", `U` sarà `U0?`</span><span class="sxs-lookup"><span data-stu-id="25b56-289">if the null state of `Ei` is "maybe null" then `U` is `U0?`</span></span>
- <span data-ttu-id="25b56-290">In caso contrario `Ei` , se ha un tipo dichiarato, `U` è quel tipo</span><span class="sxs-lookup"><span data-stu-id="25b56-290">Otherwise if `Ei` has a declared type, `U` is that type</span></span>
- <span data-ttu-id="25b56-291">In caso contrario, se `Ei` è `null` quindi `U` il limite speciale `null`</span><span class="sxs-lookup"><span data-stu-id="25b56-291">Otherwise if `Ei` is `null` then `U` is the special bound `null`</span></span>
- <span data-ttu-id="25b56-292">In caso contrario, se `Ei` è `default` quindi `U` il limite speciale `default`</span><span class="sxs-lookup"><span data-stu-id="25b56-292">Otherwise if `Ei` is `default` then `U` is the special bound `default`</span></span>
- <span data-ttu-id="25b56-293">In caso contrario, non viene eseguita alcuna inferenza.</span><span class="sxs-lookup"><span data-stu-id="25b56-293">Otherwise no inference is made.</span></span>

### <a name="exact-upper-bound-and-lower-bound-inferences"></a><span data-ttu-id="25b56-294">Inferenze esatte, con associazione superiore e con associazione inferiore</span><span class="sxs-lookup"><span data-stu-id="25b56-294">Exact, upper-bound and lower-bound inferences</span></span>

<span data-ttu-id="25b56-295">In inferenza *dal* tipo `U` *al* tipo `V` , se `V` è un tipo di riferimento Nullable `V0?` , `V0` viene usato anziché `V` nelle clausole seguenti.</span><span class="sxs-lookup"><span data-stu-id="25b56-295">In inferences *from* the type `U` *to* the type `V`, if `V` is a nullable reference type `V0?`, then `V0` is used instead of `V` in the following clauses.</span></span>
- <span data-ttu-id="25b56-296">Se `V` è una delle variabili di tipo non fixed, `U` viene aggiunto come limite esatto, superiore o inferiore come prima</span><span class="sxs-lookup"><span data-stu-id="25b56-296">If `V` is one of the unfixed type variables, `U` is added as an exact, upper or lower bound as before</span></span>
- <span data-ttu-id="25b56-297">In caso contrario, se `U` è `null` o `default` , non viene eseguita alcuna inferenza</span><span class="sxs-lookup"><span data-stu-id="25b56-297">Otherwise, if `U` is `null` or `default`, no inference is made</span></span>
- <span data-ttu-id="25b56-298">In caso contrario, se `U` è un tipo `U0?` di riferimento Nullable, `U0` viene utilizzato anziché `U` nelle clausole successive.</span><span class="sxs-lookup"><span data-stu-id="25b56-298">Otherwise, if `U` is a nullable reference type `U0?`, then `U0` is used instead of `U` in the subsequent clauses.</span></span>

<span data-ttu-id="25b56-299">L'essenza è che i valori null che riguardano direttamente una delle variabili di tipo non fisso vengono conservati nei limiti.</span><span class="sxs-lookup"><span data-stu-id="25b56-299">The essence is that nullability that pertains directly to one of the unfixed type variables is preserved into its bounds.</span></span> <span data-ttu-id="25b56-300">Per gli inferenza che rimaledicono ulteriormente i tipi di origine e di destinazione, d'altra parte, il supporto dei valori null viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="25b56-300">For the inferences that recurse further into the source and target types, on the other hand, nullability is ignored.</span></span> <span data-ttu-id="25b56-301">Può corrispondere o meno, ma in caso contrario, verrà emesso un avviso in un secondo momento se viene scelto e applicato l'overload.</span><span class="sxs-lookup"><span data-stu-id="25b56-301">It may or may not match, but if it doesn't, a warning will be issued later if the overload is chosen and applied.</span></span>

### <a name="fixing"></a><span data-ttu-id="25b56-302">correzione di </span><span class="sxs-lookup"><span data-stu-id="25b56-302">Fixing</span></span>

<span data-ttu-id="25b56-303">La specifica attualmente non esegue un processo valido per descrivere cosa accade quando più limiti sono convertibili tra loro identità, ma sono diversi.</span><span class="sxs-lookup"><span data-stu-id="25b56-303">The spec currently does not do a good job of describing what happens when multiple bounds are identity convertible to each other, but are different.</span></span> <span data-ttu-id="25b56-304">Questo problema può verificarsi tra `object` e `dynamic` , tra i tipi di tupla che differiscono solo per i nomi di elementi, tra i tipi costruiti e ora anche tra `C` e `C?` per i tipi di riferimento.</span><span class="sxs-lookup"><span data-stu-id="25b56-304">This may happen between `object` and `dynamic`, between tuple types that differ only in element names, between types constructed thereof and now also between `C` and `C?` for reference types.</span></span>

<span data-ttu-id="25b56-305">È inoltre necessario propagare "null" dalle espressioni di input al tipo di risultato.</span><span class="sxs-lookup"><span data-stu-id="25b56-305">In addition we need to propagate "nullness" from the input expressions to the result type.</span></span> 

<span data-ttu-id="25b56-306">Per gestire queste operazioni, è necessario aggiungere altre fasi per la correzione, che ora è:</span><span class="sxs-lookup"><span data-stu-id="25b56-306">To handle these we add more phases to fixing, which is now:</span></span>

1. <span data-ttu-id="25b56-307">Raccogliere tutti i tipi in tutti i limiti come candidati, rimuovendo `?` da tutti i tipi di riferimento Nullable</span><span class="sxs-lookup"><span data-stu-id="25b56-307">Gather all the types in all the bounds as candidates, removing `?` from all that are nullable reference types</span></span>
2. <span data-ttu-id="25b56-308">Elimina i candidati in base ai requisiti dei limiti esatti, inferiore e superiore (mantenimento `null` e `default` limiti)</span><span class="sxs-lookup"><span data-stu-id="25b56-308">Eliminate candidates based on requirements of exact, lower and upper bounds (keeping `null` and `default` bounds)</span></span>
3. <span data-ttu-id="25b56-309">Elimina i candidati che non dispongono di una conversione implicita a tutti gli altri candidati</span><span class="sxs-lookup"><span data-stu-id="25b56-309">Eliminate candidates that do not have an implicit conversion to all the other candidates</span></span>
4. <span data-ttu-id="25b56-310">Se i candidati rimanenti non hanno tutte le conversioni di identità tra loro, l'inferenza del tipo ha esito negativo</span><span class="sxs-lookup"><span data-stu-id="25b56-310">If the remaining candidates do not all have identity conversions to one another, then type inference fails</span></span>
5. <span data-ttu-id="25b56-311">*Unisci* i candidati rimanenti come descritto di seguito</span><span class="sxs-lookup"><span data-stu-id="25b56-311">*Merge* the remaining candidates as described below</span></span>
6. <span data-ttu-id="25b56-312">Se il candidato risultante è un tipo di riferimento o un tipo di valore non null e *tutti* i limiti esatti o *uno* dei limiti inferiori sono tipi di valore Nullable, tipi di riferimento Nullable `null` o `default` , `?` viene aggiunto al candidato risultante, rendendolo un tipo di valore o un tipo di riferimento Nullable.</span><span class="sxs-lookup"><span data-stu-id="25b56-312">If the resulting candidate is a reference type or a nonnullable value type and *all* of the exact bounds or *any* of the lower bounds are nullable value types, nullable reference types, `null` or `default`, then `?` is added to the resulting candidate, making it a nullable value type or reference type.</span></span>

<span data-ttu-id="25b56-313">L' *Unione* viene descritta tra due tipi candidati.</span><span class="sxs-lookup"><span data-stu-id="25b56-313">*Merging* is described between two candidate types.</span></span> <span data-ttu-id="25b56-314">Si tratta di transitive e commutative, quindi i candidati possono essere Uniti in qualsiasi ordine con lo stesso risultato finale.</span><span class="sxs-lookup"><span data-stu-id="25b56-314">It is transitive and commutative, so the candidates can be merged in any order with the same ultimate result.</span></span> <span data-ttu-id="25b56-315">Non è definito se i due tipi candidati non sono convertibili in identità.</span><span class="sxs-lookup"><span data-stu-id="25b56-315">It is undefined if the two candidate types are not identity convertible to each other.</span></span>

<span data-ttu-id="25b56-316">La funzione *merge* accetta due tipi candidati e una direzione ( *+* o *-* ):</span><span class="sxs-lookup"><span data-stu-id="25b56-316">The *Merge* function takes two candidate types and a direction ( *+* or *-* ):</span></span>

- <span data-ttu-id="25b56-317">*Unisci* ( `T` , `T` , *d* ) = T</span><span class="sxs-lookup"><span data-stu-id="25b56-317">*Merge* (`T`, `T`, *d* ) = T</span></span>
- <span data-ttu-id="25b56-318">*Merge* ( `S` , `T?` , *+* ) = *merge* ( `S?` , `T` , *+* ) = *merge* ( `S` , `T` , *+* )`?`</span><span class="sxs-lookup"><span data-stu-id="25b56-318">*Merge* (`S`, `T?`, *+* ) = *Merge* (`S?`, `T`, *+* ) = *Merge* (`S`, `T`, *+* )`?`</span></span>
- <span data-ttu-id="25b56-319">*Merge* ( `S` , `T?` , *-* ) = *merge* ( `S?` , `T` , *-* ) = *merge* ( `S` , `T` , *-* )</span><span class="sxs-lookup"><span data-stu-id="25b56-319">*Merge* (`S`, `T?`, *-* ) = *Merge* (`S?`, `T`, *-* ) = *Merge* (`S`, `T`, *-* )</span></span>
- <span data-ttu-id="25b56-320">*Merge* ( `C<S1,...,Sn>` , `C<T1,...,Tn>` , *+* ) = `C<` *merge* ( `S1` , `T1` , *D1* ) `,...,` *merge* ( `Sn` , `Tn` , *DN* ) `>` , *dove*</span><span class="sxs-lookup"><span data-stu-id="25b56-320">*Merge* (`C<S1,...,Sn>`, `C<T1,...,Tn>`, *+* ) = `C<`*Merge* (`S1`, `T1`, *d1* )`,...,`*Merge* (`Sn`, `Tn`, *dn* )`>`, *where*</span></span>
    - <span data-ttu-id="25b56-321">`di` = *+* Se il `i` parametro di tipo ' th di `C<...>` è covariante</span><span class="sxs-lookup"><span data-stu-id="25b56-321">`di` = *+* if the `i`'th type parameter of `C<...>` is covariant</span></span>
    - <span data-ttu-id="25b56-322">`di` = *-* Se il `i` parametro di tipo ' th di `C<...>` è contra o invariante</span><span class="sxs-lookup"><span data-stu-id="25b56-322">`di` = *-* if the `i`'th type parameter of `C<...>` is contra- or invariant</span></span>
- <span data-ttu-id="25b56-323">*Merge* ( `C<S1,...,Sn>` , `C<T1,...,Tn>` , *-* ) = `C<` *merge* ( `S1` , `T1` , *D1* ) `,...,` *merge* ( `Sn` , `Tn` , *DN* ) `>` , *dove*</span><span class="sxs-lookup"><span data-stu-id="25b56-323">*Merge* (`C<S1,...,Sn>`, `C<T1,...,Tn>`, *-* ) = `C<`*Merge* (`S1`, `T1`, *d1* )`,...,`*Merge* (`Sn`, `Tn`, *dn* )`>`, *where*</span></span>
    - <span data-ttu-id="25b56-324">`di` = *-* Se il `i` parametro di tipo ' th di `C<...>` è covariante</span><span class="sxs-lookup"><span data-stu-id="25b56-324">`di` = *-* if the `i`'th type parameter of `C<...>` is covariant</span></span>
    - <span data-ttu-id="25b56-325">`di` = *+* Se il `i` parametro di tipo ' th di `C<...>` è contra o invariante</span><span class="sxs-lookup"><span data-stu-id="25b56-325">`di` = *+* if the `i`'th type parameter of `C<...>` is contra- or invariant</span></span>
- <span data-ttu-id="25b56-326">*Merge* ( `(S1 s1,..., Sn sn)` , `(T1 t1,..., Tn tn)` , *d* ) = `(` *merge* ( `S1` , `T1` , *d* ) `n1,...,` *merge* ( `Sn` , `Tn` , *d* ) `nn)` , *dove*</span><span class="sxs-lookup"><span data-stu-id="25b56-326">*Merge* (`(S1 s1,..., Sn sn)`, `(T1 t1,..., Tn tn)`, *d* ) = `(`*Merge* (`S1`, `T1`, *d* )`n1,...,`*Merge* (`Sn`, `Tn`, *d* ) `nn)`, *where*</span></span>
    - <span data-ttu-id="25b56-327">`ni` è assente se `si` e `ti` differiscono o se entrambi sono assenti</span><span class="sxs-lookup"><span data-stu-id="25b56-327">`ni` is absent if `si` and `ti` differ, or if both are absent</span></span>
    - <span data-ttu-id="25b56-328">`ni``si`se `si` e `ti` sono uguali</span><span class="sxs-lookup"><span data-stu-id="25b56-328">`ni` is `si` if `si` and `ti` are the same</span></span>
- <span data-ttu-id="25b56-329">*Merge* ( `object` , `dynamic` ) = *merge* ( `dynamic` , `object` ) = `dynamic`</span><span class="sxs-lookup"><span data-stu-id="25b56-329">*Merge* (`object`, `dynamic`) = *Merge* (`dynamic`, `object`) = `dynamic`</span></span>

## <a name="warnings"></a><span data-ttu-id="25b56-330">Avvisi</span><span class="sxs-lookup"><span data-stu-id="25b56-330">Warnings</span></span>

### <a name="potential-null-assignment"></a><span data-ttu-id="25b56-331">Potenziale assegnazione null</span><span class="sxs-lookup"><span data-stu-id="25b56-331">Potential null assignment</span></span>

### <a name="potential-null-dereference"></a><span data-ttu-id="25b56-332">Potenziale dereferenziazione null</span><span class="sxs-lookup"><span data-stu-id="25b56-332">Potential null dereference</span></span>

### <a name="constraint-nullability-mismatch"></a><span data-ttu-id="25b56-333">Mancata corrispondenza dei valori null per i vincoli</span><span class="sxs-lookup"><span data-stu-id="25b56-333">Constraint nullability mismatch</span></span>

### <a name="nullable-types-in-disabled-annotation-context"></a><span data-ttu-id="25b56-334">Tipi nullable nel contesto di annotazione disabilitato</span><span class="sxs-lookup"><span data-stu-id="25b56-334">Nullable types in disabled annotation context</span></span>

## <a name="attributes-for-special-null-behavior"></a><span data-ttu-id="25b56-335">Attributi per un comportamento null speciale</span><span class="sxs-lookup"><span data-stu-id="25b56-335">Attributes for special null behavior</span></span>


