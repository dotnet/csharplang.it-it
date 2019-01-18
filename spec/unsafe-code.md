---
ms.openlocfilehash: 90001cf3d48f216787fc65e59166ec57c5d0ca34
ms.sourcegitcommit: 3fc033b6e98ed7ecdf46a85c79b00a3a3ddcf963
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2019
ms.locfileid: "47229619"
---
# <a name="unsafe-code"></a><span data-ttu-id="c95ac-101">Codice di tipo unsafe</span><span class="sxs-lookup"><span data-stu-id="c95ac-101">Unsafe code</span></span>

<span data-ttu-id="c95ac-102">Il linguaggio c#, come definito nei precedenti capitoli, differisce notevolmente dai linguaggi C e C++ nell'omissione dei puntatori come tipo di dati.</span><span class="sxs-lookup"><span data-stu-id="c95ac-102">The core C# language, as defined in the preceding chapters, differs notably from C and C++ in its omission of pointers as a data type.</span></span> <span data-ttu-id="c95ac-103">Al contrario, c# fornisce riferimenti e la possibilità di creare gli oggetti gestiti da un garbage collector.</span><span class="sxs-lookup"><span data-stu-id="c95ac-103">Instead, C# provides references and the ability to create objects that are managed by a garbage collector.</span></span> <span data-ttu-id="c95ac-104">Rende questa struttura, insieme ad altre funzionalità, in c# un linguaggio molto più sicuro rispetto a C o C++.</span><span class="sxs-lookup"><span data-stu-id="c95ac-104">This design, coupled with other features, makes C# a much safer language than C or C++.</span></span> <span data-ttu-id="c95ac-105">Nel linguaggio core c# semplicemente non è possibile avere una variabile non inizializzata, un puntatore "inesatti" o un'espressione che indicizza una matrice oltre i limiti.</span><span class="sxs-lookup"><span data-stu-id="c95ac-105">In the core C# language it is simply not possible to have an uninitialized variable, a "dangling" pointer, or an expression that indexes an array beyond its bounds.</span></span> <span data-ttu-id="c95ac-106">Categorie di intere di bug riscontrati che in continuazione nei C e i programmi C++ in tal modo vengono eliminati.</span><span class="sxs-lookup"><span data-stu-id="c95ac-106">Whole categories of bugs that routinely plague C and C++ programs are thus eliminated.</span></span>

<span data-ttu-id="c95ac-107">Sebbene praticamente tutti i costrutti tipo puntatore in C o C++ ha un equivalente di tipo riferimento in c#, nonostante ciò, esistono situazioni in cui l'accesso ai tipi di puntatore diventa una necessità.</span><span class="sxs-lookup"><span data-stu-id="c95ac-107">While practically every pointer type construct in C or C++ has a reference type counterpart in C#, nonetheless, there are situations where access to pointer types becomes a necessity.</span></span> <span data-ttu-id="c95ac-108">Ad esempio, l'interazione con il sistema operativo sottostante, l'accesso a un dispositivo con mapping in memoria o implementare un algoritmo potrebbe non essere possibile o fattibile senza accesso a puntatori.</span><span class="sxs-lookup"><span data-stu-id="c95ac-108">For example, interfacing with the underlying operating system, accessing a memory-mapped device, or implementing a time-critical algorithm may not be possible or practical without access to pointers.</span></span> <span data-ttu-id="c95ac-109">Per soddisfare questa esigenza, c# offre la possibilità di scrivere ***codice unsafe***.</span><span class="sxs-lookup"><span data-stu-id="c95ac-109">To address this need, C# provides the ability to write ***unsafe code***.</span></span>

<span data-ttu-id="c95ac-110">Nel codice di tipo unsafe è possibile dichiarare e utilizzare con i puntatori, eseguire le conversioni tra puntatori e i tipi integrali, per accettare l'indirizzo di variabili, e così via.</span><span class="sxs-lookup"><span data-stu-id="c95ac-110">In unsafe code it is possible to declare and operate on pointers, to perform conversions between pointers and integral types, to take the address of variables, and so forth.</span></span> <span data-ttu-id="c95ac-111">In un certo senso, la scrittura di codice unsafe è molto simile a scrivere codice C all'interno di un programma c#.</span><span class="sxs-lookup"><span data-stu-id="c95ac-111">In a sense, writing unsafe code is much like writing C code within a C# program.</span></span>

<span data-ttu-id="c95ac-112">Codice unsafe in realtà è una funzionalità "sicura" dal punto di vista di sviluppatori e utenti.</span><span class="sxs-lookup"><span data-stu-id="c95ac-112">Unsafe code is in fact a "safe" feature from the perspective of both developers and users.</span></span> <span data-ttu-id="c95ac-113">Codice unsafe debba essere chiaramente contrassegnato con il modificatore `unsafe`, in modo che gli sviluppatori usare accidentalmente le funzionalità non sicure e il motore di esecuzione si impegna per assicurare che il codice unsafe non può essere eseguito in un ambiente non attendibile.</span><span class="sxs-lookup"><span data-stu-id="c95ac-113">Unsafe code must be clearly marked with the modifier `unsafe`, so developers can't possibly use unsafe features accidentally, and the execution engine works to ensure that unsafe code cannot be executed in an untrusted environment.</span></span>

## <a name="unsafe-contexts"></a><span data-ttu-id="c95ac-114">Contesti non sicuri</span><span class="sxs-lookup"><span data-stu-id="c95ac-114">Unsafe contexts</span></span>

<span data-ttu-id="c95ac-115">Le funzionalità non sicure del linguaggio c# sono disponibili solo in contesti non sicuri.</span><span class="sxs-lookup"><span data-stu-id="c95ac-115">The unsafe features of C# are available only in unsafe contexts.</span></span> <span data-ttu-id="c95ac-116">È stato introdotto un contesto unsafe, includendo un' `unsafe` modificatore nella dichiarazione di un tipo o membro, o tramite l'uso di un *unsafe_statement*:</span><span class="sxs-lookup"><span data-stu-id="c95ac-116">An unsafe context is introduced by including an `unsafe` modifier in the declaration of a type or member, or by employing an *unsafe_statement*:</span></span>

*  <span data-ttu-id="c95ac-117">Una dichiarazione di una classe, struct, interfaccia o delegato può includere un `unsafe` modificatore, in cui il l'intera estensione testuale di tale dichiarazione del tipo (incluso il corpo della classe, struct o interfaccia) viene considerato un contesto non sicuro.</span><span class="sxs-lookup"><span data-stu-id="c95ac-117">A declaration of a class, struct, interface, or delegate may include an `unsafe` modifier, in which case the entire textual extent of that type declaration (including the body of the class, struct, or interface) is considered an unsafe context.</span></span>
*  <span data-ttu-id="c95ac-118">Una dichiarazione di un campo, metodo, proprietà, eventi, l'indicizzatore, operatore, costruttore di istanza, distruttore o costruttore statico potrebbe includere un `unsafe` modificatore, in cui il l'intera estensione testuale che della dichiarazione del membro viene considerato un tipo unsafe contesto.</span><span class="sxs-lookup"><span data-stu-id="c95ac-118">A declaration of a field, method, property, event, indexer, operator, instance constructor, destructor, or static constructor may include an `unsafe` modifier, in which case the entire textual extent of that member declaration is considered an unsafe context.</span></span>
*  <span data-ttu-id="c95ac-119">Un' *unsafe_statement* consente l'uso di un contesto unsafe all'interno di un *blocco*.</span><span class="sxs-lookup"><span data-stu-id="c95ac-119">An *unsafe_statement* enables the use of an unsafe context within a *block*.</span></span> <span data-ttu-id="c95ac-120">L'intera estensione testuale dell'oggetto associato *blocco* viene considerato un contesto non sicuro.</span><span class="sxs-lookup"><span data-stu-id="c95ac-120">The entire textual extent of the associated *block* is considered an unsafe context.</span></span>

<span data-ttu-id="c95ac-121">Productions la grammatica associati vengono visualizzati sotto.</span><span class="sxs-lookup"><span data-stu-id="c95ac-121">The associated grammar productions are shown below.</span></span>

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

<span data-ttu-id="c95ac-122">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="c95ac-122">In the example</span></span>

```csharp
public unsafe struct Node
{
    public int Value;
    public Node* Left;
    public Node* Right;
}
```

<span data-ttu-id="c95ac-123">il `unsafe` modificatore specificato nella dichiarazione di struct fa sì che l'intera estensione testuale della dichiarazione di struct per diventare un contesto non sicuro.</span><span class="sxs-lookup"><span data-stu-id="c95ac-123">the `unsafe` modifier specified in the struct declaration causes the entire textual extent of the struct declaration to become an unsafe context.</span></span> <span data-ttu-id="c95ac-124">Di conseguenza, è possibile dichiarare la `Left` e `Right` campi di un tipo di puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-124">Thus, it is possible to declare the `Left` and `Right` fields to be of a pointer type.</span></span> <span data-ttu-id="c95ac-125">Nell'esempio precedente può anche essere scritta</span><span class="sxs-lookup"><span data-stu-id="c95ac-125">The example above could also be written</span></span>

```csharp
public struct Node
{
    public int Value;
    public unsafe Node* Left;
    public unsafe Node* Right;
}
```

<span data-ttu-id="c95ac-126">In questo caso, il `unsafe` tali dichiarazioni essere considerato contesti non sicuri a causa dei modificatori nelle dichiarazioni di campo.</span><span class="sxs-lookup"><span data-stu-id="c95ac-126">Here, the `unsafe` modifiers in the field declarations cause those declarations to be considered unsafe contexts.</span></span>

<span data-ttu-id="c95ac-127">Oltre a stabilire un contesto unsafe, permettendo così l'utilizzo di tipi di puntatore, il `unsafe` modificatore non ha alcun effetto su un tipo o membro.</span><span class="sxs-lookup"><span data-stu-id="c95ac-127">Other than establishing an unsafe context, thus permitting the use of pointer types, the `unsafe` modifier has no effect on a type or a member.</span></span> <span data-ttu-id="c95ac-128">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="c95ac-128">In the example</span></span>

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

<span data-ttu-id="c95ac-129">il `unsafe` modificatore il `F` metodo nella `A` semplicemente fa sì che l'estensione testuale del `F` per diventare un contesto unsafe in cui è possono utilizzare le funzionalità del linguaggio non sicure.</span><span class="sxs-lookup"><span data-stu-id="c95ac-129">the `unsafe` modifier on the `F` method in `A` simply causes the textual extent of `F` to become an unsafe context in which the unsafe features of the language can be used.</span></span> <span data-ttu-id="c95ac-130">Nell'override di `F` nel `B`, non è necessario specificare di nuovo il `unsafe` modificatore, a meno che non, naturalmente, il `F` metodo nella `B` stesso deve avere accesso alle funzionalità di tipo unsafe.</span><span class="sxs-lookup"><span data-stu-id="c95ac-130">In the override of `F` in `B`, there is no need to re-specify the `unsafe` modifier -- unless, of course, the `F` method in `B` itself needs access to unsafe features.</span></span>

<span data-ttu-id="c95ac-131">La situazione è leggermente diversa quando un tipo di puntatore fa parte della firma del metodo</span><span class="sxs-lookup"><span data-stu-id="c95ac-131">The situation is slightly different when a pointer type is part of the method's signature</span></span>

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

<span data-ttu-id="c95ac-132">In questo caso, poiché `F`della firma include un tipo di puntatore, può essere scritta solo in un contesto unsafe.</span><span class="sxs-lookup"><span data-stu-id="c95ac-132">Here, because `F`'s signature includes a pointer type, it can only be written in an unsafe context.</span></span> <span data-ttu-id="c95ac-133">Tuttavia, il contesto non sicuro può essere introdotta apportando entrambi l'intera classe poco sicuro, come nel caso in `A`, o includendo un' `unsafe` modificatore nella dichiarazione del metodo, come nel caso in `B`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-133">However, the unsafe context can be introduced by either making the entire class unsafe, as is the case in `A`, or by including an `unsafe` modifier in the method declaration, as is the case in `B`.</span></span>

## <a name="pointer-types"></a><span data-ttu-id="c95ac-134">Tipi puntatore</span><span class="sxs-lookup"><span data-stu-id="c95ac-134">Pointer types</span></span>

