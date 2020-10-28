---
ms.openlocfilehash: 7ee72a2e3171940e93a2dc7048136a7ddc6b7698
ms.sourcegitcommit: 75ea1d741f92d6fda117caa189db17370f1b9f1c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92684059"
---
# <a name="recursive-pattern-matching"></a><span data-ttu-id="e2069-101">Criteri di ricerca ricorsivi</span><span class="sxs-lookup"><span data-stu-id="e2069-101">Recursive Pattern Matching</span></span>

## <a name="summary"></a><span data-ttu-id="e2069-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="e2069-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="e2069-103">Le estensioni di criteri di ricerca per C# consentono molti dei vantaggi dei tipi di dati algebrici e dei criteri di ricerca dai linguaggi funzionali, ma in modo da integrarsi perfettamente con l'aspetto del linguaggio sottostante.</span><span class="sxs-lookup"><span data-stu-id="e2069-103">Pattern matching extensions for C# enable many of the benefits of algebraic data types and pattern matching from functional languages, but in a way that smoothly integrates with the feel of the underlying language.</span></span> <span data-ttu-id="e2069-104">Gli elementi di questo approccio sono ispirati dalle funzionalità correlate nei linguaggi di programmazione [F #](http://www.msr-waypoint.net/pubs/79947/p29-syme.pdf "Criteri di ricerca estendibili tramite un linguaggio leggero") e [scala](https://link.springer.com/content/pdf/10.1007%2F978-3-540-73589-2.pdf "Corrispondenza di oggetti con modelli, pagina 273").</span><span class="sxs-lookup"><span data-stu-id="e2069-104">Elements of this approach are inspired by related features in the programming languages [F#](http://www.msr-waypoint.net/pubs/79947/p29-syme.pdf "Extensible Pattern Matching Via a Lightweight Language") and [Scala](https://link.springer.com/content/pdf/10.1007%2F978-3-540-73589-2.pdf "Matching Objects With Patterns, page 273").</span></span>

## <a name="detailed-design"></a><span data-ttu-id="e2069-105">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="e2069-105">Detailed design</span></span>
[design]: #detailed-design

### <a name="is-expression"></a><span data-ttu-id="e2069-106">Espressione is</span><span class="sxs-lookup"><span data-stu-id="e2069-106">Is Expression</span></span>

<span data-ttu-id="e2069-107">L' `is` operatore viene esteso per testare un'espressione rispetto a un *criterio* .</span><span class="sxs-lookup"><span data-stu-id="e2069-107">The `is` operator is extended to test an expression against a *pattern* .</span></span>

```antlr
relational_expression
    : is_pattern_expression
    ;
is_pattern_expression
    : relational_expression 'is' pattern
    ;
```

<span data-ttu-id="e2069-108">Questa forma di *relational_expression* è aggiunta ai moduli esistenti nella specifica C#.</span><span class="sxs-lookup"><span data-stu-id="e2069-108">This form of *relational_expression* is in addition to the existing forms in the C# specification.</span></span> <span data-ttu-id="e2069-109">Si tratta di un errore in fase di compilazione se il *relational_expression* a sinistra del `is` token non designa un valore o non ha un tipo.</span><span class="sxs-lookup"><span data-stu-id="e2069-109">It is a compile-time error if the *relational_expression* to the left of the `is` token does not designate a value or does not have a type.</span></span>

<span data-ttu-id="e2069-110">Ogni *identificatore* del modello introduce una nuova variabile locale *assegnata definitivamente* dopo l' `is` operatore `true` (ovvero *assegnata definitivamente quando è true* ).</span><span class="sxs-lookup"><span data-stu-id="e2069-110">Every *identifier* of the pattern introduces a new local variable that is *definitely assigned* after the `is` operator is `true` (i.e. *definitely assigned when true* ).</span></span>

> <span data-ttu-id="e2069-111">Nota: esiste tecnicamente un'ambiguità tra il *tipo* in `is-expression` e *constant_pattern* , una delle quali potrebbe essere un'analisi valida di un identificatore qualificato.</span><span class="sxs-lookup"><span data-stu-id="e2069-111">Note: There is technically an ambiguity between *type* in an `is-expression` and *constant_pattern* , either of which might be a valid parse of a qualified identifier.</span></span> <span data-ttu-id="e2069-112">Si tenta di associarlo come tipo per la compatibilità con le versioni precedenti del linguaggio; solo se l'operazione ha esito negativo, viene risolta quando si esegue un'espressione in altri contesti, fino alla prima cosa trovata (che deve essere una costante o un tipo).</span><span class="sxs-lookup"><span data-stu-id="e2069-112">We try to bind it as a type for compatibility with previous versions of the language; only if that fails do we resolve it as we do an expression in other contexts, to the first thing found (which must be either a constant or a type).</span></span> <span data-ttu-id="e2069-113">Questa ambiguità è presente solo sul lato destro di un' `is` espressione.</span><span class="sxs-lookup"><span data-stu-id="e2069-113">This ambiguity is only present on the right-hand-side of an `is` expression.</span></span>

### <a name="patterns"></a><span data-ttu-id="e2069-114">Modelli</span><span class="sxs-lookup"><span data-stu-id="e2069-114">Patterns</span></span>

