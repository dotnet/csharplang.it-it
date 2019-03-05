---
ms.openlocfilehash: 67019511d49a786a5d6edf6fea442f745fc40f3f
ms.sourcegitcommit: 0a80f26b8e455c4f09843a10e11e29c24d2d922e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57347274"
---
# <a name="expressions"></a><span data-ttu-id="8624e-101">Espressioni</span><span class="sxs-lookup"><span data-stu-id="8624e-101">Expressions</span></span>

<span data-ttu-id="8624e-102">Un'espressione è una sequenza di operatori e operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-102">An expression is a sequence of operators and operands.</span></span> <span data-ttu-id="8624e-103">In questo capitolo definisce la sintassi, l'ordine di valutazione di operandi e operatori e il significato di espressioni.</span><span class="sxs-lookup"><span data-stu-id="8624e-103">This chapter defines the syntax, order of evaluation of operands and operators, and meaning of expressions.</span></span>

## <a name="expression-classifications"></a><span data-ttu-id="8624e-104">Classificazioni delle espressioni</span><span class="sxs-lookup"><span data-stu-id="8624e-104">Expression classifications</span></span>

<span data-ttu-id="8624e-105">Un'espressione viene classificata nei seguenti modi:</span><span class="sxs-lookup"><span data-stu-id="8624e-105">An expression is classified as one of the following:</span></span>

*  <span data-ttu-id="8624e-106">Un valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-106">A value.</span></span> <span data-ttu-id="8624e-107">Ad ogni valore è associato un tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-107">Every value has an associated type.</span></span>
*  <span data-ttu-id="8624e-108">Una variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-108">A variable.</span></span> <span data-ttu-id="8624e-109">Tutte le variabili sono associato un tipo, ovvero il tipo dichiarato della variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-109">Every variable has an associated type, namely the declared type of the variable.</span></span>
*  <span data-ttu-id="8624e-110">Spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="8624e-110">A namespace.</span></span> <span data-ttu-id="8624e-111">Un'espressione con questa classificazione può solo apparire come lato sinistro di un *member_access* ([l'accesso ai membri](expressions.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="8624e-111">An expression with this classification can only appear as the left hand side of a *member_access* ([Member access](expressions.md#member-access)).</span></span> <span data-ttu-id="8624e-112">In qualsiasi altro contesto, un'espressione classificata come uno spazio dei nomi genera un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-112">In any other context, an expression classified as a namespace causes a compile-time error.</span></span>
*  <span data-ttu-id="8624e-113">Tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-113">A type.</span></span> <span data-ttu-id="8624e-114">Un'espressione con questa classificazione può solo apparire come lato sinistro di un *member_access* ([l'accesso ai membri](expressions.md#member-access)), o come operando per il `as` operatore ([l'operatore ](expressions.md#the-as-operator)), il `is` operatore ([l'operatore is](expressions.md#the-is-operator)), o la `typeof` operatore ([l'operatore typeof](expressions.md#the-typeof-operator)).</span><span class="sxs-lookup"><span data-stu-id="8624e-114">An expression with this classification can only appear as the left hand side of a *member_access* ([Member access](expressions.md#member-access)), or as an operand for the `as` operator ([The as operator](expressions.md#the-as-operator)), the `is` operator ([The is operator](expressions.md#the-is-operator)), or the `typeof` operator ([The typeof operator](expressions.md#the-typeof-operator)).</span></span> <span data-ttu-id="8624e-115">In qualsiasi altro contesto, un'espressione classificata come un tipo causa un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-115">In any other context, an expression classified as a type causes a compile-time error.</span></span>
*  <span data-ttu-id="8624e-116">Un gruppo di metodi, ovvero un set di metodi di overload risultante da una ricerca di membri ([ricerca di membri](expressions.md#member-lookup)).</span><span class="sxs-lookup"><span data-stu-id="8624e-116">A method group, which is a set of overloaded methods resulting from a member lookup ([Member lookup](expressions.md#member-lookup)).</span></span> <span data-ttu-id="8624e-117">Un gruppo di metodi potrebbe essere un'espressione di istanza e un elenco di argomenti tipo associato.</span><span class="sxs-lookup"><span data-stu-id="8624e-117">A method group may have an associated instance expression and an associated type argument list.</span></span> <span data-ttu-id="8624e-118">Quando viene richiamato un metodo di istanza, il risultato della valutazione dell'espressione dell'istanza diventa l'istanza rappresentata dal `this` ([questo accesso](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="8624e-118">When an instance method is invoked, the result of evaluating the instance expression becomes the instance represented by `this` ([This access](expressions.md#this-access)).</span></span> <span data-ttu-id="8624e-119">Un gruppo di metodi è consentito in un' *invocation_expression* ([espressioni di chiamata](expressions.md#invocation-expressions)), una *delegate_creation_expression* ([delegare la creazione le espressioni](expressions.md#delegate-creation-expressions)) e il lato sinistro di un operatore e può essere convertito in modo implicito in un tipo delegato compatibile ([conversioni dei metodi di gruppo](conversions.md#method-group-conversions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-119">A method group is permitted in an *invocation_expression* ([Invocation expressions](expressions.md#invocation-expressions)) , a *delegate_creation_expression* ([Delegate creation expressions](expressions.md#delegate-creation-expressions)) and as the left hand side of an is operator, and can be implicitly converted to a compatible delegate type ([Method group conversions](conversions.md#method-group-conversions)).</span></span> <span data-ttu-id="8624e-120">In qualsiasi altro contesto, un'espressione classificata come un gruppo di metodi provoca un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-120">In any other context, an expression classified as a method group causes a compile-time error.</span></span>
*  <span data-ttu-id="8624e-121">Un valore letterale null.</span><span class="sxs-lookup"><span data-stu-id="8624e-121">A null literal.</span></span> <span data-ttu-id="8624e-122">Un'espressione con questa classificazione può essere convertita in modo implicito in un tipo riferimento o nullable.</span><span class="sxs-lookup"><span data-stu-id="8624e-122">An expression with this classification can be implicitly converted to a reference type or nullable type.</span></span>
*  <span data-ttu-id="8624e-123">Una funzione anonima.</span><span class="sxs-lookup"><span data-stu-id="8624e-123">An anonymous function.</span></span> <span data-ttu-id="8624e-124">Un'espressione con questa classificazione può essere convertita in modo implicito a un tipo delegato compatibile o un tipo di albero delle espressioni.</span><span class="sxs-lookup"><span data-stu-id="8624e-124">An expression with this classification can be implicitly converted to a compatible delegate type or expression tree type.</span></span>
*  <span data-ttu-id="8624e-125">Accesso alla proprietà.</span><span class="sxs-lookup"><span data-stu-id="8624e-125">A property access.</span></span> <span data-ttu-id="8624e-126">Ogni accesso a proprietà è associato un tipo, ovvero il tipo della proprietà.</span><span class="sxs-lookup"><span data-stu-id="8624e-126">Every property access has an associated type, namely the type of the property.</span></span> <span data-ttu-id="8624e-127">Inoltre, un accesso a proprietà potrebbe essere un'espressione dell'istanza associata.</span><span class="sxs-lookup"><span data-stu-id="8624e-127">Furthermore, a property access may have an associated instance expression.</span></span> <span data-ttu-id="8624e-128">Quando una funzione di accesso (il `get` o `set` block) di un'istanza viene richiamato l'accesso alle proprietà, il risultato della valutazione dell'espressione dell'istanza diventa l'istanza rappresentata dal `this` ([questo accesso](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="8624e-128">When an accessor (the `get` or `set` block) of an instance property access is invoked, the result of evaluating the instance expression becomes the instance represented by `this` ([This access](expressions.md#this-access)).</span></span>
*  <span data-ttu-id="8624e-129">Accesso a un evento.</span><span class="sxs-lookup"><span data-stu-id="8624e-129">An event access.</span></span> <span data-ttu-id="8624e-130">Ogni accesso all'evento è associato un tipo, ovvero il tipo dell'evento.</span><span class="sxs-lookup"><span data-stu-id="8624e-130">Every event access has an associated type, namely the type of the event.</span></span> <span data-ttu-id="8624e-131">Accesso a un evento, inoltre, potrebbe essere un'espressione dell'istanza associata.</span><span class="sxs-lookup"><span data-stu-id="8624e-131">Furthermore, an event access may have an associated instance expression.</span></span> <span data-ttu-id="8624e-132">Accesso a un evento potrebbe essere visualizzato come operando di sinistra del `+=` e `-=` operatori ([assegnazione evento](expressions.md#event-assignment)).</span><span class="sxs-lookup"><span data-stu-id="8624e-132">An event access may appear as the left hand operand of the `+=` and `-=` operators ([Event assignment](expressions.md#event-assignment)).</span></span> <span data-ttu-id="8624e-133">In qualsiasi altro contesto, un'espressione classificata come un accesso a eventi genera un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-133">In any other context, an expression classified as an event access causes a compile-time error.</span></span>
*  <span data-ttu-id="8624e-134">Accesso all'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-134">An indexer access.</span></span> <span data-ttu-id="8624e-135">Ogni accesso all'indicizzatore è associato un tipo, ovvero il tipo di elemento dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-135">Every indexer access has an associated type, namely the element type of the indexer.</span></span> <span data-ttu-id="8624e-136">Inoltre, un accesso all'indicizzatore ha un'espressione di istanza e un elenco di argomenti.</span><span class="sxs-lookup"><span data-stu-id="8624e-136">Furthermore, an indexer access has an associated instance expression and an associated argument list.</span></span> <span data-ttu-id="8624e-137">Quando una funzione di accesso (il `get` oppure `set` blocco) di un indicizzatore accesso viene richiamato, il risultato della valutazione dell'espressione dell'istanza diventa l'istanza rappresentata dal `this` ([questo accesso](expressions.md#this-access)) e il risultato di l'elenco di argomenti di valutazione diventa l'elenco di parametri della chiamata.</span><span class="sxs-lookup"><span data-stu-id="8624e-137">When an accessor (the `get` or `set` block) of an indexer access is invoked, the result of evaluating the instance expression becomes the instance represented by `this` ([This access](expressions.md#this-access)), and the result of evaluating the argument list becomes the parameter list of the invocation.</span></span>
*  <span data-ttu-id="8624e-138">Nessun elemento.</span><span class="sxs-lookup"><span data-stu-id="8624e-138">Nothing.</span></span> <span data-ttu-id="8624e-139">Ciò si verifica quando l'espressione è una chiamata di un metodo con un tipo restituito `void`.</span><span class="sxs-lookup"><span data-stu-id="8624e-139">This occurs when the expression is an invocation of a method with a return type of `void`.</span></span> <span data-ttu-id="8624e-140">Un'espressione classificata come non è valido solo nel contesto di un *statement_expression* ([le istruzioni di espressione](statements.md#expression-statements)).</span><span class="sxs-lookup"><span data-stu-id="8624e-140">An expression classified as nothing is only valid in the context of a *statement_expression* ([Expression statements](statements.md#expression-statements)).</span></span>

<span data-ttu-id="8624e-141">Il risultato finale di un'espressione è mai un spazio dei nomi, tipo, il gruppo di metodi o l'accesso agli eventi.</span><span class="sxs-lookup"><span data-stu-id="8624e-141">The final result of an expression is never a namespace, type, method group, or event access.</span></span> <span data-ttu-id="8624e-142">Piuttosto, come indicato in precedenza, queste categorie di espressioni sono costrutti intermedi che sono consentiti solo in determinati contesti.</span><span class="sxs-lookup"><span data-stu-id="8624e-142">Rather, as noted above, these categories of expressions are intermediate constructs that are only permitted in certain contexts.</span></span>

<span data-ttu-id="8624e-143">Accesso a un accesso a proprietà o indicizzatore è sempre riclassificato come valore mediante l'esecuzione di una chiamata del *funzione di accesso get* o nella *funzione di accesso set*.</span><span class="sxs-lookup"><span data-stu-id="8624e-143">A property access or indexer access is always reclassified as a value by performing an invocation of the *get accessor* or the *set accessor*.</span></span> <span data-ttu-id="8624e-144">La funzione di accesso specifico è determinato dal contesto dell'accesso alla proprietà o indicizzatore: Se l'accesso è la destinazione di un'assegnazione, il *funzione di accesso set* viene richiamata per assegnare un nuovo valore ([assegnamento semplice](expressions.md#simple-assignment)).</span><span class="sxs-lookup"><span data-stu-id="8624e-144">The particular accessor is determined by the context of the property or indexer access: If the access is the target of an assignment, the *set accessor* is invoked to assign a new value ([Simple assignment](expressions.md#simple-assignment)).</span></span> <span data-ttu-id="8624e-145">In caso contrario, il *funzione di accesso get* viene richiamato per ottenere il valore corrente ([i valori delle espressioni](expressions.md#values-of-expressions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-145">Otherwise, the *get accessor* is invoked to obtain the current value ([Values of expressions](expressions.md#values-of-expressions)).</span></span>

### <a name="values-of-expressions"></a><span data-ttu-id="8624e-146">Valori delle espressioni</span><span class="sxs-lookup"><span data-stu-id="8624e-146">Values of expressions</span></span>

<span data-ttu-id="8624e-147">La maggior parte dei Construct che comportano un'espressione in definitiva richiede l'espressione per indicare una ***valore***.</span><span class="sxs-lookup"><span data-stu-id="8624e-147">Most of the constructs that involve an expression ultimately require the expression to denote a ***value***.</span></span> <span data-ttu-id="8624e-148">In questi casi, se l'espressione effettiva indica uno spazio dei nomi, un tipo, un gruppo di metodi o nothing, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-148">In such cases, if the actual expression denotes a namespace, a type, a method group, or nothing, a compile-time error occurs.</span></span> <span data-ttu-id="8624e-149">Tuttavia, se l'espressione indica un accesso a proprietà, un accesso all'indicizzatore o una variabile, il valore della proprietà, indicizzatore o variabile viene sostituito in modo implicito:</span><span class="sxs-lookup"><span data-stu-id="8624e-149">However, if the expression denotes a property access, an indexer access, or a variable, the value of the property, indexer, or variable is implicitly substituted:</span></span>

*  <span data-ttu-id="8624e-150">Il valore di una variabile è semplicemente il valore attualmente archiviato nella posizione identificata dalla variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-150">The value of a variable is simply the value currently stored in the storage location identified by the variable.</span></span> <span data-ttu-id="8624e-151">Deve essere considerata una variabile assegnata ([assegnazione certa](variables.md#definite-assignment)) prima che il relativo valore può essere ottenuto o in caso contrario, verrà generato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-151">A variable must be considered definitely assigned ([Definite assignment](variables.md#definite-assignment)) before its value can be obtained, or otherwise a compile-time error occurs.</span></span>
*  <span data-ttu-id="8624e-152">Il valore di un'espressione di accesso proprietà ottenuto chiamando il *funzione di accesso get* della proprietà.</span><span class="sxs-lookup"><span data-stu-id="8624e-152">The value of a property access expression is obtained by invoking the *get accessor* of the property.</span></span> <span data-ttu-id="8624e-153">Se la proprietà non ha alcun *funzione di accesso get*, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-153">If the property has no *get accessor*, a compile-time error occurs.</span></span> <span data-ttu-id="8624e-154">In caso contrario, la chiamata di un membro di funzione ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) viene eseguita, e il risultato della chiamata diventa il valore dell'espressione di accesso di proprietà.</span><span class="sxs-lookup"><span data-stu-id="8624e-154">Otherwise, a function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) is performed, and the result of the invocation becomes the value of the property access expression.</span></span>
*  <span data-ttu-id="8624e-155">Il valore di un'espressione di accesso dell'indicizzatore ottenuto chiamando il *funzione di accesso get* dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-155">The value of an indexer access expression is obtained by invoking the *get accessor* of the indexer.</span></span> <span data-ttu-id="8624e-156">Se l'indicizzatore non ha alcun *funzione di accesso get*, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-156">If the indexer has no *get accessor*, a compile-time error occurs.</span></span> <span data-ttu-id="8624e-157">In caso contrario, la chiamata di un membro di funzione ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) viene eseguita con l'argomento elenco associato all'espressione di accesso dell'indicizzatore, e il risultato della chiamata diventa il valore dell'espressione di accesso dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-157">Otherwise, a function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) is performed with the argument list associated with the indexer access expression, and the result of the invocation becomes the value of the indexer access expression.</span></span>

## <a name="static-and-dynamic-binding"></a><span data-ttu-id="8624e-158">Binding statico e dinamico</span><span class="sxs-lookup"><span data-stu-id="8624e-158">Static and Dynamic Binding</span></span>

<span data-ttu-id="8624e-159">Il processo volto a determinare il significato di un'operazione in base al tipo o valore di espressioni che costituiscono (argomenti, gli operandi, ricevitori) è spesso detta ***associazione***.</span><span class="sxs-lookup"><span data-stu-id="8624e-159">The process of determining the meaning of an operation based on the type or value of constituent expressions (arguments, operands, receivers) is often referred to as ***binding***.</span></span> <span data-ttu-id="8624e-160">Ad esempio il significato di una chiamata al metodo è determinato in base al tipo di ricevitore e argomenti.</span><span class="sxs-lookup"><span data-stu-id="8624e-160">For instance the meaning of a method call is determined based on the type of the receiver and arguments.</span></span> <span data-ttu-id="8624e-161">Il significato di un operatore è determinato in base al tipo dei relativi operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-161">The meaning of an operator is determined based on the type of its operands.</span></span>

<span data-ttu-id="8624e-162">In c# il significato di un'operazione in genere è determinato in fase di compilazione, in base al tipo di fase di compilazione delle relative espressioni che lo costituiscono.</span><span class="sxs-lookup"><span data-stu-id="8624e-162">In C# the meaning of an operation is usually determined at compile-time, based on the compile-time type of its constituent expressions.</span></span> <span data-ttu-id="8624e-163">Analogamente, se un'espressione contiene un errore, l'errore viene rilevato e segnalato dal compilatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-163">Likewise, if an expression contains an error, the error is detected and reported by the compiler.</span></span> <span data-ttu-id="8624e-164">Questo approccio è noto come ***associazioni statiche***.</span><span class="sxs-lookup"><span data-stu-id="8624e-164">This approach is known as ***static binding***.</span></span>

<span data-ttu-id="8624e-165">Tuttavia, se un'espressione è un'espressione dinamica (ad esempio è di tipo `dynamic`) indica che qualsiasi associazione che fa parte deve essere basati sul relativo tipo in fase di esecuzione (ad esempio il tipo effettivo dell'oggetto indica al runtime) anziché il tipo nel in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-165">However, if an expression is a dynamic expression (i.e. has the type `dynamic`) this indicates that any binding that it participates in should be based on its run-time type (i.e. the actual type of the object it denotes at run-time) rather than the type it has at compile-time.</span></span> <span data-ttu-id="8624e-166">Pertanto, l'associazione di tale operazione viene posticipata fino al momento in cui l'operazione deve essere eseguito durante l'esecuzione del programma.</span><span class="sxs-lookup"><span data-stu-id="8624e-166">The binding of such an operation is therefore deferred until the time where the operation is to be executed during the running of the program.</span></span> <span data-ttu-id="8624e-167">Ciò è detto ***binding dinamico***.</span><span class="sxs-lookup"><span data-stu-id="8624e-167">This is referred to as ***dynamic binding***.</span></span>

<span data-ttu-id="8624e-168">Quando un'operazione in modo dinamico è associata, senza alcuna verifica viene eseguita dal compilatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-168">When an operation is dynamically bound, little or no checking is performed by the compiler.</span></span> <span data-ttu-id="8624e-169">Se, invece l'associazione di runtime non riesce, gli errori vengono segnalati come eccezioni in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-169">Instead if the run-time binding fails, errors are reported as exceptions at run-time.</span></span>

<span data-ttu-id="8624e-170">Le operazioni seguenti nel linguaggio c# sono soggetti a binding:</span><span class="sxs-lookup"><span data-stu-id="8624e-170">The following operations in C# are subject to binding:</span></span>

*  <span data-ttu-id="8624e-171">Accesso ai membri: `e.M`</span><span class="sxs-lookup"><span data-stu-id="8624e-171">Member access: `e.M`</span></span>
*  <span data-ttu-id="8624e-172">Chiamata del metodo: `e.M(e1, ..., eN)`</span><span class="sxs-lookup"><span data-stu-id="8624e-172">Method invocation: `e.M(e1, ..., eN)`</span></span>
*  <span data-ttu-id="8624e-173">Chiamata a un delegato:`e(e1, ..., eN)`</span><span class="sxs-lookup"><span data-stu-id="8624e-173">Delegate invocation:`e(e1, ..., eN)`</span></span>
*  <span data-ttu-id="8624e-174">Accesso all'elemento: `e[e1, ..., eN]`</span><span class="sxs-lookup"><span data-stu-id="8624e-174">Element access: `e[e1, ..., eN]`</span></span>
*  <span data-ttu-id="8624e-175">Creazione di oggetti: `new C(e1, ..., eN)`</span><span class="sxs-lookup"><span data-stu-id="8624e-175">Object creation: `new C(e1, ..., eN)`</span></span>
*  <span data-ttu-id="8624e-176">Sottoposto a overload gli operatori unari: `+`, `-`, `!`, `~`, `++`, `--`, `true`, `false`</span><span class="sxs-lookup"><span data-stu-id="8624e-176">Overloaded unary operators: `+`, `-`, `!`, `~`, `++`, `--`, `true`, `false`</span></span>
*  <span data-ttu-id="8624e-177">Sottoposto a overload gli operatori binari: `+`, `-`, `*`, `/`, `%`, `&`, `&&`, `|`, `||`, `??`, `^`, `<<` , `>>`, `==`,`!=`, `>`, `<`, `>=`, `<=`</span><span class="sxs-lookup"><span data-stu-id="8624e-177">Overloaded binary operators: `+`, `-`, `*`, `/`, `%`, `&`, `&&`, `|`, `||`, `??`, `^`, `<<`, `>>`, `==`,`!=`, `>`, `<`, `>=`, `<=`</span></span>
*  <span data-ttu-id="8624e-178">Gli operatori di assegnazione: `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|=`, `^=`, `<<=`, `>>=`</span><span class="sxs-lookup"><span data-stu-id="8624e-178">Assignment operators: `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|=`, `^=`, `<<=`, `>>=`</span></span>
*  <span data-ttu-id="8624e-179">Conversioni implicite ed esplicite</span><span class="sxs-lookup"><span data-stu-id="8624e-179">Implicit and explicit conversions</span></span>

<span data-ttu-id="8624e-180">Quando non sono coinvolte espressioni dinamiche, c# impostata automaticamente su associazioni statiche, il che significa che vengono utilizzati i tipi in fase di compilazione di espressioni che costituiscono il processo di selezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-180">When no dynamic expressions are involved, C# defaults to static binding, which means that the compile-time types of constituent expressions are used in the selection process.</span></span> <span data-ttu-id="8624e-181">Tuttavia, quando una delle espressioni che costituiscono nelle operazioni elencate in precedenza è un'espressione dinamica, l'operazione viene invece associata in modo dinamico.</span><span class="sxs-lookup"><span data-stu-id="8624e-181">However, when one of the constituent expressions in the operations listed above is a dynamic expression, the operation is instead dynamically bound.</span></span>

### <a name="binding-time"></a><span data-ttu-id="8624e-182">In fase di associazione</span><span class="sxs-lookup"><span data-stu-id="8624e-182">Binding-time</span></span>

<span data-ttu-id="8624e-183">Associazione statica ha luogo in fase di compilazione, mentre l'associazione dinamica viene eseguita in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-183">Static binding takes place at compile-time, whereas dynamic binding takes place at run-time.</span></span> <span data-ttu-id="8624e-184">Nelle sezioni seguenti, il termine ***in fase di associazione*** fa riferimento alla fase di compilazione o in fase di esecuzione, a seconda di quando avviene l'associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-184">In the following sections, the term ***binding-time*** refers to either compile-time or run-time, depending on when the binding takes place.</span></span>

<span data-ttu-id="8624e-185">L'esempio seguente illustra i concetti di binding statico e dinamico e in fase di associazione:</span><span class="sxs-lookup"><span data-stu-id="8624e-185">The following example illustrates the notions of static and dynamic binding and of binding-time:</span></span>
```csharp
object  o = 5;
dynamic d = 5;

Console.WriteLine(5);  // static  binding to Console.WriteLine(int)
Console.WriteLine(o);  // static  binding to Console.WriteLine(object)
Console.WriteLine(d);  // dynamic binding to Console.WriteLine(int)
```

<span data-ttu-id="8624e-186">Le prime due chiamate vengono associate in modo statico: l'overload del metodo `Console.WriteLine` viene selezionato in base al tipo di fase di compilazione del relativo argomento.</span><span class="sxs-lookup"><span data-stu-id="8624e-186">The first two calls are statically bound: the overload of `Console.WriteLine` is picked based on the compile-time type of their argument.</span></span> <span data-ttu-id="8624e-187">Pertanto, la fase di associazione è in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-187">Thus, the binding-time is compile-time.</span></span>

<span data-ttu-id="8624e-188">La terza chiamata è associata in modo dinamico: l'overload del metodo `Console.WriteLine` viene selezionato in base al tipo di fase di esecuzione del relativo argomento.</span><span class="sxs-lookup"><span data-stu-id="8624e-188">The third call is dynamically bound: the overload of `Console.WriteLine` is picked based on the run-time type of its argument.</span></span> <span data-ttu-id="8624e-189">Ciò accade perché l'argomento è un'espressione dinamica: il tipo di fase di compilazione `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-189">This happens because the argument is a dynamic expression -- its compile-time type is `dynamic`.</span></span> <span data-ttu-id="8624e-190">Pertanto, la fase di associazione per la terza chiamata è in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-190">Thus, the binding-time for the third call is run-time.</span></span>

### <a name="dynamic-binding"></a><span data-ttu-id="8624e-191">Associazione dinamica</span><span class="sxs-lookup"><span data-stu-id="8624e-191">Dynamic binding</span></span>

<span data-ttu-id="8624e-192">Lo scopo di associazione dinamica è consentire ai programmi c# di interagirvi ***oggetti dinamici***, vale a dire gli oggetti che non seguono le regole normali di C# sistema di tipi.</span><span class="sxs-lookup"><span data-stu-id="8624e-192">The purpose of dynamic binding is to allow C# programs to interact with ***dynamic objects***, i.e. objects that do not follow the normal rules of the C# type system.</span></span> <span data-ttu-id="8624e-193">Oggetti dinamici possono essere oggetti di altri linguaggi di programmazione con i sistemi di tipi diversi, o possono essere oggetti che sono a livello di codice per implementare la propria semantica di associazione per diversi di operazioni di installazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-193">Dynamic objects may be objects from other programming languages with different types systems, or they may be objects that are programmatically setup to implement their own binding semantics for different operations.</span></span>

<span data-ttu-id="8624e-194">Il meccanismo mediante il quale un oggetto dinamico implementa la propria semantica è definito dall'implementazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-194">The mechanism by which a dynamic object implements its own semantics is implementation defined.</span></span> <span data-ttu-id="8624e-195">Una determinata interfaccia, anche in questo caso implementazione definita, viene implementata dagli oggetti dinamici per segnalare il codice c# in fase di esecuzione che hanno una semantica speciale.</span><span class="sxs-lookup"><span data-stu-id="8624e-195">A given interface -- again implementation defined -- is implemented by dynamic objects to signal to the C# run-time that they have special semantics.</span></span> <span data-ttu-id="8624e-196">Di conseguenza, ogni volta che le operazioni su un oggetto dinamico di associazione in modo dinamico, la propria semantica di associazione, anziché quelle del linguaggio c# come specificato in questo documento, intervenire.</span><span class="sxs-lookup"><span data-stu-id="8624e-196">Thus, whenever operations on a dynamic object are dynamically bound, their own binding semantics, rather than those of C# as specified in this document, take over.</span></span>

<span data-ttu-id="8624e-197">Mentre lo scopo di associazione dinamica è consentire l'interazione con gli oggetti dinamici, c# consente l'associazione dinamica in tutti gli oggetti, se sono dinamici o meno.</span><span class="sxs-lookup"><span data-stu-id="8624e-197">While the purpose of dynamic binding is to allow interoperation with dynamic objects, C# allows dynamic binding on all objects, whether they are dynamic or not.</span></span> <span data-ttu-id="8624e-198">In questo modo per un'integrazione più uniforme di oggetti dinamici, come i risultati delle operazioni su di essi non stesse possono essere oggetti dinamici, ma sono comunque di un tipo sconosciuto per il programmatore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-198">This allows for a smoother integration of dynamic objects, as the results of operations on them may not themselves be dynamic objects, but are still of a type unknown to the programmer at compile-time.</span></span> <span data-ttu-id="8624e-199">Anche associazione dinamica consente di eliminare il codice basato su reflection soggetta a errori anche quando nessuno degli oggetti interessati è oggetti dinamici.</span><span class="sxs-lookup"><span data-stu-id="8624e-199">Also dynamic binding can help eliminate error-prone reflection-based code even when no objects involved are dynamic objects.</span></span>

<span data-ttu-id="8624e-200">Le sezioni seguenti per ciascun costrutto del linguaggio esattamente quando l'associazione dinamica viene applicato, cosa compilare il controllo in fase: se uno qualsiasi, viene applicata e quali la fase di compilazione risultato ed espressione classificazione è.</span><span class="sxs-lookup"><span data-stu-id="8624e-200">The following sections describe for each construct in the language exactly when dynamic binding is applied, what compile time checking -- if any -- is applied, and what the compile-time result and expression classification is.</span></span>

### <a name="types-of-constituent-expressions"></a><span data-ttu-id="8624e-201">Tipi di espressioni che costituiscono</span><span class="sxs-lookup"><span data-stu-id="8624e-201">Types of constituent expressions</span></span>

<span data-ttu-id="8624e-202">Quando un'operazione in modo statico associata, il tipo di un'espressione che lo costituiscono (ad esempio, un ricevitore, un argomento, un indice o un operando) viene considerato sempre il tipo di fase di compilazione dell'espressione.</span><span class="sxs-lookup"><span data-stu-id="8624e-202">When an operation is statically bound, the type of a constituent expression (e.g. a receiver, an argument, an index or an operand) is always considered to be the compile-time type of that expression.</span></span>

<span data-ttu-id="8624e-203">Quando un'operazione in modo dinamico è associata, il tipo di un'espressione che lo costituiscono è determinato in modi diversi a seconda del tipo in fase di compilazione dell'espressione che lo costituiscono:</span><span class="sxs-lookup"><span data-stu-id="8624e-203">When an operation is dynamically bound, the type of a constituent expression is determined in different ways depending on the compile-time type of the constituent expression:</span></span>

*  <span data-ttu-id="8624e-204">Un'espressione che costituiscono dei tipi in fase di compilazione `dynamic` ha il tipo del valore effettivo a cui l'espressione viene valutata in fase di esecuzione</span><span class="sxs-lookup"><span data-stu-id="8624e-204">A constituent expression of compile-time type `dynamic` is considered to have the type of the actual value that the expression evaluates to at runtime</span></span>
*  <span data-ttu-id="8624e-205">Un'espressione che costituiscono il cui tipo di fase di compilazione è un parametro di tipo viene considerata il tipo di cui è associato il parametro di tipo in fase di esecuzione</span><span class="sxs-lookup"><span data-stu-id="8624e-205">A constituent expression whose compile-time type is a type parameter is considered to have the type which the type parameter is bound to at runtime</span></span>
*  <span data-ttu-id="8624e-206">In caso contrario, viene considerato l'espressione che costituiscono il relativo tipo in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-206">Otherwise the constituent expression is considered to have its compile-time type.</span></span>

## <a name="operators"></a><span data-ttu-id="8624e-207">Operatori</span><span class="sxs-lookup"><span data-stu-id="8624e-207">Operators</span></span>

<span data-ttu-id="8624e-208">Le espressioni sono costituite da ***operandi*** e ***operatori***.</span><span class="sxs-lookup"><span data-stu-id="8624e-208">Expressions are constructed from ***operands*** and ***operators***.</span></span> <span data-ttu-id="8624e-209">Gli operatori di un'espressione indicano le operazioni che devono essere eseguite sugli operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-209">The operators of an expression indicate which operations to apply to the operands.</span></span> <span data-ttu-id="8624e-210">Alcuni esempi di operatori sono `+`, `-`, `*`, `/` e `new`,</span><span class="sxs-lookup"><span data-stu-id="8624e-210">Examples of operators include `+`, `-`, `*`, `/`, and `new`.</span></span> <span data-ttu-id="8624e-211">mentre i valori effettivi, i campi, le variabili locali e le espressioni sono esempi di operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-211">Examples of operands include literals, fields, local variables, and expressions.</span></span>

<span data-ttu-id="8624e-212">Esistono tre tipi di operatori:</span><span class="sxs-lookup"><span data-stu-id="8624e-212">There are three kinds of operators:</span></span>

*  <span data-ttu-id="8624e-213">Operatori unari.</span><span class="sxs-lookup"><span data-stu-id="8624e-213">Unary operators.</span></span> <span data-ttu-id="8624e-214">Gli operatori unari accettano un unico operando e usare la notazione prefissa (ad esempio `--x`) o la notazione suffissa (ad esempio `x++`).</span><span class="sxs-lookup"><span data-stu-id="8624e-214">The unary operators take one operand and use either prefix notation (such as `--x`) or postfix notation (such as `x++`).</span></span>
*  <span data-ttu-id="8624e-215">Operatori binari.</span><span class="sxs-lookup"><span data-stu-id="8624e-215">Binary operators.</span></span> <span data-ttu-id="8624e-216">Gli operatori binari accettano due operandi e utilizzano la notazione infissa (ad esempio `x + y`).</span><span class="sxs-lookup"><span data-stu-id="8624e-216">The binary operators take two operands and all use infix notation (such as `x + y`).</span></span>
*  <span data-ttu-id="8624e-217">operatore ternario.</span><span class="sxs-lookup"><span data-stu-id="8624e-217">Ternary operator.</span></span> <span data-ttu-id="8624e-218">Solo un operatore ternario `?:`, esiste; accetta tre operandi e utilizza la notazione infissa (`c ? x : y`).</span><span class="sxs-lookup"><span data-stu-id="8624e-218">Only one ternary operator, `?:`, exists; it takes three operands and uses infix notation (`c ? x : y`).</span></span>

<span data-ttu-id="8624e-219">L'ordine di valutazione degli operatori in un'espressione è determinato dal ***precedenza*** e ***associatività*** degli operatori ([precedenza e associatività degli operatori](expressions.md#operator-precedence-and-associativity)) .</span><span class="sxs-lookup"><span data-stu-id="8624e-219">The order of evaluation of operators in an expression is determined by the ***precedence*** and ***associativity*** of the operators ([Operator precedence and associativity](expressions.md#operator-precedence-and-associativity)).</span></span>

<span data-ttu-id="8624e-220">Gli operandi in un'espressione vengono valutati da sinistra a destra.</span><span class="sxs-lookup"><span data-stu-id="8624e-220">Operands in an expression are evaluated from left to right.</span></span> <span data-ttu-id="8624e-221">Ad esempio, nella `F(i) + G(i++) * H(i)`, metodo `F` viene chiamato con il valore precedente della `i`, quindi il metodo `G` viene chiamato con il valore precedente della `i`e, infine, metodo `H` viene chiamato con il nuovo valore di `i`.</span><span class="sxs-lookup"><span data-stu-id="8624e-221">For example, in `F(i) + G(i++) * H(i)`, method `F` is called using the old value of `i`, then method `G` is called with the old value of `i`, and, finally, method `H` is called with the new value of `i`.</span></span> <span data-ttu-id="8624e-222">Questo è separato dal e non correlati alla precedenza degli operatori.</span><span class="sxs-lookup"><span data-stu-id="8624e-222">This is separate from and unrelated to operator precedence.</span></span>

<span data-ttu-id="8624e-223">Alcuni operatori possono essere ***sottoposti a overload***.</span><span class="sxs-lookup"><span data-stu-id="8624e-223">Certain operators can be ***overloaded***.</span></span> <span data-ttu-id="8624e-224">L'overload degli operatori consente di specificare implementazioni operatore definito dall'utente di specificare per le operazioni in cui uno o entrambi gli operandi sono di un tipo di classe o struct definito dall'utente ([overload di operatori](expressions.md#operator-overloading)).</span><span class="sxs-lookup"><span data-stu-id="8624e-224">Operator overloading permits user-defined operator implementations to be specified for operations where one or both of the operands are of a user-defined class or struct type ([Operator overloading](expressions.md#operator-overloading)).</span></span>

### <a name="operator-precedence-and-associativity"></a><span data-ttu-id="8624e-225">Precedenza e associatività degli operatori</span><span class="sxs-lookup"><span data-stu-id="8624e-225">Operator precedence and associativity</span></span>

<span data-ttu-id="8624e-226">Se un'espressione contiene più operatori, la ***precedenza*** degli operatori determina l'ordine in cui vengono valutati i singoli operatori.</span><span class="sxs-lookup"><span data-stu-id="8624e-226">When an expression contains multiple operators, the ***precedence*** of the operators controls the order in which the individual operators are evaluated.</span></span> <span data-ttu-id="8624e-227">Ad esempio, l'espressione `x + y * z` darà `x + (y * z)` perché la `*` operatore ha maggiore precedenza il file binario `+` operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-227">For example, the expression `x + y * z` is evaluated as `x + (y * z)` because the `*` operator has higher precedence than the binary `+` operator.</span></span> <span data-ttu-id="8624e-228">La priorità di un operatore viene stabilita dalla definizione di grammatica associata.</span><span class="sxs-lookup"><span data-stu-id="8624e-228">The precedence of an operator is established by the definition of its associated grammar production.</span></span> <span data-ttu-id="8624e-229">Ad esempio, un *additive_expression* è costituito da una sequenza di *multiplicative_expression*s separati da `+` oppure `-` operatori, offrendo così la `+` e `-` precedenza sugli operatori del `*`, `/`, e `%` operatori.</span><span class="sxs-lookup"><span data-stu-id="8624e-229">For example, an *additive_expression* consists of a sequence of *multiplicative_expression*s separated by `+` or `-` operators, thus giving the `+` and `-` operators lower precedence than the `*`, `/`, and `%` operators.</span></span>

<span data-ttu-id="8624e-230">La tabella seguente riepiloga tutti gli operatori in ordine di precedenza dalla più alta alla più bassa:</span><span class="sxs-lookup"><span data-stu-id="8624e-230">The following table summarizes all operators in order of precedence from highest to lowest:</span></span>

| <span data-ttu-id="8624e-231">__Sezione__</span><span class="sxs-lookup"><span data-stu-id="8624e-231">__Section__</span></span>                                                                                   | <span data-ttu-id="8624e-232">__Categoria__</span><span class="sxs-lookup"><span data-stu-id="8624e-232">__Category__</span></span>                | <span data-ttu-id="8624e-233">__Operatori__</span><span class="sxs-lookup"><span data-stu-id="8624e-233">__Operators__</span></span> | 
|-----------------------------------------------------------------------------------------------|-----------------------------|---------------|
| [<span data-ttu-id="8624e-234">Espressioni primarie</span><span class="sxs-lookup"><span data-stu-id="8624e-234">Primary expressions</span></span>](expressions.md#primary-expressions)                                     | <span data-ttu-id="8624e-235">Primario</span><span class="sxs-lookup"><span data-stu-id="8624e-235">Primary</span></span>                     | <span data-ttu-id="8624e-236">`x.y`  `f(x)`  `a[x]`  `x++`  `x--`  `new`  `typeof`  `default`  `checked`  `unchecked`  `delegate`</span><span class="sxs-lookup"><span data-stu-id="8624e-236">`x.y`  `f(x)`  `a[x]`  `x++`  `x--`  `new`  `typeof`  `default`  `checked`  `unchecked`  `delegate`</span></span> | 
| [<span data-ttu-id="8624e-237">Operatori unari</span><span class="sxs-lookup"><span data-stu-id="8624e-237">Unary operators</span></span>](expressions.md#unary-operators)                                             | <span data-ttu-id="8624e-238">Unario</span><span class="sxs-lookup"><span data-stu-id="8624e-238">Unary</span></span>                       | <span data-ttu-id="8624e-239">`+`  `-`  `!`  `~`  `++x`  `--x`  `(T)x`</span><span class="sxs-lookup"><span data-stu-id="8624e-239">`+`  `-`  `!`  `~`  `++x`  `--x`  `(T)x`</span></span> | 
| [<span data-ttu-id="8624e-240">Operatori aritmetici</span><span class="sxs-lookup"><span data-stu-id="8624e-240">Arithmetic operators</span></span>](expressions.md#arithmetic-operators)                                   | <span data-ttu-id="8624e-241">Moltiplicazione</span><span class="sxs-lookup"><span data-stu-id="8624e-241">Multiplicative</span></span>              | <span data-ttu-id="8624e-242">`*`  `/`  `%`</span><span class="sxs-lookup"><span data-stu-id="8624e-242">`*`  `/`  `%`</span></span> | 
| [<span data-ttu-id="8624e-243">Operatori aritmetici</span><span class="sxs-lookup"><span data-stu-id="8624e-243">Arithmetic operators</span></span>](expressions.md#arithmetic-operators)                                   | <span data-ttu-id="8624e-244">Addizione</span><span class="sxs-lookup"><span data-stu-id="8624e-244">Additive</span></span>                    | <span data-ttu-id="8624e-245">`+`  `-`</span><span class="sxs-lookup"><span data-stu-id="8624e-245">`+`  `-`</span></span>      | 
| [<span data-ttu-id="8624e-246">Operatori shift</span><span class="sxs-lookup"><span data-stu-id="8624e-246">Shift operators</span></span>](expressions.md#shift-operators)                                             | <span data-ttu-id="8624e-247">Shift</span><span class="sxs-lookup"><span data-stu-id="8624e-247">Shift</span></span>                       | <span data-ttu-id="8624e-248">`<<`  `>>`</span><span class="sxs-lookup"><span data-stu-id="8624e-248">`<<`  `>>`</span></span>    | 
| [<span data-ttu-id="8624e-249">Operatori relazionali e di test del tipo</span><span class="sxs-lookup"><span data-stu-id="8624e-249">Relational and type-testing operators</span></span>](expressions.md#relational-and-type-testing-operators) | <span data-ttu-id="8624e-250">Operatori relazionali e operatori di test del tipo</span><span class="sxs-lookup"><span data-stu-id="8624e-250">Relational and type testing</span></span> | <span data-ttu-id="8624e-251">`<`  `>`  `<=`  `>=`  `is`  `as`</span><span class="sxs-lookup"><span data-stu-id="8624e-251">`<`  `>`  `<=`  `>=`  `is`  `as`</span></span> | 
| [<span data-ttu-id="8624e-252">Operatori relazionali e di test del tipo</span><span class="sxs-lookup"><span data-stu-id="8624e-252">Relational and type-testing operators</span></span>](expressions.md#relational-and-type-testing-operators) | <span data-ttu-id="8624e-253">Uguaglianza</span><span class="sxs-lookup"><span data-stu-id="8624e-253">Equality</span></span>                    | <span data-ttu-id="8624e-254">`==`  `!=`</span><span class="sxs-lookup"><span data-stu-id="8624e-254">`==`  `!=`</span></span>    | 
| [<span data-ttu-id="8624e-255">Operatori logici</span><span class="sxs-lookup"><span data-stu-id="8624e-255">Logical operators</span></span>](expressions.md#logical-operators)                                         | <span data-ttu-id="8624e-256">AND logico</span><span class="sxs-lookup"><span data-stu-id="8624e-256">Logical AND</span></span>                 | `&`           | 
| [<span data-ttu-id="8624e-257">Operatori logici</span><span class="sxs-lookup"><span data-stu-id="8624e-257">Logical operators</span></span>](expressions.md#logical-operators)                                         | <span data-ttu-id="8624e-258">XOR logico</span><span class="sxs-lookup"><span data-stu-id="8624e-258">Logical XOR</span></span>                 | `^`           | 
| [<span data-ttu-id="8624e-259">Operatori logici</span><span class="sxs-lookup"><span data-stu-id="8624e-259">Logical operators</span></span>](expressions.md#logical-operators)                                         | <span data-ttu-id="8624e-260">OR logico</span><span class="sxs-lookup"><span data-stu-id="8624e-260">Logical OR</span></span>                  | <code>&#124;</code>           |
| [<span data-ttu-id="8624e-261">Operatori logici condizionali</span><span class="sxs-lookup"><span data-stu-id="8624e-261">Conditional logical operators</span></span>](expressions.md#conditional-logical-operators)                 | <span data-ttu-id="8624e-262">AND condizionale</span><span class="sxs-lookup"><span data-stu-id="8624e-262">Conditional AND</span></span>             | `&&`          | 
| [<span data-ttu-id="8624e-263">Operatori logici condizionali</span><span class="sxs-lookup"><span data-stu-id="8624e-263">Conditional logical operators</span></span>](expressions.md#conditional-logical-operators)                 | <span data-ttu-id="8624e-264">OR condizionale</span><span class="sxs-lookup"><span data-stu-id="8624e-264">Conditional OR</span></span>              | <code>&#124;&#124;</code>          | 
| [<span data-ttu-id="8624e-265">Operatore null-coalescing</span><span class="sxs-lookup"><span data-stu-id="8624e-265">The null coalescing operator</span></span>](expressions.md#the-null-coalescing-operator)                   | <span data-ttu-id="8624e-266">Null-coalescing</span><span class="sxs-lookup"><span data-stu-id="8624e-266">Null coalescing</span></span>             | `??`          | 
| [<span data-ttu-id="8624e-267">Operatore condizionale</span><span class="sxs-lookup"><span data-stu-id="8624e-267">Conditional operator</span></span>](expressions.md#conditional-operator)                                   | <span data-ttu-id="8624e-268">Condizionale</span><span class="sxs-lookup"><span data-stu-id="8624e-268">Conditional</span></span>                 | `?:`          | 
| <span data-ttu-id="8624e-269">[Gli operatori di assegnazione](expressions.md#assignment-operators), [espressioni di funzioni anonime](expressions.md#anonymous-function-expressions)</span><span class="sxs-lookup"><span data-stu-id="8624e-269">[Assignment operators](expressions.md#assignment-operators), [Anonymous function expressions](expressions.md#anonymous-function-expressions)</span></span>  | <span data-ttu-id="8624e-270">Assegnazione ed espressione lambda</span><span class="sxs-lookup"><span data-stu-id="8624e-270">Assignment and lambda expression</span></span> | <span data-ttu-id="8624e-271">`=`  `*=`  `/=`  `%=`  `+=`  `-=`  `<<=`  `>>=`  `&=`  `^=`  <code>&#124;=</code>  `=>`</span><span class="sxs-lookup"><span data-stu-id="8624e-271">`=`  `*=`  `/=`  `%=`  `+=`  `-=`  `<<=`  `>>=`  `&=`  `^=`  <code>&#124;=</code>  `=>`</span></span> | 

<span data-ttu-id="8624e-272">Se un operando si trova tra i due operatori con la stessa precedenza, l'associatività degli operatori determina l'ordine in cui vengono eseguite le operazioni:</span><span class="sxs-lookup"><span data-stu-id="8624e-272">When an operand occurs between two operators with the same precedence, the associativity of the operators controls the order in which the operations are performed:</span></span>

*  <span data-ttu-id="8624e-273">Fatta eccezione per gli operatori di assegnazione e l'operatore null-coalescing, sono tutti gli operatori binari ***associa all'operando sinistro***, vale a dire che le operazioni vengano eseguite da sinistra a destra.</span><span class="sxs-lookup"><span data-stu-id="8624e-273">Except for the assignment operators and the null coalescing operator, all binary operators are ***left-associative***, meaning that operations are performed from left to right.</span></span> <span data-ttu-id="8624e-274">L'espressione `x + y + z` viene ad esempio valutata come `(x + y) + z`.</span><span class="sxs-lookup"><span data-stu-id="8624e-274">For example, `x + y + z` is evaluated as `(x + y) + z`.</span></span>
*  <span data-ttu-id="8624e-275">Gli operatori di assegnazione, l'operatore null-coalescing e l'operatore condizionale (`?:`) vengono ***associa all'operando destro***, vale a dire che operazioni vengono eseguite da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="8624e-275">The assignment operators, the null coalescing operator and the conditional operator (`?:`) are ***right-associative***, meaning that operations are performed from right to left.</span></span> <span data-ttu-id="8624e-276">L'espressione `x = y = z` viene ad esempio valutata come `x = (y = z)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-276">For example, `x = y = z` is evaluated as `x = (y = z)`.</span></span>

<span data-ttu-id="8624e-277">È possibile controllare la precedenza e l'associatività usando le parentesi.</span><span class="sxs-lookup"><span data-stu-id="8624e-277">Precedence and associativity can be controlled using parentheses.</span></span> <span data-ttu-id="8624e-278">Ad esempio, `x + y * z` prima moltiplica `y` per `z` e quindi somma il risultato a `x`, ma `(x + y) * z` prima somma `x` e `y` e quindi moltiplica il risultato per `z`.</span><span class="sxs-lookup"><span data-stu-id="8624e-278">For example, `x + y * z` first multiplies `y` by `z` and then adds the result to `x`, but `(x + y) * z` first adds `x` and `y` and then multiplies the result by `z`.</span></span>

### <a name="operator-overloading"></a><span data-ttu-id="8624e-279">Overload degli operatori</span><span class="sxs-lookup"><span data-stu-id="8624e-279">Operator overloading</span></span>

<span data-ttu-id="8624e-280">Tutti gli operatori unari e binari sono predefiniti implementazioni che sono automaticamente disponibili in qualsiasi espressione.</span><span class="sxs-lookup"><span data-stu-id="8624e-280">All unary and binary operators have predefined implementations that are automatically available in any expression.</span></span> <span data-ttu-id="8624e-281">Oltre alle implementazioni predefinite, le implementazioni definite dall'utente possono essere introdotta includendo `operator` dichiarazioni di classi e struct ([operatori](classes.md#operators)).</span><span class="sxs-lookup"><span data-stu-id="8624e-281">In addition to the predefined implementations, user-defined implementations can be introduced by including `operator` declarations in classes and structs ([Operators](classes.md#operators)).</span></span> <span data-ttu-id="8624e-282">Implementazioni di operatori definiti dall'utente hanno sempre la precedenza sulle implementazioni di operatori predefiniti: Solo quando non applicabile operatore definito dall'utente esistano essere considerati le implementazioni di operatori predefiniti, come descritto in [risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution) e [overload degli operatori binari risoluzione](expressions.md#binary-operator-overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="8624e-282">User-defined operator implementations always take precedence over predefined operator implementations: Only when no applicable user-defined operator implementations exist will the predefined operator implementations be considered, as described in [Unary operator overload resolution](expressions.md#unary-operator-overload-resolution) and [Binary operator overload resolution](expressions.md#binary-operator-overload-resolution).</span></span>

<span data-ttu-id="8624e-283">Il ***operatori unari che supportano l'overload*** sono:</span><span class="sxs-lookup"><span data-stu-id="8624e-283">The ***overloadable unary operators*** are:</span></span>
```csharp
+   -   !   ~   ++   --   true   false
```

<span data-ttu-id="8624e-284">Sebbene `true` e `false` non vengono usati in modo esplicito nelle espressioni (e pertanto non sono incluse nella tabella delle precedenze in [precedenza e associatività degli operatori](expressions.md#operator-precedence-and-associativity)), sono considerati operatori perché sono richiamate in diversi contesti di espressione: espressioni booleane ([espressioni booleane](expressions.md#boolean-expressions)) ed espressioni che implicano condizionale ([operatore condizionale](expressions.md#conditional-operator)) e logica condizionale operatori ([degli operatori logici condizionali](expressions.md#conditional-logical-operators)).</span><span class="sxs-lookup"><span data-stu-id="8624e-284">Although `true` and `false` are not used explicitly in expressions (and therefore are not included in the precedence table in [Operator precedence and associativity](expressions.md#operator-precedence-and-associativity)), they are considered operators because they are invoked in several expression contexts: boolean expressions ([Boolean expressions](expressions.md#boolean-expressions)) and expressions involving the conditional ([Conditional operator](expressions.md#conditional-operator)), and conditional logical operators ([Conditional logical operators](expressions.md#conditional-logical-operators)).</span></span>

<span data-ttu-id="8624e-285">Il ***che supportano l'overload degli operatori binari*** sono:</span><span class="sxs-lookup"><span data-stu-id="8624e-285">The ***overloadable binary operators*** are:</span></span>
```csharp
+   -   *   /   %   &   |   ^   <<   >>   ==   !=   >   <   >=   <=
```

<span data-ttu-id="8624e-286">Solo gli operatori elencati in precedenza possono essere sottoposti a overload.</span><span class="sxs-lookup"><span data-stu-id="8624e-286">Only the operators listed above can be overloaded.</span></span> <span data-ttu-id="8624e-287">In particolare, non è possibile eseguire l'overload di accesso ai membri, chiamata a un metodo o la `=`, `&&`, `||`, `??`, `?:`, `=>`, `checked`, `unchecked`, `new`, `typeof`, `default`, `as`, e `is` operatori.</span><span class="sxs-lookup"><span data-stu-id="8624e-287">In particular, it is not possible to overload member access, method invocation, or the `=`, `&&`, `||`, `??`, `?:`, `=>`, `checked`, `unchecked`, `new`, `typeof`, `default`, `as`, and `is` operators.</span></span>

<span data-ttu-id="8624e-288">Quando viene eseguito l'overload di un operatore binario, viene anche eseguito in modo implicito l'overload dell'operatore di assegnazione corrispondente, se presente.</span><span class="sxs-lookup"><span data-stu-id="8624e-288">When a binary operator is overloaded, the corresponding assignment operator, if any, is also implicitly overloaded.</span></span> <span data-ttu-id="8624e-289">Ad esempio, l'overload dell'operatore `*` è anche un overload dell'operatore `*=`.</span><span class="sxs-lookup"><span data-stu-id="8624e-289">For example, an overload of operator `*` is also an overload of operator `*=`.</span></span> <span data-ttu-id="8624e-290">Questo è descritto più dettagliatamente in [assegnazione composta](expressions.md#compound-assignment).</span><span class="sxs-lookup"><span data-stu-id="8624e-290">This is described further in [Compound assignment](expressions.md#compound-assignment).</span></span> <span data-ttu-id="8624e-291">Si noti che l'operatore di assegnazione (`=`) non possono essere sottoposti a overload.</span><span class="sxs-lookup"><span data-stu-id="8624e-291">Note that the assignment operator itself (`=`) cannot be overloaded.</span></span> <span data-ttu-id="8624e-292">Un'assegnazione sempre esegue una semplice copia bit per bit di un valore in una variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-292">An assignment always performs a simple bit-wise copy of a value into a variable.</span></span>

<span data-ttu-id="8624e-293">Eseguire il cast di operazioni, quali `(T)x`, sono sottoposti a overload, fornendo le conversioni definite dall'utente ([conversioni definite dall'utente](conversions.md#user-defined-conversions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-293">Cast operations, such as `(T)x`, are overloaded by providing user-defined conversions ([User-defined conversions](conversions.md#user-defined-conversions)).</span></span>

<span data-ttu-id="8624e-294">Elemento accedere, ad esempio `a[x]`, non viene considerato un operatore che supporti l'overload.</span><span class="sxs-lookup"><span data-stu-id="8624e-294">Element access, such as `a[x]`, is not considered an overloadable operator.</span></span> <span data-ttu-id="8624e-295">Definito dall'utente l'indicizzazione è invece supportato tramite gli indicizzatori ([indicizzatori](classes.md#indexers)).</span><span class="sxs-lookup"><span data-stu-id="8624e-295">Instead, user-defined indexing is supported through indexers ([Indexers](classes.md#indexers)).</span></span>

<span data-ttu-id="8624e-296">Nelle espressioni, operatori vengono fatto riferimento usando la notazione di operatore e nelle dichiarazioni, gli operatori vengono fatto riferimento utilizzando una notazione funzionale.</span><span class="sxs-lookup"><span data-stu-id="8624e-296">In expressions, operators are referenced using operator notation, and in declarations, operators are referenced using functional notation.</span></span> <span data-ttu-id="8624e-297">Nella tabella seguente viene illustrata la relazione tra le notazioni funzionale per operatori unari e binari e operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-297">The following table shows the relationship between operator and functional notations for unary and binary operators.</span></span> <span data-ttu-id="8624e-298">Nella prima voce, ovvero *op* denota qualsiasi operatore di prefisso unario che supporti l'overload.</span><span class="sxs-lookup"><span data-stu-id="8624e-298">In the first entry, *op* denotes any overloadable unary prefix operator.</span></span> <span data-ttu-id="8624e-299">Nella seconda voce *op* identifica gli operatori unari `++` e `--` operatori.</span><span class="sxs-lookup"><span data-stu-id="8624e-299">In the second entry, *op* denotes the unary postfix `++` and `--` operators.</span></span> <span data-ttu-id="8624e-300">Nella terza voce *op* denota qualsiasi operatore binario che supporti l'overload.</span><span class="sxs-lookup"><span data-stu-id="8624e-300">In the third entry, *op* denotes any overloadable binary operator.</span></span>


| <span data-ttu-id="8624e-301">__Notazione dell'operatore__</span><span class="sxs-lookup"><span data-stu-id="8624e-301">__Operator notation__</span></span> | <span data-ttu-id="8624e-302">__Notazione funzionale__</span><span class="sxs-lookup"><span data-stu-id="8624e-302">__Functional notation__</span></span> |
|-----------------------|-------------------------|
| `op x`                | `operator op(x)`        | 
| `x op`                | `operator op(x)`        | 
| `x op y`              | `operator op(x,y)`      | 

<span data-ttu-id="8624e-303">Le dichiarazioni di operatore definito dall'utente è sempre necessario almeno uno dei parametri di tipo classe o struct che contiene la dichiarazione dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-303">User-defined operator declarations always require at least one of the parameters to be of the class or struct type that contains the operator declaration.</span></span> <span data-ttu-id="8624e-304">Non è pertanto possibile che un operatore definito dall'utente avere la stessa firma già definito.</span><span class="sxs-lookup"><span data-stu-id="8624e-304">Thus, it is not possible for a user-defined operator to have the same signature as a predefined operator.</span></span>

<span data-ttu-id="8624e-305">Le dichiarazioni di operatore definito dall'utente non è possibile modificare la sintassi, la precedenza o associazione di un operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-305">User-defined operator declarations cannot modify the syntax, precedence, or associativity of an operator.</span></span> <span data-ttu-id="8624e-306">Ad esempio, il `/` operatore è sempre un operatore binario, sempre con il livello di priorità specificato nel [precedenza e associatività degli operatori](expressions.md#operator-precedence-and-associativity)ed è sempre l'associazione all'operando a sinistra.</span><span class="sxs-lookup"><span data-stu-id="8624e-306">For example, the `/` operator is always a binary operator, always has the precedence level specified in [Operator precedence and associativity](expressions.md#operator-precedence-and-associativity), and is always left-associative.</span></span>

<span data-ttu-id="8624e-307">Sebbene sia possibile per un operatore definito dall'utente eseguire qualsiasi calcolo che ragionevolmente, le implementazioni che producono risultati diversi da quelli previsti in modo intuitivo sono fortemente sconsigliate.</span><span class="sxs-lookup"><span data-stu-id="8624e-307">While it is possible for a user-defined operator to perform any computation it pleases, implementations that produce results other than those that are intuitively expected are strongly discouraged.</span></span> <span data-ttu-id="8624e-308">Ad esempio, un'implementazione di `operator ==` deve confrontare i due operandi per verificare l'uguaglianza e restituire un oggetto appropriato `bool` risultato.</span><span class="sxs-lookup"><span data-stu-id="8624e-308">For example, an implementation of `operator ==` should compare the two operands for equality and return an appropriate `bool` result.</span></span>

<span data-ttu-id="8624e-309">Le descrizioni dei singoli operatori [espressioni primarie](expressions.md#primary-expressions) attraverso [operatori logici condizionali](expressions.md#conditional-logical-operators) specificare le implementazioni predefinite degli operatori e tutte le regole aggiuntive che si applicano a ogni operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-309">The descriptions of individual operators in [Primary expressions](expressions.md#primary-expressions) through [Conditional logical operators](expressions.md#conditional-logical-operators) specify the predefined implementations of the operators and any additional rules that apply to each operator.</span></span> <span data-ttu-id="8624e-310">Vengono inoltre usare i termini ***risoluzione dell'overload di operatore unario***, ***risoluzione dell'overload di operatore binario***, e ***innalzamento di livello***, definizioni dei quali sono vedere le sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="8624e-310">The descriptions make use of the terms ***unary operator overload resolution***, ***binary operator overload resolution***, and ***numeric promotion***, definitions of which are found in the following sections.</span></span>

### <a name="unary-operator-overload-resolution"></a><span data-ttu-id="8624e-311">Risoluzione dell'overload di operatore unario</span><span class="sxs-lookup"><span data-stu-id="8624e-311">Unary operator overload resolution</span></span>

<span data-ttu-id="8624e-312">Un'operazione del form `op x` oppure `x op`, dove `op` è un operatore unario che supporti l'overload, e `x` è un'espressione di tipo `X`, viene elaborato come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-312">An operation of the form `op x` or `x op`, where `op` is an overloadable unary operator, and `x` is an expression of type `X`, is processed as follows:</span></span>

*  <span data-ttu-id="8624e-313">Il set di operatori definiti dall'utente candidato fornita da `X` per l'operazione `operator op(x)` viene determinato mediante le regole delle [operatori definiti dall'utente candidato](expressions.md#candidate-user-defined-operators).</span><span class="sxs-lookup"><span data-stu-id="8624e-313">The set of candidate user-defined operators provided by `X` for the operation `operator op(x)` is determined using the rules of [Candidate user-defined operators](expressions.md#candidate-user-defined-operators).</span></span>
*  <span data-ttu-id="8624e-314">Se il set di operatori definiti dall'utente candidato non è vuoto, questo diventa quindi il set di operatori di candidati per l'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-314">If the set of candidate user-defined operators is not empty, then this becomes the set of candidate operators for the operation.</span></span> <span data-ttu-id="8624e-315">In caso contrario, l'unario predefinita `operator op` implementazioni, incluse le forme lift, diventano il set di operatori di candidati per l'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-315">Otherwise, the predefined unary `operator op` implementations, including their lifted forms, become the set of candidate operators for the operation.</span></span> <span data-ttu-id="8624e-316">Le implementazioni predefinite di un determinato operatore vengono specificate nella descrizione dell'operatore ([espressioni primarie](expressions.md#primary-expressions) e [operatori unari](expressions.md#unary-operators)).</span><span class="sxs-lookup"><span data-stu-id="8624e-316">The predefined implementations of a given operator are specified in the description of the operator ([Primary expressions](expressions.md#primary-expressions) and [Unary operators](expressions.md#unary-operators)).</span></span>
*  <span data-ttu-id="8624e-317">Le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution) vengono applicate al set di operatori di candidati per selezionare l'operatore migliori rispetto all'elenco di argomenti `(x)`, e questo operatore diventa il risultato dell'overload processo di risoluzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-317">The overload resolution rules of [Overload resolution](expressions.md#overload-resolution) are applied to the set of candidate operators to select the best operator with respect to the argument list `(x)`, and this operator becomes the result of the overload resolution process.</span></span> <span data-ttu-id="8624e-318">Se la risoluzione dell'overload non riesce selezionare un singolo operatore ottimo, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-318">If overload resolution fails to select a single best operator, a binding-time error occurs.</span></span>

### <a name="binary-operator-overload-resolution"></a><span data-ttu-id="8624e-319">Risoluzione dell'overload di operatore binario</span><span class="sxs-lookup"><span data-stu-id="8624e-319">Binary operator overload resolution</span></span>

<span data-ttu-id="8624e-320">Un'operazione del form `x op y`, dove `op` è un operatore binario che supporti l'overload `x` è un'espressione di tipo `X`, e `y` è un'espressione di tipo `Y`, viene elaborato come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-320">An operation of the form `x op y`, where `op` is an overloadable binary operator, `x` is an expression of type `X`, and `y` is an expression of type `Y`, is processed as follows:</span></span>

*  <span data-ttu-id="8624e-321">Il set di operatori definiti dall'utente candidato fornita da `X` e `Y` per l'operazione `operator op(x,y)` è determinata.</span><span class="sxs-lookup"><span data-stu-id="8624e-321">The set of candidate user-defined operators provided by `X` and `Y` for the operation `operator op(x,y)` is determined.</span></span> <span data-ttu-id="8624e-322">Il set è costituito dall'unione degli operatori candidato fornita da `X` e gli operatori candidato forniti da `Y`, ogni determinata usando le regole delle [operatori definiti dall'utente candidato](expressions.md#candidate-user-defined-operators).</span><span class="sxs-lookup"><span data-stu-id="8624e-322">The set consists of the union of the candidate operators provided by `X` and the candidate operators provided by `Y`, each determined using the rules of [Candidate user-defined operators](expressions.md#candidate-user-defined-operators).</span></span> <span data-ttu-id="8624e-323">Se `X` e `Y` sono dello stesso tipo, o se `X` e `Y` derivano da un tipo di base comune, quindi gli operatori condiviso candidato presenti solo nel set combinato di una volta.</span><span class="sxs-lookup"><span data-stu-id="8624e-323">If `X` and `Y` are the same type, or if `X` and `Y` are derived from a common base type, then shared candidate operators only occur in the combined set once.</span></span>
*  <span data-ttu-id="8624e-324">Se il set di operatori definiti dall'utente candidato non è vuoto, questo diventa quindi il set di operatori di candidati per l'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-324">If the set of candidate user-defined operators is not empty, then this becomes the set of candidate operators for the operation.</span></span> <span data-ttu-id="8624e-325">In caso contrario, il file binario predefinito `operator op` implementazioni, incluse le forme lift, diventano il set di operatori di candidati per l'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-325">Otherwise, the predefined binary `operator op` implementations, including their lifted forms,  become the set of candidate operators for the operation.</span></span> <span data-ttu-id="8624e-326">Le implementazioni predefinite di un determinato operatore vengono specificate nella descrizione dell'operatore ([operatori aritmetici](expressions.md#arithmetic-operators) attraverso [operatori logici condizionali](expressions.md#conditional-logical-operators)).</span><span class="sxs-lookup"><span data-stu-id="8624e-326">The predefined implementations of a given operator are specified in the description of the operator ([Arithmetic operators](expressions.md#arithmetic-operators) through [Conditional logical operators](expressions.md#conditional-logical-operators)).</span></span> <span data-ttu-id="8624e-327">Per gli operatori enum e il delegato predefiniti, gli unici operatori considerati sono quelle definite da un tipo di enumerazione o un delegato che rappresenta il tipo in fase di associazione di uno degli operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-327">For predefined enum and delegate operators, the only operators considered are those defined by an enum or delegate type that is the binding-time type of one of the operands.</span></span>
*  <span data-ttu-id="8624e-328">Le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution) vengono applicate al set di operatori di candidati per selezionare l'operatore migliori rispetto all'elenco di argomenti `(x,y)`, e questo operatore diventa il risultato dell'overload processo di risoluzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-328">The overload resolution rules of [Overload resolution](expressions.md#overload-resolution) are applied to the set of candidate operators to select the best operator with respect to the argument list `(x,y)`, and this operator becomes the result of the overload resolution process.</span></span> <span data-ttu-id="8624e-329">Se la risoluzione dell'overload non riesce selezionare un singolo operatore ottimo, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-329">If overload resolution fails to select a single best operator, a binding-time error occurs.</span></span>

### <a name="candidate-user-defined-operators"></a><span data-ttu-id="8624e-330">Operatori definiti dall'utente candidato</span><span class="sxs-lookup"><span data-stu-id="8624e-330">Candidate user-defined operators</span></span>

<span data-ttu-id="8624e-331">Dato un tipo `T` e un'operazione `operator op(A)`, dove `op` è un operatore che supporti l'overload e `A` è un elenco di argomenti, il set di candidati operatori definiti dall'utente forniti da `T` per `operator op(A)` viene determinato come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-331">Given a type `T` and an operation `operator op(A)`, where `op` is an overloadable operator and `A` is an argument list, the set of candidate user-defined operators provided by `T` for `operator op(A)` is determined as follows:</span></span>

*  <span data-ttu-id="8624e-332">Determinare il tipo `T0`.</span><span class="sxs-lookup"><span data-stu-id="8624e-332">Determine the type `T0`.</span></span> <span data-ttu-id="8624e-333">Se `T` è un tipo nullable `T0` è il tipo sottostante, in caso contrario `T0` è uguale a `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-333">If `T` is a nullable type, `T0` is its underlying type, otherwise `T0` is equal to `T`.</span></span>
*  <span data-ttu-id="8624e-334">Per tutti i `operator op` nelle dichiarazioni `T0` e tutti i moduli di questi operatori, è elevato se almeno un operatore è applicabile ([membro di funzione applicabile](expressions.md#applicable-function-member)) rispetto all'elenco di argomenti `A`, quindi il set di gli operatori candidato è costituito da tutti gli operatori applicabili in `T0`.</span><span class="sxs-lookup"><span data-stu-id="8624e-334">For all `operator op` declarations in `T0` and all lifted forms of such operators, if at least one operator is applicable ([Applicable function member](expressions.md#applicable-function-member)) with respect to the argument list `A`, then the set of candidate operators consists of all such applicable operators in `T0`.</span></span>
*  <span data-ttu-id="8624e-335">In caso contrario, se `T0` è `object`, il set di operatori candidato è vuoto.</span><span class="sxs-lookup"><span data-stu-id="8624e-335">Otherwise, if `T0` is `object`, the set of candidate operators is empty.</span></span>
*  <span data-ttu-id="8624e-336">In caso contrario, il set di operatori candidato disql `T0` è il set di operatori candidato forniti dalla classe di base diretta di `T0`, o la classe base effettiva del `T0` se `T0` è un parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-336">Otherwise, the set of candidate operators provided by `T0` is the set of candidate operators provided by the direct base class of `T0`, or the effective base class of `T0` if `T0` is a type parameter.</span></span>

### <a name="numeric-promotions"></a><span data-ttu-id="8624e-337">Promozioni numeriche</span><span class="sxs-lookup"><span data-stu-id="8624e-337">Numeric promotions</span></span>

<span data-ttu-id="8624e-338">Innalzamento di livello consiste nell'eseguire automaticamente alcune conversioni implicite gli operandi degli operatori numerici binari e unario predefinita.</span><span class="sxs-lookup"><span data-stu-id="8624e-338">Numeric promotion consists of automatically performing certain implicit conversions of the operands of the predefined unary and binary numeric operators.</span></span> <span data-ttu-id="8624e-339">Innalzamento di livello non è un meccanismo separato, ma piuttosto un effetto dell'applicazione di risoluzione dell'overload per gli operatori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="8624e-339">Numeric promotion is not a distinct mechanism, but rather an effect of applying overload resolution to the predefined operators.</span></span> <span data-ttu-id="8624e-340">Innalzamento di livello in modo specifico non influiscono sulla valutazione degli operatori definiti dall'utente, anche se gli operatori definiti dall'utente possono essere implementati per ottenere risultati simili.</span><span class="sxs-lookup"><span data-stu-id="8624e-340">Numeric promotion specifically does not affect evaluation of user-defined operators, although user-defined operators can be implemented to exhibit similar effects.</span></span>

<span data-ttu-id="8624e-341">Ad esempio di innalzamento di livello, prendere in considerazione le implementazioni predefinite del file binario `*` operatore:</span><span class="sxs-lookup"><span data-stu-id="8624e-341">As an example of numeric promotion, consider the predefined implementations of the binary `*` operator:</span></span>

```csharp
int operator *(int x, int y);
uint operator *(uint x, uint y);
long operator *(long x, long y);
ulong operator *(ulong x, ulong y);
float operator *(float x, float y);
double operator *(double x, double y);
decimal operator *(decimal x, decimal y);
```

<span data-ttu-id="8624e-342">Eseguire l'overload quando le regole di risoluzione ([risoluzione dell'Overload](expressions.md#overload-resolution)) vengono applicati a questo set di operatori, l'effetto è selezionato il primo operatore per cui sono presenti conversioni implicite dai tipi di operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-342">When overload resolution rules ([Overload resolution](expressions.md#overload-resolution)) are applied to this set of operators, the effect is to select the first of the operators for which implicit conversions exist from the operand types.</span></span> <span data-ttu-id="8624e-343">Ad esempio, per l'operazione `b * s`, dove `b` è un `byte` e `s` è un `short`, dell'overload seleziona la risoluzione `operator *(int,int)` come operatore migliore.</span><span class="sxs-lookup"><span data-stu-id="8624e-343">For example, for the operation `b * s`, where `b` is a `byte` and `s` is a `short`, overload resolution selects `operator *(int,int)` as the best operator.</span></span> <span data-ttu-id="8624e-344">Di conseguenza, il risultato è che `b` e `s` vengono convertiti `int`, e il tipo del risultato è `int`.</span><span class="sxs-lookup"><span data-stu-id="8624e-344">Thus, the effect is that `b` and `s` are converted to `int`, and the type of the result is `int`.</span></span> <span data-ttu-id="8624e-345">Analogamente, per l'operazione `i * d`, dove `i` è un `int` e `d` è un `double`, dell'overload seleziona la risoluzione dei `operator *(double,double)` come operatore migliore.</span><span class="sxs-lookup"><span data-stu-id="8624e-345">Likewise, for the operation `i * d`, where `i` is an `int` and `d` is a `double`, overload resolution selects `operator *(double,double)` as the best operator.</span></span>

#### <a name="unary-numeric-promotions"></a><span data-ttu-id="8624e-346">Promozioni numeriche unario</span><span class="sxs-lookup"><span data-stu-id="8624e-346">Unary numeric promotions</span></span>

<span data-ttu-id="8624e-347">Unario numeric vengano promossi per gli operandi degli operatori `+`, `-`, e `~` operatori unari.</span><span class="sxs-lookup"><span data-stu-id="8624e-347">Unary numeric promotion occurs for the operands of the predefined `+`, `-`, and `~` unary operators.</span></span> <span data-ttu-id="8624e-348">Innalzamento di livello unario costituita semplicemente gli operandi di tipo di conversione `sbyte`, `byte`, `short`, `ushort`, o `char` al tipo `int`.</span><span class="sxs-lookup"><span data-stu-id="8624e-348">Unary numeric promotion simply consists of converting operands of type `sbyte`, `byte`, `short`, `ushort`, or `char` to type `int`.</span></span> <span data-ttu-id="8624e-349">Inoltre, per l'operatore unario `-` operatore unario innalzamento di livello converte gli operandi di tipo `uint` al tipo `long`.</span><span class="sxs-lookup"><span data-stu-id="8624e-349">Additionally, for the unary `-` operator, unary numeric promotion converts operands of type `uint` to type `long`.</span></span>

#### <a name="binary-numeric-promotions"></a><span data-ttu-id="8624e-350">Promozioni numeriche binarie</span><span class="sxs-lookup"><span data-stu-id="8624e-350">Binary numeric promotions</span></span>

<span data-ttu-id="8624e-351">Innalzamento di livello binario viene generato per gli operandi degli operatori `+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `==`, `!=`, `>`, `<`, `>=`, e `<=` operatori binari.</span><span class="sxs-lookup"><span data-stu-id="8624e-351">Binary numeric promotion occurs for the operands of the predefined `+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `==`, `!=`, `>`, `<`, `>=`, and `<=` binary operators.</span></span> <span data-ttu-id="8624e-352">Innalzamento di livello binario in modo implicito converte entrambi gli operandi in un tipo comune che, in caso degli operatori non relazionale, diventa anche il tipo di risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-352">Binary numeric promotion implicitly converts both operands to a common type which, in case of the non-relational operators, also becomes the result type of the operation.</span></span> <span data-ttu-id="8624e-353">Innalzamento di livello binaria costituita applicando le regole seguenti, nell'ordine che vengano visualizzati qui:</span><span class="sxs-lookup"><span data-stu-id="8624e-353">Binary numeric promotion consists of applying the following rules, in the order they appear here:</span></span>

*  <span data-ttu-id="8624e-354">Se degli operandi è di tipo `decimal`, l'altro operando verrà convertito nel tipo `decimal`, o si verifica un errore in fase di associazione se l'altro operando è di tipo `float` o `double`.</span><span class="sxs-lookup"><span data-stu-id="8624e-354">If either operand is of type `decimal`, the other operand is converted to type `decimal`, or a binding-time error occurs if the other operand is of type `float` or `double`.</span></span>
*  <span data-ttu-id="8624e-355">In caso contrario, se degli operandi è di tipo `double`, l'altro operando verrà convertito nel tipo `double`.</span><span class="sxs-lookup"><span data-stu-id="8624e-355">Otherwise, if either operand is of type `double`, the other operand is converted to type `double`.</span></span>
*  <span data-ttu-id="8624e-356">In caso contrario, se degli operandi è di tipo `float`, l'altro operando verrà convertito nel tipo `float`.</span><span class="sxs-lookup"><span data-stu-id="8624e-356">Otherwise, if either operand is of type `float`, the other operand is converted to type `float`.</span></span>
*  <span data-ttu-id="8624e-357">In caso contrario, se degli operandi è di tipo `ulong`, l'altro operando verrà convertito nel tipo `ulong`, o si verifica un errore in fase di associazione se l'altro operando è di tipo `sbyte`, `short`, `int`, o `long`.</span><span class="sxs-lookup"><span data-stu-id="8624e-357">Otherwise, if either operand is of type `ulong`, the other operand is converted to type `ulong`, or a binding-time error occurs if the other operand is of type `sbyte`, `short`, `int`, or `long`.</span></span>
*  <span data-ttu-id="8624e-358">In caso contrario, se degli operandi è di tipo `long`, l'altro operando verrà convertito nel tipo `long`.</span><span class="sxs-lookup"><span data-stu-id="8624e-358">Otherwise, if either operand is of type `long`, the other operand is converted to type `long`.</span></span>
*  <span data-ttu-id="8624e-359">In caso contrario, se degli operandi è di tipo `uint` e l'altro operando è di tipo `sbyte`, `short`, o `int`, entrambi gli operandi verranno convertiti nel tipo `long`.</span><span class="sxs-lookup"><span data-stu-id="8624e-359">Otherwise, if either operand is of type `uint` and the other operand is of type `sbyte`, `short`, or `int`, both operands are converted to type `long`.</span></span>
*  <span data-ttu-id="8624e-360">In caso contrario, se degli operandi è di tipo `uint`, l'altro operando verrà convertito nel tipo `uint`.</span><span class="sxs-lookup"><span data-stu-id="8624e-360">Otherwise, if either operand is of type `uint`, the other operand is converted to type `uint`.</span></span>
*  <span data-ttu-id="8624e-361">In caso contrario, entrambi gli operandi verranno convertiti nel tipo `int`.</span><span class="sxs-lookup"><span data-stu-id="8624e-361">Otherwise, both operands are converted to type `int`.</span></span>

<span data-ttu-id="8624e-362">Si noti che la prima regola impedisce l'esecuzione di qualsiasi operazione che combinano il `decimal` tipo con il `double` e `float` tipi.</span><span class="sxs-lookup"><span data-stu-id="8624e-362">Note that the first rule disallows any operations that mix the `decimal` type with the `double` and `float` types.</span></span> <span data-ttu-id="8624e-363">La regola seguente dal fatto che non sono presenti conversioni implicite tra i `decimal` tipo e il `double` e `float` tipi.</span><span class="sxs-lookup"><span data-stu-id="8624e-363">The rule follows from the fact that there are no implicit conversions between the `decimal` type and the `double` and `float` types.</span></span>

<span data-ttu-id="8624e-364">Si noti anche che non è possibile che un operando di tipo `ulong` quando l'altro operando è di tipo integrale con segno.</span><span class="sxs-lookup"><span data-stu-id="8624e-364">Also note that it is not possible for an operand to be of type `ulong` when the other operand is of a signed integral type.</span></span> <span data-ttu-id="8624e-365">Il motivo è che esiste alcun tipo integrale che può rappresentare la gamma completa di `ulong` , nonché i tipi integrali con segno.</span><span class="sxs-lookup"><span data-stu-id="8624e-365">The reason is that no integral type exists that can represent the full range of `ulong` as well as the signed integral types.</span></span>

<span data-ttu-id="8624e-366">In entrambi i casi precedenti, un'espressione cast è utilizzabile da convertire in modo esplicito uno degli operandi a un tipo compatibile con l'altro operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-366">In both of the above cases, a cast expression can be used to explicitly convert one operand to a type that is compatible with the other operand.</span></span>

<span data-ttu-id="8624e-367">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-367">In the example</span></span>
```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (1.0 + percent / 100.0);
}
```
<span data-ttu-id="8624e-368">si verifica un errore in fase di associazione perché una `decimal` non può essere moltiplicato con un `double`.</span><span class="sxs-lookup"><span data-stu-id="8624e-368">a binding-time error occurs because a `decimal` cannot be multiplied by a `double`.</span></span> <span data-ttu-id="8624e-369">L'errore viene risolto mediante la conversione in modo esplicito il secondo operando a `decimal`, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-369">The error is resolved by explicitly converting the second operand to `decimal`, as follows:</span></span>

```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (decimal)(1.0 + percent / 100.0);
}
```

### <a name="lifted-operators"></a><span data-ttu-id="8624e-370">Operatori elevati</span><span class="sxs-lookup"><span data-stu-id="8624e-370">Lifted operators</span></span>

<span data-ttu-id="8624e-371">***Gli operatori è elevato*** consentono di predefinite e definite dall'utente operatori che operano sui tipi di valore non nullable per essere usato anche con le forme di tali tipi nullable.</span><span class="sxs-lookup"><span data-stu-id="8624e-371">***Lifted operators*** permit predefined and user-defined operators that operate on non-nullable value types to also be used with nullable forms of those types.</span></span> <span data-ttu-id="8624e-372">Gli operatori lift sono costruiti da operatori predefiniti e definiti dall'utente che soddisfano determinati requisiti, come descritto di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-372">Lifted operators are constructed from predefined and user-defined operators that meet certain requirements, as described in the following:</span></span>

*   <span data-ttu-id="8624e-373">Per gli operatori unari</span><span class="sxs-lookup"><span data-stu-id="8624e-373">For the unary operators</span></span>

    ```csharp
    +  ++  -  --  !  ~
    ```

    <span data-ttu-id="8624e-374">esiste una forma lift di un operatore se i tipi di operando e il risultato sono entrambi tipi di valore non nullable.</span><span class="sxs-lookup"><span data-stu-id="8624e-374">a lifted form of an operator exists if the operand and result types are both non-nullable value types.</span></span> <span data-ttu-id="8624e-375">La forma elevata si ottiene mediante l'aggiunta di un singolo `?` modificatore ai tipi di operando e il risultato.</span><span class="sxs-lookup"><span data-stu-id="8624e-375">The lifted form is constructed by adding a single `?` modifier to the operand and result types.</span></span> <span data-ttu-id="8624e-376">L'operatore elevata produce un valore null se l'operando è null.</span><span class="sxs-lookup"><span data-stu-id="8624e-376">The lifted operator produces a null value if the operand is null.</span></span> <span data-ttu-id="8624e-377">In caso contrario, l'operatore lift Annulla il wrapping dell'operando, si applica l'operatore sottostante e include il risultato.</span><span class="sxs-lookup"><span data-stu-id="8624e-377">Otherwise, the lifted operator unwraps the operand, applies the underlying operator, and wraps the result.</span></span>

*   <span data-ttu-id="8624e-378">Per gli operatori binari</span><span class="sxs-lookup"><span data-stu-id="8624e-378">For the binary operators</span></span>

    ```csharp
    +  -  *  /  %  &  |  ^  <<  >>
    ```

    <span data-ttu-id="8624e-379">esiste una forma lift di un operatore se i tipi di operando e il risultato sono tutti i tipi di valore non nullable.</span><span class="sxs-lookup"><span data-stu-id="8624e-379">a lifted form of an operator exists if the operand and result types are all non-nullable value types.</span></span> <span data-ttu-id="8624e-380">La forma elevata si ottiene mediante l'aggiunta di un singolo `?` modificatore a ogni tipo di operando e il risultato.</span><span class="sxs-lookup"><span data-stu-id="8624e-380">The lifted form is constructed by adding a single `?` modifier to each operand and result type.</span></span> <span data-ttu-id="8624e-381">L'operatore elevata produce un valore null se uno o entrambi gli operandi sono null (un'eccezione in fase di `&` e `|` operatori del `bool?` digitare, come descritto in [booleani operatori logici](expressions.md#boolean-logical-operators)).</span><span class="sxs-lookup"><span data-stu-id="8624e-381">The lifted operator produces a null value if one or both operands are null (an exception being the `&` and `|` operators of the `bool?` type, as described in [Boolean logical operators](expressions.md#boolean-logical-operators)).</span></span> <span data-ttu-id="8624e-382">In caso contrario, l'operatore lift Annulla il wrapping degli operandi, si applica l'operatore sottostante e include il risultato.</span><span class="sxs-lookup"><span data-stu-id="8624e-382">Otherwise, the lifted operator unwraps the operands, applies the underlying operator, and wraps the result.</span></span>

*   <span data-ttu-id="8624e-383">Per gli operatori di uguaglianza</span><span class="sxs-lookup"><span data-stu-id="8624e-383">For the equality operators</span></span>

    ```csharp
    ==  !=
    ```

    <span data-ttu-id="8624e-384">esiste una forma lift di un operatore se i tipi di operando sono entrambi tipi di valore non nullable e se il tipo di risultato è `bool`.</span><span class="sxs-lookup"><span data-stu-id="8624e-384">a lifted form of an operator exists if the operand types are both non-nullable value types and if the result type is `bool`.</span></span> <span data-ttu-id="8624e-385">La forma elevata si ottiene mediante l'aggiunta di un singolo `?` a ciascun tipo di operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-385">The lifted form is constructed by adding a single `?` modifier to each operand type.</span></span> <span data-ttu-id="8624e-386">L'operatore lift considera uguali due valori null e un valore null non uguali a qualsiasi valore diverso da null.</span><span class="sxs-lookup"><span data-stu-id="8624e-386">The lifted operator considers two null values equal, and a null value unequal to any non-null value.</span></span> <span data-ttu-id="8624e-387">Se entrambi gli operandi sono diversi da null, l'operatore lift Annulla il wrapping degli operandi e applica l'operatore sottostante per produrre il `bool` risultato.</span><span class="sxs-lookup"><span data-stu-id="8624e-387">If both operands are non-null, the lifted operator unwraps the operands and applies the underlying operator to produce the `bool` result.</span></span>

*   <span data-ttu-id="8624e-388">Per gli operatori relazionali</span><span class="sxs-lookup"><span data-stu-id="8624e-388">For the relational operators</span></span>

    ```csharp
    <  >  <=  >=
    ```

    <span data-ttu-id="8624e-389">esiste una forma lift di un operatore se i tipi di operando sono entrambi tipi di valore non nullable e se il tipo di risultato è `bool`.</span><span class="sxs-lookup"><span data-stu-id="8624e-389">a lifted form of an operator exists if the operand types are both non-nullable value types and if the result type is `bool`.</span></span> <span data-ttu-id="8624e-390">La forma elevata si ottiene mediante l'aggiunta di un singolo `?` a ciascun tipo di operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-390">The lifted form is constructed by adding a single `?` modifier to each operand type.</span></span> <span data-ttu-id="8624e-391">L'operatore elevata produce il valore `false` se uno o entrambi gli operandi sono null.</span><span class="sxs-lookup"><span data-stu-id="8624e-391">The lifted operator produces the value `false` if one or both operands are null.</span></span> <span data-ttu-id="8624e-392">In caso contrario, l'operatore lift Annulla il wrapping degli operandi e applica l'operatore sottostante per produrre il `bool` risultato.</span><span class="sxs-lookup"><span data-stu-id="8624e-392">Otherwise, the lifted operator unwraps the operands and applies the underlying operator to produce the `bool` result.</span></span>

## <a name="member-lookup"></a><span data-ttu-id="8624e-393">Ricerca di membri</span><span class="sxs-lookup"><span data-stu-id="8624e-393">Member lookup</span></span>

<span data-ttu-id="8624e-394">Una ricerca di membri è il processo in base al quale viene determinato il significato di un nome nel contesto di un tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-394">A member lookup is the process whereby the meaning of a name in the context of a type is determined.</span></span> <span data-ttu-id="8624e-395">Una ricerca di membri può verificarsi come parte della valutazione di una *simple_name* ([nomi semplici](expressions.md#simple-names)) o un *member_access* ([l'accesso ai membri](expressions.md#member-access)) in un espressione.</span><span class="sxs-lookup"><span data-stu-id="8624e-395">A member lookup can occur as part of evaluating a *simple_name* ([Simple names](expressions.md#simple-names)) or a *member_access* ([Member access](expressions.md#member-access)) in an expression.</span></span> <span data-ttu-id="8624e-396">Se il *simple_name* o *member_access* avviene quando il *primary_expression* di un *invocation_expression* ([ Chiamate al metodo](expressions.md#method-invocations)), viene definito il membro da richiamare.</span><span class="sxs-lookup"><span data-stu-id="8624e-396">If the *simple_name* or *member_access* occurs as the *primary_expression* of an *invocation_expression* ([Method invocations](expressions.md#method-invocations)), the member is said to be invoked.</span></span>

<span data-ttu-id="8624e-397">Se il membro è un metodo o evento, o se è una costante, un campo o una proprietà di un tipo di delegato ([delegati](delegates.md)) o il tipo `dynamic` ([tipo dinamico](types.md#the-dynamic-type)), quindi il membro è detto *invocable*.</span><span class="sxs-lookup"><span data-stu-id="8624e-397">If a member is a method or event, or if it is a constant, field or property of either a delegate type ([Delegates](delegates.md)) or the type `dynamic` ([The dynamic type](types.md#the-dynamic-type)), then the member is said to be *invocable*.</span></span>

<span data-ttu-id="8624e-398">Ricerca di membri prende in considerazione non solo il nome del membro, ma anche il numero di parametri di tipo che contiene il membro e indica se il membro è accessibile.</span><span class="sxs-lookup"><span data-stu-id="8624e-398">Member lookup considers not only the name of a member but also the number of type parameters the member has and whether the member is accessible.</span></span> <span data-ttu-id="8624e-399">Ai fini della ricerca dei membri, i metodi generici e tipi generici annidati hanno il numero di parametri di tipo indicati nelle rispettive dichiarazioni e tutti gli altri membri con zero parametri di tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-399">For the purposes of member lookup, generic methods and nested generic types have the number of type parameters indicated in their respective declarations and all other members have zero type parameters.</span></span>

<span data-ttu-id="8624e-400">Una ricerca di membri di un nome `N` con `K`  parametri di tipo in un tipo `T` viene elaborato come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-400">A member lookup of a name `N` with `K` type parameters in a type `T` is processed as follows:</span></span>

*  <span data-ttu-id="8624e-401">Per prima cosa, un set di membri accessibili denominato `N` viene determinato:</span><span class="sxs-lookup"><span data-stu-id="8624e-401">First, a set of accessible members named `N` is determined:</span></span>
    * <span data-ttu-id="8624e-402">Se `T` è un parametro di tipo, il set è l'unione dei set di membri accessibili denominati `N` in ciascuno dei tipi specificati come un vincolo primario o secondario vincolo ([digitare i vincoli del parametro](classes.md#type-parameter-constraints)) per  `T`, insieme ai set di membri accessibili denominato `N` in `object`.</span><span class="sxs-lookup"><span data-stu-id="8624e-402">If `T` is a type parameter, then the set is the union of the sets of accessible members named `N` in each of the types specified as a primary constraint or secondary constraint ([Type parameter constraints](classes.md#type-parameter-constraints)) for `T`, along with the set of accessible members named `N` in `object`.</span></span>
    * <span data-ttu-id="8624e-403">In caso contrario, il set è costituito tutto accessibile ([l'accesso ai membri](basic-concepts.md#member-access)) membri denominati `N` nelle `T`, inclusi i membri ereditati e i membri accessibili denominati `N` in `object`.</span><span class="sxs-lookup"><span data-stu-id="8624e-403">Otherwise, the set consists of all accessible ([Member access](basic-concepts.md#member-access)) members named `N` in `T`, including inherited members and the accessible members named `N` in `object`.</span></span> <span data-ttu-id="8624e-404">Se `T` è un tipo costruito, il set di membri viene ottenuto sostituendo gli argomenti di tipo come descritto in [i membri di tipi costruiti](classes.md#members-of-constructed-types).</span><span class="sxs-lookup"><span data-stu-id="8624e-404">If `T` is a constructed type, the set of members is obtained by substituting type arguments as described in [Members of constructed types](classes.md#members-of-constructed-types).</span></span> <span data-ttu-id="8624e-405">I membri che includono un `override` modificatore vengono esclusi dal set.</span><span class="sxs-lookup"><span data-stu-id="8624e-405">Members that include an `override` modifier are excluded from the set.</span></span>
*  <span data-ttu-id="8624e-406">Successivamente, se `K` è uguale a zero, tutto annidati vengono rimossi i tipi cui dichiarazioni di includono i parametri di tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-406">Next, if `K` is zero, all nested types whose declarations include type parameters are removed.</span></span> <span data-ttu-id="8624e-407">Se `K` è diverso da zero, tutti i membri con un numero diverso di parametri di tipo vengono rimossi.</span><span class="sxs-lookup"><span data-stu-id="8624e-407">If `K` is not zero, all members with a different number of type parameters are removed.</span></span> <span data-ttu-id="8624e-408">Si noti che quando `K` sono zero, i metodi con tipo di parametri non vengono rimossi, poiché il processo di inferenza del tipo ([inferenza del tipo](expressions.md#type-inference)) potrebbe essere in grado di dedurre gli argomenti di tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-408">Note that when `K` is zero, methods having type parameters are not removed, since the type inference process ([Type inference](expressions.md#type-inference)) might be able to infer the type arguments.</span></span>
*  <span data-ttu-id="8624e-409">Quindi, se il membro è *richiamato*, tutti gli elementi non-*invocable* i membri vengono rimossi dal set.</span><span class="sxs-lookup"><span data-stu-id="8624e-409">Next, if the member is *invoked*, all non-*invocable* members are removed from the set.</span></span>
*  <span data-ttu-id="8624e-410">Successivamente, i membri che sono nascosti da altri membri vengono rimossi dal set.</span><span class="sxs-lookup"><span data-stu-id="8624e-410">Next, members that are hidden by other members are removed from the set.</span></span> <span data-ttu-id="8624e-411">Per ogni membro `S.M` nel set, in cui `S` è il tipo in cui il membro `M` viene dichiarata, vengono applicate le regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-411">For every member `S.M` in the set, where `S` is the type in which the member `M` is declared, the following rules are applied:</span></span>
    * <span data-ttu-id="8624e-412">Se `M` è una costante, campo, proprietà, eventi o membro di enumerazione, quindi tutti i membri dichiarati nel tipo di base `S` vengono rimosse dal set.</span><span class="sxs-lookup"><span data-stu-id="8624e-412">If `M` is a constant, field, property, event, or enumeration member, then all members declared in a base type of `S` are removed from the set.</span></span>
    * <span data-ttu-id="8624e-413">Se `M` è una dichiarazione del tipo, quindi tutti non tipi dichiarati nel tipo di base `S` vengono rimosse dal set e tutte le dichiarazioni con lo stesso numero di parametri di tipo come di tipo `M` dichiarato nel tipo di base `S` vengono rimossi dal set.</span><span class="sxs-lookup"><span data-stu-id="8624e-413">If `M` is a type declaration, then all non-types declared in a base type of `S` are removed from the set, and all type declarations with the same number of type parameters as `M` declared in a base type of `S` are removed from the set.</span></span>
    * <span data-ttu-id="8624e-414">Se `M` è un metodo, quindi tutti i membri non metodo dichiarato in un tipo di base di `S` vengono rimosse dal set.</span><span class="sxs-lookup"><span data-stu-id="8624e-414">If `M` is a method, then all non-method members declared in a base type of `S` are removed from the set.</span></span>
*  <span data-ttu-id="8624e-415">Successivamente, i membri di interfaccia che sono nascosti per i membri della classe vengono rimosse dal set.</span><span class="sxs-lookup"><span data-stu-id="8624e-415">Next, interface members that are hidden by class members are removed from the set.</span></span> <span data-ttu-id="8624e-416">Questo passaggio ha effetto solo se `T` è un parametro di tipo e `T` è diverso da entrambi una classe base effettiva `object` e un insieme di interfacce efficace non vuoto ([digitare i vincoli del parametro](classes.md#type-parameter-constraints)).</span><span class="sxs-lookup"><span data-stu-id="8624e-416">This step only has an effect if `T` is a type parameter and `T` has both an effective base class other than `object` and a non-empty effective interface set ([Type parameter constraints](classes.md#type-parameter-constraints)).</span></span> <span data-ttu-id="8624e-417">Per ogni membro `S.M` nel set, in cui `S` è il tipo in cui il membro `M` viene dichiarato, le regole seguenti vengono applicate se `S` è diverso da una dichiarazione di classe `object`:</span><span class="sxs-lookup"><span data-stu-id="8624e-417">For every member `S.M` in the set, where `S` is the type in which the member `M` is declared, the following rules are applied if `S` is a class declaration other than `object`:</span></span>
    * <span data-ttu-id="8624e-418">Se `M` è una costante, campo, proprietà, eventi, membro di enumerazione o dichiarazione del tipo, quindi tutti i membri dichiarati in una dichiarazione di interfaccia vengono rimosse dal set.</span><span class="sxs-lookup"><span data-stu-id="8624e-418">If `M` is a constant, field, property, event, enumeration member, or type declaration, then all members declared in an interface declaration are removed from the set.</span></span>
    * <span data-ttu-id="8624e-419">Se `M` è un metodo, quindi tutti i membri non-method dichiarati in una dichiarazione di interfaccia vengono rimossi dal set e tutti i metodi con la stessa firma `M` dichiarati in un'interfaccia di dichiarazione vengono rimossi dal set.</span><span class="sxs-lookup"><span data-stu-id="8624e-419">If `M` is a method, then all non-method members declared in an interface declaration are removed from the set, and all methods with the same signature as `M` declared in an interface declaration are removed from the set.</span></span>
*  <span data-ttu-id="8624e-420">Infine, avere rimosso i membri nascosti, viene determinato il risultato della ricerca:</span><span class="sxs-lookup"><span data-stu-id="8624e-420">Finally, having removed hidden members, the result of the lookup is determined:</span></span>
    * <span data-ttu-id="8624e-421">Se il set è costituito da un solo membro che non è un metodo, questo membro è il risultato della ricerca.</span><span class="sxs-lookup"><span data-stu-id="8624e-421">If the set consists of a single member that is not a method, then this member is the result of the lookup.</span></span>
    * <span data-ttu-id="8624e-422">In caso contrario, se il set contiene solo metodi, questo gruppo di metodi è il risultato della ricerca.</span><span class="sxs-lookup"><span data-stu-id="8624e-422">Otherwise, if the set contains only methods, then this group of methods is the result of the lookup.</span></span>
    * <span data-ttu-id="8624e-423">In caso contrario, la ricerca è ambigua e si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-423">Otherwise, the lookup is ambiguous, and a binding-time error occurs.</span></span>

<span data-ttu-id="8624e-424">Per le ricerche di membri di tipi diversi dai parametri di tipo e le interfacce e le ricerche di membri nelle interfacce che sono strettamente a ereditarietà singola (ogni interfaccia nella catena di ereditarietà include esattamente una o nessuna interfaccia di base diretta), l'effetto delle regole di ricerca è è sufficiente che derivato membri di base Nascondi membri con lo stesso nome o firma.</span><span class="sxs-lookup"><span data-stu-id="8624e-424">For member lookups in types other than type parameters and interfaces, and member lookups in interfaces that are strictly single-inheritance (each interface in the inheritance chain has exactly zero or one direct base interface), the effect of the lookup rules is simply that derived members hide base members with the same name or signature.</span></span> <span data-ttu-id="8624e-425">Tali ricerche a ereditarietà singola mai sono ambigue.</span><span class="sxs-lookup"><span data-stu-id="8624e-425">Such single-inheritance lookups are never ambiguous.</span></span> <span data-ttu-id="8624e-426">L'ambiguità che può derivare dalle ricerche di membri nelle interfacce di ereditarietà multipla è descritte nel [l'accesso ai membri di interfaccia](interfaces.md#interface-member-access).</span><span class="sxs-lookup"><span data-stu-id="8624e-426">The ambiguities that can possibly arise from member lookups in multiple-inheritance interfaces are described in [Interface member access](interfaces.md#interface-member-access).</span></span>

### <a name="base-types"></a><span data-ttu-id="8624e-427">Tipi di base</span><span class="sxs-lookup"><span data-stu-id="8624e-427">Base types</span></span>

<span data-ttu-id="8624e-428">Per scopi di ricerca di membri, un tipo `T` ha i tipi di base seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-428">For purposes of member lookup, a type `T` is considered to have the following base types:</span></span>

*  <span data-ttu-id="8624e-429">Se `T` viene `object`, quindi `T` non dispone di alcun tipo di base.</span><span class="sxs-lookup"><span data-stu-id="8624e-429">If `T` is `object`, then `T` has no base type.</span></span>
*  <span data-ttu-id="8624e-430">Se `T` è un *enum_type*, i tipi di base `T` sono i tipi di classe `System.Enum`, `System.ValueType`, e `object`.</span><span class="sxs-lookup"><span data-stu-id="8624e-430">If `T` is an *enum_type*, the base types of `T` are the class types `System.Enum`, `System.ValueType`, and `object`.</span></span>
*  <span data-ttu-id="8624e-431">Se `T` è un *struct_type*, i tipi di base `T` sono i tipi di classe `System.ValueType` e `object`.</span><span class="sxs-lookup"><span data-stu-id="8624e-431">If `T` is a *struct_type*, the base types of `T` are the class types `System.ValueType` and `object`.</span></span>
*  <span data-ttu-id="8624e-432">Se `T` è un *class_type*, i tipi di base del `T` sono le classi di base `T`, incluso il tipo di classe `object`.</span><span class="sxs-lookup"><span data-stu-id="8624e-432">If `T` is a *class_type*, the base types of `T` are the base classes of `T`, including the class type `object`.</span></span>
*  <span data-ttu-id="8624e-433">Se `T` è un *interface_type*, i tipi di base `T` sono le interfacce di base di `T` e il tipo di classe `object`.</span><span class="sxs-lookup"><span data-stu-id="8624e-433">If `T` is an *interface_type*, the base types of `T` are the base interfaces of `T` and the class type `object`.</span></span>
*  <span data-ttu-id="8624e-434">Se `T` è un *array_type*, i tipi di base `T` sono i tipi di classe `System.Array` e `object`.</span><span class="sxs-lookup"><span data-stu-id="8624e-434">If `T` is an *array_type*, the base types of `T` are the class types `System.Array` and `object`.</span></span>
*  <span data-ttu-id="8624e-435">Se `T` è un *delegate_type*, i tipi di base `T` sono i tipi di classe `System.Delegate` e `object`.</span><span class="sxs-lookup"><span data-stu-id="8624e-435">If `T` is a *delegate_type*, the base types of `T` are the class types `System.Delegate` and `object`.</span></span>

## <a name="function-members"></a><span data-ttu-id="8624e-436">Membri di funzione</span><span class="sxs-lookup"><span data-stu-id="8624e-436">Function members</span></span>

<span data-ttu-id="8624e-437">I membri di funzione sono i membri che contengono istruzioni eseguibili.</span><span class="sxs-lookup"><span data-stu-id="8624e-437">Function members are members that contain executable statements.</span></span> <span data-ttu-id="8624e-438">I membri di funzione sono sempre i membri dei tipi e non possono essere membri degli spazi dei nomi.</span><span class="sxs-lookup"><span data-stu-id="8624e-438">Function members are always members of types and cannot be members of namespaces.</span></span> <span data-ttu-id="8624e-439">C# definisce le categorie di membri di funzione seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-439">C# defines the following categories of function members:</span></span>

*  <span data-ttu-id="8624e-440">Metodi</span><span class="sxs-lookup"><span data-stu-id="8624e-440">Methods</span></span>
*  <span data-ttu-id="8624e-441">Proprietà</span><span class="sxs-lookup"><span data-stu-id="8624e-441">Properties</span></span>
*  <span data-ttu-id="8624e-442">Eventi</span><span class="sxs-lookup"><span data-stu-id="8624e-442">Events</span></span>
*  <span data-ttu-id="8624e-443">Indicizzatori</span><span class="sxs-lookup"><span data-stu-id="8624e-443">Indexers</span></span>
*  <span data-ttu-id="8624e-444">Operatori definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="8624e-444">User-defined operators</span></span>
*  <span data-ttu-id="8624e-445">Costruttori di istanza</span><span class="sxs-lookup"><span data-stu-id="8624e-445">Instance constructors</span></span>
*  <span data-ttu-id="8624e-446">Costruttori statici</span><span class="sxs-lookup"><span data-stu-id="8624e-446">Static constructors</span></span>
*  <span data-ttu-id="8624e-447">Distruttori</span><span class="sxs-lookup"><span data-stu-id="8624e-447">Destructors</span></span>

<span data-ttu-id="8624e-448">Ad eccezione di costruttori e distruttori statici (che non è possibile richiamare in modo esplicito), le istruzioni contenute nei membri di funzione vengono eseguite tramite le chiamate di funzione membro.</span><span class="sxs-lookup"><span data-stu-id="8624e-448">Except for destructors and static constructors (which cannot be invoked explicitly), the statements contained in function members are executed through function member invocations.</span></span> <span data-ttu-id="8624e-449">La sintassi per la scrittura di una chiamata alla funzione membro dipende dalla categoria del membro di funzione specifica.</span><span class="sxs-lookup"><span data-stu-id="8624e-449">The actual syntax for writing a function member invocation depends on the particular function member category.</span></span>

<span data-ttu-id="8624e-450">L'elenco di argomenti ([elenchi di argomenti](expressions.md#argument-lists)) di un membro di funzione chiamata fornisce i valori effettivi o riferimenti a variabili per i parametri del membro funzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-450">The argument list ([Argument lists](expressions.md#argument-lists)) of a function member invocation provides actual values or variable references for the parameters of the function member.</span></span>

<span data-ttu-id="8624e-451">Le chiamate dei metodi generici possono impiegare l'inferenza del tipo per determinare il set di argomenti di tipo da passare al metodo.</span><span class="sxs-lookup"><span data-stu-id="8624e-451">Invocations of generic methods may employ type inference to determine the set of type arguments to pass to the method.</span></span> <span data-ttu-id="8624e-452">Questo processo è descritto nella [inferenza del tipo](expressions.md#type-inference).</span><span class="sxs-lookup"><span data-stu-id="8624e-452">This process is described in [Type inference](expressions.md#type-inference).</span></span>

<span data-ttu-id="8624e-453">Nelle chiamate di metodi, indicizzatori, operatori e costruttori di istanza viene utilizzata la risoluzione dell'overload per determinare quali di un set di candidati di membri di funzione da richiamare.</span><span class="sxs-lookup"><span data-stu-id="8624e-453">Invocations of methods, indexers, operators and instance constructors employ overload resolution to determine which of a candidate set of function members to invoke.</span></span> <span data-ttu-id="8624e-454">Questo processo è descritto nella [risoluzione dell'Overload](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="8624e-454">This process is described in [Overload resolution](expressions.md#overload-resolution).</span></span>

<span data-ttu-id="8624e-455">Dopo aver identificato un membro di funzione specifica in fase di associazione, possibilmente tramite la risoluzione dell'overload, il processo in fase di esecuzione effettivo di richiamare il membro di funzione è descritto nella [controllo della risoluzione dell'overload dinamiciinfasedicompilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="8624e-455">Once a particular function member has been identified at binding-time, possibly through overload resolution, the actual run-time process of invoking the function member is described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="8624e-456">La tabella seguente riepiloga l'elaborazione che si verificano nei costrutti che includono le categorie di sei membri di funzione che possono essere richiamati in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="8624e-456">The following table summarizes the processing that takes place in constructs involving the six categories of function members that can be explicitly invoked.</span></span> <span data-ttu-id="8624e-457">Nella tabella `e`, `x`, `y`, e `value` indicano classificate come variabili o valori, le espressioni `T` indica un'espressione classificata come un tipo, `F` è il nome semplice di un metodo e `P` è il nome semplice di una proprietà.</span><span class="sxs-lookup"><span data-stu-id="8624e-457">In the table, `e`, `x`, `y`, and `value` indicate expressions classified as variables or values, `T` indicates an expression classified as a type, `F` is the simple name of a method, and `P` is the simple name of a property.</span></span>


| <span data-ttu-id="8624e-458">__Costrutto__</span><span class="sxs-lookup"><span data-stu-id="8624e-458">__Construct__</span></span>     | <span data-ttu-id="8624e-459">__Esempio__</span><span class="sxs-lookup"><span data-stu-id="8624e-459">__Example__</span></span>    | <span data-ttu-id="8624e-460">__Descrizione__</span><span class="sxs-lookup"><span data-stu-id="8624e-460">__Description__</span></span> |
|-------------------|----------------|-----------------|
| <span data-ttu-id="8624e-461">Chiamata del metodo</span><span class="sxs-lookup"><span data-stu-id="8624e-461">Method invocation</span></span> | `F(x,y)`       | <span data-ttu-id="8624e-462">Risoluzione dell'overload viene applicata per selezionare il metodo migliore `F` nella classe o struct che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="8624e-462">Overload resolution is applied to select the best method `F` in the containing class or struct.</span></span> <span data-ttu-id="8624e-463">Il metodo viene richiamato con l'elenco di argomenti `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-463">The method is invoked with the argument list `(x,y)`.</span></span> <span data-ttu-id="8624e-464">Se il metodo non è `static`, l'espressione dell'istanza è `this`.</span><span class="sxs-lookup"><span data-stu-id="8624e-464">If the method is not `static`, the instance expression is `this`.</span></span> | 
|                   | `T.F(x,y)`     | <span data-ttu-id="8624e-465">Risoluzione dell'overload viene applicata per selezionare il metodo migliore `F` nella classe o struct `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-465">Overload resolution is applied to select the best method `F` in the class or struct `T`.</span></span> <span data-ttu-id="8624e-466">Se il metodo non è generato un errore in fase di associazione `static`.</span><span class="sxs-lookup"><span data-stu-id="8624e-466">A binding-time error occurs if the method is not `static`.</span></span> <span data-ttu-id="8624e-467">Il metodo viene richiamato con l'elenco di argomenti `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-467">The method is invoked with the argument list `(x,y)`.</span></span> | 
|                   | `e.F(x,y)`     | <span data-ttu-id="8624e-468">Risoluzione dell'overload viene applicata per selezionare il metodo migliore F nella classe, struct o interfaccia fornita dal tipo di `e`.</span><span class="sxs-lookup"><span data-stu-id="8624e-468">Overload resolution is applied to select the best method F in the class, struct, or interface given by the type of `e`.</span></span> <span data-ttu-id="8624e-469">Se il metodo viene generato un errore in fase di associazione `static`.</span><span class="sxs-lookup"><span data-stu-id="8624e-469">A binding-time error occurs if the method is `static`.</span></span> <span data-ttu-id="8624e-470">Il metodo viene richiamato con l'espressione dell'istanza `e` l'elenco di argomenti `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-470">The method is invoked with the instance expression `e` and the argument list `(x,y)`.</span></span> | 
| <span data-ttu-id="8624e-471">Accesso a proprietà</span><span class="sxs-lookup"><span data-stu-id="8624e-471">Property access</span></span>   | `P`            | <span data-ttu-id="8624e-472">Il `get` funzione di accesso della proprietà `P` nella classe o struct che lo contiene viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="8624e-472">The `get` accessor of the property `P` in the containing class or struct is invoked.</span></span> <span data-ttu-id="8624e-473">Si verifica un errore in fase di compilazione se `P` è di sola scrittura.</span><span class="sxs-lookup"><span data-stu-id="8624e-473">A compile-time error occurs if `P` is write-only.</span></span> <span data-ttu-id="8624e-474">Se `P` non è `static`, l'espressione dell'istanza è `this`.</span><span class="sxs-lookup"><span data-stu-id="8624e-474">If `P` is not `static`, the instance expression is `this`.</span></span> | 
|                   | `P = value`    | <span data-ttu-id="8624e-475">Il `set` funzione di accesso della proprietà `P` nella classe o struct che lo contiene viene richiamato con l'elenco di argomenti `(value)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-475">The `set` accessor of the property `P` in the containing class or struct is invoked with the argument list `(value)`.</span></span> <span data-ttu-id="8624e-476">Si verifica un errore in fase di compilazione se `P` è di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="8624e-476">A compile-time error occurs if `P` is read-only.</span></span> <span data-ttu-id="8624e-477">Se `P` non è `static`, l'espressione dell'istanza è `this`.</span><span class="sxs-lookup"><span data-stu-id="8624e-477">If `P` is not `static`, the instance expression is `this`.</span></span> | 
|                   | `T.P`          | <span data-ttu-id="8624e-478">Il `get` funzione di accesso della proprietà `P` nella classe o struct `T` viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="8624e-478">The `get` accessor of the property `P` in the class or struct `T` is invoked.</span></span> <span data-ttu-id="8624e-479">Si verifica un errore in fase di compilazione se `P` non è `static` oppure se `P` è di sola scrittura.</span><span class="sxs-lookup"><span data-stu-id="8624e-479">A compile-time error occurs if `P` is not `static` or if `P` is write-only.</span></span> | 
|                   | `T.P = value`  | <span data-ttu-id="8624e-480">Il `set` funzione di accesso della proprietà `P` nella classe o struct `T` viene richiamato con l'elenco di argomenti `(value)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-480">The `set` accessor of the property `P` in the class or struct `T` is invoked with the argument list `(value)`.</span></span> <span data-ttu-id="8624e-481">Si verifica un errore in fase di compilazione se `P` non è `static` oppure se `P` è di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="8624e-481">A compile-time error occurs if `P` is not `static` or if `P` is read-only.</span></span> | 
|                   | `e.P`          | <span data-ttu-id="8624e-482">Il `get` funzione di accesso della proprietà `P` nella classe, struct o interfaccia fornita dal tipo di `e` viene richiamato con l'espressione dell'istanza `e`.</span><span class="sxs-lookup"><span data-stu-id="8624e-482">The `get` accessor of the property `P` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e`.</span></span> <span data-ttu-id="8624e-483">Si verifica un errore in fase di associazione se `P` viene `static` oppure se `P` è di sola scrittura.</span><span class="sxs-lookup"><span data-stu-id="8624e-483">A binding-time error occurs if `P` is `static` or if `P` is write-only.</span></span> | 
|                   | `e.P = value`  | <span data-ttu-id="8624e-484">Il `set` funzione di accesso della proprietà `P` nella classe, struct o interfaccia fornita dal tipo di `e` viene richiamato con l'espressione dell'istanza `e` l'elenco di argomenti `(value)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-484">The `set` accessor of the property `P` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e` and the argument list `(value)`.</span></span> <span data-ttu-id="8624e-485">Si verifica un errore in fase di associazione se `P` viene `static` oppure se `P` è di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="8624e-485">A binding-time error occurs if `P` is `static` or if `P` is read-only.</span></span> | 
| <span data-ttu-id="8624e-486">Accesso agli eventi</span><span class="sxs-lookup"><span data-stu-id="8624e-486">Event access</span></span>      | `E += value`   | <span data-ttu-id="8624e-487">Il `add` funzione di accesso dell'evento `E` nella classe o struct che lo contiene viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="8624e-487">The `add` accessor of the event `E` in the containing class or struct is invoked.</span></span> <span data-ttu-id="8624e-488">Se `E` è l'espressione di istanza non statico, è `this`.</span><span class="sxs-lookup"><span data-stu-id="8624e-488">If `E` is not static, the instance expression is `this`.</span></span> | 
|                   | `E -= value`   | <span data-ttu-id="8624e-489">Il `remove` funzione di accesso dell'evento `E` nella classe o struct che lo contiene viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="8624e-489">The `remove` accessor of the event `E` in the containing class or struct is invoked.</span></span> <span data-ttu-id="8624e-490">Se `E` è l'espressione di istanza non statico, è `this`.</span><span class="sxs-lookup"><span data-stu-id="8624e-490">If `E` is not static, the instance expression is `this`.</span></span> | 
|                   | `T.E += value` | <span data-ttu-id="8624e-491">Il `add` funzione di accesso dell'evento `E` nella classe o struct `T` viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="8624e-491">The `add` accessor of the event `E` in the class or struct `T` is invoked.</span></span> <span data-ttu-id="8624e-492">Si verifica un errore in fase di associazione se `E` non è statico.</span><span class="sxs-lookup"><span data-stu-id="8624e-492">A binding-time error occurs if `E` is not static.</span></span> | 
|                   | `T.E -= value` | <span data-ttu-id="8624e-493">Il `remove` funzione di accesso dell'evento `E` nella classe o struct `T` viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="8624e-493">The `remove` accessor of the event `E` in the class or struct `T` is invoked.</span></span> <span data-ttu-id="8624e-494">Si verifica un errore in fase di associazione se `E` non è statico.</span><span class="sxs-lookup"><span data-stu-id="8624e-494">A binding-time error occurs if `E` is not static.</span></span> | 
|                   | `e.E += value` | <span data-ttu-id="8624e-495">Il `add` funzione di accesso dell'evento `E` nella classe, struct o interfaccia fornita dal tipo di `e` viene richiamato con l'espressione dell'istanza `e`.</span><span class="sxs-lookup"><span data-stu-id="8624e-495">The `add` accessor of the event `E` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e`.</span></span> <span data-ttu-id="8624e-496">Si verifica un errore in fase di associazione se `E` è statico.</span><span class="sxs-lookup"><span data-stu-id="8624e-496">A binding-time error occurs if `E` is static.</span></span> | 
|                   | `e.E -= value` | <span data-ttu-id="8624e-497">Il `remove` funzione di accesso dell'evento `E` nella classe, struct o interfaccia fornita dal tipo di `e` viene richiamato con l'espressione dell'istanza `e`.</span><span class="sxs-lookup"><span data-stu-id="8624e-497">The `remove` accessor of the event `E` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e`.</span></span> <span data-ttu-id="8624e-498">Si verifica un errore in fase di associazione se `E` è statico.</span><span class="sxs-lookup"><span data-stu-id="8624e-498">A binding-time error occurs if `E` is static.</span></span> | 
| <span data-ttu-id="8624e-499">Accesso all'indicizzatore</span><span class="sxs-lookup"><span data-stu-id="8624e-499">Indexer access</span></span>    | `e[x,y]`       | <span data-ttu-id="8624e-500">Risoluzione dell'overload viene applicata per selezionare l'indicizzatore migliore nella classe, struct o interfaccia fornita dal tipo di e.</span><span class="sxs-lookup"><span data-stu-id="8624e-500">Overload resolution is applied to select the best indexer in the class, struct, or interface given by the type of e.</span></span> <span data-ttu-id="8624e-501">Il `get` funzione di accesso dell'indicizzatore viene richiamato con l'espressione dell'istanza `e` l'elenco di argomenti `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-501">The `get` accessor of the indexer is invoked with the instance expression `e` and the argument list `(x,y)`.</span></span> <span data-ttu-id="8624e-502">Se l'indicizzatore è in sola lettura, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-502">A binding-time error occurs if the indexer is write-only.</span></span> | 
|                   | `e[x,y] = value` | <span data-ttu-id="8624e-503">Risoluzione dell'overload viene applicata per selezionare l'indicizzatore migliore di classe, struct o interfaccia fornita dal tipo di `e`.</span><span class="sxs-lookup"><span data-stu-id="8624e-503">Overload resolution is applied to select the best indexer in the class, struct, or interface given by the type of `e`.</span></span> <span data-ttu-id="8624e-504">Il `set` funzione di accesso dell'indicizzatore viene richiamato con l'espressione dell'istanza `e` l'elenco di argomenti `(x,y,value)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-504">The `set` accessor of the indexer is invoked with the instance expression `e` and the argument list `(x,y,value)`.</span></span> <span data-ttu-id="8624e-505">Se l'indicizzatore è di sola lettura, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-505">A binding-time error occurs if the indexer is read-only.</span></span> | 
| <span data-ttu-id="8624e-506">Chiamata dell'operatore</span><span class="sxs-lookup"><span data-stu-id="8624e-506">Operator invocation</span></span> | `-x`         | <span data-ttu-id="8624e-507">Risoluzione dell'overload viene applicata per selezionare l'operatore unario ottimo nella classe o struttura fornita dal tipo di `x`.</span><span class="sxs-lookup"><span data-stu-id="8624e-507">Overload resolution is applied to select the best unary operator in the class or struct given by the type of `x`.</span></span> <span data-ttu-id="8624e-508">L'operatore selezionato viene richiamato con l'elenco di argomenti `(x)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-508">The selected operator is invoked with the argument list `(x)`.</span></span> | 
|                     | `x + y`      | <span data-ttu-id="8624e-509">Risoluzione dell'overload viene applicata per selezionare l'operatore binario meglio nelle classi o struct di base per i tipi di `x` e `y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-509">Overload resolution is applied to select the best binary operator in the classes or structs given by the types of `x` and `y`.</span></span> <span data-ttu-id="8624e-510">L'operatore selezionato viene richiamato con l'elenco di argomenti `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-510">The selected operator is invoked with the argument list `(x,y)`.</span></span> | 
| <span data-ttu-id="8624e-511">Chiamata al costruttore di istanza</span><span class="sxs-lookup"><span data-stu-id="8624e-511">Instance constructor invocation</span></span> | `new T(x,y)` | <span data-ttu-id="8624e-512">Risoluzione dell'overload viene applicata per selezionare il costruttore di istanza migliore nella classe o struct `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-512">Overload resolution is applied to select the best instance constructor in the class or struct `T`.</span></span> <span data-ttu-id="8624e-513">Viene chiamato il costruttore di istanza con l'elenco di argomenti `(x,y)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-513">The instance constructor is invoked with the argument list `(x,y)`.</span></span> | 

### <a name="argument-lists"></a><span data-ttu-id="8624e-514">Elenchi di argomenti</span><span class="sxs-lookup"><span data-stu-id="8624e-514">Argument lists</span></span>

<span data-ttu-id="8624e-515">Ogni chiamata alla funzione membro e il delegato include un elenco di argomenti che fornisce i valori effettivi o riferimenti a variabili per i parametri del membro funzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-515">Every function member and delegate invocation includes an argument list which provides actual values or variable references for the parameters of the function member.</span></span> <span data-ttu-id="8624e-516">La sintassi per specificare l'elenco di argomenti di una chiamata alla funzione membro dipende dalla funzione membro categoria:</span><span class="sxs-lookup"><span data-stu-id="8624e-516">The syntax for specifying the argument list of a function member invocation depends on the function member category:</span></span>

*  <span data-ttu-id="8624e-517">Ad esempio costruttori, metodi, indicizzatori e delegati, gli argomenti sono specificati come un *argument_list*, come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="8624e-517">For instance constructors, methods, indexers and delegates, the arguments are specified as an *argument_list*, as described below.</span></span> <span data-ttu-id="8624e-518">Per gli indicizzatori, quando si richiama il `set` della funzione di accesso, l'elenco di argomenti include inoltre l'espressione specificata come operando destro dell'operatore di assegnazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-518">For indexers, when invoking the `set` accessor, the argument list additionally includes the expression specified as the right operand of the assignment operator.</span></span>
*  <span data-ttu-id="8624e-519">Per le proprietà, l'elenco di argomenti è vuoto quando si richiama il `get` funzione di accesso ed è costituito l'espressione specificata come operando destro dell'operatore di assegnazione quando si richiama il `set` della funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="8624e-519">For properties, the argument list is empty when invoking the `get` accessor, and consists of the expression specified as the right operand of the assignment operator when invoking the `set` accessor.</span></span>
*  <span data-ttu-id="8624e-520">Per gli eventi, l'elenco di argomenti è costituita dall'espressione specificata come operando destro del `+=` o `-=` operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-520">For events, the argument list consists of the expression specified as the right operand of the `+=` or `-=` operator.</span></span>
*  <span data-ttu-id="8624e-521">Per gli operatori definiti dall'utente, l'elenco di argomenti è costituito da due operandi dell'operatore binario o il singolo operando dell'operatore unario.</span><span class="sxs-lookup"><span data-stu-id="8624e-521">For user-defined operators, the argument list consists of the single operand of the unary operator or the two operands of the binary operator.</span></span>

<span data-ttu-id="8624e-522">Gli argomenti delle proprietà ([proprietà](classes.md#properties)), gli eventi ([eventi](classes.md#events)) e operatori definiti dall'utente ([operatori](classes.md#operators)) vengono sempre passati come parametri di valore ([ Parametri del valore](classes.md#value-parameters)).</span><span class="sxs-lookup"><span data-stu-id="8624e-522">The arguments of properties ([Properties](classes.md#properties)), events ([Events](classes.md#events)), and user-defined operators ([Operators](classes.md#operators)) are always passed as value parameters ([Value parameters](classes.md#value-parameters)).</span></span> <span data-ttu-id="8624e-523">Gli argomenti degli indicizzatori ([indicizzatori](classes.md#indexers)) vengono sempre passati come parametri di valore ([parametri del valore](classes.md#value-parameters)) o le matrici di parametri ([matrici di parametri](classes.md#parameter-arrays)).</span><span class="sxs-lookup"><span data-stu-id="8624e-523">The arguments of indexers ([Indexers](classes.md#indexers)) are always passed as value parameters ([Value parameters](classes.md#value-parameters)) or parameter arrays ([Parameter arrays](classes.md#parameter-arrays)).</span></span> <span data-ttu-id="8624e-524">Parametri di riferimento e di output non sono supportati per queste categorie di membri di funzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-524">Reference and output parameters are not supported for these categories of function members.</span></span>

<span data-ttu-id="8624e-525">Gli argomenti di una chiamata al costruttore, metodo, indicizzatore o delegato di istanza vengono specificati come un *argument_list*:</span><span class="sxs-lookup"><span data-stu-id="8624e-525">The arguments of an instance constructor, method, indexer or delegate invocation are specified as an *argument_list*:</span></span>

```antlr
argument_list
    : argument (',' argument)*
    ;

argument
    : argument_name? argument_value
    ;

argument_name
    : identifier ':'
    ;

argument_value
    : expression
    | 'ref' variable_reference
    | 'out' variable_reference
    ;
```

<span data-ttu-id="8624e-526">Un' *argument_list* costituito da uno o più *argomento*s, separati da virgole.</span><span class="sxs-lookup"><span data-stu-id="8624e-526">An *argument_list* consists of one or more *argument*s, separated by commas.</span></span> <span data-ttu-id="8624e-527">Ogni argomento è costituito da facoltativo *nome_argomento* seguita da un *argument_value*.</span><span class="sxs-lookup"><span data-stu-id="8624e-527">Each argument consists of an optional  *argument_name* followed by an *argument_value*.</span></span> <span data-ttu-id="8624e-528">Un' *argomento* con un *nome_argomento* viene definito un ***argomento denominato***, mentre un *argomento* senza un  *nome_argomento* è un ***argomento posizionale***.</span><span class="sxs-lookup"><span data-stu-id="8624e-528">An *argument* with an *argument_name* is referred to as a ***named argument***, whereas an *argument* without an *argument_name* is a ***positional argument***.</span></span> <span data-ttu-id="8624e-529">È corretto per un argomento posizionale visualizzate dopo un argomento denominato in un *argument_list*.</span><span class="sxs-lookup"><span data-stu-id="8624e-529">It is an error for a positional argument to appear after a named argument in an *argument_list*.</span></span>

<span data-ttu-id="8624e-530">Il *argument_value* può assumere uno dei formati seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-530">The *argument_value* can take one of the following forms:</span></span>

*  <span data-ttu-id="8624e-531">Un' *espressione*, che indica che l'argomento viene passato come parametro di valore ([parametri del valore](classes.md#value-parameters)).</span><span class="sxs-lookup"><span data-stu-id="8624e-531">An *expression*, indicating that the argument is passed as a value parameter ([Value parameters](classes.md#value-parameters)).</span></span>
*  <span data-ttu-id="8624e-532">La parola chiave `ref` seguita da un *variable_reference* ([riferimenti a variabili](variables.md#variable-references)), che indica che l'argomento viene passato come parametro di riferimento ([fare riferimento ai parametri ](classes.md#reference-parameters)).</span><span class="sxs-lookup"><span data-stu-id="8624e-532">The keyword `ref` followed by a *variable_reference* ([Variable references](variables.md#variable-references)), indicating that the argument is passed as a reference parameter ([Reference parameters](classes.md#reference-parameters)).</span></span> <span data-ttu-id="8624e-533">Una variabile deve essere assegnata definitivamente ([assegnazione certa](variables.md#definite-assignment)) prima che può essere passata come parametro di riferimento.</span><span class="sxs-lookup"><span data-stu-id="8624e-533">A variable must be definitely assigned ([Definite assignment](variables.md#definite-assignment)) before it can be passed as a reference parameter.</span></span> <span data-ttu-id="8624e-534">La parola chiave `out` seguita da un *variable_reference* ([riferimenti a variabili](variables.md#variable-references)), che indica che l'argomento viene passato come parametro di output ([iparametridiOutput](classes.md#output-parameters)).</span><span class="sxs-lookup"><span data-stu-id="8624e-534">The keyword `out` followed by a *variable_reference* ([Variable references](variables.md#variable-references)), indicating that the argument is passed as an output parameter ([Output parameters](classes.md#output-parameters)).</span></span> <span data-ttu-id="8624e-535">Viene considerata come una variabile assegnata ([assegnazione certa](variables.md#definite-assignment)) dopo una chiamata alla funzione membro in cui la variabile viene passata come parametro di output.</span><span class="sxs-lookup"><span data-stu-id="8624e-535">A variable is considered definitely assigned ([Definite assignment](variables.md#definite-assignment)) following a function member invocation in which the variable is passed as an output parameter.</span></span>

#### <a name="corresponding-parameters"></a><span data-ttu-id="8624e-536">Parametri corrispondenti</span><span class="sxs-lookup"><span data-stu-id="8624e-536">Corresponding parameters</span></span>

<span data-ttu-id="8624e-537">Per ogni argomento nell'elenco di argomenti deve essere un parametro corrispondente nel membro di funzione o delegato richiamato.</span><span class="sxs-lookup"><span data-stu-id="8624e-537">For each argument in an argument list there has to be a corresponding parameter in the function member or delegate being invoked.</span></span>

<span data-ttu-id="8624e-538">Elenco di parametri usato di seguito viene determinata come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-538">The parameter list used in the following is determined as follows:</span></span>

*  <span data-ttu-id="8624e-539">Per i metodi virtuali e gli indicizzatori definiti nelle classi, l'elenco dei parametri viene prelevato dalla dichiarazione di più specifica o eseguire l'override del membro (funzione), iniziando con il tipo statico di ricevitore e la ricerca tramite le relative classi base.</span><span class="sxs-lookup"><span data-stu-id="8624e-539">For virtual methods and indexers defined in classes, the parameter list is picked from the most specific declaration or override of the function member, starting with the static type of the receiver, and searching through its base classes.</span></span>
*  <span data-ttu-id="8624e-540">Per i metodi di interfaccia e gli indicizzatori, viene selezionato l'elenco di parametri costituiscono la definizione del membro, iniziando con il tipo di interfaccia e la ricerca tramite le interfacce di base più specifica.</span><span class="sxs-lookup"><span data-stu-id="8624e-540">For interface methods and indexers, the parameter list is picked form the most specific definition of the member, starting with the interface type and searching through the base interfaces.</span></span> <span data-ttu-id="8624e-541">Se non viene trovato alcun elenco di parametri univoci, viene costruito un elenco di parametri con nomi inaccessibili e nessun parametro facoltativo, in modo che le chiamate non è possibile usare i parametri denominati o omettere gli argomenti facoltativi.</span><span class="sxs-lookup"><span data-stu-id="8624e-541">If no unique parameter list is found, a parameter list with inaccessible names and no optional parameters is constructed, so that invocations cannot use named parameters or omit optional arguments.</span></span>
*  <span data-ttu-id="8624e-542">Per i metodi parziali, viene usato l'elenco di parametri della dichiarazione di metodo parziale di definizione.</span><span class="sxs-lookup"><span data-stu-id="8624e-542">For partial methods, the parameter list of the defining partial method declaration is used.</span></span>
*  <span data-ttu-id="8624e-543">Per tutti gli altri membri di funzione e delegati è disponibile solo un elenco, l'unico parametro che corrisponde a quella usata.</span><span class="sxs-lookup"><span data-stu-id="8624e-543">For all other function members and delegates there is only a single parameter list, which is the one used.</span></span>

<span data-ttu-id="8624e-544">La posizione di un argomento o un parametro viene definita come numero di argomenti o parametri precede nell'elenco di argomento o elenco di parametri.</span><span class="sxs-lookup"><span data-stu-id="8624e-544">The position of an argument or parameter is defined as the number of arguments or parameters preceding it in the argument list or parameter list.</span></span>

<span data-ttu-id="8624e-545">I parametri corrispondenti per gli argomenti della funzione membro vengono stabiliti nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="8624e-545">The corresponding parameters for function member arguments are established as follows:</span></span>

*  <span data-ttu-id="8624e-546">Gli argomenti in di *argument_list* di costruttori di istanza, metodi, indicizzatori e delegati:</span><span class="sxs-lookup"><span data-stu-id="8624e-546">Arguments in the *argument_list* of instance constructors, methods, indexers and delegates:</span></span>
    * <span data-ttu-id="8624e-547">Un argomento posizionale in cui è presente un parametro predefinito nella stessa posizione nell'elenco di parametri corrisponde al parametro.</span><span class="sxs-lookup"><span data-stu-id="8624e-547">A positional argument where a fixed parameter occurs at the same position in the parameter list corresponds to that parameter.</span></span>
    * <span data-ttu-id="8624e-548">Un argomento posizionale di un membro di funzione con una matrice di parametri richiamato nella sua forma normale corrisponde alla matrice del parametro, che deve essere utilizzati nella stessa posizione nell'elenco dei parametri.</span><span class="sxs-lookup"><span data-stu-id="8624e-548">A positional argument of a function member with a parameter array invoked in its normal form corresponds to the parameter  array, which must occur at the same position in the parameter list.</span></span>
    * <span data-ttu-id="8624e-549">Un argomento posizionale di un membro di funzione con una matrice di parametri, richiamato in forma espansa, in cui è presente alcun parametro fisso nella stessa posizione nell'elenco dei parametri, corrisponde a un elemento della matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="8624e-549">A positional argument of a function member with a parameter array invoked in its expanded form, where no fixed parameter occurs at the same position in the parameter list, corresponds to an element in the parameter array.</span></span>
    * <span data-ttu-id="8624e-550">Un argomento denominato corrispondente al parametro lo stesso nome nell'elenco dei parametri.</span><span class="sxs-lookup"><span data-stu-id="8624e-550">A named argument corresponds to the parameter of the same name in the parameter list.</span></span>
    * <span data-ttu-id="8624e-551">Per gli indicizzatori, quando si richiama il `set` funzione di accesso, l'espressione specificata come operando destro dell'operatore di assegnazione corrisponde a implicita `value` parametro del `set` dichiarazione di funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="8624e-551">For indexers, when invoking the `set` accessor, the expression specified as the right operand of the assignment operator corresponds to the implicit `value` parameter of the `set` accessor declaration.</span></span>
*  <span data-ttu-id="8624e-552">Per le proprietà, quando si richiama il `get` funzione di accesso sono presenti argomenti.</span><span class="sxs-lookup"><span data-stu-id="8624e-552">For properties, when invoking the `get` accessor there are no arguments.</span></span> <span data-ttu-id="8624e-553">Quando si richiama il `set` funzione di accesso, l'espressione specificata come operando destro dell'operatore di assegnazione corrisponde a implicita `value` parametro del `set` dichiarazione di funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="8624e-553">When invoking the `set` accessor, the expression specified as the right operand of the assignment operator corresponds to the implicit `value` parameter of the `set` accessor declaration.</span></span>
*  <span data-ttu-id="8624e-554">Per gli operatori unari definito dall'utente (comprese le conversioni), l'unico operando corrisponde al singolo parametro della dichiarazione dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-554">For user-defined unary operators (including conversions), the single operand corresponds to the single parameter of the operator declaration.</span></span>
*  <span data-ttu-id="8624e-555">Per gli operatori binari definiti dall'utente, l'operando sinistro corrisponde al primo parametro e operando destro corrisponde al secondo parametro della dichiarazione dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-555">For user-defined binary operators, the left operand corresponds to the first parameter, and the right operand corresponds to the second parameter of the operator declaration.</span></span>

#### <a name="run-time-evaluation-of-argument-lists"></a><span data-ttu-id="8624e-556">Valutazione in fase di esecuzione degli elenchi di argomenti</span><span class="sxs-lookup"><span data-stu-id="8624e-556">Run-time evaluation of argument lists</span></span>

<span data-ttu-id="8624e-557">Durante l'elaborazione in fase di esecuzione di una chiamata alla funzione membro ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), le espressioni o i riferimenti a variabili di un elenco di argomenti vengono valutati in ordine, da sinistra a destra, come di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-557">During the run-time processing of a function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), the expressions or variable references of an argument list are evaluated in order, from left to right, as follows:</span></span>

*  <span data-ttu-id="8624e-558">Per un parametro di valore, viene valutata l'espressione dell'argomento e una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) al parametro corrispondente tipo viene eseguita.</span><span class="sxs-lookup"><span data-stu-id="8624e-558">For a value parameter, the argument expression is evaluated and an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) to the corresponding parameter type is performed.</span></span> <span data-ttu-id="8624e-559">Il valore risultante diventa il valore iniziale del parametro del valore nella chiamata del membro funzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-559">The resulting value becomes the initial value of the value parameter in the function member invocation.</span></span>
*  <span data-ttu-id="8624e-560">Per un parametro di riferimento o di output, il riferimento alla variabile viene valutato e il percorso di archiviazione risultante diventa il percorso di archiviazione rappresentato dal parametro nella chiamata del membro funzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-560">For a reference or output parameter, the variable reference is evaluated and the resulting storage location becomes the storage location represented by the parameter in the function member invocation.</span></span> <span data-ttu-id="8624e-561">Se il riferimento alla variabile fornito come parametro di riferimento o di output è un elemento di un *reference_type*, viene eseguito un controllo in fase di esecuzione per garantire che il tipo di elemento della matrice è identico al tipo del parametro.</span><span class="sxs-lookup"><span data-stu-id="8624e-561">If the variable reference given as a reference or output parameter is an array element of a *reference_type*, a run-time check is performed to ensure that the element type of the array is identical to the type of the parameter.</span></span> <span data-ttu-id="8624e-562">Se questo controllo ha esito negativo, un `System.ArrayTypeMismatchException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-562">If this check fails, a `System.ArrayTypeMismatchException` is thrown.</span></span>

<span data-ttu-id="8624e-563">I metodi, indicizzatori e costruttori di istanza possono dichiarare il parametro all'estrema destra sia una matrice di parametri ([matrici di parametri](classes.md#parameter-arrays)).</span><span class="sxs-lookup"><span data-stu-id="8624e-563">Methods, indexers, and instance constructors may declare their right-most parameter to be a parameter array ([Parameter arrays](classes.md#parameter-arrays)).</span></span> <span data-ttu-id="8624e-564">Questi membri di funzione vengono richiamati in forma normale o in forma espansa in base alla scelta applicabile ([membro di funzione applicabile](expressions.md#applicable-function-member)):</span><span class="sxs-lookup"><span data-stu-id="8624e-564">Such function members are invoked either in their normal form or in their expanded form depending on which is applicable ([Applicable function member](expressions.md#applicable-function-member)):</span></span>

*  <span data-ttu-id="8624e-565">Quando viene richiamato un membro di funzione con una matrice di parametri nella sua forma normale, l'argomento specificato per la matrice di parametri deve essere una singola espressione che è convertibile in modo implicito ([conversioni implicite](conversions.md#implicit-conversions)) per il tipo di matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="8624e-565">When a function member with a parameter array is invoked in its normal form, the argument given for the parameter array must be a single expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the parameter array type.</span></span> <span data-ttu-id="8624e-566">In questo caso, la matrice di parametri funziona esattamente come un parametro di valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-566">In this case, the parameter array acts precisely like a value parameter.</span></span>
*  <span data-ttu-id="8624e-567">Quando viene richiamato un membro di funzione con una matrice di parametri in forma espansa, la chiamata è necessario specificare zero o più argomenti posizionali per la matrice di parametri, dove ogni argomento è un'espressione che è convertibile in modo implicito ([implicite le conversioni](conversions.md#implicit-conversions)) al tipo di elemento della matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="8624e-567">When a function member with a parameter array is invoked in its expanded form, the invocation must specify zero or more positional arguments for the parameter array, where each argument is an expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the element type of the parameter array.</span></span> <span data-ttu-id="8624e-568">In questo caso, la chiamata crea un'istanza del tipo di parametro matrice con una lunghezza corrispondente al numero di argomenti, inizializza gli elementi dell'istanza di matrice con i valori dell'argomento specificato e Usa l'istanza di matrice appena creata come l'effettivo argomento.</span><span class="sxs-lookup"><span data-stu-id="8624e-568">In this case, the invocation creates an instance of the parameter array type with a length corresponding to the number of arguments, initializes the elements of the array instance with the given argument values, and uses the newly created array instance as the actual argument.</span></span>

<span data-ttu-id="8624e-569">Le espressioni dell'elenco di argomenti vengono sempre valutate nell'ordine in cui sono scritti.</span><span class="sxs-lookup"><span data-stu-id="8624e-569">The expressions of an argument list are always evaluated in the order they are written.</span></span> <span data-ttu-id="8624e-570">Di conseguenza, l'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-570">Thus, the example</span></span>
```csharp
class Test
{
    static void F(int x, int y = -1, int z = -2) {
        System.Console.WriteLine("x = {0}, y = {1}, z = {2}", x, y, z);
    }

    static void Main() {
        int i = 0;
        F(i++, i++, i++);
        F(z: i++, x: i++);
    }
}
```
<span data-ttu-id="8624e-571">produce l'output</span><span class="sxs-lookup"><span data-stu-id="8624e-571">produces the output</span></span>
```
x = 0, y = 1, z = 2
x = 4, y = -1, z = 3
```

<span data-ttu-id="8624e-572">Le regole di CO-varianza matrice ([vovariante](arrays.md#array-covariance)) consentono di un valore di tipo matrice `A[]` come un riferimento a un'istanza di un tipo di matrice `B[]`, a condizione che esiste una conversione implicita del riferimento da `B` per `A`.</span><span class="sxs-lookup"><span data-stu-id="8624e-572">The array co-variance rules ([Array covariance](arrays.md#array-covariance)) permit a value of an array type `A[]` to be a reference to an instance of an array type `B[]`, provided an implicit reference conversion exists from `B` to `A`.</span></span> <span data-ttu-id="8624e-573">A causa di queste regole, quando un elemento di un *reference_type* viene passato come parametro un riferimento o di output, è necessario per garantire che il tipo effettivo degli elementi della matrice è identico a quella del parametro di un controllo in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-573">Because of these rules, when an array element of a *reference_type* is passed as a reference or output parameter, a run-time check is required to ensure that the actual element type of the array is identical to that of the parameter.</span></span> <span data-ttu-id="8624e-574">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-574">In the example</span></span>
```csharp
class Test
{
    static void F(ref object x) {...}

    static void Main() {
        object[] a = new object[10];
        object[] b = new string[10];
        F(ref a[0]);        // Ok
        F(ref b[1]);        // ArrayTypeMismatchException
    }
}
```
<span data-ttu-id="8624e-575">la seconda chiamata di `F` fa sì che un `System.ArrayTypeMismatchException` eccezione perché il tipo dell'elemento effettivo dei `b` viene `string` e non `object`.</span><span class="sxs-lookup"><span data-stu-id="8624e-575">the second invocation of `F` causes a `System.ArrayTypeMismatchException` to be thrown because the actual element type of `b` is `string` and not `object`.</span></span>

<span data-ttu-id="8624e-576">Quando viene richiamato un membro di funzione con una matrice di parametri in forma espansa, la chiamata viene elaborata esattamente come se un'espressione di creazione della matrice con un inizializzatore di matrice ([espressioni di creazione matrice](expressions.md#array-creation-expressions)) è stato inserito tutto il parametri espansi.</span><span class="sxs-lookup"><span data-stu-id="8624e-576">When a function member with a parameter array is invoked in its expanded form, the invocation is processed exactly as if an array creation expression with an array initializer ([Array creation expressions](expressions.md#array-creation-expressions)) was inserted around the expanded parameters.</span></span> <span data-ttu-id="8624e-577">Ad esempio, considerando la dichiarazione</span><span class="sxs-lookup"><span data-stu-id="8624e-577">For example, given the declaration</span></span>
```csharp
void F(int x, int y, params object[] args);
```
<span data-ttu-id="8624e-578">le chiamate seguenti nel formato espanso del metodo</span><span class="sxs-lookup"><span data-stu-id="8624e-578">the following invocations of the expanded form of the method</span></span>
```csharp
F(10, 20);
F(10, 20, 30, 40);
F(10, 20, 1, "hello", 3.0);
```
<span data-ttu-id="8624e-579">corrispondono esattamente a</span><span class="sxs-lookup"><span data-stu-id="8624e-579">correspond exactly to</span></span>
```csharp
F(10, 20, new object[] {});
F(10, 20, new object[] {30, 40});
F(10, 20, new object[] {1, "hello", 3.0});
```

<span data-ttu-id="8624e-580">In particolare, si noti che viene creata una matrice vuota quando non sono specificati argomenti per la matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="8624e-580">In particular, note that an empty array is created when there are zero arguments given for the parameter array.</span></span>

<span data-ttu-id="8624e-581">Quando gli argomenti vengono omessi da un membro di funzione con parametri facoltativi corrispondenti, gli argomenti predefiniti della dichiarazione di membro di funzione vengono passati in modo implicito.</span><span class="sxs-lookup"><span data-stu-id="8624e-581">When arguments are omitted from a function member with corresponding optional parameters, the default arguments of the function member declaration are implicitly passed.</span></span> <span data-ttu-id="8624e-582">Poiché questi sono sempre una costanti, la loro valutazione non avrà impatto sull'ordine di valutazione degli argomenti rimanenti.</span><span class="sxs-lookup"><span data-stu-id="8624e-582">Because these are always constant, their evaluation will not impact the evaluation order of the remaining arguments.</span></span>

### <a name="type-inference"></a><span data-ttu-id="8624e-583">Inferenza del tipo</span><span class="sxs-lookup"><span data-stu-id="8624e-583">Type inference</span></span>

<span data-ttu-id="8624e-584">Quando viene chiamato un metodo generico senza specificare gli argomenti di tipo, una ***inferenza del tipo*** processo prova a dedurre argomenti tipo per la chiamata.</span><span class="sxs-lookup"><span data-stu-id="8624e-584">When a generic method is called without specifying type arguments, a ***type inference*** process attempts to infer type arguments for the call.</span></span> <span data-ttu-id="8624e-585">La presenza di inferenza consente una sintassi più pratica da utilizzare per chiamare un metodo generico e consente al programmatore di evitare di specificare le informazioni sul tipo ridondanti.</span><span class="sxs-lookup"><span data-stu-id="8624e-585">The presence of type inference allows a more convenient syntax to be used for calling a generic method, and allows the programmer to avoid specifying redundant type information.</span></span> <span data-ttu-id="8624e-586">Ad esempio, considerando la dichiarazione di metodo:</span><span class="sxs-lookup"><span data-stu-id="8624e-586">For example, given the method declaration:</span></span>
```csharp
class Chooser
{
    static Random rand = new Random();

    public static T Choose<T>(T first, T second) {
        return (rand.Next(2) == 0)? first: second;
    }
}
```
<span data-ttu-id="8624e-587">è possibile richiamare il `Choose` metodo senza specificare in modo esplicito un argomento di tipo:</span><span class="sxs-lookup"><span data-stu-id="8624e-587">it is possible to invoke the `Choose` method without explicitly specifying a type argument:</span></span>
```csharp
int i = Chooser.Choose(5, 213);                 // Calls Choose<int>

string s = Chooser.Choose("foo", "bar");        // Calls Choose<string>
```

<span data-ttu-id="8624e-588">Tramite l'inferenza del tipo, gli argomenti di tipo `int` e `string` vengono determinati in base agli argomenti al metodo.</span><span class="sxs-lookup"><span data-stu-id="8624e-588">Through type inference, the type arguments `int` and `string` are determined from the arguments to the method.</span></span>

<span data-ttu-id="8624e-589">L'inferenza del tipo viene generato come parte dell'elaborazione in fase di associazione di una chiamata al metodo ([chiamate al metodo](expressions.md#method-invocations)) e viene eseguita prima la procedura di risoluzione dell'overload della chiamata.</span><span class="sxs-lookup"><span data-stu-id="8624e-589">Type inference occurs as part of the binding-time processing of a method invocation ([Method invocations](expressions.md#method-invocations)) and takes place before the overload resolution step of the invocation.</span></span> <span data-ttu-id="8624e-590">Quando un gruppo di metodi specifico viene specificato in una chiamata al metodo e nessun argomento di tipo viene specificato come parte della chiamata del metodo, l'inferenza del tipo viene applicato a ogni metodo generico nel gruppo di metodi.</span><span class="sxs-lookup"><span data-stu-id="8624e-590">When a particular method group is specified in a method invocation, and no type arguments are specified as part of the method invocation, type inference is applied to each generic method in the method group.</span></span> <span data-ttu-id="8624e-591">Se l'inferenza del tipo ha esito positivo, vengono utilizzati gli argomenti tipo dedotti per determinare i tipi di argomenti per la risoluzione dell'overload successivi.</span><span class="sxs-lookup"><span data-stu-id="8624e-591">If type inference succeeds, then the inferred type arguments are used to determine the types of arguments for subsequent overload resolution.</span></span> <span data-ttu-id="8624e-592">Se la risoluzione dell'overload viene scelto un metodo generico a quella per il richiamo, gli argomenti tipo dedotti sono usati come argomenti di tipo effettivo per la chiamata.</span><span class="sxs-lookup"><span data-stu-id="8624e-592">If overload resolution chooses a generic method as the one to invoke, then the inferred type arguments are used as the actual type arguments for the invocation.</span></span> <span data-ttu-id="8624e-593">Se l'inferenza del tipo per un particolare metodo ha esito negativo, tale metodo non fa parte della risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="8624e-593">If type inference for a particular method fails, that method does not participate in overload resolution.</span></span> <span data-ttu-id="8624e-594">L'errore di inferenza del tipo e in se stesso, non causa un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-594">The failure of type inference, in and of itself, does not cause a binding-time error.</span></span> <span data-ttu-id="8624e-595">Tuttavia, tale operazione spesso comporta un errore in fase di associazione durante la risoluzione dell'overload non sia stato possibile trovare alcun metodo applicabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-595">However, it often leads to a binding-time error when overload resolution then fails to find any applicable methods.</span></span>

<span data-ttu-id="8624e-596">Se il numero di argomenti fornito è diverso rispetto al numero di parametri del metodo, quindi inferenza ha immediatamente esito negativo.</span><span class="sxs-lookup"><span data-stu-id="8624e-596">If the supplied number of arguments is different than the number of parameters in the method, then inference immediately fails.</span></span> <span data-ttu-id="8624e-597">In caso contrario, si supponga che il metodo generico ha la firma seguente:</span><span class="sxs-lookup"><span data-stu-id="8624e-597">Otherwise, assume that the generic method has the following signature:</span></span>
```csharp
Tr M<X1,...,Xn>(T1 x1, ..., Tm xm)
```

<span data-ttu-id="8624e-598">Con una chiamata al metodo del form `M(E1...Em)` l'attività di inferenza del tipo consiste nell'individuare gli argomenti di tipo univoco `S1...Sn` per ognuno dei parametri di tipo `X1...Xn` in modo che la chiamata `M<S1...Sn>(E1...Em)` diventa valido.</span><span class="sxs-lookup"><span data-stu-id="8624e-598">With a method call of the form `M(E1...Em)` the task of type inference is to find unique type arguments `S1...Sn` for each of the type parameters `X1...Xn` so that the call `M<S1...Sn>(E1...Em)` becomes valid.</span></span>

<span data-ttu-id="8624e-599">Durante il processo di inferenza di ogni parametro di tipo `Xi` può essere *fissa* a un determinato tipo `Si` oppure *unfixed* con un set associato di *limiti*.</span><span class="sxs-lookup"><span data-stu-id="8624e-599">During the process of inference each type parameter `Xi` is either *fixed* to a particular type `Si` or *unfixed* with an associated set of *bounds*.</span></span> <span data-ttu-id="8624e-600">Ognuno dei limiti è un tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-600">Each of the bounds is some type `T`.</span></span> <span data-ttu-id="8624e-601">Inizialmente ogni variabile di tipo `Xi` è unfixed con un set vuoto di limiti.</span><span class="sxs-lookup"><span data-stu-id="8624e-601">Initially each type variable `Xi` is unfixed with an empty set of bounds.</span></span>

<span data-ttu-id="8624e-602">L'inferenza del tipo viene eseguita in fasi.</span><span class="sxs-lookup"><span data-stu-id="8624e-602">Type inference takes place in phases.</span></span> <span data-ttu-id="8624e-603">Ogni fase proverà a dedurre argomenti tipo per basarsi sui risultati della fase precedente più variabili di tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-603">Each phase will try to infer type arguments for more type variables based on the findings of the previous phase.</span></span> <span data-ttu-id="8624e-604">La prima fase prevede alcune inferenze iniziali dei limiti, mentre la seconda fase corregge le variabili di tipo a tipi specifici e deduce ulteriormente i limiti.</span><span class="sxs-lookup"><span data-stu-id="8624e-604">The first phase makes some initial inferences of bounds, whereas the second phase fixes type variables to specific types and infers further bounds.</span></span> <span data-ttu-id="8624e-605">La seconda fase potrebbe dover essere ripetuto un numero di volte.</span><span class="sxs-lookup"><span data-stu-id="8624e-605">The second phase may have to be repeated a number of times.</span></span>

<span data-ttu-id="8624e-606">*Nota:* Tipo inferenza ha luogo non solo quando viene chiamato un metodo generico.</span><span class="sxs-lookup"><span data-stu-id="8624e-606">*Note:* Type inference takes place not only when a generic method is called.</span></span> <span data-ttu-id="8624e-607">L'inferenza del tipo per la conversione dei gruppi di metodi è descritto nella [inferenza dei tipi per la conversione dei gruppi di metodi](expressions.md#type-inference-for-conversion-of-method-groups) e trovare il tipo comune di un set di espressioni è descritta in [trovare il tipo migliore di un set comune espressioni](expressions.md#finding-the-best-common-type-of-a-set-of-expressions).</span><span class="sxs-lookup"><span data-stu-id="8624e-607">Type inference for conversion of method groups is described in [Type inference for conversion of method groups](expressions.md#type-inference-for-conversion-of-method-groups) and finding the best common type of a set of expressions is described in [Finding the best common type of a set of expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions).</span></span>

#### <a name="the-first-phase"></a><span data-ttu-id="8624e-608">La prima fase</span><span class="sxs-lookup"><span data-stu-id="8624e-608">The first phase</span></span>

<span data-ttu-id="8624e-609">Per ognuno degli argomenti del metodo `Ei`:</span><span class="sxs-lookup"><span data-stu-id="8624e-609">For each of the method arguments `Ei`:</span></span>

*   <span data-ttu-id="8624e-610">Se `Ei` è una funzione anonima, un' *l'inferenza del tipo di parametro espliciti* ([inferenza dei tipi parametro esplicito](expressions.md#explicit-parameter-type-inferences)) viene eseguita dal `Ei` a `Ti`</span><span class="sxs-lookup"><span data-stu-id="8624e-610">If `Ei` is an anonymous function, an *explicit parameter type inference* ([Explicit parameter type inferences](expressions.md#explicit-parameter-type-inferences)) is made from `Ei` to `Ti`</span></span>
*   <span data-ttu-id="8624e-611">In caso contrario, se `Ei` dispone di un tipo `U` e `xi` è un parametro un' *inferenza di livello inferiore* viene effettuata *da* `U` *a* `Ti`.</span><span class="sxs-lookup"><span data-stu-id="8624e-611">Otherwise, if `Ei` has a type `U` and `xi` is a value parameter then a *lower-bound inference* is made *from* `U` *to* `Ti`.</span></span>
*   <span data-ttu-id="8624e-612">In caso contrario, se `Ei` dispone di un tipo `U` e `xi` è un `ref` o `out` parametro un' *esatte inferenza* viene effettuata *da* `U` *al* `Ti`.</span><span class="sxs-lookup"><span data-stu-id="8624e-612">Otherwise, if `Ei` has a type `U` and `xi` is a `ref` or `out` parameter then an *exact inference* is made *from* `U` *to* `Ti`.</span></span>
*   <span data-ttu-id="8624e-613">In caso contrario, viene eseguita alcuna inferenza per questo argomento.</span><span class="sxs-lookup"><span data-stu-id="8624e-613">Otherwise, no inference is made for this argument.</span></span>


#### <a name="the-second-phase"></a><span data-ttu-id="8624e-614">La seconda fase</span><span class="sxs-lookup"><span data-stu-id="8624e-614">The second phase</span></span>

<span data-ttu-id="8624e-615">La seconda fase procede nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="8624e-615">The second phase proceeds as follows:</span></span>

*   <span data-ttu-id="8624e-616">Tutti i *unfixed* variabili di tipo `Xi` che non lo fanno *dipendono* ([dipendenza](expressions.md#dependence)) qualsiasi `Xj` saranno stati corretti ([correzione](expressions.md#fixing)).</span><span class="sxs-lookup"><span data-stu-id="8624e-616">All *unfixed* type variables `Xi` which do not *depend on* ([Dependence](expressions.md#dependence)) any `Xj` are fixed ([Fixing](expressions.md#fixing)).</span></span>
*   <span data-ttu-id="8624e-617">Se non esiste alcuna variabile di tipo, tutte le *unfixed* variabili di tipo `Xi` sono *risolto* per cui tutti gli elementi seguenti contengono:</span><span class="sxs-lookup"><span data-stu-id="8624e-617">If no such type variables exist, all *unfixed* type variables `Xi` are *fixed* for which all of the following hold:</span></span>
    *   <span data-ttu-id="8624e-618">Non è presente almeno una variabile di tipo `Xj` che dipende da `Xi`</span><span class="sxs-lookup"><span data-stu-id="8624e-618">There is at least one type variable `Xj` that depends on `Xi`</span></span>
    *   <span data-ttu-id="8624e-619">`Xi` dispone un set non vuoto dei limiti</span><span class="sxs-lookup"><span data-stu-id="8624e-619">`Xi` has a non-empty set of bounds</span></span>
*   <span data-ttu-id="8624e-620">Se non esistono alcuna variabile di tipo e sono ancora presenti *unfixed* digitare variabili, inferenza ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="8624e-620">If no such type variables exist and there are still *unfixed* type variables, type inference fails.</span></span>
*   <span data-ttu-id="8624e-621">In caso contrario, se non ulteriormente *unfixed* variabili di tipo, l'inferenza del tipo ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="8624e-621">Otherwise, if no further *unfixed* type variables exist, type inference succeeds.</span></span>
*   <span data-ttu-id="8624e-622">In caso contrario, per tutti gli argomenti `Ei` con tipo di parametro corrispondente `Ti` in cui le *tipi di output* ([tipi di Output](expressions.md#output-types)) contengono *unfixed* variabili di tipo `Xj` ma la *tipi di input* ([tipi di Input](expressions.md#input-types)), non sono un *l'inferenza del tipo di output* ([Output inferenza dei tipi ](expressions.md#output-type-inferences)) viene effettuata *dalla* `Ei` *alla* `Ti`.</span><span class="sxs-lookup"><span data-stu-id="8624e-622">Otherwise, for all arguments `Ei` with corresponding parameter type `Ti` where the *output types* ([Output types](expressions.md#output-types)) contain *unfixed* type variables `Xj` but the *input types* ([Input types](expressions.md#input-types)) do not, an *output type inference* ([Output type inferences](expressions.md#output-type-inferences)) is made *from* `Ei` *to* `Ti`.</span></span> <span data-ttu-id="8624e-623">Quindi la seconda fase viene ripetuta.</span><span class="sxs-lookup"><span data-stu-id="8624e-623">Then the second phase is repeated.</span></span>

#### <a name="input-types"></a><span data-ttu-id="8624e-624">Tipi di input</span><span class="sxs-lookup"><span data-stu-id="8624e-624">Input types</span></span>

<span data-ttu-id="8624e-625">Se `E` è un gruppo di metodi o tipizzate in modo implicito funzione anonima e `T` è un delegato, tipo o il tipo di albero delle espressioni quindi tutti i tipi dei parametri `T` sono *tipi di input* di `E` *con il tipo* `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-625">If `E` is a method group or implicitly typed anonymous function and `T` is a delegate type or expression tree type then all the parameter types of `T` are *input types* of `E` *with type* `T`.</span></span>

####  <a name="output-types"></a><span data-ttu-id="8624e-626">Tipi di output</span><span class="sxs-lookup"><span data-stu-id="8624e-626">Output types</span></span>

<span data-ttu-id="8624e-627">Se `E` è un gruppo di metodi o una funzione anonima e `T` è un delegato, tipo o il tipo di albero delle espressioni quindi il tipo restituito del `T` è un *tipo dell'output* `E` *con tipo*  `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-627">If `E` is a method group or an anonymous function and `T` is a delegate type or expression tree type then the return type of `T` is an *output type of* `E` *with type* `T`.</span></span>

#### <a name="dependence"></a><span data-ttu-id="8624e-628">Dipendenza da</span><span class="sxs-lookup"><span data-stu-id="8624e-628">Dependence</span></span>

<span data-ttu-id="8624e-629">Un' *unfixed* variabile di tipo `Xi` *dipende direttamente* una variabile di tipo unfixed `Xj` se per alcuni argomenti `Ek` con tipo `Tk` `Xj` si verifica un *tipo di input* dei `Ek` con tipo `Tk` e `Xi` si verifica un *tipo di output* di `Ek` con tipo `Tk`.</span><span class="sxs-lookup"><span data-stu-id="8624e-629">An *unfixed* type variable `Xi` *depends directly on* an unfixed type variable `Xj` if for some argument `Ek` with type `Tk` `Xj` occurs in an *input type* of `Ek` with type `Tk` and `Xi` occurs in an *output type* of `Ek` with type `Tk`.</span></span>

<span data-ttu-id="8624e-630">`Xj` *dipende* `Xi` se `Xj` *dipende direttamente* `Xi` oppure se `Xi` *dipende direttamente* `Xk` e `Xk` *dipende* `Xj`.</span><span class="sxs-lookup"><span data-stu-id="8624e-630">`Xj` *depends on* `Xi` if `Xj` *depends directly on* `Xi` or if `Xi` *depends directly on* `Xk` and `Xk` *depends on* `Xj`.</span></span> <span data-ttu-id="8624e-631">Di conseguenza "dipende" è la chiusura transitiva ma non riflessiva "dipende direttamente".</span><span class="sxs-lookup"><span data-stu-id="8624e-631">Thus "depends on" is the transitive but not reflexive closure of "depends directly on".</span></span>

#### <a name="output-type-inferences"></a><span data-ttu-id="8624e-632">Inferenza dei tipi output</span><span class="sxs-lookup"><span data-stu-id="8624e-632">Output type inferences</span></span>

<span data-ttu-id="8624e-633">Un' *inferenza del tipo di output* viene effettuata *dalla* un'espressione `E` *a* un tipo `T` nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="8624e-633">An *output type inference* is made *from* an expression `E` *to* a type `T` in the following way:</span></span>

*  <span data-ttu-id="8624e-634">Se `E` è una funzione anonima con tipo restituito dedotto `U` ([derivato il tipo restituito](expressions.md#inferred-return-type)) e `T` è un tipo delegato o un tipo di albero delle espressioni con tipo restituito `Tb`, quindi un *inferenza di livello inferiore* ([limite inferiore inferenze](expressions.md#lower-bound-inferences)) viene effettuata *da* `U` *a* `Tb`.</span><span class="sxs-lookup"><span data-stu-id="8624e-634">If `E` is an anonymous function with inferred return type  `U` ([Inferred return type](expressions.md#inferred-return-type)) and `T` is a delegate type or expression tree type with return type `Tb`, then a *lower-bound inference* ([Lower-bound inferences](expressions.md#lower-bound-inferences)) is made *from* `U` *to* `Tb`.</span></span>
*  <span data-ttu-id="8624e-635">In caso contrario, se `E` è un gruppo di metodi e `T` è un tipo delegato o un tipo di albero delle espressioni con tipi di parametro `T1...Tk` e il tipo restituito `Tb`e risoluzione dell'overload delle `E` con i tipi `T1...Tk` produce un metodo con tipo restituito unico `U`, un *inferenza di livello inferiore* viene effettuata *da* `U` *per* `Tb`.</span><span class="sxs-lookup"><span data-stu-id="8624e-635">Otherwise, if `E` is a method group and `T` is a delegate type or expression tree type with parameter types `T1...Tk` and return type `Tb`, and overload resolution of `E` with the types `T1...Tk` yields a single method with return type `U`, then a *lower-bound inference* is made *from* `U` *to* `Tb`.</span></span>
*  <span data-ttu-id="8624e-636">In caso contrario, se `E` è un'espressione con tipo `U`, quindi un *inferenza di livello inferiore* viene effettuata *da* `U` *a* `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-636">Otherwise, if `E` is an expression with type `U`, then a *lower-bound inference* is made *from* `U` *to* `T`.</span></span>
*  <span data-ttu-id="8624e-637">In caso contrario, viene eseguita alcuna inferenza.</span><span class="sxs-lookup"><span data-stu-id="8624e-637">Otherwise, no inferences are made.</span></span>

#### <a name="explicit-parameter-type-inferences"></a><span data-ttu-id="8624e-638">Inferenza dei tipi parametro espliciti</span><span class="sxs-lookup"><span data-stu-id="8624e-638">Explicit parameter type inferences</span></span>

<span data-ttu-id="8624e-639">Un' *inferenza del tipo di parametro espliciti* viene effettuata *dalla* un'espressione `E` *a* un tipo `T` nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="8624e-639">An *explicit parameter type inference* is made *from* an expression `E` *to* a type `T` in the following way:</span></span>

*  <span data-ttu-id="8624e-640">Se `E` è una funzione anonima tipizzata in modo esplicito con i tipi di parametro `U1...Uk` e `T` è un tipo delegato o un tipo di albero delle espressioni con tipi di parametro `V1...Vk` quindi per ogni `Ui` un *esatta inferenza* ([esatto inferenze](expressions.md#exact-inferences)) viene effettuata *dalla* `Ui` *a* corrispondente `Vi`.</span><span class="sxs-lookup"><span data-stu-id="8624e-640">If `E` is an explicitly typed anonymous function with parameter types `U1...Uk` and `T` is a delegate type or expression tree type with parameter types `V1...Vk` then for each `Ui` an *exact inference* ([Exact inferences](expressions.md#exact-inferences)) is made *from* `Ui` *to* the corresponding `Vi`.</span></span>

#### <a name="exact-inferences"></a><span data-ttu-id="8624e-641">Inferenze esatte</span><span class="sxs-lookup"><span data-stu-id="8624e-641">Exact inferences</span></span>

<span data-ttu-id="8624e-642">Un *esatte inferenza* *da* un tipo `U` *per* un tipo `V` viene presa come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-642">An *exact inference* *from* a type `U` *to* a type `V` is made as follows:</span></span>

*  <span data-ttu-id="8624e-643">Se `V` è uno dei *unfixed* `Xi` quindi `U` viene aggiunto al gruppo di limiti esatti per `Xi`.</span><span class="sxs-lookup"><span data-stu-id="8624e-643">If `V` is one of the *unfixed* `Xi` then `U` is added to the set of exact bounds for `Xi`.</span></span>

*  <span data-ttu-id="8624e-644">In caso contrario, imposta `V1...Vk` e `U1...Uk` sono determinati dalla presenza di uno qualsiasi dei seguenti casi:</span><span class="sxs-lookup"><span data-stu-id="8624e-644">Otherwise, sets `V1...Vk` and `U1...Uk` are determined by checking if any of the following cases apply:</span></span>

   *  <span data-ttu-id="8624e-645">`V` è un tipo di matrice `V1[...]` e `U` è un tipo matrice `U1[...]` dello stesso rango</span><span class="sxs-lookup"><span data-stu-id="8624e-645">`V` is an array type `V1[...]` and `U` is an array type `U1[...]`  of the same rank</span></span>
   *  <span data-ttu-id="8624e-646">`V` è di tipo `V1?` e `U` è il tipo `U1?`</span><span class="sxs-lookup"><span data-stu-id="8624e-646">`V` is the type `V1?` and `U` is the type `U1?`</span></span>
   *  <span data-ttu-id="8624e-647">`V` è un tipo costruito `C<V1...Vk>`e `U` è un tipo costruito `C<U1...Uk>`</span><span class="sxs-lookup"><span data-stu-id="8624e-647">`V` is a constructed type `C<V1...Vk>`and `U` is a constructed type `C<U1...Uk>`</span></span>

   <span data-ttu-id="8624e-648">Se uno di questi casi si applica quindi un' *esatte inferenza* viene effettuata *da* ogni `Ui` *a* corrispondente `Vi`.</span><span class="sxs-lookup"><span data-stu-id="8624e-648">If any of these cases apply then an *exact inference* is made *from* each `Ui` *to* the corresponding `Vi`.</span></span>

*  <span data-ttu-id="8624e-649">In caso contrario, viene eseguita alcuna inferenza.</span><span class="sxs-lookup"><span data-stu-id="8624e-649">Otherwise no inferences are made.</span></span>

#### <a name="lower-bound-inferences"></a><span data-ttu-id="8624e-650">Limite inferiore inferenze</span><span class="sxs-lookup"><span data-stu-id="8624e-650">Lower-bound inferences</span></span>

<span data-ttu-id="8624e-651">Oggetto *inferenza di livello inferiore* *dalla* un tipo `U` *a* un tipo `V` viene presa come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-651">A *lower-bound inference* *from* a type `U` *to* a type `V` is made as follows:</span></span>

*  <span data-ttu-id="8624e-652">Se `V` è uno dei *unfixed* `Xi` quindi `U` viene aggiunto al gruppo di limiti inferiori per `Xi`.</span><span class="sxs-lookup"><span data-stu-id="8624e-652">If `V` is one of the *unfixed* `Xi` then `U` is added to the set of lower bounds for `Xi`.</span></span>
*  <span data-ttu-id="8624e-653">In caso contrario, se `V` è di tipo `V1?`e `U` è il tipo `U1?` inferenza un limite inferiore è costituito dalla `U1` a `V1`.</span><span class="sxs-lookup"><span data-stu-id="8624e-653">Otherwise, if `V` is the type `V1?`and `U` is the type `U1?` then a lower bound inference is made from `U1` to `V1`.</span></span>
*  <span data-ttu-id="8624e-654">In caso contrario, imposta `U1...Uk` e `V1...Vk` sono determinati dalla presenza di uno qualsiasi dei seguenti casi:</span><span class="sxs-lookup"><span data-stu-id="8624e-654">Otherwise, sets `U1...Uk` and `V1...Vk` are determined by checking if any of the following cases apply:</span></span>
   *  <span data-ttu-id="8624e-655">`V` è un tipo di matrice `V1[...]` e `U` è un tipo matrice `U1[...]` (o un parametro di tipo il cui tipo di base effettivo è `U1[...]`) dello stesso rango</span><span class="sxs-lookup"><span data-stu-id="8624e-655">`V` is an array type `V1[...]` and `U` is an array type `U1[...]` (or a type parameter whose effective base type is `U1[...]`) of the same rank</span></span>
   *  <span data-ttu-id="8624e-656">`V` è uno dei `IEnumerable<V1>`, `ICollection<V1>` oppure `IList<V1>` e `U` è un tipo di matrice unidimensionale `U1[]`(o un parametro di tipo il cui tipo di base effettivo è `U1[]`)</span><span class="sxs-lookup"><span data-stu-id="8624e-656">`V` is one of `IEnumerable<V1>`, `ICollection<V1>` or `IList<V1>` and `U` is a one-dimensional array type `U1[]`(or a type parameter whose effective base type is `U1[]`)</span></span>
   *  <span data-ttu-id="8624e-657">`V` è un tipo di classe, struct, interfaccia o delegato costruito `C<V1...Vk>` e vi è un tipo univoco `C<U1...Uk>` in modo che `U` (oppure, se `U` è un parametro di tipo, la relativa classe base effettiva o qualsiasi membro di un set di interfacce effettive) è identica a, eredita (direttamente o indirettamente), o implementa (direttamente o indirettamente) `C<U1...Uk>`.</span><span class="sxs-lookup"><span data-stu-id="8624e-657">`V` is a constructed class, struct, interface or delegate type `C<V1...Vk>` and there is a unique type `C<U1...Uk>` such that `U` (or, if `U` is a type parameter, its effective base class or any member of its effective interface set) is identical to, inherits from (directly or indirectly), or implements (directly or indirectly) `C<U1...Uk>`.</span></span>

      <span data-ttu-id="8624e-658">(La restrizione di "univocità" significa che nell'interfaccia del case `C<T> {} class U: C<X>, C<Y> {}`, quindi viene eseguita alcuna inferenza quando derivandolo da `U` al `C<T>` perché `U1` potrebbe essere `X` o `Y`.)</span><span class="sxs-lookup"><span data-stu-id="8624e-658">(The "uniqueness" restriction means that in the case interface `C<T> {} class U: C<X>, C<Y> {}`, then no inference is made when inferring from `U` to `C<T>` because `U1` could be `X` or `Y`.)</span></span>

   <span data-ttu-id="8624e-659">Se uno di questi casi si applica quindi viene eseguita un'inferenza *dal* ciascun `Ui` *al* corrispondente `Vi` come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-659">If any of these cases apply then an inference is made *from* each `Ui` *to* the corresponding `Vi` as follows:</span></span>

   *  <span data-ttu-id="8624e-660">Se `Ui` non è noto per essere un tipo riferimento un' *esatte inferenza* viene effettuata</span><span class="sxs-lookup"><span data-stu-id="8624e-660">If `Ui` is not known to be a reference type then an *exact inference* is made</span></span>
   *  <span data-ttu-id="8624e-661">In caso contrario, se `U` è un tipo matrice un' *limite inferiore inferenza* viene effettuata</span><span class="sxs-lookup"><span data-stu-id="8624e-661">Otherwise, if `U` is an array type then a *lower-bound inference* is made</span></span>
   *  <span data-ttu-id="8624e-662">In caso contrario, se `V` viene `C<V1...Vk>` , l'inferenza dipende il parametro di tipo i-esimo `C`:</span><span class="sxs-lookup"><span data-stu-id="8624e-662">Otherwise, if `V` is `C<V1...Vk>` then inference depends on the i-th type parameter of `C`:</span></span>
      *  <span data-ttu-id="8624e-663">Se è covariante un' *inferenza di livello inferiore* viene eseguita.</span><span class="sxs-lookup"><span data-stu-id="8624e-663">If it is covariant then a *lower-bound inference* is made.</span></span>
      *  <span data-ttu-id="8624e-664">Se è controvariante un' *inferenza del limite superiore* viene eseguita.</span><span class="sxs-lookup"><span data-stu-id="8624e-664">If it is contravariant then an *upper-bound inference* is made.</span></span>
      *  <span data-ttu-id="8624e-665">Se è invariante un' *esatte inferenza* viene eseguita.</span><span class="sxs-lookup"><span data-stu-id="8624e-665">If it is invariant then an *exact inference* is made.</span></span>
*  <span data-ttu-id="8624e-666">In caso contrario, viene eseguita alcuna inferenza.</span><span class="sxs-lookup"><span data-stu-id="8624e-666">Otherwise, no inferences are made.</span></span>

#### <a name="upper-bound-inferences"></a><span data-ttu-id="8624e-667">Limite superiore inferenze</span><span class="sxs-lookup"><span data-stu-id="8624e-667">Upper-bound inferences</span></span>

<span data-ttu-id="8624e-668">Un' *inferenza del limite superiore* *dalla* un tipo `U` *a* un tipo `V` viene presa come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-668">An *upper-bound inference* *from* a type `U` *to* a type `V` is made as follows:</span></span>

*  <span data-ttu-id="8624e-669">Se `V` è uno dei *unfixed* `Xi` quindi `U` viene aggiunto al gruppo di limiti superiori per `Xi`.</span><span class="sxs-lookup"><span data-stu-id="8624e-669">If `V` is one of the *unfixed* `Xi` then `U` is added to the set of upper bounds for `Xi`.</span></span>
*  <span data-ttu-id="8624e-670">In caso contrario, imposta `V1...Vk` e `U1...Uk` sono determinati dalla presenza di uno qualsiasi dei seguenti casi:</span><span class="sxs-lookup"><span data-stu-id="8624e-670">Otherwise, sets `V1...Vk` and `U1...Uk` are determined by checking if any of the following cases apply:</span></span>
   *  <span data-ttu-id="8624e-671">`U` è un tipo di matrice `U1[...]` e `V` è un tipo matrice `V1[...]` dello stesso rango</span><span class="sxs-lookup"><span data-stu-id="8624e-671">`U` is an array type `U1[...]` and `V` is an array type `V1[...]` of the same rank</span></span>
   *  <span data-ttu-id="8624e-672">`U` è uno dei `IEnumerable<Ue>`, `ICollection<Ue>` oppure `IList<Ue>` e `V` è un tipo di matrice unidimensionale `Ve[]`</span><span class="sxs-lookup"><span data-stu-id="8624e-672">`U` is one of `IEnumerable<Ue>`, `ICollection<Ue>` or `IList<Ue>` and `V` is a one-dimensional array type `Ve[]`</span></span>
   *  <span data-ttu-id="8624e-673">`U` è di tipo `U1?` e `V` è il tipo `V1?`</span><span class="sxs-lookup"><span data-stu-id="8624e-673">`U` is the type `U1?` and `V` is the type `V1?`</span></span>
   *  <span data-ttu-id="8624e-674">`U` viene costruito classe, struct, interfaccia o un tipo delegato `C<U1...Uk>` e `V` è una classe, struct, interfaccia o un tipo delegato che è identico a, eredita da (direttamente o indirettamente) o implementa (direttamente o indirettamente) un tipo univoco `C<V1...Vk>`</span><span class="sxs-lookup"><span data-stu-id="8624e-674">`U` is constructed class, struct, interface or delegate type `C<U1...Uk>` and `V` is a class, struct, interface or delegate type which is identical to, inherits from (directly or indirectly), or implements (directly or indirectly) a unique type `C<V1...Vk>`</span></span>

      <span data-ttu-id="8624e-675">(La restrizione di "univocità" significa che, se abbiamo `interface C<T>{} class V<Z>: C<X<Z>>, C<Y<Z>>{}`, viene eseguita alcuna inferenza quando derivandolo da `C<U1>` a `V<Q>`.</span><span class="sxs-lookup"><span data-stu-id="8624e-675">(The "uniqueness" restriction means that if we have `interface C<T>{} class V<Z>: C<X<Z>>, C<Y<Z>>{}`, then no inference is made when inferring from `C<U1>` to `V<Q>`.</span></span> <span data-ttu-id="8624e-676">Inferenze non vengano apportate dal `U1` a uno `X<Q>` o `Y<Q>`.)</span><span class="sxs-lookup"><span data-stu-id="8624e-676">Inferences are not made from `U1` to either `X<Q>` or `Y<Q>`.)</span></span>

   <span data-ttu-id="8624e-677">Se uno di questi casi si applica quindi viene eseguita un'inferenza *dal* ciascun `Ui` *al* corrispondente `Vi` come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-677">If any of these cases apply then an inference is made *from* each `Ui` *to* the corresponding `Vi` as follows:</span></span>
   *  <span data-ttu-id="8624e-678">Se `Ui` non è noto per essere un tipo riferimento un' *esatte inferenza* viene effettuata</span><span class="sxs-lookup"><span data-stu-id="8624e-678">If  `Ui` is not known to be a reference type then an *exact inference* is made</span></span>
   *  <span data-ttu-id="8624e-679">In caso contrario, se `V` è un tipo matrice un' *inferenza del limite superiore* viene effettuata</span><span class="sxs-lookup"><span data-stu-id="8624e-679">Otherwise, if `V` is an array type then an *upper-bound inference* is made</span></span>
   *  <span data-ttu-id="8624e-680">In caso contrario, se `U` viene `C<U1...Uk>` , l'inferenza dipende il parametro di tipo i-esimo `C`:</span><span class="sxs-lookup"><span data-stu-id="8624e-680">Otherwise, if `U` is `C<U1...Uk>` then inference depends on the i-th type parameter of `C`:</span></span>
      *  <span data-ttu-id="8624e-681">Se è covariante un' *inferenza del limite superiore* viene eseguita.</span><span class="sxs-lookup"><span data-stu-id="8624e-681">If it is covariant then an *upper-bound inference* is made.</span></span>
      *  <span data-ttu-id="8624e-682">Se è controvariante un' *inferenza di livello inferiore* viene eseguita.</span><span class="sxs-lookup"><span data-stu-id="8624e-682">If it is contravariant then a *lower-bound inference* is made.</span></span>
      *  <span data-ttu-id="8624e-683">Se è invariante un' *esatte inferenza* viene eseguita.</span><span class="sxs-lookup"><span data-stu-id="8624e-683">If it is invariant then an *exact inference* is made.</span></span>
*  <span data-ttu-id="8624e-684">In caso contrario, viene eseguita alcuna inferenza.</span><span class="sxs-lookup"><span data-stu-id="8624e-684">Otherwise, no inferences are made.</span></span>   

#### <a name="fixing"></a><span data-ttu-id="8624e-685">La correzione</span><span class="sxs-lookup"><span data-stu-id="8624e-685">Fixing</span></span>

<span data-ttu-id="8624e-686">Un' *unfixed* variabile di tipo `Xi` con un set di limiti viene *risolto* come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-686">An *unfixed* type variable `Xi` with a set of bounds is *fixed* as follows:</span></span>

*  <span data-ttu-id="8624e-687">Il set di *tipi di candidato* `Uj` inizia come il set di tutti i tipi nel set di limiti per `Xi`.</span><span class="sxs-lookup"><span data-stu-id="8624e-687">The set of *candidate types* `Uj` starts out as the set of all types in the set of bounds for `Xi`.</span></span>
*  <span data-ttu-id="8624e-688">Viene quindi esaminato ogni limite per `Xi` a sua volta: Per ogni associazione esatto `U` dei `Xi` tutti i tipi `Uj` che non sono identici a `U` vengono rimosse dal set di candidati.</span><span class="sxs-lookup"><span data-stu-id="8624e-688">We then examine each bound for `Xi` in turn: For each exact bound `U` of `Xi` all types `Uj` which are not identical to `U` are removed from the candidate set.</span></span> <span data-ttu-id="8624e-689">Per ogni limite inferiore `U` dei `Xi` tutti i tipi `Uj` al quale sono presenti sia *non* una conversione implicita da `U` vengono rimosse dal set di candidati.</span><span class="sxs-lookup"><span data-stu-id="8624e-689">For each lower bound `U` of `Xi` all types `Uj` to which there is *not* an implicit conversion from `U` are removed from the candidate set.</span></span> <span data-ttu-id="8624e-690">Per ogni limite superiore `U` dei `Xi` tutti i tipi `Uj` al quale è costituita *non* una conversione implicita a `U` vengono rimosse dal set di candidati.</span><span class="sxs-lookup"><span data-stu-id="8624e-690">For each upper bound `U` of `Xi` all types `Uj` from which there is *not* an implicit conversion to `U` are removed from the candidate set.</span></span>
*  <span data-ttu-id="8624e-691">Se tra i tipi di candidato rimanenti `Uj` è presente un tipo univoco `V` da cui è presente una conversione implicita a tutti gli altri candidati tipi, quindi `Xi` fissa `V`.</span><span class="sxs-lookup"><span data-stu-id="8624e-691">If among the remaining candidate types `Uj` there is a unique type `V` from which there is an implicit conversion to all the other candidate types, then `Xi` is fixed to `V`.</span></span>
*  <span data-ttu-id="8624e-692">In caso contrario, l'inferenza del tipo ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="8624e-692">Otherwise, type inference fails.</span></span>

#### <a name="inferred-return-type"></a><span data-ttu-id="8624e-693">Tipo restituito dedotto</span><span class="sxs-lookup"><span data-stu-id="8624e-693">Inferred return type</span></span>

<span data-ttu-id="8624e-694">Il derivato il tipo restituito di una funzione anonima `F` viene usato durante la risoluzione dell'overload e l'inferenza del tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-694">The inferred return type of an anonymous function `F` is used during type inference and overload resolution.</span></span> <span data-ttu-id="8624e-695">Il tipo restituito dedotto può essere determinato solo per una funzione anonima in cui parametro tutti i tipi sono noti in quanto viene assegnato in modo esplicito, fornite tramite una conversione funzione anonima o dedotto durante l'inferenza del tipo in un contenitore generico chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="8624e-695">The inferred return type can only be determined for an anonymous function where all parameter types are known, either because they are explicitly given, provided through an anonymous function conversion or inferred during type inference on an enclosing generic method invocation.</span></span>

<span data-ttu-id="8624e-696">Il ***dedotto il tipo di risultato*** viene determinata come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-696">The ***inferred result type*** is determined as follows:</span></span>

*  <span data-ttu-id="8624e-697">Se il corpo della `F` è un *espressione* che ha un tipo, il tipo di risultato derivato `F` è il tipo dell'espressione.</span><span class="sxs-lookup"><span data-stu-id="8624e-697">If the body of `F` is an *expression* that has a type, then the inferred result type of `F` is the type of that expression.</span></span>
*  <span data-ttu-id="8624e-698">Se il corpo della `F` è un *block* e il set di espressioni del blocco `return` istruzioni ha un tipo comune `T` ([ricerca del tipo common migliore di un set di espressioni](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)), quindi il tipo di risultato derivato `F` è `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-698">If the body of `F` is a *block* and the set of expressions in the block's `return` statements has a best common type `T` ([Finding the best common type of a set of expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)), then the inferred result type of `F` is `T`.</span></span>
*  <span data-ttu-id="8624e-699">In caso contrario, non è possibile dedurre un tipo di risultato per `F`.</span><span class="sxs-lookup"><span data-stu-id="8624e-699">Otherwise, a result type cannot be inferred for `F`.</span></span>

<span data-ttu-id="8624e-700">Il ***dedotto il tipo restituito*** viene determinata come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-700">The ***inferred return type*** is determined as follows:</span></span>

*  <span data-ttu-id="8624e-701">Se `F` è asincrono e il corpo della `F` è un'espressione classificata come nothing ([classificazioni delle espressioni](expressions.md#expression-classifications)), o un blocco di istruzioni in cui l'istruzione return non dispongono di espressioni, dedotto il tipo restituito è `System.Threading.Tasks.Task`</span><span class="sxs-lookup"><span data-stu-id="8624e-701">If `F` is async and the body of `F` is either an expression classified as nothing ([Expression classifications](expressions.md#expression-classifications)), or a statement block where no return statements have expressions, the inferred return type is `System.Threading.Tasks.Task`</span></span>
*  <span data-ttu-id="8624e-702">Se `F` è asincrona e ha un tipo di risultato derivato `T`, derivato il tipo restituito è `System.Threading.Tasks.Task<T>`.</span><span class="sxs-lookup"><span data-stu-id="8624e-702">If `F` is async and has an inferred result type `T`, the inferred return type is `System.Threading.Tasks.Task<T>`.</span></span>
*  <span data-ttu-id="8624e-703">Se `F` è non asincrone e ha un tipo di risultato derivato `T`, derivato il tipo restituito è `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-703">If `F` is non-async and has an inferred result type `T`, the inferred return type is `T`.</span></span>
*  <span data-ttu-id="8624e-704">In caso contrario, non è possibile dedurre un tipo restituito per `F`.</span><span class="sxs-lookup"><span data-stu-id="8624e-704">Otherwise a return type cannot be inferred for `F`.</span></span>

<span data-ttu-id="8624e-705">Ad esempio di inferenza del tipo che interessa le funzioni anonime, prendere in considerazione la `Select` metodo di estensione dichiarato nel `System.Linq.Enumerable` classe:</span><span class="sxs-lookup"><span data-stu-id="8624e-705">As an example of type inference involving anonymous functions, consider the `Select` extension method declared in the `System.Linq.Enumerable` class:</span></span>
```csharp
namespace System.Linq
{
    public static class Enumerable
    {
        public static IEnumerable<TResult> Select<TSource,TResult>(
            this IEnumerable<TSource> source,
            Func<TSource,TResult> selector)
        {
            foreach (TSource element in source) yield return selector(element);
        }
    }
}
```

<span data-ttu-id="8624e-706">Supponendo che il `System.Linq` dello spazio dei nomi è stato importato con un `using` clausola e una classe `Customer` con un `Name` vlastnosti typu `string`, il `Select` metodo può essere utilizzato per selezionare il nome di un elenco di clienti:</span><span class="sxs-lookup"><span data-stu-id="8624e-706">Assuming the `System.Linq` namespace was imported with a `using` clause, and given a class `Customer` with a `Name` property of type `string`, the `Select` method can be used to select the names of a list of customers:</span></span>
```csharp
List<Customer> customers = GetCustomerList();
IEnumerable<string> names = customers.Select(c => c.Name);
```

<span data-ttu-id="8624e-707">La chiamata al metodo di estensione ([chiamate al metodo di estensione](expressions.md#extension-method-invocations)) di `Select` viene elaborato riscrivendo la chiamata a una chiamata al metodo statico:</span><span class="sxs-lookup"><span data-stu-id="8624e-707">The extension method invocation ([Extension method invocations](expressions.md#extension-method-invocations)) of `Select` is processed by rewriting the invocation to a static method invocation:</span></span>
```csharp
IEnumerable<string> names = Enumerable.Select(customers, c => c.Name);
```

<span data-ttu-id="8624e-708">Poiché gli argomenti di tipo non sono stati specificati in modo esplicito, l'inferenza del tipo viene utilizzato per dedurre gli argomenti tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-708">Since type arguments were not explicitly specified, type inference is used to infer the type arguments.</span></span> <span data-ttu-id="8624e-709">Prima di tutto, il `customers` argomento è correlato al `source` parametro, l'inferenza `T` essere `Customer`.</span><span class="sxs-lookup"><span data-stu-id="8624e-709">First, the `customers` argument is related to the `source` parameter, inferring `T` to be `Customer`.</span></span> <span data-ttu-id="8624e-710">Usando la funzione anonima digitare quindi il processo di inferenza descritto in precedenza, `c` ha tipo `Customer`e l'espressione `c.Name` è correlato al tipo restituito del `selector` parametro, l'inferenza `S` sia `string`.</span><span class="sxs-lookup"><span data-stu-id="8624e-710">Then, using the anonymous function type inference process described above, `c` is given type `Customer`, and the expression `c.Name` is related to the return type of the `selector` parameter, inferring `S` to be `string`.</span></span> <span data-ttu-id="8624e-711">Di conseguenza, la chiamata è equivalente a</span><span class="sxs-lookup"><span data-stu-id="8624e-711">Thus, the invocation is equivalent to</span></span>
```csharp
Sequence.Select<Customer,string>(customers, (Customer c) => c.Name)
```
<span data-ttu-id="8624e-712">e il risultato è di tipo `IEnumerable<string>`.</span><span class="sxs-lookup"><span data-stu-id="8624e-712">and the result is of type `IEnumerable<string>`.</span></span>

<span data-ttu-id="8624e-713">L'esempio seguente illustra come anonimo tipo di funzione l'inferenza consente di informazioni sul tipo "flusso" tra gli argomenti nella chiamata a un metodo generico.</span><span class="sxs-lookup"><span data-stu-id="8624e-713">The following example demonstrates how anonymous function type inference allows type information to "flow" between arguments in a generic method invocation.</span></span> <span data-ttu-id="8624e-714">Si consideri il metodo seguente:</span><span class="sxs-lookup"><span data-stu-id="8624e-714">Given the method:</span></span>
```csharp
static Z F<X,Y,Z>(X value, Func<X,Y> f1, Func<Y,Z> f2) {
    return f2(f1(value));
}
```

<span data-ttu-id="8624e-715">Inferenza del tipo per la chiamata:</span><span class="sxs-lookup"><span data-stu-id="8624e-715">Type inference for the invocation:</span></span>
```csharp
double seconds = F("1:15:30", s => TimeSpan.Parse(s), t => t.TotalSeconds);
```
<span data-ttu-id="8624e-716">le operazioni seguenti: Per prima cosa, l'argomento `"1:15:30"` è correlato il `value` parametro, l'inferenza `X` essere `string`.</span><span class="sxs-lookup"><span data-stu-id="8624e-716">proceeds as follows: First, the argument `"1:15:30"` is related to the `value` parameter, inferring `X` to be `string`.</span></span> <span data-ttu-id="8624e-717">Quindi, il parametro della funzione anonima prima, `s`, viene assegnato il tipo dedotto `string`e l'espressione `TimeSpan.Parse(s)` è correlato al tipo restituito del `f1`, l'inferenza `Y` essere `System.TimeSpan`.</span><span class="sxs-lookup"><span data-stu-id="8624e-717">Then, the parameter of the first anonymous function, `s`, is given the inferred type `string`, and the expression `TimeSpan.Parse(s)` is related to the return type of `f1`, inferring `Y` to be `System.TimeSpan`.</span></span> <span data-ttu-id="8624e-718">Infine, il parametro della seconda funzione anonima, `t`, viene assegnato il tipo dedotto `System.TimeSpan`e l'espressione `t.TotalSeconds` è correlato al tipo restituito del `f2`, l'inferenza `Z` essere `double`.</span><span class="sxs-lookup"><span data-stu-id="8624e-718">Finally, the parameter of the second anonymous function, `t`, is given the inferred type `System.TimeSpan`, and the expression `t.TotalSeconds` is related to the return type of `f2`, inferring `Z` to be `double`.</span></span> <span data-ttu-id="8624e-719">Di conseguenza, il risultato della chiamata è di tipo `double`.</span><span class="sxs-lookup"><span data-stu-id="8624e-719">Thus, the result of the invocation is of type `double`.</span></span>

#### <a name="type-inference-for-conversion-of-method-groups"></a><span data-ttu-id="8624e-720">Inferenza del tipo per la conversione dei gruppi di metodi</span><span class="sxs-lookup"><span data-stu-id="8624e-720">Type inference for conversion of method groups</span></span>

<span data-ttu-id="8624e-721">Simile alle chiamate di metodi generici, inferenza del tipo deve essere applicata anche quando un gruppo di metodi `M` che contiene un metodo generico viene convertito in un tipo delegato specificato `D` ([conversioni dei metodi di gruppo](conversions.md#method-group-conversions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-721">Similar to calls of generic methods, type inference must also be applied when a method group `M` containing a generic method is converted to a given delegate type `D` ([Method group conversions](conversions.md#method-group-conversions)).</span></span> <span data-ttu-id="8624e-722">Dato un metodo</span><span class="sxs-lookup"><span data-stu-id="8624e-722">Given a method</span></span>
```csharp
Tr M<X1...Xn>(T1 x1 ... Tm xm)
```
<span data-ttu-id="8624e-723">e il gruppo di metodi `M` assegnato al tipo di delegato `D` è l'attività di inferenza del tipo per trovare gli argomenti di tipo `S1...Sn` in modo che l'espressione:</span><span class="sxs-lookup"><span data-stu-id="8624e-723">and the method group `M` being assigned to the delegate type `D` the task of type inference is to find type arguments `S1...Sn` so that the expression:</span></span>
```csharp
M<S1...Sn>
```
<span data-ttu-id="8624e-724">diventa compatibile ([dichiarazioni Delegate](delegates.md#delegate-declarations)) con `D`.</span><span class="sxs-lookup"><span data-stu-id="8624e-724">becomes compatible ([Delegate declarations](delegates.md#delegate-declarations)) with `D`.</span></span>

<span data-ttu-id="8624e-725">A differenza di algoritmo di inferenza del tipo per le chiamate di metodo generico, in questo caso sono presenti solo argomento *tipi*, nessun argomento *espressioni*.</span><span class="sxs-lookup"><span data-stu-id="8624e-725">Unlike the type inference algorithm for generic method calls, in this case there are only argument *types*, no argument *expressions*.</span></span> <span data-ttu-id="8624e-726">In particolare, non sono presenti funzioni anonime e pertanto non è necessario per più fasi di inferenza.</span><span class="sxs-lookup"><span data-stu-id="8624e-726">In particular, there are no anonymous functions and hence no need for multiple phases of inference.</span></span>

<span data-ttu-id="8624e-727">Al contrario, tutti i `Xi` sono considerati *unfixed*e un *inferenza di livello inferiore* viene effettuata *da* ogni tipo di argomento `Uj` di `D` *al* tipo di parametro corrispondente `Tj` di `M`.</span><span class="sxs-lookup"><span data-stu-id="8624e-727">Instead, all `Xi` are considered *unfixed*, and a *lower-bound inference* is made *from* each argument type `Uj` of `D` *to* the corresponding parameter type `Tj` of `M`.</span></span> <span data-ttu-id="8624e-728">Se per uno qualsiasi del `Xi` sono stati trovati senza limiti, l'inferenza del tipo ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="8624e-728">If for any of the `Xi` no bounds were found, type inference fails.</span></span> <span data-ttu-id="8624e-729">In caso contrario, tutti i `Xi` vengono *fissa* corrispondente `Si`, che sono il risultato di inferenza del tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-729">Otherwise, all `Xi` are *fixed* to corresponding `Si`, which are the result of type inference.</span></span>

#### <a name="finding-the-best-common-type-of-a-set-of-expressions"></a><span data-ttu-id="8624e-730">Ricerca del tipo common migliore di un set di espressioni</span><span class="sxs-lookup"><span data-stu-id="8624e-730">Finding the best common type of a set of expressions</span></span>

<span data-ttu-id="8624e-731">In alcuni casi, un tipo comune deve essere derivata per un set di espressioni.</span><span class="sxs-lookup"><span data-stu-id="8624e-731">In some cases, a common type needs to be inferred for a set of expressions.</span></span> <span data-ttu-id="8624e-732">In particolare i tipi di elementi delle matrici tipizzate in modo implicito e i tipi restituiti di funzioni anonime con *blocco* corpi di sono disponibili in questo modo.</span><span class="sxs-lookup"><span data-stu-id="8624e-732">In particular, the element types of implicitly typed arrays and the return types of anonymous functions with *block* bodies are found in this way.</span></span>

<span data-ttu-id="8624e-733">Intuitivamente, dato un set di espressioni `E1...Em` questo inferenza deve essere equivalente alla chiamata di un metodo</span><span class="sxs-lookup"><span data-stu-id="8624e-733">Intuitively, given a set of expressions `E1...Em` this inference should be equivalent to calling a method</span></span>
```csharp
Tr M<X>(X x1 ... X xm)
```
<span data-ttu-id="8624e-734">con la `Ei` come argomenti.</span><span class="sxs-lookup"><span data-stu-id="8624e-734">with the `Ei` as arguments.</span></span>

<span data-ttu-id="8624e-735">Più precisamente, l'inferenza inizia con un *unfixed* variabile di tipo `X`.</span><span class="sxs-lookup"><span data-stu-id="8624e-735">More precisely, the inference starts out with an *unfixed* type variable `X`.</span></span> <span data-ttu-id="8624e-736">*Inferenza dei tipi di output* vengono quindi resi *dalla* ogni `Ei` *alla* `X`.</span><span class="sxs-lookup"><span data-stu-id="8624e-736">*Output type inferences* are then made *from* each `Ei` *to* `X`.</span></span> <span data-ttu-id="8624e-737">Infine `X` viene *fissa* e, se ha esito positivo, l'oggetto risultante digitare `S` è il tipo comune migliore risulta per le espressioni.</span><span class="sxs-lookup"><span data-stu-id="8624e-737">Finally, `X` is *fixed* and, if successful, the resulting type `S` is the resulting best common type for the expressions.</span></span> <span data-ttu-id="8624e-738">Se non `S` esiste, le espressioni non dispone di alcun tipo comune.</span><span class="sxs-lookup"><span data-stu-id="8624e-738">If no such `S` exists, the expressions have no best common type.</span></span>

### <a name="overload-resolution"></a><span data-ttu-id="8624e-739">Risoluzione dell'overload</span><span class="sxs-lookup"><span data-stu-id="8624e-739">Overload resolution</span></span>

<span data-ttu-id="8624e-740">Risoluzione dell'overload è un meccanismo in fase di associazione per la selezione il miglior membro di funzione di richiamata, dato un elenco di argomenti e un set di membri di funzione candidati.</span><span class="sxs-lookup"><span data-stu-id="8624e-740">Overload resolution is a binding-time mechanism for selecting the best function member to invoke given an argument list and a set of candidate function members.</span></span> <span data-ttu-id="8624e-741">Risoluzione dell'overload seleziona il membro di funzione da richiamare nei contesti seguenti distinti all'interno di c#:</span><span class="sxs-lookup"><span data-stu-id="8624e-741">Overload resolution selects the function member to invoke in the following distinct contexts within C#:</span></span>

*  <span data-ttu-id="8624e-742">Chiamata di un metodo denominato un *invocation_expression* ([chiamate al metodo](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="8624e-742">Invocation of a method named in an *invocation_expression* ([Method invocations](expressions.md#method-invocations)).</span></span>
*  <span data-ttu-id="8624e-743">Chiamata di un costruttore di istanza denominata un' *object_creation_expression* ([espressioni per la creazione dell'oggetto](expressions.md#object-creation-expressions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-743">Invocation of an instance constructor named in an *object_creation_expression* ([Object creation expressions](expressions.md#object-creation-expressions)).</span></span>
*  <span data-ttu-id="8624e-744">Chiamata di una funzione di accesso dell'indicizzatore tramite un *element_access* ([l'accesso all'elemento](expressions.md#element-access)).</span><span class="sxs-lookup"><span data-stu-id="8624e-744">Invocation of an indexer accessor through an *element_access* ([Element access](expressions.md#element-access)).</span></span>
*  <span data-ttu-id="8624e-745">Chiamata di un operatore predefinito o definito dall'utente a cui fa riferimento un'espressione ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution) e [risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="8624e-745">Invocation of a predefined or user-defined operator referenced in an expression ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution) and [Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)).</span></span>

<span data-ttu-id="8624e-746">Ognuno di questi contesti definisce il set di membri di funzione candidati e l'elenco di argomenti in modo univoco, come descritto in dettaglio nelle sezioni elencate in precedenza.</span><span class="sxs-lookup"><span data-stu-id="8624e-746">Each of these contexts defines the set of candidate function members and the list of arguments in its own unique way, as described in detail in the sections listed above.</span></span> <span data-ttu-id="8624e-747">Ad esempio, il set di candidati per una chiamata al metodo non include i metodi contrassegnati `override` ([ricerca di membri](expressions.md#member-lookup)), e i metodi in una classe base non sono candidati se qualsiasi metodo in una classe derivata è applicabile ([ Chiamate al metodo](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="8624e-747">For example, the set of candidates for a method invocation does not include methods marked `override` ([Member lookup](expressions.md#member-lookup)), and methods in a base class are not candidates if any method in a derived class is applicable ([Method invocations](expressions.md#method-invocations)).</span></span>

<span data-ttu-id="8624e-748">Dopo che sono stati identificati i membri di funzione candidati e l'elenco di argomenti, la selezione del miglior membro di funzione è lo stesso in tutti i casi:</span><span class="sxs-lookup"><span data-stu-id="8624e-748">Once the candidate function members and the argument list have been identified, the selection of the best function member is the same in all cases:</span></span>

*  <span data-ttu-id="8624e-749">Dato il set di membri di funzione candidati applicabili, il membro funzione migliore in quanto viene individuato.</span><span class="sxs-lookup"><span data-stu-id="8624e-749">Given the set of applicable candidate function members, the best function member in that set is located.</span></span> <span data-ttu-id="8624e-750">Se il set contiene un solo membro di funzione, tale membro di funzione è il miglior membro di funzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-750">If the set contains only one function member, then that function member is the best function member.</span></span> <span data-ttu-id="8624e-751">In caso contrario, il miglior membro di funzione è il membro di una funzione che sia migliore rispetto a tutti gli altri membri di funzione rispetto all'elenco di argomenti specificato, purché ogni membro di funzione viene confrontato con tutti gli altri membri di funzione usando le regole in [ Miglior membro di funzione](expressions.md#better-function-member).</span><span class="sxs-lookup"><span data-stu-id="8624e-751">Otherwise, the best function member is the one function member that is better than all other function members with respect to the given argument list, provided that each function member is compared to all other function members using the rules in [Better function member](expressions.md#better-function-member).</span></span> <span data-ttu-id="8624e-752">Se si verifica non è uguale a un membro di funzione migliore di tutti gli altri membri di funzione, quindi la chiamata alla funzione membro è ambigua e si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-752">If there is not exactly one function member that is better than all other function members, then the function member invocation is ambiguous and a binding-time error occurs.</span></span>

<span data-ttu-id="8624e-753">Le sezioni seguenti definiscono l'esatto significato dei termini ***membro di funzione applicabile*** e ***miglior membro di funzione***.</span><span class="sxs-lookup"><span data-stu-id="8624e-753">The following sections define the exact meanings of the terms ***applicable function member*** and ***better function member***.</span></span>

#### <a name="applicable-function-member"></a><span data-ttu-id="8624e-754">Membro di funzione applicabile</span><span class="sxs-lookup"><span data-stu-id="8624e-754">Applicable function member</span></span>

<span data-ttu-id="8624e-755">Un membro di funzione viene definito un' ***membro di funzione applicabile*** rispetto a un elenco di argomenti `A` quando tutte le operazioni seguenti sono vere:</span><span class="sxs-lookup"><span data-stu-id="8624e-755">A function member is said to be an ***applicable function member*** with respect to an argument list `A` when all of the following are true:</span></span>

*  <span data-ttu-id="8624e-756">Ogni argomento nel `A` corrisponde a un parametro nella dichiarazione di membro di funzione, come descritto in [i parametri corrispondenti](expressions.md#corresponding-parameters), e qualsiasi parametro a cui non corrisponde alcun argomento è un parametro facoltativo.</span><span class="sxs-lookup"><span data-stu-id="8624e-756">Each argument in `A` corresponds to a parameter in the function member declaration as described in [Corresponding parameters](expressions.md#corresponding-parameters), and any parameter to which no argument corresponds is an optional parameter.</span></span>
*  <span data-ttu-id="8624e-757">Per ogni argomento nel `A`, il modalità dell'argomento di passaggio dei parametri (ad esempio, valore, `ref`, o `out`) è identica alla modalità di passaggio del parametro corrispondente, e</span><span class="sxs-lookup"><span data-stu-id="8624e-757">For each argument in `A`, the parameter passing mode of the argument (i.e., value, `ref`, or `out`) is identical to the parameter passing mode of the corresponding parameter, and</span></span>
   *  <span data-ttu-id="8624e-758">per un parametro di valore o una matrice di parametri, una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) esiste dall'argomento e il tipo del parametro corrispondente, o</span><span class="sxs-lookup"><span data-stu-id="8624e-758">for a value parameter or a parameter array, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from the argument to the type of the corresponding parameter, or</span></span>
   *  <span data-ttu-id="8624e-759">per un `ref` o `out` parametro, il tipo dell'argomento è identico al tipo del parametro corrispondente.</span><span class="sxs-lookup"><span data-stu-id="8624e-759">for a `ref` or `out` parameter, the type of the argument is identical to the type of the corresponding parameter.</span></span> <span data-ttu-id="8624e-760">Dopo tutto, un `ref` o `out` parametro è un alias per l'argomento passato.</span><span class="sxs-lookup"><span data-stu-id="8624e-760">After all, a `ref` or `out` parameter is an alias for the argument passed.</span></span>

<span data-ttu-id="8624e-761">Per un membro di funzione che include una matrice di parametri, se il membro di funzione è applicabile per le regole precedenti, viene definita sia applicabile nel relativo ***forma normale***.</span><span class="sxs-lookup"><span data-stu-id="8624e-761">For a function member that includes a parameter array, if the function member is applicable by the above rules, it is said to be applicable in its ***normal form***.</span></span> <span data-ttu-id="8624e-762">Se un membro di funzione che include una matrice di parametri non è applicabile in forma normale, il membro di funzione invece potrebbe essere applicabile nel relativo ***espanso form***:</span><span class="sxs-lookup"><span data-stu-id="8624e-762">If a function member that includes a parameter array is not applicable in its normal form, the function member may instead be applicable in its ***expanded form***:</span></span>

*  <span data-ttu-id="8624e-763">La forma espansa viene costruita, sostituendo la matrice di parametri nella dichiarazione di membro di funzione con zero o più parametri di valore del tipo di elemento del parametro di matrice ad esempio che il numero di argomenti nell'elenco di argomenti `A` corrisponde al totale numero di parametri.</span><span class="sxs-lookup"><span data-stu-id="8624e-763">The expanded form is constructed by replacing the parameter array in the function member declaration with zero or more value parameters of the element type of the parameter array such that the number of arguments in the argument list `A` matches the total number of parameters.</span></span> <span data-ttu-id="8624e-764">Se `A` dispone di meno argomenti di maggiore del numero di parametri fissi nella dichiarazione di membro di funzione, la forma espansa del membro di funzione non può essere costruita e pertanto non è applicabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-764">If `A` has fewer arguments than the number of fixed parameters in the function member declaration, the expanded form of the function member cannot be constructed and is thus not applicable.</span></span>
*  <span data-ttu-id="8624e-765">In caso contrario, la forma espansa è applicabile se per ogni argomento nel `A` è identica alla modalità di passaggio del parametro corrispondente, la modalità di passaggio dell'argomento e</span><span class="sxs-lookup"><span data-stu-id="8624e-765">Otherwise, the expanded form is applicable if for each argument in `A` the parameter passing mode of the argument is identical to the parameter passing mode of the corresponding parameter, and</span></span>
   *  <span data-ttu-id="8624e-766">per un parametro di valore fisso o un parametro di valore creati dall'espansione del, una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) esistente dal tipo dell'argomento nel tipo del parametro corrispondente, o</span><span class="sxs-lookup"><span data-stu-id="8624e-766">for a fixed value parameter or a value parameter created by the expansion, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from the type of the argument to the type of the corresponding parameter, or</span></span>
   *  <span data-ttu-id="8624e-767">per un `ref` o `out` parametro, il tipo dell'argomento è identico al tipo del parametro corrispondente.</span><span class="sxs-lookup"><span data-stu-id="8624e-767">for a `ref` or `out` parameter, the type of the argument is identical to the type of the corresponding parameter.</span></span>

#### <a name="better-function-member"></a><span data-ttu-id="8624e-768">Miglior membro di funzione</span><span class="sxs-lookup"><span data-stu-id="8624e-768">Better function member</span></span>

<span data-ttu-id="8624e-769">Ai fini di determinare il miglior membro di funzione, viene costruito un elenco di argomenti ridotta contenente solo le espressioni degli argomenti in ordine che vengono visualizzati nell'elenco di argomenti originale.</span><span class="sxs-lookup"><span data-stu-id="8624e-769">For the purposes of determining the better function member, a stripped-down argument list A is constructed containing just the argument expressions themselves in the order they appear in the original argument list.</span></span>

<span data-ttu-id="8624e-770">Elenchi di parametri per ogni funzione candidato membri vengono costruiti nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="8624e-770">Parameter lists for each of the candidate function members are constructed in the following way:</span></span>

*  <span data-ttu-id="8624e-771">La forma espansa viene utilizzata se il membro di funzione è applicabile solo in forma espansa.</span><span class="sxs-lookup"><span data-stu-id="8624e-771">The expanded form is used if the function member was applicable only in the expanded form.</span></span>
*  <span data-ttu-id="8624e-772">I parametri facoltativi senza argomenti corrispondenti vengono rimossi dall'elenco di parametri</span><span class="sxs-lookup"><span data-stu-id="8624e-772">Optional parameters with no corresponding arguments are removed from the parameter list</span></span>
*  <span data-ttu-id="8624e-773">I parametri vengono riordinati in modo che vengano eseguiti nella stessa posizione come argomento corrispondente nell'elenco di argomenti.</span><span class="sxs-lookup"><span data-stu-id="8624e-773">The parameters are reordered so that they occur at the same position as the corresponding argument in the argument list.</span></span>

<span data-ttu-id="8624e-774">Dato un elenco di argomenti `A` con un set di espressioni dell'argomento `{E1, E2, ..., En}` e due membri di funzione applicabile `Mp` e `Mq` con i tipi di parametro `{P1, P2, ..., Pn}` e `{Q1, Q2, ..., Qn}`, `Mp` viene definito come un ***miglior membro di funzione*** rispetto a `Mq` se</span><span class="sxs-lookup"><span data-stu-id="8624e-774">Given an argument list `A` with a set of argument expressions `{E1, E2, ..., En}` and two applicable function members `Mp` and `Mq` with parameter types `{P1, P2, ..., Pn}` and `{Q1, Q2, ..., Qn}`, `Mp` is defined to be a ***better function member*** than `Mq` if</span></span>

*  <span data-ttu-id="8624e-775">per ogni argomento, la conversione implicita da `Ex` al `Qx` non è migliore della conversione implicita dal `Ex` a `Px`, e</span><span class="sxs-lookup"><span data-stu-id="8624e-775">for each argument, the implicit conversion from `Ex` to `Qx` is not better than the implicit conversion from `Ex` to `Px`, and</span></span>
*  <span data-ttu-id="8624e-776">per almeno un argomento, la conversione da `Ex` al `Px` è migliore della conversione da `Ex` a `Qx`.</span><span class="sxs-lookup"><span data-stu-id="8624e-776">for at least one argument, the conversion from `Ex` to `Px` is better than the conversion from `Ex` to `Qx`.</span></span>

<span data-ttu-id="8624e-777">Quando si esegue questa versione di valutazione, se `Mp` oppure `Mq` è applicabile in forma espansa, quindi `Px` o `Qx` fa riferimento a un parametro in forma espansa all'elenco di parametri.</span><span class="sxs-lookup"><span data-stu-id="8624e-777">When performing this evaluation, if `Mp` or `Mq` is applicable in its expanded form, then `Px` or `Qx` refers to a parameter in the expanded form of the parameter list.</span></span>

<span data-ttu-id="8624e-778">Nel caso in cui il parametro di tipo sequenze `{P1, P2, ..., Pn}` e `{Q1, Q2, ..., Qn}` sono equivalenti (ovvero ognuno `Pi` dispone di una conversione di identità ai corrispondenti `Qi`), vengono applicate le seguenti regole di risoluzione di conflitti, in ordine, per determinare la migliore membro di funzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-778">In case the parameter type sequences `{P1, P2, ..., Pn}` and `{Q1, Q2, ..., Qn}` are equivalent (i.e. each `Pi` has an identity conversion to the corresponding `Qi`), the following tie-breaking rules are applied, in order, to determine the better function member.</span></span>

*  <span data-ttu-id="8624e-779">Se `Mp` è un metodo non generico e `Mq` è un metodo generico, quindi `Mp` migliore `Mq`.</span><span class="sxs-lookup"><span data-stu-id="8624e-779">If `Mp` is a non-generic method and `Mq` is a generic method, then `Mp` is better than `Mq`.</span></span>
*  <span data-ttu-id="8624e-780">In caso contrario, se `Mp` è applicabile in forma normale e `Mq` ha una `params` matrice ed è applicabile solo in forma espansa, quindi `Mp` migliore `Mq`.</span><span class="sxs-lookup"><span data-stu-id="8624e-780">Otherwise, if `Mp` is applicable in its normal form and `Mq` has a `params` array and is applicable only in its expanded form, then `Mp` is better than `Mq`.</span></span>
*  <span data-ttu-id="8624e-781">In caso contrario, se `Mp` è dichiarato più parametri rispetto `Mq`, quindi `Mp` migliore `Mq`.</span><span class="sxs-lookup"><span data-stu-id="8624e-781">Otherwise, if `Mp` has more declared parameters than `Mq`, then `Mp` is better than `Mq`.</span></span> <span data-ttu-id="8624e-782">Ciò può verificarsi se entrambi i metodi presentano `params` matrici e sono applicabile solo nelle relative forme espanse.</span><span class="sxs-lookup"><span data-stu-id="8624e-782">This can occur if both methods have `params` arrays and are applicable only in their expanded forms.</span></span>
*  <span data-ttu-id="8624e-783">In caso contrario se tutti i parametri del `Mp` dispongono di un argomento corrispondente mentre gli argomenti predefiniti devono essere sostituiti per almeno un parametro facoltativo nelle `Mq` quindi `Mp` migliore `Mq`.</span><span class="sxs-lookup"><span data-stu-id="8624e-783">Otherwise if all parameters of `Mp` have a corresponding argument whereas default arguments need to be substituted for at least one optional parameter in `Mq` then `Mp` is better than `Mq`.</span></span>
*  <span data-ttu-id="8624e-784">In caso contrario, se `Mp` dispone di tipi di parametro più specifici rispetto `Mq`, quindi `Mp` migliore `Mq`.</span><span class="sxs-lookup"><span data-stu-id="8624e-784">Otherwise, if `Mp` has more specific parameter types than `Mq`, then `Mp` is better than `Mq`.</span></span> <span data-ttu-id="8624e-785">Let `{R1, R2, ..., Rn}` e `{S1, S2, ..., Sn}` rappresentano i tipi dei parametri non espanso e privo di istanze `Mp` e `Mq`.</span><span class="sxs-lookup"><span data-stu-id="8624e-785">Let `{R1, R2, ..., Rn}` and `{S1, S2, ..., Sn}` represent the uninstantiated and unexpanded parameter types of `Mp` and `Mq`.</span></span> <span data-ttu-id="8624e-786">`Mp`di tipi di parametro sono più specifici rispetto alle `Mq`del se, per ogni parametro, `Rx` non è meno specifico `Sx`e, per almeno un parametro, `Rx` più specifico `Sx`:</span><span class="sxs-lookup"><span data-stu-id="8624e-786">`Mp`'s parameter types are more specific than `Mq`'s if, for each parameter, `Rx` is not less specific than `Sx`, and, for at least one parameter, `Rx` is more specific than `Sx`:</span></span>
   *  <span data-ttu-id="8624e-787">Un parametro di tipo è meno specifico rispetto a un parametro non di tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-787">A type parameter is less specific than a non-type parameter.</span></span>
   *  <span data-ttu-id="8624e-788">In modo ricorsivo, un tipo costruito è più specifico rispetto a un altro tipo costruito (con lo stesso numero di argomenti di tipo) se almeno un argomento di tipo è più specifico e nessun argomento di tipo è meno specifico argomento di tipo corrispondente in altro.</span><span class="sxs-lookup"><span data-stu-id="8624e-788">Recursively, a constructed type is more specific than another constructed type (with the same number of type arguments) if at least one type argument is more specific and no type argument is less specific than the corresponding type argument in the other.</span></span>
   *  <span data-ttu-id="8624e-789">Un tipo di matrice è più specifico di un altro tipo di matrice (con lo stesso numero di dimensioni) se il tipo di elemento della prima riga è più specifico rispetto al tipo di elemento del secondo.</span><span class="sxs-lookup"><span data-stu-id="8624e-789">An array type is more specific than another array type (with the same number of dimensions) if the element type of the first is more specific than the element type of the second.</span></span>
*  <span data-ttu-id="8624e-790">In caso contrario, se un membro è un operatore non è elevato e l'altro è un operatore lift, quello non elevato è migliore.</span><span class="sxs-lookup"><span data-stu-id="8624e-790">Otherwise if one member is a non-lifted operator and  the other is a lifted operator, the non-lifted one is better.</span></span>
*  <span data-ttu-id="8624e-791">In caso contrario, nessuno dei due membri di funzione sono migliore.</span><span class="sxs-lookup"><span data-stu-id="8624e-791">Otherwise, neither function member is better.</span></span>

#### <a name="better-conversion-from-expression"></a><span data-ttu-id="8624e-792">Conversione migliore dall'espressione</span><span class="sxs-lookup"><span data-stu-id="8624e-792">Better conversion from expression</span></span>

<span data-ttu-id="8624e-793">Ha una conversione implicita `C1` che converte da un'espressione `E` a un tipo `T1`e una conversione implicita `C2` che consente di convertire un'espressione `E` a un tipo `T2`, `C1` è un ***migliore conversione*** rispetto `C2` se `E` corrisponde esattamente `T2` e contiene almeno uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-793">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>

* <span data-ttu-id="8624e-794">`E` corrisponde esattamente `T1` ([esattamente corrispondenti espressioni](expressions.md#exactly-matching-expression))</span><span class="sxs-lookup"><span data-stu-id="8624e-794">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
* <span data-ttu-id="8624e-795">`T1` è una destinazione di conversione migliore `T2` ([definire meglio conversione](expressions.md#better-conversion-target))</span><span class="sxs-lookup"><span data-stu-id="8624e-795">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target))</span></span>

#### <a name="exactly-matching-expression"></a><span data-ttu-id="8624e-796">Espressione esattamente corrispondente</span><span class="sxs-lookup"><span data-stu-id="8624e-796">Exactly matching Expression</span></span>

<span data-ttu-id="8624e-797">Data un'espressione `E` e un tipo `T`, `E` corrisponde esattamente `T` se una delle seguenti condizioni:</span><span class="sxs-lookup"><span data-stu-id="8624e-797">Given an expression `E` and a type `T`, `E` exactly matches `T` if one of the following holds:</span></span>

*  <span data-ttu-id="8624e-798">`E` dispone di un tipo `S`, e non esiste una conversione di identità da `S` a `T`</span><span class="sxs-lookup"><span data-stu-id="8624e-798">`E` has a type `S`, and an identity conversion exists from `S` to `T`</span></span>
*  <span data-ttu-id="8624e-799">`E` funzione anonima `T` è un tipo di delegato `D` o un tipo di albero delle espressioni `Expression<D>` e una delle seguenti condizioni:</span><span class="sxs-lookup"><span data-stu-id="8624e-799">`E` is an anonymous function, `T` is either a delegate type `D` or an expression tree type `Expression<D>` and one of the following holds:</span></span>
   *  <span data-ttu-id="8624e-800">Un tipo restituito dedotto `X` esiste per `E` nel contesto dell'elenco dei parametri `D` ([membro derivato il tipo restituito](expressions.md#inferred-return-type)), ed esiste una conversione di identità da `X` per il tipo restituito di `D`</span><span class="sxs-lookup"><span data-stu-id="8624e-800">An inferred return type `X` exists for `E` in the context of the parameter list of `D` ([Inferred return type](expressions.md#inferred-return-type)), and an identity conversion exists from `X` to the return type of `D`</span></span>
   *  <span data-ttu-id="8624e-801">Entrambi `E` è non asincrone e `D` ha un tipo restituito `Y` oppure `E` è asincrono e `D` presenta un tipo restituito `Task<Y>`, e una delle seguenti condizioni:</span><span class="sxs-lookup"><span data-stu-id="8624e-801">Either `E` is non-async and `D` has a return type `Y` or `E` is async and `D` has a return type `Task<Y>`, and one of the following holds:</span></span>
      * <span data-ttu-id="8624e-802">Il corpo di `E` è un'espressione che corrisponde esattamente `Y`</span><span class="sxs-lookup"><span data-stu-id="8624e-802">The body of `E` is an expression that exactly matches `Y`</span></span>
      * <span data-ttu-id="8624e-803">Il corpo di `E` è un blocco di istruzioni in cui ogni istruzione return restituisce un'espressione che corrisponde esattamente `Y`</span><span class="sxs-lookup"><span data-stu-id="8624e-803">The body of `E` is a statement block where every return statement returns an expression that exactly matches `Y`</span></span>

#### <a name="better-conversion-target"></a><span data-ttu-id="8624e-804">Destinazione di conversione migliore</span><span class="sxs-lookup"><span data-stu-id="8624e-804">Better conversion target</span></span>

<span data-ttu-id="8624e-805">Ha due tipi diversi `T1` e `T2`, `T1` è una destinazione di conversione migliore `T2` se nessuna conversione implicita dai `T2` a `T1` esiste, e contiene almeno uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-805">Given two different types `T1` and `T2`, `T1` is a better conversion target than `T2` if no implicit conversion from `T2` to `T1` exists, and at least one of the following holds:</span></span>

*  <span data-ttu-id="8624e-806">Una conversione implicita da `T1` a `T2` esiste</span><span class="sxs-lookup"><span data-stu-id="8624e-806">An implicit conversion from `T1` to `T2` exists</span></span>
*  <span data-ttu-id="8624e-807">`T1` è un tipo delegato `D1` o un tipo di albero delle espressioni `Expression<D1>`, `T2` è un tipo delegato `D2` o un tipo di albero delle espressioni `Expression<D2>`, `D1` presenta un tipo restituito `S1` e una del blocchi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-807">`T1` is either a delegate type `D1` or an expression tree type `Expression<D1>`, `T2` is either a delegate type `D2` or an expression tree type `Expression<D2>`, `D1` has a return type `S1` and one of the following holds:</span></span>
   * <span data-ttu-id="8624e-808">`D2` restituisce void</span><span class="sxs-lookup"><span data-stu-id="8624e-808">`D2` is void returning</span></span>
   * <span data-ttu-id="8624e-809">`D2` ha un tipo restituito `S2`, e `S1` è una destinazione di conversione migliore rispetto a `S2`</span><span class="sxs-lookup"><span data-stu-id="8624e-809">`D2` has a return type `S2`, and `S1` is a better conversion target than `S2`</span></span>
*  <span data-ttu-id="8624e-810">`T1` viene `Task<S1>`, `T2` viene `Task<S2>`, e `S1` è una destinazione di conversione migliore rispetto a `S2`</span><span class="sxs-lookup"><span data-stu-id="8624e-810">`T1` is `Task<S1>`, `T2` is `Task<S2>`, and `S1` is a better conversion target than `S2`</span></span>
*  <span data-ttu-id="8624e-811">`T1` viene `S1` o `S1?` in cui `S1` è un tipo integrale con segno, e `T2` viene `S2` oppure `S2?` dove `S2` è un tipo integrale senza segno.</span><span class="sxs-lookup"><span data-stu-id="8624e-811">`T1` is `S1` or `S1?` where `S1` is a signed integral type, and `T2` is `S2` or `S2?` where `S2` is an unsigned integral type.</span></span> <span data-ttu-id="8624e-812">In particolare:</span><span class="sxs-lookup"><span data-stu-id="8624e-812">Specifically:</span></span>
   * <span data-ttu-id="8624e-813">`S1` viene `sbyte` e `S2` viene `byte`, `ushort`, `uint`, o `ulong`</span><span class="sxs-lookup"><span data-stu-id="8624e-813">`S1` is `sbyte` and `S2` is `byte`, `ushort`, `uint`, or `ulong`</span></span>
   * <span data-ttu-id="8624e-814">`S1` viene `short` e `S2` viene `ushort`, `uint`, o `ulong`</span><span class="sxs-lookup"><span data-stu-id="8624e-814">`S1` is `short` and `S2` is `ushort`, `uint`, or `ulong`</span></span>
   * <span data-ttu-id="8624e-815">`S1` viene `int` e `S2` è `uint`, o `ulong`</span><span class="sxs-lookup"><span data-stu-id="8624e-815">`S1` is `int` and `S2` is `uint`, or `ulong`</span></span>
   * <span data-ttu-id="8624e-816">`S1` viene `long` e `S2` è `ulong`</span><span class="sxs-lookup"><span data-stu-id="8624e-816">`S1` is `long` and `S2` is `ulong`</span></span>

#### <a name="overloading-in-generic-classes"></a><span data-ttu-id="8624e-817">L'overload in classi generiche</span><span class="sxs-lookup"><span data-stu-id="8624e-817">Overloading in generic classes</span></span>

<span data-ttu-id="8624e-818">Anche se le firme dichiarato devono essere univoche, è possibile che la sostituzione degli argomenti di tipo comporta firme identiche.</span><span class="sxs-lookup"><span data-stu-id="8624e-818">While signatures as declared must be unique, it is possible that substitution of type arguments results in identical signatures.</span></span> <span data-ttu-id="8624e-819">In questi casi, le regole di risoluzione di conflitti di risoluzione dell'overload precedente selezionerà il membro più specifico.</span><span class="sxs-lookup"><span data-stu-id="8624e-819">In such cases, the tie-breaking rules of overload resolution above will pick the most specific member.</span></span>

<span data-ttu-id="8624e-820">Gli esempi seguenti illustrano gli overload che siano valide e non è valido in base a questa regola:</span><span class="sxs-lookup"><span data-stu-id="8624e-820">The following examples show overloads that are valid and invalid according to this rule:</span></span>

```csharp
interface I1<T> {...}

interface I2<T> {...}

class G1<U>
{
    int F1(U u);                  // Overload resolution for G<int>.F1
    int F1(int i);                // will pick non-generic

    void F2(I1<U> a);             // Valid overload
    void F2(I2<U> a);
}

class G2<U,V>
{
    void F3(U u, V v);            // Valid, but overload resolution for
    void F3(V v, U u);            // G2<int,int>.F3 will fail

    void F4(U u, I1<V> v);        // Valid, but overload resolution for    
    void F4(I1<V> v, U u);        // G2<I1<int>,int>.F4 will fail

    void F5(U u1, I1<V> v2);      // Valid overload
    void F5(V v1, U u2);

    void F6(ref U u);             // valid overload
    void F6(out V v);
}
```

### <a name="compile-time-checking-of-dynamic-overload-resolution"></a><span data-ttu-id="8624e-821">Controllo della risoluzione dell'overload dinamico in fase di compilazione</span><span class="sxs-lookup"><span data-stu-id="8624e-821">Compile-time checking of dynamic overload resolution</span></span>

<span data-ttu-id="8624e-822">Per le operazioni associate più in modo dinamico il set di possibili candidati per la risoluzione è sconosciuto al momento della compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-822">For most dynamically bound operations the set of possible candidates for resolution is unknown at compile-time.</span></span> <span data-ttu-id="8624e-823">In alcuni casi, tuttavia il set di candidati è noto in fase di compilazione:</span><span class="sxs-lookup"><span data-stu-id="8624e-823">In certain cases, however the candidate set is known at compile-time:</span></span>

*  <span data-ttu-id="8624e-824">Chiamate di metodo statico con argomenti dinamici</span><span class="sxs-lookup"><span data-stu-id="8624e-824">Static method calls with dynamic arguments</span></span>
*  <span data-ttu-id="8624e-825">Chiamate ai metodi di istanza in cui il destinatario non è un'espressione dinamica</span><span class="sxs-lookup"><span data-stu-id="8624e-825">Instance method calls where the receiver is not a dynamic expression</span></span>
*  <span data-ttu-id="8624e-826">Chiamate di un indicizzatore in cui il destinatario non è un'espressione dinamica</span><span class="sxs-lookup"><span data-stu-id="8624e-826">Indexer calls where the receiver is not a dynamic expression</span></span>
*  <span data-ttu-id="8624e-827">Chiamate al costruttore con argomenti dinamici</span><span class="sxs-lookup"><span data-stu-id="8624e-827">Constructor calls with dynamic arguments</span></span>

<span data-ttu-id="8624e-828">In questi casi viene eseguita una verifica limitata di in fase di compilazione per ciascun candidato verificare se uno di essi probabilmente è stato possibile applicare in fase di esecuzione. Questo controllo è costituito dai passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-828">In these cases a limited compile-time check is performed for each candidate to see if any of them could possibly apply at run-time.This check consists of the following steps:</span></span>

*  <span data-ttu-id="8624e-829">Inferenza del tipo parziale: Qualsiasi tipo di argomento che non dipendono direttamente o indirettamente un argomento di tipo `dynamic` viene inferito usando le regole delle [inferenza del tipo](expressions.md#type-inference).</span><span class="sxs-lookup"><span data-stu-id="8624e-829">Partial type inference: Any type argument that does not depend directly or indirectly on an argument of type `dynamic` is inferred using the rules of [Type inference](expressions.md#type-inference).</span></span> <span data-ttu-id="8624e-830">I restanti argomenti di tipo sono sconosciuti.</span><span class="sxs-lookup"><span data-stu-id="8624e-830">The remaining type arguments are unknown.</span></span>
*  <span data-ttu-id="8624e-831">Errore controllo applicabilità parziale: Applicabilità viene controllato in base alla [membro di funzione applicabile](expressions.md#applicable-function-member), ma verranno ignorati i parametri i cui tipi sono sconosciuti.</span><span class="sxs-lookup"><span data-stu-id="8624e-831">Partial applicability check: Applicability is checked according to [Applicable function member](expressions.md#applicable-function-member), but ignoring parameters whose types are unknown.</span></span>
*  <span data-ttu-id="8624e-832">Se nessun candidato supera questo test, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-832">If no candidate passes this test, a compile-time error occurs.</span></span>

### <a name="function-member-invocation"></a><span data-ttu-id="8624e-833">Chiamata di funzione membro</span><span class="sxs-lookup"><span data-stu-id="8624e-833">Function member invocation</span></span>

<span data-ttu-id="8624e-834">In questa sezione descrive il processo che ha luogo in fase di esecuzione per richiamare un membro di funzione specifica.</span><span class="sxs-lookup"><span data-stu-id="8624e-834">This section describes the process that takes place at run-time to invoke a particular function member.</span></span> <span data-ttu-id="8624e-835">Si presuppone che un processo in fase di associazione già ha determinato il membro da richiamare, possibilmente applicando la risoluzione dell'overload a un set di membri di funzione candidati.</span><span class="sxs-lookup"><span data-stu-id="8624e-835">It is assumed that a binding-time process has already determined the particular member to invoke, possibly by applying overload resolution to a set of candidate function members.</span></span>

<span data-ttu-id="8624e-836">Ai fini della descrizione del processo di chiamata, i membri di funzione sono suddivisi in due categorie:</span><span class="sxs-lookup"><span data-stu-id="8624e-836">For purposes of describing the invocation process, function members are divided into two categories:</span></span>

*  <span data-ttu-id="8624e-837">Membri di funzione statica.</span><span class="sxs-lookup"><span data-stu-id="8624e-837">Static function members.</span></span> <span data-ttu-id="8624e-838">Questi sono costruttori di istanze, i metodi statici, funzioni di accesso a proprietà statiche e operatori definiti dall'utente.</span><span class="sxs-lookup"><span data-stu-id="8624e-838">These are instance constructors, static methods, static property accessors, and user-defined operators.</span></span> <span data-ttu-id="8624e-839">I membri di funzione statica sono sempre non virtuale.</span><span class="sxs-lookup"><span data-stu-id="8624e-839">Static function members are always non-virtual.</span></span>
*  <span data-ttu-id="8624e-840">Membri di funzione di istanza.</span><span class="sxs-lookup"><span data-stu-id="8624e-840">Instance function members.</span></span> <span data-ttu-id="8624e-841">Questi sono metodi di istanza, funzioni di accesso alle proprietà e indicizzatori.</span><span class="sxs-lookup"><span data-stu-id="8624e-841">These are instance methods, instance property accessors, and indexer accessors.</span></span> <span data-ttu-id="8624e-842">I membri di funzione di istanza sono non virtuali o virtuale e sono sempre richiamati in una determinata istanza.</span><span class="sxs-lookup"><span data-stu-id="8624e-842">Instance function members are either non-virtual or virtual, and are always invoked on a particular instance.</span></span> <span data-ttu-id="8624e-843">L'istanza viene calcolato da un'espressione dell'istanza e sia accessibile all'interno del membro di funzione come `this` ([questo accesso](expressions.md#this-access)).</span><span class="sxs-lookup"><span data-stu-id="8624e-843">The instance is computed by an instance expression, and it becomes accessible within the function member as `this` ([This access](expressions.md#this-access)).</span></span>

<span data-ttu-id="8624e-844">L'elaborazione in fase di esecuzione di una chiamata alla funzione membro prevede i passaggi seguenti, dove `M` è il membro di funzione e, se `M` è un membro di istanza, `E` è l'espressione dell'istanza:</span><span class="sxs-lookup"><span data-stu-id="8624e-844">The run-time processing of a function member invocation consists of the following steps, where `M` is the function member and, if `M` is an instance member, `E` is the instance expression:</span></span>

*  <span data-ttu-id="8624e-845">Se `M` è un membro di funzione statica:</span><span class="sxs-lookup"><span data-stu-id="8624e-845">If `M` is a static function member:</span></span>
   * <span data-ttu-id="8624e-846">L'elenco di argomenti viene valutata come descritto in [elenchi di argomenti](expressions.md#argument-lists).</span><span class="sxs-lookup"><span data-stu-id="8624e-846">The argument list is evaluated as described in [Argument lists](expressions.md#argument-lists).</span></span>
   * <span data-ttu-id="8624e-847">`M` viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="8624e-847">`M` is invoked.</span></span>

*  <span data-ttu-id="8624e-848">Se `M` viene dichiarato un membro di funzione di istanza un *value_type*:</span><span class="sxs-lookup"><span data-stu-id="8624e-848">If `M` is an instance function member declared in a *value_type*:</span></span>
   * <span data-ttu-id="8624e-849">`E` viene valutato.</span><span class="sxs-lookup"><span data-stu-id="8624e-849">`E` is evaluated.</span></span> <span data-ttu-id="8624e-850">Se questa versione di valutazione genera un'eccezione, non viene eseguiti alcun ulteriore passaggio.</span><span class="sxs-lookup"><span data-stu-id="8624e-850">If this evaluation causes an exception, then no further steps are executed.</span></span>
   * <span data-ttu-id="8624e-851">Se `E` non è classificato come una variabile e quindi una variabile locale temporanea dei `E`del tipo viene creato e il valore di `E` viene assegnato a tale variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-851">If `E` is not classified as a variable, then a temporary local variable of `E`'s type is created and the value of `E` is assigned to that variable.</span></span> <span data-ttu-id="8624e-852">`E` è quindi riclassificato come un riferimento alla variabile locale temporanea.</span><span class="sxs-lookup"><span data-stu-id="8624e-852">`E` is then reclassified as a reference to that temporary local variable.</span></span> <span data-ttu-id="8624e-853">Variabile temporanea è accessibile come `this` all'interno di `M`, ma non in altri modi.</span><span class="sxs-lookup"><span data-stu-id="8624e-853">The temporary variable is accessible as `this` within `M`, but not in any other way.</span></span> <span data-ttu-id="8624e-854">In questo modo, solo quando `E` è una variabile true è possibile che il chiamante deve osservare le modifiche che `M` apporta al `this`.</span><span class="sxs-lookup"><span data-stu-id="8624e-854">Thus, only when `E` is a true variable is it possible for the caller to observe the changes that `M` makes to `this`.</span></span>
   * <span data-ttu-id="8624e-855">L'elenco di argomenti viene valutata come descritto in [elenchi di argomenti](expressions.md#argument-lists).</span><span class="sxs-lookup"><span data-stu-id="8624e-855">The argument list is evaluated as described in [Argument lists](expressions.md#argument-lists).</span></span>
   * <span data-ttu-id="8624e-856">`M` viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="8624e-856">`M` is invoked.</span></span> <span data-ttu-id="8624e-857">La variabile fa riferimento `E` diventa la variabile fa riferimento `this`.</span><span class="sxs-lookup"><span data-stu-id="8624e-857">The variable referenced by `E` becomes the variable referenced by `this`.</span></span>

*  <span data-ttu-id="8624e-858">Se `M` viene dichiarato un membro di funzione di istanza un *reference_type*:</span><span class="sxs-lookup"><span data-stu-id="8624e-858">If `M` is an instance function member declared in a *reference_type*:</span></span>
   * <span data-ttu-id="8624e-859">`E` viene valutato.</span><span class="sxs-lookup"><span data-stu-id="8624e-859">`E` is evaluated.</span></span> <span data-ttu-id="8624e-860">Se questa versione di valutazione genera un'eccezione, non viene eseguiti alcun ulteriore passaggio.</span><span class="sxs-lookup"><span data-stu-id="8624e-860">If this evaluation causes an exception, then no further steps are executed.</span></span>
   * <span data-ttu-id="8624e-861">L'elenco di argomenti viene valutata come descritto in [elenchi di argomenti](expressions.md#argument-lists).</span><span class="sxs-lookup"><span data-stu-id="8624e-861">The argument list is evaluated as described in [Argument lists](expressions.md#argument-lists).</span></span>
   * <span data-ttu-id="8624e-862">Se il tipo di `E` è un *value_type*, una conversione boxing ([conversioni Boxing](types.md#boxing-conversions)) viene eseguita per convertire `E` digitare `object`, e `E` viene considerato per essere di tipo `object` nei passaggi seguenti.</span><span class="sxs-lookup"><span data-stu-id="8624e-862">If the type of `E` is a *value_type*, a boxing conversion ([Boxing conversions](types.md#boxing-conversions)) is performed to convert `E` to type `object`, and `E` is considered to be of type `object` in the following steps.</span></span> <span data-ttu-id="8624e-863">In questo caso `M` può essere solo un membro di `System.Object`.</span><span class="sxs-lookup"><span data-stu-id="8624e-863">In this case, `M` could only be a member of `System.Object`.</span></span>
   * <span data-ttu-id="8624e-864">Il valore di `E` viene verificata la validità.</span><span class="sxs-lookup"><span data-stu-id="8624e-864">The value of `E` is checked to be valid.</span></span> <span data-ttu-id="8624e-865">Se il valore di `E` viene `null`, un `System.NullReferenceException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.</span><span class="sxs-lookup"><span data-stu-id="8624e-865">If the value of `E` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
   * <span data-ttu-id="8624e-866">Viene determinato l'implementazione della funzione membro da richiamare:</span><span class="sxs-lookup"><span data-stu-id="8624e-866">The function member implementation to invoke is determined:</span></span>
     * <span data-ttu-id="8624e-867">Se la fase di associazione tipo di `E` è un'interfaccia, il membro di funzione da richiamare è l'implementazione di `M` fornita dal tipo di runtime dell'istanza a cui fanno riferimento `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-867">If the binding-time type of `E` is an interface, the function member to invoke is the implementation of `M` provided by the run-time type of the instance referenced by `E`.</span></span> <span data-ttu-id="8624e-868">Questo membro di funzione viene determinato applicando le regole di mapping di interfaccia ([mapping dell'interfaccia](interfaces.md#interface-mapping)) per determinare l'implementazione di `M` forniti dal tipo in fase di esecuzione dell'istanza a cui fanno riferimento `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-868">This function member is determined by applying the interface mapping rules ([Interface mapping](interfaces.md#interface-mapping)) to determine the implementation of `M` provided by the run-time type of the instance referenced by `E`.</span></span>
     * <span data-ttu-id="8624e-869">In caso contrario, se `M` è un membro di funzione virtuale, il membro di funzione da richiamare è l'implementazione di `M` fornita dal tipo di runtime dell'istanza a cui fanno riferimento `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-869">Otherwise, if `M` is a virtual function member, the function member to invoke is the implementation of `M` provided by the run-time type of the instance referenced by `E`.</span></span> <span data-ttu-id="8624e-870">Questo membro di funzione viene determinato applicando le regole per determinare l'implementazione più derivato ([metodi virtuali](classes.md#virtual-methods)) della `M` rispetto al tipo di runtime dell'istanza a cui fanno riferimento `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-870">This function member is determined by applying the rules for determining the most derived implementation ([Virtual methods](classes.md#virtual-methods)) of `M` with respect to the run-time type of the instance referenced by `E`.</span></span>
     * <span data-ttu-id="8624e-871">In caso contrario, `M` è un membro di funzione non virtuale e il membro di funzione da richiamare è `M` stesso.</span><span class="sxs-lookup"><span data-stu-id="8624e-871">Otherwise, `M` is a non-virtual function member, and the function member to invoke is `M` itself.</span></span>
   * <span data-ttu-id="8624e-872">Viene richiamato l'implementazione della funzione membro definito nel passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="8624e-872">The function member implementation determined in the step above is invoked.</span></span> <span data-ttu-id="8624e-873">L'oggetto fa riferimento `E` diventa l'oggetto a cui fanno riferimento `this`.</span><span class="sxs-lookup"><span data-stu-id="8624e-873">The object referenced by `E` becomes the object referenced by `this`.</span></span>

#### <a name="invocations-on-boxed-instances"></a><span data-ttu-id="8624e-874">Chiamate in istanze sottoposto a conversione boxing</span><span class="sxs-lookup"><span data-stu-id="8624e-874">Invocations on boxed instances</span></span>

<span data-ttu-id="8624e-875">Un membro di funzione implementata in una *value_type* può essere richiamato tramite un'istanza di sottoposto a conversione boxing di tale *value_type* nelle situazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-875">A function member implemented in a *value_type* can be invoked through a boxed instance of that *value_type* in the following situations:</span></span>

*  <span data-ttu-id="8624e-876">Quando il membro di funzione è un `override` di un metodo ereditato dal tipo `object` e viene richiamato tramite un'espressione dell'istanza del tipo `object`.</span><span class="sxs-lookup"><span data-stu-id="8624e-876">When the function member is an `override` of a method inherited from type `object` and is invoked through an instance expression of type `object`.</span></span>
*  <span data-ttu-id="8624e-877">Quando il membro di funzione è un'implementazione di un membro di funzione di interfaccia e viene richiamato tramite un'espressione dell'istanza di un *interface_type*.</span><span class="sxs-lookup"><span data-stu-id="8624e-877">When the function member is an implementation of an interface function member and is invoked through an instance expression of an *interface_type*.</span></span>
*  <span data-ttu-id="8624e-878">Quando tramite un delegato viene richiamato il membro di funzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-878">When the function member is invoked through a delegate.</span></span>

<span data-ttu-id="8624e-879">In queste situazioni, l'istanza sottoposto a conversione boxing viene considerata per contenere una variabile del *value_type*, e questa variabile diventa la variabile fa riferimento `this` entro la chiamata alla funzione membro.</span><span class="sxs-lookup"><span data-stu-id="8624e-879">In these situations, the boxed instance is considered to contain a variable of the *value_type*, and this variable becomes the variable referenced by `this` within the function member invocation.</span></span> <span data-ttu-id="8624e-880">In particolare, ciò significa che quando viene richiamato un membro di funzione in un'istanza sottoposto a conversione boxing, è possibile che il membro di funzione modificare il valore contenuto nell'istanza.</span><span class="sxs-lookup"><span data-stu-id="8624e-880">In particular, this means that when a function member is invoked on a boxed instance, it is possible for the function member to modify the value contained in the boxed instance.</span></span>

## <a name="primary-expressions"></a><span data-ttu-id="8624e-881">Espressioni principali:</span><span class="sxs-lookup"><span data-stu-id="8624e-881">Primary expressions</span></span>

<span data-ttu-id="8624e-882">Espressioni primarie includono i moduli più semplici delle espressioni.</span><span class="sxs-lookup"><span data-stu-id="8624e-882">Primary expressions include the simplest forms of expressions.</span></span>

```antlr
primary_expression
    : primary_no_array_creation_expression
    | array_creation_expression
    ;

primary_no_array_creation_expression
    : literal
    | interpolated_string_expression
    | simple_name
    | parenthesized_expression
    | member_access
    | invocation_expression
    | element_access
    | this_access
    | base_access
    | post_increment_expression
    | post_decrement_expression
    | object_creation_expression
    | delegate_creation_expression
    | anonymous_object_creation_expression
    | typeof_expression
    | checked_expression
    | unchecked_expression
    | default_value_expression
    | nameof_expression
    | anonymous_method_expression
    | primary_no_array_creation_expression_unsafe
    ;
```

<span data-ttu-id="8624e-883">Espressioni primarie sono divise tra *array_creation_expression*s e *primary_no_array_creation_expression*s.</span><span class="sxs-lookup"><span data-stu-id="8624e-883">Primary expressions are divided between *array_creation_expression*s and *primary_no_array_creation_expression*s.</span></span> <span data-ttu-id="8624e-884">Considerando l'espressione di creazione matrici in questo modo, invece di elenco, con le altre forme espressione semplice, abilita la grammatica da non consentire, ad esempio di codice potenzialmente poco chiaro</span><span class="sxs-lookup"><span data-stu-id="8624e-884">Treating array-creation-expression in this way, rather than listing it along with the other simple expression forms, enables the grammar to disallow potentially confusing code such as</span></span>
```csharp
object o = new int[3][1];
```
<span data-ttu-id="8624e-885">in caso contrario, che verrebbe interpretato come</span><span class="sxs-lookup"><span data-stu-id="8624e-885">which would otherwise be interpreted as</span></span>
```csharp
object o = (new int[3])[1];
```

### <a name="literals"></a><span data-ttu-id="8624e-886">Valori letterali</span><span class="sxs-lookup"><span data-stu-id="8624e-886">Literals</span></span>

<span data-ttu-id="8624e-887">Oggetto *primary_expression* costituito da un *letterale* ([valori letterali](lexical-structure.md#literals)) è classificato come un valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-887">A *primary_expression* that consists of a *literal* ([Literals](lexical-structure.md#literals)) is classified as a value.</span></span>


### <a name="interpolated-strings"></a><span data-ttu-id="8624e-888">Stringhe interpolate</span><span class="sxs-lookup"><span data-stu-id="8624e-888">Interpolated strings</span></span>

<span data-ttu-id="8624e-889">Un' *interpolated_string_expression* costituito da un `$` segno seguito da un normale o verbatim valore letterale stringa, in cui aree libere, delimitate da `{` e `}`, racchiudere le espressioni e formattazione specifiche.</span><span class="sxs-lookup"><span data-stu-id="8624e-889">An *interpolated_string_expression* consists of a `$` sign followed by a regular or verbatim string literal, wherein holes, delimited by `{` and `}`, enclose expressions and formatting specifications.</span></span> <span data-ttu-id="8624e-890">Un'espressione di stringa interpolata è il risultato di un' *interpolated_string_literal* che è stato suddiviso in singoli token, come descritto in [i valori letterali stringa interpolata](lexical-structure.md#interpolated-string-literals).</span><span class="sxs-lookup"><span data-stu-id="8624e-890">An interpolated string expression is the result of an *interpolated_string_literal* that has been broken up into individual tokens, as described in [Interpolated string literals](lexical-structure.md#interpolated-string-literals).</span></span>

```antlr
interpolated_string_expression
    : '$' interpolated_regular_string
    | '$' interpolated_verbatim_string
    ;

interpolated_regular_string
    : interpolated_regular_string_whole
    | interpolated_regular_string_start interpolated_regular_string_body interpolated_regular_string_end
    ;

interpolated_regular_string_body
    : interpolation (interpolated_regular_string_mid interpolation)*
    ;

interpolation
    : expression
    | expression ',' constant_expression
    ;

interpolated_verbatim_string
    : interpolated_verbatim_string_whole
    | interpolated_verbatim_string_start interpolated_verbatim_string_body interpolated_verbatim_string_end
    ;

interpolated_verbatim_string_body
    : interpolation (interpolated_verbatim_string_mid interpolation)+
    ;
```

<span data-ttu-id="8624e-891">Il *constant_expression* in un'interpolazione deve avere una conversione implicita a `int`.</span><span class="sxs-lookup"><span data-stu-id="8624e-891">The *constant_expression* in an interpolation must have an implicit conversion to `int`.</span></span>

<span data-ttu-id="8624e-892">Un' *interpolated_string_expression* viene classificata come un valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-892">An *interpolated_string_expression* is classified as a value.</span></span> <span data-ttu-id="8624e-893">Se viene immediatamente convertito `System.IFormattable` o `System.FormattableString` con una conversione implicita di stringa interpolata ([implicite interpolate conversioni di stringhe](conversions.md#implicit-interpolated-string-conversions)), l'espressione di stringa interpolata con tale tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-893">If it is immediately converted to `System.IFormattable` or `System.FormattableString` with an implicit interpolated string conversion ([Implicit interpolated string conversions](conversions.md#implicit-interpolated-string-conversions)), the interpolated string expression has that type.</span></span> <span data-ttu-id="8624e-894">In caso contrario, dispone del tipo `string`.</span><span class="sxs-lookup"><span data-stu-id="8624e-894">Otherwise, it has the type `string`.</span></span>

<span data-ttu-id="8624e-895">Se il tipo di una stringa interpolata `System.IFormattable` oppure `System.FormattableString`, il significato è una chiamata a `System.Runtime.CompilerServices.FormattableStringFactory.Create`.</span><span class="sxs-lookup"><span data-stu-id="8624e-895">If the type of an interpolated string is `System.IFormattable` or `System.FormattableString`, the meaning is a call to `System.Runtime.CompilerServices.FormattableStringFactory.Create`.</span></span> <span data-ttu-id="8624e-896">Se il tipo è `string`, il significato dell'espressione è una chiamata a `string.Format`.</span><span class="sxs-lookup"><span data-stu-id="8624e-896">If the type is `string`, the meaning of the expression is a call to `string.Format`.</span></span> <span data-ttu-id="8624e-897">In entrambi i casi, l'elenco di argomenti della chiamata è costituito da una stringa di formato letterale con segnaposto per ogni interpolazione e un argomento per ogni espressione corrispondente per i segnaposto.</span><span class="sxs-lookup"><span data-stu-id="8624e-897">In both cases, the argument list of the call consists of a format string literal with placeholders for each interpolation, and an argument for each expression corresponding to the place holders.</span></span>

<span data-ttu-id="8624e-898">Il valore letterale stringa di formato viene costruita come indicato di seguito, dove `N` è il numero di interpolazione nel *interpolated_string_expression*:</span><span class="sxs-lookup"><span data-stu-id="8624e-898">The format string literal is constructed as follows, where `N` is the number of interpolations in the *interpolated_string_expression*:</span></span>

*  <span data-ttu-id="8624e-899">Se un' *interpolated_regular_string_whole* o un' *interpolated_verbatim_string_whole* segue il `$` accede, il valore letterale stringa di formato è tale token.</span><span class="sxs-lookup"><span data-stu-id="8624e-899">If an *interpolated_regular_string_whole* or an *interpolated_verbatim_string_whole* follows the `$` sign, then the format string literal is that token.</span></span>
*  <span data-ttu-id="8624e-900">In caso contrario, il valore letterale stringa di formato costituito da:</span><span class="sxs-lookup"><span data-stu-id="8624e-900">Otherwise, the format string literal consists of:</span></span> 
   *  <span data-ttu-id="8624e-901">Prima di *interpolated_regular_string_start* o *interpolated_verbatim_string_start*</span><span class="sxs-lookup"><span data-stu-id="8624e-901">First the *interpolated_regular_string_start* or *interpolated_verbatim_string_start*</span></span>
   *  <span data-ttu-id="8624e-902">Quindi per ogni numero `I` dal `0` a `N-1`:</span><span class="sxs-lookup"><span data-stu-id="8624e-902">Then for each number `I` from `0` to `N-1`:</span></span> 
      * <span data-ttu-id="8624e-903">La rappresentazione decimale di `I`</span><span class="sxs-lookup"><span data-stu-id="8624e-903">The decimal representation of `I`</span></span>
      * <span data-ttu-id="8624e-904">Quindi, se corrispondente *interpolazione* ha una *constant_expression*, una `,` (virgola) seguito dalla rappresentazione di decimale del valore della *constant_expression*</span><span class="sxs-lookup"><span data-stu-id="8624e-904">Then, if the corresponding *interpolation* has a *constant_expression*, a `,` (comma) followed by the decimal representation of the value of the *constant_expression*</span></span>
      * <span data-ttu-id="8624e-905">L'oggetto *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_mid* o *interpolated_ verbatim_string_end* subito dopo l'interpolazione corrispondente.</span><span class="sxs-lookup"><span data-stu-id="8624e-905">Then the *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_mid* or *interpolated_verbatim_string_end* immediately following the corresponding interpolation.</span></span>

<span data-ttu-id="8624e-906">Gli argomenti successivi sono semplicemente il *espressioni* dalle *interpolazioni* (se presente), in ordine.</span><span class="sxs-lookup"><span data-stu-id="8624e-906">The subsequent arguments are simply the *expressions* from the *interpolations* (if any), in order.</span></span>

<span data-ttu-id="8624e-907">TODO: esempi.</span><span class="sxs-lookup"><span data-stu-id="8624e-907">TODO: examples.</span></span>


### <a name="simple-names"></a><span data-ttu-id="8624e-908">Nomi semplici</span><span class="sxs-lookup"><span data-stu-id="8624e-908">Simple names</span></span>

<span data-ttu-id="8624e-909">Oggetto *simple_name* è costituito da un identificatore, seguito facoltativamente da un elenco di argomenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-909">A *simple_name* consists of an identifier, optionally followed by a type argument list:</span></span>

```antlr
simple_name
    : identifier type_argument_list?
    ;
```

<span data-ttu-id="8624e-910">Oggetto *simple_name* può essere nel formato `I` o del form `I<A1,...,Ak>`, dove `I` è un identificatore singolo e `<A1,...,Ak>` è una funzione facoltativa *type_argument_list*.</span><span class="sxs-lookup"><span data-stu-id="8624e-910">A *simple_name* is either of the form `I` or of the form `I<A1,...,Ak>`, where `I` is a single identifier and `<A1,...,Ak>` is an optional *type_argument_list*.</span></span> <span data-ttu-id="8624e-911">Se non si specifica *type_argument_list* è specificato, prendere in considerazione `K` sia pari a zero.</span><span class="sxs-lookup"><span data-stu-id="8624e-911">When no *type_argument_list* is specified, consider `K` to be zero.</span></span> <span data-ttu-id="8624e-912">Il *simple_name* viene valutato e classificato come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-912">The *simple_name* is evaluated and classified as follows:</span></span>

*  <span data-ttu-id="8624e-913">Se `K` è uguale a zero e il *simple_name* viene visualizzato all'interno di una *blocco* e, se il *blocco*del (o un tipo di inclusione *blocco*del) locale lo spazio di dichiarazione di variabile ([dichiarazioni](basic-concepts.md#declarations)) contiene una variabile locale, un parametro o una costante con nome `I`, quindi il *simple_name* fa riferimento alla variabile locale, parametro o una costante ed è classificato come una variabile o un valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-913">If `K` is zero and the *simple_name* appears within a *block* and if the *block*'s (or an enclosing *block*'s) local variable declaration space ([Declarations](basic-concepts.md#declarations)) contains a local variable, parameter or constant with name `I`, then the *simple_name* refers to that local variable, parameter or constant and is classified as a variable or value.</span></span>
*  <span data-ttu-id="8624e-914">Se `K` è uguale a zero e il *simple_name* viene visualizzato all'interno del corpo di una dichiarazione di metodo generico e se tale dichiarazione include un parametro di tipo con nome `I`, quindi il *simple_name*fa riferimento a tale parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-914">If `K` is zero and the *simple_name* appears within the body of a generic method declaration and if that declaration includes a type parameter with name `I`, then the *simple_name* refers to that type parameter.</span></span>
*  <span data-ttu-id="8624e-915">In caso contrario, per ogni tipo di istanza `T` ([del tipo di istanza](classes.md#the-instance-type)), a partire dal tipo di istanza della dichiarazione del tipo che lo contiene e continuare con il tipo di istanza di ogni classe o struct che lo contiene dichiarazione (se presente):</span><span class="sxs-lookup"><span data-stu-id="8624e-915">Otherwise, for each instance type `T` ([The instance type](classes.md#the-instance-type)), starting with the instance type of the immediately enclosing type declaration and continuing with the instance type of each enclosing class or struct declaration (if any):</span></span>
   *  <span data-ttu-id="8624e-916">Se `K` è zero e la dichiarazione di `T` include un parametro di tipo con nome `I`, quindi il *simple_name* fa riferimento a tale parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-916">If `K` is zero and the declaration of `T` includes a type parameter with name `I`, then the *simple_name* refers to that type parameter.</span></span>
   *  <span data-ttu-id="8624e-917">In caso contrario, se una ricerca di membri ([ricerca di membri](expressions.md#member-lookup)) della `I` nelle `T` con `K`  gli argomenti di tipo produce una corrispondenza:</span><span class="sxs-lookup"><span data-stu-id="8624e-917">Otherwise, if a member lookup ([Member lookup](expressions.md#member-lookup)) of `I` in `T` with `K` type arguments produces a match:</span></span>
      * <span data-ttu-id="8624e-918">Se `T` è il tipo di istanza del tipo classe o struct che la contiene e la ricerca individua uno o più metodi, il risultato è un gruppo di metodi con un'espressione dell'istanza associata di `this`.</span><span class="sxs-lookup"><span data-stu-id="8624e-918">If `T` is the instance type of the immediately enclosing class or struct type and the lookup identifies one or more methods, the result is a method group with an associated instance expression of `this`.</span></span> <span data-ttu-id="8624e-919">Se è stato specificato un elenco di argomenti, viene utilizzato nella chiamata a un metodo generico ([chiamate al metodo](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="8624e-919">If a type argument list was specified, it is used in calling a generic method ([Method invocations](expressions.md#method-invocations)).</span></span>
      * <span data-ttu-id="8624e-920">In caso contrario, se `T` è il tipo di istanza del tipo classe o struct che li contiene, se la ricerca identifica un membro di istanza e se il riferimento si trova all'interno del corpo di un costruttore di istanza, un metodo di istanza o una funzione di accesso di istanza, il risultato è lo stesso dell'accesso ai membri ([accesso al membro](expressions.md#member-access)) nel formato `this.I`.</span><span class="sxs-lookup"><span data-stu-id="8624e-920">Otherwise, if `T` is the instance type of the immediately enclosing class or struct type, if the lookup identifies an instance member, and if the reference occurs within the body of an instance constructor, an instance method, or an instance accessor, the result is the same as a member access ([Member access](expressions.md#member-access)) of the form `this.I`.</span></span> <span data-ttu-id="8624e-921">Questo può accadere solo quando `K` è uguale a zero.</span><span class="sxs-lookup"><span data-stu-id="8624e-921">This can only happen when `K` is zero.</span></span>
      * <span data-ttu-id="8624e-922">In caso contrario, il risultato è lo stesso dell'accesso ai membri ([accesso al membro](expressions.md#member-access)) nel formato `T.I` o `T.I<A1,...,Ak>`.</span><span class="sxs-lookup"><span data-stu-id="8624e-922">Otherwise, the result is the same as a member access ([Member access](expressions.md#member-access)) of the form `T.I` or `T.I<A1,...,Ak>`.</span></span> <span data-ttu-id="8624e-923">In questo caso, è un errore in fase di associazione per il *simple_name* per fare riferimento a un membro di istanza.</span><span class="sxs-lookup"><span data-stu-id="8624e-923">In this case, it is a binding-time error for the *simple_name* to refer to an instance member.</span></span>

*  <span data-ttu-id="8624e-924">In caso contrario, per ogni spazio dei nomi `N`, che inizia con lo spazio dei nomi in cui la *simple_name* , proseguendo con ogni inclusione dello spazio dei nomi (se presente) e terminando con lo spazio dei nomi globale, i passaggi seguenti sono valutato fino a quando non viene individuata un'entità:</span><span class="sxs-lookup"><span data-stu-id="8624e-924">Otherwise, for each namespace `N`, starting with the namespace in which the *simple_name* occurs, continuing with each enclosing namespace (if any), and ending with the global namespace, the following steps are evaluated until an entity is located:</span></span>
   *  <span data-ttu-id="8624e-925">Se `K` è uguale a zero e `I` è il nome di uno spazio dei nomi in `N`, quindi:</span><span class="sxs-lookup"><span data-stu-id="8624e-925">If `K` is zero and `I` is the name of a namespace in `N`, then:</span></span>
      * <span data-ttu-id="8624e-926">Se la posizione in cui il *simple_name* si verifica è racchiuso da una dichiarazione dello spazio dei nomi per `N` e la dichiarazione dello spazio dei nomi contiene un' *extern_alias_directive* o  *using_alias_directive* che associa il nome `I` con uno spazio dei nomi o un tipo, il *simple_name* è ambiguo e si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-926">If the location where the *simple_name* occurs is enclosed by a namespace declaration for `N` and the namespace declaration contains an *extern_alias_directive* or *using_alias_directive* that associates the name `I` with a namespace or type, then the *simple_name* is ambiguous and a compile-time error occurs.</span></span>
      * <span data-ttu-id="8624e-927">In caso contrario, il *simple_name* fa riferimento allo spazio dei nomi denominato `I` in `N`.</span><span class="sxs-lookup"><span data-stu-id="8624e-927">Otherwise, the *simple_name* refers to the namespace named `I` in `N`.</span></span>
   *  <span data-ttu-id="8624e-928">In caso contrario, se `N` contiene un tipo accessibile con nome `I` e `K`  parametri di tipo, quindi:</span><span class="sxs-lookup"><span data-stu-id="8624e-928">Otherwise, if `N` contains an accessible type having name `I` and `K` type parameters, then:</span></span>
      * <span data-ttu-id="8624e-929">Se `K` è zero e il percorso in cui la *simple_name* si verifica è racchiuso da una dichiarazione dello spazio dei nomi per `N` e la dichiarazione dello spazio dei nomi contiene un *extern_alias_directive*oppure *using_alias_directive* che associa il nome `I` con uno spazio dei nomi o un tipo, il *simple_name* è ambiguo e si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-929">If `K` is zero and the location where the *simple_name* occurs is enclosed by a namespace declaration for `N` and the namespace declaration contains an *extern_alias_directive* or *using_alias_directive* that associates the name `I` with a namespace or type, then the *simple_name* is ambiguous and a compile-time error occurs.</span></span>
      * <span data-ttu-id="8624e-930">In caso contrario, il *namespace_or_type_name* fa riferimento al tipo costruito con gli argomenti di tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="8624e-930">Otherwise, the *namespace_or_type_name* refers to the type constructed with the given type arguments.</span></span>
   *  <span data-ttu-id="8624e-931">In caso contrario, se la posizione in cui il *simple_name* si verifica è racchiuso da una dichiarazione dello spazio dei nomi per `N`:</span><span class="sxs-lookup"><span data-stu-id="8624e-931">Otherwise, if the location where the *simple_name* occurs is enclosed by a namespace declaration for `N`:</span></span>
      * <span data-ttu-id="8624e-932">Se `K` è uguale a zero e la dichiarazione dello spazio dei nomi contiene un *extern_alias_directive* oppure *using_alias_directive* che associa il nome `I` con uno spazio dei nomi importato o tipo, il *simple_name* fa riferimento a tale spazio dei nomi o tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-932">If `K` is zero and the namespace declaration contains an *extern_alias_directive* or *using_alias_directive* that associates the name `I` with an imported namespace or type, then the *simple_name* refers to that namespace or type.</span></span>
      * <span data-ttu-id="8624e-933">In caso contrario, se le dichiarazioni degli spazi dei nomi e il tipo importato per la *using_namespace_directive*s e *using_static_directive*s della dichiarazione dello spazio dei nomi contiene esattamente un tipo accessibile o membro statico senza estensione con nome `I` e `K`  parametri di tipo, il *simple_name* fa riferimento a quel tipo o membro costruito con gli argomenti di tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="8624e-933">Otherwise, if the namespaces and type declarations imported by the *using_namespace_directive*s and *using_static_directive*s of the namespace declaration contain exactly one accessible type or non-extension static member having name `I` and `K` type parameters, then the *simple_name* refers to that type or member constructed with the given type arguments.</span></span>
      * <span data-ttu-id="8624e-934">In caso contrario, se gli spazi dei nomi e i tipi importati mediante il *using_namespace_directive*s della dichiarazione dello spazio dei nomi contiene più di un tipo accessibile o il metodo di estensione non membro statico con nome `I` e`K`  parametri di tipo, il *simple_name* è ambiguo e si verifica un errore.</span><span class="sxs-lookup"><span data-stu-id="8624e-934">Otherwise, if the namespaces and types imported by the *using_namespace_directive*s of the namespace declaration contain more than one accessible type or non-extension-method static member having name `I` and `K` type parameters, then the *simple_name* is ambiguous and an error occurs.</span></span>

   <span data-ttu-id="8624e-935">Si noti che questo passaggio è esattamente parallelo per il passaggio corrispondente per l'elaborazione di un *namespace_or_type_name* ([Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)).</span><span class="sxs-lookup"><span data-stu-id="8624e-935">Note that this entire step is exactly parallel to the corresponding step in the processing of a *namespace_or_type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)).</span></span>

*  <span data-ttu-id="8624e-936">In caso contrario, il *simple_name* è non definito e si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-936">Otherwise, the *simple_name* is undefined and a compile-time error occurs.</span></span>


### <a name="parenthesized-expressions"></a><span data-ttu-id="8624e-937">Espressioni tra parentesi</span><span class="sxs-lookup"><span data-stu-id="8624e-937">Parenthesized expressions</span></span>

<span data-ttu-id="8624e-938">Oggetto *parenthesized_expression* costituito da un *espressione* racchiuso tra parentesi.</span><span class="sxs-lookup"><span data-stu-id="8624e-938">A *parenthesized_expression* consists of an *expression* enclosed in parentheses.</span></span>

```antlr
parenthesized_expression
    : '(' expression ')'
    ;
```

<span data-ttu-id="8624e-939">Oggetto *parenthesized_expression* viene valutato valutando il *espressione* all'interno delle parentesi.</span><span class="sxs-lookup"><span data-stu-id="8624e-939">A *parenthesized_expression* is evaluated by evaluating the *expression* within the parentheses.</span></span> <span data-ttu-id="8624e-940">Se il *espressione* racchiusa tra parentesi indica uno spazio dei nomi o un tipo, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-940">If the *expression* within the parentheses denotes a namespace or type, a compile-time error occurs.</span></span> <span data-ttu-id="8624e-941">In caso contrario, il risultato del *parenthesized_expression* è il risultato della valutazione dell'oggetto contenuto *espressione*.</span><span class="sxs-lookup"><span data-stu-id="8624e-941">Otherwise, the result of the *parenthesized_expression* is the result of the evaluation of the contained *expression*.</span></span>

### <a name="member-access"></a><span data-ttu-id="8624e-942">Accesso ai membri</span><span class="sxs-lookup"><span data-stu-id="8624e-942">Member access</span></span>

<span data-ttu-id="8624e-943">Oggetto *member_access* costituito da un *primary_expression*, una *predefined_type*, o un *qualified_alias_member*, seguito da un"`.`"token, seguita da un' *identificatore*, seguito facoltativamente da una *type_argument_list*.</span><span class="sxs-lookup"><span data-stu-id="8624e-943">A *member_access* consists of a *primary_expression*, a *predefined_type*, or a *qualified_alias_member*, followed by a "`.`" token, followed by an *identifier*, optionally followed by a *type_argument_list*.</span></span>

```antlr
member_access
    : primary_expression '.' identifier type_argument_list?
    | predefined_type '.' identifier type_argument_list?
    | qualified_alias_member '.' identifier
    ;

predefined_type
    : 'bool'   | 'byte'  | 'char'  | 'decimal' | 'double' | 'float' | 'int' | 'long'
    | 'object' | 'sbyte' | 'short' | 'string'  | 'uint'   | 'ulong' | 'ushort'
    ;
```

<span data-ttu-id="8624e-944">Il *qualified_alias_member* definita in produzione [qualificatori di alias Namespace](namespaces.md#namespace-alias-qualifiers).</span><span class="sxs-lookup"><span data-stu-id="8624e-944">The *qualified_alias_member* production is defined in [Namespace alias qualifiers](namespaces.md#namespace-alias-qualifiers).</span></span>

<span data-ttu-id="8624e-945">Oggetto *member_access* può essere nel formato `E.I` o del form `E.I<A1, ..., Ak>`, dove `E` è un'espressione primaria, `I` è un identificatore singolo e `<A1, ..., Ak>` è una funzione facoltativa  *type_argument_list*.</span><span class="sxs-lookup"><span data-stu-id="8624e-945">A *member_access* is either of the form `E.I` or of the form `E.I<A1, ..., Ak>`, where `E` is a primary-expression, `I` is a single identifier and `<A1, ..., Ak>` is an optional *type_argument_list*.</span></span> <span data-ttu-id="8624e-946">Se non si specifica *type_argument_list* è specificato, prendere in considerazione `K` sia pari a zero.</span><span class="sxs-lookup"><span data-stu-id="8624e-946">When no *type_argument_list* is specified, consider `K` to be zero.</span></span>

<span data-ttu-id="8624e-947">Oggetto *member_access* con un *primary_expression* di tipo `dynamic` viene associata in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="8624e-947">A *member_access* with a *primary_expression* of type `dynamic` is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="8624e-948">In questo caso il compilatore classifica l'accesso al membro come un accesso a proprietà di tipo `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-948">In this case the compiler classifies the member access as a property access of type `dynamic`.</span></span> <span data-ttu-id="8624e-949">Le regole seguenti per determinare il significato del *member_access* vengono quindi applicati in fase di esecuzione, usando il tipo di fase di esecuzione anziché al tipo di fase di compilazione delle *primary_expression*.</span><span class="sxs-lookup"><span data-stu-id="8624e-949">The rules below to determine the meaning of the *member_access* are then applied at run-time, using the run-time type instead of the compile-time type of the *primary_expression*.</span></span> <span data-ttu-id="8624e-950">Se questa classificazione in fase di esecuzione conduce a un gruppo di metodi, l'accesso ai membri deve essere il *primary_expression* di un *invocation_expression*.</span><span class="sxs-lookup"><span data-stu-id="8624e-950">If this run-time classification leads to a method group, then the member access must be the *primary_expression* of an *invocation_expression*.</span></span>

<span data-ttu-id="8624e-951">Il *member_access* viene valutato e classificato come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-951">The *member_access* is evaluated and classified as follows:</span></span>

*  <span data-ttu-id="8624e-952">Se `K` è uguale a zero e `E` è uno spazio dei nomi e `E` contiene uno spazio dei nomi annidato con nome `I`, il risultato è lo spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="8624e-952">If `K` is zero and `E` is a namespace and `E` contains a nested namespace with name `I`, then the result is that namespace.</span></span>
*  <span data-ttu-id="8624e-953">In caso contrario, se `E` è uno spazio dei nomi e `E` contiene un tipo accessibile con nome `I` e `K`  parametri di tipo, il risultato è il tipo costruito con gli argomenti di tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="8624e-953">Otherwise, if `E` is a namespace and `E` contains an accessible type having name `I` and `K` type parameters, then the result is that type constructed with the given type arguments.</span></span>
*  <span data-ttu-id="8624e-954">Se `E` è un *predefined_type* o una *primary_expression* classificata come un tipo, se `E` non è un parametro di tipo e la ricerca dei membri ([ricerca di membri](expressions.md#member-lookup)) dei `I` nelle `E` con `K`  parametri di tipo produce una corrispondenza, quindi `E.I` viene valutato e classificato come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-954">If `E` is a *predefined_type* or a *primary_expression* classified as a type, if `E` is not a type parameter, and if a member lookup ([Member lookup](expressions.md#member-lookup)) of `I` in `E` with `K` type parameters produces a match, then `E.I` is evaluated and classified as follows:</span></span>
   *  <span data-ttu-id="8624e-955">Se `I` identifica un tipo, il risultato è il tipo costruito con gli argomenti di tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="8624e-955">If `I` identifies a type, then the result is that type constructed with the given type arguments.</span></span>
   *  <span data-ttu-id="8624e-956">Se `I` identifica uno o più metodi, il risultato è un gruppo di metodi senza espressione di istanza associato.</span><span class="sxs-lookup"><span data-stu-id="8624e-956">If `I` identifies one or more methods, then the result is a method group with no associated instance expression.</span></span> <span data-ttu-id="8624e-957">Se è stato specificato un elenco di argomenti, viene utilizzato nella chiamata a un metodo generico ([chiamate al metodo](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="8624e-957">If a type argument list was specified, it is used in calling a generic method ([Method invocations](expressions.md#method-invocations)).</span></span>
   *  <span data-ttu-id="8624e-958">Se `I` identifica un `static` proprietà, il risultato è un accesso a proprietà senza un'espressione di istanza associato.</span><span class="sxs-lookup"><span data-stu-id="8624e-958">If `I` identifies a `static` property, then the result is a property access with no associated instance expression.</span></span>
   *  <span data-ttu-id="8624e-959">Se `I` identifica un `static` campo:</span><span class="sxs-lookup"><span data-stu-id="8624e-959">If `I` identifies a `static` field:</span></span>
      * <span data-ttu-id="8624e-960">Se il campo `readonly` e il riferimento si trova fuori dal costruttore statico della classe o struct in cui il campo viene dichiarato, quindi il risultato è un valore, vale a dire il valore del campo statico `I` in `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-960">If the field is `readonly` and the reference occurs outside the static constructor of the class or struct in which the field is declared, then the result is a value, namely the value of the static field `I` in `E`.</span></span>
      * <span data-ttu-id="8624e-961">In caso contrario, il risultato è una variabile, ovvero il campo statico `I` in `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-961">Otherwise, the result is a variable, namely the static field `I` in `E`.</span></span>
   *  <span data-ttu-id="8624e-962">Se `I` identifica un `static` evento:</span><span class="sxs-lookup"><span data-stu-id="8624e-962">If `I` identifies a `static` event:</span></span>
      * <span data-ttu-id="8624e-963">Se il riferimento si trova all'interno della classe o struct in cui viene dichiarato l'evento e l'evento è stato dichiarato senza *event_accessor_declarations* ([eventi](classes.md#events)), quindi `E.I` viene elaborato esattamente come se `I` fosse un campo statico.</span><span class="sxs-lookup"><span data-stu-id="8624e-963">If the reference occurs within the class or struct in which the event is declared, and the event was declared without *event_accessor_declarations* ([Events](classes.md#events)), then `E.I` is processed exactly as if `I` were a static field.</span></span>
      * <span data-ttu-id="8624e-964">In caso contrario, il risultato è l'accesso a un evento senza espressione di istanza associato.</span><span class="sxs-lookup"><span data-stu-id="8624e-964">Otherwise, the result is an event access with no associated instance expression.</span></span>
   *  <span data-ttu-id="8624e-965">Se `I` identifica una costante, il risultato è un valore, vale a dire il valore della costante.</span><span class="sxs-lookup"><span data-stu-id="8624e-965">If `I` identifies a constant, then the result is a value, namely the value of that constant.</span></span>
    * <span data-ttu-id="8624e-966">Se `I` identifica un membro di enumerazione, il risultato è un valore, vale a dire il valore del membro di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-966">If `I` identifies an enumeration member, then the result is a value, namely the value of that enumeration member.</span></span>
    * <span data-ttu-id="8624e-967">In caso contrario, `E.I` è un riferimento al membro non valido e si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-967">Otherwise, `E.I` is an invalid member reference, and a compile-time error occurs.</span></span>
*  <span data-ttu-id="8624e-968">Se `E` è un accesso a proprietà, accesso all'indicizzatore, una variabile o un valore, il cui tipo è `T`e una ricerca di membri ([ricerca di membri](expressions.md#member-lookup)) di `I` nella `T` con `K`  argomenti di tipo produce una corrispondenza, quindi `E.I` viene valutato e classificato come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-968">If `E` is a property access, indexer access, variable, or value, the type of which is `T`, and a member lookup ([Member lookup](expressions.md#member-lookup)) of `I` in `T` with `K` type arguments produces a match, then `E.I` is evaluated and classified as follows:</span></span>
   *  <span data-ttu-id="8624e-969">Innanzitutto, se `E` è una proprietà o l'accesso dell'indicizzatore, quindi il valore della proprietà o ottenuto accesso all'indicizzatore ([i valori delle espressioni](expressions.md#values-of-expressions)) e `E` è riclassificato come valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-969">First, if `E` is a property or indexer access, then the value of the property or indexer access is obtained ([Values of expressions](expressions.md#values-of-expressions)) and `E` is reclassified as a value.</span></span>
   *  <span data-ttu-id="8624e-970">Se `I` identifica uno o più metodi, il risultato è un gruppo di metodi con un'espressione dell'istanza associata di `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-970">If `I` identifies one or more methods, then the result is a method group with an associated instance expression of `E`.</span></span> <span data-ttu-id="8624e-971">Se è stato specificato un elenco di argomenti, viene utilizzato nella chiamata a un metodo generico ([chiamate al metodo](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="8624e-971">If a type argument list was specified, it is used in calling a generic method ([Method invocations](expressions.md#method-invocations)).</span></span>
   *  <span data-ttu-id="8624e-972">Se `I` identifica una proprietà dell'istanza,</span><span class="sxs-lookup"><span data-stu-id="8624e-972">If `I` identifies an instance property,</span></span>
      * <span data-ttu-id="8624e-973">Se `E` viene `this`, `I` identifica una proprietà implementata automaticamente ([proprietà implementate automaticamente](classes.md#automatically-implemented-properties)) senza un setter e il riferimento si trova all'interno di un costruttore di istanza per un tipo di classe o struct `T`, il risultato è una variabile, vale a dire il campo nascosto sottostante per la proprietà automatica specificato da `I` nell'istanza di `T` fornito dal `this`.</span><span class="sxs-lookup"><span data-stu-id="8624e-973">If `E` is `this`, `I` identifies an automatically implemented property ([Automatically implemented properties](classes.md#automatically-implemented-properties)) without a setter, and the reference occurs within an instance constructor for a class or struct type `T`, then the result is a variable, namely the hidden backing field for the auto-property given by `I` in the instance of `T` given by `this`.</span></span>
      * <span data-ttu-id="8624e-974">In caso contrario, il risultato è un accesso a proprietà con un'espressione dell'istanza associata di `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-974">Otherwise, the result is a property access with an associated instance expression of `E`.</span></span>
   *  <span data-ttu-id="8624e-975">Se `T` è un *class_type* e `I` identifica un campo di istanza di tale *class_type*:</span><span class="sxs-lookup"><span data-stu-id="8624e-975">If `T` is a *class_type* and `I` identifies an instance field of that *class_type*:</span></span>
      * <span data-ttu-id="8624e-976">Se il valore di `E` viene `null`, quindi un `System.NullReferenceException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-976">If the value of `E` is `null`, then a `System.NullReferenceException` is thrown.</span></span>
      * <span data-ttu-id="8624e-977">In caso contrario, se il campo `readonly` e il riferimento si trova all'esterno di un costruttore di istanza della classe in cui il campo viene dichiarato, quindi il risultato è un valore, vale a dire il valore del campo `I` nell'oggetto fa riferimento `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-977">Otherwise, if the field is `readonly` and the reference occurs outside an instance constructor of the class in which the field is declared, then the result is a value, namely the value of the field `I` in the object referenced by `E`.</span></span>
      * <span data-ttu-id="8624e-978">In caso contrario, il risultato è una variabile, vale a dire il campo `I` nell'oggetto fa riferimento `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-978">Otherwise, the result is a variable, namely the field `I` in the object referenced by `E`.</span></span>
   *  <span data-ttu-id="8624e-979">Se `T` è un *struct_type* e `I` identifica un campo di istanza di tale *struct_type*:</span><span class="sxs-lookup"><span data-stu-id="8624e-979">If `T` is a *struct_type* and `I` identifies an instance field of that *struct_type*:</span></span>
      * <span data-ttu-id="8624e-980">Se `E` è un valore, o se il campo `readonly` e il riferimento si trova all'esterno di un costruttore di istanza dello struct in cui il campo viene dichiarato, quindi il risultato è un valore, vale a dire il valore del campo `I` nell'istanza della struttura specificata da  `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-980">If `E` is a value, or if the field is `readonly` and the reference occurs outside an instance constructor of the struct in which the field is declared, then the result is a value, namely the value of the field `I` in the struct instance given by `E`.</span></span>
      * <span data-ttu-id="8624e-981">In caso contrario, il risultato è una variabile, vale a dire il campo `I` nell'istanza della struttura specificata da `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-981">Otherwise, the result is a variable, namely the field `I` in the struct instance given by `E`.</span></span>
   *  <span data-ttu-id="8624e-982">Se `I` identifica un evento di istanza:</span><span class="sxs-lookup"><span data-stu-id="8624e-982">If `I` identifies an instance event:</span></span>
      * <span data-ttu-id="8624e-983">Se il riferimento si trova all'interno della classe o struct in cui viene dichiarato l'evento e l'evento è stato dichiarato senza *event_accessor_declarations* ([eventi](classes.md#events)), e il riferimento non vengono eseguite con il parte sinistra di una `+=` oppure `-=` operatore, quindi `E.I` viene elaborato esattamente come se `I` era un campo di istanza.</span><span class="sxs-lookup"><span data-stu-id="8624e-983">If the reference occurs within the class or struct in which the event is declared, and the event was declared without *event_accessor_declarations* ([Events](classes.md#events)), and the reference does not occur as the left-hand side of a `+=` or `-=` operator, then `E.I` is processed exactly as if `I` was an instance field.</span></span>
      * <span data-ttu-id="8624e-984">In caso contrario, il risultato è l'accesso a un evento con un'espressione dell'istanza associata di `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-984">Otherwise, the result is an event access with an associated instance expression of `E`.</span></span>
*  <span data-ttu-id="8624e-985">In caso contrario, viene effettuato un tentativo di elaborare `E.I` come una chiamata al metodo di estensione ([chiamate al metodo di estensione](expressions.md#extension-method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="8624e-985">Otherwise, an attempt is made to process `E.I` as an extension method invocation ([Extension method invocations](expressions.md#extension-method-invocations)).</span></span> <span data-ttu-id="8624e-986">In caso di errore, `E.I` è un riferimento al membro non valido e si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-986">If this fails, `E.I` is an invalid member reference, and a binding-time error occurs.</span></span>

#### <a name="identical-simple-names-and-type-names"></a><span data-ttu-id="8624e-987">I nomi dei tipi e nomi semplici identici</span><span class="sxs-lookup"><span data-stu-id="8624e-987">Identical simple names and type names</span></span>

<span data-ttu-id="8624e-988">In un accesso ai membri del modulo `E.I`, se `E` è un identificatore singolo e se il significato dei `E` come un *simple_name* ([nomi semplici](expressions.md#simple-names)) è una costante, campo, proprietà, variabile locale, o un parametro con lo stesso tipo il significato dei `E` come un *type_name* ([Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)), quindi entrambi possibili significati della `E` sono è consentito.</span><span class="sxs-lookup"><span data-stu-id="8624e-988">In a member access of the form `E.I`, if `E` is a single identifier, and if the meaning of `E` as a *simple_name* ([Simple names](expressions.md#simple-names)) is a constant, field, property, local variable, or parameter with the same type as the meaning of `E` as a *type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)), then both possible meanings of `E` are permitted.</span></span> <span data-ttu-id="8624e-989">I due possibili significati della `E.I` mai sono ambigue, poiché `I` deve necessariamente essere un membro del tipo `E` in entrambi i casi.</span><span class="sxs-lookup"><span data-stu-id="8624e-989">The two possible meanings of `E.I` are never ambiguous, since `I` must necessarily be a member of the type `E` in both cases.</span></span> <span data-ttu-id="8624e-990">In altre parole, la regola semplicemente consente l'accesso ai membri statici e i tipi annidati di `E` in cui in caso contrario, potrebbe essersi verificato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-990">In other words, the rule simply permits access to the static members and nested types of `E` where a compile-time error would otherwise have occurred.</span></span> <span data-ttu-id="8624e-991">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8624e-991">For example:</span></span>
```csharp
struct Color
{
    public static readonly Color White = new Color(...);
    public static readonly Color Black = new Color(...);

    public Color Complement() {...}
}

class A
{
    public Color Color;                // Field Color of type Color

    void F() {
        Color = Color.Black;           // References Color.Black static member
        Color = Color.Complement();    // Invokes Complement() on Color field
    }

    static void G() {
        Color c = Color.White;         // References Color.White static member
    }
}
```

#### <a name="grammar-ambiguities"></a><span data-ttu-id="8624e-992">Ambiguità di grammatica</span><span class="sxs-lookup"><span data-stu-id="8624e-992">Grammar ambiguities</span></span>

<span data-ttu-id="8624e-993">Produzioni per *simple_name* ([nomi semplici](expressions.md#simple-names)) e *member_access* ([l'accesso ai membri](expressions.md#member-access)) può dar luogo a ambiguità di grammatica delle espressioni.</span><span class="sxs-lookup"><span data-stu-id="8624e-993">The productions for *simple_name* ([Simple names](expressions.md#simple-names)) and *member_access* ([Member access](expressions.md#member-access)) can give rise to ambiguities in the grammar for expressions.</span></span> <span data-ttu-id="8624e-994">Ad esempio, l'istruzione:</span><span class="sxs-lookup"><span data-stu-id="8624e-994">For example, the statement:</span></span>
```
F(G<A,B>(7));
```
<span data-ttu-id="8624e-995">potrebbero essere interpretati come una chiamata a `F` con due argomenti, `G < A` e `B > (7)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-995">could be interpreted as a call to `F` with two arguments, `G < A` and `B > (7)`.</span></span> <span data-ttu-id="8624e-996">In alternativa, può essere interpretata come una chiamata a `F` con un solo argomento, ovvero una chiamata a un metodo generico `G` con due argomenti di tipo e un argomento regolare.</span><span class="sxs-lookup"><span data-stu-id="8624e-996">Alternatively, it could be interpreted as a call to `F` with one argument, which is a call to a generic method `G` with two type arguments and one regular argument.</span></span>

<span data-ttu-id="8624e-997">Se una sequenza di token può essere analizzata (nel contesto) come un *simple_name* ([nomi semplici](expressions.md#simple-names)), *member_access* ([l'accesso ai membri](expressions.md#member-access)), o *pointer_member_access* ([accesso ai membri del puntatore](unsafe-code.md#pointer-member-access)) che termina con un *type_argument_list* ([gli argomenti di tipo](types.md#type-arguments)), il token subito dopo la chiusura `>` token viene esaminato.</span><span class="sxs-lookup"><span data-stu-id="8624e-997">If a sequence of tokens can be parsed (in context) as a *simple_name* ([Simple names](expressions.md#simple-names)), *member_access* ([Member access](expressions.md#member-access)), or *pointer_member_access* ([Pointer member access](unsafe-code.md#pointer-member-access)) ending with a *type_argument_list* ([Type arguments](types.md#type-arguments)), the token immediately following the closing `>` token is examined.</span></span> <span data-ttu-id="8624e-998">Se è uno di</span><span class="sxs-lookup"><span data-stu-id="8624e-998">If it is one of</span></span>
```csharp
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
```
<span data-ttu-id="8624e-999">il *type_argument_list* viene mantenuto come parte del *simple_name*, *member_access* oppure *pointer_member_access* e qualsiasi altre analisi della sequenza di token viene ignorata.</span><span class="sxs-lookup"><span data-stu-id="8624e-999">then the *type_argument_list* is retained as part of the *simple_name*, *member_access* or *pointer_member_access* and any other possible parse of the sequence of tokens is discarded.</span></span> <span data-ttu-id="8624e-1000">In caso contrario, il *type_argument_list* non viene considerato a far parte delle *simple_name*, *member_access* o *pointer_member_access*, anche se è presente alcun ulteriore analisi della sequenza di token.</span><span class="sxs-lookup"><span data-stu-id="8624e-1000">Otherwise, the *type_argument_list* is not considered to be part of the *simple_name*, *member_access* or *pointer_member_access*, even if there is no other possible parse of the sequence of tokens.</span></span> <span data-ttu-id="8624e-1001">Si noti che queste regole non vengono applicate durante l'analisi di un *type_argument_list* in un *namespace_or_type_name* ([Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1001">Note that these rules are not applied when parsing a *type_argument_list* in a *namespace_or_type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)).</span></span> <span data-ttu-id="8624e-1002">L'istruzione</span><span class="sxs-lookup"><span data-stu-id="8624e-1002">The statement</span></span>
```csharp
F(G<A,B>(7));
```
<span data-ttu-id="8624e-1003">verrà, in base a questa regola, interpretato come una chiamata a `F` con un solo argomento, ovvero una chiamata a un metodo generico `G` con due argomenti di tipo e un argomento regolare.</span><span class="sxs-lookup"><span data-stu-id="8624e-1003">will, according to this rule, be interpreted as a call to `F` with one argument, which is a call to a generic method `G` with two type arguments and one regular argument.</span></span> <span data-ttu-id="8624e-1004">Le istruzioni</span><span class="sxs-lookup"><span data-stu-id="8624e-1004">The statements</span></span>
```csharp
F(G < A, B > 7);
F(G < A, B >> 7);
```
<span data-ttu-id="8624e-1005">verrà ognuno interpretato come una chiamata a `F` con due argomenti.</span><span class="sxs-lookup"><span data-stu-id="8624e-1005">will each be interpreted as a call to `F` with two arguments.</span></span> <span data-ttu-id="8624e-1006">L'istruzione</span><span class="sxs-lookup"><span data-stu-id="8624e-1006">The statement</span></span>
```csharp
x = F < A > +y;
```
<span data-ttu-id="8624e-1007">verrà interpretato come un operatore minore di, maggiore di operatore e operatore unario, come se fosse stata scritta l'istruzione `x = (F < A) > (+y)`, anziché come un *simple_name* con un *type_argument_list* seguita da un operatore binario +.</span><span class="sxs-lookup"><span data-stu-id="8624e-1007">will be interpreted as a less than operator, greater than operator, and unary plus operator, as if the statement had been written `x = (F < A) > (+y)`, instead of as a *simple_name* with a *type_argument_list* followed by a binary plus operator.</span></span> <span data-ttu-id="8624e-1008">Nell'istruzione</span><span class="sxs-lookup"><span data-stu-id="8624e-1008">In the statement</span></span>
```csharp
x = y is C<T> + z;
```
<span data-ttu-id="8624e-1009">i token `C<T>` vengono interpretati come un *namespace_or_type_name* con un *type_argument_list*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1009">the tokens `C<T>` are interpreted as a *namespace_or_type_name* with a *type_argument_list*.</span></span>

### <a name="invocation-expressions"></a><span data-ttu-id="8624e-1010">Espressioni di chiamata</span><span class="sxs-lookup"><span data-stu-id="8624e-1010">Invocation expressions</span></span>

<span data-ttu-id="8624e-1011">Un' *invocation_expression* viene utilizzato per richiamare un metodo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1011">An *invocation_expression* is used to invoke a method.</span></span>

```antlr
invocation_expression
    : primary_expression '(' argument_list? ')'
    ;
```

<span data-ttu-id="8624e-1012">Un' *invocation_expression* in modo dinamico è associato ([binding dinamico](expressions.md#dynamic-binding)) se contiene almeno uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1012">An *invocation_expression* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)) if at least one of the following holds:</span></span>

* <span data-ttu-id="8624e-1013">Il *primary_expression* è di tipo in fase di compilazione `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1013">The *primary_expression* has compile-time type `dynamic`.</span></span>
* <span data-ttu-id="8624e-1014">Almeno un argomento dell'elemento facoltativo *argument_list* è di tipo in fase di compilazione `dynamic` e il *primary_expression* non dispone di un tipo delegato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1014">At least one argument of the optional *argument_list* has compile-time type `dynamic` and the *primary_expression* does not have a delegate type.</span></span>

<span data-ttu-id="8624e-1015">In questo caso il compilatore consente di classificare le *invocation_expression* come valore di tipo `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1015">In this case the compiler classifies the *invocation_expression* as a value of type `dynamic`.</span></span> <span data-ttu-id="8624e-1016">Le regole seguenti per determinare il significato del *invocation_expression* vengono quindi applicati in fase di esecuzione, usando il tipo di fase di esecuzione anziché al tipo di fase di compilazione di quelle del *primary_expression* e gli argomenti che hanno il tipo di fase di compilazione `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1016">The rules below to determine the meaning of the *invocation_expression* are then applied at run-time, using the run-time type instead of the compile-time type of those of the *primary_expression* and arguments which have the compile-time type `dynamic`.</span></span> <span data-ttu-id="8624e-1017">Se il *primary_expression* non è in fase di compilazione `dynamic`, quindi la chiamata del metodo sottoposto a un controllo in fase di compilazione limitato, come descritto in [controllo della risoluzione dell'overload dinamico in fase di compilazione ](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="8624e-1017">If the *primary_expression* does not have compile-time type `dynamic`, then the method invocation undergoes a limited compile time check as described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="8624e-1018">Il *primary_expression* di un *invocation_expression* deve essere un gruppo di metodi o un valore di una *delegate_type*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1018">The *primary_expression* of an *invocation_expression* must be a method group or a value of a *delegate_type*.</span></span> <span data-ttu-id="8624e-1019">Se il *primary_expression* è un gruppo di metodi, il *invocation_expression* è una chiamata al metodo ([chiamate al metodo](expressions.md#method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1019">If the *primary_expression* is a method group, the *invocation_expression* is a method invocation ([Method invocations](expressions.md#method-invocations)).</span></span> <span data-ttu-id="8624e-1020">Se il *primary_expression* è un valore di una *delegate_type*, il *invocation_expression* è una chiamata al delegato ([chiamatedidelegati](expressions.md#delegate-invocations)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1020">If the *primary_expression* is a value of a *delegate_type*, the *invocation_expression* is a delegate invocation ([Delegate invocations](expressions.md#delegate-invocations)).</span></span> <span data-ttu-id="8624e-1021">Se il *primary_expression* è un gruppo di metodi né un valore di una *delegate_type*, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1021">If the *primary_expression* is neither a method group nor a value of a *delegate_type*, a binding-time error occurs.</span></span>

<span data-ttu-id="8624e-1022">L'opzione facoltativa *argument_list* ([elenchi di argomenti](expressions.md#argument-lists)) fornisce i valori o riferimenti a variabili per i parametri del metodo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1022">The optional *argument_list* ([Argument lists](expressions.md#argument-lists)) provides values or variable references for the parameters of the method.</span></span>

<span data-ttu-id="8624e-1023">Il risultato della valutazione di un' *invocation_expression* viene classificata come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-1023">The result of evaluating an *invocation_expression* is classified as follows:</span></span>

*  <span data-ttu-id="8624e-1024">Se il *invocation_expression* richiama un metodo o delegato che restituisce `void`, il risultato è nothing.</span><span class="sxs-lookup"><span data-stu-id="8624e-1024">If the *invocation_expression* invokes a method or delegate that returns `void`, the result is nothing.</span></span> <span data-ttu-id="8624e-1025">Un'espressione che viene classificata come non è consentita solo nel contesto di un *statement_expression* ([le istruzioni di espressione](statements.md#expression-statements)) o come corpo di un *lambda_expression*([Espressioni di funzioni anonime](expressions.md#anonymous-function-expressions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1025">An expression that is classified as nothing is permitted only in the context of a *statement_expression* ([Expression statements](statements.md#expression-statements)) or as the body of a *lambda_expression* ([Anonymous function expressions](expressions.md#anonymous-function-expressions)).</span></span> <span data-ttu-id="8624e-1026">In caso contrario, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1026">Otherwise a binding-time error occurs.</span></span>
*  <span data-ttu-id="8624e-1027">In caso contrario, il risultato è un valore del tipo restituito dal metodo o delegato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1027">Otherwise, the result is a value of the type returned by the method or delegate.</span></span>

#### <a name="method-invocations"></a><span data-ttu-id="8624e-1028">Chiamate ai metodi</span><span class="sxs-lookup"><span data-stu-id="8624e-1028">Method invocations</span></span>

<span data-ttu-id="8624e-1029">Per una chiamata al metodo, il *primary_expression* delle *invocation_expression* deve essere un gruppo di metodi.</span><span class="sxs-lookup"><span data-stu-id="8624e-1029">For a method invocation, the *primary_expression* of the *invocation_expression* must be a method group.</span></span> <span data-ttu-id="8624e-1030">Il gruppo di metodi identifica il metodo da richiamare o il set di metodi di overload da cui scegliere un metodo specifico da richiamare.</span><span class="sxs-lookup"><span data-stu-id="8624e-1030">The method group identifies the one method to invoke or the set of overloaded methods from which to choose a specific method to invoke.</span></span> <span data-ttu-id="8624e-1031">Nel secondo caso, la scelta del metodo specifico da richiamare è in base al contesto fornito dai tipi degli argomenti in di *argument_list*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1031">In the latter case, determination of the specific method to invoke is based on the context provided by the types of the arguments in the *argument_list*.</span></span>

<span data-ttu-id="8624e-1032">L'elaborazione in fase di associazione di una chiamata al metodo del form `M(A)`, dove `M` è un gruppo di metodi (possibilmente con un *type_argument_list*), e `A` è facoltativo *argument_ elenco*, costituita dai passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1032">The binding-time processing of a method invocation of the form `M(A)`, where `M` is a method group (possibly including a *type_argument_list*), and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="8624e-1033">Il set di metodi candidati per la chiamata al metodo viene costruito.</span><span class="sxs-lookup"><span data-stu-id="8624e-1033">The set of candidate methods for the method invocation is constructed.</span></span> <span data-ttu-id="8624e-1034">Per ogni metodo `F` associati al gruppo metodo `M`:</span><span class="sxs-lookup"><span data-stu-id="8624e-1034">For each method `F` associated with the method group `M`:</span></span>
   *  <span data-ttu-id="8624e-1035">Se `F` non è generica, `F` è un candidato quando:</span><span class="sxs-lookup"><span data-stu-id="8624e-1035">If `F` is non-generic, `F` is a candidate when:</span></span>
      * <span data-ttu-id="8624e-1036">`M` non dispone di alcun elenco di argomenti tipo, e</span><span class="sxs-lookup"><span data-stu-id="8624e-1036">`M` has no type argument list, and</span></span>
      * <span data-ttu-id="8624e-1037">`F` è applicabile alle `A` ([membro di funzione applicabile](expressions.md#applicable-function-member)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1037">`F` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span>
   *  <span data-ttu-id="8624e-1038">Se `F` è generico e `M` non dispone di alcun elenco di argomenti tipo, `F` è un candidato quando:</span><span class="sxs-lookup"><span data-stu-id="8624e-1038">If `F` is generic and `M` has no type argument list, `F` is a candidate when:</span></span>
      * <span data-ttu-id="8624e-1039">L'inferenza del tipo ([inferenza del tipo](expressions.md#type-inference)) ha esito positivo, l'inferenza di un elenco di argomenti tipo per la chiamata, e</span><span class="sxs-lookup"><span data-stu-id="8624e-1039">Type inference ([Type inference](expressions.md#type-inference)) succeeds, inferring a list of type arguments for the call, and</span></span>
      * <span data-ttu-id="8624e-1040">Una volta che vengono sostituiti gli argomenti tipo dedotti per i parametri di tipo di metodo corrispondente, tutti i tipi costruiti nell'elenco dei parametri di F soddisfano i vincoli ([che soddisfano i vincoli](types.md#satisfying-constraints)) e nell'elenco di parametri di `F` è applicabile alle `A` ([membro di funzione applicabile](expressions.md#applicable-function-member)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1040">Once the inferred type arguments are substituted for the corresponding method type parameters, all constructed types in the parameter list of F satisfy their constraints ([Satisfying constraints](types.md#satisfying-constraints)), and the parameter list of `F` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span>
   *  <span data-ttu-id="8624e-1041">Se `F` è generico e `M` include un elenco di argomenti, `F` è un candidato quando:</span><span class="sxs-lookup"><span data-stu-id="8624e-1041">If `F` is generic and `M` includes a type argument list, `F` is a candidate when:</span></span>
      * <span data-ttu-id="8624e-1042">`F` ha lo stesso numero di parametri di tipo di metodo come sono stati specificati nell'elenco di argomenti tipo, e</span><span class="sxs-lookup"><span data-stu-id="8624e-1042">`F` has the same number of method type parameters as were supplied in the type argument list, and</span></span>
      * <span data-ttu-id="8624e-1043">Una volta che gli argomenti di tipo vengono sostituiti con i parametri di tipo di metodo corrispondente, tutti i tipi costruiti nell'elenco dei parametri di F soddisfano i vincoli ([che soddisfano i vincoli](types.md#satisfying-constraints)) e nell'elenco di parametri di `F` è applicabile alle `A` ([membro di funzione applicabile](expressions.md#applicable-function-member)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1043">Once the type arguments are substituted for the corresponding method type parameters, all constructed types in the parameter list of F satisfy their constraints ([Satisfying constraints](types.md#satisfying-constraints)), and the parameter list of `F` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span>
*  <span data-ttu-id="8624e-1044">Il set di metodi candidati viene ridotto affinché contenga solo i metodi da tipi più derivati: Per ogni metodo `C.F` nel set, in cui `C` è il tipo in cui il metodo `F` viene dichiarato, tutti i metodi dichiarati in un tipo di base di `C` vengono rimosse dal set.</span><span class="sxs-lookup"><span data-stu-id="8624e-1044">The set of candidate methods is reduced to contain only methods from the most derived types: For each method `C.F` in the set, where `C` is the type in which the method `F` is declared, all methods declared in a base type of `C` are removed from the set.</span></span> <span data-ttu-id="8624e-1045">Inoltre, se `C` è diverso da un tipo di classe `object`, tutti i metodi dichiarati in un tipo di interfaccia vengono rimosse dal set.</span><span class="sxs-lookup"><span data-stu-id="8624e-1045">Furthermore, if `C` is a class type other than `object`, all methods declared in an interface type are removed from the set.</span></span> <span data-ttu-id="8624e-1046">(Questa regola di quest'ultima ha effetto solo quando il gruppo di metodi è il risultato di una ricerca di membri in un parametro di tipo con una classe base effettiva diverso dall'oggetto e un insieme di interfacce efficace non vuoto.)</span><span class="sxs-lookup"><span data-stu-id="8624e-1046">(This latter rule only has affect when the method group was the result of a member lookup on a type parameter having an effective base class other than object and a non-empty effective interface set.)</span></span>
*  <span data-ttu-id="8624e-1047">Se il set di metodi candidati risultante è vuoto, l'elaborazione e i passaggi seguenti non vengono applicate e invece viene effettuato un tentativo di elaborare la chiamata come una chiamata al metodo di estensione ([chiamate al metodo di estensione](expressions.md#extension-method-invocations)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1047">If the resulting set of candidate methods is empty, then further processing along the following steps are abandoned, and instead an attempt is made to process the invocation as an extension method invocation ([Extension method invocations](expressions.md#extension-method-invocations)).</span></span> <span data-ttu-id="8624e-1048">In caso di errore, quindi esiste alcun metodo applicabile, e si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1048">If this fails, then no applicable methods exist, and a binding-time error occurs.</span></span>
*  <span data-ttu-id="8624e-1049">Il metodo migliore del set di metodi candidati viene identificato mediante le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="8624e-1049">The best method of the set of candidate methods is identified using the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="8624e-1050">Se non è possibile identificare un metodo migliore, la chiamata al metodo è ambiguo e si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1050">If a single best method cannot be identified, the method invocation is ambiguous, and a binding-time error occurs.</span></span> <span data-ttu-id="8624e-1051">Quando si esegue la risoluzione dell'overload, i parametri di un metodo generico sono considerati dopo la sostituzione di argomenti di tipo (specificati o dedotti) per i parametri di tipo di metodo corrispondente.</span><span class="sxs-lookup"><span data-stu-id="8624e-1051">When performing overload resolution, the parameters of a generic method are considered after substituting the type arguments (supplied or inferred) for the corresponding method type parameters.</span></span>
*  <span data-ttu-id="8624e-1052">Viene eseguita la convalida finale del metodo migliore scelta:</span><span class="sxs-lookup"><span data-stu-id="8624e-1052">Final validation of the chosen best method is performed:</span></span>
   * <span data-ttu-id="8624e-1053">Il metodo viene convalidato nel contesto del gruppo di metodi: Se il metodo migliore è un metodo statico, il gruppo di metodi deve derivare da una *simple_name* o una *member_access* tramite un tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1053">The method is validated in the context of the method group: If the best method is a static method, the method group must have resulted from a *simple_name* or a *member_access* through a type.</span></span> <span data-ttu-id="8624e-1054">Se il metodo migliore è un metodo di istanza, il gruppo di metodi deve derivare da una *simple_name*, un *member_access* tramite una variabile o un valore, o un *base_access*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1054">If the best method is an instance method, the method group must have resulted from a *simple_name*, a *member_access* through a variable or value, or a *base_access*.</span></span> <span data-ttu-id="8624e-1055">Se nessuno di questi requisiti è true, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1055">If neither of these requirements is true, a binding-time error occurs.</span></span>
   * <span data-ttu-id="8624e-1056">Se il metodo migliore è un metodo generico, gli argomenti di tipo (fornito o derivato) vengono controllati in base ai vincoli ([che soddisfano i vincoli](types.md#satisfying-constraints)) dichiarati nel metodo generico.</span><span class="sxs-lookup"><span data-stu-id="8624e-1056">If the best method is a generic method, the type arguments (supplied or inferred) are checked against the constraints ([Satisfying constraints](types.md#satisfying-constraints)) declared on the generic method.</span></span> <span data-ttu-id="8624e-1057">Se qualsiasi argomento di tipo non soddisfa i relativi vincoli sul parametro di tipo, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1057">If any type argument does not satisfy the corresponding constraint(s) on the type parameter, a binding-time error occurs.</span></span>

<span data-ttu-id="8624e-1058">Dopo aver selezionato un metodo ed convalidato in fase di associazione per i passaggi precedenti, la chiamata in fase di esecuzione effettiva viene elaborata in base alle regole di chiamata di funzione membro descritto in [controllo della risoluzione dell'overload dinamico in fase di compilazione ](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="8624e-1058">Once a method has been selected and validated at binding-time by the above steps, the actual run-time invocation is processed according to the rules of function member invocation described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="8624e-1059">L'effetto intuitiva delle regole di risoluzione descritto in precedenza è come segue: Per individuare il metodo particolare richiamato da una chiamata al metodo, iniziare con il tipo indicato dalla chiamata del metodo e procedere fino alla catena di ereditarietà fino a quando non viene trovata almeno un metodo applicabile, accessibile, non-override dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1059">The intuitive effect of the resolution rules described above is as follows: To locate the particular method invoked by a method invocation, start with the type indicated by the method invocation and proceed up the inheritance chain until at least one applicable, accessible, non-override method declaration is found.</span></span> <span data-ttu-id="8624e-1060">Quindi eseguire l'inferenza del tipo e sul set di metodi applicabili, accessibili, non-override dichiarati in quel tipo di risoluzione dell'overload e richiamare il metodo così selezionato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1060">Then perform type inference and overload resolution on the set of applicable, accessible, non-override methods declared in that type and invoke the method thus selected.</span></span> <span data-ttu-id="8624e-1061">Se è stato trovato alcun metodo, provare invece a elaborare la chiamata come una chiamata al metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1061">If no method was found, try instead to process the invocation as an extension method invocation.</span></span>

#### <a name="extension-method-invocations"></a><span data-ttu-id="8624e-1062">Chiamate ai metodi di estensione</span><span class="sxs-lookup"><span data-stu-id="8624e-1062">Extension method invocations</span></span>

<span data-ttu-id="8624e-1063">In una chiamata al metodo ([chiamate nelle istanze di tipo boxed](expressions.md#invocations-on-boxed-instances)) di uno dei formati</span><span class="sxs-lookup"><span data-stu-id="8624e-1063">In a method invocation ([Invocations on boxed instances](expressions.md#invocations-on-boxed-instances)) of one of the forms</span></span>
```csharp
expr . identifier ( )

expr . identifier ( args )

expr . identifier < typeargs > ( )

expr . identifier < typeargs > ( args )
```
<span data-ttu-id="8624e-1064">Se la normale elaborazione della chiamata di non trova alcun metodo applicabile, viene effettuato un tentativo di elaborare il costrutto come una chiamata al metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1064">if the normal processing of the invocation finds no applicable methods, an attempt is made to process the construct as an extension method invocation.</span></span> <span data-ttu-id="8624e-1065">Se *expr* o uno qualsiasi delle *args* dispone di tipi in fase di compilazione `dynamic`, i metodi di estensione non verranno applicata.</span><span class="sxs-lookup"><span data-stu-id="8624e-1065">If *expr* or any of the *args* has compile-time type `dynamic`, extension methods will not apply.</span></span>

<span data-ttu-id="8624e-1066">L'obiettivo consiste nel trovare il meglio *type_name* `C`, in modo che la chiamata del metodo statico corrispondente può essere eseguita:</span><span class="sxs-lookup"><span data-stu-id="8624e-1066">The objective is to find the best *type_name* `C`, so that the corresponding static method invocation can take place:</span></span>
```csharp
C . identifier ( expr )

C . identifier ( expr , args )

C . identifier < typeargs > ( expr )

C . identifier < typeargs > ( expr , args )
```

<span data-ttu-id="8624e-1067">Un metodo di estensione `Ci.Mj` viene ***idonei*** se:</span><span class="sxs-lookup"><span data-stu-id="8624e-1067">An extension method `Ci.Mj` is ***eligible*** if:</span></span>

*  <span data-ttu-id="8624e-1068">`Ci` è una classe non generica, non annidata</span><span class="sxs-lookup"><span data-stu-id="8624e-1068">`Ci` is a non-generic, non-nested class</span></span>
*  <span data-ttu-id="8624e-1069">Il nome della `Mj` è *identificatore*</span><span class="sxs-lookup"><span data-stu-id="8624e-1069">The name of `Mj` is *identifier*</span></span>
*  <span data-ttu-id="8624e-1070">`Mj` è accessibile e applicabili quando vengono applicati agli argomenti come un metodo statico, come illustrato in precedenza</span><span class="sxs-lookup"><span data-stu-id="8624e-1070">`Mj` is accessible and applicable when applied to the arguments as a static method as shown above</span></span>
*  <span data-ttu-id="8624e-1071">Esiste un'identità implicita, riferimento o conversione boxing dal *expr* al tipo del primo parametro di `Mj`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1071">An implicit identity, reference or boxing conversion exists from *expr* to the type of the first parameter of `Mj`.</span></span>

<span data-ttu-id="8624e-1072">La ricerca di `C` procede nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="8624e-1072">The search for `C` proceeds as follows:</span></span>

*  <span data-ttu-id="8624e-1073">Inizia con la dichiarazione dello spazio dei nomi contenitore più vicino, continuare con ogni dichiarazione dello spazio dei nomi che lo contiene e che termina con unità di compilazione che lo contiene, verranno eseguiti tentativi successivi per individuare un set di candidati dei metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="8624e-1073">Starting with the closest enclosing namespace declaration, continuing with each enclosing namespace declaration, and ending with the containing compilation unit, successive attempts are made to find a candidate set of extension methods:</span></span>
   * <span data-ttu-id="8624e-1074">Se l'unità di compilazione o di spazio dei nomi specificato contiene direttamente le dichiarazioni di tipo non generico `Ci` con i metodi di estensione idonei `Mj`, quindi il set di questi metodi di estensione è il set di candidati.</span><span class="sxs-lookup"><span data-stu-id="8624e-1074">If the given namespace or compilation unit directly contains non-generic type declarations `Ci` with eligible extension methods `Mj`, then the set of those extension methods is the candidate set.</span></span>
   * <span data-ttu-id="8624e-1075">Se i tipi `Ci` importato dal *using_static_declarations* e direttamente dichiarati negli spazi dei nomi importato da *using_namespace_directive*s nella compilazione o spazio dei nomi di unità specificata direttamente contiene i metodi di estensione idonei `Mj`, quindi il set di questi metodi di estensione è il set di candidati.</span><span class="sxs-lookup"><span data-stu-id="8624e-1075">If types `Ci` imported by *using_static_declarations* and directly declared in namespaces imported by *using_namespace_directive*s in the given namespace or compilation unit directly contain eligible extension methods `Mj`, then the set of those extension methods is the candidate set.</span></span>
*  <span data-ttu-id="8624e-1076">Se non viene trovato alcun set di candidati in qualsiasi unità di compilazione o di dichiarazione dello spazio dei nomi che lo contiene, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1076">If no candidate set is found in any enclosing namespace declaration or compilation unit, a compile-time error occurs.</span></span>
*  <span data-ttu-id="8624e-1077">In caso contrario, la risoluzione dell'overload viene applicata al candidato impostato come descritto in ([risoluzione dell'Overload](expressions.md#overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1077">Otherwise, overload resolution is applied to the candidate set as described in ([Overload resolution](expressions.md#overload-resolution)).</span></span> <span data-ttu-id="8624e-1078">Se non viene trovato alcun metodo migliore, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1078">If no single best method is found, a compile-time error occurs.</span></span>
*  <span data-ttu-id="8624e-1079">`C` è il tipo all'interno del quale il metodo migliore è dichiarato come metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1079">`C` is the type within which the best method is declared as an extension method.</span></span>

<span data-ttu-id="8624e-1080">Usando `C` come destinazione, la chiamata al metodo viene quindi elaborata come una chiamata al metodo statico ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1080">Using `C` as a target, the method call is then processed as a static method invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span></span>

<span data-ttu-id="8624e-1081">Le regole precedenti indicano che i metodi di istanza hanno la precedenza sui metodi di estensione, che i metodi di estensione disponibili nelle dichiarazioni dello spazio dei nomi interni hanno la precedenza sui metodi di estensione disponibili nelle dichiarazioni di spazio dei nomi esterno e tale estensione i metodi dichiarati direttamente in uno spazio dei nomi hanno la precedenza sui metodi di estensione importati in tale spazio dei nomi stesso con un utilizzo della direttiva dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="8624e-1081">The preceding rules mean that instance methods take precedence over extension methods, that extension methods available in inner namespace declarations take precedence over extension methods available in outer namespace declarations, and that extension methods declared directly in a namespace take precedence over extension methods imported into that same namespace with a using namespace directive.</span></span> <span data-ttu-id="8624e-1082">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8624e-1082">For example:</span></span>
```csharp
public static class E
{
    public static void F(this object obj, int i) { }

    public static void F(this object obj, string s) { }
}

class A { }

class B
{
    public void F(int i) { }
}

class C
{
    public void F(object obj) { }
}

class X
{
    static void Test(A a, B b, C c) {
        a.F(1);              // E.F(object, int)
        a.F("hello");        // E.F(object, string)

        b.F(1);              // B.F(int)
        b.F("hello");        // E.F(object, string)

        c.F(1);              // C.F(object)
        c.F("hello");        // C.F(object)
    }
}
```

<span data-ttu-id="8624e-1083">Nell'esempio riportato `B`del metodo ha la precedenza sul primo metodo di estensione, e `C`del metodo ha la precedenza su entrambi i metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1083">In the example, `B`'s method takes precedence over the first extension method, and `C`'s method takes precedence over both extension methods.</span></span>

```csharp
public static class C
{
    public static void F(this int i) { Console.WriteLine("C.F({0})", i); }
    public static void G(this int i) { Console.WriteLine("C.G({0})", i); }
    public static void H(this int i) { Console.WriteLine("C.H({0})", i); }
}

namespace N1
{
    public static class D
    {
        public static void F(this int i) { Console.WriteLine("D.F({0})", i); }
        public static void G(this int i) { Console.WriteLine("D.G({0})", i); }
    }
}

namespace N2
{
    using N1;

    public static class E
    {
        public static void F(this int i) { Console.WriteLine("E.F({0})", i); }
    }

    class Test
    {
        static void Main(string[] args)
        {
            1.F();
            2.G();
            3.H();
        }
    }
}
```

<span data-ttu-id="8624e-1084">L'output di questo esempio è:</span><span class="sxs-lookup"><span data-stu-id="8624e-1084">The output of this example is:</span></span>
```
E.F(1)
D.G(2)
C.H(3)
```
<span data-ttu-id="8624e-1085">`D.G` ha la precedenza sul `C.G`, e `E.F` ha la precedenza rispetto a entrambe `D.F` e `C.F`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1085">`D.G` takes precedence over `C.G`, and `E.F` takes precedence over both `D.F` and `C.F`.</span></span>

#### <a name="delegate-invocations"></a><span data-ttu-id="8624e-1086">Chiamate del delegato</span><span class="sxs-lookup"><span data-stu-id="8624e-1086">Delegate invocations</span></span>

<span data-ttu-id="8624e-1087">Per una chiamata al delegato, il *primary_expression* delle *invocation_expression* deve essere un valore di una *delegate_type*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1087">For a delegate invocation, the *primary_expression* of the *invocation_expression* must be a value of a *delegate_type*.</span></span> <span data-ttu-id="8624e-1088">Inoltre, prendere in considerazione la *delegate_type* sia un membro di funzione con lo stesso elenco di parametri del *delegate_type*, il *delegate_type* deve essere (applicabile [Membro di funzione applicabile](expressions.md#applicable-function-member)) rispetto al *argument_list* del *invocation_expression*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1088">Furthermore, considering the *delegate_type* to be a function member with the same parameter list as the *delegate_type*, the *delegate_type* must be applicable ([Applicable function member](expressions.md#applicable-function-member)) with respect to the *argument_list* of the *invocation_expression*.</span></span>

<span data-ttu-id="8624e-1089">L'elaborazione in fase di esecuzione di una chiamata al delegato del form `D(A)`, dove `D` è un *primary_expression* di un *delegate_type* e `A` è un facoltativo*argument_list*, costituita dai passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1089">The run-time processing of a delegate invocation of the form `D(A)`, where `D` is a *primary_expression* of a *delegate_type* and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="8624e-1090">`D` viene valutato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1090">`D` is evaluated.</span></span> <span data-ttu-id="8624e-1091">Se questa versione di valutazione causa un'eccezione, non verrà eseguito alcun ulteriore passaggio.</span><span class="sxs-lookup"><span data-stu-id="8624e-1091">If this evaluation causes an exception, no further steps are executed.</span></span>
*  <span data-ttu-id="8624e-1092">Il valore di `D` viene verificata la validità.</span><span class="sxs-lookup"><span data-stu-id="8624e-1092">The value of `D` is checked to be valid.</span></span> <span data-ttu-id="8624e-1093">Se il valore di `D` viene `null`, un `System.NullReferenceException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.</span><span class="sxs-lookup"><span data-stu-id="8624e-1093">If the value of `D` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="8624e-1094">In caso contrario, `D` è un riferimento a un'istanza del delegato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1094">Otherwise, `D` is a reference to a delegate instance.</span></span> <span data-ttu-id="8624e-1095">Membro chiamate di funzione ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) vengono eseguite su ogni entità richiamabile nell'elenco chiamate del delegato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1095">Function member invocations ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) are performed on each of the callable entities in the invocation list of the delegate.</span></span> <span data-ttu-id="8624e-1096">Per le entità richiamabile costituito da un'istanza e un metodo di istanza, l'istanza per la chiamata è l'istanza contenuta nell'entità richiamabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-1096">For callable entities consisting of an instance and instance method, the instance for the invocation is the instance contained in the callable entity.</span></span>

### <a name="element-access"></a><span data-ttu-id="8624e-1097">Accesso all'elemento</span><span class="sxs-lookup"><span data-stu-id="8624e-1097">Element access</span></span>

<span data-ttu-id="8624e-1098">Un' *element_access* costituito da un *primary_no_array_creation_expression*, seguito da un "`[`" token, seguito da un *argument_list*, seguito da un " `]`"token.</span><span class="sxs-lookup"><span data-stu-id="8624e-1098">An *element_access* consists of a *primary_no_array_creation_expression*, followed by a "`[`" token, followed by an *argument_list*, followed by a "`]`" token.</span></span> <span data-ttu-id="8624e-1099">Il *argument_list* costituito da uno o più *argomento*s, separati da virgole.</span><span class="sxs-lookup"><span data-stu-id="8624e-1099">The *argument_list* consists of one or more *argument*s, separated by commas.</span></span>

```antlr
element_access
    : primary_no_array_creation_expression '[' expression_list ']'
    ;
```

<span data-ttu-id="8624e-1100">Il *argument_list* di un *element_access* non può contenere `ref` o `out` argomenti.</span><span class="sxs-lookup"><span data-stu-id="8624e-1100">The *argument_list* of an *element_access* is not allowed to contain `ref` or `out` arguments.</span></span>

<span data-ttu-id="8624e-1101">Un' *element_access* in modo dinamico è associato ([binding dinamico](expressions.md#dynamic-binding)) se contiene almeno uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1101">An *element_access* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)) if at least one of the following holds:</span></span>

* <span data-ttu-id="8624e-1102">Il *primary_no_array_creation_expression* è di tipo in fase di compilazione `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1102">The *primary_no_array_creation_expression* has compile-time type `dynamic`.</span></span>
* <span data-ttu-id="8624e-1103">Almeno un'espressione del *argument_list* è di tipo in fase di compilazione `dynamic` e il *primary_no_array_creation_expression* non dispone di un tipo di matrice.</span><span class="sxs-lookup"><span data-stu-id="8624e-1103">At least one expression of the *argument_list* has compile-time type `dynamic` and the *primary_no_array_creation_expression* does not have an array type.</span></span>

<span data-ttu-id="8624e-1104">In questo caso il compilatore consente di classificare le *element_access* come valore di tipo `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1104">In this case the compiler classifies the *element_access* as a value of type `dynamic`.</span></span> <span data-ttu-id="8624e-1105">Le regole seguenti per determinare il significato del *element_access* vengono quindi applicati in fase di esecuzione, usando il tipo di fase di esecuzione anziché al tipo di fase di compilazione di quelle del *primary_no_array_creation_expression*e *argument_list* espressioni che hanno il tipo di fase di compilazione `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1105">The rules below to determine the meaning of the *element_access* are then applied at run-time, using the run-time type instead of the compile-time type of those of the *primary_no_array_creation_expression* and *argument_list* expressions which have the compile-time type `dynamic`.</span></span> <span data-ttu-id="8624e-1106">Se il *primary_no_array_creation_expression* non è in fase di compilazione `dynamic`, quindi l'accesso all'elemento viene sottoposto a un controllo in fase di compilazione limitato come descritto in [controllo dinamico in fase di compilazione risoluzione dell'overload](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="8624e-1106">If the *primary_no_array_creation_expression* does not have compile-time type `dynamic`, then the element access undergoes a limited compile time check as described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="8624e-1107">Se il *primary_no_array_creation_expression* di un *element_access* è un valore di un *array_type*, il *element_access* è un Matrice di accesso ([matrice accesso](expressions.md#array-access)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1107">If the *primary_no_array_creation_expression* of an *element_access* is a value of an *array_type*, the *element_access* is an array access ([Array access](expressions.md#array-access)).</span></span> <span data-ttu-id="8624e-1108">In caso contrario, il *primary_no_array_creation_expression* deve essere una variabile o un valore di una classe, struct o tipo di interfaccia che ha uno o più membri di un indicizzatore, nel qual caso il *element_access* è un accesso all'indicizzatore ([accesso all'indicizzatore](expressions.md#indexer-access)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1108">Otherwise, the *primary_no_array_creation_expression* must be a variable or value of a class, struct, or interface type that has one or more indexer members, in which case the *element_access* is an indexer access ([Indexer access](expressions.md#indexer-access)).</span></span>

#### <a name="array-access"></a><span data-ttu-id="8624e-1109">Accesso a matrici</span><span class="sxs-lookup"><span data-stu-id="8624e-1109">Array access</span></span>

<span data-ttu-id="8624e-1110">Per un accesso a matrice, il *primary_no_array_creation_expression* delle *element_access* deve essere un valore di un *array_type*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1110">For an array access, the *primary_no_array_creation_expression* of the *element_access* must be a value of an *array_type*.</span></span> <span data-ttu-id="8624e-1111">Inoltre, il *argument_list* di una matrice per contenere gli argomenti denominati non è consentito l'accesso. Il numero di espressioni presenti il *argument_list* deve essere uguale all'ordine di priorità del *array_type*, e ogni espressione deve essere di tipo `int`, `uint`, `long`, `ulong`, oppure deve essere convertibile in modo implicito a uno o più di questi tipi.</span><span class="sxs-lookup"><span data-stu-id="8624e-1111">Furthermore, the *argument_list* of an array access is not allowed to contain named arguments.The number of expressions in the *argument_list* must be the same as the rank of the *array_type*, and each expression must be of type `int`, `uint`, `long`, `ulong`, or must be implicitly convertible to one or more of these types.</span></span>

<span data-ttu-id="8624e-1112">Il risultato della valutazione di un accesso a matrice è una variabile del tipo di elemento della matrice, vale a dire l'elemento della matrice selezionata in base ai valori delle espressioni disponibili nel *argument_list*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1112">The result of evaluating an array access is a variable of the element type of the array, namely the array element selected by the value(s) of the expression(s) in the *argument_list*.</span></span>

<span data-ttu-id="8624e-1113">L'elaborazione in fase di esecuzione di un accesso a matrice nel formato `P[A]`, dove `P` è un *primary_no_array_creation_expression* di un *array_type* e `A` è un *argument_list*, costituita dai passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1113">The run-time processing of an array access of the form `P[A]`, where `P` is a *primary_no_array_creation_expression* of an *array_type* and `A` is an *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="8624e-1114">`P` viene valutato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1114">`P` is evaluated.</span></span> <span data-ttu-id="8624e-1115">Se questa versione di valutazione causa un'eccezione, non verrà eseguito alcun ulteriore passaggio.</span><span class="sxs-lookup"><span data-stu-id="8624e-1115">If this evaluation causes an exception, no further steps are executed.</span></span>
*  <span data-ttu-id="8624e-1116">Le espressioni di indice del *argument_list* vengono valutati nell'ordine, da sinistra a destra.</span><span class="sxs-lookup"><span data-stu-id="8624e-1116">The index expressions of the *argument_list* are evaluated in order, from left to right.</span></span> <span data-ttu-id="8624e-1117">Dopo la valutazione di ogni espressione di indice, una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) viene eseguita su uno dei seguenti tipi: `int`, `uint`, `long`, `ulong`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1117">Following evaluation of each index expression, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) to one of the following types is performed: `int`, `uint`, `long`, `ulong`.</span></span> <span data-ttu-id="8624e-1118">Viene scelto il primo tipo in questo elenco per il quale esiste una conversione implicita.</span><span class="sxs-lookup"><span data-stu-id="8624e-1118">The first type in this list for which an implicit conversion exists is chosen.</span></span> <span data-ttu-id="8624e-1119">Ad esempio, se l'espressione di indice è di tipo `short` quindi una conversione implicita a `int` verrà eseguita, poiché le conversioni implicite dal `short` al `int` e da `short` per `long` sono possibili.</span><span class="sxs-lookup"><span data-stu-id="8624e-1119">For instance, if the index expression is of type `short` then an implicit conversion to `int` is performed, since implicit conversions from `short` to `int` and from `short` to `long` are possible.</span></span> <span data-ttu-id="8624e-1120">Se la valutazione di un'espressione di indice o la conversione implicita successive genera un'eccezione, non altre espressioni di indice vengono valutate e senza ulteriori passaggi vengono eseguiti.</span><span class="sxs-lookup"><span data-stu-id="8624e-1120">If evaluation of an index expression or the subsequent implicit conversion causes an exception, then no further index expressions are evaluated and no further steps are executed.</span></span>
*  <span data-ttu-id="8624e-1121">Il valore di `P` viene verificata la validità.</span><span class="sxs-lookup"><span data-stu-id="8624e-1121">The value of `P` is checked to be valid.</span></span> <span data-ttu-id="8624e-1122">Se il valore di `P` viene `null`, un `System.NullReferenceException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.</span><span class="sxs-lookup"><span data-stu-id="8624e-1122">If the value of `P` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="8624e-1123">Il valore di ogni espressione nel *argument_list* viene confrontato con i limiti di ciascuna dimensione dell'istanza della matrice cui fa riferimento `P`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1123">The value of each expression in the *argument_list* is checked against the actual bounds of each dimension of the array instance referenced by `P`.</span></span> <span data-ttu-id="8624e-1124">Se uno o più valori sono compreso nell'intervallo, un `System.IndexOutOfRangeException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.</span><span class="sxs-lookup"><span data-stu-id="8624e-1124">If one or more values are out of range, a `System.IndexOutOfRangeException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="8624e-1125">Viene calcolato il percorso dell'elemento della matrice specificata da espressioni indice; e questa località diventa il risultato dell'accesso alla matrice.</span><span class="sxs-lookup"><span data-stu-id="8624e-1125">The location of the array element given by the index expression(s) is computed, and this location becomes the result of the array access.</span></span>

#### <a name="indexer-access"></a><span data-ttu-id="8624e-1126">Accesso all'indicizzatore</span><span class="sxs-lookup"><span data-stu-id="8624e-1126">Indexer access</span></span>

<span data-ttu-id="8624e-1127">Per un accesso all'indicizzatore, il *primary_no_array_creation_expression* delle *element_access* deve essere una variabile o un valore di una classe, struct o tipo di interfaccia, e questo tipo deve implementare una o più agli indicizzatori che sono applicabili in base al *argument_list* delle *element_access*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1127">For an indexer access, the *primary_no_array_creation_expression* of the *element_access* must be a variable or value of a class, struct, or interface type, and this type must implement one or more indexers that are applicable with respect to the *argument_list* of the *element_access*.</span></span>

<span data-ttu-id="8624e-1128">L'elaborazione in fase di associazione di un accesso dell'indicizzatore nel formato `P[A]`, dove `P` è un *primary_no_array_creation_expression* di una classe, struct o tipo di interfaccia `T`, e `A` è un *argument_list*, costituita dai passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1128">The binding-time processing of an indexer access of the form `P[A]`, where `P` is a *primary_no_array_creation_expression* of a class, struct, or interface type `T`, and `A` is an *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="8624e-1129">Il set di indicizzatori fornita da `T` viene costruito.</span><span class="sxs-lookup"><span data-stu-id="8624e-1129">The set of indexers provided by `T` is constructed.</span></span> <span data-ttu-id="8624e-1130">Il set è costituito da tutti gli indicizzatori dichiarati nella `T` o un tipo di base del `T` che non sono presenti `override` dichiarazioni e sono accessibili nel contesto corrente ([accesso ai membri](basic-concepts.md#member-access)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1130">The set consists of all indexers declared in `T` or a base type of `T` that are not `override` declarations and are accessible in the current context ([Member access](basic-concepts.md#member-access)).</span></span>
*  <span data-ttu-id="8624e-1131">Il set viene ridotto a questi indicizzatori che sono applicabili e non è nascosto da altri indicizzatori.</span><span class="sxs-lookup"><span data-stu-id="8624e-1131">The set is reduced to those indexers that are applicable and not hidden by other indexers.</span></span> <span data-ttu-id="8624e-1132">Le regole seguenti vengono applicate a ogni indicizzatore `S.I` nel set, in cui `S` è il tipo in cui l'indicizzatore `I` viene dichiarato:</span><span class="sxs-lookup"><span data-stu-id="8624e-1132">The following rules are applied to each indexer `S.I` in the set, where `S` is the type in which the indexer `I` is declared:</span></span>
   * <span data-ttu-id="8624e-1133">Se `I` non è applicabile alle `A` ([membro di funzione applicabile](expressions.md#applicable-function-member)), quindi `I` viene rimosso dal set.</span><span class="sxs-lookup"><span data-stu-id="8624e-1133">If `I` is not applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)), then `I` is removed from the set.</span></span>
   * <span data-ttu-id="8624e-1134">Se `I` è applicabile alle `A` ([membro di funzione applicabile](expressions.md#applicable-function-member)), quindi tutti gli indicizzatori dichiarati nel tipo di base `S` vengono rimosse dal set.</span><span class="sxs-lookup"><span data-stu-id="8624e-1134">If `I` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)), then all indexers declared in a base type of `S` are removed from the set.</span></span>
   * <span data-ttu-id="8624e-1135">Se `I` è applicabile alle `A` ([membro di funzione applicabile](expressions.md#applicable-function-member)) e `S` è diverso da un tipo di classe `object`, tutti gli indicizzatori dichiarati in un'interfaccia vengono rimosse dal set.</span><span class="sxs-lookup"><span data-stu-id="8624e-1135">If `I` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)) and `S` is a class type other than `object`, all indexers declared in an interface are removed from the set.</span></span>
*  <span data-ttu-id="8624e-1136">Se il set di indicizzatori candidati risultante è vuoto, quindi non esistono alcun indicizzatore applicabile, e si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1136">If the resulting set of candidate indexers is empty, then no applicable indexers exist, and a binding-time error occurs.</span></span>
*  <span data-ttu-id="8624e-1137">L'indicizzatore del set di indicizzatori candidato migliore viene identificato mediante le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="8624e-1137">The best indexer of the set of candidate indexers is identified using the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="8624e-1138">Se non è possibile identificare un singolo indicizzatore migliore, l'accesso all'indicizzatore è ambiguo e si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1138">If a single best indexer cannot be identified, the indexer access is ambiguous, and a binding-time error occurs.</span></span>
*  <span data-ttu-id="8624e-1139">Le espressioni di indice del *argument_list* vengono valutati nell'ordine, da sinistra a destra.</span><span class="sxs-lookup"><span data-stu-id="8624e-1139">The index expressions of the *argument_list* are evaluated in order, from left to right.</span></span> <span data-ttu-id="8624e-1140">Il risultato dell'elaborazione l'accesso all'indicizzatore è un'espressione classificata come un accesso all'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1140">The result of processing the indexer access is an expression classified as an indexer access.</span></span> <span data-ttu-id="8624e-1141">L'espressione di accesso dell'indicizzatore fa riferimento all'indicizzatore definito nel passaggio precedente e dispone di un'espressione dell'istanza associata di `P` e un elenco di argomenti di `A`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1141">The indexer access expression references the indexer determined in the step above, and has an associated instance expression of `P` and an associated argument list of `A`.</span></span>

<span data-ttu-id="8624e-1142">A seconda del contesto in cui viene utilizzato, un accesso all'indicizzatore fa sì che la chiamata di uno il *funzione di accesso get* o nella *funzione di accesso set* dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1142">Depending on the context in which it is used, an indexer access causes invocation of either the *get accessor* or the *set accessor* of the indexer.</span></span> <span data-ttu-id="8624e-1143">Se l'accesso all'indicizzatore è la destinazione di un'assegnazione, il *funzione di accesso set* viene richiamata per assegnare un nuovo valore ([assegnamento semplice](expressions.md#simple-assignment)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1143">If the indexer access is the target of an assignment, the *set accessor* is invoked to assign a new value ([Simple assignment](expressions.md#simple-assignment)).</span></span> <span data-ttu-id="8624e-1144">In tutti gli altri casi, il *funzione di accesso get* viene richiamato per ottenere il valore corrente ([i valori delle espressioni](expressions.md#values-of-expressions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1144">In all other cases, the *get accessor* is invoked to obtain the current value ([Values of expressions](expressions.md#values-of-expressions)).</span></span>

### <a name="this-access"></a><span data-ttu-id="8624e-1145">Questo tipo di accesso</span><span class="sxs-lookup"><span data-stu-id="8624e-1145">This access</span></span>

<span data-ttu-id="8624e-1146">Oggetto *this_access* costituito dalla parola riservata `this`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1146">A *this_access* consists of the reserved word `this`.</span></span>

```antlr
this_access
    : 'this'
    ;
```

<span data-ttu-id="8624e-1147">Oggetto *this_access* è consentito solo nel *blocco* di un costruttore di istanza, un metodo di istanza o una funzione di accesso di istanza.</span><span class="sxs-lookup"><span data-stu-id="8624e-1147">A *this_access* is permitted only in the *block* of an instance constructor, an instance method, or an instance accessor.</span></span> <span data-ttu-id="8624e-1148">Ha una dei significati seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1148">It has one of the following meanings:</span></span>

*  <span data-ttu-id="8624e-1149">Quando `this` viene usato in un *primary_expression* all'interno di un costruttore di istanza di una classe, viene classificata come un valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1149">When `this` is used in a *primary_expression* within an instance constructor of a class, it is classified as a value.</span></span> <span data-ttu-id="8624e-1150">Il tipo del valore è il tipo di istanza ([il tipo di istanza](classes.md#the-instance-type)) della classe all'interno del quale si verifica l'utilizzo e il valore è un riferimento all'oggetto in fase di costruzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1150">The type of the value is the instance type ([The instance type](classes.md#the-instance-type)) of the class within which the usage occurs, and the value is a reference to the object being constructed.</span></span>
*  <span data-ttu-id="8624e-1151">Quando `this` viene usato in un *primary_expression* all'interno di un metodo di istanza o una funzione di accesso di istanza di una classe, viene classificata come un valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1151">When `this` is used in a *primary_expression* within an instance method or instance accessor of a class, it is classified as a value.</span></span> <span data-ttu-id="8624e-1152">Il tipo del valore è il tipo di istanza ([il tipo di istanza](classes.md#the-instance-type)) della classe all'interno del quale si verifica l'utilizzo e il valore è un riferimento all'oggetto per cui è stato richiamato il metodo o funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="8624e-1152">The type of the value is the instance type ([The instance type](classes.md#the-instance-type)) of the class within which the usage occurs, and the value is a reference to the object for which the method or accessor was invoked.</span></span>
*  <span data-ttu-id="8624e-1153">Quando `this` viene usato in un *primary_expression* all'interno di un costruttore di istanza di uno struct, viene classificata come una variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-1153">When `this` is used in a *primary_expression* within an instance constructor of a struct, it is classified as a variable.</span></span> <span data-ttu-id="8624e-1154">Il tipo della variabile è il tipo di istanza ([il tipo di istanza](classes.md#the-instance-type)) dello struct all'interno del quale si verifica l'utilizzo e la variabile rappresenta la struttura in fase di costruzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1154">The type of the variable is the instance type ([The instance type](classes.md#the-instance-type)) of the struct within which the usage occurs, and the variable represents the struct being constructed.</span></span> <span data-ttu-id="8624e-1155">Il `this` variabile di un costruttore di istanze di uno struct si comporta esattamente come un `out` parametro del tipo struct, in particolare, ciò significa che la variabile deve essere assegnata in ogni percorso di esecuzione dell'istanza costruttore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1155">The `this` variable of an instance constructor of a struct behaves exactly the same as an `out` parameter of the struct type—in particular, this means that the variable must be definitely assigned in every execution path of the instance constructor.</span></span>
*  <span data-ttu-id="8624e-1156">Quando `this` viene usato in un *primary_expression* all'interno di un metodo di istanza o una funzione di accesso di istanza di uno struct, viene classificata come una variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-1156">When `this` is used in a *primary_expression* within an instance method or instance accessor of a struct, it is classified as a variable.</span></span> <span data-ttu-id="8624e-1157">Il tipo della variabile è il tipo di istanza ([il tipo di istanza](classes.md#the-instance-type)) dello struct in cui viene effettuata l'utilizzo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1157">The type of the variable is the instance type ([The instance type](classes.md#the-instance-type)) of the struct within which the usage occurs.</span></span>
   * <span data-ttu-id="8624e-1158">Se il metodo o funzione di accesso non è un iteratore ([iteratori](classes.md#iterators)), il `this` variabile rappresenta la struttura per il quale il metodo o funzione di accesso è stato richiamato e si comporta esattamente come un `ref` parametro del tipo struct.</span><span class="sxs-lookup"><span data-stu-id="8624e-1158">If the method or accessor is not an iterator ([Iterators](classes.md#iterators)), the `this` variable represents the struct for which the method or accessor was invoked, and behaves exactly the same as a `ref` parameter of the struct type.</span></span>
   * <span data-ttu-id="8624e-1159">Se il metodo o funzione di accesso è un iteratore di `this` variabile rappresenta una copia della struttura per il quale il metodo o funzione di accesso è stato richiamato e si comporta esattamente come un parametro di valore del tipo struct.</span><span class="sxs-lookup"><span data-stu-id="8624e-1159">If the method or accessor is an iterator, the `this` variable represents a copy of the struct for which the method or accessor was invoked, and behaves exactly the same as a value parameter of the struct type.</span></span>

<span data-ttu-id="8624e-1160">Sfrutta `this` in un *primary_expression* in un contesto diverso da quelli elencati in precedenza è un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1160">Use of `this` in a *primary_expression* in a context other than the ones listed above is a compile-time error.</span></span> <span data-ttu-id="8624e-1161">In particolare, non è possibile fare riferimento a `this` in un metodo statico, una funzione di accesso proprietà statica o in un *variable_initializer* di una dichiarazione di campo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1161">In particular, it is not possible to refer to `this` in a static method, a static property accessor, or in a *variable_initializer* of a field declaration.</span></span>

### <a name="base-access"></a><span data-ttu-id="8624e-1162">Accesso di base</span><span class="sxs-lookup"><span data-stu-id="8624e-1162">Base access</span></span>

<span data-ttu-id="8624e-1163">Oggetto *base_access* costituito dalla parola riservata `base` seguita da un "`.`" token e un identificatore o un *argument_list* racchiuso tra parentesi quadre:</span><span class="sxs-lookup"><span data-stu-id="8624e-1163">A *base_access* consists of the reserved word `base` followed by either a "`.`" token and an identifier or an *argument_list* enclosed in square brackets:</span></span>

```antlr
base_access
    : 'base' '.' identifier
    | 'base' '[' expression_list ']'
    ;
```

<span data-ttu-id="8624e-1164">Oggetto *base_access* consente di accedere ai membri di classe di base che sono nascoste in modo analogo i membri denominati nella classe corrente o uno struct.</span><span class="sxs-lookup"><span data-stu-id="8624e-1164">A *base_access* is used to access base class members that are hidden by similarly named members in the current class or struct.</span></span> <span data-ttu-id="8624e-1165">Oggetto *base_access* è consentito solo nel *blocco* di un costruttore di istanza, un metodo di istanza o una funzione di accesso di istanza.</span><span class="sxs-lookup"><span data-stu-id="8624e-1165">A *base_access* is permitted only in the *block* of an instance constructor, an instance method, or an instance accessor.</span></span> <span data-ttu-id="8624e-1166">Quando `base.I` si verifica in una classe o struct, `I` deve indicare un membro della classe di base di quella classe o struct.</span><span class="sxs-lookup"><span data-stu-id="8624e-1166">When `base.I` occurs in a class or struct, `I` must denote a member of the base class of that class or struct.</span></span> <span data-ttu-id="8624e-1167">Analogamente, quando `base[E]` si verifica in una classe, un indicizzatore applicabile deve essere presente nella classe di base.</span><span class="sxs-lookup"><span data-stu-id="8624e-1167">Likewise, when `base[E]` occurs in a class, an applicable indexer must exist in the base class.</span></span>

<span data-ttu-id="8624e-1168">In fase di associazione *base_access* espressioni nel formato `base.I` e `base[E]` vengono valutate come se fossero scritti `((B)this).I` e `((B)this)[E]`, dove `B` è la classe di base della classe o uno struct in cui viene utilizzato il costrutto.</span><span class="sxs-lookup"><span data-stu-id="8624e-1168">At binding-time, *base_access* expressions of the form `base.I` and `base[E]` are evaluated exactly as if they were written `((B)this).I` and `((B)this)[E]`, where `B` is the base class of the class or struct in which the construct occurs.</span></span> <span data-ttu-id="8624e-1169">Pertanto `base.I` e `base[E]` corrispondono ai `this.I` e `this[E]`, ad eccezione di `this` viene visualizzato come un'istanza della classe di base.</span><span class="sxs-lookup"><span data-stu-id="8624e-1169">Thus, `base.I` and `base[E]` correspond to `this.I` and `this[E]`, except `this` is viewed as an instance of the base class.</span></span>

<span data-ttu-id="8624e-1170">Quando un *base_access* fa riferimento a un membro di funzione virtuale (un metodo, proprietà o indicizzatore), la determinazione di cui funzione membro da richiamare in fase di esecuzione ([controllo della risoluzione dell'overload dinamico in fase di compilazione ](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) viene modificato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1170">When a *base_access* references a virtual function member (a method, property, or indexer), the determination of which function member to invoke at run-time ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) is changed.</span></span> <span data-ttu-id="8624e-1171">Il membro di funzione che viene richiamato viene determinato mediante la ricerca di implementazione più derivata ([metodi virtuali](classes.md#virtual-methods)) del membro funzione rispetto al `B` (invece che rispetto al tipo di runtime di `this`, come sarebbe accade per un accesso non in base).</span><span class="sxs-lookup"><span data-stu-id="8624e-1171">The function member that is invoked is determined by finding the most derived implementation ([Virtual methods](classes.md#virtual-methods)) of the function member with respect to `B` (instead of with respect to the run-time type of `this`, as would be usual in a non-base access).</span></span> <span data-ttu-id="8624e-1172">Di conseguenza, all'interno di un' `override` di un `virtual` membro di funzione, un *base_access* può essere utilizzato per richiamare l'implementazione ereditata del membro di funzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1172">Thus, within an `override` of a `virtual` function member, a *base_access* can be used to invoke the inherited implementation of the function member.</span></span> <span data-ttu-id="8624e-1173">Se il membro di funzione viene fatto riferimento da un *base_access* è astratto, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1173">If the function member referenced by a *base_access* is abstract, a binding-time error occurs.</span></span>

### <a name="postfix-increment-and-decrement-operators"></a><span data-ttu-id="8624e-1174">Forma suffissa di incremento e decremento (operatori)</span><span class="sxs-lookup"><span data-stu-id="8624e-1174">Postfix increment and decrement operators</span></span>

```antlr
post_increment_expression
    : primary_expression '++'
    ;

post_decrement_expression
    : primary_expression '--'
    ;
```

<span data-ttu-id="8624e-1175">L'operando di un'operazione di incremento o decremento operazione deve essere un'espressione classificata come una variabile, un accesso a proprietà o un accesso all'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1175">The operand of a postfix increment or decrement operation must be an expression classified as a variable, a property access, or an indexer access.</span></span> <span data-ttu-id="8624e-1176">Il risultato dell'operazione è un valore dello stesso tipo dell'operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-1176">The result of the operation is a value of the same type as the operand.</span></span>

<span data-ttu-id="8624e-1177">Se il *primary_expression* dispone del tipo in fase di compilazione `dynamic` , quindi l'operatore è associato in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)), il *post_increment_expression*oppure *post_decrement_expression* ha il tipo di fase di compilazione `dynamic` e le regole seguenti vengono applicate in fase di esecuzione utilizzando il tipo di fase di esecuzione del *primary_expression*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1177">If the *primary_expression* has the compile-time type `dynamic` then the operator is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)), the *post_increment_expression* or *post_decrement_expression* has the compile-time type `dynamic` and the following rules are applied at run-time using the run-time type of the *primary_expression*.</span></span>

<span data-ttu-id="8624e-1178">Se l'operando di un suffisso quale incrementa o operazione di decremento è una proprietà o l'accesso dell'indicizzatore, la proprietà o l'indicizzatore deve avere entrambe un `get` e un `set` della funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="8624e-1178">If the operand of a postfix increment or decrement operation is a property or indexer access, the property or indexer must have both a `get` and a `set` accessor.</span></span> <span data-ttu-id="8624e-1179">Se ciò non avviene, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1179">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="8624e-1180">Risoluzione dell'overload di operatore unario ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico.</span><span class="sxs-lookup"><span data-stu-id="8624e-1180">Unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="8624e-1181">Predefined `++` e `--` operatori esistono per i tipi seguenti: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char` , `float`, `double`, `decimal`e qualsiasi tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1181">Predefined `++` and `--` operators exist for the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, and any enum type.</span></span> <span data-ttu-id="8624e-1182">Oggetto predefinito `++` gli operatori restituiscono il valore ottenuto aggiungendo 1 all'operando e oggetto predefinito `--` operatori restituiscono il valore ottenuto sottraendo 1 operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-1182">The predefined `++` operators return the value produced by adding 1 to the operand, and the predefined `--` operators return the value produced by subtracting 1 from the operand.</span></span> <span data-ttu-id="8624e-1183">In un `checked` contesto, se il risultato dell'addizione o sottrazione è compreso nell'intervallo del tipo di risultato e il tipo di risultato è un tipo integrale o enumerazione, un `System.OverflowException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1183">In a `checked` context, if the result of this addition or subtraction is outside the range of the result type and the result type is an integral type or enum type, a `System.OverflowException` is thrown.</span></span>

<span data-ttu-id="8624e-1184">L'elaborazione in fase di esecuzione di un forma suffissa di incremento o decremento operazione del form `x++` o `x--` prevede i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1184">The run-time processing of a postfix increment or decrement operation of the form `x++` or `x--` consists of the following steps:</span></span>

*   <span data-ttu-id="8624e-1185">Se `x` viene classificato come una variabile:</span><span class="sxs-lookup"><span data-stu-id="8624e-1185">If `x` is classified as a variable:</span></span>
    * <span data-ttu-id="8624e-1186">`x` viene valutata per produrre la variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-1186">`x` is evaluated to produce the variable.</span></span>
    * <span data-ttu-id="8624e-1187">Il valore di `x` viene salvato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1187">The value of `x` is saved.</span></span>
    * <span data-ttu-id="8624e-1188">L'operatore selezionato viene richiamato con il valore salvato di `x` come argomento.</span><span class="sxs-lookup"><span data-stu-id="8624e-1188">The selected operator is invoked with the saved value of `x` as its argument.</span></span>
    * <span data-ttu-id="8624e-1189">Il valore restituito dall'operatore viene archiviato nel percorso specificato dalla valutazione di `x`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1189">The value returned by the operator is stored in the location given by the evaluation of `x`.</span></span>
    * <span data-ttu-id="8624e-1190">Il valore salvato di `x` diventa il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1190">The saved value of `x` becomes the result of the operation.</span></span>
*   <span data-ttu-id="8624e-1191">Se `x` viene classificato come accesso alla proprietà o l'indicizzatore:</span><span class="sxs-lookup"><span data-stu-id="8624e-1191">If `x` is classified as a property or indexer access:</span></span>
    * <span data-ttu-id="8624e-1192">L'espressione dell'istanza (se `x` non è `static`) e l'elenco di argomenti (se `x` un accesso dell'indicizzatore) associato `x` vengono valutate, e i risultati vengono usati nella successiva `get` e `set` chiamate di funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="8624e-1192">The instance expression (if `x` is not `static`) and the argument list (if `x` is an indexer access) associated with `x` are evaluated, and the results are used in the subsequent `get` and `set` accessor invocations.</span></span>
    * <span data-ttu-id="8624e-1193">Il `get` funzione di accesso di `x` viene richiamato e il valore restituito viene salvato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1193">The `get` accessor of `x` is invoked and the returned value is saved.</span></span>
    * <span data-ttu-id="8624e-1194">L'operatore selezionato viene richiamato con il valore salvato di `x` come argomento.</span><span class="sxs-lookup"><span data-stu-id="8624e-1194">The selected operator is invoked with the saved value of `x` as its argument.</span></span>
    * <span data-ttu-id="8624e-1195">Il `set` funzione di accesso di `x` viene richiamato con il valore restituito dall'operatore come relativo `value` argomento.</span><span class="sxs-lookup"><span data-stu-id="8624e-1195">The `set` accessor of `x` is invoked with the value returned by the operator as its `value` argument.</span></span>
    * <span data-ttu-id="8624e-1196">Il valore salvato di `x` diventa il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1196">The saved value of `x` becomes the result of the operation.</span></span>

<span data-ttu-id="8624e-1197">Il `++` e `--` operatori supportano anche notazione del prefisso ([incremento prefisso e decremento (operatori)](expressions.md#prefix-increment-and-decrement-operators)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1197">The `++` and `--` operators also support prefix notation ([Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)).</span></span> <span data-ttu-id="8624e-1198">In genere, il risultato del `x++` o `x--` è il valore di `x` prima dell'operazione, mentre il risultato del `++x` o `--x` è il valore di `x` al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1198">Typically, the result of `x++` or `x--` is the value of `x` before the operation, whereas the result of `++x` or `--x` is the value of `x` after the operation.</span></span> <span data-ttu-id="8624e-1199">In entrambi i casi `x` stesso ha lo stesso valore al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1199">In either case, `x` itself has the same value after the operation.</span></span>

<span data-ttu-id="8624e-1200">Un' `operator ++` o `operator --` implementazione può essere richiamata usando la notazione prefissa o suffissa.</span><span class="sxs-lookup"><span data-stu-id="8624e-1200">An `operator ++` or `operator --` implementation can be invoked using either postfix or prefix notation.</span></span> <span data-ttu-id="8624e-1201">Non è possibile disporre di implementazioni di operatori separati per le due notazioni.</span><span class="sxs-lookup"><span data-stu-id="8624e-1201">It is not possible to have separate operator implementations for the two notations.</span></span>

### <a name="the-new-operator"></a><span data-ttu-id="8624e-1202">Operatore new</span><span class="sxs-lookup"><span data-stu-id="8624e-1202">The new operator</span></span>

<span data-ttu-id="8624e-1203">Il `new` operatore viene usato per creare nuove istanze dei tipi.</span><span class="sxs-lookup"><span data-stu-id="8624e-1203">The `new` operator is used to create new instances of types.</span></span>

<span data-ttu-id="8624e-1204">Esistono tre forme di `new` espressioni:</span><span class="sxs-lookup"><span data-stu-id="8624e-1204">There are three forms of `new` expressions:</span></span>

*  <span data-ttu-id="8624e-1205">Espressioni per la creazione di oggetti vengono utilizzate per creare nuove istanze di tipi di classi e tipi di valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1205">Object creation expressions are used to create new instances of class types and value types.</span></span>
*  <span data-ttu-id="8624e-1206">Espressioni di creazione della matrice vengono utilizzate per creare nuove istanze dei tipi di matrice.</span><span class="sxs-lookup"><span data-stu-id="8624e-1206">Array creation expressions are used to create new instances of array types.</span></span>
*  <span data-ttu-id="8624e-1207">Consentono di creare nuove istanze del delegato di tipi di espressioni per la creazione di delegato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1207">Delegate creation expressions are used to create new instances of delegate types.</span></span>

<span data-ttu-id="8624e-1208">Il `new` operatore implica la creazione di un'istanza di un tipo, ma non implica necessariamente l'allocazione dinamica della memoria.</span><span class="sxs-lookup"><span data-stu-id="8624e-1208">The `new` operator implies creation of an instance of a type, but does not necessarily imply dynamic allocation of memory.</span></span> <span data-ttu-id="8624e-1209">In particolare, le istanze dei tipi di valore non richiedono alcuna memoria aggiuntiva oltre le variabili in cui si trovano e si verifica alcuna allocazione dinamiche quando `new` viene usato per creare istanze dei tipi di valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1209">In particular, instances of value types require no additional memory beyond the variables in which they reside, and no dynamic allocations occur when `new` is used to create instances of value types.</span></span>

#### <a name="object-creation-expressions"></a><span data-ttu-id="8624e-1210">Espressioni per la creazione di oggetti</span><span class="sxs-lookup"><span data-stu-id="8624e-1210">Object creation expressions</span></span>

<span data-ttu-id="8624e-1211">Un' *object_creation_expression* viene usato per creare una nuova istanza di un *class_type* o un *value_type*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1211">An *object_creation_expression* is used to create a new instance of a *class_type* or a *value_type*.</span></span>

```antlr
object_creation_expression
    : 'new' type '(' argument_list? ')' object_or_collection_initializer?
    | 'new' type object_or_collection_initializer
    ;

object_or_collection_initializer
    : object_initializer
    | collection_initializer
    ;
```

<span data-ttu-id="8624e-1212">Il *tipo* di un *object_creation_expression* deve essere un *class_type*, un *value_type* o una *type_parameter* .</span><span class="sxs-lookup"><span data-stu-id="8624e-1212">The *type* of an *object_creation_expression* must be a *class_type*, a *value_type* or a *type_parameter*.</span></span> <span data-ttu-id="8624e-1213">Il *tipo* non può essere un' `abstract` *class_type*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1213">The *type* cannot be an `abstract` *class_type*.</span></span>

<span data-ttu-id="8624e-1214">L'opzione facoltativa *argument_list* ([elenchi di argomenti](expressions.md#argument-lists)) è consentito solo se la *tipo* è un *class_type* o un *struct_ tipo*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1214">The optional *argument_list* ([Argument lists](expressions.md#argument-lists)) is permitted only if the *type* is a *class_type* or a *struct_type*.</span></span>

<span data-ttu-id="8624e-1215">Un'espressione di creazione di oggetti è possibile omettere l'elenco di argomenti del costruttore e racchiudere tra parentesi fornito che include un inizializzatore di oggetto o un inizializzatore di raccolta.</span><span class="sxs-lookup"><span data-stu-id="8624e-1215">An object creation expression can omit the constructor argument list and enclosing parentheses provided it includes an object initializer or collection initializer.</span></span> <span data-ttu-id="8624e-1216">Omettendo l'elenco di argomenti del costruttore e racchiudendoli tra parentesi equivale a specificare un elenco di argomenti vuoto.</span><span class="sxs-lookup"><span data-stu-id="8624e-1216">Omitting the constructor argument list and enclosing parentheses is equivalent to specifying an empty argument list.</span></span>

<span data-ttu-id="8624e-1217">L'elaborazione di un'espressione di creazione di oggetti che include un inizializzatore di oggetto o un inizializzatore di insieme è costituito da elaborazione prima di tutto il costruttore di istanza e quindi elaborando le inizializzazioni membro o un elemento specificate dall'inizializzatore di oggetto ([ Inizializzatori di oggetto](expressions.md#object-initializers)) o un inizializzatore di raccolta ([inizializzatori di insieme](expressions.md#collection-initializers)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1217">Processing of an object creation expression that includes an object initializer or collection initializer consists of first processing the instance constructor and then processing the member or element initializations specified by the object initializer ([Object initializers](expressions.md#object-initializers)) or collection initializer ([Collection initializers](expressions.md#collection-initializers)).</span></span>

<span data-ttu-id="8624e-1218">Se uno qualsiasi degli argomenti facoltativi *argument_list* dispone del tipo in fase di compilazione `dynamic` il *object_creation_expression* viene associata in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)) e vengono applicate le regole seguenti in fase di esecuzione usando il tipo di fase di esecuzione di questi argomenti del *argument_list* che hanno il tipo in fase di compilazione `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1218">If any of the arguments in the optional *argument_list* has the compile-time type `dynamic` then the *object_creation_expression* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)) and the following rules are applied at run-time using the run-time type of those arguments of the *argument_list* that have the compile time type `dynamic`.</span></span> <span data-ttu-id="8624e-1219">Tuttavia, la creazione dell'oggetto sottoposto a un controllo in fase di compilazione limitato come descritto in [controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="8624e-1219">However, the object creation undergoes a limited compile time check as described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="8624e-1220">L'elaborazione in fase di associazione di un *object_creation_expression* del form `new T(A)`, dove `T` è un *class_type* o un *value_type* e`A` è una funzione facoltativa *argument_list*, costituita dai passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1220">The binding-time processing of an *object_creation_expression* of the form `new T(A)`, where `T` is a *class_type* or a *value_type* and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*   <span data-ttu-id="8624e-1221">Se `T` è un *value_type* e `A` non è presente:</span><span class="sxs-lookup"><span data-stu-id="8624e-1221">If `T` is a *value_type* and `A` is not present:</span></span>
    * <span data-ttu-id="8624e-1222">Il *object_creation_expression* è una chiamata al costruttore predefinito.</span><span class="sxs-lookup"><span data-stu-id="8624e-1222">The *object_creation_expression* is a default constructor invocation.</span></span> <span data-ttu-id="8624e-1223">Il risultato del *object_creation_expression* è un valore di tipo `T`, vale a dire il valore predefinito per `T` come definito nella [System. ValueType di tipo](types.md#the-systemvaluetype-type).</span><span class="sxs-lookup"><span data-stu-id="8624e-1223">The result of the *object_creation_expression* is a value of type `T`, namely the default value for `T` as defined in [The System.ValueType type](types.md#the-systemvaluetype-type).</span></span>
*   <span data-ttu-id="8624e-1224">In caso contrario, se `T` è un *type_parameter* e `A` non è presente:</span><span class="sxs-lookup"><span data-stu-id="8624e-1224">Otherwise, if `T` is a *type_parameter* and `A` is not present:</span></span>
    * <span data-ttu-id="8624e-1225">Se nessun vincolo di tipo valore o il vincolo del costruttore ([vincoli di parametro di tipo](classes.md#type-parameter-constraints)) è stato specificato per `T`, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1225">If no value type constraint or constructor constraint ([Type parameter constraints](classes.md#type-parameter-constraints)) has been specified for `T`, a binding-time error occurs.</span></span>
    * <span data-ttu-id="8624e-1226">Il risultato del *object_creation_expression* è un valore del tipo di runtime che il parametro di tipo è stato associato, vale a dire il risultato ottenuto richiamando il costruttore predefinito di quel tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1226">The result of the *object_creation_expression* is a value of the run-time type that the type parameter has been bound to, namely the result of invoking the default constructor of that type.</span></span> <span data-ttu-id="8624e-1227">Il tipo di runtime può essere un tipo riferimento o un tipo valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1227">The run-time type may be a reference type or a value type.</span></span>
*   <span data-ttu-id="8624e-1228">In caso contrario, se `T` è un *class_type* o una *struct_type*:</span><span class="sxs-lookup"><span data-stu-id="8624e-1228">Otherwise, if `T` is a *class_type* or a *struct_type*:</span></span>
    * <span data-ttu-id="8624e-1229">Se `T` è un `abstract` *class_type*, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1229">If `T` is an `abstract` *class_type*, a compile-time error occurs.</span></span>
    * <span data-ttu-id="8624e-1230">Per richiamare il costruttore di istanza viene determinato usando le regole di risoluzione dell'overload [risoluzione dell'Overload](expressions.md#overload-resolution).</span><span class="sxs-lookup"><span data-stu-id="8624e-1230">The instance constructor to invoke is determined using the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="8624e-1231">Il set di costruttori di istanza è costituito da tutti i costruttori di istanza accessibile dichiarati nel `T` che sono applicabili alle `A` ([membro di funzione applicabile](expressions.md#applicable-function-member)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1231">The set of candidate instance constructors consists of all accessible instance constructors declared in `T` which are applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span> <span data-ttu-id="8624e-1232">Se il set di costruttori di istanza è vuoto o se non è possibile identificare un costruttore di istanza migliore, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1232">If the set of candidate instance constructors is empty, or if a single best instance constructor cannot be identified, a binding-time error occurs.</span></span>
    * <span data-ttu-id="8624e-1233">Il risultato del *object_creation_expression* è un valore di tipo `T`, vale a dire il valore generato richiamando il costruttore di istanza definito nel passaggio precedente.</span><span class="sxs-lookup"><span data-stu-id="8624e-1233">The result of the *object_creation_expression* is a value of type `T`, namely the value produced by invoking the instance constructor determined in the step above.</span></span>
*  <span data-ttu-id="8624e-1234">In caso contrario, il *object_creation_expression* non è valido, e si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1234">Otherwise, the *object_creation_expression* is invalid, and a binding-time error occurs.</span></span>

<span data-ttu-id="8624e-1235">Anche se il *object_creation_expression* in modo dinamico è associato, il tipo di fase di compilazione è ancora `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1235">Even if the *object_creation_expression* is dynamically bound, the compile-time type is still `T`.</span></span>

<span data-ttu-id="8624e-1236">L'elaborazione in fase di esecuzione di un' *object_creation_expression* del form `new T(A)`, dove `T` viene *class_type* o un *struct_type* e`A` è una funzione facoltativa *argument_list*, costituita dai passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1236">The run-time processing of an *object_creation_expression* of the form `new T(A)`, where `T` is *class_type* or a *struct_type* and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*   <span data-ttu-id="8624e-1237">Se `T` è un *class_type*:</span><span class="sxs-lookup"><span data-stu-id="8624e-1237">If `T` is a *class_type*:</span></span>
    * <span data-ttu-id="8624e-1238">Una nuova istanza della classe `T` viene allocato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1238">A new instance of class `T` is allocated.</span></span> <span data-ttu-id="8624e-1239">Se non è disponibile memoria sufficiente per allocare la nuova istanza, una `System.OutOfMemoryException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.</span><span class="sxs-lookup"><span data-stu-id="8624e-1239">If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.</span></span>
    * <span data-ttu-id="8624e-1240">Tutti i campi della nuova istanza vengono inizializzati sui valori predefiniti ([i valori predefiniti](variables.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1240">All fields of the new instance are initialized to their default values ([Default values](variables.md#default-values)).</span></span>
    * <span data-ttu-id="8624e-1241">Viene chiamato il costruttore di istanza in base alle regole di chiamata di funzione membro ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1241">The instance constructor is invoked according to the rules of function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span></span> <span data-ttu-id="8624e-1242">Un riferimento all'istanza appena allocata viene passato automaticamente al costruttore di istanza e l'istanza è accessibile all'interno di tale costruttore come `this`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1242">A reference to the newly allocated instance is automatically passed to the instance constructor and the instance can be accessed from within that constructor as `this`.</span></span>
*   <span data-ttu-id="8624e-1243">Se `T` è un *struct_type*:</span><span class="sxs-lookup"><span data-stu-id="8624e-1243">If `T` is a *struct_type*:</span></span>
    * <span data-ttu-id="8624e-1244">Un'istanza del tipo `T` viene creato allocando una variabile locale temporanea.</span><span class="sxs-lookup"><span data-stu-id="8624e-1244">An instance of type `T` is created by allocating a temporary local variable.</span></span> <span data-ttu-id="8624e-1245">Poiché un costruttore di istanza di un *struct_type* è necessario assegnare in modo certo un valore per ogni campo dell'istanza viene creato, non è necessaria alcuna inizializzazione della variabile temporanea.</span><span class="sxs-lookup"><span data-stu-id="8624e-1245">Since an instance constructor of a *struct_type* is required to definitely assign a value to each field of the instance being created, no initialization of the temporary variable is necessary.</span></span>
    * <span data-ttu-id="8624e-1246">Viene chiamato il costruttore di istanza in base alle regole di chiamata di funzione membro ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1246">The instance constructor is invoked according to the rules of function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span></span> <span data-ttu-id="8624e-1247">Un riferimento all'istanza appena allocata viene passato automaticamente al costruttore di istanza e l'istanza è accessibile all'interno di tale costruttore come `this`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1247">A reference to the newly allocated instance is automatically passed to the instance constructor and the instance can be accessed from within that constructor as `this`.</span></span>

#### <a name="object-initializers"></a><span data-ttu-id="8624e-1248">Inizializzatori di oggetto</span><span class="sxs-lookup"><span data-stu-id="8624e-1248">Object initializers</span></span>

<span data-ttu-id="8624e-1249">Un' ***inizializzatore di oggetto*** specifica i valori per zero o più campi, proprietà o elementi indicizzati di un oggetto.</span><span class="sxs-lookup"><span data-stu-id="8624e-1249">An ***object initializer*** specifies values for zero or more fields, properties or indexed elements of an object.</span></span>

```antlr
object_initializer
    : '{' member_initializer_list? '}'
    | '{' member_initializer_list ',' '}'
    ;

member_initializer_list
    : member_initializer (',' member_initializer)*
    ;

member_initializer
    : initializer_target '=' initializer_value
    ;

initializer_target
    : identifier
    | '[' argument_list ']'
    ;

initializer_value
    : expression
    | object_or_collection_initializer
    ;
```

<span data-ttu-id="8624e-1250">Un inizializzatore di oggetto è costituito da una sequenza di inizializzatori di membro, racchiusi `{` e `}` token e separati da virgole.</span><span class="sxs-lookup"><span data-stu-id="8624e-1250">An object initializer consists of a sequence of member initializers, enclosed by `{` and `}` tokens and separated by commas.</span></span> <span data-ttu-id="8624e-1251">Ciascuna *member_initializer* designa una destinazione per l'inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1251">Each *member_initializer* designates a target for the initialization.</span></span> <span data-ttu-id="8624e-1252">Un *identificatore* deve denominare un campo accessibile o una proprietà dell'oggetto in fase di inizializzazione, mentre un *argument_list* racchiuso tra parentesi quadre deve specificare gli argomenti per un indicizzatore accessibile nel oggetto inizializzato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1252">An *identifier* must name an accessible field or property of the object being initialized, whereas an *argument_list* enclosed in square brackets must specify arguments for an accessible indexer on the object being initialized.</span></span> <span data-ttu-id="8624e-1253">È un errore per un inizializzatore di oggetto includere più di un inizializzatore di membro per lo stesso campo o proprietà.</span><span class="sxs-lookup"><span data-stu-id="8624e-1253">It is an error for an object initializer to include more than one member initializer for the same field or property.</span></span>

<span data-ttu-id="8624e-1254">Ciascuna *initializer_target* è seguita da un segno di uguale e un'espressione, un inizializzatore di oggetto o un inizializzatore di raccolta.</span><span class="sxs-lookup"><span data-stu-id="8624e-1254">Each *initializer_target* is followed by an equals sign and either an expression, an object initializer or a collection initializer.</span></span> <span data-ttu-id="8624e-1255">Non è possibile che le espressioni all'interno dell'inizializzatore di oggetto per fare riferimento all'oggetto appena creato che l'inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1255">It is not possible for expressions within the object initializer to refer to the newly created object it is initializing.</span></span>

<span data-ttu-id="8624e-1256">Un inizializzatore di membro che specifica un'espressione dopo il segno di uguale viene elaborato esattamente come un'assegnazione ([assegnamento semplice](expressions.md#simple-assignment)) alla destinazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1256">A member initializer that specifies an expression after the equals sign is processed in the same way as an assignment ([Simple assignment](expressions.md#simple-assignment)) to the target.</span></span>

<span data-ttu-id="8624e-1257">Un inizializzatore di membro che consente di specificare un inizializzatore di oggetto dopo il segno di uguale è un ***inizializzatore di oggetto annidati***, vale a dire un'inizializzazione di un oggetto incorporato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1257">A member initializer that specifies an object initializer after the equals sign is a ***nested object initializer***, i.e. an initialization of an embedded object.</span></span> <span data-ttu-id="8624e-1258">Invece di assegnare un nuovo valore per il campo o proprietà, le assegnazioni nell'inizializzatore di oggetto annidati vengono trattate come le assegnazioni ai membri del campo o della proprietà.</span><span class="sxs-lookup"><span data-stu-id="8624e-1258">Instead of assigning a new value to the field or property, the assignments in the nested object initializer are treated as assignments to members of the field or property.</span></span> <span data-ttu-id="8624e-1259">Gli inizializzatori di oggetto annidati non è possibile applicare alle proprietà con un tipo di valore o per i campi di sola lettura con un tipo di valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1259">Nested object initializers cannot be applied to properties with a value type, or to read-only fields with a value type.</span></span>

<span data-ttu-id="8624e-1260">Un inizializzatore di membro che consente di specificare un inizializzatore di raccolta dopo il segno di uguale è un'inizializzazione di una raccolta incorporata.</span><span class="sxs-lookup"><span data-stu-id="8624e-1260">A member initializer that specifies a collection initializer after the equals sign is an initialization of an embedded collection.</span></span> <span data-ttu-id="8624e-1261">Invece di assegnare una nuova raccolta per il campo di destinazione, la proprietà o indicizzatore, gli elementi specificati nell'inizializzatore vengono aggiunti alla raccolta di cui viene fatto riferimento dalla destinazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1261">Instead of assigning a new collection to the target field, property or indexer, the elements given in the initializer are added to the collection referenced by the target.</span></span> <span data-ttu-id="8624e-1262">La destinazione deve essere di tipo raccolta che soddisfa i requisiti specificati nelle [inizializzatori di insieme](expressions.md#collection-initializers).</span><span class="sxs-lookup"><span data-stu-id="8624e-1262">The target must be of a collection type that satisfies the requirements specified in [Collection initializers](expressions.md#collection-initializers).</span></span>

<span data-ttu-id="8624e-1263">Gli argomenti da un inizializzatore di indice verranno sempre essere valutati esattamente una volta.</span><span class="sxs-lookup"><span data-stu-id="8624e-1263">The arguments to an index initializer will always be evaluated exactly once.</span></span> <span data-ttu-id="8624e-1264">Di conseguenza, anche se gli argomenti finire mai usato (ad esempio a causa di un inizializzatore nidificato vuoto), questi verranno valutati in relazione agli effetti collaterali.</span><span class="sxs-lookup"><span data-stu-id="8624e-1264">Thus, even if the arguments end up never getting used (e.g. because of an empty nested initializer), they will be evaluated for their side effects.</span></span>

<span data-ttu-id="8624e-1265">La classe seguente rappresenta un punto con due coordinate:</span><span class="sxs-lookup"><span data-stu-id="8624e-1265">The following class represents a point with two coordinates:</span></span>
```csharp
public class Point
{
    int x, y;

    public int X { get { return x; } set { x = value; } }
    public int Y { get { return y; } set { y = value; } }
}
```

<span data-ttu-id="8624e-1266">Un'istanza di `Point` può essere creato e inizializzato come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-1266">An instance of `Point` can be created and initialized as follows:</span></span>
```csharp
Point a = new Point { X = 0, Y = 1 };
```
<span data-ttu-id="8624e-1267">che ha lo stesso effetto</span><span class="sxs-lookup"><span data-stu-id="8624e-1267">which has the same effect as</span></span>
```csharp
Point __a = new Point();
__a.X = 0;
__a.Y = 1; 
Point a = __a;
```
<span data-ttu-id="8624e-1268">in cui `__a` è una variabile temporanea in caso contrario invisibile e non accessibile.</span><span class="sxs-lookup"><span data-stu-id="8624e-1268">where `__a` is an otherwise invisible and inaccessible temporary variable.</span></span> <span data-ttu-id="8624e-1269">La classe seguente rappresenta un rettangolo creato da due punti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1269">The following class represents a rectangle created from two points:</span></span>
```csharp
public class Rectangle
{
    Point p1, p2;

    public Point P1 { get { return p1; } set { p1 = value; } }
    public Point P2 { get { return p2; } set { p2 = value; } }
}
```

<span data-ttu-id="8624e-1270">Un'istanza di `Rectangle` può essere creato e inizializzato come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-1270">An instance of `Rectangle` can be created and initialized as follows:</span></span>
```csharp
Rectangle r = new Rectangle {
    P1 = new Point { X = 0, Y = 1 },
    P2 = new Point { X = 2, Y = 3 }
};
```
<span data-ttu-id="8624e-1271">che ha lo stesso effetto</span><span class="sxs-lookup"><span data-stu-id="8624e-1271">which has the same effect as</span></span>
```csharp
Rectangle __r = new Rectangle();
Point __p1 = new Point();
__p1.X = 0;
__p1.Y = 1;
__r.P1 = __p1;
Point __p2 = new Point();
__p2.X = 2;
__p2.Y = 3;
__r.P2 = __p2; 
Rectangle r = __r;
```
<span data-ttu-id="8624e-1272">in cui `__r`, `__p1` e `__p2` sono variabili temporanee che altrimenti risultano invisibile e non accessibili.</span><span class="sxs-lookup"><span data-stu-id="8624e-1272">where `__r`, `__p1` and `__p2` are temporary variables that are otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="8624e-1273">Se `Rectangle`del costruttore alloca due incorporati `Point` istanze</span><span class="sxs-lookup"><span data-stu-id="8624e-1273">If `Rectangle`'s constructor allocates the two embedded `Point` instances</span></span>
```csharp
public class Rectangle
{
    Point p1 = new Point();
    Point p2 = new Point();

    public Point P1 { get { return p1; } }
    public Point P2 { get { return p2; } }
}
```
<span data-ttu-id="8624e-1274">il costrutto seguente può essere utilizzato per inizializzare l'oggetto incorporato `Point` istanze invece di assegnare le nuove istanze:</span><span class="sxs-lookup"><span data-stu-id="8624e-1274">the following construct can be used to initialize the embedded `Point` instances instead of assigning new instances:</span></span>
```csharp
Rectangle r = new Rectangle {
    P1 = { X = 0, Y = 1 },
    P2 = { X = 2, Y = 3 }
};
```
<span data-ttu-id="8624e-1275">che ha lo stesso effetto</span><span class="sxs-lookup"><span data-stu-id="8624e-1275">which has the same effect as</span></span>
```csharp
Rectangle __r = new Rectangle();
__r.P1.X = 0;
__r.P1.Y = 1;
__r.P2.X = 2;
__r.P2.Y = 3;
Rectangle r = __r;
```

<span data-ttu-id="8624e-1276">In base a una definizione appropriata di C, l'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="8624e-1276">Given an appropriate definition of C, the following example:</span></span>
```csharp
var c = new C {
    x = true,
    y = { a = "Hello" },
    z = { 1, 2, 3 },
    ["x"] = 5,
    [0,0] = { "a", "b" },
    [1,2] = {}
};
```
<span data-ttu-id="8624e-1277">è equivalente a questa serie di esercitazioni:</span><span class="sxs-lookup"><span data-stu-id="8624e-1277">is equivalent to this series of assignments:</span></span>
```csharp
C __c = new C();
__c.x = true;
__c.y.a = "Hello";
__c.z.Add(1); 
__c.z.Add(2);
__c.z.Add(3);
string __i1 = "x";
__c[__i1] = 5;
int __i2 = 0, __i3 = 0;
__c[__i2,__i3].Add("a");
__c[__i2,__i3].Add("b");
int __i4 = 1, __i5 = 2;
var c = __c;
```
<span data-ttu-id="8624e-1278">in cui `__c`e così via sono variabili generate invisibile e non accessibili al codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="8624e-1278">where `__c`, etc., are generated variables that are invisible and inaccessible to the source code.</span></span> <span data-ttu-id="8624e-1279">Si noti che gli argomenti per `[0,0]` vengono valutate una sola volta e gli argomenti per `[1,2]` vengono valutati una volta anche se non vengono mai usati.</span><span class="sxs-lookup"><span data-stu-id="8624e-1279">Note that the arguments for `[0,0]` are evaluated only once, and the arguments for `[1,2]` are evaluated once even though they are never used.</span></span>

#### <a name="collection-initializers"></a><span data-ttu-id="8624e-1280">Inizializzatori di raccolta</span><span class="sxs-lookup"><span data-stu-id="8624e-1280">Collection initializers</span></span>

<span data-ttu-id="8624e-1281">Un inizializzatore di raccolta specifica gli elementi di una raccolta.</span><span class="sxs-lookup"><span data-stu-id="8624e-1281">A collection initializer specifies the elements of a collection.</span></span>

```antlr
collection_initializer
    : '{' element_initializer_list '}'
    | '{' element_initializer_list ',' '}'
    ;

element_initializer_list
    : element_initializer (',' element_initializer)*
    ;

element_initializer
    : non_assignment_expression
    | '{' expression_list '}'
    ;

expression_list
    : expression (',' expression)*
    ;
```

<span data-ttu-id="8624e-1282">Un inizializzatore di insieme è costituito da una sequenza di inizializzatori di elemento, racchiuso `{` e `}` token e separati da virgole.</span><span class="sxs-lookup"><span data-stu-id="8624e-1282">A collection initializer consists of a sequence of element initializers, enclosed by `{` and `}` tokens and separated by commas.</span></span> <span data-ttu-id="8624e-1283">Ogni inizializzatore di elemento specifica un elemento da aggiungere all'oggetto raccolta in fase di inizializzazione ed è costituito da un elenco di espressioni racchiuse `{` e `}` token e separati da virgole.</span><span class="sxs-lookup"><span data-stu-id="8624e-1283">Each element initializer specifies an element to be added to the collection object being initialized, and consists of a list of expressions enclosed by `{` and `}` tokens and separated by commas.</span></span>  <span data-ttu-id="8624e-1284">Un inizializzatore di elemento di singola espressione può essere scritta senza parentesi graffe, ma non può quindi essere un'espressione di assegnazione, per evitare ambiguità con gli inizializzatori di membro.</span><span class="sxs-lookup"><span data-stu-id="8624e-1284">A single-expression element initializer can be written without braces, but cannot then be an assignment expression, to avoid ambiguity with member initializers.</span></span> <span data-ttu-id="8624e-1285">Il *non_assignment_expression* definita in produzione [espressione](expressions.md#expression).</span><span class="sxs-lookup"><span data-stu-id="8624e-1285">The *non_assignment_expression* production is defined in [Expression](expressions.md#expression).</span></span>

<span data-ttu-id="8624e-1286">Di seguito è riportato un esempio di un'espressione di creazione di oggetti che include un inizializzatore di raccolta:</span><span class="sxs-lookup"><span data-stu-id="8624e-1286">The following is an example of an object creation expression that includes a collection initializer:</span></span>
```csharp
List<int> digits = new List<int> { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
```

<span data-ttu-id="8624e-1287">L'oggetto raccolta in cui viene applicato un inizializzatore di raccolta deve essere di un tipo che implementa `System.Collections.IEnumerable` o si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1287">The collection object to which a collection initializer is applied must be of a type that implements `System.Collections.IEnumerable` or a compile-time error occurs.</span></span> <span data-ttu-id="8624e-1288">Per ogni specificato elemento nell'ordine, l'inizializzatore di insieme richiama un `Add` metodo sull'oggetto destinazione dell'oggetto con l'elenco di espressioni dell'inizializzatore di elemento come elenco di argomenti, applicazione di ricerca di membri normali e risoluzione per ogni chiamata dell'overload.</span><span class="sxs-lookup"><span data-stu-id="8624e-1288">For each specified element in order, the collection initializer invokes an `Add` method on the target object with the expression list of the element initializer as argument list, applying normal member lookup and overload resolution for each invocation.</span></span> <span data-ttu-id="8624e-1289">Di conseguenza, l'oggetto di raccolta deve avere un metodo di estensione o istanze applicabile con il nome `Add` per ogni inizializzatore di elemento.</span><span class="sxs-lookup"><span data-stu-id="8624e-1289">Thus, the collection object must have an applicable instance or extension method with the name `Add` for each element initializer.</span></span>

<span data-ttu-id="8624e-1290">La classe seguente rappresenta un contatto con un nome e un elenco di numeri di telefono:</span><span class="sxs-lookup"><span data-stu-id="8624e-1290">The following class represents a contact with a name and a list of phone numbers:</span></span>
```csharp
public class Contact
{
    string name;
    List<string> phoneNumbers = new List<string>();

    public string Name { get { return name; } set { name = value; } }

    public List<string> PhoneNumbers { get { return phoneNumbers; } }
}
```

<span data-ttu-id="8624e-1291">Oggetto `List<Contact>` può essere creato e inizializzato come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-1291">A `List<Contact>` can be created and initialized as follows:</span></span>
```csharp
var contacts = new List<Contact> {
    new Contact {
        Name = "Chris Smith",
        PhoneNumbers = { "206-555-0101", "425-882-8080" }
    },
    new Contact {
        Name = "Bob Harris",
        PhoneNumbers = { "650-555-0199" }
    }
};
```
<span data-ttu-id="8624e-1292">che ha lo stesso effetto</span><span class="sxs-lookup"><span data-stu-id="8624e-1292">which has the same effect as</span></span>
```csharp
var __clist = new List<Contact>();
Contact __c1 = new Contact();
__c1.Name = "Chris Smith";
__c1.PhoneNumbers.Add("206-555-0101");
__c1.PhoneNumbers.Add("425-882-8080");
__clist.Add(__c1);
Contact __c2 = new Contact();
__c2.Name = "Bob Harris";
__c2.PhoneNumbers.Add("650-555-0199");
__clist.Add(__c2);
var contacts = __clist;
```
<span data-ttu-id="8624e-1293">in cui `__clist`, `__c1` e `__c2` sono variabili temporanee che altrimenti risultano invisibile e non accessibili.</span><span class="sxs-lookup"><span data-stu-id="8624e-1293">where `__clist`, `__c1` and `__c2` are temporary variables that are otherwise invisible and inaccessible.</span></span>

#### <a name="array-creation-expressions"></a><span data-ttu-id="8624e-1294">Espressioni di creazione matrice</span><span class="sxs-lookup"><span data-stu-id="8624e-1294">Array creation expressions</span></span>

<span data-ttu-id="8624e-1295">Un' *array_creation_expression* viene usato per creare una nuova istanza di un *array_type*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1295">An *array_creation_expression* is used to create a new instance of an *array_type*.</span></span>

```antlr
array_creation_expression
    : 'new' non_array_type '[' expression_list ']' rank_specifier* array_initializer?
    | 'new' array_type array_initializer
    | 'new' rank_specifier array_initializer
    ;
```

<span data-ttu-id="8624e-1296">Un'espressione di creazione della matrice del primo form alloca un'istanza di matrice del tipo risultante da ognuna delle singole espressioni eliminazione dall'elenco di espressioni.</span><span class="sxs-lookup"><span data-stu-id="8624e-1296">An array creation expression of the first form allocates an array instance of the type that results from deleting each of the individual expressions from the expression list.</span></span> <span data-ttu-id="8624e-1297">Ad esempio, l'espressione di creazione matrice `new int[10,20]` produce un'istanza di matrice di tipo `int[,]`e l'espressione di creazione matrice `new int[10][,]` produce una matrice di tipo `int[][,]`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1297">For example, the array creation expression `new int[10,20]` produces an array instance of type `int[,]`, and the array creation expression `new int[10][,]` produces an array of type `int[][,]`.</span></span> <span data-ttu-id="8624e-1298">Ogni espressione nell'elenco dell'espressione deve essere di tipo `int`, `uint`, `long`, o `ulong`, o convertibile in modo implicito a uno o più di questi tipi.</span><span class="sxs-lookup"><span data-stu-id="8624e-1298">Each expression in the expression list must be of type `int`, `uint`, `long`, or `ulong`, or implicitly convertible to one or more of these types.</span></span> <span data-ttu-id="8624e-1299">Il valore di ogni espressione determina la lunghezza della dimensione corrispondente nell'istanza di matrice appena allocato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1299">The value of each expression determines the length of the corresponding dimension in the newly allocated array instance.</span></span> <span data-ttu-id="8624e-1300">Poiché la lunghezza di una dimensione di matrice deve essere non negativa, è un errore in fase di compilazione per avere una *constant_expression* con un valore negativo nell'elenco dell'espressione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1300">Since the length of an array dimension must be nonnegative, it is a compile-time error to have a *constant_expression* with a negative value in the expression list.</span></span>

<span data-ttu-id="8624e-1301">Tranne che in un contesto non sicuro ([contesti non sicuri](unsafe-code.md#unsafe-contexts)), il layout delle matrici è specificato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1301">Except in an unsafe context ([Unsafe contexts](unsafe-code.md#unsafe-contexts)), the layout of arrays is unspecified.</span></span>

<span data-ttu-id="8624e-1302">Se un'espressione di creazione della matrice del primo form include un inizializzatore di matrice, ogni espressione nell'elenco dell'espressione deve essere una costante e le lunghezze di classificazione e dimensione specificate dall'elenco di espressioni devono corrispondere a quelli dell'inizializzatore di matrice.</span><span class="sxs-lookup"><span data-stu-id="8624e-1302">If an array creation expression of the first form includes an array initializer, each expression in the expression list must be a constant and the rank and dimension lengths specified by the expression list must match those of the array initializer.</span></span>

<span data-ttu-id="8624e-1303">In un'espressione di creazione della matrice nel formato secondo o terzo, la classificazione dell'identificatore di tipo o il numero di dimensioni di matrice specificato deve corrispondere a quello dell'inizializzatore di matrice.</span><span class="sxs-lookup"><span data-stu-id="8624e-1303">In an array creation expression of the second or third form, the rank of the specified array type or rank specifier must match that of the array initializer.</span></span> <span data-ttu-id="8624e-1304">Le singole lunghezze vengono dedotti dal numero di elementi in ognuno dei corrispondenti livelli di nidificazione dell'inizializzatore di matrice.</span><span class="sxs-lookup"><span data-stu-id="8624e-1304">The individual dimension lengths are inferred from the number of elements in each of the corresponding nesting levels of the array initializer.</span></span> <span data-ttu-id="8624e-1305">Di conseguenza, l'espressione</span><span class="sxs-lookup"><span data-stu-id="8624e-1305">Thus, the expression</span></span>
```csharp
new int[,] {{0, 1}, {2, 3}, {4, 5}}
```
<span data-ttu-id="8624e-1306">corrisponde esattamente alla</span><span class="sxs-lookup"><span data-stu-id="8624e-1306">exactly corresponds to</span></span>
```csharp
new int[3, 2] {{0, 1}, {2, 3}, {4, 5}}
```

<span data-ttu-id="8624e-1307">Un'espressione di creazione della matrice nel formato terzo si intende un' ***tipizzate in modo implicito l'espressione di creazione matrici***.</span><span class="sxs-lookup"><span data-stu-id="8624e-1307">An array creation expression of the third form is referred to as an ***implicitly typed array creation expression***.</span></span> <span data-ttu-id="8624e-1308">È simile al secondo formato, ad eccezione del fatto che il tipo di elemento della matrice non è assegnato in modo esplicito, ma considerato il tipo più comune ([trovare il tipo comune di un set di espressioni](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)) del set di espressioni nella matrice inizializzatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1308">It is similar to the second form, except that the element type of the array is not explicitly given, but determined as the best common type ([Finding the best common type of a set of expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)) of the set of expressions in the array initializer.</span></span> <span data-ttu-id="8624e-1309">Per una matrice multidimensionale, ad esempio, una posizione in cui il *rank_specifier* contiene almeno una virgola, questo set comprende tutti *expression*s trovato annidati *array_initializer*s.</span><span class="sxs-lookup"><span data-stu-id="8624e-1309">For a multidimensional array, i.e., one where the *rank_specifier* contains at least one comma, this set comprises all *expression*s found in nested *array_initializer*s.</span></span>

<span data-ttu-id="8624e-1310">Gli inizializzatori di matrice sono descritti dettagliatamente nella [gli inizializzatori di matrice](arrays.md#array-initializers).</span><span class="sxs-lookup"><span data-stu-id="8624e-1310">Array initializers are described further in [Array initializers](arrays.md#array-initializers).</span></span>

<span data-ttu-id="8624e-1311">Il risultato della valutazione di un'espressione di creazione della matrice viene classificato come un valore, vale a dire un riferimento all'istanza di matrice appena allocato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1311">The result of evaluating an array creation expression is classified as a value, namely a reference to the newly allocated array instance.</span></span> <span data-ttu-id="8624e-1312">L'elaborazione in fase di esecuzione di un'espressione di creazione della matrice include i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1312">The run-time processing of an array creation expression consists of the following steps:</span></span>

*  <span data-ttu-id="8624e-1313">Le espressioni di lunghezza delle dimensioni di *expression_list* vengono valutati nell'ordine, da sinistra a destra.</span><span class="sxs-lookup"><span data-stu-id="8624e-1313">The dimension length expressions of the *expression_list* are evaluated in order, from left to right.</span></span> <span data-ttu-id="8624e-1314">Dopo la valutazione di ogni espressione, una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) viene eseguita su uno dei seguenti tipi: `int`, `uint`, `long`, `ulong`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1314">Following evaluation of each expression, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) to one of the following types is performed: `int`, `uint`, `long`, `ulong`.</span></span> <span data-ttu-id="8624e-1315">Viene scelto il primo tipo in questo elenco per il quale esiste una conversione implicita.</span><span class="sxs-lookup"><span data-stu-id="8624e-1315">The first type in this list for which an implicit conversion exists is chosen.</span></span> <span data-ttu-id="8624e-1316">Se la valutazione di un'espressione o la conversione implicita successive genera un'eccezione, non altre espressioni vengono valutate e viene eseguito alcun ulteriore passaggio.</span><span class="sxs-lookup"><span data-stu-id="8624e-1316">If evaluation of an expression or the subsequent implicit conversion causes an exception, then no further expressions are evaluated and no further steps are executed.</span></span>
*  <span data-ttu-id="8624e-1317">I valori calcolati per le lunghezze delle dimensioni vengono convalidati come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="8624e-1317">The computed values for the dimension lengths are validated as follows.</span></span> <span data-ttu-id="8624e-1318">Se uno o più dei valori sono minori di zero, una `System.OverflowException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.</span><span class="sxs-lookup"><span data-stu-id="8624e-1318">If one or more of the values are less than zero, a `System.OverflowException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="8624e-1319">Un'istanza di matrice con le lunghezze delle dimensioni specificata viene allocata.</span><span class="sxs-lookup"><span data-stu-id="8624e-1319">An array instance with the given dimension lengths is allocated.</span></span> <span data-ttu-id="8624e-1320">Se non è disponibile memoria sufficiente per allocare la nuova istanza, una `System.OutOfMemoryException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.</span><span class="sxs-lookup"><span data-stu-id="8624e-1320">If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="8624e-1321">Tutti gli elementi della nuova istanza di matrice vengono inizializzati sui valori predefiniti ([i valori predefiniti](variables.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1321">All elements of the new array instance are initialized to their default values ([Default values](variables.md#default-values)).</span></span>
*  <span data-ttu-id="8624e-1322">Se l'espressione di creazione matrice contiene un inizializzatore di matrice, ogni espressione dell'inizializzatore di matrice viene valutata e assegnata al relativo elemento corrispondente della matrice.</span><span class="sxs-lookup"><span data-stu-id="8624e-1322">If the array creation expression contains an array initializer, then each expression in the array initializer is evaluated and assigned to its corresponding array element.</span></span> <span data-ttu-id="8624e-1323">Le valutazioni e le assegnazioni vengono eseguite nell'ordine vengono scritte nell'inizializzatore di matrice, in altre parole, gli elementi vengono inizializzati nell'indice ordine crescente, con la dimensione più a destra prima di tutto l'aumento.</span><span class="sxs-lookup"><span data-stu-id="8624e-1323">The evaluations and assignments are performed in the order the expressions are written in the array initializer—in other words, elements are initialized in increasing index order, with the rightmost dimension increasing first.</span></span> <span data-ttu-id="8624e-1324">Se la valutazione di una determinata espressione o l'assegnazione successivi all'elemento della matrice corrispondente genera un'eccezione, quindi non viene inizializzato alcun altro elemento (e gli elementi rimanenti in questo modo verranno impostate sui valori predefiniti).</span><span class="sxs-lookup"><span data-stu-id="8624e-1324">If evaluation of a given expression or the subsequent assignment to the corresponding array element causes an exception, then no further elements are initialized (and the remaining elements will thus have their default values).</span></span>

<span data-ttu-id="8624e-1325">Un'espressione di creazione matrici consente la creazione di istanze di una matrice con elementi di un tipo di matrice, ma gli elementi di tale matrice devono essere inizializzati manualmente.</span><span class="sxs-lookup"><span data-stu-id="8624e-1325">An array creation expression permits instantiation of an array with elements of an array type, but the elements of such an array must be manually initialized.</span></span> <span data-ttu-id="8624e-1326">Ad esempio, l'istruzione</span><span class="sxs-lookup"><span data-stu-id="8624e-1326">For example, the statement</span></span>
```csharp
int[][] a = new int[100][];
```
<span data-ttu-id="8624e-1327">Crea una matrice unidimensionale con 100 elementy typu `int[]`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1327">creates a single-dimensional array with 100 elements of type `int[]`.</span></span> <span data-ttu-id="8624e-1328">Il valore iniziale di ogni elemento è `null`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1328">The initial value of each element is `null`.</span></span> <span data-ttu-id="8624e-1329">Non è possibile che l'espressione di creazione matrice stessa anche creare un'istanza sottomatrici e l'istruzione</span><span class="sxs-lookup"><span data-stu-id="8624e-1329">It is not possible for the same array creation expression to also instantiate the sub-arrays, and the statement</span></span>
```csharp
int[][] a = new int[100][5];        // Error
```
<span data-ttu-id="8624e-1330">Genera un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1330">results in a compile-time error.</span></span> <span data-ttu-id="8624e-1331">Creazione di un'istanza delle sottomatrici deve essere eseguita manualmente, come in</span><span class="sxs-lookup"><span data-stu-id="8624e-1331">Instantiation of the sub-arrays must instead be performed manually, as in</span></span>
```csharp
int[][] a = new int[100][];
for (int i = 0; i < 100; i++) a[i] = new int[5];
```

<span data-ttu-id="8624e-1332">Quando una matrice di matrici è caratterizzato da una forma "rettangolare", che risulta quando sottomatrici sono tutti della stessa lunghezza, è preferibile usare una matrice multidimensionale.</span><span class="sxs-lookup"><span data-stu-id="8624e-1332">When an array of arrays has a "rectangular" shape, that is when the sub-arrays are all of the same length, it is more efficient to use a multi-dimensional array.</span></span> <span data-ttu-id="8624e-1333">Nell'esempio precedente, la creazione di istanze della matrice di matrici crea 101 oggetti, ovvero una matrice esterna e 100 sottomatrici.</span><span class="sxs-lookup"><span data-stu-id="8624e-1333">In the example above, instantiation of the array of arrays creates 101 objects—one outer array and 100 sub-arrays.</span></span> <span data-ttu-id="8624e-1334">Al contrario,</span><span class="sxs-lookup"><span data-stu-id="8624e-1334">In contrast,</span></span>
```csharp
int[,] = new int[100, 5];
```
<span data-ttu-id="8624e-1335">Crea un singolo oggetto, una matrice bidimensionale e l'allocazione in una singola istruzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1335">creates only a single object, a two-dimensional array, and accomplishes the allocation in a single statement.</span></span>

<span data-ttu-id="8624e-1336">Di seguito sono riportati esempi di espressioni di creazione della matrice tipizzata in modo implicito:</span><span class="sxs-lookup"><span data-stu-id="8624e-1336">The following are examples of implicitly typed array creation expressions:</span></span>
```csharp
var a = new[] { 1, 10, 100, 1000 };                       // int[]

var b = new[] { 1, 1.5, 2, 2.5 };                         // double[]

var c = new[,] { { "hello", null }, { "world", "!" } };   // string[,]

var d = new[] { 1, "one", 2, "two" };                     // Error
```

<span data-ttu-id="8624e-1337">L'ultima espressione provoca un errore in fase di compilazione perché né `int` né `string` è implicitamente convertibile in altro e in tal caso non è più comune digitare.</span><span class="sxs-lookup"><span data-stu-id="8624e-1337">The last expression causes a compile-time error because neither `int` nor `string` is implicitly convertible to the other, and so there is no best common type.</span></span> <span data-ttu-id="8624e-1338">Un'espressione di creazione della matrice tipizzata in modo esplicito deve essere usata in questo caso, ad esempio si specifica il tipo è `object[]`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1338">An explicitly typed array creation expression must be used in this case, for example specifying the type to be `object[]`.</span></span> <span data-ttu-id="8624e-1339">In alternativa, uno degli elementi può essere convertito in un tipo di base comune, che diventa quindi il tipo di elemento dedotto.</span><span class="sxs-lookup"><span data-stu-id="8624e-1339">Alternatively, one of the elements can be cast to a common base type, which would then become the inferred element type.</span></span>

<span data-ttu-id="8624e-1340">Espressioni di creazione matrice tipizzata in modo implicito possono essere combinate con gli inizializzatori di oggetto anonimi ([espressioni per la creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions)) per creare in modo anonimo strutture di dati tipizzato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1340">Implicitly typed array creation expressions can be combined with anonymous object initializers ([Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions)) to create anonymously typed data structures.</span></span> <span data-ttu-id="8624e-1341">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8624e-1341">For example:</span></span>
```csharp
var contacts = new[] {
    new {
        Name = "Chris Smith",
        PhoneNumbers = new[] { "206-555-0101", "425-882-8080" }
    },
    new {
        Name = "Bob Harris",
        PhoneNumbers = new[] { "650-555-0199" }
    }
};
```

#### <a name="delegate-creation-expressions"></a><span data-ttu-id="8624e-1342">Espressioni per la creazione di delegato</span><span class="sxs-lookup"><span data-stu-id="8624e-1342">Delegate creation expressions</span></span>

<span data-ttu-id="8624e-1343">Oggetto *delegate_creation_expression* viene usato per creare una nuova istanza di un *delegate_type*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1343">A *delegate_creation_expression* is used to create a new instance of a *delegate_type*.</span></span>

```antlr
delegate_creation_expression
    : 'new' delegate_type '(' expression ')'
    ;
```

<span data-ttu-id="8624e-1344">L'argomento di un'espressione di creazione del delegato deve essere un gruppo di metodi, una funzione anonima o un valore del tipo time di compilazione `dynamic` o un *delegate_type*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1344">The argument of a delegate creation expression must be a method group, an anonymous function or a value of either the compile time type `dynamic` or a *delegate_type*.</span></span> <span data-ttu-id="8624e-1345">Se l'argomento è un gruppo di metodi, identifica il metodo e, per un metodo di istanza, l'oggetto per cui si desidera creare un delegato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1345">If the argument is a method group, it identifies the method and, for an instance method, the object for which to create a delegate.</span></span> <span data-ttu-id="8624e-1346">Se l'argomento è una funzione anonima direttamente definisce i parametri e il corpo del metodo di destinazione di delegato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1346">If the argument is an anonymous function it directly defines the parameters and method body of the delegate target.</span></span> <span data-ttu-id="8624e-1347">Se l'argomento è un valore identifica un'istanza del delegato di cui si desidera creare una copia.</span><span class="sxs-lookup"><span data-stu-id="8624e-1347">If the argument is a value it identifies a delegate instance of which to create a copy.</span></span>

<span data-ttu-id="8624e-1348">Se il *espressione* dispone del tipo in fase di compilazione `dynamic`, il *delegate_creation_expression* viene associata in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)) e le regole seguenti vengono applicati in fase di esecuzione utilizzando il tipo di fase di esecuzione del *espressione*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1348">If the *expression* has the compile-time type `dynamic`, the *delegate_creation_expression* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)), and the rules below are applied at run-time using the run-time type of the *expression*.</span></span> <span data-ttu-id="8624e-1349">In caso contrario, le regole vengono applicate in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1349">Otherwise the rules are applied at compile-time.</span></span>

<span data-ttu-id="8624e-1350">L'elaborazione in fase di associazione di un *delegate_creation_expression* del form `new D(E)`, dove `D` è un *delegate_type* e `E` è un *espressione* , costituita dai passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1350">The binding-time processing of a *delegate_creation_expression* of the form `new D(E)`, where `D` is a *delegate_type* and `E` is an *expression*, consists of the following steps:</span></span>

*  <span data-ttu-id="8624e-1351">Se `E` è un gruppo di metodi, l'espressione di creazione del delegato viene elaborato esattamente come la conversione di un gruppo di metodi ([conversioni dei metodi di gruppo](conversions.md#method-group-conversions)) dal `E` a `D`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1351">If `E` is a method group, the delegate creation expression is processed in the same way as a method group conversion ([Method group conversions](conversions.md#method-group-conversions)) from `E` to `D`.</span></span>
*  <span data-ttu-id="8624e-1352">Se `E` è una funzione anonima, l'espressione di creazione del delegato viene elaborato esattamente una conversione da funzione anonima ([conversioni delle funzioni anonime](conversions.md#anonymous-function-conversions)) dal `E` a `D`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1352">If `E` is an anonymous function, the delegate creation expression is processed in the same way as an anonymous function conversion ([Anonymous function conversions](conversions.md#anonymous-function-conversions)) from `E` to `D`.</span></span>
*  <span data-ttu-id="8624e-1353">Se `E` è un valore `E` devono essere compatibili ([dichiarazioni Delegate](delegates.md#delegate-declarations)) con `D`, e il risultato è un riferimento a un delegato appena creato del tipo `D` che fa riferimento alla stessa chiamata elencato come `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1353">If `E` is a value, `E` must be compatible ([Delegate declarations](delegates.md#delegate-declarations)) with `D`, and the result is a reference to a newly created delegate of type `D` that refers to the same invocation list as `E`.</span></span> <span data-ttu-id="8624e-1354">Se `E` non è compatibile con `D`, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1354">If `E` is not compatible with `D`, a compile-time error occurs.</span></span>

<span data-ttu-id="8624e-1355">L'elaborazione in fase di esecuzione di un *delegate_creation_expression* del form `new D(E)`, dove `D` è un *delegate_type* e `E` è un *espressione* , costituita dai passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1355">The run-time processing of a *delegate_creation_expression* of the form `new D(E)`, where `D` is a *delegate_type* and `E` is an *expression*, consists of the following steps:</span></span>

*   <span data-ttu-id="8624e-1356">Se `E` è un gruppo di metodi, l'espressione di creazione del delegato viene valutata come una conversione del gruppo (metodo) ([conversioni dei metodi di gruppo](conversions.md#method-group-conversions)) dal `E` a `D`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1356">If `E` is a method group, the delegate creation expression is evaluated as a method group conversion ([Method group conversions](conversions.md#method-group-conversions)) from `E` to `D`.</span></span>
*   <span data-ttu-id="8624e-1357">Se `E` è una funzione anonima, la creazione del delegato viene valutata come conversione da funzione anonima `E` al `D` ([conversioni delle funzioni anonime](conversions.md#anonymous-function-conversions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1357">If `E` is an anonymous function, the delegate creation is evaluated as an anonymous function conversion from `E` to `D` ([Anonymous function conversions](conversions.md#anonymous-function-conversions)).</span></span>
*   <span data-ttu-id="8624e-1358">Se `E` è un valore di una *delegate_type*:</span><span class="sxs-lookup"><span data-stu-id="8624e-1358">If `E` is a value of a *delegate_type*:</span></span>
    * <span data-ttu-id="8624e-1359">`E` viene valutato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1359">`E` is evaluated.</span></span> <span data-ttu-id="8624e-1360">Se questa versione di valutazione causa un'eccezione, non verrà eseguito alcun ulteriore passaggio.</span><span class="sxs-lookup"><span data-stu-id="8624e-1360">If this evaluation causes an exception, no further steps are executed.</span></span>
    * <span data-ttu-id="8624e-1361">Se il valore di `E` viene `null`, un `System.NullReferenceException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.</span><span class="sxs-lookup"><span data-stu-id="8624e-1361">If the value of `E` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
    * <span data-ttu-id="8624e-1362">Una nuova istanza del tipo delegato `D` viene allocato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1362">A new instance of the delegate type `D` is allocated.</span></span> <span data-ttu-id="8624e-1363">Se non è disponibile memoria sufficiente per allocare la nuova istanza, una `System.OutOfMemoryException` viene generata un'eccezione e non viene eseguito alcun ulteriore passaggio.</span><span class="sxs-lookup"><span data-stu-id="8624e-1363">If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.</span></span>
    * <span data-ttu-id="8624e-1364">La nuova istanza di delegato viene inizializzata con lo stesso elenco chiamate dell'istanza del delegato rappresentato da `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1364">The new delegate instance is initialized with the same invocation list as the delegate instance given by `E`.</span></span>

<span data-ttu-id="8624e-1365">L'elenco chiamate di un delegato viene determinato quando viene creata un'istanza di delegato e quindi rimane costante per l'intera durata del delegato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1365">The invocation list of a delegate is determined when the delegate is instantiated and then remains constant for the entire lifetime of the delegate.</span></span> <span data-ttu-id="8624e-1366">Non in altre parole, è possibile modificare le entità richiamabile destinazione di un delegato dopo che è stato creato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1366">In other words, it is not possible to change the target callable entities of a delegate once it has been created.</span></span> <span data-ttu-id="8624e-1367">Quando si combinano due delegati o uno viene rimosso da un altro ([dichiarazioni Delegate](delegates.md#delegate-declarations)), un nuovo delegato; nessun delegato esistente ha il suo contenuto modificato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1367">When two delegates are combined or one is removed from another ([Delegate declarations](delegates.md#delegate-declarations)), a new delegate results; no existing delegate has its contents changed.</span></span>

<span data-ttu-id="8624e-1368">Non è possibile creare un delegato che fa riferimento a una proprietà, indicizzatore, l'operatore definito dall'utente, costruttore di istanza, distruttore o costruttore statico.</span><span class="sxs-lookup"><span data-stu-id="8624e-1368">It is not possible to create a delegate that refers to a property, indexer, user-defined operator, instance constructor, destructor, or static constructor.</span></span>

<span data-ttu-id="8624e-1369">Come descritto in precedenza, quando viene creato un delegato da un gruppo di metodi, l'elenco di parametri formali e tipo restituito del delegato di determinare quale dei metodi di overload per selezionare.</span><span class="sxs-lookup"><span data-stu-id="8624e-1369">As described above, when a delegate is created from a method group, the formal parameter list and return type of the delegate determine which of the overloaded methods to select.</span></span> <span data-ttu-id="8624e-1370">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-1370">In the example</span></span>
```csharp
delegate double DoubleFunc(double x);

class A
{
    DoubleFunc f = new DoubleFunc(Square);

    static float Square(float x) {
        return x * x;
    }

    static double Square(double x) {
        return x * x;
    }
}
```
<span data-ttu-id="8624e-1371">il `A.f` campo viene inizializzato con un delegato che fa riferimento al secondo `Square` metodo perché tale metodo corrisponde esattamente all'elenco di parametri formali e tipo restituito di `DoubleFunc`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1371">the `A.f` field is initialized with a delegate that refers to the second `Square` method because that method exactly matches the formal parameter list and return type of `DoubleFunc`.</span></span> <span data-ttu-id="8624e-1372">Era il secondo `Square` (metodo) non è stato presenta, potrebbe essersi verificato un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1372">Had the second `Square` method not been present, a compile-time error would have occurred.</span></span>

#### <a name="anonymous-object-creation-expressions"></a><span data-ttu-id="8624e-1373">Espressioni per la creazione di oggetti anonimi</span><span class="sxs-lookup"><span data-stu-id="8624e-1373">Anonymous object creation expressions</span></span>

<span data-ttu-id="8624e-1374">Un' *anonymous_object_creation_expression* viene usato per creare un oggetto di un tipo anonimo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1374">An *anonymous_object_creation_expression* is used to create an object of an anonymous type.</span></span>

```antlr
anonymous_object_creation_expression
    : 'new' anonymous_object_initializer
    ;

anonymous_object_initializer
    : '{' member_declarator_list? '}'
    | '{' member_declarator_list ',' '}'
    ;

member_declarator_list
    : member_declarator (',' member_declarator)*
    ;

member_declarator
    : simple_name
    | member_access
    | base_access
    | null_conditional_member_access
    | identifier '=' expression
    ;
```

<span data-ttu-id="8624e-1375">Un inizializzatore di oggetto anonimi dichiara un tipo anonimo e restituisce un'istanza di quel tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1375">An anonymous object initializer declares an anonymous type and returns an instance of that type.</span></span> <span data-ttu-id="8624e-1376">Un tipo anonimo è un tipo di classe anonimi che eredita direttamente da `object`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1376">An anonymous type is a nameless class type that inherits directly from `object`.</span></span> <span data-ttu-id="8624e-1377">I membri di un tipo anonimo sono una sequenza di sola lettura proprietà dedotte dall'inizializzatore di oggetto anonimo usato per creare un'istanza del tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1377">The members of an anonymous type are a sequence of read-only properties inferred from the anonymous object initializer used to create an instance of the type.</span></span> <span data-ttu-id="8624e-1378">In particolare, un inizializzatore di oggetto anonimo del form</span><span class="sxs-lookup"><span data-stu-id="8624e-1378">Specifically, an anonymous object initializer of the form</span></span>
```csharp
new { p1 = e1, p2 = e2, ..., pn = en }
```
<span data-ttu-id="8624e-1379">dichiara un tipo anonimo del form</span><span class="sxs-lookup"><span data-stu-id="8624e-1379">declares an anonymous type of the form</span></span>
```csharp
class __Anonymous1
{
    private readonly T1 f1;
    private readonly T2 f2;
    ...
    private readonly Tn fn;

    public __Anonymous1(T1 a1, T2 a2, ..., Tn an) {
        f1 = a1;
        f2 = a2;
        ...
        fn = an;
    }

    public T1 p1 { get { return f1; } }
    public T2 p2 { get { return f2; } }
    ...
    public Tn pn { get { return fn; } }

    public override bool Equals(object __o) { ... }
    public override int GetHashCode() { ... }
}
```
<span data-ttu-id="8624e-1380">in cui ciascuna `Tx` è il tipo dell'espressione corrispondente `ex`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1380">where each `Tx` is the type of the corresponding expression `ex`.</span></span> <span data-ttu-id="8624e-1381">L'espressione utilizzata in una *member_declarator* deve presentare un tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1381">The expression used in a *member_declarator* must have a type.</span></span> <span data-ttu-id="8624e-1382">Di conseguenza, è un errore in fase di compilazione per un'espressione in una *member_declarator* deve essere null o una funzione anonima.</span><span class="sxs-lookup"><span data-stu-id="8624e-1382">Thus, it is a compile-time error for an expression in a *member_declarator* to be null or an anonymous function.</span></span> <span data-ttu-id="8624e-1383">È anche un errore in fase di compilazione per l'espressione presentare un tipo unsafe.</span><span class="sxs-lookup"><span data-stu-id="8624e-1383">It is also a compile-time error for the expression to have an unsafe type.</span></span>

<span data-ttu-id="8624e-1384">I nomi di un tipo anonimo e del parametro da relativo `Equals` metodo vengono generati automaticamente dal compilatore e non è possibile farvi riferimento nel testo di programma.</span><span class="sxs-lookup"><span data-stu-id="8624e-1384">The names of an anonymous type and of the parameter to its `Equals` method are automatically generated by the compiler and cannot be referenced in program text.</span></span>

<span data-ttu-id="8624e-1385">All'interno del programma stesso, due inizializzatori di oggetto anonimi che specificano una sequenza di proprietà degli stessi nomi e tipi in fase di compilazione nello stesso ordine produrranno istanze dello stesso tipo anonimo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1385">Within the same program, two anonymous object initializers that specify a sequence of properties of the same names and compile-time types in the same order will produce instances of the same anonymous type.</span></span>

<span data-ttu-id="8624e-1386">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-1386">In the example</span></span>
```csharp
var p1 = new { Name = "Lawnmower", Price = 495.00 };
var p2 = new { Name = "Shovel", Price = 26.95 };
p1 = p2;
```
<span data-ttu-id="8624e-1387">l'assegnazione nell'ultima riga è consentito perché `p1` e `p2` sono dello stesso tipo anonimo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1387">the assignment on the last line is permitted because `p1` and `p2` are of the same anonymous type.</span></span>

<span data-ttu-id="8624e-1388">Il `Equals` e `GetHashcode` metodi nei tipi anonimi di eseguire l'override dei metodi ereditati dalla `object`e vengono definiti in termini del `Equals` e `GetHashcode` delle proprietà, in modo che due istanze dello stesso tipo anonimo sono uguali se e solo se tutte le relative proprietà sono uguali.</span><span class="sxs-lookup"><span data-stu-id="8624e-1388">The `Equals` and `GetHashcode` methods on anonymous types override the methods inherited from `object`, and are defined in terms of the `Equals` and `GetHashcode` of the properties, so that two instances of the same anonymous type are equal if and only if all their properties are equal.</span></span>

<span data-ttu-id="8624e-1389">Un dichiaratore di membro può essere abbreviato utilizzando un nome semplice ([inferenza](expressions.md#type-inference)), un accesso ai membri ([controllo della risoluzione dell'overload dinamico in fase di compilazione](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), un accesso di base ([accessdiBase](expressions.md#base-access)) o un accesso ai membri condizionali null ([espressioni condizionali Null come gli inizializzatori di proiezione](expressions.md#null-conditional-expressions-as-projection-initializers)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1389">A member declarator can be abbreviated to a simple name ([Type inference](expressions.md#type-inference)), a member access ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), a base access ([Base access](expressions.md#base-access)) or a null-conditional member access ([Null-conditional expressions as projection initializers](expressions.md#null-conditional-expressions-as-projection-initializers)).</span></span> <span data-ttu-id="8624e-1390">Questa operazione viene definita un' ***inizializzatori di proiezione*** ed è l'abbreviazione per una dichiarazione di e l'assegnazione a una proprietà con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="8624e-1390">This is called a ***projection initializer*** and is shorthand for a declaration of and assignment to a property with the same name.</span></span> <span data-ttu-id="8624e-1391">In particolare, i dichiaratori di membri dei moduli</span><span class="sxs-lookup"><span data-stu-id="8624e-1391">Specifically, member declarators of the forms</span></span>
```csharp
identifier
expr.identifier
```
<span data-ttu-id="8624e-1392">equivalgono esattamente alle seguenti, rispettivamente:</span><span class="sxs-lookup"><span data-stu-id="8624e-1392">are precisely equivalent to the following, respectively:</span></span>
```csharp
identifier = identifier
identifier = expr.identifier
```

<span data-ttu-id="8624e-1393">Di conseguenza, in un inizializzatore di proiezione le *identificatore* consente di selezionare il valore e il campo o proprietà a cui è assegnato il valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1393">Thus, in a projection initializer the *identifier* selects both the value and the field or property to which the value is assigned.</span></span> <span data-ttu-id="8624e-1394">Un inizializzatore di proiezione progetti in modo intuitivo, non solo un valore, ma anche il nome del valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1394">Intuitively, a projection initializer projects not just a value, but also the name of the value.</span></span>

### <a name="the-typeof-operator"></a><span data-ttu-id="8624e-1395">L'operatore typeof</span><span class="sxs-lookup"><span data-stu-id="8624e-1395">The typeof operator</span></span>

<span data-ttu-id="8624e-1396">Il `typeof` operatore viene usato per ottenere il `System.Type` oggetto per un tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1396">The `typeof` operator is used to obtain the `System.Type` object for a type.</span></span>

```antlr
typeof_expression
    : 'typeof' '(' type ')'
    | 'typeof' '(' unbound_type_name ')'
    | 'typeof' '(' 'void' ')'
    ;

unbound_type_name
    : identifier generic_dimension_specifier?
    | identifier '::' identifier generic_dimension_specifier?
    | unbound_type_name '.' identifier generic_dimension_specifier?
    ;

generic_dimension_specifier
    : '<' comma* '>'
    ;

comma
    : ','
    ;
```

<span data-ttu-id="8624e-1397">La prima forma del *typeof_expression* costituito da un `typeof` parola chiave seguita da una parentesi *tipo*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1397">The first form of *typeof_expression* consists of a `typeof` keyword followed by a parenthesized *type*.</span></span> <span data-ttu-id="8624e-1398">Il risultato di un'espressione di questo modulo è il `System.Type` oggetto per il tipo indicato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1398">The result of an expression of this form is the `System.Type` object for the indicated type.</span></span> <span data-ttu-id="8624e-1399">È presente un solo `System.Type` oggetto per un determinato tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1399">There is only one `System.Type` object for any given type.</span></span> <span data-ttu-id="8624e-1400">Ciò significa che per un tipo `T`, `typeof(T) == typeof(T)` è sempre true.</span><span class="sxs-lookup"><span data-stu-id="8624e-1400">This means that for a type `T`, `typeof(T) == typeof(T)` is always true.</span></span> <span data-ttu-id="8624e-1401">Il *tipo* non può essere `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1401">The *type* cannot be `dynamic`.</span></span>

<span data-ttu-id="8624e-1402">La seconda forma di *typeof_expression* costituito da un `typeof` parola chiave seguita da una parentesi *unbound_type_name*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1402">The second form of *typeof_expression* consists of a `typeof` keyword followed by a parenthesized *unbound_type_name*.</span></span> <span data-ttu-id="8624e-1403">Un' *unbound_type_name* è molto simile a un *type_name* ([Namespace e dei tipi](basic-concepts.md#namespace-and-type-names)) ad eccezione del fatto che un *unbound_type_name* contiene *generic_dimension_specifier*s in cui un *type_name* contiene *type_argument_list*s.</span><span class="sxs-lookup"><span data-stu-id="8624e-1403">An *unbound_type_name* is very similar to a *type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)) except that an *unbound_type_name* contains *generic_dimension_specifier*s where a *type_name* contains *type_argument_list*s.</span></span> <span data-ttu-id="8624e-1404">Quando l'operando di un *typeof_expression* è una sequenza di token che soddisfi le grammatiche di entrambi *unbound_type_name* e *type_name*, vale a dire quando questo contiene né una *generic_dimension_specifier* né *type_argument_list*, la sequenza di token è considerata un *type_name*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1404">When the operand of a *typeof_expression* is a sequence of tokens that satisfies the grammars of both *unbound_type_name* and *type_name*, namely when it contains neither a *generic_dimension_specifier* nor a *type_argument_list*, the sequence of tokens is considered to be a *type_name*.</span></span> <span data-ttu-id="8624e-1405">Il significato di un *unbound_type_name* viene determinata come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-1405">The meaning of an *unbound_type_name* is determined as follows:</span></span>

*  <span data-ttu-id="8624e-1406">Converte la sequenza di token a un *type_name* sostituendo ogni *generic_dimension_specifier* con un *type_argument_list* aventi lo stesso numero di virgole e il parola chiave `object` come ognuno *type_argument*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1406">Convert the sequence of tokens to a *type_name* by replacing each *generic_dimension_specifier* with a *type_argument_list* having the same number of commas and the keyword `object` as each *type_argument*.</span></span>
*  <span data-ttu-id="8624e-1407">Valutare l'oggetto risultante *type_name*, ignorando tutti i vincoli del parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1407">Evaluate the resulting *type_name*, while ignoring all type parameter constraints.</span></span>
*  <span data-ttu-id="8624e-1408">Il *unbound_type_name* risolta nel tipo generico non associato associato con il tipo costruito risultante ([associato e non associato tipi](types.md#bound-and-unbound-types)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1408">The *unbound_type_name* resolves to the unbound generic type associated with the resulting constructed type ([Bound and unbound types](types.md#bound-and-unbound-types)).</span></span>

<span data-ttu-id="8624e-1409">Il risultato del *typeof_expression* è il `System.Type` per risultante non associato di tipo generico dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="8624e-1409">The result of the *typeof_expression* is the `System.Type` object for the resulting unbound generic type.</span></span>

<span data-ttu-id="8624e-1410">Terzo costituiti *typeof_expression* è costituito da un `typeof` parola chiave seguita da una parentesi `void` (parola chiave).</span><span class="sxs-lookup"><span data-stu-id="8624e-1410">The third form of *typeof_expression* consists of a `typeof` keyword followed by a parenthesized `void` keyword.</span></span> <span data-ttu-id="8624e-1411">Il risultato di un'espressione di questo modulo è il `System.Type` oggetto che rappresenta l'assenza di un tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1411">The result of an expression of this form is the `System.Type` object that represents the absence of a type.</span></span> <span data-ttu-id="8624e-1412">L'oggetto di tipo restituito da `typeof(void)` è diverso dall'oggetto di tipo restituito per qualsiasi tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1412">The type object returned by `typeof(void)` is distinct from the type object returned for any type.</span></span> <span data-ttu-id="8624e-1413">Questo oggetto di tipo speciale è utile nelle librerie di classi che consentono la reflection su metodi nel linguaggio in cui si desideri sono un modo per rappresentare il tipo restituito di qualsiasi metodo, inclusi i metodi di void, con un'istanza di tali metodi `System.Type`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1413">This special type object is useful in class libraries that allow reflection onto methods in the language, where those methods wish to have a way to represent the return type of any method, including void methods, with an instance of `System.Type`.</span></span>

<span data-ttu-id="8624e-1414">Il `typeof` operatore può essere usato in un parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1414">The `typeof` operator can be used on a type parameter.</span></span> <span data-ttu-id="8624e-1415">Il risultato è il `System.Type` oggetto per il tipo di runtime che è stato associato al parametro di tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1415">The result is the `System.Type` object for the run-time type that was bound to the type parameter.</span></span> <span data-ttu-id="8624e-1416">Il `typeof` operatore può essere utilizzato anche in un tipo costruito o un tipo generico non associato ([associate e non associato tipi](types.md#bound-and-unbound-types)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1416">The `typeof` operator can also be used on a constructed type or an unbound generic type ([Bound and unbound types](types.md#bound-and-unbound-types)).</span></span> <span data-ttu-id="8624e-1417">Il `System.Type` dell'oggetto per un tipo generico non associato non è lo stesso come il `System.Type` oggetto del tipo di istanza.</span><span class="sxs-lookup"><span data-stu-id="8624e-1417">The `System.Type` object for an unbound generic type is not the same as the `System.Type` object of the instance type.</span></span> <span data-ttu-id="8624e-1418">Il tipo di istanza è sempre un tipo costruito chiuso in fase di esecuzione in modo relativo `System.Type` oggetto dipende dagli argomenti di tipo di runtime in uso, mentre il tipo generico non ha alcun argomento tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1418">The instance type is always a closed constructed type at run-time so its `System.Type` object depends on the run-time type arguments in use, while the unbound generic type has no type arguments.</span></span>

<span data-ttu-id="8624e-1419">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-1419">The example</span></span>
```csharp
using System;

class X<T>
{
    public static void PrintTypes() {
        Type[] t = {
            typeof(int),
            typeof(System.Int32),
            typeof(string),
            typeof(double[]),
            typeof(void),
            typeof(T),
            typeof(X<T>),
            typeof(X<X<T>>),
            typeof(X<>)
        };
        for (int i = 0; i < t.Length; i++) {
            Console.WriteLine(t[i]);
        }
    }
}

class Test
{
    static void Main() {
        X<int>.PrintTypes();
    }
}
```
<span data-ttu-id="8624e-1420">produce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="8624e-1420">produces the following output:</span></span>
```
System.Int32
System.Int32
System.String
System.Double[]
System.Void
System.Int32
X`1[System.Int32]
X`1[X`1[System.Int32]]
X`1[T]
```

<span data-ttu-id="8624e-1421">Si noti che `int` e `System.Int32` sono dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1421">Note that `int` and `System.Int32` are the same type.</span></span>

<span data-ttu-id="8624e-1422">Si noti anche che il risultato del `typeof(X<>)` non dipende dall'argomento tipo, ma il risultato di `typeof(X<T>)` viene.</span><span class="sxs-lookup"><span data-stu-id="8624e-1422">Also note that the result of `typeof(X<>)` does not depend on the type argument but the result of `typeof(X<T>)` does.</span></span>

### <a name="the-checked-and-unchecked-operators"></a><span data-ttu-id="8624e-1423">Gli operatori checked e unchecked</span><span class="sxs-lookup"><span data-stu-id="8624e-1423">The checked and unchecked operators</span></span>

<span data-ttu-id="8624e-1424">Il `checked` e `unchecked` gli operatori vengono utilizzati per controllare le ***contesto di controllo dell'overflow*** per le conversioni e operazioni aritmetiche di tipo integrale.</span><span class="sxs-lookup"><span data-stu-id="8624e-1424">The `checked` and `unchecked` operators are used to control the ***overflow checking context*** for integral-type arithmetic operations and conversions.</span></span>

```antlr
checked_expression
    : 'checked' '(' expression ')'
    ;

unchecked_expression
    : 'unchecked' '(' expression ')'
    ;
```

<span data-ttu-id="8624e-1425">Il `checked` operatore valuta l'espressione contenuta in un contesto controllato e il `unchecked` operatore valuta l'espressione contenuta in un contesto non verificato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1425">The `checked` operator evaluates the contained expression in a checked context, and the `unchecked` operator evaluates the contained expression in an unchecked context.</span></span> <span data-ttu-id="8624e-1426">Oggetto *checked_expression* oppure *unchecked_expression* corrispondono esattamente a una *parenthesized_expression* ([espressioni tra parentesi](expressions.md#parenthesized-expressions)), ad eccezione del fatto che viene valutata l'espressione contenuta nel contesto di controllo dell'overflow specificata.</span><span class="sxs-lookup"><span data-stu-id="8624e-1426">A *checked_expression* or *unchecked_expression* corresponds exactly to a *parenthesized_expression* ([Parenthesized expressions](expressions.md#parenthesized-expressions)), except that the contained expression is evaluated in the given overflow checking context.</span></span>

<span data-ttu-id="8624e-1427">Contesto di controllo dell'overflow può anche essere controllato tramite il `checked` e `unchecked` istruzioni ([le istruzioni checked e unchecked](statements.md#the-checked-and-unchecked-statements)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1427">The overflow checking context can also be controlled through the `checked` and `unchecked` statements ([The checked and unchecked statements](statements.md#the-checked-and-unchecked-statements)).</span></span>

<span data-ttu-id="8624e-1428">Le operazioni seguenti sono interessate dal contesto definito dal controllo dell'overflow di `checked` e `unchecked` operatori e le istruzioni:</span><span class="sxs-lookup"><span data-stu-id="8624e-1428">The following operations are affected by the overflow checking context established by the `checked` and `unchecked` operators and statements:</span></span>

*  <span data-ttu-id="8624e-1429">Oggetto predefinito `++` e `--` operatori unari ([forma suffissa di incremento e decremento (operatori)](expressions.md#postfix-increment-and-decrement-operators) e [incremento prefisso e decremento (operatori)](expressions.md#prefix-increment-and-decrement-operators)), quando l'operando è di un valore integrale tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1429">The predefined `++` and `--` unary operators ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators) and [Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)), when the operand is of an integral type.</span></span>
*  <span data-ttu-id="8624e-1430">Oggetto predefinito `-` operatore unario ([operatore meno unario](expressions.md#unary-minus-operator)), quando l'operando è di tipo integrale.</span><span class="sxs-lookup"><span data-stu-id="8624e-1430">The predefined `-` unary operator ([Unary minus operator](expressions.md#unary-minus-operator)), when the operand is of an integral type.</span></span>
*  <span data-ttu-id="8624e-1431">Oggetto predefinito `+`, `-`, `*`, e `/` operatori binari ([operatori aritmetici](expressions.md#arithmetic-operators)), quando entrambi gli operandi sono di tipi integrali.</span><span class="sxs-lookup"><span data-stu-id="8624e-1431">The predefined `+`, `-`, `*`, and `/` binary operators ([Arithmetic operators](expressions.md#arithmetic-operators)), when both operands are of integral types.</span></span>
*  <span data-ttu-id="8624e-1432">Conversioni numeriche esplicite ([conversioni numeriche esplicite](conversions.md#explicit-numeric-conversions)) da un tipo integrale a un altro tipo integrale o da `float` o `double` a un tipo integrale.</span><span class="sxs-lookup"><span data-stu-id="8624e-1432">Explicit numeric conversions ([Explicit numeric conversions](conversions.md#explicit-numeric-conversions)) from one integral type to another integral type, or from `float` or `double` to an integral type.</span></span>

<span data-ttu-id="8624e-1433">Quando una delle operazioni precedenti produrre un risultato che è troppo grande per essere rappresentato nel tipo di destinazione, il contesto in cui l'operazione viene eseguiti controlli il comportamento risultante:</span><span class="sxs-lookup"><span data-stu-id="8624e-1433">When one of the above operations produce a result that is too large to represent in the destination type, the context in which the operation is performed controls the resulting behavior:</span></span>

*  <span data-ttu-id="8624e-1434">In un `checked` contesto, se l'operazione è un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)), si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1434">In a `checked` context, if the operation is a constant expression ([Constant expressions](expressions.md#constant-expressions)), a compile-time error occurs.</span></span> <span data-ttu-id="8624e-1435">In caso contrario, quando l'operazione viene eseguita in fase di esecuzione, un `System.OverflowException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1435">Otherwise, when the operation is performed at run-time, a `System.OverflowException` is thrown.</span></span>
*  <span data-ttu-id="8624e-1436">In un `unchecked` contesto, il risultato viene troncato eliminando qualsiasi bit più significativi che non rientrano nel tipo di destinazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1436">In an `unchecked` context, the result is truncated by discarding any high-order bits that do not fit in the destination type.</span></span>

<span data-ttu-id="8624e-1437">Per le espressioni non costanti (espressioni che vengono valutate in fase di esecuzione) che non sono racchiusi da qualsiasi `checked` oppure `unchecked` operatori o istruzioni, il contesto di controllo dell'overflow di predefinita è `unchecked` a meno che non fattori esterni (ad esempio compilatore opzioni e la configurazione dell'ambiente di esecuzione) chiama per `checked` valutazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1437">For non-constant expressions (expressions that are evaluated at run-time) that are not enclosed by any `checked` or `unchecked` operators or statements, the default overflow checking context is `unchecked` unless external factors (such as compiler switches and execution environment configuration) call for `checked` evaluation.</span></span>

<span data-ttu-id="8624e-1438">Per le espressioni costanti (espressioni che possono essere valutate interamente in fase di compilazione), il contesto di controllo dell'overflow di predefinita è sempre `checked`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1438">For constant expressions (expressions that can be fully evaluated at compile-time), the default overflow checking context is always `checked`.</span></span> <span data-ttu-id="8624e-1439">A meno che un'espressione costante viene inserita in modo esplicito un `unchecked` contesto, overflow che si verificano durante la valutazione in fase di compilazione dell'espressione sempre causano errori in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1439">Unless a constant expression is explicitly placed in an `unchecked` context, overflows that occur during the compile-time evaluation of the expression always cause compile-time errors.</span></span>

<span data-ttu-id="8624e-1440">Il corpo di una funzione anonima non è influenzato `checked` o `unchecked` contesti in cui si verifica la funzione anonima.</span><span class="sxs-lookup"><span data-stu-id="8624e-1440">The body of an anonymous function is not affected by `checked` or `unchecked` contexts in which the anonymous function occurs.</span></span>

<span data-ttu-id="8624e-1441">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-1441">In the example</span></span>
```csharp
class Test
{
    static readonly int x = 1000000;
    static readonly int y = 1000000;

    static int F() {
        return checked(x * y);      // Throws OverflowException
    }

    static int G() {
        return unchecked(x * y);    // Returns -727379968
    }

    static int H() {
        return x * y;               // Depends on default
    }
}
```
<span data-ttu-id="8624e-1442">non in fase di compilazione vengono segnalati errori poiché nessuna delle espressioni può essere valutata in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1442">no compile-time errors are reported since neither of the expressions can be evaluated at compile-time.</span></span> <span data-ttu-id="8624e-1443">In fase di esecuzione, il `F` metodo genera un `System.OverflowException`e il `G` metodo restituisce-727379968 (il più basso 32 bit del risultato out-of-range).</span><span class="sxs-lookup"><span data-stu-id="8624e-1443">At run-time, the `F` method throws a `System.OverflowException`, and the `G` method returns -727379968 (the lower 32 bits of the out-of-range result).</span></span> <span data-ttu-id="8624e-1444">Il comportamento dei `H` metodo dipende dal contesto per la compilazione di controllo dell'overflow di impostazione predefinita, ma è lo stesso `F` o uguale a quella `G`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1444">The behavior of the `H` method depends on the default overflow checking context for the compilation, but it is either the same as `F` or the same as `G`.</span></span>

<span data-ttu-id="8624e-1445">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-1445">In the example</span></span>
```csharp
class Test
{
    const int x = 1000000;
    const int y = 1000000;

    static int F() {
        return checked(x * y);      // Compile error, overflow
    }

    static int G() {
        return unchecked(x * y);    // Returns -727379968
    }

    static int H() {
        return x * y;               // Compile error, overflow
    }
}
```
<span data-ttu-id="8624e-1446">l'overflow che si verificano quando si valutano le espressioni costanti nelle `F` e `H` causano errori in fase di compilazione da segnalare perché le espressioni vengono valutate un `checked` contesto.</span><span class="sxs-lookup"><span data-stu-id="8624e-1446">the overflows that occur when evaluating the constant expressions in `F` and `H` cause compile-time errors to be reported because the expressions are evaluated in a `checked` context.</span></span> <span data-ttu-id="8624e-1447">Un overflow si verifica anche quando si valuta l'espressione costante nel `G`, ma poiché la versione di valutazione viene eseguita un `unchecked` contesto, non viene segnalata l'overflow.</span><span class="sxs-lookup"><span data-stu-id="8624e-1447">An overflow also occurs when evaluating the constant expression in `G`, but since the evaluation takes place in an `unchecked` context, the overflow is not reported.</span></span>

<span data-ttu-id="8624e-1448">Il `checked` e `unchecked` operatori influiscono solo sul contesto per tali operazioni testualmente contenuti all'interno di controllo dell'overflow di "`(`"e"`)`" token.</span><span class="sxs-lookup"><span data-stu-id="8624e-1448">The `checked` and `unchecked` operators only affect the overflow checking context for those operations that are textually contained within the "`(`" and "`)`" tokens.</span></span> <span data-ttu-id="8624e-1449">Gli operatori non hanno effetto sui membri di funzione che vengono richiamati come risultato di valutazione dell'espressione indipendente.</span><span class="sxs-lookup"><span data-stu-id="8624e-1449">The operators have no effect on function members that are invoked as a result of evaluating the contained expression.</span></span> <span data-ttu-id="8624e-1450">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-1450">In the example</span></span>
```csharp
class Test
{
    static int Multiply(int x, int y) {
        return x * y;
    }

    static int F() {
        return checked(Multiply(1000000, 1000000));
    }
}
```
<span data-ttu-id="8624e-1451">l'uso di `checked` nel `F` non influiscono sulla valutazione dei `x * y` in `Multiply`, quindi `x * y` viene valutato nel contesto di controllo dell'overflow predefinita.</span><span class="sxs-lookup"><span data-stu-id="8624e-1451">the use of `checked` in `F` does not affect the evaluation of `x * y` in `Multiply`, so `x * y` is evaluated in the default overflow checking context.</span></span>

<span data-ttu-id="8624e-1452">Il `unchecked` operatore è utile quando si scrive costanti dei tipi integrali con segno in notazione esadecimale.</span><span class="sxs-lookup"><span data-stu-id="8624e-1452">The `unchecked` operator is convenient when writing constants of the signed integral types in hexadecimal notation.</span></span> <span data-ttu-id="8624e-1453">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8624e-1453">For example:</span></span>
```csharp
class Test
{
    public const int AllBits = unchecked((int)0xFFFFFFFF);

    public const int HighBit = unchecked((int)0x80000000);
}
```

<span data-ttu-id="8624e-1454">Entrambe le costanti esadecimali riportato sopra sono di tipo `uint`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1454">Both of the hexadecimal constants above are of type `uint`.</span></span> <span data-ttu-id="8624e-1455">Poiché le costanti non sono comprese la `int` compresi, senza il `unchecked` operatore, i cast a `int` generano errori in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1455">Because the constants are outside the `int` range, without the `unchecked` operator, the casts to `int` would produce compile-time errors.</span></span>

<span data-ttu-id="8624e-1456">Il `checked` e `unchecked` operatori e le istruzioni consentono ai programmatori di controllare alcuni aspetti di alcuni calcoli numerici.</span><span class="sxs-lookup"><span data-stu-id="8624e-1456">The `checked` and `unchecked` operators and statements allow programmers to control certain aspects of some numeric calculations.</span></span> <span data-ttu-id="8624e-1457">Tuttavia, il comportamento di alcuni operatori numerici dipende da tipi di dati dei relativi operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-1457">However, the behavior of some numeric operators depends on their operands' data types.</span></span> <span data-ttu-id="8624e-1458">Ad esempio, moltiplicazione di due numeri decimali genera sempre un'eccezione in caso di overflow anche interno un esplicitamente `unchecked` costruire.</span><span class="sxs-lookup"><span data-stu-id="8624e-1458">For example, multiplying two decimals always results in an exception on overflow even within an explicitly `unchecked` construct.</span></span> <span data-ttu-id="8624e-1459">Analogamente, la moltiplicazione di due valori float non mai genera un'eccezione in caso di overflow anche interno un esplicitamente `checked` costruire.</span><span class="sxs-lookup"><span data-stu-id="8624e-1459">Similarly, multiplying two floats never results in an exception on overflow even within an explicitly `checked` construct.</span></span> <span data-ttu-id="8624e-1460">Inoltre, altri operatori mai sono interessate dalla modalità di controllo, predefinita o esplicita.</span><span class="sxs-lookup"><span data-stu-id="8624e-1460">In addition, other operators are never affected by the mode of checking, whether default or explicit.</span></span>

### <a name="default-value-expressions"></a><span data-ttu-id="8624e-1461">Espressioni con valore predefinito</span><span class="sxs-lookup"><span data-stu-id="8624e-1461">Default value expressions</span></span>

<span data-ttu-id="8624e-1462">Un'espressione con valore predefinito viene utilizzata per ottenere il valore predefinito ([i valori predefiniti](variables.md#default-values)) di un tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1462">A default value expression is used to obtain the default value ([Default values](variables.md#default-values)) of a type.</span></span> <span data-ttu-id="8624e-1463">In genere un'espressione con valore predefinito viene utilizzata per i parametri di tipo, poiché potrebbe non essere noto se il parametro di tipo è un tipo di valore o un tipo riferimento.</span><span class="sxs-lookup"><span data-stu-id="8624e-1463">Typically a default value expression is used for type parameters, since it may not be known if the type parameter is a value type or a reference type.</span></span> <span data-ttu-id="8624e-1464">(Nessuna conversione esistente dal `null` letterale per un parametro di tipo, a meno che il parametro di tipo è noto come tipo di riferimento.)</span><span class="sxs-lookup"><span data-stu-id="8624e-1464">(No conversion exists from the `null` literal to a type parameter unless the type parameter is known to be a reference type.)</span></span>

```antlr
default_value_expression
    : 'default' '(' type ')'
    ;
```

<span data-ttu-id="8624e-1465">Se il *tipo* in un *default_value_expression* viene valutata in fase di esecuzione a un tipo riferimento, il risultato è `null` convertiti in tale tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1465">If the *type* in a *default_value_expression* evaluates at run-time to a reference type, the result is `null` converted to that type.</span></span> <span data-ttu-id="8624e-1466">Se il *tipo* in un *default_value_expression* valuta in fase di esecuzione a un tipo valore, il risultato è la *value_type*del valore predefinito ([predefinito i costruttori](types.md#default-constructors)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1466">If the *type* in a *default_value_expression* evaluates at run-time to a value type, the result is the *value_type*'s default value ([Default constructors](types.md#default-constructors)).</span></span>

<span data-ttu-id="8624e-1467">Oggetto *default_value_expression* è un'espressione costante ([espressioni costanti](expressions.md#constant-expressions)) se il tipo è un tipo riferimento o un parametro di tipo noto per essere un tipo riferimento ([parametro di tipo i vincoli](classes.md#type-parameter-constraints)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1467">A *default_value_expression* is a constant expression ([Constant expressions](expressions.md#constant-expressions)) if the type is a reference type or a type parameter that is known to be a reference type ([Type parameter constraints](classes.md#type-parameter-constraints)).</span></span> <span data-ttu-id="8624e-1468">Inoltre, un *default_value_expression* è un'espressione costante se il tipo è uno dei seguenti tipi di valore: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, o qualsiasi tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1468">In addition, a *default_value_expression* is a constant expression if the type is one of the following value types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, or any enumeration type.</span></span>


### <a name="nameof-expressions"></a><span data-ttu-id="8624e-1469">Espressioni Nameof</span><span class="sxs-lookup"><span data-stu-id="8624e-1469">Nameof expressions</span></span>

<span data-ttu-id="8624e-1470">Oggetto *nameof_expression* viene usato per ottenere il nome di un'entità program sotto forma di stringa costante.</span><span class="sxs-lookup"><span data-stu-id="8624e-1470">A *nameof_expression* is used to obtain the name of a program entity as a constant string.</span></span>

```antlr
nameof_expression
    : 'nameof' '(' named_entity ')'
    ;

named_entity
    : simple_name
    | named_entity_target '.' identifier type_argument_list?
    ;

named_entity_target
    : 'this'
    | 'base'
    | named_entity 
    | predefined_type 
    | qualified_alias_member
    ;
```

<span data-ttu-id="8624e-1471">Grammatica a proposito, il *named_entity* operando è sempre un'espressione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1471">Grammatically speaking, the *named_entity* operand is always an expression.</span></span> <span data-ttu-id="8624e-1472">In quanto `nameof` non è una parola chiave riservata, un'espressione di nameof è sempre sintatticamente ambigua con una chiamata di nome semplice `nameof`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1472">Because `nameof` is not a reserved keyword, a nameof expression is always syntactically ambiguous with an invocation of the simple name `nameof`.</span></span> <span data-ttu-id="8624e-1473">Per motivi di compatibilità, se una ricerca del nome ([nomi semplici](expressions.md#simple-names)) del nome della `nameof` ha esito positivo, l'espressione viene considerata come un' *invocation_expression* : indipendentemente dal fatto che la chiamata è legali.</span><span class="sxs-lookup"><span data-stu-id="8624e-1473">For compatibility reasons, if a name lookup ([Simple names](expressions.md#simple-names)) of the name `nameof` succeeds, the expression is treated as an *invocation_expression* -- regardless of whether the invocation is legal.</span></span> <span data-ttu-id="8624e-1474">In caso contrario, è un *nameof_expression*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1474">Otherwise it is a *nameof_expression*.</span></span>

<span data-ttu-id="8624e-1475">Il significato del *named_entity* di un *nameof_expression* è il significato di esso come un'espressione; vale a dire, sia come un *simple_name*, un *base_access*  o un *member_access*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1475">The meaning of the *named_entity* of a *nameof_expression* is the meaning of it as an expression; that is, either as a *simple_name*, a *base_access* or a *member_access*.</span></span> <span data-ttu-id="8624e-1476">Tuttavia, in cui la ricerca è descritto nella [nomi semplici](expressions.md#simple-names) e [l'accesso ai membri](expressions.md#member-access) genera un errore perché è stato trovato un membro di istanza in un contesto statico, un *nameof_expression*non produce alcun errore di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1476">However, where the lookup described in [Simple names](expressions.md#simple-names) and [Member access](expressions.md#member-access) results in an error because an instance member was found in a static context, a *nameof_expression* produces no such error.</span></span>

<span data-ttu-id="8624e-1477">Tratta di un errore in fase di compilazione per un *named_entity* designazione di un gruppo di metodi per avere una *type_argument_list*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1477">It is a compile-time error for a *named_entity* designating a method group to have a *type_argument_list*.</span></span> <span data-ttu-id="8624e-1478">Si tratta di un errore in fase di compilazione per un *named_entity_target* con il tipo `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1478">It is a compile time error for a *named_entity_target* to have the type `dynamic`.</span></span>

<span data-ttu-id="8624e-1479">Oggetto *nameof_expression* è un'espressione costante di tipo `string`, e non ha alcun effetto in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1479">A *nameof_expression* is a constant expression of type `string`, and has no effect at runtime.</span></span> <span data-ttu-id="8624e-1480">In particolare, relativi *named_entity* non viene valutata e viene ignorato per gli scopi di analisi di assegnazione certa ([regole generali per le espressioni semplici](variables.md#general-rules-for-simple-expressions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1480">Specifically, its *named_entity* is not evaluated, and is ignored for the purposes of definite assignment analysis ([General rules for simple expressions](variables.md#general-rules-for-simple-expressions)).</span></span> <span data-ttu-id="8624e-1481">Il valore è l'ultimo identificatore del *named_entity* prima dell'istruzione facoltativa *type_argument_list*trasformato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="8624e-1481">Its value is the last identifier of the *named_entity* before the optional final *type_argument_list*, transformed in the following way:</span></span>

* <span data-ttu-id="8624e-1482">Il prefisso "`@`", se usata, è stato rimosso.</span><span class="sxs-lookup"><span data-stu-id="8624e-1482">The prefix "`@`", if used, is removed.</span></span>
* <span data-ttu-id="8624e-1483">Ciascuna *unicode_escape_sequence* viene trasformato nel carattere Unicode corrispondente.</span><span class="sxs-lookup"><span data-stu-id="8624e-1483">Each *unicode_escape_sequence* is transformed into its corresponding Unicode character.</span></span>
* <span data-ttu-id="8624e-1484">Eventuali *formatting_characters* vengono rimossi.</span><span class="sxs-lookup"><span data-stu-id="8624e-1484">Any *formatting_characters* are removed.</span></span>

<span data-ttu-id="8624e-1485">Queste sono le stesse trasformazioni applicate [identificatori](lexical-structure.md#identifiers) durante il test di uguaglianza tra gli identificatori.</span><span class="sxs-lookup"><span data-stu-id="8624e-1485">These are the same transformations applied in [Identifiers](lexical-structure.md#identifiers) when testing equality between identifiers.</span></span>

<span data-ttu-id="8624e-1486">TODO: esempi</span><span class="sxs-lookup"><span data-stu-id="8624e-1486">TODO: examples</span></span>

### <a name="anonymous-method-expressions"></a><span data-ttu-id="8624e-1487">Espressioni di metodo anonimo</span><span class="sxs-lookup"><span data-stu-id="8624e-1487">Anonymous method expressions</span></span>

<span data-ttu-id="8624e-1488">Un' *anonymous_method_expression* è uno dei due modi per definire una funzione anonima.</span><span class="sxs-lookup"><span data-stu-id="8624e-1488">An *anonymous_method_expression* is one of two ways of defining an anonymous function.</span></span> <span data-ttu-id="8624e-1489">Queste sono descritte dettagliatamente nella [espressioni di funzioni anonime](expressions.md#anonymous-function-expressions).</span><span class="sxs-lookup"><span data-stu-id="8624e-1489">These are further described in [Anonymous function expressions](expressions.md#anonymous-function-expressions).</span></span>

## <a name="unary-operators"></a><span data-ttu-id="8624e-1490">Operatori unari</span><span class="sxs-lookup"><span data-stu-id="8624e-1490">Unary operators</span></span>

<span data-ttu-id="8624e-1491">Il `?`, `+`, `-`, `!`, `~`, `++`, `--`, eseguire il cast, e `await` operatori vengono chiamati gli operatori unari.</span><span class="sxs-lookup"><span data-stu-id="8624e-1491">The `?`, `+`, `-`, `!`, `~`, `++`, `--`, cast, and `await` operators are called the unary operators.</span></span>

```antlr
unary_expression
    : primary_expression
    | null_conditional_expression
    | '+' unary_expression
    | '-' unary_expression
    | '!' unary_expression
    | '~' unary_expression
    | pre_increment_expression
    | pre_decrement_expression
    | cast_expression
    | await_expression
    | unary_expression_unsafe
    ;
```

<span data-ttu-id="8624e-1492">Se l'operando di un *unary_expression* dispone del tipo in fase di compilazione `dynamic`, in modo dinamico è associato ([binding dinamico](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1492">If the operand of a *unary_expression* has the compile-time type `dynamic`, it is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="8624e-1493">In questo caso di tipo in fase di compilazione del *unary_expression* è `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di fase di esecuzione dell'operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-1493">In this case the compile-time type of the *unary_expression* is `dynamic`, and the resolution described below will take place at run-time using the run-time type of the operand.</span></span>

### <a name="null-conditional-operator"></a><span data-ttu-id="8624e-1494">Operatore condizionale null</span><span class="sxs-lookup"><span data-stu-id="8624e-1494">Null-conditional operator</span></span>

<span data-ttu-id="8624e-1495">L'operatore condizionale null si applica un elenco di operazioni al relativo operando solo se l'operando è diverso da null.</span><span class="sxs-lookup"><span data-stu-id="8624e-1495">The null-conditional operator applies a list of operations to its operand only if that operand is non-null.</span></span> <span data-ttu-id="8624e-1496">In caso contrario, il risultato dell'applicazione dell'operatore è `null`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1496">Otherwise the result of applying the operator is `null`.</span></span>

```antlr
null_conditional_expression
    : primary_expression null_conditional_operations
    ;

null_conditional_operations
    : null_conditional_operations? '?' '.' identifier type_argument_list?
    | null_conditional_operations? '?' '[' argument_list ']'
    | null_conditional_operations '.' identifier type_argument_list?
    | null_conditional_operations '[' argument_list ']'
    | null_conditional_operations '(' argument_list? ')'
    ;
```

<span data-ttu-id="8624e-1497">L'elenco delle operazioni può includere l'accesso ai membri e le operazioni di accesso di elemento (che potrebbero essere essi stessi condizionali null), nonché la chiamata.</span><span class="sxs-lookup"><span data-stu-id="8624e-1497">The list of operations can include member access and element access operations (which may themselves be null-conditional), as well as invocation.</span></span>

<span data-ttu-id="8624e-1498">Ad esempio, l'espressione `a.b?[0]?.c()` è un *null_conditional_expression* con un *primary_expression* `a.b` e *null_conditional_operations* `?[0]` (accesso all'elemento condizionali null), `?.c` (accesso ai membri condizionali null) e `()` (chiamata).</span><span class="sxs-lookup"><span data-stu-id="8624e-1498">For example, the expression `a.b?[0]?.c()` is a *null_conditional_expression* with a *primary_expression* `a.b` and *null_conditional_operations* `?[0]` (null-conditional element access), `?.c` (null-conditional member access) and `()` (invocation).</span></span>

<span data-ttu-id="8624e-1499">Per un *null_conditional_expression* `E` con un *primary_expression* `P`, consentono `E0` l'espressione ottenuto rimuovendo testualmente iniziale `?`da ognuna delle *null_conditional_operations* di `E` che dispongono di uno.</span><span class="sxs-lookup"><span data-stu-id="8624e-1499">For a *null_conditional_expression* `E` with a *primary_expression* `P`, let `E0` be the expression obtained by textually removing the leading `?` from each of the *null_conditional_operations* of `E` that have one.</span></span> <span data-ttu-id="8624e-1500">Concettualmente `E0` è l'espressione che verrà valutato se nessuno dei controlli null rappresentato dal `?`s si trova un `null`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1500">Conceptually, `E0` is the expression that will be evaluated if none of the null checks represented by the `?`s do find a `null`.</span></span>

<span data-ttu-id="8624e-1501">Inoltre, consentire `E1` essere l'espressione ottenuto rimuovendo testualmente iniziale `?` da solo la prima del *null_conditional_operations* in `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1501">Also, let `E1` be the expression obtained by textually removing the leading `?` from just the first of the *null_conditional_operations* in `E`.</span></span> <span data-ttu-id="8624e-1502">Ciò potrebbe causare una *primary-expression* (se si è verificato solo uno `?`) o a un altro *null_conditional_expression*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1502">This may lead to a *primary-expression* (if there was just one `?`) or to another *null_conditional_expression*.</span></span>

<span data-ttu-id="8624e-1503">Ad esempio, se `E` è l'espressione `a.b?[0]?.c()`, quindi `E0` è l'espressione `a.b[0].c()` e `E1` espressione `a.b[0]?.c()`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1503">For example, if `E` is the expression `a.b?[0]?.c()`, then `E0` is the expression `a.b[0].c()` and `E1` is the expression `a.b[0]?.c()`.</span></span>

<span data-ttu-id="8624e-1504">Se `E0` viene classificato come nothing, quindi `E` viene classificato come nulla.</span><span class="sxs-lookup"><span data-stu-id="8624e-1504">If `E0` is classified as nothing, then `E` is classified as nothing.</span></span> <span data-ttu-id="8624e-1505">In caso contrario, E è classificato come un valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1505">Otherwise E is classified as a value.</span></span>

<span data-ttu-id="8624e-1506">`E0` e `E1` vengono usate per determinare il significato di `E`:</span><span class="sxs-lookup"><span data-stu-id="8624e-1506">`E0` and `E1` are used to determine the meaning of `E`:</span></span>

*  <span data-ttu-id="8624e-1507">Se `E` avviene quando un *statement_expression* il significato di `E` è quello utilizzato per l'istruzione</span><span class="sxs-lookup"><span data-stu-id="8624e-1507">If `E` occurs as a *statement_expression* the meaning of `E` is the same as the statement</span></span>

   ```csharp
   if ((object)P != null) E1;
   ```

   <span data-ttu-id="8624e-1508">ad eccezione del fatto che P viene valutato una sola volta.</span><span class="sxs-lookup"><span data-stu-id="8624e-1508">except that P is evaluated only once.</span></span>

*  <span data-ttu-id="8624e-1509">In caso contrario, se `E0` viene classificato come non si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1509">Otherwise, if `E0` is classified as nothing a compile-time error occurs.</span></span>

*  <span data-ttu-id="8624e-1510">In caso contrario, lasciare `T0` essere il tipo di `E0`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1510">Otherwise, let `T0` be the type of `E0`.</span></span>

   *  <span data-ttu-id="8624e-1511">Se `T0` è un parametro di tipo che non è noto per essere un tipo riferimento o un tipo di valore non nullable, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1511">If `T0` is a type parameter that is not known to be a reference type or a non-nullable value type, a compile-time error occurs.</span></span>

   *  <span data-ttu-id="8624e-1512">Se `T0` è un tipo di valore non nullable, è necessario il tipo di `E` viene `T0?`e il significato di `E` equivale a</span><span class="sxs-lookup"><span data-stu-id="8624e-1512">If `T0` is a non-nullable value type, then the type of `E` is `T0?`, and the meaning of `E` is the same as</span></span>

      ```csharp
      ((object)P == null) ? (T0?)null : E1
      ```

      <span data-ttu-id="8624e-1513">ad eccezione del fatto che `P` viene valutata una sola volta.</span><span class="sxs-lookup"><span data-stu-id="8624e-1513">except that `P` is evaluated only once.</span></span>

   *  <span data-ttu-id="8624e-1514">In caso contrario, il tipo dell'espressione E viene T0, e il significato di E è identico</span><span class="sxs-lookup"><span data-stu-id="8624e-1514">Otherwise the type of E is T0, and the meaning of E is the same as</span></span>

      ```csharp
      ((object)P == null) ? null : E1
      ```

      <span data-ttu-id="8624e-1515">ad eccezione del fatto che `P` viene valutata una sola volta.</span><span class="sxs-lookup"><span data-stu-id="8624e-1515">except that `P` is evaluated only once.</span></span>

<span data-ttu-id="8624e-1516">Se `E1` è a sua volta un *null_conditional_expression*, quindi cui queste regole vengono applicate anche in questo caso, la nidificazione dei test per `null` fino a quando non sono presenti ulteriori `?`del, e l'espressione è stata ridotta verso il basso per l'espressione primaria `E0`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1516">If `E1` is itself a *null_conditional_expression*, then these rules are applied again, nesting the tests for `null` until there are no further `?`'s, and the expression has been reduced all the way down to the primary-expression `E0`.</span></span>

<span data-ttu-id="8624e-1517">Ad esempio, se l'espressione `a.b?[0]?.c()` viene eseguita come un'espressione di istruzione, come l'istruzione:</span><span class="sxs-lookup"><span data-stu-id="8624e-1517">For example, if the expression `a.b?[0]?.c()` occurs as a statement-expression, as in the statement:</span></span>
```csharp
a.b?[0]?.c();
```
<span data-ttu-id="8624e-1518">è equivalente al relativo significato:</span><span class="sxs-lookup"><span data-stu-id="8624e-1518">its meaning is equivalent to:</span></span>
```csharp
if (a.b != null) a.b[0]?.c();
```
<span data-ttu-id="8624e-1519">anch ' esso equivalente a:</span><span class="sxs-lookup"><span data-stu-id="8624e-1519">which again is equivalent to:</span></span>
```csharp
if (a.b != null) if (a.b[0] != null) a.b[0].c();
```
<span data-ttu-id="8624e-1520">Con la differenza che `a.b` e `a.b[0]` vengono valutate una sola volta.</span><span class="sxs-lookup"><span data-stu-id="8624e-1520">Except that `a.b` and `a.b[0]` are evaluated only once.</span></span>

<span data-ttu-id="8624e-1521">Se si verifica in un contesto in cui viene utilizzato il valore, come in:</span><span class="sxs-lookup"><span data-stu-id="8624e-1521">If it occurs in a context where its value is used, as in:</span></span>
```csharp
var x = a.b?[0]?.c();
```
<span data-ttu-id="8624e-1522">e supponendo che il tipo della chiamata finale non è un tipo di valore non nullable, è equivalente al relativo significato:</span><span class="sxs-lookup"><span data-stu-id="8624e-1522">and assuming that the type of the final invocation is not a non-nullable value type, its meaning is equivalent to:</span></span>
```csharp
var x = (a.b == null) ? null : (a.b[0] == null) ? null : a.b[0].c();
```
<span data-ttu-id="8624e-1523">Con la differenza che `a.b` e `a.b[0]` vengono valutate una sola volta.</span><span class="sxs-lookup"><span data-stu-id="8624e-1523">except that `a.b` and `a.b[0]` are evaluated only once.</span></span>

#### <a name="null-conditional-expressions-as-projection-initializers"></a><span data-ttu-id="8624e-1524">Espressioni condizionali null come gli inizializzatori di proiezione</span><span class="sxs-lookup"><span data-stu-id="8624e-1524">Null-conditional expressions as projection initializers</span></span>

<span data-ttu-id="8624e-1525">Un'espressione condizionale null è consentita solo come un *member_declarator* in un *anonymous_object_creation_expression* ([espressioni per la creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions)) se termina con un accesso ai membri (facoltativamente condizionali null).</span><span class="sxs-lookup"><span data-stu-id="8624e-1525">A null-conditional expression is only allowed as a *member_declarator* in an *anonymous_object_creation_expression* ([Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions)) if it ends with an (optionally null-conditional) member access.</span></span> <span data-ttu-id="8624e-1526">Grammaticalmente, questo requisito può essere espresso come:</span><span class="sxs-lookup"><span data-stu-id="8624e-1526">Grammatically, this requirement can be expressed as:</span></span>

```antlr
null_conditional_member_access
    : primary_expression null_conditional_operations? '?' '.' identifier type_argument_list?
    | primary_expression null_conditional_operations '.' identifier type_argument_list?
    ;
```

<span data-ttu-id="8624e-1527">Questo è un caso speciale della grammatica per *null_conditional_expression* sopra.</span><span class="sxs-lookup"><span data-stu-id="8624e-1527">This is a special case of the grammar for *null_conditional_expression* above.</span></span> <span data-ttu-id="8624e-1528">L'ambiente di produzione per *member_declarator* nelle [espressioni per la creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions) quindi, include solo *null_conditional_member_access*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1528">The production for *member_declarator* in [Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions) then includes only *null_conditional_member_access*.</span></span>

#### <a name="null-conditional-expressions-as-statement-expressions"></a><span data-ttu-id="8624e-1529">Espressioni condizionali null come espressioni di istruzioni</span><span class="sxs-lookup"><span data-stu-id="8624e-1529">Null-conditional expressions as statement expressions</span></span>

<span data-ttu-id="8624e-1530">Un'espressione condizionale null è consentita solo come un *statement_expression* ([le istruzioni di espressione](statements.md#expression-statements)) se termina con una chiamata.</span><span class="sxs-lookup"><span data-stu-id="8624e-1530">A null-conditional expression is only allowed as a *statement_expression* ([Expression statements](statements.md#expression-statements)) if it ends with an invocation.</span></span> <span data-ttu-id="8624e-1531">Grammaticalmente, questo requisito può essere espresso come:</span><span class="sxs-lookup"><span data-stu-id="8624e-1531">Grammatically, this requirement can be expressed as:</span></span>

```antlr
null_conditional_invocation_expression
    : primary_expression null_conditional_operations '(' argument_list? ')'
    ;
```

<span data-ttu-id="8624e-1532">Questo è un caso speciale della grammatica per *null_conditional_expression* sopra.</span><span class="sxs-lookup"><span data-stu-id="8624e-1532">This is a special case of the grammar for *null_conditional_expression* above.</span></span> <span data-ttu-id="8624e-1533">L'ambiente di produzione per *statement_expression* nelle [le istruzioni di espressione](statements.md#expression-statements) quindi, include solo *null_conditional_invocation_expression*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1533">The production for *statement_expression* in [Expression statements](statements.md#expression-statements) then includes only *null_conditional_invocation_expression*.</span></span>


### <a name="unary-plus-operator"></a><span data-ttu-id="8624e-1534">Operatore più unario</span><span class="sxs-lookup"><span data-stu-id="8624e-1534">Unary plus operator</span></span>

<span data-ttu-id="8624e-1535">Per un'operazione del form `+x`, la risoluzione dell'overload di operatore unario ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico.</span><span class="sxs-lookup"><span data-stu-id="8624e-1535">For an operation of the form `+x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="8624e-1536">L'operando viene convertito nel tipo di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1536">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="8624e-1537">Operatori più unario predefinito sono:</span><span class="sxs-lookup"><span data-stu-id="8624e-1537">The predefined unary plus operators are:</span></span>

```csharp
int operator +(int x);
uint operator +(uint x);
long operator +(long x);
ulong operator +(ulong x);
float operator +(float x);
double operator +(double x);
decimal operator +(decimal x);
```

<span data-ttu-id="8624e-1538">Per ognuno di questi operatori, il risultato è semplicemente il valore dell'operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-1538">For each of these operators, the result is simply the value of the operand.</span></span>

### <a name="unary-minus-operator"></a><span data-ttu-id="8624e-1539">Operatore meno unario</span><span class="sxs-lookup"><span data-stu-id="8624e-1539">Unary minus operator</span></span>

<span data-ttu-id="8624e-1540">Per un'operazione del form `-x`, la risoluzione dell'overload di operatore unario ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico.</span><span class="sxs-lookup"><span data-stu-id="8624e-1540">For an operation of the form `-x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="8624e-1541">L'operando viene convertito nel tipo di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1541">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="8624e-1542">Gli operatori di negazione predefinite sono:</span><span class="sxs-lookup"><span data-stu-id="8624e-1542">The predefined negation operators are:</span></span>

*  <span data-ttu-id="8624e-1543">Negazione di integer:</span><span class="sxs-lookup"><span data-stu-id="8624e-1543">Integer negation:</span></span>

   ```csharp
   int operator -(int x);
   long operator -(long x);
   ```

   <span data-ttu-id="8624e-1544">Il risultato viene calcolato sottraendo `x` da zero.</span><span class="sxs-lookup"><span data-stu-id="8624e-1544">The result is computed by subtracting `x` from zero.</span></span> <span data-ttu-id="8624e-1545">Se il valore di `x` è il più piccolo valore rappresentabile del tipo di operando (-2 ^ 31 per `int` o -2 ^ 63 per `long`), quindi la negazione di matematica `x` non è possibile rappresentare all'interno del tipo di operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-1545">If the value of of `x` is the smallest representable value of the operand type (-2^31 for `int` or -2^63 for `long`), then the mathematical negation of `x` is not representable within the operand type.</span></span> <span data-ttu-id="8624e-1546">Se questo errore si verifica all'interno di un `checked` contesto, un `System.OverflowException` generata; se si verifica all'interno di un `unchecked` contesto, il risultato è il valore dell'operando e non viene segnalata l'overflow.</span><span class="sxs-lookup"><span data-stu-id="8624e-1546">If this occurs within a `checked` context, a `System.OverflowException` is thrown; if it occurs within an `unchecked` context, the result is the value of the operand and the overflow is not reported.</span></span>

   <span data-ttu-id="8624e-1547">Se l'operando dell'operatore di negazione è di tipo `uint`, viene convertito nel tipo `long`, e il tipo del risultato è `long`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1547">If the operand of the negation operator is of type `uint`, it is converted to type `long`, and the type of the result is `long`.</span></span> <span data-ttu-id="8624e-1548">Un'eccezione è la regola che consenta il `int` valore -2147483648 (-2 ^ 31) deve essere scritto come un valore letterale integer decimale ([i valori letterali Integer](lexical-structure.md#integer-literals)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1548">An exception is the rule that permits the `int` value -2147483648 (-2^31) to be written as a decimal integer literal ([Integer literals](lexical-structure.md#integer-literals)).</span></span>

   <span data-ttu-id="8624e-1549">Se l'operando dell'operatore di negazione è di tipo `ulong`, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1549">If the operand of the negation operator is of type `ulong`, a compile-time error occurs.</span></span> <span data-ttu-id="8624e-1550">Un'eccezione è la regola che consenta il `long` valore -9223372036854775808 (-2 ^ 63) deve essere scritto come un valore letterale integer decimale ([i valori letterali Integer](lexical-structure.md#integer-literals)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1550">An exception is the rule that permits the `long` value -9223372036854775808 (-2^63) to be written as a decimal integer literal ([Integer literals](lexical-structure.md#integer-literals)).</span></span>

*  <span data-ttu-id="8624e-1551">Negazione a virgola mobile:</span><span class="sxs-lookup"><span data-stu-id="8624e-1551">Floating-point negation:</span></span>

   ```csharp
   float operator -(float x);
   double operator -(double x);
   ```

   <span data-ttu-id="8624e-1552">Il risultato è il valore di `x` con segno opposto.</span><span class="sxs-lookup"><span data-stu-id="8624e-1552">The result is the value of `x` with its sign inverted.</span></span> <span data-ttu-id="8624e-1553">Se `x` è NaN, il risultato è NaN.</span><span class="sxs-lookup"><span data-stu-id="8624e-1553">If `x` is NaN, the result is also NaN.</span></span>

*  <span data-ttu-id="8624e-1554">Negazione decimale:</span><span class="sxs-lookup"><span data-stu-id="8624e-1554">Decimal negation:</span></span>

   ```csharp
   decimal operator -(decimal x);
   ```

   <span data-ttu-id="8624e-1555">Il risultato viene calcolato sottraendo `x` da zero.</span><span class="sxs-lookup"><span data-stu-id="8624e-1555">The result is computed by subtracting `x` from zero.</span></span> <span data-ttu-id="8624e-1556">Negazione decimale equivale a usare l'operatore di tipo unario `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1556">Decimal negation is equivalent to using the unary minus operator of type `System.Decimal`.</span></span>

### <a name="logical-negation-operator"></a><span data-ttu-id="8624e-1557">Operatore di negazione logica</span><span class="sxs-lookup"><span data-stu-id="8624e-1557">Logical negation operator</span></span>

<span data-ttu-id="8624e-1558">Per un'operazione del form `!x`, la risoluzione dell'overload di operatore unario ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico.</span><span class="sxs-lookup"><span data-stu-id="8624e-1558">For an operation of the form `!x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="8624e-1559">L'operando viene convertito nel tipo di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1559">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="8624e-1560">Esiste solo un operatore di negazione logica predefiniti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1560">Only one predefined logical negation operator exists:</span></span>
```csharp
bool operator !(bool x);
```

<span data-ttu-id="8624e-1561">Questo operatore calcola la negazione logica dell'operando: Se l'operando è `true`, il risultato è `false`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1561">This operator computes the logical negation of the operand: If the operand is `true`, the result is `false`.</span></span> <span data-ttu-id="8624e-1562">Se l'operando è `false`, il risultato è `true`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1562">If the operand is `false`, the result is `true`.</span></span>

### <a name="bitwise-complement-operator"></a><span data-ttu-id="8624e-1563">Operatore di complemento bit per bit</span><span class="sxs-lookup"><span data-stu-id="8624e-1563">Bitwise complement operator</span></span>

<span data-ttu-id="8624e-1564">Per un'operazione del form `~x`, la risoluzione dell'overload di operatore unario ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico.</span><span class="sxs-lookup"><span data-stu-id="8624e-1564">For an operation of the form `~x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="8624e-1565">L'operando viene convertito nel tipo di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1565">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="8624e-1566">Gli operatori di complemento bit per bit predefiniti sono:</span><span class="sxs-lookup"><span data-stu-id="8624e-1566">The predefined bitwise complement operators are:</span></span>
```csharp
int operator ~(int x);
uint operator ~(uint x);
long operator ~(long x);
ulong operator ~(ulong x);
```

<span data-ttu-id="8624e-1567">Per ognuno di questi operatori, il risultato dell'operazione è il complemento bit per bit di `x`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1567">For each of these operators, the result of the operation is the bitwise complement of `x`.</span></span>

<span data-ttu-id="8624e-1568">Ogni tipo di enumerazione `E` in modo implicito fornisce l'operatore di complemento bit per bit seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1568">Every enumeration type `E` implicitly provides the following bitwise complement operator:</span></span>

```csharp
E operator ~(E x);
```

<span data-ttu-id="8624e-1569">Il risultato della valutazione `~x`, dove `x` è un'espressione di un tipo di enumerazione `E` con un tipo sottostante `U`, è esattamente quello utilizzato per la valutazione `(E)(~(U)x)`, ad eccezione del fatto che la conversione a `E` è eseguita sempre come se si trova in un' `unchecked` contesto ([gli operatori checked e unchecked](expressions.md#the-checked-and-unchecked-operators)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1569">The result of evaluating `~x`, where `x` is an expression of an enumeration type `E` with an underlying type `U`, is exactly the same as evaluating `(E)(~(U)x)`, except that the conversion to `E` is always performed as if in an `unchecked` context ([The checked and unchecked operators](expressions.md#the-checked-and-unchecked-operators)).</span></span>

### <a name="prefix-increment-and-decrement-operators"></a><span data-ttu-id="8624e-1570">Incremento prefisso e decremento (operatori)</span><span class="sxs-lookup"><span data-stu-id="8624e-1570">Prefix increment and decrement operators</span></span>

```antlr
pre_increment_expression
    : '++' unary_expression
    ;

pre_decrement_expression
    : '--' unary_expression
    ;
```

<span data-ttu-id="8624e-1571">L'operando di un incremento prefisso o il decremento operazione deve essere un'espressione classificata come una variabile, un accesso a proprietà o un accesso all'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1571">The operand of a prefix increment or decrement operation must be an expression classified as a variable, a property access, or an indexer access.</span></span> <span data-ttu-id="8624e-1572">Il risultato dell'operazione è un valore dello stesso tipo dell'operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-1572">The result of the operation is a value of the same type as the operand.</span></span>

<span data-ttu-id="8624e-1573">Se l'operando di un prefisso di incrementa o operazione di decremento è una proprietà o l'accesso dell'indicizzatore, la proprietà o l'indicizzatore deve avere entrambe un `get` e un `set` della funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="8624e-1573">If the operand of a prefix increment or decrement operation is a property or indexer access, the property or indexer must have both a `get` and a `set` accessor.</span></span> <span data-ttu-id="8624e-1574">Se ciò non avviene, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1574">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="8624e-1575">Risoluzione dell'overload di operatore unario ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico.</span><span class="sxs-lookup"><span data-stu-id="8624e-1575">Unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="8624e-1576">Predefined `++` e `--` operatori esistono per i tipi seguenti: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char` , `float`, `double`, `decimal`e qualsiasi tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1576">Predefined `++` and `--` operators exist for the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, and any enum type.</span></span> <span data-ttu-id="8624e-1577">Oggetto predefinito `++` gli operatori restituiscono il valore ottenuto aggiungendo 1 all'operando e oggetto predefinito `--` operatori restituiscono il valore ottenuto sottraendo 1 operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-1577">The predefined `++` operators return the value produced by adding 1 to the operand, and the predefined `--` operators return the value produced by subtracting 1 from the operand.</span></span> <span data-ttu-id="8624e-1578">In un `checked` contesto, se il risultato dell'addizione o sottrazione è compreso nell'intervallo del tipo di risultato e il tipo di risultato è un tipo integrale o enumerazione, un `System.OverflowException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1578">In a `checked` context, if the result of this addition or subtraction is outside the range of the result type and the result type is an integral type or enum type, a `System.OverflowException` is thrown.</span></span>

<span data-ttu-id="8624e-1579">L'elaborazione in fase di esecuzione di un incremento prefisso o del modulo operazione di decremento `++x` o `--x` prevede i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1579">The run-time processing of a prefix increment or decrement operation of the form `++x` or `--x` consists of the following steps:</span></span>

*   <span data-ttu-id="8624e-1580">Se `x` viene classificato come una variabile:</span><span class="sxs-lookup"><span data-stu-id="8624e-1580">If `x` is classified as a variable:</span></span>
    * <span data-ttu-id="8624e-1581">`x` viene valutata per produrre la variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-1581">`x` is evaluated to produce the variable.</span></span>
    * <span data-ttu-id="8624e-1582">L'operatore selezionato viene richiamato con il valore di `x` come argomento.</span><span class="sxs-lookup"><span data-stu-id="8624e-1582">The selected operator is invoked with the value of `x` as its argument.</span></span>
    * <span data-ttu-id="8624e-1583">Il valore restituito dall'operatore viene archiviato nel percorso specificato dalla valutazione di `x`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1583">The value returned by the operator is stored in the location given by the evaluation of `x`.</span></span>
    * <span data-ttu-id="8624e-1584">Il valore restituito dall'operatore diventa il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1584">The value returned by the operator becomes the result of the operation.</span></span>
*   <span data-ttu-id="8624e-1585">Se `x` viene classificato come accesso alla proprietà o l'indicizzatore:</span><span class="sxs-lookup"><span data-stu-id="8624e-1585">If `x` is classified as a property or indexer access:</span></span>
    * <span data-ttu-id="8624e-1586">L'espressione dell'istanza (se `x` non è `static`) e l'elenco di argomenti (se `x` un accesso dell'indicizzatore) associato `x` vengono valutate, e i risultati vengono usati nella successiva `get` e `set` chiamate di funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="8624e-1586">The instance expression (if `x` is not `static`) and the argument list (if `x` is an indexer access) associated with `x` are evaluated, and the results are used in the subsequent `get` and `set` accessor invocations.</span></span>
    * <span data-ttu-id="8624e-1587">Il `get` funzione di accesso di `x` viene richiamato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1587">The `get` accessor of `x` is invoked.</span></span>
    * <span data-ttu-id="8624e-1588">L'operatore selezionato viene richiamato con il valore restituito dal `get` come argomento della funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="8624e-1588">The selected operator is invoked with the value returned by the `get` accessor as its argument.</span></span>
    * <span data-ttu-id="8624e-1589">Il `set` funzione di accesso di `x` viene richiamato con il valore restituito dall'operatore come relativo `value` argomento.</span><span class="sxs-lookup"><span data-stu-id="8624e-1589">The `set` accessor of `x` is invoked with the value returned by the operator as its `value` argument.</span></span>
    * <span data-ttu-id="8624e-1590">Il valore restituito dall'operatore diventa il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1590">The value returned by the operator becomes the result of the operation.</span></span>

<span data-ttu-id="8624e-1591">Il `++` e `--` operatori supportano anche in forma suffissa notazione ([forma suffissa di incremento e decremento (operatori)](expressions.md#postfix-increment-and-decrement-operators)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1591">The `++` and `--` operators also support postfix notation ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators)).</span></span> <span data-ttu-id="8624e-1592">In genere, il risultato del `x++` o `x--` è il valore di `x` prima dell'operazione, mentre il risultato del `++x` o `--x` è il valore di `x` al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1592">Typically, the result of `x++` or `x--` is the value of `x` before the operation, whereas the result of `++x` or `--x` is the value of `x` after the operation.</span></span> <span data-ttu-id="8624e-1593">In entrambi i casi `x` stesso ha lo stesso valore al termine dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1593">In either case, `x` itself has the same value after the operation.</span></span>

<span data-ttu-id="8624e-1594">Un' `operator++` o `operator--` implementazione può essere richiamata usando la notazione prefissa o suffissa.</span><span class="sxs-lookup"><span data-stu-id="8624e-1594">An `operator++` or `operator--` implementation can be invoked using either postfix or prefix notation.</span></span> <span data-ttu-id="8624e-1595">Non è possibile disporre di implementazioni di operatori separati per le due notazioni.</span><span class="sxs-lookup"><span data-stu-id="8624e-1595">It is not possible to have separate operator implementations for the two notations.</span></span>

### <a name="cast-expressions"></a><span data-ttu-id="8624e-1596">Espressioni cast</span><span class="sxs-lookup"><span data-stu-id="8624e-1596">Cast expressions</span></span>

<span data-ttu-id="8624e-1597">Oggetto *cast_expression* viene usata per convertire in modo esplicito un'espressione in un tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1597">A *cast_expression* is used to explicitly convert an expression to a given type.</span></span>

```antlr
cast_expression
    : '(' type ')' unary_expression
    ;
```

<span data-ttu-id="8624e-1598">Oggetto *cast_expression* del form `(T)E`, dove `T` è un *tipo* e `E` è una *unary_expression*, consente di eseguire un'esplicita conversione ([conversioni esplicite](conversions.md#explicit-conversions)) del valore di `E` al tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1598">A *cast_expression* of the form `(T)E`, where `T` is a *type* and `E` is a *unary_expression*, performs an explicit conversion ([Explicit conversions](conversions.md#explicit-conversions)) of the value of `E` to type `T`.</span></span> <span data-ttu-id="8624e-1599">Se non esiste alcuna conversione esplicita dal `E` a `T`, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1599">If no explicit conversion exists from `E` to `T`, a binding-time error occurs.</span></span> <span data-ttu-id="8624e-1600">In caso contrario, il risultato è il valore prodotto dalla conversione esplicita.</span><span class="sxs-lookup"><span data-stu-id="8624e-1600">Otherwise, the result is the value produced by the explicit conversion.</span></span> <span data-ttu-id="8624e-1601">Il risultato è sempre classificato come un valore, anche se `E` indica una variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-1601">The result is always classified as a value, even if `E` denotes a variable.</span></span>

<span data-ttu-id="8624e-1602">La grammatica per un *cast_expression* alcune ambiguità sintattica.</span><span class="sxs-lookup"><span data-stu-id="8624e-1602">The grammar for a *cast_expression* leads to certain syntactic ambiguities.</span></span> <span data-ttu-id="8624e-1603">Ad esempio, l'espressione `(x)-y` potrebbero essere interpretati come un *cast_expression* (un cast di `-y` digitare `x`) o come un *additive_expression* combinati con un *parenthesized_expression* (che calcola il valore `x - y)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1603">For example, the expression `(x)-y` could either be interpreted as a *cast_expression* (a cast of `-y` to type `x`) or as an *additive_expression* combined with a *parenthesized_expression* (which computes the value `x - y)`.</span></span>

<span data-ttu-id="8624e-1604">Per risolvere *cast_expression* ambiguità, esiste la regola seguente: Una sequenza di uno o più *token*s ([spazi vuoti](lexical-structure.md#white-space)) racchiuso tra parentesi viene considerato l'inizio di una *cast_expression* solo se almeno uno dei seguenti è vere:</span><span class="sxs-lookup"><span data-stu-id="8624e-1604">To resolve *cast_expression* ambiguities, the following rule exists: A sequence of one or more *token*s ([White space](lexical-structure.md#white-space)) enclosed in parentheses is considered the start of a *cast_expression* only if at least one of the following are true:</span></span>

*  <span data-ttu-id="8624e-1605">La sequenza di token è grammatica corretta per un *tipo*, ma non per un *espressione*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1605">The sequence of tokens is correct grammar for a *type*, but not for an *expression*.</span></span>
*  <span data-ttu-id="8624e-1606">La sequenza di token è grammatica corretta per un *tipo*, e il token immediatamente successivo alla parentesi di chiusura è il token "`~`", il token "`!`", il token "`(`", un  *Identificatore* ([sequenze di escape di caratteri Unicode](lexical-structure.md#unicode-character-escape-sequences)), una *letterale* ([valori letterali](lexical-structure.md#literals)), o qualsiasi *parola chiave*([Parole chiave](lexical-structure.md#keywords)), ad eccezione `as` e `is`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1606">The sequence of tokens is correct grammar for a *type*, and the token immediately following the closing parentheses is the token "`~`", the token "`!`", the token "`(`", an *identifier* ([Unicode character escape sequences](lexical-structure.md#unicode-character-escape-sequences)), a *literal* ([Literals](lexical-structure.md#literals)), or any *keyword* ([Keywords](lexical-structure.md#keywords)) except `as` and `is`.</span></span>

<span data-ttu-id="8624e-1607">Il termine "grammatica corretta" sopra indica solo che la sequenza di token deve essere conforme alle specifiche regole grammaticali.</span><span class="sxs-lookup"><span data-stu-id="8624e-1607">The term "correct grammar" above means only that the sequence of tokens must conform to the particular grammatical production.</span></span> <span data-ttu-id="8624e-1608">E in particolare non prende in considerazione il significato effettivo di tutti gli identificatori che lo costituiscono.</span><span class="sxs-lookup"><span data-stu-id="8624e-1608">It specifically does not consider the actual meaning of any constituent identifiers.</span></span> <span data-ttu-id="8624e-1609">Ad esempio, se `x` e `y` sono gli identificatori, quindi `x.y` grammatica corretta per un tipo, anche se `x.y` non indica un tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1609">For example, if `x` and `y` are identifiers, then `x.y` is correct grammar for a type, even if `x.y` doesn't actually denote a type.</span></span>

<span data-ttu-id="8624e-1610">Dalla regola di risoluzione dell'ambiguità segue che, se `x` e `y` sono identificatori `(x)y`, `(x)(y)`, e `(x)(-y)` sono *cast_expression*s, ma `(x)-y` non lo è, anche se `x` identifica un tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1610">From the disambiguation rule it follows that, if `x` and `y` are identifiers, `(x)y`, `(x)(y)`, and `(x)(-y)` are *cast_expression*s, but `(x)-y` is not, even if `x` identifies a type.</span></span> <span data-ttu-id="8624e-1611">Tuttavia, se `x` è una parola chiave che identifica un tipo predefinito (, ad esempio `int`), quindi tutti i quattro moduli vengono *cast_expression*s (perché tale parola chiave non può costituire un'espressione di per sé).</span><span class="sxs-lookup"><span data-stu-id="8624e-1611">However, if `x` is a keyword that identifies a predefined type (such as `int`), then all four forms are *cast_expression*s (because such a keyword could not possibly be an expression by itself).</span></span>

### <a name="await-expressions"></a><span data-ttu-id="8624e-1612">Espressioni Await</span><span class="sxs-lookup"><span data-stu-id="8624e-1612">Await expressions</span></span>

<span data-ttu-id="8624e-1613">Viene utilizzato l'operatore await per sospendere la valutazione della funzione async contenitore fino al completamento dell'operazione asincrona rappresentata dall'operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-1613">The await operator is used to suspend evaluation of the enclosing async function until the asynchronous operation represented by the operand has completed.</span></span>

```antlr
await_expression
    : 'await' unary_expression
    ;
```

<span data-ttu-id="8624e-1614">Un' *await_expression* è consentito solo nel corpo di una funzione asincrona ([iteratori](classes.md#iterators)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1614">An *await_expression* is only allowed in the body of an async function ([Iterators](classes.md#iterators)).</span></span> <span data-ttu-id="8624e-1615">All'interno di inclusione più vicina funzione async, un' *await_expression* potrebbe non verificarsi in queste posizioni:</span><span class="sxs-lookup"><span data-stu-id="8624e-1615">Within the nearest enclosing async function, an *await_expression* may not occur in these places:</span></span>

*  <span data-ttu-id="8624e-1616">All'interno di una funzione anonima annidati (non-async)</span><span class="sxs-lookup"><span data-stu-id="8624e-1616">Inside a nested (non-async) anonymous function</span></span>
*  <span data-ttu-id="8624e-1617">All'interno del blocco di un *lock_statement*</span><span class="sxs-lookup"><span data-stu-id="8624e-1617">Inside the block of a *lock_statement*</span></span>
*  <span data-ttu-id="8624e-1618">In un contesto unsafe</span><span class="sxs-lookup"><span data-stu-id="8624e-1618">In an unsafe context</span></span>

<span data-ttu-id="8624e-1619">Si noti che un *await_expression* non può verificarsi nella maggior parte delle posizioni all'interno di un *query_expression*, in quanto quelli sintatticamente vengono trasformati per usare le espressioni lambda non asincrone.</span><span class="sxs-lookup"><span data-stu-id="8624e-1619">Note that an *await_expression* cannot occur in most places within a *query_expression*, because those are syntactically transformed to use non-async lambda expressions.</span></span>

<span data-ttu-id="8624e-1620">All'interno di una funzione asincrona, `await` non può essere usato come identificatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1620">Inside of an async function, `await` cannot be used as an identifier.</span></span> <span data-ttu-id="8624e-1621">Di conseguenza non è disponibile alcuna ambiguità sintattica tra espressioni await e diverse espressioni che includono gli identificatori.</span><span class="sxs-lookup"><span data-stu-id="8624e-1621">There is therefore no syntactic ambiguity between await-expressions and various expressions involving identifiers.</span></span> <span data-ttu-id="8624e-1622">Di fuori di funzioni asincrone `await` funge da identificatore normale.</span><span class="sxs-lookup"><span data-stu-id="8624e-1622">Outside of async functions, `await` acts as a normal identifier.</span></span>

<span data-ttu-id="8624e-1623">L'operando di un' *await_expression* viene chiamato il ***attività***.</span><span class="sxs-lookup"><span data-stu-id="8624e-1623">The operand of an *await_expression* is called the ***task***.</span></span> <span data-ttu-id="8624e-1624">Rappresenta un'operazione asincrona che può o potrebbe non essere completo al momento il *await_expression* viene valutata.</span><span class="sxs-lookup"><span data-stu-id="8624e-1624">It represents an asynchronous operation that may or may not be complete at the time the *await_expression* is evaluated.</span></span> <span data-ttu-id="8624e-1625">Lo scopo dell'operatore await viene per sospendere l'esecuzione della funzione async contenitore fino al completamento dell'attività attesa e quindi ottenere il risultato.</span><span class="sxs-lookup"><span data-stu-id="8624e-1625">The purpose of the await operator is to suspend execution of the enclosing async function until the awaited task is complete, and then obtain its outcome.</span></span>

#### <a name="awaitable-expressions"></a><span data-ttu-id="8624e-1626">Espressioni awaitable</span><span class="sxs-lookup"><span data-stu-id="8624e-1626">Awaitable expressions</span></span>

<span data-ttu-id="8624e-1627">È necessaria l'attività di un'espressione await ***awaitable***.</span><span class="sxs-lookup"><span data-stu-id="8624e-1627">The task of an await expression is required to be ***awaitable***.</span></span> <span data-ttu-id="8624e-1628">Un'espressione `t` è di tipo awaitable se una delle seguenti condizioni:</span><span class="sxs-lookup"><span data-stu-id="8624e-1628">An expression `t` is awaitable if one of the following holds:</span></span>

*  <span data-ttu-id="8624e-1629">`t` è di tipo in fase di compilazione `dynamic`</span><span class="sxs-lookup"><span data-stu-id="8624e-1629">`t` is of compile time type `dynamic`</span></span>
*  <span data-ttu-id="8624e-1630">`t` dispone di un metodo di estensione o istanze accessibile chiamato `GetAwaiter` senza parametri e nessun parametro di tipo e un tipo restituito `A` per il quale tenere premuto tutti gli elementi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-1630">`t` has an accessible instance or extension method called `GetAwaiter` with no parameters and no type parameters, and a return type `A` for which all of the following hold:</span></span>
   * <span data-ttu-id="8624e-1631">`A` implementa l'interfaccia `System.Runtime.CompilerServices.INotifyCompletion` (d'ora in poi noto come `INotifyCompletion` per brevità)</span><span class="sxs-lookup"><span data-stu-id="8624e-1631">`A` implements the interface `System.Runtime.CompilerServices.INotifyCompletion` (hereafter known as `INotifyCompletion` for brevity)</span></span>
   * <span data-ttu-id="8624e-1632">`A` dispone di una proprietà di istanza accessibile e leggibile `IsCompleted` typu `bool`</span><span class="sxs-lookup"><span data-stu-id="8624e-1632">`A` has an accessible, readable instance property `IsCompleted` of type `bool`</span></span>
   * <span data-ttu-id="8624e-1633">`A` dispone di un metodo di istanza accessibile `GetResult` senza parametri e nessun parametro di tipo</span><span class="sxs-lookup"><span data-stu-id="8624e-1633">`A` has an accessible instance method `GetResult` with no parameters and no type parameters</span></span>

<span data-ttu-id="8624e-1634">Lo scopo del `GetAwaiter` metodo consiste nell'ottenere un ***awaiter*** per l'attività.</span><span class="sxs-lookup"><span data-stu-id="8624e-1634">The purpose of the `GetAwaiter` method is to obtain an ***awaiter*** for the task.</span></span> <span data-ttu-id="8624e-1635">Il tipo `A` viene chiamato il ***tipo di elemento awaiter*** per l'espressione await.</span><span class="sxs-lookup"><span data-stu-id="8624e-1635">The type `A` is called the ***awaiter type*** for the await expression.</span></span>

<span data-ttu-id="8624e-1636">Lo scopo del `IsCompleted` proprietà consiste nel determinare se l'attività è già completata.</span><span class="sxs-lookup"><span data-stu-id="8624e-1636">The purpose of the `IsCompleted` property is to determine if the task is already complete.</span></span> <span data-ttu-id="8624e-1637">In questo caso, non è necessario sospendere la valutazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1637">If so, there is no need to suspend evaluation.</span></span>

<span data-ttu-id="8624e-1638">Lo scopo del `INotifyCompletion.OnCompleted` metodo consiste nel sottoscrivere un "continuation" per l'attività; vale a dire un delegato (di tipo `System.Action`) che verrà richiamato una volta completata l'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1638">The purpose of the `INotifyCompletion.OnCompleted` method is to sign up a "continuation" to the task; i.e. a delegate (of type `System.Action`) that will be invoked once the task is complete.</span></span>

<span data-ttu-id="8624e-1639">Lo scopo del `GetResult` metodo consiste nell'ottenere il risultato dell'attività di completamento dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1639">The purpose of the `GetResult` method is to obtain the outcome of the task once it is complete.</span></span> <span data-ttu-id="8624e-1640">Questo risultato può essere esito positivo, possibilmente con un valore del risultato, oppure può essere un'eccezione che viene generata dal `GetResult` (metodo).</span><span class="sxs-lookup"><span data-stu-id="8624e-1640">This outcome may be successful completion, possibly with a result value, or it may be an exception which is thrown by the `GetResult` method.</span></span>

#### <a name="classification-of-await-expressions"></a><span data-ttu-id="8624e-1641">Classificazione di espressioni await</span><span class="sxs-lookup"><span data-stu-id="8624e-1641">Classification of await expressions</span></span>

<span data-ttu-id="8624e-1642">L'espressione `await t` viene classificato come espressione `(t).GetAwaiter().GetResult()`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1642">The expression `await t` is classified the same way as the expression `(t).GetAwaiter().GetResult()`.</span></span> <span data-ttu-id="8624e-1643">Di conseguenza, se il tipo restituito del `GetResult` viene `void`, il *await_expression* viene classificato come nulla.</span><span class="sxs-lookup"><span data-stu-id="8624e-1643">Thus, if the return type of `GetResult` is `void`, the *await_expression* is classified as nothing.</span></span> <span data-ttu-id="8624e-1644">Se dispone di un tipo restituito non void `T`, il *await_expression* viene classificato come un valore di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1644">If it has a non-void return type `T`, the *await_expression* is classified as a value of type `T`.</span></span>

#### <a name="runtime-evaluation-of-await-expressions"></a><span data-ttu-id="8624e-1645">Runtime di valutazione di espressioni await</span><span class="sxs-lookup"><span data-stu-id="8624e-1645">Runtime evaluation of await expressions</span></span>

<span data-ttu-id="8624e-1646">In fase di esecuzione, l'espressione `await t` viene valutata nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="8624e-1646">At runtime, the expression `await t` is evaluated as follows:</span></span>

*  <span data-ttu-id="8624e-1647">Un elemento awaiter `a` viene ottenuto dalla valutazione dell'espressione `(t).GetAwaiter()`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1647">An awaiter `a` is obtained by evaluating the expression `(t).GetAwaiter()`.</span></span>
*  <span data-ttu-id="8624e-1648">Oggetto `bool` `b` viene ottenuto dalla valutazione dell'espressione `(a).IsCompleted`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1648">A `bool` `b` is obtained by evaluating the expression `(a).IsCompleted`.</span></span>
*  <span data-ttu-id="8624e-1649">Se `b` viene `false` quindi valutazione dipende dal fatto che `a` implementa l'interfaccia `System.Runtime.CompilerServices.ICriticalNotifyCompletion` (d'ora in poi noto come `ICriticalNotifyCompletion` per brevità).</span><span class="sxs-lookup"><span data-stu-id="8624e-1649">If `b` is `false` then evaluation depends on whether `a` implements the interface `System.Runtime.CompilerServices.ICriticalNotifyCompletion` (hereafter known as `ICriticalNotifyCompletion` for brevity).</span></span> <span data-ttu-id="8624e-1650">Questa verifica viene eseguita in fase; di associazione ad esempio in fase di esecuzione se `a` ha il tipo in fase di compilazione `dynamic`e in fase di compilazione in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="8624e-1650">This check is done at binding time; i.e. at runtime if `a` has the compile time type `dynamic`, and at compile time otherwise.</span></span> <span data-ttu-id="8624e-1651">Let `r` indicano il delegato di ripresa ([iteratori](classes.md#iterators)):</span><span class="sxs-lookup"><span data-stu-id="8624e-1651">Let `r` denote the resumption delegate ([Iterators](classes.md#iterators)):</span></span>
    * <span data-ttu-id="8624e-1652">Se `a` neimplementuje metodu `ICriticalNotifyCompletion`, quindi l'espressione `(a as (INotifyCompletion)).OnCompleted(r)` viene valutata.</span><span class="sxs-lookup"><span data-stu-id="8624e-1652">If `a` does not implement `ICriticalNotifyCompletion`, then the expression `(a as (INotifyCompletion)).OnCompleted(r)` is evaluated.</span></span>
    * <span data-ttu-id="8624e-1653">Se `a` implementare `ICriticalNotifyCompletion`, quindi l'espressione `(a as (ICriticalNotifyCompletion)).UnsafeOnCompleted(r)` viene valutata.</span><span class="sxs-lookup"><span data-stu-id="8624e-1653">If `a` does implement `ICriticalNotifyCompletion`, then the expression `(a as (ICriticalNotifyCompletion)).UnsafeOnCompleted(r)` is evaluated.</span></span>
    * <span data-ttu-id="8624e-1654">La valutazione viene quindi sospesa e il controllo venga restituito al chiamante della funzione asincrona corrente.</span><span class="sxs-lookup"><span data-stu-id="8624e-1654">Evaluation is then suspended, and control is returned to the current caller of the async function.</span></span>
*  <span data-ttu-id="8624e-1655">Entrambi immediatamente dopo (se `b` è stata `true`), o al momento della successiva chiamata del delegato di ripresa (se `b` era `false`), l'espressione `(a).GetResult()` viene valutata.</span><span class="sxs-lookup"><span data-stu-id="8624e-1655">Either immediately after (if `b` was `true`), or upon later invocation of the resumption delegate (if `b` was `false`), the expression `(a).GetResult()` is evaluated.</span></span> <span data-ttu-id="8624e-1656">Se viene restituito un valore, tale valore è il risultato del *await_expression*.</span><span class="sxs-lookup"><span data-stu-id="8624e-1656">If it returns a value, that value is the result of the *await_expression*.</span></span> <span data-ttu-id="8624e-1657">In caso contrario, il risultato è nothing.</span><span class="sxs-lookup"><span data-stu-id="8624e-1657">Otherwise the result is nothing.</span></span>

<span data-ttu-id="8624e-1658">Implementazione di awaiter dei metodi dell'interfaccia `INotifyCompletion.OnCompleted` e `ICriticalNotifyCompletion.UnsafeOnCompleted` dovrebbero provocare il delegato `r` da richiamare al massimo una volta.</span><span class="sxs-lookup"><span data-stu-id="8624e-1658">An awaiter's implementation of the interface methods `INotifyCompletion.OnCompleted` and `ICriticalNotifyCompletion.UnsafeOnCompleted` should cause the delegate `r` to be invoked at most once.</span></span> <span data-ttu-id="8624e-1659">In caso contrario, il comportamento della funzione contenitore async è definito.</span><span class="sxs-lookup"><span data-stu-id="8624e-1659">Otherwise, the behavior of the enclosing async function is undefined.</span></span>

## <a name="arithmetic-operators"></a><span data-ttu-id="8624e-1660">Operatori aritmetici</span><span class="sxs-lookup"><span data-stu-id="8624e-1660">Arithmetic operators</span></span>

<span data-ttu-id="8624e-1661">Il `*`, `/`, `%`, `+`, e `-` gli operatori sono denominati operatori aritmetici.</span><span class="sxs-lookup"><span data-stu-id="8624e-1661">The `*`, `/`, `%`, `+`, and `-` operators are called the arithmetic operators.</span></span>

```antlr
multiplicative_expression
    : unary_expression
    | multiplicative_expression '*' unary_expression
    | multiplicative_expression '/' unary_expression
    | multiplicative_expression '%' unary_expression
    ;

additive_expression
    : multiplicative_expression
    | additive_expression '+' multiplicative_expression
    | additive_expression '-' multiplicative_expression
    ;
```

<span data-ttu-id="8624e-1662">Se un operando di un operatore aritmetico ha il tipo di fase di compilazione `dynamic`, quindi in modo dinamico è associata l'espressione ([binding dinamico](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="8624e-1662">If an operand of an arithmetic operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="8624e-1663">In questo caso è il tipo di fase di compilazione dell'espressione `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di fase di esecuzione di tali operandi che presentano il tipo di fase di compilazione `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1663">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

### <a name="multiplication-operator"></a><span data-ttu-id="8624e-1664">Operatore di moltiplicazione</span><span class="sxs-lookup"><span data-stu-id="8624e-1664">Multiplication operator</span></span>

<span data-ttu-id="8624e-1665">Per un'operazione del form `x * y`, la risoluzione dell'overload di operatore binario ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico.</span><span class="sxs-lookup"><span data-stu-id="8624e-1665">For an operation of the form `x * y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="8624e-1666">Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1666">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="8624e-1667">Di seguito sono elencati gli operatori di moltiplicazione predefiniti.</span><span class="sxs-lookup"><span data-stu-id="8624e-1667">The predefined multiplication operators are listed below.</span></span> <span data-ttu-id="8624e-1668">Tutti gli operatori calcolano il prodotto dei `x` e `y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1668">The operators all compute the product of `x` and `y`.</span></span>

*  <span data-ttu-id="8624e-1669">Moltiplicazione integer:</span><span class="sxs-lookup"><span data-stu-id="8624e-1669">Integer multiplication:</span></span>

   ```csharp
   int operator *(int x, int y);
   uint operator *(uint x, uint y);
   long operator *(long x, long y);
   ulong operator *(ulong x, ulong y);
   ```

   <span data-ttu-id="8624e-1670">In un `checked` contesto, se il prodotto è compreso nell'intervallo del tipo di risultato, un `System.OverflowException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1670">In a `checked` context, if the product is outside the range of the result type, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="8624e-1671">In un `unchecked` contesto, eventuali overflow non vengono segnalati e qualsiasi bit più significativi significativo di fuori dell'intervallo del tipo di risultato vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="8624e-1671">In an `unchecked` context, overflows are not reported and any significant high-order bits outside the range of the result type are discarded.</span></span>


*  <span data-ttu-id="8624e-1672">Moltiplicazione a virgola mobile:</span><span class="sxs-lookup"><span data-stu-id="8624e-1672">Floating-point multiplication:</span></span>

   ```csharp
   float operator *(float x, float y);
   double operator *(double x, double y);
   ```

   <span data-ttu-id="8624e-1673">Il prodotto viene calcolato in base alle regole aritmetiche IEEE 754.</span><span class="sxs-lookup"><span data-stu-id="8624e-1673">The product is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="8624e-1674">Nella tabella seguente sono elencati i risultati di tutte le possibili combinazioni di valori finiti diverso da zero, gli zeri, valori infiniti e NaN.</span><span class="sxs-lookup"><span data-stu-id="8624e-1674">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="8624e-1675">Nella tabella `x` e `y` sono valori positivi finiti.</span><span class="sxs-lookup"><span data-stu-id="8624e-1675">In the table, `x` and `y` are positive finite values.</span></span> <span data-ttu-id="8624e-1676">`z` è il risultato di `x * y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1676">`z` is the result of `x * y`.</span></span> <span data-ttu-id="8624e-1677">Se il risultato è troppo grande per il tipo di destinazione `z` è infinito.</span><span class="sxs-lookup"><span data-stu-id="8624e-1677">If the result is too large for the destination type, `z` is infinity.</span></span> <span data-ttu-id="8624e-1678">Se il risultato è troppo piccolo per il tipo di destinazione `z` è uguale a zero.</span><span class="sxs-lookup"><span data-stu-id="8624e-1678">If the result is too small for the destination type, `z` is zero.</span></span>

   |      |      |      |     |     |      |      |     |
   |:----:|-----:|:----:|:---:|:---:|:----:|:----:|:----|
   |      | <span data-ttu-id="8624e-1679">+ y</span><span class="sxs-lookup"><span data-stu-id="8624e-1679">+y</span></span>   | <span data-ttu-id="8624e-1680">-y</span><span class="sxs-lookup"><span data-stu-id="8624e-1680">-y</span></span>   | <span data-ttu-id="8624e-1681">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1681">+0</span></span>  | <span data-ttu-id="8624e-1682">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1682">-0</span></span>  | <span data-ttu-id="8624e-1683">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1683">+inf</span></span> | <span data-ttu-id="8624e-1684">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1684">-inf</span></span> | <span data-ttu-id="8624e-1685">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1685">NaN</span></span> | 
   | <span data-ttu-id="8624e-1686">+x</span><span class="sxs-lookup"><span data-stu-id="8624e-1686">+x</span></span>   | <span data-ttu-id="8624e-1687">+z</span><span class="sxs-lookup"><span data-stu-id="8624e-1687">+z</span></span>   | <span data-ttu-id="8624e-1688">-z</span><span class="sxs-lookup"><span data-stu-id="8624e-1688">-z</span></span>   | <span data-ttu-id="8624e-1689">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1689">+0</span></span>  | <span data-ttu-id="8624e-1690">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1690">-0</span></span>  | <span data-ttu-id="8624e-1691">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1691">+inf</span></span> | <span data-ttu-id="8624e-1692">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1692">-inf</span></span> | <span data-ttu-id="8624e-1693">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1693">NaN</span></span> | 
   | <span data-ttu-id="8624e-1694">-x</span><span class="sxs-lookup"><span data-stu-id="8624e-1694">-x</span></span>   | <span data-ttu-id="8624e-1695">-z</span><span class="sxs-lookup"><span data-stu-id="8624e-1695">-z</span></span>   | <span data-ttu-id="8624e-1696">+z</span><span class="sxs-lookup"><span data-stu-id="8624e-1696">+z</span></span>   | <span data-ttu-id="8624e-1697">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1697">-0</span></span>  | <span data-ttu-id="8624e-1698">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1698">+0</span></span>  | <span data-ttu-id="8624e-1699">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1699">-inf</span></span> | <span data-ttu-id="8624e-1700">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1700">+inf</span></span> | <span data-ttu-id="8624e-1701">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1701">NaN</span></span> | 
   | <span data-ttu-id="8624e-1702">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1702">+0</span></span>   | <span data-ttu-id="8624e-1703">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1703">+0</span></span>   | <span data-ttu-id="8624e-1704">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1704">-0</span></span>   | <span data-ttu-id="8624e-1705">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1705">+0</span></span>  | <span data-ttu-id="8624e-1706">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1706">-0</span></span>  | <span data-ttu-id="8624e-1707">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1707">NaN</span></span>  | <span data-ttu-id="8624e-1708">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1708">NaN</span></span>  | <span data-ttu-id="8624e-1709">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1709">NaN</span></span> | 
   | <span data-ttu-id="8624e-1710">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1710">-0</span></span>   | <span data-ttu-id="8624e-1711">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1711">-0</span></span>   | <span data-ttu-id="8624e-1712">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1712">+0</span></span>   | <span data-ttu-id="8624e-1713">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1713">-0</span></span>  | <span data-ttu-id="8624e-1714">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1714">+0</span></span>  | <span data-ttu-id="8624e-1715">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1715">NaN</span></span>  | <span data-ttu-id="8624e-1716">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1716">NaN</span></span>  | <span data-ttu-id="8624e-1717">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1717">NaN</span></span> | 
   | <span data-ttu-id="8624e-1718">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1718">+inf</span></span> | <span data-ttu-id="8624e-1719">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1719">+inf</span></span> | <span data-ttu-id="8624e-1720">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1720">-inf</span></span> | <span data-ttu-id="8624e-1721">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1721">NaN</span></span> | <span data-ttu-id="8624e-1722">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1722">NaN</span></span> | <span data-ttu-id="8624e-1723">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1723">+inf</span></span> | <span data-ttu-id="8624e-1724">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1724">-inf</span></span> | <span data-ttu-id="8624e-1725">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1725">NaN</span></span> | 
   | <span data-ttu-id="8624e-1726">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1726">-inf</span></span> | <span data-ttu-id="8624e-1727">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1727">-inf</span></span> | <span data-ttu-id="8624e-1728">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1728">+inf</span></span> | <span data-ttu-id="8624e-1729">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1729">NaN</span></span> | <span data-ttu-id="8624e-1730">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1730">NaN</span></span> | <span data-ttu-id="8624e-1731">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1731">-inf</span></span> | <span data-ttu-id="8624e-1732">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1732">+inf</span></span> | <span data-ttu-id="8624e-1733">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1733">NaN</span></span> | 
   | <span data-ttu-id="8624e-1734">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1734">NaN</span></span>  | <span data-ttu-id="8624e-1735">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1735">NaN</span></span>  | <span data-ttu-id="8624e-1736">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1736">NaN</span></span>  | <span data-ttu-id="8624e-1737">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1737">NaN</span></span> | <span data-ttu-id="8624e-1738">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1738">NaN</span></span> | <span data-ttu-id="8624e-1739">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1739">NaN</span></span>  | <span data-ttu-id="8624e-1740">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1740">NaN</span></span>  | <span data-ttu-id="8624e-1741">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1741">NaN</span></span> | 

*  <span data-ttu-id="8624e-1742">Moltiplicazione decimale:</span><span class="sxs-lookup"><span data-stu-id="8624e-1742">Decimal multiplication:</span></span>

   ```csharp
   decimal operator *(decimal x, decimal y);
   ```

   <span data-ttu-id="8624e-1743">Se il valore risultante è troppo grande per essere rappresentato nel `decimal` formato, un `System.OverflowException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1743">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="8624e-1744">Se il valore del risultato è troppo piccolo per essere rappresentato nel `decimal` formato, il risultato è uguale a zero.</span><span class="sxs-lookup"><span data-stu-id="8624e-1744">If the result value is too small to represent in the `decimal` format, the result is zero.</span></span> <span data-ttu-id="8624e-1745">La scala del risultato, prima di qualsiasi arrotondamento, è la somma delle scale dei due operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-1745">The scale of the result, before any rounding, is the sum of the scales of the two operands.</span></span>

   <span data-ttu-id="8624e-1746">Moltiplicazione decimale equivale all'uso dell'operatore di moltiplicazione di tipo `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1746">Decimal multiplication is equivalent to using the multiplication operator of type `System.Decimal`.</span></span>


### <a name="division-operator"></a><span data-ttu-id="8624e-1747">Operatore di divisione</span><span class="sxs-lookup"><span data-stu-id="8624e-1747">Division operator</span></span>

<span data-ttu-id="8624e-1748">Per un'operazione del form `x / y`, la risoluzione dell'overload di operatore binario ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico.</span><span class="sxs-lookup"><span data-stu-id="8624e-1748">For an operation of the form `x / y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="8624e-1749">Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1749">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="8624e-1750">Gli operatori di divisione predefiniti vengono elencati di seguito.</span><span class="sxs-lookup"><span data-stu-id="8624e-1750">The predefined division operators are listed below.</span></span> <span data-ttu-id="8624e-1751">Tutti gli operatori di calcolo il quoziente degli `x` e `y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1751">The operators all compute the quotient of `x` and `y`.</span></span>

*  <span data-ttu-id="8624e-1752">Divisione di interi:</span><span class="sxs-lookup"><span data-stu-id="8624e-1752">Integer division:</span></span>

   ```csharp
   int operator /(int x, int y);
   uint operator /(uint x, uint y);
   long operator /(long x, long y);
   ulong operator /(ulong x, ulong y);
   ```

   <span data-ttu-id="8624e-1753">Se il valore dell'operando destro è zero, una `System.DivideByZeroException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1753">If the value of the right operand is zero, a `System.DivideByZeroException` is thrown.</span></span>

   <span data-ttu-id="8624e-1754">La divisione Arrotonda il risultato verso lo zero.</span><span class="sxs-lookup"><span data-stu-id="8624e-1754">The division rounds the result towards zero.</span></span> <span data-ttu-id="8624e-1755">In questo modo il valore assoluto del risultato è l'integer più grande è minore o uguale al valore assoluto del quoziente di due operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-1755">Thus the absolute value of the result is the largest possible integer that is less than or equal to the absolute value of the quotient of the two operands.</span></span> <span data-ttu-id="8624e-1756">Il risultato è zero o positivo quando i due operandi hanno lo stesso segno e zero o negativo quando dispone di due operandi segno opposto.</span><span class="sxs-lookup"><span data-stu-id="8624e-1756">The result is zero or positive when the two operands have the same sign and zero or negative when the two operands have opposite signs.</span></span>

   <span data-ttu-id="8624e-1757">Se l'operando sinistro è il più piccolo che è possibile rappresentare `int` oppure `long` valore e l'operando destro è `-1`, si verifica un overflow.</span><span class="sxs-lookup"><span data-stu-id="8624e-1757">If the left operand is the smallest representable `int` or `long` value and the right operand is `-1`, an overflow occurs.</span></span> <span data-ttu-id="8624e-1758">In un `checked` contesto, in questo modo, un `System.ArithmeticException` (o una sottoclasse relativa) viene generata.</span><span class="sxs-lookup"><span data-stu-id="8624e-1758">In a `checked` context, this causes a `System.ArithmeticException` (or a subclass thereof) to be thrown.</span></span> <span data-ttu-id="8624e-1759">In un' `unchecked` contesto, è definito dall'implementazione se un `System.ArithmeticException` (o una sottoclasse relativa) viene generata un'eccezione o l'overflow viene segnalato con il valore dell'operando di sinistra.</span><span class="sxs-lookup"><span data-stu-id="8624e-1759">In an `unchecked` context, it is implementation-defined as to whether a `System.ArithmeticException` (or a subclass thereof) is thrown or the overflow goes unreported with the resulting value being that of the left operand.</span></span>

*  <span data-ttu-id="8624e-1760">Divisione a virgola mobile:</span><span class="sxs-lookup"><span data-stu-id="8624e-1760">Floating-point division:</span></span>

   ```csharp
   float operator /(float x, float y);
   double operator /(double x, double y);
   ```

   <span data-ttu-id="8624e-1761">Il quoziente viene calcolato in base alle regole aritmetiche IEEE 754.</span><span class="sxs-lookup"><span data-stu-id="8624e-1761">The quotient is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="8624e-1762">Nella tabella seguente sono elencati i risultati di tutte le possibili combinazioni di valori finiti diverso da zero, gli zeri, valori infiniti e NaN.</span><span class="sxs-lookup"><span data-stu-id="8624e-1762">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="8624e-1763">Nella tabella `x` e `y` sono valori positivi finiti.</span><span class="sxs-lookup"><span data-stu-id="8624e-1763">In the table, `x` and `y` are positive finite values.</span></span> <span data-ttu-id="8624e-1764">`z` è il risultato di `x / y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1764">`z` is the result of `x / y`.</span></span> <span data-ttu-id="8624e-1765">Se il risultato è troppo grande per il tipo di destinazione `z` è infinito.</span><span class="sxs-lookup"><span data-stu-id="8624e-1765">If the result is too large for the destination type, `z` is infinity.</span></span> <span data-ttu-id="8624e-1766">Se il risultato è troppo piccolo per il tipo di destinazione `z` è uguale a zero.</span><span class="sxs-lookup"><span data-stu-id="8624e-1766">If the result is too small for the destination type, `z` is zero.</span></span>

   |      |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | <span data-ttu-id="8624e-1767">+ y</span><span class="sxs-lookup"><span data-stu-id="8624e-1767">+y</span></span>   | <span data-ttu-id="8624e-1768">-y</span><span class="sxs-lookup"><span data-stu-id="8624e-1768">-y</span></span>   | <span data-ttu-id="8624e-1769">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1769">+0</span></span>   | <span data-ttu-id="8624e-1770">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1770">-0</span></span>   | <span data-ttu-id="8624e-1771">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1771">+inf</span></span> | <span data-ttu-id="8624e-1772">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1772">-inf</span></span> | <span data-ttu-id="8624e-1773">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1773">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1774">+x</span><span class="sxs-lookup"><span data-stu-id="8624e-1774">+x</span></span>   | <span data-ttu-id="8624e-1775">+z</span><span class="sxs-lookup"><span data-stu-id="8624e-1775">+z</span></span>   | <span data-ttu-id="8624e-1776">-z</span><span class="sxs-lookup"><span data-stu-id="8624e-1776">-z</span></span>   | <span data-ttu-id="8624e-1777">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1777">+inf</span></span> | <span data-ttu-id="8624e-1778">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1778">-inf</span></span> | <span data-ttu-id="8624e-1779">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1779">+0</span></span>   | <span data-ttu-id="8624e-1780">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1780">-0</span></span>   | <span data-ttu-id="8624e-1781">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1781">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1782">-x</span><span class="sxs-lookup"><span data-stu-id="8624e-1782">-x</span></span>   | <span data-ttu-id="8624e-1783">-z</span><span class="sxs-lookup"><span data-stu-id="8624e-1783">-z</span></span>   | <span data-ttu-id="8624e-1784">+z</span><span class="sxs-lookup"><span data-stu-id="8624e-1784">+z</span></span>   | <span data-ttu-id="8624e-1785">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1785">-inf</span></span> | <span data-ttu-id="8624e-1786">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1786">+inf</span></span> | <span data-ttu-id="8624e-1787">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1787">-0</span></span>   | <span data-ttu-id="8624e-1788">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1788">+0</span></span>   | <span data-ttu-id="8624e-1789">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1789">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1790">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1790">+0</span></span>   | <span data-ttu-id="8624e-1791">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1791">+0</span></span>   | <span data-ttu-id="8624e-1792">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1792">-0</span></span>   | <span data-ttu-id="8624e-1793">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1793">NaN</span></span>  | <span data-ttu-id="8624e-1794">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1794">NaN</span></span>  | <span data-ttu-id="8624e-1795">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1795">+0</span></span>   | <span data-ttu-id="8624e-1796">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1796">-0</span></span>   | <span data-ttu-id="8624e-1797">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1797">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1798">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1798">-0</span></span>   | <span data-ttu-id="8624e-1799">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1799">-0</span></span>   | <span data-ttu-id="8624e-1800">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1800">+0</span></span>   | <span data-ttu-id="8624e-1801">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1801">NaN</span></span>  | <span data-ttu-id="8624e-1802">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1802">NaN</span></span>  | <span data-ttu-id="8624e-1803">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1803">-0</span></span>   | <span data-ttu-id="8624e-1804">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1804">+0</span></span>   | <span data-ttu-id="8624e-1805">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1805">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1806">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1806">+inf</span></span> | <span data-ttu-id="8624e-1807">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1807">+inf</span></span> | <span data-ttu-id="8624e-1808">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1808">-inf</span></span> | <span data-ttu-id="8624e-1809">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1809">+inf</span></span> | <span data-ttu-id="8624e-1810">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1810">-inf</span></span> | <span data-ttu-id="8624e-1811">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1811">NaN</span></span>  | <span data-ttu-id="8624e-1812">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1812">NaN</span></span>  | <span data-ttu-id="8624e-1813">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1813">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1814">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1814">-inf</span></span> | <span data-ttu-id="8624e-1815">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1815">-inf</span></span> | <span data-ttu-id="8624e-1816">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1816">+inf</span></span> | <span data-ttu-id="8624e-1817">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1817">-inf</span></span> | <span data-ttu-id="8624e-1818">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1818">+inf</span></span> | <span data-ttu-id="8624e-1819">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1819">NaN</span></span>  | <span data-ttu-id="8624e-1820">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1820">NaN</span></span>  | <span data-ttu-id="8624e-1821">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1821">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1822">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1822">NaN</span></span>  | <span data-ttu-id="8624e-1823">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1823">NaN</span></span>  | <span data-ttu-id="8624e-1824">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1824">NaN</span></span>  | <span data-ttu-id="8624e-1825">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1825">NaN</span></span>  | <span data-ttu-id="8624e-1826">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1826">NaN</span></span>  | <span data-ttu-id="8624e-1827">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1827">NaN</span></span>  | <span data-ttu-id="8624e-1828">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1828">NaN</span></span>  | <span data-ttu-id="8624e-1829">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1829">NaN</span></span>  | 

*  <span data-ttu-id="8624e-1830">Divisione decimale:</span><span class="sxs-lookup"><span data-stu-id="8624e-1830">Decimal division:</span></span>

   ```csharp
   decimal operator /(decimal x, decimal y);
   ```

   <span data-ttu-id="8624e-1831">Se il valore dell'operando destro è zero, una `System.DivideByZeroException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1831">If the value of the right operand is zero, a `System.DivideByZeroException` is thrown.</span></span> <span data-ttu-id="8624e-1832">Se il valore risultante è troppo grande per essere rappresentato nel `decimal` formato, un `System.OverflowException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1832">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="8624e-1833">Se il valore del risultato è troppo piccolo per essere rappresentato nel `decimal` formato, il risultato è uguale a zero.</span><span class="sxs-lookup"><span data-stu-id="8624e-1833">If the result value is too small to represent in the `decimal` format, the result is zero.</span></span> <span data-ttu-id="8624e-1834">La scala del risultato è la più piccola scala in grado di mantenere un risultato uguale al più vicino al valore decimale rappresentabile per il risultato matematico true.</span><span class="sxs-lookup"><span data-stu-id="8624e-1834">The scale of the result is the smallest scale that will preserve a result equal to the nearest representable decimal value to the true mathematical result.</span></span>

   <span data-ttu-id="8624e-1835">Divisione di decimali equivale a usare l'operatore di divisione del tipo `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1835">Decimal division is equivalent to using the division operator of type `System.Decimal`.</span></span>


### <a name="remainder-operator"></a><span data-ttu-id="8624e-1836">Operatore di resto</span><span class="sxs-lookup"><span data-stu-id="8624e-1836">Remainder operator</span></span>

<span data-ttu-id="8624e-1837">Per un'operazione del form `x % y`, la risoluzione dell'overload di operatore binario ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico.</span><span class="sxs-lookup"><span data-stu-id="8624e-1837">For an operation of the form `x % y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="8624e-1838">Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1838">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="8624e-1839">Di seguito sono elencati gli operatori di resto predefiniti.</span><span class="sxs-lookup"><span data-stu-id="8624e-1839">The predefined remainder operators are listed below.</span></span> <span data-ttu-id="8624e-1840">Tutti gli operatori di calcolo il resto della divisione tra `x` e `y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1840">The operators all compute the remainder of the division between `x` and `y`.</span></span>

*  <span data-ttu-id="8624e-1841">Resto intero:</span><span class="sxs-lookup"><span data-stu-id="8624e-1841">Integer remainder:</span></span>

   ```csharp
   int operator %(int x, int y);
   uint operator %(uint x, uint y);
   long operator %(long x, long y);
   ulong operator %(ulong x, ulong y);
   ```

   <span data-ttu-id="8624e-1842">Il risultato del `x % y` è il valore prodotto da `x - (x / y) * y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1842">The result of `x % y` is the value produced by `x - (x / y) * y`.</span></span> <span data-ttu-id="8624e-1843">Se `y` è uguale a zero, una `System.DivideByZeroException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1843">If `y` is zero, a `System.DivideByZeroException` is thrown.</span></span>

   <span data-ttu-id="8624e-1844">Se l'operando sinistro è il più piccolo `int` oppure `long` valore e l'operando destro è `-1`, un `System.OverflowException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1844">If the left operand is the smallest `int` or `long` value and the right operand is `-1`, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="8624e-1845">In nessun caso esegue `x % y` generano un'eccezione in cui `x / y` non genererebbe un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1845">In no case does `x % y` throw an exception where `x / y` would not throw an exception.</span></span>

*  <span data-ttu-id="8624e-1846">Resto a virgola mobile:</span><span class="sxs-lookup"><span data-stu-id="8624e-1846">Floating-point remainder:</span></span>

   ```csharp
   float operator %(float x, float y);
   double operator %(double x, double y);
   ```

   <span data-ttu-id="8624e-1847">Nella tabella seguente sono elencati i risultati di tutte le possibili combinazioni di valori finiti diverso da zero, gli zeri, valori infiniti e NaN.</span><span class="sxs-lookup"><span data-stu-id="8624e-1847">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="8624e-1848">Nella tabella `x` e `y` sono valori positivi finiti.</span><span class="sxs-lookup"><span data-stu-id="8624e-1848">In the table, `x` and `y` are positive finite values.</span></span> <span data-ttu-id="8624e-1849">`z` è il risultato del `x % y` e viene calcolata come `x - n * y`, dove `n` è l'integer più grande è minore o uguale a `x / y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1849">`z` is the result of `x % y` and is computed as `x - n * y`, where `n` is the largest possible integer that is less than or equal to `x / y`.</span></span> <span data-ttu-id="8624e-1850">Questo metodo di calcolo è analogo a quella utilizzata per gli operandi di numero intero, ma è diversa dalla definizione del valore IEEE 754 (in cui `n` è il numero intero più vicino `x / y`).</span><span class="sxs-lookup"><span data-stu-id="8624e-1850">This method of computing the remainder is analogous to that used for integer operands, but differs from the IEEE 754 definition (in which `n` is the integer closest to `x / y`).</span></span>

   |      |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | <span data-ttu-id="8624e-1851">+ y</span><span class="sxs-lookup"><span data-stu-id="8624e-1851">+y</span></span>   | <span data-ttu-id="8624e-1852">-y</span><span class="sxs-lookup"><span data-stu-id="8624e-1852">-y</span></span>   | <span data-ttu-id="8624e-1853">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1853">+0</span></span>   | <span data-ttu-id="8624e-1854">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1854">-0</span></span>   | <span data-ttu-id="8624e-1855">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1855">+inf</span></span> | <span data-ttu-id="8624e-1856">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1856">-inf</span></span> | <span data-ttu-id="8624e-1857">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1857">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1858">+x</span><span class="sxs-lookup"><span data-stu-id="8624e-1858">+x</span></span>   | <span data-ttu-id="8624e-1859">+z</span><span class="sxs-lookup"><span data-stu-id="8624e-1859">+z</span></span>   | <span data-ttu-id="8624e-1860">+z</span><span class="sxs-lookup"><span data-stu-id="8624e-1860">+z</span></span>   | <span data-ttu-id="8624e-1861">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1861">NaN</span></span>  | <span data-ttu-id="8624e-1862">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1862">NaN</span></span>  | <span data-ttu-id="8624e-1863">x</span><span class="sxs-lookup"><span data-stu-id="8624e-1863">x</span></span>    | <span data-ttu-id="8624e-1864">x</span><span class="sxs-lookup"><span data-stu-id="8624e-1864">x</span></span>    | <span data-ttu-id="8624e-1865">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1865">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1866">-x</span><span class="sxs-lookup"><span data-stu-id="8624e-1866">-x</span></span>   | <span data-ttu-id="8624e-1867">-z</span><span class="sxs-lookup"><span data-stu-id="8624e-1867">-z</span></span>   | <span data-ttu-id="8624e-1868">-z</span><span class="sxs-lookup"><span data-stu-id="8624e-1868">-z</span></span>   | <span data-ttu-id="8624e-1869">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1869">NaN</span></span>  | <span data-ttu-id="8624e-1870">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1870">NaN</span></span>  | <span data-ttu-id="8624e-1871">-x</span><span class="sxs-lookup"><span data-stu-id="8624e-1871">-x</span></span>   | <span data-ttu-id="8624e-1872">-x</span><span class="sxs-lookup"><span data-stu-id="8624e-1872">-x</span></span>   | <span data-ttu-id="8624e-1873">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1873">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1874">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1874">+0</span></span>   | <span data-ttu-id="8624e-1875">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1875">+0</span></span>   | <span data-ttu-id="8624e-1876">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1876">+0</span></span>   | <span data-ttu-id="8624e-1877">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1877">NaN</span></span>  | <span data-ttu-id="8624e-1878">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1878">NaN</span></span>  | <span data-ttu-id="8624e-1879">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1879">+0</span></span>   | <span data-ttu-id="8624e-1880">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1880">+0</span></span>   | <span data-ttu-id="8624e-1881">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1881">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1882">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1882">-0</span></span>   | <span data-ttu-id="8624e-1883">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1883">-0</span></span>   | <span data-ttu-id="8624e-1884">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1884">-0</span></span>   | <span data-ttu-id="8624e-1885">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1885">NaN</span></span>  | <span data-ttu-id="8624e-1886">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1886">NaN</span></span>  | <span data-ttu-id="8624e-1887">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1887">-0</span></span>   | <span data-ttu-id="8624e-1888">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1888">-0</span></span>   | <span data-ttu-id="8624e-1889">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1889">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1890">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1890">+inf</span></span> | <span data-ttu-id="8624e-1891">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1891">NaN</span></span>  | <span data-ttu-id="8624e-1892">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1892">NaN</span></span>  | <span data-ttu-id="8624e-1893">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1893">NaN</span></span>  | <span data-ttu-id="8624e-1894">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1894">NaN</span></span>  | <span data-ttu-id="8624e-1895">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1895">NaN</span></span>  | <span data-ttu-id="8624e-1896">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1896">NaN</span></span>  | <span data-ttu-id="8624e-1897">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1897">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1898">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1898">-inf</span></span> | <span data-ttu-id="8624e-1899">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1899">NaN</span></span>  | <span data-ttu-id="8624e-1900">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1900">NaN</span></span>  | <span data-ttu-id="8624e-1901">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1901">NaN</span></span>  | <span data-ttu-id="8624e-1902">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1902">NaN</span></span>  | <span data-ttu-id="8624e-1903">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1903">NaN</span></span>  | <span data-ttu-id="8624e-1904">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1904">NaN</span></span>  | <span data-ttu-id="8624e-1905">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1905">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1906">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1906">NaN</span></span>  | <span data-ttu-id="8624e-1907">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1907">NaN</span></span>  | <span data-ttu-id="8624e-1908">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1908">NaN</span></span>  | <span data-ttu-id="8624e-1909">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1909">NaN</span></span>  | <span data-ttu-id="8624e-1910">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1910">NaN</span></span>  | <span data-ttu-id="8624e-1911">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1911">NaN</span></span>  | <span data-ttu-id="8624e-1912">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1912">NaN</span></span>  | <span data-ttu-id="8624e-1913">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1913">NaN</span></span>  | 

*  <span data-ttu-id="8624e-1914">Resto decimale:</span><span class="sxs-lookup"><span data-stu-id="8624e-1914">Decimal remainder:</span></span>

   ```csharp
   decimal operator %(decimal x, decimal y);
   ```

   <span data-ttu-id="8624e-1915">Se il valore dell'operando destro è zero, una `System.DivideByZeroException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1915">If the value of the right operand is zero, a `System.DivideByZeroException` is thrown.</span></span> <span data-ttu-id="8624e-1916">La scala del risultato, prima di qualsiasi arrotondamento, è il più elevato tra le scale dei due operandi e il segno del risultato, se diverso da zero, è uguale a quello di `x`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1916">The scale of the result, before any rounding, is the larger of the scales of the two operands, and the sign of the result, if non-zero, is the same as that of `x`.</span></span>

   <span data-ttu-id="8624e-1917">Resto decimale equivale a usare l'operatore di resto del tipo `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1917">Decimal remainder is equivalent to using the remainder operator of type `System.Decimal`.</span></span>


### <a name="addition-operator"></a><span data-ttu-id="8624e-1918">Operatore di addizione</span><span class="sxs-lookup"><span data-stu-id="8624e-1918">Addition operator</span></span>

<span data-ttu-id="8624e-1919">Per un'operazione del form `x + y`, la risoluzione dell'overload di operatore binario ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico.</span><span class="sxs-lookup"><span data-stu-id="8624e-1919">For an operation of the form `x + y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="8624e-1920">Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-1920">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="8624e-1921">Di seguito sono elencati gli operatori di addizione già.</span><span class="sxs-lookup"><span data-stu-id="8624e-1921">The predefined addition operators are listed below.</span></span> <span data-ttu-id="8624e-1922">Per i tipi numerici e di enumerazione, gli operatori di addizione già calcolano la somma dei due operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-1922">For numeric and enumeration types, the predefined addition operators compute the sum of the two operands.</span></span> <span data-ttu-id="8624e-1923">Quando uno o entrambi gli operandi sono di tipo stringa, gli operatori di addizione già concatenano la rappresentazione di stringa degli operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-1923">When one or both operands are of type string, the predefined addition operators concatenate the string representation of the operands.</span></span>

*  <span data-ttu-id="8624e-1924">Aggiunta di integer:</span><span class="sxs-lookup"><span data-stu-id="8624e-1924">Integer addition:</span></span>

   ```csharp
   int operator +(int x, int y);
   uint operator +(uint x, uint y);
   long operator +(long x, long y);
   ulong operator +(ulong x, ulong y);
   ```

   <span data-ttu-id="8624e-1925">In un `checked` contesto, se la somma è compreso nell'intervallo del tipo di risultato, un `System.OverflowException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1925">In a `checked` context, if the sum is outside the range of the result type, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="8624e-1926">In un `unchecked` contesto, eventuali overflow non vengono segnalati e qualsiasi bit più significativi significativo di fuori dell'intervallo del tipo di risultato vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="8624e-1926">In an `unchecked` context, overflows are not reported and any significant high-order bits outside the range of the result type are discarded.</span></span>

*  <span data-ttu-id="8624e-1927">Addizione a virgola mobile:</span><span class="sxs-lookup"><span data-stu-id="8624e-1927">Floating-point addition:</span></span>

   ```csharp
   float operator +(float x, float y);
   double operator +(double x, double y);
   ```

   <span data-ttu-id="8624e-1928">La somma viene calcolata in base alle regole aritmetiche IEEE 754.</span><span class="sxs-lookup"><span data-stu-id="8624e-1928">The sum is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="8624e-1929">Nella tabella seguente sono elencati i risultati di tutte le possibili combinazioni di valori finiti diverso da zero, gli zeri, valori infiniti e NaN.</span><span class="sxs-lookup"><span data-stu-id="8624e-1929">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="8624e-1930">Nella tabella `x` e `y` sono valori finiti diverso da zero, e `z` è il risultato di `x + y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1930">In the table, `x` and `y` are nonzero finite values, and `z` is the result of `x + y`.</span></span> <span data-ttu-id="8624e-1931">Se `x` e `y` hanno la stessa grandezza ma segno opposto, `z` è zero positivo.</span><span class="sxs-lookup"><span data-stu-id="8624e-1931">If `x` and `y` have the same magnitude but opposite signs, `z` is positive zero.</span></span> <span data-ttu-id="8624e-1932">Se `x + y` è troppo grande per essere rappresentato nel tipo di destinazione `z` è un numero infinito con lo stesso segno `x + y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1932">If `x + y` is too large to represent in the destination type, `z` is an infinity with the same sign as `x + y`.</span></span>

   |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | <span data-ttu-id="8624e-1933">y</span><span class="sxs-lookup"><span data-stu-id="8624e-1933">y</span></span>    | <span data-ttu-id="8624e-1934">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1934">+0</span></span>   | <span data-ttu-id="8624e-1935">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1935">-0</span></span>   | <span data-ttu-id="8624e-1936">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1936">+inf</span></span> | <span data-ttu-id="8624e-1937">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1937">-inf</span></span> | <span data-ttu-id="8624e-1938">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1938">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1939">x</span><span class="sxs-lookup"><span data-stu-id="8624e-1939">x</span></span>    | <span data-ttu-id="8624e-1940">l</span><span class="sxs-lookup"><span data-stu-id="8624e-1940">z</span></span>    | <span data-ttu-id="8624e-1941">x</span><span class="sxs-lookup"><span data-stu-id="8624e-1941">x</span></span>    | <span data-ttu-id="8624e-1942">x</span><span class="sxs-lookup"><span data-stu-id="8624e-1942">x</span></span>    | <span data-ttu-id="8624e-1943">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1943">+inf</span></span> | <span data-ttu-id="8624e-1944">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1944">-inf</span></span> | <span data-ttu-id="8624e-1945">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1945">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1946">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1946">+0</span></span>   | <span data-ttu-id="8624e-1947">y</span><span class="sxs-lookup"><span data-stu-id="8624e-1947">y</span></span>    | <span data-ttu-id="8624e-1948">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1948">+0</span></span>   | <span data-ttu-id="8624e-1949">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1949">+0</span></span>   | <span data-ttu-id="8624e-1950">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1950">+inf</span></span> | <span data-ttu-id="8624e-1951">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1951">-inf</span></span> | <span data-ttu-id="8624e-1952">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1952">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1953">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1953">-0</span></span>   | <span data-ttu-id="8624e-1954">y</span><span class="sxs-lookup"><span data-stu-id="8624e-1954">y</span></span>    | <span data-ttu-id="8624e-1955">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-1955">+0</span></span>   | <span data-ttu-id="8624e-1956">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-1956">-0</span></span>   | <span data-ttu-id="8624e-1957">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1957">+inf</span></span> | <span data-ttu-id="8624e-1958">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1958">-inf</span></span> | <span data-ttu-id="8624e-1959">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1959">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1960">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1960">+inf</span></span> | <span data-ttu-id="8624e-1961">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1961">+inf</span></span> | <span data-ttu-id="8624e-1962">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1962">+inf</span></span> | <span data-ttu-id="8624e-1963">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1963">+inf</span></span> | <span data-ttu-id="8624e-1964">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1964">+inf</span></span> | <span data-ttu-id="8624e-1965">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1965">NaN</span></span>  | <span data-ttu-id="8624e-1966">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1966">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1967">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1967">-inf</span></span> | <span data-ttu-id="8624e-1968">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1968">-inf</span></span> | <span data-ttu-id="8624e-1969">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1969">-inf</span></span> | <span data-ttu-id="8624e-1970">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1970">-inf</span></span> | <span data-ttu-id="8624e-1971">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1971">NaN</span></span>  | <span data-ttu-id="8624e-1972">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-1972">-inf</span></span> | <span data-ttu-id="8624e-1973">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1973">NaN</span></span>  | 
   | <span data-ttu-id="8624e-1974">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1974">NaN</span></span>  | <span data-ttu-id="8624e-1975">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1975">NaN</span></span>  | <span data-ttu-id="8624e-1976">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1976">NaN</span></span>  | <span data-ttu-id="8624e-1977">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1977">NaN</span></span>  | <span data-ttu-id="8624e-1978">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1978">NaN</span></span>  | <span data-ttu-id="8624e-1979">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1979">NaN</span></span>  | <span data-ttu-id="8624e-1980">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-1980">NaN</span></span>  | 

*  <span data-ttu-id="8624e-1981">Addizione:</span><span class="sxs-lookup"><span data-stu-id="8624e-1981">Decimal addition:</span></span>

   ```csharp
   decimal operator +(decimal x, decimal y);
   ```

   <span data-ttu-id="8624e-1982">Se il valore risultante è troppo grande per essere rappresentato nel `decimal` formato, un `System.OverflowException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1982">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="8624e-1983">La scala del risultato, prima di qualsiasi arrotondamento, è il più elevato tra le scale dei due operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-1983">The scale of the result, before any rounding, is the larger of the scales of the two operands.</span></span>

   <span data-ttu-id="8624e-1984">Addizione equivale all'uso dell'operatore di addizione typu `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1984">Decimal addition is equivalent to using the addition operator of type `System.Decimal`.</span></span>

*  <span data-ttu-id="8624e-1985">Aggiunta di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-1985">Enumeration addition.</span></span> <span data-ttu-id="8624e-1986">Ogni tipo di enumerazione in modo implicito fornisce i seguenti operatori, predefiniti in cui `E` è il tipo di enumerazione, e `U` è il tipo sottostante di `E`:</span><span class="sxs-lookup"><span data-stu-id="8624e-1986">Every enumeration type implicitly provides the following predefined operators, where `E` is the enum type, and `U` is the underlying type of `E`:</span></span>

   ```csharp
   E operator +(E x, U y);
   E operator +(U x, E y);
   ```

   <span data-ttu-id="8624e-1987">In fase di esecuzione questi operatori vengono valutati esattamente come `(E)((U)x + (U)y)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1987">At run-time these operators are evaluated exactly as `(E)((U)x + (U)y)`.</span></span>

*  <span data-ttu-id="8624e-1988">Concatenazione di stringhe:</span><span class="sxs-lookup"><span data-stu-id="8624e-1988">String concatenation:</span></span>

   ```csharp
   string operator +(string x, string y);
   string operator +(string x, object y);
   string operator +(object x, string y);
   ```

   <span data-ttu-id="8624e-1989">Questi overload del file binario `+` operatore esegue la concatenazione di stringhe.</span><span class="sxs-lookup"><span data-stu-id="8624e-1989">These overloads of the binary `+` operator perform string concatenation.</span></span> <span data-ttu-id="8624e-1990">Se un operando di concatenazione di stringhe viene `null`, viene sostituito con una stringa vuota.</span><span class="sxs-lookup"><span data-stu-id="8624e-1990">If an operand of string concatenation is `null`, an empty string is substituted.</span></span> <span data-ttu-id="8624e-1991">In caso contrario, qualsiasi argomento non di tipo stringa viene convertito in una rappresentazione di stringa richiamando virtuale `ToString` metodo ereditato dal tipo `object`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1991">Otherwise, any non-string argument is converted to its string representation by invoking the virtual `ToString` method inherited from type `object`.</span></span> <span data-ttu-id="8624e-1992">Se `ToString` restituisce `null`, viene sostituito con una stringa vuota.</span><span class="sxs-lookup"><span data-stu-id="8624e-1992">If `ToString` returns `null`, an empty string is substituted.</span></span>

   ```csharp
   using System;
   
   class Test
   {
       static void Main() {
           string s = null;
           Console.WriteLine("s = >" + s + "<");        // displays s = ><
           int i = 1;
           Console.WriteLine("i = " + i);               // displays i = 1
           float f = 1.2300E+15F;
           Console.WriteLine("f = " + f);               // displays f = 1.23E+15
           decimal d = 2.900m;
           Console.WriteLine("d = " + d);               // displays d = 2.900
       }
   }
   ```

   Il risultato dell'operatore di concatenazione delle stringhe è una stringa che include i caratteri dell'operando sinistro seguito dai caratteri dell'operando destro. L'operatore di concatenazione delle stringhe non restituisce mai un `null` valore. <span data-ttu-id="8624e-1995">Oggetto `System.OutOfMemoryException` può essere generata se non è disponibile memoria sufficiente per allocare la stringa risultante.</span><span class="sxs-lookup"><span data-stu-id="8624e-1995">A `System.OutOfMemoryException` may be thrown if there is not enough memory available to allocate the resulting string.</span></span>

*  <span data-ttu-id="8624e-1996">Combinazione di delegati.</span><span class="sxs-lookup"><span data-stu-id="8624e-1996">Delegate combination.</span></span> <span data-ttu-id="8624e-1997">Ogni tipo di delegato in modo implicito fornisce i seguenti operatori predefiniti, in cui `D` è il tipo di delegato:</span><span class="sxs-lookup"><span data-stu-id="8624e-1997">Every delegate type implicitly provides the following predefined operator, where `D` is the delegate type:</span></span>

   ```csharp
   D operator +(D x, D y);
   ```

   <span data-ttu-id="8624e-1998">Il file binario `+` operatore esegue una combinazione di delegati quando entrambi gli operandi sono dello stesso tipo delegato `D`.</span><span class="sxs-lookup"><span data-stu-id="8624e-1998">The binary `+` operator performs delegate combination when both operands are of some delegate type `D`.</span></span> <span data-ttu-id="8624e-1999">(Se gli operandi hanno tipi delegati diversi, si verifica un errore in fase di associazione.) Se è il primo operando `null`, il risultato dell'operazione è il valore del secondo operando (anche se sono anche `null`).</span><span class="sxs-lookup"><span data-stu-id="8624e-1999">(If the operands have different delegate types, a binding-time error occurs.) If the first operand is `null`, the result of the operation is the value of the second operand (even if that is also `null`).</span></span> <span data-ttu-id="8624e-2000">In caso contrario, se il secondo operando `null`, il risultato dell'operazione è il valore del primo operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-2000">Otherwise, if the second operand is `null`, then the result of the operation is the value of the first operand.</span></span> <span data-ttu-id="8624e-2001">In caso contrario, il risultato dell'operazione è un nuovo delegato dell'istanza che, quando richiamata, richiama il primo operando e richiama quindi il secondo operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-2001">Otherwise, the result of the operation is a new delegate instance that, when invoked, invokes the first operand and then invokes the second operand.</span></span> <span data-ttu-id="8624e-2002">Per esempi di combinazione di delegati, vedere [operatore di sottrazione](expressions.md#subtraction-operator) e [chiamata a un delegato](delegates.md#delegate-invocation).</span><span class="sxs-lookup"><span data-stu-id="8624e-2002">For examples of delegate combination, see [Subtraction operator](expressions.md#subtraction-operator) and [Delegate invocation](delegates.md#delegate-invocation).</span></span> <span data-ttu-id="8624e-2003">Poiché `System.Delegate` non è un tipo di delegato `operator`  `+` non è definito.</span><span class="sxs-lookup"><span data-stu-id="8624e-2003">Since `System.Delegate` is not a delegate type, `operator` `+` is not defined for it.</span></span>

### <a name="subtraction-operator"></a><span data-ttu-id="8624e-2004">Operatore di sottrazione</span><span class="sxs-lookup"><span data-stu-id="8624e-2004">Subtraction operator</span></span>

<span data-ttu-id="8624e-2005">Per un'operazione del form `x - y`, la risoluzione dell'overload di operatore binario ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico.</span><span class="sxs-lookup"><span data-stu-id="8624e-2005">For an operation of the form `x - y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="8624e-2006">Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2006">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="8624e-2007">Gli operatori predefiniti sottrazione sono elencati di seguito.</span><span class="sxs-lookup"><span data-stu-id="8624e-2007">The predefined subtraction operators are listed below.</span></span> <span data-ttu-id="8624e-2008">Gli operatori tutti sottrarre `y` da `x`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2008">The operators all subtract `y` from `x`.</span></span>

*  <span data-ttu-id="8624e-2009">Sottrazione di integer:</span><span class="sxs-lookup"><span data-stu-id="8624e-2009">Integer subtraction:</span></span>

   ```csharp
   int operator -(int x, int y);
   uint operator -(uint x, uint y);
   long operator -(long x, long y);
   ulong operator -(ulong x, ulong y);
   ```

   <span data-ttu-id="8624e-2010">In un `checked` contesto, se la differenza è compreso nell'intervallo del tipo di risultato, un `System.OverflowException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2010">In a `checked` context, if the difference is outside the range of the result type, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="8624e-2011">In un `unchecked` contesto, eventuali overflow non vengono segnalati e qualsiasi bit più significativi significativo di fuori dell'intervallo del tipo di risultato vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="8624e-2011">In an `unchecked` context, overflows are not reported and any significant high-order bits outside the range of the result type are discarded.</span></span>

*  <span data-ttu-id="8624e-2012">Sottrazione a virgola mobile:</span><span class="sxs-lookup"><span data-stu-id="8624e-2012">Floating-point subtraction:</span></span>

   ```csharp
   float operator -(float x, float y);
   double operator -(double x, double y);
   ```

   <span data-ttu-id="8624e-2013">La differenza viene calcolata in base alle regole aritmetiche IEEE 754.</span><span class="sxs-lookup"><span data-stu-id="8624e-2013">The difference is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="8624e-2014">Nella tabella seguente sono elencati i risultati di tutte le possibili combinazioni di valori finiti diverso da zero, gli zeri, valori infiniti e NaN.</span><span class="sxs-lookup"><span data-stu-id="8624e-2014">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaNs.</span></span> <span data-ttu-id="8624e-2015">Nella tabella `x` e `y` sono valori finiti diverso da zero, e `z` è il risultato di `x - y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2015">In the table, `x` and `y` are nonzero finite values, and `z` is the result of `x - y`.</span></span> <span data-ttu-id="8624e-2016">Se `x` e `y` sono uguali, `z` è zero positivo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2016">If `x` and `y` are equal, `z` is positive zero.</span></span> <span data-ttu-id="8624e-2017">Se `x - y` è troppo grande per essere rappresentato nel tipo di destinazione `z` è un numero infinito con lo stesso segno `x - y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2017">If `x - y` is too large to represent in the destination type, `z` is an infinity with the same sign as `x - y`.</span></span>

   |      |      |      |      |      |      |     |
   |:----:|:----:|:----:|:----:|:----:|:----:|:---:|
   |      | <span data-ttu-id="8624e-2018">y</span><span class="sxs-lookup"><span data-stu-id="8624e-2018">y</span></span>    | <span data-ttu-id="8624e-2019">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-2019">+0</span></span>   | <span data-ttu-id="8624e-2020">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-2020">-0</span></span>   | <span data-ttu-id="8624e-2021">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2021">+inf</span></span> | <span data-ttu-id="8624e-2022">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2022">-inf</span></span> | <span data-ttu-id="8624e-2023">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-2023">NaN</span></span> | 
   | <span data-ttu-id="8624e-2024">x</span><span class="sxs-lookup"><span data-stu-id="8624e-2024">x</span></span>    | <span data-ttu-id="8624e-2025">l</span><span class="sxs-lookup"><span data-stu-id="8624e-2025">z</span></span>    | <span data-ttu-id="8624e-2026">x</span><span class="sxs-lookup"><span data-stu-id="8624e-2026">x</span></span>    | <span data-ttu-id="8624e-2027">x</span><span class="sxs-lookup"><span data-stu-id="8624e-2027">x</span></span>    | <span data-ttu-id="8624e-2028">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2028">-inf</span></span> | <span data-ttu-id="8624e-2029">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2029">+inf</span></span> | <span data-ttu-id="8624e-2030">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-2030">NaN</span></span> | 
   | <span data-ttu-id="8624e-2031">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-2031">+0</span></span>   | <span data-ttu-id="8624e-2032">-y</span><span class="sxs-lookup"><span data-stu-id="8624e-2032">-y</span></span>   | <span data-ttu-id="8624e-2033">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-2033">+0</span></span>   | <span data-ttu-id="8624e-2034">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-2034">+0</span></span>   | <span data-ttu-id="8624e-2035">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2035">-inf</span></span> | <span data-ttu-id="8624e-2036">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2036">+inf</span></span> | <span data-ttu-id="8624e-2037">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-2037">NaN</span></span> | 
   | <span data-ttu-id="8624e-2038">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-2038">-0</span></span>   | <span data-ttu-id="8624e-2039">-y</span><span class="sxs-lookup"><span data-stu-id="8624e-2039">-y</span></span>   | <span data-ttu-id="8624e-2040">-0</span><span class="sxs-lookup"><span data-stu-id="8624e-2040">-0</span></span>   | <span data-ttu-id="8624e-2041">+0</span><span class="sxs-lookup"><span data-stu-id="8624e-2041">+0</span></span>   | <span data-ttu-id="8624e-2042">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2042">-inf</span></span> | <span data-ttu-id="8624e-2043">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2043">+inf</span></span> | <span data-ttu-id="8624e-2044">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-2044">NaN</span></span> | 
   | <span data-ttu-id="8624e-2045">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2045">+inf</span></span> | <span data-ttu-id="8624e-2046">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2046">+inf</span></span> | <span data-ttu-id="8624e-2047">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2047">+inf</span></span> | <span data-ttu-id="8624e-2048">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2048">+inf</span></span> | <span data-ttu-id="8624e-2049">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-2049">NaN</span></span>  | <span data-ttu-id="8624e-2050">+inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2050">+inf</span></span> | <span data-ttu-id="8624e-2051">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-2051">NaN</span></span> | 
   | <span data-ttu-id="8624e-2052">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2052">-inf</span></span> | <span data-ttu-id="8624e-2053">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2053">-inf</span></span> | <span data-ttu-id="8624e-2054">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2054">-inf</span></span> | <span data-ttu-id="8624e-2055">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2055">-inf</span></span> | <span data-ttu-id="8624e-2056">-inf</span><span class="sxs-lookup"><span data-stu-id="8624e-2056">-inf</span></span> | <span data-ttu-id="8624e-2057">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-2057">NaN</span></span>  | <span data-ttu-id="8624e-2058">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-2058">NaN</span></span> | 
   | <span data-ttu-id="8624e-2059">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-2059">NaN</span></span>  | <span data-ttu-id="8624e-2060">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-2060">NaN</span></span>  | <span data-ttu-id="8624e-2061">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-2061">NaN</span></span>  | <span data-ttu-id="8624e-2062">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-2062">NaN</span></span>  | <span data-ttu-id="8624e-2063">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-2063">NaN</span></span>  | <span data-ttu-id="8624e-2064">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-2064">NaN</span></span>  | <span data-ttu-id="8624e-2065">NaN</span><span class="sxs-lookup"><span data-stu-id="8624e-2065">NaN</span></span> | 

*  <span data-ttu-id="8624e-2066">Sottrazione decimale:</span><span class="sxs-lookup"><span data-stu-id="8624e-2066">Decimal subtraction:</span></span>

   ```csharp
   decimal operator -(decimal x, decimal y);
   ```

   <span data-ttu-id="8624e-2067">Se il valore risultante è troppo grande per essere rappresentato nel `decimal` formato, un `System.OverflowException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2067">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="8624e-2068">La scala del risultato, prima di qualsiasi arrotondamento, è il più elevato tra le scale dei due operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-2068">The scale of the result, before any rounding, is the larger of the scales of the two operands.</span></span>

   <span data-ttu-id="8624e-2069">Sottrazione decimale equivale a usare l'operatore di sottrazione del tipo `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2069">Decimal subtraction is equivalent to using the subtraction operator of type `System.Decimal`.</span></span>

*  <span data-ttu-id="8624e-2070">Sottrazione di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2070">Enumeration subtraction.</span></span> <span data-ttu-id="8624e-2071">Ogni tipo di enumerazione fornisce in modo implicito dall'operatore predefinito seguente, dove `E` è il tipo di enumerazione, e `U` è il tipo sottostante di `E`:</span><span class="sxs-lookup"><span data-stu-id="8624e-2071">Every enumeration type implicitly provides the following predefined operator, where `E` is the enum type, and `U` is the underlying type of `E`:</span></span>

   ```csharp
   U operator -(E x, E y);
   ```

   <span data-ttu-id="8624e-2072">Questo operatore viene valutato esattamente come `(U)((U)x - (U)y)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2072">This operator is evaluated exactly as `(U)((U)x - (U)y)`.</span></span> <span data-ttu-id="8624e-2073">In altre parole, l'operatore calcola la differenza tra i valori ordinali delle `x` e `y`, e il tipo del risultato è il tipo sottostante dell'enumerazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2073">In other words, the operator computes the difference between the ordinal values of `x` and `y`, and the type of the result is the underlying type of the enumeration.</span></span>

   ```csharp
   E operator -(E x, U y);
   ```

   <span data-ttu-id="8624e-2074">Questo operatore viene valutato esattamente come `(E)((U)x - y)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2074">This operator is evaluated exactly as `(E)((U)x - y)`.</span></span> <span data-ttu-id="8624e-2075">In altre parole, l'operatore sottrae un valore dal tipo sottostante dell'enumerazione, producendo un valore dell'enumerazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2075">In other words, the operator subtracts a value from the underlying type of the enumeration, yielding a value of the enumeration.</span></span>

*  <span data-ttu-id="8624e-2076">Rimozione del delegato.</span><span class="sxs-lookup"><span data-stu-id="8624e-2076">Delegate removal.</span></span> <span data-ttu-id="8624e-2077">Ogni tipo di delegato in modo implicito fornisce i seguenti operatori predefiniti, in cui `D` è il tipo di delegato:</span><span class="sxs-lookup"><span data-stu-id="8624e-2077">Every delegate type implicitly provides the following predefined operator, where `D` is the delegate type:</span></span>

   ```csharp
   D operator -(D x, D y);
   ```

   <span data-ttu-id="8624e-2078">Il file binario `-` operatore esegue la rimozione dei delegati quando entrambi gli operandi sono dello stesso tipo delegato `D`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2078">The binary `-` operator performs delegate removal when both operands are of some delegate type `D`.</span></span> <span data-ttu-id="8624e-2079">Se gli operandi hanno tipi delegati diversi, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2079">If the operands have different delegate types, a binding-time error occurs.</span></span> <span data-ttu-id="8624e-2080">Se è il primo operando `null`, il risultato dell'operazione è `null`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2080">If the first operand is `null`, the result of the operation is `null`.</span></span> <span data-ttu-id="8624e-2081">In caso contrario, se il secondo operando `null`, il risultato dell'operazione è il valore del primo operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-2081">Otherwise, if the second operand is `null`, then the result of the operation is the value of the first operand.</span></span> <span data-ttu-id="8624e-2082">In caso contrario, entrambi gli operandi rappresentano gli elenchi di chiamata ([dichiarazioni Delegate](delegates.md#delegate-declarations)) con una o più voci e il risultato è costituito l'elenco del primo operando con il secondo rimosse le voci da un nuovo elenco di chiamate fornito l'elenco del secondo operando è un sottoelenco contiguo del primo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2082">Otherwise, both operands represent invocation lists ([Delegate declarations](delegates.md#delegate-declarations)) having one or more entries, and the result is a new invocation list consisting of the first operand's list with the second operand's entries removed from it, provided the second operand's list is a proper contiguous sublist of the first's.</span></span>     <span data-ttu-id="8624e-2083">(Per determinare l'uguaglianza sottoelenco, le voci corrispondenti vengono confrontate per l'operatore di uguaglianza delegato ([delegare gli operatori di uguaglianza](expressions.md#delegate-equality-operators)).) In caso contrario, il risultato è il valore dell'operando sinistro.</span><span class="sxs-lookup"><span data-stu-id="8624e-2083">(To determine sublist equality, corresponding entries are compared as for the delegate equality operator ([Delegate equality operators](expressions.md#delegate-equality-operators)).) Otherwise, the result is the value of the left operand.</span></span> <span data-ttu-id="8624e-2084">Nessuno degli elenchi degli operandi viene modificato nel processo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2084">Neither of the operands' lists is changed in the process.</span></span> <span data-ttu-id="8624e-2085">Se l'elenco del secondo operando corrisponde a più sottoelenchi contigui voci nell'elenco del primo operando, viene rimosso il sottoelenco corrisponda più a destra delle voci contigui.</span><span class="sxs-lookup"><span data-stu-id="8624e-2085">If the second operand's list matches multiple sublists of contiguous entries in the first operand's list, the right-most matching sublist of contiguous entries is removed.</span></span> <span data-ttu-id="8624e-2086">Se la rimozione restituisce un elenco vuoto, il risultato è `null`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2086">If removal results in an empty list, the result is `null`.</span></span> <span data-ttu-id="8624e-2087">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8624e-2087">For example:</span></span>

   ```csharp
   delegate void D(int x);
   
   class C
   {
       public static void M1(int i) { /* ... */ }
       public static void M2(int i) { /* ... */ }
   }

   class Test
   {
       static void Main() { 
           D cd1 = new D(C.M1);
           D cd2 = new D(C.M2);
           D cd3 = cd1 + cd2 + cd2 + cd1;   // M1 + M2 + M2 + M1
           cd3 -= cd1;                      // => M1 + M2 + M2
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd1 + cd2;                // => M2 + M1
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd2 + cd2;                // => M1 + M1
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd2 + cd1;                // => M1 + M2
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd1 + cd1;                // => M1 + M2 + M2 + M1
       }
   }
   ```

## <a name="shift-operators"></a><span data-ttu-id="8624e-2088">Operatori shift</span><span class="sxs-lookup"><span data-stu-id="8624e-2088">Shift operators</span></span>

<span data-ttu-id="8624e-2089">Il `<<` e `>>` gli operatori vengono utilizzati per eseguire operazioni di spostamento di bit.</span><span class="sxs-lookup"><span data-stu-id="8624e-2089">The `<<` and `>>` operators are used to perform bit shifting operations.</span></span>

```antlr
shift_expression
    : additive_expression
    | shift_expression '<<' additive_expression
    | shift_expression right_shift additive_expression
    ;
```

<span data-ttu-id="8624e-2090">Se un operando di un *shift_expression* dispone del tipo in fase di compilazione `dynamic`, quindi l'espressione è associata in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2090">If an operand of a *shift_expression* has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="8624e-2091">In questo caso è il tipo di fase di compilazione dell'espressione `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di fase di esecuzione di tali operandi che presentano il tipo di fase di compilazione `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2091">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="8624e-2092">Per un'operazione del form `x << count` oppure `x >> count`, la risoluzione dell'overload di operatore binario ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico.</span><span class="sxs-lookup"><span data-stu-id="8624e-2092">For an operation of the form `x << count` or `x >> count`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="8624e-2093">Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2093">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="8624e-2094">Quando si dichiara un operatore shift di overload, il tipo del primo operando deve essere sempre la classe o struct che contiene la dichiarazione dell'operatore e il tipo del secondo operando deve essere sempre `int`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2094">When declaring an overloaded shift operator, the type of the first operand must always be the class or struct containing the operator declaration, and the type of the second operand must always be `int`.</span></span>

<span data-ttu-id="8624e-2095">Operatori di spostamento predefiniti vengono elencati di seguito.</span><span class="sxs-lookup"><span data-stu-id="8624e-2095">The predefined shift operators are listed below.</span></span>

*  <span data-ttu-id="8624e-2096">Spostamento a sinistra:</span><span class="sxs-lookup"><span data-stu-id="8624e-2096">Shift left:</span></span>

   ```csharp
   int operator <<(int x, int count);
   uint operator <<(uint x, int count);
   long operator <<(long x, int count);
   ulong operator <<(ulong x, int count);
   ```

   <span data-ttu-id="8624e-2097">Il `<<` turni operatore `x` a sinistra di un numero di bit calcolata come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="8624e-2097">The `<<` operator shifts `x` left by a number of bits computed as described below.</span></span>

   <span data-ttu-id="8624e-2098">Il bit più significativi compreso nell'intervallo del tipo di risultato di `x` vengono eliminati, i bit rimanenti vengono spostati a sinistra e le posizioni dei bit vuoti meno significativi vengono impostate su zero.</span><span class="sxs-lookup"><span data-stu-id="8624e-2098">The high-order bits outside the range of the result type of `x` are discarded, the remaining bits are shifted left, and the low-order empty bit positions are set to zero.</span></span>

*  <span data-ttu-id="8624e-2099">Spostamento a destra:</span><span class="sxs-lookup"><span data-stu-id="8624e-2099">Shift right:</span></span>

   ```csharp
   int operator >>(int x, int count);
   uint operator >>(uint x, int count);
   long operator >>(long x, int count);
   ulong operator >>(ulong x, int count);
   ```

   <span data-ttu-id="8624e-2100">Il `>>` turni operatore `x` destra di un numero di bit calcolata come descritto di seguito.</span><span class="sxs-lookup"><span data-stu-id="8624e-2100">The `>>` operator shifts `x` right by a number of bits computed as described below.</span></span>

   <span data-ttu-id="8624e-2101">Quando `x` JE typu `int` oppure `long`, il bit meno significativi del `x` vengono scartati, i bit rimanenti vengono spostati a destra e i bit vuoti meno significativi vengono impostati su zero se `x` è un valore non negativo e impostata su 1 se `x` è negativo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2101">When `x` is of type `int` or `long`, the low-order bits of `x` are discarded, the remaining bits are shifted right, and the high-order empty bit positions are set to zero if `x` is non-negative and set to one if `x` is negative.</span></span>

   <span data-ttu-id="8624e-2102">Quando `x` JE typu `uint` oppure `ulong`, il bit meno significativi del `x` vengono eliminati, i bit rimanenti vengono spostati a destra e i bit vuoti meno significativi vengono impostati su zero.</span><span class="sxs-lookup"><span data-stu-id="8624e-2102">When `x` is of type `uint` or `ulong`, the low-order bits of `x` are discarded, the remaining bits are shifted right, and the high-order empty bit positions are set to zero.</span></span>

<span data-ttu-id="8624e-2103">Per gli operatori predefiniti, il numero di bit da spostare viene calcolato come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-2103">For the predefined operators, the number of bits to shift is computed as follows:</span></span>

*  <span data-ttu-id="8624e-2104">Quando il tipo della `x` viene `int` oppure `uint`, il conteggio dello spostamento è dato dai cinque bit meno significativi del `count`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2104">When the type of `x` is `int` or `uint`, the shift count is given by the low-order five bits of `count`.</span></span> <span data-ttu-id="8624e-2105">In altre parole, il conteggio dello spostamento viene calcolato da `count & 0x1F`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2105">In other words, the shift count is computed from `count & 0x1F`.</span></span>
*  <span data-ttu-id="8624e-2106">Quando il tipo della `x` viene `long` oppure `ulong`, il conteggio dello spostamento è dato dai sei bit meno significativi del `count`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2106">When the type of `x` is `long` or `ulong`, the shift count is given by the low-order six bits of `count`.</span></span> <span data-ttu-id="8624e-2107">In altre parole, il conteggio dello spostamento viene calcolato da `count & 0x3F`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2107">In other words, the shift count is computed from `count & 0x3F`.</span></span>

<span data-ttu-id="8624e-2108">Se il conteggio dello spostamento risultante è zero, gli operatori di spostamento è sufficiente restituiscono il valore di `x`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2108">If the resulting shift count is zero, the shift operators simply return the value of `x`.</span></span>

<span data-ttu-id="8624e-2109">Operazioni di spostamento mai provocare un overflow e producono gli stessi risultati in `checked` e `unchecked` contesti.</span><span class="sxs-lookup"><span data-stu-id="8624e-2109">Shift operations never cause overflows and produce the same results in `checked` and `unchecked` contexts.</span></span>

<span data-ttu-id="8624e-2110">Quando l'operando sinistro del `>>` operatore è un tipo integrale con segno, l'operatore esegue uno scorrimento aritmetico a destra dove il valore del bit più significativo (il bit di segno) dell'operando viene propagato ai bit vuoti meno significativi.</span><span class="sxs-lookup"><span data-stu-id="8624e-2110">When the left operand of the `>>` operator is of a signed integral type, the operator performs an arithmetic shift right wherein the value of the most significant bit (the sign bit) of the operand is propagated to the high-order empty bit positions.</span></span> <span data-ttu-id="8624e-2111">Quando l'operando sinistro del `>>` operatore è di tipo integrale senza segno, l'operatore esegue uno spostamento logico verso destra in cui i bit vuoti meno significativi vengono sempre impostati su zero.</span><span class="sxs-lookup"><span data-stu-id="8624e-2111">When the left operand of the `>>` operator is of an unsigned integral type, the operator performs a logical shift right wherein high-order empty bit positions are always set to zero.</span></span> <span data-ttu-id="8624e-2112">Per eseguire l'operazione opposta a quella dedotto dal tipo di operando, è possibile utilizzare i cast espliciti.</span><span class="sxs-lookup"><span data-stu-id="8624e-2112">To perform the opposite operation of that inferred from the operand type, explicit casts can be used.</span></span> <span data-ttu-id="8624e-2113">Ad esempio, se `x` è una variabile di tipo `int`, l'operazione `unchecked((int)((uint)x >> y))` esegue uno spostamento logico a destra del `x`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2113">For example, if `x` is a variable of type `int`, the operation `unchecked((int)((uint)x >> y))` performs a logical shift right of `x`.</span></span>

## <a name="relational-and-type-testing-operators"></a><span data-ttu-id="8624e-2114">Operatori relazionali e operatori di test del tipo</span><span class="sxs-lookup"><span data-stu-id="8624e-2114">Relational and type-testing operators</span></span>

<span data-ttu-id="8624e-2115">Il `==`, `!=`, `<`, `>`, `<=`, `>=`, `is` e `as` gli operatori sono denominati operatori relazionali e test del tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2115">The `==`, `!=`, `<`, `>`, `<=`, `>=`, `is` and `as` operators are called the relational and type-testing operators.</span></span>

```antlr
relational_expression
    : shift_expression
    | relational_expression '<' shift_expression
    | relational_expression '>' shift_expression
    | relational_expression '<=' shift_expression
    | relational_expression '>=' shift_expression
    | relational_expression 'is' type
    | relational_expression 'as' type
    ;

equality_expression
    : relational_expression
    | equality_expression '==' relational_expression
    | equality_expression '!=' relational_expression
    ;
```

<span data-ttu-id="8624e-2116">Il `is` operatore è descritto nella [l'operatore](expressions.md#the-is-operator) e il `as` operatore è descritto in [l'operatore](expressions.md#the-as-operator).</span><span class="sxs-lookup"><span data-stu-id="8624e-2116">The `is` operator is described in [The is operator](expressions.md#the-is-operator) and the `as` operator is described in [The as operator](expressions.md#the-as-operator).</span></span>

<span data-ttu-id="8624e-2117">Il `==`, `!=`, `<`, `>`, `<=` e `>=` operatori sono ***gli operatori di confronto***.</span><span class="sxs-lookup"><span data-stu-id="8624e-2117">The `==`, `!=`, `<`, `>`, `<=` and `>=` operators are ***comparison operators***.</span></span>

<span data-ttu-id="8624e-2118">Se un operando di un operatore di confronto è il tipo di fase di compilazione `dynamic`, quindi in modo dinamico è associata l'espressione ([binding dinamico](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2118">If an operand of a comparison operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="8624e-2119">In questo caso è il tipo di fase di compilazione dell'espressione `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di fase di esecuzione di tali operandi che presentano il tipo di fase di compilazione `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2119">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="8624e-2120">Per un'operazione del form `x` *op* `y`, dove *op* è un operatore di confronto, la risoluzione dell'overload ([larisoluzionedell'overloaddioperatorebinario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico.</span><span class="sxs-lookup"><span data-stu-id="8624e-2120">For an operation of the form `x` *op* `y`, where *op* is a comparison operator, overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="8624e-2121">Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2121">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="8624e-2122">Gli operatori di confronto predefinite sono descritti nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="8624e-2122">The predefined comparison operators are described in the following sections.</span></span> <span data-ttu-id="8624e-2123">Tutti gli operatori di confronto predefiniti restituiscono un risultato di tipo `bool`, come descritto nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="8624e-2123">All predefined comparison operators return a result of type `bool`, as described in the following table.</span></span>


| <span data-ttu-id="8624e-2124">__Operazione__</span><span class="sxs-lookup"><span data-stu-id="8624e-2124">__Operation__</span></span> | <span data-ttu-id="8624e-2125">__Risultato__</span><span class="sxs-lookup"><span data-stu-id="8624e-2125">__Result__</span></span>                                                       |
|---------------|------------------------------------------------------------------|
| `x == y`      | <span data-ttu-id="8624e-2126">`true` Se `x` è uguale a `y`, `false` in caso contrario,</span><span class="sxs-lookup"><span data-stu-id="8624e-2126">`true` if `x` is equal to `y`, `false` otherwise</span></span>                 | 
| `x != y`      | <span data-ttu-id="8624e-2127">`true` Se `x` non è uguale a `y`, `false` in caso contrario,</span><span class="sxs-lookup"><span data-stu-id="8624e-2127">`true` if `x` is not equal to `y`, `false` otherwise</span></span>             | 
| `x < y`       | <span data-ttu-id="8624e-2128">`true` Se `x` è minore di `y`, `false` in caso contrario,</span><span class="sxs-lookup"><span data-stu-id="8624e-2128">`true` if `x` is less than `y`, `false` otherwise</span></span>                | 
| `x > y`       | <span data-ttu-id="8624e-2129">`true` Se `x` è maggiore di quella `y`, `false` in caso contrario,</span><span class="sxs-lookup"><span data-stu-id="8624e-2129">`true` if `x` is greater than `y`, `false` otherwise</span></span>             | 
| `x <= y`      | <span data-ttu-id="8624e-2130">`true` Se `x` è minore o uguale a `y`, `false` in caso contrario,</span><span class="sxs-lookup"><span data-stu-id="8624e-2130">`true` if `x` is less than or equal to `y`, `false` otherwise</span></span>    | 
| `x >= y`      | <span data-ttu-id="8624e-2131">`true` Se `x` è maggiore o uguale a `y`, `false` in caso contrario,</span><span class="sxs-lookup"><span data-stu-id="8624e-2131">`true` if `x` is greater than or equal to `y`, `false` otherwise</span></span> | 

### <a name="integer-comparison-operators"></a><span data-ttu-id="8624e-2132">Operatori di confronto di integer</span><span class="sxs-lookup"><span data-stu-id="8624e-2132">Integer comparison operators</span></span>

<span data-ttu-id="8624e-2133">Gli operatori di confronto tra integer predefiniti sono:</span><span class="sxs-lookup"><span data-stu-id="8624e-2133">The predefined integer comparison operators are:</span></span>
```csharp
bool operator ==(int x, int y);
bool operator ==(uint x, uint y);
bool operator ==(long x, long y);
bool operator ==(ulong x, ulong y);

bool operator !=(int x, int y);
bool operator !=(uint x, uint y);
bool operator !=(long x, long y);
bool operator !=(ulong x, ulong y);

bool operator <(int x, int y);
bool operator <(uint x, uint y);
bool operator <(long x, long y);
bool operator <(ulong x, ulong y);

bool operator >(int x, int y);
bool operator >(uint x, uint y);
bool operator >(long x, long y);
bool operator >(ulong x, ulong y);

bool operator <=(int x, int y);
bool operator <=(uint x, uint y);
bool operator <=(long x, long y);
bool operator <=(ulong x, ulong y);

bool operator >=(int x, int y);
bool operator >=(uint x, uint y);
bool operator >=(long x, long y);
bool operator >=(ulong x, ulong y);
```

<span data-ttu-id="8624e-2134">Ognuno di questi operatori vengono confrontati i valori numerici degli operandi di due integer e restituisce un `bool` valore che indica se è la relazione particolare `true` o `false`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2134">Each of these operators compares the numeric values of the two integer operands and returns a `bool` value that indicates whether the particular relation is `true` or `false`.</span></span>

### <a name="floating-point-comparison-operators"></a><span data-ttu-id="8624e-2135">Operatori di confronto a virgola mobile</span><span class="sxs-lookup"><span data-stu-id="8624e-2135">Floating-point comparison operators</span></span>

<span data-ttu-id="8624e-2136">Gli operatori di confronto a virgola mobile predefiniti sono:</span><span class="sxs-lookup"><span data-stu-id="8624e-2136">The predefined floating-point comparison operators are:</span></span>
```csharp
bool operator ==(float x, float y);
bool operator ==(double x, double y);

bool operator !=(float x, float y);
bool operator !=(double x, double y);

bool operator <(float x, float y);
bool operator <(double x, double y);

bool operator >(float x, float y);
bool operator >(double x, double y);

bool operator <=(float x, float y);
bool operator <=(double x, double y);

bool operator >=(float x, float y);
bool operator >=(double x, double y);
```

<span data-ttu-id="8624e-2137">Gli operatori confrontano gli operandi in base alle regole di standard IEEE 754:</span><span class="sxs-lookup"><span data-stu-id="8624e-2137">The operators compare the operands according to the rules of the IEEE 754 standard:</span></span>

*  <span data-ttu-id="8624e-2138">Se degli operandi è NaN, il risultato viene `false` per tutti gli operatori tranne `!=`, per cui il risultato è `true`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2138">If either operand is NaN, the result is `false` for all operators except `!=`, for which the result is `true`.</span></span> <span data-ttu-id="8624e-2139">Per qualsiasi coppia di operandi `x != y` produca sempre lo stesso risultato `!(x == y)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2139">For any two operands, `x != y` always produces the same result as `!(x == y)`.</span></span> <span data-ttu-id="8624e-2140">Tuttavia, quando uno o entrambi gli operandi sono un valore NaN, la `<`, `>`, `<=`, e `>=` operatori non producono gli stessi risultati la negazione logica dell'operatore opposto.</span><span class="sxs-lookup"><span data-stu-id="8624e-2140">However, when one or both operands are NaN, the `<`, `>`, `<=`, and `>=` operators do not produce the same results as the logical negation of the opposite operator.</span></span> <span data-ttu-id="8624e-2141">Ad esempio, se il valore della `x` e `y` è NaN, quindi `x < y` viene `false`, ma `!(x >= y)` è `true`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2141">For example, if either of `x` and `y` is NaN, then `x < y` is `false`, but `!(x >= y)` is `true`.</span></span>
*  <span data-ttu-id="8624e-2142">Quando nessuno dei due operandi sono NaN, gli operatori confrontano i valori dei due operandi a virgola mobile e per quanto riguarda l'ordinamento</span><span class="sxs-lookup"><span data-stu-id="8624e-2142">When neither operand is NaN, the operators compare the values of the two floating-point operands with respect to the ordering</span></span>

   ```
   -inf < -max < ... < -min < -0.0 == +0.0 < +min < ... < +max < +inf
   ```

   <span data-ttu-id="8624e-2143">in cui `min` e `max` sono i valori minimo e massimo positivi finiti che possono essere rappresentati nel formato a virgola mobile specificato.</span><span class="sxs-lookup"><span data-stu-id="8624e-2143">where `min` and `max` are the smallest and largest positive finite values that can be represented in the given floating-point format.</span></span> <span data-ttu-id="8624e-2144">Notevoli effetti di questo tipo di ordinamento sono:</span><span class="sxs-lookup"><span data-stu-id="8624e-2144">Notable effects of this ordering are:</span></span>
   * <span data-ttu-id="8624e-2145">Zero positivo e negativo zero viene considerati uguali.</span><span class="sxs-lookup"><span data-stu-id="8624e-2145">Negative and positive zeros are considered equal.</span></span>
   * <span data-ttu-id="8624e-2146">Viene considerato un valore infinito negativo minore di tutti gli altri valori, ma uguale a un altro valore infinito negativo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2146">A negative infinity is considered less than all other values, but equal to another negative infinity.</span></span>
   * <span data-ttu-id="8624e-2147">Un valore infinito positivo viene considerato maggiore di tutti gli altri valori, ma uguale a un altro numero infinito positivo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2147">A positive infinity is considered greater than all other values, but equal to another positive infinity.</span></span>

### <a name="decimal-comparison-operators"></a><span data-ttu-id="8624e-2148">Operatori di confronto decimale</span><span class="sxs-lookup"><span data-stu-id="8624e-2148">Decimal comparison operators</span></span>

<span data-ttu-id="8624e-2149">Gli operatori di confronto decimale predefiniti sono:</span><span class="sxs-lookup"><span data-stu-id="8624e-2149">The predefined decimal comparison operators are:</span></span>
```csharp
bool operator ==(decimal x, decimal y);
bool operator !=(decimal x, decimal y);
bool operator <(decimal x, decimal y);
bool operator >(decimal x, decimal y);
bool operator <=(decimal x, decimal y);
bool operator >=(decimal x, decimal y);
```

<span data-ttu-id="8624e-2150">Ognuno di questi operatori vengono confrontati i valori numerici dei due operandi decimali e restituisce un `bool` valore che indica se è la relazione particolare `true` o `false`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2150">Each of these operators compares the numeric values of the two decimal operands and returns a `bool` value that indicates whether the particular relation is `true` or `false`.</span></span> <span data-ttu-id="8624e-2151">Ogni confronto decimale equivale all'uso dell'operatore di uguaglianza del tipo o corrispondente `System.Decimal`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2151">Each decimal comparison is equivalent to using the corresponding relational or equality operator of type `System.Decimal`.</span></span>

### <a name="boolean-equality-operators"></a><span data-ttu-id="8624e-2152">Operatori di uguaglianza booleano</span><span class="sxs-lookup"><span data-stu-id="8624e-2152">Boolean equality operators</span></span>

<span data-ttu-id="8624e-2153">Gli operatori di uguaglianza booleana predefiniti sono:</span><span class="sxs-lookup"><span data-stu-id="8624e-2153">The predefined boolean equality operators are:</span></span>
```csharp
bool operator ==(bool x, bool y);
bool operator !=(bool x, bool y);
```

<span data-ttu-id="8624e-2154">Il risultato del `==` viene `true` se entrambi `x` e `y` sono `true` o se entrambi `x` e `y` sono `false`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2154">The result of `==` is `true` if both `x` and `y` are `true` or if both `x` and `y` are `false`.</span></span> <span data-ttu-id="8624e-2155">In caso contrario, il risultato è `false`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2155">Otherwise, the result is `false`.</span></span>

<span data-ttu-id="8624e-2156">Il risultato del `!=` viene `false` se entrambi `x` e `y` sono `true` o se entrambi `x` e `y` sono `false`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2156">The result of `!=` is `false` if both `x` and `y` are `true` or if both `x` and `y` are `false`.</span></span> <span data-ttu-id="8624e-2157">In caso contrario, il risultato è `true`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2157">Otherwise, the result is `true`.</span></span> <span data-ttu-id="8624e-2158">Quando gli operandi sono di tipo `bool`, il `!=` operatore produce lo stesso risultato di `^` operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2158">When the operands are of type `bool`, the `!=` operator produces the same result as the `^` operator.</span></span>

### <a name="enumeration-comparison-operators"></a><span data-ttu-id="8624e-2159">Operatori di confronto di enumerazione</span><span class="sxs-lookup"><span data-stu-id="8624e-2159">Enumeration comparison operators</span></span>

<span data-ttu-id="8624e-2160">Ogni tipo di enumerazione in modo implicito fornisce gli operatori di confronto predefinite seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-2160">Every enumeration type implicitly provides the following predefined comparison operators:</span></span>
```csharp
bool operator ==(E x, E y);
bool operator !=(E x, E y);
bool operator <(E x, E y);
bool operator >(E x, E y);
bool operator <=(E x, E y);
bool operator >=(E x, E y);
```

<span data-ttu-id="8624e-2161">Il risultato della valutazione `x op y`, dove `x` e `y` sono espressioni di un tipo di enumerazione `E` con un tipo sottostante `U`, e `op` è uno degli operatori di confronto, è esattamente identico la valutazione `((U)x) op ((U)y)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2161">The result of evaluating `x op y`, where `x` and `y` are expressions of an enumeration type `E` with an underlying type `U`, and `op` is one of the comparison operators, is exactly the same as evaluating `((U)x) op ((U)y)`.</span></span> <span data-ttu-id="8624e-2162">In altre parole, gli operatori di confronto di tipo enumerazione semplicemente confrontano i valori integrali sottostanti tra i due operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-2162">In other words, the enumeration type comparison operators simply compare the underlying integral values of the two operands.</span></span>

### <a name="reference-type-equality-operators"></a><span data-ttu-id="8624e-2163">Operatori di uguaglianza di tipo riferimento</span><span class="sxs-lookup"><span data-stu-id="8624e-2163">Reference type equality operators</span></span>

<span data-ttu-id="8624e-2164">Gli operatori di uguaglianza del tipo riferimento predefiniti sono:</span><span class="sxs-lookup"><span data-stu-id="8624e-2164">The predefined reference type equality operators are:</span></span>
```csharp
bool operator ==(object x, object y);
bool operator !=(object x, object y);
```

<span data-ttu-id="8624e-2165">Gli operatori restituiscono il risultato del confronto tra i due riferimenti per verificarne l'uguaglianza o disuguaglianza.</span><span class="sxs-lookup"><span data-stu-id="8624e-2165">The operators return the result of comparing the two references for equality or non-equality.</span></span>

<span data-ttu-id="8624e-2166">Poiché gli operatori di uguaglianza di riferimenti predefiniti tipo accettano gli operandi di tipo `object`, si applicano a tutti i tipi che non dichiarano applicabili `operator ==` e `operator !=` membri.</span><span class="sxs-lookup"><span data-stu-id="8624e-2166">Since the predefined reference type equality operators accept operands of type `object`, they apply to all types that do not declare applicable `operator ==` and `operator !=` members.</span></span> <span data-ttu-id="8624e-2167">Al contrario, qualsiasi operatore di uguaglianza definito dall'utente applicabile nascondono in modo efficace agli operatori di uguaglianza del tipo riferimento predefiniti.</span><span class="sxs-lookup"><span data-stu-id="8624e-2167">Conversely, any applicable user-defined equality operators effectively hide the predefined reference type equality operators.</span></span>

<span data-ttu-id="8624e-2168">Gli operatori di uguaglianza del tipo riferimento predefiniti richiedono uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-2168">The predefined reference type equality operators require one of the following:</span></span>

*  <span data-ttu-id="8624e-2169">Entrambi gli operandi sono un valore di un tipo noto per essere un' *reference_type* o il valore letterale `null`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2169">Both operands are a value of a type known to be a *reference_type* or the literal `null`.</span></span> <span data-ttu-id="8624e-2170">Inoltre, una conversione esplicita di riferimenti ([le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)) esistente dal tipo degli operandi nel tipo di altro operando.</span><span class="sxs-lookup"><span data-stu-id="8624e-2170">Furthermore, an explicit reference conversion ([Explicit reference conversions](conversions.md#explicit-reference-conversions)) exists from the type of either operand to the type of the other operand.</span></span>
*  <span data-ttu-id="8624e-2171">Un operando è un valore di tipo `T` in cui `T` è un *type_parameter* e l'altro operando è il valore letterale `null`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2171">One operand is a value of type `T` where `T` is a *type_parameter* and the other operand is the literal `null`.</span></span> <span data-ttu-id="8624e-2172">Inoltre `T` non ha il vincolo di tipo valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2172">Furthermore `T` does not have the value type constraint.</span></span>

<span data-ttu-id="8624e-2173">A meno che una delle seguenti condizioni sono vere, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2173">Unless one of these conditions are true, a binding-time error occurs.</span></span> <span data-ttu-id="8624e-2174">Le implicazioni principali di queste regole sono:</span><span class="sxs-lookup"><span data-stu-id="8624e-2174">Notable implications of these rules are:</span></span>

*  <span data-ttu-id="8624e-2175">È un errore in fase di associazione da utilizzare gli operatori di uguaglianza del tipo riferimento predefinito per confrontare due riferimenti che risultano diversi in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2175">It is a binding-time error to use the predefined reference type equality operators to compare two references that are known to be different at binding-time.</span></span> <span data-ttu-id="8624e-2176">Ad esempio, se i tipi in fase di associazione degli operandi sono due tipi di classe `A` e `B`e se nessuno di essi `A` né `B` deriva da un altro, quindi sarebbe impossibile ritestare per i due operandi a cui fare riferimento allo stesso oggetto.</span><span class="sxs-lookup"><span data-stu-id="8624e-2176">For example, if the binding-time types of the operands are two class types `A` and `B`, and if neither `A` nor `B` derives from the other, then it would be impossible for the two operands to reference the same object.</span></span> <span data-ttu-id="8624e-2177">Di conseguenza, l'operazione è considerato un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2177">Thus, the operation is considered a binding-time error.</span></span>
*  <span data-ttu-id="8624e-2178">Gli operatori di uguaglianza del tipo riferimento predefiniti non consentono la valore operandi di tipo da confrontare.</span><span class="sxs-lookup"><span data-stu-id="8624e-2178">The predefined reference type equality operators do not permit value type operands to be compared.</span></span> <span data-ttu-id="8624e-2179">Pertanto, a meno che un tipo struct dichiara i propri operatori di uguaglianza, non è possibile confrontare i valori di tale tipo struct.</span><span class="sxs-lookup"><span data-stu-id="8624e-2179">Therefore, unless a struct type declares its own equality operators, it is not possible to compare values of that struct type.</span></span>
*  <span data-ttu-id="8624e-2180">Gli operatori di uguaglianza del tipo riferimento predefiniti non generano mai operazioni di conversione boxing si verificano per i relativi operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-2180">The predefined reference type equality operators never cause boxing operations to occur for their operands.</span></span> <span data-ttu-id="8624e-2181">Sarebbe inutile per eseguire tali operazioni di conversione boxing, poiché i riferimenti alle istanze di boxed appena allocate necessariamente differiscono da tutti gli altri riferimenti.</span><span class="sxs-lookup"><span data-stu-id="8624e-2181">It would be meaningless to perform such boxing operations, since references to the newly allocated boxed instances would necessarily differ from all other references.</span></span>
*  <span data-ttu-id="8624e-2182">Se un operando di un parametro di tipo `T` viene confrontato con `null`e il tipo di runtime del `T` è un tipo di valore, il risultato del confronto è `false`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2182">If an operand of a type parameter type `T` is compared to `null`, and the run-time type of `T` is a value type, the result of the comparison is `false`.</span></span>

<span data-ttu-id="8624e-2183">L'esempio seguente controlla se un argomento di un parametro di tipo senza vincoli è `null`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2183">The following example checks whether an argument of an unconstrained type parameter type is `null`.</span></span>
```csharp
class C<T>
{
    void F(T x) {
        if (x == null) throw new ArgumentNullException();
        ...
    }
}
```

<span data-ttu-id="8624e-2184">Il `x == null` costrutto è consentito anche se `T` può rappresentare un tipo di valore e il risultato è definito semplicemente `false` quando `T` è un tipo valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2184">The `x == null` construct is permitted even though `T` could represent a value type, and the result is simply defined to be `false` when `T` is a value type.</span></span>

<span data-ttu-id="8624e-2185">Per un'operazione del form `x == y` oppure `x != y`, se a eventuali `operator ==` oppure `operator !=` esiste, la risoluzione dell'overload di operatore ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) selezioneranno le regole che operatore anziché l'operatore di uguaglianza di tipo riferimento predefiniti.</span><span class="sxs-lookup"><span data-stu-id="8624e-2185">For an operation of the form `x == y` or `x != y`, if any applicable `operator ==` or `operator !=` exists, the operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) rules will select that operator instead of the predefined reference type equality operator.</span></span> <span data-ttu-id="8624e-2186">Tuttavia, è sempre possibile selezionare l'operatore di uguaglianza di riferimenti predefiniti tipo eseguendo il cast esplicito di uno o entrambi gli operandi nel tipo `object`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2186">However, it is always possible to select the predefined reference type equality operator by explicitly casting one or both of the operands to type `object`.</span></span> <span data-ttu-id="8624e-2187">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2187">The example</span></span>
```csharp
using System;

class Test
{
    static void Main() {
        string s = "Test";
        string t = string.Copy(s);
        Console.WriteLine(s == t);
        Console.WriteLine((object)s == t);
        Console.WriteLine(s == (object)t);
        Console.WriteLine((object)s == (object)t);
    }
}
```
<span data-ttu-id="8624e-2188">produce l'output</span><span class="sxs-lookup"><span data-stu-id="8624e-2188">produces the output</span></span>
```
True
False
False
False
```

<span data-ttu-id="8624e-2189">Il `s` e `t` variabili fanno riferimento a due distinti `string` istanze che contiene gli stessi caratteri.</span><span class="sxs-lookup"><span data-stu-id="8624e-2189">The `s` and `t` variables refer to two distinct `string` instances containing the same characters.</span></span> <span data-ttu-id="8624e-2190">Gli output del primo confronto `True` perché l'operatore di uguaglianza di stringa predefinita ([operatori di uguaglianza delle stringhe](expressions.md#string-equality-operators)) sia selezionata quando entrambi gli operandi sono di tipo `string`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2190">The first comparison outputs `True` because the predefined string equality operator ([String equality operators](expressions.md#string-equality-operators)) is selected when both operands are of type `string`.</span></span> <span data-ttu-id="8624e-2191">Gli altri confronti tutti di output `False` perché l'operatore di uguaglianza di tipo riferimento predefiniti viene selezionata quando uno o entrambi gli operandi sono di tipo `object`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2191">The remaining comparisons all output `False` because the predefined reference type equality operator is selected when one or both of the operands are of type `object`.</span></span>

<span data-ttu-id="8624e-2192">Si noti che la tecnica precedente non è significativa per i tipi di valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2192">Note that the above technique is not meaningful for value types.</span></span> <span data-ttu-id="8624e-2193">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2193">The example</span></span>
```csharp
class Test
{
    static void Main() {
        int i = 123;
        int j = 123;
        System.Console.WriteLine((object)i == (object)j);
    }
}
```
<span data-ttu-id="8624e-2194">emette `False` perché i cast creano riferimenti a due istanze separate di tipo boxed `int` valori.</span><span class="sxs-lookup"><span data-stu-id="8624e-2194">outputs `False` because the casts create references to two separate instances of boxed `int` values.</span></span>

### <a name="string-equality-operators"></a><span data-ttu-id="8624e-2195">Operatori di uguaglianza delle stringhe</span><span class="sxs-lookup"><span data-stu-id="8624e-2195">String equality operators</span></span>

<span data-ttu-id="8624e-2196">Gli operatori di uguaglianza stringa predefinite sono:</span><span class="sxs-lookup"><span data-stu-id="8624e-2196">The predefined string equality operators are:</span></span>
```csharp
bool operator ==(string x, string y);
bool operator !=(string x, string y);
```

<span data-ttu-id="8624e-2197">Due `string` valori vengono considerati uguali quando viene soddisfatta una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-2197">Two `string` values are considered equal when one of the following is true:</span></span>

*  <span data-ttu-id="8624e-2198">Entrambi i valori sono `null`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2198">Both values are `null`.</span></span>
*  <span data-ttu-id="8624e-2199">Entrambi i valori sono i riferimenti non null per le istanze di stringa contenenti caratteri identici e lunghezze identiche in ogni posizione del carattere.</span><span class="sxs-lookup"><span data-stu-id="8624e-2199">Both values are non-null references to string instances that have identical lengths and identical characters in each character position.</span></span>

<span data-ttu-id="8624e-2200">Gli operatori di uguaglianza di stringa confrontano i valori stringa anziché riferimenti di stringa.</span><span class="sxs-lookup"><span data-stu-id="8624e-2200">The string equality operators compare string values rather than string references.</span></span> <span data-ttu-id="8624e-2201">Quando due istanze di stringhe separate contengono la stessa identica sequenza di caratteri, i valori delle stringhe sono uguali, ma i riferimenti sono diversi.</span><span class="sxs-lookup"><span data-stu-id="8624e-2201">When two separate string instances contain the exact same sequence of characters, the values of the strings are equal, but the references are different.</span></span> <span data-ttu-id="8624e-2202">Come descritto in [gli operatori di uguaglianza di riferimenti tipo](expressions.md#reference-type-equality-operators), degli operatori di uguaglianza del tipo riferimento possono essere utilizzati per confrontare i riferimenti a stringhe anziché i valori stringa.</span><span class="sxs-lookup"><span data-stu-id="8624e-2202">As described in [Reference type equality operators](expressions.md#reference-type-equality-operators), the reference type equality operators can be used to compare string references instead of string values.</span></span>

### <a name="delegate-equality-operators"></a><span data-ttu-id="8624e-2203">Delegare gli operatori di uguaglianza</span><span class="sxs-lookup"><span data-stu-id="8624e-2203">Delegate equality operators</span></span>

<span data-ttu-id="8624e-2204">Ogni tipo di delegato in modo implicito fornisce gli operatori di confronto predefinite seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-2204">Every delegate type implicitly provides the following predefined comparison operators:</span></span>

```csharp
bool operator ==(System.Delegate x, System.Delegate y);
bool operator !=(System.Delegate x, System.Delegate y);
```

<span data-ttu-id="8624e-2205">Delegato di due istanze sono uguali come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-2205">Two delegate instances are considered equal as follows:</span></span>

*  <span data-ttu-id="8624e-2206">Se si verifica una delle istanze del delegato `null`, sono uguali solo se entrambi sono `null`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2206">If either of the delegate instances is `null`, they are equal if and only if both are `null`.</span></span>
*  <span data-ttu-id="8624e-2207">Se i delegati dispongono di diversi tipi di runtime non sono uguali.</span><span class="sxs-lookup"><span data-stu-id="8624e-2207">If the delegates have different run-time type they are never equal.</span></span>
*  <span data-ttu-id="8624e-2208">Se le istanze dei delegati dispongono di un elenco chiamate ([dichiarazioni Delegate](delegates.md#delegate-declarations)), tali istanze sono uguali solo se gli elenchi sono la stessa lunghezza e ogni voce nell'elenco di chiamate è uguale (come definita di seguito) per la voce corrispondente, in ordine, nell'elenco chiamate di altro.</span><span class="sxs-lookup"><span data-stu-id="8624e-2208">If both of the delegate instances have an invocation list ([Delegate declarations](delegates.md#delegate-declarations)), those instances are equal if and only if their invocation lists are the same length, and each entry in one's invocation list is equal (as defined below) to the corresponding entry, in order, in the other's invocation list.</span></span>

<span data-ttu-id="8624e-2209">Le regole seguenti determinano l'uguaglianza tra le voci dell'elenco chiamate:</span><span class="sxs-lookup"><span data-stu-id="8624e-2209">The following rules govern the equality of invocation list entries:</span></span>

*  <span data-ttu-id="8624e-2210">Se due voci elenco di chiamate fanno riferimento allo stesso oggetto statico (metodo), le voci sono uguali.</span><span class="sxs-lookup"><span data-stu-id="8624e-2210">If two invocation list entries both refer to the same static method then the entries are equal.</span></span>
*  <span data-ttu-id="8624e-2211">Se due voci elenco di chiamate fanno riferimento allo stesso metodo non statico sullo stesso oggetto di destinazione (come definito per gli operatori di uguaglianza di riferimento) le voci sono uguali.</span><span class="sxs-lookup"><span data-stu-id="8624e-2211">If two invocation list entries both refer to the same non-static method on the same target object (as defined by the reference equality operators) then the entries are equal.</span></span>
*  <span data-ttu-id="8624e-2212">Le voci dell'elenco chiamate prodotte dalla valutazione di semanticamente identico *anonymous_method_expression*s oppure *lambda_expression*s con lo stesso set (eventualmente vuoto) della variabile esterna acquisita le istanze sono consentite (ma non necessari) sia uguale.</span><span class="sxs-lookup"><span data-stu-id="8624e-2212">Invocation list entries produced from evaluation of semantically identical *anonymous_method_expression*s or *lambda_expression*s with the same (possibly empty) set of captured outer variable instances are permitted (but not required) to be equal.</span></span>

### <a name="equality-operators-and-null"></a><span data-ttu-id="8624e-2213">Null e gli operatori di uguaglianza</span><span class="sxs-lookup"><span data-stu-id="8624e-2213">Equality operators and null</span></span>

<span data-ttu-id="8624e-2214">Il `==` e `!=` operatori consentono un operando sia un valore di un tipo che ammette valori null e l'altro sia il `null` letterale, anche se non esiste alcun operatore predefinito o definiti dall'utente (in forma unlifted o form è elevato) per l'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2214">The `==` and `!=` operators permit one operand to be a value of a nullable type and the other to be the `null` literal, even if no predefined or user-defined operator (in unlifted or lifted form) exists for the operation.</span></span>

<span data-ttu-id="8624e-2215">Per un'operazione con uno dei formati</span><span class="sxs-lookup"><span data-stu-id="8624e-2215">For an operation of one of the forms</span></span>
```csharp
x == null
null == x
x != null
null != x
```
<span data-ttu-id="8624e-2216">in cui `x` è un'espressione di un tipo che ammette valori null, se la risoluzione dell'overload di operatore ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) non riesce a trovare un operatore applicabile, il risultato viene calcolato nel `HasValue` proprietà di `x`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2216">where `x` is an expression of a nullable type, if operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) fails to find an applicable operator, the result is instead computed from the `HasValue` property of `x`.</span></span> <span data-ttu-id="8624e-2217">In particolare, le prime due forme sono tradotti `!x.HasValue`, e ultimi due formati vengono convertite in `x.HasValue`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2217">Specifically, the first two forms are translated into `!x.HasValue`, and last two forms are translated into `x.HasValue`.</span></span>

### <a name="the-is-operator"></a><span data-ttu-id="8624e-2218">L'operatore is</span><span class="sxs-lookup"><span data-stu-id="8624e-2218">The is operator</span></span>

<span data-ttu-id="8624e-2219">Il `is` operatore viene usato per controllare in modo dinamico se il tipo di fase di esecuzione di un oggetto è compatibile con un tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="8624e-2219">The `is` operator is used to dynamically check if the run-time type of an object is compatible with a given type.</span></span> <span data-ttu-id="8624e-2220">Il risultato dell'operazione `E is T`, dove `E` è un'espressione e `T` è un tipo, è un valore booleano valore che indica se `E` correttamente può essere convertito nel tipo `T` tramite una conversione di riferimenti, una conversione boxing conversione, o una conversione unboxing.</span><span class="sxs-lookup"><span data-stu-id="8624e-2220">The result of the operation `E is T`, where `E` is an expression and `T` is a type, is a boolean value indicating whether `E` can successfully be converted to type `T` by a reference conversion, a boxing conversion, or an unboxing conversion.</span></span> <span data-ttu-id="8624e-2221">L'operazione viene valutato come indicato di seguito, dopo che sono stati sostituiti da argomenti di tipo per tutti i parametri di tipo:</span><span class="sxs-lookup"><span data-stu-id="8624e-2221">The operation is evaluated as follows, after type arguments have been substituted for all type parameters:</span></span>

*  <span data-ttu-id="8624e-2222">Se `E` è una funzione anonima, si verifica un errore in fase di compilazione</span><span class="sxs-lookup"><span data-stu-id="8624e-2222">If `E` is an anonymous function, a compile-time error occurs</span></span>
*  <span data-ttu-id="8624e-2223">Se `E` è un gruppo di metodi o le `null` letterale, se il tipo di `E` è un tipo riferimento o un tipo nullable e il valore di `E` è null, il risultato è false.</span><span class="sxs-lookup"><span data-stu-id="8624e-2223">If `E` is a method group or the `null` literal, of if the type of `E` is a reference type or a nullable type and the value of `E` is null, the result is false.</span></span>
*  <span data-ttu-id="8624e-2224">In caso contrario, lasciare `D` rappresenta il tipo dinamico di `E` come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-2224">Otherwise, let `D` represent the dynamic type of `E` as follows:</span></span>
   * <span data-ttu-id="8624e-2225">Se il tipo della `E` è un tipo di riferimento `D` è il tipo di runtime del riferimento all'istanza da `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2225">If the type of `E` is a reference type, `D` is the run-time type of the instance reference by `E`.</span></span>
   * <span data-ttu-id="8624e-2226">Se il tipo della `E` è un tipo nullable, `D` è il tipo sottostante del tipo nullable.</span><span class="sxs-lookup"><span data-stu-id="8624e-2226">If the type of `E` is a nullable type, `D` is the underlying type of that nullable type.</span></span>
   * <span data-ttu-id="8624e-2227">Se il tipo della `E` è un tipo, valore non nullable `D` è il tipo di `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2227">If the type of `E` is a non-nullable value type, `D` is the type of `E`.</span></span>
*  <span data-ttu-id="8624e-2228">Il risultato dell'operazione dipende `D` e `T` come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-2228">The result of the operation depends on `D` and `T` as follows:</span></span>
   * <span data-ttu-id="8624e-2229">Se `T` è un tipo riferimento, il risultato è true se `D` e `T` presentano lo stesso tipo, se `D` è un tipo di riferimento e una conversione implicita del riferimento da `D` alla `T` esiste, oppure se `D` è un tipo di valore e dalla conversione boxing `D` a `T` esiste.</span><span class="sxs-lookup"><span data-stu-id="8624e-2229">If `T` is a reference type, the result is true if `D` and `T` are the same type, if `D` is a reference type and an implicit reference conversion from `D` to `T` exists, or if `D` is a value type and a boxing conversion from `D` to `T` exists.</span></span>
   * <span data-ttu-id="8624e-2230">Se `T` è un tipo che ammette valori null, il risultato è true se `D` è il tipo sottostante di `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2230">If `T` is a nullable type, the result is true if `D` is the underlying type of `T`.</span></span>
   * <span data-ttu-id="8624e-2231">Se `T` è un tipo di valore non nullable, il risultato è true se `D` e `T` sono dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2231">If `T` is a non-nullable value type, the result is true if `D` and `T` are the same type.</span></span>
   * <span data-ttu-id="8624e-2232">In caso contrario, il risultato è false.</span><span class="sxs-lookup"><span data-stu-id="8624e-2232">Otherwise, the result is false.</span></span>

<span data-ttu-id="8624e-2233">Si noti che le conversioni definite dall'utente, non vengono considerate dal `is` operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2233">Note that user defined conversions, are not considered by the `is` operator.</span></span>

### <a name="the-as-operator"></a><span data-ttu-id="8624e-2234">L'operatore</span><span class="sxs-lookup"><span data-stu-id="8624e-2234">The as operator</span></span>

<span data-ttu-id="8624e-2235">Il `as` operatore viene usato per convertire in modo esplicito un valore in un determinato tipo di riferimento o nullable.</span><span class="sxs-lookup"><span data-stu-id="8624e-2235">The `as` operator is used to explicitly convert a value to a given reference type or nullable type.</span></span> <span data-ttu-id="8624e-2236">A differenza di un'espressione cast ([espressioni Cast](expressions.md#cast-expressions)), il `as` operatore non genera mai un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2236">Unlike a cast expression ([Cast expressions](expressions.md#cast-expressions)), the `as` operator never throws an exception.</span></span> <span data-ttu-id="8624e-2237">In alternativa, se la conversione indicata non è possibile, il valore risultante è `null`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2237">Instead, if the indicated conversion is not possible, the resulting value is `null`.</span></span>

<span data-ttu-id="8624e-2238">In un'operazione del form `E as T`, `E` deve essere un'espressione e `T` deve essere un tipo riferimento, un parametro di tipo noto per essere un tipo riferimento o un tipo nullable.</span><span class="sxs-lookup"><span data-stu-id="8624e-2238">In an operation of the form `E as T`, `E` must be an expression and `T` must be a reference type, a type parameter known to be a reference type, or a nullable type.</span></span> <span data-ttu-id="8624e-2239">Inoltre, almeno uno dei seguenti deve essere true o in caso contrario, verrà generato un errore in fase di compilazione:</span><span class="sxs-lookup"><span data-stu-id="8624e-2239">Furthermore, at least one of the following must be true, or otherwise a compile-time error occurs:</span></span>

*  <span data-ttu-id="8624e-2240">Un'identità ([conversione di identità](conversions.md#identity-conversion)), implicita che ammette valori null ([conversioni implicite nullable](conversions.md#implicit-nullable-conversions)), implicita del riferimento ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)), conversione boxing ([ Le conversioni boxing](conversions.md#boxing-conversions)), esplicite che ammette valori null ([conversioni esplicite nullable](conversions.md#explicit-nullable-conversions)), riferimento esplicito ([le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)), o unboxing ([Conversioni di Unboxing](conversions.md#unboxing-conversions)) non esiste conversione da `E` a `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2240">An identity ([Identity conversion](conversions.md#identity-conversion)), implicit nullable ([Implicit nullable conversions](conversions.md#implicit-nullable-conversions)), implicit reference ([Implicit reference conversions](conversions.md#implicit-reference-conversions)), boxing ([Boxing conversions](conversions.md#boxing-conversions)), explicit nullable ([Explicit nullable conversions](conversions.md#explicit-nullable-conversions)), explicit reference ([Explicit reference conversions](conversions.md#explicit-reference-conversions)), or unboxing ([Unboxing conversions](conversions.md#unboxing-conversions)) conversion exists from `E` to `T`.</span></span>
*  <span data-ttu-id="8624e-2241">Il tipo della `E` o `T` è un tipo aperto.</span><span class="sxs-lookup"><span data-stu-id="8624e-2241">The type of `E` or `T` is an open type.</span></span>
*  <span data-ttu-id="8624e-2242">`E` è il `null` letterale.</span><span class="sxs-lookup"><span data-stu-id="8624e-2242">`E` is the `null` literal.</span></span>

<span data-ttu-id="8624e-2243">Se il tipo di fase di compilazione del `E` non è `dynamic`, l'operazione `E as T` produce lo stesso risultato</span><span class="sxs-lookup"><span data-stu-id="8624e-2243">If the compile-time type of `E` is not `dynamic`, the operation `E as T` produces the same result as</span></span>
```csharp
E is T ? (T)(E) : (T)null
```
<span data-ttu-id="8624e-2244">con la differenza che `E` viene valutato una sola volta.</span><span class="sxs-lookup"><span data-stu-id="8624e-2244">except that `E` is only evaluated once.</span></span> <span data-ttu-id="8624e-2245">Il compilatore può essere previsto a ottimizzare `E as T` eseguire al massimo un controllo di tipo dinamico anziché i due controlli di tipo dinamico in cui è inclusa l'espansione del precedente.</span><span class="sxs-lookup"><span data-stu-id="8624e-2245">The compiler can be expected to optimize `E as T` to perform at most one dynamic type check as opposed to the two dynamic type checks implied by the expansion above.</span></span>

<span data-ttu-id="8624e-2246">Se il tipo di fase di compilazione del `E` viene `dynamic`, a differenza dell'operatore di cast il `as` operatore non è associato in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2246">If the compile-time type of `E` is `dynamic`, unlike the cast operator the `as` operator is not dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="8624e-2247">Di conseguenza l'espansione in questo caso è:</span><span class="sxs-lookup"><span data-stu-id="8624e-2247">Therefore the expansion in this case is:</span></span>
```csharp
E is T ? (T)(object)(E) : (T)null
```

<span data-ttu-id="8624e-2248">Si noti che alcune conversioni, ad esempio le conversioni definite dall'utente, non sono possibili con la `as` operatore e devono invece essere eseguite usando le espressioni cast.</span><span class="sxs-lookup"><span data-stu-id="8624e-2248">Note that some conversions, such as user defined conversions, are not possible with the `as` operator and should instead be performed using cast expressions.</span></span>

<span data-ttu-id="8624e-2249">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2249">In the example</span></span>
```csharp
class X
{

    public string F(object o) {
        return o as string;        // OK, string is a reference type
    }

    public T G<T>(object o) where T: Attribute {
        return o as T;             // Ok, T has a class constraint
    }

    public U H<U>(object o) {
        return o as U;             // Error, U is unconstrained 
    }
}
```
<span data-ttu-id="8624e-2250">il parametro di tipo `T` di `G` è noto per essere un tipo riferimento, perché contiene il vincolo di classe.</span><span class="sxs-lookup"><span data-stu-id="8624e-2250">the type parameter `T` of `G` is known to be a reference type, because it has the class constraint.</span></span> <span data-ttu-id="8624e-2251">Il parametro di tipo `U` dei `H` non è tuttavia; di conseguenza l'utilizzo delle `as` operatore in `H` non è consentita.</span><span class="sxs-lookup"><span data-stu-id="8624e-2251">The type parameter `U` of `H` is not however; hence the use of the `as` operator in `H` is disallowed.</span></span>

## <a name="logical-operators"></a><span data-ttu-id="8624e-2252">Operatori logici</span><span class="sxs-lookup"><span data-stu-id="8624e-2252">Logical operators</span></span>

<span data-ttu-id="8624e-2253">Il `&`, `^`, e `|` gli operatori sono denominati operatori logici.</span><span class="sxs-lookup"><span data-stu-id="8624e-2253">The `&`, `^`, and `|` operators are called the logical operators.</span></span>

```antlr
and_expression
    : equality_expression
    | and_expression '&' equality_expression
    ;

exclusive_or_expression
    : and_expression
    | exclusive_or_expression '^' and_expression
    ;

inclusive_or_expression
    : exclusive_or_expression
    | inclusive_or_expression '|' exclusive_or_expression
    ;
```

<span data-ttu-id="8624e-2254">Se un operando di un operatore logico con il tipo di fase di compilazione `dynamic`, quindi in modo dinamico è associata l'espressione ([binding dinamico](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2254">If an operand of a logical operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="8624e-2255">In questo caso è il tipo di fase di compilazione dell'espressione `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di fase di esecuzione di tali operandi che presentano il tipo di fase di compilazione `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2255">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="8624e-2256">Per un'operazione del form `x op y`, dove `op` è uno degli operatori logici, la risoluzione dell'overload ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) viene applicato per selezionare un'implementazione di un operatore specifico.</span><span class="sxs-lookup"><span data-stu-id="8624e-2256">For an operation of the form `x op y`, where `op` is one of the logical operators, overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="8624e-2257">Gli operandi vengono convertiti nei tipi di parametro dell'operatore selezionato e il tipo del risultato è il tipo restituito dell'operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2257">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="8624e-2258">Gli operatori logici predefiniti sono descritte nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="8624e-2258">The predefined logical operators are described in the following sections.</span></span>

### <a name="integer-logical-operators"></a><span data-ttu-id="8624e-2259">Operatori logici di integer</span><span class="sxs-lookup"><span data-stu-id="8624e-2259">Integer logical operators</span></span>

<span data-ttu-id="8624e-2260">Gli operatori logici di integer predefiniti sono:</span><span class="sxs-lookup"><span data-stu-id="8624e-2260">The predefined integer logical operators are:</span></span>
```csharp
int operator &(int x, int y);
uint operator &(uint x, uint y);
long operator &(long x, long y);
ulong operator &(ulong x, ulong y);

int operator |(int x, int y);
uint operator |(uint x, uint y);
long operator |(long x, long y);
ulong operator |(ulong x, ulong y);

int operator ^(int x, int y);
uint operator ^(uint x, uint y);
long operator ^(long x, long y);
ulong operator ^(ulong x, ulong y);
```

<span data-ttu-id="8624e-2261">Il `&` operatore calcola bit per bit logico `AND` tra i due operandi, il `|` operatore calcola bit per bit logico `OR` dei due operandi e il `^` operatore calcola esclusivo logico bit per bit `OR` dei due operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-2261">The `&` operator computes the bitwise logical `AND` of the two operands, the `|` operator computes the bitwise logical `OR` of the two operands, and the `^` operator computes the bitwise logical exclusive `OR` of the two operands.</span></span> <span data-ttu-id="8624e-2262">Nessun overflow possono essere eseguite da queste operazioni.</span><span class="sxs-lookup"><span data-stu-id="8624e-2262">No overflows are possible from these operations.</span></span>

### <a name="enumeration-logical-operators"></a><span data-ttu-id="8624e-2263">Operatori logici di enumerazione</span><span class="sxs-lookup"><span data-stu-id="8624e-2263">Enumeration logical operators</span></span>

<span data-ttu-id="8624e-2264">Ogni tipo di enumerazione `E` in modo implicito offre operatori logici predefiniti di quanto segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-2264">Every enumeration type `E` implicitly provides the following predefined logical operators:</span></span>

```csharp
E operator &(E x, E y);
E operator |(E x, E y);
E operator ^(E x, E y);
```

<span data-ttu-id="8624e-2265">Il risultato della valutazione `x op y`, dove `x` e `y` sono espressioni di un tipo di enumerazione `E` con un tipo sottostante `U`, e `op` è uno degli operatori logici, è esattamente identico la valutazione `(E)((U)x op (U)y)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2265">The result of evaluating `x op y`, where `x` and `y` are expressions of an enumeration type `E` with an underlying type `U`, and `op` is one of the logical operators, is exactly the same as evaluating `(E)((U)x op (U)y)`.</span></span> <span data-ttu-id="8624e-2266">In altre parole, gli operatori logici di tipo di enumerazione è sufficiente eseguono l'operazione logica del tipo sottostante di due operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-2266">In other words, the enumeration type logical operators simply perform the logical operation on the underlying type of the two operands.</span></span>

### <a name="boolean-logical-operators"></a><span data-ttu-id="8624e-2267">Operatore logico booleano</span><span class="sxs-lookup"><span data-stu-id="8624e-2267">Boolean logical operators</span></span>

<span data-ttu-id="8624e-2268">L'operatore logico booleano sono:</span><span class="sxs-lookup"><span data-stu-id="8624e-2268">The predefined boolean logical operators are:</span></span>
```csharp
bool operator &(bool x, bool y);
bool operator |(bool x, bool y);
bool operator ^(bool x, bool y);
```

<span data-ttu-id="8624e-2269">Il risultato di `x & y` è `true` se `x` e `y` sono `true`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2269">The result of `x & y` is `true` if both `x` and `y` are `true`.</span></span> <span data-ttu-id="8624e-2270">In caso contrario, il risultato è `false`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2270">Otherwise, the result is `false`.</span></span>

<span data-ttu-id="8624e-2271">Il risultato del `x | y` viene `true` se uno dei due `x` oppure `y` è `true`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2271">The result of `x | y` is `true` if either `x` or `y` is `true`.</span></span> <span data-ttu-id="8624e-2272">In caso contrario, il risultato è `false`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2272">Otherwise, the result is `false`.</span></span>

<span data-ttu-id="8624e-2273">Il risultato di `x ^ y` viene `true` se `x` viene `true` e `y` viene `false`, oppure `x` è `false` e `y` è `true`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2273">The result of `x ^ y` is `true` if `x` is `true` and `y` is `false`, or `x` is `false` and `y` is `true`.</span></span> <span data-ttu-id="8624e-2274">In caso contrario, il risultato è `false`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2274">Otherwise, the result is `false`.</span></span> <span data-ttu-id="8624e-2275">Quando gli operandi sono di tipo `bool`, il `^` operatore calcola lo stesso risultato di `!=` operatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2275">When the operands are of type `bool`, the `^` operator computes the same result as the `!=` operator.</span></span>

### <a name="nullable-boolean-logical-operators"></a><span data-ttu-id="8624e-2276">Operatori logici booleani che ammette valori null</span><span class="sxs-lookup"><span data-stu-id="8624e-2276">Nullable boolean logical operators</span></span>

<span data-ttu-id="8624e-2277">Il tipo di valore boolean nullable `bool?` può rappresentare valori di tre `true`, `false`, e `null`ed è concettualmente simile al tipo a tre valori utilizzato per le espressioni booleane in SQL.</span><span class="sxs-lookup"><span data-stu-id="8624e-2277">The nullable boolean type `bool?` can represent three values, `true`, `false`, and `null`, and is conceptually similar to the three-valued type used for boolean expressions in SQL.</span></span> <span data-ttu-id="8624e-2278">Per garantire che i risultati prodotti dal `&` e `|` operatori per `bool?` gli operandi sono coerenti con la logica a tre valori di SQL, vengono forniti gli operatori predefiniti seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-2278">To ensure that the results produced by the `&` and `|` operators for `bool?` operands are consistent with SQL's three-valued logic, the following predefined operators are provided:</span></span>

```csharp
bool? operator &(bool? x, bool? y);
bool? operator |(bool? x, bool? y);
```

<span data-ttu-id="8624e-2279">Nella tabella seguente sono elencati i risultati prodotti da questi operatori per tutte le combinazioni dei valori `true`, `false`, e `null`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2279">The following table lists the results produced by these operators for all combinations of the values `true`, `false`, and `null`.</span></span>

| `x`     | `y`     | `x & y` | <code>x &#124; y</code> |
|:-------:|:-------:|:-------:|:-------:|
| `true`  | `true`  | `true`  | `true`  | 
| `true`  | `false` | `false` | `true`  | 
| `true`  | `null`  | `null`  | `true`  | 
| `false` | `true`  | `false` | `true`  | 
| `false` | `false` | `false` | `false` | 
| `false` | `null`  | `false` | `null`  | 
| `null`  | `true`  | `null`  | `true`  | 
| `null`  | `false` | `false` | `null`  | 
| `null`  | `null`  | `null`  | `null`  | 

## <a name="conditional-logical-operators"></a><span data-ttu-id="8624e-2280">Operatori logici condizionali</span><span class="sxs-lookup"><span data-stu-id="8624e-2280">Conditional logical operators</span></span>

<span data-ttu-id="8624e-2281">Il `&&` e `||` gli operatori sono denominati operatori logici condizionali.</span><span class="sxs-lookup"><span data-stu-id="8624e-2281">The `&&` and `||` operators are called the conditional logical operators.</span></span> <span data-ttu-id="8624e-2282">Vengono inoltre chiamati gli operatori logici "short circuii".</span><span class="sxs-lookup"><span data-stu-id="8624e-2282">They are also called the "short-circuiting" logical operators.</span></span>

```antlr
conditional_and_expression
    : inclusive_or_expression
    | conditional_and_expression '&&' inclusive_or_expression
    ;

conditional_or_expression
    : conditional_and_expression
    | conditional_or_expression '||' conditional_and_expression
    ;
```

<span data-ttu-id="8624e-2283">Il `&&` e `||` operatori sono versioni condizionale delle `&` e `|` operatori:</span><span class="sxs-lookup"><span data-stu-id="8624e-2283">The `&&` and `||` operators are conditional versions of the `&` and `|` operators:</span></span>

*  <span data-ttu-id="8624e-2284">L'operazione `x && y` corrisponde all'operazione `x & y`, ad eccezione del fatto che `y` viene valutato solo se `x` non `false`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2284">The operation `x && y` corresponds to the operation `x & y`, except that `y` is evaluated only if `x` is not `false`.</span></span>
*  <span data-ttu-id="8624e-2285">L'operazione `x || y` corrisponde all'operazione `x | y`, ad eccezione del fatto che `y` viene valutato solo se `x` non `true`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2285">The operation `x || y` corresponds to the operation `x | y`, except that `y` is evaluated only if `x` is not `true`.</span></span>

<span data-ttu-id="8624e-2286">Se un operando di un operatore logico condizionale ha il tipo di fase di compilazione `dynamic`, quindi in modo dinamico è associata l'espressione ([binding dinamico](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2286">If an operand of a conditional logical operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="8624e-2287">In questo caso è il tipo di fase di compilazione dell'espressione `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione usando il tipo di fase di esecuzione di tali operandi che presentano il tipo di fase di compilazione `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2287">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="8624e-2288">Un'operazione del form `x && y` oppure `x || y` avviene per la risoluzione dell'overload ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) come se l'operazione è stata scritta `x & y` o `x | y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2288">An operation of the form `x && y` or `x || y` is processed by applying overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) as if the operation was written `x & y` or `x | y`.</span></span> <span data-ttu-id="8624e-2289">Quindi,</span><span class="sxs-lookup"><span data-stu-id="8624e-2289">Then,</span></span>

*  <span data-ttu-id="8624e-2290">Se la risoluzione dell'overload non riesce a trovare un singolo operatore migliore o se la risoluzione dell'overload seleziona uno degli operatori logici integer predefiniti, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2290">If overload resolution fails to find a single best operator, or if overload resolution selects one of the predefined integer logical operators, a binding-time error occurs.</span></span>
*  <span data-ttu-id="8624e-2291">In caso contrario, se l'operatore selezionato è uno degli operatori logici booleani predefiniti ([booleani operatori logici](expressions.md#boolean-logical-operators)) o gli operatori logici booleani che ammette valori null ([operatori logici booleani Nullable](expressions.md#nullable-boolean-logical-operators)), il l'operazione viene elaborata come descritto in [booleani operatori logici condizionali](expressions.md#boolean-conditional-logical-operators).</span><span class="sxs-lookup"><span data-stu-id="8624e-2291">Otherwise, if the selected operator is one of the predefined boolean logical operators ([Boolean logical operators](expressions.md#boolean-logical-operators)) or nullable boolean logical operators ([Nullable boolean logical operators](expressions.md#nullable-boolean-logical-operators)), the operation is processed as described in [Boolean conditional logical operators](expressions.md#boolean-conditional-logical-operators).</span></span>
*  <span data-ttu-id="8624e-2292">In caso contrario, l'operatore selezionato è un operatore definito dall'utente e l'operazione viene elaborata come descritto in [definite dall'utente di operatori logici condizionali](expressions.md#user-defined-conditional-logical-operators).</span><span class="sxs-lookup"><span data-stu-id="8624e-2292">Otherwise, the selected operator is a user-defined operator, and the operation is processed as described in [User-defined conditional logical operators](expressions.md#user-defined-conditional-logical-operators).</span></span>

<span data-ttu-id="8624e-2293">Non è possibile eseguire direttamente l'overload degli operatori logici condizionali.</span><span class="sxs-lookup"><span data-stu-id="8624e-2293">It is not possible to directly overload the conditional logical operators.</span></span> <span data-ttu-id="8624e-2294">Tuttavia, poiché gli operatori logici condizionali vengono valutati in termini dei normali operatori logici, gli overload degli operatori logici comuni sono con alcune limitazioni, inoltre considerati overload degli operatori logici condizionali.</span><span class="sxs-lookup"><span data-stu-id="8624e-2294">However, because the conditional logical operators are evaluated in terms of the regular logical operators, overloads of the regular logical operators are, with certain restrictions, also considered overloads of the conditional logical operators.</span></span> <span data-ttu-id="8624e-2295">Questo è descritto più dettagliatamente in [definite dall'utente di operatori logici condizionali](expressions.md#user-defined-conditional-logical-operators).</span><span class="sxs-lookup"><span data-stu-id="8624e-2295">This is described further in [User-defined conditional logical operators](expressions.md#user-defined-conditional-logical-operators).</span></span>

### <a name="boolean-conditional-logical-operators"></a><span data-ttu-id="8624e-2296">Operatori logici condizionali booleani</span><span class="sxs-lookup"><span data-stu-id="8624e-2296">Boolean conditional logical operators</span></span>

<span data-ttu-id="8624e-2297">Quando gli operandi del `&&` o `||` sono di tipo `bool`, o quando gli operandi sono di tipi che non si definiscono dei `operator &` oppure `operator |`, ma definiscono le conversioni implicite a `bool`, l'operazione è elaborate come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-2297">When the operands of `&&` or `||` are of type `bool`, or when the operands are of types that do not define an applicable `operator &` or `operator |`, but do define implicit conversions to `bool`, the operation is processed as follows:</span></span>

*  <span data-ttu-id="8624e-2298">L'operazione `x && y` darà `x ? y : false`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2298">The operation `x && y` is evaluated as `x ? y : false`.</span></span> <span data-ttu-id="8624e-2299">In altre parole, `x` prima di tutto viene valutata e convertito nel tipo `bool`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2299">In other words, `x` is first evaluated and converted to type `bool`.</span></span> <span data-ttu-id="8624e-2300">Quindi, se `x` viene `true`, `y` viene valutata e convertito nel tipo `bool`, che diventa il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2300">Then, if `x` is `true`, `y` is evaluated and converted to type `bool`, and this becomes the result of the operation.</span></span> <span data-ttu-id="8624e-2301">In caso contrario, il risultato dell'operazione è `false`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2301">Otherwise, the result of the operation is `false`.</span></span>
*  <span data-ttu-id="8624e-2302">L'operazione `x || y` darà `x ? true : y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2302">The operation `x || y` is evaluated as `x ? true : y`.</span></span> <span data-ttu-id="8624e-2303">In altre parole, `x` prima di tutto viene valutata e convertito nel tipo `bool`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2303">In other words, `x` is first evaluated and converted to type `bool`.</span></span> <span data-ttu-id="8624e-2304">Quindi, se `x` viene `true`, il risultato dell'operazione è `true`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2304">Then, if `x` is `true`, the result of the operation is `true`.</span></span> <span data-ttu-id="8624e-2305">In caso contrario, `y` viene valutata e convertito nel tipo `bool`, che diventa il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2305">Otherwise, `y` is evaluated and converted to type `bool`, and this becomes the result of the operation.</span></span>

### <a name="user-defined-conditional-logical-operators"></a><span data-ttu-id="8624e-2306">Operatori logici condizionali definite dall'utente</span><span class="sxs-lookup"><span data-stu-id="8624e-2306">User-defined conditional logical operators</span></span>

<span data-ttu-id="8624e-2307">Quando gli operandi del `&&` oppure `||` siano di tipi che dichiarano un applicabili definite dall'utente `operator &` oppure `operator |`, entrambe le operazioni seguenti deve essere impostato su true, in cui `T` è il tipo in cui è dichiarata l'operatore selezionato:</span><span class="sxs-lookup"><span data-stu-id="8624e-2307">When the operands of `&&` or `||` are of types that declare an applicable user-defined `operator &` or `operator |`, both of the following must be true, where `T` is the type in which the selected operator is declared:</span></span>

*  <span data-ttu-id="8624e-2308">Il tipo restituito e il tipo di ogni parametro dell'operatore selezionato devono essere `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2308">The return type and the type of each parameter of the selected operator must be `T`.</span></span> <span data-ttu-id="8624e-2309">In altre parole, l'operatore deve calcolare logico `AND` o l'operatore logico `OR` dei due operandi di tipo `T`e deve restituire un risultato di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2309">In other words, the operator must compute the logical `AND` or the logical `OR` of two operands of type `T`, and must return a result of type `T`.</span></span>
*  <span data-ttu-id="8624e-2310">`T` deve contenere dichiarazioni di `operator true` e `operator false`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2310">`T` must contain declarations of `operator true` and `operator false`.</span></span>

<span data-ttu-id="8624e-2311">Se uno di questi requisiti non vengono soddisfatti, verrà generato un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2311">A binding-time error occurs if either of these requirements is not satisfied.</span></span> <span data-ttu-id="8624e-2312">In caso contrario, il `&&` oppure `||` combinando definite dall'utente viene valutata operazione `operator true` o `operator false` con l'operatore definito dall'utente selezionato:</span><span class="sxs-lookup"><span data-stu-id="8624e-2312">Otherwise, the `&&` or `||` operation is evaluated by combining the user-defined `operator true` or `operator false` with the selected user-defined operator:</span></span>

*  <span data-ttu-id="8624e-2313">L'operazione `x && y` viene valutato come `T.false(x) ? x : T.&(x, y)`, dove `T.false(x)` è una chiamata del `operator false` dichiarato nel `T`, e `T.&(x, y)` è una chiamata dell'oggetto selezionato `operator &`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2313">The operation `x && y` is evaluated as `T.false(x) ? x : T.&(x, y)`, where `T.false(x)` is an invocation of the `operator false` declared in `T`, and `T.&(x, y)` is an invocation of the selected `operator &`.</span></span> <span data-ttu-id="8624e-2314">In altre parole, `x` viene valutato per primo e `operator false` viene richiamato per determinare se il risultato `x` è indubbiamente false.</span><span class="sxs-lookup"><span data-stu-id="8624e-2314">In other words, `x` is first evaluated and `operator false` is invoked on the result to determine if `x` is definitely false.</span></span> <span data-ttu-id="8624e-2315">Quindi, se `x` viene sicuramente impostato su false, il risultato dell'operazione è il valore calcolato in precedenza per `x`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2315">Then, if `x` is definitely false, the result of the operation is the value previously computed for `x`.</span></span> <span data-ttu-id="8624e-2316">In caso contrario, `y` viene valutata viene selezionato `operator &` viene richiamato sul valore calcolato in precedenza per `x` e il valore calcolato per `y` per produrre il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2316">Otherwise, `y` is evaluated, and the selected `operator &` is invoked on the value previously computed for `x` and the value computed for `y` to produce the result of the operation.</span></span>
*  <span data-ttu-id="8624e-2317">L'operazione `x || y` viene valutato come `T.true(x) ? x : T.|(x, y)`, dove `T.true(x)` è una chiamata del `operator true` dichiarato nel `T`, e `T.|(x,y)` è una chiamata dell'oggetto selezionato `operator|`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2317">The operation `x || y` is evaluated as `T.true(x) ? x : T.|(x, y)`, where `T.true(x)` is an invocation of the `operator true` declared in `T`, and `T.|(x,y)` is an invocation of the selected `operator|`.</span></span> <span data-ttu-id="8624e-2318">In altre parole, `x` viene valutato per primo e `operator true` viene richiamato per determinare se il risultato `x` è assolutamente vero.</span><span class="sxs-lookup"><span data-stu-id="8624e-2318">In other words, `x` is first evaluated and `operator true` is invoked on the result to determine if `x` is definitely true.</span></span> <span data-ttu-id="8624e-2319">Quindi, se `x` è certamente true, il risultato dell'operazione è il valore calcolato in precedenza per `x`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2319">Then, if `x` is definitely true, the result of the operation is the value previously computed for `x`.</span></span> <span data-ttu-id="8624e-2320">In caso contrario, `y` viene valutata viene selezionato `operator |` viene richiamato sul valore calcolato in precedenza per `x` e il valore calcolato per `y` per produrre il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2320">Otherwise, `y` is evaluated, and the selected `operator |` is invoked on the value previously computed for `x` and the value computed for `y` to produce the result of the operation.</span></span>

<span data-ttu-id="8624e-2321">In una di queste operazioni, l'espressione rappresentata da `x` è solo una volta valutati e l'espressione rappresentata da `y` è o non valutato o valutata esattamente una volta.</span><span class="sxs-lookup"><span data-stu-id="8624e-2321">In either of these operations, the expression given by `x` is only evaluated once, and the expression given by `y` is either not evaluated or evaluated exactly once.</span></span>

<span data-ttu-id="8624e-2322">Per un esempio di un tipo che implementa `operator true` e `operator false`, vedere [tipo booleano Database](structs.md#database-boolean-type).</span><span class="sxs-lookup"><span data-stu-id="8624e-2322">For an example of a type that implements `operator true` and `operator false`, see [Database boolean type](structs.md#database-boolean-type).</span></span>

## <a name="the-null-coalescing-operator"></a><span data-ttu-id="8624e-2323">L'operatore null-coalescing</span><span class="sxs-lookup"><span data-stu-id="8624e-2323">The null coalescing operator</span></span>

<span data-ttu-id="8624e-2324">Il `??` operatore viene chiamato l'operatore null-coalescing.</span><span class="sxs-lookup"><span data-stu-id="8624e-2324">The `??` operator is called the null coalescing operator.</span></span>

```antlr
null_coalescing_expression
    : conditional_or_expression
    | conditional_or_expression '??' null_coalescing_expression
    ;
```

<span data-ttu-id="8624e-2325">Un'espressione null-coalescing del form `a ?? b` richiede `a` sia di un tipo riferimento o un tipo nullable.</span><span class="sxs-lookup"><span data-stu-id="8624e-2325">A null coalescing expression of the form `a ?? b` requires `a` to be of a nullable type or reference type.</span></span> <span data-ttu-id="8624e-2326">Se `a` è diverso da null, il risultato del `a ?? b` viene `a`; in caso contrario, il risultato è `b`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2326">If `a` is non-null, the result of `a ?? b` is `a`; otherwise, the result is `b`.</span></span> <span data-ttu-id="8624e-2327">L'operazione valuta `b` solo se `a` è null.</span><span class="sxs-lookup"><span data-stu-id="8624e-2327">The operation evaluates `b` only if `a` is null.</span></span>

<span data-ttu-id="8624e-2328">L'operatore null-coalescing è associativa a destra, ovvero le operazioni vengono eseguite da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="8624e-2328">The null coalescing operator is right-associative, meaning that operations are grouped from right to left.</span></span> <span data-ttu-id="8624e-2329">Ad esempio, un'espressione nel formato `a ?? b ?? c` darà `a ?? (b ?? c)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2329">For example, an expression of the form `a ?? b ?? c` is evaluated as `a ?? (b ?? c)`.</span></span> <span data-ttu-id="8624e-2330">In generale le condizioni, di un'espressione del form `E1 ?? E2 ?? ... ?? En` restituisce il primo degli operandi è diverso da null, o null se tutti gli operandi sono null.</span><span class="sxs-lookup"><span data-stu-id="8624e-2330">In general terms, an expression of the form `E1 ?? E2 ?? ... ?? En` returns the first of the operands that is non-null, or null if all operands are null.</span></span>

<span data-ttu-id="8624e-2331">Il tipo dell'espressione `a ?? b` dipende da quali conversioni implicite disponibili sugli operandi.</span><span class="sxs-lookup"><span data-stu-id="8624e-2331">The type of the expression `a ?? b` depends on which implicit conversions are available on the operands.</span></span> <span data-ttu-id="8624e-2332">In ordine di preferenza, il tipo di `a ?? b` viene `A0`, `A`, o `B`, dove `A` è il tipo di `a` (a condizione che `a` dispone di un tipo), `B` è il tipo di `b` ( purché `b` dispone di un tipo), e `A0` è il tipo sottostante del `A` se `A` è un tipo che ammette valori null, o `A` in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="8624e-2332">In order of preference, the type of `a ?? b` is `A0`, `A`, or `B`, where `A` is the type of `a` (provided that `a` has a type), `B` is the type of `b` (provided that `b` has a type), and `A0` is the underlying type of `A` if `A` is a nullable type, or `A` otherwise.</span></span> <span data-ttu-id="8624e-2333">In particolare, `a ?? b` viene elaborato come segue:</span><span class="sxs-lookup"><span data-stu-id="8624e-2333">Specifically, `a ?? b` is processed as follows:</span></span>

*  <span data-ttu-id="8624e-2334">Se `A` esiste e non è un tipo nullable o un tipo riferimento, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2334">If `A` exists and is not a nullable type or a reference type, a compile-time error occurs.</span></span>
*  <span data-ttu-id="8624e-2335">Se `b` è un'espressione dinamica, il tipo di risultato `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2335">If `b` is a dynamic expression, the result type is `dynamic`.</span></span> <span data-ttu-id="8624e-2336">In fase di esecuzione, `a` viene valutato per primo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2336">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="8624e-2337">Se `a` non è null, `a` viene convertito in dinamico, e diventa il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2337">If `a` is not null, `a` is converted to dynamic, and this becomes the result.</span></span> <span data-ttu-id="8624e-2338">In caso contrario, `b` viene valutato e diventa il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2338">Otherwise, `b` is evaluated, and this becomes the result.</span></span>
*  <span data-ttu-id="8624e-2339">In caso contrario, se `A` esista e sia un tipo nullable e non esiste una conversione implicita da `b` a `A0`, il tipo di risultato è `A0`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2339">Otherwise, if `A` exists and is a nullable type and an implicit conversion exists from `b` to `A0`, the result type is `A0`.</span></span> <span data-ttu-id="8624e-2340">In fase di esecuzione, `a` viene valutato per primo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2340">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="8624e-2341">Se `a` non è null, `a` è scorporato tipo `A0`, e diventa il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2341">If `a` is not null, `a` is unwrapped to type `A0`, and this becomes the result.</span></span> <span data-ttu-id="8624e-2342">In caso contrario, `b` viene valutata e convertito nel tipo `A0`, e diventa il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2342">Otherwise, `b` is evaluated and converted to type `A0`, and this becomes the result.</span></span>
*  <span data-ttu-id="8624e-2343">In caso contrario, se `A` esiste ed è presente una conversione implicita da `b` al `A`, il tipo di risultato `A`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2343">Otherwise, if `A` exists and an implicit conversion exists from `b` to `A`, the result type is `A`.</span></span> <span data-ttu-id="8624e-2344">In fase di esecuzione, `a` viene valutato per primo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2344">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="8624e-2345">Se `a` non è null, `a` diventa il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2345">If `a` is not null, `a` becomes the result.</span></span> <span data-ttu-id="8624e-2346">In caso contrario, `b` viene valutata e convertito nel tipo `A`, e diventa il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2346">Otherwise, `b` is evaluated and converted to type `A`, and this becomes the result.</span></span>
*  <span data-ttu-id="8624e-2347">In caso contrario, se `b` dispone di un tipo `B` ed è presente una conversione implicita da `a` al `B`, il tipo di risultato `B`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2347">Otherwise, if `b` has a type `B` and an implicit conversion exists from `a` to `B`, the result type is `B`.</span></span> <span data-ttu-id="8624e-2348">In fase di esecuzione, `a` viene valutato per primo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2348">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="8624e-2349">Se `a` non è null, `a` è scorporato per digitare `A0` (se `A` esista e ammette valori null) e convertita nel tipo `B`, e diventa il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2349">If `a` is not null, `a` is unwrapped to type `A0` (if `A` exists and is nullable) and converted to type `B`, and this becomes the result.</span></span> <span data-ttu-id="8624e-2350">In caso contrario, `b` viene valutato e diventa il risultato.</span><span class="sxs-lookup"><span data-stu-id="8624e-2350">Otherwise, `b` is evaluated and becomes the result.</span></span>
*  <span data-ttu-id="8624e-2351">In caso contrario, `a` e `b` sono incompatibili e un errore in fase di compilazione si verifica.</span><span class="sxs-lookup"><span data-stu-id="8624e-2351">Otherwise, `a` and `b` are incompatible, and a compile-time error occurs.</span></span>

## <a name="conditional-operator"></a><span data-ttu-id="8624e-2352">Operatore condizionale</span><span class="sxs-lookup"><span data-stu-id="8624e-2352">Conditional operator</span></span>

<span data-ttu-id="8624e-2353">Il `?:` operatore viene chiamato operatore condizionale.</span><span class="sxs-lookup"><span data-stu-id="8624e-2353">The `?:` operator is called the conditional operator.</span></span> <span data-ttu-id="8624e-2354">Si parla in alcuni casi anche l'operatore ternario.</span><span class="sxs-lookup"><span data-stu-id="8624e-2354">It is at times also called the ternary operator.</span></span>

```antlr
conditional_expression
    : null_coalescing_expression
    | null_coalescing_expression '?' expression ':' expression
    ;
```

<span data-ttu-id="8624e-2355">Un'espressione condizionale del form `b ? x : y` innanzitutto valuta la condizione `b`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2355">A conditional expression of the form `b ? x : y` first evaluates the condition `b`.</span></span> <span data-ttu-id="8624e-2356">Quindi, se `b` viene `true`, `x` viene valutato e diventa il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2356">Then, if `b` is `true`, `x` is evaluated and becomes the result of the operation.</span></span> <span data-ttu-id="8624e-2357">In caso contrario, `y` viene valutato e diventa il risultato dell'operazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2357">Otherwise, `y` is evaluated and becomes the result of the operation.</span></span> <span data-ttu-id="8624e-2358">Un'espressione condizionale non restituisce mai entrambe `x` e `y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2358">A conditional expression never evaluates both `x` and `y`.</span></span>

<span data-ttu-id="8624e-2359">L'operatore condizionale è associativa a destra, ovvero le operazioni vengono eseguite da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="8624e-2359">The conditional operator is right-associative, meaning that operations are grouped from right to left.</span></span> <span data-ttu-id="8624e-2360">Ad esempio, un'espressione nel formato `a ? b : c ? d : e` darà `a ? b : (c ? d : e)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2360">For example, an expression of the form `a ? b : c ? d : e` is evaluated as `a ? b : (c ? d : e)`.</span></span>

<span data-ttu-id="8624e-2361">Il primo operando del `?:` operatore deve essere un'espressione che può essere convertita in modo implicito in `bool`, o un'espressione di un tipo che implementa `operator true`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2361">The first operand of the `?:` operator must be an expression that can be implicitly converted to `bool`, or an expression of a type that implements `operator true`.</span></span> <span data-ttu-id="8624e-2362">Se viene soddisfatto nessuno di questi requisiti, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2362">If neither of these requirements is satisfied, a compile-time error occurs.</span></span>

<span data-ttu-id="8624e-2363">Il secondo e terzo operando `x` e `y`, del `?:` operatore controllare il tipo di espressione condizionale.</span><span class="sxs-lookup"><span data-stu-id="8624e-2363">The second and third operands, `x` and `y`, of the `?:` operator control the type of the conditional expression.</span></span>

*  <span data-ttu-id="8624e-2364">Se `x` è di tipo `X` e `y` ha tipo `Y` quindi</span><span class="sxs-lookup"><span data-stu-id="8624e-2364">If `x` has type `X` and `y` has type `Y` then</span></span>
   * <span data-ttu-id="8624e-2365">Se una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) esistente dal `X` al `Y`, ma non da `Y` alla `X`, quindi `Y` è il tipo dell'espressione condizionale.</span><span class="sxs-lookup"><span data-stu-id="8624e-2365">If an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from `X` to `Y`, but not from `Y` to `X`, then `Y` is the type of the conditional expression.</span></span>
   * <span data-ttu-id="8624e-2366">Se una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)) esistente dal `Y` al `X`, ma non da `X` alla `Y`, quindi `X` è il tipo dell'espressione condizionale.</span><span class="sxs-lookup"><span data-stu-id="8624e-2366">If an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from `Y` to `X`, but not from `X` to `Y`, then `X` is the type of the conditional expression.</span></span>
   * <span data-ttu-id="8624e-2367">In caso contrario, nessun tipo di espressione può essere determinato e si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2367">Otherwise, no expression type can be determined, and a compile-time error occurs.</span></span>
*  <span data-ttu-id="8624e-2368">Se solo uno dei `x` e `y` ha un tipo ed entrambe `x` e `y`, di sono implicitamente convertibili in tale tipo, che è il tipo dell'espressione condizionale.</span><span class="sxs-lookup"><span data-stu-id="8624e-2368">If only one of `x` and `y` has a type, and both `x` and `y`, of are implicitly convertible to that type, then that is the type of the conditional expression.</span></span>
*  <span data-ttu-id="8624e-2369">In caso contrario, nessun tipo di espressione può essere determinato e si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2369">Otherwise, no expression type can be determined, and a compile-time error occurs.</span></span>

<span data-ttu-id="8624e-2370">L'elaborazione in fase di esecuzione di un'espressione condizionale del form `b ? x : y` prevede i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-2370">The run-time processing of a conditional expression of the form `b ? x : y` consists of the following steps:</span></span>

*  <span data-ttu-id="8624e-2371">Prima di tutto `b` viene valutato e il `bool` pari a `b` viene determinato:</span><span class="sxs-lookup"><span data-stu-id="8624e-2371">First, `b` is evaluated, and the `bool` value of `b` is determined:</span></span>
   * <span data-ttu-id="8624e-2372">Se una conversione implicita dal tipo della `b` al `bool` esiste, quindi viene eseguita la conversione implicita per produrre un `bool` valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2372">If an implicit conversion from the type of `b` to `bool` exists, then this implicit conversion is performed to produce a `bool` value.</span></span>
   * <span data-ttu-id="8624e-2373">In caso contrario, il `operator true` definito dal tipo di `b` richiamato per produrre un `bool` valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2373">Otherwise, the `operator true` defined by the type of `b` is invoked to produce a `bool` value.</span></span>
*  <span data-ttu-id="8624e-2374">Se il `bool` valore prodotto dal passaggio precedente viene `true`, quindi `x` viene valutata e convertito nel tipo dell'espressione condizionale, che diventa il risultato dell'espressione condizionale.</span><span class="sxs-lookup"><span data-stu-id="8624e-2374">If the `bool` value produced by the step above is `true`, then `x` is evaluated and converted to the type of the conditional expression, and this becomes the result of the conditional expression.</span></span>
*  <span data-ttu-id="8624e-2375">In caso contrario, `y` viene valutata e convertito nel tipo dell'espressione condizionale, che diventa il risultato dell'espressione condizionale.</span><span class="sxs-lookup"><span data-stu-id="8624e-2375">Otherwise, `y` is evaluated and converted to the type of the conditional expression, and this becomes the result of the conditional expression.</span></span>

## <a name="anonymous-function-expressions"></a><span data-ttu-id="8624e-2376">Espressioni di funzioni anonime</span><span class="sxs-lookup"><span data-stu-id="8624e-2376">Anonymous function expressions</span></span>

<span data-ttu-id="8624e-2377">Un' ***funzione anonima*** è un'espressione che rappresenta una definizione di metodo "inline".</span><span class="sxs-lookup"><span data-stu-id="8624e-2377">An ***anonymous function*** is an expression that represents an "in-line" method definition.</span></span> <span data-ttu-id="8624e-2378">Una funzione anonima non dispone di un valore o un tipo in e di se stesso, ma è convertibile in un tipo di struttura ad albero espressioni o delegato compatibile.</span><span class="sxs-lookup"><span data-stu-id="8624e-2378">An anonymous function does not have a value or type in and of itself, but is convertible to a compatible delegate or expression tree type.</span></span> <span data-ttu-id="8624e-2379">La valutazione di una conversione funzione anonima dipende dal tipo di destinazione della conversione: Se è un tipo delegato, la conversione restituisce un valore di delegato che fa riferimento il metodo che definisce la funzione anonima.</span><span class="sxs-lookup"><span data-stu-id="8624e-2379">The evaluation of an anonymous function conversion depends on the target type of the conversion: If it is a delegate type, the conversion evaluates to a delegate value referencing the method which the anonymous function defines.</span></span> <span data-ttu-id="8624e-2380">Se è un tipo di albero delle espressioni, la conversione restituisce un albero delle espressioni che rappresenta la struttura del metodo come una struttura di oggetti.</span><span class="sxs-lookup"><span data-stu-id="8624e-2380">If it is an expression tree type, the conversion evaluates to an expression tree which represents the structure of the method as an object structure.</span></span>

<span data-ttu-id="8624e-2381">Per motivi storici esistono due varianti sintattiche di funzioni anonime, vale a dire *lambda_expression*s e *anonymous_method_expression*s.</span><span class="sxs-lookup"><span data-stu-id="8624e-2381">For historical reasons there are two syntactic flavors of anonymous functions, namely *lambda_expression*s and *anonymous_method_expression*s.</span></span> <span data-ttu-id="8624e-2382">Per quasi tutti gli scopi *lambda_expression*sono più ridotte ed espressive *anonymous_method_expression*s, che rimangono con le versioni precedenti del linguaggio per la compatibilità.</span><span class="sxs-lookup"><span data-stu-id="8624e-2382">For almost all purposes, *lambda_expression*s are more concise and expressive than *anonymous_method_expression*s, which remain in the language for backwards compatibility.</span></span>

```antlr
lambda_expression
    : anonymous_function_signature '=>' anonymous_function_body
    ;

anonymous_method_expression
    : 'delegate' explicit_anonymous_function_signature? block
    ;

anonymous_function_signature
    : explicit_anonymous_function_signature
    | implicit_anonymous_function_signature
    ;

explicit_anonymous_function_signature
    : '(' explicit_anonymous_function_parameter_list? ')'
    ;

explicit_anonymous_function_parameter_list
    : explicit_anonymous_function_parameter (',' explicit_anonymous_function_parameter)*
    ;

explicit_anonymous_function_parameter
    : anonymous_function_parameter_modifier? type identifier
    ;

anonymous_function_parameter_modifier
    : 'ref'
    | 'out'
    ;

implicit_anonymous_function_signature
    : '(' implicit_anonymous_function_parameter_list? ')'
    | implicit_anonymous_function_parameter
    ;

implicit_anonymous_function_parameter_list
    : implicit_anonymous_function_parameter (',' implicit_anonymous_function_parameter)*
    ;

implicit_anonymous_function_parameter
    : identifier
    ;

anonymous_function_body
    : expression
    | block
    ;
```

<span data-ttu-id="8624e-2383">Il `=>` operatore ha la stessa precedenza dell'assegnazione (`=`) e si associa all'operando destro.</span><span class="sxs-lookup"><span data-stu-id="8624e-2383">The `=>` operator has the same precedence as assignment (`=`) and is right-associative.</span></span>

<span data-ttu-id="8624e-2384">Una funzione anonima con il `async` modificatore è una funzione asincrona e segue le regole descritte in [iteratori](classes.md#iterators).</span><span class="sxs-lookup"><span data-stu-id="8624e-2384">An anonymous function with the `async` modifier is an async function and follows the rules described in [Iterators](classes.md#iterators).</span></span>

<span data-ttu-id="8624e-2385">I parametri di una funzione anonima sotto forma di una *lambda_expression* possono essere digitati in modo esplicito o implicito.</span><span class="sxs-lookup"><span data-stu-id="8624e-2385">The parameters of an anonymous function in the form of a *lambda_expression* can be explicitly or implicitly typed.</span></span> <span data-ttu-id="8624e-2386">In un elenco di parametri tipizzati in modo esplicito, viene dichiarato in modo esplicito il tipo di ogni parametro.</span><span class="sxs-lookup"><span data-stu-id="8624e-2386">In an explicitly typed parameter list, the type of each parameter is explicitly stated.</span></span> <span data-ttu-id="8624e-2387">In un elenco di parametri tipizzati in modo implicito, i tipi dei parametri vengono dedotti dal contesto in cui si verifica la funzione anonima, in particolare, quando la funzione anonima viene convertita in un tipo delegato compatibile o un tipo di albero delle espressioni, che fornisce tipo i tipi di parametro ([conversioni delle funzioni anonime](conversions.md#anonymous-function-conversions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2387">In an implicitly typed parameter list, the types of the parameters are inferred from the context in which the anonymous function occurs—specifically, when the anonymous function is converted to a compatible delegate type or expression tree type, that type provides the parameter types ([Anonymous function conversions](conversions.md#anonymous-function-conversions)).</span></span>

<span data-ttu-id="8624e-2388">In una funzione anonima con un parametro singolo, tipizzata in modo implicito, le parentesi che possono essere omessa dall'elenco di parametri.</span><span class="sxs-lookup"><span data-stu-id="8624e-2388">In an anonymous function with a single, implicitly typed parameter, the parentheses may be omitted from the parameter list.</span></span> <span data-ttu-id="8624e-2389">In altre parole, una funzione anonima del form</span><span class="sxs-lookup"><span data-stu-id="8624e-2389">In other words, an anonymous function of the form</span></span>
```csharp
( param ) => expr
```
<span data-ttu-id="8624e-2390">può essere abbreviato utilizzando</span><span class="sxs-lookup"><span data-stu-id="8624e-2390">can be abbreviated to</span></span>
```csharp
param => expr
```

<span data-ttu-id="8624e-2391">Elenco di parametri di una funzione anonima sotto forma di un' *anonymous_method_expression* è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2391">The parameter list of an anonymous function in the form of an *anonymous_method_expression* is optional.</span></span> <span data-ttu-id="8624e-2392">Se non specificato, i parametri devono essere tipizzati in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="8624e-2392">If given, the parameters must be explicitly typed.</span></span> <span data-ttu-id="8624e-2393">Se non, la funzione anonima è convertibile in un delegato con qualsiasi parametro di elenco che non contengono `out` parametri.</span><span class="sxs-lookup"><span data-stu-id="8624e-2393">If not, the anonymous function is convertible to a delegate with any parameter list not containing `out` parameters.</span></span>

<span data-ttu-id="8624e-2394">Oggetto *block* corpo di una funzione anonima è raggiungibile ([endpoint e raggiungibilità](statements.md#end-points-and-reachability)), a meno che la funzione anonima si verifica all'interno di un'istruzione non raggiungibile.</span><span class="sxs-lookup"><span data-stu-id="8624e-2394">A *block* body of an anonymous function is reachable ([End points and reachability](statements.md#end-points-and-reachability)) unless the anonymous function occurs inside an unreachable statement.</span></span>

<span data-ttu-id="8624e-2395">Di seguito sono riportati alcuni esempi di funzioni anonime:</span><span class="sxs-lookup"><span data-stu-id="8624e-2395">Some examples of anonymous functions follow below:</span></span>

```csharp
x => x + 1                              // Implicitly typed, expression body
x => { return x + 1; }                  // Implicitly typed, statement body
(int x) => x + 1                        // Explicitly typed, expression body
(int x) => { return x + 1; }            // Explicitly typed, statement body
(x, y) => x * y                         // Multiple parameters
() => Console.WriteLine()               // No parameters
async (t1,t2) => await t1 + await t2    // Async
delegate (int x) { return x + 1; }      // Anonymous method expression
delegate { return 1 + 1; }              // Parameter list omitted
```

<span data-ttu-id="8624e-2396">Il comportamento delle *lambda_expression*s e *anonymous_method_expression*s è uguali ad eccezione di quanto riportato di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-2396">The behavior of *lambda_expression*s and *anonymous_method_expression*s is the same except for the following points:</span></span>

*  <span data-ttu-id="8624e-2397">*anonymous_method_expression*s consentono l'elenco di parametri per essere omesse del tutto, la convertibilità in qualsiasi elenco di parametri con valori di tipi delegati.</span><span class="sxs-lookup"><span data-stu-id="8624e-2397">*anonymous_method_expression*s permit the parameter list to be omitted entirely, yielding convertibility to delegate types of any list of value parameters.</span></span>
*  <span data-ttu-id="8624e-2398">*lambda_expression*s consentono tipi di parametro per omettere e dedurre mentre *anonymous_method_expression*richiedono tipi di parametro indicare in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="8624e-2398">*lambda_expression*s permit parameter types to be omitted and inferred whereas *anonymous_method_expression*s require parameter types to be explicitly stated.</span></span>
*  <span data-ttu-id="8624e-2399">Il corpo di una *lambda_expression* può essere un'espressione o un blocco di istruzioni mentre il corpo di un' *anonymous_method_expression* deve essere un blocco di istruzioni.</span><span class="sxs-lookup"><span data-stu-id="8624e-2399">The body of a *lambda_expression* can be an expression or a statement block whereas the body of an *anonymous_method_expression* must be a statement block.</span></span>
*  <span data-ttu-id="8624e-2400">Solo *lambda_expression*hanno le conversioni in tipi di albero delle espressioni compatibili ([tipi di albero delle espressioni](types.md#expression-tree-types)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2400">Only *lambda_expression*s have conversions to compatible expression tree types ([Expression tree types](types.md#expression-tree-types)).</span></span>

### <a name="anonymous-function-signatures"></a><span data-ttu-id="8624e-2401">Firme di funzione anonima</span><span class="sxs-lookup"><span data-stu-id="8624e-2401">Anonymous function signatures</span></span>

<span data-ttu-id="8624e-2402">L'opzione facoltativa *anonymous_function_signature* di una funzione anonima definisce i nomi e, facoltativamente, i tipi dei parametri formali per la funzione anonima.</span><span class="sxs-lookup"><span data-stu-id="8624e-2402">The optional *anonymous_function_signature* of an anonymous function defines the names and optionally the types of the formal parameters for the anonymous function.</span></span> <span data-ttu-id="8624e-2403">L'ambito dei parametri della funzione anonima è il *anonymous_function_body*.</span><span class="sxs-lookup"><span data-stu-id="8624e-2403">The scope of the parameters of the anonymous function is the *anonymous_function_body*.</span></span> <span data-ttu-id="8624e-2404">([Ambiti](basic-concepts.md#scopes)) con l'elenco di parametri (se disponibile) anonimo-corpo del metodo costituisce uno spazio di dichiarazione ([dichiarazioni](basic-concepts.md#declarations)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2404">([Scopes](basic-concepts.md#scopes)) Together with the parameter list (if given) the anonymous-method-body constitutes a declaration space ([Declarations](basic-concepts.md#declarations)).</span></span> <span data-ttu-id="8624e-2405">È pertanto un errore in fase di compilazione per il nome di un parametro della funzione anonima corrisponda al nome di una variabile locale, costante locale o un parametro il cui ambito include il *anonymous_method_expression* o *lambda_ espressione*.</span><span class="sxs-lookup"><span data-stu-id="8624e-2405">It is thus a compile-time error for the name of a parameter of the anonymous function to match the name of a local variable, local constant or parameter whose scope includes the *anonymous_method_expression* or *lambda_expression*.</span></span>

<span data-ttu-id="8624e-2406">Se una funzione anonima ha un *explicit_anonymous_function_signature*, quindi il set di tipi delegati compatibili e tipi di albero delle espressioni è limitato a quelli aventi stessi tipi di parametro e modificatori nello stesso ordine.</span><span class="sxs-lookup"><span data-stu-id="8624e-2406">If an anonymous function has an *explicit_anonymous_function_signature*, then the set of compatible delegate types and expression tree types is restricted to those that have the same parameter types and modifiers in the same order.</span></span> <span data-ttu-id="8624e-2407">A differenza delle conversioni dei metodi di gruppo ([conversioni dei metodi di gruppo](conversions.md#method-group-conversions)), controvarianza dei tipi di parametro di funzione anonima non è supportato.</span><span class="sxs-lookup"><span data-stu-id="8624e-2407">In contrast to method group conversions ([Method group conversions](conversions.md#method-group-conversions)), contra-variance of anonymous function parameter types is not supported.</span></span> <span data-ttu-id="8624e-2408">Se una funzione anonima non ha un *anonymous_function_signature*, quindi il set di tipi delegati compatibili e tipi di albero delle espressioni è limitato a quelli che non hanno alcuna `out` parametri.</span><span class="sxs-lookup"><span data-stu-id="8624e-2408">If an anonymous function does not have an *anonymous_function_signature*, then the set of compatible delegate types and expression tree types is restricted to those that have no `out` parameters.</span></span>

<span data-ttu-id="8624e-2409">Si noti che un *anonymous_function_signature* non può includere attributi o una matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="8624e-2409">Note that an *anonymous_function_signature* cannot include attributes or a parameter array.</span></span> <span data-ttu-id="8624e-2410">Tuttavia, un' *anonymous_function_signature* siano compatibili con un tipo di delegato il cui elenco di parametri contiene una matrice di parametri.</span><span class="sxs-lookup"><span data-stu-id="8624e-2410">Nevertheless, an *anonymous_function_signature* may be compatible with a delegate type whose parameter list contains a parameter array.</span></span>

<span data-ttu-id="8624e-2411">Si noti inoltre che la conversione a un tipo di albero delle espressioni, anche se compatibili tra loro, può comunque non riuscire in fase di compilazione ([tipi di albero delle espressioni](types.md#expression-tree-types)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2411">Note also that conversion to an expression tree type, even if compatible, may still fail at compile-time ([Expression tree types](types.md#expression-tree-types)).</span></span>

### <a name="anonymous-function-bodies"></a><span data-ttu-id="8624e-2412">Corpi delle funzioni anonime</span><span class="sxs-lookup"><span data-stu-id="8624e-2412">Anonymous function bodies</span></span>

<span data-ttu-id="8624e-2413">Il corpo (*espressione* oppure *blocco*) di una funzione anonima è soggetto alle regole seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-2413">The body (*expression* or *block*) of an anonymous function is subject to the following rules:</span></span>

*  <span data-ttu-id="8624e-2414">Se la funzione anonima include una firma, sono disponibili nel corpo i parametri specificati nella firma.</span><span class="sxs-lookup"><span data-stu-id="8624e-2414">If the anonymous function includes a signature, the parameters specified in the signature are available in the body.</span></span> <span data-ttu-id="8624e-2415">Se la funzione anonima non dispone di alcuna firma può essere convertito in un tipo di delegato o tipo di espressione con parametri ([conversioni delle funzioni anonime](conversions.md#anonymous-function-conversions)), ma i parametri non sono accessibili nel corpo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2415">If the anonymous function has no signature it can be converted to a delegate type or expression type having parameters ([Anonymous function conversions](conversions.md#anonymous-function-conversions)), but the parameters cannot be accessed in the body.</span></span>
*  <span data-ttu-id="8624e-2416">Ad eccezione di `ref` oppure `out` i parametri specificati nella firma (se presente) di inclusione più vicino funzione anonima, è un errore in fase di compilazione per il corpo per accedere a un `ref` o `out` parametro.</span><span class="sxs-lookup"><span data-stu-id="8624e-2416">Except for `ref` or `out` parameters specified in the signature (if any) of the nearest enclosing anonymous function, it is a compile-time error for the body to access a `ref` or `out` parameter.</span></span>
*  <span data-ttu-id="8624e-2417">Quando il tipo della `this` è un tipo struct, è un errore in fase di compilazione per il corpo per accedere a `this`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2417">When the type of `this` is a struct type, it is a compile-time error for the body to access `this`.</span></span> <span data-ttu-id="8624e-2418">Ciò è vero se l'accesso è esplicita (come in `this.x`) o impliciti (come mostrato nella `x` in cui `x` è un membro di istanza dello struct).</span><span class="sxs-lookup"><span data-stu-id="8624e-2418">This is true whether the access is explicit (as in `this.x`) or implicit (as in `x` where `x` is an instance member of the struct).</span></span> <span data-ttu-id="8624e-2419">Questa regola è sufficiente impedisce l'accesso e non influisce sulla ricerca di membri comporta un membro dello struct.</span><span class="sxs-lookup"><span data-stu-id="8624e-2419">This rule simply prohibits such access and does not affect whether member lookup results in a member of the struct.</span></span>
*  <span data-ttu-id="8624e-2420">Il corpo può accedere alle variabili esterne ([Outer variabili](expressions.md#outer-variables)) della funzione anonima.</span><span class="sxs-lookup"><span data-stu-id="8624e-2420">The body has access to the outer variables ([Outer variables](expressions.md#outer-variables)) of the anonymous function.</span></span> <span data-ttu-id="8624e-2421">Accesso a una variabile esterna farà riferimento all'istanza della variabile che è attiva al momento il *lambda_expression* oppure *anonymous_method_expression* viene valutata ([valutazione di espressioni di funzioni anonime](expressions.md#evaluation-of-anonymous-function-expressions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2421">Access of an outer variable will reference the instance of the variable that is active at the time the *lambda_expression* or *anonymous_method_expression* is evaluated ([Evaluation of anonymous function expressions](expressions.md#evaluation-of-anonymous-function-expressions)).</span></span>
*  <span data-ttu-id="8624e-2422">Tratta di un errore in fase di compilazione per il corpo conterrà un `goto` istruzione `break` istruzione o `continue` istruzione la cui destinazione è all'esterno del corpo o all'interno del corpo di una funzione anonima contenuta.</span><span class="sxs-lookup"><span data-stu-id="8624e-2422">It is a compile-time error for the body to contain a `goto` statement, `break` statement, or `continue` statement whose target is outside the body or within the body of a contained anonymous function.</span></span>
*  <span data-ttu-id="8624e-2423">Oggetto `return` restituisce il controllo da una chiamata di inclusione più vicina istruzione nel corpo della funzione anonima, non dal membro della funzione contenitore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2423">A `return` statement in the body returns control from an invocation of the nearest enclosing anonymous function, not from the enclosing function member.</span></span> <span data-ttu-id="8624e-2424">Un'espressione specificata in una `return` istruzione deve essere convertibile in modo implicito nel tipo restituito del tipo di delegato o espressione tipo di struttura ad albero in cui inclusione più vicina *lambda_expression* o *anonymous_ method_expression* viene convertito ([conversioni delle funzioni anonime](conversions.md#anonymous-function-conversions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2424">An expression specified in a `return` statement must be implicitly convertible to the return type of the delegate type or expression tree type to which the nearest enclosing *lambda_expression* or *anonymous_method_expression* is converted ([Anonymous function conversions](conversions.md#anonymous-function-conversions)).</span></span>

<span data-ttu-id="8624e-2425">Non viene specificato in modo esplicito se vi è alcun modo per eseguire il blocco di una funzione anonima diverso dalla valutazione e la chiamata di *lambda_expression* o *anonymous_method_expression*.</span><span class="sxs-lookup"><span data-stu-id="8624e-2425">It is explicitly unspecified whether there is any way to execute the block of an anonymous function other than through evaluation and invocation of the *lambda_expression* or *anonymous_method_expression*.</span></span> <span data-ttu-id="8624e-2426">In particolare, il compilatore potrebbe scegliere di implementare una funzione anonima per la sintesi di uno o più metodi o tipi denominati.</span><span class="sxs-lookup"><span data-stu-id="8624e-2426">In particular, the compiler may choose to implement an anonymous function by synthesizing one or more named methods or types.</span></span> <span data-ttu-id="8624e-2427">I nomi di tali elementi sintetizzati devono essere un formato riservato per l'utilizzo del compilatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2427">The names of any such synthesized elements must be of a form reserved for compiler use.</span></span>

### <a name="overload-resolution-and-anonymous-functions"></a><span data-ttu-id="8624e-2428">Risoluzione dell'overload e le funzioni anonime</span><span class="sxs-lookup"><span data-stu-id="8624e-2428">Overload resolution and anonymous functions</span></span>

<span data-ttu-id="8624e-2429">Funzioni anonime in un elenco di argomenti fanno parte di inferenza del tipo e la risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="8624e-2429">Anonymous functions in an argument list participate in type inference and overload resolution.</span></span> <span data-ttu-id="8624e-2430">Consultare [inferenza](expressions.md#type-inference) e [risoluzione dell'Overload](expressions.md#overload-resolution) per le regole precise.</span><span class="sxs-lookup"><span data-stu-id="8624e-2430">Please refer to [Type inference](expressions.md#type-inference) and [Overload resolution](expressions.md#overload-resolution) for the exact rules.</span></span>

<span data-ttu-id="8624e-2431">L'esempio seguente illustra l'effetto delle funzioni anonime sulla risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="8624e-2431">The following example illustrates the effect of anonymous functions on overload resolution.</span></span>

```csharp
class ItemList<T>: List<T>
{
    public int Sum(Func<T,int> selector) {
        int sum = 0;
        foreach (T item in this) sum += selector(item);
        return sum;
    }

    public double Sum(Func<T,double> selector) {
        double sum = 0;
        foreach (T item in this) sum += selector(item);
        return sum;
    }
}
```

<span data-ttu-id="8624e-2432">Il `ItemList<T>` classe ha due `Sum` metodi.</span><span class="sxs-lookup"><span data-stu-id="8624e-2432">The `ItemList<T>` class has two `Sum` methods.</span></span> <span data-ttu-id="8624e-2433">Ognuno ha una `selector` argomento, che estrae il valore da sommare failover da un elemento di elenco.</span><span class="sxs-lookup"><span data-stu-id="8624e-2433">Each takes a `selector` argument, which extracts the value to sum over from a list item.</span></span> <span data-ttu-id="8624e-2434">Il valore estratto può essere un' `int` o un `double` e la somma risulta in modo analogo può essere un' `int` o un `double`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2434">The extracted value can be either an `int` or a `double` and the resulting sum is likewise either an `int` or a `double`.</span></span>

<span data-ttu-id="8624e-2435">Il `Sum` , ad esempio è stato possibile utilizzare metodi per calcolare le somme in un elenco di righe di dettaglio in un ordine.</span><span class="sxs-lookup"><span data-stu-id="8624e-2435">The `Sum` methods could for example be used to compute sums from a list of detail lines in an order.</span></span>

```csharp
class Detail
{
    public int UnitCount;
    public double UnitPrice;
    ...
}

void ComputeSums() {
    ItemList<Detail> orderDetails = GetOrderDetails(...);
    int totalUnits = orderDetails.Sum(d => d.UnitCount);
    double orderTotal = orderDetails.Sum(d => d.UnitPrice * d.UnitCount);
    ...
}
```

<span data-ttu-id="8624e-2436">La prima chiamata di `orderDetails.Sum`, entrambe `Sum` metodi sono applicabili perché la funzione anonima `d => d. UnitCount` compatibile con entrambi `Func<Detail,int>` e `Func<Detail,double>`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2436">In the first invocation of `orderDetails.Sum`, both `Sum` methods are applicable because the anonymous function `d => d. UnitCount` is compatible with both `Func<Detail,int>` and `Func<Detail,double>`.</span></span> <span data-ttu-id="8624e-2437">Tuttavia, la risoluzione dell'overload seleziona la prima `Sum` metodo perché la conversione in `Func<Detail,int>` è migliore della conversione a `Func<Detail,double>`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2437">However, overload resolution picks the first `Sum` method because the conversion to `Func<Detail,int>` is better than the conversion to `Func<Detail,double>`.</span></span>

<span data-ttu-id="8624e-2438">La seconda chiamata di `orderDetails.Sum`, solo il secondo `Sum` metodo è applicabile poiché la funzione anonima `d => d.UnitPrice * d.UnitCount` produce un valore di tipo `double`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2438">In the second invocation of `orderDetails.Sum`, only the second `Sum` method is applicable because the anonymous function `d => d.UnitPrice * d.UnitCount` produces a value of type `double`.</span></span> <span data-ttu-id="8624e-2439">Di conseguenza, l'overload resolution sceglie la seconda `Sum` per tale chiamata al metodo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2439">Thus, overload resolution picks the second `Sum` method for that invocation.</span></span>

### <a name="anonymous-functions-and-dynamic-binding"></a><span data-ttu-id="8624e-2440">Funzioni anonime e l'associazione dinamica</span><span class="sxs-lookup"><span data-stu-id="8624e-2440">Anonymous functions and dynamic binding</span></span>

<span data-ttu-id="8624e-2441">Una funzione anonima non può essere un ricevitore, argomento o un operando di un'operazione dinamica associata.</span><span class="sxs-lookup"><span data-stu-id="8624e-2441">An anonymous function cannot be a receiver, argument or operand of a dynamically bound operation.</span></span>

### <a name="outer-variables"></a><span data-ttu-id="8624e-2442">Variabili esterne</span><span class="sxs-lookup"><span data-stu-id="8624e-2442">Outer variables</span></span>

<span data-ttu-id="8624e-2443">Qualsiasi variabile locale, un parametro di valore o la matrice di parametri il cui ambito include il *lambda_expression* oppure *anonymous_method_expression* viene chiamato un ***variabile esterna*** della funzione anonima.</span><span class="sxs-lookup"><span data-stu-id="8624e-2443">Any local variable, value parameter, or parameter array whose scope includes the *lambda_expression* or *anonymous_method_expression* is called an ***outer variable*** of the anonymous function.</span></span> <span data-ttu-id="8624e-2444">In un membro di funzione di istanza di una classe, il `this` valore viene considerato un parametro di valore e una variabile di qualsiasi funzione anonima contenuta all'interno del membro di funzione esterna.</span><span class="sxs-lookup"><span data-stu-id="8624e-2444">In an instance function member of a class, the `this` value is considered a value parameter and is an outer variable of any anonymous function contained within the function member.</span></span>

#### <a name="captured-outer-variables"></a><span data-ttu-id="8624e-2445">Acquisire le variabili esterne</span><span class="sxs-lookup"><span data-stu-id="8624e-2445">Captured outer variables</span></span>

<span data-ttu-id="8624e-2446">Quando una variabile esterna fa riferimento una funzione anonima, la variabile esterna viene considerata come sono stati ***acquisite*** dalla funzione anonima.</span><span class="sxs-lookup"><span data-stu-id="8624e-2446">When an outer variable is referenced by an anonymous function, the outer variable is said to have been ***captured*** by the anonymous function.</span></span> <span data-ttu-id="8624e-2447">In genere, la durata di una variabile locale è limitata all'esecuzione del blocco o istruzione a cui è associato ([variabili locali](variables.md#local-variables)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2447">Ordinarily, the lifetime of a local variable is limited to execution of the block or statement with which it is associated ([Local variables](variables.md#local-variables)).</span></span> <span data-ttu-id="8624e-2448">Tuttavia, la durata di una variabile esterna acquisita viene esteso almeno fino a quando il delegato o albero delle espressioni creata dalla funzione anonima diventa idonea per garbage collection.</span><span class="sxs-lookup"><span data-stu-id="8624e-2448">However, the lifetime of a captured outer variable is extended at least until the delegate or expression tree created from the anonymous function becomes eligible for garbage collection.</span></span>

<span data-ttu-id="8624e-2449">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2449">In the example</span></span>
```csharp
using System;

delegate int D();

class Test
{
    static D F() {
        int x = 0;
        D result = () => ++x;
        return result;
    }

    static void Main() {
        D d = F();
        Console.WriteLine(d());
        Console.WriteLine(d());
        Console.WriteLine(d());
    }
}
```
<span data-ttu-id="8624e-2450">la variabile locale `x` viene acquisito dalla funzione anonima e la durata degli `x` viene esteso almeno fino a quando il delegato restituito da `F` diventa idonea per garbage collection (che non viene eseguita fino alla fine di il programma).</span><span class="sxs-lookup"><span data-stu-id="8624e-2450">the local variable `x` is captured by the anonymous function, and the lifetime of `x` is extended at least until the delegate returned from `F` becomes eligible for garbage collection (which doesn't happen until the very end of the program).</span></span> <span data-ttu-id="8624e-2451">Poiché ogni chiamata alla funzione anonima viene eseguita nella stessa istanza di `x`, l'output dell'esempio è:</span><span class="sxs-lookup"><span data-stu-id="8624e-2451">Since each invocation of the anonymous function operates on the same instance of `x`, the output of the example is:</span></span>
```
1
2
3
```

<span data-ttu-id="8624e-2452">Quando una variabile locale o un parametro di valore viene acquisito da una funzione anonima, la variabile locale o il parametro non è più considerato come una variabile fissa ([variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)), ma viene invece considerata un spostabile variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-2452">When a local variable or a value parameter is captured by an anonymous function, the local variable or parameter is no longer considered to be a fixed variable ([Fixed and moveable variables](unsafe-code.md#fixed-and-moveable-variables)), but is instead considered to be a moveable variable.</span></span> <span data-ttu-id="8624e-2453">In questo modo qualsiasi `unsafe` prima di tutto è necessario usare il codice che accetta l'indirizzo di una variabile acquisita outer il `fixed` istruzione per correggere la variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-2453">Thus any `unsafe` code that takes the address of a captured outer variable must first use the `fixed` statement to fix the variable.</span></span>

<span data-ttu-id="8624e-2454">Si noti che a differenza di una variabile non acquisita, una variabile acquisita locale può essere esposti contemporaneamente a più thread di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2454">Note that unlike an uncaptured variable, a captured local variable can be simultaneously exposed to multiple threads of execution.</span></span>

#### <a name="instantiation-of-local-variables"></a><span data-ttu-id="8624e-2455">Creazione di un'istanza delle variabili locali</span><span class="sxs-lookup"><span data-stu-id="8624e-2455">Instantiation of local variables</span></span>

<span data-ttu-id="8624e-2456">Una variabile locale è considerata ***creare un'istanza*** quando l'esecuzione passa all'ambito della variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-2456">A local variable is considered to be ***instantiated*** when execution enters the scope of the variable.</span></span> <span data-ttu-id="8624e-2457">Ad esempio, quando viene richiamato il metodo seguente, la variabile locale `x` viene creata un'istanza e inizializzato tre volte, una volta per ogni iterazione del ciclo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2457">For example, when the following method is invoked, the local variable `x` is instantiated and initialized three times—once for each iteration of the loop.</span></span>

```csharp
static void F() {
    for (int i = 0; i < 3; i++) {
        int x = i * 2 + 1;
        ...
    }
}
```

<span data-ttu-id="8624e-2458">Tuttavia, se si sposta la dichiarazione di `x` all'esterno del ciclo, verrà creata una sola istanza di `x`:</span><span class="sxs-lookup"><span data-stu-id="8624e-2458">However, moving the declaration of `x` outside the loop results in a single instantiation of `x`:</span></span>
```csharp
static void F() {
    int x;
    for (int i = 0; i < 3; i++) {
        x = i * 2 + 1;
        ...
    }
}
```

<span data-ttu-id="8624e-2459">Quando non è stato acquisito, non è possibile osservare esattamente la frequenza con cui viene creata un'istanza di una variabile locale, poiché le durate delle creazioni di istanze sono disgiunte, è possibile per ogni istanza è sufficiente usare la stessa posizione di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2459">When not captured, there is no way to observe exactly how often a local variable is instantiated—because the lifetimes of the instantiations are disjoint, it is possible for each instantiation to simply use the same storage location.</span></span> <span data-ttu-id="8624e-2460">Tuttavia, quando una funzione anonima consente di acquisire una variabile locale, risultano evidenti gli effetti della creazione dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="8624e-2460">However, when an anonymous function captures a local variable, the effects of instantiation become apparent.</span></span>

<span data-ttu-id="8624e-2461">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2461">The example</span></span>
```csharp
using System;

delegate void D();

class Test
{
    static D[] F() {
        D[] result = new D[3];
        for (int i = 0; i < 3; i++) {
            int x = i * 2 + 1;
            result[i] = () => { Console.WriteLine(x); };
        }
        return result;
    }

    static void Main() {
        foreach (D d in F()) d();
    }
}
```
<span data-ttu-id="8624e-2462">produce l'output:</span><span class="sxs-lookup"><span data-stu-id="8624e-2462">produces the output:</span></span>
```
1
3
5
```

<span data-ttu-id="8624e-2463">Tuttavia, quando la dichiarazione di `x` viene spostato all'esterno del ciclo:</span><span class="sxs-lookup"><span data-stu-id="8624e-2463">However, when the declaration of `x` is moved outside the loop:</span></span>
```csharp
static D[] F() {
    D[] result = new D[3];
    int x;
    for (int i = 0; i < 3; i++) {
        x = i * 2 + 1;
        result[i] = () => { Console.WriteLine(x); };
    }
    return result;
}
```
<span data-ttu-id="8624e-2464">L'output è:</span><span class="sxs-lookup"><span data-stu-id="8624e-2464">the output is:</span></span>
```
5
5
5
```

<span data-ttu-id="8624e-2465">Se un ciclo for e dichiara una variabile di iterazione, la variabile stessa viene considerata dichiarato all'esterno del ciclo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2465">If a for-loop declares an iteration variable, that variable itself is considered to be declared outside of the loop.</span></span> <span data-ttu-id="8624e-2466">Di conseguenza, se si modifica l'esempio per acquisire la variabile di iterazione stesso:</span><span class="sxs-lookup"><span data-stu-id="8624e-2466">Thus, if the example is changed to capture the iteration variable itself:</span></span>

```csharp
static D[] F() {
    D[] result = new D[3];
    for (int i = 0; i < 3; i++) {
        result[i] = () => { Console.WriteLine(i); };
    }
    return result;
}
```
<span data-ttu-id="8624e-2467">viene acquisita solo un'istanza della variabile di iterazione, che produce l'output:</span><span class="sxs-lookup"><span data-stu-id="8624e-2467">only one instance of the iteration variable is captured, which produces the output:</span></span>
```
3
3
3
```

<span data-ttu-id="8624e-2468">È possibile che i delegati di funzione anonima condividere alcune variabili acquisite ancora privo di istanze separate di altri utenti.</span><span class="sxs-lookup"><span data-stu-id="8624e-2468">It is possible for anonymous function delegates to share some captured variables yet have separate instances of others.</span></span> <span data-ttu-id="8624e-2469">Ad esempio, se `F` viene modificata in</span><span class="sxs-lookup"><span data-stu-id="8624e-2469">For example, if `F` is changed to</span></span>
```csharp
static D[] F() {
    D[] result = new D[3];
    int x = 0;
    for (int i = 0; i < 3; i++) {
        int y = 0;
        result[i] = () => { Console.WriteLine("{0} {1}", ++x, ++y); };
    }
    return result;
}
```
<span data-ttu-id="8624e-2470">i tre delegati acquisiranno la stessa istanza di `x` ma istanze separate del `y`, e l'output è:</span><span class="sxs-lookup"><span data-stu-id="8624e-2470">the three delegates capture the same instance of `x` but separate instances of `y`, and the output is:</span></span>
```
1 1
2 1
3 1
```

<span data-ttu-id="8624e-2471">Separare le funzioni anonime possono acquisire la stessa istanza di una variabile esterna.</span><span class="sxs-lookup"><span data-stu-id="8624e-2471">Separate anonymous functions can capture the same instance of an outer variable.</span></span> <span data-ttu-id="8624e-2472">Nell’esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2472">In the example:</span></span>
```csharp
using System;

delegate void Setter(int value);

delegate int Getter();

class Test
{
    static void Main() {
        int x = 0;
        Setter s = (int value) => { x = value; };
        Getter g = () => { return x; };
        s(5);
        Console.WriteLine(g());
        s(10);
        Console.WriteLine(g());
    }
}
```
<span data-ttu-id="8624e-2473">le due funzioni anonime acquisiscono la stessa istanza della variabile locale `x`, e può quindi "comunicare" tramite la variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-2473">the two anonymous functions capture the same instance of the local variable `x`, and they can thus "communicate" through that variable.</span></span> <span data-ttu-id="8624e-2474">L'output dell'esempio è:</span><span class="sxs-lookup"><span data-stu-id="8624e-2474">The output of the example is:</span></span>
```
5
10
```

### <a name="evaluation-of-anonymous-function-expressions"></a><span data-ttu-id="8624e-2475">Valutazione delle espressioni di funzione anonima</span><span class="sxs-lookup"><span data-stu-id="8624e-2475">Evaluation of anonymous function expressions</span></span>

<span data-ttu-id="8624e-2476">Una funzione anonima `F` deve essere sempre convertita in un tipo delegato `D` o un tipo di albero delle espressioni `E`, direttamente o tramite l'esecuzione di un'espressione di creazione del delegato `new D(F)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2476">An anonymous function `F` must always be converted to a delegate type `D` or an expression tree type `E`, either directly or through the execution of a delegate creation expression `new D(F)`.</span></span> <span data-ttu-id="8624e-2477">Questa conversione determina il risultato della funzione anonima, come descritto in [conversioni delle funzioni anonime](conversions.md#anonymous-function-conversions).</span><span class="sxs-lookup"><span data-stu-id="8624e-2477">This conversion determines the result of the anonymous function, as described in [Anonymous function conversions](conversions.md#anonymous-function-conversions).</span></span>

## <a name="query-expressions"></a><span data-ttu-id="8624e-2478">Espressioni di query</span><span class="sxs-lookup"><span data-stu-id="8624e-2478">Query expressions</span></span>

<span data-ttu-id="8624e-2479">***Le espressioni di query*** offrono una sintassi integrata di linguaggio per le query che è simile ai linguaggi di query gerarchici e relazionali come SQL e XQuery.</span><span class="sxs-lookup"><span data-stu-id="8624e-2479">***Query expressions*** provide a language integrated syntax for queries that is similar to relational and hierarchical query languages such as SQL and XQuery.</span></span>

```antlr
query_expression
    : from_clause query_body
    ;

from_clause
    : 'from' type? identifier 'in' expression
    ;

query_body
    : query_body_clauses? select_or_group_clause query_continuation?
    ;

query_body_clauses
    : query_body_clause
    | query_body_clauses query_body_clause
    ;

query_body_clause
    : from_clause
    | let_clause
    | where_clause
    | join_clause
    | join_into_clause
    | orderby_clause
    ;

let_clause
    : 'let' identifier '=' expression
    ;

where_clause
    : 'where' boolean_expression
    ;

join_clause
    : 'join' type? identifier 'in' expression 'on' expression 'equals' expression
    ;

join_into_clause
    : 'join' type? identifier 'in' expression 'on' expression 'equals' expression 'into' identifier
    ;

orderby_clause
    : 'orderby' orderings
    ;

orderings
    : ordering (',' ordering)*
    ;

ordering
    : expression ordering_direction?
    ;

ordering_direction
    : 'ascending'
    | 'descending'
    ;

select_or_group_clause
    : select_clause
    | group_clause
    ;

select_clause
    : 'select' expression
    ;

group_clause
    : 'group' expression 'by' expression
    ;

query_continuation
    : 'into' identifier query_body
    ;
```

<span data-ttu-id="8624e-2480">Un'espressione di query inizia con un `from` clausola e termina con un `select` o `group` clausola.</span><span class="sxs-lookup"><span data-stu-id="8624e-2480">A query expression begins with a `from` clause and ends with either a `select` or `group` clause.</span></span> <span data-ttu-id="8624e-2481">Iniziale `from` clausola può essere seguita da zero o più `from`, `let`, `where`, `join` o `orderby` clausole.</span><span class="sxs-lookup"><span data-stu-id="8624e-2481">The initial `from` clause can be followed by zero or more `from`, `let`, `where`, `join` or `orderby` clauses.</span></span> <span data-ttu-id="8624e-2482">Ciascun `from` clausola è un generatore di introduzione a un ***variabile di intervallo*** che include gli elementi di una ***sequenza***.</span><span class="sxs-lookup"><span data-stu-id="8624e-2482">Each `from` clause is a generator introducing a ***range variable*** which ranges over the elements of a ***sequence***.</span></span> <span data-ttu-id="8624e-2483">Ogni `let` clausola introduce una variabile di intervallo che rappresenta un valore calcolato mediante variabili di intervallo precedente.</span><span class="sxs-lookup"><span data-stu-id="8624e-2483">Each `let` clause introduces a range variable representing a value computed by means of previous range variables.</span></span> <span data-ttu-id="8624e-2484">Ogni `where` clausola è un filtro che escluda gli elementi dal risultato.</span><span class="sxs-lookup"><span data-stu-id="8624e-2484">Each `where` clause is a filter that excludes items from the result.</span></span> <span data-ttu-id="8624e-2485">Ogni `join` clausola WHERE che confronta le chiavi specificate della sequenza di origine con le chiavi di un'altra sequenza, producendo coppie corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="8624e-2485">Each `join` clause compares specified keys of the source sequence with keys of another sequence, yielding matching pairs.</span></span> <span data-ttu-id="8624e-2486">Ogni `orderby` clausola Riordina gli elementi in base ai criteri specificati. L'elemento finale `select` o `group` clausola specifica la forma del risultato in termini di variabili di intervallo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2486">Each `orderby` clause reorders items according to specified criteria.The final `select` or `group` clause specifies the shape of the result in terms of the range variables.</span></span> <span data-ttu-id="8624e-2487">Infine, un `into` clausola può essere utilizzata da "inserire" query trattando i risultati di una query come un generatore in una query successiva.</span><span class="sxs-lookup"><span data-stu-id="8624e-2487">Finally, an `into` clause can be used to "splice" queries by treating the results of one query as a generator in a subsequent query.</span></span>

### <a name="ambiguities-in-query-expressions"></a><span data-ttu-id="8624e-2488">Ambiguità nelle espressioni di query</span><span class="sxs-lookup"><span data-stu-id="8624e-2488">Ambiguities in query expressions</span></span>

<span data-ttu-id="8624e-2489">Le espressioni di query contengono un numero di "parole chiave contestuali", ad esempio, gli identificatori che hanno un significato speciale in un contesto specificato.</span><span class="sxs-lookup"><span data-stu-id="8624e-2489">Query expressions contain a number of "contextual keywords", i.e., identifiers that have special meaning in a given context.</span></span> <span data-ttu-id="8624e-2490">In particolare tratta `from`, `where`, `join`, `on`, `equals`, `into`, `let`, `orderby`, `ascending`, `descending`, `select`, `group` e `by`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2490">Specifically these are `from`, `where`, `join`, `on`, `equals`, `into`, `let`, `orderby`, `ascending`, `descending`, `select`, `group` and `by`.</span></span> <span data-ttu-id="8624e-2491">Per evitare ambiguità in espressioni di query ha causate dall'utilizzo misto di questi identificatori come nomi semplici o le parole chiave, questi identificatori sono considerati le parole chiave quando si verificano in un punto qualsiasi all'interno di un'espressione di query.</span><span class="sxs-lookup"><span data-stu-id="8624e-2491">In order to avoid ambiguities in query expressions caused by mixed use of these identifiers as keywords or simple names, these identifiers are considered keywords when occurring anywhere within a query expression.</span></span>

<span data-ttu-id="8624e-2492">A tale scopo, un'espressione di query è qualsiasi espressione che inizia con "`from identifier`"seguito da tutti i token ad eccezione di"`;`","`=`"o"`,`".</span><span class="sxs-lookup"><span data-stu-id="8624e-2492">For this purpose, a query expression is any expression that starts with "`from identifier`" followed by any token except "`;`", "`=`" or "`,`".</span></span>

<span data-ttu-id="8624e-2493">Per usare queste parole come identificatori all'interno di un'espressione di query, possono essere preceduti da "`@`" ([identificatori](lexical-structure.md#identifiers)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2493">In order to use these words as identifiers within a query expression, they can be prefixed with "`@`" ([Identifiers](lexical-structure.md#identifiers)).</span></span>

### <a name="query-expression-translation"></a><span data-ttu-id="8624e-2494">Conversione dell'espressione di query</span><span class="sxs-lookup"><span data-stu-id="8624e-2494">Query expression translation</span></span>

<span data-ttu-id="8624e-2495">Il linguaggio c# non specifica la semantica di esecuzione delle espressioni di query.</span><span class="sxs-lookup"><span data-stu-id="8624e-2495">The C# language does not specify the execution semantics of query expressions.</span></span> <span data-ttu-id="8624e-2496">Piuttosto, le espressioni di query vengono convertite in chiamate dei metodi che rispettano il *criterio di espressione di query* ([il criterio di espressione di query](expressions.md#the-query-expression-pattern)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2496">Rather, query expressions are translated into invocations of methods that adhere to the *query expression pattern* ([The query expression pattern](expressions.md#the-query-expression-pattern)).</span></span> <span data-ttu-id="8624e-2497">In particolare, le espressioni di query vengono convertite in chiamate di metodi denominati `Where`, `Select`, `SelectMany`, `Join`, `GroupJoin`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `GroupBy`, e `Cast`. Questi metodi devono avere firme particolare e tipi di risultati, come descritto in [il criterio di espressione di query](expressions.md#the-query-expression-pattern).</span><span class="sxs-lookup"><span data-stu-id="8624e-2497">Specifically, query expressions are translated into invocations of methods named `Where`, `Select`, `SelectMany`, `Join`, `GroupJoin`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `GroupBy`, and `Cast`.These methods are expected to have particular signatures and result types, as described in [The query expression pattern](expressions.md#the-query-expression-pattern).</span></span> <span data-ttu-id="8624e-2498">Questi metodi possono essere metodi di istanza dell'oggetto sottoposto a query o i metodi di estensione che sono esterni all'oggetto, e implementare l'esecuzione effettiva della query.</span><span class="sxs-lookup"><span data-stu-id="8624e-2498">These methods can be instance methods of the object being queried or extension methods that are external to the object, and they implement the actual execution of the query.</span></span>

<span data-ttu-id="8624e-2499">La traduzione da espressioni di query per le chiamate ai metodi è un mapping sintattico che si verifica prima di qualsiasi tipo di binding o è stata eseguita la risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="8624e-2499">The translation from query expressions to method invocations is a syntactic mapping that occurs before any type binding or overload resolution has been performed.</span></span> <span data-ttu-id="8624e-2500">La traduzione è garantito che sia sintatticamente corretta, ma non è garantito per produrre codice c# semanticamente corretto.</span><span class="sxs-lookup"><span data-stu-id="8624e-2500">The translation is guaranteed to be syntactically correct, but it is not guaranteed to produce semantically correct C# code.</span></span> <span data-ttu-id="8624e-2501">In seguito alla conversione di espressioni di query, le chiamate al metodo risultante vengono elaborati come chiamate di metodo normale e ciò a sua volta potrebbe emergere errori, ad esempio, se non sono presenti i metodi, se gli argomenti hanno tipi non corretti o se i metodi sono generici e l'inferenza del tipo ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2501">Following translation of query expressions, the resulting method invocations are processed as regular method invocations, and this may in turn uncover errors, for example if the methods do not exist, if arguments have wrong types, or if the methods are generic and type inference fails.</span></span>

<span data-ttu-id="8624e-2502">Un'espressione di query viene elaborata tramite l'applicazione più volte le seguenti traduzioni fino a quando non sono possibili non ulteriori riduzioni.</span><span class="sxs-lookup"><span data-stu-id="8624e-2502">A query expression is processed by repeatedly applying the following translations until no further reductions are possible.</span></span> <span data-ttu-id="8624e-2503">Le traduzioni sono elencate in ordine di applicazione: ogni sezione si presuppone che le traduzioni nelle sezioni precedenti sono state eseguite con attenzione e una volta esaurito, una sezione sarà non in un secondo momento è possibile accedere durante l'elaborazione dell'espressione di query stessa.</span><span class="sxs-lookup"><span data-stu-id="8624e-2503">The translations are listed in order of application: each section assumes that the translations in the preceding sections have been performed exhaustively, and once exhausted, a section will not later be revisited in the processing of the same query expression.</span></span>

<span data-ttu-id="8624e-2504">Assegnazione alle variabili di intervallo non è consentito nelle espressioni di query.</span><span class="sxs-lookup"><span data-stu-id="8624e-2504">Assignment to range variables is not allowed in query expressions.</span></span> <span data-ttu-id="8624e-2505">Un'implementazione c# può tuttavia non impone questa limitazione, poiché ciò potrebbe talvolta non essere possibile con lo schema di conversione sintattica presentato in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2505">However a C# implementation is permitted to not always enforce this restriction, since this may sometimes not be possible with the syntactic translation scheme presented here.</span></span>

<span data-ttu-id="8624e-2506">Alcune conversioni inseriscono le variabili di intervallo con identificatori trasparenti indicati da `*`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2506">Certain translations inject range variables with transparent identifiers denoted by `*`.</span></span> <span data-ttu-id="8624e-2507">Vengono illustrate le proprietà speciali degli identificatori trasparente ulteriormente in [trasparenti identificatori](expressions.md#transparent-identifiers).</span><span class="sxs-lookup"><span data-stu-id="8624e-2507">The special properties of transparent identifiers are discussed further in [Transparent identifiers](expressions.md#transparent-identifiers).</span></span>

#### <a name="select-and-groupby-clauses-with-continuations"></a><span data-ttu-id="8624e-2508">Clausole SELECT e groupby con le continuazioni</span><span class="sxs-lookup"><span data-stu-id="8624e-2508">Select and groupby clauses with continuations</span></span>

<span data-ttu-id="8624e-2509">Un'espressione di query con una continuazione</span><span class="sxs-lookup"><span data-stu-id="8624e-2509">A query expression with a continuation</span></span>
```csharp
from ... into x ...
```
<span data-ttu-id="8624e-2510">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2510">is translated into</span></span>
```csharp
from x in ( from ... ) ...
```

<span data-ttu-id="8624e-2511">Le traduzioni nelle sezioni seguenti si presuppongono che le query hanno no `into` le continuazioni.</span><span class="sxs-lookup"><span data-stu-id="8624e-2511">The translations in the following sections assume that queries have no `into` continuations.</span></span>

<span data-ttu-id="8624e-2512">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2512">The example</span></span>
```csharp
from c in customers
group c by c.Country into g
select new { Country = g.Key, CustCount = g.Count() }
```
<span data-ttu-id="8624e-2513">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2513">is translated into</span></span>
```csharp
from g in
    from c in customers
    group c by c.Country
select new { Country = g.Key, CustCount = g.Count() }
```
<span data-ttu-id="8624e-2514">la traduzione finale di cui è</span><span class="sxs-lookup"><span data-stu-id="8624e-2514">the final translation of which is</span></span>
```csharp
customers.
GroupBy(c => c.Country).
Select(g => new { Country = g.Key, CustCount = g.Count() })
```

#### <a name="explicit-range-variable-types"></a><span data-ttu-id="8624e-2515">Tipi di variabile di intervallo esplicita</span><span class="sxs-lookup"><span data-stu-id="8624e-2515">Explicit range variable types</span></span>

<span data-ttu-id="8624e-2516">Oggetto `from` clausola che specifica in modo esplicito un tipo di variabile di intervallo</span><span class="sxs-lookup"><span data-stu-id="8624e-2516">A `from` clause that explicitly specifies a range variable type</span></span>
```csharp
from T x in e
```
<span data-ttu-id="8624e-2517">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2517">is translated into</span></span>
```csharp
from x in ( e ) . Cast < T > ( )
```

<span data-ttu-id="8624e-2518">Oggetto `join` clausola che specifica in modo esplicito un tipo di variabile di intervallo</span><span class="sxs-lookup"><span data-stu-id="8624e-2518">A `join` clause that explicitly specifies a range variable type</span></span>
```
join T x in e on k1 equals k2
```
<span data-ttu-id="8624e-2519">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2519">is translated into</span></span>
```
join x in ( e ) . Cast < T > ( ) on k1 equals k2
```

<span data-ttu-id="8624e-2520">Le traduzioni nelle sezioni seguenti presuppongono che le query non hanno nessun tipo di variabile di intervallo esplicita.</span><span class="sxs-lookup"><span data-stu-id="8624e-2520">The translations in the following sections assume that queries have no explicit range variable types.</span></span>

<span data-ttu-id="8624e-2521">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2521">The example</span></span>
```csharp
from Customer c in customers
where c.City == "London"
select c
```
<span data-ttu-id="8624e-2522">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2522">is translated into</span></span>
```csharp
from c in customers.Cast<Customer>()
where c.City == "London"
select c
```
<span data-ttu-id="8624e-2523">la traduzione finale di cui è</span><span class="sxs-lookup"><span data-stu-id="8624e-2523">the final translation of which is</span></span>
```csharp
customers.
Cast<Customer>().
Where(c => c.City == "London")
```

<span data-ttu-id="8624e-2524">Tipi di variabile di intervallo espliciti sono utili per l'esecuzione di query le raccolte che implementano non generica `IEnumerable` interfaccia, ma non il tipo generico `IEnumerable<T>` interfaccia.</span><span class="sxs-lookup"><span data-stu-id="8624e-2524">Explicit range variable types are useful for querying collections that implement the non-generic `IEnumerable` interface, but not the generic `IEnumerable<T>` interface.</span></span> <span data-ttu-id="8624e-2525">Nell'esempio precedente, questo sarebbe il caso se `customers` erano di tipo `ArrayList`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2525">In the example above, this would be the case if `customers` were of type `ArrayList`.</span></span>

#### <a name="degenerate-query-expressions"></a><span data-ttu-id="8624e-2526">Espressioni di query degenere</span><span class="sxs-lookup"><span data-stu-id="8624e-2526">Degenerate query expressions</span></span>

<span data-ttu-id="8624e-2527">Un'espressione di query del form</span><span class="sxs-lookup"><span data-stu-id="8624e-2527">A query expression of the form</span></span>
```csharp
from x in e select x
```
<span data-ttu-id="8624e-2528">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2528">is translated into</span></span>
```csharp
( e ) . Select ( x => x )
```

<span data-ttu-id="8624e-2529">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2529">The example</span></span>
```csharp
from c in customers
select c
```
<span data-ttu-id="8624e-2530">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2530">is translated into</span></span>
```csharp
customers.Select(c => c)
```

<span data-ttu-id="8624e-2531">Un'espressione di query degenere è quella più facilmente seleziona gli elementi dell'origine.</span><span class="sxs-lookup"><span data-stu-id="8624e-2531">A degenerate query expression is one that trivially selects the elements of the source.</span></span> <span data-ttu-id="8624e-2532">In una fase successiva della traduzione rimuove degenere query inserite da altri passaggi di traduzione sostituendoli con relativa origine.</span><span class="sxs-lookup"><span data-stu-id="8624e-2532">A later phase of the translation removes degenerate queries introduced by other translation steps by replacing them with their source.</span></span> <span data-ttu-id="8624e-2533">È importante tuttavia per garantire che il risultato di una query espressione non è mai l'oggetto di origine stesso, che potrebbe rivelare il tipo e l'identità dell'origine per il client della query.</span><span class="sxs-lookup"><span data-stu-id="8624e-2533">It is important however to ensure that the result of a query expression is never the source object itself, as that would reveal the type and identity of the source to the client of the query.</span></span> <span data-ttu-id="8624e-2534">Di conseguenza questo passaggio consente di proteggere degenere query scritte direttamente nel codice sorgente in modo esplicito chiamando `Select` nell'origine.</span><span class="sxs-lookup"><span data-stu-id="8624e-2534">Therefore this step protects degenerate queries written directly in source code by explicitly calling `Select` on the source.</span></span> <span data-ttu-id="8624e-2535">Spetta poi gli implementatori di `Select` e altri operatori di query per garantire che questi metodi non restituiscono mai l'oggetto di origine stesso.</span><span class="sxs-lookup"><span data-stu-id="8624e-2535">It is then up to the implementers of `Select` and other query operators to ensure that these methods never return the source object itself.</span></span>

#### <a name="from-let-where-join-and-orderby-clauses"></a><span data-ttu-id="8624e-2536">Dal, let, where, clausole join e orderby</span><span class="sxs-lookup"><span data-stu-id="8624e-2536">From, let, where, join and orderby clauses</span></span>

<span data-ttu-id="8624e-2537">Un'espressione di query con una seconda `from` clausola seguita da un `select` clausola</span><span class="sxs-lookup"><span data-stu-id="8624e-2537">A query expression with a second `from` clause followed by a `select` clause</span></span>
```csharp
from x1 in e1
from x2 in e2
select v
```
<span data-ttu-id="8624e-2538">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2538">is translated into</span></span>
```csharp
( e1 ) . SelectMany( x1 => e2 , ( x1 , x2 ) => v )
```

<span data-ttu-id="8624e-2539">Un'espressione di query con una seconda `from` clausola seguita da un elemento diverso da un `select` clausola:</span><span class="sxs-lookup"><span data-stu-id="8624e-2539">A query expression with a second `from` clause followed by something other than a `select` clause:</span></span>

```csharp
from x1 in e1
from x2 in e2
...
```
<span data-ttu-id="8624e-2540">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2540">is translated into</span></span>
```csharp
from * in ( e1 ) . SelectMany( x1 => e2 , ( x1 , x2 ) => new { x1 , x2 } )
...
```

<span data-ttu-id="8624e-2541">Un'espressione di query con un `let` clausola</span><span class="sxs-lookup"><span data-stu-id="8624e-2541">A query expression with a `let` clause</span></span>
```csharp
from x in e
let y = f
...
```
<span data-ttu-id="8624e-2542">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2542">is translated into</span></span>
```csharp
from * in ( e ) . Select ( x => new { x , y = f } )
...
```

<span data-ttu-id="8624e-2543">Un'espressione di query con un `where` clausola</span><span class="sxs-lookup"><span data-stu-id="8624e-2543">A query expression with a `where` clause</span></span>
```csharp
from x in e
where f
...
```
<span data-ttu-id="8624e-2544">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2544">is translated into</span></span>
```csharp
from x in ( e ) . Where ( x => f )
...
```

<span data-ttu-id="8624e-2545">Un'espressione di query con un `join` clausola senza un' `into` seguita da un `select` clausola</span><span class="sxs-lookup"><span data-stu-id="8624e-2545">A query expression with a `join` clause without an `into` followed by a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2
select v
```
<span data-ttu-id="8624e-2546">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2546">is translated into</span></span>
```csharp
( e1 ) . Join( e2 , x1 => k1 , x2 => k2 , ( x1 , x2 ) => v )
```

<span data-ttu-id="8624e-2547">Un'espressione di query con un `join` clausola senza un' `into` seguita da un elemento diverso da un `select` clausola</span><span class="sxs-lookup"><span data-stu-id="8624e-2547">A query expression with a `join` clause without an `into` followed by something other than a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2
...
```
<span data-ttu-id="8624e-2548">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2548">is translated into</span></span>
```csharp
from * in ( e1 ) . Join( e2 , x1 => k1 , x2 => k2 , ( x1 , x2 ) => new { x1 , x2 })
...
```

<span data-ttu-id="8624e-2549">Un'espressione di query con un `join` clausola con un `into` seguita da un `select` clausola</span><span class="sxs-lookup"><span data-stu-id="8624e-2549">A query expression with a `join` clause with an `into` followed by a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2 into g
select v
```
<span data-ttu-id="8624e-2550">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2550">is translated into</span></span>
```csharp
( e1 ) . GroupJoin( e2 , x1 => k1 , x2 => k2 , ( x1 , g ) => v )
```

<span data-ttu-id="8624e-2551">Un'espressione di query con un `join` clausola con un `into` seguita da un elemento diverso da un `select` clausola</span><span class="sxs-lookup"><span data-stu-id="8624e-2551">A query expression with a `join` clause with an `into` followed by something other than a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2 into g
...
```
<span data-ttu-id="8624e-2552">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2552">is translated into</span></span>
```csharp
from * in ( e1 ) . GroupJoin( e2 , x1 => k1 , x2 => k2 , ( x1 , g ) => new { x1 , g })
...
```

<span data-ttu-id="8624e-2553">Un'espressione di query con un `orderby` clausola</span><span class="sxs-lookup"><span data-stu-id="8624e-2553">A query expression with an `orderby` clause</span></span>
```csharp
from x in e
orderby k1 , k2 , ..., kn
...
```
<span data-ttu-id="8624e-2554">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2554">is translated into</span></span>
```csharp
from x in ( e ) . 
OrderBy ( x => k1 ) . 
ThenBy ( x => k2 ) .
... .
ThenBy ( x => kn )
...
```

<span data-ttu-id="8624e-2555">Se un ordinamento clausola che specifica un `descending` indicatori di direzione, una chiamata di `OrderByDescending` o `ThenByDescending` viene invece generata.</span><span class="sxs-lookup"><span data-stu-id="8624e-2555">If an ordering clause specifies a `descending` direction indicator, an invocation of `OrderByDescending` or `ThenByDescending` is produced instead.</span></span>

<span data-ttu-id="8624e-2556">Per le conversioni seguenti presuppongono che non esistono nessun `let`, `where`, `join` oppure `orderby` clausole e non più di un iniziale `from` clausola in ogni espressione di query.</span><span class="sxs-lookup"><span data-stu-id="8624e-2556">The following translations assume that there are no `let`, `where`, `join` or `orderby` clauses, and no more than the one initial `from` clause in each query expression.</span></span>

<span data-ttu-id="8624e-2557">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2557">The example</span></span>
```csharp
from c in customers
from o in c.Orders
select new { c.Name, o.OrderID, o.Total }
```
<span data-ttu-id="8624e-2558">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2558">is translated into</span></span>
```csharp
customers.
SelectMany(c => c.Orders,
     (c,o) => new { c.Name, o.OrderID, o.Total }
)
```

<span data-ttu-id="8624e-2559">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2559">The example</span></span>
```csharp
from c in customers
from o in c.Orders
orderby o.Total descending
select new { c.Name, o.OrderID, o.Total }
```
<span data-ttu-id="8624e-2560">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2560">is translated into</span></span>
```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.OrderID, o.Total }
```
<span data-ttu-id="8624e-2561">la traduzione finale di cui è</span><span class="sxs-lookup"><span data-stu-id="8624e-2561">the final translation of which is</span></span>
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.OrderID, x.o.Total })
```
<span data-ttu-id="8624e-2562">in cui `x` è un identificatore generato dal compilatore che è altrimenti invisibili e inaccessibili.</span><span class="sxs-lookup"><span data-stu-id="8624e-2562">where `x` is a compiler generated identifier that is otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="8624e-2563">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2563">The example</span></span>
```csharp
from o in orders
let t = o.Details.Sum(d => d.UnitPrice * d.Quantity)
where t >= 1000
select new { o.OrderID, Total = t }
```
<span data-ttu-id="8624e-2564">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2564">is translated into</span></span>
```csharp
from * in orders.
    Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) })
where t >= 1000 
select new { o.OrderID, Total = t }
```
<span data-ttu-id="8624e-2565">la traduzione finale di cui è</span><span class="sxs-lookup"><span data-stu-id="8624e-2565">the final translation of which is</span></span>
```csharp
orders.
Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) }).
Where(x => x.t >= 1000).
Select(x => new { x.o.OrderID, Total = x.t })
```
<span data-ttu-id="8624e-2566">in cui `x` è un identificatore generato dal compilatore che è altrimenti invisibili e inaccessibili.</span><span class="sxs-lookup"><span data-stu-id="8624e-2566">where `x` is a compiler generated identifier that is otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="8624e-2567">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2567">The example</span></span>
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
select new { c.Name, o.OrderDate, o.Total }
```
<span data-ttu-id="8624e-2568">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2568">is translated into</span></span>
```csharp
customers.Join(orders, c => c.CustomerID, o => o.CustomerID,
    (c, o) => new { c.Name, o.OrderDate, o.Total })
```

<span data-ttu-id="8624e-2569">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2569">The example</span></span>
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID into co
let n = co.Count()
where n >= 10
select new { c.Name, OrderCount = n }
```
<span data-ttu-id="8624e-2570">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2570">is translated into</span></span>
```csharp
from * in customers.
    GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
        (c, co) => new { c, co })
let n = co.Count()
where n >= 10 
select new { c.Name, OrderCount = n }
```
<span data-ttu-id="8624e-2571">la traduzione finale di cui è</span><span class="sxs-lookup"><span data-stu-id="8624e-2571">the final translation of which is</span></span>
```csharp
customers.
GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
    (c, co) => new { c, co }).
Select(x => new { x, n = x.co.Count() }).
Where(y => y.n >= 10).
Select(y => new { y.x.c.Name, OrderCount = y.n)
```
<span data-ttu-id="8624e-2572">in cui `x` e `y` sono identificatori generato dal compilatore che altrimenti risultano invisibile e non accessibili.</span><span class="sxs-lookup"><span data-stu-id="8624e-2572">where `x` and `y` are compiler generated identifiers that are otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="8624e-2573">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2573">The example</span></span>
```csharp
from o in orders
orderby o.Customer.Name, o.Total descending
select o
```
<span data-ttu-id="8624e-2574">la conversione finale è</span><span class="sxs-lookup"><span data-stu-id="8624e-2574">has the final translation</span></span>
```csharp
orders.
OrderBy(o => o.Customer.Name).
ThenByDescending(o => o.Total)
```

#### <a name="select-clauses"></a><span data-ttu-id="8624e-2575">Clausole SELECT</span><span class="sxs-lookup"><span data-stu-id="8624e-2575">Select clauses</span></span>

<span data-ttu-id="8624e-2576">Un'espressione di query del form</span><span class="sxs-lookup"><span data-stu-id="8624e-2576">A query expression of the form</span></span>
```csharp
from x in e select v
```
<span data-ttu-id="8624e-2577">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2577">is translated into</span></span>
```csharp
( e ) . Select ( x => v )
```
<span data-ttu-id="8624e-2578">tranne quando v è l'identificatore di x, la conversione è semplicemente</span><span class="sxs-lookup"><span data-stu-id="8624e-2578">except when v is the identifier x, the translation is simply</span></span>
```csharp
( e )
```

<span data-ttu-id="8624e-2579">Esempio:</span><span class="sxs-lookup"><span data-stu-id="8624e-2579">For example</span></span>
```csharp
from c in customers.Where(c => c.City == "London")
select c
```
<span data-ttu-id="8624e-2580">viene convertito semplicemente in</span><span class="sxs-lookup"><span data-stu-id="8624e-2580">is simply translated into</span></span>
```csharp
customers.Where(c => c.City == "London")
```

#### <a name="groupby-clauses"></a><span data-ttu-id="8624e-2581">Clausole di GroupBy</span><span class="sxs-lookup"><span data-stu-id="8624e-2581">Groupby clauses</span></span>

<span data-ttu-id="8624e-2582">Un'espressione di query del form</span><span class="sxs-lookup"><span data-stu-id="8624e-2582">A query expression of the form</span></span>
```csharp
from x in e group v by k
```
<span data-ttu-id="8624e-2583">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2583">is translated into</span></span>
```csharp
( e ) . GroupBy ( x => k , x => v )
```
<span data-ttu-id="8624e-2584">tranne quando v è l'identificatore è la traduzione di x,</span><span class="sxs-lookup"><span data-stu-id="8624e-2584">except when v is the identifier x, the translation is</span></span>
```csharp
( e ) . GroupBy ( x => k )
```

<span data-ttu-id="8624e-2585">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2585">The example</span></span>
```csharp
from c in customers
group c.Name by c.Country
```
<span data-ttu-id="8624e-2586">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2586">is translated into</span></span>
```csharp
customers.
GroupBy(c => c.Country, c => c.Name)
```

#### <a name="transparent-identifiers"></a><span data-ttu-id="8624e-2587">Identificatori trasparenti</span><span class="sxs-lookup"><span data-stu-id="8624e-2587">Transparent identifiers</span></span>

<span data-ttu-id="8624e-2588">Alcune conversioni inseriscono le variabili di intervallo con ***identificatori trasparenti*** indicato da `*`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2588">Certain translations inject range variables with ***transparent identifiers*** denoted by `*`.</span></span> <span data-ttu-id="8624e-2589">Identificatori trasparenti non sono una funzionalità del linguaggio appropriata; Esistono solo come passaggio intermedio nel processo di conversione di espressione di query.</span><span class="sxs-lookup"><span data-stu-id="8624e-2589">Transparent identifiers are not a proper language feature; they exist only as an intermediate step in the query expression translation process.</span></span>

<span data-ttu-id="8624e-2590">Quando una conversione di query inserisce un identificatore trasparente, ulteriormente i passaggi di traduzione propagano l'identificatore trasparente in funzioni anonime e inizializzatori di oggetti anonimi.</span><span class="sxs-lookup"><span data-stu-id="8624e-2590">When a query translation injects a transparent identifier, further translation steps propagate the transparent identifier into anonymous functions and anonymous object initializers.</span></span> <span data-ttu-id="8624e-2591">In questi contesti, identificatori trasparenti comportano nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="8624e-2591">In those contexts, transparent identifiers have the following behavior:</span></span>

*  <span data-ttu-id="8624e-2592">Quando si verifica un identificatore trasparente come parametro in una funzione anonima, i membri del tipo anonimo associato vengono automaticamente nell'ambito del corpo della funzione anonima.</span><span class="sxs-lookup"><span data-stu-id="8624e-2592">When a transparent identifier occurs as a parameter in an anonymous function, the members of the associated anonymous type are automatically in scope in the body of the anonymous function.</span></span>
*  <span data-ttu-id="8624e-2593">Quando un membro con un identificatore trasparente è nell'ambito, i membri di tale membro rientrano nell'ambito anche.</span><span class="sxs-lookup"><span data-stu-id="8624e-2593">When a member with a transparent identifier is in scope, the members of that member are in scope as well.</span></span>
*  <span data-ttu-id="8624e-2594">Quando si verifica un identificatore trasparente come un dichiaratore di membro in un inizializzatore di oggetto anonimi, introduce un membro con un identificatore trasparente.</span><span class="sxs-lookup"><span data-stu-id="8624e-2594">When a transparent identifier occurs as a member declarator in an anonymous object initializer, it introduces a member with a transparent identifier.</span></span>
*  <span data-ttu-id="8624e-2595">Nei passaggi della conversione descritti in precedenza, gli identificatori trasparenti vengono sempre introdotte insieme ai tipi anonimi, con l'intenzione di acquisire più variabili di intervallo come membri di un singolo oggetto.</span><span class="sxs-lookup"><span data-stu-id="8624e-2595">In the translation steps described above, transparent identifiers are always introduced together with anonymous types, with the intent of capturing multiple range variables as members of a single object.</span></span> <span data-ttu-id="8624e-2596">Un'implementazione del linguaggio c# può utilizzare un meccanismo diverso rispetto ai tipi anonimi per raggruppare più variabili di intervallo.</span><span class="sxs-lookup"><span data-stu-id="8624e-2596">An implementation of C# is permitted to use a different mechanism than anonymous types to group together multiple range variables.</span></span> <span data-ttu-id="8624e-2597">Gli esempi di conversione seguenti presuppongono che vengono usati tipi anonimi e mostrano come trasparenti identificatori possono essere convertiti da subito.</span><span class="sxs-lookup"><span data-stu-id="8624e-2597">The following translation examples assume that anonymous types are used, and show how transparent identifiers can be translated away.</span></span>

<span data-ttu-id="8624e-2598">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2598">The example</span></span>
```csharp
from c in customers
from o in c.Orders
orderby o.Total descending
select new { c.Name, o.Total }
```
<span data-ttu-id="8624e-2599">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2599">is translated into</span></span>
```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.Total }
```

<span data-ttu-id="8624e-2600">che viene ulteriormente tradotti</span><span class="sxs-lookup"><span data-stu-id="8624e-2600">which is further translated into</span></span>
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(* => o.Total).
Select(* => new { c.Name, o.Total })
```
<span data-ttu-id="8624e-2601">che, quando vengono cancellati gli identificatori trasparenti, equivale a</span><span class="sxs-lookup"><span data-stu-id="8624e-2601">which, when transparent identifiers are erased, is equivalent to</span></span>
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.Total })
```
<span data-ttu-id="8624e-2602">in cui `x` è un identificatore generato dal compilatore che è altrimenti invisibili e inaccessibili.</span><span class="sxs-lookup"><span data-stu-id="8624e-2602">where `x` is a compiler generated identifier that is otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="8624e-2603">L'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2603">The example</span></span>
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```
<span data-ttu-id="8624e-2604">viene convertito in</span><span class="sxs-lookup"><span data-stu-id="8624e-2604">is translated into</span></span>
```csharp
from * in customers.
    Join(orders, c => c.CustomerID, o => o.CustomerID, 
        (c, o) => new { c, o })
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```
<span data-ttu-id="8624e-2605">che viene ulteriormente ridotta a</span><span class="sxs-lookup"><span data-stu-id="8624e-2605">which is further reduced to</span></span>
```csharp
customers.
Join(orders, c => c.CustomerID, o => o.CustomerID, (c, o) => new { c, o }).
Join(details, * => o.OrderID, d => d.OrderID, (*, d) => new { *, d }).
Join(products, * => d.ProductID, p => p.ProductID, (*, p) => new { *, p }).
Select(* => new { c.Name, o.OrderDate, p.ProductName })
```
<span data-ttu-id="8624e-2606">la traduzione finale di cui è</span><span class="sxs-lookup"><span data-stu-id="8624e-2606">the final translation of which is</span></span>
```csharp
customers.
Join(orders, c => c.CustomerID, o => o.CustomerID,
    (c, o) => new { c, o }).
Join(details, x => x.o.OrderID, d => d.OrderID,
    (x, d) => new { x, d }).
Join(products, y => y.d.ProductID, p => p.ProductID,
    (y, p) => new { y, p }).
Select(z => new { z.y.x.c.Name, z.y.x.o.OrderDate, z.p.ProductName })
```
<span data-ttu-id="8624e-2607">in cui `x`, `y`, e `z` sono identificatori generato dal compilatore che altrimenti risultano invisibile e non accessibili.</span><span class="sxs-lookup"><span data-stu-id="8624e-2607">where `x`, `y`, and `z` are compiler generated identifiers that are otherwise invisible and inaccessible.</span></span>

### <a name="the-query-expression-pattern"></a><span data-ttu-id="8624e-2608">Il criterio di espressione di query</span><span class="sxs-lookup"><span data-stu-id="8624e-2608">The query expression pattern</span></span>

<span data-ttu-id="8624e-2609">Il ***criterio di espressione di Query*** stabilisce una serie di metodi che i tipi possono implementare per supportare le espressioni di query.</span><span class="sxs-lookup"><span data-stu-id="8624e-2609">The ***Query expression pattern*** establishes a pattern of methods that types can implement to support query expressions.</span></span> <span data-ttu-id="8624e-2610">Poiché le espressioni di query vengono convertite in chiamate al metodo tramite un mapping sintattico, i tipi includono una notevole flessibilità nella modalità di implementazione del modello di espressione di query.</span><span class="sxs-lookup"><span data-stu-id="8624e-2610">Because query expressions are translated to method invocations by means of a syntactic mapping, types have considerable flexibility in how they implement the query expression pattern.</span></span> <span data-ttu-id="8624e-2611">Ad esempio, i metodi del modello possono essere implementati come metodi di istanza o come metodi di estensione perché i due hanno la stessa sintassi di chiamata e i metodi possono richiedere delegati o alberi delle espressioni perché sono convertibili in entrambe le funzioni anonime.</span><span class="sxs-lookup"><span data-stu-id="8624e-2611">For example, the methods of the pattern can be implemented as instance methods or as extension methods because the two have the same invocation syntax, and the methods can request delegates or expression trees because anonymous functions are convertible to both.</span></span>

<span data-ttu-id="8624e-2612">La forma consigliata di un tipo generico `C<T>` che supporta il modello di espressione di query è illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="8624e-2612">The recommended shape of a generic type `C<T>` that supports the query expression pattern is shown below.</span></span> <span data-ttu-id="8624e-2613">Un tipo generico viene usato per illustrare le relazioni appropriate tra i tipi di parametro e il risultato, ma è possibile implementare il modello per nonché i tipi non generici.</span><span class="sxs-lookup"><span data-stu-id="8624e-2613">A generic type is used in order to illustrate the proper relationships between parameter and result types, but it is possible to implement the pattern for non-generic types as well.</span></span>

```csharp
delegate R Func<T1,R>(T1 arg1);

delegate R Func<T1,T2,R>(T1 arg1, T2 arg2);

class C
{
    public C<T> Cast<T>();
}

class C<T> : C
{
    public C<T> Where(Func<T,bool> predicate);

    public C<U> Select<U>(Func<T,U> selector);

    public C<V> SelectMany<U,V>(Func<T,C<U>> selector,
        Func<T,U,V> resultSelector);

    public C<V> Join<U,K,V>(C<U> inner, Func<T,K> outerKeySelector,
        Func<U,K> innerKeySelector, Func<T,U,V> resultSelector);

    public C<V> GroupJoin<U,K,V>(C<U> inner, Func<T,K> outerKeySelector,
        Func<U,K> innerKeySelector, Func<T,C<U>,V> resultSelector);

    public O<T> OrderBy<K>(Func<T,K> keySelector);

    public O<T> OrderByDescending<K>(Func<T,K> keySelector);

    public C<G<K,T>> GroupBy<K>(Func<T,K> keySelector);

    public C<G<K,E>> GroupBy<K,E>(Func<T,K> keySelector,
        Func<T,E> elementSelector);
}

class O<T> : C<T>
{
    public O<T> ThenBy<K>(Func<T,K> keySelector);

    public O<T> ThenByDescending<K>(Func<T,K> keySelector);
}

class G<K,T> : C<T>
{
    public K Key { get; }
}
```

<span data-ttu-id="8624e-2614">I metodi precedenti usano i tipi delegati generici `Func<T1,R>` e `Func<T1,T2,R>`, ma è stato altrettanto bene avere usati altri tipi di struttura ad albero di delegato o un'espressione con le stesse relazioni nei tipi di parametro e risultato.</span><span class="sxs-lookup"><span data-stu-id="8624e-2614">The methods above use the generic delegate types `Func<T1,R>` and `Func<T1,T2,R>`, but they could equally well have used other delegate or expression tree types with the same relationships in parameter and result types.</span></span>

<span data-ttu-id="8624e-2615">Si noti che la relazione tra consigliata `C<T>` e `O<T>` che assicura che le `ThenBy` e `ThenByDescending` metodi sono disponibili solo sul risultato di un' `OrderBy` o `OrderByDescending`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2615">Notice the recommended relationship between `C<T>` and `O<T>` which ensures that the `ThenBy` and `ThenByDescending` methods are available only on the result of an `OrderBy` or `OrderByDescending`.</span></span> <span data-ttu-id="8624e-2616">Si noti inoltre la forma del risultato di consigliata `GroupBy` : una sequenza di sequenze, in cui ogni sequenza interna ha un'ulteriore `Key` proprietà.</span><span class="sxs-lookup"><span data-stu-id="8624e-2616">Also notice the recommended shape of the result of `GroupBy` -- a sequence of sequences, where each inner sequence has an additional `Key` property.</span></span>

<span data-ttu-id="8624e-2617">Il `System.Linq` dello spazio dei nomi fornisce un'implementazione del modello di operatore di query per qualsiasi tipo che implementa il `System.Collections.Generic.IEnumerable<T>` interfaccia.</span><span class="sxs-lookup"><span data-stu-id="8624e-2617">The `System.Linq` namespace provides an implementation of the query operator pattern for any type that implements the `System.Collections.Generic.IEnumerable<T>` interface.</span></span>

## <a name="assignment-operators"></a><span data-ttu-id="8624e-2618">Operatori di assegnazione</span><span class="sxs-lookup"><span data-stu-id="8624e-2618">Assignment operators</span></span>

<span data-ttu-id="8624e-2619">Gli operatori di assegnazione assegnano un nuovo valore a una variabile, una proprietà, un evento o un elemento dell'indicizzatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2619">The assignment operators assign a new value to a variable, a property, an event, or an indexer element.</span></span>

```antlr
assignment
    : unary_expression assignment_operator expression
    ;

assignment_operator
    : '='
    | '+='
    | '-='
    | '*='
    | '/='
    | '%='
    | '&='
    | '|='
    | '^='
    | '<<='
    | right_shift_assignment
    ;
```

<span data-ttu-id="8624e-2620">L'operando sinistro di un'assegnazione deve essere un'espressione classificata come una variabile, un accesso a proprietà, un accesso all'indicizzatore o accesso a un evento.</span><span class="sxs-lookup"><span data-stu-id="8624e-2620">The left operand of an assignment must be an expression classified as a variable, a property access, an indexer access, or an event access.</span></span>

<span data-ttu-id="8624e-2621">Il `=` viene chiamato operatore le ***operatore di assegnazione semplice***.</span><span class="sxs-lookup"><span data-stu-id="8624e-2621">The `=` operator is called the ***simple assignment operator***.</span></span> <span data-ttu-id="8624e-2622">Assegna il valore dell'operando di destra alla variabile, proprietà o indicizzatore all'elemento rappresentato dall'operando sinistro.</span><span class="sxs-lookup"><span data-stu-id="8624e-2622">It assigns the value of the right operand to the variable, property, or indexer element given by the left operand.</span></span> <span data-ttu-id="8624e-2623">L'operando sinistro dell'operatore di assegnazione semplice potrebbe non essere l'accesso a un evento (ad eccezione di quanto descritto nella [di eventi campo](classes.md#field-like-events)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2623">The left operand of the simple assignment operator may not be an event access (except as described in [Field-like events](classes.md#field-like-events)).</span></span> <span data-ttu-id="8624e-2624">L'operatore di assegnazione semplice è descritto in [assegnamento semplice](expressions.md#simple-assignment).</span><span class="sxs-lookup"><span data-stu-id="8624e-2624">The simple assignment operator is described in [Simple assignment](expressions.md#simple-assignment).</span></span>

<span data-ttu-id="8624e-2625">Gli operatori di assegnazione diverso dal `=` operatore vengono chiamati i ***operatori di assegnazione composta***.</span><span class="sxs-lookup"><span data-stu-id="8624e-2625">The assignment operators other than the `=` operator are called the ***compound assignment operators***.</span></span> <span data-ttu-id="8624e-2626">Questi operatori eseguono l'operazione indicata su due operandi e quindi assegnare il valore risultante alla variabile, proprietà o indicizzatore all'elemento rappresentato dall'operando sinistro.</span><span class="sxs-lookup"><span data-stu-id="8624e-2626">These operators perform the indicated operation on the two operands, and then assign the resulting value to the variable, property, or indexer element given by the left operand.</span></span> <span data-ttu-id="8624e-2627">Operatori di assegnamento composti sono descritte nel [assegnazione composta](expressions.md#compound-assignment).</span><span class="sxs-lookup"><span data-stu-id="8624e-2627">The compound assignment operators are described in [Compound assignment](expressions.md#compound-assignment).</span></span>

<span data-ttu-id="8624e-2628">Il `+=` e `-=` operatori con un'espressione di accesso eventi come operando di sinistra vengono chiamati i *gli operatori di assegnazione di evento*.</span><span class="sxs-lookup"><span data-stu-id="8624e-2628">The `+=` and `-=` operators with an event access expression as the left operand are called the *event assignment operators*.</span></span> <span data-ttu-id="8624e-2629">Nessun altro operatore di assegnazione è valido con accesso a un evento come operando di sinistra.</span><span class="sxs-lookup"><span data-stu-id="8624e-2629">No other assignment operator is valid with an event access as the left operand.</span></span> <span data-ttu-id="8624e-2630">Gli operatori di assegnazione di eventi sono descritti [assegnazione di eventi](expressions.md#event-assignment).</span><span class="sxs-lookup"><span data-stu-id="8624e-2630">The event assignment operators are described in [Event assignment](expressions.md#event-assignment).</span></span>

<span data-ttu-id="8624e-2631">Gli operatori di assegnazione sono associativa a destra, ovvero le operazioni vengono eseguite da destra a sinistra.</span><span class="sxs-lookup"><span data-stu-id="8624e-2631">The assignment operators are right-associative, meaning that operations are grouped from right to left.</span></span> <span data-ttu-id="8624e-2632">Ad esempio, un'espressione nel formato `a = b = c` darà `a = (b = c)`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2632">For example, an expression of the form `a = b = c` is evaluated as `a = (b = c)`.</span></span>

### <a name="simple-assignment"></a><span data-ttu-id="8624e-2633">Assegnazione singola</span><span class="sxs-lookup"><span data-stu-id="8624e-2633">Simple assignment</span></span>

<span data-ttu-id="8624e-2634">Il `=` operatore viene chiamato operatore di assegnazione semplice.</span><span class="sxs-lookup"><span data-stu-id="8624e-2634">The `=` operator is called the simple assignment operator.</span></span>

<span data-ttu-id="8624e-2635">Se l'operando sinistro di un'assegnazione semplice è nel formato `E.P` oppure `E[Ei]` in cui `E` dispone del tipo in fase di compilazione `dynamic`, quindi l'assegnazione è associata in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2635">If the left operand of a simple assignment is of the form `E.P` or `E[Ei]` where `E` has the compile-time type `dynamic`, then the assignment is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="8624e-2636">In questo caso è il tipo di fase di compilazione dell'espressione di assegnazione `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione in base al tipo di runtime di `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2636">In this case the compile-time type of the assignment expression is `dynamic`, and the resolution described below will take place at run-time based on the run-time type of `E`.</span></span>

<span data-ttu-id="8624e-2637">In un'assegnazione semplice, l'operando destro deve essere un'espressione che è implicitamente convertibile nel tipo dell'operando sinistro.</span><span class="sxs-lookup"><span data-stu-id="8624e-2637">In a simple assignment, the right operand must be an expression that is implicitly convertible to the type of the left operand.</span></span> <span data-ttu-id="8624e-2638">L'operazione assegna il valore dell'operando di destra alla variabile, proprietà o indicizzatore all'elemento rappresentato dall'operando sinistro.</span><span class="sxs-lookup"><span data-stu-id="8624e-2638">The operation assigns the value of the right operand to the variable, property, or indexer element given by the left operand.</span></span>

<span data-ttu-id="8624e-2639">Il risultato di un'espressione di assegnazione semplice è il valore assegnato all'operando di sinistra.</span><span class="sxs-lookup"><span data-stu-id="8624e-2639">The result of a simple assignment expression is the value assigned to the left operand.</span></span> <span data-ttu-id="8624e-2640">Il risultato ha lo stesso tipo dell'operando di sinistra ed è sempre classificato come un valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2640">The result has the same type as the left operand and is always classified as a value.</span></span>

<span data-ttu-id="8624e-2641">Se l'operando sinistro è un accesso alla proprietà o indicizzatore, la proprietà o l'indicizzatore deve avere un `set` della funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="8624e-2641">If the left operand is a property or indexer access, the property or indexer must have a `set` accessor.</span></span> <span data-ttu-id="8624e-2642">Se ciò non avviene, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2642">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="8624e-2643">L'elaborazione in fase di esecuzione di un'assegnazione semplice nel formato `x = y` prevede i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-2643">The run-time processing of a simple assignment of the form `x = y` consists of the following steps:</span></span>

*  <span data-ttu-id="8624e-2644">Se `x` viene classificato come una variabile:</span><span class="sxs-lookup"><span data-stu-id="8624e-2644">If `x` is classified as a variable:</span></span>
   * <span data-ttu-id="8624e-2645">`x` viene valutata per produrre la variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-2645">`x` is evaluated to produce the variable.</span></span>
   * <span data-ttu-id="8624e-2646">`y` viene valutata e, se necessario, convertito nel tipo della `x` tramite una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2646">`y` is evaluated and, if required, converted to the type of `x` through an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)).</span></span>
   * <span data-ttu-id="8624e-2647">Se la variabile rappresentata da `x` è un elemento di un *reference_type*, viene eseguito un controllo in fase di esecuzione per garantire che il valore calcolato per `y` è compatibile con l'istanza di matrice di cui `x` è un elemento.</span><span class="sxs-lookup"><span data-stu-id="8624e-2647">If the variable given by `x` is an array element of a *reference_type*, a run-time check is performed to ensure that the value computed for `y` is compatible with the array instance of which `x` is an element.</span></span> <span data-ttu-id="8624e-2648">La verifica ha esito positivo se `y` viene `null`, o se una conversione implicita del riferimento ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)) esistente dal tipo effettivo dell'istanza a cui fanno riferimento `y` al tipo di elemento effettivo istanza della matrice contenente `x`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2648">The check succeeds if `y` is `null`, or if an implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) exists from the actual type of the instance referenced by `y` to the actual element type of the array instance containing `x`.</span></span> <span data-ttu-id="8624e-2649">In caso contrario viene generata un'eccezione `System.ArrayTypeMismatchException`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2649">Otherwise, a `System.ArrayTypeMismatchException` is thrown.</span></span>
   * <span data-ttu-id="8624e-2650">Il valore risultante dalla valutazione e la conversione dei `y` viene archiviato nella posizione specificata dalla valutazione di `x`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2650">The value resulting from the evaluation and conversion of `y` is stored into the location given by the evaluation of `x`.</span></span>
*  <span data-ttu-id="8624e-2651">Se `x` viene classificato come accesso alla proprietà o l'indicizzatore:</span><span class="sxs-lookup"><span data-stu-id="8624e-2651">If `x` is classified as a property or indexer access:</span></span>
   * <span data-ttu-id="8624e-2652">L'espressione dell'istanza (se `x` non è `static`) e l'elenco di argomenti (se `x` un accesso dell'indicizzatore) associato `x` vengono valutate, e i risultati vengono usati nella successiva `set` chiamata di funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="8624e-2652">The instance expression (if `x` is not `static`) and the argument list (if `x` is an indexer access) associated with `x` are evaluated, and the results are used in the subsequent `set` accessor invocation.</span></span>
   * <span data-ttu-id="8624e-2653">`y` viene valutata e, se necessario, convertito nel tipo della `x` tramite una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2653">`y` is evaluated and, if required, converted to the type of `x` through an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)).</span></span>
   * <span data-ttu-id="8624e-2654">Il `set` funzione di accesso di `x` viene richiamato con il valore calcolato per `y` come relativo `value` argomento.</span><span class="sxs-lookup"><span data-stu-id="8624e-2654">The `set` accessor of `x` is invoked with the value computed for `y` as its `value` argument.</span></span>

<span data-ttu-id="8624e-2655">Le regole di CO-varianza matrice ([vovariante](arrays.md#array-covariance)) consentono di un valore di tipo matrice `A[]` come un riferimento a un'istanza di un tipo di matrice `B[]`, a condizione che esiste una conversione implicita del riferimento da `B` per `A`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2655">The array co-variance rules ([Array covariance](arrays.md#array-covariance)) permit a value of an array type `A[]` to be a reference to an instance of an array type `B[]`, provided an implicit reference conversion exists from `B` to `A`.</span></span> <span data-ttu-id="8624e-2656">A causa di queste regole, l'assegnazione a un elemento di un *reference_type* richiede un controllo in fase di esecuzione per garantire che il valore assegnato è compatibile con l'istanza di matrice.</span><span class="sxs-lookup"><span data-stu-id="8624e-2656">Because of these rules, assignment to an array element of a *reference_type* requires a run-time check to ensure that the value being assigned is compatible with the array instance.</span></span> <span data-ttu-id="8624e-2657">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2657">In the example</span></span>
```csharp
string[] sa = new string[10];
object[] oa = sa;

oa[0] = null;               // Ok
oa[1] = "Hello";            // Ok
oa[2] = new ArrayList();    // ArrayTypeMismatchException
```
<span data-ttu-id="8624e-2658">fa sì che l'ultima assegnazione di un `System.ArrayTypeMismatchException` viene generata perché un'istanza di `ArrayList` non possono essere archiviati in un elemento di un `string[]`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2658">the last assignment causes a `System.ArrayTypeMismatchException` to be thrown because an instance of `ArrayList` cannot be stored in an element of a `string[]`.</span></span>

<span data-ttu-id="8624e-2659">Quando una proprietà o indicizzatore dichiarato un *struct_type* è la destinazione di un'assegnazione, l'espressione dell'istanza associata alla proprietà o l'accesso all'indicizzatore deve essere classificata come una variabile.</span><span class="sxs-lookup"><span data-stu-id="8624e-2659">When a property or indexer declared in a *struct_type* is the target of an assignment, the instance expression associated with the property or indexer access must be classified as a variable.</span></span> <span data-ttu-id="8624e-2660">Se l'espressione dell'istanza è classificato come un valore, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2660">If the instance expression is classified as a value, a binding-time error occurs.</span></span> <span data-ttu-id="8624e-2661">Because of [l'accesso ai membri](expressions.md#member-access), la stessa regola si applica anche ai campi.</span><span class="sxs-lookup"><span data-stu-id="8624e-2661">Because of [Member access](expressions.md#member-access), the same rule also applies to fields.</span></span>

<span data-ttu-id="8624e-2662">Date le dichiarazioni:</span><span class="sxs-lookup"><span data-stu-id="8624e-2662">Given the declarations:</span></span>
```csharp
struct Point
{
    int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int X {
        get { return x; }
        set { x = value; }
    }

    public int Y {
        get { return y; }
        set { y = value; }
    }
}

struct Rectangle
{
    Point a, b;

    public Rectangle(Point a, Point b) {
        this.a = a;
        this.b = b;
    }

    public Point A {
        get { return a; }
        set { a = value; }
    }

    public Point B {
        get { return b; }
        set { b = value; }
    }
}
```
<span data-ttu-id="8624e-2663">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2663">in the example</span></span>
```csharp
Point p = new Point();
p.X = 100;
p.Y = 100;
Rectangle r = new Rectangle();
r.A = new Point(10, 10);
r.B = p;
```
<span data-ttu-id="8624e-2664">le assegnazioni per `p.X`, `p.Y`, `r.A`, e `r.B` sono consentite perché `p` e `r` sono variabili.</span><span class="sxs-lookup"><span data-stu-id="8624e-2664">the assignments to `p.X`, `p.Y`, `r.A`, and `r.B` are permitted because `p` and `r` are variables.</span></span> <span data-ttu-id="8624e-2665">Tuttavia, nell'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2665">However, in the example</span></span>
```csharp
Rectangle r = new Rectangle();
r.A.X = 10;
r.A.Y = 10;
r.B.X = 100;
r.B.Y = 100;
```
<span data-ttu-id="8624e-2666">le assegnazioni sono tutti non valide, poiché `r.A` e `r.B` non sono variabili.</span><span class="sxs-lookup"><span data-stu-id="8624e-2666">the assignments are all invalid, since `r.A` and `r.B` are not variables.</span></span>

### <a name="compound-assignment"></a><span data-ttu-id="8624e-2667">Assegnazione composta</span><span class="sxs-lookup"><span data-stu-id="8624e-2667">Compound assignment</span></span>

<span data-ttu-id="8624e-2668">Se l'operando sinistro di un'assegnazione composta è nel formato `E.P` oppure `E[Ei]` in cui `E` dispone del tipo in fase di compilazione `dynamic`, quindi l'assegnazione è associata in modo dinamico ([binding dinamico](expressions.md#dynamic-binding)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2668">If the left operand of a compound assignment is of the form `E.P` or `E[Ei]` where `E` has the compile-time type `dynamic`, then the assignment is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="8624e-2669">In questo caso è il tipo di fase di compilazione dell'espressione di assegnazione `dynamic`, e la risoluzione descritta di seguito verrà eseguita in fase di esecuzione in base al tipo di runtime di `E`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2669">In this case the compile-time type of the assignment expression is `dynamic`, and the resolution described below will take place at run-time based on the run-time type of `E`.</span></span>

<span data-ttu-id="8624e-2670">Un'operazione del form `x op= y` avviene per la risoluzione dell'overload di operatore binario ([risoluzione dell'overload di operatore binario](expressions.md#binary-operator-overload-resolution)) come se l'operazione è stata scritta `x op y`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2670">An operation of the form `x op= y` is processed by applying binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) as if the operation was written `x op y`.</span></span> <span data-ttu-id="8624e-2671">Quindi,</span><span class="sxs-lookup"><span data-stu-id="8624e-2671">Then,</span></span>

*  <span data-ttu-id="8624e-2672">Se il tipo restituito dell'operatore selezionato è implicitamente convertibile nel tipo della `x`, l'operazione viene valutato come `x = x op y`, ad eccezione del fatto che `x` viene valutata una sola volta.</span><span class="sxs-lookup"><span data-stu-id="8624e-2672">If the return type of the selected operator is implicitly convertible to the type of `x`, the operation is evaluated as `x = x op y`, except that `x` is evaluated only once.</span></span>
*  <span data-ttu-id="8624e-2673">In caso contrario, se l'operatore selezionato è già definito, se il tipo restituito dell'operatore selezionato è convertibile in modo esplicito al tipo di `x`e se `y` è implicitamente convertibile nel tipo di `x` o l'operatore è un operatore, quindi l'operazione viene valutato come Shift `x = (T)(x op y)`, dove `T` è il tipo di `x`, ad eccezione del fatto che `x` viene valutata una sola volta.</span><span class="sxs-lookup"><span data-stu-id="8624e-2673">Otherwise, if the selected operator is a predefined operator, if the return type of the selected operator is explicitly convertible to the type of `x`, and if `y` is implicitly convertible to the type of `x` or the operator is a shift operator, then the operation is evaluated as `x = (T)(x op y)`, where `T` is the type of `x`, except that `x` is evaluated only once.</span></span>
*  <span data-ttu-id="8624e-2674">In caso contrario, l'assegnazione composta non è valido e si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2674">Otherwise, the compound assignment is invalid, and a binding-time error occurs.</span></span>

<span data-ttu-id="8624e-2675">Il termine "valutato una sola volta" indica che nella valutazione della `x op y`, i risultati di eventuali espressioni che costituiscono dei `x` sono temporaneamente salvati e riutilizzati quando si esegue l'assegnazione al `x`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2675">The term "evaluated only once" means that in the evaluation of `x op y`, the results of any constituent expressions of `x` are temporarily saved and then reused when performing the assignment to `x`.</span></span> <span data-ttu-id="8624e-2676">Ad esempio, nell'assegnazione `A()[B()] += C()`, dove `A` è un metodo di restituzione `int[]`, e `B` e `C` sono i metodi che restituiscono `int`, i metodi vengono richiamati solo una volta, nell'ordine `A`, `B`, `C`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2676">For example, in the assignment `A()[B()] += C()`, where `A` is a method returning `int[]`, and `B` and `C` are methods returning `int`, the methods are invoked only once, in the order `A`, `B`, `C`.</span></span>

<span data-ttu-id="8624e-2677">Quando l'operando sinistro di un'assegnazione composta è un accesso a proprietà o l'accesso dell'indicizzatore, la proprietà o l'indicizzatore deve avere entrambe un `get` funzione di accesso e un `set` della funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="8624e-2677">When the left operand of a compound assignment is a property access or indexer access, the property or indexer must have both a `get` accessor and a `set` accessor.</span></span> <span data-ttu-id="8624e-2678">Se ciò non avviene, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2678">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="8624e-2679">La seconda regola sopra permessi `x op= y` deve essere valutata come `x = (T)(x op y)` in determinati contesti.</span><span class="sxs-lookup"><span data-stu-id="8624e-2679">The second rule above permits `x op= y` to be evaluated as `x = (T)(x op y)` in certain contexts.</span></span> <span data-ttu-id="8624e-2680">La regola esiste in modo che gli operatori predefiniti utilizzabili come gli operatori composti quando l'operando sinistro è di tipo `sbyte`, `byte`, `short`, `ushort`, o `char`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2680">The rule exists such that the predefined operators can be used as compound operators when the left operand is of type `sbyte`, `byte`, `short`, `ushort`, or `char`.</span></span> <span data-ttu-id="8624e-2681">Anche se entrambi gli argomenti sono di uno di questi tipi, gli operatori predefiniti producono un risultato di tipo `int`, come descritto in [Promozioni numeriche binarie](expressions.md#binary-numeric-promotions).</span><span class="sxs-lookup"><span data-stu-id="8624e-2681">Even when both arguments are of one of those types, the predefined operators produce a result of type `int`, as described in [Binary numeric promotions](expressions.md#binary-numeric-promotions).</span></span> <span data-ttu-id="8624e-2682">In questo modo, senza un cast non sarebbe possibile assegnare il risultato all'operando di sinistra.</span><span class="sxs-lookup"><span data-stu-id="8624e-2682">Thus, without a cast it would not be possible to assign the result to the left operand.</span></span>

<span data-ttu-id="8624e-2683">L'effetto della regola per gli operatori predefiniti intuitivo consiste semplicemente `x op= y` è consentita solo se entrambi dei `x op y` e `x = y` sono consentite.</span><span class="sxs-lookup"><span data-stu-id="8624e-2683">The intuitive effect of the rule for predefined operators is simply that `x op= y` is permitted if both of `x op y` and `x = y` are permitted.</span></span> <span data-ttu-id="8624e-2684">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2684">In the example</span></span>
```csharp
byte b = 0;
char ch = '\0';
int i = 0;

b += 1;             // Ok
b += 1000;          // Error, b = 1000 not permitted
b += i;             // Error, b = i not permitted
b += (byte)i;       // Ok

ch += 1;            // Error, ch = 1 not permitted
ch += (char)1;      // Ok
```
<span data-ttu-id="8624e-2685">il motivo intuitivo per ogni errore è che una semplice assegnazione corrispondente sarebbe anche stato un errore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2685">the intuitive reason for each error is that a corresponding simple assignment would also have been an error.</span></span>

<span data-ttu-id="8624e-2686">Questo significa anche che supportano operazioni di assegnazione composta operazioni è elevato.</span><span class="sxs-lookup"><span data-stu-id="8624e-2686">This also means that compound assignment operations support lifted operations.</span></span> <span data-ttu-id="8624e-2687">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="8624e-2687">In the example</span></span>
```csharp
int? i = 0;
i += 1;             // Ok
```
<span data-ttu-id="8624e-2688">l'operatore lift `+(int?,int?)` viene usato.</span><span class="sxs-lookup"><span data-stu-id="8624e-2688">the lifted operator `+(int?,int?)` is used.</span></span>

### <a name="event-assignment"></a><span data-ttu-id="8624e-2689">Assegnazione di eventi</span><span class="sxs-lookup"><span data-stu-id="8624e-2689">Event assignment</span></span>

<span data-ttu-id="8624e-2690">Se l'operando sinistro di un `+=` o `-=` operatore viene classificato come accesso a un evento, quindi l'espressione viene valutata nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="8624e-2690">If the left operand of a `+=` or `-=` operator is classified as an event access, then the expression is evaluated as follows:</span></span>

*  <span data-ttu-id="8624e-2691">L'espressione dell'istanza, se presente, dell'accesso all'evento viene valutata.</span><span class="sxs-lookup"><span data-stu-id="8624e-2691">The instance expression, if any, of the event access is evaluated.</span></span>
*  <span data-ttu-id="8624e-2692">L'operando destro del `+=` oppure `-=` operatore viene valutato e, se necessario, convertito nel tipo dell'operando sinistro tramite una conversione implicita ([conversioni implicite](conversions.md#implicit-conversions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2692">The right operand of the `+=` or `-=` operator is evaluated, and, if required, converted to the type of the left operand through an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)).</span></span>
*  <span data-ttu-id="8624e-2693">Una funzione di accesso di evento dell'evento viene richiamato, tramite l'elenco di argomenti costituito l'operando di destra, dopo la valutazione e, se necessario, la conversione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2693">An event accessor of the event is invoked, with argument list consisting of the right operand, after evaluation and, if necessary, conversion.</span></span> <span data-ttu-id="8624e-2694">Se l'operatore è stata `+=`, il `add` viene richiamata la funzione di accesso; se l'operatore è stato `-=`, il `remove` viene richiamata una funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="8624e-2694">If the operator was `+=`, the `add` accessor is invoked; if the operator was `-=`, the `remove` accessor is invoked.</span></span>

<span data-ttu-id="8624e-2695">Un'espressione di assegnazione di eventi non restituisce un valore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2695">An event assignment expression does not yield a value.</span></span> <span data-ttu-id="8624e-2696">Di conseguenza, un'espressione di assegnazione di evento è valida solo nel contesto di un *statement_expression* ([le istruzioni di espressione](statements.md#expression-statements)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2696">Thus, an event assignment expression is valid only in the context of a *statement_expression* ([Expression statements](statements.md#expression-statements)).</span></span>

## <a name="expression"></a><span data-ttu-id="8624e-2697">Espressione</span><span class="sxs-lookup"><span data-stu-id="8624e-2697">Expression</span></span>

<span data-ttu-id="8624e-2698">Un' *espressione* può essere una *non_assignment_expression* o un *assegnazione*.</span><span class="sxs-lookup"><span data-stu-id="8624e-2698">An *expression* is either a *non_assignment_expression* or an *assignment*.</span></span>

```antlr
expression
    : non_assignment_expression
    | assignment
    ;

non_assignment_expression
    : conditional_expression
    | lambda_expression
    | query_expression
    ;
```

## <a name="constant-expressions"></a><span data-ttu-id="8624e-2699">Espressioni costanti</span><span class="sxs-lookup"><span data-stu-id="8624e-2699">Constant expressions</span></span>

<span data-ttu-id="8624e-2700">Oggetto *constant_expression* è un'espressione che può essere valutata interamente in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2700">A *constant_expression* is an expression that can be fully evaluated at compile-time.</span></span>

```antlr
constant_expression
    : expression
    ;
```

<span data-ttu-id="8624e-2701">Un'espressione costante deve essere il `null` valore letterale o con uno dei seguenti tipi: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char` , `float`, `double`, `decimal`, `bool`, `object`, `string`, o qualsiasi tipo di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2701">A constant expression must be the `null` literal or a value with one of  the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `object`, `string`, or any enumeration type.</span></span> <span data-ttu-id="8624e-2702">Solo i seguenti costrutti sono consentiti nelle espressioni costanti:</span><span class="sxs-lookup"><span data-stu-id="8624e-2702">Only the following constructs are permitted in constant expressions:</span></span>

*  <span data-ttu-id="8624e-2703">Valori letterali (incluso il `null` letterale).</span><span class="sxs-lookup"><span data-stu-id="8624e-2703">Literals (including the `null` literal).</span></span>
*  <span data-ttu-id="8624e-2704">I riferimenti a `const` i membri dei tipi di classi e struct.</span><span class="sxs-lookup"><span data-stu-id="8624e-2704">References to `const` members of class and struct types.</span></span>
*  <span data-ttu-id="8624e-2705">Riferimenti ai membri dei tipi di enumerazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2705">References to members of enumeration types.</span></span>
*  <span data-ttu-id="8624e-2706">I riferimenti a `const` parametri o variabili locali</span><span class="sxs-lookup"><span data-stu-id="8624e-2706">References to `const` parameters or local variables</span></span>
*  <span data-ttu-id="8624e-2707">Tra parentesi le sottoespressioni, che sono essi stessi espressioni costanti.</span><span class="sxs-lookup"><span data-stu-id="8624e-2707">Parenthesized sub-expressions, which are themselves constant expressions.</span></span>
*  <span data-ttu-id="8624e-2708">Espressioni cast, purché il tipo di destinazione è uno dei tipi elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="8624e-2708">Cast expressions, provided the target type is one of the types listed above.</span></span>
*  <span data-ttu-id="8624e-2709">`checked` e `unchecked` espressioni</span><span class="sxs-lookup"><span data-stu-id="8624e-2709">`checked` and `unchecked` expressions</span></span>
*  <span data-ttu-id="8624e-2710">Espressioni con valore predefinito</span><span class="sxs-lookup"><span data-stu-id="8624e-2710">Default value expressions</span></span>
*  <span data-ttu-id="8624e-2711">Espressioni Nameof</span><span class="sxs-lookup"><span data-stu-id="8624e-2711">Nameof expressions</span></span>
*  <span data-ttu-id="8624e-2712">Oggetto predefinito `+`, `-`, `!`, e `~` operatori unari.</span><span class="sxs-lookup"><span data-stu-id="8624e-2712">The predefined `+`, `-`, `!`, and `~` unary operators.</span></span>
*  <span data-ttu-id="8624e-2713">Oggetto predefinito `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `&`, `|`, `^`, `&&`, `||`, `==`, `!=`, `<`, `>`, `<=`, e `>=` gli operatori binari, fornito ogni operando è di tipo elencato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="8624e-2713">The predefined `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `&`, `|`, `^`, `&&`, `||`, `==`, `!=`, `<`, `>`, `<=`, and `>=` binary operators, provided each operand is of a type listed above.</span></span>
*  <span data-ttu-id="8624e-2714">Il `?:` operatore condizionale.</span><span class="sxs-lookup"><span data-stu-id="8624e-2714">The `?:` conditional operator.</span></span>

<span data-ttu-id="8624e-2715">Nelle espressioni costanti sono consentite le conversioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="8624e-2715">The following conversions are permitted in constant expressions:</span></span>

*  <span data-ttu-id="8624e-2716">Conversioni di identità</span><span class="sxs-lookup"><span data-stu-id="8624e-2716">Identity conversions</span></span>
*  <span data-ttu-id="8624e-2717">Conversioni numeriche</span><span class="sxs-lookup"><span data-stu-id="8624e-2717">Numeric conversions</span></span>
*  <span data-ttu-id="8624e-2718">Conversioni enumerazione</span><span class="sxs-lookup"><span data-stu-id="8624e-2718">Enumeration conversions</span></span>
*  <span data-ttu-id="8624e-2719">Conversioni di espressioni costanti</span><span class="sxs-lookup"><span data-stu-id="8624e-2719">Constant expression conversions</span></span>
*  <span data-ttu-id="8624e-2720">Conversioni dei riferimenti impliciti ed espliciti, a condizione che l'origine delle conversioni sia un'espressione costante che restituisce il valore null.</span><span class="sxs-lookup"><span data-stu-id="8624e-2720">Implicit and explicit reference conversions, provided that the source of the conversions is a constant expression that evaluates to the null value.</span></span>

<span data-ttu-id="8624e-2721">Altre conversioni tra cui la conversione boxing, le conversioni dei riferimenti implicita e conversione unboxing di valori non null non sono consentite nelle espressioni costanti.</span><span class="sxs-lookup"><span data-stu-id="8624e-2721">Other conversions including boxing, unboxing and implicit reference conversions of non-null values are not permitted in constant expressions.</span></span> <span data-ttu-id="8624e-2722">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8624e-2722">For example:</span></span>
```csharp
class C {
    const object i = 5;         // error: boxing conversion not permitted
    const object str = "hello"; // error: implicit reference conversion
}
```
<span data-ttu-id="8624e-2723">l'inizializzazione dei è un errore perché non è necessaria una conversione boxing.</span><span class="sxs-lookup"><span data-stu-id="8624e-2723">the initialization of i is an error because a boxing conversion is required.</span></span> <span data-ttu-id="8624e-2724">L'inizializzazione di str è un errore perché è necessaria una conversione implicita del riferimento da un valore diverso da null.</span><span class="sxs-lookup"><span data-stu-id="8624e-2724">The initialization of str is an error because an implicit reference conversion from a non-null value is required.</span></span>

<span data-ttu-id="8624e-2725">Ogni volta che un'espressione di cui soddisfa i requisiti elencati in precedenza, l'espressione viene valutata in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2725">Whenever an expression fulfills the requirements listed above, the expression is evaluated at compile-time.</span></span> <span data-ttu-id="8624e-2726">Questo vale anche se l'espressione è una sottoespressione di un'espressione più ampia che contiene costrutti non costante.</span><span class="sxs-lookup"><span data-stu-id="8624e-2726">This is true even if the expression is a sub-expression of a larger expression that contains non-constant constructs.</span></span>

<span data-ttu-id="8624e-2727">La valutazione in fase di compilazione di espressioni costanti Usa le stesse regole come versione di valutazione in fase di esecuzione delle espressioni non costanti, ad eccezione del fatto che in fase di esecuzione valutazione avrebbe generato un'eccezione, la valutazione in fase di compilazione genera un errore in fase di compilazione si verifichi.</span><span class="sxs-lookup"><span data-stu-id="8624e-2727">The compile-time evaluation of constant expressions uses the same rules as run-time evaluation of non-constant expressions, except that where run-time evaluation would have thrown an exception, compile-time evaluation causes a compile-time error to occur.</span></span>

<span data-ttu-id="8624e-2728">A meno che un'espressione costante viene inserita in modo esplicito un `unchecked` contesto, overflow che si verificano nelle conversioni e operazioni aritmetiche di tipo integrale durante la valutazione in fase di compilazione dell'espressione sempre causano errori in fase di compilazione ([Le espressioni costanti](expressions.md#constant-expressions)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2728">Unless a constant expression is explicitly placed in an `unchecked` context, overflows that occur in integral-type arithmetic operations and conversions during the compile-time evaluation of the expression always cause compile-time errors ([Constant expressions](expressions.md#constant-expressions)).</span></span>

<span data-ttu-id="8624e-2729">Espressioni costanti verificano nei contesti elencati di seguito.</span><span class="sxs-lookup"><span data-stu-id="8624e-2729">Constant expressions occur in the contexts listed below.</span></span> <span data-ttu-id="8624e-2730">In questi contesti, si verifica un errore in fase di compilazione se un'espressione non può essere valutata interamente in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2730">In these contexts, a compile-time error occurs if an expression cannot be fully evaluated at compile-time.</span></span>

*  <span data-ttu-id="8624e-2731">Le dichiarazioni di costante ([costanti](classes.md#constants)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2731">Constant declarations ([Constants](classes.md#constants)).</span></span>
*  <span data-ttu-id="8624e-2732">Dichiarazioni di membro di enumerazione ([membri Enum](enums.md#enum-members)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2732">Enumeration member declarations ([Enum members](enums.md#enum-members)).</span></span>
*  <span data-ttu-id="8624e-2733">Gli argomenti di elenchi di parametri formali predefiniti ([parametri di metodo](classes.md#method-parameters))</span><span class="sxs-lookup"><span data-stu-id="8624e-2733">Default arguments of formal parameter lists ([Method parameters](classes.md#method-parameters))</span></span>
*  <span data-ttu-id="8624e-2734">`case` le etichette di un `switch` istruzione ([dell'istruzione switch](statements.md#the-switch-statement)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2734">`case` labels of a `switch` statement ([The switch statement](statements.md#the-switch-statement)).</span></span>
*  <span data-ttu-id="8624e-2735">`goto case` istruzioni ([l'istruzione goto](statements.md#the-goto-statement)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2735">`goto case` statements ([The goto statement](statements.md#the-goto-statement)).</span></span>
*  <span data-ttu-id="8624e-2736">Lunghezze delle dimensioni in un'espressione di creazione di matrici ([espressioni di creazione matrice](expressions.md#array-creation-expressions)) che include un inizializzatore.</span><span class="sxs-lookup"><span data-stu-id="8624e-2736">Dimension lengths in an array creation expression ([Array creation expressions](expressions.md#array-creation-expressions)) that includes an initializer.</span></span>
*  <span data-ttu-id="8624e-2737">Attributi ([attributi](attributes.md)).</span><span class="sxs-lookup"><span data-stu-id="8624e-2737">Attributes ([Attributes](attributes.md)).</span></span>

<span data-ttu-id="8624e-2738">Una conversione implicita espressione costante ([conversioni implicite di espressioni costanti](conversions.md#implicit-constant-expression-conversions)) consente a un'espressione costante di tipo `int` da convertire in `sbyte`, `byte`, `short`, `ushort`, `uint`, o `ulong`, a condizione che il valore dell'espressione costante è compreso nell'intervallo del tipo di destinazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2738">An implicit constant expression conversion ([Implicit constant expression conversions](conversions.md#implicit-constant-expression-conversions)) permits a constant expression of type `int` to be converted to `sbyte`, `byte`, `short`, `ushort`, `uint`, or `ulong`, provided the value of the constant expression is within the range of the destination type.</span></span>

## <a name="boolean-expressions"></a><span data-ttu-id="8624e-2739">espressioni booleane</span><span class="sxs-lookup"><span data-stu-id="8624e-2739">Boolean expressions</span></span>

<span data-ttu-id="8624e-2740">Oggetto *boolean_expression* è un'espressione che produce un risultato di tipo `bool`; entrambi direttamente o tramite l'applicazione di `operator true` in determinati contesti come specificato nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="8624e-2740">A *boolean_expression* is an expression that yields a result of type `bool`; either directly or through application of `operator true` in certain contexts as specified in the following.</span></span>

```antlr
boolean_expression
    : expression
    ;
```

<span data-ttu-id="8624e-2741">L'espressione condizionale controllo di un' *if_statement* ([if istruzione](statements.md#the-if-statement)), *while_statement* ([l'istruzione while](statements.md#the-while-statement)), *do_statement* ([istruzione do](statements.md#the-do-statement)), o *for_statement* ([l'istruzione for](statements.md#the-for-statement)) è un *boolean_ espressione*.</span><span class="sxs-lookup"><span data-stu-id="8624e-2741">The controlling conditional expression of an *if_statement* ([The if statement](statements.md#the-if-statement)), *while_statement* ([The while statement](statements.md#the-while-statement)), *do_statement* ([The do statement](statements.md#the-do-statement)), or *for_statement* ([The for statement](statements.md#the-for-statement)) is a *boolean_expression*.</span></span> <span data-ttu-id="8624e-2742">Il controllo dell'espressione condizionale del `?:` operatore ([operatore condizionale](expressions.md#conditional-operator)) segue le stesse regole una *boolean_expression*, ma ai fini dell'operatore di precedenza viene classificata come un *conditional_or_expression*.</span><span class="sxs-lookup"><span data-stu-id="8624e-2742">The controlling conditional expression of the `?:` operator ([Conditional operator](expressions.md#conditional-operator)) follows the same rules as a *boolean_expression*, but for reasons of operator precedence is classified as a *conditional_or_expression*.</span></span>

<span data-ttu-id="8624e-2743">Oggetto *boolean_expression* `E` deve essere in grado di produrre un valore di tipo `bool`, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="8624e-2743">A *boolean_expression* `E` is required to be able to produce a value of type `bool`, as follows:</span></span>

*  <span data-ttu-id="8624e-2744">Se `E` è implicitamente convertibile in `bool` in fase di esecuzione viene applicata tale conversione implicita.</span><span class="sxs-lookup"><span data-stu-id="8624e-2744">If `E` is implicitly convertible to `bool` then at runtime that implicit conversion is applied.</span></span>
*  <span data-ttu-id="8624e-2745">In caso contrario, la risoluzione dell'overload di operatore unario ([risoluzione dell'overload di operatore unario](expressions.md#unary-operator-overload-resolution)) viene usato per trovare un'implementazione univoca migliore dell'operatore `true` su `E`, e che l'implementazione viene applicata in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2745">Otherwise, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is used to find a unique best implementation of operator `true` on `E`, and that implementation is applied at runtime.</span></span>
*  <span data-ttu-id="8624e-2746">Se non viene trovato alcun operatore di questo tipo, si verifica un errore in fase di associazione.</span><span class="sxs-lookup"><span data-stu-id="8624e-2746">If no such operator is found, a binding-time error occurs.</span></span>

<span data-ttu-id="8624e-2747">Il `DBBool` tipo struct in [Database di tipo booleano](structs.md#database-boolean-type) viene fornito un esempio di un tipo che implementa `operator true` e `operator false`.</span><span class="sxs-lookup"><span data-stu-id="8624e-2747">The `DBBool` struct type in [Database boolean type](structs.md#database-boolean-type) provides an example of a type that implements `operator true` and `operator false`.</span></span>
