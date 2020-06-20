---
ms.openlocfilehash: 7901748edc95322275fb6a5f3fa7d336ee51a3f0
ms.sourcegitcommit: d48f35e584faa741f610350003d8ea6a5bc1958d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85111342"
---

# <a name="records"></a><span data-ttu-id="554bc-101">Record</span><span class="sxs-lookup"><span data-stu-id="554bc-101">Records</span></span>

<span data-ttu-id="554bc-102">Questa proposta tiene traccia delle specifiche per la funzionalità record di C# 9, come stabilito dal team di progettazione del linguaggio C#.</span><span class="sxs-lookup"><span data-stu-id="554bc-102">This proposal tracks the specification for the C# 9 records feature, as agreed to by the C# language design team.</span></span>

<span data-ttu-id="554bc-103">La sintassi per un record è la seguente:</span><span class="sxs-lookup"><span data-stu-id="554bc-103">The syntax for a record is as follows:</span></span>

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

<span data-ttu-id="554bc-104">I tipi di record sono tipi di riferimento, simili a una dichiarazione di classe.</span><span class="sxs-lookup"><span data-stu-id="554bc-104">Record types are reference types, similar to a class declaration.</span></span> <span data-ttu-id="554bc-105">È un errore che un record fornisca un oggetto `record_base` `argument_list` se `record_declaration` non contiene un oggetto `parameter_list` .</span><span class="sxs-lookup"><span data-stu-id="554bc-105">It is an error for a record to provide a `record_base` `argument_list` if the `record_declaration` does not contain a `parameter_list`.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="554bc-106">Membri di un tipo di record</span><span class="sxs-lookup"><span data-stu-id="554bc-106">Members of a record type</span></span>

<span data-ttu-id="554bc-107">Oltre ai membri dichiarati nel corpo del record, un tipo di record ha membri sintetizzati aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="554bc-107">In addition to the members declared in the record body, a record type has additional synthesized members.</span></span>
<span data-ttu-id="554bc-108">I membri vengono sintetizzati a meno che un membro con una firma "corrispondente" non venga dichiarato nel corpo del record o un membro non virtuale concreto accessibile con una firma "corrispondente" venga ereditato.</span><span class="sxs-lookup"><span data-stu-id="554bc-108">Members are synthesized unless a member with a "matching" signature is declared in the record body or an accessible concrete non-virtual member with a "matching" signature is inherited.</span></span>
<span data-ttu-id="554bc-109">Due membri vengono considerati corrispondenti se hanno la stessa firma o verrebbero considerati "nascosti" in uno scenario di ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="554bc-109">Two members are considered matching if they have the same signature or would be considered "hiding" in an inheritance scenario.</span></span>

<span data-ttu-id="554bc-110">I membri sintetizzati sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="554bc-110">The synthesized members are as follows:</span></span>

### <a name="equality-members"></a><span data-ttu-id="554bc-111">Membri di uguaglianza</span><span class="sxs-lookup"><span data-stu-id="554bc-111">Equality members</span></span>

<span data-ttu-id="554bc-112">Il tipo di record include una `EqualityContract` proprietà virtuale ReadOnly sintetizzata.</span><span class="sxs-lookup"><span data-stu-id="554bc-112">The record type includes a synthesized `EqualityContract` readonly virtual property.</span></span> <span data-ttu-id="554bc-113">La proprietà viene sottoposta a override in ogni tipo di record derivato.</span><span class="sxs-lookup"><span data-stu-id="554bc-113">The property is overridden in each derived record type.</span></span>
<span data-ttu-id="554bc-114">La proprietà può essere dichiarata in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="554bc-114">The property can be declared explicitly.</span></span>
<span data-ttu-id="554bc-115">Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure se la dichiarazione esplicita non è `virtual` e il tipo di record non lo è `sealed` .</span><span class="sxs-lookup"><span data-stu-id="554bc-115">It is an error if the explicit declaration does not match the expected signature or accessibility, or if the explicit declaration is not `virtual` and the record type is not `sealed`.</span></span>
<span data-ttu-id="554bc-116">La proprietà sintetizzata restituisce `typeof(R)` dove `R` è il tipo di record.</span><span class="sxs-lookup"><span data-stu-id="554bc-116">The synthesized property returns `typeof(R)` where `R` is the record type.</span></span>
```C#
protected virtual Type EqualityContract { get; };
```
<span data-ttu-id="554bc-117">_È possibile omettere `EqualityContract` se il tipo di record è `sealed` e deriva da `System.Object` ?_</span><span class="sxs-lookup"><span data-stu-id="554bc-117">_Can we omit `EqualityContract` if the record type is `sealed` and derives from `System.Object`?_</span></span>