<span data-ttu-id="e2069-115">Gli schemi vengono usati nell'operatore *is_pattern* , in una *switch_Statement* e in un *switch_expression* per esprimere la forma dei dati in base ai dati in ingresso (che chiameremo il valore di input).</span><span class="sxs-lookup"><span data-stu-id="e2069-115">Patterns are used in the *is_pattern* operator, in a *switch_statement* , and in a *switch_expression* to express the shape of data against which incoming data  (which we call the input value) is to be compared.</span></span> <span data-ttu-id="e2069-116">I modelli possono essere ricorsivi in modo che sia possibile trovare una corrispondenza tra parti dei dati e modelli secondari.</span><span class="sxs-lookup"><span data-stu-id="e2069-116">Patterns may be recursive so that parts of the data may be matched against sub-patterns.</span></span>

```antlr
pattern
    : declaration_pattern
    | constant_pattern
    | var_pattern
    | positional_pattern
    | property_pattern
    | discard_pattern
    ;
declaration_pattern
    : type simple_designation
    ;
constant_pattern
    : constant_expression
    ;
var_pattern
    : 'var' designation
    ;
positional_pattern
    : type? '(' subpatterns? ')' property_subpattern? simple_designation?
    ;
subpatterns
    : subpattern
    | subpattern ',' subpatterns
    ;
subpattern
    : pattern
    | identifier ':' pattern
    ;
property_subpattern
    : '{' '}'
    | '{' subpatterns ','? '}'
    ;
property_pattern
    : type? property_subpattern simple_designation?
    ;
simple_designation
    : single_variable_designation
    | discard_designation
    ;
discard_pattern
    : '_'
    ;
```

#### <a name="declaration-pattern"></a><span data-ttu-id="e2069-117">Modello di dichiarazione</span><span class="sxs-lookup"><span data-stu-id="e2069-117">Declaration Pattern</span></span>

```antlr
declaration_pattern
    : type simple_designation
    ;
```

<span data-ttu-id="e2069-118">Il *declaration_pattern* verifica che un'espressione sia di un tipo specificato e ne esegue il cast a tale tipo se il test ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="e2069-118">The *declaration_pattern* both tests that an expression is of a given type and casts it to that type if the test succeeds.</span></span> <span data-ttu-id="e2069-119">Questo può introdurre una variabile locale del tipo specificato denominato dall'identificatore specificato, se la designazione è un *single_variable_designation* .</span><span class="sxs-lookup"><span data-stu-id="e2069-119">This may introduce a local variable of the given type named by the given identifier, if the designation is a *single_variable_designation* .</span></span> <span data-ttu-id="e2069-120">Tale variabile locale viene *assegnata definitivamente* quando il risultato dell'operazione di corrispondenza dei modelli è `true` .</span><span class="sxs-lookup"><span data-stu-id="e2069-120">That local variable is *definitely assigned* when the result of the pattern-matching operation is `true`.</span></span>

<span data-ttu-id="e2069-121">La semantica di runtime di questa espressione è che testa il tipo di runtime dell'operando di sinistra *relational_expression* sul *tipo* nel modello.</span><span class="sxs-lookup"><span data-stu-id="e2069-121">The runtime semantic of this expression is that it tests the runtime type of the left-hand *relational_expression* operand against the *type* in the pattern.</span></span>  <span data-ttu-id="e2069-122">Se è di quel tipo di runtime (o di un sottotipo) e non `null` , il risultato di `is operator` è `true` .</span><span class="sxs-lookup"><span data-stu-id="e2069-122">If it is of that runtime type (or some subtype) and not `null`, the result of the `is operator` is `true`.</span></span>

<span data-ttu-id="e2069-123">Alcune combinazioni di tipo statico del lato sinistro e del tipo specificato vengono considerate incompatibili e generano un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="e2069-123">Certain combinations of static type of the left-hand-side and the given type are considered incompatible and result in compile-time error.</span></span> <span data-ttu-id="e2069-124">Un valore di tipo statico `E` viene definito *modello compatibile* con un tipo `T` se esiste una conversione di identità, una conversione di un riferimento implicito, una conversione boxing, una conversione esplicita di un riferimento o una conversione unboxing da `E` a `T` oppure se uno di questi tipi è un tipo aperto.</span><span class="sxs-lookup"><span data-stu-id="e2069-124">A value of static type `E` is said to be *pattern-compatible* with a type `T` if there exists an identity conversion, an implicit reference conversion, a boxing conversion, an explicit reference conversion, or an unboxing conversion from `E` to `T`, or if one of those types is an open type.</span></span> <span data-ttu-id="e2069-125">Si tratta di un errore in fase di compilazione se un input di tipo `E` non è compatibile con i *modelli* con il *tipo* in un modello di tipo con cui corrisponde.</span><span class="sxs-lookup"><span data-stu-id="e2069-125">It is a compile-time error if an input of type `E` is not *pattern-compatible* with the *type* in a type pattern that it is matched with.</span></span>

<span data-ttu-id="e2069-126">Il modello di tipo è utile per l'esecuzione di test dei tipi di riferimento in fase di esecuzione e sostituisce l'idioma</span><span class="sxs-lookup"><span data-stu-id="e2069-126">The type pattern is useful for performing run-time type tests of reference types, and replaces the idiom</span></span>

```csharp
var v = expr as Type;
if (v != null) { // code using v
```

<span data-ttu-id="e2069-127">Con l'aspetto leggermente più conciso</span><span class="sxs-lookup"><span data-stu-id="e2069-127">With the slightly more concise</span></span>

```csharp
if (expr is Type v) { // code using v
```

<span data-ttu-id="e2069-128">Si tratta di un errore se il *tipo* è un tipo di valore Nullable.</span><span class="sxs-lookup"><span data-stu-id="e2069-128">It is an error if *type* is a nullable value type.</span></span>

