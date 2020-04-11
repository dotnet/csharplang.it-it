---
ms.openlocfilehash: 1fb1f3b9025d65cb39f675e60bae1cb6415fc184
ms.sourcegitcommit: 88202acd40ca04b6e513ea1e5993625ee26fac3f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2020
ms.locfileid: "81219628"
---
# <a name="records-work-in-progress"></a><span data-ttu-id="737ec-101">Registra Lavoro in corso</span><span class="sxs-lookup"><span data-stu-id="737ec-101">Records Work-in-Progress</span></span>

<span data-ttu-id="737ec-102">A differenza delle altre proposte di record, questa non è una proposta in sé, ma un work-in-progress progettato per registrare le decisioni di progettazione del consenso per la funzione di registrazione.</span><span class="sxs-lookup"><span data-stu-id="737ec-102">Unlike the other records proposals, this is not a proposal in itself, but a work-in-progress designed to record consensus design decisions for the records feature.</span></span> <span data-ttu-id="737ec-103">I dettagli delle specifiche verranno aggiunti in base alle esigenze per risolvere le domande.</span><span class="sxs-lookup"><span data-stu-id="737ec-103">Specification detail will be added as necessary to resolve questions.</span></span>

<span data-ttu-id="737ec-104">La sintassi di un record viene proposta per essere aggiunta come segue:</span><span class="sxs-lookup"><span data-stu-id="737ec-104">The syntax for a record is proposed to be added as follows:</span></span>

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

<span data-ttu-id="737ec-105">Il `attributes` non terminale consentirà anche un `data`nuovo attributo contestuale, .</span><span class="sxs-lookup"><span data-stu-id="737ec-105">The `attributes` non-terminal will also permit a new contextual attribute, `data`.</span></span>

<span data-ttu-id="737ec-106">Una classe (struct) dichiarata con `data` un elenco di parametri o un modificatore è denominata classe di record (struttura di record), uno dei quali è un tipo di record.</span><span class="sxs-lookup"><span data-stu-id="737ec-106">A class (struct) declared with a parameter list or `data` modifier is called a record class (record struct), either of which is a record type.</span></span>

<span data-ttu-id="737ec-107">È un errore dichiarare un tipo di record `data` senza un elenco di parametri e il modificatore.</span><span class="sxs-lookup"><span data-stu-id="737ec-107">It is an error to declare a record type without both a parameter list and the `data` modifier.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="737ec-108">Membri di un tipo di record</span><span class="sxs-lookup"><span data-stu-id="737ec-108">Members of a record type</span></span>

<span data-ttu-id="737ec-109">Oltre ai membri dichiarati nel corpo della classe o dello struct, un tipo di record dispone dei seguenti membri aggiuntivi:</span><span class="sxs-lookup"><span data-stu-id="737ec-109">In addition to the members declared in the class or struct body, a record type has the following additional members:</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="737ec-110">Costruttore primario</span><span class="sxs-lookup"><span data-stu-id="737ec-110">Primary Constructor</span></span>

<span data-ttu-id="737ec-111">Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri value della dichiarazione del tipo.</span><span class="sxs-lookup"><span data-stu-id="737ec-111">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="737ec-112">Questo è chiamato il costruttore primario per il tipo e fa sì che il costruttore di classe predefinito dichiarato in modo implicito, se presente, da eliminare.</span><span class="sxs-lookup"><span data-stu-id="737ec-112">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="737ec-113">È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.</span><span class="sxs-lookup"><span data-stu-id="737ec-113">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="737ec-114">In fase di esecuzione il costruttore primario</span><span class="sxs-lookup"><span data-stu-id="737ec-114">At runtime the primary constructor</span></span>

1. <span data-ttu-id="737ec-115">esegue gli inizializzatori di campo di istanza che appaiono nel corpo della classe; e quindi richiama il costruttore della classe base senza argomenti.</span><span class="sxs-lookup"><span data-stu-id="737ec-115">executes the instance field initializers appearing in the class-body; and then  invokes the base class constructor with no arguments.</span></span>

