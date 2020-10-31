---
ms.openlocfilehash: 3605661e9c17143c8df5b9b994a1f67800db006f
ms.sourcegitcommit: 7125a8428a23fadaf4c93b2fc1ec88019876eaa7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2020
ms.locfileid: "93142158"
---

# <a name="records"></a><span data-ttu-id="fcd33-101">Record</span><span class="sxs-lookup"><span data-stu-id="fcd33-101">Records</span></span>

<span data-ttu-id="fcd33-102">Questa proposta tiene traccia delle specifiche per la funzionalità record di C# 9, come stabilito dal team di progettazione del linguaggio C#.</span><span class="sxs-lookup"><span data-stu-id="fcd33-102">This proposal tracks the specification for the C# 9 records feature, as agreed to by the C# language design team.</span></span>

<span data-ttu-id="fcd33-103">La sintassi per un record è la seguente:</span><span class="sxs-lookup"><span data-stu-id="fcd33-103">The syntax for a record is as follows:</span></span>

```antlr
record_declaration
    : attributes? class_modifier* 'partial'? 'record' identifier type_parameter_list?
      parameter_list? record_base? type_parameter_constraints_clause* record_body
    ;

record_base
    : ':' class_type argument_list?
    | ':' interface_type_list
    | ':' class_type argument_list? ',' interface_type_list
    ;

record_body
    : '{' class_member_declaration* '}' ';'?
    | ';'
    ;
```

<span data-ttu-id="fcd33-104">I tipi di record sono tipi di riferimento, simili a una dichiarazione di classe.</span><span class="sxs-lookup"><span data-stu-id="fcd33-104">Record types are reference types, similar to a class declaration.</span></span> <span data-ttu-id="fcd33-105">È un errore che un record fornisca un oggetto `record_base` `argument_list` se `record_declaration` non contiene un oggetto `parameter_list` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-105">It is an error for a record to provide a `record_base` `argument_list` if the `record_declaration` does not contain a `parameter_list`.</span></span>
<span data-ttu-id="fcd33-106">Al massimo una dichiarazione di tipo parziale di un record parziale può fornire un `parameter_list` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-106">At most one partial type declaration of a partial record may provide a `parameter_list`.</span></span>

<span data-ttu-id="fcd33-107">I parametri di record non possono usare i `ref` `out` `this` modificatori o (ma `in` `params` sono consentiti e).</span><span class="sxs-lookup"><span data-stu-id="fcd33-107">Record parameters cannot use `ref`, `out` or `this` modifiers (but `in` and `params` are allowed).</span></span>

## <a name="inheritance"></a><span data-ttu-id="fcd33-108">Ereditarietà</span><span class="sxs-lookup"><span data-stu-id="fcd33-108">Inheritance</span></span>

<span data-ttu-id="fcd33-109">I record non possono ereditare dalle classi, a meno che la classe non sia `object` e le classi non possano ereditare dai record.</span><span class="sxs-lookup"><span data-stu-id="fcd33-109">Records cannot inherit from classes, unless the class is `object`, and classes cannot inherit from records.</span></span> <span data-ttu-id="fcd33-110">I record possono ereditare da altri record.</span><span class="sxs-lookup"><span data-stu-id="fcd33-110">Records can inherit from other records.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="fcd33-111">Membri di un tipo di record</span><span class="sxs-lookup"><span data-stu-id="fcd33-111">Members of a record type</span></span>

<span data-ttu-id="fcd33-112">Oltre ai membri dichiarati nel corpo del record, un tipo di record ha membri sintetizzati aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="fcd33-112">In addition to the members declared in the record body, a record type has additional synthesized members.</span></span>
<span data-ttu-id="fcd33-113">I membri vengono sintetizzati a meno che un membro con una firma "corrispondente" non venga dichiarato nel corpo del record o un membro non virtuale concreto accessibile con una firma "corrispondente" venga ereditato.</span><span class="sxs-lookup"><span data-stu-id="fcd33-113">Members are synthesized unless a member with a "matching" signature is declared in the record body or an accessible concrete non-virtual member with a "matching" signature is inherited.</span></span>
<span data-ttu-id="fcd33-114">Due membri vengono considerati corrispondenti se hanno la stessa firma o verrebbero considerati "nascosti" in uno scenario di ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="fcd33-114">Two members are considered matching if they have the same signature or would be considered "hiding" in an inheritance scenario.</span></span>
<span data-ttu-id="fcd33-115">Un membro di un record denominato "clone" non è un errore.</span><span class="sxs-lookup"><span data-stu-id="fcd33-115">It is an error for a member of a record to be named "Clone".</span></span>
<span data-ttu-id="fcd33-116">Non è possibile specificare un tipo unsafe per un campo di istanza di un record.</span><span class="sxs-lookup"><span data-stu-id="fcd33-116">It is an error for an instance field of a record to have an unsafe type.</span></span>

<span data-ttu-id="fcd33-117">I membri sintetizzati sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="fcd33-117">The synthesized members are as follows:</span></span>

### <a name="equality-members"></a><span data-ttu-id="fcd33-118">Membri di uguaglianza</span><span class="sxs-lookup"><span data-stu-id="fcd33-118">Equality members</span></span>

