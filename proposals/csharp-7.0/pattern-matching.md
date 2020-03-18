---
ms.openlocfilehash: 3df21c5816be90387a6cd9242e99ba11f43dfd1c
ms.sourcegitcommit: f61a06970fa0562d2e40363fae3948eb168624ca
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2020
ms.locfileid: "79485168"
---
# <a name="pattern-matching-for-c-7"></a><span data-ttu-id="fafc5-101">Criteri di ricerca C# per 7</span><span class="sxs-lookup"><span data-stu-id="fafc5-101">Pattern Matching for C# 7</span></span>

<span data-ttu-id="fafc5-102">Le estensioni di criteri C# di ricerca per consentono molti dei vantaggi dei tipi di dati algebrici e dei criteri di ricerca dai linguaggi funzionali, ma in modo da integrarsi senza problemi con il linguaggio sottostante.</span><span class="sxs-lookup"><span data-stu-id="fafc5-102">Pattern matching extensions for C# enable many of the benefits of algebraic data types and pattern matching from functional languages, but in a way that smoothly integrates with the feel of the underlying language.</span></span> <span data-ttu-id="fafc5-103">Le funzionalità di base sono: i [tipi di record](https://github.com/dotnet/csharplang/blob/master/proposals/records.md), ovvero i tipi il cui significato semantico è descritto dalla forma dei dati; e criteri di ricerca, ovvero un nuovo form di espressione che consente la scomposizione multilivello estremamente concisa di questi tipi di dati.</span><span class="sxs-lookup"><span data-stu-id="fafc5-103">The basic features are: [record types](https://github.com/dotnet/csharplang/blob/master/proposals/records.md), which are types whose semantic meaning is described by the shape of the data; and pattern matching, which is a new expression form that enables extremely concise multilevel decomposition of these data types.</span></span> <span data-ttu-id="fafc5-104">Gli elementi di questo approccio sono ispirati dalle funzionalità correlate nei linguaggi [F#](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/p29-syme.pdf "Criteri di ricerca estendibili tramite un linguaggio leggero") di programmazione e in [scala](https://infoscience.epfl.ch/record/98468/files/MatchingObjectsWithPatterns-TR.pdf "Corrispondenza di oggetti con modelli").</span><span class="sxs-lookup"><span data-stu-id="fafc5-104">Elements of this approach are inspired by related features in the programming languages [F#](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/p29-syme.pdf "Extensible Pattern Matching Via a Lightweight Language") and [Scala](https://infoscience.epfl.ch/record/98468/files/MatchingObjectsWithPatterns-TR.pdf "Matching Objects With Patterns").</span></span>

## <a name="is-expression"></a><span data-ttu-id="fafc5-105">Espressione is</span><span class="sxs-lookup"><span data-stu-id="fafc5-105">Is expression</span></span>

<span data-ttu-id="fafc5-106">L'operatore `is` viene esteso per testare un'espressione rispetto a un *criterio*.</span><span class="sxs-lookup"><span data-stu-id="fafc5-106">The `is` operator is extended to test an expression against a *pattern*.</span></span>

```antlr
relational_expression
    : relational_expression 'is' pattern
    ;
```

<span data-ttu-id="fafc5-107">Questa forma di *relational_expression* è aggiunta ai moduli esistenti nella C# specifica.</span><span class="sxs-lookup"><span data-stu-id="fafc5-107">This form of *relational_expression* is in addition to the existing forms in the C# specification.</span></span> <span data-ttu-id="fafc5-108">Si tratta di un errore in fase di compilazione se il *relational_expression* a sinistra del token `is` non designa un valore o non dispone di un tipo.</span><span class="sxs-lookup"><span data-stu-id="fafc5-108">It is a compile-time error if the *relational_expression* to the left of the `is` token does not designate a value or does not have a type.</span></span>

<span data-ttu-id="fafc5-109">Ogni *identificatore* del modello introduce una nuova variabile locale che viene *assegnata definitivamente* dopo che l'operatore `is` è stato `true` (ovvero *assegnato definitivamente quando è true*).</span><span class="sxs-lookup"><span data-stu-id="fafc5-109">Every *identifier* of the pattern introduces a new local variable that is *definitely assigned* after the `is` operator is `true` (i.e. *definitely assigned when true*).</span></span>

> <span data-ttu-id="fafc5-110">Nota: esiste tecnicamente un'ambiguità tra il *tipo* in una `is-expression` e *constant_pattern*, una delle quali potrebbe essere un'analisi valida di un identificatore qualificato.</span><span class="sxs-lookup"><span data-stu-id="fafc5-110">Note: There is technically an ambiguity between *type* in an `is-expression` and *constant_pattern*, either of which might be a valid parse of a qualified identifier.</span></span> <span data-ttu-id="fafc5-111">Si tenta di associarlo come tipo per la compatibilità con le versioni precedenti del linguaggio; solo se l'operazione ha esito negativo, viene risolta come in altri contesti, fino alla prima cosa trovata (che deve essere una costante o un tipo).</span><span class="sxs-lookup"><span data-stu-id="fafc5-111">We try to bind it as a type for compatibility with previous versions of the language; only if that fails do we resolve it as we do in other contexts, to the first thing found (which must be either a constant or a type).</span></span> <span data-ttu-id="fafc5-112">Questa ambiguità è presente solo sul lato destro di un'espressione `is`.</span><span class="sxs-lookup"><span data-stu-id="fafc5-112">This ambiguity is only present on the right-hand-side of an `is` expression.</span></span>

## <a name="patterns"></a><span data-ttu-id="fafc5-113">Modelli</span><span class="sxs-lookup"><span data-stu-id="fafc5-113">Patterns</span></span>

<span data-ttu-id="fafc5-114">Gli schemi vengono usati nell'operatore `is` e in un *switch_Statement* per esprimere la forma dei dati in base ai quali confrontare i dati in ingresso.</span><span class="sxs-lookup"><span data-stu-id="fafc5-114">Patterns are used in the `is` operator and in a *switch_statement* to express the shape of data against which incoming data is to be compared.</span></span> <span data-ttu-id="fafc5-115">I modelli possono essere ricorsivi in modo che sia possibile trovare una corrispondenza tra parti dei dati e modelli secondari.</span><span class="sxs-lookup"><span data-stu-id="fafc5-115">Patterns may be recursive so that parts of the data may be matched against sub-patterns.</span></span>

```antlr
pattern
    : declaration_pattern
    | constant_pattern
    | var_pattern
    ;

declaration_pattern
    : type simple_designation
    ;

constant_pattern
    : shift_expression
    ;

var_pattern
    : 'var' simple_designation
    ;
```

> <span data-ttu-id="fafc5-116">Nota: esiste tecnicamente un'ambiguità tra il *tipo* in una `is-expression` e *constant_pattern*, una delle quali potrebbe essere un'analisi valida di un identificatore qualificato.</span><span class="sxs-lookup"><span data-stu-id="fafc5-116">Note: There is technically an ambiguity between *type* in an `is-expression` and *constant_pattern*, either of which might be a valid parse of a qualified identifier.</span></span> <span data-ttu-id="fafc5-117">Si tenta di associarlo come tipo per la compatibilità con le versioni precedenti del linguaggio; solo se l'operazione ha esito negativo, viene risolta come in altri contesti, fino alla prima cosa trovata (che deve essere una costante o un tipo).</span><span class="sxs-lookup"><span data-stu-id="fafc5-117">We try to bind it as a type for compatibility with previous versions of the language; only if that fails do we resolve it as we do in other contexts, to the first thing found (which must be either a constant or a type).</span></span> <span data-ttu-id="fafc5-118">Questa ambiguità è presente solo sul lato destro di un'espressione `is`.</span><span class="sxs-lookup"><span data-stu-id="fafc5-118">This ambiguity is only present on the right-hand-side of an `is` expression.</span></span>

