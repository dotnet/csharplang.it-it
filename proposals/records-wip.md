---
ms.openlocfilehash: e29f453eb33e06fb3c84fb542d8b74223af9b975
ms.sourcegitcommit: 7f0c8e4eac7afe75e4f312f54a554614384cd06b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80870982"
---
# <a name="records-work-in-progress"></a><span data-ttu-id="54ab3-101">Registra Lavoro in corso</span><span class="sxs-lookup"><span data-stu-id="54ab3-101">Records Work-in-Progress</span></span>

<span data-ttu-id="54ab3-102">A differenza delle altre proposte di record, questa non è una proposta in sé, ma un work-in-progress progettato per registrare le decisioni di progettazione del consenso per la funzione di registrazione.</span><span class="sxs-lookup"><span data-stu-id="54ab3-102">Unlike the other records proposals, this is not a proposal in itself, but a work-in-progress designed to record consensus design decisions for the records feature.</span></span> <span data-ttu-id="54ab3-103">I dettagli delle specifiche verranno aggiunti in base alle esigenze per risolvere le domande.</span><span class="sxs-lookup"><span data-stu-id="54ab3-103">Specification detail will be added as necessary to resolve questions.</span></span>

<span data-ttu-id="54ab3-104">La sintassi di un record viene proposta per essere aggiunta come segue:</span><span class="sxs-lookup"><span data-stu-id="54ab3-104">The syntax for a record is proposed to be added as follows:</span></span>

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

<span data-ttu-id="54ab3-105">Il `attributes` non terminale consentirà anche un `data`nuovo attributo contestuale, .</span><span class="sxs-lookup"><span data-stu-id="54ab3-105">The `attributes` non-terminal will also permit a new contextual attribute, `data`.</span></span>

<span data-ttu-id="54ab3-106">Una classe (struct) dichiarata con `data` un elenco di parametri o un modificatore è denominata classe di record (struttura di record), uno dei quali è un tipo di record.</span><span class="sxs-lookup"><span data-stu-id="54ab3-106">A class (struct) declared with a parameter list or `data` modifier is called a record class (record struct), either of which is a record type.</span></span>

<span data-ttu-id="54ab3-107">È un errore dichiarare un tipo di record `data` senza un elenco di parametri e il modificatore.</span><span class="sxs-lookup"><span data-stu-id="54ab3-107">It is an error to declare a record type without both a parameter list and the `data` modifier.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="54ab3-108">Membri di un tipo di record</span><span class="sxs-lookup"><span data-stu-id="54ab3-108">Members of a record type</span></span>

<span data-ttu-id="54ab3-109">Oltre ai membri dichiarati nel corpo della classe o dello struct, un tipo di record dispone dei seguenti membri aggiuntivi:</span><span class="sxs-lookup"><span data-stu-id="54ab3-109">In addition to the members declared in the class or struct body, a record type has the following additional members:</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="54ab3-110">Costruttore primario</span><span class="sxs-lookup"><span data-stu-id="54ab3-110">Primary Constructor</span></span>

<span data-ttu-id="54ab3-111">Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri value della dichiarazione del tipo.</span><span class="sxs-lookup"><span data-stu-id="54ab3-111">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="54ab3-112">Questo è chiamato il costruttore primario per il tipo e fa sì che il costruttore di classe predefinito dichiarato in modo implicito, se presente, da eliminare.</span><span class="sxs-lookup"><span data-stu-id="54ab3-112">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="54ab3-113">È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.</span><span class="sxs-lookup"><span data-stu-id="54ab3-113">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="54ab3-114">In fase di esecuzione il costruttore primario</span><span class="sxs-lookup"><span data-stu-id="54ab3-114">At runtime the primary constructor</span></span>

1. <span data-ttu-id="54ab3-115">esegue gli inizializzatori di campo di istanza che appaiono nel corpo della classe; e quindi richiama il costruttore della classe base senza argomenti.</span><span class="sxs-lookup"><span data-stu-id="54ab3-115">executes the instance field initializers appearing in the class-body; and then  invokes the base class constructor with no arguments.</span></span>