1. <span data-ttu-id="737ec-116">inizializza i campi di supporto generati dal compilatore per le proprietà corrispondenti ai parametri di valore (se queste proprietà sono fornite dal compilatore</span><span class="sxs-lookup"><span data-stu-id="737ec-116">initializes compiler-generated backing fields for the properties corresponding to the value parameters (if these properties are compiler-provided</span></span>

### <a name="properties"></a><span data-ttu-id="737ec-117">Proprietà</span><span class="sxs-lookup"><span data-stu-id="737ec-117">Properties</span></span>

<span data-ttu-id="737ec-118">Per ogni parametro di record di una dichiarazione di tipo di record è presente un membro di proprietà pubblica corrispondente il cui nome e tipo sono tratti dalla dichiarazione del parametro di valore.</span><span class="sxs-lookup"><span data-stu-id="737ec-118">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span> <span data-ttu-id="737ec-119">Se nessuna proprietà concreta (ovvero non astratta) con una funzione di accesso get e con questo nome e tipo viene dichiarata o ereditata in modo esplicito, viene prodotta dal compilatore come segue:</span><span class="sxs-lookup"><span data-stu-id="737ec-119">If no concrete (i.e. non-abstract) property with a get accessor and with this name and type is explicitly declared or inherited, it is produced by the compiler as follows:</span></span>

<span data-ttu-id="737ec-120">Per una struttura di record o una classe di record:For a record struct or a record class:</span><span class="sxs-lookup"><span data-stu-id="737ec-120">For a record struct or a record class:</span></span>

* <span data-ttu-id="737ec-121">Viene creata una proprietà automatica get-only pubblica.</span><span class="sxs-lookup"><span data-stu-id="737ec-121">A public get-only auto-property is created.</span></span> <span data-ttu-id="737ec-122">Il valore viene inizializzato durante la costruzione con il valore del parametro del costruttore primario corrispondente.</span><span class="sxs-lookup"><span data-stu-id="737ec-122">Its value is initialized during construction with the value of the corresponding primary constructor parameter.</span></span> <span data-ttu-id="737ec-123">Viene esegua l'override di ogni funzione di accesso get della proprietà astratta ereditata "corrispondente".</span><span class="sxs-lookup"><span data-stu-id="737ec-123">Each "matching" inherited abstract property's get accessor is overridden.</span></span>

### <a name="equality-members"></a><span data-ttu-id="737ec-124">Membri di uguaglianza</span><span class="sxs-lookup"><span data-stu-id="737ec-124">Equality members</span></span>

<span data-ttu-id="737ec-125">I tipi di record producono implementazioni sintetizzate per i metodi seguenti:Record types produce synthesized implementations for the following methods:</span><span class="sxs-lookup"><span data-stu-id="737ec-125">Record types produce synthesized implementations for the following methods:</span></span>

* <span data-ttu-id="737ec-126">`object.GetHashCode()`override, a meno che non sia sigillato o fornito dall'utente</span><span class="sxs-lookup"><span data-stu-id="737ec-126">`object.GetHashCode()` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="737ec-127">`object.Equals(object)`override, a meno che non sia sigillato o fornito dall'utente</span><span class="sxs-lookup"><span data-stu-id="737ec-127">`object.Equals(object)` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="737ec-128">`T Equals(T)`metodo, `T` dove è il tipo corrente</span><span class="sxs-lookup"><span data-stu-id="737ec-128">`T Equals(T)` method, where `T` is the current type</span></span>

<span data-ttu-id="737ec-129">`T Equals(T)`viene specificato per eseguire l'uguaglianza di valori, confrontando la proprietà con lo stesso nome di ogni parametro del costruttore primario con la proprietà corrispondente dell'altro tipo.</span><span class="sxs-lookup"><span data-stu-id="737ec-129">`T Equals(T)` is specified to perform value equality, comparing the property with same name as each primary constructor parameter to the corresponding property of the other type.</span></span>
<span data-ttu-id="737ec-130">`object.Equals`esegue l'equivalente di</span><span class="sxs-lookup"><span data-stu-id="737ec-130">`object.Equals` performs the equivalent of</span></span>

```C#
override Equals(object o) => Equals(o as T);
```

## <a name="with-expression"></a><span data-ttu-id="737ec-131">Espressione `with`</span><span class="sxs-lookup"><span data-stu-id="737ec-131">`with` expression</span></span>

<span data-ttu-id="737ec-132">Un'espressione `with` è una nuova espressione che utilizza la sintassi seguente.</span><span class="sxs-lookup"><span data-stu-id="737ec-132">A `with` expression is a new expression using the following syntax.</span></span>

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' anonymous_object_initializer
```

<span data-ttu-id="737ec-133">Un'espressione `with` consente una "mutazione non distruttiva", progettata per produrre una `anonymous_object_initializer`copia dell'espressione del ricevitore con modifiche alle proprietà elencate nell'oggetto .</span><span class="sxs-lookup"><span data-stu-id="737ec-133">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications to properties listed in the `anonymous_object_initializer`.</span></span>

<span data-ttu-id="737ec-134">Un'espressione valida `with` ha un ricevitore con un tipo non void.</span><span class="sxs-lookup"><span data-stu-id="737ec-134">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="737ec-135">Il tipo di ricevitore deve contenere un metodo di istanza senza parametri accessibile chiamato `Clone` il cui tipo restituito deve essere il tipo del tipo espresso del ricevitore o un tipo di base.</span><span class="sxs-lookup"><span data-stu-id="737ec-135">The receiver type must contain an accessible parameterless instance method called `Clone` whose return type must be the type of the receiver express type, or a base type thereof.</span></span>

<span data-ttu-id="737ec-136">Sul lato destro `with` dell'espressione `anonymous_object_initializer` è un con una sequenza di assegnazioni con una proprietà di record generata dal compilatore del ricevitore sul lato sinistro dell'assegnazione e un'espressione arbitraria sul lato destro che è convertibile in modo implicito al tipo del lato sinistro.</span><span class="sxs-lookup"><span data-stu-id="737ec-136">On the right hand side of the `with` expression is an `anonymous_object_initializer` with a sequence of assignments with a compiler-generated record property of the receiver on the left-hand side of the assignment, and an arbitrary expression on the right-hand side which is implicitly convertible to the type of the left-hand side.</span></span>

<span data-ttu-id="737ec-137">La valutazione `with` di un'espressione `Clone` equivale a chiamare il metodo esattamente una volta e quindi a impostare il campo di supporto di `Clone` ogni proprietà di record nell'elenco di argomenti sull'espressione corrispondente, in ordine lessicale, utilizzando il risultato del metodo come ricevitore.</span><span class="sxs-lookup"><span data-stu-id="737ec-137">The evaluation of a `with` expression is equivalent to calling the `Clone` method exactly once, and then setting the backing field of each record property in the argument list to its corresponding expression, in lexical order, using the result of the `Clone` method as the receiver.</span></span>
