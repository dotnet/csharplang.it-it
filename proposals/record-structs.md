---
ms.openlocfilehash: 519d546800218ac49ffdeb2d61cb311bbc57091c
ms.sourcegitcommit: 224a2ffa412eb2e7890a6b6e2c39d9ae93776a70
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2021
ms.locfileid: "101103040"
---
# <a name="record-structs"></a><span data-ttu-id="4781f-101">Struct di record</span><span class="sxs-lookup"><span data-stu-id="4781f-101">Record structs</span></span>

<span data-ttu-id="4781f-102">La sintassi per uno struct di record è la seguente:</span><span class="sxs-lookup"><span data-stu-id="4781f-102">The syntax for a record struct is as follows:</span></span>

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

<span data-ttu-id="4781f-103">I tipi di struct di record sono tipi di valore, come altri tipi struct.</span><span class="sxs-lookup"><span data-stu-id="4781f-103">Record struct types are value types, like other struct types.</span></span> <span data-ttu-id="4781f-104">Ereditano in modo implicito dalla classe `System.ValueType` .</span><span class="sxs-lookup"><span data-stu-id="4781f-104">They implicitly inherit from the class `System.ValueType`.</span></span>
<span data-ttu-id="4781f-105">I modificatori e i membri di uno struct di record sono soggetti alle stesse restrizioni di quelle degli struct (accessibilità sul tipo, modificatori sui membri, `base(...)` inizializzatori del costruttore di istanza, assegnazione definita per `this` nel costruttore, distruttori,...). Gli struct di record seguiranno anche le stesse regole degli struct per i costruttori di istanze senza parametri e gli inizializzatori di campo, ma in questo documento si presuppone che le restrizioni per gli struct vengano elevate in generale.</span><span class="sxs-lookup"><span data-stu-id="4781f-105">The modifiers and members of a record struct are subject to the same restrictions as those of structs (accessibility on type, modifiers on members, `base(...)` instance constructor initializers, definite assignment for `this` in constructor, destructors, ...). Record structs will also follow the same rules as structs for parameterless instance constructors and field initializers, but this document assumes that we will lift those restrictions for structs generally.</span></span>

<span data-ttu-id="4781f-106">Vedere https://github.com/dotnet/csharplang/blob/master/spec/structs.md</span><span class="sxs-lookup"><span data-stu-id="4781f-106">See https://github.com/dotnet/csharplang/blob/master/spec/structs.md</span></span>

<span data-ttu-id="4781f-107">Gli struct di record non possono usare il `ref` modificatore.</span><span class="sxs-lookup"><span data-stu-id="4781f-107">Record structs cannot use `ref` modifier.</span></span>

<span data-ttu-id="4781f-108">Al massimo una dichiarazione di tipo parziale di uno struct di record parziale può fornire un `parameter_list` .</span><span class="sxs-lookup"><span data-stu-id="4781f-108">At most one partial type declaration of a partial record struct may provide a `parameter_list`.</span></span>
<span data-ttu-id="4781f-109">Il `parameter_list` non può essere vuoto.</span><span class="sxs-lookup"><span data-stu-id="4781f-109">The `parameter_list` may not be empty.</span></span>

<span data-ttu-id="4781f-110">I parametri della struct di record non possono usare i `ref` `out` `this` modificatori o (ma `in` `params` sono consentiti e).</span><span class="sxs-lookup"><span data-stu-id="4781f-110">Record struct parameters cannot use `ref`, `out` or `this` modifiers (but `in` and `params` are allowed).</span></span>

## <a name="members-of-a-record-struct"></a><span data-ttu-id="4781f-111">Membri di uno struct di record</span><span class="sxs-lookup"><span data-stu-id="4781f-111">Members of a record struct</span></span>

<span data-ttu-id="4781f-112">Oltre ai membri dichiarati nel corpo dello struct di record, un tipo di struct di record ha membri sintetizzati aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="4781f-112">In addition to the members declared in the record struct body, a record struct type has additional synthesized members.</span></span>
<span data-ttu-id="4781f-113">I membri vengono sintetizzati, a meno che un membro con una firma "corrispondente" non venga dichiarato nel corpo della struct di record o un membro non virtuale concreto accessibile con una firma "corrispondente" venga ereditato.</span><span class="sxs-lookup"><span data-stu-id="4781f-113">Members are synthesized unless a member with a "matching" signature is declared in the record struct body or an accessible concrete non-virtual member with a "matching" signature is inherited.</span></span>
<span data-ttu-id="4781f-114">Due membri vengono considerati corrispondenti se hanno la stessa firma o verrebbero considerati "nascosti" in uno scenario di ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="4781f-114">Two members are considered matching if they have the same signature or would be considered "hiding" in an inheritance scenario.</span></span>
<span data-ttu-id="4781f-115">Vedere https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#signatures-and-overloading</span><span class="sxs-lookup"><span data-stu-id="4781f-115">See https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#signatures-and-overloading</span></span>

