---
ms.openlocfilehash: 01ebf5caf071467496d4ac1c2664f64b790525cf
ms.sourcegitcommit: 8087d27db178039c2dc5fbfa6b3ac9cde8cd2ad2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451320"
---
# <a name="unsafe-code"></a><span data-ttu-id="a8492-101">Codice di tipo unsafe</span><span class="sxs-lookup"><span data-stu-id="a8492-101">Unsafe code</span></span>

<span data-ttu-id="a8492-102">Il linguaggio C# di base, come definito nei capitoli precedenti, differisce in particolare da C e C++ nell'omissione dei puntatori come tipo di dati.</span><span class="sxs-lookup"><span data-stu-id="a8492-102">The core C# language, as defined in the preceding chapters, differs notably from C and C++ in its omission of pointers as a data type.</span></span> <span data-ttu-id="a8492-103">C# fornisce invece riferimenti e la possibilità di creare oggetti gestiti da un Garbage Collector.</span><span class="sxs-lookup"><span data-stu-id="a8492-103">Instead, C# provides references and the ability to create objects that are managed by a garbage collector.</span></span> <span data-ttu-id="a8492-104">Questo progetto, associato ad altre funzionalità, rende C# un linguaggio molto più sicuro rispetto a C o C++.</span><span class="sxs-lookup"><span data-stu-id="a8492-104">This design, coupled with other features, makes C# a much safer language than C or C++.</span></span> <span data-ttu-id="a8492-105">Nel linguaggio C# di base non è semplicemente possibile avere una variabile non inizializzata, un puntatore "penzoloni" o un'espressione che indicizza una matrice oltre i limiti.</span><span class="sxs-lookup"><span data-stu-id="a8492-105">In the core C# language it is simply not possible to have an uninitialized variable, a "dangling" pointer, or an expression that indexes an array beyond its bounds.</span></span> <span data-ttu-id="a8492-106">Vengono quindi eliminate le intere categorie di bug che periodicamente affliggono i programmi C e C++.</span><span class="sxs-lookup"><span data-stu-id="a8492-106">Whole categories of bugs that routinely plague C and C++ programs are thus eliminated.</span></span>

<span data-ttu-id="a8492-107">Sebbene praticamente ogni costrutto di tipo di puntatore in C o C++ includa una controparte del tipo di riferimento in C#, tuttavia, esistono situazioni in cui l'accesso ai tipi di puntatore diventa una necessità.</span><span class="sxs-lookup"><span data-stu-id="a8492-107">While practically every pointer type construct in C or C++ has a reference type counterpart in C#, nonetheless, there are situations where access to pointer types becomes a necessity.</span></span> <span data-ttu-id="a8492-108">Ad esempio, l'interazione con il sistema operativo sottostante, l'accesso a un dispositivo mappato alla memoria o l'implementazione di un algoritmo critico per il tempo potrebbe non essere possibile o praticabile senza l'accesso ai puntatori.</span><span class="sxs-lookup"><span data-stu-id="a8492-108">For example, interfacing with the underlying operating system, accessing a memory-mapped device, or implementing a time-critical algorithm may not be possible or practical without access to pointers.</span></span> <span data-ttu-id="a8492-109">Per rispondere a questa esigenza, C# consente di scrivere codice di tipo ***unsafe***.</span><span class="sxs-lookup"><span data-stu-id="a8492-109">To address this need, C# provides the ability to write ***unsafe code***.</span></span>

<span data-ttu-id="a8492-110">In codice unsafe è possibile dichiarare e operare sui puntatori, per eseguire conversioni tra puntatori e tipi integrali, per prendere l'indirizzo delle variabili e così via.</span><span class="sxs-lookup"><span data-stu-id="a8492-110">In unsafe code it is possible to declare and operate on pointers, to perform conversions between pointers and integral types, to take the address of variables, and so forth.</span></span> <span data-ttu-id="a8492-111">In un certo senso, la scrittura di codice non sicuro è molto simile alla scrittura di codice C in un programma C#.</span><span class="sxs-lookup"><span data-stu-id="a8492-111">In a sense, writing unsafe code is much like writing C code within a C# program.</span></span>

<span data-ttu-id="a8492-112">Il codice di tipo unsafe è infatti una funzionalità "sicura" dal punto di vista di sviluppatori e utenti.</span><span class="sxs-lookup"><span data-stu-id="a8492-112">Unsafe code is in fact a "safe" feature from the perspective of both developers and users.</span></span> <span data-ttu-id="a8492-113">Il codice di tipo unsafe deve essere contrassegnato chiaramente con il modificatore `unsafe` , in modo che gli sviluppatori non possano usare accidentalmente funzionalità non sicure e il motore di esecuzione funzioni per garantire che il codice non sicuro non possa essere eseguito in un ambiente non attendibile.</span><span class="sxs-lookup"><span data-stu-id="a8492-113">Unsafe code must be clearly marked with the modifier `unsafe`, so developers can't possibly use unsafe features accidentally, and the execution engine works to ensure that unsafe code cannot be executed in an untrusted environment.</span></span>

## <a name="unsafe-contexts"></a><span data-ttu-id="a8492-114">Contesti non sicuri</span><span class="sxs-lookup"><span data-stu-id="a8492-114">Unsafe contexts</span></span>

<span data-ttu-id="a8492-115">Le funzionalità unsafe di C# sono disponibili solo in contesti non sicuri.</span><span class="sxs-lookup"><span data-stu-id="a8492-115">The unsafe features of C# are available only in unsafe contexts.</span></span> <span data-ttu-id="a8492-116">Un contesto unsafe viene introdotto includendo un `unsafe` modificatore nella dichiarazione di un tipo o di un membro oppure utilizzando un *unsafe_statement*:</span><span class="sxs-lookup"><span data-stu-id="a8492-116">An unsafe context is introduced by including an `unsafe` modifier in the declaration of a type or member, or by employing an *unsafe_statement*:</span></span>