<span data-ttu-id="554bc-118">Il tipo di record implementa `System.IEquatable<R>` e include un overload fortemente tipizzato sintetizzato di `Equals(R? other)` dove `R` è il tipo di record.</span><span class="sxs-lookup"><span data-stu-id="554bc-118">The record type implements `System.IEquatable<R>` and includes a synthesized strongly-typed overload of `Equals(R? other)` where `R` is the record type.</span></span>
<span data-ttu-id="554bc-119">Il metodo è `public` e il metodo è `virtual` a meno che il tipo di record non sia `sealed` .</span><span class="sxs-lookup"><span data-stu-id="554bc-119">The method is `public`, and the method is `virtual` unless the record type is `sealed`.</span></span>
<span data-ttu-id="554bc-120">Il metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="554bc-120">The method can be declared explicitly.</span></span>
<span data-ttu-id="554bc-121">Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure se la dichiarazione esplicita non è `virtual` e il tipo di record non lo è `sealed` .</span><span class="sxs-lookup"><span data-stu-id="554bc-121">It is an error if the explicit declaration does not match the expected signature or accessibility, or the explicit declaration is not `virtual` and the record type is not `sealed`.</span></span>
```C#
public virtual bool Equals(R? other);
```
<span data-ttu-id="554bc-122">Il risultato della sintesi `Equals(R?)` restituisce `true` se e solo se ciascuno dei seguenti è `true` :</span><span class="sxs-lookup"><span data-stu-id="554bc-122">The synthesized `Equals(R?)` returns `true` if and only if each of the following are `true`:</span></span>
- <span data-ttu-id="554bc-123">`other`non è `null` , e</span><span class="sxs-lookup"><span data-stu-id="554bc-123">`other` is not `null`, and</span></span>
- <span data-ttu-id="554bc-124">Per ogni campo di istanza `fieldN` nel tipo di record non ereditato, il valore di `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` dove `TN` è il tipo di campo e</span><span class="sxs-lookup"><span data-stu-id="554bc-124">For each instance field `fieldN` in the record type that is not inherited, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="554bc-125">Se è presente un tipo di record di base, il valore di `base.Equals(other)` (una chiamata non virtuale a `public virtual bool Equals(Base? other)` ); in caso contrario, il valore di `EqualityContract == other.EqualityContract` .</span><span class="sxs-lookup"><span data-stu-id="554bc-125">If there is a base record type, the value of `base.Equals(other)` (a non-virtual call to `public virtual bool Equals(Base? other)`); otherwise the value of `EqualityContract == other.EqualityContract`.</span></span>

<span data-ttu-id="554bc-126">Se il tipo di record è derivato da un tipo `Base` di record di base, il tipo di record include un override sintetizzato dell'oggetto fortemente tipizzato `Equals(Base other)` .</span><span class="sxs-lookup"><span data-stu-id="554bc-126">If the record type is derived from a base record type `Base`, the record type includes a synthesized override of the strongly-typed `Equals(Base other)`.</span></span>
<span data-ttu-id="554bc-127">La sostituzione sintetizzata è `sealed` .</span><span class="sxs-lookup"><span data-stu-id="554bc-127">The synthesized override is `sealed`.</span></span>
<span data-ttu-id="554bc-128">Se la sostituzione viene dichiarata in modo esplicito, si tratta di un errore.</span><span class="sxs-lookup"><span data-stu-id="554bc-128">It is an error if the override is declared explicitly.</span></span>
<span data-ttu-id="554bc-129">L'override sintetizzato restituisce `Equals((object?)other)` .</span><span class="sxs-lookup"><span data-stu-id="554bc-129">The synthesized override returns `Equals((object?)other)`.</span></span>

