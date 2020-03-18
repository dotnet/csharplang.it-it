---
ms.openlocfilehash: b0d0fa70d90f7493c6c23be576155a77cec36cf8
ms.sourcegitcommit: f61a06970fa0562d2e40363fae3948eb168624ca
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2020
ms.locfileid: "79485182"
---
# <a name="default-interface-methods"></a><span data-ttu-id="32795-101">metodi di interfaccia predefiniti</span><span class="sxs-lookup"><span data-stu-id="32795-101">default interface methods</span></span>

* <span data-ttu-id="32795-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="32795-102">[x] Proposed</span></span>
* <span data-ttu-id="32795-103">[] Prototipo: [in corso](https://github.com/dotnet/roslyn/blob/master/docs/features/DefaultInterfaceImplementation.md)</span><span class="sxs-lookup"><span data-stu-id="32795-103">[ ] Prototype: [In progress](https://github.com/dotnet/roslyn/blob/master/docs/features/DefaultInterfaceImplementation.md)</span></span>
* <span data-ttu-id="32795-104">[] Implementazione: nessuna</span><span class="sxs-lookup"><span data-stu-id="32795-104">[ ] Implementation: None</span></span>
* <span data-ttu-id="32795-105">[] Specifica: in corso, di seguito</span><span class="sxs-lookup"><span data-stu-id="32795-105">[ ] Specification: In progress, below</span></span>

## <a name="summary"></a><span data-ttu-id="32795-106">Summary</span><span class="sxs-lookup"><span data-stu-id="32795-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="32795-107">Aggiungere il supporto per i _metodi di estensione virtuale_ : metodi nelle interfacce con implementazioni concrete.</span><span class="sxs-lookup"><span data-stu-id="32795-107">Add support for _virtual extension methods_ - methods in interfaces with concrete implementations.</span></span> <span data-ttu-id="32795-108">Una classe o uno struct che implementa tale interfaccia è necessario disporre di una singola implementazione _più specifica_ per il metodo di interfaccia, implementata dalla classe o dallo struct, oppure ereditata dalle relative classi o interfacce di base.</span><span class="sxs-lookup"><span data-stu-id="32795-108">A class or struct that implements such an interface is required to have a single _most specific_ implementation for the interface method, either implemented by the class or struct, or inherited from its base classes or interfaces.</span></span> <span data-ttu-id="32795-109">I metodi di estensione virtuali consentono a un autore di API di aggiungere metodi a un'interfaccia nelle versioni future senza compromettere la compatibilità di origine o binaria con le implementazioni esistenti di tale interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32795-109">Virtual extension methods enable an API author to add methods to an interface in future versions without breaking source or binary compatibility with existing implementations of that interface.</span></span>

<span data-ttu-id="32795-110">Sono simili ai ["metodi predefiniti"](http://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)di Java.</span><span class="sxs-lookup"><span data-stu-id="32795-110">These are similar to Java's ["Default Methods"](http://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html).</span></span>

<span data-ttu-id="32795-111">(In base alla tecnica di implementazione probabile) questa funzionalità richiede il supporto corrispondente nell'interfaccia della riga di comando/CLR.</span><span class="sxs-lookup"><span data-stu-id="32795-111">(Based on the likely implementation technique) this feature requires corresponding support in the CLI/CLR.</span></span> <span data-ttu-id="32795-112">I programmi che sfruttano questa funzionalità non possono essere eseguiti nelle versioni precedenti della piattaforma.</span><span class="sxs-lookup"><span data-stu-id="32795-112">Programs that take advantage of this feature cannot run on earlier versions of the platform.</span></span>

## <a name="motivation"></a><span data-ttu-id="32795-113">Motivazione</span><span class="sxs-lookup"><span data-stu-id="32795-113">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="32795-114">Le motivazioni principali per questa funzionalità sono</span><span class="sxs-lookup"><span data-stu-id="32795-114">The principal motivations for this feature are</span></span>

- <span data-ttu-id="32795-115">I metodi di interfaccia predefiniti consentono a un autore di API di aggiungere metodi a un'interfaccia nelle versioni future senza compromettere la compatibilità di origine o binaria con le implementazioni esistenti di tale interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32795-115">Default interface methods enable an API author to add methods to an interface in future versions without breaking source or binary compatibility with existing implementations of that interface.</span></span>
- <span data-ttu-id="32795-116">La funzionalità consente C# a di interagire con le API destinate ad [Android (Java)](http://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html) e [iOS (SWIFT)](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html#//apple_ref/doc/uid/TP40014097-CH25-ID267), che supportano funzionalità simili.</span><span class="sxs-lookup"><span data-stu-id="32795-116">The feature enables C# to interoperate with APIs targeting [Android (Java)](http://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html) and [iOS (Swift)](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html#//apple_ref/doc/uid/TP40014097-CH25-ID267), which support similar features.</span></span>
- <span data-ttu-id="32795-117">Come si scopre, l'aggiunta di implementazioni di interfaccia predefinite fornisce gli elementi della funzionalità del linguaggio "tratti" (<https://en.wikipedia.org/wiki/Trait_(computer_programming)>).</span><span class="sxs-lookup"><span data-stu-id="32795-117">As it turns out, adding default interface implementations provides the elements of the "traits" language feature (<https://en.wikipedia.org/wiki/Trait_(computer_programming)>).</span></span> <span data-ttu-id="32795-118">I tratti si sono dimostrati una tecnica di programmazione avanzata (<http://scg.unibe.ch/archive/papers/Scha03aTraits.pdf>).</span><span class="sxs-lookup"><span data-stu-id="32795-118">Traits have proven to be a powerful programming technique (<http://scg.unibe.ch/archive/papers/Scha03aTraits.pdf>).</span></span>

## <a name="detailed-design"></a><span data-ttu-id="32795-119">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="32795-119">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="32795-120">La sintassi di un'interfaccia viene estesa per consentire</span><span class="sxs-lookup"><span data-stu-id="32795-120">The syntax for an interface is extended to permit</span></span>

- <span data-ttu-id="32795-121">dichiarazioni di membri che dichiarano costanti, operatori, costruttori statici e tipi annidati;</span><span class="sxs-lookup"><span data-stu-id="32795-121">member declarations that declare constants, operators, static constructors, and nested types;</span></span>
- <span data-ttu-id="32795-122">*corpo* per un metodo o un indicizzatore, una proprietà o una funzione di accesso all'evento (ovvero, un'implementazione "predefinita");</span><span class="sxs-lookup"><span data-stu-id="32795-122">a *body* for a method or indexer, property, or event accessor (that is, a "default" implementation);</span></span>
- <span data-ttu-id="32795-123">dichiarazioni di membri che dichiarano campi, metodi, proprietà, indicizzatori ed eventi statici;</span><span class="sxs-lookup"><span data-stu-id="32795-123">member declarations that declare static fields, methods, properties, indexers, and events;</span></span>
- <span data-ttu-id="32795-124">dichiarazioni di membri che usano la sintassi di implementazione esplicita dell'interfaccia; e</span><span class="sxs-lookup"><span data-stu-id="32795-124">member declarations using the explicit interface implementation syntax; and</span></span>
- <span data-ttu-id="32795-125">Modificatori di accesso espliciti (l'accesso predefinito è `public`).</span><span class="sxs-lookup"><span data-stu-id="32795-125">Explicit access modifiers (the default access is `public`).</span></span>

<span data-ttu-id="32795-126">I membri con corpi consentono all'interfaccia di fornire un'implementazione "predefinita" per il metodo in classi e struct che non forniscono un'implementazione di override.</span><span class="sxs-lookup"><span data-stu-id="32795-126">Members with bodies permit the interface to provide a "default" implementation for the method in classes and structs that do not provide an overriding implementation.</span></span>

<span data-ttu-id="32795-127">Le interfacce non possono contenere lo stato dell'istanza.</span><span class="sxs-lookup"><span data-stu-id="32795-127">Interfaces may not contain instance state.</span></span> <span data-ttu-id="32795-128">Mentre i campi statici sono ora consentiti, i campi di istanza non sono consentiti nelle interfacce.</span><span class="sxs-lookup"><span data-stu-id="32795-128">While static fields are now permitted, instance fields are not permitted in interfaces.</span></span> <span data-ttu-id="32795-129">Le proprietà automatiche dell'istanza non sono supportate nelle interfacce perché dichiarano in modo implicito un campo nascosto.</span><span class="sxs-lookup"><span data-stu-id="32795-129">Instance auto-properties are not supported in interfaces, as they would implicitly declare a hidden field.</span></span>

<span data-ttu-id="32795-130">I metodi statici e privati consentono il refactoring e l'organizzazione del codice usati per implementare l'API pubblica dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32795-130">Static and private methods permit useful refactoring and organization of code used to implement the interface's public API.</span></span>

<span data-ttu-id="32795-131">Un override di metodo in un'interfaccia deve usare la sintassi di implementazione esplicita dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32795-131">A method override in an interface must use the explicit interface implementation syntax.</span></span>

<span data-ttu-id="32795-132">È un errore dichiarare un tipo di classe, un tipo di struct o un tipo enum nell'ambito di un parametro di tipo dichiarato con un *variance_annotation*.</span><span class="sxs-lookup"><span data-stu-id="32795-132">It is an error to declare a class type, struct type, or enum type within the scope of a type parameter that was declared with a *variance_annotation*.</span></span>  <span data-ttu-id="32795-133">Ad esempio, la dichiarazione di `C` seguente è un errore.</span><span class="sxs-lookup"><span data-stu-id="32795-133">For example, the declaration of `C` below is an error.</span></span>

```csharp
interface IOuter<out T>
{
    class C { } // error: class declaration within the scope of variant type parameter 'T'
}
```

### <a name="concrete-methods-in-interfaces"></a><span data-ttu-id="32795-134">Metodi concreti nelle interfacce</span><span class="sxs-lookup"><span data-stu-id="32795-134">Concrete methods in interfaces</span></span>

<span data-ttu-id="32795-135">La forma più semplice di questa funzionalità è la possibilità di dichiarare un *metodo concreto* in un'interfaccia, ovvero un metodo con un corpo.</span><span class="sxs-lookup"><span data-stu-id="32795-135">The simplest form of this feature is the ability to declare a *concrete method* in an interface, which is a method with a body.</span></span>

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
```

<span data-ttu-id="32795-136">Una classe che implementa questa interfaccia non deve implementare il metodo concreto.</span><span class="sxs-lookup"><span data-stu-id="32795-136">A class that implements this interface need not implement its concrete method.</span></span>

```csharp
class C : IA { } // OK

IA i = new C();
i.M(); // prints "IA.M"
```

<span data-ttu-id="32795-137">L'override finale per `IA.M` nella classe `C` è il metodo concreto `M` dichiarato in `IA`.</span><span class="sxs-lookup"><span data-stu-id="32795-137">The final override for `IA.M` in class `C` is the concrete method `M` declared in `IA`.</span></span> <span data-ttu-id="32795-138">Si noti che una classe non eredita i membri dalle relative interfacce. Questa funzionalità non è stata modificata:</span><span class="sxs-lookup"><span data-stu-id="32795-138">Note that a class does not inherit members from its interfaces; that is not changed by this feature:</span></span>

```csharp
new C().M(); // error: class 'C' does not contain a member 'M'
```

<span data-ttu-id="32795-139">All'interno di un membro di istanza di un'interfaccia, `this` dispone del tipo dell'interfaccia di inclusione.</span><span class="sxs-lookup"><span data-stu-id="32795-139">Within an instance member of an interface, `this` has the type of the enclosing interface.</span></span>

### <a name="modifiers-in-interfaces"></a><span data-ttu-id="32795-140">Modificatori nelle interfacce</span><span class="sxs-lookup"><span data-stu-id="32795-140">Modifiers in interfaces</span></span>

<span data-ttu-id="32795-141">La sintassi di un'interfaccia è semplificata per consentire i modificatori sui relativi membri.</span><span class="sxs-lookup"><span data-stu-id="32795-141">The syntax for an interface is relaxed to permit modifiers on its members.</span></span> <span data-ttu-id="32795-142">Sono consentiti gli elementi seguenti: `private`, `protected`, `internal`, `public`, `virtual`, `abstract`, `sealed`, `static`, `extern`e `partial`.</span><span class="sxs-lookup"><span data-stu-id="32795-142">The following are permitted: `private`, `protected`, `internal`, `public`, `virtual`, `abstract`, `sealed`, `static`, `extern`, and `partial`.</span></span>

> <span data-ttu-id="32795-143">***Todo***: controllare gli altri modificatori esistenti.</span><span class="sxs-lookup"><span data-stu-id="32795-143">***TODO***: check what other modifiers exist.</span></span>

<span data-ttu-id="32795-144">Un membro di interfaccia la cui dichiarazione include un corpo è un membro `virtual` a meno che non venga usato il modificatore `sealed` o `private`.</span><span class="sxs-lookup"><span data-stu-id="32795-144">An interface member whose declaration includes a body is a `virtual` member unless the `sealed` or `private` modifier is used.</span></span> <span data-ttu-id="32795-145">Il modificatore `virtual` può essere usato in un membro di funzione che altrimenti verrebbe `virtual`in modo implicito.</span><span class="sxs-lookup"><span data-stu-id="32795-145">The `virtual` modifier may be used on a function member that would otherwise be implicitly `virtual`.</span></span> <span data-ttu-id="32795-146">Analogamente, anche se `abstract` è l'impostazione predefinita sui membri di interfaccia senza corpi, il modificatore può essere specificato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="32795-146">Similarly, although `abstract` is the default on interface members without bodies, that modifier may be given explicitly.</span></span> <span data-ttu-id="32795-147">Un membro non virtuale può essere dichiarato con la parola chiave `sealed`.</span><span class="sxs-lookup"><span data-stu-id="32795-147">A non-virtual member may be declared using the `sealed` keyword.</span></span>

<span data-ttu-id="32795-148">Si tratta di un errore per un `private` o `sealed` membro di una funzione di un'interfaccia privo di corpo.</span><span class="sxs-lookup"><span data-stu-id="32795-148">It is an error for a `private` or `sealed` function member of an interface to have no body.</span></span> <span data-ttu-id="32795-149">Un membro `private` funzione non può avere il modificatore `sealed`.</span><span class="sxs-lookup"><span data-stu-id="32795-149">A `private` function member may not have the modifier `sealed`.</span></span>

<span data-ttu-id="32795-150">I modificatori di accesso possono essere usati sui membri di interfaccia di tutti i tipi di membri consentiti.</span><span class="sxs-lookup"><span data-stu-id="32795-150">Access modifiers may be used on interface members of all kinds of members that are permitted.</span></span> <span data-ttu-id="32795-151">Il livello di accesso `public` è l'impostazione predefinita, ma può essere specificato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="32795-151">The access level `public` is the default but it may be given explicitly.</span></span>

> <span data-ttu-id="32795-152">***Problema aperto:*** È necessario specificare il significato preciso dei modificatori di accesso, ad esempio `protected` e `internal`, e quali dichiarazioni eseguono e non eseguono l'override (in un'interfaccia derivata) o implementarle (in una classe che implementa l'interfaccia).</span><span class="sxs-lookup"><span data-stu-id="32795-152">***Open Issue:*** We need to specify the precise meaning of the access modifiers such as `protected` and `internal`, and which declarations do and do not override them (in a derived interface) or implement them (in a class that implements the interface).</span></span>

<span data-ttu-id="32795-153">Le interfacce possono dichiarare membri `static`, inclusi tipi annidati, metodi, indicizzatori, proprietà, eventi e costruttori statici.</span><span class="sxs-lookup"><span data-stu-id="32795-153">Interfaces may declare `static` members, including nested types, methods, indexers, properties, events, and static constructors.</span></span> <span data-ttu-id="32795-154">Il livello di accesso predefinito per tutti i membri di interfaccia è `public`.</span><span class="sxs-lookup"><span data-stu-id="32795-154">The default access level for all interface members is `public`.</span></span>

<span data-ttu-id="32795-155">Le interfacce non possono dichiarare costruttori di istanza, distruttori o campi.</span><span class="sxs-lookup"><span data-stu-id="32795-155">Interfaces may not declare instance constructors, destructors, or fields.</span></span>

> <span data-ttu-id="32795-156">***Problema chiuso:*** Le dichiarazioni degli operatori devono essere consentite in un'interfaccia?</span><span class="sxs-lookup"><span data-stu-id="32795-156">***Closed Issue:*** Should operator declarations be permitted in an interface?</span></span> <span data-ttu-id="32795-157">Probabilmente non si tratta di operatori di conversione, ma di altri?</span><span class="sxs-lookup"><span data-stu-id="32795-157">Probably not conversion operators, but what about others?</span></span> <span data-ttu-id="32795-158">***Decisione***: gli operatori sono consentiti *eccetto* gli operatori di conversione, uguaglianza e disuguaglianza.</span><span class="sxs-lookup"><span data-stu-id="32795-158">***Decision***: Operators are permitted *except* for conversion, equality, and inequality operators.</span></span>

> <span data-ttu-id="32795-159">***Problema chiuso:*** Deve `new` essere consentito nelle dichiarazioni dei membri di interfaccia che nascondono i membri dalle interfacce di base?</span><span class="sxs-lookup"><span data-stu-id="32795-159">***Closed Issue:*** Should `new` be permitted on interface member declarations that hide members from base interfaces?</span></span> <span data-ttu-id="32795-160">***Decisione***: Sì.</span><span class="sxs-lookup"><span data-stu-id="32795-160">***Decision***: Yes.</span></span>

> <span data-ttu-id="32795-161">***Problema chiuso:*** Attualmente non è consentito `partial` su un'interfaccia o sui relativi membri.</span><span class="sxs-lookup"><span data-stu-id="32795-161">***Closed Issue:*** We do not currently permit `partial` on an interface or its members.</span></span> <span data-ttu-id="32795-162">Che richiederebbe una proposta separata.</span><span class="sxs-lookup"><span data-stu-id="32795-162">That would require a separate proposal.</span></span> <span data-ttu-id="32795-163">***Decisione***: Sì.</span><span class="sxs-lookup"><span data-stu-id="32795-163">***Decision***: Yes.</span></span> <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#permit-partial-in-interface>

### <a name="overrides-in-interfaces"></a><span data-ttu-id="32795-164">Sostituzioni nelle interfacce</span><span class="sxs-lookup"><span data-stu-id="32795-164">Overrides in interfaces</span></span>

<span data-ttu-id="32795-165">Le dichiarazioni di override, ad esempio quelle che contengono il modificatore `override`, consentono al programmatore di fornire un'implementazione più specifica di un membro virtuale in un'interfaccia in cui il compilatore o il runtime non ne troverà uno.</span><span class="sxs-lookup"><span data-stu-id="32795-165">Override declarations (i.e. those containing the `override` modifier) allow the programmer to provide a most specific implementation of a virtual member in an interface where the compiler or runtime would not otherwise find one.</span></span> <span data-ttu-id="32795-166">Consente inoltre di trasformare un membro astratto da un'interfaccia super in un membro predefinito in un'interfaccia derivata.</span><span class="sxs-lookup"><span data-stu-id="32795-166">It also allows turning an abstract member from a super-interface into a default member in a derived interface.</span></span> <span data-ttu-id="32795-167">Una dichiarazione di override è consentita per eseguire l'override *esplicito* di un particolare metodo di interfaccia di base qualificando la dichiarazione con il nome dell'interfaccia (in questo caso non è consentito alcun modificatore di accesso).</span><span class="sxs-lookup"><span data-stu-id="32795-167">An override declaration is permitted to *explicitly* override a particular base interface method by qualifying the declaration with the interface name (no access modifier is permitted in this case).</span></span> <span data-ttu-id="32795-168">Non sono consentite sostituzioni implicite.</span><span class="sxs-lookup"><span data-stu-id="32795-168">Implicit overrides are not permitted.</span></span>

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    override void IA.M() { WriteLine("IB.M"); } // explicitly named
}
interface IC : IA
{
    override void M() { WriteLine("IC.M"); } // implicitly named
}
```

<span data-ttu-id="32795-169">Le dichiarazioni di override nelle interfacce non possono essere dichiarate `sealed`.</span><span class="sxs-lookup"><span data-stu-id="32795-169">Override declarations in interfaces may not be declared `sealed`.</span></span>

<span data-ttu-id="32795-170">È possibile eseguire l'override in modo esplicito dei membri della funzione public `virtual` in un'interfaccia in un'interfaccia derivata (qualificando il nome nella dichiarazione di override con il tipo di interfaccia che ha originariamente dichiarato il metodo e omettendo un modificatore di accesso).</span><span class="sxs-lookup"><span data-stu-id="32795-170">Public `virtual` function members in an interface may be overridden in a derived interface explicitly (by qualifying the name in the override declaration with the interface type that originally declared the method, and omitting an access modifier).</span></span>

<span data-ttu-id="32795-171">`virtual` membri di funzione in un'interfaccia possono essere sottoposti a override in modo esplicito (non implicito) nelle interfacce derivate e i membri che non sono `public` possono essere implementati solo in una classe o in uno struct in modo esplicito (non in modo implicito).</span><span class="sxs-lookup"><span data-stu-id="32795-171">`virtual` function members in an interface may only be overridden explicitly (not implicitly) in derived interfaces, and members that are not `public` may only be implemented in a class or struct explicitly (not implicitly).</span></span> <span data-ttu-id="32795-172">In entrambi i casi, il membro sottoposto a override o implementato deve essere *accessibile* se ne viene eseguito l'override.</span><span class="sxs-lookup"><span data-stu-id="32795-172">In either case, the overridden or implemented member must be *accessible* where it is overridden.</span></span>

### <a name="reabstraction"></a><span data-ttu-id="32795-173">Riastrazione</span><span class="sxs-lookup"><span data-stu-id="32795-173">Reabstraction</span></span>

<span data-ttu-id="32795-174">Un metodo virtuale (concreto) dichiarato in un'interfaccia può essere sottoposto a override in modo da essere astratto in un'interfaccia derivata</span><span class="sxs-lookup"><span data-stu-id="32795-174">A virtual (concrete) method declared in an interface may be overridden to be abstract in a derived interface</span></span>

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    abstract void IA.M();
}
class C : IB { } // error: class 'C' does not implement 'IA.M'.
```

<span data-ttu-id="32795-175">Il modificatore `abstract` non è obbligatorio nella dichiarazione di `IB.M` (ovvero l'impostazione predefinita nelle interfacce), ma è consigliabile essere esplicita in una dichiarazione di override.</span><span class="sxs-lookup"><span data-stu-id="32795-175">The `abstract` modifier is not required in the declaration of `IB.M` (that is the default in interfaces), but it is probably good practice to be explicit in an override declaration.</span></span>

<span data-ttu-id="32795-176">Questa operazione è utile nelle interfacce derivate in cui l'implementazione predefinita di un metodo non è appropriata ed è necessario fornire un'implementazione più appropriata mediante l'implementazione delle classi.</span><span class="sxs-lookup"><span data-stu-id="32795-176">This is useful in derived interfaces where the default implementation of a method is inappropriate and a more appropriate implementation should be provided by implementing classes.</span></span>

> <span data-ttu-id="32795-177">***Problema aperto:*** È necessario consentire la ritrazione?</span><span class="sxs-lookup"><span data-stu-id="32795-177">***Open Issue:*** Should reabstraction be permitted?</span></span>

### <a name="the-most-specific-override-rule"></a><span data-ttu-id="32795-178">Regola di sostituzione più specifica</span><span class="sxs-lookup"><span data-stu-id="32795-178">The most specific override rule</span></span>

<span data-ttu-id="32795-179">È necessario che ogni interfaccia e classe disponga di un *override più specifico* per ogni membro virtuale tra le sostituzioni visualizzate nel tipo o nelle relative interfacce dirette e indirette.</span><span class="sxs-lookup"><span data-stu-id="32795-179">We require that every interface and class have a *most specific override* for every virtual member among the overrides appearing in the type or its direct and indirect interfaces.</span></span> <span data-ttu-id="32795-180">L'override *più specifico* è un override univoco più specifico di ogni altro override.</span><span class="sxs-lookup"><span data-stu-id="32795-180">The *most specific override* is a unique override that is more specific than every other override.</span></span> <span data-ttu-id="32795-181">Se non è presente alcun override, il membro stesso viene considerato l'override più specifico.</span><span class="sxs-lookup"><span data-stu-id="32795-181">If there is no override, the member itself is considered the most specific override.</span></span>

<span data-ttu-id="32795-182">Una sostituzione `M1` viene considerata *più specifica* di un'altra `M2` se `M1` viene dichiarata sul tipo `T1`, `M2` viene dichiarata nel tipo `T2`e</span><span class="sxs-lookup"><span data-stu-id="32795-182">One override `M1` is considered *more specific* than another override `M2` if `M1` is declared on type `T1`, `M2` is declared on type `T2`, and either</span></span>

1. <span data-ttu-id="32795-183">`T1` contiene `T2` tra le interfacce dirette o indirette oppure</span><span class="sxs-lookup"><span data-stu-id="32795-183">`T1` contains `T2` among its direct or indirect interfaces, or</span></span>
2. <span data-ttu-id="32795-184">`T2` è un tipo di interfaccia, ma `T1` non è un tipo di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32795-184">`T2` is an interface type but `T1` is not an interface type.</span></span>

<span data-ttu-id="32795-185">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="32795-185">For example:</span></span>

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    void IA.M() { WriteLine("IB.M"); }
}
interface IC : IA
{
    void IA.M() { WriteLine("IC.M"); }
}
interface ID : IB, IC { } // error: no most specific override for 'IA.M'
abstract class C : IB, IC { } // error: no most specific override for 'IA.M'
abstract class D : IA, IB, IC // ok
{
    public abstract void M();
}

```

<span data-ttu-id="32795-186">La regola di sostituzione più specifica garantisce che un conflitto, ad esempio un'ambiguità derivante dall'ereditarietà dei diamanti, venga risolto in modo esplicito dal programmatore nel momento in cui si verifica il conflitto.</span><span class="sxs-lookup"><span data-stu-id="32795-186">The most specific override rule ensures that a conflict (i.e. an ambiguity arising from diamond inheritance) is resolved explicitly by the programmer at the point where the conflict arises.</span></span>

<span data-ttu-id="32795-187">Poiché è supportata l'esecuzione di override astratti espliciti nelle interfacce, è possibile eseguire questa operazione anche nelle classi</span><span class="sxs-lookup"><span data-stu-id="32795-187">Because we support explicit abstract overrides in interfaces, we could do so in classes as well</span></span>

```csharp
abstract class E : IA, IB, IC // ok
{
    abstract void IA.M();
}
```

> <span data-ttu-id="32795-188">***Problema aperto***: è necessario supportare le sostituzioni esplicite dell'interfaccia esplicita nelle classi?</span><span class="sxs-lookup"><span data-stu-id="32795-188">***Open issue***: should we support explicit interface abstract overrides in classes?</span></span>

<span data-ttu-id="32795-189">Inoltre, si tratta di un errore se in una dichiarazione di classe l'override più specifico di un metodo di interfaccia è un override astratto dichiarato in un'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32795-189">In addition, it is an error if in a class declaration the most specific override of some interface method is an abstract override that was declared in an interface.</span></span> <span data-ttu-id="32795-190">Si tratta di una regola esistente ridichiarata usando la nuova terminologia.</span><span class="sxs-lookup"><span data-stu-id="32795-190">This is an existing rule restated using the new terminology.</span></span>

```csharp
interface IF
{
    void M();
}
abstract class F : IF { } // error: 'F' does not implement 'IF.M'
```

<span data-ttu-id="32795-191">È possibile che una proprietà virtuale dichiarata in un'interfaccia disponga di un override più specifico per la relativa funzione di accesso `get` in un'interfaccia e un override più specifico per la relativa funzione di accesso `set` in un'interfaccia diversa.</span><span class="sxs-lookup"><span data-stu-id="32795-191">It is possible for a virtual property declared in an interface to have a most specific override for its `get` accessor in one interface and a most specific override for its `set` accessor in a different interface.</span></span> <span data-ttu-id="32795-192">Questa operazione viene considerata una violazione della regola di *sostituzione più specifica* .</span><span class="sxs-lookup"><span data-stu-id="32795-192">This is considered a violation of the *most specific override* rule.</span></span>

### <a name="static-and-private-methods"></a><span data-ttu-id="32795-193">Metodi `static` e `private`</span><span class="sxs-lookup"><span data-stu-id="32795-193">`static` and `private` methods</span></span>

<span data-ttu-id="32795-194">Poiché le interfacce possono ora contenere codice eseguibile, è utile astrarre il codice comune nei metodi privati e statici.</span><span class="sxs-lookup"><span data-stu-id="32795-194">Because interfaces may now contain executable code, it is useful to abstract common code into private and static methods.</span></span> <span data-ttu-id="32795-195">Queste sono ora consentite nelle interfacce.</span><span class="sxs-lookup"><span data-stu-id="32795-195">We now permit these in interfaces.</span></span>

> <span data-ttu-id="32795-196">***Problema chiuso***: è necessario supportare i metodi privati?</span><span class="sxs-lookup"><span data-stu-id="32795-196">***Closed issue***: Should we support private methods?</span></span> <span data-ttu-id="32795-197">Sono supportati i metodi statici?</span><span class="sxs-lookup"><span data-stu-id="32795-197">Should we support static methods?</span></span> <span data-ttu-id="32795-198">**Decisione: Sì**</span><span class="sxs-lookup"><span data-stu-id="32795-198">**Decision: YES**</span></span>

> <span data-ttu-id="32795-199">***Problema aperto***: è necessario consentire ai metodi di interfaccia di essere `protected` o `internal` o altro accesso?</span><span class="sxs-lookup"><span data-stu-id="32795-199">***Open issue***: should we permit interface methods to be `protected` or `internal` or other access?</span></span> <span data-ttu-id="32795-200">In tal caso, qual è la semantica?</span><span class="sxs-lookup"><span data-stu-id="32795-200">If so, what are the semantics?</span></span> <span data-ttu-id="32795-201">Sono `virtual` per impostazione predefinita?</span><span class="sxs-lookup"><span data-stu-id="32795-201">Are they `virtual` by default?</span></span> <span data-ttu-id="32795-202">In caso affermativo, è possibile renderli non virtuali?</span><span class="sxs-lookup"><span data-stu-id="32795-202">If so, is there a way to make them non-virtual?</span></span>

> <span data-ttu-id="32795-203">***Problema di apertura***: se sono supportati i metodi statici, è necessario supportare gli operatori (statici)?</span><span class="sxs-lookup"><span data-stu-id="32795-203">***Open issue***: If we support static methods, should we support (static) operators?</span></span>

### <a name="base-interface-invocations"></a><span data-ttu-id="32795-204">Chiamate dell'interfaccia di base</span><span class="sxs-lookup"><span data-stu-id="32795-204">Base interface invocations</span></span>

<span data-ttu-id="32795-205">Il codice in un tipo che deriva da un'interfaccia con un metodo predefinito può richiamare in modo esplicito l'implementazione "base" dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32795-205">Code in a type that derives from an interface with a default method can explicitly invoke that interface's "base" implementation.</span></span>

```csharp
interface I0
{
   void M() { Console.WriteLine("I0"); }
}
interface I1 : I0
{
   override void M() { Console.WriteLine("I1"); }
}
interface I2 : I0
{
   override void M() { Console.WriteLine("I2"); }
}
interface I3 : I1, I2
{
   // an explicit override that invoke's a base interface's default method
   void I0.M() { I2.base.M(); }
}

```

<span data-ttu-id="32795-206">Un metodo di istanza (non statico) può richiamare l'implementazione di un metodo di istanza accessibile in un'interfaccia di base diretta in modo non virtuale assegnando loro un nome usando la sintassi `base(Type).M`.</span><span class="sxs-lookup"><span data-stu-id="32795-206">An instance (nonstatic) method is permitted to invoke the implementation of an accessible instance method in a direct base interface nonvirtually by naming it using the syntax `base(Type).M`.</span></span> <span data-ttu-id="32795-207">Questa operazione è utile quando viene risolta una sostituzione che è necessario fornire a causa dell'ereditarietà a rombo delegando a una particolare implementazione di base.</span><span class="sxs-lookup"><span data-stu-id="32795-207">This is useful when an override that is required to be provided due to diamond inheritance is resolved by delegating to one particular base implementation.</span></span>

```csharp
interface IA
{
    void M() { WriteLine("IA.M"); }
}
interface IB : IA
{
    override void IA.M() { WriteLine("IB.M"); }
}
interface IC : IA
{
    override void IA.M() { WriteLine("IC.M"); }
}

class D : IA, IB, IC
{
    void IA.M() { base(IB).M(); }
}
```

<span data-ttu-id="32795-208">Quando si accede a un `virtual` o a un membro di `abstract` utilizzando la sintassi `base(Type).M`, è necessario che `Type` contenga un *override più specifico* univoco per `M`.</span><span class="sxs-lookup"><span data-stu-id="32795-208">When a `virtual` or `abstract` member is accessed using the syntax `base(Type).M`, it is required that `Type` contains a unique *most specific override* for `M`.</span></span>

### <a name="binding-base-clauses"></a><span data-ttu-id="32795-209">Binding di clausole di base</span><span class="sxs-lookup"><span data-stu-id="32795-209">Binding base clauses</span></span>

<span data-ttu-id="32795-210">Le interfacce ora contengono tipi.</span><span class="sxs-lookup"><span data-stu-id="32795-210">Interfaces now contain types.</span></span>  <span data-ttu-id="32795-211">Questi tipi possono essere utilizzati nella clausola di base come interfacce di base.</span><span class="sxs-lookup"><span data-stu-id="32795-211">These types may be used in the base clause as base interfaces.</span></span>  <span data-ttu-id="32795-212">Quando si associa una clausola di base, potrebbe essere necessario individuare il set di interfacce di base per associare tali tipi (ad esempio, per eseguire la ricerca e per risolvere l'accesso protetto).</span><span class="sxs-lookup"><span data-stu-id="32795-212">When binding a base clause, we may need to know the set of base interfaces to bind those types (e.g. to lookup in them and to resolve protected access).</span></span>  <span data-ttu-id="32795-213">Il significato della clausola di base di un'interfaccia è quindi definito in modo circolare.</span><span class="sxs-lookup"><span data-stu-id="32795-213">The meaning of an interface's base clause is thus circularly defined.</span></span>  <span data-ttu-id="32795-214">Per interrompere il ciclo, si aggiungono nuove regole della lingua corrispondenti a una regola simile già presente per le classi.</span><span class="sxs-lookup"><span data-stu-id="32795-214">To break the cycle, we add a new language rules corresponding to a similar rule already in place for classes.</span></span>

<span data-ttu-id="32795-215">Quando si determina il significato del *interface_base* di un'interfaccia, le interfacce di base vengono temporaneamente considerate vuote.</span><span class="sxs-lookup"><span data-stu-id="32795-215">While determining the meaning of the *interface_base* of an interface, the base interfaces are temporarily assumed to be empty.</span></span> <span data-ttu-id="32795-216">In modo intuitivo, questo garantisce che il significato di una clausola di base non possa dipendere in modo ricorsivo da se stesso.</span><span class="sxs-lookup"><span data-stu-id="32795-216">Intuitively this ensures that the meaning of a base clause cannot recursively depend on itself.</span></span> 

<span data-ttu-id="32795-217">**Sono state usate le regole seguenti:**</span><span class="sxs-lookup"><span data-stu-id="32795-217">**We used to have the following rules:**</span></span>

<span data-ttu-id="32795-218">"Quando una classe B deriva da una classe A, si tratta di un errore in fase di compilazione affinché un oggetto dipenda da B. Una classe **dipende direttamente** dalla relativa classe di base diretta, se presente, e **dipende direttamente** dalla ~~**classe**~~ in cui è immediatamente annidata (se presente).</span><span class="sxs-lookup"><span data-stu-id="32795-218">"When a class B derives from a class A, it is a compile-time error for A to depend on B. A class **directly depends on** its direct base class (if any) and **directly depends on** the ~~**class**~~ within which it is immediately nested (if any).</span></span> <span data-ttu-id="32795-219">Data questa definizione, il set completo di ~~**classi**~~ da cui dipende una classe è la chiusura riflessiva e transitiva del **dipendente direttamente dalla** relazione ".</span><span class="sxs-lookup"><span data-stu-id="32795-219">Given this definition, the complete set of ~~**classes**~~ upon which a class depends is the reflexive and transitive closure of the **directly depends on** relationship."</span></span>

<span data-ttu-id="32795-220">Si tratta di un errore in fase di compilazione per un'interfaccia che eredita direttamente o indirettamente da se stesso.</span><span class="sxs-lookup"><span data-stu-id="32795-220">It is a compile-time error for an interface to directly or indirectly inherit from itself.</span></span>
<span data-ttu-id="32795-221">Le **interfacce** di base di un'interfaccia sono le interfacce di base esplicite e le relative interfacce di base.</span><span class="sxs-lookup"><span data-stu-id="32795-221">The **base interfaces** of an interface are the explicit base interfaces and their base interfaces.</span></span> <span data-ttu-id="32795-222">In altre parole, il set di interfacce di base è la chiusura transitiva completa delle interfacce di base esplicite, le interfacce di base esplicite e così via.</span><span class="sxs-lookup"><span data-stu-id="32795-222">In other words, the set of base interfaces is the complete transitive closure of the explicit base interfaces, their explicit base interfaces, and so on.</span></span>

<span data-ttu-id="32795-223">**Verranno regolate come indicato di seguito:**</span><span class="sxs-lookup"><span data-stu-id="32795-223">**We are adjusting them as follows:**</span></span>

<span data-ttu-id="32795-224">Quando una classe B deriva da una classe A, si tratta di un errore in fase di compilazione affinché un oggetto dipenda da B. Una classe **dipende direttamente** dalla relativa classe di base diretta, se presente, e **dipende direttamente** dal _**tipo**_ in cui è immediatamente annidato (se presente).</span><span class="sxs-lookup"><span data-stu-id="32795-224">When a class B derives from a class A, it is a compile-time error for A to depend on B. A class **directly depends on** its direct base class (if any) and **directly depends on** the _**type**_ within which it is immediately nested (if any).</span></span>

<span data-ttu-id="32795-225">Quando un'interfaccia IB estende un'interfaccia IA, si tratta di un errore in fase di compilazione perché IA dipenda da IB.</span><span class="sxs-lookup"><span data-stu-id="32795-225">When an interface IB extends an interface IA, it is a compile-time error for IA to depend on IB.</span></span> <span data-ttu-id="32795-226">Un'interfaccia **dipende direttamente** dalle interfacce di base dirette (se presenti) e **dipende direttamente** dal tipo in cui è immediatamente annidato (se presente).</span><span class="sxs-lookup"><span data-stu-id="32795-226">An interface **directly depends on** its direct base interfaces (if any) and **directly depends on** the type within which it is immediately nested (if any).</span></span>

<span data-ttu-id="32795-227">Date queste definizioni, il set completo di **tipi** su cui dipende un tipo è la chiusura riflessiva e transitiva del **dipendente direttamente dalla** relazione.</span><span class="sxs-lookup"><span data-stu-id="32795-227">Given these definitions, the complete set of **types** upon which a type depends is the reflexive and transitive closure of the **directly depends on** relationship.</span></span>

### <a name="effect-on-existing-programs"></a><span data-ttu-id="32795-228">Effetti sui programmi esistenti</span><span class="sxs-lookup"><span data-stu-id="32795-228">Effect on existing programs</span></span>

<span data-ttu-id="32795-229">Le regole presentate in questo documento hanno lo scopo di non avere alcun effetto sul significato dei programmi esistenti.</span><span class="sxs-lookup"><span data-stu-id="32795-229">The rules presented here are intended to have no effect on the meaning of existing programs.</span></span>

<span data-ttu-id="32795-230">Esempio 1:</span><span class="sxs-lookup"><span data-stu-id="32795-230">Example 1:</span></span>

```csharp
interface IA
{
    void M();
}
class C: IA // Error: IA.M has no concrete most specific override in C
{
    public static void M() { } // method unrelated to 'IA.M' because static
}
```

<span data-ttu-id="32795-231">Esempio 2:</span><span class="sxs-lookup"><span data-stu-id="32795-231">Example 2:</span></span>

```csharp
interface IA
{
    void M();
}
class Base: IA
{
    void IA.M() { }
}
class Derived: Base, IA // OK, all interface members have a concrete most specific override
{
    private void M() { } // method unrelated to 'IA.M' because private
}
```

<span data-ttu-id="32795-232">Le stesse regole forniscono risultati simili alla situazione analoga che riguarda i metodi di interfaccia predefiniti:</span><span class="sxs-lookup"><span data-stu-id="32795-232">The same rules give similar results to the analogous situation involving default interface methods:</span></span>

```csharp
interface IA
{
    void M() { }
}
class Derived: IA // OK, all interface members have a concrete most specific override
{
    private void M() { } // method unrelated to 'IA.M' because private
}
```

> <span data-ttu-id="32795-233">***Problema chiuso***: confermare che si tratta di una conseguenza prevista della specifica.</span><span class="sxs-lookup"><span data-stu-id="32795-233">***Closed issue***: confirm that this is an intended consequence of the specification.</span></span> <span data-ttu-id="32795-234">**Decisione: Sì**</span><span class="sxs-lookup"><span data-stu-id="32795-234">**Decision: YES**</span></span>

### <a name="runtime-method-resolution"></a><span data-ttu-id="32795-235">Risoluzione del metodo di runtime</span><span class="sxs-lookup"><span data-stu-id="32795-235">Runtime method resolution</span></span>

> <span data-ttu-id="32795-236">***Problema chiuso:*** La specifica deve descrivere l'algoritmo di risoluzione del metodo di runtime nella faccia dei metodi predefiniti dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32795-236">***Closed Issue:*** The spec should describe the runtime method resolution algorithm in the face of interface default methods.</span></span> <span data-ttu-id="32795-237">È necessario assicurarsi che la semantica sia coerente con la semantica del linguaggio, ad esempio i metodi dichiarati e non eseguono l'override o implementano un metodo `internal`.</span><span class="sxs-lookup"><span data-stu-id="32795-237">We need to ensure that the semantics are consistent with the language semantics, e.g. which declared methods do and do not override or implement an `internal` method.</span></span>

### <a name="clr-support-api"></a><span data-ttu-id="32795-238">API di supporto CLR</span><span class="sxs-lookup"><span data-stu-id="32795-238">CLR support API</span></span>

<span data-ttu-id="32795-239">Per consentire ai compilatori di rilevare quando vengono compilati per un runtime che supporta questa funzionalità, le librerie per tali Runtime vengono modificate per annunciare tale fatto tramite l'API descritta in <https://github.com/dotnet/corefx/issues/17116>.</span><span class="sxs-lookup"><span data-stu-id="32795-239">In order for compilers to detect when they are compiling for a runtime that supports this feature, libraries for such runtimes are modified to advertise that fact through the API discussed in <https://github.com/dotnet/corefx/issues/17116>.</span></span> <span data-ttu-id="32795-240">Aggiungiamo</span><span class="sxs-lookup"><span data-stu-id="32795-240">We add</span></span>

```csharp
namespace System.Runtime.CompilerServices
{
    public static class RuntimeFeature
    {
        // Presence of the field indicates runtime support
        public const string DefaultInterfaceImplementation = nameof(DefaultInterfaceImplementation);
    }
}
```

> <span data-ttu-id="32795-241">***Problema aperto***: è il nome migliore per la funzionalità *CLR* ?</span><span class="sxs-lookup"><span data-stu-id="32795-241">***Open issue***: Is that the best name for the *CLR* feature?</span></span> <span data-ttu-id="32795-242">La funzionalità CLR funziona molto più di quanto non solo (ad esempio, rilassa i vincoli di protezione, supporta le sostituzioni nelle interfacce e così via).</span><span class="sxs-lookup"><span data-stu-id="32795-242">The CLR feature does much more than just that (e.g. relaxes protection constraints, supports overrides in interfaces, etc).</span></span> <span data-ttu-id="32795-243">Forse dovrebbe essere definito come "metodi concreti nelle interfacce" o "tratti"?</span><span class="sxs-lookup"><span data-stu-id="32795-243">Perhaps it should be called something like "concrete methods in interfaces", or "traits"?</span></span>

### <a name="further-areas-to-be-specified"></a><span data-ttu-id="32795-244">Altre aree da specificare</span><span class="sxs-lookup"><span data-stu-id="32795-244">Further areas to be specified</span></span>

- <span data-ttu-id="32795-245">[] Sarebbe utile catalogare i tipi di effetti di compatibilità binari e di origine causati dall'aggiunta di metodi di interfaccia predefiniti e delle sostituzioni alle interfacce esistenti.</span><span class="sxs-lookup"><span data-stu-id="32795-245">[ ] It would be useful to catalog the kinds of source and binary compatibility effects caused by adding default interface methods and overrides to existing interfaces.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="32795-246">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="32795-246">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="32795-247">Questa proposta richiede un aggiornamento coordinato alla specifica CLR (per supportare metodi concreti nelle interfacce e nella risoluzione del metodo).</span><span class="sxs-lookup"><span data-stu-id="32795-247">This proposal requires a coordinated update to the CLR specification (to support concrete methods in interfaces and method resolution).</span></span> <span data-ttu-id="32795-248">È quindi piuttosto "costoso" e può essere utile in combinazione con altre funzionalità che ci si aspettano di dover apportare modifiche a CLR.</span><span class="sxs-lookup"><span data-stu-id="32795-248">It is therefore fairly "expensive" and it may be worth doing in combination with other features that we also anticipate would require CLR changes.</span></span>

## <a name="alternatives"></a><span data-ttu-id="32795-249">Alternativi</span><span class="sxs-lookup"><span data-stu-id="32795-249">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="32795-250">No.</span><span class="sxs-lookup"><span data-stu-id="32795-250">None.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="32795-251">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="32795-251">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="32795-252">Le domande aperte sono denominate in tutto il progetto, sopra.</span><span class="sxs-lookup"><span data-stu-id="32795-252">Open questions are called out throughout the proposal, above.</span></span>
- <span data-ttu-id="32795-253">Per un elenco di domande aperte, vedere anche <https://github.com/dotnet/csharplang/issues/406>.</span><span class="sxs-lookup"><span data-stu-id="32795-253">See also <https://github.com/dotnet/csharplang/issues/406> for a list of open questions.</span></span>
- <span data-ttu-id="32795-254">La specifica dettagliata deve descrivere il meccanismo di risoluzione utilizzato in fase di esecuzione per selezionare il metodo preciso da richiamare.</span><span class="sxs-lookup"><span data-stu-id="32795-254">The detailed specification must describe the resolution mechanism used at runtime to select the precise method to be invoked.</span></span>
- <span data-ttu-id="32795-255">L'interazione dei metadati prodotti da nuovi compilatori e utilizzata dai compilatori meno recenti deve essere elaborata in dettaglio.</span><span class="sxs-lookup"><span data-stu-id="32795-255">The interaction of metadata produced by new compilers and consumed by older compilers needs to be worked out in detail.</span></span> <span data-ttu-id="32795-256">Ad esempio, è necessario assicurarsi che la rappresentazione dei metadati utilizzata non provochi l'aggiunta di un'implementazione predefinita in un'interfaccia per interrompere una classe esistente che implementa tale interfaccia quando viene compilata da un compilatore precedente.</span><span class="sxs-lookup"><span data-stu-id="32795-256">For example, we need to ensure that the metadata representation that we use does not cause the addition of a default implementation in an interface to break an existing class that implements that interface when compiled by an older compiler.</span></span> <span data-ttu-id="32795-257">Questo può influire sulla rappresentazione dei metadati che è possibile usare.</span><span class="sxs-lookup"><span data-stu-id="32795-257">This may affect the metadata representation that we can use.</span></span>
- <span data-ttu-id="32795-258">La progettazione deve considerare l'interoperabilità con altri linguaggi e compilatori esistenti per altri linguaggi.</span><span class="sxs-lookup"><span data-stu-id="32795-258">The design must consider interoperation with other languages and existing compilers for other languages.</span></span>

## <a name="resolved-questions"></a><span data-ttu-id="32795-259">Domande risolte</span><span class="sxs-lookup"><span data-stu-id="32795-259">Resolved Questions</span></span>

### <a name="abstract-override"></a><span data-ttu-id="32795-260">Override astratto</span><span class="sxs-lookup"><span data-stu-id="32795-260">Abstract Override</span></span>

<span data-ttu-id="32795-261">La specifica bozza precedente conteneva la possibilità di "astrarre" un metodo ereditato:</span><span class="sxs-lookup"><span data-stu-id="32795-261">The earlier draft spec contained the ability to "reabstract" an inherited method:</span></span>

```csharp
interface IA
{
    void M();
}
interface IB : IA
{
    override void M() { }
}
interface IC : IB
{
    override void M(); // make it abstract again
}
```

<span data-ttu-id="32795-262">Le mie note per 2017-03-20 hanno mostrato che abbiamo deciso di non consentire questo problema.</span><span class="sxs-lookup"><span data-stu-id="32795-262">My notes for 2017-03-20 showed that we decided not to allow this.</span></span> <span data-ttu-id="32795-263">Tuttavia, esistono almeno due casi d'uso:</span><span class="sxs-lookup"><span data-stu-id="32795-263">However, there are at least two use cases for it:</span></span>

1. <span data-ttu-id="32795-264">Le API Java, con cui alcuni utenti di questa funzionalità hanno la speranza di interoperare, dipendono da questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="32795-264">The Java APIs, with which some users of this feature hope to interoperate, depend on this facility.</span></span>
2. <span data-ttu-id="32795-265">La programmazione con *tratti* trae vantaggio da questa.</span><span class="sxs-lookup"><span data-stu-id="32795-265">Programming with *traits* benefits from this.</span></span> <span data-ttu-id="32795-266">La ritrazione è uno degli elementi della funzionalità del linguaggio "tratti" (https://en.wikipedia.org/wiki/Trait_(computer_programming)).</span><span class="sxs-lookup"><span data-stu-id="32795-266">Reabstraction is one of the elements of the "traits" language feature (https://en.wikipedia.org/wiki/Trait_(computer_programming)).</span></span> <span data-ttu-id="32795-267">Con le classi è consentito quanto segue:</span><span class="sxs-lookup"><span data-stu-id="32795-267">The following is permitted with classes:</span></span>

```csharp
public abstract class Base
{
    public abstract void M();
}
public abstract class A : Base
{
    public override void M() { }
}
public abstract class B : A
{
    public override abstract void M(); // reabstract Base.M
}
```

<span data-ttu-id="32795-268">Sfortunatamente, questo codice non può essere sottoposto a refactoring come un set di interfacce (tratti), a meno che non sia consentito.</span><span class="sxs-lookup"><span data-stu-id="32795-268">Unfortunately this code cannot be refactored as a set of interfaces (traits) unless this is permitted.</span></span> <span data-ttu-id="32795-269">Il *principio di Jared di greed*dovrebbe essere consentito.</span><span class="sxs-lookup"><span data-stu-id="32795-269">By the *Jared principle of greed*, it should be permitted.</span></span>

> <span data-ttu-id="32795-270">***Problema chiuso:*** È necessario consentire la ritrazione?</span><span class="sxs-lookup"><span data-stu-id="32795-270">***Closed issue:*** Should reabstraction be permitted?</span></span> <span data-ttu-id="32795-271">Sì Le mie note erano errate.</span><span class="sxs-lookup"><span data-stu-id="32795-271">[YES] My notes were wrong.</span></span> <span data-ttu-id="32795-272">Le [Note LDM](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-21.md) dicono che la ritrazione è consentita in un'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32795-272">The [LDM notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-21.md) say that reabstraction is permitted in an interface.</span></span> <span data-ttu-id="32795-273">Non in una classe.</span><span class="sxs-lookup"><span data-stu-id="32795-273">Not in a class.</span></span>

### <a name="virtual-modifier-vs-sealed-modifier"></a><span data-ttu-id="32795-274">Modificatore virtuale rispetto al modificatore sealed</span><span class="sxs-lookup"><span data-stu-id="32795-274">Virtual Modifier vs Sealed Modifier</span></span>

<span data-ttu-id="32795-275">Da [Aleksey Tsingauz](https://github.com/AlekseyTs):</span><span class="sxs-lookup"><span data-stu-id="32795-275">From [Aleksey Tsingauz](https://github.com/AlekseyTs):</span></span>

> <span data-ttu-id="32795-276">Si è deciso di consentire ai modificatori indicati in modo esplicito sui membri dell'interfaccia, a meno che non esista un motivo per impedirne alcuni.</span><span class="sxs-lookup"><span data-stu-id="32795-276">We decided to allow modifiers explicitly stated on interface members, unless there is a reason to disallow some of them.</span></span> <span data-ttu-id="32795-277">Si tratta di una domanda interessante intorno al modificatore virtuale.</span><span class="sxs-lookup"><span data-stu-id="32795-277">This brings an interesting question around virtual modifier.</span></span> <span data-ttu-id="32795-278">Deve essere richiesto per i membri con implementazione predefinita?</span><span class="sxs-lookup"><span data-stu-id="32795-278">Should it be required on members with default implementation?</span></span>
>
> <span data-ttu-id="32795-279">Possiamo dire:</span><span class="sxs-lookup"><span data-stu-id="32795-279">We could say that:</span></span>
>
> - <span data-ttu-id="32795-280">Se non è presente alcuna implementazione e non è stato specificato alcun oggetto virtuale, né sealed, si presuppone che il membro sia astratto.</span><span class="sxs-lookup"><span data-stu-id="32795-280">if there is no implementation and neither virtual, nor sealed are specified, we assume the member is abstract.</span></span>
> - <span data-ttu-id="32795-281">Se è presente un'implementazione, né abstract, né sealed, si presuppone che il membro sia virtuale.</span><span class="sxs-lookup"><span data-stu-id="32795-281">if there is an implementation and neither abstract, nor sealed are specified, we assume the member is virtual.</span></span>
> - <span data-ttu-id="32795-282">il modificatore sealed è necessario per rendere un metodo non virtuale, né abstract.</span><span class="sxs-lookup"><span data-stu-id="32795-282">sealed modifier is required to make a method neither virtual, nor abstract.</span></span>
>
> <span data-ttu-id="32795-283">In alternativa, si potrebbe dire che il modificatore virtual è obbligatorio per un membro virtuale.</span><span class="sxs-lookup"><span data-stu-id="32795-283">Alternatively, we could say that virtual modifier is required for a virtual member.</span></span> <span data-ttu-id="32795-284">Se, ad esempio, un membro con implementazione non è contrassegnato in modo esplicito con il modificatore virtual, non è né Virtual né abstract.</span><span class="sxs-lookup"><span data-stu-id="32795-284">I.e, if there is a member with implementation not explicitly marked with virtual modifier, it is neither virtual, nor abstract.</span></span> <span data-ttu-id="32795-285">Questo approccio potrebbe offrire un'esperienza migliore quando un metodo viene spostato da una classe a un'interfaccia:</span><span class="sxs-lookup"><span data-stu-id="32795-285">This approach might provide better experience when a method is moved from a class to an interface:</span></span>
>
> - <span data-ttu-id="32795-286">un metodo astratto rimane astratto.</span><span class="sxs-lookup"><span data-stu-id="32795-286">an abstract method stays abstract.</span></span>
> - <span data-ttu-id="32795-287">un metodo virtuale rimane virtuale.</span><span class="sxs-lookup"><span data-stu-id="32795-287">a virtual method stays virtual.</span></span>
> - <span data-ttu-id="32795-288">un metodo senza modificatore non resta né Virtual né abstract.</span><span class="sxs-lookup"><span data-stu-id="32795-288">a method without any modifier stays neither virtual, nor abstract.</span></span>
> - <span data-ttu-id="32795-289">non è possibile applicare il modificatore sealed a un metodo che non è un override.</span><span class="sxs-lookup"><span data-stu-id="32795-289">sealed modifier cannot be applied to a method that is not an override.</span></span>
>
> <span data-ttu-id="32795-290">Che ne pensi?</span><span class="sxs-lookup"><span data-stu-id="32795-290">What do you think?</span></span>

> <span data-ttu-id="32795-291">***Problema chiuso:*** Un metodo concreto (con implementazione) deve essere `virtual`in modo implicito?</span><span class="sxs-lookup"><span data-stu-id="32795-291">***Closed Issue:*** Should a concrete method (with implementation) be implicitly `virtual`?</span></span> <span data-ttu-id="32795-292">Sì</span><span class="sxs-lookup"><span data-stu-id="32795-292">[YES]</span></span>

<span data-ttu-id="32795-293">***Decisioni:*** Realizzato in LDM 2017-04-05:</span><span class="sxs-lookup"><span data-stu-id="32795-293">***Decisions:*** Made in the LDM 2017-04-05:</span></span>

1. <span data-ttu-id="32795-294">non virtuale deve essere espressa in modo esplicito tramite `sealed` o `private`.</span><span class="sxs-lookup"><span data-stu-id="32795-294">non-virtual should be explicitly expressed through `sealed` or `private`.</span></span>
2. <span data-ttu-id="32795-295">`sealed` è la parola chiave per rendere i membri dell'istanza di interfaccia con corpi non virtuali</span><span class="sxs-lookup"><span data-stu-id="32795-295">`sealed` is the keyword to make interface instance members with bodies non-virtual</span></span>
3. <span data-ttu-id="32795-296">Si desidera consentire tutti i modificatori nelle interfacce</span><span class="sxs-lookup"><span data-stu-id="32795-296">We want to allow all modifiers in interfaces</span></span>  
4. <span data-ttu-id="32795-297">L'accessibilità predefinita per i membri di interfaccia è Public, compresi i tipi annidati</span><span class="sxs-lookup"><span data-stu-id="32795-297">Default accessibility for interface members is public, including nested types</span></span>
5. <span data-ttu-id="32795-298">i membri della funzione privata nelle interfacce sono implicitamente sealed e `sealed` non sono consentiti su di essi.</span><span class="sxs-lookup"><span data-stu-id="32795-298">private function members in interfaces are implicitly sealed, and `sealed` is not permitted on them.</span></span>
6. <span data-ttu-id="32795-299">Le classi private (nelle interfacce) sono consentite e possono essere sealed e questo significa sealed nel senso della classe sealed.</span><span class="sxs-lookup"><span data-stu-id="32795-299">Private classes (in interfaces) are permitted and can be sealed, and that means sealed in the class sense of sealed.</span></span>
7. <span data-ttu-id="32795-300">In assenza di una proposta corretta, il parziale non è ancora consentito sulle interfacce o sui relativi membri.</span><span class="sxs-lookup"><span data-stu-id="32795-300">Absent a good proposal, partial is still not allowed on interfaces or their members.</span></span>

### <a name="binary-compatibility-1"></a><span data-ttu-id="32795-301">Compatibilità binaria 1</span><span class="sxs-lookup"><span data-stu-id="32795-301">Binary Compatibility 1</span></span>

<span data-ttu-id="32795-302">Quando una libreria fornisce un'implementazione predefinita</span><span class="sxs-lookup"><span data-stu-id="32795-302">When a library provides a default implementation</span></span>

```csharp
interface I1
{
    void M() { Impl1 }
}
interface I2 : I1
{
}
class C : I2
{
}
```

<span data-ttu-id="32795-303">Si è consapevoli che l'implementazione di `I1.M` in `C` è `I1.M`.</span><span class="sxs-lookup"><span data-stu-id="32795-303">We understand that the implementation of `I1.M` in `C` is `I1.M`.</span></span> <span data-ttu-id="32795-304">Cosa accade se l'assembly contenente `I2` viene modificato come segue e ricompilato</span><span class="sxs-lookup"><span data-stu-id="32795-304">What if the assembly containing `I2` is changed as follows and recompiled</span></span>

```csharp
interface I2 : I1
{
    override void M() { Impl2 }
}
```

<span data-ttu-id="32795-305">ma `C` non viene ricompilato.</span><span class="sxs-lookup"><span data-stu-id="32795-305">but `C` is not recompiled.</span></span> <span data-ttu-id="32795-306">Cosa accade quando il programma viene eseguito?</span><span class="sxs-lookup"><span data-stu-id="32795-306">What happens when the program is run?</span></span> <span data-ttu-id="32795-307">Chiamata di `(C as I1).M()`</span><span class="sxs-lookup"><span data-stu-id="32795-307">An invocation of `(C as I1).M()`</span></span>

1. <span data-ttu-id="32795-308">Esegue `I1.M`</span><span class="sxs-lookup"><span data-stu-id="32795-308">Runs `I1.M`</span></span>
2. <span data-ttu-id="32795-309">Esegue `I2.M`</span><span class="sxs-lookup"><span data-stu-id="32795-309">Runs `I2.M`</span></span>
3. <span data-ttu-id="32795-310">Genera un tipo di errore di runtime</span><span class="sxs-lookup"><span data-stu-id="32795-310">Throws some kind of runtime error</span></span>

<span data-ttu-id="32795-311">***Decisione:*** Prodotto 2017-04-11: esegue `I2.M`, che rappresenta l'override più specifico in modo non ambiguo in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="32795-311">***Decision:*** Made 2017-04-11: Runs `I2.M`, which is the unambiguously most specific override at runtime.</span></span>

### <a name="event-accessors-closed"></a><span data-ttu-id="32795-312">Funzioni di accesso agli eventi (closed)</span><span class="sxs-lookup"><span data-stu-id="32795-312">Event accessors (closed)</span></span>

> <span data-ttu-id="32795-313">***Problema chiuso:*** È possibile eseguire l'override di un evento "a tratti"?</span><span class="sxs-lookup"><span data-stu-id="32795-313">***Closed Issue:*** Can an event be overridden "piecewise"?</span></span>

<span data-ttu-id="32795-314">Considerare questo caso:</span><span class="sxs-lookup"><span data-stu-id="32795-314">Consider this case:</span></span>

```csharp
public interface I1
{
    event T e1;
}
public interface I2 : I1
{
    override event T
    {
        add { }
        // error: "remove" accessor missing
    }
}
```

<span data-ttu-id="32795-315">Questa implementazione "parziale" dell'evento non è consentita perché, come in una classe, la sintassi per una dichiarazione di evento non consente una sola funzione di accesso. è necessario specificare sia (che nessuno).</span><span class="sxs-lookup"><span data-stu-id="32795-315">This "partial" implementation of the event is not permitted because, as in a class, the syntax for an event declaration does not permit only one accessor; both (or neither) must be provided.</span></span> <span data-ttu-id="32795-316">È possibile eseguire la stessa operazione consentendo di astrarre in modo implicito la funzione di accesso abstract Remove nella sintassi per l'assenza di un corpo:</span><span class="sxs-lookup"><span data-stu-id="32795-316">You could accomplish the same thing by permitting the abstract remove accessor in the syntax to be implicitly abstract by the absence of a body:</span></span>

```csharp
public interface I1
{
    event T e1;
}
public interface I2 : I1
{
    override event T
    {
        add { }
        remove; // implicitly abstract
    }
}
```

<span data-ttu-id="32795-317">Si noti che si *tratta di una nuova sintassi (proposta)* .</span><span class="sxs-lookup"><span data-stu-id="32795-317">Note that *this is a new (proposed) syntax*.</span></span> <span data-ttu-id="32795-318">Nella grammatica corrente, le funzioni di accesso agli eventi hanno un corpo obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="32795-318">In the current grammar, event accessors have a mandatory body.</span></span>

> <span data-ttu-id="32795-319">***Problema chiuso:*** È possibile che una funzione di accesso agli eventi sia (in modo implicito) astratta dall'omissione di un corpo, in modo analogo al modo in cui i metodi nelle interfacce e nelle funzioni di accesso alle proprietà sono implicitamente astratti dall'omissione di un corpo?</span><span class="sxs-lookup"><span data-stu-id="32795-319">***Closed Issue:*** Can an event accessor be (implicitly) abstract by the omission of a body, similarly to the way that methods in interfaces and property accessors are (implicitly) abstract by the omission of a body?</span></span>

<span data-ttu-id="32795-320">***Decisione:*** (2017-04-18) No, le dichiarazioni di evento richiedono entrambe le funzioni di accesso concrete o nessuna delle due.</span><span class="sxs-lookup"><span data-stu-id="32795-320">***Decision:*** (2017-04-18) No, event declarations require both concrete accessors (or neither).</span></span>

### <a name="reabstraction-in-a-class-closed"></a><span data-ttu-id="32795-321">Riastrazione in una classe (chiusa)</span><span class="sxs-lookup"><span data-stu-id="32795-321">Reabstraction in a Class (closed)</span></span>

<span data-ttu-id="32795-322">***Problema chiuso:*** È necessario confermare che questa operazione è consentita. in caso contrario, l'aggiunta di un'implementazione predefinita potrebbe essere una modifica di rilievo:</span><span class="sxs-lookup"><span data-stu-id="32795-322">***Closed Issue:*** We should confirm that this is permitted (otherwise adding a default implementation would be a breaking change):</span></span>

```csharp
interface I1
{
    void M() { }
}
abstract class C : I1
{
    public abstract void M(); // implement I1.M with an abstract method in C
}
```

<span data-ttu-id="32795-323">***Decisione:*** (2017-04-18) Sì, l'aggiunta di un corpo a una dichiarazione di membro di interfaccia non deve interrompere C.</span><span class="sxs-lookup"><span data-stu-id="32795-323">***Decision:*** (2017-04-18) Yes, adding a body to an interface member declaration shouldn't break C.</span></span>

### <a name="sealed-override-closed"></a><span data-ttu-id="32795-324">Sostituzione sealed (chiusa)</span><span class="sxs-lookup"><span data-stu-id="32795-324">Sealed Override (closed)</span></span>

<span data-ttu-id="32795-325">Nella domanda precedente si presuppone implicitamente che il modificatore di `sealed` possa essere applicato a una `override` in un'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32795-325">The previous question implicitly assumes that the `sealed` modifier can be applied to an `override` in an interface.</span></span> <span data-ttu-id="32795-326">In questo senso è contraddetta la specifica bozza.</span><span class="sxs-lookup"><span data-stu-id="32795-326">This contradicts the draft specification.</span></span> <span data-ttu-id="32795-327">Si desidera consentire la chiusura di una sostituzione?</span><span class="sxs-lookup"><span data-stu-id="32795-327">Do we want to permit sealing an override?</span></span> <span data-ttu-id="32795-328">Devono essere considerati gli effetti di compatibilità binari e di origine del sealing.</span><span class="sxs-lookup"><span data-stu-id="32795-328">Source and binary compatibility effects of sealing should be considered.</span></span>

> <span data-ttu-id="32795-329">***Problema chiuso:*** È necessario consentire la chiusura di una sostituzione?</span><span class="sxs-lookup"><span data-stu-id="32795-329">***Closed Issue:*** Should we permit sealing an override?</span></span>

<span data-ttu-id="32795-330">***Decisione:*** (2017-04-18) non è consentito `sealed` sulle sostituzioni nelle interfacce.</span><span class="sxs-lookup"><span data-stu-id="32795-330">***Decision:*** (2017-04-18) Let's not allowed `sealed` on overrides in interfaces.</span></span> <span data-ttu-id="32795-331">L'unico utilizzo di `sealed` sui membri di interfaccia consiste nel renderli non virtuali nella dichiarazione iniziale.</span><span class="sxs-lookup"><span data-stu-id="32795-331">The only use of `sealed` on interface members is to make them non-virtual in their initial declaration.</span></span>

### <a name="diamond-inheritance-and-classes-closed"></a><span data-ttu-id="32795-332">Ereditarietà e classi di diamanti (chiusa)</span><span class="sxs-lookup"><span data-stu-id="32795-332">Diamond inheritance and classes (closed)</span></span>

<span data-ttu-id="32795-333">Il draft della proposta predilige le sostituzioni delle classi per le sostituzioni di interfaccia negli scenari di ereditarietà dei diamanti:</span><span class="sxs-lookup"><span data-stu-id="32795-333">The draft of the proposal prefers class overrides to interface overrides in diamond inheritance scenarios:</span></span>

> <span data-ttu-id="32795-334">È necessario che ogni interfaccia e classe dispongano di un *override più specifico* per ogni metodo di interfaccia tra le sostituzioni visualizzate nel tipo o nelle relative interfacce dirette e indirette.</span><span class="sxs-lookup"><span data-stu-id="32795-334">We require that every interface and class have a *most specific override* for every interface method among the overrides appearing in the type or its direct and indirect interfaces.</span></span> <span data-ttu-id="32795-335">L'override *più specifico* è un override univoco più specifico di ogni altro override.</span><span class="sxs-lookup"><span data-stu-id="32795-335">The *most specific override* is a unique override that is more specific than every other override.</span></span> <span data-ttu-id="32795-336">Se non è presente alcun override, il metodo stesso viene considerato l'override più specifico.</span><span class="sxs-lookup"><span data-stu-id="32795-336">If there is no override, the method itself is considered the most specific override.</span></span>
>
> <span data-ttu-id="32795-337">Una sostituzione `M1` viene considerata *più specifica* di un'altra `M2` se `M1` viene dichiarata sul tipo `T1`, `M2` viene dichiarata nel tipo `T2`e</span><span class="sxs-lookup"><span data-stu-id="32795-337">One override `M1` is considered *more specific* than another override `M2` if `M1` is declared on type `T1`, `M2` is declared on type `T2`, and either</span></span>
>
> 1. <span data-ttu-id="32795-338">`T1` contiene `T2` tra le interfacce dirette o indirette oppure</span><span class="sxs-lookup"><span data-stu-id="32795-338">`T1` contains `T2` among its direct or indirect interfaces, or</span></span>
> 2. <span data-ttu-id="32795-339">`T2` è un tipo di interfaccia, ma `T1` non è un tipo di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32795-339">`T2` is an interface type but `T1` is not an interface type.</span></span>

<span data-ttu-id="32795-340">Lo scenario è il seguente</span><span class="sxs-lookup"><span data-stu-id="32795-340">The scenario is this</span></span>

```csharp
interface IA
{
    void M();
}
interface IB : IA
{
    override void M() { WriteLine("IB"); }
}
class Base : IA
{
    void IA.M() { WriteLine("Base"); }
}
class Derived : Base, IB // allowed?
{
    static void Main()
    {
        Ia a = new Derived();
        a.M();           // what does it do?
    }
}
```

<span data-ttu-id="32795-341">È necessario confermare questo comportamento (o decidere diversamente)</span><span class="sxs-lookup"><span data-stu-id="32795-341">We should confirm this behavior (or decide otherwise)</span></span>

> <span data-ttu-id="32795-342">***Problema chiuso:*** Confermare la specifica bozza, sopra, per *la sostituzione più specifica* , in quanto si applica a classi e interfacce miste (una classe ha la priorità su un'interfaccia).</span><span class="sxs-lookup"><span data-stu-id="32795-342">***Closed Issue:*** Confirm the draft spec, above, for *most specific override* as it applies to mixed classes and interfaces (a class takes priority over an interface).</span></span> <span data-ttu-id="32795-343">Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#diamonds-with-classes>.</span><span class="sxs-lookup"><span data-stu-id="32795-343">See <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#diamonds-with-classes>.</span></span>

### <a name="interface-methods-vs-structs-closed"></a><span data-ttu-id="32795-344">Metodi di interfaccia vs struct (closed)</span><span class="sxs-lookup"><span data-stu-id="32795-344">Interface methods vs structs (closed)</span></span>

<span data-ttu-id="32795-345">Esistono alcune spiacevoli interazioni tra i metodi di interfaccia predefiniti e gli struct.</span><span class="sxs-lookup"><span data-stu-id="32795-345">There are some unfortunate interactions between default interface methods and structs.</span></span>

```csharp
interface IA
{
    public void M() { }
}
struct S : IA
{
}
```

<span data-ttu-id="32795-346">Si noti che i membri di interfaccia non vengono ereditati:</span><span class="sxs-lookup"><span data-stu-id="32795-346">Note that interface members are not inherited:</span></span>

```csharp
var s = default(S);
s.M(); // error: 'S' does not contain a member 'M'
```

<span data-ttu-id="32795-347">Di conseguenza, il client deve eseguire il box dello struct per richiamare i metodi di interfaccia</span><span class="sxs-lookup"><span data-stu-id="32795-347">Consequently, the client must box the struct to invoke interface methods</span></span>

```csharp
IA s = default(S); // an S, boxed
s.M(); // ok
```

<span data-ttu-id="32795-348">La conversione boxing in questo modo vanifica i vantaggi principali di un tipo di `struct`.</span><span class="sxs-lookup"><span data-stu-id="32795-348">Boxing in this way defeats the principal benefits of a `struct` type.</span></span> <span data-ttu-id="32795-349">Inoltre, tutti i metodi di mutazione non avranno alcun effetto evidente, perché funzionano su una *copia boxed* dello struct:</span><span class="sxs-lookup"><span data-stu-id="32795-349">Moreover, any mutation methods will have no apparent effect, because they are operating on a *boxed copy* of the struct:</span></span>

```csharp
interface IB
{
    public void Increment() { P += 1; }
    public int P { get; set; }
}
struct T : IB
{
    public int P { get; set; } // auto-property
}

T t = default(T);
Console.WriteLine(t.P); // prints 0
(t as IB).Increment();
Console.WriteLine(t.P); // prints 0
```

> <span data-ttu-id="32795-350">***Problema chiuso:*** Cosa è possibile fare:</span><span class="sxs-lookup"><span data-stu-id="32795-350">***Closed Issue:*** What can we do about this:</span></span>
>
> 1. <span data-ttu-id="32795-351">Impedire a un `struct` di ereditare un'implementazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="32795-351">Forbid a `struct` from inheriting a default implementation.</span></span> <span data-ttu-id="32795-352">Tutti i metodi di interfaccia verrebbero considerati come astratti in una `struct`.</span><span class="sxs-lookup"><span data-stu-id="32795-352">All interface methods would be treated as abstract in a `struct`.</span></span> <span data-ttu-id="32795-353">In seguito, potrebbe essere necessario un po' di tempo per decidere come migliorarne il funzionamento.</span><span class="sxs-lookup"><span data-stu-id="32795-353">Then we may take time later to decide how to make it work better.</span></span>
> 2. <span data-ttu-id="32795-354">Si tratta di un tipo di strategia di generazione del codice che evita la conversione boxing.</span><span class="sxs-lookup"><span data-stu-id="32795-354">Come up with some kind of code generation strategy that avoids boxing.</span></span> <span data-ttu-id="32795-355">All'interno di un metodo come `IB.Increment`, il tipo di `this` sarebbe probabilmente analogo a un parametro di tipo vincolato al `IB`.</span><span class="sxs-lookup"><span data-stu-id="32795-355">Inside a method like `IB.Increment`, the type of `this` would perhaps be akin to a type parameter constrained to `IB`.</span></span> <span data-ttu-id="32795-356">In combinazione con tale, per evitare la conversione boxing nel chiamante, i metodi non astratti verrebbero ereditati dalle interfacce.</span><span class="sxs-lookup"><span data-stu-id="32795-356">In conjunction with that, to avoid boxing in the caller, non-abstract methods would be inherited from interfaces.</span></span> <span data-ttu-id="32795-357">Questo può migliorare notevolmente il lavoro di implementazione del compilatore e del CLR.</span><span class="sxs-lookup"><span data-stu-id="32795-357">This may increase compiler and CLR implementation work substantially.</span></span>
> 3. <span data-ttu-id="32795-358">Senza preoccuparsi di questo aspetto e lasciarlo semplicemente come una verruca.</span><span class="sxs-lookup"><span data-stu-id="32795-358">Not worry about it and just leave it as a wart.</span></span>
> 4. <span data-ttu-id="32795-359">Altre idee?</span><span class="sxs-lookup"><span data-stu-id="32795-359">Other ideas?</span></span>

<span data-ttu-id="32795-360">***Decisione:*** Senza preoccuparsi di questo aspetto e lasciarlo semplicemente come una verruca.</span><span class="sxs-lookup"><span data-stu-id="32795-360">***Decision:*** Not worry about it and just leave it as a wart.</span></span> <span data-ttu-id="32795-361">Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#structs-and-default-implementations>.</span><span class="sxs-lookup"><span data-stu-id="32795-361">See <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#structs-and-default-implementations>.</span></span>

### <a name="base-interface-invocations-closed"></a><span data-ttu-id="32795-362">Chiamate dell'interfaccia di base (chiuse)</span><span class="sxs-lookup"><span data-stu-id="32795-362">Base interface invocations (closed)</span></span>

<span data-ttu-id="32795-363">La specifica bozza suggerisce una sintassi per le chiamate dell'interfaccia di base ispirate da Java: `Interface.base.M()`.</span><span class="sxs-lookup"><span data-stu-id="32795-363">The draft spec suggests a syntax for base interface invocations inspired by Java: `Interface.base.M()`.</span></span> <span data-ttu-id="32795-364">È necessario selezionare una sintassi, almeno per il prototipo iniziale.</span><span class="sxs-lookup"><span data-stu-id="32795-364">We need to select a syntax, at least for the initial prototype.</span></span> <span data-ttu-id="32795-365">Il mio preferito è `base<Interface>.M()`.</span><span class="sxs-lookup"><span data-stu-id="32795-365">My favorite is `base<Interface>.M()`.</span></span>

> <span data-ttu-id="32795-366">***Problema chiuso:*** Qual è la sintassi per la chiamata di un membro di base?</span><span class="sxs-lookup"><span data-stu-id="32795-366">***Closed Issue:*** What is the syntax for a base member invocation?</span></span>

<span data-ttu-id="32795-367">***Decisione:*** La sintassi è `base(Interface).M()`.</span><span class="sxs-lookup"><span data-stu-id="32795-367">***Decision:*** The syntax is `base(Interface).M()`.</span></span> <span data-ttu-id="32795-368">Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#base-invocation>.</span><span class="sxs-lookup"><span data-stu-id="32795-368">See <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#base-invocation>.</span></span> <span data-ttu-id="32795-369">L'interfaccia denominata deve essere un'interfaccia di base, ma non deve essere un'interfaccia di base diretta.</span><span class="sxs-lookup"><span data-stu-id="32795-369">The interface so named must be a base interface, but does not need to be a direct base interface.</span></span>

> <span data-ttu-id="32795-370">***Problema aperto:*** È necessario consentire le chiamate dell'interfaccia di base nei membri della classe?</span><span class="sxs-lookup"><span data-stu-id="32795-370">***Open Issue:*** Should base interface invocations be permitted in class members?</span></span>

<span data-ttu-id="32795-371">***Decisione***: Sì.</span><span class="sxs-lookup"><span data-stu-id="32795-371">***Decision***: Yes.</span></span> <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md#base-invocation>

### <a name="overriding-non-public-interface-members-closed"></a><span data-ttu-id="32795-372">Override dei membri di interfaccia non pubblici (closed)</span><span class="sxs-lookup"><span data-stu-id="32795-372">Overriding non-public interface members (closed)</span></span>

<span data-ttu-id="32795-373">In un'interfaccia i membri non pubblici delle interfacce di base vengono sottoposti a override usando il modificatore `override`.</span><span class="sxs-lookup"><span data-stu-id="32795-373">In an interface, non-public members from base interfaces are overridden using the `override` modifier.</span></span> <span data-ttu-id="32795-374">Se si tratta di un override "esplicito" che assegna un nome all'interfaccia contenente il membro, il modificatore di accesso viene omesso.</span><span class="sxs-lookup"><span data-stu-id="32795-374">If it is an "explicit" override that names the interface containing the member, the access modifier is omitted.</span></span>

> <span data-ttu-id="32795-375">***Problema chiuso:*** Se si tratta di un override "implicito" che non assegna un nome all'interfaccia, il modificatore di accesso deve corrispondere?</span><span class="sxs-lookup"><span data-stu-id="32795-375">***Closed Issue:*** If it is an "implicit" override that does not name the interface, does the access modifier have to match?</span></span>

<span data-ttu-id="32795-376">***Decisione:*** Solo i membri pubblici possono essere sottoposti a override in modo implicito e l'accesso deve corrispondere.</span><span class="sxs-lookup"><span data-stu-id="32795-376">***Decision:*** Only public members may be implicitly overridden, and the access must match.</span></span> <span data-ttu-id="32795-377">Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md#dim-implementing-a-non-public-interface-member-not-in-list>.</span><span class="sxs-lookup"><span data-stu-id="32795-377">See <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md#dim-implementing-a-non-public-interface-member-not-in-list>.</span></span>

> <span data-ttu-id="32795-378">***Problema aperto:*** Il modificatore di accesso è obbligatorio, facoltativo o omesso in un override esplicito, ad esempio `override void IB.M() {}`?</span><span class="sxs-lookup"><span data-stu-id="32795-378">***Open Issue:*** Is the access modifier required, optional, or omitted on an explicit override such as `override void IB.M() {}`?</span></span>

> <span data-ttu-id="32795-379">***Problema aperto:*** È `override` obbligatorio, facoltativo o omesso in un override esplicito, ad esempio `void IB.M() {}`?</span><span class="sxs-lookup"><span data-stu-id="32795-379">***Open Issue:*** Is `override` required, optional, or omitted on an explicit override such as `void IB.M() {}`?</span></span>

<span data-ttu-id="32795-380">In che modo un membro di interfaccia non pubblico viene implementato in una classe?</span><span class="sxs-lookup"><span data-stu-id="32795-380">How does one implement a non-public interface member in a class?</span></span> <span data-ttu-id="32795-381">Forse è necessario eseguire questa operazione in modo esplicito?</span><span class="sxs-lookup"><span data-stu-id="32795-381">Perhaps it must be done explicitly?</span></span>

```csharp
interface IA
{
    internal void MI();
    protected void MP();
}
class C : IA
{
    // are these implementations?
    internal void MI() {}
    protected void MP() {}
}
```

> <span data-ttu-id="32795-382">***Problema chiuso:*** In che modo un membro di interfaccia non pubblico viene implementato in una classe?</span><span class="sxs-lookup"><span data-stu-id="32795-382">***Closed Issue:*** How does one implement a non-public interface member in a class?</span></span>

<span data-ttu-id="32795-383">***Decisione:*** È possibile implementare i membri di interfaccia non pubblici in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="32795-383">***Decision:*** You can only implement non-public interface members explicitly.</span></span> <span data-ttu-id="32795-384">Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md#dim-implementing-a-non-public-interface-member-not-in-list>.</span><span class="sxs-lookup"><span data-stu-id="32795-384">See <https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md#dim-implementing-a-non-public-interface-member-not-in-list>.</span></span>

<span data-ttu-id="32795-385">***Decisione***: nessuna parola chiave `override` consentita nei membri di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32795-385">***Decision***: No `override` keyword permitted on interface members.</span></span> <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#does-an-override-in-an-interface-introduce-a-new-member>

### <a name="binary-compatibility-2-closed"></a><span data-ttu-id="32795-386">Compatibilità binaria 2 (chiusa)</span><span class="sxs-lookup"><span data-stu-id="32795-386">Binary Compatibility 2 (closed)</span></span>

<span data-ttu-id="32795-387">Si consideri il codice seguente in cui ogni tipo si trova in un assembly separato</span><span class="sxs-lookup"><span data-stu-id="32795-387">Consider the following code in which each type is in a separate assembly</span></span>

```csharp
interface I1
{
    void M() { Impl1 }
}
interface I2 : I1
{
    override void M() { Impl2 }
}
interface I3 : I1
{
}
class C : I2, I3
{
}
```

<span data-ttu-id="32795-388">Si è consapevoli che l'implementazione di `I1.M` in `C` è `I2.M`.</span><span class="sxs-lookup"><span data-stu-id="32795-388">We understand that the implementation of `I1.M` in `C` is `I2.M`.</span></span> <span data-ttu-id="32795-389">Cosa accade se l'assembly contenente `I3` viene modificato come segue e ricompilato</span><span class="sxs-lookup"><span data-stu-id="32795-389">What if the assembly containing `I3` is changed as follows and recompiled</span></span>

```csharp
interface I3 : I1
{
    override void M() { Impl3 }
}
```

<span data-ttu-id="32795-390">ma `C` non viene ricompilato.</span><span class="sxs-lookup"><span data-stu-id="32795-390">but `C` is not recompiled.</span></span> <span data-ttu-id="32795-391">Cosa accade quando il programma viene eseguito?</span><span class="sxs-lookup"><span data-stu-id="32795-391">What happens when the program is run?</span></span> <span data-ttu-id="32795-392">Chiamata di `(C as I1).M()`</span><span class="sxs-lookup"><span data-stu-id="32795-392">An invocation of `(C as I1).M()`</span></span>

1. <span data-ttu-id="32795-393">Esegue `I1.M`</span><span class="sxs-lookup"><span data-stu-id="32795-393">Runs `I1.M`</span></span>
2. <span data-ttu-id="32795-394">Esegue `I2.M`</span><span class="sxs-lookup"><span data-stu-id="32795-394">Runs `I2.M`</span></span>
3. <span data-ttu-id="32795-395">Esegue `I3.M`</span><span class="sxs-lookup"><span data-stu-id="32795-395">Runs `I3.M`</span></span>
4. <span data-ttu-id="32795-396">2 o 3, in modo deterministico</span><span class="sxs-lookup"><span data-stu-id="32795-396">Either 2 or 3, deterministically</span></span>
5. <span data-ttu-id="32795-397">Genera un tipo di eccezione di runtime</span><span class="sxs-lookup"><span data-stu-id="32795-397">Throws some kind of runtime exception</span></span>

<span data-ttu-id="32795-398">***Decisione***: generare un'eccezione (5).</span><span class="sxs-lookup"><span data-stu-id="32795-398">***Decision***: Throw an exception (5).</span></span> <span data-ttu-id="32795-399">Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#issues-in-default-interface-methods>.</span><span class="sxs-lookup"><span data-stu-id="32795-399">See <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#issues-in-default-interface-methods>.</span></span>

### <a name="permit-partial-in-interface-closed"></a><span data-ttu-id="32795-400">Consentire `partial` nell'interfaccia?</span><span class="sxs-lookup"><span data-stu-id="32795-400">Permit `partial` in interface?</span></span> <span data-ttu-id="32795-401">chiuso</span><span class="sxs-lookup"><span data-stu-id="32795-401">(closed)</span></span>

<span data-ttu-id="32795-402">Dato che le interfacce possono essere utilizzate in modo analogo al modo in cui vengono utilizzate le classi astratte, può essere utile dichiararle `partial`.</span><span class="sxs-lookup"><span data-stu-id="32795-402">Given that interfaces may be used in ways analogous to the way abstract classes are used, it may be useful to declare them `partial`.</span></span> <span data-ttu-id="32795-403">Questa operazione è particolarmente utile in caso di generatori.</span><span class="sxs-lookup"><span data-stu-id="32795-403">This would be particularly useful in the face of generators.</span></span>

> <span data-ttu-id="32795-404">***Proposta:*** Rimuovere la restrizione della lingua che le interfacce e i membri delle interfacce non possono essere dichiarati `partial`.</span><span class="sxs-lookup"><span data-stu-id="32795-404">***Proposal:*** Remove the language restriction that interfaces and members of interfaces may not be declared `partial`.</span></span>

<span data-ttu-id="32795-405">***Decisione***: Sì.</span><span class="sxs-lookup"><span data-stu-id="32795-405">***Decision***: Yes.</span></span> <span data-ttu-id="32795-406">Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#permit-partial-in-interface>.</span><span class="sxs-lookup"><span data-stu-id="32795-406">See <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#permit-partial-in-interface>.</span></span>

### <a name="main-in-an-interface-closed"></a><span data-ttu-id="32795-407">`Main` in un'interfaccia?</span><span class="sxs-lookup"><span data-stu-id="32795-407">`Main` in an interface?</span></span> <span data-ttu-id="32795-408">chiuso</span><span class="sxs-lookup"><span data-stu-id="32795-408">(closed)</span></span>

> <span data-ttu-id="32795-409">***Problema aperto:*** Un metodo di `static Main` in un'interfaccia è candidato a essere il punto di ingresso del programma?</span><span class="sxs-lookup"><span data-stu-id="32795-409">***Open Issue:*** Is a `static Main` method in an interface a candidate to be the program's entry point?</span></span>

<span data-ttu-id="32795-410">***Decisione***: Sì.</span><span class="sxs-lookup"><span data-stu-id="32795-410">***Decision***: Yes.</span></span> <span data-ttu-id="32795-411">Vedere <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#main-in-an-interface>.</span><span class="sxs-lookup"><span data-stu-id="32795-411">See <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#main-in-an-interface>.</span></span>

### <a name="confirm-intent-to-support-public-non-virtual-methods-closed"></a><span data-ttu-id="32795-412">Conferma finalità per supportare metodi non virtuali pubblici (closed)</span><span class="sxs-lookup"><span data-stu-id="32795-412">Confirm intent to support public non-virtual methods (closed)</span></span>

<span data-ttu-id="32795-413">È possibile confermare o annullare la decisione di consentire metodi pubblici non virtuali in un'interfaccia?</span><span class="sxs-lookup"><span data-stu-id="32795-413">Can we please confirm (or reverse) our decision to permit non-virtual public methods in an interface?</span></span>

```csharp
interface IA
{
    public sealed void M() { }
}
```

> <span data-ttu-id="32795-414">***Problema semi-chiuso:*** (2017-04-18) pensiamo che sia utile, ma che tornerà.</span><span class="sxs-lookup"><span data-stu-id="32795-414">***Semi-Closed Issue:*** (2017-04-18) We think it is going to be useful, but will come back to it.</span></span> <span data-ttu-id="32795-415">Si tratta di un blocco di attivazione del modello mentale.</span><span class="sxs-lookup"><span data-stu-id="32795-415">This is a mental model tripping block.</span></span>

<span data-ttu-id="32795-416">***Decisione***: Sì.</span><span class="sxs-lookup"><span data-stu-id="32795-416">***Decision***: Yes.</span></span> <span data-ttu-id="32795-417"><https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#confirm-that-we-support-public-non-virtual-methods>.</span><span class="sxs-lookup"><span data-stu-id="32795-417"><https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#confirm-that-we-support-public-non-virtual-methods>.</span></span>

### <a name="does-an-override-in-an-interface-introduce-a-new-member-closed"></a><span data-ttu-id="32795-418">Un `override` in un'interfaccia introduce un nuovo membro?</span><span class="sxs-lookup"><span data-stu-id="32795-418">Does an `override` in an interface introduce a new member?</span></span> <span data-ttu-id="32795-419">chiuso</span><span class="sxs-lookup"><span data-stu-id="32795-419">(closed)</span></span>

<span data-ttu-id="32795-420">Esistono diversi modi per osservare se una dichiarazione di override introduce o meno un nuovo membro.</span><span class="sxs-lookup"><span data-stu-id="32795-420">There are a few ways to observe whether an override declaration introduces a new member or not.</span></span>

```csharp
interface IA
{
    void M(int x) { }
}
interface IB : IA
{
    override void M(int y) { }
}
interface IC : IB
{
    static void M2()
    {
        M(y: 3); // permitted?
    }
    override void IB.M(int z) { } // permitted? What does it override?
}
```

> <span data-ttu-id="32795-421">***Problema aperto:*** Una dichiarazione di override in un'interfaccia introduce un nuovo membro?</span><span class="sxs-lookup"><span data-stu-id="32795-421">***Open Issue:*** Does an override declaration in an interface introduce a new member?</span></span> <span data-ttu-id="32795-422">chiuso</span><span class="sxs-lookup"><span data-stu-id="32795-422">(closed)</span></span>

<span data-ttu-id="32795-423">In una classe un metodo di override è "visibile" in alcuni sensi.</span><span class="sxs-lookup"><span data-stu-id="32795-423">In a class, an overriding method is "visible" in some senses.</span></span> <span data-ttu-id="32795-424">I nomi dei parametri, ad esempio, hanno la precedenza sui nomi dei parametri nel metodo sottoposto a override.</span><span class="sxs-lookup"><span data-stu-id="32795-424">For example, the names of its parameters take precedence over the names of parameters in the overridden method.</span></span> <span data-ttu-id="32795-425">Potrebbe essere possibile duplicare questo comportamento nelle interfacce, in quanto esiste sempre un override più specifico.</span><span class="sxs-lookup"><span data-stu-id="32795-425">It may be possible to duplicate that behavior in interfaces, as there is always a most specific override.</span></span> <span data-ttu-id="32795-426">Si vuole però duplicare questo comportamento?</span><span class="sxs-lookup"><span data-stu-id="32795-426">But do we want to duplicate that behavior?</span></span>

<span data-ttu-id="32795-427">Inoltre, è possibile eseguire l'override di un metodo di override?</span><span class="sxs-lookup"><span data-stu-id="32795-427">Also, it is possible to "override" an override method?</span></span> <span data-ttu-id="32795-428">Discutibile</span><span class="sxs-lookup"><span data-stu-id="32795-428">[Moot]</span></span>

<span data-ttu-id="32795-429">***Decisione***: nessuna parola chiave `override` consentita nei membri di interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32795-429">***Decision***: No `override` keyword permitted on interface members.</span></span> <span data-ttu-id="32795-430"><https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#does-an-override-in-an-interface-introduce-a-new-member>.</span><span class="sxs-lookup"><span data-stu-id="32795-430"><https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#does-an-override-in-an-interface-introduce-a-new-member>.</span></span>

### <a name="properties-with-a-private-accessor-closed"></a><span data-ttu-id="32795-431">Proprietà con una funzione di accesso privata (closed)</span><span class="sxs-lookup"><span data-stu-id="32795-431">Properties with a private accessor (closed)</span></span>

<span data-ttu-id="32795-432">Si dice che i membri privati non sono virtuali e la combinazione di virtuali e privati non è consentita.</span><span class="sxs-lookup"><span data-stu-id="32795-432">We say that private members are not virtual, and the combination of virtual and private is disallowed.</span></span> <span data-ttu-id="32795-433">Ma cosa accade per una proprietà con una funzione di accesso privata?</span><span class="sxs-lookup"><span data-stu-id="32795-433">But what about a property with a private accessor?</span></span>

```csharp
interface IA
{
    public virtual int P
    {
        get => 3;
        private set => { }
    }
}
```

<span data-ttu-id="32795-434">Questa operazione è consentita?</span><span class="sxs-lookup"><span data-stu-id="32795-434">Is this allowed?</span></span> <span data-ttu-id="32795-435">La funzione di accesso `set` è `virtual` o meno?</span><span class="sxs-lookup"><span data-stu-id="32795-435">Is the `set` accessor here `virtual` or not?</span></span> <span data-ttu-id="32795-436">È possibile eseguirne l'override laddove è accessibile?</span><span class="sxs-lookup"><span data-stu-id="32795-436">Can it be overridden where it is accessible?</span></span> <span data-ttu-id="32795-437">Il codice seguente implementa in modo implicito solo la funzione di accesso `get`?</span><span class="sxs-lookup"><span data-stu-id="32795-437">Does the following implicitly implement only the `get` accessor?</span></span>

```csharp
class C : IA
{
    public int P
    {
        get => 4;
        set { }
    }
}
```

<span data-ttu-id="32795-438">Si tratta probabilmente di un errore in quanto IA. P. set non è virtuale e anche perché non è accessibile?</span><span class="sxs-lookup"><span data-stu-id="32795-438">Is the following presumably an error because IA.P.set isn't virtual and also because it isn't accessible?</span></span>

```csharp
class C : IA
{
    int IA.P
    {
        get => 4;
        set { }
    }
}
```

<span data-ttu-id="32795-439">***Decisione***: il primo esempio è valido, mentre l'ultima non lo è.</span><span class="sxs-lookup"><span data-stu-id="32795-439">***Decision***: The first example looks valid, while the last does not.</span></span> <span data-ttu-id="32795-440">Questo problema viene risolto in modo analogo a come funziona C#già in.</span><span class="sxs-lookup"><span data-stu-id="32795-440">This is resolved analogously to how it already works in C#.</span></span> <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#properties-with-a-private-accessor>

### <a name="base-interface-invocations-round-2-closed"></a><span data-ttu-id="32795-441">Chiamate dell'interfaccia di base, Round 2 (closed)</span><span class="sxs-lookup"><span data-stu-id="32795-441">Base Interface Invocations, round 2 (closed)</span></span>

<span data-ttu-id="32795-442">La "Risoluzione" precedente per gestire le chiamate di base non fornisce effettivamente un'espressività sufficiente.</span><span class="sxs-lookup"><span data-stu-id="32795-442">Our previous "resolution" to how to handle base invocations doesn't actually provide sufficient expressiveness.</span></span> <span data-ttu-id="32795-443">Si scopre che in C# e CLR, a differenza di Java, è necessario specificare sia l'interfaccia che contiene la dichiarazione del metodo che la posizione dell'implementazione da richiamare.</span><span class="sxs-lookup"><span data-stu-id="32795-443">It turns out that in C# and the CLR, unlike Java, you need to specify both the interface containing the method declaration and the location of the implementation you want to invoke.</span></span>

<span data-ttu-id="32795-444">Si propone la sintassi seguente per le chiamate di base nelle interfacce.</span><span class="sxs-lookup"><span data-stu-id="32795-444">I propose the following syntax for base calls in interfaces.</span></span> <span data-ttu-id="32795-445">Non mi piace, ma viene illustrato il modo in cui qualsiasi sintassi deve essere in grado di esprimere:</span><span class="sxs-lookup"><span data-stu-id="32795-445">I’m not in love with it, but it illustrates what any syntax must be able to express:</span></span>

```csharp
interface I1 { void M(); }
interface I2 { void M(); }
interface I3 : I1, I2 { void I1.M() { } void I2.M() { } }
interface I4 : I1, I2 { void I1.M() { } void I2.M() { } }
interface I5 : I3, I4
{
    void I1.M()
    {
        base<I3>(I1).M(); // calls I3's implementation of I1.M
        base<I4>(I1).M(); // calls I4's implementation of I1.M
    }
    void I2.M()
    {
        base<I3>(I2).M(); // calls I3's implementation of I2.M
        base<I4>(I2).M(); // calls I4's implementation of I2.M
    }
}
```

<span data-ttu-id="32795-446">In assenza di ambiguità, è possibile scriverla più semplicemente</span><span class="sxs-lookup"><span data-stu-id="32795-446">If there is no ambiguity, you can write it more simply</span></span>

```csharp
interface I1 { void M(); }
interface I3 : I1 { void I1.M() { } }
interface I4 : I1 { void I1.M() { } }
interface I5 : I3, I4
{
    void I1.M()
    {
        base<I3>.M(); // calls I3's implementation of I1.M
        base<I4>.M(); // calls I4's implementation of I1.M
    }
}
```

<span data-ttu-id="32795-447">Oppure</span><span class="sxs-lookup"><span data-stu-id="32795-447">Or</span></span>

```csharp
interface I1 { void M(); }
interface I2 { void M(); }
interface I3 : I1, I2 { void I1.M() { } void I2.M() { } }
interface I5 : I3
{
    void I1.M()
    {
        base(I1).M(); // calls I3's implementation of I1.M
    }
    void I2.M()
    {
        base(I2).M(); // calls I3's implementation of I2.M
    }
}
```

<span data-ttu-id="32795-448">Oppure</span><span class="sxs-lookup"><span data-stu-id="32795-448">Or</span></span>

```csharp
interface I1 { void M(); }
interface I3 : I1 { void I1.M() { } }
interface I5 : I3
{
    void I1.M()
    {
        base.M(); // calls I3's implementation of I1.M
    }
}
```

<span data-ttu-id="32795-449">***Decisione***: si è deciso di `base(N.I1<T>).M(s)`, che se è presente un'associazione di chiamata, è possibile che si verifichi un problema in un secondo momento.</span><span class="sxs-lookup"><span data-stu-id="32795-449">***Decision***: Decided on `base(N.I1<T>).M(s)`, conceding that if we have an invocation binding there may be problem here later on.</span></span> <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-11-14.md#default-interface-implementations>

### <a name="warning-for-struct-not-implementing-default-method-closed"></a><span data-ttu-id="32795-450">Avviso per struct che non implementa il metodo predefinito?</span><span class="sxs-lookup"><span data-stu-id="32795-450">Warning for struct not implementing default method?</span></span> <span data-ttu-id="32795-451">chiuso</span><span class="sxs-lookup"><span data-stu-id="32795-451">(closed)</span></span>

<span data-ttu-id="32795-452">@vancem dichiara che è consigliabile generare un avviso se una dichiarazione del tipo di valore non è in grado di eseguire l'override di un metodo di interfaccia, anche se eredita un'implementazione di tale metodo da un'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32795-452">@vancem asserts that we should seriously consider producing a warning if a value type declaration fails to override some interface method, even if it would inherit an implementation of that method from an interface.</span></span> <span data-ttu-id="32795-453">Poiché causa la conversione boxing e le chiamate vincolate.</span><span class="sxs-lookup"><span data-stu-id="32795-453">Because it causes boxing and undermines constrained calls.</span></span>

<span data-ttu-id="32795-454">***Decisione***: questo aspetto è più adatto per un analizzatore.</span><span class="sxs-lookup"><span data-stu-id="32795-454">***Decision***: This seems like something more suited for an analyzer.</span></span> <span data-ttu-id="32795-455">Sembra anche che questo avviso potrebbe essere fastidioso, perché verrebbe attivato anche se il metodo di interfaccia predefinito non viene mai chiamato e non verrà mai eseguita alcuna conversione boxing.</span><span class="sxs-lookup"><span data-stu-id="32795-455">It also seems like this warning could be noisy, since it would fire even if the default interface method is never called and no boxing will ever occur.</span></span> <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#warning-for-struct-not-implementing-default-method>

### <a name="interface-static-constructors-closed"></a><span data-ttu-id="32795-456">Costruttori statici di interfaccia (closed)</span><span class="sxs-lookup"><span data-stu-id="32795-456">Interface static constructors (closed)</span></span>

<span data-ttu-id="32795-457">Quando vengono eseguiti i costruttori statici di interfaccia?</span><span class="sxs-lookup"><span data-stu-id="32795-457">When are interface static constructors run?</span></span>  <span data-ttu-id="32795-458">La bozza corrente dell'interfaccia della riga di comando si propone di verificarsi quando si accede al primo metodo o campo statico.</span><span class="sxs-lookup"><span data-stu-id="32795-458">The current CLI draft proposes that it occurs when the first static method or field is accessed.</span></span> <span data-ttu-id="32795-459">Se non sono presenti, è possibile che non venga mai eseguita?</span><span class="sxs-lookup"><span data-stu-id="32795-459">If there are neither of those then it might never be run??</span></span>

<span data-ttu-id="32795-460">[2018-10-09 il team CLR propone "rispecchiare le operazioni da eseguire per i ValueType (controllo cctor per l'accesso a ogni metodo di istanza)"]</span><span class="sxs-lookup"><span data-stu-id="32795-460">[2018-10-09 The CLR team proposes "Going to mirror what we do for valuetypes (cctor check on access to each instance method)"]</span></span>

<span data-ttu-id="32795-461">***Decisione***: i costruttori statici vengono eseguiti anche in presenza di metodi di istanza, se il costruttore statico non è `beforefieldinit`, nel qual caso i costruttori statici vengono eseguiti prima dell'accesso al primo campo statico.</span><span class="sxs-lookup"><span data-stu-id="32795-461">***Decision***: Static constructors are also run on entry to instance methods, if the static constructor was not `beforefieldinit`, in which case static constructors are run before access to the first static field.</span></span> <https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md#when-are-interface-static-constructors-run>

## <a name="design-meetings"></a><span data-ttu-id="32795-462">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="32795-462">Design meetings</span></span>

<span data-ttu-id="32795-463">[2017-03-08 note](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-08.md) sulla riunione LDM
[2017-03-21 LDM note sulla riunione](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-21.md)
[2017-03-23 riunione "comportamento CLR per i metodi di interfaccia predefiniti"](https://github.com/dotnet/csharplang/blob/master/meetings/2017/CLR-2017-03-23.md)
[2017-04-05 LDM riunioni note](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-05.md)
[2017-04-11 LDM riunione note](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-11.md)
[2017-04-18 LDM riunione note](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md)
[2017-04-19 LDM riunione note](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md)
[2017-05-17 LDM riunione](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-17.md) note
[2017-05-31 LDM riunione note](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-31.md) [
2017-06-14 LDM Note sulla riunione](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-14.md)
2018-10-17 note sulla riunione [LDM](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md)
[2018-11-14 LDM](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-11-14.md)</span><span class="sxs-lookup"><span data-stu-id="32795-463">[2017-03-08 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-08.md)
[2017-03-21 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-03-21.md)
[2017-03-23 meeting "CLR Behavior for Default Interface Methods"](https://github.com/dotnet/csharplang/blob/master/meetings/2017/CLR-2017-03-23.md)
[2017-04-05 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-05.md)
[2017-04-11 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-11.md)
[2017-04-18 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-18.md)
[2017-04-19 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-04-19.md)
[2017-05-17 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-17.md)
[2017-05-31 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-31.md)
[2017-06-14 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-14.md)
[2018-10-17 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md)
[2018-11-14 LDM Meeting Notes](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-11-14.md)</span></span>
