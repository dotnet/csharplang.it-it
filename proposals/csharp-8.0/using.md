---
ms.openlocfilehash: 91afbc3e3412049cd183c36c8035f1862c520413
ms.sourcegitcommit: da1180f7eacdd5067b32d291a76e6764159e00fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "79485056"
---
# <a name="pattern-based-using-and-using-declarations"></a><span data-ttu-id="7e4f2-101">"basato su modelli con" e "dichiarazioni using"</span><span class="sxs-lookup"><span data-stu-id="7e4f2-101">"pattern-based using" and "using declarations"</span></span>

## <a name="summary"></a><span data-ttu-id="7e4f2-102">Summary</span><span class="sxs-lookup"><span data-stu-id="7e4f2-102">Summary</span></span>

<span data-ttu-id="7e4f2-103">Il linguaggio aggiungerà due nuove funzionalità relative all'istruzione `using` per semplificare la gestione delle risorse: `using` dovrebbe riconoscere un modello Disposable, oltre a `IDisposable` e aggiungere una dichiarazione `using` al linguaggio.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-103">The language will add two new capabilities around the `using` statement in order to make resource management simpler: `using` should recognize a disposable pattern in addition to `IDisposable` and add a `using` declaration to the language.</span></span>

## <a name="motivation"></a><span data-ttu-id="7e4f2-104">Motivazione</span><span class="sxs-lookup"><span data-stu-id="7e4f2-104">Motivation</span></span>

<span data-ttu-id="7e4f2-105">Il `using` istruzione è uno strumento efficace per la gestione delle risorse, ma richiede una certa cerimonia.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-105">The `using` statement is an effective tool for resource management today but it requires quite a bit of ceremony.</span></span> <span data-ttu-id="7e4f2-106">I metodi con numerose risorse da gestire possono essere sintatticamente impantanati con una serie di istruzioni `using`.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-106">Methods that have a number of resources to manage can get syntactically bogged down with a series of `using` statements.</span></span> <span data-ttu-id="7e4f2-107">Questo fardello di sintassi è sufficiente per la maggior parte delle linee guida di stile di codifica in modo esplicito per questo scenario.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-107">This syntax burden is enough that most coding style guidelines explicitly have an exception around braces for this scenario.</span></span> 

<span data-ttu-id="7e4f2-108">La dichiarazione di `using` rimuove gran parte della cerimonia qui e C# ottiene la parità con altri linguaggi che includono i blocchi di gestione delle risorse.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-108">The `using` declaration removes much of the ceremony here and gets C# on par with other languages that include resource management blocks.</span></span> <span data-ttu-id="7e4f2-109">Inoltre, la `using` basata su modelli consente agli sviluppatori di espandere il set di tipi che possono partecipare qui.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-109">Additionally the pattern-based `using` lets developers expand the set of types that can participate here.</span></span> <span data-ttu-id="7e4f2-110">In molti casi, la necessità di creare tipi di wrapper che esistono solo per consentire l'utilizzo di valori in un'istruzione `using`.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-110">In many cases removing the need to create wrapper types that only exist to allow for a values use in a `using` statement.</span></span> 

<span data-ttu-id="7e4f2-111">Insieme, queste funzionalità consentono agli sviluppatori di semplificare ed espandere gli scenari in cui è possibile applicare `using`.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-111">Together these features allow developers to simplify and expand the scenarios where `using` can be applied.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="7e4f2-112">Progettazione dettagliata</span><span class="sxs-lookup"><span data-stu-id="7e4f2-112">Detailed Design</span></span> 

### <a name="using-declaration"></a><span data-ttu-id="7e4f2-113">dichiarazione using</span><span class="sxs-lookup"><span data-stu-id="7e4f2-113">using declaration</span></span>

<span data-ttu-id="7e4f2-114">Il linguaggio consentirà di aggiungere `using` a una dichiarazione di variabile locale.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-114">The language will allow for `using` to be added to a local variable declaration.</span></span> <span data-ttu-id="7e4f2-115">Tale dichiarazione avrà lo stesso effetto di dichiarare la variabile in un'istruzione `using` nello stesso percorso.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-115">Such a declaration will have the same effect as declaring the variable in a `using` statement at the same location.</span></span>

```csharp
if (...) 
{ 
   using FileStream f = new FileStream(@"C:\users\jaredpar\using.md");
   // statements
}

// Equivalent to 
if (...) 
{ 
   using (FileStream f = new FileStream(@"C:\users\jaredpar\using.md")) 
   {
    // statements
   }
}
```

<span data-ttu-id="7e4f2-116">La durata di un `using` local si estenderà alla fine dell'ambito in cui è dichiarata.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-116">The lifetime of a `using` local will extend to the end of the scope in which it is declared.</span></span> <span data-ttu-id="7e4f2-117">Le variabili locali `using` verranno quindi eliminate nell'ordine inverso in cui sono dichiarate.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-117">The `using` locals will then be disposed in the reverse order in which they are declared.</span></span> 

