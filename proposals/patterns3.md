---
ms.openlocfilehash: 57cdb682efd4cb169308e347d63e27c97b9f1b7a
ms.sourcegitcommit: 6901635c383801e4d177085587aaccadaa7b2f11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641976"
---
# <a name="pattern-matching-changes-for-c-90"></a><span data-ttu-id="974a8-101">Modifiche di criteri di ricerca per c'è 9.0Pattern-matching changes for C's 9.0</span><span class="sxs-lookup"><span data-stu-id="974a8-101">Pattern-matching changes for C# 9.0</span></span>

<span data-ttu-id="974a8-102">Stiamo prendendo in considerazione una piccola manciata di miglioramenti alla corrispondenza dei modelli per la versione 9.0 di C, che presentano una sinergia naturale e funzionano bene per risolvere una serie di problemi di programmazione comuni:We are considering a small handful of enhancements to pattern-matching for C '9.0 that have natural synergy and work well to address a number of common programming problems:</span><span class="sxs-lookup"><span data-stu-id="974a8-102">We are considering a small handful of enhancements to pattern-matching for C# 9.0 that have natural synergy and work well to address a number of common programming problems:</span></span>
- <span data-ttu-id="974a8-103">https://github.com/dotnet/csharplang/issues/2925Modelli di tipo</span><span class="sxs-lookup"><span data-stu-id="974a8-103">https://github.com/dotnet/csharplang/issues/2925 Type patterns</span></span>
- <span data-ttu-id="974a8-104">https://github.com/dotnet/csharplang/issues/1350Modelli tra parentesi per applicare o enfatizzare la precedenza dei nuovi combinatori</span><span class="sxs-lookup"><span data-stu-id="974a8-104">https://github.com/dotnet/csharplang/issues/1350 Parenthesized patterns to enforce or emphasize precedence of the new combinators</span></span>
- <span data-ttu-id="974a8-105">https://github.com/dotnet/csharplang/issues/1350Modelli congiuntivi `and` che richiedono entrambi i modelli diversi per corrispondere;</span><span class="sxs-lookup"><span data-stu-id="974a8-105">https://github.com/dotnet/csharplang/issues/1350 Conjunctive `and` patterns that require both of two different patterns to match;</span></span>
- <span data-ttu-id="974a8-106">https://github.com/dotnet/csharplang/issues/1350Modelli disgiuntivi `or` che richiedono due modelli diversi da abbinare;</span><span class="sxs-lookup"><span data-stu-id="974a8-106">https://github.com/dotnet/csharplang/issues/1350 Disjunctive `or` patterns that require either of two different patterns to match;</span></span>
- <span data-ttu-id="974a8-107">https://github.com/dotnet/csharplang/issues/1350Modelli `not` negati che richiedono un determinato modello *che non* corrispondono; E</span><span class="sxs-lookup"><span data-stu-id="974a8-107">https://github.com/dotnet/csharplang/issues/1350 Negated `not` patterns that require a given pattern *not* to match; and</span></span>
- <span data-ttu-id="974a8-108">https://github.com/dotnet/csharplang/issues/812Modelli relazionali che richiedono che il valore di input sia minore, minore o uguale a e così via a una determinata costante.</span><span class="sxs-lookup"><span data-stu-id="974a8-108">https://github.com/dotnet/csharplang/issues/812 Relational patterns that require the input value to be less than, less than or equal to, etc a given constant.</span></span>

## <a name="parenthesized-patterns"></a><span data-ttu-id="974a8-109">Modelli tra parentesi</span><span class="sxs-lookup"><span data-stu-id="974a8-109">Parenthesized Patterns</span></span>

<span data-ttu-id="974a8-110">I motivi tra parentesi consentono al programmatore di inserire le parentesi intorno a qualsiasi modello.</span><span class="sxs-lookup"><span data-stu-id="974a8-110">Parenthesized patterns permit the programmer to put parentheses around any pattern.</span></span>  <span data-ttu-id="974a8-111">Ciò non è così utile con i modelli esistenti in C . 8.0, tuttavia i nuovi combinatori di modelli introducono una precedenza che il programmatore potrebbe voler eseguire l'override.</span><span class="sxs-lookup"><span data-stu-id="974a8-111">This is not so useful with the existing patterns in C# 8.0, however the new pattern combinators introduce a precedence that the programmer may want to override.</span></span>

```antlr
primary_pattern
    : parenthesized_pattern
    | // all of the existing forms
    ;
parenthesized_pattern
    : '(' pattern ')'
    ;
```

## <a name="type-patterns"></a><span data-ttu-id="974a8-112">Modelli di tipo</span><span class="sxs-lookup"><span data-stu-id="974a8-112">Type Patterns</span></span>

