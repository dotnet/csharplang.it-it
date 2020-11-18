---
ms.openlocfilehash: a11b695d8345e521d3d781ed59cd730d83f87fd0
ms.sourcegitcommit: 3d2315ca498ffe0e87848306ca08d0f5b265890d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94717793"
---
# <a name="nullable-reference-types-specification"></a><span data-ttu-id="5414e-101">Specifica di tipi di riferimento Nullable</span><span class="sxs-lookup"><span data-stu-id="5414e-101">Nullable Reference Types Specification</span></span>

<span data-ttu-id="5414e-102">\***Questo è un lavoro in corso. diverse parti sono mancanti o incomplete.** _</span><span class="sxs-lookup"><span data-stu-id="5414e-102">\***This is a work in progress - several parts are missing or incomplete.** _</span></span>

<span data-ttu-id="5414e-103">Questa funzionalità aggiunge due nuovi tipi di tipi Nullable (tipi di riferimento nullable e tipi generici Nullable) ai tipi di valore Nullable esistenti e introduce un'analisi del flusso statica per finalità di sicurezza dei valori null.</span><span class="sxs-lookup"><span data-stu-id="5414e-103">This feature adds two new kinds of nullable types (nullable reference types and nullable generic types) to the existing nullable value types, and introduces a static flow analysis for purpose of null-safety.</span></span>

## <a name="syntax"></a><span data-ttu-id="5414e-104">Sintassi</span><span class="sxs-lookup"><span data-stu-id="5414e-104">Syntax</span></span>

### <a name="nullable-reference-types-and-nullable-type-parameters"></a><span data-ttu-id="5414e-105">Tipi di riferimento nullable e parametri di tipo Nullable</span><span class="sxs-lookup"><span data-stu-id="5414e-105">Nullable reference types and nullable type parameters</span></span>

<span data-ttu-id="5414e-106">I tipi di riferimento nullable e i parametri di tipo Nullable hanno la stessa sintassi della `T?` forma abbreviata di tipi di valore Nullable, ma non hanno una forma estesa corrispondente.</span><span class="sxs-lookup"><span data-stu-id="5414e-106">Nullable reference types and nullable type parameters have the same syntax `T?` as the short form of nullable value types, but do not have a corresponding long form.</span></span>

<span data-ttu-id="5414e-107">Ai fini della specifica, la `nullable_type` produzione corrente viene rinominata in e le `nullable_value_type` `nullable_reference_type` `nullable_type_parameter` produzioni e vengono aggiunte:</span><span class="sxs-lookup"><span data-stu-id="5414e-107">For the purposes of the specification, the current `nullable_type` production is renamed to `nullable_value_type`, and `nullable_reference_type` and `nullable_type_parameter` productions are added:</span></span>

```antlr
type
    : value_type
    | reference_type
    | nullable_type_parameter
    | type_parameter
    | type_unsafe
    ;

reference_type
    : ...
    | nullable_reference_type
    ;

nullable_reference_type
    : non_nullable_reference_type '?'
    ;

non_nullable_reference_type
    : reference_type
    ;

nullable_type_parameter
    : non_nullable_non_value_type_parameter '?'
    ;

non_nullable_non_value_type_parameter
    : type_parameter
    ;
```

<span data-ttu-id="5414e-108">Il `non_nullable_reference_type` valore di in `nullable_reference_type` deve essere un tipo di riferimento non Nullable (classe, interfaccia, delegato o matrice).</span><span class="sxs-lookup"><span data-stu-id="5414e-108">The `non_nullable_reference_type` in a `nullable_reference_type` must be a nonnullable reference type (class, interface, delegate or array).</span></span>

<span data-ttu-id="5414e-109">`non_nullable_non_value_type_parameter`In `nullable_type_parameter` deve essere un parametro di tipo che non è vincolato a un tipo valore.</span><span class="sxs-lookup"><span data-stu-id="5414e-109">The `non_nullable_non_value_type_parameter` in `nullable_type_parameter` must be a type parameter that isn't constrained to be a value type.</span></span>

<span data-ttu-id="5414e-110">I tipi di riferimento nullable e i parametri di tipo nullable non possono essere presenti nelle posizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="5414e-110">Nullable reference types and nullable type parameters cannot occur in the following positions:</span></span>

- <span data-ttu-id="5414e-111">come classe o interfaccia di base</span><span class="sxs-lookup"><span data-stu-id="5414e-111">as a base class or interface</span></span>
- <span data-ttu-id="5414e-112">come ricevitore di un `member_access`</span><span class="sxs-lookup"><span data-stu-id="5414e-112">as the receiver of a `member_access`</span></span>
- <span data-ttu-id="5414e-113">come `type` in un `object_creation_expression`</span><span class="sxs-lookup"><span data-stu-id="5414e-113">as the `type` in an `object_creation_expression`</span></span>
- <span data-ttu-id="5414e-114">come `delegate_type` in un `delegate_creation_expression`</span><span class="sxs-lookup"><span data-stu-id="5414e-114">as the `delegate_type` in a `delegate_creation_expression`</span></span>
- <span data-ttu-id="5414e-115">come `type` in un oggetto `is_expression` , un oggetto `catch_clause` o un oggetto `type_pattern`</span><span class="sxs-lookup"><span data-stu-id="5414e-115">as the `type` in an `is_expression`, a `catch_clause` or a `type_pattern`</span></span>
- <span data-ttu-id="5414e-116">come `interface` in un nome di membro di interfaccia completo</span><span class="sxs-lookup"><span data-stu-id="5414e-116">as the `interface` in a fully qualified interface member name</span></span>

<span data-ttu-id="5414e-117">Viene fornito un avviso in un oggetto `nullable_reference_type` e `nullable_type_parameter` in un contesto di annotazione _disabled \* Nullable.</span><span class="sxs-lookup"><span data-stu-id="5414e-117">A warning is given on a `nullable_reference_type` and `nullable_type_parameter` in a _disabled\* nullable annotation context.</span></span>

### <a name="class-and-class-constraint"></a><span data-ttu-id="5414e-118">`class` e `class?` vincolo</span><span class="sxs-lookup"><span data-stu-id="5414e-118">`class` and `class?` constraint</span></span>

<span data-ttu-id="5414e-119">Il `class` vincolo presenta una controparte Nullable `class?` :</span><span class="sxs-lookup"><span data-stu-id="5414e-119">The `class` constraint has a nullable counterpart `class?`:</span></span>

```antlr
primary_constraint
    : ...
    | 'class' '?'
    ;
```

<span data-ttu-id="5414e-120">È necessario creare un'istanza di un parametro di tipo vincolato a `class` (in un contesto di annotazione *abilitato* ) con un tipo di riferimento non null.</span><span class="sxs-lookup"><span data-stu-id="5414e-120">A type parameter constrained with `class` (in an *enabled* annotation context) must be instantiated with a nonnullable reference type.</span></span>

<span data-ttu-id="5414e-121">È possibile creare un'istanza di un parametro di tipo vincolato da `class?` (o `class` in un contesto di annotazione *disabilitato* ) con un tipo di riferimento nullable o non null.</span><span class="sxs-lookup"><span data-stu-id="5414e-121">A type parameter constrained with `class?` (or `class` in a *disabled* annotation context) may either be instantiated with a nullable or nonnullable reference type.</span></span>

<span data-ttu-id="5414e-122">Un avviso viene specificato in un `class?` vincolo in un contesto di annotazione *disabilitato* .</span><span class="sxs-lookup"><span data-stu-id="5414e-122">A warning is given on a `class?` constraint in a *disabled* annotation context.</span></span>

### <a name="notnull-constraint"></a><span data-ttu-id="5414e-123">`notnull` vincolo</span><span class="sxs-lookup"><span data-stu-id="5414e-123">`notnull` constraint</span></span>

<span data-ttu-id="5414e-124">Un parametro di tipo vincolato a `notnull` non può essere un tipo Nullable (tipo di valore Nullable, tipo di riferimento nullable o parametro di tipo Nullable).</span><span class="sxs-lookup"><span data-stu-id="5414e-124">A type parameter constrained with `notnull` may not be a nullable type (nullable value type, nullable reference type or nullable type parameter).</span></span>

```antlr
primary_constraint
    : ...
    | 'notnull'
    ;
```

### <a name="default-constraint"></a><span data-ttu-id="5414e-125">`default` vincolo</span><span class="sxs-lookup"><span data-stu-id="5414e-125">`default` constraint</span></span>

<span data-ttu-id="5414e-126">Il `default` vincolo può essere usato nell'override di un metodo o in un'implementazione esplicita per evitare ambiguità che `T?` significa "parametro di tipo Nullable" da "tipo di valore Nullable" ( `Nullable<T>` ).</span><span class="sxs-lookup"><span data-stu-id="5414e-126">The `default` constraint can be used on a method override or explicit implementation to disambiguate `T?` meaning "nullable type parameter" from "nullable value type" (`Nullable<T>`).</span></span> <span data-ttu-id="5414e-127">Mancanza del `default` vincolo `T?` , una sintassi in un'implementazione di override o esplicita verrà interpretata come `Nullable<T>`</span><span class="sxs-lookup"><span data-stu-id="5414e-127">Lacking the `default` constraint a `T?` syntax in an override or explicit implementation will be interpreted as `Nullable<T>`</span></span>

<span data-ttu-id="5414e-128">Vedere https://github.com/dotnet/csharplang/blob/master/proposals/csharp-9.0/unconstrained-type-parameter-annotations.md#default-constraint</span><span class="sxs-lookup"><span data-stu-id="5414e-128">See https://github.com/dotnet/csharplang/blob/master/proposals/csharp-9.0/unconstrained-type-parameter-annotations.md#default-constraint</span></span>

### <a name="the-null-forgiving-operator"></a><span data-ttu-id="5414e-129">Operatore che perdona i valori null</span><span class="sxs-lookup"><span data-stu-id="5414e-129">The null-forgiving operator</span></span>