1. <span data-ttu-id="54ab3-116">inizializza i campi di supporto generati dal compilatore per le proprietà corrispondenti ai parametri di valore (se queste proprietà sono fornite dal compilatore</span><span class="sxs-lookup"><span data-stu-id="54ab3-116">initializes compiler-generated backing fields for the properties corresponding to the value parameters (if these properties are compiler-provided</span></span>

### <a name="properties"></a><span data-ttu-id="54ab3-117">Proprietà</span><span class="sxs-lookup"><span data-stu-id="54ab3-117">Properties</span></span>

<span data-ttu-id="54ab3-118">Per ogni parametro di record di una dichiarazione di tipo di record è presente un membro di proprietà pubblica corrispondente il cui nome e tipo sono tratti dalla dichiarazione del parametro di valore.</span><span class="sxs-lookup"><span data-stu-id="54ab3-118">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span> <span data-ttu-id="54ab3-119">Se nessuna proprietà concreta (ovvero non astratta) con una funzione di accesso get e con questo nome e tipo viene dichiarata o ereditata in modo esplicito, viene prodotta dal compilatore come segue:</span><span class="sxs-lookup"><span data-stu-id="54ab3-119">If no concrete (i.e. non-abstract) property with a get accessor and with this name and type is explicitly declared or inherited, it is produced by the compiler as follows:</span></span>

<span data-ttu-id="54ab3-120">Per una struttura di record o una classe di record:For a record struct or a record class:</span><span class="sxs-lookup"><span data-stu-id="54ab3-120">For a record struct or a record class:</span></span>

* <span data-ttu-id="54ab3-121">Viene creata una proprietà automatica get-only pubblica.</span><span class="sxs-lookup"><span data-stu-id="54ab3-121">A public get-only auto-property is created.</span></span> <span data-ttu-id="54ab3-122">Il valore viene inizializzato durante la costruzione con il valore del parametro del costruttore primario corrispondente.</span><span class="sxs-lookup"><span data-stu-id="54ab3-122">Its value is initialized during construction with the value of the corresponding primary constructor parameter.</span></span> <span data-ttu-id="54ab3-123">Viene esegua l'override di ogni funzione di accesso get della proprietà astratta ereditata "corrispondente".</span><span class="sxs-lookup"><span data-stu-id="54ab3-123">Each "matching" inherited abstract property's get accessor is overridden.</span></span>

### <a name="equality-members"></a><span data-ttu-id="54ab3-124">Membri di uguaglianza</span><span class="sxs-lookup"><span data-stu-id="54ab3-124">Equality members</span></span>

<span data-ttu-id="54ab3-125">I tipi di record producono implementazioni sintetizzate per i metodi seguenti:Record types produce synthesized implementations for the following methods:</span><span class="sxs-lookup"><span data-stu-id="54ab3-125">Record types produce synthesized implementations for the following methods:</span></span>

* <span data-ttu-id="54ab3-126">`object.GetHashCode()`override, a meno che non sia sigillato o fornito dall'utente</span><span class="sxs-lookup"><span data-stu-id="54ab3-126">`object.GetHashCode()` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="54ab3-127">`object.Equals(object)`override, a meno che non sia sigillato o fornito dall'utente</span><span class="sxs-lookup"><span data-stu-id="54ab3-127">`object.Equals(object)` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="54ab3-128">`T Equals(T)`metodo, `T` dove è il tipo corrente</span><span class="sxs-lookup"><span data-stu-id="54ab3-128">`T Equals(T)` method, where `T` is the current type</span></span>

<span data-ttu-id="54ab3-129">`T Equals(T)`viene specificato per eseguire l'uguaglianza di valori, confrontando la proprietà con lo stesso nome di ogni parametro del costruttore primario con la proprietà corrispondente dell'altro tipo.</span><span class="sxs-lookup"><span data-stu-id="54ab3-129">`T Equals(T)` is specified to perform value equality, comparing the property with same name as each primary constructor parameter to the corresponding property of the other type.</span></span>
<span data-ttu-id="54ab3-130">`object.Equals`esegue l'equivalente di</span><span class="sxs-lookup"><span data-stu-id="54ab3-130">`object.Equals` performs the equivalent of</span></span>

```C#
override Equals(object o) => Equals(o as T);
```

## <a name="with-expression"></a><span data-ttu-id="54ab3-131">Espressione `with`</span><span class="sxs-lookup"><span data-stu-id="54ab3-131">`with` expression</span></span>

<span data-ttu-id="54ab3-132">Un'espressione `with` è una nuova espressione che utilizza la sintassi seguente.</span><span class="sxs-lookup"><span data-stu-id="54ab3-132">A `with` expression is a new expression using the following syntax.</span></span>

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' anonymous_object_initializer
```

<span data-ttu-id="54ab3-133">Un'espressione `with` consente una "mutazione non distruttiva", progettata per produrre una `anonymous_object_initializer`copia dell'espressione del ricevitore con modifiche alle proprietà elencate nell'oggetto .</span><span class="sxs-lookup"><span data-stu-id="54ab3-133">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications to properties listed in the `anonymous_object_initializer`.</span></span>

<span data-ttu-id="54ab3-134">Un'espressione valida `with` ha un ricevitore con un tipo non void.</span><span class="sxs-lookup"><span data-stu-id="54ab3-134">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="54ab3-135">Il tipo di ricevitore deve `With` contenere un metodo di istanza accessibile chiamato con i parametri appropriati e il tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="54ab3-135">The receiver type must contain an accessible instance method called `With` with the appropriate parameters and return type.</span></span> <span data-ttu-id="54ab3-136">Si tratta di un errore se `With` sono presenti più metodi non di override.</span><span class="sxs-lookup"><span data-stu-id="54ab3-136">It is an error if there are multiple non-override `With` methods.</span></span> <span data-ttu-id="54ab3-137">Se sono `With` presenti più override, deve essere `With` presente un metodo non di override, ovvero il metodo di destinazione.</span><span class="sxs-lookup"><span data-stu-id="54ab3-137">If there are multiple `With` overrides, there must be a non-override `With` method, which is the target method.</span></span> <span data-ttu-id="54ab3-138">In caso contrario, `With` deve essere presente esattamente un metodo.</span><span class="sxs-lookup"><span data-stu-id="54ab3-138">Otherwise, there must be exactly one `With` method.</span></span>

<span data-ttu-id="54ab3-139">Sul lato destro `with` dell'espressione `anonymous_object_initializer` è un con una sequenza di assegnazioni con un campo o una proprietà del ricevitore sul lato sinistro dell'assegnazione e un'espressione arbitraria sul lato destro che è convertibile in modo implicito al tipo del lato sinistro.</span><span class="sxs-lookup"><span data-stu-id="54ab3-139">On the right hand side of the `with` expression is an `anonymous_object_initializer` with a sequence of assignments with a field or property of the receiver on the left-hand side of the assignment, and an arbitrary expression on the right-hand side which is implicitly convertible to the type of the left-hand side.</span></span>

<span data-ttu-id="54ab3-140">Dato un `With` metodo di destinazione, il tipo restituito deve essere il tipo del tipo di espressione del ricevitore o un tipo di base.</span><span class="sxs-lookup"><span data-stu-id="54ab3-140">Given a target `With` method, the return type must be the type of the receiver expression type, or a base type thereof.</span></span> <span data-ttu-id="54ab3-141">Per ogni parametro del `With` metodo, deve essere presente un campo di istanza corrispondente accessibile o una proprietà leggibile nel tipo di ricevitore con lo stesso nome e lo stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="54ab3-141">For each parameter of the `With` method, there must be an accessible corresponding instance field or readable property on the receiver type with the same name and the same type.</span></span> <span data-ttu-id="54ab3-142">Ogni proprietà o campo sul lato destro dell'espressione With deve corrispondere anche `With` a un parametro con lo stesso nome nel metodo.</span><span class="sxs-lookup"><span data-stu-id="54ab3-142">Each property or field in the right-hand side of the With expression must also correspond to a parameter of the same name in the `With` method.</span></span>

<span data-ttu-id="54ab3-143">Dato un `With` metodo valido, `with` la valutazione di `With` un'espressione equivale `anonymous_object_initializer` a chiamare il metodo con le espressioni nel parametro sostituito con lo stesso nome della proprietà sul lato sinistro.</span><span class="sxs-lookup"><span data-stu-id="54ab3-143">Given a valid `With` method, the evaluation of a `with` expression is equivalent to calling the `With` method with the expressions in the `anonymous_object_initializer` substituted for the parameter of the same name as the property on the left hand side.</span></span> <span data-ttu-id="54ab3-144">Se non esiste alcuna proprietà corrispondente `anonymous_object_initializer`per un determinato parametro in , l'argomento è la valutazione del campo o della proprietà con lo stesso nome sul ricevitore.</span><span class="sxs-lookup"><span data-stu-id="54ab3-144">If there is no matching property for a given parameter in the `anonymous_object_initializer`, the argument is the evaluation of the field or property of the same name on the receiver.</span></span>

<span data-ttu-id="54ab3-145">L'ordine di valutazione degli effetti collaterali è il seguente, con ogni espressione valutata esattamente una volta:</span><span class="sxs-lookup"><span data-stu-id="54ab3-145">The order of evaluation of side effects is as follows, with each expression evaluated exactly once:</span></span>

1. <span data-ttu-id="54ab3-146">Espressione ricevitore</span><span class="sxs-lookup"><span data-stu-id="54ab3-146">Receiver expression</span></span>

2. <span data-ttu-id="54ab3-147">Espressioni in `anonymous_object_initializer`ordine lessicale</span><span class="sxs-lookup"><span data-stu-id="54ab3-147">Expressions in the `anonymous_object_initializer`, in lexical order</span></span>

3. <span data-ttu-id="54ab3-148">Valutazione di tutte le `With` proprietà corrispondenti ai `With` parametri del metodo, in ordine di definizione dei parametri del metodo.</span><span class="sxs-lookup"><span data-stu-id="54ab3-148">The evaluation of any properties matching the `With` method parameters, in order of definition of the `With` method parameters.</span></span>
