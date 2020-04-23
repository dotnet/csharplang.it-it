---
ms.openlocfilehash: 04dca01bad04d5c53aa1c7c876343fb7ef33d2fa
ms.sourcegitcommit: 5c7cc619214ade6a8f3a0caddfb4862635f5241d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82021930"
---
<a name="extending-partial-methods"></a><span data-ttu-id="fb363-101">Estensione di metodi parzialiExtending Partial Methods</span><span class="sxs-lookup"><span data-stu-id="fb363-101">Extending Partial Methods</span></span>
=====

## <a name="summary"></a><span data-ttu-id="fb363-102">Summary</span><span class="sxs-lookup"><span data-stu-id="fb363-102">Summary</span></span>
<span data-ttu-id="fb363-103">Questa proposta ha lo scopo di `partial` rimuovere tutte le restrizioni relative alle firme dei metodi in C.</span><span class="sxs-lookup"><span data-stu-id="fb363-103">This proposal aims to remove all restrictions around the signatures of `partial` methods in C#.</span></span> <span data-ttu-id="fb363-104">L'obiettivo è quello di espandere il set di scenari in cui questi metodi possono utilizzare i generatori di origine, nonché essere un formato di dichiarazione più generale per i metodi di C.</span><span class="sxs-lookup"><span data-stu-id="fb363-104">The goal being to expand the set of scenarios in which these methods can work with source generators as well as being a more general declaration form for C# methods.</span></span>

## <a name="motivation"></a><span data-ttu-id="fb363-105">Motivazione</span><span class="sxs-lookup"><span data-stu-id="fb363-105">Motivation</span></span>
<span data-ttu-id="fb363-106">Il supporto limitato per gli sviluppatori che suddivide i metodi in dichiarazioni e definizioni/implementazioni.</span><span class="sxs-lookup"><span data-stu-id="fb363-106">C# has limited support for developers splitting methods into declarations and definitions / implementations.</span></span> 

```cs 
partial class C
{
    // The declaration of C.M
    partial void M(string message);
}

partial class C
{
    // The definition of C.M
    partial void M(string message) => Console.WriteLine(message);
}
```

<span data-ttu-id="fb363-107">Un comportamento `partial` dei metodi è che quando la definizione è assente, il linguaggio cancellerà semplicemente tutte le chiamate al `partial` metodo.</span><span class="sxs-lookup"><span data-stu-id="fb363-107">One behavior of `partial` methods is that when the definition is absent then the language will simply erase any calls to the `partial` method.</span></span> <span data-ttu-id="fb363-108">Essenzialmente si comporta come una `[Conditional]` chiamata a un metodo in cui la condizione è stata valutata su false.</span><span class="sxs-lookup"><span data-stu-id="fb363-108">Essentially it behaves like a call to a `[Conditional]` method where the condition was evaluated to false.</span></span> 

```cs
partial class D
{
    partial void M(string message);

    void Example()
    {
        M(GetIt()); // Call to M and GetIt erased at compile time
    }

    string GetIt() => "Hello World";
}
```

<span data-ttu-id="fb363-109">La motivazione originale per questa funzionalità è stata la generazione di origine sotto forma di codice generato dalla finestra di progettazione.</span><span class="sxs-lookup"><span data-stu-id="fb363-109">The original motivation for this feature was source generation in the form of designer generated code.</span></span> <span data-ttu-id="fb363-110">Gli utenti modificavano costantemente il codice generato perché desideravano associare alcuni aspetti del codice generato.</span><span class="sxs-lookup"><span data-stu-id="fb363-110">Users were constantly editing the generated code because they wanted to hook some aspect of the generated code.</span></span> <span data-ttu-id="fb363-111">In particolare parti del processo di avvio di Windows Form, dopo l'inizializzazione dei componenti.</span><span class="sxs-lookup"><span data-stu-id="fb363-111">Most notably parts of the Windows Forms startup process, after components were initialized.</span></span>