<span data-ttu-id="4781f-116">Per un membro di uno struct di record il nome "clone" è un errore.</span><span class="sxs-lookup"><span data-stu-id="4781f-116">It is an error for a member of a record struct to be named "Clone".</span></span>

<span data-ttu-id="4781f-117">Non è possibile specificare un tipo unsafe per un campo di istanza di uno struct di record.</span><span class="sxs-lookup"><span data-stu-id="4781f-117">It is an error for an instance field of a record struct to have an unsafe type.</span></span>

<span data-ttu-id="4781f-118">Uno struct di record non è autorizzato a dichiarare un distruttore.</span><span class="sxs-lookup"><span data-stu-id="4781f-118">A record struct is not permitted to declare a destructor.</span></span>

<span data-ttu-id="4781f-119">I membri sintetizzati sono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="4781f-119">The synthesized members are as follows:</span></span>

### <a name="equality-members"></a><span data-ttu-id="4781f-120">Membri di uguaglianza</span><span class="sxs-lookup"><span data-stu-id="4781f-120">Equality members</span></span>

<span data-ttu-id="4781f-121">I membri di uguaglianza sintetizzati sono simili a quelli di una classe di record ( `Equals` per questo tipo, `Equals` per il `object` tipo `==` e per `!=` gli operatori di questo tipo), </span><span class="sxs-lookup"><span data-stu-id="4781f-121">The synthesized equality members are similar as in a record class (`Equals` for this type, `Equals` for `object` type, `==` and `!=` operators for this type),</span></span>\
<span data-ttu-id="4781f-122">eccetto la mancanza di `EqualityContract` , i controlli null o l'ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="4781f-122">except for the lack of `EqualityContract`, null checks or inheritance.</span></span>

<span data-ttu-id="4781f-123">Lo struct di record implementa `System.IEquatable<R>` e include un overload fortemente tipizzato sintetizzato di `Equals(R other)` dove `R` è lo struct di record.</span><span class="sxs-lookup"><span data-stu-id="4781f-123">The record struct implements `System.IEquatable<R>` and includes a synthesized strongly-typed overload of `Equals(R other)` where `R` is the record struct.</span></span>
<span data-ttu-id="4781f-124">Il metodo è `public` .</span><span class="sxs-lookup"><span data-stu-id="4781f-124">The method is `public`.</span></span>
<span data-ttu-id="4781f-125">Il metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="4781f-125">The method can be declared explicitly.</span></span> <span data-ttu-id="4781f-126">Se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista, è un errore.</span><span class="sxs-lookup"><span data-stu-id="4781f-126">It is an error if the explicit declaration does not match the expected signature or accessibility.</span></span>

<span data-ttu-id="4781f-127">Se `Equals(R other)` è definito dall'utente (non sintetizzato) ma `GetHashCode` non è, viene generato un avviso.</span><span class="sxs-lookup"><span data-stu-id="4781f-127">If `Equals(R other)` is user-defined (not synthesized) but `GetHashCode` is not, a warning is produced.</span></span>

```C#
public bool Equals(R other);
```

<span data-ttu-id="4781f-128">L'oggetto sintetizzato `Equals(R)` restituisce `true` se e solo se per ogni campo di istanza `fieldN` nello struct di record è il valore di `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` dove `TN` è il tipo di campo è `true` .</span><span class="sxs-lookup"><span data-stu-id="4781f-128">The synthesized `Equals(R)` returns `true` if and only if for each instance field `fieldN` in the record struct the value of `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` where `TN` is the field type is `true`.</span></span>

<span data-ttu-id="4781f-129">Lo struct di record include `==` gli operatori e sintetizzati `!=` equivalenti agli operatori dichiarati come segue:</span><span class="sxs-lookup"><span data-stu-id="4781f-129">The record struct includes synthesized `==` and `!=` operators equivalent to operators declared as follows:</span></span>
```C#
public static bool operator==(R r1, R r2)
    => r1.Equals(r2);
public static bool operator!=(R r1, R r2)
    => !(r1 == r2);
```
<span data-ttu-id="4781f-130">Il `Equals` metodo chiamato dall' `==` operatore è il `Equals(R other)` metodo specificato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="4781f-130">The `Equals` method called by the `==` operator is the `Equals(R other)` method specified above.</span></span> <span data-ttu-id="4781f-131">L' `!=` operatore delega all' `==` operatore.</span><span class="sxs-lookup"><span data-stu-id="4781f-131">The `!=` operator delegates to the `==` operator.</span></span> <span data-ttu-id="4781f-132">Se gli operatori sono dichiarati in modo esplicito, si tratta di un errore.</span><span class="sxs-lookup"><span data-stu-id="4781f-132">It is an error if the operators are declared explicitly.</span></span>