<span data-ttu-id="fcd33-119">Se il record è derivato da `object` , il tipo di record include una proprietà di sola lettura sintetizzata equivalente a una proprietà dichiarata come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="fcd33-119">If the record is derived from `object`, the record type includes a synthesized readonly property equivalent to a property declared as follows:</span></span>
```C#
Type EqualityContract { get; };
```
<span data-ttu-id="fcd33-120">La proprietà è `private` se il tipo di record è `sealed` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-120">The property is `private` if the record type is `sealed`.</span></span> <span data-ttu-id="fcd33-121">In caso contrario, la proprietà è `virtual` e `protected` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-121">Otherwise, the property is `virtual` and `protected`.</span></span>
<span data-ttu-id="fcd33-122">La proprietà può essere dichiarata in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="fcd33-122">The property can be declared explicitly.</span></span> <span data-ttu-id="fcd33-123">Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure se la dichiarazione esplicita non consente l'override in un tipo derivato e il tipo di record non lo è `sealed` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-123">It is an error if the explicit declaration does not match the expected signature or accessibility, or if the explicit declaration doesn't allow overriding it in a derived type and the record type is not `sealed`.</span></span>

<span data-ttu-id="fcd33-124">Se il tipo di record è derivato da un tipo di record di base `Base` , il tipo di record include una proprietà di sola lettura sintetizzata equivalente a una proprietà dichiarata come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="fcd33-124">If the record type is derived from a base record type `Base`, the record type includes a synthesized readonly property equivalent to a property declared as follows:</span></span>
```C#
protected override Type EqualityContract { get; };
```

<span data-ttu-id="fcd33-125">La proprietà può essere dichiarata in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="fcd33-125">The property can be declared explicitly.</span></span> <span data-ttu-id="fcd33-126">Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure se la dichiarazione esplicita non consente l'override in un tipo derivato e il tipo di record non lo è `sealed` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-126">It is an error if the explicit declaration does not match the expected signature or accessibility, or if the explicit declaration doesn't allow overriding it in a derived type and the record type is not `sealed`.</span></span> <span data-ttu-id="fcd33-127">Si verifica un errore se la proprietà sintetizzata o dichiarata in modo esplicito non esegue l'override di una proprietà con questa firma nel tipo di record, `Base` ad esempio se la proprietà non è presente in `Base` , o sealed o not Virtual e così via.</span><span class="sxs-lookup"><span data-stu-id="fcd33-127">It is an error if either synthesized, or explicitly declared property doesn't override a property with this signature in the record type `Base` (for example, if the property is missing in the `Base`, or sealed, or not virtual, etc.).</span></span>
<span data-ttu-id="fcd33-128">La proprietà sintetizzata restituisce `typeof(R)` dove `R` è il tipo di record.</span><span class="sxs-lookup"><span data-stu-id="fcd33-128">The synthesized property returns `typeof(R)` where `R` is the record type.</span></span>

<span data-ttu-id="fcd33-129">Il tipo di record implementa `System.IEquatable<R>` e include un overload fortemente tipizzato sintetizzato di `Equals(R? other)` dove `R` è il tipo di record.</span><span class="sxs-lookup"><span data-stu-id="fcd33-129">The record type implements `System.IEquatable<R>` and includes a synthesized strongly-typed overload of `Equals(R? other)` where `R` is the record type.</span></span>
<span data-ttu-id="fcd33-130">Il metodo è `public` e il metodo è `virtual` a meno che il tipo di record non sia `sealed` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-130">The method is `public`, and the method is `virtual` unless the record type is `sealed`.</span></span>
<span data-ttu-id="fcd33-131">Il metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="fcd33-131">The method can be declared explicitly.</span></span> <span data-ttu-id="fcd33-132">Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure la dichiarazione esplicita non consente l'override in un tipo derivato e il tipo di record non lo è `sealed` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-132">It is an error if the explicit declaration does not match the expected signature or accessibility, or the explicit declaration doesn't allow overriding it in a derived type and the record type is not `sealed`.</span></span>

<span data-ttu-id="fcd33-133">Se `Equals(R? other)` è definito dall'utente (non sintetizzato) ma `GetHashCode` non è, viene generato un avviso.</span><span class="sxs-lookup"><span data-stu-id="fcd33-133">If `Equals(R? other)` is user-defined (not synthesized) but `GetHashCode` is not, a warning is produced.</span></span>

```C#
public virtual bool Equals(R? other);
```
<span data-ttu-id="fcd33-134">Il risultato della sintesi `Equals(R?)` restituisce `true` se e solo se ciascuno dei seguenti è `true` :</span><span class="sxs-lookup"><span data-stu-id="fcd33-134">The synthesized `Equals(R?)` returns `true` if and only if each of the following are `true`:</span></span>
- <span data-ttu-id="fcd33-135">`other` non è `null` , e</span><span class="sxs-lookup"><span data-stu-id="fcd33-135">`other` is not `null`, and</span></span>
- <span data-ttu-id="fcd33-136">Per ogni campo di istanza `fieldN` nel tipo di record non ereditato, il valore di `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` dove `TN` è il tipo di campo e</span><span class="sxs-lookup"><span data-stu-id="fcd33-136">For each instance field `fieldN` in the record type that is not inherited, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="fcd33-137">Se è presente un tipo di record di base, il valore di `base.Equals(other)` (una chiamata non virtuale a `public virtual bool Equals(Base? other)` ); in caso contrario, il valore di `EqualityContract == other.EqualityContract` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-137">If there is a base record type, the value of `base.Equals(other)` (a non-virtual call to `public virtual bool Equals(Base? other)`); otherwise the value of `EqualityContract == other.EqualityContract`.</span></span>

