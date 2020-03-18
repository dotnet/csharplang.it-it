---
ms.openlocfilehash: 49720d62c496ff904eacacb31ae29ef97a5aefaa
ms.sourcegitcommit: 8152182f0a477cb3082e625b607262cc459a17f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "79484944"
---
# <a name="records"></a><span data-ttu-id="40a6e-101">record</span><span class="sxs-lookup"><span data-stu-id="40a6e-101">records</span></span>

* <span data-ttu-id="40a6e-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="40a6e-102">[x] Proposed</span></span>
* <span data-ttu-id="40a6e-103">[] Prototipo: [completato](https://github.com/PROTOTYPE_OWNER/roslyn/BRANCH_NAME)</span><span class="sxs-lookup"><span data-stu-id="40a6e-103">[ ] Prototype: [Complete](https://github.com/PROTOTYPE_OWNER/roslyn/BRANCH_NAME)</span></span>
* <span data-ttu-id="40a6e-104">[] Implementazione: [in corso](https://github.com/dotnet/roslyn/BRANCH_NAME)</span><span class="sxs-lookup"><span data-stu-id="40a6e-104">[ ] Implementation: [In Progress](https://github.com/dotnet/roslyn/BRANCH_NAME)</span></span>
* <span data-ttu-id="40a6e-105">[] Specification: specifica bozza racchiusa</span><span class="sxs-lookup"><span data-stu-id="40a6e-105">[ ] Specification: Draft specification enclosed</span></span>

## <a name="summary"></a><span data-ttu-id="40a6e-106">Summary</span><span class="sxs-lookup"><span data-stu-id="40a6e-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="40a6e-107">I record sono un nuovo modulo di dichiarazione semplificato per C# i tipi di classe e struct che combinano i vantaggi di una serie di funzionalità più semplici.</span><span class="sxs-lookup"><span data-stu-id="40a6e-107">Records are a new, simplified declaration form for C# class and struct types that combine the benefits of a number of simpler features.</span></span> <span data-ttu-id="40a6e-108">Verranno descritte le nuove funzionalità (parametri del ricevitore del chiamante e *con-Expression*s), verranno fornite la sintassi e la semantica per le dichiarazioni di record, quindi verranno forniti alcuni esempi.</span><span class="sxs-lookup"><span data-stu-id="40a6e-108">We describe the new features (caller-receiver parameters and *with-expression*s), give the syntax and semantics for record declarations, and then provide some examples.</span></span>


## <a name="motivation"></a><span data-ttu-id="40a6e-109">Motivazione</span><span class="sxs-lookup"><span data-stu-id="40a6e-109">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="40a6e-110">Un numero significativo di dichiarazioni di tipo in C# non è altro che di aggregare raccolte di dati tipizzati.</span><span class="sxs-lookup"><span data-stu-id="40a6e-110">A significant number of type declarations in C# are little more than aggregate collections of typed data.</span></span> <span data-ttu-id="40a6e-111">Sfortunatamente, la dichiarazione di tali tipi richiede una grande quantità di codice standard.</span><span class="sxs-lookup"><span data-stu-id="40a6e-111">Unfortunately, declaring such types requires a great deal of boilerplate code.</span></span> <span data-ttu-id="40a6e-112">I record forniscono un meccanismo per dichiarare un tipo di *dati* descrivendo i membri dell'aggregazione insieme al codice o alle deviazioni aggiuntive rispetto al normale standard, se presente.</span><span class="sxs-lookup"><span data-stu-id="40a6e-112">*Records* provide a mechanism for declaring a datatype by describing the members of the aggregate along with additional code or deviations from the usual boilerplate, if any.</span></span>

<span data-ttu-id="40a6e-113">Vedere gli [esempi](#examples)riportati di seguito.</span><span class="sxs-lookup"><span data-stu-id="40a6e-113">See [Examples](#examples), below.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="40a6e-114">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="40a6e-114">Detailed design</span></span>
[design]: #detailed-design

### <a name="caller-receiver-parameters"></a><span data-ttu-id="40a6e-115">parametri del ricevitore del chiamante</span><span class="sxs-lookup"><span data-stu-id="40a6e-115">caller-receiver parameters</span></span>

<span data-ttu-id="40a6e-116">Attualmente *, l'argomento predefinito* di un parametro del metodo deve essere</span><span class="sxs-lookup"><span data-stu-id="40a6e-116">Currently a method parameter's *default-argument* must be</span></span> 
- <span data-ttu-id="40a6e-117">*espressione costante*; o</span><span class="sxs-lookup"><span data-stu-id="40a6e-117">a *constant-expression*; or</span></span>
- <span data-ttu-id="40a6e-118">espressione nel formato `new S()` in cui `S` è un tipo di valore. o</span><span class="sxs-lookup"><span data-stu-id="40a6e-118">an expression of the form `new S()` where `S` is a value type; or</span></span>
- <span data-ttu-id="40a6e-119">espressione nel formato `default(S)` dove `S` è un tipo di valore</span><span class="sxs-lookup"><span data-stu-id="40a6e-119">an expression of the form `default(S)` where `S` is a value type</span></span>

<span data-ttu-id="40a6e-120">Questa operazione viene estesa per aggiungere quanto segue</span><span class="sxs-lookup"><span data-stu-id="40a6e-120">We extend this to add the following</span></span>
- <span data-ttu-id="40a6e-121">espressione nel formato `this.Identifier`</span><span class="sxs-lookup"><span data-stu-id="40a6e-121">an expression of the form `this.Identifier`</span></span>

<span data-ttu-id="40a6e-122">Questo nuovo form è denominato *argomento predefinito del destinatario del chiamante*ed è consentito solo se sono soddisfatte tutte le condizioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="40a6e-122">This new form is called a *caller-receiver default-argument*, and is allowed only if all of the following are satisfied</span></span>
- <span data-ttu-id="40a6e-123">Il metodo in cui viene visualizzato è un metodo di istanza. e</span><span class="sxs-lookup"><span data-stu-id="40a6e-123">The method in which it appears is an instance method; and</span></span>
- <span data-ttu-id="40a6e-124">L'espressione `this.Identifier` associa a un membro di istanza del tipo che lo contiene, che deve essere un campo o una proprietà. e</span><span class="sxs-lookup"><span data-stu-id="40a6e-124">The expression `this.Identifier` binds to an instance member of the enclosing type, which must be either a field or a property; and</span></span>
- <span data-ttu-id="40a6e-125">Il membro a cui viene associato (e la funzione di accesso `get`, se è una proprietà), è almeno accessibile come metodo; e</span><span class="sxs-lookup"><span data-stu-id="40a6e-125">The member to which it binds (and the `get` accessor, if it is a property) is at least as accessible as the method; and</span></span>
- <span data-ttu-id="40a6e-126">Il tipo di `this.Identifier` è convertibile in modo implicito da un'identità o Conversione Nullable nel tipo del parametro (si tratta di un vincolo esistente nell' *argomento default*).</span><span class="sxs-lookup"><span data-stu-id="40a6e-126">The type of `this.Identifier` is implicitly convertible by an identity or nullable conversion to the type of the parameter (this is an existing constraint on *default-argument*).</span></span>

<span data-ttu-id="40a6e-127">Quando un argomento viene omesso da una chiamata di un membro di funzione per un parametro facoltativo corrispondente con un *argomento predefinito del destinatario del chiamante*, il valore del membro del ricevitore viene passato in modo implicito.</span><span class="sxs-lookup"><span data-stu-id="40a6e-127">When an argument is omitted from an invocation of a function member for a corresponding optional parameter with a *caller-receiver default-argument*, the value of the receiver's member is implicitly passed.</span></span> 

> <span data-ttu-id="40a6e-128">**Note sulla progettazione**: il motivo principale per il parametro del destinatario del chiamante è supportare l' *espressione with*.</span><span class="sxs-lookup"><span data-stu-id="40a6e-128">**Design Notes**: the main reason for the caller-receiver parameter is to support the *with-expression*.</span></span> <span data-ttu-id="40a6e-129">L'idea è che è possibile dichiarare un metodo come questo</span><span class="sxs-lookup"><span data-stu-id="40a6e-129">The idea is that you can declare a method like this</span></span>
> ```cs
> class Point
> {
>     public readonly int X;
>     public readonly int Y;
>     public Point With(int x = this.X, int y = this.Y) => new Point(x, y);
>     // etc
> }
> ```
> <span data-ttu-id="40a6e-130">e quindi usarlo come segue</span><span class="sxs-lookup"><span data-stu-id="40a6e-130">and then use it like this</span></span>
> ```cs
>     Point p = new Point(3, 4);
>     p = p.With(x: 1);
> ```
> <span data-ttu-id="40a6e-131">Per creare un nuovo `Point` come un `Point` esistente, ma con il valore di `X` modificato.</span><span class="sxs-lookup"><span data-stu-id="40a6e-131">To create a new `Point` just like an existing `Point` but with the value of `X` changed.</span></span>
> 
> <span data-ttu-id="40a6e-132">Si tratta di una domanda aperta indipendentemente dal fatto che la forma sintattica di *with-Expression* valga la pena aggiungere una volta che è disponibile il supporto per i parametri del ricevitore del chiamante, quindi è possibile eseguire questa operazione *anziché* anziché *in aggiunta all'* *espressione with*.</span><span class="sxs-lookup"><span data-stu-id="40a6e-132">It is an open question whether or not the syntactic form of the *with-expression* is worth adding once we have support for caller-receiver parameters, so it is possible we would do this *instead of* rather than *in addition to* the *with-expression*.</span></span>

- <span data-ttu-id="40a6e-133">[] **Problema aperto**: qual è l'ordine in cui viene valutato un *argomento predefinito del destinatario del chiamante* rispetto ad altri argomenti?</span><span class="sxs-lookup"><span data-stu-id="40a6e-133">[ ] **Open issue**: What is the order in which a *caller-receiver default-argument* is evaluated with respect to other arguments?</span></span> <span data-ttu-id="40a6e-134">È necessario indicare che non è specificato?</span><span class="sxs-lookup"><span data-stu-id="40a6e-134">Should we say that it is unspecified?</span></span>

### <a name="with-expressions"></a><span data-ttu-id="40a6e-135">espressioni with</span><span class="sxs-lookup"><span data-stu-id="40a6e-135">with-expressions</span></span>

<span data-ttu-id="40a6e-136">Viene proposto un nuovo form di espressione:</span><span class="sxs-lookup"><span data-stu-id="40a6e-136">A new expression form is proposed:</span></span>

```antlr
primary_expression
    : with_expression
    ;

with_expression
    : primary_expression 'with' '{' with_initializer_list '}'
    ;

with_initializer_list
    : with_initializer
    | with_initializer ',' with_initializer_list
    ;

with_initializer
    : identifier '=' expression
    ;
```

<span data-ttu-id="40a6e-137">Il token `with` è una nuova parola chiave sensibile al contesto.</span><span class="sxs-lookup"><span data-stu-id="40a6e-137">The token `with` is a new context-sensitive keyword.</span></span>

<span data-ttu-id="40a6e-138">Ogni *identificatore* a sinistra di un *with_initializer* deve essere associato a una proprietà o a un campo di istanza accessibile del tipo di *primary_expression* della *with_expression*.</span><span class="sxs-lookup"><span data-stu-id="40a6e-138">Each *identifier* on the left of a *with_initializer* must bind to an accessible instance field or property of the type of the *primary_expression* of the *with_expression*.</span></span> <span data-ttu-id="40a6e-139">Potrebbe non essere presente alcun nome duplicato tra questi identificatori di un determinato *with_expression*.</span><span class="sxs-lookup"><span data-stu-id="40a6e-139">There may be no duplicated name among these identifiers of a given *with_expression*.</span></span>

<span data-ttu-id="40a6e-140">*With_expression* nel formato</span><span class="sxs-lookup"><span data-stu-id="40a6e-140">A *with_expression* of the form</span></span>

> <span data-ttu-id="40a6e-141">*e1* `with` `{` *identificatore* = *e2*,... `}`</span><span class="sxs-lookup"><span data-stu-id="40a6e-141">*e1* `with` `{` *identifier* = *e2*, ... `}`</span></span>

<span data-ttu-id="40a6e-142">viene considerato come una chiamata del form</span><span class="sxs-lookup"><span data-stu-id="40a6e-142">is treated as an invocation of the form</span></span>

> <span data-ttu-id="40a6e-143">*e1*`.With(`*identifier2*`:` *e2*,...`)`</span><span class="sxs-lookup"><span data-stu-id="40a6e-143">*e1*`.With(`*identifier2*`:` *e2*, ...`)`</span></span>

<span data-ttu-id="40a6e-144">Dove, per ogni metodo denominato `With` che è un membro di istanza accessibile di *E1*, si seleziona *identifier2* come nome del primo parametro del metodo che ha un parametro del ricevitore del chiamante che è lo stesso membro del campo di istanza o della proprietà associata all' *identificatore*.</span><span class="sxs-lookup"><span data-stu-id="40a6e-144">Where, for each method named `With` that is an accessible instance member of *e1*, we select *identifier2* as the name of the first parameter in that method that has a caller-receiver parameter that is the same member as the instance field or property bound to *identifier*.</span></span> <span data-ttu-id="40a6e-145">Se tale parametro non può essere identificato, il metodo viene eliminato dalla considerazione.</span><span class="sxs-lookup"><span data-stu-id="40a6e-145">If no such parameter can be identified that method is eliminated from consideration.</span></span> <span data-ttu-id="40a6e-146">Il metodo da richiamare è selezionato tra i candidati rimanenti per risoluzione dell'overload.</span><span class="sxs-lookup"><span data-stu-id="40a6e-146">The method to be invoked is selected from among the remaining candidates by overload resolution.</span></span>

> <span data-ttu-id="40a6e-147">**Note sulla progettazione**: i parametri del ricevitore del chiamante, molti dei vantaggi dell' *espressione with* sono disponibili senza questo formato di sintassi speciale.</span><span class="sxs-lookup"><span data-stu-id="40a6e-147">**Design Notes**: Given caller-receiver parameters, many of the benefits of the *with-expression* are available without this special syntax form.</span></span> <span data-ttu-id="40a6e-148">Viene pertanto considerato se è necessario o meno.</span><span class="sxs-lookup"><span data-stu-id="40a6e-148">We are therefore considering whether or not it is needed.</span></span> <span data-ttu-id="40a6e-149">Il vantaggio principale è quello di consentire la programmazione in termini di nomi di campi e proprietà, anziché in termini di nomi di parametri.</span><span class="sxs-lookup"><span data-stu-id="40a6e-149">Its main benefit is allowing one to program in terms of the names of fields and properties, rather than in terms of the names of parameters.</span></span> <span data-ttu-id="40a6e-150">In questo modo è possibile migliorare sia la leggibilità che la qualità degli strumenti (ad esempio, per passare alla definizione dell'identificatore di un *with_expression* passare alla proprietà anziché a un parametro di metodo).</span><span class="sxs-lookup"><span data-stu-id="40a6e-150">In this way we improve both readability and the quality of tooling (e.g. go-to-definition on the identifier of a *with_expression* would navigate to the property rather than to a method parameter).</span></span>

- <span data-ttu-id="40a6e-151">[] **Problema aperto**: questa descrizione deve essere modificata per supportare i metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="40a6e-151">[ ] **Open issue**: This description should be modified to support extension methods.</span></span>

### <a name="pattern-matching"></a><span data-ttu-id="40a6e-152">criteri di ricerca</span><span class="sxs-lookup"><span data-stu-id="40a6e-152">pattern-matching</span></span>

<span data-ttu-id="40a6e-153">Vedere la [specifica](csharp-8.0/patterns.md#positional-pattern) di criteri di ricerca per una specifica di `Deconstruct` e la relativa relazione con la corrispondenza dei modelli.</span><span class="sxs-lookup"><span data-stu-id="40a6e-153">See the [Pattern Matching Specification](csharp-8.0/patterns.md#positional-pattern) for a specification of `Deconstruct` and its relationship to pattern-matching.</span></span>

> <span data-ttu-id="40a6e-154">**Note sulla progettazione**: in base al `Deconstruct` generato dal compilatore come specificato in questo documento e alla specifica per la corrispondenza dei modelli, una dichiarazione di record</span><span class="sxs-lookup"><span data-stu-id="40a6e-154">**Design Notes**: By virtue of the compiler-generated `Deconstruct` as specified herein, and the specification for pattern-matching, a record declaration</span></span>
> ```cs
> public class Point(int X, int Y);
> ```
> <span data-ttu-id="40a6e-155">supporterà la corrispondenza del criterio posizionale come indicato di seguito</span><span class="sxs-lookup"><span data-stu-id="40a6e-155">will support positional pattern-matching as follows</span></span>
> ```cs
> Point p = new Point(3, 4);
> if (p is Point(3, var y)) { // if X is 3
>     Console.WriteLine(y);   // print Y
> }
> ```

### <a name="record-type-declarations"></a><span data-ttu-id="40a6e-156">dichiarazioni di tipi di record</span><span class="sxs-lookup"><span data-stu-id="40a6e-156">record type declarations</span></span>

<span data-ttu-id="40a6e-157">La sintassi per una dichiarazione di `class` o `struct` è estesa per supportare parametri di valore. i parametri diventano proprietà del tipo:</span><span class="sxs-lookup"><span data-stu-id="40a6e-157">The syntax for a `class` or `struct` declaration is extended to support value parameters; the parameters become properties of the type:</span></span>

```antlr
class_declaration
    : attributes? class_modifiers? 'partial'? 'class' identifier type_parameter_list?
      record_parameters? record_class_base? type_parameter_constraints_clauses? class_body
    ;

struct_declaration
    : attributes? struct_modifiers? 'partial'? 'struct' identifier type_parameter_list?
      record_parameters? struct_interfaces? type_parameter_constraints_clauses? struct_body
    ;

record_class_base
    : class_type record_base_arguments?
    | interface_type_list
    | class_type record_base_arguments? ',' interface_type_list
    ;

record_base_arguments
    : '(' argument_list? ')'
    ;

record_parameters
    : '(' record_parameter_list? ')'
    ;

record_parameter_list
    : record_parameter
    | record_parameter ',' record_parameter_list
    ;

record_parameter
    : attributes? type identifier record_property_name? default_argument?
    ;

record_property_name
    : ':' identifier
    ;

class_body
    : '{' class_member_declarations? '}'
    | ';'
    ;

struct_body
    : '{' struct_members_declarations? '}'
    | ';'
    ;
```

> <span data-ttu-id="40a6e-158">**Note sulla progettazione**: poiché i tipi di record sono spesso utili senza la necessità di membri dichiarati in modo esplicito in un corpo di classe, la sintassi della dichiarazione viene modificata in modo da consentire a un corpo di essere semplicemente un punto e virgola.</span><span class="sxs-lookup"><span data-stu-id="40a6e-158">**Design Notes**: Because record types are often useful without the need for any members explicitly declared in a class-body, we modify the syntax of the declaration to allow a body to be simply a semicolon.</span></span>

<span data-ttu-id="40a6e-159">Una classe (struct) dichiarata con i *parametri di record* viene chiamata *classe di record* (struct di*record*), che è un *tipo di record*.</span><span class="sxs-lookup"><span data-stu-id="40a6e-159">A class (struct) declared with the *record-parameters* is called a *record class* (*record struct*), either of which is a *record type*.</span></span>

- <span data-ttu-id="40a6e-160">[] **Problema aperto**: è necessario includere *primary_constructor_body* nella grammatica, in modo che possa essere visualizzato all'interno di una dichiarazione del tipo di record.</span><span class="sxs-lookup"><span data-stu-id="40a6e-160">[ ] **Open issue**: We need to include *primary_constructor_body* in the grammar so that it can appear inside a record type declaration.</span></span>
- <span data-ttu-id="40a6e-161">[] **Problema aperto**: quali sono le regole dei conflitti di nome per i nomi dei parametri?</span><span class="sxs-lookup"><span data-stu-id="40a6e-161">[ ] **Open issue**: What are the name conflict rules for the parameter names?</span></span> <span data-ttu-id="40a6e-162">Probabilmente non è consentito entrare in conflitto con un parametro di tipo o un altro *parametro di record*.</span><span class="sxs-lookup"><span data-stu-id="40a6e-162">Presumably one is not allowed to conflict with a type parameter or another *record-parameter*.</span></span>
- <span data-ttu-id="40a6e-163">[] **Problema aperto**: è necessario specificare l'ambito dei parametri di record.</span><span class="sxs-lookup"><span data-stu-id="40a6e-163">[ ] **Open issue**: We need to specify the scope of the record-parameters.</span></span> <span data-ttu-id="40a6e-164">Dove possono essere usati?</span><span class="sxs-lookup"><span data-stu-id="40a6e-164">Where can they be used?</span></span> <span data-ttu-id="40a6e-165">Presumibilmente all'interno degli inizializzatori di campo di istanza e *primary_constructor_body* almeno.</span><span class="sxs-lookup"><span data-stu-id="40a6e-165">Presumably within instance field initializers and *primary_constructor_body* at least.</span></span>
- <span data-ttu-id="40a6e-166">[] **Problema aperto**: la dichiarazione di un tipo di record può essere parziale?</span><span class="sxs-lookup"><span data-stu-id="40a6e-166">[ ] **Open issue**: Can a record type declaration be partial?</span></span> <span data-ttu-id="40a6e-167">In caso affermativo, i parametri devono essere ripetuti per ogni parte?</span><span class="sxs-lookup"><span data-stu-id="40a6e-167">If so, must the parameters be repeated on each part?</span></span>

#### <a name="members-of-a-record-type"></a><span data-ttu-id="40a6e-168">Membri di un tipo di record</span><span class="sxs-lookup"><span data-stu-id="40a6e-168">Members of a record type</span></span>

<span data-ttu-id="40a6e-169">Oltre ai membri dichiarati nel *corpo della classe*, un tipo di record presenta i membri aggiuntivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="40a6e-169">In addition to the members declared in the *class-body*, a record type has the following additional members:</span></span>

##### <a name="primary-constructor"></a><span data-ttu-id="40a6e-170">Costruttore primario</span><span class="sxs-lookup"><span data-stu-id="40a6e-170">Primary Constructor</span></span>

<span data-ttu-id="40a6e-171">Un tipo di record ha un costruttore di `public` la cui firma corrisponde ai parametri del valore della dichiarazione del tipo.</span><span class="sxs-lookup"><span data-stu-id="40a6e-171">A record type has a `public` constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="40a6e-172">Questo metodo è denominato *costruttore primario* per il tipo e determina l'eliminazione del *costruttore predefinito* dichiarato in modo implicito.</span><span class="sxs-lookup"><span data-stu-id="40a6e-172">This is called the *primary constructor* for the type, and causes the implicitly declared *default constructor* to be suppressed.</span></span>

<span data-ttu-id="40a6e-173">In fase di esecuzione il costruttore primario</span><span class="sxs-lookup"><span data-stu-id="40a6e-173">At runtime the primary constructor</span></span>

* <span data-ttu-id="40a6e-174">Inizializza i campi di supporto generati dal compilatore per le proprietà corrispondenti ai parametri del valore (se queste proprietà sono fornite dal compilatore; [vedere 1.1.2](#1.1.2)); quindi</span><span class="sxs-lookup"><span data-stu-id="40a6e-174">initializes compiler-generated backing fields for the properties corresponding to the value parameters (if these properties are compiler-provided; [see 1.1.2](#1.1.2)); then</span></span>
* <span data-ttu-id="40a6e-175">esegue gli inizializzatori di campo di istanza visualizzati nel *corpo della classe*; E poi</span><span class="sxs-lookup"><span data-stu-id="40a6e-175">executes the instance field initializers appearing in the *class-body*; and then</span></span>
* <span data-ttu-id="40a6e-176">richiama un costruttore della classe base:</span><span class="sxs-lookup"><span data-stu-id="40a6e-176">invokes a base class constructor:</span></span>
    * <span data-ttu-id="40a6e-177">Se sono presenti argomenti nell' *record_base_arguments*, viene richiamato un costruttore di base selezionato dalla risoluzione dell'overload con questi argomenti;</span><span class="sxs-lookup"><span data-stu-id="40a6e-177">If there are arguments in the *record_base_arguments*, a base constructor selected by overload resolution with these arguments is invoked;</span></span>
    * <span data-ttu-id="40a6e-178">In caso contrario, viene richiamato un costruttore di base senza argomenti.</span><span class="sxs-lookup"><span data-stu-id="40a6e-178">Otherwise a base constructor is invoked with no arguments.</span></span>
* <span data-ttu-id="40a6e-179">esegue il corpo di ogni *primary_constructor_body*, se presente, in ordine di origine.</span><span class="sxs-lookup"><span data-stu-id="40a6e-179">executes the body of each *primary_constructor_body*, if any, in source order.</span></span>

- <span data-ttu-id="40a6e-180">[] **Problema aperto**: è necessario specificare tale ordine, in particolare tra le unità di compilazione per i parziali.</span><span class="sxs-lookup"><span data-stu-id="40a6e-180">[ ] **Open issue**: We need to specify that order, particularly across compilation units for partials.</span></span>
- <span data-ttu-id="40a6e-181">[] **Problema aperto**: è necessario specificare che tutti i costruttori dichiarati in modo esplicito devono essere concatenati al costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="40a6e-181">[ ] **Open Issue**: We need to specify that every explicitly declared constructor must chain to the primary constructor.</span></span>
- <span data-ttu-id="40a6e-182">[] **Problema aperto**: dovrebbe essere consentito modificare il modificatore di accesso nel costruttore primario?</span><span class="sxs-lookup"><span data-stu-id="40a6e-182">[ ] **Open issue**: Should it be allowed to change the access modifier on the primary constructor?</span></span>
- <span data-ttu-id="40a6e-183">[] **Problema aperto**: in uno struct di record è un errore perché non ci siano parametri di record?</span><span class="sxs-lookup"><span data-stu-id="40a6e-183">[ ] **Open issue**: In a record struct, it is an error for there to be no record parameters?</span></span>

##### <a name="primary-constructor-body"></a><span data-ttu-id="40a6e-184">Corpo del costruttore primario</span><span class="sxs-lookup"><span data-stu-id="40a6e-184">Primary constructor body</span></span>

```antlr
primary_constructor_body
    : attributes? constructor_modifiers? identifier block
    ;
```

<span data-ttu-id="40a6e-185">Un *primary_constructor_body* può essere utilizzato solo all'interno di una dichiarazione del tipo di record.</span><span class="sxs-lookup"><span data-stu-id="40a6e-185">A *primary_constructor_body* may only be used within a record type declaration.</span></span> <span data-ttu-id="40a6e-186">L' *identificatore* di un *primary_constructor_body* deve denominare il tipo di record in cui è dichiarato.</span><span class="sxs-lookup"><span data-stu-id="40a6e-186">The *identifier* of a *primary_constructor_body* shall name the record type in which it is declared.</span></span>

<span data-ttu-id="40a6e-187">Il *primary_constructor_body* non dichiara autonomamente un membro, ma è un modo per consentire al programmatore di fornire attributi per e specificare l'accesso del costruttore primario di un tipo di record.</span><span class="sxs-lookup"><span data-stu-id="40a6e-187">The *primary_constructor_body* does not declare a member on its own, but is a way for the programmer to provide attributes for, and specify the access of, a record type's primary constructor.</span></span> <span data-ttu-id="40a6e-188">Consente inoltre al programmatore di fornire codice aggiuntivo che verrà eseguito quando viene costruita un'istanza del tipo di record.</span><span class="sxs-lookup"><span data-stu-id="40a6e-188">It also enables the programmer to provide additional code that will be executed when an instance of the record type is constructed.</span></span>

- <span data-ttu-id="40a6e-189">[] **Problema aperto**: si noti che un costruttore predefinito struct ignora questa situazione.</span><span class="sxs-lookup"><span data-stu-id="40a6e-189">[ ] **Open issue**: We should note that a struct default constructor bypasses this.</span></span>
- <span data-ttu-id="40a6e-190">[] **Problema aperto**: è necessario specificare l'ordine di esecuzione dell'inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="40a6e-190">[ ] **Open issue**: We should specify the execution order of initialization.</span></span>
- <span data-ttu-id="40a6e-191">[] **Problema aperto**: se si vuole consentire un *primary_constructor_body* (presumibilmente senza attributi e modificatori) in una dichiarazione di tipo non di record e considerarlo come il codice di un inizializzatore di campo di istanza?</span><span class="sxs-lookup"><span data-stu-id="40a6e-191">[ ] **Open issue**: Should we allow something like a *primary_constructor_body* (presumably without attributes and modifiers) in a non-record type declaration, and treat it like we would the code of an instance field initializer?</span></span>

##### <a name="properties"></a><span data-ttu-id="40a6e-192">Proprietà</span><span class="sxs-lookup"><span data-stu-id="40a6e-192">Properties</span></span>

<span data-ttu-id="40a6e-193">Per ogni parametro di record di una dichiarazione del tipo di record è presente un membro della proprietà `public` corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value.</span><span class="sxs-lookup"><span data-stu-id="40a6e-193">For each record parameter of a record type declaration there is a corresponding `public` property member whose name and type are taken from the value parameter declaration.</span></span> <span data-ttu-id="40a6e-194">Il nome è l' *identificatore* del *record_property_name*, se presente, o l' *identificatore* del *record_parameter* in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="40a6e-194">Its name is the *identifier* of the *record_property_name*, if present, or the *identifier* of the *record_parameter* otherwise.</span></span> <span data-ttu-id="40a6e-195">Se nessuna proprietà pubblica concreta (ovvero non astratta) con una funzione di accesso `get` e con questo nome e tipo è stata dichiarata o ereditata in modo esplicito, viene prodotta dal compilatore come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="40a6e-195">If no concrete (i.e. non-abstract) public property with a `get` accessor and with this name and type is explicitly declared or inherited, it is produced by the compiler as follows:</span></span>

* <span data-ttu-id="40a6e-196">Per uno *struct di record* o una *classe di record*`sealed`:</span><span class="sxs-lookup"><span data-stu-id="40a6e-196">For a *record struct* or a `sealed` *record class*:</span></span>
 * <span data-ttu-id="40a6e-197">Un campo di `readonly` `private` viene prodotto come campo di supporto per una proprietà `readonly`.</span><span class="sxs-lookup"><span data-stu-id="40a6e-197">A `private` `readonly` field is produced as a backing field for a `readonly` property.</span></span> <span data-ttu-id="40a6e-198">Il valore viene inizializzato durante la costruzione con il valore del parametro del costruttore primario corrispondente.</span><span class="sxs-lookup"><span data-stu-id="40a6e-198">Its value is initialized during construction with the value of the corresponding primary constructor parameter.</span></span>
 * <span data-ttu-id="40a6e-199">La funzione di accesso `get` della proprietà viene implementata per restituire il valore del campo sottostante.</span><span class="sxs-lookup"><span data-stu-id="40a6e-199">The property's `get` accessor is implemented to return the value of the backing field.</span></span>
 * <span data-ttu-id="40a6e-200">Ogni funzione di accesso `get` della proprietà virtuale ereditata corrispondente viene sottoposta a override.</span><span class="sxs-lookup"><span data-stu-id="40a6e-200">Each "matching" inherited virtual property's `get` accessor is overridden.</span></span>

> <span data-ttu-id="40a6e-201">**Note sulla progettazione**: in altre parole, se si estende una classe di base o si implementa un'interfaccia che dichiara una proprietà astratta pubblica con lo stesso nome e tipo di un parametro di record, tale proprietà viene sottoposta a override o implementata.</span><span class="sxs-lookup"><span data-stu-id="40a6e-201">**Design notes**: In other words, if you extend a base class or implement an interface that declares a public abstract property with the same name and type as a record parameter, that property is overridden or implemented.</span></span>

- <span data-ttu-id="40a6e-202">[] **Problema aperto**: se è possibile modificare il modificatore di accesso in una proprietà quando viene dichiarata in modo esplicito?</span><span class="sxs-lookup"><span data-stu-id="40a6e-202">[ ] **Open issue**: Should it be possible to change the access modifier on a property when it is explicitly declared?</span></span>
- <span data-ttu-id="40a6e-203">[] **Problema aperto**: se è possibile sostituire un campo con una proprietà?</span><span class="sxs-lookup"><span data-stu-id="40a6e-203">[ ] **Open issue**: Should it be possible to substitute a field for a property?</span></span>

##### <a name="object-methods"></a><span data-ttu-id="40a6e-204">Metodi di oggetto</span><span class="sxs-lookup"><span data-stu-id="40a6e-204">Object Methods</span></span>

<span data-ttu-id="40a6e-205">Per uno *struct di record* o una *classe `sealed` record*, le implementazioni dei metodi `object.GetHashCode()` e `object.Equals(object)` vengono prodotte dal compilatore a meno che non vengano fornite dall'utente.</span><span class="sxs-lookup"><span data-stu-id="40a6e-205">For a *record struct* or a `sealed` *record class*, implementations of the methods `object.GetHashCode()` and `object.Equals(object)` are produced by the compiler unless provided by the user.</span></span>

- <span data-ttu-id="40a6e-206">[] **Problema aperto**: è necessario specificare con precisione la relativa implementazione.</span><span class="sxs-lookup"><span data-stu-id="40a6e-206">[ ] **Open issue**: We should precisely specify their implementation.</span></span>
- <span data-ttu-id="40a6e-207">[] **Problema aperto**: è necessario aggiungere anche l'interfaccia `IEquatable<T>` per il tipo di record e specificare che vengono fornite le implementazioni.</span><span class="sxs-lookup"><span data-stu-id="40a6e-207">[ ] **Open issue**: We should also add the interface `IEquatable<T>` for the record type and specify that implementations are provided.</span></span>
- <span data-ttu-id="40a6e-208">[] **Problema aperto**: è necessario specificare anche l'implementazione di ogni `IEquatable<T>.Equals`.</span><span class="sxs-lookup"><span data-stu-id="40a6e-208">[ ] **Open issue**: We should also specify that we implement every `IEquatable<T>.Equals`.</span></span>
- <span data-ttu-id="40a6e-209">[] **Problema aperto**: è necessario specificare esattamente il modo in cui viene risolto il problema di Equals in presenza di ereditarietà dei record: in particolare come vengono generati metodi di uguaglianza in modo che siano simmetrici, transitivi, riflessivi e così via.</span><span class="sxs-lookup"><span data-stu-id="40a6e-209">[ ] **Open issue**: We should specify precisely how we solve the problem of Equals in the face of record inheritance: specifically how we generate equality methods such that they are symmetric, transitive, reflexive, etc.</span></span>
- <span data-ttu-id="40a6e-210">[] **Problema aperto**: è stato proposto di implementare `operator ==` e `operator !=` per i tipi di record.</span><span class="sxs-lookup"><span data-stu-id="40a6e-210">[ ] **Open issue**: It has been proposed that we implement `operator ==` and `operator !=` for record types.</span></span>
- <span data-ttu-id="40a6e-211">[] **Problema aperto**: è necessario generare automaticamente un'implementazione di `object.ToString`?</span><span class="sxs-lookup"><span data-stu-id="40a6e-211">[ ] **Open issue**: Should we auto-generate an implementation of `object.ToString`?</span></span>

##### `Deconstruct`

<span data-ttu-id="40a6e-212">Un tipo di record dispone di un metodo di `public` generato dal compilatore `void Deconstruct` a meno che l'utente non ne venga fornita una con alcuna firma.</span><span class="sxs-lookup"><span data-stu-id="40a6e-212">A record type has a compiler-generated `public` method `void Deconstruct` unless one with any signature is provided by the user.</span></span> <span data-ttu-id="40a6e-213">Ogni parametro è un parametro di `out` con lo stesso nome e lo stesso tipo del parametro corrispondente del tipo di record.</span><span class="sxs-lookup"><span data-stu-id="40a6e-213">Each parameter is an `out` parameter of the same name and type as the corresponding parameter of the record type.</span></span> <span data-ttu-id="40a6e-214">L'implementazione fornita dal compilatore di questo metodo deve assegnare ogni parametro di `out` con il valore della proprietà corrispondente.</span><span class="sxs-lookup"><span data-stu-id="40a6e-214">The compiler-provided implementation of this method shall assign each `out` parameter with the value of the corresponding property.</span></span>

<span data-ttu-id="40a6e-215">Vedere [la specifica dei criteri](csharp-8.0/patterns.md#positional-pattern) di ricerca per la semantica di `Deconstruct`.</span><span class="sxs-lookup"><span data-stu-id="40a6e-215">See [the pattern-matching specification](csharp-8.0/patterns.md#positional-pattern) for the semantics of `Deconstruct`.</span></span>

##### <a name="with-method"></a><span data-ttu-id="40a6e-216">Metodo`With`</span><span class="sxs-lookup"><span data-stu-id="40a6e-216">`With` method</span></span>

<span data-ttu-id="40a6e-217">A meno che non esista un membro dichiarato dall'utente denominato `With` dichiarato, un tipo di record dispone di un metodo fornito dal compilatore denominato `With` il cui tipo restituito è il tipo di record stesso e contenente un parametro di valore corrispondente a ogni *parametro di record* nello stesso ordine in cui questi parametri vengono visualizzati nella dichiarazione del tipo di record.</span><span class="sxs-lookup"><span data-stu-id="40a6e-217">Unless there is a user-declared member named `With` declared, a record type has a compiler-provided method named `With` whose return type is the record type itself, and containing one value parameter corresponding to each *record-parameter* in the same order that these parameters appear in the record type declaration.</span></span> <span data-ttu-id="40a6e-218">Ogni parametro deve avere un *argomento predefinito del destinatario del chiamante* della proprietà corrispondente.</span><span class="sxs-lookup"><span data-stu-id="40a6e-218">Each parameter shall have a *caller-receiver default-argument* of the corresponding property.</span></span>

<span data-ttu-id="40a6e-219">In una classe `abstract` record, il metodo di `With` fornito dal compilatore è astratto.</span><span class="sxs-lookup"><span data-stu-id="40a6e-219">In an `abstract` record class, the compiler-provided `With` method is abstract.</span></span> <span data-ttu-id="40a6e-220">In uno struct di record o una classe di record sealed, viene `sealed`il metodo di `With` fornito dal compilatore.</span><span class="sxs-lookup"><span data-stu-id="40a6e-220">In a record struct, or a sealed record class, the compiler-provided `With` method is `sealed`.</span></span> <span data-ttu-id="40a6e-221">In caso contrario, il metodo `With` fornito dal compilatore è virtuale e la relativa implementazione deve restituire una nuova istanza prodotta richiamando il costruttore primario con i parametri come argomenti per creare una nuova istanza dai parametri e restituire la nuova istanza.</span><span class="sxs-lookup"><span data-stu-id="40a6e-221">Otherwise the compiler-provided `With` method is \`virtual and its implementation shall return a new instance produced by invoking the primary constructor with the parameters as arguments to create a new instance from the parameters, and return that new instance.</span></span>

- <span data-ttu-id="40a6e-222">[] **Problema aperto**: è necessario specificare anche in quali condizioni è necessario eseguire l'override o implementare metodi di `With` virtuali ereditati o metodi `With` da interfacce implementate.</span><span class="sxs-lookup"><span data-stu-id="40a6e-222">[ ] **Open issue**: We should also specify under what conditions we override or implement inherited virtual `With` methods or `With` methods from implemented interfaces.</span></span>
- <span data-ttu-id="40a6e-223">[] **Problema aperto**: è necessario indicare cosa accade quando si eredita un metodo di `With` non virtuale.</span><span class="sxs-lookup"><span data-stu-id="40a6e-223">[ ] **Open issue**: We should say what happens when we inherit a non-virtual `With` method.</span></span>

> <span data-ttu-id="40a6e-224">**Note sulla progettazione**: poiché i tipi di record sono immutabili per impostazione predefinita, il `With` metodo consente di creare una nuova istanza che corrisponde a un'istanza esistente ma con le proprietà selezionate in base ai nuovi valori.</span><span class="sxs-lookup"><span data-stu-id="40a6e-224">**Design notes**: Because record types are by default immutable, the `With` method provides a way of creating a new instance that is the same as an existing instance but with selected properties given new values.</span></span> <span data-ttu-id="40a6e-225">Ad esempio, dato</span><span class="sxs-lookup"><span data-stu-id="40a6e-225">For example, given</span></span>
> ```cs
> public class Point(int X, int Y);
> ```
> <span data-ttu-id="40a6e-226">è presente un membro fornito dal compilatore</span><span class="sxs-lookup"><span data-stu-id="40a6e-226">there is a compiler-provided member</span></span>
> ```cs
>     public virtual Point With(int X = this.X, int Y = this.Y) => new Point(X, Y);
> ```
> <span data-ttu-id="40a6e-227">Che Abilita una variabile del tipo di record</span><span class="sxs-lookup"><span data-stu-id="40a6e-227">Which enables an variable of the record type</span></span>
> ```cs
> var p = new Point(3, 4);
> ```
> <span data-ttu-id="40a6e-228">da sostituire con un'istanza con una o più proprietà diverse</span><span class="sxs-lookup"><span data-stu-id="40a6e-228">to be replaced with an instance that has one or more properties different</span></span>
> ```cs
>     p = p.With(X: 5);
> ```
> <span data-ttu-id="40a6e-229">Questo può essere espresso anche usando il *with_expression*:</span><span class="sxs-lookup"><span data-stu-id="40a6e-229">This can also be expressed using the *with_expression*:</span></span>
> ```cs
>     p = p with { X = 5 };
> ```

### <a name="examples"></a><span data-ttu-id="40a6e-230">Esempi</span><span class="sxs-lookup"><span data-stu-id="40a6e-230">Examples</span></span>

#### <a name="compatibility-of-record-types"></a><span data-ttu-id="40a6e-231">Compatibilità dei tipi di record</span><span class="sxs-lookup"><span data-stu-id="40a6e-231">Compatibility of record types</span></span>

<span data-ttu-id="40a6e-232">Poiché il programmatore può aggiungere membri a una dichiarazione di tipo record, spesso è possibile modificare il set di elementi record senza influire sui client esistenti.</span><span class="sxs-lookup"><span data-stu-id="40a6e-232">Because the programmer can add members to a record type declaration, it is often possible to change the set of record elements without affecting existing clients.</span></span> <span data-ttu-id="40a6e-233">Ad esempio, data una versione iniziale di un tipo di record</span><span class="sxs-lookup"><span data-stu-id="40a6e-233">For example, given an initial version of a record type</span></span>

```cs
// v1
public class Person(string Name, DateTime DateOfBirth);
```

<span data-ttu-id="40a6e-234">Un nuovo elemento del tipo di record può essere aggiunto compatibilmente con la revisione successiva del tipo senza influire sulla compatibilità binaria o dell'origine:</span><span class="sxs-lookup"><span data-stu-id="40a6e-234">A new element of the record type can be compatibly added in the next revision of the type without affecting binary or source compatibility:</span></span>

```cs
// v2
public class Person(string Name, DateTime DateOfBirth, string HomeTown)
{
    // Note: below operations added to retain binary compatibility with v1
    public Person(string Name, DateTime DateOfBirth) : this(Name, DateOfBirth, string.Empty) {}
    public static void operator is(Person self, out string Name, out DateTime DateOfBirth)
        { Name = self.Name; DateOfBirth = self.DateOfBirth; }
    public Person With(string Name, DateTime DateOfBirth) => new Person(Name, DateOfBirth);
}
```

#### <a name="record-struct-example"></a><span data-ttu-id="40a6e-235">esempio di struct di record</span><span class="sxs-lookup"><span data-stu-id="40a6e-235">record struct example</span></span>

<span data-ttu-id="40a6e-236">Questo struct di record</span><span class="sxs-lookup"><span data-stu-id="40a6e-236">This record struct</span></span>

```cs
public struct Pair(object First, object Second);
```

<span data-ttu-id="40a6e-237">viene convertito in questo codice</span><span class="sxs-lookup"><span data-stu-id="40a6e-237">is translated to this code</span></span>

```cs
public struct Pair : IEquatable<Pair>
{
    public object First { get; }
    public object Second { get; }
    public Pair(object First, object Second)
    {
        this.First = First;
        this.Second = Second;
    }
    public bool Equals(Pair other) // for IEquatable<Pair>
    {
        return Equals(First, other.First) && Equals(Second, other.Second);
    }
    public override bool Equals(object other)
    {
        return (other as Pair)?.Equals(this) == true;
    }
    public override int GetHashCode()
    {
        return (First?.GetHashCode()*17 + Second?.GetHashCode()).GetValueOrDefault();
    }
    public Pair With(object First = this.First, object Second = this.Second) => new Pair(First, Second);
    public void Deconstruct(out object First, out object Second)
    {
        First = self.First;
        Second = self.Second;
    }
}
```

- <span data-ttu-id="40a6e-238">[] **Problema aperto**: se l'implementazione di Equals (pair other) è un membro pubblico di pair?</span><span class="sxs-lookup"><span data-stu-id="40a6e-238">[ ] **Open issue**: should the implementation of Equals(Pair other) be a public member of Pair?</span></span>
- <span data-ttu-id="40a6e-239">[] **Problema aperto**: questa implementazione di `Equals` non è simmetrica in presenza di ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="40a6e-239">[ ] **Open issue**: This implementation of `Equals` is not symmetric in the face of inheritance.</span></span>
- <span data-ttu-id="40a6e-240">[] **Problema aperto**: se un record dichiara `operator ==` e `operator !=`?</span><span class="sxs-lookup"><span data-stu-id="40a6e-240">[ ] **Open issue**: Should a record declare `operator ==` and `operator !=`?</span></span>

> <span data-ttu-id="40a6e-241">**Note sulla progettazione**: poiché un tipo di record può ereditare da un altro tipo di record e questa implementazione di `Equals` non è simmetrica in tal caso, non è corretta.</span><span class="sxs-lookup"><span data-stu-id="40a6e-241">**Design notes**: Because one record type can inherit from another, and this implementation of `Equals` would not be symmetric in that case, it is not correct.</span></span> <span data-ttu-id="40a6e-242">Si propone di implementare l'uguaglianza in questo modo:</span><span class="sxs-lookup"><span data-stu-id="40a6e-242">We propose to implement equality this way:</span></span>
> ```cs
>     public bool Equals(Pair other) // for IEquatable<Pair>
>     {
>         return other != null && EqualityContract == other.EqualityContract &&
>             Equals(First, other.First) && Equals(Second, other.Second);
>     }
>     protected virtual Type EqualityContract => typeof(Pair);
> ```
> <span data-ttu-id="40a6e-243">I record derivati `override EqualityContract`.</span><span class="sxs-lookup"><span data-stu-id="40a6e-243">Derived records would `override EqualityContract`.</span></span> <span data-ttu-id="40a6e-244">L'alternativa meno interessante consiste nel limitare l'ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="40a6e-244">The less attractive alternative is to restrict inheritance.</span></span>

#### <a name="sealed-record-example"></a><span data-ttu-id="40a6e-245">esempio di record sealed</span><span class="sxs-lookup"><span data-stu-id="40a6e-245">sealed record example</span></span>

<span data-ttu-id="40a6e-246">Classe di record sealed</span><span class="sxs-lookup"><span data-stu-id="40a6e-246">This sealed record class</span></span>

```cs
public sealed class Student(string Name, decimal Gpa);
```

<span data-ttu-id="40a6e-247">viene convertito in questo codice</span><span class="sxs-lookup"><span data-stu-id="40a6e-247">is translated into this code</span></span>

```cs
public sealed class Student : IEquatable<Student>
{
    public string Name { get; }
    public decimal Gpa { get; }
    public Student(string Name, decimal Gpa)
    {
        this.Name = Name;
        this.Gpa = Gpa;
    }
    public bool Equals(Student other) // for IEquatable<Student>
    {
        return other != null && Equals(Name, other.Name) && Equals(Gpa, other.Gpa);
    }
    public override bool Equals(object other)
    {
        return this.Equals(other as Student);
    }
    public override int GetHashCode()
    {
        return (Name?.GetHashCode()*17 + Gpa?.GetHashCode()).GetValueOrDefault();
    }
    public Student With(string Name = this.Name, decimal Gpa = this.Gpa) => new Student(Name, Gpa);
    public void Deconstruct(out string Name, out decimal Gpa)
    {
        Name = self.Name;
        Gpa = self.Gpa;
    }
}
```

#### <a name="abstract-record-class-example"></a><span data-ttu-id="40a6e-248">esempio di classe di record abstract</span><span class="sxs-lookup"><span data-stu-id="40a6e-248">abstract record class example</span></span>

<span data-ttu-id="40a6e-249">Questa classe di record abstract</span><span class="sxs-lookup"><span data-stu-id="40a6e-249">This abstract record class</span></span>

```cs
public abstract class Person(string Name);
```

<span data-ttu-id="40a6e-250">viene convertito in questo codice</span><span class="sxs-lookup"><span data-stu-id="40a6e-250">is translated into this code</span></span>

```cs
public abstract class Person : IEquatable<Person>
{
    public string Name { get; }
    public Person(string Name)
    {
        this.Name = Name;
    }
    public bool Equals(Person other)
    {
        return other != null && Equals(Name, other.Name);
    }
    public override bool Equals(object other)
    {
        return Equals(other as Person);
    }
    public override int GetHashCode()
    {
        return (Name?.GetHashCode()).GetValueOrDefault();
    }
    public abstract Person With(string Name = this.Name);
    public void Deconstruct(Person self, out string Name)
    {
        Name = self.Name;
    }
}
```

#### <a name="combining-abstract-and-sealed-records"></a><span data-ttu-id="40a6e-251">combinazione di record astratti e sealed</span><span class="sxs-lookup"><span data-stu-id="40a6e-251">combining abstract and sealed records</span></span>

<span data-ttu-id="40a6e-252">Data la classe di record abstract `Person` sopra, questa classe di record sealed</span><span class="sxs-lookup"><span data-stu-id="40a6e-252">Given the abstract record class `Person` above, this sealed record class</span></span>

```cs
public sealed class Student(string Name, decimal Gpa) : Person(Name);
```

<span data-ttu-id="40a6e-253">viene convertito in questo codice</span><span class="sxs-lookup"><span data-stu-id="40a6e-253">is translated into this code</span></span>

```cs
public sealed class Student : Person, IEquatable<Student>
{
    public override string Name { get; }
    public decimal Gpa { get; }
    public Student(string Name, decimal Gpa) : base(Name)
    {
        this.Name = Name;
        this.Gpa = Gpa;
    }
    public override bool Equals(Student other) // for IEquatable<Student>
    {
        return Equals(Name, other.Name) && Equals(Gpa, other.Gpa);
    }
    public bool Equals(Person other) // for IEquatable<Person>
    {
        return (other as Student)?.Equals(this) == true;
    }
    public override bool Equals(object other)
    {
        return (other as Student)?.Equals(this) == true;
    }
    public override int GetHashCode()
    {
        return (Name?.GetHashCode()*17 + Gpa.GetHashCode()).GetValueOrDefault();
    }
    public Student With(string Name = this.Name, decimal Gpa = this.Gpa) => new Student(Name, Gpa);
    public override Person With(string Name = this.Name) => new Student(Name, Gpa);
    public void Deconstruct(Student self, out string Name, out decimal Gpa)
    {
        Name = self.Name;
        Gpa = self.Gpa;
    }
}
```

## <a name="drawbacks"></a><span data-ttu-id="40a6e-254">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="40a6e-254">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="40a6e-255">Come per qualsiasi funzionalità del linguaggio, è necessario stabilire se la complessità aggiuntiva al linguaggio venga ripagata in base a una maggiore chiarezza offerta C# al corpo dei programmi che trarrebbero vantaggio dalla funzionalità.</span><span class="sxs-lookup"><span data-stu-id="40a6e-255">As with any language feature, we must question whether the additional complexity to the language is repaid in the additional clarity offered to the body of C# programs that would benefit from the feature.</span></span>

## <a name="alternatives"></a><span data-ttu-id="40a6e-256">Alternativi</span><span class="sxs-lookup"><span data-stu-id="40a6e-256">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="40a6e-257">Abbiamo preso in considerazione l'aggiunta di C# *costruttori primari* in 6.</span><span class="sxs-lookup"><span data-stu-id="40a6e-257">We considered adding *primary constructors* in C# 6.</span></span> <span data-ttu-id="40a6e-258">Sebbene occupino la stessa superficie sintattica di questa proposta, abbiamo scoperto che i vantaggi offerti dai record sono stati ridotti.</span><span class="sxs-lookup"><span data-stu-id="40a6e-258">Although they occupy the same syntactic surface as this proposal, we found that they fell short of the advantages offered by records.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="40a6e-259">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="40a6e-259">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="40a6e-260">Le domande aperte vengono visualizzate nel corpo della proposta.</span><span class="sxs-lookup"><span data-stu-id="40a6e-260">Open questions appear in the body of the proposal.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="40a6e-261">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="40a6e-261">Design meetings</span></span>

<span data-ttu-id="40a6e-262">TBD</span><span class="sxs-lookup"><span data-stu-id="40a6e-262">TBD</span></span>