<span data-ttu-id="4781f-133">Lo struct di record include un override sintetizzato equivalente a un metodo dichiarato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="4781f-133">The record struct includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
public override bool Equals(object? obj);
```
<span data-ttu-id="4781f-134">Se la sostituzione viene dichiarata in modo esplicito, si tratta di un errore.</span><span class="sxs-lookup"><span data-stu-id="4781f-134">It is an error if the override is declared explicitly.</span></span> <span data-ttu-id="4781f-135">L'override sintetizzato restituisce `other is R temp && Equals(temp)` dove `R` è lo struct di record.</span><span class="sxs-lookup"><span data-stu-id="4781f-135">The synthesized override returns `other is R temp && Equals(temp)` where `R` is the record struct.</span></span>

<span data-ttu-id="4781f-136">Lo struct di record include un override sintetizzato equivalente a un metodo dichiarato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="4781f-136">The record struct includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
public override int GetHashCode();
```
<span data-ttu-id="4781f-137">Il metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="4781f-137">The method can be declared explicitly.</span></span>

<span data-ttu-id="4781f-138">Viene segnalato un avviso se uno di `Equals(R)` e `GetHashCode()` viene dichiarato in modo esplicito, ma l'altro metodo non è esplicito.</span><span class="sxs-lookup"><span data-stu-id="4781f-138">A warning is reported if one of `Equals(R)` and `GetHashCode()` is explicitly declared but the other method is not explicit.</span></span>

<span data-ttu-id="4781f-139">L'override sintetizzato di `GetHashCode()` restituisce il `int` risultato della combinazione dei valori di `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` per ogni campo di istanza `fieldN` con `TN` il tipo di `fieldN` .</span><span class="sxs-lookup"><span data-stu-id="4781f-139">The synthesized override of `GetHashCode()` returns an `int` result of combining the values of `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` for each instance field `fieldN` with `TN` being the type of `fieldN`.</span></span>

<span data-ttu-id="4781f-140">Si consideri ad esempio il seguente struct di record:</span><span class="sxs-lookup"><span data-stu-id="4781f-140">For example, consider the following record struct:</span></span>
```C#
record struct R1(T1 P1, T2 P2);
```

<span data-ttu-id="4781f-141">Per questo struct di record, i membri di uguaglianza sintetizzati sono simili ai seguenti:</span><span class="sxs-lookup"><span data-stu-id="4781f-141">For this record struct, the synthesized equality members would be something like:</span></span>
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

### <a name="printing-members-printmembers-and-tostring-methods"></a><span data-ttu-id="4781f-142">Membri di stampa: Metodi PrintMembers e ToString</span><span class="sxs-lookup"><span data-stu-id="4781f-142">Printing members: PrintMembers and ToString methods</span></span>

<span data-ttu-id="4781f-143">Lo struct di record include un metodo sintetizzato equivalente a un metodo dichiarato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="4781f-143">The record struct includes a synthesized method equivalent to a method declared as follows:</span></span>
```C#
private bool PrintMembers(System.Text.StringBuilder builder);
```

<span data-ttu-id="4781f-144">Il metodo esegue le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="4781f-144">The method does the following:</span></span>
1. <span data-ttu-id="4781f-145">per ogni membro stampabile dello struct di record (campo pubblico non statico e membri di proprietà leggibili), aggiunge il nome del membro seguito da "=" seguito dal valore del membro separato con ",",</span><span class="sxs-lookup"><span data-stu-id="4781f-145">for each of the record struct's printable members (non-static public field and readable property members), appends that member's name followed by " = " followed by the member's value separated with ", ",</span></span>
2. <span data-ttu-id="4781f-146">Restituisce true se lo struct di record dispone di membri stampabili.</span><span class="sxs-lookup"><span data-stu-id="4781f-146">return true if the record struct has printable members.</span></span>

<span data-ttu-id="4781f-147">Per un membro con un tipo di valore, il relativo valore viene convertito in una rappresentazione di stringa utilizzando il metodo più efficiente disponibile per la piattaforma di destinazione.</span><span class="sxs-lookup"><span data-stu-id="4781f-147">For a member that has a value type, we will convert its value to a string representation using the most efficient method available to the target platform.</span></span> <span data-ttu-id="4781f-148">Al momento, significa chiamare `ToString` prima di passare a `StringBuilder.Append` .</span><span class="sxs-lookup"><span data-stu-id="4781f-148">At present that means calling `ToString` before passing to `StringBuilder.Append`.</span></span>

