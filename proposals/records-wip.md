---
ms.openlocfilehash: 25756c1811d5e6dc97512ce70f99ab7fefa91c4a
ms.sourcegitcommit: 2a6dffb60718065ece95df75e1cc7eb509e48a8d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2020
ms.locfileid: "79485238"
---
# <a name="records-work-in-progress"></a><span data-ttu-id="c1a67-101">Record di lavoro in corso</span><span class="sxs-lookup"><span data-stu-id="c1a67-101">Records Work-in-Progress</span></span>

<span data-ttu-id="c1a67-102">Diversamente dalle altre proposte di record, non si tratta di una proposta di per sé, ma è stata progettata per registrare le decisioni di progettazione di consenso per la funzionalità dei record.</span><span class="sxs-lookup"><span data-stu-id="c1a67-102">Unlike the other records proposals, this is not a proposal in itself, but a work-in-progress designed to record consensus design decisions for the records feature.</span></span> <span data-ttu-id="c1a67-103">I dettagli della specifica verranno aggiunti in modo necessario per risolvere le domande.</span><span class="sxs-lookup"><span data-stu-id="c1a67-103">Specification detail will be added as necessary to resolve questions.</span></span>

<span data-ttu-id="c1a67-104">La sintassi per un record è proposta per essere aggiunta come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="c1a67-104">The syntax for a record is proposed to be added as follows:</span></span>

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

<span data-ttu-id="c1a67-105">Il `attributes` non terminale consentirà anche un nuovo attributo contestuale, `data`.</span><span class="sxs-lookup"><span data-stu-id="c1a67-105">The `attributes` non-terminal will also permit a new contextual attribute, `data`.</span></span>

<span data-ttu-id="c1a67-106">Una classe (struct) dichiarata con un elenco di parametri o un modificatore `data` viene chiamata classe di record (struct di record), che è un tipo di record.</span><span class="sxs-lookup"><span data-stu-id="c1a67-106">A class (struct) declared with a parameter list or `data` modifier is called a record class (record struct), either of which is a record type.</span></span>

<span data-ttu-id="c1a67-107">È un errore dichiarare un tipo di record senza un elenco di parametri e il modificatore `data`.</span><span class="sxs-lookup"><span data-stu-id="c1a67-107">It is an error to declare a record type without both a parameter list and the `data` modifier.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="c1a67-108">Membri di un tipo di record</span><span class="sxs-lookup"><span data-stu-id="c1a67-108">Members of a record type</span></span>

<span data-ttu-id="c1a67-109">Oltre ai membri dichiarati nel corpo della classe, un tipo di record presenta i membri aggiuntivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="c1a67-109">In addition to the members declared in the class-body, a record type has the following additional members:</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="c1a67-110">Costruttore primario</span><span class="sxs-lookup"><span data-stu-id="c1a67-110">Primary Constructor</span></span>

<span data-ttu-id="c1a67-111">Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo.</span><span class="sxs-lookup"><span data-stu-id="c1a67-111">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="c1a67-112">Questo metodo è denominato costruttore primario per il tipo e determina l'eliminazione del costruttore predefinito dichiarato in modo implicito.</span><span class="sxs-lookup"><span data-stu-id="c1a67-112">This is called the primary constructor for the type, and causes the implicitly declared default constructor to be suppressed.</span></span> <span data-ttu-id="c1a67-113">È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.</span><span class="sxs-lookup"><span data-stu-id="c1a67-113">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>
<span data-ttu-id="c1a67-114">In fase di esecuzione il costruttore primario</span><span class="sxs-lookup"><span data-stu-id="c1a67-114">At runtime the primary constructor</span></span> 

1. <span data-ttu-id="c1a67-115">esegue gli inizializzatori di campo di istanza visualizzati nel corpo della classe; quindi richiama il costruttore della classe base senza argomenti.</span><span class="sxs-lookup"><span data-stu-id="c1a67-115">executes the instance field initializers appearing in the class-body; and then  invokes the base class constructor with no arguments.</span></span>