### <a name="declaration-pattern"></a><span data-ttu-id="fafc5-119">Modello di dichiarazione</span><span class="sxs-lookup"><span data-stu-id="fafc5-119">Declaration pattern</span></span>

<span data-ttu-id="fafc5-120">Il *declaration_pattern* verifica che un'espressione sia di un tipo specificato e ne esegue il cast a tale tipo se il test ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="fafc5-120">The *declaration_pattern* both tests that an expression is of a given type and casts it to that type if the test succeeds.</span></span> <span data-ttu-id="fafc5-121">Se il *simple_designation* è un identificatore, introduce una variabile locale del tipo specificato denominato dall'identificatore specificato.</span><span class="sxs-lookup"><span data-stu-id="fafc5-121">If the *simple_designation* is an identifier, it introduces a local variable of the given type named by the given identifier.</span></span> <span data-ttu-id="fafc5-122">La variabile locale viene *assegnata definitivamente* quando il risultato dell'operazione di corrispondenza dei modelli è true.</span><span class="sxs-lookup"><span data-stu-id="fafc5-122">That local variable is *definitely assigned* when the result of the pattern-matching operation is true.</span></span>

```antlr
declaration_pattern
    : type simple_designation
    ;
```

<span data-ttu-id="fafc5-123">La semantica di runtime di questa espressione è che testa il tipo di runtime dell'operando di sinistra *relational_expression* sul *tipo* nel modello.</span><span class="sxs-lookup"><span data-stu-id="fafc5-123">The runtime semantic of this expression is that it tests the runtime type of the left-hand *relational_expression* operand against the *type* in the pattern.</span></span> <span data-ttu-id="fafc5-124">Se è di quel tipo di runtime (o di un sottotipo), il risultato della `is operator` viene `true`.</span><span class="sxs-lookup"><span data-stu-id="fafc5-124">If it is of that runtime type (or some subtype), the result of the `is operator` is `true`.</span></span> <span data-ttu-id="fafc5-125">Dichiara una nuova variabile locale denominata dall' *identificatore* a cui viene assegnato il valore dell'operando sinistro quando il risultato viene `true`.</span><span class="sxs-lookup"><span data-stu-id="fafc5-125">It declares a new local variable named by the *identifier* that is assigned the value of the left-hand operand when the result is `true`.</span></span>

<span data-ttu-id="fafc5-126">Alcune combinazioni di tipo statico del lato sinistro e del tipo specificato vengono considerate incompatibili e generano un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="fafc5-126">Certain combinations of static type of the left-hand-side and the given type are considered incompatible and result in compile-time error.</span></span> <span data-ttu-id="fafc5-127">Un valore di tipo statico `E` viene definito *modello compatibile* con il tipo `T` se esiste una conversione di identità, una conversione di un riferimento implicito, una conversione boxing, una conversione di riferimento esplicita o una conversione unboxing da `E` a `T`.</span><span class="sxs-lookup"><span data-stu-id="fafc5-127">A value of static type `E` is said to be *pattern compatible* with the type `T` if there exists an identity conversion, an implicit reference conversion, a boxing conversion, an explicit reference conversion, or an unboxing conversion from `E` to `T`.</span></span> <span data-ttu-id="fafc5-128">Si tratta di un errore in fase di compilazione se un'espressione di tipo `E` non è compatibile con il tipo in un modello di tipo con cui corrisponde.</span><span class="sxs-lookup"><span data-stu-id="fafc5-128">It is a compile-time error if an expression of type `E` is not pattern compatible with the type in a type pattern that it is matched with.</span></span>

