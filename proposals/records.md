---
ms.openlocfilehash: 6d8f0512d478d3d82cb262466e49a22a9e3cd0ee
ms.sourcegitcommit: ae114131069ca76e4a1ec8149b7bab81fce8965c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84227979"
---
# <a name="records-work-in-progress"></a><span data-ttu-id="2a3dd-101">Record di lavoro in corso</span><span class="sxs-lookup"><span data-stu-id="2a3dd-101">Records Work-in-Progress</span></span>

<span data-ttu-id="2a3dd-102">Diversamente dalle altre proposte di record, non si tratta di una proposta di per sé, ma è stata progettata per registrare le decisioni di progettazione di consenso per la funzionalità dei record.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-102">Unlike the other records proposals, this is not a proposal in itself, but a work-in-progress designed to record consensus design decisions for the records feature.</span></span> <span data-ttu-id="2a3dd-103">I dettagli della specifica verranno aggiunti in modo necessario per risolvere le domande.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-103">Specification detail will be added as necessary to resolve questions.</span></span>

<span data-ttu-id="2a3dd-104">La sintassi per un record è proposta per essere aggiunta come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="2a3dd-104">The syntax for a record is proposed to be added as follows:</span></span>

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

<span data-ttu-id="2a3dd-105">Il `attributes` non terminale consentirà anche un nuovo attributo contestuale, `data` .</span><span class="sxs-lookup"><span data-stu-id="2a3dd-105">The `attributes` non-terminal will also permit a new contextual attribute, `data`.</span></span>

<span data-ttu-id="2a3dd-106">Una classe (struct) dichiarata con un elenco di parametri o un `data` modificatore viene chiamata classe di record (struct di record), che è un tipo di record.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-106">A class (struct) declared with a parameter list or `data` modifier is called a record class (record struct), either of which is a record type.</span></span>

<span data-ttu-id="2a3dd-107">È un errore dichiarare un tipo di record senza un elenco di parametri e il `data` modificatore.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-107">It is an error to declare a record type without both a parameter list and the `data` modifier.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="2a3dd-108">Membri di un tipo di record</span><span class="sxs-lookup"><span data-stu-id="2a3dd-108">Members of a record type</span></span>

<span data-ttu-id="2a3dd-109">Oltre ai membri dichiarati nel corpo della classe o dello struct, un tipo di record presenta i membri aggiuntivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="2a3dd-109">In addition to the members declared in the class or struct body, a record type has the following additional members:</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="2a3dd-110">Costruttore primario</span><span class="sxs-lookup"><span data-stu-id="2a3dd-110">Primary Constructor</span></span>

<span data-ttu-id="2a3dd-111">Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-111">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="2a3dd-112">Questo metodo è denominato costruttore primario per il tipo e determina l'eliminazione del costruttore della classe predefinito dichiarato in modo implicito, se presente.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-112">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="2a3dd-113">È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-113">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="2a3dd-114">In fase di esecuzione il costruttore primario</span><span class="sxs-lookup"><span data-stu-id="2a3dd-114">At runtime the primary constructor</span></span>

1. <span data-ttu-id="2a3dd-115">esegue gli inizializzatori di campo di istanza visualizzati nel corpo della classe; quindi richiama il costruttore della classe base senza argomenti.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-115">executes the instance field initializers appearing in the class-body; and then  invokes the base class constructor with no arguments.</span></span>