<span data-ttu-id="4781f-149">Il `PrintMembers` metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="4781f-149">The `PrintMembers` method can be declared explicitly.</span></span>
<span data-ttu-id="4781f-150">Se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista, è un errore.</span><span class="sxs-lookup"><span data-stu-id="4781f-150">It is an error if the explicit declaration does not match the expected signature or accessibility.</span></span>

<span data-ttu-id="4781f-151">Lo struct di record include un metodo sintetizzato equivalente a un metodo dichiarato nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="4781f-151">The record struct includes a synthesized method equivalent to a method declared as follows:</span></span>
```C#
public override string ToString();
```

<span data-ttu-id="4781f-152">Il metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="4781f-152">The method can be declared explicitly.</span></span> <span data-ttu-id="4781f-153">Se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista, è un errore.</span><span class="sxs-lookup"><span data-stu-id="4781f-153">It is an error if the explicit declaration does not match the expected signature or accessibility.</span></span>

<span data-ttu-id="4781f-154">Il metodo sintetizzato:</span><span class="sxs-lookup"><span data-stu-id="4781f-154">The synthesized method:</span></span>
1. <span data-ttu-id="4781f-155">Crea un' `StringBuilder` istanza di,</span><span class="sxs-lookup"><span data-stu-id="4781f-155">creates a `StringBuilder` instance,</span></span>
2. <span data-ttu-id="4781f-156">Accoda il nome dello struct di record al generatore, seguito da "{",</span><span class="sxs-lookup"><span data-stu-id="4781f-156">appends the record struct name to the builder, followed by " { ",</span></span>
3. <span data-ttu-id="4781f-157">richiama il metodo dello struct di record `PrintMembers` che lo assegna al generatore, seguito da "" se restituisce true.</span><span class="sxs-lookup"><span data-stu-id="4781f-157">invokes the record struct's `PrintMembers` method giving it the builder, followed by " " if it returned true,</span></span>
4. <span data-ttu-id="4781f-158">Accoda "}",</span><span class="sxs-lookup"><span data-stu-id="4781f-158">appends "}",</span></span>
5. <span data-ttu-id="4781f-159">Restituisce il contenuto del generatore con `builder.ToString()` .</span><span class="sxs-lookup"><span data-stu-id="4781f-159">returns the builder's contents with `builder.ToString()`.</span></span>

<span data-ttu-id="4781f-160">Si consideri ad esempio il seguente struct di record:</span><span class="sxs-lookup"><span data-stu-id="4781f-160">For example, consider the following record struct:</span></span>

``` csharp
record struct R1(T1 P1, T2 P2);
```

<span data-ttu-id="4781f-161">Per questo struct di record, i membri di stampa sintetizzati sono simili ai seguenti:</span><span class="sxs-lookup"><span data-stu-id="4781f-161">For this record struct, the synthesized printing members would be something like:</span></span>

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

## <a name="positional-record-struct-members"></a><span data-ttu-id="4781f-162">Membri struct dei record posizionali</span><span class="sxs-lookup"><span data-stu-id="4781f-162">Positional record struct members</span></span>

<span data-ttu-id="4781f-163">Oltre ai membri precedenti, gli struct di record con un elenco di parametri ("record posizionali") sintetizzano membri aggiuntivi con le stesse condizioni dei membri precedenti.</span><span class="sxs-lookup"><span data-stu-id="4781f-163">In addition to the above members, record structs with a parameter list ("positional records") synthesize additional members with the same conditions as the members above.</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="4781f-164">Costruttore primario</span><span class="sxs-lookup"><span data-stu-id="4781f-164">Primary Constructor</span></span>

<span data-ttu-id="4781f-165">Uno struct di record ha un costruttore pubblico la cui firma corrisponde ai parametri del valore della dichiarazione del tipo.</span><span class="sxs-lookup"><span data-stu-id="4781f-165">A record struct has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="4781f-166">Questo metodo è denominato costruttore primario per il tipo.</span><span class="sxs-lookup"><span data-stu-id="4781f-166">This is called the primary constructor for the type.</span></span> <span data-ttu-id="4781f-167">È un errore avere un costruttore primario e un costruttore con la stessa firma già presente nello struct.</span><span class="sxs-lookup"><span data-stu-id="4781f-167">It is an error to have a primary constructor and a constructor with the same signature already present in the struct.</span></span>
<span data-ttu-id="4781f-168">Uno struct di record non è autorizzato a dichiarare un costruttore primario senza parametri.</span><span class="sxs-lookup"><span data-stu-id="4781f-168">A record struct is not permitted to declare a parameterless primary constructor.</span></span>