*  <span data-ttu-id="a8492-117">Una dichiarazione di una classe, una struttura, un'interfaccia o un delegato può includere un `unsafe` modificatore, nel qual caso l'intero extent testuale della dichiarazione del tipo (incluso il corpo della classe, dello struct o dell'interfaccia) viene considerato un contesto non sicuro.</span><span class="sxs-lookup"><span data-stu-id="a8492-117">A declaration of a class, struct, interface, or delegate may include an `unsafe` modifier, in which case the entire textual extent of that type declaration (including the body of the class, struct, or interface) is considered an unsafe context.</span></span>
*  <span data-ttu-id="a8492-118">Una dichiarazione di un campo, un metodo, una proprietà, un evento, un indicizzatore, un operatore, un costruttore di istanza, un distruttore o un costruttore statico può includere un `unsafe` modificatore, nel qual caso l'intero extent testuale della dichiarazione del membro viene considerato un contesto non sicuro.</span><span class="sxs-lookup"><span data-stu-id="a8492-118">A declaration of a field, method, property, event, indexer, operator, instance constructor, destructor, or static constructor may include an `unsafe` modifier, in which case the entire textual extent of that member declaration is considered an unsafe context.</span></span>
*  <span data-ttu-id="a8492-119">Un *unsafe_statement* consente l'utilizzo di un contesto non sicuro all'interno di un *blocco*.</span><span class="sxs-lookup"><span data-stu-id="a8492-119">An *unsafe_statement* enables the use of an unsafe context within a *block*.</span></span> <span data-ttu-id="a8492-120">L'intero extent testuale del *blocco* associato viene considerato un contesto non sicuro.</span><span class="sxs-lookup"><span data-stu-id="a8492-120">The entire textual extent of the associated *block* is considered an unsafe context.</span></span>

<span data-ttu-id="a8492-121">Le produzioni di grammatica associate sono illustrate di seguito.</span><span class="sxs-lookup"><span data-stu-id="a8492-121">The associated grammar productions are shown below.</span></span>

```antlr
class_modifier_unsafe
    : 'unsafe'
    ;

struct_modifier_unsafe
    : 'unsafe'
    ;

interface_modifier_unsafe
    : 'unsafe'
    ;

delegate_modifier_unsafe
    : 'unsafe'
    ;

field_modifier_unsafe
    : 'unsafe'
    ;

method_modifier_unsafe
    : 'unsafe'
    ;

property_modifier_unsafe
    : 'unsafe'
    ;

event_modifier_unsafe
    : 'unsafe'
    ;

indexer_modifier_unsafe
    : 'unsafe'
    ;

operator_modifier_unsafe
    : 'unsafe'
    ;

constructor_modifier_unsafe
    : 'unsafe'
    ;

destructor_declaration_unsafe
    : attributes? 'extern'? 'unsafe'? '~' identifier '(' ')' destructor_body
    | attributes? 'unsafe'? 'extern'? '~' identifier '(' ')' destructor_body
    ;

static_constructor_modifiers_unsafe
    : 'extern'? 'unsafe'? 'static'
    | 'unsafe'? 'extern'? 'static'
    | 'extern'? 'static' 'unsafe'?
    | 'unsafe'? 'static' 'extern'?
    | 'static' 'extern'? 'unsafe'?
    | 'static' 'unsafe'? 'extern'?
    ;

embedded_statement_unsafe
    : unsafe_statement
    | fixed_statement
    ;

unsafe_statement
    : 'unsafe' block
    ;
```

<span data-ttu-id="a8492-122">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="a8492-122">In the example</span></span>

```csharp
public unsafe struct Node
{
    public int Value;
    public Node* Left;
    public Node* Right;
}
```

<span data-ttu-id="a8492-123">il `unsafe` modificatore specificato nella dichiarazione struct causa l'intero extent testuale della dichiarazione struct a diventare un contesto non sicuro.</span><span class="sxs-lookup"><span data-stu-id="a8492-123">the `unsafe` modifier specified in the struct declaration causes the entire textual extent of the struct declaration to become an unsafe context.</span></span> <span data-ttu-id="a8492-124">È quindi possibile dichiarare i `Left` campi e in modo che `Right` siano di tipo puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-124">Thus, it is possible to declare the `Left` and `Right` fields to be of a pointer type.</span></span> <span data-ttu-id="a8492-125">È possibile scrivere anche l'esempio precedente</span><span class="sxs-lookup"><span data-stu-id="a8492-125">The example above could also be written</span></span>

```csharp
public struct Node
{
    public int Value;
    public unsafe Node* Left;
    public unsafe Node* Right;
}
```

<span data-ttu-id="a8492-126">In questo caso, i `unsafe` modificatori nelle dichiarazioni di campo fanno sì che tali dichiarazioni vengano considerate contesti non sicuri.</span><span class="sxs-lookup"><span data-stu-id="a8492-126">Here, the `unsafe` modifiers in the field declarations cause those declarations to be considered unsafe contexts.</span></span>

<span data-ttu-id="a8492-127">Oltre a stabilire un contesto non sicuro, consentendo così l'uso di tipi di puntatore, il `unsafe` modificatore non ha alcun effetto su un tipo o un membro.</span><span class="sxs-lookup"><span data-stu-id="a8492-127">Other than establishing an unsafe context, thus permitting the use of pointer types, the `unsafe` modifier has no effect on a type or a member.</span></span> <span data-ttu-id="a8492-128">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="a8492-128">In the example</span></span>

```csharp
public class A
{
    public unsafe virtual void F() {
        char* p;
        ...
    }
}

public class B: A
{
    public override void F() {
        base.F();
        ...
    }
}
```

<span data-ttu-id="a8492-129">il `unsafe` modificatore del `F` metodo in `A` comporta semplicemente l'estensione testuale di `F` per diventare un contesto non sicuro in cui è possibile utilizzare le funzionalità non sicure del linguaggio.</span><span class="sxs-lookup"><span data-stu-id="a8492-129">the `unsafe` modifier on the `F` method in `A` simply causes the textual extent of `F` to become an unsafe context in which the unsafe features of the language can be used.</span></span> <span data-ttu-id="a8492-130">Nell'override di `F` in `B` , non è necessario specificare di nuovo il `unsafe` modificatore, a meno che, naturalmente, il `F` metodo di `B` per sé non debba accedere a funzionalità non sicure.</span><span class="sxs-lookup"><span data-stu-id="a8492-130">In the override of `F` in `B`, there is no need to re-specify the `unsafe` modifier -- unless, of course, the `F` method in `B` itself needs access to unsafe features.</span></span>

<span data-ttu-id="a8492-131">La situazione è leggermente diversa quando un tipo di puntatore fa parte della firma del metodo</span><span class="sxs-lookup"><span data-stu-id="a8492-131">The situation is slightly different when a pointer type is part of the method's signature</span></span>

```csharp
public unsafe class A
{
    public virtual void F(char* p) {...}
}

public class B: A
{
    public unsafe override void F(char* p) {...}
}
```

<span data-ttu-id="a8492-132">Qui, poiché la `F` firma include un tipo di puntatore, può essere scritta solo in un contesto unsafe.</span><span class="sxs-lookup"><span data-stu-id="a8492-132">Here, because `F`'s signature includes a pointer type, it can only be written in an unsafe context.</span></span> <span data-ttu-id="a8492-133">Tuttavia, il contesto unsafe può essere introdotto rendendo l'intera classe unsafe, come nel caso di `A` o includendo un `unsafe` modificatore nella dichiarazione del metodo, come nel caso di `B` .</span><span class="sxs-lookup"><span data-stu-id="a8492-133">However, the unsafe context can be introduced by either making the entire class unsafe, as is the case in `A`, or by including an `unsafe` modifier in the method declaration, as is the case in `B`.</span></span>

## <a name="pointer-types"></a><span data-ttu-id="a8492-134">Tipi puntatore</span><span class="sxs-lookup"><span data-stu-id="a8492-134">Pointer types</span></span>

<span data-ttu-id="a8492-135">In un contesto unsafe, un *tipo* ([tipi](types.md)) può essere un *pointer_type* nonché un *value_type* o un *reference_type*.</span><span class="sxs-lookup"><span data-stu-id="a8492-135">In an unsafe context, a *type* ([Types](types.md)) may be a *pointer_type* as well as a *value_type* or a *reference_type*.</span></span> <span data-ttu-id="a8492-136">Tuttavia, un *pointer_type* può essere usato anche in un' `typeof` espressione ([espressioni di creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions)) al di fuori di un contesto non sicuro perché tale utilizzo non è sicuro.</span><span class="sxs-lookup"><span data-stu-id="a8492-136">However, a *pointer_type* may also be used in a `typeof` expression ([Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions)) outside of an unsafe context as such usage is not unsafe.</span></span>

```antlr
type_unsafe
    : pointer_type
    ;
```

<span data-ttu-id="a8492-137">Un *pointer_type* viene scritto come *unmanaged_type* o parola chiave `void` , seguito da un `*` token:</span><span class="sxs-lookup"><span data-stu-id="a8492-137">A *pointer_type* is written as an *unmanaged_type* or the keyword `void`, followed by a `*` token:</span></span>

```antlr
pointer_type
    : unmanaged_type '*'
    | 'void' '*'
    ;

unmanaged_type
    : type
    ;
```

<span data-ttu-id="a8492-138">Il tipo specificato prima `*` di in un tipo di puntatore è denominato ***tipo referente*** del tipo di puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-138">The type specified before the `*` in a pointer type is called the ***referent type*** of the pointer type.</span></span> <span data-ttu-id="a8492-139">Rappresenta il tipo della variabile a cui fa riferimento un valore del tipo di puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-139">It represents the type of the variable to which a value of the pointer type points.</span></span>

<span data-ttu-id="a8492-140">A differenza dei riferimenti (valori dei tipi di riferimento), i puntatori non vengono rilevati dal Garbage Collector, il Garbage Collector non conosce i puntatori e i dati a cui puntano.</span><span class="sxs-lookup"><span data-stu-id="a8492-140">Unlike references (values of reference types), pointers are not tracked by the garbage collector -- the garbage collector has no knowledge of pointers and the data to which they point.</span></span> <span data-ttu-id="a8492-141">Per questo motivo un puntatore non può puntare a un riferimento o a uno struct che contiene riferimenti e il tipo referente di un puntatore deve essere un *unmanaged_type*.</span><span class="sxs-lookup"><span data-stu-id="a8492-141">For this reason a pointer is not permitted to point to a reference or to a struct that contains references, and the referent type of a pointer must be an *unmanaged_type*.</span></span>

<span data-ttu-id="a8492-142">Un *unmanaged_type* è un tipo che non è un tipo *reference_type* o costruito e non contiene *reference_type* o campi di tipo costruiti a qualsiasi livello di annidamento.</span><span class="sxs-lookup"><span data-stu-id="a8492-142">An *unmanaged_type* is any type that isn't a *reference_type* or constructed type, and doesn't contain *reference_type* or constructed type fields at any level of nesting.</span></span> <span data-ttu-id="a8492-143">In altre parole, un *unmanaged_type* è uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="a8492-143">In other words, an *unmanaged_type* is one of the following:</span></span>

*  <span data-ttu-id="a8492-144">`sbyte`, `byte` , `short` , `ushort` , `int` , `uint` , `long` , `ulong` , `char` , `float` , `double` , `decimal` o `bool` .</span><span class="sxs-lookup"><span data-stu-id="a8492-144">`sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, or `bool`.</span></span>
*  <span data-ttu-id="a8492-145">Qualsiasi *enum_type*.</span><span class="sxs-lookup"><span data-stu-id="a8492-145">Any *enum_type*.</span></span>
*  <span data-ttu-id="a8492-146">Qualsiasi *pointer_type*.</span><span class="sxs-lookup"><span data-stu-id="a8492-146">Any *pointer_type*.</span></span>
*  <span data-ttu-id="a8492-147">Qualsiasi *struct_type* definito dall'utente che non sia un tipo costruito e contiene solo campi di *unmanaged_type*.</span><span class="sxs-lookup"><span data-stu-id="a8492-147">Any user-defined *struct_type* that is not a constructed type and contains fields of *unmanaged_type*s only.</span></span>

<span data-ttu-id="a8492-148">La regola intuitiva per la combinazione di puntatori e riferimenti è che i riferimenti di riferimenti (oggetti) sono consentiti per contenere i puntatori, ma i riferimenti ai puntatori non possono contenere riferimenti.</span><span class="sxs-lookup"><span data-stu-id="a8492-148">The intuitive rule for mixing of pointers and references is that referents of references (objects) are permitted to contain pointers, but referents of pointers are not permitted to contain references.</span></span>

<span data-ttu-id="a8492-149">Nella tabella seguente sono riportati alcuni esempi di tipi di puntatore:</span><span class="sxs-lookup"><span data-stu-id="a8492-149">Some examples of pointer types are given in the table below:</span></span>

| <span data-ttu-id="a8492-150">__Esempio__</span><span class="sxs-lookup"><span data-stu-id="a8492-150">__Example__</span></span> | <span data-ttu-id="a8492-151">__Descrizione__</span><span class="sxs-lookup"><span data-stu-id="a8492-151">__Description__</span></span>                               |
|-------------|-----------------------------------------------|
| `byte*`     | <span data-ttu-id="a8492-152">Puntatore a `byte`</span><span class="sxs-lookup"><span data-stu-id="a8492-152">Pointer to `byte`</span></span>                             |
| `char*`     | <span data-ttu-id="a8492-153">Puntatore a `char`</span><span class="sxs-lookup"><span data-stu-id="a8492-153">Pointer to `char`</span></span>                             |
| `int**`     | <span data-ttu-id="a8492-154">Puntatore a puntatore a`int`</span><span class="sxs-lookup"><span data-stu-id="a8492-154">Pointer to pointer to `int`</span></span>                   |
| `int*[]`    | <span data-ttu-id="a8492-155">Matrice unidimensionale di puntatori a`int`</span><span class="sxs-lookup"><span data-stu-id="a8492-155">Single-dimensional array of pointers to `int`</span></span> |
| `void*`     | <span data-ttu-id="a8492-156">Puntatore al tipo sconosciuto</span><span class="sxs-lookup"><span data-stu-id="a8492-156">Pointer to unknown type</span></span>                       |

<span data-ttu-id="a8492-157">Per un'implementazione specifica, tutti i tipi di puntatore devono avere le stesse dimensioni e la stessa rappresentazione.</span><span class="sxs-lookup"><span data-stu-id="a8492-157">For a given implementation, all pointer types must have the same size and representation.</span></span>

<span data-ttu-id="a8492-158">Diversamente da C e C++, quando più puntatori vengono dichiarati nella stessa dichiarazione, in C# l'oggetto `*` viene scritto solo con il tipo sottostante, non come un punteggiatore di prefisso per ogni nome di puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-158">Unlike C and C++, when multiple pointers are declared in the same declaration, in C# the `*` is written along with the underlying type only, not as a prefix punctuator on each pointer name.</span></span> <span data-ttu-id="a8492-159">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a8492-159">For example</span></span>

```csharp
int* pi, pj;    // NOT as int *pi, *pj;
```

<span data-ttu-id="a8492-160">Il valore di un puntatore di tipo `T*` rappresenta l'indirizzo di una variabile di tipo `T` .</span><span class="sxs-lookup"><span data-stu-id="a8492-160">The value of a pointer having type `T*` represents the address of a variable of type `T`.</span></span> <span data-ttu-id="a8492-161">`*`Per accedere a questa variabile, è possibile usare l'operatore di riferimento indiretto del puntatore ([riferimento indiretto del puntatore](unsafe-code.md#pointer-indirection)).</span><span class="sxs-lookup"><span data-stu-id="a8492-161">The pointer indirection operator `*` ([Pointer indirection](unsafe-code.md#pointer-indirection)) may be used to access this variable.</span></span> <span data-ttu-id="a8492-162">Ad esempio, data una variabile `P` di tipo `int*` , l'espressione `*P` indica la `int` variabile trovata in corrispondenza dell'indirizzo contenuto in `P` .</span><span class="sxs-lookup"><span data-stu-id="a8492-162">For example, given a variable `P` of type `int*`, the expression `*P` denotes the `int` variable found at the address contained in `P`.</span></span>

<span data-ttu-id="a8492-163">Come un riferimento a un oggetto, un puntatore può essere `null` .</span><span class="sxs-lookup"><span data-stu-id="a8492-163">Like an object reference, a pointer may be `null`.</span></span> <span data-ttu-id="a8492-164">L'applicazione dell'operatore di riferimento indiretto a un `null` puntatore comporta un comportamento definito dall'implementazione.</span><span class="sxs-lookup"><span data-stu-id="a8492-164">Applying the indirection operator to a `null` pointer results in implementation-defined behavior.</span></span> <span data-ttu-id="a8492-165">Un puntatore con valore `null` è rappresentato da All-bits-zero.</span><span class="sxs-lookup"><span data-stu-id="a8492-165">A pointer with value `null` is represented by all-bits-zero.</span></span>

<span data-ttu-id="a8492-166">Il `void*` tipo rappresenta un puntatore a un tipo sconosciuto.</span><span class="sxs-lookup"><span data-stu-id="a8492-166">The `void*` type represents a pointer to an unknown type.</span></span> <span data-ttu-id="a8492-167">Poiché il tipo referente è sconosciuto, non è possibile applicare l'operatore di riferimento indiretto a un puntatore di tipo `void*` , né eseguire operazioni aritmetiche su un puntatore di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="a8492-167">Because the referent type is unknown, the indirection operator cannot be applied to a pointer of type `void*`, nor can any arithmetic be performed on such a pointer.</span></span> <span data-ttu-id="a8492-168">Tuttavia, `void*` è possibile eseguire il cast di un puntatore di tipo a qualsiasi altro tipo di puntatore (e viceversa).</span><span class="sxs-lookup"><span data-stu-id="a8492-168">However, a pointer of type `void*` can be cast to any other pointer type (and vice versa).</span></span>

<span data-ttu-id="a8492-169">I tipi di puntatore sono una categoria separata di tipi.</span><span class="sxs-lookup"><span data-stu-id="a8492-169">Pointer types are a separate category of types.</span></span> <span data-ttu-id="a8492-170">A differenza dei tipi di riferimento e dei tipi di valore, i tipi di puntatore non ereditano da `object` e non esistono conversioni tra i tipi di puntatore e `object` .</span><span class="sxs-lookup"><span data-stu-id="a8492-170">Unlike reference types and value types, pointer types do not inherit from `object` and no conversions exist between pointer types and `object`.</span></span> <span data-ttu-id="a8492-171">In particolare, la conversione boxing e unboxing ([Boxing e](types.md#boxing-and-unboxing)unboxing) non sono supportate per i puntatori.</span><span class="sxs-lookup"><span data-stu-id="a8492-171">In particular, boxing and unboxing ([Boxing and unboxing](types.md#boxing-and-unboxing)) are not supported for pointers.</span></span> <span data-ttu-id="a8492-172">Tuttavia, le conversioni sono consentite tra tipi di puntatore diversi e tra tipi di puntatore e tipi integrali.</span><span class="sxs-lookup"><span data-stu-id="a8492-172">However, conversions are permitted between different pointer types and between pointer types and the integral types.</span></span> <span data-ttu-id="a8492-173">Questa operazione è descritta in [conversioni di puntatori](unsafe-code.md#pointer-conversions).</span><span class="sxs-lookup"><span data-stu-id="a8492-173">This is described in [Pointer conversions](unsafe-code.md#pointer-conversions).</span></span>

<span data-ttu-id="a8492-174">Non è possibile usare un *pointer_type* come argomento di tipo ([tipi costruiti](types.md#constructed-types)) e l'inferenza del tipo ([inferenza](expressions.md#type-inference)del tipo) ha esito negativo in chiamate a metodi generici che avrebbero dedotto un argomento di tipo come tipo di puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-174">A *pointer_type* cannot be used as a type argument ([Constructed types](types.md#constructed-types)), and type inference ([Type inference](expressions.md#type-inference)) fails on generic method calls that would have inferred a type argument to be a pointer type.</span></span>

<span data-ttu-id="a8492-175">Un *pointer_type* può essere utilizzato come tipo di campo volatile ([campi volatili](classes.md#volatile-fields)).</span><span class="sxs-lookup"><span data-stu-id="a8492-175">A *pointer_type* may be used as the type of a volatile field ([Volatile fields](classes.md#volatile-fields)).</span></span>

<span data-ttu-id="a8492-176">Sebbene i puntatori possano essere passati `ref` come `out` parametri o, questo può causare un comportamento non definito, poiché il puntatore può essere impostato in modo da puntare a una variabile locale che non esiste più quando il metodo chiamato restituisce oppure l'oggetto fisso a cui è stato usato per puntare, non è più fisso.</span><span class="sxs-lookup"><span data-stu-id="a8492-176">Although pointers can be passed as `ref` or `out` parameters, doing so can cause undefined behavior, since the pointer may well be set to point to a local variable which no longer exists when the called method returns, or the fixed object to which it used to point, is no longer fixed.</span></span> <span data-ttu-id="a8492-177">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a8492-177">For example:</span></span>

```csharp
using System;

class Test
{
    static int value = 20;

    unsafe static void F(out int* pi1, ref int* pi2) {
        int i = 10;
        pi1 = &i;

        fixed (int* pj = &value) {
            // ...
            pi2 = pj;
        }
    }

    static void Main() {
        int i = 10;
        unsafe {
            int* px1;
            int* px2 = &i;

            F(out px1, ref px2);

            Console.WriteLine("*px1 = {0}, *px2 = {1}",
                *px1, *px2);    // undefined behavior
        }
    }
}
```

<span data-ttu-id="a8492-178">Un metodo può restituire un valore di un tipo e tale tipo può essere un puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-178">A method can return a value of some type, and that type can be a pointer.</span></span> <span data-ttu-id="a8492-179">Se, ad esempio, si specifica un puntatore a una sequenza contigua di `int` , il numero di elementi di tale sequenza e un altro `int` valore, il metodo seguente restituisce l'indirizzo di tale valore nella sequenza, se viene rilevata una corrispondenza; in caso contrario, restituisce `null` :</span><span class="sxs-lookup"><span data-stu-id="a8492-179">For example, when given a pointer to a contiguous sequence of `int`s, that sequence's element count, and some other `int` value, the following method returns the address of that value in that sequence, if a match occurs; otherwise it returns `null`:</span></span>

```csharp
unsafe static int* Find(int* pi, int size, int value) {
    for (int i = 0; i < size; ++i) {
        if (*pi == value) 
            return pi;
        ++pi;
    }
    return null;
}
```

<span data-ttu-id="a8492-180">In un contesto non sicuro, sono disponibili diversi costrutti per operare sui puntatori:</span><span class="sxs-lookup"><span data-stu-id="a8492-180">In an unsafe context, several constructs are available for operating on pointers:</span></span>

*  <span data-ttu-id="a8492-181">L' `*` operatore può essere utilizzato per eseguire un riferimento indiretto del puntatore ([riferimento indiretto](unsafe-code.md#pointer-indirection)).</span><span class="sxs-lookup"><span data-stu-id="a8492-181">The `*` operator may be used to perform pointer indirection ([Pointer indirection](unsafe-code.md#pointer-indirection)).</span></span>
*  <span data-ttu-id="a8492-182">L' `->` operatore può essere usato per accedere a un membro di uno struct tramite un puntatore ([accesso al membro del puntatore](unsafe-code.md#pointer-member-access)).</span><span class="sxs-lookup"><span data-stu-id="a8492-182">The `->` operator may be used to access a member of a struct through a pointer ([Pointer member access](unsafe-code.md#pointer-member-access)).</span></span>
*  <span data-ttu-id="a8492-183">L' `[]` operatore può essere utilizzato per indicizzare un puntatore ([accesso all'elemento del puntatore](unsafe-code.md#pointer-element-access)).</span><span class="sxs-lookup"><span data-stu-id="a8492-183">The `[]` operator may be used to index a pointer ([Pointer element access](unsafe-code.md#pointer-element-access)).</span></span>
*  <span data-ttu-id="a8492-184">L' `&` operatore può essere utilizzato per ottenere l'indirizzo di una variabile ([l'operatore address-of](unsafe-code.md#the-address-of-operator)).</span><span class="sxs-lookup"><span data-stu-id="a8492-184">The `&` operator may be used to obtain the address of a variable ([The address-of operator](unsafe-code.md#the-address-of-operator)).</span></span>
*  <span data-ttu-id="a8492-185">Gli `++` `--` operatori e possono essere utilizzati per incrementare e decrementare i puntatori ([incremento e decremento del puntatore](unsafe-code.md#pointer-increment-and-decrement)).</span><span class="sxs-lookup"><span data-stu-id="a8492-185">The `++` and `--` operators may be used to increment and decrement pointers ([Pointer increment and decrement](unsafe-code.md#pointer-increment-and-decrement)).</span></span>
*  <span data-ttu-id="a8492-186">Gli `+` `-` operatori e possono essere utilizzati per eseguire l'aritmetica dei puntatori ([aritmetica del puntatore](unsafe-code.md#pointer-arithmetic)).</span><span class="sxs-lookup"><span data-stu-id="a8492-186">The `+` and `-` operators may be used to perform pointer arithmetic ([Pointer arithmetic](unsafe-code.md#pointer-arithmetic)).</span></span>
*  <span data-ttu-id="a8492-187">Gli `==` `!=` operatori,, `<` ,, `>` `<=` e `>=` possono essere usati per confrontare i puntatori ([confronto tra](unsafe-code.md#pointer-comparison)puntatori).</span><span class="sxs-lookup"><span data-stu-id="a8492-187">The `==`, `!=`, `<`, `>`, `<=`, and `>=` operators may be used to compare pointers ([Pointer comparison](unsafe-code.md#pointer-comparison)).</span></span>
*  <span data-ttu-id="a8492-188">L' `stackalloc` operatore può essere utilizzato per allocare memoria dallo stack di chiamate ([buffer a dimensione fissa](unsafe-code.md#fixed-size-buffers)).</span><span class="sxs-lookup"><span data-stu-id="a8492-188">The `stackalloc` operator may be used to allocate memory from the call stack ([Fixed size buffers](unsafe-code.md#fixed-size-buffers)).</span></span>
*  <span data-ttu-id="a8492-189">L' `fixed` istruzione può essere utilizzata per correggere temporaneamente una variabile in modo che sia possibile ottenere l'indirizzo ([istruzione fixed](unsafe-code.md#the-fixed-statement)).</span><span class="sxs-lookup"><span data-stu-id="a8492-189">The `fixed` statement may be used to temporarily fix a variable so its address can be obtained ([The fixed statement](unsafe-code.md#the-fixed-statement)).</span></span>

## <a name="fixed-and-moveable-variables"></a><span data-ttu-id="a8492-190">Variabili fisse e mobili</span><span class="sxs-lookup"><span data-stu-id="a8492-190">Fixed and moveable variables</span></span>

<span data-ttu-id="a8492-191">L'operatore address-of ([l'operatore address-of](unsafe-code.md#the-address-of-operator)) e l' `fixed` istruzione ([istruzione fixed](unsafe-code.md#the-fixed-statement)) dividono le variabili in due categorie: ***variabili fisse*** e ***variabili mobili***.</span><span class="sxs-lookup"><span data-stu-id="a8492-191">The address-of operator ([The address-of operator](unsafe-code.md#the-address-of-operator)) and the `fixed` statement ([The fixed statement](unsafe-code.md#the-fixed-statement)) divide variables into two categories: ***Fixed variables*** and ***moveable variables***.</span></span>

<span data-ttu-id="a8492-192">Le variabili fisse si trovano in percorsi di archiviazione che non sono interessati dall'operazione del Garbage Collector.</span><span class="sxs-lookup"><span data-stu-id="a8492-192">Fixed variables reside in storage locations that are unaffected by operation of the garbage collector.</span></span> <span data-ttu-id="a8492-193">Esempi di variabili fisse includono variabili locali, parametri di valore e variabili create mediante la dereferenziazione dei puntatori. D'altra parte, le variabili mobili si trovano in posizioni di archiviazione soggette alla rilocazione o all'eliminazione da parte del Garbage Collector.</span><span class="sxs-lookup"><span data-stu-id="a8492-193">(Examples of fixed variables include local variables, value parameters, and variables created by dereferencing pointers.) On the other hand, moveable variables reside in storage locations that are subject to relocation or disposal by the garbage collector.</span></span> <span data-ttu-id="a8492-194">(Esempi di variabili mobili includono campi in oggetti ed elementi di matrici).</span><span class="sxs-lookup"><span data-stu-id="a8492-194">(Examples of moveable variables include fields in objects and elements of arrays.)</span></span>

<span data-ttu-id="a8492-195">L' `&` operatore ([l'operatore address-of](unsafe-code.md#the-address-of-operator)) consente di ottenere l'indirizzo di una variabile fissa senza restrizioni.</span><span class="sxs-lookup"><span data-stu-id="a8492-195">The `&` operator ([The address-of operator](unsafe-code.md#the-address-of-operator)) permits the address of a fixed variable to be obtained without restrictions.</span></span> <span data-ttu-id="a8492-196">Tuttavia, poiché una variabile mobile è soggetta alla rilocazione o all'eliminazione da parte del Garbage Collector, l'indirizzo di una variabile mobile può essere ottenuto solo utilizzando un' `fixed` istruzione ([l'istruzione fixed](unsafe-code.md#the-fixed-statement)) e tale indirizzo rimane valido solo per la durata dell' `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="a8492-196">However, because a moveable variable is subject to relocation or disposal by the garbage collector, the address of a moveable variable can only be obtained using a `fixed` statement ([The fixed statement](unsafe-code.md#the-fixed-statement)), and that address remains valid only for the duration of that `fixed` statement.</span></span>

<span data-ttu-id="a8492-197">In termini precisi, una variabile fissa è una delle seguenti:</span><span class="sxs-lookup"><span data-stu-id="a8492-197">In precise terms, a fixed variable is one of the following:</span></span>

*  <span data-ttu-id="a8492-198">Variabile risultante da un *simple_name* ([nomi semplici](expressions.md#simple-names)) che fa riferimento a una variabile locale o a un parametro di valore, a meno che la variabile non venga acquisita da una funzione anonima.</span><span class="sxs-lookup"><span data-stu-id="a8492-198">A variable resulting from a *simple_name* ([Simple names](expressions.md#simple-names)) that refers to a local variable or a value parameter, unless the variable is captured by an anonymous function.</span></span>
*  <span data-ttu-id="a8492-199">Variabile risultante da un *member_access* ([accesso ai membri](expressions.md#member-access)) del form `V.I` , dove `V` è una variabile fissa di un *struct_type*.</span><span class="sxs-lookup"><span data-stu-id="a8492-199">A variable resulting from a *member_access* ([Member access](expressions.md#member-access)) of the form `V.I`, where `V` is a fixed variable of a *struct_type*.</span></span>
*  <span data-ttu-id="a8492-200">Variabile risultante da un *pointer_indirection_expression* ([riferimento indiretto del puntatore](unsafe-code.md#pointer-indirection)) del form `*P` , un *pointer_member_access* (accesso al membro del[puntatore](unsafe-code.md#pointer-member-access)) del form `P->I` o un *pointer_element_access* (accesso all'[elemento del puntatore](unsafe-code.md#pointer-element-access)) del form `P[E]` .</span><span class="sxs-lookup"><span data-stu-id="a8492-200">A variable resulting from a *pointer_indirection_expression* ([Pointer indirection](unsafe-code.md#pointer-indirection)) of the form `*P`, a *pointer_member_access* ([Pointer member access](unsafe-code.md#pointer-member-access)) of the form `P->I`, or a *pointer_element_access* ([Pointer element access](unsafe-code.md#pointer-element-access)) of the form `P[E]`.</span></span>

<span data-ttu-id="a8492-201">Tutte le altre variabili sono classificate come variabili mobili.</span><span class="sxs-lookup"><span data-stu-id="a8492-201">All other variables are classified as moveable variables.</span></span>

<span data-ttu-id="a8492-202">Si noti che un campo statico è classificato come variabile mobile.</span><span class="sxs-lookup"><span data-stu-id="a8492-202">Note that a static field is classified as a moveable variable.</span></span> <span data-ttu-id="a8492-203">Si noti inoltre che `ref` un `out` parametro o è classificato come variabile mobile, anche se l'argomento specificato per il parametro è una variabile fissa.</span><span class="sxs-lookup"><span data-stu-id="a8492-203">Also note that a `ref` or `out` parameter is classified as a moveable variable, even if the argument given for the parameter is a fixed variable.</span></span> <span data-ttu-id="a8492-204">Si noti infine che una variabile prodotta mediante la dereferenziazione di un puntatore viene sempre classificata come variabile fissa.</span><span class="sxs-lookup"><span data-stu-id="a8492-204">Finally, note that a variable produced by dereferencing a pointer is always classified as a fixed variable.</span></span>

## <a name="pointer-conversions"></a><span data-ttu-id="a8492-205">Conversioni puntatore</span><span class="sxs-lookup"><span data-stu-id="a8492-205">Pointer conversions</span></span>

<span data-ttu-id="a8492-206">In un contesto non sicuro, il set di conversioni implicite disponibili ([conversioni implicite](conversions.md#implicit-conversions)) viene esteso in modo da includere le seguenti conversioni implicite del puntatore:</span><span class="sxs-lookup"><span data-stu-id="a8492-206">In an unsafe context, the set of available implicit conversions ([Implicit conversions](conversions.md#implicit-conversions)) is extended to include the following implicit pointer conversions:</span></span>

*  <span data-ttu-id="a8492-207">Da qualsiasi *pointer_type* al tipo `void*` .</span><span class="sxs-lookup"><span data-stu-id="a8492-207">From any *pointer_type* to the type `void*`.</span></span>
*  <span data-ttu-id="a8492-208">Dal `null` valore letterale a qualsiasi *pointer_type*.</span><span class="sxs-lookup"><span data-stu-id="a8492-208">From the `null` literal to any *pointer_type*.</span></span>

<span data-ttu-id="a8492-209">Inoltre, in un contesto unsafe, il set di conversioni esplicite disponibili ([conversioni esplicite](conversions.md#explicit-conversions)) viene esteso in modo da includere le seguenti conversioni esplicite del puntatore:</span><span class="sxs-lookup"><span data-stu-id="a8492-209">Additionally, in an unsafe context, the set of available explicit conversions ([Explicit conversions](conversions.md#explicit-conversions)) is extended to include the following explicit pointer conversions:</span></span>

*  <span data-ttu-id="a8492-210">Da qualsiasi *pointer_type* a qualsiasi altra *pointer_type*.</span><span class="sxs-lookup"><span data-stu-id="a8492-210">From any *pointer_type* to any other *pointer_type*.</span></span>
*  <span data-ttu-id="a8492-211">Da `sbyte` , `byte` , `short` , `ushort` , `int` , `uint` , `long` o `ulong` a qualsiasi *pointer_type*.</span><span class="sxs-lookup"><span data-stu-id="a8492-211">From `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, or `ulong` to any *pointer_type*.</span></span>
*  <span data-ttu-id="a8492-212">Da qualsiasi *pointer_type* a `sbyte` , `byte` , `short` , `ushort` , `int` , `uint` , `long` o `ulong` .</span><span class="sxs-lookup"><span data-stu-id="a8492-212">From any *pointer_type* to `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, or `ulong`.</span></span>

<span data-ttu-id="a8492-213">Infine, in un contesto unsafe, il set di conversioni implicite standard ([conversioni implicite standard](conversions.md#standard-implicit-conversions)) include la seguente conversione del puntatore:</span><span class="sxs-lookup"><span data-stu-id="a8492-213">Finally, in an unsafe context, the set of standard implicit conversions ([Standard implicit conversions](conversions.md#standard-implicit-conversions)) includes the following pointer conversion:</span></span>

*  <span data-ttu-id="a8492-214">Da qualsiasi *pointer_type* al tipo `void*` .</span><span class="sxs-lookup"><span data-stu-id="a8492-214">From any *pointer_type* to the type `void*`.</span></span>

<span data-ttu-id="a8492-215">Le conversioni tra due tipi di puntatore non modificano mai il valore effettivo del puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-215">Conversions between two pointer types never change the actual pointer value.</span></span> <span data-ttu-id="a8492-216">In altre parole, una conversione da un tipo di puntatore a un altro non ha alcun effetto sull'indirizzo sottostante fornito dal puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-216">In other words, a conversion from one pointer type to another has no effect on the underlying address given by the pointer.</span></span>

<span data-ttu-id="a8492-217">Quando un tipo di puntatore viene convertito in un altro, se il puntatore risultante non è allineato correttamente per il tipo puntato, il comportamento non è definito se il risultato è dereferenziato.</span><span class="sxs-lookup"><span data-stu-id="a8492-217">When one pointer type is converted to another, if the resulting pointer is not correctly aligned for the pointed-to type, the behavior is undefined if the result is dereferenced.</span></span> <span data-ttu-id="a8492-218">In generale, il concetto "allineato correttamente" è transitivo: se un puntatore al tipo `A` è correttamente allineato per un puntatore al tipo `B` , che, a sua volta, è allineato correttamente per un puntatore al tipo `C` , un puntatore al tipo `A` è correttamente allineato per un puntatore al tipo `C` .</span><span class="sxs-lookup"><span data-stu-id="a8492-218">In general, the concept "correctly aligned" is transitive: if a pointer to type `A` is correctly aligned for a pointer to type `B`, which, in turn, is correctly aligned for a pointer to type `C`, then a pointer to type `A` is correctly aligned for a pointer to type `C`.</span></span>

<span data-ttu-id="a8492-219">Si consideri il caso seguente in cui si accede a una variabile con un tipo tramite un puntatore a un tipo diverso:</span><span class="sxs-lookup"><span data-stu-id="a8492-219">Consider the following case in which a variable having one type is accessed via a pointer to a different type:</span></span>

```csharp
char c = 'A';
char* pc = &c;
void* pv = pc;
int* pi = (int*)pv;
int i = *pi;         // undefined
*pi = 123456;        // undefined
```

<span data-ttu-id="a8492-220">Quando un tipo di puntatore viene convertito in un puntatore a byte, il risultato fa riferimento al byte più basso risolto della variabile.</span><span class="sxs-lookup"><span data-stu-id="a8492-220">When a pointer type is converted to a pointer to byte, the result points to the lowest addressed byte of the variable.</span></span> <span data-ttu-id="a8492-221">Gli incrementi successivi del risultato, fino alla dimensione della variabile, restituiscono puntatori ai byte rimanenti della variabile.</span><span class="sxs-lookup"><span data-stu-id="a8492-221">Successive increments of the result, up to the size of the variable, yield pointers to the remaining bytes of that variable.</span></span> <span data-ttu-id="a8492-222">Ad esempio, il metodo seguente visualizza ciascuno degli otto byte in un valore Double come valore esadecimale:</span><span class="sxs-lookup"><span data-stu-id="a8492-222">For example, the following method displays each of the eight bytes in a double as a hexadecimal value:</span></span>

```csharp
using System;

class Test
{
    unsafe static void Main() {
      double d = 123.456e23;
        unsafe {
           byte* pb = (byte*)&d;
            for (int i = 0; i < sizeof(double); ++i)
               Console.Write("{0:X2} ", *pb++);
            Console.WriteLine();
        }
    }
}
```

<span data-ttu-id="a8492-223">Naturalmente, l'output prodotto dipende da un tipo di output.</span><span class="sxs-lookup"><span data-stu-id="a8492-223">Of course, the output produced depends on endianness.</span></span>

<span data-ttu-id="a8492-224">I mapping tra i puntatori e i numeri interi vengono definiti dall'implementazione.</span><span class="sxs-lookup"><span data-stu-id="a8492-224">Mappings between pointers and integers are implementation-defined.</span></span> <span data-ttu-id="a8492-225">Tuttavia, nelle architetture della CPU 32 \* e a 64 bit con uno spazio degli indirizzi lineare, le conversioni di puntatori a o da tipi integrali si comportano in genere esattamente come le conversioni dei `uint` valori o, `ulong` rispettivamente, verso o da tali tipi integrali.</span><span class="sxs-lookup"><span data-stu-id="a8492-225">However, on 32\* and 64-bit CPU architectures with a linear address space, conversions of pointers to or from integral types typically behave exactly like conversions of `uint` or `ulong` values, respectively, to or from those integral types.</span></span>

### <a name="pointer-arrays"></a><span data-ttu-id="a8492-226">Matrici puntatore</span><span class="sxs-lookup"><span data-stu-id="a8492-226">Pointer arrays</span></span>

<span data-ttu-id="a8492-227">In un contesto non sicuro è possibile costruire matrici di puntatori.</span><span class="sxs-lookup"><span data-stu-id="a8492-227">In an unsafe context, arrays of pointers can be constructed.</span></span> <span data-ttu-id="a8492-228">Nelle matrici di puntatore sono consentite solo alcune conversioni applicabili ad altri tipi di matrice:</span><span class="sxs-lookup"><span data-stu-id="a8492-228">Only some of the conversions that apply to other array types are allowed on pointer arrays:</span></span>

*  <span data-ttu-id="a8492-229">La conversione implicita dei riferimenti ([conversioni implicite dei riferimenti](conversions.md#implicit-reference-conversions)) da qualsiasi *array_type* a `System.Array` e le interfacce implementate si applica anche alle matrici del puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-229">The implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) from any *array_type* to `System.Array` and the interfaces it implements also applies to pointer arrays.</span></span> <span data-ttu-id="a8492-230">Tuttavia, qualsiasi tentativo di accedere agli elementi della matrice tramite `System.Array` o le interfacce implementate genererà un'eccezione in fase di esecuzione, perché i tipi di puntatore non sono convertibili in `object` .</span><span class="sxs-lookup"><span data-stu-id="a8492-230">However, any attempt to access the array elements through `System.Array` or the interfaces it implements will result in an exception at run-time, as pointer types are not convertible to `object`.</span></span>
*  <span data-ttu-id="a8492-231">Le conversioni di riferimenti implicite ed esplicite ([conversioni implicite](conversions.md#implicit-reference-conversions)dei riferimenti, conversioni di [riferimenti esplicite](conversions.md#explicit-reference-conversions)) da un tipo di matrice unidimensionale `S[]` a `System.Collections.Generic.IList<T>` e le interfacce di base generiche non si applicano mai alle matrici di puntatore, perché i tipi di puntatore non possono essere utilizzati come argomenti di tipo e non esistono conversioni dai tipi puntatore ai tipi non puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-231">The implicit and explicit reference conversions ([Implicit reference conversions](conversions.md#implicit-reference-conversions), [Explicit reference conversions](conversions.md#explicit-reference-conversions)) from a single-dimensional array type `S[]` to `System.Collections.Generic.IList<T>` and its generic base interfaces never apply to pointer arrays, since pointer types cannot be used as type arguments, and there are no conversions from pointer types to non-pointer types.</span></span>
*  <span data-ttu-id="a8492-232">La conversione esplicita del riferimento ([conversioni esplicite dei riferimenti](conversions.md#explicit-reference-conversions)) da `System.Array` e le interfacce implementate in qualsiasi *array_type* si applicano alle matrici di puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-232">The explicit reference conversion ([Explicit reference conversions](conversions.md#explicit-reference-conversions)) from `System.Array` and the interfaces it implements to any *array_type* applies to pointer arrays.</span></span>
*  <span data-ttu-id="a8492-233">Le conversioni esplicite dei riferimenti ([conversioni esplicite di riferimento](conversions.md#explicit-reference-conversions)) da `System.Collections.Generic.IList<S>` e le relative interfacce di base a un tipo di matrice unidimensionale `T[]` non si applicano mai alle matrici del puntatore, perché i tipi di puntatore non possono essere utilizzati come argomenti di tipo e non ci sono conversioni dai tipi di puntatore ai tipi non puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-233">The explicit reference conversions ([Explicit reference conversions](conversions.md#explicit-reference-conversions)) from `System.Collections.Generic.IList<S>` and its base interfaces to a single-dimensional array type `T[]` never applies to pointer arrays, since pointer types cannot be used as type arguments, and there are no conversions from pointer types to non-pointer types.</span></span>

<span data-ttu-id="a8492-234">Queste restrizioni indicano che l'espansione per l' `foreach` istruzione sulle matrici descritte nell' [istruzione foreach](statements.md#the-foreach-statement) non può essere applicata alle matrici di puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-234">These restrictions mean that the expansion for the `foreach` statement over arrays described in [The foreach statement](statements.md#the-foreach-statement) cannot be applied to pointer arrays.</span></span> <span data-ttu-id="a8492-235">Al contrario, un'istruzione foreach nel formato</span><span class="sxs-lookup"><span data-stu-id="a8492-235">Instead, a foreach statement of the form</span></span>

```csharp
foreach (V v in x) embedded_statement
```

<span data-ttu-id="a8492-236">Se il tipo di `x` è un tipo di matrice del form `T[,,...,]` , `N` è il numero di dimensioni meno 1 e `T` o `V` è un tipo di puntatore, viene espanso usando i cicli for annidati come segue:</span><span class="sxs-lookup"><span data-stu-id="a8492-236">where the type of `x` is an array type of the form `T[,,...,]`, `N` is the number of dimensions minus 1 and `T` or `V` is a pointer type, is expanded using nested for-loops as follows:</span></span>

```csharp
{
    T[,,...,] a = x;
    for (int i0 = a.GetLowerBound(0); i0 <= a.GetUpperBound(0); i0++)
    for (int i1 = a.GetLowerBound(1); i1 <= a.GetUpperBound(1); i1++)
    ...
    for (int iN = a.GetLowerBound(N); iN <= a.GetUpperBound(N); iN++) {
        V v = (V)a.GetValue(i0,i1,...,iN);
        embedded_statement
    }
}
```

<span data-ttu-id="a8492-237">Le variabili `a` , `i0` , `i1` ,..., `iN` non sono visibili o accessibili a `x` oppure il *embedded_statement* o qualsiasi altro codice sorgente del programma.</span><span class="sxs-lookup"><span data-stu-id="a8492-237">The variables `a`, `i0`, `i1`, ..., `iN` are not visible to or accessible to `x` or the *embedded_statement* or any other source code of the program.</span></span> <span data-ttu-id="a8492-238">La variabile `v` è di sola lettura nell'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="a8492-238">The variable `v` is read-only in the embedded statement.</span></span> <span data-ttu-id="a8492-239">Se non è presente una conversione esplicita ([conversioni di puntatore](unsafe-code.md#pointer-conversions)) da `T` (il tipo di elemento) a `V` , viene generato un errore e non vengono eseguiti altri passaggi.</span><span class="sxs-lookup"><span data-stu-id="a8492-239">If there is not an explicit conversion ([Pointer conversions](unsafe-code.md#pointer-conversions)) from `T` (the element type) to `V`, an error is produced and no further steps are taken.</span></span> <span data-ttu-id="a8492-240">Se `x` ha il valore `null` , `System.NullReferenceException` viene generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="a8492-240">If `x` has the value `null`, a `System.NullReferenceException` is thrown at run-time.</span></span>

## <a name="pointers-in-expressions"></a><span data-ttu-id="a8492-241">Puntatori nelle espressioni</span><span class="sxs-lookup"><span data-stu-id="a8492-241">Pointers in expressions</span></span>

<span data-ttu-id="a8492-242">In un contesto unsafe, un'espressione può restituire un risultato di un tipo di puntatore, ma all'esterno di un contesto non sicuro si tratta di un errore in fase di compilazione perché un'espressione sia di tipo puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-242">In an unsafe context, an expression may yield a result of a pointer type, but outside an unsafe context it is a compile-time error for an expression to be of a pointer type.</span></span> <span data-ttu-id="a8492-243">In termini precisi, all'esterno di un contesto non sicuro si verifica un errore in fase di compilazione se qualsiasi *simple_name* ([nomi semplici](expressions.md#simple-names)), *member_access* ([accesso ai membri](expressions.md#member-access)), *invocation_expression* ([espressioni di chiamata](expressions.md#invocation-expressions)) o *element_access* ([accesso agli elementi](expressions.md#element-access)) è di tipo puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-243">In precise terms, outside an unsafe context a compile-time error occurs if any *simple_name* ([Simple names](expressions.md#simple-names)), *member_access* ([Member access](expressions.md#member-access)), *invocation_expression* ([Invocation expressions](expressions.md#invocation-expressions)), or *element_access* ([Element access](expressions.md#element-access)) is of a pointer type.</span></span>

<span data-ttu-id="a8492-244">In un contesto non sicuro, le produzioni *primary_no_array_creation_expression* ([espressioni primarie](expressions.md#primary-expressions)) e *unary_expression* ([operatori unari](expressions.md#unary-operators)) consentono i seguenti costrutti aggiuntivi:</span><span class="sxs-lookup"><span data-stu-id="a8492-244">In an unsafe context, the *primary_no_array_creation_expression* ([Primary expressions](expressions.md#primary-expressions)) and *unary_expression* ([Unary operators](expressions.md#unary-operators)) productions permit the following additional constructs:</span></span>

```antlr
primary_no_array_creation_expression_unsafe
    : pointer_member_access
    | pointer_element_access
    | sizeof_expression
    ;

unary_expression_unsafe
    : pointer_indirection_expression
    | addressof_expression
    ;
```

<span data-ttu-id="a8492-245">Questi costrutti sono descritti nelle sezioni riportate di seguito.</span><span class="sxs-lookup"><span data-stu-id="a8492-245">These constructs are described in the following sections.</span></span> <span data-ttu-id="a8492-246">La precedenza e l'associatività degli operatori unsafe sono implicite nella grammatica.</span><span class="sxs-lookup"><span data-stu-id="a8492-246">The precedence and associativity of the unsafe operators is implied by the grammar.</span></span>

### <a name="pointer-indirection"></a><span data-ttu-id="a8492-247">Riferimento indiretto a puntatore</span><span class="sxs-lookup"><span data-stu-id="a8492-247">Pointer indirection</span></span>

<span data-ttu-id="a8492-248">Un *pointer_indirection_expression* è costituito da un asterisco ( `*` ) seguito da un *unary_expression*.</span><span class="sxs-lookup"><span data-stu-id="a8492-248">A *pointer_indirection_expression* consists of an asterisk (`*`) followed by a *unary_expression*.</span></span>

```antlr
pointer_indirection_expression
    : '*' unary_expression
    ;
```

<span data-ttu-id="a8492-249">L'operatore unario `*` denota il riferimento indiretto del puntatore e viene usato per ottenere la variabile a cui punta un puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-249">The unary `*` operator denotes pointer indirection and is used to obtain the variable to which a pointer points.</span></span> <span data-ttu-id="a8492-250">Il risultato della valutazione di `*P` , dove `P` è un'espressione di un tipo di puntatore `T*` , è una variabile di tipo `T` .</span><span class="sxs-lookup"><span data-stu-id="a8492-250">The result of evaluating `*P`, where `P` is an expression of a pointer type `T*`, is a variable of type `T`.</span></span> <span data-ttu-id="a8492-251">Si tratta di un errore in fase di compilazione per applicare l'operatore unario `*` a un'espressione di tipo `void*` o a un'espressione che non è di tipo puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-251">It is a compile-time error to apply the unary `*` operator to an expression of type `void*` or to an expression that isn't of a pointer type.</span></span>

<span data-ttu-id="a8492-252">L'effetto dell'applicazione dell'operatore unario `*` a un `null` puntatore è definito dall'implementazione.</span><span class="sxs-lookup"><span data-stu-id="a8492-252">The effect of applying the unary `*` operator to a `null` pointer is implementation-defined.</span></span> <span data-ttu-id="a8492-253">In particolare, non esiste alcuna garanzia che questa operazione generi un'eccezione `System.NullReferenceException` .</span><span class="sxs-lookup"><span data-stu-id="a8492-253">In particular, there is no guarantee that this operation throws a `System.NullReferenceException`.</span></span>

<span data-ttu-id="a8492-254">Se al puntatore è stato assegnato un valore non valido, il comportamento dell'operatore unario non `*` è definito.</span><span class="sxs-lookup"><span data-stu-id="a8492-254">If an invalid value has been assigned to the pointer, the behavior of the unary `*` operator is undefined.</span></span> <span data-ttu-id="a8492-255">Tra i valori non validi per la dereferenziazione di un puntatore dall'operatore unario `*` si intende un indirizzo allineato in modo non appropriato per il tipo a cui punta (vedere l'esempio nelle [conversioni dei puntatori](unsafe-code.md#pointer-conversions)) e l'indirizzo di una variabile dopo la fine del relativo ciclo di vita.</span><span class="sxs-lookup"><span data-stu-id="a8492-255">Among the invalid values for dereferencing a pointer by the unary `*` operator are an address inappropriately aligned for the type pointed to (see example in [Pointer conversions](unsafe-code.md#pointer-conversions)), and the address of a variable after the end of its lifetime.</span></span>

<span data-ttu-id="a8492-256">Ai fini dell'analisi di assegnazione definita, una variabile prodotta dalla valutazione di un'espressione del form `*P` viene considerata inizialmente assegnata (le[variabili assegnate inizialmente](variables.md#initially-assigned-variables)).</span><span class="sxs-lookup"><span data-stu-id="a8492-256">For purposes of definite assignment analysis, a variable produced by evaluating an expression of the form `*P` is considered initially assigned ([Initially assigned variables](variables.md#initially-assigned-variables)).</span></span>

### <a name="pointer-member-access"></a><span data-ttu-id="a8492-257">Accesso ai membri del puntatore</span><span class="sxs-lookup"><span data-stu-id="a8492-257">Pointer member access</span></span>

<span data-ttu-id="a8492-258">Un *pointer_member_access* è costituito da un *primary_expression*, seguito da un `->` token "", seguito da un *identificatore* e da un *type_argument_list*facoltativo.</span><span class="sxs-lookup"><span data-stu-id="a8492-258">A *pointer_member_access* consists of a *primary_expression*, followed by a "`->`" token, followed by an *identifier* and an optional *type_argument_list*.</span></span>

```antlr
pointer_member_access
    : primary_expression '->' identifier
    ;
```

<span data-ttu-id="a8492-259">In un accesso ai membri di un puntatore del form `P->I` , `P` deve essere un'espressione di un tipo di puntatore diverso da `void*` e `I` deve indicare un membro accessibile del tipo a cui `P` punta.</span><span class="sxs-lookup"><span data-stu-id="a8492-259">In a pointer member access of the form `P->I`, `P` must be an expression of a pointer type other than `void*`, and `I` must denote an accessible member of the type to which `P` points.</span></span>

<span data-ttu-id="a8492-260">Un accesso al membro del puntatore del form `P->I` viene valutato esattamente come `(*P).I` .</span><span class="sxs-lookup"><span data-stu-id="a8492-260">A pointer member access of the form `P->I` is evaluated exactly as `(*P).I`.</span></span> <span data-ttu-id="a8492-261">Per una descrizione dell'operatore di riferimento indiretto del puntatore ( `*` ), vedere riferimento [indiretto del puntatore](unsafe-code.md#pointer-indirection).</span><span class="sxs-lookup"><span data-stu-id="a8492-261">For a description of the pointer indirection operator (`*`), see [Pointer indirection](unsafe-code.md#pointer-indirection).</span></span> <span data-ttu-id="a8492-262">Per una descrizione dell'operatore di accesso ai membri ( `.` ), vedere [accesso ai membri](expressions.md#member-access).</span><span class="sxs-lookup"><span data-stu-id="a8492-262">For a description of the member access operator (`.`), see [Member access](expressions.md#member-access).</span></span>

<span data-ttu-id="a8492-263">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="a8492-263">In the example</span></span>

```csharp
using System;

struct Point
{
    public int x;
    public int y;

    public override string ToString() {
        return "(" + x + "," + y + ")";
    }
}

class Test
{
    static void Main() {
        Point point;
        unsafe {
            Point* p = &point;
            p->x = 10;
            p->y = 20;
            Console.WriteLine(p->ToString());
        }
    }
}
```

<span data-ttu-id="a8492-264">l' `->` operatore viene usato per accedere ai campi e richiamare un metodo di uno struct tramite un puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-264">the `->` operator is used to access fields and invoke a method of a struct through a pointer.</span></span> <span data-ttu-id="a8492-265">Poiché l'operazione `P->I` è esattamente equivalente a `(*P).I` , è `Main` possibile che sia stato scritto anche il metodo:</span><span class="sxs-lookup"><span data-stu-id="a8492-265">Because the operation `P->I` is precisely equivalent to `(*P).I`, the `Main` method could equally well have been written:</span></span>

```csharp
class Test
{
    static void Main() {
        Point point;
        unsafe {
            Point* p = &point;
            (*p).x = 10;
            (*p).y = 20;
            Console.WriteLine((*p).ToString());
        }
    }
}
```

### <a name="pointer-element-access"></a><span data-ttu-id="a8492-266">Accesso agli elementi del puntatore</span><span class="sxs-lookup"><span data-stu-id="a8492-266">Pointer element access</span></span>

<span data-ttu-id="a8492-267">Un *pointer_element_access* è costituito da un *primary_no_array_creation_expression* seguito da un'espressione racchiusa tra " `[` " e " `]` ".</span><span class="sxs-lookup"><span data-stu-id="a8492-267">A *pointer_element_access* consists of a *primary_no_array_creation_expression* followed by an expression enclosed in "`[`" and "`]`".</span></span>

```antlr
pointer_element_access
    : primary_no_array_creation_expression '[' expression ']'
    ;
```

<span data-ttu-id="a8492-268">Nell'accesso a un elemento puntatore del form `P[E]` , `P` deve essere un'espressione di un tipo di puntatore diverso da `void*` e `E` deve essere un'espressione che può essere convertita in modo implicito in `int` , `uint` , `long` o `ulong` .</span><span class="sxs-lookup"><span data-stu-id="a8492-268">In a pointer element access of the form `P[E]`, `P` must be an expression of a pointer type other than `void*`, and `E` must be an expression that can be implicitly converted to `int`, `uint`, `long`, or `ulong`.</span></span>

<span data-ttu-id="a8492-269">L'accesso di un elemento puntatore al form `P[E]` viene valutato esattamente come `*(P + E)` .</span><span class="sxs-lookup"><span data-stu-id="a8492-269">A pointer element access of the form `P[E]` is evaluated exactly as `*(P + E)`.</span></span> <span data-ttu-id="a8492-270">Per una descrizione dell'operatore di riferimento indiretto del puntatore ( `*` ), vedere riferimento [indiretto del puntatore](unsafe-code.md#pointer-indirection).</span><span class="sxs-lookup"><span data-stu-id="a8492-270">For a description of the pointer indirection operator (`*`), see [Pointer indirection](unsafe-code.md#pointer-indirection).</span></span> <span data-ttu-id="a8492-271">Per una descrizione dell'operatore di addizione del puntatore ( `+` ), vedere [aritmetica dei puntatori](unsafe-code.md#pointer-arithmetic).</span><span class="sxs-lookup"><span data-stu-id="a8492-271">For a description of the pointer addition operator (`+`), see [Pointer arithmetic](unsafe-code.md#pointer-arithmetic).</span></span>

<span data-ttu-id="a8492-272">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="a8492-272">In the example</span></span>

```csharp
class Test
{
    static void Main() {
        unsafe {
            char* p = stackalloc char[256];
            for (int i = 0; i < 256; i++) p[i] = (char)i;
        }
    }
}
```

<span data-ttu-id="a8492-273">l'accesso a un elemento del puntatore viene usato per inizializzare il buffer di caratteri in un `for` ciclo.</span><span class="sxs-lookup"><span data-stu-id="a8492-273">a pointer element access is used to initialize the character buffer in a `for` loop.</span></span> <span data-ttu-id="a8492-274">Poiché l'operazione `P[E]` è esattamente equivalente a `*(P + E)` , è possibile che sia stata scritta anche l'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="a8492-274">Because the operation `P[E]` is precisely equivalent to `*(P + E)`, the example could equally well have been written:</span></span>

```csharp
class Test
{
    static void Main() {
        unsafe {
            char* p = stackalloc char[256];
            for (int i = 0; i < 256; i++) *(p + i) = (char)i;
        }
    }
}
```

<span data-ttu-id="a8492-275">L'operatore di accesso dell'elemento puntatore non verifica la presenza di errori fuori limite e il comportamento quando si accede a un elemento fuori limite non è definito.</span><span class="sxs-lookup"><span data-stu-id="a8492-275">The pointer element access operator does not check for out-of-bounds errors and the behavior when accessing an out-of-bounds element is undefined.</span></span> <span data-ttu-id="a8492-276">Corrisponde a C e C++.</span><span class="sxs-lookup"><span data-stu-id="a8492-276">This is the same as C and C++.</span></span>

### <a name="the-address-of-operator"></a><span data-ttu-id="a8492-277">Operatore address-of</span><span class="sxs-lookup"><span data-stu-id="a8492-277">The address-of operator</span></span>

<span data-ttu-id="a8492-278">Un *addressof_expression* è costituito da una e commerciale ( `&` ) seguita da una *unary_expression*.</span><span class="sxs-lookup"><span data-stu-id="a8492-278">An *addressof_expression* consists of an ampersand (`&`) followed by a *unary_expression*.</span></span>

```antlr
addressof_expression
    : '&' unary_expression
    ;
```

<span data-ttu-id="a8492-279">Data un'espressione `E` che è di un tipo `T` ed è classificata come variabile fissa ([variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)), il costrutto `&E` Calcola l'indirizzo della variabile fornita da `E` .</span><span class="sxs-lookup"><span data-stu-id="a8492-279">Given an expression `E` which is of a type `T` and is classified as a fixed variable ([Fixed and moveable variables](unsafe-code.md#fixed-and-moveable-variables)), the construct `&E` computes the address of the variable given by `E`.</span></span> <span data-ttu-id="a8492-280">Il tipo del risultato è `T*` ed è classificato come valore.</span><span class="sxs-lookup"><span data-stu-id="a8492-280">The type of the result is `T*` and is classified as a value.</span></span> <span data-ttu-id="a8492-281">Si verifica un errore in fase di compilazione se `E` non viene classificato come una variabile, se `E` è classificato come variabile locale di sola lettura o se `E` denota una variabile mobile.</span><span class="sxs-lookup"><span data-stu-id="a8492-281">A compile-time error occurs if `E` is not classified as a variable, if `E` is classified as a read-only local variable, or if `E` denotes a moveable variable.</span></span> <span data-ttu-id="a8492-282">Nell'ultimo caso, è possibile usare un'istruzione fixed ([istruzione fixed](unsafe-code.md#the-fixed-statement)) per correggere temporaneamente la variabile prima di ottenerne l'indirizzo.</span><span class="sxs-lookup"><span data-stu-id="a8492-282">In the last case, a fixed statement ([The fixed statement](unsafe-code.md#the-fixed-statement)) can be used to temporarily "fix" the variable before obtaining its address.</span></span> <span data-ttu-id="a8492-283">Come indicato nell' [accesso ai membri](expressions.md#member-access), al di fuori di un costruttore di istanza o di un costruttore statico per uno struct o una classe che definisce un `readonly` campo, il campo viene considerato un valore, non una variabile.</span><span class="sxs-lookup"><span data-stu-id="a8492-283">As stated in [Member access](expressions.md#member-access), outside an instance constructor or static constructor for a struct or class that defines a `readonly` field, that field is considered a value, not a variable.</span></span> <span data-ttu-id="a8492-284">Di conseguenza, non è possibile eseguire il relativo indirizzo.</span><span class="sxs-lookup"><span data-stu-id="a8492-284">As such, its address cannot be taken.</span></span> <span data-ttu-id="a8492-285">Analogamente, non è possibile eseguire l'indirizzo di una costante.</span><span class="sxs-lookup"><span data-stu-id="a8492-285">Similarly, the address of a constant cannot be taken.</span></span>

<span data-ttu-id="a8492-286">L' `&` operatore non richiede che il relativo argomento venga assegnato definitivamente, ma dopo un' `&` operazione la variabile a cui viene applicato l'operatore viene considerata definitivamente assegnata nel percorso di esecuzione in cui si verifica l'operazione.</span><span class="sxs-lookup"><span data-stu-id="a8492-286">The `&` operator does not require its argument to be definitely assigned, but following an `&` operation, the variable to which the operator is applied is considered definitely assigned in the execution path in which the operation occurs.</span></span> <span data-ttu-id="a8492-287">È responsabilità del programmatore garantire che la corretta inizializzazione della variabile venga effettivamente eseguita in questa situazione.</span><span class="sxs-lookup"><span data-stu-id="a8492-287">It is the responsibility of the programmer to ensure that correct initialization of the variable actually does take place in this situation.</span></span>

<span data-ttu-id="a8492-288">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="a8492-288">In the example</span></span>

```csharp
using System;

class Test
{
    static void Main() {
        int i;
        unsafe {
            int* p = &i;
            *p = 123;
        }
        Console.WriteLine(i);
    }
}
```

<span data-ttu-id="a8492-289">`i`viene considerato definitivamente assegnato dopo l' `&i` operazione utilizzata per inizializzare `p` .</span><span class="sxs-lookup"><span data-stu-id="a8492-289">`i` is considered definitely assigned following the `&i` operation used to initialize `p`.</span></span> <span data-ttu-id="a8492-290">L'assegnazione a `*p` in effetti Inizializza `i` , ma l'inclusione di questa inizializzazione è responsabilità del programmatore e non si verificherà alcun errore in fase di compilazione se l'assegnazione è stata rimossa.</span><span class="sxs-lookup"><span data-stu-id="a8492-290">The assignment to `*p` in effect initializes `i`, but the inclusion of this initialization is the responsibility of the programmer, and no compile-time error would occur if the assignment was removed.</span></span>

<span data-ttu-id="a8492-291">Le regole di assegnazione definitiva per l' `&` operatore esistono in modo tale che l'inizializzazione ridondante delle variabili locali possa essere evitata.</span><span class="sxs-lookup"><span data-stu-id="a8492-291">The rules of definite assignment for the `&` operator exist such that redundant initialization of local variables can be avoided.</span></span> <span data-ttu-id="a8492-292">Molte API esterne, ad esempio, accettano un puntatore a una struttura compilata dall'API.</span><span class="sxs-lookup"><span data-stu-id="a8492-292">For example, many external APIs take a pointer to a structure which is filled in by the API.</span></span> <span data-ttu-id="a8492-293">Le chiamate a tali API in genere passano l'indirizzo di una variabile di struct locale e senza la regola è necessaria l'inizializzazione ridondante della variabile struct.</span><span class="sxs-lookup"><span data-stu-id="a8492-293">Calls to such APIs typically pass the address of a local struct variable, and without the rule, redundant initialization of the struct variable would be required.</span></span>

### <a name="pointer-increment-and-decrement"></a><span data-ttu-id="a8492-294">Incremento e decremento dei puntatori</span><span class="sxs-lookup"><span data-stu-id="a8492-294">Pointer increment and decrement</span></span>

<span data-ttu-id="a8492-295">In un contesto non sicuro, `++` è possibile applicare gli operatori e (operatori di `--` [incremento e decremento suffisso](expressions.md#postfix-increment-and-decrement-operators) e [operatori di incremento e decremento del prefisso](expressions.md#prefix-increment-and-decrement-operators)) a variabili puntatore di tutti i tipi ad eccezione di `void*` .</span><span class="sxs-lookup"><span data-stu-id="a8492-295">In an unsafe context, the `++` and `--` operators ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators) and [Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)) can be applied to pointer variables of all types except `void*`.</span></span> <span data-ttu-id="a8492-296">Pertanto, per ogni tipo di puntatore `T*` , gli operatori seguenti sono definiti in modo implicito:</span><span class="sxs-lookup"><span data-stu-id="a8492-296">Thus, for every pointer type `T*`, the following operators are implicitly defined:</span></span>

```csharp
T* operator ++(T* x);
T* operator --(T* x);
```

<span data-ttu-id="a8492-297">Gli operatori producono gli stessi risultati `x + 1` rispettivamente di e `x - 1` ([aritmetica del puntatore](unsafe-code.md#pointer-arithmetic)).</span><span class="sxs-lookup"><span data-stu-id="a8492-297">The operators produce the same results as `x + 1` and `x - 1`, respectively ([Pointer arithmetic](unsafe-code.md#pointer-arithmetic)).</span></span> <span data-ttu-id="a8492-298">In altre parole, per una variabile puntatore di tipo `T*` , l' `++` operatore aggiunge `sizeof(T)` all'indirizzo contenuto nella variabile e l' `--` operatore sottrae `sizeof(T)` dall'indirizzo contenuto nella variabile.</span><span class="sxs-lookup"><span data-stu-id="a8492-298">In other words, for a pointer variable of type `T*`, the `++` operator adds `sizeof(T)` to the address contained in the variable, and the `--` operator subtracts `sizeof(T)` from the address contained in the variable.</span></span>

<span data-ttu-id="a8492-299">Se un'operazione di incremento o di decremento di un puntatore causa un overflow del dominio del tipo di puntatore, il risultato è definito dall'implementazione, ma non vengono generate eccezioni.</span><span class="sxs-lookup"><span data-stu-id="a8492-299">If a pointer increment or decrement operation overflows the domain of the pointer type, the result is implementation-defined, but no exceptions are produced.</span></span>

### <a name="pointer-arithmetic"></a><span data-ttu-id="a8492-300">Puntatore aritmetico</span><span class="sxs-lookup"><span data-stu-id="a8492-300">Pointer arithmetic</span></span>

<span data-ttu-id="a8492-301">In un contesto non sicuro, gli `+` `-` operatori e (operatore di[addizione](expressions.md#addition-operator) e [sottrazione](expressions.md#subtraction-operator)) possono essere applicati ai valori di tutti i tipi di puntatore eccetto `void*` .</span><span class="sxs-lookup"><span data-stu-id="a8492-301">In an unsafe context, the `+` and `-` operators ([Addition operator](expressions.md#addition-operator) and [Subtraction operator](expressions.md#subtraction-operator)) can be applied to values of all pointer types except `void*`.</span></span> <span data-ttu-id="a8492-302">Pertanto, per ogni tipo di puntatore `T*` , gli operatori seguenti sono definiti in modo implicito:</span><span class="sxs-lookup"><span data-stu-id="a8492-302">Thus, for every pointer type `T*`, the following operators are implicitly defined:</span></span>

```csharp
T* operator +(T* x, int y);
T* operator +(T* x, uint y);
T* operator +(T* x, long y);
T* operator +(T* x, ulong y);

T* operator +(int x, T* y);
T* operator +(uint x, T* y);
T* operator +(long x, T* y);
T* operator +(ulong x, T* y);

T* operator -(T* x, int y);
T* operator -(T* x, uint y);
T* operator -(T* x, long y);
T* operator -(T* x, ulong y);

long operator -(T* x, T* y);
```

<span data-ttu-id="a8492-303">Data un'espressione `P` di un tipo di puntatore `T*` e un'espressione `N` di tipo `int` , `uint` , `long` o `ulong` , le espressioni `P + N` e `N + P` calcolano il valore del puntatore di tipo `T*` risultante dall'aggiunta `N * sizeof(T)` all'indirizzo fornito da `P` .</span><span class="sxs-lookup"><span data-stu-id="a8492-303">Given an expression `P` of a pointer type `T*` and an expression `N` of type `int`, `uint`, `long`, or `ulong`, the expressions `P + N` and `N + P` compute the pointer value of type `T*` that results from adding `N * sizeof(T)` to the address given by `P`.</span></span> <span data-ttu-id="a8492-304">Analogamente, l'espressione `P - N` Calcola il valore del puntatore di tipo `T*` risultante dalla sottrazione `N * sizeof(T)` dall'indirizzo fornito da `P` .</span><span class="sxs-lookup"><span data-stu-id="a8492-304">Likewise, the expression `P - N` computes the pointer value of type `T*` that results from subtracting `N * sizeof(T)` from the address given by `P`.</span></span>

<span data-ttu-id="a8492-305">Date due espressioni, `P` e `Q` , di un tipo di puntatore `T*` , l'espressione `P - Q` Calcola la differenza tra gli indirizzi specificati da `P` e `Q` e quindi divide la differenza in base a `sizeof(T)` .</span><span class="sxs-lookup"><span data-stu-id="a8492-305">Given two expressions, `P` and `Q`, of a pointer type `T*`, the expression `P - Q` computes the difference between the addresses given by `P` and `Q` and then divides that difference by `sizeof(T)`.</span></span> <span data-ttu-id="a8492-306">Il tipo del risultato è sempre `long` .</span><span class="sxs-lookup"><span data-stu-id="a8492-306">The type of the result is always `long`.</span></span> <span data-ttu-id="a8492-307">In effetti, `P - Q` viene calcolata come `((long)(P) - (long)(Q)) / sizeof(T)` .</span><span class="sxs-lookup"><span data-stu-id="a8492-307">In effect, `P - Q` is computed as `((long)(P) - (long)(Q)) / sizeof(T)`.</span></span>

<span data-ttu-id="a8492-308">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a8492-308">For example:</span></span>

```csharp
using System;

class Test
{
    static void Main() {
        unsafe {
            int* values = stackalloc int[20];
            int* p = &values[1];
            int* q = &values[15];
            Console.WriteLine("p - q = {0}", p - q);
            Console.WriteLine("q - p = {0}", q - p);
        }
    }
}
```

<span data-ttu-id="a8492-309">che produce l'output:</span><span class="sxs-lookup"><span data-stu-id="a8492-309">which produces the output:</span></span>

```console
p - q = -14
q - p = 14
```

<span data-ttu-id="a8492-310">Se un'operazione aritmetica del puntatore causa un overflow del dominio del tipo di puntatore, il risultato viene troncato in modo definito dall'implementazione, ma non vengono generate eccezioni.</span><span class="sxs-lookup"><span data-stu-id="a8492-310">If a pointer arithmetic operation overflows the domain of the pointer type, the result is truncated in an implementation-defined fashion, but no exceptions are produced.</span></span>

### <a name="pointer-comparison"></a><span data-ttu-id="a8492-311">Confronto tra puntatori</span><span class="sxs-lookup"><span data-stu-id="a8492-311">Pointer comparison</span></span>

<span data-ttu-id="a8492-312">In un contesto non sicuro `==` `!=` `<` `>` è possibile applicare gli operatori,,,, `<=` e `=>` ([operatori relazionali e di test dei tipi](expressions.md#relational-and-type-testing-operators)) a valori di tutti i tipi di puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-312">In an unsafe context, the `==`, `!=`, `<`, `>`, `<=`, and `=>` operators ([Relational and type-testing operators](expressions.md#relational-and-type-testing-operators)) can be applied to values of all pointer types.</span></span> <span data-ttu-id="a8492-313">Gli operatori di confronto del puntatore sono:</span><span class="sxs-lookup"><span data-stu-id="a8492-313">The pointer comparison operators are:</span></span>

```csharp
bool operator ==(void* x, void* y);
bool operator !=(void* x, void* y);
bool operator <(void* x, void* y);
bool operator >(void* x, void* y);
bool operator <=(void* x, void* y);
bool operator >=(void* x, void* y);
```

<span data-ttu-id="a8492-314">Poiché esiste una conversione implicita da qualsiasi tipo di puntatore al `void*` tipo, gli operandi di qualsiasi tipo di puntatore possono essere confrontati usando questi operatori.</span><span class="sxs-lookup"><span data-stu-id="a8492-314">Because an implicit conversion exists from any pointer type to the `void*` type, operands of any pointer type can be compared using these operators.</span></span> <span data-ttu-id="a8492-315">Gli operatori di confronto confrontano gli indirizzi forniti dai due operandi come se fossero interi senza segno.</span><span class="sxs-lookup"><span data-stu-id="a8492-315">The comparison operators compare the addresses given by the two operands as if they were unsigned integers.</span></span>

### <a name="the-sizeof-operator"></a><span data-ttu-id="a8492-316">Operatore sizeof</span><span class="sxs-lookup"><span data-stu-id="a8492-316">The sizeof operator</span></span>

<span data-ttu-id="a8492-317">L'operatore `sizeof` restituisce il numero di byte occupati da una variabile di un tipo specificato.</span><span class="sxs-lookup"><span data-stu-id="a8492-317">The `sizeof` operator returns the number of bytes occupied by a variable of a given type.</span></span> <span data-ttu-id="a8492-318">Il tipo specificato come operando `sizeof` deve essere un *unmanaged_type* ([tipi puntatore](unsafe-code.md#pointer-types)).</span><span class="sxs-lookup"><span data-stu-id="a8492-318">The type specified as an operand to `sizeof` must be an *unmanaged_type* ([Pointer types](unsafe-code.md#pointer-types)).</span></span>

```antlr
sizeof_expression
    : 'sizeof' '(' unmanaged_type ')'
    ;
```

<span data-ttu-id="a8492-319">Il risultato dell' `sizeof` operatore è un valore di tipo `int` .</span><span class="sxs-lookup"><span data-stu-id="a8492-319">The result of the `sizeof` operator is a value of type `int`.</span></span> <span data-ttu-id="a8492-320">Per determinati tipi predefiniti, l' `sizeof` operatore restituisce un valore costante, come illustrato nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="a8492-320">For certain predefined types, the `sizeof` operator yields a constant value as shown in the table below.</span></span>


| <span data-ttu-id="a8492-321">__Espressione__</span><span class="sxs-lookup"><span data-stu-id="a8492-321">__Expression__</span></span>   | <span data-ttu-id="a8492-322">__Risultato__</span><span class="sxs-lookup"><span data-stu-id="a8492-322">__Result__</span></span> |
|------------------|------------|
| `sizeof(sbyte)`  | `1`        |
| `sizeof(byte)`   | `1`        |
| `sizeof(short)`  | `2`        |
| `sizeof(ushort)` | `2`        |
| `sizeof(int)`    | `4`        |
| `sizeof(uint)`   | `4`        |
| `sizeof(long)`   | `8`        |
| `sizeof(ulong)`  | `8`        |
| `sizeof(char)`   | `2`        |
| `sizeof(float)`  | `4`        |
| `sizeof(double)` | `8`        |
| `sizeof(bool)`   | `1`        |

<span data-ttu-id="a8492-323">Per tutti gli altri tipi, il risultato dell' `sizeof` operatore è definito dall'implementazione ed è classificato come valore, non come costante.</span><span class="sxs-lookup"><span data-stu-id="a8492-323">For all other types, the result of the `sizeof` operator is implementation-defined and is classified as a value, not a constant.</span></span>

<span data-ttu-id="a8492-324">L'ordine in cui i membri vengono compressi in uno struct non è specificato.</span><span class="sxs-lookup"><span data-stu-id="a8492-324">The order in which members are packed into a struct is unspecified.</span></span>

<span data-ttu-id="a8492-325">Ai fini dell'allineamento, potrebbe essere presente una spaziatura senza nome all'inizio di uno struct, all'interno di uno struct e alla fine dello struct.</span><span class="sxs-lookup"><span data-stu-id="a8492-325">For alignment purposes, there may be unnamed padding at the beginning of a struct, within a struct, and at the end of the struct.</span></span> <span data-ttu-id="a8492-326">Il contenuto dei bit utilizzati come spaziatura interna è indeterminato.</span><span class="sxs-lookup"><span data-stu-id="a8492-326">The contents of the bits used as padding are indeterminate.</span></span>

<span data-ttu-id="a8492-327">Quando viene applicato a un operando con tipo struct, il risultato è il numero totale di byte in una variabile di quel tipo, inclusa la spaziatura interna.</span><span class="sxs-lookup"><span data-stu-id="a8492-327">When applied to an operand that has struct type, the result is the total number of bytes in a variable of that type, including any padding.</span></span>

## <a name="the-fixed-statement"></a><span data-ttu-id="a8492-328">Istruzione fixed</span><span class="sxs-lookup"><span data-stu-id="a8492-328">The fixed statement</span></span>

<span data-ttu-id="a8492-329">In un contesto non sicuro, la produzione di *embedded_statement* ([istruzioni](statements.md)) consente un costrutto aggiuntivo, `fixed` ovvero l'istruzione, che viene usata per "correggere" una variabile mobile in modo che l'indirizzo rimanga costante per la durata dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="a8492-329">In an unsafe context, the *embedded_statement* ([Statements](statements.md)) production permits an additional construct, the `fixed` statement, which is used to "fix" a moveable variable such that its address remains constant for the duration of the statement.</span></span>

```antlr
fixed_statement
    : 'fixed' '(' pointer_type fixed_pointer_declarators ')' embedded_statement
    ;

fixed_pointer_declarators
    : fixed_pointer_declarator (','  fixed_pointer_declarator)*
    ;

fixed_pointer_declarator
    : identifier '=' fixed_pointer_initializer
    ;

fixed_pointer_initializer
    : '&' variable_reference
    | expression
    ;
```

<span data-ttu-id="a8492-330">Ogni *fixed_pointer_declarator* dichiara una variabile locale della *pointer_type* specificata e inizializza la variabile locale con l'indirizzo calcolato dalla *fixed_pointer_initializer*corrispondente.</span><span class="sxs-lookup"><span data-stu-id="a8492-330">Each *fixed_pointer_declarator* declares a local variable of the given *pointer_type* and initializes that local variable with the address computed by the corresponding *fixed_pointer_initializer*.</span></span> <span data-ttu-id="a8492-331">Una variabile locale dichiarata in un' `fixed` istruzione è accessibile in qualsiasi *fixed_pointer_initializer*s che si verifica a destra della dichiarazione della variabile e nella *embedded_statement* dell' `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="a8492-331">A local variable declared in a `fixed` statement is accessible in any *fixed_pointer_initializer*s occurring to the right of that variable's declaration, and in the *embedded_statement* of the `fixed` statement.</span></span> <span data-ttu-id="a8492-332">Una variabile locale dichiarata da un' `fixed` istruzione è considerata di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="a8492-332">A local variable declared by a `fixed` statement is considered read-only.</span></span> <span data-ttu-id="a8492-333">Si verifica un errore in fase di compilazione se l'istruzione incorporata tenta di modificare questa variabile locale (tramite assegnazione o gli `++` `--` operatori e) o di passarla come `ref` `out` parametro o.</span><span class="sxs-lookup"><span data-stu-id="a8492-333">A compile-time error occurs if the embedded statement attempts to modify this local variable (via assignment or the `++` and `--` operators) or pass it as a `ref` or `out` parameter.</span></span>

<span data-ttu-id="a8492-334">Un *fixed_pointer_initializer* può essere uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="a8492-334">A *fixed_pointer_initializer* can be one of the following:</span></span>

*  <span data-ttu-id="a8492-335">Il token " `&` " seguito da un *variable_reference* ([regole precise per determinare l'assegnazione definita](variables.md#precise-rules-for-determining-definite-assignment)) a una variabile mobile ([variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)) di un tipo non gestito `T` , purché il tipo `T*` sia convertibile in modo implicito nel tipo di puntatore specificato nell' `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="a8492-335">The token "`&`" followed by a *variable_reference* ([Precise rules for determining definite assignment](variables.md#precise-rules-for-determining-definite-assignment)) to a moveable variable ([Fixed and moveable variables](unsafe-code.md#fixed-and-moveable-variables)) of an unmanaged type `T`, provided the type `T*` is implicitly convertible to the pointer type given in the `fixed` statement.</span></span> <span data-ttu-id="a8492-336">In questo caso, l'inizializzatore calcola l'indirizzo della variabile specificata e la variabile rimane in un indirizzo fisso per la durata dell' `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="a8492-336">In this case, the initializer computes the address of the given variable, and the variable is guaranteed to remain at a fixed address for the duration of the `fixed` statement.</span></span>
*  <span data-ttu-id="a8492-337">Espressione di un *array_type* con gli elementi di un tipo non gestito `T` , purché il tipo `T*` sia convertibile in modo implicito nel tipo di puntatore specificato nell' `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="a8492-337">An expression of an *array_type* with elements of an unmanaged type `T`, provided the type `T*` is implicitly convertible to the pointer type given in the `fixed` statement.</span></span> <span data-ttu-id="a8492-338">In questo caso, l'inizializzatore calcola l'indirizzo del primo elemento nella matrice e l'intera matrice rimane in un indirizzo fisso per la durata dell' `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="a8492-338">In this case, the initializer computes the address of the first element in the array, and the entire array is guaranteed to remain at a fixed address for the duration of the `fixed` statement.</span></span> <span data-ttu-id="a8492-339">Se l'espressione di matrice è null o se la matrice ha zero elementi, l'inizializzatore calcola un indirizzo uguale a zero.</span><span class="sxs-lookup"><span data-stu-id="a8492-339">If the array expression is null or if the array has zero elements, the initializer computes an address equal to zero.</span></span>
*  <span data-ttu-id="a8492-340">Espressione di tipo `string` , purché il tipo `char*` sia convertibile in modo implicito nel tipo di puntatore specificato nell' `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="a8492-340">An expression of type `string`, provided the type `char*` is implicitly convertible to the pointer type given in the `fixed` statement.</span></span> <span data-ttu-id="a8492-341">In questo caso, l'inizializzatore calcola l'indirizzo del primo carattere nella stringa e l'intera stringa rimane in un indirizzo fisso per la durata dell' `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="a8492-341">In this case, the initializer computes the address of the first character in the string, and the entire string is guaranteed to remain at a fixed address for the duration of the `fixed` statement.</span></span> <span data-ttu-id="a8492-342">Il comportamento dell' `fixed` istruzione è definito dall'implementazione se l'espressione stringa è null.</span><span class="sxs-lookup"><span data-stu-id="a8492-342">The behavior of the `fixed` statement is implementation-defined if the string expression is null.</span></span>
*  <span data-ttu-id="a8492-343">*Simple_name* o *member_access* che fa riferimento a un membro del buffer a dimensione fissa di una variabile mobile, purché il tipo del membro del buffer a dimensione fissa sia convertibile in modo implicito nel tipo di puntatore specificato nell' `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="a8492-343">A *simple_name* or *member_access* that references a fixed size buffer member of a moveable variable, provided the type of the fixed size buffer member is implicitly convertible to the pointer type given in the `fixed` statement.</span></span> <span data-ttu-id="a8492-344">In questo caso, l'inizializzatore calcola un puntatore al primo elemento del buffer a dimensione fissa ([buffer a dimensione fissa nelle espressioni](unsafe-code.md#fixed-size-buffers-in-expressions)) e il buffer a dimensione fissa rimane sempre in corrispondenza di un indirizzo fisso per la durata dell' `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="a8492-344">In this case, the initializer computes a pointer to the first element of the fixed size buffer ([Fixed size buffers in expressions](unsafe-code.md#fixed-size-buffers-in-expressions)), and the fixed size buffer is guaranteed to remain at a fixed address for the duration of the `fixed` statement.</span></span>

<span data-ttu-id="a8492-345">Per ogni indirizzo calcolato da un *fixed_pointer_initializer* l' `fixed` istruzione garantisce che la variabile a cui fa riferimento l'indirizzo non sia soggetta alla rilocazione o all'eliminazione da parte del Garbage Collector per la durata dell' `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="a8492-345">For each address computed by a *fixed_pointer_initializer* the `fixed` statement ensures that the variable referenced by the address is not subject to relocation or disposal by the garbage collector for the duration of the `fixed` statement.</span></span> <span data-ttu-id="a8492-346">Se, ad esempio, l'indirizzo calcolato da un *fixed_pointer_initializer* fa riferimento a un campo di un oggetto o di un elemento di un'istanza di matrice, l' `fixed` istruzione garantisce che l'istanza dell'oggetto contenitore non venga rilocata o eliminata durante la durata dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="a8492-346">For example, if the address computed by a *fixed_pointer_initializer* references a field of an object or an element of an array instance, the `fixed` statement guarantees that the containing object instance is not relocated or disposed of during the lifetime of the statement.</span></span>

<span data-ttu-id="a8492-347">È responsabilità del programmatore garantire che i puntatori creati dalle `fixed` istruzioni non sopravvivano oltre l'esecuzione di tali istruzioni.</span><span class="sxs-lookup"><span data-stu-id="a8492-347">It is the programmer's responsibility to ensure that pointers created by `fixed` statements do not survive beyond execution of those statements.</span></span> <span data-ttu-id="a8492-348">Se, ad esempio, i puntatori creati dalle `fixed` istruzioni vengono passati alle API esterne, è responsabilità del programmatore garantire che le API non mantengano memoria di questi puntatori.</span><span class="sxs-lookup"><span data-stu-id="a8492-348">For example, when pointers created by `fixed` statements are passed to external APIs, it is the programmer's responsibility to ensure that the APIs retain no memory of these pointers.</span></span>

<span data-ttu-id="a8492-349">Gli oggetti fissi possono causare la frammentazione dell'heap, perché non possono essere spostati.</span><span class="sxs-lookup"><span data-stu-id="a8492-349">Fixed objects may cause fragmentation of the heap (because they can't be moved).</span></span> <span data-ttu-id="a8492-350">Per questo motivo, gli oggetti devono essere corretti solo quando sono assolutamente necessari e quindi solo per la quantità di tempo più breve possibile.</span><span class="sxs-lookup"><span data-stu-id="a8492-350">For that reason, objects should be fixed only when absolutely necessary and then only for the shortest amount of time possible.</span></span>

<span data-ttu-id="a8492-351">L'esempio:</span><span class="sxs-lookup"><span data-stu-id="a8492-351">The example</span></span>

```csharp
class Test
{
    static int x;
    int y;

    unsafe static void F(int* p) {
        *p = 1;
    }

    static void Main() {
        Test t = new Test();
        int[] a = new int[10];
        unsafe {
            fixed (int* p = &x) F(p);
            fixed (int* p = &t.y) F(p);
            fixed (int* p = &a[0]) F(p);
            fixed (int* p = a) F(p);
        }
    }
}
```

<span data-ttu-id="a8492-352">vengono illustrati diversi utilizzi dell' `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="a8492-352">demonstrates several uses of the `fixed` statement.</span></span> <span data-ttu-id="a8492-353">La prima istruzione corregge e ottiene l'indirizzo di un campo statico, la seconda corregge e ottiene l'indirizzo di un campo di istanza e la terza istruzione corregge e ottiene l'indirizzo di un elemento di matrice.</span><span class="sxs-lookup"><span data-stu-id="a8492-353">The first statement fixes and obtains the address of a static field, the second statement fixes and obtains the address of an instance field, and the third statement fixes and obtains the address of an array element.</span></span> <span data-ttu-id="a8492-354">In ogni caso sarebbe stato un errore usare l' `&` operatore regolare poiché le variabili sono tutte classificate come variabili mobili.</span><span class="sxs-lookup"><span data-stu-id="a8492-354">In each case it would have been an error to use the regular `&` operator since the variables are all classified as moveable variables.</span></span>

<span data-ttu-id="a8492-355">La quarta `fixed` istruzione nell'esempio precedente produce un risultato simile al terzo.</span><span class="sxs-lookup"><span data-stu-id="a8492-355">The fourth `fixed` statement in the example above produces a similar result to the third.</span></span>

<span data-ttu-id="a8492-356">Questo esempio di `fixed` istruzione USA `string` :</span><span class="sxs-lookup"><span data-stu-id="a8492-356">This example of the `fixed` statement uses `string`:</span></span>

```csharp
class Test
{
    static string name = "xx";

    unsafe static void F(char* p) {
        for (int i = 0; p[i] != '\0'; ++i)
            Console.WriteLine(p[i]);
    }

    static void Main() {
        unsafe {
            fixed (char* p = name) F(p);
            fixed (char* p = "xx") F(p);
        }
    }
}
```

<span data-ttu-id="a8492-357">In una matrice di contesto unsafe gli elementi delle matrici unidimensionali vengono archiviati in ordine di indice crescente, iniziando con index `0` e terminando con index `Length - 1` .</span><span class="sxs-lookup"><span data-stu-id="a8492-357">In an unsafe context array elements of single-dimensional arrays are stored in increasing index order, starting with index `0` and ending with index `Length - 1`.</span></span> <span data-ttu-id="a8492-358">Per le matrici multidimensionali, gli elementi di matrice vengono archiviati in modo che gli indici della dimensione più a destra vengano aumentati per primi, quindi la dimensione a sinistra successiva e così via verso sinistra.</span><span class="sxs-lookup"><span data-stu-id="a8492-358">For multi-dimensional arrays, array elements are stored such that the indices of the rightmost dimension are increased first, then the next left dimension, and so on to the left.</span></span> <span data-ttu-id="a8492-359">All'interno di un' `fixed` istruzione che ottiene un puntatore `p` a un'istanza di matrice `a` , i valori del puntatore compresi tra `p` per `p + a.Length - 1` rappresentare gli indirizzi degli elementi nella matrice.</span><span class="sxs-lookup"><span data-stu-id="a8492-359">Within a `fixed` statement that obtains a pointer `p` to an array instance `a`, the pointer values ranging from `p` to `p + a.Length - 1` represent addresses of the elements in the array.</span></span> <span data-ttu-id="a8492-360">Analogamente, le variabili che variano da `p[0]` per `p[a.Length - 1]` rappresentare gli elementi della matrice effettivi.</span><span class="sxs-lookup"><span data-stu-id="a8492-360">Likewise, the variables ranging from `p[0]` to `p[a.Length - 1]` represent the actual array elements.</span></span> <span data-ttu-id="a8492-361">Dato il modo in cui vengono archiviate le matrici, è possibile considerare una matrice di qualsiasi dimensione come se fosse lineare.</span><span class="sxs-lookup"><span data-stu-id="a8492-361">Given the way in which arrays are stored, we can treat an array of any dimension as though it were linear.</span></span>

<span data-ttu-id="a8492-362">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a8492-362">For example:</span></span>

```csharp
using System;

class Test
{
    static void Main() {
        int[,,] a = new int[2,3,4];
        unsafe {
            fixed (int* p = a) {
                for (int i = 0; i < a.Length; ++i)    // treat as linear
                    p[i] = i;
            }
        }

        for (int i = 0; i < 2; ++i)
            for (int j = 0; j < 3; ++j) {
                for (int k = 0; k < 4; ++k)
                    Console.Write("[{0},{1},{2}] = {3,2} ", i, j, k, a[i,j,k]);
                Console.WriteLine();
            }
    }
}
```

<span data-ttu-id="a8492-363">che produce l'output:</span><span class="sxs-lookup"><span data-stu-id="a8492-363">which produces the output:</span></span>

```console
[0,0,0] =  0 [0,0,1] =  1 [0,0,2] =  2 [0,0,3] =  3
[0,1,0] =  4 [0,1,1] =  5 [0,1,2] =  6 [0,1,3] =  7
[0,2,0] =  8 [0,2,1] =  9 [0,2,2] = 10 [0,2,3] = 11
[1,0,0] = 12 [1,0,1] = 13 [1,0,2] = 14 [1,0,3] = 15
[1,1,0] = 16 [1,1,1] = 17 [1,1,2] = 18 [1,1,3] = 19
[1,2,0] = 20 [1,2,1] = 21 [1,2,2] = 22 [1,2,3] = 23
```

<span data-ttu-id="a8492-364">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="a8492-364">In the example</span></span>

```csharp
class Test
{
    unsafe static void Fill(int* p, int count, int value) {
        for (; count != 0; count--) *p++ = value;
    }

    static void Main() {
        int[] a = new int[100];
        unsafe {
            fixed (int* p = a) Fill(p, 100, -1);
        }
    }
}
```

<span data-ttu-id="a8492-365">`fixed`viene utilizzata un'istruzione per correggere una matrice in modo che l'indirizzo possa essere passato a un metodo che accetta un puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-365">a `fixed` statement is used to fix an array so its address can be passed to a method that takes a pointer.</span></span>

<span data-ttu-id="a8492-366">Nell’esempio:</span><span class="sxs-lookup"><span data-stu-id="a8492-366">In the example:</span></span>

```csharp
unsafe struct Font
{
    public int size;
    public fixed char name[32];
}

class Test
{
    unsafe static void PutString(string s, char* buffer, int bufSize) {
        int len = s.Length;
        if (len > bufSize) len = bufSize;
        for (int i = 0; i < len; i++) buffer[i] = s[i];
        for (int i = len; i < bufSize; i++) buffer[i] = (char)0;
    }

    Font f;

    unsafe static void Main()
    {
        Test test = new Test();
        test.f.size = 10;
        fixed (char* p = test.f.name) {
            PutString("Times New Roman", p, 32);
        }
    }
}
```

<span data-ttu-id="a8492-367">viene utilizzata un'istruzione fixed per correggere un buffer a dimensione fissa di uno struct, in modo che l'indirizzo possa essere utilizzato come puntatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-367">a fixed statement is used to fix a fixed size buffer of a struct so its address can be used as a pointer.</span></span>

<span data-ttu-id="a8492-368">Un `char*` valore prodotto mediante la correzione di un'istanza di stringa fa sempre riferimento a una stringa con terminazione null.</span><span class="sxs-lookup"><span data-stu-id="a8492-368">A `char*` value produced by fixing a string instance always points to a null-terminated string.</span></span> <span data-ttu-id="a8492-369">All'interno di un'istruzione fixed che ottiene un puntatore `p` a un'istanza di stringa `s` , i valori del puntatore che variano da `p` per `p + s.Length - 1` rappresentare gli indirizzi dei caratteri nella stringa e il valore del puntatore `p + s.Length` punta sempre a un carattere null (il carattere con valore `'\0'` ).</span><span class="sxs-lookup"><span data-stu-id="a8492-369">Within a fixed statement that obtains a pointer `p` to a string instance `s`, the pointer values ranging from `p` to `p + s.Length - 1` represent addresses of the characters in the string, and the pointer value `p + s.Length` always points to a null character (the character with value `'\0'`).</span></span>

<span data-ttu-id="a8492-370">La modifica di oggetti di tipo gestito tramite puntatori fissi può comportare un comportamento non definito.</span><span class="sxs-lookup"><span data-stu-id="a8492-370">Modifying objects of managed type through fixed pointers can results in undefined behavior.</span></span> <span data-ttu-id="a8492-371">Poiché, ad esempio, le stringhe non sono modificabili, è responsabilità del programmatore garantire che i caratteri a cui fa riferimento un puntatore a una stringa fissa non vengano modificati.</span><span class="sxs-lookup"><span data-stu-id="a8492-371">For example, because strings are immutable, it is the programmer's responsibility to ensure that the characters referenced by a pointer to a fixed string are not modified.</span></span>

<span data-ttu-id="a8492-372">La terminazione null automatica delle stringhe è particolarmente utile quando si chiamano API esterne che prevedono stringhe di tipo "C".</span><span class="sxs-lookup"><span data-stu-id="a8492-372">The automatic null-termination of strings is particularly convenient when calling external APIs that expect "C-style" strings.</span></span> <span data-ttu-id="a8492-373">Si noti, tuttavia, che un'istanza di stringa può contenere caratteri null.</span><span class="sxs-lookup"><span data-stu-id="a8492-373">Note, however, that a string instance is permitted to contain null characters.</span></span> <span data-ttu-id="a8492-374">Se sono presenti caratteri null, la stringa viene visualizzata troncata quando viene considerata come terminazione null `char*` .</span><span class="sxs-lookup"><span data-stu-id="a8492-374">If such null characters are present, the string will appear truncated when treated as a null-terminated `char*`.</span></span>

## <a name="fixed-size-buffers"></a><span data-ttu-id="a8492-375">Buffer a dimensione fissa</span><span class="sxs-lookup"><span data-stu-id="a8492-375">Fixed size buffers</span></span>

<span data-ttu-id="a8492-376">I buffer a dimensione fissa vengono usati per dichiarare le matrici inline "in stile C" come membri degli struct e sono particolarmente utili per l'interazione con le API non gestite.</span><span class="sxs-lookup"><span data-stu-id="a8492-376">Fixed size buffers are used to declare "C style" in-line arrays as members of structs, and are primarily useful for interfacing with unmanaged APIs.</span></span>

### <a name="fixed-size-buffer-declarations"></a><span data-ttu-id="a8492-377">Dichiarazioni di buffer a dimensione fissa</span><span class="sxs-lookup"><span data-stu-id="a8492-377">Fixed size buffer declarations</span></span>

<span data-ttu-id="a8492-378">Un ***buffer a dimensione fissa*** è un membro che rappresenta lo spazio di archiviazione per un buffer a lunghezza fissa di variabili di un determinato tipo.</span><span class="sxs-lookup"><span data-stu-id="a8492-378">A ***fixed size buffer*** is a member that represents storage for a fixed length buffer of variables of a given type.</span></span> <span data-ttu-id="a8492-379">Una dichiarazione di buffer a dimensione fissa introduce uno o più buffer a dimensione fissa di un determinato tipo di elemento.</span><span class="sxs-lookup"><span data-stu-id="a8492-379">A fixed size buffer declaration introduces one or more fixed size buffers of a given element type.</span></span> <span data-ttu-id="a8492-380">I buffer a dimensione fissa sono consentiti solo nelle dichiarazioni struct e possono verificarsi solo in contesti non sicuri ([contesti non sicuri](unsafe-code.md#unsafe-contexts)).</span><span class="sxs-lookup"><span data-stu-id="a8492-380">Fixed size buffers are only permitted in struct declarations and can only occur in unsafe contexts ([Unsafe contexts](unsafe-code.md#unsafe-contexts)).</span></span>

```antlr
struct_member_declaration_unsafe
    : fixed_size_buffer_declaration
    ;

fixed_size_buffer_declaration
    : attributes? fixed_size_buffer_modifier* 'fixed' buffer_element_type fixed_size_buffer_declarator+ ';'
    ;

fixed_size_buffer_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | 'unsafe'
    ;

buffer_element_type
    : type
    ;

fixed_size_buffer_declarator
    : identifier '[' constant_expression ']'
    ;
```

<span data-ttu-id="a8492-381">Una dichiarazione di buffer a dimensione fissa può includere un set di attributi ([attributi](attributes.md)), un `new` modificatore ([modificatori](classes.md#modifiers)), una combinazione valida dei quattro modificatori di accesso ([parametri di tipo e vincoli](classes.md#type-parameters-and-constraints)) e un `unsafe` modificatore ([contesti non sicuri](unsafe-code.md#unsafe-contexts)).</span><span class="sxs-lookup"><span data-stu-id="a8492-381">A fixed size buffer declaration may include a set of attributes ([Attributes](attributes.md)), a `new` modifier ([Modifiers](classes.md#modifiers)), a valid combination of the four access modifiers ([Type parameters and constraints](classes.md#type-parameters-and-constraints)) and an `unsafe` modifier ([Unsafe contexts](unsafe-code.md#unsafe-contexts)).</span></span> <span data-ttu-id="a8492-382">Gli attributi e i modificatori si applicano a tutti i membri dichiarati dalla dichiarazione del buffer a dimensione fissa.</span><span class="sxs-lookup"><span data-stu-id="a8492-382">The attributes and modifiers apply to all of the members declared by the fixed size buffer declaration.</span></span> <span data-ttu-id="a8492-383">È un errore che lo stesso modificatore venga visualizzato più volte in una dichiarazione di buffer a dimensione fissa.</span><span class="sxs-lookup"><span data-stu-id="a8492-383">It is an error for the same modifier to appear multiple times in a fixed size buffer declaration.</span></span>

<span data-ttu-id="a8492-384">Una dichiarazione di buffer a dimensione fissa non è consentita per includere il `static` modificatore.</span><span class="sxs-lookup"><span data-stu-id="a8492-384">A fixed size buffer declaration is not permitted to include the `static` modifier.</span></span>

<span data-ttu-id="a8492-385">Il tipo di elemento buffer di una dichiarazione di buffer a dimensione fissa specifica il tipo di elemento dei buffer introdotti dalla dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="a8492-385">The buffer element type of a fixed size buffer declaration specifies the element type of the buffer(s) introduced by the declaration.</span></span> <span data-ttu-id="a8492-386">Il tipo di elemento del buffer deve essere uno dei tipi predefiniti,,,, `sbyte` `byte` `short` `ushort` `int` , `uint` , `long` , `ulong` , `char` , `float` , `double` o `bool` .</span><span class="sxs-lookup"><span data-stu-id="a8492-386">The buffer element type must be one of the predefined types `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, or `bool`.</span></span>

<span data-ttu-id="a8492-387">Il tipo di elemento del buffer è seguito da un elenco di dichiaratori di buffer a dimensione fissa, ciascuno dei quali introduce un nuovo membro.</span><span class="sxs-lookup"><span data-stu-id="a8492-387">The buffer element type is followed by a list of fixed size buffer declarators, each of which introduces a new member.</span></span> <span data-ttu-id="a8492-388">Un dichiaratore di buffer a dimensione fissa è costituito da un identificatore che assegna un nome al membro, seguito da un'espressione costante racchiusa tra `[` `]` token e.</span><span class="sxs-lookup"><span data-stu-id="a8492-388">A fixed size buffer declarator consists of an identifier that names the member, followed by a constant expression enclosed in `[` and `]` tokens.</span></span> <span data-ttu-id="a8492-389">L'espressione costante indica il numero di elementi nel membro introdotto dal dichiaratore di buffer a dimensione fissa.</span><span class="sxs-lookup"><span data-stu-id="a8492-389">The constant expression denotes the number of elements in the member introduced by that fixed size buffer declarator.</span></span> <span data-ttu-id="a8492-390">Il tipo dell'espressione costante deve essere convertibile in modo implicito nel tipo `int` e il valore deve essere un numero intero positivo diverso da zero.</span><span class="sxs-lookup"><span data-stu-id="a8492-390">The type of the constant expression must be implicitly convertible to type `int`, and the value must be a non-zero positive integer.</span></span>

<span data-ttu-id="a8492-391">Gli elementi di un buffer a dimensione fissa sono sicuramente disposti in sequenza in memoria.</span><span class="sxs-lookup"><span data-stu-id="a8492-391">The elements of a fixed size buffer are guaranteed to be laid out sequentially in memory.</span></span>

<span data-ttu-id="a8492-392">Una dichiarazione di buffer a dimensione fissa che dichiara più buffer a dimensione fissa equivale a più dichiarazioni di una singola dichiarazione di buffer a dimensione fissa con gli stessi attributi e tipi di elemento.</span><span class="sxs-lookup"><span data-stu-id="a8492-392">A fixed size buffer declaration that declares multiple fixed size buffers is equivalent to multiple declarations of a single fixed size buffer declaration with the same attributes, and element types.</span></span> <span data-ttu-id="a8492-393">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a8492-393">For example</span></span>

```csharp
unsafe struct A
{
   public fixed int x[5], y[10], z[100];
}
```

<span data-ttu-id="a8492-394">equivale a</span><span class="sxs-lookup"><span data-stu-id="a8492-394">is equivalent to</span></span>

```csharp
unsafe struct A
{
   public fixed int x[5];
   public fixed int y[10];
   public fixed int z[100];
}
```

### <a name="fixed-size-buffers-in-expressions"></a><span data-ttu-id="a8492-395">Buffer a dimensione fissa nelle espressioni</span><span class="sxs-lookup"><span data-stu-id="a8492-395">Fixed size buffers in expressions</span></span>

<span data-ttu-id="a8492-396">La ricerca di membri ([operatori](expressions.md#operators)) di un membro del buffer a dimensione fissa procede esattamente come la ricerca dei membri di un campo.</span><span class="sxs-lookup"><span data-stu-id="a8492-396">Member lookup ([Operators](expressions.md#operators)) of a fixed size buffer member proceeds exactly like member lookup of a field.</span></span>

<span data-ttu-id="a8492-397">È possibile fare riferimento a un buffer a dimensione fissa in un'espressione utilizzando un *simple_name* ([inferenza del tipo](expressions.md#type-inference)) o una *member_access* ([controllo in fase di compilazione della risoluzione dell'overload dinamico](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="a8492-397">A fixed size buffer can be referenced in an expression using a *simple_name* ([Type inference](expressions.md#type-inference)) or a *member_access* ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span></span>

<span data-ttu-id="a8492-398">Quando si fa riferimento a un membro del buffer a dimensione fissa come nome semplice, l'effetto è identico a quello di un accesso ai membri del form `this.I` , dove `I` è il membro del buffer a dimensione fissa.</span><span class="sxs-lookup"><span data-stu-id="a8492-398">When a fixed size buffer member is referenced as a simple name, the effect is the same as a member access of the form `this.I`, where `I` is the fixed size buffer member.</span></span>

<span data-ttu-id="a8492-399">In un accesso ai membri del form `E.I` , se `E` è di un tipo struct e una ricerca di membri di `I` in quel tipo di struct identifica un membro a dimensione fissa, `E.I` viene valutato un Classificato come segue:</span><span class="sxs-lookup"><span data-stu-id="a8492-399">In a member access of the form `E.I`, if `E` is of a struct type and a member lookup of `I` in that struct type identifies a fixed size member, then `E.I` is evaluated an classified as follows:</span></span>

*  <span data-ttu-id="a8492-400">Se l'espressione non `E.I` viene eseguita in un contesto non sicuro, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="a8492-400">If the expression `E.I` does not occur in an unsafe context, a compile-time error occurs.</span></span>
*  <span data-ttu-id="a8492-401">Se `E` è classificato come valore, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="a8492-401">If `E` is classified as a value, a compile-time error occurs.</span></span>
*  <span data-ttu-id="a8492-402">In caso contrario, se `E` è una variabile mobile ([variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)) e l'espressione `E.I` non è un *fixed_pointer_initializer* ([l'istruzione fixed](unsafe-code.md#the-fixed-statement)), si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="a8492-402">Otherwise, if `E` is a moveable variable ([Fixed and moveable variables](unsafe-code.md#fixed-and-moveable-variables)) and the expression `E.I` is not a *fixed_pointer_initializer* ([The fixed statement](unsafe-code.md#the-fixed-statement)), a compile-time error occurs.</span></span>
*  <span data-ttu-id="a8492-403">In caso contrario, `E` fa riferimento a una variabile fissa e il risultato dell'espressione è un puntatore al primo elemento del membro del buffer `I` a dimensione fissa in `E` .</span><span class="sxs-lookup"><span data-stu-id="a8492-403">Otherwise, `E` references a fixed variable and the result of the expression is a pointer to the first element of the fixed size buffer member `I` in `E`.</span></span> <span data-ttu-id="a8492-404">Il risultato è di tipo `S*` , dove `S` è il tipo di elemento di `I` ed è classificato come valore.</span><span class="sxs-lookup"><span data-stu-id="a8492-404">The result is of type `S*`, where `S` is the element type of `I`, and is classified as a value.</span></span>

<span data-ttu-id="a8492-405">È possibile accedere agli elementi successivi del buffer a dimensione fissa usando le operazioni del puntatore dal primo elemento.</span><span class="sxs-lookup"><span data-stu-id="a8492-405">The subsequent elements of the fixed size buffer can be accessed using pointer operations from the first element.</span></span> <span data-ttu-id="a8492-406">A differenza dell'accesso alle matrici, l'accesso agli elementi di un buffer a dimensione fissa è un'operazione non sicura e non è selezionato intervallo.</span><span class="sxs-lookup"><span data-stu-id="a8492-406">Unlike access to arrays, access to the elements of a fixed size buffer is an unsafe operation and is not range checked.</span></span>

<span data-ttu-id="a8492-407">Nell'esempio seguente viene dichiarato e utilizzato uno struct con un membro del buffer a dimensione fissa.</span><span class="sxs-lookup"><span data-stu-id="a8492-407">The following example declares and uses a struct with a fixed size buffer member.</span></span>

```csharp
unsafe struct Font
{
    public int size;
    public fixed char name[32];
}

class Test
{
    unsafe static void PutString(string s, char* buffer, int bufSize) {
        int len = s.Length;
        if (len > bufSize) len = bufSize;
        for (int i = 0; i < len; i++) buffer[i] = s[i];
        for (int i = len; i < bufSize; i++) buffer[i] = (char)0;
    }

    unsafe static void Main()
    {
        Font f;
        f.size = 10;
        PutString("Times New Roman", f.name, 32);
    }
}
```

### <a name="definite-assignment-checking"></a><span data-ttu-id="a8492-408">Controllo di assegnazione definito</span><span class="sxs-lookup"><span data-stu-id="a8492-408">Definite assignment checking</span></span>

<span data-ttu-id="a8492-409">I buffer a dimensione fissa non sono soggetti al controllo di assegnazione definito ([assegnazione definita](variables.md#definite-assignment)) e i membri del buffer a dimensione fissa vengono ignorati ai fini del controllo di assegnazione definito delle variabili di tipo struct.</span><span class="sxs-lookup"><span data-stu-id="a8492-409">Fixed size buffers are not subject to definite assignment checking ([Definite assignment](variables.md#definite-assignment)), and fixed size buffer members are ignored for purposes of definite assignment checking of struct type variables.</span></span>

<span data-ttu-id="a8492-410">Quando la variabile di struct contenitore più esterna di un membro del buffer a dimensione fissa è una variabile statica, una variabile di istanza di un'istanza di classe o un elemento di matrice, gli elementi del buffer a dimensione fissa vengono inizializzati automaticamente sui valori predefiniti ([valori predefiniti](variables.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="a8492-410">When the outermost containing struct variable of a fixed size buffer member is a static variable, an instance variable of a class instance, or an array element, the elements of the fixed size buffer are automatically initialized to their default values ([Default values](variables.md#default-values)).</span></span> <span data-ttu-id="a8492-411">In tutti gli altri casi, il contenuto iniziale di un buffer a dimensione fissa non è definito.</span><span class="sxs-lookup"><span data-stu-id="a8492-411">In all other cases, the initial content of a fixed size buffer is undefined.</span></span>

## <a name="stack-allocation"></a><span data-ttu-id="a8492-412">Allocazione nello stack</span><span class="sxs-lookup"><span data-stu-id="a8492-412">Stack allocation</span></span>

<span data-ttu-id="a8492-413">In un contesto unsafe, una dichiarazione di variabile locale ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)) può includere un inizializzatore di allocazione dello stack che alloca memoria dallo stack di chiamate.</span><span class="sxs-lookup"><span data-stu-id="a8492-413">In an unsafe context, a local variable declaration ([Local variable declarations](statements.md#local-variable-declarations)) may include a stack allocation initializer which allocates memory from the call stack.</span></span>

```antlr
local_variable_initializer_unsafe
    : stackalloc_initializer
    ;

stackalloc_initializer
    : 'stackalloc' unmanaged_type '[' expression ']'
    ;
```

<span data-ttu-id="a8492-414">Il *unmanaged_type* indica il tipo degli elementi che verranno archiviati nel percorso appena allocato e l' *espressione* indica il numero di questi elementi.</span><span class="sxs-lookup"><span data-stu-id="a8492-414">The *unmanaged_type* indicates the type of the items that will be stored in the newly allocated location, and the *expression* indicates the number of these items.</span></span> <span data-ttu-id="a8492-415">Insieme, questi specificano le dimensioni di allocazione richieste.</span><span class="sxs-lookup"><span data-stu-id="a8492-415">Taken together, these specify the required allocation size.</span></span> <span data-ttu-id="a8492-416">Poiché le dimensioni di un'allocazione dello stack non possono essere negative, si verifica un errore in fase di compilazione per specificare il numero di elementi come *constant_expression* che restituisce un valore negativo.</span><span class="sxs-lookup"><span data-stu-id="a8492-416">Since the size of a stack allocation cannot be negative, it is a compile-time error to specify the number of items as a *constant_expression* that evaluates to a negative value.</span></span>

<span data-ttu-id="a8492-417">Un inizializzatore di allocazione dello stack del form `stackalloc T[E]` richiede `T` che sia un tipo non gestito ([tipi puntatore](unsafe-code.md#pointer-types)) e `E` che sia un'espressione di tipo `int` .</span><span class="sxs-lookup"><span data-stu-id="a8492-417">A stack allocation initializer of the form `stackalloc T[E]` requires `T` to be an unmanaged type ([Pointer types](unsafe-code.md#pointer-types)) and `E` to be an expression of type `int`.</span></span> <span data-ttu-id="a8492-418">Il costrutto alloca `E * sizeof(T)` byte dallo stack di chiamate e restituisce un puntatore di tipo `T*` al nuovo blocco allocato.</span><span class="sxs-lookup"><span data-stu-id="a8492-418">The construct allocates `E * sizeof(T)` bytes from the call stack and returns a pointer, of type `T*`, to the newly allocated block.</span></span> <span data-ttu-id="a8492-419">Se `E` è un valore negativo, il comportamento non è definito.</span><span class="sxs-lookup"><span data-stu-id="a8492-419">If `E` is a negative value, then the behavior is undefined.</span></span> <span data-ttu-id="a8492-420">Se `E` è zero, non viene eseguita alcuna allocazione e il puntatore restituito è definito dall'implementazione.</span><span class="sxs-lookup"><span data-stu-id="a8492-420">If `E` is zero, then no allocation is made, and the pointer returned is implementation-defined.</span></span> <span data-ttu-id="a8492-421">Se la memoria disponibile non è sufficiente per allocare un blocco della dimensione specificata, `System.StackOverflowException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="a8492-421">If there is not enough memory available to allocate a block of the given size, a `System.StackOverflowException` is thrown.</span></span>

<span data-ttu-id="a8492-422">Il contenuto della memoria appena allocata non è definito.</span><span class="sxs-lookup"><span data-stu-id="a8492-422">The content of the newly allocated memory is undefined.</span></span>

<span data-ttu-id="a8492-423">Gli inizializzatori di allocazione dello stack non sono consentiti nei `catch` `finally` blocchi o ([istruzione try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="a8492-423">Stack allocation initializers are not permitted in `catch` or `finally` blocks ([The try statement](statements.md#the-try-statement)).</span></span>

<span data-ttu-id="a8492-424">Non è possibile liberare esplicitamente la memoria allocata utilizzando `stackalloc` .</span><span class="sxs-lookup"><span data-stu-id="a8492-424">There is no way to explicitly free memory allocated using `stackalloc`.</span></span> <span data-ttu-id="a8492-425">Tutti i blocchi di memoria allocati dello stack creati durante l'esecuzione di un membro di funzione vengono eliminati automaticamente quando il membro della funzione restituisce.</span><span class="sxs-lookup"><span data-stu-id="a8492-425">All stack allocated memory blocks created during the execution of a function member are automatically discarded when that function member returns.</span></span> <span data-ttu-id="a8492-426">Corrisponde alla `alloca` funzione, un'estensione comunemente trovata nelle implementazioni C e C++.</span><span class="sxs-lookup"><span data-stu-id="a8492-426">This corresponds to the `alloca` function, an extension commonly found in C and C++ implementations.</span></span>

<span data-ttu-id="a8492-427">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="a8492-427">In the example</span></span>

```csharp
using System;

class Test
{
    static string IntToString(int value) {
        int n = value >= 0? value: -value;
        unsafe {
            char* buffer = stackalloc char[16];
            char* p = buffer + 16;
            do {
                *--p = (char)(n % 10 + '0');
                n /= 10;
            } while (n != 0);
            if (value < 0) *--p = '-';
            return new string(p, 0, (int)(buffer + 16 - p));
        }
    }

    static void Main() {
        Console.WriteLine(IntToString(12345));
        Console.WriteLine(IntToString(-999));
    }
}
```

<span data-ttu-id="a8492-428">un `stackalloc` inizializzatore viene usato nel `IntToString` metodo per allocare un buffer di 16 caratteri nello stack.</span><span class="sxs-lookup"><span data-stu-id="a8492-428">a `stackalloc` initializer is used in the `IntToString` method to allocate a buffer of 16 characters on the stack.</span></span> <span data-ttu-id="a8492-429">Il buffer viene rimosso automaticamente quando il metodo restituisce un risultato.</span><span class="sxs-lookup"><span data-stu-id="a8492-429">The buffer is automatically discarded when the method returns.</span></span>

## <a name="dynamic-memory-allocation"></a><span data-ttu-id="a8492-430">Allocazione di memoria dinamica</span><span class="sxs-lookup"><span data-stu-id="a8492-430">Dynamic memory allocation</span></span>

<span data-ttu-id="a8492-431">Ad eccezione dell' `stackalloc` operatore, C# non fornisce costrutti predefiniti per la gestione della memoria non sottoposta a Garbage Collection.</span><span class="sxs-lookup"><span data-stu-id="a8492-431">Except for the `stackalloc` operator, C# provides no predefined constructs for managing non-garbage collected memory.</span></span> <span data-ttu-id="a8492-432">Tali servizi vengono in genere forniti mediante il supporto di librerie di classi o importati direttamente dal sistema operativo sottostante.</span><span class="sxs-lookup"><span data-stu-id="a8492-432">Such services are typically provided by supporting class libraries or imported directly from the underlying operating system.</span></span> <span data-ttu-id="a8492-433">Ad esempio, la `Memory` classe seguente illustra il modo in cui è possibile accedere alle funzioni dell'heap di un sistema operativo sottostante da C#:</span><span class="sxs-lookup"><span data-stu-id="a8492-433">For example, the `Memory` class below illustrates how the heap functions of an underlying operating system might be accessed from C#:</span></span>

```csharp
using System;
using System.Runtime.InteropServices;

public static unsafe class Memory
{
    // Handle for the process heap. This handle is used in all calls to the
    // HeapXXX APIs in the methods below.
    private static readonly IntPtr s_heap = GetProcessHeap();

    // Allocates a memory block of the given size. The allocated memory is
    // automatically initialized to zero.
    public static void* Alloc(int size)
    {
        void* result = HeapAlloc(s_heap, HEAP_ZERO_MEMORY, (UIntPtr)size);
        if (result == null) throw new OutOfMemoryException();
        return result;
    }

    // Copies count bytes from src to dst. The source and destination
    // blocks are permitted to overlap.
    public static void Copy(void* src, void* dst, int count)
    {
        byte* ps = (byte*)src;
        byte* pd = (byte*)dst;
        if (ps > pd)
        {
            for (; count != 0; count--) *pd++ = *ps++;
        }
        else if (ps < pd)
        {
            for (ps += count, pd += count; count != 0; count--) *--pd = *--ps;
        }
    }

    // Frees a memory block.
    public static void Free(void* block)
    {
        if (!HeapFree(s_heap, 0, block)) throw new InvalidOperationException();
    }

    // Re-allocates a memory block. If the reallocation request is for a
    // larger size, the additional region of memory is automatically
    // initialized to zero.
    public static void* ReAlloc(void* block, int size)
    {
        void* result = HeapReAlloc(s_heap, HEAP_ZERO_MEMORY, block, (UIntPtr)size);
        if (result == null) throw new OutOfMemoryException();
        return result;
    }

    // Returns the size of a memory block.
    public static int SizeOf(void* block)
    {
        int result = (int)HeapSize(s_heap, 0, block);
        if (result == -1) throw new InvalidOperationException();
        return result;
    }

    // Heap API flags
    private const int HEAP_ZERO_MEMORY = 0x00000008;

    // Heap API functions
    [DllImport("kernel32")]
    private static extern IntPtr GetProcessHeap();

    [DllImport("kernel32")]
    private static extern void* HeapAlloc(IntPtr hHeap, int flags, UIntPtr size);

    [DllImport("kernel32")]
    private static extern bool HeapFree(IntPtr hHeap, int flags, void* block);

    [DllImport("kernel32")]
    private static extern void* HeapReAlloc(IntPtr hHeap, int flags, void* block, UIntPtr size);

    [DllImport("kernel32")]
    private static extern UIntPtr HeapSize(IntPtr hHeap, int flags, void* block);
}
```

<span data-ttu-id="a8492-434">Di seguito è riportato un esempio che usa la `Memory` classe:</span><span class="sxs-lookup"><span data-stu-id="a8492-434">An example that uses the `Memory` class is given below:</span></span>

```csharp
class Test
{
    static unsafe void Main()
    {
        byte* buffer = null;
        try
        {
            const int Size = 256;
            buffer = (byte*)Memory.Alloc(Size);
            for (int i = 0; i < Size; i++) buffer[i] = (byte)i;
            byte[] array = new byte[Size];
            fixed (byte* p = array) Memory.Copy(buffer, p, Size);
            for (int i = 0; i < Size; i++) Console.WriteLine(array[i]);
        }
        finally
        {
            if (buffer != null) Memory.Free(buffer);
        }
    }
}
```

<span data-ttu-id="a8492-435">Nell'esempio vengono allocati 256 byte di memoria tramite `Memory.Alloc` e viene inizializzato il blocco di memoria con valori che aumentano da 0 a 255.</span><span class="sxs-lookup"><span data-stu-id="a8492-435">The example allocates 256 bytes of memory through `Memory.Alloc` and initializes the memory block with values increasing from 0 to 255.</span></span> <span data-ttu-id="a8492-436">Viene quindi allocata una matrice di byte di elementi 256 e viene utilizzato `Memory.Copy` per copiare il contenuto del blocco di memoria nella matrice di byte.</span><span class="sxs-lookup"><span data-stu-id="a8492-436">It then allocates a 256 element byte array and uses `Memory.Copy` to copy the contents of the memory block into the byte array.</span></span> <span data-ttu-id="a8492-437">Infine, il blocco di memoria viene liberato usando `Memory.Free` e il contenuto della matrice di byte viene restituito nella console.</span><span class="sxs-lookup"><span data-stu-id="a8492-437">Finally, the memory block is freed using `Memory.Free` and the contents of the byte array are output on the console.</span></span>