> <span data-ttu-id="fafc5-129">Nota: [In C# 7,1 questa](../csharp-7.1/generics-pattern-match.md) operazione viene estesa per consentire un'operazione di corrispondenza dei modelli se il tipo di input o il tipo `T` è un tipo aperto.</span><span class="sxs-lookup"><span data-stu-id="fafc5-129">Note: [In C# 7.1 we extend this](../csharp-7.1/generics-pattern-match.md) to permit a pattern-matching operation if either the input type or the type `T` is an open type.</span></span> <span data-ttu-id="fafc5-130">Questo paragrafo viene sostituito da quanto segue:</span><span class="sxs-lookup"><span data-stu-id="fafc5-130">This paragraph is replaced by the following:</span></span>
> 
> <span data-ttu-id="fafc5-131">Alcune combinazioni di tipo statico del lato sinistro e del tipo specificato vengono considerate incompatibili e generano un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="fafc5-131">Certain combinations of static type of the left-hand-side and the given type are considered incompatible and result in compile-time error.</span></span> <span data-ttu-id="fafc5-132">Un valore di tipo statico `E` viene definito *modello compatibile* con il tipo `T` se esiste una conversione di identità, una conversione di un riferimento implicito, una conversione boxing, una conversione di riferimento esplicita o una conversione unboxing da `E` a `T`**oppure se `E` o `T` è un tipo aperto**.</span><span class="sxs-lookup"><span data-stu-id="fafc5-132">A value of static type `E` is said to be *pattern compatible* with the type `T` if there exists an identity conversion, an implicit reference conversion, a boxing conversion, an explicit reference conversion, or an unboxing conversion from `E` to `T`, **or if either `E` or `T` is an open type**.</span></span> <span data-ttu-id="fafc5-133">Si tratta di un errore in fase di compilazione se un'espressione di tipo `E` non è compatibile con il tipo in un modello di tipo con cui corrisponde.</span><span class="sxs-lookup"><span data-stu-id="fafc5-133">It is a compile-time error if an expression of type `E` is not pattern compatible with the type in a type pattern that it is matched with.</span></span>

<span data-ttu-id="fafc5-134">Il modello di dichiarazione è utile per l'esecuzione di test dei tipi di riferimento in fase di esecuzione e sostituisce l'idioma</span><span class="sxs-lookup"><span data-stu-id="fafc5-134">The declaration pattern is useful for performing run-time type tests of reference types, and replaces the idiom</span></span>

```csharp
var v = expr as Type;
if (v != null) { // code using v }
```

<span data-ttu-id="fafc5-135">Con l'aspetto leggermente più conciso</span><span class="sxs-lookup"><span data-stu-id="fafc5-135">With the slightly more concise</span></span>

```csharp
if (expr is Type v) { // code using v }
```

<span data-ttu-id="fafc5-136">Si tratta di un errore se il *tipo* è un tipo di valore Nullable.</span><span class="sxs-lookup"><span data-stu-id="fafc5-136">It is an error if *type* is a nullable value type.</span></span>

<span data-ttu-id="fafc5-137">Il modello di dichiarazione può essere usato per testare i valori dei tipi nullable: un valore di tipo `Nullable<T>` (o un `T`boxed) corrisponde a un modello di tipo `T2 id` se il valore è diverso da null e il tipo di `T2` è `T`o un tipo di base o un'interfaccia di `T`.</span><span class="sxs-lookup"><span data-stu-id="fafc5-137">The declaration pattern can be used to test values of nullable types: a value of type `Nullable<T>` (or a boxed `T`) matches a type pattern `T2 id` if the value is non-null and the type of `T2` is `T`, or some base type or interface of `T`.</span></span> <span data-ttu-id="fafc5-138">Ad esempio, nel frammento di codice</span><span class="sxs-lookup"><span data-stu-id="fafc5-138">For example, in the code fragment</span></span>

```csharp
int? x = 3;
if (x is int v) { // code using v }
```

<span data-ttu-id="fafc5-139">La condizione dell'istruzione `if` viene `true` in fase di esecuzione e la variabile `v` include il valore `3` di tipo `int` all'interno del blocco.</span><span class="sxs-lookup"><span data-stu-id="fafc5-139">The condition of the `if` statement is `true` at runtime and the variable `v` holds the value `3` of type `int` inside the block.</span></span>

### <a name="constant-pattern"></a><span data-ttu-id="fafc5-140">Criterio costante</span><span class="sxs-lookup"><span data-stu-id="fafc5-140">Constant pattern</span></span>

```antlr
constant_pattern
    : shift_expression
    ;
```

<span data-ttu-id="fafc5-141">Un criterio costante verifica il valore di un'espressione rispetto a un valore costante.</span><span class="sxs-lookup"><span data-stu-id="fafc5-141">A constant pattern tests the value of an expression against a constant value.</span></span> <span data-ttu-id="fafc5-142">La costante può essere qualsiasi espressione costante, ad esempio un valore letterale, il nome di una variabile `const` dichiarata o una costante di enumerazione o un'espressione `typeof`.</span><span class="sxs-lookup"><span data-stu-id="fafc5-142">The constant may be any constant expression, such as a literal, the name of a declared `const` variable, or an enumeration constant, or a `typeof` expression.</span></span>

<span data-ttu-id="fafc5-143">Se e *e* *c* sono di tipo integrale, il modello viene considerato corrispondente se il risultato dell'espressione `e == c` è `true`.</span><span class="sxs-lookup"><span data-stu-id="fafc5-143">If both *e* and *c* are of integral types, the pattern is considered matched if the result of the expression `e == c` is `true`.</span></span>

<span data-ttu-id="fafc5-144">In caso contrario, il modello viene considerato corrispondente se `object.Equals(e, c)` restituisce `true`.</span><span class="sxs-lookup"><span data-stu-id="fafc5-144">Otherwise the pattern is considered matching if `object.Equals(e, c)` returns `true`.</span></span> <span data-ttu-id="fafc5-145">In questo caso si tratta di un errore in fase di compilazione se il tipo statico di *e* non è *compatibile* con il tipo della costante.</span><span class="sxs-lookup"><span data-stu-id="fafc5-145">In this case it is a compile-time error if the static type of *e* is not *pattern compatible* with the type of the constant.</span></span>

### <a name="var-pattern"></a><span data-ttu-id="fafc5-146">Modello var</span><span class="sxs-lookup"><span data-stu-id="fafc5-146">Var pattern</span></span>

```antlr
var_pattern
    : 'var' simple_designation
    ;
```

<span data-ttu-id="fafc5-147">Un'espressione *e* corrisponde a un *var_pattern* sempre.</span><span class="sxs-lookup"><span data-stu-id="fafc5-147">An expression *e* matches a *var_pattern* always.</span></span> <span data-ttu-id="fafc5-148">In altre parole, una corrispondenza con un *modello var* ha sempre esito positivo.</span><span class="sxs-lookup"><span data-stu-id="fafc5-148">In other words, a match to a *var pattern* always succeeds.</span></span> <span data-ttu-id="fafc5-149">Se il *simple_designation* è un identificatore, in fase di esecuzione il valore di *e* viene associato a una variabile locale appena introdotta.</span><span class="sxs-lookup"><span data-stu-id="fafc5-149">If the *simple_designation* is an identifier, then at runtime the value of *e* is bound to a newly introduced local variable.</span></span> <span data-ttu-id="fafc5-150">Il tipo della variabile locale è il tipo statico di *e*.</span><span class="sxs-lookup"><span data-stu-id="fafc5-150">The type of the local variable is the static type of *e*.</span></span>

<span data-ttu-id="fafc5-151">Si tratta di un errore se il nome `var` viene associato a un tipo.</span><span class="sxs-lookup"><span data-stu-id="fafc5-151">It is an error if the name `var` binds to a type.</span></span>

## <a name="switch-statement"></a><span data-ttu-id="fafc5-152">Istruzioni switch</span><span class="sxs-lookup"><span data-stu-id="fafc5-152">Switch statement</span></span>

<span data-ttu-id="fafc5-153">L'istruzione `switch` viene estesa in modo da selezionare per l'esecuzione il primo blocco a cui è associato un modello corrispondente all' *espressione switch*.</span><span class="sxs-lookup"><span data-stu-id="fafc5-153">The `switch` statement is extended to select for execution the first block having an associated pattern that matches the *switch expression*.</span></span>

```antlr
switch_label
    : 'case' complex_pattern case_guard? ':'
    | 'case' constant_expression case_guard? ':'
    | 'default' ':'
    ;

case_guard
    : 'when' expression
    ;
```

<span data-ttu-id="fafc5-154">L'ordine in cui vengono confrontati i modelli non è definito.</span><span class="sxs-lookup"><span data-stu-id="fafc5-154">The order in which patterns are matched is not defined.</span></span> <span data-ttu-id="fafc5-155">Un compilatore è autorizzato a trovare una corrispondenza con i modelli non ordinati e a riutilizzare i risultati dei modelli già corrispondenti per calcolare il risultato della corrispondenza di altri criteri.</span><span class="sxs-lookup"><span data-stu-id="fafc5-155">A compiler is permitted to match patterns out of order, and to reuse the results of already matched patterns to compute the result of matching of other patterns.</span></span>

<span data-ttu-id="fafc5-156">Se è presente un *case-Guard* , l'espressione è di tipo `bool`.</span><span class="sxs-lookup"><span data-stu-id="fafc5-156">If a *case-guard* is present, its expression is of type `bool`.</span></span> <span data-ttu-id="fafc5-157">Viene valutato come una condizione aggiuntiva che deve essere soddisfatta affinché il case venga considerato soddisfatto.</span><span class="sxs-lookup"><span data-stu-id="fafc5-157">It is evaluated as an additional condition that must be satisfied for the case to be considered satisfied.</span></span>

<span data-ttu-id="fafc5-158">Se un *switch_label* non può avere alcun effetto in fase di esecuzione, non è un errore perché il relativo modello viene sostituito dai case precedenti.</span><span class="sxs-lookup"><span data-stu-id="fafc5-158">It is an error if a *switch_label* can have no effect at runtime because its pattern is subsumed by previous cases.</span></span> <span data-ttu-id="fafc5-159">[TODO: è necessario essere più precisi sulle tecniche che il compilatore deve usare per raggiungere questo giudizio.]</span><span class="sxs-lookup"><span data-stu-id="fafc5-159">[TODO: We should be more precise about the techniques the compiler is required to use to reach this judgment.]</span></span>

<span data-ttu-id="fafc5-160">Una variabile di pattern dichiarata in un *switch_label* viene assegnata in modo sicuro nel blocco case solo se il blocco case contiene esattamente una *switch_label*.</span><span class="sxs-lookup"><span data-stu-id="fafc5-160">A pattern variable declared in a *switch_label* is definitely assigned in its case block if and only if that case block contains precisely one *switch_label*.</span></span>

<span data-ttu-id="fafc5-161">[TODO: è necessario specificare quando un *blocco switch* è raggiungibile.]</span><span class="sxs-lookup"><span data-stu-id="fafc5-161">[TODO: We should specify when a *switch block* is reachable.]</span></span>

### <a name="scope-of-pattern-variables"></a><span data-ttu-id="fafc5-162">Ambito delle variabili del modello</span><span class="sxs-lookup"><span data-stu-id="fafc5-162">Scope of pattern variables</span></span>

<span data-ttu-id="fafc5-163">L'ambito di una variabile dichiarata in un modello è il seguente:</span><span class="sxs-lookup"><span data-stu-id="fafc5-163">The scope of a variable declared in a pattern is as follows:</span></span>

- <span data-ttu-id="fafc5-164">Se il modello è un'etichetta case, l'ambito della variabile è il *blocco case*.</span><span class="sxs-lookup"><span data-stu-id="fafc5-164">If the pattern is a case label, then the scope of the variable is the *case block*.</span></span>

<span data-ttu-id="fafc5-165">In caso contrario, la variabile viene dichiarata in un'espressione *is_pattern* e il relativo ambito è basato sul costrutto che racchiude immediatamente l'espressione contenente l'espressione *is_pattern* , come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="fafc5-165">Otherwise the variable is declared in an *is_pattern* expression, and its scope is based on the construct immediately enclosing the expression containing the *is_pattern* expression as follows:</span></span>

- <span data-ttu-id="fafc5-166">Se l'espressione è in un'espressione lambda con corpo di espressione, il relativo ambito è il corpo dell'espressione lambda.</span><span class="sxs-lookup"><span data-stu-id="fafc5-166">If the expression is in an expression-bodied lambda, its scope is the body of the lambda.</span></span>
- <span data-ttu-id="fafc5-167">Se l'espressione si trova in una proprietà o in un metodo con corpo di espressione, il relativo ambito è il corpo del metodo o della proprietà.</span><span class="sxs-lookup"><span data-stu-id="fafc5-167">If the expression is in an expression-bodied method or property, its scope is the body of the method or property.</span></span>
- <span data-ttu-id="fafc5-168">Se l'espressione si trova in una clausola `when` di una clausola `catch`, il suo ambito è che `catch` clausola.</span><span class="sxs-lookup"><span data-stu-id="fafc5-168">If the expression is in a `when` clause of a `catch` clause, its scope is that `catch` clause.</span></span>
- <span data-ttu-id="fafc5-169">Se l'espressione si trova in un *iteration_statement*, il suo ambito è semplicemente tale istruzione.</span><span class="sxs-lookup"><span data-stu-id="fafc5-169">If the expression is in an *iteration_statement*, its scope is just that statement.</span></span>
- <span data-ttu-id="fafc5-170">In caso contrario, se l'espressione è in un altro formato di istruzione, il relativo ambito è l'ambito che contiene l'istruzione.</span><span class="sxs-lookup"><span data-stu-id="fafc5-170">Otherwise if the expression is in some other statement form, its scope is the scope containing the statement.</span></span>

<span data-ttu-id="fafc5-171">Ai fini della determinazione dell'ambito, un *embedded_statement* viene considerato nel proprio ambito.</span><span class="sxs-lookup"><span data-stu-id="fafc5-171">For the purpose of determining the scope, an *embedded_statement* is considered to be in its own scope.</span></span> <span data-ttu-id="fafc5-172">Ad esempio, la grammatica per un *if_Statement* è</span><span class="sxs-lookup"><span data-stu-id="fafc5-172">For example, the grammar for an *if_statement* is</span></span>

``` antlr
if_statement
    : 'if' '(' boolean_expression ')' embedded_statement
    | 'if' '(' boolean_expression ')' embedded_statement 'else' embedded_statement
    ;
```

<span data-ttu-id="fafc5-173">Quindi, se l'istruzione controllata di un *if_Statement* dichiara una variabile di modello, il suo ambito è limitato a tale *embedded_statement*:</span><span class="sxs-lookup"><span data-stu-id="fafc5-173">So if the controlled statement of an *if_statement* declares a pattern variable, its scope is restricted to that *embedded_statement*:</span></span>

```csharp
if (x) M(y is var z);
```

<span data-ttu-id="fafc5-174">In questo caso l'ambito di `z` è l'istruzione incorporata `M(y is var z);`.</span><span class="sxs-lookup"><span data-stu-id="fafc5-174">In this case the scope of `z` is the embedded statement `M(y is var z);`.</span></span>

<span data-ttu-id="fafc5-175">Altri casi sono errori per altri motivi, ad esempio nel caso di un valore predefinito di un parametro o di un attributo, che sono entrambi un errore, perché tali contesti richiedono un'espressione costante.</span><span class="sxs-lookup"><span data-stu-id="fafc5-175">Other cases are errors for other reasons (e.g. in a parameter's default value or an attribute, both of which are an error because those contexts require a constant expression).</span></span>