<span data-ttu-id="e2069-129">Il modello di tipo può essere usato per testare i valori dei tipi nullable: un valore di tipo `Nullable<T>` (o un oggetto boxed `T` ) corrisponde a un modello di tipo `T2 id` se il valore è non null e il tipo di `T2` è `T` o di un tipo o di un'interfaccia di base di `T` .</span><span class="sxs-lookup"><span data-stu-id="e2069-129">The type pattern can be used to test values of nullable types: a value of type `Nullable<T>` (or a boxed `T`) matches a type pattern `T2 id` if the value is non-null and the type of `T2` is `T`, or some base type or interface of `T`.</span></span> <span data-ttu-id="e2069-130">Ad esempio, nel frammento di codice</span><span class="sxs-lookup"><span data-stu-id="e2069-130">For example, in the code fragment</span></span>

```csharp
int? x = 3;
if (x is int v) { // code using v
```

<span data-ttu-id="e2069-131">La condizione dell' `if` istruzione è in fase di `true` esecuzione e la variabile `v` include il valore `3` di tipo `int` all'interno del blocco.</span><span class="sxs-lookup"><span data-stu-id="e2069-131">The condition of the `if` statement is `true` at runtime and the variable `v` holds the value `3` of type `int` inside the block.</span></span> <span data-ttu-id="e2069-132">Dopo il blocco, la variabile `v` è nell'ambito ma non è definitivamente assegnata.</span><span class="sxs-lookup"><span data-stu-id="e2069-132">After the block the variable `v` is in scope but not definitely assigned.</span></span>

#### <a name="constant-pattern"></a><span data-ttu-id="e2069-133">Criterio costante</span><span class="sxs-lookup"><span data-stu-id="e2069-133">Constant Pattern</span></span>

```antlr
constant_pattern
    : constant_expression
    ;
```

<span data-ttu-id="e2069-134">Un criterio costante verifica il valore di un'espressione rispetto a un valore costante.</span><span class="sxs-lookup"><span data-stu-id="e2069-134">A constant pattern tests the value of an expression against a constant value.</span></span> <span data-ttu-id="e2069-135">La costante può essere qualsiasi espressione costante, ad esempio un valore letterale, il nome di una variabile dichiarata `const` o una costante di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="e2069-135">The constant may be any constant expression, such as a literal, the name of a declared `const` variable, or an enumeration constant.</span></span> <span data-ttu-id="e2069-136">Quando il valore di input non è un tipo aperto, l'espressione costante viene convertita in modo implicito nel tipo dell'espressione corrispondente. Se il tipo del valore di input non è *compatibile* con il modello con il tipo dell'espressione costante, l'operazione di corrispondenza dei modelli è un errore.</span><span class="sxs-lookup"><span data-stu-id="e2069-136">When the input value is not an open type, the constant expression is implicitly converted to the type of the matched expression; if the type of the input value is not *pattern-compatible* with the type of the constant expression, the pattern-matching operation is an error.</span></span>

<span data-ttu-id="e2069-137">Il modello *c* viene considerato corrispondente al valore di input convertito *e* se `object.Equals(c, e)` restituisce `true` .</span><span class="sxs-lookup"><span data-stu-id="e2069-137">The pattern *c* is considered matching the converted input value *e* if `object.Equals(c, e)` would return `true`.</span></span>

<span data-ttu-id="e2069-138">Si prevede di vedere `e is null` come il modo più comune per eseguire il test `null` nel codice appena scritto, perché non può richiamare un definito dall'utente `operator==` .</span><span class="sxs-lookup"><span data-stu-id="e2069-138">We expect to see `e is null` as the most common way to test for `null` in newly written code, as it cannot invoke a user-defined `operator==`.</span></span>

#### <a name="var-pattern"></a><span data-ttu-id="e2069-139">Modello var</span><span class="sxs-lookup"><span data-stu-id="e2069-139">Var Pattern</span></span>

```antlr
var_pattern
    : 'var' designation
    ;
designation
    : simple_designation
    | tuple_designation
    ;
simple_designation
    : single_variable_designation
    | discard_designation
    ;
single_variable_designation
    : identifier
    ;
discard_designation
    : _
    ;
tuple_designation
    : '(' designations? ')'
    ;
designations
    : designation
    | designations ',' designation
    ;
```

<span data-ttu-id="e2069-140">Se la *designazione* è una *simple_designation* , un'espressione *e* corrisponde al modello.</span><span class="sxs-lookup"><span data-stu-id="e2069-140">If the *designation* is a *simple_designation* , an expression *e* matches the pattern.</span></span> <span data-ttu-id="e2069-141">In altre parole, una corrispondenza con un *modello var* ha sempre esito positivo con un *simple_designation* .</span><span class="sxs-lookup"><span data-stu-id="e2069-141">In other words, a match to a *var pattern* always succeeds with a *simple_designation* .</span></span> <span data-ttu-id="e2069-142">Se il *simple_designation* è un *single_variable_designation* , il valore di *e* viene associato a una variabile locale appena introdotta.</span><span class="sxs-lookup"><span data-stu-id="e2069-142">If the *simple_designation* is a *single_variable_designation* , the value of *e* is bounds to a newly introduced local variable.</span></span> <span data-ttu-id="e2069-143">Il tipo della variabile locale è il tipo statico di *e* .</span><span class="sxs-lookup"><span data-stu-id="e2069-143">The type of the local variable is the static type of *e* .</span></span>

