---
ms.openlocfilehash: 57cdb682efd4cb169308e347d63e27c97b9f1b7a
ms.sourcegitcommit: 89bbdd101f539cefd41b2b8b9087fc9cdbcf3c62
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87412485"
---
# <a name="pattern-matching-changes-for-c-90"></a><span data-ttu-id="a100f-101">Modifiche ai criteri di ricerca per C# 9,0</span><span class="sxs-lookup"><span data-stu-id="a100f-101">Pattern-matching changes for C# 9.0</span></span>

<span data-ttu-id="a100f-102">Si sta considerando un piccolo numero di miglioramenti apportati alla corrispondenza dei modelli per C# 9,0 che hanno una sinergia naturale e funzionano bene per risolvere una serie di problemi comuni di programmazione:</span><span class="sxs-lookup"><span data-stu-id="a100f-102">We are considering a small handful of enhancements to pattern-matching for C# 9.0 that have natural synergy and work well to address a number of common programming problems:</span></span>
- <span data-ttu-id="a100f-103">https://github.com/dotnet/csharplang/issues/2925Modelli di tipo</span><span class="sxs-lookup"><span data-stu-id="a100f-103">https://github.com/dotnet/csharplang/issues/2925 Type patterns</span></span>
- <span data-ttu-id="a100f-104">https://github.com/dotnet/csharplang/issues/1350Modelli tra parentesi per applicare o evidenziare la precedenza dei nuovi combinatori</span><span class="sxs-lookup"><span data-stu-id="a100f-104">https://github.com/dotnet/csharplang/issues/1350 Parenthesized patterns to enforce or emphasize precedence of the new combinators</span></span>
- <span data-ttu-id="a100f-105">https://github.com/dotnet/csharplang/issues/1350`and`Modelli congiuntiva che richiedono la corrispondenza di due modelli diversi;</span><span class="sxs-lookup"><span data-stu-id="a100f-105">https://github.com/dotnet/csharplang/issues/1350 Conjunctive `and` patterns that require both of two different patterns to match;</span></span>
- <span data-ttu-id="a100f-106">https://github.com/dotnet/csharplang/issues/1350`or`Modelli disgiuntiva che richiedono uno dei due modelli diversi per la corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="a100f-106">https://github.com/dotnet/csharplang/issues/1350 Disjunctive `or` patterns that require either of two different patterns to match;</span></span>
- <span data-ttu-id="a100f-107">https://github.com/dotnet/csharplang/issues/1350Modelli negati `not` che richiedono un modello specificato di *non* corrispondenza; e</span><span class="sxs-lookup"><span data-stu-id="a100f-107">https://github.com/dotnet/csharplang/issues/1350 Negated `not` patterns that require a given pattern *not* to match; and</span></span>
- <span data-ttu-id="a100f-108">https://github.com/dotnet/csharplang/issues/812Modelli relazionali che richiedono che il valore di input sia minore di, minore o uguale a e così via, una costante specificata.</span><span class="sxs-lookup"><span data-stu-id="a100f-108">https://github.com/dotnet/csharplang/issues/812 Relational patterns that require the input value to be less than, less than or equal to, etc a given constant.</span></span>

## <a name="parenthesized-patterns"></a><span data-ttu-id="a100f-109">Modelli tra parentesi</span><span class="sxs-lookup"><span data-stu-id="a100f-109">Parenthesized Patterns</span></span>

<span data-ttu-id="a100f-110">I modelli tra parentesi consentono al programmatore di inserire parentesi intorno a qualsiasi modello.</span><span class="sxs-lookup"><span data-stu-id="a100f-110">Parenthesized patterns permit the programmer to put parentheses around any pattern.</span></span>  <span data-ttu-id="a100f-111">Questa operazione non è così utile con i modelli esistenti in C# 8,0, tuttavia i nuovi combinatori di modelli introducono la precedenza che il programmatore potrebbe voler sottoporre a override.</span><span class="sxs-lookup"><span data-stu-id="a100f-111">This is not so useful with the existing patterns in C# 8.0, however the new pattern combinators introduce a precedence that the programmer may want to override.</span></span>

```antlr
primary_pattern
    : parenthesized_pattern
    | // all of the existing forms
    ;
parenthesized_pattern
    : '(' pattern ')'
    ;
```

## <a name="type-patterns"></a><span data-ttu-id="a100f-112">Modelli di tipo</span><span class="sxs-lookup"><span data-stu-id="a100f-112">Type Patterns</span></span>

<span data-ttu-id="a100f-113">Si consente un tipo come modello:</span><span class="sxs-lookup"><span data-stu-id="a100f-113">We permit a type as a pattern:</span></span>

``` antlr
primary_pattern
    : type-pattern
    | // all of the existing forms
    ;
type_pattern
    : type
    ;
```

