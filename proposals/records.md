---
ms.openlocfilehash: 82f68e2e2703f1d78c191d1120781ab7306b327a
ms.sourcegitcommit: 100d3f7f04ba1e4f666c188fbd73762f2c3b8716
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84421869"
---

# <a name="records"></a><span data-ttu-id="845c8-101">Record</span><span class="sxs-lookup"><span data-stu-id="845c8-101">Records</span></span>

<span data-ttu-id="845c8-102">Questa proposta tiene traccia delle specifiche per la funzionalità record di C# 9, come stabilito dal team di progettazione del linguaggio C#.</span><span class="sxs-lookup"><span data-stu-id="845c8-102">This proposal tracks the specification for the C# 9 records feature, as agreed to by the C# language design team.</span></span>

<span data-ttu-id="845c8-103">La sintassi per un record è la seguente:</span><span class="sxs-lookup"><span data-stu-id="845c8-103">The syntax for a record is as follows:</span></span>

```antlr
record_declaration
    : attributes? class_modifier* 'partial'? 'record' identifier type_parameter_list?
      parameter_list? record_base? type_parameter_constraints_clause* record_body
    ;

record_base
    : ':' class_type argument_list?
    | ':' interface_type_list
    | ':' class_type argument_list? interface_type_list
    ;

record_body
    : '{' class_member_declaration* '}'
    | ';'
    ;
```

<span data-ttu-id="845c8-104">I tipi di record sono tipi di riferimento, simili a una dichiarazione di classe.</span><span class="sxs-lookup"><span data-stu-id="845c8-104">Record types are reference types, similar to a class declaration.</span></span> <span data-ttu-id="845c8-105">È un errore che un record fornisca un oggetto `record_base` `argument_list` se `record_declaration` non contiene un oggetto `parameter_list` .</span><span class="sxs-lookup"><span data-stu-id="845c8-105">It is an error for a record to provide a `record_base` `argument_list` if the `record_declaration` does not contain a `parameter_list`.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="845c8-106">Membri di un tipo di record</span><span class="sxs-lookup"><span data-stu-id="845c8-106">Members of a record type</span></span>

<span data-ttu-id="845c8-107">Oltre ai membri dichiarati nel corpo del record, un tipo di record ha membri sintetizzati aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="845c8-107">In addition to the members declared in the record body, a record type has additional synthesized members.</span></span>
<span data-ttu-id="845c8-108">I membri vengono sintetizzati, a meno che un membro concreto (non astratto) accessibile con una firma "corrispondente" sia ereditato o dichiarato nel corpo del record.</span><span class="sxs-lookup"><span data-stu-id="845c8-108">Members are synthesized unless an accessible concrete (non-abstract) member with a "matching" signature is either inherited or declared in the record body.</span></span> <span data-ttu-id="845c8-109">Due membri vengono considerati corrispondenti se hanno la stessa firma o verrebbero considerati "nascosti" in uno scenario di ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="845c8-109">Two members are considered matching if they have the same signature or would be considered "hiding" in an inheritance scenario.</span></span>

<span data-ttu-id="845c8-110">I membri sintetizzati sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="845c8-110">The synthesized members are as follows:</span></span>

### <a name="equality-members"></a><span data-ttu-id="845c8-111">Membri di uguaglianza</span><span class="sxs-lookup"><span data-stu-id="845c8-111">Equality members</span></span>

<span data-ttu-id="845c8-112">I tipi di record producono implementazioni sintetizzate per i metodi seguenti, dove `T` è il tipo che lo contiene:</span><span class="sxs-lookup"><span data-stu-id="845c8-112">Record types produce synthesized implementations for the following methods, where `T` is the containing type:</span></span>

* <span data-ttu-id="845c8-113">`object.GetHashCode()`override</span><span class="sxs-lookup"><span data-stu-id="845c8-113">`object.GetHashCode()` override</span></span>
* <span data-ttu-id="845c8-114">`object.Equals(object)`override</span><span class="sxs-lookup"><span data-stu-id="845c8-114">`object.Equals(object)` override</span></span>
* <span data-ttu-id="845c8-115">`T Equals(T)`Metodo, dove `T` è il tipo corrente</span><span class="sxs-lookup"><span data-stu-id="845c8-115">`T Equals(T)` method, where `T` is the current type</span></span>
* <span data-ttu-id="845c8-116">`Type EqualityContract`Proprietà solo Get</span><span class="sxs-lookup"><span data-stu-id="845c8-116">`Type EqualityContract` get-only property</span></span>

