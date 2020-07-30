---
ms.openlocfilehash: 9cfc0758f16b2153d52faec1d19f0ecd817cde3b
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297476"
---
# <a name="covariant-return-types"></a><span data-ttu-id="89711-101">tipi restituiti covarianti</span><span class="sxs-lookup"><span data-stu-id="89711-101">covariant return types</span></span>

* <span data-ttu-id="89711-102">[x] proposto</span><span class="sxs-lookup"><span data-stu-id="89711-102">[x] Proposed</span></span>
* <span data-ttu-id="89711-103">[] Prototipo: non avviato</span><span class="sxs-lookup"><span data-stu-id="89711-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="89711-104">[] Implementazione: non avviata</span><span class="sxs-lookup"><span data-stu-id="89711-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="89711-105">[X] Specifica: non avviata</span><span class="sxs-lookup"><span data-stu-id="89711-105">[X] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="89711-106">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="89711-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="89711-107">Supportare _tipi restituiti covariante_.</span><span class="sxs-lookup"><span data-stu-id="89711-107">Support _covariant return types_.</span></span> <span data-ttu-id="89711-108">In particolare, consentire all'override di un metodo di restituire un tipo restituito più derivato rispetto al metodo sottoposto a override e in modo analogo per consentire l'override di una proprietà di sola lettura per restituire un tipo restituito più derivato.</span><span class="sxs-lookup"><span data-stu-id="89711-108">Specifically, permit the override of a method to return a more derived return type than the method it overrides, and similarly to permit the override of a read-only property to return a more derived return type.</span></span> <span data-ttu-id="89711-109">I chiamanti del metodo o della proprietà riceveranno in modo statico il tipo restituito più raffinato da una chiamata e le sostituzioni visualizzate in più tipi derivati sarebbero necessarie per fornire un tipo restituito almeno in base a quanto visualizzato nelle sostituzioni nei relativi tipi di base.</span><span class="sxs-lookup"><span data-stu-id="89711-109">Callers of the method or property would statically receive the more refined return type from an invocation, and overrides appearing in more derived types would be required to provide a return type at least as specific as that appearing in overrides in its base types.</span></span>

## <a name="motivation"></a><span data-ttu-id="89711-110">Motivazione</span><span class="sxs-lookup"><span data-stu-id="89711-110">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="89711-111">Si tratta di un modello comune nel codice in cui è necessario inventare nomi di metodo diversi per aggirare il vincolo di linguaggio che le sostituzioni devono restituire lo stesso tipo del metodo sottoposto a override.</span><span class="sxs-lookup"><span data-stu-id="89711-111">It is a common pattern in code that different method names have to be invented to work around the language constraint that overrides must return the same type as the overridden method.</span></span>

<span data-ttu-id="89711-112">Questa operazione è utile nel modello Factory.</span><span class="sxs-lookup"><span data-stu-id="89711-112">This would be useful in the factory pattern.</span></span> <span data-ttu-id="89711-113">Ad esempio, nella base di codice Roslyn avremmo</span><span class="sxs-lookup"><span data-stu-id="89711-113">For example, in the Roslyn code base we would have</span></span>

``` cs
class Compilation ...
{
    virtual Compilation WithOptions(Options options)...
}
```

``` cs
class CSharpCompilation : Compilation
{
    override CSharpCompilation WithOptions(Options options)...
}
```

## <a name="detailed-design"></a><span data-ttu-id="89711-114">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="89711-114">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="89711-115">Si tratta di una bozza proposta per i [tipi restituiti covarianti](https://github.com/dotnet/csharplang/issues/49) in C#.</span><span class="sxs-lookup"><span data-stu-id="89711-115">This is a draft proposed specification for [covariant return types](https://github.com/dotnet/csharplang/issues/49) in C#.</span></span>  <span data-ttu-id="89711-116">Il nostro obiettivo è consentire l'override di un metodo per restituire un tipo restituito più derivato rispetto al metodo sottoposto a override e in modo analogo per consentire all'override di una proprietà di sola lettura di restituire un tipo restituito più derivato.</span><span class="sxs-lookup"><span data-stu-id="89711-116">Our intent is to permit the override of a method to return a more derived return type than the method it overrides, and similarly to permit the override of a read-only property to return a more derived return type.</span></span>  <span data-ttu-id="89711-117">I chiamanti del metodo o della proprietà riceveranno in modo statico il tipo restituito più raffinato da una chiamata e le sostituzioni visualizzate in più tipi derivati sarebbero necessarie per fornire un tipo restituito almeno in base a quanto visualizzato nelle sostituzioni nei relativi tipi di base.</span><span class="sxs-lookup"><span data-stu-id="89711-117">Callers of the method or property would statically receive the more refined return type from an invocation, and overrides appearing in more derived types would be required to provide a return type at least as specific as that appearing in overrides in its base types.</span></span>