<span data-ttu-id="c95ac-135">In un contesto unsafe, un *tipo* ([tipi](types.md)) può essere una *pointer_type* nonché un *value_type* o una *reference_type* .</span><span class="sxs-lookup"><span data-stu-id="c95ac-135">In an unsafe context, a *type* ([Types](types.md)) may be a *pointer_type* as well as a *value_type* or a *reference_type*.</span></span> <span data-ttu-id="c95ac-136">Tuttavia, un *pointer_type* può essere usato anche un `typeof` espressione ([espressioni per la creazione di oggetti anonimi](expressions.md#anonymous-object-creation-expressions)) di fuori di un contesto non sicuro in quanto tale utilizzo non unsafe.</span><span class="sxs-lookup"><span data-stu-id="c95ac-136">However, a *pointer_type* may also be used in a `typeof` expression ([Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions)) outside of an unsafe context as such usage is not unsafe.</span></span>

```antlr
type_unsafe
    : pointer_type
    ;
```

<span data-ttu-id="c95ac-137">Oggetto *pointer_type* viene scritto come un' *unmanaged_type* o la parola chiave `void`, seguito da un `*` token:</span><span class="sxs-lookup"><span data-stu-id="c95ac-137">A *pointer_type* is written as an *unmanaged_type* or the keyword `void`, followed by a `*` token:</span></span>

```antlr
pointer_type
    : unmanaged_type '*'
    | 'void' '*'
    ;

unmanaged_type
    : type
    ;
```

<span data-ttu-id="c95ac-138">Il tipo specificato prima la `*` un puntatore di tipo viene chiamato il ***tipo referente*** del tipo puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-138">The type specified before the `*` in a pointer type is called the ***referent type*** of the pointer type.</span></span> <span data-ttu-id="c95ac-139">Rappresenta il tipo della variabile a cui fa riferimento un valore del tipo di puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-139">It represents the type of the variable to which a value of the pointer type points.</span></span>

<span data-ttu-id="c95ac-140">A differenza dei riferimenti (valori dei tipi di riferimento), i puntatori non vengono rilevati dal garbage collector, ovvero il garbage collector non ha una conoscenza di puntatori e i dati a cui puntano.</span><span class="sxs-lookup"><span data-stu-id="c95ac-140">Unlike references (values of reference types), pointers are not tracked by the garbage collector -- the garbage collector has no knowledge of pointers and the data to which they point.</span></span> <span data-ttu-id="c95ac-141">Per questo motivo un puntatore non è consentito in modo che punti a un riferimento o a una struttura che contiene i riferimenti e deve essere di tipo referente di un puntatore a un *unmanaged_type*.</span><span class="sxs-lookup"><span data-stu-id="c95ac-141">For this reason a pointer is not permitted to point to a reference or to a struct that contains references, and the referent type of a pointer must be an *unmanaged_type*.</span></span>

<span data-ttu-id="c95ac-142">Un' *unmanaged_type* è qualsiasi tipo che non è un *reference_type* o tipo costruito e non contiene *reference_type* o costruito i campi di tipo a qualsiasi livello di annidamento.</span><span class="sxs-lookup"><span data-stu-id="c95ac-142">An *unmanaged_type* is any type that isn't a *reference_type* or constructed type, and doesn't contain *reference_type* or constructed type fields at any level of nesting.</span></span> <span data-ttu-id="c95ac-143">In altre parole, un' *unmanaged_type* è uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="c95ac-143">In other words, an *unmanaged_type* is one of the following:</span></span>

*  <span data-ttu-id="c95ac-144">`sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, o `bool`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-144">`sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, or `bool`.</span></span>
*  <span data-ttu-id="c95ac-145">Eventuali *enum_type*.</span><span class="sxs-lookup"><span data-stu-id="c95ac-145">Any *enum_type*.</span></span>
*  <span data-ttu-id="c95ac-146">Eventuali *pointer_type*.</span><span class="sxs-lookup"><span data-stu-id="c95ac-146">Any *pointer_type*.</span></span>
*  <span data-ttu-id="c95ac-147">Qualsiasi definiti dall'utente *struct_type* che non è un tipo costruito e contiene i campi del *unmanaged_type*solo.</span><span class="sxs-lookup"><span data-stu-id="c95ac-147">Any user-defined *struct_type* that is not a constructed type and contains fields of *unmanaged_type*s only.</span></span>

<span data-ttu-id="c95ac-148">La regola intuitiva per la combinazione di puntatori e riferimenti è che sono consentiti referenti dei riferimenti (oggetti) che contiene i puntatori, ma non possono contenere riferimenti referenti di puntatori.</span><span class="sxs-lookup"><span data-stu-id="c95ac-148">The intuitive rule for mixing of pointers and references is that referents of references (objects) are permitted to contain pointers, but referents of pointers are not permitted to contain references.</span></span>

<span data-ttu-id="c95ac-149">Nella tabella seguente sono indicati alcuni esempi di tipi di puntatore:</span><span class="sxs-lookup"><span data-stu-id="c95ac-149">Some examples of pointer types are given in the table below:</span></span>

| <span data-ttu-id="c95ac-150">__Esempio__</span><span class="sxs-lookup"><span data-stu-id="c95ac-150">__Example__</span></span> | <span data-ttu-id="c95ac-151">__Descrizione__</span><span class="sxs-lookup"><span data-stu-id="c95ac-151">__Description__</span></span>                               |
|-------------|-----------------------------------------------|
| `byte*`     | <span data-ttu-id="c95ac-152">Puntatore a `byte`</span><span class="sxs-lookup"><span data-stu-id="c95ac-152">Pointer to `byte`</span></span>                             |
| `char*`     | <span data-ttu-id="c95ac-153">Puntatore a `char`</span><span class="sxs-lookup"><span data-stu-id="c95ac-153">Pointer to `char`</span></span>                             |
| `int**`     | <span data-ttu-id="c95ac-154">Puntatore a puntatore a `int`</span><span class="sxs-lookup"><span data-stu-id="c95ac-154">Pointer to pointer to `int`</span></span>                   |
| `int*[]`    | <span data-ttu-id="c95ac-155">Matrice unidimensionale di puntatori a `int`</span><span class="sxs-lookup"><span data-stu-id="c95ac-155">Single-dimensional array of pointers to `int`</span></span> |
| `void*`     | <span data-ttu-id="c95ac-156">Puntatore a tipo sconosciuto</span><span class="sxs-lookup"><span data-stu-id="c95ac-156">Pointer to unknown type</span></span>                       |

<span data-ttu-id="c95ac-157">Per una determinata implementazione, tutti i tipi di puntatore devono avere le stesse dimensioni e rappresentazione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-157">For a given implementation, all pointer types must have the same size and representation.</span></span>

<span data-ttu-id="c95ac-158">Diversamente da C e C++, quando vengono dichiarati più puntatori nella stessa dichiarazione, in c# il `*` viene scritto insieme al tipo sottostante, non come un segno di punteggiatura di prefisso su ciascun nome di puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-158">Unlike C and C++, when multiple pointers are declared in the same declaration, in C# the `*` is written along with the underlying type only, not as a prefix punctuator on each pointer name.</span></span> <span data-ttu-id="c95ac-159">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c95ac-159">For example</span></span>

```csharp
int* pi, pj;    // NOT as int *pi, *pj;
```

<span data-ttu-id="c95ac-160">Il valore di un puntatore di tipo `T*` rappresenta l'indirizzo di una variabile di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-160">The value of a pointer having type `T*` represents the address of a variable of type `T`.</span></span> <span data-ttu-id="c95ac-161">L'operatore di riferimento indiretto puntatore `*` ([riferimento indiretto al puntatore](unsafe-code.md#pointer-indirection)) può essere utilizzato per accedere a questa variabile.</span><span class="sxs-lookup"><span data-stu-id="c95ac-161">The pointer indirection operator `*` ([Pointer indirection](unsafe-code.md#pointer-indirection)) may be used to access this variable.</span></span> <span data-ttu-id="c95ac-162">Si consideri ad esempio una variabile `P` typu `int*`, l'espressione `*P` denota il `int` variabile trovata in corrispondenza dell'indirizzo contenuto in `P`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-162">For example, given a variable `P` of type `int*`, the expression `*P` denotes the `int` variable found at the address contained in `P`.</span></span>

<span data-ttu-id="c95ac-163">Ad esempio un riferimento all'oggetto, potrebbe essere un puntatore `null`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-163">Like an object reference, a pointer may be `null`.</span></span> <span data-ttu-id="c95ac-164">Si applica l'operatore di riferimento indiretto a un `null` puntatore produce un comportamento definito dall'implementazione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-164">Applying the indirection operator to a `null` pointer results in implementation-defined behavior.</span></span> <span data-ttu-id="c95ac-165">Un puntatore con valore `null` è rappresentato da tutti i bit a zero.</span><span class="sxs-lookup"><span data-stu-id="c95ac-165">A pointer with value `null` is represented by all-bits-zero.</span></span>

<span data-ttu-id="c95ac-166">Il `void*` tipo rappresenta un puntatore a un tipo sconosciuto.</span><span class="sxs-lookup"><span data-stu-id="c95ac-166">The `void*` type represents a pointer to an unknown type.</span></span> <span data-ttu-id="c95ac-167">Poiché il tipo referente è sconosciuto, l'operatore di riferimento indiretto non può essere applicato a un puntatore di tipo `void*`, né può essere eseguite le operazioni aritmetiche su tale puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-167">Because the referent type is unknown, the indirection operator cannot be applied to a pointer of type `void*`, nor can any arithmetic be performed on such a pointer.</span></span> <span data-ttu-id="c95ac-168">Tuttavia, un puntatore di tipo `void*` può eseguire il cast a qualsiasi altro tipo di puntatore (e viceversa).</span><span class="sxs-lookup"><span data-stu-id="c95ac-168">However, a pointer of type `void*` can be cast to any other pointer type (and vice versa).</span></span>

<span data-ttu-id="c95ac-169">Tipi di puntatori sono una categoria di tipi separata.</span><span class="sxs-lookup"><span data-stu-id="c95ac-169">Pointer types are a separate category of types.</span></span> <span data-ttu-id="c95ac-170">A differenza dei tipi di riferimento e tipi di valore, tipi di puntatore non ereditano da `object` e non esiste alcuna conversione tra tipi di puntatore e `object`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-170">Unlike reference types and value types, pointer types do not inherit from `object` and no conversions exist between pointer types and `object`.</span></span> <span data-ttu-id="c95ac-171">In particolare, la conversione boxing e unboxing ([conversioni Boxing e unboxing](types.md#boxing-and-unboxing)) non sono supportati per i puntatori.</span><span class="sxs-lookup"><span data-stu-id="c95ac-171">In particular, boxing and unboxing ([Boxing and unboxing](types.md#boxing-and-unboxing)) are not supported for pointers.</span></span> <span data-ttu-id="c95ac-172">Tuttavia, sono consentite le conversioni tra tipi di puntatore diversi e tra i tipi di puntatore e tipi integrali.</span><span class="sxs-lookup"><span data-stu-id="c95ac-172">However, conversions are permitted between different pointer types and between pointer types and the integral types.</span></span> <span data-ttu-id="c95ac-173">Come descritto nella [conversioni di puntatori](unsafe-code.md#pointer-conversions).</span><span class="sxs-lookup"><span data-stu-id="c95ac-173">This is described in [Pointer conversions](unsafe-code.md#pointer-conversions).</span></span>

<span data-ttu-id="c95ac-174">Oggetto *pointer_type* non può essere usato come argomento di tipo ([tipi costruiti](types.md#constructed-types)) e l'inferenza del tipo ([inferenza del tipo](expressions.md#type-inference)) ha esito negativo nelle chiamate di metodo generico sono inferire un tipo di argomento sia un tipo di puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-174">A *pointer_type* cannot be used as a type argument ([Constructed types](types.md#constructed-types)), and type inference ([Type inference](expressions.md#type-inference)) fails on generic method calls that would have inferred a type argument to be a pointer type.</span></span>

<span data-ttu-id="c95ac-175">Oggetto *pointer_type* può essere usato come tipo di un campo volatile ([campi volatili](classes.md#volatile-fields)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-175">A *pointer_type* may be used as the type of a volatile field ([Volatile fields](classes.md#volatile-fields)).</span></span>

<span data-ttu-id="c95ac-176">Anche se i puntatori possono essere passati come `ref` o `out` parametri, tale operazione può causare un comportamento non definito, poiché il puntatore può anche essere impostati in modo che punti a una variabile locale che non esiste più quando il metodo chiamato termina oppure l'oggetto predefinito a cui si usato per puntare, non è stato risolto.</span><span class="sxs-lookup"><span data-stu-id="c95ac-176">Although pointers can be passed as `ref` or `out` parameters, doing so can cause undefined behavior, since the pointer may well be set to point to a local variable which no longer exists when the called method returns, or the fixed object to which it used to point, is no longer fixed.</span></span> <span data-ttu-id="c95ac-177">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c95ac-177">For example:</span></span>

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

<span data-ttu-id="c95ac-178">Un metodo può restituire un valore di tipo e tale tipo può essere un puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-178">A method can return a value of some type, and that type can be a pointer.</span></span> <span data-ttu-id="c95ac-179">Ad esempio, quando si riceve un puntatore a una sequenza di contigui `int`s, numero di elementi della sequenza e un altro `int` valore, il metodo seguente restituisce l'indirizzo di tale valore in tale sequenza, se esiste una corrispondenza; in caso contrario, restituisce `null`:</span><span class="sxs-lookup"><span data-stu-id="c95ac-179">For example, when given a pointer to a contiguous sequence of `int`s, that sequence's element count, and some other `int` value, the following method returns the address of that value in that sequence, if a match occurs; otherwise it returns `null`:</span></span>

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

<span data-ttu-id="c95ac-180">In un contesto unsafe, sono disponibili per l'uso di puntatori di diversi costrutti:</span><span class="sxs-lookup"><span data-stu-id="c95ac-180">In an unsafe context, several constructs are available for operating on pointers:</span></span>

*  <span data-ttu-id="c95ac-181">Il `*` operatore può essere utilizzato per eseguire riferimento indiretto al puntatore ([riferimento indiretto al puntatore](unsafe-code.md#pointer-indirection)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-181">The `*` operator may be used to perform pointer indirection ([Pointer indirection](unsafe-code.md#pointer-indirection)).</span></span>
*  <span data-ttu-id="c95ac-182">Il `->` operatore può essere utilizzato per accedere a un membro di uno struct tramite un puntatore ([accesso ai membri del puntatore](unsafe-code.md#pointer-member-access)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-182">The `->` operator may be used to access a member of a struct through a pointer ([Pointer member access](unsafe-code.md#pointer-member-access)).</span></span>
*  <span data-ttu-id="c95ac-183">Il `[]` operatore può essere utilizzato per indicizzare un puntatore ([accesso all'elemento puntatore](unsafe-code.md#pointer-element-access)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-183">The `[]` operator may be used to index a pointer ([Pointer element access](unsafe-code.md#pointer-element-access)).</span></span>
*  <span data-ttu-id="c95ac-184">Il `&` operatore può essere utilizzato per ottenere l'indirizzo di una variabile ([l'operatore address-of](unsafe-code.md#the-address-of-operator)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-184">The `&` operator may be used to obtain the address of a variable ([The address-of operator](unsafe-code.md#the-address-of-operator)).</span></span>
*  <span data-ttu-id="c95ac-185">Il `++` e `--` operatori possono essere usato per i puntatori di incremento e decremento ([puntatore incremento e decremento](unsafe-code.md#pointer-increment-and-decrement)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-185">The `++` and `--` operators may be used to increment and decrement pointers ([Pointer increment and decrement](unsafe-code.md#pointer-increment-and-decrement)).</span></span>
*  <span data-ttu-id="c95ac-186">Il `+` e `-` operatori possono essere utilizzati per eseguire l'aritmetica dei puntatori ([aritmetica dei puntatori](unsafe-code.md#pointer-arithmetic)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-186">The `+` and `-` operators may be used to perform pointer arithmetic ([Pointer arithmetic](unsafe-code.md#pointer-arithmetic)).</span></span>
*  <span data-ttu-id="c95ac-187">Il `==`, `!=`, `<`, `>`, `<=`, e `=>` operatori possono essere utilizzati per confrontare i puntatori ([confronto tra puntatori](unsafe-code.md#pointer-comparison)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-187">The `==`, `!=`, `<`, `>`, `<=`, and `=>` operators may be used to compare pointers ([Pointer comparison](unsafe-code.md#pointer-comparison)).</span></span>
*  <span data-ttu-id="c95ac-188">Il `stackalloc` operatore può essere utilizzato per allocare memoria dallo stack di chiamate ([buffer a dimensione fissa](unsafe-code.md#fixed-size-buffers)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-188">The `stackalloc` operator may be used to allocate memory from the call stack ([Fixed size buffers](unsafe-code.md#fixed-size-buffers)).</span></span>
*  <span data-ttu-id="c95ac-189">Il `fixed` istruzione può essere utilizzata per risolvere temporaneamente una variabile in modo che il relativo indirizzo può essere ottenuto ([l'istruzione fixed](unsafe-code.md#the-fixed-statement)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-189">The `fixed` statement may be used to temporarily fix a variable so its address can be obtained ([The fixed statement](unsafe-code.md#the-fixed-statement)).</span></span>

## <a name="fixed-and-moveable-variables"></a><span data-ttu-id="c95ac-190">Variabili fisse e mobili</span><span class="sxs-lookup"><span data-stu-id="c95ac-190">Fixed and moveable variables</span></span>

<span data-ttu-id="c95ac-191">L'operatore address-of ([l'operatore address-of](unsafe-code.md#the-address-of-operator)) e il `fixed` istruzione ([l'istruzione fixed](unsafe-code.md#the-fixed-statement)) consentono di dividere le variabili in due categorie: ***Fissa le variabili*** e ***variabili mobili***.</span><span class="sxs-lookup"><span data-stu-id="c95ac-191">The address-of operator ([The address-of operator](unsafe-code.md#the-address-of-operator)) and the `fixed` statement ([The fixed statement](unsafe-code.md#the-fixed-statement)) divide variables into two categories: ***Fixed variables*** and ***moveable variables***.</span></span>

<span data-ttu-id="c95ac-192">Variabili fisse si trovano in posizioni di archiviazione che non sono interessati dall'operazione di garbage collector.</span><span class="sxs-lookup"><span data-stu-id="c95ac-192">Fixed variables reside in storage locations that are unaffected by operation of the garbage collector.</span></span> <span data-ttu-id="c95ac-193">(Esempi di variabili fisse includono le variabili locali e parametri con valori di variabili create da dereferenziazione dei puntatori). D'altra parte, le variabili mobili si trovano in posizioni di archiviazione che sono soggetti a spostamenti o eliminazione tramite garbage collector.</span><span class="sxs-lookup"><span data-stu-id="c95ac-193">(Examples of fixed variables include local variables, value parameters, and variables created by dereferencing pointers.) On the other hand, moveable variables reside in storage locations that are subject to relocation or disposal by the garbage collector.</span></span> <span data-ttu-id="c95ac-194">(Esempi di variabili mobili includono campi negli elementi di matrici e oggetti).</span><span class="sxs-lookup"><span data-stu-id="c95ac-194">(Examples of moveable variables include fields in objects and elements of arrays.)</span></span>

<span data-ttu-id="c95ac-195">Il `&` operatore ([l'operatore address-of](unsafe-code.md#the-address-of-operator)) consente l'indirizzo di una variabile fissa devono essere ottenuti senza restrizioni.</span><span class="sxs-lookup"><span data-stu-id="c95ac-195">The `&` operator ([The address-of operator](unsafe-code.md#the-address-of-operator)) permits the address of a fixed variable to be obtained without restrictions.</span></span> <span data-ttu-id="c95ac-196">Tuttavia, poiché una variabile mobile è soggetti a spostamenti o eliminazione tramite garbage collector, l'indirizzo di una variabile mobile può essere ottenuta solo tramite un `fixed` istruzione ([l'istruzione fixed](unsafe-code.md#the-fixed-statement)) e tale indirizzo rimane valida solo per la durata di tale `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-196">However, because a moveable variable is subject to relocation or disposal by the garbage collector, the address of a moveable variable can only be obtained using a `fixed` statement ([The fixed statement](unsafe-code.md#the-fixed-statement)), and that address remains valid only for the duration of that `fixed` statement.</span></span>

<span data-ttu-id="c95ac-197">Più precisamente, una variabile fissa è uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="c95ac-197">In precise terms, a fixed variable is one of the following:</span></span>

*  <span data-ttu-id="c95ac-198">Una variabile risultante da un *simple_name* ([nomi semplici](expressions.md#simple-names)) che fa riferimento a una variabile locale o un parametro di valore, a meno che la variabile viene acquisita da una funzione anonima.</span><span class="sxs-lookup"><span data-stu-id="c95ac-198">A variable resulting from a *simple_name* ([Simple names](expressions.md#simple-names)) that refers to a local variable or a value parameter, unless the variable is captured by an anonymous function.</span></span>
*  <span data-ttu-id="c95ac-199">Una variabile risultante da un *member_access* ([l'accesso ai membri](expressions.md#member-access)) nel formato `V.I`, dove `V` variabile fisse di un *struct_type*.</span><span class="sxs-lookup"><span data-stu-id="c95ac-199">A variable resulting from a *member_access* ([Member access](expressions.md#member-access)) of the form `V.I`, where `V` is a fixed variable of a *struct_type*.</span></span>
*  <span data-ttu-id="c95ac-200">Una variabile risultante da un *pointer_indirection_expression* ([riferimento indiretto al puntatore](unsafe-code.md#pointer-indirection)) nel formato `*P`, un *pointer_member_access* ([Accesso ai membri del puntatore](unsafe-code.md#pointer-member-access)) nel formato `P->I`, o un *pointer_element_access* ([accesso all'elemento puntatore](unsafe-code.md#pointer-element-access)) nel formato `P[E]`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-200">A variable resulting from a *pointer_indirection_expression* ([Pointer indirection](unsafe-code.md#pointer-indirection)) of the form `*P`, a *pointer_member_access* ([Pointer member access](unsafe-code.md#pointer-member-access)) of the form `P->I`, or a *pointer_element_access* ([Pointer element access](unsafe-code.md#pointer-element-access)) of the form `P[E]`.</span></span>

<span data-ttu-id="c95ac-201">Tutte le altre variabili sono classificate come spostabile.</span><span class="sxs-lookup"><span data-stu-id="c95ac-201">All other variables are classified as moveable variables.</span></span>

<span data-ttu-id="c95ac-202">Si noti che un campo statico viene classificato come una variabile mobile.</span><span class="sxs-lookup"><span data-stu-id="c95ac-202">Note that a static field is classified as a moveable variable.</span></span> <span data-ttu-id="c95ac-203">Si noti anche che un `ref` o `out` parametro viene classificato come una variabile mobile, anche se l'argomento specificato per il parametro è una variabile fissa.</span><span class="sxs-lookup"><span data-stu-id="c95ac-203">Also note that a `ref` or `out` parameter is classified as a moveable variable, even if the argument given for the parameter is a fixed variable.</span></span> <span data-ttu-id="c95ac-204">Infine, si noti che una variabile prodotta da dereferenzia un puntatore sia sempre classificata come una variabile fissa.</span><span class="sxs-lookup"><span data-stu-id="c95ac-204">Finally, note that a variable produced by dereferencing a pointer is always classified as a fixed variable.</span></span>

## <a name="pointer-conversions"></a><span data-ttu-id="c95ac-205">Conversioni puntatore</span><span class="sxs-lookup"><span data-stu-id="c95ac-205">Pointer conversions</span></span>

<span data-ttu-id="c95ac-206">In un contesto unsafe, il set di conversioni implicite disponibili ([conversioni implicite](conversions.md#implicit-conversions)) è stato esteso per includere le seguenti conversioni di puntatori implicite:</span><span class="sxs-lookup"><span data-stu-id="c95ac-206">In an unsafe context, the set of available implicit conversions ([Implicit conversions](conversions.md#implicit-conversions)) is extended to include the following implicit pointer conversions:</span></span>

*  <span data-ttu-id="c95ac-207">Da qualsiasi *pointer_type* al tipo `void*`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-207">From any *pointer_type* to the type `void*`.</span></span>
*  <span data-ttu-id="c95ac-208">Dal `null` qualsiasi valore letterale *pointer_type*.</span><span class="sxs-lookup"><span data-stu-id="c95ac-208">From the `null` literal to any *pointer_type*.</span></span>

<span data-ttu-id="c95ac-209">Inoltre, in un contesto unsafe, il set di conversioni esplicite ([conversioni esplicite](conversions.md#explicit-conversions)) è stato esteso per includere le seguenti conversioni di puntatori esplicite:</span><span class="sxs-lookup"><span data-stu-id="c95ac-209">Additionally, in an unsafe context, the set of available explicit conversions ([Explicit conversions](conversions.md#explicit-conversions)) is extended to include the following explicit pointer conversions:</span></span>

*  <span data-ttu-id="c95ac-210">Da qualsiasi *pointer_type* a qualsiasi altra *pointer_type*.</span><span class="sxs-lookup"><span data-stu-id="c95ac-210">From any *pointer_type* to any other *pointer_type*.</span></span>
*  <span data-ttu-id="c95ac-211">Dal `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, o `ulong` a qualsiasi *pointer_type*.</span><span class="sxs-lookup"><span data-stu-id="c95ac-211">From `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, or `ulong` to any *pointer_type*.</span></span>
*  <span data-ttu-id="c95ac-212">Da qualsiasi *pointer_type* al `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, o `ulong`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-212">From any *pointer_type* to `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, or `ulong`.</span></span>

<span data-ttu-id="c95ac-213">Infine, in un contesto unsafe, il set di conversioni implicite ([conversioni implicite](conversions.md#standard-implicit-conversions)) include le seguenti conversioni puntatore:</span><span class="sxs-lookup"><span data-stu-id="c95ac-213">Finally, in an unsafe context, the set of standard implicit conversions ([Standard implicit conversions](conversions.md#standard-implicit-conversions)) includes the following pointer conversion:</span></span>

*  <span data-ttu-id="c95ac-214">Da qualsiasi *pointer_type* al tipo `void*`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-214">From any *pointer_type* to the type `void*`.</span></span>

<span data-ttu-id="c95ac-215">Le conversioni tra due tipi di puntatore non modificano mai il valore del puntatore effettivo.</span><span class="sxs-lookup"><span data-stu-id="c95ac-215">Conversions between two pointer types never change the actual pointer value.</span></span> <span data-ttu-id="c95ac-216">In altre parole, una conversione da un tipo puntatore a un altro non ha effetto sull'indirizzo specificato dal puntatore sottostante.</span><span class="sxs-lookup"><span data-stu-id="c95ac-216">In other words, a conversion from one pointer type to another has no effect on the underlying address given by the pointer.</span></span>

<span data-ttu-id="c95ac-217">Quando un tipo puntatore viene convertito in un altro, se il puntatore risultante non è correttamente allineato per il tipo di riferimento, il comportamento è definito se il risultato è dereferenziato.</span><span class="sxs-lookup"><span data-stu-id="c95ac-217">When one pointer type is converted to another, if the resulting pointer is not correctly aligned for the pointed-to type, the behavior is undefined if the result is dereferenced.</span></span> <span data-ttu-id="c95ac-218">In generale, il concetto di "correttamente allineato" è transitivo: se un puntatore al tipo `A` viene correttamente allineato per un puntatore al tipo `B`che, a sua volta, viene correttamente allineato per un puntatore al tipo `C`, quindi un puntatore a tipo `A`è correttamente allineato per un puntatore al tipo `C`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-218">In general, the concept "correctly aligned" is transitive: if a pointer to type `A` is correctly aligned for a pointer to type `B`, which, in turn, is correctly aligned for a pointer to type `C`, then a pointer to type `A` is correctly aligned for a pointer to type `C`.</span></span>

<span data-ttu-id="c95ac-219">Si consideri il caso seguente in cui una variabile con un tipo è accessibile tramite un puntatore a un tipo diverso:</span><span class="sxs-lookup"><span data-stu-id="c95ac-219">Consider the following case in which a variable having one type is accessed via a pointer to a different type:</span></span>

```csharp
char c = 'A';
char* pc = &c;
void* pv = pc;
int* pi = (int*)pv;
int i = *pi;         // undefined
*pi = 123456;        // undefined
```

<span data-ttu-id="c95ac-220">Quando un tipo puntatore viene convertito in un puntatore al byte, i punti di risultato per il byte più basso indirizzato della variabile.</span><span class="sxs-lookup"><span data-stu-id="c95ac-220">When a pointer type is converted to a pointer to byte, the result points to the lowest addressed byte of the variable.</span></span> <span data-ttu-id="c95ac-221">Incrementi successivi del risultato, fino alla dimensione della variabile, restituiscono puntatori ai byte rimanenti di tale variabile.</span><span class="sxs-lookup"><span data-stu-id="c95ac-221">Successive increments of the result, up to the size of the variable, yield pointers to the remaining bytes of that variable.</span></span> <span data-ttu-id="c95ac-222">Ad esempio, il metodo seguente consente di visualizzare ognuno degli otto byte in un valore double come valore esadecimale:</span><span class="sxs-lookup"><span data-stu-id="c95ac-222">For example, the following method displays each of the eight bytes in a double as a hexadecimal value:</span></span>

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

<span data-ttu-id="c95ac-223">Naturalmente, l'output del programma dipende dall'ordine dei byte.</span><span class="sxs-lookup"><span data-stu-id="c95ac-223">Of course, the output produced depends on endianness.</span></span>

<span data-ttu-id="c95ac-224">Mapping tra i puntatori e i numeri interi vengono definiti dall'implementazione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-224">Mappings between pointers and integers are implementation-defined.</span></span> <span data-ttu-id="c95ac-225">Tuttavia, in 32 \* e architetture della CPU a 64 bit con uno spazio degli indirizzi lineare, le conversioni dei puntatori verso o dai tipi integrali si comportano in genere esattamente come le conversioni delle `uint` o `ulong` valori, rispettivamente, da o verso tali tipi integrali.</span><span class="sxs-lookup"><span data-stu-id="c95ac-225">However, on 32\* and 64-bit CPU architectures with a linear address space, conversions of pointers to or from integral types typically behave exactly like conversions of `uint` or `ulong` values, respectively, to or from those integral types.</span></span>

### <a name="pointer-arrays"></a><span data-ttu-id="c95ac-226">Matrici di puntatori</span><span class="sxs-lookup"><span data-stu-id="c95ac-226">Pointer arrays</span></span>

<span data-ttu-id="c95ac-227">In un contesto unsafe, è possibile costruire le matrici di puntatori.</span><span class="sxs-lookup"><span data-stu-id="c95ac-227">In an unsafe context, arrays of pointers can be constructed.</span></span> <span data-ttu-id="c95ac-228">Solo alcune delle conversioni che si applicano ad altri tipi di matrice sono consentite nelle matrici di puntatore:</span><span class="sxs-lookup"><span data-stu-id="c95ac-228">Only some of the conversions that apply to other array types are allowed on pointer arrays:</span></span>

*  <span data-ttu-id="c95ac-229">La conversione implicita del riferimento ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions)) da qualsiasi *array_type* a `System.Array` e le interfacce implementate anche si applica alle matrici di puntatori.</span><span class="sxs-lookup"><span data-stu-id="c95ac-229">The implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) from any *array_type* to `System.Array` and the interfaces it implements also applies to pointer arrays.</span></span> <span data-ttu-id="c95ac-230">Tuttavia, qualsiasi tentativo di accedere a elementi della matrice tramite `System.Array` o le interfacce implementate comporterà un'eccezione in fase di esecuzione, come i tipi di puntatore non sono convertibili in `object`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-230">However, any attempt to access the array elements through `System.Array` or the interfaces it implements will result in an exception at run-time, as pointer types are not convertible to `object`.</span></span>
*  <span data-ttu-id="c95ac-231">Conversioni di riferimenti di implicita ed esplicita ([le conversioni dei riferimenti implicita](conversions.md#implicit-reference-conversions), [le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)) da un tipo di matrice unidimensionale `S[]` a `System.Collections.Generic.IList<T>` e le interfacce di base generiche senza mai applicano alle matrici di puntatori, poiché i tipi di puntatore non possono essere utilizzati come argomenti tipo e non sono presenti conversioni da tipi di puntatori a tipi non puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-231">The implicit and explicit reference conversions ([Implicit reference conversions](conversions.md#implicit-reference-conversions), [Explicit reference conversions](conversions.md#explicit-reference-conversions)) from a single-dimensional array type `S[]` to `System.Collections.Generic.IList<T>` and its generic base interfaces never apply to pointer arrays, since pointer types cannot be used as type arguments, and there are no conversions from pointer types to non-pointer types.</span></span>
*  <span data-ttu-id="c95ac-232">La conversione di riferimenti esplicita ([le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)) da `System.Array` e le interfacce implementate in qualsiasi *array_type* si applica alle matrici di puntatori.</span><span class="sxs-lookup"><span data-stu-id="c95ac-232">The explicit reference conversion ([Explicit reference conversions](conversions.md#explicit-reference-conversions)) from `System.Array` and the interfaces it implements to any *array_type* applies to pointer arrays.</span></span>
*  <span data-ttu-id="c95ac-233">Le conversioni di riferimento esplicito ([le conversioni dei riferimenti espliciti](conversions.md#explicit-reference-conversions)) da `System.Collections.Generic.IList<S>` e interfacce di base a un tipo di matrice unidimensionale `T[]` mai si applica alle matrici di puntatori, poiché i tipi di puntatore non possono essere usati come argomenti tipo e non sono presenti conversioni da tipi di puntatori a tipi non puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-233">The explicit reference conversions ([Explicit reference conversions](conversions.md#explicit-reference-conversions)) from `System.Collections.Generic.IList<S>` and its base interfaces to a single-dimensional array type `T[]` never applies to pointer arrays, since pointer types cannot be used as type arguments, and there are no conversions from pointer types to non-pointer types.</span></span>

<span data-ttu-id="c95ac-234">Queste restrizioni significa che l'espansione per il `foreach` istruzione sulle matrici come descritto in [l'istruzione foreach](statements.md#the-foreach-statement) non può essere applicato alle matrici di puntatori.</span><span class="sxs-lookup"><span data-stu-id="c95ac-234">These restrictions mean that the expansion for the `foreach` statement over arrays described in [The foreach statement](statements.md#the-foreach-statement) cannot be applied to pointer arrays.</span></span> <span data-ttu-id="c95ac-235">Al contrario, un'istruzione foreach del form</span><span class="sxs-lookup"><span data-stu-id="c95ac-235">Instead, a foreach statement of the form</span></span>

```csharp
foreach (V v in x) embedded_statement
```

<span data-ttu-id="c95ac-236">in cui il tipo della `x` è un tipo di matrice nel formato `T[,,...,]`, `N` è il numero di dimensioni meno 1 e `T` o `V` è un tipo di puntatore, viene espanso con cicli for annidati, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="c95ac-236">where the type of `x` is an array type of the form `T[,,...,]`, `N` is the number of dimensions minus 1 and `T` or `V` is a pointer type, is expanded using nested for-loops as follows:</span></span>

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

<span data-ttu-id="c95ac-237">Le variabili `a`, `i0`, `i1`,..., `iN` non sono visibile e accessibile al `x` o il *embedded_statement* o qualsiasi altro codice sorgente del programma.</span><span class="sxs-lookup"><span data-stu-id="c95ac-237">The variables `a`, `i0`, `i1`, ..., `iN` are not visible to or accessible to `x` or the *embedded_statement* or any other source code of the program.</span></span> <span data-ttu-id="c95ac-238">La variabile `v` è di sola lettura in un'istruzione incorporata.</span><span class="sxs-lookup"><span data-stu-id="c95ac-238">The variable `v` is read-only in the embedded statement.</span></span> <span data-ttu-id="c95ac-239">Se non esiste una conversione esplicita ([conversioni di puntatori](unsafe-code.md#pointer-conversions)) da `T` (tipo di elemento) per `V`, viene generato un errore e viene eseguita alcuna operazione ulteriormente.</span><span class="sxs-lookup"><span data-stu-id="c95ac-239">If there is not an explicit conversion ([Pointer conversions](unsafe-code.md#pointer-conversions)) from `T` (the element type) to `V`, an error is produced and no further steps are taken.</span></span> <span data-ttu-id="c95ac-240">Se `x` ha il valore `null`, un `System.NullReferenceException` viene generata un'eccezione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-240">If `x` has the value `null`, a `System.NullReferenceException` is thrown at run-time.</span></span>

## <a name="pointers-in-expressions"></a><span data-ttu-id="c95ac-241">Puntatori nelle espressioni</span><span class="sxs-lookup"><span data-stu-id="c95ac-241">Pointers in expressions</span></span>

<span data-ttu-id="c95ac-242">In un contesto unsafe, un'espressione può restituire un risultato di un tipo di puntatore, ma all'esterno di un contesto non sicuro è un errore in fase di compilazione per un'espressione di tipo puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-242">In an unsafe context, an expression may yield a result of a pointer type, but outside an unsafe context it is a compile-time error for an expression to be of a pointer type.</span></span> <span data-ttu-id="c95ac-243">Più precisamente, all'esterno di un contesto non sicuro in fase di compilazione si verifica un errore se eventuali *simple_name* ([nomi semplici](expressions.md#simple-names)), *member_access* ([accesso ai membri ](expressions.md#member-access)), *invocation_expression* ([espressioni di chiamata](expressions.md#invocation-expressions)), oppure *element_access* ([accesso all'elemento](expressions.md#element-access)) è di tipo puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-243">In precise terms, outside an unsafe context a compile-time error occurs if any *simple_name* ([Simple names](expressions.md#simple-names)), *member_access* ([Member access](expressions.md#member-access)), *invocation_expression* ([Invocation expressions](expressions.md#invocation-expressions)), or *element_access* ([Element access](expressions.md#element-access)) is of a pointer type.</span></span>

<span data-ttu-id="c95ac-244">In un contesto unsafe, il *primary_no_array_creation_expression* ([espressioni primarie](expressions.md#primary-expressions)) e *unary_expression* ([operatori unari](expressions.md#unary-operators)) Productions consentono i seguenti costrutti aggiuntivi:</span><span class="sxs-lookup"><span data-stu-id="c95ac-244">In an unsafe context, the *primary_no_array_creation_expression* ([Primary expressions](expressions.md#primary-expressions)) and *unary_expression* ([Unary operators](expressions.md#unary-operators)) productions permit the following additional constructs:</span></span>

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

<span data-ttu-id="c95ac-245">Questi costrutti sono descritte nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="c95ac-245">These constructs are described in the following sections.</span></span> <span data-ttu-id="c95ac-246">La precedenza e associatività degli operatori di tipo unsafe è implicita la grammatica.</span><span class="sxs-lookup"><span data-stu-id="c95ac-246">The precedence and associativity of the unsafe operators is implied by the grammar.</span></span>

### <a name="pointer-indirection"></a><span data-ttu-id="c95ac-247">Riferimento indiretto a puntatore</span><span class="sxs-lookup"><span data-stu-id="c95ac-247">Pointer indirection</span></span>

<span data-ttu-id="c95ac-248">Oggetto *pointer_indirection_expression* costituito da un asterisco (`*`) seguito da una *unary_expression*.</span><span class="sxs-lookup"><span data-stu-id="c95ac-248">A *pointer_indirection_expression* consists of an asterisk (`*`) followed by a *unary_expression*.</span></span>

```antlr
pointer_indirection_expression
    : '*' unary_expression
    ;
```

<span data-ttu-id="c95ac-249">Operatore unario `*` operatore denota riferimento indiretto al puntatore e viene usato per ottenere la variabile a cui punta il puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-249">The unary `*` operator denotes pointer indirection and is used to obtain the variable to which a pointer points.</span></span> <span data-ttu-id="c95ac-250">Il risultato della valutazione `*P`, dove `P` è un'espressione di tipo puntatore `T*`, è una variabile di tipo `T`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-250">The result of evaluating `*P`, where `P` is an expression of a pointer type `T*`, is a variable of type `T`.</span></span> <span data-ttu-id="c95ac-251">Tratta di un errore in fase di compilazione per applicare l'operatore unario `*` operatore da un'espressione di tipo `void*` o su un'espressione che non è di tipo puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-251">It is a compile-time error to apply the unary `*` operator to an expression of type `void*` or to an expression that isn't of a pointer type.</span></span>

<span data-ttu-id="c95ac-252">L'effetto dell'applicazione con operatore unario `*` operatore da un `null` puntatore è definito dall'implementazione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-252">The effect of applying the unary `*` operator to a `null` pointer is implementation-defined.</span></span> <span data-ttu-id="c95ac-253">In particolare, non c'è garanzia che questa operazione genera un `System.NullReferenceException`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-253">In particular, there is no guarantee that this operation throws a `System.NullReferenceException`.</span></span>

<span data-ttu-id="c95ac-254">Se è stato assegnato un valore non valido per il puntatore, il comportamento dell'operatore unario `*` operatore non è definito.</span><span class="sxs-lookup"><span data-stu-id="c95ac-254">If an invalid value has been assigned to the pointer, the behavior of the unary `*` operator is undefined.</span></span> <span data-ttu-id="c95ac-255">Tra i valori non validi per dereferenzia un puntatore da operatore unario `*` operatore sono un indirizzo allineato in modo non appropriato per il tipo a cui (vedere l'esempio nella [conversioni di puntatori](unsafe-code.md#pointer-conversions)) e l'indirizzo di una variabile dopo il fine della sua durata.</span><span class="sxs-lookup"><span data-stu-id="c95ac-255">Among the invalid values for dereferencing a pointer by the unary `*` operator are an address inappropriately aligned for the type pointed to (see example in [Pointer conversions](unsafe-code.md#pointer-conversions)), and the address of a variable after the end of its lifetime.</span></span>

<span data-ttu-id="c95ac-256">Ai fini dell'assegnazione definita analisi, una variabile prodotti dalla valutazione di un'espressione nel formato `*P` viene considerato come inizialmente assegnato ([inizialmente assegnata variabili](variables.md#initially-assigned-variables)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-256">For purposes of definite assignment analysis, a variable produced by evaluating an expression of the form `*P` is considered initially assigned ([Initially assigned variables](variables.md#initially-assigned-variables)).</span></span>

### <a name="pointer-member-access"></a><span data-ttu-id="c95ac-257">Accesso ai membri del puntatore</span><span class="sxs-lookup"><span data-stu-id="c95ac-257">Pointer member access</span></span>

<span data-ttu-id="c95ac-258">Oggetto *pointer_member_access* è costituito da un *primary_expression*, seguito da un "`->`" token, seguito da un *identificatore* e un facoltativo*type_argument_list*.</span><span class="sxs-lookup"><span data-stu-id="c95ac-258">A *pointer_member_access* consists of a *primary_expression*, followed by a "`->`" token, followed by an *identifier* and an optional *type_argument_list*.</span></span>

```antlr
pointer_member_access
    : primary_expression '->' identifier
    ;
```

<span data-ttu-id="c95ac-259">In un accesso ai membri del puntatore del form `P->I`, `P` deve essere diverso da un'espressione di tipo puntatore `void*`, e `I` deve indicare un membro accessibile del tipo a cui `P` punti.</span><span class="sxs-lookup"><span data-stu-id="c95ac-259">In a pointer member access of the form `P->I`, `P` must be an expression of a pointer type other than `void*`, and `I` must denote an accessible member of the type to which `P` points.</span></span>

<span data-ttu-id="c95ac-260">Un accesso ai membri del puntatore del form `P->I` viene valutata esattamente come `(*P).I`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-260">A pointer member access of the form `P->I` is evaluated exactly as `(*P).I`.</span></span> <span data-ttu-id="c95ac-261">Per una descrizione dell'operatore di riferimento indiretto del puntatore (`*`), vedere [riferimento indiretto al puntatore](unsafe-code.md#pointer-indirection).</span><span class="sxs-lookup"><span data-stu-id="c95ac-261">For a description of the pointer indirection operator (`*`), see [Pointer indirection](unsafe-code.md#pointer-indirection).</span></span> <span data-ttu-id="c95ac-262">Per una descrizione dell'operatore di accesso membri (`.`), vedere [l'accesso ai membri](expressions.md#member-access).</span><span class="sxs-lookup"><span data-stu-id="c95ac-262">For a description of the member access operator (`.`), see [Member access](expressions.md#member-access).</span></span>

<span data-ttu-id="c95ac-263">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="c95ac-263">In the example</span></span>

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

<span data-ttu-id="c95ac-264">il `->` operatore viene usato per accedere ai campi e richiamare un metodo di uno struct tramite un puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-264">the `->` operator is used to access fields and invoke a method of a struct through a pointer.</span></span> <span data-ttu-id="c95ac-265">Poiché l'operazione `P->I` equivale esattamente alla `(*P).I`, il `Main` possibile scrivere metodo altrettanto bene:</span><span class="sxs-lookup"><span data-stu-id="c95ac-265">Because the operation `P->I` is precisely equivalent to `(*P).I`, the `Main` method could equally well have been written:</span></span>

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

### <a name="pointer-element-access"></a><span data-ttu-id="c95ac-266">Accesso agli elementi di puntatore</span><span class="sxs-lookup"><span data-stu-id="c95ac-266">Pointer element access</span></span>

<span data-ttu-id="c95ac-267">Oggetto *pointer_element_access* costituito da un *primary_no_array_creation_expression* seguita da un'espressione racchiusa tra "`[`"e"`]`".</span><span class="sxs-lookup"><span data-stu-id="c95ac-267">A *pointer_element_access* consists of a *primary_no_array_creation_expression* followed by an expression enclosed in "`[`" and "`]`".</span></span>

```antlr
pointer_element_access
    : primary_no_array_creation_expression '[' expression ']'
    ;
```

<span data-ttu-id="c95ac-268">Nell'accesso all'elemento puntatore nel formato `P[E]`, `P` deve essere diverso da un'espressione di tipo puntatore `void*`, e `E` deve essere un'espressione che può essere convertita in modo implicito in `int`, `uint`, `long`, o `ulong`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-268">In a pointer element access of the form `P[E]`, `P` must be an expression of a pointer type other than `void*`, and `E` must be an expression that can be implicitly converted to `int`, `uint`, `long`, or `ulong`.</span></span>

<span data-ttu-id="c95ac-269">Accesso all'elemento un puntatore nel formato `P[E]` viene valutata esattamente come `*(P + E)`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-269">A pointer element access of the form `P[E]` is evaluated exactly as `*(P + E)`.</span></span> <span data-ttu-id="c95ac-270">Per una descrizione dell'operatore di riferimento indiretto del puntatore (`*`), vedere [riferimento indiretto al puntatore](unsafe-code.md#pointer-indirection).</span><span class="sxs-lookup"><span data-stu-id="c95ac-270">For a description of the pointer indirection operator (`*`), see [Pointer indirection](unsafe-code.md#pointer-indirection).</span></span> <span data-ttu-id="c95ac-271">Per una descrizione dell'operatore di addizione puntatore (`+`), vedere [aritmetica dei puntatori](unsafe-code.md#pointer-arithmetic).</span><span class="sxs-lookup"><span data-stu-id="c95ac-271">For a description of the pointer addition operator (`+`), see [Pointer arithmetic](unsafe-code.md#pointer-arithmetic).</span></span>

<span data-ttu-id="c95ac-272">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="c95ac-272">In the example</span></span>

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

<span data-ttu-id="c95ac-273">accesso all'elemento un puntatore utilizzato per inizializzare il buffer di caratteri in un `for` ciclo.</span><span class="sxs-lookup"><span data-stu-id="c95ac-273">a pointer element access is used to initialize the character buffer in a `for` loop.</span></span> <span data-ttu-id="c95ac-274">Poiché l'operazione `P[E]` equivale esattamente alla `*(P + E)`, nell'esempio è stato altrettanto bene sono stati scritti:</span><span class="sxs-lookup"><span data-stu-id="c95ac-274">Because the operation `P[E]` is precisely equivalent to `*(P + E)`, the example could equally well have been written:</span></span>

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

<span data-ttu-id="c95ac-275">L'operatore di accesso di elemento di puntatore non controlla oltre i limiti per gli errori e il comportamento quando si accede a un non compresi nell'intervallo elemento è definito.</span><span class="sxs-lookup"><span data-stu-id="c95ac-275">The pointer element access operator does not check for out-of-bounds errors and the behavior when accessing an out-of-bounds element is undefined.</span></span> <span data-ttu-id="c95ac-276">Questo è lo stesso come C e C++.</span><span class="sxs-lookup"><span data-stu-id="c95ac-276">This is the same as C and C++.</span></span>

### <a name="the-address-of-operator"></a><span data-ttu-id="c95ac-277">L'operatore address-of</span><span class="sxs-lookup"><span data-stu-id="c95ac-277">The address-of operator</span></span>

<span data-ttu-id="c95ac-278">Un' *addressof_expression* costituito da una e commerciale (`&`) seguito da una *unary_expression*.</span><span class="sxs-lookup"><span data-stu-id="c95ac-278">An *addressof_expression* consists of an ampersand (`&`) followed by a *unary_expression*.</span></span>

```antlr
addressof_expression
    : '&' unary_expression
    ;
```

<span data-ttu-id="c95ac-279">Data un'espressione `E` che è di tipo `T` ed è classificato come una variabile fissa ([variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)), il costrutto `&E` calcola l'indirizzo della variabile specificata dalla `E`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-279">Given an expression `E` which is of a type `T` and is classified as a fixed variable ([Fixed and moveable variables](unsafe-code.md#fixed-and-moveable-variables)), the construct `&E` computes the address of the variable given by `E`.</span></span> <span data-ttu-id="c95ac-280">Il tipo del risultato è `T*` ed è classificato come un valore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-280">The type of the result is `T*` and is classified as a value.</span></span> <span data-ttu-id="c95ac-281">Si verifica un errore in fase di compilazione se `E` non è classificato come una variabile, se `E` viene classificato come una variabile locale di sola lettura, o se `E` denota una variabile mobile.</span><span class="sxs-lookup"><span data-stu-id="c95ac-281">A compile-time error occurs if `E` is not classified as a variable, if `E` is classified as a read-only local variable, or if `E` denotes a moveable variable.</span></span> <span data-ttu-id="c95ac-282">Nell'ultimo caso, un'istruzione fixed ([l'istruzione fixed](unsafe-code.md#the-fixed-statement)) può essere utilizzato per temporaneamente "risolvere" la variabile prima di ottenerne l'indirizzo.</span><span class="sxs-lookup"><span data-stu-id="c95ac-282">In the last case, a fixed statement ([The fixed statement](unsafe-code.md#the-fixed-statement)) can be used to temporarily "fix" the variable before obtaining its address.</span></span> <span data-ttu-id="c95ac-283">Come indicato nella [accesso al membro](expressions.md#member-access), all'esterno di un costruttore di istanza o un costruttore statico per uno struct o una classe che definisce un `readonly` campo, tale campo viene considerato come un valore, non una variabile.</span><span class="sxs-lookup"><span data-stu-id="c95ac-283">As stated in [Member access](expressions.md#member-access), outside an instance constructor or static constructor for a struct or class that defines a `readonly` field, that field is considered a value, not a variable.</span></span> <span data-ttu-id="c95ac-284">Di conseguenza, non è possibile accettarne l'indirizzo.</span><span class="sxs-lookup"><span data-stu-id="c95ac-284">As such, its address cannot be taken.</span></span> <span data-ttu-id="c95ac-285">Analogamente, l'indirizzo di una costante non può essere accettato.</span><span class="sxs-lookup"><span data-stu-id="c95ac-285">Similarly, the address of a constant cannot be taken.</span></span>

<span data-ttu-id="c95ac-286">Il `&` operatore non richiede il relativo argomento sia assegnata, ma segue un `&` operazione, la variabile a cui viene applicato l'operatore viene considerata assegnata nel percorso di esecuzione in cui l'operazione viene eseguita.</span><span class="sxs-lookup"><span data-stu-id="c95ac-286">The `&` operator does not require its argument to be definitely assigned, but following an `&` operation, the variable to which the operator is applied is considered definitely assigned in the execution path in which the operation occurs.</span></span> <span data-ttu-id="c95ac-287">È responsabilità del programmatore assicurare che l'inizializzazione corretta della variabile vengono effettivamente eseguite in questa situazione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-287">It is the responsibility of the programmer to ensure that correct initialization of the variable actually does take place in this situation.</span></span>

<span data-ttu-id="c95ac-288">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="c95ac-288">In the example</span></span>

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

<span data-ttu-id="c95ac-289">`i` viene considerata assegnata seguendo la `&i` operazione consente di inizializzare `p`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-289">`i` is considered definitely assigned following the `&i` operation used to initialize `p`.</span></span> <span data-ttu-id="c95ac-290">L'assegnazione al `*p` Inizializza in vigore `i`, ma l'inclusione di questa inizializzazione è responsabilità del programmatore e si verificherebbe alcun errore in fase di compilazione se è stata rimossa l'assegnazione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-290">The assignment to `*p` in effect initializes `i`, but the inclusion of this initialization is the responsibility of the programmer, and no compile-time error would occur if the assignment was removed.</span></span>

<span data-ttu-id="c95ac-291">Le regole di assegnazione definite per il `&` operatore esiste in modo tale che può essere evitato ridondante inizializzazione delle variabili locali.</span><span class="sxs-lookup"><span data-stu-id="c95ac-291">The rules of definite assignment for the `&` operator exist such that redundant initialization of local variables can be avoided.</span></span> <span data-ttu-id="c95ac-292">Ad esempio, molte API esterne accettano un puntatore a una struttura di cui viene compilato tramite l'API.</span><span class="sxs-lookup"><span data-stu-id="c95ac-292">For example, many external APIs take a pointer to a structure which is filled in by the API.</span></span> <span data-ttu-id="c95ac-293">Le chiamate a tali API in genere passano l'indirizzo di una variabile locale di struct e senza la regola, l'inizializzazione con ridondanza della variabile di uno struct sarebbero necessario.</span><span class="sxs-lookup"><span data-stu-id="c95ac-293">Calls to such APIs typically pass the address of a local struct variable, and without the rule, redundant initialization of the struct variable would be required.</span></span>

### <a name="pointer-increment-and-decrement"></a><span data-ttu-id="c95ac-294">Decremento e incremento di puntatore</span><span class="sxs-lookup"><span data-stu-id="c95ac-294">Pointer increment and decrement</span></span>

<span data-ttu-id="c95ac-295">In un contesto unsafe, il `++` e `--` operatori ([forma suffissa di incremento e decremento (operatori)](expressions.md#postfix-increment-and-decrement-operators) e [incremento prefisso e decremento (operatori)](expressions.md#prefix-increment-and-decrement-operators)) possono essere applicati a puntatore le variabili di tutti i tipi eccetto `void*`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-295">In an unsafe context, the `++` and `--` operators ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators) and [Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)) can be applied to pointer variables of all types except `void*`.</span></span> <span data-ttu-id="c95ac-296">Pertanto, per ogni tipo di puntatore `T*`, gli operatori seguenti vengono definiti in modo implicito:</span><span class="sxs-lookup"><span data-stu-id="c95ac-296">Thus, for every pointer type `T*`, the following operators are implicitly defined:</span></span>

```csharp
T* operator ++(T* x);
T* operator --(T* x);
```

<span data-ttu-id="c95ac-297">Gli operatori generano gli stessi risultati `x + 1` e `x - 1`, rispettivamente ([aritmetica dei puntatori](unsafe-code.md#pointer-arithmetic)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-297">The operators produce the same results as `x + 1` and `x - 1`, respectively ([Pointer arithmetic](unsafe-code.md#pointer-arithmetic)).</span></span> <span data-ttu-id="c95ac-298">In altre parole, per una variabile puntatore di tipo `T*`, il `++` operatore aggiunge `sizeof(T)` all'indirizzo contenuto nella variabile e il `--` operatore sottrae `sizeof(T)` dall'indirizzo contenuto nella variabile.</span><span class="sxs-lookup"><span data-stu-id="c95ac-298">In other words, for a pointer variable of type `T*`, the `++` operator adds `sizeof(T)` to the address contained in the variable, and the `--` operator subtracts `sizeof(T)` from the address contained in the variable.</span></span>

<span data-ttu-id="c95ac-299">Se un incremento o decremento operazione supera il dominio del tipo di puntatore, il risultato è definito dall'implementazione, ma non verranno generate eccezioni.</span><span class="sxs-lookup"><span data-stu-id="c95ac-299">If a pointer increment or decrement operation overflows the domain of the pointer type, the result is implementation-defined, but no exceptions are produced.</span></span>

### <a name="pointer-arithmetic"></a><span data-ttu-id="c95ac-300">Puntatore aritmetico</span><span class="sxs-lookup"><span data-stu-id="c95ac-300">Pointer arithmetic</span></span>

<span data-ttu-id="c95ac-301">In un contesto unsafe, il `+` e `-` operatori ([operatore di addizione](expressions.md#addition-operator) e [operatore di sottrazione](expressions.md#subtraction-operator)) possono essere applicati ai valori di tutti i tipi di puntatore, ad eccezione `void*`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-301">In an unsafe context, the `+` and `-` operators ([Addition operator](expressions.md#addition-operator) and [Subtraction operator](expressions.md#subtraction-operator)) can be applied to values of all pointer types except `void*`.</span></span> <span data-ttu-id="c95ac-302">Pertanto, per ogni tipo di puntatore `T*`, gli operatori seguenti vengono definiti in modo implicito:</span><span class="sxs-lookup"><span data-stu-id="c95ac-302">Thus, for every pointer type `T*`, the following operators are implicitly defined:</span></span>

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

<span data-ttu-id="c95ac-303">Data un'espressione `P` di un tipo di puntatore `T*` e un'espressione `N` typu `int`, `uint`, `long`, o `ulong`, le espressioni `P + N` e `N + P` calcolo il valore del puntatore di tipo `T*` risultante dalla somma `N * sizeof(T)` all'indirizzo specificato da `P`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-303">Given an expression `P` of a pointer type `T*` and an expression `N` of type `int`, `uint`, `long`, or `ulong`, the expressions `P + N` and `N + P` compute the pointer value of type `T*` that results from adding `N * sizeof(T)` to the address given by `P`.</span></span> <span data-ttu-id="c95ac-304">Analogamente, l'espressione `P - N` calcola il valore del puntatore di tipo `T*` risultante dalla sottrazione `N * sizeof(T)` rispetto all'indirizzo specificato da `P`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-304">Likewise, the expression `P - N` computes the pointer value of type `T*` that results from subtracting `N * sizeof(T)` from the address given by `P`.</span></span>

<span data-ttu-id="c95ac-305">Due espressioni, specificate `P` e `Q`, di un tipo di puntatore `T*`, l'espressione `P - Q` calcola la differenza tra gli indirizzi forniti dagli `P` e `Q` e quindi divide tale differenza per `sizeof(T)`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-305">Given two expressions, `P` and `Q`, of a pointer type `T*`, the expression `P - Q` computes the difference between the addresses given by `P` and `Q` and then divides that difference by `sizeof(T)`.</span></span> <span data-ttu-id="c95ac-306">Il tipo del risultato è sempre `long`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-306">The type of the result is always `long`.</span></span> <span data-ttu-id="c95ac-307">In effetti `P - Q` viene calcolato come `((long)(P) - (long)(Q)) / sizeof(T)`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-307">In effect, `P - Q` is computed as `((long)(P) - (long)(Q)) / sizeof(T)`.</span></span>

<span data-ttu-id="c95ac-308">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c95ac-308">For example:</span></span>

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

<span data-ttu-id="c95ac-309">l'output ottenuto sarà:</span><span class="sxs-lookup"><span data-stu-id="c95ac-309">which produces the output:</span></span>

```
p - q = -14
q - p = 14
```

<span data-ttu-id="c95ac-310">Se un'operazione aritmetica puntatore supera il dominio del tipo di puntatore, il risultato viene troncato in modo definito dall'implementazione, ma non verranno generate eccezioni.</span><span class="sxs-lookup"><span data-stu-id="c95ac-310">If a pointer arithmetic operation overflows the domain of the pointer type, the result is truncated in an implementation-defined fashion, but no exceptions are produced.</span></span>

### <a name="pointer-comparison"></a><span data-ttu-id="c95ac-311">Confronto tra puntatori</span><span class="sxs-lookup"><span data-stu-id="c95ac-311">Pointer comparison</span></span>

<span data-ttu-id="c95ac-312">In un contesto unsafe, il `==`, `!=`, `<`, `>`, `<=`, e `=>` operatori ([relazionali e operatori di test del tipo](expressions.md#relational-and-type-testing-operators)) possono essere applicati ai valori di tutti tipi di puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-312">In an unsafe context, the `==`, `!=`, `<`, `>`, `<=`, and `=>` operators ([Relational and type-testing operators](expressions.md#relational-and-type-testing-operators)) can be applied to values of all pointer types.</span></span> <span data-ttu-id="c95ac-313">Gli operatori di confronto del puntatore sono:</span><span class="sxs-lookup"><span data-stu-id="c95ac-313">The pointer comparison operators are:</span></span>

```csharp
bool operator ==(void* x, void* y);
bool operator !=(void* x, void* y);
bool operator <(void* x, void* y);
bool operator >(void* x, void* y);
bool operator <=(void* x, void* y);
bool operator >=(void* x, void* y);
```

<span data-ttu-id="c95ac-314">Poiché esiste una conversione implicita da qualsiasi tipo di puntatore per il `void*` tipo, gli operandi di tipo puntatore può essere confrontato utilizzando questi operatori.</span><span class="sxs-lookup"><span data-stu-id="c95ac-314">Because an implicit conversion exists from any pointer type to the `void*` type, operands of any pointer type can be compared using these operators.</span></span> <span data-ttu-id="c95ac-315">Gli operatori di confronto confrontano l'indirizzi specificati da due operandi come se fossero unsigned integer.</span><span class="sxs-lookup"><span data-stu-id="c95ac-315">The comparison operators compare the addresses given by the two operands as if they were unsigned integers.</span></span>

### <a name="the-sizeof-operator"></a><span data-ttu-id="c95ac-316">L'operatore sizeof</span><span class="sxs-lookup"><span data-stu-id="c95ac-316">The sizeof operator</span></span>

<span data-ttu-id="c95ac-317">Il `sizeof` operatore restituisce il numero di byte occupati da una variabile di un determinato tipo.</span><span class="sxs-lookup"><span data-stu-id="c95ac-317">The `sizeof` operator returns the number of bytes occupied by a variable of a given type.</span></span> <span data-ttu-id="c95ac-318">Il tipo specificato come un operando `sizeof` deve essere un *unmanaged_type* ([tipi di puntatore](unsafe-code.md#pointer-types)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-318">The type specified as an operand to `sizeof` must be an *unmanaged_type* ([Pointer types](unsafe-code.md#pointer-types)).</span></span>

```antlr
sizeof_expression
    : 'sizeof' '(' unmanaged_type ')'
    ;
```

<span data-ttu-id="c95ac-319">Il risultato del `sizeof` operatore è un valore di tipo `int`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-319">The result of the `sizeof` operator is a value of type `int`.</span></span> <span data-ttu-id="c95ac-320">Per determinati i tipi predefiniti, il `sizeof` operatore restituisce un valore costante, come illustrato nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="c95ac-320">For certain predefined types, the `sizeof` operator yields a constant value as shown in the table below.</span></span>


| <span data-ttu-id="c95ac-321">__Espressione__</span><span class="sxs-lookup"><span data-stu-id="c95ac-321">__Expression__</span></span>   | <span data-ttu-id="c95ac-322">__Risultato__</span><span class="sxs-lookup"><span data-stu-id="c95ac-322">__Result__</span></span> |
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

<span data-ttu-id="c95ac-323">Per tutti gli altri tipi, il risultato del `sizeof` l'operatore definito dall'implementazione ed è classificato come un valore, non una costante.</span><span class="sxs-lookup"><span data-stu-id="c95ac-323">For all other types, the result of the `sizeof` operator is implementation-defined and is classified as a value, not a constant.</span></span>

<span data-ttu-id="c95ac-324">L'ordine in cui i membri vengono raggruppati in uno struct non è specificato.</span><span class="sxs-lookup"><span data-stu-id="c95ac-324">The order in which members are packed into a struct is unspecified.</span></span>

<span data-ttu-id="c95ac-325">Ai fini dell'allineamento, potrebbe esserci riempimento all'inizio di uno struct, all'interno di uno struct e alla fine dello struct senza nome.</span><span class="sxs-lookup"><span data-stu-id="c95ac-325">For alignment purposes, there may be unnamed padding at the beginning of a struct, within a struct, and at the end of the struct.</span></span> <span data-ttu-id="c95ac-326">Il contenuto dei bit usato come spaziatura interna è indeterminato.</span><span class="sxs-lookup"><span data-stu-id="c95ac-326">The contents of the bits used as padding are indeterminate.</span></span>

<span data-ttu-id="c95ac-327">Quando applicato a un operando con il tipo di struct, il risultato è il numero totale di byte in una variabile di quel tipo, include eventuali spaziature interne.</span><span class="sxs-lookup"><span data-stu-id="c95ac-327">When applied to an operand that has struct type, the result is the total number of bytes in a variable of that type, including any padding.</span></span>

## <a name="the-fixed-statement"></a><span data-ttu-id="c95ac-328">L'istruzione fixed</span><span class="sxs-lookup"><span data-stu-id="c95ac-328">The fixed statement</span></span>

<span data-ttu-id="c95ac-329">In un contesto unsafe, il *embedded_statement* ([istruzioni](statements.md)) produzione consente di creare un costrutto aggiuntivo, il `fixed` istruzione in cui viene utilizzata una variabile mobile "corretti" in modo che relativo indirizzo rimane costante per la durata dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-329">In an unsafe context, the *embedded_statement* ([Statements](statements.md)) production permits an additional construct, the `fixed` statement, which is used to "fix" a moveable variable such that its address remains constant for the duration of the statement.</span></span>

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

<span data-ttu-id="c95ac-330">Ogni *fixed_pointer_declarator* dichiara una variabile locale di dato *pointer_type* e inizializza la variabile locale con l'indirizzo calcolata dal corrispondente *fixed_ pointer_initializer*.</span><span class="sxs-lookup"><span data-stu-id="c95ac-330">Each *fixed_pointer_declarator* declares a local variable of the given *pointer_type* and initializes that local variable with the address computed by the corresponding *fixed_pointer_initializer*.</span></span> <span data-ttu-id="c95ac-331">Una variabile locale dichiarata un `fixed` è accessibile in qualsiasi istruzione *fixed_pointer_initializer*s che si verificano a destra della dichiarazione della variabile nelle *embedded_statement* del `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-331">A local variable declared in a `fixed` statement is accessible in any *fixed_pointer_initializer*s occurring to the right of that variable's declaration, and in the *embedded_statement* of the `fixed` statement.</span></span> <span data-ttu-id="c95ac-332">Una variabile locale dichiarata da un `fixed` istruzione viene considerata di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="c95ac-332">A local variable declared by a `fixed` statement is considered read-only.</span></span> <span data-ttu-id="c95ac-333">Si verifica un errore in fase di compilazione se l'istruzione incorporata tenta di modificare questa variabile locale (tramite assegnazione o il `++` e `--` operators) o passarlo come una `ref` o `out` parametro.</span><span class="sxs-lookup"><span data-stu-id="c95ac-333">A compile-time error occurs if the embedded statement attempts to modify this local variable (via assignment or the `++` and `--` operators) or pass it as a `ref` or `out` parameter.</span></span>

<span data-ttu-id="c95ac-334">Oggetto *fixed_pointer_initializer* può essere uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="c95ac-334">A *fixed_pointer_initializer* can be one of the following:</span></span>

*  <span data-ttu-id="c95ac-335">Il token "`&`" seguita da un *variable_reference* ([regole Precise per la determinazione di assegnazione certa](variables.md#precise-rules-for-determining-definite-assignment)) a una variabile mobile ([variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)) di un tipo non gestito `T`, purché il tipo `T*` implicita al tipo di puntatore fornito nel `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-335">The token "`&`" followed by a *variable_reference* ([Precise rules for determining definite assignment](variables.md#precise-rules-for-determining-definite-assignment)) to a moveable variable ([Fixed and moveable variables](unsafe-code.md#fixed-and-moveable-variables)) of an unmanaged type `T`, provided the type `T*` is implicitly convertible to the pointer type given in the `fixed` statement.</span></span> <span data-ttu-id="c95ac-336">In questo caso, l'inizializzatore calcola l'indirizzo della variabile specificata e la variabile è garantita per rimanere in un indirizzo fisso per la durata del `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-336">In this case, the initializer computes the address of the given variable, and the variable is guaranteed to remain at a fixed address for the duration of the `fixed` statement.</span></span>
*  <span data-ttu-id="c95ac-337">Un'espressione di un' *array_type* con elementi di un tipo non gestito `T`, purché il tipo `T*` implicita al tipo di puntatore fornito nel `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-337">An expression of an *array_type* with elements of an unmanaged type `T`, provided the type `T*` is implicitly convertible to the pointer type given in the `fixed` statement.</span></span> <span data-ttu-id="c95ac-338">In questo caso, l'inizializzatore calcola l'indirizzo del primo elemento nella matrice e l'intera matrice devono restare in un indirizzo fisso per la durata del `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-338">In this case, the initializer computes the address of the first element in the array, and the entire array is guaranteed to remain at a fixed address for the duration of the `fixed` statement.</span></span> <span data-ttu-id="c95ac-339">Se l'espressione di matrice è null o se la matrice con zero elementi, l'inizializzatore calcola un indirizzo uguale a zero.</span><span class="sxs-lookup"><span data-stu-id="c95ac-339">If the array expression is null or if the array has zero elements, the initializer computes an address equal to zero.</span></span>
*  <span data-ttu-id="c95ac-340">Un'espressione di tipo `string`, purché il tipo `char*` implicita al tipo di puntatore fornito nel `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-340">An expression of type `string`, provided the type `char*` is implicitly convertible to the pointer type given in the `fixed` statement.</span></span> <span data-ttu-id="c95ac-341">In questo caso, l'inizializzatore calcola l'indirizzo del primo carattere nella stringa e l'intera stringa devono restare in un indirizzo fisso per la durata del `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-341">In this case, the initializer computes the address of the first character in the string, and the entire string is guaranteed to remain at a fixed address for the duration of the `fixed` statement.</span></span> <span data-ttu-id="c95ac-342">Il comportamento del `fixed` istruzione è definito dall'implementazione se l'espressione di stringa è null.</span><span class="sxs-lookup"><span data-stu-id="c95ac-342">The behavior of the `fixed` statement is implementation-defined if the string expression is null.</span></span>
*  <span data-ttu-id="c95ac-343">Oggetto *simple_name* oppure *member_access* che fa riferimento a un membro del buffer a dimensione fissa di una variabile spostabile, purché il tipo di membro di buffer a dimensione fissa è implicitamente convertibile nel tipo di puntatore fornito nel `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-343">A *simple_name* or *member_access* that references a fixed size buffer member of a moveable variable, provided the type of the fixed size buffer member is implicitly convertible to the pointer type given in the `fixed` statement.</span></span> <span data-ttu-id="c95ac-344">In questo caso, l'inizializzatore calcola un puntatore al primo elemento del buffer a dimensione fissa ([buffer a dimensione fissa in espressioni](unsafe-code.md#fixed-size-buffers-in-expressions)), e il buffer a dimensione fissa è garantito a rimanere in un indirizzo fisso per la durata della `fixed`istruzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-344">In this case, the initializer computes a pointer to the first element of the fixed size buffer ([Fixed size buffers in expressions](unsafe-code.md#fixed-size-buffers-in-expressions)), and the fixed size buffer is guaranteed to remain at a fixed address for the duration of the `fixed` statement.</span></span>

<span data-ttu-id="c95ac-345">Per ogni indirizzo calcolata da una *fixed_pointer_initializer* il `fixed` istruzione garantisce che la variabile fa riferimento l'indirizzo non soggetti a spostamenti o eliminazione tramite garbage collector per la durata del `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-345">For each address computed by a *fixed_pointer_initializer* the `fixed` statement ensures that the variable referenced by the address is not subject to relocation or disposal by the garbage collector for the duration of the `fixed` statement.</span></span> <span data-ttu-id="c95ac-346">Ad esempio, se l'indirizzo calcolata da una *fixed_pointer_initializer* fa riferimento a un campo di un oggetto o un elemento di un'istanza di matrice, il `fixed` istruzione garantisce che l'istanza dell'oggetto contenitore non viene rilocato o eliminato nel corso della durata dell'istruzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-346">For example, if the address computed by a *fixed_pointer_initializer* references a field of an object or an element of an array instance, the `fixed` statement guarantees that the containing object instance is not relocated or disposed of during the lifetime of the statement.</span></span>

<span data-ttu-id="c95ac-347">È responsabilità del programmatore per assicurarsi che i puntatori creato da `fixed` istruzioni non sopravvivono oltre l'esecuzione di tali istruzioni.</span><span class="sxs-lookup"><span data-stu-id="c95ac-347">It is the programmer's responsibility to ensure that pointers created by `fixed` statements do not survive beyond execution of those statements.</span></span> <span data-ttu-id="c95ac-348">Ad esempio, quando i puntatori creata da `fixed` istruzioni vengono passate per le API esterne, è responsabilità del programmatore assicurare che le API venga mantenuto nessuno di questi puntatori.</span><span class="sxs-lookup"><span data-stu-id="c95ac-348">For example, when pointers created by `fixed` statements are passed to external APIs, it is the programmer's responsibility to ensure that the APIs retain no memory of these pointers.</span></span>

<span data-ttu-id="c95ac-349">Oggetti fissi possono provocare la frammentazione dell'heap (perché non possono essere spostati).</span><span class="sxs-lookup"><span data-stu-id="c95ac-349">Fixed objects may cause fragmentation of the heap (because they can't be moved).</span></span> <span data-ttu-id="c95ac-350">Per questo motivo, gli oggetti devono essere corretto solo quando strettamente necessario e solo per minor tempo possibile.</span><span class="sxs-lookup"><span data-stu-id="c95ac-350">For that reason, objects should be fixed only when absolutely necessary and then only for the shortest amount of time possible.</span></span>

<span data-ttu-id="c95ac-351">L'esempio</span><span class="sxs-lookup"><span data-stu-id="c95ac-351">The example</span></span>

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

<span data-ttu-id="c95ac-352">viene illustrato come utilizzare diverse di `fixed` istruzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-352">demonstrates several uses of the `fixed` statement.</span></span> <span data-ttu-id="c95ac-353">La prima istruzione correzioni e ottiene l'indirizzo di un campo statico, la seconda istruzione consente di risolvere e ottiene l'indirizzo di un campo di istanza e la terza istruzione correzioni e ottiene l'indirizzo di un elemento della matrice.</span><span class="sxs-lookup"><span data-stu-id="c95ac-353">The first statement fixes and obtains the address of a static field, the second statement fixes and obtains the address of an instance field, and the third statement fixes and obtains the address of an array element.</span></span> <span data-ttu-id="c95ac-354">In ogni caso sarebbe stato possibile usare la normale `&` operatore poiché tutte le variabili sono classificate come variabili spostabile.</span><span class="sxs-lookup"><span data-stu-id="c95ac-354">In each case it would have been an error to use the regular `&` operator since the variables are all classified as moveable variables.</span></span>

<span data-ttu-id="c95ac-355">Il quarto `fixed` istruzione nell'esempio precedente produce un risultato simile al terzo.</span><span class="sxs-lookup"><span data-stu-id="c95ac-355">The fourth `fixed` statement in the example above produces a similar result to the third.</span></span>

<span data-ttu-id="c95ac-356">In questo esempio del `fixed` istruzione Usa `string`:</span><span class="sxs-lookup"><span data-stu-id="c95ac-356">This example of the `fixed` statement uses `string`:</span></span>

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

<span data-ttu-id="c95ac-357">In un contesto unsafe elementi della matrice di matrici unidimensionali vengono archiviati in ordine crescente di indice, partire dall'indice `0` fino all'indice `Length - 1`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-357">In an unsafe context array elements of single-dimensional arrays are stored in increasing index order, starting with index `0` and ending with index `Length - 1`.</span></span> <span data-ttu-id="c95ac-358">Per le matrici multidimensionali, matrice gli elementi vengono archiviati in modo che gli indici della dimensione a destra vengono incrementati in primo luogo, quindi successivo a sinistra, dimensione, e così via, a sinistra.</span><span class="sxs-lookup"><span data-stu-id="c95ac-358">For multi-dimensional arrays, array elements are stored such that the indices of the rightmost dimension are increased first, then the next left dimension, and so on to the left.</span></span> <span data-ttu-id="c95ac-359">All'interno di un `fixed` istruzione che consente di ottenere un puntatore `p` a un'istanza di matrice `a`, i valori di puntatore compreso `p` a `p + a.Length - 1` rappresentano indirizzi degli elementi nella matrice.</span><span class="sxs-lookup"><span data-stu-id="c95ac-359">Within a `fixed` statement that obtains a pointer `p` to an array instance `a`, the pointer values ranging from `p` to `p + a.Length - 1` represent addresses of the elements in the array.</span></span> <span data-ttu-id="c95ac-360">Analogamente, le variabili comprese tra `p[0]` a `p[a.Length - 1]` rappresentano gli elementi di matrice effettive.</span><span class="sxs-lookup"><span data-stu-id="c95ac-360">Likewise, the variables ranging from `p[0]` to `p[a.Length - 1]` represent the actual array elements.</span></span> <span data-ttu-id="c95ac-361">Dato il modo in cui sono archiviate le matrici, è possibile considerare una matrice di qualsiasi dimensione come se fosse lineare.</span><span class="sxs-lookup"><span data-stu-id="c95ac-361">Given the way in which arrays are stored, we can treat an array of any dimension as though it were linear.</span></span>

<span data-ttu-id="c95ac-362">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c95ac-362">For example:</span></span>

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

<span data-ttu-id="c95ac-363">l'output ottenuto sarà:</span><span class="sxs-lookup"><span data-stu-id="c95ac-363">which produces the output:</span></span>

```
[0,0,0] =  0 [0,0,1] =  1 [0,0,2] =  2 [0,0,3] =  3
[0,1,0] =  4 [0,1,1] =  5 [0,1,2] =  6 [0,1,3] =  7
[0,2,0] =  8 [0,2,1] =  9 [0,2,2] = 10 [0,2,3] = 11
[1,0,0] = 12 [1,0,1] = 13 [1,0,2] = 14 [1,0,3] = 15
[1,1,0] = 16 [1,1,1] = 17 [1,1,2] = 18 [1,1,3] = 19
[1,2,0] = 20 [1,2,1] = 21 [1,2,2] = 22 [1,2,3] = 23
```

<span data-ttu-id="c95ac-364">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="c95ac-364">In the example</span></span>

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

<span data-ttu-id="c95ac-365">un `fixed` istruzione viene utilizzata per risolvere una matrice in modo che il relativo indirizzo può essere passato a un metodo che accetta un puntatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-365">a `fixed` statement is used to fix an array so its address can be passed to a method that takes a pointer.</span></span>

<span data-ttu-id="c95ac-366">Nell’esempio</span><span class="sxs-lookup"><span data-stu-id="c95ac-366">In the example:</span></span>

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

<span data-ttu-id="c95ac-367">Per correggere un buffer a dimensione fissa di uno struct, in modo relativo indirizzo può essere utilizzato come indicatore di misura viene utilizzata un'istruzione fissa.</span><span class="sxs-lookup"><span data-stu-id="c95ac-367">a fixed statement is used to fix a fixed size buffer of a struct so its address can be used as a pointer.</span></span>

<span data-ttu-id="c95ac-368">Oggetto `char*` valore generato tramite la correzione di un'istanza di stringa sempre punta a una stringa con terminazione null.</span><span class="sxs-lookup"><span data-stu-id="c95ac-368">A `char*` value produced by fixing a string instance always points to a null-terminated string.</span></span> <span data-ttu-id="c95ac-369">All'interno di un'istruzione fixed che consente di ottenere un puntatore `p` a un'istanza di stringa `s`, i valori di puntatore compreso tra `p` al `p + s.Length - 1` rappresentano indirizzi dei caratteri nella stringa e il valore del puntatore `p + s.Length` fa sempre riferimento a un carattere null (il carattere con valore `'\0'`).</span><span class="sxs-lookup"><span data-stu-id="c95ac-369">Within a fixed statement that obtains a pointer `p` to a string instance `s`, the pointer values ranging from `p` to `p + s.Length - 1` represent addresses of the characters in the string, and the pointer value `p + s.Length` always points to a null character (the character with value `'\0'`).</span></span>

<span data-ttu-id="c95ac-370">La modifica di oggetti di tipo gestito tramite puntatori fissi può determina un comportamento non definito.</span><span class="sxs-lookup"><span data-stu-id="c95ac-370">Modifying objects of managed type through fixed pointers can results in undefined behavior.</span></span> <span data-ttu-id="c95ac-371">Ad esempio, poiché le stringhe sono modificabili, è compito del programmatore garantire che i caratteri di cui viene fatto riferimento da un puntatore a una stringa fissa non vengono modificati.</span><span class="sxs-lookup"><span data-stu-id="c95ac-371">For example, because strings are immutable, it is the programmer's responsibility to ensure that the characters referenced by a pointer to a fixed string are not modified.</span></span>

<span data-ttu-id="c95ac-372">La terminazione null automatico delle stringhe è particolarmente utile quando si chiamano le API esterne che sono previste stringhe "C-style".</span><span class="sxs-lookup"><span data-stu-id="c95ac-372">The automatic null-termination of strings is particularly convenient when calling external APIs that expect "C-style" strings.</span></span> <span data-ttu-id="c95ac-373">Si noti tuttavia che un'istanza di stringa può contenere caratteri null.</span><span class="sxs-lookup"><span data-stu-id="c95ac-373">Note, however, that a string instance is permitted to contain null characters.</span></span> <span data-ttu-id="c95ac-374">Se sono presenti caratteri null, la stringa risulterà troncata quando viene trattata come una terminazione null `char*`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-374">If such null characters are present, the string will appear truncated when treated as a null-terminated `char*`.</span></span>

## <a name="fixed-size-buffers"></a><span data-ttu-id="c95ac-375">Buffer a dimensione fissa</span><span class="sxs-lookup"><span data-stu-id="c95ac-375">Fixed size buffers</span></span>

<span data-ttu-id="c95ac-376">Buffer a dimensione fissa vengono usate per dichiarare "C style" matrici in linea come membri di strutture e sono principalmente utili per interfacciarsi con le API non gestite.</span><span class="sxs-lookup"><span data-stu-id="c95ac-376">Fixed size buffers are used to declare "C style" in-line arrays as members of structs, and are primarily useful for interfacing with unmanaged APIs.</span></span>

### <a name="fixed-size-buffer-declarations"></a><span data-ttu-id="c95ac-377">Dichiarazioni di buffer a dimensione fissa</span><span class="sxs-lookup"><span data-stu-id="c95ac-377">Fixed size buffer declarations</span></span>

<span data-ttu-id="c95ac-378">Oggetto ***buffer a dimensione fissa*** è un membro che rappresenta l'archiviazione per un buffer di lunghezza fissa di variabili di un determinato tipo.</span><span class="sxs-lookup"><span data-stu-id="c95ac-378">A ***fixed size buffer*** is a member that represents storage for a fixed length buffer of variables of a given type.</span></span> <span data-ttu-id="c95ac-379">Una dichiarazione di buffer di dimensioni fisse introduce uno o più buffer a dimensione fissa di un tipo di elemento specificato.</span><span class="sxs-lookup"><span data-stu-id="c95ac-379">A fixed size buffer declaration introduces one or more fixed size buffers of a given element type.</span></span> <span data-ttu-id="c95ac-380">Buffer a dimensione fissa sono consentiti solo nelle dichiarazioni di struct e possono verificarsi solo in contesti non sicuri ([contesti non sicuri](unsafe-code.md#unsafe-contexts)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-380">Fixed size buffers are only permitted in struct declarations and can only occur in unsafe contexts ([Unsafe contexts](unsafe-code.md#unsafe-contexts)).</span></span>

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

<span data-ttu-id="c95ac-381">Una dichiarazione del buffer a dimensione fissa può includere un set di attributi ([attributi](attributes.md)), un `new` modificatore ([modificatori](classes.md#modifiers)), una combinazione valida dei quattro modificatori di accesso ([tipo i parametri e i vincoli](classes.md#type-parameters-and-constraints)) e un' `unsafe` modificatore ([contesti non sicuri](unsafe-code.md#unsafe-contexts)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-381">A fixed size buffer declaration may include a set of attributes ([Attributes](attributes.md)), a `new` modifier ([Modifiers](classes.md#modifiers)), a valid combination of the four access modifiers ([Type parameters and constraints](classes.md#type-parameters-and-constraints)) and an `unsafe` modifier ([Unsafe contexts](unsafe-code.md#unsafe-contexts)).</span></span> <span data-ttu-id="c95ac-382">Gli attributi e modificatori si applicano a tutti i membri dichiarati nella dichiarazione del buffer di dimensione fissa.</span><span class="sxs-lookup"><span data-stu-id="c95ac-382">The attributes and modifiers apply to all of the members declared by the fixed size buffer declaration.</span></span> <span data-ttu-id="c95ac-383">È un errore per lo stesso modificatore venga visualizzato più volte in una dichiarazione di buffer di dimensione fissa.</span><span class="sxs-lookup"><span data-stu-id="c95ac-383">It is an error for the same modifier to appear multiple times in a fixed size buffer declaration.</span></span>

<span data-ttu-id="c95ac-384">Una dichiarazione del buffer a dimensione fissa non è consentita includere il `static` modificatore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-384">A fixed size buffer declaration is not permitted to include the `static` modifier.</span></span>

<span data-ttu-id="c95ac-385">Il tipo di elemento del buffer di una dichiarazione di buffer di dimensione fissa specifica il tipo di elemento di buffer introdotte dalla dichiarazione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-385">The buffer element type of a fixed size buffer declaration specifies the element type of the buffer(s) introduced by the declaration.</span></span> <span data-ttu-id="c95ac-386">Il tipo di elemento del buffer deve essere uno dei tipi predefiniti `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, o `bool`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-386">The buffer element type must be one of the predefined types `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, or `bool`.</span></span>

<span data-ttu-id="c95ac-387">Il tipo di elemento del buffer è seguito da un elenco di dichiaratori di buffer a dimensione fissa, ognuno dei quali presenta un nuovo membro.</span><span class="sxs-lookup"><span data-stu-id="c95ac-387">The buffer element type is followed by a list of fixed size buffer declarators, each of which introduces a new member.</span></span> <span data-ttu-id="c95ac-388">Un dichiaratore di buffer a dimensione fissa è costituito da un identificatore che denomina il membro, seguito da un'espressione costante racchiuso tra parentesi `[` e `]` i token.</span><span class="sxs-lookup"><span data-stu-id="c95ac-388">A fixed size buffer declarator consists of an identifier that names the member, followed by a constant expression enclosed in `[` and `]` tokens.</span></span> <span data-ttu-id="c95ac-389">L'espressione costante indica il numero di elementi nel membro introdotte da tale dichiaratore di buffer a dimensione fissa.</span><span class="sxs-lookup"><span data-stu-id="c95ac-389">The constant expression denotes the number of elements in the member introduced by that fixed size buffer declarator.</span></span> <span data-ttu-id="c95ac-390">Il tipo dell'espressione costante deve essere convertibile in modo implicito nel tipo `int`, e il valore deve essere un numero intero positivo diverso da zero.</span><span class="sxs-lookup"><span data-stu-id="c95ac-390">The type of the constant expression must be implicitly convertible to type `int`, and the value must be a non-zero positive integer.</span></span>

<span data-ttu-id="c95ac-391">Gli elementi di un buffer a dimensione fissa che potrebbero essere disposti in sequenza in memoria.</span><span class="sxs-lookup"><span data-stu-id="c95ac-391">The elements of a fixed size buffer are guaranteed to be laid out sequentially in memory.</span></span>

<span data-ttu-id="c95ac-392">Una dichiarazione del buffer a dimensione fissa che dichiara più buffer a dimensione fissa è equivalente alla più dichiarazioni di una dichiarazione di buffer di dimensione fissa singola con gli stessi attributi e tipi di elemento.</span><span class="sxs-lookup"><span data-stu-id="c95ac-392">A fixed size buffer declaration that declares multiple fixed size buffers is equivalent to multiple declarations of a single fixed size buffer declaration with the same attributes, and element types.</span></span> <span data-ttu-id="c95ac-393">Esempio:</span><span class="sxs-lookup"><span data-stu-id="c95ac-393">For example</span></span>

```csharp
unsafe struct A
{
   public fixed int x[5], y[10], z[100];
}
```

<span data-ttu-id="c95ac-394">equivale a</span><span class="sxs-lookup"><span data-stu-id="c95ac-394">is equivalent to</span></span>

```csharp
unsafe struct A
{
   public fixed int x[5];
   public fixed int y[10];
   public fixed int z[100];
}
```

### <a name="fixed-size-buffers-in-expressions"></a><span data-ttu-id="c95ac-395">Buffer a dimensione fissa nelle espressioni</span><span class="sxs-lookup"><span data-stu-id="c95ac-395">Fixed size buffers in expressions</span></span>

<span data-ttu-id="c95ac-396">Ricerca di membri ([operatori](expressions.md#operators)) di dimensioni fisse membro buffer procede esattamente come ricerca di membri di un campo.</span><span class="sxs-lookup"><span data-stu-id="c95ac-396">Member lookup ([Operators](expressions.md#operators)) of a fixed size buffer member proceeds exactly like member lookup of a field.</span></span>

<span data-ttu-id="c95ac-397">Buffer a dimensione fissa è reperibile in un'espressione che usa un' *simple_name* ([inferenza del tipo](expressions.md#type-inference)) o un *member_access* ([controllo della fase di compilazione risoluzione dell'overload dinamica](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-397">A fixed size buffer can be referenced in an expression using a *simple_name* ([Type inference](expressions.md#type-inference)) or a *member_access* ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span></span>

<span data-ttu-id="c95ac-398">Quando un membro del buffer a dimensione fissa viene fatto riferimento come un nome semplice, l'effetto è lo stesso dell'accesso ai membri del modulo `this.I`, dove `I` è membro di buffer a dimensione fissa.</span><span class="sxs-lookup"><span data-stu-id="c95ac-398">When a fixed size buffer member is referenced as a simple name, the effect is the same as a member access of the form `this.I`, where `I` is the fixed size buffer member.</span></span>

<span data-ttu-id="c95ac-399">In un accesso ai membri del modulo `E.I`, se `E` è di un tipo di struct e la ricerca dei membri `I` in quanto tipo struct identifica un membro di dimensione fissa, quindi `E.I` viene valutata e classificata come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="c95ac-399">In a member access of the form `E.I`, if `E` is of a struct type and a member lookup of `I` in that struct type identifies a fixed size member, then `E.I` is evaluated an classified as follows:</span></span>

*  <span data-ttu-id="c95ac-400">Se l'espressione `E.I` non si verifica in un contesto unsafe, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-400">If the expression `E.I` does not occur in an unsafe context, a compile-time error occurs.</span></span>
*  <span data-ttu-id="c95ac-401">Se `E` viene classificato come un valore, si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-401">If `E` is classified as a value, a compile-time error occurs.</span></span>
*  <span data-ttu-id="c95ac-402">In caso contrario, se `E` è una variabile mobile ([variabili fisse e mobili](unsafe-code.md#fixed-and-moveable-variables)) e l'espressione `E.I` non è un *fixed_pointer_initializer* ([fissi istruzione](unsafe-code.md#the-fixed-statement)), si verifica un errore in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-402">Otherwise, if `E` is a moveable variable ([Fixed and moveable variables](unsafe-code.md#fixed-and-moveable-variables)) and the expression `E.I` is not a *fixed_pointer_initializer* ([The fixed statement](unsafe-code.md#the-fixed-statement)), a compile-time error occurs.</span></span>
*  <span data-ttu-id="c95ac-403">In caso contrario, `E` fa riferimento a una variabile fissa e il risultato dell'espressione è un puntatore al primo elemento del membro di buffer a dimensione fissa `I` in `E`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-403">Otherwise, `E` references a fixed variable and the result of the expression is a pointer to the first element of the fixed size buffer member `I` in `E`.</span></span> <span data-ttu-id="c95ac-404">Il risultato è di tipo `S*`, dove `S` è il tipo di elemento di `I`ed è classificato come un valore.</span><span class="sxs-lookup"><span data-stu-id="c95ac-404">The result is of type `S*`, where `S` is the element type of `I`, and is classified as a value.</span></span>

<span data-ttu-id="c95ac-405">Gli elementi successivi del buffer a dimensione fissa sono accessibile mediante operazioni di puntatore tra il primo elemento.</span><span class="sxs-lookup"><span data-stu-id="c95ac-405">The subsequent elements of the fixed size buffer can be accessed using pointer operations from the first element.</span></span> <span data-ttu-id="c95ac-406">A differenza di accesso alle matrici, accesso agli elementi di un buffer a dimensione fissa è un'operazione non sicura e non è selezionata di intervallo.</span><span class="sxs-lookup"><span data-stu-id="c95ac-406">Unlike access to arrays, access to the elements of a fixed size buffer is an unsafe operation and is not range checked.</span></span>

<span data-ttu-id="c95ac-407">Nell'esempio seguente viene dichiarata e utilizzata una struttura con un membro del buffer a dimensione fissa.</span><span class="sxs-lookup"><span data-stu-id="c95ac-407">The following example declares and uses a struct with a fixed size buffer member.</span></span>

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

### <a name="definite-assignment-checking"></a><span data-ttu-id="c95ac-408">Controllo dell'assegnazione definita</span><span class="sxs-lookup"><span data-stu-id="c95ac-408">Definite assignment checking</span></span>

<span data-ttu-id="c95ac-409">Buffer a dimensione fissa non sono soggetti a controllo dell'assegnazione definita ([assegnazione certa](variables.md#definite-assignment)), e i membri di dimensione fissa del buffer vengono ignorati per scopi di assegnazione certa il controllo delle variabili di tipo struct.</span><span class="sxs-lookup"><span data-stu-id="c95ac-409">Fixed size buffers are not subject to definite assignment checking ([Definite assignment](variables.md#definite-assignment)), and fixed size buffer members are ignored for purposes of definite assignment checking of struct type variables.</span></span>

<span data-ttu-id="c95ac-410">Quando la variabile di uno struct che lo contiene più esterno di un membro di dimensione fissa del buffer è una variabile statica, una variabile di istanza di un'istanza della classe o un elemento di matrice, gli elementi di buffer a dimensione fissa vengono inizializzati automaticamente i valori predefiniti ([Valori predefiniti](variables.md#default-values)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-410">When the outermost containing struct variable of a fixed size buffer member is a static variable, an instance variable of a class instance, or an array element, the elements of the fixed size buffer are automatically initialized to their default values ([Default values](variables.md#default-values)).</span></span> <span data-ttu-id="c95ac-411">In tutti gli altri casi, il contenuto iniziale di un buffer a dimensione fissa è indefinito.</span><span class="sxs-lookup"><span data-stu-id="c95ac-411">In all other cases, the initial content of a fixed size buffer is undefined.</span></span>

## <a name="stack-allocation"></a><span data-ttu-id="c95ac-412">Allocazione nello stack</span><span class="sxs-lookup"><span data-stu-id="c95ac-412">Stack allocation</span></span>

<span data-ttu-id="c95ac-413">In un contesto unsafe, una dichiarazione di variabile locale ([dichiarazioni di variabili locali](statements.md#local-variable-declarations)) può includere un inizializzatore di allocazione dello stack che consente di allocare memoria dallo stack di chiamate.</span><span class="sxs-lookup"><span data-stu-id="c95ac-413">In an unsafe context, a local variable declaration ([Local variable declarations](statements.md#local-variable-declarations)) may include a stack allocation initializer which allocates memory from the call stack.</span></span>

```antlr
local_variable_initializer_unsafe
    : stackalloc_initializer
    ;

stackalloc_initializer
    : 'stackalloc' unmanaged_type '[' expression ']'
    ;
```

<span data-ttu-id="c95ac-414">Il *unmanaged_type* indica il tipo degli elementi che verranno archiviati nel percorso appena allocato, e il *espressione* indica il numero di questi elementi.</span><span class="sxs-lookup"><span data-stu-id="c95ac-414">The *unmanaged_type* indicates the type of the items that will be stored in the newly allocated location, and the *expression* indicates the number of these items.</span></span> <span data-ttu-id="c95ac-415">Nel loro insieme, questi elementi specificano la dimensione di allocazione necessaria.</span><span class="sxs-lookup"><span data-stu-id="c95ac-415">Taken together, these specify the required allocation size.</span></span> <span data-ttu-id="c95ac-416">Poiché le dimensioni di un'allocazione dello stack non possono essere negativa, è un errore in fase di compilazione per specificare il numero di elementi come un *constant_expression* che restituisce un valore negativo.</span><span class="sxs-lookup"><span data-stu-id="c95ac-416">Since the size of a stack allocation cannot be negative, it is a compile-time error to specify the number of items as a *constant_expression* that evaluates to a negative value.</span></span>

<span data-ttu-id="c95ac-417">Un inizializzatore di allocazione dello stack del form `stackalloc T[E]` richiede `T` sia un tipo non gestito ([tipi di puntatore](unsafe-code.md#pointer-types)) e `E` essere un'espressione di tipo `int`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-417">A stack allocation initializer of the form `stackalloc T[E]` requires `T` to be an unmanaged type ([Pointer types](unsafe-code.md#pointer-types)) and `E` to be an expression of type `int`.</span></span> <span data-ttu-id="c95ac-418">Alloca il costrutto `E * sizeof(T)` byte dalla chiamata dello stack e restituisce un puntatore di tipo `T*`, per il blocco appena allocato.</span><span class="sxs-lookup"><span data-stu-id="c95ac-418">The construct allocates `E * sizeof(T)` bytes from the call stack and returns a pointer, of type `T*`, to the newly allocated block.</span></span> <span data-ttu-id="c95ac-419">Se `E` è un valore negativo, il comportamento sarà indefinito.</span><span class="sxs-lookup"><span data-stu-id="c95ac-419">If `E` is a negative value, then the behavior is undefined.</span></span> <span data-ttu-id="c95ac-420">Se `E` è uguale a zero, quindi viene eseguita alcuna allocazione e il puntatore restituito è definito dall'implementazione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-420">If `E` is zero, then no allocation is made, and the pointer returned is implementation-defined.</span></span> <span data-ttu-id="c95ac-421">Se non è disponibile memoria sufficiente per allocare un blocco della dimensione specificata, un `System.StackOverflowException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-421">If there is not enough memory available to allocate a block of the given size, a `System.StackOverflowException` is thrown.</span></span>

<span data-ttu-id="c95ac-422">Il contenuto della memoria appena allocata è definito.</span><span class="sxs-lookup"><span data-stu-id="c95ac-422">The content of the newly allocated memory is undefined.</span></span>

<span data-ttu-id="c95ac-423">Gli inizializzatori di allocazione dello stack non sono consentiti nelle `catch` oppure `finally` blocchi ([l'istruzione try](statements.md#the-try-statement)).</span><span class="sxs-lookup"><span data-stu-id="c95ac-423">Stack allocation initializers are not permitted in `catch` or `finally` blocks ([The try statement](statements.md#the-try-statement)).</span></span>

<span data-ttu-id="c95ac-424">Non è possibile liberare in modo esplicito la memoria allocata tramite `stackalloc`.</span><span class="sxs-lookup"><span data-stu-id="c95ac-424">There is no way to explicitly free memory allocated using `stackalloc`.</span></span> <span data-ttu-id="c95ac-425">Tutti i blocchi di memoria allocato nello stack creati durante l'esecuzione di un membro di funzione vengono eliminati automaticamente quando restituisce il membro di funzione.</span><span class="sxs-lookup"><span data-stu-id="c95ac-425">All stack allocated memory blocks created during the execution of a function member are automatically discarded when that function member returns.</span></span> <span data-ttu-id="c95ac-426">Corrisponde al `alloca` (funzione), un'estensione comunemente reperibile nelle implementazioni di C e C++.</span><span class="sxs-lookup"><span data-stu-id="c95ac-426">This corresponds to the `alloca` function, an extension commonly found in C and C++ implementations.</span></span>

<span data-ttu-id="c95ac-427">Nell'esempio</span><span class="sxs-lookup"><span data-stu-id="c95ac-427">In the example</span></span>

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

<span data-ttu-id="c95ac-428">una `stackalloc` inizializzatore viene utilizzato la `IntToString` metodo per allocare un buffer di 16 caratteri nello stack.</span><span class="sxs-lookup"><span data-stu-id="c95ac-428">a `stackalloc` initializer is used in the `IntToString` method to allocate a buffer of 16 characters on the stack.</span></span> <span data-ttu-id="c95ac-429">Il buffer viene eliminato automaticamente quando il metodo restituisce.</span><span class="sxs-lookup"><span data-stu-id="c95ac-429">The buffer is automatically discarded when the method returns.</span></span>

## <a name="dynamic-memory-allocation"></a><span data-ttu-id="c95ac-430">Allocazione dinamica della memoria</span><span class="sxs-lookup"><span data-stu-id="c95ac-430">Dynamic memory allocation</span></span>

<span data-ttu-id="c95ac-431">Fatta eccezione per il `stackalloc` operatore, c# non offre costrutti predefiniti per la gestione della memoria raccolta non garbage.</span><span class="sxs-lookup"><span data-stu-id="c95ac-431">Except for the `stackalloc` operator, C# provides no predefined constructs for managing non-garbage collected memory.</span></span> <span data-ttu-id="c95ac-432">Tali servizi sono in genere forniti dal supporto di librerie di classi o importati direttamente dal sistema operativo sottostante.</span><span class="sxs-lookup"><span data-stu-id="c95ac-432">Such services are typically provided by supporting class libraries or imported directly from the underlying operating system.</span></span> <span data-ttu-id="c95ac-433">Ad esempio, il `Memory` classe riportata di seguito viene illustrato come le funzioni heap di un sistema operativo sottostante potrebbe essere possibile accedere da c#:</span><span class="sxs-lookup"><span data-stu-id="c95ac-433">For example, the `Memory` class below illustrates how the heap functions of an underlying operating system might be accessed from C#:</span></span>

```csharp
using System;
using System.Runtime.InteropServices;

public unsafe class Memory
{
    // Handle for the process heap. This handle is used in all calls to the
    // HeapXXX APIs in the methods below.
    static int ph = GetProcessHeap();

    // Private instance constructor to prevent instantiation.
    private Memory() {}

    // Allocates a memory block of the given size. The allocated memory is
    // automatically initialized to zero.
    public static void* Alloc(int size) {
        void* result = HeapAlloc(ph, HEAP_ZERO_MEMORY, size);
        if (result == null) throw new OutOfMemoryException();
        return result;
    }

    // Copies count bytes from src to dst. The source and destination
    // blocks are permitted to overlap.
    public static void Copy(void* src, void* dst, int count) {
        byte* ps = (byte*)src;
        byte* pd = (byte*)dst;
        if (ps > pd) {
            for (; count != 0; count--) *pd++ = *ps++;
        }
        else if (ps < pd) {
            for (ps += count, pd += count; count != 0; count--) *--pd = *--ps;
        }
    }

    // Frees a memory block.
    public static void Free(void* block) {
        if (!HeapFree(ph, 0, block)) throw new InvalidOperationException();
    }

    // Re-allocates a memory block. If the reallocation request is for a
    // larger size, the additional region of memory is automatically
    // initialized to zero.
    public static void* ReAlloc(void* block, int size) {
        void* result = HeapReAlloc(ph, HEAP_ZERO_MEMORY, block, size);
        if (result == null) throw new OutOfMemoryException();
        return result;
    }

    // Returns the size of a memory block.
    public static int SizeOf(void* block) {
        int result = HeapSize(ph, 0, block);
        if (result == -1) throw new InvalidOperationException();
        return result;
    }

    // Heap API flags
    const int HEAP_ZERO_MEMORY = 0x00000008;

    // Heap API functions
    [DllImport("kernel32")]
    static extern int GetProcessHeap();

    [DllImport("kernel32")]
    static extern void* HeapAlloc(int hHeap, int flags, int size);

    [DllImport("kernel32")]
    static extern bool HeapFree(int hHeap, int flags, void* block);

    [DllImport("kernel32")]
    static extern void* HeapReAlloc(int hHeap, int flags, void* block, int size);

    [DllImport("kernel32")]
    static extern int HeapSize(int hHeap, int flags, void* block);
}
```

<span data-ttu-id="c95ac-434">Un esempio che usa il `Memory` classe è indicata di seguito:</span><span class="sxs-lookup"><span data-stu-id="c95ac-434">An example that uses the `Memory` class is given below:</span></span>

```csharp
class Test
{
    static void Main() {
        unsafe {
            byte* buffer = (byte*)Memory.Alloc(256);
            try {
                for (int i = 0; i < 256; i++) buffer[i] = (byte)i;
                byte[] array = new byte[256];
                fixed (byte* p = array) Memory.Copy(buffer, p, 256); 
            }
            finally {
                Memory.Free(buffer);
            }
            for (int i = 0; i < 256; i++) Console.WriteLine(array[i]);
        }
    }
}
```

<span data-ttu-id="c95ac-435">Nell'esempio viene allocata 256 byte di memoria tramite `Memory.Alloc` e inizializza il blocco di memoria con i valori crescenti da 0 a 255.</span><span class="sxs-lookup"><span data-stu-id="c95ac-435">The example allocates 256 bytes of memory through `Memory.Alloc` and initializes the memory block with values increasing from 0 to 255.</span></span> <span data-ttu-id="c95ac-436">Quindi alloca una matrice di byte di 256 elementi e utilizza `Memory.Copy` per copiare il contenuto del blocco di memoria nella matrice di byte.</span><span class="sxs-lookup"><span data-stu-id="c95ac-436">It then allocates a 256 element byte array and uses `Memory.Copy` to copy the contents of the memory block into the byte array.</span></span> <span data-ttu-id="c95ac-437">Infine, il blocco di memoria viene liberato usando `Memory.Free` e il contenuto della matrice di byte viene visualizzati nella console.</span><span class="sxs-lookup"><span data-stu-id="c95ac-437">Finally, the memory block is freed using `Memory.Free` and the contents of the byte array are output on the console.</span></span>