<span data-ttu-id="4781f-169">Le dichiarazioni dei campi dell'istanza per uno struct di record possono includere inizializzatori di variabile.</span><span class="sxs-lookup"><span data-stu-id="4781f-169">Instance field declarations for a record struct are permitted to include variable initializers.</span></span>
<span data-ttu-id="4781f-170">Se non è presente alcun costruttore primario, gli inizializzatori di istanza vengono eseguiti come parte del costruttore senza parametri.</span><span class="sxs-lookup"><span data-stu-id="4781f-170">If there is no primary constructor, the instance initializers execute as part of the parameterless constructor.</span></span>
<span data-ttu-id="4781f-171">In caso contrario, in fase di esecuzione il costruttore primario esegue gli inizializzatori di istanza visualizzati nel corpo della struttura di record.</span><span class="sxs-lookup"><span data-stu-id="4781f-171">Otherwise, at runtime the primary constructor executes the instance initializers appearing in the record-struct-body.</span></span>

<span data-ttu-id="4781f-172">Se uno struct di record ha un costruttore primario, qualsiasi costruttore definito dall'utente, ad eccezione del costruttore di copia, deve disporre di un `this` inizializzatore di costruttore esplicito.</span><span class="sxs-lookup"><span data-stu-id="4781f-172">If a record struct has a primary constructor, any user-defined constructor, except "copy constructor" must have an explicit `this` constructor initializer.</span></span>

<span data-ttu-id="4781f-173">I parametri del costruttore primario e i membri dello struct di record sono inclusi nell'ambito all'interno degli inizializzatori dei campi o delle proprietà dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="4781f-173">Parameters of the primary constructor as well as members of the record struct are in scope within initializers of instance fields or properties.</span></span> <span data-ttu-id="4781f-174">I membri di istanza sarebbero un errore in queste posizioni (analogamente a come i membri dell'istanza sono inclusi nell'ambito negli inizializzatori di costruttori normali, ma un errore da usare), ma i parametri del costruttore primario sarebbero inclusi nell'ambito e utilizzabili e verrebbero nascosti ai membri.</span><span class="sxs-lookup"><span data-stu-id="4781f-174">Instance members would be an error in these locations (similar to how instance members are in scope in regular constructor initializers today, but an error to use), but the parameters of the primary constructor would be in scope and useable and would shadow members.</span></span> <span data-ttu-id="4781f-175">I membri statici sarebbero anche utilizzabili.</span><span class="sxs-lookup"><span data-stu-id="4781f-175">Static members would also be useable.</span></span>

<span data-ttu-id="4781f-176">Se non viene letto un parametro del costruttore primario, viene generato un avviso.</span><span class="sxs-lookup"><span data-stu-id="4781f-176">A warning is produced if a parameter of the primary constructor is not read.</span></span>

<span data-ttu-id="4781f-177">Le regole assigment definite per i costruttori di istanze struct si applicano al costruttore primario di struct di record.</span><span class="sxs-lookup"><span data-stu-id="4781f-177">The definite assigment rules for struct instance constructors apply to the primary constructor of record structs.</span></span> <span data-ttu-id="4781f-178">Ad esempio, di seguito è riportato un errore:</span><span class="sxs-lookup"><span data-stu-id="4781f-178">For instance, the following is an error:</span></span>

```csharp
record struct Pos(int X) // definite assignment error in primary constructor
{
    private int x;
    public int X { get { return x; } set { x = value; } } = X;
}
```

### <a name="properties"></a><span data-ttu-id="4781f-179">Proprietà</span><span class="sxs-lookup"><span data-stu-id="4781f-179">Properties</span></span>

<span data-ttu-id="4781f-180">Per ogni parametro dello struct di record di una dichiarazione di struct di record è presente un membro della proprietà pubblica corrispondente il cui nome e tipo vengono ricavati dalla dichiarazione del parametro value.</span><span class="sxs-lookup"><span data-stu-id="4781f-180">For each record struct parameter of a record struct declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span>

<span data-ttu-id="4781f-181">Per uno struct di record:</span><span class="sxs-lookup"><span data-stu-id="4781f-181">For a record struct:</span></span>