<span data-ttu-id="fb363-112">La modifica del codice generato era soggetta a errori perché qualsiasi azione che ha causato la rigenerazione del codice da parte della finestra di progettazione causerebbe la cancellazione della modifica da parte dell'utente.</span><span class="sxs-lookup"><span data-stu-id="fb363-112">Editing the generated code was error prone because any action which caused the designer to regenerate the code would cause the user edit to be erased.</span></span> <span data-ttu-id="fb363-113">La `partial` funzionalità del metodo ha allentato questa tensione perché ha `partial` permesso ai progettisti di emettere hook sotto forma di metodi.</span><span class="sxs-lookup"><span data-stu-id="fb363-113">The `partial` method feature eased this tension because it allowed designers to emit hooks in the form of `partial` methods.</span></span> 

<span data-ttu-id="fb363-114">I progettisti potevano emettere hook come `partial void OnComponentInit()` e gli sviluppatori potevano definire le dichiarazioni per loro o non definirli.</span><span class="sxs-lookup"><span data-stu-id="fb363-114">Designers could emit hooks like `partial void OnComponentInit()` and developers could define declarations for them or not define them.</span></span> <span data-ttu-id="fb363-115">In entrambi i casi, anche se il codice generato verrebbe compilato e gli sviluppatori che erano interessati al processo potrebbero eseguire l'hook in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="fb363-115">In either case though the generated code would compile and developers who were interested in the process could hook in as needed.</span></span> 

<span data-ttu-id="fb363-116">Ciò significa che i metodi parziali hanno diverse restrizioni:This does that partial methods have several restrictions:</span><span class="sxs-lookup"><span data-stu-id="fb363-116">This does mean that partial methods have several restrictions:</span></span>

1. <span data-ttu-id="fb363-117">Deve avere `void` un tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="fb363-117">Must have a `void` return type.</span></span>
1. <span data-ttu-id="fb363-118">Non `ref` può `out` avere o parametri.</span><span class="sxs-lookup"><span data-stu-id="fb363-118">Cannot have `ref` or `out` parameters.</span></span> 
1. <span data-ttu-id="fb363-119">Non può avere accessibilità `private`(implicitamente).</span><span class="sxs-lookup"><span data-stu-id="fb363-119">Cannot have any accessibility (implicitly `private`).</span></span>

<span data-ttu-id="fb363-120">Queste restrizioni esistono perché la lingua deve essere in grado di generare codice quando il sito di chiamata viene cancellato.</span><span class="sxs-lookup"><span data-stu-id="fb363-120">These restrictions exist because the language must be able to emit code when the call site is erased.</span></span> <span data-ttu-id="fb363-121">Dato che possono essere `private` cancellati è l'unica accessibilità possibile perché il membro non può essere esposto nei metadati dell'assembly.</span><span class="sxs-lookup"><span data-stu-id="fb363-121">Given they can be erased `private` is the only possible accessibility because the member can't be exposed in assembly metadata.</span></span> <span data-ttu-id="fb363-122">Queste restrizioni servono anche a limitare `partial` il set di scenari in cui i metodi possono essere applicati.</span><span class="sxs-lookup"><span data-stu-id="fb363-122">These restrictions also serve to limit the set of scenarios in which `partial` methods can be applied.</span></span>

<span data-ttu-id="fb363-123">La proposta è quella di eliminare tutte `partial` le restrizioni esistenti sui metodi.</span><span class="sxs-lookup"><span data-stu-id="fb363-123">The proposal here is to remove all of the existing restrictions around `partial` methods.</span></span> <span data-ttu-id="fb363-124">Essenzialmente lasciare `out`che abbiano , tipi restituiti non void o qualsiasi tipo di accessibilità.</span><span class="sxs-lookup"><span data-stu-id="fb363-124">Essentially let them have `out`, non-void return types or any type of accessibility.</span></span> <span data-ttu-id="fb363-125">Tali `partial` dichiarazioni avrebbero quindi il requisito aggiuntivo che una definizione deve esistere.</span><span class="sxs-lookup"><span data-stu-id="fb363-125">Such `partial` declarations would then have the added requirement that a definition must exist.</span></span> <span data-ttu-id="fb363-126">Ciò significa che la lingua non deve considerare l'impatto della cancellazione dei siti di chiamata.</span><span class="sxs-lookup"><span data-stu-id="fb363-126">That means the language does not have to consider the impact of erasing the call sites.</span></span> 

