---
ms.openlocfilehash: 8f9551b9e7f70379836c23a60f0d37dc02f8e18e
ms.sourcegitcommit: 94a3d151c438d34ede1d99de9eb4ebdc07ba4699
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/25/2019
ms.locfileid: "64488819"
---
# <a name="statements"></a><span data-ttu-id="9ab6d-101">Istruzioni</span><span class="sxs-lookup"><span data-stu-id="9ab6d-101">Statements</span></span>

<span data-ttu-id="9ab6d-102">C# offre un'ampia gamma di istruzioni.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-102">C# provides a variety of statements.</span></span> <span data-ttu-id="9ab6d-103">La maggior parte di queste istruzioni saranno nota agli sviluppatori che conoscono in C e C++.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-103">Most of these statements will be familiar to developers who have programmed in C and C++.</span></span>

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

<span data-ttu-id="9ab6d-104">Il *embedded_statement* non terminale viene usato per le istruzioni presenti in altre istruzioni.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-104">The *embedded_statement* nonterminal is used for statements that appear within other statements.</span></span> <span data-ttu-id="9ab6d-105">L'uso di *embedded_statement* invece *istruzione* esclude l'uso di istruzioni di dichiarazione e istruzioni con etichetta in questi contesti.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-105">The use of *embedded_statement* rather than *statement* excludes the use of declaration statements and labeled statements in these contexts.</span></span> <span data-ttu-id="9ab6d-106">L'esempio</span><span class="sxs-lookup"><span data-stu-id="9ab6d-106">The example</span></span>
```csharp
void F(bool b) {
    if (b)
        int i = 44;
}
```
<span data-ttu-id="9ab6d-107">Genera un errore in fase di compilazione perché un `if` istruzione richiede un *embedded_statement* anziché un *istruzione* per relativo se ramo.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-107">results in a compile-time error because an `if` statement requires an *embedded_statement* rather than a *statement* for its if branch.</span></span> <span data-ttu-id="9ab6d-108">Se questo fosse consentito codice, quindi la variabile `i` sarebbe dichiarata, ma non è stato mai utilizzato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-108">If this code were permitted, then the variable `i` would be declared, but it could never be used.</span></span> <span data-ttu-id="9ab6d-109">Si noti tuttavia che, inserendo `i`della dichiarazione in un blocco, l'esempio è valido.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-109">Note, however, that by placing `i`'s declaration in a block, the example is valid.</span></span>

## <a name="end-points-and-reachability"></a><span data-ttu-id="9ab6d-110">Endpoint e raggiungibilità</span><span class="sxs-lookup"><span data-stu-id="9ab6d-110">End points and reachability</span></span>

<span data-ttu-id="9ab6d-111">Ogni istruzione è un' ***punto di fine***.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-111">Every statement has an ***end point***.</span></span> <span data-ttu-id="9ab6d-112">In pratica, il punto finale di un'istruzione è la posizione immediatamente successiva l'istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-112">In intuitive terms, the end point of a statement is the location that immediately follows the statement.</span></span> <span data-ttu-id="9ab6d-113">Le regole di esecuzione delle istruzioni composte (istruzioni che contengono istruzioni incorporate) specificano l'azione da eseguita quando il controllo raggiunge il punto finale di un'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-113">The execution rules for composite statements (statements that contain embedded statements) specify the action that is taken when control reaches the end point of an embedded statement.</span></span> <span data-ttu-id="9ab6d-114">Ad esempio, quando il controllo raggiunge il punto finale di un'istruzione in un blocco, il controllo viene trasferito all'istruzione successiva nel blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-114">For example, when control reaches the end point of a statement in a block, control is transferred to the next statement in the block.</span></span>

<span data-ttu-id="9ab6d-115">Se un'istruzione che può essere raggiunta in esecuzione, l'istruzione viene detto ***raggiungibile***.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-115">If a statement can possibly be reached by execution, the statement is said to be ***reachable***.</span></span> <span data-ttu-id="9ab6d-116">Per contro, se non è possibile che venga eseguita un'istruzione, l'istruzione è detta ***raggiungibile***.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-116">Conversely, if there is no possibility that a statement will be executed, the statement is said to be ***unreachable***.</span></span>

<span data-ttu-id="9ab6d-117">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="9ab6d-117">In the example</span></span>
```csharp
void F() {
    Console.WriteLine("reachable");
    goto Label;
    Console.WriteLine("unreachable");
    Label:
    Console.WriteLine("reachable");
}
```
<span data-ttu-id="9ab6d-118">la seconda chiamata di `Console.WriteLine` non è raggiungibile perché non è possibile che l'istruzione viene eseguita.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-118">the second invocation of `Console.WriteLine` is unreachable because there is no possibility that the statement will be executed.</span></span>

<span data-ttu-id="9ab6d-119">Un avviso viene segnalato se il compilatore determina che un'istruzione non è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-119">A warning is reported if the compiler determines that a statement is unreachable.</span></span> <span data-ttu-id="9ab6d-120">Si tratta in particolare non un errore per un'istruzione sia raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-120">It is specifically not an error for a statement to be unreachable.</span></span>