<span data-ttu-id="5414e-130">L'operatore post-correzione `!` è denominato operatore che perdona i valori null.</span><span class="sxs-lookup"><span data-stu-id="5414e-130">The post-fix `!` operator is called the null-forgiving operator.</span></span> <span data-ttu-id="5414e-131">Può essere applicato a un *primary_expression* o all'interno di un *null_conditional_expression*:</span><span class="sxs-lookup"><span data-stu-id="5414e-131">It can be applied on a *primary_expression* or within a *null_conditional_expression*:</span></span>

```antlr
primary_expression
    : ...
    | null_forgiving_expression
    ;

null_forgiving_expression
    : primary_expression '!'
    ;

null_conditional_expression
    : primary_expression null_conditional_operations_no_suppression suppression?
    ;

null_conditional_operations_no_suppression
    : null_conditional_operations? '?' '.' identifier type_argument_list?
    | null_conditional_operations? '?' '[' argument_list ']'
    | null_conditional_operations '.' identifier type_argument_list?
    | null_conditional_operations '[' argument_list ']'
    | null_conditional_operations '(' argument_list? ')'
    ;

null_conditional_operations
    : null_conditional_operations_no_suppression suppression?
    ;

suppression
    : '!'
    ;
```

<span data-ttu-id="5414e-132">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5414e-132">For example:</span></span>

```csharp
var v = expr!;
expr!.M();
_ = a?.b!.c;
```

<span data-ttu-id="5414e-133">`primary_expression`E `null_conditional_operations_no_suppression` devono essere di un tipo Nullable.</span><span class="sxs-lookup"><span data-stu-id="5414e-133">The `primary_expression` and `null_conditional_operations_no_suppression` must be of a nullable type.</span></span>

<span data-ttu-id="5414e-134">L' `!` operatore di suffisso non ha alcun effetto in fase di esecuzione. restituisce il risultato dell'espressione sottostante.</span><span class="sxs-lookup"><span data-stu-id="5414e-134">The postfix `!` operator has no runtime effect - it evaluates to the result of the underlying expression.</span></span> <span data-ttu-id="5414e-135">Il suo unico ruolo consiste nel modificare lo stato null dell'espressione in "not null" e per limitare gli avvisi in base al relativo utilizzo.</span><span class="sxs-lookup"><span data-stu-id="5414e-135">Its only role is to change the null state of the expression to "not null", and to limit warnings given on its use.</span></span>

### <a name="nullable-compiler-directives"></a><span data-ttu-id="5414e-136">Direttive del compilatore Nullable</span><span class="sxs-lookup"><span data-stu-id="5414e-136">Nullable compiler directives</span></span>

<span data-ttu-id="5414e-137">`#nullable` le direttive controllano i contesti di annotazione e avviso Nullable.</span><span class="sxs-lookup"><span data-stu-id="5414e-137">`#nullable` directives control the nullable annotation and warning contexts.</span></span>

```antlr
pp_directive
    : ...
    | pp_nullable
    ;

pp_nullable
    : whitespace? '#' whitespace? 'nullable' whitespace nullable_action (whitespace nullable_target)? pp_new_line
    ;

nullable_action
    : 'disable'
    | 'enable'
    | 'restore'
    ;

nullable_target
    : 'warnings'
    | 'annotations'
    ;
```

<span data-ttu-id="5414e-138">`#pragma warning` le direttive vengono espanse per consentire la modifica del contesto di avviso Nullable:</span><span class="sxs-lookup"><span data-stu-id="5414e-138">`#pragma warning` directives are expanded to allow changing the nullable warning context:</span></span>

```antlr
pragma_warning_body
    : ...
    | 'warning' whitespace warning_action whitespace 'nullable'
    ;
```

<span data-ttu-id="5414e-139">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5414e-139">For example:</span></span>

```csharp
#pragma warning disable nullable
```

## <a name="nullable-contexts"></a><span data-ttu-id="5414e-140">Contesti nullable</span><span class="sxs-lookup"><span data-stu-id="5414e-140">Nullable contexts</span></span>

<span data-ttu-id="5414e-141">Ogni riga del codice sorgente ha un *contesto di annotazione Nullable* e un *contesto di avviso Nullable*.</span><span class="sxs-lookup"><span data-stu-id="5414e-141">Every line of source code has a *nullable annotation context* and a *nullable warning context*.</span></span> <span data-ttu-id="5414e-142">Questi controllano se le annotazioni Nullable hanno effetto e se vengono specificati avvisi di supporto dei valori null.</span><span class="sxs-lookup"><span data-stu-id="5414e-142">These control whether nullable annotations have effect, and whether nullability warnings are given.</span></span> <span data-ttu-id="5414e-143">Il contesto di annotazione di una determinata riga è *disabilitato* o *abilitato*.</span><span class="sxs-lookup"><span data-stu-id="5414e-143">The annotation context of a given line is either *disabled* or *enabled*.</span></span> <span data-ttu-id="5414e-144">Il contesto di avviso di una determinata riga è *disabilitato* o *abilitato*.</span><span class="sxs-lookup"><span data-stu-id="5414e-144">The warning context of a given line is either *disabled* or *enabled*.</span></span>

