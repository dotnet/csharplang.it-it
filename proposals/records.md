---
ms.openlocfilehash: 35f6836e20776450ce5f776e7fdb66ca634d04a0
ms.sourcegitcommit: 0f56445e250ddf82b88848b94c59870f13ab8ffc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84663267"
---

# <a name="records"></a><span data-ttu-id="169f9-101">Record</span><span class="sxs-lookup"><span data-stu-id="169f9-101">Records</span></span>

<span data-ttu-id="169f9-102">Questa proposta tiene traccia delle specifiche per la funzionalità record di C# 9, come stabilito dal team di progettazione del linguaggio C#.</span><span class="sxs-lookup"><span data-stu-id="169f9-102">This proposal tracks the specification for the C# 9 records feature, as agreed to by the C# language design team.</span></span>

<span data-ttu-id="169f9-103">La sintassi per un record è la seguente:</span><span class="sxs-lookup"><span data-stu-id="169f9-103">The syntax for a record is as follows:</span></span>

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

<span data-ttu-id="169f9-104">I tipi di record sono tipi di riferimento, simili a una dichiarazione di classe.</span><span class="sxs-lookup"><span data-stu-id="169f9-104">Record types are reference types, similar to a class declaration.</span></span> <span data-ttu-id="169f9-105">È un errore che un record fornisca un oggetto `record_base` `argument_list` se `record_declaration` non contiene un oggetto `parameter_list` .</span><span class="sxs-lookup"><span data-stu-id="169f9-105">It is an error for a record to provide a `record_base` `argument_list` if the `record_declaration` does not contain a `parameter_list`.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="169f9-106">Membri di un tipo di record</span><span class="sxs-lookup"><span data-stu-id="169f9-106">Members of a record type</span></span>

<span data-ttu-id="169f9-107">Oltre ai membri dichiarati nel corpo del record, un tipo di record ha membri sintetizzati aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="169f9-107">In addition to the members declared in the record body, a record type has additional synthesized members.</span></span>
<span data-ttu-id="169f9-108">I membri vengono sintetizzati a meno che un membro con una firma "corrispondente" non venga dichiarato nel corpo del record o un membro non virtuale concreto accessibile con una firma "corrispondente" venga ereditato.</span><span class="sxs-lookup"><span data-stu-id="169f9-108">Members are synthesized unless a member with a "matching" signature is declared in the record body or an accessible concrete non-virtual member with a "matching" signature is inherited.</span></span>
<span data-ttu-id="169f9-109">Due membri vengono considerati corrispondenti se hanno la stessa firma o verrebbero considerati "nascosti" in uno scenario di ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="169f9-109">Two members are considered matching if they have the same signature or would be considered "hiding" in an inheritance scenario.</span></span>

<span data-ttu-id="169f9-110">I membri sintetizzati sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="169f9-110">The synthesized members are as follows:</span></span>

### <a name="equality-members"></a><span data-ttu-id="169f9-111">Membri di uguaglianza</span><span class="sxs-lookup"><span data-stu-id="169f9-111">Equality members</span></span>

<span data-ttu-id="169f9-112">I tipi di record producono implementazioni sintetizzate dei metodi seguenti, dove `T` è il tipo che lo contiene:</span><span class="sxs-lookup"><span data-stu-id="169f9-112">Record types produce synthesized implementations of the following methods, where `T` is the containing type:</span></span>
```C#
public override int GetHashCode();
public override bool Equals(object other);
public virtual bool Equals(T other);
```
<span data-ttu-id="169f9-113">`GetHashCode()`e `Equals(object other)` sono override dei metodi virtuali in `System.Object` .</span><span class="sxs-lookup"><span data-stu-id="169f9-113">`GetHashCode()` and `Equals(object other)` are overrides of the virtual methods in `System.Object`.</span></span>
<span data-ttu-id="169f9-114">Quando si esegue l'override di tutti i metodi delle classi di base intermedie che nascondono tali metodi viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="169f9-114">Any methods on intermediate base classes that would hide those methods are ignored when overriding.</span></span>

<span data-ttu-id="169f9-115">Anche i tipi di record derivati eseguono l'override del `Equals(TBase other)` metodo da ogni tipo di record di base.</span><span class="sxs-lookup"><span data-stu-id="169f9-115">Derived record types also override the `Equals(TBase other)` method from each base record type.</span></span>