<span data-ttu-id="e2069-144">Se la *designazione* è un *tuple_designation* , il modello è equivalente a una *positional_pattern* della `(var` *designazione* del modulo,... `)` dove le *designazioni* sono quelle trovate all'interno dell' *tuple_designation* .</span><span class="sxs-lookup"><span data-stu-id="e2069-144">If the *designation* is a *tuple_designation* , then the pattern is equivalent to a *positional_pattern* of the form `(var` *designation* , ... `)` where the *designation* s are those found within the *tuple_designation* .</span></span>  <span data-ttu-id="e2069-145">Ad esempio, il modello `var (x, (y, z))` è equivalente a `(var x, (var y, var z))` .</span><span class="sxs-lookup"><span data-stu-id="e2069-145">For example, the pattern `var (x, (y, z))` is equivalent to `(var x, (var y, var z))`.</span></span>

<span data-ttu-id="e2069-146">Si tratta di un errore se il nome viene `var` associato a un tipo.</span><span class="sxs-lookup"><span data-stu-id="e2069-146">It is an error if the name `var` binds to a type.</span></span>

#### <a name="discard-pattern"></a><span data-ttu-id="e2069-147">Elimina modello</span><span class="sxs-lookup"><span data-stu-id="e2069-147">Discard Pattern</span></span>

```antlr
discard_pattern
    : '_'
    ;
```

<span data-ttu-id="e2069-148">Un'espressione *e* corrisponde sempre al modello `_` .</span><span class="sxs-lookup"><span data-stu-id="e2069-148">An expression *e* matches the pattern `_` always.</span></span> <span data-ttu-id="e2069-149">In altre parole, ogni espressione corrisponde al modello di scarto.</span><span class="sxs-lookup"><span data-stu-id="e2069-149">In other words, every expression matches the discard pattern.</span></span>

<span data-ttu-id="e2069-150">Un modello di scarto non può essere usato come modello di un *is_pattern_expression* .</span><span class="sxs-lookup"><span data-stu-id="e2069-150">A discard pattern may not be used as the pattern of an *is_pattern_expression* .</span></span>

#### <a name="positional-pattern"></a><span data-ttu-id="e2069-151">Modello posizionale</span><span class="sxs-lookup"><span data-stu-id="e2069-151">Positional Pattern</span></span>

<span data-ttu-id="e2069-152">Un criterio posizionale verifica che il valore di input non sia `null` , richiama un metodo appropriato `Deconstruct` ed esegue ulteriori criteri di ricerca sui valori risultanti.</span><span class="sxs-lookup"><span data-stu-id="e2069-152">A positional pattern checks that the input value is not `null`, invokes an appropriate `Deconstruct` method, and performs further pattern matching on the resulting values.</span></span>  <span data-ttu-id="e2069-153">Supporta inoltre una sintassi del modello simile a una tupla (senza il tipo fornito) quando il tipo del valore di input è uguale al tipo che contiene `Deconstruct` o se il tipo del valore di input è un tipo di tupla o se il tipo del valore di input è `object` o `ITuple` e il tipo di runtime dell'espressione implementa `ITuple` .</span><span class="sxs-lookup"><span data-stu-id="e2069-153">It also supports a tuple-like pattern syntax (without the type being provided) when the type of the input value is the same as the type containing `Deconstruct`, or if the type of the input value is a tuple type, or if the type of the input value is `object` or `ITuple` and the runtime type of the expression implements `ITuple`.</span></span>

```antlr
positional_pattern
    : type? '(' subpatterns? ')' property_subpattern? simple_designation?
    ;
subpatterns
    : subpattern
    | subpattern ',' subpatterns
    ;
subpattern
    : pattern
    | identifier ':' pattern
    ;
```

<span data-ttu-id="e2069-154">Se il *tipo* viene omesso, lo consideriamo come il tipo statico del valore di input.</span><span class="sxs-lookup"><span data-stu-id="e2069-154">If the *type* is omitted, we take it to be the static type of the input value.</span></span>

<span data-ttu-id="e2069-155">Data la corrispondenza di un valore di input con il *tipo* di pattern `(` *subpattern_list* `)` , viene selezionato un metodo eseguendo una ricerca nel *tipo* per le dichiarazioni accessibili `Deconstruct` e selezionando una tra di esse usando le stesse regole della dichiarazione di decostruzione.</span><span class="sxs-lookup"><span data-stu-id="e2069-155">Given a match of an input value to the pattern *type* `(` *subpattern_list* `)`, a method is selected by searching in *type* for accessible declarations of `Deconstruct` and selecting one among them using the same rules as for the deconstruction declaration.</span></span>

<span data-ttu-id="e2069-156">Se un *positional_pattern* omette il tipo, presenta un solo *sottomodello* senza *identificatore* , non ha *property_subpattern* e non ha *simple_designation* .</span><span class="sxs-lookup"><span data-stu-id="e2069-156">It is an error if a *positional_pattern* omits the type, has a single *subpattern* without an *identifier* , has no *property_subpattern* and has no *simple_designation* .</span></span> <span data-ttu-id="e2069-157">Questo ambiguità tra un *constant_pattern* racchiuso tra parentesi e una *positional_pattern* .</span><span class="sxs-lookup"><span data-stu-id="e2069-157">This disambiguates between a *constant_pattern* that is parenthesized and a *positional_pattern* .</span></span>