<span data-ttu-id="fcd33-138">Il tipo di record include gli `==` operatori e sintetizzati `!=` equivalenti agli operatori dichiarati come segue:</span><span class="sxs-lookup"><span data-stu-id="fcd33-138">The record type includes synthesized `==` and `!=` operators equivalent to operators declared as follows:</span></span>
```C#
public static bool operator==(R? r1, R? r2)
    => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
public static bool operator!=(R? r1, R? r2)
    => !(r1 == r2);
```
<span data-ttu-id="fcd33-139">Il `Equals` metodo chiamato dall' `==` operatore è il `Equals(R? other)` metodo specificato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="fcd33-139">The `Equals` method called by the `==` operator is the `Equals(R? other)` method specified above.</span></span> <span data-ttu-id="fcd33-140">L' `!=` operatore delega all' `==` operatore.</span><span class="sxs-lookup"><span data-stu-id="fcd33-140">The `!=` operator delegates to the `==` operator.</span></span> <span data-ttu-id="fcd33-141">Se gli operatori sono dichiarati in modo esplicito, si tratta di un errore.</span><span class="sxs-lookup"><span data-stu-id="fcd33-141">It is an error if the operators are declared explicitly.</span></span>
    
<span data-ttu-id="fcd33-142">Se il tipo di record è derivato da un tipo di record di base `Base` , il tipo di record include un override sintetizzato equivalente a un metodo dichiarato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="fcd33-142">If the record type is derived from a base record type `Base`, the record type includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
public sealed override bool Equals(Base? other);
```
<span data-ttu-id="fcd33-143">Se la sostituzione viene dichiarata in modo esplicito, si tratta di un errore.</span><span class="sxs-lookup"><span data-stu-id="fcd33-143">It is an error if the override is declared explicitly.</span></span> <span data-ttu-id="fcd33-144">Si tratta di un errore se il metodo non esegue l'override di un metodo con la stessa firma nel tipo di record `Base` , ad esempio se il metodo non è presente in `Base` , oppure sealed o non virtuale e così via.</span><span class="sxs-lookup"><span data-stu-id="fcd33-144">It is an error if the method doesn't override a method with same signature in record type `Base` (for example, if the method is missing in the `Base`, or sealed, or not virtual, etc.).</span></span>
<span data-ttu-id="fcd33-145">L'override sintetizzato restituisce `Equals((object?)other)` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-145">The synthesized override returns `Equals((object?)other)`.</span></span>

<span data-ttu-id="fcd33-146">Il tipo di record include un override sintetizzato equivalente a un metodo dichiarato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="fcd33-146">The record type includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
public override bool Equals(object? obj);
```
<span data-ttu-id="fcd33-147">Se la sostituzione viene dichiarata in modo esplicito, si tratta di un errore.</span><span class="sxs-lookup"><span data-stu-id="fcd33-147">It is an error if the override is declared explicitly.</span></span> <span data-ttu-id="fcd33-148">Si tratta di un errore se il metodo non esegue l'override `object.Equals(object? obj)` , ad esempio a causa dello shadowing nei tipi di base intermedi e così via.</span><span class="sxs-lookup"><span data-stu-id="fcd33-148">It is an error if the method doesn't override `object.Equals(object? obj)` (for example, due to shadowing in intermediate base types, etc.).</span></span>
<span data-ttu-id="fcd33-149">L'override sintetizzato restituisce `Equals(other as R)` dove `R` è il tipo di record.</span><span class="sxs-lookup"><span data-stu-id="fcd33-149">The synthesized override returns `Equals(other as R)` where `R` is the record type.</span></span>

<span data-ttu-id="fcd33-150">Il tipo di record include un override sintetizzato equivalente a un metodo dichiarato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="fcd33-150">The record type includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
public override int GetHashCode();
```
<span data-ttu-id="fcd33-151">Il metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="fcd33-151">The method can be declared explicitly.</span></span>
<span data-ttu-id="fcd33-152">Si tratta di un errore se la dichiarazione esplicita non consente l'override in un tipo derivato e il tipo di record non lo è `sealed` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-152">It is an error if the explicit declaration doesn't allow overriding it in a derived type and the record type is not `sealed`.</span></span> <span data-ttu-id="fcd33-153">Il metodo non viene sottoposta a override, `object.GetHashCode()` ad esempio a causa dello shadowing nei tipi di base intermedi e così via.</span><span class="sxs-lookup"><span data-stu-id="fcd33-153">It is an error if either synthesized, or explicitly declared method doesn't override `object.GetHashCode()` (for example, due to shadowing in intermediate base types, etc.).</span></span>
 
<span data-ttu-id="fcd33-154">Viene segnalato un avviso se uno di `Equals(R?)` e `GetHashCode()` viene dichiarato in modo esplicito, ma l'altro metodo non è esplicito.</span><span class="sxs-lookup"><span data-stu-id="fcd33-154">A warning is reported if one of `Equals(R?)` and `GetHashCode()` is explicitly declared but the other method is not explicit.</span></span>

<span data-ttu-id="fcd33-155">L'override sintetizzato di `GetHashCode()` restituisce `int` il risultato di una funzione deterministica che combina i valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="fcd33-155">The synthesized override of `GetHashCode()` returns an `int` result of a deterministic function combining the following values:</span></span>
- <span data-ttu-id="fcd33-156">Per ogni campo di istanza `fieldN` nel tipo di record non ereditato, il valore di `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` dove `TN` è il tipo di campo e</span><span class="sxs-lookup"><span data-stu-id="fcd33-156">For each instance field `fieldN` in the record type that is not inherited, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="fcd33-157">Se è presente un tipo di record di base, il valore di `base.GetHashCode()` ; in caso contrario, il valore di `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-157">If there is a base record type, the value of `base.GetHashCode()`; otherwise the value of `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)`.</span></span>

<span data-ttu-id="fcd33-158">Si considerino, ad esempio, i seguenti tipi di record:</span><span class="sxs-lookup"><span data-stu-id="fcd33-158">For example, consider the following record types:</span></span>
```C#
record R1(T1 P1);
record R2(T1 P1, T2 P2) : R1(P1);
record R3(T1 P1, T2 P2, T3 P3) : R2(P1, P2);
```

