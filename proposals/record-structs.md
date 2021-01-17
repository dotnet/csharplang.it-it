---
ms.openlocfilehash: a62f2efa5b0a38a408fc4aaba2860be22f5d161b
ms.sourcegitcommit: a9b70c6ee1117df36eb66cf5b8e45c47e6c4f12e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98536273"
---
# <a name="record-structs"></a><span data-ttu-id="3dfc2-101">Struct di record</span><span class="sxs-lookup"><span data-stu-id="3dfc2-101">Record structs</span></span>

<span data-ttu-id="3dfc2-102">La sintassi per uno struct di record è la seguente:</span><span class="sxs-lookup"><span data-stu-id="3dfc2-102">The syntax for a record struct is as follows:</span></span>

```antlr
record_struct_declaration
    : attributes? struct_modifier* 'partial'? 'record' 'struct' identifier type_parameter_list?
      parameter_list? struct_interfaces? type_parameter_constraints_clause* record_struct_body
    ;

record_struct_body
    : struct_body
    | ';'
    ;
```

<span data-ttu-id="3dfc2-103">I tipi di struct di record sono tipi di valore, come altri tipi struct.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-103">Record struct types are value types, like other struct types.</span></span> <span data-ttu-id="3dfc2-104">Ereditano in modo implicito dalla classe `System.ValueType` .</span><span class="sxs-lookup"><span data-stu-id="3dfc2-104">They implicitly inherit from the class `System.ValueType`.</span></span>
<span data-ttu-id="3dfc2-105">I modificatori e i membri di uno struct di record sono soggetti alle stesse restrizioni di struct (accessibilità sul tipo, modificatori su membri, costruttori di istanza senza parametri, `base(...)` inizializzatori di costruttori di istanza, assegnazione definita per `this` nel costruttore, distruttori,...).</span><span class="sxs-lookup"><span data-stu-id="3dfc2-105">The modifiers and members of a record struct are subject to the same restrictions as those of structs (accessibility on type, modifiers on members, parameterless instance constructors, `base(...)` instance constructor initializers, definite assignment for `this` in constructor, destructors, ...).</span></span>

<span data-ttu-id="3dfc2-106">Vedere https://github.com/dotnet/csharplang/blob/master/spec/structs.md</span><span class="sxs-lookup"><span data-stu-id="3dfc2-106">See https://github.com/dotnet/csharplang/blob/master/spec/structs.md</span></span>

<span data-ttu-id="3dfc2-107">Tuttavia, le dichiarazioni dei campi dell'istanza per uno struct di record possono includere inizializzatori di variabile quando esiste un costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-107">But instance field declarations for a record struct are permitted to include variable initializers when there is a primary constructor.</span></span>

<span data-ttu-id="3dfc2-108">Gli struct di record non possono usare il `ref` modificatore.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-108">Record structs cannot use `ref` modifier.</span></span>

<span data-ttu-id="3dfc2-109">Al massimo una dichiarazione di tipo parziale di uno struct di record parziale può fornire un `parameter_list` .</span><span class="sxs-lookup"><span data-stu-id="3dfc2-109">At most one partial type declaration of a partial record struct may provide a `parameter_list`.</span></span>
<span data-ttu-id="3dfc2-110">Il `parameter_list` non può essere vuoto.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-110">The `parameter_list` may not be empty.</span></span>

<span data-ttu-id="3dfc2-111">I parametri della struct di record non possono usare i `ref` `out` `this` modificatori o (ma `in` `params` sono consentiti e).</span><span class="sxs-lookup"><span data-stu-id="3dfc2-111">Record struct parameters cannot use `ref`, `out` or `this` modifiers (but `in` and `params` are allowed).</span></span>

## <a name="members-of-a-record-struct"></a><span data-ttu-id="3dfc2-112">Membri di uno struct di record</span><span class="sxs-lookup"><span data-stu-id="3dfc2-112">Members of a record struct</span></span>

