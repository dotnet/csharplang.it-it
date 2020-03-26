---
ms.openlocfilehash: 258ae6865c5b2c3103a0cdf7e1e5a2cdee11e740
ms.sourcegitcommit: 1e1c7c72b156e2fbc54d6d6ac8d21bca9934d8d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80281957"
---
# <a name="records-work-in-progress"></a><span data-ttu-id="a3a9c-101">Record di lavoro in corso</span><span class="sxs-lookup"><span data-stu-id="a3a9c-101">Records Work-in-Progress</span></span>

<span data-ttu-id="a3a9c-102">Diversamente dalle altre proposte di record, non si tratta di una proposta di per sé, ma è stata progettata per registrare le decisioni di progettazione di consenso per la funzionalità dei record.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-102">Unlike the other records proposals, this is not a proposal in itself, but a work-in-progress designed to record consensus design decisions for the records feature.</span></span> <span data-ttu-id="a3a9c-103">I dettagli della specifica verranno aggiunti in modo necessario per risolvere le domande.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-103">Specification detail will be added as necessary to resolve questions.</span></span>

<span data-ttu-id="a3a9c-104">La sintassi per un record è proposta per essere aggiunta come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="a3a9c-104">The syntax for a record is proposed to be added as follows:</span></span>

```antlr
class_declaration
    : attributes? class_modifiers? 'partial'? 'class' identifier type_parameter_list?
      parameter_list? type_parameter_constraints_clauses? class_body
    ;

struct_declaration
    : attributes? struct_modifiers? 'partial'? 'struct' identifier type_parameter_list?
      parameter_list? struct_interfaces? type_parameter_constraints_clauses? struct_body
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

<span data-ttu-id="a3a9c-105">Il `attributes` non terminale consentirà anche un nuovo attributo contestuale, `data`.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-105">The `attributes` non-terminal will also permit a new contextual attribute, `data`.</span></span>

<span data-ttu-id="a3a9c-106">Una classe (struct) dichiarata con un elenco di parametri o un modificatore `data` viene chiamata classe di record (struct di record), che è un tipo di record.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-106">A class (struct) declared with a parameter list or `data` modifier is called a record class (record struct), either of which is a record type.</span></span>

<span data-ttu-id="a3a9c-107">È un errore dichiarare un tipo di record senza un elenco di parametri e il modificatore `data`.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-107">It is an error to declare a record type without both a parameter list and the `data` modifier.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="a3a9c-108">Membri di un tipo di record</span><span class="sxs-lookup"><span data-stu-id="a3a9c-108">Members of a record type</span></span>

<span data-ttu-id="a3a9c-109">Oltre ai membri dichiarati nel corpo della classe, un tipo di record presenta i membri aggiuntivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="a3a9c-109">In addition to the members declared in the class-body, a record type has the following additional members:</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="a3a9c-110">Costruttore primario</span><span class="sxs-lookup"><span data-stu-id="a3a9c-110">Primary Constructor</span></span>

<span data-ttu-id="a3a9c-111">Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-111">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="a3a9c-112">Questo metodo è denominato costruttore primario per il tipo e determina l'eliminazione del costruttore predefinito dichiarato in modo implicito.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-112">This is called the primary constructor for the type, and causes the implicitly declared default constructor to be suppressed.</span></span> <span data-ttu-id="a3a9c-113">È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-113">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>
<span data-ttu-id="a3a9c-114">In fase di esecuzione il costruttore primario</span><span class="sxs-lookup"><span data-stu-id="a3a9c-114">At runtime the primary constructor</span></span> 

1. <span data-ttu-id="a3a9c-115">esegue gli inizializzatori di campo di istanza visualizzati nel corpo della classe; quindi richiama il costruttore della classe base senza argomenti.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-115">executes the instance field initializers appearing in the class-body; and then  invokes the base class constructor with no arguments.</span></span>

1. <span data-ttu-id="a3a9c-116">Inizializza i campi di supporto generati dal compilatore per le proprietà corrispondenti ai parametri del valore (se queste proprietà sono fornite dal compilatore; vedere [Proprietà sintetizzate](#Synthesized Properties))</span><span class="sxs-lookup"><span data-stu-id="a3a9c-116">initializes compiler-generated backing fields for the properties corresponding to the value parameters (if these properties are compiler-provided; see [Synthesized properties](#Synthesized Properties))</span></span>


<span data-ttu-id="a3a9c-117">[] TODO: aggiungere la sintassi e la specifica della chiamata di base sulla scelta del costruttore di base tramite la risoluzione dell'overload</span><span class="sxs-lookup"><span data-stu-id="a3a9c-117">[ ] TODO: add base call syntax and specification about choosing base constructor through overload resolution</span></span>

### <a name="properties"></a><span data-ttu-id="a3a9c-118">Proprietà</span><span class="sxs-lookup"><span data-stu-id="a3a9c-118">Properties</span></span>

<span data-ttu-id="a3a9c-119">Per ogni parametro di record di una dichiarazione del tipo di record è presente un membro della proprietà pubblica corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-119">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span> <span data-ttu-id="a3a9c-120">Se nessuna proprietà concreta (ovvero non astratta) con una funzione di accesso get e con il nome e il tipo specificati viene dichiarata o ereditata in modo esplicito, viene prodotta dal compilatore come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="a3a9c-120">If no concrete (i.e. non-abstract) property with a get accessor and with this name and type is explicitly declared or inherited, it is produced by the compiler as follows:</span></span>

<span data-ttu-id="a3a9c-121">Per uno struct di record o una classe di record:</span><span class="sxs-lookup"><span data-stu-id="a3a9c-121">For a record struct or a record class:</span></span>

* <span data-ttu-id="a3a9c-122">Viene creata una proprietà automatica Get-only pubblica.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-122">A public get-only auto-property is created.</span></span> <span data-ttu-id="a3a9c-123">Il valore viene inizializzato durante la costruzione con il valore del parametro del costruttore primario corrispondente.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-123">Its value is initialized during construction with the value of the corresponding primary constructor parameter.</span></span> <span data-ttu-id="a3a9c-124">Ogni funzione di accesso get della proprietà astratta "corrispondente" ereditata viene sottoposta a override.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-124">Each "matching" inherited abstract property's get accessor is overridden.</span></span>

### <a name="equality-members"></a><span data-ttu-id="a3a9c-125">Membri di uguaglianza</span><span class="sxs-lookup"><span data-stu-id="a3a9c-125">Equality members</span></span>

<span data-ttu-id="a3a9c-126">I tipi di record producono implementazioni sintetizzate per i metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="a3a9c-126">Record types produce synthesized implementations for the following methods:</span></span>

* <span data-ttu-id="a3a9c-127">`object.GetHashCode()` sostituzione, a meno che non sia sealed o fornito dall'utente</span><span class="sxs-lookup"><span data-stu-id="a3a9c-127">`object.GetHashCode()` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="a3a9c-128">`object.Equals(object)` sostituzione, a meno che non sia sealed o fornito dall'utente</span><span class="sxs-lookup"><span data-stu-id="a3a9c-128">`object.Equals(object)` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="a3a9c-129">`T Equals(T)` metodo, dove `T` è il tipo corrente</span><span class="sxs-lookup"><span data-stu-id="a3a9c-129">`T Equals(T)` method, where `T` is the current type</span></span>

<span data-ttu-id="a3a9c-130">`T Equals(T)` viene specificato per eseguire l'uguaglianza dei valori, confrontando la proprietà con lo stesso nome di ogni parametro del costruttore primario per la proprietà corrispondente dell'altro tipo.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-130">`T Equals(T)` is specified to perform value equality, comparing the property with same name as each primary constructor parameter to the corresponding property of the other type.</span></span>
<span data-ttu-id="a3a9c-131">`object.Equals` esegue l'equivalente di</span><span class="sxs-lookup"><span data-stu-id="a3a9c-131">`object.Equals` performs the equivalent of</span></span>

```C#
override Equals(object o) => Equals(o as T);
```

## <a name="with-expression"></a><span data-ttu-id="a3a9c-132">Espressione `with`</span><span class="sxs-lookup"><span data-stu-id="a3a9c-132">`with` expression</span></span>

<span data-ttu-id="a3a9c-133">Un'espressione `with` è una nuova espressione che usa la sintassi seguente.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-133">A `with` expression is a new expression using the following syntax.</span></span>

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' anonymous_object_initializer
```