<span data-ttu-id="fcd33-159">Per questi tipi di record, i membri di uguaglianza sintetizzati sono simili ai seguenti:</span><span class="sxs-lookup"><span data-stu-id="fcd33-159">For those record types, the synthesized equality members would be something like:</span></span>
```C#
class R1 : IEquatable<R1>
{
    public T1 P1 { get; init; }
    protected virtual Type EqualityContract => typeof(R1);
    public override bool Equals(object? obj) => Equals(obj as R1);
    public virtual bool Equals(R1? other)
    {
        return !(other is null) &&
            EqualityContract == other.EqualityContract &&
            EqualityComparer<T1>.Default.Equals(P1, other.P1);
    }
    public static bool operator==(R1? r1, R1? r2)
        => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
    public static bool operator!=(R1? r1, R1? r2)
        => !(r1 == r2);    
    public override int GetHashCode()
    {
        return Combine(EqualityComparer<Type>.Default.GetHashCode(EqualityContract),
            EqualityComparer<T1>.Default.GetHashCode(P1));
    }
}

class R2 : R1, IEquatable<R2>
{
    public T2 P2 { get; init; }
    protected override Type EqualityContract => typeof(R2);
    public override bool Equals(object? obj) => Equals(obj as R2);
    public sealed override bool Equals(R1? other) => Equals((object?)other);
    public virtual bool Equals(R2? other)
    {
        return base.Equals((R1?)other) &&
            EqualityComparer<T2>.Default.Equals(P2, other.P2);
    }
    public static bool operator==(R2? r1, R2? r2)
        => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
    public static bool operator!=(R2? r1, R2? r2)
        => !(r1 == r2);    
    public override int GetHashCode()
    {
        return Combine(base.GetHashCode(),
            EqualityComparer<T2>.Default.GetHashCode(P2));
    }
}

class R3 : R2, IEquatable<R3>
{
    public T3 P3 { get; init; }
    protected override Type EqualityContract => typeof(R3);
    public override bool Equals(object? obj) => Equals(obj as R3);
    public sealed override bool Equals(R2? other) => Equals((object?)other);
    public virtual bool Equals(R3? other)
    {
        return base.Equals((R2?)other) &&
            EqualityComparer<T3>.Default.Equals(P3, other.P3);
    }
    public static bool operator==(R3? r1, R3? r2)
        => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
    public static bool operator!=(R3? r1, R3? r2)
        => !(r1 == r2);    
    public override int GetHashCode()
    {
        return Combine(base.GetHashCode(),
            EqualityComparer<T3>.Default.GetHashCode(P3));
    }
}
```

### <a name="copy-and-clone-members"></a><span data-ttu-id="fcd33-160">Copiare e clonare i membri</span><span class="sxs-lookup"><span data-stu-id="fcd33-160">Copy and Clone members</span></span>

<span data-ttu-id="fcd33-161">Un tipo di record contiene due membri di copia:</span><span class="sxs-lookup"><span data-stu-id="fcd33-161">A record type contains two copying members:</span></span>

* <span data-ttu-id="fcd33-162">Costruttore che accetta un solo argomento del tipo di record.</span><span class="sxs-lookup"><span data-stu-id="fcd33-162">A constructor taking a single argument of the record type.</span></span> <span data-ttu-id="fcd33-163">Viene definito "costruttore di copia".</span><span class="sxs-lookup"><span data-stu-id="fcd33-163">It is referred to as a "copy constructor".</span></span>
* <span data-ttu-id="fcd33-164">Metodo "clone" dell'istanza senza parametri pubblico sintetizzato con un nome riservato dal compilatore</span><span class="sxs-lookup"><span data-stu-id="fcd33-164">A synthesized public parameterless instance "clone" method with a compiler-reserved name</span></span>

<span data-ttu-id="fcd33-165">Lo scopo del costruttore di copia è copiare lo stato dal parametro alla nuova istanza creata.</span><span class="sxs-lookup"><span data-stu-id="fcd33-165">The purpose of the copy constructor is to copy the state from the parameter to the new instance being created.</span></span> <span data-ttu-id="fcd33-166">Questo costruttore non esegue gli inizializzatori di proprietà o campi di istanza presenti nella dichiarazione di record.</span><span class="sxs-lookup"><span data-stu-id="fcd33-166">This constructor doesn't run any instance field/property initializers present in the record declaration.</span></span> <span data-ttu-id="fcd33-167">Se il costruttore non è dichiarato in modo esplicito, un costruttore verrà sintetizzato dal compilatore.</span><span class="sxs-lookup"><span data-stu-id="fcd33-167">If the constructor is not explicitly declared, a constructor will be synthesized by the compiler.</span></span> <span data-ttu-id="fcd33-168">Se il record è sealed, il costruttore sarà privato, in caso contrario verrà protetto.</span><span class="sxs-lookup"><span data-stu-id="fcd33-168">If the record is sealed, the constructor will be private, otherwise it will be protected.</span></span>
<span data-ttu-id="fcd33-169">Un costruttore di copia dichiarato in modo esplicito deve essere pubblico o protetto, a meno che il record non sia sealed.</span><span class="sxs-lookup"><span data-stu-id="fcd33-169">An explicitly declared copy constructor must be either public or protected, unless the record is sealed.</span></span>
<span data-ttu-id="fcd33-170">Per prima cosa, il costruttore deve eseguire la chiamata a un costruttore di copia della base o a un costruttore di oggetti senza parametri se il record eredita dall'oggetto.</span><span class="sxs-lookup"><span data-stu-id="fcd33-170">The first thing the constructor must do, is to call a copy constructor of the base, or a parameter-less object constructor if the record inherits from object.</span></span> <span data-ttu-id="fcd33-171">Se un costruttore di copia definito dall'utente utilizza un inizializzatore di costruttore implicito o esplicito che non soddisfa questo requisito, viene restituito un errore.</span><span class="sxs-lookup"><span data-stu-id="fcd33-171">An error is reported if a user-defined copy constructor uses an implicit or explicit constructor initializer that doesn't fulfill this requirement.</span></span>
<span data-ttu-id="fcd33-172">Dopo che un costruttore di copia di base viene richiamato, un costruttore di copia sintetizzato copia i valori per tutti i campi di istanza dichiarati in modo implicito o esplicito nel tipo di record.</span><span class="sxs-lookup"><span data-stu-id="fcd33-172">After a base copy constructor is invoked, a synthesized copy constructor copies values for all instance fields implicitly or explicitly declared within the record type.</span></span> <span data-ttu-id="fcd33-173">L'unica presenza di un costruttore di copia, esplicito o implicito, non impedisce l'aggiunta automatica di un costruttore di istanza predefinito.</span><span class="sxs-lookup"><span data-stu-id="fcd33-173">The sole presence of a copy constructor, whether explicit or implicit, doesn't prevent an automatic addition of a default instance constructor.</span></span>