<span data-ttu-id="3dfc2-113">Oltre ai membri dichiarati nel corpo dello struct di record, un tipo di struct di record ha membri sintetizzati aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-113">In addition to the members declared in the record struct body, a record struct type has additional synthesized members.</span></span>
<span data-ttu-id="3dfc2-114">I membri vengono sintetizzati, a meno che un membro con una firma "corrispondente" non venga dichiarato nel corpo della struct di record o un membro non virtuale concreto accessibile con una firma "corrispondente" venga ereditato.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-114">Members are synthesized unless a member with a "matching" signature is declared in the record struct body or an accessible concrete non-virtual member with a "matching" signature is inherited.</span></span>
<span data-ttu-id="3dfc2-115">Due membri vengono considerati corrispondenti se hanno la stessa firma o verrebbero considerati "nascosti" in uno scenario di ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-115">Two members are considered matching if they have the same signature or would be considered "hiding" in an inheritance scenario.</span></span>
<span data-ttu-id="3dfc2-116">Vedere https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#signatures-and-overloading</span><span class="sxs-lookup"><span data-stu-id="3dfc2-116">See https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#signatures-and-overloading</span></span>

<span data-ttu-id="3dfc2-117">Per un membro di uno struct di record il nome "clone" è un errore.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-117">It is an error for a member of a record struct to be named "Clone".</span></span>

<span data-ttu-id="3dfc2-118">Non è possibile specificare un tipo unsafe per un campo di istanza di uno struct di record.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-118">It is an error for an instance field of a record struct to have an unsafe type.</span></span>

<span data-ttu-id="3dfc2-119">Uno struct di record non è autorizzato a dichiarare un distruttore.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-119">A record struct is not permitted to declare a destructor.</span></span>

<span data-ttu-id="3dfc2-120">I membri sintetizzati sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="3dfc2-120">The synthesized members are as follows:</span></span>

### <a name="equality-members"></a><span data-ttu-id="3dfc2-121">Membri di uguaglianza</span><span class="sxs-lookup"><span data-stu-id="3dfc2-121">Equality members</span></span>

<span data-ttu-id="3dfc2-122">I membri di uguaglianza sintetizzati sono simili a quelli di una classe di record ( `Equals` per questo tipo, `Equals` per il `object` tipo `==` e per `!=` gli operatori di questo tipo), </span><span class="sxs-lookup"><span data-stu-id="3dfc2-122">The synthesized equality members are similar as in a record class (`Equals` for this type, `Equals` for `object` type, `==` and `!=` operators for this type),</span></span>\
<span data-ttu-id="3dfc2-123">eccetto la mancanza di `EqualityContract` , i controlli null o l'ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-123">except for the lack of `EqualityContract`, null checks or inheritance.</span></span>

<span data-ttu-id="3dfc2-124">Lo struct di record implementa `System.IEquatable<R>` e include un overload fortemente tipizzato sintetizzato di `Equals(R other)` dove `R` è lo struct di record.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-124">The record struct implements `System.IEquatable<R>` and includes a synthesized strongly-typed overload of `Equals(R other)` where `R` is the record struct.</span></span>
<span data-ttu-id="3dfc2-125">Il metodo è `public` .</span><span class="sxs-lookup"><span data-stu-id="3dfc2-125">The method is `public`.</span></span>
<span data-ttu-id="3dfc2-126">Il metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-126">The method can be declared explicitly.</span></span> <span data-ttu-id="3dfc2-127">Se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista, è un errore.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-127">It is an error if the explicit declaration does not match the expected signature or accessibility.</span></span>

<span data-ttu-id="3dfc2-128">Se `Equals(R other)` è definito dall'utente (non sintetizzato) ma `GetHashCode` non è, viene generato un avviso.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-128">If `Equals(R other)` is user-defined (not synthesized) but `GetHashCode` is not, a warning is produced.</span></span>

```C#
public bool Equals(R other);
```

<span data-ttu-id="3dfc2-129">L'oggetto sintetizzato `Equals(R)` restituisce `true` se e solo se per ogni campo di istanza `fieldN` nello struct di record è il valore di `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` dove `TN` è il tipo di campo è `true` .</span><span class="sxs-lookup"><span data-stu-id="3dfc2-129">The synthesized `Equals(R)` returns `true` if and only if for each instance field `fieldN` in the record struct the value of `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` where `TN` is the field type is `true`.</span></span>