<span data-ttu-id="a100f-114">Questo retcons l'oggetto *is-Type-Expression* esistente come oggetto *is-pattern-Expression* , in cui il modello è un *modello di tipo*, anche se non si modifica l'albero della sintassi prodotto dal compilatore.</span><span class="sxs-lookup"><span data-stu-id="a100f-114">This retcons the existing *is-type-expression* to be an *is-pattern-expression* in which the pattern is a *type-pattern*, though we would not change the syntax tree produced by the compiler.</span></span>

<span data-ttu-id="a100f-115">Un problema di implementazione sottile è che questa grammatica è ambigua.</span><span class="sxs-lookup"><span data-stu-id="a100f-115">One subtle implementation issue is that this grammar is ambiguous.</span></span>  <span data-ttu-id="a100f-116">Una stringa come `a.b` può essere analizzata come nome completo (in un contesto di tipo) o come espressione tratteggiata (in un contesto di espressione).</span><span class="sxs-lookup"><span data-stu-id="a100f-116">A string such as `a.b` can be parsed either as a qualified name (in a type context) or a dotted expression (in an expression context).</span></span>  <span data-ttu-id="a100f-117">Il compilatore è già in grado di trattare un nome qualificato allo stesso modo di un'espressione punteggiata per gestire qualcosa di simile a `e is Color.Red` .</span><span class="sxs-lookup"><span data-stu-id="a100f-117">The compiler is already capable of treating a qualified name the same as a dotted expression in order to handle something like `e is Color.Red`.</span></span>  <span data-ttu-id="a100f-118">L'analisi semantica del compilatore viene estesa ulteriormente per essere in grado di associare un criterio costante (sintattico), ad esempio un'espressione tratteggiata, come un tipo, in modo da considerarlo come modello di tipo associato per supportare questo costrutto.</span><span class="sxs-lookup"><span data-stu-id="a100f-118">The compiler's semantic analysis would be further extended to be capable of binding a (syntactic) constant pattern (e.g. a dotted expression) as a type in order to treat it as a bound type pattern in order to support this construct.</span></span>

<span data-ttu-id="a100f-119">Dopo questa modifica, sarà possibile scrivere</span><span class="sxs-lookup"><span data-stu-id="a100f-119">After this change, you would be able to write</span></span>
```csharp
void M(object o1, object o2)
{
    var t = (o1, o2);
    if (t is (int, string)) {} // test if o1 is an int and o2 is a string
    switch (o1) {
        case int: break; // test if o1 is an int
        case System.String: break; // test if o1 is a string
    }
}
```

## <a name="relational-patterns"></a><span data-ttu-id="a100f-120">Modelli relazionali</span><span class="sxs-lookup"><span data-stu-id="a100f-120">Relational Patterns</span></span>

<span data-ttu-id="a100f-121">I modelli relazionali consentono al programmatore di esprimere che un valore di input deve soddisfare un vincolo relazionale rispetto a un valore costante:</span><span class="sxs-lookup"><span data-stu-id="a100f-121">Relational patterns permit the programmer to express that an input value must satisfy a relational constraint when compared to a constant value:</span></span>

``` C#
    public static LifeStage LifeStageAtAge(int age) => age switch
    {
        < 0 =>  LifeStage.Prenatal,
        < 2 =>  LifeStage.Infant,
        < 4 =>  LifeStage.Toddler,
        < 6 =>  LifeStage.EarlyChild,
        < 12 => LifeStage.MiddleChild,
        < 20 => LifeStage.Adolescent,
        < 40 => LifeStage.EarlyAdult,
        < 65 => LifeStage.MiddleAdult,
        _ =>    LifeStage.LateAdult,
    };
```

<span data-ttu-id="a100f-122">I modelli relazionali supportano gli operatori relazionali `<` ,, `<=` `>` e `>=` su tutti i tipi incorporati che supportano tali operatori relazionali binari con due operandi dello stesso tipo in un'espressione.</span><span class="sxs-lookup"><span data-stu-id="a100f-122">Relational patterns support the relational operators `<`, `<=`, `>`, and `>=` on all of the built-in types that support such binary relational operators with two operands of the same type in an expression.</span></span> <span data-ttu-id="a100f-123">In particolare, sono supportati tutti questi modelli relazionali per `sbyte` , `byte` , `short` , `ushort` , `int` , `uint` , `long` , `ulong` , `char` , `float` , `double` , `decimal` , `nint` e `nuint` .</span><span class="sxs-lookup"><span data-stu-id="a100f-123">Specifically, we support all of these relational patterns for `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `nint`, and `nuint`.</span></span>

```antlr
primary_pattern
    : relational_pattern
    ;
relational_pattern
    : '<' relational_expression
    | '<=' relational_expression
    | '>' relational_expression
    | '>=' relational_expression
    ;
```