<span data-ttu-id="e2069-158">Per estrarre i valori in base ai criteri dell'elenco,</span><span class="sxs-lookup"><span data-stu-id="e2069-158">In order to extract the values to match against the patterns in the list,</span></span>
- <span data-ttu-id="e2069-159">Se il *tipo* è stato omesso e il tipo del valore di input è un tipo di tupla, il numero di sottomodelli deve corrispondere alla cardinalità della tupla.</span><span class="sxs-lookup"><span data-stu-id="e2069-159">If *type* was omitted and the input value's type is a tuple type, then the number of subpatterns is required to be the same as the cardinality of the tuple.</span></span> <span data-ttu-id="e2069-160">Ogni elemento di tupla viene associato al *criterio secondario* corrispondente e la corrispondenza ha esito positivo se tutti gli elementi hanno esito positivo.</span><span class="sxs-lookup"><span data-stu-id="e2069-160">Each tuple element is matched against the corresponding *subpattern* , and the match succeeds if all of these succeed.</span></span> <span data-ttu-id="e2069-161">Se un *sottomodello* ha un *identificatore* , deve denominare un elemento tupla nella posizione corrispondente nel tipo di tupla.</span><span class="sxs-lookup"><span data-stu-id="e2069-161">If any *subpattern* has an *identifier* , then that must name a tuple element at the corresponding position in the tuple type.</span></span>
- <span data-ttu-id="e2069-162">In caso contrario, se un oggetto appropriato `Deconstruct` esiste come membro di *tipo* , si verifica un errore in fase di compilazione se il tipo del valore di input non è compatibile con i *modelli* con il *tipo* .</span><span class="sxs-lookup"><span data-stu-id="e2069-162">Otherwise, if a suitable `Deconstruct` exists as a member of *type* , it is a compile-time error if the type of the input value is not *pattern-compatible* with *type* .</span></span> <span data-ttu-id="e2069-163">In fase di esecuzione il valore di input viene testato rispetto al *tipo* .</span><span class="sxs-lookup"><span data-stu-id="e2069-163">At runtime the input value is tested against *type* .</span></span> <span data-ttu-id="e2069-164">Se questa operazione ha esito negativo, la corrispondenza del criterio posizionale ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="e2069-164">If this fails then the positional pattern match fails.</span></span> <span data-ttu-id="e2069-165">Se ha esito positivo, il valore di input viene convertito in questo tipo e `Deconstruct` viene richiamato con le nuove variabili generate dal compilatore per ricevere i `out` parametri.</span><span class="sxs-lookup"><span data-stu-id="e2069-165">If it succeeds,  the input value is converted to this type and `Deconstruct` is invoked with fresh compiler-generated variables to receive the `out` parameters.</span></span> <span data-ttu-id="e2069-166">Ogni valore ricevuto viene individuato in base al *criterio secondario* corrispondente e la corrispondenza ha esito positivo se tutti questi valori hanno esito positivo.</span><span class="sxs-lookup"><span data-stu-id="e2069-166">Each value that was received is matched against the corresponding *subpattern* , and the match succeeds if all of these succeed.</span></span> <span data-ttu-id="e2069-167">Se un *sottomodello* ha un *identificatore* , deve denominare un parametro nella posizione corrispondente di `Deconstruct` .</span><span class="sxs-lookup"><span data-stu-id="e2069-167">If any *subpattern* has an *identifier* , then that must name a parameter at the corresponding position of `Deconstruct`.</span></span>
- <span data-ttu-id="e2069-168">In caso contrario, se il *tipo* è stato omesso e il valore di input è di tipo `object` o `ITuple` o di un tipo che può essere convertito in `ITuple` mediante una conversione implicita di riferimento e non viene visualizzato alcun *identificatore* tra i modelli, viene trovata una corrispondenza con `ITuple` .</span><span class="sxs-lookup"><span data-stu-id="e2069-168">Otherwise if *type* was omitted, and the input value is of type `object` or `ITuple` or some type that can be converted to `ITuple` by an implicit reference conversion, and no *identifier* appears among the subpatterns, then we match using `ITuple`.</span></span>
- <span data-ttu-id="e2069-169">In caso contrario, il modello è un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="e2069-169">Otherwise the pattern is a compile-time error.</span></span>

<span data-ttu-id="e2069-170">L'ordine in cui i criteri di ricerca vengono confrontati in fase di esecuzione non è specificato e una corrispondenza non riuscita potrebbe non tentare di trovare la corrispondenza con tutti i sottomodelli.</span><span class="sxs-lookup"><span data-stu-id="e2069-170">The order in which subpatterns are matched at runtime is unspecified, and a failed match may not attempt to match all subpatterns.</span></span>

##### <a name="example"></a><span data-ttu-id="e2069-171">Esempio</span><span class="sxs-lookup"><span data-stu-id="e2069-171">Example</span></span>

<span data-ttu-id="e2069-172">Questo esempio usa molte delle funzionalità descritte in questa specifica</span><span class="sxs-lookup"><span data-stu-id="e2069-172">This example uses many of the features described in this specification</span></span>

``` c#
    var newState = (GetState(), action, hasKey) switch {
        (DoorState.Closed, Action.Open, _) => DoorState.Opened,
        (DoorState.Opened, Action.Close, _) => DoorState.Closed,
        (DoorState.Closed, Action.Lock, true) => DoorState.Locked,
        (DoorState.Locked, Action.Unlock, true) => DoorState.Closed,
        (var state, _, _) => state };
```

#### <a name="property-pattern"></a><span data-ttu-id="e2069-173">Modello di proprietà</span><span class="sxs-lookup"><span data-stu-id="e2069-173">Property Pattern</span></span>

<span data-ttu-id="e2069-174">Un modello di proprietà controlla che il valore di input non sia `null` e che corrisponda in modo ricorsivo ai valori estratti dall'uso di proprietà o campi accessibili.</span><span class="sxs-lookup"><span data-stu-id="e2069-174">A property pattern checks that the input value is not `null` and recursively matches values extracted by the use of accessible properties or fields.</span></span>