<span data-ttu-id="3dfc2-130">Lo struct di record include `==` gli operatori e sintetizzati `!=` equivalenti agli operatori dichiarati come segue:</span><span class="sxs-lookup"><span data-stu-id="3dfc2-130">The record struct includes synthesized `==` and `!=` operators equivalent to operators declared as follows:</span></span>
```C#
public static bool operator==(R r1, R r2)
    => r1.Equals(r2);
public static bool operator!=(R r1, R r2)
    => !(r1 == r2);
```
<span data-ttu-id="3dfc2-131">Il `Equals` metodo chiamato dall' `==` operatore è il `Equals(R other)` metodo specificato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-131">The `Equals` method called by the `==` operator is the `Equals(R other)` method specified above.</span></span> <span data-ttu-id="3dfc2-132">L' `!=` operatore delega all' `==` operatore.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-132">The `!=` operator delegates to the `==` operator.</span></span> <span data-ttu-id="3dfc2-133">Se gli operatori sono dichiarati in modo esplicito, si tratta di un errore.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-133">It is an error if the operators are declared explicitly.</span></span>

<span data-ttu-id="3dfc2-134">Lo struct di record include un override sintetizzato equivalente a un metodo dichiarato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="3dfc2-134">The record struct includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
public override bool Equals(object? obj);
```
<span data-ttu-id="3dfc2-135">Se la sostituzione viene dichiarata in modo esplicito, si tratta di un errore.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-135">It is an error if the override is declared explicitly.</span></span> <span data-ttu-id="3dfc2-136">L'override sintetizzato restituisce `other is R temp && Equals(temp)` dove `R` è lo struct di record.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-136">The synthesized override returns `other is R temp && Equals(temp)` where `R` is the record struct.</span></span>

<span data-ttu-id="3dfc2-137">Lo struct di record include un override sintetizzato equivalente a un metodo dichiarato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="3dfc2-137">The record struct includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
public override int GetHashCode();
```
<span data-ttu-id="3dfc2-138">Il metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-138">The method can be declared explicitly.</span></span>

<span data-ttu-id="3dfc2-139">Viene segnalato un avviso se uno di `Equals(R)` e `GetHashCode()` viene dichiarato in modo esplicito, ma l'altro metodo non è esplicito.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-139">A warning is reported if one of `Equals(R)` and `GetHashCode()` is explicitly declared but the other method is not explicit.</span></span>

<span data-ttu-id="3dfc2-140">L'override sintetizzato di `GetHashCode()` restituisce il `int` risultato di una funzione deterministica che combina i valori di `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` per ogni campo di istanza `fieldN` con `TN` il tipo di `fieldN` .</span><span class="sxs-lookup"><span data-stu-id="3dfc2-140">The synthesized override of `GetHashCode()` returns an `int` result of a deterministic function combining the values of `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` for each instance field `fieldN` with `TN` being the type of `fieldN`.</span></span>

<span data-ttu-id="3dfc2-141">Si consideri ad esempio il seguente struct di record:</span><span class="sxs-lookup"><span data-stu-id="3dfc2-141">For example, consider the following record struct:</span></span>
```C#
record struct R1(T1 P1, T2 P2);
```

<span data-ttu-id="3dfc2-142">Per questo struct di record, i membri di uguaglianza sintetizzati sono simili ai seguenti:</span><span class="sxs-lookup"><span data-stu-id="3dfc2-142">For this record struct, the synthesized equality members would be something like:</span></span>
```C#
struct R1 : IEquatable<R1>
{
    public T1 P1 { get; set; }
    public T2 P2 { get; set; }
    public override bool Equals(object? obj) => obj is R1 temp && Equals(temp);
    public bool Equals(R1 other)
    {
        return
            EqualityComparer<T1>.Default.Equals(P1, other.P1) &&
            EqualityComparer<T2>.Default.Equals(P2, other.P2);
    }
    public static bool operator==(R1 r1, R1 r2)
        => r1.Equals(r2);
    public static bool operator!=(R1 r1, R1 r2)
        => !(r1 == r2);    
    public override int GetHashCode()
    {
        return Combine(
            EqualityComparer<T1>.Default.GetHashCode(P1),
            EqualityComparer<T2>.Default.GetHashCode(P2));
    }
}
```