<span data-ttu-id="fcd33-174">Se nel record di base è presente un metodo "clone" virtuale, il metodo "clone" sintetizzato ne esegue l'override e il tipo restituito del metodo è il tipo che lo contiene corrente se la funzionalità "ritorni covariante" è supportata e il tipo restituito di override in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="fcd33-174">If a virtual "clone" method is present in the base record, the synthesized "clone" method overrides it and the return type of the method is the current containing type if the "covariant returns" feature is supported and the override return type otherwise.</span></span> <span data-ttu-id="fcd33-175">Se il metodo clone del record di base è sealed, viene generato un errore.</span><span class="sxs-lookup"><span data-stu-id="fcd33-175">An error is produced if the base record clone method is sealed.</span></span>
<span data-ttu-id="fcd33-176">Se un metodo "clone" virtuale non è presente nel record di base, il tipo restituito del metodo Clone è il tipo che lo contiene e il metodo è virtuale, a meno che il record non sia sealed o abstract.</span><span class="sxs-lookup"><span data-stu-id="fcd33-176">If a virtual "clone" method is not present in the base record, the return type of the clone method is the containing type and the method is virtual, unless the record is sealed or abstract.</span></span>
<span data-ttu-id="fcd33-177">Se il record contenitore è astratto, anche il metodo Clone sintetizzato è astratto.</span><span class="sxs-lookup"><span data-stu-id="fcd33-177">If the containing record is abstract, the synthesized clone method is also abstract.</span></span>
<span data-ttu-id="fcd33-178">Se il metodo "clone" non è astratto, restituisce il risultato di una chiamata a un costruttore di copia.</span><span class="sxs-lookup"><span data-stu-id="fcd33-178">If the "clone" method is not abstract, it returns the result of a call to a copy constructor.</span></span> 


### <a name="printing-members-printmembers-and-tostring-methods"></a><span data-ttu-id="fcd33-179">Membri di stampa: Metodi PrintMembers e ToString</span><span class="sxs-lookup"><span data-stu-id="fcd33-179">Printing members: PrintMembers and ToString methods</span></span>

<span data-ttu-id="fcd33-180">Se il record è derivato da `object` , il record include un metodo sintetizzato equivalente a un metodo dichiarato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="fcd33-180">If the record is derived from `object`, the record includes a synthesized method equivalent to a method declared as follows:</span></span>
```C#
bool PrintMembers(System.Text.StringBuilder builder);
```
<span data-ttu-id="fcd33-181">Il metodo è `private` se il tipo di record è `sealed` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-181">The method is `private` if the record type is `sealed`.</span></span> <span data-ttu-id="fcd33-182">In caso contrario, il metodo è `virtual` e `protected` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-182">Otherwise, the method is `virtual` and `protected`.</span></span>

<span data-ttu-id="fcd33-183">Il metodo si comporta come di seguito:</span><span class="sxs-lookup"><span data-stu-id="fcd33-183">The method:</span></span>
1. <span data-ttu-id="fcd33-184">per ogni membro stampabile del record (campo pubblico non statico e membri di proprietà leggibili), aggiunge il nome di tale membro seguito da "=" seguito dal valore del membro: `this.member` , separato da ",",</span><span class="sxs-lookup"><span data-stu-id="fcd33-184">for each of the record's printable members (non-static public field and readable property members), appends that member's name followed by " = " followed by the member's value: `this.member`, separated with ", ",</span></span>
2. <span data-ttu-id="fcd33-185">Restituisce true se il record dispone di membri stampabili.</span><span class="sxs-lookup"><span data-stu-id="fcd33-185">return true if the record has printable members.</span></span>