<span data-ttu-id="554bc-130">Il tipo di record include un override sintetizzato di `object.Equals(object? obj)` .</span><span class="sxs-lookup"><span data-stu-id="554bc-130">The record type includes a synthesized override of `object.Equals(object? obj)`.</span></span>
<span data-ttu-id="554bc-131">Se la sostituzione viene dichiarata in modo esplicito, si tratta di un errore.</span><span class="sxs-lookup"><span data-stu-id="554bc-131">It is an error if the override is declared explicitly.</span></span>
<span data-ttu-id="554bc-132">L'override sintetizzato restituisce `Equals(other as R)` dove `R` è il tipo di record.</span><span class="sxs-lookup"><span data-stu-id="554bc-132">The synthesized override returns `Equals(other as R)` where `R` is the record type.</span></span>
```C#
public override bool Equals(object? obj);
```

<span data-ttu-id="554bc-133">Il tipo di record include un override sintetizzato di `object.GetHashCode()` .</span><span class="sxs-lookup"><span data-stu-id="554bc-133">The record type includes a synthesized override of `object.GetHashCode()`.</span></span>
<span data-ttu-id="554bc-134">Il metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="554bc-134">The method can be declared explicitly.</span></span>
<span data-ttu-id="554bc-135">Si tratta di un errore se la dichiarazione esplicita è `sealed` , a meno che il tipo di record non sia `sealed` .</span><span class="sxs-lookup"><span data-stu-id="554bc-135">It is an error if the explicit declaration is `sealed` unless the record type is `sealed`.</span></span>
```C#
public override int GetHashCode();
```
<span data-ttu-id="554bc-136">Viene segnalato un avviso se uno di `Equals(R?)` e `GetHashCode()` viene dichiarato in modo esplicito, ma l'altro metodo non è esplicito.</span><span class="sxs-lookup"><span data-stu-id="554bc-136">A warning is reported if one of `Equals(R?)` and `GetHashCode()` is explicitly declared but the other method is not explicit.</span></span>

<span data-ttu-id="554bc-137">L'override sintetizzato di `GetHashCode()` restituisce `int` il risultato di una funzione deterministica che combina i valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="554bc-137">The synthesized override of `GetHashCode()` returns an `int` result of a deterministic function combining the following values:</span></span>
- <span data-ttu-id="554bc-138">Per ogni campo di istanza `fieldN` nel tipo di record non ereditato, il valore di `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` dove `TN` è il tipo di campo e</span><span class="sxs-lookup"><span data-stu-id="554bc-138">For each instance field `fieldN` in the record type that is not inherited, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="554bc-139">Se è presente un tipo di record di base, il valore di `base.GetHashCode()` ; in caso contrario, il valore di `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)` .</span><span class="sxs-lookup"><span data-stu-id="554bc-139">If there is a base record type, the value of `base.GetHashCode()`; otherwise the value of `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)`.</span></span>

<span data-ttu-id="554bc-140">Si considerino, ad esempio, i seguenti tipi di record:</span><span class="sxs-lookup"><span data-stu-id="554bc-140">For example, consider the following record types:</span></span>
```C#
record R1(T1 P1);
record R2(T1 P1, T2 P2) : R1(P1);
record R2(T1 P1, T2 P2, T3 P3) : R2(P1, P2);
```