* <span data-ttu-id="4781f-182">`get` `init` Viene creata una proprietà pubblica e automatica se lo struct di record ha un `readonly` modificatore `get` e `set` in caso contrario.</span><span class="sxs-lookup"><span data-stu-id="4781f-182">A public `get` and `init` auto-property is created if the record struct has `readonly` modifier, `get` and `set` otherwise.</span></span>
  <span data-ttu-id="4781f-183">Entrambi i tipi di funzioni di accesso set ( `set` e `init` ) sono considerati "corrispondenti".</span><span class="sxs-lookup"><span data-stu-id="4781f-183">Both kinds of set accessors (`set` and `init`) are considered "matching".</span></span> <span data-ttu-id="4781f-184">Quindi, l'utente può dichiarare una proprietà solo init al posto di un oggetto modificabile sintetizzato.</span><span class="sxs-lookup"><span data-stu-id="4781f-184">So the user may declare an init-only property in place of a synthesized mutable one.</span></span>
  <span data-ttu-id="4781f-185">Viene eseguito l'override di una proprietà ereditata `abstract` con tipo corrispondente.</span><span class="sxs-lookup"><span data-stu-id="4781f-185">An inherited `abstract` property with matching type is overridden.</span></span>
  <span data-ttu-id="4781f-186">Si tratta di un errore se la proprietà ereditata non dispone `public` `get` di `set` / `init` funzioni di accesso e.</span><span class="sxs-lookup"><span data-stu-id="4781f-186">It is an error if the inherited property does not have `public` `get` and `set`/`init` accessors.</span></span>
  <span data-ttu-id="4781f-187">La proprietà auto viene inizializzata sul valore del parametro del costruttore primario corrispondente.</span><span class="sxs-lookup"><span data-stu-id="4781f-187">The auto-property is initialized to the value of the corresponding primary constructor parameter.</span></span>
  <span data-ttu-id="4781f-188">Gli attributi possono essere applicati alla proprietà automatica sintetizzata e al relativo campo sottostante usando `property:` `field:` le destinazioni o per gli attributi applicati in modo sintattico al parametro corrispondente dello struct di record.</span><span class="sxs-lookup"><span data-stu-id="4781f-188">Attributes can be applied to the synthesized auto-property and its backing field by using `property:` or `field:` targets for attributes syntactically applied to the corresponding record struct parameter.</span></span>  

### <a name="deconstruct"></a><span data-ttu-id="4781f-189">Decostruire</span><span class="sxs-lookup"><span data-stu-id="4781f-189">Deconstruct</span></span>

<span data-ttu-id="4781f-190">Uno struct di record posizionali con almeno un parametro sintetizza un metodo di istanza pubblico che restituisce void denominato `Deconstruct` con una dichiarazione di parametro out per ogni parametro della dichiarazione del costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="4781f-190">A positional record struct with at least one parameter synthesizes a public void-returning instance method called `Deconstruct` with an out parameter declaration for each parameter of the primary constructor declaration.</span></span> <span data-ttu-id="4781f-191">Ogni parametro del metodo Deconstruct è dello stesso tipo del parametro corrispondente della dichiarazione del costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="4781f-191">Each parameter of the Deconstruct method has the same type as the corresponding parameter of the primary constructor declaration.</span></span> <span data-ttu-id="4781f-192">Il corpo del metodo assegna a un membro con lo stesso nome ogni parametro del metodo di decostruzione al valore di un membro di istanza.</span><span class="sxs-lookup"><span data-stu-id="4781f-192">The body of the method assigns each parameter of the Deconstruct method to the value from an instance member access to a member of the same name.</span></span>
<span data-ttu-id="4781f-193">Il metodo può essere dichiarato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="4781f-193">The method can be declared explicitly.</span></span> <span data-ttu-id="4781f-194">Si tratta di un errore se la dichiarazione esplicita non corrisponde alla firma o all'accessibilità prevista oppure è statica.</span><span class="sxs-lookup"><span data-stu-id="4781f-194">It is an error if the explicit declaration does not match the expected signature or accessibility, or is static.</span></span>

# <a name="allow-with-expression-on-structs"></a><span data-ttu-id="4781f-195">Consenti `with` espressione sugli struct</span><span class="sxs-lookup"><span data-stu-id="4781f-195">Allow `with` expression on structs</span></span>

<span data-ttu-id="4781f-196">È ora valido affinché il ricevitore in un' `with` espressione disponga di un tipo struct.</span><span class="sxs-lookup"><span data-stu-id="4781f-196">It is now valid for the receiver in a `with` expression to have a struct type.</span></span>

<span data-ttu-id="4781f-197">Sul lato destro dell' `with` espressione è `member_initializer_list` presente una con una sequenza di assegnazioni all' *identificatore*, che deve essere un campo di istanza accessibile o una proprietà del tipo del destinatario.</span><span class="sxs-lookup"><span data-stu-id="4781f-197">On the right hand side of the `with` expression is a `member_initializer_list` with a sequence of assignments to *identifier*, which must be an accessible instance field or property of the receiver's type.</span></span>