### <a name="printing-members-printmembers-and-tostring-methods"></a><span data-ttu-id="3dfc2-143">Membri di stampa: Metodi PrintMembers e ToString</span><span class="sxs-lookup"><span data-stu-id="3dfc2-143">Printing members: PrintMembers and ToString methods</span></span>

<span data-ttu-id="3dfc2-144">Lo struct di record include un metodo sintetizzato equivalente a un metodo dichiarato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="3dfc2-144">The record struct includes a synthesized method equivalent to a method declared as follows:</span></span>
```C#
private bool PrintMembers(System.Text.StringBuilder builder);
```

<span data-ttu-id="3dfc2-145">Il metodo esegue le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="3dfc2-145">The method does the following:</span></span>
1. <span data-ttu-id="3dfc2-146">per ogni membro stampabile dello struct di record (campo pubblico non statico e membri di proprietà leggibili), aggiunge il nome del membro seguito da "=" seguito dal valore del membro separato con ",",</span><span class="sxs-lookup"><span data-stu-id="3dfc2-146">for each of the record struct's printable members (non-static public field and readable property members), appends that member's name followed by " = " followed by the member's value separated with ", ",</span></span>
2. <span data-ttu-id="3dfc2-147">Restituisce true se lo struct di record dispone di membri stampabili.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-147">return true if the record struct has printable members.</span></span>

<span data-ttu-id="3dfc2-148">Per un membro con un tipo di valore, il relativo valore viene convertito in una rappresentazione di stringa utilizzando il metodo più efficiente disponibile per la piattaforma di destinazione.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-148">For a member that has a value type, we will convert its value to a string representation using the most efficient method available to the target platform.</span></span> <span data-ttu-id="3dfc2-149">Al momento, significa chiamare `ToString` prima di passare a `StringBuilder.Append` .</span><span class="sxs-lookup"><span data-stu-id="3dfc2-149">At present that means calling `ToString` before passing to `StringBuilder.Append`.</span></span>

<span data-ttu-id="3dfc2-150">Il `PrintMembers` metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-150">The `PrintMembers` method can be declared explicitly.</span></span>
<span data-ttu-id="3dfc2-151">Se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista, è un errore.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-151">It is an error if the explicit declaration does not match the expected signature or accessibility.</span></span>

<span data-ttu-id="3dfc2-152">Lo struct di record include un metodo sintetizzato equivalente a un metodo dichiarato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="3dfc2-152">The record struct includes a synthesized method equivalent to a method declared as follows:</span></span>
```C#
public override string ToString();
```

<span data-ttu-id="3dfc2-153">Il metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-153">The method can be declared explicitly.</span></span> <span data-ttu-id="3dfc2-154">Se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista, è un errore.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-154">It is an error if the explicit declaration does not match the expected signature or accessibility.</span></span>

<span data-ttu-id="3dfc2-155">Il metodo sintetizzato:</span><span class="sxs-lookup"><span data-stu-id="3dfc2-155">The synthesized method:</span></span>
1. <span data-ttu-id="3dfc2-156">Crea un' `StringBuilder` istanza di,</span><span class="sxs-lookup"><span data-stu-id="3dfc2-156">creates a `StringBuilder` instance,</span></span>
2. <span data-ttu-id="3dfc2-157">Accoda il nome dello struct di record al generatore, seguito da "{",</span><span class="sxs-lookup"><span data-stu-id="3dfc2-157">appends the record struct name to the builder, followed by " { ",</span></span>
3. <span data-ttu-id="3dfc2-158">richiama il metodo dello struct di record `PrintMembers` che lo assegna al generatore, seguito da "" se restituisce true.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-158">invokes the record struct's `PrintMembers` method giving it the builder, followed by " " if it returned true,</span></span>
4. <span data-ttu-id="3dfc2-159">Accoda "}",</span><span class="sxs-lookup"><span data-stu-id="3dfc2-159">appends "}",</span></span>
5. <span data-ttu-id="3dfc2-160">Restituisce il contenuto del generatore con `builder.ToString()` .</span><span class="sxs-lookup"><span data-stu-id="3dfc2-160">returns the builder's contents with `builder.ToString()`.</span></span>

