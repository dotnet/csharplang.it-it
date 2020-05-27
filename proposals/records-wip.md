---
ms.openlocfilehash: 758bc3fddf577b118fc50866a683fa96324e5dc3
ms.sourcegitcommit: 300226c009490828d615e86c50df7ac8c836f945
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83843599"
---
# <a name="records-work-in-progress"></a><span data-ttu-id="223ee-101">Record di lavoro in corso</span><span class="sxs-lookup"><span data-stu-id="223ee-101">Records Work-in-Progress</span></span>

<span data-ttu-id="223ee-102">Diversamente dalle altre proposte di record, non si tratta di una proposta di per sé, ma è stata progettata per registrare le decisioni di progettazione di consenso per la funzionalità dei record.</span><span class="sxs-lookup"><span data-stu-id="223ee-102">Unlike the other records proposals, this is not a proposal in itself, but a work-in-progress designed to record consensus design decisions for the records feature.</span></span> <span data-ttu-id="223ee-103">I dettagli della specifica verranno aggiunti in modo necessario per risolvere le domande.</span><span class="sxs-lookup"><span data-stu-id="223ee-103">Specification detail will be added as necessary to resolve questions.</span></span>

<span data-ttu-id="223ee-104">La sintassi per un record è proposta per essere aggiunta come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="223ee-104">The syntax for a record is proposed to be added as follows:</span></span>

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

<span data-ttu-id="223ee-105">Il `attributes` non terminale consentirà anche un nuovo attributo contestuale, `data` .</span><span class="sxs-lookup"><span data-stu-id="223ee-105">The `attributes` non-terminal will also permit a new contextual attribute, `data`.</span></span>

<span data-ttu-id="223ee-106">Una classe (struct) dichiarata con un elenco di parametri o un `data` modificatore viene chiamata classe di record (struct di record), che è un tipo di record.</span><span class="sxs-lookup"><span data-stu-id="223ee-106">A class (struct) declared with a parameter list or `data` modifier is called a record class (record struct), either of which is a record type.</span></span>

<span data-ttu-id="223ee-107">È un errore dichiarare un tipo di record senza un elenco di parametri e il `data` modificatore.</span><span class="sxs-lookup"><span data-stu-id="223ee-107">It is an error to declare a record type without both a parameter list and the `data` modifier.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="223ee-108">Membri di un tipo di record</span><span class="sxs-lookup"><span data-stu-id="223ee-108">Members of a record type</span></span>

<span data-ttu-id="223ee-109">Oltre ai membri dichiarati nel corpo della classe o dello struct, un tipo di record presenta i membri aggiuntivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="223ee-109">In addition to the members declared in the class or struct body, a record type has the following additional members:</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="223ee-110">Costruttore primario</span><span class="sxs-lookup"><span data-stu-id="223ee-110">Primary Constructor</span></span>

<span data-ttu-id="223ee-111">Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo.</span><span class="sxs-lookup"><span data-stu-id="223ee-111">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="223ee-112">Questo metodo è denominato costruttore primario per il tipo e determina l'eliminazione del costruttore della classe predefinito dichiarato in modo implicito, se presente.</span><span class="sxs-lookup"><span data-stu-id="223ee-112">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="223ee-113">È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.</span><span class="sxs-lookup"><span data-stu-id="223ee-113">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="223ee-114">In fase di esecuzione il costruttore primario</span><span class="sxs-lookup"><span data-stu-id="223ee-114">At runtime the primary constructor</span></span>

1. <span data-ttu-id="223ee-115">esegue gli inizializzatori di campo di istanza visualizzati nel corpo della classe; quindi richiama il costruttore della classe base senza argomenti.</span><span class="sxs-lookup"><span data-stu-id="223ee-115">executes the instance field initializers appearing in the class-body; and then  invokes the base class constructor with no arguments.</span></span>