<span data-ttu-id="9ab6d-121">Per determinare se una determinata istruzione o il punto finale è raggiungibile, il compilatore esegue l'analisi di flusso in base alle regole raggiungibilità definiti per ogni istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-121">To determine whether a particular statement or end point is reachable, the compiler performs flow analysis according to the reachability rules defined for each statement.</span></span> <span data-ttu-id="9ab6d-122">L'analisi di flusso prende in considerazione i valori delle espressioni costanti ([espressioni costanti](expressions.md#constant-expressions)) che controllano il comportamento delle istruzioni, ma i valori possibili di espressioni non costanti non vengono considerati.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-122">The flow analysis takes into account the values of constant expressions ([Constant expressions](expressions.md#constant-expressions)) that control the behavior of statements, but the possible values of non-constant expressions are not considered.</span></span> <span data-ttu-id="9ab6d-123">In altre parole, per scopi di analisi di flusso di controllo, un'espressione non costante di un determinato tipo viene considerata per avere qualsiasi valore di quel tipo.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-123">In other words, for purposes of control flow analysis, a non-constant expression of a given type is considered to have any possible value of that type.</span></span>

<span data-ttu-id="9ab6d-124">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="9ab6d-124">In the example</span></span>
```csharp
void F() {
    const int i = 1;
    if (i == 2) Console.WriteLine("unreachable");
}
```
<span data-ttu-id="9ab6d-125">l'espressione booleana del `if` istruzione è un'espressione costante perché entrambi gli operandi del `==` operatore sono costanti.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-125">the boolean expression of the `if` statement is a constant expression because both operands of the `==` operator are constants.</span></span> <span data-ttu-id="9ab6d-126">Poiché l'espressione costante viene valutata in fase di compilazione, che restituisce il valore `false`, il `Console.WriteLine` chiamata viene considerata non raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-126">As the constant expression is evaluated at compile-time, producing the value `false`, the `Console.WriteLine` invocation is considered unreachable.</span></span> <span data-ttu-id="9ab6d-127">Tuttavia, se `i` viene modificato per essere una variabile locale</span><span class="sxs-lookup"><span data-stu-id="9ab6d-127">However, if `i` is changed to be a local variable</span></span>
```csharp
void F() {
    int i = 1;
    if (i == 2) Console.WriteLine("reachable");
}
```
<span data-ttu-id="9ab6d-128">il `Console.WriteLine` chiamata viene considerata raggiungibile, anche se, in realtà non verrà mai eseguito.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-128">the `Console.WriteLine` invocation is considered reachable, even though, in reality, it will never be executed.</span></span>

<span data-ttu-id="9ab6d-129">Il *blocco* di una funzione membro viene sempre considerato raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-129">The *block* of a function member is always considered reachable.</span></span> <span data-ttu-id="9ab6d-130">Per la valutazione in successione la raggiungibilità delle regole di ogni istruzione in un blocco, è possibile determinare la raggiungibilità di qualsiasi istruzione specificata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-130">By successively evaluating the reachability rules of each statement in a block, the reachability of any given statement can be determined.</span></span>

<span data-ttu-id="9ab6d-131">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="9ab6d-131">In the example</span></span>
```csharp
void F(int x) {
    Console.WriteLine("start");
    if (x < 0) Console.WriteLine("negative");
}
```
<span data-ttu-id="9ab6d-132">il problema relativo alla raggiungibilità del secondo `Console.WriteLine` viene determinata come segue:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-132">the reachability of the second `Console.WriteLine` is determined as follows:</span></span>

*  <span data-ttu-id="9ab6d-133">Il primo `Console.WriteLine` istruzione di espressione perché il blocco del `F` metodo sia raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-133">The first `Console.WriteLine` expression statement is reachable because the block of the `F` method is reachable.</span></span>
*  <span data-ttu-id="9ab6d-134">Il punto finale del primo `Console.WriteLine` istruzione di espressione è raggiungibile perché tale istruzione è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-134">The end point of the first `Console.WriteLine` expression statement is reachable because that statement is reachable.</span></span>
*  <span data-ttu-id="9ab6d-135">Il `if` istruzione non è raggiungibile perché il punto di fine del primo `Console.WriteLine` istruzione di espressione è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-135">The `if` statement is reachable because the end point of the first `Console.WriteLine` expression statement is reachable.</span></span>
*  <span data-ttu-id="9ab6d-136">La seconda `Console.WriteLine` istruzione di espressione perché l'espressione booleana del `if` istruzione non ha il valore costante `false`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-136">The second `Console.WriteLine` expression statement is reachable because the boolean expression of the `if` statement does not have the constant value `false`.</span></span>

<span data-ttu-id="9ab6d-137">Esistono due casi in cui è un errore in fase di compilazione per il punto finale di un'istruzione sia raggiungibile:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-137">There are two situations in which it is a compile-time error for the end point of a statement to be reachable:</span></span>

*  <span data-ttu-id="9ab6d-138">Poiché il `switch` istruzione non consente una sezione di switch "giungano" alla sezione switch successiva, è un errore in fase di compilazione per il punto di fine dell'elenco di istruzioni di una sezione di switch sia raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-138">Because the `switch` statement does not permit a switch section to "fall through" to the next switch section, it is a compile-time error for the end point of the statement list of a switch section to be reachable.</span></span> <span data-ttu-id="9ab6d-139">Se si verifica questo errore, è in genere un valore che indica che un `break` istruzione non è presente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-139">If this error occurs, it is typically an indication that a `break` statement is missing.</span></span>
*  <span data-ttu-id="9ab6d-140">È un errore in fase di compilazione per il punto finale del blocco di un membro di funzione che calcola un valore di essere raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-140">It is a compile-time error for the end point of the block of a function member that computes a value to be reachable.</span></span> <span data-ttu-id="9ab6d-141">Se si verifica questo errore, in genere è un valore che indica che un `return` istruzione non è presente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-141">If this error occurs, it typically is an indication that a `return` statement is missing.</span></span>

## <a name="blocks"></a><span data-ttu-id="9ab6d-142">Blocchi</span><span class="sxs-lookup"><span data-stu-id="9ab6d-142">Blocks</span></span>

<span data-ttu-id="9ab6d-143">Un *blocco* consente di scrivere più istruzioni nei contesti in cui ne è consentita una sola.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-143">A *block* permits multiple statements to be written in contexts where a single statement is allowed.</span></span>

```antlr
block
    : '{' statement_list? '}'
    ;
```

<span data-ttu-id="9ab6d-144">Oggetto *block* costituito facoltativo *statement_list* ([istruzione Elenca](statements.md#statement-lists)), racchiuso tra parentesi graffe.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-144">A *block* consists of an optional *statement_list* ([Statement lists](statements.md#statement-lists)), enclosed in braces.</span></span> <span data-ttu-id="9ab6d-145">Se l'elenco di istruzioni viene omesso, si dice che il blocco può essere vuoto.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-145">If the statement list is omitted, the block is said to be empty.</span></span>

<span data-ttu-id="9ab6d-146">Un blocco può contenere istruzioni di dichiarazione ([istruzioni di dichiarazione](statements.md#declaration-statements)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-146">A block may contain declaration statements ([Declaration statements](statements.md#declaration-statements)).</span></span> <span data-ttu-id="9ab6d-147">L'ambito di una costante o variabile locale dichiarata in un blocco è il blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-147">The scope of a local variable or constant declared in a block is the block.</span></span>

<span data-ttu-id="9ab6d-148">Un blocco viene eseguito come segue:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-148">A block is executed as follows:</span></span>

*  <span data-ttu-id="9ab6d-149">Se il blocco è vuoto, il controllo viene trasferito al punto di fine del blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-149">If the block is empty, control is transferred to the end point of the block.</span></span>
*  <span data-ttu-id="9ab6d-150">Se il blocco non è vuoto, il controllo viene trasferito all'elenco di istruzioni.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-150">If the block is not empty, control is transferred to the statement list.</span></span> <span data-ttu-id="9ab6d-151">Se e quando il controllo raggiunge il punto di fine dell'elenco di istruzioni, il controllo viene trasferito al punto di fine del blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-151">When and if control reaches the end point of the statement list, control is transferred to the end point of the block.</span></span>

<span data-ttu-id="9ab6d-152">Nell'elenco di istruzioni di un blocco è raggiungibile se il blocco stesso è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-152">The statement list of a block is reachable if the block itself is reachable.</span></span>

<span data-ttu-id="9ab6d-153">Se il blocco è vuoto o se il punto di fine dell'elenco di istruzioni è raggiungibile, il punto finale di un blocco è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-153">The end point of a block is reachable if the block is empty or if the end point of the statement list is reachable.</span></span>

<span data-ttu-id="9ab6d-154">Oggetto *block* che contiene uno o più `yield` istruzioni ([l'istruzione yield](statements.md#the-yield-statement)) viene chiamato un blocco iteratore.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-154">A *block* that contains one or more `yield` statements ([The yield statement](statements.md#the-yield-statement)) is called an iterator block.</span></span> <span data-ttu-id="9ab6d-155">Blocchi di iteratori vengono usati per implementare i membri di funzione come gli iteratori ([iteratori](classes.md#iterators)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-155">Iterator blocks are used to implement function members as iterators ([Iterators](classes.md#iterators)).</span></span> <span data-ttu-id="9ab6d-156">Alcune restrizioni aggiuntive si applicano ai blocchi di iteratore:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-156">Some additional restrictions apply to iterator blocks:</span></span>

*  <span data-ttu-id="9ab6d-157">Tratta di un errore in fase di compilazione per un `return` istruzione venga visualizzato in un blocco iteratore (ma `yield return` istruzioni sono permesse).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-157">It is a compile-time error for a `return` statement to appear in an iterator block (but `yield return` statements are permitted).</span></span>
*  <span data-ttu-id="9ab6d-158">Tratta di un errore in fase di compilazione per un blocco iteratore contenga un contesto non sicuro ([contesti non sicuri](unsafe-code.md#unsafe-contexts)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-158">It is a compile-time error for an iterator block to contain an unsafe context ([Unsafe contexts](unsafe-code.md#unsafe-contexts)).</span></span> <span data-ttu-id="9ab6d-159">Un blocco iteratore sempre definisce un contesto di sicuro, anche quando la relativa dichiarazione è annidata in un contesto unsafe.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-159">An iterator block always defines a safe context, even when its declaration is nested in an unsafe context.</span></span>

### <a name="statement-lists"></a><span data-ttu-id="9ab6d-160">Elenchi di istruzioni</span><span class="sxs-lookup"><span data-stu-id="9ab6d-160">Statement lists</span></span>

<span data-ttu-id="9ab6d-161">Oggetto ***elenco di istruzioni*** è costituito da uno o più istruzioni scritte nella sequenza.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-161">A ***statement list*** consists of one or more statements written in sequence.</span></span> <span data-ttu-id="9ab6d-162">Gli elenchi di istruzioni si verificano nel *blocco*s ([blocchi](statements.md#blocks)) e in *switch_block*s ([istruzione switch](statements.md#the-switch-statement)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-162">Statement lists occur in *block*s ([Blocks](statements.md#blocks)) and in *switch_block*s ([The switch statement](statements.md#the-switch-statement)).</span></span>

```antlr
statement_list
    : statement+
    ;
```

<span data-ttu-id="9ab6d-163">Viene eseguito un elenco di istruzioni di trasferimento del controllo alla prima istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-163">A statement list is executed by transferring control to the first statement.</span></span> <span data-ttu-id="9ab6d-164">Se e quando il controllo raggiunge il punto finale di un'istruzione, il controllo viene trasferito all'istruzione successiva.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-164">When and if control reaches the end point of a statement, control is transferred to the next statement.</span></span> <span data-ttu-id="9ab6d-165">Se e quando il controllo raggiunge il punto finale dell'ultima istruzione, il controllo viene trasferito al punto di fine dell'elenco di istruzioni.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-165">When and if control reaches the end point of the last statement, control is transferred to the end point of the statement list.</span></span>

<span data-ttu-id="9ab6d-166">Un'istruzione in un elenco di istruzioni è raggiungibile solo se viene soddisfatta almeno una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-166">A statement in a statement list is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="9ab6d-167">L'istruzione è la prima istruzione e l'elenco di istruzioni è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-167">The statement is the first statement and the statement list itself is reachable.</span></span>
*  <span data-ttu-id="9ab6d-168">Il punto finale dell'istruzione precedente è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-168">The end point of the preceding statement is reachable.</span></span>
*  <span data-ttu-id="9ab6d-169">L'istruzione è un'istruzione con etichetta e l'etichetta fa riferimento un raggiungibile `goto` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-169">The statement is a labeled statement and the label is referenced by a reachable `goto` statement.</span></span>

<span data-ttu-id="9ab6d-170">Se il punto finale dell'ultima istruzione nell'elenco è raggiungibile, il punto finale di un elenco di istruzioni è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-170">The end point of a statement list is reachable if the end point of the last statement in the list is reachable.</span></span>

## <a name="the-empty-statement"></a><span data-ttu-id="9ab6d-171">Istruzione vuota</span><span class="sxs-lookup"><span data-stu-id="9ab6d-171">The empty statement</span></span>

<span data-ttu-id="9ab6d-172">Un' *empty_statement* non esegue alcuna operazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-172">An *empty_statement* does nothing.</span></span>

```antlr
empty_statement
    : ';'
    ;
```

<span data-ttu-id="9ab6d-173">Un'istruzione vuota viene utilizzata quando non sono presenti operazioni da eseguire in un contesto in cui è richiesta un'istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-173">An empty statement is used when there are no operations to perform in a context where a statement is required.</span></span>

<span data-ttu-id="9ab6d-174">Esecuzione di un'istruzione vuota trasferisce semplicemente il controllo al punto di fine dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-174">Execution of an empty statement simply transfers control to the end point of the statement.</span></span> <span data-ttu-id="9ab6d-175">Di conseguenza, il punto finale di un'istruzione vuota è raggiungibile se l'istruzione vuota è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-175">Thus, the end point of an empty statement is reachable if the empty statement is reachable.</span></span>

<span data-ttu-id="9ab6d-176">Un'istruzione vuota può essere utilizzata durante la scrittura di un `while` istruzione con un corpo null:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-176">An empty statement can be used when writing a `while` statement with a null body:</span></span>
```csharp
bool ProcessMessage() {...}

void ProcessMessages() {
    while (ProcessMessage())
        ;
}
```

<span data-ttu-id="9ab6d-177">Inoltre, un'istruzione vuota è utilizzabile per dichiarare un'etichetta subito prima del tag "`}`" di un blocco:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-177">Also, an empty statement can be used to declare a label just before the closing "`}`" of a block:</span></span>
```csharp
void F() {
    ...
    if (done) goto exit;
    ...
    exit: ;
}
```

## <a name="labeled-statements"></a><span data-ttu-id="9ab6d-178">Istruzioni con etichetta</span><span class="sxs-lookup"><span data-stu-id="9ab6d-178">Labeled statements</span></span>

<span data-ttu-id="9ab6d-179">Oggetto *labeled_statement* consente un'istruzione può essere preceduta da un'etichetta.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-179">A *labeled_statement* permits a statement to be prefixed by a label.</span></span> <span data-ttu-id="9ab6d-180">Istruzioni con etichetta sono consentite in blocchi, ma non sono consentite come istruzioni incorporate.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-180">Labeled statements are permitted in blocks, but are not permitted as embedded statements.</span></span>

```antlr
labeled_statement
    : identifier ':' statement
    ;
```

<span data-ttu-id="9ab6d-181">Un'istruzione con etichetta dichiara un'etichetta con il nome specificato per il *identificatore*.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-181">A labeled statement declares a label with the name given by the *identifier*.</span></span> <span data-ttu-id="9ab6d-182">L'ambito di un'etichetta è l'intero blocco in cui è dichiarata l'etichetta, inclusi eventuali blocchi annidati.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-182">The scope of a label is the whole block in which the label is declared, including any nested blocks.</span></span> <span data-ttu-id="9ab6d-183">È un errore in fase di compilazione per due etichette con il nome stesso in modo che gli ambiti sovrapposti.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-183">It is a compile-time error for two labels with the same name to have overlapping scopes.</span></span>

<span data-ttu-id="9ab6d-184">Un'etichetta è possibile fare riferimento dal `goto` istruzioni ([istruzione goto](statements.md#the-goto-statement)) all'interno dell'ambito dell'etichetta.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-184">A label can be referenced from `goto` statements ([The goto statement](statements.md#the-goto-statement)) within the scope of the label.</span></span> <span data-ttu-id="9ab6d-185">Ciò significa che `goto` istruzioni possono trasferire il controllo all'interno di blocchi e da blocchi, ma mai in blocchi.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-185">This means that `goto` statements can transfer control within blocks and out of blocks, but never into blocks.</span></span>

<span data-ttu-id="9ab6d-186">Le etichette sono proprio spazio di dichiarazione e non interferiscono con altri identificatori.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-186">Labels have their own declaration space and do not interfere with other identifiers.</span></span> <span data-ttu-id="9ab6d-187">L'esempio</span><span class="sxs-lookup"><span data-stu-id="9ab6d-187">The example</span></span>
```csharp
int F(int x) {
    if (x >= 0) goto x;
    x = -x;
    x: return x;
}
```
<span data-ttu-id="9ab6d-188">è valido e viene utilizzato il nome `x` come parametro e un'etichetta.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-188">is valid and uses the name `x` as both a parameter and a label.</span></span>

<span data-ttu-id="9ab6d-189">Esecuzione di un'istruzione con etichetta corrisponde esattamente all'esecuzione dell'istruzione che segue l'etichetta.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-189">Execution of a labeled statement corresponds exactly to execution of the statement following the label.</span></span>

<span data-ttu-id="9ab6d-190">Oltre la raggiungibilità fornita da normale flusso di controllo, un'istruzione con etichetta è raggiungibile solo se l'etichetta fa riferimento un raggiungibile `goto` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-190">In addition to the reachability provided by normal flow of control, a labeled statement is reachable if the label is referenced by a reachable `goto` statement.</span></span> <span data-ttu-id="9ab6d-191">(Eccezione: Se un `goto` istruzione si trova all'interno una `try` che include un `finally` blocco e l'istruzione con etichetta non è compreso il `try`e il punto finale del `finally` blocco non è raggiungibile, quindi l'istruzione con etichetta non è raggiungibile da che `goto` istruzione.)</span><span class="sxs-lookup"><span data-stu-id="9ab6d-191">(Exception: If a `goto` statement is inside a `try` that includes a `finally` block, and the labeled statement is outside the `try`, and the end point of the `finally` block is unreachable, then the labeled statement is not reachable from that `goto` statement.)</span></span>

## <a name="declaration-statements"></a><span data-ttu-id="9ab6d-192">Istruzioni di dichiarazione</span><span class="sxs-lookup"><span data-stu-id="9ab6d-192">Declaration statements</span></span>

<span data-ttu-id="9ab6d-193">Oggetto *declaration_statement* dichiara una variabile locale o una costante.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-193">A *declaration_statement* declares a local variable or constant.</span></span> <span data-ttu-id="9ab6d-194">Le istruzioni di dichiarazione sono consentite in blocchi, ma non sono consentite come istruzioni incorporate.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-194">Declaration statements are permitted in blocks, but are not permitted as embedded statements.</span></span>

```antlr
declaration_statement
    : local_variable_declaration ';'
    | local_constant_declaration ';'
    ;
```

### <a name="local-variable-declarations"></a><span data-ttu-id="9ab6d-195">Dichiarazioni di variabili locali</span><span class="sxs-lookup"><span data-stu-id="9ab6d-195">Local variable declarations</span></span>

<span data-ttu-id="9ab6d-196">Oggetto *local_variable_declaration* dichiara uno o più variabili locali.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-196">A *local_variable_declaration* declares one or more local variables.</span></span>

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

<span data-ttu-id="9ab6d-197">Il *local_variable_type* di un *local_variable_declaration* direttamente specifica il tipo delle variabili introdotte dalla dichiarazione, o con l'identificatore indica `var` che il tipo può essere presupposta basato su un inizializzatore.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-197">The *local_variable_type* of a *local_variable_declaration* either directly specifies the type of the variables introduced by the declaration, or indicates with the identifier `var` that the type should be inferred based on an initializer.</span></span> <span data-ttu-id="9ab6d-198">Il tipo è seguito da un elenco delle *local_variable_declarator*s, ognuno dei quali viene introdotta una nuova variabile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-198">The type is followed by a list of *local_variable_declarator*s, each of which introduces a new variable.</span></span> <span data-ttu-id="9ab6d-199">Oggetto *local_variable_declarator* costituito da un *identifier* che denomina la variabile, seguita facoltativamente da un "`=`" token e un *local_variable_initializer* che specifica il valore iniziale della variabile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-199">A *local_variable_declarator* consists of an *identifier* that names the variable, optionally followed by an "`=`" token and a *local_variable_initializer* that gives the initial value of the variable.</span></span>

<span data-ttu-id="9ab6d-200">Nel contesto di una dichiarazione di variabile locale, l'identificatore var agisce come una parola chiave contestuale ([parole chiave](lexical-structure.md#keywords)). Quando la *local_variable_type* è specificato come `var` e nessun tipo denominato `var` è incluso nell'ambito, la dichiarazione è un ***tipizzate in modo implicito la dichiarazione di variabile locale***, il cui tipo è dedotto dal tipo dell'espressione dell'inizializzatore associato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-200">In the context of a local variable declaration, the identifier var acts as a contextual keyword ([Keywords](lexical-structure.md#keywords)).When the *local_variable_type* is specified as `var` and no type named `var` is in scope, the declaration is an ***implicitly typed local variable declaration***, whose type is inferred from the type of the associated initializer expression.</span></span> <span data-ttu-id="9ab6d-201">Dichiarazioni di variabili locali tipizzate in modo implicito sono soggette alle restrizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-201">Implicitly typed local variable declarations are subject to the following restrictions:</span></span>

*  <span data-ttu-id="9ab6d-202">Il *local_variable_declaration* non può includere più *local_variable_declarator*s.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-202">The *local_variable_declaration* cannot include multiple *local_variable_declarator*s.</span></span>
*  <span data-ttu-id="9ab6d-203">Il *local_variable_declarator* deve includere un *local_variable_initializer*.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-203">The *local_variable_declarator* must include a *local_variable_initializer*.</span></span>
*  <span data-ttu-id="9ab6d-204">Il *local_variable_initializer* deve essere un' *espressione*.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-204">The *local_variable_initializer* must be an *expression*.</span></span>
*  <span data-ttu-id="9ab6d-205">L'inizializzatore *espressione* deve presentare un tipo in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-205">The initializer *expression* must have a compile-time type.</span></span>
*  <span data-ttu-id="9ab6d-206">L'inizializzatore *espressione* non può fare riferimento alla variabile dichiarata stesso</span><span class="sxs-lookup"><span data-stu-id="9ab6d-206">The initializer *expression* cannot refer to the declared variable itself</span></span>

<span data-ttu-id="9ab6d-207">Di seguito è riportati esempi di dichiarazioni di variabili locali tipizzate in modo implicito non corrette:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-207">The following are examples of incorrect implicitly typed local variable declarations:</span></span>

```csharp
var x;               // Error, no initializer to infer type from
var y = {1, 2, 3};   // Error, array initializer not permitted
var z = null;        // Error, null does not have a type
var u = x => x + 1;  // Error, anonymous functions do not have a type
var v = v++;         // Error, initializer cannot refer to variable itself
```

<span data-ttu-id="9ab6d-208">Viene ottenuto il valore di una variabile locale in un'espressione che usa un' *simple_name* ([nomi semplici](expressions.md#simple-names)), e il valore di una variabile locale viene modificato tramite un' *assegnazione* ( [Gli operatori di assegnazione](expressions.md#assignment-operators)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-208">The value of a local variable is obtained in an expression using a *simple_name* ([Simple names](expressions.md#simple-names)), and the value of a local variable is modified using an *assignment* ([Assignment operators](expressions.md#assignment-operators)).</span></span> <span data-ttu-id="9ab6d-209">Una variabile locale deve essere assegnata definitivamente ([assegnazione certa](variables.md#definite-assignment)) in ogni posizione in cui viene ottenuto il relativo valore.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-209">A local variable must be definitely assigned ([Definite assignment](variables.md#definite-assignment)) at each location where its value is obtained.</span></span>

<span data-ttu-id="9ab6d-210">L'ambito di una variabile locale dichiarata un *local_variable_declaration* è il blocco in cui si trova la dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-210">The scope of a local variable declared in a *local_variable_declaration* is the block in which the declaration occurs.</span></span> <span data-ttu-id="9ab6d-211">Si tratta di un errore per fare riferimento a una variabile locale in una posizione di testo che precede il *local_variable_declarator* della variabile locale.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-211">It is an error to refer to a local variable in a textual position that precedes the *local_variable_declarator* of the local variable.</span></span> <span data-ttu-id="9ab6d-212">Nell'ambito di una variabile locale, è un errore in fase di compilazione per dichiarare local un'altra variabile o costante con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-212">Within the scope of a local variable, it is a compile-time error to declare another local variable or constant with the same name.</span></span>

<span data-ttu-id="9ab6d-213">Una dichiarazione di variabile locale che dichiara più variabili equivale a più dichiarazioni di variabili singole con lo stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-213">A local variable declaration that declares multiple variables is equivalent to multiple declarations of single variables with the same type.</span></span> <span data-ttu-id="9ab6d-214">Inoltre, un inizializzatore di variabile in una dichiarazione di variabile locale corrisponde esattamente a un'istruzione di assegnazione di inserimento immediatamente dopo la dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-214">Furthermore, a variable initializer in a local variable declaration corresponds exactly to an assignment statement that is inserted immediately after the declaration.</span></span>

<span data-ttu-id="9ab6d-215">L'esempio</span><span class="sxs-lookup"><span data-stu-id="9ab6d-215">The example</span></span>
```csharp
void F() {
    int x = 1, y, z = x * 2;
}
```
<span data-ttu-id="9ab6d-216">corrisponde esattamente alla</span><span class="sxs-lookup"><span data-stu-id="9ab6d-216">corresponds exactly to</span></span>
```csharp
void F() {
    int x; x = 1;
    int y;
    int z; z = x * 2;
}
```

<span data-ttu-id="9ab6d-217">In una dichiarazione di variabile locale tipizzata in modo implicito, il tipo di variabile locale dichiarata presuppone che sia uguale al tipo dell'espressione utilizzata per inizializzare la variabile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-217">In an implicitly typed local variable declaration, the type of the local variable being declared is taken to be the same as the type of the expression used to initialize the variable.</span></span> <span data-ttu-id="9ab6d-218">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-218">For example:</span></span>
```csharp
var i = 5;
var s = "Hello";
var d = 1.0;
var numbers = new int[] {1, 2, 3};
var orders = new Dictionary<int,Order>();
```

<span data-ttu-id="9ab6d-219">Il tipizzata implicitamente dichiarazioni di variabili locali precedenti equivalgono esattamente alle seguenti dichiarazioni tipizzate in modo esplicito:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-219">The implicitly typed local variable declarations above are precisely equivalent to the following explicitly typed declarations:</span></span>
```csharp
int i = 5;
string s = "Hello";
double d = 1.0;
int[] numbers = new int[] {1, 2, 3};
Dictionary<int,Order> orders = new Dictionary<int,Order>();
```

### <a name="local-constant-declarations"></a><span data-ttu-id="9ab6d-220">Dichiarazioni di costante locale</span><span class="sxs-lookup"><span data-stu-id="9ab6d-220">Local constant declarations</span></span>

<span data-ttu-id="9ab6d-221">Oggetto *local_constant_declaration* dichiara una o più costanti locali.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-221">A *local_constant_declaration* declares one or more local constants.</span></span>

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

<span data-ttu-id="9ab6d-222">Il *tipo* di un *local_constant_declaration* specifica il tipo di costanti introdotte dalla dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-222">The *type* of a *local_constant_declaration* specifies the type of the constants introduced by the declaration.</span></span> <span data-ttu-id="9ab6d-223">Il tipo è seguito da un elenco delle *constant_declarator*s, ognuno dei quali viene introdotta una nuova costante.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-223">The type is followed by a list of *constant_declarator*s, each of which introduces a new constant.</span></span> <span data-ttu-id="9ab6d-224">Oggetto *constant_declarator* costituito da un *identificatore* che assegna il nome di costante, seguita da un "`=`" token, seguito da un *constant_expression* ([ Espressioni costanti](expressions.md#constant-expressions)) che specifica il valore della costante.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-224">A *constant_declarator* consists of an *identifier* that names the constant, followed by an "`=`" token, followed by a *constant_expression* ([Constant expressions](expressions.md#constant-expressions)) that gives the value of the constant.</span></span>

<span data-ttu-id="9ab6d-225">Il *tipo* e *constant_expression* di una dichiarazione di costante locale deve seguire le stesse regole a quelle di una dichiarazione di membro costante ([costanti](classes.md#constants)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-225">The *type* and *constant_expression* of a local constant declaration must follow the same rules as those of a constant member declaration ([Constants](classes.md#constants)).</span></span>

<span data-ttu-id="9ab6d-226">Viene ottenuto il valore di una costante locale in un'espressione che usa un' *simple_name* ([nomi semplici](expressions.md#simple-names)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-226">The value of a local constant is obtained in an expression using a *simple_name* ([Simple names](expressions.md#simple-names)).</span></span>

<span data-ttu-id="9ab6d-227">L'ambito di una costante locale è il blocco in cui si trova la dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-227">The scope of a local constant is the block in which the declaration occurs.</span></span> <span data-ttu-id="9ab6d-228">Si tratta di un errore per fare riferimento a una costante locale in una posizione di testo che precede relativi *constant_declarator*.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-228">It is an error to refer to a local constant in a textual position that precedes its *constant_declarator*.</span></span> <span data-ttu-id="9ab6d-229">Nell'ambito di una costante locale, è un errore in fase di compilazione per dichiarare local un'altra variabile o costante con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-229">Within the scope of a local constant, it is a compile-time error to declare another local variable or constant with the same name.</span></span>

<span data-ttu-id="9ab6d-230">Una dichiarazione di costante locale che dichiara più costanti equivale a più dichiarazioni di singole costanti con lo stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-230">A local constant declaration that declares multiple constants is equivalent to multiple declarations of single constants with the same type.</span></span>

## <a name="expression-statements"></a><span data-ttu-id="9ab6d-231">Istruzioni di espressione</span><span class="sxs-lookup"><span data-stu-id="9ab6d-231">Expression statements</span></span>

<span data-ttu-id="9ab6d-232">Un' *expression_statement* valuta un'espressione specificata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-232">An *expression_statement* evaluates a given expression.</span></span> <span data-ttu-id="9ab6d-233">Il valore calcolato dall'espressione, se presente, viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-233">The value computed by the expression, if any, is discarded.</span></span>

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

<span data-ttu-id="9ab6d-234">Non tutte le espressioni sono consentite come istruzioni.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-234">Not all expressions are permitted as statements.</span></span> <span data-ttu-id="9ab6d-235">In particolare, espressioni, ad esempio `x + y` e `x == 1` che semplicemente calcolare un valore (che verrà eliminato), non sono consentiti come istruzioni.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-235">In particular, expressions such as `x + y` and `x == 1` that merely compute a value (which will be discarded), are not permitted as statements.</span></span>

<span data-ttu-id="9ab6d-236">Esecuzione di un' *expression_statement* valuta l'espressione indipendente e quindi trasferisce il controllo al punto finale delle *expression_statement*.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-236">Execution of an *expression_statement* evaluates the contained expression and then transfers control to the end point of the *expression_statement*.</span></span> <span data-ttu-id="9ab6d-237">Il punto finale di un' *expression_statement* raggiungibile se tale *expression_statement* è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-237">The end point of an *expression_statement* is reachable if that *expression_statement* is reachable.</span></span>

## <a name="selection-statements"></a><span data-ttu-id="9ab6d-238">Istruzioni di selezione</span><span class="sxs-lookup"><span data-stu-id="9ab6d-238">Selection statements</span></span>

<span data-ttu-id="9ab6d-239">Istruzioni di selezione selezionare uno dei numerosi possibili istruzioni per l'esecuzione in base al valore di un'espressione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-239">Selection statements select one of a number of possible statements for execution based on the value of some expression.</span></span>

```antlr
selection_statement
    : if_statement
    | switch_statement
    ;
```

### <a name="the-if-statement"></a><span data-ttu-id="9ab6d-240">Se l'istruzione</span><span class="sxs-lookup"><span data-stu-id="9ab6d-240">The if statement</span></span>

<span data-ttu-id="9ab6d-241">Il `if` istruzione consente di selezionare un'istruzione per l'esecuzione in base al valore di un'espressione booleana.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-241">The `if` statement selects a statement for execution based on the value of a boolean expression.</span></span>

```antlr
if_statement
    : 'if' '(' boolean_expression ')' embedded_statement
    | 'if' '(' boolean_expression ')' embedded_statement 'else' embedded_statement
    ;
```

<span data-ttu-id="9ab6d-242">Un' `else` part è associata al livello lessicale più vicina precedente `if` quello consentito dalla sintassi.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-242">An `else` part is associated with the lexically nearest preceding `if` that is allowed by the syntax.</span></span> <span data-ttu-id="9ab6d-243">Di conseguenza, un `if` istruzione del form</span><span class="sxs-lookup"><span data-stu-id="9ab6d-243">Thus, an `if` statement of the form</span></span>
```csharp
if (x) if (y) F(); else G();
```
<span data-ttu-id="9ab6d-244">equivale a</span><span class="sxs-lookup"><span data-stu-id="9ab6d-244">is equivalent to</span></span>
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

<span data-ttu-id="9ab6d-245">Un `if` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-245">An `if` statement is executed as follows:</span></span>

*  <span data-ttu-id="9ab6d-246">Il *boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)) viene valutata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-246">The *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)) is evaluated.</span></span>
*  <span data-ttu-id="9ab6d-247">Se l'espressione booleana restituisce `true`, il controllo viene trasferito alla prima istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-247">If the boolean expression yields `true`, control is transferred to the first embedded statement.</span></span> <span data-ttu-id="9ab6d-248">Se e quando il controllo raggiunge il punto finale di tale istruzione, il controllo viene trasferito al punto finale del `if` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-248">When and if control reaches the end point of that statement, control is transferred to the end point of the `if` statement.</span></span>
*  <span data-ttu-id="9ab6d-249">Se l'espressione booleana restituisce `false` e, se un `else` parte è presente, il controllo viene trasferito alla seconda istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-249">If the boolean expression yields `false` and if an `else` part is present, control is transferred to the second embedded statement.</span></span> <span data-ttu-id="9ab6d-250">Se e quando il controllo raggiunge il punto finale di tale istruzione, il controllo viene trasferito al punto finale del `if` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-250">When and if control reaches the end point of that statement, control is transferred to the end point of the `if` statement.</span></span>
*  <span data-ttu-id="9ab6d-251">Se l'espressione booleana restituisce `false` e, se un' `else` parte non è presente, il controllo viene trasferito al punto finale del `if` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-251">If the boolean expression yields `false` and if an `else` part is not present, control is transferred to the end point of the `if` statement.</span></span>

<span data-ttu-id="9ab6d-252">La prima istruzione di incorporata un' `if` istruzione è raggiungibile se il `if` istruzione sia raggiungibile e che l'espressione booleana non ha il valore costante `false`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-252">The first embedded statement of an `if` statement is reachable if the `if` statement is reachable and the boolean expression does not have the constant value `false`.</span></span>

<span data-ttu-id="9ab6d-253">La seconda istruzione di incorporata un' `if` istruzione, se presente, è raggiungibile se il `if` istruzione sia raggiungibile e che l'espressione booleana non ha il valore costante `true`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-253">The second embedded statement of an `if` statement, if present, is reachable if the `if` statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

<span data-ttu-id="9ab6d-254">Il punto finale di un `if` istruzione è raggiungibile solo se il punto finale di almeno una delle relative istruzioni incorporate è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-254">The end point of an `if` statement is reachable if the end point of at least one of its embedded statements is reachable.</span></span> <span data-ttu-id="9ab6d-255">Inoltre, come punto alla fine di un' `if` istruzione senza alcun `else` parte è raggiungibile se il `if` istruzione sia raggiungibile e che l'espressione booleana non ha il valore costante `true`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-255">In addition, the end point of an `if` statement with no `else` part is reachable if the `if` statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

### <a name="the-switch-statement"></a><span data-ttu-id="9ab6d-256">L'istruzione switch</span><span class="sxs-lookup"><span data-stu-id="9ab6d-256">The switch statement</span></span>

<span data-ttu-id="9ab6d-257">L'istruzione switch seleziona per l'esecuzione di un elenco di istruzioni con un'etichetta di commutatore associato che corrisponde al valore dell'espressione switch.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-257">The switch statement selects for execution a statement list having an associated switch label that corresponds to the value of the switch expression.</span></span>

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

<span data-ttu-id="9ab6d-258">Oggetto *switch_statement* costituita dalla parola chiave `switch`, seguita da un'espressione tra parentesi (chiamata espressione switch), seguita da una *switch_block*.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-258">A *switch_statement* consists of the keyword `switch`, followed by a parenthesized expression (called the switch expression), followed by a *switch_block*.</span></span> <span data-ttu-id="9ab6d-259">Il *switch_block* costituita da zero o più *switch_section*s, racchiuso tra parentesi graffe.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-259">The *switch_block* consists of zero or more *switch_section*s, enclosed in braces.</span></span> <span data-ttu-id="9ab6d-260">Ciascuna *switch_section* costituito da uno o più *switch_label*s seguita da una *statement_list* ([istruzione Elenca](statements.md#statement-lists)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-260">Each *switch_section* consists of one or more *switch_label*s followed by a *statement_list* ([Statement lists](statements.md#statement-lists)).</span></span>

<span data-ttu-id="9ab6d-261">Il ***che controllano di tipo*** di un `switch` istruzione viene stabilita dall'espressione switch.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-261">The ***governing type*** of a `switch` statement is established by the switch expression.</span></span>

*  <span data-ttu-id="9ab6d-262">Se il tipo dell'espressione switch `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `bool`, `char`, `string`, o un *enum_type*, o se è il tipo nullable corrispondente a uno di questi tipi, questo sarà l'oggetto tipo del `switch` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-262">If the type of the switch expression is `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `bool`, `char`, `string`, or an *enum_type*, or if it is the nullable type corresponding to one of these types, then that is the governing type of the `switch` statement.</span></span>
*  <span data-ttu-id="9ab6d-263">In caso contrario, uno definito dall'utente la conversione implicita ([conversioni definite dall'utente](conversions.md#user-defined-conversions)) deve esistere dal tipo dell'espressione switch a uno dei seguenti che controllano i tipi possibili: `sbyte`, `byte`, `short` , `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `string`, o un tipo nullable corrispondente a uno di tali tipi.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-263">Otherwise, exactly one user-defined implicit conversion ([User-defined conversions](conversions.md#user-defined-conversions)) must exist from the type of the switch expression to one of the following possible governing types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `string`, or,  a nullable type corresponding to one of those types.</span></span>
*  <span data-ttu-id="9ab6d-264">In caso contrario, se non esiste alcuna conversione implicita o se più di una conversione implicita esiste, verrà generato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-264">Otherwise, if no such implicit conversion exists, or if more than one such implicit conversion exists, a compile-time error occurs.</span></span>

<span data-ttu-id="9ab6d-265">L'espressione costante della ognuno `case` etichetta deve indicare un valore convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) per il tipo che implementano la governance del `switch` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-265">The constant expression of each `case` label must denote a value that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the governing type of the `switch` statement.</span></span> <span data-ttu-id="9ab6d-266">Si verifica un errore in fase di compilazione se due o più `case` le etichette nella stessa `switch` istruzione specificare lo stesso valore costante.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-266">A compile-time error occurs if two or more `case` labels in the same `switch` statement specify the same constant value.</span></span>

<span data-ttu-id="9ab6d-267">Può esistere al massimo un `default` label in un'istruzione switch.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-267">There can be at most one `default` label in a switch statement.</span></span>

<span data-ttu-id="9ab6d-268">Oggetto `switch` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-268">A `switch` statement is executed as follows:</span></span>

*  <span data-ttu-id="9ab6d-269">L'espressione switch viene valutata e convertito nel tipo che implementano la governance.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-269">The switch expression is evaluated and converted to the governing type.</span></span>
*  <span data-ttu-id="9ab6d-270">Se una delle costanti specificato in un `case` etichetta nella stessa `switch` istruzione è uguale al valore dell'espressione switch, il controllo viene trasferito all'elenco di istruzioni seguente corrispondente `case` etichetta.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-270">If one of the constants specified in a `case` label in the same `switch` statement is equal to the value of the switch expression, control is transferred to the statement list following the matched `case` label.</span></span>
*  <span data-ttu-id="9ab6d-271">Se nessuna costante specificati in `case` le etichette nella stessa `switch` è uguale al valore dell'espressione switch, istruzione e, se un `default` è presente alcuna etichetta, il controllo viene trasferito all'istruzione successiva all'elenco di `default` etichetta.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-271">If none of the constants specified in `case` labels in the same `switch` statement is equal to the value of the switch expression, and if a `default` label is present, control is transferred to the statement list following the `default` label.</span></span>
*  <span data-ttu-id="9ab6d-272">Se nessuna costante specificati in `case` le etichette nella stessa `switch` è uguale al valore dell'espressione switch, istruzione e, se non `default` è presente alcuna etichetta, il controllo viene trasferito al punto finale del `switch` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-272">If none of the constants specified in `case` labels in the same `switch` statement is equal to the value of the switch expression, and if no `default` label is present, control is transferred to the end point of the `switch` statement.</span></span>

<span data-ttu-id="9ab6d-273">Se il punto di fine dell'elenco di istruzioni di una sezione di switch è raggiungibile, verrà generato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-273">If the end point of the statement list of a switch section is reachable, a compile-time error occurs.</span></span> <span data-ttu-id="9ab6d-274">Questo è noto come la regola "passaggio non consentito".</span><span class="sxs-lookup"><span data-stu-id="9ab6d-274">This is known as the "no fall through" rule.</span></span> <span data-ttu-id="9ab6d-275">L'esempio</span><span class="sxs-lookup"><span data-stu-id="9ab6d-275">The example</span></span>
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
<span data-ttu-id="9ab6d-276">è valida perché nessuna sezione opzione ha un punto finale raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-276">is valid because no switch section has a reachable end point.</span></span> <span data-ttu-id="9ab6d-277">Diversamente da C e C++, l'esecuzione di una sezione di switch non è consentito "giungano" alla sezione switch successiva e l'esempio</span><span class="sxs-lookup"><span data-stu-id="9ab6d-277">Unlike C and C++, execution of a switch section is not permitted to "fall through" to the next switch section, and the example</span></span>
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
<span data-ttu-id="9ab6d-278">Genera un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-278">results in a compile-time error.</span></span> <span data-ttu-id="9ab6d-279">Quando l'esecuzione di una sezione di switch deve essere seguita dall'esecuzione di un'altra sezione di switch, l'impostazione esplicita `goto case` o `goto default` istruzione deve essere utilizzata:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-279">When execution of a switch section is to be followed by execution of another switch section, an explicit `goto case` or `goto default` statement must be used:</span></span>
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

<span data-ttu-id="9ab6d-280">Più etichette sono consentite in una *switch_section*.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-280">Multiple labels are permitted in a *switch_section*.</span></span> <span data-ttu-id="9ab6d-281">L'esempio</span><span class="sxs-lookup"><span data-stu-id="9ab6d-281">The example</span></span>
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
<span data-ttu-id="9ab6d-282">è valido.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-282">is valid.</span></span> <span data-ttu-id="9ab6d-283">L'esempio non violi la regola "passaggio non consentito" perché le etichette `case 2:` e `default:` fanno parte dello stesso *switch_section*.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-283">The example does not violate the "no fall through" rule because the labels `case 2:` and `default:` are part of the same *switch_section*.</span></span>

<span data-ttu-id="9ab6d-284">La regola "passaggio non consentito" impedisce un tipo comune di bug che si verificano in C e C++ quando `break` istruzioni accidentalmente vengono omessi.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-284">The "no fall through" rule prevents a common class of bugs that occur in C and C++ when `break` statements are accidentally omitted.</span></span> <span data-ttu-id="9ab6d-285">Inoltre, a causa di questa regola, le sezioni switch di un `switch` istruzione può essere riorganizzata in modo arbitrario senza influenzare il comportamento dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-285">In addition, because of this rule, the switch sections of a `switch` statement can be arbitrarily rearranged without affecting the behavior of the statement.</span></span> <span data-ttu-id="9ab6d-286">Ad esempio, le sezioni del `switch` istruzione precedente può essere annullata senza influenzare il comportamento dell'istruzione:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-286">For example, the sections of the `switch` statement above can be reversed without affecting the behavior of the statement:</span></span>
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

<span data-ttu-id="9ab6d-287">Elenco di istruzioni di una sezione Opzioni in genere termina con un `break`, `goto case`, o `goto default` istruzione, ma qualsiasi costrutto che esegue il rendering Impossibile raggiungere il punto di fine dell'elenco di istruzioni è consentito.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-287">The statement list of a switch section typically ends in a `break`, `goto case`, or `goto default` statement, but any construct that renders the end point of the statement list unreachable is permitted.</span></span> <span data-ttu-id="9ab6d-288">Ad esempio, un `while` istruzione controllata dall'espressione booleana `true` è noto che mai raggiungere il punto finale.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-288">For example, a `while` statement controlled by the boolean expression `true` is known to never reach its end point.</span></span> <span data-ttu-id="9ab6d-289">Analogamente, un `throw` o `return` istruzione sempre trasferisce il controllo in un' posizione e non raggiunge mai il punto finale.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-289">Likewise, a `throw` or `return` statement always transfers control elsewhere and never reaches its end point.</span></span> <span data-ttu-id="9ab6d-290">Di conseguenza, l'esempio seguente è valido:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-290">Thus, the following example is valid:</span></span>
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

<span data-ttu-id="9ab6d-291">Il tipo che implementano la governance di un' `switch` istruzione potrebbe essere il tipo `string`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-291">The governing type of a `switch` statement may be the type `string`.</span></span> <span data-ttu-id="9ab6d-292">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-292">For example:</span></span>
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

<span data-ttu-id="9ab6d-293">Ad esempio gli operatori di uguaglianza di stringa ([gli operatori di uguaglianza di stringhe](expressions.md#string-equality-operators)), il `switch` istruzione distinzione maiuscole / minuscole e verrà eseguita una determinata sezione switch solo se la stringa dell'espressione switch corrisponda esattamente un `case` etichetta costante.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-293">Like the string equality operators ([String equality operators](expressions.md#string-equality-operators)), the `switch` statement is case sensitive and will execute a given switch section only if the switch expression string exactly matches a `case` label constant.</span></span>

<span data-ttu-id="9ab6d-294">Quando l'oggetto tipo di un `switch` istruzione viene `string`, il valore `null` è consentito come una costante di etichetta case.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-294">When the governing type of a `switch` statement is `string`, the value `null` is permitted as a case label constant.</span></span>

<span data-ttu-id="9ab6d-295">Il *statement_list*s di un *switch_block* può contenere istruzioni di dichiarazione ([istruzioni di dichiarazione](statements.md#declaration-statements)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-295">The *statement_list*s of a *switch_block* may contain declaration statements ([Declaration statements](statements.md#declaration-statements)).</span></span> <span data-ttu-id="9ab6d-296">L'ambito di una costante o variabile locale dichiarata in un blocco switch è il blocco switch.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-296">The scope of a local variable or constant declared in a switch block is the switch block.</span></span>

<span data-ttu-id="9ab6d-297">Elenco di istruzioni di una determinata sezione switch è raggiungibile se il `switch` istruzione sia raggiungibile e che viene soddisfatta almeno una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-297">The statement list of a given switch section is reachable if the `switch` statement is reachable and at least one of the following is true:</span></span>

*  <span data-ttu-id="9ab6d-298">L'espressione switch è un valore non costante.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-298">The switch expression is a non-constant value.</span></span>
*  <span data-ttu-id="9ab6d-299">L'espressione switch è un valore costante che corrisponde a un `case` etichetta nella sezione Opzioni.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-299">The switch expression is a constant value that matches a `case` label in the switch section.</span></span>
*  <span data-ttu-id="9ab6d-300">L'espressione switch è un valore costante che non corrisponde ad alcuno `case` etichetta e la sezione switch contiene il `default` etichetta.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-300">The switch expression is a constant value that doesn't match any `case` label, and the switch section contains the `default` label.</span></span>
*  <span data-ttu-id="9ab6d-301">Un'etichetta switch della sezione switch fa riferimento un raggiungibile `goto case` o `goto default` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-301">A switch label of the switch section is referenced by a reachable `goto case` or `goto default` statement.</span></span>

<span data-ttu-id="9ab6d-302">Il punto finale di un `switch` istruzione sia raggiungibile in presenza di almeno una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-302">The end point of a `switch` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="9ab6d-303">Il `switch` istruzione contiene un raggiungibile `break` istruzione che viene chiuso il `switch` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-303">The `switch` statement contains a reachable `break` statement that exits the `switch` statement.</span></span>
*  <span data-ttu-id="9ab6d-304">Il `switch` istruzione è raggiungibile, l'espressione switch è un valore non costante e nessun `default` è presente alcuna etichetta.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-304">The `switch` statement is reachable, the switch expression is a non-constant value, and no `default` label is present.</span></span>
*  <span data-ttu-id="9ab6d-305">Il `switch` istruzione è raggiungibile, l'espressione switch è un valore costante che non corrisponde ad alcuno `case` etichetta e nessun `default` è presente alcuna etichetta.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-305">The `switch` statement is reachable, the switch expression is a constant value that doesn't match any `case` label, and no `default` label is present.</span></span>

## <a name="iteration-statements"></a><span data-ttu-id="9ab6d-306">Istruzioni di iterazione</span><span class="sxs-lookup"><span data-stu-id="9ab6d-306">Iteration statements</span></span>

<span data-ttu-id="9ab6d-307">Istruzioni di iterazione eseguire ripetutamente un'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-307">Iteration statements repeatedly execute an embedded statement.</span></span>

```antlr
iteration_statement
    : while_statement
    | do_statement
    | for_statement
    | foreach_statement
    ;
```

### <a name="the-while-statement"></a><span data-ttu-id="9ab6d-308">L'istruzione while</span><span class="sxs-lookup"><span data-stu-id="9ab6d-308">The while statement</span></span>

<span data-ttu-id="9ab6d-309">Il `while` istruzione esegue in modo condizionale in un'istruzione incorporata zero o più volte.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-309">The `while` statement conditionally executes an embedded statement zero or more times.</span></span>

```antlr
while_statement
    : 'while' '(' boolean_expression ')' embedded_statement
    ;
```

<span data-ttu-id="9ab6d-310">Oggetto `while` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-310">A `while` statement is executed as follows:</span></span>

*  <span data-ttu-id="9ab6d-311">Il *boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)) viene valutata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-311">The *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)) is evaluated.</span></span>
*  <span data-ttu-id="9ab6d-312">Se l'espressione booleana restituisce `true`, il controllo viene trasferito all'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-312">If the boolean expression yields `true`, control is transferred to the embedded statement.</span></span> <span data-ttu-id="9ab6d-313">Quando e se il controllo raggiunge il punto finale dell'istruzione incorporata (possibilmente dall'esecuzione di un `continue` istruzione), il controllo viene trasferito all'inizio del `while` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-313">When and if control reaches the end point of the embedded statement (possibly from execution of a `continue` statement), control is transferred to the beginning of the `while` statement.</span></span>
*  <span data-ttu-id="9ab6d-314">Se l'espressione booleana restituisce `false`, il controllo viene trasferito al punto finale del `while` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-314">If the boolean expression yields `false`, control is transferred to the end point of the `while` statement.</span></span>

<span data-ttu-id="9ab6d-315">All'interno dell'istruzione incorporata in un `while` istruzione, un' `break` istruzione ([l'istruzione break](statements.md#the-break-statement)) può essere utilizzato per trasferire il controllo al punto finale del `while` istruzione (in modo da interrompere l'iterazione di incorporato istruzione) e un `continue` istruzione ([l'istruzione continue](statements.md#the-continue-statement)) può essere utilizzato per trasferire il controllo al punto di fine dell'istruzione incorporata (in modo da eseguire un'altra iterazione del `while` istruzione).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-315">Within the embedded statement of a `while` statement, a `break` statement ([The break statement](statements.md#the-break-statement)) may be used to transfer control to the end point of the `while` statement (thus ending iteration of the embedded statement), and a `continue` statement ([The continue statement](statements.md#the-continue-statement)) may be used to transfer control to the end point of the embedded statement (thus performing another iteration of the `while` statement).</span></span>

<span data-ttu-id="9ab6d-316">L'istruzione incorporata in un `while` istruzione è raggiungibile se il `while` istruzione sia raggiungibile e che l'espressione booleana non ha il valore costante `false`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-316">The embedded statement of a `while` statement is reachable if the `while` statement is reachable and the boolean expression does not have the constant value `false`.</span></span>

<span data-ttu-id="9ab6d-317">Il punto finale di un `while` istruzione sia raggiungibile in presenza di almeno una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-317">The end point of a `while` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="9ab6d-318">Il `while` istruzione contiene un raggiungibile `break` istruzione che viene chiuso il `while` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-318">The `while` statement contains a reachable `break` statement that exits the `while` statement.</span></span>
*  <span data-ttu-id="9ab6d-319">Il `while` istruzione sia raggiungibile e che l'espressione booleana non ha il valore costante `true`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-319">The `while` statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

### <a name="the-do-statement"></a><span data-ttu-id="9ab6d-320">Istruzione do</span><span class="sxs-lookup"><span data-stu-id="9ab6d-320">The do statement</span></span>

<span data-ttu-id="9ab6d-321">Il `do` istruzione esegue in modo condizionale in un'istruzione incorporata una o più volte.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-321">The `do` statement conditionally executes an embedded statement one or more times.</span></span>

```antlr
do_statement
    : 'do' embedded_statement 'while' '(' boolean_expression ')' ';'
    ;
```

<span data-ttu-id="9ab6d-322">Oggetto `do` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-322">A `do` statement is executed as follows:</span></span>

*  <span data-ttu-id="9ab6d-323">Il controllo viene trasferito all'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-323">Control is transferred to the embedded statement.</span></span>
*  <span data-ttu-id="9ab6d-324">Quando e se il controllo raggiunge il punto finale dell'istruzione incorporata (possibilmente dall'esecuzione di un `continue` istruzione), il *boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)) viene valutata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-324">When and if control reaches the end point of the embedded statement (possibly from execution of a `continue` statement), the *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)) is evaluated.</span></span> <span data-ttu-id="9ab6d-325">Se l'espressione booleana restituisce `true`, il controllo viene trasferito all'inizio del `do` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-325">If the boolean expression yields `true`, control is transferred to the beginning of the `do` statement.</span></span> <span data-ttu-id="9ab6d-326">In caso contrario, il controllo viene trasferito al punto finale del `do` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-326">Otherwise, control is transferred to the end point of the `do` statement.</span></span>

<span data-ttu-id="9ab6d-327">All'interno dell'istruzione incorporata in un `do` istruzione, un' `break` istruzione ([l'istruzione break](statements.md#the-break-statement)) può essere utilizzato per trasferire il controllo al punto finale del `do` istruzione (in modo da interrompere l'iterazione di incorporato istruzione) e un `continue` istruzione ([l'istruzione continue](statements.md#the-continue-statement)) può essere utilizzato per trasferire il controllo al punto di fine dell'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-327">Within the embedded statement of a `do` statement, a `break` statement ([The break statement](statements.md#the-break-statement)) may be used to transfer control to the end point of the `do` statement (thus ending iteration of the embedded statement), and a `continue` statement ([The continue statement](statements.md#the-continue-statement)) may be used to transfer control to the end point of the embedded statement.</span></span>

<span data-ttu-id="9ab6d-328">L'istruzione incorporata in un `do` istruzione è raggiungibile se il `do` istruzione sia raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-328">The embedded statement of a `do` statement is reachable if the `do` statement is reachable.</span></span>

<span data-ttu-id="9ab6d-329">Il punto finale di un `do` istruzione sia raggiungibile in presenza di almeno una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-329">The end point of a `do` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="9ab6d-330">Il `do` istruzione contiene un raggiungibile `break` istruzione che viene chiuso il `do` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-330">The `do` statement contains a reachable `break` statement that exits the `do` statement.</span></span>
*  <span data-ttu-id="9ab6d-331">Il punto finale dell'istruzione incorporata sia raggiungibile e che l'espressione booleana non ha il valore costante `true`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-331">The end point of the embedded statement is reachable and the boolean expression does not have the constant value `true`.</span></span>

### <a name="the-for-statement"></a><span data-ttu-id="9ab6d-332">L'istruzione for</span><span class="sxs-lookup"><span data-stu-id="9ab6d-332">The for statement</span></span>

<span data-ttu-id="9ab6d-333">Il `for` istruzione restituisce una sequenza di espressioni di inizializzazione e quindi, mentre una condizione è true, ripetutamente esegue un'istruzione incorporata e restituisce una sequenza di espressioni di iterazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-333">The `for` statement evaluates a sequence of initialization expressions and then, while a condition is true, repeatedly executes an embedded statement and evaluates a sequence of iteration expressions.</span></span>

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

<span data-ttu-id="9ab6d-334">Il *for_initializer*, se presente, è costituita da un *local_variable_declaration* ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)) o un elenco di *statement_ espressione*s ([istruzioni di espressione](statements.md#expression-statements)) separati da virgole.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-334">The *for_initializer*, if present, consists of either a *local_variable_declaration* ([Local variable declarations](statements.md#local-variable-declarations)) or a list of *statement_expression*s ([Expression statements](statements.md#expression-statements)) separated by commas.</span></span> <span data-ttu-id="9ab6d-335">L'ambito di una variabile locale dichiarata da un *for_initializer* inizia in corrispondenza la *local_variable_declarator* per la variabile e si estende fino alla fine dell'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-335">The scope of a local variable declared by a *for_initializer* starts at the *local_variable_declarator* for the variable and extends to the end of the embedded statement.</span></span> <span data-ttu-id="9ab6d-336">L'ambito include il *for_condition* e il *for_iterator*.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-336">The scope includes the *for_condition* and the *for_iterator*.</span></span>

<span data-ttu-id="9ab6d-337">Il *for_condition*, se presente, deve essere un *boolean_expression* ([espressioni booleane](expressions.md#boolean-expressions)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-337">The *for_condition*, if present, must be a *boolean_expression* ([Boolean expressions](expressions.md#boolean-expressions)).</span></span>

<span data-ttu-id="9ab6d-338">Il *for_iterator*, se presente, è costituito da un elenco delle *statement_expression*s ([istruzioni di espressione](statements.md#expression-statements)) separati da virgole.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-338">The *for_iterator*, if present, consists of a list of *statement_expression*s ([Expression statements](statements.md#expression-statements)) separated by commas.</span></span>

<span data-ttu-id="9ab6d-339">Oggetto per l'istruzione viene eseguito come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-339">A for statement is executed as follows:</span></span>

*  <span data-ttu-id="9ab6d-340">Se un *for_initializer* presente, gli inizializzatori di variabili o espressioni di istruzioni vengono eseguite nell'ordine in cui sono scritti.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-340">If a *for_initializer* is present, the variable initializers or statement expressions are executed in the order they are written.</span></span> <span data-ttu-id="9ab6d-341">Questo passaggio viene eseguito solo una volta.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-341">This step is only performed once.</span></span>
*  <span data-ttu-id="9ab6d-342">Se un *for_condition* è presente, viene valutata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-342">If a *for_condition* is present, it is evaluated.</span></span>
*  <span data-ttu-id="9ab6d-343">Se il *for_condition* non è presente o se il risultato della valutazione `true`, il controllo viene trasferito all'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-343">If the *for_condition* is not present or if the evaluation yields `true`, control is transferred to the embedded statement.</span></span> <span data-ttu-id="9ab6d-344">Quando e se il controllo raggiunge il punto finale dell'istruzione incorporata (anche in seguito all'esecuzione di un `continue` istruzione), le espressioni del *for_iterator*, se presente, vengono valutati in sequenza, e quindi un'altra iterazione eseguito, inizia con la valutazione del *for_condition* nel passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-344">When and if control reaches the end point of the embedded statement (possibly from execution of a `continue` statement), the expressions of the *for_iterator*, if any, are evaluated in sequence, and then another iteration is performed, starting with evaluation of the *for_condition* in the step above.</span></span>
*  <span data-ttu-id="9ab6d-345">Se il *for_condition* sia presente e la versione di valutazione produce `false`, il controllo viene trasferito al punto finale del `for` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-345">If the *for_condition* is present and the evaluation yields `false`, control is transferred to the end point of the `for` statement.</span></span>

<span data-ttu-id="9ab6d-346">All'interno dell'istruzione incorporata in un `for` istruzione, un' `break` istruzione ([l'istruzione break](statements.md#the-break-statement)) può essere utilizzato per trasferire il controllo al punto finale del `for` istruzione (in modo da interrompere l'iterazione di incorporato istruzione) e un `continue` istruzione ([l'istruzione continue](statements.md#the-continue-statement)) può essere utilizzato per trasferire il controllo al punto di fine dell'istruzione incorporata (in modo da eseguire il *for_iterator* e esecuzione di un'altra iterazione del `for` iniziale dell'istruzione, con il *for_condition*).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-346">Within the embedded statement of a `for` statement, a `break` statement ([The break statement](statements.md#the-break-statement)) may be used to transfer control to the end point of the `for` statement (thus ending iteration of the embedded statement), and a `continue` statement ([The continue statement](statements.md#the-continue-statement)) may be used to transfer control to the end point of the embedded statement (thus executing the *for_iterator* and performing another iteration of the `for` statement, starting with the *for_condition*).</span></span>

<span data-ttu-id="9ab6d-347">L'istruzione incorporata in un `for` istruzione è raggiungibile solo se viene soddisfatta una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-347">The embedded statement of a `for` statement is reachable if one of the following is true:</span></span>

*  <span data-ttu-id="9ab6d-348">Il `for` istruzione sia raggiungibile e nessun *for_condition* è presente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-348">The `for` statement is reachable and no *for_condition* is present.</span></span>
*  <span data-ttu-id="9ab6d-349">Il `for` istruzione sia raggiungibile e una *for_condition* sia presente e non ha il valore costante `false`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-349">The `for` statement is reachable and a *for_condition* is present and does not have the constant value `false`.</span></span>

<span data-ttu-id="9ab6d-350">Il punto finale di un `for` istruzione sia raggiungibile in presenza di almeno una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-350">The end point of a `for` statement is reachable if at least one of the following is true:</span></span>

*  <span data-ttu-id="9ab6d-351">Il `for` istruzione contiene un raggiungibile `break` istruzione che viene chiuso il `for` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-351">The `for` statement contains a reachable `break` statement that exits the `for` statement.</span></span>
*  <span data-ttu-id="9ab6d-352">Il `for` istruzione sia raggiungibile e una *for_condition* sia presente e non ha il valore costante `true`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-352">The `for` statement is reachable and a *for_condition* is present and does not have the constant value `true`.</span></span>

### <a name="the-foreach-statement"></a><span data-ttu-id="9ab6d-353">L'istruzione foreach</span><span class="sxs-lookup"><span data-stu-id="9ab6d-353">The foreach statement</span></span>

<span data-ttu-id="9ab6d-354">Il `foreach` istruzione enumera gli elementi di una raccolta, l'esecuzione di un'istruzione incorporata per ogni elemento della raccolta.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-354">The `foreach` statement enumerates the elements of a collection, executing an embedded statement for each element of the collection.</span></span>

```antlr
foreach_statement
    : 'foreach' '(' local_variable_type identifier 'in' expression ')' embedded_statement
    ;
```

<span data-ttu-id="9ab6d-355">Il *tipo* e *identifier* di un `foreach` istruzione dichiara il ***variabile di iterazione*** dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-355">The *type* and *identifier* of a `foreach` statement declare the ***iteration variable*** of the statement.</span></span> <span data-ttu-id="9ab6d-356">Se il `var` identificatore viene specificato come il *local_variable_type*e nessun tipo denominato `var` è incluso nell'ambito, viene definita la variabile di iterazione un' ***variabile di iterazione tipizzate in modo implicito***, e presuppone che sia il tipo di elemento del relativo tipo di `foreach` istruzione, come specificato di seguito.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-356">If the `var` identifier is given as the *local_variable_type*, and no type named `var` is in scope, the iteration variable is said to be an ***implicitly typed iteration variable***, and its type is taken to be the element type of the `foreach` statement, as specified below.</span></span> <span data-ttu-id="9ab6d-357">La variabile di iterazione corrisponde a una variabile locale di sola lettura con un ambito esteso tramite l'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-357">The iteration variable corresponds to a read-only local variable with a scope that extends over the embedded statement.</span></span> <span data-ttu-id="9ab6d-358">Durante l'esecuzione di un `foreach` istruzione, la variabile di iterazione rappresenta l'elemento della raccolta per il quale viene eseguita un'iterazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-358">During execution of a `foreach` statement, the iteration variable represents the collection element for which an iteration is currently being performed.</span></span> <span data-ttu-id="9ab6d-359">Si verifica un errore in fase di compilazione se l'istruzione incorporata tenta di modificare la variabile di iterazione (tramite l'assegnazione o la `++` e `--` operatori) oppure passare la variabile di iterazione come una `ref` o `out` parametro.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-359">A compile-time error occurs if the embedded statement attempts to modify the iteration variable (via assignment or the `++` and `--` operators) or pass the iteration variable as a `ref` or `out` parameter.</span></span>

<span data-ttu-id="9ab6d-360">Nell'esempio seguente, per brevità, `IEnumerable`, `IEnumerator`, `IEnumerable<T>` e `IEnumerator<T>` fanno riferimento ai tipi corrispondenti negli spazi dei nomi `System.Collections` e `System.Collections.Generic`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-360">In the following, for brevity, `IEnumerable`, `IEnumerator`, `IEnumerable<T>` and `IEnumerator<T>` refer to the corresponding types in the namespaces `System.Collections` and `System.Collections.Generic`.</span></span>

<span data-ttu-id="9ab6d-361">L'elaborazione in fase di compilazione di un'istruzione foreach determina innanzitutto le ***tipo di raccolta***, ***tipo di enumeratore*** e ***tipo di elemento*** dell'espressione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-361">The compile-time processing of a foreach statement first determines the ***collection type***, ***enumerator type*** and ***element type*** of the expression.</span></span> <span data-ttu-id="9ab6d-362">Questa operazione avviene nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-362">This determination proceeds as follows:</span></span>

*  <span data-ttu-id="9ab6d-363">Se il tipo `X` dei *espressione* è un tipo di matrice non vi è una conversione implicita del riferimento da `X` per il `IEnumerable` interfaccia (poiché `System.Array` implementa questa interfaccia).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-363">If the type `X` of *expression* is an array type then there is an implicit reference conversion from `X` to the `IEnumerable` interface (since `System.Array` implements this interface).</span></span> <span data-ttu-id="9ab6d-364">Il ***tipo di raccolta*** è la `IEnumerable` interfaccia, il ***tipo di enumeratore*** è il `IEnumerator` interfaccia e il ***tipo di elemento*** è il tipo di elemento del tipo di matrice `X`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-364">The ***collection type*** is the `IEnumerable` interface, the ***enumerator type*** is the `IEnumerator` interface and the ***element type*** is the element type of the array type `X`.</span></span>
*  <span data-ttu-id="9ab6d-365">Se il tipo `X` di *espressione* viene `dynamic` non vi è una conversione implicita da *espressione* per il `IEnumerable` interfaccia ([dinamico implicita le conversioni](conversions.md#implicit-dynamic-conversions)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-365">If the type `X` of *expression* is `dynamic` then there is an implicit conversion from *expression* to the `IEnumerable` interface ([Implicit dynamic conversions](conversions.md#implicit-dynamic-conversions)).</span></span> <span data-ttu-id="9ab6d-366">Il ***tipo di raccolta*** è la `IEnumerable` interfaccia e il ***tipo di enumeratore*** è il `IEnumerator` interfaccia.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-366">The ***collection type*** is the `IEnumerable` interface and the ***enumerator type*** is the `IEnumerator` interface.</span></span> <span data-ttu-id="9ab6d-367">Se il `var` identificatore viene specificato come il *local_variable_type* il ***tipo di elemento*** è `dynamic`, in caso contrario è `object`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-367">If the `var` identifier is given as the *local_variable_type* then the ***element type*** is `dynamic`, otherwise it is `object`.</span></span>
*  <span data-ttu-id="9ab6d-368">In caso contrario, determinare se il tipo `X` dispone di un oggetto appropriato `GetEnumerator` metodo:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-368">Otherwise, determine whether the type `X` has an appropriate `GetEnumerator` method:</span></span>
   * <span data-ttu-id="9ab6d-369">Eseguire la ricerca dei membri del tipo `X` con identificatore `GetEnumerator` e nessun argomento di tipo.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-369">Perform member lookup on the type `X` with identifier `GetEnumerator` and no type arguments.</span></span> <span data-ttu-id="9ab6d-370">Se la ricerca di membri non produce una corrispondenza, o che produce un'ambiguità, o produce una corrispondenza che non è un gruppo di metodi, controllare per un'interfaccia enumerabile, come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-370">If the member lookup does not produce a match, or it produces an ambiguity, or produces a match that is not a method group, check for an enumerable interface as described below.</span></span> <span data-ttu-id="9ab6d-371">È consigliabile che verrà generato un avviso se la ricerca di membri produce qualsiasi ad eccezione di un gruppo di metodi o alcuna corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-371">It is recommended that a warning be issued if member lookup produces anything except a method group or no match.</span></span>
   * <span data-ttu-id="9ab6d-372">Eseguire la risoluzione dell'overload con il gruppo di metodi risultante e un elenco di argomenti vuoto.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-372">Perform overload resolution using the resulting method group and an empty argument list.</span></span> <span data-ttu-id="9ab6d-373">Se la risoluzione dell'overload determina in alcun metodo applicabile, un'ambiguità o risultati in un singolo metodo migliore, ma tale metodo è statico o non pubblico, cercare un'interfaccia enumerabile come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-373">If overload resolution results in no applicable methods, results in an ambiguity, or results in a single best method but that method is either static or not public, check for an enumerable interface as described below.</span></span> <span data-ttu-id="9ab6d-374">È consigliabile che verrà generato un avviso se la risoluzione dell'overload produce qualsiasi ad eccezione di un metodo di istanza pubblici non ambigue o alcun metodo applicabile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-374">It is recommended that a warning be issued if overload resolution produces anything except an unambiguous public instance method or no applicable methods.</span></span>
   * <span data-ttu-id="9ab6d-375">Se il tipo restituito `E` del `GetEnumerator` (metodo) non è una classe, viene prodotto il tipo di struct o interfaccia, un errore e viene eseguita alcuna operazione ulteriormente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-375">If the return type `E` of the `GetEnumerator` method is not a class, struct or interface type, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="9ab6d-376">Ricerca di membri viene eseguita su `E` con l'identificatore `Current` e nessun argomento di tipo.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-376">Member lookup is performed on `E` with the identifier `Current` and no type arguments.</span></span> <span data-ttu-id="9ab6d-377">Se il membro individuata alcuna corrispondenza, il risultato è un errore o il risultato è diverso da una proprietà di istanza pubblici che consente la lettura, viene generato un errore e viene eseguita alcuna operazione ulteriormente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-377">If the member lookup produces no match, the result is an error, or the result is anything except a public instance property that permits reading, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="9ab6d-378">Ricerca di membri viene eseguita su `E` con l'identificatore `MoveNext` e nessun argomento di tipo.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-378">Member lookup is performed on `E` with the identifier `MoveNext` and no type arguments.</span></span> <span data-ttu-id="9ab6d-379">Se il membro individuata alcuna corrispondenza, il risultato è un errore o il risultato è diverso da un gruppo di metodi, viene generato un errore e viene eseguita alcuna operazione ulteriormente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-379">If the member lookup produces no match, the result is an error, or the result is anything except a method group, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="9ab6d-380">Risoluzione dell'overload viene eseguita sul gruppo di metodi con un elenco di argomenti vuoto.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-380">Overload resolution is performed on the method group with an empty argument list.</span></span> <span data-ttu-id="9ab6d-381">Se i risultati di risoluzione dell'overload in alcun metodo applicabile, i risultati in un'ambiguità o i risultati in un metodo migliore, ma tale metodo è statico o non pubblico, o il tipo restituito non `bool`, viene generato un errore e viene eseguita alcuna operazione ulteriormente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-381">If overload resolution results in no applicable methods, results in an ambiguity, or results in a single best method but that method is either static or not public, or its return type is not `bool`, an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="9ab6d-382">Il ***tipo di raccolta*** è `X`, il ***tipo di enumeratore*** è `E`e il ***tipo dell'elemento*** è il tipo del `Current` proprietà.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-382">The ***collection type*** is `X`, the ***enumerator type*** is `E`, and the ***element type*** is the type of the `Current` property.</span></span>

*  <span data-ttu-id="9ab6d-383">In caso contrario, cercare un'interfaccia enumerabile:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-383">Otherwise, check for an enumerable interface:</span></span>
   * <span data-ttu-id="9ab6d-384">Se tra tutti i tipi `Ti` per cui non esiste una conversione implicita da `X` a `IEnumerable<Ti>`, è presente un tipo univoco `T` modo tale che `T` non è `dynamic` e per tutti gli altri `Ti` è presente un la conversione implicita da `IEnumerable<T>` al `IEnumerable<Ti>`, il ***tipo di raccolta*** è l'interfaccia `IEnumerable<T>`, la ***tipo di enumeratore*** è l'interfaccia `IEnumerator<T>`e il ***tipo di elemento*** è `T`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-384">If among all the types `Ti` for which there is an implicit conversion from `X` to `IEnumerable<Ti>`, there is a unique type `T` such that `T` is not `dynamic` and for all the other `Ti` there is an implicit conversion from `IEnumerable<T>` to `IEnumerable<Ti>`, then the ***collection type*** is the interface `IEnumerable<T>`, the ***enumerator type*** is the interface `IEnumerator<T>`, and the ***element type*** is `T`.</span></span>
   * <span data-ttu-id="9ab6d-385">In caso contrario, se è presente più di un tale tipo `T`, viene generato un errore e viene eseguita alcuna operazione ulteriormente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-385">Otherwise, if there is more than one such type `T`, then an error is produced and no further steps are taken.</span></span>
   * <span data-ttu-id="9ab6d-386">In caso contrario, se è presente una conversione implicita da `X` per il `System.Collections.IEnumerable` interfaccia, il ***tipo di raccolta*** è questa interfaccia, il ***tipo di enumeratore*** è l'interfaccia `System.Collections.IEnumerator`e il ***tipo di elemento*** è `object`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-386">Otherwise, if there is an implicit conversion from `X` to the `System.Collections.IEnumerable` interface, then the ***collection type*** is this interface, the ***enumerator type*** is the interface `System.Collections.IEnumerator`, and the ***element type*** is `object`.</span></span>
   * <span data-ttu-id="9ab6d-387">In caso contrario, viene generato un errore e viene eseguita alcuna operazione ulteriormente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-387">Otherwise, an error is produced and no further steps are taken.</span></span>

<span data-ttu-id="9ab6d-388">I passaggi precedenti, se ha esito positivo, in modo non ambiguo producono un tipo di raccolta `C`, tipo di enumeratore `E` e il tipo di elemento `T`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-388">The above steps, if successful, unambiguously produce a collection type `C`, enumerator type `E` and element type `T`.</span></span> <span data-ttu-id="9ab6d-389">Un'istruzione foreach del form</span><span class="sxs-lookup"><span data-stu-id="9ab6d-389">A foreach statement of the form</span></span>
```csharp
foreach (V v in x) embedded_statement
```
<span data-ttu-id="9ab6d-390">viene quindi espansa per:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-390">is then expanded to:</span></span>
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

<span data-ttu-id="9ab6d-391">La variabile `e` non è visibile e accessibile all'espressione `x` o l'istruzione incorporata o qualsiasi altro codice sorgente del programma.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-391">The variable `e` is not visible to or accessible to the expression `x` or the embedded statement or any other source code of the program.</span></span> <span data-ttu-id="9ab6d-392">La variabile `v` è di sola lettura in un'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-392">The variable `v` is read-only in the embedded statement.</span></span> <span data-ttu-id="9ab6d-393">Se non esiste una conversione esplicita ([conversioni esplicite](conversions.md#explicit-conversions)) da `T` (tipo di elemento) per `V` (la *local_variable_type* nell'istruzione foreach), viene generato un errore e non altri passaggi da eseguire.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-393">If there is not an explicit conversion ([Explicit conversions](conversions.md#explicit-conversions)) from `T` (the element type) to `V` (the *local_variable_type* in the foreach statement), an error is produced and no further steps are taken.</span></span> <span data-ttu-id="9ab6d-394">Se `x` ha il valore `null`, un `System.NullReferenceException` viene generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-394">If `x` has the value `null`, a `System.NullReferenceException` is thrown at run-time.</span></span>

<span data-ttu-id="9ab6d-395">Un'implementazione può implementare una determinata istruzione foreach in modo diverso, ad esempio, per motivi di prestazioni, purché il comportamento è coerente con l'espansione del precedente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-395">An implementation is permitted to implement a given foreach-statement differently, e.g. for performance reasons, as long as the behavior is consistent with the above expansion.</span></span>

<span data-ttu-id="9ab6d-396">Il posizionamento dei `v` all'interno di while è importante per la modalità viene acquisito da qualsiasi funzione anonima che si verificano nel ciclo il *embedded_statement*.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-396">The placement of `v` inside the while loop is important for how it is captured by any anonymous function occurring in the *embedded_statement*.</span></span>

<span data-ttu-id="9ab6d-397">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-397">For example:</span></span>
```csharp
int[] values = { 7, 9, 13 };
Action f = null;

foreach (var value in values)
{
    if (f == null) f = () => Console.WriteLine("First value: " + value);
}

f();
```
<span data-ttu-id="9ab6d-398">Se `v` è stato dichiarato all'esterno di while loop, possono essere condivisi tra tutte le iterazioni e il relativo valore dopo il per ciclo sarebbe il valore finale, `13`, ovvero che la chiamata di `f` stamperebbe.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-398">If `v` was declared outside of the while loop, it would be shared among all iterations, and its value after the for loop would be the final value, `13`, which is what the invocation of `f` would print.</span></span> <span data-ttu-id="9ab6d-399">Al contrario, poiché ogni iterazione ha il proprio variabile `v`, quella acquisita dal `f` nella prima iterazione continuerà a contenere il valore `7`, che è ciò che sarà stampato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-399">Instead, because each iteration has its own variable `v`, the one captured by `f` in the first iteration will continue to hold the value `7`, which is what will be printed.</span></span> <span data-ttu-id="9ab6d-400">(Nota: le versioni precedenti di c# dichiarato `v` ciclo esterno di while.)</span><span class="sxs-lookup"><span data-stu-id="9ab6d-400">(Note: earlier versions of C# declared `v` outside of the while loop.)</span></span>

<span data-ttu-id="9ab6d-401">Il corpo del blocco finally viene costruito seguendo i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-401">The body of the finally block is constructed according to the following steps:</span></span>

*  <span data-ttu-id="9ab6d-402">Se è presente una conversione implicita da `E` per il `System.IDisposable` interfaccia, quindi</span><span class="sxs-lookup"><span data-stu-id="9ab6d-402">If there is an implicit conversion from `E` to the `System.IDisposable` interface, then</span></span>
   *  <span data-ttu-id="9ab6d-403">Se `E` è un tipo di valore non nullable la clausola finally viene espansa sull'equivalente della semantica:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-403">If `E` is a non-nullable value type then the finally clause is expanded to the semantic equivalent  of:</span></span>

      ```csharp
      finally {
          ((System.IDisposable)e).Dispose();
      }
      ```

   *  <span data-ttu-id="9ab6d-404">In caso contrario, la clausola finally viene espansa sull'equivalente della semantica:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-404">Otherwise the finally clause is expanded to the semantic equivalent of:</span></span>

      ```csharp
      finally {
          if (e != null) ((System.IDisposable)e).Dispose();
      }
      ```

   <span data-ttu-id="9ab6d-405">Tuttavia se `E` è un tipo di valore o creare un'istanza di un tipo di valore, quindi il cast di un parametro di tipo `e` a `System.IDisposable` non causerà la conversione boxing si verifichi.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-405">except that if `E` is a value type, or a type parameter instantiated to a value type, then the cast of `e` to `System.IDisposable` will not cause boxing to occur.</span></span>

*  <span data-ttu-id="9ab6d-406">In caso contrario, se `E` è un tipo sealed, la clausola finally viene espansa in un blocco vuoto:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-406">Otherwise, if `E` is a sealed type, the finally clause is expanded to an empty block:</span></span>

   ```csharp
   finally {
   }
   ```

*  <span data-ttu-id="9ab6d-407">In caso contrario, la clausola finally viene espansa per:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-407">Otherwise, the finally clause is expanded to:</span></span>

   ```csharp
   finally {
       System.IDisposable d = e as System.IDisposable;
       if (d != null) d.Dispose();
   }
   ```    

   <span data-ttu-id="9ab6d-408">La variabile locale `d` non è visibile e accessibile a qualsiasi codice utente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-408">The local variable `d` is not visible to or accessible to any user code.</span></span> <span data-ttu-id="9ab6d-409">In particolare, non è in conflitto con qualsiasi altra variabile il cui ambito include il blocco finally.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-409">In particular, it does not conflict with any other variable whose scope includes the finally block.</span></span>

<span data-ttu-id="9ab6d-410">L'ordine in cui `foreach` scorre gli elementi di una matrice, è il seguente: Per gli elementi di matrici unidimensionali vengono attraversati in ordine di indice crescente, partire dall'indice `0` fino all'indice `Length - 1`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-410">The order in which `foreach` traverses the elements of an array, is as follows: For single-dimensional arrays elements are traversed in increasing index order, starting with index `0` and ending with index `Length - 1`.</span></span> <span data-ttu-id="9ab6d-411">Per le matrici multidimensionali, gli elementi vengono scorsi in modo che gli indici della dimensione più a destra vengono prima un aumento, quindi la successiva dimensione a sinistra, e così via a sinistra.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-411">For multi-dimensional arrays, elements are traversed such that the indices of the rightmost dimension are increased first, then the next left dimension, and so on to the left.</span></span>

<span data-ttu-id="9ab6d-412">Ciascun valore in una matrice bidimensionale, in ordine degli elementi viene visualizzato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-412">The following example prints out each value in a two-dimensional array, in element order:</span></span>
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
<span data-ttu-id="9ab6d-413">Come indicato di seguito è riportato l'output prodotto:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-413">The output produced is as follows:</span></span>
```csharp
1.2 2.3 3.4 4.5 5.6 6.7 7.8 8.9
```

<span data-ttu-id="9ab6d-414">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="9ab6d-414">In the example</span></span>
```csharp
int[] numbers = { 1, 3, 5, 7, 9 };
foreach (var n in numbers) Console.WriteLine(n);
```
<span data-ttu-id="9ab6d-415">il tipo della `n` viene dedotto come `int`, il tipo di elemento di `numbers`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-415">the type of `n` is inferred to be `int`, the element type of `numbers`.</span></span>

## <a name="jump-statements"></a><span data-ttu-id="9ab6d-416">Istruzioni di salto</span><span class="sxs-lookup"><span data-stu-id="9ab6d-416">Jump statements</span></span>

<span data-ttu-id="9ab6d-417">Istruzioni di salto trasferisce incondizionatamente il controllo.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-417">Jump statements unconditionally transfer control.</span></span>

```antlr
jump_statement
    : break_statement
    | continue_statement
    | goto_statement
    | return_statement
    | throw_statement
    ;
```

<span data-ttu-id="9ab6d-418">Il percorso in cui un'istruzione jump trasferisce il controllo viene chiamato il ***destinazione*** dell'istruzione di spostamento.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-418">The location to which a jump statement transfers control is called the ***target*** of the jump statement.</span></span>

<span data-ttu-id="9ab6d-419">Quando un'istruzione di salto si verifica all'interno di un blocco e la destinazione dell'istruzione jump è esterna al blocco, l'istruzione di salto viene detto ***uscire*** il blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-419">When a jump statement occurs within a block, and the target of that jump statement is outside that block, the jump statement is said to ***exit*** the block.</span></span> <span data-ttu-id="9ab6d-420">Mentre un'istruzione di salto può trasferire il controllo all'esterno di un blocco, è non possibile trasferire il controllo mai in un blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-420">While a jump statement may transfer control out of a block, it can never transfer control into a block.</span></span>

<span data-ttu-id="9ab6d-421">Esecuzione di istruzioni di salto è ulteriormente complicata dalla presenza di intermedi `try` istruzioni.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-421">Execution of jump statements is complicated by the presence of intervening `try` statements.</span></span> <span data-ttu-id="9ab6d-422">In assenza di tali `try` (istruzioni), un'istruzione jump trasferisce il controllo dell'istruzione di salto per il valore di destinazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-422">In the absence of such `try` statements, a jump statement unconditionally transfers control from the jump statement to its target.</span></span> <span data-ttu-id="9ab6d-423">In presenza di questo tipo di protezione `try` (istruzioni), l'esecuzione risulta più complessa.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-423">In the presence of such intervening `try` statements, execution is more complex.</span></span> <span data-ttu-id="9ab6d-424">Se l'istruzione di salto termina uno o più `try` con i blocchi associati `finally` blocchi di controllo viene trasferito inizialmente per il `finally` blocco di più interna `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-424">If the jump statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="9ab6d-425">Quando e se il controllo raggiunge il punto finale di un `finally` blocco di controllo viene trasferito al `finally` blocco di inclusione successivo `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-425">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="9ab6d-426">Questo processo viene ripetuto finché il `finally` blocchi di tutti interessati `try` istruzioni sono state eseguite.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-426">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>

<span data-ttu-id="9ab6d-427">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="9ab6d-427">In the example</span></span>
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
<span data-ttu-id="9ab6d-428">il `finally` associati a due blocchi `try` istruzioni vengono eseguite prima che il controllo viene trasferito nella destinazione dell'istruzione di spostamento.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-428">the `finally` blocks associated with two `try` statements are executed before control is transferred to the target of the jump statement.</span></span>

<span data-ttu-id="9ab6d-429">Come indicato di seguito è riportato l'output prodotto:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-429">The output produced is as follows:</span></span>
```
Before break
Innermost finally block
Outermost finally block
After break
```

### <a name="the-break-statement"></a><span data-ttu-id="9ab6d-430">L'istruzione break</span><span class="sxs-lookup"><span data-stu-id="9ab6d-430">The break statement</span></span>

<span data-ttu-id="9ab6d-431">Il `break` istruzione abbandona inclusione più vicina `switch`, `while`, `do`, `for`, o `foreach` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-431">The `break` statement exits the nearest enclosing `switch`, `while`, `do`, `for`, or `foreach` statement.</span></span>

```antlr
break_statement
    : 'break' ';'
    ;
```

<span data-ttu-id="9ab6d-432">La destinazione di una `break` istruzione è il punto finale di inclusione più vicina `switch`, `while`, `do`, `for`, o `foreach` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-432">The target of a `break` statement is the end point of the nearest enclosing `switch`, `while`, `do`, `for`, or `foreach` statement.</span></span> <span data-ttu-id="9ab6d-433">Se un `break` istruzione non è racchiuso da un `switch`, `while`, `do`, `for`, o `foreach` istruzione, verrà generato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-433">If a `break` statement is not enclosed by a `switch`, `while`, `do`, `for`, or `foreach` statement, a compile-time error occurs.</span></span>

<span data-ttu-id="9ab6d-434">Quando più `switch`, `while`, `do`, `for`, o `foreach` istruzioni sono annidate all'interno di altro, un `break` informativa si applica solo all'istruzione più interno.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-434">When multiple `switch`, `while`, `do`, `for`, or `foreach` statements are nested within each other, a `break` statement applies only to the innermost statement.</span></span> <span data-ttu-id="9ab6d-435">Per trasferire il controllo con più livelli di nidificazione, un `goto` istruzione ([istruzione goto](statements.md#the-goto-statement)) deve essere utilizzato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-435">To transfer control across multiple nesting levels, a `goto` statement ([The goto statement](statements.md#the-goto-statement)) must be used.</span></span>

<span data-ttu-id="9ab6d-436">Oggetto `break` non può uscire dall'istruzione un' `finally` blocco ([l'istruzione try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-436">A `break` statement cannot exit a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span> <span data-ttu-id="9ab6d-437">Quando un `break` istruzione si trova all'interno di un `finally` block, la destinazione delle `break` istruzione deve essere all'interno della stessa `finally` bloccare; in caso contrario, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-437">When a `break` statement occurs within a `finally` block, the target of the `break` statement must be within the same `finally` block; otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="9ab6d-438">Oggetto `break` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-438">A `break` statement is executed as follows:</span></span>

*  <span data-ttu-id="9ab6d-439">Se il `break` istruzione esce da uno o più `try` blocchi con associati `finally` blocchi di controllo viene trasferito inizialmente al `finally` blocco di più interna `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-439">If the `break` statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="9ab6d-440">Quando e se il controllo raggiunge il punto finale di un `finally` blocco di controllo viene trasferito al `finally` blocco di inclusione successivo `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-440">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="9ab6d-441">Questo processo viene ripetuto finché il `finally` blocchi di tutti interessati `try` istruzioni sono state eseguite.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-441">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>
*  <span data-ttu-id="9ab6d-442">Il controllo viene trasferito alla destinazione del `break` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-442">Control is transferred to the target of the `break` statement.</span></span>

<span data-ttu-id="9ab6d-443">Poiché un `break` istruzione in modo incondizionato trasferisce il controllo in un' posizione, il punto finale di un `break` istruzione non è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-443">Because a `break` statement unconditionally transfers control elsewhere, the end point of a `break` statement is never reachable.</span></span>

### <a name="the-continue-statement"></a><span data-ttu-id="9ab6d-444">L'istruzione continue</span><span class="sxs-lookup"><span data-stu-id="9ab6d-444">The continue statement</span></span>

<span data-ttu-id="9ab6d-445">Il `continue` istruzione avvia una nuova iterazione di inclusione più vicina `while`, `do`, `for`, o `foreach` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-445">The `continue` statement starts a new iteration of the nearest enclosing `while`, `do`, `for`, or `foreach` statement.</span></span>

```antlr
continue_statement
    : 'continue' ';'
    ;
```

<span data-ttu-id="9ab6d-446">La destinazione di una `continue` istruzione è il punto finale dell'ultima istruzione di inclusione più vicina `while`, `do`, `for`, o `foreach` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-446">The target of a `continue` statement is the end point of the embedded statement of the nearest enclosing `while`, `do`, `for`, or `foreach` statement.</span></span> <span data-ttu-id="9ab6d-447">Se un `continue` istruzione non è racchiuso da un `while`, `do`, `for`, o `foreach` istruzione, verrà generato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-447">If a `continue` statement is not enclosed by a `while`, `do`, `for`, or `foreach` statement, a compile-time error occurs.</span></span>

<span data-ttu-id="9ab6d-448">Quando più `while`, `do`, `for`, o `foreach` istruzioni sono annidate all'interno di altro, un `continue` informativa si applica solo all'istruzione più interno.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-448">When multiple `while`, `do`, `for`, or `foreach` statements are nested within each other, a `continue` statement applies only to the innermost statement.</span></span> <span data-ttu-id="9ab6d-449">Per trasferire il controllo con più livelli di nidificazione, un `goto` istruzione ([istruzione goto](statements.md#the-goto-statement)) deve essere utilizzato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-449">To transfer control across multiple nesting levels, a `goto` statement ([The goto statement](statements.md#the-goto-statement)) must be used.</span></span>

<span data-ttu-id="9ab6d-450">Oggetto `continue` non può uscire dall'istruzione un' `finally` blocco ([l'istruzione try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-450">A `continue` statement cannot exit a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span> <span data-ttu-id="9ab6d-451">Quando un `continue` istruzione si trova all'interno di un `finally` block, la destinazione delle `continue` istruzione deve essere all'interno della stessa `finally` bloccare; in caso contrario, verrà generato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-451">When a `continue` statement occurs within a `finally` block, the target of the `continue` statement must be within the same `finally` block; otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="9ab6d-452">Oggetto `continue` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-452">A `continue` statement is executed as follows:</span></span>

*  <span data-ttu-id="9ab6d-453">Se il `continue` istruzione esce da uno o più `try` blocchi con associati `finally` blocchi di controllo viene trasferito inizialmente al `finally` blocco di più interna `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-453">If the `continue` statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="9ab6d-454">Quando e se il controllo raggiunge il punto finale di un `finally` blocco di controllo viene trasferito al `finally` blocco di inclusione successivo `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-454">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="9ab6d-455">Questo processo viene ripetuto finché il `finally` blocchi di tutti interessati `try` istruzioni sono state eseguite.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-455">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>
*  <span data-ttu-id="9ab6d-456">Il controllo viene trasferito alla destinazione del `continue` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-456">Control is transferred to the target of the `continue` statement.</span></span>

<span data-ttu-id="9ab6d-457">Poiché un `continue` istruzione in modo incondizionato trasferisce il controllo in un' posizione, il punto finale di un `continue` istruzione non è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-457">Because a `continue` statement unconditionally transfers control elsewhere, the end point of a `continue` statement is never reachable.</span></span>

### <a name="the-goto-statement"></a><span data-ttu-id="9ab6d-458">Istruzione GoTo</span><span class="sxs-lookup"><span data-stu-id="9ab6d-458">The goto statement</span></span>

<span data-ttu-id="9ab6d-459">Il `goto` istruzione trasferisce il controllo a un'istruzione che è contrassegnata da un'etichetta.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-459">The `goto` statement transfers control to a statement that is marked by a label.</span></span>

```antlr
goto_statement
    : 'goto' identifier ';'
    | 'goto' 'case' constant_expression ';'
    | 'goto' 'default' ';'
    ;
```

<span data-ttu-id="9ab6d-460">La destinazione di una `goto` *identificatore* istruzione è l'istruzione con etichetta con l'etichetta specificata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-460">The target of a `goto` *identifier* statement is the labeled statement with the given label.</span></span> <span data-ttu-id="9ab6d-461">Se un'etichetta con il nome specificato non esiste nel membro della funzione corrente o se il `goto` istruzione non è all'interno dell'ambito dell'etichetta, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-461">If a label with the given name does not exist in the current function member, or if the `goto` statement is not within the scope of the label, a compile-time error occurs.</span></span> <span data-ttu-id="9ab6d-462">Questa regola consente di utilizzare un `goto` istruzione per trasferire il controllo all'esterno di un ambito annidato, ma non in un ambito annidato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-462">This rule permits the use of a `goto` statement to transfer control out of a nested scope, but not into a nested scope.</span></span> <span data-ttu-id="9ab6d-463">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="9ab6d-463">In the example</span></span>
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
<span data-ttu-id="9ab6d-464">un `goto` istruzione viene usata per trasferire il controllo all'esterno di un ambito annidato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-464">a `goto` statement is used to transfer control out of a nested scope.</span></span>

<span data-ttu-id="9ab6d-465">La destinazione di una `goto case` istruzione è l'elenco di istruzioni in immediatamente che li racchiude `switch` istruzione ([l'istruzione switch](statements.md#the-switch-statement)), che contiene un `case` etichetta con il valore costante specificato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-465">The target of a `goto case` statement is the statement list in the immediately enclosing `switch` statement ([The switch statement](statements.md#the-switch-statement)), which contains a `case` label with the given constant value.</span></span> <span data-ttu-id="9ab6d-466">Se il `goto case` istruzione non si trova a un `switch` istruzione, se il *constant_expression* non è implicitamente convertibile ([conversioni implicite](conversions.md#implicit-conversions)) per il tipo che implementano la governance del più vicino che li racchiude `switch` istruzione, oppure se l'inclusione più vicina `switch` istruzione non contiene un `case` assegnare un'etichetta con il valore costante specificato, verrà generato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-466">If the `goto case` statement is not enclosed by a `switch` statement, if the *constant_expression* is not implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the governing type of the nearest enclosing `switch` statement, or if the nearest enclosing `switch` statement does not contain a `case` label with the given constant value, a compile-time error occurs.</span></span>

<span data-ttu-id="9ab6d-467">La destinazione di una `goto default` istruzione è l'elenco di istruzioni in immediatamente che li racchiude `switch` istruzione ([l'istruzione switch](statements.md#the-switch-statement)), che contiene un `default` etichetta.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-467">The target of a `goto default` statement is the statement list in the immediately enclosing `switch` statement ([The switch statement](statements.md#the-switch-statement)), which contains a `default` label.</span></span> <span data-ttu-id="9ab6d-468">Se il `goto default` istruzione non è racchiuso un `switch` istruzione, oppure se inclusione più vicina `switch` istruzione non contiene un `default` assegnare un'etichetta, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-468">If the `goto default` statement is not enclosed by a `switch` statement, or if the nearest enclosing `switch` statement does not contain a `default` label, a compile-time error occurs.</span></span>

<span data-ttu-id="9ab6d-469">Oggetto `goto` non può uscire dall'istruzione un' `finally` blocco ([l'istruzione try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-469">A `goto` statement cannot exit a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span> <span data-ttu-id="9ab6d-470">Quando un `goto` istruzione si trova all'interno di un `finally` block, la destinazione delle `goto` istruzione deve essere all'interno della stessa `finally` blocco, o in caso contrario, verrà generato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-470">When a `goto` statement occurs within a `finally` block, the target of the `goto` statement must be within the same `finally` block, or otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="9ab6d-471">Oggetto `goto` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-471">A `goto` statement is executed as follows:</span></span>

*  <span data-ttu-id="9ab6d-472">Se il `goto` istruzione esce da uno o più `try` blocchi con associati `finally` blocchi di controllo viene trasferito inizialmente al `finally` blocco di più interna `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-472">If the `goto` statement exits one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="9ab6d-473">Quando e se il controllo raggiunge il punto finale di un `finally` blocco di controllo viene trasferito al `finally` blocco di inclusione successivo `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-473">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="9ab6d-474">Questo processo viene ripetuto finché il `finally` blocchi di tutti interessati `try` istruzioni sono state eseguite.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-474">This process is repeated until the `finally` blocks of all intervening `try` statements have been executed.</span></span>
*  <span data-ttu-id="9ab6d-475">Il controllo viene trasferito alla destinazione del `goto` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-475">Control is transferred to the target of the `goto` statement.</span></span>

<span data-ttu-id="9ab6d-476">Poiché un `goto` istruzione in modo incondizionato trasferisce il controllo in un' posizione, il punto finale di un `goto` istruzione non è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-476">Because a `goto` statement unconditionally transfers control elsewhere, the end point of a `goto` statement is never reachable.</span></span>

### <a name="the-return-statement"></a><span data-ttu-id="9ab6d-477">L'istruzione return</span><span class="sxs-lookup"><span data-stu-id="9ab6d-477">The return statement</span></span>

<span data-ttu-id="9ab6d-478">Il `return` istruzione restituisce il controllo al chiamante corrente della funzione in cui il `return` viene visualizzata l'istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-478">The `return` statement returns control to the current caller of the function in which the `return` statement appears.</span></span>

```antlr
return_statement
    : 'return' expression? ';'
    ;
```

<span data-ttu-id="9ab6d-479">Oggetto `return` istruzione senza un'espressione può essere utilizzata solo in un membro di funzione che calcola un valore, vale a dire, un metodo con tipo di risultato ([corpo del metodo](classes.md#method-body)) `void`, il `set` della funzione di accesso di una proprietà o un indicizzatore, il `add` e `remove` funzioni di accesso di un evento, un costruttore di istanza, un costruttore statico o un distruttore.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-479">A `return` statement with no expression can be used only in a function member that does not compute a value, that is, a method with the result type ([Method body](classes.md#method-body)) `void`, the `set` accessor of a property or indexer, the `add` and `remove` accessors of an event, an instance constructor, a static constructor, or a destructor.</span></span>

<span data-ttu-id="9ab6d-480">Oggetto `return` istruzione con un'espressione può essere usata solo in un membro di funzione che calcola un valore, vale a dire, un metodo con un tipo di risultato diverso da void, il `get` funzione di accesso di una proprietà o indicizzatore o un operatore definito dall'utente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-480">A `return` statement with an expression can only be used in a function member that computes a value, that is, a method with a non-void result type, the `get` accessor of a property or indexer, or a user-defined operator.</span></span> <span data-ttu-id="9ab6d-481">Una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) deve esistere dal tipo dell'espressione nel tipo restituito del membro di funzione che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-481">An implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) must exist from the type of the expression to the return type of the containing function member.</span></span>

<span data-ttu-id="9ab6d-482">Restituire può anche essere usate all'interno delle espressioni di funzioni anonime ([espressioni di funzioni anonime](expressions.md#anonymous-function-expressions)) e partecipare a determinare quali conversioni esistono per tali funzioni.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-482">Return statements can also be used in the body of anonymous function expressions ([Anonymous function expressions](expressions.md#anonymous-function-expressions)), and participate in determining which conversions exist for those functions.</span></span>

<span data-ttu-id="9ab6d-483">Tratta di un errore in fase di compilazione per un `return` istruzione venga visualizzato un `finally` blocco ([l'istruzione try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-483">It is a compile-time error for a `return` statement to appear in a `finally` block ([The try statement](statements.md#the-try-statement)).</span></span>

<span data-ttu-id="9ab6d-484">Oggetto `return` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-484">A `return` statement is executed as follows:</span></span>

*  <span data-ttu-id="9ab6d-485">Se il `return` istruzione specifica un'espressione, l'espressione viene valutata e il valore risultante viene convertito nel tipo restituito della funzione contenitore da parte di una conversione implicita.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-485">If the `return` statement specifies an expression, the expression is evaluated and the resulting value is converted to the return type of the containing function by an implicit conversion.</span></span> <span data-ttu-id="9ab6d-486">Il risultato della conversione diventa il valore del risultato prodotto dalla funzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-486">The result of the conversion becomes the result value produced by the function.</span></span>
*  <span data-ttu-id="9ab6d-487">Se il `return` istruzione è racchiuso da uno o più `try` o `catch` con i blocchi associati `finally` blocchi di controllo viene trasferito inizialmente al `finally` blocco di più interna `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-487">If the `return` statement is enclosed by one or more `try` or `catch` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="9ab6d-488">Quando e se il controllo raggiunge il punto finale di un `finally` blocco di controllo viene trasferito al `finally` blocco di inclusione successivo `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-488">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="9ab6d-489">Questo processo viene ripetuto finché il `finally` blocchi di inclusione tutti `try` istruzioni sono state eseguite.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-489">This process is repeated until the `finally` blocks of all enclosing `try` statements have been executed.</span></span>
*  <span data-ttu-id="9ab6d-490">Se la funzione che lo contiene non è una funzione asincrona, il controllo viene restituito al chiamante della funzione contenitore insieme al valore di risultato, se presente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-490">If the containing function is not an async function, control is returned to the caller of the containing function along with the result value, if any.</span></span>
*  <span data-ttu-id="9ab6d-491">Se la funzione che lo contiene è una funzione asincrona, il controllo venga restituito al chiamante corrente e il valore del risultato, se presente, viene registrato nell'attività restituito come descritto in ([enumeratore (interfacce)](classes.md#enumerator-interfaces)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-491">If the containing function is an async function, control is returned to the current caller, and the result value, if any, is recorded in the return task as described in ([Enumerator interfaces](classes.md#enumerator-interfaces)).</span></span>

<span data-ttu-id="9ab6d-492">Poiché un `return` istruzione in modo incondizionato trasferisce il controllo in un' posizione, il punto finale di un `return` istruzione non è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-492">Because a `return` statement unconditionally transfers control elsewhere, the end point of a `return` statement is never reachable.</span></span>

### <a name="the-throw-statement"></a><span data-ttu-id="9ab6d-493">L'istruzione throw</span><span class="sxs-lookup"><span data-stu-id="9ab6d-493">The throw statement</span></span>

<span data-ttu-id="9ab6d-494">Il `throw` istruzione genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-494">The `throw` statement throws an exception.</span></span>

```antlr
throw_statement
    : 'throw' expression? ';'
    ;
```

<span data-ttu-id="9ab6d-495">Oggetto `throw` istruzione con un'espressione genera il valore restituito dalla valutazione dell'espressione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-495">A `throw` statement with an expression throws the value produced by evaluating the expression.</span></span> <span data-ttu-id="9ab6d-496">L'espressione deve indicare un valore del tipo di classe `System.Exception`, di un tipo di classe che deriva da `System.Exception` o di un tipo di parametro di tipo con `System.Exception` (o una sottoclasse relativa) come classe di base effettivo.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-496">The expression must denote a value of the class type `System.Exception`, of a class type that derives from `System.Exception` or of a type parameter type that has `System.Exception` (or a subclass thereof) as its effective base class.</span></span> <span data-ttu-id="9ab6d-497">Se la valutazione dell'espressione produce `null`, un `System.NullReferenceException` invece generata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-497">If evaluation of the expression produces `null`, a `System.NullReferenceException` is thrown instead.</span></span>

<span data-ttu-id="9ab6d-498">Oggetto `throw` istruzione senza un'espressione può essere utilizzata solo in un `catch` block, nel qual caso tale istruzione genera nuovamente l'eccezione che viene attualmente gestito da tale `catch` blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-498">A `throw` statement with no expression can be used only in a `catch` block, in which case that statement re-throws the exception that is currently being handled by that `catch` block.</span></span>

<span data-ttu-id="9ab6d-499">Poiché un `throw` istruzione in modo incondizionato trasferisce il controllo in un' posizione, il punto finale di un `throw` istruzione non è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-499">Because a `throw` statement unconditionally transfers control elsewhere, the end point of a `throw` statement is never reachable.</span></span>

<span data-ttu-id="9ab6d-500">Quando viene generata un'eccezione, il controllo viene trasferito al primo `catch` clausola in un'inclusione `try` informativa in grado di gestire l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-500">When an exception is thrown, control is transferred to the first `catch` clause in an enclosing `try` statement that can handle the exception.</span></span> <span data-ttu-id="9ab6d-501">Il processo che viene eseguita dal punto dell'eccezione generata per il punto di trasferimento del controllo in un gestore di eccezioni appropriato è noto come ***propagazione delle eccezioni***.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-501">The process that takes place from the point of the exception being thrown to the point of transferring control to a suitable exception handler is known as ***exception propagation***.</span></span> <span data-ttu-id="9ab6d-502">La propagazione di un'eccezione è costituito da valutare più volte i passaggi seguenti solo un `catch` clausola che corrisponde all'eccezione viene trovato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-502">Propagation of an exception consists of repeatedly evaluating the following steps until a `catch` clause that matches the exception is found.</span></span> <span data-ttu-id="9ab6d-503">In questa descrizione, il ***throw punto*** iniziale corrisponde alla posizione in corrispondenza del quale viene generata l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-503">In this description, the ***throw point*** is initially the location at which the exception is thrown.</span></span>

*  <span data-ttu-id="9ab6d-504">Nel membro della funzione corrente, ogni `try` viene esaminato l'istruzione che include il punto di generazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-504">In the current function member, each `try` statement that encloses the throw point is examined.</span></span> <span data-ttu-id="9ab6d-505">Istruzione for each `S`, che inizia con più interna `try` istruzione iniziale e finendo con più esterna `try` istruzione, vengono valutati i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-505">For each statement `S`, starting with the innermost `try` statement and ending with the outermost `try` statement, the following steps are evaluated:</span></span>

   * <span data-ttu-id="9ab6d-506">Se il `try` blocco `S` racchiude il punto di generazione e S contiene uno o più `catch` clausole, il `catch` clausole vengono esaminate nell'ordine di visualizzazione per individuare un gestore appropriato per l'eccezione, in base alle regole specificate Sezione [l'istruzione try](statements.md#the-try-statement).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-506">If the `try` block of `S` encloses the throw point and if S has one or more `catch` clauses, the `catch` clauses are examined in order of appearance to locate a suitable handler for the exception, according to the rules specified in Section [The try statement](statements.md#the-try-statement).</span></span> <span data-ttu-id="9ab6d-507">Se un oggetto corrispondente `catch` clausola si trova, la propagazione delle eccezioni viene completata per il trasferimento del controllo al blocco di cui `catch` clausola.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-507">If a matching `catch` clause is located, the exception propagation is completed by transferring control to the block of that `catch` clause.</span></span>

   * <span data-ttu-id="9ab6d-508">In caso contrario, se il `try` blocco o un `catch` blocco `S` include il punto di generazione e, se `S` ha una `finally` blocco di controllo viene trasferito al `finally` blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-508">Otherwise, if the `try` block or a `catch` block of `S` encloses the throw point and if `S` has a `finally` block, control is transferred to the `finally` block.</span></span> <span data-ttu-id="9ab6d-509">Se il `finally` blocco Genera eccezione un altro, interrompere l'elaborazione dell'eccezione corrente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-509">If the `finally` block throws another exception, processing of the current exception is terminated.</span></span> <span data-ttu-id="9ab6d-510">In caso contrario, quando il controllo raggiunge il punto finale del `finally` blocco, continua l'elaborazione dell'eccezione corrente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-510">Otherwise, when control reaches the end point of the `finally` block, processing of the current exception is continued.</span></span>

*  <span data-ttu-id="9ab6d-511">Se un gestore di eccezioni non è accessibile dalla chiamata alla funzione corrente, la chiamata alla funzione viene terminata e si verifica una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-511">If an exception handler was not located in the current function invocation, the function invocation is terminated, and one of the following occurs:</span></span>

   * <span data-ttu-id="9ab6d-512">Se la funzione corrente è non asincrone, i passaggi precedenti vengono ripetuti per il chiamante della funzione con un punto di generazione corrispondente all'istruzione da cui è stato richiamato il membro di funzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-512">If the current function is non-async, the steps above are repeated for the caller of the function with a throw point corresponding to the statement from which the function member was invoked.</span></span>

   * <span data-ttu-id="9ab6d-513">Se la funzione corrente è asincrono e restituzione di task, l'eccezione viene registrata nell'attività restituito, che viene inserito in uno stato faulted o cancelled come descritto in [enumeratore (interfacce)](classes.md#enumerator-interfaces).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-513">If the current function is async and task-returning, the exception is recorded in the return task, which is put into a faulted or cancelled state as described in [Enumerator interfaces](classes.md#enumerator-interfaces).</span></span>

   * <span data-ttu-id="9ab6d-514">Se la funzione corrente è asincrono e che restituisce void, il contesto di sincronizzazione del thread corrente è una notifica come descritto in [interfacce enumerabili](classes.md#enumerable-interfaces).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-514">If the current function is async and void-returning, the synchronization context of the current thread is notified as described in [Enumerable interfaces](classes.md#enumerable-interfaces).</span></span>

*  <span data-ttu-id="9ab6d-515">Se l'elaborazione delle eccezioni termina tutte le chiamate al membro di funzione nel thread corrente, che indica che il thread non ha alcun gestore per l'eccezione, quindi il thread stesso verrà terminato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-515">If the exception processing terminates all function member invocations in the current thread, indicating that the thread has no handler for the exception, then the thread is itself terminated.</span></span> <span data-ttu-id="9ab6d-516">L'impatto di tale azione è definito dall'implementazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-516">The impact of such termination is implementation-defined.</span></span>

## <a name="the-try-statement"></a><span data-ttu-id="9ab6d-517">L'istruzione try</span><span class="sxs-lookup"><span data-stu-id="9ab6d-517">The try statement</span></span>

<span data-ttu-id="9ab6d-518">Il `try` istruzione fornisce un meccanismo per intercettare le eccezioni che si verificano durante l'esecuzione di un blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-518">The `try` statement provides a mechanism for catching exceptions that occur during execution of a block.</span></span> <span data-ttu-id="9ab6d-519">Inoltre, il `try` istruzione offre la possibilità di specificare un blocco di codice che viene sempre eseguito quando il controllo lascia il `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-519">Furthermore, the `try` statement provides the ability to specify a block of code that is always executed when control leaves the `try` statement.</span></span>

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

<span data-ttu-id="9ab6d-520">Esistono tre forme possibili di `try` istruzioni:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-520">There are three possible forms of `try` statements:</span></span>

*  <span data-ttu-id="9ab6d-521">Oggetto `try` blocco seguito da uno o più `catch` blocchi.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-521">A `try` block followed by one or more `catch` blocks.</span></span>
*  <span data-ttu-id="9ab6d-522">Oggetto `try` blocco seguito da un `finally` blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-522">A `try` block followed by a `finally` block.</span></span>
*  <span data-ttu-id="9ab6d-523">Oggetto `try` blocco seguito da uno o più `catch` blocchi seguita da un `finally` blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-523">A `try` block followed by one or more `catch` blocks followed by a `finally` block.</span></span>

<span data-ttu-id="9ab6d-524">Quando un `catch` clausola consente di specificare un *exception_specifier*, il tipo deve essere `System.Exception`, un tipo che deriva da `System.Exception` o un tipo di parametro di tipo che ha `System.Exception` (o una sottoclasse relativa) come relativi effettiva classe di base.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-524">When a `catch` clause specifies an *exception_specifier*, the type must be `System.Exception`, a type that derives from `System.Exception` or a type parameter type that has `System.Exception` (or a subclass thereof) as its effective base class.</span></span>

<span data-ttu-id="9ab6d-525">Quando un `catch` clausola specifica sia un' *exception_specifier* con un *identificatore*, un ***variabile dell'eccezione*** del nome specificato e il tipo è dichiarato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-525">When a `catch` clause specifies both an *exception_specifier* with an *identifier*, an ***exception variable*** of the given name and type is declared.</span></span> <span data-ttu-id="9ab6d-526">La variabile dell'eccezione corrisponde a una variabile locale con un ambito che ricopre il `catch` clausola.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-526">The exception variable corresponds to a local variable with a scope that extends over the `catch` clause.</span></span> <span data-ttu-id="9ab6d-527">Durante l'esecuzione del *exception_filter* e *blocco*, la variabile dell'eccezione rappresenta l'eccezione attualmente gestita.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-527">During execution of the *exception_filter* and *block*, the exception variable represents the exception currently being handled.</span></span> <span data-ttu-id="9ab6d-528">Ai fini di controllo dell'assegnazione definita, la variabile dell'eccezione viene considerata assegnata nel relativo ambito.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-528">For purposes of definite assignment checking, the exception variable is considered definitely assigned in its entire scope.</span></span>

<span data-ttu-id="9ab6d-529">A meno che un `catch` clausola include un nome di variabile di eccezione, non è possibile accedere all'oggetto eccezione nel filtro e `catch` blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-529">Unless a `catch` clause includes an exception variable name, it is impossible to access the exception object in the filter and `catch` block.</span></span>

<span data-ttu-id="9ab6d-530">Oggetto `catch` clausola che non specifica un' *exception_specifier* viene chiamato un generale `catch` clausola.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-530">A `catch` clause that does not specify an *exception_specifier* is called a general `catch` clause.</span></span>

<span data-ttu-id="9ab6d-531">Alcuni linguaggi di programmazione possono supportare le eccezioni che non sono rappresentabili come un oggetto derivato da `System.Exception`, anche se tali eccezioni non possono mai essere generate dal codice c#.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-531">Some programming languages may support exceptions that are not representable as an object derived from `System.Exception`, although such exceptions could never be generated by C# code.</span></span> <span data-ttu-id="9ab6d-532">Generale `catch` clausola può essere usata per intercettare tali eccezioni.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-532">A general `catch` clause may be used to catch such exceptions.</span></span> <span data-ttu-id="9ab6d-533">Di conseguenza, una general `catch` clausola è semanticamente diversa da quella più specifica il tipo `System.Exception`, in quanto nel primo caso può inoltre rilevare eccezioni da altri linguaggi.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-533">Thus, a general `catch` clause is semantically different from one that specifies the type `System.Exception`, in that the former may also catch exceptions from other languages.</span></span>

<span data-ttu-id="9ab6d-534">Per individuare un gestore per un'eccezione, `catch` clausole vengono esaminate in ordine lessicale.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-534">In order to locate a handler for an exception, `catch` clauses are examined in lexical order.</span></span> <span data-ttu-id="9ab6d-535">Se un `catch` clausola specifica un tipo, ma nessun filtro eccezioni, è un errore in fase di compilazione per una versione successiva `catch` clausola nella stessa `try` istruzione per specificare un tipo che equivale a o deriva da, indipendente dai tipi.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-535">If a `catch` clause specifies a type but no exception filter, it is a compile-time error for a later `catch` clause in the same `try` statement to specify a type that is the same as, or is derived from, that type.</span></span> <span data-ttu-id="9ab6d-536">Se un `catch` clausola specifica alcun tipo e alcun filtro, deve essere l'ultimo `catch` clausola tale `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-536">If a `catch` clause specifies no type and no filter, it must be the last `catch` clause for that `try` statement.</span></span>

<span data-ttu-id="9ab6d-537">All'interno di un `catch` blocco, una `throw` istruzione ([l'istruzione throw](statements.md#the-throw-statement)) senza un'espressione è utilizzabile per generare nuovamente l'eccezione che ha rilevato il `catch` blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-537">Within a `catch` block, a `throw` statement ([The throw statement](statements.md#the-throw-statement)) with no expression can be used to re-throw the exception that was caught by the `catch` block.</span></span> <span data-ttu-id="9ab6d-538">Le assegnazioni a una variabile dell'eccezione non alterano l'eccezione che viene generata nuovamente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-538">Assignments to an exception variable do not alter the exception that is re-thrown.</span></span>

<span data-ttu-id="9ab6d-539">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="9ab6d-539">In the example</span></span>
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
<span data-ttu-id="9ab6d-540">il metodo `F` rileva un'eccezione, scrive alcune informazioni di diagnostica nella console, consente di modificare la variabile dell'eccezione e genera nuovamente l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-540">the method `F` catches an exception, writes some diagnostic information to the console, alters the exception variable, and re-throws the exception.</span></span> <span data-ttu-id="9ab6d-541">L'eccezione che viene generato nuovamente è l'eccezione originale, in modo che l'output del programma:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-541">The exception that is re-thrown is the original exception, so the output produced is:</span></span>
```
Exception in F: G
Exception in Main: G
```

<span data-ttu-id="9ab6d-542">Se non fosse generata il primo blocco catch `e` invece di generare nuovamente l'eccezione corrente, l'output ottenuto sarà il seguente:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-542">If the first catch block had thrown `e` instead of rethrowing the current exception, the output produced would be as follows:</span></span>
```csharp
Exception in F: G
Exception in Main: F
```

<span data-ttu-id="9ab6d-543">Tratta di un errore in fase di compilazione per un `break`, `continue`, o `goto` istruzione per trasferire il controllo fuori un `finally` blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-543">It is a compile-time error for a `break`, `continue`, or `goto` statement to transfer control out of a `finally` block.</span></span> <span data-ttu-id="9ab6d-544">Quando un `break`, `continue`, o `goto` istruzione si trova un `finally` blocco, la destinazione dell'istruzione devono essere all'interno della stessa `finally` blocco, o in caso contrario, verrà generato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-544">When a `break`, `continue`, or `goto` statement occurs in a `finally` block, the target of the statement must be within the same `finally` block, or otherwise a compile-time error occurs.</span></span>

<span data-ttu-id="9ab6d-545">Tratta di un errore in fase di compilazione per un `return` istruzione all'interno un `finally` blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-545">It is a compile-time error for a `return` statement to occur in a `finally` block.</span></span>

<span data-ttu-id="9ab6d-546">Oggetto `try` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-546">A `try` statement is executed as follows:</span></span>

*  <span data-ttu-id="9ab6d-547">Per il controllo viene trasferito il `try` blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-547">Control is transferred to the `try` block.</span></span>
*  <span data-ttu-id="9ab6d-548">Quando e se il controllo raggiunge il punto finale del `try` blocco:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-548">When and if control reaches the end point of the `try` block:</span></span>
   *  <span data-ttu-id="9ab6d-549">Se il `try` istruzione presenta un `finally` blocco, il `finally` blocco viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-549">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
   *  <span data-ttu-id="9ab6d-550">Il controllo viene trasferito al punto finale del `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-550">Control is transferred to the end point of the `try` statement.</span></span>

*  <span data-ttu-id="9ab6d-551">Se un'eccezione viene propagata per il `try` durante l'esecuzione dell'istruzione il `try` blocco:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-551">If an exception is propagated to the `try` statement during execution of the `try` block:</span></span>
   *  <span data-ttu-id="9ab6d-552">Il `catch` clausole, se presenti, vengono esaminate nell'ordine di visualizzazione per individuare un gestore appropriato per l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-552">The `catch` clauses, if any, are examined in order of appearance to locate a suitable handler for the exception.</span></span> <span data-ttu-id="9ab6d-553">Se un `catch` clausola non specifica un tipo o specifica il tipo di eccezione o un tipo di base del tipo di eccezione:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-553">If a `catch` clause does not specify a type, or specifies the exception type or a base type of the exception type:</span></span>
      *  <span data-ttu-id="9ab6d-554">Se il `catch` clausola dichiara una variabile di eccezione, l'oggetto eccezione viene assegnato alla variabile dell'eccezione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-554">If the `catch` clause declares an exception variable, the exception object is assigned to the exception variable.</span></span>
      *  <span data-ttu-id="9ab6d-555">Se il `catch` clausola dichiara un filtro eccezioni, il filtro viene valutato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-555">If the `catch` clause declares an exception filter, the filter is evaluated.</span></span> <span data-ttu-id="9ab6d-556">Se viene restituito `false`, la clausola catch non è una corrispondenza e la ricerca continua tramite qualsiasi successiva `catch` clausole per un gestore appropriato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-556">If it evaluates to `false`, the catch clause is not a match, and the search continues through any subsequent `catch` clauses for a suitable handler.</span></span>
      *  <span data-ttu-id="9ab6d-557">In caso contrario, il `catch` clausola viene considerata una corrispondenza e il controllo viene trasferito sul corrispondente `catch` blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-557">Otherwise, the `catch` clause is considered a match, and control is transferred to the matching `catch` block.</span></span>
      *  <span data-ttu-id="9ab6d-558">Quando e se il controllo raggiunge il punto finale del `catch` blocco:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-558">When and if control reaches the end point of the `catch` block:</span></span>
         * <span data-ttu-id="9ab6d-559">Se il `try` istruzione presenta un `finally` blocco, il `finally` blocco viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-559">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
         * <span data-ttu-id="9ab6d-560">Il controllo viene trasferito al punto finale del `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-560">Control is transferred to the end point of the `try` statement.</span></span>
      *  <span data-ttu-id="9ab6d-561">Se un'eccezione viene propagata per il `try` durante l'esecuzione dell'istruzione il `catch` blocco:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-561">If an exception is propagated to the `try` statement during execution of the `catch` block:</span></span>
         *  <span data-ttu-id="9ab6d-562">Se il `try` istruzione presenta un `finally` blocco, il `finally` blocco viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-562">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
         *  <span data-ttu-id="9ab6d-563">L'eccezione viene propagata alla successiva inclusione `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-563">The exception is propagated to the next enclosing `try` statement.</span></span>
   *  <span data-ttu-id="9ab6d-564">Se il `try` istruzione non ha alcun `catch` clausole o se nessun `catch` clausola corrisponde all'eccezione:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-564">If the `try` statement has no `catch` clauses or if no `catch` clause matches the exception:</span></span>
      *  <span data-ttu-id="9ab6d-565">Se il `try` istruzione presenta un `finally` blocco, il `finally` blocco viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-565">If the `try` statement has a `finally` block, the `finally` block is executed.</span></span>
      *  <span data-ttu-id="9ab6d-566">L'eccezione viene propagata alla successiva inclusione `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-566">The exception is propagated to the next enclosing `try` statement.</span></span>

<span data-ttu-id="9ab6d-567">Le istruzioni di una `finally` blocco vengono sempre eseguiti quando il controllo lascia un `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-567">The statements of a `finally` block are always executed when control leaves a `try` statement.</span></span> <span data-ttu-id="9ab6d-568">Ciò è vero se il trasferimento del controllo viene eseguita come risultato dell'esecuzione normale, in seguito all'esecuzione una `break`, `continue`, `goto`, o `return` istruzione, o come risultato la propagazione di un'eccezione del `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-568">This is true whether the control transfer occurs as a result of normal execution, as a result of executing a `break`, `continue`, `goto`, or `return` statement, or as a result of propagating an exception out of the `try` statement.</span></span>

<span data-ttu-id="9ab6d-569">Se viene generata un'eccezione durante l'esecuzione di un `finally` blocca e non rilevata all'interno della stessa blocco finally, l'eccezione viene propagata alla successiva inclusione `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-569">If an exception is thrown during execution of a `finally` block, and is not caught within the same finally block, the exception is propagated to the next enclosing `try` statement.</span></span> <span data-ttu-id="9ab6d-570">Se un'altra eccezione era in corso la propagazione, tale eccezione viene persa.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-570">If another exception was in the process of being propagated, that exception is lost.</span></span> <span data-ttu-id="9ab6d-571">Il processo di propagazione di un'eccezione viene trattato ulteriormente nella descrizione del `throw` istruzione ([l'istruzione throw](statements.md#the-throw-statement)).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-571">The process of propagating an exception is discussed further in the description of the `throw` statement ([The throw statement](statements.md#the-throw-statement)).</span></span>

<span data-ttu-id="9ab6d-572">Il `try` block di una `try` istruzione è raggiungibile se il `try` istruzione sia raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-572">The `try` block of a `try` statement is reachable if the `try` statement is reachable.</span></span>

<span data-ttu-id="9ab6d-573">Oggetto `catch` blocco di un `try` istruzione è raggiungibile se il `try` istruzione sia raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-573">A `catch` block of a `try` statement is reachable if the `try` statement is reachable.</span></span>

<span data-ttu-id="9ab6d-574">Il `finally` block di una `try` istruzione è raggiungibile se il `try` istruzione sia raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-574">The `finally` block of a `try` statement is reachable if the `try` statement is reachable.</span></span>

<span data-ttu-id="9ab6d-575">Il punto finale di un `try` istruzione è raggiungibile solo se entrambe le operazioni seguenti sono vere:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-575">The end point of a `try` statement is reachable if both of the following are true:</span></span>

*  <span data-ttu-id="9ab6d-576">Il punto finale del `try` blocco è raggiungibile o punto alla fine di almeno un `catch` blocco sia raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-576">The end point of the `try` block is reachable or the end point of at least one `catch` block is reachable.</span></span>
*  <span data-ttu-id="9ab6d-577">Se un `finally` blocco è presente, il punto finale del `finally` blocco sia raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-577">If a `finally` block is present, the end point of the `finally` block is reachable.</span></span>

## <a name="the-checked-and-unchecked-statements"></a><span data-ttu-id="9ab6d-578">Le istruzioni checked e unchecked</span><span class="sxs-lookup"><span data-stu-id="9ab6d-578">The checked and unchecked statements</span></span>

<span data-ttu-id="9ab6d-579">Il `checked` e `unchecked` istruzioni usate per controllare le ***contesto di controllo dell'overflow*** per le conversioni e operazioni aritmetiche di tipo integrale.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-579">The `checked` and `unchecked` statements are used to control the ***overflow checking context*** for integral-type arithmetic operations and conversions.</span></span>

```antlr
checked_statement
    : 'checked' block
    ;

unchecked_statement
    : 'unchecked' block
    ;
```

<span data-ttu-id="9ab6d-580">Il `checked` istruzione fa sì che tutte le espressioni nel *blocco* deve essere valutata in un contesto controllato e il `unchecked` istruzione fa sì che tutte le espressioni nel *blocco* deve essere valutata un contesto non verificato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-580">The `checked` statement causes all expressions in the *block* to be evaluated in a checked context, and the `unchecked` statement causes all expressions in the *block* to be evaluated in an unchecked context.</span></span>

<span data-ttu-id="9ab6d-581">Il `checked` e `unchecked` istruzioni rappresentano l'equivalente per il `checked` e `unchecked` operatori ([gli operatori checked e unchecked](expressions.md#the-checked-and-unchecked-operators)), ad eccezione del fatto che operano in base a blocchi anziché le espressioni .</span><span class="sxs-lookup"><span data-stu-id="9ab6d-581">The `checked` and `unchecked` statements are precisely equivalent to the `checked` and `unchecked` operators ([The checked and unchecked operators](expressions.md#the-checked-and-unchecked-operators)), except that they operate on blocks instead of expressions.</span></span>

## <a name="the-lock-statement"></a><span data-ttu-id="9ab6d-582">Istruzione lock</span><span class="sxs-lookup"><span data-stu-id="9ab6d-582">The lock statement</span></span>

<span data-ttu-id="9ab6d-583">Il `lock` istruzione Ottiene il blocco a esclusione reciproca per un determinato oggetto, esegue un'istruzione e quindi rilascia il blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-583">The `lock` statement obtains the mutual-exclusion lock for a given object, executes a statement, and then releases the lock.</span></span>

```antlr
lock_statement
    : 'lock' '(' expression ')' embedded_statement
    ;
```

<span data-ttu-id="9ab6d-584">L'espressione di un `lock` istruzione deve indicare un valore di un tipo noto per essere un *reference_type*.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-584">The expression of a `lock` statement must denote a value of a type known to be a *reference_type*.</span></span> <span data-ttu-id="9ab6d-585">Nessuna conversione boxing implicito ([conversioni Boxing](conversions.md#boxing-conversions)) non viene mai eseguita per l'espressione di un `lock` istruzione e, pertanto è un errore in fase di compilazione per l'espressione indicare un valore di un *value_type*.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-585">No implicit boxing conversion ([Boxing conversions](conversions.md#boxing-conversions)) is ever performed for the expression of a `lock` statement, and thus it is a compile-time error for the expression to denote a value of a *value_type*.</span></span>

<span data-ttu-id="9ab6d-586">Oggetto `lock` istruzione del form</span><span class="sxs-lookup"><span data-stu-id="9ab6d-586">A `lock` statement of the form</span></span>
```csharp
lock (x) ...
```
<span data-ttu-id="9ab6d-587">in cui `x` è un'espressione di un *reference_type*, equivale a</span><span class="sxs-lookup"><span data-stu-id="9ab6d-587">where `x` is an expression of a *reference_type*, is precisely equivalent to</span></span>
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
<span data-ttu-id="9ab6d-588">con la differenza che `x` viene valutato una sola volta.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-588">except that `x` is only evaluated once.</span></span>

<span data-ttu-id="9ab6d-589">Mentre è attivo un blocco a esclusione reciproca, l'esecuzione di codice nello stesso thread di esecuzione nonché ottenere e rilasciare il blocco.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-589">While a mutual-exclusion lock is held, code executing in the same execution thread can also obtain and release the lock.</span></span> <span data-ttu-id="9ab6d-590">Tuttavia, l'esecuzione di codice in altri thread viene impedito ottenendo il blocco fino a quando il blocco viene rilasciato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-590">However, code executing in other threads is blocked from obtaining the lock until the lock is released.</span></span>

<span data-ttu-id="9ab6d-591">Blocco `System.Type` oggetti per sincronizzare l'accesso ai dati statici non è consigliata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-591">Locking `System.Type` objects in order to synchronize access to static data is not recommended.</span></span> <span data-ttu-id="9ab6d-592">Impedire l'altro codice sullo stesso tipo, che può causare deadlock.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-592">Other code might lock on the same type, which can result in deadlock.</span></span> <span data-ttu-id="9ab6d-593">Un approccio migliore consiste per sincronizzare l'accesso ai dati statici il blocco di un oggetto statico privato.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-593">A better approach is to synchronize access to static data by locking a private static object.</span></span> <span data-ttu-id="9ab6d-594">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-594">For example:</span></span>
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

## <a name="the-using-statement"></a><span data-ttu-id="9ab6d-595">Istruzione using</span><span class="sxs-lookup"><span data-stu-id="9ab6d-595">The using statement</span></span>

<span data-ttu-id="9ab6d-596">Il `using` istruzione Ottiene uno o più risorse, esegue un'istruzione e quindi elimina la risorsa.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-596">The `using` statement obtains one or more resources, executes a statement, and then disposes of the resource.</span></span>

```antlr
using_statement
    : 'using' '(' resource_acquisition ')' embedded_statement
    ;

resource_acquisition
    : local_variable_declaration
    | expression
    ;
```

<span data-ttu-id="9ab6d-597">Oggetto ***resource*** è una classe o struct che implementa `System.IDisposable`, che include un singolo metodo senza parametri denominato `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-597">A ***resource*** is a class or struct that implements `System.IDisposable`, which includes a single parameterless method named `Dispose`.</span></span> <span data-ttu-id="9ab6d-598">Il codice che usa una risorsa può chiamare `Dispose` per indicare che la risorsa non è più necessario.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-598">Code that is using a resource can call `Dispose` to indicate that the resource is no longer needed.</span></span> <span data-ttu-id="9ab6d-599">Se `Dispose` non viene chiamato, quindi eliminazione automatica si verifica alla fine di conseguenza la garbage collection.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-599">If `Dispose` is not called, then automatic disposal eventually occurs as a consequence of garbage collection.</span></span>

<span data-ttu-id="9ab6d-600">Se la forma di *resource_acquisition* viene *local_variable_declaration* quindi il tipo del *local_variable_declaration* deve essere `dynamic` o un tipo che può essere convertito in modo implicito nel `System.IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-600">If the form of *resource_acquisition* is *local_variable_declaration* then the type of the *local_variable_declaration* must be either `dynamic` or a type that can be implicitly converted to `System.IDisposable`.</span></span> <span data-ttu-id="9ab6d-601">Se la forma di *resource_acquisition* viene *expression* questa espressione deve essere convertibile in modo implicito in `System.IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-601">If the form of *resource_acquisition* is *expression* then this expression must be implicitly convertible to `System.IDisposable`.</span></span>

<span data-ttu-id="9ab6d-602">Le variabili locali dichiarate un *resource_acquisition* sono di sola lettura e deve includere un inizializzatore.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-602">Local variables declared in a *resource_acquisition* are read-only, and must include an initializer.</span></span> <span data-ttu-id="9ab6d-603">Si verifica un errore in fase di compilazione se l'istruzione incorporata tenta di modificare queste variabili locali (tramite l'assegnazione o la `++` e `--` operatori), accettare l'indirizzo di loro o passarli come `ref` o `out` parametri.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-603">A compile-time error occurs if the embedded statement attempts to modify these local variables (via assignment or the `++` and `--` operators) , take the address of them, or pass them as `ref` or `out` parameters.</span></span>

<span data-ttu-id="9ab6d-604">Oggetto `using` istruzione viene convertita in tre parti: acquisizione, utilizzo ed eliminazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-604">A `using` statement is translated into three parts: acquisition, usage, and disposal.</span></span> <span data-ttu-id="9ab6d-605">Uso della risorsa è incluso in modo implicito in un `try` istruzione che include un `finally` clausola.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-605">Usage of the resource is implicitly enclosed in a `try` statement that includes a `finally` clause.</span></span> <span data-ttu-id="9ab6d-606">Ciò `finally` determina l'eliminazione della risorsa.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-606">This `finally` clause disposes of the resource.</span></span> <span data-ttu-id="9ab6d-607">Se un `null` risorsa acquisita, quindi Nessuna chiamata a `Dispose` viene effettuata, e viene generata alcuna eccezione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-607">If a `null` resource is acquired, then no call to `Dispose` is made, and no exception is thrown.</span></span> <span data-ttu-id="9ab6d-608">Se la risorsa è di tipo `dynamic` viene convertito in modo dinamico mediante una conversione implicita dinamica ([conversioni implicite dinamiche](conversions.md#implicit-dynamic-conversions)) a `IDisposable` durante l'acquisizione per garantire che la conversione è esito positivo, prima dell'utilizzo e l'eliminazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-608">If the resource is of type `dynamic` it is dynamically converted through an implicit dynamic conversion ([Implicit dynamic conversions](conversions.md#implicit-dynamic-conversions)) to `IDisposable` during acquisition in order to ensure that the conversion is successful before the usage and disposal.</span></span>

<span data-ttu-id="9ab6d-609">Oggetto `using` istruzione del form</span><span class="sxs-lookup"><span data-stu-id="9ab6d-609">A `using` statement of the form</span></span>
```csharp
using (ResourceType resource = expression) statement
```
<span data-ttu-id="9ab6d-610">corrisponde a uno dei tre possibili espansioni.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-610">corresponds to one of three possible expansions.</span></span> <span data-ttu-id="9ab6d-611">Quando si `ResourceType` è un tipo di valore non nullable, è l'espansione</span><span class="sxs-lookup"><span data-stu-id="9ab6d-611">When `ResourceType` is a non-nullable value type, the expansion is</span></span>
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

<span data-ttu-id="9ab6d-612">In caso contrario, quando `ResourceType` è un tipo valore nullable o un tipo riferimento diverso da `dynamic`, l'espansione è</span><span class="sxs-lookup"><span data-stu-id="9ab6d-612">Otherwise, when `ResourceType` is a nullable value type or a reference type other than `dynamic`, the expansion is</span></span>
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

<span data-ttu-id="9ab6d-613">In caso contrario, quando `ResourceType` è `dynamic`, l'espansione è</span><span class="sxs-lookup"><span data-stu-id="9ab6d-613">Otherwise, when `ResourceType` is `dynamic`, the expansion is</span></span>
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

<span data-ttu-id="9ab6d-614">In entrambe le espansioni, la `resource` variabile è di sola lettura in un'istruzione incorporata e il `d` variabile è inaccessibile e invisibile, l'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-614">In either expansion, the `resource` variable is read-only in the embedded statement, and the `d` variable is inaccessible in, and invisible to, the embedded statement.</span></span>

<span data-ttu-id="9ab6d-615">Un'implementazione può implementare una determinata istruzione utilizzando in modo diverso, ad esempio, per motivi di prestazioni, purché il comportamento è coerente con l'espansione del precedente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-615">An implementation is permitted to implement a given using-statement differently, e.g. for performance reasons, as long as the behavior is consistent with the above expansion.</span></span>

<span data-ttu-id="9ab6d-616">Oggetto `using` istruzione del form</span><span class="sxs-lookup"><span data-stu-id="9ab6d-616">A `using` statement of the form</span></span>
```csharp
using (expression) statement
```
<span data-ttu-id="9ab6d-617">dispone di tre possibili espansioni stesso.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-617">has the same three possible expansions.</span></span> <span data-ttu-id="9ab6d-618">In questo caso `ResourceType` in modo implicito è il tipo di fase di compilazione di `expression`, se presente.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-618">In this case `ResourceType` is implicitly the compile-time type of the `expression`, if it has one.</span></span> <span data-ttu-id="9ab6d-619">In caso contrario, l'interfaccia `IDisposable` stesso viene utilizzato come il `ResourceType`.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-619">Otherwise the interface `IDisposable` itself is used as the `ResourceType`.</span></span> <span data-ttu-id="9ab6d-620">Il `resource` variabile è inaccessibile e invisibile, l'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-620">The `resource` variable is inaccessible in, and invisible to, the embedded statement.</span></span>

<span data-ttu-id="9ab6d-621">Quando un *resource_acquisition* assume la forma di un *local_variable_declaration*, è possibile acquisire più risorse di un determinato tipo.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-621">When a *resource_acquisition* takes the form of a *local_variable_declaration*, it is possible to acquire multiple resources of a given type.</span></span> <span data-ttu-id="9ab6d-622">Oggetto `using` istruzione del form</span><span class="sxs-lookup"><span data-stu-id="9ab6d-622">A `using` statement of the form</span></span>
```csharp
using (ResourceType r1 = e1, r2 = e2, ..., rN = eN) statement
```
<span data-ttu-id="9ab6d-623">è esattamente equivalente a una sequenza di annidata `using` istruzioni:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-623">is precisely equivalent to a sequence of nested `using` statements:</span></span>
```csharp
using (ResourceType r1 = e1)
    using (ResourceType r2 = e2)
        ...
            using (ResourceType rN = eN)
                statement
```

<span data-ttu-id="9ab6d-624">L'esempio seguente crea un file denominato `log.txt` e scrive due righe di testo nel file.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-624">The example below creates a file named `log.txt` and writes two lines of text to the file.</span></span> <span data-ttu-id="9ab6d-625">Nell'esempio viene quindi apre uno stesso file per la lettura e copia le righe di testo contenute nella console.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-625">The example then opens that same file for reading and copies the contained lines of text to the console.</span></span>
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

<span data-ttu-id="9ab6d-626">Poiché il `TextWriter` e `TextReader` sono classi che implementano le `IDisposable` interfaccia, è possibile usare l'esempio `using` istruzioni per assicurarsi che il file sottostante venga chiusa correttamente seguendo la scrittura o operazioni di lettura.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-626">Since the `TextWriter` and `TextReader` classes implement the `IDisposable` interface, the example can use `using` statements to ensure that the underlying file is properly closed following the write or read operations.</span></span>

## <a name="the-yield-statement"></a><span data-ttu-id="9ab6d-627">L'istruzione yield</span><span class="sxs-lookup"><span data-stu-id="9ab6d-627">The yield statement</span></span>

<span data-ttu-id="9ab6d-628">Il `yield` istruzione viene utilizzata in un blocco iteratore ([blocchi](statements.md#blocks)) per produrre un valore all'oggetto enumeratore ([oggetti dell'enumeratore](classes.md#enumerator-objects)) o un oggetto enumerabile ([degli oggetti enumerabili](classes.md#enumerable-objects)) un iteratore o per segnalare la fine dell'iterazione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-628">The `yield` statement is used in an iterator block ([Blocks](statements.md#blocks)) to yield a value to the enumerator object ([Enumerator objects](classes.md#enumerator-objects)) or enumerable object ([Enumerable objects](classes.md#enumerable-objects)) of an iterator or to signal the end of the iteration.</span></span>

```antlr
yield_statement
    : 'yield' 'return' expression ';'
    | 'yield' 'break' ';'
    ;
```

<span data-ttu-id="9ab6d-629">`yield` non è una parola riservata. ha un significato speciale solo quando utilizzata prima un `return` o `break` (parola chiave).</span><span class="sxs-lookup"><span data-stu-id="9ab6d-629">`yield` is not a reserved word; it has special meaning only when used immediately before a `return` or `break` keyword.</span></span> <span data-ttu-id="9ab6d-630">In altri contesti, `yield` può essere usato come identificatore.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-630">In other contexts, `yield` can be used as an identifier.</span></span>

<span data-ttu-id="9ab6d-631">Esistono diverse restrizioni relative a dove un `yield` istruzione può essere specificata, come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-631">There are several restrictions on where a `yield` statement can appear, as described in the following.</span></span>

*  <span data-ttu-id="9ab6d-632">Tratta di un errore in fase di compilazione per un `yield` istruzione (di qualsiasi forma) all'esterno di un *method_body*, *operator_body* o *accessor_body*</span><span class="sxs-lookup"><span data-stu-id="9ab6d-632">It is a compile-time error for a `yield` statement (of either form) to appear outside a *method_body*, *operator_body* or *accessor_body*</span></span>
*  <span data-ttu-id="9ab6d-633">Tratta di un errore in fase di compilazione per un `yield` istruzione (di qualsiasi forma) all'interno di una funzione anonima.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-633">It is a compile-time error for a `yield` statement (of either form) to appear inside an anonymous function.</span></span>
*  <span data-ttu-id="9ab6d-634">Tratta di un errore in fase di compilazione per un `yield` istruzione (di qualsiasi forma) venga visualizzato nel `finally` clausola di un `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-634">It is a compile-time error for a `yield` statement (of either form) to appear in the `finally` clause of a `try` statement.</span></span>
*  <span data-ttu-id="9ab6d-635">Tratta di un errore in fase di compilazione per un `yield return` trovarsi in qualsiasi punto dell'istruzione una `try` istruzione che contiene gli eventuali `catch` clausole.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-635">It is a compile-time error for a `yield return` statement to appear anywhere in a `try` statement that contains any `catch` clauses.</span></span>

<span data-ttu-id="9ab6d-636">L'esempio seguente mostra alcuni usi validi e non validi di `yield` istruzioni.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-636">The following example shows some valid and invalid uses of `yield` statements.</span></span>

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

<span data-ttu-id="9ab6d-637">Una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) deve esistere dal tipo dell'espressione nella `yield return` istruzione per il tipo di yield ([Yield tipo](classes.md#yield-type)) dell'iteratore.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-637">An implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) must exist from the type of the expression in the `yield return` statement to the yield type ([Yield type](classes.md#yield-type)) of the iterator.</span></span>

<span data-ttu-id="9ab6d-638">Oggetto `yield return` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-638">A `yield return` statement is executed as follows:</span></span>

*  <span data-ttu-id="9ab6d-639">L'espressione specificata nell'istruzione viene valutata in modo implicito convertito nel tipo di yield e assegnato al `Current` proprietà dell'oggetto enumeratore.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-639">The expression given in the statement is evaluated, implicitly converted to the yield type, and assigned to the `Current` property of the enumerator object.</span></span>
*  <span data-ttu-id="9ab6d-640">L'esecuzione del blocco iteratore viene sospesa.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-640">Execution of the iterator block is suspended.</span></span> <span data-ttu-id="9ab6d-641">Se il `yield return` istruzione è all'interno di uno o più `try` blocca, associato `finally` blocchi non vengono eseguiti in questo momento.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-641">If the `yield return` statement is within one or more `try` blocks, the associated `finally` blocks are not executed at this time.</span></span>
*  <span data-ttu-id="9ab6d-642">Il `MoveNext` metodo dell'oggetto enumeratore restituisce `true` al relativo chiamante, che indica che l'oggetto enumeratore viene spostato sull'elemento successivo.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-642">The `MoveNext` method of the enumerator object returns `true` to its caller, indicating that the enumerator object successfully advanced to the next item.</span></span>

<span data-ttu-id="9ab6d-643">La chiamata successiva dell'oggetto enumeratore `MoveNext` metodo riprende l'esecuzione del blocco di iteratore da cui è stato sospeso.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-643">The next call to the enumerator object's `MoveNext` method resumes execution of the iterator block from where it was last suspended.</span></span>

<span data-ttu-id="9ab6d-644">Oggetto `yield break` istruzione viene eseguita come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="9ab6d-644">A `yield break` statement is executed as follows:</span></span>

*  <span data-ttu-id="9ab6d-645">Se il `yield break` istruzione è racchiuso da uno o più `try` blocchi con associati `finally` blocchi di controllo viene trasferito inizialmente al `finally` blocco di più interna `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-645">If the `yield break` statement is enclosed by one or more `try` blocks with associated `finally` blocks, control is initially transferred to the `finally` block of the innermost `try` statement.</span></span> <span data-ttu-id="9ab6d-646">Quando e se il controllo raggiunge il punto finale di un `finally` blocco di controllo viene trasferito al `finally` blocco di inclusione successivo `try` istruzione.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-646">When and if control reaches the end point of a `finally` block, control is transferred to the `finally` block of the next enclosing `try` statement.</span></span> <span data-ttu-id="9ab6d-647">Questo processo viene ripetuto finché il `finally` blocchi di inclusione tutti `try` istruzioni sono state eseguite.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-647">This process is repeated until the `finally` blocks of all enclosing `try` statements have been executed.</span></span>
*  <span data-ttu-id="9ab6d-648">Il controllo viene restituito al chiamante del blocco iteratore.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-648">Control is returned to the caller of the iterator block.</span></span> <span data-ttu-id="9ab6d-649">Valori possibili sono i `MoveNext` metodo o `Dispose` metodo dell'oggetto enumeratore.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-649">This is either the `MoveNext` method or `Dispose` method of the enumerator object.</span></span>

<span data-ttu-id="9ab6d-650">Poiché un `yield break` istruzione in modo incondizionato trasferisce il controllo in un' posizione, il punto finale di un `yield break` istruzione non è raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="9ab6d-650">Because a `yield break` statement unconditionally transfers control elsewhere, the end point of a `yield break` statement is never reachable.</span></span>