```csharp
{ 
    using var f1 = new FileStream("...");
    using var f2 = new FileStream("..."), f3 = new FileStream("...");
    ...
    // Dispose f3
    // Dispose f2 
    // Dispose f1
}
```

<span data-ttu-id="7e4f2-118">Non sono previste restrizioni per `goto`o qualsiasi altro costrutto del flusso di controllo in faccia di una dichiarazione di `using`.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-118">There are no restrictions around `goto`, or any other control flow construct in the face of a `using` declaration.</span></span> <span data-ttu-id="7e4f2-119">Il codice agisce invece esattamente come per l'istruzione `using` equivalente:</span><span class="sxs-lookup"><span data-stu-id="7e4f2-119">Instead the code acts just as it would for the equivalent `using` statement:</span></span>

```csharp
{
    using var f1 = new FileStream("...");
  target:
    using var f2 = new FileStream("...");
    if (someCondition) 
    {
        // Causes f2 to be disposed but has no effect on f1
        goto target;
    }
}
```

<span data-ttu-id="7e4f2-120">Una dichiarazione locale dichiarata in una `using` dichiarazione locale sarà in modo implicito di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-120">A local declared in a `using` local declaration will be implicitly read-only.</span></span> <span data-ttu-id="7e4f2-121">Corrisponde al comportamento delle variabili locali dichiarate in un'istruzione `using`.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-121">This matches the behavior of locals declared in a `using` statement.</span></span> 

<span data-ttu-id="7e4f2-122">La grammatica del linguaggio per le dichiarazioni di `using` sarà la seguente:</span><span class="sxs-lookup"><span data-stu-id="7e4f2-122">The language grammar for `using` declarations will be the following:</span></span>

```antlr
local-using-declaration:
  using type using-declarators

using-declarators:
  using-declarator
  using-declarators , using-declarator
  
using-declarator:
  identifier = expression
```

<span data-ttu-id="7e4f2-123">Limitazioni relative alla dichiarazione `using`:</span><span class="sxs-lookup"><span data-stu-id="7e4f2-123">Restrictions around `using` declaration:</span></span>

- <span data-ttu-id="7e4f2-124">Potrebbe non essere visualizzato direttamente all'interno di un'etichetta di `case` ma deve trovarsi all'interno di un blocco all'interno dell'etichetta `case`.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-124">May not appear directly inside a `case` label but instead must be within a block inside the `case` label.</span></span>
- <span data-ttu-id="7e4f2-125">Potrebbe non essere incluso in una dichiarazione di variabile `out`.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-125">May not appear as part of an `out` variable declaration.</span></span> 
- <span data-ttu-id="7e4f2-126">Deve disporre di un inizializzatore per ogni dichiaratore.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-126">Must have an initializer for each declarator.</span></span>
- <span data-ttu-id="7e4f2-127">Il tipo locale deve essere convertibile in modo implicito per `IDisposable` o soddisfare il modello di `using`.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-127">The local type must be implicitly convertible to `IDisposable` or fulfill the `using` pattern.</span></span>

### <a name="pattern-based-using"></a><span data-ttu-id="7e4f2-128">Basato su pattern con</span><span class="sxs-lookup"><span data-stu-id="7e4f2-128">pattern-based using</span></span>

<span data-ttu-id="7e4f2-129">Il linguaggio aggiungerà la nozione di modello Disposable, ovvero un tipo che dispone di un metodo di istanza accessibile `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-129">The language will add the notion of a disposable pattern: that is a type which has an accessible `Dispose` instance method.</span></span> <span data-ttu-id="7e4f2-130">I tipi che soddisfano il modello Disposable possono partecipare a un'istruzione o a una dichiarazione `using` senza che sia necessario implementare `IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-130">Types which fit the disposable pattern can participate in a `using` statement or declaration without being required to implement `IDisposable`.</span></span> 

```csharp
class Resource
{ 
    public void Dispose() { ... }
}

using (var r = new Resource())
{
    // statements
}
```

<span data-ttu-id="7e4f2-131">Ciò consentirà agli sviluppatori di sfruttare `using` in diversi nuovi scenari:</span><span class="sxs-lookup"><span data-stu-id="7e4f2-131">This will allow developers to leverage `using` in a number of new scenarios:</span></span>

- <span data-ttu-id="7e4f2-132">`ref struct`: questi tipi non possono implementare interfacce oggi e pertanto non possono partecipare a istruzioni `using`.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-132">`ref struct`: These types can't implement interfaces today and hence can't participate in `using` statements.</span></span>
- <span data-ttu-id="7e4f2-133">I metodi di estensione consentono agli sviluppatori di potenziare i tipi di altri assembly per partecipare alle istruzioni `using`.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-133">Extension methods will allow developers to augment types in other assemblies to participate in `using` statements.</span></span>