<span data-ttu-id="3dfc2-161">Si consideri ad esempio il seguente struct di record:</span><span class="sxs-lookup"><span data-stu-id="3dfc2-161">For example, consider the following record struct:</span></span>

``` csharp
record struct R1(T1 P1, T2 P2);
```

<span data-ttu-id="3dfc2-162">Per questo struct di record, i membri di stampa sintetizzati sono simili ai seguenti:</span><span class="sxs-lookup"><span data-stu-id="3dfc2-162">For this record struct, the synthesized printing members would be something like:</span></span>

```C#
struct R1 : IEquatable<R1>
{
    public T1 P1 { get; set; }
    public T2 P2 { get; set; }

    private bool PrintMembers(StringBuilder builder)
    {
        builder.Append(nameof(P1));
        builder.Append(" = ");
        builder.Append(this.P1); // or builder.Append(this.P1.ToString()); if P1 has a value type
        builder.Append(", ");

        builder.Append(nameof(P2));
        builder.Append(" = ");
        builder.Append(this.P2); // or builder.Append(this.P2.ToString()); if P2 has a value type

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
```

## <a name="positional-record-struct-members"></a><span data-ttu-id="3dfc2-163">Membri struct dei record posizionali</span><span class="sxs-lookup"><span data-stu-id="3dfc2-163">Positional record struct members</span></span>

<span data-ttu-id="3dfc2-164">Oltre ai membri precedenti, gli struct di record con un elenco di parametri ("record posizionali") sintetizzano membri aggiuntivi con le stesse condizioni dei membri precedenti.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-164">In addition to the above members, record structs with a parameter list ("positional records") synthesize additional members with the same conditions as the members above.</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="3dfc2-165">Costruttore primario</span><span class="sxs-lookup"><span data-stu-id="3dfc2-165">Primary Constructor</span></span>

<span data-ttu-id="3dfc2-166">Uno struct di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-166">A record struct has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="3dfc2-167">Questo metodo è denominato costruttore primario per il tipo.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-167">This is called the primary constructor for the type.</span></span> <span data-ttu-id="3dfc2-168">È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nello struct.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-168">It is an error to have a primary constructor and a constructor with the same signature already present in the struct.</span></span>
<span data-ttu-id="3dfc2-169">Uno struct di record non è autorizzato a dichiarare un costruttore primario senza parametri.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-169">A record struct is not permitted to declare a parameterless primary constructor.</span></span>

<span data-ttu-id="3dfc2-170">Le dichiarazioni dei campi dell'istanza per uno struct di record possono includere inizializzatori di variabile quando è presente un costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-170">Instance field declarations for a record struct are permitted to include variable initializers when there is a primary constructor.</span></span> <span data-ttu-id="3dfc2-171">In fase di esecuzione il costruttore primario esegue gli inizializzatori di istanza visualizzati nel corpo della struct di record.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-171">At runtime the primary constructor executes the instance initializers appearing in the record-struct-body.</span></span>

<span data-ttu-id="3dfc2-172">Se uno struct di record ha un costruttore primario, qualsiasi costruttore definito dall'utente, ad eccezione del costruttore di copia, deve disporre di un `this` inizializzatore di costruttore esplicito.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-172">If a record struct has a primary constructor, any user-defined constructor, except "copy constructor" must have an explicit `this` constructor initializer.</span></span>

<span data-ttu-id="3dfc2-173">I parametri del costruttore primario e i membri dello struct di record sono inclusi nell'ambito all'interno degli inizializzatori dei campi o delle proprietà dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-173">Parameters of the primary constructor as well as members of the record struct are in scope within initializers of instance fields or properties.</span></span> <span data-ttu-id="3dfc2-174">I membri di istanza sarebbero un errore in queste posizioni (analogamente a come i membri dell'istanza sono inclusi nell'ambito negli inizializzatori di costruttori normali, ma un errore da usare), ma i parametri del costruttore primario sarebbero inclusi nell'ambito e utilizzabili e verrebbero nascosti ai membri.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-174">Instance members would be an error in these locations (similar to how instance members are in scope in regular constructor initializers today, but an error to use), but the parameters of the primary constructor would be in scope and useable and would shadow members.</span></span> <span data-ttu-id="3dfc2-175">I membri statici sarebbero anche utilizzabili.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-175">Static members would also be useable.</span></span>