<span data-ttu-id="5414e-145">Entrambi i contesti possono essere specificati a livello di progetto (all'esterno del codice sorgente C#) o in qualsiasi punto all'interno di un file di origine tramite `#nullable` direttive pre-processore.</span><span class="sxs-lookup"><span data-stu-id="5414e-145">Both contexts can be specified at the project level (outside of C# source code), or anywhere within a source file via `#nullable` pre-processor directives.</span></span> <span data-ttu-id="5414e-146">Se non vengono specificate impostazioni a livello di progetto, il valore predefinito è per entrambi i contesti da *disabilitare*.</span><span class="sxs-lookup"><span data-stu-id="5414e-146">If no project level settings are provided the default is for both contexts to be *disabled*.</span></span>

<span data-ttu-id="5414e-147">La `#nullable` direttiva controlla l'annotazione e i contesti di avviso all'interno del testo di origine e hanno la precedenza sulle impostazioni a livello di progetto.</span><span class="sxs-lookup"><span data-stu-id="5414e-147">The `#nullable` directive controls the annotation and warning contexts within the source text, and take precedence over the project-level settings.</span></span>

<span data-ttu-id="5414e-148">Una direttiva imposta i contesti che controlla per le righe di codice successive, fino a quando un'altra direttiva ne esegue l'override o fino alla fine del file di origine.</span><span class="sxs-lookup"><span data-stu-id="5414e-148">A directive sets the context(s) it controls for subsequent lines of code, until another directive overrides it, or until the end of the source file.</span></span>

<span data-ttu-id="5414e-149">L'effetto delle direttive è il seguente:</span><span class="sxs-lookup"><span data-stu-id="5414e-149">The effect of the directives is as follows:</span></span>

- <span data-ttu-id="5414e-150">`#nullable disable`: Imposta l'annotazione nullable e i contesti di avviso su *disabled*</span><span class="sxs-lookup"><span data-stu-id="5414e-150">`#nullable disable`: Sets the nullable annotation and warning contexts to *disabled*</span></span>
- <span data-ttu-id="5414e-151">`#nullable enable`: Imposta l'annotazione nullable e i contesti di avviso su *Enabled*</span><span class="sxs-lookup"><span data-stu-id="5414e-151">`#nullable enable`: Sets the nullable annotation and warning contexts to *enabled*</span></span>
- <span data-ttu-id="5414e-152">`#nullable restore`: Ripristina i contesti di avviso e di annotazione Nullable nelle impostazioni del progetto</span><span class="sxs-lookup"><span data-stu-id="5414e-152">`#nullable restore`: Restores the nullable annotation and warning contexts to project settings</span></span>
- <span data-ttu-id="5414e-153">`#nullable disable annotations`: Imposta il contesto di annotazione Nullable su *disabled*</span><span class="sxs-lookup"><span data-stu-id="5414e-153">`#nullable disable annotations`: Sets the nullable annotation context to *disabled*</span></span>
- <span data-ttu-id="5414e-154">`#nullable enable annotations`: Imposta il contesto di annotazione Nullable su *Enabled*</span><span class="sxs-lookup"><span data-stu-id="5414e-154">`#nullable enable annotations`: Sets the nullable annotation context to *enabled*</span></span>
- <span data-ttu-id="5414e-155">`#nullable restore annotations`: Ripristina il contesto di annotazione Nullable nelle impostazioni del progetto</span><span class="sxs-lookup"><span data-stu-id="5414e-155">`#nullable restore annotations`: Restores the nullable annotation context to project settings</span></span>
- <span data-ttu-id="5414e-156">`#nullable disable warnings`: Imposta il contesto di avviso Nullable su *disabled*</span><span class="sxs-lookup"><span data-stu-id="5414e-156">`#nullable disable warnings`: Sets the nullable warning context to *disabled*</span></span>
- <span data-ttu-id="5414e-157">`#nullable enable warnings`: Imposta il contesto di avviso Nullable su *Enabled*</span><span class="sxs-lookup"><span data-stu-id="5414e-157">`#nullable enable warnings`: Sets the nullable warning context to *enabled*</span></span>
- <span data-ttu-id="5414e-158">`#nullable restore warnings`: Ripristina il contesto di avviso Nullable nelle impostazioni del progetto</span><span class="sxs-lookup"><span data-stu-id="5414e-158">`#nullable restore warnings`: Restores the nullable warning context to project settings</span></span>

## <a name="nullability-of-types"></a><span data-ttu-id="5414e-159">Supporto dei valori Null dei tipi</span><span class="sxs-lookup"><span data-stu-id="5414e-159">Nullability of types</span></span>

<span data-ttu-id="5414e-160">Un tipo specificato può avere uno dei tre nullabilities: *ignaro*, non *nullo* e *Nullable*.</span><span class="sxs-lookup"><span data-stu-id="5414e-160">A given type can have one of three nullabilities: *oblivious*, *nonnullable*, and *nullable*.</span></span>

<span data-ttu-id="5414e-161">I tipi che non *ammettono valori null* possono causare avvisi se `null` viene assegnato un valore potenziale.</span><span class="sxs-lookup"><span data-stu-id="5414e-161">*Nonnullable* types may cause warnings if a potential `null` value is assigned to them.</span></span> <span data-ttu-id="5414e-162">I tipi *ignari* e *Nullable* , tuttavia, sono "*assegnabili a null*" e possono avere `null` valori assegnati senza avvisi.</span><span class="sxs-lookup"><span data-stu-id="5414e-162">*Oblivious* and *nullable* types, however, are "*null-assignable*" and can have `null` values assigned to them without warnings.</span></span>

<span data-ttu-id="5414e-163">I valori dei tipi *ignari* e non *null* possono essere dereferenziati o assegnati senza avvisi.</span><span class="sxs-lookup"><span data-stu-id="5414e-163">Values of *oblivious* and *nonnullable* types can be dereferenced or assigned without warnings.</span></span> <span data-ttu-id="5414e-164">I valori dei tipi *Nullable* , tuttavia, sono "*null-Yielding*" e possono causare avvisi quando vengono dereferenziati o assegnati senza il controllo null appropriato.</span><span class="sxs-lookup"><span data-stu-id="5414e-164">Values of *nullable* types, however, are "*null-yielding*" and may cause warnings when dereferenced or assigned without proper null checking.</span></span>

<span data-ttu-id="5414e-165">Lo *stato null predefinito* di un tipo che cede un valore null è "Maybe null" o "Maybe default".</span><span class="sxs-lookup"><span data-stu-id="5414e-165">The *default null state* of a null-yielding type is "maybe null" or "maybe default".</span></span> <span data-ttu-id="5414e-166">Lo stato null predefinito di un tipo non null restituisce "not null".</span><span class="sxs-lookup"><span data-stu-id="5414e-166">The default null state of a non-null-yielding type is "not null".</span></span>

<span data-ttu-id="5414e-167">Il tipo di tipo e il contesto di annotazione nullable in cui si verificano determinano il supporto di valori null:</span><span class="sxs-lookup"><span data-stu-id="5414e-167">The kind of type and the nullable annotation context it occurs in determine its nullability:</span></span>

- <span data-ttu-id="5414e-168">Un tipo di valore che non ammette valori null `S` è sempre non *null*</span><span class="sxs-lookup"><span data-stu-id="5414e-168">A nonnullable value type `S` is always *nonnullable*</span></span>
- <span data-ttu-id="5414e-169">Un tipo di valore Nullable `S?` è sempre *Nullable*</span><span class="sxs-lookup"><span data-stu-id="5414e-169">A nullable value type `S?` is always *nullable*</span></span>
- <span data-ttu-id="5414e-170">Un tipo di riferimento senza annotazioni `C` in un contesto di annotazione *disabilitato* è *ignaro*</span><span class="sxs-lookup"><span data-stu-id="5414e-170">An unannotated reference type `C` in a *disabled* annotation context is *oblivious*</span></span>
- <span data-ttu-id="5414e-171">Un tipo di riferimento non annotato `C` in un contesto di annotazione *abilitato* non è *null*</span><span class="sxs-lookup"><span data-stu-id="5414e-171">An unannotated reference type `C` in an *enabled* annotation context is *nonnullable*</span></span>
- <span data-ttu-id="5414e-172">Un tipo di riferimento Nullable ammette i `C?` *valori null* (ma è possibile che venga restituito un avviso in un contesto di annotazione *disabilitato* )</span><span class="sxs-lookup"><span data-stu-id="5414e-172">A nullable reference type `C?` is *nullable* (but a warning may be yielded in a *disabled* annotation context)</span></span>

<span data-ttu-id="5414e-173">I parametri di tipo prendono inoltre in considerazione i vincoli:</span><span class="sxs-lookup"><span data-stu-id="5414e-173">Type parameters additionally take their constraints into account:</span></span>

- <span data-ttu-id="5414e-174">Parametro di tipo in `T` cui tutti i vincoli (se presenti) sono tipi nullable o il `class?` vincolo *ammette i valori null*</span><span class="sxs-lookup"><span data-stu-id="5414e-174">A type parameter `T` where all constraints (if any) are either nullable types or the `class?` constraint is *nullable*</span></span>
- <span data-ttu-id="5414e-175">Parametro di tipo `T` in cui almeno un vincolo è *ignaro* o non può essere *null* o uno dei `struct` `class` vincoli o o `notnull` è</span><span class="sxs-lookup"><span data-stu-id="5414e-175">A type parameter `T` where at least one constraint is either *oblivious* or *nonnullable* or one of the `struct` or `class` or `notnull` constraints is</span></span>
    - <span data-ttu-id="5414e-176">*ignaro* in un contesto di annotazione *disabilitato*</span><span class="sxs-lookup"><span data-stu-id="5414e-176">*oblivious* in a *disabled* annotation context</span></span>
    - <span data-ttu-id="5414e-177">non *ammette valori null* in un contesto di annotazione *abilitato*</span><span class="sxs-lookup"><span data-stu-id="5414e-177">*nonnullable* in an *enabled* annotation context</span></span>
- <span data-ttu-id="5414e-178">Un parametro di tipo Nullable ammette i `T?` *valori null*, ma un avviso viene restituito in un contesto di annotazione *disabilitato* se `T` non è un tipo di valore</span><span class="sxs-lookup"><span data-stu-id="5414e-178">A nullable type parameter `T?` is *nullable*, but a warning is yielded in a *disabled* annotation context if `T` isn't a value type</span></span>

### <a name="oblivious-vs-nonnullable"></a><span data-ttu-id="5414e-179">E non nullable</span><span class="sxs-lookup"><span data-stu-id="5414e-179">Oblivious vs nonnullable</span></span>

<span data-ttu-id="5414e-180">Un `type` viene considerato che si verifica in un contesto di annotazione specificato quando l'ultimo token del tipo si trova all'interno di tale contesto.</span><span class="sxs-lookup"><span data-stu-id="5414e-180">A `type` is deemed to occur in a given annotation context when the last token of the type is within that context.</span></span>

<span data-ttu-id="5414e-181">Se un tipo di riferimento specificato `C` nel codice sorgente viene interpretato come ignaro o non nullo dipende dal contesto dell'annotazione di tale codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="5414e-181">Whether a given reference type `C` in source code is interpreted as oblivious or nonnullable depends on the annotation context of that source code.</span></span> <span data-ttu-id="5414e-182">Una volta stabilito, viene considerato parte di quel tipo e "lo sposta con esso", ad esempio durante la sostituzione di argomenti di tipo generico.</span><span class="sxs-lookup"><span data-stu-id="5414e-182">But once established, it is considered part of that type, and "travels with it" e.g. during substitution of generic type arguments.</span></span> <span data-ttu-id="5414e-183">È come se fosse presente un'annotazione come `?` nel tipo, ma invisibile.</span><span class="sxs-lookup"><span data-stu-id="5414e-183">It is as if there is an annotation like `?` on the type, but invisible.</span></span>

## <a name="constraints"></a><span data-ttu-id="5414e-184">Vincoli</span><span class="sxs-lookup"><span data-stu-id="5414e-184">Constraints</span></span>

<span data-ttu-id="5414e-185">I tipi di riferimento nullable possono essere utilizzati come vincoli generici.</span><span class="sxs-lookup"><span data-stu-id="5414e-185">Nullable reference types can be used as generic constraints.</span></span>

<span data-ttu-id="5414e-186">`class?` è un nuovo vincolo che indica "probabile tipo di riferimento Nullable", mentre `class` in un contesto di annotazione *abilitato* denota "tipo di riferimento non null".</span><span class="sxs-lookup"><span data-stu-id="5414e-186">`class?` is a new constraint denoting "possibly nullable reference type", whereas `class` in an *enabled* annotation context denotes "nonnullable reference type".</span></span>

<span data-ttu-id="5414e-187">`default` è un nuovo vincolo che indica un parametro di tipo che non è noto come tipo di riferimento o di valore.</span><span class="sxs-lookup"><span data-stu-id="5414e-187">`default` is a new constraint denoting a type parameter that isn't known to be a reference or value type.</span></span> <span data-ttu-id="5414e-188">Può essere utilizzato solo su metodi sottoposti a override e implementati in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="5414e-188">It can only be used on overridden and explicitly implemented methods.</span></span> <span data-ttu-id="5414e-189">Con questo vincolo, `T?` significa un parametro di tipo nullable, anziché essere una sintassi abbreviata per `Nullable<T>` .</span><span class="sxs-lookup"><span data-stu-id="5414e-189">With this constraint, `T?` means a nullable type parameter, as opposed to being a shorthand for `Nullable<T>`.</span></span>

<span data-ttu-id="5414e-190">`notnull` è un nuovo vincolo che indica un parametro di tipo che non ammette valori null.</span><span class="sxs-lookup"><span data-stu-id="5414e-190">`notnull` is a new constraint denoting a type parameter that is nonnullable.</span></span>

<span data-ttu-id="5414e-191">Il supporto di valori null di un argomento di tipo o di un vincolo non ha alcun effetto sul fatto che il tipo soddisfi il vincolo, ad eccezione del caso in cui è già presente. i tipi di valore nullable non soddisfano il `struct` vincolo.</span><span class="sxs-lookup"><span data-stu-id="5414e-191">The nullability of a type argument or of a constraint does not impact whether the type satisfies the constraint, except where that is already the case today (nullable value types do not satisfy the `struct` constraint).</span></span> <span data-ttu-id="5414e-192">Tuttavia, se l'argomento di tipo non soddisfa i requisiti di supporto dei valori null del vincolo, è possibile che venga fornito un avviso.</span><span class="sxs-lookup"><span data-stu-id="5414e-192">However, if the type argument does not satisfy the nullability requirements of the constraint, a warning may be given.</span></span>

## <a name="null-state-and-null-tracking"></a><span data-ttu-id="5414e-193">Stato null e rilevamento null</span><span class="sxs-lookup"><span data-stu-id="5414e-193">Null state and null tracking</span></span>

<span data-ttu-id="5414e-194">Ogni espressione in una determinata posizione di origine ha uno *stato null*, che indica se si ritiene che possa restituire potenzialmente null.</span><span class="sxs-lookup"><span data-stu-id="5414e-194">Every expression in a given source location has a *null state*, which indicated whether it is believed to potentially evaluate to null.</span></span> <span data-ttu-id="5414e-195">Lo stato null può essere "not null", "Maybe null" o "Maybe default".</span><span class="sxs-lookup"><span data-stu-id="5414e-195">The null state is either "not null", "maybe null", or "maybe default".</span></span> <span data-ttu-id="5414e-196">Lo stato null viene utilizzato per determinare se è necessario che venga fornito un avviso sulle conversioni e le dereferenziazioni non sicure null.</span><span class="sxs-lookup"><span data-stu-id="5414e-196">The null state is used to determine whether a warning should be given about null-unsafe conversions and dereferences.</span></span>

<span data-ttu-id="5414e-197">La distinzione tra "Maybe null" e "Maybe default" è sottile e si applica ai parametri di tipo.</span><span class="sxs-lookup"><span data-stu-id="5414e-197">The distinction between "maybe null" and "maybe default" is subtle and applies to type parameters.</span></span> <span data-ttu-id="5414e-198">La distinzione è che un parametro `T` di tipo con lo stato "Maybe null" significa che il valore si trova nel dominio dei valori validi per `T` tuttavia il valore valido può includere `null` .</span><span class="sxs-lookup"><span data-stu-id="5414e-198">The distinction is that a type parameter `T` which has the state "maybe null" means the value is in the domain of legal values for `T` however that legal value may include `null`.</span></span> <span data-ttu-id="5414e-199">Dove come "impostazione predefinita" indica che il valore può essere esterno al dominio valido dei valori per `T` .</span><span class="sxs-lookup"><span data-stu-id="5414e-199">Where as a "maybe default" means that the value may be outside the legal domain of values for `T`.</span></span> 

<span data-ttu-id="5414e-200">Esempio:</span><span class="sxs-lookup"><span data-stu-id="5414e-200">Example:</span></span> 

```c#
// The value `t` here has the state "maybe null". It's possible for `T` to be instantiated
// with `string?` in which case `null` would be within the domain of legal values here. The 
// assumption though is the value provided here is within the legal values of `T`. Hence 
// if `T` is `string` then `null` will not be a value, just as we assume that `null` is not
// provided for a normal `string` parameter
void M<T>(T t)
{
    // There is no guarantee that default(T) is within the legal values for T hence the 
    // state *must* be "maybe-default" and hence `local` must be `T?`
    T? local = default(T);
}
```

### <a name="null-tracking-for-variables"></a><span data-ttu-id="5414e-201">Rilevamento di valori null per le variabili</span><span class="sxs-lookup"><span data-stu-id="5414e-201">Null tracking for variables</span></span>

<span data-ttu-id="5414e-202">Per alcune espressioni che indicano variabili, campi o proprietà, lo stato null viene rilevato tra le occorrenze, in base alle assegnazioni, i test eseguiti su di essi e il flusso di controllo tra di essi.</span><span class="sxs-lookup"><span data-stu-id="5414e-202">For certain expressions denoting variables, fields or properties, the null state is tracked between occurrences, based on assignments to them, tests performed on them and the control flow between them.</span></span> <span data-ttu-id="5414e-203">Questa operazione è simile alla modalità di rilevamento dell'assegnazione definita per le variabili.</span><span class="sxs-lookup"><span data-stu-id="5414e-203">This is similar to how definite assignment is tracked for variables.</span></span> <span data-ttu-id="5414e-204">Le espressioni registrate sono quelle con il formato seguente:</span><span class="sxs-lookup"><span data-stu-id="5414e-204">The tracked expressions are the ones of the following form:</span></span>

```antlr
tracked_expression
    : simple_name
    | this
    | base
    | tracked_expression '.' identifier
    ;
```

<span data-ttu-id="5414e-205">Dove gli identificatori denotano i campi o le proprietà.</span><span class="sxs-lookup"><span data-stu-id="5414e-205">Where the identifiers denote fields or properties.</span></span>

<span data-ttu-id="5414e-206">Lo stato null per le variabili rilevate è "not null" in codice non eseguibile.</span><span class="sxs-lookup"><span data-stu-id="5414e-206">The null state for tracked variables is "not null" in unreachable code.</span></span> <span data-ttu-id="5414e-207">Questo segue le altre decisioni riguardanti il codice non eseguibile, ad esempio considerando tutte le variabili locali da assegnare definitivamente.</span><span class="sxs-lookup"><span data-stu-id="5414e-207">This follows other decisions around unreachable code like considering all locals to be definitely assigned.</span></span>

<span data-ttu-id="5414e-208">\***Descrivere le transizioni di stato null simili all'assegnazione definita** _</span><span class="sxs-lookup"><span data-stu-id="5414e-208">\***Describe null state transitions similar to definite assignment** _</span></span>

### <a name="null-state-for-expressions"></a><span data-ttu-id="5414e-209">Stato null per le espressioni</span><span class="sxs-lookup"><span data-stu-id="5414e-209">Null state for expressions</span></span>

<span data-ttu-id="5414e-210">Lo stato null di un'espressione deriva dal formato e dal tipo e dallo stato null delle variabili in esso incluse.</span><span class="sxs-lookup"><span data-stu-id="5414e-210">The null state of an expression is derived from its form and type, and from the null state of variables involved in it.</span></span>

### <a name="literals"></a><span data-ttu-id="5414e-211">Valori letterali</span><span class="sxs-lookup"><span data-stu-id="5414e-211">Literals</span></span>

<span data-ttu-id="5414e-212">Lo stato null di un `null` valore letterale dipende dal tipo di destinazione dell'espressione.</span><span class="sxs-lookup"><span data-stu-id="5414e-212">The null state of a `null` literal depends on the target type of the expression.</span></span> <span data-ttu-id="5414e-213">Se il tipo di destinazione è un parametro di tipo vincolato a un tipo di riferimento, è "probabilmente default".</span><span class="sxs-lookup"><span data-stu-id="5414e-213">If the target type is a type parameter constrained to a reference type then it's "maybe default".</span></span> <span data-ttu-id="5414e-214">In caso contrario, è "Maybe null".</span><span class="sxs-lookup"><span data-stu-id="5414e-214">Otherwise it is "maybe null".</span></span>

<span data-ttu-id="5414e-215">Lo stato null di un `default` valore letterale dipende dal tipo di destinazione del `default` valore letterale.</span><span class="sxs-lookup"><span data-stu-id="5414e-215">The null state of a `default` literal depends on the target type of the `default` literal.</span></span> <span data-ttu-id="5414e-216">Un `default` valore letterale con tipo di destinazione `T` ha lo stesso stato null dell' `default(T)` espressione.</span><span class="sxs-lookup"><span data-stu-id="5414e-216">A `default` literal with target type `T` has the same null state as the `default(T)` expression.</span></span>

<span data-ttu-id="5414e-217">Lo stato null di qualsiasi altro valore letterale è "not null".</span><span class="sxs-lookup"><span data-stu-id="5414e-217">The null state of any other literal is "not null".</span></span>

### <a name="simple-names"></a><span data-ttu-id="5414e-218">Nomi semplici</span><span class="sxs-lookup"><span data-stu-id="5414e-218">Simple names</span></span>

<span data-ttu-id="5414e-219">Se un oggetto `simple_name` non è classificato come valore, il relativo stato null è "not null".</span><span class="sxs-lookup"><span data-stu-id="5414e-219">If a `simple_name` is not classified as a value, its null state is "not null".</span></span> <span data-ttu-id="5414e-220">In caso contrario, si tratta di un'espressione rilevata e il relativo stato null è il relativo stato null rilevato in questa posizione di origine.</span><span class="sxs-lookup"><span data-stu-id="5414e-220">Otherwise it is a tracked expression, and its null state is its tracked null state at this source location.</span></span>

### <a name="member-access"></a><span data-ttu-id="5414e-221">Accesso ai membri</span><span class="sxs-lookup"><span data-stu-id="5414e-221">Member access</span></span>

<span data-ttu-id="5414e-222">Se un oggetto `member_access` non è classificato come valore, il relativo stato null è "not null".</span><span class="sxs-lookup"><span data-stu-id="5414e-222">If a `member_access` is not classified as a value, its null state is "not null".</span></span> <span data-ttu-id="5414e-223">In caso contrario, se si tratta di un'espressione rilevata, il relativo stato null è il relativo stato null rilevato in questa posizione di origine.</span><span class="sxs-lookup"><span data-stu-id="5414e-223">Otherwise, if it is a tracked expression, its null state is its tracked null state at this source location.</span></span> <span data-ttu-id="5414e-224">In caso contrario, lo stato null è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="5414e-224">Otherwise its null state is the default null state of its type.</span></span>

```c#
var person = new Person();

// The receiver is a tracked expression hence the member_access of the property 
// is tracked as well 
if (person.FirstName is not null)
{
    Use(person.FirstName);
}

// The return of an invocation is not a tracked expression hence the member_access
// of the return is also not tracked
if (GetAnonymous().FirstName is not null)
{
    // Warning: Cannot convert null literal to non-nullable reference type.
    Use(GetAnonymous().FirstName);
}

void Use(string s) 
{ 
    // ...
}

public class Person
{
    public string? FirstName { get; set; }
    public string? LastName { get; set; }

    private static Person s_anonymous = new Person();
    public static Person GetAnonymous() => s_anonymous;
}
```

### <a name="invocation-expressions"></a><span data-ttu-id="5414e-225">Espressioni di chiamata</span><span class="sxs-lookup"><span data-stu-id="5414e-225">Invocation expressions</span></span>

<span data-ttu-id="5414e-226">Se un oggetto `invocation_expression` richiama un membro dichiarato con uno o più attributi per un comportamento null speciale, lo stato null è determinato da tali attributi.</span><span class="sxs-lookup"><span data-stu-id="5414e-226">If an `invocation_expression` invokes a member that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="5414e-227">In caso contrario, lo stato null dell'espressione è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="5414e-227">Otherwise the null state of the expression is the default null state of its type.</span></span>

<span data-ttu-id="5414e-228">Lo stato null di un oggetto `invocation_expression` non viene rilevato dal compilatore.</span><span class="sxs-lookup"><span data-stu-id="5414e-228">The null state of an `invocation_expression` is not tracked by the compiler.</span></span>

```c#

// The result of an invocation_expression is not tracked
if (GetText() is not null)
{
    // Warning: Converting null literal or possible null value to non-nullable type.
    string s = GetText();
    // Warning: Dereference of a possibly null reference.
    Use(s);
}

// Nullable friendly pattern
if (GetText() is string s)
{
    Use(s);
}

string? GetText() => ... 
Use(string s) {  }
```

### <a name="element-access"></a><span data-ttu-id="5414e-229">Accesso a elementi</span><span class="sxs-lookup"><span data-stu-id="5414e-229">Element access</span></span>

<span data-ttu-id="5414e-230">Se un oggetto `element_access` richiama un indicizzatore dichiarato con uno o più attributi per un comportamento null speciale, lo stato null è determinato da tali attributi.</span><span class="sxs-lookup"><span data-stu-id="5414e-230">If an `element_access` invokes an indexer that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="5414e-231">In caso contrario, lo stato null dell'espressione è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="5414e-231">Otherwise the null state of the expression is the default null state of its type.</span></span>

```c#
object?[] array = ...;
if (array[0] != null)
{
    // Warning: Converting null literal or possible null value to non-nullable type.
    object o = array[0];
    // Warning: Dereference of a possibly null reference.
    Console.WriteLine(o.ToString());
}

// Nullable friendly pattern
if (array[0] is {} o)
{
    Console.WriteLine(o.ToString());
}
```

### <a name="base-access"></a><span data-ttu-id="5414e-232">Accesso di base</span><span class="sxs-lookup"><span data-stu-id="5414e-232">Base access</span></span>

<span data-ttu-id="5414e-233">Se `B` denota il tipo di base del tipo di inclusione, `base.I` ha lo stesso stato null di `((B)this).I` e `base[E]` ha lo stesso stato null di `((B)this)[E]` .</span><span class="sxs-lookup"><span data-stu-id="5414e-233">If `B` denotes the base type of the enclosing type, `base.I` has the same null state as `((B)this).I` and `base[E]` has the same null state as `((B)this)[E]`.</span></span>

### <a name="default-expressions"></a><span data-ttu-id="5414e-234">Espressioni predefinite</span><span class="sxs-lookup"><span data-stu-id="5414e-234">Default expressions</span></span>

<span data-ttu-id="5414e-235">`default(T)` ha lo stato null in base alle proprietà del tipo `T` :</span><span class="sxs-lookup"><span data-stu-id="5414e-235">`default(T)` has the null state based on the properties of the type `T`:</span></span>

- <span data-ttu-id="5414e-236">Se il tipo è un tipo _nonnullable \*, avrà lo stato null "not null"</span><span class="sxs-lookup"><span data-stu-id="5414e-236">If the type is a _nonnullable\* type then it has the null state "not null"</span></span>
- <span data-ttu-id="5414e-237">Altrimenti, se il tipo è un parametro di tipo, avrà lo stato null "Maybe default"</span><span class="sxs-lookup"><span data-stu-id="5414e-237">Else if the type is a type parameter then it has the null state "maybe default"</span></span>
- <span data-ttu-id="5414e-238">In caso contrario, ha lo stato null "Maybe null"</span><span class="sxs-lookup"><span data-stu-id="5414e-238">Else it has the null state "maybe null"</span></span>

### <a name="null-conditional-expressions-"></a><span data-ttu-id="5414e-239">Espressioni condizionali null?.</span><span class="sxs-lookup"><span data-stu-id="5414e-239">Null-conditional expressions ?.</span></span>

<span data-ttu-id="5414e-240">Un oggetto `null_conditional_expression` ha lo stato null in base al tipo di espressione.</span><span class="sxs-lookup"><span data-stu-id="5414e-240">A `null_conditional_expression` has the null state based on the expression type.</span></span> <span data-ttu-id="5414e-241">Si noti che si riferisce al tipo di `null_conditional_expression` , non al tipo originale del membro richiamato:</span><span class="sxs-lookup"><span data-stu-id="5414e-241">Note that this refers to the type of the `null_conditional_expression`, not the original type of the member being invoked:</span></span>

- <span data-ttu-id="5414e-242">Se il tipo è un tipo di valore *Nullable* , sarà presente lo stato null "Maybe null"</span><span class="sxs-lookup"><span data-stu-id="5414e-242">If the type is a *nullable* value type then it has the null state "maybe null"</span></span>
- <span data-ttu-id="5414e-243">Altrimenti, se il tipo è un parametro di tipo *Nullable* , avrà lo stato null "Maybe default"</span><span class="sxs-lookup"><span data-stu-id="5414e-243">Else if the type is a *nullable* type parameter then it has the null state "maybe default"</span></span>
- <span data-ttu-id="5414e-244">In caso contrario, ha lo stato null "Maybe null"</span><span class="sxs-lookup"><span data-stu-id="5414e-244">Else it has the null state "maybe null"</span></span>

### <a name="cast-expressions"></a><span data-ttu-id="5414e-245">Espressioni cast</span><span class="sxs-lookup"><span data-stu-id="5414e-245">Cast expressions</span></span>

<span data-ttu-id="5414e-246">Se un'espressione cast `(T)E` richiama una conversione definita dall'utente, lo stato null dell'espressione sarà lo stato null predefinito per il tipo della conversione definita dall'utente.</span><span class="sxs-lookup"><span data-stu-id="5414e-246">If a cast expression `(T)E` invokes a user-defined conversion, then the null state of the expression is the default null state for the type of the user-defined conversion.</span></span> <span data-ttu-id="5414e-247">In caso contrario:</span><span class="sxs-lookup"><span data-stu-id="5414e-247">Otherwise:</span></span>

- <span data-ttu-id="5414e-248">Se `T` è un tipo di valore che non *ammette valori null* , `T` ha lo stato null "not null"</span><span class="sxs-lookup"><span data-stu-id="5414e-248">If `T` is a *nonnullable* value type then `T` has the null state "not null"</span></span>
- <span data-ttu-id="5414e-249">Altrimenti, se `T` è un tipo di valore *Nullable* , `T` avrà lo stato null "Maybe null"</span><span class="sxs-lookup"><span data-stu-id="5414e-249">Else if `T` is a *nullable* value type then `T` has the null state "maybe null"</span></span>
- <span data-ttu-id="5414e-250">Altrimenti se `T` è un tipo *Nullable* nel formato in `U?` cui `U` è un parametro di tipo `T` ha lo stato null "Maybe default"</span><span class="sxs-lookup"><span data-stu-id="5414e-250">Else if `T` is a *nullable* type in the form `U?` where `U` is a type parameter then `T` has the null state "maybe default"</span></span>
- <span data-ttu-id="5414e-251">Altrimenti se `T` è un tipo *Nullable* e `E` ha uno stato null "Maybe null" o "Maybe default", `T` ha lo stato null "Maybe null"</span><span class="sxs-lookup"><span data-stu-id="5414e-251">Else if `T` is a *nullable* type, and `E` has null state "maybe null" or "maybe default", then `T` has the null state "maybe null"</span></span>
- <span data-ttu-id="5414e-252">Altrimenti se `T` è un parametro di tipo e `E` ha uno stato null "Maybe null" o "Maybe default", `T` ha lo stato null "Maybe default".</span><span class="sxs-lookup"><span data-stu-id="5414e-252">Else if `T` is a type parameter, and `E` has null state "maybe null" or "maybe default", then `T` has the null state "maybe default"</span></span>
- <span data-ttu-id="5414e-253">Else `T` ha lo stesso stato null di `E`</span><span class="sxs-lookup"><span data-stu-id="5414e-253">Else `T` has the same null state as `E`</span></span>

### <a name="unary-and-binary-operators"></a><span data-ttu-id="5414e-254">Operatori unari e binari</span><span class="sxs-lookup"><span data-stu-id="5414e-254">Unary and binary operators</span></span>

<span data-ttu-id="5414e-255">Se un operatore unario o binario richiama un operatore definito dall'utente, lo stato null dell'espressione è lo stato null predefinito per il tipo dell'operatore definito dall'utente.</span><span class="sxs-lookup"><span data-stu-id="5414e-255">If a unary or binary operator invokes an user-defined operator then the null state of the expression is the default null state for the type of the user-defined operator.</span></span> <span data-ttu-id="5414e-256">In caso contrario, è lo stato null dell'espressione.</span><span class="sxs-lookup"><span data-stu-id="5414e-256">Otherwise it is the null state of the expression.</span></span>

<span data-ttu-id="5414e-257">\*Si tratta **di un'operazione speciale per binari `+` su stringhe e delegati?** _</span><span class="sxs-lookup"><span data-stu-id="5414e-257">\***Something special to do for binary `+` over strings and delegates?** _</span></span>

### <a name="await-expressions"></a><span data-ttu-id="5414e-258">Espressioni await</span><span class="sxs-lookup"><span data-stu-id="5414e-258">Await expressions</span></span>

<span data-ttu-id="5414e-259">Lo stato null di `await E` è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="5414e-259">The null state of `await E` is the default null state of its type.</span></span>

### <a name="the-as-operator"></a><span data-ttu-id="5414e-260">Operatore `as`</span><span class="sxs-lookup"><span data-stu-id="5414e-260">The `as` operator</span></span>

<span data-ttu-id="5414e-261">Lo stato null di un' `E as T` espressione dipende prima dalle proprietà del tipo `T` .</span><span class="sxs-lookup"><span data-stu-id="5414e-261">The null state of an `E as T` expression depends first on properties of the type `T`.</span></span> <span data-ttu-id="5414e-262">Se il tipo di `T` è _nonnullable \*, lo stato null è "not null".</span><span class="sxs-lookup"><span data-stu-id="5414e-262">If the type of `T` is _nonnullable\* then the null state is "not null".</span></span> <span data-ttu-id="5414e-263">In caso contrario, lo stato null dipende dalla conversione dal tipo di `E` al tipo `T` :</span><span class="sxs-lookup"><span data-stu-id="5414e-263">Otherwise the null state depends on the conversion from the type of `E` to type `T`:</span></span>

- <span data-ttu-id="5414e-264">Se la conversione è un'identità, una conversione boxing, un riferimento implicito o una conversione implicita Nullable, lo stato null è lo stato null di `E`</span><span class="sxs-lookup"><span data-stu-id="5414e-264">If the conversion is an identity, boxing, implicit reference, or implicit nullable conversion, then the null state is the null state of `E`</span></span>
- <span data-ttu-id="5414e-265">Altrimenti `T` , se è un parametro di tipo, lo stato del valore null è "Maybe default"</span><span class="sxs-lookup"><span data-stu-id="5414e-265">Else if `T` is a type parameter then it has the null state "maybe default"</span></span>
- <span data-ttu-id="5414e-266">In caso contrario, ha lo stato null "Maybe null"</span><span class="sxs-lookup"><span data-stu-id="5414e-266">Else it has the null state "maybe null"</span></span>

### <a name="the-null-coalescing-operator"></a><span data-ttu-id="5414e-267">Operatore di Unione null</span><span class="sxs-lookup"><span data-stu-id="5414e-267">The null-coalescing operator</span></span>

<span data-ttu-id="5414e-268">Lo stato null di `E1 ?? E2` è lo stato null di `E2`</span><span class="sxs-lookup"><span data-stu-id="5414e-268">The null state of `E1 ?? E2` is the null state of `E2`</span></span>

### <a name="the-conditional-operator"></a><span data-ttu-id="5414e-269">Operatore condizionale</span><span class="sxs-lookup"><span data-stu-id="5414e-269">The conditional operator</span></span>

<span data-ttu-id="5414e-270">Lo stato null di `E1 ? E2 : E3` è basato sullo stato null di `E2` e `E3` :</span><span class="sxs-lookup"><span data-stu-id="5414e-270">The null state of `E1 ? E2 : E3` is based on the null state of `E2` and `E3`:</span></span>

- <span data-ttu-id="5414e-271">Se entrambi sono "not null", lo stato null è "not null"</span><span class="sxs-lookup"><span data-stu-id="5414e-271">If both are "not null" then the null state is "not null"</span></span>
- <span data-ttu-id="5414e-272">Altrimenti, se è "probabilmente default", lo stato null è "Maybe default".</span><span class="sxs-lookup"><span data-stu-id="5414e-272">Else if either is "maybe default" then the null state is "maybe default"</span></span>
- <span data-ttu-id="5414e-273">Altrimenti lo stato null è "not null"</span><span class="sxs-lookup"><span data-stu-id="5414e-273">Else the null state is "not null"</span></span>

### <a name="query-expressions"></a><span data-ttu-id="5414e-274">Espressioni di query</span><span class="sxs-lookup"><span data-stu-id="5414e-274">Query expressions</span></span>

<span data-ttu-id="5414e-275">Lo stato null di un'espressione di query è lo stato null predefinito del relativo tipo.</span><span class="sxs-lookup"><span data-stu-id="5414e-275">The null state of a query expression is the default null state of its type.</span></span>

<span data-ttu-id="5414e-276">*Lavoro aggiuntivo necessario*</span><span class="sxs-lookup"><span data-stu-id="5414e-276">*Additional work needed here*</span></span>

### <a name="assignment-operators"></a><span data-ttu-id="5414e-277">Operatori di assegnazione</span><span class="sxs-lookup"><span data-stu-id="5414e-277">Assignment operators</span></span>

<span data-ttu-id="5414e-278">`E1 = E2` e `E1 op= E2` hanno lo stesso stato null di `E2` dopo l'applicazione di tutte le conversioni implicite.</span><span class="sxs-lookup"><span data-stu-id="5414e-278">`E1 = E2` and `E1 op= E2` have the same null state as `E2` after any implicit conversions have been applied.</span></span>

### <a name="expressions-that-propagate-null-state"></a><span data-ttu-id="5414e-279">Espressioni che propagano lo stato null</span><span class="sxs-lookup"><span data-stu-id="5414e-279">Expressions that propagate null state</span></span>

<span data-ttu-id="5414e-280">`(E)`, `checked(E)` e `unchecked(E)` hanno tutti lo stesso stato null di `E` .</span><span class="sxs-lookup"><span data-stu-id="5414e-280">`(E)`, `checked(E)` and `unchecked(E)` all have the same null state as `E`.</span></span>

### <a name="expressions-that-are-never-null"></a><span data-ttu-id="5414e-281">Espressioni che non sono mai null</span><span class="sxs-lookup"><span data-stu-id="5414e-281">Expressions that are never null</span></span>

<span data-ttu-id="5414e-282">Lo stato null dei seguenti form di espressione è sempre "not null":</span><span class="sxs-lookup"><span data-stu-id="5414e-282">The null state of the following expression forms is always "not null":</span></span>

- <span data-ttu-id="5414e-283">`this` accesso</span><span class="sxs-lookup"><span data-stu-id="5414e-283">`this` access</span></span>
- <span data-ttu-id="5414e-284">stringhe interpolate</span><span class="sxs-lookup"><span data-stu-id="5414e-284">interpolated strings</span></span>
- <span data-ttu-id="5414e-285">`new` espressioni (oggetto, delegato, oggetto anonimo e espressioni di creazione di matrici)</span><span class="sxs-lookup"><span data-stu-id="5414e-285">`new` expressions (object, delegate, anonymous object and array creation expressions)</span></span>
- <span data-ttu-id="5414e-286">Espressioni `typeof`</span><span class="sxs-lookup"><span data-stu-id="5414e-286">`typeof` expressions</span></span>
- <span data-ttu-id="5414e-287">Espressioni `nameof`</span><span class="sxs-lookup"><span data-stu-id="5414e-287">`nameof` expressions</span></span>
- <span data-ttu-id="5414e-288">funzioni anonime (metodi anonimi ed espressioni lambda)</span><span class="sxs-lookup"><span data-stu-id="5414e-288">anonymous functions (anonymous methods and lambda expressions)</span></span>
- <span data-ttu-id="5414e-289">espressioni con indulgenza null</span><span class="sxs-lookup"><span data-stu-id="5414e-289">null-forgiving expressions</span></span>
- <span data-ttu-id="5414e-290">Espressioni `is`</span><span class="sxs-lookup"><span data-stu-id="5414e-290">`is` expressions</span></span>

### <a name="nested-functions"></a><span data-ttu-id="5414e-291">Funzioni annidate</span><span class="sxs-lookup"><span data-stu-id="5414e-291">Nested functions</span></span>

<span data-ttu-id="5414e-292">Le funzioni annidate (espressioni lambda e funzioni locali) vengono trattate come metodi, tranne che per quanto riguarda le variabili acquisite.</span><span class="sxs-lookup"><span data-stu-id="5414e-292">Nested functions (lambdas and local functions) are treated like methods, except in regards to their captured variables.</span></span>
<span data-ttu-id="5414e-293">Lo stato iniziale di una variabile acquisita all'interno di un'espressione lambda o di una funzione locale è l'intersezione dello stato Nullable della variabile in tutti gli "utilizzi" di tale funzione o lambda nidificata.</span><span class="sxs-lookup"><span data-stu-id="5414e-293">The initial state of a captured variable inside a lambda or local function is the intersection of the nullable state of the variable at all the "uses" of that nested function or lambda.</span></span> <span data-ttu-id="5414e-294">L'uso di una funzione locale è una chiamata a tale funzione o quando viene convertito in un delegato.</span><span class="sxs-lookup"><span data-stu-id="5414e-294">A use of a local function is either a call to that function, or where it is converted to a delegate.</span></span> <span data-ttu-id="5414e-295">L'uso di un'espressione lambda è il punto in cui è definito nell'origine.</span><span class="sxs-lookup"><span data-stu-id="5414e-295">A use of a lambda is the point at which it is defined in source.</span></span>

## <a name="type-inference"></a><span data-ttu-id="5414e-296">Inferenza del tipo</span><span class="sxs-lookup"><span data-stu-id="5414e-296">Type inference</span></span>

### <a name="nullable-implicitly-typed-local-variables"></a><span data-ttu-id="5414e-297">variabili locali tipizzate in modo implicito Nullable</span><span class="sxs-lookup"><span data-stu-id="5414e-297">nullable implicitly typed local variables</span></span>

<span data-ttu-id="5414e-298">`var` deduce un tipo con annotazioni per i tipi di riferimento e i parametri di tipo che non sono limitati a essere un tipo valore.</span><span class="sxs-lookup"><span data-stu-id="5414e-298">`var` infers an annotated type for reference types, and type parameters that aren't constrained to be a value type.</span></span>
<span data-ttu-id="5414e-299">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="5414e-299">For instance:</span></span>
- <span data-ttu-id="5414e-300">in `var s = "";` `var` viene dedotto come `string?` .</span><span class="sxs-lookup"><span data-stu-id="5414e-300">in `var s = "";` the `var` is inferred as `string?`.</span></span>
- <span data-ttu-id="5414e-301">in `var t = new T();` con un oggetto non vincolato `T` `var` viene dedotto come `T?` .</span><span class="sxs-lookup"><span data-stu-id="5414e-301">in `var t = new T();` with an unconstrained `T` the `var` is inferred as `T?`.</span></span>

### <a name="generic-type-inference"></a><span data-ttu-id="5414e-302">Inferenza del tipo generico</span><span class="sxs-lookup"><span data-stu-id="5414e-302">Generic type inference</span></span>

<span data-ttu-id="5414e-303">L'inferenza del tipo generico è stata migliorata per determinare se i tipi di riferimento derivati devono essere nullable o meno.</span><span class="sxs-lookup"><span data-stu-id="5414e-303">Generic type inference is enhanced to help decide whether inferred reference types should be nullable or not.</span></span> <span data-ttu-id="5414e-304">Si tratta di un impegno ottimale.</span><span class="sxs-lookup"><span data-stu-id="5414e-304">This is a best effort.</span></span> <span data-ttu-id="5414e-305">Può produrre avvisi relativi ai vincoli di supporto dei valori null e può causare avvisi Nullable quando i tipi dedotti dell'overload selezionato vengono applicati agli argomenti.</span><span class="sxs-lookup"><span data-stu-id="5414e-305">It may yield warnings regarding nullability constraints, and may lead to nullable warnings when the inferred types of the selected overload are applied to the arguments.</span></span>

### <a name="the-first-phase"></a><span data-ttu-id="5414e-306">Prima fase</span><span class="sxs-lookup"><span data-stu-id="5414e-306">The first phase</span></span>

<span data-ttu-id="5414e-307">I tipi di riferimento Nullable scorrono nei limiti delle espressioni iniziali, come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="5414e-307">Nullable reference types flow into the bounds from the initial expressions, as described below.</span></span> <span data-ttu-id="5414e-308">Inoltre, `null` vengono introdotti due nuovi tipi di limiti, ovvero e `default` .</span><span class="sxs-lookup"><span data-stu-id="5414e-308">In addition, two new kinds of bounds, namely `null` and `default` are introduced.</span></span> <span data-ttu-id="5414e-309">Il loro scopo è quello di eseguire le occorrenze di `null` o `default` nelle espressioni di input, che possono causare il Nullable di un tipo derivato, anche in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="5414e-309">Their purpose is to carry through occurrences of `null` or `default` in the input expressions, which may cause an inferred type to be nullable, even when it otherwise wouldn't.</span></span> <span data-ttu-id="5414e-310">Funziona anche per i tipi di *valore* Nullable, che sono stati migliorati per prelevare il "valore null" nel processo di inferenza.</span><span class="sxs-lookup"><span data-stu-id="5414e-310">This works even for nullable *value* types, which are enhanced to pick up "nullness" in the inference process.</span></span>

<span data-ttu-id="5414e-311">La determinazione dei limiti da aggiungere nella prima fase viene migliorata nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="5414e-311">The determination of what bounds to add in the first phase are enhanced as follows:</span></span>

<span data-ttu-id="5414e-312">Se un argomento `Ei` ha un tipo di riferimento, il tipo `U` usato per l'inferenza dipende dallo stato null di e dal `Ei` tipo dichiarato:</span><span class="sxs-lookup"><span data-stu-id="5414e-312">If an argument `Ei` has a reference type, the type `U` used for inference depends on the null state of `Ei` as well as its declared type:</span></span>
- <span data-ttu-id="5414e-313">Se il tipo dichiarato è un tipo di riferimento non null `U0` o un tipo di riferimento Nullable, `U0?`</span><span class="sxs-lookup"><span data-stu-id="5414e-313">If the declared type is a nonnullable reference type `U0` or a nullable reference type `U0?` then</span></span>
    - <span data-ttu-id="5414e-314">Se lo stato null di `Ei` è "not null", `U` sarà `U0`</span><span class="sxs-lookup"><span data-stu-id="5414e-314">if the null state of `Ei` is "not null" then `U` is `U0`</span></span>
    - <span data-ttu-id="5414e-315">Se lo stato null di `Ei` è "Maybe null", `U` sarà `U0?`</span><span class="sxs-lookup"><span data-stu-id="5414e-315">if the null state of `Ei` is "maybe null" then `U` is `U0?`</span></span>
- <span data-ttu-id="5414e-316">In caso contrario `Ei` , se ha un tipo dichiarato, `U` è quel tipo</span><span class="sxs-lookup"><span data-stu-id="5414e-316">Otherwise if `Ei` has a declared type, `U` is that type</span></span>
- <span data-ttu-id="5414e-317">In caso contrario, se `Ei` è `null` quindi `U` il limite speciale `null`</span><span class="sxs-lookup"><span data-stu-id="5414e-317">Otherwise if `Ei` is `null` then `U` is the special bound `null`</span></span>
- <span data-ttu-id="5414e-318">In caso contrario, se `Ei` è `default` quindi `U` il limite speciale `default`</span><span class="sxs-lookup"><span data-stu-id="5414e-318">Otherwise if `Ei` is `default` then `U` is the special bound `default`</span></span>
- <span data-ttu-id="5414e-319">In caso contrario, non viene eseguita alcuna inferenza.</span><span class="sxs-lookup"><span data-stu-id="5414e-319">Otherwise no inference is made.</span></span>

### <a name="exact-upper-bound-and-lower-bound-inferences"></a><span data-ttu-id="5414e-320">Inferenze esatte, con associazione superiore e con associazione inferiore</span><span class="sxs-lookup"><span data-stu-id="5414e-320">Exact, upper-bound and lower-bound inferences</span></span>

<span data-ttu-id="5414e-321">In inferenza *dal* tipo `U` *al* tipo `V` , se `V` è un tipo di riferimento Nullable `V0?` , `V0` viene usato anziché `V` nelle clausole seguenti.</span><span class="sxs-lookup"><span data-stu-id="5414e-321">In inferences *from* the type `U` *to* the type `V`, if `V` is a nullable reference type `V0?`, then `V0` is used instead of `V` in the following clauses.</span></span>
- <span data-ttu-id="5414e-322">Se `V` è una delle variabili di tipo non fixed, `U` viene aggiunto come limite esatto, superiore o inferiore come prima</span><span class="sxs-lookup"><span data-stu-id="5414e-322">If `V` is one of the unfixed type variables, `U` is added as an exact, upper or lower bound as before</span></span>
- <span data-ttu-id="5414e-323">In caso contrario, se `U` è `null` o `default` , non viene eseguita alcuna inferenza</span><span class="sxs-lookup"><span data-stu-id="5414e-323">Otherwise, if `U` is `null` or `default`, no inference is made</span></span>
- <span data-ttu-id="5414e-324">In caso contrario, se `U` è un tipo `U0?` di riferimento Nullable, `U0` viene utilizzato anziché `U` nelle clausole successive.</span><span class="sxs-lookup"><span data-stu-id="5414e-324">Otherwise, if `U` is a nullable reference type `U0?`, then `U0` is used instead of `U` in the subsequent clauses.</span></span>

<span data-ttu-id="5414e-325">L'essenza è che i valori null che riguardano direttamente una delle variabili di tipo non fisso vengono conservati nei limiti.</span><span class="sxs-lookup"><span data-stu-id="5414e-325">The essence is that nullability that pertains directly to one of the unfixed type variables is preserved into its bounds.</span></span> <span data-ttu-id="5414e-326">Per gli inferenza che rimaledicono ulteriormente i tipi di origine e di destinazione, d'altra parte, il supporto dei valori null viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="5414e-326">For the inferences that recurse further into the source and target types, on the other hand, nullability is ignored.</span></span> <span data-ttu-id="5414e-327">Può corrispondere o meno, ma in caso contrario, verrà emesso un avviso in un secondo momento se viene scelto e applicato l'overload.</span><span class="sxs-lookup"><span data-stu-id="5414e-327">It may or may not match, but if it doesn't, a warning will be issued later if the overload is chosen and applied.</span></span>

### <a name="fixing"></a><span data-ttu-id="5414e-328">correzione di </span><span class="sxs-lookup"><span data-stu-id="5414e-328">Fixing</span></span>

<span data-ttu-id="5414e-329">La specifica attualmente non esegue un processo valido per descrivere cosa accade quando più limiti sono convertibili tra loro identità, ma sono diversi.</span><span class="sxs-lookup"><span data-stu-id="5414e-329">The spec currently does not do a good job of describing what happens when multiple bounds are identity convertible to each other, but are different.</span></span> <span data-ttu-id="5414e-330">Questo problema può verificarsi tra `object` e `dynamic` , tra i tipi di tupla che differiscono solo per i nomi di elementi, tra i tipi costruiti e ora anche tra `C` e `C?` per i tipi di riferimento.</span><span class="sxs-lookup"><span data-stu-id="5414e-330">This may happen between `object` and `dynamic`, between tuple types that differ only in element names, between types constructed thereof and now also between `C` and `C?` for reference types.</span></span>

<span data-ttu-id="5414e-331">È inoltre necessario propagare "null" dalle espressioni di input al tipo di risultato.</span><span class="sxs-lookup"><span data-stu-id="5414e-331">In addition we need to propagate "nullness" from the input expressions to the result type.</span></span>

<span data-ttu-id="5414e-332">Per gestire queste operazioni, è necessario aggiungere altre fasi per la correzione, che ora è:</span><span class="sxs-lookup"><span data-stu-id="5414e-332">To handle these we add more phases to fixing, which is now:</span></span>

1. <span data-ttu-id="5414e-333">Raccogliere tutti i tipi in tutti i limiti come candidati, rimuovendo `?` da tutti i tipi di riferimento Nullable</span><span class="sxs-lookup"><span data-stu-id="5414e-333">Gather all the types in all the bounds as candidates, removing `?` from all that are nullable reference types</span></span>
2. <span data-ttu-id="5414e-334">Elimina i candidati in base ai requisiti dei limiti esatti, inferiore e superiore (mantenimento `null` e `default` limiti)</span><span class="sxs-lookup"><span data-stu-id="5414e-334">Eliminate candidates based on requirements of exact, lower and upper bounds (keeping `null` and `default` bounds)</span></span>
3. <span data-ttu-id="5414e-335">Elimina i candidati che non dispongono di una conversione implicita a tutti gli altri candidati</span><span class="sxs-lookup"><span data-stu-id="5414e-335">Eliminate candidates that do not have an implicit conversion to all the other candidates</span></span>
4. <span data-ttu-id="5414e-336">Se i candidati rimanenti non hanno tutte le conversioni di identità tra loro, l'inferenza del tipo ha esito negativo</span><span class="sxs-lookup"><span data-stu-id="5414e-336">If the remaining candidates do not all have identity conversions to one another, then type inference fails</span></span>
5. <span data-ttu-id="5414e-337">*Unisci* i candidati rimanenti come descritto di seguito</span><span class="sxs-lookup"><span data-stu-id="5414e-337">*Merge* the remaining candidates as described below</span></span>
6. <span data-ttu-id="5414e-338">Se il candidato risultante è un tipo di riferimento o un tipo di valore non null e *tutti* i limiti esatti o *uno* dei limiti inferiori sono tipi di valore Nullable, tipi di riferimento Nullable `null` o `default` , `?` viene aggiunto al candidato risultante, rendendolo un tipo di valore o un tipo di riferimento Nullable.</span><span class="sxs-lookup"><span data-stu-id="5414e-338">If the resulting candidate is a reference type or a nonnullable value type and *all* of the exact bounds or *any* of the lower bounds are nullable value types, nullable reference types, `null` or `default`, then `?` is added to the resulting candidate, making it a nullable value type or reference type.</span></span>

<span data-ttu-id="5414e-339">L' *Unione* viene descritta tra due tipi candidati.</span><span class="sxs-lookup"><span data-stu-id="5414e-339">*Merging* is described between two candidate types.</span></span> <span data-ttu-id="5414e-340">Si tratta di transitive e commutative, quindi i candidati possono essere Uniti in qualsiasi ordine con lo stesso risultato finale.</span><span class="sxs-lookup"><span data-stu-id="5414e-340">It is transitive and commutative, so the candidates can be merged in any order with the same ultimate result.</span></span> <span data-ttu-id="5414e-341">Non è definito se i due tipi candidati non sono convertibili in identità.</span><span class="sxs-lookup"><span data-stu-id="5414e-341">It is undefined if the two candidate types are not identity convertible to each other.</span></span>

<span data-ttu-id="5414e-342">La funzione *merge* accetta due tipi candidati e una direzione ( *+* o *-* ):</span><span class="sxs-lookup"><span data-stu-id="5414e-342">The *Merge* function takes two candidate types and a direction (*+* or *-*):</span></span>

- <span data-ttu-id="5414e-343">*Unisci*( `T` , `T` , *d*) = T</span><span class="sxs-lookup"><span data-stu-id="5414e-343">*Merge*(`T`, `T`, *d*) = T</span></span>
- <span data-ttu-id="5414e-344">*Merge*( `S` , `T?` , *+* ) = *merge*( `S?` , `T` , *+* ) = *merge*( `S` , `T` , *+* )`?`</span><span class="sxs-lookup"><span data-stu-id="5414e-344">*Merge*(`S`, `T?`, *+*) = *Merge*(`S?`, `T`, *+*) = *Merge*(`S`, `T`, *+*)`?`</span></span>
- <span data-ttu-id="5414e-345">*Merge*( `S` , `T?` , *-* ) = *merge*( `S?` , `T` , *-* ) = *merge*( `S` , `T` , *-* )</span><span class="sxs-lookup"><span data-stu-id="5414e-345">*Merge*(`S`, `T?`, *-*) = *Merge*(`S?`, `T`, *-*) = *Merge*(`S`, `T`, *-*)</span></span>
- <span data-ttu-id="5414e-346">*Merge*( `C<S1,...,Sn>` , `C<T1,...,Tn>` , *+* ) = `C<` *merge*( `S1` , `T1` , *D1*) `,...,` *merge*( `Sn` , `Tn` , *DN*) `>` , *dove*</span><span class="sxs-lookup"><span data-stu-id="5414e-346">*Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *+*) = `C<`*Merge*(`S1`, `T1`, *d1*)`,...,`*Merge*(`Sn`, `Tn`, *dn*)`>`, *where*</span></span>
    - <span data-ttu-id="5414e-347">`di` = *+* Se il `i` parametro di tipo ' th di `C<...>` è covariante</span><span class="sxs-lookup"><span data-stu-id="5414e-347">`di` = *+* if the `i`'th type parameter of `C<...>` is covariant</span></span>
    - <span data-ttu-id="5414e-348">`di` = *-* Se il `i` parametro di tipo ' th di `C<...>` è contra o invariante</span><span class="sxs-lookup"><span data-stu-id="5414e-348">`di` = *-* if the `i`'th type parameter of `C<...>` is contra- or invariant</span></span>
- <span data-ttu-id="5414e-349">*Merge*( `C<S1,...,Sn>` , `C<T1,...,Tn>` , *-* ) = `C<` *merge*( `S1` , `T1` , *D1*) `,...,` *merge*( `Sn` , `Tn` , *DN*) `>` , *dove*</span><span class="sxs-lookup"><span data-stu-id="5414e-349">*Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *-*) = `C<`*Merge*(`S1`, `T1`, *d1*)`,...,`*Merge*(`Sn`, `Tn`, *dn*)`>`, *where*</span></span>
    - <span data-ttu-id="5414e-350">`di` = *-* Se il `i` parametro di tipo ' th di `C<...>` è covariante</span><span class="sxs-lookup"><span data-stu-id="5414e-350">`di` = *-* if the `i`'th type parameter of `C<...>` is covariant</span></span>
    - <span data-ttu-id="5414e-351">`di` = *+* Se il `i` parametro di tipo ' th di `C<...>` è contra o invariante</span><span class="sxs-lookup"><span data-stu-id="5414e-351">`di` = *+* if the `i`'th type parameter of `C<...>` is contra- or invariant</span></span>
