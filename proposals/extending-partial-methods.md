---
ms.openlocfilehash: 6fbc866af9971d86a287b026013e235e5b25fc21
ms.sourcegitcommit: ab0873759f86d44adfc5daefb18cb922df8adb8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82162074"
---
<a name="extending-partial-methods"></a><span data-ttu-id="041eb-101">Estensione di metodi parziali</span><span class="sxs-lookup"><span data-stu-id="041eb-101">Extending Partial Methods</span></span>
=====

## <a name="summary"></a><span data-ttu-id="041eb-102">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="041eb-102">Summary</span></span>
<span data-ttu-id="041eb-103">Questa proposta mira a rimuovere tutte le restrizioni relative alle firme `partial` dei metodi in C#.</span><span class="sxs-lookup"><span data-stu-id="041eb-103">This proposal aims to remove all restrictions around the signatures of `partial` methods in C#.</span></span> <span data-ttu-id="041eb-104">L'obiettivo è quello di espandere il set di scenari in cui questi metodi possono funzionare con i generatori di origine, nonché un modulo di dichiarazione più generale per i metodi C#.</span><span class="sxs-lookup"><span data-stu-id="041eb-104">The goal being to expand the set of scenarios in which these methods can work with source generators as well as being a more general declaration form for C# methods.</span></span>