<span data-ttu-id="974a8-113">Permettiamo un tipo come modello:</span><span class="sxs-lookup"><span data-stu-id="974a8-113">We permit a type as a pattern:</span></span>

``` antlr
primary_pattern
    : type-pattern
    | // all of the existing forms
    ;
type_pattern
    : type
    ;
```

<span data-ttu-id="974a8-114">In questo modo l'espressione di *tipo* is esistente è *un'is-pattern-expression* in cui il pattern è un *pattern di tipo*, anche se non si modificherebbe l'albero della sintassi prodotto dal compilatore.</span><span class="sxs-lookup"><span data-stu-id="974a8-114">This retcons the existing *is-type-expression* to be an *is-pattern-expression* in which the pattern is a *type-pattern*, though we would not change the syntax tree produced by the compiler.</span></span>

<span data-ttu-id="974a8-115">Un sottile problema di implementazione è che questa grammatica è ambigua.</span><span class="sxs-lookup"><span data-stu-id="974a8-115">One subtle implementation issue is that this grammar is ambiguous.</span></span>  <span data-ttu-id="974a8-116">Una stringa `a.b` come può essere analizzata come un nome completo (in un contesto di tipo) o un'espressione punteggiata (in un contesto di espressione).</span><span class="sxs-lookup"><span data-stu-id="974a8-116">A string such as `a.b` can be parsed either as a qualified name (in a type context) or a dotted expression (in an expression context).</span></span>  <span data-ttu-id="974a8-117">Il compilatore è già in grado di trattare un nome completo `e is Color.Red`come un'espressione punteggiata per gestire un nome simile a .</span><span class="sxs-lookup"><span data-stu-id="974a8-117">The compiler is already capable of treating a qualified name the same as a dotted expression in order to handle something like `e is Color.Red`.</span></span>  <span data-ttu-id="974a8-118">L'analisi semantica del compilatore verrebbe ulteriormente estesa per essere in grado di associare un modello costante (sintattico) (ad esempio un'espressione punteggiata) come un tipo per considerarlo come un modello di tipo associato per supportare questo costrutto.</span><span class="sxs-lookup"><span data-stu-id="974a8-118">The compiler's semantic analysis would be further extended to be capable of binding a (syntactic) constant pattern (e.g. a dotted expression) as a type in order to treat it as a bound type pattern in order to support this construct.</span></span>

<span data-ttu-id="974a8-119">Dopo questa modifica, si sarebbe in grado di scrivere</span><span class="sxs-lookup"><span data-stu-id="974a8-119">After this change, you would be able to write</span></span>
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

## <a name="relational-patterns"></a><span data-ttu-id="974a8-120">Modelli relazionali</span><span class="sxs-lookup"><span data-stu-id="974a8-120">Relational Patterns</span></span>

<span data-ttu-id="974a8-121">I modelli relazionali consentono al programmatore di esprimere che un valore di input deve soddisfare un vincolo relazionale rispetto a un valore costante:Relational patterns permit the programmer to express that an input value must satisfy a relational constraint when compared to a constant value:</span><span class="sxs-lookup"><span data-stu-id="974a8-121">Relational patterns permit the programmer to express that an input value must satisfy a relational constraint when compared to a constant value:</span></span>

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

<span data-ttu-id="974a8-122">I modelli relazionali `<` `<=`supportano gli operatori relazionali , , `>`e `>=` su tutti i tipi incorporati che supportano tali operatori relazionali binari con due operandi dello stesso tipo in un'espressione.</span><span class="sxs-lookup"><span data-stu-id="974a8-122">Relational patterns support the relational operators `<`, `<=`, `>`, and `>=` on all of the built-in types that support such binary relational operators with two operands of the same type in an expression.</span></span> <span data-ttu-id="974a8-123">In particolare, supportiamo tutti `sbyte`questi `byte` `short`modelli `ushort`relazionali per `char` `float`, `double` `decimal`, `nint`, `nuint`, `int` `uint` `long`, `ulong`, , , , , , , e .</span><span class="sxs-lookup"><span data-stu-id="974a8-123">Specifically, we support all of these relational patterns for `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `nint`, and `nuint`.</span></span>

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

<span data-ttu-id="974a8-124">L'espressione è necessaria per restituire un valore costante.</span><span class="sxs-lookup"><span data-stu-id="974a8-124">The expression is required to evaluate to a constant value.</span></span>  <span data-ttu-id="974a8-125">È un errore se tale `double.NaN` `float.NaN`valore costante è o .</span><span class="sxs-lookup"><span data-stu-id="974a8-125">It is an error if that constant value is `double.NaN` or `float.NaN`.</span></span>  <span data-ttu-id="974a8-126">Si tratta di un errore se l'espressione è una costante null.</span><span class="sxs-lookup"><span data-stu-id="974a8-126">It is an error if the expression is a null constant.</span></span>