<span data-ttu-id="3dfc2-176">Se non viene letto un parametro del costruttore primario, viene generato un avviso.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-176">A warning is produced if a parameter of the primary constructor is not read.</span></span>

<span data-ttu-id="3dfc2-177">Le regole assigment definite per i costruttori di istanze struct si applicano al costruttore primario di struct di record.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-177">The definite assigment rules for struct instance constructors apply to the primary constructor of record structs.</span></span> <span data-ttu-id="3dfc2-178">Ad esempio, di seguito è riportato un errore:</span><span class="sxs-lookup"><span data-stu-id="3dfc2-178">For instance, the following is an error:</span></span>

```csharp
record struct Pos(int X) // definite assignment error in primary constructor
{
    private int x;
    public int X { get { return x; } set { x = value; } } = X;
}
```

### <a name="properties"></a><span data-ttu-id="3dfc2-179">Properties</span><span class="sxs-lookup"><span data-stu-id="3dfc2-179">Properties</span></span>

<span data-ttu-id="3dfc2-180">Per ogni parametro dello struct di record di una dichiarazione di struct di record è presente un membro della proprietà pubblica corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-180">For each record struct parameter of a record struct declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span>

<span data-ttu-id="3dfc2-181">Per uno struct di record:</span><span class="sxs-lookup"><span data-stu-id="3dfc2-181">For a record struct:</span></span>

* <span data-ttu-id="3dfc2-182">`get` `init` Viene creata una proprietà pubblica e automatica se lo struct di record ha un `readonly` modificatore `get` e `set` in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-182">A public `get` and `init` auto-property is created if the record struct has `readonly` modifier, `get` and `set` otherwise.</span></span>
  <span data-ttu-id="3dfc2-183">Entrambi i tipi di funzioni di accesso set ( `set` e `init` ) sono considerati "corrispondenti".</span><span class="sxs-lookup"><span data-stu-id="3dfc2-183">Both kinds of set accessors (`set` and `init`) are considered "matching".</span></span> <span data-ttu-id="3dfc2-184">Quindi, l'utente può dichiarare una proprietà solo init al posto di un oggetto modificabile sintetizzato.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-184">So the user may declare an init-only property in place of a synthesized mutable one.</span></span>
  <span data-ttu-id="3dfc2-185">Viene eseguito l'override di una proprietà ereditata `abstract` con tipo corrispondente.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-185">An inherited `abstract` property with matching type is overridden.</span></span>
  <span data-ttu-id="3dfc2-186">Si tratta di un errore se la proprietà ereditata non dispone `public` `get` di `set` / `init` funzioni di accesso e.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-186">It is an error if the inherited property does not have `public` `get` and `set`/`init` accessors.</span></span>
  <span data-ttu-id="3dfc2-187">La proprietà auto viene inizializzata sul valore del parametro del costruttore primario corrispondente.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-187">The auto-property is initialized to the value of the corresponding primary constructor parameter.</span></span>
  <span data-ttu-id="3dfc2-188">Gli attributi possono essere applicati alla proprietà automatica sintetizzata e al relativo campo sottostante usando `property:` `field:` le destinazioni o per gli attributi applicati in modo sintattico al parametro corrispondente dello struct di record.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-188">Attributes can be applied to the synthesized auto-property and its backing field by using `property:` or `field:` targets for attributes syntactically applied to the corresponding record struct parameter.</span></span>  

### <a name="deconstruct"></a><span data-ttu-id="3dfc2-189">Decostruire</span><span class="sxs-lookup"><span data-stu-id="3dfc2-189">Deconstruct</span></span>