<span data-ttu-id="a100f-124">L'espressione è obbligatoria per restituire un valore costante.</span><span class="sxs-lookup"><span data-stu-id="a100f-124">The expression is required to evaluate to a constant value.</span></span>  <span data-ttu-id="a100f-125">Si tratta di un errore se il valore costante è `double.NaN` o `float.NaN` .</span><span class="sxs-lookup"><span data-stu-id="a100f-125">It is an error if that constant value is `double.NaN` or `float.NaN`.</span></span>  <span data-ttu-id="a100f-126">Si tratta di un errore se l'espressione è una costante null.</span><span class="sxs-lookup"><span data-stu-id="a100f-126">It is an error if the expression is a null constant.</span></span>

<span data-ttu-id="a100f-127">Quando l'input è un tipo per cui viene definito un operatore relazionale binario appropriato, applicabile all'input come operando sinistro e la costante specificata come operando destro, la valutazione di tale operatore viene considerata come il significato del modello relazionale.</span><span class="sxs-lookup"><span data-stu-id="a100f-127">When the input is a type for which a suitable built-in binary relational operator is defined that is applicable with the input as its left operand and the given constant as its right operand, the evaluation of that operator is taken as the meaning of the relational pattern.</span></span>  <span data-ttu-id="a100f-128">In caso contrario, l'input viene convertito nel tipo dell'espressione utilizzando una conversione esplicita che ammette i valori null o unboxing.</span><span class="sxs-lookup"><span data-stu-id="a100f-128">Otherwise we convert the input to the type of the expression using an explicit nullable or unboxing conversion.</span></span>  <span data-ttu-id="a100f-129">Si tratta di un errore in fase di compilazione se non esiste alcuna conversione di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="a100f-129">It is a compile-time error if no such conversion exists.</span></span>  <span data-ttu-id="a100f-130">Il modello viene considerato non corrispondente se la conversione non riesce.</span><span class="sxs-lookup"><span data-stu-id="a100f-130">The pattern is considered not to match if the conversion fails.</span></span>  <span data-ttu-id="a100f-131">Se la conversione ha esito positivo, il risultato dell'operazione di corrispondenza dei criteri è il risultato della valutazione dell'espressione in `e OP v` cui `e` è l'input convertito, `OP` è l'operatore relazionale e `v` è l'espressione costante.</span><span class="sxs-lookup"><span data-stu-id="a100f-131">If the conversion succeeds then the result of the pattern-matching operation is the result of evaluating the expression `e OP v` where `e` is the converted input, `OP` is the relational operator, and `v` is the constant expression.</span></span>

## <a name="pattern-combinators"></a><span data-ttu-id="a100f-132">Combinatori di modelli</span><span class="sxs-lookup"><span data-stu-id="a100f-132">Pattern Combinators</span></span>