<span data-ttu-id="fcd33-186">Se il tipo di record è derivato da un record di base `Base` , il record include un override sintetizzato equivalente a un metodo dichiarato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="fcd33-186">If the record type is derived from a base record `Base`, the record includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
protected override bool PrintMembers(StringBuilder builder);
```

<span data-ttu-id="fcd33-187">Se il record non dispone di membri stampabili, il metodo chiama il `PrintMembers` metodo di base con un argomento (il relativo `builder` parametro) e restituisce il risultato.</span><span class="sxs-lookup"><span data-stu-id="fcd33-187">If the record has no printable members, the method calls the base `PrintMembers` method with one argument (its `builder` parameter) and returns the result.</span></span>

<span data-ttu-id="fcd33-188">In caso contrario, il metodo:</span><span class="sxs-lookup"><span data-stu-id="fcd33-188">Otherwise, the method:</span></span>
1. <span data-ttu-id="fcd33-189">chiama il `PrintMembers` metodo di base con un argomento (il relativo `builder` parametro),</span><span class="sxs-lookup"><span data-stu-id="fcd33-189">calls the base `PrintMembers` method with one argument (its `builder` parameter),</span></span>
2. <span data-ttu-id="fcd33-190">Se il `PrintMembers` metodo ha restituito true, accodare "," al generatore,</span><span class="sxs-lookup"><span data-stu-id="fcd33-190">if the `PrintMembers` method returned true, append ", " to the builder,</span></span>
3. <span data-ttu-id="fcd33-191">per ogni membro stampabile del record, aggiunge il nome del membro seguito da "=" seguito dal valore del membro: `this.member` (o `this.member.ToString()` per i tipi di valore), separati da ",",</span><span class="sxs-lookup"><span data-stu-id="fcd33-191">for each of the record's printable members, appends that member's name followed by " = " followed by the member's value: `this.member` (or `this.member.ToString()` for value types), separated with ", ",</span></span>
4. <span data-ttu-id="fcd33-192">Restituisce true.</span><span class="sxs-lookup"><span data-stu-id="fcd33-192">return true.</span></span>

<span data-ttu-id="fcd33-193">Il `PrintMembers` metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="fcd33-193">The `PrintMembers` method can be declared explicitly.</span></span>
<span data-ttu-id="fcd33-194">Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure se la dichiarazione esplicita non consente l'override in un tipo derivato e il tipo di record non lo è `sealed` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-194">It is an error if the explicit declaration does not match the expected signature or accessibility, or if the explicit declaration doesn't allow overriding it in a derived type and the record type is not `sealed`.</span></span>

<span data-ttu-id="fcd33-195">Il record include un metodo sintetizzato equivalente a un metodo dichiarato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="fcd33-195">The record includes a synthesized method equivalent to a method declared as follows:</span></span>
```C#
public override string ToString();
```

<span data-ttu-id="fcd33-196">Il metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="fcd33-196">The method can be declared explicitly.</span></span> <span data-ttu-id="fcd33-197">Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure se la dichiarazione esplicita non consente l'override in un tipo derivato e il tipo di record non lo è `sealed` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-197">It is an error if the explicit declaration does not match the expected signature or accessibility, or if the explicit declaration doesn't allow overriding it in a derived type and the record type is not `sealed`.</span></span> <span data-ttu-id="fcd33-198">Il metodo non viene sottoposta a override, `object.ToString()` ad esempio a causa dello shadowing nei tipi di base intermedi e così via.</span><span class="sxs-lookup"><span data-stu-id="fcd33-198">It is an error if either synthesized, or explicitly declared method doesn't override `object.ToString()` (for example, due to shadowing in intermediate base types, etc.).</span></span>

<span data-ttu-id="fcd33-199">Il metodo sintetizzato:</span><span class="sxs-lookup"><span data-stu-id="fcd33-199">The synthesized method:</span></span>
1. <span data-ttu-id="fcd33-200">Crea un' `StringBuilder` istanza di,</span><span class="sxs-lookup"><span data-stu-id="fcd33-200">creates a `StringBuilder` instance,</span></span>
2. <span data-ttu-id="fcd33-201">Accoda il nome del record al generatore, seguito da "{",</span><span class="sxs-lookup"><span data-stu-id="fcd33-201">appends the record name to the builder, followed by " { ",</span></span>
3. <span data-ttu-id="fcd33-202">richiama il metodo del record `PrintMembers` che lo assegna al generatore, seguito da "" se restituisce true.</span><span class="sxs-lookup"><span data-stu-id="fcd33-202">invokes the record's `PrintMembers` method giving it the builder, followed by " " if it returned true,</span></span>
4. <span data-ttu-id="fcd33-203">Accoda "}",</span><span class="sxs-lookup"><span data-stu-id="fcd33-203">appends "}",</span></span>
3. <span data-ttu-id="fcd33-204">Restituisce il contenuto del generatore con `builder.ToString()` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-204">returns the builder's contents with `builder.ToString()`.</span></span>

<span data-ttu-id="fcd33-205">Si considerino, ad esempio, i seguenti tipi di record:</span><span class="sxs-lookup"><span data-stu-id="fcd33-205">For example, consider the following record types:</span></span>

``` csharp
record R1(T1 P1);
record R2(T1 P1, T2 P2, T3 P3) : R1(P1);
```

<span data-ttu-id="fcd33-206">Per questi tipi di record, i membri di stampa sintetizzati sarebbero simili ai seguenti:</span><span class="sxs-lookup"><span data-stu-id="fcd33-206">For those record types, the synthesized printing members would be something like:</span></span>

```C#
class R1 : IEquatable<R1>
{
    public T1 P1 { get; init; }
    
    protected virtual bool PrintMembers(StringBuilder builder)
    {
        builder.Append(nameof(P1));
        builder.Append(" = ");
        builder.Append(this.P1); // or builder.Append(this.P1); if P1 has a value type
        
        return true;
    }
    
    public override string ToString()
    {
        var builder = new StringBuilder();
        builder.Append(nameof(R1));
        builder.Append(" { ");

        if (PrintMembers(builder))
            builder.Append(" ");

        builder.Append("}");
        return builder.ToString();
    }
}

class R2 : R1, IEquatable<R2>
{
    public T2 P2 { get; init; }
    public T3 P3 { get; init; }
    
    protected override void PrintMembers(StringBuilder builder)
    {
        if (base.PrintMembers(builder))
            builder.Append(", ");
            
        builder.Append(nameof(P2));
        builder.Append(" = ");
        builder.Append(this.P2); // or builder.Append(this.P2); if P2 has a value type
        
        builder.Append(", ");
        
        builder.Append(nameof(P3));
        builder.Append(" = ");
        builder.Append(this.P3); // or builder.Append(this.P3); if P3 has a value type
        
        return true;
    }
    