<span data-ttu-id="3dfc2-190">Uno struct di record posizionali con almeno un parametro sintetizza un metodo di istanza pubblico che restituisce void denominato `Deconstruct` con una dichiarazione di parametro out per ogni parametro della dichiarazione del costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-190">A positional record struct with at least one parameter synthesizes a public void-returning instance method called `Deconstruct` with an out parameter declaration for each parameter of the primary constructor declaration.</span></span> <span data-ttu-id="3dfc2-191">Ogni parametro del metodo Deconstruct è dello stesso tipo del parametro corrispondente della dichiarazione del costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-191">Each parameter of the Deconstruct method has the same type as the corresponding parameter of the primary constructor declaration.</span></span> <span data-ttu-id="3dfc2-192">Il corpo del metodo assegna a un membro con lo stesso nome ogni parametro del metodo di decostruzione al valore di un membro di istanza.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-192">The body of the method assigns each parameter of the Deconstruct method to the value from an instance member access to a member of the same name.</span></span>
<span data-ttu-id="3dfc2-193">Il metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-193">The method can be declared explicitly.</span></span> <span data-ttu-id="3dfc2-194">Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure è statica.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-194">It is an error if the explicit declaration does not match the expected signature or accessibility, or is static.</span></span>

# <a name="allow-with-expression-on-structs"></a><span data-ttu-id="3dfc2-195">Consenti `with` espressione sugli struct</span><span class="sxs-lookup"><span data-stu-id="3dfc2-195">Allow `with` expression on structs</span></span>

<span data-ttu-id="3dfc2-196">È ora valido affinché il ricevitore in un' `with` espressione disponga di un tipo struct.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-196">It is now valid for the receiver in a `with` expression to have a struct type.</span></span>

<span data-ttu-id="3dfc2-197">Sul lato destro dell' `with` espressione è `member_initializer_list` presente una con una sequenza di assegnazioni all' *identificatore*, che deve essere un campo di istanza accessibile o una proprietà del tipo del destinatario.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-197">On the right hand side of the `with` expression is a `member_initializer_list` with a sequence of assignments to *identifier*, which must be an accessible instance field or property of the receiver's type.</span></span>

<span data-ttu-id="3dfc2-198">Per un ricevitore con tipo struct, il ricevitore viene prima copiato, quindi ogni `member_initializer` viene elaborato allo stesso modo di un'assegnazione a un campo o a una proprietà per l'accesso al risultato della conversione.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-198">For a receiver with struct type, the receiver is first copied, then each `member_initializer` is processed the same way as an assignment to a field or property access of the result of the conversion.</span></span> <span data-ttu-id="3dfc2-199">Le assegnazioni vengono elaborate in ordine lessicale.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-199">Assignments are processed in lexical order.</span></span>

# <a name="improvements-on-records"></a><span data-ttu-id="3dfc2-200">Miglioramenti ai record</span><span class="sxs-lookup"><span data-stu-id="3dfc2-200">Improvements on records</span></span>

## <a name="allow-record-class"></a><span data-ttu-id="3dfc2-201">Consentire `record class`</span><span class="sxs-lookup"><span data-stu-id="3dfc2-201">Allow `record class`</span></span>

<span data-ttu-id="3dfc2-202">La sintassi esistente per i tipi di record consente di usare lo `record class` stesso significato di `record` :</span><span class="sxs-lookup"><span data-stu-id="3dfc2-202">The existing syntax for record types allows `record class` with the same meaning as `record`:</span></span>

```antlr
record_declaration
    : attributes? class_modifier* 'partial'? 'record' 'class'? identifier type_parameter_list?
      parameter_list? record_base? type_parameter_constraints_clause* record_body
    ;
```

## <a name="allow-user-defined-positional-members-to-be-fields"></a><span data-ttu-id="3dfc2-203">Consenti i membri posizionali definiti dall'utente come campi</span><span class="sxs-lookup"><span data-stu-id="3dfc2-203">Allow user-defined positional members to be fields</span></span>

<span data-ttu-id="3dfc2-204">Vedere https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-10-05.md#changing-the-member-type-of-a-primary-constructor-parameter</span><span class="sxs-lookup"><span data-stu-id="3dfc2-204">See https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-10-05.md#changing-the-member-type-of-a-primary-constructor-parameter</span></span>