<span data-ttu-id="974a8-127">Quando l'input è un tipo per il quale viene definito un operatore relazionale binario incorporato adatto che è applicabile con l'input come operando sinistro e la costante specificata come operando destro, la valutazione di tale operatore viene considerata come significato del modello relazionale.</span><span class="sxs-lookup"><span data-stu-id="974a8-127">When the input is a type for which a suitable built-in binary relational operator is defined that is applicable with the input as its left operand and the given constant as its right operand, the evaluation of that operator is taken as the meaning of the relational pattern.</span></span>  <span data-ttu-id="974a8-128">In caso contrario, convertiamo l'input nel tipo dell'espressione usando una conversione unboxable o unboxing esplicita.</span><span class="sxs-lookup"><span data-stu-id="974a8-128">Otherwise we convert the input to the type of the expression using an explicit nullable or unboxing conversion.</span></span>  <span data-ttu-id="974a8-129">Si tratta di un errore in fase di compilazione se tale conversione non esiste.</span><span class="sxs-lookup"><span data-stu-id="974a8-129">It is a compile-time error if no such conversion exists.</span></span>  <span data-ttu-id="974a8-130">Il modello è considerato non corrispondente se la conversione non riesce.</span><span class="sxs-lookup"><span data-stu-id="974a8-130">The pattern is considered not to match if the conversion fails.</span></span>  <span data-ttu-id="974a8-131">Se la conversione ha esito positivo, il risultato dell'operazione di corrispondenza dei modelli è il risultato della valutazione dell'espressione `e OP v` in cui `e` è l'input convertito, `OP` è l'operatore relazionale ed `v` è l'espressione costante.</span><span class="sxs-lookup"><span data-stu-id="974a8-131">If the conversion succeeds then the result of the pattern-matching operation is the result of evaluating the expression `e OP v` where `e` is the converted input, `OP` is the relational operator, and `v` is the constant expression.</span></span>

## <a name="pattern-combinators"></a><span data-ttu-id="974a8-132">Combinatori modello</span><span class="sxs-lookup"><span data-stu-id="974a8-132">Pattern Combinators</span></span>