    public override string ToString()
    {
        var builder = new StringBuilder();
        builder.Append(nameof(R2));
        builder.Append(" { ");

        if (PrintMembers(builder))
            builder.Append(" ");

        builder.Append("}");
        return builder.ToString();
    }
}
```

## <a name="positional-record-members"></a><span data-ttu-id="fcd33-207">Membri record posizionali</span><span class="sxs-lookup"><span data-stu-id="fcd33-207">Positional record members</span></span>

<span data-ttu-id="fcd33-208">Oltre ai membri precedenti, i record con un elenco di parametri ("record posizionali") sintetizzano membri aggiuntivi con le stesse condizioni dei membri precedenti.</span><span class="sxs-lookup"><span data-stu-id="fcd33-208">In addition to the above members, records with a parameter list ("positional records") synthesize additional members with the same conditions as the members above.</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="fcd33-209">Costruttore primario</span><span class="sxs-lookup"><span data-stu-id="fcd33-209">Primary Constructor</span></span>

<span data-ttu-id="fcd33-210">Un tipo di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo.</span><span class="sxs-lookup"><span data-stu-id="fcd33-210">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="fcd33-211">Questo metodo è denominato costruttore primario per il tipo e determina l'eliminazione del costruttore della classe predefinito dichiarato in modo implicito, se presente.</span><span class="sxs-lookup"><span data-stu-id="fcd33-211">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="fcd33-212">È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nella classe.</span><span class="sxs-lookup"><span data-stu-id="fcd33-212">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="fcd33-213">In fase di esecuzione il costruttore primario</span><span class="sxs-lookup"><span data-stu-id="fcd33-213">At runtime the primary constructor</span></span>

1. <span data-ttu-id="fcd33-214">esegue gli inizializzatori di istanza visualizzati nel corpo della classe</span><span class="sxs-lookup"><span data-stu-id="fcd33-214">executes the instance initializers appearing in the class-body</span></span>

1. <span data-ttu-id="fcd33-215">richiama il costruttore della classe base con gli argomenti specificati nella `record_base` clausola, se presente</span><span class="sxs-lookup"><span data-stu-id="fcd33-215">invokes the base class constructor with the arguments provided in the `record_base` clause, if present</span></span>

<span data-ttu-id="fcd33-216">Se un record ha un costruttore primario, qualsiasi costruttore definito dall'utente, ad eccezione del costruttore di copia, deve disporre di un `this` inizializzatore di costruttore esplicito.</span><span class="sxs-lookup"><span data-stu-id="fcd33-216">If a record has a primary constructor, any user-defined constructor, except "copy constructor" must have an explicit `this` constructor initializer.</span></span> 

<span data-ttu-id="fcd33-217">I parametri del costruttore primario e i membri del record si trovano nell'ambito all'interno della `argument_list` clausola della `record_base` clausola e negli inizializzatori dei campi o delle proprietà dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="fcd33-217">Parameters of the primary constructor as well as members of the record are in scope within the `argument_list` of the `record_base` clause and within initializers of instance fields or properties.</span></span> <span data-ttu-id="fcd33-218">I membri di istanza sarebbero un errore in queste posizioni (analogamente a come i membri dell'istanza sono inclusi nell'ambito negli inizializzatori di costruttori normali, ma un errore da usare), ma i parametri del costruttore primario sarebbero inclusi nell'ambito e utilizzabili e verrebbero nascosti ai membri.</span><span class="sxs-lookup"><span data-stu-id="fcd33-218">Instance members would be an error in these locations (similar to how instance members are in scope in regular constructor initializers today, but an error to use), but the parameters of the primary constructor would be in scope and useable and would shadow members.</span></span> <span data-ttu-id="fcd33-219">I membri statici sarebbero anche utilizzabili, in modo analogo al funzionamento delle chiamate e degli inizializzatori di base nei costruttori normali.</span><span class="sxs-lookup"><span data-stu-id="fcd33-219">Static members would also be useable, similar to how base calls and initializers work in ordinary constructors today.</span></span>

<span data-ttu-id="fcd33-220">Se non viene letto un parametro del costruttore primario, viene generato un avviso.</span><span class="sxs-lookup"><span data-stu-id="fcd33-220">A warning is produced if a parameter of the primary constructor is not read.</span></span>

<span data-ttu-id="fcd33-221">Le variabili di espressione dichiarate nell'oggetto sono incluse nell' `argument_list` ambito di `argument_list` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-221">Expression variables declared in the `argument_list` are in scope within the `argument_list`.</span></span> <span data-ttu-id="fcd33-222">Si applicano le stesse regole di shadowing in un elenco di argomenti di un inizializzatore di costruttore normale.</span><span class="sxs-lookup"><span data-stu-id="fcd33-222">The same shadowing rules as within an argument list of a regular constructor initializer apply.</span></span>

### <a name="properties"></a><span data-ttu-id="fcd33-223">Proprietà</span><span class="sxs-lookup"><span data-stu-id="fcd33-223">Properties</span></span>

<span data-ttu-id="fcd33-224">Per ogni parametro di record di una dichiarazione del tipo di record è presente un membro della proprietà pubblica corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value.</span><span class="sxs-lookup"><span data-stu-id="fcd33-224">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span>

<span data-ttu-id="fcd33-225">Per un record:</span><span class="sxs-lookup"><span data-stu-id="fcd33-225">For a record:</span></span>

* <span data-ttu-id="fcd33-226">`get` `init` Viene creata una proprietà pubblica e automatica (vedere Specifica della `init` funzione di accesso separata).</span><span class="sxs-lookup"><span data-stu-id="fcd33-226">A public `get` and `init` auto-property is created (see separate `init` accessor specification).</span></span>
  <span data-ttu-id="fcd33-227">Viene eseguito l'override di una proprietà ereditata `abstract` con tipo corrispondente.</span><span class="sxs-lookup"><span data-stu-id="fcd33-227">An inherited `abstract` property with matching type is overridden.</span></span>
  <span data-ttu-id="fcd33-228">Si tratta di un errore se la proprietà ereditata non dispone `public` `get` di funzioni di accesso e sottoponibili a override `init` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-228">It is an error if the inherited property does not have `public` overridable `get` and `init` accessors.</span></span>
  <span data-ttu-id="fcd33-229">La proprietà auto viene inizializzata sul valore del parametro del costruttore primario corrispondente.</span><span class="sxs-lookup"><span data-stu-id="fcd33-229">The auto-property is initialized to the value of the corresponding primary constructor parameter.</span></span>
  <span data-ttu-id="fcd33-230">Gli attributi possono essere applicati alla proprietà automatica sintetizzata e al campo sottostante usando `property:` `field:` le destinazioni o per gli attributi applicati in modo sintattico al parametro record corrispondente.</span><span class="sxs-lookup"><span data-stu-id="fcd33-230">Attributes can be applied to the synthesized auto-property and its backing field by using `property:` or `field:` targets for attributes syntactically applied to the corresponding record parameter.</span></span>  

### <a name="deconstruct"></a><span data-ttu-id="fcd33-231">Decostruire</span><span class="sxs-lookup"><span data-stu-id="fcd33-231">Deconstruct</span></span>

<span data-ttu-id="fcd33-232">Un record posizionale con almeno un parametro sintetizza un metodo di istanza pubblico che restituisce void denominato Deconstruct con una dichiarazione di parametro out per ogni parametro della dichiarazione del costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="fcd33-232">A positional record with at least one parameter synthesizes a public void-returning instance method called Deconstruct with an out parameter declaration for each parameter of the primary constructor declaration.</span></span> <span data-ttu-id="fcd33-233">Ogni parametro del metodo Deconstruct è dello stesso tipo del parametro corrispondente della dichiarazione del costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="fcd33-233">Each parameter of the Deconstruct method has the same type as the corresponding parameter of the primary constructor declaration.</span></span> <span data-ttu-id="fcd33-234">Il corpo del metodo assegna a un membro con lo stesso nome ogni parametro del metodo di decostruzione al valore di un membro di istanza.</span><span class="sxs-lookup"><span data-stu-id="fcd33-234">The body of the method assigns each parameter of the Deconstruct method to the value from an instance member access to a member of the same name.</span></span>
<span data-ttu-id="fcd33-235">Il metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="fcd33-235">The method can be declared explicitly.</span></span> <span data-ttu-id="fcd33-236">Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure è statica.</span><span class="sxs-lookup"><span data-stu-id="fcd33-236">It is an error if the explicit declaration does not match the expected signature or accessibility, or is static.</span></span>

## <a name="with-expression"></a><span data-ttu-id="fcd33-237">Espressione `with`</span><span class="sxs-lookup"><span data-stu-id="fcd33-237">`with` expression</span></span>

<span data-ttu-id="fcd33-238">Un' `with` espressione è una nuova espressione che usa la sintassi seguente.</span><span class="sxs-lookup"><span data-stu-id="fcd33-238">A `with` expression is a new expression using the following syntax.</span></span>

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
<span data-ttu-id="fcd33-239">Un' `with` espressione non è consentita come istruzione.</span><span class="sxs-lookup"><span data-stu-id="fcd33-239">A `with` expression is not permitted as a statement.</span></span>

<span data-ttu-id="fcd33-240">Un' `with` espressione consente la "mutazione non distruttiva", progettata per produrre una copia dell'espressione Receiver con modifiche nelle assegnazioni in `member_initializer_list` .</span><span class="sxs-lookup"><span data-stu-id="fcd33-240">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications in assignments in the `member_initializer_list`.</span></span>

<span data-ttu-id="fcd33-241">Un' `with` espressione valida ha un ricevitore con un tipo non void.</span><span class="sxs-lookup"><span data-stu-id="fcd33-241">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="fcd33-242">Il tipo di ricevitore deve essere un record.</span><span class="sxs-lookup"><span data-stu-id="fcd33-242">The receiver type must be a record.</span></span>

<span data-ttu-id="fcd33-243">Sul lato destro dell' `with` espressione è `member_initializer_list` presente una con una sequenza di assegnazioni all' *identificatore* , che deve essere un campo di istanza accessibile o una proprietà del tipo del destinatario.</span><span class="sxs-lookup"><span data-stu-id="fcd33-243">On the right hand side of the `with` expression is a `member_initializer_list` with a sequence of assignments to *identifier* , which must be an accessible instance field or property of the receiver's type.</span></span>

<span data-ttu-id="fcd33-244">Innanzitutto, viene richiamato il metodo "clone" del destinatario (specificato sopra) e il risultato viene convertito nel tipo del destinatario.</span><span class="sxs-lookup"><span data-stu-id="fcd33-244">First, receiver's "clone" method (specified above) is invoked and its result is converted to the receiver's type.</span></span> <span data-ttu-id="fcd33-245">Ognuna viene quindi `member_initializer` elaborata allo stesso modo di un'assegnazione a un campo o a una proprietà per l'accesso al risultato della conversione.</span><span class="sxs-lookup"><span data-stu-id="fcd33-245">Then, each `member_initializer` is processed the same way as an assignment to a field or property access of the result of the conversion.</span></span> <span data-ttu-id="fcd33-246">Le assegnazioni vengono elaborate in ordine lessicale.</span><span class="sxs-lookup"><span data-stu-id="fcd33-246">Assignments are processed in lexical order.</span></span>