<span data-ttu-id="554bc-141">Per questi tipi di record, i membri sintetizzati sono simili ai seguenti:</span><span class="sxs-lookup"><span data-stu-id="554bc-141">For those record types, the synthesized members would be something like:</span></span>
```C#
class R1 : IEquatable<R1>
{
    public T1 P1 { get; set; }
    protected virtual Type EqualityContract => typeof(R1);
    public override bool Equals(object? obj) => Equals(obj as R1);
    public virtual bool Equals(R1? other)
    {
        return !(other is null) &&
            EqualityContract == other.EqualityContract &&
            EqualityComparer<T1>.Default.Equals(P1, other.P1);
    }
    public override int GetHashCode()
    {
        return Combine(EqualityComparer<Type>.Default.GetHashCode(EqualityContract),
            EqualityComparer<T1>.Default.GetHashCode(P1));
    }
}

class R2 : R1, IEquatable<R2>
{
    public T2 P2 { get; set; }
    protected override Type EqualityContract => typeof(R2);
    public override bool Equals(object? obj) => Equals(obj as R2);
    public sealed override bool Equals(R1? other) => Equals((object?)other);
    public virtual bool Equals(R2? other)
    {
        return base.Equals((R1?)other) &&
            EqualityComparer<T2>.Default.Equals(P2, other.P2);
    }
    public override int GetHashCode()
    {
        return Combine(base.GetHashCode(),
            EqualityComparer<T2>.Default.GetHashCode(P2));
    }
}

class R3 : R2, IEquatable<R3>
{
    public T3 P3 { get; set; }
    protected override Type EqualityContract => typeof(R3);
    public override bool Equals(object? obj) => Equals(obj as R3);
    public sealed override bool Equals(R2? other) => Equals((object?)other);
    public virtual bool Equals(R3? other)
    {
        return base.Equals((R2?)other) &&
            EqualityComparer<T3>.Default.Equals(P3, other.P3);
    }
    public override int GetHashCode()
    {
        return Combine(base.GetHashCode(),
            EqualityComparer<T3>.Default.GetHashCode(P3));
    }
}
```

### <a name="copy-and-clone-members"></a><span data-ttu-id="554bc-142">Copiare e clonare i membri</span><span class="sxs-lookup"><span data-stu-id="554bc-142">Copy and Clone members</span></span>

<span data-ttu-id="554bc-143">Un tipo di record contiene due membri di copia:</span><span class="sxs-lookup"><span data-stu-id="554bc-143">A record type contains two copying members:</span></span>

* <span data-ttu-id="554bc-144">Costruttore che accetta un solo argomento del tipo di record.</span><span class="sxs-lookup"><span data-stu-id="554bc-144">A constructor taking a single argument of the record type.</span></span> <span data-ttu-id="554bc-145">Viene definito "costruttore di copia".</span><span class="sxs-lookup"><span data-stu-id="554bc-145">It is referred to as a "copy constructor".</span></span>
* <span data-ttu-id="554bc-146">Metodo "clone" dell'istanza virtuale senza parametri pubblico sintetizzato con un nome riservato dal compilatore</span><span class="sxs-lookup"><span data-stu-id="554bc-146">A synthesized public parameterless virtual instance "clone" method with a compiler-reserved name</span></span>

<span data-ttu-id="554bc-147">Lo scopo del costruttore di copia è copiare lo stato dal parametro alla nuova istanza creata.</span><span class="sxs-lookup"><span data-stu-id="554bc-147">The purpose of the copy constructor is to copy the state from the parameter to the new instance being created.</span></span> <span data-ttu-id="554bc-148">Questo costruttore non esegue gli inizializzatori di proprietà o campi di istanza presenti nella dichiarazione di record.</span><span class="sxs-lookup"><span data-stu-id="554bc-148">This constructor doesn't run any instance field/property initializers present in the record declaration.</span></span> <span data-ttu-id="554bc-149">Se il costruttore non è dichiarato in modo esplicito, un costruttore protetto verrà sintetizzato dal compilatore.</span><span class="sxs-lookup"><span data-stu-id="554bc-149">If the constructor is not explicitly declared, a protected constructor will be synthesized by the compiler.</span></span>
<span data-ttu-id="554bc-150">Per prima cosa, il costruttore deve eseguire la chiamata a un costruttore di copia della base o a un costruttore di oggetti senza parametri se il record eredita dall'oggetto.</span><span class="sxs-lookup"><span data-stu-id="554bc-150">The first thing the constructor must do, is to call a copy constructor of the base, or a parameter-less object constructor if the record inherits from object.</span></span> <span data-ttu-id="554bc-151">Se un costruttore di copia definito dall'utente utilizza un inizializzatore di costruttore implicito o esplicito che non soddisfa questo requisito, viene restituito un errore.</span><span class="sxs-lookup"><span data-stu-id="554bc-151">An error is reported if a user-defined copy constructor uses an implicit or explicit constructor initializer that doesn't fulfill this requirement.</span></span>
<span data-ttu-id="554bc-152">Dopo che un costruttore di copia di base viene richiamato, un costruttore di copia sintetizzato copia i valori per tutti i campi di istanza dichiarati in modo implicito o esplicito nel tipo di record.</span><span class="sxs-lookup"><span data-stu-id="554bc-152">After a base copy constructor is invoked, a synthesized copy constructor copies values for all instance fields implicitly or explicitly declared within the record type.</span></span>