<span data-ttu-id="974a8-133">I *combinatori* di modelli consentono `and` di abbinare entrambi i diversi modelli utilizzando `and`(questo può essere `or` esteso a qualsiasi numero di modelli mediante l'uso ripetuto di ), uno dei due modelli diversi utilizzando (ditto) o la *negazione* di un modello utilizzando `not`.</span><span class="sxs-lookup"><span data-stu-id="974a8-133">Pattern *combinators* permit matching both of two different patterns using `and` (this can be extended to any number of patterns by the repeated use of `and`), either of two different patterns using `or` (ditto), or the *negation* of a pattern using `not`.</span></span>

<span data-ttu-id="974a8-134">Un uso comune di un combinatore sarà l'idioma</span><span class="sxs-lookup"><span data-stu-id="974a8-134">A common use of a combinator will be the idiom</span></span>

``` c#
if (e is not null) ...
```

<span data-ttu-id="974a8-135">Più leggibile dell'idioma `e is object`corrente, questo modello esprime chiaramente che si sta verificando un valore non null.</span><span class="sxs-lookup"><span data-stu-id="974a8-135">More readable than the current idiom `e is object`, this pattern clearly expresses that one is checking for a non-null value.</span></span>

<span data-ttu-id="974a8-136">I `and` `or` combinatori e saranno utili per testare intervalli di valori</span><span class="sxs-lookup"><span data-stu-id="974a8-136">The `and` and `or` combinators will be useful for testing ranges of values</span></span>

``` c#
bool IsLetter(char c) => c is >= 'a' and <= 'z' or >= 'A' and <= 'Z';
```

<span data-ttu-id="974a8-137">In questo esempio `and` viene illustrato che avrà una priorità di analisi più `or`alta (ad esempio verrà associata più strettamente) rispetto a .</span><span class="sxs-lookup"><span data-stu-id="974a8-137">This example illustrates that `and` will have a higher parsing priority (i.e. will bind more closely) than `or`.</span></span>  <span data-ttu-id="974a8-138">Il programmatore può utilizzare il *modello tra parentesi* per rendere esplicita la precedenza:</span><span class="sxs-lookup"><span data-stu-id="974a8-138">The programmer can use the *parenthesized pattern* to make the precedence explicit:</span></span>

``` c#
bool IsLetter(char c) => c is (>= 'a' and <= 'z') or (>= 'A' and <= 'Z');
```

<span data-ttu-id="974a8-139">Come tutti i modelli, questi combinatori possono essere utilizzati in qualsiasi contesto in cui è previsto un modello, inclusi i modelli annidati, *l'espressione is-pattern*, l'espressione *switch*e il modello dell'etichetta case di un'istruzione switch.</span><span class="sxs-lookup"><span data-stu-id="974a8-139">Like all patterns, these combinators can be used in any context in which a pattern is expected, including nested patterns, the *is-pattern-expression*, the *switch-expression*, and the pattern of a switch statement's case label.</span></span>

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

## <a name="open-issues-with-proposed-changes"></a><span data-ttu-id="974a8-140">Problemi aperti con le modifiche proposte</span><span class="sxs-lookup"><span data-stu-id="974a8-140">Open Issues with Proposed Changes</span></span>

### <a name="syntax-for-relational-operators"></a><span data-ttu-id="974a8-141">Sintassi per gli operatori relazionali</span><span class="sxs-lookup"><span data-stu-id="974a8-141">Syntax for relational operators</span></span>

<span data-ttu-id="974a8-142">Sono `and` `or`, `not` e qualche tipo di parola chiave contestuale?</span><span class="sxs-lookup"><span data-stu-id="974a8-142">Are `and`, `or`, and `not` some kind of contextual keyword?</span></span>  <span data-ttu-id="974a8-143">In caso affermativo, vi è una modifica sostanziale (ad esempio rispetto al loro utilizzo come designatore in un *modello di dichiarazione*).</span><span class="sxs-lookup"><span data-stu-id="974a8-143">If so, is there a breaking change (e.g. compared to their use as a designator in a *declaration-pattern*).</span></span>

### <a name="semantics-eg-type-for-relational-operators"></a><span data-ttu-id="974a8-144">Semantica (ad esempio tipo) per gli operatori relazionali</span><span class="sxs-lookup"><span data-stu-id="974a8-144">Semantics (e.g. type) for relational operators</span></span>

<span data-ttu-id="974a8-145">Prevediamo di supportare tutti i tipi primitivi che possono essere confrontati in un'espressione utilizzando un operatore relazionale.</span><span class="sxs-lookup"><span data-stu-id="974a8-145">We expect to support all of the primitive types that can be compared in an expression using a relational operator.</span></span>  <span data-ttu-id="974a8-146">Il significato in casi semplici è chiaro</span><span class="sxs-lookup"><span data-stu-id="974a8-146">The meaning in simple cases is clear</span></span>

``` c#
bool IsValidPercentage(int x) => x is >= 0 and <= 100;
```

<span data-ttu-id="974a8-147">Ma quando l'input non è un tipo così primitivo, che tipo cerchiamo di convertirlo?</span><span class="sxs-lookup"><span data-stu-id="974a8-147">But when the input is not such a primitive type, what type do we attempt to convert it to?</span></span>

``` c#
bool IsValidPercentage(object x) => x is >= 0 and <= 100;
```

<span data-ttu-id="974a8-148">Abbiamo proposto che quando il tipo di input è già una primitiva comparabile, questo è il tipo di confronto.</span><span class="sxs-lookup"><span data-stu-id="974a8-148">We have proposed that when the input type is already a comparable primitive, that is the type of the comparison.</span></span> <span data-ttu-id="974a8-149">Tuttavia, quando l'input non è una primitiva comparabile, trattiamo l'relazionale come incluso un test di tipo implicito per il tipo della costante sul lato destro dell'relazionale.</span><span class="sxs-lookup"><span data-stu-id="974a8-149">However, when the input is not a comparable primitive, we treat the relational as including an implicit type test to the type of the constant on the right-hand-side of the relational.</span></span>  <span data-ttu-id="974a8-150">Se il programmatore intende supportare più di un tipo di input, è necessario eseguire questa operazione in modo esplicito:If the programmer intends to support more than one input type, that must be done explicitly:</span><span class="sxs-lookup"><span data-stu-id="974a8-150">If the programmer intends to support more than one input type, that must be done explicitly:</span></span>

``` c#
bool IsValidPercentage(object x) => x is
    >= 0 and <= 100 or    // integer tests
    >= 0F and <= 100F or  // float tests
    >= 0D and <= 100D;    // double tests
```

### <a name="flowing-type-information-from-the-left-to-the-right-of-and"></a><span data-ttu-id="974a8-151">Informazioni sul tipo scorrevoli da sinistra a destra di`and`</span><span class="sxs-lookup"><span data-stu-id="974a8-151">Flowing type information from the left to the right of `and`</span></span>

<span data-ttu-id="974a8-152">È stato suggerito che quando `and` si scrive un combinatore, le informazioni sul tipo apprese a sinistra sul tipo di primo livello potrebbero fluire verso destra.</span><span class="sxs-lookup"><span data-stu-id="974a8-152">It has been suggested that when you write an `and` combinator, type information learned on the left about the top-level type could flow to the right.</span></span>  <span data-ttu-id="974a8-153">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="974a8-153">For example</span></span>

```csharp
bool isSmallByte(object o) => o is byte and < 100;
```

<span data-ttu-id="974a8-154">In questo caso, il tipo di *input* al secondo modello viene `and`ristretto dai requisiti di *restringimento* del tipo di sinistra dell'oggetto .</span><span class="sxs-lookup"><span data-stu-id="974a8-154">Here, the *input type* to the second pattern is narrowed by the *type narrowing* requirements of left of the `and`.</span></span>  <span data-ttu-id="974a8-155">È possibile definire la semantica di restringimento dei tipi per tutti i modelli come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="974a8-155">We would define type narrowing semantics for all patterns as follows.</span></span>  <span data-ttu-id="974a8-156">Il *tipo ristretto* `P` di un modello è definito come segue:</span><span class="sxs-lookup"><span data-stu-id="974a8-156">The *narrowed type* of a pattern `P` is defined as follows:</span></span>
1. <span data-ttu-id="974a8-157">Se `P` è un modello di tipo, il *tipo ristretto* è il tipo del tipo del modello di tipo.</span><span class="sxs-lookup"><span data-stu-id="974a8-157">If `P` is a type pattern, the *narrowed type* is the type of the type pattern's type.</span></span>
2. <span data-ttu-id="974a8-158">Se `P` è un modello di dichiarazione, il *tipo ristretto* è il tipo del tipo del modello di dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="974a8-158">If `P` is a declaration pattern, the *narrowed type* is the type of the declaration pattern's type.</span></span>
3. <span data-ttu-id="974a8-159">Se `P` è un modello ricorsivo che fornisce un tipo esplicito, il *tipo ristretto* è quel tipo.</span><span class="sxs-lookup"><span data-stu-id="974a8-159">If `P` is a recursive pattern that gives an explicit type, the *narrowed type* is that type.</span></span>
4. <span data-ttu-id="974a8-160">Se `P` è un modello costante in cui la costante non è la costante null e in cui l'espressione non dispone di alcuna *conversione* di espressione costante nel *tipo di input*, il tipo *ristretto* è il tipo della costante.</span><span class="sxs-lookup"><span data-stu-id="974a8-160">If `P` is a constant pattern where the constant is not the null constant and where the expression has no *constant expression conversion* to the *input type*, the *narrowed type* is the type of the constant.</span></span>
5. <span data-ttu-id="974a8-161">Se `P` è un modello relazionale in cui l'espressione costante non dispone di *alcuna conversione dell'espressione costante* nel tipo di *input,* il *tipo ristretto* è il tipo della costante.</span><span class="sxs-lookup"><span data-stu-id="974a8-161">If `P` is a relational pattern where the constant expression has no *constant expression conversion* to the *input type*, the *narrowed type* is the type of the constant.</span></span>
6. <span data-ttu-id="974a8-162">Se `P` è `or` un modello, il *tipo ristretto* è il tipo comune del *tipo ristretto* dei sottomodelli se esiste un tipo comune di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="974a8-162">If `P` is an `or` pattern, the *narrowed type* is the common type of the *narrowed type* of the subpatterns if such a common type exists.</span></span> <span data-ttu-id="974a8-163">A tale scopo, l'algoritmo di tipo comune considera solo le conversioni di identità, `or` boxing e riferimenti implicite e considera tutti i sottomodelli di una sequenza di modelli (ignorando i modelli tra parentesi).</span><span class="sxs-lookup"><span data-stu-id="974a8-163">For this purpose, the common type algorithm considers only identity, boxing, and implicit reference conversions, and it considers all subpatterns of a sequence of `or` patterns (ignoring parenthesized patterns).</span></span>
7. <span data-ttu-id="974a8-164">Se `P` è `and` un modello, il *tipo ristretto* è il *tipo ristretto* del modello destro.</span><span class="sxs-lookup"><span data-stu-id="974a8-164">If `P` is an `and` pattern, the *narrowed type* is the *narrowed type* of the right pattern.</span></span> <span data-ttu-id="974a8-165">Inoltre, il *tipo ristretto* del pattern sinistro è il tipo di *input* del modello destro.</span><span class="sxs-lookup"><span data-stu-id="974a8-165">Moreover, the *narrowed type* of the left pattern is the *input type* of the right pattern.</span></span>
8. <span data-ttu-id="974a8-166">In caso contrario, `P`il *tipo ristretto* di è 's tipo di `P` input.</span><span class="sxs-lookup"><span data-stu-id="974a8-166">Otherwise the *narrowed type* of `P` is `P`'s input type.</span></span>

### <a name="variable-definitions-and-definite-assignment"></a><span data-ttu-id="974a8-167">Definizioni delle variabili e assegnazione definita</span><span class="sxs-lookup"><span data-stu-id="974a8-167">Variable definitions and definite assignment</span></span>

<span data-ttu-id="974a8-168">L'aggiunta `or` `not` di e modelli crea alcuni interessanti nuovi problemi relativi alle variabili di modello e all'assegnazione definita.</span><span class="sxs-lookup"><span data-stu-id="974a8-168">The addition of `or` and `not` patterns creates some interesting new problems around pattern variables and definite assignment.</span></span>  <span data-ttu-id="974a8-169">Poiché le variabili possono in genere essere dichiarate al massimo `or` una volta, sembrerebbe che qualsiasi variabile di modello dichiarata su un lato di un modello non venga assegnata in modo definito quando il modello corrisponde.</span><span class="sxs-lookup"><span data-stu-id="974a8-169">Since variables can normally be declared at most once, it would seem any pattern variable declared on one side of an `or` pattern would not be definitely assigned when the pattern matches.</span></span>  <span data-ttu-id="974a8-170">Analogamente, una variabile `not` dichiarata all'interno di un modello non dovrebbe essere assegnata in modo definito quando il modello corrisponde.</span><span class="sxs-lookup"><span data-stu-id="974a8-170">Similarly, a variable declared inside a `not` pattern would not be expected to be definitely assigned when the pattern matches.</span></span>  <span data-ttu-id="974a8-171">Il modo più semplice per risolvere questo problema consiste nel proibire la dichiarazione di variabili di pattern in questi contesti.</span><span class="sxs-lookup"><span data-stu-id="974a8-171">The simplest way to address this is to forbid declaring pattern variables in these contexts.</span></span>  <span data-ttu-id="974a8-172">Tuttavia, questo può essere troppo restrittivo.</span><span class="sxs-lookup"><span data-stu-id="974a8-172">However, this may be too restrictive.</span></span>  <span data-ttu-id="974a8-173">Ci sono altri approcci da considerare.</span><span class="sxs-lookup"><span data-stu-id="974a8-173">There are other approaches to consider.</span></span>

<span data-ttu-id="974a8-174">Uno scenario che vale la pena considerare è questo</span><span class="sxs-lookup"><span data-stu-id="974a8-174">One scenario that is worth considering is this</span></span>

``` csharp
if (e is not int i) return;
M(i); // is i definitely assigned here?
```

<span data-ttu-id="974a8-175">Questo non funziona oggi perché, per *un'espressione è-pattern*, le variabili di modello vengono considerate *assegnate in modo definito* solo dove l'is-pattern-expression è true ("definitely assigned when true"). *is-pattern-expression*</span><span class="sxs-lookup"><span data-stu-id="974a8-175">This does not work today because, for an *is-pattern-expression*, the pattern variables are considered *definitely assigned* only where the *is-pattern-expression* is true ("definitely assigned when true").</span></span>

<span data-ttu-id="974a8-176">A supporto di questo sarebbe più semplice (dal punto di vista `if` del programmatore) che aggiungere anche il supporto per un'istruzione di condizione negata.</span><span class="sxs-lookup"><span data-stu-id="974a8-176">Supporting this would be simpler (from the programmer's perspective) than also adding support for a negated-condition `if` statement.</span></span>  <span data-ttu-id="974a8-177">Anche se aggiungiamo tale supporto, i programmatori si chiederebbero perché lo snippet di cui sopra non funziona.</span><span class="sxs-lookup"><span data-stu-id="974a8-177">Even if we add such support, programmers would wonder why the above snippet does not work.</span></span>  <span data-ttu-id="974a8-178">D'altra parte, lo stesso `switch` scenario in un ha meno senso, in quanto non esiste un punto corrispondente nel programma in cui *assegnata in modo definito quando false* sarebbe significativo.</span><span class="sxs-lookup"><span data-stu-id="974a8-178">On the other hand, the same scenario in a `switch` makes less sense, as there is no corresponding point in the program where *definitely assigned when false* would be meaningful.</span></span>  <span data-ttu-id="974a8-179">Lo consentiremmo in *un'espressione is-pattern,* ma non in altri contesti in cui i modelli sono consentiti?</span><span class="sxs-lookup"><span data-stu-id="974a8-179">Would we permit this in an *is-pattern-expression* but not in other contexts where patterns are permitted?</span></span>  <span data-ttu-id="974a8-180">Sembra irregolare.</span><span class="sxs-lookup"><span data-stu-id="974a8-180">That seems irregular.</span></span>