<span data-ttu-id="845c8-117">Se `object.GetHashCode()` o `object.Equals(object)` sono sealed, viene generato un errore.</span><span class="sxs-lookup"><span data-stu-id="845c8-117">If either `object.GetHashCode()` or `object.Equals(object)` are sealed, an error is produced.</span></span>

<span data-ttu-id="845c8-118">`EqualityContract`è una proprietà dell'istanza virtuale che restituisce `typeof(T)` .</span><span class="sxs-lookup"><span data-stu-id="845c8-118">`EqualityContract` is a virtual instance property which returns `typeof(T)`.</span></span> <span data-ttu-id="845c8-119">Se il tipo di base definisce un oggetto `EqualityContract` , ne viene eseguito l'override nel record derivato.</span><span class="sxs-lookup"><span data-stu-id="845c8-119">If the base type defines an `EqualityContract` it is overridden in the derived record.</span></span> <span data-ttu-id="845c8-120">Se la base `EqualityContract` è sealed o non virtuale, viene generato un errore.</span><span class="sxs-lookup"><span data-stu-id="845c8-120">If the base `EqualityContract` is sealed or non-virtual, an error is produced.</span></span>

<span data-ttu-id="845c8-121">`T Equals(T)`viene specificato per eseguire l'uguaglianza dei valori `Equals` in modo che sia true solo se tutti i campi di istanza accessibili nel ricevitore sono uguali ai campi del parametro e è `this.EqualityContract` uguale a `other.EqualityContract` .</span><span class="sxs-lookup"><span data-stu-id="845c8-121">`T Equals(T)` is specified to perform value equality such that `Equals` is true if and only if all accessible instance fields in the receiver are equal to the fields of the parameter and `this.EqualityContract` equals `other.EqualityContract`.</span></span>

<span data-ttu-id="845c8-122">`object.Equals`esegue l'equivalente di</span><span class="sxs-lookup"><span data-stu-id="845c8-122">`object.Equals` performs the equivalent of</span></span>

```C#
override Equals(object o) => Equals(o as T);
```

### <a name="copy-and-clone-members"></a><span data-ttu-id="845c8-123">Copiare e clonare i membri</span><span class="sxs-lookup"><span data-stu-id="845c8-123">Copy and Clone members</span></span>

<span data-ttu-id="845c8-124">Un tipo di record contiene due membri di copia sintetizzati:</span><span class="sxs-lookup"><span data-stu-id="845c8-124">A record type contains two synthesized copying members:</span></span>

* <span data-ttu-id="845c8-125">Costruttore protetto che accetta un solo argomento del tipo di record.</span><span class="sxs-lookup"><span data-stu-id="845c8-125">A protected constructor taking a single argument of the record type.</span></span>
* <span data-ttu-id="845c8-126">Metodo "clone" dell'istanza virtuale senza parametri pubblico con un nome riservato dal compilatore</span><span class="sxs-lookup"><span data-stu-id="845c8-126">A public parameterless virtual instance "clone" method with a compiler-reserved name</span></span>

<span data-ttu-id="845c8-127">Il costruttore protetto viene definito "costruttore di copia" e il corpo sintetizzato copia i valori di tutti i campi di istanza accessibili nel tipo di input nei campi corrispondenti di `this` .</span><span class="sxs-lookup"><span data-stu-id="845c8-127">The protected constructor is referred to as the "copy constructor" and the synthesized body copies the values of all accessible instance fields in the input type to the corresponding fields of `this`.</span></span>