<span data-ttu-id="554bc-153">Il metodo "clone" restituisce il risultato di una chiamata a un costruttore con la stessa firma del costruttore di copia.</span><span class="sxs-lookup"><span data-stu-id="554bc-153">The "clone" method returns the result of a call to a constructor with the same signature as the copy constructor.</span></span> <span data-ttu-id="554bc-154">Il tipo restituito del metodo Clone è il tipo che lo contiene, a meno che non sia presente un metodo clone virtuale nella classe di base.</span><span class="sxs-lookup"><span data-stu-id="554bc-154">The return type of the clone method is the containing type, unless a virtual clone method is present in the base class.</span></span> <span data-ttu-id="554bc-155">In tal caso, il tipo restituito è il tipo contenitore corrente se la funzionalità "revariante Returns" è supportata e il tipo restituito di override in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="554bc-155">In that case, the return type is the current containing type if the "covariant returns" feature is supported and the override return type otherwise.</span></span> <span data-ttu-id="554bc-156">Il metodo Clone sintetizzato è un override del metodo clone del tipo di base se ne esiste uno.</span><span class="sxs-lookup"><span data-stu-id="554bc-156">The synthesized clone method is an override of the base type clone method if one exists.</span></span> <span data-ttu-id="554bc-157">Se il metodo clone del tipo di base è sealed, viene generato un errore.</span><span class="sxs-lookup"><span data-stu-id="554bc-157">An error is produced if the base type clone method is sealed.</span></span>

<span data-ttu-id="554bc-158">Se il record contenitore è astratto, anche il metodo Clone sintetizzato è astratto.</span><span class="sxs-lookup"><span data-stu-id="554bc-158">If the containing record is abstract, the synthesized clone method is also abstract.</span></span>

## <a name="positional-record-members"></a><span data-ttu-id="554bc-159">Membri record posizionali</span><span class="sxs-lookup"><span data-stu-id="554bc-159">Positional record members</span></span>

<span data-ttu-id="554bc-160">Oltre ai membri precedenti, i record con un elenco di parametri ("record posizionali") sintetizzano membri aggiuntivi con le stesse condizioni dei membri precedenti.</span><span class="sxs-lookup"><span data-stu-id="554bc-160">In addition to the above members, records with a parameter list ("positional records") synthesize additional members with the same conditions as the members above.</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="554bc-161">Costruttore primario</span><span class="sxs-lookup"><span data-stu-id="554bc-161">Primary Constructor</span></span>

<span data-ttu-id="554bc-162">Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo.</span><span class="sxs-lookup"><span data-stu-id="554bc-162">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="554bc-163">Questo metodo è denominato costruttore primario per il tipo e determina l'eliminazione del costruttore della classe predefinito dichiarato in modo implicito, se presente.</span><span class="sxs-lookup"><span data-stu-id="554bc-163">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="554bc-164">È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.</span><span class="sxs-lookup"><span data-stu-id="554bc-164">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="554bc-165">In fase di esecuzione il costruttore primario</span><span class="sxs-lookup"><span data-stu-id="554bc-165">At runtime the primary constructor</span></span>

