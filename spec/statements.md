---
ms.openlocfilehash: 94346034a667ad4af26796c0c4bbc96d6ed79aba
ms.sourcegitcommit: 7f7fc6e9e195e51b7ff8229aeaa70aa9fbbb63cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876833"
---
# <a name="statements"></a><span data-ttu-id="635c5-101">Istruzioni</span><span class="sxs-lookup"><span data-stu-id="635c5-101">Statements</span></span>

<span data-ttu-id="635c5-102">C#fornisce una serie di istruzioni.</span><span class="sxs-lookup"><span data-stu-id="635c5-102">C# provides a variety of statements.</span></span> <span data-ttu-id="635c5-103">La maggior parte di queste istruzioni sarà familiare per gli sviluppatori che hanno programmato in C++C e.</span><span class="sxs-lookup"><span data-stu-id="635c5-103">Most of these statements will be familiar to developers who have programmed in C and C++.</span></span>

```antlr
statement
    : labeled_statement
    | declaration_statement
    | embedded_statement
    ;

embedded_statement
    : block
    | empty_statement
    | expression_statement
    | selection_statement
    | iteration_statement
    | jump_statement
    | try_statement
    | checked_statement
    | unchecked_statement
    | lock_statement
    | using_statement
    | yield_statement
    | embedded_statement_unsafe
    ;
```

<span data-ttu-id="635c5-104">Il *embedded_statement* non terminale viene usato per le istruzioni visualizzate all'interno di altre istruzioni.</span><span class="sxs-lookup"><span data-stu-id="635c5-104">The *embedded_statement* nonterminal is used for statements that appear within other statements.</span></span> <span data-ttu-id="635c5-105">L'utilizzo di *embedded_statement* anziché di un' *istruzione* esclude l'utilizzo di istruzioni di dichiarazione e istruzioni con etichetta in questi contesti.</span><span class="sxs-lookup"><span data-stu-id="635c5-105">The use of *embedded_statement* rather than *statement* excludes the use of declaration statements and labeled statements in these contexts.</span></span> <span data-ttu-id="635c5-106">Esempio</span><span class="sxs-lookup"><span data-stu-id="635c5-106">The example</span></span>
```csharp
void F(bool b) {
    if (b)
        int i = 44;
}
```
<span data-ttu-id="635c5-107">genera un errore in fase di compilazione perché un' `if` istruzione richiede un *embedded_statement* anziché un' *istruzione* per il relativo ramo if.</span><span class="sxs-lookup"><span data-stu-id="635c5-107">results in a compile-time error because an `if` statement requires an *embedded_statement* rather than a *statement* for its if branch.</span></span> <span data-ttu-id="635c5-108">Se questo codice è stato consentito, la variabile `i` verrebbe dichiarata, ma non potrebbe mai essere utilizzata.</span><span class="sxs-lookup"><span data-stu-id="635c5-108">If this code were permitted, then the variable `i` would be declared, but it could never be used.</span></span> <span data-ttu-id="635c5-109">Si noti, tuttavia, che `i`inserendo la dichiarazione in un blocco, l'esempio è valido.</span><span class="sxs-lookup"><span data-stu-id="635c5-109">Note, however, that by placing `i`'s declaration in a block, the example is valid.</span></span>

## <a name="end-points-and-reachability"></a><span data-ttu-id="635c5-110">Punti finali e raggiungibilità</span><span class="sxs-lookup"><span data-stu-id="635c5-110">End points and reachability</span></span>

<span data-ttu-id="635c5-111">Ogni istruzione ha un ***endpoint***.</span><span class="sxs-lookup"><span data-stu-id="635c5-111">Every statement has an ***end point***.</span></span> <span data-ttu-id="635c5-112">In termini intuitivi, il punto finale di un'istruzione è il percorso immediatamente successivo all'istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-112">In intuitive terms, the end point of a statement is the location that immediately follows the statement.</span></span> <span data-ttu-id="635c5-113">Le regole di esecuzione per le istruzioni composite (istruzioni che contengono istruzioni incorporate) specificano l'azione eseguita quando il controllo raggiunge il punto finale di un'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="635c5-113">The execution rules for composite statements (statements that contain embedded statements) specify the action that is taken when control reaches the end point of an embedded statement.</span></span> <span data-ttu-id="635c5-114">Ad esempio, quando il controllo raggiunge il punto finale di un'istruzione in un blocco, il controllo viene trasferito all'istruzione successiva nel blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-114">For example, when control reaches the end point of a statement in a block, control is transferred to the next statement in the block.</span></span>

<span data-ttu-id="635c5-115">Se un'istruzione può essere raggiunta dall'esecuzione, l'istruzione è detta ***raggiungibile***.</span><span class="sxs-lookup"><span data-stu-id="635c5-115">If a statement can possibly be reached by execution, the statement is said to be ***reachable***.</span></span> <span data-ttu-id="635c5-116">Viceversa, se non è possibile che venga eseguita un'istruzione, l'istruzione è detta non ***raggiungibile***.</span><span class="sxs-lookup"><span data-stu-id="635c5-116">Conversely, if there is no possibility that a statement will be executed, the statement is said to be ***unreachable***.</span></span>

<span data-ttu-id="635c5-117">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="635c5-117">In the example</span></span>
```csharp
void F() {
    Console.WriteLine("reachable");
    goto Label;
    Console.WriteLine("unreachable");
    Label:
    Console.WriteLine("reachable");
}
```
<span data-ttu-id="635c5-118">la seconda chiamata di `Console.WriteLine` non è raggiungibile perché non è possibile che l'istruzione venga eseguita.</span><span class="sxs-lookup"><span data-stu-id="635c5-118">the second invocation of `Console.WriteLine` is unreachable because there is no possibility that the statement will be executed.</span></span>

<span data-ttu-id="635c5-119">Viene segnalato un avviso se il compilatore determina che un'istruzione non è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-119">A warning is reported if the compiler determines that a statement is unreachable.</span></span> <span data-ttu-id="635c5-120">In particolare, non è un errore perché un'istruzione non sia raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-120">It is specifically not an error for a statement to be unreachable.</span></span>

<span data-ttu-id="635c5-121">Per determinare se un'istruzione o un endpoint specifico è raggiungibile, il compilatore esegue l'analisi del flusso in base alle regole di raggiungibilità definite per ogni istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-121">To determine whether a particular statement or end point is reachable, the compiler performs flow analysis according to the reachability rules defined for each statement.</span></span> <span data-ttu-id="635c5-122">L'analisi del flusso prende in considerazione i valori delle espressioni costanti ([espressioni costanti](expressions.md#constant-expressions)) che controllano il comportamento delle istruzioni, ma i valori possibili delle espressioni non costanti non vengono considerati.</span><span class="sxs-lookup"><span data-stu-id="635c5-122">The flow analysis takes into account the values of constant expressions ([Constant expressions](expressions.md#constant-expressions)) that control the behavior of statements, but the possible values of non-constant expressions are not considered.</span></span> <span data-ttu-id="635c5-123">In altre parole, per finalità di analisi del flusso di controllo, un'espressione non costante di un determinato tipo viene considerata come un possibile valore di quel tipo.</span><span class="sxs-lookup"><span data-stu-id="635c5-123">In other words, for purposes of control flow analysis, a non-constant expression of a given type is considered to have any possible value of that type.</span></span>

<span data-ttu-id="635c5-124">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="635c5-124">In the example</span></span>
```csharp
void F() {
    const int i = 1;
    if (i == 2) Console.WriteLine("unreachable");
}
```
<span data-ttu-id="635c5-125">l'espressione booleana dell' `if` istruzione è un'espressione costante perché entrambi gli `==` operandi dell'operatore sono costanti.</span><span class="sxs-lookup"><span data-stu-id="635c5-125">the boolean expression of the `if` statement is a constant expression because both operands of the `==` operator are constants.</span></span> <span data-ttu-id="635c5-126">Poiché l'espressione costante viene valutata in fase di compilazione, producendo `false`il valore `Console.WriteLine` , la chiamata viene considerata irraggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-126">As the constant expression is evaluated at compile-time, producing the value `false`, the `Console.WriteLine` invocation is considered unreachable.</span></span> <span data-ttu-id="635c5-127">Tuttavia, se `i` viene modificato come variabile locale</span><span class="sxs-lookup"><span data-stu-id="635c5-127">However, if `i` is changed to be a local variable</span></span>
```csharp
void F() {
    int i = 1;
    if (i == 2) Console.WriteLine("reachable");
}
```
<span data-ttu-id="635c5-128">la `Console.WriteLine` chiamata è considerata raggiungibile, anche se, in realtà, non verrà mai eseguita.</span><span class="sxs-lookup"><span data-stu-id="635c5-128">the `Console.WriteLine` invocation is considered reachable, even though, in reality, it will never be executed.</span></span>

<span data-ttu-id="635c5-129">Il *blocco* di un membro di funzione è sempre considerato raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-129">The *block* of a function member is always considered reachable.</span></span> <span data-ttu-id="635c5-130">Se successivamente si valutano le regole di raggiungibilità di ogni istruzione in un blocco, è possibile determinare la raggiungibilità di una determinata istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-130">By successively evaluating the reachability rules of each statement in a block, the reachability of any given statement can be determined.</span></span>

<span data-ttu-id="635c5-131">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="635c5-131">In the example</span></span>
```csharp
void F(int x) {
    Console.WriteLine("start");
    if (x < 0) Console.WriteLine("negative");
}
```
<span data-ttu-id="635c5-132">la raggiungibilità della seconda `Console.WriteLine` viene determinata nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="635c5-132">the reachability of the second `Console.WriteLine` is determined as follows:</span></span>

*  <span data-ttu-id="635c5-133">La prima `Console.WriteLine` istruzione `F` di espressione è raggiungibile perché il blocco del metodo è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-133">The first `Console.WriteLine` expression statement is reachable because the block of the `F` method is reachable.</span></span>
*  <span data-ttu-id="635c5-134">Il punto finale della prima `Console.WriteLine` istruzione di espressione è raggiungibile perché tale istruzione è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-134">The end point of the first `Console.WriteLine` expression statement is reachable because that statement is reachable.</span></span>
*  <span data-ttu-id="635c5-135">L' `if` istruzione è raggiungibile perché il punto finale della prima `Console.WriteLine` istruzione di espressione è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-135">The `if` statement is reachable because the end point of the first `Console.WriteLine` expression statement is reachable.</span></span>
*  <span data-ttu-id="635c5-136">La seconda `Console.WriteLine` istruzione `if` di espressione è raggiungibile perché l'espressione booleana dell'istruzione non ha il valore `false`costante.</span><span class="sxs-lookup"><span data-stu-id="635c5-136">The second `Console.WriteLine` expression statement is reachable because the boolean expression of the `if` statement does not have the constant value `false`.</span></span>

<span data-ttu-id="635c5-137">Ci sono due situazioni in cui si verifica un errore in fase di compilazione per il raggiungimento del punto finale di un'istruzione:</span><span class="sxs-lookup"><span data-stu-id="635c5-137">There are two situations in which it is a compile-time error for the end point of a statement to be reachable:</span></span>

*  <span data-ttu-id="635c5-138">Poiché l' `switch` istruzione non consente a una sezione switch di passare alla sezione switch successiva, si tratta di un errore in fase di compilazione per il punto finale dell'elenco di istruzioni di una sezione di opzioni da raggiungere.</span><span class="sxs-lookup"><span data-stu-id="635c5-138">Because the `switch` statement does not permit a switch section to "fall through" to the next switch section, it is a compile-time error for the end point of the statement list of a switch section to be reachable.</span></span> <span data-ttu-id="635c5-139">Se si verifica questo errore, in genere indica che manca un' `break` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-139">If this error occurs, it is typically an indication that a `break` statement is missing.</span></span>
*  <span data-ttu-id="635c5-140">Si tratta di un errore in fase di compilazione per il punto finale del blocco di un membro di funzione che calcola un valore in modo che sia raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-140">It is a compile-time error for the end point of the block of a function member that computes a value to be reachable.</span></span> <span data-ttu-id="635c5-141">Se si verifica questo errore, in genere indica che un' `return` istruzione è mancante.</span><span class="sxs-lookup"><span data-stu-id="635c5-141">If this error occurs, it typically is an indication that a `return` statement is missing.</span></span>

## <a name="blocks"></a><span data-ttu-id="635c5-142">Blocchi</span><span class="sxs-lookup"><span data-stu-id="635c5-142">Blocks</span></span>

<span data-ttu-id="635c5-143">Un *blocco* consente di scrivere più istruzioni nei contesti in cui ne è consentita una sola.</span><span class="sxs-lookup"><span data-stu-id="635c5-143">A *block* permits multiple statements to be written in contexts where a single statement is allowed.</span></span>

```antlr
block
    : '{' statement_list? '}'
    ;
```