1. <span data-ttu-id="223ee-116">Inizializza i campi di supporto generati dal compilatore per le proprietà corrispondenti ai parametri del valore (se queste proprietà sono fornite dal compilatore</span><span class="sxs-lookup"><span data-stu-id="223ee-116">initializes compiler-generated backing fields for the properties corresponding to the value parameters (if these properties are compiler-provided</span></span>

### <a name="properties"></a><span data-ttu-id="223ee-117">Proprietà</span><span class="sxs-lookup"><span data-stu-id="223ee-117">Properties</span></span>

<span data-ttu-id="223ee-118">Per ogni parametro di record di una dichiarazione del tipo di record è presente un membro della proprietà pubblica corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value.</span><span class="sxs-lookup"><span data-stu-id="223ee-118">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span> <span data-ttu-id="223ee-119">Se nessuna proprietà concreta (ovvero non astratta) con una funzione di accesso get e con il nome e il tipo specificati viene dichiarata o ereditata in modo esplicito, viene prodotta dal compilatore come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="223ee-119">If no concrete (i.e. non-abstract) property with a get accessor and with this name and type is explicitly declared or inherited, it is produced by the compiler as follows:</span></span>

<span data-ttu-id="223ee-120">Per uno struct di record o una classe di record:</span><span class="sxs-lookup"><span data-stu-id="223ee-120">For a record struct or a record class:</span></span>

* <span data-ttu-id="223ee-121">Viene creata una proprietà automatica Get-only pubblica.</span><span class="sxs-lookup"><span data-stu-id="223ee-121">A public get-only auto-property is created.</span></span> <span data-ttu-id="223ee-122">Il valore viene inizializzato durante la costruzione con il valore del parametro del costruttore primario corrispondente.</span><span class="sxs-lookup"><span data-stu-id="223ee-122">Its value is initialized during construction with the value of the corresponding primary constructor parameter.</span></span> <span data-ttu-id="223ee-123">Ogni funzione di accesso get della proprietà astratta "corrispondente" ereditata viene sottoposta a override.</span><span class="sxs-lookup"><span data-stu-id="223ee-123">Each "matching" inherited abstract property's get accessor is overridden.</span></span>

  * <span data-ttu-id="223ee-124">Questa proprietà è anche `initonly` , vale a dire che il campo sottostante può essere modificato nell'espressione [with](#With) sotto, se la `get` funzione di accesso corrispondente è accessibile</span><span class="sxs-lookup"><span data-stu-id="223ee-124">This property is also `initonly`, meaning the backing field can be modified in the [with](#With) expression below, if the corresponding `get` accessor is accessible</span></span>

### <a name="equality-members"></a><span data-ttu-id="223ee-125">Membri di uguaglianza</span><span class="sxs-lookup"><span data-stu-id="223ee-125">Equality members</span></span>

<span data-ttu-id="223ee-126">I tipi di record producono implementazioni sintetizzate per i metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="223ee-126">Record types produce synthesized implementations for the following methods:</span></span>

* <span data-ttu-id="223ee-127">`object.GetHashCode()`eseguire l'override di, a meno che non sia sealed o fornito dall'utente</span><span class="sxs-lookup"><span data-stu-id="223ee-127">`object.GetHashCode()` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="223ee-128">`object.Equals(object)`eseguire l'override di, a meno che non sia sealed o fornito dall'utente</span><span class="sxs-lookup"><span data-stu-id="223ee-128">`object.Equals(object)` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="223ee-129">`T Equals(T)`Metodo, dove `T` è il tipo corrente</span><span class="sxs-lookup"><span data-stu-id="223ee-129">`T Equals(T)` method, where `T` is the current type</span></span>

<span data-ttu-id="223ee-130">`T Equals(T)`viene specificato per eseguire l'uguaglianza dei valori in modo che `Equals` sia true solo se tutti i campi di istanza dichiarati nel tipo di ricevitore sono uguali ai campi dell'altro tipo.</span><span class="sxs-lookup"><span data-stu-id="223ee-130">`T Equals(T)` is specified to perform value equality such that `Equals` is true if and only if all the instance fields declared in the receiver type are equal to the fields of the other type.</span></span>

<span data-ttu-id="223ee-131">`object.Equals`esegue l'equivalente di</span><span class="sxs-lookup"><span data-stu-id="223ee-131">`object.Equals` performs the equivalent of</span></span>

```C#
override Equals(object o) => Equals(o as T);
```

## <a name="with-expression"></a><span data-ttu-id="223ee-132">Espressione `with`</span><span class="sxs-lookup"><span data-stu-id="223ee-132">`with` expression</span></span>

<span data-ttu-id="223ee-133">Un' `with` espressione è una nuova espressione che usa la sintassi seguente.</span><span class="sxs-lookup"><span data-stu-id="223ee-133">A `with` expression is a new expression using the following syntax.</span></span>

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' anonymous_object_initializer
```

<span data-ttu-id="223ee-134">Un' `with` espressione consente la "mutazione non distruttiva", progettata per produrre una copia dell'espressione Receiver con le modifiche alle proprietà elencate in `anonymous_object_initializer` .</span><span class="sxs-lookup"><span data-stu-id="223ee-134">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications to properties listed in the `anonymous_object_initializer`.</span></span>

<span data-ttu-id="223ee-135">Un' `with` espressione valida ha un ricevitore con un tipo non void.</span><span class="sxs-lookup"><span data-stu-id="223ee-135">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="223ee-136">Il tipo di ricevitore deve contenere un metodo di istanza senza parametri accessibile denominato `Clone` il cui tipo restituito deve essere il tipo di ricevitore o un tipo di base.</span><span class="sxs-lookup"><span data-stu-id="223ee-136">The receiver type must contain an accessible parameterless instance method called `Clone` whose return type must be the receiver type, or a base type thereof.</span></span>

<span data-ttu-id="223ee-137">Sul lato destro dell' `with` espressione è presente una `anonymous_object_initializer` con una sequenza di assegnazioni, ognuna con una `initonly` Proprietà (vedere [Proprietà](#Properties)) del `Clone` tipo restituito sul lato sinistro dell'assegnazione (come chiamata di proprietà) e un'espressione arbitraria sul lato destro, che è implicitamente convertibile nel tipo della proprietà.</span><span class="sxs-lookup"><span data-stu-id="223ee-137">On the right hand side of the `with` expression is an `anonymous_object_initializer` with a sequence of assignments, each with an `initonly` property (see [Properties](#Properties)) of the `Clone` return type on the left-hand side of the assignment (as a property invocation), and an arbitrary expression on the right-hand side which is implicitly convertible to the type of the property.</span></span>

<span data-ttu-id="223ee-138">La valutazione di un' `with` espressione chiama il `Clone` Metodo esattamente una volta, quindi imposta il campo sottostante di ogni `initonly` proprietà nell'elenco di argomenti sul valore dell'espressione corrispondente, in ordine lessicale, usando il risultato della `Clone` chiamata al metodo come ricevitore.</span><span class="sxs-lookup"><span data-stu-id="223ee-138">The evaluation of a `with` expression calls the `Clone` method exactly once, and then sets the backing field of each `initonly` property in the argument list to the value of its corresponding expression, in lexical order, using the result of the `Clone` method invocation as the receiver.</span></span> <span data-ttu-id="223ee-139">Il tipo dell' `with` espressione è uguale al tipo restituito del `Clone` metodo.</span><span class="sxs-lookup"><span data-stu-id="223ee-139">The type of the `with` expression is the same as the return type of the `Clone` method.</span></span>