<span data-ttu-id="a100f-133">I *combinatori* di pattern consentono di abbinare due modelli diversi usando `and` (questo può essere esteso a un numero qualsiasi di modelli mediante l'uso ripetuto di `and` ), uno dei due modelli diversi usando `or` (Ditto) o la *negazione* di un modello usando `not` .</span><span class="sxs-lookup"><span data-stu-id="a100f-133">Pattern *combinators* permit matching both of two different patterns using `and` (this can be extended to any number of patterns by the repeated use of `and`), either of two different patterns using `or` (ditto), or the *negation* of a pattern using `not`.</span></span>

<span data-ttu-id="a100f-134">Un uso comune di un combinatore sarà l'idioma</span><span class="sxs-lookup"><span data-stu-id="a100f-134">A common use of a combinator will be the idiom</span></span>

``` c#
if (e is not null) ...
```

<span data-ttu-id="a100f-135">Più leggibile dell'idioma corrente `e is object` , questo modello esprime chiaramente che è in corso la verifica di un valore non null.</span><span class="sxs-lookup"><span data-stu-id="a100f-135">More readable than the current idiom `e is object`, this pattern clearly expresses that one is checking for a non-null value.</span></span>

<span data-ttu-id="a100f-136">I `and` `or` combinatori e saranno utili per il test degli intervalli di valori</span><span class="sxs-lookup"><span data-stu-id="a100f-136">The `and` and `or` combinators will be useful for testing ranges of values</span></span>

``` c#
bool IsLetter(char c) => c is >= 'a' and <= 'z' or >= 'A' and <= 'Z';
```

<span data-ttu-id="a100f-137">Questo esempio illustra che `and` avrà una priorità di analisi più elevata (ad esempio, verrà associata più a vicino) rispetto a `or` .</span><span class="sxs-lookup"><span data-stu-id="a100f-137">This example illustrates that `and` will have a higher parsing priority (i.e. will bind more closely) than `or`.</span></span>  <span data-ttu-id="a100f-138">Il programmatore può usare il *modello racchiuso tra parentesi* per rendere esplicita la precedenza:</span><span class="sxs-lookup"><span data-stu-id="a100f-138">The programmer can use the *parenthesized pattern* to make the precedence explicit:</span></span>

``` c#
bool IsLetter(char c) => c is (>= 'a' and <= 'z') or (>= 'A' and <= 'Z');
```

<span data-ttu-id="a100f-139">Come tutti i modelli, questi combinatori possono essere usati in qualsiasi contesto in cui è previsto un modello, inclusi i modelli annidati, *is-pattern-Expression*, *Switch-Expression*e il modello dell'etichetta case di un'istruzione switch.</span><span class="sxs-lookup"><span data-stu-id="a100f-139">Like all patterns, these combinators can be used in any context in which a pattern is expected, including nested patterns, the *is-pattern-expression*, the *switch-expression*, and the pattern of a switch statement's case label.</span></span>

```antlr
pattern
    : disjunctive_pattern
    ;
disjunctive_pattern
    : disjunctive_pattern 'or' conjunctive_pattern
    | conjunctive_pattern
    ;
conjunctive_pattern
    : conjunctive_pattern 'and' negated_pattern
    | negated_pattern
    ;
negated_pattern
    : 'not' negated_pattern
    | primary_pattern
    ;
primary_pattern
    : // all of the patterns forms previously defined
    ;
```

## <a name="open-issues-with-proposed-changes"></a><span data-ttu-id="a100f-140">Problemi aperti con le modifiche proposte</span><span class="sxs-lookup"><span data-stu-id="a100f-140">Open Issues with Proposed Changes</span></span>

### <a name="syntax-for-relational-operators"></a><span data-ttu-id="a100f-141">Sintassi per gli operatori relazionali</span><span class="sxs-lookup"><span data-stu-id="a100f-141">Syntax for relational operators</span></span>

<span data-ttu-id="a100f-142">Sono `and` , `or` e `not` un tipo di parola chiave contestuale?</span><span class="sxs-lookup"><span data-stu-id="a100f-142">Are `and`, `or`, and `not` some kind of contextual keyword?</span></span>  <span data-ttu-id="a100f-143">In tal caso, è presente una modifica di rilievo, ad esempio rispetto al loro utilizzo come indicatore in un *modello di dichiarazione*.</span><span class="sxs-lookup"><span data-stu-id="a100f-143">If so, is there a breaking change (e.g. compared to their use as a designator in a *declaration-pattern*).</span></span>

### <a name="semantics-eg-type-for-relational-operators"></a><span data-ttu-id="a100f-144">Semantica (ad esempio, tipo) per gli operatori relazionali</span><span class="sxs-lookup"><span data-stu-id="a100f-144">Semantics (e.g. type) for relational operators</span></span>

<span data-ttu-id="a100f-145">Si prevede di supportare tutti i tipi primitivi che possono essere confrontati in un'espressione utilizzando un operatore relazionale.</span><span class="sxs-lookup"><span data-stu-id="a100f-145">We expect to support all of the primitive types that can be compared in an expression using a relational operator.</span></span>  <span data-ttu-id="a100f-146">Il significato nei casi semplici è chiaro</span><span class="sxs-lookup"><span data-stu-id="a100f-146">The meaning in simple cases is clear</span></span>

``` c#
bool IsValidPercentage(int x) => x is >= 0 and <= 100;
```

<span data-ttu-id="a100f-147">Tuttavia, quando l'input non è un tipo primitivo, a quale tipo si tenta di convertirlo?</span><span class="sxs-lookup"><span data-stu-id="a100f-147">But when the input is not such a primitive type, what type do we attempt to convert it to?</span></span>

``` c#
bool IsValidPercentage(object x) => x is >= 0 and <= 100;
```

<span data-ttu-id="a100f-148">Si è proposto che quando il tipo di input è già una primitiva paragonabile, ovvero il tipo di confronto.</span><span class="sxs-lookup"><span data-stu-id="a100f-148">We have proposed that when the input type is already a comparable primitive, that is the type of the comparison.</span></span> <span data-ttu-id="a100f-149">Tuttavia, quando l'input non è una primitiva paragonabile, consideriamo il tipo relazionale come incluso un test di tipo implicito al tipo della costante sul lato destro della relazione.</span><span class="sxs-lookup"><span data-stu-id="a100f-149">However, when the input is not a comparable primitive, we treat the relational as including an implicit type test to the type of the constant on the right-hand-side of the relational.</span></span>  <span data-ttu-id="a100f-150">Se il programmatore intende supportare più di un tipo di input, è necessario eseguire questa operazione in modo esplicito:</span><span class="sxs-lookup"><span data-stu-id="a100f-150">If the programmer intends to support more than one input type, that must be done explicitly:</span></span>

``` c#
bool IsValidPercentage(object x) => x is
    >= 0 and <= 100 or    // integer tests
    >= 0F and <= 100F or  // float tests
    >= 0D and <= 100D;    // double tests
```

### <a name="flowing-type-information-from-the-left-to-the-right-of-and"></a><span data-ttu-id="a100f-151">Flusso delle informazioni sul tipo da sinistra a destra di`and`</span><span class="sxs-lookup"><span data-stu-id="a100f-151">Flowing type information from the left to the right of `and`</span></span>

<span data-ttu-id="a100f-152">È stato suggerito che, quando si scrive un `and` combinatore, le informazioni sul tipo acquisite a sinistra sul tipo di primo livello possono propagarsi a destra.</span><span class="sxs-lookup"><span data-stu-id="a100f-152">It has been suggested that when you write an `and` combinator, type information learned on the left about the top-level type could flow to the right.</span></span>  <span data-ttu-id="a100f-153">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a100f-153">For example</span></span>

```csharp
bool isSmallByte(object o) => o is byte and < 100;
```

<span data-ttu-id="a100f-154">In questo caso, il *tipo di input* per il secondo modello viene ridotto in base ai requisiti di *riduzione del tipo* a sinistra di `and` .</span><span class="sxs-lookup"><span data-stu-id="a100f-154">Here, the *input type* to the second pattern is narrowed by the *type narrowing* requirements of left of the `and`.</span></span>  <span data-ttu-id="a100f-155">Si definisce la semantica di riduzione dei tipi per tutti i modelli come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="a100f-155">We would define type narrowing semantics for all patterns as follows.</span></span>  <span data-ttu-id="a100f-156">Il *tipo limitato* di un modello `P` viene definito nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="a100f-156">The *narrowed type* of a pattern `P` is defined as follows:</span></span>
1. <span data-ttu-id="a100f-157">Se `P` è un modello di tipo, il tipo *limitato* è il tipo del tipo del modello di tipo.</span><span class="sxs-lookup"><span data-stu-id="a100f-157">If `P` is a type pattern, the *narrowed type* is the type of the type pattern's type.</span></span>
2. <span data-ttu-id="a100f-158">Se `P` è un modello di dichiarazione, il tipo *limitato* è il tipo del tipo del modello di dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="a100f-158">If `P` is a declaration pattern, the *narrowed type* is the type of the declaration pattern's type.</span></span>
3. <span data-ttu-id="a100f-159">Se `P` è un modello ricorsivo che fornisce un tipo esplicito, il tipo *limitato* è quel tipo.</span><span class="sxs-lookup"><span data-stu-id="a100f-159">If `P` is a recursive pattern that gives an explicit type, the *narrowed type* is that type.</span></span>
4. <span data-ttu-id="a100f-160">Se `P` è un criterio costante in cui la costante non è la costante null e in cui l'espressione non dispone di una *conversione di espressione costante* nel *tipo di input*, il *tipo limitato* è il tipo della costante.</span><span class="sxs-lookup"><span data-stu-id="a100f-160">If `P` is a constant pattern where the constant is not the null constant and where the expression has no *constant expression conversion* to the *input type*, the *narrowed type* is the type of the constant.</span></span>
5. <span data-ttu-id="a100f-161">Se `P` è un modello relazionale in cui l'espressione costante non ha una *conversione dell'espressione costante* nel *tipo di input*, il *tipo limitato* è il tipo della costante.</span><span class="sxs-lookup"><span data-stu-id="a100f-161">If `P` is a relational pattern where the constant expression has no *constant expression conversion* to the *input type*, the *narrowed type* is the type of the constant.</span></span>
6. <span data-ttu-id="a100f-162">Se `P` è un `or` modello, il tipo *limitato* è il tipo comune del *tipo ristretto* dei criteri di ricerca se tale tipo comune esiste.</span><span class="sxs-lookup"><span data-stu-id="a100f-162">If `P` is an `or` pattern, the *narrowed type* is the common type of the *narrowed type* of the subpatterns if such a common type exists.</span></span> <span data-ttu-id="a100f-163">A questo scopo, l'algoritmo di tipo comune considera solo le conversioni di identità, Boxing e riferimento implicito e considera tutti i sottomodelli di una sequenza di `or` modelli (ignorando i modelli racchiusi tra parentesi).</span><span class="sxs-lookup"><span data-stu-id="a100f-163">For this purpose, the common type algorithm considers only identity, boxing, and implicit reference conversions, and it considers all subpatterns of a sequence of `or` patterns (ignoring parenthesized patterns).</span></span>
7. <span data-ttu-id="a100f-164">Se `P` è un `and` modello, il *tipo ristretto* è il *tipo limitato* del criterio destro.</span><span class="sxs-lookup"><span data-stu-id="a100f-164">If `P` is an `and` pattern, the *narrowed type* is the *narrowed type* of the right pattern.</span></span> <span data-ttu-id="a100f-165">Inoltre, il *tipo limitato* del criterio di sinistra è il *tipo di input* del criterio destro.</span><span class="sxs-lookup"><span data-stu-id="a100f-165">Moreover, the *narrowed type* of the left pattern is the *input type* of the right pattern.</span></span>
8. <span data-ttu-id="a100f-166">In caso *narrowed type* contrario, il tipo di `P` input di è limitato `P` .</span><span class="sxs-lookup"><span data-stu-id="a100f-166">Otherwise the *narrowed type* of `P` is `P`'s input type.</span></span>

### <a name="variable-definitions-and-definite-assignment"></a><span data-ttu-id="a100f-167">Definizioni di variabili e assegnazione definitiva</span><span class="sxs-lookup"><span data-stu-id="a100f-167">Variable definitions and definite assignment</span></span>

<span data-ttu-id="a100f-168">L'aggiunta di `or` `not` modelli e consente di creare alcuni nuovi problemi interessanti sulle variabili del modello e sull'assegnazione definita.</span><span class="sxs-lookup"><span data-stu-id="a100f-168">The addition of `or` and `not` patterns creates some interesting new problems around pattern variables and definite assignment.</span></span>  <span data-ttu-id="a100f-169">Poiché le variabili possono in genere essere dichiarate al massimo una volta, sembrerebbe che qualsiasi variabile di pattern dichiarata su un lato di un `or` modello non venisse assegnata definitivamente quando il modello corrisponde.</span><span class="sxs-lookup"><span data-stu-id="a100f-169">Since variables can normally be declared at most once, it would seem any pattern variable declared on one side of an `or` pattern would not be definitely assigned when the pattern matches.</span></span>  <span data-ttu-id="a100f-170">Analogamente, una variabile dichiarata all'interno di un `not` modello non dovrebbe essere assegnata definitivamente quando il modello corrisponde a.</span><span class="sxs-lookup"><span data-stu-id="a100f-170">Similarly, a variable declared inside a `not` pattern would not be expected to be definitely assigned when the pattern matches.</span></span>  <span data-ttu-id="a100f-171">Il modo più semplice per risolvere questo problema è impedire la dichiarazione di variabili di modello in questi contesti.</span><span class="sxs-lookup"><span data-stu-id="a100f-171">The simplest way to address this is to forbid declaring pattern variables in these contexts.</span></span>  <span data-ttu-id="a100f-172">Tuttavia, questa operazione potrebbe essere troppo restrittiva.</span><span class="sxs-lookup"><span data-stu-id="a100f-172">However, this may be too restrictive.</span></span>  <span data-ttu-id="a100f-173">Ci sono altri approcci da considerare.</span><span class="sxs-lookup"><span data-stu-id="a100f-173">There are other approaches to consider.</span></span>

<span data-ttu-id="a100f-174">Uno scenario che vale la pena considerare è il seguente</span><span class="sxs-lookup"><span data-stu-id="a100f-174">One scenario that is worth considering is this</span></span>

``` csharp
if (e is not int i) return;
M(i); // is i definitely assigned here?
```

<span data-ttu-id="a100f-175">Questa operazione non funziona oggi perché, per un *is-pattern-Expression*, le variabili del modello vengono considerate assegnate in modo *sicuro* solo quando l'oggetto *is-pattern-Expression* è true ("assegnato definitivamente se true").</span><span class="sxs-lookup"><span data-stu-id="a100f-175">This does not work today because, for an *is-pattern-expression*, the pattern variables are considered *definitely assigned* only where the *is-pattern-expression* is true ("definitely assigned when true").</span></span>

<span data-ttu-id="a100f-176">Il supporto di questo metodo sarebbe più semplice (dal punto di vista del programmatore) rispetto all'aggiunta del supporto per un'istruzione di condizione negata `if` .</span><span class="sxs-lookup"><span data-stu-id="a100f-176">Supporting this would be simpler (from the programmer's perspective) than also adding support for a negated-condition `if` statement.</span></span>  <span data-ttu-id="a100f-177">Anche se si aggiunge tale supporto, i programmatori chiederanno perché il frammento di codice precedente non funziona.</span><span class="sxs-lookup"><span data-stu-id="a100f-177">Even if we add such support, programmers would wonder why the above snippet does not work.</span></span>  <span data-ttu-id="a100f-178">D'altra parte, lo stesso scenario in a `switch` è meno appropriato, in quanto non esiste un punto corrispondente nel programma in cui l' *assegnazione è assegnata quando false* sarebbe significativa.</span><span class="sxs-lookup"><span data-stu-id="a100f-178">On the other hand, the same scenario in a `switch` makes less sense, as there is no corresponding point in the program where *definitely assigned when false* would be meaningful.</span></span>  <span data-ttu-id="a100f-179">Questa operazione è consentita in un *modello is-pattern-Expression,* ma non in altri contesti in cui sono consentiti i modelli?</span><span class="sxs-lookup"><span data-stu-id="a100f-179">Would we permit this in an *is-pattern-expression* but not in other contexts where patterns are permitted?</span></span>  <span data-ttu-id="a100f-180">Sembra irregolare.</span><span class="sxs-lookup"><span data-stu-id="a100f-180">That seems irregular.</span></span>

<span data-ttu-id="a100f-181">Correlato a questo è il problema dell'assegnazione definita in un *modello disgiuntiva*.</span><span class="sxs-lookup"><span data-stu-id="a100f-181">Related to this is the problem of definite assignment in a *disjunctive-pattern*.</span></span>

```csharp
if (e is 0 or int i)
{
    M(i); // is i definitely assigned here?
}
```

<span data-ttu-id="a100f-182">Si prevede che `i` venga assegnato solo se l'input è diverso da zero.</span><span class="sxs-lookup"><span data-stu-id="a100f-182">We would only expect `i` to be definitely assigned when the input is not zero.</span></span>  <span data-ttu-id="a100f-183">Tuttavia, poiché non è noto se l'input è zero o non è presente nel blocco, `i` non viene assegnato definitivamente.</span><span class="sxs-lookup"><span data-stu-id="a100f-183">But since we don't know whether the input is zero or not inside the block, `i` is not definitely assigned.</span></span>  <span data-ttu-id="a100f-184">Tuttavia, cosa accade se si concede la `i` dichiarazione in modelli diversi che si escludono a vicenda?</span><span class="sxs-lookup"><span data-stu-id="a100f-184">However, what if we permit `i` to be declared in different mutually exclusive patterns?</span></span>

```csharp
if ((e1, e2) is (0, int i) or (int i, 0))
{
    M(i);
}
```

<span data-ttu-id="a100f-185">In questo caso la variabile `i` viene assegnata in modo sicuro all'interno del blocco e accetta il valore dall'altro elemento della tupla quando viene trovato un elemento zero.</span><span class="sxs-lookup"><span data-stu-id="a100f-185">Here, the variable `i` is definitely assigned inside the block, and takes it value from the other element of the tuple when a zero element is found.</span></span>

<span data-ttu-id="a100f-186">È stato inoltre suggerito di consentire le variabili da (moltiplicare) definite in ogni caso di un blocco case:</span><span class="sxs-lookup"><span data-stu-id="a100f-186">It has also been suggested to permit variables to be (multiply) defined in every case of a case block:</span></span>

```csharp
    case (0, int x):
    case (int x, 0):
        Console.WriteLine(x);
```

<span data-ttu-id="a100f-187">Per eseguire una di queste operazioni, è necessario definire con attenzione il punto in cui sono consentite tali definizioni e in quali condizioni tale variabile viene considerata definitivamente assegnata.</span><span class="sxs-lookup"><span data-stu-id="a100f-187">To make any of this work, we would have to carefully define where such multiple definitions are permitted and under what conditions such a variable is considered definitely assigned.</span></span>

<span data-ttu-id="a100f-188">Se decidiamo di rinviare tale lavoro fino a un secondo momento (che Consiglio), potremmo dire in C# 9</span><span class="sxs-lookup"><span data-stu-id="a100f-188">Should we elect to defer such work until later (which I advise), we could say in C# 9</span></span>
- <span data-ttu-id="a100f-189">sotto un oggetto `not` o `or` , le variabili del modello non possono essere dichiarate.</span><span class="sxs-lookup"><span data-stu-id="a100f-189">beneath a `not` or `or`, pattern variables may not be declared.</span></span>

<span data-ttu-id="a100f-190">Quindi, avremmo tempo per sviluppare una certa esperienza che forniva informazioni sul possibile valore di relax che in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="a100f-190">Then, we would have time to develop some experience that would provide insight into the possible value of relaxing that later.</span></span>

### <a name="diagnostics-subsumption-and-exhaustiveness"></a><span data-ttu-id="a100f-191">Diagnostica, sussunzione e esaustività</span><span class="sxs-lookup"><span data-stu-id="a100f-191">Diagnostics, subsumption, and exhaustiveness</span></span>

<span data-ttu-id="a100f-192">Questi nuovi moduli presentano molte nuove opportunità per l'errore del programmatore diagnosticabile.</span><span class="sxs-lookup"><span data-stu-id="a100f-192">These new pattern forms introduce many new opportunities for diagnosable programmer error.</span></span>  <span data-ttu-id="a100f-193">È necessario decidere quali tipi di errori si eseguiranno per la diagnosi e come farlo.</span><span class="sxs-lookup"><span data-stu-id="a100f-193">We will need to decide what kinds of errors we will diagnose, and how to do so.</span></span>  <span data-ttu-id="a100f-194">Di seguito sono riportati alcuni esempi:</span><span class="sxs-lookup"><span data-stu-id="a100f-194">Here are some examples:</span></span>

``` csharp
case >= 0 and <= 100D:
```

<span data-ttu-id="a100f-195">Questo caso non può mai corrispondere (perché l'input non può essere sia un `int` che un `double` ).</span><span class="sxs-lookup"><span data-stu-id="a100f-195">This case can never match (because the input cannot be both an `int` and a `double`).</span></span>  <span data-ttu-id="a100f-196">Si è già verificato un errore quando viene rilevato un caso che non può mai corrispondere, ma la relativa formulazione ("il caso di cambio è già stato gestito da un case precedente" e "il modello è già stato gestito da un ARM precedente dell'espressione switch") può essere fuorviante nei nuovi scenari.</span><span class="sxs-lookup"><span data-stu-id="a100f-196">We already have an error when we detect a case that can never match, but its wording ("The switch case has already been handled by a previous case" and "The pattern has already been handled by a previous arm of the switch expression") may be misleading in new scenarios.</span></span>  <span data-ttu-id="a100f-197">Potrebbe essere necessario modificare la formulazione per indicare semplicemente che il modello non corrisponderà mai all'input.</span><span class="sxs-lookup"><span data-stu-id="a100f-197">We may have to modify the wording to just say that the pattern will never match the input.</span></span>

``` csharp
case 1 and 2:
```

<span data-ttu-id="a100f-198">Analogamente, si tratta di un errore perché un valore non può essere sia `1` che `2` .</span><span class="sxs-lookup"><span data-stu-id="a100f-198">Similarly, this would be an error because a value cannot be both `1` and `2`.</span></span>

``` csharp
case 1 or 2 or 3 or 1:
```

<span data-ttu-id="a100f-199">Questo caso è possibile per trovare una corrispondenza, ma la alla `or 1` fine non aggiunge alcun significato al modello.</span><span class="sxs-lookup"><span data-stu-id="a100f-199">This case is possible to match, but the `or 1` at the end adds no meaning to the pattern.</span></span>  <span data-ttu-id="a100f-200">Si consiglia di generare un errore ogni volta che alcuni congiunzione delle o disgiunta di uno schema composto non definiscono una variabile di pattern o influiscono sul set di valori corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="a100f-200">I suggest we should aim to produce an error whenever some conjunct or disjunct of a compound pattern does not either define a pattern variable or affect the set of matched values.</span></span>

``` csharp
case < 2: break;
case 0 or 1 or 2 or 3 or 4 or 5: break;
```

<span data-ttu-id="a100f-201">Qui, `0 or 1 or` non aggiunge nulla al secondo caso, perché tali valori sarebbero stati gestiti dal primo caso.</span><span class="sxs-lookup"><span data-stu-id="a100f-201">Here, `0 or 1 or` adds nothing to the second case, as those values would have been handled by the first case.</span></span>  <span data-ttu-id="a100f-202">Questa operazione merita anche un errore.</span><span class="sxs-lookup"><span data-stu-id="a100f-202">This too deserves an error.</span></span>

``` csharp
byte b = ...;
int x = b switch { <100 => 0, 100 => 1, 101 => 2, >101 => 3 };
```

<span data-ttu-id="a100f-203">Un'espressione switch come questa deve essere considerata *esaustiva* (gestisce tutti i possibili valori di input).</span><span class="sxs-lookup"><span data-stu-id="a100f-203">A switch expression such as this should be considered *exhaustive* (it handles all possible input values).</span></span>

<span data-ttu-id="a100f-204">In C# 8,0, un'espressione switch con un input di tipo `byte` è considerata esaustiva solo se contiene un ARM finale il cui modello corrisponde a tutti gli elementi (uno *scarto* o un *modello var*).</span><span class="sxs-lookup"><span data-stu-id="a100f-204">In C# 8.0, a switch expression with an input of type `byte` is only considered exhaustive if it contains a final arm whose pattern matches everything (a *discard-pattern* or *var-pattern*).</span></span>  <span data-ttu-id="a100f-205">Anche un'espressione switch con ARM per ogni valore distinto `byte` non è considerata esaustiva in C# 8.</span><span class="sxs-lookup"><span data-stu-id="a100f-205">Even a switch expression that has an arm for every distinct `byte` value is not considered exhaustive in C# 8.</span></span>  <span data-ttu-id="a100f-206">Per gestire correttamente la completezza dei modelli relazionali, sarà necessario gestire anche questo caso.</span><span class="sxs-lookup"><span data-stu-id="a100f-206">In order to properly handle exhaustiveness of relational patterns, we will have to handle this case too.</span></span>  <span data-ttu-id="a100f-207">Tecnicamente si tratta di una modifica sostanziale, ma non è probabile che nessun utente si accorga.</span><span class="sxs-lookup"><span data-stu-id="a100f-207">This will technically be a breaking change, but no user is likely to notice.</span></span>