<span data-ttu-id="3dfc2-205">C'è un problema di compatibilità con la compatibilità, quindi è possibile eliminare questa funzionalità oppure è necessario implementarla rapidamente, come correzione di bug.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-205">There is a back compat issue, so we may either drop this feature or we need to implement it fast (as a bug fix).</span></span>

```csharp
public record Base
{
    public int Field;
}
public record Derived(int Field);
```

# <a name="open-questions"></a><span data-ttu-id="3dfc2-206">Domande aperte</span><span class="sxs-lookup"><span data-stu-id="3dfc2-206">Open questions</span></span>

- <span data-ttu-id="3dfc2-207">è consigliabile non consentire un costruttore definito dall'utente con una firma del costruttore di copia?</span><span class="sxs-lookup"><span data-stu-id="3dfc2-207">should we disallow a user-defined constructor with a copy constructor signature?</span></span>
- <span data-ttu-id="3dfc2-208">confermare che si vuole lasciare la progettazione PrintMembers (metodo separato che restituisce `bool` )</span><span class="sxs-lookup"><span data-stu-id="3dfc2-208">confirm that we want to keep PrintMembers design (separate method returning `bool`)</span></span>
- <span data-ttu-id="3dfc2-209">confermare che si desidera impedire i membri denominati "clone".</span><span class="sxs-lookup"><span data-stu-id="3dfc2-209">confirm that we want to disallow members named "Clone".</span></span>
- <span data-ttu-id="3dfc2-210">perché non sono stati consentiti tipi unsafe nei campi di istanza nei record?</span><span class="sxs-lookup"><span data-stu-id="3dfc2-210">why did we disallow unsafe types in instance fields in records?</span></span> <span data-ttu-id="3dfc2-211">Si supponga di voler impedire anche negli struct di record.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-211">I assume we also want to disallow in record structs.</span></span>
- <span data-ttu-id="3dfc2-212">`with` sui generics?</span><span class="sxs-lookup"><span data-stu-id="3dfc2-212">`with` on generics?</span></span> <span data-ttu-id="3dfc2-213">(può influire sulla progettazione per gli struct di record)</span><span class="sxs-lookup"><span data-stu-id="3dfc2-213">(may affect the design for record structs)</span></span>
- <span data-ttu-id="3dfc2-214">confermare che non è consentito `record ref struct` (emettere con i `IEquatable<RefStruct>` campi di riferimento e)</span><span class="sxs-lookup"><span data-stu-id="3dfc2-214">confirm we won't allow `record ref struct` (issue with `IEquatable<RefStruct>` and ref fields)</span></span>
- <span data-ttu-id="3dfc2-215">confermare l'implementazione dei membri di uguaglianza.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-215">confirm implementation of equality members.</span></span> <span data-ttu-id="3dfc2-216">In alternativa `bool Equals(R other)` , gli operatori vengono sintetizzati `bool Equals(object? other)` e tutti semplicemente delegati a `ValueType.Equals` .</span><span class="sxs-lookup"><span data-stu-id="3dfc2-216">Alternative is that synthesized `bool Equals(R other)`, `bool Equals(object? other)` and operators all just delegate to `ValueType.Equals`.</span></span>
- <span data-ttu-id="3dfc2-217">confermare che si desidera consentire gli inizializzatori di campo quando è presente un costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="3dfc2-217">confirm that we want to allow field initializers when there is a primary constructor.</span></span> <span data-ttu-id="3dfc2-218">Si vuole anche consentire i costruttori di struct senza parametri mentre ci troviamo (il problema dell'attivatore era apparentemente fisso)?</span><span class="sxs-lookup"><span data-stu-id="3dfc2-218">Do we also want to allow parameterless struct constructors while we're at it (the Activator issue was apparently fixed)?</span></span>
- <span data-ttu-id="3dfc2-219">quanto si vuole dire sul `Combine` Metodo?</span><span class="sxs-lookup"><span data-stu-id="3dfc2-219">how much do we want to say about `Combine` method?</span></span>