1. <span data-ttu-id="554bc-166">esegue gli inizializzatori di istanza visualizzati nel corpo della classe</span><span class="sxs-lookup"><span data-stu-id="554bc-166">executes the instance initializers appearing in the class-body</span></span>

1. <span data-ttu-id="554bc-167">richiama il costruttore della classe base con gli argomenti specificati nella `record_base` clausola, se presente</span><span class="sxs-lookup"><span data-stu-id="554bc-167">invokes the base class constructor with the arguments provided in the `record_base` clause, if present</span></span>

<span data-ttu-id="554bc-168">Se un record ha un costruttore primario, qualsiasi costruttore definito dall'utente, ad eccezione del costruttore di copia, deve disporre di un `this` inizializzatore di costruttore esplicito.</span><span class="sxs-lookup"><span data-stu-id="554bc-168">If a record has a primary constructor, any user-defined constructor, except "copy constructor" must have an explicit `this` constructor initializer.</span></span> 

<span data-ttu-id="554bc-169">I parametri del costruttore primario e i membri del record si trovano nell'ambito all'interno della `argument_list` clausola della `record_base` clausola e negli inizializzatori dei campi o delle proprietà dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="554bc-169">Parameters of the primary constructor as well as members of the record are in scope within the `argument_list` of the `record_base` clause and within initializers of instance fields or properties.</span></span> <span data-ttu-id="554bc-170">I membri di istanza sarebbero un errore in queste posizioni (analogamente a come i membri dell'istanza sono inclusi nell'ambito negli inizializzatori di costruttori normali, ma un errore da usare), ma i parametri del costruttore primario sarebbero inclusi nell'ambito e utilizzabili e verrebbero nascosti ai membri.</span><span class="sxs-lookup"><span data-stu-id="554bc-170">Instance members would be an error in these locations (similar to how instance members are in scope in regular constructor initializers today, but an error to use), but the parameters of the primary constructor would be in scope and useable and would shadow members.</span></span> <span data-ttu-id="554bc-171">I membri statici sarebbero anche utilizzabili, in modo analogo al funzionamento delle chiamate e degli inizializzatori di base nei costruttori normali.</span><span class="sxs-lookup"><span data-stu-id="554bc-171">Static members would also be useable, similar to how base calls and initializers work in ordinary constructors today.</span></span> 

<span data-ttu-id="554bc-172">Le variabili di espressione dichiarate nell'oggetto sono incluse nell' `argument_list` ambito di `argument_list` .</span><span class="sxs-lookup"><span data-stu-id="554bc-172">Expression variables declared in the `argument_list` are in scope within the `argument_list`.</span></span> <span data-ttu-id="554bc-173">Si applicano le stesse regole di shadowing in un elenco di argomenti di un inizializzatore di costruttore normale.</span><span class="sxs-lookup"><span data-stu-id="554bc-173">The same shadowing rules as within an argument list of a regular constructor initializer apply.</span></span>

### <a name="properties"></a><span data-ttu-id="554bc-174">Proprietà</span><span class="sxs-lookup"><span data-stu-id="554bc-174">Properties</span></span>

<span data-ttu-id="554bc-175">Per ogni parametro di record di una dichiarazione del tipo di record è presente un membro della proprietà pubblica corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value.</span><span class="sxs-lookup"><span data-stu-id="554bc-175">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span>

<span data-ttu-id="554bc-176">Per un record:</span><span class="sxs-lookup"><span data-stu-id="554bc-176">For a record:</span></span>

* <span data-ttu-id="554bc-177">`get` `init` Viene creata una proprietà pubblica e automatica (vedere Specifica della `init` funzione di accesso separata).</span><span class="sxs-lookup"><span data-stu-id="554bc-177">A public `get` and `init` auto-property is created (see separate `init` accessor specification).</span></span>
  <span data-ttu-id="554bc-178">Ogni funzione di accesso astratta ereditata "corrispondente" viene sottoposta a override.</span><span class="sxs-lookup"><span data-stu-id="554bc-178">Each "matching" inherited abstract accessor is overridden.</span></span> <span data-ttu-id="554bc-179">La proprietà auto viene inizializzata sul valore del parametro del costruttore primario corrispondente.</span><span class="sxs-lookup"><span data-stu-id="554bc-179">The auto-property is initialized to the value of the corresponding primary constructor parameter.</span></span>