```antlr
property_pattern
    : type? property_subpattern simple_designation?
    ;
property_subpattern
    : '{' '}'
    | '{' subpatterns ','? '}'
    ;
```

<span data-ttu-id="e2069-175">È un errore se un _sottomodello_ di un _property_pattern_ non contiene un _identificatore_ (deve essere nel secondo formato, che ha un _identificatore_ ).</span><span class="sxs-lookup"><span data-stu-id="e2069-175">It is an error if any _subpattern_ of a _property_pattern_ does not contain an _identifier_ (it must be of the second form, which has an _identifier_ ).</span></span>  <span data-ttu-id="e2069-176">Una virgola finale dopo l'ultimo criterio secondario è facoltativa.</span><span class="sxs-lookup"><span data-stu-id="e2069-176">A trailing comma after the last subpattern is optional.</span></span>

<span data-ttu-id="e2069-177">Si noti che un modello di controllo null rientra in un modello di proprietà semplice.</span><span class="sxs-lookup"><span data-stu-id="e2069-177">Note that a null-checking pattern falls out of a trivial property pattern.</span></span> <span data-ttu-id="e2069-178">Per verificare se la stringa `s` è non null, è possibile scrivere uno dei moduli seguenti</span><span class="sxs-lookup"><span data-stu-id="e2069-178">To check if the string `s` is non-null, you can write any of the following forms</span></span>

```csharp
if (s is object o) ... // o is of type object
if (s is string x) ... // x is of type string
if (s is {} x) ... // x is of type string
if (s is {}) ...
```

<span data-ttu-id="e2069-179">Data la corrispondenza di un'espressione *e* con il *tipo* di pattern `{` *property_pattern_list* `}` , si tratta di un errore in fase di compilazione se l'espressione *e* non è compatibile con i *modelli* con il tipo *T* designato dal *tipo* .</span><span class="sxs-lookup"><span data-stu-id="e2069-179">Given a match of an expression *e* to the pattern *type* `{` *property_pattern_list* `}`, it is a compile-time error if the expression *e* is not *pattern-compatible* with the type *T* designated by *type* .</span></span> <span data-ttu-id="e2069-180">Se il tipo è assente, viene importato come tipo statico di *e* .</span><span class="sxs-lookup"><span data-stu-id="e2069-180">If the type is absent, we take it to be the static type of *e* .</span></span> <span data-ttu-id="e2069-181">Se l' *identificatore* è presente, viene dichiarata una variabile di modello *di tipo Type.*</span><span class="sxs-lookup"><span data-stu-id="e2069-181">If the *identifier* is present, it declares a pattern variable of type *type* .</span></span> <span data-ttu-id="e2069-182">Ognuno degli identificatori visualizzati sul lato sinistro della relativa *property_pattern_list* deve designare una proprietà leggibile accessibile o un campo di *T* . Se è presente la *simple_designation* del *property_pattern* , definisce una variabile di tipo pattern *T* .</span><span class="sxs-lookup"><span data-stu-id="e2069-182">Each of the identifiers appearing on the left-hand-side of its *property_pattern_list* must designate an accessible readable property or field of *T* . If the *simple_designation* of the *property_pattern* is present, it defines a pattern variable of type *T* .</span></span>

<span data-ttu-id="e2069-183">In fase di esecuzione, l'espressione viene testata rispetto a *T* . Se l'operazione ha esito negativo, il modello di proprietà non riesce e il risultato è `false` .</span><span class="sxs-lookup"><span data-stu-id="e2069-183">At runtime, the expression is tested against *T* . If this fails then the property pattern match fails and the result is `false`.</span></span> <span data-ttu-id="e2069-184">Se ha esito positivo, ogni campo *property_subpattern* o proprietà viene letto e il relativo valore corrisponde al criterio corrispondente.</span><span class="sxs-lookup"><span data-stu-id="e2069-184">If it succeeds, then each *property_subpattern* field or property is read and its value matched against its corresponding pattern.</span></span> <span data-ttu-id="e2069-185">Il risultato dell'intera corrispondenza è `false` solo se il risultato di uno di questi è `false` .</span><span class="sxs-lookup"><span data-stu-id="e2069-185">The result of the whole match is `false` only if the result of any of these is `false`.</span></span> <span data-ttu-id="e2069-186">L'ordine in cui vengono confrontati i modelli non è specificato e una corrispondenza non riuscita potrebbe non corrispondere a tutti i sottomodelli in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="e2069-186">The order in which subpatterns are matched is not specified, and a failed match may not match all subpatterns at runtime.</span></span> <span data-ttu-id="e2069-187">Se la corrispondenza ha esito positivo e il *simple_designation* del *property_pattern* è un *single_variable_designation* , definisce una variabile di tipo *T* a cui viene assegnato il valore corrispondente.</span><span class="sxs-lookup"><span data-stu-id="e2069-187">If the match succeeds and the *simple_designation* of the *property_pattern* is a *single_variable_designation* , it defines a variable of type *T* that is assigned the matched value.</span></span>

> <span data-ttu-id="e2069-188">Nota: il modello di proprietà può essere usato per la corrispondenza dei modelli con i tipi anonimi.</span><span class="sxs-lookup"><span data-stu-id="e2069-188">Note: The property pattern can be used to pattern-match with anonymous types.</span></span>