<span data-ttu-id="169f9-116">Il tipo di record sintetizza un'implementazione di `System.IEquatable<T>` che viene implementata in modo implicito da `Equals(T other)` Where `T` è il tipo che lo contiene.</span><span class="sxs-lookup"><span data-stu-id="169f9-116">The record type synthesizes an implementation of `System.IEquatable<T>` that is implicitly implemented by `Equals(T other)` where `T` is the containing type.</span></span>
<span data-ttu-id="169f9-117">I tipi di record non sintetizzano le implementazioni di `System.IEquatable<TBase>` per qualsiasi tipo di base `TBase` , anche se tali interfacce sono implementate dai tipi di record di base.</span><span class="sxs-lookup"><span data-stu-id="169f9-117">Record types do not synthesize implementations of `System.IEquatable<TBase>` for any base type `TBase`, even if those interfaces are implemented by the base record types.</span></span>

<span data-ttu-id="169f9-118">La classe di record di base sintetizza una `EqualityContract` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="169f9-118">The base record class synthesizes an `EqualityContract` property.</span></span> <span data-ttu-id="169f9-119">La proprietà viene sottoposta a override nelle classi di record derivate.</span><span class="sxs-lookup"><span data-stu-id="169f9-119">The property is overridden in derived record classes.</span></span> <span data-ttu-id="169f9-120">Le implementazioni sintetizzate restituiscono `typeof(T)` Where che `T` contiene il tipo.</span><span class="sxs-lookup"><span data-stu-id="169f9-120">The synthesized implementations return `typeof(T)` where `T` is containing type.</span></span>
```C#
protected virtual Type EqualityContract { get; }
```

<span data-ttu-id="169f9-121">Si tratta di un errore se le implementazioni di base di uno dei membri sottoposti a override sono sealed o non virtuali oppure non corrispondono alla firma e all'accessibilità previste.</span><span class="sxs-lookup"><span data-stu-id="169f9-121">It is an error if the base implementations of any of the overridden members is sealed or non-virtual, or do not match the expected signature and accessibility.</span></span>

<span data-ttu-id="169f9-122">`Equals(T other)`Restituisce true solo se si verificano tutti i termini seguenti:</span><span class="sxs-lookup"><span data-stu-id="169f9-122">`Equals(T other)` returns true if and only if each of the following terms are true:</span></span>
- <span data-ttu-id="169f9-123">`other`non è `null` , e</span><span class="sxs-lookup"><span data-stu-id="169f9-123">`other` is not `null`, and</span></span>
- <span data-ttu-id="169f9-124">Per ogni campo dichiarato nel tipo di record, il valore di `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` dove `TN` è il tipo di campo e</span><span class="sxs-lookup"><span data-stu-id="169f9-124">For each field declared in the record type, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="169f9-125">Se è presente un tipo di record di base, il valore di `base.Equals(other)` ; in caso contrario, il valore di `EqualityContract.Equals(other.EqualityContract)` .</span><span class="sxs-lookup"><span data-stu-id="169f9-125">If there is a base record type, the value of `base.Equals(other)`; otherwise the value of `EqualityContract.Equals(other.EqualityContract)`.</span></span>

<span data-ttu-id="169f9-126">Le sostituzioni di `Equals(T other)` per i metodi di base, incluso `object.Equals(object other)` , eseguono l'equivalente di:</span><span class="sxs-lookup"><span data-stu-id="169f9-126">The overrides of `Equals(T other)` for the base methods, including `object.Equals(object other)`, perform the equivalent of:</span></span>
```C#
public override bool Equals(object other) => Equals(other as T);
```

<span data-ttu-id="169f9-127">`GetHashCode()`Restituisce il `int` risultato di una funzione deterministica che accetta i valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="169f9-127">`GetHashCode()` returns the `int` result of a deterministic function taking the following values:</span></span>
- <span data-ttu-id="169f9-128">Per ogni campo dichiarato nel tipo di record, il valore di `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` dove `TN` è il tipo di campo e</span><span class="sxs-lookup"><span data-stu-id="169f9-128">For each field declared in the record type, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="169f9-129">Se è presente un tipo di record di base, il valore di `base.GetHashCode()` ; in caso contrario, il valore di `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)` .</span><span class="sxs-lookup"><span data-stu-id="169f9-129">If there is a base record type, the value of `base.GetHashCode()`; otherwise the value of `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)`.</span></span>

### <a name="copy-and-clone-members"></a><span data-ttu-id="169f9-130">Copiare e clonare i membri</span><span class="sxs-lookup"><span data-stu-id="169f9-130">Copy and Clone members</span></span>

<span data-ttu-id="169f9-131">Un tipo di record contiene due membri di copia:</span><span class="sxs-lookup"><span data-stu-id="169f9-131">A record type contains two copying members:</span></span>