- <span data-ttu-id="5414e-352">*Merge*( `(S1 s1,..., Sn sn)` , `(T1 t1,..., Tn tn)` , *d*) = `(` *merge*( `S1` , `T1` , *d*) `n1,...,` *merge*( `Sn` , `Tn` , *d*) `nn)` , *dove*</span><span class="sxs-lookup"><span data-stu-id="5414e-352">*Merge*(`(S1 s1,..., Sn sn)`, `(T1 t1,..., Tn tn)`, *d*) = `(`*Merge*(`S1`, `T1`, *d*)`n1,...,`*Merge*(`Sn`, `Tn`, *d*) `nn)`, *where*</span></span>
    - <span data-ttu-id="5414e-353">`ni` è assente se `si` e `ti` differiscono o se entrambi sono assenti</span><span class="sxs-lookup"><span data-stu-id="5414e-353">`ni` is absent if `si` and `ti` differ, or if both are absent</span></span>
    - <span data-ttu-id="5414e-354">`ni``si`se `si` e `ti` sono uguali</span><span class="sxs-lookup"><span data-stu-id="5414e-354">`ni` is `si` if `si` and `ti` are the same</span></span>
- <span data-ttu-id="5414e-355">*Merge*( `object` , `dynamic` ) = *merge*( `dynamic` , `object` ) = `dynamic`</span><span class="sxs-lookup"><span data-stu-id="5414e-355">*Merge*(`object`, `dynamic`) = *Merge*(`dynamic`, `object`) = `dynamic`</span></span>

