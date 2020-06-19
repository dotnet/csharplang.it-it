---
ms.openlocfilehash: 4400cc56fc52fb36cdd19809ff7fb8f13706c161
ms.sourcegitcommit: eb00bb077e46c46807d804e9e1de3d794fb32405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "85070845"
---
# <a name="constant-interpolated-strings"></a><span data-ttu-id="76016-101">Stringhe interpolate costanti</span><span class="sxs-lookup"><span data-stu-id="76016-101">Constant Interpolated Strings</span></span>

* <span data-ttu-id="76016-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="76016-102">[x] Proposed</span></span>
* <span data-ttu-id="76016-103">[] Prototipo: [non avviato](https://github.com/kevinsun-dev/roslyn/BRANCH_NAME)</span><span class="sxs-lookup"><span data-stu-id="76016-103">[ ] Prototype: [Not Started](https://github.com/kevinsun-dev/roslyn/BRANCH_NAME)</span></span>
* <span data-ttu-id="76016-104">[] Implementazione: [non avviata](https://github.com/dotnet/roslyn/BRANCH_NAME)</span><span class="sxs-lookup"><span data-stu-id="76016-104">[ ] Implementation: [Not Started](https://github.com/dotnet/roslyn/BRANCH_NAME)</span></span>
* <span data-ttu-id="76016-105">[] Specifica: [non avviata](pr/1)</span><span class="sxs-lookup"><span data-stu-id="76016-105">[ ] Specification: [Not Started](pr/1)</span></span>

## <a name="summary"></a><span data-ttu-id="76016-106">Summary</span><span class="sxs-lookup"><span data-stu-id="76016-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="76016-107">Consente la generazione di costanti da stringhe interpolate di tipo costante stringa.</span><span class="sxs-lookup"><span data-stu-id="76016-107">Enables constants to be generated from interpolated strings of type string constant.</span></span>

## <a name="motivation"></a><span data-ttu-id="76016-108">Motivazione</span><span class="sxs-lookup"><span data-stu-id="76016-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="76016-109">Il codice seguente è già valido:</span><span class="sxs-lookup"><span data-stu-id="76016-109">The following code is already legal:</span></span>
```
public class C
{
    const string S1 = "Hello world";
    const string S2 = "Hello" + " " + "World";
    const string S3 = S1 + " Kevin, welcome to the team!";
}
```
<span data-ttu-id="76016-110">Tuttavia, sono state apportate numerose richieste della community per rendere valide le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="76016-110">However, there have been many community requests to make the following also legal:</span></span>
```
public class C
{
    const string S1 = $"Hello world";
    const string S2 = $"Hello{" "}World";
    const string S3 = $"{S1} Kevin, welcome to the team!";
}
```
<span data-ttu-id="76016-111">Questa proposta rappresenta il passaggio logico successivo per la generazione di stringhe costanti, in cui la sintassi di stringa esistente che funziona in altre situazioni viene eseguita per le costanti.</span><span class="sxs-lookup"><span data-stu-id="76016-111">This proposal represents the next logical step for constant string generation, where existing string syntax that works in other situations is made to work for constants.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="76016-112">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="76016-112">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="76016-113">Di seguito sono rappresentate le specifiche aggiornate per le espressioni costanti in questa nuova proposta.</span><span class="sxs-lookup"><span data-stu-id="76016-113">The following represent the updated specifications for constant expressions under this new proposal.</span></span> <span data-ttu-id="76016-114">Le specifiche correnti da cui questo è stato direttamente basato su sono disponibili [qui](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#constant-expressions).</span><span class="sxs-lookup"><span data-stu-id="76016-114">Current specifications from which this was directly based on can be found [here](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#constant-expressions).</span></span>

### <a name="constant-expressions"></a><span data-ttu-id="76016-115">Espressioni costanti</span><span class="sxs-lookup"><span data-stu-id="76016-115">Constant Expressions</span></span>

<span data-ttu-id="76016-116">Un *constant_expression* è un'espressione che può essere valutata interamente in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="76016-116">A *constant_expression* is an expression that can be fully evaluated at compile-time.</span></span>

```antlr
constant_expression
    : expression
    ;
```

<span data-ttu-id="76016-117">Un'espressione costante deve essere un `null` valore letterale o un valore con uno dei tipi seguenti: `sbyte` ,, `byte` `short` , `ushort` , `int` , `uint` , `long` , `ulong` , `char` , `float` , `double` , `decimal` , `bool` , `object` , `string` o qualsiasi tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="76016-117">A constant expression must be the `null` literal or a value with one of  the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `object`, `string`, or any enumeration type.</span></span> <span data-ttu-id="76016-118">Nelle espressioni costanti sono consentiti solo i costrutti seguenti:</span><span class="sxs-lookup"><span data-stu-id="76016-118">Only the following constructs are permitted in constant expressions:</span></span>

*  <span data-ttu-id="76016-119">Valori letterali (incluso il `null` valore letterale).</span><span class="sxs-lookup"><span data-stu-id="76016-119">Literals (including the `null` literal).</span></span>
*  <span data-ttu-id="76016-120">Riferimenti ai `const` membri dei tipi di classe e struct.</span><span class="sxs-lookup"><span data-stu-id="76016-120">References to `const` members of class and struct types.</span></span>
*  <span data-ttu-id="76016-121">Riferimenti a membri di tipi di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="76016-121">References to members of enumeration types.</span></span>
*  <span data-ttu-id="76016-122">Riferimenti a `const` parametri o variabili locali</span><span class="sxs-lookup"><span data-stu-id="76016-122">References to `const` parameters or local variables</span></span>
*  <span data-ttu-id="76016-123">Espressioni secondarie tra parentesi, ovvero espressioni costanti.</span><span class="sxs-lookup"><span data-stu-id="76016-123">Parenthesized sub-expressions, which are themselves constant expressions.</span></span>
*  <span data-ttu-id="76016-124">Le espressioni cast, purché il tipo di destinazione sia uno dei tipi elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="76016-124">Cast expressions, provided the target type is one of the types listed above.</span></span>
*  <span data-ttu-id="76016-125">`checked``unchecked`espressioni and</span><span class="sxs-lookup"><span data-stu-id="76016-125">`checked` and `unchecked` expressions</span></span>
*  <span data-ttu-id="76016-126">Espressioni con valore predefinito</span><span class="sxs-lookup"><span data-stu-id="76016-126">Default value expressions</span></span>
*  <span data-ttu-id="76016-127">Espressioni NameOf</span><span class="sxs-lookup"><span data-stu-id="76016-127">Nameof expressions</span></span>
*  <span data-ttu-id="76016-128">Gli `+` `-` operatori unari,, `!` e predefiniti `~` .</span><span class="sxs-lookup"><span data-stu-id="76016-128">The predefined `+`, `-`, `!`, and `~` unary operators.</span></span>
*  <span data-ttu-id="76016-129">Gli operatori binari predefiniti,,,,,,,, `+` `-` `*` `/` `%` `<<` `>>` `&` `|` , `^` , `&&` , `||` , `==` , `!=` , `<` , `>` , `<=` e `>=` , purché ogni operando sia di un tipo elencato sopra.</span><span class="sxs-lookup"><span data-stu-id="76016-129">The predefined `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `&`, `|`, `^`, `&&`, `||`, `==`, `!=`, `<`, `>`, `<=`, and `>=` binary operators, provided each operand is of a type listed above.</span></span>
*  <span data-ttu-id="76016-130">`?:`Operatore condizionale.</span><span class="sxs-lookup"><span data-stu-id="76016-130">The `?:` conditional operator.</span></span>
*  <span data-ttu-id="76016-131">*Le stringhe interpolate `${}` , purché tutti i componenti siano espressioni costanti di tipo `string` e tutti i componenti interpolati non hanno identificatori di formato e allineamento.*</span><span class="sxs-lookup"><span data-stu-id="76016-131">*Interpolated strings `${}`, provided that all components are constant expressions of type `string` and all interpolated components lack alignment and format specifiers.*</span></span>

<span data-ttu-id="76016-132">Nelle espressioni costanti sono consentite le conversioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="76016-132">The following conversions are permitted in constant expressions:</span></span>

*  <span data-ttu-id="76016-133">Conversioni di identità</span><span class="sxs-lookup"><span data-stu-id="76016-133">Identity conversions</span></span>
*  <span data-ttu-id="76016-134">Conversioni numeriche</span><span class="sxs-lookup"><span data-stu-id="76016-134">Numeric conversions</span></span>
*  <span data-ttu-id="76016-135">Conversioni di enumerazione</span><span class="sxs-lookup"><span data-stu-id="76016-135">Enumeration conversions</span></span>
*  <span data-ttu-id="76016-136">Conversioni di espressioni costanti</span><span class="sxs-lookup"><span data-stu-id="76016-136">Constant expression conversions</span></span>
*  <span data-ttu-id="76016-137">Conversioni di riferimenti implicite ed esplicite, a condizione che l'origine delle conversioni sia un'espressione costante che restituisce il valore null.</span><span class="sxs-lookup"><span data-stu-id="76016-137">Implicit and explicit reference conversions, provided that the source of the conversions is a constant expression that evaluates to the null value.</span></span>

<span data-ttu-id="76016-138">Nelle espressioni costanti non sono consentite altre conversioni, incluse le conversioni boxing, unboxing e riferimento implicito di valori non null.</span><span class="sxs-lookup"><span data-stu-id="76016-138">Other conversions including boxing, unboxing and implicit reference conversions of non-null values are not permitted in constant expressions.</span></span> <span data-ttu-id="76016-139">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="76016-139">For example:</span></span>
```csharp
class C 
{
    const object i = 5;         // error: boxing conversion not permitted
    const object str = "hello"; // error: implicit reference conversion
}
```
<span data-ttu-id="76016-140">l'inizializzazione di i è un errore perché è necessaria una conversione boxing.</span><span class="sxs-lookup"><span data-stu-id="76016-140">the initialization of i is an error because a boxing conversion is required.</span></span> <span data-ttu-id="76016-141">L'inizializzazione di str è un errore perché è necessaria una conversione implicita di un riferimento da un valore non null.</span><span class="sxs-lookup"><span data-stu-id="76016-141">The initialization of str is an error because an implicit reference conversion from a non-null value is required.</span></span>

<span data-ttu-id="76016-142">Ogni volta che un'espressione soddisfa i requisiti elencati sopra, l'espressione viene valutata in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="76016-142">Whenever an expression fulfills the requirements listed above, the expression is evaluated at compile-time.</span></span> <span data-ttu-id="76016-143">Questo vale anche se l'espressione è una sottoespressione di un'espressione più grande che contiene costrutti non costanti.</span><span class="sxs-lookup"><span data-stu-id="76016-143">This is true even if the expression is a sub-expression of a larger expression that contains non-constant constructs.</span></span>

<span data-ttu-id="76016-144">La valutazione in fase di compilazione delle espressioni costanti usa le stesse regole della valutazione in fase di esecuzione delle espressioni non costanti, ad eccezione del fatto che la valutazione in fase di esecuzione genera un'eccezione, mentre la valutazione in fase di compilazione genera un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="76016-144">The compile-time evaluation of constant expressions uses the same rules as run-time evaluation of non-constant expressions, except that where run-time evaluation would have thrown an exception, compile-time evaluation causes a compile-time error to occur.</span></span>

<span data-ttu-id="76016-145">A meno che un'espressione costante non venga inserita in modo esplicito in un `unchecked` contesto, i flussi che si verificano nelle operazioni aritmetiche di tipo integrale e nelle conversioni durante la valutazione in fase di compilazione dell'espressione provocano sempre errori in fase di compilazione ([espressioni costanti](expressions.md#constant-expressions)).</span><span class="sxs-lookup"><span data-stu-id="76016-145">Unless a constant expression is explicitly placed in an `unchecked` context, overflows that occur in integral-type arithmetic operations and conversions during the compile-time evaluation of the expression always cause compile-time errors ([Constant expressions](expressions.md#constant-expressions)).</span></span>

<span data-ttu-id="76016-146">Le espressioni costanti si verificano nei contesti elencati di seguito.</span><span class="sxs-lookup"><span data-stu-id="76016-146">Constant expressions occur in the contexts listed below.</span></span> <span data-ttu-id="76016-147">In questi contesti, si verifica un errore in fase di compilazione se non è possibile valutare completamente un'espressione in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="76016-147">In these contexts, a compile-time error occurs if an expression cannot be fully evaluated at compile-time.</span></span>

*  <span data-ttu-id="76016-148">Dichiarazioni di costanti ([costanti](classes.md#constants)).</span><span class="sxs-lookup"><span data-stu-id="76016-148">Constant declarations ([Constants](classes.md#constants)).</span></span>
*  <span data-ttu-id="76016-149">Dichiarazioni di membri di enumerazione ([membri enum](enums.md#enum-members)).</span><span class="sxs-lookup"><span data-stu-id="76016-149">Enumeration member declarations ([Enum members](enums.md#enum-members)).</span></span>
*  <span data-ttu-id="76016-150">Argomenti predefiniti degli elenchi di parametri formali ([parametri del metodo](classes.md#method-parameters))</span><span class="sxs-lookup"><span data-stu-id="76016-150">Default arguments of formal parameter lists ([Method parameters](classes.md#method-parameters))</span></span>
*  <span data-ttu-id="76016-151">`case`etichette di un' `switch` istruzione ([istruzione switch](statements.md#the-switch-statement)).</span><span class="sxs-lookup"><span data-stu-id="76016-151">`case` labels of a `switch` statement ([The switch statement](statements.md#the-switch-statement)).</span></span>
*  <span data-ttu-id="76016-152">`goto case`istruzioni ([istruzione goto](statements.md#the-goto-statement)).</span><span class="sxs-lookup"><span data-stu-id="76016-152">`goto case` statements ([The goto statement](statements.md#the-goto-statement)).</span></span>
*  <span data-ttu-id="76016-153">Lunghezza delle dimensioni in un'espressione di creazione di matrici ([espressioni di creazione di matrici](expressions.md#array-creation-expressions)) che include un inizializzatore.</span><span class="sxs-lookup"><span data-stu-id="76016-153">Dimension lengths in an array creation expression ([Array creation expressions](expressions.md#array-creation-expressions)) that includes an initializer.</span></span>
*  <span data-ttu-id="76016-154">Attributi ([attributi](attributes.md)).</span><span class="sxs-lookup"><span data-stu-id="76016-154">Attributes ([Attributes](attributes.md)).</span></span>

<span data-ttu-id="76016-155">Una conversione implicita di espressioni costanti ([conversioni implicite di espressioni costanti](conversions.md#implicit-constant-expression-conversions)) consente a un'espressione costante di tipo di `int` essere convertita in `sbyte` , `byte` , `short` , `ushort` , `uint` o `ulong` , purché il valore dell'espressione costante sia compreso nell'intervallo del tipo di destinazione.</span><span class="sxs-lookup"><span data-stu-id="76016-155">An implicit constant expression conversion ([Implicit constant expression conversions](conversions.md#implicit-constant-expression-conversions)) permits a constant expression of type `int` to be converted to `sbyte`, `byte`, `short`, `ushort`, `uint`, or `ulong`, provided the value of the constant expression is within the range of the destination type.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="76016-156">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="76016-156">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="76016-157">Questa proposta aggiunge ulteriore complessità al compilatore in Exchange per un'applicabilità più ampia di stringhe interpolate.</span><span class="sxs-lookup"><span data-stu-id="76016-157">This proposal adds additional complexity to the compiler in exchange for broader applicability of interpolated strings.</span></span> <span data-ttu-id="76016-158">Poiché queste stringhe vengono completamente valutate in fase di compilazione, le funzionalità di formattazione automatica preziose delle stringhe interpolate sono meno necessario.</span><span class="sxs-lookup"><span data-stu-id="76016-158">As these strings are fully evaluated at compile time, the valuable automatic formatting features of interpolated strings are less neccesary.</span></span> <span data-ttu-id="76016-159">La maggior parte dei casi d'uso può essere ampiamente replicata tramite le alternative seguenti.</span><span class="sxs-lookup"><span data-stu-id="76016-159">Most use cases can be largely replicated through the alternatives below.</span></span>

## <a name="alternatives"></a><span data-ttu-id="76016-160">Alternativi</span><span class="sxs-lookup"><span data-stu-id="76016-160">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="76016-161">L' `+` operatore corrente per la stringa concatnation può combinare le stringhe in modo analogo alla proposta corrente.</span><span class="sxs-lookup"><span data-stu-id="76016-161">The current `+` operator for string concatnation can combine strings in a similar manner to the current proposal.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="76016-162">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="76016-162">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="76016-163">Quali parti della progettazione sono ancora indecise?</span><span class="sxs-lookup"><span data-stu-id="76016-163">What parts of the design are still undecided?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="76016-164">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="76016-164">Design meetings</span></span>

<span data-ttu-id="76016-165">Collegamento alle note sulla progettazione che interessano questa proposta e descrivono in una frase per ogni modifica apportata.</span><span class="sxs-lookup"><span data-stu-id="76016-165">Link to design notes that affect this proposal, and describe in one sentence for each what changes they led to.</span></span>