* <span data-ttu-id="169f9-132">Costruttore che accetta un solo argomento del tipo di record.</span><span class="sxs-lookup"><span data-stu-id="169f9-132">A constructor taking a single argument of the record type.</span></span> <span data-ttu-id="169f9-133">Viene definito "costruttore di copia".</span><span class="sxs-lookup"><span data-stu-id="169f9-133">It is referred to as a "copy constructor".</span></span>
* <span data-ttu-id="169f9-134">Metodo "clone" dell'istanza virtuale senza parametri pubblico sintetizzato con un nome riservato dal compilatore</span><span class="sxs-lookup"><span data-stu-id="169f9-134">A synthesized public parameterless virtual instance "clone" method with a compiler-reserved name</span></span>

<span data-ttu-id="169f9-135">Lo scopo del costruttore di copia è copiare lo stato dal parametro alla nuova istanza creata.</span><span class="sxs-lookup"><span data-stu-id="169f9-135">The purpose of the copy constructor is to copy the state from the parameter to the new instance being created.</span></span> <span data-ttu-id="169f9-136">Questo costruttore non esegue gli inizializzatori di proprietà o campi di istanza presenti nella dichiarazione di record.</span><span class="sxs-lookup"><span data-stu-id="169f9-136">This constructor doesn't run any instance field/property initializers present in the record declaration.</span></span> <span data-ttu-id="169f9-137">Se il costruttore non è dichiarato in modo esplicito, un costruttore protetto verrà sintetizzato dal compilatore.</span><span class="sxs-lookup"><span data-stu-id="169f9-137">If the constructor is not explicitly declared, a protected constructor will be synthesized by the compiler.</span></span>
<span data-ttu-id="169f9-138">Per prima cosa, il costruttore deve eseguire la chiamata a un costruttore di copia della base o a un costruttore di oggetti senza parametri se il record eredita dall'oggetto.</span><span class="sxs-lookup"><span data-stu-id="169f9-138">The first thing the constructor must do, is to call a copy constructor of the base, or a parameter-less object constructor if the record inherits from object.</span></span> <span data-ttu-id="169f9-139">Se un costruttore di copia definito dall'utente utilizza un inizializzatore di costruttore implicito o esplicito che non soddisfa questo requisito, viene restituito un errore.</span><span class="sxs-lookup"><span data-stu-id="169f9-139">An error is reported if a user-defined copy constructor uses an implicit or explicit constructor initializer that doesn't fulfill this requirement.</span></span>
<span data-ttu-id="169f9-140">Dopo che un costruttore di copia di base viene richiamato, un costruttore di copia sintetizzato copia i valori per tutti i campi di istanza dichiarati in modo implicito o esplicito nel tipo di record.</span><span class="sxs-lookup"><span data-stu-id="169f9-140">After a base copy constructor is invoked, a synthesized copy constructor copies values for all instance fields implicitly or explicitly declared within the record type.</span></span>

<span data-ttu-id="169f9-141">Il metodo "clone" restituisce il risultato di una chiamata a un costruttore con la stessa firma del costruttore di copia.</span><span class="sxs-lookup"><span data-stu-id="169f9-141">The "clone" method returns the result of a call to a constructor with the same signature as the copy constructor.</span></span> <span data-ttu-id="169f9-142">Il tipo restituito del metodo Clone è il tipo che lo contiene, a meno che non sia presente un metodo clone virtuale nella classe di base.</span><span class="sxs-lookup"><span data-stu-id="169f9-142">The return type of the clone method is the containing type, unless a virtual clone method is present in the base class.</span></span> <span data-ttu-id="169f9-143">In tal caso, il tipo restituito è il tipo contenitore corrente se la funzionalità "revariante Returns" è supportata e il tipo restituito di override in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="169f9-143">In that case, the return type is the current containing type if the "covariant returns" feature is supported and the override return type otherwise.</span></span> <span data-ttu-id="169f9-144">Il metodo Clone sintetizzato è un override del metodo clone del tipo di base se ne esiste uno.</span><span class="sxs-lookup"><span data-stu-id="169f9-144">The synthesized clone method is an override of the base type clone method if one exists.</span></span> <span data-ttu-id="169f9-145">Se il metodo clone del tipo di base è sealed, viene generato un errore.</span><span class="sxs-lookup"><span data-stu-id="169f9-145">An error is produced if the base type clone method is sealed.</span></span>

<span data-ttu-id="169f9-146">Se il record contenitore è astratto, anche il metodo Clone sintetizzato è astratto.</span><span class="sxs-lookup"><span data-stu-id="169f9-146">If the containing record is abstract, the synthesized clone method is also abstract.</span></span>