<span data-ttu-id="635c5-144">Un *blocco* è costituito da un *statement_list* facoltativo ([elenchi di istruzioni](statements.md#statement-lists)) racchiuso tra parentesi graffe.</span><span class="sxs-lookup"><span data-stu-id="635c5-144">A *block* consists of an optional *statement_list* ([Statement lists](statements.md#statement-lists)), enclosed in braces.</span></span> <span data-ttu-id="635c5-145">Se l'elenco di istruzioni viene omesso, il blocco viene definito vuoto.</span><span class="sxs-lookup"><span data-stu-id="635c5-145">If the statement list is omitted, the block is said to be empty.</span></span>

<span data-ttu-id="635c5-146">Un blocco può contenere istruzioni di dichiarazione ([istruzioni di dichiarazione](statements.md#declaration-statements)).</span><span class="sxs-lookup"><span data-stu-id="635c5-146">A block may contain declaration statements ([Declaration statements](statements.md#declaration-statements)).</span></span> <span data-ttu-id="635c5-147">L'ambito di una variabile locale o di una costante dichiarata in un blocco è il blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-147">The scope of a local variable or constant declared in a block is the block.</span></span>

<span data-ttu-id="635c5-148">Un blocco viene eseguito come segue:</span><span class="sxs-lookup"><span data-stu-id="635c5-148">A block is executed as follows:</span></span>

*  <span data-ttu-id="635c5-149">Se il blocco è vuoto, il controllo viene trasferito al punto finale del blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-149">If the block is empty, control is transferred to the end point of the block.</span></span>
*  <span data-ttu-id="635c5-150">Se il blocco non è vuoto, il controllo viene trasferito all'elenco delle istruzioni.</span><span class="sxs-lookup"><span data-stu-id="635c5-150">If the block is not empty, control is transferred to the statement list.</span></span> <span data-ttu-id="635c5-151">Quando e se il controllo raggiunge il punto finale dell'elenco di istruzioni, il controllo viene trasferito al punto finale del blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-151">When and if control reaches the end point of the statement list, control is transferred to the end point of the block.</span></span>

<span data-ttu-id="635c5-152">L'elenco di istruzioni di un blocco è raggiungibile se il blocco stesso è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-152">The statement list of a block is reachable if the block itself is reachable.</span></span>

<span data-ttu-id="635c5-153">Il punto finale di un blocco è raggiungibile se il blocco è vuoto o se il punto finale dell'elenco di istruzioni è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-153">The end point of a block is reachable if the block is empty or if the end point of the statement list is reachable.</span></span>

<span data-ttu-id="635c5-154">Un *blocco* che contiene una o più `yield` istruzioni ([l'istruzione yield](statements.md#the-yield-statement)) viene definito blocco iteratore.</span><span class="sxs-lookup"><span data-stu-id="635c5-154">A *block* that contains one or more `yield` statements ([The yield statement](statements.md#the-yield-statement)) is called an iterator block.</span></span> <span data-ttu-id="635c5-155">I blocchi iteratori vengono usati per implementare i membri della funzione come iteratori ([iteratori](classes.md#iterators)).</span><span class="sxs-lookup"><span data-stu-id="635c5-155">Iterator blocks are used to implement function members as iterators ([Iterators](classes.md#iterators)).</span></span> <span data-ttu-id="635c5-156">Per i blocchi iteratori vengono applicate alcune restrizioni aggiuntive:</span><span class="sxs-lookup"><span data-stu-id="635c5-156">Some additional restrictions apply to iterator blocks:</span></span>

*  <span data-ttu-id="635c5-157">Si tratta di un errore in fase di compilazione `return` perché un'istruzione venga visualizzata in un blocco iteratore (ma `yield return` sono consentite le istruzioni).</span><span class="sxs-lookup"><span data-stu-id="635c5-157">It is a compile-time error for a `return` statement to appear in an iterator block (but `yield return` statements are permitted).</span></span>
*  <span data-ttu-id="635c5-158">Si tratta di un errore in fase di compilazione per un blocco iteratore che contiene un contesto non sicuro ([contesti non sicuri](unsafe-code.md#unsafe-contexts)).</span><span class="sxs-lookup"><span data-stu-id="635c5-158">It is a compile-time error for an iterator block to contain an unsafe context ([Unsafe contexts](unsafe-code.md#unsafe-contexts)).</span></span> <span data-ttu-id="635c5-159">Un blocco iteratore definisce sempre un contesto sicuro, anche quando la relativa dichiarazione è annidata in un contesto non sicuro.</span><span class="sxs-lookup"><span data-stu-id="635c5-159">An iterator block always defines a safe context, even when its declaration is nested in an unsafe context.</span></span>

### <a name="statement-lists"></a><span data-ttu-id="635c5-160">Elenchi di istruzioni</span><span class="sxs-lookup"><span data-stu-id="635c5-160">Statement lists</span></span>

<span data-ttu-id="635c5-161">Un ***elenco*** di istruzioni è costituito da una o più istruzioni scritte in sequenza.</span><span class="sxs-lookup"><span data-stu-id="635c5-161">A ***statement list*** consists of one or more statements written in sequence.</span></span> <span data-ttu-id="635c5-162">Gli elenchi di istruzioni si verificano in *Block*s[(blocks) e](statements.md#blocks)in *switch_block*s ([l'istruzione switch](statements.md#the-switch-statement)).</span><span class="sxs-lookup"><span data-stu-id="635c5-162">Statement lists occur in *block*s ([Blocks](statements.md#blocks)) and in *switch_block*s ([The switch statement](statements.md#the-switch-statement)).</span></span>

```antlr
statement_list
    : statement+
    ;
```

<span data-ttu-id="635c5-163">Un elenco di istruzioni viene eseguito trasferendo il controllo alla prima istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-163">A statement list is executed by transferring control to the first statement.</span></span> <span data-ttu-id="635c5-164">Quando e se il controllo raggiunge il punto finale di un'istruzione, il controllo viene trasferito all'istruzione successiva.</span><span class="sxs-lookup"><span data-stu-id="635c5-164">When and if control reaches the end point of a statement, control is transferred to the next statement.</span></span> <span data-ttu-id="635c5-165">Quando e se il controllo raggiunge il punto finale dell'ultima istruzione, il controllo viene trasferito al punto finale dell'elenco di istruzioni.</span><span class="sxs-lookup"><span data-stu-id="635c5-165">When and if control reaches the end point of the last statement, control is transferred to the end point of the statement list.</span></span>

<span data-ttu-id="635c5-166">Un'istruzione in un elenco di istruzioni è raggiungibile se si verifica almeno una delle condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="635c5-166">A statement in a statement list is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="635c5-167">L'istruzione è la prima istruzione e l'elenco di istruzioni è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-167">The statement is the first statement and the statement list itself is reachable.</span></span>
*  <span data-ttu-id="635c5-168">Il punto finale dell'istruzione precedente è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-168">The end point of the preceding statement is reachable.</span></span>
*  <span data-ttu-id="635c5-169">L'istruzione è un'istruzione con etichetta e l'etichetta fa riferimento a un'istruzione raggiungibile `goto` .</span><span class="sxs-lookup"><span data-stu-id="635c5-169">The statement is a labeled statement and the label is referenced by a reachable `goto` statement.</span></span>

<span data-ttu-id="635c5-170">Il punto finale di un elenco di istruzioni è raggiungibile se il punto finale dell'ultima istruzione nell'elenco è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-170">The end point of a statement list is reachable if the end point of the last statement in the list is reachable.</span></span>

## <a name="the-empty-statement"></a><span data-ttu-id="635c5-171">Istruzione vuota</span><span class="sxs-lookup"><span data-stu-id="635c5-171">The empty statement</span></span>

<span data-ttu-id="635c5-172">Un *empty_statement* non esegue alcuna operazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-172">An *empty_statement* does nothing.</span></span>

```antlr
empty_statement
    : ';'
    ;
```

<span data-ttu-id="635c5-173">Quando non sono presenti operazioni da eseguire in un contesto in cui è richiesta un'istruzione, viene utilizzata un'istruzione vuota.</span><span class="sxs-lookup"><span data-stu-id="635c5-173">An empty statement is used when there are no operations to perform in a context where a statement is required.</span></span>

<span data-ttu-id="635c5-174">L'esecuzione di un'istruzione vuota trasferisce semplicemente il controllo al punto finale dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-174">Execution of an empty statement simply transfers control to the end point of the statement.</span></span> <span data-ttu-id="635c5-175">Pertanto, il punto finale di un'istruzione vuota è raggiungibile se l'istruzione Empty è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-175">Thus, the end point of an empty statement is reachable if the empty statement is reachable.</span></span>

<span data-ttu-id="635c5-176">Quando si scrive un' `while` istruzione con un corpo null, è possibile utilizzare un'istruzione vuota:</span><span class="sxs-lookup"><span data-stu-id="635c5-176">An empty statement can be used when writing a `while` statement with a null body:</span></span>
```csharp
bool ProcessMessage() {...}

void ProcessMessages() {
    while (ProcessMessage())
        ;
}
```

<span data-ttu-id="635c5-177">Inoltre, è possibile utilizzare un'istruzione vuota per dichiarare un'etichetta immediatamente prima della chiusura`}`di un blocco:</span><span class="sxs-lookup"><span data-stu-id="635c5-177">Also, an empty statement can be used to declare a label just before the closing "`}`" of a block:</span></span>
```csharp
void F() {
    ...
    if (done) goto exit;
    ...
    exit: ;
}
```

## <a name="labeled-statements"></a><span data-ttu-id="635c5-178">Istruzioni con etichetta</span><span class="sxs-lookup"><span data-stu-id="635c5-178">Labeled statements</span></span>

<span data-ttu-id="635c5-179">Un *labeled_statement* consente a un'istruzione di essere preceduta da un'etichetta.</span><span class="sxs-lookup"><span data-stu-id="635c5-179">A *labeled_statement* permits a statement to be prefixed by a label.</span></span> <span data-ttu-id="635c5-180">Le istruzioni con etichetta sono consentite in blocchi, ma non sono consentite come istruzioni incorporate.</span><span class="sxs-lookup"><span data-stu-id="635c5-180">Labeled statements are permitted in blocks, but are not permitted as embedded statements.</span></span>

```antlr
labeled_statement
    : identifier ':' statement
    ;
```

<span data-ttu-id="635c5-181">Un'istruzione con etichetta dichiara un'etichetta con il nome specificato dall' *identificatore*.</span><span class="sxs-lookup"><span data-stu-id="635c5-181">A labeled statement declares a label with the name given by the *identifier*.</span></span> <span data-ttu-id="635c5-182">L'ambito di un'etichetta è l'intero blocco in cui è dichiarata l'etichetta, inclusi tutti i blocchi annidati.</span><span class="sxs-lookup"><span data-stu-id="635c5-182">The scope of a label is the whole block in which the label is declared, including any nested blocks.</span></span> <span data-ttu-id="635c5-183">Si tratta di un errore in fase di compilazione per due etichette con lo stesso nome con ambiti sovrapposti.</span><span class="sxs-lookup"><span data-stu-id="635c5-183">It is a compile-time error for two labels with the same name to have overlapping scopes.</span></span>

<span data-ttu-id="635c5-184">È possibile fare riferimento a un'etichetta `goto` dalle istruzioni ([istruzione goto](statements.md#the-goto-statement)) nell'ambito dell'etichetta.</span><span class="sxs-lookup"><span data-stu-id="635c5-184">A label can be referenced from `goto` statements ([The goto statement](statements.md#the-goto-statement)) within the scope of the label.</span></span> <span data-ttu-id="635c5-185">Ciò significa che `goto` le istruzioni possono trasferire il controllo all'interno di blocchi e blocchi, ma mai in blocchi.</span><span class="sxs-lookup"><span data-stu-id="635c5-185">This means that `goto` statements can transfer control within blocks and out of blocks, but never into blocks.</span></span>

<span data-ttu-id="635c5-186">Le etichette hanno uno spazio di dichiarazione e non interferiscono con altri identificatori.</span><span class="sxs-lookup"><span data-stu-id="635c5-186">Labels have their own declaration space and do not interfere with other identifiers.</span></span> <span data-ttu-id="635c5-187">Esempio</span><span class="sxs-lookup"><span data-stu-id="635c5-187">The example</span></span>
```csharp
int F(int x) {
    if (x >= 0) goto x;
    x = -x;
    x: return x;
}
```
<span data-ttu-id="635c5-188">è valido e usa il nome `x` sia come parametro che come etichetta.</span><span class="sxs-lookup"><span data-stu-id="635c5-188">is valid and uses the name `x` as both a parameter and a label.</span></span>

<span data-ttu-id="635c5-189">L'esecuzione di un'istruzione con etichetta corrisponde esattamente all'esecuzione dell'istruzione che segue l'etichetta.</span><span class="sxs-lookup"><span data-stu-id="635c5-189">Execution of a labeled statement corresponds exactly to execution of the statement following the label.</span></span>

<span data-ttu-id="635c5-190">Oltre alla raggiungibilità fornita da un normale flusso di controllo, un'istruzione con etichetta è raggiungibile se un' `goto` istruzione raggiungibile fa riferimento a tale etichetta.</span><span class="sxs-lookup"><span data-stu-id="635c5-190">In addition to the reachability provided by normal flow of control, a labeled statement is reachable if the label is referenced by a reachable `goto` statement.</span></span> <span data-ttu-id="635c5-191">Eccezione Se un' `goto` istruzione si trova all' `try` interno di un `finally` oggetto che include un blocco `try`e l'istruzione con etichetta è esterna a e il punto finale `finally` del blocco non è raggiungibile, l'istruzione con etichetta non è raggiungibile da Questa `goto` istruzione).</span><span class="sxs-lookup"><span data-stu-id="635c5-191">(Exception: If a `goto` statement is inside a `try` that includes a `finally` block, and the labeled statement is outside the `try`, and the end point of the `finally` block is unreachable, then the labeled statement is not reachable from that `goto` statement.)</span></span>

## <a name="declaration-statements"></a><span data-ttu-id="635c5-192">Istruzioni di dichiarazione</span><span class="sxs-lookup"><span data-stu-id="635c5-192">Declaration statements</span></span>

<span data-ttu-id="635c5-193">Un *declaration_statement* dichiara una variabile o una costante locale.</span><span class="sxs-lookup"><span data-stu-id="635c5-193">A *declaration_statement* declares a local variable or constant.</span></span> <span data-ttu-id="635c5-194">Le istruzioni di dichiarazione sono consentite in blocchi, ma non sono consentite come istruzioni incorporate.</span><span class="sxs-lookup"><span data-stu-id="635c5-194">Declaration statements are permitted in blocks, but are not permitted as embedded statements.</span></span>

```antlr
declaration_statement
    : local_variable_declaration ';'
    | local_constant_declaration ';'
    ;
```

### <a name="local-variable-declarations"></a><span data-ttu-id="635c5-195">Dichiarazioni di variabili locali</span><span class="sxs-lookup"><span data-stu-id="635c5-195">Local variable declarations</span></span>

<span data-ttu-id="635c5-196">Un *local_variable_declaration* dichiara una o più variabili locali.</span><span class="sxs-lookup"><span data-stu-id="635c5-196">A *local_variable_declaration* declares one or more local variables.</span></span>

```antlr
local_variable_declaration
    : local_variable_type local_variable_declarators
    ;

local_variable_type
    : type
    | 'var'
    ;

local_variable_declarators
    : local_variable_declarator
    | local_variable_declarators ',' local_variable_declarator
    ;

local_variable_declarator
    : identifier
    | identifier '=' local_variable_initializer
    ;

local_variable_initializer
    : expression
    | array_initializer
    | local_variable_initializer_unsafe
    ;
```

<span data-ttu-id="635c5-197">Il *local_variable_type* di un *local_variable_declaration* specifica direttamente il tipo delle variabili introdotte dalla dichiarazione o indica con l'identificatore `var` che il tipo deve essere dedotto in base a un inizializzatore.</span><span class="sxs-lookup"><span data-stu-id="635c5-197">The *local_variable_type* of a *local_variable_declaration* either directly specifies the type of the variables introduced by the declaration, or indicates with the identifier `var` that the type should be inferred based on an initializer.</span></span> <span data-ttu-id="635c5-198">Il tipo è seguito da un elenco di *local_variable_declarator*, ognuno dei quali introduce una nuova variabile.</span><span class="sxs-lookup"><span data-stu-id="635c5-198">The type is followed by a list of *local_variable_declarator*s, each of which introduces a new variable.</span></span> <span data-ttu-id="635c5-199">Un *local_variable_declarator* è costituito da un *identificatore* che denomina la variabile, seguito facoltativamente da`=`un token "" e da un *local_variable_initializer* che fornisce il valore iniziale della variabile.</span><span class="sxs-lookup"><span data-stu-id="635c5-199">A *local_variable_declarator* consists of an *identifier* that names the variable, optionally followed by an "`=`" token and a *local_variable_initializer* that gives the initial value of the variable.</span></span>

<span data-ttu-id="635c5-200">Nel contesto di una dichiarazione di variabile locale, l'identificatore var funge da[parola chiave contestuale (](lexical-structure.md#keywords)Keywords). Quando *local_variable_type* viene specificato come `var` e nessun tipo denominato `var` è nell'ambito, la dichiarazione è una dichiarazione di ***variabile locale tipizzata in modo implicito***, il cui tipo viene dedotto dal tipo dell'inizializzatore associato espressione.</span><span class="sxs-lookup"><span data-stu-id="635c5-200">In the context of a local variable declaration, the identifier var acts as a contextual keyword ([Keywords](lexical-structure.md#keywords)).When the *local_variable_type* is specified as `var` and no type named `var` is in scope, the declaration is an ***implicitly typed local variable declaration***, whose type is inferred from the type of the associated initializer expression.</span></span> <span data-ttu-id="635c5-201">Le dichiarazioni di variabili locali tipizzate in modo implicito sono soggette alle restrizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="635c5-201">Implicitly typed local variable declarations are subject to the following restrictions:</span></span>

*  <span data-ttu-id="635c5-202">*Local_variable_declaration* non può includere più *local_variable_declarator*.</span><span class="sxs-lookup"><span data-stu-id="635c5-202">The *local_variable_declaration* cannot include multiple *local_variable_declarator*s.</span></span>
*  <span data-ttu-id="635c5-203">*Local_variable_declarator* deve includere un *local_variable_initializer*.</span><span class="sxs-lookup"><span data-stu-id="635c5-203">The *local_variable_declarator* must include a *local_variable_initializer*.</span></span>
*  <span data-ttu-id="635c5-204">*Local_variable_initializer* deve essere un' *espressione*.</span><span class="sxs-lookup"><span data-stu-id="635c5-204">The *local_variable_initializer* must be an *expression*.</span></span>
*  <span data-ttu-id="635c5-205">L' *espressione* dell'inizializzatore deve avere un tipo in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-205">The initializer *expression* must have a compile-time type.</span></span>
*  <span data-ttu-id="635c5-206">L' *espressione* dell'inizializzatore non può fare riferimento alla variabile dichiarata stessa</span><span class="sxs-lookup"><span data-stu-id="635c5-206">The initializer *expression* cannot refer to the declared variable itself</span></span>

<span data-ttu-id="635c5-207">Di seguito sono riportati alcuni esempi di dichiarazioni di variabili locali non corrette tipizzate in modo implicito:</span><span class="sxs-lookup"><span data-stu-id="635c5-207">The following are examples of incorrect implicitly typed local variable declarations:</span></span>

```csharp
var x;               // Error, no initializer to infer type from
var y = {1, 2, 3};   // Error, array initializer not permitted
var z = null;        // Error, null does not have a type
var u = x => x + 1;  // Error, anonymous functions do not have a type
var v = v++;         // Error, initializer cannot refer to variable itself
```

<span data-ttu-id="635c5-208">Il valore di una variabile locale viene ottenuto in un'espressione usando un *simple_name* ([nomi semplici](expressions.md#simple-names)) e il valore di una variabile locale viene modificato usando un' *assegnazione* ([operatori di assegnazione](expressions.md#assignment-operators)).</span><span class="sxs-lookup"><span data-stu-id="635c5-208">The value of a local variable is obtained in an expression using a *simple_name* ([Simple names](expressions.md#simple-names)), and the value of a local variable is modified using an *assignment* ([Assignment operators](expressions.md#assignment-operators)).</span></span> <span data-ttu-id="635c5-209">Una variabile locale deve essere assegnata definitivamente ([assegnazione definita](variables.md#definite-assignment)) in ogni posizione in cui viene ottenuto il relativo valore.</span><span class="sxs-lookup"><span data-stu-id="635c5-209">A local variable must be definitely assigned ([Definite assignment](variables.md#definite-assignment)) at each location where its value is obtained.</span></span>

<span data-ttu-id="635c5-210">L'ambito di una variabile locale dichiarata in un *local_variable_declaration* è il blocco in cui si verifica la dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-210">The scope of a local variable declared in a *local_variable_declaration* is the block in which the declaration occurs.</span></span> <span data-ttu-id="635c5-211">Si tratta di un errore per fare riferimento a una variabile locale in una posizione testuale che precede *local_variable_declarator* della variabile locale.</span><span class="sxs-lookup"><span data-stu-id="635c5-211">It is an error to refer to a local variable in a textual position that precedes the *local_variable_declarator* of the local variable.</span></span> <span data-ttu-id="635c5-212">Nell'ambito di una variabile locale, si tratta di un errore in fase di compilazione per dichiarare un'altra variabile o costante locale con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="635c5-212">Within the scope of a local variable, it is a compile-time error to declare another local variable or constant with the same name.</span></span>

<span data-ttu-id="635c5-213">Una dichiarazione di variabile locale che dichiara più variabili è equivalente a più dichiarazioni di variabili singole con lo stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="635c5-213">A local variable declaration that declares multiple variables is equivalent to multiple declarations of single variables with the same type.</span></span> <span data-ttu-id="635c5-214">Inoltre, un inizializzatore di variabile in una dichiarazione di variabile locale corrisponde esattamente a un'istruzione di assegnazione inserita immediatamente dopo la dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-214">Furthermore, a variable initializer in a local variable declaration corresponds exactly to an assignment statement that is inserted immediately after the declaration.</span></span>

<span data-ttu-id="635c5-215">Esempio</span><span class="sxs-lookup"><span data-stu-id="635c5-215">The example</span></span>
```csharp
void F() {
    int x = 1, y, z = x * 2;
}
```
<span data-ttu-id="635c5-216">corrisponde esattamente a</span><span class="sxs-lookup"><span data-stu-id="635c5-216">corresponds exactly to</span></span>
```csharp
void F() {
    int x; x = 1;
    int y;
    int z; z = x * 2;
}
```

<span data-ttu-id="635c5-217">In una dichiarazione di variabile locale tipizzata in modo implicito, il tipo della variabile locale dichiarata viene considerato uguale al tipo dell'espressione utilizzata per inizializzare la variabile.</span><span class="sxs-lookup"><span data-stu-id="635c5-217">In an implicitly typed local variable declaration, the type of the local variable being declared is taken to be the same as the type of the expression used to initialize the variable.</span></span> <span data-ttu-id="635c5-218">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="635c5-218">For example:</span></span>
```csharp
var i = 5;
var s = "Hello";
var d = 1.0;
var numbers = new int[] {1, 2, 3};
var orders = new Dictionary<int,Order>();
```

<span data-ttu-id="635c5-219">Le dichiarazioni di variabili locali tipizzate in modo implicito sopra sono esattamente equivalenti alle dichiarazioni tipizzate in modo esplicito seguenti:</span><span class="sxs-lookup"><span data-stu-id="635c5-219">The implicitly typed local variable declarations above are precisely equivalent to the following explicitly typed declarations:</span></span>
```csharp
int i = 5;
string s = "Hello";
double d = 1.0;
int[] numbers = new int[] {1, 2, 3};
Dictionary<int,Order> orders = new Dictionary<int,Order>();
```

### <a name="local-constant-declarations"></a><span data-ttu-id="635c5-220">Dichiarazioni di costanti locali</span><span class="sxs-lookup"><span data-stu-id="635c5-220">Local constant declarations</span></span>

<span data-ttu-id="635c5-221">Un *local_constant_declaration* dichiara una o più costanti locali.</span><span class="sxs-lookup"><span data-stu-id="635c5-221">A *local_constant_declaration* declares one or more local constants.</span></span>

```antlr
local_constant_declaration
    : 'const' type constant_declarators
    ;

constant_declarators
    : constant_declarator (',' constant_declarator)*
    ;

constant_declarator
    : identifier '=' constant_expression
    ;
```

<span data-ttu-id="635c5-222">Il *tipo* di *local_constant_declaration* specifica il tipo delle costanti introdotte dalla dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-222">The *type* of a *local_constant_declaration* specifies the type of the constants introduced by the declaration.</span></span> <span data-ttu-id="635c5-223">Il tipo è seguito da un elenco di *constant_declarator*, ognuno dei quali introduce una nuova costante.</span><span class="sxs-lookup"><span data-stu-id="635c5-223">The type is followed by a list of *constant_declarator*s, each of which introduces a new constant.</span></span> <span data-ttu-id="635c5-224">Un *constant_declarator* è costituito da un *identificatore* che assegna un nome alla costante,`=`seguito da un token "", seguito da un *constant_expression* ([espressioni costanti](expressions.md#constant-expressions)) che fornisce il valore della costante.</span><span class="sxs-lookup"><span data-stu-id="635c5-224">A *constant_declarator* consists of an *identifier* that names the constant, followed by an "`=`" token, followed by a *constant_expression* ([Constant expressions](expressions.md#constant-expressions)) that gives the value of the constant.</span></span>

<span data-ttu-id="635c5-225">Il *tipo* e il *constant_expression* di una dichiarazione di costante locale devono rispettare le stesse regole di una dichiarazione di membro costante ([costanti](classes.md#constants)).</span><span class="sxs-lookup"><span data-stu-id="635c5-225">The *type* and *constant_expression* of a local constant declaration must follow the same rules as those of a constant member declaration ([Constants](classes.md#constants)).</span></span>

<span data-ttu-id="635c5-226">Il valore di una costante locale viene ottenuto in un'espressione usando un *simple_name* ([nomi semplici](expressions.md#simple-names)).</span><span class="sxs-lookup"><span data-stu-id="635c5-226">The value of a local constant is obtained in an expression using a *simple_name* ([Simple names](expressions.md#simple-names)).</span></span>

<span data-ttu-id="635c5-227">L'ambito di una costante locale è il blocco in cui si verifica la dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-227">The scope of a local constant is the block in which the declaration occurs.</span></span> <span data-ttu-id="635c5-228">Si tratta di un errore per fare riferimento a una costante locale in una posizione testuale che precede il relativo *constant_declarator*.</span><span class="sxs-lookup"><span data-stu-id="635c5-228">It is an error to refer to a local constant in a textual position that precedes its *constant_declarator*.</span></span> <span data-ttu-id="635c5-229">Nell'ambito di una costante locale, si tratta di un errore in fase di compilazione per dichiarare un'altra variabile o costante locale con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="635c5-229">Within the scope of a local constant, it is a compile-time error to declare another local variable or constant with the same name.</span></span>

<span data-ttu-id="635c5-230">Una dichiarazione di costante locale che dichiara più costanti è equivalente a più dichiarazioni di costanti singole con lo stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="635c5-230">A local constant declaration that declares multiple constants is equivalent to multiple declarations of single constants with the same type.</span></span>

## <a name="expression-statements"></a><span data-ttu-id="635c5-231">Istruzioni di espressione</span><span class="sxs-lookup"><span data-stu-id="635c5-231">Expression statements</span></span>

<span data-ttu-id="635c5-232">Un *expression_statement* valuta un'espressione specificata.</span><span class="sxs-lookup"><span data-stu-id="635c5-232">An *expression_statement* evaluates a given expression.</span></span> <span data-ttu-id="635c5-233">Il valore calcolato dall'espressione, se presente, viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="635c5-233">The value computed by the expression, if any, is discarded.</span></span>

```antlr
expression_statement
    : statement_expression ';'
    ;

statement_expression
    : invocation_expression
    | null_conditional_invocation_expression
    | object_creation_expression
    | assignment
    | post_increment_expression
    | post_decrement_expression
    | pre_increment_expression
    | pre_decrement_expression
    | await_expression
    ;
```

<span data-ttu-id="635c5-234">Non tutte le espressioni sono consentite come istruzioni.</span><span class="sxs-lookup"><span data-stu-id="635c5-234">Not all expressions are permitted as statements.</span></span> <span data-ttu-id="635c5-235">In particolare, `x + y` le espressioni come e `x == 1` che calcolano semplicemente un valore (che verrà rimosso) non sono consentite come istruzioni.</span><span class="sxs-lookup"><span data-stu-id="635c5-235">In particular, expressions such as `x + y` and `x == 1` that merely compute a value (which will be discarded), are not permitted as statements.</span></span>

<span data-ttu-id="635c5-236">L'esecuzione di un *expression_statement* valuta l'espressione contenuta e quindi trasferisce il controllo al punto finale di *expression_statement*.</span><span class="sxs-lookup"><span data-stu-id="635c5-236">Execution of an *expression_statement* evaluates the contained expression and then transfers control to the end point of the *expression_statement*.</span></span> <span data-ttu-id="635c5-237">Il punto finale di un *expression_statement* è raggiungibile se il *expression_statement* è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-237">The end point of an *expression_statement* is reachable if that *expression_statement* is reachable.</span></span>

## <a name="selection-statements"></a><span data-ttu-id="635c5-238">Istruzioni di selezione</span><span class="sxs-lookup"><span data-stu-id="635c5-238">Selection statements</span></span>

<span data-ttu-id="635c5-239">Istruzioni di selezione consente di selezionare una delle diverse istruzioni per l'esecuzione in base al valore di un'espressione.</span><span class="sxs-lookup"><span data-stu-id="635c5-239">Selection statements select one of a number of possible statements for execution based on the value of some expression.</span></span>

```antlr
selection_statement
    : if_statement
    | switch_statement
    ;
```

### <a name="the-if-statement"></a><span data-ttu-id="635c5-240">Istruzione if</span><span class="sxs-lookup"><span data-stu-id="635c5-240">The if statement</span></span>

<span data-ttu-id="635c5-241">L' `if` istruzione seleziona un'istruzione per l'esecuzione in base al valore di un'espressione booleana.</span><span class="sxs-lookup"><span data-stu-id="635c5-241">The `if` statement selects a statement for execution based on the value of a boolean expression.</span></span>

```antlr
if_statement
    : 'if' '(' boolean_expression ')' embedded_statement
    | 'if' '(' boolean_expression ')' embedded_statement 'else' embedded_statement
    ;
```

<span data-ttu-id="635c5-242">Una `else` parte è associata al valore precedente `if` lessicalmente più vicino consentito dalla sintassi.</span><span class="sxs-lookup"><span data-stu-id="635c5-242">An `else` part is associated with the lexically nearest preceding `if` that is allowed by the syntax.</span></span> <span data-ttu-id="635c5-243">Quindi, un' `if` istruzione nel formato</span><span class="sxs-lookup"><span data-stu-id="635c5-243">Thus, an `if` statement of the form</span></span>
```csharp
if (x) if (y) F(); else G();
```
<span data-ttu-id="635c5-244">equivale a</span><span class="sxs-lookup"><span data-stu-id="635c5-244">is equivalent to</span></span>
```csharp
if (x) {
    if (y) {
        F();
    }
    else {
        G();
    }
}
```

<span data-ttu-id="635c5-245">Un' `if` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="635c5-245">An `if` statement is executed as follows:</span></span>

*  <span data-ttu-id="635c5-246">Viene valutato *Boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)).</span><span class="sxs-lookup"><span data-stu-id="635c5-246">The *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)) is evaluated.</span></span>
*  <span data-ttu-id="635c5-247">Se l'espressione booleana restituisce `true`, il controllo viene trasferito alla prima istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="635c5-247">If the boolean expression yields `true`, control is transferred to the first embedded statement.</span></span> <span data-ttu-id="635c5-248">Quando e se il controllo raggiunge il punto finale di tale istruzione, il controllo viene trasferito al punto finale dell' `if` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-248">When and if control reaches the end point of that statement, control is transferred to the end point of the `if` statement.</span></span>
*  <span data-ttu-id="635c5-249">Se l'espressione booleana restituisce `false` e se è `else` presente una parte, il controllo viene trasferito alla seconda istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="635c5-249">If the boolean expression yields `false` and if an `else` part is present, control is transferred to the second embedded statement.</span></span> <span data-ttu-id="635c5-250">Quando e se il controllo raggiunge il punto finale di tale istruzione, il controllo viene trasferito al punto finale dell' `if` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-250">When and if control reaches the end point of that statement, control is transferred to the end point of the `if` statement.</span></span>
*  <span data-ttu-id="635c5-251">Se l'espressione booleana restituisce `false` e se una `else` parte non è presente, il controllo viene trasferito al punto finale dell' `if` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-251">If the boolean expression yields `false` and if an `else` part is not present, control is transferred to the end point of the `if` statement.</span></span>

<span data-ttu-id="635c5-252">La prima istruzione incorporata di `if` un'istruzione è raggiungibile se l' `if` istruzione è raggiungibile e l'espressione booleana non ha il valore `false`costante.</span><span class="sxs-lookup"><span data-stu-id="635c5-252">The first embedded statement of an `if` statement is reachable if the `if` statement is reachable and the boolean expression does not have the constant value `false`.</span></span>

<span data-ttu-id="635c5-253">La seconda istruzione incorporata di `if` un'istruzione, se presente, è raggiungibile se l' `if` istruzione è raggiungibile e l'espressione booleana non ha il valore `true`costante.</span><span class="sxs-lookup"><span data-stu-id="635c5-253">The second embedded statement of an `if` statement, if present, is reachable if the `if` statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

<span data-ttu-id="635c5-254">Il punto finale di un' `if` istruzione è raggiungibile se il punto finale di almeno una delle relative istruzioni incorporate è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-254">The end point of an `if` statement is reachable if the end point of at least one of its embedded statements is reachable.</span></span> <span data-ttu-id="635c5-255">Inoltre, il punto `if` finale di un'istruzione senza `else` parte è raggiungibile se l' `if` istruzione è raggiungibile e l'espressione booleana non ha il valore `true`costante.</span><span class="sxs-lookup"><span data-stu-id="635c5-255">In addition, the end point of an `if` statement with no `else` part is reachable if the `if` statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

### <a name="the-switch-statement"></a><span data-ttu-id="635c5-256">Istruzione switch</span><span class="sxs-lookup"><span data-stu-id="635c5-256">The switch statement</span></span>

<span data-ttu-id="635c5-257">L'istruzione switch seleziona per l'esecuzione di un elenco di istruzioni con un'etichetta switch associata che corrisponde al valore dell'espressione switch.</span><span class="sxs-lookup"><span data-stu-id="635c5-257">The switch statement selects for execution a statement list having an associated switch label that corresponds to the value of the switch expression.</span></span>

```antlr
switch_statement
    : 'switch' '(' expression ')' switch_block
    ;

switch_block
    : '{' switch_section* '}'
    ;

switch_section
    : switch_label+ statement_list
    ;

switch_label
    : 'case' constant_expression ':'
    | 'default' ':'
    ;
```

<span data-ttu-id="635c5-258">Un *switch_Statement* è costituito dalla `switch`parola chiave, seguita da un'espressione racchiusa tra parentesi (denominata espressione switch), seguita da un *switch_block*.</span><span class="sxs-lookup"><span data-stu-id="635c5-258">A *switch_statement* consists of the keyword `switch`, followed by a parenthesized expression (called the switch expression), followed by a *switch_block*.</span></span> <span data-ttu-id="635c5-259">*Switch_block* è costituito da zero o più *switch_section*, racchiusi tra parentesi graffe.</span><span class="sxs-lookup"><span data-stu-id="635c5-259">The *switch_block* consists of zero or more *switch_section*s, enclosed in braces.</span></span> <span data-ttu-id="635c5-260">Ogni *switch_section* è costituito da uno o più *switch_label*, seguiti da un *statement_list* ([elenchi di istruzioni](statements.md#statement-lists)).</span><span class="sxs-lookup"><span data-stu-id="635c5-260">Each *switch_section* consists of one or more *switch_label*s followed by a *statement_list* ([Statement lists](statements.md#statement-lists)).</span></span>

<span data-ttu-id="635c5-261">Il ***tipo di regola*** di un' `switch` istruzione viene stabilito dall'espressione switch.</span><span class="sxs-lookup"><span data-stu-id="635c5-261">The ***governing type*** of a `switch` statement is established by the switch expression.</span></span>

*  <span data-ttu-id="635c5-262">Se `sbyte`il tipo dell'espressione switch è `uint` `int` `ushort` `short`, `byte`,,,,, `long`, `ulong`, ,`bool` ,`string`o `char`  *enum_type*o se si tratta del tipo nullable corrispondente a uno di questi tipi, si tratta del tipo di governance dell' `switch` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-262">If the type of the switch expression is `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `bool`, `char`, `string`, or an *enum_type*, or if it is the nullable type corresponding to one of these types, then that is the governing type of the `switch` statement.</span></span>
*  <span data-ttu-id="635c5-263">In caso contrario, è necessario che esista esattamente una conversione implicita definita dall'utente ([conversioni definite dall'utente](conversions.md#user-defined-conversions)) dal tipo dell'espressione switch a uno dei seguenti tipi di regola: `sbyte`, `byte`, `short`, `ushort` , `int`, `uint`, ,`long` ,`char`,o, un tipo nullable corrispondente a uno di questi tipi. `ulong` `string`</span><span class="sxs-lookup"><span data-stu-id="635c5-263">Otherwise, exactly one user-defined implicit conversion ([User-defined conversions](conversions.md#user-defined-conversions)) must exist from the type of the switch expression to one of the following possible governing types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `string`, or,  a nullable type corresponding to one of those types.</span></span>
*  <span data-ttu-id="635c5-264">In caso contrario, se non esiste alcuna conversione implicita o se è presente più di una conversione implicita, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-264">Otherwise, if no such implicit conversion exists, or if more than one such implicit conversion exists, a compile-time error occurs.</span></span>

<span data-ttu-id="635c5-265">L'espressione costante di ogni `case` etichetta deve indicare un valore convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) nel tipo di regola dell' `switch` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-265">The constant expression of each `case` label must denote a value that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the governing type of the `switch` statement.</span></span> <span data-ttu-id="635c5-266">Si verifica un errore in fase di compilazione se due `case` o più etichette nella `switch` stessa istruzione specificano lo stesso valore costante.</span><span class="sxs-lookup"><span data-stu-id="635c5-266">A compile-time error occurs if two or more `case` labels in the same `switch` statement specify the same constant value.</span></span>

<span data-ttu-id="635c5-267">In un'istruzione switch può essere `default` presente al massimo un'etichetta.</span><span class="sxs-lookup"><span data-stu-id="635c5-267">There can be at most one `default` label in a switch statement.</span></span>

<span data-ttu-id="635c5-268">Un' `switch` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="635c5-268">A `switch` statement is executed as follows:</span></span>

*  <span data-ttu-id="635c5-269">L'espressione switch viene valutata e convertita nel tipo di regola.</span><span class="sxs-lookup"><span data-stu-id="635c5-269">The switch expression is evaluated and converted to the governing type.</span></span>
*  <span data-ttu-id="635c5-270">Se una delle costanti specificate in un' `case` etichetta nella stessa `switch` istruzione è uguale al valore dell'espressione switch, il controllo viene trasferito all' `case` elenco di istruzioni che segue l'etichetta corrispondente.</span><span class="sxs-lookup"><span data-stu-id="635c5-270">If one of the constants specified in a `case` label in the same `switch` statement is equal to the value of the switch expression, control is transferred to the statement list following the matched `case` label.</span></span>
*  <span data-ttu-id="635c5-271">Se nessuna `case` delle costanti specificate nelle etichette nella stessa `switch` istruzione è uguale al valore dell'espressione switch e se è presente un' `default` etichetta, il controllo `default` viene trasferito all'elenco di istruzioni che segue etichetta.</span><span class="sxs-lookup"><span data-stu-id="635c5-271">If none of the constants specified in `case` labels in the same `switch` statement is equal to the value of the switch expression, and if a `default` label is present, control is transferred to the statement list following the `default` label.</span></span>
*  <span data-ttu-id="635c5-272">Se nessuna `case` delle costanti specificate nelle etichette nella stessa `switch` istruzione è uguale al valore dell'espressione switch e se non è presente alcuna `default` etichetta, il controllo `switch` viene trasferito al punto finale dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-272">If none of the constants specified in `case` labels in the same `switch` statement is equal to the value of the switch expression, and if no `default` label is present, control is transferred to the end point of the `switch` statement.</span></span>

<span data-ttu-id="635c5-273">Se il punto finale dell'elenco di istruzioni di una sezione switch è raggiungibile, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-273">If the end point of the statement list of a switch section is reachable, a compile-time error occurs.</span></span> <span data-ttu-id="635c5-274">Questa operazione è nota come regola "No fall through".</span><span class="sxs-lookup"><span data-stu-id="635c5-274">This is known as the "no fall through" rule.</span></span> <span data-ttu-id="635c5-275">Esempio</span><span class="sxs-lookup"><span data-stu-id="635c5-275">The example</span></span>
```csharp
switch (i) {
case 0:
    CaseZero();
    break;
case 1:
    CaseOne();
    break;
default:
    CaseOthers();
    break;
}
```
<span data-ttu-id="635c5-276">è valido perché nessuna sezione switch ha un endpoint raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-276">is valid because no switch section has a reachable end point.</span></span> <span data-ttu-id="635c5-277">Diversamente da C e C++, l'esecuzione di una sezione switch non è consentita per passare alla sezione switch successiva e l'esempio</span><span class="sxs-lookup"><span data-stu-id="635c5-277">Unlike C and C++, execution of a switch section is not permitted to "fall through" to the next switch section, and the example</span></span>
```csharp
switch (i) {
case 0:
    CaseZero();
case 1:
    CaseZeroOrOne();
default:
    CaseAny();
}
```
<span data-ttu-id="635c5-278">genera un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-278">results in a compile-time error.</span></span> <span data-ttu-id="635c5-279">Quando l'esecuzione di una sezione switch deve essere seguita dall'esecuzione di un'altra sezione switch, è `goto case` necessario `goto default` usare un'istruzione o esplicita:</span><span class="sxs-lookup"><span data-stu-id="635c5-279">When execution of a switch section is to be followed by execution of another switch section, an explicit `goto case` or `goto default` statement must be used:</span></span>
```csharp
switch (i) {
case 0:
    CaseZero();
    goto case 1;
case 1:
    CaseZeroOrOne();
    goto default;
default:
    CaseAny();
    break;
}
```

<span data-ttu-id="635c5-280">In un *switch_section*sono consentite più etichette.</span><span class="sxs-lookup"><span data-stu-id="635c5-280">Multiple labels are permitted in a *switch_section*.</span></span> <span data-ttu-id="635c5-281">Esempio</span><span class="sxs-lookup"><span data-stu-id="635c5-281">The example</span></span>
```csharp
switch (i) {
case 0:
    CaseZero();
    break;
case 1:
    CaseOne();
    break;
case 2:
default:
    CaseTwo();
    break;
}
```
<span data-ttu-id="635c5-282">è valido.</span><span class="sxs-lookup"><span data-stu-id="635c5-282">is valid.</span></span> <span data-ttu-id="635c5-283">L'esempio non viola la regola "No fall through" perché le etichette `case 2:` e `default:` fanno parte dello stesso *switch_section*.</span><span class="sxs-lookup"><span data-stu-id="635c5-283">The example does not violate the "no fall through" rule because the labels `case 2:` and `default:` are part of the same *switch_section*.</span></span>

<span data-ttu-id="635c5-284">La regola "No fall through" impedisce una classe comune di bug che si verificano in C++ C `break` e in caso di omissione accidentale delle istruzioni.</span><span class="sxs-lookup"><span data-stu-id="635c5-284">The "no fall through" rule prevents a common class of bugs that occur in C and C++ when `break` statements are accidentally omitted.</span></span> <span data-ttu-id="635c5-285">Inoltre, a causa di questa regola, le sezioni switch di un' `switch` istruzione possono essere riorganizzate arbitrariamente senza influire sul comportamento dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-285">In addition, because of this rule, the switch sections of a `switch` statement can be arbitrarily rearranged without affecting the behavior of the statement.</span></span> <span data-ttu-id="635c5-286">Le sezioni dell' `switch` istruzione precedente, ad esempio, possono essere invertite senza influire sul comportamento dell'istruzione:</span><span class="sxs-lookup"><span data-stu-id="635c5-286">For example, the sections of the `switch` statement above can be reversed without affecting the behavior of the statement:</span></span>
```csharp
switch (i) {
default:
    CaseAny();
    break;
case 1:
    CaseZeroOrOne();
    goto default;
case 0:
    CaseZero();
    goto case 1;
}
```

<span data-ttu-id="635c5-287">L'elenco di istruzioni di una sezione switch termina in genere `break`in `goto case`un'istruzione `goto default` , o, ma qualsiasi costrutto che esegue il rendering del punto finale dell'elenco di istruzioni non raggiungibile è consentito.</span><span class="sxs-lookup"><span data-stu-id="635c5-287">The statement list of a switch section typically ends in a `break`, `goto case`, or `goto default` statement, but any construct that renders the end point of the statement list unreachable is permitted.</span></span> <span data-ttu-id="635c5-288">Ad esempio, un' `while` istruzione controllata dall'espressione `true` booleana è nota che non raggiunge mai il punto finale.</span><span class="sxs-lookup"><span data-stu-id="635c5-288">For example, a `while` statement controlled by the boolean expression `true` is known to never reach its end point.</span></span> <span data-ttu-id="635c5-289">Analogamente, `throw` un' `return` istruzione o trasferisce sempre il controllo altrove e non raggiunge mai il punto finale.</span><span class="sxs-lookup"><span data-stu-id="635c5-289">Likewise, a `throw` or `return` statement always transfers control elsewhere and never reaches its end point.</span></span> <span data-ttu-id="635c5-290">Di conseguenza, l'esempio seguente è valido:</span><span class="sxs-lookup"><span data-stu-id="635c5-290">Thus, the following example is valid:</span></span>
```csharp
switch (i) {
case 0:
    while (true) F();
case 1:
    throw new ArgumentException();
case 2:
    return;
}
```

<span data-ttu-id="635c5-291">Il tipo di regola di un' `switch` istruzione può essere il tipo `string`.</span><span class="sxs-lookup"><span data-stu-id="635c5-291">The governing type of a `switch` statement may be the type `string`.</span></span> <span data-ttu-id="635c5-292">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="635c5-292">For example:</span></span>
```csharp
void DoCommand(string command) {
    switch (command.ToLower()) {
    case "run":
        DoRun();
        break;
    case "save":
        DoSave();
        break;
    case "quit":
        DoQuit();
        break;
    default:
        InvalidCommand(command);
        break;
    }
}
```

<span data-ttu-id="635c5-293">Analogamente agli operatori di uguaglianza di stringa (operatori di uguaglianza `switch` di[stringa](expressions.md#string-equality-operators)), l'istruzione fa distinzione tra maiuscole e minuscole e consente di eseguire una determinata `case` sezione switch solo se la stringa dell'espressione switch corrisponde esattamente a una costante Label.</span><span class="sxs-lookup"><span data-stu-id="635c5-293">Like the string equality operators ([String equality operators](expressions.md#string-equality-operators)), the `switch` statement is case sensitive and will execute a given switch section only if the switch expression string exactly matches a `case` label constant.</span></span>

<span data-ttu-id="635c5-294">Quando il tipo di regola di un' `switch` istruzione è `string`, il valore `null` è consentito come costante di etichetta case.</span><span class="sxs-lookup"><span data-stu-id="635c5-294">When the governing type of a `switch` statement is `string`, the value `null` is permitted as a case label constant.</span></span>

<span data-ttu-id="635c5-295">Il *statement_list*s di un *switch_block* può contenere istruzioni di dichiarazione ([istruzioni di dichiarazione](statements.md#declaration-statements)).</span><span class="sxs-lookup"><span data-stu-id="635c5-295">The *statement_list*s of a *switch_block* may contain declaration statements ([Declaration statements](statements.md#declaration-statements)).</span></span> <span data-ttu-id="635c5-296">L'ambito di una variabile locale o di una costante dichiarata in un blocco switch è il blocco switch.</span><span class="sxs-lookup"><span data-stu-id="635c5-296">The scope of a local variable or constant declared in a switch block is the switch block.</span></span>

<span data-ttu-id="635c5-297">L'elenco di istruzioni di una sezione switch specificata è raggiungibile se l' `switch` istruzione è raggiungibile e viene soddisfatta almeno una delle condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="635c5-297">The statement list of a given switch section is reachable if the `switch` statement is reachable and at least one of the following is true:</span></span>

*  <span data-ttu-id="635c5-298">L'espressione switch è un valore non costante.</span><span class="sxs-lookup"><span data-stu-id="635c5-298">The switch expression is a non-constant value.</span></span>
*  <span data-ttu-id="635c5-299">L'espressione switch è un valore costante che corrisponde a `case` un'etichetta nella sezione switch.</span><span class="sxs-lookup"><span data-stu-id="635c5-299">The switch expression is a constant value that matches a `case` label in the switch section.</span></span>
*  <span data-ttu-id="635c5-300">L'espressione switch è un valore costante che non corrisponde ad `case` alcuna etichetta e la sezione switch contiene l' `default` etichetta.</span><span class="sxs-lookup"><span data-stu-id="635c5-300">The switch expression is a constant value that doesn't match any `case` label, and the switch section contains the `default` label.</span></span>
*  <span data-ttu-id="635c5-301">Un'etichetta switch della sezione switch è a cui fa riferimento un'istruzione `goto case` o `goto default` raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-301">A switch label of the switch section is referenced by a reachable `goto case` or `goto default` statement.</span></span>

<span data-ttu-id="635c5-302">Il punto finale di un' `switch` istruzione è raggiungibile se si verifica almeno una delle condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="635c5-302">The end point of a `switch` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="635c5-303">L' `switch` istruzione contiene un'istruzione `break` raggiungibile che esce dall' `switch` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-303">The `switch` statement contains a reachable `break` statement that exits the `switch` statement.</span></span>
*  <span data-ttu-id="635c5-304">L' `switch` istruzione è raggiungibile, l'espressione switch è un valore non costante e non è presente alcuna `default` etichetta.</span><span class="sxs-lookup"><span data-stu-id="635c5-304">The `switch` statement is reachable, the switch expression is a non-constant value, and no `default` label is present.</span></span>
*  <span data-ttu-id="635c5-305">L' `switch` istruzione è raggiungibile, l'espressione switch è un valore costante che non corrisponde ad alcuna `case` etichetta e non è `default` presente alcuna etichetta.</span><span class="sxs-lookup"><span data-stu-id="635c5-305">The `switch` statement is reachable, the switch expression is a constant value that doesn't match any `case` label, and no `default` label is present.</span></span>

## <a name="iteration-statements"></a><span data-ttu-id="635c5-306">Istruzioni di iterazione</span><span class="sxs-lookup"><span data-stu-id="635c5-306">Iteration statements</span></span>

<span data-ttu-id="635c5-307">Le istruzioni di iterazione eseguono ripetutamente un'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="635c5-307">Iteration statements repeatedly execute an embedded statement.</span></span>

```antlr
iteration_statement
    : while_statement
    | do_statement
    | for_statement
    | foreach_statement
    ;
```

### <a name="the-while-statement"></a><span data-ttu-id="635c5-308">Istruzione while</span><span class="sxs-lookup"><span data-stu-id="635c5-308">The while statement</span></span>

<span data-ttu-id="635c5-309">L' `while` istruzione esegue in modo condizionale un'istruzione incorporata zero o più volte.</span><span class="sxs-lookup"><span data-stu-id="635c5-309">The `while` statement conditionally executes an embedded statement zero or more times.</span></span>

```antlr
while_statement
    : 'while' '(' boolean_expression ')' embedded_statement
    ;
```

<span data-ttu-id="635c5-310">Un' `while` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="635c5-310">A `while` statement is executed as follows:</span></span>

*  <span data-ttu-id="635c5-311">Viene valutato *Boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)).</span><span class="sxs-lookup"><span data-stu-id="635c5-311">The *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)) is evaluated.</span></span>
*  <span data-ttu-id="635c5-312">Se l'espressione booleana restituisce `true`, il controllo viene trasferito all'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="635c5-312">If the boolean expression yields `true`, control is transferred to the embedded statement.</span></span> <span data-ttu-id="635c5-313">Quando e se il controllo raggiunge il punto finale dell'istruzione incorporata (possibilmente dall'esecuzione `continue` di un'istruzione), il controllo viene trasferito all'inizio `while` dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-313">When and if control reaches the end point of the embedded statement (possibly from execution of a `continue` statement), control is transferred to the beginning of the `while` statement.</span></span>
*  <span data-ttu-id="635c5-314">Se l'espressione booleana restituisce `false`, il controllo viene trasferito al punto finale `while` dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-314">If the boolean expression yields `false`, control is transferred to the end point of the `while` statement.</span></span>

<span data-ttu-id="635c5-315">All'interno dell'istruzione incorporata `while` di un'istruzione `break` , un'istruzione ([istruzione break](statements.md#the-break-statement)) può essere utilizzata per trasferire il `while` controllo al punto finale dell'istruzione (in modo che termina l'iterazione dell'istruzione incorporata) e `continue` è possibile utilizzare l'istruzione ([istruzione continue](statements.md#the-continue-statement)) per trasferire il controllo al punto finale dell'istruzione incorporata, eseguendo così un'altra `while` iterazione dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-315">Within the embedded statement of a `while` statement, a `break` statement ([The break statement](statements.md#the-break-statement)) may be used to transfer control to the end point of the `while` statement (thus ending iteration of the embedded statement), and a `continue` statement ([The continue statement](statements.md#the-continue-statement)) may be used to transfer control to the end point of the embedded statement (thus performing another iteration of the `while` statement).</span></span>

<span data-ttu-id="635c5-316">L'istruzione incorporata di `while` un'istruzione è raggiungibile se l' `while` istruzione è raggiungibile e l'espressione booleana non ha il valore `false`costante.</span><span class="sxs-lookup"><span data-stu-id="635c5-316">The embedded statement of a `while` statement is reachable if the `while` statement is reachable and the boolean expression does not have the constant value `false`.</span></span>

<span data-ttu-id="635c5-317">Il punto finale di un' `while` istruzione è raggiungibile se si verifica almeno una delle condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="635c5-317">The end point of a `while` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="635c5-318">L' `while` istruzione contiene un'istruzione `break` raggiungibile che esce dall' `while` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-318">The `while` statement contains a reachable `break` statement that exits the `while` statement.</span></span>
*  <span data-ttu-id="635c5-319">L' `while` istruzione è raggiungibile e l'espressione booleana non ha il valore `true`costante.</span><span class="sxs-lookup"><span data-stu-id="635c5-319">The `while` statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

### <a name="the-do-statement"></a><span data-ttu-id="635c5-320">Istruzione do</span><span class="sxs-lookup"><span data-stu-id="635c5-320">The do statement</span></span>

<span data-ttu-id="635c5-321">L' `do` istruzione esegue in modo condizionale un'istruzione incorporata una o più volte.</span><span class="sxs-lookup"><span data-stu-id="635c5-321">The `do` statement conditionally executes an embedded statement one or more times.</span></span>

```antlr
do_statement
    : 'do' embedded_statement 'while' '(' boolean_expression ')' ';'
    ;
```

<span data-ttu-id="635c5-322">Un' `do` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="635c5-322">A `do` statement is executed as follows:</span></span>

*  <span data-ttu-id="635c5-323">Il controllo viene trasferito all'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="635c5-323">Control is transferred to the embedded statement.</span></span>
*  <span data-ttu-id="635c5-324">Quando e se il controllo raggiunge il punto finale dell'istruzione incorporata (possibilmente dall'esecuzione `continue` di un'istruzione), viene valutato *Boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)).</span><span class="sxs-lookup"><span data-stu-id="635c5-324">When and if control reaches the end point of the embedded statement (possibly from execution of a `continue` statement), the *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)) is evaluated.</span></span> <span data-ttu-id="635c5-325">Se l'espressione booleana restituisce `true`, il controllo viene trasferito all'inizio `do` dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-325">If the boolean expression yields `true`, control is transferred to the beginning of the `do` statement.</span></span> <span data-ttu-id="635c5-326">In caso contrario, il controllo viene trasferito al punto finale `do` dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-326">Otherwise, control is transferred to the end point of the `do` statement.</span></span>

<span data-ttu-id="635c5-327">All'interno dell'istruzione incorporata `do` di un'istruzione `break` , un'istruzione ([istruzione break](statements.md#the-break-statement)) può essere utilizzata per trasferire il `do` controllo al punto finale dell'istruzione (in modo che termina l'iterazione dell'istruzione incorporata) e `continue` l'istruzione ([istruzione continue](statements.md#the-continue-statement)) può essere utilizzata per trasferire il controllo al punto finale dell'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="635c5-327">Within the embedded statement of a `do` statement, a `break` statement ([The break statement](statements.md#the-break-statement)) may be used to transfer control to the end point of the `do` statement (thus ending iteration of the embedded statement), and a `continue` statement ([The continue statement](statements.md#the-continue-statement)) may be used to transfer control to the end point of the embedded statement.</span></span>

<span data-ttu-id="635c5-328">L'istruzione incorporata di `do` un'istruzione è raggiungibile se l' `do` istruzione è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-328">The embedded statement of a `do` statement is reachable if the `do` statement is reachable.</span></span>

<span data-ttu-id="635c5-329">Il punto finale di un' `do` istruzione è raggiungibile se si verifica almeno una delle condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="635c5-329">The end point of a `do` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="635c5-330">L' `do` istruzione contiene un'istruzione `break` raggiungibile che esce dall' `do` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-330">The `do` statement contains a reachable `break` statement that exits the `do` statement.</span></span>
*  <span data-ttu-id="635c5-331">Il punto finale dell'istruzione incorporata è raggiungibile e l'espressione booleana non ha il valore `true`costante.</span><span class="sxs-lookup"><span data-stu-id="635c5-331">The end point of the embedded statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

### <a name="the-for-statement"></a><span data-ttu-id="635c5-332">Istruzione for</span><span class="sxs-lookup"><span data-stu-id="635c5-332">The for statement</span></span>

<span data-ttu-id="635c5-333">L' `for` istruzione valuta una sequenza di espressioni di inizializzazione e quindi, mentre una condizione è true, esegue ripetutamente un'istruzione incorporata e valuta una sequenza di espressioni di iterazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-333">The `for` statement evaluates a sequence of initialization expressions and then, while a condition is true, repeatedly executes an embedded statement and evaluates a sequence of iteration expressions.</span></span>

```antlr
for_statement
    : 'for' '(' for_initializer? ';' for_condition? ';' for_iterator? ')' embedded_statement
    ;

for_initializer
    : local_variable_declaration
    | statement_expression_list
    ;

for_condition
    : boolean_expression
    ;

for_iterator
    : statement_expression_list
    ;

statement_expression_list
    : statement_expression (',' statement_expression)*
    ;
```

<span data-ttu-id="635c5-334">Il valore di *for_initializer*, se presente, è costituito da un *local_variable_declaration* ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)) o da un elenco di *statement_expression*s ([istruzioni di espressione](statements.md#expression-statements)) separate da virgole.</span><span class="sxs-lookup"><span data-stu-id="635c5-334">The *for_initializer*, if present, consists of either a *local_variable_declaration* ([Local variable declarations](statements.md#local-variable-declarations)) or a list of *statement_expression*s ([Expression statements](statements.md#expression-statements)) separated by commas.</span></span> <span data-ttu-id="635c5-335">L'ambito di una variabile locale dichiarata da un *for_initializer* inizia da *local_variable_declarator* per la variabile e si estende fino alla fine dell'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="635c5-335">The scope of a local variable declared by a *for_initializer* starts at the *local_variable_declarator* for the variable and extends to the end of the embedded statement.</span></span> <span data-ttu-id="635c5-336">L'ambito include *for_condition* e *for_iterator*.</span><span class="sxs-lookup"><span data-stu-id="635c5-336">The scope includes the *for_condition* and the *for_iterator*.</span></span>

<span data-ttu-id="635c5-337">*For_condition*, se presente, deve essere un *Boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)).</span><span class="sxs-lookup"><span data-stu-id="635c5-337">The *for_condition*, if present, must be a *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)).</span></span>

<span data-ttu-id="635c5-338">Il valore di *for_iterator*, se presente, è costituito da un elenco di *statement_expression*([istruzioni di espressione](statements.md#expression-statements)) separate da virgole.</span><span class="sxs-lookup"><span data-stu-id="635c5-338">The *for_iterator*, if present, consists of a list of *statement_expression*s ([Expression statements](statements.md#expression-statements)) separated by commas.</span></span>

<span data-ttu-id="635c5-339">Un'istruzione for viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="635c5-339">A for statement is executed as follows:</span></span>

*  <span data-ttu-id="635c5-340">Se è presente un *for_initializer* , gli inizializzatori di variabile o le espressioni di istruzione vengono eseguiti nell'ordine in cui vengono scritti.</span><span class="sxs-lookup"><span data-stu-id="635c5-340">If a *for_initializer* is present, the variable initializers or statement expressions are executed in the order they are written.</span></span> <span data-ttu-id="635c5-341">Questo passaggio viene eseguito una sola volta.</span><span class="sxs-lookup"><span data-stu-id="635c5-341">This step is only performed once.</span></span>
*  <span data-ttu-id="635c5-342">Se è presente un *for_condition* , viene valutato.</span><span class="sxs-lookup"><span data-stu-id="635c5-342">If a *for_condition* is present, it is evaluated.</span></span>
*  <span data-ttu-id="635c5-343">Se *for_condition* non è presente o se la valutazione restituisce `true`, il controllo viene trasferito all'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="635c5-343">If the *for_condition* is not present or if the evaluation yields `true`, control is transferred to the embedded statement.</span></span> <span data-ttu-id="635c5-344">Quando e se il controllo raggiunge il punto finale dell'istruzione incorporata (possibilmente dall'esecuzione `continue` di un'istruzione), le espressioni di *for_iterator*, se presenti, vengono valutate in sequenza e quindi viene eseguita un'altra iterazione, a partire da valutazione del *for_condition* nel passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="635c5-344">When and if control reaches the end point of the embedded statement (possibly from execution of a `continue` statement), the expressions of the *for_iterator*, if any, are evaluated in sequence, and then another iteration is performed, starting with evaluation of the *for_condition* in the step above.</span></span>
*  <span data-ttu-id="635c5-345">Se *for_condition* è presente e la valutazione restituisce `false`, il controllo viene trasferito al punto `for` finale dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-345">If the *for_condition* is present and the evaluation yields `false`, control is transferred to the end point of the `for` statement.</span></span>

<span data-ttu-id="635c5-346">All'interno dell'istruzione incorporata `for` di un'istruzione `break` , un'istruzione ([istruzione break](statements.md#the-break-statement)) può essere utilizzata per trasferire il `for` controllo al punto finale dell'istruzione (in modo che termina l'iterazione dell'istruzione incorporata) e `continue` l'istruzione ([istruzione continue](statements.md#the-continue-statement)) può essere utilizzata per trasferire il controllo al punto finale dell'istruzione incorporata, eseguendo in questo modo *for_iterator* ed `for` eseguendo un'altra iterazione dell'istruzione, a partire da *for_condition*).</span><span class="sxs-lookup"><span data-stu-id="635c5-346">Within the embedded statement of a `for` statement, a `break` statement ([The break statement](statements.md#the-break-statement)) may be used to transfer control to the end point of the `for` statement (thus ending iteration of the embedded statement), and a `continue` statement ([The continue statement](statements.md#the-continue-statement)) may be used to transfer control to the end point of the embedded statement (thus executing the *for_iterator* and performing another iteration of the `for` statement, starting with the *for_condition*).</span></span>

<span data-ttu-id="635c5-347">L'istruzione incorporata di `for` un'istruzione è raggiungibile se si verifica una delle condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="635c5-347">The embedded statement of a `for` statement is reachable if one of the following is true:</span></span>

*  <span data-ttu-id="635c5-348">L' `for` istruzione è raggiungibile e non è presente alcun *for_condition* .</span><span class="sxs-lookup"><span data-stu-id="635c5-348">The `for` statement is reachable and no *for_condition* is present.</span></span>
*  <span data-ttu-id="635c5-349">L' `for` istruzione è raggiungibile e un *for_condition* è presente e non ha il valore `false`costante.</span><span class="sxs-lookup"><span data-stu-id="635c5-349">The `for` statement is reachable and a *for_condition* is present and does not have the constant value `false`.</span></span>

<span data-ttu-id="635c5-350">Il punto finale di un' `for` istruzione è raggiungibile se si verifica almeno una delle condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="635c5-350">The end point of a `for` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="635c5-351">L' `for` istruzione contiene un'istruzione `break` raggiungibile che esce dall' `for` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-351">The `for` statement contains a reachable `break` statement that exits the `for` statement.</span></span>
*  <span data-ttu-id="635c5-352">L' `for` istruzione è raggiungibile e un *for_condition* è presente e non ha il valore `true`costante.</span><span class="sxs-lookup"><span data-stu-id="635c5-352">The `for` statement is reachable and a *for_condition* is present and does not have the constant value `true`.</span></span>

### <a name="the-foreach-statement"></a><span data-ttu-id="635c5-353">Istruzione foreach</span><span class="sxs-lookup"><span data-stu-id="635c5-353">The foreach statement</span></span>

<span data-ttu-id="635c5-354">L' `foreach` istruzione enumera gli elementi di una raccolta, eseguendo un'istruzione incorporata per ogni elemento della raccolta.</span><span class="sxs-lookup"><span data-stu-id="635c5-354">The `foreach` statement enumerates the elements of a collection, executing an embedded statement for each element of the collection.</span></span>

```antlr
foreach_statement
    : 'foreach' '(' local_variable_type identifier 'in' expression ')' embedded_statement
    ;
```

<span data-ttu-id="635c5-355">Il *tipo* e l' *identificatore* di `foreach` un'istruzione dichiarano la ***variabile di iterazione*** dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-355">The *type* and *identifier* of a `foreach` statement declare the ***iteration variable*** of the statement.</span></span> <span data-ttu-id="635c5-356">Se l' `var` identificatore viene specificato come *local_variable_type*e non è presente alcun tipo `var` denominato nell'ambito, la variabile di iterazione è detta ***variabile di iterazione tipizzata in modo implicito***e il tipo viene considerato come il tipo di elemento del `foreach` , come specificato di seguito.</span><span class="sxs-lookup"><span data-stu-id="635c5-356">If the `var` identifier is given as the *local_variable_type*, and no type named `var` is in scope, the iteration variable is said to be an ***implicitly typed iteration variable***, and its type is taken to be the element type of the `foreach` statement, as specified below.</span></span> <span data-ttu-id="635c5-357">La variabile di iterazione corrisponde a una variabile locale di sola lettura con un ambito che si estende sull'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="635c5-357">The iteration variable corresponds to a read-only local variable with a scope that extends over the embedded statement.</span></span> <span data-ttu-id="635c5-358">Durante l'esecuzione di `foreach` un'istruzione, la variabile di iterazione rappresenta l'elemento della raccolta per il quale è attualmente in corso l'esecuzione di un'iterazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-358">During execution of a `foreach` statement, the iteration variable represents the collection element for which an iteration is currently being performed.</span></span> <span data-ttu-id="635c5-359">Si verifica un errore in fase di compilazione se l'istruzione incorporata tenta di modificare la variabile di iterazione `++` ( `--` tramite assegnazione o gli operatori e) o di `ref` passare `out` la variabile di iterazione come parametro o.</span><span class="sxs-lookup"><span data-stu-id="635c5-359">A compile-time error occurs if the embedded statement attempts to modify the iteration variable (via assignment or the `++` and `--` operators) or pass the iteration variable as a `ref` or `out` parameter.</span></span>

<span data-ttu-id="635c5-360">Nell'esempio seguente, per brevità `IEnumerable`, `IEnumerator` `IEnumerable<T>` , e `IEnumerator<T>` si riferiscono ai tipi corrispondenti negli spazi `System.Collections` dei `System.Collections.Generic`nomi e.</span><span class="sxs-lookup"><span data-stu-id="635c5-360">In the following, for brevity, `IEnumerable`, `IEnumerator`, `IEnumerable<T>` and `IEnumerator<T>` refer to the corresponding types in the namespaces `System.Collections` and `System.Collections.Generic`.</span></span>

<span data-ttu-id="635c5-361">L'elaborazione in fase di compilazione di un'istruzione foreach determina innanzitutto il tipo di ***raccolta***, il tipo di ***enumeratore*** e il ***tipo di elemento*** dell'espressione.</span><span class="sxs-lookup"><span data-stu-id="635c5-361">The compile-time processing of a foreach statement first determines the ***collection type***, ***enumerator type*** and ***element type*** of the expression.</span></span> <span data-ttu-id="635c5-362">Questa determinazione procede come segue:</span><span class="sxs-lookup"><span data-stu-id="635c5-362">This determination proceeds as follows:</span></span>

*  <span data-ttu-id="635c5-363">Se il tipo `X` di *espressione* è un tipo di matrice, esiste una `X` `IEnumerable` conversione implicita del riferimento da all'interfaccia ( `System.Array` poiché implementa questa interfaccia).</span><span class="sxs-lookup"><span data-stu-id="635c5-363">If the type `X` of *expression* is an array type then there is an implicit reference conversion from `X` to the `IEnumerable` interface (since `System.Array` implements this interface).</span></span> <span data-ttu-id="635c5-364">Il ***tipo di raccolta*** è `IEnumerable` l'interfaccia, ***il tipo di enumeratore*** è `IEnumerator` l'interfaccia e il tipo di ***elemento*** è il tipo di `X`elemento del tipo di matrice.</span><span class="sxs-lookup"><span data-stu-id="635c5-364">The ***collection type*** is the `IEnumerable` interface, the ***enumerator type*** is the `IEnumerator` interface and the ***element type*** is the element type of the array type `X`.</span></span>
*  <span data-ttu-id="635c5-365">Se il tipo `X` di *espressione* è `dynamic` , esiste `IEnumerable` una conversione implicita dall' *espressione* all'interfaccia ([conversioni dinamiche implicite](conversions.md#implicit-dynamic-conversions)).</span><span class="sxs-lookup"><span data-stu-id="635c5-365">If the type `X` of *expression* is `dynamic` then there is an implicit conversion from *expression* to the `IEnumerable` interface ([Implicit dynamic conversions](conversions.md#implicit-dynamic-conversions)).</span></span> <span data-ttu-id="635c5-366">Il ***tipo di raccolta*** è `IEnumerable` l'interfaccia e il ***tipo di enumeratore*** è l' `IEnumerator` interfaccia.</span><span class="sxs-lookup"><span data-stu-id="635c5-366">The ***collection type*** is the `IEnumerable` interface and the ***enumerator type*** is the `IEnumerator` interface.</span></span> <span data-ttu-id="635c5-367">Se l' `var` identificatore viene specificato come *local_variable_type* , il ***tipo di elemento*** è `dynamic`; in caso contrario `object`, è.</span><span class="sxs-lookup"><span data-stu-id="635c5-367">If the `var` identifier is given as the *local_variable_type* then the ***element type*** is `dynamic`, otherwise it is `object`.</span></span>
*  <span data-ttu-id="635c5-368">In caso contrario, determinare se `X` il tipo dispone `GetEnumerator` di un metodo appropriato:</span><span class="sxs-lookup"><span data-stu-id="635c5-368">Otherwise, determine whether the type `X` has an appropriate `GetEnumerator` method:</span></span>
   * <span data-ttu-id="635c5-369">Eseguire la ricerca di membri sul `X` tipo con `GetEnumerator` identificatore e nessun argomento di tipo.</span><span class="sxs-lookup"><span data-stu-id="635c5-369">Perform member lookup on the type `X` with identifier `GetEnumerator` and no type arguments.</span></span> <span data-ttu-id="635c5-370">Se la ricerca dei membri non produce una corrispondenza o produce un'ambiguità o produce una corrispondenza che non è un gruppo di metodi, verificare la presenza di un'interfaccia enumerabile come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="635c5-370">If the member lookup does not produce a match, or it produces an ambiguity, or produces a match that is not a method group, check for an enumerable interface as described below.</span></span> <span data-ttu-id="635c5-371">Si consiglia di emettere un avviso se la ricerca di membri produce qualsiasi elemento tranne un gruppo di metodi o nessuna corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="635c5-371">It is recommended that a warning be issued if member lookup produces anything except a method group or no match.</span></span>
   * <span data-ttu-id="635c5-372">Eseguire la risoluzione dell'overload usando il gruppo di metodi risultante e un elenco di argomenti vuoto.</span><span class="sxs-lookup"><span data-stu-id="635c5-372">Perform overload resolution using the resulting method group and an empty argument list.</span></span> <span data-ttu-id="635c5-373">Se la risoluzione dell'overload non produce metodi applicabili, comporta un'ambiguità o produce un unico metodo migliore, ma tale metodo è statico o non pubblico, verificare la presenza di un'interfaccia enumerabile come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="635c5-373">If overload resolution results in no applicable methods, results in an ambiguity, or results in a single best method but that method is either static or not public, check for an enumerable interface as described below.</span></span> <span data-ttu-id="635c5-374">Si consiglia di emettere un avviso se la risoluzione dell'overload produce qualsiasi elemento tranne un metodo di istanza pubblica non ambiguo o senza metodi applicabili.</span><span class="sxs-lookup"><span data-stu-id="635c5-374">It is recommended that a warning be issued if overload resolution produces anything except an unambiguous public instance method or no applicable methods.</span></span>
   * <span data-ttu-id="635c5-375">Se il tipo `E` restituito `GetEnumerator` del metodo non è una classe, uno struct o un tipo di interfaccia, viene generato un errore e non vengono eseguiti altri passaggi.</span><span class="sxs-lookup"><span data-stu-id="635c5-375">If the return type `E` of the `GetEnumerator` method is not a class, struct or interface type, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="635c5-376">La ricerca del membro viene `E` eseguita con l' `Current` identificatore e nessun argomento di tipo.</span><span class="sxs-lookup"><span data-stu-id="635c5-376">Member lookup is performed on `E` with the identifier `Current` and no type arguments.</span></span> <span data-ttu-id="635c5-377">Se la ricerca dei membri non produce corrispondenze, il risultato è un errore o il risultato è qualsiasi eccezione, ad eccezione di una proprietà di istanza pubblica che consente la lettura, viene generato un errore e non vengono eseguiti altri passaggi.</span><span class="sxs-lookup"><span data-stu-id="635c5-377">If the member lookup produces no match, the result is an error, or the result is anything except a public instance property that permits reading, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="635c5-378">La ricerca del membro viene `E` eseguita con l' `MoveNext` identificatore e nessun argomento di tipo.</span><span class="sxs-lookup"><span data-stu-id="635c5-378">Member lookup is performed on `E` with the identifier `MoveNext` and no type arguments.</span></span> <span data-ttu-id="635c5-379">Se la ricerca dei membri non produce corrispondenze, il risultato è un errore o il risultato è qualsiasi eccezione a un gruppo di metodi, viene generato un errore e non vengono eseguiti altri passaggi.</span><span class="sxs-lookup"><span data-stu-id="635c5-379">If the member lookup produces no match, the result is an error, or the result is anything except a method group, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="635c5-380">La risoluzione dell'overload viene eseguita sul gruppo di metodi con un elenco di argomenti vuoto.</span><span class="sxs-lookup"><span data-stu-id="635c5-380">Overload resolution is performed on the method group with an empty argument list.</span></span> <span data-ttu-id="635c5-381">Se la risoluzione dell'overload non produce metodi applicabili, comporta un'ambiguità o produce un unico metodo migliore, ma tale metodo è statico o non pubblico oppure il tipo restituito non `bool`lo è, viene generato un errore e non vengono eseguiti altri passaggi.</span><span class="sxs-lookup"><span data-stu-id="635c5-381">If overload resolution results in no applicable methods, results in an ambiguity, or results in a single best method but that method is either static or not public, or its return type is not `bool`, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="635c5-382">Il ***tipo*** di raccolta `X`è, il tipo di `E` ***enumeratore*** è `Current` e il tipo di elemento è il tipo della proprietà.</span><span class="sxs-lookup"><span data-stu-id="635c5-382">The ***collection type*** is `X`, the ***enumerator type*** is `E`, and the ***element type*** is the type of the `Current` property.</span></span>

*  <span data-ttu-id="635c5-383">In caso contrario, verificare la presenza di un'interfaccia enumerabile:</span><span class="sxs-lookup"><span data-stu-id="635c5-383">Otherwise, check for an enumerable interface:</span></span>
   * <span data-ttu-id="635c5-384">Se tra tutti i tipi `Ti` per i quali esiste una conversione implicita `X` da `IEnumerable<Ti>`a, esiste un tipo `T` univoco che `T` non `dynamic` è e per tutti gli altri `Ti` è presente un `IEnumerable<T>` la conversione implicita da a, quindi il tipo di ***raccolta*** è l' `IEnumerable<T>`interfaccia, il tipo di `IEnumerator<T>` `IEnumerable<Ti>`enumeratore è l'interfaccia e il tipo di ***elemento*** è `T`.</span><span class="sxs-lookup"><span data-stu-id="635c5-384">If among all the types `Ti` for which there is an implicit conversion from `X` to `IEnumerable<Ti>`, there is a unique type `T` such that `T` is not `dynamic` and for all the other `Ti` there is an implicit conversion from `IEnumerable<T>` to `IEnumerable<Ti>`, then the ***collection type*** is the interface `IEnumerable<T>`, the ***enumerator type*** is the interface `IEnumerator<T>`, and the ***element type*** is `T`.</span></span>
   * <span data-ttu-id="635c5-385">In caso contrario, se è presente più di un `T`tipo di questo tipo, viene generato un errore e non vengono eseguiti altri passaggi.</span><span class="sxs-lookup"><span data-stu-id="635c5-385">Otherwise, if there is more than one such type `T`, then an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="635c5-386">In caso contrario, se esiste una `X` conversione implicita da `System.Collections.IEnumerable` all'interfaccia, il ***tipo di raccolta*** è questa interfaccia, il tipo di ***enumeratore*** è l'interfaccia `System.Collections.IEnumerator`e il tipo di ***elemento*** è `object`.</span><span class="sxs-lookup"><span data-stu-id="635c5-386">Otherwise, if there is an implicit conversion from `X` to the `System.Collections.IEnumerable` interface, then the ***collection type*** is this interface, the ***enumerator type*** is the interface `System.Collections.IEnumerator`, and the ***element type*** is `object`.</span></span>
   * <span data-ttu-id="635c5-387">In caso contrario, viene generato un errore e non vengono eseguiti altri passaggi.</span><span class="sxs-lookup"><span data-stu-id="635c5-387">Otherwise, an error is produced and no further steps are taken.</span></span>

<span data-ttu-id="635c5-388">I passaggi precedenti, se riusciti, producono in modo non ambiguo un `C`tipo di raccolta `E` , un tipo `T`di enumeratore e un tipo di elemento.</span><span class="sxs-lookup"><span data-stu-id="635c5-388">The above steps, if successful, unambiguously produce a collection type `C`, enumerator type `E` and element type `T`.</span></span> <span data-ttu-id="635c5-389">Istruzione foreach nel formato</span><span class="sxs-lookup"><span data-stu-id="635c5-389">A foreach statement of the form</span></span>
```csharp
foreach (V v in x) embedded_statement
```
<span data-ttu-id="635c5-390">viene quindi espanso in:</span><span class="sxs-lookup"><span data-stu-id="635c5-390">is then expanded to:</span></span>
```csharp
{
    E e = ((C)(x)).GetEnumerator();
    try {
        while (e.MoveNext()) {
            V v = (V)(T)e.Current;
            embedded_statement
        }
    }
    finally {
        ... // Dispose e
    }
}
```

<span data-ttu-id="635c5-391">La variabile `e` non è visibile o accessibile all'espressione `x` o all'istruzione incorporata o a qualsiasi altro codice sorgente del programma.</span><span class="sxs-lookup"><span data-stu-id="635c5-391">The variable `e` is not visible to or accessible to the expression `x` or the embedded statement or any other source code of the program.</span></span> <span data-ttu-id="635c5-392">La variabile `v` è di sola lettura nell'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="635c5-392">The variable `v` is read-only in the embedded statement.</span></span> <span data-ttu-id="635c5-393">Se non è presente una conversione esplicita ([conversioni esplicite](conversions.md#explicit-conversions)) `T` da (il tipo di elemento `V` ) a ( *local_variable_type* nell'istruzione foreach), viene generato un errore e non vengono eseguiti altri passaggi.</span><span class="sxs-lookup"><span data-stu-id="635c5-393">If there is not an explicit conversion ([Explicit conversions](conversions.md#explicit-conversions)) from `T` (the element type) to `V` (the *local_variable_type* in the foreach statement), an error is produced and no further steps are taken.</span></span> <span data-ttu-id="635c5-394">Se `x` ha il valore `null`, viene `System.NullReferenceException` generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-394">If `x` has the value `null`, a `System.NullReferenceException` is thrown at run-time.</span></span>

<span data-ttu-id="635c5-395">Un'implementazione può implementare una determinata istruzione foreach in modo diverso, ad esempio per motivi di prestazioni, purché il comportamento sia coerente con l'espansione precedente.</span><span class="sxs-lookup"><span data-stu-id="635c5-395">An implementation is permitted to implement a given foreach-statement differently, e.g. for performance reasons, as long as the behavior is consistent with the above expansion.</span></span>

<span data-ttu-id="635c5-396">La posizione di `v` all'interno del ciclo while è importante per il modo in cui viene acquisita da qualsiasi funzione anonima che si verifica in *embedded_statement*.</span><span class="sxs-lookup"><span data-stu-id="635c5-396">The placement of `v` inside the while loop is important for how it is captured by any anonymous function occurring in the *embedded_statement*.</span></span>

<span data-ttu-id="635c5-397">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="635c5-397">For example:</span></span>
```csharp
int[] values = { 7, 9, 13 };
Action f = null;

foreach (var value in values)
{
    if (f == null) f = () => Console.WriteLine("First value: " + value);
}

f();
```
<span data-ttu-id="635c5-398">Se `v` è stato dichiarato all'esterno del ciclo while, verrebbe condiviso tra tutte le iterazioni e il relativo valore dopo il ciclo for sarebbe il valore finale, `13`, che corrisponde alla chiamata di `f` stampa.</span><span class="sxs-lookup"><span data-stu-id="635c5-398">If `v` was declared outside of the while loop, it would be shared among all iterations, and its value after the for loop would be the final value, `13`, which is what the invocation of `f` would print.</span></span> <span data-ttu-id="635c5-399">Al contrario, poiché ogni iterazione dispone di `v`una propria variabile, quella `f` acquisita dalla prima iterazione continuerà a contenere `7`il valore, che è quello che verrà stampato.</span><span class="sxs-lookup"><span data-stu-id="635c5-399">Instead, because each iteration has its own variable `v`, the one captured by `f` in the first iteration will continue to hold the value `7`, which is what will be printed.</span></span> <span data-ttu-id="635c5-400">(Nota: le versioni precedenti C# di `v` sono state dichiarate all'esterno del ciclo while).</span><span class="sxs-lookup"><span data-stu-id="635c5-400">(Note: earlier versions of C# declared `v` outside of the while loop.)</span></span>

<span data-ttu-id="635c5-401">Il corpo del blocco finally viene costruito in base ai passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="635c5-401">The body of the finally block is constructed according to the following steps:</span></span>

*  <span data-ttu-id="635c5-402">Se esiste una conversione implicita da `E` `System.IDisposable` all'interfaccia,</span><span class="sxs-lookup"><span data-stu-id="635c5-402">If there is an implicit conversion from `E` to the `System.IDisposable` interface, then</span></span>
   *  <span data-ttu-id="635c5-403">Se `E` è un tipo di valore non nullable, la clausola finally viene espansa nell'equivalente semantico di:</span><span class="sxs-lookup"><span data-stu-id="635c5-403">If `E` is a non-nullable value type then the finally clause is expanded to the semantic equivalent  of:</span></span>

      ```csharp
      finally {
          ((System.IDisposable)e).Dispose();
      }
      ```

   *  <span data-ttu-id="635c5-404">In caso contrario, la clausola finally viene espansa all'equivalente semantico di:</span><span class="sxs-lookup"><span data-stu-id="635c5-404">Otherwise the finally clause is expanded to the semantic equivalent of:</span></span>

      ```csharp
      finally {
          if (e != null) ((System.IDisposable)e).Dispose();
      }
      ```

   <span data-ttu-id="635c5-405">ad eccezione del `E` fatto che se è un tipo di valore o un parametro di tipo di cui è stata creata un'istanza `e` a `System.IDisposable` un tipo di valore, il cast di a non comporterà la conversione boxing.</span><span class="sxs-lookup"><span data-stu-id="635c5-405">except that if `E` is a value type, or a type parameter instantiated to a value type, then the cast of `e` to `System.IDisposable` will not cause boxing to occur.</span></span>

*  <span data-ttu-id="635c5-406">In caso contrario `E` , se è un tipo sealed, la clausola finally viene espansa in un blocco vuoto:</span><span class="sxs-lookup"><span data-stu-id="635c5-406">Otherwise, if `E` is a sealed type, the finally clause is expanded to an empty block:</span></span>

   ```csharp
   finally {
   }
   ```

*  <span data-ttu-id="635c5-407">In caso contrario, la clausola finally viene espansa in:</span><span class="sxs-lookup"><span data-stu-id="635c5-407">Otherwise, the finally clause is expanded to:</span></span>

   ```csharp
   finally {
       System.IDisposable d = e as System.IDisposable;
       if (d != null) d.Dispose();
   }
   ```    

   <span data-ttu-id="635c5-408">La variabile `d` locale non è visibile o accessibile a qualsiasi codice utente.</span><span class="sxs-lookup"><span data-stu-id="635c5-408">The local variable `d` is not visible to or accessible to any user code.</span></span> <span data-ttu-id="635c5-409">In particolare, non è in conflitto con altre variabili il cui ambito include il blocco finally.</span><span class="sxs-lookup"><span data-stu-id="635c5-409">In particular, it does not conflict with any other variable whose scope includes the finally block.</span></span>

<span data-ttu-id="635c5-410">L'ordine in cui `foreach` attraversa gli elementi di una matrice è il seguente: Per gli elementi delle matrici unidimensionali viene attraversato l'ordine di indice crescente, iniziando con `0` index e terminando `Length - 1`con index.</span><span class="sxs-lookup"><span data-stu-id="635c5-410">The order in which `foreach` traverses the elements of an array, is as follows: For single-dimensional arrays elements are traversed in increasing index order, starting with index `0` and ending with index `Length - 1`.</span></span> <span data-ttu-id="635c5-411">Per le matrici multidimensionali, gli elementi vengono attraversati in modo che gli indici della dimensione più a destra vengano aumentati per primi, quindi la dimensione sinistra successiva e così via verso sinistra.</span><span class="sxs-lookup"><span data-stu-id="635c5-411">For multi-dimensional arrays, elements are traversed such that the indices of the rightmost dimension are increased first, then the next left dimension, and so on to the left.</span></span>

<span data-ttu-id="635c5-412">Nell'esempio seguente viene stampato ogni valore in una matrice bidimensionale, nell'ordine degli elementi:</span><span class="sxs-lookup"><span data-stu-id="635c5-412">The following example prints out each value in a two-dimensional array, in element order:</span></span>
```csharp
using System;

class Test
{
    static void Main() {
        double[,] values = {
            {1.2, 2.3, 3.4, 4.5},
            {5.6, 6.7, 7.8, 8.9}
        };

        foreach (double elementValue in values)
            Console.Write("{0} ", elementValue);

        Console.WriteLine();
    }
}
```
<span data-ttu-id="635c5-413">L'output prodotto è il seguente:</span><span class="sxs-lookup"><span data-stu-id="635c5-413">The output produced is as follows:</span></span>
```csharp
1.2 2.3 3.4 4.5 5.6 6.7 7.8 8.9
```

<span data-ttu-id="635c5-414">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="635c5-414">In the example</span></span>
```csharp
int[] numbers = { 1, 3, 5, 7, 9 };
foreach (var n in numbers) Console.WriteLine(n);
```
<span data-ttu-id="635c5-415">il tipo di `n` viene dedotto `int`come, il tipo di elemento di `numbers`.</span><span class="sxs-lookup"><span data-stu-id="635c5-415">the type of `n` is inferred to be `int`, the element type of `numbers`.</span></span>

## <a name="jump-statements"></a><span data-ttu-id="635c5-416">Istruzioni di salto</span><span class="sxs-lookup"><span data-stu-id="635c5-416">Jump statements</span></span>

<span data-ttu-id="635c5-417">Istruzioni Jump che trasferiscono il controllo in modo non condizionale.</span><span class="sxs-lookup"><span data-stu-id="635c5-417">Jump statements unconditionally transfer control.</span></span>

```antlr
jump_statement
    : break_statement
    | continue_statement
    | goto_statement
    | return_statement
    | throw_statement
    ;
```

<span data-ttu-id="635c5-418">Il percorso in cui un'istruzione Jump trasferisce il controllo è chiamato la ***destinazione*** dell'istruzione Jump.</span><span class="sxs-lookup"><span data-stu-id="635c5-418">The location to which a jump statement transfers control is called the ***target*** of the jump statement.</span></span>

<span data-ttu-id="635c5-419">Quando un'istruzione Jump si verifica all'interno di un blocco e la destinazione dell'istruzione Jump è esterna a tale blocco, viene detto che l'istruzione Jump ***esce*** dal blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-419">When a jump statement occurs within a block, and the target of that jump statement is outside that block, the jump statement is said to ***exit*** the block.</span></span> <span data-ttu-id="635c5-420">Mentre un'istruzione Jump può trasferire il controllo da un blocco, non può mai trasferire il controllo in un blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-420">While a jump statement may transfer control out of a block, it can never transfer control into a block.</span></span>

<span data-ttu-id="635c5-421">L'esecuzione di istruzioni di salto è complessa dalla presenza di `try` istruzioni che interessano.</span><span class="sxs-lookup"><span data-stu-id="635c5-421">Execution of jump statements is complicated by the presence of intervening `try` statements.</span></span> <span data-ttu-id="635c5-422">In assenza di tali `try` istruzioni, un'istruzione Jump trasferisce in modo incondizionato il controllo dall'istruzione Jump alla relativa destinazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-422">In the absence of such `try` statements, a jump statement unconditionally transfers control from the jump statement to its target.</span></span> <span data-ttu-id="635c5-423">In presenza di tali `try` istruzioni, l'esecuzione è più complessa.</span><span class="sxs-lookup"><span data-stu-id="635c5-423">In the presence of such intervening `try` statements, execution is more complex.</span></span> <span data-ttu-id="635c5-424">Se l'istruzione Jump esce da uno o più `try` blocchi con blocchi `finally` associati, il controllo `finally` viene inizialmente trasferito al blocco dell'istruzione più `try` interna.</span><span class="sxs-lookup"><span data-stu-id="635c5-424">If the jump statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="635c5-425">Quando e se il controllo raggiunge il punto finale di `finally` un blocco, il `finally` controllo viene trasferito al blocco della successiva istruzione di `try` inclusione.</span><span class="sxs-lookup"><span data-stu-id="635c5-425">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="635c5-426">Questo processo viene ripetuto fino a `finally` quando non vengono eseguiti i `try` blocchi di tutte le istruzioni coinvolte.</span><span class="sxs-lookup"><span data-stu-id="635c5-426">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>

<span data-ttu-id="635c5-427">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="635c5-427">In the example</span></span>
```csharp
using System;

class Test
{
    static void Main() {
        while (true) {
            try {
                try {
                    Console.WriteLine("Before break");
                    break;
                }
                finally {
                    Console.WriteLine("Innermost finally block");
                }
            }
            finally {
                Console.WriteLine("Outermost finally block");
            }
        }
        Console.WriteLine("After break");
    }
}
```
<span data-ttu-id="635c5-428">i `finally` blocchi associati a due `try` istruzioni vengono eseguiti prima che il controllo venga trasferito alla destinazione dell'istruzione Jump.</span><span class="sxs-lookup"><span data-stu-id="635c5-428">the `finally` blocks associated with two `try` statements are executed before control is transferred to the target of the jump statement.</span></span>

<span data-ttu-id="635c5-429">L'output prodotto è il seguente:</span><span class="sxs-lookup"><span data-stu-id="635c5-429">The output produced is as follows:</span></span>
```
Before break
Innermost finally block
Outermost finally block
After break
```

### <a name="the-break-statement"></a><span data-ttu-id="635c5-430">Istruzione break</span><span class="sxs-lookup"><span data-stu-id="635c5-430">The break statement</span></span>

<span data-ttu-id="635c5-431">L' `break` istruzione esce dall'istruzione di inclusione `switch`, `while` `do` `for`,, o `foreach` più vicina.</span><span class="sxs-lookup"><span data-stu-id="635c5-431">The `break` statement exits the nearest enclosing `switch`, `while`, `do`, `for`, or `foreach` statement.</span></span>

```antlr
break_statement
    : 'break' ';'
    ;
```

<span data-ttu-id="635c5-432">La destinazione di un' `break` istruzione è il punto finale dell'istruzione di inclusione `switch`più `while`vicina `do`, `for`,, `foreach` o.</span><span class="sxs-lookup"><span data-stu-id="635c5-432">The target of a `break` statement is the end point of the nearest enclosing `switch`, `while`, `do`, `for`, or `foreach` statement.</span></span> <span data-ttu-id="635c5-433">Se un' `break` istruzione non è racchiusa tra `switch`un' `while`istruzione `do`, `for`,, `foreach` o, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-433">If a `break` statement is not enclosed by a `switch`, `while`, `do`, `for`, or `foreach` statement, a compile-time error occurs.</span></span>

<span data-ttu-id="635c5-434">Quando più `switch`istruzioni `while` `break` ,, ,`for` o`foreach` sono annidate all'interno di un'altra, un'istruzione si applica solo all'istruzione più interna. `do`</span><span class="sxs-lookup"><span data-stu-id="635c5-434">When multiple `switch`, `while`, `do`, `for`, or `foreach` statements are nested within each other, a `break` statement applies only to the innermost statement.</span></span> <span data-ttu-id="635c5-435">Per trasferire il controllo tra più livelli di nidificazione `goto` , è necessario utilizzare un'istruzione ([istruzione goto](statements.md#the-goto-statement)).</span><span class="sxs-lookup"><span data-stu-id="635c5-435">To transfer control across multiple nesting levels, a `goto` statement ([The goto statement](statements.md#the-goto-statement)) must be used.</span></span>

<span data-ttu-id="635c5-436">Un' `break` istruzione non può uscire `finally` da un blocco ([istruzione try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="635c5-436">A `break` statement cannot exit a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span> <span data-ttu-id="635c5-437">Quando un' `break` istruzione si verifica all' `finally` interno di un blocco `break` , la destinazione dell'istruzione deve trovarsi `finally` all'interno dello stesso blocco; in caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-437">When a `break` statement occurs within a `finally` block, the target of the `break` statement must be within the same `finally` block; otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="635c5-438">Un' `break` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="635c5-438">A `break` statement is executed as follows:</span></span>

*  <span data-ttu-id="635c5-439">Se l' `break` istruzione esce da uno o più `try` blocchi con `finally` blocchi `finally` associati, il controllo viene inizialmente trasferito al blocco dell'istruzione più `try` interna.</span><span class="sxs-lookup"><span data-stu-id="635c5-439">If the `break` statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="635c5-440">Quando e se il controllo raggiunge il punto finale di `finally` un blocco, il `finally` controllo viene trasferito al blocco della successiva istruzione di `try` inclusione.</span><span class="sxs-lookup"><span data-stu-id="635c5-440">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="635c5-441">Questo processo viene ripetuto fino a `finally` quando non vengono eseguiti i `try` blocchi di tutte le istruzioni coinvolte.</span><span class="sxs-lookup"><span data-stu-id="635c5-441">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>
*  <span data-ttu-id="635c5-442">Il controllo viene trasferito alla destinazione dell' `break` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-442">Control is transferred to the target of the `break` statement.</span></span>

<span data-ttu-id="635c5-443">Poiché un' `break` istruzione trasferisce in modo incondizionato il controllo altrove, il `break` punto finale di un'istruzione non è mai raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-443">Because a `break` statement unconditionally transfers control elsewhere, the end point of a `break` statement is never reachable.</span></span>

### <a name="the-continue-statement"></a><span data-ttu-id="635c5-444">Istruzione continue</span><span class="sxs-lookup"><span data-stu-id="635c5-444">The continue statement</span></span>

<span data-ttu-id="635c5-445">L' `continue` istruzione avvia una nuova iterazione dell'istruzione di inclusione `do` `while`, `for`, o `foreach` più vicina.</span><span class="sxs-lookup"><span data-stu-id="635c5-445">The `continue` statement starts a new iteration of the nearest enclosing `while`, `do`, `for`, or `foreach` statement.</span></span>

```antlr
continue_statement
    : 'continue' ';'
    ;
```

<span data-ttu-id="635c5-446">La destinazione di un' `continue` istruzione è il punto finale dell'istruzione incorporata dell'istruzione, `do`, `for`o `while` `foreach` di inclusione più vicina.</span><span class="sxs-lookup"><span data-stu-id="635c5-446">The target of a `continue` statement is the end point of the embedded statement of the nearest enclosing `while`, `do`, `for`, or `foreach` statement.</span></span> <span data-ttu-id="635c5-447">Se un' `continue` istruzione non è racchiusa tra `while`un' `do`istruzione `for`,, `foreach` o, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-447">If a `continue` statement is not enclosed by a `while`, `do`, `for`, or `foreach` statement, a compile-time error occurs.</span></span>

<span data-ttu-id="635c5-448">Quando più `while`istruzioni `do` `continue` ,, o `foreach` sono annidate all'interno di un'altra, un'istruzione si applica solo all'istruzione più interna. `for`</span><span class="sxs-lookup"><span data-stu-id="635c5-448">When multiple `while`, `do`, `for`, or `foreach` statements are nested within each other, a `continue` statement applies only to the innermost statement.</span></span> <span data-ttu-id="635c5-449">Per trasferire il controllo tra più livelli di nidificazione `goto` , è necessario utilizzare un'istruzione ([istruzione goto](statements.md#the-goto-statement)).</span><span class="sxs-lookup"><span data-stu-id="635c5-449">To transfer control across multiple nesting levels, a `goto` statement ([The goto statement](statements.md#the-goto-statement)) must be used.</span></span>

<span data-ttu-id="635c5-450">Un' `continue` istruzione non può uscire `finally` da un blocco ([istruzione try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="635c5-450">A `continue` statement cannot exit a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span> <span data-ttu-id="635c5-451">Quando un' `continue` istruzione si verifica all' `finally` interno di un blocco `continue` , la destinazione dell'istruzione deve trovarsi `finally` all'interno dello stesso blocco; in caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-451">When a `continue` statement occurs within a `finally` block, the target of the `continue` statement must be within the same `finally` block; otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="635c5-452">Un' `continue` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="635c5-452">A `continue` statement is executed as follows:</span></span>

*  <span data-ttu-id="635c5-453">Se l' `continue` istruzione esce da uno o più `try` blocchi con `finally` blocchi `finally` associati, il controllo viene inizialmente trasferito al blocco dell'istruzione più `try` interna.</span><span class="sxs-lookup"><span data-stu-id="635c5-453">If the `continue` statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="635c5-454">Quando e se il controllo raggiunge il punto finale di `finally` un blocco, il `finally` controllo viene trasferito al blocco della successiva istruzione di `try` inclusione.</span><span class="sxs-lookup"><span data-stu-id="635c5-454">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="635c5-455">Questo processo viene ripetuto fino a `finally` quando non vengono eseguiti i `try` blocchi di tutte le istruzioni coinvolte.</span><span class="sxs-lookup"><span data-stu-id="635c5-455">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>
*  <span data-ttu-id="635c5-456">Il controllo viene trasferito alla destinazione dell' `continue` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-456">Control is transferred to the target of the `continue` statement.</span></span>

<span data-ttu-id="635c5-457">Poiché un' `continue` istruzione trasferisce in modo incondizionato il controllo altrove, il `continue` punto finale di un'istruzione non è mai raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-457">Because a `continue` statement unconditionally transfers control elsewhere, the end point of a `continue` statement is never reachable.</span></span>

### <a name="the-goto-statement"></a><span data-ttu-id="635c5-458">Istruzione GoTo</span><span class="sxs-lookup"><span data-stu-id="635c5-458">The goto statement</span></span>

<span data-ttu-id="635c5-459">L' `goto` istruzione trasferisce il controllo a un'istruzione contrassegnata da un'etichetta.</span><span class="sxs-lookup"><span data-stu-id="635c5-459">The `goto` statement transfers control to a statement that is marked by a label.</span></span>

```antlr
goto_statement
    : 'goto' identifier ';'
    | 'goto' 'case' constant_expression ';'
    | 'goto' 'default' ';'
    ;
```

<span data-ttu-id="635c5-460">La destinazione di un' `goto` istruzione *Identifier* è l'istruzione con etichetta con l'etichetta specificata.</span><span class="sxs-lookup"><span data-stu-id="635c5-460">The target of a `goto` *identifier* statement is the labeled statement with the given label.</span></span> <span data-ttu-id="635c5-461">Se un'etichetta con il nome specificato non esiste nel membro della funzione corrente o se l' `goto` istruzione non è inclusa nell'ambito dell'etichetta, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-461">If a label with the given name does not exist in the current function member, or if the `goto` statement is not within the scope of the label, a compile-time error occurs.</span></span> <span data-ttu-id="635c5-462">Questa regola consente di utilizzare un' `goto` istruzione per trasferire il controllo da un ambito annidato, ma non da un ambito annidato.</span><span class="sxs-lookup"><span data-stu-id="635c5-462">This rule permits the use of a `goto` statement to transfer control out of a nested scope, but not into a nested scope.</span></span> <span data-ttu-id="635c5-463">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="635c5-463">In the example</span></span>
```csharp
using System;

class Test
{
    static void Main(string[] args) {
        string[,] table = {
            {"Red", "Blue", "Green"},
            {"Monday", "Wednesday", "Friday"}
        };

        foreach (string str in args) {
            int row, colm;
            for (row = 0; row <= 1; ++row)
                for (colm = 0; colm <= 2; ++colm)
                    if (str == table[row,colm])
                         goto done;

            Console.WriteLine("{0} not found", str);
            continue;
    done:
            Console.WriteLine("Found {0} at [{1}][{2}]", str, row, colm);
        }
    }
}
```
<span data-ttu-id="635c5-464">viene `goto` utilizzata un'istruzione per trasferire il controllo da un ambito annidato.</span><span class="sxs-lookup"><span data-stu-id="635c5-464">a `goto` statement is used to transfer control out of a nested scope.</span></span>

<span data-ttu-id="635c5-465">La destinazione di un' `goto case` istruzione è l'elenco di istruzioni nell'istruzione di inclusione `switch` immediata ([istruzione switch](statements.md#the-switch-statement)), che contiene un' `case` etichetta con il valore costante specificato.</span><span class="sxs-lookup"><span data-stu-id="635c5-465">The target of a `goto case` statement is the statement list in the immediately enclosing `switch` statement ([The switch statement](statements.md#the-switch-statement)), which contains a `case` label with the given constant value.</span></span> <span data-ttu-id="635c5-466">Se l' `goto case` istruzione non è racchiusa tra `switch` un'istruzione, se *constant_expression* non è implicitamente convertibile ([conversioni implicite](conversions.md#implicit-conversions)) nel tipo di regola dell'istruzione di inclusione `switch` più vicina oppure se l'istruzione di inclusione `switch` più vicina non contiene un' `case` etichetta con il valore costante specificato, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-466">If the `goto case` statement is not enclosed by a `switch` statement, if the *constant_expression* is not implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the governing type of the nearest enclosing `switch` statement, or if the nearest enclosing `switch` statement does not contain a `case` label with the given constant value, a compile-time error occurs.</span></span>

<span data-ttu-id="635c5-467">La destinazione di un' `goto default` istruzione è l'elenco di istruzioni nell'istruzione di inclusione `switch` immediata ([istruzione switch](statements.md#the-switch-statement)), che contiene un' `default` etichetta.</span><span class="sxs-lookup"><span data-stu-id="635c5-467">The target of a `goto default` statement is the statement list in the immediately enclosing `switch` statement ([The switch statement](statements.md#the-switch-statement)), which contains a `default` label.</span></span> <span data-ttu-id="635c5-468">Se l' `goto default` istruzione non è racchiusa tra `switch` un'istruzione o l'istruzione di inclusione `switch` più vicina non contiene un' `default` etichetta, si verificherà un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-468">If the `goto default` statement is not enclosed by a `switch` statement, or if the nearest enclosing `switch` statement does not contain a `default` label, a compile-time error occurs.</span></span>

<span data-ttu-id="635c5-469">Un' `goto` istruzione non può uscire `finally` da un blocco ([istruzione try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="635c5-469">A `goto` statement cannot exit a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span> <span data-ttu-id="635c5-470">Quando un' `goto` istruzione si verifica all' `finally` interno di un blocco `goto` , la destinazione dell'istruzione deve trovarsi `finally` all'interno dello stesso blocco o in caso contrario si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-470">When a `goto` statement occurs within a `finally` block, the target of the `goto` statement must be within the same `finally` block, or otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="635c5-471">Un' `goto` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="635c5-471">A `goto` statement is executed as follows:</span></span>

*  <span data-ttu-id="635c5-472">Se l' `goto` istruzione esce da uno o più `try` blocchi con `finally` blocchi `finally` associati, il controllo viene inizialmente trasferito al blocco dell'istruzione più `try` interna.</span><span class="sxs-lookup"><span data-stu-id="635c5-472">If the `goto` statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="635c5-473">Quando e se il controllo raggiunge il punto finale di `finally` un blocco, il `finally` controllo viene trasferito al blocco della successiva istruzione di `try` inclusione.</span><span class="sxs-lookup"><span data-stu-id="635c5-473">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="635c5-474">Questo processo viene ripetuto fino a `finally` quando non vengono eseguiti i `try` blocchi di tutte le istruzioni coinvolte.</span><span class="sxs-lookup"><span data-stu-id="635c5-474">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>
*  <span data-ttu-id="635c5-475">Il controllo viene trasferito alla destinazione dell' `goto` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-475">Control is transferred to the target of the `goto` statement.</span></span>

<span data-ttu-id="635c5-476">Poiché un' `goto` istruzione trasferisce in modo incondizionato il controllo altrove, il `goto` punto finale di un'istruzione non è mai raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-476">Because a `goto` statement unconditionally transfers control elsewhere, the end point of a `goto` statement is never reachable.</span></span>

### <a name="the-return-statement"></a><span data-ttu-id="635c5-477">Istruzione return</span><span class="sxs-lookup"><span data-stu-id="635c5-477">The return statement</span></span>

<span data-ttu-id="635c5-478">L' `return` istruzione restituisce il controllo al chiamante corrente della funzione in cui viene visualizzata `return` l'istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-478">The `return` statement returns control to the current caller of the function in which the `return` statement appears.</span></span>

```antlr
return_statement
    : 'return' expression? ';'
    ;
```

<span data-ttu-id="635c5-479">Un' `return` istruzione senza espressione può essere utilizzata solo in un membro di funzione che non calcola un valore, ovvero un metodo con il tipo di risultato ([corpo del metodo](classes.md#method-body) `void`), la `set` funzione di accesso di una proprietà o un indicizzatore, `add` il funzioni `remove` di accesso e di un evento, un costruttore di istanza, un costruttore statico o un distruttore.</span><span class="sxs-lookup"><span data-stu-id="635c5-479">A `return` statement with no expression can be used only in a function member that does not compute a value, that is, a method with the result type ([Method body](classes.md#method-body)) `void`, the `set` accessor of a property or indexer, the `add` and `remove` accessors of an event, an instance constructor, a static constructor, or a destructor.</span></span>

<span data-ttu-id="635c5-480">Un' `return` istruzione con un'espressione può essere utilizzata solo in un membro di funzione che calcola un valore, ovvero un metodo con un tipo di risultato non void, la `get` funzione di accesso di una proprietà o un indicizzatore o un operatore definito dall'utente.</span><span class="sxs-lookup"><span data-stu-id="635c5-480">A `return` statement with an expression can only be used in a function member that computes a value, that is, a method with a non-void result type, the `get` accessor of a property or indexer, or a user-defined operator.</span></span> <span data-ttu-id="635c5-481">Una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) deve esistere dal tipo dell'espressione al tipo restituito del membro della funzione contenitore.</span><span class="sxs-lookup"><span data-stu-id="635c5-481">An implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) must exist from the type of the expression to the return type of the containing function member.</span></span>

<span data-ttu-id="635c5-482">È inoltre possibile utilizzare le istruzioni return nel corpo di espressioni di funzione anonime ([espressioni di funzione anonime](expressions.md#anonymous-function-expressions)) e partecipare alla determinazione delle conversioni esistenti per tali funzioni.</span><span class="sxs-lookup"><span data-stu-id="635c5-482">Return statements can also be used in the body of anonymous function expressions ([Anonymous function expressions](expressions.md#anonymous-function-expressions)), and participate in determining which conversions exist for those functions.</span></span>

<span data-ttu-id="635c5-483">Si tratta di un errore in fase di compilazione `return` per un'istruzione da visualizzare `finally` in un blocco ([istruzione try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="635c5-483">It is a compile-time error for a `return` statement to appear in a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span>

<span data-ttu-id="635c5-484">Un' `return` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="635c5-484">A `return` statement is executed as follows:</span></span>

*  <span data-ttu-id="635c5-485">Se l' `return` istruzione specifica un'espressione, l'espressione viene valutata e il valore risultante viene convertito nel tipo restituito della funzione che lo contiene mediante una conversione implicita.</span><span class="sxs-lookup"><span data-stu-id="635c5-485">If the `return` statement specifies an expression, the expression is evaluated and the resulting value is converted to the return type of the containing function by an implicit conversion.</span></span> <span data-ttu-id="635c5-486">Il risultato della conversione diventa il valore risultante prodotto dalla funzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-486">The result of the conversion becomes the result value produced by the function.</span></span>
*  <span data-ttu-id="635c5-487">Se l' `return` istruzione è racchiusa tra uno o `try` più `catch` blocchi o con `finally` blocchi associati, il controllo viene inizialmente trasferito `finally` al blocco dell'istruzione `try` più interna.</span><span class="sxs-lookup"><span data-stu-id="635c5-487">If the `return` statement is enclosed by one or more `try` or `catch` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="635c5-488">Quando e se il controllo raggiunge il punto finale di `finally` un blocco, il `finally` controllo viene trasferito al blocco della successiva istruzione di `try` inclusione.</span><span class="sxs-lookup"><span data-stu-id="635c5-488">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="635c5-489">Questo processo viene ripetuto fino a `finally` quando non vengono eseguiti i `try` blocchi di tutte le istruzioni di inclusione.</span><span class="sxs-lookup"><span data-stu-id="635c5-489">This process is repeated until the `finally` blocks of all enclosing `try` statements have been executed.</span></span>
*  <span data-ttu-id="635c5-490">Se la funzione che lo contiene non è una funzione asincrona, il controllo viene restituito al chiamante della funzione contenitore insieme al valore del risultato, se presente.</span><span class="sxs-lookup"><span data-stu-id="635c5-490">If the containing function is not an async function, control is returned to the caller of the containing function along with the result value, if any.</span></span>
*  <span data-ttu-id="635c5-491">Se la funzione contenitore è una funzione asincrona, il controllo viene restituito al chiamante corrente e il valore del risultato, se presente, viene registrato nell'attività restituita come descritto in ([interfacce enumeratore](classes.md#enumerator-interfaces)).</span><span class="sxs-lookup"><span data-stu-id="635c5-491">If the containing function is an async function, control is returned to the current caller, and the result value, if any, is recorded in the return task as described in ([Enumerator interfaces](classes.md#enumerator-interfaces)).</span></span>

<span data-ttu-id="635c5-492">Poiché un' `return` istruzione trasferisce in modo incondizionato il controllo altrove, il `return` punto finale di un'istruzione non è mai raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-492">Because a `return` statement unconditionally transfers control elsewhere, the end point of a `return` statement is never reachable.</span></span>

### <a name="the-throw-statement"></a><span data-ttu-id="635c5-493">Istruzione throw</span><span class="sxs-lookup"><span data-stu-id="635c5-493">The throw statement</span></span>

<span data-ttu-id="635c5-494">L' `throw` istruzione genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="635c5-494">The `throw` statement throws an exception.</span></span>

```antlr
throw_statement
    : 'throw' expression? ';'
    ;
```

<span data-ttu-id="635c5-495">Un' `throw` istruzione con un'espressione genera il valore prodotto dalla valutazione dell'espressione.</span><span class="sxs-lookup"><span data-stu-id="635c5-495">A `throw` statement with an expression throws the value produced by evaluating the expression.</span></span> <span data-ttu-id="635c5-496">L'espressione deve indicare un valore del tipo `System.Exception`di classe, di un tipo di classe che deriva da `System.Exception` o di un tipo di parametro di tipo che `System.Exception` ha (o una sottoclasse) come classe di base valida.</span><span class="sxs-lookup"><span data-stu-id="635c5-496">The expression must denote a value of the class type `System.Exception`, of a class type that derives from `System.Exception` or of a type parameter type that has `System.Exception` (or a subclass thereof) as its effective base class.</span></span> <span data-ttu-id="635c5-497">Se la valutazione dell'espressione produce `null`, viene `System.NullReferenceException` invece generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="635c5-497">If evaluation of the expression produces `null`, a `System.NullReferenceException` is thrown instead.</span></span>

<span data-ttu-id="635c5-498">Un' `throw` istruzione senza espressione può essere utilizzata solo in un `catch` blocco, nel qual caso tale istruzione genera di nuovo l'eccezione attualmente gestita da tale `catch` blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-498">A `throw` statement with no expression can be used only in a `catch` block, in which case that statement re-throws the exception that is currently being handled by that `catch` block.</span></span>

<span data-ttu-id="635c5-499">Poiché un' `throw` istruzione trasferisce in modo incondizionato il controllo altrove, il `throw` punto finale di un'istruzione non è mai raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-499">Because a `throw` statement unconditionally transfers control elsewhere, the end point of a `throw` statement is never reachable.</span></span>

<span data-ttu-id="635c5-500">Quando viene generata un'eccezione, il controllo viene trasferito alla prima `catch` clausola in un'istruzione contenitore `try` in grado di gestire l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="635c5-500">When an exception is thrown, control is transferred to the first `catch` clause in an enclosing `try` statement that can handle the exception.</span></span> <span data-ttu-id="635c5-501">Il processo che si verifica dal punto dell'eccezione generata al momento del trasferimento del controllo a un gestore di eccezioni adatto è noto come ***propagazione delle eccezioni***.</span><span class="sxs-lookup"><span data-stu-id="635c5-501">The process that takes place from the point of the exception being thrown to the point of transferring control to a suitable exception handler is known as ***exception propagation***.</span></span> <span data-ttu-id="635c5-502">La propagazione di un'eccezione è costituita dalla valutazione ripetuta dei `catch` passaggi seguenti fino a quando non viene trovata una clausola che corrisponde all'eccezione.</span><span class="sxs-lookup"><span data-stu-id="635c5-502">Propagation of an exception consists of repeatedly evaluating the following steps until a `catch` clause that matches the exception is found.</span></span> <span data-ttu-id="635c5-503">In questa descrizione, il ***punto di generazione*** è inizialmente la posizione in cui viene generata l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="635c5-503">In this description, the ***throw point*** is initially the location at which the exception is thrown.</span></span>

*  <span data-ttu-id="635c5-504">Nel membro della funzione corrente, viene `try` esaminata ogni istruzione che racchiude il punto di generazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-504">In the current function member, each `try` statement that encloses the throw point is examined.</span></span> <span data-ttu-id="635c5-505">Per ogni istruzione `S`, a partire dall'istruzione `try` più interna e terminando con `try` l'istruzione più esterna, vengono valutati i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="635c5-505">For each statement `S`, starting with the innermost `try` statement and ending with the outermost `try` statement, the following steps are evaluated:</span></span>

   * <span data-ttu-id="635c5-506">Se il `S` `catch` `catch` blocco di racchiude il punto di generazione e se S ha una o più clausole, le clausole vengono esaminate in ordine di aspetto per individuare un gestore appropriato per l'eccezione, in base alle regole specificate in `try` Sezione [espressione try](statements.md#the-try-statement).</span><span class="sxs-lookup"><span data-stu-id="635c5-506">If the `try` block of `S` encloses the throw point and if S has one or more `catch` clauses, the `catch` clauses are examined in order of appearance to locate a suitable handler for the exception, according to the rules specified in Section [The try statement](statements.md#the-try-statement).</span></span> <span data-ttu-id="635c5-507">Se viene individuata una clausola corrispondente `catch` , la propagazione delle eccezioni viene completata trasferendo il controllo al blocco `catch` della clausola.</span><span class="sxs-lookup"><span data-stu-id="635c5-507">If a matching `catch` clause is located, the exception propagation is completed by transferring control to the block of that `catch` clause.</span></span>

   * <span data-ttu-id="635c5-508">In caso contrario, `try` se il blocco `catch` o un `S` blocco di racchiude il punto di generazione e `S` se dispone `finally` di un blocco, il controllo viene `finally` trasferito nel blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-508">Otherwise, if the `try` block or a `catch` block of `S` encloses the throw point and if `S` has a `finally` block, control is transferred to the `finally` block.</span></span> <span data-ttu-id="635c5-509">Se il `finally` blocco genera un'altra eccezione, l'elaborazione dell'eccezione corrente viene terminata.</span><span class="sxs-lookup"><span data-stu-id="635c5-509">If the `finally` block throws another exception, processing of the current exception is terminated.</span></span> <span data-ttu-id="635c5-510">In caso contrario, quando il controllo raggiunge il punto `finally` finale del blocco, viene continuata l'elaborazione dell'eccezione corrente.</span><span class="sxs-lookup"><span data-stu-id="635c5-510">Otherwise, when control reaches the end point of the `finally` block, processing of the current exception is continued.</span></span>

*  <span data-ttu-id="635c5-511">Se un gestore di eccezioni non si trova nella chiamata della funzione corrente, la chiamata alla funzione viene terminata e si verifica una delle condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="635c5-511">If an exception handler was not located in the current function invocation, the function invocation is terminated, and one of the following occurs:</span></span>

   * <span data-ttu-id="635c5-512">Se la funzione corrente è non asincrona, i passaggi precedenti vengono ripetuti per il chiamante della funzione con un punto di generazione corrispondente all'istruzione da cui il membro della funzione è stato richiamato.</span><span class="sxs-lookup"><span data-stu-id="635c5-512">If the current function is non-async, the steps above are repeated for the caller of the function with a throw point corresponding to the statement from which the function member was invoked.</span></span>

   * <span data-ttu-id="635c5-513">Se la funzione corrente è asincrona e restituisce un'attività, l'eccezione viene registrata nell'attività return, che viene inserita in uno stato di errore o annullato, come descritto in [interfacce dell'enumeratore](classes.md#enumerator-interfaces).</span><span class="sxs-lookup"><span data-stu-id="635c5-513">If the current function is async and task-returning, the exception is recorded in the return task, which is put into a faulted or cancelled state as described in [Enumerator interfaces](classes.md#enumerator-interfaces).</span></span>

   * <span data-ttu-id="635c5-514">Se la funzione corrente è asincrona e restituisce un risultato null, viene notificato il contesto di sincronizzazione del thread corrente come descritto in [interfacce enumerabili](classes.md#enumerable-interfaces).</span><span class="sxs-lookup"><span data-stu-id="635c5-514">If the current function is async and void-returning, the synchronization context of the current thread is notified as described in [Enumerable interfaces](classes.md#enumerable-interfaces).</span></span>

*  <span data-ttu-id="635c5-515">Se l'elaborazione delle eccezioni termina tutte le chiamate del membro della funzione nel thread corrente, a indicare che il thread non ha alcun gestore per l'eccezione, il thread viene interrotto.</span><span class="sxs-lookup"><span data-stu-id="635c5-515">If the exception processing terminates all function member invocations in the current thread, indicating that the thread has no handler for the exception, then the thread is itself terminated.</span></span> <span data-ttu-id="635c5-516">L'effetto di tale interruzione è definito dall'implementazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-516">The impact of such termination is implementation-defined.</span></span>

## <a name="the-try-statement"></a><span data-ttu-id="635c5-517">Istruzione try</span><span class="sxs-lookup"><span data-stu-id="635c5-517">The try statement</span></span>

<span data-ttu-id="635c5-518">L' `try` istruzione fornisce un meccanismo per intercettare le eccezioni che si verificano durante l'esecuzione di un blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-518">The `try` statement provides a mechanism for catching exceptions that occur during execution of a block.</span></span> <span data-ttu-id="635c5-519">L' `try` istruzione offre inoltre la possibilità di specificare un blocco di codice che viene sempre eseguito quando il controllo lascia l' `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-519">Furthermore, the `try` statement provides the ability to specify a block of code that is always executed when control leaves the `try` statement.</span></span>

```antlr
try_statement
    : 'try' block catch_clause+
    | 'try' block finally_clause
    | 'try' block catch_clause+ finally_clause
    ;

catch_clause
    : 'catch' exception_specifier? exception_filter?  block
    ;

exception_specifier
    : '(' type identifier? ')'
    ;

exception_filter
    : 'when' '(' expression ')'
    ;

finally_clause
    : 'finally' block
    ;
```

<span data-ttu-id="635c5-520">Esistono tre possibili forme di `try` istruzioni:</span><span class="sxs-lookup"><span data-stu-id="635c5-520">There are three possible forms of `try` statements:</span></span>

*  <span data-ttu-id="635c5-521">Un `try` blocco seguito da uno o più `catch` blocchi.</span><span class="sxs-lookup"><span data-stu-id="635c5-521">A `try` block followed by one or more `catch` blocks.</span></span>
*  <span data-ttu-id="635c5-522">Un `try` blocco seguito da un `finally` blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-522">A `try` block followed by a `finally` block.</span></span>
*  <span data-ttu-id="635c5-523">Un `try` blocco seguito da uno o più `catch` blocchi seguiti da `finally` un blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-523">A `try` block followed by one or more `catch` blocks followed by a `finally` block.</span></span>

<span data-ttu-id="635c5-524">Quando una `catch` clausola specifica un *exception_specifier*, il tipo deve essere `System.Exception`, un tipo che deriva da `System.Exception` o un tipo di parametro di tipo che `System.Exception` ha (o una sottoclasse) come classe di base valida.</span><span class="sxs-lookup"><span data-stu-id="635c5-524">When a `catch` clause specifies an *exception_specifier*, the type must be `System.Exception`, a type that derives from `System.Exception` or a type parameter type that has `System.Exception` (or a subclass thereof) as its effective base class.</span></span>

<span data-ttu-id="635c5-525">Quando una `catch` clausola specifica sia un *exception_specifier* con un *identificatore*, viene dichiarata una ***variabile di eccezione*** con il nome e il tipo specificati.</span><span class="sxs-lookup"><span data-stu-id="635c5-525">When a `catch` clause specifies both an *exception_specifier* with an *identifier*, an ***exception variable*** of the given name and type is declared.</span></span> <span data-ttu-id="635c5-526">La variabile di eccezione corrisponde a una variabile locale con un ambito che si estende `catch` sulla clausola.</span><span class="sxs-lookup"><span data-stu-id="635c5-526">The exception variable corresponds to a local variable with a scope that extends over the `catch` clause.</span></span> <span data-ttu-id="635c5-527">Durante l'esecuzione del *exception_filter* e del *blocco*, la variabile di eccezione rappresenta l'eccezione attualmente gestita.</span><span class="sxs-lookup"><span data-stu-id="635c5-527">During execution of the *exception_filter* and *block*, the exception variable represents the exception currently being handled.</span></span> <span data-ttu-id="635c5-528">Ai fini del controllo di assegnazione definito, la variabile di eccezione viene considerata definitivamente assegnata nell'intero ambito.</span><span class="sxs-lookup"><span data-stu-id="635c5-528">For purposes of definite assignment checking, the exception variable is considered definitely assigned in its entire scope.</span></span>

<span data-ttu-id="635c5-529">A meno che `catch` una clausola non includa un nome di variabile di eccezione, non è possibile accedere all'oggetto eccezione `catch` nel filtro e nel blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-529">Unless a `catch` clause includes an exception variable name, it is impossible to access the exception object in the filter and `catch` block.</span></span>

<span data-ttu-id="635c5-530">Una `catch` clausola che non specifica un *exception_specifier* è detta clausola generale `catch` .</span><span class="sxs-lookup"><span data-stu-id="635c5-530">A `catch` clause that does not specify an *exception_specifier* is called a general `catch` clause.</span></span>

<span data-ttu-id="635c5-531">Alcuni linguaggi di programmazione possono supportare eccezioni che non possono essere rappresentate come un oggetto derivato `System.Exception`da, sebbene tali eccezioni non possano mai essere C# generate dal codice.</span><span class="sxs-lookup"><span data-stu-id="635c5-531">Some programming languages may support exceptions that are not representable as an object derived from `System.Exception`, although such exceptions could never be generated by C# code.</span></span> <span data-ttu-id="635c5-532">È possibile `catch` utilizzare una clausola generale per intercettare tali eccezioni.</span><span class="sxs-lookup"><span data-stu-id="635c5-532">A general `catch` clause may be used to catch such exceptions.</span></span> <span data-ttu-id="635c5-533">Pertanto, una clausola `catch` generale è semanticamente diversa da quella che specifica il tipo `System.Exception`, in quanto il primo può rilevare anche eccezioni da altri linguaggi.</span><span class="sxs-lookup"><span data-stu-id="635c5-533">Thus, a general `catch` clause is semantically different from one that specifies the type `System.Exception`, in that the former may also catch exceptions from other languages.</span></span>

<span data-ttu-id="635c5-534">Per individuare un gestore per un'eccezione, `catch` le clausole vengono esaminate in ordine lessicale.</span><span class="sxs-lookup"><span data-stu-id="635c5-534">In order to locate a handler for an exception, `catch` clauses are examined in lexical order.</span></span> <span data-ttu-id="635c5-535">Se una `catch` clausola specifica un tipo ma nessun filtro eccezioni, si tratta di un errore in fase di compilazione per `catch` una clausola successiva nella `try` stessa istruzione per specificare un tipo che è uguale a o è derivato da tale tipo.</span><span class="sxs-lookup"><span data-stu-id="635c5-535">If a `catch` clause specifies a type but no exception filter, it is a compile-time error for a later `catch` clause in the same `try` statement to specify a type that is the same as, or is derived from, that type.</span></span> <span data-ttu-id="635c5-536">Se una `catch` clausola non specifica alcun tipo e nessun filtro, deve essere l'ultima `catch` clausola `try` dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-536">If a `catch` clause specifies no type and no filter, it must be the last `catch` clause for that `try` statement.</span></span>

<span data-ttu-id="635c5-537">All'interno `catch` di un blocco `throw` , un'istruzione ([istruzione throw](statements.md#the-throw-statement)) senza espressione può essere utilizzata per generare di nuovo `catch` l'eccezione rilevata dal blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-537">Within a `catch` block, a `throw` statement ([The throw statement](statements.md#the-throw-statement)) with no expression can be used to re-throw the exception that was caught by the `catch` block.</span></span> <span data-ttu-id="635c5-538">Le assegnazioni a una variabile di eccezione non modificano l'eccezione generata nuovamente.</span><span class="sxs-lookup"><span data-stu-id="635c5-538">Assignments to an exception variable do not alter the exception that is re-thrown.</span></span>

<span data-ttu-id="635c5-539">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="635c5-539">In the example</span></span>
```csharp
using System;

class Test
{
    static void F() {
        try {
            G();
        }
        catch (Exception e) {
            Console.WriteLine("Exception in F: " + e.Message);
            e = new Exception("F");
            throw;                // re-throw
        }
    }

    static void G() {
        throw new Exception("G");
    }

    static void Main() {
        try {
            F();
        }
        catch (Exception e) {
            Console.WriteLine("Exception in Main: " + e.Message);
        }
    }
}
```
<span data-ttu-id="635c5-540">il metodo `F` rileva un'eccezione, scrive alcune informazioni di diagnostica nella console, modifica la variabile di eccezione e genera nuovamente l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="635c5-540">the method `F` catches an exception, writes some diagnostic information to the console, alters the exception variable, and re-throws the exception.</span></span> <span data-ttu-id="635c5-541">L'eccezione generata di nuovo è l'eccezione originale, quindi l'output prodotto è:</span><span class="sxs-lookup"><span data-stu-id="635c5-541">The exception that is re-thrown is the original exception, so the output produced is:</span></span>
```
Exception in F: G
Exception in Main: G
```

<span data-ttu-id="635c5-542">Se è stato generato `e` il primo blocco catch anziché rigenerare l'eccezione corrente, l'output prodotto sarà il seguente:</span><span class="sxs-lookup"><span data-stu-id="635c5-542">If the first catch block had thrown `e` instead of rethrowing the current exception, the output produced would be as follows:</span></span>
```
Exception in F: G
Exception in Main: F
```

<span data-ttu-id="635c5-543">Si tratta di un errore in fase di compilazione `break`per `continue`un'istruzione `goto` , o per trasferire il controllo all' `finally` esterno di un blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-543">It is a compile-time error for a `break`, `continue`, or `goto` statement to transfer control out of a `finally` block.</span></span> <span data-ttu-id="635c5-544">Quando un' `break`istruzione `continue`, o `goto` si verifica in un `finally` blocco, la destinazione dell'istruzione deve trovarsi all'interno dello `finally` stesso blocco o in caso contrario si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-544">When a `break`, `continue`, or `goto` statement occurs in a `finally` block, the target of the statement must be within the same `finally` block, or otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="635c5-545">Si tratta di un errore in fase di compilazione `return` per un'istruzione che si `finally` verifica in un blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-545">It is a compile-time error for a `return` statement to occur in a `finally` block.</span></span>

<span data-ttu-id="635c5-546">Un' `try` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="635c5-546">A `try` statement is executed as follows:</span></span>

*  <span data-ttu-id="635c5-547">Il `try` controllo viene trasferito nel blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-547">Control is transferred to the `try` block.</span></span>
*  <span data-ttu-id="635c5-548">Quando e se il controllo raggiunge il punto finale del `try` blocco:</span><span class="sxs-lookup"><span data-stu-id="635c5-548">When and if control reaches the end point of the `try` block:</span></span>
   *  <span data-ttu-id="635c5-549">Se l' `try` istruzione ha un `finally` blocco, il `finally` blocco viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="635c5-549">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
   *  <span data-ttu-id="635c5-550">Il controllo viene trasferito al punto finale dell' `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-550">Control is transferred to the end point of the `try` statement.</span></span>

*  <span data-ttu-id="635c5-551">Se un'eccezione viene propagata all' `try` istruzione durante l'esecuzione `try` del blocco:</span><span class="sxs-lookup"><span data-stu-id="635c5-551">If an exception is propagated to the `try` statement during execution of the `try` block:</span></span>
   *  <span data-ttu-id="635c5-552">Le `catch` clausole, se presenti, vengono esaminate in ordine di aspetto per individuare un gestore appropriato per l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="635c5-552">The `catch` clauses, if any, are examined in order of appearance to locate a suitable handler for the exception.</span></span> <span data-ttu-id="635c5-553">Se una `catch` clausola non specifica un tipo o specifica il tipo di eccezione o un tipo di base del tipo di eccezione:</span><span class="sxs-lookup"><span data-stu-id="635c5-553">If a `catch` clause does not specify a type, or specifies the exception type or a base type of the exception type:</span></span>
      *  <span data-ttu-id="635c5-554">Se la `catch` clausola dichiara una variabile di eccezione, l'oggetto eccezione viene assegnato alla variabile di eccezione.</span><span class="sxs-lookup"><span data-stu-id="635c5-554">If the `catch` clause declares an exception variable, the exception object is assigned to the exception variable.</span></span>
      *  <span data-ttu-id="635c5-555">Se la `catch` clausola dichiara un filtro eccezioni, il filtro viene valutato.</span><span class="sxs-lookup"><span data-stu-id="635c5-555">If the `catch` clause declares an exception filter, the filter is evaluated.</span></span> <span data-ttu-id="635c5-556">Se restituisce `false`, la clausola catch non è una corrispondenza e la ricerca continua con le clausole successive `catch` per un gestore appropriato.</span><span class="sxs-lookup"><span data-stu-id="635c5-556">If it evaluates to `false`, the catch clause is not a match, and the search continues through any subsequent `catch` clauses for a suitable handler.</span></span>
      *  <span data-ttu-id="635c5-557">In caso contrario `catch` , la clausola viene considerata una corrispondenza e il controllo viene trasferito al `catch` blocco corrispondente.</span><span class="sxs-lookup"><span data-stu-id="635c5-557">Otherwise, the `catch` clause is considered a match, and control is transferred to the matching `catch` block.</span></span>
      *  <span data-ttu-id="635c5-558">Quando e se il controllo raggiunge il punto finale del `catch` blocco:</span><span class="sxs-lookup"><span data-stu-id="635c5-558">When and if control reaches the end point of the `catch` block:</span></span>
         * <span data-ttu-id="635c5-559">Se l' `try` istruzione ha un `finally` blocco, il `finally` blocco viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="635c5-559">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
         * <span data-ttu-id="635c5-560">Il controllo viene trasferito al punto finale dell' `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-560">Control is transferred to the end point of the `try` statement.</span></span>
      *  <span data-ttu-id="635c5-561">Se un'eccezione viene propagata all' `try` istruzione durante l'esecuzione `catch` del blocco:</span><span class="sxs-lookup"><span data-stu-id="635c5-561">If an exception is propagated to the `try` statement during execution of the `catch` block:</span></span>
         *  <span data-ttu-id="635c5-562">Se l' `try` istruzione ha un `finally` blocco, il `finally` blocco viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="635c5-562">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
         *  <span data-ttu-id="635c5-563">L'eccezione viene propagata alla successiva istruzione di `try` inclusione.</span><span class="sxs-lookup"><span data-stu-id="635c5-563">The exception is propagated to the next enclosing `try` statement.</span></span>
   *  <span data-ttu-id="635c5-564">Se l' `try` istruzione non `catch` ha clausole o se nessuna `catch` clausola corrisponde all'eccezione:</span><span class="sxs-lookup"><span data-stu-id="635c5-564">If the `try` statement has no `catch` clauses or if no `catch` clause matches the exception:</span></span>
      *  <span data-ttu-id="635c5-565">Se l' `try` istruzione ha un `finally` blocco, il `finally` blocco viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="635c5-565">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
      *  <span data-ttu-id="635c5-566">L'eccezione viene propagata alla successiva istruzione di `try` inclusione.</span><span class="sxs-lookup"><span data-stu-id="635c5-566">The exception is propagated to the next enclosing `try` statement.</span></span>

<span data-ttu-id="635c5-567">Le istruzioni di un `finally` blocco vengono sempre eseguite quando il controllo lascia `try` un'istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-567">The statements of a `finally` block are always executed when control leaves a `try` statement.</span></span> <span data-ttu-id="635c5-568">Questo vale se il trasferimento del controllo si verifica in seguito all'esecuzione normale, in seguito `break`all'esecuzione di un'istruzione, `continue`, `goto`o o `return` a seguito della propagazione di `try` un'eccezione dal istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-568">This is true whether the control transfer occurs as a result of normal execution, as a result of executing a `break`, `continue`, `goto`, or `return` statement, or as a result of propagating an exception out of the `try` statement.</span></span>

<span data-ttu-id="635c5-569">Se viene generata un'eccezione durante l'esecuzione di `finally` un blocco e non viene rilevata all'interno dello stesso blocco finally, l'eccezione viene propagata `try` alla successiva istruzione di inclusione.</span><span class="sxs-lookup"><span data-stu-id="635c5-569">If an exception is thrown during execution of a `finally` block, and is not caught within the same finally block, the exception is propagated to the next enclosing `try` statement.</span></span> <span data-ttu-id="635c5-570">Se è in corso la propagazione di un'altra eccezione, l'eccezione viene persa.</span><span class="sxs-lookup"><span data-stu-id="635c5-570">If another exception was in the process of being propagated, that exception is lost.</span></span> <span data-ttu-id="635c5-571">Il processo di propagazione di un'eccezione viene discusso ulteriormente nella descrizione dell' `throw` istruzione ([istruzione throw](statements.md#the-throw-statement)).</span><span class="sxs-lookup"><span data-stu-id="635c5-571">The process of propagating an exception is discussed further in the description of the `throw` statement ([The throw statement](statements.md#the-throw-statement)).</span></span>

<span data-ttu-id="635c5-572">Il `try` blocco di un' `try` istruzione è raggiungibile se l' `try` istruzione è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-572">The `try` block of a `try` statement is reachable if the `try` statement is reachable.</span></span>

<span data-ttu-id="635c5-573">Un `catch` blocco di un' `try` istruzione è raggiungibile se l' `try` istruzione è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-573">A `catch` block of a `try` statement is reachable if the `try` statement is reachable.</span></span>

<span data-ttu-id="635c5-574">Il `finally` blocco di un' `try` istruzione è raggiungibile se l' `try` istruzione è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-574">The `finally` block of a `try` statement is reachable if the `try` statement is reachable.</span></span>

<span data-ttu-id="635c5-575">Il punto finale di un' `try` istruzione è raggiungibile se si verificano entrambe le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="635c5-575">The end point of a `try` statement is reachable if both of the following are true:</span></span>

*  <span data-ttu-id="635c5-576">Il punto finale del `try` blocco è raggiungibile oppure è raggiungibile il punto finale di almeno un `catch` blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-576">The end point of the `try` block is reachable or the end point of at least one `catch` block is reachable.</span></span>
*  <span data-ttu-id="635c5-577">Se è `finally` presente un blocco, il punto finale `finally` del blocco è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-577">If a `finally` block is present, the end point of the `finally` block is reachable.</span></span>

## <a name="the-checked-and-unchecked-statements"></a><span data-ttu-id="635c5-578">Istruzioni checked e unchecked</span><span class="sxs-lookup"><span data-stu-id="635c5-578">The checked and unchecked statements</span></span>

<span data-ttu-id="635c5-579">Le `checked` istruzioni `unchecked` e vengono utilizzate per controllare il ***contesto di controllo dell'overflow*** per le conversioni e le operazioni aritmetiche di tipo integrale.</span><span class="sxs-lookup"><span data-stu-id="635c5-579">The `checked` and `unchecked` statements are used to control the ***overflow checking context*** for integral-type arithmetic operations and conversions.</span></span>

```antlr
checked_statement
    : 'checked' block
    ;

unchecked_statement
    : 'unchecked' block
    ;
```

<span data-ttu-id="635c5-580">L' `checked` istruzione fa in modo che tutte le espressioni del *blocco* vengano valutate in un contesto controllato `unchecked` e l'istruzione provochi la valutazione di tutte le espressioni del *blocco* in un contesto non verificato.</span><span class="sxs-lookup"><span data-stu-id="635c5-580">The `checked` statement causes all expressions in the *block* to be evaluated in a checked context, and the `unchecked` statement causes all expressions in the *block* to be evaluated in an unchecked context.</span></span>

<span data-ttu-id="635c5-581">Le `checked` istruzioni `unchecked` e`checked` sono esattamente equivalenti agli operatori `unchecked` and ([gli operatori checked e unchecked](expressions.md#the-checked-and-unchecked-operators)), ad eccezione del fatto che operano su blocchi anziché su espressioni.</span><span class="sxs-lookup"><span data-stu-id="635c5-581">The `checked` and `unchecked` statements are precisely equivalent to the `checked` and `unchecked` operators ([The checked and unchecked operators](expressions.md#the-checked-and-unchecked-operators)), except that they operate on blocks instead of expressions.</span></span>

## <a name="the-lock-statement"></a><span data-ttu-id="635c5-582">Istruzione lock</span><span class="sxs-lookup"><span data-stu-id="635c5-582">The lock statement</span></span>

<span data-ttu-id="635c5-583">L' `lock` istruzione ottiene il blocco di esclusione reciproca per un determinato oggetto, esegue un'istruzione e quindi rilascia il blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-583">The `lock` statement obtains the mutual-exclusion lock for a given object, executes a statement, and then releases the lock.</span></span>

```antlr
lock_statement
    : 'lock' '(' expression ')' embedded_statement
    ;
```

<span data-ttu-id="635c5-584">L'espressione di un' `lock` istruzione deve indicare un valore di un tipo noto come *reference_type*.</span><span class="sxs-lookup"><span data-stu-id="635c5-584">The expression of a `lock` statement must denote a value of a type known to be a *reference_type*.</span></span> <span data-ttu-id="635c5-585">Non viene mai eseguita alcuna conversione boxing implicita ([conversioni boxing](conversions.md#boxing-conversions)) per l'espressione di `lock` un'istruzione e pertanto si tratta di un errore in fase di compilazione perché l'espressione denoti un valore di *value_type*.</span><span class="sxs-lookup"><span data-stu-id="635c5-585">No implicit boxing conversion ([Boxing conversions](conversions.md#boxing-conversions)) is ever performed for the expression of a `lock` statement, and thus it is a compile-time error for the expression to denote a value of a *value_type*.</span></span>

<span data-ttu-id="635c5-586">Un' `lock` istruzione nel formato</span><span class="sxs-lookup"><span data-stu-id="635c5-586">A `lock` statement of the form</span></span>
```csharp
lock (x) ...
```
<span data-ttu-id="635c5-587">dove `x` è un'espressione di un *reference_type*, è esattamente equivalente a</span><span class="sxs-lookup"><span data-stu-id="635c5-587">where `x` is an expression of a *reference_type*, is precisely equivalent to</span></span>
```csharp
bool __lockWasTaken = false;
try {
    System.Threading.Monitor.Enter(x, ref __lockWasTaken);
    ...
}
finally {
    if (__lockWasTaken) System.Threading.Monitor.Exit(x);
}
```
<span data-ttu-id="635c5-588">con la differenza che `x` viene valutato una sola volta.</span><span class="sxs-lookup"><span data-stu-id="635c5-588">except that `x` is only evaluated once.</span></span>

<span data-ttu-id="635c5-589">Mentre viene mantenuto un blocco di esclusione reciproca, il codice in esecuzione nello stesso thread di esecuzione può anche ottenere e rilasciare il blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-589">While a mutual-exclusion lock is held, code executing in the same execution thread can also obtain and release the lock.</span></span> <span data-ttu-id="635c5-590">Tuttavia, il codice in esecuzione in altri thread viene bloccato dall'ottenimento del blocco fino al rilascio del blocco.</span><span class="sxs-lookup"><span data-stu-id="635c5-590">However, code executing in other threads is blocked from obtaining the lock until the lock is released.</span></span>

<span data-ttu-id="635c5-591">Non `System.Type` è consigliabile bloccare gli oggetti per sincronizzare l'accesso ai dati statici.</span><span class="sxs-lookup"><span data-stu-id="635c5-591">Locking `System.Type` objects in order to synchronize access to static data is not recommended.</span></span> <span data-ttu-id="635c5-592">Un altro codice potrebbe bloccarsi sullo stesso tipo, che può causare un deadlock.</span><span class="sxs-lookup"><span data-stu-id="635c5-592">Other code might lock on the same type, which can result in deadlock.</span></span> <span data-ttu-id="635c5-593">Un approccio migliore consiste nel sincronizzare l'accesso ai dati statici bloccando un oggetto statico privato.</span><span class="sxs-lookup"><span data-stu-id="635c5-593">A better approach is to synchronize access to static data by locking a private static object.</span></span> <span data-ttu-id="635c5-594">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="635c5-594">For example:</span></span>
```csharp
class Cache
{
    private static readonly object synchronizationObject = new object();

    public static void Add(object x) {
        lock (Cache.synchronizationObject) {
            ...
        }
    }

    public static void Remove(object x) {
        lock (Cache.synchronizationObject) {
            ...
        }
    }
}
```

## <a name="the-using-statement"></a><span data-ttu-id="635c5-595">Istruzione using</span><span class="sxs-lookup"><span data-stu-id="635c5-595">The using statement</span></span>

<span data-ttu-id="635c5-596">L' `using` istruzione ottiene una o più risorse, esegue un'istruzione e quindi Elimina la risorsa.</span><span class="sxs-lookup"><span data-stu-id="635c5-596">The `using` statement obtains one or more resources, executes a statement, and then disposes of the resource.</span></span>

```antlr
using_statement
    : 'using' '(' resource_acquisition ')' embedded_statement
    ;

resource_acquisition
    : local_variable_declaration
    | expression
    ;
```

<span data-ttu-id="635c5-597">Una ***risorsa*** è una classe o uno struct che `System.IDisposable`implementa, che include un singolo metodo senza parametri `Dispose`denominato.</span><span class="sxs-lookup"><span data-stu-id="635c5-597">A ***resource*** is a class or struct that implements `System.IDisposable`, which includes a single parameterless method named `Dispose`.</span></span> <span data-ttu-id="635c5-598">Il codice che usa una risorsa può chiamare `Dispose` per indicare che la risorsa non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="635c5-598">Code that is using a resource can call `Dispose` to indicate that the resource is no longer needed.</span></span> <span data-ttu-id="635c5-599">Se `Dispose` non viene chiamato, l'eliminazione automatica viene infine eseguita come conseguenza del Garbage Collection.</span><span class="sxs-lookup"><span data-stu-id="635c5-599">If `Dispose` is not called, then automatic disposal eventually occurs as a consequence of garbage collection.</span></span>

<span data-ttu-id="635c5-600">Se il formato di *resource_acquisition* è *local_variable_declaration* , il tipo di `dynamic` *local_variable_declaration* deve essere o un tipo che può essere convertito in modo implicito in `System.IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="635c5-600">If the form of *resource_acquisition* is *local_variable_declaration* then the type of the *local_variable_declaration* must be either `dynamic` or a type that can be implicitly converted to `System.IDisposable`.</span></span> <span data-ttu-id="635c5-601">Se il formato di *resource_acquisition* è *Expression* , l'espressione deve essere convertibile in modo `System.IDisposable`implicito in.</span><span class="sxs-lookup"><span data-stu-id="635c5-601">If the form of *resource_acquisition* is *expression* then this expression must be implicitly convertible to `System.IDisposable`.</span></span>

<span data-ttu-id="635c5-602">Le variabili locali dichiarate in un *resource_acquisition* sono di sola lettura e devono includere un inizializzatore.</span><span class="sxs-lookup"><span data-stu-id="635c5-602">Local variables declared in a *resource_acquisition* are read-only, and must include an initializer.</span></span> <span data-ttu-id="635c5-603">Si verifica un errore in fase di compilazione se l'istruzione incorporata tenta di modificare queste variabili locali (tramite `++` assegnazione `--` o gli operatori e), di prenderne l'indirizzo o di `ref` passarle come parametri o `out` .</span><span class="sxs-lookup"><span data-stu-id="635c5-603">A compile-time error occurs if the embedded statement attempts to modify these local variables (via assignment or the `++` and `--` operators) , take the address of them, or pass them as `ref` or `out` parameters.</span></span>

<span data-ttu-id="635c5-604">Un' `using` istruzione viene convertita in tre parti: acquisizione, utilizzo e eliminazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-604">A `using` statement is translated into three parts: acquisition, usage, and disposal.</span></span> <span data-ttu-id="635c5-605">L'utilizzo della risorsa viene racchiuso in modo implicito in un' `try` istruzione che include una `finally` clausola.</span><span class="sxs-lookup"><span data-stu-id="635c5-605">Usage of the resource is implicitly enclosed in a `try` statement that includes a `finally` clause.</span></span> <span data-ttu-id="635c5-606">Questa `finally` clausola Elimina la risorsa.</span><span class="sxs-lookup"><span data-stu-id="635c5-606">This `finally` clause disposes of the resource.</span></span> <span data-ttu-id="635c5-607">Se viene `null` acquisita una risorsa, non viene effettuata `Dispose` alcuna chiamata a e non viene generata alcuna eccezione.</span><span class="sxs-lookup"><span data-stu-id="635c5-607">If a `null` resource is acquired, then no call to `Dispose` is made, and no exception is thrown.</span></span> <span data-ttu-id="635c5-608">Se la risorsa è di tipo `dynamic` , viene convertita dinamicamente tramite una conversione dinamica implicita ([conversioni dinamiche implicite](conversions.md#implicit-dynamic-conversions)) `IDisposable` in durante l'acquisizione, in modo da garantire che la conversione venga eseguita prima dell'utilizzo e eliminazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-608">If the resource is of type `dynamic` it is dynamically converted through an implicit dynamic conversion ([Implicit dynamic conversions](conversions.md#implicit-dynamic-conversions)) to `IDisposable` during acquisition in order to ensure that the conversion is successful before the usage and disposal.</span></span>

<span data-ttu-id="635c5-609">Un' `using` istruzione nel formato</span><span class="sxs-lookup"><span data-stu-id="635c5-609">A `using` statement of the form</span></span>
```csharp
using (ResourceType resource = expression) statement
```
<span data-ttu-id="635c5-610">corrisponde a una delle tre espansioni possibili.</span><span class="sxs-lookup"><span data-stu-id="635c5-610">corresponds to one of three possible expansions.</span></span> <span data-ttu-id="635c5-611">Quando `ResourceType` è un tipo di valore non nullable, l'espansione è</span><span class="sxs-lookup"><span data-stu-id="635c5-611">When `ResourceType` is a non-nullable value type, the expansion is</span></span>
```csharp
{
    ResourceType resource = expression;
    try {
        statement;
    }
    finally {
        ((IDisposable)resource).Dispose();
    }
}
```

<span data-ttu-id="635c5-612">In caso contrario `ResourceType` , quando è un tipo di valore nullable o un tipo `dynamic`di riferimento diverso da, l'espansione è</span><span class="sxs-lookup"><span data-stu-id="635c5-612">Otherwise, when `ResourceType` is a nullable value type or a reference type other than `dynamic`, the expansion is</span></span>
```csharp
{
    ResourceType resource = expression;
    try {
        statement;
    }
    finally {
        if (resource != null) ((IDisposable)resource).Dispose();
    }
}
```

<span data-ttu-id="635c5-613">In caso contrario `ResourceType` , `dynamic`quando è, l'espansione è</span><span class="sxs-lookup"><span data-stu-id="635c5-613">Otherwise, when `ResourceType` is `dynamic`, the expansion is</span></span>
```csharp
{
    ResourceType resource = expression;
    IDisposable d = (IDisposable)resource;
    try {
        statement;
    }
    finally {
        if (d != null) d.Dispose();
    }
}
```

<span data-ttu-id="635c5-614">In una delle due espansioni `resource` , la variabile è di sola lettura nell'istruzione incorporata e `d` la variabile è inaccessibile in e invisibile a, l'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="635c5-614">In either expansion, the `resource` variable is read-only in the embedded statement, and the `d` variable is inaccessible in, and invisible to, the embedded statement.</span></span>

<span data-ttu-id="635c5-615">Un'implementazione di può implementare una determinata istruzione using in modo diverso, ad esempio per motivi di prestazioni, purché il comportamento sia coerente con l'espansione precedente.</span><span class="sxs-lookup"><span data-stu-id="635c5-615">An implementation is permitted to implement a given using-statement differently, e.g. for performance reasons, as long as the behavior is consistent with the above expansion.</span></span>

<span data-ttu-id="635c5-616">Un' `using` istruzione nel formato</span><span class="sxs-lookup"><span data-stu-id="635c5-616">A `using` statement of the form</span></span>
```csharp
using (expression) statement
```
<span data-ttu-id="635c5-617">presenta le stesse tre espansioni possibili.</span><span class="sxs-lookup"><span data-stu-id="635c5-617">has the same three possible expansions.</span></span> <span data-ttu-id="635c5-618">In questo caso `ResourceType` è implicitamente il tipo in fase `expression`di compilazione di, se presente.</span><span class="sxs-lookup"><span data-stu-id="635c5-618">In this case `ResourceType` is implicitly the compile-time type of the `expression`, if it has one.</span></span> <span data-ttu-id="635c5-619">In caso contrario `IDisposable` `ResourceType`, l'interfaccia stessa viene utilizzata come.</span><span class="sxs-lookup"><span data-stu-id="635c5-619">Otherwise the interface `IDisposable` itself is used as the `ResourceType`.</span></span> <span data-ttu-id="635c5-620">La `resource` variabile è inaccessibile in e invisibile a, l'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="635c5-620">The `resource` variable is inaccessible in, and invisible to, the embedded statement.</span></span>

<span data-ttu-id="635c5-621">Quando un *resource_acquisition* assume il formato di un *local_variable_declaration*, è possibile acquisire più risorse di un determinato tipo.</span><span class="sxs-lookup"><span data-stu-id="635c5-621">When a *resource_acquisition* takes the form of a *local_variable_declaration*, it is possible to acquire multiple resources of a given type.</span></span> <span data-ttu-id="635c5-622">Un' `using` istruzione nel formato</span><span class="sxs-lookup"><span data-stu-id="635c5-622">A `using` statement of the form</span></span>
```csharp
using (ResourceType r1 = e1, r2 = e2, ..., rN = eN) statement
```
<span data-ttu-id="635c5-623">è esattamente equivalente a una sequenza di istruzioni `using` nidificate:</span><span class="sxs-lookup"><span data-stu-id="635c5-623">is precisely equivalent to a sequence of nested `using` statements:</span></span>
```csharp
using (ResourceType r1 = e1)
    using (ResourceType r2 = e2)
        ...
            using (ResourceType rN = eN)
                statement
```

<span data-ttu-id="635c5-624">Nell'esempio seguente viene creato un file `log.txt` denominato e vengono scritte due righe di testo nel file.</span><span class="sxs-lookup"><span data-stu-id="635c5-624">The example below creates a file named `log.txt` and writes two lines of text to the file.</span></span> <span data-ttu-id="635c5-625">Nell'esempio viene quindi aperto lo stesso file per la lettura e la copia delle righe di testo contenute nella console.</span><span class="sxs-lookup"><span data-stu-id="635c5-625">The example then opens that same file for reading and copies the contained lines of text to the console.</span></span>
```csharp
using System;
using System.IO;

class Test
{
    static void Main() {
        using (TextWriter w = File.CreateText("log.txt")) {
            w.WriteLine("This is line one");
            w.WriteLine("This is line two");
        }

        using (TextReader r = File.OpenText("log.txt")) {
            string s;
            while ((s = r.ReadLine()) != null) {
                Console.WriteLine(s);
            }

        }
    }
}
```

<span data-ttu-id="635c5-626">Poiché le classi `TextReader`eimplementano l' `IDisposable` interfaccia, l'esempio può `using` utilizzare le istruzioni per garantire che il file sottostante venga chiuso correttamente dopo le operazioni di scrittura o lettura. `TextWriter`</span><span class="sxs-lookup"><span data-stu-id="635c5-626">Since the `TextWriter` and `TextReader` classes implement the `IDisposable` interface, the example can use `using` statements to ensure that the underlying file is properly closed following the write or read operations.</span></span>

## <a name="the-yield-statement"></a><span data-ttu-id="635c5-627">Istruzione yield</span><span class="sxs-lookup"><span data-stu-id="635c5-627">The yield statement</span></span>

<span data-ttu-id="635c5-628">L' `yield` istruzione viene utilizzata in un blocco iteratore ([blocchi](statements.md#blocks)) per restituire un valore all'oggetto enumeratore ([oggetti enumeratore](classes.md#enumerator-objects)) o a un oggetto enumerabile ([oggetti enumerabile](classes.md#enumerable-objects)) di un iteratore o per segnalare la fine dell'iterazione.</span><span class="sxs-lookup"><span data-stu-id="635c5-628">The `yield` statement is used in an iterator block ([Blocks](statements.md#blocks)) to yield a value to the enumerator object ([Enumerator objects](classes.md#enumerator-objects)) or enumerable object ([Enumerable objects](classes.md#enumerable-objects)) of an iterator or to signal the end of the iteration.</span></span>

```antlr
yield_statement
    : 'yield' 'return' expression ';'
    | 'yield' 'break' ';'
    ;
```

<span data-ttu-id="635c5-629">`yield`non è una parola riservata. ha un significato speciale solo quando viene usato immediatamente prima `return` di `break` una parola chiave o.</span><span class="sxs-lookup"><span data-stu-id="635c5-629">`yield` is not a reserved word; it has special meaning only when used immediately before a `return` or `break` keyword.</span></span> <span data-ttu-id="635c5-630">In altri contesti, `yield` può essere usato come identificatore.</span><span class="sxs-lookup"><span data-stu-id="635c5-630">In other contexts, `yield` can be used as an identifier.</span></span>

<span data-ttu-id="635c5-631">Esistono diverse restrizioni per la posizione in `yield` cui può essere visualizzata un'istruzione, come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="635c5-631">There are several restrictions on where a `yield` statement can appear, as described in the following.</span></span>

*  <span data-ttu-id="635c5-632">Si tratta di un errore in fase di compilazione `yield` per un'istruzione (di uno dei due form) che viene visualizzata all'esterno di *method_body*, *operator_body* o *accessor_body*</span><span class="sxs-lookup"><span data-stu-id="635c5-632">It is a compile-time error for a `yield` statement (of either form) to appear outside a *method_body*, *operator_body* or *accessor_body*</span></span>
*  <span data-ttu-id="635c5-633">Si tratta di un errore in fase di compilazione `yield` per l'inclusione di un'istruzione (di uno dei due form) all'interno di una funzione anonima.</span><span class="sxs-lookup"><span data-stu-id="635c5-633">It is a compile-time error for a `yield` statement (of either form) to appear inside an anonymous function.</span></span>
*  <span data-ttu-id="635c5-634">Si tratta di un errore in fase di compilazione `yield` per l' `finally` inclusione di un'istruzione (di uno dei due form `try` ) nella clausola di un'istruzione.</span><span class="sxs-lookup"><span data-stu-id="635c5-634">It is a compile-time error for a `yield` statement (of either form) to appear in the `finally` clause of a `try` statement.</span></span>
*  <span data-ttu-id="635c5-635">Si tratta di un errore in fase di compilazione `yield return` per un'istruzione che viene visualizzata `try` in un punto qualsiasi `catch` di un'istruzione che contiene clausole.</span><span class="sxs-lookup"><span data-stu-id="635c5-635">It is a compile-time error for a `yield return` statement to appear anywhere in a `try` statement that contains any `catch` clauses.</span></span>

<span data-ttu-id="635c5-636">Nell'esempio seguente vengono illustrati alcuni usi validi e `yield` non validi delle istruzioni.</span><span class="sxs-lookup"><span data-stu-id="635c5-636">The following example shows some valid and invalid uses of `yield` statements.</span></span>

```csharp
delegate IEnumerable<int> D();

IEnumerator<int> GetEnumerator() {
    try {
        yield return 1;        // Ok
        yield break;           // Ok
    }
    finally {
        yield return 2;        // Error, yield in finally
        yield break;           // Error, yield in finally
    }

    try {
        yield return 3;        // Error, yield return in try...catch
        yield break;           // Ok
    }
    catch {
        yield return 4;        // Error, yield return in try...catch
        yield break;           // Ok
    }

    D d = delegate { 
        yield return 5;        // Error, yield in an anonymous function
    }; 
}

int MyMethod() {
    yield return 1;            // Error, wrong return type for an iterator block
}
```

<span data-ttu-id="635c5-637">Una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) deve esistere dal tipo dell'espressione nell' `yield return` istruzione al tipo yield ([tipo](classes.md#yield-type)di risultato) dell'iteratore.</span><span class="sxs-lookup"><span data-stu-id="635c5-637">An implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) must exist from the type of the expression in the `yield return` statement to the yield type ([Yield type](classes.md#yield-type)) of the iterator.</span></span>

<span data-ttu-id="635c5-638">Un' `yield return` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="635c5-638">A `yield return` statement is executed as follows:</span></span>

*  <span data-ttu-id="635c5-639">L'espressione specificata nell'istruzione viene valutata, convertita in modo implicito nel tipo yield e assegnata `Current` alla proprietà dell'oggetto enumeratore.</span><span class="sxs-lookup"><span data-stu-id="635c5-639">The expression given in the statement is evaluated, implicitly converted to the yield type, and assigned to the `Current` property of the enumerator object.</span></span>
*  <span data-ttu-id="635c5-640">L'esecuzione del blocco iteratore è sospesa.</span><span class="sxs-lookup"><span data-stu-id="635c5-640">Execution of the iterator block is suspended.</span></span> <span data-ttu-id="635c5-641">Se l' `yield return` istruzione si trova all'interno di `try` uno o più blocchi `finally` , i blocchi associati non vengono eseguiti in questo momento.</span><span class="sxs-lookup"><span data-stu-id="635c5-641">If the `yield return` statement is within one or more `try` blocks, the associated `finally` blocks are not executed at this time.</span></span>
*  <span data-ttu-id="635c5-642">Il `MoveNext` metodo dell'oggetto enumeratore viene `true` restituito al chiamante, a indicare che l'oggetto enumeratore è stato spostato correttamente sull'elemento successivo.</span><span class="sxs-lookup"><span data-stu-id="635c5-642">The `MoveNext` method of the enumerator object returns `true` to its caller, indicating that the enumerator object successfully advanced to the next item.</span></span>

<span data-ttu-id="635c5-643">La chiamata successiva al `MoveNext` metodo dell'oggetto enumeratore riprende l'esecuzione del blocco iteratore dal punto in cui è stata sospesa l'ultima volta.</span><span class="sxs-lookup"><span data-stu-id="635c5-643">The next call to the enumerator object's `MoveNext` method resumes execution of the iterator block from where it was last suspended.</span></span>

<span data-ttu-id="635c5-644">Un' `yield break` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="635c5-644">A `yield break` statement is executed as follows:</span></span>

*  <span data-ttu-id="635c5-645">Se l' `yield break` istruzione è racchiusa tra uno o `try` più blocchi con `finally` `finally` blocchi associati, il controllo viene inizialmente trasferito al blocco dell'istruzione `try` più interna.</span><span class="sxs-lookup"><span data-stu-id="635c5-645">If the `yield break` statement is enclosed by one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="635c5-646">Quando e se il controllo raggiunge il punto finale di `finally` un blocco, il `finally` controllo viene trasferito al blocco della successiva istruzione di `try` inclusione.</span><span class="sxs-lookup"><span data-stu-id="635c5-646">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="635c5-647">Questo processo viene ripetuto fino a `finally` quando non vengono eseguiti i `try` blocchi di tutte le istruzioni di inclusione.</span><span class="sxs-lookup"><span data-stu-id="635c5-647">This process is repeated until the `finally` blocks of all enclosing `try` statements have been executed.</span></span>
*  <span data-ttu-id="635c5-648">Il controllo viene restituito al chiamante del blocco iteratore.</span><span class="sxs-lookup"><span data-stu-id="635c5-648">Control is returned to the caller of the iterator block.</span></span> <span data-ttu-id="635c5-649">Si tratta del `MoveNext` metodo o `Dispose` del metodo dell'oggetto enumeratore.</span><span class="sxs-lookup"><span data-stu-id="635c5-649">This is either the `MoveNext` method or `Dispose` method of the enumerator object.</span></span>

<span data-ttu-id="635c5-650">Poiché un' `yield break` istruzione trasferisce in modo incondizionato il controllo altrove, il `yield break` punto finale di un'istruzione non è mai raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="635c5-650">Because a `yield break` statement unconditionally transfers control elsewhere, the end point of a `yield break` statement is never reachable.</span></span>