<span data-ttu-id="fb363-127">Questo espanderebbe l'insieme `partial` di scenari di generatori a cui i metodi potrebbero partecipare e quindi collegarsi bene con la nostra funzionalità dei generatori di origine.</span><span class="sxs-lookup"><span data-stu-id="fb363-127">This would expand the set of generator scenarios that `partial` methods could participate in and hence link in nicely with our source generators feature.</span></span> <span data-ttu-id="fb363-128">Ad esempio, un'espressione regolare può essere definita usando il modello seguente:For example a regex could be defined using the following pattern:</span><span class="sxs-lookup"><span data-stu-id="fb363-128">For example a regex could be defined using the following pattern:</span></span>

```cs
[RegexGenerated("(dog|cat|fish)")]
partial bool IsPetMatch(string input);
```

<span data-ttu-id="fb363-129">Questo dà sia lo sviluppatore un semplice modo dichiarativo di opting in generatori, nonché dando generatori un set molto semplice di dichiarazioni per guardare attraverso nel codice sorgente per guidare il loro output generato.</span><span class="sxs-lookup"><span data-stu-id="fb363-129">This gives both the developer a simple declarative way of opting into generators as well as giving generators a very easy set of declarations to look through in the source code to drive their generated output.</span></span> 

<span data-ttu-id="fb363-130">Confrontare che con la difficoltà che un generatore avrebbe agganciando il seguente frammento di codice.</span><span class="sxs-lookup"><span data-stu-id="fb363-130">Compare that with the difficulty that a generator would have hooking up the following snippet of code.</span></span> 

```cs
var regex = new RegularExpression("(dog|cat|fish)");
if (regex.IsMatch(someInput))
{

}
```

<span data-ttu-id="fb363-131">Dato che il compilatore non consente ai generatori di modificare il codice che collega questo modello sarebbe praticamente impossibile per i generatori.</span><span class="sxs-lookup"><span data-stu-id="fb363-131">Given that the compiler doesn't allow generators to modify code hooking up this pattern would be pretty much impossible for generators.</span></span> <span data-ttu-id="fb363-132">Avrebbero bisogno di ricorrere `IsMatch` alla reflection nell'implementazione o chiedere agli utenti di modificare i loro siti di chiamata a un nuovo metodo - effettuare il refactoring dell'espressione regolare per passare il valore letterale stringa come argomento.</span><span class="sxs-lookup"><span data-stu-id="fb363-132">They would need to resort to reflection in the `IsMatch` implementation, or asking users to change their call sites to a new method + refactor the regex to pass the string literal as an argument.</span></span> <span data-ttu-id="fb363-133">È piuttosto disordinato.</span><span class="sxs-lookup"><span data-stu-id="fb363-133">It's pretty messy.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="fb363-134">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="fb363-134">Detailed Design</span></span>
<span data-ttu-id="fb363-135">Il linguaggio cambierà per consentire `partial` agli metodi di essere annotati con un modificatore di accessibilità esplicito.</span><span class="sxs-lookup"><span data-stu-id="fb363-135">The language will change to allow `partial` methods to be annotated with an explicit accessibility modifier.</span></span> <span data-ttu-id="fb363-136">Ciò significa che possono `private` `public`essere etichettati come , , ecc ...</span><span class="sxs-lookup"><span data-stu-id="fb363-136">This means they can be labeled as `private`, `public`, etc ...</span></span> 

<span data-ttu-id="fb363-137">Quando `partial` un metodo dispone di un modificatore di accessibilità esplicito, anche `private`se il linguaggio richiede che la dichiarazione abbia una definizione corrispondente anche quando l'accessibilità è :</span><span class="sxs-lookup"><span data-stu-id="fb363-137">When a `partial` method has an explicit accessibility modifier though the language will require that the declaration has a matching definition even when the accessibility is `private`:</span></span>