<span data-ttu-id="89711-118">Si tratta di una prima bozza, quindi è stata necessariamente inventata da zero.</span><span class="sxs-lookup"><span data-stu-id="89711-118">This is a first draft, so it was necessarily invented from scratch.</span></span>  <span data-ttu-id="89711-119">Molte delle idee introdotte sono provvisorie e possono essere modificate o eliminate nelle future revisioni.</span><span class="sxs-lookup"><span data-stu-id="89711-119">Many of the ideas introduced are tentative and may be revised or eliminated in future revisions.</span></span>

--------------

### <a name="class-method-override"></a><span data-ttu-id="89711-120">Override del metodo della classe</span><span class="sxs-lookup"><span data-stu-id="89711-120">Class Method Override</span></span>

<span data-ttu-id="89711-121">[Vincolo esistente sui metodi di override della classe](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#override-methods)</span><span class="sxs-lookup"><span data-stu-id="89711-121">The [existing constraint on class override](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#override-methods) methods</span></span>

> - <span data-ttu-id="89711-122">Il metodo di override e il metodo di base sottoposto a override hanno lo stesso tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="89711-122">The override method and the overridden base method have the same return type.</span></span>

<span data-ttu-id="89711-123">viene modificato in</span><span class="sxs-lookup"><span data-stu-id="89711-123">is modified to</span></span>

> - <span data-ttu-id="89711-124">Il metodo di override deve avere un tipo restituito convertibile da un'identità o conversione di un riferimento implicito al tipo restituito del metodo di base sottoposto a override.</span><span class="sxs-lookup"><span data-stu-id="89711-124">The override method must have have a return type that is convertible by an identity or implicit reference conversion to the return type of the overridden base method.</span></span>

<span data-ttu-id="89711-125">E i seguenti requisiti aggiuntivi vengono aggiunti all'elenco:</span><span class="sxs-lookup"><span data-stu-id="89711-125">And the following additional requirements are appended to that list:</span></span>

> - <span data-ttu-id="89711-126">Il metodo di override deve avere un tipo restituito convertibile da un'identità o conversione di un riferimento implicito nel tipo restituito di ogni override del metodo di base sottoposto a override dichiarato in un tipo di base (diretto o indiretto) del metodo di override.</span><span class="sxs-lookup"><span data-stu-id="89711-126">The override method must have have a return type that is convertible by an identity or implicit reference conversion to the return type of every override of the overridden base method that is declared in a (direct or indirect) base type of the override method.</span></span>
> - <span data-ttu-id="89711-127">Il tipo restituito del metodo di override deve essere accessibile almeno quanto il metodo di override ([domini di accessibilità](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#accessibility-domains)).</span><span class="sxs-lookup"><span data-stu-id="89711-127">The override method's return type must be at least as accessible as the override method  ([Accessibility domains](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#accessibility-domains)).</span></span>

<span data-ttu-id="89711-128">Questo vincolo consente a un metodo di override in una `private` classe di avere un `private` tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="89711-128">This constraint permits an override method in a `private` class to have a `private` return type.</span></span>  <span data-ttu-id="89711-129">Tuttavia, richiede un `public` metodo di override in un `public` tipo per avere un `public` tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="89711-129">However it requires a `public` override method in a `public` type to have a `public` return type.</span></span>

### <a name="class-property-and-indexer-override"></a><span data-ttu-id="89711-130">Override della proprietà della classe e dell'indicizzatore</span><span class="sxs-lookup"><span data-stu-id="89711-130">Class Property and Indexer Override</span></span>

<span data-ttu-id="89711-131">[Vincolo esistente sulle proprietà di override della classe](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#virtual-sealed-override-and-abstract-property-accessors)</span><span class="sxs-lookup"><span data-stu-id="89711-131">The [existing constraint on class override](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#virtual-sealed-override-and-abstract-property-accessors) properties</span></span>

> <span data-ttu-id="89711-132">Una dichiarazione di proprietà che esegue l'override deve specificare esattamente gli stessi modificatori di accessibilità e il nome della proprietà ereditata e deve essere presente una conversione di identità ~~tra il tipo di override e la proprietà ereditata~~.</span><span class="sxs-lookup"><span data-stu-id="89711-132">An overriding property declaration shall specify the exact same accessibility modifiers and name as the inherited property, and there shall be an identity conversion ~~between the type of the overriding and the inherited property~~.</span></span> <span data-ttu-id="89711-133">Se la proprietà ereditata ha solo una singola funzione di accesso (ad esempio, se la proprietà ereditata è di sola lettura o di sola scrittura), la proprietà che esegue l'override deve includere solo tale funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="89711-133">If the inherited property has only a single accessor (i.e., if the inherited property is read-only or write-only), the overriding property shall include only that accessor.</span></span> <span data-ttu-id="89711-134">Se la proprietà ereditata include entrambe le funzioni di accesso (ad esempio, se la proprietà ereditata è di lettura/scrittura), la proprietà che esegue l'override può includere una singola funzione di accesso o entrambe le funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="89711-134">If the inherited property includes both accessors (i.e., if the inherited property is read-write), the overriding property can include either a single accessor or both accessors.</span></span>

<span data-ttu-id="89711-135">viene modificato in</span><span class="sxs-lookup"><span data-stu-id="89711-135">is modified to</span></span>

> <span data-ttu-id="89711-136">Una dichiarazione di proprietà che esegue l'override deve specificare esattamente gli stessi modificatori di accessibilità e il nome della proprietà ereditata, ed è presente una conversione di identità **o (se la proprietà ereditata è di sola lettura) conversione di riferimento implicita dal tipo della proprietà che esegue l'override al tipo della proprietà ereditata**.</span><span class="sxs-lookup"><span data-stu-id="89711-136">An overriding property declaration shall specify the exact same accessibility modifiers and name as the inherited property, and there shall be an identity conversion **or (if the inherited property is read-only) implicit reference conversion from the type of the overriding property to the type of the inherited property**.</span></span> <span data-ttu-id="89711-137">Se la proprietà ereditata ha solo una singola funzione di accesso (ad esempio, se la proprietà ereditata è di sola lettura o di sola scrittura), la proprietà che esegue l'override deve includere solo tale funzione di accesso.</span><span class="sxs-lookup"><span data-stu-id="89711-137">If the inherited property has only a single accessor (i.e., if the inherited property is read-only or write-only), the overriding property shall include only that accessor.</span></span> <span data-ttu-id="89711-138">Se la proprietà ereditata include entrambe le funzioni di accesso (ad esempio, se la proprietà ereditata è di lettura/scrittura), la proprietà che esegue l'override può includere una singola funzione di accesso o entrambe le funzioni di accesso.</span><span class="sxs-lookup"><span data-stu-id="89711-138">If the inherited property includes both accessors (i.e., if the inherited property is read-write), the overriding property can include either a single accessor or both accessors.</span></span> <span data-ttu-id="89711-139">**Il tipo della proprietà che esegue l'override deve essere accessibile almeno quanto la proprietà di override ([domini di accessibilità](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#accessibility-domains)).**</span><span class="sxs-lookup"><span data-stu-id="89711-139">**The overriding property's type must be at least as accessible as the overriding property ([Accessibility domains](https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#accessibility-domains)).**</span></span>

<span data-ttu-id="89711-140">***Il resto della specifica bozza riportata di seguito propone un'ulteriore estensione per i ritorni covariante dei metodi di interfaccia da prendere in considerazione in un secondo momento.***</span><span class="sxs-lookup"><span data-stu-id="89711-140">***The remainder of the draft specification below proposes a further extension to covariant returns of interface methods to be considered later.***</span></span>

### <a name="interface-method-property-and-indexer-override"></a><span data-ttu-id="89711-141">Override del metodo di interfaccia, della proprietà e dell'indicizzatore</span><span class="sxs-lookup"><span data-stu-id="89711-141">Interface Method, Property, and Indexer Override</span></span>

<span data-ttu-id="89711-142">Aggiungendo ai tipi di membri consentiti in un'interfaccia con l'aggiunta della funzionalità DIM in C# 8,0, viene aggiunto ulteriormente il supporto per `override` i membri insieme a Returns covariante.</span><span class="sxs-lookup"><span data-stu-id="89711-142">Adding to the kinds of members that are permitted in an interface with the addition of the DIM feature in C# 8.0, we further add support for `override` members along with covariant returns.</span></span>  <span data-ttu-id="89711-143">Seguono le regole dei `override` membri come specificato per le classi, con le differenze seguenti:</span><span class="sxs-lookup"><span data-stu-id="89711-143">These follow the rules of `override` members as specified for classes, with the following differences:</span></span>

<span data-ttu-id="89711-144">Il testo seguente nelle classi:</span><span class="sxs-lookup"><span data-stu-id="89711-144">The following text in classes:</span></span>

> <span data-ttu-id="89711-145">Il metodo sottoposto a override da una dichiarazione di override è noto come ***metodo di base sottoposto***a override.</span><span class="sxs-lookup"><span data-stu-id="89711-145">The method overridden by an override declaration is known as the ***overridden base method***.</span></span> <span data-ttu-id="89711-146">Per un metodo di override `M` dichiarato in una classe `C` , il metodo di base sottoposto a override viene determinato esaminando ogni classe base di `C` , iniziando con la classe di base diretta di `C` e continuando con ogni classe di base diretta successiva, finché in un determinato tipo di classe di base viene individuato almeno un metodo accessibile con la stessa firma di `M` dopo la sostituzione di argomenti di tipo.</span><span class="sxs-lookup"><span data-stu-id="89711-146">For an override method `M` declared in a class `C`, the overridden base method is determined by examining each base class of `C`, starting with the direct base class of `C` and continuing with each successive direct base class, until in a given base class type at least one accessible method is located which has the same signature as `M` after substitution of type arguments.</span></span>

<span data-ttu-id="89711-147">viene fornita la specifica corrispondente per le interfacce:</span><span class="sxs-lookup"><span data-stu-id="89711-147">is given the corresponding specification for interfaces:</span></span>

> <span data-ttu-id="89711-148">Il metodo sottoposto a override da una dichiarazione di override è noto come ***metodo di base sottoposto***a override.</span><span class="sxs-lookup"><span data-stu-id="89711-148">The method overridden by an override declaration is known as the ***overridden base method***.</span></span> <span data-ttu-id="89711-149">Per un metodo di override `M` dichiarato in un'interfaccia `I` , il metodo di base sottoposto a override viene determinato esaminando ogni interfaccia di base diretta o indiretta di `I` , raccogliendo il set di interfacce che dichiarano un metodo accessibile con la stessa firma di `M` dopo la sostituzione degli argomenti di tipo.</span><span class="sxs-lookup"><span data-stu-id="89711-149">For an override method `M` declared in an interface `I`, the overridden base method is determined by examining each direct or indirect base interface of `I`, collecting the set of interfaces declaring an accessible method which has the same signature as `M` after substitution of type arguments.</span></span>  <span data-ttu-id="89711-150">Se questo set di interfacce dispone di un *tipo più derivato*, a cui è associata una conversione di identità o di riferimento implicita da ogni tipo in questo set e tale tipo contiene una dichiarazione di metodo univoca, questo è il *metodo di base sottoposto a override*.</span><span class="sxs-lookup"><span data-stu-id="89711-150">If this set of interfaces has a *most derived type*, to which there is an identity or implicit reference conversion from every type in this set, and that type contains a unique such method declaration, then that is the *overridden base method*.</span></span>

<span data-ttu-id="89711-151">In modo analogo, le proprietà e gli indicizzatori sono consentiti `override` nelle interfacce come specificato per le classi nelle *funzioni di accesso 15.7.6 Virtual, sealed, override e abstract*.</span><span class="sxs-lookup"><span data-stu-id="89711-151">We similarly permit `override` properties and indexers in interfaces as specified for classes in *15.7.6 Virtual, sealed, override, and abstract accessors*.</span></span>

### <a name="name-lookup"></a><span data-ttu-id="89711-152">Ricerca nome</span><span class="sxs-lookup"><span data-stu-id="89711-152">Name Lookup</span></span>

<span data-ttu-id="89711-153">La ricerca del nome in presenza di `override` dichiarazioni di classe modifica attualmente il risultato della ricerca del nome imponendo i dettagli dei membri trovati dalla dichiarazione più derivata `override` nella gerarchia di classi a partire dal tipo di qualificatore dell'identificatore (o `this` quando non è presente alcun qualificatore).</span><span class="sxs-lookup"><span data-stu-id="89711-153">Name lookup in the presence of class `override` declarations currently modify the result of name lookup by imposing on the found member details from the most derived `override` declaration in the class hierarchy starting from the type of the identifier's qualifier (or `this` when there is no qualifier).</span></span>  <span data-ttu-id="89711-154">Ad esempio, in *12.6.2.2 parametri corrispondenti* abbiamo</span><span class="sxs-lookup"><span data-stu-id="89711-154">For example, in *12.6.2.2 Corresponding parameters* we have</span></span>

> <span data-ttu-id="89711-155">Per i metodi virtuali e gli indicizzatori definiti nelle classi, l'elenco di parametri viene selezionato dalla prima dichiarazione o dall'override del membro della funzione trovato quando si inizia con il tipo statico del destinatario e si cerca nelle relative classi di base.</span><span class="sxs-lookup"><span data-stu-id="89711-155">For virtual methods and indexers defined in classes, the parameter list is picked from the first  declaration or override of the function member found when starting with the static type of the receiver, and searching through its base classes.</span></span>

<span data-ttu-id="89711-156">per aggiungere</span><span class="sxs-lookup"><span data-stu-id="89711-156">to this we add</span></span>

> <span data-ttu-id="89711-157">Per i metodi virtuali e gli indicizzatori definiti nelle interfacce, l'elenco di parametri viene selezionato dalla dichiarazione o dall'override del membro della funzione trovato nel tipo più derivato tra quei tipi che contengono la dichiarazione di override del membro di funzione.</span><span class="sxs-lookup"><span data-stu-id="89711-157">For virtual methods and indexers defined in interfaces, the parameter list is picked from the declaration or override of the function member found in the most derived type among those types containing the declaration of override of the function member.</span></span>  <span data-ttu-id="89711-158">Si tratta di un errore in fase di compilazione se non esiste alcun tipo univoco di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="89711-158">It is a compile-time error if no unique such type exists.</span></span>

<span data-ttu-id="89711-159">Per il tipo di risultato di un accesso a una proprietà o a un indicizzatore, il testo esistente</span><span class="sxs-lookup"><span data-stu-id="89711-159">For the result type of a property or indexer access, the existing text</span></span>

> - <span data-ttu-id="89711-160">Se si identifica una proprietà di istanza, il risultato è un accesso alla proprietà con un'espressione di istanza associata di e e un tipo associato che è il tipo della proprietà.</span><span class="sxs-lookup"><span data-stu-id="89711-160">If I identifies an instance property, then the result is a property access with an associated instance expression of E and an associated type that is the type of the property.</span></span> <span data-ttu-id="89711-161">Se T è un tipo di classe, il tipo associato viene selezionato dalla prima dichiarazione o override della proprietà trovata quando si inizia con T e si esegue una ricerca nelle relative classi di base.</span><span class="sxs-lookup"><span data-stu-id="89711-161">If T is a class type, the associated type is picked from the first declaration or override of the property found when starting with T, and searching through its base classes.</span></span>

<span data-ttu-id="89711-162">è aumentata con</span><span class="sxs-lookup"><span data-stu-id="89711-162">is augmented with</span></span>

> <span data-ttu-id="89711-163">Se T è un tipo di interfaccia, il tipo associato viene selezionato dalla dichiarazione o dall'override della proprietà trovata nell'oggetto più derivato di T o nelle interfacce di base dirette o indirette.</span><span class="sxs-lookup"><span data-stu-id="89711-163">If T is an interface type, the associated type is picked from the declaration or override of the property found in the most derived of T or its direct or indirect base interfaces.</span></span>  <span data-ttu-id="89711-164">Si tratta di un errore in fase di compilazione se non esiste alcun tipo univoco di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="89711-164">It is a compile-time error if no unique such type exists.</span></span>

<span data-ttu-id="89711-165">È necessario apportare una modifica simile nell' *accesso dell'indicizzatore 12.7.7.3*</span><span class="sxs-lookup"><span data-stu-id="89711-165">A similar change should be made in *12.7.7.3 Indexer access*</span></span>

<span data-ttu-id="89711-166">Nelle *espressioni di chiamata 12.7.6* si aumenta il testo esistente</span><span class="sxs-lookup"><span data-stu-id="89711-166">In *12.7.6 Invocation expressions* we augment the existing text</span></span>

> - <span data-ttu-id="89711-167">In caso contrario, il risultato è un valore, con un tipo associato del tipo restituito del metodo o del delegato.</span><span class="sxs-lookup"><span data-stu-id="89711-167">Otherwise, the result is a value, with an associated type of the return type of the method or delegate.</span></span> <span data-ttu-id="89711-168">Se la chiamata è di un metodo di istanza e il ricevitore è di un tipo di classe T, il tipo associato viene selezionato dalla prima dichiarazione o dall'override del metodo trovato quando si inizia con T e si esegue la ricerca nelle relative classi di base.</span><span class="sxs-lookup"><span data-stu-id="89711-168">If the invocation is of an instance method, and the receiver is of a class type T, the associated type is picked from the first declaration or override of the method found when starting with T and searching through its base classes.</span></span>

<span data-ttu-id="89711-169">con</span><span class="sxs-lookup"><span data-stu-id="89711-169">with</span></span>

> <span data-ttu-id="89711-170">Se la chiamata è di un metodo di istanza e il ricevitore è di un tipo di interfaccia T, il tipo associato viene selezionato dalla dichiarazione o dall'override del metodo trovato nell'interfaccia più derivata tra T e le interfacce di base dirette e indirette.</span><span class="sxs-lookup"><span data-stu-id="89711-170">If the invocation is of an instance method, and the receiver is of an interface type T, the associated type is picked from the declaration or override of the method found in the most derived interface from among T and its direct and indirect base interfaces.</span></span>  <span data-ttu-id="89711-171">Si tratta di un errore in fase di compilazione se non esiste alcun tipo univoco di questo tipo.</span><span class="sxs-lookup"><span data-stu-id="89711-171">It is a compile-time error if no unique such type exists.</span></span>

### <a name="implicit-interface-implementations"></a><span data-ttu-id="89711-172">Implementazioni di interfacce implicite</span><span class="sxs-lookup"><span data-stu-id="89711-172">Implicit Interface Implementations</span></span>

<span data-ttu-id="89711-173">Questa sezione della specifica</span><span class="sxs-lookup"><span data-stu-id="89711-173">This section of the specification</span></span>

> <span data-ttu-id="89711-174">Ai fini del mapping dell'interfaccia, un membro di classe `A` corrisponde a un membro di interfaccia `B` quando:</span><span class="sxs-lookup"><span data-stu-id="89711-174">For purposes of interface mapping, a class member `A` matches an interface member `B` when:</span></span>
> 
> - <span data-ttu-id="89711-175">`A`e `B` sono metodi e gli elenchi nome, tipo e parametro formale di `A` e `B` sono identici.</span><span class="sxs-lookup"><span data-stu-id="89711-175">`A` and `B` are methods, and the name, type, and formal parameter lists of `A` and `B` are identical.</span></span>
> - <span data-ttu-id="89711-176">`A`e `B` sono proprietà, il nome e il tipo di `A` e `B` sono identici e hanno `A` le stesse funzioni di accesso di `B` ( `A` possono avere funzioni di accesso aggiuntive se non si tratta di un'implementazione esplicita di un membro di interfaccia).</span><span class="sxs-lookup"><span data-stu-id="89711-176">`A` and `B` are properties, the name and type of `A` and `B` are identical, and `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation).</span></span>
> - <span data-ttu-id="89711-177">`A`e `B` sono eventi e il nome e il tipo di `A` e `B` sono identici.</span><span class="sxs-lookup"><span data-stu-id="89711-177">`A` and `B` are events, and the name and type of `A` and `B` are identical.</span></span>
> - <span data-ttu-id="89711-178">`A`e `B` sono indicizzatori, gli elenchi di parametri di tipo e formale di `A` e `B` sono identici e `A` hanno le stesse funzioni di accesso di `B` ( `A` possono avere funzioni di accesso aggiuntive se non si tratta di un'implementazione esplicita di un membro di interfaccia).</span><span class="sxs-lookup"><span data-stu-id="89711-178">`A` and `B` are indexers, the type and formal parameter lists of `A` and `B` are identical, and `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation).</span></span>

<span data-ttu-id="89711-179">viene modificato come segue:</span><span class="sxs-lookup"><span data-stu-id="89711-179">is modified as follows:</span></span>

> <span data-ttu-id="89711-180">Ai fini del mapping dell'interfaccia, un membro di classe `A` corrisponde a un membro di interfaccia `B` quando:</span><span class="sxs-lookup"><span data-stu-id="89711-180">For purposes of interface mapping, a class member `A` matches an interface member `B` when:</span></span>
> 
> - <span data-ttu-id="89711-181">`A`e `B` sono metodi e gli elenchi di parametri di nome e formale di `A` e `B` sono identici e il tipo restituito di `A` è convertibile nel tipo restituito `B` tramite un'identità di conversione implicita dei riferimenti al tipo restituito di `B` .</span><span class="sxs-lookup"><span data-stu-id="89711-181">`A` and `B` are methods, and the name and formal parameter lists of `A` and `B` are identical, and the return type of `A` is convertible to the return type of `B` via an identity of implicit reference convertion to the return type of `B`.</span></span>
> - <span data-ttu-id="89711-182">`A`e `B` sono proprietà, il nome di `A` e `B` sono identici, `A` ha le stesse funzioni di accesso di `B` ( `A` è consentito disporre di funzioni di accesso aggiuntive se non si tratta di un'implementazione esplicita di un membro di interfaccia) e il tipo di `A` è convertibile nel tipo restituito `B` tramite una conversione di identità o, se `A` è una proprietà ReadOnly, una conversione di un riferimento implicito.</span><span class="sxs-lookup"><span data-stu-id="89711-182">`A` and `B` are properties, the name of `A` and `B` are identical, `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation), and the type of `A` is convertible to the return type of `B` via an identity conversion or, if `A` is a readonly property, an implicit reference conversion.</span></span>
> - <span data-ttu-id="89711-183">`A`e `B` sono eventi e il nome e il tipo di `A` e `B` sono identici.</span><span class="sxs-lookup"><span data-stu-id="89711-183">`A` and `B` are events, and the name and type of `A` and `B` are identical.</span></span>
> - <span data-ttu-id="89711-184">`A`e `B` sono indicizzatori, gli elenchi di parametri formali di `A` e `B` sono identici, `A` hanno le stesse funzioni di accesso di `B` ( `A` possono avere funzioni di accesso aggiuntive se non si tratta di un'implementazione esplicita di un membro di interfaccia) e il tipo di `A` è convertibile nel tipo restituito `B` tramite una conversione di identità o, se `A` è un indicizzatore di sola lettura, una conversione implicita di riferimenti.</span><span class="sxs-lookup"><span data-stu-id="89711-184">`A` and `B` are indexers, the formal parameter lists of `A` and `B` are identical, `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation), and the type of `A` is convertible to the return type of `B` via an identity conversion or, if `A` is a readonly indexer, an implicit reference conversion.</span></span>

<span data-ttu-id="89711-185">Si tratta tecnicamente di una modifica di rilievo, perché il programma seguente stampa "C1. M "oggi, ma stampa" C2 ". M "nella revisione proposta.</span><span class="sxs-lookup"><span data-stu-id="89711-185">This is technically a breaking change, as the program below prints "C1.M" today, but would print "C2.M" under the proposed revision.</span></span>

``` c#
using System;

interface I1 { object M(); }
class C1 : I1 { public object M() { return "C1.M"; } }
class C2 : C1, I1 { public new string M() { return "C2.M"; } }
class Program
{
    static void Main()
    {
        I1 i = new C2();
        Console.WriteLine(i.M());
    }
}
```

<span data-ttu-id="89711-186">A causa di questa modifica sostanziale, si potrebbe considerare di non supportare tipi restituiti covarianti nelle implementazioni implicite.</span><span class="sxs-lookup"><span data-stu-id="89711-186">Due to this breaking change, we might consider not supporting covariant return types on implicit implementations.</span></span>

### <a name="constraints-on-interface-implementation"></a><span data-ttu-id="89711-187">Vincoli sull'implementazione dell'interfaccia</span><span class="sxs-lookup"><span data-stu-id="89711-187">Constraints on Interface Implementation</span></span>

<span data-ttu-id="89711-188">**È necessaria una regola che un'implementazione esplicita dell'interfaccia debba dichiarare un tipo restituito non meno derivato rispetto al tipo restituito dichiarato in qualsiasi override nelle relative interfacce di base.**</span><span class="sxs-lookup"><span data-stu-id="89711-188">**We will need a rule that an explicit interface implementation must declare a return type no less derived than the return type declared in any override in its base interfaces.**</span></span>

### <a name="api-compatibility-implications"></a><span data-ttu-id="89711-189">Implicazioni per la compatibilità delle API</span><span class="sxs-lookup"><span data-stu-id="89711-189">API Compatibility Implications</span></span>

<span data-ttu-id="89711-190">*TBD*</span><span class="sxs-lookup"><span data-stu-id="89711-190">*TBD*</span></span>

### <a name="open-issues"></a><span data-ttu-id="89711-191">Problemi non risolti</span><span class="sxs-lookup"><span data-stu-id="89711-191">Open Issues</span></span>

<span data-ttu-id="89711-192">La specifica non indica in che modo il chiamante ottiene il tipo restituito più raffinato.</span><span class="sxs-lookup"><span data-stu-id="89711-192">The specification does not say how the caller gets the more refined return type.</span></span> <span data-ttu-id="89711-193">Presumibilmente che verrebbe eseguita in modo analogo al modo in cui i chiamanti ottengono le specifiche dei parametri dell'override più derivato.</span><span class="sxs-lookup"><span data-stu-id="89711-193">Presumably that would be done in a way similar to the way that callers get the most derived override's parameter specifications.</span></span>

--------------

<span data-ttu-id="89711-194">Se sono disponibili le interfacce seguenti:</span><span class="sxs-lookup"><span data-stu-id="89711-194">If we have the following interfaces:</span></span>

```csharp
interface I1 { I1 M(); }
interface I2 { I2 M(); }
interface I3: I1, I2 { override I3 M(); }
```

<span data-ttu-id="89711-195">Si noti che in `I3` i metodi `I1.M()` e `I2.M()` sono stati "Uniti".</span><span class="sxs-lookup"><span data-stu-id="89711-195">Note that in `I3`, the methods `I1.M()` and `I2.M()` have been “merged”.</span></span>  <span data-ttu-id="89711-196">Quando `I3` si implementa, è necessario implementarli insieme.</span><span class="sxs-lookup"><span data-stu-id="89711-196">When implementing `I3`, it is necessary to implement them both together.</span></span>

<span data-ttu-id="89711-197">In genere, è necessaria un'implementazione esplicita per fare riferimento al metodo originale.</span><span class="sxs-lookup"><span data-stu-id="89711-197">Generally, we require an explicit implementation to refer to the original method.</span></span>  <span data-ttu-id="89711-198">La domanda è, in una classe</span><span class="sxs-lookup"><span data-stu-id="89711-198">The question is, in a class</span></span>

```csharp
class C : I1, I2, I3
{
    C IN.M();
}
```

<span data-ttu-id="89711-199">Che cosa significa?</span><span class="sxs-lookup"><span data-stu-id="89711-199">What does that mean here?</span></span>  <span data-ttu-id="89711-200">Che cosa *dovrebbe essere* ?</span><span class="sxs-lookup"><span data-stu-id="89711-200">What should *N* be?</span></span>

<span data-ttu-id="89711-201">Suggerisco di consentire l'implementazione `I1.M` `I2.M` di o (ma non entrambi) e considerarlo come un'implementazione di entrambi.</span><span class="sxs-lookup"><span data-stu-id="89711-201">I suggest that we permit implementing either `I1.M` or `I2.M` (but not both), and treat that as an implementation of both.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="89711-202">Svantaggi</span><span class="sxs-lookup"><span data-stu-id="89711-202">Drawbacks</span></span>
[drawbacks]: #drawbacks

- <span data-ttu-id="89711-203">[] Ogni modifica della lingua deve corrispondere a se stessa.</span><span class="sxs-lookup"><span data-stu-id="89711-203">[ ] Every language change must pay for itself.</span></span>
- <span data-ttu-id="89711-204">[] È necessario assicurarsi che le prestazioni siano ragionevoli, anche in caso di gerarchie di ereditarietà approfondita</span><span class="sxs-lookup"><span data-stu-id="89711-204">[ ] We should ensure that the performance is reasonable, even in the case of deep inheritance hierarchies</span></span>
- <span data-ttu-id="89711-205">[] È necessario assicurarsi che gli artefatti della strategia di traduzione non influiscano sulla semantica del linguaggio, anche quando si utilizza IL nuovo IL da compilatori obsoleti.</span><span class="sxs-lookup"><span data-stu-id="89711-205">[ ] We should ensure that artifacts of the translation strategy do not affect language semantics, even when consuming new IL from old compilers.</span></span>

## <a name="alternatives"></a><span data-ttu-id="89711-206">Alternativi</span><span class="sxs-lookup"><span data-stu-id="89711-206">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="89711-207">È possibile ridurre leggermente le regole del linguaggio per consentire, in origine,</span><span class="sxs-lookup"><span data-stu-id="89711-207">We could relax the language rules slightly to allow, in source,</span></span>

```csharp
abstract class Cloneable
{
    public abstract Cloneable Clone();
}

class Digit : Cloneable
{
    public override Cloneable Clone()
    {
        return this.Clone();
    }

    public new Digit Clone() // Error: 'Digit' already defines a member called 'Clone' with the same parameter types
    {
        return this;
    }
}
```

## <a name="unresolved-questions"></a><span data-ttu-id="89711-208">Domande non risolte</span><span class="sxs-lookup"><span data-stu-id="89711-208">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="89711-209">[] In che modo le API compilate per utilizzare questa funzionalità funzionano in versioni precedenti del linguaggio?</span><span class="sxs-lookup"><span data-stu-id="89711-209">[ ] How will APIs that have been compiled to use this feature work in older versions of the language?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="89711-210">Riunioni di progettazione</span><span class="sxs-lookup"><span data-stu-id="89711-210">Design meetings</span></span>

- <span data-ttu-id="89711-211">una discussione all'indirizzo <https://github.com/dotnet/roslyn/issues/357> .</span><span class="sxs-lookup"><span data-stu-id="89711-211">some discussion at <https://github.com/dotnet/roslyn/issues/357>.</span></span>
- https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-01-08.md
- <span data-ttu-id="89711-212">Discussione offline verso la decisione di supportare l'override dei metodi della classe solo in C# 9,0.</span><span class="sxs-lookup"><span data-stu-id="89711-212">Offline discussion toward a decision to support overriding of class methods only in C# 9.0.</span></span>