> <span data-ttu-id="fafc5-176">[In C# 7,3 sono stati aggiunti i seguenti contesti](../csharp-7.3/expression-variables-in-initializers.md) in cui è possibile dichiarare una variabile di modello:</span><span class="sxs-lookup"><span data-stu-id="fafc5-176">[In C# 7.3 we added the following contexts](../csharp-7.3/expression-variables-in-initializers.md) in which a pattern variable may be declared:</span></span>
> - <span data-ttu-id="fafc5-177">Se l'espressione si trova in un *inizializzatore di Costruttore*, il relativo ambito è l' *inizializzatore del costruttore* e il corpo del costruttore.</span><span class="sxs-lookup"><span data-stu-id="fafc5-177">If the expression is in a *constructor initializer*, its scope is the *constructor initializer* and the constructor's body.</span></span>
> - <span data-ttu-id="fafc5-178">Se l'espressione si trova in un inizializzatore di campo, il relativo ambito è il *equals_value_clause* in cui viene visualizzato.</span><span class="sxs-lookup"><span data-stu-id="fafc5-178">If the expression is in a field initializer, its scope is the *equals_value_clause* in which it appears.</span></span>
> - <span data-ttu-id="fafc5-179">Se l'espressione si trova in una clausola di query specificata per essere convertita nel corpo di un'espressione lambda, il suo ambito è semplicemente tale espressione.</span><span class="sxs-lookup"><span data-stu-id="fafc5-179">If the expression is in a query clause that is specified to be translated into the body of a lambda, its scope is just that expression.</span></span>

## <a name="changes-to-syntactic-disambiguation"></a><span data-ttu-id="fafc5-180">Modifiche alla disambiguazione sintattica</span><span class="sxs-lookup"><span data-stu-id="fafc5-180">Changes to syntactic disambiguation</span></span>

<span data-ttu-id="fafc5-181">Esistono situazioni che coinvolgono generics in cui la C# grammatica è ambigua e la specifica del linguaggio spiega come risolvere le ambiguità seguenti:</span><span class="sxs-lookup"><span data-stu-id="fafc5-181">There are situations involving generics where the C# grammar is ambiguous, and the language spec says how to resolve those ambiguities:</span></span>

> #### <a name="7652-grammar-ambiguities"></a><span data-ttu-id="fafc5-182">ambiguità della grammatica 7.6.5.2</span><span class="sxs-lookup"><span data-stu-id="fafc5-182">7.6.5.2 Grammar ambiguities</span></span>
> <span data-ttu-id="fafc5-183">Le produzioni per il *nome semplice* (§ 7.6.3) e l' *accesso ai membri* (§ 7.6.5) possono dare luogo a ambiguità nella grammatica per le espressioni.</span><span class="sxs-lookup"><span data-stu-id="fafc5-183">The productions for *simple-name* (§7.6.3) and *member-access* (§7.6.5) can give rise to ambiguities in the grammar for expressions.</span></span> <span data-ttu-id="fafc5-184">Ad esempio, l'istruzione:</span><span class="sxs-lookup"><span data-stu-id="fafc5-184">For example, the statement:</span></span>
> ```csharp
> F(G<A,B>(7));
> ```
> <span data-ttu-id="fafc5-185">può essere interpretato come una chiamata a `F` con due argomenti, `G < A` e `B > (7)`.</span><span class="sxs-lookup"><span data-stu-id="fafc5-185">could be interpreted as a call to `F` with two arguments, `G < A` and `B > (7)`.</span></span> <span data-ttu-id="fafc5-186">In alternativa, può essere interpretato come una chiamata a `F` con un argomento, ovvero una chiamata a un metodo generico `G` con due argomenti di tipo e un argomento normale.</span><span class="sxs-lookup"><span data-stu-id="fafc5-186">Alternatively, it could be interpreted as a call to `F` with one argument, which is a call to a generic method `G` with two type arguments and one regular argument.</span></span>

> <span data-ttu-id="fafc5-187">Se una sequenza di token può essere analizzata (nel contesto) come *nome semplice* (§ 7.6.3), *accesso ai membri* (§ 7.6.5) o *puntatore-membro-Access* (§ 18.5.2) che termina con un *tipo-argument-list* (§ 4.4.1), il token che segue immediatamente il token di chiusura `>` viene esaminato.</span><span class="sxs-lookup"><span data-stu-id="fafc5-187">If a sequence of tokens can be parsed (in context) as a *simple-name* (§7.6.3), *member-access* (§7.6.5), or *pointer-member-access* (§18.5.2) ending with a *type-argument-list* (§4.4.1), the token immediately following the closing `>` token is examined.</span></span> <span data-ttu-id="fafc5-188">Se è uno dei</span><span class="sxs-lookup"><span data-stu-id="fafc5-188">If it is one of</span></span>
> ```none
> (  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
> ```
> <span data-ttu-id="fafc5-189">quindi, l' *elenco di argomenti di tipo* viene mantenuto come parte del *nome semplice*, l' *accesso ai membri* o l'accesso ai membri del *puntatore* e qualsiasi altra possibile analisi della sequenza di token viene scartata.</span><span class="sxs-lookup"><span data-stu-id="fafc5-189">then the *type-argument-list* is retained as part of the *simple-name*, *member-access* or *pointer-member-access* and any other possible parse of the sequence of tokens is discarded.</span></span> <span data-ttu-id="fafc5-190">In caso contrario, l' *elenco di argomenti di tipo* non viene considerato parte dell'accesso con *nome semplice*, *accesso ai membri* o > *puntatore al membro*, anche se non è possibile analizzare la sequenza di token.</span><span class="sxs-lookup"><span data-stu-id="fafc5-190">Otherwise, the *type-argument-list* is not considered to be part of the *simple-name*, *member-access* or > *pointer-member-access*, even if there is no other possible parse of the sequence of tokens.</span></span> <span data-ttu-id="fafc5-191">Si noti che queste regole non vengono applicate durante l'analisi di un *elenco di argomenti di tipo* in uno *spazio dei nomi o un nome di tipo* (§ 3,8).</span><span class="sxs-lookup"><span data-stu-id="fafc5-191">Note that these rules are not applied when parsing a *type-argument-list* in a *namespace-or-type-name* (§3.8).</span></span> <span data-ttu-id="fafc5-192">L'istruzione</span><span class="sxs-lookup"><span data-stu-id="fafc5-192">The statement</span></span>
> ```csharp
> F(G<A,B>(7));
> ```
> <span data-ttu-id="fafc5-193">in base a questa regola, sarà interpretato come una chiamata a `F` con un argomento, ovvero una chiamata a un metodo generico `G` con due argomenti di tipo e un argomento normale.</span><span class="sxs-lookup"><span data-stu-id="fafc5-193">will, according to this rule, be interpreted as a call to `F` with one argument, which is a call to a generic method `G` with two type arguments and one regular argument.</span></span> <span data-ttu-id="fafc5-194">Istruzioni</span><span class="sxs-lookup"><span data-stu-id="fafc5-194">The statements</span></span>
> ```csharp
> F(G < A, B > 7);
> F(G < A, B >> 7);
> ```
> <span data-ttu-id="fafc5-195">ogni oggetto verrà interpretato come una chiamata a `F` con due argomenti.</span><span class="sxs-lookup"><span data-stu-id="fafc5-195">will each be interpreted as a call to `F` with two arguments.</span></span> <span data-ttu-id="fafc5-196">L'istruzione</span><span class="sxs-lookup"><span data-stu-id="fafc5-196">The statement</span></span>
> ```csharp
> x = F < A > +y;
> ```
> <span data-ttu-id="fafc5-197">verrà interpretato come un operatore minore di, un operatore maggiore di e un operatore unario più, come se l'istruzione fosse stata scritta `x = (F < A) > (+y)`, anziché come *nome semplice* con un *tipo-argument-list* seguito da un operatore binario più.</span><span class="sxs-lookup"><span data-stu-id="fafc5-197">will be interpreted as a less than operator, greater than operator, and unary plus operator, as if the statement had been written `x = (F < A) > (+y)`, instead of as a *simple-name* with a *type-argument-list* followed by a binary plus operator.</span></span> <span data-ttu-id="fafc5-198">Nell'istruzione</span><span class="sxs-lookup"><span data-stu-id="fafc5-198">In the statement</span></span>
> ```csharp
> x = y is C<T> + z;
> ```
> <span data-ttu-id="fafc5-199">i token `C<T>` vengono interpretati come uno *spazio dei nomi o un nome di tipo* con un *elenco di argomenti di tipo*.</span><span class="sxs-lookup"><span data-stu-id="fafc5-199">the tokens `C<T>` are interpreted as a *namespace-or-type-name* with a *type-argument-list*.</span></span>

<span data-ttu-id="fafc5-200">In C# 7 sono state introdotte alcune modifiche che rendono queste regole di risoluzione dell'ambiguità non più sufficienti per gestire la complessità del linguaggio.</span><span class="sxs-lookup"><span data-stu-id="fafc5-200">There are a number of changes being introduced in C# 7 that make these disambiguation rules no longer sufficient to handle the complexity of the language.</span></span>

### <a name="out-variable-declarations"></a><span data-ttu-id="fafc5-201">Dichiarazioni di variabili out</span><span class="sxs-lookup"><span data-stu-id="fafc5-201">Out variable declarations</span></span>

<span data-ttu-id="fafc5-202">È ora possibile dichiarare una variabile in un argomento out:</span><span class="sxs-lookup"><span data-stu-id="fafc5-202">It is now possible to declare a variable in an out argument:</span></span>

```csharp
M(out Type name);
```

<span data-ttu-id="fafc5-203">Tuttavia, il tipo può essere generico:</span><span class="sxs-lookup"><span data-stu-id="fafc5-203">However, the type may be generic:</span></span> 

```csharp
M(out A<B> name);
```

<span data-ttu-id="fafc5-204">Poiché la grammatica del linguaggio per l'argomento utilizza *Expression*, questo contesto è soggetto alla regola di risoluzione dell'ambiguità.</span><span class="sxs-lookup"><span data-stu-id="fafc5-204">Since the language grammar for the argument uses *expression*, this context is subject to the disambiguation rule.</span></span> <span data-ttu-id="fafc5-205">In questo caso il `>` di chiusura è seguito da un *identificatore*, che non è uno dei token che consente di considerarlo come un elenco di *argomenti di tipo*.</span><span class="sxs-lookup"><span data-stu-id="fafc5-205">In this case the closing `>` is followed by an *identifier*, which is not one of the tokens that permits it to be treated as a *type-argument-list*.</span></span> <span data-ttu-id="fafc5-206">Viene pertanto proposto di **aggiungere *identificatore* al set di token che attiva la risoluzione dell'ambiguità in un *tipo-argument-list*.**</span><span class="sxs-lookup"><span data-stu-id="fafc5-206">I therefore propose to **add *identifier* to the set of tokens that triggers the disambiguation to a *type-argument-list*.**</span></span>

### <a name="tuples-and-deconstruction-declarations"></a><span data-ttu-id="fafc5-207">Tuple e dichiarazioni di decostruzione</span><span class="sxs-lookup"><span data-stu-id="fafc5-207">Tuples and deconstruction declarations</span></span>

<span data-ttu-id="fafc5-208">Un valore letterale di tupla viene eseguito esattamente nello stesso problema.</span><span class="sxs-lookup"><span data-stu-id="fafc5-208">A tuple literal runs into exactly the same issue.</span></span> <span data-ttu-id="fafc5-209">Si consideri l'espressione di tupla</span><span class="sxs-lookup"><span data-stu-id="fafc5-209">Consider the tuple expression</span></span>

```csharp
(A < B, C > D, E < F, G > H)
```

<span data-ttu-id="fafc5-210">Con le 6 C# regole precedenti per l'analisi di un elenco di argomenti, questo verrebbe analizzato come una tupla con quattro elementi, a partire da `A < B` come primo.</span><span class="sxs-lookup"><span data-stu-id="fafc5-210">Under the old C# 6 rules for parsing an argument list, this would parse as a tuple with four elements, starting with `A < B` as the first.</span></span> <span data-ttu-id="fafc5-211">Tuttavia, quando viene visualizzato a sinistra di una decostruzione, si vuole che la risoluzione dell'ambiguità venga attivata dal token dell' *identificatore* , come descritto in precedenza:</span><span class="sxs-lookup"><span data-stu-id="fafc5-211">However, when this appears on the left of a deconstruction, we want the disambiguation triggered by the *identifier* token as described above:</span></span>

```csharp
(A<B,C> D, E<F,G> H) = e;
```

<span data-ttu-id="fafc5-212">Si tratta di una dichiarazione di decostruzione che dichiara due variabili, la prima delle quali è di tipo `A<B,C>` e `D`denominata.</span><span class="sxs-lookup"><span data-stu-id="fafc5-212">This is a deconstruction declaration which declares two variables, the first of which is of type `A<B,C>` and named `D`.</span></span> <span data-ttu-id="fafc5-213">In altre parole, il valore letterale di tupla contiene due espressioni, ciascuna delle quali è un'espressione di dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="fafc5-213">In other words, the tuple literal contains two expressions, each of which is a declaration expression.</span></span>

<span data-ttu-id="fafc5-214">Per semplicità della specifica e del compilatore, propongo che questo valore letterale di tupla venga analizzato come tupla con due elementi ovunque venga visualizzato (indipendentemente dal fatto che venga visualizzato sul lato sinistro di un'assegnazione).</span><span class="sxs-lookup"><span data-stu-id="fafc5-214">For simplicity of the specification and compiler, I propose that this tuple literal be parsed as a two-element tuple wherever it appears (whether or not it appears on the left-hand-side of an assignment).</span></span> <span data-ttu-id="fafc5-215">Si tratta di un risultato naturale della risoluzione dell'ambiguità descritta nella sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="fafc5-215">That would be a natural result of the disambiguation described in the previous section.</span></span>

### <a name="pattern-matching"></a><span data-ttu-id="fafc5-216">Criteri di ricerca</span><span class="sxs-lookup"><span data-stu-id="fafc5-216">Pattern-matching</span></span>

<span data-ttu-id="fafc5-217">Criteri di ricerca introduce un nuovo contesto in cui si verifica l'ambiguità del tipo di espressione.</span><span class="sxs-lookup"><span data-stu-id="fafc5-217">Pattern matching introduces a new context where the expression-type ambiguity arises.</span></span> <span data-ttu-id="fafc5-218">In precedenza, il lato destro di un operatore `is` era un tipo.</span><span class="sxs-lookup"><span data-stu-id="fafc5-218">Previously the right-hand-side of an `is` operator was a type.</span></span> <span data-ttu-id="fafc5-219">A questo punto può essere un tipo o un'espressione e, se è un tipo, può essere seguito da un identificatore.</span><span class="sxs-lookup"><span data-stu-id="fafc5-219">Now it can be a type or expression, and if it is a type it may be followed by an identifier.</span></span> <span data-ttu-id="fafc5-220">Questo può, tecnicamente, modificare il significato del codice esistente:</span><span class="sxs-lookup"><span data-stu-id="fafc5-220">This can, technically, change the meaning of existing code:</span></span>

```csharp
var x = e is T < A > B;
```

<span data-ttu-id="fafc5-221">Questa operazione può essere analizzata in base alle regole di C# 6 come</span><span class="sxs-lookup"><span data-stu-id="fafc5-221">This could be parsed under C#6 rules as</span></span>

```csharp
var x = ((e is T) < A) > B;
```

<span data-ttu-id="fafc5-222">Tuttavia, in base alle regole di C# 7 (con la risoluzione dell'ambiguità proposta in precedenza) verrebbe analizzata come</span><span class="sxs-lookup"><span data-stu-id="fafc5-222">but under under C#7 rules (with the disambiguation proposed above) would be parsed as</span></span>

```csharp
var x = e is T<A> B;
```

<span data-ttu-id="fafc5-223">che dichiara una variabile `B` di tipo `T<A>`.</span><span class="sxs-lookup"><span data-stu-id="fafc5-223">which declares a variable `B` of type `T<A>`.</span></span> <span data-ttu-id="fafc5-224">Fortunatamente, i compilatori nativi e Roslyn hanno un bug in cui forniscono un errore di sintassi sul codice C# 6.</span><span class="sxs-lookup"><span data-stu-id="fafc5-224">Fortunately, the native and Roslyn compilers have a bug whereby they give a syntax error on the C#6 code.</span></span> <span data-ttu-id="fafc5-225">Questa particolare modifica di rilievo non costituisce pertanto un problema.</span><span class="sxs-lookup"><span data-stu-id="fafc5-225">Therefore this particular breaking change is not a concern.</span></span>

<span data-ttu-id="fafc5-226">La corrispondenza dei modelli introduce token aggiuntivi che dovrebbero guidare la risoluzione dell'ambiguità verso la selezione di un tipo.</span><span class="sxs-lookup"><span data-stu-id="fafc5-226">Pattern-matching introduces additional tokens that should drive the ambiguity resolution toward selecting a type.</span></span> <span data-ttu-id="fafc5-227">Gli esempi seguenti di codice C# 6 valido esistente verrebbero interrotti senza regole di risoluzione dell'ambiguità aggiuntive:</span><span class="sxs-lookup"><span data-stu-id="fafc5-227">The following examples of existing valid C#6 code would be broken without additional disambiguation rules:</span></span>

```csharp
var x = e is A<B> && f;            // &&
var x = e is A<B> || f;            // ||
var x = e is A<B> & f;             // &
var x = e is A<B>[];               // [
```

### <a name="proposed-change-to-the-disambiguation-rule"></a><span data-ttu-id="fafc5-228">Modifica proposta alla regola di risoluzione dell'ambiguità</span><span class="sxs-lookup"><span data-stu-id="fafc5-228">Proposed change to the disambiguation rule</span></span>

<span data-ttu-id="fafc5-229">Suggerisco di rivedere la specifica per modificare l'elenco di token distinguere da</span><span class="sxs-lookup"><span data-stu-id="fafc5-229">I propose to revise the specification to change the list of disambiguating tokens from</span></span>

>
```none
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
```

<span data-ttu-id="fafc5-230">to</span><span class="sxs-lookup"><span data-stu-id="fafc5-230">to</span></span>

>
```none
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^  &&  ||  &  [
```

<span data-ttu-id="fafc5-231">E, in determinati contesti, consideriamo l' *identificatore* come token distinguere.</span><span class="sxs-lookup"><span data-stu-id="fafc5-231">And, in certain contexts, we treat *identifier* as a disambiguating token.</span></span> <span data-ttu-id="fafc5-232">Questi contesti sono i casi in cui la sequenza di token ambiguità è immediatamente preceduta da una delle parole chiave `is`, `case`o `out`o si verifica durante l'analisi del primo elemento di un valore letterale di tupla (nel qual caso i token sono preceduti da `(` o `:` e l'identificatore è seguito da un `,`) o da un elemento successivo di un valore letterale di tupla</span><span class="sxs-lookup"><span data-stu-id="fafc5-232">Those contexts are where the sequence of tokens being disambiguated is immediately preceded by one of the keywords `is`, `case`, or `out`, or arises while parsing the first element of a tuple literal (in which case the tokens are preceded by `(` or `:` and the identifier is followed by a `,`) or a subsequent element of a tuple literal.</span></span>

### <a name="modified-disambiguation-rule"></a><span data-ttu-id="fafc5-233">Regola di risoluzione dell'ambiguità modificata</span><span class="sxs-lookup"><span data-stu-id="fafc5-233">Modified disambiguation rule</span></span>

<span data-ttu-id="fafc5-234">La regola di risoluzione dell'ambiguità riveduta sarà simile alla seguente</span><span class="sxs-lookup"><span data-stu-id="fafc5-234">The revised disambiguation rule would be something like this</span></span>

> <span data-ttu-id="fafc5-235">Se una sequenza di token può essere analizzata (nel contesto) come *nome semplice* (§ 7.6.3), *accesso ai membri* (§ 7.6.5) o *puntatore-membro-Access* (§ 18.5.2) che termina con un *tipo-argument-list* (§ 4.4.1), il token che segue immediatamente il token di chiusura `>` viene esaminato, per verificare se è</span><span class="sxs-lookup"><span data-stu-id="fafc5-235">If a sequence of tokens can be parsed (in context) as a *simple-name* (§7.6.3), *member-access* (§7.6.5), or *pointer-member-access* (§18.5.2) ending with a *type-argument-list* (§4.4.1), the token immediately following the closing `>` token is examined, to see if it is</span></span>
> - <span data-ttu-id="fafc5-236">Uno dei `(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^  &&  ||  &  [`; o</span><span class="sxs-lookup"><span data-stu-id="fafc5-236">One of `(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^  &&  ||  &  [`; or</span></span>
> - <span data-ttu-id="fafc5-237">Uno degli operatori relazionali `<  >  <=  >=  is as`; o</span><span class="sxs-lookup"><span data-stu-id="fafc5-237">One of the relational operators `<  >  <=  >=  is as`; or</span></span>
> - <span data-ttu-id="fafc5-238">Parola chiave di query contestuale visualizzata all'interno di un'espressione di query. o</span><span class="sxs-lookup"><span data-stu-id="fafc5-238">A contextual query keyword appearing inside a query expression; or</span></span>
> - <span data-ttu-id="fafc5-239">In determinati contesti consideriamo l' *identificatore* come token distinguere.</span><span class="sxs-lookup"><span data-stu-id="fafc5-239">In certain contexts, we treat *identifier* as a disambiguating token.</span></span> <span data-ttu-id="fafc5-240">Questi contesti sono i casi in cui la sequenza di token ambiguità è immediatamente preceduta da una delle parole chiave `is`, `case` o `out`o si verifica durante l'analisi del primo elemento di un valore letterale di tupla (nel qual caso i token sono preceduti da `(` o `:` e l'identificatore è seguito da un `,`) o da un elemento successivo di un valore letterale di tupla</span><span class="sxs-lookup"><span data-stu-id="fafc5-240">Those contexts are where the sequence of tokens being disambiguated is immediately preceded by one of the keywords `is`, `case` or `out`, or arises while parsing the first element of a tuple literal (in which case the tokens are preceded by `(` or `:` and the identifier is followed by a `,`) or a subsequent element of a tuple literal.</span></span>
> 
> <span data-ttu-id="fafc5-241">Se il token seguente è incluso in questo elenco oppure un identificatore in un contesto di questo tipo, il *tipo-argument-list* viene mantenuto come parte del *nome semplice*, l' *accesso ai membri* o l' *accesso ai membri del puntatore* e qualsiasi altra possibile analisi della sequenza di token viene scartata.</span><span class="sxs-lookup"><span data-stu-id="fafc5-241">If the following token is among this list, or an identifier in such a context, then the *type-argument-list* is retained as part of the *simple-name*, *member-access* or  *pointer-member-access* and any other possible parse of the sequence of tokens is discarded.</span></span>  <span data-ttu-id="fafc5-242">In caso contrario, l' *elenco di argomenti di tipo* non viene considerato parte dell'accesso con *nome semplice*, *accesso ai membri* o *puntatore-membro*, anche se non è possibile analizzare la sequenza di token.</span><span class="sxs-lookup"><span data-stu-id="fafc5-242">Otherwise, the *type-argument-list* is not considered to be part of the *simple-name*, *member-access* or *pointer-member-access*, even if there is no other possible parse of the sequence of tokens.</span></span> <span data-ttu-id="fafc5-243">Si noti che queste regole non vengono applicate durante l'analisi di un *elenco di argomenti di tipo* in uno *spazio dei nomi o un nome di tipo* (§ 3,8).</span><span class="sxs-lookup"><span data-stu-id="fafc5-243">Note that these rules are not applied when parsing a *type-argument-list* in a *namespace-or-type-name* (§3.8).</span></span>

### <a name="breaking-changes-due-to-this-proposal"></a><span data-ttu-id="fafc5-244">Modifiche di rilievo dovute a questa proposta</span><span class="sxs-lookup"><span data-stu-id="fafc5-244">Breaking changes due to this proposal</span></span>

<span data-ttu-id="fafc5-245">Nessuna modifica di rilievo è nota a causa di questa regola di risoluzione dell'ambiguità proposta.</span><span class="sxs-lookup"><span data-stu-id="fafc5-245">No breaking changes are known due to this proposed disambiguation rule.</span></span>

### <a name="interesting-examples"></a><span data-ttu-id="fafc5-246">Esempi interessanti</span><span class="sxs-lookup"><span data-stu-id="fafc5-246">Interesting examples</span></span>

<span data-ttu-id="fafc5-247">Ecco alcuni risultati interessanti di queste regole di risoluzione dell'ambiguità:</span><span class="sxs-lookup"><span data-stu-id="fafc5-247">Here are some interesting results of these disambiguation rules:</span></span>

<span data-ttu-id="fafc5-248">L'espressione `(A < B, C > D)` è una tupla con due elementi, ognuno dei quali è un confronto.</span><span class="sxs-lookup"><span data-stu-id="fafc5-248">The expression `(A < B, C > D)` is a tuple with two elements, each a comparison.</span></span>

<span data-ttu-id="fafc5-249">L'espressione `(A<B,C> D, E)` è una tupla con due elementi, il primo dei quali è un'espressione di dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="fafc5-249">The expression `(A<B,C> D, E)` is a tuple with two elements, the first of which is a declaration expression.</span></span>

<span data-ttu-id="fafc5-250">La chiamata `M(A < B, C > D, E)` dispone di tre argomenti.</span><span class="sxs-lookup"><span data-stu-id="fafc5-250">The invocation `M(A < B, C > D, E)` has three arguments.</span></span>

<span data-ttu-id="fafc5-251">La chiamata `M(out A<B,C> D, E)` dispone di due argomenti, il primo dei quali è una dichiarazione di `out`.</span><span class="sxs-lookup"><span data-stu-id="fafc5-251">The invocation `M(out A<B,C> D, E)` has two arguments, the first of which is an `out` declaration.</span></span>

<span data-ttu-id="fafc5-252">L'espressione `e is A<B> C` utilizza un'espressione di dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="fafc5-252">The expression `e is A<B> C` uses a declaration expression.</span></span>

<span data-ttu-id="fafc5-253">L'etichetta case `case A<B> C:` usa un'espressione di dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="fafc5-253">The case label `case A<B> C:` uses a declaration expression.</span></span>

## <a name="some-examples-of-pattern-matching"></a><span data-ttu-id="fafc5-254">Alcuni esempi di criteri di ricerca</span><span class="sxs-lookup"><span data-stu-id="fafc5-254">Some examples of pattern matching</span></span>

### <a name="is-as"></a><span data-ttu-id="fafc5-255">Is-As</span><span class="sxs-lookup"><span data-stu-id="fafc5-255">Is-As</span></span>

<span data-ttu-id="fafc5-256">È possibile sostituire l'idioma</span><span class="sxs-lookup"><span data-stu-id="fafc5-256">We can replace the idiom</span></span>

```csharp
var v = expr as Type;   
if (v != null) {
    // code using v
}
```

<span data-ttu-id="fafc5-257">Con la forma leggermente più concisa e diretta</span><span class="sxs-lookup"><span data-stu-id="fafc5-257">With the slightly more concise and direct</span></span>

```csharp
if (expr is Type v) {
    // code using v
}
```

### <a name="testing-nullable"></a><span data-ttu-id="fafc5-258">Test Nullable</span><span class="sxs-lookup"><span data-stu-id="fafc5-258">Testing nullable</span></span>

<span data-ttu-id="fafc5-259">È possibile sostituire l'idioma</span><span class="sxs-lookup"><span data-stu-id="fafc5-259">We can replace the idiom</span></span>

```csharp
Type? v = x?.y?.z;
if (v.HasValue) {
    var value = v.GetValueOrDefault();
    // code using value
}
```

<span data-ttu-id="fafc5-260">Con la forma leggermente più concisa e diretta</span><span class="sxs-lookup"><span data-stu-id="fafc5-260">With the slightly more concise and direct</span></span>

```csharp
if (x?.y?.z is Type value) {
    // code using value
}
```

### <a name="arithmetic-simplification"></a><span data-ttu-id="fafc5-261">Semplificazione aritmetica</span><span class="sxs-lookup"><span data-stu-id="fafc5-261">Arithmetic simplification</span></span>

<span data-ttu-id="fafc5-262">Si supponga di definire un set di tipi ricorsivi per rappresentare le espressioni (per una proposta separata):</span><span class="sxs-lookup"><span data-stu-id="fafc5-262">Suppose we define a set of recursive types to represent expressions (per a separate proposal):</span></span>

```csharp
abstract class Expr;
class X() : Expr;
class Const(double Value) : Expr;
class Add(Expr Left, Expr Right) : Expr;
class Mult(Expr Left, Expr Right) : Expr;
class Neg(Expr Value) : Expr;
```

<span data-ttu-id="fafc5-263">A questo punto è possibile definire una funzione per calcolare la derivata (non ridotta) di un'espressione:</span><span class="sxs-lookup"><span data-stu-id="fafc5-263">Now we can define a function to compute the (unreduced) derivative of an expression:</span></span>

```csharp
Expr Deriv(Expr e)
{
  switch (e) {
    case X(): return Const(1);
    case Const(*): return Const(0);
    case Add(var Left, var Right):
      return Add(Deriv(Left), Deriv(Right));
    case Mult(var Left, var Right):
      return Add(Mult(Deriv(Left), Right), Mult(Left, Deriv(Right)));
    case Neg(var Value):
      return Neg(Deriv(Value));
  }
}
```

<span data-ttu-id="fafc5-264">Un semplificatore di espressioni illustra i modelli posizionali:</span><span class="sxs-lookup"><span data-stu-id="fafc5-264">An expression simplifier demonstrates positional patterns:</span></span>

```csharp
Expr Simplify(Expr e)
{
  switch (e) {
    case Mult(Const(0), *): return Const(0);
    case Mult(*, Const(0)): return Const(0);
    case Mult(Const(1), var x): return Simplify(x);
    case Mult(var x, Const(1)): return Simplify(x);
    case Mult(Const(var l), Const(var r)): return Const(l*r);
    case Add(Const(0), var x): return Simplify(x);
    case Add(var x, Const(0)): return Simplify(x);
    case Add(Const(var l), Const(var r)): return Const(l+r);
    case Neg(Const(var k)): return Const(-k);
    default: return e;
  }
}
```