```cs
partial class C
{
    // Okay because no definition is required here
    partial void M1();

    // Okay because M2 has a definition
    private partial void M2();

    // Error: partial method M3 must have a definition
    private partial void M3();
}

partial class C
{
    private partial void M2() { }
}
```

<span data-ttu-id="fb363-138">Inoltre il linguaggio rimuoverà tutte le `partial` restrizioni su ciò che può apparire su un metodo che ha un'accessibilità esplicita.</span><span class="sxs-lookup"><span data-stu-id="fb363-138">Further the language will remove all restrictions on what can appear on a `partial` method which has an explicit accessibility.</span></span> <span data-ttu-id="fb363-139">Tali dichiarazioni possono contenere tipi `ref` `out` restituiti `extern` non void, o parametri, modificatori e così via. Queste firme avranno l'espressività completa del linguaggio C.</span><span class="sxs-lookup"><span data-stu-id="fb363-139">Such declarations can contain non-void return types, `ref` or `out` parameters, `extern` modifier, etc ... These signatures will have the full expressivity of the C# language.</span></span>

```cs
partial class D
{
    // Okay
    internal partial bool TryParse(string s, out int i); 
}

partial class D
{
    internal partial bool TryParse(string s, out int i) { }
}
```

<span data-ttu-id="fb363-140">Ciò consente `partial` in modo `overrides` `interface` esplicito ai metodi di partecipare e alle implementazioni:This explicitly allows for methods to participate to e implementations:</span><span class="sxs-lookup"><span data-stu-id="fb363-140">This explicitly allows for `partial` methods to participate in `overrides` and `interface` implementations:</span></span>

```cs
interface IStudent
{
    string GetName();
}

partial class C : IStudent
{
    public virtual partial string GetName(); 
}

partial class C
{
    public virtual partial string GetName() => "Jarde";
}
```

<span data-ttu-id="fb363-141">Il compilatore modificherà l'errore `partial` generato quando un metodo contiene un elemento non valido per dire essenzialmente:</span><span class="sxs-lookup"><span data-stu-id="fb363-141">The compiler will change the error it emits when a `partial` method contains an illegal element to essentially say:</span></span>

> <span data-ttu-id="fb363-142">Impossibile `ref` utilizzare `partial` un metodo privo di accessibilità esplicita</span><span class="sxs-lookup"><span data-stu-id="fb363-142">Cannot use `ref` on a `partial` method that lacks explicit accessibility</span></span> 

<span data-ttu-id="fb363-143">Questo aiuterà gli sviluppatori a indirizzare gli sviluppatori nella giusta direzione quando si utilizza questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="fb363-143">This will help point developers in the right direction when using this feature.</span></span>

<span data-ttu-id="fb363-144">Restrizioni:</span><span class="sxs-lookup"><span data-stu-id="fb363-144">Restrictions:</span></span>
- <span data-ttu-id="fb363-145">`partial`le dichiarazioni con accessibilità esplicita devono avere una definizione</span><span class="sxs-lookup"><span data-stu-id="fb363-145">`partial` declarations with explicit accessibility must have a definition</span></span>
- <span data-ttu-id="fb363-146">`partial`Le dichiarazioni e le firme di definizione devono corrispondere a tutti i modificatori di metodo e parametro.</span><span class="sxs-lookup"><span data-stu-id="fb363-146">`partial` declarations and definition signatures must match on all method and parameter modifiers.</span></span> <span data-ttu-id="fb363-147">Gli unici aspetti che possono differire sono i nomi dei parametri `partial` e gli elenchi di attributi (questo non è nuovo, ma piuttosto un requisito esistente dei metodi).</span><span class="sxs-lookup"><span data-stu-id="fb363-147">The only aspects which can differ are parameter names and attribute lists (this is not new but rather an existing requirement of `partial` methods).</span></span>