<span data-ttu-id="4781f-198">Per un ricevitore con tipo struct, il ricevitore viene prima copiato, quindi ogni `member_initializer` viene elaborato allo stesso modo di un'assegnazione a un campo o a una proprietà per l'accesso al risultato della conversione.</span><span class="sxs-lookup"><span data-stu-id="4781f-198">For a receiver with struct type, the receiver is first copied, then each `member_initializer` is processed the same way as an assignment to a field or property access of the result of the conversion.</span></span> <span data-ttu-id="4781f-199">Le assegnazioni vengono elaborate in ordine lessicale.</span><span class="sxs-lookup"><span data-stu-id="4781f-199">Assignments are processed in lexical order.</span></span>

# <a name="improvements-on-records"></a><span data-ttu-id="4781f-200">Miglioramenti ai record</span><span class="sxs-lookup"><span data-stu-id="4781f-200">Improvements on records</span></span>

## <a name="allow-record-class"></a><span data-ttu-id="4781f-201">Consentire `record class`</span><span class="sxs-lookup"><span data-stu-id="4781f-201">Allow `record class`</span></span>

<span data-ttu-id="4781f-202">La sintassi esistente per i tipi di record consente di usare lo `record class` stesso significato di `record` :</span><span class="sxs-lookup"><span data-stu-id="4781f-202">The existing syntax for record types allows `record class` with the same meaning as `record`:</span></span>

```antlr
record_declaration
    : attributes? class_modifier* 'partial'? 'record' 'class'? identifier type_parameter_list?
      parameter_list? record_base? type_parameter_constraints_clause* record_body
    ;
```

## <a name="allow-user-defined-positional-members-to-be-fields"></a><span data-ttu-id="4781f-203">Consenti i membri posizionali definiti dall'utente come campi</span><span class="sxs-lookup"><span data-stu-id="4781f-203">Allow user-defined positional members to be fields</span></span>

<span data-ttu-id="4781f-204">Vedere https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-10-05.md#changing-the-member-type-of-a-primary-constructor-parameter</span><span class="sxs-lookup"><span data-stu-id="4781f-204">See https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-10-05.md#changing-the-member-type-of-a-primary-constructor-parameter</span></span>

<span data-ttu-id="4781f-205">C'è un problema di compatibilità con la compatibilità, quindi è possibile eliminare questa funzionalità oppure è necessario implementarla rapidamente, come correzione di bug.</span><span class="sxs-lookup"><span data-stu-id="4781f-205">There is a back compat issue, so we may either drop this feature or we need to implement it fast (as a bug fix).</span></span>

```csharp
public record Base
{
    public int Field;
}
public record Derived(int Field);
```

# <a name="allow-parameterless-constructors-and-member-initializers-in-structs"></a><span data-ttu-id="4781f-206">Consenti costruttori e inizializzatori di membri senza parametri negli struct</span><span class="sxs-lookup"><span data-stu-id="4781f-206">Allow parameterless constructors and member initializers in structs</span></span>

<span data-ttu-id="4781f-207">In struct verranno supportati sia costruttori senza parametri che inizializzatori di membri.</span><span class="sxs-lookup"><span data-stu-id="4781f-207">We are going to support both parameterless constructors and member initializers in structs.</span></span>
<span data-ttu-id="4781f-208">Questa operazione verrà specificata in altri dettagli.</span><span class="sxs-lookup"><span data-stu-id="4781f-208">This will be specified in more details.</span></span>

<span data-ttu-id="4781f-209">Note non elaborate:</span><span class="sxs-lookup"><span data-stu-id="4781f-209">Raw notes:</span></span>  
<span data-ttu-id="4781f-210">Consenti ctor senza parametri su struct e anche inizializzatori di campo (nessun rilevamento Runtime)</span><span class="sxs-lookup"><span data-stu-id="4781f-210">Allow parameterless ctors on structs and also field initializers (no runtime detection)</span></span>  
<span data-ttu-id="4781f-211">Verranno enumerati gli scenari in cui gli inizializzatori non vengono valutati: matrici, generics, default...</span><span class="sxs-lookup"><span data-stu-id="4781f-211">We will enumerate scenarios where initializers aren't evaluated: arrays, generics, default, ...</span></span>  
<span data-ttu-id="4781f-212">Si consideri la diagnostica per l'utilizzo di struct con un ctor senza parametri in alcuni di questi casi?</span><span class="sxs-lookup"><span data-stu-id="4781f-212">Consider diagnostics for using struct with parameterless ctor in some of those cases?</span></span>  

# <a name="open-questions"></a><span data-ttu-id="4781f-213">Domande aperte</span><span class="sxs-lookup"><span data-stu-id="4781f-213">Open questions</span></span>