<span data-ttu-id="041eb-105">Vedere anche la [specifica dei metodi parziali originali](/spec/classes.md#partial-methods).</span><span class="sxs-lookup"><span data-stu-id="041eb-105">See also the [original partial methods specification](/spec/classes.md#partial-methods).</span></span>

## <a name="motivation"></a><span data-ttu-id="041eb-106">Motivazione</span><span class="sxs-lookup"><span data-stu-id="041eb-106">Motivation</span></span>
<span data-ttu-id="041eb-107">C# offre un supporto limitato per gli sviluppatori che dividono metodi in dichiarazioni e definizioni/implementazioni.</span><span class="sxs-lookup"><span data-stu-id="041eb-107">C# has limited support for developers splitting methods into declarations and definitions / implementations.</span></span> 

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

<span data-ttu-id="041eb-108">Un comportamento dei `partial` metodi è che, quando la definizione è assente, il linguaggio cancellerà semplicemente tutte le `partial` chiamate al metodo.</span><span class="sxs-lookup"><span data-stu-id="041eb-108">One behavior of `partial` methods is that when the definition is absent then the language will simply erase any calls to the `partial` method.</span></span> <span data-ttu-id="041eb-109">Sostanzialmente si comporta come una chiamata a un `[Conditional]` metodo in cui la condizione è stata valutata come false.</span><span class="sxs-lookup"><span data-stu-id="041eb-109">Essentially it behaves like a call to a `[Conditional]` method where the condition was evaluated to false.</span></span> 

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

<span data-ttu-id="041eb-110">La motivazione originale per questa caratteristica è la generazione di origini sotto forma di codice generato dalla finestra di progettazione.</span><span class="sxs-lookup"><span data-stu-id="041eb-110">The original motivation for this feature was source generation in the form of designer generated code.</span></span> <span data-ttu-id="041eb-111">Gli utenti modificavano costantemente il codice generato perché volevano associare un aspetto del codice generato.</span><span class="sxs-lookup"><span data-stu-id="041eb-111">Users were constantly editing the generated code because they wanted to hook some aspect of the generated code.</span></span> <span data-ttu-id="041eb-112">In particolare parti del processo di avvio Windows Forms, dopo l'inizializzazione dei componenti.</span><span class="sxs-lookup"><span data-stu-id="041eb-112">Most notably parts of the Windows Forms startup process, after components were initialized.</span></span>

<span data-ttu-id="041eb-113">La modifica del codice generato è soggetta a errori poiché qualsiasi azione che ha causato la rigenerazione del codice da parte della finestra di progettazione comporterebbe la cancellazione della modifica dell'utente.</span><span class="sxs-lookup"><span data-stu-id="041eb-113">Editing the generated code was error prone because any action which caused the designer to regenerate the code would cause the user edit to be erased.</span></span> <span data-ttu-id="041eb-114">La `partial` funzionalità del metodo ha semplificato questa tensione perché ha consentito ai progettisti di creare hook `partial` sotto forma di metodi.</span><span class="sxs-lookup"><span data-stu-id="041eb-114">The `partial` method feature eased this tension because it allowed designers to emit hooks in the form of `partial` methods.</span></span> 

<span data-ttu-id="041eb-115">I progettisti possono creare hook `partial void OnComponentInit()` come e gli sviluppatori possono definire dichiarazioni per loro o non definirli.</span><span class="sxs-lookup"><span data-stu-id="041eb-115">Designers could emit hooks like `partial void OnComponentInit()` and developers could define declarations for them or not define them.</span></span> <span data-ttu-id="041eb-116">In entrambi i casi, anche se il codice generato viene compilato e gli sviluppatori interessati al processo potrebbero collegarsi in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="041eb-116">In either case though the generated code would compile and developers who were interested in the process could hook in as needed.</span></span> 

<span data-ttu-id="041eb-117">Ciò significa che i metodi parziali presentano alcune restrizioni:</span><span class="sxs-lookup"><span data-stu-id="041eb-117">This does mean that partial methods have several restrictions:</span></span>

1. <span data-ttu-id="041eb-118">Deve avere un `void` tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="041eb-118">Must have a `void` return type.</span></span>
1. <span data-ttu-id="041eb-119">Non possono `out` avere parametri.</span><span class="sxs-lookup"><span data-stu-id="041eb-119">Cannot have `out` parameters.</span></span> 
1. <span data-ttu-id="041eb-120">Non può avere alcuna accessibilità ( `private`in modo implicito).</span><span class="sxs-lookup"><span data-stu-id="041eb-120">Cannot have any accessibility (implicitly `private`).</span></span>

<span data-ttu-id="041eb-121">Queste restrizioni sono dovute al fatto che la lingua deve essere in grado di emettere codice quando viene cancellato il sito di chiamata.</span><span class="sxs-lookup"><span data-stu-id="041eb-121">These restrictions exist because the language must be able to emit code when the call site is erased.</span></span> <span data-ttu-id="041eb-122">Dato che possono essere cancellati `private` , l'unica accessibilità possibile è che il membro non può essere esposto nei metadati dell'assembly.</span><span class="sxs-lookup"><span data-stu-id="041eb-122">Given they can be erased `private` is the only possible accessibility because the member can't be exposed in assembly metadata.</span></span> <span data-ttu-id="041eb-123">Queste restrizioni servono anche per limitare il set di scenari in cui `partial` è possibile applicare i metodi.</span><span class="sxs-lookup"><span data-stu-id="041eb-123">These restrictions also serve to limit the set of scenarios in which `partial` methods can be applied.</span></span>

<span data-ttu-id="041eb-124">La proposta consiste nel rimuovere tutte le restrizioni esistenti intorno `partial` ai metodi.</span><span class="sxs-lookup"><span data-stu-id="041eb-124">The proposal here is to remove all of the existing restrictions around `partial` methods.</span></span> <span data-ttu-id="041eb-125">In sostanza, è `out`possibile consentire l'accesso ai tipi restituiti non void o a qualsiasi tipo di accessibilità.</span><span class="sxs-lookup"><span data-stu-id="041eb-125">Essentially let them have `out`, non-void return types or any type of accessibility.</span></span> <span data-ttu-id="041eb-126">Tali `partial` dichiarazioni avrebbero quindi il requisito aggiuntivo che deve esistere una definizione.</span><span class="sxs-lookup"><span data-stu-id="041eb-126">Such `partial` declarations would then have the added requirement that a definition must exist.</span></span> <span data-ttu-id="041eb-127">Questo significa che la lingua non deve considerare l'effetto della cancellazione dei siti di chiamata.</span><span class="sxs-lookup"><span data-stu-id="041eb-127">That means the language does not have to consider the impact of erasing the call sites.</span></span> 

<span data-ttu-id="041eb-128">In questo modo si espande il set di scenari `partial` di generazione che i metodi possono partecipare e quindi si collegano correttamente con la funzionalità generatori di origine.</span><span class="sxs-lookup"><span data-stu-id="041eb-128">This would expand the set of generator scenarios that `partial` methods could participate in and hence link in nicely with our source generators feature.</span></span> <span data-ttu-id="041eb-129">Ad esempio, è possibile definire un'espressione regolare usando il modello seguente:</span><span class="sxs-lookup"><span data-stu-id="041eb-129">For example a regex could be defined using the following pattern:</span></span>

```cs
[RegexGenerated("(dog|cat|fish)")]
partial bool IsPetMatch(string input);
```

<span data-ttu-id="041eb-130">In questo modo, allo sviluppatore viene assegnato un semplice metodo dichiarativo per optare per i generatori e fornire ai generatori un set molto semplice di dichiarazioni da esaminare nel codice sorgente per guidare l'output generato.</span><span class="sxs-lookup"><span data-stu-id="041eb-130">This gives both the developer a simple declarative way of opting into generators as well as giving generators a very easy set of declarations to look through in the source code to drive their generated output.</span></span> 

<span data-ttu-id="041eb-131">Si confronti con la difficoltà che un generatore avrebbe collegato al frammento di codice seguente.</span><span class="sxs-lookup"><span data-stu-id="041eb-131">Compare that with the difficulty that a generator would have hooking up the following snippet of code.</span></span> 

```cs
var regex = new RegularExpression("(dog|cat|fish)");
if (regex.IsMatch(someInput))
{

}
```

<span data-ttu-id="041eb-132">Dato che il compilatore non consente ai generatori di modificare il codice, l'associazione di questo modello potrebbe essere molto impossible per i generatori.</span><span class="sxs-lookup"><span data-stu-id="041eb-132">Given that the compiler doesn't allow generators to modify code hooking up this pattern would be pretty much impossible for generators.</span></span> <span data-ttu-id="041eb-133">Dovranno ricorrere alla reflection nell' `IsMatch` implementazione o chiedere agli utenti di modificare i siti di chiamata in un nuovo metodo e di eseguire il refactoring dell'espressione regolare per passare il valore letterale stringa come argomento.</span><span class="sxs-lookup"><span data-stu-id="041eb-133">They would need to resort to reflection in the `IsMatch` implementation, or asking users to change their call sites to a new method + refactor the regex to pass the string literal as an argument.</span></span> <span data-ttu-id="041eb-134">È piuttosto complicato.</span><span class="sxs-lookup"><span data-stu-id="041eb-134">It's pretty messy.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="041eb-135">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="041eb-135">Detailed Design</span></span>
<span data-ttu-id="041eb-136">Il linguaggio verrà modificato in modo `partial` da consentire l'annotazione dei metodi con un modificatore di accessibilità esplicito.</span><span class="sxs-lookup"><span data-stu-id="041eb-136">The language will change to allow `partial` methods to be annotated with an explicit accessibility modifier.</span></span> <span data-ttu-id="041eb-137">Ciò significa che possono essere etichettati come `private`, `public`e così via...</span><span class="sxs-lookup"><span data-stu-id="041eb-137">This means they can be labeled as `private`, `public`, etc ...</span></span> 

<span data-ttu-id="041eb-138">Quando un `partial` metodo dispone di un modificatore di accessibilità esplicita, anche se per il linguaggio è necessario che la dichiarazione includa una definizione corrispondente anche quando l'accessibilità è `private`:</span><span class="sxs-lookup"><span data-stu-id="041eb-138">When a `partial` method has an explicit accessibility modifier though the language will require that the declaration has a matching definition even when the accessibility is `private`:</span></span>

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

<span data-ttu-id="041eb-139">Oltre al linguaggio verranno rimosse tutte le restrizioni su ciò che può `partial` essere visualizzato in un metodo con un'accessibilità esplicita.</span><span class="sxs-lookup"><span data-stu-id="041eb-139">Further the language will remove all restrictions on what can appear on a `partial` method which has an explicit accessibility.</span></span> <span data-ttu-id="041eb-140">Tali dichiarazioni possono contenere tipi restituiti non void, `out` parametri, `extern` modificatori e così via. Queste firme avranno la massima espressività del linguaggio C#.</span><span class="sxs-lookup"><span data-stu-id="041eb-140">Such declarations can contain non-void return types, `out` parameters, `extern` modifier, etc ... These signatures will have the full expressivity of the C# language.</span></span>

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

<span data-ttu-id="041eb-141">Questo consente in `partial` `overrides` modo esplicito ai metodi di partecipare `interface` alle implementazioni di e:</span><span class="sxs-lookup"><span data-stu-id="041eb-141">This explicitly allows for `partial` methods to participate in `overrides` and `interface` implementations:</span></span>

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

<span data-ttu-id="041eb-142">Il compilatore modificherà l'errore emesso quando un `partial` metodo contiene un elemento non valido per indicare essenzialmente:</span><span class="sxs-lookup"><span data-stu-id="041eb-142">The compiler will change the error it emits when a `partial` method contains an illegal element to essentially say:</span></span>

> <span data-ttu-id="041eb-143">Impossibile utilizzare `ref` su un `partial` metodo privo di accessibilità esplicita</span><span class="sxs-lookup"><span data-stu-id="041eb-143">Cannot use `ref` on a `partial` method that lacks explicit accessibility</span></span> 

<span data-ttu-id="041eb-144">Ciò consentirà agli sviluppatori di puntare alla direzione corretta quando si usa questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="041eb-144">This will help point developers in the right direction when using this feature.</span></span>

<span data-ttu-id="041eb-145">Restrizioni:</span><span class="sxs-lookup"><span data-stu-id="041eb-145">Restrictions:</span></span>
- <span data-ttu-id="041eb-146">`partial`le dichiarazioni con accessibilità esplicita devono avere una definizione</span><span class="sxs-lookup"><span data-stu-id="041eb-146">`partial` declarations with explicit accessibility must have a definition</span></span>
- <span data-ttu-id="041eb-147">`partial`le dichiarazioni e le firme delle definizioni devono corrispondere a tutti i modificatori di parametro e metodo.</span><span class="sxs-lookup"><span data-stu-id="041eb-147">`partial` declarations and definition signatures must match on all method and parameter modifiers.</span></span> <span data-ttu-id="041eb-148">Gli unici aspetti che possono variare sono i nomi di parametro e gli elenchi di attributi (questo non è un nuovo requisito `partial` , ma piuttosto un requisito esistente di metodi).</span><span class="sxs-lookup"><span data-stu-id="041eb-148">The only aspects which can differ are parameter names and attribute lists (this is not new but rather an existing requirement of `partial` methods).</span></span>

## <a name="questions"></a><span data-ttu-id="041eb-149">Domande</span><span class="sxs-lookup"><span data-stu-id="041eb-149">Questions</span></span>

### <a name="partial-on-all-members"></a><span data-ttu-id="041eb-150">parziale su tutti i membri</span><span class="sxs-lookup"><span data-stu-id="041eb-150">partial on all members</span></span>
<span data-ttu-id="041eb-151">Dato che la nostra espansione `partial` è più intuitiva per i generatori di origine, dobbiamo espanderla anche per lavorare su tutti i membri della classe?</span><span class="sxs-lookup"><span data-stu-id="041eb-151">Given that we're expanding `partial` to be more friendly to source generators should we also expand it to work on all class members?</span></span> <span data-ttu-id="041eb-152">Ad esempio, dovrebbe essere possibile dichiarare `partial` costruttori, operatori e così via...</span><span class="sxs-lookup"><span data-stu-id="041eb-152">For example should we be able to declare `partial` constructors, operators, etc ...</span></span>

<span data-ttu-id="041eb-153">**Risoluzione** dei problemi L'idea è valida, ma a questo punto nella pianificazione di C# 9 si sta tentando di evitare il creep della funzionalità non necessaria.</span><span class="sxs-lookup"><span data-stu-id="041eb-153">**Resolution** The idea is sound but at this point in the C# 9 schedule we're trying to avoid unnecessary feature creep.</span></span> <span data-ttu-id="041eb-154">Si vuole risolvere il problema immediato dell'espansione della funzionalità per lavorare con i generatori di origine moderni.</span><span class="sxs-lookup"><span data-stu-id="041eb-154">Want to solve the immediate problem of expanding the feature to work with modern source generators.</span></span> 

<span data-ttu-id="041eb-155">L' `partial` estensione per il supporto di altri membri verrà considerata per la versione di C# 10.</span><span class="sxs-lookup"><span data-stu-id="041eb-155">Extending `partial` to support other members will be considered for the C# 10 release.</span></span> <span data-ttu-id="041eb-156">Sembra probabile che questa estensione verrà considerata.</span><span class="sxs-lookup"><span data-stu-id="041eb-156">Seems likely that we will consider this extension.</span></span>

### <a name="use-abstract-instead-of-partial"></a><span data-ttu-id="041eb-157">USA abstract anziché partial</span><span class="sxs-lookup"><span data-stu-id="041eb-157">Use abstract instead of partial</span></span>
<span data-ttu-id="041eb-158">Il punto cruciale di questa proposta è essenzialmente garantire che una dichiarazione disponga di una definizione/implementazione corrispondente.</span><span class="sxs-lookup"><span data-stu-id="041eb-158">The crux of this proposal is essentially ensuring that a declaration has a corresponding definition / implementation.</span></span> <span data-ttu-id="041eb-159">Dato che è necessario usare `abstract` perché è già una parola chiave del linguaggio che impone allo sviluppatore di pensare a un'implementazione?</span><span class="sxs-lookup"><span data-stu-id="041eb-159">Given that should we use `abstract` since it's already a language keyword that forces the developer to think about having an implementation?</span></span>

<span data-ttu-id="041eb-160">**Risoluzione** dei problemi Si è verificata una discussione di questo aspetto, ma alla fine è stata decisa.</span><span class="sxs-lookup"><span data-stu-id="041eb-160">**Resolution** There was a healthy discussion about this but eventually it was decided against.</span></span>
<span data-ttu-id="041eb-161">Sì, i requisiti sono noti ma i concetti sono significativamente diversi.</span><span class="sxs-lookup"><span data-stu-id="041eb-161">Yes the requirements are familiar but the concepts are significantly different.</span></span>
<span data-ttu-id="041eb-162">Potrebbe facilmente condurre lo sviluppatore a credere che stesse creando slot virtuali quando non lo facevano.</span><span class="sxs-lookup"><span data-stu-id="041eb-162">Could easily lead the developer to believe they were creating virtual slots when they were not doing so.</span></span>