<span data-ttu-id="7e4f2-134">Nel caso in cui un tipo possa essere convertito in modo implicito in `IDisposable` e si adatta anche al modello Disposable, sarà preferibile `IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-134">In the situation where a type can be implicitly converted to `IDisposable` and also fits the disposable pattern, then `IDisposable` will be preferred.</span></span> <span data-ttu-id="7e4f2-135">Sebbene questa operazione accetti l'approccio opposto di `foreach` (modello preferito rispetto all'interfaccia), è necessario per la compatibilità con le versioni precedenti.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-135">While this takes the opposite approach of `foreach` (pattern preferred over interface) it is necessary for backwards compatibility.</span></span>

<span data-ttu-id="7e4f2-136">Di seguito sono riportate le stesse restrizioni di un'istruzione `using` tradizionale: le variabili locali dichiarate nella `using` sono di sola lettura, un valore `null` non causerà la generazione di un'eccezione e così via... La generazione del codice sarà diversa solo perché non sarà presente un cast da `IDisposable` prima di chiamare Dispose:</span><span class="sxs-lookup"><span data-stu-id="7e4f2-136">The same restrictions from a traditional `using` statement apply here as well: local variables declared in the `using` are read-only, a `null` value will not cause an exception to be thrown, etc ... The code generation will be different only in that there will not be a cast to `IDisposable` before calling Dispose:</span></span>

```csharp
{
      Resource r = new Resource();
      try {
            // statements
      }
      finally {
            if (resource != null) resource.Dispose();
      }
}
```

<span data-ttu-id="7e4f2-137">Per adattarsi al modello Disposable, il metodo `Dispose` deve essere accessibile senza parametri e deve avere un tipo `void` restituito.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-137">In order to fit the disposable pattern the `Dispose` method must be accessible, parameterless and have a `void` return type.</span></span> <span data-ttu-id="7e4f2-138">Non sono previste altre restrizioni.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-138">There are no other restrictions.</span></span> <span data-ttu-id="7e4f2-139">Questo significa esplicitamente che è possibile usare i metodi di estensione.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-139">This explicitly means that extension methods can be used here.</span></span>

## <a name="considerations"></a><span data-ttu-id="7e4f2-140">Considerazioni</span><span class="sxs-lookup"><span data-stu-id="7e4f2-140">Considerations</span></span>

### <a name="case-labels-without-blocks"></a><span data-ttu-id="7e4f2-141">etichette case senza blocchi</span><span class="sxs-lookup"><span data-stu-id="7e4f2-141">case labels without blocks</span></span>

<span data-ttu-id="7e4f2-142">Una `using declaration` non è valida direttamente all'interno di un'etichetta `case` a causa di complicazioni relative alla durata effettiva.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-142">A `using declaration` is illegal directly inside a `case` label due to complications around its actual lifetime.</span></span> <span data-ttu-id="7e4f2-143">Una possibile soluzione è semplicemente assegnarle la stessa durata di un `out var` nello stesso percorso.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-143">One potential solution is to simply give it the same lifetime as an `out var` in the same location.</span></span> <span data-ttu-id="7e4f2-144">È stata considerata la complessità aggiuntiva all'implementazione della funzionalità e la facilità di gestione (solo aggiungere un blocco all'etichetta `case`) non giustificava l'esecuzione di questa route.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-144">It was deemed the extra complexity to the feature implementation and the ease of the work around (just add a block to the `case` label) didn't justify taking this route.</span></span>

## <a name="future-expansions"></a><span data-ttu-id="7e4f2-145">Espansioni future</span><span class="sxs-lookup"><span data-stu-id="7e4f2-145">Future Expansions</span></span>

### <a name="fixed-locals"></a><span data-ttu-id="7e4f2-146">variabili locali fisse</span><span class="sxs-lookup"><span data-stu-id="7e4f2-146">fixed locals</span></span>

<span data-ttu-id="7e4f2-147">Un'istruzione `fixed` dispone di tutte le proprietà di `using` istruzioni che hanno motivato la possibilità di avere `using` variabili locali.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-147">A `fixed` statement has all of the properties of `using` statements that motivated the ability to have `using` locals.</span></span> <span data-ttu-id="7e4f2-148">È necessario considerare l'estensione di questa funzionalità anche a `fixed` variabili locali.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-148">Consideration should be given to extending this feature to `fixed` locals as well.</span></span> <span data-ttu-id="7e4f2-149">Le regole di durata e di ordinamento dovrebbero essere valide anche per `using` e `fixed`.</span><span class="sxs-lookup"><span data-stu-id="7e4f2-149">The lifetime and ordering rules should apply equally well for `using` and `fixed` here.</span></span>