<span data-ttu-id="974a8-181">Correlato a questo è il problema dell'assegnazione definita in un *modello disgiuntivo*.</span><span class="sxs-lookup"><span data-stu-id="974a8-181">Related to this is the problem of definite assignment in a *disjunctive-pattern*.</span></span>

```csharp
if (e is 0 or int i)
{
    M(i); // is i definitely assigned here?
}
```

<span data-ttu-id="974a8-182">Ci si `i` aspetterebbe di essere assegnato in modo definito solo quando l'ingresso non è zero.</span><span class="sxs-lookup"><span data-stu-id="974a8-182">We would only expect `i` to be definitely assigned when the input is not zero.</span></span>  <span data-ttu-id="974a8-183">Ma dal momento che non sappiamo se l'ingresso `i` è zero o meno all'interno del blocco, non è assegnato in modo definito.</span><span class="sxs-lookup"><span data-stu-id="974a8-183">But since we don't know whether the input is zero or not inside the block, `i` is not definitely assigned.</span></span>  <span data-ttu-id="974a8-184">Tuttavia, cosa `i` succede se permettiamo di essere dichiarati in diversi modelli che si escludono a vicenda?</span><span class="sxs-lookup"><span data-stu-id="974a8-184">However, what if we permit `i` to be declared in different mutually exclusive patterns?</span></span>