##### <a name="example"></a><span data-ttu-id="e2069-189">Esempio</span><span class="sxs-lookup"><span data-stu-id="e2069-189">Example</span></span>

```csharp
if (o is string { Length: 5 } s)
```

### <a name="switch-expression"></a><span data-ttu-id="e2069-190">Espressione switch</span><span class="sxs-lookup"><span data-stu-id="e2069-190">Switch Expression</span></span>

<span data-ttu-id="e2069-191">Viene aggiunta una *switch_expression* alla `switch` semantica di tipo supporto per un contesto dell'espressione.</span><span class="sxs-lookup"><span data-stu-id="e2069-191">A *switch_expression* is added to support `switch`-like semantics for an expression context.</span></span>

<span data-ttu-id="e2069-192">La sintassi del linguaggio C# è aumentata con le seguenti produzioni sintattiche:</span><span class="sxs-lookup"><span data-stu-id="e2069-192">The C# language syntax is augmented with the following syntactic productions:</span></span>

```antlr
multiplicative_expression
    : switch_expression
    | multiplicative_expression '*' switch_expression
    | multiplicative_expression '/' switch_expression
    | multiplicative_expression '%' switch_expression
    ;
switch_expression
    : range_expression 'switch' '{' '}'
    | range_expression 'switch' '{' switch_expression_arms ','? '}'
    ;
switch_expression_arms
    : switch_expression_arm
    | switch_expression_arms ',' switch_expression_arm
    ;
switch_expression_arm
    : pattern case_guard? '=>' expression
    ;
case_guard
    : 'when' null_coalescing_expression
    ;
```

<span data-ttu-id="e2069-193">Il *switch_expression* non è consentito come *expression_statement* .</span><span class="sxs-lookup"><span data-stu-id="e2069-193">The *switch_expression* is not permitted as an *expression_statement* .</span></span>

> <span data-ttu-id="e2069-194">In una revisione futura si sta cercando di rilassarvi.</span><span class="sxs-lookup"><span data-stu-id="e2069-194">We are looking at relaxing this in a future revision.</span></span>

<span data-ttu-id="e2069-195">Il tipo di *switch_expression* è il [*tipo comune migliore*](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) delle espressioni visualizzate a destra dei `=>` token della *switch_expression_arm* s se tale tipo esiste e l'espressione in ogni ARM dell'espressione switch può essere convertita in modo implicito in tale tipo.</span><span class="sxs-lookup"><span data-stu-id="e2069-195">The type of the *switch_expression* is the [*best common type*](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) of the expressions appearing to the right of the `=>` tokens of the *switch_expression_arm* s if such a type exists and the expression in every arm of the switch expression can be implicitly converted to that type.</span></span>  <span data-ttu-id="e2069-196">Viene inoltre aggiunta una nuova conversione dell' *espressione switch* , ovvero una conversione implicita predefinita da un'espressione switch a ogni tipo per il `T` quale esiste una conversione implicita dall'espressione di ogni ARM a `T` .</span><span class="sxs-lookup"><span data-stu-id="e2069-196">In addition, we add a new *switch expression conversion* , which is a predefined implicit conversion from a switch expression to every type `T` for which there exists an implicit conversion from each arm's expression to `T`.</span></span>

<span data-ttu-id="e2069-197">Si tratta di un errore se il modello di un *switch_expression_arm* non può influenzare il risultato perché alcuni criteri e Guard precedenti corrisponderanno sempre.</span><span class="sxs-lookup"><span data-stu-id="e2069-197">It is an error if some *switch_expression_arm* 's pattern cannot affect the result because some previous pattern and guard will always match.</span></span>

<span data-ttu-id="e2069-198">Un'espressione switch è definita *esauriente* se un ARM dell'espressione switch gestisce ogni valore dell'input.</span><span class="sxs-lookup"><span data-stu-id="e2069-198">A switch expression is said to be *exhaustive* if some arm of the switch expression handles every value of its input.</span></span>  <span data-ttu-id="e2069-199">Il compilatore genera un avviso se un'espressione switch non è *completa* .</span><span class="sxs-lookup"><span data-stu-id="e2069-199">The compiler shall produce a warning if a switch expression is not *exhaustive* .</span></span>

<span data-ttu-id="e2069-200">In fase di esecuzione, il risultato della *switch_expression* è il valore dell' *espressione* del primo *switch_expression_arm* per il quale l'espressione sul lato sinistro del *switch_expression* corrisponde al modello del *switch_expression_arm* e per il quale il *case_guard* del *switch_expression_arm* , se presente, restituisce `true` .</span><span class="sxs-lookup"><span data-stu-id="e2069-200">At runtime, the result of the *switch_expression* is the value of the *expression* of the first *switch_expression_arm* for which the expression on the left-hand-side of the *switch_expression* matches the *switch_expression_arm* 's pattern, and for which the *case_guard* of the *switch_expression_arm* , if present, evaluates to `true`.</span></span> <span data-ttu-id="e2069-201">Se non esiste un *switch_expression_arm* di questo tipo, il *switch_expression* genera un'istanza dell'eccezione `System.Runtime.CompilerServices.SwitchExpressionException` .</span><span class="sxs-lookup"><span data-stu-id="e2069-201">If there is no such *switch_expression_arm* , the *switch_expression* throws an instance of the exception `System.Runtime.CompilerServices.SwitchExpressionException`.</span></span>

### <a name="optional-parens-when-switching-on-a-tuple-literal"></a><span data-ttu-id="e2069-202">Parentesi opzionali quando si passa a un valore letterale di tupla</span><span class="sxs-lookup"><span data-stu-id="e2069-202">Optional parens when switching on a tuple literal</span></span>