<span data-ttu-id="a3a9c-134">Un'espressione `with` consente "mutazione non distruttiva", progettata per produrre una copia dell'espressione Receiver con le modifiche alle proprietà elencate nel `anonymous_object_initializer`.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-134">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications to properties listed in the `anonymous_object_initializer`.</span></span>

<span data-ttu-id="a3a9c-135">Un'espressione `with` valida ha un ricevitore con un tipo non void.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-135">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="a3a9c-136">Il tipo di ricevitore deve contenere un metodo di istanza accessibile denominato `With` con i parametri e il tipo restituiti appropriati.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-136">The receiver type must contain an accessible instance method called `With` with the appropriate parameters and return type.</span></span> <span data-ttu-id="a3a9c-137">Se sono presenti più metodi di `With` senza override, è un errore.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-137">It is an error if there are multiple non-override `With` methods.</span></span> <span data-ttu-id="a3a9c-138">Se sono presenti più sostituzioni di `With`, deve essere presente un metodo di `With` non di override, ovvero il metodo di destinazione.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-138">If there are multiple `With` overrides, there must be a non-override `With` method, which is the target method.</span></span> <span data-ttu-id="a3a9c-139">In caso contrario, deve essere presente esattamente un `With` metodo.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-139">Otherwise, there must be exactly one `With` method.</span></span>