1. <span data-ttu-id="c1a67-116">Inizializza i campi di supporto generati dal compilatore per le proprietà corrispondenti ai parametri del valore (se queste proprietà sono fornite dal compilatore; vedere [Proprietà sintetizzate](#Synthesized Properties))</span><span class="sxs-lookup"><span data-stu-id="c1a67-116">initializes compiler-generated backing fields for the properties corresponding to the value parameters (if these properties are compiler-provided; see [Synthesized properties](#Synthesized Properties))</span></span>


<span data-ttu-id="c1a67-117">[] TODO: aggiungere la sintassi e la specifica della chiamata di base sulla scelta del costruttore di base tramite la risoluzione dell'overload</span><span class="sxs-lookup"><span data-stu-id="c1a67-117">[ ] TODO: add base call syntax and specification about choosing base constructor through overload resolution</span></span>

### <a name="properties"></a><span data-ttu-id="c1a67-118">Proprietà</span><span class="sxs-lookup"><span data-stu-id="c1a67-118">Properties</span></span>

<span data-ttu-id="c1a67-119">Per ogni parametro di record di una dichiarazione del tipo di record è presente un membro della proprietà pubblica corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value.</span><span class="sxs-lookup"><span data-stu-id="c1a67-119">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span> <span data-ttu-id="c1a67-120">Se nessuna proprietà concreta (ovvero non astratta) con una funzione di accesso get e con il nome e il tipo specificati viene dichiarata o ereditata in modo esplicito, viene prodotta dal compilatore come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="c1a67-120">If no concrete (i.e. non-abstract) property with a get accessor and with this name and type is explicitly declared or inherited, it is produced by the compiler as follows:</span></span>

<span data-ttu-id="c1a67-121">Per uno struct di record o una classe di record:</span><span class="sxs-lookup"><span data-stu-id="c1a67-121">For a record struct or a record class:</span></span>

* <span data-ttu-id="c1a67-122">Viene creata una proprietà automatica Get-only pubblica.</span><span class="sxs-lookup"><span data-stu-id="c1a67-122">A public get-only auto-property is created.</span></span> <span data-ttu-id="c1a67-123">Il valore viene inizializzato durante la costruzione con il valore del parametro del costruttore primario corrispondente.</span><span class="sxs-lookup"><span data-stu-id="c1a67-123">Its value is initialized during construction with the value of the corresponding primary constructor parameter.</span></span> <span data-ttu-id="c1a67-124">Ogni funzione di accesso get della proprietà astratta "corrispondente" ereditata viene sottoposta a override.</span><span class="sxs-lookup"><span data-stu-id="c1a67-124">Each "matching" inherited abstract property's get accessor is overridden.</span></span>

### <a name="equality-members"></a><span data-ttu-id="c1a67-125">Membri di uguaglianza</span><span class="sxs-lookup"><span data-stu-id="c1a67-125">Equality members</span></span>

<span data-ttu-id="c1a67-126">I tipi di record producono implementazioni sintetizzate per i metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="c1a67-126">Record types produce synthesized implementations for the following methods:</span></span>

* <span data-ttu-id="c1a67-127">`object.GetHashCode()` sostituzione, a meno che non sia sealed o fornito dall'utente</span><span class="sxs-lookup"><span data-stu-id="c1a67-127">`object.GetHashCode()` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="c1a67-128">`object.Equals(object)` sostituzione, a meno che non sia sealed o fornito dall'utente</span><span class="sxs-lookup"><span data-stu-id="c1a67-128">`object.Equals(object)` override, unless it is sealed or user provided</span></span>
* <span data-ttu-id="c1a67-129">`T Equals(T)` metodo, dove `T` è il tipo corrente</span><span class="sxs-lookup"><span data-stu-id="c1a67-129">`T Equals(T)` method, where `T` is the current type</span></span>

<span data-ttu-id="c1a67-130">`T Equals(T)` viene specificato per eseguire l'uguaglianza dei valori, confrontando la proprietà con lo stesso nome di ogni parametro del costruttore primario per la proprietà corrispondente dell'altro tipo.</span><span class="sxs-lookup"><span data-stu-id="c1a67-130">`T Equals(T)` is specified to perform value equality, comparing the property with same name as each primary constructor parameter to the corresponding property of the other type.</span></span>
<span data-ttu-id="c1a67-131">`object.Equals` esegue l'equivalente di</span><span class="sxs-lookup"><span data-stu-id="c1a67-131">`object.Equals` performs the equivalent of</span></span>

```C#
override Equals(object o) => Equals(o as T);
```