<span data-ttu-id="e2069-203">Per attivare un valore letterale di tupla usando il *switch_Statement* , è necessario scrivere le parentesi che sembrano essere ridondanti</span><span class="sxs-lookup"><span data-stu-id="e2069-203">In order to switch on a tuple literal using the *switch_statement* , you have to write what appear to be redundant parens</span></span>

```csharp
switch ((a, b))
{
```

<span data-ttu-id="e2069-204">Per consentire</span><span class="sxs-lookup"><span data-stu-id="e2069-204">To permit</span></span>

```csharp
switch (a, b)
{
```

<span data-ttu-id="e2069-205">le parentesi dell'istruzione switch sono facoltative quando l'espressione da attivare è un valore letterale di tupla.</span><span class="sxs-lookup"><span data-stu-id="e2069-205">the parentheses of the switch statement are optional when the expression being switched on is a tuple literal.</span></span>

### <a name="order-of-evaluation-in-pattern-matching"></a><span data-ttu-id="e2069-206">Ordine di valutazione nella corrispondenza dei modelli</span><span class="sxs-lookup"><span data-stu-id="e2069-206">Order of evaluation in pattern-matching</span></span>

<span data-ttu-id="e2069-207">Fornire la flessibilità del compilatore per riordinare le operazioni eseguite durante la corrispondenza dei modelli può consentire la flessibilità che può essere usata per migliorare l'efficienza della corrispondenza dei modelli.</span><span class="sxs-lookup"><span data-stu-id="e2069-207">Giving the compiler flexibility in reordering the operations executed during pattern-matching can permit flexibility that can be used to improve the efficiency of pattern-matching.</span></span> <span data-ttu-id="e2069-208">Il requisito (non applicato) prevede che le proprietà a cui si accede in un modello e i metodi di decostruzione debbano essere "pure" (effetto collaterale gratuito, idempotente e così via).</span><span class="sxs-lookup"><span data-stu-id="e2069-208">The (unenforced) requirement would be that properties accessed in a pattern, and the Deconstruct methods, are required to be "pure" (side-effect free, idempotent, etc).</span></span> <span data-ttu-id="e2069-209">Ciò non significa che verrà aggiunta la purezza come concetto di linguaggio, ma solo che la flessibilità del compilatore verrà riordinata.</span><span class="sxs-lookup"><span data-stu-id="e2069-209">That doesn't mean that we would add purity as a language concept, only that we would allow the compiler flexibility in reordering operations.</span></span>

<span data-ttu-id="e2069-210">**Risoluzione 2018-04-04 LDM** : confermato: il compilatore è autorizzato a riordinare le chiamate a `Deconstruct` , gli accessi alle proprietà e le chiamate dei metodi in `ITuple` e può presumere che i valori restituiti siano gli stessi da più chiamate.</span><span class="sxs-lookup"><span data-stu-id="e2069-210">**Resolution 2018-04-04 LDM** : confirmed: the compiler is permitted to reorder calls to `Deconstruct`, property accesses, and invocations of methods in `ITuple`, and may assume that returned values are the same from multiple calls.</span></span> <span data-ttu-id="e2069-211">Il compilatore non deve richiamare funzioni che non possono influire sul risultato e sarà necessario prestare particolare attenzione prima di apportare eventuali modifiche all'ordine di valutazione generato dal compilatore in futuro.</span><span class="sxs-lookup"><span data-stu-id="e2069-211">The compiler should not invoke functions that cannot affect the result, and we will be very careful before making any changes to the compiler-generated order of evaluation in the future.</span></span>

### <a name="some-possible-optimizations"></a><span data-ttu-id="e2069-212">Alcune possibili ottimizzazioni</span><span class="sxs-lookup"><span data-stu-id="e2069-212">Some Possible Optimizations</span></span>

<span data-ttu-id="e2069-213">La compilazione di criteri di ricerca può trarre vantaggio da parti comuni di modelli.</span><span class="sxs-lookup"><span data-stu-id="e2069-213">The compilation of pattern matching can take advantage of common parts of patterns.</span></span> <span data-ttu-id="e2069-214">Se, ad esempio, il test del tipo di primo livello di due modelli successivi in una *switch_Statement* è dello stesso tipo, il codice generato può ignorare il test del tipo per il secondo modello.</span><span class="sxs-lookup"><span data-stu-id="e2069-214">For example, if the top-level type test of two successive patterns in a *switch_statement* is the same type, the generated code can skip the type test for the second pattern.</span></span>

<span data-ttu-id="e2069-215">Quando alcuni dei modelli sono numeri interi o stringhe, il compilatore può generare lo stesso tipo di codice generato per un'istruzione switch nelle versioni precedenti del linguaggio.</span><span class="sxs-lookup"><span data-stu-id="e2069-215">When some of the patterns are integers or strings, the compiler can generate the same kind of code it generates for a switch-statement in earlier versions of the language.</span></span>

<span data-ttu-id="e2069-216">Per ulteriori informazioni su questi tipi di ottimizzazioni, vedere [[Scott e Ramsey (2000)]](https://www.cs.tufts.edu/~nr/cs257/archive/norman-ramsey/match.pdf "Quando Match-Compilation euristica è importante?").</span><span class="sxs-lookup"><span data-stu-id="e2069-216">For more on these kinds of optimizations, see [[Scott and Ramsey (2000)]](https://www.cs.tufts.edu/~nr/cs257/archive/norman-ramsey/match.pdf "When Do Match-Compilation Heuristics Matter?").</span></span>