```csharp
if ((e1, e2) is (0, int i) or (int i, 0))
{
    M(i);
}
```

<span data-ttu-id="974a8-185">In questo `i` caso, la variabile viene assegnata in modo definito all'interno del blocco e accetta il valore dall'altro elemento della tupla quando viene trovato un elemento zero.</span><span class="sxs-lookup"><span data-stu-id="974a8-185">Here, the variable `i` is definitely assigned inside the block, and takes it value from the other element of the tuple when a zero element is found.</span></span>

<span data-ttu-id="974a8-186">È stato anche suggerito di consentire alle variabili di essere definite (moltiplicazione) in ogni caso di un blocco di maiuscole e minuscole:</span><span class="sxs-lookup"><span data-stu-id="974a8-186">It has also been suggested to permit variables to be (multiply) defined in every case of a case block:</span></span>

```csharp
    case (0, int x):
    case (int x, 0):
        Console.WriteLine(x);
```

<span data-ttu-id="974a8-187">Per fare qualsiasi di questo lavoro, dovremmo definire attentamente dove tali definizioni multiple sono consentite e a quali condizioni tale variabile è considerata assegnata in modo definito.</span><span class="sxs-lookup"><span data-stu-id="974a8-187">To make any of this work, we would have to carefully define where such multiple definitions are permitted and under what conditions such a variable is considered definitely assigned.</span></span>