- <span data-ttu-id="4781f-214">è consigliabile non consentire un costruttore definito dall'utente con una firma del costruttore di copia?</span><span class="sxs-lookup"><span data-stu-id="4781f-214">should we disallow a user-defined constructor with a copy constructor signature?</span></span>
- <span data-ttu-id="4781f-215">confermare che si desidera impedire i membri denominati "clone".</span><span class="sxs-lookup"><span data-stu-id="4781f-215">confirm that we want to disallow members named "Clone".</span></span>
- <span data-ttu-id="4781f-216">`with` sui generics?</span><span class="sxs-lookup"><span data-stu-id="4781f-216">`with` on generics?</span></span> <span data-ttu-id="4781f-217">(può influire sulla progettazione per gli struct di record)</span><span class="sxs-lookup"><span data-stu-id="4781f-217">(may affect the design for record structs)</span></span>
- <span data-ttu-id="4781f-218">Verificare che la logica sintetizzata `Equals` sia equivalente dal punto di vista funzionale all'implementazione di runtime, ad esempio float. Nan</span><span class="sxs-lookup"><span data-stu-id="4781f-218">double-check that synthesized `Equals` logic is functionally equivalent to runtime implementation (e.g. float.NaN)</span></span>
- <span data-ttu-id="4781f-219">come riconoscere gli struct di record nei metadati</span><span class="sxs-lookup"><span data-stu-id="4781f-219">how to recognize record structs in metadata?</span></span> <span data-ttu-id="4781f-220">(non è presente un metodo clone che è possibile utilizzare...)</span><span class="sxs-lookup"><span data-stu-id="4781f-220">(we don't have an unspeakable clone method to leverage...)</span></span>

## <a name="answered"></a><span data-ttu-id="4781f-221">Rispose</span><span class="sxs-lookup"><span data-stu-id="4781f-221">Answered</span></span>

- <span data-ttu-id="4781f-222">confermare che si vuole lasciare la progettazione PrintMembers (metodo separato che restituisce `bool` ) (risposta: Sì)</span><span class="sxs-lookup"><span data-stu-id="4781f-222">confirm that we want to keep PrintMembers design (separate method returning `bool`) (answer: yes)</span></span>
- <span data-ttu-id="4781f-223">confermare che non verrà consentito `record ref struct` (emettere con i `IEquatable<RefStruct>` campi di riferimento e) (risposta: Sì)</span><span class="sxs-lookup"><span data-stu-id="4781f-223">confirm we won't allow `record ref struct` (issue with `IEquatable<RefStruct>` and ref fields) (answer: yes)</span></span>
- <span data-ttu-id="4781f-224">confermare l'implementazione dei membri di uguaglianza.</span><span class="sxs-lookup"><span data-stu-id="4781f-224">confirm implementation of equality members.</span></span> <span data-ttu-id="4781f-225">In alternativa `bool Equals(R other)` , gli operatori vengono sintetizzati `bool Equals(object? other)` e tutti semplicemente delegati a `ValueType.Equals` .</span><span class="sxs-lookup"><span data-stu-id="4781f-225">Alternative is that synthesized `bool Equals(R other)`, `bool Equals(object? other)` and operators all just delegate to `ValueType.Equals`.</span></span> <span data-ttu-id="4781f-226">(risposta: Sì)</span><span class="sxs-lookup"><span data-stu-id="4781f-226">(answer: yes)</span></span>
- <span data-ttu-id="4781f-227">confermare che si desidera consentire gli inizializzatori di campo quando è presente un costruttore primario.</span><span class="sxs-lookup"><span data-stu-id="4781f-227">confirm that we want to allow field initializers when there is a primary constructor.</span></span> <span data-ttu-id="4781f-228">Si vuole anche consentire i costruttori di struct senza parametri mentre ci troviamo (il problema dell'attivatore era apparentemente fisso)?</span><span class="sxs-lookup"><span data-stu-id="4781f-228">Do we also want to allow parameterless struct constructors while we're at it (the Activator issue was apparently fixed)?</span></span> <span data-ttu-id="4781f-229">(risposta: Sì, è necessario rivedere la specifica aggiornata in LDM)</span><span class="sxs-lookup"><span data-stu-id="4781f-229">(answer: yes, updated spec should be reviewed in LDM)</span></span>
- <span data-ttu-id="4781f-230">quanto si vuole dire sul `Combine` Metodo?</span><span class="sxs-lookup"><span data-stu-id="4781f-230">how much do we want to say about `Combine` method?</span></span> <span data-ttu-id="4781f-231">(risposta: il minor possibile)</span><span class="sxs-lookup"><span data-stu-id="4781f-231">(answer: as little as possible)</span></span>