1. <span data-ttu-id="2a3dd-116">Inizializza i campi di supporto generati dal compilatore per le proprietà corrispondenti ai parametri del valore (se queste proprietà sono fornite dal compilatore</span><span class="sxs-lookup"><span data-stu-id="2a3dd-116">initializes compiler-generated backing fields for the properties corresponding to the value parameters (if these properties are compiler-provided</span></span>

### <a name="properties"></a><span data-ttu-id="2a3dd-117">Proprietà</span><span class="sxs-lookup"><span data-stu-id="2a3dd-117">Properties</span></span>

<span data-ttu-id="2a3dd-118">Per ogni parametro di record di una dichiarazione del tipo di record è presente un membro della proprietà pubblica corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-118">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span> <span data-ttu-id="2a3dd-119">Se nessuna proprietà concreta (ovvero non astratta) con una funzione di accesso get e con il nome e il tipo specificati viene dichiarata o ereditata in modo esplicito, viene prodotta dal compilatore come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="2a3dd-119">If no concrete (i.e. non-abstract) property with a get accessor and with this name and type is explicitly declared or inherited, it is produced by the compiler as follows:</span></span>

<span data-ttu-id="2a3dd-120">Per uno struct di record o una classe di record:</span><span class="sxs-lookup"><span data-stu-id="2a3dd-120">For a record struct or a record class:</span></span>

* <span data-ttu-id="2a3dd-121">`get` `init` Viene creata una proprietà pubblica e automatica (vedere Specifica della `init` funzione di accesso separata).</span><span class="sxs-lookup"><span data-stu-id="2a3dd-121">A public `get` and `init` auto-property is created (see separate `init` accessor specification).</span></span> <span data-ttu-id="2a3dd-122">Il valore viene inizializzato durante la costruzione con il valore del parametro del costruttore primario corrispondente.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-122">Its value is initialized during construction with the value of the corresponding primary constructor parameter.</span></span> <span data-ttu-id="2a3dd-123">Ogni funzione di accesso get della proprietà astratta "corrispondente" ereditata viene sottoposta a override.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-123">Each "matching" inherited abstract property's get accessor is overridden.</span></span>

### <a name="equality-members"></a><span data-ttu-id="2a3dd-124">Membri di uguaglianza</span><span class="sxs-lookup"><span data-stu-id="2a3dd-124">Equality members</span></span>

<span data-ttu-id="2a3dd-125">I tipi di record producono implementazioni sintetizzate per i metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="2a3dd-125">Record types produce synthesized implementations for the following methods:</span></span>

* <span data-ttu-id="2a3dd-126">`object.GetHashCode()`eseguire l'override di, a meno che non sia sealed o fornito dall'utente</span><span class="sxs-lookup"><span data-stu-id="2a3dd-126">`object.GetHashCode()` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="2a3dd-127">`object.Equals(object)`eseguire l'override di, a meno che non sia sealed o fornito dall'utente</span><span class="sxs-lookup"><span data-stu-id="2a3dd-127">`object.Equals(object)` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="2a3dd-128">`T Equals(T)`Metodo, dove `T` è il tipo corrente</span><span class="sxs-lookup"><span data-stu-id="2a3dd-128">`T Equals(T)` method, where `T` is the current type</span></span>

<span data-ttu-id="2a3dd-129">`T Equals(T)`viene specificato per eseguire l'uguaglianza dei valori in modo che `Equals` sia true solo se tutti i campi di istanza dichiarati nel tipo di ricevitore sono uguali ai campi dell'altro tipo.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-129">`T Equals(T)` is specified to perform value equality such that `Equals` is true if and only if all the instance fields declared in the receiver type are equal to the fields of the other type.</span></span>

<span data-ttu-id="2a3dd-130">`object.Equals`esegue l'equivalente di</span><span class="sxs-lookup"><span data-stu-id="2a3dd-130">`object.Equals` performs the equivalent of</span></span>

```C#
override Equals(object o) => Equals(o as T);
```

### <a name="copy-and-clone-members"></a><span data-ttu-id="2a3dd-131">Copiare e clonare i membri</span><span class="sxs-lookup"><span data-stu-id="2a3dd-131">Copy and Clone members</span></span>

<span data-ttu-id="2a3dd-132">Un tipo di record contiene due membri di copia sintetizzati se i metodi con la stessa firma non sono già stati dichiarati all'interno del tipo di record:</span><span class="sxs-lookup"><span data-stu-id="2a3dd-132">A record type contains two synthesized copying members if methods with the same signature are not already declared within the record type:</span></span>

* <span data-ttu-id="2a3dd-133">Costruttore protetto che accetta un solo argomento del tipo di record.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-133">A protected constructor taking a single argument of the record type.</span></span>
* <span data-ttu-id="2a3dd-134">Metodo di istanza pubblico senza parametri denominato `Clone` che restituisce il tipo di record.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-134">A public parameterless instance method called `Clone` which returns the record type.</span></span>

<span data-ttu-id="2a3dd-135">Il costruttore protetto viene definito "costruttore di copia" e il corpo sintetizzato copia i valori di tutti i campi di istanza dichiarati nel tipo di input nei campi corrispondenti di `this` .</span><span class="sxs-lookup"><span data-stu-id="2a3dd-135">The protected constructor is referred to as the "copy constructor" and the synthesized body copies the values of all instance fields declared in the input type to the corresponding fields of `this`.</span></span>

<span data-ttu-id="2a3dd-136">Il `Clone` metodo restituisce il risultato di una chiamata a un costruttore con la stessa firma del costruttore di copia.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-136">The `Clone` method returns the result of a call to a constructor with the same signature as the copy constructor.</span></span>
## <a name="with-expression"></a><span data-ttu-id="2a3dd-137">Espressione `with`</span><span class="sxs-lookup"><span data-stu-id="2a3dd-137">`with` expression</span></span>

<span data-ttu-id="2a3dd-138">Un' `with` espressione è una nuova espressione che usa la sintassi seguente.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-138">A `with` expression is a new expression using the following syntax.</span></span>

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' '{' member_initializer_list? '}'
    ;
    
member_initializer_list
    : member_initializer (',' member_initializer)*
    ;

member_initializer
    : identifier '=' expression
    ;
```

<span data-ttu-id="2a3dd-139">Un' `with` espressione consente la "mutazione non distruttiva", progettata per produrre una copia dell'espressione Receiver con modifiche nelle assegnazioni in `member_initializer_list` .</span><span class="sxs-lookup"><span data-stu-id="2a3dd-139">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications in assignments in the `member_initializer_list`.</span></span>

<span data-ttu-id="2a3dd-140">Un' `with` espressione valida ha un ricevitore con un tipo non void.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-140">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="2a3dd-141">Il tipo di ricevitore deve contenere un metodo di istanza senza parametri accessibile denominato `Clone` il cui tipo restituito deve essere il tipo di ricevitore o un tipo di base.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-141">The receiver type must contain an accessible parameterless instance method called `Clone` whose return type must be the receiver type, or a base type thereof.</span></span>

<span data-ttu-id="2a3dd-142">Sul lato destro dell' `with` espressione è presente una `member_initializer_list` con una sequenza di assegnazioni all' *identificatore*, che deve essere un campo di istanza accessibile o una proprietà del tipo restituito del `Clone()` metodo.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-142">On the right hand side of the `with` expression is an `member_initializer_list` with a sequence of assignments to *identifier*, which must an accessible instance field or property of the return type of the `Clone()` method.</span></span>

<span data-ttu-id="2a3dd-143">Ogni `member_initializer` viene elaborata allo stesso modo di un'assegnazione al campo o alla destinazione della proprietà sul valore restituito del `Clone()` metodo.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-143">Each `member_initializer` is processed the same way as an assignment to the field or property target on the return value of the `Clone()` method.</span></span> <span data-ttu-id="2a3dd-144">Il `Clone()` metodo viene eseguito una sola volta e le assegnazioni vengono elaborate in ordine lessicale.</span><span class="sxs-lookup"><span data-stu-id="2a3dd-144">The `Clone()` method is executed only once and the assignments are processed in lexical order.</span></span>