<span data-ttu-id="845c8-128">Il metodo "clone" restituisce il risultato di una chiamata a un costruttore con la stessa firma del costruttore di copia.</span><span class="sxs-lookup"><span data-stu-id="845c8-128">The "clone" method returns the result of a call to a constructor with the same signature as the copy constructor.</span></span> <span data-ttu-id="845c8-129">Il tipo restituito del metodo Clone è il tipo che lo contiene, a meno che non sia presente un metodo clone virtuale nella classe di base.</span><span class="sxs-lookup"><span data-stu-id="845c8-129">The return type of the clone method is the containing type, unless a virtual clone method is present in the base class.</span></span> <span data-ttu-id="845c8-130">In tal caso, il tipo restituito è il tipo contenitore corrente se la funzionalità "revariante Returns" è supportata e il tipo restituito di override in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="845c8-130">In that case, the return type is the current containing type if the "covariant returns" feature is supported and the override return type otherwise.</span></span> <span data-ttu-id="845c8-131">Il metodo Clone sintetizzato è un override del metodo clone del tipo di base se ne esiste uno.</span><span class="sxs-lookup"><span data-stu-id="845c8-131">The synthesized clone method is an override of the base type clone method if one exists.</span></span> <span data-ttu-id="845c8-132">Se il metodo clone del tipo di base è sealed, viene generato un errore.</span><span class="sxs-lookup"><span data-stu-id="845c8-132">An error is produced if the base type clone method is sealed.</span></span>

## <a name="positional-record-members"></a><span data-ttu-id="845c8-133">Membri record posizionali</span><span class="sxs-lookup"><span data-stu-id="845c8-133">Positional record members</span></span>

<span data-ttu-id="845c8-134">Oltre ai membri precedenti, i record con un elenco di parametri ("record posizionali") sintetizzano membri aggiuntivi con le stesse condizioni dei membri precedenti.</span><span class="sxs-lookup"><span data-stu-id="845c8-134">In addition to the above members, records with a parameter list ("positional records") synthesize additional members with the same conditions as the members above.</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="845c8-135">Costruttore primario</span><span class="sxs-lookup"><span data-stu-id="845c8-135">Primary Constructor</span></span>

<span data-ttu-id="845c8-136">Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo.</span><span class="sxs-lookup"><span data-stu-id="845c8-136">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="845c8-137">Questo metodo è denominato costruttore primario per il tipo e determina l'eliminazione del costruttore della classe predefinito dichiarato in modo implicito, se presente.</span><span class="sxs-lookup"><span data-stu-id="845c8-137">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="845c8-138">È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.</span><span class="sxs-lookup"><span data-stu-id="845c8-138">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="845c8-139">In fase di esecuzione il costruttore primario</span><span class="sxs-lookup"><span data-stu-id="845c8-139">At runtime the primary constructor</span></span>

1. <span data-ttu-id="845c8-140">esegue gli inizializzatori di istanza visualizzati nel corpo della classe</span><span class="sxs-lookup"><span data-stu-id="845c8-140">executes the instance initializers appearing in the class-body</span></span>

1. <span data-ttu-id="845c8-141">richiama il costruttore della classe base con gli argomenti specificati nella `record_base` clausola, se presente</span><span class="sxs-lookup"><span data-stu-id="845c8-141">invokes the base class constructor with the arguments provided in the `record_base` clause, if present</span></span>


### <a name="properties"></a><span data-ttu-id="845c8-142">Proprietà</span><span class="sxs-lookup"><span data-stu-id="845c8-142">Properties</span></span>

<span data-ttu-id="845c8-143">Per ogni parametro di record di una dichiarazione del tipo di record è presente un membro della proprietà pubblica corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value.</span><span class="sxs-lookup"><span data-stu-id="845c8-143">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span>

<span data-ttu-id="845c8-144">Per un record:</span><span class="sxs-lookup"><span data-stu-id="845c8-144">For a record:</span></span>