<span data-ttu-id="974a8-188">Dovremmo scegliere di rinviare tale lavoro fino a dopo (che vi consiglio), potremmo dire in C</span><span class="sxs-lookup"><span data-stu-id="974a8-188">Should we elect to defer such work until later (which I advise), we could say in C# 9</span></span>
- <span data-ttu-id="974a8-189">sotto `not` a `or`o , le variabili di pattern non possono essere dichiarate.</span><span class="sxs-lookup"><span data-stu-id="974a8-189">beneath a `not` or `or`, pattern variables may not be declared.</span></span>

<span data-ttu-id="974a8-190">Poi, avremmo il tempo di sviluppare un'esperienza che fornisca informazioni sul possibile valore di rilassarsi in seguito.</span><span class="sxs-lookup"><span data-stu-id="974a8-190">Then, we would have time to develop some experience that would provide insight into the possible value of relaxing that later.</span></span>

### <a name="diagnostics-subsumption-and-exhaustiveness"></a><span data-ttu-id="974a8-191">Diagnostica, suntitudine ed esaustività</span><span class="sxs-lookup"><span data-stu-id="974a8-191">Diagnostics, subsumption, and exhaustiveness</span></span>

<span data-ttu-id="974a8-192">Queste nuove forme di modello introducono molte nuove opportunità per l'errore del programmatore diagnosable.</span><span class="sxs-lookup"><span data-stu-id="974a8-192">These new pattern forms introduce many new opportunities for diagnosable programmer error.</span></span>  <span data-ttu-id="974a8-193">Dovremo decidere quali tipi di errori diagnosticare e come farlo.</span><span class="sxs-lookup"><span data-stu-id="974a8-193">We will need to decide what kinds of errors we will diagnose, and how to do so.</span></span>  <span data-ttu-id="974a8-194">Di seguito sono riportati alcuni esempi:</span><span class="sxs-lookup"><span data-stu-id="974a8-194">Here are some examples:</span></span>

``` csharp
case >= 0 and <= 100D:
```