### <a name="deconstruct"></a><span data-ttu-id="554bc-180">Decostruire</span><span class="sxs-lookup"><span data-stu-id="554bc-180">Deconstruct</span></span>

<span data-ttu-id="554bc-181">Un record posizionale sintetizza un metodo pubblico che restituisce un valore void denominato Deconstruct con una dichiarazione di parametro out per ogni parametro della dichiarazione del costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="554bc-181">A positional record synthesizes a public void-returning method called Deconstruct with an out parameter declaration for each parameter of the primary constructor declaration.</span></span> <span data-ttu-id="554bc-182">Ogni parametro del metodo Deconstruct è dello stesso tipo del parametro corrispondente della dichiarazione del costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="554bc-182">Each parameter of the Deconstruct method has the same type as the corresponding parameter of the primary constructor declaration.</span></span> <span data-ttu-id="554bc-183">Il corpo del metodo assegna a un membro con lo stesso nome ogni parametro del metodo di decostruzione al valore di un membro di istanza.</span><span class="sxs-lookup"><span data-stu-id="554bc-183">The body of the method assigns each parameter of the Deconstruct method to the value from an instance member access to a member of the same name.</span></span>

## <a name="with-expression"></a><span data-ttu-id="554bc-184">Espressione `with`</span><span class="sxs-lookup"><span data-stu-id="554bc-184">`with` expression</span></span>

<span data-ttu-id="554bc-185">Un' `with` espressione è una nuova espressione che usa la sintassi seguente.</span><span class="sxs-lookup"><span data-stu-id="554bc-185">A `with` expression is a new expression using the following syntax.</span></span>

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

<span data-ttu-id="554bc-186">Un' `with` espressione consente la "mutazione non distruttiva", progettata per produrre una copia dell'espressione Receiver con modifiche nelle assegnazioni in `member_initializer_list` .</span><span class="sxs-lookup"><span data-stu-id="554bc-186">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications in assignments in the `member_initializer_list`.</span></span>

<span data-ttu-id="554bc-187">Un' `with` espressione valida ha un ricevitore con un tipo non void.</span><span class="sxs-lookup"><span data-stu-id="554bc-187">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="554bc-188">Il tipo di ricevitore deve contenere un metodo "clone" del record sintetizzato accessibile.</span><span class="sxs-lookup"><span data-stu-id="554bc-188">The receiver type must contain an accessible synthesized record "clone" method.</span></span>

<span data-ttu-id="554bc-189">Sul lato destro dell' `with` espressione è `member_initializer_list` presente una con una sequenza di assegnazioni all' *identificatore*, che deve essere un campo di istanza accessibile o una proprietà del tipo restituito del `Clone()` metodo.</span><span class="sxs-lookup"><span data-stu-id="554bc-189">On the right hand side of the `with` expression is a `member_initializer_list` with a sequence of assignments to *identifier*, which must be an accessible instance field or property of the return type of the `Clone()` method.</span></span>

<span data-ttu-id="554bc-190">Ogni `member_initializer` viene elaborato allo stesso modo di un'assegnazione a un campo o a una proprietà per l'accesso al valore restituito del metodo clone del record.</span><span class="sxs-lookup"><span data-stu-id="554bc-190">Each `member_initializer` is processed the same way as an assignment to a field or property access of the return value of the record clone method.</span></span> <span data-ttu-id="554bc-191">Il metodo Clone viene eseguito una sola volta e le assegnazioni vengono elaborate in ordine lessicale.</span><span class="sxs-lookup"><span data-stu-id="554bc-191">The clone method is executed only once and the assignments are processed in lexical order.</span></span>