## <a name="warnings"></a><span data-ttu-id="5414e-356">Avvisi</span><span class="sxs-lookup"><span data-stu-id="5414e-356">Warnings</span></span>

### <a name="potential-null-assignment"></a><span data-ttu-id="5414e-357">Potenziale assegnazione null</span><span class="sxs-lookup"><span data-stu-id="5414e-357">Potential null assignment</span></span>

### <a name="potential-null-dereference"></a><span data-ttu-id="5414e-358">Potenziale dereferenziazione null</span><span class="sxs-lookup"><span data-stu-id="5414e-358">Potential null dereference</span></span>

### <a name="constraint-nullability-mismatch"></a><span data-ttu-id="5414e-359">Mancata corrispondenza dei valori null per i vincoli</span><span class="sxs-lookup"><span data-stu-id="5414e-359">Constraint nullability mismatch</span></span>

### <a name="nullable-types-in-disabled-annotation-context"></a><span data-ttu-id="5414e-360">Tipi nullable nel contesto di annotazione disabilitato</span><span class="sxs-lookup"><span data-stu-id="5414e-360">Nullable types in disabled annotation context</span></span>

### <a name="override-and-implementation-nullability-mismatch"></a><span data-ttu-id="5414e-361">Override e implementazione del supporto dei valori null non corrispondenti</span><span class="sxs-lookup"><span data-stu-id="5414e-361">Override and implementation nullability mismatch</span></span>

## <a name="attributes-for-special-null-behavior"></a><span data-ttu-id="5414e-362">Attributi per un comportamento null speciale</span><span class="sxs-lookup"><span data-stu-id="5414e-362">Attributes for special null behavior</span></span>