* <span data-ttu-id="845c8-145">`get` `init` Viene creata una proprietà pubblica e automatica (vedere Specifica della `init` funzione di accesso separata).</span><span class="sxs-lookup"><span data-stu-id="845c8-145">A public `get` and `init` auto-property is created (see separate `init` accessor specification).</span></span>
  <span data-ttu-id="845c8-146">Ogni funzione di accesso astratta ereditata "corrispondente" viene sottoposta a override.</span><span class="sxs-lookup"><span data-stu-id="845c8-146">Each "matching" inherited abstract accessor is overridden.</span></span> <span data-ttu-id="845c8-147">La proprietà auto viene inizializzata sul valore del parametro del costruttore primario corrispondente.</span><span class="sxs-lookup"><span data-stu-id="845c8-147">The auto-property is initialized to the value of the corresponding primary constructor parameter.</span></span>

### <a name="deconstruct"></a><span data-ttu-id="845c8-148">Decostruire</span><span class="sxs-lookup"><span data-stu-id="845c8-148">Deconstruct</span></span>

<span data-ttu-id="845c8-149">Un record posizionale sintetizza un metodo pubblico che restituisce un valore void denominato Deconstruct con una dichiarazione di parametro out per ogni parametro della dichiarazione del costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="845c8-149">A positional record synthesizes a public void-returning method called Deconstruct with an out parameter declaration for each parameter of the primary constructor declaration.</span></span> <span data-ttu-id="845c8-150">Ogni parametro del metodo Deconstruct è dello stesso tipo del parametro corrispondente della dichiarazione del costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="845c8-150">Each parameter of the Deconstruct method has the same type as the corresponding parameter of the primary constructor declaration.</span></span> <span data-ttu-id="845c8-151">Il corpo del metodo assegna a un membro con lo stesso nome ogni parametro del metodo di decostruzione al valore di un membro di istanza.</span><span class="sxs-lookup"><span data-stu-id="845c8-151">The body of the method assigns each parameter of the Deconstruct method to the value from an instance member access to a member of the same name.</span></span>

## <a name="with-expression"></a><span data-ttu-id="845c8-152">Espressione `with`</span><span class="sxs-lookup"><span data-stu-id="845c8-152">`with` expression</span></span>

<span data-ttu-id="845c8-153">Un' `with` espressione è una nuova espressione che usa la sintassi seguente.</span><span class="sxs-lookup"><span data-stu-id="845c8-153">A `with` expression is a new expression using the following syntax.</span></span>

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

<span data-ttu-id="845c8-154">Un' `with` espressione consente la "mutazione non distruttiva", progettata per produrre una copia dell'espressione Receiver con modifiche nelle assegnazioni in `member_initializer_list` .</span><span class="sxs-lookup"><span data-stu-id="845c8-154">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications in assignments in the `member_initializer_list`.</span></span>

<span data-ttu-id="845c8-155">Un' `with` espressione valida ha un ricevitore con un tipo non void.</span><span class="sxs-lookup"><span data-stu-id="845c8-155">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="845c8-156">Il tipo di ricevitore deve contenere un metodo "clone" del record sintetizzato accessibile.</span><span class="sxs-lookup"><span data-stu-id="845c8-156">The receiver type must contain an accessible synthesized record "clone" method.</span></span>

<span data-ttu-id="845c8-157">Sul lato destro dell' `with` espressione è `member_initializer_list` presente una con una sequenza di assegnazioni all' *identificatore*, che deve essere un campo di istanza accessibile o una proprietà del tipo restituito del `Clone()` metodo.</span><span class="sxs-lookup"><span data-stu-id="845c8-157">On the right hand side of the `with` expression is a `member_initializer_list` with a sequence of assignments to *identifier*, which must be an accessible instance field or property of the return type of the `Clone()` method.</span></span>

<span data-ttu-id="845c8-158">Ogni `member_initializer` viene elaborato allo stesso modo di un'assegnazione a un campo o a una proprietà per l'accesso al valore restituito del metodo clone del record.</span><span class="sxs-lookup"><span data-stu-id="845c8-158">Each `member_initializer` is processed the same way as an assignment to a field or property access of the return value of the record clone method.</span></span> <span data-ttu-id="845c8-159">Il metodo Clone viene eseguito una sola volta e le assegnazioni vengono elaborate in ordine lessicale.</span><span class="sxs-lookup"><span data-stu-id="845c8-159">The clone method is executed only once and the assignments are processed in lexical order.</span></span>