## <a name="positional-record-members"></a><span data-ttu-id="169f9-147">Membri record posizionali</span><span class="sxs-lookup"><span data-stu-id="169f9-147">Positional record members</span></span>

<span data-ttu-id="169f9-148">Oltre ai membri precedenti, i record con un elenco di parametri ("record posizionali") sintetizzano membri aggiuntivi con le stesse condizioni dei membri precedenti.</span><span class="sxs-lookup"><span data-stu-id="169f9-148">In addition to the above members, records with a parameter list ("positional records") synthesize additional members with the same conditions as the members above.</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="169f9-149">Costruttore primario</span><span class="sxs-lookup"><span data-stu-id="169f9-149">Primary Constructor</span></span>

<span data-ttu-id="169f9-150">Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo.</span><span class="sxs-lookup"><span data-stu-id="169f9-150">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="169f9-151">Questo metodo è denominato costruttore primario per il tipo e determina l'eliminazione del costruttore della classe predefinito dichiarato in modo implicito, se presente.</span><span class="sxs-lookup"><span data-stu-id="169f9-151">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="169f9-152">È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.</span><span class="sxs-lookup"><span data-stu-id="169f9-152">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="169f9-153">In fase di esecuzione il costruttore primario</span><span class="sxs-lookup"><span data-stu-id="169f9-153">At runtime the primary constructor</span></span>

1. <span data-ttu-id="169f9-154">esegue gli inizializzatori di istanza visualizzati nel corpo della classe</span><span class="sxs-lookup"><span data-stu-id="169f9-154">executes the instance initializers appearing in the class-body</span></span>

1. <span data-ttu-id="169f9-155">richiama il costruttore della classe base con gli argomenti specificati nella `record_base` clausola, se presente</span><span class="sxs-lookup"><span data-stu-id="169f9-155">invokes the base class constructor with the arguments provided in the `record_base` clause, if present</span></span>

<span data-ttu-id="169f9-156">Se un record ha un costruttore primario, qualsiasi costruttore definito dall'utente, ad eccezione del costruttore di copia, deve disporre di un `this` inizializzatore di costruttore esplicito.</span><span class="sxs-lookup"><span data-stu-id="169f9-156">If a record has a primary constructor, any user-defined constructor, except "copy constructor" must have an explicit `this` constructor initializer.</span></span> 

<span data-ttu-id="169f9-157">I parametri del costruttore primario e i membri del record si trovano nell'ambito all'interno della `argument_list` clausola della `record_base` clausola e negli inizializzatori dei campi o delle proprietà dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="169f9-157">Parameters of the primary constructor as well as members of the record are in scope within the `argument_list` of the `record_base` clause and within initializers of instance fields or properties.</span></span> <span data-ttu-id="169f9-158">I membri di istanza sarebbero un errore in queste posizioni (analogamente a come i membri dell'istanza sono inclusi nell'ambito negli inizializzatori di costruttori normali, ma un errore da usare), ma i parametri del costruttore primario sarebbero inclusi nell'ambito e utilizzabili e verrebbero nascosti ai membri.</span><span class="sxs-lookup"><span data-stu-id="169f9-158">Instance members would be an error in these locations (similar to how instance members are in scope in regular constructor initializers today, but an error to use), but the parameters of the primary constructor would be in scope and useable and would shadow members.</span></span> <span data-ttu-id="169f9-159">I membri statici sarebbero anche utilizzabili, in modo analogo al funzionamento delle chiamate e degli inizializzatori di base nei costruttori normali.</span><span class="sxs-lookup"><span data-stu-id="169f9-159">Static members would also be useable, similar to how base calls and initializers work in ordinary constructors today.</span></span> 

<span data-ttu-id="169f9-160">Le variabili di espressione dichiarate nell'oggetto sono incluse nell' `argument_list` ambito di `argument_list` .</span><span class="sxs-lookup"><span data-stu-id="169f9-160">Expression variables declared in the `argument_list` are in scope within the `argument_list`.</span></span> <span data-ttu-id="169f9-161">Si applicano le stesse regole di shadowing in un elenco di argomenti di un inizializzatore di costruttore normale.</span><span class="sxs-lookup"><span data-stu-id="169f9-161">The same shadowing rules as within an argument list of a regular constructor initializer apply.</span></span>

### <a name="properties"></a><span data-ttu-id="169f9-162">Proprietà</span><span class="sxs-lookup"><span data-stu-id="169f9-162">Properties</span></span>

<span data-ttu-id="169f9-163">Per ogni parametro di record di una dichiarazione del tipo di record è presente un membro della proprietà pubblica corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value.</span><span class="sxs-lookup"><span data-stu-id="169f9-163">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span>