## <a name="questions"></a><span data-ttu-id="fb363-148">Domande</span><span class="sxs-lookup"><span data-stu-id="fb363-148">Questions</span></span>

### <a name="partial-on-all-members"></a><span data-ttu-id="fb363-149">parziale su tutti i membri</span><span class="sxs-lookup"><span data-stu-id="fb363-149">partial on all members</span></span>
<span data-ttu-id="fb363-150">Dato che ci stiamo `partial` espandendo per essere più amichevoli ai generatori di origine dovremmo anche espanderlo per lavorare su tutti i membri della classe?</span><span class="sxs-lookup"><span data-stu-id="fb363-150">Given that we're expanding `partial` to be more friendly to source generators should we also expand it to work on all class members?</span></span> <span data-ttu-id="fb363-151">Per esempio dovremmo essere `partial` in grado di dichiarare costruttori, operatori, ecc ...</span><span class="sxs-lookup"><span data-stu-id="fb363-151">For example should we be able to declare `partial` constructors, operators, etc ...</span></span>

<span data-ttu-id="fb363-152">**Risoluzione** L'idea è valida, ma a questo punto della pianificazione di C è 9 stiamo cercando di evitare inutili funzionalità creep.</span><span class="sxs-lookup"><span data-stu-id="fb363-152">**Resolution** The idea is sound but at this point in the C# 9 schedule we're trying to avoid unnecessary feature creep.</span></span> <span data-ttu-id="fb363-153">Vuoi risolvere il problema immediato di espandere la funzione per lavorare con i generatori di origine moderni.</span><span class="sxs-lookup"><span data-stu-id="fb363-153">Want to solve the immediate problem of expanding the feature to work with modern source generators.</span></span> 

<span data-ttu-id="fb363-154">L'estensione `partial` per supportare altri membri verrà considerata per la versione di C .</span><span class="sxs-lookup"><span data-stu-id="fb363-154">Extending `partial` to support other members will be considered for the C# 10 release.</span></span> <span data-ttu-id="fb363-155">Sembra probabile che prenderemo in considerazione questa estensione.</span><span class="sxs-lookup"><span data-stu-id="fb363-155">Seems likely that we will consider this extension.</span></span>

### <a name="use-abstract-instead-of-partial"></a><span data-ttu-id="fb363-156">Utilizzare astratto anziché parziale</span><span class="sxs-lookup"><span data-stu-id="fb363-156">Use abstract instead of partial</span></span>
<span data-ttu-id="fb363-157">Il nocciolo di questa proposta garantisce essenzialmente che una dichiarazione abbia una definizione/attuazione corrispondente.</span><span class="sxs-lookup"><span data-stu-id="fb363-157">The crux of this proposal is essentially ensuring that a declaration has a corresponding definition / implementation.</span></span> <span data-ttu-id="fb363-158">Dato che dovremmo `abstract` usare dal momento che è già una parola chiave del linguaggio che costringe lo sviluppatore a pensare di avere un'implementazione?</span><span class="sxs-lookup"><span data-stu-id="fb363-158">Given that should we use `abstract` since it's already a language keyword that forces the developer to think about having an implementation?</span></span>

<span data-ttu-id="fb363-159">**Risoluzione** C'è stata una sana discussione su questo, ma alla fine è stato deciso contro.</span><span class="sxs-lookup"><span data-stu-id="fb363-159">**Resolution** There was a healthy discussion about this but eventually it was decided against.</span></span>
<span data-ttu-id="fb363-160">Sì, i requisiti sono familiari, ma i concetti sono significativamente diversi.</span><span class="sxs-lookup"><span data-stu-id="fb363-160">Yes the requirements are familiar but the concepts are significantly different.</span></span>
<span data-ttu-id="fb363-161">Potrebbe facilmente portare lo sviluppatore a credere che stavano creando slot virtuali quando non lo stavano facendo.</span><span class="sxs-lookup"><span data-stu-id="fb363-161">Could easily lead the developer to believe they were creating virtual slots when they were not doing so.</span></span>