<span data-ttu-id="974a8-195">Questo caso non può mai corrispondere (perché l'input non può essere sia un e `int` un `double`).</span><span class="sxs-lookup"><span data-stu-id="974a8-195">This case can never match (because the input cannot be both an `int` and a `double`).</span></span>  <span data-ttu-id="974a8-196">Abbiamo già un errore quando rileviamo un caso che non può mai corrispondere, ma la sua formulazione ("Il caso switch è già stato gestito da un caso precedente" e "Il modello è già stato gestito da un braccio precedente dell'espressione switch") può essere fuorviante in nuovi scenari.</span><span class="sxs-lookup"><span data-stu-id="974a8-196">We already have an error when we detect a case that can never match, but its wording ("The switch case has already been handled by a previous case" and "The pattern has already been handled by a previous arm of the switch expression") may be misleading in new scenarios.</span></span>  <span data-ttu-id="974a8-197">Potremmo dover modificare la formulazione per dire solo che il modello non corrisponderà mai all'input.</span><span class="sxs-lookup"><span data-stu-id="974a8-197">We may have to modify the wording to just say that the pattern will never match the input.</span></span>

``` csharp
case 1 and 2:
```

<span data-ttu-id="974a8-198">Analogamente, si tratterebbe di un `1` errore `2`perché un valore non può essere entrambi e .</span><span class="sxs-lookup"><span data-stu-id="974a8-198">Similarly, this would be an error because a value cannot be both `1` and `2`.</span></span>

``` csharp
case 1 or 2 or 3 or 1:
```

<span data-ttu-id="974a8-199">Questo caso è possibile trovare `or 1` una corrispondenza, ma alla fine non aggiunge alcun significato al modello.</span><span class="sxs-lookup"><span data-stu-id="974a8-199">This case is possible to match, but the `or 1` at the end adds no meaning to the pattern.</span></span>  <span data-ttu-id="974a8-200">Suggerisco di mirare a produrre un errore ogni volta che qualche congiunzione o disgiunzione di un modello composto non definisce una variabile di modello né influisce sul set di valori corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="974a8-200">I suggest we should aim to produce an error whenever some conjunct or disjunct of a compound pattern does not either define a pattern variable or affect the set of matched values.</span></span>

``` csharp
case < 2: break;
case 0 or 1 or 2 or 3 or 4 or 5: break;
```

<span data-ttu-id="974a8-201">In `0 or 1 or` questo caso, non aggiunge nulla al secondo caso, in quanto tali valori sarebbero stati gestiti dal primo caso.</span><span class="sxs-lookup"><span data-stu-id="974a8-201">Here, `0 or 1 or` adds nothing to the second case, as those values would have been handled by the first case.</span></span>  <span data-ttu-id="974a8-202">Anche questo merita un errore.</span><span class="sxs-lookup"><span data-stu-id="974a8-202">This too deserves an error.</span></span>

``` csharp
byte b = ...;
int x = b switch { <100 => 0, 100 => 1, 101 => 2, >101 => 3 };
```

<span data-ttu-id="974a8-203">Un'espressione switch come questa deve essere considerata *esaustiva* (gestisce tutti i valori di input possibili).</span><span class="sxs-lookup"><span data-stu-id="974a8-203">A switch expression such as this should be considered *exhaustive* (it handles all possible input values).</span></span>

<span data-ttu-id="974a8-204">Nella versione 8.0 di C, un'espressione switch con un input di tipo `byte` viene considerata completa solo se contiene un braccio finale il cui modello corrisponde a tutto (un *discard-pattern* o *var-pattern*).</span><span class="sxs-lookup"><span data-stu-id="974a8-204">In C# 8.0, a switch expression with an input of type `byte` is only considered exhaustive if it contains a final arm whose pattern matches everything (a *discard-pattern* or *var-pattern*).</span></span>  <span data-ttu-id="974a8-205">Anche un'espressione switch che dispone `byte` di un braccio per ogni valore distinto non è considerata esaustiva in C .</span><span class="sxs-lookup"><span data-stu-id="974a8-205">Even a switch expression that has an arm for every distinct `byte` value is not considered exhaustive in C# 8.</span></span>  <span data-ttu-id="974a8-206">Al fine di gestire correttamente l'esaurimento dei modelli relazionali, dovremo gestire anche questo caso.</span><span class="sxs-lookup"><span data-stu-id="974a8-206">In order to properly handle exhaustiveness of relational patterns, we will have to handle this case too.</span></span>  <span data-ttu-id="974a8-207">Tecnicamente si tratterà di un cambiamento di rilievo, ma nessun utente è probabile che se ne accorga.</span><span class="sxs-lookup"><span data-stu-id="974a8-207">This will technically be a breaking change, but no user is likely to notice.</span></span>