<span data-ttu-id="a3a9c-140">Sul lato destro dell'espressione `with` è presente una `anonymous_object_initializer` con una sequenza di assegnazioni con un campo o una proprietà del ricevitore sul lato sinistro dell'assegnazione e un'espressione arbitraria sul lato destro, convertibile in modo implicito nel tipo della parte sinistra del lato sinistro.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-140">On the right hand side of the `with` expression is an `anonymous_object_initializer` with a sequence of assignments with a field or property of the receiver on the left-hand side of the assignment, and an arbitrary expression on the right-hand side which is implicitly convertible to the type of the left-hand side.</span></span>

<span data-ttu-id="a3a9c-141">Dato un metodo di `With` di destinazione, il tipo restituito deve essere il tipo del tipo di espressione del ricevitore o un tipo di base.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-141">Given a target `With` method, the return type must be the type of the receiver expression type, or a base type thereof.</span></span> <span data-ttu-id="a3a9c-142">Per ogni parametro del metodo `With`, deve essere presente un campo di istanza corrispondente accessibile o una proprietà leggibile per il tipo di ricevitore con lo stesso nome e lo stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-142">For each parameter of the `With` method, there must be an accessible corresponding instance field or readable property on the receiver type with the same name and the same type.</span></span> <span data-ttu-id="a3a9c-143">Ogni proprietà o campo nel lato destro dell'espressione with deve corrispondere anche a un parametro con lo stesso nome nel metodo `With`.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-143">Each property or field in the right-hand side of the With expression must also correspond to a parameter of the same name in the `With` method.</span></span>

<span data-ttu-id="a3a9c-144">Dato un metodo di `With` valido, la valutazione di un'espressione `with` equivale a chiamare il metodo `With` con le espressioni nel `anonymous_object_initializer` sostituito per il parametro con lo stesso nome della proprietà sulla parte sinistra.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-144">Given a valid `With` method, the evaluation of a `with` expression is equivalent to calling the `With` method with the expressions in the `anonymous_object_initializer` substituted for the parameter of the same name as the property on the left hand side.</span></span> <span data-ttu-id="a3a9c-145">Se non esiste una proprietà corrispondente per un determinato parametro nell'`anonymous_object_initializer`, l'argomento è la valutazione del campo o della proprietà con lo stesso nome nel destinatario.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-145">If there is no matching property for a given parameter in the `anonymous_object_initializer`, the argument is the evaluation of the field or property of the same name on the receiver.</span></span>

<span data-ttu-id="a3a9c-146">L'ordine di valutazione degli effetti collaterali è il seguente, in cui ogni espressione viene valutata esattamente una volta:</span><span class="sxs-lookup"><span data-stu-id="a3a9c-146">The order of evaluation of side effects is as follows, with each expression evaluated exactly once:</span></span>

1. <span data-ttu-id="a3a9c-147">Espressione ricevitore</span><span class="sxs-lookup"><span data-stu-id="a3a9c-147">Receiver expression</span></span>

2. <span data-ttu-id="a3a9c-148">Espressioni nel `anonymous_object_initializer`, in ordine lessicale</span><span class="sxs-lookup"><span data-stu-id="a3a9c-148">Expressions in the `anonymous_object_initializer`, in lexical order</span></span>

3. <span data-ttu-id="a3a9c-149">Valutazione delle proprietà corrispondenti ai parametri del metodo `With`, in ordine di definizione dei parametri del metodo di `With`.</span><span class="sxs-lookup"><span data-stu-id="a3a9c-149">The evaluation of any properties matching the `With` method parameters, in order of definition of the `With` method parameters.</span></span>