<span data-ttu-id="169f9-164">Per un record:</span><span class="sxs-lookup"><span data-stu-id="169f9-164">For a record:</span></span>

* <span data-ttu-id="169f9-165">`get` `init` Viene creata una proprietà pubblica e automatica (vedere Specifica della `init` funzione di accesso separata).</span><span class="sxs-lookup"><span data-stu-id="169f9-165">A public `get` and `init` auto-property is created (see separate `init` accessor specification).</span></span>
  <span data-ttu-id="169f9-166">Ogni funzione di accesso astratta ereditata "corrispondente" viene sottoposta a override.</span><span class="sxs-lookup"><span data-stu-id="169f9-166">Each "matching" inherited abstract accessor is overridden.</span></span> <span data-ttu-id="169f9-167">La proprietà auto viene inizializzata sul valore del parametro del costruttore primario corrispondente.</span><span class="sxs-lookup"><span data-stu-id="169f9-167">The auto-property is initialized to the value of the corresponding primary constructor parameter.</span></span>

### <a name="deconstruct"></a><span data-ttu-id="169f9-168">Decostruire</span><span class="sxs-lookup"><span data-stu-id="169f9-168">Deconstruct</span></span>

<span data-ttu-id="169f9-169">Un record posizionale sintetizza un metodo pubblico che restituisce un valore void denominato Deconstruct con una dichiarazione di parametro out per ogni parametro della dichiarazione del costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="169f9-169">A positional record synthesizes a public void-returning method called Deconstruct with an out parameter declaration for each parameter of the primary constructor declaration.</span></span> <span data-ttu-id="169f9-170">Ogni parametro del metodo Deconstruct è dello stesso tipo del parametro corrispondente della dichiarazione del costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="169f9-170">Each parameter of the Deconstruct method has the same type as the corresponding parameter of the primary constructor declaration.</span></span> <span data-ttu-id="169f9-171">Il corpo del metodo assegna a un membro con lo stesso nome ogni parametro del metodo di decostruzione al valore di un membro di istanza.</span><span class="sxs-lookup"><span data-stu-id="169f9-171">The body of the method assigns each parameter of the Deconstruct method to the value from an instance member access to a member of the same name.</span></span>

## <a name="with-expression"></a><span data-ttu-id="169f9-172">Espressione `with`</span><span class="sxs-lookup"><span data-stu-id="169f9-172">`with` expression</span></span>

<span data-ttu-id="169f9-173">Un' `with` espressione è una nuova espressione che usa la sintassi seguente.</span><span class="sxs-lookup"><span data-stu-id="169f9-173">A `with` expression is a new expression using the following syntax.</span></span>

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

<span data-ttu-id="169f9-174">Un' `with` espressione consente la "mutazione non distruttiva", progettata per produrre una copia dell'espressione Receiver con modifiche nelle assegnazioni in `member_initializer_list` .</span><span class="sxs-lookup"><span data-stu-id="169f9-174">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications in assignments in the `member_initializer_list`.</span></span>

<span data-ttu-id="169f9-175">Un' `with` espressione valida ha un ricevitore con un tipo non void.</span><span class="sxs-lookup"><span data-stu-id="169f9-175">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="169f9-176">Il tipo di ricevitore deve contenere un metodo "clone" del record sintetizzato accessibile.</span><span class="sxs-lookup"><span data-stu-id="169f9-176">The receiver type must contain an accessible synthesized record "clone" method.</span></span>

<span data-ttu-id="169f9-177">Sul lato destro dell' `with` espressione è `member_initializer_list` presente una con una sequenza di assegnazioni all' *identificatore*, che deve essere un campo di istanza accessibile o una proprietà del tipo restituito del `Clone()` metodo.</span><span class="sxs-lookup"><span data-stu-id="169f9-177">On the right hand side of the `with` expression is a `member_initializer_list` with a sequence of assignments to *identifier*, which must be an accessible instance field or property of the return type of the `Clone()` method.</span></span>

<span data-ttu-id="169f9-178">Ogni `member_initializer` viene elaborato allo stesso modo di un'assegnazione a un campo o a una proprietà per l'accesso al valore restituito del metodo clone del record.</span><span class="sxs-lookup"><span data-stu-id="169f9-178">Each `member_initializer` is processed the same way as an assignment to a field or property access of the return value of the record clone method.</span></span> <span data-ttu-id="169f9-179">Il metodo Clone viene eseguito una sola volta e le assegnazioni vengono elaborate in ordine lessicale.</span><span class="sxs-lookup"><span data-stu-id="169f9-179">The clone method is executed only once and the assignments are processed in lexical order.</span></span>
